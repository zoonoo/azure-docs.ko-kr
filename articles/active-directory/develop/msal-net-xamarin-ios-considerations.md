---
title: Xamarin iOS 고려 사항 (MSAL.NET) | Microsoft
titleSuffix: Microsoft identity platform
description: Microsoft Authentication Library for .NET (MSAL.NET)과 함께 Xamarin iOS를 사용 하기 위한 고려 사항에 대해 알아봅니다.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/09/2020
ms.author: marsma
ms.reviewer: saeeda
ms.custom: devx-track-csharp, aaddev
ms.openlocfilehash: f550cb4e9069055da6569492b35fc7fe75d70980
ms.sourcegitcommit: 1b320bc7863707a07e98644fbaed9faa0108da97
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/09/2020
ms.locfileid: "89594053"
---
# <a name="considerations-for-using-xamarin-ios-with-msalnet"></a>MSAL.NET와 함께 Xamarin iOS 사용에 대 한 고려 사항

Xamarin iOS에서 .NET 용 Microsoft Authentication Library (MSAL.NET)를 사용 하는 경우 다음을 수행 해야 합니다.

- 에서 함수를 재정의 하 고 구현 `OpenUrl` `AppDelegate` 합니다.
- 키 집합 그룹을 사용 합니다.
- 토큰 캐시 공유를 사용 하도록 설정 합니다.
- 키 집합 액세스를 사용 합니다.
- IOS 12 및 iOS 13 및 인증의 알려진 문제를 이해 합니다.

## <a name="implement-openurl"></a>OpenUrl 구현

`OpenUrl`파생 클래스의 메서드를 재정의 `FormsApplicationDelegate` 하 고를 호출 `AuthenticationContinuationHelper.SetAuthenticationContinuationEventArgs` 합니다. 예를 들면 다음과 같습니다.

```csharp
public override bool OpenUrl(UIApplication app, NSUrl url, NSDictionary options)
{
    AuthenticationContinuationHelper.SetAuthenticationContinuationEventArgs(url);
    return true;
}
```

또한 다음 작업을 수행 합니다.

