---
title: Azure AD B2C (MSAL.NET) | Azure
titleSuffix: Microsoft identity platform
description: .NET(MSAL.NET)에 대한 Microsoft 인증 라이브러리에서 Azure AD B2C를 사용할 때의 특정 고려 사항에 대해 알아봅니다.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 10/29/2019
ms.author: jeferrie
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: d31cf3a4e024dc59b865d096cbd0829d50f61a1a
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81533958"
---
# <a name="use-msalnet-to-sign-in-users-with-social-identities"></a>MSAL.NET 사용하여 소셜 ID로 사용자 로그인

azure [Active Directory B2C(Azure AD B2C)를](https://aka.ms/aadb2c)사용하여 MSAL.NET 사용하여 소셜 ID를 사용하여 사용자를 로그인할 수 있습니다. Azure AD B2C는 정책 개념을 기반으로 합니다. MSAL.NET 정책을 지정하는 것은 권한을 제공하는 것으로 변환됩니다.

- 공용 클라이언트 응용 프로그램을 인스턴스화할 때 기관에서 정책을 지정해야 합니다.
- 정책을 적용하려면 `AcquireTokenInteractive` `authority` 매개 변수를 포함하는 재정의를 호출해야 합니다.

이 페이지는 MSAL 3.x용입니다. MSAL 2.x에 관심이 있으시면 [MSAL 2.x의 Azure AD B2C 세부 사항을](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/AAD-B2C-Specifics-MSAL-2.x)참조하십시오.

## <a name="authority-for-a-azure-ad-b2c-tenant-and-policy"></a>Azure AD B2C 테넌트 및 정책에 대한 권한

사용할 권한은 `https://{azureADB2CHostname}/tfp/{tenant}/{policyName}` 다음과 같은 위치입니다.

- `azureADB2CHostname`은 Azure AD B2C 테넌트와 호스트의 `{your-tenant-name}.b2clogin.com`이름입니다(예: )
- `tenant`는 Azure AD B2C 테넌트(예: `{your-tenant-name}.onmicrosoft.com`또는 테넌트의 GUID)의 전체 이름입니다.
- `policyName`신청할 정책 또는 사용자 흐름의 이름(예: 등록/로그인을 위한 "b2c_1_susi").

Azure AD B2C 기관에 대한 자세한 내용은 이 [설명서를](/azure/active-directory-b2c/b2clogin)참조하십시오.

## <a name="instantiating-the-application"></a>응용 프로그램 인스턴스화

응용 프로그램을 빌드할 때 권한을 제공해야 합니다.

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

## <a name="acquire-a-token-to-apply-a-policy"></a>토큰을 획득하여 정책을 적용

공용 클라이언트 응용 프로그램에서 Azure AD B2C 보호 API에 대한 토큰을 얻으려면 권한으로 재정의를 사용해야 합니다.

```csharp
IEnumerable<IAccount> accounts = await application.GetAccountsAsync();
AuthenticationResult ar = await application .AcquireTokenInteractive(scopes)
                                            .WithAccount(GetAccountByPolicy(accounts, policy))
                                            .WithParentActivityOrWindow(ParentActivityOrWindow)
                                            .ExecuteAsync();
```

다음으로 바꿉니다.

- `policy`이전 문자열 중 하나(예:)가 `PolicySignUpSignIn`됩니다.
- `ParentActivityOrWindow`Android(활동)에 필요하며 windows의 창 및 iOS의 UIViewController와 같이 상위 UI를 지원하는 다른 플랫폼에는 선택 사항입니다. [UI 대화 상자에서](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Acquiring-tokens-interactively#withparentactivityorwindow)자세한 내용은 여기를 참조하십시오.
- `GetAccountByPolicy(IEnumerable<IAccount>, string)`은 지정된 정책에 대한 계정을 찾는 방법입니다. 다음은 그 예입니다.

  ```csharp
  private IAccount GetAccountByPolicy(IEnumerable<IAccount> accounts, string policy)
  {
   foreach (var account in accounts)
   {
    string userIdentifier = account.HomeAccountId.ObjectId.Split('.')[0];
    if (userIdentifier.EndsWith(policy.ToLower()))
     return account;
   }
   return null;
  }
  ```

정책 또는 사용자 흐름(예: 최종 사용자가 자신의 프로필을 편집하거나 암호를 재설정하도록 허용)을 `AcquireTokenInteractive`적용하는 작업은 현재 를 호출하여 수행됩니다. 이 두 정책의 경우 반환된 토큰/인증 결과를 사용하지 않습니다.

## <a name="special-case-of-editprofile-and-resetpassword-policies"></a>편집 프로필 및 암호 재설정 정책의 특별 한 경우

최종 사용자가 소셜 ID로 로그인한 다음 프로필을 편집하는 환경을 제공하려는 경우 Azure AD B2C 프로필 편집 정책을 적용하려고 합니다. 이 작업을 수행하는 방법은 `AcquireTokenInteractive` 해당 정책에 대한 특정 권한과 계정 `Prompt.NoPrompt` 선택 대화 상자가 표시되지 않도록 설정된 프롬프트(사용자가 이미 로그인되어 있고 활성 쿠키 세션이 있는 경우)를 호출하는 것입니다.

```csharp
private async void EditProfileButton_Click(object sender, RoutedEventArgs e)
{
 IEnumerable<IAccount> accounts = await app.GetAccountsAsync();
 try
 {
  var authResult = await app.AcquireToken(scopes:App.ApiScopes)
                               .WithAccount(GetUserByPolicy(accounts, App.PolicyEditProfile)),
                               .WithPrompt(Prompt.NoPrompt),
                               .WithB2CAuthority(App.AuthorityEditProfile)
                               .ExecuteAsync();
  DisplayBasicTokenInfo(authResult);
 }
 catch
 {
  . . .
 }
}
```
## <a name="resource-owner-password-credentials-ropc-with-azure-ad-b2c"></a>Azure AD B2C를 사용 하 고 리소스 소유자 암호 자격 증명 (ROPC)
ROPC 흐름에 대한 자세한 내용은 이 [설명서를](v2-oauth-ropc.md)참조하십시오.

사용자에게 암호를 묻는 응용 프로그램이 안전하지 않으므로 이 흐름은 **권장되지 않습니다.** 이 문제에 대한 자세한 내용은 [이 문서를](https://news.microsoft.com/features/whats-solution-growing-problem-passwords-says-microsoft/)참조하십시오.

사용자 이름/암호를 사용하면 다음과 같은 여러 가지 를 포기합니다.
- 현대 정체성의 핵심 신조 : 암호는 물고기, 재생됩니다. 왜냐하면 우리는 가로챌 수 있는 공유 비밀의 개념을 가지고 있기 때문입니다. 이것은 암호가 없는 것과 호환되지 않습니다.
- MFA를 수행해야 하는 사용자는 상호 작용이 없으므로 로그인할 수 없습니다.
- 사용자는 단일 사인온을 수행할 수 없습니다.

### <a name="configure-the-ropc-flow-in-azure-ad-b2c"></a>Azure AD B2C에서 ROPC 흐름 구성
Azure AD B2C 테넌트에서 새 사용자 흐름을 만들고 **ROPC를 사용하여 로그인을**선택합니다. 이렇게 하면 테넌트에 대한 ROPC 정책이 활성화됩니다. 자세한 내용은 [리소스 소유자 암호 자격 증명 흐름 구성을](/azure/active-directory-b2c/configure-ropc) 참조하십시오.

`IPublicClientApplication`메서드를 포함합니다.
```csharp
AcquireTokenByUsernamePassword(
            IEnumerable<string> scopes,
            string username,
            SecureString password)
```

이 메서드는 매개 변수로 사용 합니다.
- 액세스 *토큰을* 요청하는 범위입니다.
- *사용자 이름*.
- 사용자에 대 한 보안 문자열 *암호입니다.*

ROPC 정책이 포함된 권한을 사용해야 합니다.

### <a name="limitations-of-the-ropc-flow"></a>ROPC 흐름의 한계
 - ROPC 흐름은 로컬 계정(전자 메일 또는 사용자 이름을 사용하여 Azure AD B2C에 등록하는 경우)에서만 **작동합니다.** Azure AD B2C(Facebook, Google 등)에서 지원하는 ID 공급자에게 페더레이션하는 경우 이 흐름은 작동하지 않습니다.

## <a name="google-auth-and-embedded-webview"></a>구글 인증 및 임베디드 웹뷰

Google을 ID 공급자로 사용하는 Azure AD B2C 개발자인 경우 Google은 [포함된 웹뷰에서 인증을](https://developers.googleblog.com/2016/08/modernizing-oauth-interactions-in-native-apps.html)허용하지 않으므로 시스템 브라우저를 사용하도록 명령합니다. 현재 `login.microsoftonline.com` Google에서 신뢰할 수 있는 기관입니다. 이 기관을 사용하면 포함된 웹뷰에서 작동합니다. 그러나 `b2clogin.com` Google에서 사용하는 것은 신뢰할 수 있는 권한이 아니므로 사용자는 인증할 수 없습니다.

상황이 변경되면 이 [문제에](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/issues/688) 대한 업데이트를 제공합니다.

## <a name="caching-with-azure-ad-b2c-in-msalnet"></a>MSAL.Net Azure AD B2C로 캐싱

### <a name="known-issue-with-azure-ad-b2c"></a>Azure AD B2C에서 알려진 문제

MSAL.Net [토큰 캐시를](/dotnet/api/microsoft.identity.client.tokencache?view=azure-dotnet)지원합니다. 토큰 캐싱 키는 ID 공급자가 반환한 클레임을 기반으로 합니다. 현재 MSAL.Net 토큰 캐시 키를 빌드하려면 두 가지 클레임이 필요합니다.
- `tid`Azure AD 테넌트 ID이며
- `preferred_username`

이러한 클레임은 많은 Azure AD B2C 시나리오에서 누락되었습니다.

고객의 영향은 사용자 이름 필드를 표시하려고 할 때 값으로 "토큰 응답에서 누락"을 받고 있다는 것입니다. 이 경우 Azure AD B2C는 소셜 계정 및 외부 ID 공급자(IdP)의 제한으로 인해 preferred_username 대한 IdToken에서 값을 반환하지 않기 때문입니다. Azure AD는 사용자가 누구인지 알고 있기 때문에 preferred_username 값을 반환하지만 Azure AD B2C의 경우 사용자가 로컬 계정, Facebook, Google, GitHub 등으로 로그인할 수 있기 때문에 Azure AD B2C가 preferred_username 사용할 일관된 값이 없습니다. MsAL이 ADAL과의 캐시 호환성을 롤아웃하지 못하도록 차단하기 위해 IdToken이 preferred_username 동안 아무 것도 반환하지 않는 Azure AD B2C 계정을 처리할 때 끝에 "토큰 응답에서 누락"을 사용하기로 결정했습니다. MSAL은 라이브러리 간에 캐시 호환성을 유지하기 위해 preferred_username 값을 반환해야 합니다.

### <a name="workarounds"></a>해결 방법

#### <a name="mitigation-for-the-missing-tenant-id"></a>누락된 테넌트 ID에 대한 완화

제안된 해결 방법은 [정책별 캐싱을](#acquire-a-token-to-apply-a-policy) 사용하는 것입니다.

또는 [B2C](https://aka.ms/ief)사용자 `tid` 지정 정책을 사용하는 경우 응용 프로그램에 추가 클레임을 반환하는 기능을 제공하기 때문에 클레임을 사용할 수 있습니다. [클레임 변환에](/azure/active-directory-b2c/claims-transformation-technical-profile) 대해 자세히 알아보려면

#### <a name="mitigation-for-missing-from-the-token-response"></a>"토큰 응답에서 누락" 완화
한 가지 옵션은 "이름" 클레임을 기본 사용자 이름으로 사용하는 것입니다. 프로세스는 이 [B2C 문서](../../active-directory-b2c/user-flow-overview.md) -> "Return 클레임 열에서 성공적인 프로필 편집 환경 후 응용 프로그램으로 다시 전송된 권한 부여 토큰에서 반환할 클레임을 선택합니다. 예를 들어 이름 표시, 우편 번호를 선택합니다."

## <a name="next-steps"></a>다음 단계

Azure AD B2C 응용 프로그램에 대한 MSAL.NET 대화식으로 토큰을 획득하는 것에 대한 자세한 내용은 다음 샘플에서 제공합니다.

| 예제 | 플랫폼 | Description|
|------ | -------- | -----------|
|[액티브 디렉토리 b2c-자마린 네이티브](https://github.com/Azure-Samples/active-directory-b2c-xamarin-native) | 자마린 아이폰 OS, 자마린 안드로이드, UWP | Azure AD B2C를 통해 사용자를 인증하고 결과 토큰을 사용하여 웹 API에 액세스하는 방법을 MSAL.NET 사용하는 방법을 보여주는 간단한 Xamarin Forms 앱입니다.|
