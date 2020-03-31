---
title: 파이썬 ADAL에서 MSAL 마이그레이션 가이드 | Azure
description: Azure Active Directory 인증 라이브러리(ADAL) 파이썬 앱을 파이썬용 MSAL(Microsoft 인증 라이브러리)으로 마이그레이션하는 방법에 대해 알아봅니다.
services: active-directory
titleSuffix: Microsoft identity platform
author: rayluo
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.tgt_pltfrm: Python
ms.workload: identity
ms.date: 11/11/2019
ms.author: rayluo
ms.reviewer: rayluo, nacanuma, twhitney
ms.custom: aaddev
ms.openlocfilehash: fe9dc6c04fe033fd518218d1b5ea971e573405fc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76696561"
---
# <a name="adal-to-msal-migration-guide-for-python"></a>파이썬에 대한 MSAL 마이그레이션 가이드에 ADAL

이 문서에서는 ADAL(Azure Active Directory 인증 라이브러리)을 사용하여 MSAL(Microsoft 인증 라이브러리)을 사용하는 앱을 마이그레이션하는 데 필요한 변경 사항을 간중합니다.

## <a name="difference-highlights"></a>차이 하이라이트

ADAL은 Azure Active Directory(Azure AD) v1.0 끝점에서 작동합니다. MSAL(Microsoft 인증 라이브러리)은 이전에 Azure Active Directory v2.0 끝점으로 알려진 Microsoft ID 플랫폼과 함께 작동합니다. Microsoft ID 플랫폼은 다음과 같은 점에서 Azure AD v1.0과 다릅니다.

지원:
  - 회사 및 학교 계정(Azure AD로 프로비저닝한 계정)
  - 개인 계정(예: Outlook.com 또는 Hotmail.com)
  - Azure AD B2C 제품을 통해 자신의 이메일 또는 소셜 ID(예: LinkedIn, Facebook, Google)를 사용하는 고객

- 표준은 다음과 호환됩니다.
  - 오오스 v2.0
  - 오픈ID 커넥트(OIDC)

