---
title: OAuth 2.0 암시적 권한 부여 흐름-Microsoft id 플랫폼 | Microsoft
description: Microsoft id 플랫폼 암시적 흐름을 사용 하 여 단일 페이지 앱을 보호 합니다.
services: active-directory
author: hpsin
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 11/30/2020
ms.author: hirsin
ms.reviewer: hirsin
ms.custom: aaddev
ms.openlocfilehash: f3598c6f072d09d7e427db66dcfbf8721b92a3a1
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2021
ms.locfileid: "99226491"
---
# <a name="microsoft-identity-platform-and-implicit-grant-flow"></a>Microsoft id 플랫폼 및 암시적 허용 흐름

Microsoft id 플랫폼은 [oauth 2.0 사양](https://tools.ietf.org/html/rfc6749#section-4.2)에 설명 된 대로 Oauth 2.0 암시적 권한 부여 흐름을 지원 합니다. 암시적 권한 부여의 특성을 정의 하는 것은 토큰 (ID 토큰 또는 액세스 토큰)이/ttoken 끝점 대신/srhi 끝점에서 직접 반환 된다는 것입니다. 이는 인증 코드와 함께/crrequest 요청에서 ID 토큰을 검색 하는 "하이브리드 흐름" 이라는 [인증 코드 흐름](v2-oauth2-auth-code-flow.md)의 일부로 사용 되는 경우가 많습니다.

[!INCLUDE [suggest-msal-from-protocols](includes/suggest-msal-from-protocols.md)]

## <a name="prefer-the-auth-code-flow"></a>인증 코드 흐름 선호

[브라우저에서 타사 쿠키](reference-third-party-cookies-spas.md)에 대 한 계획을 제거 하는 경우 **암시적 허용 흐름은 더 이상 적절 한 인증 방법이 아닙니다**.  암시적 흐름의 [자동 SSO 기능은](#getting-access-tokens-silently-in-the-background) 타사 쿠키를 사용 하지 않고 작동 하지 않으므로 응용 프로그램이 새 토큰을 가져오려고 할 때 중단 됩니다. 모든 새 응용 프로그램은 이제 암시적 흐름 대신 단일 페이지 앱을 지원 하 고 [기존의 단일 페이지 앱이 인증 코드 흐름으로 마이그레이션을 시작](migrate-spa-implicit-to-auth-code.md) 하는 [권한 부여 코드 흐름](v2-oauth2-auth-code-flow.md) 을 사용 하는 것이 좋습니다.

## <a name="suitable-scenarios-for-the-oauth2-implicit-grant"></a>OAuth2 암시적 허용에 적합한 시나리오

암시적 권한 부여는 [타사 쿠키가](reference-third-party-cookies-spas.md) 없어도 응용 프로그램에 영향을 줄 수 있는 로그인 흐름의 초기 대화형 부분에만 신뢰할 수 있습니다. 이러한 제한 사항은 응용 프로그램이 권한 부여 끝점에서 토큰 뿐만 아니라 코드를 요청 하는 하이브리드 흐름의 일부로 독점적으로 사용 해야 함을 의미 합니다. 이렇게 하면 응용 프로그램에서 새로 고침 토큰에 대해 사용할 수 있는 코드를 받을 수 있으므로 앱의 로그인 세션이 시간에 따라 유효 하 게 유지 됩니다.

## <a name="protocol-diagram"></a>프로토콜 다이어그램

다음 다이어그램에서는 전체적인 암시적 로그인 흐름을 보여 주며, 이어지는 섹션에서 각 단계에 대해 자세히 설명합니다.

![암시적 로그인 흐름을 보여 주는 다이어그램](./media/v2-oauth2-implicit-grant-flow/convergence-scenarios-implicit.svg)

## <a name="send-the-sign-in-request"></a>로그인 요청 보내기

처음에 사용자를 앱에 로그인 하기 위해 [Openid connect Connect](v2-protocols-oidc.md) 인증 요청을 보내고 `id_token` Microsoft id 플랫폼에서를 가져올 수 있습니다.

> [!IMPORTANT]
> ID 토큰 및/또는 액세스 토큰을 성공적으로 요청 하려면 **암시적 권한 부여 및 하이브리드 흐름** 섹션에서 **id 토큰** 및 **액세스 토큰** 을 선택 하 여 [Azure Portal 앱 등록](https://go.microsoft.com/fwlink/?linkid=2083908) 페이지의 앱 등록에서 해당 하는 암시적 권한 부여 흐름을 사용 하도록 설정 해야 합니다. 사용 하도록 설정 되지 않은 경우 `unsupported_response` 다음과 같은 오류가 반환 됩니다. `The provided value for the input parameter 'response_type' is not allowed for this client. Expected value is 'code'`

```
// Line breaks for legibility only

https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&response_type=id_token
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&scope=openid
&response_mode=fragment
&state=12345
&nonce=678910
```

> [!TIP]
> 암시적 흐름을 사용 하 여 로그인을 테스트 하려면을 클릭 <a href="https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&response_type=id_token&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F&scope=openid&response_mode=fragment&state=12345&nonce=678910" target="_blank"> https://login.microsoftonline.com/common/oauth2/v2.0/authorize.. 합니다.</a> 로그인 한 후 브라우저는 `https://localhost/myapp/` 주소 표시줄에서를 사용 하 여로 리디렉션해야 합니다 `id_token` .
>

| 매개 변수 | Type | Description |
| --- | --- | --- |
| `tenant` | required |요청의 경로에 있는 `{tenant}` 값을 사용하여 애플리케이션에 로그인할 수 있는 사용자를 제어할 수 있습니다. 허용되는 값은 `common`, `organizations`, `consumers` 및 테넌트 ID입니다. 자세한 내용은 [프로토콜 기본](active-directory-v2-protocols.md#endpoints)을 참조하세요. |
| `client_id` | required | [Azure Portal - 앱 등록](https://go.microsoft.com/fwlink/?linkid=2083908) 페이지가 앱에 할당한 애플리케이션(클라이언트) ID |
| `response_type` | 필수 |OpenID Connect 로그인을 위한 `id_token` 이 포함되어야 합니다. response_type `token`을 포함할 수도 있습니다. `token` 을 여기서 사용하면 앱은 권한 부여 엔드포인트에 두 번째 요청을 수행하지 않고 권한 부여 엔드포인트에서 즉시 액세스 토큰을 받을 수 있습니다. Response_type를 사용 하는 경우 `token` `scope` 매개 변수는 토큰을 발급할 리소스를 나타내는 범위를 포함 해야 합니다 (예: Microsoft Graph에서 읽기). 권한 부여 코드 `code` `token` [흐름](v2-oauth2-auth-code-flow.md)에 사용 하기 위해 대신를 포함 하 여 인증 코드를 제공할 수도 있습니다. 이 id_token + 코드 응답을 하이브리드 흐름이 라고도 합니다.  |
| `redirect_uri` | 권장 |앱이 인증 응답을 보내고 받을 수 있는 앱의 redirect_uri입니다. URL로 인코드되어야 한다는 점을 제외하고 포털에서 등록한 redirect_uri 중 하나와 정확히 일치해야 합니다. |
| `scope` | 필수 |공백으로 구분 된 [범위](v2-permissions-and-consent.md)목록입니다. Openid connect Connect (id_tokens)의 경우 `openid` 동의 UI의 "로그인" 권한으로 변환 되는 범위를 포함 해야 합니다. 필요에 따라 `email` `profile` 추가 사용자 데이터에 대 한 액세스 권한을 얻기 위해 및 범위를 포함할 수도 있습니다. 액세스 토큰을 요청 하는 경우 다양 한 리소스에 대 한 동의를 요청 하기 위해이 요청에 다른 범위를 포함할 수도 있습니다. |
| `response_mode` | 선택적 |결과 토큰을 앱으로 다시 보내는 데 사용해야 하는 메서드를 지정합니다. 는 기본적으로 액세스 토큰에 대해서만 쿼리 하지만 요청에 id_token 포함 된 경우에는 fragment를 사용 합니다. |
| `state` | 권장 |토큰 응답에도 반환되는 요청에 포함된 값입니다. 원하는 모든 콘텐츠의 문자열일 수 있습니다. 일반적으로 [교차 사이트 요청 위조 공격을 방지](https://tools.ietf.org/html/rfc6749#section-10.12)하기 위해 임의로 생성된 고유 값이 사용됩니다. 또한 state는 인증 요청이 발생하기 전에 앱에서 사용자 상태에 대한 정보(예: 페이지 또는 보기)를 인코딩하는 데 사용됩니다. |
| `nonce` | 필수 |앱에서 생성하여 요청에 포함된 값이며, 결과 id_token에 클레임으로 포함됩니다. 그러면 앱에서 이 값을 확인하여 토큰 재생 공격을 완화할 수 있습니다. 이 값은 일반적으로 요청의 출처를 식별하는 데 사용할 수 있는 임의의 고유 문자열입니다. id_token이 요청된 경우에만 필요합니다. |
| `prompt` | 선택적 |필요한 사용자 상호 작용 유형을 나타냅니다. 현재 유효한 값은 'login', 'none', 'select_account', 'consent'뿐입니다. `prompt=login` 은 Single-Sign On을 무효화면서, 사용자가 요청에 자신의 자격 증명을 입력하도록 합니다. `prompt=none` 그 반대의 경우에는 사용자에 게 대화형 프롬프트가 표시 되지 않습니다. Single sign-on을 통해 요청을 자동으로 완료할 수 없는 경우 Microsoft id 플랫폼은 오류를 반환 합니다. `prompt=select_account`는 세션에 저장된 모든 계정이 표시되는 계정 선택기로 사용자를 전송합니다. `prompt=consent` 는 사용자가 로그인한 후에 OAuth 동의 대화 상자를 트리거하여 앱에 권한을 부여할 것을 사용자에게 요청합니다. |
| `login_hint`  |선택적 |사용자 이름을 미리 알고 있는 경우 사용자 로그인 페이지의 사용자 이름/이메일 주소 필드를 미리 채우는 데 사용할 수 있습니다. 일반적으로 앱은 재인증 중에이 매개 변수를 사용 하 고 클레임을 사용 하 여 이전 로그인에서 사용자 이름을 추출 `preferred_username` 했습니다.|
| `domain_hint` | 선택적 |포함 되는 경우 사용자가 로그인 페이지에서 이동 하는 전자 메일 기반 검색 프로세스를 건너뛰고 약간 더 간소화 된 사용자 환경을 제공 합니다. 이 매개 변수는 단일 테 넌 트에서 작동 하는 lob (기간 업무) 앱에 일반적으로 사용 되며, 지정 된 테 넌 트 내에서 도메인 이름을 제공 하 여 사용자를 해당 테 넌 트의 페더레이션 공급자에 게 전달 합니다.  이 힌트는 게스트가이 응용 프로그램에 로그인 하는 것을 방지 하 고 FIDO와 같은 클라우드 자격 증명의 사용을 제한 합니다.  |

이 시점에서 사용자에게 자격 증명을 입력하고 인증을 완료하라는 메시지가 표시됩니다. Microsoft id 플랫폼은 또한 사용자가 쿼리 매개 변수에 표시 된 사용 권한에 동의한 확인 합니다 `scope` . 사용자가 해당 권한 중 어떤 항목에도 동의하지 **않은** 경우에는 필요한 권한에 동의하라는 메시지가 표시됩니다. 자세한 내용은. [권한, 동의 및 다중 테넌트 앱](v2-permissions-and-consent.md)을 참조하세요.

사용자가 인증 하 고 동의 하면 Microsoft id 플랫폼은 `redirect_uri` 매개 변수에 지정 된 메서드를 사용 하 여 지정 된에서 앱에 대 한 응답을 반환 합니다 `response_mode` .

#### <a name="successful-response"></a>성공적인 응답

`response_mode=fragment` 및 `response_type=id_token+code` 사용 시의 정상 응답은 다음과 같습니다. 쉽게 읽을 수 있도록 아래 예제에는 줄 바꿈이 적용되어 있습니다.

```HTTP
GET https://localhost/myapp/#
code=0.AgAAktYV-sfpYESnQynylW_UKZmH-C9y_G1A
&id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
&state=12345
```

| 매개 변수 | 설명 |
| --- | --- |
| `code` | `response_type`이 `code`을 포함하는 경우 포함됩니다. 이는 [인증 코드 흐름](v2-oauth2-auth-code-flow.md)에서 사용 하기에 적합 한 인증 코드입니다.  |
| `access_token` |`response_type`이 `token`을 포함하는 경우 포함됩니다. 앱에서 요청한 액세스 토큰입니다. 액세스 토큰을 디코드 하거나 검사 하지 않아야 합니다 .이 토큰은 불투명 문자열로 처리 되어야 합니다. |
| `token_type` |`response_type`이 `token`을 포함하는 경우 포함됩니다. 항상 `Bearer`입니다. |
| `expires_in`|`response_type`이 `token`을 포함하는 경우 포함됩니다. 캐싱을 위해 토큰이 유효한 시간(초)을 나타냅니다. |
| `scope` |`response_type`이 `token`을 포함하는 경우 포함됩니다. access_token이 유효한 범위를 나타냅니다. 사용자에 게 적용할 수 없는 경우 요청 된 범위를 모두 포함 하지 않을 수 있습니다 (개인 계정을 사용 하 여 로그인 할 때 Azure AD 전용 범위를 요청 하는 경우). |
| `id_token` | 서명된 JWT(JSON 웹 토큰)입니다. 앱은 이 토큰의 세그먼트를 디코드하여 로그인한 사용자에 대한 정보를 요청할 수 있습니다. 앱은 값을 캐시 하 고 표시할 수 있지만 권한 부여 또는 보안 경계에는이 값을 사용 하지 않아야 합니다. id_tokens에 대한 자세한 내용은 [`id_token reference`](id-tokens.md)를 참조하세요. <br> **참고:** `openid` 범위가 요청 되 고 포함 된 경우 `response_type` 에만 제공 `id_tokens` 됩니다. |
| `state` |state 매개 변수가 요청에 포함된 경우 동일한 값이 응답에 표시됩니다. 앱은 요청 및 응답의 상태 값이 동일한지 확인해야 합니다. |

#### <a name="error-response"></a>오류 응답

앱이 적절하게 처리할 수 있도록 `redirect_uri` 에 오류 응답을 보낼 수도 있습니다.

```HTTP
GET https://localhost/myapp/#
error=access_denied
&error_description=the+user+canceled+the+authentication
```

| 매개 변수 | Description |
| --- | --- |
| `error` |발생하는 오류 유형을 분류하는 데 사용할 수 있고 오류에 대응하는 데 사용할 수 있는 오류 코드 문자열입니다. |
| `error_description` |개발자가 인증 오류의 근본 원인을 식별하도록 도울 수 있는 특정 오류 메시지입니다. |

## <a name="getting-access-tokens-silently-in-the-background"></a>백그라운드에서 자동으로 액세스 토큰 가져오기

> [!Important]
> 암시적 흐름의이 부분은 [기본적으로 타사 쿠키의 제거](reference-third-party-cookies-spas.md)로 인해 다른 브라우저에서 사용 되므로 응용 프로그램에 대해 작동 하지 않을 수 있습니다.  아직 Incognito에 없는 Chromium 기반 브라우저에서는 계속 작동 하지만 개발자는 흐름의이 부분을 사용 하는 것을 고려해 야 합니다. 타사 쿠키를 지원 하지 않는 브라우저에서는 로그인 페이지의 세션 쿠키가 브라우저에서 제거 되었으므로 사용자가 로그인 하지 않았음을 나타내는 오류가 표시 됩니다. 

사용자를 단일 페이지 앱에 로그인 했으므로 Microsoft id 플랫폼에 의해 보호 되는 웹 Api를 호출 하기 위한 액세스 토큰을 자동으로 가져올 수 있습니다 (예: [Microsoft Graph](https://developer.microsoft.com/graph)). `token` response_type을 사용하여 토큰을 이미 받았더라도 이 방법을 사용하여 사용자가 다시 로그인하도록 리디렉션하지 않고 추가 리소스에 대한 토큰을 얻을 수 있습니다.

일반적인 Openid connect Connect/OAuth 흐름에서 Microsoft id 플랫폼 끝점에 요청을 수행 하 여이 작업을 수행 `/token` 합니다. 숨겨진 iframe에서 요청을 만들어 다른 웹 Api에 대 한 새 토큰을 가져올 수 있습니다.

```
// Line breaks for legibility only

https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&response_type=token
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&scope=https%3A%2F%2Fgraph.microsoft.com%2Fuser.read
&response_mode=fragment
&state=12345
&nonce=678910
&prompt=none
&login_hint=myuser@mycompany.com
```

URL의 쿼리 매개 변수에 대한 자세한 내용은 [로그인 요청 보내기](#send-the-sign-in-request)를 참조하세요.

> [!TIP]
> 아래 요청을 브라우저 탭에 복사하여 붙여 넣으세요! (`login_hint` 값을 사용자에 대한 올바른 값으로 바꾸는 것을 잊지 마세요)
>
>`https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&response_type=token&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F&scope=https%3A%2F%2Fgraph.microsoft.com%2Fuser.read&response_mode=fragment&state=12345&nonce=678910&prompt=none&login_hint={your-username}`
>
> 이는 iframe 내에서 열지 않고 브라우저 모음에 직접 입력 하므로 타사 쿠키를 지원 하지 않는 브라우저 에서도 작동 합니다. 

`prompt=none` 매개 변수로 인해, 이 요청은 즉시 성공하거나 실패하고 애플리케이션에 반환됩니다. 응답은 `redirect_uri` 매개 변수에 지정 된 메서드를 사용 하 여 지정 된에서 앱으로 전송 됩니다 `response_mode` .

#### <a name="successful-response"></a>성공적인 응답

`response_mode=fragment` 를 사용한 성공적인 응답은 다음과 같습니다.

```HTTP
GET https://localhost/myapp/#
access_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
&state=12345
&token_type=Bearer
&expires_in=3599
&scope=https%3A%2F%2Fgraph.microsoft.com%2Fdirectory.read
```

| 매개 변수 | 설명 |
| --- | --- |
| `access_token` |`response_type`이 `token`을 포함하는 경우 포함됩니다. Microsoft Graph의 경우 앱에서 요청한 액세스 토큰입니다. 액세스 토큰을 디코드 하거나 검사 하지 않아야 합니다 .이 토큰은 불투명 문자열로 처리 되어야 합니다. |
| `token_type` | 항상 `Bearer`입니다. |
| `expires_in` | 캐싱을 위해 토큰이 유효한 시간(초)을 나타냅니다. |
| `scope` | access_token이 유효한 범위를 나타냅니다. 사용자에 게 적용할 수 없는 경우 요청 된 범위를 모두 포함 하지 않을 수 있습니다 (개인 계정을 사용 하 여 로그인 할 때 Azure AD 전용 범위를 요청 하는 경우). |
| `id_token` | 서명된 JWT(JSON 웹 토큰)입니다. `response_type`이 `id_token`을 포함하는 경우 포함됩니다. 앱은 이 토큰의 세그먼트를 디코드하여 로그인한 사용자에 대한 정보를 요청할 수 있습니다. 앱은 값을 캐시 하 고 표시할 수 있지만 권한 부여 또는 보안 경계에는이 값을 사용 하지 않아야 합니다. Id_tokens에 대 한 자세한 내용은 [ `id_token` 참조](id-tokens.md)를 참조 하세요. <br> **참고:** `openid` 범위가 요청된 경우에만 제공됩니다. |
| `state` |state 매개 변수가 요청에 포함된 경우 동일한 값이 응답에 표시됩니다. 앱은 요청 및 응답의 상태 값이 동일한지 확인해야 합니다. |

#### <a name="error-response"></a>오류 응답

앱이 적절하게 처리할 수 있도록 `redirect_uri` 에 오류 응답을 보낼 수도 있습니다. `prompt=none`의 경우, 예상되는 오류는 다음과 같습니다.

```HTTP
GET https://localhost/myapp/#
error=user_authentication_required
&error_description=the+request+could+not+be+completed+silently
```

| 매개 변수 | Description |
| --- | --- |
| `error` |발생하는 오류 유형을 분류하는 데 사용할 수 있고 오류에 대응하는 데 사용할 수 있는 오류 코드 문자열입니다. |
| `error_description` |개발자가 인증 오류의 근본 원인을 식별하도록 도울 수 있는 특정 오류 메시지입니다. |

iFrame 요청에 이러한 오류를 수신하면, 사용자는 새 토큰을 얻기 위해 대화형으로 다시 로그인해야 합니다. 어떠한 방식이든 애플리케이션에 적합한 방식으로 이러한 경우를 처리하도록 선택할 수 있습니다.

## <a name="refreshing-tokens"></a>토큰 새로 고침

암시적 권한 부여는 새로 고침 토큰을 제공하지 않습니다. `id_token`과 `access_token`은 모두 짧은 시간 안에 만료되기 때문에 앱은 주기적으로 토큰을 새로 고치도록 준비가 되어야 합니다. 두 가지 유형의 토큰을 새로 고치려면 매개 변수를 사용 하 여 위와 동일한 숨겨진 iframe 요청을 수행 하 여 `prompt=none` id 플랫폼의 동작을 제어할 수 있습니다. 새를 받으려는 경우 `id_token` `id_token` 및에서 `response_type` 및 매개 변수를 사용 해야 `scope=openid` `nonce` 합니다.

타사 쿠키를 지원 하지 않는 브라우저에서는 로그인 한 사용자가 없다는 오류가 표시 됩니다. 

## <a name="send-a-sign-out-request"></a>로그아웃 요청 보내기

Openid connect Connect를 `end_session_endpoint` 사용 하면 앱에서 microsoft id 플랫폼에 요청을 보내 사용자 세션을 종료 하 고 microsoft id 플랫폼에서 설정한 쿠키를 지울 수 있습니다. 앱은 웹 애플리케이션에서 특정 사용자를 완전히 로그아웃시키기 위해 일반적으로 토큰 캐시를 지우거나 쿠키를 삭제하여 고유한 사용자 세션을 종료한 다음, 브라우저를 아래 주소로 리디렉션합니다.

```
https://login.microsoftonline.com/{tenant}/oauth2/v2.0/logout?post_logout_redirect_uri=https://localhost/myapp/
```

| 매개 변수 | Type | Description |
| --- | --- | --- |
| `tenant` |required |요청의 경로에 있는 `{tenant}` 값을 사용하여 애플리케이션에 로그인할 수 있는 사용자를 제어할 수 있습니다. 허용되는 값은 `common`, `organizations`, `consumers` 및 테넌트 ID입니다. 자세한 내용은 [프로토콜 기본](active-directory-v2-protocols.md#endpoints)을 참조하세요. |
| `post_logout_redirect_uri` | 권장 | 로그아웃이 완료된 후 사용자가 반환되어야 하는 URL입니다. 이 값은 애플리케이션에 대해 등록된 리디렉션 URI 중 하나와 일치해야 합니다. 포함 되지 않은 경우 사용자는 Microsoft id 플랫폼에 의해 일반 메시지가 표시 됩니다. |

## <a name="next-steps"></a>다음 단계

* [MSAL JS 샘플](sample-v2-code.md)을 검토하여 코딩을 시작합니다.
* 암시적 권한 부여에 대 한 보다 최신의 대안으로 [권한 부여 코드 흐름](v2-oauth2-auth-code-flow.md) 을 검토 합니다. 
