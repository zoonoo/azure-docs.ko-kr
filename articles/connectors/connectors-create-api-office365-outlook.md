<properties
    pageTitle="PowerApps 엔터프라이즈 또는 논리 앱에 Office 365 Outlook 커넥터 추가 | Microsoft Azure"
    description="REST API 매개 변수를 사용하는 Office 365 Outlook 커넥터 개요"
    services=""    
    documentationCenter=""     
    authors="msftman"    
    manager="erikre"    
    editor="" 
    tags="connectors" />

<tags
ms.service="multiple"
ms.devlang="na"
ms.topic="article"
ms.tgt_pltfrm="na"
ms.workload="integration"
ms.date="05/18/2016"
ms.author="mandia"/>

# Office 365 Outlook 커넥터 시작 

Office 365 Outlook에 연결하여 전자 메일 받기, 전자 메일 회신, 일정 및 연락처 업데이트 등을 수행할 수 있습니다. Office 365 Outlook 커넥터는 다음에서 사용할 수 있습니다.

- 논리 앱 
- PowerApps

> [AZURE.SELECTOR]
- [논리 앱](../articles/connectors/connectors-create-api-office365-outlook.md)
- [PowerApps 엔터프라이즈](../articles/power-apps/powerapps-create-api-office365-outlook.md)

&nbsp;

>[AZURE.NOTE] 이 버전의 문서는 논리 앱 2015-08-01-preview 스키마 버전에 적용됩니다.

Office 365 Outlook을 사용하면 다음을 수행할 수 있습니다.

- Office 365 Outlook에서 가져온 데이터를 기반으로 비즈니스 흐름을 빌드합니다. 
- 새 전자 메일이 있거나 새 연락처를 만든 경우 등에 트리거를 사용합니다.
- 전자 메일에 회신하는 동작을 사용하고 새 일정 이벤트를 만드는 등의 작업을 수행합니다. 이러한 작업을 사용하여 응답을 가져오고 출력을 다른 작업에 사용할 수 있도록 설정합니다. 예를 들어 Salesforce에 새 개체가 있는 경우 이 개체를 가져와 Office 365 Outlook 연락처를 업데이트할 수 있습니다. 
- PowerApps 엔터프라이즈에 Office 365 Outlook 커넥터 추가 이렇게 하면 사용자가 앱 내에서 이 커넥터를 사용할 수 있습니다. 

PowerApps 엔터프라이즈에서 커넥터를 추가하는 방법을 보려면 [PowerApps에서 커넥터 등록](../power-apps/powerapps-register-from-available-apis.md)으로 이동하세요.

논리 앱에 작업을 추가하려면 [논리 앱 만들기](../app-service-logic/app-service-logic-create-a-logic-app.md)를 참조하세요.

## 트리거 및 작업

Office 365 Outlook 커넥터에서 사용할 수 있는 트리거와 작업은 다음과 같습니다.

| 트리거 | actions|
| --- | --- |
|<ul><li>곧 시작되는 이벤트</li><li>새 메일</li><li>새 항목</li><li>업데이트된 항목</li></ul>| <ul><li>연락처 만들기</li><li>이벤트 만들기</li><li>승인 메일 보내기</li><li>메일 보내기</li><li>연락처 삭제</li><li>메일 삭제</li><li>이벤트 삭제</li><li>첨부 파일 가져오기</li><li>일정 가져오기</li><li>연락처 가져오기</li><li>연락처 폴더 가져오기</li><li>연락처 가져오기</li><li>메일 가져오기</li><li>이벤트 가져오기</li><li>이벤트 가져오기</li><li>읽은 상태로 표시</li><li>이벤트 곧 시작</li><li>새 메일</li><li>새 항목</li><li>업데이트된 항목</li><li>메시지에 회신</li><li>옵션과 함께 메일 보내기</li><li>연락처 업데이트</li><li>이벤트 업데이트</li></ul> |

