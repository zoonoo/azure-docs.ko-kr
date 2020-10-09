---
title: ADAL & MSAL 앱 간 SSO (iOS/macOS)-Microsoft identity platform | Microsoft
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
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "80880753"
---
# <a name="how-to-sso-between-adal-and-msal-apps-on-macos-and-ios"></a>방법: macOS 및 iOS에서 ADAL 및 MSAL 앱 간 SSO

IOS 용 MSAL (Microsoft 인증 라이브러리)은 응용 프로그램 사이에서 [ADAL 목적과](https://github.com/AzureAD/azure-activedirectory-library-for-objc) 함께 SSO 상태를 공유할 수 있습니다. 앱을 자신만의 속도로 마이그레이션할 수 있으므로 사용자가 앱 간 SSO (ADAL 및 MSAL 기반 앱의 혼합)를 활용 하 여 혜택을 받을 수 있습니다.

MSAL SDK를 사용 하 여 앱 간에 SSO를 설정 하는 방법에 대 한 지침은 [여러 앱 간의 자동 sso](single-sign-on-macos-ios.md#silent-sso-between-apps)를 참조 하세요. 이 문서에서는 ADAL과 MSAL 간의 SSO에 대해 집중적으로 설명 합니다.

SSO 구현에 대 한 자세한 내용은 사용 중인 ADAL 버전에 따라 다릅니다.

## <a name="adal-27x"></a>ADAL 2.7. x

이 섹션에서는 MSAL과 ADAL 2.7. x의 SSO 차이점에 대해 설명 합니다.

### <a name="cache-format"></a>캐시 형식

ADAL 2.7. x는 MSAL 캐시 형식을 읽을 수 있습니다. 버전 ADAL 2.7. x를 사용 하 여 앱 간 SSO에 대해 특별 한 작업을 수행할 필요가 없습니다. 그러나 이러한 두 라이브러리가 지 원하는 계정 식별자의 차이점을 알고 있어야 합니다.

### <a name="account-identifier-differences"></a>계정 식별자 차이

MSAL 및 ADAL은 다른 계정 식별자를 사용 합니다. ADAL은 기본 계정 식별자로 UPN을 사용 합니다. MSAL은 개체 ID와 AAD 계정에 대 한 테 넌 트 ID를 기반으로 하는, `sub` 다른 유형의 계정에 대 한 클레임을 기반으로 하는 표시할 수 없는 계정 식별자를 사용 합니다.

`MSALAccount`MSAL 결과에서 개체를 받으면 속성에 계정 식별자가 포함 `identifier` 됩니다. 응용 프로그램은 후속 자동 요청에이 식별자를 사용 해야 합니다.

외에도 `identifier` `MSALAccount` 개체에는 라는 표시할 수 있는 식별자가 포함 되어 있습니다 `username` . ADAL에서로 변환 하는입니다 `userId` . `username` 는 고유 식별자로 간주 되지 않으며 언제 든 지 변경할 수 있으므로 ADAL을 사용 하는 이전 버전과의 호환성을 위해서만 사용 해야 합니다. MSAL은를 사용 하 여 쿼리를 사용 하는 경우 또는를 사용 하 여 캐시 쿼리를 지원 `username` `identifier` `identifier` 합니다.

다음 표에는 ADAL과 MSAL 간의 계정 식별자 차이가 요약 되어 있습니다.

| 계정 식별자                | MSAL                                                         | ADAL 2.7. x      | 이전 ADAL (ADAL 2.7. x 이전) |
| --------------------------------- | ------------------------------------------------------------ | --------------- | ------------------------------ |
| 표시할 식별자            | `username`                                                   | `userId`        | `userId`                       |
| 고유 하 게 표시할 때 고유 하지 않은 식별자 | `identifier`                                                 | `homeAccountId` | 해당 없음                            |
| 알려진 계정 id 없음               | 에서 API를 통해 모든 계정 쿼리 `allAccounts:``MSALPublicClientApplication` | 해당 없음             | 해당 없음                            |

`MSALAccount`다음은 이러한 식별자를 제공 하는 인터페이스입니다.

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

### <a name="sso-from-msal-to-adal"></a>MSAL에서 ADAL으로 SSO

MSAL 앱과 ADAL 앱이 있고 사용자가 먼저 MSAL 기반 앱에 로그인 하는 경우 `username` 개체에서를 저장 `MSALAccount` 하 고 adal 기반 앱에으로 전달 하 여 adal 앱에서 SSO를 가져올 수 있습니다 `userId` . 그런 다음 ADAL은 API를 사용 하 여 계정 정보를 자동으로 찾을 수 있습니다 `acquireTokenSilentWithResource:clientId:redirectUri:userId:completionBlock:` .

### <a name="sso-from-adal-to-msal"></a>ADAL에서 MSAL으로 SSO

MSAL 앱과 ADAL 앱이 있고 사용자가 먼저 ADAL 기반 앱에 로그인 하는 경우 MSAL의 계정 조회에 ADAL 사용자 식별자를 사용할 수 있습니다. ADAL에서 MSAL으로 마이그레이션하는 경우에도 적용 됩니다.

#### <a name="adals-homeaccountid"></a>ADAL의 homeAccountId

ADAL 2.7. x는 `homeAccountId` 이 속성을 통해 결과의 개체에서을 반환 합니다 `ADUserInformation` .

```objc
/*! Unique AAD account identifier across tenants based on user's home OID/home tenantId. */
@property (readonly) NSString *homeAccountId;
```

`homeAccountId` ADAL의는 MSAL의와 동일 `identifier` 합니다. API를 사용 하 여 계정 조회를 위해 MSAL에서 사용 하기 위해이 식별자를 저장할 수 있습니다 `accountForIdentifier:error:` .

#### <a name="adals-userid"></a>ADAL의 `userId`

을 `homeAccountId` 사용할 수 없거나 표시 가능한 식별자만 있는 경우 ADAL을 사용 `userId` 하 여 msal에서 계정을 조회할 수 있습니다.

MSAL에서 먼저 또는을 기준으로 계정을 조회 `username` `identifier` 합니다. 항상 쿼리를 사용 하 `identifier` 고 `username` 대체 (fallback)로만 사용 합니다. 계정이 있는 경우 호출에서 계정을 사용 `acquireTokenSilent` 합니다.

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



MSAL 지원 계정 조회 Api:

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

이 섹션에서는 MSAL과 ADAL 2.6.6 간의 SSO 차이점에 대해 설명 합니다.

이전 ADAL 버전은 기본적으로 MSAL 캐시 형식을 지원 하지 않습니다. 그러나 ADAL에서 MSAL으로의 원활한 마이그레이션을 위해 MSAL은 사용자 자격 증명을 다시 묻지 않고 이전 ADAL 캐시 형식을 읽을 수 있습니다.

`homeAccountId`이전 ADAL 버전에서는 사용할 수 없기 때문에 다음을 사용 하 여 계정을 조회 해야 합니다 `username` .

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



또는 모든 계정을 읽을 수 있으며,이를 통해 ADAL에서 계정 정보를 읽을 수도 있습니다.

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
