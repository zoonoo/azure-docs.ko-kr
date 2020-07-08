---
title: Azure Event Grid 이벤트에 대한 이벤트 처리기로서의 Azure 함수
description: Event Grid 이벤트에 대한 이벤트 처리기로 Azure 함수를 사용하는 방법을 설명합니다.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 05/11/2020
ms.author: spelluru
ms.openlocfilehash: 0152a9fc11562744f83ab9d20466a3dcc8e2e6e0
ms.sourcegitcommit: cf7caaf1e42f1420e1491e3616cc989d504f0902
ms.contentlocale: ko-KR
ms.lasthandoff: 05/22/2020
ms.locfileid: "83800422"
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
                "maxEventsPerBatch": 1,
                "preferredBatchSizeInKilobytes": 64
            }
        },
        "eventDeliverySchema": "EventGridSchema"
    }
}
```

## <a name="next-steps"></a>다음 단계
지원되는 이벤트 처리기 목록은 [이벤트 처리기](event-handlers.md) 문서를 참조하세요. 
