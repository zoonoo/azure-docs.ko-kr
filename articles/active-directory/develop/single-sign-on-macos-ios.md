---
title: MacOS 및 iOS에서 SSO 구성
titleSuffix: Microsoft identity platform
description: MacOS 및 iOS에서 SSO (single sign on)를 구성 하는 방법에 대해 알아봅니다.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 02/03/2020
ms.author: marsma
ms.reviewer: ''
ms.custom: aaddev
ms.openlocfilehash: 25389348476552298ddb947ccb59acb8b3d5bc57
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "80881251"
---
# <a name="how-to-configure-sso-on-macos-and-ios"></a>방법: macOS 및 iOS에서 SSO 구성

MacOS 및 iOS 용 MSAL (Microsoft 인증 라이브러리)은 macOS/iOS 앱과 브라우저 간에 SSO (Single Sign-on)를 지원 합니다. 이 문서에서는 다음 SSO 시나리오에 대해 설명 합니다.

- [여러 앱 간의 자동 SSO](#silent-sso-between-apps)

이러한 유형의 SSO는 동일한 Apple Developer에서 배포 하는 여러 앱 간에 작동 합니다. 키 집합에서 다른 앱이 작성 한 새로 고침 토큰을 읽고 액세스 토큰에 대해 자동으로 교환 하 여 자동 SSO (즉, 사용자에 게 자격 증명을 입력 하 라는 메시지가 표시 되지 않음)를 제공 합니다.  

- [Authentication broker를 통한 SSO](#sso-through-authentication-broker-on-ios)

> [!IMPORTANT]
> 이 흐름은 macOS에서 사용할 수 없습니다.

Microsoft는 모바일 장치가 Azure Active Directory (AAD)에 등록 되어 있는 한 다른 공급 업체의 응용 프로그램 간에 SSO를 사용 하도록 설정 하는 broker 라는 앱을 제공 합니다. 이 유형의 SSO를 사용 하려면 사용자의 장치에 broker 응용 프로그램을 설치 해야 합니다.

- **MSAL과 Safari 간 SSO**

SSO는 [Aswebauthenticationsession](https://developer.apple.com/documentation/authenticationservices/aswebauthenticationsession?language=objc) 클래스를 통해 구현 됩니다. 다른 앱 및 Safari 브라우저에서 기존 로그인 상태를 사용 합니다. 동일한 Apple 개발자가 배포한 앱에 국한 되지 않지만 일부 사용자 조작이 필요 합니다.

앱의 기본 웹 보기를 사용 하 여 사용자를 로그인 하는 경우 MSAL 기반 응용 프로그램과 Safari 사이에서 자동 SSO를 받게 됩니다. MSAL에서 지 원하는 웹 보기에 대 한 자세한 내용은 [브라우저 및 웹 보기 사용자 지정](customize-webviews.md)을 참조 하세요.

> [!IMPORTANT]
> 현재이 유형의 SSO는 macOS에서 사용할 수 없습니다. MacOS의 MSAL은 Safari를 사용 하 여 SSO를 지원 하지 않는 WKWebView 지원 합니다. 

- **ADAL 및 MSAL macOS/iOS 앱 간의 자동 SSO**

MSAL 목표-C는 ADAL 목표 C 기반 앱을 사용 하 여 마이그레이션 및 SSO를 지원 합니다. 동일한 Apple 개발자가 앱을 배포 해야 합니다.

ADAL 및 MSAL 기반 앱 간의 앱 간 SSO에 대 한 지침은 [macOS 및 iOS의 adal 및 msal 앱 간 sso](sso-between-adal-msal-apps-macos-ios.md) 를 참조 하세요.

## <a name="silent-sso-between-apps"></a>앱 간 자동 SSO

MSAL은 iOS 키 집합 액세스 그룹을 통해 SSO 공유를 지원 합니다.

응용 프로그램에서 SSO를 사용 하도록 설정 하려면 다음 단계를 수행 해야 합니다 .이 단계는 아래에 자세히 설명 되어 있습니다.

1. 모든 응용 프로그램이 동일한 클라이언트 ID 또는 응용 프로그램 ID를 사용 하는지 확인 합니다.
1. 모든 애플리케이션이 키 집합을 공유할 수 있도록 Apple에서 동일한 서명 인증서를 공유하는지 확인합니다.
1. 각 애플리케이션에 대한 동일한 키 집합 권한 부여를 요청합니다.
1. 기본 키 집합이 아닌 경우 사용 하고자 하는 공유 키 집합에 대 한 MSAL Sdk를 알려 주세요.

### <a name="use-the-same-client-id-and-application-id"></a>동일한 클라이언트 ID 및 응용 프로그램 ID 사용

Microsoft id 플랫폼에서 토큰을 공유할 수 있는 응용 프로그램을 확인 하려면 이러한 응용 프로그램이 동일한 클라이언트 ID 또는 응용 프로그램 ID를 공유 해야 합니다. 포털에 첫 번째 애플리케이션을 등록했던 경우에 제공된 고유 식별자입니다.

Microsoft id 플랫폼에서 동일한 응용 프로그램 ID를 사용 하는 앱을 **리디렉션 uri**로 알리는 방법이 있습니다. 각 애플리케이션에는 등록 포털에 등록한 여러 개의 리디렉션 URI가 있을 수 있습니다. 제품의 각 앱은 다른 리디렉션 URI를 갖습니다. 예를 들면 다음과 같습니다.

App1 리디렉션 URI: `msauth.com.contoso.mytestapp1://auth`  
App2 리디렉션 URI: `msauth.com.contoso.mytestapp2://auth`  
App3 리디렉션 URI: `msauth.com.contoso.mytestapp3://auth`  

> [!IMPORTANT]
> 리디렉션 uri의 형식은 msal 지원 형식과 호환 되어야 합니다 .이 형식은 [Msal 리디렉션 URI 형식 요구 사항](redirect-uris-ios.md#msal-redirect-uri-format-requirements)에 설명 되어 있습니다.

### <a name="setup-keychain-sharing-between-applications"></a>응용 프로그램 간에 키 집합 공유 설정

키 집합 공유를 사용 하도록 설정 하려면 Apple의 [기능 추가](https://developer.apple.com/library/ios/documentation/IDEs/Conceptual/AppDistributionGuide/AddingCapabilities/AddingCapabilities.html) 문서를 참조 하세요. 중요 한 것은 키 집합을 호출 하려는 항목을 결정 하 고 SSO에 포함 될 모든 응용 프로그램에 해당 기능을 추가 하는 것입니다.

자격이 올바르게 설정 된 경우 `entitlements.plist` 프로젝트 디렉터리에이 예제와 같은 내용이 포함 된 파일이 표시 됩니다.

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

#### <a name="add-a-new-keychain-group"></a>새 키 집합 그룹 추가

프로젝트 **기능**에 새 키 집합 그룹을 추가 합니다. 키 집합 그룹은 다음과 같아야 합니다.
* `com.microsoft.adalcache`iOS 
* `com.microsoft.identity.universalstorage`macOS에서.

![키 집합 예제](media/single-sign-on-macos-ios/keychain-example.png)

자세한 내용은 키 [집합 그룹](howto-v2-keychain-objc.md)을 참조 하세요.

## <a name="configure-the-application-object"></a>응용 프로그램 개체 구성

각 응용 프로그램에서 키 집합 자격을 사용 하도록 설정 하 고 SSO를 사용할 준비가 되 면 `MSALPublicClientApplication` 다음 예제와 같이 키 집합 액세스 그룹을 사용 하 여를 구성 합니다.

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
> 응용 프로그램 전체에서 키 집합을 공유 하는 경우 모든 응용 프로그램은 사용자 또는 응용 프로그램 전체에서 모든 토큰을 삭제할 수 있습니다.
> 이는 작업을 수행 하기 위해 토큰을 사용 하는 응용 프로그램이 있는 경우 특히 유용 합니다.
> 키 집합을 공유 하면 앱이 Microsoft identity SDK 제거 작업을 사용 하는 경우 매우 주의 해야 합니다.

이것으로 끝입니다. Microsoft id SDK는 이제 모든 응용 프로그램에서 자격 증명을 공유 합니다. 또한 계정 목록은 응용 프로그램 인스턴스 간에 공유 됩니다.

## <a name="sso-through-authentication-broker-on-ios"></a>IOS에서 Authentication broker를 통한 SSO

MSAL은 Microsoft Authenticator를 사용 하 여 조정 된 인증을 지원 합니다. Microsoft Authenticator는 AAD 등록 장치에 대 한 SSO를 제공 하 고 응용 프로그램이 조건부 액세스 정책을 따르도록 도와줍니다.

다음 단계는 앱에 대 한 인증 브로커를 사용 하 여 SSO를 사용 하도록 설정 하는 방법입니다.

1. 앱의 info.plist에서 응용 프로그램에 대 한 broker 호환 리디렉션 URI 형식을 등록 합니다. Broker 호환 리디렉션 URI 형식은 `msauth.<app.bundle.id>://auth` 입니다. '<app.bundle.id>' '을 (를) 응용 프로그램의 번들 ID로 바꿉니다. 예를 들어:

    ```xml
    <key>CFBundleURLSchemes</key>
    <array>
        <string>msauth.<app.bundle.id></string>
    </array>
    ```

1. 앱의 info.plist에 다음 스키마를 추가 `LSApplicationQueriesSchemes` 합니다.

    ```xml
    <key>LSApplicationQueriesSchemes</key>
    <array>
         <string>msauthv2</string>
         <string>msauthv3</string>
    </array>
    ```

1. 다음을 파일에 추가 하 여 `AppDelegate.m` 콜백을 처리 합니다.

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
    
**Xcode 11을 사용 하는 경우**msal 콜백을 파일에 대신 추가 해야 `SceneDelegate` 합니다.
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