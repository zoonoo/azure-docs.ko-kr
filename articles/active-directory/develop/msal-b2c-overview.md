---
title: Azure Active Directory B2CLearn에서 MSAL 사용 | Microsoft
titleSuffix: Microsoft identity platform
description: JavaScript 용 Microsoft Authentication Library (MSAL)를 사용 하면 응용 프로그램에서 Azure AD B2C 작업 하 고 보안 웹 Api를 호출 하는 토큰을 가져올 수 있습니다. 이러한 웹 API에는 Microsoft Graph, 기타 Microsoft API, 타사 웹 API 또는 사용자의 웹 API가 있습니다.
services: active-directory
author: negoe
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/16/2019
ms.author: negoe
ms.reviewer: nacanuma
ms.custom: aaddev
ms.openlocfilehash: 8e076dfd6670265d458eb35d8e1b3e4500009a12
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81534485"
---
# <a name="use-microsoft-authentication-library-for-javascript-to-work-with-azure-active-directory-b2c"></a>JavaScript 용 Microsoft 인증 라이브러리를 사용 하 여 Azure Active Directory B2C 작업

[Javascript 용 Microsoft Authentication Library (MSAL)](https://github.com/AzureAD/microsoft-authentication-library-for-js) 를 사용 하면 javascript 개발자가 [Azure Active Directory B2C (Azure AD B2C)](https://docs.microsoft.com/azure/active-directory-b2c/)를 사용 하 여 소셜 및 로컬 id를 사용 하 여 사용자를 인증할 수 있습니다. Id 관리 서비스로 Azure AD B2C를 사용 하 여 고객이 응용 프로그램을 사용 하는 경우 자신의 프로필을 등록, 로그인 및 관리 하는 방법을 사용자 지정 하 고 제어할 수 있습니다.

또한 Azure AD B2C를 사용 하면 고객에 게 원활한 환경을 제공 하기 위해 인증 프로세스 중에 응용 프로그램의 UI를 브랜드 및 사용자 지정할 수 있습니다.

이 문서에서는 MSAL를 사용 하 여 Azure AD B2C 작업 하 고 알고 있어야 하는 핵심 요소를 요약 하는 방법을 보여 줍니다. 전체 토론 및 자습서를 보려면 [Azure AD B2C 설명서](https://docs.microsoft.com/azure/active-directory-b2c/overview)를 참조 하세요.

## <a name="prerequisites"></a>전제 조건

자체 [Azure AD B2C 테 넌 트](https://docs.microsoft.com/azure/active-directory-b2c/tutorial-create-tenant)를 아직 만들지 않은 경우 지금 만들기로 시작 합니다 (이미 있는 경우 기존 Azure AD B2C 테 넌 트를 사용할 수 있음).

이 데모는 다음과 같은 두 부분으로 구성 됩니다.

- 웹 API를 보호 하는 방법
- 단일 페이지 응용 프로그램을 등록 하 여 *해당* web API를 인증 하 고 호출 하는 방법입니다.

## <a name="nodejs-web-api"></a>Node.js Web API

> [!NOTE]
> 현재 노드의 MSAL는 아직 개발 중입니다 ( [로드맵](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki#roadmap)참조). 그 동안에는 Microsoft에서 개발 하 고 지 원하는 node.js 용 인증 라이브러리인 [passport-ad](https://github.com/AzureAD/passport-azure-ad)를 사용 하는 것이 좋습니다.

다음 단계는 **WEB API** 가 Azure AD B2C를 사용 하 여 자신을 보호 하 고 선택한 범위를 클라이언트 응용 프로그램에 노출 하는 방법을 보여 줍니다.

### <a name="step-1-register-your-application"></a>1단계: 애플리케이션 등록

Azure AD B2C를 사용 하 여 web API를 보호 하려면 먼저 등록 해야 합니다. 자세한 단계는 [애플리케이션 등록](https://docs.microsoft.com/azure/active-directory-b2c/add-web-application?tabs=applications)을 참조하세요.

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

자세한 내용은이 [NODE.JS B2C WEB API 샘플](https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi)을 확인 하세요.

---

## <a name="javascript-spa"></a>JavaScript SPA

다음 단계에서는 **단일 페이지 응용 프로그램이** Azure AD B2C를 사용 하 여 보호 된 웹 API를 등록, 로그인 및 호출 하는 방법을 보여 줍니다.

### <a name="step-1-register-your-application"></a>1단계: 애플리케이션 등록

인증을 구현하려면 먼저 애플리케이션을 등록해야 합니다. 자세한 단계는 [애플리케이션 등록](https://docs.microsoft.com/azure/active-directory-b2c/tutorial-register-applications)을 참조하세요.

### <a name="step-2-download-the-sample-application"></a>2 단계: 샘플 응용 프로그램 다운로드

샘플을 zip 파일로 다운로드하거나 GitHub에서 복제합니다.

```console
git clone https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp.git
```

### <a name="step-3-configure-authentication"></a>3 단계: 인증 구성

응용 프로그램을 구성할 때는 다음 두 가지 사항을 다룹니다.

- API 끝점 및 노출 된 범위 구성
- 인증 매개 변수 및 토큰 범위 구성

1. 샘플에서 `apiConfig.js` 파일을 엽니다.

2. 웹 API를 등록 하는 동안 이전에 얻은 매개 변수를 사용 하 여 샘플을 구성 합니다. 값을 web API 및 노출 된 범위의 주소로 바꿔 다음 코드 줄을 변경 합니다.

   ```javascript
    // The current application coordinates were pre-registered in a B2C tenant.
    const apiConfig = {
        b2cScopes: ["https://fabrikamb2c.onmicrosoft.com/helloapi/demo.read"], //API scopes you exposed during api registration
        webApi: "https://fabrikamb2chello.azurewebsites.net/hello"
    };
   ```

3. 샘플에서 `authConfig.js` 파일을 엽니다.

4. 단일 페이지 응용 프로그램을 등록 하는 동안 이전에 얻은 매개 변수를 사용 하 여 샘플을 구성 합니다. 값을 ClientId, 기관 메타 데이터 및 토큰 요청 범위로 바꿔서 다음 코드 줄을 변경 합니다.

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

---

## <a name="next-steps"></a>다음 단계

다음에 대해 자세히 알아봅니다.
- [사용자 흐름](https://docs.microsoft.com/azure/active-directory-b2c/tutorial-create-user-flows)
- [사용자 지정 정책](https://docs.microsoft.com/azure/active-directory-b2c/custom-policy-get-started)
- [UX 사용자 지정](https://docs.microsoft.com/azure/active-directory-b2c/custom-policy-configure-user-input)
