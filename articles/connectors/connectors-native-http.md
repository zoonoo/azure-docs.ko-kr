<properties
	pageTitle="논리 앱에 HTTP 작업 추가 | Microsoft Azure"
	description="HTTP 작업 및 해당 속성 개요"
	services=""
	documentationCenter="" 
	authors="jeffhollan"
	manager="erikre"
	editor=""
	tags="connectors"/>

<tags
   ms.service="logic-apps"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na" 
   ms.date="07/15/2016"
   ms.author="jehollan"/>

# HTTP 작업 시작

HTTP 작업을 사용하여 조직에 대한 워크플로를 확장하고 HTTP를 통해 끝점과 통신할 수 있습니다.

- 관리하는 웹 사이트가 중단되면 활성화되는(트리거) 논리 앱 워크플로를 만듭니다.
- HTTP를 통해 모든 끝점과 통신하여 다른 서비스로 워크플로를 확장합니다.

논리 앱에서 HTTP 작업 사용을 시작하려면 [논리 앱 만들기](../app-service-logic/app-service-logic-create-a-logic-app.md)를 참조하세요.

---

## HTTP 트리거 사용

트리거는 논리 앱에 정의된 워크플로를 시작하는 데 사용할 수 있는 이벤트입니다. [트리거에 대해 자세히 알아보세요](connectors-overview.md).

논리 앱 디자이너에서 HTTP 트리거를 설정하는 방법의 예제 시퀀스는 다음과 같습니다.

1. 논리 앱에 HTTP 트리거를 추가합니다.
1. 폴링할 HTTP 끝점에 대한 매개 변수를 입력합니다.
1. 폴링할 빈도에 대해 되풀이 간격을 수정합니다.
1. 이제 각 검사 중에 반환된 모든 콘텐츠에 대해 논리 앱이 실행됩니다.

![HTTP 트리거](./media/connectors-native-http/using-trigger.png)

### HTTP 트리거 작동 방식

HTTP 트리거는 되풀이 간격에 따라 HTTP 끝점을 호출합니다. 기본적으로 300보다 작은 모든 HTTP 응답 코드에서는 논리 앱이 실행됩니다. 논리 앱이 실행되어야 하는지를 결정하기 위해 HTTP 호출 후에 평가될 조건을 코드 보기에 추가할 수 있습니다. 다음은 반환된 상태 코드가 `400`보다 크거나 같을 때마다 발생하는 HTTP 트리거의 예입니다.

```javascript
"Http": 
{
	"conditions": [
		{
			"expression": "@greaterOrEquals(triggerOutputs()['statusCode'], 400)"
		}
	],
	"inputs": {
		"method": "GET",
		"uri": "https://blogs.msdn.microsoft.com/logicapps/",
		"headers": {
			"accept-language": "en"
		}
	},
	"recurrence": {
		"frequency": "Second",
		"interval": 15
	},
	"type": "Http"
}
```

