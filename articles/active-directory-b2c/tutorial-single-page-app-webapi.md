---
title: '자습서: Azure AD B2C를 사용하여 Node.js 웹 API를 보호하고 SPA(단일 페이지 애플리케이션)에 대한 액세스 권한 부여'
titleSuffix: Azure AD B2C
description: 이 자습서에서는 Active Directory B2C를 사용하여 Node.js 웹 API를 보호하고 단일 페이지 애플리케이션에서 API를 호출하는 방법을 알아봅니다.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.author: mimart
ms.date: 04/04/2020
ms.custom: mvc, devx-track-js
ms.topic: tutorial
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: 8b10dd2d87ab7d4cf41a0bf860798f27651294d7
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/25/2020
ms.locfileid: "91259002"
---
# <a name="tutorial-protect-and-grant-access-to-a-nodejs-web-api-from-a-single-page-application-with-azure-ad-b2c"></a>자습서: Azure AD B2C를 사용하여 단일 페이지 애플리케이션에서 Node.js 웹 API를 보호하고 액세스 권한 부여

이 자습서에서는 단일 페이지 애플리케이션에서 Azure AD B2C(Azure Active Directory B2C)로 보호된 Node.js 웹 API를 호출하는 방법을 보여줍니다.

이 자습서는 2부로 구성된 시리즈 중 제2부입니다.

> [!div class="checklist"]
> * Azure AD B2C 테넌트에서 새 웹 API 애플리케이션 등록 만들기
> * 웹 API의 범위 구성
> * 웹 API에 사용 권한 부여
> * 테넌트에서 작동하도록 웹 API 코드 샘플 수정

이 시리즈의 [첫 번째 자습서](tutorial-single-page-app.md)에서 코드 샘플을 다운로드하고 Azure AD B2C 테넌트의 사용자 흐름을 사용하여 사용자를 로그인하도록 수정했습니다.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>사전 요구 사항

