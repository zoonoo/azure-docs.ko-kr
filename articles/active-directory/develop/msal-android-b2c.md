---
title: Azure AD B2C(MSAL Android) | Azure
titleSuffix: Microsoft identity platform
description: Android용 Microsoft 인증 라이브러리(MSAL.Android)가 있는 Azure AD B2C를 사용할 때 특별히 고려할 내용에 관하여 알아봅니다.
services: active-directory
author: iambmelt
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 9/18/2019
ms.author: brianmel
ms.reviewer: rapong
ms.custom: aaddev
ms.openlocfilehash: 1a9b9481d0b4086505bbfd3c2cd654ce228d1ae2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101688878"
---
# <a name="use-msal-for-android-with-b2c"></a>B2C가 있는 Android용 MSAL

MSAL(Microsoft 인증 라이브러리)를 통하여 애플리케이션 개발자는 [Azure Active Directory B2C(Azure AD B2C)](../../active-directory-b2c/index.yml)를 사용하여 소셜 ID와 로컬 ID로 사용자를 인증할 수 있습니다. Azure AD B2C는 ID 관리 서비스입니다. 이 라이브러리를 사용하여 고객이 애플리케이션을 사용할 때 등록, 로그인하고 프로필을 관리하는 방법을 사용자 지정하고 제어할 수 있습니다.

