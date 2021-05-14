---
title: Azure AD B2C와 함께 MSAL.js 사용
titleSuffix: Microsoft identity platform
description: MSAL.js(JavaScript용 Microsoft 인증 라이브러리)는 애플리케이션이 Azure AD B2C와 함께 작업하고 토큰을 획득하여 보안 웹 API를 호출할 수 있도록 합니다. 이러한 웹 API에는 Microsoft Graph, 기타 Microsoft API, 타사 웹 API 또는 사용자의 웹 API가 있습니다.
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
ms.custom: aaddev devx-track-js
ms.openlocfilehash: 8ff54a40120bed040ec25fb3f18385dba3b0bf11
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106063664"
---
# <a name="use-the-microsoft-authentication-library-for-javascript-to-work-with-azure-ad-b2c"></a>Azure AD B2C와 함께 작업하려면 JavaScript용 Microsoft 인증 라이브러리 사용

[MSAL.js(JavaScript용 Microsoft 인증 라이브러리)](https://github.com/AzureAD/microsoft-authentication-library-for-js)를 사용하면 JavaScript 개발자가 Azure AD B2C([Azure Active Directory B2C](../../active-directory-b2c/overview.md))를 사용하여 소셜 및 로컬 ID로 사용자를 인증할 수 있습니다.

Azure AD B2C를 ID 관리 서비스로 사용하면 고객이 애플리케이션을 사용할 때 프로필을 등록, 로그인, 관리하는 방법을 사용자 정의하고 제어할 수 있습니다. 또한 Azure AD B2C를 사용하면 인증 프로세스 중에 애플리케이션이 표시하는 UI를 브랜딩 및 사용자 지정할 수 있습니다.

다음 섹션에서는 다음을 수행하는 방법을 설명합니다.

- Node.js 웹 API 보호
- SPA(단일 페이지 애플리케이션)의 로그인 지원 및 보호‘된’ 웹 API 호출
- 암호 재설정 지원을 사용

## <a name="prerequisites"></a>사전 요구 사항

아직 없다면 [Azure AD B2C 테넌트](../../active-directory-b2c/tutorial-create-tenant.md)를 만듭니다.

## <a name="nodejs-web-api"></a>Node.js Web API

다음 단계는 **웹 API** 가 Azure AD B2C를 사용하여 자신을 보호하고 선택한 범위를 클라이언트 애플리케이션에 노출하는 방법을 보여 줍니다.

Node용 MSAL.js는 현재 개발 중입니다. 자세한 내용은 GitHub에서 [로드맵](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki#roadmap)을 참조하세요. 현재 Microsoft에서 개발하고 지원하는 Node.js에 대한 인증 라이브러리인 [passport-azure-ad](https://github.com/AzureAD/passport-azure-ad)를 사용하는 것이 좋습니다.

### <a name="step-1-register-your-application"></a>1단계: 애플리케이션 등록

Azure AD B2C로 웹 API를 보호하려면 먼저 등록해야 합니다. 자세한 단계는 [애플리케이션 등록](../../active-directory-b2c/add-web-api-application.md)을 참조하세요.

### <a name="step-2-download-the-sample-application"></a>2단계: 샘플 애플리케이션 다운로드

샘플을 zip 파일로 다운로드하거나 GitHub에서 복제합니다.

```console
git clone https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi.git
```

### <a name="step-3-configure-authentication"></a>3단계: 인증 구성

1. 샘플에서 *config.json* 파일을 엽니다.

2. 애플리케이션을 등록하는 동안 이전에 가져온 애플리케이션 자격 증명을 사용하여 샘플을 구성합니다. 값을 테넌트 이름, 클라이언트 ID, 정책 이름으로 바꿔 다음 코드 줄을 변경합니다.

    ```json
         "credentials": {
             "tenantName": "<your-tenant-name>",
             "clientID": "<your-webapi-application-ID>"
         },
         "policies": {
             "policyName": "B2C_1_signupsignin1"
         },
         "resource": {
             "scope": ["demo.read"] 
         },
    ```

자세한 내용은 [Node.js B2C web API sample](https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi)을 확인하세요.

## <a name="javascript-spa"></a>JavaScript SPA

다음 단계는 **단일 페이지 애플리케이션** 에서 Azure AD B2C를 사용하여 사용자 등록과 로그인을 처리하고 보호되는 웹 API를 호출하는 방법을 보여 줍니다.

### <a name="step-1-register-your-application"></a>1단계: 애플리케이션 등록

인증을 구현하려면 먼저 애플리케이션을 등록해야 합니다. 자세한 단계는 [애플리케이션 등록](../../active-directory-b2c/tutorial-register-applications.md)을 참조하세요.

### <a name="step-2-download-the-sample-application"></a>2단계: 샘플 애플리케이션 다운로드

코드 샘플의 [.ZIP 보관 파일](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp/archive/master.zip)을 다운로드하거나 GitHub 리포지토리를 복제합니다.

```console
git clone https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp.git
```

### <a name="step-3-configure-authentication"></a>3단계: 인증 구성

애플리케이션을 구성하는 데에는 두 가지 요소가 있습니다.

- API 엔드포인트 및 노출된 범위 구성
- 인증 매개 변수 및 토큰 범위 구성

1. 샘플에서 *apiConfig.js* 파일을 엽니다.

2. 웹 API를 등록하는 동안 이전에 가져온 매개 변수를 사용하여 샘플을 구성합니다. 값을 웹 API 및 노출 범위 주소로 바꿔 다음 코드 줄을 변경합니다.

   ```javascript
    // The current application coordinates were pre-registered in a B2C tenant.
    const apiConfig = {
        b2cScopes: ["https://fabrikamb2c.onmicrosoft.com/helloapi/demo.read"], //API scopes you exposed during api registration
        webApi: "https://fabrikamb2chello.azurewebsites.net/hello"
    };
   ```

1. 샘플에서 *authConfig.js* 파일을 엽니다.

1. 단일 페이지 애플리케이션을 등록하는 동안 이전에 가져온 매개 변수를 사용하여 샘플을 구성합니다. 값을 ClientId, 기관 메타데이터, 토큰 요청 범위로 바꿔 다음 코드 줄을 변경합니다.

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

자세한 내용은 이 [JavaScript B2C 단일 페이지 애플리케이션 샘플](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp)을 확인하세요.

## <a name="support-password-reset"></a>암호 재설정 지원

이 섹션에서는 Azure AD B2C 암호 재설정 사용자 흐름을 사용하도록 단일 페이지 애플리케이션을 확장합니다. MSAL.js는 현재 여러 사용자 흐름 또는 사용자 지정 정책을 기본적으로 지원하지 않지만 라이브러리를 사용하여 암호 재설정과 같은 일반적인 사용 사례를 처리할 수 있습니다.

다음 단계에서는 이전 [JavaScript SPA](#javascript-spa) 섹션의 단계를 이미 수행한 것으로 가정합니다.

### <a name="step-1-define-the-authority-string-for-password-reset-user-flow"></a>1 단계: 암호 재설정 사용자 흐름에 대한 기관 문자열 정의

1. 먼저, 기관 URI를 저장하는 개체를 만듭니다.

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

1. 그런 다음 `signInSignUp` 정책을 기본값으로 사용하여 MSAL 개체를 초기화합니다(이전 코드 조각 참조). 사용자가 로그인을 시도하면 다음 화면이 표시됩니다.

    :::image type="content" source="media/msal-b2c-overview/user-journey-01-signin.png" alt-text="Azure AD B2C에서 표시하는 로그인 화면":::

### <a name="step-2-catch-and-handle-authentication-errors-in-your-login-method"></a>2 단계: 로그인 메서드에서 인증 오류 catch 및 처리

사용자가 **암호 잊음** 을 선택하면 애플리케이션은 코드에 catch해야 할 오류를 throw한 다음 적절한 사용자 흐름을 표시하여 처리합니다. 이 경우 `b2c_1_reset` 암호 재설정 흐름입니다.

1. 로그인 방법을 다음과 같이 확장합니다.

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

1. 이전 코드 조각은 코드 `AADB2C90118` 오류를 catch한 후 암호 재설정 화면을 표시하는 방법을 보여 줍니다.

    암호를 재설정한 후 다시 로그인하도록 사용자가 다시 애플리케이션에 반환됩니다.

    :::image type="content" source="media/msal-b2c-overview/user-journey-02-password-reset.png" alt-text="Azure AD B2C에 의해 표시된 암호 재설정 흐름 화면" border="false":::

    오류 코드 및 예외 처리에 대한 자세한 내용은 [MSAL 오류 및 예외 코드](msal-error-handling-js.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계

Azure AD B2C 개념에 관해 자세히 알아보세요.

- [사용자 흐름](../../active-directory-b2c/tutorial-create-user-flows.md)
- [사용자 지정 정책](../../active-directory-b2c/custom-policy-get-started.md)
- [UX 사용자 지정](../../active-directory-b2c/configure-user-input.md)
