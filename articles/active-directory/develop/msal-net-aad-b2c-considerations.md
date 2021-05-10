---
title: Azure AD B2C 및 MSAL.NET
titleSuffix: Microsoft identity platform
description: MSAL.NET(.NET용 Microsoft 인증 라이브러리)로 Azure AD B2C를 사용할 때의 고려 사항
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/07/2020
ms.author: jeferrie
ms.reviewer: saeeda
ms.custom: devx-track-csharp, aaddev
ms.openlocfilehash: cdbba1618815dbc8b52195169b7de2bcec2986d8
ms.sourcegitcommit: 20f8bf22d621a34df5374ddf0cd324d3a762d46d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/09/2021
ms.locfileid: "107255582"
---
# <a name="use-msalnet-to-sign-in-users-with-social-identities"></a>MSAL.NET를 사용하여 소셜 ID로 사용자 로그인

MSAL.NET를 사용하고 [Azure AD B2C(Azure Active Directory B2C)](../../active-directory-b2c/overview.md)를 사용하여 소셜 ID로 사용자 로그인할 수 있습니다. Azure AD B2C는 정책의 개념을 중심으로 빌드되었습니다. MSAL.NET에서는 정책을 지정하면 권한을 제공하는 것으로 해석됩니다.

- 퍼블릭 클라이언트 애플리케이션을 인스턴스화할 때 정책을 권한의 일부로 지정합니다.
- 정책을 적용하려 할 때 `authority` 매개 변수를 허용하는 `AcquireTokenInteractive` 재정의를 호출합니다.

이 문서는 MSAL.NET 3.x에 적용됩니다. MSAL.NET 2.x는 GitHub의 MSAL.NET Wiki에서 [MSAL 2.x에 있는 Azure AD B2C의 세부 정보](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/AAD-B2C-Specifics-MSAL-2.x)를 참조하세요.

## <a name="authority-for-an-azure-ad-b2c-tenant-and-policy"></a>Azure AD B2C 테넌트의 인증 기관 및 정책

Azure AD B2C의 인증 기관 형식은 `https://{azureADB2CHostname}/tfp/{tenant}/{policyName}`과 같습니다.

- `azureADB2CHostname` - Azure AD B2C 테넌트와 호스트의 이름입니다. 예를 들면 *contosob2c.b2clogin.com* 입니다.
- `tenant` - Azure AD B2C 테넌트의 도메인 이름 또는 디렉터리(테넌트) ID입니다. 예를 들면, 각각 *contosob2c.onmicrosoft.com* 또는 GUID입니다.
- `policyName` - 적용할 사용자 흐름 또는 사용자 지정 정책의 이름입니다. 예를 들면 *b2c_1_susi* 와 같은 등록/로그인 정책이 있습니다.

Azure AD B2C 인증 기관에 대한 자세한 내용은 [리디렉션 URL을 b2clogin.com으로 설정](../../active-directory-b2c/b2clogin.md)을 참조하세요.

## <a name="instantiating-the-application"></a>애플리케이션 인스턴스화

애플리케이션 개체를 만들 때 `WithB2CAuthority()`를 호출하여 인증 기관을 제공합니다.

```csharp
// Azure AD B2C Coordinates
public static string Tenant = "fabrikamb2c.onmicrosoft.com";
public static string AzureADB2CHostname = "fabrikamb2c.b2clogin.com";
public static string ClientID = "90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6";
public static string PolicySignUpSignIn = "b2c_1_susi";
public static string PolicyEditProfile = "b2c_1_edit_profile";
public static string PolicyResetPassword = "b2c_1_reset";

public static string AuthorityBase = $"https://{AzureADB2CHostname}/tfp/{Tenant}/";
public static string Authority = $"{AuthorityBase}{PolicySignUpSignIn}";
public static string AuthorityEditProfile = $"{AuthorityBase}{PolicyEditProfile}";
public static string AuthorityPasswordReset = $"{AuthorityBase}{PolicyResetPassword}";

application = PublicClientApplicationBuilder.Create(ClientID)
               .WithB2CAuthority(Authority)
               .Build();
```

## <a name="acquire-a-token-to-apply-a-policy"></a>정책을 적용하는 토큰 획득

퍼블릭 클라이언트 애플리케이션에서 Azure AD B2C로 보호된 API에 대한 토큰을 획득하려면 인증 기관과 함께 재정의를 사용해야 합니다.

