<properties
pageTitle="논리 앱에 Office 365 Outlook API 추가 | Microsoft Azure"
description="REST API 매개 변수를 사용하는 Office 365 Outlook API 개요"
services=""	
documentationCenter="" 	
authors="msftman"	
manager="dwrede"	
editor="" tags="connectors" />

<tags
ms.service="multiple"
ms.devlang="na"
ms.topic="article"
ms.tgt_pltfrm="na"
ms.workload="integration"
ms.date="02/22/2016"
ms.author="deonhe"/>

# Office365 Outlook API 시작

Office 365 API를 사용하면 Office 365와 상호 작용합니다. 예: 연락처 및 일정 항목 만들기, 편집 및 업데이트.

>[AZURE.NOTE] 이 버전의 문서는 논리 앱 2015-08-01-미리 보기 스키마 버전에 적용됩니다. 2014-12-01 미리 보기 스키마 버전에 대한 내용을 보려면 [Office 365 API](../app-service-logic/app-service-logic-connector-Office365.md)를 클릭하세요.

Office 365를 사용하면 다음과 같은 작업을 수행할 수 있습니다.

* 논리 앱 빌드
* 전원 앱 빌드

PowerApps 엔터프라이즈에서 API를 추가하는 방법을 보려면 [Register an API in PowerApps](../power-apps/powerapps-register-from-available-apis.md)(PowerApps에서 API 등록)로 이동하세요.

논리 앱에 작업을 추가하려면 [논리 앱 만들기](../app-service-logic/app-service-logic-create-a-logic-app.md)를 참조하세요.

## 트리거 및 작업에 대한 정보

Office365 API는 작업으로 사용할 수 있으며 트리거를 가지고 있습니다. 모든 API는 JSON 및 XML 형식의 데이터를 지원합니다.

 Office365 API에서는 다음과 같은 동작 및/또는 트리거를 사용할 수 있습니다.

### Office365 작업
다음 작업을 수행할 수 있습니다.

|작업|설명|
|--- | ---|
|GetEmails|폴더에서 전자 메일 검색|
|SendEmail|전자 메일 메시지 전송|
|DeleteEmail|ID로 전자 메일 메시지 삭제|
|MarkAsRead|전자 메일 메시지를 읽은 것으로 표시|
|ReplyTo|전자 메일 메시지에 회신|
|GetAttachment|ID별로 메시지 첨부 파일 검색|
|SendMailWithOptions|여러 옵션이 포함된 전자 메일을 보내고 받는 사람이 옵션 중 하나를 사용하여 다시 응답하기를 기다립니다.|
|SendApprovalMail|승인 전자 메일을 보내고 받는 사람으로부터 응답을 기다립니다.|
|CalendarGetTables|일정 검색|
|CalendarGetItems|일정에서 항목 검색|
|CalendarPostItem|새 이벤트 만들기|
|CalendarGetItem|일정에서 특정 항목 검색|
|CalendarDeleteItem|일정 항목 삭제|
|CalendarPatchItem|일정 항목 부분적 업데이트|
|ContactGetTables|연락처 폴더 검색|
|ContactGetItems|연락처 폴더에서 연락처 검색|
|ContactPostItem|새 연락처 만들기|
|ContactGetItem|연락처 폴더에서 특정 연락처 검색|
|ContactDeleteItem|연락처 삭제|
|ContactPatchItem|연락처 부분적 업데이트|
### Office365 트리거
다음 이벤트를 수신할 수 있습니다.

|트리거 | 설명|
|--- | ---|
|OnUpcomingEvents|예정된 일정 이벤트를 시작할 때 흐름 트리거|
|OnNewEmail|새 전자 메일이 도착했을 때 흐름 트리거|
|CalendarGetOnNewItems|새 일정 항목이 만들어질 때 트리거됨|
|CalendarGetOnUpdatedItems|일정 항목이 수정될 때 트리거됨|


## Office365에 대한 연결 만들기
Office365 API를 사용하려면 먼저 **연결**을 만든 다음 이러한 속성에 대한 세부 정보를 제공합니다.

