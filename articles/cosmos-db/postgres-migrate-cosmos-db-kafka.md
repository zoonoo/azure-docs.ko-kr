---
title: Apache Kafka를 사용 하 여 PostgreSQL에서 Azure Cosmos DB Cassandra API 계정으로 데이터 마이그레이션
description: Kafka Connect를 사용 하 여 실시간으로 PostgreSQL에서 Azure Cosmos DB Cassandra API로 데이터를 동기화 하는 방법을 알아봅니다.
author: abhirockzz
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: how-to
ms.date: 01/05/2021
ms.author: abhishgu
ms.reviewer: abhishgu
ms.openlocfilehash: 24714b185b0f666770b306a7e80a97a3f8f868a3
ms.sourcegitcommit: 8dd8d2caeb38236f79fe5bfc6909cb1a8b609f4a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/08/2021
ms.locfileid: "98052629"
---
# <a name="migrate-data-from-postgresql-to-azure-cosmos-db-cassandra-api-account-using-apache-kafka"></a>Apache Kafka를 사용 하 여 PostgreSQL에서 Azure Cosmos DB Cassandra API 계정으로 데이터 마이그레이션
[!INCLUDE[appliesto-cassandra-api](includes/appliesto-cassandra-api.md)]

Azure Cosmos DB Cassandra API는 다음과 같은 다양 한 이유로 Apache Cassandra에서 실행 되는 엔터프라이즈 워크 로드에 적합 한 선택이 되었습니다.

* **상당한 비용 절감:** Azure Cosmos DB를 사용 하 여 비용을 절감할 수 있습니다. 여기에는 VM, 대역폭 및 해당 하는 모든 Oracle 라이선스의 비용이 포함 됩니다. 또한 데이터 센터, 서버, SSD 저장소, 네트워킹 및 전기 비용을 관리할 필요가 없습니다.

* **확장성 및 가용성 향상:** 응용 프로그램에 대 한 단일 실패 지점이 나 향상 된 확장성 및 가용성을 제거 합니다.

* **관리 및 모니터링의 오버 헤드 없음:** 완벽 하 게 관리 되는 클라우드 서비스인 Azure Cosmos DB는 수많은 설정을 관리 하 고 모니터링 하는 오버 헤드를 제거 합니다.

