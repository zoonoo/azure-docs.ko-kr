---
title: 호출 웹 Api 앱에 대 한 토큰을 가져오는 되는 모바일 앱 | Microsoft id 플랫폼
description: 웹 Api (응용 프로그램에 대 한 토큰을 가져오는)를 호출 하는 모바일 앱을 빌드하는 방법을 알아봅니다
services: active-directory
documentationcenter: dev-center-name
author: danieldobalian
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
ms.openlocfilehash: 590184c25fa0aa3cb3219aa9c185a31e62090ba9
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67111138"
---
# <a name="mobile-app-that-calls-web-apis---get-a-token"></a>모바일 앱 웹 Api를 호출 하는 토큰을 가져옵니다.

보호를 호출 하기 전에 웹 Api 앱 액세스 토큰이 필요 합니다. 이 문서에서는 Microsoft 인증 라이브러리 (MSAL)를 사용 하 여 토큰을 가져오는 과정을 안내 합니다.

## <a name="scopes-to-request"></a>요청에 범위

토큰을 요청 하는 경우 범위를 정의 해야 합니다. 범위는 앱에 액세스할 수 있는 데이터를 결정 합니다.  

가장 쉬운 방법은 원하는 웹 API를 결합 하는 것 `App ID URI` 범위를 사용 하 여 `.default`입니다. 이렇게 앱 포털에서 모든 범위를 설정 해야 하는 Microsoft id 플랫폼을 알려 줍니다.

#### <a name="android"></a>Android
```Java
String[] SCOPES = {"https://graph.microsoft.com/.default"};
```

#### <a name="ios"></a>iOS
```swift
let scopes: [String] = ["https://graph.microsoft.com/.default"]
```

#### <a name="xamarin"></a>Xamarin
```CSharp 
var scopes = new [] {"https://graph.microsoft.com/.default"};
```

## <a name="get-tokens"></a>토큰 가져오기

### <a name="via-msal"></a>Via MSAL

MSAL에 앱을을 자동으로 하 고 대화형으로 토큰을 획득할 수 있습니다. 이러한 메서드 호출 및 MSAL 요청된 범위에 대 한 액세스 토큰을 반환 합니다. 올바른 패턴은 자동 요청을 수행 하 고 대화형 요청으로 대체 하는 것입니다.

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

```swift
// Initialize the app.
guard let authorityURL = URL(string: kAuthority) else {
    self.loggingText.text = "Unable to create authority URL"
    return
}
let authority = try MSALAADAuthority(url: authorityURL)
let msalConfiguration = MSALPublicClientApplicationConfig(clientId: kClientID, redirectUri: nil, authority: authority)
self.applicationContext = try MSALPublicClientApplication(configuration: msalConfiguration)

// Get tokens.
let parameters = MSALSilentTokenParameters(scopes: kScopes, account: account)
applicationContext.acquireTokenSilent(with: parameters) { (result, error) in
    if let error = error {
        let nsError = error as NSError

        // interactionRequired means you need to ask the user to sign in. This usually happens
        // when the user's refresh token is expired or when the user has changed the password,
        // among other possible reasons.
        if (nsError.domain == MSALErrorDomain) {
            if (nsError.code == MSALError.interactionRequired.rawValue) {    
                DispatchQueue.main.async {
                    guard let applicationContext = self.applicationContext else { return }
                    let parameters = MSALInteractiveTokenParameters(scopes: kScopes)
                    applicationContext.acquireToken(with: parameters) { (result, error) in
                        if let error = error {
                            self.updateLogging(text: "Could not acquire token: \(error)")
                            return
                        }

                        guard let result = result else {
                            self.updateLogging(text: "Could not acquire token: No result returned")
                            return
                        }
                        
                        // Token is ready via interaction!
                        self.accessToken = result.accessToken
                    }
                }
                return
            }
        }

        self.updateLogging(text: "Could not acquire token silently: \(error)")
        return
    }
    guard let result = result else {
        self.updateLogging(text: "Could not acquire token: No result returned")
        return
    }

    // Token is ready via silent acquisition.
    self.accessToken = result.accessToken
}
```

#### <a name="xamarin"></a>Xamarin

```CSharp
string[] scopes = new string["https://graph.microsoft.com/.default"];
var app = PublicClientApplicationBuilder.Create(clientId).Build();
var accounts = await app.GetAccountsAsync();
AuthenticationResult result;
try
{
 result = await app.AcquireTokenSilent(scopes, accounts.FirstOrDefault())
             .ExecuteAsync();
}
catch(MsalUiRequiredException e)
{
 result = await app.AcquireTokenInteractive(scopes)
             .ExecuteAsync();
}
```

### <a name="via-the-protocol"></a>프로토콜을 통해

직접 프로토콜을 사용 하 여 없는 것이 좋습니다. 이렇게 하면 응용 프로그램 일부에서 single sign-on (SSO), 장치 관리 및 조건부 액세스 시나리오를 지원 하지 않습니다.

두 요청을 수행 해야 하는 프로토콜을 사용 하 여 모바일 앱에 대 한 토큰을 가져오려고 하는 경우: 권한 부여 코드를 가져오고 토큰과 교환 합니다.

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
