---
title: ADAL 앱과 MSAL 앱 간의 SSO(iOS/macOS) | Azure
titleSuffix: Microsoft identity platform
description: ADAL 앱과 MSAL 앱 간에 SSO를 공유하는 방법을 알아봅니다.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 08/28/2019
ms.author: marsma
ms.reviewer: ''
ms.custom: aaddev, has-adal-ref
ms.openlocfilehash: 548a6ff767b6dd11428b8c1a147534da26c05b61
ms.sourcegitcommit: 1deb51bc3de58afdd9871bc7d2558ee5916a3e89
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/19/2021
ms.locfileid: "122539123"
---
# <a name="how-to-sso-between-adal-and-msal-apps-on-macos-and-ios"></a>방법: macOS 및 iOS에서 ADAL 앱과 MSAL 앱 간의 SSO

iOS용 MSAL(Microsoft 인증 라이브러리)은 애플리케이션 간에 [ADAL Objective-C](https://github.com/AzureAD/azure-activedirectory-library-for-objc)와 함께 SSO 상태를 공유할 수 있습니다. 자신의 속도에 맞게 앱을 MSAL에 마이그레이션할 수 있으므로 ADAL 기반 앱과 MSAL 기반 앱이 혼합된 경우에도 사용자는 앱 간 SSO의 이점을 얻을 수 있습니다.

MSAL SDK를 사용하여 앱 간에 SSO를 설정하는 방법에 대한 지침은 [여러 앱 간의 자동 SSO](single-sign-on-macos-ios.md#silent-sso-between-apps)를 참조하세요. 이 문서에서는 ADAL과 MSAL 간의 SSO에 대해 집중적으로 설명합니다.

SSO 구현에 대한 세부 정보는 사용 중인 ADAL 버전에 따라 다릅니다.

## <a name="adal-27x"></a>ADAL 2.7.x

이 섹션에서는 MSAL과 ADAL 2.7.x 간의 SSO 차이에 대해 설명합니다.

### <a name="cache-format"></a>캐시 형식

ADAL 2.7.x는 MSAL 캐시 형식을 읽을 수 있습니다. ADAL 2.7.x 버전을 사용하여 앱 간 SSO에 대해 특별한 작업을 수행할 필요가 없습니다. 그러나 이러한 두 라이브러리가 지원하는 계정 식별자의 차이에 주의해야 합니다.

### <a name="account-identifier-differences"></a>계정 식별자 차이

MSAL 및 ADAL은 서로 다른 계정 식별자를 사용합니다. ADAL은 기본 계정 식별자로 UPN을 사용합니다. MSAL은 AAD 계정에 대한 개체 ID와 테넌트 ID를 기반으로 하는 표시 불가능한 계정 식별자와 다른 유형의 계정에 대한 `sub` 클레임을 사용합니다.

MSAL 결과에서 `MSALAccount` 개체를 받으면 `identifier` 속성에 계정 식별자가 포함됩니다. 애플리케이션은 후속 자동 요청에 이 식별자를 사용해야 합니다.

`identifier` 외에도 `MSALAccount` 개체에는 `username`이라는 표시 기능한 식별자가 포함되어 있습니다. 이는 ADAL에서 `userId`로 변환됩니다. `username`은 고유 식별자로 간주되지 않으며 언제든지 변경할 수 있으므로 ADAL의 이전 버전과의 호환성 시나리오에만 사용해야 합니다. MSAL은 `username` 또는 `identifier`를 사용하는 캐시 쿼리를 지원합니다. 여기서는 `identifier`로 쿼리하는 것이 좋습니다.

다음 표에는 ADAL과 MSAL 간의 계정 식별자 차이가 요약되어 있습니다.

| 계정 식별자                | MSAL                                                         | ADAL 2.7.x      | 이전 ADAL(ADAL 2.7.x 이전) |
| --------------------------------- | ------------------------------------------------------------ | --------------- | ------------------------------ |
| 표시 가능한 식별자            | `username`                                                   | `userId`        | `userId`                       |
| 표시 불가능한 고유 식별자 | `identifier`                                                 | `homeAccountId` | 해당 없음                            |
| 알려진 계정 ID 없음               | `MSALPublicClientApplication`에서 `allAccounts:` API를 통해 모든 계정 쿼리 | 해당 없음             | 해당 없음                            |

다음은 이러한 식별자를 제공하는 `MSALAccount` 인터페이스입니다.

```objc
@protocol MSALAccount <NSObject>

/*!
 Displayable user identifier. Can be used for UI and backward compatibility with ADAL.
 */
@property (readonly, nullable) NSString *username;

/*!
 Unique identifier for the account.
 Save this for account lookups from cache at a later point.
 */
@property (readonly, nullable) NSString *identifier;

/*!
 Host part of the authority string used for authentication based on the issuer identifier.
 */
@property (readonly, nonnull) NSString *environment;

/*!
 ID token claims for the account.
 Can be used to read additional information about the account, e.g. name
 Will only be returned if there has been an id token issued for the client Id for the account's source tenant.
 */
@property (readonly, nullable) NSDictionary<NSString *, NSString *> *accountClaims;

@end
```

### <a name="sso-from-msal-to-adal"></a>MSAL에서 ADAL로 SSO

MSAL 앱과 ADAL 앱이 있고 사용자가 먼저 MSAL 기반 앱에 로그인하는 경우 `MSALAccount` 개체의 `username`을 저장하고 ADAL 기반 앱에 `userId`로 전달하여 ADAL 앱에서 SSO를 가져올 수 있습니다. 그런 다음, ADAL은 `acquireTokenSilentWithResource:clientId:redirectUri:userId:completionBlock:` API를 사용하여 계정 정보를 자동으로 찾을 수 있습니다.

### <a name="sso-from-adal-to-msal"></a>ADAL에서 MSAL로 SSO

MSAL 앱과 ADAL 앱이 있고 사용자가 먼저 ADAL 기반 앱에 로그인하는 경우 MSAL에서 계정을 조회하기 위해 ADAL 사용자 식별자를 사용할 수 있습니다. ADAL에서 MSAL로 마이그레이션하는 경우에도 적용됩니다.

#### <a name="adals-homeaccountid"></a>ADAL의 homeAccountId

ADAL 2.7.x는 다음 속성을 통해 `ADUserInformation` 개체의 `homeAccountId`를 결과에 반환합니다.

```objc
/*! Unique AAD account identifier across tenants based on user's home OID/home tenantId. */
@property (readonly) NSString *homeAccountId;
```

ADAL의 `homeAccountId`는 MSAL의 `identifier`와 동일합니다. `accountForIdentifier:error:` API를 사용하여 계정을 조회하기 위해 MSAL에서 사용하도록 이 식별자를 저장할 수 있습니다.

#### <a name="adals-userid"></a>ADAL의 `userId`

`homeAccountId`를 사용할 수 없거나 표시 가능한 식별자만 있는 경우 ADAL의 `userId`를 사용하여 MSAL에서 계정을 조회할 수 있습니다.

MSAL에서 먼저 `username` 또는 `identifier`로 계정을 조회합니다. 쿼리할 때는 항상 `identifier`를 사용하고(있는 경우) `username`은 대체 수단으로만 사용합니다. 계정이 있는 경우 `acquireTokenSilent` 호출에서 계정을 사용합니다.

Objective-C:

```objc
NSString *msalIdentifier = @"previously.saved.msal.account.id";
MSALAccount *account = nil;
    
if (msalIdentifier)
{
    // If you have MSAL account id returned either from MSAL as identifier or ADAL as homeAccountId, use it
    account = [application accountForIdentifier:@"my.account.id.here" error:nil];
}
else
{
    // Fallback to ADAL userId for migration
    account = [application accountForUsername:@"adal.user.id" error:nil];
}
    
if (!account)
{
  // Account not found.
  return;
}

MSALSilentTokenParameters *silentParameters = [[MSALSilentTokenParameters alloc] initWithScopes:@[@"user.read"] account:account];
[application acquireTokenSilentWithParameters:silentParameters completionBlock:completionBlock];
```

Swift:

```swift
        
let msalIdentifier: String?
var account: MSALAccount
        
do {
  if let msalIdentifier = msalIdentifier {
    account = try application.account(forIdentifier: msalIdentifier)
  }
  else {
    account = try application.account(forUsername: "adal.user.id") 
  }
             
  let silentParameters = MSALSilentTokenParameters(scopes: ["user.read"], account: account)          
  application.acquireTokenSilent(with: silentParameters) {
    (result: MSALResult?, error: Error?) in
    // handle result
  }  
} catch let error as NSError {
  // handle error or account not found
}
```



MSAL에서 지원하는 계정 조회 API:

```objc
/*!
 Returns account for the given account identifier (received from an account object returned in a previous acquireToken call)
 
 @param  error      The error that occurred trying to get the accounts, if any, if you're
                    not interested in the specific error pass in nil.
 */
- (nullable MSALAccount *)accountForIdentifier:(nonnull NSString *)identifier
                                         error:(NSError * _Nullable __autoreleasing * _Nullable)error;
    
/*!
Returns account for for the given username (received from an account object returned in a previous acquireToken call or ADAL)
    
@param  username    The displayable value in UserPrincipleName(UPN) format
@param  error       The error that occurred trying to get the accounts, if any, if you're
                    not interested in the specific error pass in nil.
*/
- (MSALAccount *)accountForUsername:(NSString *)username
                              error:(NSError * __autoreleasing *)error;
```

## <a name="adal-2x-266"></a>ADAL 2.x-2.6.6

이 섹션에서는 MSAL과 ADAL 2.x-2.6.6 간의 SSO 차이에 대해 설명합니다.

이전 ADAL 버전은 기본적으로 MSAL 캐시 형식을 지원하지 않습니다. 그러나 ADAL에서 MSAL으로의 원활한 마이그레이션을 위해 MSAL은 사용자 자격 증명을 입력하도록 다시 요구하지 않고 이전 ADAL 캐시 형식을 읽을 수 있습니다.

`homeAccountId`는 이전 ADAL 버전에서 사용할 수 없으므로 `username`을 사용하여 계정을 조회해야 합니다.

```objc
/*!
 Returns account for for the given username (received from an account object returned in a previous acquireToken call or ADAL)

 @param  username    The displayable value in UserPrincipleName(UPN) format
 @param  error       The error that occurred trying to get the accounts, if any.  If you're not interested in the specific error pass in nil.
 */
- (MSALAccount *)accountForUsername:(NSString *)username
                              error:(NSError * __autoreleasing *)error;
```

예를 들면 다음과 같습니다.

Objective-C:


```objc
MSALAccount *account = [application accountForUsername:@"adal.user.id" error:nil];;
MSALSilentTokenParameters *silentParameters = [[MSALSilentTokenParameters alloc] initWithScopes:@[@"user.read"] account:account];
[application acquireTokenSilentWithParameters:silentParameters completionBlock:completionBlock];
```

Swift:

```swift
do {
  let account = try application.account(forUsername: "adal.user.id")          
  let silentParameters = MSALSilentTokenParameters(scopes: ["user.read"], account: account)
  application.acquireTokenSilent(with: silentParameters) { 
    (result: MSALResult?, error: Error?) in
    // handle result
  }   
} catch let error as NSError { 
  // handle error or account not found
}
```



또는 모든 계정을 읽을 수 있으며, 이를 통해 ADAL에서 계정 정보를 읽을 수도 있습니다.

Objective-C:

```objc
NSArray *accounts = [application allAccounts:nil];
    
if ([accounts count] == 0)
{
  // No account found.
  return; 
}
if ([accounts count] > 1)
{
  // You might want to display an account picker to user in actual application
  // For this sample we assume there's only ever one account in cache
  return;
}
    ``
MSALSilentTokenParameters *silentParameters = [[MSALSilentTokenParameters alloc] initWithScopes:@[@"user.read"] account:accounts[0]];
[application acquireTokenSilentWithParameters:silentParameters completionBlock:completionBlock];
```

Swift:

```swift
      
do {
  let accounts = try application.allAccounts()
  if accounts.count == 0 {
    // No account found.
    return
  }
  if accounts.count > 1 {
    // You might want to display an account picker to user in actual application
    // For this sample we assume there's only ever one account in cache
    return
  }
  
  let silentParameters = MSALSilentTokenParameters(scopes: ["user.read"], account: accounts[0])
  application.acquireTokenSilent(with: silentParameters) {
    (result: MSALResult?, error: Error?) in
    // handle result or error  
  }  
} catch let error as NSError { 
  // handle error
}
```



## <a name="next-steps"></a>다음 단계

[인증 흐름 및 애플리케이션 시나리오](authentication-flows-app-scenarios.md)에 대해 알아보기
