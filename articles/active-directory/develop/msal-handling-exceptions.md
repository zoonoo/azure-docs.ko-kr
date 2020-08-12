---
title: 오류 및 예외(MSAL)
titleSuffix: Microsoft identity platform
description: MSAL 애플리케이션에서 오류와 예외, 조건부 액세스 및 클레임 챌린지를 처리하는 방법을 알아봅니다.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/18/2020
ms.author: marsma
ms.reviewer: saeeda, jmprieur
ms.custom: aaddev
ms.openlocfilehash: 60c61ff4753413d2241820400dcbc899e925eecc
ms.sourcegitcommit: b8702065338fc1ed81bfed082650b5b58234a702
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/11/2020
ms.locfileid: "88120952"
---
# <a name="handle-msal-exceptions-and-errors"></a>MSAL 예외 및 오류 처리

이 문서에서는 다양한 유형의 오류를 개략적으로 설명하고 일반적인 로그인 오류 처리를 위한 권장 사항을 제공합니다.

## <a name="msal-error-handling-basics"></a>MSAL 오류 처리 기본 사항

MSAL(Microsoft 인증 라이브러리)의 예외는 앱 개발자가 최종 사용자에게 표시하지 않고 문제를 해결하기 위한 것입니다. 예외 메시지는 지역화되지 않았습니다.

예외 및 오류를 처리할 때 예외 유형 자체와 오류 코드를 사용하여 예외를 구별할 수 있습니다.  오류 코드 목록은 [인증 및 권한 부여 오류 코드](reference-aadsts-error-codes.md)를 참조하세요.

로그인 환경에서 동의, 조건부 액세스(MFA, 디바이스 관리, 위치 기반 제한), 토큰 발급 및 상환 및 사용자 속성에 대한 오류가 발생할 수 있습니다.

앱에 대한 오류를 처리하는 방법에 대한 자세한 내용은 사용하는 언어에 해당하는 다음 섹션을 참조하세요.

## <a name="net"></a>[.NET](#tab/dotnet)

.NET 예외를 처리할 때 예외 유형 자체와 `ErrorCode` 멤버를 사용하여 예외를 구별할 수 있습니다. `ErrorCode`의 값은 [MsalError](/dotnet/api/microsoft.identity.client.msalerror?view=azure-dotnet) 형식의 상수입니다.

또한 [MsalClientException](/dotnet/api/microsoft.identity.client.msalexception?view=azure-dotnet), [MsalServiceException](/dotnet/api/microsoft.identity.client.msalserviceexception?view=azure-dotnet) 및 [MsalUIRequiredException](/dotnet/api/microsoft.identity.client.msaluirequiredexception?view=azure-dotnet)의 필드를 살펴볼 수도 있습니다.

[MsalServiceException](/dotnet/api/microsoft.identity.client.msalserviceexception?view=azure-dotnet)이 throw되면 [인증 및 권한 부여 오류 코드](reference-aadsts-error-codes.md)를 확인하여 코드가 해당 목록에 표시되는지 알아봅니다.

### <a name="common-net-exceptions"></a>일반적인 .NET 예외

throw될 수 있는 일반적인 예외와 몇 가지 가능한 완화는 다음과 같습니다.  

