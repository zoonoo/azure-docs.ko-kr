---
title: 웹 Api를 호출 하는 데스크톱 앱 (앱에 대 한 토큰 획득)-Microsoft identity platform
description: 웹 Api를 호출 하는 데스크톱 앱을 빌드하는 방법 알아보기 (앱에 대 한 토큰 획득)
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
ms.date: 07/16/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 05596365dfa011675f38beda2435fdda1a53a5a3
ms.sourcegitcommit: bafb70af41ad1326adf3b7f8db50493e20a64926
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/25/2019
ms.locfileid: "68488857"
---
# <a name="desktop-app-that-calls-web-apis---acquire-a-token"></a>웹 Api를 호출 하는 데스크톱 앱-토큰 획득

빌드 `IPublicClientApplication`를 완료 한 후에는이를 사용 하 여 웹 API를 호출 하는 데 사용할 토큰을 가져옵니다.

## <a name="recommended-pattern"></a>권장 패턴

Web API는에 의해 `scopes`정의 됩니다. 응용 프로그램에서 제공 하는 환경에 관계 없이 사용 하려는 패턴은 다음과 같습니다.

- 을 호출 하 여 토큰 캐시에서 토큰을 가져옵니다.`AcquireTokenSilent`
- 이 호출이 실패 하면 사용 하려는 `AcquireToken` 흐름을 사용 합니다 (여기서는로 `AcquireTokenXX`표시 됨).

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

이제 데스크톱 응용 프로그램에서 토큰을 획득 하는 다양 한 방법에 대 한 자세한 내용은 다음과 같습니다.

## <a name="acquiring-a-token-interactively"></a>대화형으로 토큰 가져오기

다음 예에서는 Microsoft Graph를 사용 하 여 사용자 프로필을 읽기 위해 대화형으로 토큰을 가져오는 최소한의 코드를 보여 줍니다.

