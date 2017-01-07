---
title: "논리 앱에서 Azure 서비스 버스 커넥터 사용 방법 알아보기 | Microsoft Docs"
description: "Azure 앱 서비스로 논리 앱을 만듭니다. Azure 서비스 버스에 연결하여 메시지를 보내고 받습니다. 큐에 보내기, 항목에 보내기, 큐에서 수신, 구독에서 수신 등의 동작을 수행할 수 있습니다."
services: logic-apps
documentationcenter: .net,nodejs,java
author: msftman
manager: erikre
editor: 
tags: connectors
ms.assetid: d6d14f5f-2126-4e33-808e-41de08e6721f
ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 08/02/2016
ms.author: deonhe
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: a1ecedf9bf78e0a15908c1a03ca24d611f551875


---
# <a name="get-started-with-the-azure-service-bus-connector"></a>Azure 서비스 버스 커넥터 시작
Azure 서비스 버스에 연결하여 메시지를 보내고 받습니다. 큐에 보내기, 항목에 보내기, 큐에서 수신, 구독에서 수신 등의 동작을 수행할 수 있습니다.

[커넥터](apis-list.md)를 사용하려면 먼저 논리 앱을 만들어야 합니다. [지금 논리 앱을 만들어](../app-service-logic/app-service-logic-create-a-logic-app.md) 시작할 수 있습니다.

## <a name="connect-to-service-bus"></a>서비스 버스에 연결
논리 앱에서 서비스에 액세스하려면 먼저 서비스에 대한 연결을 만들어야 합니다. [연결](connectors-overview.md)은 논리 앱과 다른 서비스 간의 연결을 제공합니다.  

> [!INCLUDE [Steps to create a connection to Azure Service Bus](../../includes/connectors-create-api-servicebus.md)]
> 
> 

