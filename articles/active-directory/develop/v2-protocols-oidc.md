---
title: Microsoft identity platform & Openid connect Connect 프로토콜 | Microsoft
description: Openid connect Connect 인증 프로토콜의 Microsoft identity platform 구현을 사용 하 여 웹 응용 프로그램을 빌드합니다.
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
editor: ''
ms.assetid: a4875997-3aac-4e4c-b7fe-2b4b829151ce
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/12/2019
ms.author: ryanwi
ms.reviewer: hirsin
ms.custom: aaddev, identityplatformtop40
ms.collection: M365-identity-device-management
ms.openlocfilehash: bc3778f31cb5dd68d3f3f49ed3cddf574b1cc3bd
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/10/2019
ms.locfileid: "74966744"
---
# <a name="microsoft-identity-platform-and-openid-connect-protocol"></a>Microsoft id 플랫폼 및 Openid connect Connect 프로토콜

Openid connect Connect는 웹 응용 프로그램에 사용자를 안전 하 게 로그인 하는 데 사용할 수 있는 OAuth 2.0을 기반으로 하는 인증 프로토콜입니다. Openid connect Connect의 Microsoft identity platform 끝점 구현을 사용 하는 경우 웹 기반 앱에 로그인 및 API 액세스를 추가할 수 있습니다. 이 문서에서는 언어와 독립적으로이 작업을 수행 하는 방법을 보여 주고 Microsoft 오픈 소스 라이브러리를 사용 하지 않고 HTTP 메시지를 보내고 받는 방법을 설명 합니다.

> [!NOTE]
> Microsoft id 플랫폼 끝점은 모든 Azure Active Directory (Azure AD) 시나리오 및 기능을 지원 하지 않습니다. Microsoft id 플랫폼 끝점을 사용 해야 하는지 여부를 확인 하려면 [microsoft id 플랫폼 제한 사항](active-directory-v2-limitations.md)을 참조 하세요.

