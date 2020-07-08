---
title: Azure AD B2C에서 MSAL.js 사용
titleSuffix: Microsoft identity platform
description: JavaScript 용 Microsoft Authentication Library (MSAL.js)를 사용 하면 응용 프로그램에서 Azure AD B2C를 사용 하 여 보안 웹 Api를 호출 하는 토큰을 가져올 수 있습니다. 이러한 웹 API에는 Microsoft Graph, 기타 Microsoft API, 타사 웹 API 또는 사용자의 웹 API가 있습니다.
services: active-directory
author: negoe
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 06/05/2020
ms.author: negoe
ms.reviewer: nacanuma
ms.custom: aaddev
ms.openlocfilehash: f43711652bb205c75870fdb969c44298087a2b07
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "84308580"
---
# <a name="use-microsoft-authentication-library-for-javascript-to-work-with-azure-ad-b2c"></a>JavaScript 용 Microsoft 인증 라이브러리를 사용 하 여 Azure AD B2C 작업

Javascript 개발자는 [MSAL.js (Microsoft Authentication Library For javascript)](https://github.com/AzureAD/microsoft-authentication-library-for-js) 를 사용 하 여 [Azure Active Directory B2C](../../active-directory-b2c/overview.md) (Azure AD B2C)를 통해 소셜 및 로컬 id로 사용자를 인증할 수 있습니다.

Id 관리 서비스로 Azure AD B2C를 사용 하 여 고객이 응용 프로그램을 사용 하는 경우 자신의 프로필에 등록 하 고 로그인 하 고 관리 하는 방법을 사용자 지정 하 고 제어할 수 있습니다. 또한 Azure AD B2C를 사용 하면 인증 프로세스 중에 응용 프로그램에 표시 되는 UI를 브랜드 및 사용자 지정할 수 있습니다.

다음 섹션에서는 다음을 수행 하는 방법을 보여 줍니다.

- Node.js web API 보호
- SPA (단일 페이지 응용 프로그램)에서의 로그인을 지원 하 고 보호 *된* 웹 API를 호출 합니다.
- 암호 재설정 지원 사용

## <a name="prerequisites"></a>사전 요구 사항

아직 없는 경우 [Azure AD B2C 테 넌 트](../../active-directory-b2c/tutorial-create-tenant.md)를 만듭니다.

## <a name="nodejs-web-api"></a>Node.js Web API

다음 단계는 **WEB API** 가 Azure AD B2C를 사용 하 여 자신을 보호 하 고 선택한 범위를 클라이언트 응용 프로그램에 노출 하는 방법을 보여 줍니다.

노드에 대 한 MSAL.js 현재 개발 중입니다. 자세한 내용은 GitHub에서 [로드맵](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki#roadmap) 을 참조 하세요. 현재 Microsoft에서 개발 하 고 지 원하는 Node.js에 대 한 인증 라이브러리인 [passport-ad](https://github.com/AzureAD/passport-azure-ad)를 사용 하는 것이 좋습니다.

### <a name="step-1-register-your-application"></a>1단계: 애플리케이션 등록

Azure AD B2C를 사용 하 여 web API를 보호 하려면 먼저 등록 해야 합니다. 자세한 단계는 [애플리케이션 등록](../../active-directory-b2c/add-web-application.md)을 참조하세요.

### <a name="step-2-download-the-sample-application"></a>2 단계: 샘플 응용 프로그램 다운로드

샘플을 zip 파일로 다운로드하거나 GitHub에서 복제합니다.

```console
git clone https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi.git
```

### <a name="step-3-configure-authentication"></a>3 단계: 인증 구성

1. 샘플에서 `config.js` 파일을 엽니다.

2. 응용 프로그램을 등록 하는 동안 이전에 가져온 응용 프로그램 자격 증명을 사용 하 여 샘플을 구성 합니다. 값을 clientID, 호스트, tenantId 및 정책 이름의 이름으로 바꿔 다음 코드 줄을 변경 합니다.

```JavaScript
const clientID = "<Application ID for your Node.js web API - found on Properties page in Azure portal e.g. 93733604-cc77-4a3c-a604-87084dd55348>";
const b2cDomainHost = "<Domain of your B2C host eg. fabrikamb2c.b2clogin.com>";
const tenantId = "<your-tenant-ID>.onmicrosoft.com"; // Alternatively, you can use your Directory (tenant) ID (GUID)
const policyName = "<Name of your sign in / sign up policy, e.g. B2C_1_signupsignin1>";
```

자세한 내용은이 [Node.js B2C WEB API 샘플](https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi)을 확인 하세요.

## <a name="javascript-spa"></a>JavaScript SPA

다음 단계에서는 **단일 페이지 응용 프로그램이** Azure AD B2C를 사용 하 여 보호 된 웹 API를 등록, 로그인 및 호출 하는 방법을 보여 줍니다.

### <a name="step-1-register-your-application"></a>1단계: 애플리케이션 등록

인증을 구현하려면 먼저 애플리케이션을 등록해야 합니다. 자세한 단계는 [애플리케이션 등록](../../active-directory-b2c/tutorial-register-applications.md)을 참조하세요.

### <a name="step-2-download-the-sample-application"></a>2 단계: 샘플 응용 프로그램 다운로드

코드 샘플을 다운로드 [합니다. ZIP 보관 파일을 보관](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp/archive/master.zip) 하거나 GitHub 리포지토리를 복제 합니다.

```console
git clone https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp.git
```

### <a name="step-3-configure-authentication"></a>3 단계: 인증 구성

응용 프로그램을 구성할 때는 다음 두 가지 사항을 다룹니다.

- API 끝점 및 노출 된 범위 구성
- 인증 매개 변수 및 토큰 범위 구성

1. 샘플에서 *apiConfig.js* 파일을 엽니다.

2. 웹 API를 등록 하는 동안 이전에 얻은 매개 변수를 사용 하 여 샘플을 구성 합니다. 값을 web API 및 노출 된 범위의 주소로 바꿔 다음 코드 줄을 변경 합니다.

   ```javascript
    // The current application coordinates were pre-registered in a B2C tenant.
    const apiConfig = {
        b2cScopes: ["https://fabrikamb2c.onmicrosoft.com/helloapi/demo.read"], //API scopes you exposed during api registration
        webApi: "https://fabrikamb2chello.azurewebsites.net/hello"
    };
   ```

1. 샘플에서 *authConfig.js* 파일을 엽니다.

1. 단일 페이지 응용 프로그램을 등록 하는 동안 이전에 얻은 매개 변수를 사용 하 여 샘플을 구성 합니다. 값을 ClientId, 기관 메타 데이터 및 토큰 요청 범위로 바꿔서 다음 코드 줄을 변경 합니다.

   ```javascript
    // Config object to be passed to Msal on creation.
    const msalConfig = {
        auth: {
            clientId: "e760cab2-b9a1-4c0d-86fb-ff7084abd902",
            authority: "https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/B2C_1_signupsignin1",
            validateAuthority: false
        },
        cache: {
            cacheLocation: "localStorage", // This configures where your cache will be stored
            storeAuthStateInCookie: false // Set this to "true" to save cache in cookies
        }
    };

    // Add here scopes for id token to be used at the MS Identity Platform endpoint
    const loginRequest = {
        scopes: ["openid", "profile"],
    };
   ```

자세한 내용은이 [JAVASCRIPT B2C 단일 페이지 응용 프로그램 샘플](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp)을 확인 하세요.

## <a name="support-password-reset"></a>암호 재설정 지원

이 섹션에서는 Azure AD B2C 암호 재설정 사용자 흐름을 사용 하도록 단일 페이지 응용 프로그램을 확장 합니다. MSAL.js는 현재 여러 사용자 흐름이 나 사용자 지정 정책을 기본적으로 지원 하지 않지만, 라이브러리를 사용 하 여 암호 재설정과 같은 일반적인 사용 사례를 처리할 수 있습니다.

다음 단계에서는 이전 [JAVASCRIPT SPA](#javascript-spa) 섹션의 단계를 이미 수행한 것으로 가정 합니다.

### <a name="step-1-define-the-authority-string-for-password-reset-user-flow"></a>1 단계: 암호 재설정 사용자 흐름에 대 한 기관 문자열 정의

1. 먼저, 기관 Uri를 저장 하는 개체를 만듭니다.

    ```javascript
        const b2cPolicies = {
            names: {
                signUpSignIn: "b2c_1_susi",
                forgotPassword: "b2c_1_reset"
            },
            authorities: {
                signUpSignIn: {
                    authority: "https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/b2c_1_susi",
                },
                forgotPassword: {
                    authority: "https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/b2c_1_reset",
                },
            },
        }
    ```

1. 그런 다음 정책을 사용 하 여 MSAL 개체를 `signInSignUp` 기본값으로 초기화 합니다 (이전 코드 조각 참조). 사용자가 로그인 하려고 하면 다음과 같은 화면이 표시 됩니다.

    :::image type="content" source="media/msal-b2c-overview/user-journey-01-signin.png" alt-text="Azure AD B2C에서 표시 하는 로그인 화면":::

### <a name="step-2-catch-and-handle-authentication-errors-in-your-login-method"></a>2 단계: 로그인 메서드에서 인증 오류 Catch 및 처리

사용자가 **암호 잊음**를 선택 하면 응용 프로그램에서 오류를 throw 하 고 코드에서 catch 한 다음 적절 한 사용자 흐름을 제시 하 여를 처리 합니다. 이 경우 `b2c_1_reset` 암호 다시 설정 흐름입니다.

1. 다음과 같이 로그인 방법을 확장 합니다.

    ```javascript
    function signIn() {
      myMSALObj.loginPopup(loginRequest)
        .then(loginResponse => {
            console.log("id_token acquired at: " + new Date().toString());

            if (myMSALObj.getAccount()) {
              updateUI();
            }

        }).catch(function (error) {
          console.log(error);

          // error handling
          if (error.errorMessage) {
            // check for forgot password error
            if (error.errorMessage.indexOf("AADB2C90118") > -1) {

              //call login method again with the password reset user flow
              myMSALObj.loginPopup(b2cPolicies.authorities.forgotPassword)
                .then(loginResponse => {
                  console.log(loginResponse);
                  window.alert("Password has been reset successfully. \nPlease sign-in with your new password.");
                })
            }
          }
        });
    }
    ```

1. 위의 코드 조각에서는 코드를 사용 하 여 오류를 catch 한 후 암호 재설정 화면을 표시 하는 방법을 보여 줍니다 `AADB2C90118` .

    암호를 재설정 한 후 다시 로그인 하도록 사용자가 다시 응용 프로그램에 반환 됩니다.

    :::image type="content" source="media/msal-b2c-overview/user-journey-02-password-reset.png" alt-text="Azure AD B2C 표시 된 암호 다시 설정 흐름 화면" border="false":::

    오류 코드 및 예외 처리에 대 한 자세한 내용은 [Msal 오류 및 예외 코드](msal-handling-exceptions.md)를 참조 하세요.

## <a name="next-steps"></a>다음 단계

이러한 Azure AD B2C 개념에 대해 자세히 알아보세요.

- [사용자 흐름](../../active-directory-b2c/tutorial-create-user-flows.md)
- [사용자 지정 정책](../../active-directory-b2c/custom-policy-get-started.md)
- [UX 사용자 지정](../../active-directory-b2c/custom-policy-configure-user-input.md)
