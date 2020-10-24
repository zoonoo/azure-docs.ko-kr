---
title: 질문과 대답 - Azure Event Hubs | Microsoft Docs
description: 이 문서에서는 Azure Event Hubs에 대한 FAQ(질문과 대답) 목록 및 그에 대한 답변을 제공합니다.
ms.topic: article
ms.date: 10/23/2020
ms.openlocfilehash: c95016064ecc9bbfc091138863c8215feeec50b4
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/24/2020
ms.locfileid: "92518027"
---
# <a name="event-hubs-frequently-asked-questions"></a>Event Hubs 질문과 대답

## <a name="general"></a>일반

### <a name="what-is-an-event-hubs-namespace"></a>Event Hubs 네임스페이스란?
네임스페이스는 Event Hub/Kafka 토픽에 대한 범위 지정 컨테이너입니다. 고유한 [FQDN](https://en.wikipedia.org/wiki/Fully_qualified_domain_name)을 제공합니다. 네임스페이스는 여러 Event Hub/Kafka 토픽을 저장할 수 있는 애플리케이션 컨테이너로 사용됩니다. 

### <a name="when-do-i-create-a-new-namespace-vs-use-an-existing-namespace"></a>새 네임스페이스를 만드는 경우와 기존 네임스페이스를 사용하는 경우는 어떻게 다른가요?
용량 할당[[TU(처리량 단위)](#throughput-units)]은 네임스페이스 수준에서 청구됩니다. 네임스페이스는 지역에도 연결되어 있습니다.

다음 시나리오 중 하나에서 기존 네임스페이스를 사용하는 대신 새 네임스페이스를 만들 수 있습니다. 

- 새 지역과 연결된 이벤트 허브가 필요합니다.
- 다른 구독과 연결된 이벤트 허브가 필요합니다.
- 고유한 용량 할당이 있는 이벤트 허브가 필요합니다. 즉, 추가된 이벤트 허브가 포함된 네임스페이스의 용량 요구는 40TU 임계값을 초과하게 되므로 전용 클러스터를 사용하려고 하지 않을 것입니다.  

### <a name="what-is-the-difference-between-event-hubs-basic-and-standard-tiers"></a>Event Hubs 기본 및 표준 계층 간의 차이는 무엇입니까?

Azure Event Hubs의 표준 계층은 기본 계층에서 제공할 수 있는 것보다 많은 기능을 제공합니다. 다음 기능이 표준에 포함됩니다.

* 더 길어진 이벤트 보존 기간
* 추가 조정된 연결, 포함된 수보다 많은 경우 초과분 요금 있음
* 단일 [소비자 그룹](event-hubs-features.md#consumer-groups) 초과
* [캡처](event-hubs-capture-overview.md)
* [Kafka 통합](event-hubs-for-kafka-ecosystem-overview.md)

전용 Event Hubs를 비롯한 가격 책정 계층에 대한 자세한 내용은 [Event Hubs 가격 책정 정보](https://azure.microsoft.com/pricing/details/event-hubs/)를 참조하세요.

### <a name="where-is-azure-event-hubs-available"></a>어디에서 Azure Event Hubs를 사용할 수 있나요?

Azure Event Hubs는 지원되는 모든 Azure 지역에서 사용할 수 있습니다. 목록은 [Azure 지역](https://azure.microsoft.com/regions/)를 참조하세요.  

### <a name="can-i-use-a-single-advanced-message-queuing-protocol-amqp-connection-to-send-and-receive-from-multiple-event-hubs"></a>단일 고급 메시지 큐 프로토콜 (AMQP) 연결을 사용 하 여 여러 event hubs에서 보내고 받을 수 있나요?

예, 모든 이벤트 허브가 동일한 네임스페이스에 있는 경우 가능합니다.

### <a name="what-is-the-maximum-retention-period-for-events"></a>이벤트의 최대 보존 기간은 어떻게 되나요?

Event Hubs 표준 계층은 현재 최대 7일의 보존 기간을 지원합니다. Event Hubs는 영구 데이터 스토리지로 사용되지 않습니다. 24 시간 보다 큰 보존 기간은 이벤트 스트림을 동일한 시스템으로 재생 하는 데 편리한 시나리오를 위한 것입니다. 예를 들어 기존 데이터에서 새로운 기계 학습 모델을 학습 하거나 확인 합니다. 7일을 초과하여 메시지를 보존해야 하는 경우 Event Hubs에서 [Event Hubs 캡처](event-hubs-capture-overview.md)를 사용하도록 설정하면 Event Hubs의 데이터를 선택한 Storage 계정 또는 Azure Data Lake 서비스 계정으로 가져옵니다. 캡처를 사용하도록 설정하면 구매한 처리량 단위에 따라 요금이 부과됩니다.

스토리지 계정에서 캡처된 데이터의 보존 기간을 구성할 수 있습니다. Azure Storage의 **수명 주기 관리** 기능은 범용 v2 및 Blob 스토리지 계정에 대한 다양한 규칙 기반 정책을 제공합니다. 정책을 사용하여 데이터를 적절한 액세스 계층으로 전환하거나 데이터의 수명 주기 후에 만료합니다. 자세한 내용은 [Azure Blob 스토리지 수명 주기 관리](../storage/blobs/storage-lifecycle-management-concepts.md)를 참조하세요. 

### <a name="how-do-i-monitor-my-event-hubs"></a>내 Event Hubs를 모니터링하려면 어떻게 할까요?
Event Hubs는 [Azure Monitor](../azure-monitor/overview.md)에 리소스 상태를 제공하는 자세한 메트릭을 내보냅니다. 또한 네임스페이스 수준뿐만 아니라 엔터티 수준에서도 Event Hubs 서비스의 전반적인 상태를 평가할 수 있습니다. [Azure Event Hubs](event-hubs-metrics-azure-monitor.md)에 제공되는 모니터링에 대해 알아보세요.

### <a name="where-does-azure-event-hubs-store-customer-data"></a><a name="in-region-data-residency"></a>Azure Event Hubs 고객 데이터를 저장 하는 위치
Azure Event Hubs는 고객 데이터를 저장 합니다. 이 데이터는 단일 지역에 Event Hubs 의해 자동으로 저장 되므로이 서비스는 [보안 센터](https://azuredatacentermap.azurewebsites.net/)에 지정 된 데이터를 포함 하 여 지역 데이터 상주 요구 사항을 자동으로 충족 합니다.

### <a name="what-ports-do-i-need-to-open-on-the-firewall"></a>방화벽에서 열어야 하는 포트는 어느 것인가요? 
Azure Service Bus에서 다음 프로토콜을 사용하여 메시지를 주고받을 수 있습니다.

- AMQP
- HTTP
- Apache Kafka

이러한 프로토콜을 사용하여 Azure Event Hubs와 통신하기 위해 열어야 하는 아웃바운드 포트는 다음 표를 참조하세요. 

| 프로토콜 | 포트 | 세부 정보 | 
| -------- | ----- | ------- | 
| AMQP | 5671 및 5672 | [AMQP 프로토콜 가이드](../service-bus-messaging/service-bus-amqp-protocol-guide.md)를 참조하세요. | 
| HTTP, HTTPS | 80, 443 |  |
| Kafka | 9093 | [Kafka 애플리케이션에서 Event Hubs 사용](event-hubs-for-kafka-ecosystem-overview.md)을 참조하세요.

### <a name="what-ip-addresses-do-i-need-to-allow"></a>허용 해야 하는 IP 주소는 무엇 인가요?
연결에 대해 허용 된 목록에 추가할 올바른 IP 주소를 찾으려면 다음 단계를 수행 합니다.

1. 명령 프롬프트에서 다음 명령을 실행합니다. 

    ```
    nslookup <YourNamespaceName>.servicebus.windows.net
    ```
2. `Non-authoritative answer`에서 반환된 IP 주소를 적어 둡니다. 

네임 스페이스에 **영역 중복성** 을 사용 하는 경우 몇 가지 추가 단계를 수행 해야 합니다. 

1. 먼저 네임스페이스에서 nslookup을 실행합니다.

    ```
    nslookup <yournamespace>.servicebus.windows.net
    ```
2. **신뢰할 수 없는 응답** 섹션에서 다음 형식 중 하나로 표시되는 이름을 적어 둡니다. 

    ```
    <name>-s1.cloudapp.net
    <name>-s2.cloudapp.net
    <name>-s3.cloudapp.net
    ```
3. 접미사 s1, s2 및 s3를 포함하는 각 이름에 대해 nslookup을 실행하여 세 개의 가용성 영역에서 실행되는 세 인스턴스의 IP 주소를 모두 가져옵니다. 

    > [!NOTE]
    > 명령에서 반환 된 IP 주소는 `nslookup` 고정 ip 주소가 아닙니다. 그러나 기본 배포가 삭제 되거나 다른 클러스터로 이동 될 때까지 일정 하 게 유지 됩니다.

### <a name="where-can-i-find-client-ip-sending-or-receiving-messages-to-my-namespace"></a>네임 스페이스에 메시지를 보내거나 받는 클라이언트 IP를 어디에서 찾을 수 있나요?
먼저 네임 스페이스에서 [IP 필터링](event-hubs-ip-filtering.md) 을 사용 하도록 설정 합니다. 

그런 다음 [진단 로그 사용](event-hubs-diagnostic-logs.md#enable-diagnostic-logs)의 지침에 따라 [가상 네트워크 연결 이벤트 Event Hubs](event-hubs-diagnostic-logs.md#event-hubs-virtual-network-connection-event-schema) 에 대 한 진단 로그를 사용 하도록 설정 합니다. 연결이 거부 된 IP 주소가 표시 됩니다.

```json
{
    "SubscriptionId": "0000000-0000-0000-0000-000000000000",
    "NamespaceName": "namespace-name",
    "IPAddress": "1.2.3.4",
    "Action": "Deny Connection",
    "Reason": "IPAddress doesn't belong to a subnet with Service Endpoint enabled.",
    "Count": "65",
    "ResourceId": "/subscriptions/0000000-0000-0000-0000-000000000000/resourcegroups/testrg/providers/microsoft.eventhub/namespaces/namespace-name",
    "Category": "EventHubVNetConnectionEvent"
}
```

## <a name="apache-kafka-integration"></a>Apache Kafka 통합

### <a name="how-do-i-integrate-my-existing-kafka-application-with-event-hubs"></a>기존 Kafka 애플리케이션을 Event Hubs와 통합하려면 어떻게 할까요?
Event Hubs는 기존 Apache Kafka 기반 애플리케이션에서 사용할 수 있는 Kafka 엔트포인트를 제공합니다. PaaS Kafka 환경을 갖추려면 구성만 변경하면 됩니다. 이 구성은 사용자 고유의 Kafka 클러스터를 실행하기 위한 대안을 제공합니다. Event Hubs는 Apache Kafka 1.0 및 최신 클라이언트 버전을 지원하고, 기존 Kafka 애플리케이션, 도구 및 프레임워크와 작동합니다. 자세한 내용은 [Kafka용 Event Hubs 리포지토리](https://github.com/Azure/azure-event-hubs-for-kafka)를 참조하세요.

### <a name="what-configuration-changes-need-to-be-done-for-my-existing-application-to-talk-to-event-hubs"></a>기존 애플리케이션에서 Event Hubs와 통신하려면 어떤 구성을 변경해야 하나요?
이벤트 허브에 연결하려면 Kafka 클라이언트 구성을 업데이트해야 합니다. 이렇게 하려면 Event Hubs 네임스페이스를 만들고 [연결 문자열](event-hubs-get-connection-string.md)을 가져오면 됩니다. Event Hubs FQDN과 포트에서 9093을 가리키도록 bootstrap.servers를 변경합니다. 아래와 같이 올바른 인증으로 Kafka 클라이언트를 Event Hubs 엔드포인트(가져온 연결 문자열)에 연결하도록 sasl.jaas.config를 업데이트합니다.

```properties
bootstrap.servers={YOUR.EVENTHUBS.FQDN}:9093
request.timeout.ms=60000
security.protocol=SASL_SSL
sasl.mechanism=PLAIN
sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="{YOUR.EVENTHUBS.CONNECTION.STRING}";
```

예제:

```properties
bootstrap.servers=dummynamespace.servicebus.windows.net:9093
request.timeout.ms=60000
security.protocol=SASL_SSL
sasl.mechanism=PLAIN
sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="Endpoint=sb://dummynamespace.servicebus.windows.net/;SharedAccessKeyName=DummyAccessKeyName;SharedAccessKey=XXXXXXXXXXXXXXXXXXXXX";
```
참고: sasl.jaas.config가 프레임워크에서 지원되는 구성이 아닌 경우 SASL 사용자 이름과 암호를 설정하는 데 사용되는 구성을 찾아 대신 사용하세요. 사용자 이름은 $ConnectionString으로 설정하고, 암호는 Event Hubs 연결 문자열로 설정합니다.

### <a name="what-is-the-messageevent-size-for-event-hubs"></a>Event Hubs의 메시지/이벤트 크기는 어떻게 되나요?
Event Hubs에 허용되는 최대 메시지 크기는 1MB입니다.

## <a name="throughput-units"></a>처리량 단위

### <a name="what-are-event-hubs-throughput-units"></a>Event Hubs 처리량 단위는 무엇입니까?
Event Hubs의 처리량은 Event Hubs를 통해 들어오고 나가는 1KB 이벤트의 데이터 양(메가바이트 단위) 또는 수(1,000개 단위)를 정의합니다. 이 처리량은 TU(처리량 단위)로 측정됩니다. Event Hubs 서비스를 사용하려면 먼저 TU를 구입해야 합니다. 포털 또는 Event Hubs Resource Manager 템플릿을 사용하여 Event Hubs TU를 명시적으로 선택할 수 있습니다. 


### <a name="do-throughput-units-apply-to-all-event-hubs-in-a-namespace"></a>처리량 단위는 네임스페이스의 모든 이벤트 허브에 적용되나요?
예, TU(처리량 단위)는 Event Hubs 네임스페이스의 모든 이벤트 허브에 적용됩니다. 즉, 네임스페이스 수준에서 TU를 구입하고, 해당 네임스페이스에 속한 이벤트 허브 간에 공유됩니다. 각 TU에서 네임스페이스에 제공하는 기능은 다음과 같습니다.

- 초당 최대 1MB의 수신 이벤트(이벤트 허브로 전송된 이벤트), 단 초당 1,000개 이하의 수신 이벤트, 관리 작업 또는 제어 API 호출
- 초당 최대 2MB의 송신 이벤트(이벤트 허브에서 사용된 이벤트), 단 4096개를 초과하지 않는 송신 이벤트.
- 최대 84GB의 이벤트 스토리지(기본 24시간 보존 기간에 충분).

### <a name="how-are-throughput-units-billed"></a>처리량 단위는 어떻게 청구되나요?
TU(처리량 단위)는 시간 단위로 청구됩니다. 청구는 지정된 시간 동안 선택한 최대 단위 수를 기준으로 합니다. 

### <a name="how-can-i-optimize-the-usage-on-my-throughput-units"></a>내 처리량 단위에서 사용량을 최적화하려면 어떻게 해야 하나요?
1TU(처리량 단위)와 같이 낮은 단위에서 시작하고 [자동 팽창](event-hubs-auto-inflate.md)을 설정하면 됩니다. 자동 팽창 기능을 사용하면 트래픽/페이로드가 증가함에 따라 TU를 늘릴 수 있습니다. 또한 TU 수에 대한 상한값도 설정할 수 있습니다.

### <a name="how-does-auto-inflate-feature-of-event-hubs-work"></a>Event Hubs의 자동 팽창 기능은 어떻게 작동하나요?
자동 팽창 기능을 사용하여 TU(처리량 단위)를 강화할 수 있습니다. 즉, 낮은 TU를 구입하고 수신이 증가함에 따라 자동 팽창을 통해 TU를 강화할 수 있습니다. 비용 효율적인 옵션과 관리할 TU 수에 대한 완벽한 제어를 제공합니다. 이 기능은 **강화 전용** 기능이며, TU 수를 업데이트하여 규모 축소를 완전히 제어할 수 있습니다. 

예를 들어 2TU와 같이 낮은 TU(처리량 단위)로 시작하는 것이 좋습니다. 트래픽이 15TU까지 증가할 수 있다고 예상하는 경우 네임스페이스에서 자동 팽창 기능을 설정하고 최대 제한을 15TU로 설정합니다. 이제 트래픽이 증가함에 따라 TU를 자동으로 늘릴 수 있습니다.

### <a name="is-there-a-cost-associated-when-i-turn-on-the-auto-inflate-feature"></a>자동 팽창 기능을 설정하면 관련 비용이 발생하나요?
이 기능과 관련된 **비용은 없습니다**. 

### <a name="how-are-throughput-limits-enforced"></a>처리량 제한은 어떻게 적용되나요?
전체 **수신** 처리량 또는 네임스페이스 내 모든 이벤트 허브에서의 전체 수신 이벤트 비율이 집계 처리량 단위 허용 한도를 초과하면, 발신자가 제한되고 수신 할당량을 초과했음을 나타내는 오류가 표시됩니다.

전체 **수신** 처리량 또는 네임스페이스의 모든 Event Hubs에서의 전체 수신 이벤트 비율이 집계 처리량 단위 허용 한도를 초과하면, 받는 사람이 제한되지만 제한 오류는 생성되지 않습니다. 

수신 및 송신 할당량은 별도로 적용되므로 발신자가 이벤트 사용 속도를 저하시키지 않으며 수신자가 이벤트 허브로의 이벤트 전송을 차단하지도 않습니다.

### <a name="is-there-a-limit-on-the-number-of-throughput-units-tus-that-can-be-reservedselected"></a>예약/선택할 수 있는 TU(처리량 단위) 수에 제한이 있나요?
다중 테넌트 제안에서 처리량 단위는 최대 40TU까지 늘릴 수 있습니다(포털에서 최대 20TU를 선택하고, 지원 티켓을 제출하여 동일한 네임스페이스에서 40TU로 늘릴 수 있음). 40TU를 초과하는 경우 Event Hubs는 **Event Hubs Dedicated 클러스터**라고 하는 리소스/용량 기반 모델을 제공합니다. Dedicated 클러스터는 CU(용량 단위)로 판매됩니다.

## <a name="dedicated-clusters"></a>Dedicated 클러스터

### <a name="what-are-event-hubs-dedicated-clusters"></a>Event Hubs Dedicated 클러스터란?
Event Hubs Dedicated 클러스터는 요구 사항이 가장 까다로운 고객을 위한 단일 테넌트 배포를 제공합니다. 이 제안은 처리량 단위로 구속되지 않는 용량 기반 클러스터를 구축합니다. 즉, 클러스터를 사용하여 클러스터의 메모리 사용량 및 CPU에 따라 데이터를 수집하고 스트림할 수 있습니다. 자세한 내용은 [Event Hubs Dedicated 클러스터](event-hubs-dedicated-overview.md)를 참조하세요.

### <a name="how-do-i-create-an-event-hubs-dedicated-cluster"></a>Event Hubs Dedicated 클러스터를 만들려면 어떻게 할까요?
Event Hubs 전용 클러스터를 설정 하는 방법에 대 한 단계별 지침과 자세한 내용은 [빠른 시작: Azure Portal를 사용 하 여 전용 Event Hubs 클러스터 만들기](event-hubs-dedicated-cluster-create-portal.md)를 참조 하세요. 


[!INCLUDE [event-hubs-dedicated-clusters-faq](../../includes/event-hubs-dedicated-clusters-faq.md)]


## <a name="best-practices"></a>모범 사례

### <a name="how-many-partitions-do-i-need"></a>얼마나 많은 파티션이 필요한가요?
파티션 수는 생성 시 지정 되며 1에서 32 사이 여야 합니다. 파티션 수는 변경할 수 없으므로 파티션 수를 설정할 때 장기적인 규모를 고려해야 합니다. 파티션은 애플리케이션을 사용하는 데 필요한 다운스트림 병렬 처리와 관련된 데이터 구성 메커니즘입니다. Event Hub의 파티션 수는 예상되는 동시 판독기의 수와 직접적으로 관련이 있습니다. 파티션에 대한 자세한 내용은 [파티션](event-hubs-features.md#partitions)을 참조하세요.

만들 때 가장 높은 값(32)으로 설정할 수 있습니다. 두 개 이상의 파티션이 있는 경우 전송자가 32개 중에서 단일 파티션으로만 전송하도록 구성하여 나머지 31개 파티션이 중복되도록 하지 않는 한, 두 개 이상의 파티션이 순서를 유지하지 않으면서 여러 파티션으로 이벤트가 전송됩니다. 전자의 경우에는 모든 32개 파티션에서 이벤트를 읽어야 합니다. 후자의 경우에는 이벤트 프로세서 호스트에서 수행해야 하는 추가 구성에 대한 명확한 추가 비용이 발생하지 않습니다.

Event Hubs는 소비자 그룹당 단일 파티션 판독기를 허용하도록 설계되었습니다. 대부분의 경우 기본 설정인 4개의 파티션만으로도 충분합니다. 이벤트 처리의 크기를 조정하려는 경우 추가 파티션을 추가할지 고려할 수 있습니다. 파티션에 대한 특정 처리량 제한은 없지만 네임스페이스에서 집계 처리량은 처리량 단위 수로 제한됩니다. 네임스페이스에서 처리량 단위 수를 늘리면 추가 파티션을 통해 동시 판독기가 고유한 최대 처리량을 달성하도록 할 수 있습니다.

그러나 애플리케이션에 특정 파티션에 대한 선호도가 있는 모델인 경우 파티션 수를 늘리게 되면 사용자에게 혜택이 돌아오지 않습니다. 자세한 내용은 [가용성 및 일관성](event-hubs-availability-and-consistency.md)을 참조하세요.

## <a name="pricing"></a>가격 책정

### <a name="where-can-i-find-more-pricing-information"></a>추가 가격 책정 정보는 어디에서 찾을 수 있나요?

Event Hubs 가격 책정에 대한 전체 내용은 [Event Hubs 가격 책정 정보](https://azure.microsoft.com/pricing/details/event-hubs/)를 참조하세요.

### <a name="is-there-a-charge-for-retaining-event-hubs-events-for-more-than-24-hours"></a>24시간 이상 Event Hubs 이벤트를 유지 하려면 비용이 청구됩니까?

Event Hubs 표준 계층은 최대 7일 동안 24시간을 초과하는 메시지 보존 기간을 허용합니다. File Storage에 대한 자세한 내용은 파일 서비스 REST API를 참조하세요. 처리량 단위가 최대 수신 허용 한도까지 사용되었더라도 각 처리량 단위의 스토리지 허용 한도는 24 시간(기본값) 동안의 보존 기간에 대한 모든 스토리지 비용이 포함됩니다.

### <a name="how-is-the-event-hubs-storage-size-calculated-and-charged"></a>Event Hubs 스토리지 크기가 계산 및 청구되는 방법

모든 이벤트 허브의 디스크 스토리지 구조 또는 이벤트 헤더의 내부 오버헤드를 포함하여, 저장된 모든 이벤트의 전체 크기가 하루 종일 측정됩니다. 하루가 끝날 때 최대 스토리지 크기가 계산됩니다. 일일 스토리지 허용 한도는 하루 동안 선택된 최소 처리량 단위 수를 기준으로 계산됩니다(각 처리량 단위는 84GB의 허용 한도를 제공함) . 총 크기가 계산된 일일 스토리지 허용 한도를 초과하면, 초과 스토리지는 Azure Blob Storage 가격을 사용하여 청구됩니다( **로컬 중복 스토리지** 속도로).

### <a name="how-are-event-hubs-ingress-events-calculated"></a>Event Hubs 수신 이벤트 계산하는 방법

이벤트 허브에 전송된 각 이벤트는 청구 가능한 메시지로 계산됩니다. *수신 이벤트* 는 64KB보다 작은 데이터 단위로 정의됩니다. 크기가 64KB 이하인 모든 이벤트는 하나의 청구 가능한 이벤트로 간주됩니다. 이벤트가 64KB보다 큰 경우 64KB의 배수로 이벤트 크기에 따라 청구 가능한 이벤트 수가 계산됩니다. 예를 들어 이벤트 허브에 전송된 8KB 이벤트는 하나의 이벤트로 청구되지만, 이벤트 허브에 전송된 96KB 메시지는 두 개의 이벤트로 청구됩니다.

이벤트 허브에서 사용된 이벤트와 검사점 등의 제어 호출 및 관리 작업은 청구 가능한 수신 이벤트로 계산되지 않고 처리량 단위 허용 한도까지 누적됩니다.

### <a name="do-brokered-connection-charges-apply-to-event-hubs"></a>조정된 연결 요금이 Event Hubs에 적용됩니까?

AMQP 프로토콜을 사용하는 경우에 연결 요금이 적용됩니다. 시스템 또는 디바이스 수에 관계 없이 HTTP를 사용하여 이벤트를 보내는 데에는 연결 요금이 부과되지 않습니다. AMQP를 사용하려는 경우(예: 보다 효율적인 이벤트 스트리밍을 달성하거나 IoT 명령 및 제어 시나리오에서 양방향 통신 설정), 각 서비스 계층에 포함된 연결 수에 대한 자세한 내용은 [Event Hubs 가격 책정 정보](https://azure.microsoft.com/pricing/details/event-hubs/) 페이지를 참조하세요.

### <a name="how-is-event-hubs-capture-billed"></a>Event Hubs 캡처는 어떻게 청구되나요?

캡처는 네임스페이스에 캡처 옵션이 설정된 이벤트 허브가 하나라도 있을 경우 사용됩니다. Event Hubs 캡처는 구매한 처리량 단위를 기준으로 시간당 청구됩니다. 처리량 단위 수가 증가하거나 감소하면 Event Hubs 캡처 청구 시 전체 증가 시간에 이러한 변경 내용이 반영됩니다. Event Hubs 캡처 청구에 대한 자세한 내용은 [Event Hubs 가격 정보](https://azure.microsoft.com/pricing/details/event-hubs/)를 참조하세요.

### <a name="do-i-get-billed-for-the-storage-account-i-select-for-event-hubs-capture"></a>Event Hubs 캡처에 대해 선택한 스토리지 계정에 요금이 청구되나요?

이벤트 허브에서 설정된 경우 캡처는 사용자가 제공한 스토리지 계정을 사용합니다. 사용자의 스토리지 계정이므로 이 구성에 대한 모든 변경은 사용자의 Azure 구독에 청구됩니다.

## <a name="quotas"></a>할당량

### <a name="are-there-any-quotas-associated-with-event-hubs"></a>Event Hubs와 관련된 할당량이 있나요?

Event Hubs 할당량의 목록은 [할당량](event-hubs-quotas.md)을 참조하세요.

## <a name="troubleshooting"></a>문제 해결

### <a name="why-am-i-not-able-to-create-a-namespace-after-deleting-it-from-another-subscription"></a>네임스페이스를 다른 구독에서 삭제한 후 만들 수 없는 이유는 무엇인가요? 
구독에서 네임스페이스를 삭제한 후에 다른 구독에서 동일한 이름으로 다시 만들려면 4시간 정도 기다려야 합니다. 그렇지 않으면 다음 오류 메시지가 표시될 수 있습니다. `Namespace already exists` 

### <a name="what-are-some-of-the-exceptions-generated-by-event-hubs-and-their-suggested-actions"></a>Event Hubs에 의해 생성된 일부 예외 및 해당 제안된 작업은 무엇인가요?

가능한 Event Hubs 예외의 목록은 [예외 개요](event-hubs-messaging-exceptions.md)를 참조하세요.

### <a name="diagnostic-logs"></a>진단 로그

Event Hubs는 두 가지 유형의 [진단 로그](event-hubs-diagnostic-logs.md)인 캡처 오류 로그와 작업 로그를 지원합니다. 두 로그 모두 json으로 표시되며 Azure Portal을 통해 설정할 수 있습니다.

### <a name="support-and-sla"></a>지원 및 SLA

Event Hubs에 대한 기술 지원은 [Azure Service Bus에 대한 Microsoft Q&A 질문 페이지](/answers/topics/azure-service-bus.html)를 통해 사용할 수 있습니다. 청구 및 구독 관리 지원은 무료로 제공됩니다.

SLA에 대한 자세한 내용에 대해 알아보려면 [서비스 수준 계약](https://azure.microsoft.com/support/legal/sla/) 페이지를 참조하세요.

## <a name="azure-stack-hub"></a>Azure Stack Hub

### <a name="how-can-i-target-a-specific-version-of-azure-storage-sdk-when-using-azure-blob-storage-as-a-checkpoint-store"></a>Azure Blob Storage를 검사점 저장소로 사용할 때 특정 버전의 Azure Storage SDK를 대상으로 지정 하려면 어떻게 해야 하나요?
Azure Stack Hub에서 이 코드를 실행하는 경우 특정 Storage API 버전을 대상으로 하지 않는 한 런타임 오류가 발생합니다. 이는 Event Hubs SDK가 Azure Stack Hub 플랫폼에서는 사용할 수 없는 Azure에서 사용 가능한 최신 Azure Storage API를 사용하기 때문입니다. Azure Stack 허브는 Azure에서 일반적으로 사용할 수 있는 것과 다른 버전의 Storage Blob SDK를 지원할 수 있습니다. Azure Blog Storage를 검사점 저장소로 사용하는 경우 [Azure Stack Hub 빌드에 대해 지원되는 Azure Storage API 버전](/azure-stack/user/azure-stack-acs-differences?#api-version)을 확인하고 코드에서 해당 버전을 대상으로 지정합니다. 

예를 들어 Azure Stack 허브 버전 2005에서 실행 중인 경우 저장소 서비스에 사용할 수 있는 가장 높은 버전은 2019-02-02입니다. 기본적으로 Event Hubs SDK 클라이언트 라이브러리는 Azure에서 사용 가능한 가장 높은 버전을 사용합니다(SDK 릴리스 당시 2019-07-07). 이 경우 이 섹션의 다음 단계 외에도 스토리지 서비스 API 버전 2019-02-02를 대상으로 하는 코드를 추가해야 합니다. 특정 Storage API 버전을 대상으로 지정 하는 방법에 대 한 예제는 c #, Java, Python 및 JavaScript/TypeScript에 대 한 다음 샘플을 참조 하세요.  

코드에서 특정 Storage API 버전을 대상으로 지정 하는 방법에 대 한 예제는 GitHub의 다음 샘플을 참조 하세요. 

- [.NET](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/eventhub/Azure.Messaging.EventHubs.Processor/samples/Sample10_RunningWithDifferentStorageVersion.cs)
- [Java](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/eventhubs/azure-messaging-eventhubs-checkpointstore-blob/src/samples/java/com/azure/messaging/eventhubs/checkpointstore/blob/EventProcessorWithCustomStorageVersion.java)
- Python- [동기](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/eventhub/azure-eventhub-checkpointstoreblob/samples/receive_events_using_checkpoint_store_storage_api_version.py), [비동기](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/eventhub/azure-eventhub-checkpointstoreblob-aio/samples/receive_events_using_checkpoint_store_storage_api_version_async.py)
- [JavaScript](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/eventhub/eventhubs-checkpointstore-blob/samples/javascript/receiveEventsWithApiSpecificStorage.js) 및 [TypeScript](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/eventhub/eventhubs-checkpointstore-blob/samples/typescript/src/receiveEventsWithApiSpecificStorage.ts)

## <a name="next-steps"></a>다음 단계

Event Hubs에 대한 자세한 내용은 다음 링크를 참조하세요.

* [Event Hubs 개요](./event-hubs-about.md)
* [이벤트 허브 만들기](event-hubs-create.md)
* [Event Hubs 자동 확장](event-hubs-auto-inflate.md)
