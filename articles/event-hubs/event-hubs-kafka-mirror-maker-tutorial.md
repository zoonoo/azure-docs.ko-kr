---
title: Apache Kafka MirrorMaker 사용 - Azure Event Hubs | Microsoft Docs
description: 이 문서에서는 Kafka MirrorMaker를 사용하여 Azure Event Hubs에서 Kafka 클러스터를 미러링하는 방법에 대한 정보를 제공합니다.
services: event-hubs
documentationcenter: .net
author: ShubhaVijayasarathy
manager: timlt
ms.service: event-hubs
ms.topic: conceptual
ms.custom: seodec18
ms.date: 12/06/2018
ms.author: shvija
ms.openlocfilehash: 3e0339cf4431d3ed36f50b43134803079e30b101
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/01/2020
ms.locfileid: "80521752"
---
# <a name="use-kafka-mirrormaker-with-event-hubs-for-apache-kafka"></a>Apache Kafka용 Event Hubs에서 Kafka MirrorMaker 사용

이 자습서에서는 Kafka MirrorMaker를 사용하여 이벤트 허브에서 Kafka 브로커를 미러리는 방법을 보여 주십니다.

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
최신 클라우드 크기 조정 앱에 대한 주요 고려 사항 중 하나는 서비스를 중단하지 않고 인프라를 업데이트, 개선 및 변경하는 기능입니다. 이 자습서에서는 이벤트 허브와 Kafka MirrorMaker가 이벤트 허브 서비스에서 Kafka 입력 스트림을 "미러링"하여 기존 Kafka 파이프라인을 Azure에 통합하는 방법을 보여 주며 이 자습서를 보여 주며, 이 자습서에서는 기존 Kafka 파이프라인을 Azure에 통합할 수 있습니다. 

Azure Event Hubs Kafka 엔드포인트를 사용하면 Kafka 프로토콜(즉, Kafka 클라이언트)을 사용하여 Azure Event Hubs에 연결할 수 있습니다. Kafka 애플리케이션을 최소한으로 변경하면 Azure Event Hubs에 연결하여 Azure 에코시스템의 이점을 누릴 수 있습니다. 이벤트 허브는 현재 카프카 버전 1.0 이상지원합니다.

## <a name="prerequisites"></a>사전 요구 사항

이 자습서를 완료하려면 다음이 설치되어 있어야 합니다.

