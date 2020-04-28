---
title: 키 집합 구성
titleSuffix: Microsoft identity platform
description: 앱이 키 집합에서 토큰을 캐시할 수 있도록 키 집합을 구성 하는 방법에 대해 알아봅니다.
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "77085854"
---
# <a name="configure-keychain"></a>키 집합 구성

[IOS 및 macOS 용 Microsoft 인증 라이브러리](msal-overview.md) (msal)가 사용자에 게 로그인 하거나 토큰을 새로 고치면 키 집합에서 토큰을 캐시 하려고 시도 합니다. 키 집합에서 토큰을 캐시 하면 MSAL이 동일한 Apple 개발자에 의해 배포 되는 여러 앱 간에 SSO (자동 Single Sign-On)를 제공할 수 있습니다. SSO는 키 집합 액세스 그룹 기능을 통해 구현 됩니다. 자세한 내용은 Apple의 키 [집합 항목 설명서](https://developer.apple.com/documentation/security/keychain_services/keychain_items/sharing_access_to_keychain_items_among_a_collection_of_apps?language=objc)를 참조 하세요.

이 문서에서는 MSAL에서 iOS 및 macOS 키 집합에 캐시 된 토큰을 쓸 수 있도록 앱 자격을 구성 하는 방법을 설명 합니다.

## <a name="default-keychain-access-group"></a>기본 키 집합 액세스 그룹

### <a name="ios"></a>iOS

IOS의 MSAL은 기본적 `com.microsoft.adalcache` 으로 액세스 그룹을 사용 합니다. 이는 MSAL 및 Azure AD 인증 Library (ADAL) Sdk 둘 다에서 사용 되는 공유 액세스 그룹으로, 동일한 게시자의 여러 앱 간에 SSO (최고 Single Sign-On) 환경을 보장 합니다.

IOS에서 **프로젝트 설정** > **기능** > 키**집합 공유** 에서 XCode의 앱 자격에 키 집합 그룹을 추가 합니다. `com.microsoft.adalcache`

### <a name="macos"></a>macOS

MacOS의 MSAL은 `com.microsoft.identity.universalstorage` 기본적으로 액세스 그룹을 사용 합니다.

MacOS 키 집합 제한으로 인해 MSAL은 `access group` macos 10.14 및 이전 버전에서 키 집합 액세스 그룹 특성 ( [kSecAttrAccessGroup](https://developer.apple.com/documentation/security/ksecattraccessgroup?language=objc)참조)으로 직접 변환 하지 않습니다. 그러나 동일한 Apple 개발자가 배포 하는 여러 응용 프로그램에서 자동 SSO를 사용할 수 있도록 하 여 SSO 관점과 유사 하 게 동작 합니다.

MacOS 10.15 이상 (macOS Catalina.properties)에서 MSAL은 키 집합 액세스 그룹 특성을 사용 하 여 iOS와 비슷하게 자동 SSO를 구현 합니다.

## <a name="custom-keychain-access-group"></a>사용자 지정 키 집합 액세스 그룹

다른 키 집합 액세스 그룹을 사용 하려는 경우 만들기 `MSALPublicClientApplicationConfig` `MSALPublicClientApplication`전에 만들 때 다음과 같이 사용자 지정 그룹을 전달할 수 있습니다.

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

## <a name="disable-keychain-sharing"></a>키 집합 공유 사용 안 함

여러 앱 간에 SSO 상태를 공유 하거나 키 집합 액세스 그룹을 사용 하지 않으려면 응용 프로그램 번들 ID를 keychainGroup로 전달 하 여 키 집합 공유를 사용 하지 않도록 설정 합니다.

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

## <a name="handle--34018-error-failed-to-set-item-into-keychain"></a>핸들-34018 오류 (항목을 키 집합으로 설정 하지 못함)

오류-34018은 일반적으로 키 집합이 올바르게 구성 되지 않았음을 의미 합니다. MSAL에서 구성 된 키 집합 액세스 그룹이 자격에 구성 된 것과 일치 하는지 확인 합니다.

## <a name="ensure-your-application-is-properly-signed"></a>애플리케이션이 제대로 서명되었는지 확인

MacOS에서 개발자가 서명 하지 않고도 응용 프로그램을 실행할 수 있습니다. 대부분의 MSAL 기능은 계속 작동 하지만 키 집합 액세스를 통한 SSO는 응용 프로그램에 서명 해야 합니다. 여러 키 집합 프롬프트가 표시 되는 경우 응용 프로그램의 서명이 유효한 지 확인 합니다.

## <a name="next-steps"></a>다음 단계

[앱 컬렉션에서 키 집합 항목에](https://developer.apple.com/documentation/security/keychain_services/keychain_items/sharing_access_to_keychain_items_among_a_collection_of_apps?language=objc) 대 한 Apple의 공유 액세스에서 키 집합 액세스 그룹에 대해 자세히 알아보세요.
