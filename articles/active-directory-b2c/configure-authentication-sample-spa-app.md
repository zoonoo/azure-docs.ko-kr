---
title: Azure Active Directory B2C를 사용하여 샘플 SPA 애플리케이션에서 인증 구성
description: Azure Active Directory B2C를 사용하여 SPA 애플리케이션에 사용자를 로그인하고 가입할 수 있습니다.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 06/11/2021
ms.author: mimart
ms.subservice: B2C
ms.custom: b2c-support
ms.openlocfilehash: addf3870c22105a2ff42202e768d1e8cda4ffbde
ms.sourcegitcommit: 8651d19fca8c5f709cbb22bfcbe2fd4a1c8e429f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/14/2021
ms.locfileid: "112073084"
---
# <a name="configure-authentication-in-a-sample-single-page-application-using-azure-active-directory-b2c"></a>Azure Active Directory B2C를 사용하여 샘플 단일 페이지 애플리케이션에서 인증 구성

이 문서에서는 샘플 JavaScript 단일 페이지 애플리케이션을 사용하여 SPA 앱에 Azure AD B2C(Azure Active Directory B2C) 인증을 추가하는 방법을 설명합니다.

## <a name="overview"></a>개요

OIDC(OpenID Connect)는 애플리케이션에 사용자가 안전하게 로그인하는 데 사용할 수 있는 OAuth 2.0 기반의 인증 프로토콜입니다. 이 단일 페이지 애플리케이션 샘플은 [MSAL.js](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-browser) 및 OIDC PKCE 흐름을 사용합니다. MSAL.js는 SPA 앱에 인증 및 권한 부여 지원을 간편하게 추가할 수 있도록 하는 Microsoft에서 제공하는 라이브러리입니다.

### <a name="sign-in-flow"></a>로그인 흐름
로그인 흐름에 포함되는 단계는 다음과 같습니다.

1. 사용자가 웹앱으로 이동하여 **로그인** 을 선택합니다. 
1. 앱에서 권한 부여 요청을 시작하고 사용자를 Azure AD B2C로 리디렉션합니다.
1. 사용자가 [가입하거나 로그인하거나](add-sign-up-and-sign-in-policy.md), [암호를 다시 설정하거나](add-password-reset-policy.md), [소셜 계정](add-identity-provider.md)으로 로그인합니다.
1. 로그인하면 Azure AD B2C에서 ID 토큰을 앱에 반환합니다.
1. 단일 페이지 애플리케이션은 ID 토큰의 유효성을 검사하고, 클레임을 읽고, 사용자가 보호된 리소스/API를 호출할 수 있도록 합니다.

### <a name="app-registration-overview"></a>앱 등록 개요

앱이 Azure AD B2C에 로그인하고 웹 API를 호출할 수 있도록 하려면 Azure AD B2C 디렉터리에 두 개의 애플리케이션을 등록해야 합니다.  

- **웹 애플리케이션** 등록을 사용하면 앱이 Azure AD B2C로 로그인할 수 있습니다. 앱을 등록하는 동안 *리디렉션 URI* 를 지정합니다. 리디렉션 URI는 사용자가 Azure AD B2C로 인증하면 리디렉션되는 엔드포인트입니다. 앱 등록 프로세스는 앱을 고유하게 식별하는 *애플리케이션 ID*(*클라이언트 ID* 라고도 함)를 생성합니다.

- **웹 API** 등록을 사용하면 앱에서 보안 웹 API를 호출할 수 있습니다. 등록에는 웹 API *범위* 가 포함됩니다. 범위는 웹 API와 같은 보호된 리소스에 대한 사용 권한을 관리하는 방법을 제공합니다. 웹 API 범위에 웹 애플리케이션 권한을 부여합니다. 액세스 토큰을 요청할 때 앱에서 요청의 범위 매개 변수에서 원하는 권한을 지정합니다.  

다음 다이어그램에서는 앱 등록 및 애플리케이션 아키텍처를 설명합니다.

![웹 API 호출 등록 및 토큰이 있는 웹앱](./media/configure-authentication-sample-spa-app/spa-app-with-api-architecture.png) 

### <a name="call-to-a-web-api"></a>웹 API 호출

[!INCLUDE [active-directory-b2c-app-integration-call-api](../../includes/active-directory-b2c-app-integration-call-api.md)]

### <a name="sign-out-flow"></a>로그아웃 흐름

