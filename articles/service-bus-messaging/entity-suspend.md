---
title: Azure Service Bus-메시징 엔터티 일시 중단
description: 이 문서에서는 Azure Service Bus 메시지 엔터티 (큐, 토픽 및 구독)를 일시적으로 일시 중단 하 고 다시 활성화 하는 방법을 설명 합니다.
ms.topic: article
ms.date: 09/29/2020
ms.openlocfilehash: ea1acab3d0a86b0064f8b3eef7bfd1496bd17041
ms.sourcegitcommit: 6ab718e1be2767db2605eeebe974ee9e2c07022b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/12/2020
ms.locfileid: "94543054"
---
# <a name="suspend-and-reactivate-messaging-entities-disable"></a>메시징 엔터티 일시 중단 및 다시 활성화(사용 안 함)

큐, 토픽 및 구독을 일시적으로 중단할 수 있습니다. 일시 중단을 수행하면 엔터티가 사용 안 함 상태가 되며 모든 메시지는 스토리지에 유지됩니다. 그러나 메시지를 제거 또는 추가할 수 없으며 해당 프로토콜 작업은 오류를 생성합니다.

긴급 관리를 위해 엔터티를 일시 중단 하는 것이 좋습니다. 예를 들어 결함이 있는 수신자가 큐에서 메시지를 가져오고, 처리에 실패 하 고, 아직 메시지를 잘못 완료 하 고 제거 하는 경우입니다. 이 경우 코드를 수정 하 고 배포할 때까지 수신 큐를 사용 하지 않도록 설정할 수 있습니다. 

사용자 또는 시스템에 의해 일시 중단 또는 다시 활성화가 수행될 수 있습니다. 시스템은 구독 지출 한도에 도달 하는 등의 억음 관리 이유로 인해 엔터티만 일시 중단 합니다. 시스템에서 사용하지 않도록 설정된 엔터티는 사용자가 다시 활성화할 수 없지만 일시 중단의 원인에 해결되면 복원됩니다.

## <a name="queue-status"></a>큐 상태 
**큐** 에 대해 설정할 수 있는 상태는 다음과 같습니다.

-   **Active** : 큐가 활성 상태입니다. 큐에서 메시지를 보내고 받을 수 있습니다. 
-   **Disabled** : 큐가 일시 중단되었습니다. **Senddisabled** 및 **ReceiveDisabled** 를 모두 설정 하는 것과 같습니다. 
-   **Senddisabled** : 큐에 메시지를 보낼 수 없지만 메시지에서 메시지를 받을 수 있습니다. 큐에 메시지를 전송 하려고 하면 예외가 발생 합니다. 
-   **ReceiveDisabled** : 큐에 메시지를 보낼 수 있지만 메시지에서 메시지를 받을 수는 없습니다. 큐에 메시지를 수신 하려고 하면 예외가 발생 합니다.


### <a name="change-the-queue-status-in-the-azure-portal"></a>Azure Portal에서 큐 상태를 변경 합니다. 

1. Azure Portal에서 Service Bus 네임 스페이스로 이동 합니다. 
1. 상태를 변경 하려는 큐를 선택 합니다. 가운데의 아래쪽 창에 큐가 표시 됩니다. 
1. **Service Bus 큐** 페이지에서 큐의 현재 상태를 하이퍼링크로 표시 합니다. 왼쪽 메뉴에서 개요를 선택 하지 않은 경우 해당 **개요** 를 선택 하 여 큐의 상태를 확인 합니다. 큐의 현재 상태를 선택 하 여 변경 합니다. 

    :::image type="content" source="./media/entity-suspend/select-state.png" alt-text="큐의 상태를 선택 합니다.":::
4. 큐의 새 상태를 선택 하 고 **확인** 을 선택 합니다. 

    :::image type="content" source="./media/entity-suspend/entity-state-change.png" alt-text="큐의 상태를 설정 합니다.":::
    
.NET SDK의 Service Bus [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) api를 사용 하거나 Azure CLI 또는 Azure PowerShell를 통해 Azure Resource Manager 템플릿을 사용 하 여 보내기 및 받기 작업을 사용 하지 않도록 설정할 수도 있습니다.

