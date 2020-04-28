---
title: 사용자 지정 브라우저 & 웹 보기 (MSAL iOS/macOS) | Microsoft
titleSuffix: Microsoft identity platform
description: 사용자 로그인을 위해 MSAL iOS/macOS 브라우저 환경을 사용자 지정 하는 방법에 대해 알아봅니다.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 08/28/2019
ms.author: marsma
ms.reviewer: oldalton
ms.custom: aaddev
ms.openlocfilehash: 3b4362e4c5e69efddfbc99ef0f98ad3c5966165c
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81450880"
---
# <a name="how-to-customize-browsers-and-webviews-for-iosmacos"></a>방법: iOS/macOS에 대 한 브라우저 및 웹 보기 사용자 지정

대화형 인증에는 웹 브라우저가 필요 합니다. IOS 및 macOS 10.15 +에서 MSAL (Microsoft 인증 라이브러리)은 기본적으로 시스템 웹 브라우저 (앱 위에 표시 될 수 있음)를 사용 하 여 사용자 로그인에 대 한 대화형 인증을 수행 합니다. 시스템 브라우저를 사용 하면 SSO (Single Sign On) 상태를 다른 응용 프로그램 및 웹 응용 프로그램과 공유할 때 이점이 있습니다.

다음과 같은 웹 콘텐츠를 표시 하는 다른 옵션에 대 한 구성을 사용자 지정 하 여 환경을 변경할 수 있습니다.

IOS에만 해당:

