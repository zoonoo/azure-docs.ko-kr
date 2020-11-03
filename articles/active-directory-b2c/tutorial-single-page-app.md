---
title: '자습서: 단일 페이지 앱에서 인증 사용'
titleSuffix: Azure AD B2C
description: 이 자습서에서는 Azure Active Directory B2C를 사용하여 JavaScript 기반 SPA(단일 페이지 애플리케이션)에 대한 사용자 로그인을 제공하는 방법을 알아봅니다.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.author: mimart
ms.date: 04/04/2020
ms.custom: mvc, seo-javascript-september2019, devx-track-js
ms.topic: tutorial
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: 3a3eb77315953c3791e09c4326af7cc3e3231a69
ms.sourcegitcommit: 4cb89d880be26a2a4531fedcc59317471fe729cd
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/27/2020
ms.locfileid: "92670039"
---
# <a name="tutorial-enable-authentication-in-a-single-page-application-with-azure-ad-b2c"></a>자습서: 단일 페이지 애플리케이션에서 Azure AD B2C를 사용하여 인증 설정

이 자습서에서는 OAuth 2.0 암시적 허용 흐름을 통해 Azure AD B2C(Azure Active Directory B2C)를 사용하여 SPA(단일 페이지 애플리케이션)에서 사용자를 가입하고 로그인하는 방법을 보여줍니다.

이 자습서는 2부로 구성된 시리즈 중 제1부입니다.

> [!div class="checklist"]
> * Azure AD B2C 테넌트에 등록된 애플리케이션에 회신 URL 추가
> * GitHub에서 코드 샘플 다운로드
> * 해당 테넌트에서 작동하도록 샘플 애플리케이션의 코드 수정
> * 가입/로그인 사용자 흐름을 사용하여 가입

이 시리즈의 [다음 자습서](tutorial-single-page-app-webapi.md)에서는 코드 샘플의 웹 API 부분을 사용하도록 설정합니다.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>필수 구성 요소

이 자습서의 단계를 진행하려면 다음과 같은 Azure AD B2C 리소스가 있어야 합니다.

* [Azure AD B2C 테넌트](tutorial-create-tenant.md)
* 테넌트에 [등록된 애플리케이션](tutorial-register-spa.md)(암시적 흐름 옵션 사용)
* 테넌트에서 [만든 사용자 흐름](tutorial-create-user-flows.md)

또한 로컬 개발 환경에서 다음 항목이 필요합니다.

