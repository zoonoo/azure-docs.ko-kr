<properties
    pageTitle="논리 앱에 Office 365 사용자 커넥터 추가 | Microsoft Azure"
    description="REST API 매개 변수를 사용하는 Office 365 사용자 커넥터 개요"
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
ms.date="08/18/2016"
ms.author="deonhe"/>

# Office 365 사용자 커넥터 시작

Office 365 사용자에 연결하여 프로필 가져오기, 사용자 검색 등을 수행합니다.

>[AZURE.NOTE] 이 버전의 문서는 논리 앱 2015-08-01-preview 스키마 버전에 적용됩니다.

Office 365 사용자를 사용하여 다음을 수행할 수 있습니다.

- Office 365 사용자에서 가져온 데이터를 기반으로 비즈니스 흐름을 빌드합니다.
- 부하 직원 가져오기, 관리자의 사용자 프로필 가져오기 등의 작업을 사용합니다. 이러한 작업을 사용하여 응답을 가져오고 출력을 다른 작업에 사용할 수 있도록 설정합니다. 예를 들어 사용자의 부하 직원을 가져온 다음 이 정보를 사용하여 SQL Azure 데이터베이스를 업데이트합니다.

논리 앱에 작업을 추가하려면 [논리 앱 만들기](../app-service-logic/app-service-logic-create-a-logic-app.md)를 참조하세요.

## 트리거 및 작업

Office 365 사용자 커넥터에서는 다음과 같은 작업을 사용할 수 있습니다. 트리거는 없습니다.

| 트리거 | 작업|
| --- | --- |
|없음 | <ul><li>관리자 가져오기</li><li>내 프로필 가져오기</li><li>부하 직원 가져오기</li><li>사용자 프로필 가져오기</li><li>사용자 검색</li></ul>|

모든 커넥터는 JSON 및 XML 형식의 데이터를 지원합니다.


## Office 365 사용자에 대한 연결 만들기

논리 앱에 이 커넥터를 추가할 때 Office 365 사용자 계정에 로그인하고 논리 앱을 계정에 연결해야 합니다.

>[AZURE.INCLUDE [Office 365 사용자에 대한 연결을 만드는 단계](../../includes/connectors-create-api-office365users.md)]

연결을 만든 후 사용자 ID 등의 Office 365 사용자 속성을 입력합니다. 이 항목의 **REST API 참조**에서는 이러한 속성에 대해 설명합니다.

>[AZURE.TIP] 다른 논리 앱에서 이와 동일한 Office 365 사용자 연결을 사용할 수 있습니다.


## Office 365 사용자 REST API 참조
적용 버전: 1.0

### 내 프로필 가져오기 
현재 사용자에 대한 프로필을 검색합니다. ```GET: /users/me```

이 호출에 대한 매개 변수는 없습니다.

#### 응답

|Name|설명|
|---|---|
|200|작업이 성공했습니다.|
|202|작업이 성공했습니다.|
|400|BadRequest|
|401|권한 없음|
|403|사용할 수 없음|
|500|내부 서버 오류|
|기본값|작업이 실패했습니다.|


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



### 부하 직원을 가져옵니다. 
부하 직원을 가져옵니다. ```GET: /users/{userId}/directReports```

| Name| 데이터 형식|필수|위치|기본값|설명|
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



## 개체 정의

#### User: 사용자 모델 클래스입니다.

|속성 이름 | 데이터 형식 |필수
|---|---|---|
|DisplayName|string|no|
|GivenName|string|no|
|Surname|string|no|
|Mail|string|no|
|MailNickname|string|no|
|TelephoneNumber|string|no|
|AccountEnabled|부울|no|
|Id|string|yes
|UserPrincipalName|string|no|
|부서|string|no|
|JobTitle|string|no|
|MobilePhone|string|no|


## 다음 단계

[논리 앱 만들기](../app-service-logic/app-service-logic-create-a-logic-app.md)

[API 목록](apis-list.md)으로 돌아갑니다.

<!--References-->
[5]: https://portal.azure.com
[7]: ./media/connectors-create-api-office365-users/aad-tenant-applications.PNG
[8]: ./media/connectors-create-api-office365-users/aad-tenant-applications-add-appinfo.PNG
[9]: ./media/connectors-create-api-office365-users/aad-tenant-applications-add-app-properties.PNG
[10]: ./media/connectors-create-api-office365-users/contoso-aad-app.PNG
[11]: ./media/connectors-create-api-office365-users/contoso-aad-app-configure.PNG

<!---HONumber=AcomDC_0824_2016-->