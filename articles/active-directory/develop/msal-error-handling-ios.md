---
title: iOS/macOS용 MSAL에서 오류 및 예외 처리
titleSuffix: Microsoft identity platform
description: IOS/macOS 응용 프로그램에 대 한 오류 및 예외, 조건부 액세스 클레임 문제 및 MSAL의 재시도를 처리 하는 방법에 대해 알아봅니다.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 11/26/2020
ms.author: marsma
ms.reviewer: saeeda, oldalton
ms.custom: aaddev
ms.openlocfilehash: f7f2abadb7586e1b19168eb46a54bad53caa05cc
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98761109"
---
# <a name="handle-errors-and-exceptions-in-msal-for-iosmacos"></a>iOS/macOS용 MSAL에서 오류 및 예외 처리

[!INCLUDE [Active directory error handling introduction](../../../includes/active-directory-develop-error-handling-introduction.md)]

## <a name="error-handling-in-msal-for-iosmacos"></a>IOS/macOS에 대 한 MSAL의 오류 처리

iOS 및 macOS 오류에 대한 MSAL의 전체 목록은 [MSALError 열거형](https://github.com/AzureAD/microsoft-authentication-library-for-objc/blob/master/MSAL/src/public/MSALError.h#L128)에 나와 있습니다.

오류를 생성한 모든 MSAL이 `MSALErrorDomain` 도메인과 함께 반환됩니다.

시스템 오류의 경우 MSAL은 시스템 API에서 원래 `NSError`를 반환합니다. 예를 들어 네트워크 연결 부족으로 인해 토큰 획득에 실패하는 경우 MSAL은 `NSURLErrorDomain` 도메인 및 `NSURLErrorNotConnectedToInternet` 코드와 함께 오류를 반환합니다.

클라이언트 쪽에서 적어도 다음과 같은 두 개의 MSAL 오류를 처리하는 것이 좋습니다.

- `MSALErrorInteractionRequired`: 사용자는 대화형 요청을 수행해야 합니다. 인증 세션 만료나 추가 인증 요구 사항이 필요한 경우처럼 이 오류가 발생할 수 있는 여러 상황이 있습니다. MSAL 대화형 토큰 획득 API를 호출하여 복구합니다. 

- `MSALErrorServerDeclinedScopes`: 일부 또는 모든 범위가 거부되었습니다. 권한이 부여된 범위에서만 계속할지 또는 로그인 프로세스를 중지할지 결정합니다.

> [!NOTE]
> `MSALInternalError` 열거형은 참조 및 디버깅에만 사용해야 합니다. 런타임에 이러한 오류를 자동으로 처리하지 마세요. 앱에서 `MSALInternalError`에 속하는 오류가 발생할 경우 발생한 상황을 설명하는 일반 사용자 연결 메시지를 표시할 수 있습니다.

예를 들어 `MSALInternalErrorBrokerResponseNotReceived`는 사용자가 인증을 완료하지 않았고 앱에 수동으로 반환되었음을 의미합니다. 이 경우, 애플리케이션은 인증이 완료되지 않은 것을 설명하는 일반 오류 메시지를 표시하고 인증을 다시 시도할 것을 제안합니다.

다음 목표 C 샘플 코드는 몇 가지 일반적인 오류 조건을 처리 하기 위한 모범 사례를 보여 줍니다.

```objc
    MSALInteractiveTokenParameters *interactiveParameters = ...;
    MSALSilentTokenParameters *silentParameters = ...;
    
    MSALCompletionBlock completionBlock;
    __block __weak MSALCompletionBlock weakCompletionBlock;
    
    weakCompletionBlock = completionBlock = ^(MSALResult *result, NSError *error)
    {
        if (!error)
        {
            // Use result.accessToken
            NSString *accessToken = result.accessToken;
            return;
        }
        
        if ([error.domain isEqualToString:MSALErrorDomain])
        {
            switch (error.code)
            {
                case MSALErrorInteractionRequired:
                {
                    // Interactive auth will be required
                    [application acquireTokenWithParameters:interactiveParameters
                                            completionBlock:weakCompletionBlock];
                    
                    break;
                }
                    
                case MSALErrorServerDeclinedScopes:
                {
                    // These are list of granted and declined scopes.
                    NSArray *grantedScopes = error.userInfo[MSALGrantedScopesKey];
                    NSArray *declinedScopes = error.userInfo[MSALDeclinedScopesKey];
                    
                    // To continue acquiring token for granted scopes only, do the following
                    silentParameters.scopes = grantedScopes;
                    [application acquireTokenSilentWithParameters:silentParameters
                                                  completionBlock:weakCompletionBlock];
                    
                    // Otherwise, instead, handle error fittingly to the application context
                    break;
                }
                    
                case MSALErrorServerProtectionPoliciesRequired:
                {
                    // Integrate the Intune SDK and call the
                    // remediateComplianceForIdentity:silent: API.
                    // Handle this error only if you integrated Intune SDK.
                    // See more info here: https://aka.ms/intuneMAMSDK
                    
                    break;
                }
                    
                case MSALErrorUserCanceled:
                {
                    // The user cancelled the web auth session.
                    // You may want to ask the user to try again.
                    // Handling of this error is optional.
                    
                    break;
                }
                    
                case MSALErrorInternal:
                {
                    // Log the error, then inspect the MSALInternalErrorCodeKey
                    // in the userInfo dictionary.
                    // Display generic error message to the end user
                    // More detailed information about the specific error
                    // under MSALInternalErrorCodeKey can be found in MSALInternalError enum.
                    NSLog(@"Failed with error %@", error);
                    
                    break;
                }
                    
                default:
                    NSLog(@"Failed with unknown MSAL error %@", error);
                    
                    break;
            }
            
            return;
        }
        
        // Handle no internet connection.
        if ([error.domain isEqualToString:NSURLErrorDomain] && error.code == NSURLErrorNotConnectedToInternet)
        {
            NSLog(@"No internet connection.");
            return;
        }
        
        // Other errors may require trying again later,
        // or reporting authentication problems to the user.
        NSLog(@"Failed with error %@", error);
    };
    
    // Acquire token silently
    [application acquireTokenSilentWithParameters:silentParameters
                                  completionBlock:completionBlock];

     // or acquire it interactively.
     [application acquireTokenWithParameters:interactiveParameters
                             completionBlock:completionBlock];
```

```swift
    let interactiveParameters: MSALInteractiveTokenParameters = ...
    let silentParameters: MSALSilentTokenParameters = ...
            
    var completionBlock: MSALCompletionBlock!
    completionBlock = { (result: MSALResult?, error: Error?) in
                
        if let result = result
        {
            // Use result.accessToken
            let accessToken = result.accessToken
            return
        }

        guard let error = error as NSError? else { return }

        if error.domain == MSALErrorDomain, let errorCode = MSALError(rawValue: error.code)
        {
            switch errorCode
            {
                case .interactionRequired:
                    // Interactive auth will be required
                    application.acquireToken(with: interactiveParameters, completionBlock: completionBlock)

                case .serverDeclinedScopes:
                    let grantedScopes = error.userInfo[MSALGrantedScopesKey]
                    let declinedScopes = error.userInfo[MSALDeclinedScopesKey]

                    if let scopes = grantedScopes as? [String] {
                        silentParameters.scopes = scopes
                        application.acquireTokenSilent(with: silentParameters, completionBlock: completionBlock)
                    }
                        
                    case .serverProtectionPoliciesRequired:
                        // Integrate the Intune SDK and call the
                        // remediateComplianceForIdentity:silent: API.
                        // Handle this error only if you integrated Intune SDK.
                        // See more info here: https://aka.ms/intuneMAMSDK
                        break
                        
                    case .userCanceled:
                       // The user cancelled the web auth session.
                       // You may want to ask the user to try again.
                       // Handling of this error is optional.
                       break
                        
                    case .internal:
                        // Log the error, then inspect the MSALInternalErrorCodeKey
                        // in the userInfo dictionary.
                        // Display generic error message to the end user
                        // More detailed information about the specific error
                        // under MSALInternalErrorCodeKey can be found in MSALInternalError enum.
                        print("Failed with error \(error)");
                        
                    default:
                        print("Failed with unknown MSAL error \(error)")
            }
        }
                
        // Handle no internet connection.
        if error.domain == NSURLErrorDomain && error.code == NSURLErrorNotConnectedToInternet
        {
            print("No internet connection.")
            return
        }
                
        // Other errors may require trying again later,
        // or reporting authentication problems to the user.
        print("Failed with error \(error)");    
    }
   
    // Acquire token silently
    application.acquireToken(with: interactiveParameters, completionBlock: completionBlock)
 
    // or acquire it interactively.
    application.acquireTokenSilent(with: silentParameters, completionBlock: completionBlock)
```

---

[!INCLUDE [Active directory error handling claims challenges](../../../includes/active-directory-develop-error-handling-claims-challenges.md)]

iOS 및 macOS용 MSAL을 사용하여 대화형 및 자동 토큰 취득 시나리오에서 특정 클레임을 요청할 수 있습니다.

사용자 지정 클레임을 요청하려면 `MSALSilentTokenParameters` 또는 `MSALInteractiveTokenParameters`에서 `claimsRequest`를 지정합니다.

자세한 내용은 [iOS 및 macOS용 MSAL을 사용하여 사용자 지정 클레임 요청](request-custom-claims.md)을 참조하세요.

[!INCLUDE [Active directory error handling retries](../../../includes/active-directory-develop-error-handling-retries.md)]

## <a name="next-steps"></a>다음 단계

[IOS/macOS에 대 한 MSAL에서 로깅을](msal-logging-ios.md) 사용 하도록 설정 하 여 문제를 진단 하 고 디버그할 수 있습니다.