|속성| 필수|설명|
| ---|---|---|
|신뢰|예|Office365 자격 증명 제공|


>[AZURE.TIP] 다른 논리 앱에서 이 연결을 사용할 수 있습니다.

## Office365 REST API 참조
#### 이 문서 적용 버전: 1.0


### 곧 시작되는 이벤트 


 예정된 일정 이벤트를 시작할 때 흐름 트리거 ```GET: /Events/OnUpcomingEvents```



| 이름| 데이터 형식|필수|위치|기본값|설명|
| ---|---|---|---|---|---|
|테이블|string|yes|쿼리|없음|일정의 고유 식별자|
|lookAheadTimeInMinutes|정수|no|쿼리|15|예정된 이벤트에 대해 조회할 시간(분)|


#### 응답

|이름|설명|
|---|---|
|200|작업이 성공함|
|202|작업이 성공함|
|400|BadRequest|
|401|권한 없음|
|403|사용할 수 없음|
|500|내부 서버 오류|
|기본값|작업이 실패했습니다.|
------



### 전자 메일 가져오기 


 폴더에서 전자 메일 검색 ```GET: /Mail```



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
|200|작업이 성공함|
|400|BadRequest|
|401|권한 없음|
|403|사용할 수 없음|
|500|내부 서버 오류|
|기본값|작업이 실패했습니다.|
------



### 메일 보내기 


 전자 메일 메시지 전송 ```POST: /Mail```



| 이름| 데이터 형식|필수|위치|기본값|설명|
| ---|---|---|---|---|---|
|emailMessage| |yes|body|없음|전자 메일 메시지 인스턴스|


#### 응답

|이름|설명|
|---|---|
|200|작업이 성공함|
|400|BadRequest|
|401|권한 없음|
|403|사용할 수 없음|
|500|내부 서버 오류|
|기본값|작업이 실패했습니다.|
------



### 전자 메일 삭제 


 ID로 전자 메일 메시지 삭제 ```DELETE: /Mail/{messageId}```



| 이름| 데이터 형식|필수|위치|기본값|설명|
| ---|---|---|---|---|---|
|messageId|string|yes|path|없음|삭제할 메시지의 id입니다.|


#### 응답

|이름|설명|
|---|---|
|200|작업이 성공함|
|400|BadRequest|
|401|권한 없음|
|403|사용할 수 없음|
|500|내부 서버 오류|
|기본값|작업이 실패했습니다.|
------



### 읽은 상태로 표시 


 전자 메일 메시지를 읽은 것으로 표시 ```POST: /Mail/MarkAsRead/{messageId}```



| 이름| 데이터 형식|필수|위치|기본값|설명|
| ---|---|---|---|---|---|
|messageId|string|yes|path|없음|읽은 상태로 표시될 메시지의 id|


#### 응답

|이름|설명|
|---|---|
|200|작업이 성공함|
|400|BadRequest|
|401|권한 없음|
|403|사용할 수 없음|
|500|내부 서버 오류|
|기본값|작업이 실패했습니다.|
------



### 메시지에 회신 


 전자 메일 메시지에 회신 ```POST: /Mail/ReplyTo/{messageId}```



| 이름| 데이터 형식|필수|위치|기본값|설명|
| ---|---|---|---|---|---|
|messageId|string|yes|path|없음|회신할 메시지의 id|
|주석|string|yes|쿼리|없음|회신 주석|
|replyAll|부울|no|쿼리|false|모든 받는 사람에게 회신|


#### 응답

|이름|설명|
|---|---|
|200|작업이 성공함|
|400|BadRequest|
|401|권한 없음|
|403|사용할 수 없음|
|500|내부 서버 오류|
|기본값|작업이 실패했습니다.|
------



### 첨부 파일 가져오기 


 ID별로 메시지 첨부 파일 검색 ```GET: /Mail/{messageId}/Attachments/{attachmentId}```



| 이름| 데이터 형식|필수|위치|기본값|설명|
| ---|---|---|---|---|---|
|messageId|string|yes|path|없음|메시지의 id|
|attachmentId|string|yes|path|없음|다운로드할 첨부 파일의 id|


