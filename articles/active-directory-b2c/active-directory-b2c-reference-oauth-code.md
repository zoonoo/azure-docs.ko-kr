<properties
	pageTitle="Azure Active Directory B2C 미리 보기 | Microsoft Azure"
	description="OpenID Connect 인증 프로토콜의 Azure Active Directory 구현을 사용하여 웹 응용 프로그램을 빌드합니다."
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
	ms.date="05/16/2016"
	ms.author="dastrock"/>

# Azure Active Directory B2C 미리 보기: OAuth 2.0 인증 코드 흐름

OAuth 2.0 인증 코드 권한은 장치에 설치된 앱에서 사용하여 Web API와 같은 보호된 리소스에 대한 액세스 권한을 얻을 수 있습니다. OAuth 2.0의 Azure AD(Azure Active Directory) B2C 구현을 사용하여 모바일 및 데스크톱 앱에 등록, 로그인 및 기타 ID 관리 작업을 추가할 수 있습니다. 이 가이드는 언어에 상관없이 적용됩니다. 오픈 소스 라이브러리를 사용하지 않고 HTTP 메시지를 보내고 받는 방법을 설명합니다.

<!-- TODO: Need link to libraries -->

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]

OAuth 2.0 인증 코드 흐름은 [OAuth 2.0 사양의 섹션 4.1](http://tools.ietf.org/html/rfc6749)에서 설명합니다. [웹앱](active-directory-b2c-apps.md#web-apps) 및 [기본적으로 설치된 앱](active-directory-b2c-apps.md#mobile-and-native-apps)을 포함하여 대부분의 앱 형식에서 인증 및 권한 부여를 수행하는 데 사용할 수 있습니다. [인증 서버](active-directory-b2c-reference-protocols.md#the-basics)를 사용하여 보안된 리소스에 액세스하는 데 사용할 수 있는 **access\_token**을 앱이 안전하게 획득할 수 있도록 합니다.

이 가이드는 OAuth 2.0 인증 코드 흐름의 특정 버전(**공용 클라이언트**)에 중점을 둡니다. 공용 클라이언트는 보안 암호의 무결성을 안전하게 유지하기 위해 신뢰할 수 없는 클라이언트 응용 프로그램입니다. 모바일 앱, 데스크톱 앱 및 장치에서 실행되고 access\_tokens 가져와야 하는 거의 모든 응용 프로그램을 포함합니다. Azure AD B2C를 사용하여 ID 관리를 웹앱에 추가하려는 경우 OAuth 2.0 보다 [OpenID Connect](active-directory-b2c-reference-oidc.md)를 사용해야 합니다.

Azure AD B2C는 단순한 인증 및 권한 부여 보다 더 많은 작업으로 표준 OAuth 2.0의 흐름을 확장합니다. [**정책 매개 변수**](active-directory-b2c-reference-policies.md)를 소개하며 OAuth 2.0을 사용하여 등록, 로그인 및 프로필 관리와 같은 앱에 사용자 환경을 추가할 수 있습니다. 또한 OAuth 2.0 및 정책을 사용하여 네이티브 응용 프로그램에서 각 이러한 환경을 구현하는 방법 및 Web API에 액세스하기 위한 access\_token을 얻는 방법을 보여 줍니다.

아래 예제 HTTP 요청은 샘플 B2C 디렉터리, **fabrikamb2c.onmicrosoft.com** 뿐만 아니라 샘플 응용 프로그램 및 정책을 사용합니다. 이러한 값을 사용하여 직접 요청을 시도하거나 고유의 작업으로 바꿀 수 있습니다. [사용자 고유의 B2C 디렉터리, 응용 프로그램 및 정책을 가져오는](#use-your-own-b2c-directory) 방법을 알아봅니다.

## 1\. 권한 부여 코드 가져오기
인증 코드 흐름은 클라이언트가 사용자를 `/authorize` 끝점으로 보내는 것으로 시작됩니다. 사용자가 실제로 조치를 취하는 흐름의 대화형 부분입니다. 이 요청에서 클라이언트는 `p` 매개 변수를 실행하기 위해 `scope` 매개 변수 및 정책에서 사용자로부터 가져와야 할 사용 권한을 나타냅니다. 각각 다른 정책을 사용하여 세 가지 예제가 아래에 제공됩니다.(가독성을 위해 구분선이 있음)

#### 로그인 정책 사용

```
GET https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=code
&redirect_uri=urn%3Aietf%3Awg%3Aoauth%3A2.0%3Aoob
&response_mode=query
&scope=openid%20offline_access
&state=arbitrary_data_you_can_receive_in_the_response
&p=b2c_1_sign_in
```

#### 등록 정책 사용

```
GET https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=code
&redirect_uri=urn%3Aietf%3Awg%3Aoauth%3A2.0%3Aoob
&response_mode=query
&scope=openid%20offline_access
&state=arbitrary_data_you_can_receive_in_the_response
&p=b2c_1_sign_up
```

#### 편집 프로필 정책 사용

```
GET https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=code
&redirect_uri=urn%3Aietf%3Awg%3Aoauth%3A2.0%3Aoob
&response_mode=query
&scope=openid%20offline_access
&state=arbitrary_data_you_can_receive_in_the_response
&p=b2c_1_edit_profile
```

| 매개 변수 | Required? | 설명 |
| ----------------------- | ------------------------------- | ----------------------- |
| client\_id | 필수 | [Azure 포털](https://portal.azure.com)이 앱에 할당된 응용 프로그램 ID입니다. |
| response\_type | 필수 | 응답 유형입니다. 인증 코드 흐름의 경우 `code`를 포함해야 합니다. |
| redirect\_uri | 필수 | 앱이 인증 응답을 보내고 받을 수 있는 앱의 redirect\_uri입니다. URL로 인코딩되어야 한다는 점을 제외하고 포털에서 등록한 redirect\_uri 중 하나와 정확히 일치해야 합니다. |
| scope | 필수 | 공백으로 구분된 범위 목록입니다. 단일 범위 값은 요청된 사용 권한을 모두 Azure AD에 나타냅니다. `openid` 범위는 사용자를 로그인시키고 **id\_token** 형식으로 사용자에 대한 데이터를 가져올 권한을 나타냅니다(자세한 내용은 이 문서의 뒷부분 참조). `offline_access` 범위는 앱이 리소스에 장기간 액세스할 수 있도록 **refresh\_token**이 필요함을 나타냅니다. |
| response\_mode | 권장 | 결과 authorization\_code를 앱에 다시 보내는 데 사용해야 하는 방법입니다. 'query', 'form\_post' 또는 'fragment' 중 하나일 수 있습니다.
| state | 권장 | 토큰 응답에도 반환되는 요청에 포함된 값입니다. 원하는 모든 콘텐츠의 문자열일 수 있습니다. 일반적으로 교차 사이트 요청 위조 공격을 방지하기 위해 임의로 생성된 고유 값이 사용됩니다. 상태는 인증 요청이 발생하기 전 앱의 사용자 상태에 대한 정보(예: 사용하거나 실행된 정책에 대한 페이지)를 인코드하는 데에도 사용됩니다. |
| p | 필수 | 실행할 정책입니다. B2C 디렉터리에 생성된 정책의 이름입니다. 정책 이름 값은 "b2c\_1\_"로 시작해야 합니다. 정책에 대한 자세한 내용은 [확장 가능한 정책 프레임워크](active-directory-b2c-reference-policies.md)를 참조하세요. |
| prompt | 옵션 | 필요한 사용자 상호 작용 유형입니다. 현재 유효한 값은 'login'뿐이며 강제로 사용자가 해당 요청에 자격 증명을 입력하도록 합니다. Single Sign-On은 적용되지 않습니다. |

이 때 사용자에게 정책의 워크플로 완료하도록 요청합니다. 해당 사용자 이름과 암호 입력, 소셜 ID로 로그인, 디렉터리에 등록하는 사용자 또는 정책을 정의하는 방식에 따라 다른 많은 단계를 포함합니다.

사용자가 정책을 완료하면 Azure AD가 `response_mode` 매개 변수에 지정된 방법을 사용하여 표시된 `redirect_uri`에서 해당 앱에 응답을 반환합니다. 응답은 위의 경우 각각에, 독립적으로 실행된 정책에 대해 정확히 동일합니다 합니다.

`response_mode=query`를 사용한 성공적인 응답은 다음과 같습니다.

```
GET urn:ietf:wg:oauth:2.0:oob?
code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...        // the authorization_code, truncated
&state=arbitrary_data_you_can_receive_in_the_response                // the value provided in the request
```

| 매개 변수 | 설명 |
| ----------------------- | ------------------------------- |
| 코드 | 앱이 요청한 authorization\_code입니다. 앱은 인증 코드를 사용하여 대상 리소스에 대한 액세스 토큰을 요청할 수 있습니다. Authorization\_code는 수명이 매우 짧습니다. 일반적으로 약 10분 후에 만료됩니다. |
| state | 전체 설명은 위 표를 참조하세요. 요청에 state 매개 변수가 포함되어 있으면 동일한 값이 응답에도 나타나야 합니다. 앱에서 요청 및 응답의 상태 값이 동일한지 확인해야 합니다. |

앱이 적절하게 처리할 수 있도록 `redirect_uri`에 오류 응답을 보낼 수도 있습니다.

```
GET urn:ietf:wg:oauth:2.0:oob?
error=access_denied
&error_description=The+user+has+cancelled+entering+self-asserted+information
&state=arbitrary_data_you_can_receive_in_the_response
```

| 매개 변수 | 설명 |
| ----------------------- | ------------------------------- |
| error | 발생하는 오류 유형을 분류하는 데 사용할 수 있고 오류에 대응하는 데 사용할 수 있는 오류 코드 문자열입니다. |
| error\_description | 개발자가 인증 오류의 근본 원인을 식별하도록 도울 수 있는 특정 오류 메시지입니다. |
| state | 전체 설명은 이 섹션의 첫 번째 표를 참조하세요. 요청에 state 매개 변수가 포함되어 있으면 동일한 값이 응답에도 나타나야 합니다. 앱에서 요청 및 응답의 상태 값이 동일한지 확인해야 합니다. |


## 2\. 토큰 가져오기
authorization\_code를 획득하였으므로 이제 `POST` 요청을 `/token` 끝점으로 보내 를 원하는 리소스에 대한 토큰을 `code`으로 교환할 수 있습니다. Azure AD B2C 미리 보기에서 토큰을 요청할 수 있는 리소스는 앱 자체의 백 엔드 Web API입니다. 자신에게 토큰을 요청하는 데 사용된 규칙은 `openid` 범위를 사용하는 것입니다.

```
POST fabrikamb2c.onmicrosoft.com/v2.0/oauth2/token?p=b2c_1_sign_in HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

grant_type=authorization_code&client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6&scope=openid offline_access&code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...&redirect_uri=urn:ietf:wg:oauth:2.0:oob

```

| 매개 변수 | Required? | 설명 |
| ----------------------- | ------------------------------- | --------------------- |
| p | 필수 | 권한 부여 코드를 획득하는 데 사용된 정책입니다. 이 요청에 다른 정책을 사용할 수 없습니다. 이 매개 변수는 POST 본문이 아니라 *쿼리 문자열*에 추가해야 합니다. |
| client\_id | 필수 | [Azure 포털](https://portal.azure.com)이 앱에 할당된 응용 프로그램 ID입니다. |
| grant\_type | 필수 | 권한 유형입니다. 인증 코드 흐름의 경우 `authorization_code`이어야 합니다. |
| scope | 필수 | 공백으로 구분된 범위 목록입니다. 단일 범위 값은 요청된 사용 권한을 모두 Azure AD에 나타냅니다. `openid` 범위는 사용자 로그인 및 **id\_token**의 형식으로 사용자에 대 한 데이터를 가져올 사용 권한을 나타냅니다. 앱 자체의 백 엔드 Web API에 토큰을 가져오기 위해 사용할 수 있으며 이는 클라이언트와 동일한 응용 프로그램 ID로 나타납니다. `offline_access` 범위는 앱이 리소스에 장기간 액세스할 수 있도록 **refresh\_token**이 필요함을 나타냅니다. |
| 코드 | 필수 | 흐름의 첫 번째 레그에서 얻은 authorization\_code입니다. |
| redirect\_uri | 필수 | authorization\_code을 받은 응용 프로그램의 redirect\_uri입니다. |

성공적인 토큰 응답은 다음과 같습니다.

```
{
	"not_before": "1442340812",
	"token_type": "Bearer",
	"id_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...",
	"scope": "openid offline_access",
	"id_token_expires_in": "3600",
	"profile_info": "eyJ2ZXIiOiIxLjAiLCJ0aWQiOiI3NzU1MjdmZi05YTM3LTQzMDctOGIzZC1jY...",
	"refresh_token": "AAQfQmvuDy8WtUv-sd0TBwWVQs1rC-Lfxa_NDkLqpg50Cxp5Dxj0VPF1mx2Z...",
	"refresh_token_expires_in": "1209600"
}
```
| 매개 변수 | 설명 |
| ----------------------- | ------------------------------- |
| not\_before | epoch 시간에서 토큰은 유효한 것으로 간주되는 시간입니다. |
| token\_type | 토큰 형식 값입니다. Azure AD는 전달자 유형만 지원합니다. |
| id\_token | 사용자가 요청한 서명된 JWT(JSON 웹 토큰) 토큰입니다. |
| scope | 토큰이 유효한 범위는 나중에 사용할 토큰 캐싱에 사용할 수 있습니다. |
| id\_token\_expires\_in | id\_token이 유효한 기간(초)입니다. |
| profile\_info | 네이티브 응용 프로그램에 표시하기 위해 사용자에 대한 유용한 정보를 포함하는 Base64로 인코딩된 JSON 문자열입니다. 정확한 내용은 정책에 구성된 응용 프로그램 클레임에 따라 달라집니다. |
| refresh\_token | OAuth 2.0 새로 고침 토큰입니다. 앱은 현재 토큰이 만료된 후 이 토큰을 사용하여 추가 토큰을 획득할 수 있습니다. refresh\_token은 수명이 길며, 오랜 시간 동안 리소스에 대한 액세스를 유지하는 데 사용할 수 있습니다. 자세한 내용은 [B2C 토큰 참조](active-directory-b2c-reference-tokens.md)를 참조하세요. |
| refresh\_token\_expires\_in | refresh\_token이 유효할 수 있는 최대 시간(초)입니다. 그러나 refresh\_token은 언제든지 무효화될 수 있습니다. |

> [AZURE.NOTE]
	이 시점에서 "access\_token가 어디 있는지" 생각하고 있다면 다음을 고려합니다. `openid` 범위를 요청하는 경우 Azure AD는 응답에서 JWT(JSON 웹 토큰) `id_token`를 발급합니다. 이 `id_token`은 기술적으로 OAuth 2.0 access\_token이 아니지만 앱 자체의 백 엔드 서비스와 통신할 때와 같은 경우에 사용될 수 있으며 클라이언트와 동일한 client\_id로 표시됩니다. `id_token`는 여전히 HTTP 권한 부여 헤더에 있는 리소스에 전송되고 요청을 인증하는 데 사용될 수 있는 서명된 JWT 전달자 토큰입니다. <br><br>차이점은 `id_token`에는 특정 클라이언트 응용 프로그램이 가질 수 있는 액세스 범위를 축소하는 메커니즘이 없다는 점입니다. 그러나 클라이언트 응용 프로그램이 백 엔드 서비스와 통신할 수 있는 클라이언트인 경우(현재 Azure AD B2C 미리 보기를 사용한 경우처럼) 범위 지정 메커니즘과 같은 것은 필요하지 않습니다. <br><br>Azure AD B2C에서 클라이언트가 추가적인 자사 및 타사 리소스와 통신할 수 있는 기능을 추가하는 경우 access\_token이 도입됩니다. 그러나 이 경우에도 `id_tokens`을 사용하여 앱의 고유한 백 엔드 서비스와 통신하는 것이 좋습니다. 자세한 내용은 Azure AD B2C 미리 보기로 빌드할 수 있는 [응용 프로그램 형식](active-directory-b2c-apps.md)을 참조하세요.

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

## 3\. 토큰 사용
`id_token`을 성공적으로 획득했으므로 이제 `Authorization` 헤더에 포함하여 백 엔드 Web API에 대한 요청에 토큰을 사용할 수 있습니다.

```
GET /tasks
Host: https://mytaskwebapi.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
```

## 4\. 토큰 새로 고침
id\_token은 수명이 짧습니다. 리소스에 계속 액세스하려면 만료된 후 새로 고쳐야 합니다. 이렇게 하려면 다른 `POST` 요청을 `/token` 끝점에 제출하면 됩니다. 여기에서는 `code` 대신 `refresh_token`을 제공합니다.

```
POST fabrikamb2c.onmicrosoft.com/v2.0/oauth2/token?p=b2c_1_sign_in HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

grant_type=refresh_token&client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6&scope=openid offline_access&refresh_token=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...&redirect_uri=urn:ietf:wg:oauth:2.0:oob
```

| 매개 변수 | Required? | 설명 |
| ----------------------- | ------------------------------- | -------- |
| p | 필수 | 원래 refresh\_token을 획득하는 데 사용된 정책입니다. 이 요청에 다른 정책을 사용할 수 없습니다. 이 매개 변수는 POST 본문이 아니라 *쿼리 문자열*에 추가해야 합니다. |
| client\_id | 필수 | [Azure 포털](https://portal.azure.com)이 앱에 할당된 응용 프로그램 ID입니다. |
| grant\_type | 필수 | 권한 유형입니다. 이 인증 코드 흐름 범례의 경우 `refresh_token`이어야 합니다. |
| scope | 필수 | 공백으로 구분된 범위 목록입니다. 단일 범위 값은 요청된 사용 권한을 모두 Azure AD에 나타냅니다. `openid` 범위는 사용자 로그인 및 **id\_token**의 형식으로 사용자에 대 한 데이터를 가져올 사용 권한을 나타냅니다. 앱 자체의 백 엔드 Web API에 토큰을 가져오기 위해 사용할 수 있으며 이는 클라이언트와 동일한 응용 프로그램 ID로 나타납니다. `offline_access` 범위는 앱이 리소스에 장기간 액세스할 수 있도록 **refresh\_token**이 필요함을 나타냅니다. |
| redirect\_uri | 필수 | authorization\_code을 받은 응용 프로그램의 redirect\_uri입니다. |
| refresh\_token | 필수 | 흐름의 두 번째 레그에서 얻은 원래 refresh\_token입니다. |

성공적인 토큰 응답은 다음과 같습니다.

```
{
	"not_before": "1442340812",
	"token_type": "Bearer",
	"id_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...",
	"scope": "openid offline_access",
	"id_token_expires_in": "3600",
	"profile_info": "eyJ2ZXIiOiIxLjAiLCJ0aWQiOiI3NzU1MjdmZi05YTM3LTQzMDctOGIzZC1jY...",
	"refresh_token": "AAQfQmvuDy8WtUv-sd0TBwWVQs1rC-Lfxa_NDkLqpg50Cxp5Dxj0VPF1mx2Z...",
	"refresh_token_expires_in": "1209600"
}
```
| 매개 변수 | 설명 |
| ----------------------- | ------------------------------- |
| not\_before | epoch 시간에서 토큰은 유효한 것으로 간주되는 시간입니다. |
| token\_type | 토큰 형식 값입니다. Azure AD는 전달자 유형만 지원합니다. |
| id\_token | 사용자가 요청한 서명된 JWT 토큰입니다. |
| scope | 토큰이 유효한 범위는 나중에 사용할 토큰 캐싱에 사용할 수 있습니다. |
| id\_token\_expires\_in | id\_token이 유효한 기간(초)입니다. |
| profile\_info | 네이티브 응용 프로그램에 표시하기 위해 사용자에 대한 유용한 정보를 포함하는 Base64로 인코딩된 JSON 문자열입니다. 정확한 내용은 정책에 구성된 응용 프로그램 클레임에 따라 달라집니다. |
| refresh\_token | OAuth 2.0 새로 고침 토큰입니다. 앱은 현재 토큰이 만료된 후 이 토큰을 사용하여 추가 토큰을 획득할 수 있습니다. refresh\_token은 수명이 길며, 오랜 시간 동안 리소스에 대한 액세스를 유지하는 데 사용할 수 있습니다. 자세한 내용은 [B2C 토큰 참조](active-directory-b2c-reference-tokens.md)를 참조하세요. |
| refresh\_token\_expires\_in | refresh\_token이 유효할 수 있는 최대 시간(초)입니다. 그러나 refresh\_token은 언제든지 무효화될 수 있습니다. |

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


<!--

Here is the entire flow for a native app; each request is detailed in the sections below:

![OAuth Auth code flow](./media/active-directory-b2c-reference-oauth-code/convergence_scenarios_native.png)

-->

## 사용자 고유의 B2C 디렉터리 사용

자신에 대한 이러한 요청을 사용해 보려는 경우 먼저 이러한 세 단계를 수행한 다음 위의 예제 값을 고유한 값으로 바꿉니다.

- [B2C 디렉터리를 만들고](active-directory-b2c-get-started.md), 요청에서 디렉터리의 이름을 사용합니다.
- [응용 프로그램을 만들어](active-directory-b2c-app-registration.md) 응용 프로그램 ID 및 redirect\_uri를 얻을 수 있습니다. 앱에서 **네이티브 클라이언트**를 포함하려 합니다.
- [정책을 만들어](active-directory-b2c-reference-policies.md) 정책 이름을 얻습니다.

<!---HONumber=AcomDC_0525_2016-->