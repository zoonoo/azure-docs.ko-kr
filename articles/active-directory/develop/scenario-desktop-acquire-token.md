---
title: 웹 API(데스크톱 앱)를 호출하는 토큰 획득 | Azure
titleSuffix: Microsoft identity platform
description: 앱에 대한 토큰을 획득하기 위해 웹 API를 호출하는 데스크톱 앱을 빌드하는 방법에 대해 알아봅니다.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 10/30/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: a5942a9d614bbb06fadb1d4b16d4c68c007434c7
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/08/2020
ms.locfileid: "80885321"
---
# <a name="desktop-app-that-calls-web-apis-acquire-a-token"></a>웹 API를 호출하는 데스크톱 앱: 토큰 획득

공용 클라이언트 응용 프로그램의 인스턴스를 만든 후에는 이를 사용하여 웹 API를 호출하는 데 사용할 토큰을 획득합니다.

## <a name="recommended-pattern"></a>추천 패턴

웹 API는 에 `scopes`의해 정의됩니다. 응용 프로그램에서 제공하는 경험이 무엇이든 사용할 패턴은 다음과 다 됩니다.

- 을 호출하여 `AcquireTokenSilent`토큰 캐시에서 토큰을 체계적으로 가져옵니다.
- 이 호출이 `AcquireToken` 실패하면 사용할 흐름을 사용하여 `AcquireTokenXX`여기에 표시됩니다.

# <a name="net"></a>[.NET](#tab/dotnet)

### <a name="in-msalnet"></a>In MSAL.NET

```csharp
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

# <a name="java"></a>[Java](#tab/java)

```java

Set<IAccount> accountsInCache = pca.getAccounts().join();
// Take first account in the cache. In a production application, you would filter
// accountsInCache to get the right account for the user authenticating.
IAccount account = accountsInCache.iterator().next();

IAuthenticationResult result;
try {
    SilentParameters silentParameters =
            SilentParameters
                    .builder(SCOPE, account)
                    .build();

    // try to acquire token silently. This call will fail since the token cache
    // does not have any data for the user you are trying to acquire a token for
    result = pca.acquireTokenSilently(silentParameters).join();
} catch (Exception ex) {
    if (ex.getCause() instanceof MsalException) {

        InteractiveRequestParameters parameters = InteractiveRequestParameters
                .builder(new URI("http://localhost"))
                .scopes(SCOPE)
                .build();

        // Try to acquire a token interactively with system browser. If successful, you should see
        // the token and account information printed out to console
        result = pca.acquireToken(parameters).join();
    } else {
        // Handle other exceptions accordingly
        throw ex;
    }
}
return result;

```

# <a name="python"></a>[Python](#tab/python)

```Python
result = None

# Firstly, check the cache to see if this end user has signed in before
accounts = app.get_accounts(username=config["username"])
if accounts:
    result = app.acquire_token_silent(config["scope"], account=accounts[0])

if not result:
    result = app.acquire_token_by_xxx(scopes=config["scope"])
```

# <a name="macos"></a>[Macos](#tab/macOS)

### <a name="in-msal-for-ios-and-macos"></a>iOS 및 macOS용 MSAL에서

Objective-C:

```objc
MSALAccount *account = [application accountForIdentifier:accountIdentifier error:nil];

MSALSilentTokenParameters *silentParams = [[MSALSilentTokenParameters alloc] initWithScopes:scopes account:account];
[application acquireTokenSilentWithParameters:silentParams completionBlock:^(MSALResult *result, NSError *error) {

    // Check the error
    if (error && [error.domain isEqual:MSALErrorDomain] && error.code == MSALErrorInteractionRequired)
    {
        // Interactive auth will be required, call acquireTokenWithParameters:error:
    }
}];
```
Swift:

```swift
guard let account = try? application.account(forIdentifier: accountIdentifier) else { return }
let silentParameters = MSALSilentTokenParameters(scopes: scopes, account: account)
application.acquireTokenSilent(with: silentParameters) { (result, error) in

    guard let authResult = result, error == nil else {

    let nsError = error! as NSError

        if (nsError.domain == MSALErrorDomain &&
            nsError.code == MSALError.interactionRequired.rawValue) {

            // Interactive auth will be required, call acquireToken()
            return
        }
        return
    }
}
```
---

다음은 데스크톱 응용 프로그램에서 토큰을 획득하는 다양한 방법입니다.

## <a name="acquire-a-token-interactively"></a>대화식으로 토큰 획득

다음 예제에서는 Microsoft Graph를 사용하여 사용자 프로필을 읽기 위해 대화형으로 토큰을 얻을 수 있는 최소 코드를 보여 주어 있습니다.

# <a name="net"></a>[.NET](#tab/dotnet)
### <a name="in-msalnet"></a>In MSAL.NET

```csharp
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