| 예외 | 오류 코드 | 완화 방법|
| --- | --- | --- |
| [MsalUiRequiredException](/dotnet/api/microsoft.identity.client.msaluirequiredexception?view=azure-dotnet) | AADSTS65001: 사용자 또는 관리자가 '{appName}'이라는' {appId}' ID의 애플리케이션을 사용하는 데 동의하지 않았습니다. 이 사용자 및 리소스에 대한 대화형 권한 부여 요청을 보냅니다.| 먼저 사용자 동의를 받아야 합니다. 웹 UI가 없는 .NET Core를 사용하지 않는 경우 `AcquireTokeninteractive`를 한 번만 호출합니다. .NET Core를 사용하거나 `AcquireTokenInteractive`를 수행하지 않으려는 경우 사용자는 URL(`https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id={clientId}&response_type=code&scope=user.read`)로 이동하여 동의합니다. `AcquireTokenInteractive`를 호출하려면 `app.AcquireTokenInteractive(scopes).WithAccount(account).WithClaims(ex.Claims).ExecuteAsync();`를 실행합니다.|
| [MsalUiRequiredException](/dotnet/api/microsoft.identity.client.msaluirequiredexception?view=azure-dotnet) | AADSTS50079: 사용자는 [MFA(Multi-Factor Authentication)](../authentication/concept-mfa-howitworks.md)를 사용해야 합니다.| 완화가 없습니다. MFA가 테넌트에 대해 구성되어 있고 AAD(Azure Active Directory)에서 적용하도록 결정한 경우 `AcquireTokenInteractive` 또는 `AcquireTokenByDeviceCode`와 같은 대화형 흐름으로 대체해야 합니다.|
| [MsalServiceException](/dotnet/api/microsoft.identity.client.msalserviceexception?view=azure-dotnet) |AADSTS90010: 권한 부여 유형은 */common* 또는 */consumers* 엔드포인트에서 지원되지 않습니다. */organizations* 또는 테넌트 특정 엔드포인트를 사용합니다. */common*을 사용했습니다.| Azure AD의 메시지에서 설명한 대로 인증 기관에 테넌트 또는 */organizations*가 있어야 합니다.|
| [MsalServiceException](/dotnet/api/microsoft.identity.client.msalserviceexception?view=azure-dotnet) | AADSTS70002: 요청 본문에는 `client_secret or client_assertion` 매개 변수가 있어야 합니다.| Azure AD에서 애플리케이션이 공용 클라이언트 애플리케이션으로 등록되지 않은 경우에 이 예외가 throw될 수 있습니다. Azure Portal에서 애플리케이션에 대한 매니페스트를 편집하고 `allowPublicClient`를 `true`로 설정합니다. |
| [MsalClientException](/dotnet/api/microsoft.identity.client.msalclientexception?view=azure-dotnet)| `unknown_user Message`: 로그인한 사용자를 식별할 수 없음| 라이브러리에서 현재 Windows에 로그인한 사용자를 쿼리할 수 없거나, 이 사용자가 AD 또는 AAD에 조인되어 있지 않습니다(작업 공간에 조인된 사용자는 지원되지 않음). 완화 1: UWP에서 애플리케이션에 엔터프라이즈 인증, 사설망(클라이언트 및 서버), 사용자 계정 정보와 같은 기능이 있는지 확인합니다. 해결 방법 2: 사용자 이름(예: john@contoso.com)을 가져오도록 사용자 고유의 논리를 구현하고 사용자 이름을 사용하는 `AcquireTokenByIntegratedWindowsAuth` 양식을 사용합니다.|
| [MsalClientException](/dotnet/api/microsoft.identity.client.msalclientexception?view=azure-dotnet)|integrated_windows_auth_not_supported_managed_user| 이 메서드는 AD(Active Directory)에서 공개되는 프로토콜을 사용합니다. 사용자("관리" 사용자)를 AD 지원 없이 Azure Active Directory에서 만든 경우 이 메서드는 실패합니다. AD에서 만들어지고 AAD에서 지원하는 사용자("페더레이션" 사용자)는 이 비대화형 인증 메서드를 활용할 수 있습니다. 마이그레이션: 대화형 인증을 사용합니다.|

### `MsalUiRequiredException`

`AcquireTokenSilent()`를 호출할 때 MSAL.NET에서 반환되는 일반적인 상태 코드 중 하나는 `MsalError.InvalidGrantError`입니다. 이 상태 코드는 애플리케이션이 인증 라이브러리를 대화형 모드(퍼블릭 클라이언트 애플리케이션의 경우 AcquireTokenInteractive 또는 AcquireTokenByDeviceCodeFlow, 웹앱에서 챌린지 수행)에서 다시 호출해야 함을 의미합니다. 인증 토큰을 발급하려면 추가 사용자 조작이 필요하기 때문입니다.