[!INCLUDE [active-directory-b2c-app-integration-sign-out-flow](../../includes/active-directory-b2c-app-integration-sign-out-flow.md)]

## <a name="prerequisites"></a>사전 요구 사항

다음 중 하나를 실행하는 컴퓨터:

* [Visual Studio Code](https://code.visualstudio.com/) 또는 다른 코드 편집기
* [Node.js 런타임](https://nodejs.org/en/download/)

## <a name="step-1-configure-your-user-flow"></a>1단계: 사용자 흐름 구성

[!INCLUDE [active-directory-b2c-app-integration-add-user-flow](../../includes/active-directory-b2c-app-integration-add-user-flow.md)]

## <a name="step-2-register-your-spa-and-api"></a>2단계: SPA 및 API 등록

이 단계에서는 SPA 앱 및 웹 API 애플리케이션 등록을 만들고 웹 API의 범위를 지정합니다.

### <a name="21-register-the-web-api-application"></a>2.1 웹 API 애플리케이션 등록

[!INCLUDE [active-directory-b2c-app-integration-register-api](../../includes/active-directory-b2c-app-integration-register-api.md)]

### <a name="22-configure-scopes"></a>2.2 범위 구성

[!INCLUDE [active-directory-b2c-app-integration-api-scopes](../../includes/active-directory-b2c-app-integration-api-scopes.md)]

### <a name="23-register-the-client-app"></a>2.3 클라이언트 앱 등록

다음 단계에 따라 앱 등록을 만듭니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. 포털 도구 모음에서 **디렉터리 + 구독** 아이콘을 선택한 다음, Azure AD B2C 테넌트가 포함된 디렉터리를 선택합니다.
1. Azure Portal에서 **Azure AD B2C** 를 검색하고 선택합니다.
1. **앱 등록** 을 선택한 다음, **새 등록** 을 선택합니다.
1. 애플리케이션의 **이름** 을 입력합니다. 예: *MyApp*
1. **지원되는 계정 유형** 아래에서 **모든 ID 공급자 또는 조직 디렉터리의 계정(사용자 흐름에서 사용자를 인증하는 용도)** 을 선택합니다. 
1. **리디렉션 URI** 에서 **SPA(단일 페이지 애플리케이션)** 를 선택한 다음, URL 텍스트 상자에 `http://localhost:6420`를 입력합니다.
1. **사용 권한** 에서 **openid 및 오프라인 액세스 권한에 대한 관리자 동의 부여** 확인란을 선택합니다.
1. **등록** 을 선택합니다.

다음으로, 암시적 권한 부여 흐름을 사용하도록 설정합니다.

1. 관리에서 인증을 선택합니다.
1. 새 환경을 체험해 보세요. (표시되는 경우)를 선택합니다.
1. 암시적 허용에서 ID 토큰 확인란을 선택합니다.
1. [저장]을 선택합니다.

웹 애플리케이션을 구성하는 이후 단계에 사용할 수 있도록 **애플리케이션(클라이언트) ID** 를 기록합니다.
    ![애플리케이션 ID 가져오기](./media/configure-authentication-sample-web-app/get-azure-ad-b2c-app-id.png)  

### <a name="25-grant-permissions"></a>2.5 권한 부여

[!INCLUDE [active-directory-b2c-app-integration-grant-permissions](../../includes/active-directory-b2c-app-integration-grant-permissions.md)]

## <a name="step-3-get-the-spa-sample-code"></a>3단계: SPA 샘플 코드 가져오기

이 샘플은 단일 페이지 애플리케이션에서 사용자 가입 및 로그인에 Azure AD B2C를 사용하고, 보호되는 Web API를 호출하는 방법을 보여 줍니다. 아래 샘플을 다운로드합니다.

  [zip 파일을 다운로드](https://github.com/Azure-Samples/ms-identity-b2c-javascript-spa/archive/main.zip)하거나 GitHub에서 샘플을 복제합니다.

  ```
  git clone https://github.com/Azure-Samples/ms-identity-b2c-javascript-spa.git
  ```

### <a name="31-update-the-spa-sample"></a>3.1 SPA 샘플 업데이트

샘플을 확보했으면 2.3단계에서 기록해둔 Azure AD B2C 테넌트 이름과 *myApp* 의 애플리케이션 ID로 코드를 업데이트합니다.

*App* 폴더 내에서 *authConfig.js* 파일을 엽니다.
1. `msalConfig` 개체에서 `clientId`에 대한 할당을 찾아 2.3단계에서 기록한 **애플리케이션(클라이언트) ID** 로 바꿉니다.

`policies.js` 파일을 엽니다.
1. `names` 아래에서 항목을 찾아 해당 할당을 이전 단계에서 만든 사용자 흐름의 이름(예: `b2c_1_susi`)으로 바꿉니다.
1. `authorities` 아래에서 항목을 찾아 이전 단계에서 만든 사용자 흐름의 이름(예: `https://<your-tenant-name>.b2clogin.com/<your-tenant-name>.onmicrosoft.com/<your-sign-in-sign-up-policy>`)으로 적절하게 바꿉니다.
1. `authorityDomain`에 대한 할당을 찾아 `<your-tenant-name>.b2clogin.com`으로 바꿉니다.

`apiConfig.js` 파일을 엽니다.
1. `b2cScopes`에 대한 할당을 찾아 URL을 Web API용으로 만든 범위 URL(예: `b2cScopes: ["https://<your-tenant-name>.onmicrosoft.com/tasks-api/tasks.read"]`)로 바꿉니다.
1. `webApi`에 대한 할당을 찾아서 현재 URL을 `http://localhost:5000/tasks`로 바꿉니다.


결과 코드는 다음 샘플과 유사해야 합니다.

*authConfig.js*:

```javascript
const msalConfig = {
  auth: {
    clientId: "<your-MyApp-application-ID>"
    authority: b2cPolicies.authorities.signUpSignIn.authority,
    knownAuthorities: [b2cPolicies.authorityDomain],
  },
  cache: {
    cacheLocation: "localStorage",
    storeAuthStateInCookie: true
  }
};

const loginRequest = {
  scopes: ["openid", "profile"],
};

const tokenRequest = {
  scopes: apiConfig.b2cScopes
};
```

*policies.js*:

```javascript
const b2cPolicies = {
    names: {
        signUpSignIn: "b2c_1_susi",
        forgotPassword: "b2c_1_reset",
        editProfile: "b2c_1_edit_profile"
    },
    authorities: {
        signUpSignIn: {
            authority: "https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/b2c_1_susi",
        },
        forgotPassword: {
            authority: "https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/b2c_1_reset",
        },
        editProfile: {
            authority: "https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/b2c_1_edit_profile"
        }
    },
    authorityDomain: "your-tenant-name.b2clogin.com"
}
```

*apiConfig.js*:

```javascript
const apiConfig = {
  b2cScopes: ["https://your-tenant-name.onmicrosoft.com/tasks-api/tasks.read"],
  webApi: "http://localhost:5000/tasks"
};
```

## <a name="step-4-get-the-web-api-sample-code"></a>4단계: 웹 API 샘플 코드 가져오기

웹 API를 등록하고 해당 범위를 정의했으므로, 이제 Azure AD B2C 테넌트에서 작동하도록 웹 API 코드를 구성합니다. 아래 샘플을 다운로드합니다.

[\*.zip 보관 파일을 다운로드](https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi/archive/master.zip)하거나 GitHub에서 웹 API 프로젝트 샘플을 복제합니다. GitHub에서 [Azure-Samples/active-directory-b2c-javascript-nodejs-webapi](https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi) 프로젝트로 직접 이동할 수도 있습니다.

```console
git clone https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi.git
```

### <a name="41-update-the-web-api"></a>4.1 웹 API 업데이트

1. 코드 편집기에서 *config.json* 파일을 엽니다.
1. 앞에서 만든 애플리케이션 등록으로 변수 값을 수정합니다. 그리고 `policyName`을 필수 구성 요소의 일부로 만든 사용자 흐름으로 업데이트합니다. 예: *b2c_1_susi*
    
    ```json
    "credentials": {
        "tenantName": "<your-tenant-name>",
        "clientID": "<your-webapi-application-ID>"
    },
    "policies": {
        "policyName": "b2c_1_susi"
    },
    "resource": {
        "scope": ["tasks.read"] 
    },
    ```

### <a name="42-enable-cors"></a>4.2 CORS를 사용하도록 설정

단일 페이지 애플리케이션에서 Node.js 웹 API를 호출할 수 있게 하려면 웹 API에서 [CORS](https://expressjs.com/en/resources/middleware/cors.html)를 사용하도록 설정해야 합니다. 프로덕션 애플리케이션에서는 요청을 만드는 도메인에 주의해야 합니다. 이 예제에서는 모든 도메인의 요청을 허용합니다.

CORS를 사용하도록 설정하려면 다음 미들웨어를 사용합니다. 다운로드한 Node.js 웹 API 코드 샘플에서는 이미 *index.js* 파일에 추가되어 있습니다.

```javascript
app.use((req, res, next) => {
    res.header("Access-Control-Allow-Origin", "*");
    res.header("Access-Control-Allow-Headers", "Authorization, Origin, X-Requested-With, Content-Type, Accept");
    next();
});
```

## <a name="step-5-run-the-spa-and-web-api"></a>5단계: SPA 및 웹 API 실행

이제 API에 대한 단일 페이지 애플리케이션의 범위가 지정된 액세스 권한을 테스트할 준비가 되었습니다. 로컬 머신에서 Node.js 웹 API와 샘플 JavaScript 단일 페이지 애플리케이션을 모두 실행합니다. 그런 다음, 단일 페이지 애플리케이션에 로그인하고 **API 호출** 단추를 선택하여 보호된 API에 대한 요청을 시작합니다.

### <a name="run-the-nodejs-web-api"></a>Node.js Web API 실행

1. 콘솔 창을 열고, Node.js 웹 API 샘플이 있는 디렉터리로 변경합니다. 다음은 그 예입니다.

    ```console
    cd active-directory-b2c-javascript-nodejs-webapi
    ```

1. 다음 명령을 실행합니다.

    ```console
    npm install && npm update
    node index.js
    ```

    콘솔 창에는 애플리케이션이 호스트되는 포트 번호가 표시됩니다.

    ```console
    Listening on port 5000...
    ```

### <a name="run-the-single-page-app"></a>단일 페이지 앱 실행

1. 다른 콘솔 창을 열고, JavaScript SPA 샘플이 있는 디렉터리로 변경합니다. 다음은 그 예입니다.

    ```console
    cd ms-identity-b2c-javascript-spa
    ```

1. 다음 명령을 실행합니다.

    ```console
    npm install && npm update
    npm start
    ```

    콘솔 창에는 애플리케이션이 호스트되는 포트 번호가 표시됩니다.

    ```console
    Listening on port 6420...
    ```

1. 브라우저에서 `http://localhost:6420`으로 이동하여 애플리케이션을 확인합니다.

    ![브라우저에 표시된 단일 페이지 애플리케이션 샘플 앱](./media/configure-authentication-sample-spa-app/sample-app-sign-in.png)

1. [이전 자습서](tutorial-single-page-app.md)에서 사용한 이메일 주소와 암호를 사용하여 로그인합니다. 로그인에 성공하면 `User 'Your Username' logged-in` 메시지가 표시됩니다.
1. **API 호출** 단추를 선택합니다. SPA는 로그인한 사용자의 표시 이름을 반환하는 보호된 웹 API에 대한 요청에 액세스 토큰을 보냅니다.

    ![API에서 반환된 사용자 이름 JSON 결과를 표시하는 브라우저의 단일 페이지 애플리케이션](./media/configure-authentication-sample-spa-app/sample-app-result.png)

## <a name="deploy-your-application"></a>애플리케이션 배포 

프로덕션 애플리케이션에서 앱 등록 리디렉션 URI는 일반적으로 앱이 실행되는, 공개적으로 액세스 가능한 엔드포인트입니다(예: `https://contoso.com/signin-oidc`). 

언제든지 등록된 애플리케이션에서 리디렉션 URI를 추가하고 수정할 수 있습니다. 리디렉션 URI에는 다음 제한 사항이 적용됩니다.

* 회신 URL은 스키마 `https`로 시작해야 합니다.
* 회신 URL은 대/소문자를 구분합니다. 해당 사례는 실행 중인 애플리케이션의 URL 경로에 대한 대/소문자와 일치해야 합니다. 

## <a name="next-steps"></a>다음 단계

* [코드 샘플에 대해](https://github.com/Azure-Samples/ms-identity-b2c-javascript-spa) 자세히 알아보기
* [Azure AD B2C를 사용하여 사용자 고유의 SPA 애플리케이션에서 옵션 인증](enable-authentication-spa-app-options.md) 방법 알아보기
