---
title: ADAL에서 MSAL로 Node.js 애플리케이션 마이그레이션 | Azure
titleSuffix: Microsoft identity platform
description: ADAL(Active Directory 인증 라이브러리) 대신 인증 및 권한 부여에 MSAL (Microsoft 인증 라이브러리)을 사용하도록 기존 Node.js 애플리케이션을 업데이트하는 방법입니다.
services: active-directory
author: derisen
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: how-to
ms.workload: identity
ms.date: 04/26/2021
ms.author: v-doeris
ms.openlocfilehash: e2b82976c84d838f8c774cfba39edb630cbceb61
ms.sourcegitcommit: 4a54c268400b4158b78bb1d37235b79409cb5816
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2021
ms.locfileid: "108124296"
---
# <a name="how-to-migrate-a-nodejs-app-from-adal-to-msal"></a>ADAL에서 MSAL로 Node.js 앱을 마이그레이션하는 방법

이제 [Microsoft Authentication Library for Node](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-node)(MSAL Node)는 Microsoft ID 플랫폼에 등록된 애플리케이션에 대한 인증 및 권한 부여를 사용하도록 설정하는 데 권장되는 SDK입니다. 이 문서에서는 Active Directory Authentication Library for Node(ADAL Node)에서 MSAL Node로 앱을 마이그레이션하기 위해 수행해야 하는 중요한 단계에 대해 설명합니다.

## <a name="prerequisites"></a>사전 요구 사항