`AcquireTokenSilent`가 실패하는 대부분의 경우는 토큰 캐시에 요청과 일치하는 토큰이 없기 때문입니다. 액세스 토큰은 1시간 후에 만료되고 `AcquireTokenSilent`는 새로 고침 토큰(OAuth2 용어로는 "새로 고침 토큰 ' 흐름)을 기준으로 새 토큰을 가져오려고 합니다. 이 흐름은 테넌트 관리자가 보다 엄격한 로그인 정책을 구성하는 등, 여러 가지 이유로 실패할 수도 있습니다. 

상호 작용은 사용자가 작업을 수행하도록 하는 것을 목표로 합니다. 클릭 한 번으로 사용 약관에 동의하는 것과 같이 사용자가 쉽게 해결할 수 있는 상황도 있고, 컴퓨터에서 특정 회사 네트워크에 연결해야 하는 경우처럼 현재 구성으로 해결할 수 없는 상황도 있습니다. 사용자가 Multi-Factor Authentication을 설정하거나 디바이스에 Microsoft Authenticator를 설치하는 데 도움이 되는 상황도 있습니다.

### <a name="msaluirequiredexception-classification-enumeration"></a>`MsalUiRequiredException` 분류 열거

MSAL은 사용자가 암호가 만료되었거나 일부 리소스를 사용하기 위해 동의해야 한다는 것을 알려 주는 데 사용되는 것처럼 더 나은 사용자 환경을 제공하기 위해 읽을 수 있는 `Classification` 필드를 노출합니다. 지원되는 값은 `UiRequiredExceptionClassification` 열거형에 속합니다.

| 분류    | 의미           | 권장 처리 |
|-------------------|-------------------|----------------------|
| BasicAction | 대화형 인증 흐름 중에 사용자 상호 작용을 통해 상황을 해결할 수 있습니다. | AcquireTokenInteractively()를 호출합니다. |
| AdditionalAction | 대화형 인증 흐름 외부에서 시스템과의 추가적인 수정 상호 작업을 통해 상황을 해결할 수 있습니다. | AcquireTokenInteractively()를 호출하여 수정 작업을 설명하는 메시지를 표시합니다. 호출 애플리케이션은 사용자가 수정 작업을 완료할 가능성이 없는 경우 additional_action이 필요한 흐름을 숨기도록 선택할 수 있습니다. |
| MessageOnly      | 지금은 상황을 해결할 수 없습니다. 대화형 인증 흐름을 시작하면 상황을 설명하는 메시지가 표시됩니다. | AcquireTokenInteractively()를 호출하여 상황을 설명하는 메시지를 표시합니다. AcquireTokenInteractively()는 사용자가 메시지를 읽고 창을 닫으면 UserCanceled 오류를 반환합니다. 호출 애플리케이션은 사용자가 메시지를 사용하는 것이 불가능한 경우 message_only를 발생하는 흐름을 숨기도록 선택할 수 있습니다.|
| ConsentRequired  | 사용자 동의가 누락되었거나 해지되었습니다. | 사용자가 동의할 수 있도록 AcquireTokenInteractively()를 호출합니다. |
| UserPasswordExpired | 사용자 암호가 만료되었습니다. | 사용자가 암호를 재설정할 수 있도록 AcquireTokenInteractively()를 호출합니다. |
| PromptNeverFailed| 대화형 인증이 매개 변수 prompt=never를 사용하여 호출되었으며 강제로 MSAL이 브라우저 쿠키를 사용하고 브라우저를 표시하지 않도록 합니다. 이 작업은 실패했습니다. | Prompt.None 없이 AcquireTokenInteractively()를 호출합니다. |
| AcquireTokenSilentFailed | MSAL SDK에는 캐시에서 토큰을 가져오기 위한 충분한 정보가 없습니다. 캐시에 토큰이 없거나 계정을 찾을 수 없기 때문일 수 있습니다. 오류 메시지에 자세한 내용이 포함되어 있습니다.  | AcquireTokenInteractively()를 호출합니다. |
| None    | 자세한 내용은 제공되지 않습니다. 대화형 인증 흐름 중에 사용자 상호 작용을 통해 상황을 해결할 수 있습니다. | AcquireTokenInteractively()를 호출합니다. |

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

## <a name="javascript"></a>[JavaScript](#tab/javascript)

MSAL.js는 여러 유형의 일반적인 오류를 추상화하고 분류 하는 오류 개체를 제공합니다. 또한 오류 메시지와 같은 오류에 대한 특정 세부 정보에 액세스하여 적절히 처리하기 위한 인터페이스도 제공합니다.

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
- `AuthError.message`:  `errorMessage`와 같습니다.
- `AuthError.stack`: throw된 오류에 대한 스택 추적입니다.

### <a name="error-types"></a>오류 유형

사용할 수 있는 오류 유형은 다음과 같습니다.

- `AuthError`: MSAL.js 라이브러리의 기본 오류 클래스이며, 예기치 않은 오류에도 사용됩니다.

- `ClientAuthError`: 클라이언트 인증 관련 문제를 나타내는 오류 클래스입니다. 라이브러리에서 발생하는 대부분의 오류는 ClientAuthErrors입니다. 이러한 오류는 로그인이 이미 진행 중이거나 사용자가 로그인을 취소하는 경우 등에 발생한 로그인 방법 호출과 같은 작업으로 인해 나타납니다.

- `ClientConfigurationError`: 지정한 사용자 구성 매개 변수가 누락되었거나 형식이 잘못된 경우 요청하기 전에 throw되는 `ClientAuthError`를 확장하는 오류 클래스입니다.

- `ServerError`: 인증 서버에서 보낸 오류 문자열을 나타내는 오류 클래스입니다. 잘못된 요청 형식 또는 매개 변수와 같은 오류 또는 서버에서 사용자를 인증하거나 사용자에게 권한을 부여하지 못하도록 하는 다른 오류일 수 있습니다.

- `InteractionRequiredAuthError`: 대화형 호출이 필요한 서버 오류를 나타내는 `ServerError`를 확장하는 오류 클래스입니다. 인증/권한 부여에 대한 자격 증명 또는 동의를 제공하기 위해 서버와 상호 작용해야 하는 경우 `acquireTokenSilent`에서 이 오류를 throw합니다. 오류 코드는 `"interaction_required"`, `"login_required"` 및 `"consent_required"`를 포함합니다.

리디렉션 메서드(`loginRedirect`, `acquireTokenRedirect`)를 사용하여 인증 흐름의 오류를 처리하려면 다음과 같이 `handleRedirectCallback()` 메서드를 사용하여 리디렉션 후에 성공 또는 실패로 호출되는 콜백을 등록해야 합니다.

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

### <a name="errors-that-require-interaction"></a>상호 작용이 필요한 오류

`acquireTokenSilent`와 같은 토큰 획득 비대화형 메서드를 사용하려고 하지만 MSAL에서 자동으로 이 작업을 수행할 수 없는 경우 오류가 반환됩니다.

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

## <a name="python"></a>[Python](#tab/python)

Python용 MSAL에서 대부분의 오류는 API 호출의 반환 값으로 전달됩니다. 이 오류는 Microsoft ID 플랫폼의 JSON 응답을 포함하는 사전으로 표시됩니다.

* 성공적인 응답에는 `"access_token"` 키가 포함됩니다. 응답의 형식은 OAuth2 프로토콜로 정의됩니다. 자세한 내용은 [5.1 성공 응답](https://tools.ietf.org/html/rfc6749#section-5.1)을 참조하세요.
* 오류 응답은 는 `"error"`를 포함되며 일반적으로 `"error_description"`입니다. 응답의 형식은 OAuth2 프로토콜로 정의됩니다. 자세한 내용은 [5.2 오류 응답](https://tools.ietf.org/html/rfc6749#section-5.2)을 참조하세요.

오류가 반환되면 `"error_description"` 키에 인간 판독 가능 메시지가 포함되며 여기에는 일반적으로 Microsoft ID 플랫폼 오류 코드가 포함됩니다. 다양한 오류 코드에 대한 자세한 내용은 [인증 및 권한 부여 오류 코드](./reference-aadsts-error-codes.md)를 참조하세요.

Python용 MSAL에서는 대부분의 오류가 오류 값을 반환하여 처리되기 때문에 예외가 드물게 발생합니다. `ValueError` 예외는 API 매개 변수의 형식이 잘못된 경우처럼 라이브러리를 사용하는 방법에 문제가 있는 경우에만 throw됩니다.

## <a name="java"></a>[Java](#tab/java)

Java용 MSAL에는 모두 `MsalException`에서 상속되는 `MsalClientException`, `MsalServiceException` 및 `MsalInteractionRequiredException`과 같은 세 가지 유형의 예외가 있습니다.

- 라이브러리 또는 디바이스에 로컬인 오류가 발생하면 `MsalClientException`이 throw됩니다.
- STS(보안 토큰 서비스)가 오류 응답을 반환하거나 다른 네트워킹 오류가 발생하면 `MsalServiceException`이 throw됩니다.
- 성공적인 인증을 위해 UI 상호 작용이 필요한 경우 `MsalInteractionRequiredException`이 throw됩니다.

### <a name="msalserviceexception"></a>MsalServiceException

`MsalServiceException`은 STS에 대한 요청에서 반환된 HTTP 헤더를 노출합니다. `MsalServiceException.headers()`를 통해 액세스합니다.

### <a name="msalinteractionrequiredexception"></a>MsalInteractionRequiredException

`AcquireTokenSilently()`를 호출할 때 Java용 MSAL에서 반환되는 일반적인 상태 코드 중 하나는 `InvalidGrantError`입니다. 이것은 인증 토큰을 발급하기 위해서는 추가 사용자 조작이 필요하다는 것을 의미합니다. 애플리케이션은 인증 라이브러리를 다시 호출하지만 퍼블릭 클라이언트 애플리케이션에 대한 `AuthorizationCodeParameters` 또는 `DeviceCodeParameters`를 전송하여 대화형 모드에서 호출해야 합니다.

`AcquireTokenSilently`가 실패하는 대부분의 경우는 토큰 캐시에 요청과 일치하는 토큰이 없기 때문입니다. 액세스 토큰은 1시간 후에 만료되고 `AcquireTokenSilently`는 새로 고침 토큰을 기준으로 새 토큰을 가져오려고 합니다. OAuth2 용어에서는 이것을 새로 고침 토큰 흐름이라고 합니다. 이 흐름은 테넌트 관리자가 보다 엄격한 로그인 정책을 구성하는 등, 여러 가지 이유로 실패할 수도 있습니다.

이 오류가 발생하는 상황은 사용자가 쉽게 해결할 수 있습니다. 예를 들어 사용 약관에 동의해야 할 수 있습니다. 또는 컴퓨터를 특정 회사 네트워크에 연결해야 하기 때문에 현재 구성으로 요청을 이행하지 못할 수도 있습니다.

MSAL은 더 나은 사용자 환경을 제공하는 데 사용할 수 있는 `reason` 필드를 노출합니다. 예를 들어 `reason` 필드는 사용자에게 암호가 만료되었거나 일부 리소스를 사용하기 위해 동의를 제공해야 한다는 사실을 사용자에게 알려줄 수 있습니다. 지원되는 값은 `InteractionRequiredExceptionReason` 열거형에 속합니다.

| 이유 | 의미 | 권장 처리 |
|---------|-----------|-----------------------------|
| `BasicAction` | 대화형 인증 흐름 중에 사용자 상호 작용을 통해 상황을 해결할 수 있습니다. | 대화형 매개 변수를 사용하여 `acquireToken`을 호출합니다. |
| `AdditionalAction` | 대화형 인증 흐름 외부에서 시스템과의 추가적인 수정 상호 작업을 통해 상황을 해결할 수 있습니다. | 대화형 매개 변수를 사용하여 `acquireToken`을 호출하는 방식으로 수정 작업을 설명하는 메시지를 표시합니다. 호출 앱은 사용자가 수정 작업을 완료할 가능성이 없는 경우 additional_action이 필요한 흐름을 숨기도록 선택할 수 있습니다. |
| `MessageOnly` | 지금은 상황을 해결할 수 없습니다. 대화형 인증 흐름을 시작하면 상황을 설명하는 메시지가 표시됩니다. | 대화형 매개 변수를 사용하여 `acquireToken`을 호출하는 방식으로 상황을 설명하는 메시지를 표시합니다. `acquireToken`은 사용자가 메시지를 읽고 창을 닫은 후에 `UserCanceled` 오류를 반환합니다. 앱은 사용자가 메시지를 사용하는 것이 불가능한 경우 메시지를 표시하는 흐름을 숨기도록 선택할 수 있습니다. |
| `ConsentRequired`| 사용자 동의가 누락되었거나 해지되었습니다. |사용자가 동의를 제공할 수 있도록 대화형 매개 변수를 사용하여 `acquireToken`을 호출합니다. |
| `UserPasswordExpired` | 사용자 암호가 만료되었습니다. | 사용자가 암호를 재설정할 수 있도록 대화형 매개 변수를 사용하여 `acquireToken`을 호출합니다. |
| `None` |  자세한 내용이 제공됩니다. 대화형 인증 흐름 중에 사용자 상호 작용을 통해 상황을 해결할 수 있습니다. | 대화형 매개 변수를 사용하여 `acquireToken`을 호출합니다. |

### <a name="code-example"></a>코드 예

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

## <a name="iosmacos"></a>[iOS/macOS](#tab/iosmacos)

iOS 및 macOS 오류에 대한 MSAL의 전체 목록은 [MSALError 열거형](https://github.com/AzureAD/microsoft-authentication-library-for-objc/blob/master/MSAL/src/public/MSALError.h#L128)에 나와 있습니다.

오류를 생성한 모든 MSAL이 `MSALErrorDomain` 도메인과 함께 반환됩니다.

시스템 오류의 경우 MSAL은 시스템 API에서 원래 `NSError`를 반환합니다. 예를 들어 네트워크 연결 부족으로 인해 토큰 획득에 실패하는 경우 MSAL은 `NSURLErrorDomain` 도메인 및 `NSURLErrorNotConnectedToInternet` 코드와 함께 오류를 반환합니다.

클라이언트 쪽에서 적어도 다음과 같은 두 개의 MSAL 오류를 처리하는 것이 좋습니다.

- `MSALErrorInteractionRequired`: 사용자는 대화형 요청을 수행해야 합니다. 인증 세션 만료나 추가 인증 요구 사항이 필요한 경우처럼 이 오류가 발생할 수 있는 여러 상황이 있습니다. MSAL 대화형 토큰 획득 API를 호출하여 복구합니다. 

- `MSALErrorServerDeclinedScopes`: 일부 또는 모든 범위가 거부되었습니다. 권한이 부여된 범위에서만 계속할지 또는 로그인 프로세스를 중지할지 결정합니다.

> [!NOTE]
> `MSALInternalError` 열거형은 참조 및 디버깅에만 사용해야 합니다. 런타임에 이러한 오류를 자동으로 처리하지 마세요. 앱에서 `MSALInternalError`에 속하는 오류가 발생할 경우 발생한 상황을 설명하는 일반 사용자 연결 메시지를 표시할 수 있습니다.

예를 들어 `MSALInternalErrorBrokerResponseNotReceived`는 사용자가 인증을 완료하지 않았고 앱에 수동으로 반환되었음을 의미합니다. 이 경우, 애플리케이션은 인증이 완료되지 않은 것을 설명하는 일반 오류 메시지를 표시하고 인증을 다시 시도할 것을 제안합니다.

다음 Objective-C 샘플 코드는 몇 가지 일반적인 오류 조건을 처리하기 위한 모범 사례를 보여 줍니다.

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

토큰을 자동으로 가져올 때 액세스하려는 API에서 [조건부 액세스 클레임 챌린지](../azuread-dev/conditional-access-dev-guide.md)와 같은 MFA 정책이 필요한 경우 애플리케이션에서 오류가 발생할 수 있습니다.

이 오류를 처리하는 패턴은 MSAL을 사용하여 토큰을 대화형으로 획득하는 것입니다. 토큰을 대화형으로 획득하면 사용자에게 메시지가 표시되어 필요한 조건부 액세스 정책을 충족시킬 수 있는 기회가 제공됩니다.

조건부 액세스가 필요한 API를 호출하는 경우 클레임 챌린지를 API의 오류로 받을 수 있습니다. 예를 들어 조건부 액세스 정책에 관리 디바이스(Intune)가 있는 경우 오류는 [AADSTS53000: 이 리소스에 액세스하려면 디바이스가 관리되어야 합니다](reference-aadsts-error-codes.md)이거나 이와 비슷합니다. 이 경우 토큰 획득 호출에서 클레임을 전달하여 사용자에게 적절한 정책을 충족시키라는 메시지가 표시되도록 할 수 있습니다.

### <a name="net"></a>.NET

MSAL.NET에서 조건부 액세스가 필요한 API를 호출하는 경우 애플리케이션에서 클레임 챌린지 예외를 처리해야 합니다. 이 예외는 [Claims](/dotnet/api/microsoft.identity.client.msalserviceexception.claims?view=azure-dotnet) 속성이 비어 있지 않은 [MsalServiceException](/dotnet/api/microsoft.identity.client.msalserviceexception?view=azure-dotnet)으로 표시됩니다.

클레임 챌린지를 처리하려면 `PublicClientApplicationBuilder` 클래스의 `.WithClaim()` 메서드를 사용해야 합니다.

### <a name="javascript"></a>JavaScript

MSAL.js를 사용하여 자동으로 토큰을 가져올 때(`acquireTokenSilent` 사용) 액세스하려는 API에서 MFA 정책과 같은 [조건부 액세스 클레임 챌린지](../azuread-dev/conditional-access-dev-guide.md)를 요구하면 애플리케이션에서 오류가 발생할 수 있습니다.

이 오류를 처리하는 패턴은 다음 예제와 같이 MSAL.js에서 토큰(예: `acquireTokenPopup` 또는 `acquireTokenRedirect`)을 획득하기 위해 대화형 호출을 수행하는 것입니다.

```javascript
myMSALObj.acquireTokenSilent(accessTokenRequest).then(function(accessTokenResponse) {
    // call API
}).catch(function(error) {
    if (error instanceof InteractionRequiredAuthError) {
    
        // extract, if exists, claims from error message
        if (error.ErrorMessage.claims) {
            accessTokenRequest.claimsRequest = JSON.stringify(error.ErrorMessage.claims);
        }
        
        // call acquireTokenPopup in case of InteractionRequiredAuthError failure
        myMSALObj.acquireTokenPopup(accessTokenRequest).then(function(accessTokenResponse) {
            // call API
        }).catch(function(error) {
            console.log(error);
        });
    }
});
```

토큰을 대화형으로 획득하면 사용자에게 메시지가 표시되어 필요한 조건부 액세스 정책을 충족시킬 수 있는 기회가 제공됩니다.

조건부 액세스가 필요한 API를 호출할 때 클레임 챌린지를 API의 오류로 받을 수 있습니다. 이 경우 오류에서 반환된 클레임을 `AuthenticationParameters.ts` 클래스의 `claimsRequest` 필드로 전달하여 적절한 정책을 충족시킬 수 있습니다. 

자세한 내용은 [추가 클레임 요청](active-directory-optional-claims.md)을 참조하세요.

### <a name="msal-for-ios-and-macos"></a>iOS 및 macOS용 MSAL

iOS 및 macOS용 MSAL을 사용하여 대화형 및 자동 토큰 취득 시나리오에서 특정 클레임을 요청할 수 있습니다.

사용자 지정 클레임을 요청하려면 `MSALSilentTokenParameters` 또는 `MSALInteractiveTokenParameters`에서 `claimsRequest`를 지정합니다.

자세한 내용은 [iOS 및 macOS용 MSAL을 사용하여 사용자 지정 클레임 요청](request-custom-claims.md)을 참조하세요.

## <a name="retrying-after-errors-and-exceptions"></a>오류 및 예외 후 다시 시도

MSAL을 호출할 때 사용자 고유의 재시도 정책을 구현해야 합니다. MSAL은 AAD 서비스에 대한 HTTP 호출을 수행하며, 네트워크 작동이 중단되거나 서버가 오버로드되는 것과 같은 오류가 가끔 발생할 수 있습니다.  

### <a name="http-error-codes-500-600"></a>500-600 HTTP 오류 코드

MSAL.NET은 500-600 HTTP 오류 코드의 오류에 대한 간단한 일회성 다시 시도 메커니즘을 구현합니다.

### <a name="http-429"></a>429 HTTP

STS(서비스 토큰 서버)가 너무 많은 요청으로 인해 오버로드되는 경우 `Retry-After` 응답 필드에서 다시 시도할 수 있을 때까지의 시간에 대한 힌트를 포함하는 HTTP 오류 429를 반환합니다.

### <a name="net"></a>.NET

[MsalServiceException](/dotnet/api/microsoft.identity.client.msalserviceexception?view=azure-dotnet)은 `System.Net.Http.Headers.HttpResponseHeaders`를 `namedHeaders` 속성으로 표시합니다. 오류 코드의 추가 정보를 사용하여 애플리케이션의 안정성을 향상시킬 수 있습니다. 설명된 경우에서 `RetryAfterproperty`(`RetryConditionHeaderValue` 형식)를 사용하여 다시 시도 시기를 계산할 수 있습니다.

클라이언트 자격 증명 흐름을 사용하는 디먼 애플리케이션 예제는 다음과 같습니다. 토큰을 획득하는 방법에 이 예제를 적용할 수 있습니다.

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