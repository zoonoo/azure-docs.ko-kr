---
title: Event Hubs에 대 한 Apache Kafka 개발자 가이드
description: 이 문서에서는 Azure Event Hubs와 Kafka 응용 프로그램을 통합 하는 방법을 설명 하는 문서에 대 한 링크를 제공 합니다.
ms.date: 06/23/2020
ms.topic: article
ms.openlocfilehash: 757db4faf2a933e366bc818d5cf6aab04d0b08ba
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "90061736"
---
# <a name="apache-kafka-developer-guide-for-azure-event-hubs"></a>Azure Event Hubs에 대 한 Apache Kafka 개발자 가이드
이 문서에서는 Azure Event Hubs와 Apache Kafka 응용 프로그램을 통합 하는 방법을 설명 하는 문서에 대 한 링크를 제공 합니다. 

## <a name="overview"></a>개요
Event Hubs는 기존 Kafka 기반 애플리케이션이 사용자 고유의 Kafka 클러스터 실행에 대한 대안으로 사용할 수 있는 Kafka 엔드포인트을 제공합니다. Event Hubs는 기존 Kafka 응용 프로그램의 다양 한 응용 프로그램과 함께 작동 합니다. 자세한 내용은 [Event Hubs Apache Kafka](event-hubs-for-kafka-ecosystem-overview.md) 를 참조 하세요.

## <a name="quickstarts"></a>빠른 시작
GitHub에서 빠른 시작을 찾을 수 있으며,이 콘텐츠 집합에서 Kafka에 대 한 Event Hubs에 빠르게 진입 하는 데 도움이 됩니다.

### <a name="quickstarts-in-github"></a>GitHub의 빠른 시작
Azure-- **---kafka** 리포지토리의 

