---
title: Apache Kafka MirrorMaker 사용 - Azure Event Hubs | Microsoft Docs
description: 이 문서에서는 Kafka MirrorMaker를 사용하여 Azure Event Hubs에서 Kafka 클러스터를 미러링하는 방법에 대한 정보를 제공합니다.
ms.topic: how-to
ms.date: 06/23/2020
ms.openlocfilehash: d1ec20a32ef27856483492212608e20e82725f58
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2020
ms.locfileid: "92369525"
---
# <a name="use-kafka-mirrormaker-with-event-hubs-for-apache-kafka"></a>Apache Kafka용 Event Hubs에서 Kafka MirrorMaker 사용

이 자습서에서는 Kafka MirrorMaker를 사용 하 여 이벤트 허브에서 Kafka broker를 미러링 하는 방법을 보여 줍니다.

   ![Event Hubs를 사용하는 Kafka MirrorMaker](./media/event-hubs-kafka-mirror-maker-tutorial/evnent-hubs-mirror-maker1.png)

> [!NOTE]
> 이 샘플은 [GitHub](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/mirror-maker)에서 사용할 수 있습니다.


이 자습서에서는 다음 작업 방법을 알아봅니다.
> [!div class="checklist"]
> * Event Hubs 네임스페이스 만들기
> * 프로젝트 예제 복제
> * Kafka 클러스터 설정
> * Kafka MirrorMaker 구성
> * Kafka MirrorMaker 실행

## <a name="introduction"></a>소개
최신 클라우드 크기 조정 앱에 대한 주요 고려 사항 중 하나는 서비스를 중단하지 않고 인프라를 업데이트, 개선 및 변경하는 기능입니다. 이 자습서에서는 이벤트 허브 및 Kafka MirrorMaker가 Event Hubs 서비스에서 Kafka 입력 스트림을 "미러링" 하 여 Azure에 기존 Kafka 파이프라인을 통합 하는 방법을 보여 줍니다. 

Azure Event Hubs Kafka 엔드포인트를 사용하면 Kafka 프로토콜(즉, Kafka 클라이언트)을 사용하여 Azure Event Hubs에 연결할 수 있습니다. Kafka 애플리케이션을 최소한으로 변경하면 Azure Event Hubs에 연결하여 Azure 에코시스템의 이점을 누릴 수 있습니다. Event Hubs 현재 Kafka 버전 1.0 이상을 지원 합니다.

## <a name="prerequisites"></a>사전 요구 사항

이 자습서를 완료하려면 다음이 설치되어 있어야 합니다.

* [Apache Kafka용 Event Hubs](event-hubs-for-kafka-ecosystem-overview.md) 문서를 참조하세요. 
* Azure 구독 구독이 없으면 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)을 만듭니다.
* [JDK (Java Development Kit) 1.7 이상](/azure/developer/java/fundamentals/java-jdk-long-term-support)
    * Ubuntu에서 `apt-get install default-jdk`를 실행하여 JDK를 설치합니다.
    * JAVA_HOME 환경 변수가 반드시 JDK가 설치된 폴더를 지정하도록 설정합니다.
