---
title: Azure AD B2C (Microsoft Authentication Library for.NET) | Azure
description: .NET (MSAL.NET)에 대 한 Microsoft 인증 라이브러리를 사용 하 여 Azure AD B2C를 사용 하는 경우 특정 고려 사항에 알아봅니다.
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/24/2019
ms.author: ryanwi
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8240a487bdb01cdbe9017ddc7cb95ce4fc0e1503
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67052370"
---
# <a name="use-msalnet-to-sign-in-users-with-social-identities"></a>MSAL.NET을 사용 하 여 소셜 id 사용 하 여 사용자를 로그인

MSAL.NET을 사용 하 여 사용 하 여 소셜 id 사용 하 여 사용자 로그인 [Azure Active Directory B2C (Azure AD B2C)](https://aka.ms/aadb2c)합니다. Azure AD B2C 정책 개념이 기반으로 구축 되었습니다. MSAL.NET을 정책을 지정 하는 권한을 제공 하는 데로 변환 합니다.

- 공용 클라이언트 응용 프로그램을 인스턴스화할 때 정책 기관에서 지정 해야 합니다.
- 재정의 호출 해야 하는 정책을 적용 하려는 경우 `AcquireTokenInteractive` 포함 하는 `authority` 매개 변수입니다.

이 페이지는 msal 3.x입니다. MSAL에 관심이 있다면 2.x를 참조 하세요 [MSAL의 Azure AD B2C 세부 사항을 2.x](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/AAD-B2C-Specifics-MSAL-2.x).

## <a name="authority-for-a-azure-ad-b2c-tenant-and-policy"></a>Azure AD B2C 테 넌 트 및 정책에 대 한 인증 기관

사용 하 여 기관을 `https://login.microsoftonline.com/tfp/{tenant}/{policyName}` 위치:

- `tenant` Azure AD B2C 테 넌 트의 이름 
- `policyName` 이름 (예를 들어 "b2c_1_susi"에 대 한 로그인-에서/등록)에 적용할 정책입니다.

에 사용 되는 Azure AD B2C에서 현재 지침은 `b2clogin.com` 기관으로 합니다. 예: `$"https://{your-tenant-name}.b2clogin.com/tfp/{your-tenant-ID}/{policyname}"`. 자세한 내용은 참조 [설명서](/azure/active-directory-b2c/b2clogin)합니다.

## <a name="instantiating-the-application"></a>응용 프로그램 인스턴스화

응용 프로그램을 빌드하는 경우에 권한을 제공 해야 합니다.

```csharp
// Azure AD B2C Coordinates
public static string Tenant = "fabrikamb2c.onmicrosoft.com";
public static string ClientID = "90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6";
public static string PolicySignUpSignIn = "b2c_1_susi";
public static string PolicyEditProfile = "b2c_1_edit_profile";
public static string PolicyResetPassword = "b2c_1_reset";

public static string AuthorityBase = $"https://fabrikamb2c.b2clogin.com/tfp/{Tenant}/";
public static string Authority = $"{AuthorityBase}{PolicySignUpSignIn}";
public static string AuthorityEditProfile = $"{AuthorityBase}{PolicyEditProfile}";
public static string AuthorityPasswordReset = $"{AuthorityBase}{PolicyResetPassword}";

application = PublicClientApplicationBuilder.Create(ClientID)
               .WithB2CAuthority(Authority)
               .Build();
```

## <a name="acquire-a-token-to-apply-a-policy"></a>정책을 적용할 수 있는 토큰 획득

공용 클라이언트 응용 프로그램에서 보호 된 API는 토큰을 획득 하는 Azure AD B2C에 대 한 권한을 재정의 사용 하는 필요 합니다.

```csharp
IEnumerable<IAccount> accounts = await application.GetAccountsAsync();
AuthenticationResult ar = await application .AcquireToken(scopes, parentWindow)
                                            .WithAccount(GetAccountByPolicy(accounts, policy))
                                            .ExecuteAsync();
```

다음으로 바꿀 수 있습니다.

- `policy` 이전 문자열 중 하나가 되 고 (예를 들어 `PolicySignUpSignIn`).
- `GetAccountByPolicy(IEnumerable<IAccount>, string)` 지정 된 정책에 대 한 계정을 발견 하는 메서드입니다. 예를 들면 다음과 같습니다.

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

현재 호출 하 여 수행 됩니다 (예를 들어 최종 사용자가 암호를 재설정 또는 프로필을 편집할 수 있도록) 정책 적용 `AcquireTokenInteractive`합니다. 반환된 된 토큰을 사용 하지 않는 경우 이러한 두 가지 정책 / 인증 결과입니다.

## <a name="special-case-of-editprofile-and-resetpassword-policies"></a>특수 한 사례의 EditProfile 및 ResetPassword 정책

소셜 id를 사용 하 여 최종 사용자에 게 로그인 하는 위치는 환경을 제공 하 고 다음 프로필을 편집 하려는 경우 Azure AD B2C EditProfile 정책을 적용 하려는 합니다. 호출 된 경우이 작업을 수행 하는 방법은 `AcquireTokenInteractive` 해당 정책 및로 설정 하는 프롬프트에 대 한 특정 기관 `Prompt.NoPrompt` 계정 선택 대화 상자 표시 (사용자가 이미 로그인)를 방지 하려면

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
## <a name="resource-owner-password-credentials-ropc-with-azure-ad-b2c"></a>리소스 소유자 암호 자격 증명 (ROPC) Azure AD B2C를 사용 하 여
ROPC 흐름에 대 한 자세한 내용은를 참조 하세요 [설명서](v2-oauth-ropc.md)합니다.

이 흐름은 다음과 **좋지** 사용자가 암호를 묻는 응용 프로그램에 안전 하지 않으므로 합니다. 이 문제에 대 한 자세한 내용은 참조 하세요. [이 문서에서는](https://news.microsoft.com/features/whats-solution-growing-problem-passwords-says-microsoft/)합니다. 

사용자 이름/암호를 사용 하 여 사용자는 제공 접속 다양 한 작업:
- 최신 id의 테 넌 트 핵심: 암호 fished 가져옵니다 재생 합니다. 이 개념을 가로챌 수 있는 공유 암호를 지정 했으므로 합니다. 암호 없는 호환 되지 않습니다.
- MFA를 수행 해야 하는 사용자 (개입 없이 하므로)에 로그인 할 수 없습니다.
- 사용자가 single sign on 수행할 수 없습니다.

### <a name="configure-the-ropc-flow-in-azure-ad-b2c"></a>Azure AD B2C에서 ROPC 흐름을 구성 합니다.
Azure AD B2C 테 넌 트에서 새 사용자 흐름을 만들고 선택 **ROPC를 사용 하 여 로그인**합니다. 이렇게 하면 테 넌 트에 대 한 ROPC 정책이 활성화 됩니다. 참조 [리소스 소유자 암호 자격 증명 흐름 구성](/azure/active-directory-b2c/configure-ropc) 대 한 자세한 내용은 합니다.

`IPublicClientApplication` 메서드를 포함 합니다.
```csharp
AcquireTokenByUsernamePassword(
            IEnumerable<string> scopes,
            string username,
            SecureString password)
```

이 메서드는 매개 변수로 사용 합니다.
- 합니다 *범위* 에 대 한 액세스 토큰을 요청 합니다.
- A *username*합니다.
- SecureString *암호* 사용자에 대 한 합니다.

ROPC 정책이 포함 된 기관을 사용 해야 합니다.

### <a name="limitations-of-the-ropc-flow"></a>ROPC 흐름의 제한 사항
 - ROPC 흐름 **로컬 계정에만** (Azure AD B2C를 사용 하 여 등록할 전자 메일 또는 username을 사용 하 여). Azure AD B2C에서 지 원하는 id 공급자 중 하나를 페더레이션 하는 경우이 흐름을 사용할 수 없습니다 (Facebook, Google, 등.).
 - 현재 방법이 **Azure AD B2C에서 반환 된 없습니다 id_token** MSAL에서 ROPC 흐름을 구현 하는 경우. 즉, 될 수 있도록 캐시에 없는 계정 및 사용자 계정 개체를 만들 수 없습니다. AcquireTokenSilent 흐름은이 시나리오에서는 작동 하지 않습니다. 그러나 ROPC UI를 표시 하지 않습니다, 그리고 없으므로 사용자 환경에 영향을 미치지 않습니다.

## <a name="google-auth-and-embedded-webview"></a>Google 인증 및 포함 된 웹 보기

Azure AD B2C 개발자의 경우 Google을 id 공급자로 사용 하 여 우리가 recommand 시스템 브라우저를 사용 하 여 Google에서 허용 되지 [포함 된 웹 보기에서 인증](https://developers.googleblog.com/2016/08/modernizing-oauth-interactions-in-native-apps.html)합니다. 현재 `login.microsoftonline.com` 는 Google 사용 하 여 신뢰할 수 있는 기관. 이 권한을 사용 하 여 포함 된 웹 보기를 사용 하 여 작동 합니다. 그러나 사용 하 여 `b2clogin.com` 면 Google 사용 하 여 신뢰할 수 있는 기관 아니므로 사용자를 인증할 수 없습니다.

Wiki 및이에 대 한 업데이트를 제공할 것 [문제](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/issues/688) 변화 하는 경우.

## <a name="caching-with-azure-ad-b2c-in-msalnet"></a>MSAL.Net에서 Azure AD B2C를 사용 하 여 캐싱 

### <a name="known-issue-with-azure-ad-b2c"></a>Azure AD B2C를 사용 하 여 알려진된 문제

MSAL.Net 지원를 [토큰 캐시](/dotnet/api/microsoft.identity.client.tokencache?view=azure-dotnet)합니다. 키를 캐시 하는 토큰 Id 공급자에 의해 반환 된 클레임을 기반으로 합니다. 현재 MSAL.Net 두 클레임을 토큰 캐시 키를 빌드할 필요 합니다.  
- `tid` Azure AD 테 넌 트 ID는 및 
- `preferred_username` 

둘 다 이러한 클레임 대부분의 Azure AD B2C 시나리오에서 누락 되었습니다. 

고객 영향 값으로는 사용자 이름 필드를 표시할 때을 얻고 "토큰 응답에서 누락 된"는? 그렇다면, Azure AD B2C는 값을 반환 하지는 preferred_username에 대 한 IdToken에서 외부 id 공급자 (Idp) 고 소셜 계정을 사용 하 여 제한으로 인해 때문입니다. Azure AD 사용자가 누구 인지 알고 있으므로 preferred_username에 대 한 값을 반환 하지 않으면이 Azure AD B2C에 대 한 사용자 로컬 계정, Facebook, Google, GitHub에 로그인 할 수 있으므로 등 있습니다 preferred_username에 사용할 Azure AD B2C에 대 한 일관 된 값입니다. MSAL에서 ADAL 사용 하 여 캐시 호환성 출시를 차단 해제 하는 IdToken preferred_username에 대해 nothing을 반환 하는 경우 Azure AD B2C 계정으로 처리 하는 경우 끝에서 "토큰 응답에서 누락 된"를 사용 하기로 합니다. MSAL은 라이브러리 간에 캐시 호환성을 유지 하기 위해 preferred_username에 대 한 값을 반환 해야 합니다.

### <a name="workarounds"></a>해결 방법

#### <a name="mitigation-for-the-missing-tenant-id"></a>누락 된 테 넌 트 ID에 대 한 완화

제안 된 해결 방법을 사용 하는 것은 [정책에 따라 캐싱](#acquire-a-token-to-apply-a-policy)

사용할 수 있습니다 합니다 `tid` 클레임을 사용 하는 경우는 [B2C 사용자 지정 정책](https://aka.ms/ief)추가 클레임을 응용 프로그램을 반환 하는 기능을 제공 하기 때문에, 합니다. 에 대해 자세히 알아보려면 [클레임 변환](/azure/active-directory-b2c/claims-transformation-technical-profile)

#### <a name="mitigation-for-missing-from-the-token-response"></a>"누락 된 토큰 응답에서" 완화 하는 방법
한 가지 옵션은 기본 사용자 이름으로 "name" 클레임을 사용 하는 것입니다. 이 프로세스는 언급 [B2C 문서](../../active-directory-b2c/active-directory-b2c-reference-policies.md) "반환 클레임 열에는 성공적인 프로필 편집 환경 이후에 응용 프로그램으로 다시 보낸 권한 부여 토큰에서 반환 하려는 클레임을 선택-> 합니다. 표시 이름, 우편 번호 선택 예를 들어,. "

## <a name="next-steps"></a>다음 단계 

Azure AD B2C 응용 프로그램에 대 한 MSAL.NET 사용 하 여 대화형으로 토큰을 획득 하는 방법에 대 한 자세한 내용은 다음 샘플에 나와 있습니다.

| 샘플 | 플랫폼 | 설명|
|------ | -------- | -----------|
|[active-directory-b2c-xamarin-native](https://github.com/Azure-Samples/active-directory-b2c-xamarin-native) | Xamarin iOS, Xamarin Android, UWP | Azure AD B2C를 통해 사용자를 인증 하 고 결과 토큰을 사용 하 여 Web API 액세스 MSAL.NET을 사용 하는 방법을 보여주는 간단한 Xamarin Forms 앱.|
