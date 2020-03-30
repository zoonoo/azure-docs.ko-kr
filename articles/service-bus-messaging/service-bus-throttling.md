---
title: Azure 서비스 버스 제한 개요 | 마이크로 소프트 문서
description: 서비스 버스 제한 개요 - 표준 및 프리미엄 계층.
services: service-bus-messaging
author: axisc
editor: spelluru
ms.service: service-bus-messaging
ms.topic: article
ms.date: 10/01/2019
ms.author: aschhab
ms.openlocfilehash: f852ad70b2eb97e2b8b3e40d086e98b3836c3592
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77598292"
---
# <a name="throttling-operations-on-azure-service-bus"></a>Azure 서비스 버스에서 제한 작업

클라우드 네이티브 솔루션은 워크로드에 따라 확장할 수 있는 무제한 리소스에 대한 개념을 제공합니다. 이 개념은 온-프레미스 시스템보다 클라우드에서 더 사실이지만 클라우드에는 여전히 한계가 있습니다.

이러한 제한으로 인해 이 문서에서 설명한 대로 표준 및 프리미엄 계층 모두에서 클라이언트 응용 프로그램 요청이 제한될 수 있습니다. 

## <a name="throttling-in-azure-service-bus-standard-tier"></a>Azure 서비스 버스 표준 계층의 제한

Azure 서비스 버스 표준 계층은 종량제 가격 책정 모델을 갖춘 다중 테넌트 설정으로 작동합니다. 여기서 동일한 클러스터의 여러 네임스페이스는 할당된 리소스를 공유합니다. 표준 계층은 낮은 처리량 생산 시스템과 함께 개발자, 테스트 및 QA 환경에 권장되는 선택입니다.

과거에Azure Service Bus는 리소스 사용률에 따라 엄격하게 제한되는 엄격한 제한을 가지고 있었습니다. 그러나 제한 논리를 구체화하고 이러한 리소스를 공유하는 모든 네임스페이스에 예측 가능한 제한 동작을 제공할 수 있습니다.

동일한 리소스를 사용하는 모든 Azure Service Bus Standard 네임스페이스에서 리소스의 공정한 사용 및 배포를 보장하기 위해 제한 논리가 신용 기반으로 수정되었습니다.

> [!NOTE]
> 제한은 Azure Service Bus 또는 클라우드 네이티브 서비스에 대한 새로운 서비스가 ***아닙니다.***
>
> 크레딧 기반 제한은 단순히 다양한 네임스페이스가 다중 테넌트 표준 계층 환경에서 리소스를 공유하는 방식을 구체화하여 리소스를 공유하는 모든 네임스페이스에서 공정한 사용을 가능하게 합니다.

### <a name="what-is-credit-based-throttling"></a>신용 기반 제한이란 무엇입니까?

크레딧 기반 제한은 특정 기간 동안 지정된 네임스페이스에서 수행할 수 있는 작업 수를 제한합니다. 

다음은 신용 기반 제한에 대한 워크플로우입니다. 

  * 각 기간이 시작될 때마다 각 네임스페이스에 특정 수의 크레딧을 제공합니다.
  * 보낸 자 또는 수신자 클라이언트 응용 프로그램에서 수행하는 모든 작업은 이러한 크레딧에 대해 계산되며 사용 가능한 크레딧에서 뺍니다.
  * 크레딧이 소진되면 다음 기간이 시작될 때까지 후속 작업이 제한됩니다.
  * 크레딧은 다음 기간이 시작될 때 보충됩니다.

### <a name="what-are-the-credit-limits"></a>신용 한도는 무엇입니까?

크레딧 한도는 현재 초당 '1000' 크레딧으로 설정됩니다(네임스페이스당).

모든 작업이 동일하게 만들어지는 것은 아닙니다. 각 작업의 신용 비용은 다음과 같습니다. 

| 작업(Operation) | 신용 비용|
|-----------|-----------|
| 데이터 작업(보내기, SendAsync, 수신, 수신Async, 엿보기) |메시지당 1크레딧 |
| 관리 작업(큐, 토픽, 구독, 필터에서 만들기, 읽기, 업데이트, 삭제) | 10 크레딧 |

> [!NOTE]
> 토픽으로 보낼 때 구독에서 사용할 수 있게 되기 전에 각 메시지는 필터에 대해 평가됩니다.
> 각 필터 평가는 신용 한도(예: 필터 평가당 1크레딧)에도 계산됩니다.
>

### <a name="how-will-i-know-that-im-being-throttled"></a>내가 제한되고 있다는 것을 어떻게 알 수 있습니까?

클라이언트 응용 프로그램 요청이 제한되면 아래 서버 응답이 응용 프로그램에서 수신되고 기록됩니다.

```
The request was terminated because the entity is being throttled. Error code: 50009. Please wait 2 seconds and try again.
```

### <a name="how-can-i-avoid-being-throttled"></a>제한되는 것을 어떻게 피할 수 있습니까?

공유 리소스를 사용하면 이러한 리소스를 공유하는 다양한 Service Bus 네임스페이스에서 일종의 공정한 사용을 적용하는 것이 중요합니다. 제한을 사용하면 단일 워크로드의 스파이크로 인해 동일한 리소스의 다른 워크로드가 제한되지 않습니다.

이 문서의 후반부에서 언급했듯이 클라이언트 SDK(및 기타 Azure PaaS 제품)에 기본 재시도 정책이 내장되어 있으므로 제한될 위험이 없습니다. 제한된 요청은 지수 후퇴로 다시 시도되며 크레딧이 보충되면 결국 통과됩니다.