## <a name="choosing-a-compatible-authorization_user_agent"></a>호환되는 authorization_user_agent 선택
B2C ID 관리 시스템은 Google, Facebook, Twitter, Amazon과 같은 여러 소셜 계정 공급자에서 인증을 지원합니다. 앱에서 이러한 계정 유형을 지원하려면 일부 외부 ID 공급자에서 웹 보기 기반 인증의 사용이 제한되므로 매니페스트의 [`authorization_user_agent`](msal-configuration.md#authorization_user_agent)를 지정할 때 `DEFAULT` 또는 `BROWSER` 값을 사용하도록 MSAL 퍼블릭 클라이언트 애플리케이션을 구성하는 것이 좋습니다.

## <a name="configure-known-authorities-and-redirect-uri"></a>알려진 인증 기관 및 리디렉션 URI 구성

Android용 MSAL에서 B2C 정책(사용자 경험)은 개별 인증 기관으로 구성됩니다.

두 가지 정책이 있는 B2C 애플리케이션이 제공됩니다.
- 등록/ 로그인
    * `B2C_1_SISOPolicy`라고 함
- 프로필 편집
    * `B2C_1_EditProfile`라고 함

앱에 대한 구성 파일은 두 개의 `authorities`를 선언합니다. 정책당 하나입니다. 인증 기관별 `type` 속성은 `B2C`입니다.

>참고: B2C 애플리케이션에 대하여 `account_mode`는 반드시 **다중** 으로 설정하여야 합니다. [다중 계정 퍼블릭 클라이언트 앱](./single-multi-account.md#multiple-account-public-client-application)에 대한 자세한 내용은 설명서를 참조하세요.

### `app/src/main/res/raw/msal_config.json`

```json
{
  "client_id": "<your_client_id_here>",
  "redirect_uri": "<your_redirect_uri_here>",
  "account_mode" : "MULTIPLE",
  "authorization_user_agent" : "DEFAULT",
  "authorities": [
    {
      "type": "B2C",
      "authority_url": "https://contoso.b2clogin.com/tfp/contoso.onmicrosoft.com/B2C_1_SISOPolicy/",
      "default": true
    },
    {
      "type": "B2C",
      "authority_url": "https://contoso.b2clogin.com/tfp/contoso.onmicrosoft.com/B2C_1_EditProfile/"
    }
  ]
}
```

`redirect_uri`가 앱 구성에 등록되어야 하며 `AndroidManifest.xml`에도 등록되어 [인증 코드 부여 흐름](../../active-directory-b2c/authorization-code-flow.md) 중에 리디렉션을 지원하여야 합니다.

## <a name="initialize-ipublicclientapplication"></a>IPublicClientApplication 초기화

팩터리 메서드는 애플리케이션 구성을 비동기적으로 구문 분석할 수 있도록 `IPublicClientApplication`을 구성합니다.

```java
PublicClientApplication.createMultipleAccountPublicClientApplication(
    context, // Your application Context
    R.raw.msal_config, // Id of app JSON config
    new IPublicClientApplication.ApplicationCreatedListener() {
        @Override
        public void onCreated(IMultipleAccountPublicClientApplication pca) {
            // Application has been initialized.
        }

        @Override
        public void onError(MsalException exception) {
            // Application could not be created.
            // Check Exception message for details.
        }
    }
);
```

## <a name="interactively-acquire-a-token"></a>대화형으로 토큰 획득

MSAL을 통하여 대화형으로 토큰을 획득하려면 `AcquireTokenParameters` 인스턴스를 빌드하여 `acquireToken` 메서드에 제공합니다. 아래의 토큰 요청은 `default` 권한을 사용합니다.

```java
IMultipleAccountPublicClientApplication pca = ...; // Initialization not shown

AcquireTokenParameters parameters = new AcquireTokenParameters.Builder()
    .startAuthorizationFromActivity(activity)
    .withScopes(Arrays.asList("https://contoso.onmicrosoft.com/contosob2c/read")) // Provide your registered scope here
    .withPrompt(Prompt.LOGIN)
    .callback(new AuthenticationCallback() {
        @Override
        public void onSuccess(IAuthenticationResult authenticationResult) {
            // Token request was successful, inspect the result
        }

        @Override
        public void onError(MsalException exception) {
            // Token request was unsuccessful, inspect the exception
        }

        @Override
        public void onCancel() {
            // The user cancelled the flow
        }
    }).build();

pca.acquireToken(parameters);
```

## <a name="silently-renew-a-token"></a>토큰 자동 갱신

MSAL을 통하여 자동으로 토큰을 획득하려면 `AcquireTokenSilentParameters` 인스턴스를 빌드하여 `acquireTokenSilentAsync` 메서드에 제공합니다. `acquireToken` 메서드와는 달리 `authority`는 자동으로 토큰을 획득하려면 반드시 지정하여야 합니다.

```java
IMultipleAccountPublicClientApplication pca = ...; // Initialization not shown
AcquireTokenSilentParameters parameters = new AcquireTokenSilentParameters.Builder()
    .withScopes(Arrays.asList("https://contoso.onmicrosoft.com/contosob2c/read")) // Provide your registered scope here
    .forAccount(account)
    // Select a configured authority (policy), mandatory for silent auth requests
    .fromAuthority("https://contoso.b2clogin.com/tfp/contoso.onmicrosoft.com/B2C_1_SISOPolicy/")
    .callback(new SilentAuthenticationCallback() {
        @Override
        public void onSuccess(IAuthenticationResult authenticationResult) {
            // Token request was successful, inspect the result
        }

        @Override
        public void onError(MsalException exception) {
            // Token request was unsuccessful, inspect the exception
        }
    })
    .build();

pca.acquireTokenSilentAsync(parameters);
```

## <a name="specify-a-policy"></a>정책 지정

B2C 정책은 개별 인증 기관으로 표시되기 때문에, `acquireToken` 또는 `acquireTokenSilent` 매개 변수를 구성할 때 `fromAuthority` 절을 지정하여 기본값 이외의 정책을 호출합니다.  예를 들면 다음과 같습니다.

```java
AcquireTokenParameters parameters = new AcquireTokenParameters.Builder()
    .startAuthorizationFromActivity(activity)
    .withScopes(Arrays.asList("https://contoso.onmicrosoft.com/contosob2c/read")) // Provide your registered scope here
    .withPrompt(Prompt.LOGIN)
    .callback(...) // provide callback here
    .fromAuthority("<url_of_policy_defined_in_configuration_json>")
    .build();
```

## <a name="handle-password-change-policies"></a>암호 변경 정책 처리

로컬 계정 등록이나 로그인 사용자 흐름에 ‘**암호 분실?** ’을 표시합니다. 링크를 사용하여 비용 없이 계정을 만들 수 있습니다. 이 링크를 클릭해도 암호 재설정 사용자 흐름이 자동으로 트리거되지는 않습니다.

대신 `AADB2C90118` 오류 코드가 앱에 반환됩니다. 앱은 암호를 재설정하는 특정 사용자 흐름을 실행하여 해당 오류 코드를 처리하여야 합니다.

암호 재설정 오류 코드를 잡아내기 위하여서는 다음을 `AuthenticationCallback`에서 구현할 수 있습니다.:

```java
new AuthenticationCallback() {

    @Override
    public void onSuccess(IAuthenticationResult authenticationResult) {
        // ..
    }

    @Override
    public void onError(MsalException exception) {
        final String B2C_PASSWORD_CHANGE = "AADB2C90118";

        if (exception.getMessage().contains(B2C_PASSWORD_CHANGE)) {
            // invoke password reset flow
        }
    }

    @Override
    public void onCancel() {
        // ..
    }
}
```

## <a name="use-iauthenticationresult"></a>IAuthenticationResult 사용

성공적인 토큰 획득은 `IAuthenticationResult` 개체로 나타납니다. 여기에는 액세스 토큰, 사용자 클레임, 메타데이터가 들어 있습니다.

### <a name="get-the-access-token-and-related-properties"></a>액세스 토큰 및 관련 속성 가져오기

```java
// Get the raw bearer token
String accessToken = authenticationResult.getAccessToken();

// Get the scopes included in the access token
String[] accessTokenScopes = authenticationResult.getScope();

// Gets the access token's expiry
Date expiry = authenticationResult.getExpiresOn();

// Get the tenant for which this access token was issued
String tenantId = authenticationResult.getTenantId();
```

### <a name="get-the-authorized-account"></a>인증된 계정 가져오기

```java
// Get the account from the result
IAccount account = authenticationResult.getAccount();

// Get the id of this account - note for B2C, the policy name is a part of the id
String id = account.getId();

// Get the IdToken Claims
//
// For more information about B2C token claims, see reference documentation
// https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-reference-tokens
Map<String, ?> claims = account.getClaims();

// Get the 'preferred_username' claim through a convenience function
String username = account.getUsername();

// Get the tenant id (tid) claim through a convenience function
String tenantId = account.getTenantId();
```

### <a name="idtoken-claims"></a>IdToken 클레임

IdToken에서 반환된 클레임은 MSAL이 아니라 STS(보안 토큰 서비스)가 채웁니다. 사용된 IdP(ID 공급자)에 따라 어떤 클레임은 없을 수도 있습니다. 현재, `preferred_username` 클레임을 제공하지 않는 IdP도 있습니다. 캐싱을 위하여 MSAL이 해당 클레임을 사용하기 때문에 자리 표시자 값인 `MISSING FROM THE TOKEN RESPONSE`가 그 자리에 사용됩니다. B2C IdToken 클레임에 대한 자세한 내용은 [Azure Active Directory B2C의 토큰 개요](../../active-directory-b2c/tokens-overview.md#claims)를 확인하세요.

## <a name="managing-accounts-and-policies"></a>계정 및 정책 관리

B2C에서는 개별 정책을 개별 인증 기관으로 취급합니다. 따라서, 정책별로 반환된 해당 액세스 토큰, 새로 고침 토큰, ID 토큰은 교환할 수 없습니다. 이는 각 정책이 다른 정책을 호출하는 데 이용할 수 없는 토큰을 가지고 있는 개별 `IAccount` 개체를 반환한다는 의미입니다.

정책마다 사용자 각각의 캐시에 `IAccount`를 추가합니다. 사용자 한 명이 애플리케이션에 로그인하여 두 개의 정책을 호출하는 경우, `IAccount` 두 개를 가지게 됩니다. 캐시에서 해당 사용자를 제거하려면 정책별로 `removeAccount()`를 호출하여야 합니다.

`acquireTokenSilent`를 사용하여 어느 정책에 대한 토큰을 갱신하는 경우, 해당 정책이 이전에 호출하여 반환된 것과 동일한 `IAccount`를 `AcquireTokenSilentParameters`에 제공합니다. 다른 정책이 반환한 계정을 제공하면 오류가 발생합니다.

## <a name="next-steps"></a>다음 단계

Azure Active Directory B2C(Azure AD B2C)에 대한 자세한 내용은 [Azure Active Directory B2C란 무엇입니까?](../../active-directory-b2c/overview.md)에서 알아보세요.
