---
title: 웹 API(모바일 앱)를 호출하는 토큰 획득 | Azure
titleSuffix: Microsoft identity platform
description: 웹 API를 호출하는 모바일 앱을 빌드하는 방법을 알아봅니다. (앱에 대한 토큰을 가져옵니다.)
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
ms.openlocfilehash: 5750f4a5aa62b33c7d793b3e0c34f304ce1b187e
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81535930"
---
# <a name="get-a-token-for-a-mobile-app-that-calls-web-apis"></a>웹 API를 호출하는 모바일 앱에 대한 토큰 받기

앱에서 보호된 웹 API를 호출하려면 액세스 토큰이 필요합니다. 이 문서에서는 MSAL(Microsoft 인증 라이브러리)을 사용하여 토큰을 얻는 프로세스를 안내합니다.

## <a name="define-a-scope"></a>범위 정의

토큰을 요청할 때 범위를 정의해야 합니다. 범위는 앱에서 액세스할 수 있는 데이터를 결정합니다.

범위를 정의하는 가장 쉬운 방법은 원하는 웹 API를 `App ID URI` 범위와 `.default`결합하는 것입니다. 이 정의는 앱에 포털에 설정된 모든 범위가 필요하다는 것을 Microsoft ID 플랫폼에 알려줍니다.

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

## <a name="get-tokens"></a>토큰 받기

### <a name="acquire-tokens-via-msal"></a>MSAL을 통해 토큰 획득

MSAL을 사용하면 앱이 자동으로 대화식으로 토큰을 획득할 수 있습니다. 호출하거나 `AcquireTokenSilent()` `AcquireTokenInteractive()`, MSAL은 요청된 범위에 대한 액세스 토큰을 반환합니다. 올바른 패턴은 자동 요청을 한 다음 대화형 요청으로 대체되는 것입니다.

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

먼저 토큰을 자동으로 획득해 보십시오.

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

MSAL이 `MSALErrorInteractionRequired`반환되면 대화식으로 토큰을 획득해 보십시오.

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