[Kafka Connect](https://kafka.apache.org/documentation/#connect) 는 확장 가능 하 고 신뢰할 수 있는 방식으로 [Apache Kafka](https://kafka.apache.org/) 와 기타 시스템 간에 데이터를 스트림 하는 플랫폼입니다. 이는 선반 커넥터를 여러 개 지원 합니다. 즉, 외부 시스템을 Apache Kafka와 통합 하는 사용자 지정 코드가 필요 하지 않습니다.

이 문서에서는 Kafka 커넥터의 조합을 사용 하 여 데이터 파이프라인을 설정 하 여 [PostgreSQL](https://www.postgresql.org/) 와 같은 관계형 데이터베이스의 레코드를 [Azure Cosmos DB Cassandra API](cassandra-introduction.md)로 지속적으로 동기화 하는 방법을 보여 줍니다.

## <a name="overview"></a>개요

다음은이 문서에서 제공 하는 종단 간 흐름에 대 한 개략적인 개요입니다.

PostgreSQL 테이블의 데이터는 Kafka Connect **원본** 커넥터용 [Debezium PostgreSQL 커넥터](https://debezium.io/documentation/reference/1.2/connectors/postgresql.html)를 사용 하 여 Apache Kafka에 푸시됩니다. PostgreSQL 테이블의 레코드에 대 한 삽입, 업데이트 또는 삭제는 이벤트로 캡처되고 `change data` Kafka 토픽으로 전송 됩니다. [DataStax Apache Kafka 커넥터](https://docs.datastax.com/en/kafka/doc/kafka/kafkaIntro.html) (kafka Connect **싱크** 커넥터)는 파이프라인의 두 번째 부분을 형성 합니다. 이는 Kafka 토픽의 변경 데이터 이벤트를 동기화 하 여 Cassandra API 테이블을 Azure Cosmos DB 합니다.

> [!NOTE]
> DataStax Apache Kafka 커넥터의 특정 기능을 사용 하면 데이터를 여러 테이블에 푸시할 수 있습니다. 이 예제에서 커넥터는 다른 쿼리 요구 사항을 지원할 수 있는 두 개의 Cassandra 테이블에 변경 데이터 레코드를 유지 하는 데 도움이 됩니다.

## <a name="prerequisites"></a>사전 요구 사항

* [Azure Cosmos DB Cassandra API 계정 프로비전](create-cassandra-dotnet.md#create-a-database-account)
* [유효성 검사에 cqlsh 또는 hosted shell 사용](cassandra-support.md#hosted-cql-shell-preview)
* JDK 8 이상
* [Docker](https://www.docker.com/) (선택 사항)

## <a name="base-setup"></a>기본 설정

### <a name="set-up-postgresql-database-if-you-havent-already"></a>PostgreSQL 데이터베이스를 설치 합니다 (아직 없는 경우). 

이는 기존 온-프레미스 데이터베이스 일 수도 있고 로컬 컴퓨터에 [다운로드 하 여 설치할](https://www.postgresql.org/download/) 수도 있습니다. [Docker 컨테이너](https://hub.docker.com/_/postgres)도 사용할 수 있습니다.

컨테이너를 시작 하려면:

```bash
docker run --rm -p 5432:5432 -e POSTGRES_PASSWORD=<enter password> postgres
```

클라이언트를 사용 하 여 PostgreSQL 인스턴스에 연결 합니다 [`psql`](https://www.postgresql.org/docs/current/app-psql.html) .

```bash
psql -h localhost -p 5432 -U postgres -W -d postgres
```

예제 주문 정보를 저장할 테이블을 만듭니다.

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

### <a name="using-the-azure-portal-create-the-cassandra-keyspace-and-the-tables-required-for-the-demo-application"></a>Azure Portal를 사용 하 여 데모 응용 프로그램에 필요한 Cassandra Keyspace 및 테이블을 만듭니다.

> [!NOTE]
> 아래와 같은 Keyspace 및 테이블 이름을 사용 합니다.

```sql
CREATE KEYSPACE retail WITH REPLICATION = {'class' : 'NetworkTopologyStrategy', 'datacenter1' : 1};

CREATE TABLE retail.orders_by_customer (order_id int, customer_id int, purchase_amount int, city text, purchase_time timestamp, PRIMARY KEY (customer_id, purchase_time)) WITH CLUSTERING ORDER BY (purchase_time DESC) AND cosmosdb_cell_level_timestamp=true AND cosmosdb_cell_level_timestamp_tombstones=true AND cosmosdb_cell_level_timetolive=true;

CREATE TABLE retail.orders_by_city (order_id int, customer_id int, purchase_amount int, city text, purchase_time timestamp, PRIMARY KEY (city,order_id)) WITH cosmosdb_cell_level_timestamp=true AND cosmosdb_cell_level_timestamp_tombstones=true AND cosmosdb_cell_level_timetolive=true;
```

### <a name="setup-apache-kafka"></a>설치 Apache Kafka 

이 문서에서는 로컬 클러스터를 사용 하지만 다른 옵션을 선택할 수 있습니다. [Kafka을 다운로드](https://kafka.apache.org/downloads)하 고 압축을 풀고, 사육 사 및 kafka 클러스터를 시작 합니다.

```bash
cd <KAFKA_HOME>/bin

#start zookeeper
bin/zookeeper-server-start.sh config/zookeeper.properties

#start kafka (in another terminal)
bin/kafka-server-start.sh config/server.properties
```

### <a name="setup-connectors"></a>커넥터 설정

Debezium PostgreSQL 및 DataStax Apache Kafka 커넥터를 설치 합니다. Debezium PostgreSQL connector 플러그 인 아카이브를 다운로드 합니다. 예를 들어 커넥터의 버전 1.3.0 (작성 시점에 최신)를 다운로드 하려면 [이 링크](https://repo1.maven.org/maven2/io/debezium/debezium-connector-postgres/1.3.0.Final/debezium-connector-postgres-1.2.0.Final-plugin.tar.gz)를 사용 합니다. [이 링크](https://downloads.datastax.com/#akc)에서 DataStax Apache Kafka 커넥터를 다운로드 합니다.

커넥터 보관 파일의 압축을 풀고 JAR 파일을 [Kafka Connect 플러그인](https://kafka.apache.org/documentation/#connectconfigs)에 복사 합니다. 경로.


```bash
cp <path_to_debezium_connector>/*.jar <KAFKA_HOME>/libs
cp <path_to_cassandra_connector>/*.jar <KAFKA_HOME>/libs
```

> 자세한 내용은 [Debezium](https://debezium.io/documentation/reference/1.2/connectors/postgresql.html#postgresql-deploying-a-connector) 및 [DataStax](https://docs.datastax.com/en/kafka/doc/) 설명서를 참조 하세요.

## <a name="configure-kafka-connect-and-start-data-pipeline"></a>Kafka Connect 구성 및 데이터 파이프라인 시작

### <a name="start-kafka-connect-cluster"></a>Kafka Connect 클러스터 시작

```bash
cd <KAFKA_HOME>/bin
./connect-distributed.sh ../config/connect-distributed.properties
```

### <a name="start-postgresql-connector-instance"></a>PostgreSQL 커넥터 인스턴스 시작

커넥터 구성 (JSON)을 파일 예제에 저장 합니다. `pg-source-config.json`

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

PostgreSQL 커넥터 인스턴스를 시작 하려면:

```bash
curl -X POST -H "Content-Type: application/json" --data @pg-source-config.json http://localhost:8083/connectors
```

> [!NOTE]
> 을 삭제 하려면 다음을 사용할 수 있습니다. `curl -X DELETE http://localhost:8083/connectors/pg-orders-source`


### <a name="insert-data"></a>데이터 삽입

테이블에는 `orders_info` 주문 ID, 고객 ID, 도시 등의 주문 정보가 포함 되어 있습니다. 아래 SQL을 사용 하 여 테이블을 임의의 데이터로 채웁니다.

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

테이블에 10 개의 레코드를 삽입 해야 합니다. 요구 사항 예제에 따라 아래의 레코드 수를 업데이트 하 여 `generate_series(1, 10)` 레코드를 삽입 하 고 다음을 사용 합니다. `100``generate_series(1, 100)`

확인 하려면 다음을 수행 합니다.

```bash
select * from retail.orders_info;
```

Kafka 토픽의 변경 데이터 캡처 이벤트를 확인 합니다.

> [!NOTE]
> 항목 이름은 `myserver.retail.orders_info` [커넥터 규칙](https://debezium.io/documentation/reference/1.3/connectors/postgresql.html#postgresql-topic-names) 에 따라 결정 됩니다.

```bash
cd <KAFKA_HOME>/bin

./kafka-console-consumer.sh --topic myserver.retail.orders_info --bootstrap-server localhost:9092 --from-beginning
```

JSON 형식의 변경 데이터 이벤트가 표시 되어야 합니다.

### <a name="start-datastax-apache-kafka-connector-instance"></a>DataStax Apache Kafka 커넥터 인스턴스 시작

커넥터 구성 (JSON)을 파일 예제에 저장 `cassandra-sink-config.json` 하 고 환경에 따라 속성을 업데이트 합니다.

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

커넥터 인스턴스를 시작 하려면:

```bash
curl -X POST -H "Content-Type: application/json" --data @cassandra-sink-config.json http://localhost:8083/connectors
```

커넥터는 작업을 스프링 하 고 종단 간 파이프라인이 PostgreSQL에서 Azure Cosmos DB 작동 하 게 됩니다.

### <a name="query-azure-cosmos-db"></a>Azure Cosmos DB 쿼리

Azure Cosmos DB에서 Cassandra 테이블을 확인 합니다. 다음은 시도해 볼 수 있는 몇 가지 쿼리입니다.

```sql
select count(*) from retail.orders_by_customer;
select count(*) from retail.orders_by_city;

select * from retail.orders_by_customer;
select * from retail.orders_by_city;

select * from retail.orders_by_city where city='Seattle';
select * from retail.orders_by_customer where customer_id = 10;
```

PostgreSQL에 더 많은 데이터를 계속 삽입 하 고 레코드가 Azure Cosmos DB에 동기화 되었는지 확인할 수 있습니다.

## <a name="next-steps"></a>다음 단계

* [Kafka Connect를 사용 하 여 Apache Kafka 및 Azure Cosmos DB Cassandra API 통합](cassandra-kafka-connect.md)
* [변경 데이터 캡처에 대 한 Debezium를 사용 하 여 Azure Event Hubs (미리 보기)에 Apache Kafka 연결 통합](../event-hubs/event-hubs-kafka-connect-debezium.md)
* [Blitzz를 사용 하 여 Oracle에서 Azure Cosmos DB Cassandra API로 데이터 마이그레이션](oracle-migrate-cosmos-db-blitzz.md)
* [컨테이너 및 데이터베이스의 처리량 프로비전](set-throughput.md) 
* [파티션 키 모범 사례](partitioning-overview.md#choose-partitionkey)
* [Azure Cosmos DB capacity planner 문서를 사용 하 여 r u/초 예측](estimate-ru-with-capacity-planner.md)

