---
author: baanders
description: Azure Digital Twins 엔드포인트를 만드는 데 필요한 리소스에 대한 파일 포함
ms.service: digital-twins
ms.topic: include
ms.date: 1/26/2021
ms.author: baanders
ms.openlocfilehash: 58c90bae3dea0f3a47489ea7d8de6a79f823dcab
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "99054513"
---
### <a name="prerequisite-create-endpoint-resources"></a>필수 구성 요소: 엔드포인트 리소스 만들기

엔드포인트를 Azure Digital Twins에 연결하려면 엔드포인트에 사용 중인 이벤트 그리드 토픽, 이벤트 허브 또는 Service Bus 토픽이 이미 있어야 합니다.

다음 차트를 사용하여 엔드포인트를 만들기 전에 설정해야 하는 리소스를 확인합니다.

| 엔드포인트 유형 | 필수 리소스(생성 지침에 연결됨) |
| --- | --- |
| Event Grid 엔드포인트 | [이벤트 그리드 토픽](../articles/event-grid/custom-event-quickstart-portal.md#create-a-custom-topic) |
| Event Hubs 엔드포인트 | [Event&nbsp;Hubs&nbsp;네임스페이스](../articles/event-hubs/event-hubs-create.md)<br/><br/>[이벤트 허브](../articles/event-hubs/event-hubs-create.md)<br/><br/>(선택 사항) 키 기반 인증에 대한 [권한 부여 규칙](../articles/event-hubs/authorize-access-shared-access-signature.md) | 
| 서비스 버스 엔드포인트 | [Service Bus 네임스페이스](../articles/service-bus-messaging/service-bus-quickstart-topics-subscriptions-portal.md)<br/><br/>[Service Bus 토픽](../articles/service-bus-messaging/service-bus-quickstart-topics-subscriptions-portal.md)<br/><br/> (선택 사항) 키 기반 인증에 대한 [권한 부여 규칙](../articles/service-bus-messaging/service-bus-authentication-and-authorization.md#shared-access-signature)|
