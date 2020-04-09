---
title: 아달 & MSAL 애플 리케이션 사이의 SSO (아이폰 OS / 맥OS) - 마이크로 소프트 아이덴티티 플랫폼 | Azure
description: ''
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
ms.custom: aaddev
ms.openlocfilehash: 7a8a1667ba1ca2a99c053c6941e3ba778299fd53
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/08/2020
ms.locfileid: "80880753"
---
# <a name="how-to-sso-between-adal-and-msal-apps-on-macos-and-ios"></a>방법: 맥 OS와 iOS에서 ADAL과 MSAL 앱 사이의 SSO

iOS용 MSAL(Microsoft 인증 라이브러리)은 응용 프로그램 간에 [ADAL Objective-C와](https://github.com/AzureAD/azure-activedirectory-library-for-objc) SSO 상태를 공유할 수 있습니다. ADAL 및 MSAL 기반 앱이 혼합되어 있어도 사용자가 교차 앱 SSO의 이점을 누릴 수 있도록 원하는 속도로 앱을 MSAL로 마이그레이션할 수 있습니다.

MSAL SDK를 사용하여 앱 간에 SSO를 설정하는 지침을 찾고 있다면 [여러 앱 간에 자동 SSO를](single-sign-on-macos-ios.md#silent-sso-between-apps)참조하십시오. 이 문서에서는 ADAL과 MSAL 간의 SSO에 중점을 둡니다.

SSO를 구현하는 세부 사항은 사용 중이던 ADAL 버전에 따라 다릅니다.

## <a name="adal-27x"></a>ADAL 2.7.x

이 섹션에서는 MSAL과 ADAL 2.7.x 간의 SSO 차이점을 다룹니다.

### <a name="cache-format"></a>캐시 형식

ADAL 2.7.x는 MSAL 캐시 형식을 읽을 수 있습니다. 당신은 버전 ADAL 2.7.x와 크로스 앱 SSO에 대한 특별한 아무것도 할 필요가 없습니다. 그러나 두 라이브러리가 지원하는 계정 식별자의 차이점을 알고 있어야 합니다.

### <a name="account-identifier-differences"></a>계정 식별자 차이

MSAL 및 ADAL은 서로 다른 계정 식별자를 사용합니다. ADAL은 UPN을 기본 계정 식별자로 사용합니다. MSAL은 AAD 계정에 대한 개체 ID 및 테넌트 ID를 기반으로 하는 표시가 없는 계정 식별자와 다른 유형의 계정에 대한 클레임을 `sub` 사용합니다.

MSAL 결과에서 개체를 `MSALAccount` 받으면 `identifier` 속성에 계정 식별자가 포함됩니다. 응용 프로그램은 후속 자동 요청에 이 식별자를 사용해야 합니다.

`identifier`개체에는 에 `MSALAccount` 이외에도 `username`. 즉, `userId` ADAL에서 변환합니다. `username`고유 식별자로 간주되지 않으며 언제든지 변경할 수 있으므로 ADAL과의 이전 버전 과의 호환성 시나리오에만 사용해야 합니다. MSAL은 `username` 또는 `identifier`에 의한 `identifier` 쿼리를 사용하는 캐시 쿼리를 지원합니다.

다음 표에는 ADAL과 MSAL 간의 계정 식별자 차이점이 요약되어 있습니다.

| 계정 식별자                | MSAL                                                         | ADAL 2.7.x      | 이전 ADAL(ADAL 2.7.x 이전) |
| --------------------------------- | ------------------------------------------------------------ | --------------- | ------------------------------ |
| 표시 가능한 식별자            | `username`                                                   | `userId`        | `userId`                       |
| 표시할 수 없는 고유 식별자 | `identifier`                                                 | `homeAccountId` | 해당 없음                            |
| 알려진 계정 ID가 없습니다.               | 에서 API를 `allAccounts:` 통해 모든 계정 쿼리`MSALPublicClientApplication` | 해당 없음             | 해당 없음                            |

이러한 식별자를 제공하는 `MSALAccount` 인터페이스는 다음과 같은 인터페이스입니다.

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

### <a name="sso-from-msal-to-adal"></a>MSAL에서 아달까지 의 SSO

MSAL 앱과 ADAL 앱이 있고 사용자가 MSAL 기반 앱에 처음 로그인한 경우 `username` `MSALAccount` 개체에서 저장하고 ADAL 기반 앱으로 `userId`전달하여 ADAL 앱에서 SSO를 얻을 수 있습니다. 그런 다음 ADAL은 `acquireTokenSilentWithResource:clientId:redirectUri:userId:completionBlock:` API를 통해 계정 정보를 자동으로 찾을 수 있습니다.

### <a name="sso-from-adal-to-msal"></a>아달에서 MSAL까지 의 SSO

MSAL 앱과 ADAL 앱이 있고 사용자가 ADAL 기반 앱에 처음 로그인한 경우 MSAL의 계정 조회에 ADAL 사용자 식별자를 사용할 수 있습니다. 이는 ADAL에서 MSAL로 마이그레이션할 때도 적용됩니다.

#### <a name="adals-homeaccountid"></a>아달의 홈어카운트Id

ADAL 2.7.x는 `homeAccountId` 이 `ADUserInformation` 속성을 통해 결과의 개체를 반환합니다.

```objc
/*! Unique AAD account identifier across tenants based on user's home OID/home tenantId. */
@property (readonly) NSString *homeAccountId;
```

`homeAccountId`ADAL에서 MSAL과 `identifier` 동일합니다. `accountForIdentifier:error:` 이 식별자를 저장하여 MSAL에서 API를 사용한 계정 조회에 사용할 수 있습니다.

#### <a name="adals-userid"></a>아달의`userId`

사용할 `homeAccountId` 수 없거나 표시 가능한 식별자만 있는 경우 ADAL's를 `userId` 사용하여 MSAL에서 계정을 조회할 수 있습니다.

MSAL에서 먼저 또는 `username` `identifier`에 의해 계정을 조회합니다. 쿼리가 `identifier` 있는 경우 항상 쿼리에 사용하고 `username` 대체로만 사용합니다. 계정이 발견되면 `acquireTokenSilent` 통화에서 계정을 사용합니다.

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



MSAL 지원 계정 조회 API:

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

이 섹션에서는 MSAL과 ADAL 2.x-2.6.6 간의 SSO 차이점을 다룹니다.

이전 ADAL 버전은 기본적으로 MSAL 캐시 형식을 지원하지 않습니다. 그러나 ADAL에서 MSAL로 원활하게 마이그레이션할 수 있도록 MSAL은 사용자 자격 증명을 다시 표시하지 않고 이전 ADAL 캐시 형식을 읽을 수 있습니다.

`homeAccountId` 이전 ADAL 버전에서는 사용할 수 없으므로 `username`다음을 사용하여 계정을 찾아야 합니다.

```objc
/*!
 Returns account for for the given username (received from an account object returned in a previous acquireToken call or ADAL)

 @param  username    The displayable value in UserPrincipleName(UPN) format
 @param  error       The error that occurred trying to get the accounts, if any.  If you're not interested in the specific error pass in nil.
 */
- (MSALAccount *)accountForUsername:(NSString *)username
                              error:(NSError * __autoreleasing *)error;
```

예를 들어:

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



또는 ADAL의 계정 정보도 읽을 수 있는 모든 계정을 읽을 수 있습니다.

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