```csharp
AuthenticationResult authResult = null;
IEnumerable<IAccount> accounts = await application.GetAccountsAsync(policy);
IAccount account = accounts.FirstOrDefault();
try
{
    authResult = await application.AcquireTokenSilent(scopes, account)
                      .ExecuteAsync();
}
catch (MsalUiRequiredException ex)
{
    authResult = await application.AcquireTokenInteractive(scopes)
                        .WithAccount(account)
                        .WithParentActivityOrWindow(ParentActivityOrWindow)
                        .ExecuteAsync();
}  
```

앞의 코드 조각에서 다음을 확인할 수 있습니다.

- `policy`는 Azure AD B2C 사용자 흐름 또는 사용자 지정 정책(예: `PolicySignUpSignIn`)의 이름을 포함하는 문자열입니다.
- `ParentActivityOrWindow`는 Android(Activity)에 필요하며 Microsoft Windows 및 iOS의 UIViewController와 같은 부모 UI를 지원하는 다른 플랫폼에 대해서는 선택 사항입니다. UI 대화 상자에 대한 자세한 내용은 MSAL Wiki의 [WithParentActivityOrWindow](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Acquiring-tokens-interactively#withparentactivityorwindow)를 참조하세요.

사용자 흐름 또는 사용자 지정 정책 적용(예: 사용자가 프로필을 편집하거나 암호를 재설정할 수 있음)은 현재 `AcquireTokenInteractive`를 호출하여 수행됩니다. 이 두 정책의 경우 반환된 토큰/인증 결과를 사용하지 않습니다.

## <a name="profile-edit-policies"></a>프로필 편집 정책

사용자가 소셜 ID로 로그인한 다음 프로필을 편집할 수 있도록 하려면 Azure AD B2C 프로필 편집 정책을 적용합니다.

이렇게 하려면 해당 정책에 대한 권한을 사용하여 `AcquireTokenInteractive`를 호출합니다. 사용자가 이미 로그인되어 있고 활성 쿠키 세션이 있으므로 `Prompt.NoPrompt`를 사용하여 계정 선택 대화 상자가 표시되지 않도록 합니다.

```csharp
private async void EditProfileButton_Click(object sender, RoutedEventArgs e)
{
    IEnumerable<IAccount> accounts = await application.GetAccountsAsync(PolicyEditProfile);
    IAccount account = accounts.FirstOrDefault();
    try
    {
        var authResult = await application.AcquireTokenInteractive(scopes)
                            .WithPrompt(Prompt.NoPrompt),
                            .WithAccount(account)
                            .WithB2CAuthority(AuthorityEditProfile)
                            .ExecuteAsync();
     }
    catch
    {
    }
}
```

## <a name="resource-owner-password-credentials-ropc"></a>ROPC(리소스 소유자 암호 자격 증명)

ROPC 흐름에 대한 자세한 내용은 [리소스 소유자 암호 자격 증명 부여를 사용하여 로그인](v2-oauth-ropc.md)을 참조하세요.

애플리케이션에서 사용자에게 암호를 묻는 것은 안전하지 않으므로 ROPC 흐름을 **사용하지 않는** 것이 좋습니다. 이 문제에 대한 자세한 내용은 [늘어나는 암호 문제에 대한 해결 방법](https://news.microsoft.com/features/whats-solution-growing-problem-passwords-says-microsoft/)을 참조하세요.

ROPC 흐름에서 사용자 이름/암호를 사용하여 다음과 같은 몇 가지 작업을 수행할 수 있습니다.

- 최신 ID의 핵심 개념: 공유 비밀을 가로채어 암호를 도난당하거나 사용될 수 있습니다. 정의에 따라 ROPC는 암호 없는 흐름과 호환되지 않습니다.
- MFA를 수행해야 하는 사용자는 상호 작용이 없으므로 로그인할 수 없습니다.
- 사용자는 SSO(Single Sign-On)를 사용할 수 없습니다.

### <a name="configure-the-ropc-flow-in-azure-ad-b2c"></a>Azure AD B2C에서 ROPC 흐름 구성

Azure AD B2C 테넌트에서 새 사용자 흐름을 만들고 **ROPC를 사용하여 로그인** 을 선택하여 사용자 흐름에 ROPC를 사용하도록 설정합니다. 자세한 내용은 [리소스 소유자 암호 자격 증명 흐름 구성](../../active-directory-b2c/add-ropc-policy.md)을 참조하세요.

`IPublicClientApplication`은 `AcquireTokenByUsernamePassword` 메서드를 포함합니다.

```csharp
AcquireTokenByUsernamePassword(
            IEnumerable<string> scopes,
            string username,
            SecureString password)
```

이 `AcquireTokenByUsernamePassword` 메서드에서 사용하는 매개 변수는 다음과 같습니다.

- 액세스 토큰을 가져올 ‘범위’
- ‘사용자 이름’
- 사용자의 SecureString ‘암호’

### <a name="limitations-of-the-ropc-flow"></a>ROPC 흐름의 제한 사항

ROPC 흐름은 사용자가 메일 주소 또는 사용자 이름을 사용하여 Azure AD B2C에 등록한 **로컬 계정에서만 작동** 합니다. 이 흐름은 Azure AD B2C(Facebook, Google 등)에서 지원하는 외부 ID 공급자에 페더레이션 하는 경우에는 작동하지 않습니다.

## <a name="google-auth-and-embedded-webview"></a>Google 인증 및 포함된 웹 보기

Google을 ID 공급자로 사용하는 경우 Google은 [포함된 웹 보기에서 인증](https://developers.googleblog.com/2016/08/modernizing-oauth-interactions-in-native-apps.html)을 허용하지 않으므로 시스템 브라우저를 사용하는 것이 좋습니다. 현재 `login.microsoftonline.com`은 Google을 이용하는 신뢰할 수 있는 기관이며 포함된 웹 보기와 함께 작동합니다. 그러나 `b2clogin.com`은 Google을 이용하는 신뢰할 수 있는 기관이 아니므로 사용자가 인증할 수 없습니다.

변경 사항이 있으면 이 [이슈](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/issues/688)에 대한 업데이트를 제공합니다.

## <a name="token-caching-in-msalnet"></a>MSAL.NET의 토큰 캐싱

### <a name="known-issue-with-azure-ad-b2c"></a>Azure AD B2C의 알려진 문제

MSAL.NET는 [토큰 캐시](/dotnet/api/microsoft.identity.client.tokencache)를 지원합니다. 토큰 캐싱 키는 IdP(ID 공급자)가 반환한 클레임을 기반으로 합니다.

현재 MSAL.NET는 토큰 캐시 키를 빌드하는 데 두 가지 클레임이 필요합니다.

- `tid`(Azure AD 테넌트 ID)
- `preferred_username`

모든 소셜 ID 공급자(Facebook, Google 등)가 Azure AD B2C로 반환하는 토큰으로 반환하는 것은 아니므로 Azure AD B2C 시나리오에서 이러한 두 가지 클레임은 모두 누락될 수 있습니다.

이러한 시나리오의 증상은 Azure AD B2C에서 발급한 토큰의 `preferred_username` 클레임 값에 액세스할 때 MSAL.NET가 `Missing from the token response`를 반환한다는 것입니다. MSAL은 `preferred_username`의 `Missing from the token response` 값을 사용하여 라이브러리 사이의 캐시 상호 호환성을 유지 관리합니다.

### <a name="workarounds"></a>해결 방법

#### <a name="mitigation-for-missing-tenant-id"></a>누락된 테넌트 ID에 대한 완화

제안된 해결 방법은 앞에서 설명한 [정책별 캐싱](#acquire-a-token-to-apply-a-policy)을 사용하는 것입니다.

또는 Azure AD B2C에서 [사용자 지정 정책](../../active-directory-b2c/user-flow-overview.md)을 사용하면 `tid` 클레임을 사용할 수 있습니다. 사용자 지정 정책은 [클레임 변환](../../active-directory-b2c/claims-transformation-technical-profile.md)을 사용하여 애플리케이션에 추가 클레임을 반환할 수 있습니다.

#### <a name="mitigation-for-missing-from-the-token-response"></a>“토큰 응답에서 누락”에 대한 완화

한 가지 옵션은 `preferred_username` 대신 `name` 클레임을 사용하는 것입니다. Azure AD B2C에서 발급한 ID 토큰에 `name` 클레임을 포함하려면 사용자 흐름을 구성할 때 **표시 이름** 을 선택합니다.

사용자 흐름에서 반환되는 클레임을 지정하는 방법에 대한 자세한 내용은 [자습서: Azure AD B2C에서 사용자 흐름 만들기](../../active-directory-b2c/tutorial-create-user-flows.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계

Azure AD B2C 애플리케이션용 MSAL.NET과 대화형으로 토큰을 가져오는 방법에 대한 자세한 내용은 다음 샘플에 나와 있습니다.

| 샘플 | 플랫폼 | Description|
|------ | -------- | -----------|
|[active-directory-b2c-xamarin-native](https://github.com/Azure-Samples/active-directory-b2c-xamarin-native) | Xamarin iOS, Xamarin Android, UWP | MSAL.NET를 사용하여 Azure AD B2C를 통해 사용자를 인증한 후 반환된 토큰을 사용하여 웹 API에 액세스하는 Xamarin Forms 앱입니다.|
