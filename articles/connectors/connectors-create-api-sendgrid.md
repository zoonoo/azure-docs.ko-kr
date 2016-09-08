<properties
pageTitle="SendGrid | Microsoft Azure"
description="Azure 앱 서비스로 논리 앱을 만듭니다. SendGrid 연결 공급자를 통해 전자 메일을 보내고 받는 사람 목록을 관리할 수 있습니다."
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
ms.date="08/18/2016"
ms.author="deonhe"/>

# SendGrid 커넥터 시작

SendGrid 연결 공급자를 통해 전자 메일을 보내고 받는 사람 목록을 관리할 수 있습니다.

>[AZURE.NOTE] 이 버전의 문서는 논리 앱 2015-08-01-preview 스키마 버전에 적용됩니다.

이제 논리 앱을 만들어 시작할 수 있습니다. [논리 앱 만들기](../app-service-logic/app-service-logic-create-a-logic-app.md)를 참조하세요.

## 트리거 및 작업

SendGrid 커넥터는 작업으로 사용할 수 있으며 트리거를 가지고 있습니다. 모든 커넥터는 JSON 및 XML 형식의 데이터를 지원합니다.

 SendGrid 커넥터에서는 다음과 같은 작업을 사용할 수 있습니다. 트리거는 없습니다.

### SendGrid 작업
다음 작업을 수행할 수 있습니다.

|작업|설명|
|--- | ---|
|[SendEmail](connectors-create-api-sendgrid.md#sendemail)|SendGrid API(받는 사람은 10,000명으로 제한)를 사용하여 전자 메일 보내기|
|[AddRecipientToList](connectors-create-api-sendgrid.md#addrecipienttolist)|받는 사람 목록에 개별 받는 사람을 추가합니다.|


## SendGrid에 대한 연결 만들기
SendGrid로 논리 앱을 만들려면 먼저 **연결**을 만든 후에 다음 속성에 대한 세부 정보를 제공해야 합니다.

|속성| 필수|설명|
| ---|---|---|
|ApiKey|예|SendGrid API 키 제공|
 

>[AZURE.INCLUDE [SendGrid에 대한 연결을 만드는 단계](../../includes/connectors-create-api-sendgrid.md)]

>[AZURE.TIP] 다른 논리 앱에서 이 연결을 사용할 수 있습니다.

연결을 만든 후에 사용하여 작업을 실행하고 이 문서에 설명된 트리거에 대한 수신을 대기할 수 있습니다.

## SendGrid에 대한 참조
적용 버전: 1.0

## SendEmail
전자 메일 보내기: SendGrid API(받는 사람은 10,000명으로 제한)를 사용하여 전자 메일 보내기

```POST: /api/mail.send.json```

| Name| 데이터 형식|필수|위치|기본값|설명|
| ---|---|---|---|---|---|
|request| |yes|body|없음|보낼 전자 메일 메시지|

#### 응답

|Name|설명|
|---|---|
|200|확인|
|400|잘못된 요청|
|401|권한 없음|
|403|사용할 수 없음|
|404|찾을 수 없음|
|429|너무 많은 요청|
|500|내부 서버 오류. 알 수 없는 오류 발생|
|기본값|작업이 실패했습니다.|


## AddRecipientToList
목록에 받는 사람 추가: 받는 사람 목록에 개별 받는 사람을 추가합니다.

```POST: /v3/contactdb/lists/{listId}/recipients/{recipientId}```

| 이름| 데이터 형식|필수|위치|기본값|설명|
| ---|---|---|---|---|---|
|listId|string|yes|path|없음|받는 사람 목록의 고유 ID|
|recipientId|string|yes|path|없음|받는 사람의 고유 ID|

#### 응답

|이름|설명|
|---|---|
|200|확인|
|400|잘못된 요청|
|401|권한 없음|
|403|사용할 수 없음|
|404|찾을 수 없음|
|500|내부 서버 오류. 알 수 없는 오류 발생|
|기본값|작업이 실패했습니다.|


## 개체 정의 

### EmailRequest


| 속성 이름 | 데이터 형식 | 필수 |
|---|---|---|
|from|string|예 |
|fromname|string|아니요 |
|to|string|예 |
|toname|string|아니요 |
|subject|string|예 |
|body|string|예 |
|ishtml|부울|아니요 |
|cc|string|아니요 |
|ccname|string|아니요 |
|bcc|string|아니요 |
|bccname|string|아니요 |
|replyto|string|아니요 |
|date|string|아니요 |
|headers|string|아니요 |
|업로드|array|아니요 |
|filenames|array|아니요 |



### EmailResponse


| 속성 이름 | 데이터 형식 | 필수 |
|---|---|---|
|message|string|아니요 |



### RecipientLists


| 속성 이름 | 데이터 형식 | 필수 |
|---|---|---|
|lists|array|아니요 |



### RecipientList


| 속성 이름 | 데이터 형식 | 필수 |
|---|---|---|
|id|정수|아니요 |
|name|string|아니요 |
|recipient\_count|정수|아니요 |



### 받는 사람


| 속성 이름 | 데이터 형식 | 필수 |
|---|---|---|
|recipients|array|아니요 |



### 받는 사람


| 속성 이름 | 데이터 형식 | 필수 |
|---|---|---|
|email|string|아니요 |
|last\_name|string|아니요 |
|first\_name|string|아니요 |
|id|string|아니요 |


## 다음 단계
[논리 앱 만들기](../app-service-logic/app-service-logic-create-a-logic-app.md)

<!---HONumber=AcomDC_0824_2016-->