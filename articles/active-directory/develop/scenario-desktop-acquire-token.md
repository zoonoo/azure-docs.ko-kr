---
title: 웹 Api (응용 프로그램에 대 한 토큰을 확보 합니다.)-Microsoft id 플랫폼을 호출 하는 데스크톱 앱
description: 웹 Api를 호출 하는 데스크톱 앱을 빌드하는 방법을 알아봅니다 (앱 토큰을 획득 |)
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: e1fe9594471c6e8f723afff2def940bb675e04fb
ms.sourcegitcommit: 6f043a4da4454d5cb673377bb6c4ddd0ed30672d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/08/2019
ms.locfileid: "65407001"
---
# <a name="desktop-app-that-calls-web-apis---acquire-a-token"></a>토큰을 획득 하는 웹 Api를 호출 하는 데스크톱 앱

사용자를 만든 후 `IPublicClientApplication`, 다음 web API 호출을 사용 하는 토큰 획득을 사용 합니다.

## <a name="recommended-pattern"></a>권장된 패턴

Web API가 정의한 해당 `scopes`합니다. 모든 응용 프로그램에서 제공 하는 패턴을 사용 하는 것이 좋습니다 환경은:

- 체계적으로 호출 하 여 토큰 캐시에서 토큰을 가져오려고 시도 `AcquireTokenSilent`
- 이 호출에 실패 하는 경우 사용 합니다 `AcquireToken` 흐름을 사용 하려면 (여기 나타내는 `AcquireTokenXX`)

```CSharp
AuthenticationResult result;
var accounts = await app.GetAccountsAsync();
IAccount account = ChooseAccount(accounts); // for instance accounts.FirstOrDefault
                                            // if the app manages is at most one account  
try
{
 result = await app.AcquireTokenSilent(scopes, account)
                   .ExecuteAsync();
}
catch(MsalUiRequiredException ex)
{
  result = await app.AcquireTokenXX(scopes, account)
                    .WithOptionalParameterXXX(parameter)
                    .ExecuteAsync();
}
```

데스크톱 응용 프로그램에서 토큰을 획득 하는 여러 가지 정보는 이제 같습니다.

## <a name="acquiring-a-token-interactively"></a>대화형 토큰 획득

다음 예제에서는 토큰을 가져오려면 대화형으로 Microsoft Graph를 사용 하 여 사용자의 프로필을 읽는 데 필요한 최소한의 코드를 보여 줍니다.

```CSharp
string[] scopes = new string["user.read"];
var app = PublicClientApplicationBuilder.Create(clientId).Build();
var accounts = await app.GetAccountsAsync();
AuthenticationResult result;
try
{
 result = await app.AcquireTokenSilent(scopes, accounts.FirstOrDefault())
             .ExecuteAsync();
}
catch(MsalUiRequiredException)
{
 result = await app.AcquireTokenInteractive(scopes)
             .ExecuteAsync();
}
```

### <a name="mandatory-parameters"></a>필수 매개 변수

