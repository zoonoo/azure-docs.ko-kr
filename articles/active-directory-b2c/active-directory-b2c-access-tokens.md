---
title: Azure Active Directory B2C 액세스 토큰-요청 | Microsoft Docs
description: Azure Active Directory B2C에서 액세스 토큰을 요청 하는 방법에 알아봅니다.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/16/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 1a545f1e0fd1360d9147280454fb8b75bf216152
ms.sourcegitcommit: adb6c981eba06f3b258b697251d7f87489a5da33
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/04/2019
ms.locfileid: "66507385"
---
# <a name="request-an-access-token-in-azure-active-directory-b2c"></a>Azure Active Directory B2C에서 액세스 토큰 요청

*액세스 토큰* 사용할 수 있는 Azure Active Directory (Azure AD) B2C에서 Api에 부여 된 권한을 식별 하는 클레임을 포함 합니다. 리소스 서버를 호출할 때 액세스 토큰을 HTTP 요청에 있어야 합니다. 액세스 토큰으로 표시 됩니다 **access_token** Azure AD B2C에서 응답에서 합니다. 

이 문서에서는 웹 응용 프로그램 및 웹 API에 대 한 액세스 토큰을 요청 하는 방법을 보여 줍니다. Azure AD B2C에서 토큰에 대 한 자세한 내용은 참조는 [Azure Active Directory B2C에서 토큰의 개요](active-directory-b2c-reference-tokens.md)합니다.

> [!NOTE]
> **Azure AD B2C에서는 Web API 체인(On-Behalf-Of)을 지원하지 않습니다.** -많은 아키텍처에는 다른 다운스트림 웹 API를 둘 다 Azure AD B2C로 보안을 호출 해야 하는 web API를 포함 합니다. 이 시나리오는 다른 서비스를 호출 하는 web API 백 엔드를 포함 하는 클라이언트에서 일반적입니다. On-Behalf-Of 흐름이라고도 하는 OAuth 2.0 JWT 전달자 자격 증명 권한 부여를 사용하여 이 연결된 웹 API 시나리오를 지원할 수 있습니다. 그러나 On-Behalf-Of 흐름은 현재 Azure AD B2C에 구현되어 있지 않습니다.

## <a name="prerequisites"></a>필수 조건

- 사용자가 애플리케이션에 가입하고 로그인할 수 있는 [사용자 흐름을 만듭니다](tutorial-create-user-flows.md).
- 따라서 아직 수행 하지 않은 경우 [web API 응용 프로그램에 Azure Active Directory B2C 테 넌 트에 추가](add-web-application.md)합니다.

## <a name="scopes"></a>범위

범위에는 보호 된 리소스에 대 한 사용 권한을 관리 하는 방법을 제공 합니다. 클라이언트 응용 프로그램에서 원하는 권한을 지정 해야 하는 액세스 토큰이 요청 되 면 합니다 **범위** 요청의 매개 변수입니다. 예를 들어 지정 하는 **범위 값** 의 `read` 가 있는 API에 대 한 합니다 **앱 ID URI** 의 `https://contoso.onmicrosoft.com/api`, 범위 것 `https://contoso.onmicrosoft.com/api/read`합니다.

범위는 웹 API에서 범위 기반 액세스 제어를 구현하는 데 사용됩니다. 예를 들어 웹 API 사용자가 읽기 및 쓰기 액세스 권한을 모두 갖고 있을 수도 있고, 읽기 권한만 갖고 있을 수도 있습니다. 같은 요청에 여러 권한을 얻으려고 단일에서 항목이 여러 개를 추가할 수 있습니다 **범위** 공백으로 구분 하 여 요청 매개 변수입니다.

다음 예제에서는 URL에 디코딩할 범위를 보여 줍니다.

```
scope=https://contoso.onmicrosoft.com/api/read openid offline_access
```

다음 예제에서는 URL 인코딩된 범위를 보여 줍니다.

```
scope=https%3A%2F%2Fcontoso.onmicrosoft.com%2Fapi%2Fread%20openid%20offline_access
```

클라이언트 응용 프로그램에 부여 된 것 보다 더 많은 범위를 요청 하는 경우 하나 이상의 권한이 부여 되 면 호출이 성공 합니다. 합니다 **scp** 결과 액세스 토큰의 클레임이 성공적으로 부여 된 권한만 채워집니다. OpenID Connect 표준은 몇 가지 특별 한 범위 값을 지정합니다. 다음 범위에 대 한 사용자의 프로필 액세스 권한을 나타냅니다.

