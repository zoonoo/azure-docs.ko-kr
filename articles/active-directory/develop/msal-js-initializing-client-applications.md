---
title: 클라이언트 앱 MSAL.js 초기화 | Microsoft
titleSuffix: Microsoft identity platform
description: JavaScript 용 Microsoft Authentication Library (MSAL.js)를 사용 하 여 클라이언트 응용 프로그램을 초기화 하는 방법을 알아봅니다.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 07/17/2020
ms.author: marsma
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 5af5d3a88262792f4b32e2ce3d8143ac680f083a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "87027039"
---
# <a name="initialize-client-applications-using-msaljs"></a>MSAL.js를 사용 하 여 클라이언트 응용 프로그램 초기화

이 문서에서는 사용자 에이전트 응용 프로그램의 인스턴스를 사용 하 여 JavaScript 용 Microsoft Authentication Library (MSAL.js)를 초기화 하는 방법을 설명 합니다.

사용자 에이전트 응용 프로그램은 클라이언트 코드가 웹 브라우저와 같은 사용자 에이전트에서 실행 되는 공용 클라이언트 응용 프로그램의 형태입니다. 브라우저 컨텍스트는 공개적으로 액세스할 수 있기 때문에 이러한 클라이언트는 비밀을 저장 하지 않습니다.

클라이언트 응용 프로그램 유형 및 응용 프로그램 구성 옵션에 대 한 자세한 내용은 [MSAL의 공용 및 기밀 클라이언트 앱](msal-client-applications.md)을 참조 하세요.

## <a name="prerequisites"></a>사전 요구 사항

응용 프로그램을 초기화 하기 전에 먼저 Azure Portal에 [등록](scenario-spa-app-registration.md)하 여 응용 프로그램과 Microsoft id 플랫폼 간의 트러스트 관계를 설정 해야 합니다.

앱을 등록 한 후 Azure Portal에서 찾을 수 있는 다음 값의 일부 또는 모두가 필요 합니다.

| 값 | 필수 | 설명 |
|:----- | :------: | :---------- |
| 애플리케이션(클라이언트) ID | 필수 | Microsoft id 플랫폼 내에서 응용 프로그램을 고유 하 게 식별 하는 GUID입니다. |
| Authority | 선택 사항 | 응용 프로그램에 대 한 id 공급자 URL ( *인스턴스*) 및 *로그인 대상* 입니다. 인스턴스와 로그인 대상이 연결 되 면 *인증 기관*을 구성 합니다. |
| 디렉터리(테넌트) ID | 선택 사항 | 조직 전용 lob (기간 업무) 응용 프로그램을 작성 하는 경우 (종종 *단일 테 넌 트 응용 프로그램*이라고 함)이를 지정 합니다. |
| 리디렉션 URI | 선택 사항 | 웹 앱을 빌드하는 경우는 `redirectUri` id 공급자 (Microsoft id 플랫폼)에서 발급 한 보안 토큰을 반환 해야 하는 위치를 지정 합니다. |

## <a name="initialize-msaljs-2x-apps"></a>MSAL.js 2.x 앱 초기화

[구성][msal-js-configuration] 개체를 사용 하 여 [publicclientapplication][msal-js-publicclientapplication] 을 인스턴스화하여 msal 인증 컨텍스트를 초기화 합니다. 필요한 최소 구성 속성은 `clientID` Azure Portal에서 앱 등록의 **개요** 페이지에 **응용 프로그램 (클라이언트) ID** 로 표시 되는 응용 프로그램의입니다.

다음은의 구성 개체 및 인스턴스화에 대 한 예제입니다 `PublicClientApplication` .

```javascript
const msalConfig = {
    auth: {
        clientId: "11111111-1111-1111-111111111111",
        authority: "https://login.microsoftonline.com/common",
        knownAuthorities: [],
        redirectUri: "https://localhost:3001",
        postLogoutRedirectUri: "https://localhost:3001/logout",
        navigateToLoginRequestUrl: true
    },
    cache: {
        cacheLocation: "sessionStorage",
        storeAuthStateInCookie: false
    },
    system: {
        loggerOptions: {
            loggerCallback: (level: LogLevel, message: string, containsPii: boolean): void => {
                if (containsPii) {
                    return;
                }
                switch (level) {
                    case LogLevel.Error:
                        console.error(message);
                        return;
                    case LogLevel.Info:
                        console.info(message);
                        return;
                    case LogLevel.Verbose:
                        console.debug(message);
                        return;
                    case LogLevel.Warning:
                        console.warn(message);
                        return;
                }
            },
            piiLoggingEnabled: false
        },
        windowHashTimeout: 60000,
        iframeHashTimeout: 6000,
        loadFrameTimeout: 0
    }
};

// Create an instance of PublicClientApplication
const msalInstance = new PublicClientApplication(msalConfig);

// Handle the redirect flows
msalInstance.handleRedirectPromise().then((tokenResponse) => {
    // Handle redirect response
}).catch((error) => {
    // Handle redirect error
});
```