`AcquireTokenInteractive` 필수 매개 변수가 하나뿐인 ``scopes``, 토큰은 필요한 범위를 정의 하는 문자열의 열거형이 포함 된 합니다. Microsoft Graph 용 토큰 인 경우 "권한" 섹션에서 각 Microsoft graph API의 api 참조에 필요한 범위를 찾을 수 있습니다. 예를 들어 하 [사용자의 연락처 목록](https://developer.microsoft.com/graph/docs/api-reference/v1.0/api/user_list_contacts), "User.Read", "Contacts.Read" 범위를 사용 해야 합니다. 참고 항목 [Microsoft Graph 권한 참조](https://developer.microsoft.com/graph/docs/concepts/permissions_reference)합니다.

Android에서는 또한 부모 활동을 지정 해야 (사용 하 여 `.WithParentActivityOrWindow`, 아래 참조)을 토큰 상호 작용 한 후 해당 부모 활동을 다시 가져오도록 합니다. 호출할 때 예외가 throw 됩니다 지정 하지 않을 경우 `.ExecuteAsync()`합니다.

### <a name="specific-optional-parameters"></a>특정 선택적 매개 변수

#### <a name="withparentactivityorwindow"></a>WithParentActivityOrWindow

대화형, UI 것은 중요 합니다. `AcquireTokenInteractive` 하나의 특정 선택적 매개 변수를 지 원하는 플랫폼을 지정 하려면 활성화가 부모 UI입니다. 데스크톱 응용 프로그램에서 사용 하는 경우 `.WithParentActivityOrWindow` 플랫폼에 따라 다른 형식이 있습니다.

```CSharp
// net45
WithParentActivityOrWindow(IntPtr windowPtr)
WithParentActivityOrWindow(IWin32Window window)

// Mac
WithParentActivityOrWindow(NSWindow window)

// .Net Standard (this will be on all platforms at runtime, but only on NetStandard at build time)
WithParentActivityOrWindow(object parent).
```

설명:

- .NET standard를 예상 `object` 은 `Activity` android에서 `UIViewController` ios의 경우는 `NSWindow` MAC에서 및 `IWin32Window` 또는 `IntPr` Windows에서.
- Windows를 호출 해야 `AcquireTokenInteractive` ui에서 스레드는 포함 된 브라우저는 적절 한 UI 동기화 컨텍스트를 가져옵니다.  UI 스레드에서 호출 하지 않으면 하지 제대로 펌프 및/또는 UI 사용 하 여 시나리오를 교착 상태 메시지가 될 수 있습니다. UI 스레드에서 아니라면 UI 스레드에서 MSAL을 호출 하는 한 가지 방법은 이미 사용 하는 것은 `Dispatcher` WPF에서.
- WPF 창을 WPF 컨트롤을 활용 하려면를 사용 하는 경우 사용할 수 있습니다 `WindowInteropHelper.Handle` 클래스입니다. WPF 컨트롤에서 차례로 호출이 (`this`):
  
  ```CSharp
  result = await app.AcquireTokenInteractive(scopes)
                    .WithParentActivityOrWindow(new WindowInteropHelper(this).Handle)
                    .ExecuteAsync();
  ```

#### <a name="withprompt"></a>WithPrompt

`WithPrompt()` 프롬프트를 지정 하 여 사용자와 상호 작용을 제어 하는 데 사용 됩니다.

<img src="https://user-images.githubusercontent.com/13203188/53438042-3fb85700-39ff-11e9-9a9e-1ff9874197b3.png" width="25%" />

클래스는 다음과 같은 상수를 정의합니다.

- ``SelectAccount``: 사용자 세션에 있는 계정을 포함 하는 계정 선택 대화 상자를 표시 하도록 STS를 강제 적용 됩니다. 이 옵션은 응용 프로그램 개발자가 사용자가 서로 다른 id를 선택할 수 있도록 하려는 경우에 유용 합니다. 이 옵션에는 보내도록 MSAL 드라이브 ``prompt=select_account`` id 공급자입니다. 이 옵션은 기본적으로 적절 한 작업 (계정, 사용자, 등에 대 한 세션의 현재 상태 사용 가능한 정보를 기반으로 가장 가능한 환경을 제공 하는 것과입니다. ...). 작업을 수행 하는 이유가 없다면 바꾸지 마세요.
- ``Consent``: 응용 프로그램을 사용 하도록 설정 하기 전에 승인을 받은 경우에 사용자가 개발자 동의를 묻는 합니다. 이 경우 MSAL 보냅니다 `prompt=consent` id 공급자입니다. 이 옵션 사용할 수 있습니다 일부 보안 초점을 맞춘 응용 프로그램에서 조직의 거 버 넌 스 요구 응용 프로그램을 사용할 때마다 사용자가 동의 대화 상자는 표시 하는 위치입니다.
- ``ForceLogin``:이 사용자 프롬프트를 필요 하지 않습니다. 경우에 서비스에서 자격 증명을 묻는 사용자가 응용 프로그램 개발자를 사용 하도록 설정 합니다. 이 옵션은 토큰을 확보에 실패 하면 사용자가 다시 로그인 하는 경우에 유용할 수 있습니다. 이 경우 MSAL 보냅니다 `prompt=login` id 공급자입니다. 마찬가지로 일부 보안 초점을 맞춘 응용 프로그램에서 사용 하는 사용자 relogs에 응용 프로그램의 특정 부분에 액세스할 때마다 조직의 거 버 넌 스 요구 되는 위치 확인 되었습니다.
- ``Never`` (.NET 4.5 및 WinRT만)에 사용자를 묻는 메시지가 나타나지 않지만 대신 숨겨진된 포함 된 웹 보기에 저장 된 쿠키를 사용 하려고 시도 합니다 (아래 참조: 웹 보기 MSAL.NET에서)입니다. 이 옵션을 사용 하 여 실패할 및 이런 `AcquireTokenInteractive` UI 상호 작용 하는 필요 하지 않으며 하나를 사용 해야는 알리기 위해 예외를 throw 합니다 `Prompt` 매개 변수입니다.
- ``NoPrompt``: Id 공급자에 모든 프롬프트를 보내지 않습니다. 이 옵션은 Azure AD B2C 편집 프로필 정책에 대 한 유용한만 (참조 [B2C 세부 사항을](https://aka.ms/msal-net-b2c-specificities)).

#### <a name="withextrascopetoconsent"></a>WithExtraScopeToConsent

이 한정자는 고급 시나리오에서는 여러 리소스 사전에 미리 동의 하려는 (MSAL.NET을 사용 하 여 일반적으로 사용 되는 증분 동의 사용 하지 않으려는 / Microsoft identity 플랫폼 v2.0). 자세한 내용은 참조 하십시오 [방법: 여러 리소스에 대 한 사전 동의 사용자에 게](scenario-desktop-production.md#how-to-have--the-user-consent-upfront-for-several-resources)합니다.

```CSharp
var result = await app.AcquireTokenInteractive(scopesForCustomerApi)
                     .WithExtraScopeToConsent(scopesForVendorApi)
                     .ExecuteAsync();
```

#### <a name="withcustomwebui"></a>WithCustomWebUi

##### <a name="withcustomwebui-is-an-extensibility-point"></a>WithCustomWebUi는 확장성 지점

`WithCustomWebUi` 공용 클라이언트 응용 프로그램에서 직접 UI를 제공할 수 있도록 하는 확장성 지점 및 /Authorize 끝점 id 공급자 및 사용을 통해 사용자에 게 로그인 하 고이 동의 합니다. MSAL.NET 수, 그런 다음, 인증 코드를 사용 하 고이 토큰을 가져오려면 만듭니다. 예를 들어 응용 프로그램 (예: VS 피드백) 웹과 상호 작용을 제공 하지만 대부분의 작업을 위해 MSAL.NET 그대로 전자 하도록 Visual Studio에서 사용 됩니다. UI 자동화를 제공 하려는 경우에 다음이 사용할 수도 있습니다. 공용 클라이언트 응용 프로그램에서 MSAL.NET는 PKCE 표준을 ([RFC 7636-OAuth 공용 클라이언트에서 코드 Exchange에 대 한 증명 키](https://tools.ietf.org/html/rfc7636)) 보안이 적용 되도록 합니다. MSAL.NET만 코드를 교환할 수 있습니다.

  ```CSharp
  using Microsoft.Identity.Client.Extensions;
  ```

##### <a name="how-to-use-withcustomwebui"></a>WithCustomWebUi를 사용 하는 방법

사용 하기 위해 `.WithCustomWebUI`를 해야 합니다.
  
  1. 구현 된 `ICustomWebUi` 인터페이스 (참조 [여기](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/blob/053a98d16596be7e9ca1ab916924e5736e341fe8/src/Microsoft.Identity.Client/Extensibility/ICustomWebUI.cs#L32-L70)합니다. 기본적으로 하나의 메서드를 구현 해야 `AcquireAuthorizationCodeAsync` 수락 (MSAL.NET 하 여 계산 된) 권한 부여 코드 URL, 사용자가 id 공급자와 상호 작용을 통해 이동 하 고 반환 되는 id 공급자는 URL을 다시 다시 (포함 하 여 인증 코드) 구현을 호출 합니다. 문제가 있으면 구현 시켜야는 `MsalExtensionException` MSAL을 사용 하 여 원활 하 게 협력 하는 예외입니다.
  2. 사용자 `AcquireTokenInteractive` 호출을 사용할 수 `.WithCustomUI()` 사용자의 인스턴스를 전달 하는 한정자 웹 UI

     ```CSharp
     result = await app.AcquireTokenInteractive(scopes)
                       .WithCustomWebUi(yourCustomWebUI)
                       .ExecuteAsync();
     ```

##### <a name="examples-of-implementation-of-icustomwebui-in-test-automation---seleniumwebui"></a>테스트 자동화-SeleniumWebUI ICustomWebUi의 구현 예제

MSAL.NET 팀이 확장성 메커니즘을 활용 하 여 UI 테스트를 다시 작성 해야만 합니다. 참조 하려는 경우 있습니다 합니다 [SeleniumWebUI](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/blob/053a98d16596be7e9ca1ab916924e5736e341fe8/tests/Microsoft.Identity.Test.Integration/Infrastructure/SeleniumWebUI.cs#L15-L160) MSAL.NET 소스 코드에서 클래스

#### <a name="other-optional-parameters"></a>다른 선택적 매개 변수

모든 다른 선택적 매개 변수에 대 한 자세한 `AcquireTokenInteractive` 에 대 한 참조 설명서에서 [AcquireTokenInteractiveParameterBuilder](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.apiconfig.acquiretokeninteractiveparameterbuilder?view=azure-dotnet-preview#methods)

## <a name="integrated-windows-authentication"></a>통합된 Windows 인증

도메인 또는 Azure AD 가입 된 컴퓨터에 도메인 사용자를 로그인 하려는 경우 사용 해야 합니다.

```csharp
AcquireTokenByIntegratedWindowsAuth(IEnumerable<string> scopes)
```

### <a name="constraints"></a>제약 조건

- AcquireTokenByIntegratedWindowsAuth (IWA)에 유용 **페더레이션** 사용자 에게만, 사용자가 Active Directory에서 생성 하 고 Azure Active Directory에서 지원 합니다. 사용자가 AD 지원-없이 AAD에서 직접 만든 **관리 되는** 사용자-이 인증 흐름을 사용할 수 없습니다. 이 제한은 사용자 이름/암호 흐름에 영향을 주지 않습니다.
- IWA는.NET Framework,.NET Core 및 UWP 플랫폼용으로 작성 된 앱
- IWA는 MFA (다단계 인증)를 무시 되지 않습니다. MFA를 구성 하는 경우 IWA 실패할 수 있습니다 MFA 챌린지를 필요한 경우 MFA 사용자 상호 작용이 필요 합니다.
  > [!NOTE]
  > 이 항목은 까다롭습니다. IWA 비 대화형 이지만 2fa가 필요한 사용자 상호 작용 합니다. Id 공급자 요청 수행 하는 데 2fa가 제어 하지 않으면 테 넌 트 관리자입니다. 이 관찰에서 VPN을 통해 회사 네트워크에 연결 되지 않음 및 경우에 따라 VPN을 통해 연결 하는 경우에 하는 경우 다른 국가에서 로그인 하는 경우 2fa가 필요 합니다. 규칙 집합을 결정적으로 예상 하, Azure Active Directory AI를 사용 하 여 지속적으로 2FA가 필요한 경우에 대해 알아봅니다. IWA 하지 못하면 사용자 프롬프트 (대화형 장치 또는 인증 코드 흐름)를 대체 해야 합니다.

- 인증 기관에서 전달 된 `PublicClientApplicationBuilder` 해야:
  - 테 넌 트 ed (양식의 `https://login.microsoftonline.com/{tenant}/` 여기서 `tenant` 테 넌 트와 연결 된 도메인 또는 테 넌 트 ID를 나타내는 guid입니다.
  - 에 대 한 회사 및 학교 계정 (`https://login.microsoftonline.com/organizations/`)

  > 개인 Microsoft 계정이 지원 되지 않습니다 (/common 또는 /consumers 테 넌 트를 사용할 수 없음)

- 통합 Windows 인증 자동 흐름 이므로:
  - 응용 프로그램을 사용 하려면 응용 프로그램의 사용자를 이전에 동의한 해야
  - 또는 응용 프로그램을 사용 하려면 테 넌 트에서 모든 사용자에 게 테 넌 트 관리자를 이전에 동의한 해야 합니다.
  - 즉:
    - 개발자로 서 사용자가 누른 하거나 합니다 **부여** 자신에 대 한 Azure portal에서 단추
    - 또는 테 넌 트 관리자가 누를 합니다 **{테 넌 트 도메인}에 대 한 Grant/revoke 관리자 동의** 단추를 **API 사용 권한** 응용 프로그램 등록의 탭 (참조 [권한을 추가 액세스 web Api](https://docs.microsoft.com/azure/active-directory/develop/quickstart-configure-app-access-web-apis#add-permissions-to-access-web-apis))
    - 사용자가 응용 프로그램에 동의 하면 제공한 또는 (참조 [개별 사용자의 동의 요청](https://docs.microsoft.com/azure/active-directory/develop/v2-permissions-and-consent#requesting-individual-user-consent))
    - 테 넌 트 관리자가 응용 프로그램에 대 한 동의 하는 방법을 제공한 또는 (참조 [관리자 동의](https://docs.microsoft.com/azure/active-directory/develop/v2-permissions-and-consent#requesting-consent-for-an-entire-tenant))

- 이 흐름은.net 데스크톱,.net core 및 Windows (UWP (유니버설) 앱에 대 한 활성화 됩니다. .NET core에서 사용자를 갖는 오버 로드만은 사용 가능한.NET Core 플랫폼 os username을 요청할 수 없습니다.
  
동의에 대 한 자세한 내용은 참조 하세요. [v2.0 권한 및 동의](https://docs.microsoft.com/azure/active-directory/develop/v2-permissions-and-consent)

### <a name="how-to-use-it"></a>사용 방법

일반적으로 매개 변수가 하나만 필요한 (`scopes`). Windows 관리자가 정책을 설정 하는 방법에 따라 단 수 windows 컴퓨터에 응용 프로그램에 로그인 한 사용자를 조회할 수 없는 가능한 합니다. 이 경우 두 번째 메서드를 사용 하 여 `.WithUsername()` -UPN 형식으로 로그인된 한 사용자의 사용자 이름을 전달 `joe@contoso.com`합니다.

다음을 제공 합니다. 현재 대부분의 경우를 예외를 가져올 수 있습니다 및 해당 완화 방법이 종류에 대 한 설명이 포함

```CSharp
static async Task GetATokenForGraph()
{
 string authority = "https://login.microsoftonline.com/contoso.com";
 string[] scopes = new string[] { "user.read" };
 IPublicClientApplication app = PublicClientApplicationBuilder
      .Create(clientId)
      .WithAuthority(authority)
      .Build();

 var accounts = await app.GetAccountsAsync();

 AuthenticationResult result = null;
 if (accounts.Any())
 {
  result = await app.AcquireTokenSilent(scopes, accounts.FirstOrDefault())
      .ExecuteAsync();
 }
 else
 {
  try
  {
   result = await app.AcquireTokenByIntegratedWindowsAuth(scopes)
      .ExecuteAsync(CancellationToken.None);
  }
  catch (MsalUiRequiredException ex)
  {
   // MsalUiRequiredException: AADSTS65001: The user or administrator has not consented to use the application
   // with ID '{appId}' named '{appName}'.Send an interactive authorization request for this user and resource.

   // you need to get user consent first. This can be done, if you are not using .NET Core (which does not have any Web UI)
   // by doing (once only) an AcquireToken interactive.

   // If you are using .NET core or don't want to do an AcquireTokenInteractive, you might want to suggest the user to navigate
   // to a URL to consent: https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id={clientId}&response_type=code&scope=user.read

   // AADSTS50079: The user is required to use multi-factor authentication.
   // There is no mitigation - if MFA is configured for your tenant and AAD decides to enforce it,
   // you need to fallback to an interactive flows such as AcquireTokenInteractive or AcquireTokenByDeviceCode
   }
   catch (MsalServiceException ex)
   {
    // Kind of errors you could have (in ex.Message)

    // MsalServiceException: AADSTS90010: The grant type is not supported over the /common or /consumers endpoints. Please use the /organizations or tenant-specific endpoint.
    // you used common.
    // Mitigation: as explained in the message from Azure AD, the authority needs to be tenanted or otherwise organizations

    // MsalServiceException: AADSTS70002: The request body must contain the following parameter: 'client_secret or client_assertion'.
    // Explanation: this can happen if your application was not registered as a public client application in Azure AD
    // Mitigation: in the Azure portal, edit the manifest for your application and set the `allowPublicClient` to `true`
   }
   catch (MsalClientException ex)
   {
      // Error Code: unknown_user Message: Could not identify logged in user
      // Explanation: the library was unable to query the current Windows logged-in user or this user is not AD or AAD
      // joined (work-place joined users are not supported).

      // Mitigation 1: on UWP, check that the application has the following capabilities: Enterprise Authentication,
      // Private Networks (Client and Server), User Account Information

      // Mitigation 2: Implement your own logic to fetch the username (e.g. john@contoso.com) and use the
      // AcquireTokenByIntegratedWindowsAuth form that takes in the username

      // Error Code: integrated_windows_auth_not_supported_managed_user
      // Explanation: This method relies on an a protocol exposed by Active Directory (AD). If a user was created in Azure
      // Active Directory without AD backing ("managed" user), this method will fail. Users created in AD and backed by
      // AAD ("federated" users) can benefit from this non-interactive method of authentication.
      // Mitigation: Use interactive authentication
   }
 }


 Console.WriteLine(result.Account.Username);
}
```

AcquireTokenByIntegratedWindowsAuthentication에 한정자가 가능한 목록을 참조 하세요. [AcquireTokenByIntegratedWindowsAuthParameterBuilder](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.apiconfig.acquiretokenbyintegratedwindowsauthparameterbuilder?view=azure-dotnet-preview#methods)

## <a name="username--password"></a>사용자 이름/암호

사용자 이름 및 암호를 제공 하 여 토큰을 얻을 수도 있습니다. 이 흐름은 제한 및 권장 되는 밖에도 계속 해야 하는 경우를 사용 합니다.

### <a name="this-flow-isnt-recommended"></a>이 흐름을 권장 하지 않습니다.

이 흐름은 다음과 **좋지** 하므로 사용자가 암호를 묻는 응용 프로그램 보안이 유지 되지 않습니다. 이 문제에 대 한 자세한 내용은 참조 하세요. [이 문서에서는](https://news.microsoft.com/features/whats-solution-growing-problem-passwords-says-microsoft/)합니다. Windows 도메인에 가입 된 컴퓨터에서 자동으로 토큰 획득 하기 위해 기본 흐름은 다음과 [통합 Windows 인증](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Integrated-Windows-Authentication)합니다. 그렇지 않은 경우 사용할 수도 있습니다 [장치 코드 흐름](https://aka.ms/msal-net-device-code-flow)

> 경우에이 유용한 경우도 (DevOps 시나리오)에 onw UI를 제공 하는 대화형 시나리오에서 사용자 이름/암호를 사용 하려는 경우 실제로 벗어나면 이동 하는 방법에 대 한 간주 해야 합니다. 사용자 이름/암호를 사용 하 여 사용자는 제공 접속 다양 한 작업:

> - 최신 id의 테 넌 트 핵심: 암호 fished 가져옵니다 재생 합니다. 이 개념을 가로챌 수 있는 공유 암호를 지정 했으므로 합니다.
> 암호 없는 호환 되지 않습니다.
> - 사용자가 MFA를 수행 해야 할 수 없습니다 (상호 작용이 없는)으로 로그인
> - 사용자가 single sign on 수행할 수 없습니다.

### <a name="constraints"></a>제약 조건

다음과 같은 제약 조건이 적용 됩니다.

- 사용자 이름/암호 흐름은 조건부 액세스 및 multi-factor authentication을 사용 하 여 호환 되지 않습니다. 결과적으로 앱을 실행 하면 Azure AD 테 넌 트에서 테 넌 트 관리자가 multi-factor authentication 인증을 필요로 하는이 흐름을 사용할 수 없습니다. 대부분의 조직에서는 이렇게 합니다.
- 작업에 대해서만 작동 및 학교 계정 (MSA)
- .NET 데스크톱 및.net core, 있지만 없습니다 UWP 흐름 수

### <a name="b2c-specifics"></a>B2C 세부 정보

[ROPC B2C 사용에 대 한 자세한 내용은](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/AAD-B2C-specifics#resource-owner-password-credentials-ropc-with-b2c)합니다.

### <a name="how-to-use-it"></a>사용 하는 방법

`IPublicClientApplication`메서드를 포함합니다. `AcquireTokenByUsernamePassword`

다음 예제는 간소화 된 대/소문자 표시

```CSharp
static async Task GetATokenForGraph()
{
 string authority = "https://login.microsoftonline.com/contoso.com";
 string[] scopes = new string[] { "user.read" };
 IPublicClientApplication app;
 app = PublicClientApplicationBuild.Create(clientId)
       .WithAuthority(authority)
       .Build();
 var accounts = await app.GetAccountsAsync();

 AuthenticationResult result = null;
 if (accounts.Any())
 {
  result = await app.AcquireTokenSilent(scopes, accounts.FirstOrDefault())
                    .ExecuteAsync();
 }
 else
 {
  try
  {
   var securePassword = new SecureString();
   foreach (char c in "dummy")        // you should fetch the password
    securePassword.AppendChar(c);  // keystroke by keystroke

   result = await app.AcquireTokenByUsernamePassword(scopes,
                                                    "joe@contoso.com",
                                                     securePassword)
                      .ExecuteAsync();
  }
  catch(MsalException)
  {
   // See details below
  }
 }
 Console.WriteLine(result.Account.Username);
}
```

다음을 제공 합니다. 현재 대부분의 경우를 예외를 가져올 수 있습니다 및 해당 완화 방법이 종류에 대 한 설명이 포함

```CSharp
static async Task GetATokenForGraph()
{
 string authority = "https://login.microsoftonline.com/contoso.com";
 string[] scopes = new string[] { "user.read" };
 IPublicClientApplication app;
 app = PublicClientApplicationBuild.Create(clientId)
                                   .WithAuthority(authority)
                                   .Build();
 var accounts = await app.GetAccountsAsync();

 AuthenticationResult result = null;
 if (accounts.Any())
 {
  result = await app.AcquireTokenSilent(scopes, accounts.FirstOrDefault())
                    .ExecuteAync();
 }
 else
 {
  try
  {
   var securePassword = new SecureString();
   foreach (char c in "dummy")        // you should fetch the password keystroke
    securePassword.AppendChar(c);  // by keystroke

   result = await app.AcquireTokenByUsernamePassword(scopes,
                                                    "joe@contoso.com",
                                                    securePassword)
                    .ExecuteAsync();
  }
  catch (MsalUiRequiredException ex) when (ex.Message.Contains("AADSTS65001"))
  {
   // Here are the kind of error messages you could have, and possible mitigations

   // ------------------------------------------------------------------------
   // MsalUiRequiredException: AADSTS65001: The user or administrator has not consented to use the application
   // with ID '{appId}' named '{appName}'. Send an interactive authorization request for this user and resource.

   // Mitigation: you need to get user consent first. This can be done either statically (through the portal),
   /// or dynamically (but this requires an interaction with Azure AD, which is not possible with
   // the username/password flow)
   // Statically: in the portal by doing the following in the "API permissions" tab of the application registration:
   // 1. Click "Add a permission" and add all the delegated permissions corresponding to the scopes you want (for instance
   // User.Read and User.ReadBasic.All)
   // 2. Click "Grant/revoke admin consent for <tenant>") and click "yes".
   // Dynamically, if you are not using .NET Core (which does not have any Web UI) by
   // calling (once only) AcquireTokenInteractive.
   // remember that Username/password is for public client applications that is desktop/mobile applications.
   // If you are using .NET core or don't want to call AcquireTokenInteractive, you might want to:
   // - use device code flow (See https://aka.ms/msal-net-device-code-flow)
   // - or suggest the user to navigate to a URL to consent: https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id={clientId}&response_type=code&scope=user.read
   // ------------------------------------------------------------------------

   // ------------------------------------------------------------------------
   // ErrorCode: invalid_grant
   // SubError: basic_action
   // MsalUiRequiredException: AADSTS50079: The user is required to use multi-factor authentication.
   // The tenant admin for your organization has chosen to oblige users to perform multi-factor authentication.
   // Mitigation: none for this flow
   // Your application cannot use the Username/Password grant.
   // Like in the previous case, you might want to use an interactive flow (AcquireTokenInteractive()),
   // or Device Code Flow instead.
   // Note this is one of the reason why using username/password is not recommended;
   // ------------------------------------------------------------------------

   // ------------------------------------------------------------------------
   // ex.ErrorCode: invalid_grant
   // subError: null
   // Message = "AADSTS70002: Error validating credentials.
   // AADSTS50126: Invalid username or password
   // In the case of a managed user (user from an Azure AD tenant opposed to a
   // federated user, which would be owned
   // in another IdP through ADFS), the user has entered the wrong password
   // Mitigation: ask the user to re-enter the password
   // ------------------------------------------------------------------------

   // ------------------------------------------------------------------------
   // ex.ErrorCode: invalid_grant
   // subError: null
   // MsalServiceException: ADSTS50034: To sign into this application the account must be added to
   // the {domainName} directory.
   // or The user account does not exist in the {domainName} directory. To sign into this application,
   // the account must be added to the directory.
   // The user was not found in the directory
   // Explanation: wrong username
   // Mitigation: ask the user to re-enter the username.
   // ------------------------------------------------------------------------
  }
  catch (MsalServiceException ex) when (ex.ErrorCode == "invalid_request")
  {
   // ------------------------------------------------------------------------
   // AADSTS90010: The grant type is not supported over the /common or /consumers endpoints.
   // Please use the /organizations or tenant-specific endpoint.
   // you used common.
   // Mitigation: as explained in the message from Azure AD, the authority you use in the application needs
   // to be tenanted or otherwise "organizations". change the
   // "Tenant": property in the appsettings.json to be a GUID (tenant Id), or domain name (contoso.com)
   // if such a domain is registered with your tenant
   // or "organizations", if you want this application to sign-in users in any Work and School accounts.
   // ------------------------------------------------------------------------

  }
  catch (MsalServiceException ex) when (ex.ErrorCode == "unauthorized_client")
  {
   // ------------------------------------------------------------------------
   // AADSTS700016: Application with identifier '{clientId}' was not found in the directory '{domain}'.
   // This can happen if the application has not been installed by the administrator of the tenant or consented
   // to by any user in the tenant.
   // You may have sent your authentication request to the wrong tenant
   // Cause: The clientId in the appsettings.json might be wrong
   // Mitigation: check the clientId and the app registration
   // ------------------------------------------------------------------------
  }
  catch (MsalServiceException ex) when (ex.ErrorCode == "invalid_client")
  {
   // ------------------------------------------------------------------------
   // AADSTS70002: The request body must contain the following parameter: 'client_secret or client_assertion'.
   // Explanation: this can happen if your application was not registered as a public client application in Azure AD
   // Mitigation: in the Azure portal, edit the manifest for your application and set the `allowPublicClient` to `true`
   // ------------------------------------------------------------------------
  }
  catch (MsalServiceException)
  {
   throw;
  }

  catch (MsalClientException ex) when (ex.ErrorCode == "unknown_user_type")
  {
   // Message = "Unsupported User Type 'Unknown'. Please see https://aka.ms/msal-net-up"
   // The user is not recognized as a managed user, or a federated user. Azure AD was not
   // able to identify the IdP that needs to process the user
   throw new ArgumentException("U/P: Wrong username", ex);
  }
  catch (MsalClientException ex) when (ex.ErrorCode == "user_realm_discovery_failed")
  {
   // The user is not recognized as a managed user, or a federated user. Azure AD was not
   // able to identify the IdP that needs to process the user. That's for instance the case
   // if you use a phone number
   throw new ArgumentException("U/P: Wrong username", ex);
  }
  catch (MsalClientException ex) when (ex.ErrorCode == "unknown_user")
  {
   // the username was probably empty
   // ex.Message = "Could not identify the user logged into the OS. See https://aka.ms/msal-net-iwa for details."
   throw new ArgumentException("U/P: Wrong username", ex);
  }
  catch (MsalClientException ex) when (ex.ErrorCode == "parsing_wstrust_response_failed")
  {
   // ------------------------------------------------------------------------
   // In the case of a Federated user (that is owned by a federated IdP, as opposed to a managed user owned in an Azure AD tenant)
   // ID3242: The security token could not be authenticated or authorized.
   // The user does not exist or has entered the wrong password
   // ------------------------------------------------------------------------
  }
 }

 Console.WriteLine(result.Account.Username);
}
```

에 적용할 수 있는 모든 한정자에 대 한 내용은 `AcquireTokenByUsernamePassword`를 참조 하세요 [AcquireTokenByUsernamePasswordParameterBuilder](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.apiconfig.acquiretokenbyusernamepasswordparameterbuilder?view=azure-dotnet-preview#methods)

## <a name="command-line-tool-without-web-browser"></a>명령줄 도구 (웹 브라우저) 없음

### <a name="device-code-flow-why-and-how"></a>장치 코드 흐름 이유? 방법과?

(없는 웹 컨트롤) 명령줄 도구를 작성 하는 경우 및 없거나 이전 흐름을 사용 하지 않으려는 사용 해야 `AcquireTokenWithDeviceCode`합니다.

Azure AD 사용 하 여 대화형 인증은 웹 브라우저 필요 (자세한 내용은 참조 하십시오 [웹 브라우저 사용](https://aka.ms/msal-net-uses-web-browser)). 그러나 장치 또는 웹 브라우저를 제공 하지 않는 운영 체제에서 사용자를 인증 하려면 장치 코드 흐름 수가 있습니다 (예를 들어 다른 컴퓨터 또는 휴대폰)에 서명 하는 다른 장치를 사용 하 여 대화형으로. 장치 코드 흐름을 사용 하면 응용 프로그램이 특히 이러한 장치/OS를 위한 2 단계 프로세스를 통해 토큰을 가져옵니다. 이러한 응용 프로그램의 예로 iOT, 또는 명령줄 도구 (CLI)에서 실행 중인 응용 프로그램입니다. 있다는 아이디어가입니다.

1. 사용자 인증이 필요할 때마다 앱 코드를 제공 하 고 다른 장치 (예: 인터넷에 연결 된 smartphone)를 사용 하 여 URL로 이동 하도록 요청 합니다 (예를 들어 `https://microsoft.com/devicelogin`) 있는 사용자를 될 하 라는 메시지가 표시 코드를 입력 합니다. 완료 웹 페이지 인해 필요한 경우 동의 확인 프롬프트 및 multi-factor authentication을 포함 하는 일반적인 인증 환경 안내 합니다.

2. 인증이 성공 하면 필요한 웹 API 호출을 수행 하는 데 사용할 및 명령줄 앱 인 백 채널을 통해 필요한 토큰을 받게 됩니다.

### <a name="code"></a>코드

`IPublicClientApplication`라는 메서드를 포함 합니다. `AcquireTokenWithDeviceCode`

```CSharp
 AcquireTokenWithDeviceCode(IEnumerable<string> scopes,
                            Func<DeviceCodeResult, Task> deviceCodeResultCallback)
```

이 메서드는 매개 변수로 사용 합니다.

- `scopes` 에 대 한 액세스 토큰을 요청 하려면
- 받을 콜백 합니다 `DeviceCodeResult`

  ![Image](https://user-images.githubusercontent.com/13203188/56024968-7af1b980-5d11-11e9-84c2-5be2ef306dc5.png)

다음 샘플 코드는 예외를 가져올 수 있습니다 및 완화 방법 종류에 대 한 설명이 포함 최신 경우를 표시 합니다.

```CSharp
static async Task<AuthenticationResult> GetATokenForGraph()
{
 string authority = "https://login.microsoftonline.com/contoso.com";
 string[] scopes = new string[] { "user.read" };
 IPublicClientApplication pca = PublicClientApplicationBuilder
      .Create(clientId)
      .WithAuthority(authority)
      .Build();

 AuthenticationResult result = null;
 var accounts = await app.GetAccountsAsync();

 // All AcquireToken* methods store the tokens in the cache, so check the cache first
 try
 {
  result = await app.AcquireTokenSilent(scopes, accounts.FirstOrDefault())
       .ExecuteAsync();
 }
 catch (MsalUiRequiredException ex)
 {
  // A MsalUiRequiredException happened on AcquireTokenSilent.
  // This indicates you need to call AcquireTokenInteractive to acquire a token
  System.Diagnostics.Debug.WriteLine($"MsalUiRequiredException: {ex.Message}");
 }

 try
 {
  result = await app.AcquireTokenWithDeviceCode(scopes,
      deviceCodeCallback =>
  {
       // This will print the message on the console which tells the user where to go sign-in using
       // a separate browser and the code to enter once they sign in.
       // The AcquireTokenWithDeviceCode() method will poll the server after firing this
       // device code callback to look for the successful login of the user via that browser.
       // This background polling (whose interval and timeout data is also provided as fields in the
       // deviceCodeCallback class) will occur until:
       // * The user has successfully logged in via browser and entered the proper code
       // * The timeout specified by the server for the lifetime of this code (typically ~15 minutes) has been reached
       // * The developing application calls the Cancel() method on a CancellationToken sent into the method.
       //   If this occurs, an OperationCanceledException will be thrown (see catch below for more details).
       Console.WriteLine(deviceCodeResult.Message);
       return Task.FromResult(0);
  }).ExecuteAsync();

  Console.WriteLine(result.Account.Username);
  return result;
 }
 catch (MsalServiceException ex)
 {
  // Kind of errors you could have (in ex.Message)

  // AADSTS50059: No tenant-identifying information found in either the request or implied by any provided credentials.
  // Mitigation: as explained in the message from Azure AD, the authoriy needs to be tenanted. you have probably created
  // your public client application with the following authorities:
  // https://login.microsoftonline.com/common or https://login.microsoftonline.com/organizations

  // AADSTS90133: Device Code flow is not supported under /common or /consumers endpoint.
  // Mitigation: as explained in the message from Azure AD, the authority needs to be tenanted

  // AADSTS90002: Tenant <tenantId or domain you used in the authority> not found. This may happen if there are
  // no active subscriptions for the tenant. Check with your subscription administrator.
  // Mitigation: if you have an active subscription for the tenant this might be that you have a typo in the
  // tenantId (GUID) or tenant domain name.
 }
 catch (OperationCanceledException ex)
 {
  // If you use a CancellationToken, and call the Cancel() method on it, then this may be triggered
  // to indicate that the operation was cancelled.
  // See https://docs.microsoft.com/dotnet/standard/threading/cancellation-in-managed-threads
  // for more detailed information on how C# supports cancellation in managed threads.
 }
 catch (MsalClientException ex)
 {
  // Verification code expired before contacting the server
  // This exception will occur if the user does not manage to sign-in before a time out (15 mins) and the
  // call to `AcquireTokenWithDeviceCode` is not cancelled in between
 }
}
```

## <a name="file-based-token-cache"></a>파일 기반 토큰 캐시

MSAL.NET을 메모리 내 토큰 캐시를 기본적으로 제공 됩니다.

### <a name="serialization-is-customizable-in-windows-desktop-apps-and-web-appsweb-apis"></a>Serialization은 Windows 데스크톱 앱 및 웹 앱/웹 Api에서 사용자 지정 가능

.NET Framework 및.NET core의 경우, 추가 하지 않으면 메모리 내 토큰 캐시를 응용 프로그램의 기간 동안 지속 됩니다. MSAL.NET 기억 serialization 기본적으로 제공 되지 않는 이유를 이해 하기 콘솔 또는 Windows 응용 프로그램 (파일 시스템에 액세스 해야 함), 데스크톱/core 응용 프로그램 일 수 있습니다 **뿐만 아니라** 웹 응용 프로그램 또는 웹 API입니다. 이러한 웹 앱 및 web Api는 데이터베이스에 분산 된 캐시, redis cache 등과 같은 몇 가지 특정 캐시 메커니즘에 사용할 수 있습니다. .NET 데스크톱 또는 Core 응용 프로그램 영구 토큰 캐시에 serialization을 사용자 지정 해야 합니다.

클래스 및 인터페이스를 토큰 캐시 serialization에 관련 된 다음 유형이 있습니다.

- ``ITokenCache``를 serialize 하거나 deserialize 다양 한 형식에서 캐시 방법 뿐만 아니라 토큰 캐시 serialization 요청 알림을 신청할 이벤트를 정의 하는 (ADAL v3.0, MSAL 2.x 및 MSAL 3.x = ADAL v5.0)
- ``TokenCacheCallback`` 콜백을 넘어갑니다 이벤트 serialization을 처리할 수 있도록 합니다. 형식의 인수를 사용 하 여 호출할 수 ``TokenCacheNotificationArgs``입니다.
- ``TokenCacheNotificationArgs`` 제공 된 ``ClientId`` 응용 프로그램 및 토큰을 사용할 수 있는 사용자에 대 한 참조

  ![Image](https://user-images.githubusercontent.com/13203188/56027172-d58d1480-5d15-11e9-8ada-c0292f1800b3.png)

> [!IMPORTANT]
> MSAL.NET 토큰 캐시를 만들고 사용 하 여 제공 합니다 `IToken` 응용 프로그램을 호출 하는 경우의 cache `GetUserTokenCache` 및 `GetAppTokenCache` 메서드. 인터페이스를 구현 하는 것이 없게 합니다. 사용자의 책임을 사용자 지정 토큰 캐시에 대 한 serialization을 구현 하는 경우 다음과 같습니다.
>
> - 에 대응할 `BeforeAccess` 고 `AfterAccess` "이벤트"입니다. 합니다`BeforeAccess` 대리자가 반면 캐시를 deserialize 해야 하는 `AfterAccess` 하나는 캐시를 직렬화 하는 작업을 담당 합니다.
> - 이러한 이벤트의 일부 저장 하거나 전달 되는 이벤트 인수를 통해 원하는 어떤 저장소에 blob을 로드 합니다.

전략은 공용 클라이언트 응용 프로그램 (데스크톱) 또는 기밀 클라이언트 응용 프로그램 (웹 앱/웹 API에 디먼 앱)에 대 한 토큰 캐시에 대 한 serialization을 작성 하는 경우에 따라 다양 합니다.

MSAL V2.x 이후 몇 가지 옵션이 있습니다, MSAL.NET 형식 (통합된 형식 캐시 플랫폼 간 뿐만 MSAL을 사용 하 여 공통 된)에 캐시를 serialize 하려는 경우 또는 지원 하려는 경우에 따라 합니다 [레거시](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Token-cache-serialization) ADAL V3의 토큰 캐시를 직렬화 합니다.

사용자 지정 토큰 캐시 serialization ADAL.NET 간 SSO 상태 공유 3.x ADAL.NET 5.x 및 MSAL.NET 다음 샘플의 일부 설명 되어: [active-directory-dotnet-v1-to-v2](https://github.com/Azure-Samples/active-directory-dotnet-v1-to-v2)

### <a name="simple-token-cache-serialization-msal-only"></a>단순 토큰 캐시 serialization (MSAL에만 해당)

다음은 데스크톱 응용 프로그램에 대 한 토큰 캐시의 사용자 지정 직렬화를 구현 하는 것은 순진한의 예입니다. 다음 응용 프로그램과 같은 폴더에 파일의 사용자 토큰 캐시입니다.

호출 하 여 serialization을 활성화 하는 응용 프로그램을 빌드한 후 ``TokenCacheHelper.EnableSerialization()`` 응용 프로그램 전달 `UserTokenCache`

```CSharp
app = PublicClientApplicationBuilder.Create(ClientId)
    .Build();
TokenCacheHelper.EnableSerialization(app.UserTokenCache);
```

이 도우미 클래스를 다음 코드 조각은 다음과 같습니다.

```CSharp
static class TokenCacheHelper
 {
  public static void EnableSerialization(ITokenCache tokenCache)
  {
   tokenCache.SetBeforeAccess(BeforeAccessNotification);
   tokenCache.SetAfterAccess(AfterAccessNotification);
  }

  /// <summary>
  /// Path to the token cache
  /// </summary>
  public static readonly string CacheFilePath = System.Reflection.Assembly.GetExecutingAssembly().Location + ".msalcache.bin3";

  private static readonly object FileLock = new object();


  private static void BeforeAccessNotification(TokenCacheNotificationArgs args)
  {
   lock (FileLock)
   {
    args.TokenCache.DeserializeMsalV3(File.Exists(CacheFilePath)
            ? ProtectedData.Unprotect(File.ReadAllBytes(CacheFilePath),
                                      null,
                                      DataProtectionScope.CurrentUser)
            : null);
   }
  }

  private static void AfterAccessNotification(TokenCacheNotificationArgs args)
  {
   // if the access operation resulted in a cache update
   if (args.HasStateChanged)
   {
    lock (FileLock)
    {
     // reflect changesgs in the persistent store
     File.WriteAllBytes(CacheFilePath,
                         ProtectedData.Protect(args.TokenCache.SerializeMsalV3(),
                                                 null,
                                                 DataProtectionScope.CurrentUser)
                         );
    }
   }
  }
 }
```

공용 클라이언트 응용 프로그램 (Windows, Mac 및 linux에서 실행 되는 데스크톱 응용 프로그램)에 대 한 파일 기반 serializer는에서 사용할 수 있는 제품 품질 토큰 캐시의 미리 보기는 [Microsoft.Identity.Client.Extensions.Msal](https://github.com/AzureAD/microsoft-authentication-extensions-for-dotnet/tree/master/src/Microsoft.Identity.Client.Extensions.Msal) 오픈 소스 라이브러리입니다. 다음 nuget 패키지에서 응용 프로그램에 포함할 수 있습니다. [Microsoft.Identity.Client.Extensions.Msal](https://www.nuget.org/packages/Microsoft.Identity.Client.Extensions.Msal/).

> 고 지 사항입니다. Microsoft.Identity.Client.Extensions.Msal 라이브러리 MSAL.NET을 통해 확장입니다. 이러한 라이브러리의 클래스를 만들 수 있습니다 반송 MSAL.NET에 나중에 그대로 또는 주요 변경 내용.

### <a name="dual-token-cache-serialization-msal-unified-cache--adal-v3"></a>이중 토큰 캐시 serialization (unified MSAL 캐시 + ADAL V3)

형식 캐시 둘 다의 통합을 사용 하 여 토큰 캐시 serialization을 구현 하려는 경우 (ADAL.NET에 공통적으로 적용 4.x 및 MSAL.NET 2.x의 경우와 동일한 세대의 또는 동일한 플랫폼에서 이전에 다른 MSALs), 다음 코드에서 영감 얻기 수 :

```CSharp
string appLocation = Path.GetDirectoryName(Assembly.GetEntryAssembly().Location;
string cacheFolder = Path.GetFullPath(appLocation) + @"..\..\..\..");
string adalV3cacheFileName = Path.Combine(cacheFolder, "cacheAdalV3.bin");
string unifiedCacheFileName = Path.Combine(cacheFolder, "unifiedCache.bin");

IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
                                    .Build();
FilesBasedTokenCacheHelper.EnableSerialization(app.UserTokenCache,
                                               unifiedCacheFileName,
                                               adalV3cacheFileName);

```

이 이번 도우미 클래스는 다음 코드와 같습니다.

```CSharp
using System;
using System.IO;
using System.Security.Cryptography;
using Microsoft.Identity.Client;

namespace CommonCacheMsalV3
{
 /// <summary>
 /// Simple persistent cache implementation of the dual cache serialization (ADAL V3 legacy
 /// and unified cache format) for a desktop applications (from MSAL 2.x)
 /// </summary>
 static class FilesBasedTokenCacheHelper
 {
  /// <summary>
  /// Get the user token cache
  /// </summary>
  /// <param name="adalV3CacheFileName">File name where the cache is serialized with the
  /// ADAL V3 token cache format. Can
  /// be <c>null</c> if you don't want to implement the legacy ADAL V3 token cache
  /// serialization in your MSAL 2.x+ application</param>
  /// <param name="unifiedCacheFileName">File name where the cache is serialized
  /// with the Unified cache format, common to
  /// ADAL V4 and MSAL V2 and above, and also across ADAL/MSAL on the same platform.
  ///  Should not be <c>null</c></param>
  /// <returns></returns>
  public static void EnableSerialization(ITokenCache cache, string unifiedCacheFileName, string adalV3CacheFileName)
  {
   usertokenCache = cache;
   UnifiedCacheFileName = unifiedCacheFileName;
   AdalV3CacheFileName = adalV3CacheFileName;

   usertokenCache.SetBeforeAccess(BeforeAccessNotification);
   usertokenCache.SetAfterAccess(AfterAccessNotification);
  }

  /// <summary>
  /// Token cache
  /// </summary>
  static ITokenCache usertokenCache;

  /// <summary>
  /// File path where the token cache is serialized with the unified cache format
  /// (ADAL.NET V4, MSAL.NET V3)
  /// </summary>
  public static string UnifiedCacheFileName { get; private set; }

  /// <summary>
  /// File path where the token cache is serialized with the legacy ADAL V3 format
  /// </summary>
  public static string AdalV3CacheFileName { get; private set; }

  private static readonly object FileLock = new object();

  public static void BeforeAccessNotification(TokenCacheNotificationArgs args)
  {
   lock (FileLock)
   {
    args.TokenCache.DeserializeAdalV3(ReadFromFileIfExists(AdalV3CacheFileName));
    try
    {
     args.TokenCache.DeserializeMsalV3(ReadFromFileIfExists(UnifiedCacheFileName));
    }
    catch(Exception ex)
    {
     // Compatibility with the MSAL v2 cache if you used one
     args.TokenCache.DeserializeMsalV2(ReadFromFileIfExists(UnifiedCacheFileName));
    }
   }
  }

  public static void AfterAccessNotification(TokenCacheNotificationArgs args)
  {
   // if the access operation resulted in a cache update
   if (args.HasStateChanged)
   {
    lock (FileLock)
    {
     WriteToFileIfNotNull(UnifiedCacheFileName, args.TokenCache.SerializeMsalV3());
     if (!string.IsNullOrWhiteSpace(AdalV3CacheFileName))
     {
      WriteToFileIfNotNull(AdalV3CacheFileName, args.TokenCache.SerializeAdalV3());
     }
    }
   }
  }

  /// <summary>
  /// Read the content of a file if it exists
  /// </summary>
  /// <param name="path">File path</param>
  /// <returns>Content of the file (in bytes)</returns>
  private static byte[] ReadFromFileIfExists(string path)
  {
   byte[] protectedBytes = (!string.IsNullOrEmpty(path) && File.Exists(path))
       ? File.ReadAllBytes(path) : null;
   byte[] unprotectedBytes = encrypt ?
       ((protectedBytes != null) ? ProtectedData.Unprotect(protectedBytes, null, DataProtectionScope.CurrentUser) : null)
       : protectedBytes;
   return unprotectedBytes;
  }

  /// <summary>
  /// Writes a blob of bytes to a file. If the blob is <c>null</c>, deletes the file
  /// </summary>
  /// <param name="path">path to the file to write</param>
  /// <param name="blob">Blob of bytes to write</param>
  private static void WriteToFileIfNotNull(string path, byte[] blob)
  {
   if (blob != null)
   {
    byte[] protectedBytes = encrypt
      ? ProtectedData.Protect(blob, null, DataProtectionScope.CurrentUser)
      : blob;
    File.WriteAllBytes(path, protectedBytes);
   }
   else
   {
    File.Delete(path);
   }
  }

  // Change if you want to test with an un-encrypted blob (this is a json format)
  private static bool encrypt = true;
 }
}
```

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [데스크톱 앱에서 web API 호출](scenario-desktop-call-api.md)