```CSharp
string[] scopes = new string[] {"user.read"};
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

`AcquireTokenInteractive`에는 토큰을 필요로 ``scopes``하는 범위를 정의 하는 문자열의 열거형을 포함 하는 필수 매개 변수가 하나 뿐입니다. Microsoft Graph에 대 한 토큰 인 경우 필요한 범위는 "권한" 섹션에서 각 Microsoft Graph API의 api 참조에서 찾을 수 있습니다. 예를 들어 [사용자의 연락처를 나열](https://developer.microsoft.com/graph/docs/api-reference/v1.0/api/user_list_contacts)하려면 "사용자. 읽기", "연락처. 읽기" 범위를 사용 해야 합니다. 참고 항목 [Microsoft Graph 사용 권한 참조](https://developer.microsoft.com/graph/docs/concepts/permissions_reference)합니다.

Android에서는 상호 작용 후에 토큰을 다시 해당 부모 작업으로 `.WithParentActivityOrWindow`다시 가져오기 위해 부모 작업 (아래 참조)도 지정 해야 합니다. 지정 하지 않으면를 호출할 `.ExecuteAsync()`때 예외가 throw 됩니다.

### <a name="specific-optional-parameters"></a>특정 선택적 매개 변수

#### <a name="withparentactivityorwindow"></a>WithParentActivityOrWindow

대화형으로 UI가 중요 합니다. `AcquireTokenInteractive`에는 선택적 매개 변수 하나를 사용 하 여이를 지 원하는 플랫폼에서 부모 UI를 지정할 수 있습니다. 데스크톱 응용 프로그램 `.WithParentActivityOrWindow` 에서 사용 되는 경우 플랫폼에 따라 다른 형식이 사용 됩니다.

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

- `object` .NET Standard에서 예상 `Activity` 되는는 Android, `NSWindow` a `UIViewController` `IWin32Window`,iOS , MAC의 경우, Windows 의경우입니다.`IntPr`
- Windows에서는 포함 된 브라우저가 적절 `AcquireTokenInteractive` 한 ui 동기화 컨텍스트를 가져오기 위해 ui 스레드에서를 호출 해야 합니다.  UI 스레드에서를 호출 하지 않으면 메시지에서 UI를 사용 하 여 제대로 또는 교착 상태 시나리오를 펌프 하지 않을 수 있습니다. Ui 스레드를 사용 하지 않는 경우 ui 스레드에서 msal을 호출 하는 한 가지 방법은 WPF에서를 `Dispatcher` 사용 하는 것입니다.
- Wpf를 사용 하는 경우 wpf 컨트롤에서 창을 가져오려면 클래스를 사용할 `WindowInteropHelper.Handle` 수 있습니다. 그러면 WPF 컨트롤 (`this`)에서 호출 됩니다.
  
  ```CSharp
  result = await app.AcquireTokenInteractive(scopes)
                    .WithParentActivityOrWindow(new WindowInteropHelper(this).Handle)
                    .ExecuteAsync();
  ```

#### <a name="withprompt"></a>WithPrompt

`WithPrompt()`는 프롬프트를 지정 하 여 사용자와의 상호 작용을 제어 하는 데 사용 됩니다.

<img src="https://user-images.githubusercontent.com/13203188/53438042-3fb85700-39ff-11e9-9a9e-1ff9874197b3.png" width="25%" />

클래스는 다음 상수를 정의 합니다.

- ``SelectAccount``: STS가 사용자에 게 세션이 있는 계정을 포함 하는 계정 선택 대화 상자를 표시 하도록 합니다. 이 옵션은 응용 프로그램 개발자가 다양 한 id 중에서 선택할 수 있도록 하려는 경우에 유용 합니다. 이 옵션은 id 공급자에 게 ``prompt=select_account`` 보낼 msal을 구동 합니다. 이 옵션은 기본적으로 사용 가능한 정보 (계정, 사용자에 대 한 세션의 상태 등)를 기반으로 최상의 환경을 제공 하는 데 유용 합니다. ...). 이 작업을 수행 해야 하는 경우를 제외 하 고는 변경 하지 마십시오.
- ``Consent``: 응용 프로그램 개발자가 이전에 동의가 부여 된 경우에도 사용자에 게 동의 여부를 묻는 메시지를 표시 하도록 합니다. 이 경우 msal은 id 공급자 `prompt=consent` 에 게 보냅니다. 이 옵션은 조직에서 응용 프로그램이 사용 될 때마다 사용자에 게 동의 대화 상자가 표시 되는 일부 보안 중심 응용 프로그램에서 사용할 수 있습니다.
- ``ForceLogin``:이 사용자 프롬프트가 필요 하지 않은 경우에도 응용 프로그램 개발자가 서비스에서 자격 증명을 묻는 메시지를 표시 하도록 할 수 있습니다. 이 옵션은 토큰을 획득 하는 데 실패 하 여 사용자가 다시 로그인 할 수 있도록 하는 경우에 유용할 수 있습니다. 이 경우 msal은 id 공급자 `prompt=login` 에 게 보냅니다. 이는 조직에서 사용자가 응용 프로그램의 특정 부분에 액세스할 때마다 다시 로그인 하는 것을 요구 하는 일부 보안 중심 응용 프로그램에서 사용 되었습니다.
- ``Never``(.NET 4.5 및 WinRT에만 해당) 사용자에 게 메시지를 표시 하지 않고, 대신 숨겨진 포함 웹 보기에 저장 된 쿠키를 사용 하려고 합니다 (아래 참조). MSAL.NET의 웹 보기). 이 옵션을 사용 하면 오류가 발생할 수 있으며, `AcquireTokenInteractive` 이 경우 UI 조작이 필요 하다는 알림을 표시 하는 예외를 throw 하 고 다른 `Prompt` 매개 변수를 사용 해야 합니다.
- ``NoPrompt``: 프롬프트를 id 공급자에 게 보내지 않습니다. 이 옵션은 Azure AD B2C 프로필 정책 편집에만 유용 합니다 ( [B2C 특정](https://aka.ms/msal-net-b2c-specificities)항목 참조).

#### <a name="withextrascopetoconsent"></a>WithExtraScopeToConsent

이 한정자는 사용자가 사전에 여러 리소스에 대 한 동의를 원하는 고급 시나리오에서 사용 됩니다 (일반적으로 MSAL.NET/Microsoft id platform v 2.0에서 사용 되는 증분 동의를 사용 하지 않음). 자세한 내용은 [방법: 사용자에 게 여러 리소스에 대 한 사전 동의가 필요](scenario-desktop-production.md#how-to-have--the-user-consent-upfront-for-several-resources)합니다.

```CSharp
var result = await app.AcquireTokenInteractive(scopesForCustomerApi)
                     .WithExtraScopeToConsent(scopesForVendorApi)
                     .ExecuteAsync();