모든 커넥터는 JSON 및 XML 형식의 데이터를 지원합니다.


## Office365에 대한 연결 만들기

논리 앱에 이 커넥터를 추가할 때 Office 365 Outlook 계정에 로그인하고 논리 앱을 계정에 연결해야 합니다.

1. Office 365 Outlook 계정에 로그인합니다.
2. 논리 앱에서 Office 365 계정에 연결하고 사용할 수 있도록 허용합니다. 

연결을 만든 후에 받은 편지함 폴더 경로 또는 전자 메일 메시지 등의 Office 365 Outlook 속성을 입력합니다. 이 항목의 **REST API 참조**에서는 이러한 속성에 대해 설명합니다.

>[AZURE.TIP] 다른 논리 앱에서 이와 동일한 Office 365 Outlook 연결을 사용할 수 있습니다.

## Swagger REST API 참조
적용 버전: 1.0


### 곧 시작되는 이벤트 
예정된 일정 이벤트를 시작할 때 흐름을 트리거합니다. ```GET: /Events/OnUpcomingEvents```

| 이름| 데이터 형식|필수|위치|기본값|설명|
| ---|---|---|---|---|---|
|테이블|string|yes|쿼리|없음|일정의 고유 식별자|
|lookAheadTimeInMinutes|정수|no|쿼리|15|예정된 이벤트에 대해 조회할 시간(분)|

#### 응답
|이름|설명|
|---|---|
|200|작업이 성공했습니다.|
|202|작업이 성공했습니다.|
|400|BadRequest|
|401|권한 없음|
|403|사용할 수 없음|
|500|내부 서버 오류|
|기본값|작업이 실패했습니다.|


### 전자 메일 가져오기 
폴더에서 메일을 검색합니다. ```GET: /Mail```

| 이름| 데이터 형식|필수|위치|기본값|설명|
| ---|---|---|---|---|---|
|folderPath|string|no|쿼리|받은 편지함|메시지를 검색하는 폴더의 경로(기본값: '받은 편지함')|
|top|정수|no|쿼리|10|검색할 전자 메일의 수(기본값: 10)|
|fetchOnlyUnread|부울|no|쿼리|true|읽지 않은 메시지만 검색할 수 있습니까?|
|includeAttachments|부울|no|쿼리|false|true로 설정된 경우 첨부 파일도 전자 메일 메시지와 함께 검색됩니다.|
|searchQuery|string|no|쿼리|없음|전자 메일을 필터링하려는 검색 쿼리|
|skip|정수|no|쿼리|0|건너뛸 전자 메일의 수(기본값: 0)|
|skipToken|string|no|쿼리|없음|새 페이지를 가져오는 토큰 건너뛰기|

#### 응답

|이름|설명|
|---|---|
|200|작업이 성공했습니다.|
|400|BadRequest|
|401|권한 없음|
|403|사용할 수 없음|
|500|내부 서버 오류|
|기본값|작업이 실패했습니다.|


### 전자 메일 보내기 
메일 메시지를 전송합니다. ```POST: /Mail```

| 이름| 데이터 형식|필수|위치|기본값|설명|
| ---|---|---|---|---|---|
|emailMessage| |yes|body|없음|전자 메일 메시지 인스턴스|


#### 응답

|이름|설명|
|---|---|
|200|작업이 성공했습니다.|
|400|BadRequest|
|401|권한 없음|
|403|사용할 수 없음|
|500|내부 서버 오류|
|기본값|작업이 실패했습니다.|


### 전자 메일 삭제 
ID로 메일 메시지를 삭제합니다. ```DELETE: /Mail/{messageId}```

| 이름| 데이터 형식|필수|위치|기본값|설명|
| ---|---|---|---|---|---|
|messageId|string|yes|path|없음|삭제할 메시지의 id입니다.|

#### 응답