- [SFAuthenticationSession](https://developer.apple.com/documentation/safariservices/sfauthenticationsession?language=objc) 
- [SFSafariViewController](https://developer.apple.com/documentation/safariservices/sfsafariviewcontroller?language=objc)

IOS 및 macOS의 경우:

- [ASWebAuthenticationSession](https://developer.apple.com/documentation/authenticationservices/aswebauthenticationsession?language=objc)
- [WKWebView](https://developer.apple.com/documentation/webkit/wkwebview?language=objc).

MacOS 용 MSAL은 이전 `WKWebView` OS 버전 에서만 지원 합니다. `ASWebAuthenticationSession`는 macOS 10.15 이상 에서만 지원 됩니다. 

## <a name="system-browsers"></a>시스템 브라우저

IOS `ASWebAuthenticationSession` `SFAuthenticationSession`의 경우,, 및 `SFSafariViewController` 는 시스템 브라우저를 대상으로 합니다. MacOS의 경우에 `ASWebAuthenticationSession` 만 사용할 수 있습니다. 일반적으로 시스템 브라우저는 Safari 브라우저 응용 프로그램을 사용 하 여 쿠키 및 기타 웹 사이트 데이터를 공유 합니다.

기본적으로 MSAL은 iOS 버전을 동적으로 검색 하 고 해당 버전에서 사용할 수 있는 권장 시스템 브라우저를 선택 합니다. IOS 12 이상에서는 `ASWebAuthenticationSession`입니다. 

### <a name="default-configuration-for-ios"></a>IOS에 대 한 기본 구성

| 버전 | 웹 브라우저 사용 |
|:-------------:|:-------------:|
| iOS 12 이상 | ASWebAuthenticationSession |
| iOS 11 | SFAuthenticationSession |
| iOS 10 | SFSafariViewController |

### <a name="default-configuration-for-macos"></a>MacOS에 대 한 기본 구성

| 버전 | 웹 브라우저 사용 |
|:-------------:|:-------------:|
| macOS 10.15 + | ASWebAuthenticationSession |
| 기타 버전 | WKWebView |

개발자는 MSAL 앱에 대해 다른 시스템 브라우저를 선택할 수도 있습니다.

- `SFAuthenticationSession`는의 `ASWebAuthenticationSession`iOS 11 버전입니다.
- `SFSafariViewController`는 일반적으로 사용 되는 용도로, 웹을 검색 하기 위한 인터페이스를 제공 하며 로그인 용도로도 사용할 수 있습니다. IOS 9 및 10에서는 쿠키와 기타 웹 사이트 데이터가 Safari와 공유 되지만 iOS 11 이상에서는 공유 되지 않습니다.

## <a name="in-app-browser"></a>앱 내 브라우저

[WKWebView](https://developer.apple.com/documentation/webkit/wkwebview) 는 웹 콘텐츠를 표시 하는 앱 내 브라우저입니다. 쿠키 나 웹 사이트 데이터를 다른 **WKWebView** 인스턴스 또는 Safari 브라우저와 공유 하지 않습니다. WKWebView는 iOS 및 macOS 모두에 사용할 수 있는 플랫폼 간 브라우저입니다.

## <a name="cookie-sharing-and-single-sign-on-sso-implications"></a>쿠키 공유 및 SSO (Single sign-on)의 영향

쿠키를 공유 하는 방법 때문에 사용 하는 브라우저는 SSO 환경에 영향을 줍니다. 다음 표에는 브라우저 별로 SSO 환경이 요약 되어 있습니다.

| 기술    | 브라우저 종류  | iOS 가용성 | macOS 가용성 | 쿠키 및 기타 데이터 공유  | MSAL 가용성 | SSO |
|:-------------:|:-------------:|:-------------:|:-------------:|:-------------:|:-------------:|-------------:|
| [ASWebAuthenticationSession](https://developer.apple.com/documentation/authenticationservices/aswebauthenticationsession) | System (시스템) | iOS12 이상 | macOS 10.15 이상 | 예 | iOS 및 macOS 10.15 + | w/Safari 인스턴스
| [SFAuthenticationSession](https://developer.apple.com/documentation/safariservices/sfauthenticationsession) | System (시스템) | iOS11 이상 | 해당 없음 | 예 | iOS만 |  w/Safari 인스턴스
| [SFSafariViewController](https://developer.apple.com/documentation/safariservices/sfsafariviewcontroller) | System (시스템) | iOS11 이상 | 해당 없음 | 아니요 | iOS만 | 아니요**
| **SFSafariViewController** | System (시스템) | iOS10 | 해당 없음 | 예 | iOS만 |  w/Safari 인스턴스
| **WKWebView**  | 앱 내 | iOS8 이상 | macOS 10.10 이상 | 아니요 | iOS 및 macOS | 아니요**

* * SSO가 작동 하려면 앱 간에 토큰을 공유 해야 합니다. 이를 위해서는 토큰 캐시 또는 iOS 용 Microsoft Authenticator와 같은 broker 응용 프로그램이 필요 합니다.

## <a name="change-the-default-browser-for-the-request"></a>요청에 대 한 기본 브라우저 변경

에서 `MSALWebviewParameters`다음 속성을 변경 하 여 UX 요구 사항에 따라 앱 내 브라우저 또는 특정 시스템 브라우저를 사용할 수 있습니다.

```objc
@property (nonatomic) MSALWebviewType webviewType;
```

## <a name="change-per-interactive-request"></a>대화형 요청 마다 변경

각 요청을 `MSALInteractiveTokenParameters.webviewParameters.webviewType` `acquireTokenWithParameters:completionBlock:` API로 전달 하기 전에 속성을 변경 하 여 기본 브라우저를 재정의 하도록 구성할 수 있습니다.

또한 MSAL은 `MSALInteractiveTokenParameters.webviewParameters.customWebView` 속성을 설정 하 여 `WKWebView` 사용자 지정를 전달 하도록 지원 합니다.

예를 들면 다음과 같습니다.

Objective-C
```objc
UIViewController *myParentController = ...;
WKWebView *myCustomWebView = ...;
MSALWebviewParameters *webViewParameters = [[MSALWebviewParameters alloc] initWithAuthPresentationViewController:myParentController];
webViewParameters.webviewType = MSALWebviewTypeWKWebView;
webViewParameters.customWebview = myCustomWebView;
MSALInteractiveTokenParameters *interactiveParameters = [[MSALInteractiveTokenParameters alloc] initWithScopes:@[@"myscope"] webviewParameters:webViewParameters];
    
[app acquireTokenWithParameters:interactiveParameters completionBlock:completionBlock];
```
Swift
```swift
let myParentController: UIViewController = ...
let myCustomWebView: WKWebView = ...
let webViewParameters = MSALWebviewParameters(authPresentationViewController: myParentController)
webViewParameters.webviewType = MSALWebviewType.wkWebView
webViewParameters.customWebview = myCustomWebView
let interactiveParameters = MSALInteractiveTokenParameters(scopes: ["myscope"], webviewParameters: webViewParameters)

app.acquireToken(with: interactiveParameters, completionBlock: completionBlock)
```

사용자 지정 웹 보기를 사용 하는 경우 다음과 같이 표시 되는 웹 콘텐츠의 상태를 표시 하는 데 알림이 사용 됩니다.

```objc
/*! Fired at the start of a resource load in the webview. The URL of the load, if available, will be in the @"url" key in the userInfo dictionary */
extern NSString *MSALWebAuthDidStartLoadNotification;

/*! Fired when a resource finishes loading in the webview. */
extern NSString *MSALWebAuthDidFinishLoadNotification;

/*! Fired when web authentication fails due to reasons originating from the network. Look at the @"error" key in the userInfo dictionary for more details.*/
extern NSString *MSALWebAuthDidFailNotification;

/*! Fired when authentication finishes */
extern NSString *MSALWebAuthDidCompleteNotification;

/*! Fired before ADAL invokes the broker app */
extern NSString *MSALWebAuthWillSwitchToBrokerApp;
```

### <a name="options"></a>옵션

모든 MSAL 지원 웹 브라우저 유형은 [Msalwebviewtype 열거형](https://github.com/AzureAD/microsoft-authentication-library-for-objc/blob/master/MSAL/src/public/MSALDefinitions.h#L47) 에서 선언 됩니다.

```objc
typedef NS_ENUM(NSInteger, MSALWebviewType)
{
    /**
     For iOS 11 and up, uses AuthenticationSession (ASWebAuthenticationSession or SFAuthenticationSession).
     For older versions, with AuthenticationSession not being available, uses SafariViewController.
     For macOS 10.15 and above uses ASWebAuthenticationSession
     For older macOS versions uses WKWebView
     */
    MSALWebviewTypeDefault,
    
    /** Use ASWebAuthenticationSession where available.
     On older iOS versions uses SFAuthenticationSession
     Doesn't allow any other webview type, so if either of these are not present, fails the request*/
    MSALWebviewTypeAuthenticationSession,
    
#if TARGET_OS_IPHONE
    
    /** Use SFSafariViewController for all versions. */
    MSALWebviewTypeSafariViewController,
    
#endif
    /** Use WKWebView */
    MSALWebviewTypeWKWebView,
};
```

## <a name="next-steps"></a>다음 단계

[인증 흐름 및 애플리케이션 시나리오](authentication-flows-app-scenarios.md)에 대해 알아보기