```

#### <a name="withcustomwebui"></a>WithCustomWebUi

##### <a name="withcustomwebui-is-an-extensibility-point"></a>WithCustomWebUi는 확장성 지점입니다.

`WithCustomWebUi`는 공용 클라이언트 응용 프로그램에서 사용자 고유의 UI를 제공 하 고 사용자가 id 공급자의/권한 부여 끝점을 통해 로그인 하 고 동의할 수 있도록 하는 확장 지점입니다. MSAL.NET는 인증 코드를 교환 하 고 토큰을 가져올 수 있습니다. Visual Studio에서 파괴 응용 프로그램 (예를 들어 VS 피드백)이 웹 상호 작용을 제공 하도록 하는 데 사용 되며 대부분의 작업을 수행 하는 MSAL.NET로 유지 됩니다. UI 자동화를 제공 하려는 경우에도 사용할 수 있습니다. 공용 클라이언트 응용 프로그램에서 MSAL.NET는 PKCE standard ([RFC 7636-OAuth 공용 클라이언트의 코드 교환에 대 한 증명 키](https://tools.ietf.org/html/rfc7636))를 사용 하 여 보안이 적용 되도록 합니다. MSAL.NET만 코드를 사용할 수 있습니다.

  ```CSharp
  using Microsoft.Identity.Client.Extensions;
  ```

##### <a name="how-to-use-withcustomwebui"></a>WithCustomWebUi 사용 방법

을 사용 `.WithCustomWebUI`하려면 다음을 수행 해야 합니다.
  
  1. 인터페이스를 `ICustomWebUi` 구현 합니다 ( [여기](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/blob/053a98d16596be7e9ca1ab916924e5736e341fe8/src/Microsoft.Identity.Client/Extensibility/ICustomWebUI.cs#L32-L70)참조). 기본적으로 인증 코드 URL (MSAL.NET에서 `AcquireAuthorizationCodeAsync` 계산)을 허용 하는 하나의 메서드를 구현 하 여 사용자가 id 공급자와의 상호 작용을 수행한 다음 id 공급자의 url을 다시 반환 하도록 해야 합니다. 구현 (인증 코드 포함)을 다시 호출 했습니다. 문제가 발생 하는 경우에는 msal과 `MsalExtensionException` 의 적절 한 상호 작용을 위해 구현에서 예외를 throw 해야 합니다.
  2. 호출에서 사용자 지정 웹 UI의 `.WithCustomUI()` 인스턴스를 전달 하는 한정자를 사용할 수 있습니다. `AcquireTokenInteractive`

     ```CSharp
     result = await app.AcquireTokenInteractive(scopes)
                       .WithCustomWebUi(yourCustomWebUI)
                       .ExecuteAsync();
     ```

##### <a name="examples-of-implementation-of-icustomwebui-in-test-automation---seleniumwebui"></a>테스트 자동화의 ICustomWebUi 구현 예제-SeleniumWebUI

MSAL.NET 팀은이 확장성 메커니즘을 활용 하기 위해 UI 테스트를 다시 작성 했습니다. 관심이 있는 경우 MSAL.NET 소스 코드에서 [SeleniumWebUI](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/blob/053a98d16596be7e9ca1ab916924e5736e341fe8/tests/Microsoft.Identity.Test.Integration/Infrastructure/SeleniumWebUI.cs#L15-L160) 클래스를 살펴볼 수 있습니다.

#### <a name="other-optional-parameters"></a>기타 선택적 매개 변수

`AcquireTokenInteractive` [AcquireTokenInteractiveParameterBuilder](/dotnet/api/microsoft.identity.client.acquiretokeninteractiveparameterbuilder?view=azure-dotnet-preview#methods) 에 대 한 참조 설명서에서의 다른 모든 선택적 매개 변수에 대 한 자세한 정보

## <a name="integrated-windows-authentication"></a>Windows 통합 인증

도메인 또는 Azure AD 가입 컴퓨터에서 도메인 사용자에 로그인 하려면 다음을 사용 해야 합니다.

```csharp
AcquireTokenByIntegratedWindowsAuth(IEnumerable<string> scopes)
```

### <a name="constraints"></a>제약 조건

- AcquireTokenByIntegratedWindowsAuth (IWA)는 **페더레이션** 사용자만 사용할 수 있습니다. 즉, Active Directory에서 만들어지고 Azure Active Directory에 의해 지원 되는 사용자만 사용할 수 있습니다. AD 백업 관리 사용자 없이 AAD에서 직접 만든 사용자 **는** 이 인증 흐름을 사용할 수 없습니다. 이 제한은 사용자 이름/암호 흐름에 영향을 주지 않습니다.
- IWA는 .NET Framework, .NET Core 및 UWP 플랫폼용으로 작성 된 앱에 대 한 것입니다.
- IWA는 MFA (multi-factor authentication)를 바이패스 하지 않습니다. Mfa가 구성 된 경우 mfa에 사용자 조작이 필요 하기 때문에 mfa 챌린지가 필요한 경우 IWA가 실패할 수 있습니다.
  > [!NOTE]
  > 이 방법은 복잡 합니다. IWA는 비 대화형 이지만 MFA를 사용 하려면 사용자의 상호 작용이 필요 합니다. Id 공급자가 MFA를 수행 하도록 요청 하는 시간을 제어 하지 않습니다. 테 넌 트 관리자가 수행 합니다. 이러한 관찰에서 MFA는 VPN을 통해 회사 네트워크에 연결 되어 있지 않은 경우와 VPN을 통해 연결 된 경우에도 다른 국가에서 로그인 하는 경우에 필요 합니다. 결정적 규칙 집합이 필요 하지 않으며, Azure Active Directory AI를 사용 하 여 MFA가 필요한 지 여부를 지속적으로 파악 합니다. IWA이 실패 하면 사용자 프롬프트 (대화형 인증 또는 장치 코드 흐름)로 대체 해야 합니다.

- 에 전달 된 기관은 다음 `PublicClientApplicationBuilder` 을 충족 해야 합니다.
  - 테 넌 트를 사용 합니다 `tenant` . 여기서은테넌트ID또는테넌트와연결된도메인을나타내는guid입니다.`https://login.microsoftonline.com/{tenant}/`
  - 모든 회사 및 학교 계정 (`https://login.microsoftonline.com/organizations/`)에 대해

  > Microsoft 개인 계정은 지원 되지 않습니다 (/scommon 또는/소비자 테 넌 트를 사용할 수 없음).

