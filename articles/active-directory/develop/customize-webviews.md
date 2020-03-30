---
title: 웹 뷰 (MSAL iOS / 맥OS)& 브라우저를 사용자 정의 | Azure
titleSuffix: Microsoft identity platform
description: 사용자에게 로그인하려면 MSAL iOS/macOS 브라우저 환경을 사용자 지정하는 방법을 알아봅니다.
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
ms.openlocfilehash: 759f61860c62bcb668db6844df28c52fa28eac80
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77085912"
---
# <a name="how-to-customize-browsers-and-webviews-for-iosmacos"></a>방법: iOS/macOS용 브라우저 및 웹뷰 사용자 지정

대화형 인증을 위해서는 웹 브라우저가 필요합니다. iOS에서 MICROSOFT 인증 라이브러리(MSAL)는 기본적으로 시스템 웹 브라우저(앱 위에 나타날 수 있음)를 사용하여 사용자에게 로그인하기 위해 대화형 인증을 수행합니다. 시스템 브라우저를 사용하면 SSO(단일 사인 온) 상태를 다른 응용 프로그램 및 웹 응용 프로그램과 공유할 수 있다는 장점이 있습니다.

다음과 같이 웹 콘텐츠를 표시하기 위한 다른 옵션으로 구성을 사용자 지정하여 환경을 변경할 수 있습니다.

iOS 전용:

