---
title: 웹 Api를 호출 하는 모바일 앱-앱에 대 한 토큰을 가져오는 중 | Microsoft id 플랫폼
description: 웹 Api를 호출 하는 모바일 앱을 빌드하는 방법 알아보기 (앱에 대 한 토큰 가져오기)
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
ms.reviwer: brandwe
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 454d62f871290d2e7dd8d0eee4b1a2429625a5fc
ms.sourcegitcommit: 263a69b70949099457620037c988dc590d7c7854
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/25/2019
ms.locfileid: "71268262"
---
# <a name="mobile-app-that-calls-web-apis---get-a-token"></a>웹 Api를 호출 하는 모바일 앱-토큰 가져오기

보호 되는 웹 Api 호출을 시작 하기 전에 앱에 액세스 토큰이 필요 합니다. 이 문서에서는 MSAL (Microsoft 인증 라이브러리)을 사용 하 여 토큰을 가져오는 과정을 안내 합니다.

## <a name="scopes-to-request"></a>요청할 범위

토큰을 요청 하는 경우 범위를 정의 해야 합니다. 범위는 앱이 액세스할 수 있는 데이터를 결정 합니다.  

가장 쉬운 방법은 원하는 web API `App ID URI` 를 범위 `.default`와 결합 하는 것입니다. 이렇게 하면 앱이 포털에서 모든 범위를 설정 해야 한다는 Microsoft id 플랫폼이 Microsoft id 플랫폼에 알려 줍니다.

#### <a name="android"></a>Android
```Java
String[] SCOPES = {"https://graph.microsoft.com/.default"};
```

#### <a name="ios"></a>iOS
```swift
let scopes = ["https://graph.microsoft.com/.default"]
```

#### <a name="xamarin"></a>Xamarin
```CSharp 
var scopes = new [] {"https://graph.microsoft.com/.default"};
```

## <a name="get-tokens"></a>토큰 가져오기

### <a name="acquire-tokens-via-msal"></a>MSAL을 통해 토큰 획득

MSAL을 사용 하면 앱에서 토큰을 자동으로 및 대화형으로 가져올 수 있습니다. 이러한 메서드를 호출 하기만 하면 MSAL이 요청 된 범위에 대 한 액세스 토큰을 반환 합니다. 올바른 패턴은 자동 요청을 수행 하 고 대화형 요청으로 대체 하는 것입니다.

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
// TODO: Create an interactive callback to catch successful or failed request.
sampleApp.acquireToken(getActivity(), SCOPES, getAuthInteractiveCallback());        
```

#### <a name="ios"></a>iOS

**먼저 토큰을 자동으로 획득 해 봅니다.**

Objective-C:

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
            
        // Access token to call the Web API
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
 
Swift

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
            
    // Access token to call the Web API
    let accessToken = authResult.accessToken
}
```

**그런 다음 msal이 `MSALErrorInteractionRequired`반환 되 면 대화형으로 토큰을 획득 해 봅니다.**

Objective-C:

```objc
UIViewController *viewController = ...; // Pass a reference to the view controller that should be used when getting a token interactively
MSALWebviewParameters *webParameters = [[MSALWebviewParameters alloc] initWithParentViewController:viewController];
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

Swift

```swift
let viewController = ... // Pass a reference to the view controller that should be used when getting a token interactively
let webviewParameters = MSALWebviewParameters(parentViewController: viewController)
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

