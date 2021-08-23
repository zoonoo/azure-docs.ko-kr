---
title: Python ADAL에서 MSAL 마이그레이션 지침 | Azure
titleSuffix: Microsoft identity platform
description: ADAL(Azure Active Directory 인증 라이브러리) Python 앱을 Python용 MSAL(Microsoft 인증 라이브러리)로 마이그레이션하는 방법에 대해 알아봅니다.
services: active-directory
author: rayluo
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.tgt_pltfrm: Python
ms.workload: identity
ms.date: 11/11/2019
ms.author: rayluo
ms.reviewer: marsma, rayluo, nacanuma
ms.custom: aaddev, devx-track-python, has-adal-ref
ms.openlocfilehash: e8e579654e03b1c4ec77c29ee4852f1c820b42cf
ms.sourcegitcommit: 34aa13ead8299439af8b3fe4d1f0c89bde61a6db
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/18/2021
ms.locfileid: "122568079"
---
# <a name="adal-to-msal-migration-guide-for-python"></a>Python용 ADAL에서 MSAL 마이그레이션 가이드

이 문서에서는 MSAL(Microsoft Authentication Library)을 사용하도록 ADAL(Azure Active Directory 인증 라이브러리)을 사용하는 앱을 마이그레이션하기 위해 변경해야 하는 사항을 강조합니다.

MSAL에 대해 자세히 알아보고 [Microsoft 인증 라이브러리 개요](msal-overview.md)로 시작할 수 있습니다.

## <a name="difference-highlights"></a>주된 차이점

ADAL은 Azure AD(Azure Active Directory) v1.0 엔드포인트에서 작동합니다. MSAL(Microsoft 인증 라이브러리)은 Microsoft ID 플랫폼(이전의 Azure Active Directory v2.0 엔드포인트)에서 작동합니다. Microsoft ID 플랫폼과 Azure AD v1.0의 차이점은 다음과 같습니다.

지원:
  - 회사 및 학교 계정(Azure AD로 프로비저닝한 계정)
  - 개인 계정(예: Outlook.com 또는 Hotmail.com)
  - Azure AD B2C 제품을 통해 자신의 이메일 또는 소셜 ID(예: LinkedIn, Facebook, Google)를 사용하는 고객

- 호환되는 표준입니다.
  - OAuth v2.0
  - OpenID Connect(OIDC)

자세한 내용은 [Microsoft ID 플랫폼은 무엇이 다른가요?](../azuread-dev/azure-ad-endpoint-comparison.md)를 참조하세요.

### <a name="scopes-not-resources"></a>리소스가 아닌 범위

ADAL Python은 리소스용 토큰을 획득하지만 MSAL Python은 범위 토큰을 획득합니다. MSAL Python의 API 표면에는 이제 리소스 매개 변수가 없습니다. 필요한 사용 권한 및 요청된 리소스를 선언하는 문자열의 목록으로 범위를 제공해야 합니다. 범위에 대한 몇 가지 예시를 보려면 [Microsoft Graph의 범위](/graph/permissions-reference)를 참조하세요.

리소스에 `/.default` 범위 접미사를 추가하여 v.1.0 엔드포인트(ADAL)에서 MSAL(Microsoft ID 플랫폼)로 앱을 마이그레이션할 수 있습니다. 예를 들어 `https://graph.microsoft.com`의 리소스 값에 해당하는 범위 값은 `https://graph.microsoft.com/.default`입니다.  리소스가 URL 형식이 아닌 `XXXXXXXX-XXXX-XXXX-XXXXXXXXXXXX` 형식의 리소스 ID인 경우에서 범위 값을 `XXXXXXXX-XXXX-XXXX-XXXXXXXXXXXX/.default`로 계속 사용할 수 있습니다.

다양한 범위 형식에 대한 자세한 내용은 [Microsoft ID 플랫폼의 사용 권한 및 동의](./v2-permissions-and-consent.md) 및 [v1.0 토큰을 수락하는 웹 API에 대한 범위](./msal-v1-app-scopes.md)를 참조하세요.

### <a name="error-handling"></a>오류 처리

Python용 Azure ADAL(Active Directory 인증 라이브러리)은 예외 `AdalError`를 사용해 문제가 있음을 표시합니다. Python용 MSAL은 대신 일반적으로 오류 코드를 사용합니다. 자세한 내용은 [Python용 MSAL 오류 처리](msal-error-handling-python.md)를 참조하세요.