## <a name="use-a-service-bus-trigger"></a>서비스 버스 트리거 사용
트리거는 논리 앱에 정의된 워크플로를 시작하는 데 사용할 수 있는 이벤트입니다. [트리거에 대해 자세히 알아보세요.](../app-service-logic/app-service-logic-what-are-logic-apps.md#logic-app-concepts)  

> [!INCLUDE [Steps to create a Service Bus trigger](../../includes/connectors-create-api-servicebus-trigger.md)]
> 
> 

## <a name="use-a-service-bus-action"></a>서비스 버스 동작 사용
작업은 논리 앱에 정의된 워크플로에 의해 수행되는 작업입니다. [작업에 대해 자세히 알아봅니다.](../app-service-logic/app-service-logic-what-are-logic-apps.md#logic-app-concepts)

[!INCLUDE [Steps to create a Service Bus action](../../includes/connectors-create-api-servicebus-action.md)]

## <a name="technical-details"></a>기술 세부 정보
이 연결에서 지원하는 트리거, 동작 및 응답에 대한 세부 정보는 다음과 같습니다.

### <a name="service-bus-triggers"></a>서비스 버스 트리거
서비스 버스에는 다음과 같은 트리거가 있습니다.  

| 트리거 | 설명 |
| --- | --- |
| [큐에 메시지가 수신될 때](connectors-create-api-servicebus.md#when-a-message-is-received-in-a-queue) |이 작업은 큐에 메시지가 수신될 때 흐름을 트리거합니다. |
| [항목 구독에 메시지가 수신될 때](connectors-create-api-servicebus.md#when-a-message-is-received-in-a-topic-subscription) |이 작업은 항목 구독에 메시지가 수신될 때 흐름을 트리거합니다. |

### <a name="service-bus-actions"></a>서비스 버스 작업
서비스 버스에는 다음과 같은 작업이 있습니다.

| 작업 | 설명 |
| --- | --- |
| [메시지 보내기](connectors-create-api-servicebus.md#send-message) |이 작업은 큐 또는 항목에 메시지를 보냅니다. |

### <a name="action-and-trigger-details"></a>작업 및 트리거 세부 정보
이 커넥터에 대한 동작 및 트리거 세부 정보와 해당 응답은 다음과 같습니다.

#### <a name="send-message"></a>메시지 보내기
| 속성 이름 | 표시 이름 | 설명 |
| --- | --- | --- |
| ContentData* |Content |메시지의 콘텐츠입니다. |
| ContentType |콘텐츠 형식 |메시지 콘텐츠의 콘텐츠 형식입니다. |
| 속성 |속성 |각 조정된 속성의 키-값 쌍입니다. |
| entityName* |큐/항목 이름 |큐 또는 항목의 이름입니다. |

이 고급 매개 변수를 사용할 수도 있습니다.

| 속성 이름 | 표시 이름 | 설명 |
| --- | --- | --- |
| MessageId |메시지 ID |서비스 버스에서 중복 메시지를 식별하는 데 사용할 수 있는 사용자 정의 값입니다(설정된 경우). |
| 받는 사람 |받는 사람 |보낼 주소입니다. |
| ReplyTo |회신 |회신할 큐의 주소입니다. |
| ReplyToSessionId |세션 ID에 회신 |회신할 세션의 식별자입니다. |
| 레이블 |레이블 |응용 프로그램 관련 레이블입니다. |
| ScheduledEnqueueTimeUtc |ScheduledEnqueueTimeUtc |큐에 메시지가 추가될 날짜 및 시간(UTC)입니다. |
| SessionId |세션 ID |세션의 식별자입니다. |
| CorrelationId |상관관계 ID |상관 관계의 식별자입니다. |
| TimeToLive |TTL(Time to live) |메시지가 유효한 기간(틱)입니다. 이 기간은 메시지를 서비스 버스로 보낼 때부터 시작됩니다. |

*는 필수 속성을 나타냅니다.

#### <a name="when-a-message-is-received-in-a-queue"></a>큐에 메시지가 수신될 때
| 속성 이름 | 표시 이름 | 설명 |
| --- | --- | --- |
| queueName* |큐 이름 |큐의 이름입니다. |

*는 필수 속성을 나타냅니다.

##### <a name="output-details"></a>출력 세부 정보
ServiceBusMessage: 이 개체는 서비스 버스 메시지의 콘텐츠 및 속성을 포함합니다.

| 속성 이름 | 데이터 형식 | 설명 |
| --- | --- | --- |
| ContentData |string |메시지의 콘텐츠입니다. |
| ContentType |string |메시지 콘텐츠의 콘텐츠 형식입니다. |
| 속성 |object |각 조정된 속성의 키-값 쌍입니다. |
| MessageId |string |서비스 버스에서 중복 메시지를 식별하는 데 사용할 수 있는 사용자 정의 값입니다(설정된 경우). |
| 받는 사람 |string |주소로 보냅니다. |
| ReplyTo |string |회신할 큐의 주소입니다. |
| ReplyToSessionId |string |회신할 세션의 식별자입니다. |
| 레이블 |string |응용 프로그램 관련 레이블입니다. |
| ScheduledEnqueueTimeUtc |string |큐에 메시지가 추가될 날짜 및 시간(UTC)입니다. |
| SessionId |string |세션의 식별자입니다. |
| CorrelationId |string |상관 관계의 식별자입니다. |
| TimeToLive |string |메시지가 유효한 기간(틱)입니다. 이 기간은 메시지를 서비스 버스로 보낼 때부터 시작됩니다. |

#### <a name="when-a-message-is-received-in-a-topic-subscription"></a>항목 구독에 메시지가 수신될 때
| 속성 이름 | 표시 이름 | 설명 |
| --- | --- | --- |
| topicName* |항목 이름 |항목의 이름입니다. |
| subscriptionName* |항목 구독 이름 |항목 구독의 이름입니다. |

*는 필수 속성을 나타냅니다.

##### <a name="output-details"></a>출력 세부 정보
ServiceBusMessage: 이 개체는 서비스 버스 메시지의 콘텐츠 및 속성을 포함합니다.

| 속성 이름 | 데이터 형식 | 설명 |
| --- | --- | --- |
| ContentData |string |메시지의 콘텐츠입니다. |
| ContentType |string |메시지 콘텐츠의 콘텐츠 형식입니다. |
| 속성 |object |각 조정된 속성의 키-값 쌍입니다. |
| MessageId |string |서비스 버스에서 중복 메시지를 식별하는 데 사용할 수 있는 사용자 정의 값입니다(설정된 경우). |
| 받는 사람 |string |주소로 보냅니다. |
| ReplyTo |string |회신할 큐의 주소입니다. |
| ReplyToSessionId |string |회신할 세션의 식별자입니다. |
| 레이블 |string |응용 프로그램 관련 레이블입니다. |
| ScheduledEnqueueTimeUtc |string |큐에 메시지가 추가될 날짜 및 시간(UTC)입니다. |
| SessionId |string |세션의 식별자입니다. |
| CorrelationId |string |상관 관계의 식별자입니다. |
| TimeToLive |string |메시지가 유효한 기간(틱)입니다. 이 기간은 메시지를 서비스 버스로 보낼 때부터 시작됩니다. |

### <a name="http-responses"></a>HTTP 응답
위의 동작 및 트리거는 다음 HTTP 상태 코드 중 하나 이상을 반환할 수 있습니다.

| Name | 설명 |
| --- | --- |
| 200 |확인 |
| 202 |수락됨 |
| 400 |잘못된 요청 |
| 401 |권한 없음 |
| 403 |사용할 수 없음 |
| 404 |찾을 수 없음 |
| 500 |내부 서버 오류. 알 수 없는 오류 발생. |
| 기본값 |작업이 실패했습니다. |

## <a name="next-steps"></a>다음 단계
[논리 앱 만들기](../app-service-logic/app-service-logic-create-a-logic-app.md)




<!--HONumber=Nov16_HO3-->


