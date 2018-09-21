---
title: 포함 파일
description: 포함 파일
services: active-directory
documentationcenter: dev-center-name
author: andretms
manager: mtillman
editor: ''
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: ios
ms.workload: identity
ms.date: 09/19/2018
ms.author: andret
ms.custom: include file
ms.openlocfilehash: c6d5fab6ff065dee336c510e3f94583cb0c4960b
ms.sourcegitcommit: 06724c499837ba342c81f4d349ec0ce4f2dfd6d6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/19/2018
ms.locfileid: "46466176"
---
## <a name="use-the-microsoft-authentication-library-msal-to-get-a-token-for-the-microsoft-graph-api"></a>MSAL(Microsoft 인증 라이브러리)를 사용하여 Microsoft Graph API에 대한 토큰 가져오기

`ViewController.swift`를 열고 코드를 다음으로 바꿉니다.

```swift
import UIKit
import MSAL

/// 😃 A View Controller that will respond to the events of the Storyboard.
class ViewController: UIViewController, UITextFieldDelegate, URLSessionDelegate {
    
    // Update the below to your client ID you received in the portal. The below is for running the demo only
    let kClientID = "Your_Application_Id_Here"
    
    // These settings you don't need to edit unless you wish to attempt deeper scenarios with the app.
    let kGraphURI = "https://graph.microsoft.com/v1.0/me/"
    let kScopes: [String] = ["https://graph.microsoft.com/user.read"]
    let kAuthority = "https://login.microsoftonline.com/common"
    
    var accessToken = String()
    var applicationContext : MSALPublicClientApplication?

    @IBOutlet weak var loggingText: UITextView!
    @IBOutlet weak var signoutButton: UIButton!

    /**
        Setup public client application in viewDidLoad
    */

    override func viewDidLoad() {

        super.viewDidLoad()

        do {

            /**
             Initialize a MSALPublicClientApplication with a given clientID and authority
             - clientId:            The clientID of your application, you should get this from the app portal.
             - authority:           A URL indicating a directory that MSAL can use to obtain tokens. In Azure AD
                                    it is of the form https://<instance/<tenant>, where <instance> is the
                                    directory host (e.g. https://login.microsoftonline.com) and <tenant> is a
                                    identifier within the directory itself (e.g. a domain associated to the
                                    tenant, such as contoso.onmicrosoft.com, or the GUID representing the
                                    TenantID property of the directory)
             - error                The error that occurred creating the application object, if any, if you're
                                    not interested in the specific error pass in nil.
             */

            guard let authorityURL = URL(string: kAuthority) else {
                self.loggingText.text = "Unable to create authority URL"
                return
            }

            let authority = try MSALAuthority(url: authorityURL)
            self.applicationContext = try MSALPublicClientApplication(clientId: kClientID, authority: authority)

        } catch let error {
            self.loggingText.text = "Unable to create Application Context \(error)"
        }
    }

    override func viewWillAppear(_ animated: Bool) {

        super.viewWillAppear(animated)
        signoutButton.isEnabled = !self.accessToken.isEmpty
    }
    
    /**
     This button will invoke the authorization flow.
    */

    @IBAction func callGraphButton(_ sender: UIButton) {

        if self.currentAccount() == nil {
            // We check to see if we have a current logged in account.
            // If we don't, then we need to sign someone in.
            self.acquireTokenInteractively()
        } else {
            self.acquireTokenSilently()
        }
    }

    func acquireTokenInteractively() {

        guard let applicationContext = self.applicationContext else { return }

        applicationContext.acquireToken(forScopes: kScopes) { (result, error) in

            if let error = error {

                self.updateLogging(text: "Could not acquire token: \(error)")
                return
            }

            guard let result = result else {

                self.updateLogging(text: "Could not acquire token: No result returned")
                return
            }

            self.accessToken = result.accessToken!
            self.updateLogging(text: "Access token is \(self.accessToken)")
            self.updateSignoutButton(enabled: true)
            self.getContentWithToken()
        }
    }

    func acquireTokenSilently() {

        guard let applicationContext = self.applicationContext else { return }

        /**
         Acquire a token for an existing account silently
         - forScopes:           Permissions you want included in the access token received
                                in the result in the completionBlock. Not all scopes are
                                guaranteed to be included in the access token returned.
         - account:             An account object that we retrieved from the application object before that the
                                authentication flow will be locked down to.
         - completionBlock:     The completion block that will be called when the authentication
                                flow completes, or encounters an error.
         */

        applicationContext.acquireTokenSilent(forScopes: kScopes, account: self.currentAccount()) { (result, error) in

            if let error = error {

                let nsError = error as NSError

                // interactionRequired means we need to ask the user to sign-in. This usually happens
                // when the user's Refresh Token is expired or if the user has changed their password
                // among other possible reasons.
                if (nsError.domain == MSALErrorDomain
                    && nsError.code == MSALErrorCode.interactionRequired.rawValue) {

                    DispatchQueue.main.async {
                        self.acquireTokenInteractively()
                    }

                } else {
                    self.updateLogging(text: "Could not acquire token silently: \(error)")
                }

                return
            }

            guard let result = result else {

                self.updateLogging(text: "Could not acquire token: No result returned")
                return
            }

            self.accessToken = result.accessToken!
            self.updateLogging(text: "Refreshed Access token is \(self.accessToken)")
            self.updateSignoutButton(enabled: true)
            self.getContentWithToken()
        }
    }

    func currentAccount() -> MSALAccount? {

        guard let applicationContext = self.applicationContext else { return nil }

        // We retrieve our current account by getting the first account from cache
        // In multi-account applications, account should be retrieved by home account identifier or username instead
        do {

            let cachedAccounts = try applicationContext.allAccounts()

            if !cachedAccounts.isEmpty {
                return cachedAccounts.first
            }

        } catch let error as NSError {

            self.updateLogging(text: "Didn't find any accounts in cache: \(error)")
        }

        return nil
    }
```

