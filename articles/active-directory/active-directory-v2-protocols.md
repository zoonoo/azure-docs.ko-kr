<properties
	pageTitle="앱 모델 v2.0 프로토콜 | Microsoft Azure"
	description="Azure AD v2.0 앱 모델 공개 미리 보기에서 지원하는 프로토콜입니다."
	services="active-directory"
	documentationCenter=""
	authors="dstrockis"
	manager="mbaldwin"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/12/2015"
	ms.author="dastrock"/>

# 앱 모델 v2.0 미리 보기: 프로토콜 - OAuth 2.0 및 OpenID Connect

v2.0 앱 모델은 업계 표준 프로토콜, OpenID Connect 및 OAuth 2.0을 지원하여 앱에 대한 identity-as-a-service를 제공합니다. 서비스는 표준을 준수하지만 이러한 프로토콜의 두 구현 간에는 약간의 차이가 있을 수 있습니다. 오픈 소스 라이브러리 중 하나를 사용하는 대신 HTTP 요청을 직접 전송 및 처리하여 코드를 작성하는 경우 여기에 포함된 정보가 유용합니다. <!-- TODO: Need link to libraries above -->

> [AZURE.NOTE]이 정보는 v2.0 앱 모델 공개 미리 보기에 적용됩니다. 일반 공급 Azure AD 서비스와 통합하는 방법에 대한 지침은 [Azure Active Directory 개발자 가이드](active-directory-developers-guide.md)를 참조하세요.

