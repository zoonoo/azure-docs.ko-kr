<properties
	pageTitle="논리 앱에 Office 365 사용자 API 추가 | Microsoft Azure"
	description="REST API 매개 변수를 사용하는 Office 365 사용자 API 개요"
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
ms.date="02/22/2016"
ms.author="deonhe"/>

# Office 365 사용자 API 시작

Office 365 사용자 연결 공급자를 사용하면 Office 365 계정을 사용하여 조직의 사용자 프로필에 액세스할 수 있습니다. 자신의 프로필, 사용자의 프로필, 사용자의 관리자 또는 부하 직원 프로필을 가져오고 사용자 프로필을 업데이트하는 등 여러 작업을 수행할 수 있습니다.

>[AZURE.NOTE] 이 버전의 문서는 논리 앱 2015-08-01-preview 스키마 버전에 적용됩니다. 2014-12-01-preview 스키마 버전에 대한 내용을 보려면 [Office 365 API](../app-service-logic/app-service-logic-connector-office365.md)를 클릭하세요.


Office 365 사용자를 사용하여 다음을 수행할 수 있습니다.

* 논리 앱 빌드
* 파워 앱 빌드

PowerApps 엔터프라이즈에서 API를 추가하는 방법을 보려면 [Register an API in PowerApps](../power-apps/powerapps-register-from-available-apis.md)(PowerApps에서 API 등록)로 이동하세요.

논리 앱에 작업을 추가하려면 [논리 앱 만들기](../app-service-logic/app-service-logic-create-a-logic-app.md)를 참조하세요.

## 트리거 및 작업에 대한 정보

Office 365 사용자 API를 작업으로 사용할 수 있습니다. 트리거는 없습니다. 모든 API는 JSON 및 XML 형식의 데이터를 지원합니다.

 Office365 사용자 API에서는 다음과 같은 동작 및/또는 트리거를 사용할 수 있습니다.

### Office 365 사용자 작업
다음 작업을 수행할 수 있습니다.

|작업|설명|
|--- | ---|
|MyProfile|현재 사용자에 대한 프로필을 검색합니다.|
|UserProfile|특정 사용자 프로필을 검색합니다.|
|Manager|지정된 사용자의 관리자에 대한 사용자 프로필을 검색합니다.|
|DirectReports|부하 직원을 가져옵니다.|
|SearchUser|사용자 프로필 검색 결과를 가져옵니다.|
## Office 365 사용자에 대한 연결 만들기
Office365 사용자 API를 사용하려면 먼저 **연결**을 만든 다음 이러한 속성에 대한 세부 정보를 제공합니다.

|속성| 필수|설명|
| ---|---|---|
|신뢰|예|Office365 자격 증명 제공|


>[AZURE.TIP] 다른 논리 앱에서 이 연결을 사용할 수 있습니다.

## Office 365 사용자 REST API 참조
#### 이 문서 적용 버전: 1.0


### 내 프로필 가져오기 


 현재 사용자에 대한 프로필을 검색합니다. ```GET: /users/me```

이 호출에 대한 매개 변수는 없습니다.
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
------



### 사용자 프로필 가져오기 


 특정 사용자 프로필을 검색합니다. ```GET: /users/{userId}```



| 이름| 데이터 형식|필수|위치|기본값|설명|
| ---|---|---|---|---|---|
|userId|string|yes|path|없음|사용자 계정 이름 또는 전자 메일 ID|


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
------



### 관리자 가져오기 


 지정된 사용자의 관리자에 대한 사용자 프로필을 검색합니다. ```GET: /users/{userId}/manager```



| 이름| 데이터 형식|필수|위치|기본값|설명|
| ---|---|---|---|---|---|
|userId|string|yes|path|없음|사용자 계정 이름 또는 전자 메일 ID|


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
------



### 부하 직원을 가져옵니다. 


 부하 직원을 가져옵니다. ```GET: /users/{userId}/directReports```



| 이름| 데이터 형식|필수|위치|기본값|설명|
| ---|---|---|---|---|---|
|userId|string|yes|path|없음|사용자 계정 이름 또는 전자 메일 ID|


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
------



### 사용자 검색 


 사용자 프로필 검색 결과를 가져옵니다. ```GET: /users```



| 이름| 데이터 형식|필수|위치|기본값|설명|
| ---|---|---|---|---|---|
|searchTerm|string|no|쿼리|없음|검색 문자열(적용 대상: 표시 이름, 이름, 성, 메일, 메일 애칭 및 사용자 계정 이름)|


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
------



## 개체 정의: 

 **User**: 사용자 모델 클래스입니다.

User에 대한 필수 속성:

Id

**모든 속성**:


| 이름 | 데이터 형식 |
|---|---|
|DisplayName|string|
|GivenName|string|
|Surname|string|
|Mail|string|
|MailNickname|string|
|TelephoneNumber|string|
|AccountEnabled|부울|
|Id|string|
|UserPrincipalName|string|
|Department|string|
|JobTitle|string|


## 다음 단계
PowerApps 엔터프라이즈에 Office 365 API를 추가한 후 해당 앱에서 API를 사용할 [권한을 사용자에게 부여](../power-apps/powerapps-manage-api-connection-user-access.md)합니다.

[논리 앱 만들기](../app-service-logic/app-service-logic-create-a-logic-app.md)

<!---HONumber=AcomDC_0224_2016-->