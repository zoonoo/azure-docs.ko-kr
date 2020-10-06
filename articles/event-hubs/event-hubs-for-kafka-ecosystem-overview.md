---
title: Apache Kafka 앱에서 이벤트 허브 사용 - Azure Event Hubs | Microsoft Docs
description: 이 문서에서는 Azure Event Hubs에서 지원하는 Apache Kafka에 정보를 제공합니다.
ms.topic: article
ms.date: 09/25/2020
ms.openlocfilehash: 5c49f8f87d8d399cda33a332f7464ed340ae3a0f
ms.sourcegitcommit: 6a4687b86b7aabaeb6aacdfa6c2a1229073254de
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/06/2020
ms.locfileid: "91761501"
---
# <a name="use-azure-event-hubs-from-apache-kafka-applications"></a>Apache Kafka 애플리케이션에서 Azure Event Hubs 사용
Event Hubs는 사용자 고유의 Apache Kafka 클러스터를 실행 하는 대신 대부분의 기존 Apache Kafka 클라이언트 응용 프로그램에서 사용할 수 있는 Apache Kafka® 생산자 및 소비자 Api와 호환 되는 끝점을 제공 합니다. Event Hubs는 1.0 이상 버전에서 Apache Kafka의 생산자 및 소비자 Api 클라이언트를 지원 합니다.

