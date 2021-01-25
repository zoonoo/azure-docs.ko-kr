---
title: MSAL.js에서 오류 및 예외 처리
titleSuffix: Microsoft identity platform
description: 오류 및 예외를 처리 하는 방법, 조건부 액세스 클레임 문제 및 MSAL.js 응용 프로그램에서 다시 시도 하는 방법에 대해 알아봅니다.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 11/26/2020
ms.author: marsma
ms.reviewer: saeeda, hahamil
ms.custom: aaddev
ms.openlocfilehash: 20d276aba2ee3260911748cbee0a16020270059a
ms.sourcegitcommit: 3c3ec8cd21f2b0671bcd2230fc22e4b4adb11ce7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/25/2021
ms.locfileid: "98761348"
---
# <a name="handle-errors-and-exceptions-in-msaljs"></a>MSAL.js에서 오류 및 예외 처리

[!INCLUDE [Active directory error handling introduction](../../../includes/active-directory-develop-error-handling-introduction.md)]

## <a name="error-handling-in-msaljs"></a>MSAL.js의 오류 처리

MSAL.js는 여러 유형의 일반적인 오류를 추상화하고 분류 하는 오류 개체를 제공합니다. 또한 오류 메시지와 같은 오류에 대 한 특정 세부 정보에 액세스 하 여 적절 하 게 처리할 수 있는 인터페이스도 제공 합니다.

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

[!INCLUDE [Active directory error handling claims challenges](../../../includes/active-directory-develop-error-handling-claims-challenges.md)]

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


[!INCLUDE [Active directory error handling retries](../../../includes/active-directory-develop-error-handling-retries.md)]

## <a name="next-steps"></a>다음 단계

문제를 진단 하 고 디버깅 하는 데 도움이 되도록 [MSAL.js](msal-logging-js.md) 로그인을 사용 하는 것이 좋습니다.
