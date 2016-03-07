
<properties
	pageTitle="Azure AD v2.0 OAuth 인증 코드 흐름 | Microsoft Azure"
	description="OAuth 2.0 인증 프로토콜의 Azure AD의 구현을 사용하여 웹 응용 프로그램을 빌드합니다."
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
	ms.date="02/20/2016"
	ms.author="dastrock"/>

# v2.0 프로토콜 - OAuth 2.0 인증 코드 흐름

OAuth 2.0 인증 코드 권한은 장치에 설치된 앱에서 사용하여 Web API와 같은 보호된 리소스에 대한 액세스 권한을 얻을 수 있습니다. 앱 모델 v2.0의 OAuth 2.0 구현을 사용하여, 로그인 및 모바일 및 API 액세스를 데스크톱 앱에 추가할 수 있습니다. 이 가이드는 언어 독립적이며 공개 소스 라이브러리 중 하나를 사용하지 않고 HTTP 메시지를 수신하는 방법을 설명합니다.

<!-- TODO: Need link to libraries -->

> [AZURE.NOTE]
	일부 Azure Active Directory 시나리오 및 기능만 v2.0 끝점에서 지원합니다. v2.0 끝점을 사용해야 하는지 확인하려면 [v2.0 제한 사항](active-directory-v2-limitations.md)을 참조하세요.

