---
title: 이벤트 허브에 대한 아파치 카프카 개발자 가이드
description: 이 문서에서는 Kafka 응용 프로그램을 Azure 이벤트 허브와 통합하는 방법을 설명하는 문서에 대한 링크를 제공합니다.
services: event-hubs
author: spelluru
manager: ''
ms.author: spelluru
ms.date: 03/31/2020
ms.topic: article
ms.service: event-hubs
ms.openlocfilehash: 8a72d20101aacaf59b4be5c4a231b132237113f3
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/03/2020
ms.locfileid: "80633919"
---
# <a name="apache-kafka-developer-guide-for-azure-event-hubs"></a>Azure 이벤트 허브에 대 한 아파치 카프카 개발자 가이드
이 문서에서는 아파치 카프카 응용 프로그램을 Azure 이벤트 허브와 통합하는 방법을 설명하는 문서에 대한 링크를 제공합니다. 

## <a name="overview"></a>개요
Event Hubs는 기존 Kafka 기반 애플리케이션이 사용자 고유의 Kafka 클러스터 실행에 대한 대안으로 사용할 수 있는 Kafka 엔드포인트을 제공합니다. Event Hubs는 Apache Kafka 프로토콜 1.0 이상을 지원하고 MirrorMaker를 포함하여 기존 Kafka 애플리케이션과 작동합니다. 자세한 내용은 [아파치 카프카의 이벤트 허브를](event-hubs-for-kafka-ecosystem-overview.md) 참조하십시오.

## <a name="quickstarts"></a>빠른 시작
GitHub 및 이 콘텐츠 세트에서 빠른 시작을 찾을 수 있으며, 이 도움말에서는 Kafka의 이벤트 허브를 빠르게 강화할 수 있습니다.

### <a name="quickstarts-in-github"></a>GitHub의 빠른 시작
**azure-이벤트 허브-for-kafka** 리포지토리에서 다음 빠른 시작을 참조하십시오. 

