<properties
pageTitle="논리 앱에서 Azure 서비스 버스 커넥터 사용 방법 알아보기 | Microsoft Azure"
description="Azure 앱 서비스로 논리 앱을 만듭니다. Azure 서비스 버스에 연결하여 메시지를 보내고 받습니다. 큐에 보내기, 항목에 보내기, 큐에서 수신, 구독에서 수신 등의 작업을 수행할 수 있습니다."
services="logic-apps"	
documentationCenter=".net,nodejs,java" 	
authors="msftman"	
manager="erikre"	
editor=""
tags="connectors" />

<tags
ms.service="logic-apps"
ms.devlang="multiple"
ms.topic="article"
ms.tgt_pltfrm="na"
ms.workload="integration"
ms.date="08/02/2016"
ms.author="deonhe"/>

# Azure 서비스 버스 커넥터 시작

Azure 서비스 버스에 연결하여 메시지를 보내고 받습니다. 큐에 보내기, 항목에 보내기, 큐에서 수신, 구독에서 수신 등의 작업을 수행할 수 있습니다.

[커넥터](./apis-list.md)를 사용하려면 먼저 논리 앱을 만들어야 합니다. [지금 논리 앱을 만들어](../app-service-logic/app-service-logic-create-a-logic-app.md) 시작할 수 있습니다.

## Azure 서비스 버스에 연결

논리 앱에서 서비스에 액세스하려면 먼저 서비스에 대한 *연결*을 만들어야 합니다. [연결](./connectors-overview.md)은 논리 앱과 다른 서비스 간의 연결을 제공합니다.

### Azure 서비스 버스에 대한 연결 만들기

>[AZURE.INCLUDE [Azure 서비스 버스에 대한 연결을 만드는 단계](../../includes/connectors-create-api-servicebus.md)]

## Azure 서비스 버스 트리거 사용

