---
title: 키 집합 구성
titleSuffix: Microsoft identity platform
description: 앱이 키 체인에서 토큰을 캐시할 수 있도록 키체인을 구성하는 방법을 알아봅니다.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 08/28/2019
ms.author: marsma
ms.reviewer: oldalton
ms.custom: aaddev
ms.openlocfilehash: d94bf7ffe955c9ec9ee2a2e7f7c4dbaaa28df270
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77085854"
---
# <a name="configure-keychain"></a>키 집합 구성

[iOS 및 macOS(MSAL)에 대한 Microsoft 인증 라이브러리가](msal-overview.md) 사용자에 서명하거나 토큰을 새로 고치면 키체인에서 토큰을 캐시하려고 시도합니다. 키 체인의 토큰 캐싱을 사용하면 MSAL이 동일한 Apple 개발자가 배포하는 여러 앱 간에 자동 단일 사인온(SSO)을 제공할 수 있습니다. SSO는 키체인 액세스 그룹 기능을 통해 달성됩니다. 자세한 내용은 Apple의 [키체인 항목 설명서를](https://developer.apple.com/documentation/security/keychain_services/keychain_items/sharing_access_to_keychain_items_among_a_collection_of_apps?language=objc)참조하십시오.

이 문서에서는 MSAL이 iOS 및 macOS 키체인에 캐시된 토큰을 쓸 수 있도록 앱 사용 권한을 구성하는 방법을 설명합니다.

## <a name="default-keychain-access-group"></a>기본 키체인 액세스 그룹

### <a name="ios"></a>iOS

iOS의 MSAL은 `com.microsoft.adalcache` 기본적으로 액세스 그룹을 사용합니다. 이 그룹은 MSAL 및 Azure AD 인증 라이브러리(ADAL) SDK에서 모두 사용하는 공유 액세스 그룹이며 동일한 게시자의 여러 앱 간에 최상의 단일 사인온(SSO) 환경을 보장합니다.

iOS에서 프로젝트 `com.microsoft.adalcache` **설정** > 기능**키체인** > 공유에서 XCode에서 앱의 권한에**키체인** 그룹을 추가합니다.

### <a name="macos"></a>macOS

macOS의 MSAL은 기본적으로 액세스 그룹을 사용합니다. `com.microsoft.identity.universalstorage`

macOS 키체인 제한으로 인해 MSAL은 `access group` macOS 10.14 및 이전 의 키체인 액세스 그룹 특성(kSecAttrAccessGroup 참조)으로 직접 변환되지 않습니다. [kSecAttrAccessGroup](https://developer.apple.com/documentation/security/ksecattraccessgroup?language=objc) 그러나 동일한 Apple 개발자가 배포한 여러 응용 프로그램이 자동 SSO를 가질 수 있도록 하여 SSO 관점에서 유사하게 작동합니다.

macOS 10.15 이후(macOS Catalina)에서 MSAL은 키체인 액세스 그룹 속성을 사용하여 iOS와 마찬가지로 자동 SSO를 달성합니다.

## <a name="custom-keychain-access-group"></a>사용자 지정 키체인 액세스 그룹

다른 키체인 액세스 그룹을 사용하려는 경우 `MSALPublicClientApplicationConfig` `MSALPublicClientApplication`다음과 같이 만들 때 사용자 지정 그룹을 전달할 수 있습니다.

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

## <a name="disable-keychain-sharing"></a>키체인 공유 비활성화

여러 앱 간에 SSO 상태를 공유하거나 키체인 액세스 그룹을 사용하지 않으려면 응용 프로그램 번들 ID를 키체인 그룹으로 전달하여 키체인 공유를 사용하지 않도록 설정합니다.

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

## <a name="handle--34018-error-failed-to-set-item-into-keychain"></a>핸들 -34018 오류(항목을 키체인으로 설정하지 못했습니다).

오류 -34018은 일반적으로 키 체인이 올바르게 구성되지 않았음을 의미합니다. MSAL에서 구성된 키체인 액세스 그룹이 사용 권한에 구성된 액세스 그룹과 일치하는지 확인합니다.

## <a name="ensure-your-application-is-properly-signed"></a>애플리케이션이 제대로 서명되었는지 확인

macOS에서 개발자가 서명하지 않고도 응용 프로그램을 실행할 수 있습니다. MSAL의 대부분의 기능은 계속 작동하지만 키 체인 액세스를 통해 SSO에는 응용 프로그램에 서명해야 합니다. 여러 키체인 프롬프트가 발생하는 경우 응용 프로그램의 서명이 유효한지 확인합니다.

## <a name="next-steps"></a>다음 단계

Apple의 앱 컬렉션 항목 중 [키체인 항목에 대한 공유 액세스](https://developer.apple.com/documentation/security/keychain_services/keychain_items/sharing_access_to_keychain_items_among_a_collection_of_apps?language=objc) 에서 키체인 액세스 그룹에 대해 자세히 알아봅니다.
