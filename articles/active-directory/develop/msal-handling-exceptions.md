---
title: Errors and exceptions (MSAL)
titleSuffix: Microsoft identity platform
description: Learn how to handle errors and exceptions, Conditional Access, and claims challenges in MSAL applications.
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/22/2019
ms.author: twhitney
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 753296596982279a14ff2775b0e129048dbe8369
ms.sourcegitcommit: 8cf199fbb3d7f36478a54700740eb2e9edb823e8
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/25/2019
ms.locfileid: "74482074"
---
# <a name="handle-msal-exceptions-and-errors"></a>Handle MSAL exceptions and errors

This article gives an overview of the different types of errors and recommendations for handling common sign-in errors.

## <a name="msal-error-handling-basics"></a>MSAL error handling basics

Exceptions in Microsoft Authentication Library (MSAL) are intended for app developers to troubleshoot--not for displaying to end users. 예외 메시지는 지역화되지 않았습니다.

예외 및 오류를 처리할 때 예외 유형 자체와 오류 코드를 사용하여 예외를 구별할 수 있습니다.  오류 코드 목록은 [인증 및 권한 부여 오류 코드](reference-aadsts-error-codes.md)를 참조하세요.

During the sign-in experience, you may encounter errors about consents, Conditional Access (MFA, Device Management, Location-based restrictions), token issuance and redemption, and user properties.

See the following section that matches the language you are using for more details about error handling for your app.

## <a name="nettabdotnet"></a>[.NET](#tab/dotnet)

When processing .NET exceptions, you can use the exception type itself and the `ErrorCode` member to distinguish between exceptions. `ErrorCode` values are constants of type [MsalError](/dotnet/api/microsoft.identity.client.msalerror?view=azure-dotnet).

You can also have a look at the fields of [MsalClientException](/dotnet/api/microsoft.identity.client.msalexception?view=azure-dotnet), [MsalServiceException](/dotnet/api/microsoft.identity.client.msalserviceexception?view=azure-dotnet), and [MsalUIRequiredException](/dotnet/api/microsoft.identity.client.msaluirequiredexception?view=azure-dotnet).

If [MsalServiceException](/dotnet/api/microsoft.identity.client.msalserviceexception?view=azure-dotnet) is thrown, try [Authentication and authorization error codes](reference-aadsts-error-codes.md) to see if the code is listed there.

### <a name="common-net-exceptions"></a>Common .NET exceptions

Here are the common exceptions that might be thrown and some possible mitigations:  

| 예외 | 오류 코드 | 해결 방법|
| --- | --- | --- |
| [MsalUiRequiredException](/dotnet/api/microsoft.identity.client.msaluirequiredexception?view=azure-dotnet) | AADSTS65001: The user or administrator has not consented to use the application with ID '{appId}' named '{appName}'. 이 사용자 및 리소스에 대한 대화형 권한 부여 요청을 보냅니다.| 먼저 사용자 동의를 받아야 합니다. If you aren't using .NET Core (which doesn't have any Web UI), call (once only) `AcquireTokeninteractive`. If you are using .NET core or don't want to do an `AcquireTokenInteractive`, the user can navigate to a URL to give consent: https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id={clientId}&response_type=code&scope=user.read. to call `AcquireTokenInteractive`: `app.AcquireTokenInteractive(scopes).WithAccount(account).WithClaims(ex.Claims).ExecuteAsync();`|
| [MsalUiRequiredException](/dotnet/api/microsoft.identity.client.msaluirequiredexception?view=azure-dotnet) | AADSTS50079: The user is required to use multi-factor authentication (MFA).| There is no mitigation. If MFA is configured for your tenant and Azure Active Directory (AAD) decides to enforce it, you need to fallback to an interactive flow such as `AcquireTokenInteractive` or `AcquireTokenByDeviceCode`.|
| [MsalServiceException](/dotnet/api/microsoft.identity.client.msalserviceexception?view=azure-dotnet) |AADSTS90010: The grant type isn't supported over the */common* or */consumers* endpoints. */organizations* 또는 테넌트 특정 엔드포인트를 사용합니다. */common*을 사용했습니다.| Azure AD의 메시지에서 설명한 대로 인증 기관에 테넌트 또는 */organizations*가 있어야 합니다.|
| [MsalServiceException](/dotnet/api/microsoft.identity.client.msalserviceexception?view=azure-dotnet) | AADSTS70002: The request body must contain the following parameter: `client_secret or client_assertion`.| This exception can be thrown if your application was not registered as a public client application in Azure AD. In the Azure portal, edit the manifest for your application and set `allowPublicClient` to `true`. |
| [MsalClientException](/dotnet/api/microsoft.identity.client.msalclientexception?view=azure-dotnet)| `unknown_user Message`: Could not identify logged in user| The library was unable to query the current Windows logged-in user or this user isn't AD or AAD joined (work-place joined users aren't supported). Mitigation 1: on UWP, check that the application has the following capabilities: Enterprise Authentication, Private Networks (Client and Server), User Account Information. Mitigation 2: Implement your own logic to fetch the username (for example, john@contoso.com) and use the `AcquireTokenByIntegratedWindowsAuth` form that takes in the username.|
| [MsalClientException](/dotnet/api/microsoft.identity.client.msalclientexception?view=azure-dotnet)|integrated_windows_auth_not_supported_managed_user| 이 메서드는 AD(Active Directory)에서 공개되는 프로토콜을 사용합니다. 사용자("관리" 사용자)를 AD 지원 없이 Azure Active Directory에서 만든 경우 이 메서드는 실패합니다. AD에서 만들어지고 AAD에서 지원하는 사용자("페더레이션" 사용자)는 이 비대화형 인증 메서드를 활용할 수 있습니다. Mitigation: Use interactive authentication.|

