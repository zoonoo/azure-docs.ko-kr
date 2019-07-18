---
title: Apache Kafka Connect와의 통합 - Azure Event Hubs | Microsoft Docs
description: 이 문서에서는 Kafka에 대한 Azure Event Hubs에서 Apache Spark를 사용하는 방법을 설명합니다.
services: event-hubs
documentationcenter: .net
author: basilhariri
manager: timlt
ms.service: event-hubs
ms.topic: tutorial
ms.custom: seodec18
ms.date: 12/06/2018
ms.author: bahariri
ms.openlocfilehash: 2ed4432aec9b833efe6b521b4452177088d21d70
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2019
ms.locfileid: "58119414"
---
# <a name="integrate-apache-kafka-connect-support-on-azure-event-hubs-preview"></a>Azure Event Hubs(미리 보기)에 Apache Kafka Connect 지원 통합
수집되는 비즈니스 요구 사항이 증가함에 따라 다양한 외부 소스 및 싱크에 대한 수집 요구 사항도 증가합니다. [Apache Kafka Connect](https://kafka.apache.org/documentation/#connect)는 Kafka 클러스터를 통해 MySQL, HDFS 같은 외부 시스템 및 파일 시스템에 연결하고 데이터를 가져오는/내보내는 프레임워크를 제공합니다. 이 자습서에서는 Kafka 지원 Event hubs와 함께 Kafka Connect 프레임워크를 사용하는 방법을 안내합니다.

이 자습서에서는 Kafka Connect를 Kafka 지원 Azure 이벤트 허브와 통합하고 기본 FileStreamSource 및 FileStreamSink 커넥터를 배포하는 방법을 안내합니다. 이 기능은 현재 미리 보기로 제공됩니다. 이러한 커넥터는 프로덕션 용도로 제작된 것이 아니지만, Azure Event Hubs가 Kafka broker 역할을 하는 엔드투엔드 Kafka Connect 시나리오를 보여줍니다.

> [!NOTE]
> 이 샘플은 [GitHub](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/connect)에서 사용할 수 있습니다.

이 자습서에서 수행하는 단계는 다음과 같습니다.

> [!div class="checklist"]
> * Event Hubs 네임스페이스 만들기
> * 프로젝트 예제 복제
> * Event Hubs에 대해 Kafka Connect 구성
> * Kafka Connect 실행
> * 커넥터 만들기

## <a name="prerequisites"></a>필수 조건
이 연습을 완료하려면 다음 필수 구성 요소가 있어야 합니다.

- 동작합니다. 아직 없는 경우 [체험 계정](https://azure.microsoft.com/free/)을 만들 수 있습니다.
- [Git](https://www.git-scm.com/downloads)
- Linux/MacOS
- Kafka 릴리스(버전 1.1.1, Scala 버전 2.11), [kafka.apache.org](https://kafka.apache.org/downloads#1.1.1)에서 제공
- [Apache Kafka용 Event Hubs](https://docs.microsoft.com/azure/event-hubs/event-hubs-for-kafka-ecosystem-overview) 지침 문서를 참조하세요.

## <a name="create-an-event-hubs-namespace"></a>Event Hubs 네임스페이스 만들기
Event Hubs 서비스와 통신하려면 Event Hubs 네임스페이스가 필요합니다. Event Hubs Kafka 엔드포인트를 가져오는 방법에 대한 지침은 [Kafka 지원 Event Hub 만들기](event-hubs-create.md)를 참조하세요. 나중에 사용할 수 있도록 Event Hubs 연결 문자열 및 FQDN(정규화된 도메인 이름)을 가져옵니다. 자세한 지침은 [Event Hubs 연결 문자열 가져오기](event-hubs-get-connection-string.md)를 참조하세요. 

## <a name="clone-the-example-project"></a>프로젝트 예제 복제
Azure Event Hubs 리포지토리를 복제하고 자습서/연결 하위 폴더로 이동합니다. 

```
git clone https://github.com/Azure/azure-event-hubs-for-kafka.git
cd azure-event-hubs-for-kafka/tutorials/connect
```

## <a name="configure-kafka-connect-for-event-hubs"></a>Event Hubs에 대해 Kafka Connect 구성
Kafka에서 Event Hubs로의 Kafka Connect 처리량을 리디렉션할 때 최소한의 재구성이 필요합니다.  다음 `connect-distributed.properties` 샘플은 Event Hubs에서 Kafka 엔드포인트를 인증하고 통신하도록 Connect를 구성하는 방법을 보여줍니다.

```properties
bootstrap.servers={YOUR.EVENTHUBS.FQDN}:9093 # e.g. namespace.servicebus.windows.net:9093
group.id=connect-cluster-group

# connect internal topic names, auto-created if not exists
config.storage.topic=connect-cluster-configs
offset.storage.topic=connect-cluster-offsets
status.storage.topic=connect-cluster-status

# internal topic replication factors - auto 3x replication in Azure Storage
config.storage.replication.factor=1
offset.storage.replication.factor=1
status.storage.replication.factor=1

rest.advertised.host.name=connect
offset.flush.interval.ms=10000

key.converter=org.apache.kafka.connect.json.JsonConverter
value.converter=org.apache.kafka.connect.json.JsonConverter
internal.key.converter=org.apache.kafka.connect.json.JsonConverter
internal.value.converter=org.apache.kafka.connect.json.JsonConverter

internal.key.converter.schemas.enable=false
internal.value.converter.schemas.enable=false

# required EH Kafka security settings
security.protocol=SASL_SSL
sasl.mechanism=PLAIN
sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="{YOUR.EVENTHUBS.CONNECTION.STRING}";

producer.security.protocol=SASL_SSL
producer.sasl.mechanism=PLAIN
producer.sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="{YOUR.EVENTHUBS.CONNECTION.STRING}";

consumer.security.protocol=SASL_SSL
consumer.sasl.mechanism=PLAIN
consumer.sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="{YOUR.EVENTHUBS.CONNECTION.STRING}";

plugin.path={KAFKA.DIRECTORY}/libs # path to the libs directory within the Kafka release
```

## <a name="run-kafka-connect"></a>Kafka Connect 실행

이 단계에서 Kafka Connect 작업자는 로컬에서 분산 모드로 시작되고, Event Hubs를 사용하여 클러스터 상태를 유지합니다.

1. 위의 `connect-distributed.properties` 파일을 로컬에 저장합니다.  중괄호 안에 있는 모든 값을 바꿔야 합니다.
2. 사용하는 머신에서 Kafka 릴리스가 있는 위치로 이동합니다.
4. `./bin/connect-distributed.sh /PATH/TO/connect-distributed.properties`을 실행합니다.  `'INFO Finished starting connectors and tasks'` 메시지가 보이면 Connect 작업자 REST API와 상호 작용할 준비가 완료된 것입니다. 

> [!NOTE]
> Event Hubs는 자동으로 토픽을 만드는 Kafka 클라이언트를 지원합니다. Azure Portal에서 네임스페이스를 신속하게 살펴보면 Connect 작업자의 내부 토픽이 자동으로 만들어진 것을 알 수 있습니다.

### <a name="create-connectors"></a>커넥터 만들기
이 섹션에서는 FileStreamSource 및 FileStreamSink 커넥터를 작동하는 방법을 안내합니다. 

1. 입력 및 출력 데이터 파일에 사용할 디렉터리를 만듭니다.
    ```bash
    mkdir ~/connect-quickstart
    ```

2. 두 파일을 만듭니다. 한 파일에는 FileStreamSource 커넥터가 읽는 시드 데이터가 포함되고, 다른 파일에는 FileStreamSink 커넥터가 데이터를 씁니다.
    ```bash
    seq 1000 > ~/connect-quickstart/input.txt
    touch ~/connect-quickstart/output.txt
    ```

3. FileStreamSource 커넥터를 만듭니다.  중괄호를 홈 디렉터리 경로로 바꿔야 합니다.
    ```bash
    curl -s -X POST -H "Content-Type: application/json" --data '{"name": "file-source","config": {"connector.class":"org.apache.kafka.connect.file.FileStreamSourceConnector","tasks.max":"1","topic":"connect-quickstart","file": "{YOUR/HOME/PATH}/connect-quickstart/input.txt"}}' http://localhost:8083/connectors
    ```
    위의 명령을 실행하면 Event Hubs 인스턴스에 이벤트 허브 `connect-quickstart`가 보일 것입니다.
4. 원본 커넥터의 상태를 확인합니다.
    ```bash
    curl -s http://localhost:8083/connectors/file-source/status
    ```
    원한다면 [Service Bus Explorer](https://github.com/paolosalvatori/ServiceBusExplorer/releases)를 사용하여 `connect-quickstart` 토픽에 이벤트가 도착했는지 확인할 수 있습니다.

5. FileStreamSink 커넥터를 만듭니다.  마찬가지로 중괄호를 홈 디렉터리 경로로 바꿔야 합니다.
    ```bash
    curl -X POST -H "Content-Type: application/json" --data '{"name": "file-sink", "config": {"connector.class":"org.apache.kafka.connect.file.FileStreamSinkConnector", "tasks.max":"1", "topics":"connect-quickstart", "file": "{YOUR/HOME/PATH}/connect-quickstart/output.txt"}}' http://localhost:8083/connectors
    ```
 
6. 싱크 커넥터의 상태를 확인합니다.
    ```bash
    curl -s http://localhost:8083/connectors/file-sink/status
    ```

7. 파일 간에 데이터가 복제되었는지, 두 파일의 데이터가 동일한지 확인합니다.
    ```bash
    # read the file
    cat ~/connect-quickstart/output.txt
    # diff the input and output files
    diff ~/connect-quickstart/input.txt ~/connect-quickstart/output.txt
    ```

### <a name="cleanup"></a>정리
Kafka Connect는 Connect 클러스터가 중단된 후에도 유지되는 구성, 오프셋 및 상태를 저장하는 이벤트 허브 토픽을 만듭니다. 이 지속성을 원하지 않는다면 이러한 토픽을 삭제하는 것이 좋습니다. 이 연습의 과정에서 만든 `connect-quickstart` 이벤트 허브를 삭제할 수도 있습니다.

## <a name="next-steps"></a>다음 단계

Event Hubs 및 Kafka용 Event Hubs에 대해 자세히 알아보려면 다음 항목을 참조하세요.  

- [Event Hubs에 대해 알아봅니다](event-hubs-what-is-event-hubs.md).
- [Apache Kafka용 Event Hubs](event-hubs-for-kafka-ecosystem-overview.md)
- [Kafka 사용 Event Hubs 만드는 방법](event-hubs-create-kafka-enabled.md)
- [Kafka 애플리케이션에서 이벤트 허브로 스트리밍](event-hubs-quickstart-kafka-enabled-event-hubs.md)
- [Kafka 지원 이벤트 허브에서 Kafka broker 미러링](event-hubs-kafka-mirror-maker-tutorial.md)
- [Kafka 지원 이벤트 허브에 Apache Spark 연결](event-hubs-kafka-spark-tutorial.md)
- [Kafka 지원 이벤트 허브에 Apache Flink 연결](event-hubs-kafka-flink-tutorial.md)
- [Kafka 지원 이벤트 허브에 Akka Streams 연결](event-hubs-kafka-akka-streams-tutorial.md)
- [GitHub에서 더 많은 샘플 탐색](https://github.com/Azure/azure-event-hubs-for-kafka)