|이름|설명|
|---|---|
|200|작업이 성공했습니다.|
|400|BadRequest|
|401|권한 없음|
|403|사용할 수 없음|
|500|내부 서버 오류|
|기본값|작업이 실패했습니다.|


### 읽은 상태로 표시 
메일 메시지를 읽은 것으로 표시합니다. ```POST: /Mail/MarkAsRead/{messageId}```

| 이름| 데이터 형식|필수|위치|기본값|설명|
| ---|---|---|---|---|---|
|messageId|string|yes|path|없음|읽은 상태로 표시될 메시지의 id|

#### 응답

|이름|설명|
|---|---|
|200|작업이 성공했습니다.|
|400|BadRequest|
|401|권한 없음|
|403|사용할 수 없음|
|500|내부 서버 오류|
|기본값|작업이 실패했습니다.|


### 메시지에 회신 
메일 메시지에 회신합니다. ```POST: /Mail/ReplyTo/{messageId}```

| 이름| 데이터 형식|필수|위치|기본값|설명|
| ---|---|---|---|---|---|
|messageId|string|yes|path|없음|회신할 메시지의 id|
|주석|string|yes|쿼리|없음|회신 주석|
|replyAll|부울|no|쿼리|false|모든 받는 사람에게 회신|

#### 응답

|이름|설명|
|---|---|
|200|작업이 성공했습니다.|
|400|BadRequest|
|401|권한 없음|
|403|사용할 수 없음|
|500|내부 서버 오류|
|기본값|작업이 실패했습니다.|


### 첨부 파일 가져오기 
ID별로 메시지 첨부 파일을 검색합니다. ```GET: /Mail/{messageId}/Attachments/{attachmentId}```

| 이름| 데이터 형식|필수|위치|기본값|설명|
| ---|---|---|---|---|---|
|messageId|string|yes|path|없음|메시지의 id|
|attachmentId|string|yes|path|없음|다운로드할 첨부 파일의 id|

#### 응답

|이름|설명|
|---|---|
|200|작업이 성공했습니다.|
|400|BadRequest|
|401|권한 없음|
|403|사용할 수 없음|
|500|내부 서버 오류|
|기본값|작업이 실패했습니다.|


### 새 전자 메일에 
새 메일이 도착했을 때 흐름을 트리거합니다. ```GET: /Mail/OnNewEmail```

| 이름| 데이터 형식|필수|위치|기본값|설명|
| ---|---|---|---|---|---|
|folderPath|string|no|쿼리|받은 편지함|검색할 전자 메일 폴더(기본값: 받은 편지함)|
|to|string|no|쿼리|없음|받는 사람 전자 메일 주소|
|from|string|no|쿼리|없음|보낸 사람 주소|
|중요도|string|no|쿼리|정상|전자 메일의 중요도(높음, 보통, 낮음) (기본값: 보통)|
|fetchOnlyWithAttachment|부울|no|쿼리|false|첨부 파일이 있는 전자 메일만 검색|
|includeAttachments|부울|no|쿼리|false|첨부 파일 포함|
|subjectFilter|string|no|쿼리|없음|제목에서 찾을 문자열|

#### 응답

|이름|설명|
|---|---|
|200|작업이 성공했습니다.|
|202|수락됨|
|400|BadRequest|
|401|권한 없음|
|403|사용할 수 없음|
|500|내부 서버 오류|
|기본값|작업이 실패했습니다.|


### 옵션을 사용하여 메일 전송 
여러 옵션이 포함된 메일을 보내고 받는 사람이 옵션 중 하나를 사용하여 다시 응답하기를 기다립니다. ```POST: /mailwithoptions/$subscriptions```

| 이름| 데이터 형식|필수|위치|기본값|설명|
| ---|---|---|---|---|---|
|optionsEmailSubscription| |yes|body|없음|옵션에 대한 구독 요청 전자 메일|

#### 응답

|이름|설명|
|---|---|
|200|확인|
|201|구독 만들어짐|
|400|BadRequest|
|401|권한 없음|
|403|사용할 수 없음|
|500|내부 서버 오류|
|기본값|작업이 실패했습니다.|