#### 응답

|이름|설명|
|---|---|
|200|작업이 성공함|
|400|BadRequest|
|401|권한 없음|
|403|사용할 수 없음|
|500|내부 서버 오류|
|기본값|작업이 실패했습니다.|
------



### 새 전자 메일에 


 새 전자 메일이 도착했을 때 흐름 트리거 ```GET: /Mail/OnNewEmail```



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
------



### 옵션을 사용하여 메일 전송 


 여러 옵션이 포함된 전자 메일을 보내고 받는 사람이 옵션 중 하나를 사용하여 다시 응답하기를 기다립니다. ```POST: /mailwithoptions/$subscriptions```



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
------



### 승인 전자 메일 보내기 


 승인 전자 메일을 보내고 받는 사람으로부터 응답을 기다립니다. ```POST: /approvalmail/$subscriptions```



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
------



### 일정 가져오기 


 일정 검색 ```GET: /datasets/calendars/tables```

이 호출에 대한 매개 변수는 없습니다
#### 응답

|이름|설명|
|---|---|
|200|확인|
|기본값|작업이 실패했습니다.|
------



### 이벤트 가져오기 


 일정에서 항목 검색 ```GET: /datasets/calendars/tables/{table}/items```



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
------



### 이벤트 만들기 


 새 이벤트 만들기 ```POST: /datasets/calendars/tables/{table}/items```



| 이름| 데이터 형식|필수|위치|기본값|설명|
| ---|---|---|---|---|---|
|테이블|string|yes|path|없음|일정의 고유 식별자|
|항목| |yes|body|없음|만들 일정 항목|


#### 응답

|이름|설명|
|---|---|
|200|확인|
|기본값|작업이 실패했습니다.|
------



### 이벤트 가져오기 


 일정에서 특정 항목 검색 ```GET: /datasets/calendars/tables/{table}/items/{id}```



| 이름| 데이터 형식|필수|위치|기본값|설명|
| ---|---|---|---|---|---|
|테이블|string|yes|path|없음|일정의 고유 식별자|
|id|string|yes|path|없음|검색할 일정 항목의 고유 식별자|


#### 응답

|이름|설명|
|---|---|
|200|확인|
|기본값|작업이 실패했습니다.|
------



### 이벤트 삭제 


 일정 항목 삭제 ```DELETE: /datasets/calendars/tables/{table}/items/{id}```



| 이름| 데이터 형식|필수|위치|기본값|설명|
| ---|---|---|---|---|---|
|테이블|string|yes|path|없음|일정의 고유 식별자.|
|id|string|yes|path|없음|삭제할 일정 항목의 고유 식별자|


#### 응답

|이름|설명|
|---|---|
|200|확인|
|기본값|작업이 실패했습니다.|
------



### 이벤트 업데이트 


 일정 항목 부분적 업데이트 ```PATCH: /datasets/calendars/tables/{table}/items/{id}```



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
------



### 새 항목에 


 새 일정 항목이 만들어질 때 트리거됨 ```GET: /datasets/calendars/tables/{table}/onnewitems```



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
------



### 업데이트된 항목에 


 일정 항목이 수정될 때 트리거됨 ```GET: /datasets/calendars/tables/{table}/onupdateditems```



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
------



### 연락처 폴더 가져오기 


 연락처 폴더 검색 ```GET: /datasets/contacts/tables```

이 호출에 대한 매개 변수는 없습니다
#### 응답

|이름|설명|
|---|---|
|200|확인|
|기본값|작업이 실패했습니다.|
------



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
------



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
------



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
------



### 연락처 삭제 


 연락처 삭제 ```DELETE: /datasets/contacts/tables/{table}/items/{id}```



| 이름| 데이터 형식|필수|위치|기본값|설명|
| ---|---|---|---|---|---|
|테이블|string|yes|path|없음|연락처 폴더의 고유 식별자.|
|id|string|yes|path|없음|삭제할 연락처의 고유 식별자|


#### 응답

|이름|설명|
|---|---|
|200|확인|
|기본값|작업이 실패했습니다.|
------



