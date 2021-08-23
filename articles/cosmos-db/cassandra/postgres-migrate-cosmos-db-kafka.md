---
title: Apache Kafka를 사용하여 PostgreSQL에서 Azure Cosmos DB Cassandra API 계정으로 데이터 마이그레이션
description: Kafka Connect를 사용하여 실시간으로 PostgreSQL에서 Azure Cosmos DB Cassandra API로 데이터를 동기화하는 방법을 알아봅니다.
author: abhirockzz
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: how-to
ms.date: 01/05/2021
ms.author: abhishgu
ms.reviewer: abhishgu
ms.openlocfilehash: f2f6148d268a4a4b41a8b59c0751143e04cae95f
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122536082"
---
# <a name="migrate-data-from-postgresql-to-azure-cosmos-db-cassandra-api-account-using-apache-kafka"></a>Apache Kafka를 사용하여 PostgreSQL에서 Azure Cosmos DB Cassandra API 계정으로 데이터 마이그레이션
[!INCLUDE[appliesto-cassandra-api](../includes/appliesto-cassandra-api.md)]

Azure Cosmos DB의 Cassandra API는 다음과 같은 다양한 이유로 Apache Cassandra에서 실행되는 엔터프라이즈 워크로드에 적합한 선택이 되었습니다.

* **대폭적인 비용 절감:** Azure Cosmos DB를 사용하여 비용을 절감할 수 있습니다. 여기에는 VM, 대역폭, 적용 가능한 Oracle 라이선스 비용이 포함됩니다. 또한 데이터 센터, 서버, SSD 스토리지, 네트워킹, 전기 비용을 관리할 필요가 없습니다.

* **스케일링 수준 및 가용성 향상:** 단일 실패 지점을 제거하고 애플리케이션의 스케일링 수준과 가용성을 향상합니다.

* **관리 및 모니터링 오버헤드 없음:** 완전 관리형 클라우드 서비스인 Azure Cosmos DB는 많은 설정을 관리하고 모니터링하는 오버헤드를 제거합니다.