트리거는 논리 앱에 정의된 워크플로를 시작하는 데 사용할 수 있는 이벤트입니다. [트리거에 대해 자세히 알아보세요.](../app-service-logic/app-service-logic-what-are-logic-apps.md#logic-app-concepts)

>[AZURE.INCLUDE [서비스 버스 트리거를 만드는 단계](../../includes/connectors-create-api-servicebus-trigger.md)]

## Azure 서비스 버스 작업 사용

작업은 논리 앱에 정의된 워크플로에 의해 수행되는 작업입니다. [작업에 대해 자세히 알아보세요.](../app-service-logic/app-service-logic-what-are-logic-apps.md#logic-app-concepts)

[AZURE.INCLUDE [서비스 버스 작업을 만드는 단계](../../includes/connectors-create-api-servicebus-action.md)]

## 기술 세부 정보

이 연결에서 지원하는 트리거, 작업 및 응답에 대한 세부 정보는 다음과 같습니다.

## Azure 서비스 버스 트리거

Azure 서비스 버스에는 다음과 같은 트리거가 있습니다.

|트리거 | 설명|
|--- | ---|
|[큐에 메시지가 수신될 때](connectors-create-api-servicebus.md#when-a-message-is-received-in-a-queue)|이 작업은 큐에 메시지가 수신될 때 흐름을 트리거합니다.|
|[항목 구독에 메시지가 수신될 때](connectors-create-api-servicebus.md#when-a-message-is-received-in-a-topic-subscription)|이 작업은 항목 구독에 메시지가 수신될 때 흐름을 트리거합니다.|


## Azure 서비스 버스 작업

Azure 서비스 버스에는 다음과 같은 작업이 있습니다.


|작업|설명|
|--- | ---|
|[메시지 보내기](connectors-create-api-servicebus.md#send-message)|이 작업은 큐 또는 항목에 메시지를 보냅니다.|
### 작업 세부 정보

이 커넥터에 대한 작업 및 트리거 세부 정보와 해당 응답은 다음과 같습니다.



### 메시지 보내기
이 작업은 큐 또는 항목에 메시지를 보냅니다.


|속성 이름| 표시 이름|설명|
| ---|---|---|
|ContentData*|Content|메시지의 콘텐츠|
|ContentType|콘텐츠 형식|메시지 콘텐츠의 콘텐츠 형식|
|속성|속성|각 조정된 속성의 키-값 쌍|
|entityName*|큐/항목 이름|큐 또는 항목의 이름입니다.|

이 고급 매개 변수를 사용할 수도 있습니다.

|속성 이름| 표시 이름|설명|
| ---|---|---|
|MessageId|메시지 ID|서비스 버스에서 중복 메시지를 식별하는 데 사용할 수 있는 사용자 정의 값입니다(설정된 경우).|
|받는 사람  
|받는 사람  
|주소로 보내기|
|ReplyTo|회신|회신할 큐의 주소|
|ReplyToSessionId|세션 ID에 회신|회신할 세션의 식별자|
|레이블|레이블|응용 프로그램 특정 레이블|
|ScheduledEnqueueTimeUtc|ScheduledEnqueueTimeUtc|큐에 메시지가 추가될 때 날짜 및 시간(UTC)|
|SessionId|세션 ID|세션의 식별자|
|CorrelationId|상관관계 ID|상관 관계의 식별자|
|TimeToLive|TTL(Time to live)|메시지가 유효한 기간(틱)입니다. 이 기간은 메시지를 서비스 버스로 보낼 때부터 시작됩니다.|



*는 필수 속성을 나타냅니다.




### 큐에 메시지가 수신될 때
이 작업은 큐에 메시지가 수신될 때 흐름을 트리거합니다.


|속성 이름| 표시 이름|설명|
| ---|---|---|
|queueName*|큐 이름|큐의 이름|


*는 필수 속성을 나타냅니다.

#### 출력 세부 정보

ServiceBusMessage: 이 개체는 서비스 버스 메시지의 콘텐츠 및 속성을 포함합니다.


| 속성 이름 | 데이터 형식 | 설명 |
|---|---|---|
|ContentData|string|메시지의 콘텐츠|
|ContentType|string|메시지 콘텐츠의 콘텐츠 형식|
|속성|object|각 조정된 속성의 키-값 쌍|
|MessageId|string|서비스 버스에서 중복 메시지를 식별하는 데 사용할 수 있는 사용자 정의 값입니다(설정된 경우).|
|받는 사람  
|string|주소로 보내기|
|ReplyTo|string|회신할 큐의 주소|
|ReplyToSessionId|string|회신할 세션의 식별자|
|레이블|string|응용 프로그램 특정 레이블|
|ScheduledEnqueueTimeUtc|string|큐에 메시지가 추가될 때 날짜 및 시간(UTC)|
|SessionId|string|세션의 식별자|
|CorrelationId|string|상관 관계의 식별자|
|TimeToLive|string|메시지가 유효한 기간(틱)입니다. 이 기간은 메시지를 서비스 버스로 보낼 때부터 시작됩니다.|




### 항목 구독에 메시지가 수신될 때
이 작업은 항목 구독에 메시지가 수신될 때 흐름을 트리거합니다.


|속성 이름| 표시 이름|설명|
| ---|---|---|
|topicName*|항목 이름|항목의 이름|
|subscriptionName*|항목 구독 이름|항목 구독의 이름|


*는 필수 속성을 나타냅니다.

#### 출력 세부 정보

ServiceBusMessage: 이 개체는 서비스 버스 메시지의 콘텐츠 및 속성을 포함합니다.


| 속성 이름 | 데이터 형식 | 설명 |
|---|---|---|
|ContentData|string|메시지의 콘텐츠|
|ContentType|string|메시지 콘텐츠의 콘텐츠 형식|
|속성|object|각 조정된 속성의 키-값 쌍|
|MessageId|string|서비스 버스에서 중복 메시지를 식별하는 데 사용할 수 있는 사용자 정의 값입니다(설정된 경우).|
|받는 사람  
|string|주소로 보내기|
|ReplyTo|string|회신할 큐의 주소|
|ReplyToSessionId|string|회신할 세션의 식별자|
|레이블|string|응용 프로그램 특정 레이블|
|ScheduledEnqueueTimeUtc|string|큐에 메시지가 추가될 때 날짜 및 시간(UTC)|
|SessionId|string|세션의 식별자|
|CorrelationId|string|상관 관계의 식별자|
|TimeToLive|string|메시지가 유효한 기간(틱)입니다. 이 기간은 메시지를 서비스 버스로 보낼 때부터 시작됩니다.|



## HTTP 응답

위의 작업 및 트리거는 다음 HTTP 상태 코드 중 하나 이상을 반환할 수 있습니다.

|이름|설명|
|---|---|
|200|확인|
|202|수락됨|
|400|잘못된 요청|
|401|권한 없음|
|403|사용할 수 없음|
|404|찾을 수 없음|
|500|내부 서버 오류. 알 수 없는 오류 발생.|
|기본값|작업이 실패했습니다.|

## 다음 단계
[논리 앱 만들기](../app-service-logic/app-service-logic-create-a-logic-app.md)

<!---HONumber=AcomDC_0803_2016-->