### 연락처 업데이트 


 연락처 부분적 업데이트 ```PATCH: /datasets/contacts/tables/{table}/items/{id}```



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
------



## 개체 정의: 

 **TriggerBatchResponse[IDictionary[String,Object]]**:

TriggerBatchResponse[IDictionary[String,Object]]에 대한 필수 속성:


필수 속성이 없습니다.


**모든 속성**:


| 이름 | 데이터 형식 |
|---|---|
|value|array|



 **SendMessage**: 전자 메일 메시지 보내기

SendMessage에 대한 필수 속성:

Subject, Body, To

**모든 속성**:


| 이름 | 데이터 형식 |
|---|---|
|첨부 파일|array|
|원본|string|
|참조|string|
|Bcc|string|
|제목|string|
|본문|string|
|중요도|string|
|IsHtml|부울|
|받는 사람|string|



 **SendAttachment**: 첨부 파일

SendAttachment에 대한 필수 속성:

Name, ContentBytes

**모든 속성**:


| 이름 | 데이터 형식 |
|---|---|
|@odata.type|string|
|이름|string|
|ContentBytes|string|



 **ReceiveMessage**: 전자 메일 메시지 수신

ReceiveMessage에 대한 필수 속성:

Subject, Body, To

**모든 속성**:


| 이름 | 데이터 형식 |
|---|---|
|Id|string|
|IsRead|부울|
|HasAttachment|부울|
|DateTimeReceived|string|
|첨부 파일|array|
|원본|string|
|참조|string|
|Bcc|string|
|제목|string|
|본문|string|
|중요도|string|
|IsHtml|부울|
|받는 사람|string|



 **ReceiveAttachment**: 첨부 파일 수신

ReceiveAttachment에 대한 필수 속성:

Id, ContentType, Name, ContentBytes

**모든 속성**:


| 이름 | 데이터 형식 |
|---|---|
|Id|string|
|ContentType|string|
|@odata.type|string|
|이름|string|
|ContentBytes|string|



 **DigestMessage**: 전자 메일 메시지 보내기

DigestMessage에 대한 필수 속성:

Subject, Digest, To

**모든 속성**:


| 이름 | 데이터 형식 |
|---|---|
|제목|string|
|본문|string|
|중요도|string|
|Digest|array|
|첨부 파일|array|
|받는 사람|string|



 **TriggerBatchResponse [ReceiveMessage]**:

TriggerBatchResponse [ReceiveMessage]에 대한 필수 속성:


필수 속성이 없습니다.


**모든 속성**:


| 이름 | 데이터 형식 |
|---|---|
|value|array|



 **DataSetsMetadata**:

DataSetsMetadata에 대한 필수 속성:


필수 속성이 없습니다.


**모든 속성**:


| 이름 | 데이터 형식 |
|---|---|
|tabular|정의되지 않음|
|Blob|정의되지 않음|



 **TabularDataSetsMetadata**:

TabularDataSetsMetadata에 대한 필수 속성:


필수 속성이 없습니다.


**모든 속성**:


| 이름 | 데이터 형식 |
|---|---|
|원본|string|
|displayName|string|
|urlEncoding|string|
|tableDisplayName|string|
|tablePluralName|string|



 **BlobDataSetsMetadata**:

BlobDataSetsMetadata에 대한 필수 속성:


필수 속성이 없습니다.


**모든 속성**:


| 이름 | 데이터 형식 |
|---|---|
|원본|string|
|displayName|string|
|urlEncoding|string|



 **TableMetadata**:

TableMetadata에 대한 필수 속성:


필수 속성이 없습니다.


**모든 속성**:


| 이름 | 데이터 형식 |
|---|---|
|name|string|
|title|string|
|x-ms-permission|string|
|schema|정의되지 않음|



 **OptionsEmailSubscription**: 옵션 전자 메일 구독에 대한 모델

OptionsEmailSubscription에 대한 필수 속성:


필수 속성이 없습니다.


**모든 속성**:


| 이름 | 데이터 형식 |
|---|---|
|NotificationUrl|string|
|Message|정의되지 않음|



 **MessageWithOptions**: 사용자 옵션 전자 메일 메시지. 사용자 입력의 일부로 예상된 메시지