### `MsalUiRequiredException`

One of common status codes returned from MSAL.NET when calling `AcquireTokenSilent()` is `MsalError.InvalidGrantError`. This status code means that the application should call the authentication library again, but in interactive mode (AcquireTokenInteractive or AcquireTokenByDeviceCodeFlow for public client applications, and do a challenge in Web apps). This is because additional user interaction is required before authentication token can be issued.

Most of the time when `AcquireTokenSilent` fails, it is because the token cache doesn't have tokens matching your request. Access tokens expire in 1 hour, and `AcquireTokenSilent` will try to fetch a new one based on a refresh token (in OAuth2 terms, this is the "Refresh Token' flow). This flow can also fail for various reasons, for example if a tenant admin configures more stringent login policies. 

The interaction aims at having the user do an action. Some of those conditions are easy for users to resolve (for example, accept Terms of Use with a single click), and some can't be resolved with the current configuration (for example, the machine in question needs to connect to a specific corporate network). Some help the user setting-up Multi-factor authentication, or install Microsoft Authenticator on their device.

### <a name="msaluirequiredexception-classification-enumeration"></a>`MsalUiRequiredException` classification enumeration

MSAL exposes a `Classification` field, which you can read to provide a better user experience, for example to tell the user that their password expired or that they'll need to provide consent to use some resources. The supported values are part of the `UiRequiredExceptionClassification` enum:

| 분류    | 의미           | Recommended handling |
|-------------------|-------------------|----------------------|
| BasicAction | Condition can be resolved by user interaction during the interactive authentication flow. | Call AcquireTokenInteractively(). |
| AdditionalAction | Condition can be resolved by additional remedial interaction with the system, outside of the interactive authentication flow. | Call AcquireTokenInteractively() to show a message that explains the remedial action. Calling application may choose to hide flows that require additional_action if the user is unlikely to complete the remedial action. |
| MessageOnly      | Condition can't be resolved at this time. Launching interactive authentication flow will show a message explaining the condition. | Call AcquireTokenInteractively() to show a message that explains the condition. AcquireTokenInteractively() will return UserCanceled error after the user reads the message and closes the window. Calling application may choose to hide flows that result in message_only if the user is unlikely to benefit from the message.|
| ConsentRequired  | User consent is missing, or has been revoked. | Call AcquireTokenInteractively() for user to give consent. |
| UserPasswordExpired | User's password has expired. | Call AcquireTokenInteractively() so that user can reset their password. |
| PromptNeverFailed| Interactive Authentication was called with the parameter prompt=never, forcing MSAL to rely on browser cookies and not to display the browser. This has failed. | Call AcquireTokenInteractively() without Prompt.None |
| AcquireTokenSilentFailed | MSAL SDK doesn't have enough information to fetch a token from the cache. This can be because no tokens are in the cache or an account wasn't found. The error message has more details.  | Call AcquireTokenInteractively(). |
| 없음    | No further details are provided. Condition may be resolved by user interaction during the interactive authentication flow. | Call AcquireTokenInteractively(). |

## <a name="net-code-example"></a>.NET 코드 예제

