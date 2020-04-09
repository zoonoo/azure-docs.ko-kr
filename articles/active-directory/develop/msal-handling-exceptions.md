---
title: 오류 및 예외(MSAL)
titleSuffix: Microsoft identity platform
description: MSAL 응용 프로그램에서 오류 및 예외, 조건부 액세스 및 클레임 문제를 처리하는 방법을 알아봅니다.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 11/22/2019
ms.author: marsma
ms.reviewer: saeeda, jmprieur
ms.custom: aaddev
ms.openlocfilehash: 93d07ab1740da68298478ae2dcc2ab46d8d8362e
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/08/2020
ms.locfileid: "80884021"
---
# <a name="handle-msal-exceptions-and-errors"></a>MSAL 예외 및 오류 처리

이 문서에서는 다양한 유형의 오류에 대한 개요와 일반적인 로그인 오류를 처리하기 위한 권장 사항을 제공합니다.

## <a name="msal-error-handling-basics"></a>MSAL 오류 처리 기본 사항

MSAL(Microsoft 인증 라이브러리)의 예외는 앱 개발자가 최종 사용자에게 표시하는 것이 아니라 문제를 해결하기 위한 것입니다. 예외 메시지는 지역화되지 않았습니다.

예외 및 오류를 처리할 때 예외 유형 자체와 오류 코드를 사용하여 예외를 구별할 수 있습니다.  오류 코드 목록은 [인증 및 권한 부여 오류 코드](reference-aadsts-error-codes.md)를 참조하세요.

로그인 환경 동안 동의, 조건부 액세스(MFA, 장치 관리, 위치 기반 제한), 토큰 발급 및 사용, 사용자 속성에 대한 오류가 발생할 수 있습니다.

앱의 오류 처리에 대한 자세한 내용은 사용 중인 언어와 일치하는 다음 섹션을 참조하세요.

## <a name="net"></a>[.NET](#tab/dotnet)

.NET 예외를 처리할 때 예외 유형 자체와 `ErrorCode` 멤버를 사용하여 예외를 구분할 수 있습니다. `ErrorCode`값은 [MsalError](/dotnet/api/microsoft.identity.client.msalerror?view=azure-dotnet)형식의 상수입니다.

[MsalClientException,](/dotnet/api/microsoft.identity.client.msalexception?view=azure-dotnet) [MsalServiceException](/dotnet/api/microsoft.identity.client.msalserviceexception?view=azure-dotnet)및 [MsalUIRequiredException](/dotnet/api/microsoft.identity.client.msaluirequiredexception?view=azure-dotnet)의 필드를 살펴볼 수도 있습니다.

[MsalServiceException이](/dotnet/api/microsoft.identity.client.msalserviceexception?view=azure-dotnet) throw된 경우 [인증 및 권한 부여 오류 코드를](reference-aadsts-error-codes.md) 시도하여 코드가 목록에 있는지 확인합니다.

### <a name="common-net-exceptions"></a>일반적인 .NET 예외

다음은 throw될 수 있는 일반적인 예외와 몇 가지 가능한 완화 사항입니다.  

