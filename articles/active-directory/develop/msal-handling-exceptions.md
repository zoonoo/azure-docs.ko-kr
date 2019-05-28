---
title: 오류 및 예외(MSAL) | Azure
description: MSAL 애플리케이션에서 오류와 예외, 조건부 액세스 및 클레임 챌린지를 처리하는 방법을 알아봅니다.
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/10/2019
ms.author: ryanwi
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: f1972a870ac15e1ca8dde963eef6cf7f1caf3039
ms.sourcegitcommit: f6c85922b9e70bb83879e52c2aec6307c99a0cac
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/11/2019
ms.locfileid: "65544179"
---
# <a name="handling-exceptions-and-errors-using-msal"></a>MSAL을 사용하여 예외 및 오류 처리
MSAL(Microsoft 인증 라이브러리)의 예외는 앱 개발자가 최종 사용자에게 표시하지 않고 문제를 해결하기 위한 것입니다. 예외 메시지는 지역화되지 않았습니다.

예외 및 오류를 처리할 때 예외 유형 자체와 오류 코드를 사용하여 예외를 구별할 수 있습니다.  오류 코드 목록은 [인증 및 권한 부여 오류 코드](reference-aadsts-error-codes.md)를 참조하세요.

## <a name="net-exceptions"></a>.NET 예외
예외를 처리할 때 예외 유형 자체와 `ErrorCode` 멤버를 사용하여 예외를 구별할 수 있습니다. `ErrorCode`의 값은 [MsalError](/dotnet/api/microsoft.identity.client.msalerror?view=azure-dotnet#fields) 형식의 상수입니다.

또한 [MsalClientException](/dotnet/api/microsoft.identity.client.msalexception?view=azure-dotnet#fields), [MsalServiceException](/dotnet/api/microsoft.identity.client.msalserviceexception?view=azure-dotnet#fields), [MsalUIRequiredException](/dotnet/api/microsoft.identity.client.msaluirequiredexception?view=azure-dotnet#fields)의 필드를 살펴볼 수도 있습니다.

[MsalServiceException](/dotnet/api/microsoft.identity.client.msalserviceexception?view=azure-dotnet)가 throw되면 오류 코드에 [인증 및 권한 부여 오류 코드](reference-aadsts-error-codes.md)에서 찾을 수 있는 코드가 포함될 수 있습니다.

### <a name="common-exceptions"></a>일반적인 예외
throw될 수 있는 일반적인 예외와 몇 가지 가능한 완화는 다음과 같습니다.

| 예외 | 오류 코드 | 해결 방법|
| --- | --- | --- |
| [MsalUiRequiredException](/dotnet/api/microsoft.identity.client.msaluirequiredexception?view=azure-dotnet) | AADSTS65001: 사용자 또는 관리자가 '{appName}'이라는' {appId}' ID의 애플리케이션을 사용하는 데 동의하지 않았습니다. 이 사용자 및 리소스에 대한 대화형 권한 부여 요청을 보냅니다.| 먼저 사용자 동의를 받아야 합니다. 웹 UI가 없는 .NET Core를 사용하지 않는 경우 `AcquireTokeninteractive`를 한 번만 호출합니다. .NET Core를 사용하거나 `AcquireTokenInteractive`를 수행하지 않으려는 경우 사용자는 URL(https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id={clientId}&response_type=code&scope=user.read)로 이동하여 동의합니다. `AcquireTokenInteractive`를 호출하려면 `app.AcquireTokenInteractive(scopes).WithAccount(account).WithClaims(ex.Claims).ExecuteAsync();`를 실행합니다.|
| [MsalUiRequiredException](/dotnet/api/microsoft.identity.client.msaluirequiredexception?view=azure-dotnet) | AADSTS50079: 사용자는 다단계 인증을 사용해야 합니다.| 완화가 없습니다. MFA가 테넌트에 대해 구성되어 있고 AAD에서 적용하도록 결정한 경우 `AcquireTokenInteractive` 또는 `AcquireTokenByDeviceCode`와 같은 대화형 흐름으로 대체해야 합니다.|
| [MsalServiceException](/dotnet/api/microsoft.identity.client.msalserviceexception?view=azure-dotnet#fields) |AADSTS90010: 권한 부여 유형은 */common* 또는 */consumers* 엔드포인트에서 지원되지 않습니다. */organizations* 또는 테넌트 특정 엔드포인트를 사용합니다. */common*을 사용했습니다.| Azure AD의 메시지에서 설명한 대로 인증 기관에 테넌트 또는 */organizations*가 있어야 합니다.|
| [MsalServiceException](/dotnet/api/microsoft.identity.client.msalserviceexception?view=azure-dotnet#fields) | AADSTS70002: 요청 본문에는 'client_secret 또는 client_assertion' 매개 변수가 있어야 합니다.| Azure AD에서 애플리케이션이 공용 클라이언트 애플리케이션으로 등록되지 않은 경우에 이 예외가 발생할 수 있습니다. Azure Portal에서 애플리케이션에 대한 매니페스트를 편집하고 `allowPublicClient`를 `true`로 설정합니다. |
| [MsalClientException](/dotnet/api/microsoft.identity.client.msalclientexception?view=azure-dotnet)| unknown_user 메시지: 로그인한 사용자를 식별할 수 없음| 라이브러리에서 현재 Windows에 로그인한 사용자를 쿼리할 수 없거나, 이 사용자가 AD 또는 AAD에 조인되어 있지 않습니다(작업 공간에 조인된 사용자는 지원되지 않음). 완화 1: UWP에서 애플리케이션에 엔터프라이즈 인증, 사설망(클라이언트 및 서버), 사용자 계정 정보와 같은 기능이 있는지 확인합니다. 해결 방법 2: 사용자 이름(예: john@contoso.com)을 가져오도록 사용자 고유의 논리를 구현하고 사용자 이름을 사용하는 `AcquireTokenByIntegratedWindowsAuth` 양식을 사용합니다.|
| [MsalClientException](/dotnet/api/microsoft.identity.client.msalclientexception?view=azure-dotnet)|integrated_windows_auth_not_supported_managed_user| 이 메서드는 AD(Active Directory)에서 공개되는 프로토콜을 사용합니다. 사용자("관리" 사용자)를 AD 지원 없이 Azure Active Directory에서 만든 경우 이 메서드는 실패합니다. AD에서 만들어지고 AAD에서 지원하는 사용자("페더레이션" 사용자)는 이 비대화형 인증 메서드를 활용할 수 있습니다. 완화: 대화형 인증을 사용합니다.|

## <a name="javascript-errors"></a>JavaScript 오류

MSAL.js는 다양한 유형의 일반 오류를 추상화 및 분류하고 오류 메시지와 같은 오류의 특정 세부 정보에 액세스하여 해당 오류를 적절하게 처리할 수 있는 인터페이스가 있는 Error 개체를 제공합니다.

**Error 개체**

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
* **AuthError.message:** errorMessage와 같습니다.
* **AuthError.stack:** throw된 오류에 대한 스택 추적입니다. 오류의 원점에 대한 추적을 허용합니다.

**오류 유형**

사용할 수 있는 오류 유형은 다음과 같습니다.

* *AuthError:* MSAL.js 라이브러리의 기본 오류 클래스이며, 예기치 않은 오류에도 사용됩니다.

* *ClientAuthError:* 클라이언트 인증 관련 문제를 나타내는 오류 클래스입니다. 라이브러리에서 발생하는 대부분의 오류는 ClientAuthErrors입니다. 로그인이 진행 중일 때의 로그인 메서드 호출 또는 사용자의 로그인 취소와 같은 오류일 수 있습니다.

* *ClientConfigurationError:* 지정한 사용자 구성 매개 변수가 누락되었거나 형식이 잘못된 경우 요청하기 전에 throw되는 ClientAuthError를 확장하는 오류 클래스입니다.

* *ServerError:* 인증 서버에서 보낸 오류 문자열을 나타내는 오류 클래스입니다. 잘못된 요청 형식 또는 매개 변수와 같은 오류 또는 서버에서 사용자를 인증하거나 사용자에게 권한을 부여하지 못하도록 하는 다른 오류일 수 있습니다.

* *InteractionRequiredAuthError:* 대화형 호출이 필요한 서버 오류를 나타내는 ServerError를 확장하는 오류 클래스입니다. 인증/권한 부여에 대한 자격 증명 또는 동의를 제공하기 위해 서버와 상호 작용해야 하는 경우 `acquireTokenSilent`에서 이 오류 유형을 throw합니다. 오류 코드에는 "interaction_required", "login_required", "consent_required"가 포함됩니다.

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

### <a name="interaction-required-errors"></a>상호 작용에 필요한 오류

UI 상호 작용이 필요한 경우 오류가 반환됩니다. 즉, 토큰(예: `acquireTokenSilent`)을 획득하는 비대화형 메서드를 사용하려고 했지만 MSAL에서 이를 자동으로 수행할 수 없습니다. 가능한 원인은 다음과 같습니다.

* 로그인해야 합니다.
* 동의해야 합니다.
* 다단계 인증 환경을 거쳐야 합니다.

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

## <a name="conditional-access-and-claims-challenges"></a>조건부 액세스 및 클레임 챌린지
토큰을 자동으로 가져올 때 액세스하려는 API에서 [조건부 액세스 클레임 챌린지](conditional-access-dev-guide.md)와 같은 MFA 정책이 필요한 경우 애플리케이션에서 오류가 발생할 수 있습니다.

이 오류를 처리하는 패턴은 MSAL을 사용하여 토큰을 대화형으로 획득하는 것입니다. 토큰을 대화형으로 획득하면 사용자에게 메시지가 표시되어 필요한 조건부 액세스 정책을 충족시킬 수 있는 기회가 제공됩니다.

조건부 액세스가 필요한 API를 호출하는 경우 클레임 챌린지를 API의 오류로 받을 수 있습니다. 예를 들어 조건부 액세스 정책에 관리 디바이스(Intune)가 있는 경우 오류는 [AADSTS53000: 이 리소스에 액세스하려면 디바이스가 관리되어야 합니다](reference-aadsts-error-codes.md)이거나 이와 비슷합니다. 이 경우 토큰 획득 호출에서 클레임을 전달하여 사용자에게 적절한 정책을 충족시키라는 메시지가 표시되도록 할 수 있습니다.

### <a name="net"></a>.NET
MSAL.NET에서 조건부 액세스가 필요한 API를 호출하는 경우 애플리케이션에서 클레임 챌린지 예외를 처리해야 합니다. 이 예외는 [Claims](/dotnet/api/microsoft.identity.client.msalserviceexception.claims?view=azure-dotnet) 속성이 비어 있지 않은 [MsalServiceException](/dotnet/api/microsoft.identity.client.msalserviceexception?view=azure-dotnet)으로 표시됩니다.

클레임 챌린지를 처리하려면 `PublicClientApplicationBuilder` 클래스의 `.WithClaim()` 메서드를 사용해야 합니다.

### <a name="javascript"></a>JavaScript
MSAL.js를 사용하여 자동으로 토큰을 가져올 때(`acquireTokenSilent` 사용) 액세스하려는 API에서 MFA 정책과 같은 [조건부 액세스 클레임 챌린지](conditional-access-dev-guide.md)를 요구하면 애플리케이션에서 오류가 발생할 수 있습니다.

이 오류를 처리하는 패턴은 다음 예제와 같이 MSAL.js에서 토큰(예: `acquireTokenPopup` 또는 `acquireTokenRedirect`)을 획득하기 위해 대화형 호출을 수행하는 것입니다.

```javascript
myMSALObj.acquireTokenSilent(accessTokenRequest).then(function (accessTokenResponse) {
    // call API
}).catch( function (error) {
    // call acquireTokenPopup in case of acquireTokenSilent failure
    myMSALObj.acquireTokenPopup(accessTokenRequest).then(
        function (accessTokenResponse) {
            // call API
        }).catch(function (error) {
            console.log(error);
        });
});
```

토큰을 대화형으로 획득하면 사용자에게 메시지가 표시되어 필요한 조건부 액세스 정책을 충족시킬 수 있는 기회가 제공됩니다.

조건부 액세스가 필요한 API를 호출할 때 클레임 챌린지를 API의 오류로 받을 수 있습니다. 이 경우 토큰 획득 호출에서 오류에 반환된 클레임을 `extraQueryParameters`로 전달하여 사용자에게 적절한 정책을 충족시키라는 메시지가 표시되도록 할 수 있습니다.

```javascript
var request = {
    scopes: ["user.read"],
    extraQueryParameters: {claims: claims}
}

myMSALObj.acquireTokenPopup(request);
```

## <a name="retrying-after-errors-and-exceptions"></a>오류 및 예외 후 다시 시도

### <a name="http-error-codes-500-600"></a>500-600 HTTP 오류 코드
MSAL.NET은 500-600 HTTP 오류 코드의 오류에 대한 간단한 일회성 다시 시도 메커니즘을 구현합니다.

### <a name="http-429"></a>429 HTTP
"너무 많은 요청"으로 인해 STS(서비스 토큰 서버)가 너무 많이 사용되는 경우 지연 시간(초) 또는 날짜로 다시 시도할 수 있는 시기에 대한 힌트(다음 시간 후 다시 시도 응답 필드)가 있는 429 HTTP 오류를 반환합니다.

#### <a name="net"></a>.NET
[MsalServiceException](/dotnet/api/microsoft.identity.client.msalserviceexception?view=azure-dotnet) 예외는 `System.Net.Http.Headers.HttpResponseHeaders`를 `namedHeaders` 속성으로 표시합니다. 따라서 오류 코드의 추가 정보를 활용하여 애플리케이션의 안정성을 향상시킬 수 있습니다. 방금 설명한 경우에는 `RetryAfterproperty`(`RetryConditionHeaderValue` 형식)를 사용하여 다시 시도 시기를 계산할 수 있습니다.

다음은 클라이언트 자격 증명 흐름을 사용하는 디먼 애플리케이션에 대한 예제이지만, 토큰을 획득하는 방법 중 하나에 이를 적용할 수 있습니다.

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
