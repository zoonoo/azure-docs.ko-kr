---
title: Azure AD B2C (MSAL Android) | Microsoft
titleSuffix: Microsoft identity platform
description: Microsoft Authentication Library for Android (MSAL)에서 Azure AD B2C를 사용 하는 경우의 특정 고려 사항에 대해 알아봅니다. 용
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "76696524"
---
# <a name="use-msal-for-android-with-b2c"></a>B2C와 함께 Android 용 MSAL 사용

MSAL(Microsoft 인증 라이브러리)을 사용하면 애플리케이션 개발자가 [Azure AD B2C(Azure Active Directory B2C)](https://docs.microsoft.com/azure/active-directory-b2c/)를 사용하여 소셜 및 로컬 ID로 사용자를 인증할 수 있습니다. Azure AD B2C는 ID 관리 서비스입니다. 이를 통해 고객이 응용 프로그램을 사용 하 여 등록, 로그인 및 프로필을 관리 하는 방법을 사용자 지정 하 고 제어할 수 있습니다.

## <a name="configure-known-authorities-and-redirect-uri"></a>알려진 인증 기관 및 리디렉션 URI 구성

Android 용 MSAL에서 B2C 정책 (사용자 경험)은 개별 기관으로 구성 됩니다.

두 개의 정책이 있는 B2C 응용 프로그램을 제공 합니다.
- 등록/로그인
    * 이름의`B2C_1_SISOPolicy`
- 프로필 편집
    * 이름의`B2C_1_EditProfile`

앱에 대 한 구성 파일은 2 `authorities`를 선언 합니다. 각 정책에 대해 하나씩입니다. 각 `type` 기관의 속성은 `B2C`입니다.

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

는 `redirect_uri` 앱 구성에 등록 되어 있어야 하며, `AndroidManifest.xml` [인증 코드 부여 흐름](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-reference-oauth-code)동안 리디렉션을 지원 하기 위해에도 있어야 합니다.

## <a name="initialize-ipublicclientapplication"></a>IPublicClientApplication 초기화

`IPublicClientApplication`는 팩터리 메서드에서 생성 되어 응용 프로그램 구성을 비동기적으로 구문 분석할 수 있도록 합니다.

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

MSAL을 사용 하 여 대화형으로 토큰을 가져오려면 `AcquireTokenParameters` 인스턴스를 빌드하고 `acquireToken` 메서드에 제공 합니다. 아래 토큰 요청은 `default` 권한을 사용 합니다.

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

MSAL을 사용 하 여 토큰을 자동으로 획득 `AcquireTokenSilentParameters` 하려면 인스턴스를 빌드하여 `acquireTokenSilentAsync` 메서드에 제공 합니다. `acquireToken` 메서드와 달리 토큰을 자동 `authority` 으로 얻으려면를 지정 해야 합니다.

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

B2C의 정책은 별도의 인증 기관으로 표시 되기 때문에 또는 `fromAuthority` `acquireToken` `acquireTokenSilent` 매개 변수를 생성할 때 절을 지정 하 여 기본값 이외의 정책을 호출 하는 것이 좋습니다.  예를 들면 다음과 같습니다.

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

로컬 계정 등록 또는 로그인 사용자 흐름에서 '**암호 잊음?**'를 표시 합니다. 링크를 클릭하도록 요청합니다. 이 링크를 클릭해도 암호 재설정 사용자 흐름이 자동으로 트리거되지는 않습니다.

대신 `AADB2C90118` 오류 코드가 앱에 반환됩니다. 앱은 암호를 재설정 하는 특정 사용자 흐름을 실행 하 여이 오류 코드를 처리 해야 합니다.

암호 다시 설정 오류 코드를 catch 하기 위해 내에서 다음 구현을 사용할 수 있습니다 `AuthenticationCallback`.

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

토큰 획득이 성공 하면 `IAuthenticationResult` 개체를 반환 합니다. 여기에는 액세스 토큰, 사용자 클레임 및 메타 데이터가 포함 됩니다.

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

### <a name="get-the-authorized-account"></a>권한 있는 계정 가져오기

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

IdToken에서 반환 된 클레임은 MSAL이 아니라 STS (보안 토큰 서비스)에 의해 채워집니다. 사용 된 IdP (id 공급자)에 따라 일부 클레임이 없을 수도 있습니다. 일부 IdPs는 현재 클레임을 `preferred_username` 제공 하지 않습니다. 이 클레임은 캐싱에 대해 MSAL에서 사용 되므로 자리 표시자 값 `MISSING FROM THE TOKEN RESPONSE`가 대신 사용 됩니다. B2C IdToken 클레임에 대 한 자세한 내용은 [Azure Active Directory B2C의 토큰 개요](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-reference-tokens#claims)를 참조 하세요.

## <a name="managing-accounts-and-policies"></a>계정 및 정책 관리

B2C는 각 정책을 별도의 기관으로 처리 합니다. 따라서 각 정책에서 반환 된 액세스 토큰, 새로 고침 토큰 및 ID 토큰은 서로 교환할 수 없습니다. 즉, 각 정책은 다른 정책을 호출 `IAccount` 하는 데 사용할 수 없는 토큰을 포함 하는 별도의 개체를 반환 합니다.

각 정책은 각 사용자 `IAccount` 의 캐시에를 추가 합니다. 사용자가 응용 프로그램에 로그인 하 고 두 개의 정책을 호출 하는 경우 두 개의 `IAccount`를 갖게 됩니다. 캐시에서이 사용자를 제거 하려면 각 정책에 대해 `removeAccount()` 를 호출 해야 합니다.

에서 정책 `acquireTokenSilent`에 대 한 토큰을 갱신 하는 경우 정책 `IAccount` 에 대 한 이전 호출에서 반환 된 것과 `AcquireTokenSilentParameters`동일한를 제공 합니다. 다른 정책에서 반환 된 계정을 제공 하면 오류가 발생 합니다.

## <a name="next-steps"></a>다음 단계

[Azure Active Directory B2C](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-overview) Azure AD B2C Azure Active Directory B2C에 대해 자세히 알아보세요.