- Node 버전 10, 12 또는 14 [버전 지원에 대한 참고](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-node#node-version-support)를 참조하세요.

## <a name="update-app-registration-settings"></a>앱 등록 설정 업데이트

ADAL Node로 작업할 때 **Azure AD v1.0 엔드포인트** 를 사용하고 있을 것입니다. ADAL에서 MSAL으로 마이그레이션하는 앱은 **Azure AD v2.0 엔드포인트** 로 전환하는 것도 고려해야 합니다.

1. [v1 엔드포인트와 v2 엔드포인트의 차이점](https://docs.microsoft.com/azure/active-directory/azuread-dev/azure-ad-endpoint-comparison)을 검토합니다.
1. 필요에 따라 기존 앱 등록을 업데이트합니다.

> [!NOTE]
> 이전 버전과의 호환성을 보장하기 위해 MSAL Node는 v1.0 엔드 v2.0 엔드포인트 모두를 지원합니다.

## <a name="install-and-import-msal"></a>MSAL 설치 및 가져오기

1. NPM를 통해 MSAL Node 패키지를 설치합니다.

```console
npm install @azure/msal-node
```

1. 그런 다음 코드에서 MSAL Node를 가져옵니다.

```javascript
const msal = require('@azure/msal-node');
```

1. 마지막으로, ADAL Node 패키지를 제거하고 코드에서 모든 참조를 제거합니다.

```console
npm uninstall adal-node
```

## <a name="initialize-msal"></a>MSAL 초기화

ADAL Node에서 `AuthenticationContext` 개체를 초기화합니다. 그러면 다른 인증 흐름(예: 웹앱용 `acquireTokenWithAuthorizationCode`)에서 사용할 수 있는 메서드가 공개됩니다. 초기화할 때 필수 매개 변수는 다음과 같은 **인증 기관 URI** 입니다.

```javascript
var adal = require('adal-node');

var authorityURI = "https://login.microsoftonline.com/common";
var authenticationContex = new adal.AuthenticationContext(authorityURI);
```

MSAL Node에는 대신 두 가지 대안이 있습니다. 모바일 앱 또는 데스크톱 앱을 빌드하는 경우 `PublicClientApplication` 개체를 인스턴스화합니다. 생성자에는 최소한 `clientId` 매개 변수를 포함하는 [구성 개체](#configure-msal)가 필요합니다. MSAL은 인증 기관 URI를 지정하지 않는 경우 `https://login.microsoftonline.com/common`을 기본값으로 설정합니다.

```javascript
const msal = require('@azure/msal-node');

const pca = new msal.PublicClientApplication({
        auth: {
            clientId = "YOUR_CLIENT_ID"
        }
    });
```

> [!NOTE]
> v2.0에서 `https://login.microsoftonline.com/common` 인증 기관을 사용하는 경우 사용자가 모든 Azure AD 조직 또는 MSA(개인 Microsoft 계정)를 사용하여 로그인하도록 허용할 수 있습니다. MSAL Node에서 Azure AD 계정에 대한 로그인(ADAL Node와 동일한 동작)을 제한하려면 `https://login.microsoftonline.com/organizations`를 사용해야 합니다.

반면, 웹앱 또는 디먼 앱을 빌드하는 경우에는 `ConfidentialClientApplication` 개체를 인스턴스화합니다. 이러한 앱을 사용하면 클라이언트 암호 또는 인증서와 같은 ‘클라이언트 자격 증명’을 제공해야 합니다.

```javascript
const msal = require('@azure/msal-node');

const cca = new msal.ConfidentialClientApplication({
        auth: {
            clientId = "YOUR_CLIENT_ID",
            clientSecret = "YOUR_CLIENT_SECRET"
        }
    });
```

ADAL의 `AuthenticationContext`와 달리 `PublicClientApplication` 및 `ConfidentialClientApplication`은 모두 클라이언트 ID에 바인딩됩니다. 즉, 애플리케이션에서 사용하려는 클라이언트 ID가 서로 다른 경우 각각에 대해 새 MSAL 인스턴스를 인스턴스화해야 합니다. 자세한 정보는 다음을 참조 하세요. [MSAL Node 초기화](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-node/docs/initialize-confidential-client-application.md)

## <a name="configure-msal"></a>MSAL 구성

Microsoft ID 플랫폼에서 앱을 빌드할 때 앱은 인증과 관련된 많은 매개 변수를 포함합니다. ADAL Node에서는 `AuthenticationContext` 개체에 인스턴스화할 수 있는 구성 매개 변수의 수가 제한되어 있지만 나머지 매개 변수는 코드에서 자유롭게 중단됩니다(예: *clientSecret*).

```javascript
var adal = require('adal-node');

var authority = "https://login.microsoftonline.com/YOUR_TENANT_ID"
var validateAuthority = true,
var cache = null;

var authenticationContext = new adal.AuthenticationContext(authority, validateAuthority, cache);
```

- `authority`: 토큰 인증 기관을 식별하는 URL
- `validateAuthority`: 코드가 잠재적 악성 인증 기관에서 토큰을 요청하지 못하도록 하는 기능
- `cache`이 AuthenticationContext 인스턴스에서 사용하는 토큰 캐시를 설정합니다.  이 매개 변수를 설정하지 않으면 메모리 캐시의 기본값이 사용됩니다.

반면에 MSAL Node는 [구성](https://azuread.github.io/microsoft-authentication-library-for-js/ref/modules/_azure_msal_node.html#configuration)유형의 구성 개체를 사용합니다. 다음 속성을 포함합니다.

```javascript
const msal = require('@azure/msal-node');

const msalConfig = {
    auth: {
        clientId: "YOUR_CLIENT_ID",
        authority: "https://login.microsoftonline.com/YOUR_TENANT_ID",
        clientSecret: "YOUR_TENANT_ID",
        knownAuthorities: [], 
    },
    cache: {
        // your implementation of caching
    },
    system: {
        loggerOptions: { /** logging related options */ }
    }
}


const cca = new msal.ConfidentialClientApplication(msalConfig);
```

주목할 만한 차이점으로, MSAL은 권한 유효성 검사를 사용하지 않도록 설정하는 플래그가 없으며 인증 기관은 항상 기본값으로 유효성 검사가 이루어집니다. MSAL은 요청된 인증 기관을 Microsoft에 알려진 인증 기관 목록과 비교하여 구성에서 지정된 인증 기관 목록과 비교합니다. 자세한 정보는 [구성 옵션](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-node/docs/configuration.md)을 참조하세요.

## <a name="enable-logging"></a>로깅 사용

ADAL Node에서는 코드의 어느 위치에서나 별도로 로깅을 구성합니다.

```javascript
var adal = require('adal-node');

//PII or OII logging disabled. Default Logger does not capture any PII or OII.
adal.logging.setLoggingOptions({
  log: function (level, message, error) {
    console.log(message);

    if (error) {
      console.log(error);
    }
  },
  level: logging.LOGGING_LEVEL.VERBOSE, // provide the logging level
  loggingWithPII: false  // Determine if you want to log personal identification information. The default value is false.
});
```

MSAL Node에서 로깅은 구성 옵션의 일부이며 MSAL Node 인스턴스를 초기화하여 생성됩니다.

```javascript
const msal = require('@azure/msal-node');

const msalConfig = {
    auth: {
        // authentication related parameters
    },
    cache: {
        // cache related parameters
    },
    system: {
        loggerOptions: {
            loggerCallback(loglevel, message, containsPii) {
                console.log(message);
            },
            piiLoggingEnabled: false,
            logLevel: msal.LogLevel.Verbose,
        }
    }
}

const cca = new msal.ConfidentialClientApplication(msalConfig);
```

## <a name="use-scopes-instead-of-resources"></a>리소스 대신 범위 사용

v1.0과 v2.0 엔드포인트 간의 중요한 차이점은 리소스에 액세스하는 방법입니다. ADAL Node에서는 먼저 앱 등록 포털에 대한 권한을 등록한 다음 아래와 같이 리소스(예: Microsoft Graph)에 대한 액세스 토큰을 요청합니다.

```javascript
  authenticationContext.acquireTokenWithAuthorizationCode(
    req.query.code,
    redirectUri,
    resource, // e.g. 'https://graph.microsoft.com'
    clientId,
    clientSecret,
    function (err, response) {
      // do something with the authentication response
  );
```

MSAL Node는 **v1.0** 및 **v2.0** 엔드포인트를 모두 지원합니다. v2.0 엔드포인트는 ‘범위 중심’ 모델을 사용하여 리소스에 액세스합니다. 따라서 리소스에 대한 액세스 토큰을 요청하는 경우 해당 리소스에 대한 범위도 지정해야 합니다.

```javascript
    const tokenRequest = {
        code: req.query.code,
        scopes: ["https://graph.microsoft.com/User.Read"],
        redirectUri: REDIRECT_URI,
    };

    pca.acquireTokenByCode(tokenRequest).then((response) => {
        // do something with the authentication response
    }).catch((error) => {
        console.log(error);
    });
```

범위 중심 모델의 이점 중 하나는 ‘동적 범위’를 사용하는 기능입니다. v1.0을 사용하여 애플리케이션을 빌드하는 경우 로그인할 때 사용자가 동의하도록 애플리케이션에서 요구하는 권한의 전체 세트(‘고정적인 범위’)를 등록해야 했습니다. v2.0에서는 범위 매개 변수를 사용하여 원하는 시간에 권한을 요청할 수 있습니다(즉, ‘동적 범위’). 이를 통해 사용자는 범위에 대한 **증분 동의** 를 제공할 수 있습니다. 따라서 처음에는 사용자가 애플리케이션에 로그인하도록 하고 어떤 종류의 액세스도 필요하지 않은 경우 그렇게 할 수 있습니다. 나중에 사용자의 일정을 읽을 수 있는 기능이 필요한 경우 acquireToken 메서드에서 일정 범위를 요청하고 사용자의 동의를 받을 수 있습니다. 자세한 정보는 [리소스 및 범위](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-browser/docs/resources-and-scopes.md)를 참조하세요.

## <a name="switch-to-msal-api"></a>MSAL API로 전환

ADAL Node의 퍼블릭 메서드 대부분은 MSAL Node에 해당합니다.

| ADAL                                | MSAL                              | 참고                             |
|-------------------------------------|-----------------------------------|-----------------------------------|
| `acquireToken`                      | `acquireTokenSilent`              | 이름이 바뀌고 [계정](https://azuread.github.io/microsoft-authentication-library-for-js/ref/modules/_azure_msal_common.html#accountinfo) 개체가 필요합니다. |
| `acquireTokenWithAuthorizationCode` | `acquireByAuthorizationCode`      |                                   |
| `acquireTokenWithClientCredentials` | `acquireTokenByClientCredentials` |                                   |
| `acquireTokenWithRefreshToken`      | `acquireTokenByRefreshToken`      |                                   |
| `acquireTokenWithDeviceCode`        | `acquireTokenByDeviceCode`        | 이제 사용자 코드 취득(아래 참조)을 추상화합니다. |
| `acquireTokenWithUsernamePassword`  | `acquireTokenByUsernamePassword`  |                                   |

ADAL Node의 일부 메서드는 사용되지 않지만 MSAL Node는 새로운 메서드를 제공합니다.

| ADAL                              | MSAL                            | 참고                             |
|-----------------------------------|---------------------------------|-----------------------------------|
| `acquireUserCode`                   | 해당 없음                             | `acquireTokeByDeviceCode`와 병합됨(위 참조)|
| 해당 없음                               | `acquireTokenOnBehalfOf`          | [OBO 흐름](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-on-behalf-of-flow)을 추상화하는 새 메서드 |
| `acquireTokenWithClientCertificate` | 해당 없음                             | 지금 초기화하는 동안 인증서가 더 이상 필요하지 않습니다([구성 옵션](#configure-msal)참조). |
| 해당 없음                               | `getAuthCodeUrl`                  | [엔드포인트 URL 생성 권한 부여](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-protocols#endpoints)를 추상화하는 새 메서드입니다. |

## <a name="use-promises-instead-of-callbacks"></a>콜백 대신 프라미스 사용

ADAL Node에서는 인증이 성공하고 응답을 가져온 후 모든 작업에 대해 콜백을 사용합니다.

```javascript
var context = new AuthenticationContext(authorityUrl, validateAuthority);

context.acquireTokenWithClientCredentials(resource, clientId, clientSecret, function(err, response) {
  if (err) {
    console.log(err);
  } else {
    // do something with the authentication response
  }
});
```

MSAL Node에서는 프라미스가 대신 사용됩니다.

```javascript
    const cca = new msal.ConfidentialClientApplication(msalConfig);

    cca.acquireTokenByClientCredentials(tokenRequest).then((response) => {
        // do something with the authentication response
    }).catch((error) => {
        console.log(error);
    });
```

ES8와 함께 제공되는 **async/await** 구문을 사용할 수도 있습니다.

```javascript
    try {
        const authResponse = await cca.acquireTokenByCode(tokenRequest);
    } catch (error) {
        console.log(error);
    }
```

## <a name="enable-token-caching"></a>토큰 캐싱 사용

ADAL Node에 메모리 내 토큰 캐시를 가져오는 옵션이 있습니다. 토큰 캐시는 `AuthenticationContext` 개체를 초기화할 때 매개 변수로 사용됩니다.

```javascript
var MemoryCache = require('adal-node/lib/memory-cache');

var cache = new MemoryCache();
var authorityURI = "https://login.microsoftonline.com/common";

var context = new AuthenticationContext(authorityURI, true, cache);
```

MSAL Node는 기본값으로 메모리 내 토큰 캐시를 사용합니다. 명시적으로 가져올 필요는 없습니다. `ConfidentialClientApplication`과 `PublicClientApplication` 개체의 일부로 공개됩니다.

```javascript
const msalTokenCache = publicClientApplication.getTokenCache();
```

사용자 고유의 **캐시 플러그인** 을 제공하여 디스크에 캐시를 작성할 수도 있습니다. 캐시 플러그인은 [ICachePlugin](https://azuread.github.io/microsoft-authentication-library-for-js/ref/interfaces/_azure_msal_common.icacheplugin.html) 인터페이스를 구현해야 합니다. 로깅과 마찬가지로 캐싱은 구성 옵션의 일부이며 MSAL Node 인스턴스를 초기화하여 생성됩니다.

```javascript
const msal = require('@azure/msal-node');

const msalConfig = {
    auth: {
        // authentication related parameters 
    },
    cache: {
        cachePlugin // your implementation of cache plugin
    },
    system: {
        // logging related options 
    }
}

const msalInstance = new ConfidentialClientApplication(msalConfig);
```

예제 캐시 플러그인은 다음과 같이 구현할 수 있습니다.

```javascript
const fs = require('fs');

// Call back APIs which automatically write and read into a .json file - example implementation
const beforeCacheAccess = async (cacheContext) => {
    cacheContext.tokenCache.deserialize(await fs.readFile(cachePath, "utf-8"));
};

const afterCacheAccess = async (cacheContext) => {
    if(cacheContext.cacheHasChanged) {
        await fs.writeFile(cachePath, cacheContext.tokenCache.serialize());
    }
};

// Cache Plugin
const cachePlugin = {
    beforeCacheAccess,
    afterCacheAccess
};
```

데스크톱 앱과 같은 [퍼블릭 클라이언트 애플리케이션](https://docs.microsoft.com/azure/active-directory/develop/msal-client-applications)을 개발하는 경우 [Microsoft Authentication Extensions for Node](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/extensions/msal-node-extensions) 제품은 클라이언트 애플리케이션에서 플랫폼 간 토큰 캐시 serialization 및 지속성을 수행하는 보안 메커니즘을 제공합니다. 지원되는 플랫폼은 Windows, Mac 및 Linux입니다.

> [!NOTE]
> [Microsoft Authentication Extensions for Node](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/extensions/msal-node-extensions)는 규모 및 성능 문제를 일으킬 수 있으므로 웹 애플리케이션에는 권장 **되지 않습니다** . 대신 웹앱은 세션에서 캐시를 유지하는 것이 좋습니다.

## <a name="remove-logic-around-refresh-tokens"></a>새로 고침 토큰에 대한 논리 제거

ADAL Node에서는 새로 고침 토큰(RT)이 공개되어 토큰을 캐싱하고 `acquireTokenWithRefreshToken` 메서드를 사용함으로써 이러한 토큰 사용과 관련된 솔루션을 개발할 수 있었습니다. 특히 RT가 관련된 일반적인 시나리오는 다음과 같습니다.

- 사용자가 더 이상 연결되지 않은 상태에서 사용자를 대신하여 대시보드를 새로 고치는 등의 작업을 수행하는 장기 실행 서비스
- 클라이언트에서 RT를 웹 서비스로 가져올 수 있게 하는 WebFarm 시나리오(클라이언트 쪽에서 캐싱 수행, 쿠키 암호화, 서버 쪽이 아님)

MSAL Node는 보안상의 이유로 새로 고침 토큰을 공개하지 않습니다. 대신, MSAL은 사용자 대신 토큰 새로 고침을 처리합니다. 따라서 더 이상 이에 대한 논리를 작성할 필요가 없습니다. 하지만 이전에 획득한 새로 고침 토큰을 마이그레이션하고 사용해야 하는 경우 MSAL Node는 ADAL Node의 `acquireTokenWithRefreshToken` 메서드에 해당하는 `acquireTokenByRefreshToken`을 제공합니다.

## <a name="run-the-app"></a>앱 실행

변경이 완료되면 앱을 실행하고 인증 시나리오를 테스트합니다.

```console
npm start
```

## <a name="example-securing-web-apps-with-adal-node-vs-msal-node"></a>예제: ADAL Node 및 MSAL Node를 사용하여 웹앱 보안하기

아래 코드 조각은 Express.js framework의 기밀 클라이언트 웹앱을 보여 줍니다. 앱은 ADAL Node를 사용하여 보호됩니다. 사용자가 `/auth` 인증 경로에 도달하면 로그인을 수행하고, `/redirect` 경로를 통해 Microsoft Graph에 대한 액세스 토큰을 획득한 다음, 언급된 토큰의 콘텐츠를 표시합니다.

```javascript
// Import dependencies
var express = require('express');
var crypto = require('crypto');
var adal = require('adal-node');

// Authentication parameters
var clientId = 'Enter_the_Application_Id_Here';
var clientSecret = 'Enter_the_Client_Secret_Here';
var tenant = 'common';
var authorityUrl = 'https://login.microsoftonline.com/' + tenant;
var redirectUri = 'http://localhost:3000/redirect';
var resource = 'https://graph.microsoft.com';

// Configure logging
adal.Logging.setLoggingOptions({
    log: function (level, message, error) {
        console.log(message);
    },
    level: adal.Logging.LOGGING_LEVEL.VERBOSE,
    loggingWithPII: false
});

// Auth code request URL template
var templateAuthzUrl = 'https://login.microsoftonline.com/' + tenant + 
 '/oauth2/authorize?response_type=code&client_id=' + clientId + '&redirect_uri=' 
 + redirectUri + '&state=<state>&resource=' + resource;

// Initialize express
var app = express();

// State variable persists throughout the app lifetime
app.locals.state = "";

app.get('/auth', function(req, res) {

    // Create a random string as state parameter, which is used against XSRF
    crypto.randomBytes(48, function(ex, buf) {
        app.locals.state = buf.toString('base64').replace(/\//g, '_').replace(/\+/g, '-');
        
        // Construct auth code request URL
        var authorizationUrl = templateAuthzUrl.replace('<state>', app.locals.state);
        res.redirect(authorizationUrl);
    });
});

app.get('/redirect', function(req, res) {
    // Compare state parameter against XSRF
    if (app.locals.state !== req.query.state) {
        res.send('error: state does not match');
    }

    // Initialize an AuthenticationContext object
    var authenticationContext = new adal.AuthenticationContext(authorityUrl);
    
    // Exchange auth code for tokens
    authenticationContext.acquireTokenWithAuthorizationCode(
        req.query.code, 
        redirectUri, 
        resource, 
        clientId, 
        clientSecret,
        function(err, response) {
            res.send(response);
        }
    );
});

app.listen(3000, function() { console.log(`listening on port 3000!`); });
```

다음과 같이 MSAL Node를 사용하여 동등한 기능의 웹앱에 보안을 설정할 수 있습니다.

```javascript
// Import dependencies
const express = require("express");
const msal = require('@azure/msal-node');

// Authentication parameters
const config = {
    auth: {
        clientId: "Enter_the_Application_Id_Here",
        authority: "https://login.microsoftonline.com/common",
        clientSecret: "Enter_the_Client_Secret_Here"
    },
    system: {
        loggerOptions: {
            loggerCallback(loglevel, message, containsPii) {
                console.log(message);
            },
            piiLoggingEnabled: false,
            logLevel: msal.LogLevel.Verbose,
        }
    }
};

const REDIRECT_URI = "http://localhost:3000/redirect";

// Initialize MSAL Node object using authentication parameters
const cca = new msal.ConfidentialClientApplication(config);

// Initialize express
const app = express();

app.get('/auth', (req, res) => {
    
    // Construct a request object for auth code
    const authCodeUrlParameters = {
        scopes: ["user.read"],
        redirectUri: REDIRECT_URI,
    };

    // Request auth code, then redirect
    cca.getAuthCodeUrl(authCodeUrlParameters).then((response) => {
        res.redirect(response);
    }).catch((error) => res.send(error));
});

app.get('/redirect', (req, res) => {
    
    // Use the auth code in redirect request to construct
    // a token request object
    const tokenRequest = {
        code: req.query.code,
        scopes: ["user.read"],
        redirectUri: REDIRECT_URI,
    };

    // Exchange the auth code for tokens
    cca.acquireTokenByCode(tokenRequest).then((response) => {
        res.send(response);
    }).catch((error) => res.status(500).send(error));
});

app.listen(3000, () => console.log(`listening on port 3000!`));
```

## <a name="next-steps"></a>다음 단계

- [MSAL Node API 참조](https://azuread.github.io/microsoft-authentication-library-for-js/ref/modules/_azure_msal_node.html)
- [MSAL Node Code 샘플](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/samples/msal-node-samples)