* [Apache Kafka용 Event Hubs](event-hubs-for-kafka-ecosystem-overview.md) 문서를 참조하세요. 
* Azure 구독 구독이 없으면 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)을 만듭니다.
* [JDK(Java Development Kit) 1.7+](https://aka.ms/azure-jdks)
    * Ubuntu에서 `apt-get install default-jdk`를 실행하여 JDK를 설치합니다.
    * JAVA_HOME 환경 변수가 반드시 JDK가 설치된 폴더를 지정하도록 설정합니다.
* Maven 이진 아카이브 [다운로드](https://maven.apache.org/download.cgi) 및 [설치](https://maven.apache.org/install.html)
    * Ubuntu에서 `apt-get install maven`을 실행하여 Maven을 실행할 수 있습니다.
* [Git](https://www.git-scm.com/downloads)
    * Ubuntu에서 `sudo apt-get install git`를 실행하여 Git를 실행할 수 있습니다.

## <a name="create-an-event-hubs-namespace"></a>Event Hubs 네임스페이스 만들기

Event Hubs 서비스와 통신하려면 Event Hubs 네임스페이스가 필요합니다. 네임스페이스 및 이벤트 허브를 만드는 방법에 대한 지침은 [이벤트 허브 만들기를](event-hubs-create.md) 참조하십시오. 나중에 사용하기 위해 Event Hubs 연결 문자열을 복사합니다.

## <a name="clone-the-example-project"></a>프로젝트 예제 복제

이제 이벤트 허브 연결 문자열이 있으므로 Kafka 리포지토리에 대한 Azure 이벤트 허브를 복제하고 `mirror-maker` 하위 폴더로 이동합니다.

```shell
git clone https://github.com/Azure/azure-event-hubs-for-kafka.git
cd azure-event-hubs-for-kafka/tutorials/mirror-maker
```

## <a name="set-up-a-kafka-cluster"></a>Kafka 클러스터 설정

[Kafka 빠른 시작 가이드](https://kafka.apache.org/quickstart)를 사용하여 원하는 설정으로 클러스터를 설정하거나 기존 Kafka 클러스터를 사용합니다.

## <a name="configure-kafka-mirrormaker"></a>Kafka MirrorMaker 구성

Kafka MirrorMaker를 사용하면 스트림을 "미러"할 수 있습니다. 원본 및 대상 Kafka 클러스터가 지정되면 MirrorMaker는 원본 클러스터에 보낸 모든 메시지를 원본 및 대상 클러스터 모두에서 받을 수 있도록 합니다. 이 예제에서는 대상 이벤트 허브를 사용하여 소스 Kafka 클러스터를 미러하는 방법을 보여 주십습니다. 이 시나리오는 데이터 흐름을 중단하지 않고 기존 Kafka 파이프라인에서 Event Hubs로 데이터를 보내는 데 사용할 수 있습니다. 

Kafka MirrorMaker에 대한 자세한 내용은 [Kafka 미러링/MirrorMaker 가이드](https://cwiki.apache.org/confluence/pages/viewpage.action?pageId=27846330)를 참조하세요.

카프카 미러메이커를 구성하려면 Kafka 클러스터를 소비자/소스로, 이벤트 허브를 생산자/대상으로 지정합니다.

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

## <a name="run-kafka-mirrormaker"></a>Kafka MirrorMaker 실행

새로 업데이트된 구성 파일을 사용하여 루트 Kafka 디렉터리에서 Kafka MirrorMaker 스크립트를 실행합니다. 구성 파일을 루트 Kafka 디렉터리에 복사하거나 다음 명령에서 해당 경로를 업데이트해야 합니다.

```shell
bin/kafka-mirror-maker.sh --consumer.config source-kafka.config --num.streams 1 --producer.config mirror-eventhub.config --whitelist=".*"
```

이벤트가 이벤트 허브에 도달하고 있는지 확인하려면 [Azure Portal의](https://azure.microsoft.com/features/azure-portal/)침투 통계를 참조하거나 이벤트 허브에 대해 소비자를 실행합니다.

MirrorMaker가 실행되면 카프카 클러스터로 전송된 모든 이벤트는 Kafka 클러스터와 미러된 이벤트 허브모두에서 수신됩니다. MirrorMaker 및 Event Hubs Kafka 엔드포인트를 사용하면, 기존 클러스터를 변경하거나 진행 중인 데이터 흐름을 중단하지 않고도 기존 Kafka 파이프라인을 관리되는 Azure Event Hubs 서비스로 마이그레이션할 수 있습니다.

## <a name="samples"></a>샘플
GitHub에서 다음 샘플을 참조하십시오.

- [GitHub에서 이 자습서에 대한 샘플 코드](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/mirror-maker)
- [Azure 이벤트 허브 Kafka 미러메이커 Azure 컨테이너 인스턴스에서 실행](https://github.com/djrosanova/EventHubsMirrorMaker)

## <a name="next-steps"></a>다음 단계

이 자습서에서는 다음 작업 방법을 알아봅니다.
> [!div class="checklist"]
> * Event Hubs 네임스페이스 만들기
> * 프로젝트 예제 복제
> * Kafka 클러스터 설정
> * Kafka MirrorMaker 구성
> * Kafka MirrorMaker 실행

Event Hubs 및 Kafka용 Event Hubs에 대해 자세히 알아보려면 다음 항목을 참조하세요.  

- [Event Hubs에 대해 알아봅니다](event-hubs-what-is-event-hubs.md).
- [Apache Kafka용 Event Hubs](event-hubs-for-kafka-ecosystem-overview.md)
- [이벤트 허브를 만드는 방법](event-hubs-create.md)
- [Kafka 애플리케이션에서 이벤트 허브로 스트리밍](event-hubs-quickstart-kafka-enabled-event-hubs.md)
- [이벤트 허브에 Apache Spark 연결](event-hubs-kafka-spark-tutorial.md)
- [이벤트 허브에 Apache Flink 연결](event-hubs-kafka-flink-tutorial.md)
- [카프카 커넥트와 이벤트 허브 통합](event-hubs-kafka-connect-tutorial.md)
- [이벤트 허브에 Akka 스트림 연결](event-hubs-kafka-akka-streams-tutorial.md)
- [GitHub에서 더 많은 샘플 탐색](https://github.com/Azure/azure-event-hubs-for-kafka)
