<properties
	pageTitle="Azure AD B2C 미리 보기 | Microsoft Azure"
	description="Azure AD B2C 미리 보기에서 발급된 토큰의 형식입니다."
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
	ms.date="01/21/2016"
	ms.author="dastrock"/>

# Azure AD B2C 미리 보기: 토큰 참조

Azure AD B2C는 각 [인증 흐름](active-directory-b2c-apps.md)의 처리 과정에서 여러 유형의 보안 토큰을 내보냅니다. 이 문서에서는 각 토큰 유형의 형식, 보안 특성 및 내용을 설명합니다.

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]

## 토큰 유형

Azure AD B2C는 access\_token 및 refresh\_token 둘 다를 활용하는 [OAuth 2.0 인증 프로토콜](active-directory-b2c-reference-protocols.md)을 지원합니다. 또한 세 번째 토큰 유형인 id\_token을 도입하는 [OpenID Connect](active-directory-b2c-reference-protocols.md)를 통해 인증 및 로그인을 지원합니다. 이러한 토큰은 각각 "전달자 토큰"으로 표시됩니다.

전달자 토큰은 보호된 리소스에 대한 "전달자" 액세스 권한을 부여하는 간단한 보안 토큰입니다. 이런 의미에서, "전달자"는 토큰을 제공할 수 있는 당사자입니다. 이 당사자는 전달자 토큰을 수신하려면 먼저 Azure AD를 사용하여 인증해야 합니다. 하지만 전송 및 저장 시 토큰 보안을 유지하는 데 필요한 단계를 취하지 않는 경우 의도하지 않은 당사자가 토큰을 가로채서 사용할 수 있습니다. 일부 보안 토큰에는 권한 없는 당사자가 사용하는 것을 방지하는 기본 제공 메커니즘이 있지만, 전달자 토큰에는 이러한 메커니즘이 없으므로 전송 계층 보안(HTTPS)과 같은 보안 채널에서 전달자 토큰을 전송해야 합니다. 전달자 토큰이 일반 텍스트 상태로 전송되는 경우 악의적인 사용자가 메시지 가로채기(man-in-the-middle) 공격을 사용해서 토큰을 획득하고 보호된 리소스에 무단으로 액세스하는 데 이 토큰을 사용할 수 있습니다. 나중에 사용하기 위해 전달자 토큰을 저장하거나 캐싱할 때도 동일한 보안 원칙이 적용됩니다. 항상 앱이 안전한 방식으로 전달자 토큰을 전송하고 저장하도록 합니다. 전달자 토큰의 보안 고려 사항을 자세히 알아보려면 [RFC 6750 Section 5](http://tools.ietf.org/html/rfc6750)를 참조하세요.

Azure AD B2C에서 발급된 토큰은 대부분 Json 웹 토큰, 즉 JWT로 구현됩니다. JWT는 두 요소 간에 정보를 전송하는 URL로부터 안전한 간단한 수단입니다. JWT에 포함된 정보를 "클레임" 또는 토큰의 전달자 및 주체에 대한 정보 어설션이라고 합니다. JWT의 클레임은 전송을 위해 인코드 및 직렬화된 JSON 개체입니다. Azure AD B2C에서 발급된 JWT가 서명되었지만 암호화되지 않았으므로 디버깅을 위해 JWT의 내용을 쉽게 검사할 수 있습니다. [calebb.net](https://calebb.net) 등 이러한 작업에 사용할 수 있는 여러 도구가 있습니다. JWT에 대한 자세한 내용은 [JWT 사양](http://self-issued.info/docs/draft-ietf-oauth-json-web-token.html)을 참조하세요.

## id\_token

Id\_token은 Azure AD B2C `authorize` 및 `token` 끝점에서 앱이 수신하는 보안 토큰의 형태입니다. [JWT](#types-of-tokens)로 표시되며 사용자를 앱에 식별하는 데 사용할 수 있는 클레임을 포함합니다. `authorize` 끝점에서 얻은 경우 id\_tokens은 웹 응용 프로그램에 사용자를 로그인시키는 데 자주 사용됩니다. `token` 끝점에서 얻은 경우 id\_tokens은 동일한 응용 프로그램 또는 서비스의 두 구성 요소 간의 통신할 때 HTTP 요청에서 전송될 수 있습니다. id\_token의 클레임을 적절하게 사용할 수 있습니다. 일반적으로 계정 정보를 표시하거나 앱에서 액세스 제어 결정을 내리는 데 사용됩니다.

지금은 Id\_token이 서명되었지만 암호화되지 않았습니다. 앱 또는 API가 id\_token을 받으면 [서명의 유효성을 검사](#validating-tokens)하여 토큰의 신뢰성을 입증하고 토큰에 있는 몇 개 클레임의 유효성을 검사하여 유효성을 입증해야 합니다. 앱에서 유효성을 검사하는 클레임은 시나리오 요구 사항에 따라 다르지만 앱이 모든 시나리오에서 수행해야 하는 몇 가지 [일반적인 클레임 유효성 검사](#validating-tokens)가 있습니다.

id\_token의 클레임에 대한 자세한 내용 및 샘플 id\_token은 아래에 나와 있습니다. id\_token의 클레임은 특정 순서로 반환되지 않습니다. 또한 언제든지 id\_token에 새 클레임이 도입될 수 있으므로 새 클레임이 도입될 때 앱이 손상되지 않아야 합니다. 아래 목록에는 이 문서가 작성될 때 앱이 안정적으로 해석할 수 있는 클레임이 포함되어 있습니다. 연습을 위해 샘플 id\_token에 있는 클레임을 [calebb.net](https://calebb.net)에 붙여넣어 검사하세요. 필요한 경우 [OpenID Connect 사양](http://openid.net/specs/openid-connect-core-1_0.html)에서 자세한 내용을 확인할 수 있습니다.

#### 샘플 id\_token
```
// Line breaks for display purposes only

eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6IklkVG9rZW5TaWduaW5nS2V5Q29udGFpbmVyIn0.
eyJleHAiOjE0NDIzNjAwMzQsIm5iZiI6MTQ0MjM1NjQzNCwidmVyIjoiMS4wIiwiaXNzIjoiaHR0cHM6Ly9s
b2dpbi5taWNyb3NvZnRvbmxpbmUuY29tLzc3NTUyN2ZmLTlhMzctNDMwNy04YjNkLWNjMzExZjU4ZDkyNS92
Mi4wLyIsImFjciI6ImIyY18xX3NpZ25faW5fc3RvY2siLCJzdWIiOiJOb3Qgc3VwcG9ydGVkIGN1cnJlbnRs
eS4gVXNlIG9pZCBjbGFpbS4iLCJhdWQiOiI5MGMwZmU2My1iY2YyLTQ0ZDUtOGZiNy1iOGJiYzBiMjlkYzYi
LCJpYXQiOjE0NDIzNTY0MzQsImF1dGhfdGltZSI6MTQ0MjM1NjQzNCwiaWRwIjoiZmFjZWJvb2suY29tIn0.
h-uiKcrT882pSKUtWCpj-_3b3vPs3bOWsESAhPMrL-iIIacKc6_uZrWxaWvIYkLra5czBcGKWrYwrAC8ZvQe
DJWZ50WXQrZYODEW1OUwzaD_I1f1HE0c2uvaWdGXBpDEVdsD3ExKaFlKGjFR2V7F-fPThkVDdKmkUDQX3bVc
yyj2V2nlCQ9jd7aGnokTPfLfpOjuIrTsAdPcGpe5hfSEuwYDmqOJjGs9Jp1f-eSNEiCDQOaTBSvr479L5ptP
XWeQZyX2SypN05Rjr05bjZh3j70ZUimiocfJzjibeoDCaQTz907yAg91WYuFOrQxb-5BaUoR7K-O7vxr2M-_
CQhoFA

```

#### id\_token의 클레임

Azure AD B2C로 프로그램 토큰의 내용에 대해 정교하게 세분화된 조정을 해야합니다. [정책](active-directory-b2c-reference-policies.md)을 구성하여 해당 작업에 대해 앱이 필요한 클레임에서 사용자 데이터의 특정 집합을 전송할 수 있습니다. 이러한 클레임은 사용자의 `displayName` 및 `emailAddress`, 뿐만 아니라 B2C 디렉터리에 정의할 수 있는 [사용자 지정 특성](active-directory-b2c-reference-custom-attr)과 같은 표준 속성을 포함할 수 있습니다. 그러나 받은 모든 id\_token은 응용 프로그램이 안전하게 사용자 및 요청을 인증하는 데 사용할 수 있는 특정 보안 관련 클레임 집합을 포함합니다. 다음은 Azure AD B2C에서 발급한 모든 id\_token에 존재하는 클레임입니다. 다른 클레임은 정책에 의해 결정됩니다.

| 이름 | 클레임 | 예제 값 | 설명 |
| ----------------------- | ------------------------------- | ------------ | --------------------------------- |
| 대상 | `aud` | `90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6` | 토큰의 의도한 수신자를 식별합니다. Id\_token에서 대상은 앱 등록 포털에서 앱에 할당된 앱의 응용 프로그램 ID입니다. 앱은 이 값의 유효성을 검사하고 일치하지 않을 경우 토큰을 거부해야 합니다. |
| 발급자 | `iss` | `https://login.microsoftonline.com/775527ff-9a37-4307-8b3d-cc311f58d925/v2.0/` | 사용자가 인증된 Azure AD 디렉터리뿐 아니라 토큰을 생성하고 반환하는 STS(보안 토큰 서비스)를 식별합니다. 앱은 발급자 클레임의 유효성을 검사하여 토큰이 v2.0 끝점에서 제공된 것인지 확인해야 합니다. |
| 발급 시간 | `iat` | `1438535543` | epoch 시간으로 표시된, 토큰이 발급된 시간입니다. |
| 만료 시간 | `exp` | `1438539443` | epoch 시간으로 표시된, 토큰이 무효화되는 시간입니다. 앱은 이 클레임을 사용하여 토큰 수명의 유효성을 확인해야 합니다. |
| 이전이 아님 | `nbf` | `1438535543` | epoch 시간으로 표시된, 토큰이 유효화되는 시간입니다. 발급 시간과 일반적으로 동일합니다. 앱은 이 클레임을 사용하여 토큰 수명의 유효성을 확인해야 합니다. |
| 버전 | `ver` | `1.0` | Azure AD에서 정의된 id\_token 버전입니다. |
| 코드 해시 | `c_hash` | `SGCPtt01wxwfgnYZy2VJtQ` | 코드 해시는 OAuth 2.0 인증 코드와 함께 id\_token이 발급된 경우에만 id\_token에 포함됩니다. 인증 코드의 신뢰성이 유효한지 검사하는 데 사용할 수 있습니다. 이 유효성 검사를 수행하는 방법에 대한 자세한 내용은 [OpenID Connect 사양](http://openid.net/specs/openid-connect-core-1_0.html)을 참조하세요. |
| 액세스 토큰 해시 | `at_hash` | `SGCPtt01wxwfgnYZy2VJtQ` | 액세스 토큰 해시는 OAuth 2.0 액세스 토큰과 함께 id\_token이 발급된 경우에만 id\_token에 포함됩니다. 액세스 토큰의 신뢰성이 유효한지 검사하는 데 사용할 수 있습니다. 이 유효성 검사를 수행하는 방법에 대한 자세한 내용은 [OpenID Connect 사양](http://openid.net/specs/openid-connect-core-1_0.html)을 참조하세요. |
| nonce | `nonce` | `12345` | nonce는 토큰 재생 공격을 완화하기 위한 전략입니다. 앱은 `nonce` 쿼리 매개 변수를 사용하여 권한 부여 요청에 nonce를 지정할 수 있습니다. 요청에 제공한 값은 수정되지 않고 id\_token의 `nonce` 클레임에 내보내집니다. 따라서 앱이 지정된 id\_token과 앱 세션을 연결하는 요청에 지정된 값과 비교하여 값을 확인할 수 있습니다. 앱은 id\_token 유효성 검사 프로세스 중에 이 유효성 검사를 수행해야 합니다. |
| 제목  
 | `sub` | `Not supported currently. Use oid claim.` | 앱 사용자 등 토큰에서 정보를 어설션하는 보안 주체입니다. 이 값은 변경할 수 없으며 재할당 또는 재사용할 수 없습니다. 따라서 예를 들어 리소스 액세스에 토큰을 사용할 때 이 값을 사용하면 안전하게 인증 검사를 수행할 수 있습니다. 그러나 제목 클레임은 Azure AD B2C 미리 보기에서 아직 구현되지 않습니다. 권한 부여에 제목 클레임을 사용하는 대신 정책을 구성하여 개체 ID `oid` 클레임을 포함하는 정책을 구성하고 사용자를 식별하는 해당 값을 사용해야 합니다. |
| 인증 컨텍스트 클래스 참조 | `acr` | `b2c_1_sign_in` | id\_token를 얻는 데 사용된 정책의 이름입니다. |
| 인증 시간 | `auth_time | `1438535543` | epoch 시간에서 사용자가 마지막에 자격 증명을 입력한 시간입니다. |



## 액세스 토큰

Azure AD B2C은 지금 액세스 토큰을 발급하지 않습니다. 이 초기 미리 보기 단계에서 서로 다른 두 당사자 간에 인증을 할 수 없습니다. 그러나 id\_tokens을 사용하여 동일한 응용 프로그램의 구성 요소 간에 통신할 수 있습니다. Azure AD B2C 공용 미리 보기에서 지원하는 응용 프로그램 및 인증 시나리오에 대해 알아보려면 [B2C 앱 문서](active-directory-b2c-apps.md)를 참조하세요.

## 새로 고침 토큰

새로 고침 토큰은 앱이 OAuth 2.0 흐름에서 새 id\_tokens 및 access\_tokens을 획득하는 데 사용할 수 있는 보안 토큰입니다. 새로 고침 토큰을 통해 앱은 사용자 조작을 요구하지 않고 사용자 대신 리소스에 장기적으로 액세스할 수 있습니다.

토큰 응답에서 새로 고침을 받으려면 앱이 `offline_acesss` 범위를 요청해야 합니다. `offline_access` 범위에 대한 자세한 내용은 [Azure AD B2C 프로토콜 참조](active-directory-b2c-reference-protocols.md)를 참조하세요.

새로 고침 토큰은 앱에 완전히 불투명하며 항상 그럴 것입니다. 새로 고침 토큰은 Azure AD에서 발급되며 Azure AD에서만 검사되고 해석됩니다. 또한 새로 고침 토큰은 수명이 길지만 일정 기간 지속될 것으로 예상하도록 앱을 작성해서는 안 됩니다. 다양한 이유로 언제든지 새로 고침 토큰이 무효화될 수 있기 때문입니다. 앱에서 새로 고침 토큰이 유효한지 확인하는 유일한 방법은 Azure AD에 대한 토큰 요청을 수행하여 교환하는 것입니다.

새로 고침 토큰을 새 토큰으로 교환할 때 앱에 `offline_access` 범위가 부여된 경우 토큰 응답에 새로운 새로 고침 토큰을 받게 됩니다. 새로 발급된 새로 고침 토큰을 저장하고 이전에 요청에서 사용한 토큰을 대체해야 합니다. 이렇게 하면 새로 고침 토큰이 최대한 오랫동안 유효한 상태로 유지됩니다.

## 토큰 유효성 검사

이 시점에서 앱이 수행해야 하는 토큰 유효성 검사는 id\_token 유효성 검사뿐입니다. id\_token의 유효성을 검사하려면 앱이 id\_token의 서명과 id\_token에 있는 클레임의 유효성을 모두 검사해야 합니다.

<!-- TODO: Link -->
기본 설정의 언어에 따라 JWT의 유효성 검사에 사용할 수 있는 다양한 공개 소스 라이브러리가 있습니다. 고유한 유효성 검사 논리 보다는 이러한 옵션을 탐색하는 것이 좋습니다. 여기의 정보는 올바르게 해당 라이브러리를 사용하는 방법을 파악하는 데 도움이 됩니다.

#### 서명 유효성 검사
JWT에는 `.` 문자로 구분된 세 개의 세그먼트가 포함되어 있습니다. 첫 번째 세그먼트는 **헤더**, 두 번째 세그먼트는 **본문**, 세 번째 세그먼트는 **서명**이라고 합니다. 서명 세그먼트는 앱이 신뢰할 수 있도록 id\_token의 신뢰성이 유효한지 검사하는 데 사용할 수 있습니다.

id\_token은 RSA 256 등의 업계 표준 비대칭 암호화 알고리즘을 사용하여 서명됩니다. id\_token의 헤더에는 토큰 서명에 사용된 키 및 암호화 방법에 대한 정보가 들어 있습니다.

```
{
		"typ": "JWT",
		"alg": "RS256",
		"kid": "GvnPApfWMdLRi8PDmisFn7bprKg"
}
```

`alg` 클레임은 토큰 서명에 사용된 알고리즘을 나타내고, `kid` 또는 `x5t` 클레임은 토큰 서명에 사용된 특정 공개 키를 나타냅니다.

특정 시점에 Azure AD는 공개-개인 키 쌍의 특정 집합 중 하나를 사용하여 id\_token에 서명할 수 있습니다. Azure AD는 주기적으로 가능한 키 집합을 순환하므로 이러한 키 변경을 자동으로 처리하도록 앱을 작성해야 합니다. Azure AD에서 사용된 공개 키에 대한 업데이트를 확인하는 적절한 빈도는 약 24시간입니다.

Azure AD B2C에는 앱이 런타임에 Azure AD B2C에 대한 정보를 가져올 수 있게 해주는 OpenID Connect 메타데이터 끝점이 있습니다. 이 정보에는 끝점, 토큰 콘텐츠 및 토큰 서명 키가 포함됩니다. B2C 디렉터리에서 각 정책에 대한 JSON 메타데이터 문서가 있습니다. 예를 들어 `fabrikamb2c.onmicrosoft.com`의 `b2c_1_sign_in` 정책에 대한 메타데이터 문서는 다음에서 찾을 수 있습니다.

`https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/v2.0/.well-known/openid-configuration?p=b2c_1_sign_in`

다음 위치에 있는 OpenID Connect 메타데이터 문서를 사용하여 서명 유효성 검사에 필요한 서명 키 데이터를 얻을 수 있습니다.

```
https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/v2.0/.well-known/openid-configuration?p=b2c_1_sign_in
```

`fabrikamb2c.onmicrosoft.com`은 사용자를 인증하는 데 사용되는 b2c 디렉터리이고 `b2c_1_sign_in`는 id\_token를 가져오는 데 사용되는 정책입니다. id\_token을 서명하는 데 어떤 정책을 사용할지(그리고 메타데이터를 인출하는 위치)를 결정하기 위해 두 가지 옵션이 있습니다. 먼저 정책 이름은 id\_token의 `acr` 클레임에 포함됩니다. base-64로 본문을 디코딩하고 결과 JSON 문자열을 역직렬화하여 JWT의 본문에서 클레임을 구문 분석할 수 있습니다. `acr` 클레임은 id\_token을 발급하는 데 사용된 정책의 이름입니다. 다른 옵션은 요청을 실행할 때 `state` 매개 변수의 값에 정책을 인코딩한 다음 이를 디코딩하여 어떤 정책을 사용할지 결정하는 것입니다. 두 방법 모두 완벽하게 유효합니다.

이 메타데이터 문서는 OpenID Connect 인증을 수행하는 데 필요한 다양한 끝점의 위치 등 여러 유용한 정보를 포함하는 JSON 개체입니다. 토큰 서명에 사용되는 공개 키 집합의 위치를 제공하는 `jwks_uri`도 포함합니다. 해당 위치는 아래에 제공되어 있지만 메타데이터 문서를 사용하고 `jwks_uri`를 구문 분석하여 위치를 동적으로 가져오는 것이 가장 좋습니다.

`https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/discovery/v2.0/keys?p=b2c_1_sign_in`

이 url에 있는 JSON 문서는 해당 특정 시점에 사용 중인 공개 키 정보를 모두 포함합니다. 앱은 JWT 헤더에 `kid` 또는 `x5t` 클레임을 사용하여 토큰 서명에 사용된 공개 키를 이 문서에서 선택할 수 있습니다. 그런 다음 올바른 공개 키와 표시된 알고리즘을 사용하여 서명 유효성 검사를 수행할 수 있습니다.

서명 유효성 검사는 이 문서의 범위를 벗어납니다. 필요한 경우 이 작업에 도움이 되는 다양한 오픈 소스 라이브러리가 있습니다.

#### 클레임 유효성 검사
앱 또는 API가 id\_token을 받은 경우 id\_token의 클레임에 대해서도 몇 가지 검사를 수행해야 합니다. 내용은 다음과 같습니다.

- **대상** 클레임 - id\_token이 앱에 제공된 것이 맞는지 확인합니다.
- **이전이 아님** 및 **만료 시간** 클레임 - id\_token이 만료되지 않았는지 확인합니다.
- **발급자** 클레임 - Azure AD에서 실제로 앱에 토큰을 발급했는지 확인합니다.
- **nonce** - 토큰 재생 공격 완화로 사용됩니다.

이러한 클레임의 예상 값에 대한 자세한 내용은 위의 [id\_token 섹션](#id_tokens)에 포함되어 있습니다.

## 토큰 수명

다음 토큰 수명은 앱 개발 및 디버그에 유용할 수 있으므로 순수하게 이해를 돕기 위해 제공되었습니다. 이러한 수명이 일정하게 유지된다고 예상하도록 앱을 작성해서는 안 됩니다. 언제든지 변경될 수 있고 변경됩니다.

| 신뢰 | 수명 | 설명 |
| ----------------------- | ------------------------------- | ------------ |
| id\_token | 1시간 | id\_token은 일반적으로 1시간 동안 유효합니다. 웹앱은 이 동일한 수명을 사용하여 사용자와의 자체 세션을 유지 관리하거나 완전히 다른 세션 수명을 선택할 수 있습니다. 앱이 새 id\_token을 가져와야 하는 경우 새 로그인 요청 Azure AD를 만들면 됩니다. 사용자에게 Azure AD와 유효한 브라우저 세션이 있는 경우 자격 증명을 다시 입력할 필요가 없을 수도 있습니다. |
| 새로 고침 토큰 | 최대 14일 | 단일 새로 고침 토큰이 최대 14일 동안 유효합니다. 그러나 새로 고침 토큰은 다양한 이유로 언제든지 무효화될 수 있으므로 실패하거나 앱이 새로운 새로 고침 토큰으로 대체할 때까지 앱에서 계속 새로 고침 토큰을 시도하고 사용해야 합니다. 사용자가 자격 증명을 입력한 후 90일이 경과한 경우에도 새로 고침 토큰이 무효화됩니다. |
| 권한 부여 코드 | 5분 | 인증 코드는 의도적으로 수명이 짧으므로 받는 즉시 access\_token, id\_tokens 및 refresh\_token으로 교환해야 합니다. |

<!---HONumber=AcomDC_0128_2016-->