OAuth 2.0 인증 코드 흐름은 [OAuth 2.0 사양의 섹션 4.1](http://tools.ietf.org/html/rfc6749)에서 설명합니다. [웹앱](active-directory-v2-flows.md#web-apps) 및 [기본적으로 설치된 앱](active-directory-v2-flows.md#mobile-and-native-apps)을 포함하여 대부분의 앱 형식에서 인증 및 권한 부여를 수행하는 데 사용됩니다. v2.0 끝점을 사용하여 보안된 리소스에 액세스하는 데 사용할 수 있는 access\_token을 앱이 안전하게 획득할 수 있도록 합니다.

## 프로토콜 다이어그램
높은 수준에서 네이티브/모바일 응용 프로그램에 대한 전체 인증 흐름은 다음과 같습니다.

![OAuth 인증 코드 흐름](../media/active-directory-v2-flows/convergence_scenarios_native.png)

## 인증 코드 요청
인증 코드 흐름은 클라이언트가 사용자를 `/authorize` 끝점으로 보내는 것으로 시작됩니다. 이 요청에서 클라이언트는 사용자로부터 얻어야 하는 사용 권한을 나타냅니다.

```
// Line breaks for legibility only

https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&response_type=code
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&response_mode=query
&scope=openid%20offline_access%20https%3A%2F%2Fgraph.microsoft.com%2Fmail.read
&state=12345
```

> [AZURE.TIP] 아래 요청을 브라우저에 붙여 넣으세요!

```
https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&response_type=code&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F&response_mode=query&scope=openid%20offline_access%20https%3A%2F%2Fgraph.microsoft.com%2Fmail.read&state=12345
```

| 매개 변수 | | 설명 |
| ----------------------- | ------------------------------- | --------------- |
| tenant | 필수 | 요청의 경로에 있는 `{tenant}` 값을 사용하여 응용 프로그램에 로그인할 수 있는 사용자를 제어할 수 있습니다. 허용되는 값은 `common`, `organizations`, `consumers` 및 테넌트 ID입니다. 자세한 내용은 [프로토콜 기본](active-directory-v2-protocols.md#endpoints)을 참조하세요. |
| client\_id | 필수 | 등록 포털([apps.dev.microsoft.com](https://apps.dev.microsoft.com))에서 앱에 할당한 응용 프로그램 ID입니다. |
| response\_type | 필수 | 인증 코드 흐름에 대한 `code`를 포함해야 합니다. |
| redirect\_uri | 권장 | 앱이 인증 응답을 보내고 받을 수 있는 앱의 redirect\_uri입니다. URL로 인코드되어야 한다는 점을 제외하고 포털에서 등록한 redirect\_uri 중 하나와 정확히 일치해야 합니다. 네이티브 및 모바일 앱의 경우 `urn:ietf:wg:oauth:2.0:oob`의 기본값을 사용해야 합니다. |
| scope | 필수 | 사용자가 동의하게 할 공백으로 구분된 [범위](active-directory-v2-scopes.md) 목록입니다. |
| response\_mode | 권장 | 결과 토큰을 앱에 다시 보내는 데 사용해야 하는 방법을 지정합니다. `query` 또는 `form_post`일 수 있습니다. |
| state | 권장 | 토큰 응답에도 반환되는 요청에 포함된 값입니다. 원하는 모든 콘텐츠의 문자열일 수 있습니다. 일반적으로 [교차 사이트 요청 위조 공격을 방지](http://tools.ietf.org/html/rfc6749#section-10.12)하기 위해 임의로 생성된 고유 값이 사용됩니다. 상태는 인증 요청이 발생하기 전 앱의 사용자 상태에 대한 정보(예: 사용한 페이지 또는 보기)를 인코드하는 데에도 사용됩니다. |
| prompt | 선택 사항 | 필요한 사용자 상호 작용 유형을 나타냅니다. 이 경우 유효한 값은 'login', 'none', 'consent'뿐입니다. `prompt=login`은 Single-Sign On을 무효화면서, 사용자가 요청에 자신의 자격 증명을 입력하도록 합니다. `prompt=none`은 그 반대로 사용자에게 어떠한 대화형 프롬프트도 표시되지 않도록 합니다. Single-Sign On을 통해 요청이 자동으로 완료될 수 없는 경우에 v2.0 끝점은 오류를 반환합니다. `prompt=consent`는 사용자가 로그인한 후에 OAuth 동의 대화 상자를 트리거하여 앱에 권한을 부여할 것을 사용자에게 요청합니다. |
| login\_hint | 선택 사항 | 사용자 이름을 미리 알고 있는 경우 사용자를 위해 로그인 페이지의 사용자 이름/이메일 주소 필드를 미리 채우는 데 사용될 수 있습니다. `preferred_username` 클레임을 사용하여 이전 로그인 작업에서 사용자 이름이 이미 추출된 경우 앱이 재인증 과정에서 이 매개 변수를 종종 사용합니다. |
| domain\_hint | 선택 사항 | `consumers` 또는 `organizations` 중 하나일 수 있습니다. 포함된 경우, v2.0 로그인 페이지를 거친 사용자가 좀 더 효율적인 사용자 경험을 가질 수 있도록 전자 메일 기반 검색 프로세스를 건너뜁니다. 앱이 이전 로그인 작업에서 `tid`를 추출하여 재인증 과정에서 이 매개 변수를 종종 사용합니다. `tid` 클레임 값이 `9188040d-6c67-4c5b-b112-36a304b66dad`인 경우 `domain_hint=consumers`를 사용해야 합니다. 그렇지 않으면 `domain_hint=organizations`를 사용합니다. |

이 시점에서 사용자에게 자격 증명을 입력하고 인증을 완료하라는 메시지가 표시됩니다. v2.0 끝점은 사용자가 `scope` 쿼리 매개 변수에 표시된 사용 권한에 동의했는지도 확인합니다. 사용자가 이러한 사용 권한 중 하나에 동의하지 않은 경우 필요한 사용 권한에 동의하라는 메시지가 표시됩니다. [사용 권한, 동의 및 다중 테넌트 앱의 세부 정보는 여기에 제공되어 있습니다](active-directory-v2-scopes.md).

사용자가 인증하고 동의하면 v2.0 끝점이 `response_mode` 매개 변수에 지정된 방법을 사용하여 표시된 `redirect_uri`에서 해당 앱에 응답을 반환합니다.

#### 성공적인 응답
`response_mode=query`를 사용한 성공적인 응답은 다음과 같습니다.

```
GET urn:ietf:wg:oauth:2.0:oob?
code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...
&state=12345
```

| 매개 변수 | 설명 |
| ----------------------- | ------------------------------- |
| 코드 | 앱이 요청한 authorization\_code입니다. 앱은 인증 코드를 사용하여 대상 리소스에 대한 액세스 토큰을 요청할 수 있습니다. authorization\_code는 수명이 매우 짧으며, 일반적으로 약 10분 후에 만료됩니다. |
| state | 요청에 state 매개 변수가 포함되어 있으면 동일한 값이 응답에도 나타나야 합니다. 앱은 요청 및 응답의 상태 값이 동일한지 확인해야 합니다. |

#### 오류 응답
앱이 적절하게 처리할 수 있도록 `redirect_uri`에 오류 응답을 보낼 수도 있습니다.

```
GET urn:ietf:wg:oauth:2.0:oob?
error=access_denied
&error_description=the+user+canceled+the+authentication
```

| 매개 변수 | 설명 |
| ----------------------- | ------------------------------- |
| error | 발생하는 오류 유형을 분류하는 데 사용할 수 있고 오류에 대응하는 데 사용할 수 있는 오류 코드 문자열입니다. |
| error\_description | 개발자가 인증 오류의 근본 원인을 식별하도록 도울 수 있는 특정 오류 메시지입니다. |

## 액세스 토큰 요청
authorization\_code를 획득하고 사용자가 사용 권한을 부여했으므로 이제 `POST` 요청을 `/token` 끝점으로 보내 `code`를 원하는 리소스에 대한 `access_token`으로 교환할 수 있습니다.

```
// Line breaks for legibility only

POST /{tenant}/oauth2/v2.0/token HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&scope=https%3A%2F%2Fgraph.microsoft.com%2Fmail.read
&code=OAAABAAAAiL9Kn2Z27UubvWFPbm0gLWQJVzCTE9UkP3pSx1aXxUjq3n8b2JRLk4OxVXr...
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&grant_type=authorization_code
&client_secret=JqQX2PNo9bpM0uEihUPzyrh    // NOTE: Only required for web apps
```

> [AZURE.TIP] 아래 curl 명령을 postman으로 가져옵니다!(성공하려면 `code`를 사용자 고유의 것으로 바꿔야 합니다.)

```
curl -X POST -H "Cache-Control: no-cache" -H "Content-Type: application/x-www-form-urlencoded" -d 'client_id=6731de76-14a6-49ae-97bc-6eba6914391e&scope=https%3A%2F%2Fgraph.microsoft.com%2Fmail.read&code=OAAABAAAAiL9Kn2Z27UubvWFPbm0gLWQJVzCTE9UkP3pSx1aXxUjq3n8b2JRLk4OxVXrFgnryzZvcDbKTvyz36ono600tLhxSdnoOe50zSgxiIQhD36sIPLln7lNOMrUi1ralV_hOfZItjuwqeTOTFgXRG_rhkIzBfKmudQHD1KUodPD84a308LAfJ5ciLak9nlNVyVOL7gViWADpdZv_KrBXgaJXkxKZ4qxeYT_wf6yajHP2Gt4LPijuhqJIsqId7Xo8FkNIsmlvZkdArZDLgpZdunDmnis_623fu4vMeuWyVhrAoesilIqbwP_bKWNhGO_fcQ1Spsa-TDgfqUyrXnk3UYc-B3m6Npvkx3bYv3NrUSNxqdMONxR-3HowU3Uke-jM3Z8GR25HE4YAdfTqVxHtd6DEP9aamMIRH0LwuM4uxUrgeALqpbPenabekOZkkZ5-KKY4AyJKMOWxvMmqJRz9gYHnGUxqKcl2-F7250rHNGZTbJPurie_3WzNrRKFOQAF84mbsGoeYvSXlbI5uiH3Bw9kpOw302r26K4j-IKoMpw2BXU0mNxoGEL_wC0oTkVqRNg_sTTcsAPU1giW0hj-LONWc0ZgcKNI00fXaC5l6V8i2ERWyBy4Ys8gKIc7mynZnCpf2tgrxMBH5sloZ1Lf6P63CiAA&client_secret=JqQX2PNo9bpM0uEihUPzyrh&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F&grant_type=authorization_code' 'https://login.microsoftonline.com/common/oauth2/v2.0/token'
```


| 매개 변수 | | 설명 |
| ----------------------- | ------------------------------- | --------------------- |
| tenant | 필수 | 요청의 경로에 있는 `{tenant}` 값을 사용하여 응용 프로그램에 로그인할 수 있는 사용자를 제어할 수 있습니다. 허용되는 값은 `common`, `organizations`, `consumers` 및 테넌트 ID입니다. 자세한 내용은 [프로토콜 기본](active-directory-v2-protocols.md#endpoints)을 참조하세요. |
| client\_id | 필수 | 등록 포털([apps.dev.microsoft.com](https://apps.dev.microsoft.com))에서 앱에 할당한 응용 프로그램 ID입니다. |
| grant\_type | 필수 | 인증 코드 흐름에 대한 `authorization_code`여야 합니다. |
| scope | 필수 | 공백으로 구분된 범위 목록입니다. 이 레그에서 요청된 범위가 첫 번째 레그에서 요청된 범위와 동일하거나 하위 집합이어야 합니다. 이 요청에 지정된 범위가 여러 리소스 서버에 걸쳐 있는 경우 v2.0 끝점은 첫 번째 범위에 지정된 리소스에 대한 토큰을 반환합니다. 범위에 대한 자세한 설명은 [사용 권한, 동의 및 범위](active-directory-v2-scopes.md)를 참조하세요. |
| 코드 | 필수 | 흐름의 첫 번째 레그에서 얻은 authorization\_code입니다. |
| redirect\_uri | 필수 | authorization\_code를 획득하는 데 사용된 값과 동일한 redirect\_uri 값입니다. |
| client\_secret | 웹앱에 필요 | 앱에 대한 앱 등록 포털에서 만든 응용 프로그램 암호입니다. 장치에 client\_secret을 안정적으로 저장할 수 없으므로 네이티브 앱에서는 사용하면 안 됩니다. 서버 쪽에서 client\_secret을 안전하게 저장할 수 있는 웹앱과 Web API에 필요합니다. |

#### 성공적인 응답
성공적인 토큰 응답은 다음과 같습니다.

```
{
	"access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...",
	"token_type": "Bearer",
	"expires_in": 3599,
	"scope": "https%3A%2F%2Fgraph.microsoft.com%2Fmail.read",
	"refresh_token": "AwABAAAAvPM1KaPlrEqdFSBzjqfTGAMxZGUTdM0t4B4...",
	"id_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJub25lIn0.eyJhdWQiOiIyZDRkMTFhMi1mODE0LTQ2YTctOD...",
}
```
| 매개 변수 | 설명 |
| ----------------------- | ------------------------------- |
| access\_token | 요청된 액세스 토큰입니다. 앱은 이 토큰을 사용하여 Web API와 같은 보안 리소스에 인증할 수 있습니다. |
| token\_type | 토큰 유형 값을 나타냅니다. Azure AD는 전달자 유형만 지원합니다. |
| expires\_in | 액세스 토큰이 유효한 기간(초)입니다. |
| scope | access\_token이 유효한 범위입니다. |
| refresh\_token | OAuth 2.0 새로 고침 토큰입니다. 앱은 현재 액세스 토큰이 만료된 후 이 토큰을 사용하여 추가 액세스 토큰을 획득할 수 있습니다. refresh\_token은 수명이 길며, 오랜 시간 동안 리소스에 대한 액세스를 유지하는 데 사용할 수 있습니다. 자세한 내용은 [v2.0 토큰 참조](active-directory-v2-tokens.md)를 참조하세요. |
| id\_token | 서명되지 않은 JWT(JSON 웹 토큰)입니다. 앱은 이 토큰의 세그먼트를 base64Url로 디코드하여 로그인한 사용자에 대한 정보를 요청할 수 있습니다. 앱은 값을 캐시하고 표시할 수 있지만 권한 부여 또는 보안 경계에 대해 의존해서는 안 됩니다. id\_token에 대한 자세한 내용은 [v2.0 끝점 토큰 참조](active-directory-v2-tokens.md)를 참조하세요. |

#### 오류 응답
오류 응답은 다음과 같습니다.

```
{
  "error": "invalid_scope",
  "error_description": "AADSTS70011: The provided value for the input parameter 'scope' is not valid. The scope https://foo.microsoft.com/mail.read is not valid.\r\nTrace ID: 255d1aef-8c98-452f-ac51-23d051240864\r\nCorrelation ID: fb3d2015-bc17-4bb9-bb85-30c5cf1aaaa7\r\nTimestamp: 2016-01-09 02:02:12Z",
  "error_codes": [
    70011
  ],
  "timestamp": "2016-01-09 02:02:12Z",
  "trace_id": "255d1aef-8c98-452f-ac51-23d051240864",
  "correlation_id": "fb3d2015-bc17-4bb9-bb85-30c5cf1aaaa7"
}
```

| 매개 변수 | 설명 |
| ----------------------- | ------------------------------- |
| error | 발생하는 오류 유형을 분류하는 데 사용할 수 있고 오류에 대응하는 데 사용할 수 있는 오류 코드 문자열입니다. |
| error\_description | 개발자가 인증 오류의 근본 원인을 식별하도록 도울 수 있는 특정 오류 메시지입니다. |
| error\_codes | 진단에 도움이 될 수 있는 STS 특정 오류 코드의 목록입니다. |
| timestamp | 오류가 발생한 시간입니다. |
| trace\_id | 진단에 도움이 될 수 있는 요청에 대한 고유 식별자입니다. |
| correlation\_id | 여러 구성 요소에서 진단에 도움이 될 수 있는 요청에 대한 고유 식별자입니다. |

## 액세스 토큰 사용
`access_token`을 성공적으로 획득했으므로 이제 `Authorization` 헤더에 포함하여 Web API에 대한 요청에 토큰을 사용할 수 있습니다.

```
GET /v1.0/me/messages
Host: https://graph.microsoft.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
```

> [AZURE.TIP] 아래 명령을 사용해 보세요!(하지만 토큰을 사용자 고유의 것으로 교체합니다.)

```
curl -X GET -H "Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q" 'https://graph.microsoft.com/v1.0/me/messages'
```

## 액세스 토큰 새로 고침
access\_token은 수명이 짧으며, 만료되면 새로 고쳐야 리소스에 계속 액세스할 수 있습니다. 이렇게 하려면 다른 `POST` 요청을 `/token` 끝점에 제출해야 하며, 이번에는 `code` 대신 `refresh_token`을 제공해야 합니다.

```
// Line breaks for legibility only

POST /{tenant}/oauth2/v2.0/token HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&scope=https%3A%2F%2Fgraph.microsoft.com%2Fmail.read
&refresh_token=OAAABAAAAiL9Kn2Z27UubvWFPbm0gLWQJVzCTE9UkP3pSx1aXxUjq...
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&grant_type=refresh_token
&client_secret=JqQX2PNo9bpM0uEihUPzyrh	  // NOTE: Only required for web apps
```

> [AZURE.TIP] 아래 curl 명령을 postman으로 가져옵니다!(성공하려면 refresh\_token을 사용자 고유의 것으로 바꿔야 합니다.)

```
curl -X POST -H "Cache-Control: no-cache" -H "Content-Type: application/x-www-form-urlencoded" -d 'client_id=6731de76-14a6-49ae-97bc-6eba6914391e&scope=https%3A%2F%2Fgraph.microsoft.com%2Fmail.read&refresh_token=OAAABAAAAiL9Kn2Z27UubvWFPbm0gLWQJVzCTE9UkP3pSx1aXxUjq3n8b2JRLk4OxVXrFgnryzZvcDbKTvyz36ono600tLhxSdnoOe50zSgxiIQhD36sIPLln7lNOMrUi1ralV_hOfZItjuwqeTOTFgXRG_rhkIzBfKmudQHD1KUodPD84a308LAfJ5ciLak9nlNVyVOL7gViWADpdZv_KrBXgaJXkxKZ4qxeYT_wf6yajHP2Gt4LPijuhqJIsqId7Xo8FkNIsmlvZkdArZDLgpZdunDmnis_623fu4vMeuWyVhrAoesilIqbwP_bKWNhGO_fcQ1Spsa-TDgfqUyrXnk3UYc-B3m6Npvkx3bYv3NrUSNxqdMONxR-3HowU3Uke-jM3Z8GR25HE4YAdfTqVxHtd6DEP9aamMIRH0LwuM4uxUrgeALqpbPenabekOZkkZ5-KKY4AyJKMOWxvMmqJRz9gYHnGUxqKcl2-F7250rHNGZTbJPurie_3WzNrRKFOQAF84mbsGoeYvSXlbI5uiH3Bw9kpOw302r26K4j-IKoMpw2BXU0mNxoGEL_wC0oTkVqRNg_sTTcsAPU1giW0hj-LONWc0ZgcKNI00fXaC5l6V8i2ERWyBy4Ys8gKIc7mynZnCpf2tgrxMBH5sloZ1Lf6P63CiAA&client_secret=JqQX2PNo9bpM0uEihUPzyrh&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F&grant_type=refresh_token' 'https://login.microsoftonline.com/common/oauth2/v2.0/token'
```

| 매개 변수 | | 설명 |
| ----------------------- | ------------------------------- | -------- |
| tenant | 필수 | 요청의 경로에 있는 `{tenant}` 값을 사용하여 응용 프로그램에 로그인할 수 있는 사용자를 제어할 수 있습니다. 허용되는 값은 `common`, `organizations`, `consumers` 및 테넌트 ID입니다. 자세한 내용은 [프로토콜 기본](active-directory-v2-protocols.md#endpoints)을 참조하세요. |
| client\_id | 필수 | 등록 포털([apps.dev.microsoft.com](https://apps.dev.microsoft.com))에서 앱에 할당한 응용 프로그램 ID입니다. |
| grant\_type | 필수 | 이 인증 코드 흐름 범례에 대한 `refresh_token`이어야 합니다. |
| scope | 필수 | 공백으로 구분된 범위 목록입니다. 이 레그에서 요청된 범위가 원래 authorization\_code 요청 레그에서 요청된 범위와 동일하거나 하위 집합이어야 합니다. 이 요청에 지정된 범위가 여러 리소스 서버에 걸쳐 있는 경우 v2.0 끝점은 첫 번째 범위에 지정된 리소스에 대한 토큰을 반환합니다. 범위에 대한 자세한 설명은 [사용 권한, 동의 및 범위](active-directory-v2-scopes.md)를 참조하세요. |
| refresh\_token | 필수 | 흐름의 두 번째 레그에서 얻은 refresh\_token입니다. |
| redirect\_uri | 필수 | authorization\_code를 획득하는 데 사용된 값과 동일한 redirect\_uri 값입니다. |
| client\_secret | 웹앱에 필요 | 앱에 대한 앱 등록 포털에서 만든 응용 프로그램 암호입니다. 장치에 client\_secret을 안정적으로 저장할 수 없으므로 네이티브 앱에서는 사용하면 안 됩니다. 서버 쪽에서 client\_secret을 안전하게 저장할 수 있는 웹앱과 Web API에 필요합니다. |

#### 성공적인 응답
성공적인 토큰 응답은 다음과 같습니다.

```
{
	"access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...",
	"token_type": "Bearer",
	"expires_in": 3599,
	"scope": "https%3A%2F%2Fgraph.microsoft.com%2Fmail.read",
	"refresh_token": "AwABAAAAvPM1KaPlrEqdFSBzjqfTGAMxZGUTdM0t4B4...",
	"id_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJub25lIn0.eyJhdWQiOiIyZDRkMTFhMi1mODE0LTQ2YTctOD...",
}
```
| 매개 변수 | 설명 |
| ----------------------- | ------------------------------- |
| access\_token | 요청된 액세스 토큰입니다. 앱은 이 토큰을 사용하여 Web API와 같은 보안 리소스에 인증할 수 있습니다. |
| token\_type | 토큰 유형 값을 나타냅니다. Azure AD는 전달자 유형만 지원합니다. |
| expires\_in | 액세스 토큰이 유효한 기간(초)입니다. |
| scope | access\_token이 유효한 범위입니다. |
| refresh\_token | 새 OAuth 2.0 새로 고침 토큰입니다. 이전 새로 고침 토큰을 새로 얻은 새로 고침 토큰으로 대체하여 새로 고침 토큰을 최대한 오랫동안 유효한 상태로 유지해야 합니다. |
| id\_token | 서명되지 않은 JWT(JSON 웹 토큰)입니다. 앱은 이 토큰의 세그먼트를 base64Url로 디코드하여 로그인한 사용자에 대한 정보를 요청할 수 있습니다. 앱은 값을 캐시하고 표시할 수 있지만 권한 부여 또는 보안 경계에 대해 의존해서는 안 됩니다. id\_token에 대한 자세한 내용은 [v2.0 끝점 토큰 참조](active-directory-v2-tokens.md)를 참조하세요. |

#### 오류 응답
```
{
  "error": "invalid_scope",
  "error_description": "AADSTS70011: The provided value for the input parameter 'scope' is not valid. The scope https://foo.microsoft.com/mail.read is not valid.\r\nTrace ID: 255d1aef-8c98-452f-ac51-23d051240864\r\nCorrelation ID: fb3d2015-bc17-4bb9-bb85-30c5cf1aaaa7\r\nTimestamp: 2016-01-09 02:02:12Z",
  "error_codes": [
    70011
  ],
  "timestamp": "2016-01-09 02:02:12Z",
  "trace_id": "255d1aef-8c98-452f-ac51-23d051240864",
  "correlation_id": "fb3d2015-bc17-4bb9-bb85-30c5cf1aaaa7"
}
```

| 매개 변수 | 설명 |
| ----------------------- | ------------------------------- |
| error | 발생하는 오류 유형을 분류하는 데 사용할 수 있고 오류에 대응하는 데 사용할 수 있는 오류 코드 문자열입니다. |
| error\_description | 개발자가 인증 오류의 근본 원인을 식별하도록 도울 수 있는 특정 오류 메시지입니다. |
| error\_codes | 진단에 도움이 될 수 있는 STS 특정 오류 코드의 목록입니다. |
| timestamp | 오류가 발생한 시간입니다. |
| trace\_id | 진단에 도움이 될 수 있는 요청에 대한 고유 식별자입니다. |
| correlation\_id | 여러 구성 요소에서 진단에 도움이 될 수 있는 요청에 대한 고유 식별자입니다. |

<!---HONumber=AcomDC_0224_2016-->