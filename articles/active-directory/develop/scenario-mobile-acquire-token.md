---
title: 웹 API 호출을 위한 토큰 획득(모바일 앱) | Azure
titleSuffix: Microsoft identity platform
description: 웹 API를 호출하는 모바일 앱을 빌드하는 방법에 대해 알아봅니다. (앱용 토큰 획득)
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.reviewer: brandwe
ms.custom: aaddev
ms.openlocfilehash: 6e6eda3d711710ea7450165ab02d7a260067bfcb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99582556"
---
# <a name="get-a-token-for-a-mobile-app-that-calls-web-apis"></a>웹 API를 호출하는 모바일 앱에 대한 토큰 가져오기

앱이 보호된 웹 API를 호출하려면 액세스 토큰이 필요합니다. 이 문서에서는 MSAL(Microsoft 인증 라이브러리)을 사용하여 토큰을 가져오는 프로세스를 안내합니다.

## <a name="define-a-scope"></a>범위 정의

토큰을 요청하는 경우 범위를 정의합니다. 범위는 앱이 액세스할 수 있는 데이터를 결정합니다.

범위를 정의하는 가장 쉬운 방법은 원하는 웹 API의 `App ID URI`와 범위 `.default`를 결합하는 것입니다. 이 정의는 앱이 포털에 설정된 모든 범위가 필요함을 Microsoft ID 플랫폼에 알려줍니다.

### <a name="android"></a>Android
```Java
String[] SCOPES = {"https://graph.microsoft.com/.default"};
```

### <a name="ios"></a>iOS
```swift
let scopes = ["https://graph.microsoft.com/.default"]
```

### <a name="xamarin"></a>Xamarin
```csharp
var scopes = new [] {"https://graph.microsoft.com/.default"};
```

## <a name="get-tokens"></a>토큰 획득

### <a name="acquire-tokens-via-msal"></a>MSAL을 통해 토큰 획득

MSAL을 사용하면 앱에서 토큰을 자동으로, 그리고 대화형으로 가져올 수 있습니다. `AcquireTokenSilent()` 또는 `AcquireTokenInteractive()`를 호출할 때 MSAL은 요청된 범위에 대한 액세스 토큰을 반환합니다. 올바른 패턴은 자동 요청을 만든 다음, 대화형 요청으로 대체하는 것입니다.

#### <a name="android"></a>Android

```Java
String[] SCOPES = {"https://graph.microsoft.com/.default"};
PublicClientApplication sampleApp = new PublicClientApplication(
                    this.getApplicationContext(),
                    R.raw.auth_config);

// Check if there are any accounts we can sign in silently.
// Result is in the silent callback (success or error).
sampleApp.getAccounts(new PublicClientApplication.AccountsLoadedCallback() {
    @Override
    public void onAccountsLoaded(final List<IAccount> accounts) {

        if (accounts.isEmpty() && accounts.size() == 1) {
            // TODO: Create a silent callback to catch successful or failed request.
            sampleApp.acquireTokenSilentAsync(SCOPES, accounts.get(0), getAuthSilentCallback());
        } else {
            /* No accounts or > 1 account. */
        }
    }
});

[...]

// No accounts found. Interactively request a token.
// TODO: Create an interactive callback to catch successful or failed requests.
sampleApp.acquireToken(getActivity(), SCOPES, getAuthInteractiveCallback());
```

#### <a name="ios"></a>iOS

먼저 토큰을 자동으로 획득해 봅니다.

```objc

NSArray *scopes = @[@"https://graph.microsoft.com/.default"];
NSString *accountIdentifier = @"my.account.id";

MSALAccount *account = [application accountForIdentifier:accountIdentifier error:nil];

MSALSilentTokenParameters *silentParams = [[MSALSilentTokenParameters alloc] initWithScopes:scopes account:account];
[application acquireTokenSilentWithParameters:silentParams completionBlock:^(MSALResult *result, NSError *error) {

    if (!error)
    {
        // You'll want to get the account identifier to retrieve and reuse the account
        // for later acquireToken calls
        NSString *accountIdentifier = result.account.identifier;

        // Access token to call the web API
        NSString *accessToken = result.accessToken;
    }

    // Check the error
    if (error && [error.domain isEqual:MSALErrorDomain] && error.code == MSALErrorInteractionRequired)
    {
        // Interactive auth will be required, call acquireTokenWithParameters:error:
        return;
    }
}];
```

```swift

let scopes = ["https://graph.microsoft.com/.default"]
let accountIdentifier = "my.account.id"

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

    // You'll want to get the account identifier to retrieve and reuse the account
    // for later acquireToken calls
    let accountIdentifier = authResult.account.identifier

    // Access token to call the web API
    let accessToken = authResult.accessToken
}
```

MSAL이 `MSALErrorInteractionRequired`를 반환하는 경우 토큰을 대화형으로 획득해 봅니다.