IOS 및 macOS 용 MSAL은 토큰을 대화형 또는 자동으로 가져올 때 다양 한 한정자를 지원 합니다.
* [토큰을 가져올 때 일반 매개 변수](https://azuread.github.io/microsoft-authentication-library-for-objc/Classes/MSALTokenParameters.html#/Configuration%20parameters)
* [대화형 토큰 획득을 위한 매개 변수](https://azuread.github.io/microsoft-authentication-library-for-objc/Classes/MSALInteractiveTokenParameters.html#/Configuring%20MSALInteractiveTokenParameters)
* [자동 토큰 획득에 대 한 매개 변수](https://azuread.github.io/microsoft-authentication-library-for-objc/Classes/MSALSilentTokenParameters.html)

#### <a name="xamarin"></a>Xamarin

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

#### <a name="mandatory-parameters-in-msalnet"></a>MSAL.NET의 필수 매개 변수

`AcquireTokenInteractive`에는 토큰을 필요로 ``scopes``하는 범위를 정의 하는 문자열의 열거형을 포함 하는 필수 매개 변수가 하나 뿐입니다. Microsoft Graph에 대 한 토큰 인 경우 필요한 범위는 "권한" 섹션에서 각 Microsoft Graph API의 api 참조에서 찾을 수 있습니다. 예를 들어 [사용자의 연락처를 나열](https://developer.microsoft.com/graph/docs/api-reference/v1.0/api/user_list_contacts)하려면 "사용자. 읽기", "연락처. 읽기" 범위를 사용 해야 합니다. 참고 항목 [Microsoft Graph 사용 권한 참조](https://developer.microsoft.com/graph/docs/concepts/permissions_reference)합니다.

앱을 빌드할 때 지정 하지 않은 경우에는 Android에서 부모 작업 ( `.WithParentActivityOrWindow`아래 참조)도 지정 하 여 상호 작용 후에 해당 부모 작업으로 토큰을 다시 가져옵니다. 지정 하지 않으면를 호출할 `.ExecuteAsync()`때 예외가 throw 됩니다.

#### <a name="specific-optional-parameters-in-msalnet"></a>MSAL.NET의 특정 선택적 매개 변수

##### <a name="withprompt"></a>WithPrompt

`WithPrompt()`는 프롬프트를 지정 하 여 사용자와의 상호 작용을 제어 하는 데 사용 됩니다.

<img src="https://user-images.githubusercontent.com/13203188/53438042-3fb85700-39ff-11e9-9a9e-1ff9874197b3.png" width="25%" />

클래스는 다음 상수를 정의 합니다.

- ``SelectAccount``: STS가 사용자에 게 세션이 있는 계정을 포함 하는 계정 선택 대화 상자를 표시 하도록 합니다. 이 옵션은 응용 프로그램 개발자가 다양 한 id 중에서 선택할 수 있도록 하려는 경우에 유용 합니다. 이 옵션은 id 공급자에 게 ``prompt=select_account`` 보낼 msal을 구동 합니다. 이 옵션은 기본적으로 사용 가능한 정보 (계정, 사용자에 대 한 세션의 상태 등)를 기반으로 최상의 환경을 제공 하는 데 유용 합니다. ...). 이 작업을 수행 해야 하는 경우를 제외 하 고는 변경 하지 마십시오.
- ``Consent``: 응용 프로그램 개발자가 이전에 동의가 부여 된 경우에도 사용자에 게 동의 여부를 묻는 메시지를 표시 하도록 합니다. 이 경우 msal은 id 공급자 `prompt=consent` 에 게 보냅니다. 이 옵션은 조직에서 응용 프로그램이 사용 될 때마다 사용자에 게 동의 대화 상자가 표시 되는 일부 보안 중심 응용 프로그램에서 사용할 수 있습니다.
- ``ForceLogin``:이 사용자 프롬프트가 필요 하지 않은 경우에도 응용 프로그램 개발자가 서비스에서 자격 증명을 묻는 메시지를 표시 하도록 할 수 있습니다. 이 옵션은 토큰을 획득 하는 데 실패 하 여 사용자가 다시 로그인 할 수 있도록 하는 경우에 유용할 수 있습니다. 이 경우 msal은 id 공급자 `prompt=login` 에 게 보냅니다. 이는 조직에서 사용자가 응용 프로그램의 특정 부분에 액세스할 때마다 다시 로그인 하는 것을 요구 하는 일부 보안 중심 응용 프로그램에서 사용 되었습니다.
- ``Never``(.NET 4.5 및 WinRT에만 해당) 사용자에 게 메시지를 표시 하지 않고, 대신 숨겨진 포함 웹 보기에 저장 된 쿠키를 사용 하려고 합니다 (아래 참조). MSAL.NET의 웹 보기). 이 옵션을 사용 하면 오류가 발생할 수 있으며, `AcquireTokenInteractive` 이 경우 UI 조작이 필요 하다는 알림을 표시 하는 예외를 throw 하 고 다른 `Prompt` 매개 변수를 사용 해야 합니다.
- ``NoPrompt``: 프롬프트를 id 공급자에 게 보내지 않습니다. 이 옵션은 Azure AD B2C 프로필 정책 편집에만 유용 합니다 ( [B2C 특정](https://aka.ms/msal-net-b2c-specificities)항목 참조).

##### <a name="withextrascopetoconsent"></a>WithExtraScopeToConsent

이 한정자는 사용자가 사전에 여러 리소스에 대 한 동의를 원하는 고급 시나리오에서 사용 됩니다 (일반적으로 MSAL.NET/Microsoft id platform v 2.0에서 사용 되는 증분 동의를 사용 하지 않음). 자세한 내용은 [방법: 사용자에 게 여러 리소스에 대 한 사전 동의가 필요](scenario-desktop-production.md#how-to-have--the-user-consent-upfront-for-several-resources)합니다.

```CSharp
var result = await app.AcquireTokenInteractive(scopesForCustomerApi)
                     .WithExtraScopeToConsent(scopesForVendorApi)
                     .ExecuteAsync();
```

##### <a name="other-optional-parameters"></a>기타 선택적 매개 변수

`AcquireTokenInteractive` [AcquireTokenInteractiveParameterBuilder](/dotnet/api/microsoft.identity.client.acquiretokeninteractiveparameterbuilder?view=azure-dotnet-preview#methods) 에 대 한 참조 설명서에서의 다른 모든 선택적 매개 변수에 대 한 자세한 정보

### <a name="acquire-tokens-via-the-protocol"></a>프로토콜을 통해 토큰 획득

프로토콜을 직접 사용 하지 않는 것이 좋습니다. 이렇게 하면 앱에서 일부 SSO (single sign-on), 장치 관리 및 조건부 액세스 시나리오를 지원 하지 않습니다.

프로토콜을 사용 하 여 모바일 앱에 대 한 토큰을 가져오는 경우 두 요청을 수행 해야 합니다. 권한 부여 코드를 가져오고 토큰에 대해 교환 합니다.

#### <a name="get-authorization-code"></a>인증 코드 가져오기

```Text
https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize?
client_id=<CLIENT_ID>
&response_type=code
&redirect_uri=<ENCODED_REDIRECT_URI>
&response_mode=query
&scope=openid%20offline_access%20https%3A%2F%2Fgraph.microsoft.com%2F.default
&state=12345
```

#### <a name="get-access-and-refresh-token"></a>액세스 및 새로 고침 토큰 가져오기

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
> [Web API 호출](scenario-mobile-call-api.md)
