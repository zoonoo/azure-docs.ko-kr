<properties
pageTitle="MailChimp | Microsoft Azure"
description="Azure 앱 서비스로 논리 앱을 만듭니다. MailChimp는 비즈니스에서 마케팅 전자 메일, 자동화된 메시지 및 대상 캠페인 전송을 비롯한 전자 메일 마케팅 작업을 관리하고 자동화할 수 있는 SaaS 서비스입니다."
services="app-servicelogic"	
documentationCenter=".net,nodejs,java" 	
authors="msftman"	
manager="erikre"	
editor=""
tags="connectors" />

<tags
ms.service="app-service-logic"
ms.devlang="multiple"
ms.topic="article"
ms.tgt_pltfrm="na"
ms.workload="integration"
ms.date="05/17/2016"
ms.author="deonhe"/>

# MailChimp 커넥터 시작



MailChimp 커넥터는 다음에서 사용할 수 있습니다.

- [논리 앱](../app-service-logic/app-service-logic-what-are-logic-apps.md)  
- [PowerApps](http://powerapps.microsoft.com)  
- [흐름](http://flows.microsoft.com)  

>[AZURE.NOTE] 이 버전의 문서는 논리 앱 2015-08-01-preview 스키마 버전에 적용됩니다.

이제 논리 앱을 만들어 시작할 수 있습니다. [논리 앱 만들기](../app-service-logic/app-service-logic-create-a-logic-app.md)를 참조하세요.

## 트리거 및 작업

MailChimp 커넥터를 작업으로 사용할 수 있으며 트리거를 가지고 있습니다. 모든 커넥터는 JSON 및 XML 형식의 데이터를 지원합니다.

 MailChimp 커넥터에서는 다음과 같은 작업 및/또는 트리거를 사용할 수 있습니다.

### MailChimp 작업
다음 작업을 수행할 수 있습니다.

|작업|설명|
|--- | ---|
|[newcampaign](connectors-create-api-mailchimp.md#newcampaign)|캠페인 형식, 받는 사람 목록 및 캠페인 설정(예: 제목 줄, 제목, from\_name 및 reply\_to)을 기반으로 새 캠페인 만들기|
|[newlist](connectors-create-api-mailchimp.md#newlist)|MailChimp 계정에서 새 목록 만들기|
|[addmember](connectors-create-api-mailchimp.md#addmember)|목록 멤버 추가 또는 업데이트|
|[removemember](connectors-create-api-mailchimp.md#removemember)|목록에서 멤버 삭제|
|[updatemember](connectors-create-api-mailchimp.md#updatemember)|특정 목록 멤버에 대한 정보 업데이트|
### MailChimp 트리거
다음 이벤트를 수신할 수 있습니다.

|트리거 | 설명|
|--- | ---|
|목록에 멤버를 추가한 경우|목록에 새 멤버를 추가한 경우 워크플로 트리거|
|새 목록을 만든 경우|새 목록을 만든 경우 워크플로 트리거|


## MailChimp에 대한 연결 만들기
MailChimp로 논리 앱을 만들려면 먼저 **연결**을 만든 후에 다음 속성에 대한 세부 정보를 제공해야 합니다.

|속성| 필수|설명|
| ---|---|---|
|신뢰|예|MailChimp 자격 증명 제공|

>[AZURE.INCLUDE [MailChimp에 대한 연결을 만드는 단계](../../includes/connectors-create-api-mailchimp.md)]

>[AZURE.TIP] 다른 논리 앱에서 이 연결을 사용할 수 있습니다.

## MailChimp에 대한 참조
적용 버전: 1.0

## newcampaign
새 캠페인: 캠페인 형식, 받는 사람 목록 및 캠페인 설정(예: 제목 줄, 제목, from\_name 및 reply\_to)을 기반으로 새 캠페인 만들기

```POST: /campaigns```

| 이름| 데이터 형식|필수|위치|기본값|설명|
| ---|---|---|---|---|---|
|newCampaignRequest| |yes|body|없음|새 캠페인 요청 매개 변수가 있는 본문에 보낼 Json 개체|

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


## newlist
새 목록: MailChimp 계정에서 새 목록 만들기

```POST: /lists```

| 이름| 데이터 형식|필수|위치|기본값|설명|
| ---|---|---|---|---|---|
|newListRequest| |yes|body|없음|새 캠페인 요청 매개 변수가 있는 본문에 보낼 Json 개체|

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


## addmember
목록에 멤버 추가: 목록 멤버 추가 또는 업데이트

```POST: /lists/{list_id}/members```

| 이름| 데이터 형식|필수|위치|기본값|설명|
| ---|---|---|---|---|---|
|list\_id|string|yes|path|없음|목록에 대한 고유 ID|
|newMemberInList| |yes|body|없음|새 멤버 정보가 있는 본문에 보낼 Json 개체|

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


## removemember
목록에서 멤버 제거: 목록에서 멤버 삭제

```DELETE: /lists/replacemailwithhash/{list_id}/members/{member_email}```

| 이름| 데이터 형식|필수|위치|기본값|설명|
| ---|---|---|---|---|---|
|list\_id|string|yes|path|없음|목록에 대한 고유 ID|
|member\_email|string|yes|path|없음|삭제할 멤버의 전자 메일 주소|

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


## updatemember
멤버 정보 업데이트: 특정 목록 멤버에 대한 정보 업데이트

```PATCH: /lists/replacemailwithhash/{list_id}/members/{member_email}```

| 이름| 데이터 형식|필수|위치|기본값|설명|
| ---|---|---|---|---|---|
|list\_id|string|yes|path|없음|목록에 대한 고유 ID|
|member\_email|string|yes|path|없음|업데이트할 멤버의 고유한 전자 메일 주소|
|updateMemberInListRequest| |yes|body|없음|업데이트된 멤버 정보가 있는 본문에 보낼 Json 개체|

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


## OnMemberSubscribed
목록에 멤버를 추가한 경우: 목록에 새 멤버를 추가한 경우 워크플로 트리거

```GET: /trigger/lists/{list_id}/members```

| 이름| 데이터 형식|필수|위치|기본값|설명|
| ---|---|---|---|---|---|
|list\_id|string|yes|path|없음|목록에 대한 고유 ID|

#### 응답

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


## OnCreateList
새 목록을 만든 경우: 새 목록을 만든 경우 워크플로 트리거

```GET: /trigger/lists```

이 호출에 대한 매개 변수는 없습니다.
#### 응답

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


## 개체 정의 

### NewCampaignRequest


| 속성 이름 | 데이터 형식 | 필수 |
|---|---|---|
|type|string|예 |
|recipients|정의되지 않음|예 |
|설정|정의되지 않음|예 |
|variate\_settings|정의되지 않음|아니요 |
|tracking|정의되지 않음|아니요 |
|rss\_opts|정의되지 않음|아니요 |
|social\_card|정의되지 않음|아니요 |



### 받는 사람


| 속성 이름 | 데이터 형식 | 필수 |
|---|---|---|
|list\_id|string|예 |
|segment\_opts|정의되지 않음|아니요 |



### 설정


| 속성 이름 | 데이터 형식 | 필수 |
|---|---|---|
|subject\_line|string|예 |
|title|string|아니요 |
|from\_name|string|예 |
|reply\_to|string|예 |
|use\_conversation|부울|아니요 |
|to\_name|string|아니요 |
|folder\_id|정수|아니요 |
|authenticate|부울|아니요 |
|auto\_footer|부울|아니요 |
|inline\_css|부울|아니요 |
|auto\_tweet|부울|아니요 |
|auto\_fb\_post|array|아니요 |
|fb\_comments|부울|아니요 |



### Variate\_Settings


| 속성 이름 | 데이터 형식 | 필수 |
|---|---|---|
|winner\_criteria|string|아니요 |
|wait\_time|정수|아니요 |
|test\_size|정수|아니요 |
|subject\_lines|array|아니요 |
|send\_times|array|아니요 |
|from\_names|array|아니요 |
|reply\_to\_addresses|array|아니요 |



### 추적


| 속성 이름 | 데이터 형식 | 필수 |
|---|---|---|
|opens|부울|아니요 |
|html\_clicks|부울|아니요 |
|text\_clicks|부울|아니요 |
|goal\_tracking|부울|아니요 |
|ecomm360|부울|아니요 |
|google\_analytics|string|아니요 |
|clicktale|string|아니요 |
|salesforce|정의되지 않음|아니요 |
|highrise|정의되지 않음|아니요 |
|capsule|정의되지 않음|아니요 |



### RSS\_Opts


| 속성 이름 | 데이터 형식 | 필수 |
|---|---|---|
|feed\_url|string|아니요 |
|frequency|string|아니요 |
|constrain\_rss\_img|string|아니요 |
|schedule|정의되지 않음|아니요 |



### Social\_Card


| 속성 이름 | 데이터 형식 | 필수 |
|---|---|---|
|image\_url|string|아니요 |
|description|string|아니요 |
|title|string|아니요 |



### Segment\_Opts


| 속성 이름 | 데이터 형식 | 필수 |
|---|---|---|
|saved\_segment\_id|정수|아니요 |
|match|string|아니요 |



### Salesforce


| 속성 이름 | 데이터 형식 | 필수 |
|---|---|---|
|campaign|부울|아니요 |
|정보|부울|아니요 |



### Highrise


| 속성 이름 | 데이터 형식 | 필수 |
|---|---|---|
|campaign|부울|아니요 |
|정보|부울|아니요 |



### Capsule


| 속성 이름 | 데이터 형식 | 필수 |
|---|---|---|
|정보|부울|아니요 |



### 일정


| 속성 이름 | 데이터 형식 | 필수 |
|---|---|---|
|시간|정수|아니요 |
|daily\_send|정의되지 않음|아니요 |
|weekly\_send\_day|string|아니요 |
|monthly\_send\_date|number|아니요 |



### Daily\_Send


| 속성 이름 | 데이터 형식 | 필수 |
|---|---|---|
|sunday|부울|아니요 |
|monday|부울|아니요 |
|tuesday|부울|아니요 |
|wednesday|부울|아니요 |
|thursday|부울|아니요 |
|friday|부울|아니요 |
|saturday|부울|아니요 |



### CampaignResponseModel


| 속성 이름 | 데이터 형식 | 필수 |
|---|---|---|
|id|string|아니요 |
|type|string|아니요 |
|create\_time|string|아니요 |
|archive\_url|string|아니요 |
|status|string|아니요 |
|emails\_sent|정수|아니요 |
|send\_time|string|아니요 |
|content\_type|string|아니요 |
|recipient|array|아니요 |
|설정|정의되지 않음|아니요 |
|variate\_settings|정의되지 않음|아니요 |
|tracking|정의되지 않음|아니요 |
|rss\_opts|정의되지 않음|아니요 |
|ab\_split\_opts|정의되지 않음|아니요 |
|social\_card|정의되지 않음|아니요 |
|report\_summary|정의되지 않음|아니요 |
|delivery\_status|정의되지 않음|아니요 |
|_\_links|array|No |



### AB\_Split\_Opts


| 속성 이름 | 데이터 형식 | 필수 |
|---|---|---|
|split\_test|string|아니요 |
|pick\_winner|string|아니요 |
|wait\_units|string|아니요 |
|wait\_time|정수|아니요 |
|split\_size|정수|아니요 |
|from\_name\_a|string|아니요 |
|from\_name\_b|string|아니요 |
|reply\_email\_a|string|아니요 |
|reply\_email\_b|string|아니요 |
|subject\_a|string|아니요 |
|subject\_b|string|아니요 |
|send\_time\_a|string|아니요 |
|send\_time\_b|string|아니요 |
|send\_time\_winner|string|아니요 |



### Report\_Summary


| 속성 이름 | 데이터 형식 | 필수 |
|---|---|---|
|opens|정수|아니요 |
|unique\_opens|정수|아니요 |
|open\_rate|number|아니요 |
|clicks|정수|아니요 |
|subscriber\_clicks|number|아니요 |
|click\_rate|number|아니요 |



### Delivery\_Status


| 속성 이름 | 데이터 형식 | 필수 |
|---|---|---|
|사용|부울|아니요 |
|can\_cancel|부울|아니요 |
|status|string|아니요 |
|emails\_sent|정수|아니요 |
|emails\_canceled|정수|아니요 |



### 링크


| 속성 이름 | 데이터 형식 | 필수 |
|---|---|---|
|rel|string|아니요 |
|href|string|아니요 |
|메서드|string|아니요 |
|targetSchema|string|아니요 |
|schema|string|아니요 |



### NewListRequest


| 속성 이름 | 데이터 형식 | 필수 |
|---|---|---|
|name|string|예 |
|contact|정의되지 않음|예 |
|permission\_reminder|string|예 |
|use\_archive\_bar|부울|아니요 |
|campaign\_defaults|정의되지 않음|예 |
|notify\_on\_subscribe|string|아니요 |
|notify\_on\_unsubscribe|string|아니요 |
|email\_type\_option|부울|예 |
|visibility|string|아니요 |



### 연락처


| 속성 이름 | 데이터 형식 | 필수 |
|---|---|---|
|company|string|예 |
|address1|string|예 |
|address2|string|아니요 |
|city|string|예 |
|state|string|예 |
|zip|string|예 |
|country|string|예 |
|phone|string|예 |



### Campaign\_Defaults


| 속성 이름 | 데이터 형식 | 필수 |
|---|---|---|
|from\_name|string|예 |
|from\_email|string|예 |
|subject|string|아니요 |
|language|string|예 |



### CreateNewListResponseModel


| 속성 이름 | 데이터 형식 | 필수 |
|---|---|---|
|id|string|예 |
|name|string|예 |
|contact|정의되지 않음|예 |
|permission\_reminder|string|예 |
|use\_archive\_bar|부울|아니요 |
|campaign\_defaults|정의되지 않음|예 |
|notify\_on\_subscribe|string|아니요 |
|notify\_on\_unsubscribe|string|아니요 |
|date\_created|string|아니요 |
|list\_rating|정수|아니요 |
|email\_type\_option|부울|예 |
|subscribe\_url\_short|string|아니요 |
|subscribe\_url\_long|string|아니요 |
|beamer\_address|string|아니요 |
|visibility|string|아니요 |
|modules|array|아니요 |
|stats|정의되지 않음|아니요 |
|_\_links|array|No |



### 통계


| 속성 이름 | 데이터 형식 | 필수 |
|---|---|---|
|member\_count|정수|아니요 |
|unsubscribe\_count|정수|아니요 |
|cleaned\_count|정수|아니요 |
|member\_count\_since\_send|정수|아니요 |
|unsubscribe\_count\_since\_send|정수|아니요 |
|cleaned\_count\_since\_send|정수|아니요 |
|campaign\_count|정수|아니요 |
|campaign\_last\_sent|정수|아니요 |
|merge\_field\_count|정수|아니요 |
|avg\_sub\_rate|number|아니요 |
|avg\_unsub\_rate|number|아니요 |
|target\_sub\_rate|number|아니요 |
|open\_rate|number|아니요 |
|click\_rate|number|아니요 |
|last\_sub\_date|string|아니요 |
|last\_unsub\_date|string|아니요 |



### GetListsResponseModel


| 속성 이름 | 데이터 형식 | 필수 |
|---|---|---|
|lists|array|아니요 |
|total\_items|정수|아니요 |



### NewMemberInListRequest


| 속성 이름 | 데이터 형식 | 필수 |
|---|---|---|
|email\_type|string|아니요 |
|status|string|예 |
|merge\_fields|정의되지 않음|아니요 |
|interests|string|아니요 |
|language|string|아니요 |
|vip|부울|아니요 |
|location|정의되지 않음|아니요 |
|email\_address|string|예 |



### FirstAndLastName


| 속성 이름 | 데이터 형식 | 필수 |
|---|---|---|
|FNAME|string|아니요 |
|LNAME|string|아니요 |



### 위치


| 속성 이름 | 데이터 형식 | 필수 |
|---|---|---|
|latitude|number|아니요 |
|longitude|number|아니요 |



### MemberResponseModel


| 속성 이름 | 데이터 형식 | 필수 |
|---|---|---|
|id|string|아니요 |
|email\_address|string|아니요 |
|unique\_email\_id|string|아니요 |
|email\_type|string|아니요 |
|status|string|아니요 |
|merge\_fields|정의되지 않음|아니요 |
|interests|string|아니요 |
|stats|정의되지 않음|아니요 |
|ip\_signup|string|아니요 |
|timestamp\_signup|string|아니요 |
|ip\_opt|string|아니요 |
|timestamp\_opt|string|아니요 |
|member\_rating|정수|아니요 |
|last\_changed|string|아니요 |
|language|string|아니요 |
|vip|부울|아니요 |
|email\_client|string|아니요 |
|location|정의되지 않음|아니요 |
|last\_note|정의되지 않음|아니요 |
|list\_id|string|아니요 |
|_\_links|array|No |



### Last\_Note


| 속성 이름 | 데이터 형식 | 필수 |
|---|---|---|
|note\_id|정수|아니요 |
|created\_at|string|아니요 |
|created\_by|string|아니요 |
|note|string|아니요 |



### GetAllMembersResponseModel


| 속성 이름 | 데이터 형식 | 필수 |
|---|---|---|
|members|array|아니요 |
|list\_id|string|아니요 |
|total\_items|정수|아니요 |



### Object


| 속성 이름 | 데이터 형식 | 필수 |
|---|---|---|



### UpdateMemberInListRequest


| 속성 이름 | 데이터 형식 | 필수 |
|---|---|---|
|email\_address|string|아니요 |
|email\_type|string|아니요 |
|status|string|예 |
|merge\_fields|정의되지 않음|아니요 |
|interests|string|아니요 |
|language|string|아니요 |
|vip|부울|아니요 |
|location|정의되지 않음|아니요 |



### GetMembersResponseModel


| 속성 이름 | 데이터 형식 | 필수 |
|---|---|---|
|members|array|아니요 |
|list\_id|string|아니요 |
|total\_items|정수|아니요 |



### AddUserResponseModel


| 속성 이름 | 데이터 형식 | 필수 |
|---|---|---|
|id|string|예 |
|email\_address|string|예 |
|unique\_email\_id|string|아니요 |
|email\_type|string|아니요 |
|status|string|아니요 |
|merge\_fields|정의되지 않음|예 |
|interests|string|아니요 |
|stats|정의되지 않음|아니요 |
|ip\_signup|string|아니요 |
|timestamp\_signup|string|아니요 |
|ip\_opt|string|아니요 |
|timestamp\_opt|string|아니요 |
|member\_rating|정수|아니요 |
|last\_changed|string|아니요 |
|language|string|아니요 |
|vip|부울|아니요 |
|email\_client|string|아니요 |
|location|정의되지 않음|아니요 |
|last\_note|정의되지 않음|아니요 |
|list\_id|string|아니요 |
|_\_links|array|No |


## 다음 단계
[논리 앱 만들기](../app-service-logic/app-service-logic-create-a-logic-app.md)

<!---HONumber=AcomDC_0518_2016-->