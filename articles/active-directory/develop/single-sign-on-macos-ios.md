---
title: macOS 및 iOS에서 SSO 구성
titleSuffix: Microsoft identity platform
description: macOS 및 iOS에서 단일 사인온(SSO)을 구성하는 방법을 알아봅니다.
services: active-directory
documentationcenter: dev-center-name
author: mmacy
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/03/2020
ms.author: marsma
ms.reviewer: ''
ms.custom: aaddev
ms.openlocfilehash: 91a55520b37c549c8f1d94ba6cf08ecd24db85b5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79262452"
---
# <a name="how-to-configure-sso-on-macos-and-ios"></a>방법: macOS 및 iOS에서 SSO 구성

macOS 및 iOS용 마이크로소프트 인증 라이브러리(MSAL)는 macOS/iOS 앱과 브라우저 간에 단일 사인온(SSO)을 지원합니다. 이 문서에서는 다음 SSO 시나리오를 다룹니다.

- [여러 앱 간에 자동 SSO](#silent-sso-between-apps)

이러한 유형의 SSO는 동일한 Apple 개발자가 배포한 여러 앱 간에 작동합니다. 키체인에서 다른 앱에서 작성한 새로 고침 토큰을 읽고 액세스 토큰으로 자동으로 교환하여 자동 SSO(즉, 사용자에게 자격 증명하라는 메시지가 표시되지 않습니다)를 제공합니다.  

- [인증 브로커를 통한 SSO](#sso-through-authentication-broker-on-ios)

> [!IMPORTANT]
> macOS에서 이 흐름을 사용할 수 없습니다.

Microsoft는 모바일 장치가 Azure Active Directory(AAD)에 등록된 경우 다른 공급업체의 응용 프로그램 간에 SSO를 사용하도록 설정하는 브로커라는 앱을 제공합니다. 이러한 유형의 SSO는 사용자의 장치에 브로커 응용 프로그램을 설치해야 합니다.

- **MSAL과 사파리 사이의 SSO**

SSO는 [ASWebAuthenticationSession](https://developer.apple.com/documentation/authenticationservices/aswebauthenticationsession?language=objc) 클래스를 통해 달성됩니다. 다른 앱과 Safari 브라우저의 기존 로그인 상태를 사용합니다. 동일한 Apple 개발자가 배포한 앱에 국한되지 않지만 일부 사용자 상호 작용이 필요합니다.

앱의 기본 웹 보기를 사용하여 사용자를 로그인하는 경우 MSAL 기반 응용 프로그램과 Safari 간에 자동 SSO가 표시됩니다. MSAL이 지원하는 웹 보기에 대해 자세히 알아보려면 [브라우저 사용자 지정 및 WebView 를](customize-webviews.md)방문하십시오.

> [!IMPORTANT]
> 이 유형의 SSO는 현재 macOS에서 사용할 수 없습니다. macOS의 MSAL은 사파리에서 SSO가 지원하지 않는 WKWebView만 지원합니다. 

- **ADAL과 MSAL 맥OS / iOS 애플 리케이션 사이의 침묵 SSO**

MSAL Objective-C는 ADAL Objective-C 기반 앱으로 마이그레이션 및 SSO를 지원합니다. 앱은 동일한 Apple 개발자가 배포해야 합니다.

ADAL과 MSAL 기반 앱 간의 교차 앱 SSO에 대한 지침은 [macOS 및 iOS에서 ADAL](sso-between-adal-msal-apps-macos-ios.md) 및 MSAL 앱 간의 SSO를 참조하십시오.

## <a name="silent-sso-between-apps"></a>앱 간 자동 SSO

MSAL은 iOS 키체인 액세스 그룹을 통해 SSO 공유를 지원합니다.

응용 프로그램 전체에서 SSO를 활성화하려면 아래에 자세히 설명된 다음 단계를 수행해야 합니다.

1. 모든 응용 프로그램이 동일한 클라이언트 ID 또는 응용 프로그램 ID를 사용하는지 확인합니다.
1. 모든 애플리케이션이 키 집합을 공유할 수 있도록 Apple에서 동일한 서명 인증서를 공유하는지 확인합니다.
1. 각 애플리케이션에 대한 동일한 키 집합 권한 부여를 요청합니다.
1. MSAL SDK에 기본 키와 다른 경우 사용할 공유 키 체인에 대해 알려주십시오.

### <a name="use-the-same-client-id-and-application-id"></a>동일한 클라이언트 ID 및 응용 프로그램 ID 사용

Microsoft ID 플랫폼에서 토큰을 공유할 수 있는 응용 프로그램을 알려면 해당 응용 프로그램이 동일한 클라이언트 ID 또는 응용 프로그램 ID를 공유해야 합니다. 포털에 첫 번째 애플리케이션을 등록했던 경우에 제공된 고유 식별자입니다.

Microsoft ID 플랫폼이 동일한 응용 프로그램 ID를 따로 사용하는 앱을 알려주는 방법은 **리디렉션 URI를**사용하는 것입니다. 각 애플리케이션에는 등록 포털에 등록한 여러 개의 리디렉션 URI가 있을 수 있습니다. 제품의 각 앱은 다른 리디렉션 URI를 갖습니다. 예를 들어:

App1 리디렉션 URI: `msauth.com.contoso.mytestapp1://auth`  
App2 리디렉션 URI: `msauth.com.contoso.mytestapp2://auth`  
App3 리디렉션 URI: `msauth.com.contoso.mytestapp3://auth`  

> [!IMPORTANT]
> 리디렉션 uri의 형식은 MSAL 리디렉션 URI 형식 요구 사항에 설명되어 있는 [MSAL](redirect-uris-ios.md#msal-redirect-uri-format-requirements)지원 형식과 호환되어야 합니다.

### <a name="setup-keychain-sharing-between-applications"></a>응용 프로그램 간의 키 체인 공유 설정

키체인 공유를 활성화하려면 Apple의 [기능 추가](https://developer.apple.com/library/ios/documentation/IDEs/Conceptual/AppDistributionGuide/AddingCapabilities/AddingCapabilities.html) 문서를 참조하십시오. 중요한 것은 키체인을 호출할 것을 결정하고 SSO에 관련된 모든 응용 프로그램에 해당 기능을 추가하는 것입니다.

사용 권한이 올바르게 설정되면 프로젝트 디렉터리에 `entitlements.plist` 다음과 같은 내용이 포함된 파일이 표시됩니다.

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
<dict>
    <key>keychain-access-groups</key>
    <array>
        <string>$(AppIdentifierPrefix)com.myapp.mytestapp</string>
        <string>$(AppIdentifierPrefix)com.myapp.mycache</string>
    </array>
</dict>
</plist>
```

#### <a name="add-a-new-keychain-group"></a>새 키체인 그룹 추가

프로젝트 **기능에**새 키체인 그룹을 추가합니다. 키체인 그룹은 다음과 같은 여야 합니다.
* `com.microsoft.adalcache`iOS에서 
* `com.microsoft.identity.universalstorage`macOS에서 볼 수 있습니다.

![키 체인 예제](media/single-sign-on-macos-ios/keychain-example.png)

자세한 내용은 [키체인 그룹을](howto-v2-keychain-objc.md)참조하십시오.

## <a name="configure-the-application-object"></a>응용 프로그램 개체 구성

각 응용 프로그램에서 키체인 사용 권한을 사용하도록 설정하고 SSO를 사용할 준비가 되면 `MSALPublicClientApplication` 다음 예제와 같이 키체인 액세스 그룹으로 구성하십시오.

Objective-C:

```objc
NSError *error = nil;
MSALPublicClientApplicationConfig *configuration = [[MSALPublicClientApplicationConfig alloc] initWithClientId:@"<my-client-id>"];
configuration.cacheConfig.keychainSharingGroup = @"my.keychain.group";
    
MSALPublicClientApplication *application = [[MSALPublicClientApplication alloc] initWithConfiguration:configuration error:&error];
```

Swift:

```swift
let config = MSALPublicClientApplicationConfig(clientId: "<my-client-id>")
config.cacheConfig.keychainSharingGroup = "my.keychain.group"

do {
   let application = try MSALPublicClientApplication(configuration: config)
  // continue on with application
} catch let error as NSError {
  // handle error here
}
```

> [!WARNING]
> 응용 프로그램 간에 키 체인을 공유하면 모든 응용 프로그램이 사용자 또는 응용 프로그램 전체의 모든 토큰을 삭제할 수 있습니다.
> 이는 백그라운드 작업을 수행하는 토큰에 의존하는 응용 프로그램이 있는 경우 특히 영향을 미칩니다.
> 키체인을 공유한다는 것은 앱에서 Microsoft ID SDK 제거 작업을 사용할 때 매우 주의해야 함을 의미합니다.

이것으로 끝입니다. 이제 Microsoft ID SDK는 모든 응용 프로그램에서 자격 증명을 공유합니다. 계정 목록은 응용 프로그램 인스턴스 간에도 공유됩니다.

## <a name="sso-through-authentication-broker-on-ios"></a>iOS에서 인증 브로커를 통한 SSO

MSAL은 Microsoft 인증기에서 중개된 인증에 대한 지원을 제공합니다. Microsoft 인증자는 AAD 등록 장치에 대한 SSO를 제공하며 응용 프로그램이 조건부 액세스 정책을 따르는 데도 도움이 됩니다.

다음 단계는 앱에 대한 인증 브로커를 사용하여 SSO를 사용하도록 설정하는 방법입니다.

1. 앱의 Info.plist에서 응용 프로그램에 대한 브로커 호환 리디렉션 URI 형식을 등록합니다. 브로커 호환 리디렉션 URI `msauth.<app.bundle.id>://auth`형식은 . '<app.bundle.id>'를 응용 프로그램의 번들 ID로 바꿉습니다. 예를 들어:

    ```xml
    <key>CFBundleURLSchemes</key>
    <array>
        <string>msauth.<app.bundle.id></string>
    </array>
    ```

1. `LSApplicationQueriesSchemes`다음 구성표를 앱의 Info.plist에 추가합니다.

    ```xml
    <key>LSApplicationQueriesSchemes</key>
    <array>
         <string>msauthv2</string>
         <string>msauthv3</string>
    </array>
    ```

1. 콜백을 처리하려면 `AppDelegate.m` 파일에 다음을 추가합니다.

    Objective-C:
    
    ```objc
    - (BOOL)application:(UIApplication *)app openURL:(NSURL *)url options:(NSDictionary<NSString *,id> *)options
    {
        return [MSALPublicClientApplication handleMSALResponse:url sourceApplication:options[UIApplicationOpenURLOptionsSourceApplicationKey]];
    }
    ```
    
    Swift:
    
    ```swift
    func application(_ app: UIApplication, open url: URL, options: [UIApplication.OpenURLOptionsKey : Any] = [:]) -> Bool {
        return MSALPublicClientApplication.handleMSALResponse(url, sourceApplication: options[UIApplication.OpenURLOptionsKey.sourceApplication] as? String)
    }
    ```
    
**Xcode 11을 사용하는 경우**대신 MSAL 콜백을 `SceneDelegate` 파일에 배치해야 합니다.
이전 iOS와의 호환성을 위해 UISceneDelegate 및 UIApplicationDelegate를 모두 지원하는 경우 MSAL 콜백을 두 파일에 배치해야 합니다.

Objective-C:

```objc
 - (void)scene:(UIScene *)scene openURLContexts:(NSSet<UIOpenURLContext *> *)URLContexts
 {
     UIOpenURLContext *context = URLContexts.anyObject;
     NSURL *url = context.URL;
     NSString *sourceApplication = context.options.sourceApplication;
     
     [MSALPublicClientApplication handleMSALResponse:url sourceApplication:sourceApplication];
 }
```

Swift:

```swift
func scene(_ scene: UIScene, openURLContexts URLContexts: Set<UIOpenURLContext>) {
        
        guard let urlContext = URLContexts.first else {
            return
        }
        
        let url = urlContext.url
        let sourceApp = urlContext.options.sourceApplication
        
        MSALPublicClientApplication.handleMSALResponse(url, sourceApplication: sourceApp)
    }
```

## <a name="next-steps"></a>다음 단계

[인증 흐름 및 애플리케이션 시나리오](authentication-flows-app-scenarios.md)에 대해 알아보기