- **openid** -ID 토큰을 요청 합니다.
- **offline_access** -새로 고침 토큰을 요청 [인증 코드 흐름](active-directory-b2c-reference-oauth-code.md)합니다.

경우는 **response_type** 에서 매개 변수는 `/authorize` 요청에 포함 됩니다 `token`의 **범위** 매개 변수는 이외의 하나 이상의 리소스 범위를 포함 해야 `openid` 및 `offline_access`부여 된 합니다. 그렇지 않은 경우는 `/authorize` 요청이 실패 합니다.

## <a name="request-a-token"></a>토큰 요청

액세스 토큰을 요청 하려면 권한 부여 코드를 해야 합니다. 다음은 하는 요청의 예는 `/authorize` 권한 부여 코드에 대 한 끝점입니다. 액세스 토큰 사용에 대 한 사용자 지정 도메인을 사용할 수 없습니다. 요청 URL에서 테 넌 트 name.onmicrosoft.com 도메인을 사용 합니다.

다음 예제에서는 다음 값을 바꿉니다.

- `<tenant-name>` - Azure AD B2C 테넌트의 이름.
- `<policy-name>` - 사용자 지정 정책 또는 사용자 흐름의 이름.
- `<application-ID>` 사용자 흐름을 지원 하기 위해 등록 하는 웹 응용 프로그램-응용 프로그램 식별자입니다.
- `<redirect-uri>` - 클라이언트 애플리케이션을 등록할 때 입력한 **리디렉션 URI**.

```
GET https://<tenant-name>.b2clogin.com/tfp/<tenant-name>.onmicrosoft.com/<policy-name>/oauth2/v2.0/authorize?
client_id=<application-ID>
&nonce=anyRandomValue
&redirect_uri=https://jwt.ms
&scope=https://tenant-name>.onmicrosoft.com/api/read
&response_type=code 
```

권한 부여 코드를 사용 하 여 응답이 예와 비슷해야 합니다.

```
https://jwt.ms/?code=eyJraWQiOiJjcGltY29yZV8wOTI1MjAxNSIsInZlciI6IjEuMC...
```

권한 부여 코드를 받은 후에 액세스 토큰 요청에 사용할 수 있습니다.

```
POST <tenant-name>.onmicrosoft.com/oauth2/v2.0/token?p=<policy-name> HTTP/1.1
Host: https://<tenant-name>.b2clogin.com
Content-Type: application/x-www-form-urlencoded

grant_type=authorization_code
&client_id=<application-ID>
&scope=https://<tenant-name>.onmicrosoft.com/api/read
&code=eyJraWQiOiJjcGltY29yZV8wOTI1MjAxNSIsInZlciI6IjEuMC...
&redirect_uri=https://jwt.ms
&client_secret=2hMG2-_:y12n10vwH...
```

다음 응답과 유사한 나타나야 합니다.

```
{
    "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6Ilg1ZVhrN...",
    "token_type": "Bearer",
    "not_before": 1549647431,
    "expires_in": 3600,
    "expires_on": 1549651031,
    "resource": "f2a76e08-93f2-4350-833c-965c02483b11",
    "profile_info": "eyJ2ZXIiOiIxLjAiLCJ0aWQiOiJjNjRhNGY3ZC0zMDkxLTRjNzMtYTcyMi1hM2YwNjk0Z..."
}
```

사용 하는 경우 https://jwt.ms 반환 된 액세스 토큰을 검사 하려면 다음 예와 유사한 페이지가 표시 됩니다 있습니다.

```
{
  "typ": "JWT",
  "alg": "RS256",
  "kid": "X5eXk4xyojNFum1kl2Ytv8dl..."
}.{
  "iss": "https://contoso0926tenant.b2clogin.com/c64a4f7d-3091-4c73-a7.../v2.0/",
  "exp": 1549651031,
  "nbf": 1549647431,
  "aud": "f2a76e08-93f2-4350-833c-965...",
  "oid": "1558f87f-452b-4757-bcd1-883...",
  "sub": "1558f87f-452b-4757-bcd1-883...",
  "name": "David",
  "tfp": "B2C_1_signupsignin1",
  "nonce": "anyRandomValue",
  "scp": "read",
  "azp": "38307aee-303c-4fff-8087-d8d2...",
  "ver": "1.0",
  "iat": 1549647431
}.[Signature]
```

## <a name="next-steps"></a>다음 단계

- 하는 방법을 알아보려면 [Azure AD B2C에서 토큰을 구성 합니다.](configure-tokens.md)
