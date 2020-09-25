---
title: Azure Event Grid 이벤트에 대한 이벤트 처리기로서의 Azure 함수
description: Event Grid 이벤트에 대한 이벤트 처리기로 Azure 함수를 사용하는 방법을 설명합니다.
ms.topic: conceptual
ms.date: 09/18/2020
ms.openlocfilehash: 87aeb78729dcc7bec9f193fab389e5c0952e63d5
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/25/2020
ms.locfileid: "91270327"
---
# <a name="azure-function-as-an-event-handler-for-event-grid-events"></a>Event Grid 이벤트에 대한 이벤트 처리기로서의 Azure 함수

이벤트 처리기는 이벤트가 전송된 위치입니다. 처리기는 이벤트를 처리하기 위한 작업을 수행합니다. 이벤트를 처리하도록 여러 Azure 서비스가 자동으로 구성되며, **Azure Functions**가 그 중 하나입니다. 

서버리스 아키텍처에서 **Azure Functions**를 사용하여 Event Grid의 이벤트에 응답합니다. Azure 함수를 처리기로 사용할 때는 일반 HTTP 트리거 대신 Event Grid 트리거를 사용합니다. Event Grid는 Event Grid 트리거의 유효성을 자동으로 검사합니다. 일반 HTTP 트리거를 사용하면 [유효성 검사 응답](webhook-event-delivery.md)을 직접 구현해야 합니다.

자세한 내용은 함수에서 Event Grid 트리거를 사용하는 방법에 대한 개요는 [Azure Functions용 Event Grid 트리거](../azure-functions/functions-bindings-event-grid.md)를 참조하세요.

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
높은 처리량을 위해 구독에서 일괄 처리를 사용 하도록 설정 합니다. Azure Portal 사용 하는 경우 구독을 만들 때 또는 만든 후에 일괄 처리당 최대 이벤트 수와 기본 일괄 처리 크기 (kb)를 설정할 수 있습니다. 

Azure Portal, PowerShell, CLI 또는 리소스 관리자 템플릿을 사용 하 여 일괄 처리 설정을 구성할 수 있습니다. 

### <a name="azure-portal"></a>Azure portal
UI에서 구독을 만들 때 **이벤트 구독 만들기** 페이지에서 **고급 기능** 탭으로 전환 하 고 **일괄 처리당 최대 이벤트** 수 및 **기본 일괄 처리 크기 (kb)** 를 설정 합니다. 
    
:::image type="content" source="./media/custom-event-to-function/enable-batching.png" alt-text="구독을 만들 때 일괄 처리 사용":::

**Event Grid 항목** 페이지의 **기능** 탭에서 기존 구독에 대해 이러한 값을 업데이트할 수 있습니다. 

:::image type="content" source="./media/custom-event-to-function/features-batch-settings.png" alt-text="만든 후 일괄 처리 사용":::

### <a name="azure-resource-manager-template"></a>Azure Resource Manager 템플릿
Azure Resource Manager 템플릿에서 **maxEventsPerBatch** 및 **preferredBatchSizeInKilobytes** 를 설정할 수 있습니다. 자세한 내용은 [Microsoft EventGrid Eventgrid 템플릿 참조](https://docs.microsoft.com/azure/templates/microsoft.eventgrid/eventsubscriptions)를 참조 하세요.

### <a name="azure-cli"></a>Azure CLI
[Az event grid event-subscription create](https://docs.microsoft.com/cli/azure/eventgrid/event-subscription?view=azure-cli-latest#az_eventgrid_event_subscription_create&preserve-view=true) 또는 [az event grid event-subscription update](https://docs.microsoft.com/cli/azure/eventgrid/event-subscription?view=azure-cli-latest#az_eventgrid_event_subscription_update&preserve-view=true) 명령을 사용 하 여 다음 매개 변수를 사용 하 여 일괄 처리 관련 설정을 구성할 수 `--max-events-per-batch` 있습니다. `--preferred-batch-size-in-kilobytes`

### <a name="azure-powershell"></a>Azure PowerShell
[AzEventGridSubscription](https://docs.microsoft.com/powershell/module/az.eventgrid/new-azeventgridsubscription) 또는 [AzEventGridSubscription](https://docs.microsoft.com/powershell/module/az.eventgrid/update-azeventgridsubscription) cmdlet을 사용 하 여 또는와 같은 매개 변수를 사용 하 여 일괄 처리 관련 설정을 구성할 수 있습니다. `-MaxEventsPerBatch` `-PreferredBatchSizeInKiloBytes`

> [!NOTE]
> **다른 테 넌 트** 의 Azure 함수에 이벤트를 전달 하는 것은 지원 되지 않습니다. 

## <a name="next-steps"></a>다음 단계
지원되는 이벤트 처리기 목록은 [이벤트 처리기](event-handlers.md) 문서를 참조하세요. 
