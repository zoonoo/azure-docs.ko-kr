---
title: Xamarin iOS 고려 사항 (Microsoft Authentication Library for.NET) | Azure
description: .NET (MSAL.NET)에 대 한 Microsoft 인증 라이브러리를 사용 하 여 Xamarin iOS를 사용할 때 특정 고려 사항에 알아봅니다.
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/24/2019
ms.author: ryanwi
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: bf236bff2300129ec97d3b8946c4c2a2748bca77
ms.sourcegitcommit: 6ea7f0a6e9add35547c77eef26f34d2504796565
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/14/2019
ms.locfileid: "65602137"
---
# <a name="xamarin-ios-specific-considerations-with-msalnet"></a>MSAL.NET 사용 하 여 Xamarin iOS 관련 고려 사항
Xamarin iOS에서 가지 MSAL.NET을 사용 하는 경우 계정에 수행 해야 하는 몇 가지 고려 사항

- [IOS 12 및 인증을 사용 하 여 알려진된 문제](#known-issues-with-ios-12-and-authentication)
- [재정의 하 여 구현 된 `OpenUrl` 함수는 `AppDelegate`](#implement-openurl)
- [키 집합 그룹을 사용 하도록 설정](#enable-keychain-groups)
- [토큰 캐시 공유를 사용 하도록 설정](#enable-token-cache-sharing-across-ios-applications)
- [Keychain access를 사용 하도록 설정](#enable-keychain-access)

## <a name="known-issues-with-ios-12-and-authentication"></a>IOS 12 및 인증을 사용 하 여 알려진된 문제
Microsoft는 출시 했습니다를 [보안 권고](https://github.com/aspnet/AspNetCore/issues/4647) iOS12 및 일부 형식의 인증 간의 비 호환성에 대 한 정보를 제공 합니다. 비 호환성 나누기 소셜, WSFed 및 OIDC 로그인 합니다. 이 권고는 또한 개발자가 ASP.NET에서 iOS12와 호환 되도록 응용 프로그램에 추가 하는 현재 보안 제한을 제거 하려면 수행할 수 있는 지침을 제공 합니다.  

IOS 12에서에서 웹 사이트에 로그인 하려고 할 때 무한 루프 MSAL.NET Xamarin iOS 응용 프로그램을 개발할 때 표시 될 수 있습니다 (이 비슷합니다 [ADAL 문제](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/issues/1329)합니다. 

IOS 12 사용 하 여 ASP.NET Core OIDC 인증 중단도 확인할 수 있습니다이에 설명 된 대로 Safari [WebKit 문제](https://bugs.webkit.org/show_bug.cgi?id=188165)합니다.

## <a name="implement-openurl"></a>OpenUrl 구현

재정의 해야 하는 먼저 합니다 `OpenUrl` 메서드를 `FormsApplicationDelegate` 파생 클래스 및 호출 `AuthenticationContinuationHelper.SetAuthenticationContinuationEventArgs`합니다.

```csharp
public override bool OpenUrl(UIApplication app, NSUrl url, NSDictionary options)
{
    AuthenticationContinuationHelper.SetAuthenticationContinuationEventArgs(url);
    return true;
}
```

앱이 다른 앱 호출, 리디렉션 URL은 특정 폼에 및에이 리디렉션 URL을 등록 하려면 권한이 필요를 URL 체계를 정의 해야 합니다 [Azure portal](https://portal.azure.com)합니다.

## <a name="enable-keychain-groups"></a>키 집합 그룹을 사용 하도록 설정

작업을 캐시 하 고 있는 토큰을 확인 하기 위해는 `AcquireTokenSilentAsync` 메서드 작업을 여러 단계를 따라야 합니다.
1. 키 집합 액세스를 사용 하 *`* Entitlements.plist* 파일을 지정 합니다 **키 집합 그룹** 번들 식별자에 합니다.
2. 선택 *`*Entitlements.plist*`* 파일을 **사용자 지정 자격** iOS 프로젝트 옵션 창에서 필드 **번들 서명 보기**합니다.
3. 인증서를 서명 하는 경우 XCode를 사용 하도록 동일한 Apple id입니다.

## <a name="enable-token-cache-sharing-across-ios-applications"></a>IOS 응용 프로그램 간에 공유 되는 토큰 캐시를 사용 하도록 설정

MSAL부터 2.x의 경우 여러 응용 프로그램에서 토큰 캐시를 유지 하는 데 사용 하 여 키 집합 보안 그룹을 지정할 수 있습니다. 사용 하 여 개발 된를 포함 하 여 동일한 키 집합 보안 그룹에 있는 여러 응용 프로그램 간에 토큰 캐시를 공유할 수 있습니다 [ADAL.NET](https://aka.ms/adal-net), MSAL.NET Xamarin.iOS 응용 프로그램 및 네이티브 iOS 응용 프로그램 개발 사용 하 여 [ADAL.objc](https://github.com/AzureAD/azure-activedirectory-library-for-objc) 하거나 [MSAL.objc](https://github.com/AzureAD/microsoft-authentication-library-for-objc)).

토큰 캐시를 공유 간의 모든 동일한 키 집합 보안 그룹을 사용 하는 응용 프로그램에서 single sign-on (SSO) 있습니다.

Single sign on을 사용 하도록 설정 하려면 설정 해야 합니다 `PublicClientApplication.iOSKeychainSecurityGroup` 속성을 모든 응용 프로그램에서 동일한 값입니다.

이러한 예로 사용 하 여 MSAL v3.x 것:
```csharp
var builder = PublicClientApplicationBuilder
     .Create(ClientId)
     .WithIosKeychainSecurityGroup("com.microsoft.msalrocks")
     .Build();
```

이러한 예로 사용 하 여 MSAL v2.7.x 것:

```csharp
PublicClientApplication.iOSKeychainSecurityGroup = "com.microsoft.msalrocks";
```

> [!NOTE]
> `KeychainSecurityGroup` 속성에 사용 되지 않습니다. MSAL에서 이전에 2.x의 경우 개발자 들은 사용 하는 경우에 팀 Id 접두사를 포함 해야 했습니다는 `KeychainSecurityGroup` 속성입니다. 
> 
> 이제 MSAL부터 2.7.x, MSAL 하면 해결 됩니다 TeamId 접두사 런타임에 사용 하 여 `iOSKeychainSecurityGroup` 속성입니다. 이 속성을 사용 하는 경우 값 TeamId 접두사가 없어야 합니다. 
> 
> 새 `iOSKeychainSecurityGroup` 속성에는 팀 Id를 제공 하는 개발자는 필요 하지 않습니다. `KeychainSecurityGroup` 속성은 이제 사용 되지 않습니다. 

## <a name="enable-keychain-access"></a>키 집합 액세스를 사용 하도록 설정

MSAL의 2.x 및 ADAL 4.x는 TeamId-간에 single sign-on (SSO) 동일한 게시자의 응용 프로그램을 제공 하려면 인증 라이브러리를 사용 하도록 설정 하는 키 집합에 액세스할 때 사용 됩니다. 

새로운 기능을 [TeamIdentifierPrefix](/xamarin/ios/deploy-test/provisioning/entitlements?tabs=vsmac) (TeamId)? 앱 스토어의 고유 식별자 (회사 또는 개인)는 것입니다. AppId는 앱에 대해 고유 합니다. 둘 이상의 앱이 있는 모든 앱에 대 한 TeamId 동일 됩니다 있지만 AppId 다른 됩니다. 키 집합 액세스 그룹은 접두사로 TeamId 자동으로 각 그룹에 대 한 시스템에서. OS 적용 동일한 게시자의 앱 공유 키 집합에 액세스할 수 있도록 하는 방법 이며 

초기화할 때를 `PublicClientApplication`수신 되 면는 `MsalClientException` 메시지와 함께: `TeamId returned null from the iOS keychain...`, Xamarin iOS 앱에서 다음을 수행 해야 합니다.

1. VS에서 디버그 탭으로 이동 nameOfMyApp.iOS 속성...
2. IOS 번들 서명으로 이동 
3. 사용자 지정 권한 부여를 클릭 합니다... 앱의 Entitlements.plist 파일을 선택 합니다.
4. IOS 앱의 csproj 파일에서이 줄을 포함 해야 합니다. `<CodesignEntitlements>Entitlements.plist</CodesignEntitlements>`
5. **다시 작성** 프로젝트입니다.

이것이 *또한* 에서 키 집합 액세스를 사용 하도록 설정 하는 `Entitlements.plist` 파일을 사용 하는 액세스 그룹 아래 또는 사용자 고유의:

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
<dict>
  <key>keychain-access-groups</key>
  <array>
    <string>$(AppIdentifierPrefix)com.microsoft.adalcache</string>
  </array>
</dict>
</plist>
```

## <a name="next-steps"></a>다음 단계

자세한 내용은에 나와 합니다 [iOS 특정 고려 사항](https://github.com/azure-samples/active-directory-xamarin-native-v2#ios-specific-considerations) 단락에 다음 샘플의 readme.md 파일:

샘플 | 플랫폼 | 설명 
------ | -------- | -----------
[https://github.com/Azure-Samples/active-directory-xamarin-native-v2](https://github.com/azure-samples/active-directory-xamarin-native-v2) | Xamarin iOS, Android, UWP | MSAL을 사용 하 여 MSA 및 AAD V2.0 끝점을 통해 Azure AD를 인증 하 고 결과 토큰을 사용 하 여 Microsoft Graph를 액세스 하는 방법을 보여주는 간단한 Xamarin Forms 앱입니다. <br>![토폴로지](media/msal-net-xamarin-ios-considerations/topology.png)