### `handleRedirectPromise`

응용 프로그램에서 리디렉션 흐름을 사용 하는 경우 [handleRedirectPromise][msal-js-handleredirectpromise] 를 호출 합니다. 리디렉션 흐름을 사용 하는 경우 `handleRedirectPromise` 모든 페이지 로드에서를 실행 해야 합니다.

약속의 세 가지 가능한 결과는 다음과 같습니다.

- `.then` 가 호출 되 고 `tokenResponse` truthy: 응용 프로그램이 성공한 리디렉션 작업에서 반환 됩니다.
- `.then` 가 호출 되 고 `tokenResponse` 가 falsey ( `null` ): 응용 프로그램이 리디렉션 작업에서 반환 되지 않습니다.
- `.catch` 호출 됨: 응용 프로그램이 리디렉션 작업에서 반환 되 고 오류가 발생 했습니다.

## <a name="initialize-msaljs-1x-apps"></a>1.x 앱 MSAL.js 초기화

구성 개체를 사용 하 여 [Useragentapplication][msal-js-useragentapplication] 을 인스턴스화하여 msal 1.x 인증 컨텍스트를 초기화 합니다. 필요한 최소 구성 속성은 `clientID` Azure Portal에서 앱 등록의 **개요** 페이지에 **응용 프로그램 (클라이언트) ID** 로 표시 되는 응용 프로그램의입니다.

MSAL.js 1.2. x 또는 그 이전 버전에서 리디렉션 흐름 ([loginRedirect][msal-js-loginredirect] 및 [acquireTokenRedirect][msal-js-acquiretokenredirect])을 사용 하는 인증 방법에 대해서는 메서드를 통해 성공 또는 오류에 대 한 콜백을 명시적으로 등록 해야 합니다. `handleRedirectCallback()` 명시적으로 콜백을 등록 하려면 MSAL.js 1.2. x 및 이전 버전에서 필요 합니다 .이 경우 리디렉션 흐름은 팝업 환경을 사용 하는 메서드와 같은 약속을 반환 하지 않기 때문입니다. MSAL.js 버전 1.3. x 이상에서는 콜백을 *등록할 수 있습니다* .

```javascript
// Configuration object constructed
const msalConfig = {
    auth: {
        clientId: "11111111-1111-1111-111111111111"
    }
}

// Create UserAgentApplication instance
const msalInstance = new UserAgentApplication(msalConfig);

function authCallback(error, response) {
    // Handle redirect response
}

// Register a redirect callback for Success or Error (when using redirect methods)
// **REQUIRED** in MSAL.js 1.2.x and earlier
// **OPTIONAL** in MSAL.js 1.3.x and later
msalInstance.handleRedirectCallback(authCallback);
```

## <a name="single-instance-and-configuration"></a>단일 인스턴스 및 구성

MSAL.js 1.x 및 2.x 모두 단일 인스턴스 및 구성 (각각 또는)을 포함 하 여 `UserAgentApplication` `PublicClientApplication` 단일 인증 컨텍스트를 나타냅니다.

또는의 여러 인스턴스는 `UserAgentApplication` `PublicClientApplication` 브라우저에서 충돌 하는 캐시 항목 및 동작을 야기 하므로 권장 되지 않습니다.

## <a name="next-steps"></a>다음 단계

GitHub의이 MSAL.js 2.x 코드 샘플에서는 [구성][msal-js-configuration] 개체를 사용 하 여 [publicclientapplication][msal-js-publicclientapplication] 의 인스턴스화를 보여 줍니다.

[Azure-Samples/ms-identity-javascript-v2](https://github.com/Azure-Samples/ms-identity-javascript-v2)

<!-- LINKS - External -->
[msal-browser]: https://azuread.github.io/microsoft-authentication-library-for-js/ref/msal-browser/
[msal-core]: https://azuread.github.io/microsoft-authentication-library-for-js/ref/msal-core/
[msal-js-acquiretokenredirect]: https://azuread.github.io/microsoft-authentication-library-for-js/ref/msal-core/classes/_useragentapplication_.useragentapplication.html#acquiretokenredirect
[msal-js-configuration]: https://azuread.github.io/microsoft-authentication-library-for-js/ref/msal-core/modules/_configuration_.html
[msal-js-handleredirectpromise]: https://azuread.github.io/microsoft-authentication-library-for-js/ref/msal-browser/classes/_src_app_publicclientapplication_.publicclientapplication.html#handleredirectpromise
[msal-js-loginredirect]: https://azuread.github.io/microsoft-authentication-library-for-js/ref/msal-core/classes/_useragentapplication_.useragentapplication.html#loginredirect
[msal-js-publicclientapplication]: https://azuread.github.io/microsoft-authentication-library-for-js/ref/msal-browser/classes/_src_app_publicclientapplication_.publicclientapplication.html
[msal-js-useragentapplication]: https://azuread.github.io/microsoft-authentication-library-for-js/ref/msal-core/modules/_useragentapplication_.html
