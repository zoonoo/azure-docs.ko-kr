---
title: Azure Event Grid 이벤트에 대한 이벤트 처리기로서 Azure의 함수 사용
description: Azure Functions에서 만들고 호스트된 함수를 Event Grid 이벤트에 대한 이벤트 처리기로 사용하는 방법에 대해 설명합니다.
ms.topic: conceptual
ms.date: 03/15/2021
ms.openlocfilehash: f547b09fe7e62eb3fa9e02bd17298a936350f871
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103496544"
---
# <a name="use-a-function-as-an-event-handler-for-event-grid-events"></a>Event Grid 이벤트에 대한 이벤트 처리기로서의 함수 사용

이벤트 처리기는 이벤트가 전송된 위치입니다. 처리기는 이벤트를 처리하기 위한 작업을 수행합니다. 이벤트를 처리하도록 여러 Azure 서비스가 자동으로 구성되며, **Azure Functions** 가 그 중 하나입니다. 


Azure에서 함수를 이벤트에 대한 처리기로 사용하려면 다음 방법 중 하나를 수행합니다. 

-   [Event Grid 트리거](../azure-functions/functions-bindings-event-grid-trigger.md)를 사용합니다.  **Azure Function** 을 **엔드포인트 유형** 으로 지정합니다. 그런 다음, 함수 앱 및 이벤트를 처리할 함수를 지정합니다. 
-   [HTTP 트리거](../azure-functions/functions-bindings-http-webhook.md)를 사용합니다.  **웹후크** 를 **엔드포인트 유형** 으로 지정합니다. 그런 다음, 이벤트를 처리할 함수에 대한 URL을 지정합니다. 

