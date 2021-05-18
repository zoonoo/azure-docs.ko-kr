---
title: Azure SignalR Service 이벤트에 대응
description: Azure Event Grid를 사용하여 Azure SignalR Service 이벤트를 구독할 수 있습니다. 해당 이벤트는 다른 다운스트림 서비스를 트리거할 수 있습니다.
services: azure-signalr,event-grid
author: chenyl
ms.author: chenyl
ms.reviewer: zhshang
ms.date: 11/13/2019
ms.topic: conceptual
ms.service: signalr
ms.openlocfilehash: 77c8887ac19c6ce4c7d83734bdd2b44d9213914d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92151117"
---
# <a name="reacting-to-azure-signalr-service-events"></a>Azure SignalR Service 이벤트에 대응

Azure SignalR Service 이벤트를 통해 애플리케이션은 최신 서버리스 아키텍처를 사용하여 연결되거나 연결이 끊긴 클라이언트 연결에 대응할 수 있습니다. 복잡한 코드나 비용이 많이 들고 비효율적인 폴링 서비스가 없어도 이렇게 할 수 있습니다.  대신, 이벤트는 [Azure Event Grid](https://azure.microsoft.com/services/event-grid/)를 통해 [Azure Functions](https://azure.microsoft.com/services/functions/), [Azure Logic Apps](https://azure.microsoft.com/services/logic-apps/)와 같은 구독자 또는 사용자 지정 HTTP 수신기로도 푸시됩니다. Azure SignalR에서는 사용한 양만큼만 요금을 지불하면 됩니다.

Azure SignalR Service 이벤트는 다양한 다시 시도 정책 및 배달 못한 편지를 통해 애플리케이션에 신뢰할 수 있는 배달 서비스를 제공하는 Event Grid 서비스에 안정적으로 전송됩니다. 자세한 내용은 [Event Grid 메시지 배달 및 다시 시도](../event-grid/delivery-and-retry.md)를 참조하세요.

![Event Grid 모델](/azure/event-grid/media/overview/functional-model.png)

## <a name="serverless-state"></a>서버리스 상태
Azure SignalR Service 이벤트는 클라이언트 연결이 서버리스 상태인 경우에만 활성화됩니다. 클라이언트가 허브 서버로 라우팅되지 않는 경우 서버리스 상태로 전환됩니다. 클래식 모드는 클라이언트가 연결되는 허브에 허브 서버가 없는 경우에만 작동합니다. 서버리스 모드를 사용하는 것이 좋습니다. 서비스 모드에 대한 자세한 내용은 [서비스 모드를 선택하는 방법](https://github.com/Azure/azure-signalr/blob/dev/docs/faq.md#what-is-the-meaning-of-service-mode-defaultserverlessclassic-how-can-i-choose)을 참조하세요.

## <a name="available-azure-signalr-service-events"></a>사용 가능한 Azure SignalR Service 이벤트
Event Grid는 [이벤트 구독](../event-grid/concepts.md#event-subscriptions)을 사용하여 이벤트 메시지를 구독자에게 라우팅합니다. Azure SignalR Service 이벤트 구독은 다음 두 가지 유형의 이벤트를 지원합니다.  

|이벤트 이름|Description|
|----------|-----------|
|`Microsoft.SignalRService.ClientConnectionConnected`|클라이언트가 연결될 때 발생합니다.|
|`Microsoft.SignalRService.ClientConnectionDisconnected`|클라이언트 연결이 끊어질 때 발생합니다.|

## <a name="event-schema"></a>이벤트 스키마
Azure SignalR Service 이벤트에는 데이터 변경에 대응하는 데 필요한 모든 정보가 포함되어 있습니다. eventType 속성이 “Microsoft.SignalRService”로 시작하는 Azure SignalR Service 이벤트를 식별할 수 있습니다. Event Grid 이벤트 속성 사용법에 대한 추가 정보는 [Event Grid 이벤트 스키마](../event-grid/event-schema.md)를 참조하세요.  

다음은 클라이언트 연결됨 이벤트의 예입니다.
```json
[{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/signalr-rg/providers/Microsoft.SignalRService/SignalR/signalr-resource",
  "subject": "/hub/chat",
  "eventType": "Microsoft.SignalRService.ClientConnectionConnected",
  "eventTime": "2019-06-10T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
  "data": {
    "timestamp": "2019-06-10T18:41:00.9584103Z",
    "hubName": "chat",
    "connectionId": "crH0uxVSvP61p5wkFY1x1A",
    "userId": "user-eymwyo23"
  },
  "dataVersion": "1.0",
  "metadataVersion": "1"
}]
```

자세한 내용은 [SignalR Service 이벤트 스키마](../event-grid/event-schema-azure-signalr.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계

Event Grid에 대해 자세히 알아보고 Azure SignalR Service 이벤트를 사용해 보세요.

> [!div class="nextstepaction"]
> [Azure SignalR Service와 Event Grid 통합 샘플 사용해 보기](./signalr-howto-event-grid-integration.md)
> [Event Grid 정보](../event-grid/overview.md)