### <a name="api-changes"></a>API 변경 내용

다음 표에서는 Python용 ADAL의 API와 Python용 MSAL에서 사용할 API를 나열합니다.

| Python API용 ADAL  | Python API용 MSAL |
| ------------------- | ---------------------------------- |
| [AuthenticationContext](https://adal-python.readthedocs.io/en/latest/#adal.AuthenticationContext)  | [PublicClientApplication](https://msal-python.readthedocs.io/en/latest/#msal.PublicClientApplication.__init__) 또는 [ConfidentialClientApplication](https://msal-python.readthedocs.io/en/latest/#msal.ConfidentialClientApplication.__init__)  |
| 해당 없음  | [PublicClientApplication.acquire_token_interactive()](https://msal-python.readthedocs.io/en/latest/#msal.PublicClientApplication.acquire_token_interactive)  |
| 해당 없음  | [ConfidentialClientApplication.initiate_auth_code_flow()](https://msal-python.readthedocs.io/en/latest/#msal.ConfidentialClientApplication.initiate_auth_code_flow)  |
| [acquire_token_with_authorization_code()](https://adal-python.readthedocs.io/en/latest/#adal.AuthenticationContext.acquire_token_with_authorization_code) | [ConfidentialClientApplication.acquire_token_by_auth_code_flow()](https://msal-python.readthedocs.io/en/latest/#msal.ConfidentialClientApplication.acquire_token_by_auth_code_flow) |
| [acquire_token()](https://adal-python.readthedocs.io/en/latest/#adal.AuthenticationContext.acquire_token) | [PublicClientApplication.acquire_token_silent()](https://msal-python.readthedocs.io/en/latest/#msal.PublicClientApplication.acquire_token_silent) 또는 [ConfidentialClientApplication.acquire_token_silent()](https://msal-python.readthedocs.io/en/latest/#msal.ConfidentialClientApplication.acquire_token_silent) |
| [acquire_token_with_refresh_token()](https://adal-python.readthedocs.io/en/latest/#adal.AuthenticationContext.acquire_token_with_refresh_token) | 이 두 도우미는 [마이그레이션](#migrate-existing-refresh-tokens-for-msal-python) 동안에만 사용됩니다. [PublicClientApplication.acquire_token_by_refresh_token()](https://msal-python.readthedocs.io/en/latest/#msal.PublicClientApplication.acquire_token_by_refresh_token) 또는 [ConfidentialClientApplication.acquire_token_by_refresh_token()](https://msal-python.readthedocs.io/en/latest/#msal.ConfidentialClientApplication.acquire_token_by_refresh_token) |
| [acquire_user_code()](https://adal-python.readthedocs.io/en/latest/#adal.AuthenticationContext.acquire_user_code) | [initiate_device_flow()](https://msal-python.readthedocs.io/en/latest/#msal.PublicClientApplication.initiate_device_flow) |
| [acquire_token_with_device_code ()](https://adal-python.readthedocs.io/en/latest/#adal.AuthenticationContext.acquire_token_with_device_code) 및 [cancel_request_to_get_token_with_device_code ()](https://adal-python.readthedocs.io/en/latest/#adal.AuthenticationContext.cancel_request_to_get_token_with_device_code) | [acquire_token_by_device_flow()](https://msal-python.readthedocs.io/en/latest/#msal.PublicClientApplication.acquire_token_by_device_flow) |
| [acquire_token_with_username_password()](https://adal-python.readthedocs.io/en/latest/#adal.AuthenticationContext.acquire_token_with_username_password) | [acquire_token_by_username_password()](https://msal-python.readthedocs.io/en/latest/#msal.PublicClientApplication.acquire_token_by_username_password) |
| [acquire_token_with_client_credentials ()](https://adal-python.readthedocs.io/en/latest/#adal.AuthenticationContext.acquire_token_with_client_credentials) 및 [acquire_token_with_client_certificate ()](https://adal-python.readthedocs.io/en/latest/#adal.AuthenticationContext.acquire_token_with_client_certificate) | [acquire_token_for_client()](https://msal-python.readthedocs.io/en/latest/#msal.ConfidentialClientApplication.acquire_token_for_client) |
| 해당 없음 | [acquire_token_on_behalf_of()](https://msal-python.readthedocs.io/en/latest/#msal.ConfidentialClientApplication.acquire_token_on_behalf_of) |
| [TokenCache()](https://adal-python.readthedocs.io/en/latest/#adal.TokenCache) | [SerializableTokenCache()](https://msal-python.readthedocs.io/en/latest/#msal.SerializableTokenCache) |
| 해당 없음 | [MSAL 확장](https://github.com/marstr/original-microsoft-authentication-extensions-for-python)에서 사용할 수 있는 지속성이 있는 캐시 |

## <a name="migrate-existing-refresh-tokens-for-msal-python"></a>MSAL Python용 기존 새로 고침 토큰 마이그레이션

MSAL(Microsoft 인증 라이브러리)은 새로 고침 토큰의 개념을 추출합니다. MSAL Python은 새로 고침 토큰을 저장, 조회 또는 업데이트할 필요가 없도록 기본적으로 메모리 내에서 토큰 캐시를 제공합니다. 사용자 개입 없이 새로 고침 토큰을 일반적으로 업데이트할 수 있으므로 사용자가 로그인 프롬프트를 볼 일도 적습니다. 토큰 캐시에 대한 자세한 내용은 [Python용 MSAL의 사용자 지정 토큰 캐시](msal-python-token-cache-serialization.md)를 참조하세요.

다음 코드는 다른 OAuth2 라이브러리에서 관리하는 새로 고침 토큰을 마이그레이션하는 데 도움이 됩니다(ADAL Python을 포함하지만 이에 국한되지는 않음). 해당 새로 고침 토큰을 마이그레이션하는 한 가지 이유는 앱을 Python용 MSAL로 마이그레이션할 때 기존 사용자가 다시 로그인해야 하는 경우를 방지하기 위해서입니다.

Python용 MSAL을 써서 이전 새로 고침 토큰을 사용한 새 액세스 토큰을 획득하는 방법으로 새로 고침 토큰을 마이그레이션할 수 있습니다. 새로운 새로 고침 토큰이 반환되면 Python용 MSAL이 캐시에 이를 저장합니다.
MSAL Python 1.3.0 이후 이 목적으로 사용하라고 MSAL 내에서 API를 제공합니다.
[MSAL Python을 사용하여 새로 고침 토큰을 마이그레이션하는 완료된 샘플](https://github.com/AzureAD/microsoft-authentication-library-for-python/blob/1.3.0/sample/migrate_rt.py#L28-L67)에서 인용한 다음 코드 조각을 참조하세요.

```python
import msal
def get_preexisting_rt_and_their_scopes_from_elsewhere():
    # Maybe you have an ADAL-powered app like this
    #   https://github.com/AzureAD/azure-activedirectory-library-for-python/blob/1.2.3/sample/device_code_sample.py#L72
    # which uses a resource rather than a scope,
    # you need to convert your v1 resource into v2 scopes
    # See https://docs.microsoft.com/azure/active-directory/azuread-dev/azure-ad-endpoint-comparison#scopes-not-resources
    # You may be able to append "/.default" to your v1 resource to form a scope
    # See https://docs.microsoft.com/azure/active-directory/develop/v2-permissions-and-consent#the-default-scope

    # Or maybe you have an app already talking to the Microsoft identity platform,
    # powered by some 3rd-party auth library, and persist its tokens somehow.

    # Either way, you need to extract RTs from there, and return them like this.
    return [
        ("old_rt_1", ["scope1", "scope2"]),
        ("old_rt_2", ["scope3", "scope4"]),
        ]


# We will migrate all the old RTs into a new app powered by MSAL
app = msal.PublicClientApplication(
    "client_id", authority="...",
    # token_cache=...  # Default cache is in memory only.
                       # You can learn how to use SerializableTokenCache from
                       # https://msal-python.readthedocs.io/en/latest/#msal.SerializableTokenCache
    )

# We choose a migration strategy of migrating all RTs in one loop
for old_rt, scopes in get_preexisting_rt_and_their_scopes_from_elsewhere():
    result = app.acquire_token_by_refresh_token(old_rt, scopes)
    if "error" in result:
        print("Discarding unsuccessful RT. Error: ", json.dumps(result, indent=2))

print("Migration completed")
```


## <a name="next-steps"></a>다음 단계

자세한 내용은 [v1.0 및 v2.0 비교](../azuread-dev/azure-ad-endpoint-comparison.md)를 참조하세요.