자세한 내용은 [Microsoft ID 플랫폼(v2.0) 끝점의 다른 점은 무엇입니까?](https://docs.microsoft.com/azure/active-directory/develop/azure-ad-endpoint-comparison)

### <a name="scopes-not-resources"></a>리소스가 아닌 범위

ADAL 파이썬은 리소스에 대한 토큰을 획득하지만 MSAL 파이썬은 범위에 대한 토큰을 얻습니다. MSAL 파이썬의 API 표면에는 더 이상 리소스 매개 변수가 없습니다. 원하는 사용 권한 및 요청된 리소스를 선언하는 문자열 목록으로 범위를 제공해야 합니다. 범위의 몇 가지 예를 보려면 [Microsoft 그래프의 범위를](https://docs.microsoft.com/graph/permissions-reference)참조하십시오.

### <a name="error-handling"></a>오류 처리

파이썬에 대한 Azure Active Directory 인증 `AdalError` 라이브러리(ADAL)는 예외를 사용하여 문제가 있음을 나타냅니다. 파이썬의 MSAL은 일반적으로 오류 코드를 대신 사용합니다. 자세한 내용은 [파이썬 오류 처리에 대한 MSAL을](https://docs.microsoft.com/azure/active-directory/develop/msal-handling-exceptions?tabs=python)참조하십시오.

### <a name="api-changes"></a>API 변경

다음 표에는 파이썬용 ADAL의 API와 파이썬용 MSAL에서 사용할 API가 나열되어 있습니다.

| 파이썬 API에 대한 ADAL  | 파이썬 API용 MSAL |
| ------------------- | ---------------------------------- |
| [인증 컨텍스트](https://adal-python.readthedocs.io/en/latest/#adal.AuthenticationContext)  | [공용 클라이언트 응용 프로그램 또는 기밀 클라이언트 응용 프로그램](https://msal-python.readthedocs.io/en/latest/#msal.ClientApplication.__init__)  |
| 해당 없음  | [get_authorization_request_url()](https://msal-python.readthedocs.io/en/latest/#msal.ClientApplication.get_authorization_request_url)  |
| [acquire_token_with_authorization_code()](https://adal-python.readthedocs.io/en/latest/#adal.AuthenticationContext.acquire_token_with_authorization_code) | [acquire_token_by_authorization_code()](https://msal-python.readthedocs.io/en/latest/#msal.ClientApplication.acquire_token_by_authorization_code) |
| [acquire_token()](https://adal-python.readthedocs.io/en/latest/#adal.AuthenticationContext.acquire_token) | [acquire_token_silent()](https://msal-python.readthedocs.io/en/latest/#msal.ClientApplication.acquire_token_silent) |
| [acquire_token_with_refresh_token()](https://adal-python.readthedocs.io/en/latest/#adal.AuthenticationContext.acquire_token_with_refresh_token) | 해당 없음 |
| [acquire_user_code()](https://adal-python.readthedocs.io/en/latest/#adal.AuthenticationContext.acquire_user_code) | [initiate_device_flow()](https://msal-python.readthedocs.io/en/latest/#msal.PublicClientApplication.initiate_device_flow) |
| [acquire_token_with_device_code()](https://adal-python.readthedocs.io/en/latest/#adal.AuthenticationContext.acquire_token_with_device_code) 및 [cancel_request_to_get_token_with_device_code()](https://adal-python.readthedocs.io/en/latest/#adal.AuthenticationContext.cancel_request_to_get_token_with_device_code) | [acquire_token_by_device_flow()](https://msal-python.readthedocs.io/en/latest/#msal.PublicClientApplication.acquire_token_by_device_flow) |
| [acquire_token_with_username_password()](https://adal-python.readthedocs.io/en/latest/#adal.AuthenticationContext.acquire_token_with_username_password) | [acquire_token_by_username_password()](https://msal-python.readthedocs.io/en/latest/#msal.PublicClientApplication.acquire_token_by_username_password) |
| [acquire_token_with_client_credentials()](https://adal-python.readthedocs.io/en/latest/#adal.AuthenticationContext.acquire_token_with_client_credentials) 및 [acquire_token_with_client_certificate()](https://adal-python.readthedocs.io/en/latest/#adal.AuthenticationContext.acquire_token_with_client_certificate) | [acquire_token_for_client()](https://msal-python.readthedocs.io/en/latest/#msal.ConfidentialClientApplication.acquire_token_for_client) |
| 해당 없음 | [acquire_token_on_behalf_of()](https://msal-python.readthedocs.io/en/latest/#msal.ConfidentialClientApplication.acquire_token_on_behalf_of) |
| [토큰 캐시()](https://adal-python.readthedocs.io/en/latest/#adal.TokenCache) | [직렬화 가능한 토큰 캐시()](https://msal-python.readthedocs.io/en/latest/#msal.SerializableTokenCache) |
| 해당 없음 | 지속성이 있는 캐시, [MSAL 확장에서](https://github.com/marstr/original-microsoft-authentication-extensions-for-python) 사용 가능 |

## <a name="migrate-existing-refresh-tokens-for-msal-python"></a>MSAL 파이썬에 대한 기존 새로 고침 토큰 마이그레이션

MSAL(Microsoft 인증 라이브러리)은 새로 고침 토큰의 개념을 추상화합니다. MSAL Python은 기본적으로 메모리 내 토큰 캐시를 제공하므로 토큰을 저장, 조회 또는 업데이트할 필요가 없습니다. 또한 새로 고침 토큰은 일반적으로 사용자 개입 없이 업데이트할 수 있기 때문에 로그인 프롬프트가 줄어듭니다. 토큰 캐시에 대한 자세한 내용은 [Python에 대한 MSAL의 사용자 지정 토큰 캐시 직렬화를](msal-python-token-cache-serialization.md)참조하십시오.

다음 코드는 다른 OAuth2 라이브러리에서 관리하는 새로 고침 토큰을 마이그레이션하는 데 도움이 됩니다(ADAL 파이썬을 포함하되 이에 국한되지 않음)는 MSAL for Python에서 관리합니다. 이러한 새로 고침 토큰을 마이그레이션하는 한 가지 이유는 파이썬용 MSAL로 앱을 마이그레이션할 때 기존 사용자가 다시 로그인할 필요가 없도록 하기 위해서입니다.

새로 고침 토큰을 마이그레이션하는 방법은 MSAL for Python을 사용하여 이전 새로 고침 토큰을 사용하여 새 액세스 토큰을 획득하는 것입니다. 새 새로 고침 토큰이 반환되면 파이썬용 MSAL이 캐시에 저장됩니다. 다음은 이 작업을 수행하는 방법의 예입니다.

```python
from msal import PublicClientApplication

def get_preexisting_rt_and_their_scopes_from_elsewhere(...):
    raise NotImplementedError("You will need to implement this by yourself")

app = PublicClientApplication(..., token_cache=...)

for old_rt, old_scope in get_preexisting_rt_and_their_scopes_from_elsewhere(...):
    # Assuming the old scope could be a space-delimited string.
    # MSAL expects a list, like ["scope1", "scope2"].
    scopes = old_scope.split()
        # If your old refresh token came from ADAL for Python, which uses a resource rather than a scope,
        # you need to convert your v1 resource into v2 scopes
        # See https://docs.microsoft.com/azure/active-directory/develop/azure-ad-endpoint-comparison#scopes-not-resources
        # You may be able to append "/.default" to your v1 resource to form a scope
        # See https://docs.microsoft.com/azure/active-directory/develop/v2-permissions-and-consent#the-default-scope

    result = app.client.obtain_token_by_refresh_token(old_rt, scope=scopes)
    # When this call returns the new token(s), a new refresh token is issued by the Microsoft identity platform and MSAL for Python
    # stores it in the token cache.
```

## <a name="next-steps"></a>다음 단계

자세한 내용은 [v1.0 및 v2.0 비교](active-directory-v2-compare.md)를 참조하세요.