| 예외 | 오류 코드 | 완화 방법|
| --- | --- | --- |
| [MsalUiRequiredException](/dotnet/api/microsoft.identity.client.msaluirequiredexception?view=azure-dotnet) | AADSTS65001: 사용자 또는 관리자가 '{appName}'라는 ID '{appId}'와 함께 응용 프로그램을 사용하는 데 동의하지 않았습니다. 이 사용자 및 리소스에 대한 대화형 권한 부여 요청을 보냅니다.| 먼저 사용자 동의를 받아야 합니다. .NET Core(웹 UI가 없는)를 사용하지 않는 경우 호출(한 `AcquireTokeninteractive`번만). .NET 코어를 사용 중이거나 `AcquireTokenInteractive`을 수행하지 않으려는 경우 사용자는 URL로 이동하여 동의를 제공할 수 `https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id={clientId}&response_type=code&scope=user.read`있습니다. 전화 `AcquireTokenInteractive`:`app.AcquireTokenInteractive(scopes).WithAccount(account).WithClaims(ex.Claims).ExecuteAsync();`|
| [MsalUiRequiredException](/dotnet/api/microsoft.identity.client.msaluirequiredexception?view=azure-dotnet) | AADSTS50079: 사용자는 MFA(다단계 인증)를 사용해야 합니다.| 완화가 없습니다. 테넌트에 대해 MFA가 구성되고 AAD(Azure Active Directory)가 이를 적용하기로 결정한 경우 `AcquireTokenInteractive` 또는 `AcquireTokenByDeviceCode`와 같은 대화형 흐름으로 대체해야 합니다.|
| [MsalServiceException](/dotnet/api/microsoft.identity.client.msalserviceexception?view=azure-dotnet) |AADSTS90010: 권한 부여 유형은 */common* 또는 */consumers* 끝점에서 지원되지 않습니다. */organizations* 또는 테넌트 특정 엔드포인트를 사용합니다. */common*을 사용했습니다.| Azure AD의 메시지에서 설명한 대로 인증 기관에 테넌트 또는 */organizations*가 있어야 합니다.|
| [MsalServiceException](/dotnet/api/microsoft.identity.client.msalserviceexception?view=azure-dotnet) | AADSTS70002: 요청 본문에는 다음 `client_secret or client_assertion`매개 변수가 포함되어야 합니다.| 응용 프로그램이 Azure AD에서 공용 클라이언트 응용 프로그램으로 등록되지 않은 경우 이 예외를 throw할 수 있습니다. Azure 포털에서 응용 프로그램에 대한 매니페스트를 `allowPublicClient` `true`편집하고 로 설정합니다. |
| [MsalClientException](/dotnet/api/microsoft.identity.client.msalclientexception?view=azure-dotnet)| `unknown_user Message`: 로그인한 사용자를 식별할 수 없습니다.| 라이브러리가 현재 Windows 로그인 사용자를 쿼리할 수 없거나 이 사용자가 AD 또는 AAD가 조인되지 않았습니다(작업 장소에 참여한 사용자는 지원되지 않음). 완화 1: UWP에서 응용 프로그램에 엔터프라이즈 인증, 개인 네트워크(클라이언트 및 서버), 사용자 계정 정보 등의 기능이 있는지 확인합니다. 완화 2: 사용자 이름을 가져오고 사용자 john@contoso.com이름에 걸리는 `AcquireTokenByIntegratedWindowsAuth` 양식을 사용 하려면 고유한 논리를 구현 합니다.|
| [MsalClientException](/dotnet/api/microsoft.identity.client.msalclientexception?view=azure-dotnet)|integrated_windows_auth_not_supported_managed_user| 이 메서드는 AD(Active Directory)에서 공개되는 프로토콜을 사용합니다. 사용자("관리" 사용자)를 AD 지원 없이 Azure Active Directory에서 만든 경우 이 메서드는 실패합니다. AD에서 만들어지고 AAD에서 지원하는 사용자("페더레이션" 사용자)는 이 비대화형 인증 메서드를 활용할 수 있습니다. 완화: 대화형 인증을 사용합니다.|

### `MsalUiRequiredException`

호출 `AcquireTokenSilent()` 할 때 MSAL.NET 반환 된 `MsalError.InvalidGrantError`공통 상태 코드 중 하나입니다. 이 상태 코드는 응용 프로그램이 인증 라이브러리를 다시 호출해야 하지만 대화형 모드(공용 클라이언트 응용 프로그램에 대한 AcquireTokenInteractive 또는 AcquireTokenByDeviceCodeFlow)에서 웹 앱에서 챌린지를 수행해야 한다는 것을 의미합니다. 인증 토큰을 발급하기 전에 추가 사용자 상호 작용이 필요하기 때문입니다.