```csharp
AuthenticationResult res;
try
{
 res = await application.AcquireTokenSilent(scopes, account)
        .ExecuteAsync();
}
catch (MsalUiRequiredException ex) when (ex.ErrorCode == MsalError.InvalidGrantError)
{
 switch (ex.Classification)
 {
  case UiRequiredExceptionClassification.None:
   break;
  case UiRequiredExceptionClassification.MessageOnly:
  // You might want to call AcquireTokenInteractive(). Azure AD will show a message
  // that explains the condition. AcquireTokenInteractively() will return UserCanceled error
  // after the user reads the message and closes the window. The calling application may choose
  // to hide features or data that result in message_only if the user is unlikely to benefit 
  // from the message
  try
  {
      res = await application.AcquireTokenInteractive(scopes).ExecuteAsync();
  }
  catch (MsalClientException ex2) when (ex2.ErrorCode == MsalError.AuthenticationCanceledError)
  {
   // Do nothing. The user has seen the message
  }
  break;

  case UiRequiredExceptionClassification.BasicAction:
  // Call AcquireTokenInteractive() so that the user can, for instance accept terms
  // and conditions

  case UiRequiredExceptionClassification.AdditionalAction:
  // You might want to call AcquireTokenInteractive() to show a message that explains the remedial action. 
  // The calling application may choose to hide flows that require additional_action if the user 
  // is unlikely to complete the remedial action (even if this means a degraded experience)

  case UiRequiredExceptionClassification.ConsentRequired:
  // Call AcquireTokenInteractive() for user to give consent.
  
  case UiRequiredExceptionClassification.UserPasswordExpired:
  // Call AcquireTokenInteractive() so that user can reset their password
  
  case UiRequiredExceptionClassification.PromptNeverFailed:
  // You used WithPrompt(Prompt.Never) and this failed
  
  case UiRequiredExceptionClassification.AcquireTokenSilentFailed:
  default:
  // May be resolved by user interaction during the interactive authentication flow.
  res = await application.AcquireTokenInteractive(scopes)
                         .ExecuteAsync(); break;
 }
}
```

## <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

MSAL.js provides error objects that abstract and classify the different types of common errors. It also provides interface to access specific details of the errors such as error messages to handle them appropriately.

### <a name="error-object"></a>Error 개체

```javascript
export class AuthError extends Error {
    // This is a short code describing the error
    errorCode: string;
    // This is a descriptive string of the error,
    // and may also contain the mitigation strategy
    errorMessage: string;
    // Name of the error class
    this.name = "AuthError";
}
```

오류 클래스를 확장하면 다음 속성에 액세스할 수 있습니다.
- `AuthError.message`:  Same as the `errorMessage`.
- `AuthError.stack`: Stack trace for thrown errors.

### <a name="error-types"></a>Error types

사용할 수 있는 오류 유형은 다음과 같습니다.

- `AuthError`: Base error class for the MSAL.js library, also used for unexpected errors.

- `ClientAuthError`: Error class, which denotes an issue with Client authentication. 라이브러리에서 발생하는 대부분의 오류는 ClientAuthErrors입니다. These errors result from things like calling a login method when login is already in progress, the user cancels the login, and so on.

- `ClientConfigurationError`: Error class, extends `ClientAuthError` thrown before requests are made when the given user config parameters are malformed or missing.

- `ServerError`: Error class, represents the error strings sent by the authentication server. 잘못된 요청 형식 또는 매개 변수와 같은 오류 또는 서버에서 사용자를 인증하거나 사용자에게 권한을 부여하지 못하도록 하는 다른 오류일 수 있습니다.

- `InteractionRequiredAuthError`: Error class, extends `ServerError` to represent server errors, which require an interactive call. This error is thrown by `acquireTokenSilent` if the user is required to interact with the server to provide credentials or consent for authentication/authorization. Error codes include `"interaction_required"`, `"login_required"`, and `"consent_required"`.

For error handling in authentication flows with redirect methods (`loginRedirect`, `acquireTokenRedirect`), you'll need to register the callback, which is called with success or failure after the redirect using `handleRedirectCallback()` method as follows:

```javascript
function authCallback(error, response) {
    //handle redirect response
}

var myMSALObj = new Msal.UserAgentApplication(msalConfig);

// Register Callbacks for redirect flow
myMSALObj.handleRedirectCallback(authCallback);
myMSALObj.acquireTokenRedirect(request);
```

팝업 환경에 대한 메서드(`loginPopup`, `acquireTokenPopup`)는 약속을 반환하므로 약속 패턴(.then 및 .catch)을 사용하여 다음과 같이 처리할 수 있습니다.

