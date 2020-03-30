---
title: Azure SignalR 서비스 이벤트에 반응
description: Azure 이벤트 그리드를 사용하여 Azure SignalR 서비스 이벤트를 구독합니다. 이러한 이벤트에 의해 다른 다운스트림 서비스가 트리거될 수 있습니다.
services: azure-signalr,event-grid
author: chenyl
ms.author: chenyl
ms.reviewer: zhshang
ms.date: 11/13/2019
ms.topic: conceptual
ms.service: signalr
ms.openlocfilehash: a8e25907b40b910f2b91884d355b6ac85eeaa250
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74158201"
---
# <a name="reacting-to-azure-signalr-service-events"></a>Azure SignalR Service 이벤트에 대응

Azure SignalR Service 이벤트를 사용하면 응용 프로그램이 최신 서버리스 아키텍처를 사용하여 연결되거나 연결이 끊어진 클라이언트 연결에 반응할 수 있습니다. 복잡한 코드나 비용이 많이 들고 비효율적인 폴링 서비스가 없어도 이렇게 할 수 있습니다.  대신, 이벤트는 [Azure Event Grid](https://azure.microsoft.com/services/event-grid/)를 통해 [Azure Functions](https://azure.microsoft.com/services/functions/), [Azure Logic Apps](https://azure.microsoft.com/services/logic-apps/)와 같은 구독자로 푸시되거나 사용자 지정 HTTP 수신기로도 푸시되며, 요금은 사용한 만큼만 청구됩니다.

Azure SignalR Service 이벤트는 풍부한 재시도 정책과 배달 못한 배달을 통해 응용 프로그램에 안정적인 배달 서비스를 제공하는 Event Grid 서비스로 안정적으로 전송됩니다. 자세한 내용은 [이벤트 그리드 메시지 배달 및 다시 시도를](https://docs.microsoft.com/azure/event-grid/delivery-and-retry)참조하세요.

![Event Grid 모델](https://docs.microsoft.com/azure/event-grid/media/overview/functional-model.png)

## <a name="serverless-state"></a>서버 없는 상태
Azure SignalR 서비스 이벤트는 클라이언트 연결이 서버없는 상태에 있는 경우에만 활성화됩니다. 일반적으로 클라이언트가 허브 서버로 라우팅하지 않으면 서버 없는 상태로 전환됩니다. 클래식 모드는 해당 클라이언트 연결에 연결되는 허브 서버가 없는 경우에만 작동합니다. 그러나 몇 가지 문제를 피하기 위해 서버없는 모드를 사용하는 것이 좋습니다. 서비스 모드에 대한 자세한 내용은 [서비스 모드를 선택하는 방법을](https://github.com/Azure/azure-signalr/blob/dev/docs/faq.md#what-is-the-meaning-of-service-mode-defaultserverlessclassic-how-can-i-choose)참조하십시오.

## <a name="available-azure-signalr-service-events"></a>사용 가능한 Azure SignalR 서비스 이벤트
Event Grid는 [이벤트 구독](../event-grid/concepts.md#event-subscriptions)을 사용하여 이벤트 메시지를 구독자에게 라우팅합니다. Azure SignalR 서비스 이벤트 구독은 두 가지 유형의 이벤트를 지원합니다.  

|이벤트 이름|설명|
|----------|-----------|
|`Microsoft.SignalRService.ClientConnectionConnected`|클라이언트 연결이 연결될 때 발생합니다.|
|`Microsoft.SignalRService.ClientConnectionDisconnected`|클라이언트 연결이 끊어지면 발생합니다.|

## <a name="event-schema"></a>이벤트 스키마
Azure SignalR 서비스 이벤트에는 데이터의 변경 사항에 응답하는 데 필요한 모든 정보가 포함됩니다. eventType 속성 "Microsoft.SignalRService"로 시작 하는 Azure SignalR 서비스 이벤트를 식별할 수 있습니다. 이벤트 그리드 이벤트 속성의 사용에 대한 추가 정보는 [이벤트 그리드 이벤트 스키마에서](../event-grid/event-schema.md)설명합니다.  

다음은 클라이언트 연결 연결 이벤트의 예입니다.
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

자세한 내용은 [SignalR 서비스 이벤트 스키마를](../event-grid/event-schema-azure-signalr.md)참조하십시오.

## <a name="next-steps"></a>다음 단계

이벤트 그리드에 대해 자세히 알아보고 Azure SignalR 서비스 이벤트를 사용해 보십시오.

> [!div class="nextstepaction"]
> [이벤트 그리드에 대한 Azure SignalR 서비스와의](./signalr-howto-event-grid-integration.md)
> [About Event Grid](../event-grid/overview.md) 샘플 이벤트 그리드 통합 시도