### 승인 전자 메일 보내기 
승인 메일을 보내고 받는 사람으로부터 응답을 기다립니다. ```POST: /approvalmail/$subscriptions```

| 이름| 데이터 형식|필수|위치|기본값|설명|
| ---|---|---|---|---|---|
|approvalEmailSubscription| |yes|body|없음|승인 전자 메일에 대한 구독 요청.|


#### 응답

|이름|설명|
|---|---|
|200|확인|
|201|구독 만들어짐|
|400|BadRequest|
|401|권한 없음|
|403|사용할 수 없음|
|500|내부 서버 오류|
|기본값|작업이 실패했습니다.|




### 일정 가져오기 
일정을 검색합니다. ```GET: /datasets/calendars/tables```

이 호출에 대한 매개 변수는 없습니다.

#### 응답

|이름|설명|
|---|---|
|200|확인|
|기본값|작업이 실패했습니다.|




### 이벤트 가져오기 
일정에서 항목을 검색합니다. ```GET: /datasets/calendars/tables/{table}/items```

| 이름| 데이터 형식|필수|위치|기본값|설명|
| ---|---|---|---|---|---|
|테이블|string|yes|path|없음|검색할 일정의 고유 식별자|
|$skip|정수|no|쿼리|없음|건너뛸 항목의 수(기본값 = 0)|
|$top|정수|no|쿼리|없음|검색할 항목의 최대 수(기본값 = 256)|
|$filter|string|no|쿼리|없음|항목의 수를 제한할 ODATA 필터 쿼리|
|$orderby|string|no|쿼리|없음|항목의 순서를 지정하는 ODATA orderBy 쿼리|

#### 응답

|이름|설명|
|---|---|
|200|확인|
|기본값|작업이 실패했습니다.|


### 이벤트 만들기 
새 이벤트를 만듭니다. ```POST: /datasets/calendars/tables/{table}/items```

| 이름| 데이터 형식|필수|위치|기본값|설명|
| ---|---|---|---|---|---|
|테이블|string|yes|path|없음|일정의 고유 식별자|
|항목| |yes|body|없음|만들 일정 항목|

#### 응답

|이름|설명|
|---|---|
|200|확인|
|기본값|작업이 실패했습니다.|


### 이벤트 가져오기 
일정에서 특정 항목을 검색합니다. ```GET: /datasets/calendars/tables/{table}/items/{id}```

| 이름| 데이터 형식|필수|위치|기본값|설명|
| ---|---|---|---|---|---|
|테이블|string|yes|path|없음|일정의 고유 식별자|
|id|string|yes|path|없음|검색할 일정 항목의 고유 식별자|

#### 응답

|이름|설명|
|---|---|
|200|확인|
|기본값|작업이 실패했습니다.|


### 이벤트 삭제 
일정 항목을 삭제합니다. ```DELETE: /datasets/calendars/tables/{table}/items/{id}```

| 이름| 데이터 형식|필수|위치|기본값|설명|
| ---|---|---|---|---|---|
|테이블|string|yes|path|없음|일정의 고유 식별자.|
|id|string|yes|path|없음|삭제할 일정 항목의 고유 식별자|

#### 응답

|이름|설명|
|---|---|
|200|확인|
|기본값|작업이 실패했습니다.|


### 이벤트 업데이트 
일정 항목을 부분적으로 업데이트합니다. ```PATCH: /datasets/calendars/tables/{table}/items/{id}```

| 이름| 데이터 형식|필수|위치|기본값|설명|
| ---|---|---|---|---|---|
|테이블|string|yes|path|없음|일정의 고유 식별자|
|id|string|yes|path|없음|업데이트할 일정 항목의 고유 식별자|
|항목| |yes|body|없음|업데이트할 일정 항목|

#### 응답

