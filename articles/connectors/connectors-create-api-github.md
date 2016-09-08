<properties
pageTitle="GitHub | Microsoft Azure"
description="Azure 앱 서비스로 논리 앱을 만듭니다. GitHub은 서비스를 호스팅하는 웹 기반 Git 리포지토리입니다. 고유한 기능을 추가할 뿐 아니라 Git의 분산된 수정 버전 제어 및 SCM(소스 코드 관리) 기능을 모두 제공합니다."
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

# GitHub 커넥터 시작

GitHub은 서비스를 호스팅하는 웹 기반 Git 리포지토리입니다. 고유한 기능을 추가할 뿐 아니라 Git의 분산된 수정 버전 제어 및 SCM(소스 코드 관리) 기능을 모두 제공합니다.

>[AZURE.NOTE] 이 버전의 문서는 논리 앱 2015-08-01-preview 스키마 버전에 적용됩니다.

이제 논리 앱을 만들어 시작할 수 있습니다. [논리 앱 만들기](../app-service-logic/app-service-logic-create-a-logic-app.md)를 참조하세요.

## 트리거 및 작업

GitHub 커넥터를 작업으로 사용할 수 있으며 트리거를 가지고 있습니다. 모든 커넥터는 JSON 및 XML 형식의 데이터를 지원합니다.

 GitHub 커넥터에서는 다음과 같은 작업 및/또는 트리거를 사용할 수 있습니다.

### GitHub 작업
다음 작업을 수행할 수 있습니다.

|작업|설명|
|--- | ---|
|[CreateIssue](connectors-create-api-github.md#createissue)|문제 만들기|
### GitHub 트리거
다음 이벤트를 수신할 수 있습니다.

|트리거 | 설명|
|--- | ---|
|문제가 열릴 경우|문제가 열립니다|
|문제가 닫힐 경우|문제가 닫힙니다|
|문제가 할당될 경우|문제가 할당됩니다|


## GitHub에 대한 연결 만들기
GitHub로 논리 앱을 만들려면 먼저 **연결**을 만든 후에 다음 속성에 대한 세부 정보를 제공해야 합니다.

|속성| 필수|설명|
| ---|---|---|
|위임|예|GitHub 자격 증명 제공|
연결을 만든 후에 사용하여 작업을 실행하고 이 문서에 설명된 트리거에 대한 수신을 대기할 수 있습니다.

>[AZURE.INCLUDE [GitHub에 대한 연결을 만드는 단계](../../includes/connectors-create-api-github.md)]

>[AZURE.TIP] 다른 논리 앱에서 이 연결을 사용할 수 있습니다.

## GitHub에 대한 참조
적용 버전: 1.0

## CreateIssue
문제 만들기: 문제를 만듭니다

```POST: /repos/{repositoryOwner}/{repositoryName}/issues```

| Name| 데이터 형식|필수|위치|기본값|설명|
| ---|---|---|---|---|---|
|repositoryOwner|string|yes|path|없음|리포지토리 소유자|
|repositoryName|string|yes|path|없음|리포지토리 이름|
|issueBasicDetails| |yes|body|없음|문제 세부 정보|

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


## IssueOpened
문제가 열릴 경우: 문제가 열립니다

```GET: /trigger/issueOpened```

이 호출에 대한 매개 변수는 없습니다.
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


## IssueClosed
문제가 닫힐 경우: 문제가 닫힙니다

```GET: /trigger/issueClosed```

이 호출에 대한 매개 변수는 없습니다.
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


## IssueAssigned
문제가 할당될 경우: 문제가 할당됩니다

```GET: /trigger/issueAssigned```

이 호출에 대한 매개 변수는 없습니다.
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

### IssueBasicDetailsModel


| 속성 이름 | 데이터 형식 | 필수 |
|---|---|---|
|title|string|예 |
|body|string|예 |
|assignee|string|예 |



### IssueDetailsModel


| 속성 이름 | 데이터 형식 | 필수 |
|---|---|---|
|title|string|예 |
|body|string|예 |
|assignee|string|예 |
|number|string|아니요 |
|state|string|아니요 |
|created\_at|string|아니요 |
|repository\_url|string|아니요 |


## 다음 단계
[논리 앱 만들기](../app-service-logic/app-service-logic-create-a-logic-app.md)

<!---HONumber=AcomDC_0824_2016-->