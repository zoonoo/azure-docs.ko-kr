---
title: 자마린 iOS 고려 사항 (MSAL.NET) | Azure
titleSuffix: Microsoft identity platform
description: .NET(MSAL.NET)에 대한 Microsoft 인증 라이브러리에서 Xamarin iOS를 사용하는 방법에 대해 알아봅니다.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 07/16/2019
ms.author: marsma
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 76e614b605cd07cd5dc454824dd204447f806907
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79262712"
---
# <a name="considerations-for-using-xamarin-ios-with-msalnet"></a>MSAL.NET 함께 Xamarin iOS를 사용하는 고려 사항
Xamarin iOS에서 .NET(MSAL.NET)에 대한 Microsoft 인증 라이브러리를 사용하는 경우 다음을 수행해야 합니다. 

- 에서 `OpenUrl` `AppDelegate`함수를 재정의하고 구현합니다.
- 키 체인 그룹을 활성화합니다.
- 토큰 캐시 공유를 사용하도록 설정합니다.
- 키 체인 액세스를 활성화합니다.
- iOS 12 및 인증과 관련된 알려진 문제를 이해합니다.

## <a name="implement-openurl"></a>OpenUrl 구현

파생 클래스및 `OpenUrl` 호출의 `FormsApplicationDelegate` `AuthenticationContinuationHelper.SetAuthenticationContinuationEventArgs`메서드를 재정의합니다. 예를 들면 다음과 같습니다.

```csharp
public override bool OpenUrl(UIApplication app, NSUrl url, NSDictionary options)
{
    AuthenticationContinuationHelper.SetAuthenticationContinuationEventArgs(url);
    return true;
}
```