* Maven 이진 보관 파일 [다운로드](https://maven.apache.org/download.cgi) 및 [설치](https://maven.apache.org/install.html)
    * Ubuntu에서 `apt-get install maven`을 실행하여 Maven을 실행할 수 있습니다.
* [Git](https://www.git-scm.com/downloads)
    * Ubuntu에서 `sudo apt-get install git`를 실행하여 Git를 실행할 수 있습니다.

## <a name="create-an-event-hubs-namespace"></a>Event Hubs 네임스페이스 만들기

Event Hubs 서비스와 통신하려면 Event Hubs 네임스페이스가 필요합니다. 네임 스페이스 및 이벤트 허브를 만드는 방법에 대 한 지침은 [이벤트 허브 만들기](event-hubs-create.md) 를 참조 하세요. 나중에 사용하기 위해 Event Hubs 연결 문자열을 복사합니다.

## <a name="clone-the-example-project"></a>프로젝트 예제 복제

이제 연결 문자열 Event Hubs 있으므로 Kafka 리포지토리의 Azure Event Hubs를 복제 하 고 `mirror-maker` 하위 폴더로 이동 합니다.

```shell
git clone https://github.com/Azure/azure-event-hubs-for-kafka.git
cd azure-event-hubs-for-kafka/tutorials/mirror-maker
```

## <a name="set-up-a-kafka-cluster"></a>Kafka 클러스터 설정

[Kafka 빠른 시작 가이드](https://kafka.apache.org/quickstart)를 사용하여 원하는 설정으로 클러스터를 설정하거나 기존 Kafka 클러스터를 사용합니다.

## <a name="configure-kafka-mirrormaker"></a>Kafka MirrorMaker 구성

Kafka MirrorMaker를 사용하면 스트림을 "미러"할 수 있습니다. 원본 및 대상 Kafka 클러스터가 지정되면 MirrorMaker는 원본 클러스터에 보낸 모든 메시지를 원본 및 대상 클러스터 모두에서 받을 수 있도록 합니다. 이 예제에서는 대상 이벤트 허브를 사용 하 여 원본 Kafka 클러스터를 미러링 하는 방법을 보여 줍니다. 이 시나리오는 데이터 흐름을 중단하지 않고 기존 Kafka 파이프라인에서 Event Hubs로 데이터를 보내는 데 사용할 수 있습니다. 

Kafka MirrorMaker에 대한 자세한 내용은 [Kafka 미러링/MirrorMaker 가이드](https://cwiki.apache.org/confluence/pages/viewpage.action?pageId=27846330)를 참조하세요.

Kafka MirrorMaker를 구성 하려면 해당 소비자/원본으로 kafstcluster를 제공 하 고, 해당 생산자/destination으로 이벤트 허브를 제공 합니다.

#### <a name="consumer-configuration"></a>소비자 구성

MirrorMaker에 원본 Kafka 클러스터의 속성을 알려주는 `source-kafka.config` 소비자 구성 파일을 업데이트합니다.

##### <a name="source-kafkaconfig"></a>source-kafka.config

```
bootstrap.servers={SOURCE.KAFKA.IP.ADDRESS1}:{SOURCE.KAFKA.PORT1},{SOURCE.KAFKA.IP.ADDRESS2}:{SOURCE.KAFKA.PORT2},etc
group.id=example-mirrormaker-group
exclude.internal.topics=true
client.id=mirror_maker_consumer
```

#### <a name="producer-configuration"></a>생산자 구성

이제 MirrorMaker에서 복제된(또는 "미러된") 데이터를 Event Hubs 서비스에 보내도록 지시하는 `mirror-eventhub.config` 생산자 구성 파일을 업데이트합니다. 특히 Event Hubs Kafka 엔드포인트를 가리키도록 `bootstrap.servers`과 `sasl.jaas.config`를 변경합니다. Event Hubs 서비스에는 다음 구성에서 마지막 세 가지 속성을 설정하여 얻을 수 있는 보안 통신(SASL)이 필요합니다. 

##### <a name="mirror-eventhubconfig"></a>mirror-eventhub.config

```
bootstrap.servers={YOUR.EVENTHUBS.FQDN}:9093
client.id=mirror_maker_producer

#Required for Event Hubs
sasl.mechanism=PLAIN
security.protocol=SASL_SSL
sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="{YOUR.EVENTHUBS.CONNECTION.STRING}";
```

> [!IMPORTANT]
> `{YOUR.EVENTHUBS.CONNECTION.STRING}`을 Event Hubs 네임스페이스의 연결 문자열로 바꿉니다. 연결 문자열을 가져오는 방법에 대 한 지침은 [Event Hubs 연결 문자열 가져오기](event-hubs-get-connection-string.md)를 참조 하세요. 구성 예는 다음과 같습니다. `sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="Endpoint=sb://mynamespace.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=XXXXXXXXXXXXXXXX";`

## <a name="run-kafka-mirrormaker"></a>Kafka MirrorMaker 실행

새로 업데이트된 구성 파일을 사용하여 루트 Kafka 디렉터리에서 Kafka MirrorMaker 스크립트를 실행합니다. 구성 파일을 루트 Kafka 디렉터리에 복사하거나 다음 명령에서 해당 경로를 업데이트해야 합니다.

```shell
bin/kafka-mirror-maker.sh --consumer.config source-kafka.config --num.streams 1 --producer.config mirror-eventhub.config --whitelist=".*"
```

이벤트가 이벤트 허브에 도달 하는지 확인 하려면 [Azure Portal](https://azure.microsoft.com/features/azure-portal/)에서 수신 통계를 참조 하거나 이벤트 허브에 대해 소비자를 실행 합니다.

MirrorMaker를 실행 하면 Kafka 클러스터와 미러된 이벤트 허브 모두에서 원본 Kafka 클러스터로 전송 된 모든 이벤트를 수신 합니다. MirrorMaker 및 Event Hubs Kafka 엔드포인트를 사용하면, 기존 클러스터를 변경하거나 진행 중인 데이터 흐름을 중단하지 않고도 기존 Kafka 파이프라인을 관리되는 Azure Event Hubs 서비스로 마이그레이션할 수 있습니다.

## <a name="samples"></a>샘플
GitHub에서 다음 샘플을 참조하세요.

- [GitHub에 대 한이 자습서의 샘플 코드](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/mirror-maker)
- [Azure Container Instance에서 실행 되는 azure Event Hubs Kafka MirrorMaker](https://github.com/djrosanova/EventHubsMirrorMaker)

## <a name="next-steps"></a>다음 단계
Kafka에 대 한 Event Hubs에 대해 자세히 알아보려면 다음 문서를 참조 하세요.  

- [이벤트 허브에 Apache Spark 연결](event-hubs-kafka-spark-tutorial.md)
- [이벤트 허브에 Apache Flink 연결](event-hubs-kafka-flink-tutorial.md)
- [Kafka Connect를 이벤트 허브와 통합](event-hubs-kafka-connect-tutorial.md)
- [GitHub에서 더 많은 샘플 탐색](https://github.com/Azure/azure-event-hubs-for-kafka)
- [이벤트 허브에 Akka Streams 연결](event-hubs-kafka-akka-streams-tutorial.md)
- [Azure Event Hubs에 대 한 Apache Kafka 개발자 가이드](apache-kafka-developer-guide.md)