대부분의 경우 `AcquireTokenSilent` 토큰 캐시에 요청과 일치하는 토큰이 없기 때문입니다. 액세스 토큰은 1시간 후에 `AcquireTokenSilent` 만료되며 새로 고침 토큰을 기반으로 새 토큰을 가져오려고 시도합니다(OAuth2 용어로 는 "새로 고침 토큰' 흐름입니다). 테넌트 관리자가 보다 엄격한 로그인 정책을 구성하는 경우와 같은 여러 가지 이유로 이 흐름이 실패할 수도 있습니다. 

상호 작용은 사용자가 작업을 수행하도록 하는 것을 목표로 합니다. 이러한 조건 중 일부는 사용자가 쉽게 해결할 수 있으며(예: 한 번의 클릭으로 이용 약관수락) 일부는 현재 구성으로 해결할 수 없습니다(예: 해당 컴퓨터가 특정 회사 네트워크에 연결해야 함). 일부는 사용자가 다단계 인증을 설정하거나 장치에 Microsoft 인증자를 설치하는 데 도움이 됩니다.

### <a name="msaluirequiredexception-classification-enumeration"></a>`MsalUiRequiredException`분류 열거

MSAL은 더 `Classification` 나은 사용자 환경을 제공하기 위해 읽을 수 있는 필드를 노출합니다(예: 사용자에게 암호가 만료되었거나 일부 리소스를 사용하기 위해 동의를 제공해야 한다는 내용). 지원되는 값은 열거형의 `UiRequiredExceptionClassification` 일부입니다.

| 분류    | 의미           | 권장 취급 |
|-------------------|-------------------|----------------------|
| 기본 작업 | 대화형 인증 흐름 중에 사용자 상호 작용으로 상태를 해결할 수 있습니다. | 인수 토큰대화형()를 호출합니다. |
| 추가 작업 | 대화형 인증 흐름 외부에서 시스템과의 추가 수정 상호 작용을 통해 상태를 해결할 수 있습니다. | AcquireTokenInteractively()를 호출하여 수정 작업을 설명하는 메시지를 표시합니다. 응용 프로그램을 호출하면 사용자가 수정 작업을 완료할 가능성이 없는 경우 additional_action 필요한 흐름을 숨길 수 있습니다. |
| 메시지 만      | 현재 조건을 해결할 수 없습니다. 대화형 인증 흐름을 시작하면 조건을 설명하는 메시지가 표시됩니다. | AcquireTokenInteractively()를 호출하여 조건을 설명하는 메시지를 표시합니다. AcquireTokenInteractively())는 사용자가 메시지를 읽고 창을 닫은 후 UserCanceled 오류를 반환합니다. 응용 프로그램을 호출하면 사용자가 메시지의 이점을 얻을 가능성이 없는 경우 message_only 발생하는 흐름을 숨길 수 있습니다.|
| 동의 필수  | 사용자 동의가 없거나 취소되었습니다. | 사용자가 동의할 수 있도록 AcquireTokenInteractively()를 호출합니다. |
| 사용자 암호만료됨 | 사용자의 암호가 만료되었습니다. | 사용자가 암호를 재설정할 수 있도록 AcquireTokenInteractively()를 호출합니다. |
| 프롬프트네실패| 대화형 인증은 매개 변수 프롬프트=never를 사용하여 호출되어 MSAL이 브라우저 쿠키에 의존하고 브라우저를 표시하지 않도록 합니다. 이 데 실패했습니다. | 프롬프트 없이 AcquireTokeninteractively() 호출합니다.없음 |
| 취득토큰자동실패 | MSAL SDK는 캐시에서 토큰을 가져올 정보가 충분하지 않습니다. 이는 토큰이 캐시에 없거나 계정을 찾을 수 없기 때문일 수 있습니다. 오류 메시지에 자세한 내용이 있습니다.  | 인수 토큰대화형()를 호출합니다. |
| None    | 자세한 내용은 제공되지 않습니다. 조건은 대화형 인증 흐름 동안 사용자 상호 작용에 의해 해결될 수 있다. | 인수 토큰대화형()를 호출합니다. |

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

