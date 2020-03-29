---
title: Azure AD B2C (MSAL 안드로이드) | Azure
titleSuffix: Microsoft identity platform
description: Android용 Microsoft 인증 라이브러리(MSAL)에서 Azure AD B2C를 사용할 때의 특정 고려 사항에 대해 알아봅니다. 안드로이드)
services: active-directory
author: brianmel
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 9/18/2019
ms.author: brianmel
ms.reviewer: rapong
ms.custom: aaddev
ms.openlocfilehash: 0998bb04b0dfc69db4696f2e390cfe259eba6718
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76696524"
---
# <a name="use-msal-for-android-with-b2c"></a>B2C와 안드로이드에 대한 MSAL을 사용하여

MSAL(Microsoft 인증 라이브러리)을 사용하면 애플리케이션 개발자가 [Azure AD B2C(Azure Active Directory B2C)](https://docs.microsoft.com/azure/active-directory-b2c/)를 사용하여 소셜 및 로컬 ID로 사용자를 인증할 수 있습니다. Azure AD B2C는 ID 관리 서비스입니다. 이를 사용하여 고객이 애플리케이션을 사용할 때 프로필에 등록, 로그인 및 관리하는 방법을 사용자 지정하고 제어할 수 있습니다.

## <a name="configure-known-authorities-and-redirect-uri"></a>알려진 기관 구성 및 URI 리디렉션

Android용 MSAL에서 B2C 정책(사용자 여정)은 개별 기관으로 구성됩니다.

두 가지 정책이 있는 B2C 응용 프로그램이 주어집니다.
- 가입 / 로그인
    * 호출`B2C_1_SISOPolicy`
- 프로필 편집
    * 호출`B2C_1_EditProfile`

앱의 구성 파일은 두 `authorities`를 선언합니다. 각 정책에 대해 하나씩. 각 `type` 권한의 속성은 . `B2C`

### `app/src/main/res/raw/msal_config.json`
```json
{
    "client_id": "<your_client_id_here>",
    "redirect_uri": "<your_redirect_uri_here>",
    "authorities": [{
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

앱 `redirect_uri` 구성에 등록해야 하며 권한 `AndroidManifest.xml` 부여 코드 권한 [부여 흐름](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-reference-oauth-code)중에 리디렉션을 지원해야 합니다.

## <a name="initialize-ipublicclientapplication"></a>iPublic클라이언트 응용 프로그램 초기화

`IPublicClientApplication`응용 프로그램 구성을 비동기적으로 구문 분석할 수 있도록 팩터리 메서드에 의해 구성됩니다.

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

## <a name="interactively-acquire-a-token"></a>대화식으로 토큰 획득

MSAL을 사용하여 대화식으로 토큰을 `AcquireTokenParameters` 획득하려면 인스턴스를 `acquireToken` 빌드하고 메서드에 공급합니다. 아래 토큰 요청은 `default` 권한을 사용합니다.

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

## <a name="silently-renew-a-token"></a>토큰을 자동으로 갱신

MSAL을 사용하여 자동으로 토큰을 획득하려면 인스턴스를 `AcquireTokenSilentParameters` 빌드하고 메서드에 `acquireTokenSilentAsync` 공급합니다. 메서드와 `acquireToken` 달리 `authority` 토큰을 자동으로 획득하려면 지정해야 합니다.

```java
IMultilpeAccountPublicClientApplication pca = ...; // Initialization not shown
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
            // Token request was unsuccesful, inspect the exception
        }
    })
    .build();

pca.acquireTokenSilentAsync(parameters);
```

## <a name="specify-a-policy"></a>정책 지정

B2C의 정책은 별도의 기관으로 표시되므로 기본값 이외의 정책을 호출하는 것은 `fromAuthority` 생성 `acquireToken` 또는 `acquireTokenSilent` 매개 변수를 생성할 때 절을 지정하여 수행됩니다.  예를 들어:

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

로컬 계정 등록 또는 로그인 사용자 흐름에 **'암호를 잊어버렸습니까?**' 링크를 클릭하도록 요청합니다. 이 링크를 클릭해도 암호 재설정 사용자 흐름이 자동으로 트리거되지는 않습니다.

대신 `AADB2C90118` 오류 코드가 앱에 반환됩니다. 앱은 암호를 재설정하는 특정 사용자 흐름을 실행하여 이 오류 코드를 처리해야 합니다.

암호 재설정 오류 코드를 catch하려면 다음 구현을 다음과 `AuthenticationCallback`같은 구현을 사용할 수 있습니다.

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

## <a name="use-iauthenticationresult"></a>I인증 결과 사용

토큰을 성공적으로 수집하면 `IAuthenticationResult` 개체가 생성됩니다. 여기에는 액세스 토큰, 사용자 클레임 및 메타데이터가 포함됩니다.

### <a name="get-the-access-token-and-related-properties"></a>액세스 토큰 및 관련 속성 받기

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

### <a name="get-the-authorized-account"></a>공인 계정 받기

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

IdToken에서 반환되는 클레임은 MSAL이 아닌 STS(보안 토큰 서비스)에 의해 채워집니다. 사용되는 ID 공급자(IdP)에 따라 일부 클레임이 없을 수 있습니다. 일부 IdP는 현재 클레임을 `preferred_username` 제공하지 않습니다. 이 클레임은 MSAL에서 캐싱에 사용되므로 자리 `MISSING FROM THE TOKEN RESPONSE`표시자 값은 그 자리에서 사용됩니다. B2C IdToken 클레임에 대한 자세한 내용은 [Azure Active Directory B2C의 토큰 개요를](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-reference-tokens#claims)참조하십시오.

## <a name="managing-accounts-and-policies"></a>계정 및 정책 관리

B2C는 각 정책을 별도의 권한으로 취급합니다. 따라서 각 정책에서 반환되는 액세스 토큰, 새로 고침 토큰 및 ID 토큰은 상호 교환할 수 없습니다. 즉, 각 정책은 `IAccount` 토큰을 사용하여 다른 정책을 호출할 수 없는 별도의 개체를 반환합니다.

각 정책은 `IAccount` 각 사용자에 대한 캐시에 를 추가합니다. 사용자가 응용 프로그램에 등록하고 두 개의 정책을 호출하면 두 `IAccount`개의 정책이 있습니다. 캐시에서 이 사용자를 제거하려면 각 `removeAccount()` 정책을 호출해야 합니다.

을 사용하여 정책에 `acquireTokenSilent`대한 토큰을 갱신할 `IAccount` 때 정책의 이전 호출에서 반환된 `AcquireTokenSilentParameters`것과 동일한 을 을 제공합니다. 다른 정책에서 반환된 계정을 제공하면 오류가 발생합니다.

## <a name="next-steps"></a>다음 단계

Azure Active Directory B2C(Azure AD B2C)에 대해 자세히 알아보려면 [Azure Active Directory B2C란 무엇입니까?](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-overview)