|이름|설명|
|---|---|
|200|확인|
|기본값|작업이 실패했습니다.|


### 새 항목에 
새 일정 항목이 만들어질 때 트리거됩니다. ```GET: /datasets/calendars/tables/{table}/onnewitems```

| 이름| 데이터 형식|필수|위치|기본값|설명|
| ---|---|---|---|---|---|
|테이블|string|yes|path|없음|일정의 고유 식별자|
|$skip|정수|no|쿼리|없음|건너뛸 항목의 수(기본값 = 0)|
|$top|정수|no|쿼리|없음|검색할 항목의 최대 수(기본값 = 256)|
|$filter|string|no|쿼리|없음|항목의 수를 제한할 ODATA 필터 쿼리|
|$orderby|string|no|쿼리|없음|항목의 순서를 지정하는 ODATA orderBy 쿼리|

#### 응답

|이름|설명|
|---|---|
|200|확인|
|기본값|작업이 실패했습니다.|


### 업데이트된 항목에 
일정 항목이 수정될 때 트리거됩니다. ```GET: /datasets/calendars/tables/{table}/onupdateditems```

| 이름| 데이터 형식|필수|위치|기본값|설명|
| ---|---|---|---|---|---|
|테이블|string|yes|path|없음|일정의 고유 식별자|
|$skip|정수|no|쿼리|없음|건너뛸 항목의 수(기본값 = 0)|
|$top|정수|no|쿼리|없음|검색할 항목의 최대 수(기본값 = 256)|
|$filter|string|no|쿼리|없음|항목의 수를 제한할 ODATA 필터 쿼리|
|$orderby|string|no|쿼리|없음|항목의 순서를 지정하는 ODATA orderBy 쿼리|

#### 응답

|이름|설명|
|---|---|
|200|확인|
|기본값|작업이 실패했습니다.|


### 연락처 폴더 가져오기 
연락처 폴더 검색 ```GET: /datasets/contacts/tables```

이 호출에 대한 매개 변수는 없습니다.

#### 응답

|이름|설명|
|---|---|
|200|확인|
|기본값|작업이 실패했습니다.|


### 연락처 가져오기 
연락처 폴더에서 연락처 검색 ```GET: /datasets/contacts/tables/{table}/items```

| 이름| 데이터 형식|필수|위치|기본값|설명|
| ---|---|---|---|---|---|
|테이블|string|yes|path|없음|검색할 연락처 폴더의 고유 식별자|
|$skip|정수|no|쿼리|없음|건너뛸 항목의 수(기본값 = 0)|
|$top|정수|no|쿼리|없음|검색할 항목의 최대 수(기본값 = 256)|
|$filter|string|no|쿼리|없음|항목의 수를 제한할 ODATA 필터 쿼리|
|$orderby|string|no|쿼리|없음|항목의 순서를 지정하는 ODATA orderBy 쿼리|

#### 응답

|이름|설명|
|---|---|
|200|확인|
|기본값|작업이 실패했습니다.|


### 연락처 만들기 
새 연락처 만들기 ```POST: /datasets/contacts/tables/{table}/items```

| 이름| 데이터 형식|필수|위치|기본값|설명|
| ---|---|---|---|---|---|
|테이블|string|yes|path|없음|연락처 폴더의 고유 식별자|
|항목| |yes|body|없음|만들 연락처|

#### 응답

|이름|설명|
|---|---|
|200|확인|
|기본값|작업이 실패했습니다.|


### 연락처 가져오기 
연락처 폴더에서 특정 연락처 검색 ```GET: /datasets/contacts/tables/{table}/items/{id}```

| 이름| 데이터 형식|필수|위치|기본값|설명|
| ---|---|---|---|---|---|
|테이블|string|yes|path|없음|연락처 폴더의 고유 식별자|
|id|string|yes|path|없음|검색할 연락처의 고유 식별자|

#### 응답

|이름|설명|
|---|---|
|200|확인|
|기본값|작업이 실패했습니다.|