```objc
UIViewController *viewController = ...; // Pass a reference to the view controller that should be used when getting a token interactively
MSALWebviewParameters *webParameters = [[MSALWebviewParameters alloc] initWithAuthPresentationViewController:viewController];
MSALInteractiveTokenParameters *interactiveParams = [[MSALInteractiveTokenParameters alloc] initWithScopes:scopes webviewParameters:webParameters];
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

```swift
let viewController = ... // Pass a reference to the view controller that should be used when getting a token interactively
let webviewParameters = MSALWebviewParameters(authPresentationViewController: viewController)
let interactiveParameters = MSALInteractiveTokenParameters(scopes: scopes, webviewParameters: webviewParameters)
application.acquireToken(with: interactiveParameters, completionBlock: { (result, error) in

    guard let authResult = result, error == nil else {
        print(error!.localizedDescription)
        return
    }

    // Get access token from result
    let accessToken = authResult.accessToken
})
```

iOS 및 macOS용 MSAL은 토큰을 대화형으로 또는 자동으로 가져오기 위해 다양한 한정자를 지원합니다.
* [토큰을 가져오기 위한 일반 매개 변수](https://azuread.github.io/microsoft-authentication-library-for-objc/Classes/MSALTokenParameters.html#/Configuration%20parameters)
* [대화형 토큰을 가져오기 위한 매개 변수](https://azuread.github.io/microsoft-authentication-library-for-objc/Classes/MSALInteractiveTokenParameters.html#/Configuring%20MSALInteractiveTokenParameters)
* [자동 토큰을 가져오기 위한 매개 변수](https://azuread.github.io/microsoft-authentication-library-for-objc/Classes/MSALSilentTokenParameters.html)

#### <a name="xamarin"></a>Xamarin

다음 예제에서는 대화형으로 토큰을 가져오기 위한 최소한의 코드를 보여줍니다. 이 예제에서는 Microsoft Graph를 사용하여 사용자의 프로필을 읽습니다.

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

#### <a name="mandatory-parameters-in-msalnet"></a>MSAL.NET의 필수 매개 변수

`AcquireTokenInteractive`에는 하나의 필수 매개 변수 `scopes`가 있습니다. `scopes` 매개 변수는 토큰이 필요한 범위를 정의하는 문자열을 열거합니다. 토큰이 Microsoft Graph용인 경우 각 Microsoft Graph API의 API 참조에서 필요한 범위를 확인할 수 있습니다. 해당 참조에서 "사용 권한" 섹션으로 이동합니다.

예를 들어 [사용자 연락처를 나열하려면](/graph/api/user-list-contacts) "User.Read", "Contacts.Read" 범위를 사용합니다. 자세한 내용은 [Microsoft Graph 사용 권한 참조](/graph/permissions-reference)를 참조하세요.

Android에서 `PublicClientApplicationBuilder`를 사용하여 앱을 만들 때 부모 활동을 지정할 수 있습니다. 이때 부모 활동을 지정하지 않으면 다음 섹션에서와 같이 나중에 `.WithParentActivityOrWindow`를 사용하여 지정할 수 있습니다. 부모 활동을 지정하면 상호 작용 후에 토큰이 부모 활동으로 돌아갑니다. 지정하지 않으면 `.ExecuteAsync()` 호출에서 예외가 발생합니다.

#### <a name="specific-optional-parameters-in-msalnet"></a>MSAL.NET의 특정 선택적 매개 변수

다음 섹션에서는 MSAL.NET의 선택적 매개 변수에 대해 설명합니다.

##### <a name="withprompt"></a>WithPrompt

`WithPrompt()` 매개 변수는 프롬프트를 지정하여 사용자와의 대화형 작업을 제어합니다.

![프롬프트 구조의 필드를 표시하는 이미지입니다. 이러한 상수 값은 WithPrompt() 매개 변수에서 표시하는 프롬프트 형식을 정의하여 사용자와의 대화형 작업을 제어합니다.](https://user-images.githubusercontent.com/13203188/53438042-3fb85700-39ff-11e9-9a9e-1ff9874197b3.png)

클래스는 다음과 같은 상수를 정의합니다.

- `SelectAccount`는 STS(보안 토큰 서비스)가 계정 선택 대화 상자를 표시하도록 합니다. 이 대화 상자에는 사용자가 가진 세션에 대한 계정이 포함되어 있습니다. 사용자가 다양한 ID 중에서 선택할 수 있도록 하려면 이 옵션을 사용할 수 있습니다. 이 옵션은 MSAL이 ID 공급자에게 `prompt=select_account`를 보내도록 유도합니다.

    `SelectAccount` 상수는 기본값이며 사용 가능한 정보에 따라 최상의 환경을 효과적으로 제공합니다. 사용 가능한 정보에는 계정, 사용자에 대한 세션의 현재 상태 등이 포함될 수 있습니다. 특별한 이유가 없는 한 기본값은 변경하지 마세요.
- `Consent`를 이용하면 이전에 동의를 얻은 경우에도 사용자에게 동의에 대한 메시지를 표시할 수 있습니다. 이 경우 MSAL이 ID 공급자에게 `prompt=consent`을 보냅니다.

    조직 거버넌스에 따라 애플리케이션을 사용할 때마다 사용자에게 동의 대화 상자를 표시해야 하는 보안 중심 애플리케이션에서 `Consent` 상수를 사용하고 싶을 수 있습니다.
- `ForceLogin`을 이용하면 프롬프트가 필요하지 않은 경우에도 사용자에게 자격 증명을 묻는 메시지를 표시할 수 있습니다.

    이 옵션은 토큰 획득에 실패하고 사용자를 다시 로그인시키고 싶을 때 유용할 수 있습니다. 이 경우 MSAL이 ID 공급자에게 `prompt=login`을 보냅니다. 조직 거버넌스에 따라 애플리케이션의 특정 부분에 액세스할 때마다 사용자에게 로그인하도록 하는 보안 중심 애플리케이션에서 이 옵션을 사용하고 싶을 수 있습니다.
- `Never`는 .NET 4.5 및 Windows 런타임(WinRT)에만 해당됩니다. 이 상수는 사용자에게 메시지를 표시하지 않지만 숨겨진 임베디드 웹뷰에 저장된 쿠키를 사용하려고 할 것입니다. 자세한 내용은 [MSAL.NET을 이용한 웹 브라우저 사용](./msal-net-web-browsers.md)을 참조하세요.

    이 옵션이 실패하면 `AcquireTokenInteractive`가 예외를 발생시켜 UI 조작이 필요하다는 것을 알려줍니다. 그런 다음, 다른 `Prompt` 매개 변수를 사용합니다.
- `NoPrompt`는 ID 공급자에게 프롬프트를 보내지 않습니다.

    이 옵션은 Azure Active Directory B2C 편집 프로필 정책에서만 유용합니다. 자세한 내용은 [B2C 관련 사항](https://aka.ms/msal-net-b2c-specificities)을 참조하세요.

##### <a name="withextrascopetoconsent"></a>WithExtraScopeToConsent

사용자가 여러 리소스에 대한 사전 동의를 제공하도록 하려는 고급 시나리오에서 `WithExtraScopeToConsent` 한정자를 사용합니다. MSAL.NET 또는 Microsoft ID 플랫폼에서 일반적으로 사용되는 증분 동의를 사용하고 싶지 않은 경우 이 한정자를 사용할 수 있습니다. 자세한 내용은 [처음에 여러 리소스에 대한 사용자 동의 받기](scenario-desktop-production.md#have-the-user-consent-upfront-for-several-resources)를 참조하세요.

코드 예제는 다음과 같습니다.

```csharp
var result = await app.AcquireTokenInteractive(scopesForCustomerApi)
                     .WithExtraScopeToConsent(scopesForVendorApi)
                     .ExecuteAsync();