<!--start-collapse-->
### <a name="more-information"></a>추가 정보
#### <a name="getting-a-user-token-interactively"></a>대화형으로 사용자 토큰 가져오기
`acquireToken` 메서드를 호출하면 사용자에게 로그인하라는 브라우저 창이 표시됩니다. 사용자가 처음으로 보호되는 리소스에 액세스해야 하거나 토큰 획득을 위한 자동 작업에 실패한 경우(예: 사용자의 암호 만료) 일반적으로 사용자는 응용 프로그램에서 대화식으로 로그인해야 합니다.

#### <a name="getting-a-user-token-silently"></a>자동으로 사용자 토큰 가져오기
`acquireTokenSilent` 메서드는 사용자 개입 없이 토큰 획득 및 갱신을 자동으로 처리합니다. `acquireToken`이 처음으로 실행된 후 일반적으로 `acquireTokenSilent` 메서드를 사용하여 후속 호출 시 보호되는 리소스에 액세스하는 데 사용되는 토큰을 가져옵니다. 즉, 토큰을 요청하거나 갱신하기 위한 호출이 자동으로 수행됩니다.

결국 `acquireTokenSilent`에 실패합니다(예: 사용자 로그아웃 또는 다른 장치에서 사용자가 암호 변경). MSAL이 대화형 작업을 요구해 이 문제를 해결할 수 있다고 감지하면 `MSALErrorCode.interactionRequired` 예외를 발생합니다. 응용 프로그램에서는 이러한 예외를 다음 두 가지 방법으로 처리할 수 있습니다.

1.  즉시 `acquireToken`에 대한 호출을 수행합니다. 그러면 사용자에게 로그인하라는 메시지가 표시됩니다. 이 패턴은 응용 프로그램에 사용자가 사용할 수 있는 오프라인 콘텐츠가 없는 온라인 응용 프로그램에서 일반적으로 사용됩니다. 이 단계별 설치에 따라 생성된 샘플 응용 프로그램은 이 패턴을 사용합니다. 이 패턴은 응용 프로그램을 처음 실행할 때 작동되는 것을 확인할 수 있습니다. 이 응용 프로그램을 사용한 사용자가 없으므로 `applicationContext.allAccounts().first`에는 null 값이 포함되며 ` MSALErrorCode.interactionRequired ` 예외가 throw됩니다. 샘플의 코드는 `acquireToken`를 호출해 예외를 처리하며, 사용자에게 로그인하라는 메시지가 표시됩니다.

2.  또한 응용 프로그램에서는 대화형 로그인이 필요하다는 시각적 표시를 사용자에게 보여줍니다. 따라서 사용자가 로그인할 적절한 시간을 선택하거나 이후에 응용 프로그램이 `acquireTokenSilent`를 다시 시작할 수 있습니다. 이는 사용자가 중단 없이 응용 프로그램의 기능을 사용할 수 있는 경우(예: 응용 프로그램에 사용 가능한 오프라인 콘텐츠가 있는 경우) 일반적으로 사용됩니다. 이 경우 사용자가 보호되는 리소스에 액세스하거나 오래된 정보를 새로 고치기 위해 로그인할 시점을 결정하거나 응용 프로그램이 일시적으로 사용할 수 없게 된 후 네트워크가 복원된 경우 `acquireTokenSilent`를 다시 시도하도록 결정할 수 있습니다.