MSAL.js는 다양한 유형의 일반적인 오류를 추상화하고 분류하는 오류 개체를 제공합니다. 또한 오류 메시지와 같은 오류의 특정 세부 정보에 액세스하여 적절하게 처리하는 인터페이스를 제공합니다.

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
- `AuthError.message`: 와 `errorMessage`동일합니다.
- `AuthError.stack`: throw된 오류에 대한 스택 추적.

### <a name="error-types"></a>오류 형식

사용할 수 있는 오류 유형은 다음과 같습니다.

- `AuthError`: MSAL.js 라이브러리의 기본 오류 클래스로 예기치 않은 오류에도 사용됩니다.

- `ClientAuthError`: 클라이언트 인증 문제를 나타내는 오류 클래스입니다. 라이브러리에서 발생하는 대부분의 오류는 ClientAuthErrors입니다. 이러한 오류는 로그인이 이미 진행 중일 때 로그인 메서드를 호출하고 사용자가 로그인을 취소하는 등의 방법으로 인해 발생합니다.

- `ClientConfigurationError`: 오류 클래스, `ClientAuthError` 지정된 사용자 구성 매개 변수가 잘못 되거나 누락 된 경우 요청이 수행되기 전에 throw된 확장입니다.

- `ServerError`: 오류 클래스는 인증 서버에서 보낸 오류 문자열을 나타냅니다. 잘못된 요청 형식 또는 매개 변수와 같은 오류 또는 서버에서 사용자를 인증하거나 사용자에게 권한을 부여하지 못하도록 하는 다른 오류일 수 있습니다.

- `InteractionRequiredAuthError`: 오류 클래스는 `ServerError` 대화형 호출이 필요한 서버 오류를 나타내기 위해 확장됩니다. 이 오류는 사용자가 `acquireTokenSilent` 인증/권한 부여에 대한 자격 증명 또는 동의를 제공하기 위해 서버와 상호 작용해야 하는 경우에 의해 throw됩니다. 오류 코드에는 `"login_required"`을 `"consent_required"`포함합니다. `"interaction_required"`

리디렉션 메서드 (,`loginRedirect`)를 `acquireTokenRedirect`사용하여 인증 흐름에서 오류 처리의 경우 다음과 같이 리디렉션 메서드를 사용하여 `handleRedirectCallback()` 리디렉션 한 후 성공 또는 실패로 호출되는 콜백을 등록해야합니다.

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

와 같은 `acquireTokenSilent`토큰을 가져오는 비대화형 방법을 사용하려고 시도할 때 오류가 반환되지만 MSAL은 자동으로 수행할 수 없습니다.

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

파이썬에 대한 MSAL에서 대부분의 오류는 API 호출에서 반환 값으로 전달됩니다. 이 오류는 Microsoft ID 플랫폼의 JSON 응답을 포함하는 사전으로 표시됩니다.