```

##### <a name="other-optional-parameters"></a>그 밖의 선택적 매개 변수

`AcquireTokenInteractive`의 다른 선택적 매개 변수에 대해 알아보려면 [AcquireTokenInteractiveParameterBuilder 참조 문서](/dotnet/api/microsoft.identity.client.acquiretokeninteractiveparameterbuilder#methods)를 참조하세요.

### <a name="acquire-tokens-via-the-protocol"></a>프로토콜을 통해 토큰 획득

프로토콜을 직접적으로 사용하여 토큰을 획득하는 것은 권장하지 않습니다. 이렇게 하면 앱이 SSO(Single Sign-On), 디바이스 관리, 조건부 액세스 등을 포함하는 일부 시나리오를 지원하지 않습니다.

프로토콜을 사용하여 모바일 앱에 대한 토큰을 가져오는 경우 다음 두 요청을 수행합니다.

* 인증 코드를 가져옵니다.
* 토큰에 대한 코드를 교환합니다.

#### <a name="get-an-authorization-code"></a>권한 부여 코드 가져오기

```
https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize?
client_id=<CLIENT_ID>
&response_type=code
&redirect_uri=<ENCODED_REDIRECT_URI>
&response_mode=query
&scope=openid%20offline_access%20https%3A%2F%2Fgraph.microsoft.com%2F.default
&state=12345
```

#### <a name="get-access-and-refresh-the-token"></a>액세스 권한 가져오기 및 토큰 새로 고침

```HTTP
POST /{tenant}/oauth2/v2.0/token HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

client_id=<CLIENT_ID>
&scope=https%3A%2F%2Fgraph.microsoft.com%2F.default
&code=OAAABAAAAiL9Kn2Z27UubvWFPbm0gLWQJVzCTE9UkP3pSx1aXxUjq3n8b2JRLk4OxVXr...
&redirect_uri=<ENCODED_REDIRECT_URI>
&grant_type=authorization_code
```

## <a name="next-steps"></a>다음 단계

이 시나리오의 다음 문서 [웹 API 호출](scenario-mobile-call-api.md)로 이동합니다.