* [자습서: 단일 페이지 애플리케이션에서 Azure AD B2C를 사용하여 인증 설정](tutorial-single-page-app.md)
* [Visual Studio Code](https://code.visualstudio.com/) 또는 다른 코드 편집기
* [Node.JS](https://nodejs.org/en/download/)

## <a name="add-a-web-api-application"></a>웹 API 애플리케이션 추가

[!INCLUDE [active-directory-b2c-appreg-webapi](../../includes/active-directory-b2c-appreg-webapi.md)]

## <a name="configure-scopes"></a>범위 구성

범위는 보호된 리소스에 대한 액세스를 제어하는 방법을 제공합니다. 범위는 웹 API에서 범위 기반 액세스 제어를 구현하는 데 사용됩니다. 예를 들어 일부 사용자는 읽기 및 쓰기 권한을 모두 가질 수 있지만, 다른 사용자는 읽기 전용 권한을 가질 수 있습니다. 이 자습서에서는 웹 API에 대한 읽기 및 쓰기 권한을 모두 정의합니다.

[!INCLUDE [active-directory-b2c-scopes](../../includes/active-directory-b2c-scopes.md)]

이후에 단일 페이지 애플리케이션을 구성할 때 범위로 사용할 수 있도록 **범위** 아래에서 `demo.read` 범위의 값을 적어 둡니다. 전체 범위 값은 `https://contosob2c.onmicrosoft.com/api/demo.read`와 비슷합니다.

## <a name="grant-permissions"></a>권한 부여

다른 애플리케이션에서 보호된 웹 API를 호출하려면 해당 애플리케이션 권한을 웹 API에 부여해야 합니다.

필수 조건 자습서에서 *webapp1*이라는 웹 애플리케이션을 만들었습니다. 이 자습서에서는 이전 섹션에서 만든 웹 API( *webapi1*)를 호출하도록 해당 애플리케이션을 구성합니다.

[!INCLUDE [active-directory-b2c-permissions-api](../../includes/active-directory-b2c-permissions-api.md)]

이제 지정된 범위에서 보호된 웹 API에 대한 권한이 단일 페이지 웹 애플리케이션에 부여되었습니다. 사용자는 단일 페이지 애플리케이션을 사용하기 위해 Azure AD B2C에서 인증됩니다. 단일 페이지 앱은 권한 부여 흐름을 통해 Azure AD B2C에서 반환된 액세스 토큰을 사용하여 보호된 웹 API에 액세스합니다.

## <a name="configure-the-sample"></a>샘플 구성

웹 API를 등록하고 범위를 정의했으므로, 이제 Azure AD B2C 테넌트에서 작동하도록 웹 API 코드를 구성하겠습니다. 이 자습서에서는 GitHub에서 다운로드한 샘플 Node.js 웹 API를 구성합니다.

[\*.zip 보관 파일을 다운로드](https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi/archive/master.zip)하거나 GitHub에서 웹 API 프로젝트 샘플을 복제합니다. GitHub에서 [Azure-Samples/active-directory-b2c-javascript-nodejs-webapi](https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi) 프로젝트로 직접 이동할 수도 있습니다.

```console
git clone https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi.git
```

### <a name="configure-the-web-api"></a>Web API 구성

1. 코드 편집기에서 *config.js* 파일을 엽니다.
1. 앞에서 만든 애플리케이션 등록을 반영하도록 변수 값을 수정합니다. 그리고 `policyName`을 필수 구성 요소의 일부로 만든 사용자 흐름으로 업데이트합니다. 예를 들어 *B2C_1_signupsignin1*으로 업데이트합니다.

    ```javascript
    const clientID = "<your-webapi-application-ID>"; // Application (client) ID
    const b2cDomainHost = "<your-tenant-name>.b2clogin.com";
    const tenantId = "<your-tenant-ID>.onmicrosoft.com"; // Alternatively, you can use your Directory (tenant) ID (a GUID)
    const policyName = "B2C_1_signupsignin1";
    ```

#### <a name="enable-cors"></a>CORS를 사용하도록 설정

단일 페이지 애플리케이션에서 Node.js 웹 API를 호출할 수 있게 하려면 웹 API에서 [CORS](https://expressjs.com/en/resources/middleware/cors.html)를 사용하도록 설정해야 합니다. 프로덕션 애플리케이션에서는 요청을 만드는 도메인에 주의해야 하지만, 이 자습서에서는 모든 도메인의 요청을 허용합니다.

CORS를 사용하도록 설정하려면 다음 미들웨어를 사용합니다. 이 자습서의 Node.js 웹 API 코드 샘플에서는 이미 *index.js* 파일에 추가되어 있습니다.

```javascript
app.use((req, res, next) => {
    res.header("Access-Control-Allow-Origin", "*");
    res.header("Access-Control-Allow-Headers", "Authorization, Origin, X-Requested-With, Content-Type, Accept");
    next();
});
```

### <a name="configure-the-single-page-application"></a>단일 페이지 애플리케이션 구성

이 시리즈의 [이전 자습서](tutorial-single-page-app.md)에 나오는 SPA(단일 페이지 애플리케이션)는 사용자 가입 및 로그인에 Azure AD B2C를 사용하고, 기본적으로 *fabrikamb2c* 데모 테넌트에서 보호되는 Node.js 웹 API를 호출합니다.

이 섹션에서는 *사용자*의 Azure AD B2C 테넌트에서 보호되고 로컬 머신에서 실행되는 Node.js 웹 API를 호출하도록 단일 페이지 웹 애플리케이션을 업데이트합니다.

SPA의 설정을 변경하려면 다음을 수행합니다.

1. 이전 자습서에서 다운로드하거나 복제한 [active-directory-b2c-javascript-msal-singlepageapp][github-js-spa] 프로젝트에서 *JavaScriptSPA* 폴더 내부의 *apiConfig.js* 파일을 엽니다.
1. 앞에서 만든 *demo.read* 범위의 URI와 웹 API의 URL을 사용하여 샘플을 구성합니다.
    1. `apiConfig` 정의에서 `b2cScopes` 값을 *demo.read* 범위의 전체 URI(앞에서 적어 둔 **범위** 값)로 바꿉니다.
    1. `webApi` 값의 도메인을 이전 단계에서 웹 API 애플리케이션을 등록할 때 추가한 리디렉션 URI로 변경합니다.

    이 API는 `/hello` 엔드포인트에서 액세스할 수 있으므로 URI의 */hello*를 그대로 둡니다.

    `apiConfig` 정의는 다음 코드 블록과 비슷한 형식이지만, `<your-tenant-name>` 대신 B2C 테넌트의 이름이 사용됩니다.

    ```javascript
    // The current application coordinates were pre-registered in a B2C tenant.
    const apiConfig = {
      b2cScopes: ["https://<your-tenant-name>.onmicrosoft.com/api/demo.read"],
      webApi: "http://localhost:5000/hello" // '/hello' should remain in the URI
    };
    ```

## <a name="run-the-spa-and-web-api"></a>SPA 및 웹 API 실행

이제 API에 대한 단일 페이지 애플리케이션의 범위가 지정된 액세스 권한을 테스트할 준비가 되었습니다. 로컬 머신에서 Node.js 웹 API와 샘플 JavaScript 단일 페이지 애플리케이션을 모두 실행합니다. 그런 다음, 단일 페이지 애플리케이션에 로그인하고 **API 호출** 단추를 선택하여 보호된 API에 대한 요청을 시작합니다.

이 자습서에서는 두 애플리케이션이 모두 로컬로 실행되지만, 우리는 앞에서 Azure AD B2C를 사용하여 안전하게 가입/로그인하고 보호된 웹 API에 대한 액세스 권한을 부여하도록 두 애플리케이션을 구성했습니다.

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
    cd active-directory-b2c-javascript-msal-singlepageapp
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

    ![브라우저에 표시된 단일 페이지 애플리케이션 샘플 앱](./media/tutorial-single-page-app-webapi/tutorial-01-sample-app-browser.png)

1. [이전 자습서](tutorial-single-page-app.md)에서 사용한 이메일 주소와 암호를 사용하여 로그인합니다. 로그인에 성공하면 `User 'Your Username' logged-in` 메시지가 표시됩니다.
1. **API 호출** 단추를 선택합니다. 다음과 같이 SPA가 Azure AD B2C로부터 권한을 부여받은 다음, 보호된 웹 API에 액세스하여 로그인한 사용자 이름을 표시합니다.

    ![API에서 반환된 사용자 이름 JSON 결과를 표시하는 브라우저의 단일 페이지 애플리케이션](./media/tutorial-single-page-app-webapi/tutorial-02-call-api.png)

## <a name="next-steps"></a>다음 단계

이 자습서에서는 다음을 수행합니다.

> [!div class="checklist"]
> * Azure AD B2C 테넌트에서 새 웹 API 애플리케이션 등록을 만들었습니다.
> * 웹 API의 범위를 구성했습니다.
> * 웹 API에 사용 권한을 부여했습니다.
> * 테넌트에서 작동하도록 웹 API 코드 샘플을 수정했습니다.

이제 SPA가 보호된 웹 API에서 리소스를 요청하는 방법을 살펴보았으므로 이러한 애플리케이션 유형에서 서로 간에 상호 작용하고 Azure AD B2C와 상호 작용하는 방법에 대해 자세히 이해할 수 있습니다.

> [!div class="nextstepaction"]
> [Azure Active Directory B2C에서 사용할 수 있는 애플리케이션 유형 >](application-types.md)

<!-- Links - EXTERNAL -->
[github-js-spa]: https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp
