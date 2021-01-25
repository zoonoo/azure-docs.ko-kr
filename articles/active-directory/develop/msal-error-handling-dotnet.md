---
title: MSAL.NET에서 오류 및 예외 처리
titleSuffix: Microsoft identity platform
description: MSAL.NET에서 오류 및 예외를 처리 하는 방법, 조건부 액세스 클레임 문제 및 재시도를 처리 하는 방법에 대해 알아봅니다.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 11/26/2020
ms.author: marsma
ms.reviewer: saeeda, jmprieur
ms.custom: aaddev
ms.openlocfilehash: 381416384cacd44bdb1b08801f7b3174c9504d0b
ms.sourcegitcommit: 3c3ec8cd21f2b0671bcd2230fc22e4b4adb11ce7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/25/2021
ms.locfileid: "98761184"
---
# <a name="handle-errors-and-exceptions-in-msalnet"></a>MSAL.NET에서 오류 및 예외 처리

[!INCLUDE [Active directory error handling introduction](../../../includes/active-directory-develop-error-handling-introduction.md)]

## <a name="error-handling-in-msalnet"></a>MSAL.NET의 오류 처리

.NET 예외를 처리할 때 예외 유형 자체와 `ErrorCode` 멤버를 사용하여 예외를 구별할 수 있습니다. `ErrorCode`의 값은 [MsalError](/dotnet/api/microsoft.identity.client.msalerror) 형식의 상수입니다.

또한 [MsalClientException](/dotnet/api/microsoft.identity.client.msalexception), [MsalServiceException](/dotnet/api/microsoft.identity.client.msalserviceexception) 및 [MsalUIRequiredException](/dotnet/api/microsoft.identity.client.msaluirequiredexception)의 필드를 살펴볼 수도 있습니다.

[MsalServiceException](/dotnet/api/microsoft.identity.client.msalserviceexception)이 throw되면 [인증 및 권한 부여 오류 코드](reference-aadsts-error-codes.md)를 확인하여 코드가 해당 목록에 표시되는지 알아봅니다.

### <a name="common-net-exceptions"></a>일반적인 .NET 예외

throw될 수 있는 일반적인 예외와 몇 가지 가능한 완화는 다음과 같습니다.  

| 예외 | 오류 코드 | 완화 방법|
| --- | --- | --- |
| [MsalUiRequiredException](/dotnet/api/microsoft.identity.client.msaluirequiredexception) | AADSTS65001: 사용자 또는 관리자가 '{appName}'이라는' {appId}' ID의 애플리케이션을 사용하는 데 동의하지 않았습니다. 이 사용자 및 리소스에 대한 대화형 권한 부여 요청을 보냅니다.| 먼저 사용자 동의를 받아야 합니다. 웹 UI가 없는 .NET Core를 사용하지 않는 경우 `AcquireTokeninteractive`를 한 번만 호출합니다. .NET Core를 사용하거나 `AcquireTokenInteractive`를 수행하지 않으려는 경우 사용자는 URL(`https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id={clientId}&response_type=code&scope=user.read`)로 이동하여 동의합니다. `AcquireTokenInteractive`를 호출하려면 `app.AcquireTokenInteractive(scopes).WithAccount(account).WithClaims(ex.Claims).ExecuteAsync();`를 실행합니다.|
| [MsalUiRequiredException](/dotnet/api/microsoft.identity.client.msaluirequiredexception) | AADSTS50079: 사용자는 [MFA(Multi-Factor Authentication)](../authentication/concept-mfa-howitworks.md)를 사용해야 합니다.| 완화가 없습니다. MFA가 테넌트에 대해 구성되어 있고 AAD(Azure Active Directory)에서 적용하도록 결정한 경우 `AcquireTokenInteractive` 또는 `AcquireTokenByDeviceCode`와 같은 대화형 흐름으로 대체해야 합니다.|
| [MsalServiceException](/dotnet/api/microsoft.identity.client.msalserviceexception) |AADSTS90010: 권한 부여 유형은 */common* 또는 */consumers* 엔드포인트에서 지원되지 않습니다. */organizations* 또는 테넌트 특정 엔드포인트를 사용합니다. */common* 을 사용했습니다.| Azure AD의 메시지에서 설명한 대로 인증 기관에 테넌트 또는 */organizations* 가 있어야 합니다.|
| [MsalServiceException](/dotnet/api/microsoft.identity.client.msalserviceexception) | AADSTS70002: 요청 본문에는 `client_secret or client_assertion` 매개 변수가 있어야 합니다.| Azure AD에서 애플리케이션이 공용 클라이언트 애플리케이션으로 등록되지 않은 경우에 이 예외가 throw될 수 있습니다. Azure Portal에서 애플리케이션에 대한 매니페스트를 편집하고 `allowPublicClient`를 `true`로 설정합니다. |
| [MsalClientException](/dotnet/api/microsoft.identity.client.msalclientexception)| `unknown_user Message`: 로그인한 사용자를 식별할 수 없음| 라이브러리가 현재 Windows 로그인 사용자를 쿼리하지 못했습니다. 또는이 사용자가 AD 또는 Azure AD에 연결 되지 않았습니다 (작업에 참가 한 사용자가 지원 되지 않음). 완화 1: UWP에서 애플리케이션에 엔터프라이즈 인증, 사설망(클라이언트 및 서버), 사용자 계정 정보와 같은 기능이 있는지 확인합니다. 해결 방법 2: 사용자 이름(예: john@contoso.com)을 가져오도록 사용자 고유의 논리를 구현하고 사용자 이름을 사용하는 `AcquireTokenByIntegratedWindowsAuth` 양식을 사용합니다.|
| [MsalClientException](/dotnet/api/microsoft.identity.client.msalclientexception)|integrated_windows_auth_not_supported_managed_user| 이 메서드는 AD(Active Directory)에서 공개되는 프로토콜을 사용합니다. 사용자가 AD 백업 ("관리" 사용자) 없이 Azure AD에서 만들어진 경우이 메서드는 실패 합니다. AD에서 만들고 Azure AD ("페더레이션된" 사용자)가 지 원하는 사용자는 이러한 비 대화형 인증 방법으로 이점을 누릴 수 있습니다. 마이그레이션: 대화형 인증을 사용합니다.|

### `MsalUiRequiredException`

`AcquireTokenSilent()`를 호출할 때 MSAL.NET에서 반환되는 일반적인 상태 코드 중 하나는 `MsalError.InvalidGrantError`입니다. 이 상태 코드는 응용 프로그램이 인증 라이브러리를 다시 호출 하지만 대화형 모드 (공용 클라이언트 응용 프로그램의 경우 AcquireTokenInteractive 또는 AcquireTokenByDeviceCodeFlow)에서 웹 앱에 문제가 있음을 의미 합니다. 인증 토큰을 발급하려면 추가 사용자 조작이 필요하기 때문입니다.

`AcquireTokenSilent`가 실패하는 대부분의 경우는 토큰 캐시에 요청과 일치하는 토큰이 없기 때문입니다. 액세스 토큰은 1시간 후에 만료되고 `AcquireTokenSilent`는 새로 고침 토큰(OAuth2 용어로는 "새로 고침 토큰 ' 흐름)을 기준으로 새 토큰을 가져오려고 합니다. 이 흐름은 테넌트 관리자가 보다 엄격한 로그인 정책을 구성하는 등, 여러 가지 이유로 실패할 수도 있습니다. 

상호 작용은 사용자가 작업을 수행하도록 하는 것을 목표로 합니다. 클릭 한 번으로 사용 약관에 동의하는 것과 같이 사용자가 쉽게 해결할 수 있는 상황도 있고, 컴퓨터에서 특정 회사 네트워크에 연결해야 하는 경우처럼 현재 구성으로 해결할 수 없는 상황도 있습니다. 사용자가 Multi-Factor Authentication을 설정하거나 디바이스에 Microsoft Authenticator를 설치하는 데 도움이 되는 상황도 있습니다.

### <a name="msaluirequiredexception-classification-enumeration"></a>`MsalUiRequiredException` 분류 열거

MSAL은 `Classification` 더 나은 사용자 환경을 제공 하기 위해 읽을 수 있는 필드를 노출 합니다. 예를 들어 사용자가 암호를 만료 했거나 일부 리소스를 사용 하는 데 동의 해야 함을 사용자에 게 알립니다. 지원되는 값은 `UiRequiredExceptionClassification` 열거형에 속합니다.

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
[!INCLUDE [Active directory error handling claims challenges](../../../includes/active-directory-develop-error-handling-claims-challenges.md)]

MSAL.NET에서 조건부 액세스가 필요한 API를 호출하는 경우 애플리케이션에서 클레임 챌린지 예외를 처리해야 합니다. 이 예외는 [Claims](/dotnet/api/microsoft.identity.client.msalserviceexception.claims) 속성이 비어 있지 않은 [MsalServiceException](/dotnet/api/microsoft.identity.client.msalserviceexception)으로 표시됩니다.

클레임 챌린지를 처리하려면 `PublicClientApplicationBuilder` 클래스의 `.WithClaim()` 메서드를 사용해야 합니다.

[!INCLUDE [Active directory error handling retries](../../../includes/active-directory-develop-error-handling-retries.md)]

### <a name="http-error-codes-500-600"></a>500-600 HTTP 오류 코드

MSAL.NET은 500-600 HTTP 오류 코드의 오류에 대한 간단한 일회성 다시 시도 메커니즘을 구현합니다.

[MsalServiceException](/dotnet/api/microsoft.identity.client.msalserviceexception)은 `System.Net.Http.Headers.HttpResponseHeaders`를 `namedHeaders` 속성으로 표시합니다. 오류 코드의 추가 정보를 사용하여 애플리케이션의 안정성을 향상시킬 수 있습니다. 설명된 경우에서 `RetryAfterproperty`(`RetryConditionHeaderValue` 형식)를 사용하여 다시 시도 시기를 계산할 수 있습니다.

클라이언트 자격 증명 흐름을 사용하는 디먼 애플리케이션 예제는 다음과 같습니다. 토큰을 획득하는 방법에 이 예제를 적용할 수 있습니다.

```csharp

bool retry = false;
do
{
    TimeSpan? delay;
    try
    {
         result = await publicClientApplication.AcquireTokenForClient(scopes, account).ExecuteAsync();
    }
    catch (MsalServiceException serviceException)
    {
         if (serviceException.ErrorCode == "temporarily_unavailable")
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
    // . . .
    if (delay.HasValue)
    {
        Thread.Sleep((int)delay.Value.TotalMilliseconds); // sleep or other
        retry = true;
    }
} while (retry);
```

## <a name="next-steps"></a>다음 단계

문제를 진단 하 고 디버그할 수 있도록 [MSAL.NET에서 로깅을](msal-logging-dotnet.md) 사용 하도록 설정 하는 것이 좋습니다.