HTTP 트리거 매개 변수에 대한 자세한 내용은 [MSDN에서 확인](https://msdn.microsoft.com/library/azure/mt643939.aspx#HTTP-trigger)할 수 있습니다.

## HTTP 작업 사용
	
작업은 논리 앱에 정의된 워크플로에 의해 수행되는 작업입니다. [작업에 대해 자세히 알아봅니다.](connectors-overview.md)

1. **새 단계** 단추를 선택합니다.
1. **작업 추가**를 선택합니다.
1. 작업 검색 상자에 "HTTP"를 입력하여 HTTP 작업을 나열합니다.

	![HTTP 작업 선택](./media/connectors-native-http/using-action-1.png)

1. HTTP 호출에 필요한 모든 매개 변수를 추가합니다.

	![HTTP 작업 완료](./media/connectors-native-http/using-action-2.png)

1. 도구 모음 왼쪽 위에서 저장을 클릭하면 논리 앱이 저장되고 게시됩니다(활성화).

---

## 기술 세부 정보

이 커넥터가 지원하는 트리거 및 작업에 대한 세부 정보는 다음과 같습니다.

## HTTP 트리거

트리거는 논리 앱에 정의된 워크플로를 시작하는 데 사용할 수 있는 이벤트입니다. [트리거에 대해 자세히 알아보세요.](connectors-overview.md) HTTP 커넥터에는 1개의 트리거가 있습니다.

|트리거|설명|
|---|---|
|HTTP|HTTP 호출을 수행하고 응답 콘텐츠를 반환합니다.|

## HTTP 작업

작업은 논리 앱에 정의된 워크플로에 의해 수행되는 작업입니다. [작업에 대해 자세히 알아봅니다.](connectors-overview.md) HTTP 커넥터에는 1개의 가능한 작업이 있습니다.

|작업|설명|
|---|---|
|HTTP|HTTP 호출을 수행하고 응답 콘텐츠를 반환합니다.|

## HTTP 세부 정보

HTTP 커넥터에는 1개의 가능한 작업이 있습니다. 아래에는 각 작업, 해당 필수 및 옵션 입력 필드, 사용과 관련된 해당 출력 세부 정보에 대한 정보가 나와 있습니다.

### HTTP 요청
HTTP 아웃바운드 요청을 수행합니다. *는 필수 필드를 의미합니다.

|표시 이름|속성 이름|설명|
|---|---|---|
|Method*|메서드|사용할 HTTP 동사|
|URI*|uri|HTTP 요청에 대한 URI|
|헤더|headers|포함할 HTTP 헤더의 JSON 개체|
|본문|body|HTTP 요청 본문|
|인증|authentication|[세부 정보](#authentication)|
<br>

**출력 세부 정보**

HTTP 응답

|속성 이름|데이터 형식|설명|
|---|---|---|
|헤더|object|응답 헤더|
|본문|object|응답 개체|
|상태 코드|int|HTTP 상태 코드|

## 인증

논리 앱을 사용하면 HTTP 끝점에 대해 다른 유형의 인증을 사용할 수 있습니다. 이 인증은 HTTP, [HTTP + Swagger](./connectors-native-http-swagger.md) 및 [HTTP 웹후크](./connectors-native-webhook.md) 커넥터와 함께 사용할 수 있습니다. 다음 인증 유형은 구성할 수 있습니다.

* [기본 인증](#basic-authentication)
* [ClientCertificate 인증](#client-certificate-authentication)
* [ActiveDirectoryOAuth 인증](#azure-active-directory-oauth-authentication)

#### 기본 인증

기본 인증 개체는 기본 인증에 필요합니다. *는 필수 필드를 의미합니다.

|속성 이름|데이터 형식|설명|
|---|---|---|
|형식*|type|인증 유형입니다. 기본 인증의 경우 이 값은 `Basic`이어야 합니다.|
|사용자 이름*|username|인증하기 위한 사용자 이름|
|암호*|password|인증하기 위한 암호|

>[AZURE.TIP] 정의에서 검색할 수 없는 암호를 사용하려는 경우 `securestring` 매개 변수 및 `@parameters()` [워크플로 정의 함수](http://aka.ms/logicappdocs)를 사용합니다.

따라서 다음과 같은 개체를 인증 필드에 만듭니다.

```javascript
{
	"type": "Basic",
	"username": "user",
	"password": "test"
}
```

#### 클라이언트 인증서 인증

기본 인증 개체는 클라이언트 인증서 인증에 필요합니다. *는 필수 필드를 의미합니다.

|속성 이름|데이터 형식|설명|
|---|---|---|
|형식*|type|인증 유형입니다. SSL 클라이언트 인증서의 경우 이 값은 `ClientCertificate`여야 합니다.|
|PFX*|pfx|PFX 파일의 Base64 인코딩 콘텐츠|
|암호*|password|PFX 파일에 액세스하기 위한 암호|

>[AZURE.TIP] `securestring` 매개 변수 및 `@parameters()` [워크플로 정의 함수](http://aka.ms/logicappdocs)를 사용하여 저장한 후 정의에서 읽을 수 없는 매개 변수를 사용할 수 있습니다.

예:

```javascript
{
	"type": "ClientCertificate",
	"pfx": "aGVsbG8g...d29ybGQ=",
	"password": "@parameters('myPassword')"
}
```

#### Azure Active Directory OAuth 인증

기본 인증 개체는 Azure Active Directory OAuth 인증에 필요합니다. *는 필수 필드를 의미합니다.

|속성 이름|데이터 형식|설명|
|---|---|---|
|형식*|type|인증 유형입니다. ActiveDirectoryOAuth의 경우 이 값은 `ActiveDirectoryOAuth`여야 합니다.|
|테넌트*|tenant|Azure AD 테넌트의 테넌트 식별자|
|대상*|audience|이 값은 `https://management.core.windows.net/`로 설정됩니다.|
|클라이언트 ID*|clientId|Azure AD 응용 프로그램의 클라이언트 식별자를 제공합니다.|
|암호*|secret|토큰을 요청하는 클라이언트의 암호|

>[AZURE.TIP] `securestring` 매개 변수 및 `@parameters()` [워크플로 정의 함수](http://aka.ms/logicappdocs)를 사용하여 저장한 후 정의에서 읽을 수 없는 매개 변수를 사용할 수 있습니다.

예:

```javascript
{
	"type": "ActiveDirectoryOAuth",
	"tenant": "72f988bf-86f1-41af-91ab-2d7cd011db47",
	"audience": "https://management.core.windows.net/",
	"clientId": "34750e0b-72d1-4e4f-bbbe-664f6d04d411",
	"secret": "hcqgkYc9ebgNLA5c+GDg7xl9ZJMD88TmTJiJBgZ8dFo="
}
```

---

## 다음 단계

논리 앱 및 커뮤니티를 사용하여 앞으로 이동하는 방법에 대한 세부 정보는 다음과 같습니다.

## 논리 앱 만들기

지금 플랫폼을 사용해 보고 [논리 앱을 만드세요](../app-service-logic/app-service-logic-create-a-logic-app.md). [API 목록](apis-list.md)에서 논리 앱의 사용 가능한 다른 커넥터를 확인할 수 있습니다.

<!---HONumber=AcomDC_0727_2016-->