---
title: Kafka Connect를 사용 하 여 Apache Kafka 및 Azure Cosmos DB Cassandra API 통합
description: DataStax Apache Kafka 커넥터를 사용 하 여 Kafka에서 Azure Cosmos DB Cassandra API 데이터를 수집 하는 방법을 알아봅니다.
author: abhirockzz
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: how-to
ms.date: 12/14/2020
ms.author: abhishgu
ms.reviewer: abhishgu
ms.openlocfilehash: a233845e8f19cc44cd9d00a0392b1341db297fd6
ms.sourcegitcommit: 8c3a656f82aa6f9c2792a27b02bbaa634786f42d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/17/2020
ms.locfileid: "97632967"
---
# <a name="ingest-data-from-apache-kafka-into-azure-cosmos-db-cassandra-api-using-kafka-connect"></a>Kafka Connect를 사용 하 여 Apache Kafka에서 Azure Cosmos DB Cassandra API으로 데이터 수집
[!INCLUDE[appliesto-cassandra-api](includes/appliesto-cassandra-api.md)]

기존 Cassandra 응용 프로그램은 [CQLv4 드라이버 호환성](https://cassandra.apache.org/doc/latest/getting_started/drivers.html?highlight=driver)으로 인해 [Azure Cosmos DB Cassandra API](cassandra-introduction.md) 쉽게 작업할 수 있습니다. 이 기능을 활용 하 여 [Apache Kafka](https://kafka.apache.org/) 같은 스트리밍 플랫폼과 통합 하 고 Azure Cosmos DB 데이터를 가져옵니다.

Apache Kafka (토픽)의 데이터는 다른 응용 프로그램에서 사용 하거나 다른 시스템에 수집 하는 경우에만 유용 합니다. [선택한 언어와 클라이언트 SDK를 사용](https://cwiki.apache.org/confluence/display/KAFKA/Clients)하 여 [Kafka 생산자/소비자](https://kafka.apache.org/documentation/#api) api를 사용 하 여 솔루션을 빌드할 수 있습니다. Kafka Connect는 대체 솔루션을 제공 합니다. 확장 가능 하 고 신뢰할 수 있는 방식으로 Apache Kafka와 기타 시스템 간에 데이터를 스트림 하는 플랫폼입니다. Kafka Connect는 Cassandra을 포함 하는 선반 커넥터를 지원 하므로 Kafka을 Azure Cosmos DB Cassandra API와 통합 하는 사용자 지정 코드를 작성할 필요가 없습니다. 

이 문서에서는 kafka Connect 프레임 워크를 기반으로 하 여 Kafka 토픽에서 하나 이상의 Cassandra 테이블의 행으로 레코드를 수집 하는 오픈 소스 [DataStax Apache Kafka 커넥터](https://docs.datastax.com/kafka/doc/kafka/kafkaIntro.html)를 사용 합니다. 이 예제에서는 Docker Compose를 사용 하 여 다시 사용할 수 있는 설정을 제공 합니다. 이는 단일 명령을 사용 하 여 모든 필수 구성 요소를 로컬로 부트스트랩 시킬 수 있기 때문에 매우 편리 합니다. 이러한 구성 요소에는 Kafka, 사육 사, Kafka Connect 작업자 및 샘플 데이터 생성기 응용 프로그램이 포함 됩니다.

다음은 구성 요소 및 해당 서비스 정의를 분석 하는 것입니다 `docker-compose` . [GitHub 리포지토리의](https://github.com/Azure-Samples/cosmosdb-cassandra-kafka/blob/main/docker-compose.yaml)전체 파일을 참조할 수 있습니다.

- Kafka 및 사육 사가 [debezium](https://hub.docker.com/r/debezium/kafka/) 이미지를 사용 합니다.
- Docker 컨테이너로 실행 하려면 DataStax Apache Kafka 커넥터가 기존 Docker 이미지 ( [debezium/connect-](https://github.com/debezium/docker-images/tree/master/connect-base/1.2)기반) 위에 구운 됩니다. 이 이미지에는 Kafka 및 해당 Kafka Connect 라이브러리가 설치 되어 있어 사용자 지정 커넥터를 매우 편리 하 게 추가할 수 있습니다. [Dockerfile](https://github.com/Azure-Samples/cosmosdb-cassandra-kafka/blob/main/connector/Dockerfile)을 참조할 수 있습니다.
- `data-generator`서비스는 임의로 생성 된 (JSON) 데이터를 `weather-data` kafka 토픽으로 시드 합니다. `Dockerfile` [GitHub 리포지토리](https://github.com/Azure-Samples/cosmosdb-cassandra-kafka/blob/main/data-generator/) 및 코드를 참조할 수 있습니다.

## <a name="prerequisites"></a>필수 구성 요소

* [Azure Cosmos DB Cassandra API 계정 프로비전](create-cassandra-dotnet.md#create-a-database-account)

* [유효성 검사에 cqlsh 또는 hosted shell 사용](cassandra-support.md#hosted-cql-shell-preview)

* [Docker](https://docs.docker.com/get-docker/) 를 설치 하 고 [Docker Compose](https://docs.docker.com/compose/install)

## <a name="create-keyspace-tables-and-start-the-integration-pipeline"></a>Keyspace 테이블을 만들고 통합 파이프라인을 시작 합니다.

Azure Portal를 사용 하 여 데모 응용 프로그램에 필요한 Cassandra Keyspace 및 테이블을 만듭니다.

> [!NOTE]
> 아래와 같은 Keyspace 및 테이블 이름을 사용 합니다.

```sql
CREATE KEYSPACE weather WITH REPLICATION = {'class' : 'NetworkTopologyStrategy', 'datacenter1' : 1};

CREATE TABLE weather.data_by_state (station_id text, temp int, state text, ts timestamp, PRIMARY KEY (state, ts)) WITH CLUSTERING ORDER BY (ts DESC) AND cosmosdb_cell_level_timestamp=true AND cosmosdb_cell_level_timestamp_tombstones=true AND cosmosdb_cell_level_timetolive=true;

CREATE TABLE weather.data_by_station (station_id text, temp int, state text, ts timestamp, PRIMARY KEY (station_id, ts)) WITH CLUSTERING ORDER BY (ts DESC) AND cosmosdb_cell_level_timestamp=true AND cosmosdb_cell_level_timestamp_tombstones=true AND cosmosdb_cell_level_timetolive=true;
```

GitHub 리포지토리를 복제 합니다.

```bash
git clone https://github.com/Azure-Samples/cosmosdb-cassandra-kafka
cd cosmosdb-cassandra-kafka
```

모든 서비스를 시작 합니다.

```shell
docker-compose --project-name kafka-cosmos-cassandra up --build
```

> [!NOTE]
> 컨테이너를 다운로드 하 고 시작 하는 데 시간이 걸릴 수 있습니다 .이 작업은 한 번만 수행 하면 됩니다.

모든 컨테이너가 시작 되었는지 여부를 확인 하려면 다음을 수행 합니다.

```shell
docker-compose -p kafka-cosmos-cassandra ps
```

데이터 생성기 응용 프로그램은 `weather-data` Kafka의 토픽으로 데이터를 펌프 하기 시작 합니다. 빠른 온전성 검사를 수행 하 여 확인할 수도 있습니다. Kafka connect 작업자를 실행 하는 Docker 컨테이너를 피킹 합니다.


```bash
docker exec -it kafka-cosmos-cassandra_cassandra-connector_1 bash
```

컨테이너 셸에 놓으면 일반적인 Kafka 콘솔 소비자 프로세스를 시작 하 고에서 흐르는 날씨 데이터 (JSON 형식)가 표시 되어야 합니다.

```bash
cd ../bin
./kafka-console-consumer.sh --bootstrap-server kafka:9092 --topic weather-data
```

## <a name="cassandra-sink-connector-setup"></a>Cassandra 싱크 커넥터 설정

아래 JSON 콘텐츠를 파일에 복사 합니다 (이름을 지정할 수 있음 `cassandra-sink-config.json` ). 설정에 따라 업데이트 해야 하며,이 섹션의 나머지 부분에서는이 항목에 대 한 지침을 제공 합니다.

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

특성에 대 한 요약은 다음과 같습니다.

**기본 연결**

- `contactPoints`: Cosmos DB Cassandra에 대 한 연락처 지점을 입력 하세요.
- `loadBalancing.localDc`: Cosmos DB 계정에 대 한 지역 (예: 동남 아시아)을 입력 합니다.
- `auth.username`: 사용자 이름을 입력 합니다.
- `auth.password`: 암호를 입력 합니다.
- `port`: 포트 값을 입력 `10350` `9042` 합니다. 그대로 유지)

**SSL 구성**

SSL을 [통해 보안 연결](database-security.md) 을 적용 하 Azure Cosmos DB Kafka Connect 커넥터는 ssl도 지원 합니다.

- `ssl.keystore.path`: 컨테이너의 JDK 키 저장소 경로- `/etc/alternatives/jre/lib/security/cacerts/`
- `ssl.keystore.password`: JDK 키 저장소 (기본값) 암호
- `ssl.hostnameValidation`: 고유한 노드 호스트 이름 유효성 검사를 설정 합니다.
- `ssl.provider`: `JDK` SSL 공급자로 사용 됩니다.

**제네릭 매개 변수**

- `key.converter`: 문자열 변환기를 사용 합니다. `org.apache.kafka.connect.storage.StringConverter`
- `value.converter`: Kafka 토픽의 데이터는 JSON 이므로 다음을 사용 합니다. `org.apache.kafka.connect.json.JsonConverter`
- `value.converter.schemas.enable`:이 JSON 페이로드에는 데모 앱의 용도로 연결 된 스키마가 없기 때문에이 특성을로 설정 하 여 Kafka Connect에서 스키마를 찾지 않도록 지시 해야 `false` 합니다. 그렇게 하지 않으면 오류가 발생 합니다.

### <a name="install-the-connector"></a>커넥터 설치

Kafka Connect REST 끝점을 사용 하 여 커넥터를 설치 합니다.

```shell
curl -X POST -H "Content-Type: application/json" --data @cassandra-sink-config.json http://localhost:8083/connectors
```

상태를 확인 하려면:

```
curl http://localhost:8080/connectors/kafka-cosmosdb-sink/status
```

모두 제대로 작동 하는 경우 커넥터가 마법 weaving 시작 해야 합니다. Azure Cosmos DB에 인증 하 고 Kafka 토픽 ()에서 Cassandra 테이블로 데이터를 수집 시작 하 고 `weather-data` `weather.data_by_state` , `weather.data_by_station`

이제 테이블의 데이터를 쿼리할 수 있습니다. Azure Portal로 이동 하 여 Azure Cosmos DB 계정에 대해 호스트 된 CQL Shell을 가져옵니다.

:::image type="content" source="./media/cassandra-kafka-connect/cqlsh.png" alt-text="오픈 CQLSH":::

## <a name="query-data-from-azure-cosmos-db"></a>Azure Cosmos DB에서 데이터 쿼리

`data_by_state`및 테이블을 확인 `data_by_station` 합니다. 다음은 시작 하기 위한 몇 가지 샘플 쿼리입니다.

```sql
select * from weather.data_by_state where state = 'state-1';
select * from weather.data_by_state where state IN ('state-1', 'state-2');
select * from weather.data_by_state where state = 'state-3' and ts > toTimeStamp('2020-11-26');

select * from weather.data_by_station where station_id = 'station-1';
select * from weather.data_by_station where station_id IN ('station-1', 'station-2');
select * from weather.data_by_station where station_id IN ('station-2', 'station-3') and ts > toTimeStamp('2020-11-26');
```

## <a name="clean-up-resources"></a>리소스 정리

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>다음 단계

* [컨테이너 및 데이터베이스의 처리량 프로비전](set-throughput.md) 
* [파티션 키 모범 사례](partitioning-overview.md#choose-partitionkey)
* [Azure Cosmos DB capacity planner 문서를 사용 하 여 r u/초 예측](estimate-ru-with-capacity-planner.md)