### 연락처 삭제 
연락처를 삭제합니다. ```DELETE: /datasets/contacts/tables/{table}/items/{id}```

| 이름| 데이터 형식|필수|위치|기본값|설명|
| ---|---|---|---|---|---|
|테이블|string|yes|path|없음|연락처 폴더의 고유 식별자.|
|id|string|yes|path|없음|삭제할 연락처의 고유 식별자|

#### 응답

|이름|설명|
|---|---|
|200|확인|
|기본값|작업이 실패했습니다.|


### 연락처 업데이트 
연락처를 부분적으로 업데이트합니다. ```PATCH: /datasets/contacts/tables/{table}/items/{id}```

| 이름| 데이터 형식|필수|위치|기본값|설명|
| ---|---|---|---|---|---|
|테이블|string|yes|path|없음|연락처 폴더의 고유 식별자|
|id|string|yes|path|없음|업데이트할 연락처의 고유 식별자|
|항목| |yes|body|없음|업데이트할 연락처 항목|

#### 응답

|이름|설명|
|---|---|
|200|확인|
|기본값|작업이 실패했습니다.|


## 개체 정의

#### TriggerBatchResponse[IDictionary[String,Object]]

| 이름 | 데이터 형식 |필수|
|---|---|---|
|value|array|no|


#### SendMessage: 전자 메일 메시지 보내기

| 이름 | 데이터 형식 |필수|
|---|---|---|
|첨부 파일|array|no|
|원본|string|no|
|참조|string|no|
|Bcc|string|no|
|제목  
|string|yes|
|본문|string|yes|
|중요도  
|string|no|
|IsHtml|부울|no|
|받는 사람  
|string|yes|

#### SendAttachment: 첨부 파일

| 이름 | 데이터 형식 |필수|
|---|---|---|
|@odata.type|string|no|
|이름|string|yes|
|ContentBytes|string|yes|


#### ReceiveMessage: 전자 메일 메시지 받기

| 이름 | 데이터 형식 |필수|
|---|---|---|
|Id|string|no|
|IsRead|부울|no|
|HasAttachment|부울|no|
|DateTimeReceived|string|no|
|첨부 파일|array|no|
|원본|string|no|
|참조|string|no|
|Bcc|string|no|
|제목  
|string|yes|
|본문|string|yes|
|중요도  
|string|no|
|IsHtml|부울|no|
|받는 사람  
|string|yes|


#### ReceiveAttachment: 첨부 파일 받기

| 이름 | 데이터 형식 |필수|
|---|---|---|
|Id|string|yes|
|ContentType|string|yes|
|@odata.type|string|no|
|이름|string|no|
|ContentBytes|string|yes|


#### DigestMessage: 전자 메일 메시지 보내기

| 이름 | 데이터 형식 |필수|
|---|---|---|
|제목  
|string|yes|
|본문|string|no|
|중요도  
|string|no|
|Digest|array|yes|
|첨부 파일|array|no|
|받는 사람  
|string|yes|

#### TriggerBatchResponse[ReceiveMessage]

| 이름 | 데이터 형식 |필수|
|---|---|---|
|value|array|no|


#### DataSetsMetadata

| 이름 | 데이터 형식 |필수|
|---|---|---|
|tabular|정의되지 않음|no|
|Blob|정의되지 않음|no|


#### TabularDataSetsMetadata

| 이름 | 데이터 형식 |필수|
|---|---|---|
|원본|string|no|
|displayName|string|no|
|urlEncoding|string|no|
|tableDisplayName|string|no|
|tablePluralName|string|no|


#### BlobDataSetsMetadata

| 이름 | 데이터 형식 |필수|
|---|---|---|
|원본|string|no|
|displayName|string|no|
|urlEncoding|string|no|


#### TableMetadata

| 이름 | 데이터 형식 |필수|
|---|---|---|
|name|string|no|
|title|string|no|
|x-ms-permission|string|no|
|schema|정의되지 않음|no|