| 클라이언트 언어/프레임워크 | 설명 | 
| ------------------------- | ----------- | 
| [.NET](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/quickstart/dotnet) | <p>이 빠른 시작은 .NET Core 2.0을 사용하여 C#로 작성된 예제 생산자 및 소비자를 사용하여 이벤트 허브 Kafka 끝점을 만들고 연결하는 방법을 보여 줄 것입니다.</p><p>이 샘플은 [Confluent의 아파치 카프카 .NET 클라이언트를](https://github.com/confluentinc/confluent-kafka-dotnet)기반으로 하며, 카프카용 이벤트 허브와 함께 사용하도록 수정되었습니다.</p> | 
| [Java](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/quickstart/java) | 이 빠른 시작은 Java로 작성된 예제 생산자 및 소비자를 사용하여 이벤트 허브 Kafka 끝점을 만들고 연결하는 방법을 보여 줄 것입니다. |
| [Node.JS](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/quickstart/node) | <p>이 빠른 시작은 노드에 기록된 예제 생산자 및 소비자를 사용하여 이벤트 허브 Kafka 끝점을 만들고 연결하는 방법을 보여 줄 것입니다.</p><p>이 샘플에서는 [노드 rdkafka](https://github.com/Blizzard/node-rdkafka) 라이브러리를 사용합니다. </p>| 
| [Python](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/quickstart/python) | <p>이 빠른 시작은 파이썬으로 작성된 예제 생산자 및 소비자를 사용하여 이벤트 허브 Kafka 끝점을 만들고 연결하는 방법을 보여줍니다.</p><p>이 샘플은 [Confluent의 아파치 카프카 파이썬 클라이언트를](https://github.com/confluentinc/confluent-kafka-python)기반으로 하며, 카프카용 이벤트 허브와 함께 사용하도록 수정되었습니다.</p>|
| [이동](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/quickstart/go) | <p>이 빠른 시작은 Go에 기록된 예제 생산자 및 소비자를 사용하여 이벤트 허브 Kafka 끝점을 만들고 연결하는 방법을 보여 줄 것입니다.</p><p>이 샘플은 [Confluent의 아파치 카프카 골랑 클라이언트를](https://github.com/confluentinc/confluent-kafka-go)기반으로 하며, 카프카의 이벤트 허브와 함께 사용하도록 수정되었습니다.</p>| 
| [사라마 카프카 고](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/quickstart/go-sarama-client) | 이 빠른 시작은 Sarama Kafka 클라이언트 라이브러리를 사용하여 Go에 기록된 예제 생산자 및 소비자를 사용하여 이벤트 허브 [Kafka](https://github.com/Shopify/sarama) 끝점을 만들고 연결하는 방법을 보여 줄 것입니다. |
| [Kafka](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/quickstart/kafka-cli) | 이 빠른 시작은 아파치 카프카 배포판과 함께 제공되는 CLI를 사용하여 이벤트 허브 카프카 엔드포인트를 만들고 연결하는 방법을 보여줍니다.| 
| [카프카캣](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/quickstart/kafkacat) | kafkacat는 비 JVM 명령 라인 소비자와 librdkafka에 따라 생산자, 그것의 속도와 작은 풋 프린트로 인해 인기. 이 빠른 시작에는 샘플 구성과 몇 가지 간단한 샘플 kafkacat 명령이 포함되어 있습니다. | 
 
### <a name="quickstarts-in-docs"></a>DOCS의 빠른 시작
빠른 시작: 이벤트 허브로 스트리밍하는 방법에 대한 단계별 지침을 제공하는 이 콘텐츠 집합의 [Kafka 프로토콜을 사용하여 이벤트](event-hubs-quickstart-kafka-enabled-event-hubs.md) 허브를 사용하여 데이터 스트리밍을 확인합니다. 응용 프로그램의 구성 변경만으로 생산자와 소비자를 사용하여 이벤트 허브와 대화하는 방법을 알아봅니다. 


## <a name="tutorials"></a>자습서 

### <a name="tutorials-in-github"></a>GitHub 자습서
GitHub에 대한 다음 자습서를 참조하십시오.

| 자습서 | 설명 | 
| ------------------------- | ----------- | 
| [아카 (것)에](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/akka/java) | 이 자습서에서는 프로토콜 클라이언트를 변경하거나 자체 클러스터를 실행하지 않고 Akka Streams를 Kafka 지원 이벤트 허브에 연결하는 방법을 보여 줍니다. **Java** 및 **Scala** 프로그래밍 언어를 사용하는 두 개의 별도 자습서가 있습니다. | 
| [연결](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/connect) | 이 문서에서는 Kafka Connect를 Azure 이벤트 허브에 통합하고 기본 FileStreamSource 및 FileStreamSink 커넥터를 배포하는 데 대해 안내합니다. 이러한 커넥터는 프로덕션 용으로 사용되지 는 않지만 Azure 이벤트 허브가 Kafka 브로커로 가장하는 종단 간 카프카 연결 시나리오를 보여 줍니다.| 
| [파일 비트](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/filebeat) | 이 문서에서는 Filebeat의 Kafka 출력을 통해 Filebeat 및 이벤트 허브통합을 안내합니다. | 
| [플링크 ()플링크 (것](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/flink) | 이 자습서는 프로토콜 클라이언트를 변경하거나 자체 클러스터를 실행하지 않고 아파치 Flink를 카프카 지원 이벤트 허브에 연결하는 방법을 보여 줍니다. | 
| [유창한](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/fluentd) | 이 문서에서는 Fluentd의 `out_kafka` 출력 플러그인을 사용하여 Fluentd 및 이벤트 허브를 통합하는 데 대해 안내합니다. |
| [Interop](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/interop) | 이 자습서에서는 서로 다른 프로토콜을 사용하여 소비자와 생산자 간에 이벤트를 교환하는 방법을 보여 주며 있습니다. |
| [Logstash](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/logstash) | 이 튜토리얼은 Logstash 카프카 입력 / 출력 플러그인을 사용하여 카프카 지원 이벤트 허브와 Logstash통합을 통해 당신을 안내합니다. | 
| [미러메이커](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/mirror-maker) | 이 자습서에서는 이벤트 허브 및 Kafka MirrorMaker가 이벤트 허브 서비스에서 Kafka 입력 스트림을 미러링하여 기존 Kafka 파이프라인을 Azure에 통합하는 방법을 보여 주며 있습니다. |
| [NiFi](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/nifi) | 이 자습서는 아파치 NiFi를 이벤트 허브 네임스페이스에 연결하는 방법을 보여 준다. | 
| [Oauth](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/oauth) | Quickstarts는 Go 및 Java 프로그래밍 언어로 작성된 예제 생산자 및 소비자를 사용하여 이벤트 허브 Kafka 엔드포인트를 만들고 연결하는 방법을 보여 준다. |
| [컨리시의 스키마 레지스트리](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/schema-registry) | 이 자습서에서는 Kafka에 대한 스키마 레지스트리 및 이벤트 허브 통합을 안내합니다. | 
| [Spark](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/spark) | 이 자습서에서 프로토콜 클라이언트를 변경하거나 고유한 Kafka 클러스터를 실행하지 않고 Spark 응용 프로그램을 이벤트 허브에 연결하는 방법을 보여 줍니다. | 

### <a name="tutorials-in-docs"></a>DOCS 자습서
또한 자습서를 참조: 이 콘텐츠 집합에서 [스트림 분석을 사용 하 여 이벤트 허브에 대 한 아파치 Kafka 프로세스,](event-hubs-kafka-stream-analytics.md) 이벤트 허브로 데이터를 스트리밍 하 고 Azure 스트림 분석으로 처리 하는 방법을 보여 합니다.

## <a name="how-to-guides"></a>방법 가이드
설명서의 다음 방법 가이드를 참조하십시오.

| 아티클 | 설명 | 
| ------- | ----------- | 
| [이벤트 허브에서 Kafka broker 미러링](event-hubs-kafka-mirror-maker-tutorial.md) | 카프카 미러메이커를 사용하여 이벤트 허브에서 카프카 브로커를 미러리는 방법을 보여줍니다. |
| [이벤트 허브에 Apache Spark 연결](event-hubs-kafka-spark-tutorial.md) | Spark 응용 프로그램을 이벤트 허브에 연결하여 실시간 스트리밍을 진행합니다. |
| [이벤트 허브에 Apache Flink 연결](event-hubs-kafka-flink-tutorial.md) | 프로토콜 클라이언트를 변경하거나 자체 클러스터를 실행하지 않고 Apache Flink를 이벤트 허브에 연결하는 방법을 보여 줍니다. |
| [아파치 카프카 연결과 이벤트 허브 통합(미리 보기)](event-hubs-kafka-connect-tutorial.md) | Kafka Connect를 이벤트 허브와 통합하고 기본 FileStreamSource 및 FileStreamSink 커넥터를 배포하는 데 대해 안내합니다. |
| [이벤트 허브에 Akka 스트림 연결](event-hubs-kafka-akka-streams-tutorial.md) | 프로토콜 클라이언트를 변경하거나 자체 클러스터를 실행하지 않고 Akka Streams를 이벤트 허브에 연결하는 방법을 보여 줍니다. |
| [Azure 이벤트 허브와 아파치 카프카에 대 한 스프링 부팅 스타터를 사용 하 여](/azure/java/spring-framework/configure-spring-cloud-stream-binder-java-app-kafka-azure-event-hub) | Azure 이벤트 허브와 아파치 카프카를 사용 하 여 스프링 부팅 초기화로 만든 자바 기반 스프링 클라우드 스트림 바인더를 구성 하는 방법을 보여 줍니다. |

## <a name="next-steps"></a>다음 단계
빠른 시작 및 자습서 폴더에서 GitHub 리포지토리 [azure-event-hubs-for-kafka에서](https://github.com/Azure/azure-event-hubs-for-kafka) 샘플을 검토합니다.