iOS 및 macOS용 MSAL은 대화형 또는 자동적으로 토큰을 얻기 위해 다양한 수정자를 지원합니다.
* [토큰 을 얻기위한 일반적인 매개 변수](https://azuread.github.io/microsoft-authentication-library-for-objc/Classes/MSALTokenParameters.html#/Configuration%20parameters)
* [대화형 토큰을 얻기 위한 매개 변수](https://azuread.github.io/microsoft-authentication-library-for-objc/Classes/MSALInteractiveTokenParameters.html#/Configuring%20MSALInteractiveTokenParameters)
* [자동 토큰을 얻기 위한 매개 변수](https://azuread.github.io/microsoft-authentication-library-for-objc/Classes/MSALSilentTokenParameters.html)

#### <a name="xamarin"></a>Xamarin

다음 예제에서는 토큰을 대화식으로 얻을 수 있는 최소 코드를 보여 주습니다. 이 예제에서는 Microsoft 그래프를 사용하여 사용자의 프로필을 읽습니다.

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

#### <a name="mandatory-parameters-in-msalnet"></a>MSAL.NET 필수 매개 변수

`AcquireTokenInteractive`필수 매개 변수는 `scopes`하나만 있습니다. 매개 `scopes` 변수는 토큰이 필요한 범위를 정의하는 문자열을 개명합니다. 토큰이 Microsoft 그래프용인 경우 각 Microsoft 그래프 API의 API 참조에서 필요한 범위를 찾을 수 있습니다. 참조에서 "권한" 섹션으로 이동합니다.

예를 들어 [사용자의 연락처를 나열하려면](https://developer.microsoft.com/graph/docs/api-reference/v1.0/api/user_list_contacts)"User.Read", "Contacts.Read" 범위를 사용합니다. 자세한 내용은 [Microsoft 그래프 사용 권한 참조를](https://developer.microsoft.com/graph/docs/concepts/permissions_reference)참조하십시오.

Android에서 `PublicClientApplicationBuilder`응용 프로그램을 만들 때 부모 활동을 지정할 수 있습니다. 해당 시점에 상위 활동을 지정하지 않으면 나중에 다음 섹션과 `.WithParentActivityOrWindow` 같이 사용하여 부모 활동을 지정할 수 있습니다. 상위 활동을 지정하면 상호 작용 후 토큰이 해당 상위 활동으로 돌아갑니다. 지정하지 않으면 호출에서 `.ExecuteAsync()` 예외를 throw합니다.

#### <a name="specific-optional-parameters-in-msalnet"></a>MSAL.NET 특정 선택적 매개 변수

다음 섹션에서는 MSAL.NET 선택적 매개 변수를 설명합니다.

##### <a name="withprompt"></a>프롬프트

매개 `WithPrompt()` 변수는 프롬프트를 지정하여 사용자와의 상호 작용을 제어합니다.

<img src="https://user-images.githubusercontent.com/13203188/53438042-3fb85700-39ff-11e9-9a9e-1ff9874197b3.png" width="25%" />

클래스는 다음 상수를 정의합니다.

- `SelectAccount`을 강제로 보안 토큰 서비스(STS)하여 계정 선택 대화 상자를 표시합니다. 대화 상자에는 사용자에게 세션이 있는 계정이 포함되어 있습니다. 사용자가 다른 ID 중에서 선택할 수 있도록 하려는 경우 이 옵션을 사용할 수 있습니다. 이 옵션은 MSAL을 `prompt=select_account` ID 공급자에게 보냅니다.

    상수는 `SelectAccount` 기본값이며 사용 가능한 정보를 기반으로 최상의 환경을 효과적으로 제공합니다. 사용 가능한 정보에는 계정, 사용자에 대한 세션의 존재 등이 포함될 수 있습니다. 이 기본값을 변경할 만한 이유가 없는 한 변경하지 마십시오.
- `Consent`이전에 동의가 부여된 경우에도 사용자에게 동의를 요청하면 됩니다. 이 경우 MSAL은 `prompt=consent` ID 공급자에게 보냅니다.

    조직 거버넌스에서 사용자가 `Consent` 응용 프로그램을 사용할 때마다 동의 대화 상자를 볼 것을 요구하는 보안 중심 응용 프로그램에서 상수를 사용할 수 있습니다.
- `ForceLogin`을 사용하면 프롬프트가 필요하지 않은 경우에도 사용자에게 자격 증명을 묻는 메시지가 표시됩니다.

    이 옵션은 토큰 획득에 실패하고 사용자가 다시 로그인하도록 하려는 경우에 유용할 수 있습니다. 이 경우 MSAL은 `prompt=login` ID 공급자에게 보냅니다. 조직 거버넌스에서 사용자가 응용 프로그램의 특정 부분에 액세스할 때마다 로그인해야 하는 보안 중심 응용 프로그램에서 이 옵션을 사용할 수 있습니다.
- `Never`.NET 4.5 및 Windows 런타임(WinRT)에 만 사용할 수 있습니다. 이 상수는 사용자에게 메시지를 표시하지 않지만 숨겨진 포함된 웹 보기에 저장된 쿠키를 사용하려고 시도합니다. 자세한 내용은 [MSAL.NET 있는 웹 브라우저 사용을 참조하십시오.](https://docs.microsoft.com/azure/active-directory/develop/msal-net-web-browsers)

    이 옵션이 실패하면 `AcquireTokenInteractive` 예외를 throw하여 UI 상호 작용이 필요하다는 것을 알립니다. 그런 다음 다른 `Prompt` 매개 변수를 사용해야합니다.
- `NoPrompt`을 사용 하려면 메시지를 ID 공급자에 보내지 않습니다.

    이 옵션은 Azure Active Directory B2C의 편집 프로필 정책에만 유용합니다. 자세한 내용은 [B2C 세부 사항을](https://aka.ms/msal-net-b2c-specificities)참조하십시오.

##### <a name="withextrascopetoconsent"></a>엑스트라스코프ToConsent

사용자가 `WithExtraScopeToConsent` 여러 리소스에 대한 사전 동의를 제공하려는 고급 시나리오에서 수정자를 사용합니다. 일반적으로 MSAL.NET 또는 Microsoft ID 플랫폼 2.0에서 사용되는 증분 동의를 사용하지 않으려는 경우 이 수정자를 사용할 수 있습니다. 자세한 내용은 [여러 리소스에 대한 사용자 동의를 미리 확인을](scenario-desktop-production.md#have-the-user-consent-upfront-for-several-resources)참조하십시오.

코드 예제는 다음과 같습니다.

```csharp
var result = await app.AcquireTokenInteractive(scopesForCustomerApi)
                     .WithExtraScopeToConsent(scopesForVendorApi)
                     .ExecuteAsync();
```

##### <a name="other-optional-parameters"></a>기타 선택적 매개 변수

에 대 `AcquireTokenInteractive`한 다른 선택적 매개 변수에 대 한 자세한 내용은 [AcquireTokenInteractiveParameterBuilder에 대 한 참조 설명서를](/dotnet/api/microsoft.identity.client.acquiretokeninteractiveparameterbuilder?view=azure-dotnet-preview#methods)참조 하십시오.

### <a name="acquire-tokens-via-the-protocol"></a>프로토콜을 통해 토큰 획득

토큰을 얻기 위해 프로토콜을 직접 사용하지 않는 것이 좋습니다. 이렇게 하면 앱은 단일 사인온(SSO), 장치 관리 및 조건부 액세스를 포함하는 일부 시나리오를 지원하지 않습니다.

프로토콜을 사용하여 모바일 앱에 대한 토큰을 얻을 때 다음 두 가지 요청을 합니다.

* 권한 부여 코드를 가져옵니다.
* 토큰에 대한 코드를 교환합니다.

#### <a name="get-an-authorization-code"></a>권한 부여 코드 가져오기

```Text
https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize?
client_id=<CLIENT_ID>
&response_type=code
&redirect_uri=<ENCODED_REDIRECT_URI>
&response_mode=query
&scope=openid%20offline_access%20https%3A%2F%2Fgraph.microsoft.com%2F.default
&state=12345
```

#### <a name="get-access-and-refresh-the-token"></a>액세스 권한 얻기 및 토큰 새로 고침

```Text
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

> [!div class="nextstepaction"]
> [웹 API 호출](scenario-mobile-call-api.md)
