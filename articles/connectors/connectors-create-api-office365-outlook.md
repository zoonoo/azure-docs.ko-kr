<properties
    pageTitle="논리 앱에서 Office 365 Outlook 커넥터 추가 | Microsoft Azure"
    description="Office 365와 상호 작용할 수 있도록 Office 365 커넥터로 논리 앱을 만듭니다. 예: 연락처 및 일정 항목 만들기, 편집 및 업데이트."
    services=""    
    documentationCenter=""     
    authors="MandiOhlinger"    
    manager="erikre"    
    editor="" 
    tags="connectors" />

<tags
ms.service="logic-apps"
ms.devlang="na"
ms.topic="article"
ms.tgt_pltfrm="na"
ms.workload="integration"
ms.date="07/26/2016"
ms.author="mandia"/>

# Office 365 Outlook 커넥터 시작 

Office 365 Outlook 커넥터를 통해 Office 365에서 Outlook과 상호 작용할 수 있습니다. 이 커넥터를 사용하여 연락처 및 일정 항목을 만들기, 편집 및 업데이트하고 전자 메일을 가져오고 보내며 회신할 수도 있습니다.

Office 365 Outlook을 사용하여 다음을 수행합니다.

- Office 365 내에서 전자 메일 및 일정 기능을 사용하여 워크플로를 빌드합니다.
- 새 전자 메일이 있거나 일정 항목이 업데이트될 때 트리거를 사용하여 워크플로를 시작합니다.
- 전자 메일을 보내고 새 일정 이벤트를 만드는 등의 작업을 사용합니다. 예를 들어 Salesforce에 새 개체(트리거)가 있는 경우 Office 365 Outlook으로 전자 메일을 보냅니다(작업).

이 항목에서는 논리 앱에서 Office 365 Outlook 커넥터를 사용하는 방법을 보여 주고 트리거 및 작업을 나열합니다.

>[AZURE.NOTE] 이 버전의 문서는 논리 앱 GA(일반 공급)에 적용됩니다.

논리 앱에 대해 자세히 알아보려면 [논리 앱이란 무엇인가요?](../app-service-logic/app-service-logic-what-are-logic-apps.md) 및 [논리 앱 만들기](../app-service-logic/app-service-logic-create-a-logic-app.md)를 참조하세요.

## Office 365에 연결

논리 앱에서 서비스에 액세스하려면 먼저 서비스에 대한 *연결*을 만들어야 합니다. 연결은 논리 앱과 다른 서비스 간의 연결을 제공합니다. 예를 들어 Office 365 Outlook에 연결하려면 먼저 Office 365 *연결*이 필요합니다. 연결을 만들려면 연결하려는 서비스에 액세스할 때 일반적으로 사용하는 자격 증명을 입력합니다. 따라서 Office 365 Outlook을 사용하는 경우 Office 365 계정에 대한 자격 증명을 입력하여 연결을 만듭니다.


## 연결 만들기

>[AZURE.INCLUDE [Office 365에 대한 연결을 만드는 단계](../../includes/connectors-create-api-office365-outlook.md)]

## 트리거 사용