```javascript
myMSALObj.acquireTokenPopup(request).then(
    function (response) {
        // success response
    }).catch(function (error) {
        console.log(error);
    });
```

### <a name="errors-that-require-interaction"></a>Errors that require interaction

An error is returned when you attempt to use a non-interactive method of acquiring a token such as `acquireTokenSilent`, but MSAL couldn't do it silently.

가능한 원인은 다음과 같습니다.

- 로그인해야 합니다.
- 동의해야 합니다.
- 다단계 인증 환경을 거쳐야 합니다.

업데이트 관리는 `acquireTokenPopup` 또는 `acquireTokenRedirect`와 같은 대화형 메서드를 호출하는 것입니다.

```javascript
// Request for Access Token
myMSALObj.acquireTokenSilent(request).then(function (response) {
    // call API
}).catch( function (error) {
    // call acquireTokenPopup in case of acquireTokenSilent failure
    // due to consent or interaction required
    if (error.errorCode === "consent_required"
    || error.errorCode === "interaction_required"
    || error.errorCode === "login_required") {
        myMSALObj.acquireTokenPopup(request).then(
            function (response) {
                // call API
            }).catch(function (error) {
                console.log(error);
            });
    }
});
```

## <a name="pythontabpython"></a>[Python](#tab/python)

In MSAL for Python, most errors are conveyed as a return value from the API call. The error is represented as a dictionary containing the JSON response from the Microsoft identity platform.

