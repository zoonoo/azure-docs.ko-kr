---
title: Azure Event Grid 이벤트에 대한 이벤트 처리기로서의 이벤트 허브
description: Azure Event Grid 이벤트에 대한 이벤트 처리기로 이벤트 허브를 사용하는 방법을 설명합니다.
ms.topic: conceptual
ms.date: 07/07/2020
ms.openlocfilehash: fa8fdd66eb153f6a972753eb359261100f19cd15
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/08/2020
ms.locfileid: "86105832"
---
# <a name="event-hub-as-an-event-handler-for-azure-event-grid-events"></a>Azure Event Grid 이벤트에 대한 이벤트 처리기로서의 이벤트 허브
이벤트 처리기는 이벤트가 전송된 위치입니다. 처리기는 이벤트를 처리하기 위한 작업을 수행합니다. 이벤트를 처리하도록 여러 Azure 서비스가 자동으로 구성되며, **Azure Event Hubs**가 그 중 하나입니다. 

솔루션이 이벤트를 처리할 수 있는 속도보다 빠르게 Event Grid에서 이벤트를 가져오는 경우 **Event Hubs**를 사용하세요. 이벤트가 이벤트 허브에 있으면 애플리케이션은 자체 일정에 따라 이벤트 허브에서 이벤트를 처리할 수 있습니다. 들어오는 이벤트를 처리하도록 이벤트 처리 규모를 확장할 수 있습니다.

## <a name="tutorials"></a>자습서
다음 예제를 참조하세요. 

|제목  |Description  |
|---------|---------|
| [빠른 시작: Azure CLI를 사용하여 Azure Event Hubs로 사용자 지정 이벤트 라우팅](custom-event-to-eventhub.md) | 애플리케이션에서 처리하도록 사용자 지정 이벤트를 이벤트 허브로 보냅니다. |
| [Resource Manager 템플릿: Event Grid 사용자 지정 항목 만들기 및 이벤트 허브로 이벤트 보내기](https://github.com/Azure/azure-quickstart-templates/tree/master/101-event-grid-event-hubs-handler)| 사용자 지정 토픽에 대한 구독을 만드는 Resource Manager 템플릿입니다. Azure Event Hubs에 이벤트를 전송합니다. |

## <a name="message-properties"></a>메시지 속성
**이벤트 허브**를 Event Grid의 이벤트에 대한 이벤트 처리기로 사용하는 경우 다음 메시지 헤더를 설정합니다. 

| 속성 이름 | Description |
| ------------- | ----------- | 
| aeg-subscription-name | 이벤트 구독의 이름입니다. |
| aeg-delivery-count | <p>이벤트를 시도한 횟수입니다.</p> <p>예제: "1"</p> |
| aeg-event-type | <p>이벤트의 유형입니다.</p><p> 예제: “Microsoft.Storage.blobCreated”</p> | 
| aeg-metadata-version | <p>이벤트의 메타데이터 버전입니다.</p> <p>예제: “1”.</p><p> **Event Grid 이벤트 스키마**의 경우 이 속성은 메타데이터 버전을 나타내고 **클라우드 이벤트 스키마**의 경우 **사양 버전**을 나타냅니다. </p>|
| aeg-data-version | <p>이벤트의 데이터 버전입니다.</p><p>예제: “1”.</p><p>**Event Grid 이벤트 스키마**의 경우 이 속성은 데이터 버전을 나타내고 **클라우드 이벤트 스키마**에 대해서는 적용되지 않습니다.</p> |
| aeg-output-event-id | Event Grid 이벤트의 ID입니다. |

## <a name="rest-examples-for-put"></a>REST 예제(PUT의 경우)


### <a name="event-hub"></a>이벤트 허브

```json
{
    "properties": 
    {
        "destination": 
        {
            "endpointType": "EventHub",
            "properties": 
            {
                "resourceId": "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventHub/namespaces/<EVENT HUBS NAMESPACE NAME>/eventhubs/<EVENT HUB NAME>"
            }
        },
        "eventDeliverySchema": "EventGridSchema"
    }
}
```

### <a name="event-hub---delivery-with-managed-identity"></a>이벤트 허브 - 관리 ID로 전달

```json
{
    "properties": {
        "deliveryWithResourceIdentity": 
        {
            "identity": 
            {
                "type": "SystemAssigned"
            },
            "destination": 
            {
                "endpointType": "EventHub",
                "properties": 
                {
                    "resourceId": "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventHub/namespaces/<EVENT HUBS NAMESPACE NAME>/eventhubs/<EVENT HUB NAME>"
                }
            }
        },
        "eventDeliverySchema": "EventGridSchema"
    }
}
```

## <a name="next-steps"></a>다음 단계
지원되는 이벤트 처리기 목록은 [이벤트 처리기](event-handlers.md) 문서를 참조하세요. 
