---
title: Azure Event Grid 이벤트에 대한 이벤트 처리기로서의 Service Bus 큐 및 토픽
description: Service Bus 큐 및 토픽을 Azure Event Grid 이벤트에 대한 이벤트 처리기로 사용하는 방법을 설명합니다.
ms.topic: conceptual
ms.date: 09/03/2020
ms.openlocfilehash: 00f937a66ff17c2d5f502fe976675c999ee02a58
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/25/2020
ms.locfileid: "91270171"
---
# <a name="service-bus-queues-and-topics-as-event-handlers-for-azure-event-grid-events"></a>Azure Event Grid 이벤트에 대한 이벤트 처리기로서의 Service Bus 큐 및 토픽
이벤트 처리기는 이벤트가 전송된 위치입니다. 처리기는 이벤트를 처리하기 위한 추가 작업을 수행합니다. 이벤트를 처리하도록 여러 Azure 서비스가 자동으로 구성되며, **Azure Service Bus**는 그 중 하나입니다. 

서비스 큐 또는 토픽을 Event Grid의 이벤트 처리기로 사용할 수 있습니다. 

## <a name="service-bus-queues"></a>Service Bus 큐
엔터프라이즈 애플리케이션의 버퍼링 또는 명령 및 제어 시나리오에서 사용하기 위해 Event Grid의 이벤트를 Service Bus 큐에 직접 라우팅할 수 있습니다.

Azure Portal에서 이벤트 구독을 만드는 동안 **Service Bus 큐**를 엔드포인트 유형으로 선택한 다음, **엔드포인트 선택**을 클릭하여 Service Bus 큐를 선택합니다.

### <a name="using-cli-to-add-a-service-bus-queue-handler"></a>CLI를 사용하여 Service Bus 큐 처리기 추가

Azure CLI의 경우 다음 예제에서는 Event Grid 토픽을 구독하고 Service Bus 큐에 연결합니다.

```azurecli-interactive
az eventgrid event-subscription create \
    --name <my-event-subscription> \
    --source-resource-id /subscriptions/{SubID}/resourceGroups/{RG}/providers/Microsoft.EventGrid/topics/topic1 \
    --endpoint-type servicebusqueue \
    --endpoint /subscriptions/{SubID}/resourceGroups/TestRG/providers/Microsoft.ServiceBus/namespaces/ns1/queues/queue1
```

## <a name="service-bus-topics"></a>Service Bus 토픽

Event Grid의 이벤트를 Service Bus 토픽으로 직접 라우팅하여 Service Bus 토픽 또는 명령 및 제어 메시징 시나리오에서 Azure 시스템 이벤트를 처리할 수 있습니다.

Azure Portal에서 이벤트 구독을 만들 때 **Service Bus 토픽**을 엔드포인트 유형으로 선택한 다음, **선택 및 엔드포인트**를 클릭하여 Service Bus 토픽을 선택합니다.

### <a name="using-cli-to-add-a-service-bus-topic-handler"></a>CLI를 사용하여 Service Bus 토픽 처리기 추가

Azure CLI의 경우 다음 예제에서는 Event Grid 토픽을 구독하고 Service Bus 큐에 연결합니다.

```azurecli-interactive
az eventgrid event-subscription create \
    --name <my-event-subscription> \
    --source-resource-id /subscriptions/{SubID}/resourceGroups/{RG}/providers/Microsoft.EventGrid/topics/topic1 \
    --endpoint-type servicebustopic \
    --endpoint /subscriptions/{SubID}/resourceGroups/TestRG/providers/Microsoft.ServiceBus/namespaces/ns1/topics/topic1
```

## <a name="message-properties"></a>메시지 속성
Event Grid에서 이벤트에 대 한 이벤트 처리기로 **Service Bus 토픽 또는 큐** 를 사용 하는 경우 메시지 헤더에 표시 되는 속성은 다음과 같습니다. 

