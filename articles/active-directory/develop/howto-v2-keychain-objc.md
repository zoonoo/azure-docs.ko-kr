---
title: 키 집합 구성
titleSuffix: Microsoft identity platform
description: 앱이 키 집합에서 토큰을 캐시할 수 있도록 키 집합을 구성하는 방법에 대해 알아봅니다.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: how-to
ms.date: 08/28/2019
ms.author: marsma
ms.reviewer: oldalton
ms.custom: aaddev
ms.openlocfilehash: 06e197a6e445c7dc1179be696318905f2132ee36
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "85477739"
---
# <a name="configure-keychain"></a>키 집합 구성

[iOS 및 macOS 용 Microsoft 인증 라이브러리](msal-overview.md) (MSAL)가 사용자에게 로그인하거나 토큰을 새로 고치면 키 집합에서 토큰을 캐시하려고 시도합니다. 키 집합에서 토큰을 캐시하면 MSAL이 동일한 Apple 개발자에 의해 배포되는 여러 앱 간에 자동 Single Sign-On(SSO)를 제공할 수 있습니다. SSO는 키 집합 액세스 그룹 기능을 통해 구현됩니다. 자세한 내용은 Apple의 [키 집합 항목 문서](https://developer.apple.com/documentation/security/keychain_services/keychain_items/sharing_access_to_keychain_items_among_a_collection_of_apps?language=objc)를 참조하세요.

이 문서에서는 MSAL에서 iOS 및 macOS 키 집합에 캐시된 토큰을 쓸 수 있도록 앱 자격을 구성하는 방법을 설명합니다.

## <a name="default-keychain-access-group"></a>기본적인 키 집합 액세스 그룹

### <a name="ios"></a>iOS

iOS의 MSAL은 기본적으로 `com.microsoft.adalcache` 액세스 그룹을 사용합니다. 이는 MSAL과 Azure AD 인증 라이브러리(ADAL) SDK 둘 다에서 사용되는 공유 액세스 그룹으로, 동일한 게시자의 여러 앱 간에 최고 Single Sign-On(SSO) 환경을 보장합니다.

iOS에서 **프로젝트 설정**  >  **기능**  >  **키 집합 공유** 에서 XCode의 앱 자격에 키 집합 그룹 `com.microsoft.adalcache` 을  추가합니다.

### <a name="macos"></a>macOS

macOS의 MSAL은 기본적으로 `com.microsoft.identity.universalstorage` 액세스 그룹을 사용합니다.

macOS 키 집합 제한으로 인해 MSAL은 `access group` macos 10.14 및 이전 버전에서 키 집합 액세스 그룹 특성 ( [kSecAttrAccessGroup](https://developer.apple.com/documentation/security/ksecattraccessgroup?language=objc)참조)으로 직접 변환하지 않습니다. 그러나 동일한 Apple 개발자가 배포하는 여러 애플리케이션에서 자동 SSO를 사용할 수 있도록 하여 SSO 관점과 유사하게 동작합니다.

macOS 10.15 이상 (macOS Catalina)에서 MSAL은 키 집합 액세스 그룹 특성을 사용하여 iOS와 비슷하게 자동 SSO를 구현합니다.

## <a name="custom-keychain-access-group"></a>키 집합 액세스 그룹 사용자 지정

다른 키 집합 액세스 그룹을 사용하려는 경우 `MSALPublicClientApplication`를 만들기 전에 `MSALPublicClientApplicationConfig`을 만들 때 다음과 같이 사용자 지정 그룹을 전달할 수 있습니다.

# <a name="objective-c"></a>[Objective-C](#tab/objc)

```objc
MSALPublicClientApplicationConfig *config = [[MSALPublicClientApplicationConfig alloc] initWithClientId:@"your-client-id"
                                                                                            redirectUri:@"your-redirect-uri"
                                                                                              authority:nil];
    
config.cacheConfig.keychainSharingGroup = @"custom-group";
    
MSALPublicClientApplication *application = [[MSALPublicClientApplication alloc] initWithConfiguration:config error:nil];
    
// Now call acquiretoken. 
// Tokens will be saved into the "custom-group" access group
// and only shared with other applications declaring the same access group
```

# <a name="swift"></a>[Swift](#tab/swift)

```swift
let config = MSALPublicClientApplicationConfig(clientId: "your-client-id",
                                            redirectUri: "your-redirect-uri",
                                              authority: nil)
config.cacheConfig.keychainSharingGroup = "custom-group"
        
do {
  let application = try MSALPublicClientApplication(configuration: config)
  // continue on with application          
} catch let error as NSError {
  // handle error here
}       
```

---

## <a name="disable-keychain-sharing"></a>키 집합 공유 사용 안함

여러 앱 간에 SSO 상태를 공유하거나 키 집합 액세스 그룹을 사용하지 않으려면 애플리케이션 번들 ID를 keychainGroup로 전달하여 키 집합 공유를 사용하지 않도록 설정합니다.

# <a name="objective-c"></a>[Objective-C](#tab/objc)

```objc
config.cacheConfig.keychainSharingGroup = [[NSBundle mainBundle] bundleIdentifier];
```

# <a name="swift"></a>[Swift](#tab/swift)

```swift
if let bundleIdentifier = Bundle.main.bundleIdentifier {
    config.cacheConfig.keychainSharingGroup = bundleIdentifier
}
```

---

## <a name="handle--34018-error-failed-to-set-item-into-keychain"></a>핸들-34018 오류 (항목을 키 집합으로 설정하지 못함)

오류 -34018은 일반적으로 키 체인이 올바르게 구성되지 않았음을 의미합니다. MSAL에서 구성된 키 체인 액세스 그룹이 사용 권한에 구성된 키 체인 액세스 그룹과 일치하는지 확인합니다.

## <a name="ensure-your-application-is-properly-signed"></a>애플리케이션이 제대로 서명되었는지 확인

macOS에서는 개발자가 서명하지 않고 애플리케이션을 실행할 수 있습니다. MSAL의 기능 대부분은 계속 작동하지만, 키 체인 액세스를 통한 SSO는 애플리케이션에 서명해야 합니다. 여러 키 체인 프롬프트가 발생하는 경우 애플리케이션의 서명이 유효한지 확인합니다.

## <a name="next-steps"></a>다음 단계

Apple의 [ 앱 컬렉션 중 키체인 항목에 대한 액세스 공유](https://developer.apple.com/documentation/security/keychain_services/keychain_items/sharing_access_to_keychain_items_among_a_collection_of_apps?language=objc) 문서의 키체인 액세스 그룹에 대해 자세히 알아보세요.