MessageWithOptions에 대한 필수 속성:

Subject, Options, To

**모든 속성**:


| 이름 | 데이터 형식 |
|---|---|
|제목|string|
|옵션|string|
|본문|string|
|중요도|string|
|첨부 파일|array|
|받는 사람|string|



 **SubscriptionResponse**: 승인 전자 메일 구독에 대한 모델

SubscriptionResponse에 대한 필수 속성:


필수 속성이 없습니다.


**모든 속성**:


| 이름 | 데이터 형식 |
|---|---|
|id|string|
|resource|string|
|notificationType|string|
|notificationUrl|string|



 **ApprovalEmailSubscription**: 승인 전자 메일 구독에 대한 모델

ApprovalEmailSubscription에 대한 필수 속성:


필수 속성이 없습니다.


**모든 속성**:


| 이름 | 데이터 형식 |
|---|---|
|NotificationUrl|string|
|Message|정의되지 않음|



 **ApprovalMessage**: 승인 전자 메일 메시지. 사용자 입력의 일부로 예상된 메시지

ApprovalMessage에 대한 필수 속성:

Subject, Options, To

**모든 속성**:


| 이름 | 데이터 형식 |
|---|---|
|제목|string|
|옵션|string|
|본문|string|
|중요도|string|
|첨부 파일|array|
|받는 사람  
|string|



 **ApprovalEmailResponse**: 승인 전자 메일 응답

ApprovalEmailResponse에 대한 필수 속성:


필수 속성이 없습니다.


**모든 속성**:


| 이름 | 데이터 형식 |
|---|---|
|SelectedOption|string|



 **TablesList**:

TablesList에 대한 필수 속성:


필수 속성이 없습니다.


**모든 속성**:


| 이름 | 데이터 형식 |
|---|---|
|value|array|



 **테이블**:

Table에 대한 필수 속성:


필수 속성이 없습니다.


**모든 속성**:


| 이름 | 데이터 형식 |
|---|---|
|이름|string|
|DisplayName|string|



 **Item**:

Item에 대한 필수 속성:


필수 속성이 없습니다.


**모든 속성**:


| 이름 | 데이터 형식 |
|---|---|
|ItemInternalId|string|



 **CalendarItemsList**: 일정 항목의 목록

CalendarItemsList에 대한 필수 속성:


필수 속성이 없습니다.


**모든 속성**:


| 이름 | 데이터 형식 |
|---|---|
|value|array|



 **CalendarItem**: 일정 테이블 항목을 나타냄

CalendarItem에 대한 필수 속성:


필수 속성이 없습니다.


**모든 속성**:


| 이름 | 데이터 형식 |
|---|---|
|ItemInternalId|string|



 **ContactItemsList**: 연락처 항목의 목록

ContactItemsList에 대한 필수 속성:


필수 속성이 없습니다.


**모든 속성**:


| 이름 | 데이터 형식 |
|---|---|
|value|array|



 **ContactItem**: 연락처 테이블 항목을 나타냄

ContactItem에 대한 필수 속성:


필수 속성이 없습니다.


**모든 속성**:


| 이름 | 데이터 형식 |
|---|---|
|ItemInternalId|string|



 **DataSetsList**:

DataSetsList에 대한 필수 속성:


필수 속성이 없습니다.


**모든 속성**:


| 이름 | 데이터 형식 |
|---|---|
|value|array|



 **DataSet**:

DataSet에 대한 필수 속성:


필수 속성이 없습니다.


**모든 속성**:


| 이름 | 데이터 형식 |
|---|---|
|이름|string|
|DisplayName|string|


## 다음 단계
PowerApps 엔터프라이즈에 Office 365 API를 추가한 후 해당 앱에서 API를 사용할 [권한을 사용자에게 부여](../power-apps/powerapps-manage-api-connection-user-access.md)합니다.

[논리 앱 만들기](../app-service-logic/app-service-logic-create-a-logic-app.md).

<!---HONumber=AcomDC_0224_2016-->