`AcquireTokenInteractive`토큰이 필요한 범위를 ``scopes``정의하는 문자열의 열거를 포함하는 필수 매개 변수가 하나만 있습니다. 토큰이 Microsoft Graph용인 경우 "사용 권한" 섹션의 각 Microsoft 그래프 API의 API 참조에서 필요한 범위를 찾을 수 있습니다. 예를 들어 [사용자의 연락처를 나열하려면](https://developer.microsoft.com/graph/docs/api-reference/v1.0/api/user_list_contacts)범위 "User.Read", "Contacts.Read"를 사용해야 합니다. 자세한 내용은 [Microsoft 그래프 사용 권한 참조를](https://developer.microsoft.com/graph/docs/concepts/permissions_reference)참조하십시오.

Android에서는 그림과 같이 을 사용하여 `.WithParentActivityOrWindow`상위 활동을 지정해야 상호 작용 후 토큰이 해당 상위 활동으로 돌아갑니다. 지정하지 않으면 을 호출할 `.ExecuteAsync()`때 예외가 throw됩니다.

### <a name="specific-optional-parameters-in-msalnet"></a>MSAL.NET 특정 선택적 매개 변수

#### <a name="withparentactivityorwindow"></a>와드부모활동또는창

UI는 대화형이기 때문에 중요합니다. `AcquireTokenInteractive`이를 지원하는 플랫폼인 부모 UI에 대해 지정할 수 있는 하나의 특정 선택적 매개 변수가 있습니다. 데스크톱 응용 프로그램에서 `.WithParentActivityOrWindow` 사용할 경우 플랫폼에 따라 달라지는 다른 유형이 있습니다.

```csharp
// net45
WithParentActivityOrWindow(IntPtr windowPtr)
WithParentActivityOrWindow(IWin32Window window)

// Mac
WithParentActivityOrWindow(NSWindow window)

// .Net Standard (this will be on all platforms at runtime, but only on NetStandard at build time)
WithParentActivityOrWindow(object parent).
```

설명:

- .NET 표준에서 예상되는 `object` `Activity` 방법은 안드로이드, `UIViewController` iOS, `NSWindow` MAC `IWin32Window` 및 `IntPr` Windows에서 제공됩니다.
- Windows에서 포함된 브라우저가 적절한 UI 동기화 컨텍스트를 얻을 수 있도록 UI 스레드에서 호출해야 `AcquireTokenInteractive` 합니다. UI 스레드에서 호출하지 않으면 메시지가 UI를 사용하여 제대로 펌핑되지 않고 교착 상태가 발생할 수 있습니다. UI 스레드에 없는 경우 UI 스레드에서 Microsoft 인증 라이브러리(MSA)를 호출하는 한 `Dispatcher` 가지 방법은 WPF에서 를 사용하는 것입니다.
- WPF를 사용하는 경우 WPF 컨트롤에서 창을 얻으려면 클래스를 `WindowInteropHelper.Handle` 사용할 수 있습니다. 그런 다음 호출은 WPF`this`컨트롤 ()

  ```csharp
  result = await app.AcquireTokenInteractive(scopes)
                    .WithParentActivityOrWindow(new WindowInteropHelper(this).Handle)
                    .ExecuteAsync();
  ```

#### <a name="withprompt"></a>프롬프트

`WithPrompt()`은 프롬프트를 지정하여 사용자와의 상호 작용을 제어하는 데 사용됩니다.

<img src="https://user-images.githubusercontent.com/13203188/53438042-3fb85700-39ff-11e9-9a9e-1ff9874197b3.png" width="25%" />

클래스는 다음 상수를 정의합니다.

- ``SelectAccount``강제로 사용자에게 세션이 있는 계정이 포함된 계정 선택 대화 상자가 표시됩니다. 이 옵션은 응용 프로그램 개발자가 사용자가 다른 ID 중에서 선택할 수 있도록 하려는 경우에 유용합니다. 이 옵션은 MSAL을 ``prompt=select_account`` ID 공급자에게 보냅니다. 이 옵션이 기본값입니다. 사용자에 대한 세션의 계정 및 현재 상태와 같은 사용 가능한 정보를 기반으로 최상의 환경을 제공하는 것이 좋습니다. 당신이 그것을 할 수있는 좋은 이유가없는 한 그것을 변경하지 마십시오.
- ``Consent``응용 프로그램 개발자가 이전에 동의를 받은 경우에도 사용자에게 동의를 요청하도록 강제할 수 있습니다. 이 경우 MSAL은 `prompt=consent` ID 공급자에게 보냅니다. 이 옵션은 조직 거버넌스에서 응용 프로그램을 사용할 때마다 동의 대화 상자가 사용자에게 제공되도록 요구하는 일부 보안 중심 응용 프로그램에서 사용할 수 있습니다.
- ``ForceLogin``응용 프로그램 개발자는 이 사용자 프롬프트가 필요하지 않더라도 사용자가 서비스에서 자격 증명을 묻는 메시지를 받을 수 있습니다. 이 옵션은 토큰 획득에 실패할 경우 사용자가 다시 로그인하도록 하는 데 유용할 수 있습니다. 이 경우 MSAL은 `prompt=login` ID 공급자에게 보냅니다. 때로는 조직 거버넌스가 사용자가 응용 프로그램의 특정 부분에 액세스할 때마다 다시 서명하도록 요구하는 보안 중심 응용 프로그램에 사용되는 경우가 있습니다.
- ``Never``(.NET 4.5 및 WinRT 전용)는 사용자에게 메시지를 표시하지 않고 대신 숨겨진 임베디드 웹 보기에 저장된 쿠키를 사용하려고 시도합니다. 자세한 내용은 MSAL.NET 웹 보기를 참조하세요. 이 옵션을 사용하면 실패할 수 있습니다. 이 경우 `AcquireTokenInteractive` UI 상호 작용이 필요하다는 것을 알리는 예외를 throw합니다. 다른 `Prompt` 매개 변수를 사용해야 합니다.
- ``NoPrompt``ID 공급자에게 프롬프트를 보내지 않습니다. 이 옵션은 Azure Active Directory(Azure AD) B2C 편집 프로필 정책에만 유용합니다. 자세한 내용은 [Azure AD B2C 세부 정보를](https://aka.ms/msal-net-b2c-specificities)참조하십시오.

#### <a name="withextrascopetoconsent"></a>엑스트라스코프ToConsent

이 수정자는 사용자가 여러 리소스에 미리 동의하도록 하려는 고급 시나리오에서 사용되며 일반적으로 MSAL.NET/the Microsoft ID 플랫폼과 함께 사용되는 증분 동의를 사용하지 않으려는 경우. 자세한 내용은 [여러 리소스에 대한 사용자 동의를 미리 확인을](scenario-desktop-production.md#have-the-user-consent-upfront-for-several-resources)참조하십시오.

```csharp
var result = await app.AcquireTokenInteractive(scopesForCustomerApi)
                     .WithExtraScopeToConsent(scopesForVendorApi)
                     .ExecuteAsync();
```

#### <a name="withcustomwebui"></a>위드커스터마이웨이

웹 UI는 브라우저를 호출하는 메커니즘입니다. 이 메커니즘은 전용 UI WebBrowser 컨트롤이거나 브라우저 열기를 위임하는 방법일 수 있습니다.
MSAL은 대부분의 플랫폼에 웹 UI 구현을 제공하지만 브라우저를 직접 호스팅하려는 경우가 있습니다.

- MSAL에서 명시적으로 다루지 않는 플랫폼(예: 데스크톱의 블레이저, Unity 및 모노).
- 응용 프로그램을 테스트하고 셀레늄과 함께 사용할 수 있는 자동화된 브라우저를 사용하려고 합니다.
- MSAL을 실행하는 브라우저와 앱은 별도의 프로세스에 있습니다.

##### <a name="at-a-glance"></a>개요

이를 위해 최종 사용자가 사용자 `start Url`이름과 같은 항목을 입력할 수 있도록 선택한 브라우저에 표시해야 하는 MSAL을 제공합니다.
인증이 완료되면 앱은 Azure AD에서 `end Url`제공하는 코드를 포함하는 MSAL로 다시 전달해야 합니다.
의 `end Url` 호스트는 `redirectUri`항상 입니다. 가로채려면 다음 중 하나를 수행합니다. `end Url`

- 모니터 브라우저 리디렉션이 `redirect Url` 적중될 때까지.
- 브라우저가 모니터링하는 URL로 리디렉션하도록 합니다.

##### <a name="withcustomwebui-is-an-extensibility-point"></a>With CustomWebUi는 확장성 지점입니다.

`WithCustomWebUi`는 공용 클라이언트 응용 프로그램에서 고유한 UI를 제공하는 데 사용할 수 있는 확장성 지점입니다. 또한 사용자가 ID 공급자의 /권한 부여 끝점을 통과하도록 허용하고 로그인하고 동의하도록 할 수 있습니다. MSAL.NET 인증 코드를 구속하고 토큰을 얻을 수 있습니다. 예를 들어 Visual Studio에서 전자 응용 프로그램(예: Visual Studio 피드백)이 웹 상호 작용을 제공하지만 대부분의 작업을 수행하도록 MSAL.NET 있도록 합니다. UI 자동화를 제공하려는 경우에도 사용할 수 있습니다. 공용 클라이언트 응용 프로그램에서 MSAL.NET PKCE(코드 교환증명 키) 표준을 사용하여 보안을 준수합니다. MSAL.NET 코드를 사용할 수 있습니다. 자세한 내용은 [RFC 7636 - OAuth 공용 클라이언트의 코드 교환에 대한 증명 키를](https://tools.ietf.org/html/rfc7636)참조하십시오.

  ```csharp
  using Microsoft.Identity.Client.Extensions;
  ```

##### <a name="use-withcustomwebui"></a>와 함께 사용 사용자 정의 웹Ui

`.WithCustomWebUI`사용하려면 다음 단계를 따르십시오.

  1. `ICustomWebUi` 인터페이스를 구현합니다. 자세한 내용은 [이 웹 사이트를](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/blob/053a98d16596be7e9ca1ab916924e5736e341fe8/src/Microsoft.Identity.Client/Extensibility/ICustomWebUI.cs#L32-L70)참조하십시오. 한 `AcquireAuthorizationCodeAsync`메서드를 구현 하 고 MSAL.NET 의해 계산 된 권한 부여 코드 URL을 수락 합니다. 그런 다음 사용자가 ID 공급자와의 상호 작용을 살펴보고 ID 공급자가 권한 부여 코드와 함께 구현을 다시 호출한 URL을 반환하도록 합니다. 문제가 있는 경우 구현에서 예외를 `MsalExtensionException` throw하여 MSAL과 잘 협력해야 합니다.
  2. `AcquireTokenInteractive` 통화에서 사용자 지정 `.WithCustomUI()` 웹 UI의 인스턴스를 전달하는 수정자를 사용합니다.

     ```csharp
     result = await app.AcquireTokenInteractive(scopes)
                       .WithCustomWebUi(yourCustomWebUI)
                       .ExecuteAsync();
     ```

##### <a name="examples-of-implementation-of-icustomwebui-in-test-automation-seleniumwebui"></a>테스트 자동화에서 ICustomWebUi 구현의 예: 셀레늄웹UI

MSAL.NET 팀은 이 확장성 메커니즘을 사용하도록 UI 테스트를 다시 작성했습니다. 관심이 있으시면 MSAL.NET 소스 코드에서 [SeleniumWebUI](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/blob/053a98d16596be7e9ca1ab916924e5736e341fe8/tests/Microsoft.Identity.Test.Integration/Infrastructure/SeleniumWebUI.cs#L15-L160) 클래스를 참조하십시오.

##### <a name="provide-a-great-experience-with-systemwebviewoptions"></a>시스템 웹뷰옵션에 대한 훌륭한 경험 제공

MSAL.NET 4.1에서 [`SystemWebViewOptions`](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.systemwebviewoptions?view=azure-dotnet)다음을 지정할 수 있습니다.

- 시스템 웹 브라우저에서`BrowserRedirectError`로그인 또는 동의 오류가`HtmlMessageError`있는 경우 () 또는 HTML 조각으로 이동하여 표시합니다.
- 에 이동 하는`BrowserRedirectSuccess`URI () 또는 HTML`HtmlMessageSuccess`조각 표시 () 성공적인 로그인 또는 동의의 경우.
- 시스템 브라우저를 시작하기 위해 실행할 작업입니다. 대리자를 설정하여 고유한 구현을 `OpenBrowserAsync` 제공할 수 있습니다. 클래스는 또한 두 개의 브라우저에 `OpenWithEdgeBrowserAsync` 대 `OpenWithChromeEdgeBrowserAsync` 한 기본 구현을 제공 합니다.: 그리고 마이크로소프트 가장자리와 [마이크로소프트 가장자리 크롬에](https://www.windowscentral.com/faq-edge-chromium)대 한, 각각.

이 구조를 사용하려면 다음 예제와 같은 것을 작성합니다.

```csharp
IPublicClientApplication app;
...

options = new SystemWebViewOptions
{
 HtmlMessageError = "<b>Sign-in failed. You can close this tab ...</b>",
 BrowserRedirectSuccess = "https://contoso.com/help-for-my-awesome-commandline-tool.html"
};

var result = app.AcquireTokenInteractive(scopes)
                .WithEmbeddedWebView(false)       // The default in .NET Core
                .WithSystemWebViewOptions(options)
                .Build();
```

#### <a name="other-optional-parameters"></a>기타 선택적 매개 변수

에 대한 `AcquireTokenInteractive`다른 모든 선택적 매개 변수에 대한 자세한 내용은 [AcquireTokenInteractive매개 빌더](/dotnet/api/microsoft.identity.client.acquiretokeninteractiveparameterbuilder?view=azure-dotnet-preview#methods)를 참조하십시오.

# <a name="java"></a>[Java](#tab/java)

```java
private static IAuthenticationResult acquireTokenInteractive() throws Exception {

    // Load token cache from file and initialize token cache aspect. The token cache will have
    // dummy data, so the acquireTokenSilently call will fail.
    TokenCacheAspect tokenCacheAspect = new TokenCacheAspect("sample_cache.json");

    PublicClientApplication pca = PublicClientApplication.builder(CLIENT_ID)
            .authority(AUTHORITY)
            .setTokenCacheAccessAspect(tokenCacheAspect)
            .build();

    Set<IAccount> accountsInCache = pca.getAccounts().join();
    // Take first account in the cache. In a production application, you would filter
    // accountsInCache to get the right account for the user authenticating.
    IAccount account = accountsInCache.iterator().next();

    IAuthenticationResult result;
    try {
        SilentParameters silentParameters =
                SilentParameters
                        .builder(SCOPE, account)
                        .build();

        // try to acquire token silently. This call will fail since the token cache
        // does not have any data for the user you are trying to acquire a token for
        result = pca.acquireTokenSilently(silentParameters).join();
    } catch (Exception ex) {
        if (ex.getCause() instanceof MsalException) {

            InteractiveRequestParameters parameters = InteractiveRequestParameters
                    .builder(new URI("http://localhost"))
                    .scopes(SCOPE)
                    .build();

            // Try to acquire a token interactively with system browser. If successful, you should see
            // the token and account information printed out to console
            result = pca.acquireToken(parameters).join();
        } else {
            // Handle other exceptions accordingly
            throw ex;
        }
    }
    return result;
}
```

# <a name="python"></a>[Python](#tab/python)

MSAL 파이썬은 대화형 획득 토큰 메서드를 직접 제공하지 않습니다. 대신 응용 프로그램이 권한 부여 코드를 얻기 위해 사용자 상호 작용 흐름을 구현할 때 권한 부여 요청을 보내야 합니다. 그런 다음 이 코드를 `acquire_token_by_authorization_code` 메서드에 전달하여 토큰을 얻을 수 있습니다.

```Python
result = None

# Firstly, check the cache to see if this end user has signed in before
accounts = app.get_accounts(username=config["username"])
if accounts:
    result = app.acquire_token_silent(config["scope"], account=accounts[0])

if not result:
    result = app.acquire_token_by_authorization_code(
         request.args['code'],
         scopes=config["scope"])    

```

# <a name="macos"></a>[Macos](#tab/macOS)

### <a name="in-msal-for-ios-and-macos"></a>iOS 및 macOS용 MSAL에서

Objective-C:

```objc
MSALInteractiveTokenParameters *interactiveParams = [[MSALInteractiveTokenParameters alloc] initWithScopes:scopes webviewParameters:[MSALWebviewParameters new]];
[application acquireTokenWithParameters:interactiveParams completionBlock:^(MSALResult *result, NSError *error) {
    if (!error)
    {
        // You'll want to get the account identifier to retrieve and reuse the account
        // for later acquireToken calls
        NSString *accountIdentifier = result.account.identifier;

        NSString *accessToken = result.accessToken;
    }
}];
```

Swift:

```swift
let interactiveParameters = MSALInteractiveTokenParameters(scopes: scopes, webviewParameters: MSALWebviewParameters())
application.acquireToken(with: interactiveParameters, completionBlock: { (result, error) in

    guard let authResult = result, error == nil else {
        print(error!.localizedDescription)
        return
    }

    // Get access token from result
    let accessToken = authResult.accessToken
})
```
---

## <a name="integrated-windows-authentication"></a>Windows 통합 인증

도메인 또는 Azure AD 조인 컴퓨터에서 도메인 사용자에 로그인하려면 IWA(통합 Windows 인증)를 사용합니다.

### <a name="constraints"></a>제약 조건

- 통합 Windows 인증은 *페더레이션된 사용자,* 즉 Active Directory에서 만들고 Azure AD에서 지원하는 사용자만 사용할 수 있습니다. *관리되는* 사용자라고 하는 Active Directory 백업 없이 Azure AD에서 직접 만든 사용자는 이 인증 흐름을 사용할 수 없습니다. 이 제한은 사용자 이름 및 암호 흐름에 영향을 주지 않습니다.
- IWA는 .NET 프레임워크, .NET 코어 및 유니버설 윈도우 플랫폼(UWP) 플랫폼을 위해 작성된 앱용입니다.
- IWA는 MFA(다단계 인증)를 우회하지 않습니다. MFA가 구성된 경우 MFA에 사용자 상호 작용이 필요하기 때문에 MFA 챌린지가 필요한 경우 IWA가 실패할 수 있습니다.
  > [!NOTE]
  > 이것은 까다롭습니다. IWA는 비대화형이지만 MFA에는 사용자 상호 작용이 필요합니다. ID 공급자가 MFA를 수행하도록 요청하는 시기는 제어하지 않습니다. 우리의 관찰에서, MFA는 다른 국가에서 로그인 할 때, VPN을 통해 회사 네트워크에 연결되지 않은 경우, 때로는 VPN을 통해 연결될 때 필요합니다. 결정적인 규칙 집합을 기대하지 마십시오. Azure AD는 AI를 사용하여 MFA가 필요한지 지속적으로 학습합니다. IWA가 실패하면 대화형 인증 또는 장치 코드 흐름과 같은 사용자 프롬프트로 대체합니다.

- 통과된 권한은 다음과 같은 것이어야 합니다. `PublicClientApplicationBuilder`
  - 테넌트 ID를 `tenant` 나타내는 GUID 또는 테넌트와 연결된 도메인인 양식의 `https://login.microsoftonline.com/{tenant}/`테넌트입니다.
  - 모든 직장 및 학교 `https://login.microsoftonline.com/organizations/`계정: .
  - Microsoft 개인 계정은 지원되지 않습니다. /common 또는 /consumers 테넌트는 사용할 수 없습니다.

- 통합 Windows 인증은 자동 흐름이기 때문에:
  - 응용 프로그램 사용자가 응용 프로그램 사용에 이전에 동의했어야 합니다.
  - 또는 테넌트 관리자는 응용 프로그램을 사용하려면 테넌트의 모든 사용자에게 이전에 동의했어야 합니다.
  - 즉, 다음과 같습니다.
    - 개발자가 Azure 포털에서 **권한 부여** 단추를 직접 선택했습니다.
    - 또는 테넌트 관리자가 응용 프로그램 등록의 **API 권한** 탭에서 **{테넌트 도메인}** 단추에 대한 권한 부여/해지 관리자 동의를 선택했습니다. 자세한 내용은 [웹 API에 액세스할 수 있는 권한 추가를](https://docs.microsoft.com/azure/active-directory/develop/quickstart-configure-app-access-web-apis#add-permissions-to-access-web-apis)참조하십시오.
    - 또는 사용자가 응용 프로그램에 동의할 수 있는 방법을 제공했습니다. 자세한 내용은 [개별 사용자 동의 를 참조하십시오.](https://docs.microsoft.com/azure/active-directory/develop/v2-permissions-and-consent#requesting-individual-user-consent)
    - 또는 테넌트 관리자가 응용 프로그램에 동의할 수 있는 방법을 제공했습니다. 자세한 내용은 [관리자 동의를](https://docs.microsoft.com/azure/active-directory/develop/v2-permissions-and-consent#requesting-consent-for-an-entire-tenant)참조하십시오.

- 이 흐름은 .NET 데스크톱, .NET 코어 및 UWP 앱에 사용할 수 있습니다.

동의에 대한 자세한 내용은 [Microsoft ID 플랫폼 권한 및 동의를](https://docs.microsoft.com/azure/active-directory/develop/v2-permissions-and-consent)참조하십시오.

### <a name="learn-how-to-use-it"></a>사용 방법 알아보기

# <a name="net"></a>[.NET](#tab/dotnet)

MSAL.NET 다음을 사용해야 합니다.

```csharp
AcquireTokenByIntegratedWindowsAuth(IEnumerable<string> scopes)
```

일반적으로 하나의 매개 변수`scopes`()만 필요합니다. Windows 관리자가 정책을 설정하는 방법에 따라 Windows 컴퓨터의 응용 프로그램이 로그인한 사용자를 조회할 수 없을 수 있습니다. 이 경우 두 번째 `.WithUsername()`메서드를 사용하고 로그인한 사용자의 사용자 이름을 UPN 형식으로 `joe@contoso.com`전달합니다. .NET Core에서는 .NET Core 플랫폼에서 사용자 이름을 OS에 요청할 수 없으므로 사용자 이름을 차지하는 오버로드만 사용할 수 있습니다.

다음 샘플에서는 얻을 수 있는 예외 의 종류와 해당 완화에 대한 설명과 함께 최신 사례를 제공합니다.

```csharp
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

AcquireTokenBy통합윈도우인증에 대한 가능한 수정자 목록은 [acquireTokenByIntegratedWindowsAAParameterBuilder](/dotnet/api/microsoft.identity.client.acquiretokenbyintegratedwindowsauthparameterbuilder?view=azure-dotnet-preview#methods)를 참조하십시오.

# <a name="java"></a>[Java](#tab/java)

이 추출은 [MSAL Java 개발자 샘플에서 추출한 것입니다.](https://github.com/AzureAD/microsoft-authentication-library-for-java/blob/dev/src/samples/public-client/)

```Java
private static IAuthenticationResult acquireTokenIwa() throws Exception {

    // Load token cache from file and initialize token cache aspect. The token cache will have
    // dummy data, so the acquireTokenSilently call will fail.
    TokenCacheAspect tokenCacheAspect = new TokenCacheAspect("sample_cache.json");

    PublicClientApplication pca = PublicClientApplication.builder(CLIENT_ID)
            .authority(AUTHORITY)
            .setTokenCacheAccessAspect(tokenCacheAspect)
            .build();

    Set<IAccount> accountsInCache = pca.getAccounts().join();
    // Take first account in the cache. In a production application, you would filter
    // accountsInCache to get the right account for the user authenticating.
    IAccount account = accountsInCache.iterator().next();

    IAuthenticationResult result;
    try {
        SilentParameters silentParameters =
                SilentParameters
                        .builder(SCOPE, account)
                        .build();

        // try to acquire token silently. This call will fail since the token cache
        // does not have any data for the user you are trying to acquire a token for
        result = pca.acquireTokenSilently(silentParameters).join();
    } catch (Exception ex) {
        if (ex.getCause() instanceof MsalException) {

            IntegratedWindowsAuthenticationParameters parameters =
                    IntegratedWindowsAuthenticationParameters
                            .builder(SCOPE, USER_NAME)
                            .build();

            // Try to acquire a IWA. You will need to generate a Kerberos ticket.
            // If successful, you should see the token and account information printed out to
            // console
            result = pca.acquireToken(parameters).join();
        } else {
            // Handle other exceptions accordingly
            throw ex;
        }
    }
    return result;
}
```

# <a name="python"></a>[Python](#tab/python)

이 흐름은 MSAL 파이썬에서 아직 지원되지 않습니다.

# <a name="macos"></a>[Macos](#tab/macOS)

이 흐름은 MacOS에 적용되지 않습니다.

---

## <a name="username-and-password"></a>사용자 이름 및 암호

사용자 이름과 암호를 제공하여 토큰을 획득할 수도 있습니다. 이 흐름은 제한적이며 권장되지는 않지만 필요한 사용 사례가 여전히 있습니다.

### <a name="this-flow-isnt-recommended"></a>이 흐름은 권장되지 않습니다.

응용 프로그램에서 사용자에게 암호를 묻는 것은 안전하지 않으므로 이 흐름은 *권장되지* 않습니다. 자세한 내용은 [증가하는 암호 문제에 대한 해결 방법은 무엇입니까?](https://news.microsoft.com/features/whats-solution-growing-problem-passwords-says-microsoft/) Windows 도메인 에 가입된 컴퓨터에서 토큰을 자동으로 획득하기 위한 기본 흐름은 [통합 Windows 인증입니다.](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Integrated-Windows-Authentication) 장치 코드 [흐름을](https://aka.ms/msal-net-device-code-flow)사용할 수도 있습니다.

> [!NOTE]
> 사용자 이름과 암호를 사용하는 것은 DevOps 시나리오와 같은 경우에 유용합니다. 그러나 사용자 이름과 암호를 고유한 UI를 제공하는 대화형 시나리오에서 사용하려면 사용자 이름과 암호를 멀리 이동하는 방법을 생각해 보십시오. 사용자 이름과 암호를 사용하면 다음과 같은 여러 가지 사항을 포기합니다.
>
> - 현대 정체성의 핵심 신조. 공유 암호를 가로챌 수 있으므로 암호는 피싱및 재생될 수 있습니다. 암호가 없는 것과 호환되지 않습니다.
> - 상호 작용이 없기 때문에 MFA를 수행해야 하는 사용자는 로그인할 수 없습니다.
> - 사용자는 단일 사인온(SSO)을 수행할 수 없습니다.

### <a name="constraints"></a>제약 조건

다음과 같은 제약 조건도 적용됩니다.

- 사용자 이름 및 암호 흐름은 조건부 액세스 및 다단계 인증과 호환되지 않습니다. 따라서 테넌트 관리자가 다단계 인증을 필요로 하는 Azure AD 테넌트에서 앱이 실행되는 경우 이 흐름을 사용할 수 없습니다. 많은 조직에서 그렇게 합니다.
- 그것은 직장과 학교 계정 (MSA가 아님)에서만 작동합니다.
- 흐름은 .NET 데스크톱 및 .NET Core에서 사용할 수 있지만 UWP에서는 사용할 수 없습니다.

### <a name="b2c-specifics"></a>B2C 특이사항

자세한 내용은 [B2C가 있는 ROPC(리소스 소유자 암호 자격 증명)를](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/AAD-B2C-specifics#resource-owner-password-credentials-ropc-with-b2c)참조하십시오.

### <a name="use-it"></a>그것을 사용 하 여

# <a name="net"></a>[.NET](#tab/dotnet)

`IPublicClientApplication`메서드가 `AcquireTokenByUsernamePassword`포함되어 있습니다.

다음 샘플에서는 간소화된 사례를 제공합니다.

```csharp
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

다음 샘플에서는 얻을 수 있는 예외 의 종류와 해당 완화에 대한 설명과 함께 최신 사례를 제공합니다.

```csharp
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

에 적용할 `AcquireTokenByUsernamePassword`수 있는 모든 수정자에 대한 자세한 내용은 [AcquireTokenByUsernamePasswordPasswordBuilder](/dotnet/api/microsoft.identity.client.acquiretokenbyusernamepasswordparameterbuilder?view=azure-dotnet-preview#methods)를 참조하십시오.

# <a name="java"></a>[Java](#tab/java)

다음 추출은 [MSAL Java 개발자 샘플에서 추출한 것입니다.](https://github.com/AzureAD/microsoft-authentication-library-for-java/blob/dev/src/samples/public-client/)

```Java
private static IAuthenticationResult acquireTokenUsernamePassword() throws Exception {

    // Load token cache from file and initialize token cache aspect. The token cache will have
    // dummy data, so the acquireTokenSilently call will fail.
    TokenCacheAspect tokenCacheAspect = new TokenCacheAspect("sample_cache.json");

    PublicClientApplication pca = PublicClientApplication.builder(CLIENT_ID)
            .authority(AUTHORITY)
            .setTokenCacheAccessAspect(tokenCacheAspect)
            .build();

    Set<IAccount> accountsInCache = pca.getAccounts().join();
    // Take first account in the cache. In a production application, you would filter
    // accountsInCache to get the right account for the user authenticating.
    IAccount account = accountsInCache.iterator().next();

    IAuthenticationResult result;
    try {
        SilentParameters silentParameters =
                SilentParameters
                        .builder(SCOPE, account)
                        .build();
        // try to acquire token silently. This call will fail since the token cache
        // does not have any data for the user you are trying to acquire a token for
        result = pca.acquireTokenSilently(silentParameters).join();
    } catch (Exception ex) {
        if (ex.getCause() instanceof MsalException) {

            UserNamePasswordParameters parameters =
                    UserNamePasswordParameters
                            .builder(SCOPE, USER_NAME, USER_PASSWORD.toCharArray())
                            .build();
            // Try to acquire a token via username/password. If successful, you should see
            // the token and account information printed out to console
            result = pca.acquireToken(parameters).join();
        } else {
            // Handle other exceptions accordingly
            throw ex;
        }
    }
    return result;
}
```

# <a name="python"></a>[Python](#tab/python)

이 추출은 [MSAL 파이썬 개발 샘플에서 추출한 것입니다.](https://github.com/AzureAD/microsoft-authentication-library-for-python/blob/dev/sample/)

```Python
# Create a preferably long-lived app instance which maintains a token cache.
app = msal.PublicClientApplication(
    config["client_id"], authority=config["authority"],
    # token_cache=...  # Default cache is in memory only.
                       # You can learn how to use SerializableTokenCache from
                       # https://msal-python.rtfd.io/en/latest/#msal.SerializableTokenCache
    )

# The pattern to acquire a token looks like this.
result = None

# Firstly, check the cache to see if this end user has signed in before
accounts = app.get_accounts(username=config["username"])
if accounts:
    logging.info("Account(s) exists in cache, probably with token too. Let's try.")
    result = app.acquire_token_silent(config["scope"], account=accounts[0])

if not result:
    logging.info("No suitable token exists in cache. Let's get a new one from AAD.")
    # See this page for constraints of Username Password Flow.
    # https://github.com/AzureAD/microsoft-authentication-library-for-python/wiki/Username-Password-Authentication
    result = app.acquire_token_by_username_password(
        config["username"], config["password"], scopes=config["scope"])
```

# <a name="macos"></a>[Macos](#tab/macOS)

이 흐름은 macOS의 MSAL에서 지원되지 않습니다.

---

## <a name="command-line-tool-without-a-web-browser"></a>웹 브라우저가 없는 명령줄 도구

### <a name="device-code-flow"></a>디바이스 코드 흐름

웹 컨트롤이 없는 명령줄 도구를 작성하고 이전 흐름을 사용할 수 없거나 사용하지 않으려는 경우 장치 코드 흐름을 사용해야 합니다.

Azure AD를 사용 하 고 대화형 인증 웹 브라우저가 필요 합니다. 자세한 내용은 [웹 브라우저 사용을](https://aka.ms/msal-net-uses-web-browser)참조하십시오. 웹 브라우저를 제공하지 않는 장치 또는 운영 체제에서 사용자를 인증하기 위해 장치 코드 흐름을 사용하면 컴퓨터 나 휴대폰과 같은 다른 장치를 사용하여 대화식으로 로그인할 수 있습니다. 장치 코드 흐름을 사용 하 여 응용 프로그램은 이러한 장치 또는 OSes에 대 한 설계 된 2 단계 프로세스를 통해 토큰을 가져옵니다. 이러한 응용 프로그램의 예로는 iOT 또는 명령줄 도구(CLI)에서 실행되는 응용 프로그램이 있습니다. 아이디어는 다음과 같은 것입니다.

1. 사용자 인증이 필요할 때마다 앱은 사용자에 대한 코드를 제공합니다. 사용자는 인터넷에 연결된 스마트폰과 같은 다른 장치를 사용하여 URL로 이동하라는 메시지가 `https://microsoft.com/devicelogin`표시됩니다. 그런 다음 사용자에게 코드를 입력하라는 메시지가 표시됩니다. 이렇게 하면 웹 페이지는 필요한 경우 동의 프롬프트 및 다단계 인증을 포함하는 정상적인 인증 환경을 통해 사용자를 이끕습니다.

2. 인증이 성공하면 명령줄 앱은 백 채널을 통해 필요한 토큰을 수신하고 이를 사용하여 필요한 웹 API 호출을 수행합니다.

### <a name="use-it"></a>그것을 사용 하 여

# <a name="net"></a>[.NET](#tab/dotnet)

`IPublicClientApplication``AcquireTokenWithDeviceCode`라는 메서드가 포함되어 있습니다.

```csharp
 AcquireTokenWithDeviceCode(IEnumerable<string> scopes,
                            Func<DeviceCodeResult, Task> deviceCodeResultCallback)
```

이 메서드는 매개 변수로 사용 합니다.

- 에 `scopes` 대 한 액세스 토큰을 요청 합니다.
- 을 수신하는 콜백입니다. `DeviceCodeResult`

  ![장치 코드결과 속성](https://user-images.githubusercontent.com/13203188/56024968-7af1b980-5d11-11e9-84c2-5be2ef306dc5.png)

다음 샘플 코드는 얻을 수 있는 예외의 종류와 해당 완화에 대한 설명과 함께 최신 사례를 제공합니다.

```csharp
private const string ClientId = "<client_guid>";
private const string Authority = "https://login.microsoftonline.com/contoso.com";
private readonly string[] Scopes = new string[] { "user.read" };

static async Task<AuthenticationResult> GetATokenForGraph()
{
    IPublicClientApplication pca = PublicClientApplicationBuilder
            .Create(ClientId)
            .WithAuthority(Authority)
            .WithDefaultRedirectUri()
            .Build();

    var accounts = await pca.GetAccountsAsync();

    // All AcquireToken* methods store the tokens in the cache, so check the cache first
    try
    {
        return await pca.AcquireTokenSilent(Scopes, accounts.FirstOrDefault())
            .ExecuteAsync();
    }
    catch (MsalUiRequiredException ex)
    {
        // No token found in the cache or AAD insists that a form interactive auth is required (e.g. the tenant admin turned on MFA)
        // If you want to provide a more complex user experience, check out ex.Classification

        return await AcquireByDeviceCodeAsync(pca);
    }         
}

private async Task<AuthenticationResult> AcquireByDeviceCodeAsync(IPublicClientApplication pca)
{
    try
    {
        var result = await pca.AcquireTokenWithDeviceCode(scopes,
            deviceCodeResult =>
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
    // TODO: handle or throw all these exceptions depending on your app
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
        // If you use a CancellationToken, and call the Cancel() method on it, then this *may* be triggered
        // to indicate that the operation was cancelled.
        // See https://docs.microsoft.com/dotnet/standard/threading/cancellation-in-managed-threads
        // for more detailed information on how C# supports cancellation in managed threads.
    }
    catch (MsalClientException ex)
    {
        // Possible cause - verification code expired before contacting the server
        // This exception will occur if the user does not manage to sign-in before a time out (15 mins) and the
        // call to `AcquireTokenWithDeviceCode` is not cancelled in between
    }
}
```
# <a name="java"></a>[Java](#tab/java)

이 추출은 [MSAL Java 개발자 샘플에서 추출한 것입니다.](https://github.com/AzureAD/microsoft-authentication-library-for-java/blob/dev/src/samples/public-client/)

```java
private static IAuthenticationResult acquireTokenDeviceCode() throws Exception {

    // Load token cache from file and initialize token cache aspect. The token cache will have
    // dummy data, so the acquireTokenSilently call will fail.
    TokenCacheAspect tokenCacheAspect = new TokenCacheAspect("sample_cache.json");

    PublicClientApplication pca = PublicClientApplication.builder(CLIENT_ID)
            .authority(AUTHORITY)
            .setTokenCacheAccessAspect(tokenCacheAspect)
            .build();

    Set<IAccount> accountsInCache = pca.getAccounts().join();
    // Take first account in the cache. In a production application, you would filter
    // accountsInCache to get the right account for the user authenticating.
    IAccount account = accountsInCache.iterator().next();

    IAuthenticationResult result;
    try {
        SilentParameters silentParameters =
                SilentParameters
                        .builder(SCOPE, account)
                        .build();

        // try to acquire token silently. This call will fail since the token cache
        // does not have any data for the user you are trying to acquire a token for
        result = pca.acquireTokenSilently(silentParameters).join();
    } catch (Exception ex) {
        if (ex.getCause() instanceof MsalException) {

            Consumer<DeviceCode> deviceCodeConsumer = (DeviceCode deviceCode) ->
                    System.out.println(deviceCode.message());

            DeviceCodeFlowParameters parameters =
                    DeviceCodeFlowParameters
                            .builder(SCOPE, deviceCodeConsumer)
                            .build();

            // Try to acquire a token via device code flow. If successful, you should see
            // the token and account information printed out to console, and the sample_cache.json
            // file should have been updated with the latest tokens.
            result = pca.acquireToken(parameters).join();
        } else {
            // Handle other exceptions accordingly
            throw ex;
        }
    }
    return result;
}
```

# <a name="python"></a>[Python](#tab/python)

이 추출은 [MSAL 파이썬 개발 샘플에서 추출한 것입니다.](https://github.com/AzureAD/microsoft-authentication-library-for-python/blob/dev/sample/)

```Python
# Create a preferably long-lived app instance which maintains a token cache.
app = msal.PublicClientApplication(
    config["client_id"], authority=config["authority"],
    # token_cache=...  # Default cache is in memory only.
                       # You can learn how to use SerializableTokenCache from
                       # https://msal-python.rtfd.io/en/latest/#msal.SerializableTokenCache
    )

# The pattern to acquire a token looks like this.
result = None

# Note: If your device-flow app does not have any interactive ability, you can
#   completely skip the following cache part. But here we demonstrate it anyway.
# We now check the cache to see if we have some end users signed in before.
accounts = app.get_accounts()
if accounts:
    logging.info("Account(s) exists in cache, probably with token too. Let's try.")
    print("Pick the account you want to use to proceed:")
    for a in accounts:
        print(a["username"])
    # Assuming the end user chose this one
    chosen = accounts[0]
    # Now let's try to find a token in cache for this account
    result = app.acquire_token_silent(config["scope"], account=chosen)

if not result:
    logging.info("No suitable token exists in cache. Let's get a new one from AAD.")

    flow = app.initiate_device_flow(scopes=config["scope"])
    if "user_code" not in flow:
        raise ValueError(
            "Fail to create device flow. Err: %s" % json.dumps(flow, indent=4))

    print(flow["message"])
    sys.stdout.flush()  # Some terminal needs this to ensure the message is shown

    # Ideally you should wait here, in order to save some unnecessary polling
    # input("Press Enter after signing in from another device to proceed, CTRL+C to abort.")

    result = app.acquire_token_by_device_flow(flow)  # By default it will block
        # You can follow this instruction to shorten the block time
        #    https://msal-python.readthedocs.io/en/latest/#msal.PublicClientApplication.acquire_token_by_device_flow
        # or you may even turn off the blocking behavior,
        # and then keep calling acquire_token_by_device_flow(flow) in your own customized loop
```

# <a name="macos"></a>[Macos](#tab/macOS)

이 흐름은 MacOS에 적용되지 않습니다.

---

## <a name="file-based-token-cache"></a>파일 기반 토큰 캐시

MSAL.NET에서는 메모리 내 토큰 캐시가 기본적으로 제공됩니다.

### <a name="serialization-is-customizable-in-windows-desktop-apps-and-web-apps-or-web-apis"></a>직렬화는 Windows 데스크톱 앱 및 웹 앱 또는 웹 API에서 사용자 지정할 수 있습니다.

.NET Framework 및 .NET Core의 경우 추가 작업을 수행하지 않으면 인메모리 토큰 캐시가 응용 프로그램 기간 동안 지속됩니다. 직렬화가 즉시 제공되지 않는 이유를 이해하려면 MSAL .NET 데스크톱 또는 .NET Core 응용 프로그램은 콘솔 또는 Windows 응용 프로그램(파일 시스템에 액세스할 수 있음)뿐만 아니라 웹 응용 프로그램이나 웹 *API일 수* 있습니다. 이러한 웹 앱 및 웹 API는 데이터베이스, 분산 캐시 및 Redis 캐시와 같은 특정 캐시 메커니즘을 사용할 수 있습니다. .NET 데스크톱 또는 .NET Core에 영구 토큰 캐시 응용 프로그램을 사용하려면 직렬화를 사용자 지정해야 합니다.

토큰 캐시 직렬화와 관련된 클래스 및 인터페이스는 다음과 같은 유형입니다.

- ``ITokenCache``토큰 캐시 직렬화 요청을 구독하는 이벤트와 다양한 형식(ADAL v3.0, MSAL 2.x 및 MSAL 3.x = ADAL v5.0)에서 캐시를 직렬화또는 직렬화하는 메서드를 정의합니다.
- ``TokenCacheCallback``은 직렬화를 처리할 수 있도록 이벤트에 전달되는 콜백입니다. ``TokenCacheNotificationArgs`` 형식의 인수를 사용하여 호출됩니다.
- ``TokenCacheNotificationArgs``토큰을 사용할 ``ClientId`` 수 있는 사용자에 대한 응용 프로그램과 참조만 제공합니다.

  ![토큰 캐시 직렬화 다이어그램](https://user-images.githubusercontent.com/13203188/56027172-d58d1480-5d15-11e9-8ada-c0292f1800b3.png)

> [!IMPORTANT]
> 사용자가 애플리케이션의 `UserTokenCache` 및 `AppTokenCache` 속성을 호출하면 MSAL.NET은 사용자 대신 토큰 캐시를 만들고 사용자에게 `IToken` 캐시를 제공합니다. 인터페이스를 직접 구현해서는 안 됩니다. 사용자는 사용자 지정 토큰 캐시 직렬화를 구현할 때 다음과 같은 일만 하면 됩니다.
>
> - 반응 `BeforeAccess` 및 `AfterAccess` 이벤트 또는 *비동기* 대응. 대리자는`BeforeAccess` 캐시를 역직렬화할 책임이 있습니다. 대리자는 `AfterAccess` 캐시를 직렬화합니다.
> - 이러한 이벤트의 일부가 Blob을 저장하거나 로드하는 것을 이해하며, 이 Blob은 이벤트 인수를 통해 원하는 저장소에 전달됩니다.

데스크톱과 같은 공용 클라이언트 응용 프로그램또는 웹 앱이나 웹 API 또는 데몬 앱과 같은 기밀 클라이언트 응용 프로그램에 대한 토큰 캐시 직렬화를 작성하는 지 여부에 따라 전략이 다릅니다.

MSAL v2.x 이후, 당신은 몇 가지 옵션이 있습니다. 선택한 방법은 MSAL뿐만 아니라 플랫폼 전체에서 공통되는 통합 형식 캐시인 MSAL.NET 형식으로만 캐시를 직렬화할지 여부에 따라 달라집니다. 또는 ADAL v3의 [레거시](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Token-cache-serialization) 토큰 캐시 직렬화를 지원할 수도 있습니다.

ADAL.NET 3.x, ADAL.NET 5.x 및 MSAL.NET 사이 SSO 상태를 공유 하는 토큰 캐시 직렬화의 사용자 지정 샘플 [활성 디렉토리-dotnet-v1-v2의](https://github.com/Azure-Samples/active-directory-dotnet-v1-to-v2)일부에 설명 되어 있습니다.

### <a name="simple-token-cache-serialization-msal-only"></a>간단한 토큰 캐시 직렬화(MSAL만 해당)

다음 예제는 데스크톱 응용 프로그램에 대 한 토큰 캐시의 사용자 지정 직렬화의 순진한 구현입니다. 여기서 사용자 토큰 캐시는 응용 프로그램과 동일한 폴더에 있는 파일에 있습니다.

응용 프로그램을 빌드한 후 응용 프로그램을 ``TokenCacheHelper.EnableSerialization()`` `UserTokenCache`호출하고 전달하여 직렬화를 사용하도록 설정합니다.

```csharp
app = PublicClientApplicationBuilder.Create(ClientId)
    .Build();
TokenCacheHelper.EnableSerialization(app.UserTokenCache);
```

이 도우미 클래스는 다음과 같은 코드 코드 조각처럼 보입니다.

```csharp
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

Windows, Mac 및 Linux에서 실행되는 데스크톱 응용 프로그램에 대한 공용 클라이언트 응용 프로그램에 대한 제품 품질 토큰 캐시 파일 기반 serializer의 미리 보기는 [Microsoft.Identity.Client.Extensions.Msal](https://github.com/AzureAD/microsoft-authentication-extensions-for-dotnet/tree/master/src/Microsoft.Identity.Client.Extensions.Msal) 오픈 소스 라이브러리에서 확인할 수 있습니다. 다음 NuGet 패키지: [Microsoft.Identity.Client.Extensions.Msal에서](https://www.nuget.org/packages/Microsoft.Identity.Client.Extensions.Msal/)응용 프로그램에 포함할 수 있습니다.

> [!NOTE]
> 면책 조항: Microsoft.Identity.Client.Extensions.Msal 라이브러리는 MSAL.NET 대한 확장입니다. 이러한 라이브러리의 클래스는 향후 MSAL.NET, 현재 또는 주요 변경 사항으로 인해 발생할 수 있습니다.

### <a name="dual-token-cache-serialization-msal-unified-cache--adal-v3"></a>듀얼 토큰 캐시 직렬화(MSAL 통합 캐시 + ADAL v3)

통합 캐시 형식으로 토큰 캐시 직렬화를 구현할 수 있습니다. 이 형식은 4.x ADAL.NET MSAL.NET 2.x, 동일한 세대 이상의 다른 MSAL과 동일한 플랫폼에서 일반적입니다. 다음 코드에서 영감을 얻으십시오.

```csharp
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

이번에는 도우미 클래스가 다음과 같은 코드와 같습니다.

```csharp
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
> [데스크톱 앱에서 웹 API 호출](scenario-desktop-call-api.md)