<!--end-collapse-->

## <a name="call-the-microsoft-graph-api-using-the-token-you-just-obtained"></a>방금 가져온 토큰을 사용하여 Microsoft Graph API 호출

`ViewController.swift`에 아래의 새 메서드를 추가합니다. 이 메서드는 *HTTP 인증 헤더*를 사용하여 Microsoft Graph API에 대해 `GET` 요청을 수행하는 데 사용됩니다.

```swift
 func getContentWithToken() {

        // Specify the Graph API endpoint
        let url = URL(string: kGraphURI)
        var request = URLRequest(url: url!)
    
        // Set the Authorization header for the request. We use Bearer tokens, so we specify Bearer + the token we got from the result
        request.setValue("Bearer \(self.accessToken)", forHTTPHeaderField: "Authorization")

        URLSession.shared.dataTask(with: request) { data, response, error in

            if let error = error {
                self.updateLogging(text: "Couldn't get graph result: \(error)")
                return
            }

            guard let result = try? JSONSerialization.jsonObject(with: data!, options: []) else {

                self.updateLogging(text: "Couldn't deserialize result JSON")
                return
            }

            self.updateLogging(text: "Result from Graph: \(result))")

        }.resume()
    }
```

<!--start-collapse-->
### <a name="making-a-rest-call-against-a-protected-api"></a>보호되는 API에 대해 REST 호출 수행

이 샘플 응용 프로그램에서 `getContentWithToken()` 메서드는 토큰이 필요한 보호되는 리소스에 대한 HTTP `GET` 요청을 실행한 다음 호출자에게 콘텐츠를 반환하는 데 사용됩니다. 이 메서드는 *HTTP 인증 헤더*에 획득된 토큰을 추가합니다. 이 샘플에서 리소스는 사용자 프로필 정보를 표시하는 Microsoft Graph API *me* 엔드포인트입니다.
<!--end-collapse-->

## <a name="set-up-sign-out"></a>로그아웃 설정

`ViewController.swift`에 다음 메서드를 추가하면 사용자가 로그아웃됩니다.

```swift 
 @IBAction func signoutButton(_ sender: UIButton) {

        guard let applicationContext = self.applicationContext else { return }

        guard let account = self.currentAccount() else { return }

        do {

            /**
             Removes all tokens from the cache for this application for the provided account
             - account:    The account to remove from the cache
             */

            try applicationContext.remove(account)
            self.loggingText.text = ""
            self.signoutButton.isEnabled = false

        } catch let error as NSError {

            self.updateLogging(text: "Received error signing account out: \(error)")
        }
    }

}
```
<!--start-collapse-->
### <a name="more-info-on-sign-out"></a>로그아웃에 대한 자세한 정보

`signoutButton` 메서드는 MSAL 사용자 캐시에서 사용자를 제거하여 MSAL에 현재 사용자를 잊으라고 효율적으로 전달합니다. 따라서 대화식으로 수행되는 경우에만 토큰 획득을 위한 이후 요청에 성공하게 됩니다.

이 샘플의 응용 프로그램이 단일 사용자를 지원하더라도 MSAL은 동시에 여러 계정에 로그인할 수 있는 시나리오를 지원합니다(예: 사용자 한 명이 여러 계정을 가질 수 있는 메일 응용 프로그램).
<!--end-collapse-->

## <a name="register-the-callback"></a>콜백 등록

사용자가 인증되면 브라우저는 사용자를 응용 프로그램으로 다시 리디렉션합니다. 아래 단계에 따라 이 콜백을 등록합니다.

1.  `AppDelegate.swift`를 열고 MSAL을 가져옵니다.

```swift
import MSAL
```
<!-- Workaround for Docs conversion bug -->
<ol start="2">
<li>
<code>AppDelegate</code> 클래스에 다음 메서드를 추가하여 콜백을 처리합니다.
</li>
</ol>

```swift
// @brief Handles inbound URLs. Checks if the URL matches the redirect URI for a pending AppAuth
// authorization request and if so, will look for the code in the response.

func application(_ application: UIApplication, open url: URL, sourceApplication: String?, annotation: Any) -> Bool {
    
    print("Received callback!")
    
    MSALPublicClientApplication.handleMSALResponse(url)
    
    return true
}
```
