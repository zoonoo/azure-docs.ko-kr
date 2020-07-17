---
title: Apache Kafka용 Apache Flink 사용 - Azure Event Hubs | Microsoft Docs
description: 이 문서에서는 Azure 이벤트 허브에 Apache Flink를 연결 하는 방법에 대 한 정보를 제공 합니다.
ms.topic: how-to
ms.date: 06/23/2020
ms.openlocfilehash: 43cbf99a6ba2c0384ceffc10b01916f6ad22b26a
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85320141"
---
# <a name="use-apache-flink-with-azure-event-hubs-for-apache-kafka"></a>Apache Kafka용 Azure Event Hubs에서 Apache Flink 사용
이 자습서에서는 프로토콜 클라이언트를 변경 하거나 자체 클러스터를 실행 하지 않고도 Apache Flink를 이벤트 허브에 연결 하는 방법을 보여 줍니다. Azure Event Hubs는 [Apache Kafka 버전 1.0를](https://kafka.apache.org/10/documentation.html)지원 합니다.

Apache Kafka를 사용하는 주요 이점 중 하나는 연결할 수 있는 프레임워크의 에코시스템입니다. Event Hubs은 Azure 에코 시스템의 확장성, 일관성 및 지원과 함께 Kafka의 유연성을 결합 합니다.

이 자습서에서는 다음과 같은 작업을 수행하는 방법을 살펴봅니다.
> [!div class="checklist"]
> * Event Hubs 네임스페이스 만들기
> * 프로젝트 예제 복제
> * Flink 생산자 실행 
> * Flink 소비자 실행

> [!NOTE]
> 이 샘플은 [GitHub](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/flink)에서 사용할 수 있습니다.

## <a name="prerequisites"></a>사전 요구 사항

이 자습서를 완료하려면 다음 필수 구성 요소가 있어야 합니다.

* [Apache Kafka용 Event Hubs](event-hubs-for-kafka-ecosystem-overview.md) 문서를 참조하세요. 
* Azure 구독 구독이 없으면 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)을 만듭니다.
* [JDK (Java Development Kit) 1.7 이상](https://aka.ms/azure-jdks)
    * Ubuntu에서 `apt-get install default-jdk`를 실행하여 JDK를 설치합니다.
    * JAVA_HOME 환경 변수가 반드시 JDK가 설치된 폴더를 지정하도록 설정합니다.
* Maven 이진 보관 파일 [다운로드](https://maven.apache.org/download.cgi) 및 [설치](https://maven.apache.org/install.html)
    * Ubuntu에서 `apt-get install maven`을 실행하여 Maven을 실행할 수 있습니다.
* [Git](https://www.git-scm.com/downloads)
    * Ubuntu에서 `sudo apt-get install git`를 실행하여 Git를 실행할 수 있습니다.

## <a name="create-an-event-hubs-namespace"></a>Event Hubs 네임스페이스 만들기

Event Hubs 서비스에서 보내거나 받으려면 Event Hubs 네임스페이스가 필요합니다. 네임 스페이스 및 이벤트 허브를 만드는 방법에 대 한 지침은 [이벤트 허브 만들기](event-hubs-create.md) 를 참조 하세요. 나중에 사용하기 위해 Event Hubs 연결 문자열을 복사합니다.

## <a name="clone-the-example-project"></a>프로젝트 예제 복제

이제 연결 문자열 Event Hubs 했으므로 Kafka 리포지토리의 Azure Event Hubs를 복제 하 고 `flink` 하위 폴더로 이동 합니다.

```shell
git clone https://github.com/Azure/azure-event-hubs-for-kafka.git
cd azure-event-hubs-for-kafka/tutorials/flink
```

## <a name="run-flink-producer"></a>Flink 생산자 실행

제공된 Flink 생산자 예제를 사용하여 Event Hubs 서비스에 메시지를 보냅니다.

### <a name="provide-an-event-hubs-kafka-endpoint"></a>Event Hubs Kafka 엔드포인트 제공

#### <a name="producerconfig"></a>producer.config

생산자에서 Event Hubs Kafka 엔드포인트를 가리키도록 `producer/src/main/resources/producer.config`에서 `bootstrap.servers` 및 `sasl.jaas.config` 값을 업데이트합니다.

```xml
bootstrap.servers={YOUR.EVENTHUBS.FQDN}:9093
client.id=FlinkExampleProducer
sasl.mechanism=PLAIN
security.protocol=SASL_SSL
sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required \
   username="$ConnectionString" \
   password="{YOUR.EVENTHUBS.CONNECTION.STRING}";
```

### <a name="run-producer-from-the-command-line"></a>명령줄에서 생성자 실행

명령줄에서 생성자를 실행하려면 JAR을 생성한 다음, Maven 내에서 실행하거나, Maven을 사용하여 JAR을 생성한 다음, 필요한 Kafka JAR을 클래스 경로에 추가하여 Java에서 실행합니다.

```shell
mvn clean package
mvn exec:java -Dexec.mainClass="FlinkTestProducer"
```

이제 생산자는 토픽에서 이벤트 허브로 이벤트를 보내고 stdout에 이벤트를 인쇄 하기 시작 합니다 `test` .

## <a name="run-flink-consumer"></a>Flink 소비자 실행

제공 된 소비자 예제를 사용 하 여 이벤트 허브에서 메시지를 수신 합니다. 

### <a name="provide-an-event-hubs-kafka-endpoint"></a>Event Hubs Kafka 엔드포인트 제공

#### <a name="consumerconfig"></a>consumer.config

소비자에서 Event Hubs Kafka 엔드포인트를 가리키도록 `consumer/src/main/resources/consumer.config`에서 `bootstrap.servers` 및 `sasl.jaas.config` 값을 업데이트합니다.

```xml
bootstrap.servers={YOUR.EVENTHUBS.FQDN}:9093
group.id=FlinkExampleConsumer
sasl.mechanism=PLAIN
security.protocol=SASL_SSL
sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required \
   username="$ConnectionString" \
   password="{YOUR.EVENTHUBS.CONNECTION.STRING}";
```

### <a name="run-consumer-from-the-command-line"></a>명령줄에서 소비자 실행

명령줄에서 소비자를 실행하려면 JAR을 생성하고 Maven 내에서 실행하거나, Maven을 사용하여 JAR을 생성하고 필요한 Kafka JAR을 클래스 경로에 추가하여 Java에서 실행합니다.

```shell
mvn clean package
mvn exec:java -Dexec.mainClass="FlinkTestConsumer"
```

이벤트 허브에 이벤트가 있는 경우 (예: 생산자가 실행 중인 경우) 소비자는 이제 토픽에서 이벤트를 수신 하기 시작 합니다 `test` .

Flink를 Kafka에 연결하는 방법에 대한 자세한 내용은 [Flink의 Kafka 커넥터 가이드](https://ci.apache.org/projects/flink/flink-docs-stable/dev/connectors/kafka.html)를 확인하세요.

## <a name="next-steps"></a>다음 단계
Kafka에 대 한 Event Hubs에 대해 자세히 알아보려면 다음 문서를 참조 하세요.  

- [이벤트 허브에서 Kafka broker 미러링](event-hubs-kafka-mirror-maker-tutorial.md)
- [이벤트 허브에 Apache Spark 연결](event-hubs-kafka-spark-tutorial.md)
- [Kafka Connect를 이벤트 허브와 통합](event-hubs-kafka-connect-tutorial.md)
- [GitHub에서 더 많은 샘플 탐색](https://github.com/Azure/azure-event-hubs-for-kafka)
- [Akka streams 스트림을 이벤트 허브에 연결](event-hubs-kafka-akka-streams-tutorial.md)
- [Azure Event Hubs에 대 한 Apache Kafka 개발자 가이드](apache-kafka-developer-guide.md)