* A successful response contains the `"access_token"` key. The format of the response is defined by the OAuth2 protocol. For more information, see [5.1 Successful Response](https://tools.ietf.org/html/rfc6749#section-5.1)
* An error response contains `"error"` and usually `"error_description"`. The format of the response is defined by the OAuth2 protocol. For more information, see [5.2 Error Response](https://tools.ietf.org/html/rfc6749#section-5.2)

When an error is returned, the `"error_description"` key contains a human-readable message; which in turn typically contains a Microsoft identity platform error code. For details about the various error codes, see [Authentication and authorization error codes](https://docs.microsoft.com/azure/active-directory/develop/reference-aadsts-error-codes).

In MSAL for Python, exceptions are rare because most errors are handled by returning an error value. The `ValueError` exception is only thrown when there is an issue with how you are attempting to use the library--such as when API parameter(s) are malformed.

## <a name="javatabjava"></a>[Java](#tab/java)

In MSAL for Java, there are three types of exceptions: `MsalClientException`, `MsalServiceException`, and `MsalInteractionRequiredException`; all which inherit from `MsalException`.

- `MsalClientException` is thrown when an error occurs that is local to the library or device.
- `MsalServiceException` is thrown when the secure token service (STS) returns an error response or another networking error occurs.
- `MsalInteractionRequiredException` is thrown when UI interaction is required for authentication to succeed.

### <a name="msalserviceexception"></a>MsalServiceException

`MsalServiceException` exposes HTTP headers returned in the requests to the STS. Access them via `MsalServiceException.headers()`

### <a name="msalinteractionrequiredexception"></a>MsalInteractionRequiredException

One of common status codes returned from MSAL for Java when calling `AcquireTokenSilently()` is `InvalidGrantError`. This means that additional user interaction is required before an authentication token can be issued. Your application should call the authentication library again, but in interactive mode by sending `AuthorizationCodeParameters` or `DeviceCodeParameters` for public client applications.

Most of the time when `AcquireTokenSilently` fails, it's because the token cache doesn't have a token matching your request. Access tokens expire in one hour, and `AcquireTokenSilently` will try to get a new one based on a refresh token. In OAuth2 terms, this is the Refresh Token flow. This flow can also fail for various reasons such as when a tenant admin configures more stringent login policies.

Some conditions that result in this error are easy for users to resolve. For example, they may need to accept Terms of Use. Or perhaps the request can't be fulfilled with the current configuration because the machine needs to connect to a specific corporate network.

MSAL exposes a `reason` field, which you can use to provide a better user experience. For example, the `reason` field may lead you to tell the user that their password expired or that they'll need to provide consent to use some resources. The supported values are part of the  `InteractionRequiredExceptionReason` enum:

| 이유 | 의미 | Recommended Handling |
|---------|-----------|-----------------------------|
| `BasicAction` | Condition can be resolved by user interaction during the interactive authentication flow | Call `acquireToken` with interactive parameters |
| `AdditionalAction` | Condition can be resolved by additional remedial interaction with the system outside of the interactive authentication flow. | Call `acquireToken` with interactive parameters to show a message that explains the remedial action to take. The calling app may choose to hide flows that require additional action if the user is unlikely to complete the remedial action. |
| `MessageOnly` | Condition can't be resolved at this time. Launch interactive authentication flow to show a message explaining the condition. | Call `acquireToken` with interactive parameters to show a message that explains the condition. `acquireToken` will return the `UserCanceled` error after the user reads the message and closes the window. The app may choose to hide flows that result in message if the user is unlikely to benefit from the message. |
| `ConsentRequired`| User consent is missing, or has been revoked. |Call `acquireToken` with interactive parameters so that the user can give consent. |
| `UserPasswordExpired` | User's password has expired. | Call `acquireToken` with interactive parameter so the user can reset their password |
| `None` |  Further details are provided. The condition may be resolved by user interaction during the interactive authentication flow. | Call `acquireToken` with interactive parameters |

### <a name="code-example"></a>코드 예제

```java
        IAuthenticationResult result;
        try {
            PublicClientApplication application = PublicClientApplication
                    .builder("clientId")
                    .b2cAuthority("authority")
                    .build();

            SilentParameters parameters = SilentParameters
                    .builder(Collections.singleton("scope"))
                    .build();

            result = application.acquireTokenSilently(parameters).join();
        }
        catch (Exception ex){
            if(ex instanceof MsalInteractionRequiredException){
                // AcquireToken by either AuthorizationCodeParameters or DeviceCodeParameters
            } else{
                // Log and handle exception accordingly
            }
        }
```

## <a name="iosmacostabiosmacos"></a>[iOS/macOS](#tab/iosmacos)

The complete list of MSAL for iOS and macOS errors is listed in [MSALError enum](https://github.com/AzureAD/microsoft-authentication-library-for-objc/blob/master/MSAL/src/public/MSALError.h#L128).

All MSAL produced errors are returned with `MSALErrorDomain` domain.

For system errors, MSAL returns the original `NSError` from the system API. For example, if token acquisition fails because of a lack of network connectivity, MSAL returns an error with the `NSURLErrorDomain` domain and `NSURLErrorNotConnectedToInternet` code.

We recommend that you handle at least the following two MSAL errors on the client side:

- `MSALErrorInteractionRequired`: The user must do an interactive request. There are many conditions that can lead to this error such as an expired authentication session or the need for additional authentication requirements. Call the MSAL interactive token acquisition API to recover. 

- `MSALErrorServerDeclinedScopes`: Some or all scopes were declined. Decide whether to continue with only the granted scopes, or stop the sign-in process.

> [!NOTE]
> The `MSALInternalError` enum should only be used for reference and debugging. Do not try to automatically handle these errors at runtime. If your app encounters any of the errors that fall under `MSALInternalError`, you may want to show a generic user facing message explaining what happened.

For example, `MSALInternalErrorBrokerResponseNotReceived` means that user didn't complete authentication and manually returned to the app. In this case, your app should show a generic error message explaining that authentication didn't complete and suggest that they try to authenticate again.

The following Objective-C sample code demonstrates best practices for handling some common error conditions:

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

## <a name="conditional-access-and-claims-challenges"></a>조건부 액세스 및 클레임 챌린지

When getting tokens silently, your application may receive errors when a [Conditional Access claims challenge](conditional-access-dev-guide.md) such as MFA policy is required by an API you're trying to access.

The pattern for handling this error is to interactively acquire a token using MSAL. 토큰을 대화형으로 획득하면 사용자에게 메시지가 표시되어 필요한 조건부 액세스 정책을 충족시킬 수 있는 기회가 제공됩니다.

조건부 액세스가 필요한 API를 호출하는 경우 클레임 챌린지를 API의 오류로 받을 수 있습니다. For instance if the Conditional Access policy is to have a managed device (Intune) the error will be something like [AADSTS53000: Your device is required to be managed to access this resource](reference-aadsts-error-codes.md) or something similar. 이 경우 토큰 획득 호출에서 클레임을 전달하여 사용자에게 적절한 정책을 충족시키라는 메시지가 표시되도록 할 수 있습니다.

### <a name="net"></a>.NET

MSAL.NET에서 조건부 액세스가 필요한 API를 호출하는 경우 애플리케이션에서 클레임 챌린지 예외를 처리해야 합니다. 이 예외는 [Claims](/dotnet/api/microsoft.identity.client.msalserviceexception.claims?view=azure-dotnet) 속성이 비어 있지 않은 [MsalServiceException](/dotnet/api/microsoft.identity.client.msalserviceexception?view=azure-dotnet)으로 표시됩니다.

To handle the claim challenge, you'll need to use the `.WithClaim()` method of the `PublicClientApplicationBuilder` class.

### <a name="javascript"></a>JavaScript

When getting tokens silently (using `acquireTokenSilent`) using MSAL.js, your application may receive errors when a [Conditional Access claims challenge](conditional-access-dev-guide.md) such as MFA policy is required by an API you're trying to access.

이 오류를 처리하는 패턴은 다음 예제와 같이 MSAL.js에서 토큰(예: `acquireTokenPopup` 또는 `acquireTokenRedirect`)을 획득하기 위해 대화형 호출을 수행하는 것입니다.

```javascript
myMSALObj.acquireTokenSilent(accessTokenRequest).then(function (accessTokenResponse) {
    // call API
}).catch( function (error) {
    if (error instanceof InteractionRequiredAuthError) {
        // Extract claims from error message
        accessTokenRequest.claimsRequest = extractClaims(error.errorMessage);
        // call acquireTokenPopup in case of InteractionRequiredAuthError failure
        myMSALObj.acquireTokenPopup(accessTokenRequest).then(function (accessTokenResponse) {
            // call API
        }).catch(function (error) {
            console.log(error);
        });
    }
});
```

토큰을 대화형으로 획득하면 사용자에게 메시지가 표시되어 필요한 조건부 액세스 정책을 충족시킬 수 있는 기회가 제공됩니다.

조건부 액세스가 필요한 API를 호출할 때 클레임 챌린지를 API의 오류로 받을 수 있습니다. In this case, you can pass the claims returned in the error to the `claimsRequest` field of the `AuthenticationParameters.ts` class to satisfy the appropriate policy. 

See [Requesting Additional Claims](active-directory-optional-claims.md) for more detail.

### <a name="msal-for-ios-and-macos"></a>iOS 및 macOS용 MSAL

MSAL for iOS and macOS allows you to request specific claims in both interactive and silent token acquisition scenarios.

To request custom claims, specify `claimsRequest` in `MSALSilentTokenParameters` or `MSALInteractiveTokenParameters`.

See [Request custom claims using MSAL for iOS and macOS](request-custom-claims.md) for more info.

## <a name="retrying-after-errors-and-exceptions"></a>오류 및 예외 후 다시 시도

You're expected to implement you own retry policies when calling MSAL. MSAL makes HTTP calls to the AAD service, and occasional failures can occur, for example the network can go down or the server is overloaded.  

### <a name="http-error-codes-500-600"></a>500-600 HTTP 오류 코드

MSAL.NET은 500-600 HTTP 오류 코드의 오류에 대한 간단한 일회성 다시 시도 메커니즘을 구현합니다.

### <a name="http-429"></a>429 HTTP

When the Service Token Server (STS) is overloaded with too many requests, it returns HTTP error 429 with a hint about how long until you can try again in the `Retry-After` response field.

### <a name="net"></a>.NET

[MsalServiceException](/dotnet/api/microsoft.identity.client.msalserviceexception?view=azure-dotnet) surfaces `System.Net.Http.Headers.HttpResponseHeaders` as a property `namedHeaders`. You can use additional information from the error code to improve the reliability of your applications. In the case described, you can use the `RetryAfterproperty` (of type `RetryConditionHeaderValue`) and compute when to retry.

Here is an example for a daemon application using the client credentials flow. You can adapt this to any of the methods for acquiring a token.

```csharp
do
{
    retry = false;
    TimeSpan? delay;
    try
    {
         result = await publicClientApplication.AcquireTokenForClient(scopes, account)
                                           .ExecuteAsync();
    }
    catch (MsalServiceException serviceException)
    {
         if (ex.ErrorCode == "temporarily_unavailable")
         {
             RetryConditionHeaderValue retryAfter = serviceException.Headers.RetryAfter;
             if (retryAfter.Delta.HasValue)
             {
                 delay = retryAfter.Delta;
             }
             else if (retryAfter.Date.HasValue)
             {
                 delay = retryAfter.Date.Value.Offset;
             }
         }
    }
    …
    if (delay.HasValue)
    {
        Thread.Sleep((int)delay.Value.TotalMilliseconds); // sleep or other
        retry = true;
    }
} while (retry);
```