### <a name="change-the-queue-status-using-azure-powershell"></a>Azure PowerShell를 사용 하 여 큐 상태 변경
큐를 사용하지 않도록 설정하는 PowerShell 명령은 다음 예제에 나와 있습니다. 재활성화 명령은 동일하게 `Status`를 **Active** 로 설정합니다.

```powershell
$q = Get-AzServiceBusQueue -ResourceGroup mygrp -NamespaceName myns -QueueName myqueue

$q.Status = "Disabled"

Set-AzServiceBusQueue -ResourceGroup mygrp -NamespaceName myns -QueueName myqueue -QueueObj $q
```

## <a name="topic-status"></a>토픽 상태
Azure Portal에서 토픽 상태를 변경할 수 있습니다. 항목의 현재 상태를 선택 하 여 상태를 변경할 수 있는 다음 페이지를 표시 합니다. 

:::image type="content" source="./media/entity-suspend/topic-state-change.png" alt-text="항목 상태 변경":::

**토픽** 에 대해 설정할 수 있는 상태는 다음과 같습니다.
- **활성** : 항목이 활성 상태입니다. 항목에 메시지를 보낼 수 있습니다. 
- **사용 안 함** : 항목이 일시 중단 됩니다. 토픽에 메시지를 보낼 수 없습니다. 
- **Senddisabled** : **disabled** 와 동일 하 게 적용 됩니다. 토픽에 메시지를 보낼 수 없습니다. 토픽에 메시지를 전송 하려고 하면 예외가 발생 합니다. 

## <a name="subscription-status"></a>구독 상태
Azure Portal에서 구독 상태를 변경할 수 있습니다. 구독의 현재 상태를 선택 하 여 상태를 변경할 수 있는 다음 페이지를 표시 합니다. 

:::image type="content" source="./media/entity-suspend/subscription-state-change.png" alt-text="구독 상태 변경":::

**구독** 에 대해 설정할 수 있는 상태는 다음과 같습니다.
- **활성** : 구독이 활성 상태입니다. 구독 signature.frm 메시지를 받을 수 있습니다.
- **사용 안 함** : 구독이 일시 중단 됩니다. 구독에서 메시지를 받을 수 없습니다. 
- **ReceiveDisabled** : **Disabled** 와 동일 하 게 적용 됩니다. 구독에서 메시지를 받을 수 없습니다. 구독에 메시지를 수신 하려고 하면 예외가 발생 합니다.

| 토픽 상태 | 구독 상태 | 동작 | 
| ------------ | ------------------- | -------- | 
| 활성 | 활성 | 토픽에 메시지를 보내고 구독에서 메시지를 받을 수 있습니다. | 
| 활성 | 사용 안 함 또는 받기 사용 안 함 | 토픽에 메시지를 보낼 수 있지만 구독에서 메시지를 받을 수는 없습니다. | 
| 사용 안 함 또는 보내기 사용 안 함 | 활성 | 토픽에 메시지를 보낼 수는 없지만 이미 구독에 있는 메시지는 받을 수 있습니다. | 
| 사용 안 함 또는 보내기 사용 안 함 | 사용 안 함 또는 받기 사용 안 함 | 토픽에 메시지를 보낼 수 없으며 구독에서 받을 수 없습니다. | 

## <a name="other-statuses"></a>기타 상태
[EntityStatus](/dotnet/api/microsoft.servicebus.messaging.entitystatus) 열거형은 시스템에 의해서만 설정될 수 있는 전환 상태 집합도 정의합니다. 


## <a name="next-steps"></a>다음 단계

Service Bus 메시징에 대해 자세히 알아보려면 다음 항목을 참조하세요.

* [Service Bus 큐, 토픽 및 구독](service-bus-queues-topics-subscriptions.md)
* [Service Bus 큐 시작](service-bus-dotnet-get-started-with-queues.md)
* [Service Bus 토픽 및 구독을 사용하는 방법](service-bus-dotnet-how-to-use-topics-subscriptions.md)

[1]: ./media/entity-suspend/entity-state-change.png

