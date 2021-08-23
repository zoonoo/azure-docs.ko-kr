---
title: Kafka Connect를 사용하여 Apache Kafka 및 Azure Cosmos DB Cassandra API 통합
description: DataStax Apache Kafka Connector를 사용하여 Kafka에서 Azure Cosmos DB Cassandra API 데이터를 수집하는 방법 알아보기
author: abhirockzz
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: how-to
ms.date: 12/14/2020
ms.author: abhishgu
ms.reviewer: abhishgu
ms.openlocfilehash: a980f1cbebf9579a776713209eaaaeb525575ad8
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122567125"
---
# <a name="ingest-data-from-apache-kafka-into-azure-cosmos-db-cassandra-api-using-kafka-connect"></a>Kafka Connect를 사용하여 Apache Kafka에서 Azure Cosmos DB Cassandra API로 데이터 수집
[!INCLUDE[appliesto-cassandra-api](../includes/appliesto-cassandra-api.md)]

기존 Cassandra 응용 프로그램은 [CQLv4 드라이버 호환성](https://cassandra.apache.org/doc/latest/cassandra/getting_started/drivers.html?highlight=driver)으로 인해 [Azure Cosmos DB Cassandra API](cassandra-introduction.md)와 쉽게 작업할 수 있습니다. 이 기능을 활용하여 [Apache Kafka](https://kafka.apache.org/) 같은 스트리밍 플랫폼과 통합하고 Azure Cosmos DB 데이터를 가져옵니다.

Apache Kafka(토픽)의 데이터는 다른 응용 프로그램에서 사용하거나 다른 시스템에 수집하는 경우에만 유용합니다. [선택한 언어와 클라이언트 SDK를 사용하는](https://cwiki.apache.org/confluence/display/KAFKA/Clients) [Kafka 생산자/소비자](https://kafka.apache.org/documentation/#api) API를 사용하여 솔루션을 빌드할 수 있습니다. Kafka Connect는 대체 솔루션을 제공합니다. 이는 확장 가능하고 신뢰할 수 있는 방식으로 Apache Kafka와 기타 시스템 간에 데이터를 스트림하는 플랫폼입니다. Kafka Connect는 Cassandra을 포함하는 기성 커넥터를 지원하므로, Kafka을 Azure Cosmos DB Cassandra API와 통합하는 사용자 지정 코드를 작성할 필요가 없습니다. 

이 문서에서는 오픈 소스 [DataStax Apache Kafka 커넥터](https://docs.datastax.com/en/kafka/doc/kafka/kafkaIntro.html)를 사용하며, 이는 kafka Connect 프레임 워크를 기반으로 하여 Kafka 토픽에서 하나 이상의 Cassandra 테이블의 행으로 레코드를 수집합니다. 이 예시에서는 Docker Compose를 사용하여 다시 사용할 수 있는 설정을 제공합니다. 이는 단일 명령을 사용하여 모든 필수 구성 요소를 로컬로 부트스트랩 시킬 수 있기 때문에 매우 편리합니다. 이러한 구성 요소에는 Kafka, Zookeeper, Kafka Connect 작업자 및 샘플 데이터 생성기 응용 프로그램이 포함됩니다.

다음은 구성 요소 및 해당 서비스 정의의 내역입니다. 이를 통해 [GitHub 리포지토리](https://github.com/Azure-Samples/cosmosdb-cassandra-kafka/blob/main/docker-compose.yaml)의 전체 `docker-compose` 파일을 참조할 수 있습니다.

- Kafka 및 Zookeeper는 [debezium](https://hub.docker.com/r/debezium/kafka/) 이미지를 사용합니다.
- Docker 컨테이너로 실행하려면, DataStax Apache Kafka 커넥터가 기존 Docker 이미지([debezium/connect-기반](https://github.com/debezium/docker-images/tree/master/connect-base/1.2)) 위에 구워집니다. 이 이미지에는 Kafka 및 해당 Kafka Connect 라이브러리 설치가 포함되어 있으므로 사용자 지정 커넥터를 추가하는 것이 매우 편리합니다. [Dockerfile](https://github.com/Azure-Samples/cosmosdb-cassandra-kafka/blob/main/connector/Dockerfile)을 참조할 수 있습니다.
- `data-generator`서비스는 임의로 생성된 데이터(JSON)를 `weather-data` Kafka 토픽에 시드합니다. [GitHub 리포지토리](https://github.com/Azure-Samples/cosmosdb-cassandra-kafka/blob/main/data-generator/)의 코드 및 `Dockerfile`을 참조할 수 있습니다

## <a name="prerequisites"></a>사전 요구 사항

* [Azure Cosmos DB Cassandra API 계정 프로비전](manage-data-dotnet.md#create-a-database-account)

* [유효성 검사를 위해 cqlsh 또는 호스트된 셸을 사용합니다](cassandra-support.md#hosted-cql-shell-preview)

* [Docker](https://docs.docker.com/get-docker/) 및 [Docker Compose](https://docs.docker.com/compose/install) 설치

## <a name="create-keyspace-tables-and-start-the-integration-pipeline"></a>Keyspace, 테이블 생성 및 통합 파이프라인 시작

Azure Portal 사용하여 Cassandra Keyspace 및 데모 응용 프로그램에 필요한 테이블을 생성합니다.

> [!NOTE]
> 아래와 동일한 Keyspace 및 테이블 이름을 사용합니다

```sql
CREATE KEYSPACE weather WITH REPLICATION = {'class' : 'NetworkTopologyStrategy', 'datacenter1' : 1};

CREATE TABLE weather.data_by_state (station_id text, temp int, state text, ts timestamp, PRIMARY KEY (state, ts)) WITH CLUSTERING ORDER BY (ts DESC) AND cosmosdb_cell_level_timestamp=true AND cosmosdb_cell_level_timestamp_tombstones=true AND cosmosdb_cell_level_timetolive=true;

CREATE TABLE weather.data_by_station (station_id text, temp int, state text, ts timestamp, PRIMARY KEY (station_id, ts)) WITH CLUSTERING ORDER BY (ts DESC) AND cosmosdb_cell_level_timestamp=true AND cosmosdb_cell_level_timestamp_tombstones=true AND cosmosdb_cell_level_timetolive=true;
```

GitHub 리포지토리를 복제합니다.

```bash
git clone https://github.com/Azure-Samples/cosmosdb-cassandra-kafka
cd cosmosdb-cassandra-kafka
```

서비스를 모두 시작합니다.

```shell
docker-compose --project-name kafka-cosmos-cassandra up --build
```

> [!NOTE]
> 컨테이너를 다운로드하고 시작하는 데 시간이 걸릴 수 있습니다. 이 프로세스는 한 번만 수행하면 됩니다.

모든 컨테이너가 시작되었는지 확인하려면 다음을 수행합니다.

```shell
docker-compose -p kafka-cosmos-cassandra ps
```

데이터 생성기 응용 프로그램은 Kafka의 `weather-data` 토픽으로 데이터를 펌프하기 시작합니다. 빠른 온전성 검사를 수행하여 확인할 수도 있습니다. Kafka Connect 작업자를 실행하는 Docker 컨테이너를 살펴보세요.


```bash
docker exec -it kafka-cosmos-cassandra_cassandra-connector_1 bash
```

컨테이너 셸에 놓으면, 일반적인 Kafka 콘솔 소비자 프로세스가 시작되며, 날씨 데이터(JSON 형식)가 유입되는 것을 볼 수 있습니다.

```bash
cd ../bin
./kafka-console-consumer.sh --bootstrap-server kafka:9092 --topic weather-data
```

## <a name="cassandra-sink-connector-setup"></a>Cassandra 싱크 커넥터 설정

아래 JSON 콘텐츠를 파일에 복사합니다(이름을 `cassandra-sink-config.json`로 지정할 수 있습니다). 설정에 따라 업데이트해야 하며, 이 섹션의 나머지 부분에서 이에 대한 지침을 제공합니다.

```json
{
    "name": "kafka-cosmosdb-sink",
    "config": {
        "connector.class": "com.datastax.oss.kafka.sink.CassandraSinkConnector",
        "tasks.max": "1",
        "topics": "weather-data",
        "contactPoints": "<cosmos db account name>.cassandra.cosmos.azure.com",
        "port": 10350,
        "loadBalancing.localDc": "<cosmos db region e.g. Southeast Asia>",
        "auth.username": "<enter username for cosmosdb account>",
        "auth.password": "<enter password for cosmosdb account>",
        "ssl.hostnameValidation": true,
        "ssl.provider": "JDK",
        "ssl.keystore.path": "/etc/alternatives/jre/lib/security/cacerts/",
        "ssl.keystore.password": "changeit",
        "datastax-java-driver.advanced.connection.init-query-timeout": 5000,
        "maxConcurrentRequests": 500,
        "maxNumberOfRecordsInBatch": 32,
        "queryExecutionTimeout": 30,
        "connectionPoolLocalSize": 4,
        "topic.weather-data.weather.data_by_state.mapping": "station_id=value.stationid, temp=value.temp, state=value.state, ts=value.created",
        "topic.weather-data.weather.data_by_station.mapping": "station_id=value.stationid, temp=value.temp, state=value.state, ts=value.created",
        "key.converter": "org.apache.kafka.connect.storage.StringConverter",
        "value.converter": "org.apache.kafka.connect.json.JsonConverter",
        "value.converter.schemas.enable": false,
        "offset.flush.interval.ms": 10000
    }
}
```

다음은 특성에 대한 개요입니다.

**기본 연결**

- `contactPoints`: Cosmos DB Cassandra에 대한 접점을 입력합니다
- `loadBalancing.localDc`: Cosmos DB 계정의 지역(예: 동남아시아)을 입력합니다
- `auth.username`: 사용자 이름을 입력합니다
- `auth.password`: 암호를 입력합니다
- `port`: 포트 값을 입력 합니다(`10350`이며 `9042`. 그대로 두지 않습니다)

**SSL 구성**

Azure Cosmos DB는 [SSL에 대한 보안 연결](../database-security.md) 적용하며, Kafka Connect 커넥터도 SSL을 지원합니다.

- `ssl.keystore.path`: 컨테이너의 JDK 키 저장소 경로- `/etc/alternatives/jre/lib/security/cacerts/`
- `ssl.keystore.password`: JDK 키 저장소(기본값) 암호
- `ssl.hostnameValidation`: 자체 노드 호스트 이름 유효성 검사를 설정합니다
- `ssl.provider`: `JDK`가 SSL 공급자로 사용됩니다

**제네릭 매개 변수**

- `key.converter`: 문자열 변환기 `org.apache.kafka.connect.storage.StringConverter`를 사용합니다
- `value.converter`: Kafka 토픽의 데이터는 JSON이므로 `org.apache.kafka.connect.json.JsonConverter`를 사용합니다
- `value.converter.schemas.enable`: JSON 페이로드에는 데모 앱 용도로 연결된 스키마가 없기 때문에, 이 특성을 `false`로 설정하여 Kafka Connect에서 스키마를 찾지 않도록 지시해야 합니다. 그렇게 하지 않으면 오류가 발생합니다.

### <a name="install-the-connector"></a>커넥터 설치

Kafka Connect REST 엔드포인트를 사용하여 커넥터를 설치합니다.

```shell
curl -X POST -H "Content-Type: application/json" --data @cassandra-sink-config.json http://localhost:8083/connectors
```

상태를 확인하려면 다음을 수행합니다.

```
curl http://localhost:8080/connectors/kafka-cosmosdb-sink/status
```

모두 제대로 작동하는 경우, 커넥터가 매직을 위빙해야 합니다. Azure Cosmos DB에 인증하고 Kafka 토픽(`weather-data`)에서 Cassandra 테이블로 데이터를 수집하기 시작합니다(`weather.data_by_state` 및 `weather.data_by_station`)

이제 테이블의 데이터를 쿼리할 수 있습니다. Azure Portal로 이동하여 Azure Cosmos DB 계정에 대해 호스트된 CQL Shell을 가져옵니다.

:::image type="content" source="./media/kafka-connect/cqlsh.png" alt-text="오픈 CQLSH":::

## <a name="query-data-from-azure-cosmos-db"></a>Azure Cosmos DB에서 데이터를 쿼리합니다

`data_by_state` 및 `data_by_station` 테이블을 확인합니다. 다음은 시작하기 위한 몇 가지 샘플 쿼리입니다.

```sql
select * from weather.data_by_state where state = 'state-1';
select * from weather.data_by_state where state IN ('state-1', 'state-2');
select * from weather.data_by_state where state = 'state-3' and ts > toTimeStamp('2020-11-26');

select * from weather.data_by_station where station_id = 'station-1';
select * from weather.data_by_station where station_id IN ('station-1', 'station-2');
select * from weather.data_by_station where station_id IN ('station-2', 'station-3') and ts > toTimeStamp('2020-11-26');
```

## <a name="clean-up-resources"></a>리소스 정리

[!INCLUDE [cosmosdb-delete-resource-group](../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>다음 단계

* [컨테이너 및 데이터베이스의 처리량 프로비전](../set-throughput.md) 
* [파티션 키 모범 사례](../partitioning-overview.md#choose-partitionkey)
* [Azure Cosmos DB capacity planner 문서를 사용하여 RU/s 예측](../estimate-ru-with-capacity-planner.md) 문서