[Kafka Connect](https://kafka.apache.org/documentation/#connect)는 확장 가능하고 신뢰할 수 있는 방식으로 [Apache Kafka](https://kafka.apache.org/)와 기타 시스템 간에 데이터를 스트림하는 플랫폼입니다. 이 기능은 몇 가지 상용 커넥터를 지원합니다. 즉, 외부 시스템을 Apache Kafka와 통합하기 위한 사용자 지정 코드는 필요하지 않습니다.

이 문서에서는 Kafka Connect 조합을 사용하여 [PostgreSQL](https://www.postgresql.org/)과 같은 관계형 데이터베이스의 레코드를 [Azure Cosmos DB Cassandra API](cassandra-introduction.md)로 지속적으로 동기화하도록 데이터 파이프라인을 설정하는 방법을 보여 줍니다.

## <a name="overview"></a>개요

다음은 이 문서에 설명된 엔드투엔드 흐름에 대한 개략적인 개요입니다.

PostgreSQL 테이블의 데이터는 Kafka Connect **원본** 커넥터에 해당하는 [Debezium PostgreSQL 커넥터](https://debezium.io/documentation/reference/1.2/connectors/postgresql.html)를 사용하여 Apache Kafka에 푸시됩니다. PostgreSQL 테이블의 레코드 삽입, 업데이트 또는 삭제는 `change data` 이벤트로 캡처되고 Kafka 토픽으로 전송됩니다. [DataStax Apache Kafka 커넥터](https://docs.datastax.com/en/kafka/doc/kafka/kafkaIntro.html)(kafka Connect **싱크** 커넥터)는 파이프라인의 두 번째 부분을 형성합니다. 이 커넥터는 Kafka 토픽의 변경 데이터 이벤트를 Azure Cosmos DB Cassandra API 테이블과 동기화합니다.

> [!NOTE]
> DataStax Apache Kafka 커넥터의 특정 기능을 사용하면 데이터를 여러 테이블에 푸시할 수 있습니다. 이 예제에서 커넥터는 다른 쿼리 요구 사항을 지원할 수 있는 두 개의 Cassandra 테이블에 변경 데이터 레코드를 유지하는 데 유용합니다.

## <a name="prerequisites"></a>사전 요구 사항

* [Azure Cosmos DB Cassandra API 계정 프로비전](manage-data-dotnet.md#create-a-database-account)
* [유효성 검사를 위해 cqlsh 또는 호스트된 셸 사용](cassandra-support.md#hosted-cql-shell-preview).
* JDK 8 이상
* [Docker](https://www.docker.com/)(선택 사항)

## <a name="base-setup"></a>기본 설정

### <a name="set-up-postgresql-database-if-you-havent-already"></a>아직 설정하지 않은 경우 PostgreSQL 데이터베이스 설정 

이것은 기존 온-프레미스 데이터베이스일 수도 있고 로컬 머신에 [다운로드하여 설치](https://www.postgresql.org/download/)할 수도 있습니다. [Docker 컨테이너](https://hub.docker.com/_/postgres)도 사용할 수 있습니다.
[!INCLUDE [pull-image-include](../../../includes/pull-image-include.md)]

컨테이너를 시작하려면 다음을 수행합니다.

```bash
docker run --rm -p 5432:5432 -e POSTGRES_PASSWORD=<enter password> postgres
```

[`psql`](https://www.postgresql.org/docs/current/app-psql.html) 클라이언트를 사용하여 PostgreSQL 인스턴스에 연결합니다.

```bash
psql -h localhost -p 5432 -U postgres -W -d postgres
```

샘플 주문 정보를 저장할 테이블을 만듭니다.

```sql
CREATE SCHEMA retail;

CREATE TABLE retail.orders_info (
    orderid SERIAL NOT NULL PRIMARY KEY,
    custid INTEGER NOT NULL,
    amount INTEGER NOT NULL,
    city VARCHAR(255) NOT NULL,
    purchase_time VARCHAR(40) NOT NULL
);
```

### <a name="using-the-azure-portal-create-the-cassandra-keyspace-and-the-tables-required-for-the-demo-application"></a>Azure Portal을 사용하여 Cassandra Keyspace 및 데모 애플리케이션에 필요한 테이블을 만듭니다.

> [!NOTE]
> 아래와 동일한 Keyspace 및 테이블 이름을 사용합니다.

```sql
CREATE KEYSPACE retail WITH REPLICATION = {'class' : 'NetworkTopologyStrategy', 'datacenter1' : 1};

CREATE TABLE retail.orders_by_customer (order_id int, customer_id int, purchase_amount int, city text, purchase_time timestamp, PRIMARY KEY (customer_id, purchase_time)) WITH CLUSTERING ORDER BY (purchase_time DESC) AND cosmosdb_cell_level_timestamp=true AND cosmosdb_cell_level_timestamp_tombstones=true AND cosmosdb_cell_level_timetolive=true;

CREATE TABLE retail.orders_by_city (order_id int, customer_id int, purchase_amount int, city text, purchase_time timestamp, PRIMARY KEY (city,order_id)) WITH cosmosdb_cell_level_timestamp=true AND cosmosdb_cell_level_timestamp_tombstones=true AND cosmosdb_cell_level_timetolive=true;
```

### <a name="setup-apache-kafka"></a>Apache Kafka 설치 

이 문서에서는 로컬 클러스터를 사용하지만 다른 옵션을 선택할 수 있습니다. [Kafka를 다운로드하고](https://kafka.apache.org/downloads), 압축을 풀고, Zookeeper 및 Kafka 클러스터를 시작합니다.

```bash
cd <KAFKA_HOME>/bin

#start zookeeper
bin/zookeeper-server-start.sh config/zookeeper.properties

#start kafka (in another terminal)
bin/kafka-server-start.sh config/server.properties
```

### <a name="setup-connectors"></a>커넥터 설치

Debezium PostgreSQL 및 DataStax Apache Kafka 커넥터를 설치합니다. Debezium PostgreSQL 커넥터 플러그 인 보관 파일을 다운로드합니다. 예를 들어, 커넥터 버전 1.3.0(작성 시점의 최신 버전)을 다운로드하려면 [이 링크](https://repo1.maven.org/maven2/io/debezium/debezium-connector-postgres/1.3.0.Final/debezium-connector-postgres-1.3.0.Final-plugin.tar.gz)를 사용합니다. [이 링크](https://downloads.datastax.com/#akc)에서 DataStax Apache Kafka 커넥터를 다운로드합니다.

커넥터 보관 파일의 압축을 풀고 JAR 파일을 [Kafka Connect plugin.path](https://kafka.apache.org/documentation/#connectconfigs)에 복사합니다.


```bash
cp <path_to_debezium_connector>/*.jar <KAFKA_HOME>/libs
cp <path_to_cassandra_connector>/*.jar <KAFKA_HOME>/libs
```

> 자세한 내용은 [Debezium](https://debezium.io/documentation/reference/1.2/connectors/postgresql.html#postgresql-deploying-a-connector) 및 [DataStax](https://docs.datastax.com/en/kafka/doc/) 설명서를 참조하세요.

## <a name="configure-kafka-connect-and-start-data-pipeline"></a>Kafka Connect 구성 및 데이터 파이프라인 시작

### <a name="start-kafka-connect-cluster"></a>Kafka Connect 클러스터 시작

```bash
cd <KAFKA_HOME>/bin
./connect-distributed.sh ../config/connect-distributed.properties
```

### <a name="start-postgresql-connector-instance"></a>PostgreSQL 커넥터 인스턴스 시작

파일 예제 `pg-source-config.json`에 커넥터 구성(JSON)을 저장합니다.

```json
{
    "name": "pg-orders-source",
    "config": {
        "connector.class": "io.debezium.connector.postgresql.PostgresConnector",
        "database.hostname": "localhost",
        "database.port": "5432",
        "database.user": "postgres",
        "database.password": "password",
        "database.dbname": "postgres",
        "database.server.name": "myserver",
        "plugin.name": "wal2json",
        "table.include.list": "retail.orders_info",
        "value.converter": "org.apache.kafka.connect.json.JsonConverter"
    }
}
```

PostgreSQL 커넥터 인스턴스를 시작하려면 다음을 수행합니다.

```bash
curl -X POST -H "Content-Type: application/json" --data @pg-source-config.json http://localhost:8083/connectors
```

> [!NOTE]
> `curl -X DELETE http://localhost:8083/connectors/pg-orders-source` 명령을 사용하여 삭제할 수 있습니다.


### <a name="insert-data"></a>데이터 삽입

`orders_info` 테이블에는 주문 ID, 고객 ID, 도시 등과 같은 주문 세부 정보가 포함되어 있습니다. 아래 SQL을 사용하여 임의 데이터로 테이블을 채웁니다.

```sql
insert into retail.orders_info (
    custid, amount, city, purchase_time
)
select
    random() * 10000 + 1,
    random() * 200,
    ('{New Delhi,Seattle,New York,Austin,Chicago,Cleveland}'::text[])[ceil(random()*3)],
    NOW() + (random() * (interval '1 min'))
from generate_series(1, 10) s(i);
```

테이블에 10개 레코드를 삽입해야 합니다. 요구 사항 예제에 따라 아래의 `generate_series(1, 10)`에서 레코드 수를 업데이트하여 `100`개 레코드를 삽입하려면 `generate_series(1, 100)`을 사용합니다.

확인하려면 다음을 수행합니다.

```bash
select * from retail.orders_info;
```

Kafka 토픽에서 변경 데이터 캡처 이벤트를 확인합니다.

> [!NOTE]
> 토픽 이름은 [커넥터 규칙](https://debezium.io/documentation/reference/1.3/connectors/postgresql.html#postgresql-topic-names)에 따라 `myserver.retail.orders_info`입니다.

```bash
cd <KAFKA_HOME>/bin

./kafka-console-consumer.sh --topic myserver.retail.orders_info --bootstrap-server localhost:9092 --from-beginning
```

변경 데이터 이벤트가 JSON 형식으로 표시됩니다.

### <a name="start-datastax-apache-kafka-connector-instance"></a>DataStax Apache Kafka 커넥터 인스턴스 시작

커넥터 구성(JSON)을 파일 예제 `cassandra-sink-config.json`에 저장하고 환경에 따라 속성을 업데이트합니다.

```json
{
    "name": "kafka-cosmosdb-sink",
    "config": {
        "connector.class": "com.datastax.oss.kafka.sink.CassandraSinkConnector",
        "tasks.max": "1",
        "topics": "myserver.retail.orders_info",
        "contactPoints": "<Azure Cosmos DB account name>.cassandra.cosmos.azure.com",
        "loadBalancing.localDc": "<Azure Cosmos DB region e.g. Southeast Asia>",
        "datastax-java-driver.advanced.connection.init-query-timeout": 5000,
        "ssl.hostnameValidation": true,
        "ssl.provider": "JDK",
        "ssl.keystore.path": "<path to JDK keystore path e.g. <JAVA_HOME>/jre/lib/security/cacerts>",
        "ssl.keystore.password": "<keystore password: it is 'changeit' by default>",
        "port": 10350,
        "maxConcurrentRequests": 500,
        "maxNumberOfRecordsInBatch": 32,
        "queryExecutionTimeout": 30,
        "connectionPoolLocalSize": 4,
        "auth.username": "<Azure Cosmos DB user name (same as account name)>",
        "auth.password": "<Azure Cosmos DB password>",
        "topic.myserver.retail.orders_info.retail.orders_by_customer.mapping": "order_id=value.orderid, customer_id=value.custid, purchase_amount=value.amount, city=value.city, purchase_time=value.purchase_time",
        "topic.myserver.retail.orders_info.retail.orders_by_city.mapping": "order_id=value.orderid, customer_id=value.custid, purchase_amount=value.amount, city=value.city, purchase_time=value.purchase_time",
        "key.converter": "org.apache.kafka.connect.storage.StringConverter",
        "transforms": "unwrap",
        "transforms.unwrap.type": "io.debezium.transforms.ExtractNewRecordState",
        "offset.flush.interval.ms": 10000
    }
}
```

커넥터 인스턴스를 시작하려면 다음을 수행합니다.

```bash
curl -X POST -H "Content-Type: application/json" --data @cassandra-sink-config.json http://localhost:8083/connectors
```

커넥터가 작동하기 시작하고 PostgreSQL에서 Azure Cosmos DB로의 엔드투엔드 파이프라인이 작동하게 됩니다.

### <a name="query-azure-cosmos-db"></a>Azure Cosmos DB 쿼리

Azure Cosmos DB에서 Cassandra 테이블을 확인합니다. 다음은 시도할 수 있는 몇 가지 쿼리입니다.

```sql
select count(*) from retail.orders_by_customer;
select count(*) from retail.orders_by_city;

select * from retail.orders_by_customer;
select * from retail.orders_by_city;

select * from retail.orders_by_city where city='Seattle';
select * from retail.orders_by_customer where customer_id = 10;
```

PostgreSQL에 더 많은 데이터를 계속 삽입하고 레코드가 Azure Cosmos DB와 동기화되었는지 확인할 수 있습니다.

## <a name="next-steps"></a>다음 단계

* [Kafka Connect를 사용하여 Apache Kafka 및 Azure Cosmos DB Cassandra API 통합](kafka-connect.md)
* [변경 데이터 캡처를 위해 Azure Event Hubs(미리 보기)의 Apache Kafka Connect를 Debezium와 통합](../../event-hubs/event-hubs-kafka-connect-debezium.md)
* [Blitzz를 사용하여 Oracle에서 Azure Cosmos DB Cassandra API로 데이터 마이그레이션](oracle-migrate-cosmos-db-blitzz.md)
* [컨테이너 및 데이터베이스의 처리량 프로비전](../set-throughput.md) 
* [파티션 키 모범 사례](../partitioning-overview.md#choose-partitionkey)
* [Azure Cosmos DB capacity planner 문서를 사용하여 RU/s 예측](../estimate-ru-with-capacity-planner.md) 문서