트리거는 논리 앱에 정의된 워크플로를 시작하는 데 사용할 수 있는 이벤트입니다. 원하는 간격 및 빈도로 서비스의 "폴링"을 트리거합니다. [트리거에 대해 자세히 알아보세요.](../app-service-logic/app-service-logic-what-are-logic-apps.md#logic-app-concepts)

1. 논리 앱에서 트리거 목록을 가져오려면 "office 365"를 입력합니다.

	![](./media/connectors-create-api-office365-outlook/office365-trigger.png)

2. **Office 365 Outlook - 예정된 이벤트가 곧 시작될 때**를 선택합니다. 연결이 이미 있는 경우 드롭다운 목록에서 일정을 선택합니다.

	![](./media/connectors-create-api-office365-outlook/sample-calendar.png)

	로그인하라는 메시지가 표시되면 로그인 세부 정보를 입력하여 연결을 만듭니다. 이 항목의 [연결 만들기](connectors-create-api-office365-outlook.md#create-the-connection)에 단계가 표시됩니다.

	> [AZURE.NOTE] 이 예제에서는 일정 이벤트가 업데이트되면 논리 앱이 실행됩니다. 이 트리거의 결과를 보려면 텍스트 메시지를 보내는 다른 작업을 추가합니다. 예를 들어 일정 이벤트가 15분 내에 시작되는 경우 텍스트를 보내는 Twilio *메시지 보내기* 작업을 추가합니다.

3. **편집** 단추를 선택하고 **빈도** 및 **간격** 값을 설정합니다. 예를 들어 15분마다 폴링을 트리거하려면 **빈도**를 **분**으로, **간격**을 **15**로 설정합니다.

	![](./media/connectors-create-api-office365-outlook/calendar-settings.png)

4. 변경 내용을 **저장**합니다(도구 모음 왼쪽 위). 논리 앱이 저장되며 이 논리 앱이 사용 상태로 자동 설정될 수 있습니다.


## 작업 사용

작업은 논리 앱에 정의된 워크플로에 의해 수행되는 작업입니다. [작업에 대해 자세히 알아봅니다.](../app-service-logic/app-service-logic-what-are-logic-apps.md#logic-app-concepts)

1. 더하기 기호를 선택합니다. **작업 추가**, **조건 추가** 또는 **자세히** 옵션 등이 표시됩니다.

	![](./media/connectors-create-api-office365-outlook/add-action.png)

2. **작업 추가**를 선택합니다.

3. 사용 가능한 모든 작업의 목록을 표시하려면 텍스트 상자에 "office 365"를 입력합니다.

	![](./media/connectors-create-api-office365-outlook/office365-actions.png)

4. 이 예제에서는 **Office 365 Outlook - 연락처 만들기**를 선택했습니다. 연결이 이미 존재하는 경우 **폴더 ID**, **지정된 이름** 및 기타 속성을 선택합니다.

	![](./media/connectors-create-api-office365-outlook/office365-sampleaction.png)

	연결 정보를 묻는 메시지가 표시되면 연결을 만들기 위한 세부 정보를 입력합니다. 이 항목의 [연결 만들기](connectors-create-api-office365-outlook.md#create-the-connection)에서는 이러한 속성에 대해 설명합니다.

	> [AZURE.NOTE] 이 예제에서는 Office 365 Outlook에 새 연락처를 만듭니다. 다른 트리거의 출력을 사용하여 연락처를 만들 수 있습니다. 예를 들어 SalesForce *개체를 만들 때* 트리거를 추가합니다. 그런 다음 SalesForce 필드를 사용하여 Office 365에서 새 연락처를 만드는 Office 365 Outlook *연락처 만들기* 작업을 추가합니다.

5. 변경 내용을 **저장**합니다(도구 모음 왼쪽 위). 논리 앱이 저장되며 이 논리 앱이 사용 상태로 자동 설정될 수 있습니다.


## 기술 세부 정보

이 연결에서 지원하는 트리거, 작업 및 응답에 대한 세부 정보는 다음과 같습니다.

## Office 365 트리거

|트리거 | 설명|
|--- | ---|
|[예정된 이벤트가 곧 시작될 때](connectors-create-api-office365-outlook.md#when-an-upcoming-event-is-starting-soon)|이 작업은 예정된 일정 이벤트를 시작할 때 흐름을 트리거합니다.|
|[새 전자 메일이 도착했을 때](connectors-create-api-office365-outlook.md#when-a-new-email-arrives)|이 작업은 새 전자 메일이 도착했을 때 흐름을 트리거합니다.|
|[새 이벤트를 만들 때](connectors-create-api-office365-outlook.md#when-a-new-event-is-created)|이 작업은 일정에서 새 파일을 만들 때 흐름을 트리거합니다.|
|[이벤트를 수정할 때](connectors-create-api-office365-outlook.md#when-an-event-is-modified)|이 작업은 일정에서 이벤트를 수정할 때 흐름을 트리거합니다.|


## Office 365 작업

|작업|설명|
|--- | ---|
|[전자 메일 가져오기](connectors-create-api-office365-outlook.md#get-emails)|이 작업은 폴더에서 전자 메일을 가져옵니다.|
|[전자 메일 보내기](connectors-create-api-office365-outlook.md#send-an-email)|이 작업은 전자 메일 메시지를 전송합니다.|
|[전자 메일 삭제](connectors-create-api-office365-outlook.md#delete-email)|이 작업은 ID별 전자 메일을 삭제합니다.|
|[읽은 상태로 표시](connectors-create-api-office365-outlook.md#mark-as-read)|이 작업은 전자 메일을 읽은 것으로 표시합니다.|
|[전자 메일에 회신](connectors-create-api-office365-outlook.md#reply-to-email)|이 작업은 전자 메일에 회신합니다.|
|[첨부 파일 가져오기](connectors-create-api-office365-outlook.md#get-attachment)|이 작업은 ID별로 전자 메일 첨부 파일을 가져옵니다.|
|[옵션을 사용하여 메일 전송](connectors-create-api-office365-outlook.md#send-email-with-options)|이 작업은 여러 옵션이 포함된 메일을 보내고 받는 사람이 옵션 중 하나로 다시 응답하기를 대기합니다.|
|[승인 전자 메일 보내기](connectors-create-api-office365-outlook.md#send-approval-email)|이 작업은 승인 메일을 보내고 받는 사람으로부터 응답을 기다립니다.|
|[일정 가져오기](connectors-create-api-office365-outlook.md#get-calendars)|이 작업은 사용 가능한 일정을 나열합니다.|
|[이벤트 가져오기](connectors-create-api-office365-outlook.md#get-events)|이 작업은 일정에서 이벤트를 가져옵니다.|
|[이벤트 만들기](connectors-create-api-office365-outlook.md#create-event)|이 작업은 일정에서 새 이벤트를 만듭니다.|
|[이벤트 가져오기](connectors-create-api-office365-outlook.md#get-event)|이 작업은 일정에서 특정 이벤트를 가져옵니다.|
|[이벤트 삭제](connectors-create-api-office365-outlook.md#delete-event)|이 작업은 일정에서 이벤트를 삭제합니다.|
|[이벤트 업데이트](connectors-create-api-office365-outlook.md#update-event)|이 작업은 일정에서 이벤트를 업데이트합니다.|
|[연락처 폴더 가져오기](connectors-create-api-office365-outlook.md#get-contact-folders)|이 작업은 사용 가능한 연락처 폴더를 나열합니다.|
|[연락처 가져오기](connectors-create-api-office365-outlook.md#get-contacts)|이 작업은 연락처 폴더에서 연락처를 가져옵니다.|
|[연락처 만들기](connectors-create-api-office365-outlook.md#create-contact)|이 작업은 연락처 폴더에 새 연락처를 만듭니다.|
|[연락처 가져오기](connectors-create-api-office365-outlook.md#get-contact)|이 작업은 연락처 폴더에서 특정 연락처를 가져옵니다.|
|[연락처 삭제](connectors-create-api-office365-outlook.md#delete-contact)|이 작업은 연락처 폴더에서 연락처를 삭제합니다.|
|[연락처 업데이트](connectors-create-api-office365-outlook.md#update-contact)|이 작업은 연락처 폴더에서 연락처를 업데이트합니다.|

### 트리거 및 작업 세부 정보

이 섹션에서는 모든 필수 또는 선택적 입력 속성 및 커넥터와 연결된 모든 해당 출력을 비롯한 각 트리거 및 작업에 대한 특정 세부 정보를 참조하세요.

#### 예정된 이벤트가 곧 시작될 때
이 작업은 예정된 일정 이벤트를 시작할 때 흐름을 트리거합니다.

|속성 이름| 표시 이름|설명|
| ---|---|---|
|table*|일정 ID|일정의 고유 식별자|
|lookAheadTimeInMinutes|미리 보기 시간|예정된 이벤트에 대해 조회할 시간(분)|

별표(*)는 속성이 필수 사항임을 의미합니다.

##### 출력 세부 정보
CalendarItemsList: 일정 항목의 목록

| 속성 이름 | 데이터 형식 | 설명 |
|---|---|---|
|value|array|일정 항목의 목록|


#### 전자 메일 가져오기
이 작업은 폴더에서 전자 메일을 가져옵니다.

|속성 이름| 표시 이름|설명|
| ---|---|---|
|folderPath|폴더 경로|전자 메일을 검색하는 폴더의 경로(기본값: '받은 편지함')|
|top|상위|검색할 전자 메일의 수(기본값: 10)|
|fetchOnlyUnread|읽지 않은 메시지만 가져오기|읽지 않은 전자 메일만 검색할 수 있습니까?|
|includeAttachments|첨부 파일 포함|true로 설정된 경우 첨부 파일도 전자 메일과 함께 검색됩니다.|
|searchQuery|검색 쿼리|전자 메일을 필터링하려는 검색 쿼리|
|skip|Skip|건너뛸 전자 메일의 수(기본값: 0)|
|skipToken|토큰 건너뛰기|새 페이지를 가져오는 토큰 건너뛰기|

별표(*)는 속성이 필수 사항임을 의미합니다.

##### 출력 세부 정보
ReceiveMessage: 전자 메일 메시지 받기

| 속성 이름 | 데이터 형식 | 설명 |
|---|---|---|
|원본|string|원본|
|받는 사람  
|string|받는 사람  
|
|제목  
|string|제목  
|
|본문|string|본문|
|중요도  
|string|중요도  
|
|HasAttachment|부울|첨부 파일 포함|
|Id|string|메시지 ID|
|IsRead|부울|읽음|
|DateTimeReceived|string|수신한 날짜 시간|
|첨부 파일|array|첨부 파일|
|참조|string|someone@contoso.com처럼 세미콜론으로 구분된 전자 메일 주소를 지정합니다.|
|Bcc|string|someone@contoso.com처럼 세미콜론으로 구분된 전자 메일 주소를 지정합니다.|
|IsHtml|부울|Html임|


#### 전자 메일 보내기
이 작업은 전자 메일 메시지를 전송합니다.

|속성 이름| 표시 이름|설명|
| ---|---|---|
|emailMessage*|Email|Email|

별표(*)는 속성이 필수 사항임을 의미합니다.

##### 출력 세부 정보
없음

#### 전자 메일 삭제
이 작업은 ID별 전자 메일을 삭제합니다.

|속성 이름| 표시 이름|설명|
| ---|---|---|
|messageId*|메시지 ID|삭제할 전자 메일의 ID|

별표(*)는 속성이 필수 사항임을 의미합니다.

##### 출력 세부 정보
없음

#### 읽은 상태로 표시
이 작업은 전자 메일을 읽은 것으로 표시합니다.

|속성 이름| 표시 이름|설명|
| ---|---|---|
|messageId*|메시지 ID|읽은 상태로 표시될 메시지의 ID|

별표(*)는 속성이 필수 사항임을 의미합니다.

##### 출력 세부 정보
없음


#### 전자 메일에 회신
이 작업은 전자 메일에 회신합니다.

|속성 이름| 표시 이름|설명|
| ---|---|---|
|messageId*|메시지 ID|회신할 전자 메일의 ID|
|comment*|주석|회신 주석|
|replyAll|전체 회신|모든 받는 사람에게 회신|

별표(*)는 속성이 필수 사항임을 의미합니다.

##### 출력 세부 정보
없음


#### 첨부 파일 가져오기
이 작업은 ID별로 전자 메일 첨부 파일을 가져옵니다.

|속성 이름| 표시 이름|설명|
| ---|---|---|
|messageId*|메시지 ID|전자 메일의 ID|
|attachmentId*|첨부 파일 ID|다운로드할 첨부 파일의 id|

별표(*)는 속성이 필수 사항임을 의미합니다.

##### 출력 세부 정보
없음


#### 새 전자 메일이 도착했을 때
이 작업은 새 전자 메일이 도착했을 때 흐름을 트리거합니다.

|속성 이름| 표시 이름|설명|
| ---|---|---|
|folderPath|폴더 경로|검색할 전자 메일 폴더(기본값: 받은 편지함)|
|to|받는 사람  
|받는 사람 전자 메일 주소|
|from|원본|보낸 사람 주소|
|중요도|중요도  
|전자 메일의 중요도(높음, 보통, 낮음) (기본값: 보통)|
|fetchOnlyWithAttachment|첨부 파일 있음|첨부 파일이 있는 전자 메일만 검색|
|includeAttachments|첨부 파일 포함|첨부 파일 포함|
|subjectFilter|제목 필터|제목에서 찾을 문자열|

별표(*)는 속성이 필수 사항임을 의미합니다.

##### 출력 세부 정보
TriggerBatchResponse[ReceiveMessage]

| 속성 이름 | 데이터 형식 |
|---|---|
|value|array|


#### 옵션을 사용하여 메일 전송
이 작업은 여러 옵션이 포함된 메일을 보내고 받는 사람이 옵션 중 하나로 다시 응답하기를 대기합니다.

|속성 이름| 표시 이름|설명|
| ---|---|---|
|optionsEmailSubscription*|옵션에 대한 구독 요청 전자 메일|옵션에 대한 구독 요청 전자 메일|

별표(*)는 속성이 필수 사항임을 의미합니다.

##### 출력 세부 정보
SubscriptionResponse: 승인 전자 메일 구독에 대한 모델

| 속성 이름 | 데이터 형식 | 설명 |
|---|---|---|
|id|string|구독의 ID|
|resource|string|구독 요청의 리소스|
|notificationType|string|알림 형식|
|notificationUrl|string|알림 URL|


#### 승인 전자 메일 보내기
이 작업은 승인 메일을 보내고 받는 사람으로부터 응답을 기다립니다.

|속성 이름| 표시 이름|설명|
| ---|---|---|
|approvalEmailSubscription*|승인 전자 메일에 대한 구독 요청|승인 전자 메일에 대한 구독 요청|

별표(*)는 속성이 필수 사항임을 의미합니다.

##### 출력 세부 정보
SubscriptionResponse: 승인 전자 메일 구독에 대한 모델

| 속성 이름 | 데이터 형식 | 설명 |
|---|---|---|
|id|string|구독의 ID|
|resource|string|구독 요청의 리소스|
|notificationType|string|알림 형식|
|notificationUrl|string|알림 URL|


#### 일정 가져오기
이 작업은 사용 가능한 일정을 나열합니다.

이 호출에 대한 매개 변수는 없습니다.

##### 출력 세부 정보
TablesList

| 속성 이름 | 데이터 형식 |
|---|---|
|value|array|


#### 이벤트 가져오기
이 작업은 일정에서 이벤트를 가져옵니다.

|속성 이름| 표시 이름|설명|
| ---|---|---|
|table*|일정 ID|일정 선택|
|$filter|필터 쿼리|반환된 항목을 제한할 ODATA 필터 쿼리|
|$orderby|Order By|항목의 순서를 지정하는 ODATA orderBy 쿼리|
|$skip|숫자 건너뛰며 세기|건너뛸 항목의 수(기본값 = 0)|
|$top|최대 가져오기 수|검색할 항목의 최대 수(기본값 = 256)|

별표(*)는 속성이 필수 사항임을 의미합니다.

##### 출력 세부 정보
CalendarEventList: 일정 항목의 목록

| 속성 이름 | 데이터 형식 | 설명 |
|---|---|---|
|value|array|일정 항목의 목록|


#### 이벤트 만들기
이 작업은 일정에서 새 이벤트를 만듭니다.

|속성 이름| 표시 이름|설명|
| ---|---|---|
|table*|일정 ID|일정 선택|
|item*|항목|만들 이벤트|

별표(*)는 속성이 필수 사항임을 의미합니다.

##### 출력 세부 정보
CalendarEvent: 커넥터별 일정 이벤트 모델 클래스입니다.

| 속성 이름 | 데이터 형식 | 설명 |
|---|---|---|
|Id|string|이벤트의 고유 식별자입니다.|
|Attendees|array|이벤트의 참석자 목록입니다.|
|본문|정의되지 않음|이벤트와 연결된 메시지 본문입니다.|
|BodyPreview|string|이벤트와 연결된 메시지 미리 보기입니다.|
|범주|array|이벤트와 연결된 범주입니다.|
|ChangeKey|string|이벤트 개체의 버전을 식별합니다. 이벤트가 변경될 때마다 ChangeKey도 변경됩니다.|
|DateTimeCreated|string|이벤트가 생성된 날짜 및 시간입니다.|
|DateTimeLastModified|string|이벤트가 마지막으로 수정된 날짜 및 시간입니다.|
|끝|string|이벤트의 종료 시간입니다.|
|EndTimeZone|string|모임 종료 시간의 시간대를 지정합니다. 이 값은 Windows에 정의된 대로여야 합니다(예: '태평양 표준시').|
|HasAttachments|부울|이벤트에 첨부 파일이 있으면 true로 설정합니다.|
|중요도  
|string|이벤트의 중요도(낮음, 보통, 높음)입니다.|
|IsAllDay|부울|이벤트가 하루 종일 진행되는 경우 true로 설정합니다.|
|IsCancelled|부울|이벤트가 취소된 경우 true로 설정합니다.|
|IsOrganizer|부울|메시지 보낸 사람이 이끌이인 경우 true로 설정합니다.|
|위치|정의되지 않음|이벤트의 위치입니다.|
|이끌이|정의되지 않음|이벤트의 이끌이입니다.|
|되풀이|정의되지 않음|이벤트의 되풀이 패턴입니다.|
|미리 알림|정수|이벤트 시작을 미리 알리는 시간(분)입니다.|
|ResponseRequested|부울|이벤트가 수락 또는 거절될 때 보낸 사람이 응답하고 싶다면 true로 설정합니다.|
|ResponseStatus|정의되지 않음|이벤트 메시지에 대한 응답으로 보낸 응답 형식을 나타냅니다.|
|SeriesMasterId|string|Series Master 이벤트 유형에 대한 고유 식별자입니다.|
|ShowAs|string|사용 가능 또는 사용 중으로 표시합니다.|
|시작|string|이벤트의 시간입니다.|
|StartTimeZone|string|모임 시작 시간의 시간대를 지정합니다. 이 값은 Windows에 정의된 대로여야 합니다(예: "태평양 표준시").|
|제목  
|string|이벤트 제목입니다.|
|형식|string|이벤트 유형: Single Instance, Occurrence, Exception 또는 Series Master.|
|WebLink|string|이벤트와 연결된 메시지 미리 보기입니다.|


#### 이벤트 가져오기
이 작업은 일정에서 특정 이벤트를 가져옵니다.

|속성 이름| 표시 이름|설명|
| ---|---|---|
|table*|일정 ID|일정 선택|
|id*|항목 ID|이벤트 선택|

별표(*)는 속성이 필수 사항임을 의미합니다.

##### 출력 세부 정보
CalendarEvent: 커넥터별 일정 이벤트 모델 클래스입니다.

| 속성 이름 | 데이터 형식 | 설명 |
|---|---|---|
|Id|string|이벤트의 고유 식별자입니다.|
|Attendees|array|이벤트의 참석자 목록입니다.|
|본문|정의되지 않음|이벤트와 연결된 메시지 본문입니다.|
|BodyPreview|string|이벤트와 연결된 메시지 미리 보기입니다.|
|범주|array|이벤트와 연결된 범주입니다.|
|ChangeKey|string|이벤트 개체의 버전을 식별합니다. 이벤트가 변경될 때마다 ChangeKey도 변경됩니다.|
|DateTimeCreated|string|이벤트가 생성된 날짜 및 시간입니다.|
|DateTimeLastModified|string|이벤트가 마지막으로 수정된 날짜 및 시간입니다.|
|끝|string|이벤트의 종료 시간입니다.|
|EndTimeZone|string|모임 종료 시간의 시간대를 지정합니다. 이 값은 Windows에 정의된 대로여야 합니다(예: '태평양 표준시').|
|HasAttachments|부울|이벤트에 첨부 파일이 있으면 true로 설정합니다.|
|중요도  
|string|이벤트의 중요도(낮음, 보통, 높음)입니다.|
|IsAllDay|부울|이벤트가 하루 종일 진행되는 경우 true로 설정합니다.|
|IsCancelled|부울|이벤트가 취소된 경우 true로 설정합니다.|
|IsOrganizer|부울|메시지 보낸 사람이 이끌이인 경우 true로 설정합니다.|
|위치|정의되지 않음|이벤트의 위치입니다.|
|이끌이|정의되지 않음|이벤트의 이끌이입니다.|
|되풀이|정의되지 않음|이벤트의 되풀이 패턴입니다.|
|미리 알림|정수|이벤트 시작을 미리 알리는 시간(분)입니다.|
|ResponseRequested|부울|이벤트가 수락 또는 거절될 때 보낸 사람이 응답하고 싶다면 true로 설정합니다.|
|ResponseStatus|정의되지 않음|이벤트 메시지에 대한 응답으로 보낸 응답 형식을 나타냅니다.|
|SeriesMasterId|string|Series Master 이벤트 유형에 대한 고유 식별자입니다.|
|ShowAs|string|사용 가능 또는 사용 중으로 표시합니다.|
|시작|string|이벤트의 시간입니다.|
|StartTimeZone|string|모임 시작 시간의 시간대를 지정합니다. 이 값은 Windows에 정의된 대로여야 합니다(예: "태평양 표준시").|
|제목  
|string|이벤트 제목입니다.|
|형식|string|이벤트 유형: Single Instance, Occurrence, Exception 또는 Series Master.|
|WebLink|string|이벤트와 연결된 메시지 미리 보기입니다.|


#### 이벤트 삭제
이 작업은 일정에서 이벤트를 삭제합니다.

|속성 이름| 표시 이름|설명|
| ---|---|---|
|table*|일정 ID|일정 선택|
|id*|Id|이벤트 선택|

별표(*)는 속성이 필수 사항임을 의미합니다.

##### 출력 세부 정보
없음


#### 이벤트 업데이트
이 작업은 일정에서 이벤트를 업데이트합니다.

|속성 이름| 표시 이름|설명|
| ---|---|---|
|table*|일정 ID|일정 선택|
|id*|Id|이벤트 선택|
|item*|항목|업데이트할 이벤트|

별표(*)는 속성이 필수 사항임을 의미합니다.

##### 출력 세부 정보
CalendarEvent: 커넥터별 일정 이벤트 모델 클래스입니다.

| 속성 이름 | 데이터 형식 | 설명 |
|---|---|---|
|Id|string|이벤트의 고유 식별자입니다.|
|Attendees|array|이벤트의 참석자 목록입니다.|
|본문|정의되지 않음|이벤트와 연결된 메시지 본문입니다.|
|BodyPreview|string|이벤트와 연결된 메시지 미리 보기입니다.|
|범주|array|이벤트와 연결된 범주입니다.|
|ChangeKey|string|이벤트 개체의 버전을 식별합니다. 이벤트가 변경될 때마다 ChangeKey도 변경됩니다.|
|DateTimeCreated|string|이벤트가 생성된 날짜 및 시간입니다.|
|DateTimeLastModified|string|이벤트가 마지막으로 수정된 날짜 및 시간입니다.|
|끝|string|이벤트의 종료 시간입니다.|
|EndTimeZone|string|모임 종료 시간의 시간대를 지정합니다. 이 값은 Windows에 정의된 대로여야 합니다(예: '태평양 표준시').|
|HasAttachments|부울|이벤트에 첨부 파일이 있으면 true로 설정합니다.|
|중요도  
|string|이벤트의 중요도(낮음, 보통, 높음)입니다.|
|IsAllDay|부울|이벤트가 하루 종일 진행되는 경우 true로 설정합니다.|
|IsCancelled|부울|이벤트가 취소된 경우 true로 설정합니다.|
|IsOrganizer|부울|메시지 보낸 사람이 이끌이인 경우 true로 설정합니다.|
|위치|정의되지 않음|이벤트의 위치입니다.|
|이끌이|정의되지 않음|이벤트의 이끌이입니다.|
|되풀이|정의되지 않음|이벤트의 되풀이 패턴입니다.|
|미리 알림|정수|이벤트 시작을 미리 알리는 시간(분)입니다.|
|ResponseRequested|부울|이벤트가 수락 또는 거절될 때 보낸 사람이 응답하고 싶다면 true로 설정합니다.|
|ResponseStatus|정의되지 않음|이벤트 메시지에 대한 응답으로 보낸 응답 형식을 나타냅니다.|
|SeriesMasterId|string|Series Master 이벤트 유형에 대한 고유 식별자입니다.|
|ShowAs|string|사용 가능 또는 사용 중으로 표시합니다.|
|시작|string|이벤트의 시간입니다.|
|StartTimeZone|string|모임 시작 시간의 시간대를 지정합니다. 이 값은 Windows에 정의된 대로여야 합니다(예: "태평양 표준시").|
|제목  
|string|이벤트 제목입니다.|
|형식|string|이벤트 유형: Single Instance, Occurrence, Exception 또는 Series Master.|
|WebLink|string|이벤트와 연결된 메시지 미리 보기입니다.|


#### 새 이벤트를 만들 때
이 작업은 일정에서 새 파일을 만들 때 흐름을 트리거합니다.

|속성 이름| 표시 이름|설명|
| ---|---|---|
|table*|일정 ID|일정 선택|
|$filter|필터 쿼리|반환된 항목을 제한할 ODATA 필터 쿼리|
|$orderby|Order By|항목의 순서를 지정하는 ODATA orderBy 쿼리|
|$skip|숫자 건너뛰며 세기|건너뛸 항목의 수(기본값 = 0)|
|$top|최대 가져오기 수|검색할 항목의 최대 수(기본값 = 256)|

별표(*)는 속성이 필수 사항임을 의미합니다.

##### 출력 세부 정보
CalendarItemsList: 일정 항목의 목록

| 속성 이름 | 데이터 형식 | 설명 |
|---|---|---|
|value|array|일정 항목의 목록|


#### 이벤트를 수정할 때
이 작업은 일정에서 이벤트를 수정할 때 흐름을 트리거합니다.

|속성 이름| 표시 이름|설명|
| ---|---|---|
|table*|일정 ID|일정 선택|
|$filter|필터 쿼리|반환된 항목을 제한할 ODATA 필터 쿼리|
|$orderby|Order By|항목의 순서를 지정하는 ODATA orderBy 쿼리|
|$skip|숫자 건너뛰며 세기|건너뛸 항목의 수(기본값 = 0)|
|$top|최대 가져오기 수|검색할 항목의 최대 수(기본값 = 256)|

별표(*)는 속성이 필수 사항임을 의미합니다.

##### 출력 세부 정보
CalendarItemsList: 일정 항목의 목록


| 속성 이름 | 데이터 형식 | 설명 |
|---|---|---|
|value|array|일정 항목의 목록|


#### 연락처 폴더 가져오기
이 작업은 사용 가능한 연락처 폴더를 나열합니다.

이 호출에 대한 매개 변수는 없습니다.

##### 출력 세부 정보
TablesList

| 속성 이름 | 데이터 형식 |
|---|---|
|value|array|


#### 연락처 가져오기
이 작업은 연락처 폴더에서 연락처를 가져옵니다.

|속성 이름| 표시 이름|설명|
| ---|---|---|
|table*|폴더 ID|검색할 연락처 폴더의 고유 식별자|
|$filter|필터 쿼리|반환된 항목을 제한할 ODATA 필터 쿼리|
|$orderby|Order By|항목의 순서를 지정하는 ODATA orderBy 쿼리|
|$skip|숫자 건너뛰며 세기|건너뛸 항목의 수(기본값 = 0)|
|$top|최대 가져오기 수|검색할 항목의 최대 수(기본값 = 256)|

별표(*)는 속성이 필수 사항임을 의미합니다.

##### 출력 세부 정보
ContactList: 연락처 목록

| 속성 이름 | 데이터 형식 | 설명 |
|---|---|---|
|value|array|연락처 목록|


#### 연락처 만들기
이 작업은 연락처 폴더에 새 연락처를 만듭니다.

|속성 이름| 표시 이름|설명|
| ---|---|---|
|table*|폴더 ID|연락처 폴더 선택|
|item*|항목|만들 연락처|

별표(*)는 속성이 필수 사항임을 의미합니다.

##### 출력 세부 정보
Contact: 연락처

| 속성 이름 | 데이터 형식 | 설명 |
|---|---|---|
|Id|string|연락처의 고유 식별자입니다.|
|ParentFolderId|string|연락처의 상위 폴더 ID입니다.|
|Birthday|string|연락처의 생일입니다.|
|FileAs|string|연락처가 파일화되는 이름입니다.|
|DisplayName|string|연락처의 표시 이름입니다.|
|GivenName|string|연락처의 지정된 이름입니다.|
|Initials|string|연락처의 머리글자입니다.|
|MiddleName|string|연락처의 중간 이름입니다.|
|NickName|string|연락처의 별명입니다.|
|Surname|string|연락처의 성입니다.|
|제목|string|연락처의 제목입니다.|
|Generation|string|연락처의 세대입니다.|
|EmailAddresses|array|연락처의 전자 메일 주소입니다.|
|ImAddresses|array|연락처의 인스턴트 메시징(IM) 주소입니다.|
|JobTitle|string|연락처의 직함입니다.|
|CompanyName|string|연락처의 회사 이름입니다.|
|Department|string|연락처의 부서입니다.|
|OfficeLocation|string|연락처의 사무실 위치입니다.|
|Profession|string|연락처의 전문 분야입니다.|
|BusinessHomePage|string|연락처의 비즈니스 홈 페이지입니다.|
|AssistantName|string|연락처의 도우미 이름입니다.|
|Manager|string|연락처의 관리자 이름입니다.|
|HomePhones|array|연락처의 집 전화 번호입니다.|
|BusinessPhones|array|연락처의 회사 전화 번호입니다.|
|MobilePhone1|string|연락처의 휴대폰 번호입니다.|
|HomeAddress|정의되지 않음|연락처의 집 주소입니다.|
|BusinessAddress|정의되지 않음|연락처의 회사 주소입니다.|
|OtherAddress|정의되지 않음|연락처의 기타 주소입니다.|
|YomiCompanyName|string|연락처의 음성 일본어 회사 이름입니다.|
|YomiGivenName|string|연락처의 음성 일본어 지정된 이름(이름)입니다.|
|YomiSurname|string|연락처의 음성 일본어 성입니다.|
|범주|array|연락처와 연결된 범주입니다.|
|ChangeKey|string|이벤트 개체의 버전을 식별합니다.|
|DateTimeCreated|string|연락처가 생성된 시간입니다.|
|DateTimeLastModified|string|연락처가 수정된 시간입니다.|


#### 연락처 가져오기
이 작업은 연락처 폴더에서 특정 연락처를 가져옵니다.

|속성 이름| 표시 이름|설명|
| ---|---|---|
|table*|폴더 ID|연락처 폴더 선택|
|id*|항목 ID|검색할 연락처의 고유 식별자|

별표(*)는 속성이 필수 사항임을 의미합니다.

##### 출력 세부 정보
Contact: 연락처

| 속성 이름 | 데이터 형식 | 설명 |
|---|---|---|
|Id|string|연락처의 고유 식별자입니다.|
|ParentFolderId|string|연락처의 상위 폴더 ID입니다.|
|Birthday|string|연락처의 생일입니다.|
|FileAs|string|연락처가 파일화되는 이름입니다.|
|DisplayName|string|연락처의 표시 이름입니다.|
|GivenName|string|연락처의 지정된 이름입니다.|
|Initials|string|연락처의 머리글자입니다.|
|MiddleName|string|연락처의 중간 이름입니다.|
|NickName|string|연락처의 별명입니다.|
|Surname|string|연락처의 성입니다.|
|제목|string|연락처의 제목입니다.|
|Generation|string|연락처의 세대입니다.|
|EmailAddresses|array|연락처의 전자 메일 주소입니다.|
|ImAddresses|array|연락처의 인스턴트 메시징(IM) 주소입니다.|
|JobTitle|string|연락처의 직함입니다.|
|CompanyName|string|연락처의 회사 이름입니다.|
|Department|string|연락처의 부서입니다.|
|OfficeLocation|string|연락처의 사무실 위치입니다.|
|Profession|string|연락처의 전문 분야입니다.|
|BusinessHomePage|string|연락처의 비즈니스 홈 페이지입니다.|
|AssistantName|string|연락처의 도우미 이름입니다.|
|Manager|string|연락처의 관리자 이름입니다.|
|HomePhones|array|연락처의 집 전화 번호입니다.|
|BusinessPhones|array|연락처의 회사 전화 번호입니다.|
|MobilePhone1|string|연락처의 휴대폰 번호입니다.|
|HomeAddress|정의되지 않음|연락처의 집 주소입니다.|
|BusinessAddress|정의되지 않음|연락처의 회사 주소입니다.|
|OtherAddress|정의되지 않음|연락처의 기타 주소입니다.|
|YomiCompanyName|string|연락처의 음성 일본어 회사 이름입니다.|
|YomiGivenName|string|연락처의 음성 일본어 지정된 이름(이름)입니다.|
|YomiSurname|string|연락처의 음성 일본어 성입니다.|
|범주|array|연락처와 연결된 범주입니다.|
|ChangeKey|string|이벤트 개체의 버전을 식별합니다.|
|DateTimeCreated|string|연락처가 생성된 시간입니다.|
|DateTimeLastModified|string|연락처가 수정된 시간입니다.|


#### 연락처 삭제
이 작업은 연락처 폴더에서 연락처를 삭제합니다.

|속성 이름| 표시 이름|설명|
| ---|---|---|
|table*|폴더 ID|연락처 폴더 선택|
|id*|Id|삭제할 연락처의 고유 식별자|

별표(*)는 속성이 필수 사항임을 의미합니다.

##### 출력 세부 정보
없음


#### 연락처 업데이트
이 작업은 연락처 폴더에서 연락처를 업데이트합니다.

|속성 이름| 표시 이름|설명|
| ---|---|---|
|table*|폴더 ID|연락처 폴더 선택|
|id*|Id|업데이트할 연락처의 고유 식별자|
|item*|항목|업데이트할 연락처 항목|

별표(*)는 속성이 필수 사항임을 의미합니다.

##### 출력 세부 정보
Contact: 연락처

| 속성 이름 | 데이터 형식 | 설명 |
|---|---|---|
|Id|string|연락처의 고유 식별자입니다.|
|ParentFolderId|string|연락처의 상위 폴더 ID입니다.|
|Birthday|string|연락처의 생일입니다.|
|FileAs|string|연락처가 파일화되는 이름입니다.|
|DisplayName|string|연락처의 표시 이름입니다.|
|GivenName|string|연락처의 지정된 이름입니다.|
|Initials|string|연락처의 머리글자입니다.|
|MiddleName|string|연락처의 중간 이름입니다.|
|NickName|string|연락처의 별명입니다.|
|Surname|string|연락처의 성입니다.|
|제목|string|연락처의 제목입니다.|
|Generation|string|연락처의 세대입니다.|
|EmailAddresses|array|연락처의 전자 메일 주소입니다.|
|ImAddresses|array|연락처의 인스턴트 메시징(IM) 주소입니다.|
|JobTitle|string|연락처의 직함입니다.|
|CompanyName|string|연락처의 회사 이름입니다.|
|Department|string|연락처의 부서입니다.|
|OfficeLocation|string|연락처의 사무실 위치입니다.|
|Profession|string|연락처의 전문 분야입니다.|
|BusinessHomePage|string|연락처의 비즈니스 홈 페이지입니다.|
|AssistantName|string|연락처의 도우미 이름입니다.|
|Manager|string|연락처의 관리자 이름입니다.|
|HomePhones|array|연락처의 집 전화 번호입니다.|
|BusinessPhones|array|연락처의 회사 전화 번호입니다.|
|MobilePhone1|string|연락처의 휴대폰 번호입니다.|
|HomeAddress|정의되지 않음|연락처의 집 주소입니다.|
|BusinessAddress|정의되지 않음|연락처의 회사 주소입니다.|
|OtherAddress|정의되지 않음|연락처의 기타 주소입니다.|
|YomiCompanyName|string|연락처의 음성 일본어 회사 이름입니다.|
|YomiGivenName|string|연락처의 음성 일본어 지정된 이름(이름)입니다.|
|YomiSurname|string|연락처의 음성 일본어 성입니다.|
|범주|array|연락처와 연결된 범주입니다.|
|ChangeKey|string|이벤트 개체의 버전을 식별합니다.|
|DateTimeCreated|string|연락처가 생성된 시간입니다.|
|DateTimeLastModified|string|연락처가 수정된 시간입니다.|



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
|500|내부 서버 오류. 알 수 없는 오류 발생|
|기본값|작업이 실패했습니다.|


## 다음 단계

[논리 앱 만들기](../app-service-logic/app-service-logic-create-a-logic-app.md). [API 목록](apis-list.md)에서 논리 앱의 사용 가능한 다른 커넥터를 확인하세요.

<!---HONumber=AcomDC_0727_2016-->