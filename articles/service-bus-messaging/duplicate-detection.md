---
title: Azure Service Bus 중복 메시지 검색 | Microsoft Docs
description: 이 문서에서는 Azure Service Bus 메시지에서 중복 메시지를 검색하는 방법을 설명합니다. 중복 메시지는 무시하고 삭제할 수 있습니다.
ms.topic: article
ms.date: 01/13/2021
ms.openlocfilehash: 527c2dea34b02733907372b6e75a40a5ef5fc289
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101711928"
---
# <a name="duplicate-detection"></a>중복 검색

애플리케이션이 메시지를 보낸 직후 치명적인 오류로 인해 실패하고 다시 시작된 애플리케이션 인스턴스가 이전 메시지 전송이 발생하지 않았다고 잘못 판단하면, 후속 보내기로 인해 동일한 메시지가 시스템에 두 번 표시됩니다.

또한 클라이언트 또는 네트워크 수준의 오류가 잠시 전에 발생하고, 보낸 메시지가 큐에 커밋되어, 승인이 클라이언트에 성공적으로 반환되지 않을 수도 있습니다. 이런 시나리오에서 클라이언트는 보내기 작업의 결과에 의문을 갖게 됩니다.

중복 검색은 보낸 사람이 동일한 메시지를 다시 보낼 수 있도록 하고 큐 또는 토픽에서 중복된 복사본을 삭제하여 이러한 상황에 대한 의문을 해소합니다.