또한 다음 작업을 수행합니다. 
* URL 구성표를 정의합니다.
* 앱에서 다른 앱을 호출할 수 있는 권한이 필요합니다.
* 리디렉션 URL에 대한 특정 양식을 갖습니다.
* [Azure 포털에서](https://portal.azure.com)리디렉션 URL을 등록합니다.

### <a name="enable-keychain-access"></a>키체인 액세스 활성화

키체인 액세스를 사용하려면 응용 프로그램에 키체인 액세스 그룹이 있는지 확인합니다. `WithIosKeychainSecurityGroup()` API를 사용하여 응용 프로그램을 만들 때 키체인 액세스 그룹을 설정할 수 있습니다.

캐시 및 단일 사인온(SSO)의 이점을 활용하려면 키체인 액세스 그룹을 모든 응용 프로그램에서 동일한 값으로 설정합니다.

이 설정 예제에서는 MSAL 4.x를 사용합니다.
```csharp
var builder = PublicClientApplicationBuilder
     .Create(ClientId)
     .WithIosKeychainSecurityGroup("com.microsoft.adalcache")
     .Build();
```

또한 파일에서 키 `Entitlements.plist` 체인 액세스를 활성화합니다. 다음 액세스 그룹 또는 사용자 고유의 액세스 그룹을 사용합니다.

```xml
<dict>
  <key>keychain-access-groups</key>
  <array>
    <string>$(AppIdentifierPrefix)com.microsoft.adalcache</string>
  </array>
</dict>
```

API를 `WithIosKeychainSecurityGroup()` 사용하면 MSAL이 자동으로 보안 그룹을 응용 프로그램의 *팀 ID()의* `AppIdentifierPrefix`끝에 적용합니다. MSAL은 Xcode에서 응용 프로그램을 빌드할 때 동일한 작업을 수행하므로 보안 그룹을 추가합니다. 따라서 `Entitlements.plist` 파일의 사용 권한은 키체인 `$(AppIdentifierPrefix)` 액세스 그룹 앞에 포함해야 합니다.

자세한 내용은 [iOS 권한 설명서를](https://developer.apple.com/documentation/security/keychain_services/keychain_items/sharing_access_to_keychain_items_among_a_collection_of_apps)참조하십시오. 

### <a name="enable-token-cache-sharing-across-ios-applications"></a>iOS 응용 프로그램 간에 토큰 캐시 공유 활성화

MSAL 2.x에서 시작하여 여러 응용 프로그램에서 토큰 캐시를 유지하도록 키체인 액세스 그룹을 지정할 수 있습니다. 이 설정을 사용하면 동일한 키체인 액세스 그룹이 있는 여러 응용 프로그램 간에 토큰 캐시를 공유할 수 있습니다. ADAL.NET [응용](https://aka.ms/adal-net) 프로그램, MSAL.NET Xamarin.iOS 응용 프로그램 및 [ADAL.objc](https://github.com/AzureAD/azure-activedirectory-library-for-objc) 또는 [MSAL.objc에서](https://github.com/AzureAD/microsoft-authentication-library-for-objc)개발 된 네이티브 iOS 응용 프로그램 간에 토큰 현금을 공유할 수 있습니다.

토큰 캐시를 공유하면 동일한 키체인 액세스 그룹을 사용하는 모든 응용 프로그램 간에 단일 사인온(SSO)을 허용할 수 있습니다.

이 캐시 공유를 사용하려면 메서드를 `WithIosKeychainSecurityGroup()` 사용하여 키체인 액세스 그룹을 동일한 캐시를 공유하는 모든 응용 프로그램에서 동일한 값으로 설정합니다. 이 문서의 첫 번째 코드 예제에서는 메서드를 사용하는 방법을 보여 주며 있습니다.

이 문서의 앞에서 는 API를 `$(AppIdentifierPrefix)` 사용할 때마다 `WithIosKeychainSecurityGroup()` MSAL이 추가한다는 것을 알게 되었습니다. 팀 ID는 `AppIdentifierPrefix` 동일한 게시자가 만든 응용 프로그램만 키체인 액세스를 공유할 수 있도록 하기 때문에 MSAL은 이 요소를 추가합니다.

> [!NOTE]
> 속성은 `KeychainSecurityGroup` 더 이상 사용되지 않습니다.
> 
> MSAL 2.x에서 시작하여 개발자는 `TeamId` `KeychainSecurityGroup` 속성을 사용할 때 접두사를 포함해야 했습니다. 그러나 MSAL 2.7.x에서 시작하여 새 `iOSKeychainSecurityGroup` 속성을 사용할 때 MSAL은 런타임 중에 `TeamId` 접두사를 해결합니다. 이 속성을 사용하는 경우 값에 `TeamId` 접두사를 포함하지 마십시오. 접두사는 필요하지 않습니다.
>
> 속성이 `KeychainSecurityGroup` 더 이상 사용되지 않으므로 `iOSKeychainSecurityGroup` 속성을 사용합니다.

### <a name="use-microsoft-authenticator"></a>마이크로소프트 인증자 사용

응용 프로그램은 다음을 활성화하기 위해 Microsoft 인증기를 브로커로 사용할 수 있습니다.

- **SSO**: SSO를 사용하도록 설정하면 사용자가 각 응용 프로그램에 로그인할 필요가 없습니다.
- **장치 식별**: 장치 ID를 사용하여 장치 인증서에 액세스하여 인증합니다. 이 인증서는 업무 직장에 가입할 때 장치에서 만들어집니다. 테넌트 관리자가 장치와 관련된 조건부 액세스를 사용하도록 설정하면 응용 프로그램이 준비됩니다.
- **응용 프로그램 식별 확인**: 응용 프로그램이 브로커를 호출하면 리디렉션 URL을 전달합니다. 브로커는 리디렉션 URL을 확인합니다.

브로커를 활성화하는 방법에 대한 자세한 내용은 [Xamarin iOS 및 Android 응용 프로그램에서 Microsoft 인증자 또는 Microsoft 인튠 회사 포털 사용을](msal-net-use-brokers-with-xamarin-apps.md)참조하십시오.

## <a name="known-issues-with-ios-12-and-authentication"></a>iOS 12 및 인증과 관련된 알려진 문제
Microsoft는 iOS 12와 일부 유형의 인증 간의 비호환성에 대한 [보안 권고를](https://github.com/aspnet/AspNetCore/issues/4647) 발표했습니다. 비호환성은 소셜, WSFed 및 OIDC 로그인을 중단합니다. 보안 권고는 개발자가 iOS 12와 호환되도록 응용 프로그램에서 ASP.NET 보안 제한을 제거하는 방법을 이해하는 데 도움이 됩니다.  

Xamarin iOS에서 MSAL.NET 응용 프로그램을 개발할 때 iOS 12에서 웹 사이트에 로그인하려고 할 때 무한 루프가 표시될 수 있습니다. 이 동작은 이 [ADAL 문제와](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/issues/1329)유사합니다. 

또한 iOS 12 Safari를 통해 코어 OIDC 인증에 ASP.NET 중단이 표시될 수도 있습니다. 자세한 내용은 이 [WebKit 문제를](https://bugs.webkit.org/show_bug.cgi?id=188165)참조하십시오.

## <a name="next-steps"></a>다음 단계

Xamarin iOS의 속성에 대한 자세한 내용은 다음 샘플의 README.md 파일의 [iOS 별 고려 사항](https://github.com/Azure-Samples/active-directory-xamarin-native-v2/tree/master/1-Basic#ios-specific-considerations) 단락을 참조하십시오.

예제 | 플랫폼 | 설명
------ | -------- | -----------
[https://github.com/Azure-Samples/active-directory-xamarin-native-v2](https://github.com/azure-samples/active-directory-xamarin-native-v2) | 자마린 아이폰 OS, 안드로이드, 유니버설 윈도우 플랫폼 (UWP) | MSAL을 사용하여 Azure AD 2.0 끝점을 통해 Microsoft 개인 계정 및 Azure AD를 인증하는 방법을 보여 주는 간단한 Xamarin Forms 앱입니다. 또한 앱은 결과 토큰을 사용하여 Microsoft Graph에 액세스하는 방법을 보여 주며 있습니다.

<!--- https://github.com/Azure-Samples/active-directory-xamarin-native-v2/blob/master/ReadmeFiles/Topology.png -->