[Openid connect Connect](https://openid.net/specs/openid-connect-core-1_0.html) 는 oauth 2.0 *권한 부여* 프로토콜을 확장 하 여 oauth를 사용 하 Single Sign-On 수행할 수 있도록 *인증* 프로토콜로 사용 합니다. Openid connect Connect는 클라이언트가 사용자의 id를 확인할 수 있도록 하는 보안 토큰 인 *ID 토큰*의 개념을 소개 합니다. 또한 ID 토큰은 사용자에 대 한 기본 프로필 정보를 가져옵니다. Openid connect Connect는 OAuth 2.0를 확장 하므로, 앱은 [권한 부여 서버](active-directory-v2-protocols.md#the-basics)에 의해 보호 되는 리소스에 액세스 하는 데 사용할 수 있는 *액세스 토큰*을 안전 하 게 가져올 수 있습니다. Microsoft id 플랫폼 끝점을 사용 하면 Azure AD에 등록 된 타사 앱에서 웹 Api와 같은 보안 리소스에 대 한 액세스 토큰을 발급할 수도 있습니다. 액세스 토큰을 발급 하도록 응용 프로그램을 설정 하는 방법에 대 한 자세한 내용은 [Microsoft id 플랫폼 끝점을 사용 하 여 앱을 등록 하는 방법](quickstart-register-app.md)을 참조 하세요. 서버에서 호스트 되 고 브라우저를 통해 액세스 되는 [웹 응용 프로그램](v2-app-types.md#web-apps) 을 빌드하는 경우에는 openid connect Connect를 사용 하는 것이 좋습니다.

## <a name="protocol-diagram-sign-in"></a>프로토콜 다이어그램: 로그인

가장 기본적인 로그인 흐름에는 다음 다이어그램에 표시 된 단계가 있습니다. 각 단계에 대해서는이 문서에서 자세히 설명 합니다.

![Openid connect Connect 프로토콜: 로그인](./media/v2-protocols-oidc/convergence-scenarios-webapp.svg)

## <a name="fetch-the-openid-connect-metadata-document"></a>Openid connect Connect 메타 데이터 문서를 가져옵니다.

Openid connect Connect는 앱이 로그인을 수행 하는 데 필요한 대부분의 정보를 포함 하는 메타 데이터 문서를 설명 합니다. 여기에는 사용할 Url, 서비스의 공개 서명 키의 위치 등과 같은 정보가 포함 됩니다. Microsoft id 플랫폼 끝점의 경우 사용 해야 하는 Openid connect Connect 메타 데이터 문서입니다.

```
https://login.microsoftonline.com/{tenant}/v2.0/.well-known/openid-configuration
```
> [!TIP]
> Próbálja ki! [https://login.microsoftonline.com/common/v2.0/.well-known/openid-configuration](https://login.microsoftonline.com/common/v2.0/.well-known/openid-configuration) 를 클릭 하 `common` 테 넌 트 구성을 확인 합니다.

`{tenant}`는 다음 네 가지 값 중 하나를 사용할 수 있습니다.

| Value (Díj) | Leírás |
| --- | --- |
| `common` |개인 Microsoft 계정와 Azure AD의 회사 또는 학교 계정이 모두 있는 사용자는 응용 프로그램에 로그인 할 수 있습니다. |
| `organizations` |Azure AD의 회사 또는 학교 계정이 있는 사용자만 응용 프로그램에 로그인 할 수 있습니다. |
| `consumers` |개인 Microsoft 계정 있는 사용자만 응용 프로그램에 로그인 할 수 있습니다. |
| `8eaef023-2b34-4da1-9baa-8bc8c9d6a490` vagy `contoso.onmicrosoft.com` | 특정 Azure AD 테 넌 트의 사용자만 (회사 또는 학교 계정을 사용 하 여 디렉터리의 구성원 인지 또는 개인 Microsoft 계정 있는 디렉터리의 게스트) 응용 프로그램에 로그인 할 수 있습니다. Azure AD 테 넌 트의 친숙 한 도메인 이름 또는 테 넌 트의 GUID 식별자 중 하나를 사용할 수 있습니다. `consumers` 테 넌 트 대신 소비자 테 넌 트 `9188040d-6c67-4c5b-b112-36a304b66dad`를 사용할 수도 있습니다.  |

메타 데이터는 JSON (simple JavaScript Object Notation) 문서입니다. 예제는 다음 코드 조각을 참조 하세요. 코드 조각 내용은 [Openid connect Connect 사양](https://openid.net/specs/openid-connect-discovery-1_0.html#rfc.section.4.2)에 자세히 설명 되어 있습니다.

```
{
  "authorization_endpoint": "https:\/\/login.microsoftonline.com\/{tenant}\/oauth2\/v2.0\/authorize",
  "token_endpoint": "https:\/\/login.microsoftonline.com\/{tenant}\/oauth2\/v2.0\/token",
  "token_endpoint_auth_methods_supported": [
    "client_secret_post",
    "private_key_jwt"
  ],
  "jwks_uri": "https:\/\/login.microsoftonline.com\/{tenant}\/discovery\/v2.0\/keys",

  ...

}
```

[클레임 매핑](active-directory-claims-mapping.md) 기능을 사용한 결과로 앱에 사용자 지정 서명 키가 있는 경우 앱의 서명 키 정보를 가리키는 `jwks_uri`을 가져오려면 앱 ID가 포함 된 `appid` 쿼리 매개 변수를 추가 해야 합니다. 예를 들어 `https://login.microsoftonline.com/{tenant}/.well-known/v2.0/openid-configuration?appid=6731de76-14a6-49ae-97bc-6eba6914391e`에는 `https://login.microsoftonline.com/{tenant}/discovery/v2.0/keys?appid=6731de76-14a6-49ae-97bc-6eba6914391e``jwks_uri` 포함 되어 있습니다.

일반적으로이 메타 데이터 문서를 사용 하 여 Openid connect Connect 라이브러리 또는 SDK를 구성 합니다. 라이브러리는 메타 데이터를 사용 하 여 작업을 수행 합니다. 그러나 미리 작성 된 Openid connect Connect 라이브러리를 사용 하지 않는 경우이 문서의 나머지 부분에 있는 단계에 따라 Microsoft identity platform 끝점을 사용 하 여 웹 앱에서 로그인 할 수 있습니다.

## <a name="send-the-sign-in-request"></a>로그인 요청 보내기

웹 앱이 사용자를 인증 해야 하는 경우 사용자를 `/authorize` 끝점으로 보낼 수 있습니다. 이 요청은 [OAuth 2.0 권한 부여 코드 흐름](v2-oauth2-auth-code-flow.md)의 첫 번째 레그와 유사 하지만 다음과 같은 중요 한 차이점이 있습니다.

* 요청은 `scope` 매개 변수의 `openid` 범위를 포함 해야 합니다.
* `response_type` 매개 변수는 `id_token`를 포함 해야 합니다.
* 요청은 `nonce` 매개 변수를 포함 해야 합니다.

> [!IMPORTANT]
> /Cauthentication 끝점에서 ID 토큰을 성공적으로 요청 하려면 [등록 포털](https://portal.azure.com) 의 앱 등록에 인증 탭에서 사용 하도록 설정 된 id_tokens의 암시적 허용이 있어야 합니다 ( [응용 프로그램 매니페스트의](reference-app-manifest.md) `oauth2AllowIdTokenImplicitFlow` 플래그를 `true`로 설정). 사용할 수 없는 경우 `unsupported_response` 오류가 반환 됩니다. "입력 매개 변수 ' response_type '에 대해 제공 된 값이이 클라이언트에 허용 되지 않습니다. 필요한 값은 ' 코드 '입니다.

Példa:

```
// Line breaks are for legibility only.

GET https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&response_type=id_token
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&response_mode=form_post
&scope=openid
&state=12345
&nonce=678910
```

> [!TIP]
> 이 요청을 실행 하려면 다음 링크를 클릭 하십시오. 로그인 하면 브라우저는 주소 표시줄에 ID 토큰과 함께 `https://localhost/myapp/`로 리디렉션됩니다. 이 요청은 `response_mode=fragment`를 사용 합니다 (데모용 으로만 사용). `response_mode=form_post`를 사용 하는 것이 좋습니다.
> <a href="https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&response_type=id_token&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F&scope=openid&response_mode=fragment&state=12345&nonce=678910" target="_blank">https://login.microsoftonline.com/common/oauth2/v2.0/authorize...</a>

| Paraméter | Állapot | Leírás |
| --- | --- | --- |
| `tenant` | Szükséges | 요청 경로에서 `{tenant}` 값을 사용 하 여 응용 프로그램에 로그인 할 수 있는 사용자를 제어할 수 있습니다. 허용 되는 값은 `common`, `organizations`, `consumers`및 테 넌 트 식별자입니다. 자세한 내용은 [프로토콜 기본 사항](active-directory-v2-protocols.md#endpoints)을 참조 하세요. |
| `client_id` | Szükséges | [Azure Portal – 앱 등록](https://go.microsoft.com/fwlink/?linkid=2083908) 환경에서 앱에 할당 한 **응용 프로그램 (클라이언트) ID** 입니다. |
| `response_type` | Szükséges | Openid connect Connect 로그인에 대 한 `id_token`를 포함 해야 합니다. `code`와 같은 다른 `response_type` 값도 포함할 수 있습니다. |
| `redirect_uri` | Ajánlott | 앱에서 인증 응답을 보내고 받을 수 있는 앱의 리디렉션 URI입니다. URL로 인코딩해야 한다는 점을 제외 하 고 포털에서 등록 한 리디렉션 Uri 중 하 나와 정확히 일치 해야 합니다. 없는 경우 끝점은 임의로 등록 된 redirect_uri 하나를 선택 하 여 사용자를로 다시 보냅니다. |
| `scope` | Szükséges | 공백으로 구분 된 범위 목록입니다. Openid connect Connect의 경우 동의 UI의 "로그인" 권한으로 변환 되는 `openid`범위를 포함 해야 합니다. 동의를 요청 하기 위해이 요청에 다른 범위를 포함할 수도 있습니다. |
| `nonce` | Szükséges | 앱에서 생성 한 요청에 포함 된 값으로, 결과 id_token 값에 클레임으로 포함 됩니다. 앱은 토큰 재생 공격을 완화 하기 위해이 값을 확인할 수 있습니다. 값은 일반적으로 요청의 출처를 식별 하는 데 사용할 수 있는 임의의 고유 문자열입니다. |
| `response_mode` | Ajánlott | 결과 권한 부여 코드를 앱에 다시 보내는 데 사용 해야 하는 메서드를 지정 합니다. A következők egyike lehet: `form_post` vagy `fragment`. 웹 응용 프로그램의 경우 응용 프로그램에 토큰을 가장 안전 하 게 전송 하기 위해 `response_mode=form_post`를 사용 하는 것이 좋습니다. |
| `state` | Ajánlott | 토큰 응답에도 반환 되는 요청에 포함 된 값입니다. 원하는 모든 콘텐츠의 문자열일 수 있습니다. 일반적으로 임의로 생성 된 고유 값은 [교차 사이트 요청 위조 공격을 방지](https://tools.ietf.org/html/rfc6749#section-10.12)하는 데 사용 됩니다. 또한 상태는 인증 요청이 발생 하기 전에 앱에서 사용자 상태에 대 한 정보를 인코딩하는 데 사용 됩니다 (예: 사용자의 페이지 또는 보기). |
| `prompt` | Választható | 필요한 사용자 상호 작용의 유형을 나타냅니다. 이 경우 유효한 값은 `login`, `none`및 `consent`뿐입니다. `prompt=login` 클레임은 사용자가 해당 요청에 대 한 자격 증명을 입력 하도록 합니다. 이렇게 하면 Single Sign-On 부정 됩니다. `prompt=none` 클레임은 그 반대입니다. 이 클레임은 사용자가에 대화형 프롬프트를 표시 하지 않도록 합니다. Single Sign-On를 통해 요청을 자동으로 완료할 수 없는 경우 Microsoft id 플랫폼 끝점에서 오류를 반환 합니다. `prompt=consent` 클레임은 사용자가 로그인 한 후에 OAuth 동의 대화 상자를 트리거합니다. 대화 상자에서 사용자에 게 앱에 대 한 사용 권한을 부여 하 라는 메시지를 표시 합니다. |
| `login_hint` | Választható | 이 매개 변수를 사용 하 여 미리 사용자 이름을 미리 알고 있는 경우 사용자에 대 한 로그인 페이지의 사용자 이름 및 전자 메일 주소 필드를 미리 채울 수 있습니다. 일반적으로 앱은 `preferred_username` 클레임을 사용 하 여 이전 로그인에서 사용자 이름을 추출 하 고 재인증 중에이 매개 변수를 사용 합니다. |
| `domain_hint` | Választható | 페더레이션된 디렉터리에 있는 사용자의 영역입니다.  이렇게 하면 사용자가 로그인 페이지에서 이동 하는 전자 메일 기반 검색 프로세스를 건너뛰고 약간 더 간소화 된 사용자 환경을 제공 합니다. AD FS 같은 온-프레미스 디렉터리를 통해 페더레이션된 테 넌 트의 경우 기존 로그인 세션 때문에 원활한 로그인이 발생 하는 경우가 많습니다. |

이 시점에서 사용자에 게 자격 증명을 입력 하 고 인증을 완료 하 라는 메시지가 표시 됩니다. Microsoft id 플랫폼 끝점은 사용자가 `scope` 쿼리 매개 변수에 표시 된 사용 권한에 동의한 확인 합니다. 사용자가 이러한 사용 권한 중 하나에 동의한 하지 않은 경우 Microsoft identity platform 끝점에서 사용자에 게 필요한 사용 권한에 동의 하 라는 메시지를 표시 합니다. [권한, 동의 및 다중 테 넌 트 앱](v2-permissions-and-consent.md)에 대해 자세히 알아볼 수 있습니다.

사용자가 인증 하 고 동의 하면 Microsoft id 플랫폼 끝점이 `response_mode` 매개 변수에 지정 된 메서드를 사용 하 여 표시 된 리디렉션 URI에서 앱에 대 한 응답을 반환 합니다.

### <a name="successful-response"></a>성공적인 응답

`response_mode=form_post`를 사용 하는 경우 성공적인 응답은 다음과 같습니다.

```
POST /myapp/ HTTP/1.1
Host: localhost
Content-Type: application/x-www-form-urlencoded

id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik1uQ19WWmNB...&state=12345
```

| Paraméter | Leírás |
| --- | --- |
| `id_token` | 앱이 요청한 ID 토큰입니다. `id_token` 매개 변수를 사용 하 여 사용자의 id를 확인 하 고 사용자와 세션을 시작할 수 있습니다. ID 토큰 및 해당 내용에 대 한 자세한 내용은 [`id_tokens` 참조](id-tokens.md)를 참조 하세요. |
| `state` | 요청에 `state` 매개 변수가 포함 되어 있으면 동일한 값이 응답에 표시 됩니다. 앱은 요청 및 응답의 상태 값이 동일한 지 확인 해야 합니다. |

### <a name="error-response"></a>오류 응답

앱이 처리할 수 있도록 리디렉션 URI에 오류 응답을 보낼 수도 있습니다. 오류 응답은 다음과 같습니다.

```
POST /myapp/ HTTP/1.1
Host: localhost
Content-Type: application/x-www-form-urlencoded

error=access_denied&error_description=the+user+canceled+the+authentication
```

| Paraméter | Leírás |
| --- | --- |
| `error` | 발생 한 오류 유형을 분류 하 고 오류에 대응 하는 데 사용할 수 있는 오류 코드 문자열입니다. |
| `error_description` | 인증 오류의 근본 원인을 식별 하는 데 도움이 될 수 있는 특정 오류 메시지입니다. |

### <a name="error-codes-for-authorization-endpoint-errors"></a>권한 부여 끝점 오류에 대 한 오류 코드

다음 표에서는 오류 응답의 `error` 매개 변수에 반환 될 수 있는 오류 코드에 대해 설명 합니다.

| Hibakód | Leírás | 클라이언트 작업 |
| --- | --- | --- |
| `invalid_request` | 누락 된 필수 매개 변수와 같은 프로토콜 오류입니다. |요청을 수정 하 고 다시 제출 합니다. 일반적으로 초기 테스트 중에 발견 되는 개발 오류입니다. |
| `unauthorized_client` | 클라이언트 응용 프로그램에서 권한 부여 코드를 요청할 수 없습니다. |이는 일반적으로 클라이언트 응용 프로그램이 Azure AD에 등록 되지 않았거나 사용자의 Azure AD 테 넌 트에 추가 되지 않은 경우에 발생 합니다. 응용 프로그램은 응용 프로그램을 설치 하 고 Azure AD에 추가 하는 지침을 사용자에 게 표시할 수 있습니다. |
| `access_denied` | 리소스 소유자가 동의를 거부 했습니다. |클라이언트 응용 프로그램은 사용자가 동의 때까지 계속할 수 없다고 사용자에 게 알릴 수 있습니다. |
| `unsupported_response_type` |권한 부여 서버가 요청에서 응답 유형을 지원 하지 않습니다. |요청을 수정 하 고 다시 제출 합니다. 일반적으로 초기 테스트 중에 발견 되는 개발 오류입니다. |
| `server_error` | 서버에서 예기치 않은 오류가 발생 했습니다. |요청을 다시 시도 하세요. 이러한 오류는 일시적인 조건으로 인해 발생할 수 있습니다. 클라이언트 응용 프로그램은 일시적인 오류로 인해 응답이 지연 되었음을 사용자에 게 설명할 수 있습니다. |
| `temporarily_unavailable` | 서버 사용량이 너무 많아 요청을 처리할 수 없습니다. |요청을 다시 시도 하세요. 클라이언트 응용 프로그램은 일시적인 상태 때문에 응답이 지연 되었음을 사용자에 게 설명할 수 있습니다. |
| `invalid_resource` | 대상 리소스가 존재 하지 않거나 Azure AD에서 해당 리소스를 찾을 수 없거나 올바르게 구성 되지 않았기 때문에 잘못 되었습니다. |이는 리소스가 있는 경우 테 넌 트에서 구성 되지 않았음을 나타냅니다. 응용 프로그램에서 응용 프로그램을 설치 하 고 Azure AD에 추가 하는 방법에 대 한 지침을 사용자에 게 표시할 수 있습니다. |

## <a name="validate-the-id-token"></a>ID 토큰의 유효성을 검사 합니다.

Id_token 수신 하는 것 만으로는 사용자를 인증할 수 없습니다. id_token 서명의 유효성을 검사 하 고 앱의 요구 사항에 따라 토큰의 클레임을 확인 해야 합니다. Microsoft id 플랫폼 끝점은 jwt [(JSON 웹 토큰)](https://self-issued.info/docs/draft-ietf-oauth-json-web-token.html) 및 공개 키 암호화를 사용 하 여 토큰에 서명 하 고 토큰이 유효한 지 확인 합니다.

클라이언트 코드에서 `id_token`의 유효성을 검사 하도록 선택할 수 있지만, 일반적으로 `id_token`을 백 엔드 서버로 보내고 그에 대 한 유효성 검사를 수행 하는 것이 좋습니다. Id_token 서명의 유효성을 검사 한 후에는 확인 해야 할 몇 가지 클레임이 있습니다. [토큰 유효성 검사](id-tokens.md#validating-an-id_token) 및 [서명 키 롤오버에 대 한 중요 한 정보](active-directory-signing-key-rollover.md)를 비롯 한 자세한 내용은 [`id_token` 참조](id-tokens.md) 를 참조 하세요. 토큰을 구문 분석 하 고 유효성을 검사 하는 라이브러리를 사용 하는 것이 좋습니다. 대부분의 언어 및 플랫폼에 대해 하나 이상의 사용할 수 있습니다.

시나리오에 따라 추가 클레임의 유효성을 검사할 수도 있습니다. 몇 가지 일반적인 유효성 검사는 다음과 같습니다.

* 사용자/조직이 앱에 등록 했는지 확인 합니다.
* 사용자에 게 적절 한 권한 부여/권한이 있도록 보장
* Multi-factor authentication과 같은 특정 수준의 인증이 발생 했는지 확인 합니다.

Id_token의 유효성을 검사 한 후 사용자와 세션을 시작 하 고 id_token의 클레임을 사용 하 여 앱에서 사용자에 대 한 정보를 가져올 수 있습니다. 이 정보는 표시, 레코드, 개인 설정 등에 사용할 수 있습니다.

## <a name="send-a-sign-out-request"></a>로그 아웃 요청 보내기

앱에서 사용자를 로그 아웃 시키려는 경우 앱의 쿠키를 지우거 나 사용자의 세션을 종료 하는 것 만으로는 충분 하지 않습니다. 또한 로그 아웃 하려면 Microsoft id 플랫폼 끝점으로 사용자를 리디렉션해야 합니다. 이 작업을 수행 하지 않으면 사용자가 자격 증명을 다시 입력 하지 않고 앱에 reauthenticates 됩니다 .이는 Microsoft id 플랫폼 끝점과의 유효한 single sign-on 세션이 있기 때문입니다.

Openid connect Connect 메타 데이터 문서에 나열 된 `end_session_endpoint` 사용자를 리디렉션할 수 있습니다.

```
GET https://login.microsoftonline.com/common/oauth2/v2.0/logout?
post_logout_redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
```

| Paraméter | Állapot | Leírás |
| ----------------------- | ------------------------------- | ------------ |
| `post_logout_redirect_uri` | Ajánlott | 성공적으로 로그 아웃 한 후 사용자가 리디렉션되는 URL입니다. 매개 변수가 포함 되지 않은 경우 사용자는 Microsoft id 플랫폼 끝점에 의해 생성 된 일반 메시지를 표시 합니다. 이 URL은 앱 등록 포털에서 응용 프로그램에 대해 등록 된 리디렉션 Uri 중 하 나와 일치 해야 합니다. |

## <a name="single-sign-out"></a>Egyszeri kijelentkezés

사용자를 `end_session_endpoint`으로 리디렉션하면 Microsoft id 플랫폼 끝점이 브라우저에서 사용자의 세션을 지웁니다. 그러나 사용자는 인증을 위해 Microsoft 계정을 사용 하는 다른 응용 프로그램에 여전히 로그인 되어 있을 수 있습니다. 이러한 응용 프로그램이 사용자에 게 동시에 로그인 할 수 있도록 하기 위해 Microsoft id 플랫폼 끝점은 현재 사용자가 로그인 되어 있는 모든 응용 프로그램의 등록 된 `LogoutUrl`에 HTTP GET 요청을 보냅니다. 응용 프로그램은 사용자를 식별 하 고 `200` 응답을 반환 하는 세션을 지워이 요청에 응답 해야 합니다. 응용 프로그램에서 single sign-on을 지원 하려는 경우 응용 프로그램 코드에 이러한 `LogoutUrl`를 구현 해야 합니다. 앱 등록 포털에서 `LogoutUrl`를 설정할 수 있습니다.

## <a name="protocol-diagram-access-token-acquisition"></a>프로토콜 다이어그램: 액세스 토큰 획득

많은 웹 앱은에서 사용자를 서명 하는 것 뿐만 아니라 OAuth를 사용 하 여 사용자를 대신 하 여 웹 서비스에 액세스 해야 합니다. 이 시나리오에서는 OAuth 권한 부여 코드 흐름을 사용 하는 경우 액세스 토큰을 가져오는 데 사용할 수 있는 권한 부여 코드를 동시에 가져오는 동안 사용자 인증을 위해 Openid connect Connect를 결합 합니다.

Full Openid connect Connect 로그인 및 토큰 획득 흐름은 다음 다이어그램과 유사 하 게 표시 됩니다. 문서의 다음 섹션에서 각 단계에 대해 자세히 설명 합니다.

![Openid connect Connect 프로토콜: 토큰 획득](./media/v2-protocols-oidc/convergence-scenarios-webapp-webapi.svg)

## <a name="get-access-tokens"></a>액세스 토큰 가져오기
액세스 토큰을 얻으려면 로그인 요청을 수정 합니다.

```
// Line breaks are for legibility only.

GET https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e        // Your registered Application ID
&response_type=id_token%20code
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F       // Your registered redirect URI, URL encoded
&response_mode=form_post                              // 'form_post' or 'fragment'
&scope=openid%20                                      // Include both 'openid' and scopes that your app needs  
offline_access%20                                         
https%3A%2F%2Fgraph.microsoft.com%2Fuser.read
&state=12345                                          // Any value, provided by your app
&nonce=678910                                         // Any value, provided by your app
```

> [!TIP]
> 이 요청을 실행 하려면 다음 링크를 클릭 하십시오. 로그인 하면 브라우저가 주소 표시줄의 ID 토큰 및 코드를 사용 하 여 `https://localhost/myapp/`으로 리디렉션됩니다. 이 요청은 데모용 으로만 `response_mode=fragment`를 사용 합니다. `response_mode=form_post`를 사용 하는 것이 좋습니다.
> <a href="https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&response_type=id_token%20code&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F&response_mode=fragment&scope=openid%20offline_access%20https%3A%2F%2Fgraph.microsoft.com%2Fuser.read&state=12345&nonce=678910" target="_blank">https://login.microsoftonline.com/common/oauth2/v2.0/authorize...</a>

Microsoft identity platform 끝점은 요청에 권한 범위를 포함 하 고 `response_type=id_token code`를 사용 하 여 사용자가 `scope` 쿼리 매개 변수에 표시 된 사용 권한에 동의한 확인 합니다. 액세스 토큰을 교환 하기 위해 앱에 인증 코드를 반환 합니다.

### <a name="successful-response"></a>성공적인 응답

`response_mode=form_post`를 사용 하는 성공적인 응답은 다음과 같습니다.

```
POST /myapp/ HTTP/1.1
Host: localhost
Content-Type: application/x-www-form-urlencoded

id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik1uQ19WWmNB...&code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...&state=12345
```

| Paraméter | Leírás |
| --- | --- |
| `id_token` | 앱이 요청한 ID 토큰입니다. ID 토큰을 사용 하 여 사용자의 id를 확인 하 고 사용자와 세션을 시작할 수 있습니다. ID 토큰 및 해당 내용에 대 한 자세한 내용은 [`id_tokens` 참조](id-tokens.md)에서 찾을 수 있습니다. |
| `code` | 앱이 요청한 권한 부여 코드입니다. 앱은 권한 부여 코드를 사용 하 여 대상 리소스에 대 한 액세스 토큰을 요청할 수 있습니다. 권한 부여 코드는 수명이 짧습니다. 일반적으로 인증 코드는 약 10 분 후에 만료 됩니다. |
| `state` | 요청에 state 매개 변수가 포함 되어 있으면 동일한 값이 응답에 표시 되어야 합니다. 앱은 요청 및 응답의 상태 값이 동일한 지 확인 해야 합니다. |

### <a name="error-response"></a>오류 응답

앱이 적절 하 게 처리할 수 있도록 리디렉션 URI에 오류 응답을 보낼 수도 있습니다. 오류 응답은 다음과 같습니다.

```
POST /myapp/ HTTP/1.1
Host: localhost
Content-Type: application/x-www-form-urlencoded

error=access_denied&error_description=the+user+canceled+the+authentication
```

| Paraméter | Leírás |
| --- | --- |
| `error` | 발생 한 오류 유형을 분류 하 고 오류에 대응 하는 데 사용할 수 있는 오류 코드 문자열입니다. |
| `error_description` | 인증 오류의 근본 원인을 식별 하는 데 도움이 될 수 있는 특정 오류 메시지입니다. |

가능한 오류 코드 및 권장 되는 클라이언트 응답에 대 한 설명은 [권한 부여 끝점 오류에 대 한 오류 코드](#error-codes-for-authorization-endpoint-errors)를 참조 하세요.

인증 코드와 ID 토큰이 있는 경우 사용자에 게 로그인 하 고 대신 액세스 토큰을 가져올 수 있습니다. 에서 사용자를 서명 하려면 [설명 된 대로](id-tokens.md#validating-an-id_token)ID 토큰의 유효성을 정확 하 게 확인 해야 합니다. 액세스 토큰을 가져오려면 [OAuth 코드 흐름 설명서](v2-oauth2-auth-code-flow.md#request-an-access-token)에 설명 된 단계를 따르세요.