## 토큰
v2.0 앱 모델의 OAuth 2.0 및 OpenID Connect 구현은 JWT로 표현되는 전달자 토큰을 포함하여 전달자 토큰을 광범위하게 활용합니다. 전달자 토큰은 보호된 리소스에 대한 "전달자" 액세스 권한을 부여하는 간단한 보안 토큰입니다. 이런 의미에서, "전달자"는 토큰을 제공할 수 있는 당사자입니다. 이 당사자는 전달자 토큰을 수신하려면 먼저 Azure AD를 사용하여 인증해야 합니다. 하지만 전송 및 저장 시 토큰 보안을 유지하는 데 필요한 단계를 취하지 않는 경우 의도하지 않은 당사자가 토큰을 가로채서 사용할 수 있습니다. 일부 보안 토큰에는 권한 없는 당사자가 사용하는 것을 방지하는 기본 제공 메커니즘이 있지만, 전달자 토큰에는 이러한 메커니즘이 없으므로 전송 계층 보안(HTTPS)과 같은 보안 채널에서 전달자 토큰을 전송해야 합니다. 전달자 토큰이 일반 텍스트 상태로 전송되는 경우 악의적인 사용자가 메시지 가로채기(man-in-the-middle) 공격을 사용해서 토큰을 획득하고 보호된 리소스에 무단으로 액세스하는 데 이 토큰을 사용할 수 있습니다. 나중에 사용하기 위해 전달자 토큰을 저장하거나 캐싱할 때도 동일한 보안 원칙이 적용됩니다. 항상 앱이 안전한 방식으로 전달자 토큰을 전송하고 저장하도록 합니다. 전달자 토큰의 보안 고려 사항을 자세히 알아보려면 [RFC 6750 Section 5](http://tools.ietf.org/html/rfc6750)를 참조하세요.

v2.0 앱 모델에서 사용되는 다양한 토큰 형식에 대한 자세한 내용은 [v2.0 앱 모델 토큰 참조](active-directory-v2-tokens.md)를 참조하세요.

## 기본 사항
v2.0 앱 모델을 사용하는 각 앱을 [apps.dev.microsoft.com](https://apps.dev.microsoft.com)에 등록해야 합니다. 앱 등록 프로세스는 몇 개의 값을 수집하고 앱에 할당합니다.

- 앱을 고유하게 식별하는 **응용 프로그램 ID**
- 응답을 다시 앱으로 보내는 데 사용할 수 있는 **리디렉션 URI** 또는 **패키지 식별자**
- 다른 몇 가지 시나리오 관련 값. 자세한 내용은 [앱 등록](active-directory-v2-app-registration.md) 방법을 참조하세요.

등록되면 앱이 v2.0 끝점에 요청을 보내기 위해 Azure AD와 통신합니다.

```
https://login.microsoftonline.com/common/oauth2/v2.0/authorize
https://login.microsoftonline.com/common/oauth2/v2.0/token
```

거의 모든 OAuth 및 OpenID Connect 흐름에서 교환에 참여하는 다음 네 가지 요소가 있습니다.

![OAuth 2.0 역할](../media/active-directory-v2-flows/protocols_roles.png)

- **권한 부여 서버**는 v2.0 끝점입니다. 사용자 ID를 확인하고 리소스에 대한 액세스 권한을 부여 및 해지하고, 토큰을 발급합니다. ID 공급자라고도 하며 사용자 정보, 해당 액세스 및 흐름의 요소 간 트러스트 관계와 관련된 모든 사항을 안전하게 처리합니다.
- **리소스 소유자**는 일반적으로 최종 사용자입니다. 데이터를 소유하는 당사자이며 제3자가 해당 데이터 또는 리소스에 액세스하도록 허용할 권한이 있습니다.
- **OAuth 클라이언트**는 해당 응용 프로그램 ID로 식별되는 앱입니다. 일반적으로 최종 사용자가 상호 작용하는 요소이며 권한 부여 서버의 토큰을 요청합니다. 리소스 소유자가 리소스에 액세스할 수 있는 권한을 클라이언트에 부여해야 합니다.
- **리소스 서버**는 리소스 또는 데이터가 있는 곳입니다. OAuth 클라이언트를 안전하게 인증하고 권한을 부여할 수 있도록 권한 부여 서버를 신뢰하고 전달자 access\_token을 사용하여 리소스에 대한 액세스 권한을 부여할 수 있도록 합니다.

## OAuth2 인증 코드 흐름
OAuth 2.0 인증 코드 흐름은 [OAuth 2.0 사양의 섹션 4.1](http://tools.ietf.org/html/rfc6749)에서 설명합니다. [웹앱](active-directory-v2-flows.md#web-apps) 및 [기본적으로 설치된 앱](active-directory-v2-flows.md#mobile-and-native-apps)을 포함하여 대부분의 앱 형식에서 인증 및 권한 부여를 수행하는 데 사용됩니다. v2.0 앱 모델을 사용하여 보안된 리소스에 액세스하는 데 사용할 수 있는 access\_token을 앱이 안전하게 획득할 수 있도록 합니다.

다음은 네이티브 앱에 대한 전체 흐름입니다. 각 요청은 아래 섹션에서 자세히 설명합니다. ![OAuth 인증 코드 흐름](../media/active-directory-v2-flows/convergence_scenarios_native.png)

#### 인증 코드 요청
인증 코드 흐름은 클라이언트가 사용자를 `/authorize` 끝점으로 보내는 것으로 시작됩니다. 이 요청에서 클라이언트는 사용자로부터 얻어야 하는 사용 권한을 나타냅니다.

```
GET https://login.microsoftonline.com/common/oauth2/v2.0/authorize?
client_id=2d4d11a2-f814-46a7-890a-274a72a7309e		// Your registered Application Id
&response_type=code
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F 	  // Your registered Redirect Uri, url encoded
&response_mode=query							      // 'query', 'form_post', or 'fragment'
&scope=											   // See table below for scope parameter details.
https%3A%2F%2Fgraph.windows.net%2Fdirectory.read%20
https%3A%2F%2Fgraph.windows.net%2Fdirectory.write
&state=12345						 				 // Any value provided by your app
```

| 매개 변수 | | 설명 |
| ----------------------- | ------------------------------- | ----------------------- |
| client\_id | 필수 | 등록 포털([apps.dev.microsoft.com](https://apps.dev.microsoft.com))에서 앱에 할당한 응용 프로그램 ID입니다. |
| response\_type | 필수 | 인증 코드 흐름에 대한 `code`를 포함해야 합니다. |
| redirect\_uri | 필수 | 앱이 인증 응답을 보내고 받을 수 있는 앱의 redirect\_uri입니다. URL로 인코드되어야 한다는 점을 제외하고 포털에서 등록한 redirect\_uri 중 하나와 정확히 일치해야 합니다. |
| scope | 필수 | 공백으로 구분된 범위 목록입니다. 단일 범위 값은 리소스 및 해당 리소스에 대한 사용 권한이 모두 요청됨을 v2.0 끝점에 나타냅니다. 범위는 `<app identifier URI>/<scope value>` 형태를 사용합니다. 위의 예제에서는 Azure AD Graph API에 대한 앱 식별자 `https://graph.windows.net`이 사용되고 두 개의 사용 권한 `directory.read` 및 `directory.write`가 요청됩니다. 범위에 대한 자세한 설명은 c를 참조하세요. |
| response\_mode | 권장 | 결과 authorization\_code를 앱에 다시 보내는 데 사용해야 하는 방법을 지정합니다. 'query', 'form\_post' 또는 'fragment' 중 하나일 수 있습니다.
| state | 권장 | 토큰 응답에도 반환되는 요청에 포함된 값입니다. 원하는 모든 콘텐츠의 문자열일 수 있습니다. 일반적으로 교차 사이트 요청 위조 공격을 방지하기 위해 임의로 생성된 고유 값이 사용됩니다. 상태는 인증 요청이 발생하기 전 앱의 사용자 상태에 대한 정보(예: 사용한 페이지 또는 보기)를 인코드하는 데에도 사용됩니다. |
| prompt | 선택 사항 | 필요한 사용자 상호 작용 유형을 나타냅니다. 현재 유효한 값은 'login'뿐이며 강제로 사용자가 해당 요청에 자격 증명을 입력하도록 합니다. Single Sign-On은 적용되지 않습니다. |

이 시점에서 사용자에게 자격 증명을 입력하고 인증을 완료하라는 메시지가 표시됩니다. v2.0 끝점은 사용자가 `scope` 쿼리 매개 변수에 표시된 사용 권한에 동의했는지도 확인합니다. 사용자가 이러한 사용 권한 중 하나에 동의하지 않은 경우 필요한 사용 권한에 동의하라는 메시지가 표시됩니다. [사용 권한, 동의 및 다중 테넌트 앱의 세부 정보는 여기에 제공되어 있습니다](active-directory-v2-scopes.md).

사용자가 인증하고 동의하면 v2.0 끝점이 `response_mode` 매개 변수에 지정된 방법을 사용하여 표시된 `redirect_uri`에서 해당 앱에 응답을 반환합니다.

`response_mode=query`를 사용한 성공적인 응답은 다음과 같습니다.

```
GET https://localhost/myapp/?
code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq... 	// the authorization_code, truncated
&session_state=7B29111D-C220-4263-99AB-6F6E135D75EF			   // a value generated by the v2.0 endpoint
&state=12345												  							// the value provided in the request
```

| 매개 변수 | 설명 |
| ----------------------- | ------------------------------- |
| 코드 | 앱이 요청한 authorization\_code입니다. 앱은 인증 코드를 사용하여 대상 리소스에 대한 액세스 토큰을 요청할 수 있습니다. authorization\_code는 수명이 매우 짧으며, 일반적으로 약 10분 후에 만료됩니다. |
| session\_state | 현재 사용자 세션을 식별하는 고유 값입니다. 이 값은 GUID이지만 검사 없이 전달되는 불투명 값으로 처리되어야 합니다. |
| state | 요청에 state 매개 변수가 포함되어 있으면 동일한 값이 응답에도 나타나야 합니다. 앱은 요청 및 응답의 상태 값이 동일한지 확인해야 합니다. |

앱이 적절하게 처리할 수 있도록 `redirect_uri`에 오류 응답을 보낼 수도 있습니다.

```
GET https://localhost/myapp/?
error=access_denied
&error_description=the+user+canceled+the+authentication
```

| 매개 변수 | 설명 |
| ----------------------- | ------------------------------- |
| error | 발생하는 오류 유형을 분류하는 데 사용할 수 있고 오류에 대응하는 데 사용할 수 있는 오류 코드 문자열입니다. |
| error\_description | 개발자가 인증 오류의 근본 원인을 식별하도록 도울 수 있는 특정 오류 메시지입니다. |

#### 액세스 토큰 요청
authorization\_code를 획득하고 사용자가 사용 권한을 부여했으므로 이제 `POST` 요청을 `/token` 끝점으로 보내 `code`를 원하는 리소스에 대한 `access_token`으로 교환할 수 있습니다.

```
POST common/v2.0/oauth2/token HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/json

{
	"grant_type": "authorization_code",
	"client_id": "2d4d11a2-f814-46a7-890a-274a72a7309e",
	"scope": "https://graph.windows.net/directory.read https://graph.windows.net/directory.write",
	"code": "AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq..."
	"client_secret": "zc53fwe80980293klaj9823"  // NOTE: Only required for web apps
}
```

| 매개 변수 | | 설명 |
| ----------------------- | ------------------------------- | --------------------- |
| client\_id | 필수 | 등록 포털([apps.dev.microsoft.com](https://apps.dev.microsoft.com))에서 앱에 할당한 응용 프로그램 ID입니다. |
| grant\_type | 필수 | 인증 코드 흐름에 대한 `authorization_code`여야 합니다. |
| scope | 필수 | 공백으로 구분된 범위 목록입니다. 이 레그에서 요청된 범위가 첫 번째 레그에서 요청된 범위와 동일하거나 하위 집합이어야 합니다. 이 요청에 지정된 범위가 여러 리소스 서버에 걸쳐 있는 경우 v2.0 끝점은 첫 번째 범위에 지정된 리소스에 대한 토큰을 반환합니다. 범위에 대한 자세한 설명은 [사용 권한, 동의 및 범위](active-directory-v2-scopes.md)를 참조하세요. |
| 코드 | 필수 | 흐름의 첫 번째 레그에서 얻은 authorization\_code입니다. |
| client\_secret | 웹앱에 필요 | 앱에 대한 앱 등록 포털에서 만든 응용 프로그램 암호입니다. 장치에 client\_secret을 안정적으로 저장할 수 없으므로 네이티브 앱에서는 사용하면 안 됩니다. 서버 쪽에서 client\_secret을 안전하게 저장할 수 있는 웹앱과 Web API에 필요합니다. |

성공적인 토큰 응답은 다음과 같습니다.

```
{
	"access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...",
	"token_type": "Bearer",
	"expires_in": "3600",
	"expires_on": "1388444763",
	"scope": "https://graph.windows.net/directory.read https://graph.windows.net/directory.write",
	"refresh_token": "AwABAAAAvPM1KaPlrEqdFSBzjqfTGAMxZGUTdM0t4B4...",
	"id_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJub25lIn0.eyJhdWQiOiIyZDRkMTFhMi1mODE0LTQ2YTctOD..."
}
```
| 매개 변수 | 설명 |
| ----------------------- | ------------------------------- |
| access\_token | 요청된 액세스 토큰입니다. 앱은 이 토큰을 사용하여 Web API와 같은 보안 리소스에 인증할 수 있습니다. |
| token\_type | 토큰 유형 값을 나타냅니다. Azure AD는 전달자 유형만 지원합니다. |
| expires\_in | 액세스 토큰이 유효한 기간(초)입니다. |
| expires\_on | 액세스 토큰이 만료되는 시간입니다. 날짜는 epoch 시간에서의 초 수로 표시됩니다. |
| scope | access\_token이 유효한 범위입니다. |
| refresh\_token | OAuth 2.0 새로 고침 토큰입니다. 앱은 현재 액세스 토큰이 만료된 후 이 토큰을 사용하여 추가 액세스 토큰을 획득할 수 있습니다. refresh\_token은 수명이 길며, 오랜 시간 동안 리소스에 대한 액세스를 유지하는 데 사용할 수 있습니다. 자세한 내용은 [v2.0 토큰 참조](active-directory-v2-tokens.md)를 참조하세요. |
| id\_token | 서명되지 않은 JWT(JSON 웹 토큰)입니다. 앱은 이 토큰의 세그먼트를 base64Url로 디코드하여 로그인한 사용자에 대한 정보를 요청할 수 있습니다. 앱은 값을 캐시하고 표시할 수 있지만 권한 부여 또는 보안 경계에 대해 의존해서는 안 됩니다. id\_token에 대한 자세한 내용은 [v2.0 앱 모델 토큰 참조](active-directory-v2-tokens.md)를 참조하세요. |

오류 응답은 다음과 같습니다.

```
{
	"error": "access_denied",
	"error_description": "The user revoked access to the app.",
}
```

| 매개 변수 | 설명 |
| ----------------------- | ------------------------------- |
| error | 발생하는 오류 유형을 분류하는 데 사용할 수 있고 오류에 대응하는 데 사용할 수 있는 오류 코드 문자열입니다. |
| error\_description | 개발자가 인증 오류의 근본 원인을 식별하도록 도울 수 있는 특정 오류 메시지입니다. |

#### 액세스 토큰 사용
`access_token`을 성공적으로 획득했으므로 이제 `Authorization` 헤더에 포함하여 Web API에 대한 요청에 토큰을 사용할 수 있습니다.

```
GET /contoso.onmicrosoft.com/users
Host: https://graph.windows.net
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
```

#### 액세스 토큰 새로 고침
access\_token은 수명이 짧으며, 만료되면 새로 고쳐야 리소스에 계속 액세스할 수 있습니다. 이렇게 하려면 다른 `POST` 요청을 `/authorize` 끝점에 제출해야 하며, 이번에는 `code` 대신 `refresh_token`을 제공해야 합니다.

```
POST common/v2.0/oauth2/token HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/json

{
	"grant_type": "refresh_token",
	"client_id": "2d4d11a2-f814-46a7-890a-274a72a7309e",
	"scope": "https://graph.windows.net/directory.read https://graph.windows.net/directory.write",
	"refresh_token": "AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...",
	"client_secret": "zc53fwe80980293klaj9823"  // NOTE: Only required for web apps
}
```

| 매개 변수 | | 설명 |
| ----------------------- | ------------------------------- | -------- |
| client\_id | 필수 | 등록 포털([apps.dev.microsoft.com](https://apps.dev.microsoft.com))에서 앱에 할당한 응용 프로그램 ID입니다. |
| grant\_type | 필수 | 이 인증 코드 흐름 레그에 대한 `refresh_token`이어야 합니다. |
| scope | 필수 | 공백으로 구분된 범위 목록입니다. 이 레그에서 요청된 범위가 원래 authorization\_code 요청 레그에서 요청된 범위와 동일하거나 하위 집합이어야 합니다. 이 요청에 지정된 범위가 여러 리소스 서버에 걸쳐 있는 경우 v2.0 끝점은 첫 번째 범위에 지정된 리소스에 대한 토큰을 반환합니다. 범위에 대한 자세한 설명은 [사용 권한, 동의 및 범위](active-directory-v2-scopes.md)를 참조하세요. |
| refresh\_token | 필수 | 흐름의 두 번째 레그에서 얻은 refresh\_token입니다. |
| client\_secret | 웹앱에 필요 | 앱에 대한 앱 등록 포털에서 만든 응용 프로그램 암호입니다. 장치에 client\_secret을 안정적으로 저장할 수 없으므로 네이티브 앱에서는 사용하면 안 됩니다. 서버 쪽에서 client\_secret을 안전하게 저장할 수 있는 웹앱과 Web API에 필요합니다. |

성공적인 토큰 응답은 다음과 같습니다.

```
{
	"access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...",
	"token_type": "Bearer",
	"expires_in": "3600",
	"expires_on": "1388444763",
	"scope": "https://graph.windows.net/directory.read https://graph.windows.net/directory.write",
	"refresh_token": "AwABAAAAvPM1KaPlrEqdFSBzjqfTGAMxZGUTdM0t4B4...",
	"id_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJub25lIn0.eyJhdWQiOiIyZDRkMTFhMi1mODE0LTQ2YTctOD..."
}
```
| 매개 변수 | 설명 |
| ----------------------- | ------------------------------- |
| access\_token | 요청된 액세스 토큰입니다. 앱은 이 토큰을 사용하여 Web API와 같은 보안 리소스에 인증할 수 있습니다. |
| token\_type | 토큰 유형 값을 나타냅니다. Azure AD는 전달자 유형만 지원합니다. |
| expires\_in | 액세스 토큰이 유효한 기간(초)입니다. |
| expires\_on | 액세스 토큰이 만료되는 시간입니다. 날짜는 epoch 시간에서의 초 수로 표시됩니다. |
| scope | access\_token이 유효한 범위입니다. |
| refresh\_token | 새 OAuth 2.0 새로 고침 토큰입니다. 이전 새로 고침 토큰을 새로 얻은 새로 고침 토큰으로 대체하여 새로 고침 토큰을 최대한 오랫동안 유효한 상태로 유지해야 합니다. |
| id\_token | 서명되지 않은 JWT(JSON 웹 토큰)입니다. 앱은 이 토큰의 세그먼트를 base64Url로 디코드하여 로그인한 사용자에 대한 정보를 요청할 수 있습니다. 앱은 값을 캐시하고 표시할 수 있지만 권한 부여 또는 보안 경계에 대해 의존해서는 안 됩니다. id\_token에 대한 자세한 내용은 [v2.0 앱 모델 토큰 참조](active-directory-v2-tokens.md)를 참조하세요. |

오류 응답은 다음과 같습니다.

```
{
	"error": "access_denied",
	"error_description": "The user revoked access to the app.",
}
```

| 매개 변수 | 설명 |
| ----------------------- | ------------------------------- |
| error | 발생하는 오류 유형을 분류하는 데 사용할 수 있고 오류에 대응하는 데 사용할 수 있는 오류 코드 문자열입니다. |
| error\_description | 개발자가 인증 오류의 근본 원인을 식별하도록 도울 수 있는 특정 오류 메시지입니다. |






## OpenID Connect 로그인 흐름
[OpenID Connect](http://openid.net/specs/openid-connect-core-1_0.html)는 *인증* 프로토콜로 사용하기 위해 OAuth 2.0 *권한 부여* 프로토콜을 확장하여 OAuth를 통해 Single Sign-On을 수행할 수 있게 합니다. 클라이언트가 사용자 ID를 확인하고 사용자에 대한 기본 프로필 정보를 얻을 수 있게 하는 보안 토큰인 `id_token`의 개념을 소개합니다.

v2.0 앱 모델용 OpenID Connect는 [웹앱](active-directory-v2-flows.md#web-apps)에 대한 로그인을 구현하는 권장 방법입니다. 가장 기본적인 로그인 흐름에는 다음 단계가 포함됩니다.

![OpenId Connect 스윔 레인](../media/active-directory-v2-flows/convergence_scenarios_webapp.png)

#### 로그인 요청 보내기
웹앱이 사용자를 인증해야 하는 경우 사용자를 `/authorize` 끝점으로 보낼 수 있습니다. 이 요청은 다음과 같은 몇 가지 중요한 차이점을 제외하고 [OAuth 2.0 인증 코드 흐름](#oauth2-authorization-code-flow)의 첫 번째 레그와 유사합니다. - 요청의 `scope` 매개 변수에 `openid` 범위가 포함되어야 합니다. - `response_type` 매개 변수에 `id_token`이 포함되어야 합니다. - 요청에 `nonce` 매개 변수가 포함되어야 합니다.

```
GET https://login.microsoftonline.com/common/oauth2/v2.0/authorize?
client_id=2d4d11a2-f814-46a7-890a-274a72a7309e		// Your registered Application Id
&response_type=id_token
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F 	  // Your registered Redirect Uri, url encoded
&response_mode=query							      // 'query', 'form_post', or 'fragment'
&scope=openid										 // Translates to the 'Read your profile' permission
&state=12345						 				 // Any value, provided by your app
&nonce=678910										 // Any value, provided by your app
```

| 매개 변수 | | 설명 |
| ----------------------- | ------------------------------- | --------------- |
| client\_id | 필수 | 등록 포털([apps.dev.microsoft.com](https://apps.dev.microsoft.com))에서 앱에 할당한 응용 프로그램 ID입니다. |
| response\_type | 필수 | OpenID Connect 로그인을 위한 `id_token`이 포함되어야 합니다. [OAuth를 사용한 OpenID Connect 코드 흐름](#OpenID-Connect-with-OAuth-Code-Flow) 섹션에 설명된 대로 다른 response\_type을 포함할 수도 있습니다. |
| redirect\_uri | 필수 | 앱이 인증 응답을 보내고 받을 수 있는 앱의 redirect\_uri입니다. URL로 인코드되어야 한다는 점을 제외하고 포털에서 등록한 redirect\_uri 중 하나와 정확히 일치해야 합니다. |
| scope | 필수 | 공백으로 구분된 범위 목록입니다. OpenID Connect의 경우 동의 UI에서 "로그인 및 프로필 읽기" 권한으로 해석되는 `openid` 범위가 포함되어야 합니다. [OAuth를 사용한 OpenID Connect 코드 흐름](#OpenID-Connect-with-OAuth-Code-Flow) 섹션에 설명된 대로 동의를 요청하기 위해 이 요청에 다른 범위를 포함할 수도 있습니다. |
| nonce | 필수 | 결과 id\_token에 클레임으로 포함되는, 앱에서 생성한 요청에 포함되는 값입니다. 그러면 앱이 이 값을 확인하여 토큰 재생 공격을 완화시킬 수 있습니다. 값은 일반적으로 요청의 출처를 식별하는 데 사용할 수 있는 임의의 고유 문자열입니다. |
| response\_mode | 권장 | 결과 authorization\_code를 앱에 다시 보내는 데 사용해야 하는 방법을 지정합니다. 'query', 'form\_post' 또는 'fragment' 중 하나일 수 있습니다.  
| state | 권장 | 토큰 응답에도 반환되는 요청에 포함된 값입니다. 원하는 모든 콘텐츠의 문자열일 수 있습니다. 일반적으로 교차 사이트 요청 위조 공격을 방지하기 위해 임의로 생성된 고유 값이 사용됩니다. 상태는 인증 요청이 발생하기 전 앱의 사용자 상태에 대한 정보(예: 사용한 페이지 또는 보기)를 인코드하는 데에도 사용됩니다. |
| prompt | 선택 사항 | 필요한 사용자 상호 작용 유형을 나타냅니다. 현재 유효한 값은 'login'뿐이며 강제로 사용자가 해당 요청에 자격 증명을 입력하도록 합니다. Single Sign-On은 적용되지 않습니다. |

이 시점에서 사용자에게 자격 증명을 입력하고 인증을 완료하라는 메시지가 표시됩니다. v2.0 끝점은 사용자가 `scope` 쿼리 매개 변수에 표시된 사용 권한에 동의했는지도 확인합니다. 사용자가 이러한 사용 권한 중 하나에 동의하지 않은 경우 필요한 사용 권한에 동의하라는 메시지가 표시됩니다. [사용 권한, 동의 및 다중 테넌트 앱의 세부 정보는 여기에 제공되어 있습니다](active-directory-v2-scopes.md).

사용자가 인증하고 동의하면 v2.0 끝점이 `response_mode` 매개 변수에 지정된 방법을 사용하여 표시된 `redirect_uri`에서 해당 앱에 응답을 반환합니다.

`response_mode=query`를 사용한 성공적인 응답은 다음과 같습니다.

```
GET https://localhost/myapp/?
id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q... 	// the id_token, truncated
&session_state=7B29111D-C220-4263-99AB-6F6E135D75EF			   // a value generated by the v2.0 endpoint
&state=12345												  	// the value provided in the request

```

| 매개 변수 | 설명 |
| ----------------------- | ------------------------------- |
| id\_token | 앱이 요청한 id\_token입니다. id\_token을 사용하여 사용자 ID를 확인하고 사용자와 세션을 시작할 수 있습니다. Id\_token 및 해당 콘텐츠에 대한 자세한 내용은 [v2.0 끝점 토큰 참조](active-directory-v2-tokens.md)에 포함되어 있습니다. |
| session\_state | 현재 사용자 세션을 식별하는 고유 값입니다. 이 값은 GUID이지만 검사 없이 전달되는 불투명 값으로 처리되어야 합니다. |
| state | 요청에 state 매개 변수가 포함되어 있으면 동일한 값이 응답에도 나타나야 합니다. 앱은 요청 및 응답의 상태 값이 동일한지 확인해야 합니다. |

앱이 적절하게 처리할 수 있도록 `redirect_uri`에 오류 응답을 보낼 수도 있습니다.

```
GET https://localhost/myapp/?
error=access_denied
&error_description=the+user+canceled+the+authentication
```

| 매개 변수 | 설명 |
| ----------------------- | ------------------------------- |
| error | 발생하는 오류 유형을 분류하는 데 사용할 수 있고 오류에 대응하는 데 사용할 수 있는 오류 코드 문자열입니다. |
| error\_description | 개발자가 인증 오류의 근본 원인을 식별하도록 도울 수 있는 특정 오류 메시지입니다. |

#### id\_token 유효성 검사
id\_token을 받는 것만으로는 사용자를 인증하는 데 충분하지 않습니다. id\_token의 서명 유효성을 검사하고 앱의 요구 사항에 따라 토큰의 클레임을 확인해야 합니다. v2.0 끝점은 [JWT(JSON 웹 토큰)](http://self-issued.info/docs/draft-ietf-oauth-json-web-token.html) 및 공개 키 암호화를 사용하여 토큰에 서명하고 토큰이 유효한지 확인합니다.

v2.0 앱 모델에는 앱이 런타임에 v2.0 앱 모델에 대한 정보를 가져올 수 있게 해주는 OpenID Connect 메타데이터 끝점이 있습니다. 이 정보에는 끝점, 토큰 콘텐츠 및 토큰 서명 키가 포함됩니다. 메타데이터 끝점은 다음 위치에 있는 JSON 문서를 포함합니다.

`https://login.microsoftonline.com/common/v2.0/.well-known/configuration`

이 구성 문서의 속성 중 하나는 `jwks_uri`이며, v2.0 앱 모델에 대한 해당 값은 다음과 같습니다.

`https://login.microsoftonline.com/common/discovery/v2.0/keys`.

이 끝점에 있는 RSA256 공개 키를 사용하여 id\_token의 서명 유효성을 검사할 수 있습니다. 이 끝점에는 항상 여러 키가 나열되어 있으며, 각각 `kid`로 식별됩니다. id\_token의 헤더에는 id\_token 서명에 사용된 키를 나타내는 `kid` 클레임도 포함됩니다.

[토큰 유효성 검사](active-directory-v2-tokens.md#validating-tokens) 및 [서명 키 롤오버에 대한 중요한 정보](active-directory-v2-tokens.md#validating-tokens)를 포함하여 자세한 내용은 [v2.0 앱 모델 토큰 참조](active-directory-v2-tokens.md)를 참조하세요. <!--TODO: Improve the information on this-->

id\_token의 서명 유효성을 검사한 후 확인해야 하는 몇 개의 클레임이 있습니다.

- `nonce` 클레임의 유효성을 검사하여 토큰 재생 공격을 방지해야 합니다. 해당 값이 로그인 요청에 지정된 값이어야 합니다.
- `aud` 클레임의 유효성을 검사하여 id\_token이 앱에 대해 발급된 것인지 확인해야 합니다. 해당 값이 앱의 `client_id`여야 합니다.
- `iat` 및 `exp` 클레임의 유효성을 검사하여 id\_token이 만료되지 않았는지 확인해야 합니다.

시나리오에 따라 추가 클레임의 유효성을 검사할 수도 있습니다. 몇 가지 일반적인 유효성 검사는 다음과 같습니다.

- 사용자/조직이 앱에 등록했는지 확인
- 사용자에게 적절한 권한 부여/권한이 있는지 확인
- 다단계 인증과 같은 특정 강도의 인증이 발생했는지 확인

id\_token의 클레임에 대한 자세한 내용은 [v2.0 앱 모델 토큰 참조](active-directory-v2-tokens.md)를 참조하세요.

id\_token의 유효성을 완전히 검사한 후 사용자와 세션을 시작하고 id\_token의 클레임을 사용하여 앱에서 사용자 정보를 가져올 수 있습니다. 이 정보는 표시, 레코드, 권한 부여 등에 사용될 수 있습니다.

#### 로그아웃 요청 보내기

OpenIdConnect `end_session_endpoint`는 현재 v2.0 앱 모델 미리 보기에서 지원되지 않습니다. 즉, 앱이 v2.0 끝점에 요청을 보내 사용자 세션을 종료하고 v2.0 끝점에서 설정한 쿠키를 지울 수 없습니다. 사용자를 로그아웃하기 위해 앱은 단순히 사용자와의 해당 세션을 종료하고 v2.0 끝점과의 사용자 세션을 그대로 둡니다. 다음에 사용자가 로그인하려고 하면 자주 로그인한 계정이 나열된 "계정 선택" 페이지가 표시됩니다. 해당 페이지에서 사용자는 계정을 로그아웃하고 v2.0 끝점과의 세션을 종료할 수 있습니다.

<!--

When you wish to sign the user out of the  app, it is not sufficient to clear your app's cookies or otherwise end the session with the user.  You must also redirect the user to the v2.0 endpoint for sign out.  If you fail to do so, the user will be able to re-authenticate to your app without entering their credentials again, because they will have a valid single sign-on session with the v2.0 endpoint.

You can simply redirect the user to the `end_session_endpoint` listed in the OpenID Connect metadata document:

```
GET https://login.microsoftonline.com/common/oauth2/v2.0/logout?
post_logout_redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
```

| Parameter | | Description |
| ----------------------- | ------------------------------- | ------------ |
| post_logout_redirect_uri | recommended | The URL which the user should be redirected to after successful logout.  If not included, the user will be shown a generic message by the v2.0 endpoint.  |

-->

## OAuth를 사용한 OpenID Connect 코드 흐름
대부분의 웹앱은 사용자를 로그인한 다음 OAuth를 사용하여 해당 사용자 대신 웹 서비스에 액세스해야 합니다. 이 시나리오에서는 위의 두 섹션을 결합합니다. OAuth 인증 코드 흐름을 사용하여 access\_token을 가져오는 데 사용할 수 있는 authorization\_code를 획득하는 동시에 사용자 인증에 OpenID Connect를 사용합니다.

![OpenId Connect 스윔 레인](../media/active-directory-v2-flows/convergence_scenarios_webapp_webapi.png)

이 흐름은 로그인 요청을 보내는 방법이 위의 섹션과 약간 다릅니다.

#### 로그인 요청 보내기
웹앱이 사용자를 인증하고 리소스 액세스에 필요한 사용 권한을 가져와야 하는 경우 사용자를 `/authorize` 끝점으로 보낼 수 있습니다. 이 경우 앱이 응답에 `id_token` 및 `code`를 둘 다 요청해야 합니다.

```
GET https://login.microsoftonline.com/common/oauth2/v2.0/authorize?
client_id=2d4d11a2-f814-46a7-890a-274a72a7309e		// Your registered Application Id
&response_type=id_token+code
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F 	  // Your registered Redirect Uri, url encoded
&response_mode=query							      // 'query', 'form_post', or 'fragment'
&scope=openid%20										 // Include both 'openid' and scopes your app needs  
https%3A%2F%2Fgraph.windows.net%2Fdirectory.read%20
https%3A%2F%2Fgraph.windows.net%2Fdirectory.write
&state=12345						 				 // Any value, provided by your app
&nonce=678910										 // Any value, provided by your app
```

| 매개 변수 | | 설명 |
| ----------------------- | ------------------------------- | ----------------- |
| client\_id | 필수 | 등록 포털([apps.dev.microsoft.com](https://apps.dev.microsoft.com))에서 앱에 할당한 응용 프로그램 ID입니다. |
| response\_type | 필수 | 이 시나리오에 대한 `id_token` 및 `code`를 포함해야 합니다. |
| redirect\_uri | 필수 | 앱이 인증 응답을 보내고 받을 수 있는 앱의 redirect\_uri입니다. URL로 인코드되어야 한다는 점을 제외하고 포털에서 등록한 redirect\_uri 중 하나와 정확히 일치해야 합니다. |
| scope | 필수 | 공백으로 구분된 범위 목록입니다. OpenID Connect의 경우 동의 UI에서 "로그인 및 프로필 읽기" 권한으로 해석되는 `openid` 범위가 포함되어야 합니다. 또한 앱이 액세스해야 하는 리소스에 대한 범위를 포함해야 합니다. 범위에 대한 자세한 설명은 [사용 권한, 동의 및 범위](active-directory-v2-scopes.md)를 참조하세요. |
| nonce | 필수 | 결과 id\_token에 클레임으로 포함되는, 앱에서 생성한 요청에 포함되는 값입니다. 그러면 앱이 이 값을 확인하여 토큰 재생 공격을 완화시킬 수 있습니다. 값은 일반적으로 요청의 출처를 식별하는 데 사용할 수 있는 임의의 고유 문자열입니다. |
| response\_mode | 권장 | 결과 authorization\_code를 앱에 다시 보내는 데 사용해야 하는 방법을 지정합니다. 'query', 'form\_post' 또는 'fragment' 중 하나일 수 있습니다.  
| state | 권장 | 토큰 응답에도 반환되는 요청에 포함된 값입니다. 원하는 모든 콘텐츠의 문자열일 수 있습니다. 일반적으로 교차 사이트 요청 위조 공격을 방지하기 위해 임의로 생성된 고유 값이 사용됩니다. 상태는 인증 요청이 발생하기 전 앱의 사용자 상태에 대한 정보(예: 사용한 페이지 또는 보기)를 인코드하는 데에도 사용됩니다. |
| prompt | 선택 사항 | 필요한 사용자 상호 작용 유형을 나타냅니다. 현재 유효한 값은 'login'뿐이며 강제로 사용자가 해당 요청에 자격 증명을 입력하도록 합니다. Single Sign-On은 적용되지 않습니다. |

이 시점에서 사용자에게 자격 증명을 입력하고 인증을 완료하라는 메시지가 표시됩니다. v2.0 끝점은 사용자가 `scope` 쿼리 매개 변수에 표시된 사용 권한에 동의했는지도 확인합니다. 사용자가 이러한 사용 권한 중 하나에 동의하지 않은 경우 필요한 사용 권한에 동의하라는 메시지가 표시됩니다. [사용 권한, 동의 및 다중 테넌트 앱의 세부 정보는 여기에 제공되어 있습니다](active-directory-v2-scopes.md).

사용자가 인증하고 동의하면 v2.0 끝점이 `response_mode` 매개 변수에 지정된 방법을 사용하여 표시된 `redirect_uri`에서 해당 앱에 응답을 반환합니다.

`response_mode=query`를 사용한 성공적인 응답은 다음과 같습니다.

```
GET https://localhost/myapp/?
id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q... 	// the id_token, truncated
&code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq... 	// the authorization_code, truncated
&session_state=7B29111D-C220-4263-99AB-6F6E135D75EF			   // a value generated by the v2.0 endpoint
&state=12345												  	// the value provided in the request

```

| 매개 변수 | 설명 |
| ----------------------- | ------------------------------- |
| id\_token | 앱이 요청한 id\_token입니다. id\_token을 사용하여 사용자 ID를 확인하고 사용자와 세션을 시작할 수 있습니다. Id\_token 및 해당 콘텐츠에 대한 자세한 내용은 [v2.0 앱 모델 토큰 참조](active-directory-v2-tokens.md)에 포함되어 있습니다. |
| 코드 | 앱이 요청한 authorization\_code입니다. 앱은 인증 코드를 사용하여 대상 리소스에 대한 액세스 토큰을 요청할 수 있습니다. authorization\_code는 수명이 매우 짧으며, 일반적으로 약 10분 후에 만료됩니다. |
| session\_state | 현재 사용자 세션을 식별하는 고유 값입니다. 이 값은 GUID이지만 검사 없이 전달되는 불투명 값으로 처리되어야 합니다. |
| state | 요청에 state 매개 변수가 포함되어 있으면 동일한 값이 응답에도 나타나야 합니다. 앱은 요청 및 응답의 상태 값이 동일한지 확인해야 합니다. |

앱이 적절하게 처리할 수 있도록 `redirect_uri`에 오류 응답을 보낼 수도 있습니다.

```
GET https://localhost/myapp/?
error=access_denied
&error_description=the+user+canceled+the+authentication
```

| 매개 변수 | 설명 |
| ----------------------- | ------------------------------- |
| error | 발생하는 오류 유형을 분류하는 데 사용할 수 있고 오류에 대응하는 데 사용할 수 있는 오류 코드 문자열입니다. |
| error\_description | 개발자가 인증 오류의 근본 원인을 식별하도록 도울 수 있는 특정 오류 메시지입니다. |

#### id\_token 유효성 검사
이 프로세스는 위의 [OpenID Connect 로그인 흐름](#OpenID-Connect-Sign-In-Flow)에서 설명한 것과 같습니다.

#### 로그아웃 요청 보내기
이 프로세스는 위의 [OpenID Connect 로그인 흐름](#OpenID-Connect-Sign-In-Flow)에서 설명한 것과 같습니다.

#### 액세스 토큰 요청
이 프로세스는 위의 [OAuth 2.0 인증 코드 흐름](#oauth2-authorization-code-flow)에서 설명한 것과 같습니다.

#### 액세스 토큰 사용
이 프로세스는 위의 [OAuth 2.0 인증 코드 흐름](#oauth2-authorization-code-flow)에서 설명한 것과 같습니다.

#### 액세스 토큰 새로 고침
이 프로세스는 위의 [OAuth 2.0 인증 코드 흐름](#oauth2-authorization-code-flow)에서 설명한 것과 같습니다.





## OAuth2 클라이언트 자격 증명 부여 흐름
OAuth 2.0 클라이언트 자격 증명 부여는 [OAuth 2.0 사양](http://tools.ietf.org/html/rfc6749#section-4.4)에서 설명합니다. 앱이 사용자의 위임된 자체 "응용 프로그램 ID"를 사용하여 리소스에 인증할 수 있는 장기 실행 프로세스와 다른 서버 간 시나리오에 유용합니다.

이 흐름은 현재 v2.0 앱 모델 미리 보기에서 지원되지 않습니다. 일반 공급 Azure AD 서비스에서 이 흐름이 작동하는 방식을 확인하려면 [이 Azure AD 코드 샘플](https://github/com/AzureADSamples/Daemon-DotNet)을 참조하세요.

## OAuth2 암시적 흐름
OAuth 2.0 암시적 흐름은 [OAuth 2.0 사양](http://tools.ietf.org/html/rfc6749#section-4.2)에서 설명합니다. 브라우저에서 실행되는 단일 페이지/Javascript 앱에 자주 사용됩니다.

이 흐름은 현재 v2.0 앱 모델 미리 보기에서 지원되지 않습니다. 일반 공급 Azure AD 서비스에서 이 흐름이 작동하는 방식을 확인하려면 [이 Azure AD 코드 샘플](active-directory-devquickstarts-angular.md)을 참조하세요.

## OAuth2 리소스 소유자 암호 자격 증명 부여
OAuth 2.0 리소스 소유자 암호 자격 증명 부여는 [OAuth 2.0 사양](http://tools.ietf.org/html/rfc6749#section-4.3)에서 설명합니다. 토큰 요청에 사용자의 사용자 이름 및 암호를 제공하여 앱이 access\_token을 획득할 수 있게 합니다.

이 흐름은 현재 v2.0 앱 모델 미리 보기에서 지원되지 않습니다. 일반 공급 Azure AD 서비스에서 이 흐름이 작동하는 방식을 확인하려면 [이 Azure AD 코드 샘플](https://github.com/AzureADSamples/NativeClient-Headless-DotNet)을 참조하세요.

## OAuth2 On Behalf Of 흐름
On Behalf Of 흐름 또는 JWT 전달자 자격 증명 부여는 [OAuth 2.0 확장 부여 초안](https://tools.ietf.org/html/draft-ietf-oauth-jwt-bearer-12)에서 설명합니다. access\_token을 받는 Web API가 다른 리소스에 대한 access\_token을 획득하기 위한 자격 증명으로 해당 access\_token을 사용할 수 있게 합니다. 이렇게 하면 Web API가 다른 다운스트림 Web API를 안전하게 호출할 수 있습니다.

이 흐름은 현재 v2.0 앱 모델 미리 보기에서 지원되지 않습니다. 일반 공급 Azure AD 서비스에서 이 흐름이 작동하는 방식을 확인하려면 [이 Azure AD 코드 샘플](https://github.com/AzureADSamples/WebAPI-OnBehalfOf-DotNet)을 참조하세요.

<!---HONumber=Sept15_HO3-->