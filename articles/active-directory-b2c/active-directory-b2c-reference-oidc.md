<properties
	pageTitle="Azure AD B2C 미리 보기 | Microsoft Azure"
	description="OpenID Connect 인증 프로토콜의 Azure AD의 구현을 사용하여 웹 응용 프로그램을 빌드합니다."
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
	ms.date="02/18/2016"
	ms.author="dastrock"/>

# Azure AD B2C 미리 보기: OpenID Connect로 웹 로그인

OpenID Connect는 웹 응용 프로그램에 사용자를 안전하게 로그인하는 데 사용할 수 있는 OAuth 2.0을 기반으로 빌드된 인증 프로토콜입니다. OpenID Connect의 Azure AD B2C 구현을 사용하여 웹 응용 프로그램의 등록, 로그인 및 기타 ID 관리 환경을 Azure AD로 아웃소싱할 수 있습니다. 이 가이드는 언어 독립적인 방식으로 수행하는 방식을 보여주며 공개 소스 라이브러리 중 하나를 사용하지 않고 HTTP 메시지를 수신하는 방법을 설명합니다.

<!-- TODO: Need link to libraries -->

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]

[OpenID Connect](http://openid.net/specs/openid-connect-core-1_0.html)는 *인증* 프로토콜로 사용하기 위해 OAuth 2.0 *권한 부여* 프로토콜을 확장하여 OAuth를 통해 Single Sign-On을 수행할 수 있게 합니다. 클라이언트가 사용자 ID를 확인하고 사용자에 대한 기본 프로필 정보를 얻을 수 있게 하는 보안 토큰인 `id_token`의 개념을 소개합니다. OAuth 2.0를 확장하기 때문에 또한 [인증 서버](active-directory-b2c-reference-protocols.md#the-basics)를 사용하여 보안된 리소스에 액세스하는 데 사용할 수 있는 **access\_tokens**을 앱이 안전하게 획득할 수 있도록 합니다. OpenID Connect는 서버에서 호스트되고 브라우저를 통해 액세스되는 웹 응용 프로그램을 빌드하는 경우 권장 사항입니다. Azure AD B2C를 사용하여 ID 관리를 모바일 또는 데스크톱 응용 프로그램에 추가하려는 경우 OpenID Connect 보다 [OAuth 2.0](active-directory-b2c-reference-oauth-code.md)를 사용해야 합니다.

Azure AD B2C는 단순한 인증 및 권한 부여 보다 더 많은 작업으로 표준 OpenID Connect 프로토콜을 확장합니다. [**정책 매개 변수**](active-directory-b2c-reference-policies.md)를 소개하며 OpenID Connect를 사용하여 등록, 로그인 및 프로필 관리와 같은 앱에 사용자 환경을 추가할 수 있습니다. 여기서 어떻게 OpenID Connect 및 정책을 사용하여 웹 응용 프로그램에서 각 이러한 환경을 구현하고 Web API에 액세스하기 위한 access\_tokens 가져오는지 보여줍니다.

아래 예제 HTTP 요청은 샘플 B2C 디렉터리, **fabrikamb2c.onmicrosoft.com** 뿐만 아니라 샘플 응용 프로그램****https://aadb2cplayground.azurewebsites.net** 및 정책을 사용합니다. 이러한 값을 사용하여 직접 요청을 시도하거나 고유의 작업으로 바꿀 수 있습니다. [사용자 고유의 B2C 테넌트, 응용 프로그램 및 정책을 가져오는](#use-your-own-b2c-directory) 방법을 알아봅니다.

## 인증 요청 보내기
웹앱이 사용자를 인증하고 정책을 실행해야 하는 경우 사용자를 `/authorize` 끝점으로 보낼 수 있습니다. 정책에 따라 사용자가 실제로 조치를 취하는 흐름의 대화형 부분입니다. 이 요청에서 클라이언트는 `p` 매개 변수를 실행하기 위해 `scope` 매개 변수 및 정책에서 사용자로부터 가져와야 할 사용 권한을 나타냅니다. 각각 다른 정책을 사용하여 세 가지 예제가 아래에 제공됩니다.(가독성을 위해 구분선이 있음) 각 요청의 작동 방식에 대해 이해하려면 요청을 브라우저에 붙여 넣고 실행합니다.

#### 로그인 정책 사용

```
GET https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=code+id_token
&redirect_uri=https%3A%2F%2Faadb2cplayground.azurewebsites.net%2F
&response_mode=form_post
&scope=openid%20offline_access
&state=arbitrary_data_you_can_receive_in_the_response
&nonce=12345
&p=b2c_1_sign_in
```

#### 등록 정책 사용

```
GET https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=code+id_token
&redirect_uri=https%3A%2F%2Faadb2cplayground.azurewebsites.net%2F
&response_mode=form_post
&scope=openid%20offline_access
&state=arbitrary_data_you_can_receive_in_the_response
&nonce=12345
&p=b2c_1_sign_up
```

#### 편집 프로필 정책 사용

```
GET https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=code+id_token
&redirect_uri=https%3A%2F%2Faadb2cplayground.azurewebsites.net%2F
&response_mode=fragment
&scope=openid%20offline_access
&state=arbitrary_data_you_can_receive_in_the_response
&nonce=12345
&p=b2c_1_edit_profile
```

| 매개 변수 | | 설명 |
| ----------------------- | ------------------------------- | ----------------------- |
| client\_id | 필수 | [Azure 포털](https://portal.azure.com/)이 앱에 할당된 응용 프로그램 ID입니다. |
| response\_type | 필수 | OpenID Connect를 위한 `id_token`이 포함되어야 합니다. 웹앱이 Web API를 호출하기 위해 토큰이 필요한 경우 여기서 수행한 대로 `code+id_token`를 사용할 수 있습니다. |
| redirect\_uri | 필수 | 앱이 인증 응답을 보내고 받을 수 있는 앱의 redirect\_uri입니다. URL로 인코드되어야 한다는 점을 제외하고 포털에서 등록한 redirect\_uri 중 하나와 정확히 일치해야 합니다. |
| scope | 필수 | 공백으로 구분된 범위 목록입니다. 단일 범위 값은 요청된 사용 권한을 모두 Azure AD에 나타냅니다. `openid` 범위는 사용자 로그인 및 **id\_tokens**의 형식으로 사용자에 대 한 데이터를 가져올 사용 권한을 나타냅니다.(앞으로 추가됨) `offline_access` 범위는 웹앱에 대한 선택 사항입니다. 앱이 리소스에 장기간 액세스할 수 있도록 **refresh\_token**이 필요함을 나타냅니다. |
| response\_mode | 권장 | 결과 authorization\_code를 앱에 다시 보내는 데 사용해야 하는 방법을 지정합니다. 'query', 'form\_post' 또는 'fragment' 중 하나일 수 있습니다. |
| state | 권장 | 토큰 응답에도 반환되는 요청에 포함된 값입니다. 원하는 모든 콘텐츠의 문자열일 수 있습니다. 일반적으로 교차 사이트 요청 위조 공격을 방지하기 위해 임의로 생성된 고유 값이 사용됩니다. 상태는 인증 요청이 발생하기 전 앱의 사용자 상태에 대한 정보(예: 사용한 페이지)를 인코드하는 데에도 사용됩니다. |
| nonce | 필수 | 결과 id\_token에 클레임으로 포함되는, 앱에서 생성한 요청에 포함되는 값입니다. 그러면 앱이 이 값을 확인하여 토큰 재생 공격을 완화시킬 수 있습니다. 값은 일반적으로 요청의 출처를 식별하는 데 사용할 수 있는 임의의 고유 문자열입니다. |
| p | 필수 | 실행할 정책을 나타냅니다. "b2c\_1\_"로 시작하는 값을 가진 B2C 테넌트에 작성된 정책의 이름입니다. [여기](active-directory-b2c-reference-policies.md)서 정책에 대해 자세한 알아봅니다. |
| prompt | 선택 사항 | 필요한 사용자 상호 작용 유형을 나타냅니다. 현재 유효한 값은 'login'뿐이며 강제로 사용자가 해당 요청에 자격 증명을 입력하도록 합니다. Single Sign-On은 적용되지 않습니다. |

이 때 사용자에게 정책의 워크플로 완료하도록 요청합니다. 해당 사용자 이름과 암호 입력, 소셜 ID로 로그인, 디렉터리에 등록하는 사용자 또는 정책을 정의하는 방식에 따라 다른 많은 단계를 포함합니다. 사용자가 정책을 완료하면 Azure AD가 `response_mode` 매개 변수에 지정된 방법을 사용하여 표시된 `redirect_uri`에서 해당 앱에 응답을 반환합니다. 응답은 위의 경우 각각에, 독립적으로 실행된 정책에 대해 정확히 동일합니다 합니다.

`response_mode=query`를 사용한 성공적인 응답은 다음과 같습니다.

```
GET https://aadb2cplayground.azurewebsites.net/#
id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
&code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...
&state=arbitrary_data_you_can_receive_in_the_response
```

| 매개 변수 | 설명 |
| ----------------------- | ------------------------------- |
| id\_token | 앱이 요청한 id\_token입니다. id\_token을 사용하여 사용자 ID를 확인하고 사용자와 세션을 시작할 수 있습니다. Id\_token 및 해당 콘텐츠에 대한 자세한 내용은 [Azure AD B2C 토큰 참조](active-directory-b2c-reference-tokens.md)에 포함되어 있습니다. |
| 코드 | `response_type=code+id_token`을 사용한 경우 앱이 요청한 authorization\_code입니다. 앱은 인증 코드를 사용하여 대상 리소스에 대한 액세스 토큰을 요청할 수 있습니다. authorization\_code는 수명이 매우 짧으며, 일반적으로 약 10분 후에 만료됩니다. |
| state | 요청에 state 매개 변수가 포함되어 있으면 동일한 값이 응답에도 나타나야 합니다. 앱은 요청 및 응답의 상태 값이 동일한지 확인해야 합니다. |

앱이 적절하게 처리할 수 있도록 `redirect_uri`에 오류 응답을 보낼 수도 있습니다.

```
GET https://aadb2cplayground.azurewebsites.net/#
error=access_denied
&error_description=the+user+canceled+the+authentication
&state=arbitrary_data_you_can_receive_in_the_response
```

| 매개 변수 | 설명 |
| ----------------------- | ------------------------------- |
| error | 발생하는 오류 유형을 분류하는 데 사용할 수 있고 오류에 대응하는 데 사용할 수 있는 오류 코드 문자열입니다. |
| error\_description | 개발자가 인증 오류의 근본 원인을 식별하도록 도울 수 있는 특정 오류 메시지입니다. |
| state | 요청에 state 매개 변수가 포함되어 있으면 동일한 값이 응답에도 나타나야 합니다. 앱은 요청 및 응답의 상태 값이 동일한지 확인해야 합니다. |


## id\_token 유효성 검사
id\_token을 받는 것만으로는 사용자를 인증하는 데 충분하지 않습니다. id\_token의 서명 유효성을 검사하고 앱의 요구 사항에 따라 토큰의 클레임을 확인해야 합니다. Azure AD B2C는 [JWT(JSON 웹 토큰)](http://self-issued.info/docs/draft-ietf-oauth-json-web-token.html) 및 공개 키 암호화를 사용하여 토큰에 서명하고 토큰이 유효한지 확인합니다. 기본 설정의 언어에 따라 JWT의 유효성 검사에 사용할 수 있는 다양한 공개 소스 라이브러리가 있습니다. 고유한 유효성 검사 논리 보다는 이러한 옵션을 탐색하는 것이 좋습니다. 여기의 정보는 올바르게 해당 라이브러리를 사용하는 방법을 파악하는 데 도움이 됩니다.

Azure AD B2C에는 앱이 런타임에 Azure AD B2C에 대한 정보를 가져올 수 있게 해주는 OpenID Connect 메타데이터 끝점이 있습니다. 이 정보에는 끝점, 토큰 콘텐츠 및 토큰 서명 키가 포함됩니다. B2C 테넌트에서 각 정책에 대한 JSON 메타데이터 문서가 있습니다. 예를 들어 `fabrikamb2c.onmicrosoft.com`의 `b2c_1_sign_in` 정책에 대한 메타데이터 문서는 다음에서 찾을 수 있습니다.

`https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/v2.0/.well-known/openid-configuration?p=b2c_1_sign_in`

이 구성 문서의 속성 중 하나는 `jwks_uri`이며, 동일한 정책에 대한 해당 값은 다음과 같습니다.

`https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/discovery/v2.0/keys?p=b2c_1_sign_in`.

id\_token을 서명하는 데 어떤 정책을 사용할지(그리고 메타데이터를 인출하는 위치)를 결정하기 위해 두 가지 옵션이 있습니다. 먼저 정책 이름은 id\_token의 `acr` 클레임에 포함됩니다. id\_token에서 클레임을 구문 분석하는 방법에 대한 내용은 [Azure AD B2C 토큰 참조](active-directory-b2c-reference-tokens.md)를 참조하세요. 다른 옵션은 요청을 실행할 때 `state` 매개 변수의 값에 정책을 인코딩한 다음 이를 디코딩하여 어떤 정책을 사용할지 결정하는 것입니다. 두 방법 모두 완벽하게 유효합니다.

OpenID Connect 메타데이터 끝점에서 메타데이터 문서를 획득하면 이 끝점에 위치한 RSA256 공용 키를 사용하여 id\_token의 서명의 유효성을 검사할 수 있습니다. 이 끝점에는 항상 여러 키가 나열될 수 있으며, 각각 `kid`로 식별됩니다. id\_token의 헤더에는 id\_token 서명에 사용된 키를 나타내는 `kid` 클레임도 포함됩니다. [토큰 유효성 검사](active-directory-b2c-reference-tokens.md#validating-tokens) 및 [서명 키 롤오버에 대한 중요한 정보](active-directory-b2c-reference-tokens.md#validating-tokens)를 포함하여 자세한 내용은 [Azure AD B2C 토큰 참조](active-directory-b2c-reference-tokens.md)를 참조하세요.<!--TODO: Improve the information on this-->

id\_token의 서명 유효성을 검사한 후 확인해야 하는 몇 개의 클레임이 있습니다.

- `nonce` 클레임의 유효성을 검사하여 토큰 재생 공격을 방지해야 합니다. 해당 값이 로그인 요청에 지정된 값이어야 합니다.
- `aud` 클레임의 유효성을 검사하여 id\_token이 앱에 대해 발급된 것인지 확인해야 합니다. 해당 값이 앱의 응용 프로그램 ID여야 합니다.
- `iat` 및 `exp` 클레임의 유효성을 검사하여 id\_token이 만료되지 않았는지 확인해야 합니다.

시나리오에 따라 추가 클레임의 유효성을 검사할 수도 있습니다. 몇 가지 일반적인 유효성 검사는 다음과 같습니다.

- 사용자/조직이 앱에 등록했는지 확인
- 사용자에게 적절한 권한 부여/권한이 있는지 확인
- 다단계 인증과 같은 특정 강도의 인증이 발생했는지 확인

id\_token의 클레임에 대한 자세한 내용은 [Azure AD B2C 토큰 참조](active-directory-b2c-reference-tokens.md)를 참조하세요.

id\_token의 유효성을 완전히 검사한 후 사용자와 세션을 시작하고 id\_token의 클레임을 사용하여 앱에서 사용자 정보를 가져올 수 있습니다. 이 정보는 표시, 레코드, 권한 부여 등에 사용될 수 있습니다.

## 토큰 가져오기
웹앱이 정책을 실행하기만 하면 다음 섹션을 몇 개 건너뛸 수 있습니다. 이러한 섹션은 Azure AD B2C에서 보호되는 Web API에 인증된 호출을 만들어야 하는 웹앱에만 적용할 수 있습니다.

`POST` 요청을 `/token` 끝점으로 보내 원하는 리소스에 대한 토큰에 대해 (`response_type=code+id_token`을 사용하여) 획득한 authorization\_code를 사용할 수 있습니다. Azure AD B2C 미리 보기에서 토큰을 요청할 수 있는 리소스는 앱 자체의 백 엔드 Web API입니다. 자신에게 토큰을 요청하는 데 사용된 규칙은 `openid` 범위를 사용하는 것입니다.

```
POST fabrikamb2c.onmicrosoft.com/v2.0/oauth2/token?p=b2c_1_sign_in HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/json

{
	"grant_type": "authorization_code",
	"client_id": "90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6",
	"scope": "openid offline_access",
	"code": "AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...",
	"redirect_uri": "urn:ietf:wg:oauth:2.0:oob",
	"client_secret": "<your-application-secret>"
}
```

| 매개 변수 | | 설명 |
| ----------------------- | ------------------------------- | --------------------- |
| p | 필수 | 권한 부여 코드를 획득하는 데 사용된 정책입니다. 이 요청에 다른 정책을 사용할 수 없습니다. **이 매개 변수는 쿼리 문자열에 추가 됩니다**. 게시 본문은 안됩니다. |
| client\_id | 필수 | [Azure 포털](https://portal.azure.com/)이 앱에 할당된 응용 프로그램 ID입니다. |
| grant\_type | 필수 | 인증 코드 흐름에 대한 `authorization_code`여야 합니다. |
| scope | 필수 | 공백으로 구분된 범위 목록입니다. 단일 범위 값은 요청된 사용 권한을 모두 Azure AD에 나타냅니다. `openid` 범위는 사용자 로그인 및 **id\_tokens**의 형식으로 사용자에 대 한 데이터를 가져올 사용 권한을 나타냅니다. 앱 자체의 백 엔드 Web API에 토큰을 가져오기 위해 사용할 수 있으며 이는 클라이언트와 동일한 응용 프로그램 ID로 나타납니다. `offline_access` 범위는 앱이 리소스에 장기간 액세스할 수 있도록 **refresh\_token**이 필요함을 나타냅니다. |
| 코드 | 필수 | 흐름의 첫 번째 레그에서 얻은 authorization\_code입니다. |
| redirect\_uri | 필수 | authorization\_code을 받은 응용 프로그램의 redirect\_uri입니다. |
| client\_secret | 필수 | [Azure 포털](https://portal.azure.com/)에서 생성한 응용 프로그램 암호입니다. 이 응용 프로그램 암호는 중요한 보안 아티팩트이며 서버에 안전하게 저장해야 합니다. 또한 이 클라이언트 암호를 정기적으로 순환하도록 주의해야 합니다. |

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
| token\_type | 토큰 유형 값을 나타냅니다. Azure AD는 전달자 유형만 지원합니다. |
| id\_token | 사용자가 요청한 서명된 JWT 토큰입니다. |
| scope | 토큰이 유효한 범위는 나중에 사용할 토큰 캐싱에 사용할 수 있습니다. |
| id\_token\_expires\_in | id\_token이 유효한 기간(초)입니다. |
| profile\_info | 네이티브 응용 프로그램에 표시하기 위해 사용자에 대한 유용한 정보를 포함하는 base-64로 인코딩된 JSON 문자열입니다. 정확한 내용은 정책에 구성된 응용 프로그램 클레임에 따라 달라집니다. |
| refresh\_token | OAuth 2.0 새로 고침 토큰입니다. 앱은 현재 토큰이 만료된 후 이 토큰을 사용하여 추가 토큰을 획득할 수 있습니다. refresh\_token은 수명이 길며, 오랜 시간 동안 리소스에 대한 액세스를 유지하는 데 사용할 수 있습니다. 자세한 내용은 [B2C 토큰 참조](active-directory-b2c-reference-tokens.md)를 참조하세요. 권한 부여 및 토큰 요청에서 새로 고침 토큰을 수신하기 위해 범위 `offline_access`를 사용했습니다. |
| refresh\_token\_expires\_in | 최대 시간 새로 고침 토큰은 (초)동안 유효할 수 있습니다. 그러나 새로 고침 토큰은 언제든지 잘못될 수 있습니다. |

> [AZURE.NOTE]
	이 시점에서 "access\_token가 어디 있는지" 생각하고 있다면 다음을 고려합니다. `openid` 범위를 요청하는 경우 Azure AD는 응답에서 JWT `id_token`를 발급합니다. `id_token`이 기술적으로 OAuth 2.0 access\_token이 아닌 반면 앱 자체의 백 엔드 서비스와 통신할 때와 같이 사용될 수 있으며 클라이언트와 동일한 client\_id로 나타납니다. `id_token`는 여전히 HTTP 권한 부여 헤더에 있는 리소스에 전송되고 요청을 인증하는 데 사용될 수 있는 서명된 JWT 전달자 토큰입니다. 차이점은 `id_token`이 특정 클라이언트 응용 프로그램이 가질 수 있는 액세스 범위를 축소하는 메커니즘이 없다는 점입니다. 그러나 클라이언트 응용 프로그램이 백 엔드 서비스와 통신할 수 있는 클라이언트인 경우(현재 Azure AD B2C 미리 보기를 사용한 경우처럼) 범위 지정 메커니즘과 같은 것은 필요하지 않습니다. Azure AD B2C 미리 보기는 추가 본사 및 타사 리소스와 통신하는 클라이언트에 대한 기능을 추가하며 access\_tokens을 소개합니다. 그러나 해당 시간에도 앱 자체의 백 엔드 서비스와 통신하는 데 `id_tokens`을 사용하는 것이 추천되는 패턴입니다. Azure AD B2C 미리 보기로 빌드할 수 있는 응용 프로그램의 형식에 대해 자세한 정보는 [이 문서](active-directory-b2c-apps.md)를 참조하세요.

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

## 토큰 사용
`id_token`을 성공적으로 획득했으므로 이제 `Authorization` 헤더에 포함하여 백 엔드 Web API에 대한 요청에 토큰을 사용할 수 있습니다.

```
GET /tasks
Host: https://mytaskwebapi.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
```

## 토큰 새로 고침
Id\_token은 수명이 짧으며, 만료되면 새로 고쳐야 리소스에 계속 액세스할 수 있습니다. 이렇게 하려면 다른 `POST` 요청을 `/token` 끝점에 제출해야 하며, 이번에는 `code` 대신 `refresh_token`을 제공해야 합니다.

```
POST fabrikamb2c.onmicrosoft.com/v2.0/oauth2/token?p=b2c_1_sign_in HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/json

{
	"grant_type": "refresh_token",
	"client_id": "90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6",
	"scope": "openid offline_access",
	"refresh_token": "AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...",
	"redirect_uri": "urn:ietf:wg:oauth:2.0:oob",
	"client_secret": "<your-application-secret>"
}
```

| 매개 변수 | | 설명 |
| ----------------------- | ------------------------------- | -------- |
| p | 필수 | 원래 새로 고침 토큰을 획득하는 데 사용된 정책입니다. 이 요청에 다른 정책을 사용할 수 없습니다. **이 매개 변수는 쿼리 문자열에 추가 됩니다**. 게시 본문은 안됩니다. |
| client\_id | 필수 | [Azure 포털](https://portal.azure.com/)이 앱에 할당된 응용 프로그램 ID입니다. |
| grant\_type | 필수 | 이 인증 코드 흐름 범례에 대한 `refresh_token`이어야 합니다. |
| scope | 필수 | 공백으로 구분된 범위 목록입니다. 단일 범위 값은 요청된 사용 권한을 모두 Azure AD에 나타냅니다. `openid` 범위는 사용자 로그인 및 **id\_tokens**의 형식으로 사용자에 대 한 데이터를 가져올 사용 권한을 나타냅니다. 앱 자체의 백 엔드 Web API에 토큰을 가져오기 위해 사용할 수 있으며 이는 클라이언트와 동일한 응용 프로그램 ID로 나타납니다. `offline_access` 범위는 앱이 리소스에 장기간 액세스할 수 있도록 **refresh\_token**이 필요함을 나타냅니다. |
| redirect\_uri | 필수 | authorization\_code을 받은 응용 프로그램의 redirect\_uri입니다. |
| refresh\_token | 필수 | 흐름의 두 번째 레그에서 얻은 원래 refresh\_token입니다. 권한 부여 및 토큰 요청에서 새로 고침 토큰을 수신하기 위해 범위 `offline_access`를 사용했습니다. |
| client\_secret | 필수 | [Azure 포털](https://portal.azure.com/)에서 생성한 응용 프로그램 암호입니다. 이 응용 프로그램 암호는 중요한 보안 아티팩트이며 서버에 안전하게 저장해야 합니다. 또한 이 클라이언트 암호를 정기적으로 순환하도록 주의해야 합니다. |

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
| token\_type | 토큰 유형 값을 나타냅니다. Azure AD는 전달자 유형만 지원합니다. |
| id\_token | 사용자가 요청한 서명된 JWT 토큰입니다. |
| scope | 토큰이 유효한 범위는 나중에 사용할 토큰 캐싱에 사용할 수 있습니다. |
| id\_token\_expires\_in | id\_token이 유효한 기간(초)입니다. |
| profile\_info | 네이티브 응용 프로그램에 표시하기 위해 사용자에 대한 유용한 정보를 포함하는 base-64로 인코딩된 JSON 문자열입니다. 정확한 내용은 정책에 구성된 응용 프로그램 클레임에 따라 달라집니다. |
| refresh\_token | OAuth 2.0 새로 고침 토큰입니다. 앱은 현재 토큰이 만료된 후 이 토큰을 사용하여 추가 토큰을 획득할 수 있습니다. refresh\_token은 수명이 길며, 오랜 시간 동안 리소스에 대한 액세스를 유지하는 데 사용할 수 있습니다. 자세한 내용은 [B2C 토큰 참조](active-directory-b2c-reference-tokens.md)를 참조하세요. |
| refresh\_token\_expires\_in | 최대 시간 새로 고침 토큰은 (초)동안 유효할 수 있습니다. 그러나 새로 고침 토큰은 언제든지 잘못될 수 있습니다. |

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

Here is the entire flow for a native  app; each request is detailed in the sections below:

![OAuth Auth Code Flow](./media/active-directory-b2c-reference-oauth-code/convergence_scenarios_native.png)

-->

## 로그아웃 요청 보내기

앱에서 사용자를 로그아웃시키려는 경우 앱의 쿠키를 삭제하거나 그렇지 않은 경우 사용자로 세션을 지우는 것은 충분하지 않습니다. 또한 로그아웃의 경우 사용자를 Azure AD로 리디렉션해야 합니다. 이렇게 하지 않으면 사용자는 Azure AD를 사용하여 유효한 단일 로그인 세션이 있기 때문에 자격 증명을 다시 입력하지 않고 앱에 다시 인증할 수 있습니다.

[위에](#validate-the-id-token) 설명한 동일한 OpenID Connect 메타데이터 문서에 나열된 `end_session_endpoint`에 사용자를 단순히 리디렉션할 수 있습니다.

```
GET https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/logout?
p=b2c_1_sign_in
&post_logout_redirect_uri=https%3A%2F%2Faadb2cplayground.azurewebsites.net%2F
```

| 매개 변수 | | 설명 |
| ----------------------- | ------------------------------- | ------------ |
| p | 필수 | 사용자가 가장 최근에 응용 프로그램에 로그인하기 위해 사용한 정책입니다. |
| post\_logout\_redirect\_uri | 권장 | 성공적으로 로그아웃한 후에 사용자가 리디렉션되는 URL입니다. 포함되지 않은 경우 Azure AD B2C에서 사용자에게 일반 메시지를 표시합니다. |

> [AZURE.NOTE]
	사용자를 `end_session_endpoint`로 지시하면 Azure AD B2C를 사용하여 일부 사용자의 Single Sign-On 상태를 취소하지만 사용자를 해당 사용자의 소셜 IDP 세션에서 로그아웃시키지 않습니다. 사용자가 다음 로그인하는 동안 같은 IDP를 선택하는 경우 자격 증명을 입력하지 않아도 다시 인증됩니다다. 사용자가 B2C 응용 프로그램에서 로그아웃하려는 경우 반드시 완전히 Facebook 계정을 로그아웃한다는 의미는 아닙니다. 그러나 로컬 계정의 경우 사용자의 세션이 올바르게 종료됩니다.

## 사용자 고유의 B2C 테넌트 사용

자신에 대한 이러한 요청을 사용해 보려는 경우 먼저 이러한 세 단계를 수행한 다음 위의 예제 값을 고유한 값으로 바꿉니다.

- [B2C 테넌트를 만들고](active-directory-b2c-get-started.md), 요청에서 테넌트의 이름을 사용합니다.
- [응용 프로그램을 만들어](active-directory-b2c-app-registration.md) 응용 프로그램 ID 및 redirect\_uri를 얻을 수 있습니다. 앱에서 **웹앱/Web API**을 포함하고 필요에 따라 **응용 프로그램 암호**를 만들려 합니다.
- [정책을 만들어](active-directory-b2c-reference-policies.md) 정책 이름을 얻습니다.

<!--

TODO

OpenID Connect for the v2.0 app model is the recommended way to implement sign-in for a [web  app](active-directory-v2-flows.md#web-apps).  The most basic sign-in flow contains the following steps:

image goes here

-->

<!---HONumber=AcomDC_0224_2016-->