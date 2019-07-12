---
title: Azure SignalR Service 이벤트에 대응
description: Azure Event Grid를 사용 하 여 Azure SignalR Service 이벤트를 구독할 수 있습니다.
services: azure-signalr,event-grid
author: chenyl
ms.author: chenyl
ms.reviewer: zhshang
ms.date: 06/12/2019
ms.topic: conceptual
ms.service: azure-signalr
ms.openlocfilehash: 02f88c5953d499b30f2ea3408318f70a72f42b0f
ms.sourcegitcommit: 1572b615c8f863be4986c23ea2ff7642b02bc605
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/10/2019
ms.locfileid: "67789190"
---
# <a name="reacting-to-azure-signalr-service-events"></a>Azure SignalR Service 이벤트에 대응

Azure SignalR Service 이벤트에 연결 되었거나 연결이 최신 서버 리스 아키텍처를 사용 하 여 클라이언트 연결에 반응 하는 응용 프로그램을 허용 합니다. 복잡한 코드나 비용이 많이 들고 비효율적인 폴링 서비스가 없어도 이렇게 할 수 있습니다.  대신, 이벤트는 [Azure Event Grid](https://azure.microsoft.com/services/event-grid/)를 통해 [Azure Functions](https://azure.microsoft.com/services/functions/), [Azure Logic Apps](https://azure.microsoft.com/services/logic-apps/)와 같은 구독자로 푸시되거나 사용자 지정 HTTP 수신기로도 푸시되며, 요금은 사용한 만큼만 청구됩니다.

Azure SignalR Service 이벤트는 신뢰할 수 있는 배달 서비스를 통해 다양 한 응용 프로그램을 다시 시도 정책 및 배달 못 한 편지를 제공 하는 Event Grid 서비스를 안정적으로 전송 됩니다. 자세한 내용은 참조 하세요 [Event Grid 메시지 배달 및 재시도](https://docs.microsoft.com/azure/event-grid/delivery-and-retry)합니다.

![Event Grid 모델](https://docs.microsoft.com/azure/event-grid/media/overview/functional-model.png)

## <a name="serverless-state"></a>서버 리스 상태
클라이언트 연결 서버 리스 상태인 경우에 azure SignalR Service 이벤트 활성화 됩니다. 일반적으로 클라이언트 허브 서버로 라우팅 하지, 하는 경우 서버 리스 상태로 이동 합니다. 클래식 모드 클라이언트 연결에 연결 하는 허브 허브 서버 없는 경우에 작동 합니다. 그러나 서버 리스 모드는 몇 가지 문제를 방지 하려면 권장 됩니다. 서비스 모드에 대 한 자세한 내용은 참조 하십시오 [서비스 모드를 선택 하는 방법](https://github.com/Azure/azure-signalr/blob/dev/docs/faq.md#what-is-the-meaning-of-service-mode-defaultserverlessclassic-how-can-i-choose)합니다.

## <a name="available-azure-signalr-service-events"></a>사용 가능한 Azure SignalR Service 이벤트
Event Grid는 [이벤트 구독](../event-grid/concepts.md#event-subscriptions)을 사용하여 이벤트 메시지를 구독자에게 라우팅합니다. Azure SignalR Service 이벤트 구독에는 두 가지 유형의 이벤트 지원:  

|이벤트 이름|설명|
|----------|-----------|
|`Microsoft.SignalRService.ClientConnectionConnected`|클라이언트 연결에 연결 되어 있을 때 발생 합니다.|
|`Microsoft.SignalRService.ClientConnectionDisconnected`|클라이언트 연결을 연결이 끊긴 경우에 발생 합니다.|

## <a name="event-schema"></a>이벤트 스키마
Azure SignalR Service 이벤트 데이터의 변경 내용에 응답 하는 데 필요한 모든 정보를 포함 합니다. 이벤트는 eventType 속성이 시작 "Microsoft.SignalRService"를 사용 하 여 Azure SignalR Service를 식별할 수 있습니다. Event Grid 이벤트 속성의 사용에 대 한 추가 정보에 설명 되어 있습니다 [Event Grid 이벤트 스키마](../event-grid/event-schema.md)합니다.  

클라이언트 연결 연결 된 이벤트의 예는 다음과 같습니다.
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

자세한 내용은 [SignalR Service 이벤트 스키마](../event-grid/event-schema-azure-signalr.md)합니다.

## <a name="next-steps"></a>다음 단계

Azure SignalR Service 이벤트를 사용해 및 Event Grid에 대 한 자세한 정보:

> [!div class="nextstepaction"]
> [Azure SignalR Service를 사용 하 여 샘플 Event Grid 통합을 시도](./signalr-howto-event-grid-integration.md)
> [Event Grid 정보](../event-grid/overview.md)
