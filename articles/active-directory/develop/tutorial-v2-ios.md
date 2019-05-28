---
title: iOS 시작 - Microsoft ID 플랫폼 | Azure
description: Microsoft ID 플랫폼을 사용하는 액세스 토큰이 필요한 API를 iOS(Swift) 애플리케이션이 호출하는 방법
services: active-directory
documentationcenter: dev-center-name
author: danieldobalian
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/26/2019
ms.author: jmprieur
ms.reviwer: brandwe
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 780da9359aaf645abc9b685fa9d90bbea9199759
ms.sourcegitcommit: e9a46b4d22113655181a3e219d16397367e8492d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/21/2019
ms.locfileid: "65962170"
---
# <a name="sign-in-users-and-call-the-microsoft-graph-from-an-ios-app"></a>iOS 앱에서 사용자를 로그인하고 Microsoft Graph 호출

이 자습서에서는 iOS 애플리케이션을 빌드하고 Microsoft ID 플랫폼에 통합하는 방법을 알아봅니다. 특히 이 앱은 사용자로 로그인하고 Microsoft Graph API를 호출하는 액세스 토큰을 가져오며 Microsoft Graph API에 대한 기본 요청을 만듭니다.  

이 가이드를 완료했으면 애플리케이션에서 Azure Active Directory를 사용하는 모든 회사 또는 조직의 회사 또는 학교 계정뿐만 아니라 개인 Microsoft 계정(outlook.com, live.com 등)의 로그인을 수락하게 됩니다.

## <a name="how-this-guide-works"></a>이 가이드의 작동 방식

![이 자습서에서 생성된 샘플 앱의 작동 방식 표시](../../../includes/media/active-directory-develop-guidedsetup-ios-introduction/iosintro.svg)

이 샘플의 앱에서는 사용자로 로그인하고 사용자를 대신하여 데이터를 가져옵니다.  이 데이터는 권한 부여가 필요하고 Microsoft ID 플랫폼으로 보호되는 API(여기서는 Microsoft Graph API)를 통해 액세스됩니다.

더 구체적으로 살펴보면 다음과 같습니다.

* 앱이 브라우저 또는 Microsoft Authenticator를 통해 사용자를 로그인합니다.
* 최종 사용자는 애플리케이션에서 요청한 권한을 수락합니다. 
* 앱에서 Microsoft Graph API에 대한 액세스 토큰을 발급합니다.
* 액세스 토큰은 웹 API에 대한 HTTP 요청에 포함됩니다.
* Microsoft Graph 응답을 처리합니다.

이 샘플에서는 MSAL(Microsoft 인증 라이브러리)을 사용하여 인증을 구현합니다. MSAL은 자동으로 토큰을 갱신하고, 디바이스의 다른 앱 간에 SSO를 제공하고, 계정을 관리합니다.

## <a name="prerequisites"></a>필수 조건

