<properties
	pageTitle="Azure AD B2C 미리 보기 | Microsoft Azure"
	description="응용 프로그램의 형식은 Azure AD B2C 미리 보기에서 빌드할 수 있습니다."
	services="active-directory-b2c"
	documentationCenter=""
	authors="dstrockis"
	manager="msmbaldwin"
	editor=""/>

<tags
	ms.service="active-directory-b2c"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="12/22/2015"
	ms.author="dastrock"/>

# Azure AD B2C 미리 보기: 응용 프로그램의 형식

Azure AD B2C는 모두 업계 표준 프로토콜 [OAuth 2.0](active-directory-b2c-reference-protocols.md) 및/또는 [OpenID Connect](active-directory-b2c-reference-protocols.md)를 기반으로 하는 다양한 최신 앱 아키텍처에 대한 인증을 지원합니다. 이 문서에서는 선호하는 언어 또는 플랫폼에 독립적으로 빌드할 수 있는 앱 형식에 대해 간략하게 설명합니다. [코드를 바로 시작](active-directory-b2c-overview.md#getting-started)하기 전에 높은 수준의 시나리오를 이해하는 데 도움이 됩니다.

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]

## 기본 사항
Azure AD B2C를 사용하는 모든 앱은 [Azure 포털](https://portal.azure.com)을 통해 [B2C 디렉터리](active-directory-b2c-get-started.md)에 등록해야 합니다. 앱 등록 프로세스는 몇 개의 값을 수집하고 앱에 할당합니다.

- 앱을 고유하게 식별하는 **응용 프로그램 ID**
- 응답을 다시 앱으로 보내는 데 사용할 수 있는 **리디렉션 URI**
- 다른 몇 가지 시나리오 관련 값. 자세한 내용은 [앱 등록](active-directory-b2c-app-registration.md) 방법을 참조하세요.

등록되면 앱이 Azure AD v2.0 끝점에 요청을 보내기 위해 Azure AD와 통신합니다.

```
https://login.microsoftonline.com/common/oauth2/v2.0/authorize
https://login.microsoftonline.com/common/oauth2/v2.0/token
```

Azure AD B2C에 전송되는 각 요청은 **정책**을 지정합니다. 정책은 Azure AD의 동작을 제어하고 이러한 끝점을 사용하여 고도로 사용자 지정 가능한 사용자 환경 집합을 만들 수 있습니다. 일부 공통 정책에는 등록 정책, 로그인 정책 및 프로필 편집 정책이 포함됩니다. 정책과 익숙하지 않은 경우 계속 읽기 전에 Azure AD B2C의 [확장할 수 있는 정책 프레임워크](active-directory-b2c-reference-policies.md)에 대해 읽어야 합니다.

v2.0 끝점과 각 앱의 상호 작용은 높은 수준에서 비슷한 패턴을 따릅니다.

1. 앱이 최종 사용자를 v2.0 끝점으로 보내어 [정책](active-directory-b2c-reference-policies.md)을 실행합니다.
2. 사용자는 정책 정의에 따라 정책을 완료합니다.
4. 앱이 v2.0 끝점에서 일종의 보안 토큰을 받습니다.
5. 앱이 보안 토큰을 사용하여 보호된 정보 또는 리소스에 액세스합니다.
6. 리소스 서버가 보안 토큰의 유효성을 검사하여 액세스 권한을 부여할 수 있는지 확인합니다.
7. 앱이 주기적으로 보안 토큰을 새로 고칩니다.

<!-- TODO: Need a page for libraries to link to -->
이러한 단계는 각각 빌드하는 앱 형식에 따라 약간씩 다르며, 자동으로 세부 정보를 처리하는 오픈 소스 라이브러리가 있습니다.

## 웹앱
서버에서 호스팅되며 브라우저를 통해 액세스하는 웹앱(.NET, PHP, Java, Ruby, Python, 노드 등)의 경우 Azure AD B2C는 로그인, 등록 및 프로필 관리를 포함한 모든 사용자 환경에 [OpenID Connect](active-directory-b2c-reference-protocols.md)를 지원합니다. OpenID Connect의 Azure AD B2C 구현에서 웹앱은 Azure AD로 인증 요청을 발급하여 이러한 사용자 환경을 시작합니다. 요청의 결과는 `id_token`, 즉 사용자 ID를 확인하고 클레임 형태로 사용자 정보를 제공하는 보안 토큰입니다.

```
// Partial raw id_token
eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6ImtyaU1QZG1Cd...

// Partial content of a decoded id_token
{
	"name": "John Smith",
	"email": "john.smith@gmail.com",
	"oid": "d9674823-dffc-4e3f-a6eb-62fe4bd48a58"
	...
}
```

[B2C 토큰 참조](active-directory-b2c-reference-tokens.md)에서 앱이 사용할 수 있는 모든 토큰 및 클레임 형식에 대해 알아볼 수 있습니다.

웹앱에서 [정책](active-directory-b2c-reference-policies.md)의 각 실행에서는 이러한 수준 높은 단계를 수행합니다.

![웹앱 스윔 레인 이미지](./media/active-directory-b2c-apps/webapp.png)

Azure AD에서 받은 공개 서명 키를 사용하여 id\_token의 유효성을 검사하기만 하면 사용자 ID를 확인하고 후속 페이지 요청 시 사용자를 식별하는 데 사용할 수 있는 세션 쿠키를 설정할 수 있습니다.

이 시나리오의 작동 방식을 확인하려면 [시작](active-directory-b2c-overview.md#getting-started) 섹션의 웹앱 로그인 코드 샘플 중 하나를 체험해 보세요.

간단한 로그인뿐 아니라 웹 서버 앱은 일부 백 엔드 웹 서비스에 액세스해야 할 수도 있습니다. 이 경우 웹앱은 약간 다른 [OpenID Connect 흐름](active-directory-b2c-reference-oidc.md)을 수행하고 권한 부여 코드를 사용하여 토큰을 획득하며 토큰을 새로 고칠 수 있습니다. 이 시나리오는 아래 [Web API 섹션](#web-apis)에서 설명합니다.

<!--, and in our [WebApp-WebAPI Getting Started topic](active-directory-b2c-devquickstarts-web-api-dotnet.md).-->

## Web API
또한 Azure AD B2C을 사용하여 앱의 RESTful Web API와 같은 웹 서비스의 보안을 유지할 수 있습니다. Web API는 OAuth 2.0을 사용하여 자신의 데이터를 보호하고 토큰을 사용하여 들어오는 HTTP 요청을 인증할 수 있습니다. Web API 호출자는 HTTP 요청의 인증 헤더에 토큰을 추가합니다.

```
GET /api/items HTTP/1.1
Host: www.mywebapi.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6...
Accept: application/json
...
```

Web API는 토큰을 사용하여 API 호출자의 ID를 확인하고 토큰에 인코드된 클레임에서 호출자에 대한 정보를 추출할 수 있습니다. [Azure AD B2C 토큰 참조](active-directory-b2c-reference-tokens.md)에서 앱이 사용할 수 있는 모든 토큰 및 클레임 형식에 대해 알아볼 수 있습니다.

> [AZURE.NOTE]Azure AD B2C 미리 보기는 현재 고유한 잘 알려진 클라이언트에서 액세스하는 Web API만을 지원합니다. 예를 들어 전체 앱이 iOS 앱, Android 앱 및 백 엔드 Web API를 포함할 수도 있습니다. 이 아키텍처를 완전히 지원합니다. 현재 지원되지 않는 사항은 다른 iOS 응용 프로그램과 같은 타사 클라이언트를 사용하여 동일한 Web API에 액세스하는 작업입니다. 실제로 전체 앱의 각 구성 요소는 모두 단일 응용 프로그램 ID를 공유해야 합니다.

Web API는 웹앱, 데스크톱 및 모바일 앱, 단일 페이지 앱, 서버 쪽 데몬 및 다른 Web API까지 포함하여 모든 유형의 클라이언트에서 토큰을 받을 수 있습니다. 한 예로 Web API를 호출하는 웹앱에 대한 전체 흐름을 살펴보겠습니다.

![웹앱 Web API 스윔 레인 이미지](./media/active-directory-b2c-apps/webapi.png)

authorization\_code, refresh\_token 및 토큰을 가져오는 세부 단계에 대한 자세한 내용은 [OAuth 2.0 프로토콜](active-directory-b2c-reference-oauth-code.md)을 참조하세요.

Azure AD B2C로 Web API를 보호하는 방법을 알아보려면 [시작 섹션](active-directory-b2c-overview.md#getting-started)에서 Web API 자습서를 확인하세요.
	
## 모바일 및 네이티브 앱
모바일 및 데스크톱 앱과 같은 장치에 설치된 앱은 사용자 대신 백 엔드 서비스 또는 Web API에 액세스해야 하는 경우가 많습니다. 네이티브 앱에 사용자 지정된 ID 관리 환경을 추가하고 Azure AD B2C 및 [OAuth 2.0 인증 코드 흐름](active-directory-b2c-reference-oauth-code.md)을 사용하여 안전하게 백 엔드 서비스를 호출할 수 있습니다.

이 흐름에서 앱은 [정책](active-directory-b2c-reference-policies.md)을 실행하고 사용자 정책을 완료한 후에 Azure AD에서 authorization\_code를 수신합니다. authorization\_code는 현재 로그인한 사용자를 대신하여 응용 프로그램의 사용 권한을 나타내어 백 엔드 서비스를 호출합니다. 앱은 백그라운드에서 authoriztion\_code를 id\_token 및 refresh\_token으로 교환할 수 있습니다. 앱은 id\_token을 사용하여 HTTP 요청 시 백 엔드 Web API에 인증할 수 있고 refresh\_token을 사용하여 이전 토큰 만료 시 새 id\_token을 가져올 수 있습니다.

> [AZURE.NOTE]Azure AD B2C 미리 보기는 현재 앱 자체의 백 엔드 웹 서비스에 액세스하는 데 사용되는 id\_tokens를 가져오도록 지원합니다. 예를 들어 전체 앱이 iOS 앱, Android 앱 및 백 엔드 Web API를 포함할 수도 있습니다. 이 아키텍처를 완전히 지원합니다. 현재 지원되지 않는 사항은 iOS 앱이 access\_tokens OAuth 2.0을 사용하여 타사 Web API에 액세스하는 작업입니다. 실제로 전체 앱의 각 구성 요소는 모두 단일 응용 프로그램 ID를 공유해야 합니다.

![네이티브 앱 스윔 레인 이미지](./media/active-directory-b2c-apps/native.png)

## 현재 미리 보기 제한 사항
이러한 형식의 앱은 현재 Azure AD B2C 미리 보기에서 지원되지 않지만 조만간 일반 공급될 예정입니다. Azure AD B2C 미리 보기에 대한 추가 제한 사항은 [제한 사항 문서](active-directory-b2c-limitations.md)에서 설명합니다.

### 단일 페이지 앱(Javascript)
대부분의 최신 앱에는 주로 Javascript로 작성되고 AngularJS, Ember.js, Durandal 등과 같은 SPA 프레임워크로도 작성되는 단일 페이지 앱 프런트 엔드가 있습니다. 일반 공급 Azure AD 서비스는 OAuth 2.0 암시적 흐름을 사용하여 이러한 앱을 지원하지만 Azure AD B2C에서는 이 흐름을 아직 사용할 수 없습니다. 곧 제공될 예정입니다.

### 디먼/서버 쪽 앱
장기 실행 프로세스를 포함하거나 사용자 없이 작동하는 앱은 Web API아 같은 보안 리소스에 액세스하는 방법도 필요합니다. 이러한 앱은 OAuth 2.0 클라이언트 자격 증명 흐름을 사용하여 사용자의 위임된 ID 대신 앱 ID로 인증하고 토큰을 가져올 수 있습니다.

이 흐름은 현재 Azure AD B2C에서 지원되지 않으므로 대화형 사용자 흐름이 발생한 후에만 앱이 토큰을 가져올 수 있습니다. 조만간 클라이언트 자격 증명 흐름이 추가될 예정입니다.

### Web API 체인(On-Behalf-Of)
많은 아키텍처에는 다른 다운스트림 Web API를 호출해야 하는 Web API가 포함되어 있으며 둘 다 Azure AD B2C로 보안됩니다. 이 시나리오는 Web API 백 엔드를 포함하는 네이티브 클라이언트에서 일반적이며, Web API 백 엔드가 다시 Azure AD Graph API와 같은 Microsoft 온라인 서비스를 호출합니다.

On-Behalf-Of 흐름이라고도 하는 OAuth 2.0 Jwt 전달자 자격 증명 권한 부여를 사용하여 이 연결된 Web API 시나리오를 지원할 수 있습니다. 그러나 On-Behalf-Of 흐름은 현재 Azure AD B2C 미리 보기에 구현되어 있지 않습니다.

<!---HONumber=AcomDC_0107_2016-->