당연히 일부 응용 프로그램은 제한되는 데 민감할 수 있습니다. 이 경우 현재 서비스 [버스 표준 네임스페이스를 Premium 로 마이그레이션하는](service-bus-migrate-standard-premium.md)것이 좋습니다. 

마이그레이션시 Service Bus 네임스페이스에 전용 리소스를 할당하고 워크로드가 급증할 경우 리소스를 적절하게 확장하고 제한될 가능성을 줄일 수 있습니다. 또한 워크로드가 정상 수준으로 감소하면 네임스페이스에 할당된 리소스를 축소할 수 있습니다.

## <a name="throttling-in-azure-service-bus-premium-tier"></a>Azure 서비스 버스 프리미엄 계층의 제한

[Azure Service Bus Premium](service-bus-premium-messaging.md) 계층은 메시징 단위 측면에서 고객의 각 네임스페이스 설정에 전용 리소스를 할당합니다. 이러한 전용 리소스는 예측 가능한 처리량 및 대기 시간을 제공하며 높은 처리량 또는 민감한 프로덕션 등급 시스템에 권장됩니다.

또한 프리미엄 계층을 사용하면 고객이 워크로드에 급증할 때 처리량 용량을 확장할 수 있습니다.

### <a name="how-does-throttling-work-in-service-bus-premium"></a>서비스 버스 프리미엄에서 제한은 어떻게 작동합니까?

Service Bus Premium에 대한 단독 리소스 할당을 사용하면 네임스페이스에 할당된 리소스의 제한에 의해서만 제한이 적용됩니다.

요청 수가 현재 리소스가 서비스할 수 있는 것보다 많으면 요청이 제한됩니다.

### <a name="how-will-i-know-that-im-being-throttled"></a>내가 제한되고 있다는 것을 어떻게 알 수 있습니까?

Azure Service Bus Premium에서 제한을 식별하는 방법에는 여러 가지가 있습니다. 
  * **제한된 요청은** [Azure 모니터 요청 메트릭에](service-bus-metrics-azure-monitor.md#request-metrics) 표시되어 제한된 요청 수를 식별합니다.
  * **CPU 사용량이** 높으면 현재 리소스 할당이 높으며 현재 워크로드가 줄어들지 않으면 요청이 제한될 수 있습니다.
  * **메모리 사용량이** 높으면 현재 리소스 할당이 높으며 현재 워크로드가 줄어들지 않으면 요청이 제한될 수 있습니다.

### <a name="how-can-i-avoid-being-throttled"></a>제한되는 것을 어떻게 피할 수 있습니까?

Service Bus Premium 네임스페이스에는 이미 전용 리소스가 있으므로 워크로드가 급증하는 이벤트(또는 예상)에 네임스페이스에 할당된 메시징 단위 수를 확장하여 제한될 가능성을 줄일 수 있습니다.

위의 메트릭의 변경으로 트리거될 수 있는 [Runbook을](../automation/automation-create-alert-triggered-runbook.md) 만들어 확장/축소를 수행할 수 있습니다.

## <a name="faqs"></a>FAQ

### <a name="how-does-throttling-affect-my-application"></a>제한은 내 응용 프로그램에 어떤 영향을 미칩니까?

요청이 제한되면 리소스가 허용하는 것보다 많은 요청에 직면하기 때문에 서비스가 사용 중임을 의미합니다. 몇 분 후에 동일한 작업을 다시 시도하면 서비스가 현재 워크로드를 통해 작업한 후 요청을 사용할 수 있습니다.

제한은 모든 클라우드 네이티브 서비스의 예상 된 동작이므로 Azure Service Bus SDK 자체에 다시 시도 논리를 빌드했습니다. 기본값은 매번 동일한 요청이 제한되지 않도록 지수 백오프를 사용하여 자동 재시도로 설정됩니다.

기본 재시도 논리는 모든 작업에 적용됩니다.

### <a name="does-throttling-result-in-data-loss"></a>제한하면 데이터 손실이 발생합니까?

Azure Service Bus는 지속성에 최적화되어 있으므로 서비스가 요청의 성공을 인정하기 전에 Service Bus로 전송되는 모든 데이터가 저장소에 커밋되도록 합니다.

서비스가 'ACK'(승인)에 성공하면 서비스 버스가 요청을 성공적으로 처리되었음을 의미합니다. Service Bus가 'NACK'(실패)를 반환하는 경우 Service Bus가 요청을 처리할 수 없고 클라이언트 응용 프로그램이 요청을 다시 시도해야 함을 의미합니다.

그러나 요청이 제한되면 서비스는 리소스 제한으로 인해 현재 요청을 수락하고 처리할 수 없음을 의미합니다. Service Bus가 단순히 요청을 살펴보지 않았기 때문에 어떠한 종류의 데이터 손실도 의미하지는 **않습니다.** 이 경우 Service Bus SDK의 기본 재시도 정책에 의존하면 요청이 결국 처리됩니다.

## <a name="next-steps"></a>다음 단계

Service Bus 메시징을 사용하는 방법에 대한 자세한 내용 및 예제는 다음에 나오는 고급 항목을 참조하세요.

* [Service Bus 메시징 개요](service-bus-messaging-overview.md)
* [빠른 시작: Azure Portal 및 .NET을 사용하여 메시지 보내기 및 받기](service-bus-quickstart-portal.md)
* [자습서: Azure Portal 및 토픽/구독을 사용하여 재고 업데이트](service-bus-tutorial-topics-subscriptions-portal.md)