첫 번째 접근 방식(Event Grid 트리거)은 두 번째 접근 방식에 비해 다음과 같은 이점이 있으므로 첫 번째 접근 방식을 사용하는 것이 좋습니다.
-   Event Grid는 Event Grid 트리거의 유효성을 자동으로 검사합니다. 일반 HTTP 트리거를 사용하면 [유효성 검사 응답](webhook-event-delivery.md)을 직접 구현해야 합니다.
-   Event Grid는 함수가 이벤트를 처리할 수 있는 인식률에 따라 Event Grid 이벤트에 의해 트리거되는 함수에 이벤트가 전달되는 속도를 자동으로 조정합니다. 이 속도 일치 기능은 함수의 이벤트 처리 속도가 시간에 따라 달라질 수 있으므로 함수가 이벤트를 처리할 수 없기 때문에 발생하는 전송 오류를 방지합니다. 높은 처리량으로 효율성을 향상시키려면 이벤트 구독에서 일괄 처리를 사용합니다. 자세한 내용은 [일괄 처리 사용](#enable-batching)을 참조하세요.

    > [!NOTE]
    > 현재, 이벤트가 **CloudEvents** 스키마를 통해 전달되는 경우 함수 앱에 대해 Event Grid 트리거를 사용할 수 없습니다. 대신, HTTP 트리거를 사용합니다.

## <a name="tutorials"></a>자습서

|제목  |Description  |
|---------|---------|
| [빠른 시작: 함수를 사용하여 이벤트 처리](custom-event-to-function.md) | 처리를 위한 함수로 사용자 지정 이벤트를 보냅니다. |
| [자습서: Event Grid를 사용하여 업로드된 이미지 크기 자동 조정](resize-images-on-storage-blob-upload-event.md) | 사용자가 웹앱을 통해 이미지를 스토리지 계정에 업로드합니다. 스토리지 BLOB이 만들어지면 Event Grid는 이벤트를 함수 앱에 보내고, 그곳에서 업로드된 이미지를 크기 조정합니다. |
| [자습서: 데이터 웨어하우스로 빅 데이터 스트림](event-grid-event-hubs-integration.md) | Event Hubs가 캡처 파일을 만들 때 Event Grid는 함수 앱에 이벤트를 보냅니다. 앱은 캡처 파일을 검색하고 데이터를 데이터 웨어하우스에 마이그레이션합니다. |
| [자습서: Azure Service Bus-Azure Event Grid 통합 예제](../service-bus-messaging/service-bus-to-event-grid-integration-example.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Event Grid는 Service Bus 토픽의 메시지를 함수 앱과 논리 앱에 전송합니다. |

## <a name="rest-example-for-put"></a>REST 예제(PUT의 경우)

```json
{
    "properties": 
    {
        "destination": 
        {
            "endpointType": "AzureFunction",
            "properties": 
            {
                "resourceId": "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.Web/sites/<FUNCTION APP NAME>/functions/<FUNCTION NAME>",
                "maxEventsPerBatch": 10,
                "preferredBatchSizeInKilobytes": 6400
            }
        },
        "eventDeliverySchema": "EventGridSchema"
    }
}
```

## <a name="enable-batching"></a>일괄 처리 사용
높은 처리량을 위해 구독에서 일괄 처리를 사용합니다. Azure Portal을 사용하는 경우 구독을 만들 때 또는 만든 후에 일괄 처리당 최대 이벤트 수와 기본 일괄 처리 크기(KB)를 설정할 수 있습니다. 

Azure Portal, PowerShell, CLI 또는 Resource Manager 템플릿을 사용하여 일괄 처리 설정을 구성할 수 있습니다. 

### <a name="azure-portal"></a>Azure portal
UI에 구독을 만들 때 **이벤트 구독 만들기** 페이지에서 **고급 기능** 탭으로 전환하고, **일괄 처리당 최대 이벤트** 및 **기본 설정된 일괄 처리(KB)** 에 대한 값을 설정합니다. 
    
:::image type="content" source="./media/custom-event-to-function/enable-batching.png" alt-text="구독을 만들 때 일괄 처리 사용":::

**Event Grid 토픽** 페이지의 **기능** 탭에서 기존 구독에 대해 이러한 값을 업데이트할 수 있습니다. 

:::image type="content" source="./media/custom-event-to-function/features-batch-settings.png" alt-text="만든 후에 일괄 처리 사용":::

### <a name="azure-resource-manager-template"></a>Azure Resource Manager 템플릿
Azure Resource Manager 템플릿에서 **maxEventsPerBatch** 및 **preferredBatchSizeInKilobytes** 를 설정할 수 있습니다. 자세한 내용은 [Microsoft.EventGrid eventSubscriptions 템플릿 참조](/azure/templates/microsoft.eventgrid/eventsubscriptions)를 참조하세요.

### <a name="azure-cli"></a>Azure CLI
[az eventgrid event-subscription create](/cli/azure/eventgrid/event-subscription#az_eventgrid_event_subscription_create&preserve-view=true) 또는 [az eventgrid event-subscription update](/cli/azure/eventgrid/event-subscription#az_eventgrid_event_subscription_update&preserve-view=true) 명령을 사용하여 `--max-events-per-batch` 또는 `--preferred-batch-size-in-kilobytes` 매개 변수를 사용하는 일괄 처리 관련 설정을 구성할 수 있습니다.

### <a name="azure-powershell"></a>Azure PowerShell
[New-AzEventGridSubscription](/powershell/module/az.eventgrid/new-azeventgridsubscription) 또는 [Update-AzEventGridSubscription](/powershell/module/az.eventgrid/update-azeventgridsubscription) cmdlet을 사용하여 `-MaxEventsPerBatch` 또는 `-PreferredBatchSizeInKiloBytes` 매개 변수를 사용하는 일괄 처리 관련 설정을 구성할 수 있습니다.

> [!NOTE]
> Event Grid 트리거를 사용하는 경우 Event Grid 서비스는 대상 Azure 함수에 대한 클라이언트 암호를 가져오고 이를 사용하여 Azure 함수에 이벤트를 전달합니다. Azure Active Directory 애플리케이션을 사용하여 Azure 함수를 보호하는 경우, 일반 웹후크 방식으로 HTTP 트리거를 사용해야 합니다.

## <a name="next-steps"></a>다음 단계
지원되는 이벤트 처리기 목록은 [이벤트 처리기](event-handlers.md) 문서를 참조하세요.
