
<properties
	pageTitle="Azure AD v2.0 OpenID Connect 프로토콜 | Microsoft Azure"
	description="OpenID Connect 인증 프로토콜의 Azure AD의 v2.0 구현을 사용하여 웹 응용 프로그램을 빌드합니다."
	services="active-directory"
	documentationCenter=""
	authors="dstrockis"
	manager="msmbaldwin"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="06/23/2016"
	ms.author="dastrock"/>

# v2.0 프로토콜 - OpenID Connect
OpenID Connect는 웹 응용 프로그램에 사용자를 안전하게 로그인하는 데 사용할 수 있는 OAuth 2.0을 기반으로 빌드된 인증 프로토콜입니다. v2.0 끝점의 OpenID Connect 구현을 사용하여 로그인 및 API 액세스를 웹 기반 응용 프로그램에 추가할 수 있습니다. 이 가이드는 언어 독립적인 방식으로 수행하는 방식을 보여주며 공개 소스 라이브러리 중 하나를 사용하지 않고 HTTP 메시지를 수신하는 방법을 설명합니다.

> [AZURE.NOTE]
	일부 Azure Active Directory 시나리오 및 기능만 v2.0 끝점에서 지원합니다. v2.0 끝점을 사용해야 하는지 확인하려면 [v2.0 제한 사항](active-directory-v2-limitations.md)을 참조하세요.