> [!NOTE]
> Service Bus의 기본 계층에서는 중복 검색을 지원하지 않습니다. 표준 및 프리미엄 계층은 중복 검색을 지원합니다. 이러한 계층 간의 차이점은 [Service Bus 가격 책정](https://azure.microsoft.com/pricing/details/service-bus/)을 참조하세요.

## <a name="how-it-works"></a>작동 원리 
중복 검색을 사용하면 지정된 기간 동안 큐 또는 토픽으로 전송된 모든 메시지의 애플리케이션 제어 *MessageId* 를 추적하는 데 도움이 됩니다. 해당 기간 중에 기록된 *MessageId* 로 새 메시지가 전송되면 해당 메시지는 수락된 것으로 보고되고(보내기 작업 성공), 새로 보낸 메시지는 즉시 무시되고 삭제됩니다. *MessageId* 를 제외한 메시지의 다른 부분은 고려되지 않습니다.

식별자의 애플리케이션 제어는 통해 애플리케이션이 오류 발생시 예측 가능하게 재구성할 수 있는 비즈니스 프로세스 컨텍스트에 *MessageId* 를 연결하도록 허용하기 때문에 필수적입니다.

애플리케이션 컨텍스트를 처리하는 과정에서 여러 메시지가 전송되는 비즈니스 프로세스의 경우 *MessageId* 는 구매 주문 번호와 같은 애플리케이션 수준 컨텍스트 식별자와 메시지 제목을 결합한 것(예: **12345.2017/payment**)이 될 수 있습니다.

*MessageId* 는 항상 GUID일 수 있지만 식별자를 비즈니스 프로세스에 고정하면 예측 가능한 반복성을 얻을 수 있으며 이것은 중복 검색 기능을 효과적으로 활용하는 데 바람직합니다.

> [!IMPORTANT]
>- **분할** 을 **사용** 하는 경우 고유성 확인에 `MessageId+PartitionKey`가 사용됩니다. 세션을 사용하는 경우 분할 키와 세션 ID가 동일해야 합니다. 
>- **분할** 을 **사용하지 않는** 경우(기본값) 고유성 확인에 `MessageId`만 사용됩니다.
>- SessionId, PartitionKey 및 MessageId에 대한 자세한 내용은 [분할 키 사용](service-bus-partitioning.md#use-of-partition-keys)을 참조하세요.
>- [프리미어 계층](service-bus-premium-messaging.md)은 분할을 지원하지 않기 때문에, 중복 검색을 위해 분할 키에 의존하기보다는 애플리케이션에서 고유한 메시지 ID를 사용하는 것이 좋습니다. 


## <a name="enable-duplicate-detection"></a>중복 검색 사용

포털에서 **중복 검색 사용** 확인란을 사용하여 엔터티를 만드는 동안 이 기능은 켜져 있으며 기본적으로 해제되어 있습니다. 새 토픽을 만드는 설정은 동일합니다.

![중복 검색 사용 옵션이 선택되고 빨간색으로 윤곽선이 그려진 큐 만들기 대화 상자의 스크린샷.][1]

> [!IMPORTANT]
> 큐를 만든 후에 중복 검색을 사용하도록/사용하지 않도록 설정할 수 없습니다. 큐를 만들 때에만 수행할 수 있습니다. 

프로그래밍 방식으로 전체 프레임 워크 .NET API에서 [QueueDescription.requiresDuplicateDetection](/dotnet/api/microsoft.servicebus.messaging.queuedescription.requiresduplicatedetection#Microsoft_ServiceBus_Messaging_QueueDescription_RequiresDuplicateDetection) 속성으로 플래그를 설정합니다. Azure Resource Manager API를 사용하면 값이 [queueProperties.requiresDuplicateDetection](/azure/templates/microsoft.servicebus/namespaces/queues#property-values) 속성으로 설정됩니다.

중복 검색 시간 기록은 큐 및 토픽의 경우 기본값이 10분이며 최솟값은 20분, 최댓값은 7일입니다. 이 설정은 Azure Portal의 큐 및 토픽 속성 창에서 변경할 수 있습니다.

![속성 설정이 강조 표시되고 중복 검색 기록 옵션에 빨간색으로 윤곽선이 그려진 Service Bus 기능의 스크린샷.][2]

프로그래밍 방식으로 전체 .NET Framework API에서 [QueueDescription.DuplicateDetectionHistoryTimeWindow](/dotnet/api/microsoft.servicebus.messaging.queuedescription.duplicatedetectionhistorytimewindow#Microsoft_ServiceBus_Messaging_QueueDescription_DuplicateDetectionHistoryTimeWindow) 속성을 사용하여 메시지 ID가 유지되는 동안 중복 검색 기간의 크기를 구성할 수 있습니다. Azure Resource Manager API를 사용하면 값이 [queueProperties.duplicateDetectionHistoryTimeWindow](/azure/templates/microsoft.servicebus/namespaces/queues#property-values) 속성으로 설정됩니다.

기록된 모든 메시지 ID는 새로 제출된 메시지 식별자와 일치해야 하기 때문에 중복 검색 사용과 기간의 크기는 큐(및 토픽) 처리량에 직접적인 영향을 줍니다.

기간을 작게 하면 유지하고 일치시켜야 할 메시지 ID 수가 줄어들기 때문에 처리량에 미치는 영향이 작아집니다. 중복 검색이 필요한 처리량이 많은 엔터티의 경우 기간을 가능한 작게 유지해야 합니다.

## <a name="next-steps"></a>다음 단계

Service Bus 메시징에 대해 자세히 알아보려면 다음 항목을 참조하세요.

* [Service Bus 큐, 토픽 및 구독](service-bus-queues-topics-subscriptions.md)
* [Service Bus 큐 시작](service-bus-dotnet-get-started-with-queues.md)
* [Service Bus 토픽 및 구독을 사용하는 방법](service-bus-dotnet-how-to-use-topics-subscriptions.md)

클라이언트 코드가 이전과 동일한 *MessageId* 를 사용하여 메시지를 다시 전송할 수 없는 시나리오에서는 안전하게 다시 처리할 수 있는 메시지를 설계하는 것이 중요합니다. [멱동성(idempotence)에 관한 이 블로그 게시물](https://particular.net/blog/what-does-idempotent-mean)에서는 이를 수행하는 방법에 대한 다양한 기술을 설명합니다.

[1]: ./media/duplicate-detection/create-queue.png
[2]: ./media/duplicate-detection/queue-prop.png