#### OptionsEmailSubscription: 옵션 전자 메일 구독에 대한 모델

| 이름 | 데이터 형식 |필수|
|---|---|---|
|NotificationUrl|string|no|
|Message|정의되지 않음|no|

#### MessageWithOptions: 사용자 옵션 전자 메일 메시지 사용자 입력의 일부로 예상된 메시지

| 이름 | 데이터 형식 |필수|
|---|---|---|
|제목  
|string|yes|
|옵션|string|yes|
|본문|string|no|
|중요도  
|string|no|
|첨부 파일|array|no|
|받는 사람  
|string|yes|

#### SubscriptionResponse: 승인 전자 메일 구독에 대한 모델

| 이름 | 데이터 형식 |필수|
|---|---|---|
|id|string|no|
|resource|string|no|
|notificationType|string|no|
|notificationUrl|string|no|


#### ApprovalEmailSubscription: 승인 전자 메일 구독에 대한 모델

| 이름 | 데이터 형식 |필수|
|---|---|---|
|NotificationUrl|string|no|
|Message|정의되지 않음|no|


#### ApprovalMessage: 승인 전자 메일 메시지 사용자 입력의 일부로 예상된 메시지

| 이름 | 데이터 형식 |필수|
|---|---|---|
|제목  
|string|yes|
|옵션|string|yes|
|본문|string|no|
|중요도  
|string|no|
|첨부 파일|array|no|
|받는 사람  
|string|yes|

#### ApprovalEmailResponse: 승인 전자 메일 응답

| 이름 | 데이터 형식 |필수|
|---|---|---|
|SelectedOption|string|no|

#### TablesList

| 이름 | 데이터 형식 |필수|
|---|---|---|
|value|array|no|


#### 테이블

| 이름 | 데이터 형식 |필수|
|---|---|---|
|이름|string|no|
|DisplayName|string|no|


#### 항목

| 이름 | 데이터 형식 |필수|
|---|---|---|
|ItemInternalId|string|no|


#### CalendarItemsList: 일정 항목의 목록

| 이름 | 데이터 형식 |필수|
|---|---|---|
|value|array|no|


#### CalendarItem: 일정 테이블 항목을 나타냄

| 이름 | 데이터 형식 |필수|
|---|---|---|
|ItemInternalId|string|no|


#### ContactItemsList: 연락처 항목의 목록

| 이름 | 데이터 형식 |필수|
|---|---|---|
|value|array|no|


#### ContactItem: 연락처 테이블 항목을 나타냄

| 이름 | 데이터 형식 |필수|
|---|---|---|
|ItemInternalId|string|no|


#### DataSetsList

| 이름 | 데이터 형식 |필수|
|---|---|---|
|value|array|no|


#### DataSet

| 이름 | 데이터 형식 | 필수|
|---|---|---|
|이름|string|no|
|DisplayName|string|no|


## 다음 단계

[논리 앱 만들기](../app-service-logic/app-service-logic-create-a-logic-app.md)

[API 목록](apis-list.md)으로 돌아갑니다.

<!--References-->
[5]: https://portal.azure.com
[7]: ./media/connectors-create-api-office365-outlook/aad-tenant-applications.png
[8]: ./media/connectors-create-api-office365-outlook/aad-tenant-applications-add-appinfo.png
[9]: ./media/connectors-create-api-office365-outlook/aad-tenant-applications-add-app-properties.png
[10]: ./media/connectors-create-api-office365-outlook/contoso-aad-app.png
[11]: ./media/connectors-create-api-office365-outlook/contoso-aad-app-configure.png
[12]: ./media/connectors-create-api-office365-outlook/contoso-aad-app-delegate-office365-outlook.png
[13]: ./media/connectors-create-api-office365-outlook/contoso-aad-app-delegate-office365-outlook-permissions.png

<!---HONumber=AcomDC_0525_2016-->