> [!VIDEO https://www.youtube.com/embed/UE1WgB96_fc]

## <a name="what-does-event-hubs-for-kafka-provide"></a>Kafka용 Event Hubs는 무엇을 제공하나요?

Apache Kafka 용 Event Hubs 기능에서는 Apache Kafka server 버전 1.0 이상에 대해 작성 된 Apache Kafka 클라이언트와 호환 되는 프로토콜을 Azure Event Hubs 위에 제공 하 고 Event Hubs 항목에 대해 읽기 및 쓰기를 모두 지원 합니다 .이는 Apache Kafka 항목에 해당 합니다. 

기존 Kafka 설치와 비교 하 여 코드 변경 없이 응용 프로그램에서 Event Hubs Kafka 끝점을 사용 하 고 구성만 수정 합니다. 구성에서 연결 문자열을 업데이트 하 여 Kafka 클러스터를 가리키는 대신 Event hub에 의해 노출 되는 Kafka 끝점을 가리키도록 구성에서 연결 문자열을 업데이트 합니다. 그런 다음, Kafka 프로토콜을 사용하는 애플리케이션에서 Event Hubs로 이벤트 스트리밍을 시작할 수 있습니다. 

개념적으로, Kafka 및 Event Hubs는 매우 유사 합니다. 스트리밍 데이터에 대해 작성 된 분할 된 로그 이며, 클라이언트는 유지 되는 로그의 어느 부분을 제어 합니다. 다음 표에서 Kafka 및 Event Hubs 간 개념을 매핑합니다.

### <a name="kafka-and-event-hub-conceptual-mapping"></a>Kafka 및 Event Hubs의 개념적 매핑

| Kafka 개념 | Event Hubs 클러스터|
| --- | --- |
| 클러스터 | 네임스페이스 |
| 항목 | 이벤트 허브 |
| 파티션 | 파티션|
| 소비자 그룹 | 소비자 그룹 |
| Offset | Offset|

### <a name="key-differences-between-apache-kafka-and-event-hubs"></a>Apache Kafka와 Event Hubs의 주요 차이점

[Apache Kafka](https://kafka.apache.org/) 은 일반적으로 설치 하 고 작동 해야 하는 소프트웨어 이지만, Event Hubs는 완전히 관리 되는 클라우드 네이티브 서비스입니다. 관리 및 모니터링할 서버, 디스크 또는 네트워크는 없으며, 그에 맞게 고려 하거나 구성할 broker가 없습니다. 정규화 된 도메인 이름이 있는 끝점 인 네임 스페이스를 만든 다음 해당 네임 스페이스 내에 Event Hubs (토픽)을 만듭니다. 

Event Hubs 및 네임스페이스에 대한 자세한 내용은 [Event Hubs 기능](event-hubs-features.md#namespace)을 참조하세요. 클라우드 서비스인 Event Hubs은 안정적인 단일 가상 IP 주소를 끝점으로 사용 하므로 클라이언트는 클러스터 내의 브로커 또는 컴퓨터에 대해 알 필요가 없습니다. Event Hubs 동일한 프로토콜을 구현 하는 경우에도이 차이는 모든 파티션에 대 한 모든 Kafka 트래픽이 클러스터의 모든 브로커에 대 한 방화벽 액세스를 요구 하는 것이 아니라이 단일 끝점을 통해 예측 가능 함을 의미 합니다.   

규모 Event Hubs 규모는 구매 하는 처리량 단위 수에 따라 결정 되며, 각 처리량 단위는 초당 1mb로 entitling, 수신의 초당 1000 개의 이벤트를 수신 하 고 송신에서 해당 볼륨의 두 배가 됩니다. [자동](event-hubs-auto-inflate.md) 확장 기능을 사용 하는 경우 처리량 한도에 도달 하면 자동으로 처리량 단위를 확장할 수 Event Hubs. 이 기능은 Apache Kafka 프로토콜 지원과도 작동 합니다.  

### <a name="is-apache-kafka-the-right-solution-for-your-workload"></a>워크 로드에 적합 한 솔루션을 Apache Kafka 하 고 있습니까?

Apache Kafka를 사용 하 여 응용 프로그램을 빌드할 때부터 Azure Event Hubs [Azure Service Bus](../service-bus-messaging/service-bus-messaging-overview.md)및 [Azure Event Grid](../event-grid/overview.md)도 포함 하는 서비스의 일부를 이해 하는 것도 유용 합니다. 

Apache Kafka에 대 한 상용 배포판의 일부 공급자는 모든 메시징 플랫폼 요구 사항에 대 한 일회용 상점 이라고 Apache Kafka 할 수 있지만, 예를 들어 Apache Kafka은를 구현 하지 않습니다. [경쟁 소비자](/azure/architecture/patterns/competing-consumers) 큐 패턴은 일반 오프셋이 아닌 서버 평가 규칙을 기반으로 들어오는 메시지에 대 한 구독자 액세스를 허용 하는 수준에서  [게시-구독](/azure/architecture/patterns/publisher-subscriber) 을 지원 하지 않으며, 메시지에 의해 시작 된 작업의 수명 주기를 추적 하거나 잘못 된 메시지를 배달 못 한 편지 큐로 그룹화 하는 기능을 제공 하지 않습니다 .이는 모두 많은 엔터프라이즈 메시징 시나리오에서 기본 설정입니다.

패턴의 차이점과 해당 서비스에서 가장 잘 적용 되는 패턴을 이해 하려면 [Azure 지침의 비동기 메시징 옵션](/azure/architecture/guide/technology-choices/messaging) 을 검토 하세요. Apache Kafka 사용자는 이제 Kafka으로 현실화 된 통신 경로를 사용 하는 것을 알 수 있습니다 .이는 훨씬 덜 기본적인 복잡성을 제공 하 고 Event Grid 또는 Service Bus를 사용 하 여 보다 강력한 기능을 제공 합니다. 

Apache Kafka 인터페이스에 대 한 Event Hubs를 통해 사용할 수 없는 Apache Kafka의 특정 기능이 필요 하거나 구현 패턴이 [Event Hubs 할당량](event-hubs-quotas.md)을 초과 하는 경우 [Azure HDInsight에서 네이티브 Apache Kafka 클러스터](../hdinsight/kafka/apache-kafka-introduction.md)를 실행할 수도 있습니다.  

## <a name="security-and-authentication"></a>보안 및 인증
Kafka에 대해 Event Hubs에서 이벤트를 게시 하거나 사용할 때마다 클라이언트는 Event Hubs 리소스에 액세스 하려고 시도 합니다. 권한이 부여 된 엔터티를 사용 하 여 리소스에 액세스할 수 있도록 하려고 합니다. 클라이언트에서 Apache Kafka 프로토콜을 사용 하는 경우 SASL 메커니즘을 사용 하 여 인증 및 암호화에 대 한 구성을 설정할 수 있습니다. Kafka에 Event Hubs를 사용 하는 경우 tls 암호화가 필요 합니다 (Event Hubs로 전송 되는 모든 데이터는 TLS로 암호화 됨). 구성 파일에 SASL_SSL 옵션을 지정 하 여이 작업을 수행할 수 있습니다. 

Azure Event Hubs는 보안 리소스에 대 한 액세스 권한을 부여 하는 여러 옵션을 제공 합니다. 

- OAuth 2.0
- 공유 액세스 서명(SAS)

#### <a name="oauth-20"></a>OAuth 2.0
Event Hubs는 **OAuth 2.0** 호환 중앙 집중식 권한 부여 서버를 제공 하는 Azure Active Directory (Azure AD)와 통합 됩니다. Azure AD에서는 RBAC (역할 기반 액세스 제어)를 사용 하 여 클라이언트 id에 대 한 세분화 된 권한을 부여할 수 있습니다. 프로토콜에 대 한 **SASL_SSL** 를 지정 하 고 해당 메커니즘에 대 한  **oauthbearer** 를 지정 하 여이 기능을 kafka 클라이언트와 함께 사용할 수 있습니다. 범위 액세스에 대 한 Azure 역할 및 수준에 대 한 자세한 내용은 [AZURE AD를 사용 하 여 액세스 권한 부여](authorize-access-azure-active-directory.md)를 참조 하세요.

```xml
bootstrap.servers=NAMESPACENAME.servicebus.windows.net:9093
security.protocol=SASL_SSL
sasl.mechanism=OAUTHBEARER
sasl.jaas.config=org.apache.kafka.common.security.oauthbearer.OAuthBearerLoginModule required;
sasl.login.callback.handler.class=CustomAuthenticateCallbackHandler;
```

#### <a name="shared-access-signature-sas"></a>공유 액세스 서명(SAS)
또한 Event Hubs는 Kafka 리소스의 Event Hubs에 대 한 위임 된 액세스에 대 한 **SAS (공유 액세스 서명)** 를 제공 합니다. OAuth 2.0 토큰 기반 메커니즘을 사용 하 여 액세스 권한을 부여 하면 우수한 보안과 SAS를 통한 사용 편의성을 제공 합니다. 기본 제공 역할은 사용자가 유지 관리 하 고 관리 해야 하는 ACL 기반 권한 부여에 대 한 필요성을 없앨 수도 있습니다. 프로토콜에 대 한 **SASL_SSL** 를 지정 하 고 메커니즘에 **일반** 을 지정 하 여 kafka 클라이언트에서이 기능을 사용할 수 있습니다. 

```xml
bootstrap.servers=NAMESPACENAME.servicebus.windows.net:9093
security.protocol=SASL_SSL
sasl.mechanism=PLAIN
sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="{YOUR.EVENTHUBS.CONNECTION.STRING}";
```

> [!NOTE]
> Kafka 클라이언트에서 SAS 인증을 사용 하는 경우 SAS 키가 다시 생성 되 면 설정 된 연결의 연결이 끊깁니다. 

#### <a name="samples"></a>샘플 
이벤트 허브를 만들고 SAS 또는 OAuth를 사용 하 여 액세스 하는 방법에 대 한 단계별 지침이 포함 된 **자습서** 는 [빠른 시작: kafka 프로토콜을 사용 하 여 Event Hubs 데이터 스트리밍](event-hubs-quickstart-kafka-enabled-event-hubs.md)을 참조 하세요.

Kafka에 Event Hubs OAuth를 사용 하는 방법을 보여 주는 **샘플** 은 [GitHub의 샘플](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/oauth)을 참조 하세요.

## <a name="other-event-hubs-features"></a>기타 Event Hubs 기능 

Apache Kafka 기능 Event Hubs는 Azure Event Hubs, 보완 HTTP 및 AMQP에서 동시에 사용할 수 있는 세 가지 프로토콜 중 하나입니다. 이러한 프로토콜을 사용 하 여 작성 하 고 다른 프로토콜을 사용 하 여 읽을 수 있습니다. 그러면 현재 Apache Kafka 생산자는 Apache Kafka를 통해 계속 게시할 수 있지만 판독기는 Azure Stream Analytics 또는 Azure Functions와 같은 Event Hubs ' AMQP 인터페이스와의 네이티브 통합을 활용할 수 있습니다. Reversely Azure Event Hubs를 AMQP 라우팅 네트워크에 대상 끝점으로 쉽게 통합할 수 있으며, 아직 Apache Kafka 통합을 통해 데이터를 읽을 수 있습니다.  

또한 [캡처](event-hubs-capture-overview.md)와 같은 Event Hubs 기능을 사용 하 여 Azure Blob Storage 및 Azure Data Lake Storage를 통해 매우 비용 효율적인 장기 보관을 가능 하 게 하 고 [지역 재해 복구](event-hubs-geo-dr.md) 도 kafka 기능에 Event Hubs를 사용할 수 있습니다.

## <a name="apache-kafka-feature-differences"></a>Apache Kafka 기능 차이점 

Apache Kafka에 대 한 Event Hubs의 목표는 Apache Kafka API로 잠긴 응용 프로그램에 Azure Event Hub의 기능에 대 한 액세스를 제공 하는 것 이며, 그렇지 않은 경우에는 Apache Kafka 클러스터에서 지원 해야 합니다. 

[위에서](#is-apache-kafka-the-right-solution-for-your-workload)설명한 것 처럼 Azure Messaging는 수많은 메시징 시나리오에 대 한 풍부 하 고 강력한 검사를 제공 하 고, 다음 기능은 현재 Apache Kafka API에 대 한 Event Hubs 지원을 통해 지원 되지 않지만 원하는 기능을 사용할 수 있는 위치와 방법을 확인 합니다.

### <a name="transactions"></a>트랜잭션

[Azure Service Bus](../service-bus-messaging/service-bus-transactions.md) 에는 메시지 처리에서 발생 하는 아웃 바운드 메시지를 트랜잭션 일관성 보호를 통해 여러 대상 엔터티로 보내는 동시에 메시지 및 세션을 수신 하 고 정착 수 있는 강력한 트랜잭션 지원이 있습니다. 기능 집합은 시퀀스에서 각 메시지를 정확히 한 번만 처리 하도록 허용할 뿐만 아니라 Apache Kafka의 경우와 같이 다른 소비자가 실수로 동일한 메시지를 다시 처리 하는 위험을 방지 합니다. Service Bus은 트랜잭션 메시지 작업에 권장 되는 서비스입니다.

### <a name="compression"></a>압축

Apache Kafka의 클라이언트 쪽 [압축](https://cwiki.apache.org/confluence/display/KAFKA/Compression) 기능은 여러 메시지의 일괄 처리를 생산자 쪽의 단일 메시지로 압축 하 고 소비자 쪽에서 일괄 처리의 압축을 풉니다. Apache Kafka broker는 일괄 처리를 특수 메시지로 처리 합니다.

이 기능은 기본적으로 일괄 처리로 전송 되는 메시지를 사용 하 여 broker 및 모든 프로토콜을 통해 개별적으로 검색할 수 있도록 하는 Azure Event Hubs ' 멀티 프로토콜 모델 '과는 다릅니다. 

이벤트 허브 이벤트의 페이로드는 바이트 스트림으로, 선택한 알고리즘을 사용 하 여 콘텐츠를 압축할 수 있습니다. Apache Avro 인코딩 형식은 기본적으로 압축을 지원 합니다.

### <a name="log-compaction"></a>로그 압축

Apache Kafka 로그 압축은 파티션에서 각 키의 마지막 레코드를 제외한 모든 레코드를 제거 하는 기능입니다 .이 기능을 사용 하면 Apache Kafka 항목을 키-값 저장소로 변환 하 여 마지막에 추가한 값이 이전 값을 재정의 합니다. 자주 업데이트 되는 경우에도 키-값 저장소 패턴은 [Azure Cosmos DB](../cosmos-db/introduction.md)같은 데이터베이스 서비스에서 훨씬 더 잘 지원 됩니다.

로그 압축 기능은 Kafka Connect 및 Kafka Stream 클라이언트 프레임 워크에서 사용 됩니다.

### <a name="kafka-streams"></a>Kafka 스트림

Kafka 스트림은 Apache Kafka 오픈 소스 프로젝트의 일부인 stream analytics 용 클라이언트 라이브러리 이지만 Apache Kafka event stream broker와는 별개입니다. 

Azure Event Hubs 고객이 Kafka Stream 지원을 요청 하는 가장 일반적인 이유는 Confluent의 "Kafka" 제품에 관심이 있기 때문입니다. "ksqlDB"는 "ksqlDB" 지원을 사용 하거나 제공 하는 공급 업체가 "Confluent 제품이 나 서비스와 경합 하는 타사 또는 기타 유사한 온라인 서비스를 제공 하는 공급 업체"를 사용 하지 않도록 [허가](https://github.com/confluentinc/ksql/blob/master/LICENSE) 된 독점적인 공유 원본 프로젝트입니다. 실제로 ksqlDB를 사용 하는 경우 Ksqldb을 직접 운영 하거나 Confluent의 클라우드 제품을 사용 해야 합니다. 라이선스 사용 조건은 라이선스에서 제외 된 용도에 대 한 서비스를 제공 하는 Azure 고객 에게도 영향을 줄 수 있습니다.

독립 실행형 및 ksqlDB를 사용 하지 않는 경우 Ksqldb 스트림은 대부분의 대체 프레임 워크 및 서비스 보다 많은 기능을 제공 하며, 대부분은 기본 제공 되는 스트리밍 SQL 인터페이스를 포함 하 고 있으며 현재 Azure Event Hubs와 통합 됩니다.

- [Azure Stream Analytics](../stream-analytics/stream-analytics-introduction.md)
- [Azure Synapse Analytics (Event Hubs 캡처를 통해)](../event-grid/event-grid-event-hubs-integration.md)
- [Azure Databricks](https://docs.microsoft.com/azure/databricks/scenarios/databricks-stream-from-eventhubs)
- [Apache Samza](https://samza.apache.org/learn/documentation/latest/connectors/eventhubs)
- [Apache Storm](event-hubs-storm-getstarted-receive.md)
- [Apache Spark](event-hubs-kafka-spark-tutorial.md)
- [Apache Flink](event-hubs-kafka-flink-tutorial.md)
- [Akka streams 스트림](event-hubs-kafka-akka-streams-tutorial.md)

일반적으로 나열 된 서비스 및 프레임 워크는 어댑터를 통해 다양 한 원본 집합에서 직접 이벤트 스트림 및 참조 데이터를 가져올 수 있습니다. Kafka 스트림은 Apache Kafka 에서만 데이터를 가져올 수 있으므로 분석 프로젝트가 Apache Kafka으로 잠깁니다. 다른 원본의 데이터를 사용 하려면 먼저 Kafka Connect 프레임 워크를 사용 하 여 데이터를 Apache Kafka으로 가져와야 합니다.

Azure에서 Kafka Stream 프레임 워크를 사용 해야 하는 경우 [HDInsight에서 Apache Kafka](../hdinsight/kafka/apache-kafka-introduction.md) 이 옵션을 제공 합니다. HDInsight의 Apache Kafka은 Apache Kafka의 모든 구성 측면을 완벽 하 게 제어 하는 동시에 장애/업데이트 도메인 배치에서 네트워크 격리를 통해 모니터링 통합으로 Azure 플랫폼의 다양 한 측면을 완벽 하 게 통합 합니다. 

## <a name="next-steps"></a>다음 단계
이 문서는 Kafka용 Event Hubs에 대한 소개를 제공합니다. 자세한 내용은 [Azure Event Hubs용 Apache Kafka 개발자 가이드](apache-kafka-developer-guide.md)를 참조하세요.