* 리디렉션 URI 체계를 정의 합니다.
* 앱에서 다른 앱을 호출 하는 데 필요한 권한이 필요 합니다.
* 리디렉션 URI에 대 한 특정 폼이 있어야 합니다.
* Azure Portal에서 [리디렉션 URI를 등록](quickstart-register-app.md#add-a-redirect-uri) 합니다.

### <a name="enable-keychain-access"></a>키 집합 액세스 사용

키 집합 액세스를 사용 하도록 설정 하려면 응용 프로그램에 키 집합 액세스 그룹이 있는지 확인 합니다. API를 사용 하 여 응용 프로그램을 만들 때 키 집합 액세스 그룹을 설정할 수 있습니다 `WithIosKeychainSecurityGroup()` .

캐시 및 Single Sign-On (SSO)를 활용 하려면 모든 응용 프로그램에서 키 집합 액세스 그룹을 동일한 값으로 설정 합니다.

이 설치 예제에서는 MSAL 4.x를 사용 합니다.

```csharp
var builder = PublicClientApplicationBuilder
     .Create(ClientId)
     .WithIosKeychainSecurityGroup("com.microsoft.adalcache")
     .Build();
```

또한 파일에서 키 집합 액세스를 사용 하도록 설정 `Entitlements.plist` 합니다. 다음 액세스 그룹 또는 사용자의 액세스 그룹을 사용 합니다.

```xml
<dict>
  <key>keychain-access-groups</key>
  <array>
    <string>$(AppIdentifierPrefix)com.microsoft.adalcache</string>
  </array>
</dict>
```

API를 사용 하는 경우 `WithIosKeychainSecurityGroup()` MSAL은 응용 프로그램의 *팀 ID* ()의 끝에 보안 그룹을 자동으로 추가 `AppIdentifierPrefix` 합니다. Xcode에서 응용 프로그램을 빌드할 때 동일한 작업을 수행 하기 때문에 MSAL은 보안 그룹을 추가 합니다. 이러한 이유로 파일의 자격에 `Entitlements.plist` 키 `$(AppIdentifierPrefix)` 집합 액세스 그룹 앞에를 포함 해야 합니다.

자세한 내용은 [iOS 자격 설명서](https://developer.apple.com/documentation/security/keychain_services/keychain_items/sharing_access_to_keychain_items_among_a_collection_of_apps)를 참조 하세요.

### <a name="enable-token-cache-sharing-across-ios-applications"></a>IOS 응용 프로그램에서 토큰 캐시 공유 사용

MSAL 2.x부터 키 집합 액세스 그룹을 지정 하 여 여러 응용 프로그램에 걸쳐 토큰 캐시를 유지할 수 있습니다. 이 설정을 사용 하면 동일한 키 집합 액세스 그룹을 포함 하는 여러 응용 프로그램에서 토큰 캐시를 공유할 수 있습니다. [ADAL.NET](https://aka.ms/adal-net) 응용 프로그램, MSAL.NET 응용 프로그램 및 ADAL에서 개발한 네이티브 iOS 응용 프로그램 간에 토큰 현금을 공유할 수 있습니다. [Objc](https://github.com/AzureAD/azure-activedirectory-library-for-objc) 또는 [msal. objc](https://github.com/AzureAD/microsoft-authentication-library-for-objc).

토큰 캐시를 공유 하면 동일한 키 집합 액세스 그룹을 사용 하는 모든 응용 프로그램에서 SSO (Single Sign-On)를 사용할 수 있습니다.

이 캐시 공유를 사용 하도록 설정 하려면 메서드를 사용 하 여 `WithIosKeychainSecurityGroup()` 동일한 캐시를 공유 하는 모든 응용 프로그램에서 키 집합 액세스 그룹을 동일한 값으로 설정 합니다. 이 문서의 첫 번째 코드 예제에서는 메서드를 사용 하는 방법을 보여 줍니다.

이 문서의 앞부분에서 MSAL이 API를 사용할 때마다 추가 된다는 것을 배웠습니다 `$(AppIdentifierPrefix)` `WithIosKeychainSecurityGroup()` . 팀 ID는 `AppIdentifierPrefix` 동일한 게시자가 만든 응용 프로그램만 키 집합 액세스를 공유할 수 있도록 하기 때문에 MSAL은이 요소를 추가 합니다.

> [!NOTE]
> `KeychainSecurityGroup`속성은 사용 되지 않습니다. 대신 `iOSKeychainSecurityGroup` 속성을 사용하세요. 을 `TeamId` 사용 하는 경우에는 접두사가 필요 하지 않습니다 `iOSKeychainSecurityGroup` .

### <a name="use-microsoft-authenticator"></a>Microsoft Authenticator 사용

응용 프로그램에서 broker로 Microsoft Authenticator를 사용 하 여 다음을 사용할 수 있습니다.

- **Sso**: sso를 사용 하도록 설정 하면 사용자가 각 응용 프로그램에 로그인 할 필요가 없습니다.
- **장치 식별**: 장치 id를 사용 하 여 장치 인증서에 액세스 하 여 인증 합니다. 이 인증서는 작업 공간에 조인 될 때 장치에 생성 됩니다. 테 넌 트 관리자가 장치와 관련 된 조건부 액세스를 사용 하도록 설정 하면 응용 프로그램이 준비 됩니다.
- **응용 프로그램 id 확인**: 응용 프로그램에서 broker를 호출할 때 해당 리디렉션 URL을 전달 합니다. Broker가 리디렉션 URL을 확인 합니다.

Broker를 사용 하도록 설정 하는 방법에 대 한 자세한 내용은 [Xamarin iOS 및 Android 응용 프로그램에서 Microsoft Authenticator 또는 Microsoft Intune 회사 포털 사용](msal-net-use-brokers-with-xamarin-apps.md)을 참조 하세요.

## <a name="known-issues-with-ios-12-and-authentication"></a>IOS 12 및 인증의 알려진 문제

Microsoft는 iOS 12와 일부 인증 유형 간의 비 호환성에 대 한 [보안 권고](https://github.com/aspnet/AspNetCore/issues/4647) 를 출시 했습니다. 비호환은 소셜, WSFed 및 OIDC 로그인을 중단 합니다. 보안 권고는 응용 프로그램에서 ASP.NET 보안 제한을 제거 하 여 iOS 12와 호환 되도록 하는 방법을 이해 하는 데 도움이 됩니다.

Xamarin iOS에서 MSAL.NET 응용 프로그램을 개발할 때 iOS 12에서 웹 사이트에 로그인 하려고 하면 무한 루프가 표시 될 수 있습니다. 이러한 동작은 [iOS 12 #1329에서 웹 사이트에 로그인 하려고 할 때 GitHub: 무한 루프](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/issues/1329)의 ADAL 문제와 비슷합니다.

IOS 12 Safari를 사용 하 여 ASP.NET Core OIDC 인증이 중단 되는 것을 볼 수도 있습니다. 자세한 내용은이 [WebKit 문제](https://bugs.webkit.org/show_bug.cgi?id=188165)를 참조 하세요.

## <a name="known-issues-with-ios-13-and-authentication"></a>IOS 13 및 인증의 알려진 문제

앱이 조건부 액세스 또는 인증서 인증을 지원 해야 하는 경우 앱이 Microsoft Authenticator broker 앱과 통신할 수 있도록 설정 합니다. 그러면 MSAL이 응용 프로그램과 Microsoft Authenticator 간의 요청 및 응답을 처리 하는 일을 담당 합니다.

IOS 13에서 Apple은 사용자 지정 URL 체계를 통해 외부 응용 프로그램에서 응답을 받을 때 원본 응용 프로그램을 읽는 응용 프로그램의 기능을 제거 하 여 주요 API 변경을 수행 했습니다.

[UIApplicationOpenURLOptionsSourceApplicationKey](https://developer.apple.com/documentation/uikit/uiapplicationopenurloptionssourceapplicationkey?language=objc) 상태에 대 한 Apple의 설명서:

> *요청이 팀에 속한 다른 앱에서 시작 된 경우 UIKit는이 키의 값을 해당 앱의 ID로 설정 합니다. 원래 앱의 팀 식별자가 현재 앱의 팀 식별자와 다른 경우 키의 값은 nil입니다.*

Msal은 `UIApplication.SharedApplication.OpenUrl` msal과 Microsoft Authenticator 앱 간의 통신을 확인 하는 데 의존 하기 때문에 이러한 변경 내용이 중단 됩니다.

또한 iOS 13에서 개발자는를 사용할 때 프레젠테이션 컨트롤러를 제공 해야 `ASWebAuthenticationSession` 합니다.

Xcode 11을 사용 하 여 빌드하는 중 이며 iOS broker 또는를 사용 하는 경우 앱에 영향이 있습니다 `ASWebAuthenticationSession` .

이러한 경우에는 [MSAL.NET 4.4.0 +](https://www.nuget.org/packages/Microsoft.Identity.Client/) 를 사용 하 여 성공적인 인증을 사용 하도록 설정 합니다.

### <a name="additional-requirements"></a>추가 요구 사항

- 최신 MSAL 라이브러리를 사용 하는 경우 **Microsoft Authenticator 버전 6.3.19 +** 가 장치에 설치 되어 있는지 확인 합니다.
- MSAL.NET 4.4.0 +로 업데이트 하는 경우 `LSApplicationQueriesSchemes` *info.plist* 파일에서를 업데이트 하 고 다음을 추가 합니다 `msauthv3` .

    ```xml
    <key>LSApplicationQueriesSchemes</key>
    <array>
         <string>msauthv2</string>
         <string>msauthv3</string>
    </array>
    ```

    Info.plist에 추가 하는 `msauthv3` 것은 iOS 13을 지 원하는 장치에서 최신 Microsoft Authenticator 앱이 있는지 검색 하는 데 필요 *합니다.*

## <a name="report-an-issue"></a>문제 보고

MSAL.NET에서 발견 한 문제를 보고 하려는 경우, GitHub의 [AzureAD/dotnet (dotnet 용](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/issues) ) 리포지토리에서 문제를 여세요.

## <a name="next-steps"></a>다음 단계

Xamarin iOS의 속성에 대 한 자세한 내용은 다음 샘플의 README.md 파일에서 [iOS 관련 고려 사항](https://github.com/Azure-Samples/active-directory-xamarin-native-v2/tree/master/1-Basic#ios-specific-considerations) 단락을 참조 하세요.

예제 | 플랫폼 | Description
------ | -------- | -----------
[https://github.com/Azure-Samples/active-directory-xamarin-native-v2](https://github.com/azure-samples/active-directory-xamarin-native-v2) | Xamarin iOS, Android, 유니버설 Windows 플랫폼 (UWP) | MSAL을 사용 하 여 Azure AD 2.0 끝점을 통해 Microsoft 개인 계정 및 Azure AD를 인증 하는 방법을 보여 주는 간단한 Xamarin Forms 앱입니다. 또한 앱은 결과 토큰을 사용 하 여 Microsoft Graph에 액세스 하는 방법을 보여 줍니다.

<!--- https://github.com/Azure-Samples/active-directory-xamarin-native-v2/blob/master/ReadmeFiles/Topology.png -->