| 속성 이름 | Description |
| ------------- | ----------- | 
| aeg-subscription-name | 이벤트 구독의 이름입니다. |
| aeg-delivery-count | <p>이벤트를 시도한 횟수입니다.</p> <p>예제: "1"</p> |
| aeg-event-type | <p>이벤트의 유형입니다.</p><p> 예제: “Microsoft.Storage.blobCreated”</p> | 
| aeg-metadata-version | <p>이벤트의 메타데이터 버전입니다.</p> <p>예제: “1”.</p><p> **Event Grid 이벤트 스키마**의 경우 이 속성은 메타데이터 버전을 나타내고 **클라우드 이벤트 스키마**의 경우 **사양 버전**을 나타냅니다. </p>|
| aeg-data-version | <p>이벤트의 데이터 버전입니다.</p><p>예제: “1”.</p><p>**Event Grid 이벤트 스키마**의 경우 이 속성은 데이터 버전을 나타내고 **클라우드 이벤트 스키마**에 대해서는 적용되지 않습니다.</p> |

## <a name="message-headers"></a>메시지 헤더
조정 된 메시지로 Service Bus 큐 또는 토픽으로 이벤트를 보낼 때 조정 된 `messageid` 메시지의는 내부 시스템 ID입니다.

메시지의 내부 시스템 ID는 이벤트를 다시 배달 하는 동안 유지 되므로 중복 된 배달을 방지 하려면 service bus 엔터티에서 **중복 검색** 을 설정 합니다. Service Bus 엔터티에서 중복 검색 기간을 이벤트의 TTL(Time-to-Live) 또는 최대 다시 시도 기간 중 더 긴 기간으로 설정하는 것이 좋습니다.

## <a name="rest-examples-for-put"></a>REST 예제(PUT의 경우)

### <a name="service-bus-queue"></a>Service Bus 큐

```json
{
    "properties": 
    {
        "destination": 
        {
            "endpointType": "ServiceBusQueue",
            "properties": 
            {
                "resourceId": "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.ServiceBus/namespaces/<SERVICE BUS NAMESPACE NAME>/queues/<SERVICE BUS QUEUE NAME>"
            }
        },
        "eventDeliverySchema": "EventGridSchema"
    }
}
```

### <a name="service-bus-queue---delivery-with-managed-identity"></a>Service Bus 쿼리 - 관리 ID로 전달

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
                "endpointType": "ServiceBusQueue",
                "properties": 
                {
                    "resourceId": "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.ServiceBus/namespaces/<SERVICE BUS NAMESPACE NAME>/queues/<SERVICE BUS QUEUE NAME>"
                }
            }
        },
        "eventDeliverySchema": "EventGridSchema"
    }
}
```

### <a name="service-bus-topic"></a>Service Bus 토픽

```json
{
    "properties": 
    {
        "destination": 
        {
            "endpointType": "ServiceBusTopic",
            "properties": 
            {
                "resourceId": "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.ServiceBus/namespaces/<SERVICE BUS NAMESPACE NAME>/topics/<SERVICE BUS TOPIC NAME>"
            }
        },
        "eventDeliverySchema": "EventGridSchema"
    }
}
```

### <a name="service-bus-topic---delivery-with-managed-identity"></a>Service Bus 토픽 - 관리 ID로 전달

```json
{
    "properties": 
    {
        "deliveryWithResourceIdentity": 
        {
            "identity": 
            {
                "type": "SystemAssigned"
            },
            "destination": 
            {
                "endpointType": "ServiceBusTopic",
                "properties": 
                {
                    "resourceId": "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.ServiceBus/namespaces/<SERVICE BUS NAMESPACE NAME>/topics/<SERVICE BUS TOPIC NAME>"
                }
            }
        },
        "eventDeliverySchema": "EventGridSchema"
    }
}
```

> [!NOTE]
> **다른 테 넌 트** 의 Azure Servive Bus 큐 또는 토픽에 이벤트를 전달 하는 것은 지원 되지 않습니다. 

## <a name="next-steps"></a>다음 단계
지원되는 이벤트 처리기 목록은 [이벤트 처리기](event-handlers.md) 문서를 참조하세요. 
