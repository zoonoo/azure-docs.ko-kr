---
title: 사용자 지정 클레임 요청(MSAL iOS/macOS) | Azure
titleSuffix: Microsoft identity platform
description: 맞춤 소유권 주장을 요청하는 방법을 알아보세요.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 08/26/2019
ms.author: marsma
ms.custom: aaddev
ms.openlocfilehash: 4974fe3b387683f662d7a7b4f3ccb4935153f07e
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/08/2020
ms.locfileid: "80883099"
---
# <a name="how-to-request-custom-claims-using-msal-for-ios-and-macos"></a>방법: iOS 및 macOS용 MSAL을 사용하여 사용자 지정 소유권 주장 요청

OpenID Connect를 사용하면 UserInfo 엔드포인트 및/또는 ID 토큰에서 개별 클레임의 반환을 선택적으로 요청할 수 있습니다. 클레임 요청은 요청된 클레임 목록을 포함하는 JSON 개체로 표시됩니다. 자세한 내용은 [OpenID 연결 코어 1.0을](https://openid.net/specs/openid-connect-core-1_0-final.html#ClaimsParameter) 참조하십시오.

iOS 및 macOS용 MSAL(Microsoft 인증 라이브러리)을 사용하면 대화형 및 자동 토큰 수집 시나리오에서 특정 클레임을 요청할 수 있습니다. 매개 변수를 `claimsRequest` 통해 수행합니다.

이 필요한 여러 시나리오가 있습니다. 예를 들어:

- 응용 프로그램에 대한 표준 집합 외부에서 클레임을 요청합니다.
- 응용 프로그램의 범위를 사용하여 지정할 수 없는 표준 클레임의 특정 조합을 요청합니다. 예를 들어 누락된 클레임으로 인해 액세스 토큰이 거부되는 경우 응용 프로그램은 MSAL을 사용하여 누락된 클레임을 요청할 수 있습니다.

> [!NOTE]
> MSAL은 클레임 요청이 지정될 때마다 액세스 토큰 캐시를 무시합니다. 각 MSAL API `claimsRequest` 호출에서 항상 동일한 `claimsRequest` 매개 변수를 제공하는 것과 는 달리 추가 클레임이 필요한 경우에만 매개 변수를 제공하는 것이 중요합니다.

`claimsRequest`다음 을 `MSALSilentTokenParameters` 지정할 수 `MSALInteractiveTokenParameters`있습니다.

```objc
/*!
 MSALTokenParameters is the base abstract class for all types of token parameters (silent and interactive).
 */
@interface MSALTokenParameters : NSObject

/*!
 The claims parameter that needs to be sent to authorization or token endpoint.
 If claims parameter is passed in silent flow, access token will be skipped and refresh token will be tried.
 */
@property (nonatomic, nullable) MSALClaimsRequest *claimsRequest;

@end
```
`MSALClaimsRequest`JSON 클레임 요청의 NSString 표현에서 생성할 수 있습니다. 

Objective-C:

```objc
NSError *claimsError = nil;
MSALClaimsRequest *request = [[MSALClaimsRequest alloc] initWithJsonString:@"{\"id_token\":{\"auth_time\":{\"essential\":true},\"acr\":{\"values\":[\"urn:mace:incommon:iap:silver\"]}}}" error:&claimsError];
```

Swift:

```swift
var requestError: NSError? = nil
let request = MSALClaimsRequest(jsonString: "{\"id_token\":{\"auth_time\":{\"essential\":true},\"acr\":{\"values\":[\"urn:mace:incommon:iap:silver\"]}}}",
                                        error: &requestError)
```



또한 추가 특정 클레임을 요청하여 수정할 수도 있습니다.

Objective-C:

```objc
MSALIndividualClaimRequest *individualClaimRequest = [[MSALIndividualClaimRequest alloc] initWithName:@"custom_claim"];
individualClaimRequest.additionalInfo = [MSALIndividualClaimRequestAdditionalInfo new];
individualClaimRequest.additionalInfo.essential = @1;
individualClaimRequest.additionalInfo.value = @"myvalue";
[request requestClaim:individualClaimRequest forTarget:MSALClaimsRequestTargetIdToken error:&claimsError];
```

Swift:

```swift
let individualClaimRequest = MSALIndividualClaimRequest(name: "custom-claim")
let additionalInfo = MSALIndividualClaimRequestAdditionalInfo()
additionalInfo.essential = 1
additionalInfo.value = "myvalue"
individualClaimRequest.additionalInfo = additionalInfo
do {
  try request.requestClaim(individualClaimRequest, for: .idToken)
} catch let error as NSError {
  // handle error here  
}
        
```



`MSALClaimsRequest`그런 다음 토큰 매개 변수에 설정하고 MSAL 토큰 획득 API 중 하나에 제공해야 합니다.

Objective-C:

```objc
MSALPublicClientApplication *application = ...;
MSALWebviewParameters *webParameters = ...;

MSALInteractiveTokenParameters *parameters = [[MSALInteractiveTokenParameters alloc] initWithScopes:@[@"user.read"]
                                                                                  webviewParameters:webParameters];
parameters.claimsRequest = request;
    
[application acquireTokenWithParameters:parameters completionBlock:completionBlock];
```

Swift:

```swift
let application: MSALPublicClientApplication!
let webParameters: MSALWebviewParameters!
        
let parameters = MSALInteractiveTokenParameters(scopes: ["user.read"], webviewParameters: webParameters)
parameters.claimsRequest = request
        
application.acquireToken(with: parameters) { (result: MSALResult?, error: Error?) in            ...

```



## <a name="next-steps"></a>다음 단계

[인증 흐름 및 애플리케이션 시나리오](authentication-flows-app-scenarios.md)에 대해 알아보기