* 성공적인 응답에는 `"access_token"` 키가 포함되어 있습니다. 응답 형식은 OAuth2 프로토콜에 의해 정의됩니다. 자세한 내용은 [5.1 성공적인 응답을 참조하십시오.](https://tools.ietf.org/html/rfc6749#section-5.1)
* 오류 응답에는 `"error"` 일반적으로 `"error_description"`.가 포함됩니다. 응답 형식은 OAuth2 프로토콜에 의해 정의됩니다. 자세한 내용은 [5.2 오류 응답을 참조하십시오.](https://tools.ietf.org/html/rfc6749#section-5.2)

오류가 반환되면 키에는 `"error_description"` 사람이 읽을 수 있는 메시지가 포함됩니다. 일반적으로 Microsoft ID 플랫폼 오류 코드가 포함되어 있습니다. 다양한 오류 코드에 대한 자세한 내용은 [인증 및 권한 부여 오류 코드를](https://docs.microsoft.com/azure/active-directory/develop/reference-aadsts-error-codes)참조하십시오.

파이썬의 MSAL에서는 대부분의 오류가 오류 값을 반환하여 처리되기 때문에 예외가 드뭅니다. 예외는 `ValueError` API 매개 변수가 형식이 잘못된 경우와 같이 라이브러리를 사용하려고 하는 방법에 문제가 있는 경우에만 throw됩니다.

## <a name="java"></a>[Java](#tab/java)

Java용 MSAL에는 세 가지 유형의 `MsalClientException`예외가 `MsalInteractionRequiredException`있습니다. `MsalServiceException` `MsalException`에서 상속되는 모든 .

- `MsalClientException`는 라이브러리 또는 장치에 로컬인 오류가 발생할 때 throw됩니다.
- `MsalServiceException`보안 토큰 서비스(STS)가 오류 응답을 반환하거나 다른 네트워킹 오류가 발생하면 throw됩니다.
- `MsalInteractionRequiredException`인증이 성공하려면 UI 상호 작용이 필요한 경우 throw됩니다.

### <a name="msalserviceexception"></a>MsalServiceException

`MsalServiceException`을 사용하여 요청에서 반환된 HTTP 헤더를 STS에 노출합니다. 통해 액세스`MsalServiceException.headers()`

### <a name="msalinteractionrequiredexception"></a>Msal상호작용필수예외

호출 `AcquireTokenSilently()` 할 때 Java에 대한 MSAL에서 `InvalidGrantError`반환된 공통 상태 코드 중 하나입니다. 즉, 인증 토큰을 발급하기 전에 추가 사용자 상호 작용이 필요합니다. 응용 프로그램은 인증 라이브러리를 다시 호출해야 하지만 `AuthorizationCodeParameters` `DeviceCodeParameters` 대화형 모드에서는 전송하거나 공용 클라이언트 응용 프로그램을 호출해야 합니다.

대부분의 경우 `AcquireTokenSilently` 토큰 캐시에 요청과 일치하는 토큰이 없기 때문입니다. 액세스 토큰은 1시간 후에 `AcquireTokenSilently` 만료되며 새로 고침 토큰을 기반으로 새 토큰을 얻으려고 시도합니다. OAuth2 용어에서 이 흐름은 토큰 새로 고침 흐름입니다. 이 흐름은 테넌트 관리자가 보다 엄격한 로그인 정책을 구성하는 경우와 같은 여러 가지 이유로 실패할 수도 있습니다.

이 오류로 인해 발생하는 일부 조건은 사용자가 쉽게 해결할 수 있습니다. 예를 들어, 이용 약관에 동의해야 할 수 있습니다. 또는 컴퓨터가 특정 회사 네트워크에 연결해야 하기 때문에 현재 구성으로 요청을 이행할 수 없습니다.

MSAL은 더 `reason` 나은 사용자 환경을 제공하는 데 사용할 수 있는 필드를 노출합니다. 예를 들어 `reason` 이 필드를 통해 사용자에게 암호가 만료되었거나 일부 리소스를 사용하기 위해 동의를 제공해야 한다고 알릴 수 있습니다. 지원되는 값은 열거형의 `InteractionRequiredExceptionReason` 일부입니다.

| 이유 | 의미 | 권장 취급 |
|---------|-----------|-----------------------------|
| `BasicAction` | 대화형 인증 흐름 중에 사용자 상호 작용으로 상태를 해결할 수 있습니다. | 대화형 `acquireToken` 매개 변수를 사용 하 고 호출 |
| `AdditionalAction` | 조건은 대화형 인증 흐름 외부에서 시스템과의 추가 수정 상호 작용을 통해 해결할 수 있습니다. | 대화형 `acquireToken` 매개 변수를 사용하여 호출하여 취할 수정 작업을 설명하는 메시지를 표시합니다. 호출 앱은 사용자가 수정 작업을 완료할 가능성이 없는 경우 추가 작업이 필요한 흐름을 숨길 수 있습니다. |
| `MessageOnly` | 현재 조건을 해결할 수 없습니다. 대화형 인증 흐름을 시작하여 조건을 설명하는 메시지를 표시합니다. | 대화형 `acquireToken` 매개 변수를 사용하여 조건을 설명하는 메시지를 표시합니다. `acquireToken`사용자가 메시지를 `UserCanceled` 읽고 창을 닫은 후 오류가 반환됩니다. 앱은 사용자가 메시지의 이점을 얻을 가능성이 낮은 경우 메시지로 인해 발생하는 흐름을 숨기도록 선택할 수 있습니다. |
| `ConsentRequired`| 사용자 동의가 없거나 취소되었습니다. |사용자가 `acquireToken` 동의할 수 있도록 대화형 매개 변수를 사용합니다. |
| `UserPasswordExpired` | 사용자의 암호가 만료되었습니다. | 사용자가 `acquireToken` 암호를 재설정할 수 있도록 대화형 매개 변수로 통화 |
| `None` |  자세한 내용은 제공됩니다. 조건은 대화형 인증 흐름 동안 사용자 상호 작용에 의해 해결될 수 있다. | 대화형 `acquireToken` 매개 변수를 사용 하 고 호출 |

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

iOS 및 macOS 오류에 대한 MSAL의 전체 목록은 [MSALError 열거형에](https://github.com/AzureAD/microsoft-authentication-library-for-objc/blob/master/MSAL/src/public/MSALError.h#L128)나열되어 있습니다.

MSAL에서 생성된 모든 `MSALErrorDomain` 오류는 도메인과 함께 반환됩니다.

시스템 오류의 경우 MSAL은 시스템 API에서 원본을 `NSError` 반환합니다. 예를 들어 네트워크 연결이 부족하여 토큰 획득에 실패한 경우 MSAL은 `NSURLErrorDomain` 도메인 `NSURLErrorNotConnectedToInternet` 및 코드에 대한 오류를 반환합니다.

클라이언트 측에서 적어도 다음 두 개의 MSAL 오류를 처리하는 것이 좋습니다.

- `MSALErrorInteractionRequired`: 사용자가 대화형 요청을 수행해야 합니다. 만료된 인증 세션 또는 추가 인증 요구 사항과 같이 이 오류로 이어질 수 있는 많은 조건이 있습니다. MSAL 대화형 토큰 수집 API를 호출하여 복구합니다. 

- `MSALErrorServerDeclinedScopes`: 일부 또는 모든 범위가 거부되었습니다. 부여된 범위만 계속할지 아니면 로그인 프로세스를 중지할지 결정합니다.

> [!NOTE]
> 열거형은 `MSALInternalError` 참조 및 디버깅에만 사용해야 합니다. 런타임에 이러한 오류를 자동으로 처리하지 마십시오. 앱에서 아래에 `MSALInternalError`있는 오류가 발생하는 경우 발생한 일을 설명하는 일반 사용자에게 메시지를 표시할 수 있습니다.

예를 들어 `MSALInternalErrorBrokerResponseNotReceived` 사용자가 인증을 완료하지 않았고 앱으로 수동으로 반환됨을 의미합니다. 이 경우 앱에 인증이 완료되지 않았음을 설명하는 일반 오류 메시지가 표시되고 다시 인증하도록 제안해야 합니다.

다음 Objective-C 샘플 코드는 몇 가지 일반적인 오류 조건을 처리하는 모범 사례를 보여 줍니다.

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

토큰을 자동으로 가져오는 경우 액세스하려는 API에서 MFA 정책과 같은 [조건부 액세스 클레임 챌린지가](../azuread-dev/conditional-access-dev-guide.md) 필요한 경우 응용 프로그램에 오류가 발생할 수 있습니다.

이 오류를 처리하는 패턴은 MSAL을 사용하여 대화식으로 토큰을 획득하는 것입니다. 토큰을 대화형으로 획득하면 사용자에게 메시지가 표시되어 필요한 조건부 액세스 정책을 충족시킬 수 있는 기회가 제공됩니다.

조건부 액세스가 필요한 API를 호출하는 경우 클레임 챌린지를 API의 오류로 받을 수 있습니다. 예를 들어 조건부 액세스 정책이 관리되는 장치(Intune)를 사용하려는 경우 [AADSTS53000과](reference-aadsts-error-codes.md) 같은 오류가 발생합니다. 이 경우 토큰 획득 호출에서 클레임을 전달하여 사용자에게 적절한 정책을 충족시키라는 메시지가 표시되도록 할 수 있습니다.

### <a name="net"></a>.NET

MSAL.NET에서 조건부 액세스가 필요한 API를 호출하는 경우 애플리케이션에서 클레임 챌린지 예외를 처리해야 합니다. 이 예외는 [Claims](/dotnet/api/microsoft.identity.client.msalserviceexception.claims?view=azure-dotnet) 속성이 비어 있지 않은 [MsalServiceException](/dotnet/api/microsoft.identity.client.msalserviceexception?view=azure-dotnet)으로 표시됩니다.

클레임 챌린지를 처리하려면 클래스의 메서드를 `.WithClaim()` `PublicClientApplicationBuilder` 사용해야 합니다.

### <a name="javascript"></a>JavaScript

MSAL.js를 사용하여 `acquireTokenSilent`토큰을 자동으로 (사용) 가져오는 경우 액세스하려는 API에서 MFA 정책과 같은 [조건부 액세스 클레임 챌린지가](../azuread-dev/conditional-access-dev-guide.md) 필요한 경우 응용 프로그램에 오류가 발생할 수 있습니다.

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

조건부 액세스가 필요한 API를 호출할 때 클레임 챌린지를 API의 오류로 받을 수 있습니다. 이 경우 오류에서 `claimsRequest` 반환된 클레임을 `AuthenticationParameters.ts` 클래스 필드에 전달하여 적절한 정책을 충족할 수 있습니다. 

자세한 내용은 [추가 클레임 요청을](active-directory-optional-claims.md) 참조하십시오.

### <a name="msal-for-ios-and-macos"></a>iOS 및 macOS용 MSAL

iOS 및 macOS용 MSAL을 사용하면 대화형 및 자동 토큰 수집 시나리오에서 특정 클레임을 요청할 수 있습니다.

사용자 지정 클레임을 `claimsRequest` `MSALSilentTokenParameters` 요청하려면 또는 `MSALInteractiveTokenParameters`에 지정합니다.

자세한 내용은 [iOS 및 macOS용 MSAL을 사용하여 사용자 지정 소유권 주장 요청을](request-custom-claims.md) 참조하세요.

## <a name="retrying-after-errors-and-exceptions"></a>오류 및 예외 후 다시 시도

MSAL을 호출할 때 재시도 정책을 직접 구현해야 합니다. MSAL은 AAD 서비스에 대한 HTTP 호출을 수행하며, 네트워크가 다운될 수 있거나 서버가 오버로드되는 등 가끔 오류가 발생할 수 있습니다.  

### <a name="http-error-codes-500-600"></a>500-600 HTTP 오류 코드

MSAL.NET은 500-600 HTTP 오류 코드의 오류에 대한 간단한 일회성 다시 시도 메커니즘을 구현합니다.

### <a name="http-429"></a>429 HTTP

STS(서비스 토큰 서버)가 너무 많은 요청으로 오버로드되면 응답 필드에서 다시 시도할 수 있을 때까지의 `Retry-After` 기간에 대한 힌트와 함께 HTTP 오류 429를 반환합니다.

### <a name="net"></a>.NET

[MsalService예외는](/dotnet/api/microsoft.identity.client.msalserviceexception?view=azure-dotnet) `System.Net.Http.Headers.HttpResponseHeaders` 속성으로 `namedHeaders`표시됩니다. 오류 코드의 추가 정보를 사용하여 응용 프로그램의 안정성을 향상시킬 수 있습니다. 설명된 경우 `RetryAfterproperty` (형식)을 `RetryConditionHeaderValue`사용하고 다시 시도할 시기를 계산할 수 있습니다.

다음은 클라이언트 자격 증명 흐름을 사용하는 데몬 응용 프로그램에 대한 예입니다. 토큰을 획득하는 모든 메서드에 맞게 조정할 수 있습니다.

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