* [Visual Studio Code](https://code.visualstudio.com/) 또는 다른 코드 편집기
* [Node.JS](https://nodejs.org/en/download/)

## <a name="update-the-application"></a>애플리케이션 업데이트

필수 조건의 일부로 완료한 두 번째 자습서에서 Azure AD B2C에 웹 애플리케이션을 등록했습니다. 이 자습서에서 코드 샘플과의 통신을 사용하도록 설정하려면 애플리케이션 등록에 회신 URL(리디렉트 URI라고도 함)을 추가해야 합니다.

Azure AD B2C 테넌트에 애플리케이션을 업데이트하려면 새로운 통합 **앱 등록** 환경 또는 레거시 **애플리케이션(레거시)** 환경을 사용하면 됩니다. [새 환경에 대해 자세히 알아보세요](https://aka.ms/b2cappregtraining).

#### <a name="app-registrations"></a>[앱 등록](#tab/app-reg-ga/)

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. 상단 메뉴에서 **디렉터리 + 구독** 필터를 선택한 다음, Azure AD B2C 테넌트가 포함된 디렉터리를 선택합니다.
1. 왼쪽 메뉴에서 **Azure AD B2C** 를 선택합니다. 또는 **모든 서비스** 를 선택하고 **Azure AD B2C** 를 검색하여 선택합니다.
1. **앱 등록** 을 선택하고 **소유한 애플리케이션** 탭을 선택한 다음, *webapp1* 애플리케이션을 선택합니다.
1. **웹** 아래에서 **URI 추가** 링크를 선택하고 `http://localhost:6420`을 입력합니다.
1. **암시적 허용** 에서 아직 선택하지 않은 경우 **액세스 토큰** 및 **ID 토큰** 에 대한 확인란을 선택한 다음, **저장** 을 선택합니다.
1. **개요** 를 선택합니다.
1. 단일 페이지의 웹 애플리케이션에서 코드를 업데이트하는 이후 단계에 사용할 수 있도록 **애플리케이션(클라이언트) ID** 를 기록합니다.

#### <a name="applications-legacy"></a>[애플리케이션(레거시)](#tab/applications-legacy/)

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. Azure AD B2C 테넌트를 포함하는 디렉터리를 사용하려면 위쪽 메뉴에서 **디렉터리 + 구독** 필터를 선택하고, 테넌트가 포함된 디렉터리를 선택합니다.
1. Azure Portal의 왼쪽 상단 모서리에서 **모든 서비스** 를 선택한 다음, **Azure AD B2C** 를 검색하여 선택합니다.
1. **애플리케이션(레거시)** 을 선택한 다음, *webapp1* 애플리케이션을 선택합니다.
1. **회신 URL** 아래에서 `http://localhost:6420`을 추가합니다.
1. **저장** 을 선택합니다.
1. 속성 페이지에서 **애플리케이션 ID** 를 기록해둡니다. 앱 ID는 나중에 나오는 단계에서 단일 페이지 웹 애플리케이션의 코드를 업데이트할 때 사용됩니다.

* * *

## <a name="get-the-sample-code"></a>샘플 코드 가져오기

이 자습서에서는 GitHub에서 다운로드한 코드 샘플을 B2C 테넌트에서 작동하도록 구성합니다. 이 샘플은 단일 페이지 애플리케이션에서 가입, 로그인 및 보호되는 웹 API(이 시리즈의 다음 자습서에서 웹 API를 사용하도록 설정) 호출에 Azure AD B2C를 사용하는 방법을 보여줍니다.

GitHub에서 [zip 파일을 다운로드](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp/archive/master.zip)하거나 샘플을 복제합니다.

```
git clone https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp.git
```

## <a name="update-the-sample"></a>샘플 업데이트

샘플을 확보했으면 이전 단계에서 기록해둔 Azure AD B2C 테넌트 이름과 애플리케이션 ID로 코드를 업데이트합니다.

1. *JavaScriptSPA* 폴더 안에서 *authConfig.js* 파일을 엽니다.
1. `msalConfig` 개체에서 다음을 업데이트합니다.
    * `clientId`를 이전 단계에서 적어 둔 **애플리케이션(클라이언트) ID** 값으로 업데이트
    * `authority` URI를 Azure AD B2C 이름 및 필수 구성 요소 중 하나로 만든 등록/로그인 사용자 흐름의 이름(예: *B2C_1_signupsignin1* )으로 업데이트

    ```javascript
    const msalConfig = {
        auth: {
          clientId: "00000000-0000-0000-0000-000000000000", // Replace this value with your Application (client) ID
          authority: b2cPolicies.authorities.signUpSignIn.authority,
          validateAuthority: false
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
      scopes: apiConfig.b2cScopes // i.e. ["https://fabrikamb2c.onmicrosoft.com/helloapi/demo.read"]
    };
    ```

1. *JavaScriptSPA* 폴더 안에서 *authConfig.js* 파일을 엽니다.
1. `msalConfig` 개체에서 다음을 업데이트합니다.
    * `clientId`를 이전 단계에서 기록한 **애플리케이션(클라이언트) ID** 로 업데이트
    * `authority` URI를 Azure AD B2C 이름 및 필수 구성 요소 중 하나로 만든 등록/로그인 사용자 흐름의 이름(예: *B2C_1_signupsignin1* )으로 업데이트
1. *policies.js* 파일을 엽니다.
1. `names` 및 `authorities`에 대한 항목을 찾아 2단계에서 만든 정책 이름으로 적절하게 바꿉니다. `fabrikamb2c.onmicrosoft.com`을 Azure AD B2C 테넌트의 이름(예: `https://<your-tenant-name>.b2clogin.com/<your-tenant-name>.onmicrosoft.com/<your-sign-in-sign-up-policy>`)으로 바꿉니다.
1. *apiConfig.js* 파일을 엽니다.
1. 범위 `b2cScopes`에 대한 할당을 찾아 URL을 Web API용으로 만든 범위 URL(예: `b2cScopes: ["https://<your-tenant-name>.onmicrosoft.com/helloapi/demo.read"]`)로 바꿉니다.
1. API URL `webApi`에 대한 할당을 찾아 현재 URL을 4단계에서 Web API를 배포한 URL(예: `webApi: http://localhost:5000/hello`)로 바꿉니다.

결과 코드는 다음과 같아야 합니다.

### <a name="authconfigjs"></a>authConfig.js

```javascript
const msalConfig = {
  auth: {
    clientId: "e760cab2-b9a1-4c0d-86fb-ff7084abd902",
    authority: b2cPolicies.authorities.signUpSignIn.authority,
    validateAuthority: false
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
  scopes: apiConfig.b2cScopes // i.e. ["https://fabrikamb2c.onmicrosoft.com/helloapi/demo.read"]
};
```
### <a name="policiesjs"></a>policies.js

```javascript
const b2cPolicies = {
    names: {
        signUpSignIn: "b2c_1_susi",
        forgotPassword: "b2c_1_reset",
        editProfile: "b2c_1_edit_profile"
    },
    authorities: {
        signUpSignIn: {
            authority: "https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/b2c_1_susi",
        },
        forgotPassword: {
            authority: "https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/b2c_1_reset",
        },
        editProfile: {
            authority: "https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/b2c_1_edit_profile"
        }
    },
}
```
### <a name="apiconfigjs"></a>apiConfig.js

```javascript
const apiConfig = {
  b2cScopes: ["https://fabrikamb2c.onmicrosoft.com/helloapi/demo.read"],
  webApi: "https://fabrikamb2chello.azurewebsites.net/hello"
};
```

## <a name="run-the-sample"></a>샘플 실행

1. 콘솔 창을 열고 샘플이 포함된 디렉터리로 변경합니다. 예를 들면 다음과 같습니다.

    ```console
    cd active-directory-b2c-javascript-msal-singlepageapp
    ```
1. 다음 명령을 실행합니다.

    ```console
    npm install && npm update
    npm start
    ```

    콘솔 창에 로컬에서 실행 중인 Node.js 서버의 포트 번호가 표시됩니다.

    ```console
    Listening on port 6420...
    ```
1. `http://localhost:6420`으로 이동하여 로컬 머신에서 실행 중인 웹 애플리케이션을 봅니다.

    :::image type="content" source="media/tutorial-single-page-app/web-app-spa-01-not-logged-in.png" alt-text="로컬로 실행되는 단일 페이지 애플리케이션을 보여주는 웹 브라우저":::

### <a name="sign-up-using-an-email-address"></a>전자 메일 주소를 사용하여 등록

이 샘플 애플리케이션은 가입, 로그인 및 암호 재설정을 지원합니다. 이 자습서에서는 이메일 주소를 사용하여 가입합니다.

1. **로그인** 을 선택하여 이전 단계에서 지정한 *B2C_1_signupsignin1* 사용자 흐름을 시작합니다.
1. Azure AD B2C에서 가입 링크가 있는 로그인 페이지를 제공합니다. 아직 계정이 없으므로 **지금 가입** 링크를 선택합니다.
1. 가입 워크플로에서 이메일 주소를 사용하여 사용자의 ID를 수집하고 확인하는 페이지를 제공합니다. 또한 가입 워크플로에서는 사용자 흐름에 정의된 사용자의 암호와 요청된 특성을 수집합니다.

    유효한 이메일 주소를 사용하고 확인 코드를 사용하여 유효성을 검사합니다. 암호를 설정합니다. 요청된 특성에 대한 값을 입력합니다.

    :::image type="content" source="media/tutorial-single-page-app/user-flow-sign-up-workflow-01.png" alt-text="Azure AD B2C 사용자 흐름에 표시된 가입 페이지":::

1. **만들기** 를 선택하여 로컬 계정을 Azure AD B2C 디렉터리에 만듭니다.

**만들기** 를 선택하면 애플리케이션이 로그인한 사용자의 이름을 표시합니다.

:::image type="content" source="media/tutorial-single-page-app/web-app-spa-02-logged-in.png" alt-text="사용자가 로그인한 단일 페이지 애플리케이션을 보여주는 웹 브라우저":::

로그인을 테스트하려면 **로그아웃** 단추를 선택한 다음, **로그인** 을 선택하고 가입할 때 입력한 이메일 주소와 암호를 사용하여 로그인합니다.

### <a name="what-about-calling-the-api"></a>API 호출은 어떻게 할까요?

로그인한 후 **API 호출** 단추를 선택하면 API 호출의 결과 대신 가입/로그인 사용자 흐름 페이지가 표시됩니다. 이는 예상된 동작입니다. *내* Azure AD B2C 테넌트에 등록된 웹 API 애플리케이션과 통신하도록 애플리케이션의 API 부분을 아직 구성하지 않았기 때문입니다.

현재 시점에서 애플리케이션은 여전히 데모 테넌트(fabrikamb2c.onmicrosoft.com)에 등록된 API와 통신하려고 시도하지만, 사용자가 아직 해당 테넌트에 인증되지 않았기 때문에 가입/로그인 페이지가 표시됩니다.

이 시리즈의 다음 자습서로 이동하여 보호된 API를 사용하도록 설정하세요([다음 단계](#next-steps) 섹션 참조).

## <a name="next-steps"></a>다음 단계

이 자습서에서는 가입 및 로그인 기능을 제공하기 위해 Azure AD B2C 테넌트의 사용자 흐름과 연동되는 단일 페이지 애플리케이션을 구성했습니다. 다음 단계를 완료했습니다.

> [!div class="checklist"]
> * Azure AD B2C 테넌트에 등록된 애플리케이션에 회신 URL을 추가했습니다.
> * GitHub에서 코드 샘플을 다운로드했습니다.
> * 해당 테넌트에서 작동하도록 샘플 애플리케이션의 코드를 수정했습니다.
> * 가입/로그인 사용자 흐름을 사용하여 가입했습니다.

이제 시리즈의 다음 자습서로 이동하여 SPA에서 보호된 웹 API에 대한 액세스 권한을 부여하세요.

> [!div class="nextstepaction"]
> [자습서: 단일 페이지 애플리케이션에서 웹 API를 보호하고 액세스 권한 부여](tutorial-single-page-app-webapi.md)