- 이 가이드에서 만드는 샘플에는 XCode 버전 10.x가 필요합니다. [iTunes 웹 사이트](https://geo.itunes.apple.com/us/app/xcode/id497799835?mt=12 "XCode 다운로드 URL")에서 XCode를 다운로드할 수 있습니다.
- Microsoft 인증 라이브러리([MSAL.framework](https://github.com/AzureAD/microsoft-authentication-library-for-objc)). 종속성 관리자를 사용해도 되고 수동으로 해도 됩니다. 아래에 [자세한 정보](#add-msal)를 설명하는 섹션이 있습니다. 

## <a name="set-up-your-project"></a>프로젝트 설정

이 자습서에서는 새 프로젝트를 만듭니다. 새 프로젝트를 만드는 대신 완료된 자습서를 다운로드하려면 [코드를 다운로드](https://github.com/Azure-Samples/active-directory-ios-swift-native-v2/archive/master.zip)합니다.

### <a name="create-a-new-project"></a>새 프로젝트 만들기

1. Xcode를 열고 **Create a new Xcode project**를 선택합니다.
2. **iOS > Single view Application**을 선택하고 **Next**를 선택합니다.
3. 제품 이름을 입력하고 **Next**를 선택합니다.
4. 앱을 만들 폴더를 선택하고 *만들기*를 클릭합니다.

## <a name="register-your-application"></a>애플리케이션 등록 

다음 두 섹션에 설명된 대로 두 가지 방법 중 하나로 애플리케이션을 등록할 수 있습니다.

### <a name="register-your-app"></a>앱 등록

1. [Azure Portal](https://aka.ms/MobileAppReg)로 이동하여 `New registration`을 선택합니다. 
2. 앱 **이름**을 입력하고 `Register`로 이동합니다. **이 단계에서는 리디렉션 URI를 설정하지 마세요**. 
3. `Manage` 섹션에서 `Authentication` > `Add a platform` > `iOS`로 이동합니다.
    - 프로젝트의 번들 ID를 입력합니다. 코드를 다운로드한 경우 `com.microsoft.identitysample.MSALiOS`입니다.
4. 나중에 사용할 수 있도록 `Configure`를 누르고 `MSAL Configuration`을 저장합니다. 

## <a name="add-msal"></a>MSAL 추가

### <a name="get-msal"></a>MSAL 가져오기

#### <a name="cocoapods"></a>CocoaPods

다음과 같이 [CocoaPods](https://cocoapods.org/)를 사용하여 대상 아래의 `Podfile`에 `MSAL`을 추가하는 방법으로 설치할 수 있습니다.

```
use_frameworks!

target '<your-target-here>' do
   pod 'MSAL', '~> 0.4.0'
end
```

#### <a name="carthage"></a>Carthage

다음과 같이 [Carthage](https://github.com/Carthage/Carthage)를 사용하여 `Cartfile`에 `MSAL`을 추가하는 방법으로 설치할 수 있습니다. 

```
github "AzureAD/microsoft-authentication-library-for-objc" "master"
```

#### <a name="manually"></a>수동

Git 하위 모듈을 사용하거나 최신 릴리스를 확인하고 애플리케이션에 프레임워크로 사용할 수도 있습니다.

### <a name="add-your-app-registration"></a>앱 등록 추가

다음으로, 코드에 앱 등록을 추가합니다. 다음과 같이 ***클라이언트/애플리케이션 ID***를 `ViewController.swift`에 추가합니다.

```swift
let kClientID = "Your_Application_Id_Here"

// Additional variables for Auth and Graph API
let kGraphURI = "https://graph.microsoft.com/v1.0/me/"
let kScopes: [String] = ["https://graph.microsoft.com/user.read"]
let kAuthority = "https://login.microsoftonline.com/common"
var accessToken = String()
var applicationContext : MSALPublicClientApplication?
```

### <a name="configure-url-schemes"></a>URL 스키마 구성

로그인 후 사용자를 다시 앱으로 리디렉션할 수 있도록 `CFBundleURLSchemes`를 등록하여 콜백을 허용합니다.

`LSApplicationQueriesSchemes`를 사용하면 앱에서 Microsoft Authenticator를 사용할 수 있습니다(사용 가능한 경우). 

이렇게 하려면 `Info.plist`를 소스 코드로 열고 다음 코드를 추가하되, ***BUNDLE_ID***를 Azure Portal에서 구성한 내용으로 바꿉니다.

```xml
<key>CFBundleURLTypes</key>
<array>
    <dict>
        <key>CFBundleURLSchemes</key>
        <array>
            <string>msauth.[BUNDLE_ID]</string>
        </array>
    </dict>
</array>
<key>LSApplicationQueriesSchemes</key>
<array>
    <string>msauth</string>
    <string>msauthv2</string>
</array>
```

### <a name="import-msal"></a>MSAL 가져오기

다음과 같이 `ViewController.swift` 및 `AppDelegate.swift` 파일에서 MSAL 프레임워크를 가져옵니다.

```swift
import MSAL
```

## <a name="create-your-apps-ui"></a>앱 UI 만들기

이 자습서에서는 다음 항목을 만들어야 합니다.

- Graph API 호출 단추
- 로그아웃 단추
- textview 로깅

`ViewController.swift`에서 다음과 같이 속성 및 `initUI()`를 정의합니다.

```swift

var loggingText: UITextView!
var signOutButton: UIButton!
var callGraphButton: UIButton!

func initUI() {
        // Add call Graph button
        callGraphButton  = UIButton()
        callGraphButton.translatesAutoresizingMaskIntoConstraints = false
        callGraphButton.setTitle("Call Microsoft Graph API", for: .normal)
        callGraphButton.setTitleColor(.blue, for: .normal)
        callGraphButton.addTarget(self, action: #selector(callGraphAPI(_:)), for: .touchUpInside)
        self.view.addSubview(callGraphButton)
        
        callGraphButton.centerXAnchor.constraint(equalTo: view.centerXAnchor).isActive = true
        callGraphButton.topAnchor.constraint(equalTo: view.topAnchor, constant: 50.0).isActive = true
        callGraphButton.widthAnchor.constraint(equalToConstant: 300.0).isActive = true
        callGraphButton.heightAnchor.constraint(equalToConstant: 50.0).isActive = true
        
        // Add sign out button
        signOutButton = UIButton()
        signOutButton.translatesAutoresizingMaskIntoConstraints = false
        signOutButton.setTitle("Sign Out", for: .normal)
        signOutButton.setTitleColor(.blue, for: .normal)
        signOutButton.setTitleColor(.gray, for: .disabled)
        signOutButton.addTarget(self, action: #selector(signOut(_:)), for: .touchUpInside)
        self.view.addSubview(signOutButton)
        
        signOutButton.centerXAnchor.constraint(equalTo: view.centerXAnchor).isActive = true
        signOutButton.topAnchor.constraint(equalTo: callGraphButton.bottomAnchor, constant: 10.0).isActive = true
        signOutButton.widthAnchor.constraint(equalToConstant: 150.0).isActive = true
        signOutButton.heightAnchor.constraint(equalToConstant: 50.0).isActive = true
        signOutButton.isEnabled = false
        
        // Add logging textfield
        loggingText = UITextView()
        loggingText.isUserInteractionEnabled = false
        loggingText.translatesAutoresizingMaskIntoConstraints = false
        
        self.view.addSubview(loggingText)
        
        loggingText.topAnchor.constraint(equalTo: signOutButton.bottomAnchor, constant: 10.0).isActive = true
        loggingText.leftAnchor.constraint(equalTo: self.view.leftAnchor, constant: 10.0).isActive = true
        loggingText.rightAnchor.constraint(equalTo: self.view.rightAnchor, constant: 10.0).isActive = true
        loggingText.bottomAnchor.constraint(equalTo: self.view.bottomAnchor, constant: 10.0).isActive = true
    }
```

그리고 다음과 같이 ViewController.swift의 `viewDidLoad()`에 추가합니다.

```swift
    override func viewDidLoad() {
        super.viewDidLoad()
        initUI()
        do {
            try self.initMSAL()
        } catch let error {
            self.loggingText.text = "Unable to create Application Context \(error)"
        }
    }
```

## <a name="use-msal"></a>MSAL 사용

### <a name="initialize-msal"></a>MSAL 초기화

먼저, 애플리케이션의 `MSALPublicClientConfiguration` 인스턴스를 사용하여 `MSALPublicClientApplication`을 만들어야 합니다.

```swift
    func initMSAL() throws {
        
        guard let authorityURL = URL(string: kAuthority) else {
            self.loggingText.text = "Unable to create authority URL"
            return
        }
        
        let authority = try MSALAADAuthority(url: authorityURL)
        
        let msalConfiguration = MSALPublicClientApplicationConfig(clientId: kClientID, redirectUri: nil, authority: authority)
        self.applicationContext = try MSALPublicClientApplication(configuration: msalConfiguration)
    }
```

### <a name="handle-the-callback"></a>콜백 처리

로그인 후 콜백을 처리하려면 다음과 같이 `appDelegate`에 `MSALPublicClientApplication.handleMSALResponse`를 추가합니다.

```swift
    func application(_ app: UIApplication, open url: URL, options: [UIApplication.OpenURLOptionsKey : Any] = [:]) -> Bool {
        
        guard let sourceApplication = options[UIApplication.OpenURLOptionsKey.sourceApplication] as? String else {
            return false
        }
        
        return MSALPublicClientApplication.handleMSALResponse(url, sourceApplication: sourceApplication)
    }
```

#### <a name="acquire-tokens"></a>토큰 획득

이제 MSAL을 통해 대화형으로 애플리케이션의 UI 처리 논리를 구현하고 토큰을 가져올 수 있습니다. 

MSAL은 토큰을 가져오기 위한 두 가지 기본 메서드 `acquireTokenSilently` 및 `acquireTokenInteractively`을 공개합니다.  

`acquireTokenSilently()`는 계정이 있는 경우 사용자 개입 없이 사용자를 로그인하고 토큰을 가져오려고 시도합니다.

`acquireTokenInteractively`은 사용자를 로그인하고 토큰을 가져오려고 시도할 때 항상 UI를 표시합니다. 하지만 브라우저의 세션 쿠키 또는 Microsoft Authenticator의 계정을 사용하여 대화형 SSO 환경을 제공할 수도 있습니다. 

```swift
    @objc func callGraphAPI(_ sender: UIButton) {
        
        guard let currentAccount = self.currentAccount() else {
            // We check to see if we have a current logged in account.
            // If we don't, then we need to sign someone in.
            acquireTokenInteractively()
            return
        }
        
        acquireTokenSilently(currentAccount)
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

#### <a name="get-a-token-interactively"></a>대화형으로 토큰 가져오기

처음으로 토큰을 획득하려면 `MSALInteractiveTokenParameters`를 만들고 `acquireToken`을 호출해야 합니다.

1. 범위를 사용하여 `MSALInteractiveTokenParameters`를 만듭니다.
2. 만든 매개 변수를 사용하여 `acquireToken`을 호출합니다.
3. 오류를 적절하게 처리합니다. 자세한 내용은 [iOS 오류 처리 가이드](https://github.com/AzureAD/microsoft-authentication-library-for-objc/wiki/Error-Handling)를 참조하세요.
4. 성공 사례를 처리합니다. 

```swift
    func acquireTokenInteractively() {
   
        guard let applicationContext = self.applicationContext else { return }
     // #1    
        let parameters = MSALInteractiveTokenParameters(scopes: kScopes)
     // #2        
        applicationContext.acquireToken(with: parameters) { (result, error) in
     // #3            
            if let error = error {
                self.updateLogging(text: "Could not acquire token: \(error)")
                return
            }
            guard let result = result else {   
                self.updateLogging(text: "Could not acquire token: No result returned")
                return
            }
     // #4            
            self.accessToken = result.accessToken
            self.updateLogging(text: "Access token is \(self.accessToken)")
            self.updateSignOutButton(enabled: true)
            self.getContentWithToken()
        }
    }
```



#### <a name="get-a-token-silently"></a>자동으로 토큰 가져오기

업데이트된 토큰을 자동으로 획득하려면 `MSALSilentTokenParameters` 및 `acquireTokenSilent`를 만들어야 합니다.

```swift
    
    func acquireTokenSilently(_ account : MSALAccount!) {
        guard let applicationContext = self.applicationContext else { return }
        let parameters = MSALSilentTokenParameters(scopes: kScopes, account: account)
        
        applicationContext.acquireTokenSilent(with: parameters) { (result, error) in    
            if let error = error {
                let nsError = error as NSError
                if (nsError.domain == MSALErrorDomain) {
                    if (nsError.code == MSALError.interactionRequired.rawValue) {
                        DispatchQueue.main.async {
                            self.acquireTokenInteractively()
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
            
            self.accessToken = result.accessToken
            self.updateLogging(text: "Refreshed Access token is \(self.accessToken)")
            self.updateSignOutButton(enabled: true)
            self.getContentWithToken()
        }
    }
```

### <a name="call-the-microsoft-graph-api"></a>Microsoft Graph API 호출 

`self.accessToken`을 통해 토큰을 획득한 후에는 앱에서 다음과 같이 이 값을 HTTP 헤더에 사용하여 Microsoft Graph에 대한 권한 있는 요청을 만들 수 있습니다.

| 헤더 키    | 값                 |
| ------------- | --------------------- |
| 권한 부여 | 전달자 <access-token> |

다음 줄을 `ViewController.swift`에 추가합니다.

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

[Microsoft Graph API](https://graph.microsoft.com)에 대한 자세한 정보

### <a name="use-msal-for-sign-out"></a>로그아웃에 MSAL 사용

다음으로, 앱에 로그아웃 지원을 추가하겠습니다. 

MSAL을 사용하여 로그아웃하면 사용자에 대해 알려진 모든 정보가 이 애플리케이션에서 제거되지만, 사용자의 디바이스에서는 여전히 활성 세션이 유지됩니다. 사용자가 다시 로그인하려고 시도할 때 자격 증명을 입력하라는 상호 작용이 표시될 수도 있지만, 디바이스 세션이 활성 상태이므로 다시 입력할 필요가 없습니다. 

로그아웃을 추가하려면 다음 메서드를 `ViewController.swift`에 복사합니다.

```swift 
    @objc func signOut(_ sender: UIButton) {
        
        guard let applicationContext = self.applicationContext else { return }
        
        guard let account = self.currentAccount() else { return }
        
        do {
            
            /**
             Removes all tokens from the cache for this application for the provided account
             
             - account:    The account to remove from the cache
             */
            
            try applicationContext.remove(account)
            self.loggingText.text = ""
            self.signOutButton.isEnabled = false
            
        } catch let error as NSError {
            
            self.updateLogging(text: "Received error signing account out: \(error)")
        }
    }
```

### <a name="enable-token-caching"></a>토큰 캐싱 사용

기본적으로 MSAL은 앱의 토큰을 iOS 키 집합에 캐시합니다. 

토큰 캐싱을 사용하려면 Xcode 프로젝트 설정 > `Capabilities tab` > `Enable Keychain Sharing` > `Plus` 클릭 > **com.microsoft.adalcache**를 입력합니다.

### <a name="add-helper-methods"></a>도우미 메서드 추가

샘플을 완료하려면 다음과 같은 도우미 메서드를 추가합니다.

``` swift
    
    func updateLogging(text : String) {
        
        if Thread.isMainThread {
            self.loggingText.text = text
        } else {
            DispatchQueue.main.async {
                self.loggingText.text = text
            }
        }
    }
    
    func updateSignOutButton(enabled : Bool) {
        if Thread.isMainThread {
            self.signOutButton.isEnabled = enabled
        } else {
            DispatchQueue.main.async {
                self.signOutButton.isEnabled = enabled
            }
        }
    }
```

### <a name="multi-account-applications"></a>다중 계정 애플리케이션

이 앱은 단일 계정 시나리오를 위해 빌드됩니다. MSAL은 다중 계정 시나리오도 지원하지만, 그러려면 앱에서 몇 가지 추가 작업을 수행해야 합니다. 사용자가 토큰이 필요한 각 작업에 어떤 계정을 사용할 것인지 선택할 수 있는 UI를 만들어야 합니다. 또는 앱에서 `getAllAccounts(...)` 메서드를 통해 어떤 계정을 사용할 것인지 선택하는 경험적 접근을 구현할 수 있습니다.

## <a name="test-your-app"></a>앱 테스트

### <a name="run-locally"></a>로컬 실행

위의 코드를 따라 했으면 앱을 빌드하고 배포하여 디바이스 또는 에뮬레이터를 테스트합니다. 로그인하여 Azure AD 계정 또는 Microsoft 개인 계정의 토큰을 가져올 수 있어야 합니다. 사용자가 로그인하면 이 앱은 Microsoft Graph `/me` 엔드포인트에서 반환된 데이터를 표시합니다. 

이슈가 있는 경우 이 문서나 MSAL 라이브러리에서 이슈를 제기하여 알려주세요. 

### <a name="consent-to-your-app"></a>앱 사용 권한 동의

사용자가 앱에 처음으로 로그인하면 Microsoft ID는 사용자에게 요청된 권한에 동의할 것을 요구합니다.  대부분의 사용자가 동의할 수 있지만, 일부 Azure AD 테넌트에서는 사용자 동의를 해제했습니다. 이 경우 관리자가 사용자 대신 동의해야 합니다.  이 시나리오를 지원하려면 Azure Portal에서 앱의 범위를 등록해야 합니다.

## <a name="help-and-support"></a>도움말 및 지원

이 자습서 또는 Microsoft ID 플랫폼과 관련하여 문제가 있나요? [도움말 및 지원](https://docs.microsoft.com/azure/active-directory/develop/developer-support-help-options)을 참조하세요.