- [AS웹 인증세션](https://developer.apple.com/documentation/authenticationservices/aswebauthenticationsession?language=objc)
- [SF 인증 세션](https://developer.apple.com/documentation/safariservices/sfauthenticationsession?language=objc) 
- [SF사파리뷰컨트롤러](https://developer.apple.com/documentation/safariservices/sfsafariviewcontroller?language=objc)

iOS 및 macOS의 경우:

- [WKWebView](https://developer.apple.com/documentation/webkit/wkwebview?language=objc).

macOS용 MSAL은 `WKWebView`지원만 합니다.

## <a name="system-browsers"></a>시스템 브라우저

iOS의 `ASWebAuthenticationSession`경우 `SFAuthenticationSession`, `SFSafariViewController` 은 시스템 브라우저로 간주됩니다. 일반적으로 시스템 브라우저는 Safari 브라우저 응용 프로그램과 쿠키 및 기타 웹 사이트 데이터를 공유합니다.

기본적으로 MSAL은 iOS 버전을 동적으로 검색하고 해당 버전에서 사용할 수 있는 권장 시스템 브라우저를 선택합니다. iOS 12+ 그것은 `ASWebAuthenticationSession`될 것입니다 . 

| 버전 | 웹 브라우저 사용 |
|:-------------:|:-------------:|
| iOS 12+ | AS웹 인증세션 |
| iOS 11 | SF 인증 세션 |
| iOS 10 | SF사파리뷰컨트롤러 |

개발자는 MSAL 앱에 대해 다른 시스템 브라우저를 선택할 수도 있습니다.

- `SFAuthenticationSession`의 iOS 11 `ASWebAuthenticationSession`버전입니다.
- `SFSafariViewController`보다 범용적이고 웹 브라우징을 위한 인터페이스를 제공하며 로그인 용도로도 사용할 수 있습니다. iOS 9 및 10에서는 쿠키 및 기타 웹 사이트 데이터가 Safari와 공유되지만 iOS 11 이상에서는 공유되지 않습니다.

## <a name="in-app-browser"></a>인앱 브라우저

[WKWebView](https://developer.apple.com/documentation/webkit/wkwebview) 웹 콘텐츠를 표시 하는 인앱 브라우저입니다. 다른 **WKWebView** 인스턴스 또는 Safari 브라우저와 쿠키 또는 웹 사이트 데이터를 공유하지 않습니다. WKWebView는 iOS와 macOS 모두에서 사용할 수 있는 크로스 플랫폼 브라우저입니다.

## <a name="cookie-sharing-and-single-sign-on-sso-implications"></a>쿠키 공유 및 단일 사인온(SSO) 의미

사용하는 브라우저는 쿠키를 공유하는 방식 때문에 SSO 환경에 영향을 미칩니다. 다음 표에는 브라우저별 SSO 환경이 요약되어 있습니다.

| 기술    | 브라우저 유형  | iOS 가용성 | macOS 가용성 | 쿠키 및 기타 데이터 공유  | MSAL 가용성 | SSO |
|:-------------:|:-------------:|:-------------:|:-------------:|:-------------:|:-------------:|-------------:|
| [AS웹 인증세션](https://developer.apple.com/documentation/authenticationservices/aswebauthenticationsession) | 시스템 | iOS12 이상 | 맥 OS 10.15 이상 | yes | iOS만 | w / 사파리 인스턴스
| [SF 인증 세션](https://developer.apple.com/documentation/safariservices/sfauthenticationsession) | 시스템 | iOS11 이상 | 해당 없음 | yes | iOS만 |  w / 사파리 인스턴스
| [SF사파리뷰컨트롤러](https://developer.apple.com/documentation/safariservices/sfsafariviewcontroller) | 시스템 | iOS11 이상 | 해당 없음 | 예 | iOS만 | 아니요**
| **SF사파리뷰컨트롤러** | 시스템 | iOS10 | 해당 없음 | yes | iOS만 |  w / 사파리 인스턴스
| **WK웹뷰**  | 인앱 | iOS8 이상 | 맥 OS 10.10 이상 | 예 | iOS 및 macOS | 아니요**

** SSO가 작동하려면 앱 간에 토큰을 공유해야 합니다. 이를 위해서는 iOS용 Microsoft 인증기와 같은 토큰 캐시 또는 브로커 응용 프로그램이 필요합니다.

## <a name="change-the-default-browser-for-the-request"></a>요청에 대한 기본 브라우저 변경

UX 요구 사항에 따라 다음 속성을 변경하여 인앱 브라우저 또는 특정 시스템 `MSALWebviewParameters`브라우저를 사용할 수 있습니다.

```objc
@property (nonatomic) MSALWebviewType webviewType;
```

## <a name="change-per-interactive-request"></a>대화형 요청당 변경

각 요청은 `MSALInteractiveTokenParameters.webviewParameters.webviewType` `acquireTokenWithParameters:completionBlock:` API에 전달하기 전에 속성을 변경하여 기본 브라우저를 재정의하도록 구성할 수 있습니다.

또한 MSAL은 속성을 설정하여 사용자 `MSALInteractiveTokenParameters.webviewParameters.customWebView` 지정전달을 `WKWebView` 지원합니다.

예를 들어:

Objective-C
```objc
UIViewController *myParentController = ...;
WKWebView *myCustomWebView = ...;
MSALWebviewParameters *webViewParameters = [[MSALWebviewParameters alloc] initWithParentViewController:myParentController];
webViewParameters.webviewType = MSALWebviewTypeWKWebView;
webViewParameters.customWebview = myCustomWebView;
MSALInteractiveTokenParameters *interactiveParameters = [[MSALInteractiveTokenParameters alloc] initWithScopes:@[@"myscope"] webviewParameters:webViewParameters];
    
[app acquireTokenWithParameters:interactiveParameters completionBlock:completionBlock];
```
Swift
```swift
let myParentController: UIViewController = ...
let myCustomWebView: WKWebView = ...
let webViewParameters = MSALWebviewParameters(parentViewController: myParentController)
webViewParameters.webviewType = MSALWebviewType.wkWebView
webViewParameters.customWebview = myCustomWebView
let interactiveParameters = MSALInteractiveTokenParameters(scopes: ["myscope"], webviewParameters: webViewParameters)

app.acquireToken(with: interactiveParameters, completionBlock: completionBlock)
```

사용자 지정 웹 보기를 사용하는 경우 다음과 같이 표시되는 웹 콘텐츠의 상태를 나타내는 알림이 사용됩니다.

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

MSAL이 지원하는 모든 웹 브라우저 유형은 [MSALWebviewType 열거형에](https://github.com/AzureAD/microsoft-authentication-library-for-objc/blob/master/MSAL/src/public/MSALDefinitions.h#L47) 선언됩니다.

```objc
typedef NS_ENUM(NSInteger, MSALWebviewType)
{
#if TARGET_OS_IPHONE
    // For iOS 11 and up, uses AuthenticationSession (ASWebAuthenticationSession
    // or SFAuthenticationSession).
    // For older versions, with AuthenticationSession not being available, uses
    // SafariViewController.
    MSALWebviewTypeDefault,
    
    // Use SFAuthenticationSession/ASWebAuthenticationSession
    MSALWebviewTypeAuthenticationSession,
    
    // Use SFSafariViewController for all versions.
    MSALWebviewTypeSafariViewController,
    
#endif
    // Use WKWebView
    MSALWebviewTypeWKWebView,
};
```

## <a name="next-steps"></a>다음 단계

[인증 흐름 및 애플리케이션 시나리오](authentication-flows-app-scenarios.md)에 대해 알아보기
