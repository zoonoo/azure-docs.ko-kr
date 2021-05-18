---
title: ADAL에서 MSAL로의 마이그레이션 가이드(MSAL iOS/macOS) | Azure
titleSuffix: Microsoft identity platform
description: iOS/macOS용 MSAL과 ObjectiveC용 Azure AD 인증 라이브러리(ADAL.ObjC)의 차이점에 대해 알아보고 iOS/macOS용 MSAL로 마이그레이션하는 방법을 설명합니다.
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
ms.openlocfilehash: 7dc3241198fbc6eeddba059251f28c6dc35c8a29
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98754937"
---
# <a name="migrate-applications-to-msal-for-ios-and-macos"></a>iOS 및 macOS용 MSAL로 애플리케이션 마이그레이션

Azure Active Directory 인증 라이브러리([ADAL Objective-C](https://github.com/AzureAD/azure-activedirectory-library-for-objc))는 v1.0 엔드포인트를 통해 Azure Active Directory 계정을 사용하기 위해 만들어졌습니다.

iOS 및 macOS용 MSAL(Microsoft 인증 라이브러리)은 Microsoft ID 플랫폼(공식 Azure AD v2.0 엔드포인트)을 통해 Azure AD(Azure Active Directory) 계정, 개인 Microsoft 계정 및 Azure AD B2C 계정과 같은 모든 Microsoft ID를 사용하도록 빌드됩니다.

Microsoft ID 플랫폼은 Azure Active Directory v1.0과 몇 가지 중요한 차이점이 있습니다. 이 문서에서는 이러한 차이를 강조하고 앱을 ADAL에서 MSAL로 마이그레이션하기 위한 지침을 제공합니다.

## <a name="adal-and-msal-app-capability-differences"></a>ADAL 및 MSAL 앱 기능 차이점

### <a name="who-can-sign-in"></a>로그인할 수 있는 사용자

* ADAL은 회사 및 학교 계정(Azure AD 계정)만 지원합니다.
* MSAL은 Hotmail.com, Outlook.com 및 Live.com과 같은 개인 Microsoft 계정(MSA 계정)을 지원합니다.
* MSAL은 회사 및 학교 계정, Azure AD B2C 계정을 지원합니다.

### <a name="standards-compliance"></a>표준 준수:

* Microsoft ID 플랫폼은 OAuth 2.0 및 Openid Connect 표준을 따릅니다.

### <a name="incremental-and-dynamic-consent"></a>증분 및 동적 동의

* Azure Active Directory v1.0 엔드포인트를 사용하려면 애플리케이션 등록 중에 모든 사용 권한을 미리 선언해야 합니다. 이는 해당 권한이 정적임을 의미합니다.
* Microsoft ID 플랫폼을 사용하면 권한을 동적으로 요청할 수 있습니다. 앱은 필요한 경우에만 권한을 요청하고 앱에 필요한 것보다 더 많이 요청할 수 있습니다.

Azure Active Directory v1.0과 Microsoft ID 플랫폼 간의 차이점에 대한 자세한 내용은 [Microsoft ID 플랫폼을 업데이트하는 이유](../azuread-dev/azure-ad-endpoint-comparison.md)를 참조하세요.

## <a name="adal-and-msal-library-differences"></a>ADAL 및 MSAL 라이브러리 차이점

MSAL 공용 API는 Azure AD v1.0과 Microsoft ID 플랫폼 간의 몇 가지 주요 차이점을 반영합니다.

### <a name="msalpublicclientapplication-instead-of-adauthenticationcontext"></a>ADAuthenticationContext 대신 MSALPublicClientApplication

`ADAuthenticationContext`는 ADAL 앱에서 만드는 첫 번째 개체입니다. ADAL의 인스턴스화를 나타냅니다. 앱은 각 Azure Active Directory 클라우드 및 테넌트(기관) 조합에 대한 `ADAuthenticationContext`의 새 인스턴스를 만듭니다. 동일한 `ADAuthenticationContext`를 사용하여 여러 공용 클라이언트 애플리케이션에 대한 토큰을 가져올 수 있습니다.

MSAL에서 [OAuth 2.0 공용 클라이언트](https://tools.ietf.org/html/rfc6749#section-2.1)를 통해 모델링되는 `MSALPublicClientApplication` 개체를 통해 주로 상호 작용합니다. `MSALPublicClientApplication`의 인스턴스 하나를 사용하여 각 기관에 대한 새 인스턴스를 만들 필요 없이 여러 AAD 클라우드 및 테넌트와 상호 작용할 수 있습니다. 대부분의 앱에 하나의 `MSALPublicClientApplication` 인스턴스만 있으면 충분합니다.

### <a name="scopes-instead-of-resources"></a>리소스 대신 범위

ADAL에서 앱은 Azure Active Directory v 1.0 엔드포인트에서 토큰을 획득하기 위해 `https://graph.microsoft.com`과 같은 *리소스* 식별자를 제공해야 했습니다. 리소스는 애플리케이션 매니페스트에 다양한 범위 또는 oAuth2Permissions를 정의하여 파악할 수 있습니다. 이로 인해 클라이언트 앱은 앱 등록 중에 미리 정의된 특정 범위 집합에 대해 해당 리소스의 토큰을 요청할 수 있습니다.

MSAL에서는 단일 리소스 식별자 대신 앱이 요청당 범위 집합을 제공합니다. 범위는 리소스/권한 형식의 사용 권한 이름 앞의 리소스 식별자입니다. 예를 들어 `https://graph.microsoft.com/user.read`

MSAL에서 범위를 제공하는 방법에는 두 가지가 있습니다.

* 앱에 필요한 모든 사용 권한 목록을 제공합니다. 예를 들면 다음과 같습니다. 

    `@[@"https://graph.microsoft.com/directory.read", @"https://graph.microsoft.com/directory.write"]`

    이 경우 앱은 `directory.read` 및 `directory.write` 사용 권한을 요청합니다. 사용자가 이 앱에 대해 이전에 동의하지 않은 경우 해당 사용 권한에 동의하라는 메시지가 표시됩니다. 애플리케이션은 사용자가 애플리케이션에 대해 이미 동의한 경우 추가 사용 권한을 받을 수도 있습니다. 사용자에게는 새 권한 또는 부여되지 않은 사용 권한에 동의하라는 메시지만 표시됩니다.

* `/.default` 범위입니다.

이는 모든 애플리케이션에 대해 기본 제공되는 범위입니다. 애플리케이션을 등록할 때 구성된 사용 권한의 정적 목록에 대한 것입니다. 이 동작은 `resource`의 동작과 유사합니다. 이는 유사한 범위 및 사용자 환경 집합이 유지되도록 마이그레이션하는 경우에 유용할 수 있습니다.

`/.default` 범위를 사용하려면 리소스 식별자에 `/.default`를 추가합니다. 예: `https://graph.microsoft.com/.default` 리소스가 슬래시(`/`)로 끝나는 경우 선행 슬래시를 포함하여 계속해서 `/.default`를 추가해야 합니다. 이 경우에는 이중 선행 슬래시(`//`)가 포함된 범위가 됩니다.

"/.default" 범위를 사용하는 방법에 대한 자세한 내용은 [여기](./v2-permissions-and-consent.md#the-default-scope)를 참조하세요.

### <a name="supporting-different-webview-types--browsers"></a>다양한 WebView 유형 및 브라우저 지원

ADAL은 iOS용 UIWebView/WKWebView와 macOS용 WebView만 지원합니다. iOS용 MSAL은 권한 부여 코드를 요청할 때 웹 콘텐츠를 표시하기 위한 더 많은 옵션을 지원하고 `UIWebView`를 더 이상 지원하지 않으므로 사용자 환경 및 보안이 향상될 수 있습니다.

기본적으로 iOS의 MSAL은 iOS 12 이상 디바이스에서 인증을 위해 Apple에서 권장하는 웹 구성 요소인 [Aswebauthenticationsession](https://developer.apple.com/documentation/authenticationservices/aswebauthenticationsession?language=objc)을 사용합니다. 앱과 Safari 브라우저 간에 쿠키를 공유하면 SSO(Single Sign-On) 이점이 제공됩니다.

앱 요구 사항 및 원하는 최종 사용자 환경에 따라 다른 웹 구성 요소를 사용하도록 선택할 수 있습니다. 자세한 옵션은 [지원되는 웹 보기 형식](customize-webviews.md)을 참조하세요.

ADAL에서 MSAL로 마이그레이션하는 경우에서 `WKWebView`는 iOS 및 macOS의 ADAL과 가장 유사한 사용자 환경을 제공합니다. 가능하면 iOS에서 `ASWebAuthenticationSession`으로 마이그레이션하는 것이 좋습니다. macOS의 경우 `WKWebView`를 사용하는 것이 좋습니다.

### <a name="account-management-api-differences"></a>계정 관리 API 차이점

ADAL 메서드 `acquireToken()` 또는 `acquireTokenSilent()`를 호출하는 경우 인증되는 계정을 나타내는 `id_token`의 클레임 목록이 포함된 `ADUserInformation` 개체를 받습니다. 또한 `ADUserInformation`은 `upn` 클레임을 기반으로 `userId`를 반환합니다. 초기 대화형 토큰 획득 후 ADAL은 개발자가 모든 자동 호출에서 `userId`를 제공할 것으로 예상합니다.

ADAL은 알려진 사용자 ID를 검색하는 API를 제공하지 않습니다. 앱을 사용하여 이러한 계정을 저장하고 관리합니다.

MSAL은 토큰을 획득할 필요 없이 MSAL에 알려진 모든 계정을 나열하는 API 집합을 제공합니다.

ADAL과 마찬가지로 MSAL은 `id_token`에서 클레임 목록을 보유하는 계정 정보를 반환합니다. `MSALResult` 개체 내 `MSALAccount` 개체의 일부입니다.

MSAL은 계정을 제거하기 위한 API 집합을 제공하며 제거된 계정은 앱에서 액세스할 수 없게 됩니다. 계정을 제거한 후 나중에 토큰 획득을 호출하면 사용자에게 대화형 토큰 획득을 수행하라는 메시지를 표시합니다. 계정 제거는 시작한 클라이언트 애플리케이션에만 적용되며, 디바이스에서 실행되는 다른 앱 또는 시스템 브라우저에서 계정이 제거되지 않습니다. 따라서 개별 앱에서 로그아웃한 후에도 사용자가 디바이스에서 계속 SSO 환경을 사용할 수 있습니다.

또한 MSAL은 나중에 토큰을 자동으로 요청하는 데 사용할 수 있는 계정 식별자도 반환합니다. 그러나 계정 식별자(`MSALAccount` 개체의 `identifier` 속성을 통해 액세스 가능)는 표시되지 않으며, 이 식별자의 형식을 가정하는 것은 불가능하고 이를 해석하거나 구문 분석하려고 해서는 안 됩니다.

### <a name="migrating-the-account-cache"></a>계정 캐시 마이그레이션

ADAL에서 마이그레이션할 때 앱은 일반적으로 MSAL에 필요한 `identifier`가 없는 ADAL의 `userId`를 저장합니다. 일회성 마이그레이션 단계로 앱은 다음 API를 사용하여 ADAL의 userId를 사용하여 MSAL 계정을 쿼리할 수 있습니다.

`- (nullable MSALAccount *)accountForUsername:(nonnull NSString *)username error:(NSError * _Nullable __autoreleasing * _Nullable)error;`

이 API는 MSAL 및 ADAL의 캐시를 모두 읽어 ADAL userId(UPN)를 통해 계정을 찾습니다.

계정이 있는 경우 개발자는 이 계정을 사용하여 자동 토큰 가져오기를 수행해야 합니다. 첫 번째 자동 토큰 획득은 계정을 효율적으로 업그레이드하며 개발자는 MSAL 결과(`identifier`)에서 MSAL 호환 계정 식별자를 얻습니다. 그 후 `identifier`는 다음 API를 사용하여 계정 조회에만 사용해야 합니다.

`- (nullable MSALAccount *)accountForIdentifier:(nonnull NSString *)identifier error:(NSError * _Nullable __autoreleasing * _Nullable)error;`

`userId`가 UPN을 기반으로 하기 때문에 MSAL의 모든 작업에 ADAL의 `userId`를 계속 사용할 수 있지만 사용자 환경 관련 문제를 야기하는 여러 제한 사항이 적용됩니다. 예를 들어 UPN이 변경되면 사용자는 다시 로그인해야 합니다. 모든 앱이 표시되지 않는 계정 `identifier`를 사용하는 것이 좋습니다.

자세한 내용은 [캐시 상태 마이그레이션](sso-between-adal-msal-apps-macos-ios.md)을 참조하세요.

### <a name="token-acquisition-changes"></a>토큰 획득 변경

MSAL에는 몇 가지 토큰 획득 호출 변경 사항이 도입됩니다.

* ADAL과 마찬가지로 `acquireTokenSilent`에서 항상 자동 요청이 발생합니다.
* ADAL과 달리 `acquireToken`은 항상 웹 보기 또는 Microsoft Authenticator 앱을 통해 사용자가 작업할 수 있는 UI를 표시합니다. 웹 보기/Microsoft Authenticator 내부의 SSO 상태에 따라 사용자에게 자격 증명을 입력하라는 메시지가 표시될 수 있습니다.
* ADAL에서 `acquireToken`은 `AD_PROMPT_AUTO`와 먼저 자동 토큰 획득을 시도하고 자동 요청이 실패하는 경우에만 UI를 표시합니다. MSAL에서는 먼저 `acquireTokenSilent`를 호출하고 자동 획득이 실패한 경우에만 `acquireToken`를 호출하여 이 논리를 구현할 수 있습니다. 이를 통해 개발자는 대화형 토큰 획득을 시작하기 전에 사용자 환경을 사용자 지정할 수 있습니다.

### <a name="error-handling-differences"></a>오류 처리의 차이점

MSAL은 앱에서 처리할 수 있는 오류와 사용자의 개입이 필요한 오류를 보다 명확하게 구분합니다. 개발자가 처리해야 하는 특정한 오류는 다음과 같습니다.

* `MSALErrorInteractionRequired`: 사용자는 대화형 요청을 수행해야 합니다. 이는 만료된 인증 세션, 조건부 액세스 정책 변경, 새로 고침 토큰이 만료되거나 해지된 경우, 캐시에 유효한 토큰이 없는 등의 다양한 이유로 인해 발생할 수 있습니다.
* `MSALErrorServerDeclinedScopes`: 요청이 완전히 완료되지 않았으며 일부 범위에 액세스 권한이 부여되지 않았습니다. 이는 하나 이상의 범위에 대한 사용자의 동의를 거부하는 경우에 발생할 수 있습니다.

[`MSALError` 목록](https://github.com/AzureAD/microsoft-authentication-library-for-objc/blob/master/MSAL/src/public/MSALError.h#L128)에서 다른 모든 오류를 처리하는 것은 선택 사항입니다. 이러한 오류의 정보를 사용하여 사용자 환경을 향상시킬 수 있습니다.

MSAL 오류 처리에 대한 자세한 내용은 [MSAL을 사용하여 예외 및 오류 처리](msal-error-handling-ios.md)를 참조하세요.

### <a name="broker-support"></a>Broker 지원

버전 0.3.0부터 MSAL은 Microsoft Authenticator 앱을 사용하여 조정된 인증을 지원합니다. 또한 Microsoft Authenticator를 사용하여 조건부 액세스 시나리오를 지원할 수 있습니다. 조건부 액세스 시나리오의 예로는 사용자가 Intune을 통해 디바이스를 등록하거나 AAD에 등록하여 토큰을 가져와야 하는 디바이스 규정 준수 정책이 있습니다. 그 외에도 MAM(모바일 애플리케이션 관리) 조건부 액세스 정책을 사용하여 앱에서 토큰을 가져오기 전에 규정 준수 증명이 필요합니다.

애플리케이션에 broker를 사용하도록 설정하려면:

1. 애플리케이션에 대한 broker 호환 리디렉션 URI 형식을 등록합니다. Broker 호환 리디렉션 URI 형식은 `msauth.<app.bundle.id>://auth`입니다. `<app.bundle.id>`를 애플리케이션의 번들 ID로 바꿉니다. ADAL에서 마이그레이션하는 경우 애플리케이션이 이미 broker를 사용할 수 있다면 추가 작업이 필요하지 않습니다. 이전 리디렉션 URI는 MSAL과 완전히 호환되므로 3단계로 건너뛸 수 있습니다.

2. info.plist 파일에 애플리케이션의 리디렉션 URI 체계를 추가합니다. 기본 MSAL 리디렉션 URI의 경우 형식은 `msauth.<app.bundle.id>`입니다. 예를 들면 다음과 같습니다.

    ```xml
    <key>CFBundleURLSchemes</key>
    <array>
        <string>msauth.<app.bundle.id></string>
    </array>
    ```

3. LSApplicationQueriesSchemes 아래의 앱 info.plist에 다음 체계를 추가합니다.

    ```xml
    <key>LSApplicationQueriesSchemes</key>
    <array>
         <string>msauthv2</string>
         <string>msauthv3</string>
    </array>
    ```

4. AppDelegate.m 파일에 다음을 추가하여 콜백을 처리합니다. Objective-C:
    
    ```objc
    - (BOOL)application:(UIApplication *)app openURL:(NSURL *)url options:(NSDictionary<NSString *,id> *)options`
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

### <a name="business-to-business-b2b"></a>B2B(Business to business)

ADAL에서 앱이 토큰을 요청하는 각 테넌트에 대해 별도의 `ADAuthenticationContext` 인스턴스를 만듭니다. 이는 더 이상 MSAL의 요구 사항이 아닙니다. MSAL에서는 acquireToken 및 acquireTokenSilent 호출에 대해 서로 다른 기관을 지정하여 `MSALPublicClientApplication`의 단일 인스턴스를 만들고 모든 AAD 클라우드 및 조직에 사용할 수 있습니다.

## <a name="sso-in-partnership-with-other-sdks"></a>다른 SDK와 공동으로 실행되는 SSO

iOS용 MSAL은 다음 SDK를 사용하여 통합 캐시를 통해 SSO를 구현할 수 있습니다.

- ADAL Objective-C 2.7.x+
- Xamarin용 MSAL.NET 2.4.x 이상
- Xamarin용 ADAL.NET 4.4.x 이상

SSO는 iOS 키 집합 공유를 통해 구현되며 동일한 Apple 개발자 계정에서 게시된 앱 간에만 사용할 수 있습니다.

iOS 키 집합 공유를 통한 SSO는 유일한 자동 SSO 유형입니다.

macOS에서 MSAL은 iOS와 macOS 기반 애플리케이션 및 ADAL Objective-C 기반 애플리케이션에 대해 다른 MSAL을 사용하여 SSO를 구현할 수 있습니다.

iOS의 MSAL은 다음과 같은 두 가지 유형의 SSO도 지원합니다.

* 웹 브라우저를 통한 SSO. iOS용 MSAL은 디바이스 및 특히 Safari 브라우저에서 다른 앱 간에 공유되는 쿠키를 통해 SSO를 제공하는 `ASWebAuthenticationSession`을 지원합니다.
* 인증 broker를 통한 SSO. iOS 디바이스에서 Microsoft Authenticator는 인증 broker 역할을 합니다. 규격을 준수하는 디바이스 요구와 같은 조건부 액세스 정책에 따라 등록된 디바이스에 대한 SSO를 제공할 수 있습니다. 버전 0.3.0부터 MSAL SDK는 기본적으로 broker를 지원합니다.

## <a name="intune-mam-sdk"></a>Intune MAM SDK

[Intune MAM SDK](/intune/app-sdk-get-started)는 [11.1.2](https://github.com/msintuneappsdk/ms-intune-app-sdk-ios/releases/tag/11.1.2) 버전부터 iOS용 MSAL을 지원합니다.

## <a name="msal-and-adal-in-the-same-app"></a>동일한 앱의 MSAL 및 ADAL

ADAL 버전 2.7.0 이상에서는 동일한 애플리케이션에서 MSAL과 함께 사용될 수 없습니다. 주된 이유는 하위 모듈 공통 코드를 공유하기 때문입니다. Objective-C는 네임스페이스를 지원하지 않으므로 ADAL 및 MSAL 프레임워크를 모두 애플리케이션에 추가하는 경우 동일한 클래스의 인스턴스가 두 개 있게 됩니다. 런타임에 무엇이 선택될지는 알 수 없습니다. 두 SDK가 충돌하는 클래스의 동일한 버전을 사용하는 경우 앱이 계속 작동할 수 있습니다. 그러나 다른 버전인 경우에는 애플리케이션에서 진단하기 어려운 예기치 않은 충돌이 발생할 수 있습니다.

동일한 프로덕션 애플리케이션에서 ADAL과 MSAL을 실행하는 것은 지원되지 않습니다. 그러나 ADAL Objective-C에서 iOS 및 macOS용 MSAL로 사용자를 테스트하고 마이그레이션하는 경우 [ADAL Objective-C 2.6.10](https://github.com/AzureAD/azure-activedirectory-library-for-objc/releases/tag/2.6.10)을 계속 사용할 수 있습니다. 동일한 애플리케이션에서 MSAL과 연동되는 유일한 버전입니다. 이 ADAL 버전에 대한 새로운 기능 업데이트는 없으므로 마이그레이션 및 테스트 목적으로만 사용해야 합니다. ADAL과 MSAL이 공존하는 환경에서 오랜 기간 앱을 사용해서는 안 됩니다.

동일한 애플리케이션에서 ADAL과 MSAL의 공존은 지원되지 않습니다.
여러 애플리케이션 간 ADAL과 MSAL의 공존은 완전히 지원됩니다.

## <a name="practical-migration-steps"></a>실용적인 마이그레이션 단계

### <a name="app-registration-migration"></a>앱 등록 마이그레이션

MSAL로 전환하고 AAD 계정을 사용하도록 설정하기 위해 기존 AAD 애플리케이션을 변경할 필요가 없습니다. 그러나 ADAL 기반 애플리케이션에서 조정된 인증을 지원하지 않는 경우에는 애플리케이션에 대한 새 리디렉션 URI를 등록해야 MSAL로 전환할 수 있습니다.

리디렉션 URI는 `msauth.<app.bundle.id>://auth` 형식이어야 합니다. `<app.bundle.id>`를 애플리케이션의 번들 ID로 바꿉니다. [Azure Portal](https://aka.ms/MobileAppReg)에서 리디렉션 URI를 지정합니다.

iOS의 경우에만 인증서 기반 인증을 지원하기 위해 추가 리디렉션 URI를 다음 `msauth://code/<broker-redirect-uri-in-url-encoded-form>`의 형식으로 애플리케이션과 Azure Portal에 등록해야 합니다. 예를 들어 `msauth://code/msauth.com.microsoft.mybundleId%3A%2F%2Fauth`

모든 앱에서 리디렉션 URI 모두를 등록하는 것이 좋습니다.

증분 승인 지원을 추가하려는 경우 **API 권한** 탭 아래의 앱 등록에 대한 액세스를 요청하도록 구성된 앱의 API 및 사용 권한을 선택합니다.

ADAL에서 마이그레이션하고 AAD와 MSA 계정을 모두 지원하려는 경우 기존 애플리케이션 등록을 업데이트해야 합니다. AAD 및 MSA를 즉시 지원하도록 기존 프로덕션 앱을 업데이트하지 않는 것이 좋습니다. 대신 테스트를 위해 AAD 및 MSA를 모두 지원하는 다른 클라이언트 ID를 만들고 모든 시나리오가 작동하는지 확인한 후 기존 앱을 업데이트합니다.

### <a name="add-msal-to-your-app"></a>앱에 MSAL 추가

기본 패키지 관리 도구를 사용하여 MSAL SDK를 앱에 추가할 수 있습니다. [자세한 지침은 여기](https://github.com/AzureAD/microsoft-authentication-library-for-objc/wiki/Installation)를 참조하세요.

### <a name="update-your-apps-infoplist-file"></a>앱의 info.plist 파일 업데이트

iOS의 경우에만 info.plist 파일에 애플리케이션의 리디렉션 URI 체계를 추가합니다. ADAL broker 호환 앱의 경우 이미 있을 것입니다. 기본 MSAL 리디렉션 URI 체계의 형식은 `msauth.<app.bundle.id>`입니다.  

```xml
<key>CFBundleURLSchemes</key>
<array>
    <string>msauth.<app.bundle.id></string>
</array>
```

`LSApplicationQueriesSchemes`에서 앱의 info.plist에 다음 체계를 추가합니다.

```xml
<key>LSApplicationQueriesSchemes</key>
<array>
     <string>msauthv2</string>
     <string>msauthv3</string>
</array>
```

### <a name="update-your-appdelegate-code"></a>AppDelegate 코드 업데이트

iOS의 경우에만 AppDelegate 파일에 다음을 추가합니다.

Objective-C:

```objc
- (BOOL)application:(UIApplication *)app openURL:(NSURL *)url options:(NSDictionary<NSString *,id> *)options`
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

**Xcode 11을 사용하는 경우** `SceneDelegate` 파일에 MSAL 콜백을 대신 추가해야 합니다.
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

이를 통해 MSAL이 broker 및 웹 구성 요소의 응답을 처리할 수 있습니다.
앱 대리자 메서드가 자동으로 "다시 구성"되었으므로 ADAL에서는 필요하지 않았습니다. 수동으로 추가하면 오류가 발생할 가능성이 줄어들며 애플리케이션이 더 많이 제어됩니다.

### <a name="enable-token-caching"></a>토큰 캐싱 사용

기본적으로 MSAL은 앱의 토큰을 iOS 또는 macOS 키 집합에 캐시합니다. 

토큰 캐싱을 사용하려면 다음을 수행합니다.
1. 애플리케이션이 제대로 서명되었는지 확인
2. Xcode 프로젝트 설정 > **기능 탭** > **키 집합 공유 사용** 으로 이동합니다.
3. **+** 를 클릭하고 다음 **키 집합 그룹** 항목을 입력합니다. 3.a iOS의 경우 `com.microsoft.adalcache`를 입력합니다. 3.b macOS의 경우 `com.microsoft.identity.universalstorage`를 입력합니다.

### <a name="create-msalpublicclientapplication-and-switch-to-its-acquiretoken-and-acquiretokesilent-calls"></a>MSALPublicClientApplication을 만들고 acquireToken 및 acquireTokeSilent 호출로 전환합니다.

다음 코드를 사용하여 `MSALPublicClientApplication`을 만들 수 있습니다.

Objective-C:

```objc
NSError *error = nil;
MSALPublicClientApplicationConfig *configuration = [[MSALPublicClientApplicationConfig alloc] initWithClientId:@"<your-client-id-here>"];
    
MSALPublicClientApplication *application =
[[MSALPublicClientApplication alloc] initWithConfiguration:configuration
                                                     error:&error];
```

Swift:

```swift
let config = MSALPublicClientApplicationConfig(clientId: "<your-client-id-here>")
do {
  let application = try MSALPublicClientApplication(configuration: config)
  // continue on with application
            
} catch let error as NSError {
  // handle error here
}
```

그런 다음 계정 관리 API를 호출하여 캐시에 계정이 있는지 확인합니다.

Objective-C:

```objc
NSString *accountIdentifier = nil /*previously saved MSAL account identifier */;
NSError *error = nil;
MSALAccount *account = [application accountForIdentifier:accountIdentifier error:&error];
```

Swift:

```swift
// definitions that need to be initialized
let application: MSALPublicClientApplication!
let accountIdentifier: String! /*previously saved MSAL account identifier */

do {
  let account = try application.account(forIdentifier: accountIdentifier)
  // continue with account usage
} catch let error as NSError {
  // handle error here
}
```



또는 모든 계정을 읽습니다.

Objective-C:

```objc
NSError *error = nil;
NSArray<MSALAccount *> *accounts = [application allAccounts:&error];
```

Swift:

```swift
let application: MSALPublicClientApplication!
do {
  let accounts = try application.allAccounts()
  // continue with account usage
} catch let error as NSError {
  // handle error here
}
```



계정이 있는 경우 MSAL `acquireTokenSilent` API를 호출합니다.

Objective-C:

```objc
MSALSilentTokenParameters *silentParameters = [[MSALSilentTokenParameters alloc] initWithScopes:@[@"<your-resource-here>/.default"] account:account];
    
[application acquireTokenSilentWithParameters:silentParameters
                              completionBlock:^(MSALResult *result, NSError *error)
{
    if (result)
    {
        NSString *accessToken = result.accessToken;
        // Use your token
    }
    else
    {
        // Check the error
        if ([error.domain isEqual:MSALErrorDomain] && error.code == MSALErrorInteractionRequired)
        {
            // Interactive auth will be required
        }
            
        // Other errors may require trying again later, or reporting authentication problems to the user
    }
}];
```

Swift:

```swift
let application: MSALPublicClientApplication!
let account: MSALAccount!
        
let silentParameters = MSALSilentTokenParameters(scopes: ["<your-resource-here>/.default"], 
                                                 account: account)
application.acquireTokenSilent(with: silentParameters) {
  (result: MSALResult?, error: Error?) in
  if let accessToken = result?.accessToken {
     // use accessToken
  }
  else {
    // Check the error
    guard let error = error else {
      assert(true, "callback should contain a valid result or error")
      return
    }
    
    let nsError = error as NSError
    if (nsError.domain == MSALErrorDomain
        && nsError.code == MSALError.interactionRequired.rawValue) {
      // Interactive auth will be required
    }
                
    // Other errors may require trying again later, or reporting authentication problems to the user
  }
}
```



## <a name="next-steps"></a>다음 단계

[인증 흐름 및 애플리케이션 시나리오](authentication-flows-app-scenarios.md)에 대해 알아보기