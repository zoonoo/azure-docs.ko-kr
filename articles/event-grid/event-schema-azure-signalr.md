---
title: Event Grid 소스로 서의 Azure SignalR
description: Azure Event Grid Azure SignalR 이벤트에 제공 되는 속성을 설명 합니다.
ms.topic: conceptual
ms.date: 07/07/2020
ms.openlocfilehash: 2ac391f366c4b9a82741a1b6b3135f5d7b5fe331
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "86106654"
---
# <a name="azure-event-grid-event-schema-for-signalr-service"></a>SignalR Service에 대 한 Azure Event Grid 이벤트 스키마

이 문서에서는 SignalR Service 이벤트에 대 한 속성 및 스키마를 제공 합니다.이벤트 스키마에 대한 소개는 [Azure Event Grid 이벤트 스키마](event-schema.md)를 참조하세요. 또한 Azure SignalR를 이벤트 원본으로 사용 하는 빠른 시작 및 자습서의 목록을 제공 합니다.

## <a name="event-grid-event-schema"></a>Event Grid 이벤트 스키마

### <a name="available-event-types"></a>사용할 수 있는 이벤트 유형

SignalR Service는 다음과 같은 이벤트 유형을 내보냅니다.

| 이벤트 유형 | 설명 |
| ---------- | ----------- |
| SignalRService 연결 됨 | 클라이언트 연결이 연결 될 때 발생 합니다. |
| SignalRService 연결 끊김 | 클라이언트 연결의 연결이 끊어지면 발생 합니다. |

### <a name="example-event"></a>예제 이벤트

다음 예제에서는 클라이언트 연결 연결 이벤트의 스키마를 보여 줍니다. 

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

클라이언트 연결 끊김 이벤트의 스키마는 다음과 유사 합니다. 

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/signalr-rg/providers/Microsoft.SignalRService/SignalR/signalr-resource",
  "subject": "/hub/chat",
  "eventType": "Microsoft.SignalRService.ClientConnectionDisconnected",
  "eventTime": "2019-06-10T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
  "data": {
    "timestamp": "2019-06-10T18:41:00.9584103Z",
    "hubName": "chat",
    "connectionId": "crH0uxVSvP61p5wkFY1x1A",
    "userId": "user-eymwyo23",
    "errorMessage": "Internal server error."
  },
  "dataVersion": "1.0",
  "metadataVersion": "1"
}]
```

### <a name="event-properties"></a>이벤트 속성

이벤트에는 다음과 같은 최상위 데이터가 있습니다.

| 속성 | 유형 | Description |
| -------- | ---- | ----------- |
| 토픽 | 문자열 | 이벤트 원본에 대한 전체 리소스 경로입니다. 이 필드는 쓸 수 없습니다. Event Grid는 이 값을 제공합니다. |
| subject | 문자열 | 게시자가 정의한 이벤트 주체의 경로입니다. |
| eventType | 문자열 | 이 이벤트 원본에 대해 등록된 이벤트 유형 중 하나입니다. |
| eventTime | 문자열 | 공급자의 UTC 시간을 기준으로 이벤트가 생성되는 시간입니다. |
| id | 문자열 | 이벤트에 대한 고유 식별자입니다. |
| 데이터 | object | SignalR Service 이벤트 데이터입니다. |
| dataVersion | 문자열 | 데이터 개체의 스키마 버전입니다. 게시자가 스키마 버전을 정의합니다. |
| metadataVersion | 문자열 | 이벤트 메타데이터의 스키마 버전입니다. Event Grid는 최상위 속성의 스키마를 정의합니다. Event Grid는 이 값을 제공합니다. |

데이터 개체의 속성은 다음과 같습니다.

| 속성 | 유형 | Description |
| -------- | ---- | ----------- |
| timestamp | 문자열 | 공급자의 UTC 시간을 기준으로 이벤트가 생성되는 시간입니다. |
| hubName | 문자열 | 클라이언트 연결이 속한 허브입니다. |
| connectionId | 문자열 | 클라이언트 연결에 대 한 고유 식별자입니다. |
| userId | 문자열 | 클레임에 정의 된 사용자 id입니다. |
| errorMessage | 문자열 | 연결을 끊을 때 발생 하는 오류입니다. |

## <a name="tutorials-and-how-tos"></a>자습서 및 방법
|제목 | 설명 |
|---------|---------|
| [Event Grid를 사용 하 여 Azure SignalR Service 이벤트에 대응](../azure-signalr/signalr-concept-event-grid-integration.md) | Azure SignalR Service를 Event Grid와 통합 하는 방법에 대 한 개요입니다. |
| [Event Grid에 Azure SignalR Service 이벤트를 보내는 방법](../azure-signalr/signalr-howto-event-grid-integration.md) | Event Grid를 통해 Azure SignalR Service 이벤트를 응용 프로그램에 보내는 방법을 보여 줍니다. |

## <a name="next-steps"></a>다음 단계

* Azure Event Grid에 대한 소개는 [Event Grid란?](overview.md)을 참조하세요.
* Azure Event Grid 구독을 만드는 방법에 대한 자세한 내용은 [Event Grid 구독 스키마](subscription-creation-schema.md)를 참조하세요.
