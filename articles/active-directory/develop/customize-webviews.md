---
title: 브라우저 및 웹 보기 사용자 지정 | Microsoft id 플랫폼
description: IOS 및 macOS 용 MSAL에서 사용자를 로그인 하는 데 사용 하는 브라우저 환경을 사용자 지정 하는 방법을 알아봅니다.
services: active-directory
documentationcenter: dev-center-name
author: tylermsft
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 08/28/2019
ms.author: twhitney
ms.reviewer: ''
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0dd5be3944bdff459f6d920b358ae08efedcc431
ms.sourcegitcommit: b4665f444dcafccd74415fb6cc3d3b65746a1a31
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/11/2019
ms.locfileid: "72264206"
---
# <a name="how-to-customize-browsers-and-webviews-for-iosmacos"></a>방법: IOS/macOS에 대 한 브라우저 및 웹 보기 사용자 지정

대화형 인증에는 웹 브라우저가 필요 합니다. IOS에서 MSAL (Microsoft 인증 라이브러리)은 기본적으로 시스템 웹 브라우저 (앱 위에 표시 될 수 있음)를 사용 하 여 사용자 로그인에 대 한 대화형 인증을 수행 합니다. 시스템 브라우저를 사용 하면 SSO (Single Sign ON) 상태를 다른 응용 프로그램 및 웹 응용 프로그램과 공유할 때 이점이 있습니다.

다음과 같은 웹 콘텐츠를 표시 하는 다른 옵션에 대 한 구성을 사용자 지정 하 여 환경을 변경할 수 있습니다.

IOS에만 해당:

- [ASWebAuthenticationSession](https://developer.apple.com/documentation/authenticationservices/aswebauthenticationsession?language=objc)
- [SFAuthenticationSession](https://developer.apple.com/documentation/safariservices/sfauthenticationsession?language=objc) 
- [SFSafariViewController](https://developer.apple.com/documentation/safariservices/sfsafariviewcontroller?language=objc)

IOS 및 macOS의 경우:

- [WKWebView](https://developer.apple.com/documentation/webkit/wkwebview?language=objc).

MacOS 용 MSAL은 `WKWebView`만 지원 합니다.

## <a name="system-browsers"></a>시스템 브라우저

IOS의 경우 `ASWebAuthenticationSession`, `SFAuthenticationSession` 및 `SFSafariViewController`는 시스템 브라우저로 간주 됩니다. 일반적으로 시스템 브라우저는 Safari 브라우저 응용 프로그램을 사용 하 여 쿠키 및 기타 웹 사이트 데이터를 공유 합니다.

기본적으로 MSAL은 iOS 버전을 동적으로 검색 하 고 해당 버전에서 사용할 수 있는 권장 시스템 브라우저를 선택 합니다. IOS 12 이상에서는-0이 @no__t 됩니다. 

| 버전 | 웹 브라우저 사용 |
|:-------------:|:-------------:|
| iOS 12 이상 | ASWebAuthenticationSession |
| iOS 11 | SFAuthenticationSession |
| iOS 10 | SFSafariViewController |

개발자는 MSAL 앱에 대해 다른 시스템 브라우저를 선택할 수도 있습니다.

- `SFAuthenticationSession`은 `ASWebAuthenticationSession`의 iOS 11 버전입니다.
- `SFSafariViewController`은 일반적으로 사용 되는 용도로, 웹을 검색 하기 위한 인터페이스를 제공 하며 로그인 용도로도 사용할 수 있습니다. IOS 9 및 10에서는 쿠키와 기타 웹 사이트 데이터가 Safari와 공유 되지만 iOS 11 이상에서는 공유 되지 않습니다.

## <a name="in-app-browser"></a>앱 내 브라우저

[WKWebView](https://developer.apple.com/documentation/webkit/wkwebview) 는 웹 콘텐츠를 표시 하는 앱 내 브라우저입니다. 쿠키 나 웹 사이트 데이터를 다른 **WKWebView** 인스턴스 또는 Safari 브라우저와 공유 하지 않습니다. WKWebView는 iOS 및 macOS 모두에 사용할 수 있는 플랫폼 간 브라우저입니다.

## <a name="cookie-sharing-and-single-sign-on-sso-implications"></a>쿠키 공유 및 SSO (Single sign-on)의 영향

쿠키를 공유 하는 방법 때문에 사용 하는 브라우저는 SSO 환경에 영향을 줍니다. 다음 표에는 브라우저 별로 SSO 환경이 요약 되어 있습니다.

| 기술    | 브라우저 종류  | iOS 가용성 | macOS 가용성 | 쿠키 및 기타 데이터 공유  | MSAL 가용성 | SSO |
|:-------------:|:-------------:|:-------------:|:-------------:|:-------------:|:-------------:|-------------:|
| [ASWebAuthenticationSession](https://developer.apple.com/documentation/authenticationservices/aswebauthenticationsession) | 시스템 | iOS12 이상 | macOS 10.15 이상 | 예 | iOS만 | w/Safari 인스턴스
| [SFAuthenticationSession](https://developer.apple.com/documentation/safariservices/sfauthenticationsession) | 시스템 | iOS11 이상 | 해당 사항 없음 | 예 | iOS만 |  w/Safari 인스턴스
| [SFSafariViewController](https://developer.apple.com/documentation/safariservices/sfsafariviewcontroller) | 시스템 | iOS11 이상 | 해당 사항 없음 | 아니요 | iOS만 | 아니요 * *
| **SFSafariViewController** | 시스템 | iOS10 | 해당 사항 없음 | 예 | iOS만 |  w/Safari 인스턴스
| **WKWebView**  | 앱 내 | iOS8 이상 | macOS 10.10 이상 | 아니요 | iOS 및 macOS | 아니요 * *

\* * SSO가 작동 하려면 앱 간에 토큰을 공유 해야 합니다. 이를 위해서는 토큰 캐시 또는 iOS 용 Microsoft Authenticator와 같은 broker 응용 프로그램이 필요 합니다.

## <a name="change-the-default-browser-for-the-request"></a>요청에 대 한 기본 브라우저 변경

@No__t-0에서 다음 속성을 변경 하 여 UX 요구 사항에 따라 앱 내 브라우저 또는 특정 시스템 브라우저를 사용할 수 있습니다.

```objc
@property (nonatomic) MSALWebviewType webviewType;
```

## <a name="change-per-interactive-request"></a>대화형 요청 마다 변경

@No__t-1 API에 전달 하기 전에 `MSALInteractiveTokenParameters.webviewParameters.webviewType` 속성을 변경 하 여 기본 브라우저를 재정의 하도록 각 요청을 구성할 수 있습니다.

또한 MSAL은 `MSALInteractiveTokenParameters.webviewParameters.customWebView` 속성을 설정 하 여 사용자 지정 `WKWebView`을 전달 하도록 지원 합니다.

예를 들어 다음과 같은 가치를 제공해야 합니다.

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

### <a name="options"></a>변수

모든 MSAL 지원 웹 브라우저 유형은 [Msalwebviewtype 열거형](https://github.com/AzureAD/microsoft-authentication-library-for-objc/blob/master/MSAL/src/public/MSALDefinitions.h#L47) 에서 선언 됩니다.

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