- Windows 통합 인증은 자동 흐름입니다.
  - 응용 프로그램의 사용자가 응용 프로그램을 사용 하려면 이전에 동의한 해야 합니다.
  - 또는 테 넌 트 관리자가 응용 프로그램을 사용 하려면 테 넌 트의 모든 사용자에 게 이전에 동의한 해야 합니다.
  - 즉, 다음을 수행 합니다.
    - 개발자가 Azure Portal에 대 한 **권한 부여** 단추를 누르면 됩니다.
    - 또는 테 넌 트 관리자가 응용 프로그램 등록의 **API 사용 권한** 탭에 있는 **{테 넌 트 도메인}에 대 한 권한 부여/해지 관리자 동의** ( [웹 api 액세스 권한 추가](https://docs.microsoft.com/azure/active-directory/develop/quickstart-configure-app-access-web-apis#add-permissions-to-access-web-apis)참조)를 눌렀습니다.
    - 또는 사용자가 응용 프로그램에 동의할 수 있는 방법을 제공 했습니다 ( [개별 사용자 동의 요청](https://docs.microsoft.com/azure/active-directory/develop/v2-permissions-and-consent#requesting-individual-user-consent)참조).
    - 또는 테 넌 트 관리자가 응용 프로그램에 동의할 수 있는 방법을 제공 했습니다 ( [관리자 동의](https://docs.microsoft.com/azure/active-directory/develop/v2-permissions-and-consent#requesting-consent-for-an-entire-tenant)참조).

- 이 흐름은 .net 데스크톱, .net core 및 UWP (Windows 유니버설) 앱에 대해 사용 하도록 설정 됩니다. .Net core에서는 .NET Core 플랫폼이 사용자 이름을 OS에 요청할 수 없기 때문에 사용자 이름을 사용 하는 오버 로드만 사용할 수 있습니다.
  
승인에 대 한 자세한 내용은 v2.0 [사용 권한 및 동의](https://docs.microsoft.com/azure/active-directory/develop/v2-permissions-and-consent) 를 참조 하세요.

### <a name="how-to-use-it"></a>사용 방법

일반적으로 하나의 매개 변수 (`scopes`)만 필요 합니다. 그러나 Windows 관리자가 정책을 설정 하는 방법에 따라 windows 컴퓨터의 응용 프로그램에서 로그인 한 사용자를 조회할 수 없습니다. 이 경우 두 번째 방법을 `.WithUsername()` 사용 하 여 로그인 한 사용자의 사용자 이름을 UPN `joe@contoso.com`형식으로 전달 합니다.

다음 샘플에서는 가장 최신 사례를 제공 하 고, 얻을 수 있는 예외의 종류에 대 한 설명과 해당 완화 방법을 제공 합니다.

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

AcquireTokenByIntegratedWindowsAuthentication에서 사용할 수 있는 한정자 목록은 [AcquireTokenByIntegratedWindowsAuthParameterBuilder](/dotnet/api/microsoft.identity.client.acquiretokenbyintegratedwindowsauthparameterbuilder?view=azure-dotnet-preview#methods) 를 참조 하세요.

## <a name="username--password"></a>사용자 이름/암호

사용자 이름 및 암호를 제공 하 여 토큰을 가져올 수도 있습니다. 이 흐름은 제한적 이며 권장 되지 않지만 필요한 경우 사용 사례가 계속 사용 됩니다.

### <a name="this-flow-isnt-recommended"></a>이 흐름은 권장 되지 않습니다.

사용자에 게 암호를 묻는 응용 프로그램이 안전 하지 않으므로이 흐름은 사용 **하지 않는 것이 좋습니다** . 이 문제에 대 한 자세한 내용은 [이 문서](https://news.microsoft.com/features/whats-solution-growing-problem-passwords-says-microsoft/)를 참조 하세요. Windows 도메인 가입 컴퓨터에서 자동으로 토큰을 획득 하는 기본 흐름은 [Windows 통합 인증](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Integrated-Windows-Authentication)입니다. 그렇지 않으면 [장치 코드 흐름](https://aka.ms/msal-net-device-code-flow) 을 사용할 수도 있습니다.

> [!NOTE] 
> 이는 일부 경우에 유용 하지만 (DevOps 시나리오), onw UI를 제공 하는 대화형 시나리오에서 사용자 이름/암호를 사용 하려는 경우 그 밖으로 이동 하는 방법을 고려해 야 합니다. 사용자 이름/암호를 사용 하 여 여러 가지를 제공 합니다.
>
> - 최신 id의 핵심 테 넌 트: 암호는 fished을 가져온 후 재생 됩니다. 이 개념은 가로챌 수 있는 공유 암호의 개념입니다.
> 이는 passwordless와 호환 되지 않습니다.
> - MFA를 수행 해야 하는 사용자는 상호 작용이 없으므로 로그인 할 수 없습니다.
> - 사용자는 single sign-on을 수행할 수 없습니다.

### <a name="constraints"></a>제약 조건

다음 제약 조건도 적용 됩니다.

- 사용자 이름/암호 흐름은 조건부 액세스 및 multi-factor authentication과 호환 되지 않습니다. 따라서 테 넌 트 관리자가 다단계 인증을 필요로 하는 Azure AD 테 넌 트에서 앱이 실행 되는 경우이 흐름을 사용할 수 없습니다. 많은 조직에서이 작업을 수행 합니다.
- 회사 및 학교 계정 (MSA 아님)에 대해서만 작동 합니다.
- 흐름은 .net 데스크톱 및 .net core에서 사용할 수 있지만 UWP에서는 사용할 수 없습니다.

### <a name="b2c-specifics"></a>B2C 세부 정보

[ROPC를 B2C와 함께 사용 하는 방법에 대 한 자세한 정보](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/AAD-B2C-specifics#resource-owner-password-credentials-ropc-with-b2c).

### <a name="how-to-use-it"></a>사용 방법

`IPublicClientApplication`메서드를 포함 합니다.`AcquireTokenByUsernamePassword`

다음 샘플에서는 간단한 사례를 제공 합니다.

```CSharp
static async Task GetATokenForGraph()
{
 string authority = "https://login.microsoftonline.com/contoso.com";
 string[] scopes = new string[] { "user.read" };
 IPublicClientApplication app;
 app = PublicClientApplicationBuilder.Create(clientId)
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

다음 샘플에서는 가장 최신 사례를 제공 하 고, 얻을 수 있는 예외의 종류에 대 한 설명과 해당 완화 방법을 제공 합니다.

```CSharp
static async Task GetATokenForGraph()
{
 string authority = "https://login.microsoftonline.com/contoso.com";
 string[] scopes = new string[] { "user.read" };
 IPublicClientApplication app;
 app = PublicClientApplicationBuilder.Create(clientId)
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

에 `AcquireTokenByUsernamePassword`적용할 수 있는 모든 한정자에 대 한 자세한 내용은 [AcquireTokenByUsernamePasswordParameterBuilder](/dotnet/api/microsoft.identity.client.acquiretokenbyusernamepasswordparameterbuilder?view=azure-dotnet-preview#methods) 를 참조 하세요.

## <a name="command-line-tool-without-web-browser"></a>명령줄 도구 (웹 브라우저 없음)

### <a name="device-code-flow-why-and-how"></a>장치 코드 흐름 이유 무엇 인가요?

웹 컨트롤이 없는 명령줄 도구를 작성 하 고 이전 흐름을 사용 하지 않으려는 경우에는를 사용 `AcquireTokenWithDeviceCode`해야 합니다.

Azure AD를 사용한 대화형 인증에는 웹 브라우저가 필요 합니다 (자세한 내용은 [웹 브라우저 사용](https://aka.ms/msal-net-uses-web-browser)참조). 그러나 웹 브라우저를 제공 하지 않는 장치 또는 운영 체제에서 사용자를 인증 하기 위해 장치 코드 흐름을 통해 사용자는 다른 장치 (예를 들어 다른 컴퓨터 또는 휴대폰)를 사용 하 여 대화형으로 로그인 할 수 있습니다. 응용 프로그램은 장치 코드 흐름을 사용 하 여 이러한 장치/OS 용으로 특별히 설계 된 2 단계 프로세스를 통해 토큰을 가져옵니다. 이러한 응용 프로그램의 예로는 iOT에서 실행 되는 응용 프로그램이 나 CLI (명령줄 도구)가 있습니다. 그 이유는 다음과 같습니다.

1. 사용자 인증이 필요한 경우 앱은 코드를 제공 하 고, 다른 장치 (예: 인터넷에 연결 된 스마트폰)를 사용 하 여 사용자에 게 코드를 입력 하 라는 메시지가 표시 `https://microsoft.com/devicelogin`되는 URL (예:)로 이동 하도록 요청 합니다. 이 작업이 완료 되 면 웹 페이지에는 필요한 경우 승인 프롬프트 및 multi-factor authentication을 비롯 하 여 일반 인증 환경을 통해 사용자가 게 됩니다.

2. 성공적으로 인증 되 면 명령줄 앱은 백 채널을 통해 필요한 토큰을 수신 하 고이를 사용 하 여 필요한 웹 API 호출을 수행 합니다.

### <a name="code"></a>코드

`IPublicClientApplication`이름이 인 메서드를 포함 합니다.`AcquireTokenWithDeviceCode`

```CSharp
 AcquireTokenWithDeviceCode(IEnumerable<string> scopes,
                            Func<DeviceCodeResult, Task> deviceCodeResultCallback)
```

이 메서드는를 매개 변수로 사용 합니다.

- `scopes` 액세스 토큰을 요청할입니다.
- 를 수신 하는 콜백입니다.`DeviceCodeResult`

  ![image](https://user-images.githubusercontent.com/13203188/56024968-7af1b980-5d11-11e9-84c2-5be2ef306dc5.png)

다음 샘플 코드는 가장 최신 사례를 제공 하며,이를 통해 얻을 수 있는 예외의 종류와 완화 방법을 설명 합니다.

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

MSAL.NET에서는 메모리 내 토큰 캐시가 기본적으로 제공됩니다.

### <a name="serialization-is-customizable-in-windows-desktop-apps-and-web-appsweb-apis"></a>Serialization은 Windows 데스크톱 앱 및 웹 앱/웹 Api에서 사용자 지정할 수 있습니다.

.NET Framework 및 .NET core의 경우 추가 작업을 수행 하지 않으면 메모리 내 토큰 캐시가 응용 프로그램 기간 동안 지속 됩니다. Serialization이 즉시 제공 되지 않는 이유를 이해 하려면 MSAL .NET 데스크톱/코어 응용 프로그램은 콘솔 또는 Windows 응용 프로그램 (파일 시스템에 액세스할 수 있음) 뿐만 **아니라** 웹 응용 프로그램 또는 web API 일 수 있습니다. 이러한 웹 앱 및 웹 Api는 데이터베이스, 분산 캐시, redis 캐시 등과 같은 특정 캐시 메커니즘을 사용할 수 있습니다. .NET 데스크톱 또는 Core에서 영구 토큰 캐시 응용 프로그램을 만들려면 serialization을 사용자 지정 해야 합니다.

토큰 캐시 직렬화와 관련 된 클래스 및 인터페이스는 다음과 같은 형식입니다.

- ``ITokenCache``-토큰 캐시 serialization 요청을 구독할 이벤트를 정의 하 고 다양 한 형식으로 캐시를 직렬화 하거나 역직렬화 하는 메서드 (ADAL v 3.0, MSAL 2.x 및 MSAL 3(sp3) = ADAL v 5.0)
- ``TokenCacheCallback``은 직렬화를 처리할 수 있도록 이벤트에 전달되는 콜백입니다. 형식의 ``TokenCacheNotificationArgs``인수를 사용 하 여 호출 됩니다.
- ``TokenCacheNotificationArgs``응용 프로그램 ``ClientId`` 의 및 토큰을 사용할 수 있는 사용자에 대 한 참조만 제공 합니다.

  ![image](https://user-images.githubusercontent.com/13203188/56027172-d58d1480-5d15-11e9-8ada-c0292f1800b3.png)

> [!IMPORTANT]
> 사용자가 애플리케이션의 `UserTokenCache` 및 `AppTokenCache` 속성을 호출하면 MSAL.NET은 사용자 대신 토큰 캐시를 만들고 사용자에게 `IToken` 캐시를 제공합니다. 인터페이스를 직접 구현할 필요가 없습니다. 사용자는 사용자 지정 토큰 캐시 직렬화를 구현할 때 다음과 같은 일만 하면 됩니다.
>
> - 및 `BeforeAccess` "`AfterAccess` events" (또는 *비동기* 대응)에 대응 합니다. 이 대리자는 캐시를 직렬화 하는 `AfterAccess` 반면 캐시를 직렬화 해야 합니다.`BeforeAccess`
> - 이러한 이벤트의 일부는 Blob을 저장하거나 로드하며, Blob은 이벤트 인수를 통해 사용자가 원하는 스토리지에 전달됩니다.

이 전략은 공용 클라이언트 응용 프로그램 (데스크톱) 또는 기밀 클라이언트 응용 프로그램 (웹 앱/웹 API, 디먼 앱)에 대 한 토큰 캐시 serialization을 작성 하는 경우에 따라 다릅니다.

MSAL V2. x에는 캐시를 MSAL.NET 형식 (MSAL에 공통 되는 통합 형식 캐시 및 플랫폼 전체)에만 serialize 하 고, [레거시](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Token-cache-serialization) 토큰 캐시를 지원 하려는 경우에 따라 몇 가지 옵션이 있습니다. ADAL V3 직렬화.

ADAL.NET, ADAL.NET 및 MSAL.NET 간에 SSO 상태를 공유 하는 토큰 캐시 serialization의 사용자 지정은 다음 샘플의 일부로 설명 됩니다. [active directory-dotnet-v1--v2](https://github.com/Azure-Samples/active-directory-dotnet-v1-to-v2)

### <a name="simple-token-cache-serialization-msal-only"></a>간단한 토큰 캐시 직렬화(MSAL만 해당)

아래는 데스크톱 애플리케이션의 토큰 캐시를 사용자 지정 직렬화하는 간단한 예입니다. 응용 프로그램과 동일한 폴더에 있는 파일의 사용자 토큰 캐시입니다.

응용 프로그램을 빌드한 후 응용 프로그램 전달을 호출 ``TokenCacheHelper.EnableSerialization()`` 하 여 serialization을 사용 하도록 설정 합니다.`UserTokenCache`

```CSharp
app = PublicClientApplicationBuilder.Create(ClientId)
    .Build();
TokenCacheHelper.EnableSerialization(app.UserTokenCache);
```

이 도우미 클래스는 다음 코드 조각과 같습니다.

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

공용 클라이언트 응용 프로그램 (Windows, Mac 및 linux에서 실행 되는 데스크톱 응용 프로그램의 경우)에 대 한 제품 품질 토큰 캐시 파일 기반 serializer의 미리 보기는 [Microsoft.](https://github.com/AzureAD/microsoft-authentication-extensions-for-dotnet/tree/master/src/Microsoft.Identity.Client.Extensions.Msal) 다음 nuget 패키지에서 이 미리 보기를 애플리케이션에 포함할 수 있습니다. [Microsoft.Identity.Client.Extensions.Msal](https://www.nuget.org/packages/Microsoft.Identity.Client.Extensions.Msal/).

> [!NOTE]
> 내용을. MSAL.NET 라이브러리는 확장 프로그램을 통해 확장 됩니다. 이러한 라이브러리의 클래스는 나중에 또는 주요 변경 내용으로 MSAL.NET 될 수 있습니다.

### <a name="dual-token-cache-serialization-msal-unified-cache--adal-v3"></a>이중 토큰 캐시 serialization (MSAL 통합 캐시 + ADAL V3)

ADAL.NET 4.x 및 MSAL.NET 2.x에 공통적으로 적용 되 고 동일한 플랫폼에서 동일한 세대의 다른 MSALs를 사용 하 여 토큰 캐시 serialization을 구현 하려는 경우 다음 코드를 사용 하 여 아이디어를 얻을 수 있습니다. :

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

이번에는 도우미 클래스가 다음 코드와 같습니다.

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
   UnifiedCacheFileName = unifiedCacheFileName;
   AdalV3CacheFileName = adalV3CacheFileName;

   cache.SetBeforeAccess(BeforeAccessNotification);
   cache.SetAfterAccess(AfterAccessNotification);
  }

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
