---
title: 액세스 토큰 요청 - Azure Active Directory B2C | 마이크로 소프트 문서
description: Azure Active Directory B2C에서 액세스 토큰을 요청하는 방법을 알아봅니다.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/16/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 8358d3378ea892ebeef653bcb51243c9f1aa0b8d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79259774"
---
# <a name="request-an-access-token-in-azure-active-directory-b2c"></a>Azure Active Directory B2C에서 액세스 토큰 요청

*액세스 토큰에는* Azure Active Directory B2C(Azure AD B2C)에서 API에 대한 부여된 권한을 식별하는 데 사용할 수 있는 클레임이 포함되어 있습니다. 리소스 서버를 호출할 때 HTTP 요청에 액세스 토큰이 있어야 합니다. 액세스 토큰은 Azure AD B2C의 응답에서 **access_token** 것으로 표시됩니다.

이 문서에서는 웹 응용 프로그램 및 웹 API에 대한 액세스 토큰을 요청하는 방법을 보여 주며 이 문서에서는 Azure AD B2C의 토큰에 대한 자세한 내용은 [Azure Active Directory B2C의 토큰 개요를](tokens-overview.md)참조하십시오.

> [!NOTE]
> **Azure AD B2C에서는 Web API 체인(On-Behalf-Of)을 지원하지 않습니다.** - 많은 아키텍처에는 Azure AD B2C에서 보호되는 다른 다운스트림 웹 API를 호출해야 하는 웹 API가 포함되어 있습니다. 이 시나리오는 웹 API 백 엔드가 있는 클라이언트에서 일반적이며, 이 경우 다른 서비스를 호출합니다. On-Behalf-Of 흐름이라고도 하는 OAuth 2.0 JWT 전달자 자격 증명 권한 부여를 사용하여 이 연결된 웹 API 시나리오를 지원할 수 있습니다. 그러나 On-Behalf-Of 흐름은 현재 Azure AD B2C에 구현되어 있지 않습니다.

## <a name="prerequisites"></a>사전 요구 사항

- 사용자가 애플리케이션에 가입하고 로그인할 수 있는 [사용자 흐름을 만듭니다](tutorial-create-user-flows.md).
- 아직 수행하지 않은 경우 [Azure Active Directory B2C 테넌트에 웹 API 응용 프로그램을 추가합니다.](add-web-application.md)

## <a name="scopes"></a>범위

범위는 보호된 리소스에 대한 사용 권한을 관리하는 방법을 제공합니다. 액세스 토큰이 요청되면 클라이언트 응용 프로그램은 요청의 **범위** 매개 변수에 원하는 권한을 지정해야 합니다. 예를 들어 `https://contoso.onmicrosoft.com/api`의 App ID `read` **URI가** 있는 API의 **범위 값을** 지정하려면 `https://contoso.onmicrosoft.com/api/read`의 범위가 됩니다.

범위는 웹 API에서 범위 기반 액세스 제어를 구현하는 데 사용됩니다. 예를 들어 웹 API 사용자가 읽기 및 쓰기 액세스 권한을 모두 갖고 있을 수도 있고, 읽기 권한만 갖고 있을 수도 있습니다. 동일한 요청에서 여러 권한을 획득하려면 공백으로 구분된 요청의 단일 **범위** 매개 변수에 여러 항목을 추가할 수 있습니다.

다음 예제에서는 URL에서 디코딩된 범위를 보여 주십니다.

```
scope=https://contoso.onmicrosoft.com/api/read openid offline_access
```

다음 예제에서는 URL에서 인코딩된 범위를 보여 주십니다.

```
scope=https%3A%2F%2Fcontoso.onmicrosoft.com%2Fapi%2Fread%20openid%20offline_access
```

클라이언트 응용 프로그램에 대해 부여된 것보다 더 많은 범위를 요청하는 경우 하나 이상의 권한이 부여된 경우 호출이 성공합니다. 결과 액세스 토큰의 **scp** 클레임은 성공적으로 부여된 사용 권한으로만 채워집니다. OpenID Connect 표준은 몇 가지 특수 범위 값을 지정합니다. 다음 범위는 사용자의 프로필에 액세스할 수 있는 권한을 나타냅니다.

- **openid** - ID 토큰을 요청합니다.
- **offline_access** - [Auth Code 흐름을](authorization-code-flow.md)사용하여 새로 고침 토큰을 요청합니다.

`/authorize` 요청의 `token` **response_type** 매개 변수가 포함된 경우 **scope** 매개 변수에는 `openid` 하나 `offline_access` 이상의 리소스 범위가 포함되어야 하며 허용됩니다. 그렇지 않으면 `/authorize` 요청이 실패합니다.

## <a name="request-a-token"></a>토큰 요청

액세스 토큰을 요청하려면 권한 부여 코드가 필요합니다. 다음은 권한 부여 코드에 `/authorize` 대한 끝점에 대한 요청의 예입니다. 사용자 지정 도메인은 액세스 토큰과 함께 사용할 수 없습니다. 요청 URL에서 tenant-name.onmicrosoft.com 도메인을 사용합니다.

다음 예제에서는 다음 값을 바꿉니다.

- `<tenant-name>` - Azure AD B2C 테넌트의 이름.
- `<policy-name>` - 사용자 지정 정책 또는 사용자 흐름의 이름.
- `<application-ID>`- 사용자 흐름을 지원하기 위해 등록 한 웹 응용 프로그램의 응용 프로그램 식별자입니다.
- `<redirect-uri>` - 클라이언트 애플리케이션을 등록할 때 입력한 **리디렉션 URI**.

```HTTP
GET https://<tenant-name>.b2clogin.com/tfp/<tenant-name>.onmicrosoft.com/<policy-name>/oauth2/v2.0/authorize?
client_id=<application-ID>
&nonce=anyRandomValue
&redirect_uri=https://jwt.ms
&scope=https://<tenant-name>.onmicrosoft.com/api/read
&response_type=code
```

권한 부여 코드에 대한 응답은 이 예제와 유사해야 합니다.

```
https://jwt.ms/?code=eyJraWQiOiJjcGltY29yZV8wOTI1MjAxNSIsInZlciI6IjEuMC...
```

권한 부여 코드를 성공적으로 받은 후 액세스 토큰을 요청하는 데 사용할 수 있습니다.

```HTTP
POST <tenant-name>.onmicrosoft.com/oauth2/v2.0/token?p=<policy-name> HTTP/1.1
Host: <tenant-name>.b2clogin.com
Content-Type: application/x-www-form-urlencoded

grant_type=authorization_code
&client_id=<application-ID>
&scope=https://<tenant-name>.onmicrosoft.com/api/read
&code=eyJraWQiOiJjcGltY29yZV8wOTI1MjAxNSIsInZlciI6IjEuMC...
&redirect_uri=https://jwt.ms
&client_secret=2hMG2-_:y12n10vwH...
```

다음 응답과 비슷한 항목이 표시됩니다.

```JSON
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

반환된 https://jwt.ms 액세스 토큰을 검사하는 데 사용할 때 다음 예제와 비슷한 것이 표시됩니다.

```JSON
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

- [Azure AD B2C에서 토큰을 구성하는](configure-tokens.md) 방법에 대해 알아보기
