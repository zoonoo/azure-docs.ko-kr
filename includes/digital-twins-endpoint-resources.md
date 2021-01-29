---
author: baanders
description: Azure Digital Twins 끝점을 만드는 데 필요한 리소스에 대 한 파일 포함
ms.service: digital-twins
ms.topic: include
ms.date: 1/26/2021
ms.author: baanders
ms.openlocfilehash: 58c90bae3dea0f3a47489ea7d8de6a79f823dcab
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/28/2021
ms.locfileid: "99054513"
---
### <a name="prerequisite-create-endpoint-resources"></a>필수 조건: 끝점 리소스 만들기

끝점을 Azure Digital Twins에 연결 하려면 끝점에 사용 중인 event grid 토픽, event hub 또는 Service Bus 항목이 이미 존재 해야 합니다.

끝점을 만들기 전에 다음 차트를 사용 하 여 설정 해야 하는 리소스를 확인할 수 있습니다.

| 엔드포인트 유형 | 필수 리소스 (생성 지침에 연결 됨) |
| --- | --- |
| Event Grid 끝점 | [event grid 항목](../articles/event-grid/custom-event-quickstart-portal.md#create-a-custom-topic) |
| Event Hubs 끝점 | [Event &nbsp; Hubs &nbsp; 네임 스페이스](../articles/event-hubs/event-hubs-create.md)<br/><br/>[이벤트 허브](../articles/event-hubs/event-hubs-create.md)<br/><br/>필드 키 기반 인증에 대 한 [권한 부여 규칙](../articles/event-hubs/authorize-access-shared-access-signature.md) | 
| 서비스 버스 엔드포인트 | [Service Bus 네임스페이스](../articles/service-bus-messaging/service-bus-quickstart-topics-subscriptions-portal.md)<br/><br/>[Service Bus 토픽](../articles/service-bus-messaging/service-bus-quickstart-topics-subscriptions-portal.md)<br/><br/> 필드 키 기반 인증에 대 한 [권한 부여 규칙](../articles/service-bus-messaging/service-bus-authentication-and-authorization.md#shared-access-signature)|
