---
title: MSAL 마이그레이션 가이드 (MSAL iOS /macOS)에 ADAL | Azure
titleSuffix: Microsoft identity platform
description: iOS/macOS용 MSAL과 ObjectiveC용 Azure AD 인증 라이브러리(ADAL)의 차이점에 대해 알아봅니다. ObjC) 및 iOS/macOS용 MSAL로 마이그레이션하는 방법.
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
ms.openlocfilehash: 6050bdc8c2600998b9804b04b62102e74612719f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77085184"
---
# <a name="migrate-applications-to-msal-for-ios-and-macos"></a>iOS 및 macOS용 MSAL로 응용 프로그램 마이그레이션

Azure Active Directory 인증[라이브러리(ADAL Objective-C)는](https://github.com/AzureAD/azure-activedirectory-library-for-objc)v1.0 끝점을 통해 Azure Active Directory 계정으로 작동하도록 만들어졌습니다.

iOS 및 macOS(MSAL)용 Microsoft 인증 라이브러리는 Microsoft ID 플랫폼을 통해 Azure Active Directory(Azure AD) 계정, 개인 Microsoft 계정 및 Azure AD B2C 계정과 같은 모든 Microsoft ID와 함께 작동하도록 빌드되었습니다( 공식적으로 Azure AD v2.0 끝점)을 참조하십시오.

Microsoft ID 플랫폼에는 Azure Active Directory v1.0과 몇 가지 주요 차이점이 있습니다. 이 문서에서는 이러한 차이점을 중대하고 ADAL에서 MSAL로 앱을 마이그레이션하는 지침을 제공합니다.

## <a name="adal-and-msal-app-capability-differences"></a>ADAL 및 MSAL 앱 기능 차이

### <a name="who-can-sign-in"></a>로그인할 수 있는 사용자

* ADAL은 Azure AD 계정이라고도 하는 업무 및 학교 계정만 지원합니다.
* MSAL은 Hotmail.com, Outlook.com 및 Live.com 같은 개인 Microsoft 계정(MSA 계정)을 지원합니다.
* MSAL은 직장 및 학교 계정과 Azure AD B2C 계정을 지원합니다.

### <a name="standards-compliance"></a>표준 준수

* Microsoft ID 플랫폼 끝점은 OAuth 2.0 및 OpenId Connect 표준을 따릅니다.

### <a name="incremental-and-dynamic-consent"></a>증분 및 동적 동의

* Azure Active Directory v1.0 끝점에는 응용 프로그램 등록 중에 모든 사용 권한을 미리 선언해야 합니다. 즉, 이러한 사용 권한이 정적입니다.
* Microsoft ID 플랫폼을 사용하면 권한을 동적으로 요청할 수 있습니다. 앱은 필요에 따라 권한을 요청하고 앱이 필요에 따라 더 많은 권한을 요청할 수 있습니다.

Azure Active Directory v1.0과 Microsoft ID 플랫폼 간의 차이점에 대한 자세한 내용은 [Microsoft ID 플랫폼(v2.0)으로 업데이트하는 이유를 참조하세요.](https://docs.microsoft.com/azure/active-directory/develop/azure-ad-endpoint-comparison)

## <a name="adal-and-msal-library-differences"></a>ADAL 및 MSAL 라이브러리 차이점

MSAL 공용 API는 Azure AD v1.0과 Microsoft ID 플랫폼 간의 몇 가지 주요 차이점을 반영합니다.

### <a name="msalpublicclientapplication-instead-of-adauthenticationcontext"></a>AD인증 컨텍스트 대신 MSALPublic클라이언트 응용 프로그램

`ADAuthenticationContext`는 ADAL 앱이 만드는 첫 번째 개체입니다. 그것은 ADAL의 인스턴스화를 나타냅니다. 앱은 각 Azure `ADAuthenticationContext` Active Directory 클라우드 및 테넌트(기관) 조합에 대한 새 인스턴스를 만듭니다. 여러 `ADAuthenticationContext` 공용 클라이언트 응용 프로그램에 대한 토큰을 얻는 데 도 마찬가지입니다.

MSAL에서 주요 상호 작용은 `MSALPublicClientApplication` [OAuth 2.0 공용 클라이언트를](https://tools.ietf.org/html/rfc6749#section-2.1)모델로 하는 개체를 통해입니다. 하나의 인스턴스는 각 기관에 대한 새 인스턴스를 만들 필요 없이 여러 AAD 클라우드 및 테넌트와 상호 작용하는 데 사용할 `MSALPublicClientApplication` 수 있습니다. 대부분의 앱에서 `MSALPublicClientApplication` 하나의 인스턴스로 충분합니다.

### <a name="scopes-instead-of-resources"></a>리소스 대신 범위

ADAL에서 앱은 Azure Active *resource* Directory `https://graph.microsoft.com` v1.0 끝점에서 토큰을 획득하는 것과 같은 리소스 식별자를 제공해야 했습니다. 리소스는 앱 매니페스트에서 여러 범위 또는 oAuth2권한(이해)을 정의할 수 있습니다. 이를 통해 클라이언트 앱은 앱 등록 중에 미리 정의된 특정 범위 집합에 대해 해당 리소스에서 토큰을 요청할 수 있었습니다.

MSAL에서 앱은 단일 리소스 식별자 대신 요청당 범위 집합을 제공합니다. 범위는 리소스 식별자 다음에 양식 리소스/권한의 사용 권한 이름이 있습니다. 예를 들어 `https://graph.microsoft.com/user.read`

MSAL에서 범위를 제공하는 방법에는 두 가지가 있습니다.

* 앱에 필요한 모든 권한 목록을 제공합니다. 예를 들어: 

    `@[@"https://graph.microsoft.com/directory.read", @"https://graph.microsoft.com/directory.write"]`

    이 경우 앱은 `directory.read` 및 `directory.write` 권한을 요청합니다. 이 앱에 대해 이전에 동의하지 않은 경우 사용자는 해당 권한에 대해 동의하라는 메시지가 표시됩니다. 응용 프로그램은 사용자가 이미 응용 프로그램에 대해 동의한 추가 권한을 받을 수도 있습니다. 사용자에게새 사용 권한 또는 부여되지 않은 권한에 대해서만 동의하라는 메시지가 표시됩니다.

* `/.default` 범위입니다.

모든 응용 프로그램에 대한 기본 제공 범위입니다. 응용 프로그램이 등록될 때 구성된 정적 사용 권한 목록을 나타냅니다. 해당 동작은 의 `resource`동작과 유사합니다. 이 기능은 유사한 범위 및 사용자 환경 집합이 유지되도록 마이그레이션할 때 유용할 수 있습니다.

`/.default` 범위를 사용하려면 리소스 `/.default` 식별자를 부가합니다. 예: `https://graph.microsoft.com/.default` 리소스가 슬래시()로`/`끝나는 경우 선행 `/.default`방향 슬래시를 포함하여 계속 추가해야 하며, 그 안에`//`이중 정방향 슬래시()가 있는 범위가 생성됩니다.

여기에서 "/.default" 범위 사용에 대한 자세한 정보를 확인할 수 [있습니다.](https://docs.microsoft.com/azure/active-directory/develop/v2-permissions-and-consent#the-default-scope)

### <a name="supporting-different-webview-types--browsers"></a>브라우저& 다양한 WebView 유형 지원

ADAL은 iOS용 UIWebView/WKWebView 및 macOS용 웹뷰만 지원합니다. iOS용 MSAL은 권한 부여 코드를 요청할 때 웹 콘텐츠를 표시하기 `UIWebView`위한 더 많은 옵션을 지원하며 더 이상 지원하지 않습니다. 사용자 경험과 보안을 향상시킬 수 있습니다.

기본적으로 iOS의 MSAL은 애플이 iOS 12+ 기기에서 인증을 위해 권장하는 웹 구성 요소인 [ASWebAuthenticationSession을](https://developer.apple.com/documentation/authenticationservices/aswebauthenticationsession?language=objc)사용합니다. 앱과 Safari 브라우저 간의 쿠키 공유를 통해 단일 사인온(SSO) 혜택을 제공합니다.

앱 요구 사항 및 원하는 최종 사용자 경험에 따라 다른 웹 구성 요소를 사용하도록 선택할 수 있습니다. 지원되는 [웹 보기 유형을](customize-webviews.md) 참조하여 더 많은 옵션을 확인하세요.

ADAL에서 MSAL로 마이그레이션할 `WKWebView` 때 iOS 및 macOS에서 ADAL과 가장 유사한 사용자 환경을 제공합니다. 가능하면 iOS로 `ASWebAuthenticationSession` 마이그레이션하는 것이 좋습니다. macOS의 경우 을 사용하는 `WKWebView`것이 좋습니다.

### <a name="account-management-api-differences"></a>계정 관리 API 차이

`acquireToken()` ADAL 메서드또는 `acquireTokenSilent()`을 호출하면 인증중인 계정을 나타내는 `ADUserInformation` `id_token` 클레임 목록이 포함된 개체를 받게 됩니다. 또한 `ADUserInformation` 클레임에 `userId` 따라 `upn` 반환 합니다. 초기 대화형 토큰 을 수집한 후 `userId` ADAL은 개발자가 모든 자동 호출을 제공할 것으로 기대합니다.

ADAL은 알려진 사용자 ID를 검색하는 API를 제공하지 않습니다. 그것은 저장하고 해당 계정을 관리하는 응용 프로그램에 의존한다.

MSAL은 토큰을 획득하지 않고도 MSAL에 알려진 모든 계정을 나열하는 API 집합을 제공합니다.

ADAL과 마찬가지로 MSAL은 에서 클레임 목록을 보유하는 계정 정보를 반환합니다. `id_token` 개체 내부의 `MSALAccount` 개체의 일부입니다. `MSALResult`

MSAL은 계정을 제거하는 API 집합을 제공하여 제거된 계정에 액세스할 수 없도록 합니다. 계정이 제거된 후 나중에 토큰 획득 호출을 통해 대화형 토큰 수집을 수행하라는 메시지가 표시됩니다. 계정 제거는 시작한 클라이언트 응용 프로그램에만 적용되며 장치 또는 시스템 브라우저에서 실행중인 다른 앱에서 계정을 제거하지 않습니다. 이렇게 하면 사용자가 개별 앱에서 로그아웃한 후에도 장치에서 SSO 환경을 계속 유지할 수 있습니다.

또한 MSAL은 나중에 자동으로 토큰을 요청하는 데 사용할 수 있는 계정 식별자를 반환합니다. 그러나 계정 `identifier` `MSALAccount` 식별자(개체의 속성을 통해 액세스할 수 있음)는 표시할 수 없으며 어떤 형식인지 가정할 수 없으며 해석하거나 구문 분석하려고 시도해서는 안 됩니다.

### <a name="migrating-the-account-cache"></a>계정 캐시 마이그레이션

ADAL에서 마이그레이션할 때 앱은 일반적으로 `userId`MSAL에서 요구하지 않는 `identifier` ADAL의 저장합니다. 일회성 마이그레이션 단계로 앱은 다음 API를 사용하여 ADAL의 userId를 사용하여 MSAL 계정을 쿼리할 수 있습니다.

`- (nullable MSALAccount *)accountForUsername:(nonnull NSString *)username error:(NSError * _Nullable __autoreleasing * _Nullable)error;`

이 API는 MSAL과 ADAL의 캐시를 모두 읽고 ADAL userId(UPN)별로 계정을 찾습니다.

계정이 발견되면 개발자는 이 계정을 사용하여 자동 토큰 획득을 수행해야 합니다. 첫 번째 자동 토큰 획득은 계정을 효과적으로 업그레이드하고 개발자는 MSAL 결과 ()에서`identifier`MSAL 호환 계정 식별자를 얻습니다. 그런 다음 `identifier` 다음 API를 사용하여 계정 조회에만 사용해야 합니다.

`- (nullable MSALAccount *)accountForIdentifier:(nonnull NSString *)identifier error:(NSError * _Nullable __autoreleasing * _Nullable)error;`

MSAL의 `userId` `userId` 모든 작업에 대해 ADAL을 계속 사용할 수 있지만 UPN을 기반으로 하므로 사용자 환경이 좋지 않은 여러 제한 사항이 적용됩니다. 예를 들어 UPN이 변경되면 사용자가 다시 로그인해야 합니다. 모든 앱은 모든 작업에 대해 `identifier` 표시가 표시되지 않는 계정을 사용하는 것이 좋습니다.

[캐시 상태 마이그레이션에](sso-between-adal-msal-apps-macos-ios.md)대해 자세히 알아보기.

### <a name="token-acquisition-changes"></a>토큰 획득 변경

MSAL은 몇 가지 토큰 획득 호출 변경 사항을 소개합니다.

* ADAL과 `acquireTokenSilent` 마찬가지로 항상 자동 요청이 발생합니다.
* ADAL과 `acquireToken` 달리 항상 웹 보기 또는 Microsoft 인증자 앱을 통해 사용자가 실행 가능한 UI를 생성합니다. 웹뷰/Microsoft 인증자 내부의 SSO 상태에 따라 사용자에게 자격 증명을 입력하라는 메시지가 표시될 수 있습니다.
* ADAL에서 `acquireToken` `AD_PROMPT_AUTO` 첫 번째 시도는 자동 토큰 획득을 시도하며 자동 요청이 실패한 경우에만 UI를 표시합니다. MSAL에서 이 논리는 자동 수집이 `acquireTokenSilent` 실패한 `acquireToken` 경우에만 호출하고 호출하여 수행할 수 있습니다. 이를 통해 개발자는 대화형 토큰 수집을 시작하기 전에 사용자 환경을 사용자 지정할 수 있습니다.

### <a name="error-handling-differences"></a>오류 처리 차이

MSAL은 앱에서 처리할 수 있는 오류와 사용자가 개입해야 하는 오류 간에 더 명확성을 제공합니다. 개발자가 처리해야 하는 오류의 수는 제한되어 있습니다.

* `MSALErrorInteractionRequired`: 사용자가 대화형 요청을 수행해야 합니다. 이 문제는 만료된 인증 세션, 조건부 액세스 정책이 변경되었거나 새로 고침 토큰이 만료되었거나 해지된 경우, 캐시에 유효한 토큰이 없는 등의 여러 가지 이유로 인해 발생할 수 있습니다.
* `MSALErrorServerDeclinedScopes`: 요청이 완전히 완료되지 않았으며 일부 범위에 액세스 권한이 부여되지 않았습니다. 이는 사용자가 하나 이상의 범위에 대한 동의를 거부하여 발생할 수 있습니다.

[ `MSALError` 목록의](https://github.com/AzureAD/microsoft-authentication-library-for-objc/blob/master/MSAL/src/public/MSALError.h#L128) 다른 모든 오류를 처리하는 것은 선택 사항입니다. 이러한 오류의 정보를 사용하여 사용자 환경을 개선할 수 있습니다.

MSAL 오류 처리에 대한 자세한 내용은 [MSAL을 사용하여 예외 및 오류 처리를](msal-handling-exceptions.md) 참조하십시오.

### <a name="broker-support"></a>브로커 지원

버전 0.3.0부터 시작되는 MSAL은 Microsoft 인증자 앱을 사용하여 중개된 인증을 지원합니다. 또한 Microsoft 인증자는 조건부 액세스 시나리오를 지원할 수 있습니다. 조건부 액세스 시나리오의 예로는 사용자가 Intune을 통해 장치를 등록하거나 토큰을 가져오기 위해 AAD에 등록해야 하는 장치 준수 정책이 있습니다. 또한 MAM(모바일 응용 프로그램 관리) 조건부 액세스 정책은 앱에서 토큰을 받기 전에 규정 준수 증명이 필요합니다.

응용 프로그램에 대한 브로커를 사용하려면 다음을 수행하십시오.

1. 응용 프로그램에 대한 브로커 호환 리디렉션 URI 형식을 등록합니다. 브로커 호환 리디렉션 URI `msauth.<app.bundle.id>://auth`형식은 . 응용 `<app.bundle.id>` 프로그램의 번들 ID로 바꿉습니다. ADAL에서 마이그레이션하는 중이고 응용 프로그램이 이미 브로커가 가능한 경우 수행해야 할 추가 방법이 없습니다. 이전 리디렉션 URI는 MSAL과 완벽하게 호환되므로 3단계로 건너뛸 수 있습니다.

2. 응용 프로그램의 리디렉션 URI 스키마를 info.plist 파일에 추가합니다. 기본 MSAL 리디렉션 URI의 경우 `msauth.<app.bundle.id>`형식은 입니다. 예를 들어:

    ```xml
    <key>CFBundleURLSchemes</key>
    <array>
        <string>msauth.<app.bundle.id></string>
    </array>
    ```

3. LSApplicationQueryScheme에서 앱의 Info.plist에 다음 스키마를 추가합니다.

    ```xml
    <key>LSApplicationQueriesSchemes</key>
    <array>
         <string>msauthv2</string>
         <string>msauthv3</string>
    </array>
    ```

4. 콜백을 처리하기 위해 AppDelegate.m 파일에 다음을 추가합니다: Objective-C:
    
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

ADAL에서 앱이 `ADAuthenticationContext` 토큰을 요청하는 각 테넌트에 대해 별도의 인스턴스를 만듭니다. 이는 더 이상 MSAL의 요구 사항이 아닙니다. MSAL에서는 acquireToken `MSALPublicClientApplication` 및 acquireTokenSilent 호출에 대해 다른 권한을 지정하여 AAD 클라우드 및 조직에 단일 인스턴스를 만들고 사용할 수 있습니다.

## <a name="sso-in-partnership-with-other-sdks"></a>다른 SDK와 파트너십을 맺고 있는 SSO

iOS용 MSAL은 다음과 같은 SDK를 통해 통합 캐시를 통해 SSO를 달성할 수 있습니다.

- ADAL 목표 C 2.7.x+
- MSAL.NET 2.4.x+
- ADAL.NET x.4.x+

SSO는 iOS 키체인 공유를 통해 달성되며 동일한 Apple 개발자 계정에서 게시된 앱 간에만 사용할 수 있습니다.

iOS 키 체인 공유를 통한 SSO는 자동 SSO 유형일 뿐입니다.

macOS에서 MSAL은 iOS 및 macOS 기반 응용 프로그램 및 ADAL Objective-C 기반 응용 프로그램에 대한 다른 MSAL과 함께 SSO를 달성할 수 있습니다.

iOS의 MSAL은 두 가지 다른 유형의 SSO도 지원합니다.

* 웹 브라우저를 통해 SSO. iOS용 `ASWebAuthenticationSession`MSAL은 장치의 다른 앱과 특히 Safari 브라우저 간에 공유되는 쿠키를 통해 SSO를 제공합니다.
* 인증 브로커를 통해 SSO. iOS 장치에서 Microsoft 인증자는 인증 브로커 역할을 합니다. 준수 장치를 요구하는 것과 같은 조건부 액세스 정책을 따를 수 있으며 등록된 장치에 대해 SSO를 제공합니다. 버전 0.3.0으로 시작하는 MSAL SDK는 기본적으로 브로커를 지원합니다.

## <a name="intune-mam-sdk"></a>인튠 맘 SDK

[Intune MAM SDK는](https://docs.microsoft.com/intune/app-sdk-get-started) 버전 [11.1.2부터](https://github.com/msintuneappsdk/ms-intune-app-sdk-ios/releases/tag/11.1.2) iOS용 MSAL을 지원합니다.

## <a name="msal-and-adal-in-the-same-app"></a>같은 응용 프로그램에서 MSAL과 ADAL

ADAL 버전 2.7.0 이상은 동일한 응용 프로그램에서 MSAL과 공존할 수 없습니다. 주된 이유는 공유 하위 모듈 공통 코드 때문입니다. Objective-C는 네임스페이스를 지원하지 않으므로 응용 프로그램에 ADAL 및 MSAL 프레임워크를 모두 추가하면 동일한 클래스의 두 인스턴스가 있습니다. 런타임에 선택되는 것을 보장할 수 없습니다. 두 SDK가 충돌하는 클래스의 동일한 버전을 사용하는 경우 앱이 계속 작동할 수 있습니다. 그러나 다른 버전인 경우 앱에서 진단하기 어려운 예기치 않은 충돌이 발생할 수 있습니다.

동일한 프로덕션 응용 프로그램에서 ADAL 및 MSAL을 실행하는 것은 지원되지 않습니다. 그러나 사용자를 ADAL Objective-C에서 iOS 및 macOS용 MSAL으로 테스트하고 마이그레이션하는 경우 [ADAL Objective-C 2.6.10을](https://github.com/AzureAD/azure-activedirectory-library-for-objc/releases/tag/2.6.10)계속 사용할 수 있습니다. 그것은 동일한 응용 프로그램에서 MSAL과 함께 작동하는 유일한 버전입니다. 이 ADAL 버전에는 새로운 기능 업데이트가 없으므로 마이그레이션 및 테스트 목적으로만 사용해야 합니다. 앱은 ADAL 및 MSAL 공존에 장기적으로 의존해서는 안 됩니다.

동일한 응용 프로그램에서 ADAL 및 MSAL 공존은 지원되지 않습니다.
여러 응용 프로그램 간의 ADAL 및 MSAL 공존이 완벽하게 지원됩니다.

## <a name="practical-migration-steps"></a>실용적인 마이그레이션 단계

### <a name="app-registration-migration"></a>앱 등록 마이그레이션

MSAL로 전환하고 AAD 계정을 사용하도록 설정하려면 기존 AAD 응용 프로그램을 변경할 필요가 없습니다. 그러나 ADAL 기반 응용 프로그램이 중개된 인증을 지원하지 않는 경우 MSAL로 전환하려면 응용 프로그램에 대한 새 리디렉션 URI를 등록해야 합니다.

리디렉션 URI는 이 형식이어야 `msauth.<app.bundle.id>://auth`합니다. 응용 `<app.bundle.id>` 프로그램의 번들 ID로 바꿉습니다. [Azure 포털에서](https://aka.ms/MobileAppReg)리디렉션 URI를 지정합니다.

iOS전용 인증서 기반 인증을 지원하려면 응용 프로그램 및 Azure 포털에 다음 형식으로 추가 리디렉션 URI를 `msauth://code/<broker-redirect-uri-in-url-encoded-form>`등록해야 합니다. 예를 들어 `msauth://code/msauth.com.microsoft.mybundleId%3A%2F%2Fauth`

모든 앱은 리디렉션 URI를 모두 등록하는 것이 좋습니다.

증분 동의에 대한 지원을 추가하려면 API **권한** 탭에서 앱 등록에 대한 액세스를 요청하도록 구성된 API 및 권한을 선택합니다.

ADAL에서 마이그레이션하고 AAD 및 MSA 계정을 모두 지원하려는 경우 두 계정을 모두 지원하도록 기존 응용 프로그램 등록을 업데이트해야 합니다. AAD와 MSA를 모두 지원하도록 기존 프로덕션 앱을 업데이트하는 것은 권장되지 않습니다. 대신 테스트를 위해 AAD와 MSA를 모두 지원하는 다른 클라이언트 ID를 만들고 모든 시나리오가 작동하는지 확인한 후 기존 앱을 업데이트합니다.

### <a name="add-msal-to-your-app"></a>앱에 MSAL 추가

원하는 패키지 관리 도구를 사용하여 앱에 MSAL SDK를 추가할 수 있습니다. [자세한 지침은 여기를](https://github.com/AzureAD/microsoft-authentication-library-for-objc/wiki/Installation)참조하십시오.

### <a name="update-your-apps-infoplist-file"></a>앱의 Info.plist 파일 업데이트

iOS의 경우 응용 프로그램의 리디렉션 URI 스키마를 info.plist 파일에 추가합니다. ADAL 브로커 호환 앱의 경우 이미 있어야 합니다. 기본 MSAL 리디렉션 URI 체계는 형식이 `msauth.<app.bundle.id>`됩니다.  

```xml
<key>CFBundleURLSchemes</key>
<array>
    <string>msauth.<app.bundle.id></string>
</array>
```

아래 앱의 Info.plist에 다음 구성표를 `LSApplicationQueriesSchemes`추가합니다.

```xml
<key>LSApplicationQueriesSchemes</key>
<array>
     <string>msauthv2</string>
     <string>msauthv3</string>
</array>
```

### <a name="update-your-appdelegate-code"></a>앱도자 코드 업데이트

iOS전용의 경우 AppDelegate.m 파일에 다음을 추가합니다.

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

이를 통해 MSAL은 브로커 및 웹 구성 요소의 응답을 처리할 수 있습니다.
ADAL에서는 앱 위임 메서드가 자동으로 "회전"하기 때문에 이 방법이 필요하지 않았습니다. 수동으로 추가하면 오류가 적고 응용 프로그램을 더 많이 제어할 수 있습니다.

### <a name="enable-token-caching"></a>토큰 캐싱 사용

기본적으로 MSAL은 앱의 토큰을 iOS 또는 macOS 키 집합에 캐시합니다. 

토큰 캐싱을 사용하려면 다음을 수행합니다.
1. 애플리케이션이 제대로 서명되었는지 확인
2. Xcode 프로젝트 설정 > **기능 탭으로** > **이동키체인 공유 사용 설정**
3. 클릭 **+** 하 고 다음 **키 체인 그룹** 항목을 입력: `com.microsoft.adalcache` 3.a iOS에 대 한, 입력 3.b macOS 입력`com.microsoft.identity.universalstorage`

### <a name="create-msalpublicclientapplication-and-switch-to-its-acquiretoken-and-acquiretokesilent-calls"></a>MSALPublic클라이언트응용 프로그램을 만들고 acquireToken으로 전환하고 TokeSilent 호출을 획득합니다.

다음 코드를 `MSALPublicClientApplication` 사용하여 만들 수 있습니다.

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



계정이 발견되면 MSAL `acquireTokenSilent` API를 호출합니다.

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