[OpenID Connect](http://openid.net/specs/openid-connect-core-1_0.html)는 *인증* 프로토콜로 사용하기 위해 OAuth 2.0 *권한 부여* 프로토콜을 확장하여 OAuth를 통해 Single Sign-On을 수행할 수 있게 합니다. 클라이언트가 사용자 ID를 확인하고 사용자에 대한 기본 프로필 정보를 얻을 수 있게 하는 보안 토큰인 `id_token`의 개념을 소개합니다. OAuth 2.0를 확장하기 때문에 또한 [인증 서버](active-directory-v2-protocols.md#the-basics)를 사용하여 보안된 리소스에 액세스하는 데 사용할 수 있는 **access\_tokens**을 앱이 안전하게 획득할 수 있도록 합니다. OpenID Connect는 서버에서 호스트되고 브라우저를 통해 액세스되는 [웹 응용 프로그램](active-directory-v2-flows.md#web-apps)을 빌드하는 경우 권장 사항입니다.

## 프로토콜 다이어그램 - 로그인
대부분의 기본 로그인 흐름은 다음 단계를 포함합니다. - 각 단계를 아래에서 자세히 설명합니다.

![OpenId Connect 스윔 레인](../media/active-directory-v2-flows/convergence_scenarios_webapp.png)

## 로그인 요청 보내기
웹앱이 사용자를 인증해야 하는 경우 사용자를 `/authorize` 끝점으로 보낼 수 있습니다. 이 요청은 몇 가지 중요한 차이점이 있지만 [OAuth 2.0 인증 코드 흐름](active-directory-v2-protocols-oauth-code.md)의 첫 번째 레그와 유사합니다.

- 요청이 `openid` 범위를 `scope` 매개 변수에 포함해야 합니다.
- `response_type` 매개 변수는 `id_token`을 포함해야 합니다.
- 요청은 `nonce` 매개 변수를 포함해야 합니다.

```
// Line breaks for legibility only

GET https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&response_type=id_token
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&response_mode=form_post
&scope=openid
&state=12345
&nonce=678910
```

> [AZURE.TIP] 이 요청을 실행하려면 아래 링크를 클릭하세요. 로그인하면 브라우저가 주소 표시줄에서 `id_token`과 함께 `https://localhost/myapp/`으로 리디렉션됩니다. 이 요청은 `response_mode=query`를 사용합니다(학습 용도로만). `response_mode=form_post`를 사용하는 것이 좋습니다. <a href="https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&response_type=id_token&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F&scope=openid&response_mode=query&state=12345&nonce=678910" target="_blank">https://login.microsoftonline.com/common/oauth2/v2.0/authorize...</a>

| 매개 변수 | | 설명 |
| ----------------------- | ------------------------------- | --------------- |
| tenant | 필수 | 요청의 경로에 있는 `{tenant}` 값을 사용하여 응용 프로그램에 로그인할 수 있는 사용자를 제어할 수 있습니다. 허용되는 값은 `common`, `organizations`, `consumers` 및 테넌트 ID입니다. 자세한 내용은 [프로토콜 기본](active-directory-v2-protocols.md#endpoints)을 참조하세요. |
| client\_id | 필수 | 등록 포털([apps.dev.microsoft.com](https://apps.dev.microsoft.com))에서 앱에 할당한 응용 프로그램 ID입니다. |
| response\_type | 필수 | OpenID Connect 로그인을 위한 `id_token`이 포함되어야 합니다. `code`와 같은 다른 response\_types을 포함할 수도 있습니다. |
| redirect\_uri | 권장 | 앱이 인증 응답을 보내고 받을 수 있는 앱의 redirect\_uri입니다. URL로 인코드되어야 한다는 점을 제외하고 포털에서 등록한 redirect\_uri 중 하나와 정확히 일치해야 합니다. |
| scope | 필수 | 공백으로 구분된 범위 목록입니다. OpenID Connect의 경우 동의 UI에서 "로그인" 권한으로 해석되는 `openid` 범위가 포함되어야 합니다. 동의를 요청하기 위해 이 요청에 다른 범위를 포함할 수도 있습니다. |
| nonce | 필수 | 결과 id\_token에 클레임으로 포함되는, 앱에서 생성한 요청에 포함되는 값입니다. 그러면 앱이 이 값을 확인하여 토큰 재생 공격을 완화시킬 수 있습니다. 값은 일반적으로 요청의 출처를 식별하는 데 사용할 수 있는 임의의 고유 문자열입니다. |
| response\_mode | 권장 | 결과 authorization\_code를 앱에 다시 보내는 데 사용해야 하는 방법을 지정합니다. 'query', 'form\_post' 또는 'fragment' 중 하나일 수 있습니다. 웹 응용 프로그램의 경우 응용 프로그램에 대한 가장 안전한 토큰 전송을 보장하기 위해 `response_mode=form_post`를 사용하는 것이 좋습니다.  
| state | 권장 | 토큰 응답에도 반환되는 요청에 포함된 값입니다. 원하는 모든 콘텐츠의 문자열일 수 있습니다. 일반적으로 [교차 사이트 요청 위조 공격을 방지](http://tools.ietf.org/html/rfc6749#section-10.12)하기 위해 임의로 생성된 고유 값이 사용됩니다. 상태는 인증 요청이 발생하기 전 앱의 사용자 상태에 대한 정보(예: 사용한 페이지 또는 보기)를 인코드하는 데에도 사용됩니다. |
| prompt | 선택 사항 | 필요한 사용자 상호 작용 유형을 나타냅니다. 이 경우 유효한 값은 'login', 'none', 'consent'뿐입니다. `prompt=login`은 Single-Sign On을 무효화면서, 사용자가 요청에 자신의 자격 증명을 입력하도록 합니다. `prompt=none`은 그 반대로 사용자에게 어떠한 대화형 프롬프트도 표시되지 않도록 합니다. Single-Sign On을 통해 요청이 자동으로 완료될 수 없는 경우에 v2.0 끝점은 오류를 반환합니다. `prompt=consent`는 사용자가 로그인한 후에 OAuth 동의 대화 상자를 트리거하여 앱에 권한을 부여할 것을 사용자에게 요청합니다. |
| login\_hint | 선택 사항 | 사용자 이름을 미리 알고 있는 경우 사용자를 위해 로그인 페이지의 사용자 이름/이메일 주소 필드를 미리 채우는 데 사용될 수 있습니다. `preferred_username` 클레임을 사용하여 이전 로그인 작업에서 사용자 이름이 이미 추출된 경우 앱이 재인증 과정에서 이 매개 변수를 종종 사용합니다. |
| domain\_hint | 선택 사항 | `consumers` 또는 `organizations` 중 하나일 수 있습니다. 포함된 경우, v2.0 로그인 페이지를 거친 사용자가 좀 더 효율적인 사용자 경험을 가질 수 있도록 전자 메일 기반 검색 프로세스를 건너뜁니다. 앱이 id\_token에서 `tid` 클레임을 추출하여 재인증 과정에서 이 매개 변수를 종종 사용합니다. `tid` 클레임 값이 `9188040d-6c67-4c5b-b112-36a304b66dad`인 경우 `domain_hint=consumers`를 사용해야 합니다. 그렇지 않으면 `domain_hint=organizations`를 사용합니다. |
이 시점에서 사용자에게 자격 증명을 입력하고 인증을 완료하라는 메시지가 표시됩니다. v2.0 끝점은 사용자가 `scope` 쿼리 매개 변수에 표시된 사용 권한에 동의했는지도 확인합니다. 사용자가 이러한 사용 권한 중 하나에 동의하지 않은 경우 필요한 사용 권한에 동의하라는 메시지가 표시됩니다. [사용 권한, 동의 및 다중 테넌트 앱의 세부 정보는 여기에 제공되어 있습니다](active-directory-v2-scopes.md).

사용자가 인증하고 동의하면 v2.0 끝점이 `response_mode` 매개 변수에 지정된 방법을 사용하여 표시된 `redirect_uri`에서 해당 앱에 응답을 반환합니다.

#### 성공적인 응답
`response_mode=form_post`를 사용한 성공적인 응답은 다음과 같습니다.

```
POST /myapp/ HTTP/1.1
Host: localhost
Content-Type: application/x-www-form-urlencoded

id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik1uQ19WWmNB...&state=12345
```

| 매개 변수 | 설명 |
| ----------------------- | ------------------------------- |
| id\_token | 앱이 요청한 id\_token입니다. id\_token을 사용하여 사용자 ID를 확인하고 사용자와 세션을 시작할 수 있습니다. Id\_token 및 해당 콘텐츠에 대한 자세한 내용은 [v2.0 끝점 토큰 참조](active-directory-v2-tokens.md)에 포함되어 있습니다. |
| state | 요청에 state 매개 변수가 포함되어 있으면 동일한 값이 응답에도 나타나야 합니다. 앱은 요청 및 응답의 상태 값이 동일한지 확인해야 합니다. |

#### 오류 응답
앱이 적절하게 처리할 수 있도록 `redirect_uri`에 오류 응답을 보낼 수도 있습니다.

```
POST /myapp/ HTTP/1.1
Host: localhost
Content-Type: application/x-www-form-urlencoded

error=access_denied&error_description=the+user+canceled+the+authentication
```

| 매개 변수 | 설명 |
| ----------------------- | ------------------------------- |
| error | 발생하는 오류 유형을 분류하는 데 사용할 수 있고 오류에 대응하는 데 사용할 수 있는 오류 코드 문자열입니다. |
| error\_description | 개발자가 인증 오류의 근본 원인을 식별하도록 도울 수 있는 특정 오류 메시지입니다. |

#### 권한 부여 끝점 오류에 대한 오류 코드

다음 테이블은 오류 응답의 `error` 매개 변수에 반환될 수 있는 여러 오류 코드를 설명합니다.

| 오류 코드 | 설명 | 클라이언트 작업 |
|------------|-------------|---------------|
| invalid\_request | 프로토콜 오류(예: 필수 매개 변수 누락). | 요청을 수정하여 다시 제출하십시오. 일반적으로 초기 설정 중에 발견되는 개발 오류입니다.|
| unauthorized\_client | 클라이언트 응용 프로그램이 인증 코드를 요청할 수 없습니다. | 이 오류는 일반적으로 클라이언트 응용 프로그램이 Azure AD에 등록되지 않았거나 사용자의 Azure AD 테넌트에 추가되지 않은 경우 발생합니다. 응용 프로그램이 사용자에게 응용 프로그램을 설치하고 Azure AD에 추가하기 위한 지침이 포함된 메시지를 표시할 수 있습니다. |
| access\_denied | 리소스 소유자가 동의 거부 | 클라이언트 응용 프로그램이 사용자의 동의를 받지 않으면 계속 진행할 수 없다고 알릴 수 있습니다. |
| unsupported\_response\_type | 권한 부여 서버가 요청에 해당 응답 형식을 지원하지 않습니다. | 요청을 수정하여 다시 제출하십시오. 일반적으로 초기 설정 중에 발견되는 개발 오류입니다.|
|server\_error | 서버에 예기치 않은 오류가 발생했습니다. | 요청을 다시 시도하십시오. 이러한 오류는 일시적인 상태 때문에 발생할 수 있습니다. 클라이언트 응용 프로그램이 일시적 오류 때문에 응답이 지연되었음을 사용자에게 설명할 수 있습니다. |
| temporarily\_unavailable | 서버가 일시적으로 사용량이 많아 요청을 처리할 수 없습니다. | 요청을 다시 시도하십시오. 클라이언트 응용 프로그램이 일시적 상태 때문에 응답이 지연되었음을 사용자에게 설명할 수 있습니다. |
| invalid\_resource |대상 리소스가 존재하지 않거나 Azure AD에서 해당 리소스를 찾을 수 없거나 올바르게 구성되지 않았기 때문에 잘못되었습니다.| 리소스가 존재하는 경우 테넌트에 구성되지 않았음을 나타냅니다. 응용 프로그램이 사용자에게 응용 프로그램을 설치하고 Azure AD에 추가하기 위한 지침이 포함된 메시지를 표시할 수 있습니다. |

## id\_token 유효성 검사
id\_token을 받는 것만으로는 사용자를 인증하는 데 충분하지 않습니다. id\_token의 서명 유효성을 검사하고 앱의 요구 사항에 따라 토큰의 클레임을 확인해야 합니다. v2.0 끝점은 [JWT(JSON 웹 토큰)](http://self-issued.info/docs/draft-ietf-oauth-json-web-token.html) 및 공개 키 암호화를 사용하여 토큰에 서명하고 토큰이 유효한지 확인합니다.

클라이언트 코드에서 `id_token`의 유효성을 검사하도록 선택할 수 있지만, 일반적으로 `id_token`을 백 엔드 서버에 보내서 그 곳에서 유효성 검사를 수행합니다. id\_token의 서명 유효성을 검사한 후 확인해야 하는 몇 개의 클레임이 있습니다. [토큰 유효성 검사](active-directory-v2-tokens.md#validating-tokens) 및 [서명 키 롤오버에 대한 중요한 정보](active-directory-v2-tokens.md#validating-tokens)를 포함하여 자세한 내용은 [v2.0 토큰 참조](active-directory-v2-tokens.md)를 참조하세요. 대부분의 언어 및 플랫폼에서 사용할 수 있는 하나 이상의 토큰의 구문 분석 및 유효성 검사에 대한 라이브러리를 사용하는 것이 좋습니다.
<!--TODO: Improve the information on this-->

시나리오에 따라 추가 클레임의 유효성을 검사할 수도 있습니다. 몇 가지 일반적인 유효성 검사는 다음과 같습니다.

- 사용자/조직이 앱에 등록했는지 확인
- 사용자에게 적절한 권한 부여/권한이 있는지 확인
- 다단계 인증과 같은 특정 강도의 인증이 발생했는지 확인

id\_token의 클레임에 대한 자세한 내용은 [v2.0 끝점 토큰 참조](active-directory-v2-tokens.md)를 참조하세요.

id\_token의 유효성을 완전히 검사한 후 사용자와 세션을 시작하고 id\_token의 클레임을 사용하여 앱에서 사용자 정보를 가져올 수 있습니다. 이 정보는 표시, 레코드, 권한 부여 등에 사용될 수 있습니다.

## 로그아웃 요청 보내기

OpenIdConnect `end_session_endpoint`는 현재 v2.0 끝점에서 지원되지 않습니다. 즉, 앱이 v2.0 끝점에 요청을 보내 사용자 세션을 종료하고 v2.0 끝점에서 설정한 쿠키를 지울 수 없습니다. 사용자를 로그아웃하기 위해 앱은 단순히 사용자와의 해당 세션을 종료하고 v2.0 끝점과의 사용자 세션을 그대로 둡니다. 다음에 사용자가 로그인하려고 하면 자주 로그인한 계정이 나열된 "계정 선택" 페이지가 표시됩니다. 해당 페이지에서 사용자는 계정을 로그아웃하고 v2.0 끝점과의 세션을 종료할 수 있습니다.

<!--

When you wish to sign the user out of the app, it is not sufficient to clear your app's cookies or otherwise end the session with the user.  You must also redirect the user to the v2.0 endpoint for sign out.  If you fail to do so, the user will be able to re-authenticate to your app without entering their credentials again, because they will have a valid single sign-on session with the v2.0 endpoint.

You can simply redirect the user to the `end_session_endpoint` listed in the OpenID Connect metadata document:

```
GET https://login.microsoftonline.com/common/oauth2/v2.0/logout?
post_logout_redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
```

| Parameter | | Description |
| ----------------------- | ------------------------------- | ------------ |
| post_logout_redirect_uri | recommended | The URL which the user should be redirected to after successful logout.  If not included, the user will be shown a generic message by the v2.0 endpoint.  |

-->

## 프로토콜 다이어그램 - 토큰 가져오기
대부분의 웹앱은 사용자를 로그인할 뿐만 아니라 OAuth를 사용하여 해당 사용자 대신 웹 서비스에 액세스해야 합니다. 이 시나리오에서는 OAuth 인증 코드 흐름을 사용하여 access\_token을 가져오는 데 사용할 수 있는 authorization\_code를 획득하는 동시에 사용자 인증에 OpenID Connect를 사용합니다.

전체 OpenID Connect 로그인 및 토큰 가져오기 흐름은 다음과 같습니다. - 각 단계를 아래에서 자세히 설명합니다.

![OpenId Connect 스윔 레인](../media/active-directory-v2-flows/convergence_scenarios_webapp_webapi.png)

## 액세스 토큰 가져오기
액세스 토큰을 얻으려면 위에서 로그인 요청을 약간 수정해야 합니다.

```
// Line breaks for legibility only

GET https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e		// Your registered Application Id
&response_type=id_token+code
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F 	  // Your registered Redirect Uri, url encoded
&response_mode=form_post						      // 'query', 'form_post', or 'fragment'
&scope=openid%20                                      // Include both 'openid' and scopes your app needs  
offline_access%20										 
https%3A%2F%2Fgraph.microsoft.com%2Fmail.read
&state=12345						 				 // Any value, provided by your app
&nonce=678910										 // Any value, provided by your app
```

> [AZURE.TIP] 이 요청을 실행하려면 아래 링크를 클릭하세요. 로그인하면 브라우저가 주소 표시줄에서 `id_token` 및 `code`와 함께 `https://localhost/myapp/`으로 리디렉션됩니다. 이 요청은 `response_mode=query`를 사용합니다(학습 용도로만). `response_mode=form_post`를 사용하는 것이 좋습니다. <a href="https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&response_type=id_token+code&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F&response_mode=query&scope=openid%20offline_access%20https%3A%2F%2Fgraph.microsoft.com%2Fmail.read&state=12345&nonce=678910" target="_blank">https://login.microsoftonline.com/common/oauth2/v2.0/authorize...</a>

v2.0 끝점은 요청에 사용 권한 범위를 포함하고 `response_type=code+id_token`을 사용하여 `scope` 쿼리 매개 변수에 표시된 사용 권한에 사용자가 동의했음을 보장하고 액세스 토큰과 교환할 인증 코드를 앱에 반환합니다.

#### 성공적인 응답
`response_mode=form_post`를 사용한 성공적인 응답은 다음과 같습니다.

```
POST /myapp/ HTTP/1.1
Host: localhost
Content-Type: application/x-www-form-urlencoded

id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik1uQ19WWmNB...&code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...&state=12345
```

| 매개 변수 | 설명 |
| ----------------------- | ------------------------------- |
| id\_token | 앱이 요청한 id\_token입니다. id\_token을 사용하여 사용자 ID를 확인하고 사용자와 세션을 시작할 수 있습니다. Id\_token 및 해당 콘텐츠에 대한 자세한 내용은 [v2.0 끝점 토큰 참조](active-directory-v2-tokens.md)에 포함되어 있습니다. |
| 코드 | 앱이 요청한 authorization\_code입니다. 앱은 인증 코드를 사용하여 대상 리소스에 대한 액세스 토큰을 요청할 수 있습니다. authorization\_code는 수명이 매우 짧으며, 일반적으로 약 10분 후에 만료됩니다. |
| state | 요청에 state 매개 변수가 포함되어 있으면 동일한 값이 응답에도 나타나야 합니다. 앱은 요청 및 응답의 상태 값이 동일한지 확인해야 합니다. |

#### 오류 응답
앱이 적절하게 처리할 수 있도록 `redirect_uri`에 오류 응답을 보낼 수도 있습니다.

```
POST /myapp/ HTTP/1.1
Host: localhost
Content-Type: application/x-www-form-urlencoded

error=access_denied&error_description=the+user+canceled+the+authentication
```

| 매개 변수 | 설명 |
| ----------------------- | ------------------------------- |
| error | 발생하는 오류 유형을 분류하는 데 사용할 수 있고 오류에 대응하는 데 사용할 수 있는 오류 코드 문자열입니다. |
| error\_description | 개발자가 인증 오류의 근본 원인을 식별하도록 도울 수 있는 특정 오류 메시지입니다. |

가능한 오류 코드 및 권장되는 클라이언트 작업에 대한 설명은 [권한 부여 끝점 오류에 대한 오류 코드](#error-codes-for-authorization-endpoint-errors)를 참조합니다.

인증 `code` 및 `id_token`을 받은 후 사용자를 로그인하고 대신 액세스 토큰을 얻을 수 있습니다. 사용자를 로그인하려면 [위에](#validating-the-id-token) 설명된 대로 정확하게 `id_token`의 유효성을 검사해야 합니다. 액세스 토큰을 얻으려면 [OAuth 프로토콜 설명서](active-directory-v2-protocols-oauth-code.md#request-an-access-token)에 설명된 단계를 따르세요.

<!---HONumber=AcomDC_0629_2016-->