| 클라이언트 언어/프레임 워크 | 설명 | 
| ------------------------- | ----------- | 
| [.NET](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/quickstart/dotnet) | <p>이 빠른 시작에서는 .NET Core 2.0를 사용 하 여 c #으로 작성 된 예제 생산자 및 소비자를 사용 하 여 Event Hubs Kafka 끝점을 만들고 연결 하는 방법을 보여 줍니다.</p><p>이 샘플은 [Confluent의 Apache Kafka .net 클라이언트](https://github.com/confluentinc/confluent-kafka-dotnet)를 기반으로 하며, Kafka 용 Event Hubs와 함께 사용 하도록 수정 되었습니다.</p> | 
| [Java](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/quickstart/java) | 이 빠른 시작에서는 Java로 작성 된 예제 생산자 및 소비자를 사용 하 여 Event Hubs Kafka 끝점을 만들고 연결 하는 방법을 보여 줍니다. |
| [Node.js](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/quickstart/node) | <p>이 빠른 시작에서는 노드에 작성 된 예제 생산자와 소비자를 사용 하 여 Kafka 끝점 Event Hubs 만들고 연결 하는 방법을 보여 줍니다.</p><p>이 샘플에서는 [노드 rdkafka](https://github.com/Blizzard/node-rdkafka) 라이브러리를 사용 합니다. </p>| 
| [Python](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/quickstart/python) | <p>이 빠른 시작에서는 python으로 작성 된 예제 생산자와 소비자를 사용 하 여 Kafka 끝점 Event Hubs 만들고 연결 하는 방법을 보여 줍니다.</p><p>이 샘플은 [Confluent의 Python 클라이언트 Apache Kafka](https://github.com/confluentinc/confluent-kafka-python)를 기반으로 하며, Kafka 용 Event Hubs와 함께 사용 하도록 수정 되었습니다.</p>|
| [Go](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/quickstart/go) | <p>이 빠른 시작에서는 Go로 작성 된 예제 생산자와 소비자를 사용 하 여 Kafka 끝점 Event Hubs 만들고 연결 하는 방법을 보여 줍니다.</p><p>이 샘플은 [Confluent의 Apache Kafka Golang client](https://github.com/confluentinc/confluent-kafka-go)를 기반으로 하며, Kafka 용 Event Hubs와 함께 사용 하도록 수정 되었습니다.</p>| 
| [Sarama kafka Go](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/quickstart/go-sarama-client) | 이 빠른 시작에서는 [Sarama kafka 클라이언트](https://github.com/Shopify/sarama) 라이브러리를 사용 하 여 Go로 작성 된 예제 생산자 및 소비자를 사용 하 여 kafka 끝점 Event Hubs을 만들고 연결 하는 방법을 보여 줍니다. |
| [Kafka](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/quickstart/kafka-cli) | 이 빠른 시작에서는 Apache Kafka 배포와 함께 제공 되는 CLI를 사용 하 여 Event Hubs Kafka 끝점을 만들고 연결 하는 방법을 보여 줍니다.| 
| [Kafkacat](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/quickstart/kafkacat) | kafkacat은 librdkafka를 기반으로 하는 비 JVM 명령줄 소비자 및 생산자로, 속도 및 작은 공간으로 널리 사용 됩니다. 이 빠른 시작에는 샘플 구성과 여러 간단한 샘플 kafkacat 명령이 포함 되어 있습니다. | 
 
### <a name="quickstarts-in-docs"></a>문서에서 빠른 시작
Event Hubs로 스트리밍하는 방법에 대 한 단계별 지침을 제공 하는이 콘텐츠 집합에서 [Kafka 프로토콜을 사용 하 여 Event Hubs에서 데이터 스트리밍](event-hubs-quickstart-kafka-enabled-event-hubs.md) 빠른 시작을 참조 하세요. 생산자와 소비자가 애플리케이션 구성을 간단하게 변경하여 Event Hubs로 대화하는 방법을 알아봅니다. 


## <a name="tutorials"></a>자습서 

### <a name="tutorials-in-github"></a>GitHub의 자습서
GitHub에서 다음 자습서를 참조 하세요.

| 자습서 | Description | 
| ------------------------- | ----------- | 
| [Akka streams](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/akka/java) | 이 자습서에서는 프로토콜 클라이언트를 변경 하거나 사용자 고유의 클러스터를 실행 하지 않고 Akka streams 스트림을 Kafka 사용 Event Hubs에 연결 하는 방법을 보여 줍니다. **Java** 및 **Scala** 프로그래밍 언어를 사용 하는 두 가지 별도의 자습서가 있습니다. | 
| [연결](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/connect) | 이 문서에서는 Kafka Connect를 Azure Event Hubs와 통합 하 고 기본 FileStreamSource 및 FileStreamSink 커넥터를 배포 하는 과정을 안내 합니다. 이러한 커넥터는 프로덕션 용도로 사용 되지 않지만 Azure Event Hubs masquerades Kafka broker로 하는 종단 간 Kafka 연결 시나리오를 보여 줍니다.| 
| [Filebeat](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/filebeat) | 이 문서에서는 File\\anaf카 출력을 통해 file\Event Hubs를 통합 하는 과정을 안내 합니다. | 
| [Flink](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/flink) | 이 자습서에서는 프로토콜 클라이언트를 변경 하거나 자체 클러스터를 실행 하지 않고 Kafka 사용 Event Hubs에 Apache Flink를 연결 하는 방법을 보여 줍니다. | 
| [FluentD](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/fluentd) | 이 문서에서는 `out_kafka` Fluentd의 출력 플러그 인을 사용 하 여 Fluentd 및 Event Hubs를 통합 하는 과정을 안내 합니다. |
| [Interop](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/interop) | 이 자습서에서는 서로 다른 프로토콜을 사용 하 여 소비자와 생산자 간에 이벤트를 교환 하는 방법을 보여 줍니다. |
| [Logstash](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/logstash) | 이 자습서에서는 Logstash 태 시/출력 플러그 인을 사용 하 여 Logstash를 Kafka 사용 Event Hubs와 통합 하는 과정을 안내 합니다. | 
| [MirrorMaker](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/mirror-maker) | 이 자습서에서는 Event Hubs 서비스에서 Kafka 입력 스트림을 미러링 하 여 이벤트 허브 및 Kafka MirrorMaker가 기존 Kafka 파이프라인을 Azure에 통합 하는 방법을 보여 줍니다. |
| [NiFi](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/nifi) | 이 자습서에서는 Apache NiFi를 Event Hubs 네임 스페이스에 연결 하는 방법을 보여 줍니다. | 
| [OAuth](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/oauth) | 빠른 시작에서는 Go 및 Java 프로그래밍 언어로 작성 된 예제 생산자와 소비자를 사용 하 여 Event Hubs Kafka 끝점을 만들고 연결 하는 방법을 보여 줍니다. |
| [Confluent의 스키마 레지스트리](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/schema-registry) | 이 자습서에서는 스키마 레지스트리를 통합 하 고 Kafka에 Event Hubs 하는 과정을 안내 합니다. | 
| [Spark](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/spark) | 이 자습서에서는 프로토콜 클라이언트를 변경 하거나 고유한 Kafka 클러스터를 실행 하지 않고 Spark 응용 프로그램을 이벤트 허브에 연결 하는 방법을 보여 줍니다. | 

### <a name="tutorials-in-docs"></a>문서 자습서
또한 자습서:이 콘텐츠 집합에서 [stream analytics를 사용 하 여 Event Hubs 이벤트에 대 한 프로세스 Apache Kafka 처리](event-hubs-kafka-stream-analytics.md) 를 참조 하세요 .이 내용은 데이터를 Event Hubs로 스트리밍하는 방법 및 Azure Stream Analytics로 처리 하는 방법을 보여 줍니다.

## <a name="how-to-guides"></a>방법 가이드
설명서에서 다음 방법 가이드를 참조 하세요.

| 아티클 | 설명 | 
| ------- | ----------- | 
| [이벤트 허브에서 Kafka broker 미러링](event-hubs-kafka-mirror-maker-tutorial.md) | Kafka MirrorMaker를 사용 하 여 이벤트 허브에서 Kafka broker를 미러링 하는 방법을 보여 줍니다. |
| [이벤트 허브에 Apache Spark 연결](event-hubs-kafka-spark-tutorial.md) | 실시간 스트리밍을 위해 Spark 응용 프로그램을 Event Hubs 연결 하는 과정을 안내 합니다. |
| [이벤트 허브에 Apache Flink 연결](event-hubs-kafka-flink-tutorial.md) | 프로토콜 클라이언트를 변경 하거나 사용자 고유의 클러스터를 실행 하지 않고 Apache Flink를 이벤트 허브에 연결 하는 방법을 보여 줍니다. |
| [이벤트 허브와 Apache Kafka 연결 통합 (미리 보기)](event-hubs-kafka-connect-tutorial.md) | Kafka Connect를 이벤트 허브와 통합 하 고 기본 FileStreamSource 및 FileStreamSink 커넥터를 배포 하는 과정을 안내 합니다. |
| [이벤트 허브에 Akka Streams 연결](event-hubs-kafka-akka-streams-tutorial.md) | 프로토콜 클라이언트를 변경 하거나 사용자 고유의 클러스터를 실행 하지 않고 Akka streams 스트림을 이벤트 허브에 연결 하는 방법을 보여 줍니다. |
| [Azure Event Hubs에서 Apache Kafka에 대 한 스프링 부팅 스타터 사용](/azure/developer/java/spring-framework/configure-spring-cloud-stream-binder-java-app-kafka-azure-event-hub) | Azure Event Hubs에서 Apache Kafka를 사용 하도록 스프링 부팅 이니셜라이저를 사용 하 여 만든 Java 기반 스프링 클라우드 스트림 바인더를 구성 하는 방법을 보여 줍니다. |

## <a name="next-steps"></a>다음 단계
빠른 시작 및 자습서 폴더 아래에 있는 GitHub 리포지토리의 [샘플을 검토](https://github.com/Azure/azure-event-hubs-for-kafka) 합니다.

또한 다음 문서를 참조 하세요.

- [Event Hubs에 대 한 Apache Kafka 문제 해결 가이드](apache-kafka-troubleshooting-guide.md)
- [Faq (질문과 대답) Event Hubs Apache Kafka](apache-kafka-frequently-asked-questions.md)
- [Event Hubs에 대 한 Apache Kafka 마이그레이션 가이드](apache-kafka-migration-guide.md)



