---
title: Azure Active 디렉토리 B2CLearn과 함께 MSAL 사용 | Azure
titleSuffix: Microsoft identity platform
description: 자바 스크립트에 대 한 Microsoft 인증 라이브러리 (MSAL.js) 응용 프로그램 Azure AD B2C와 함께 작동 하 고 보안된 웹 API를 호출 하는 토큰을 획득 할 수 있습니다. 이러한 웹 API에는 Microsoft Graph, 기타 Microsoft API, 타사 웹 API 또는 사용자의 웹 API가 있습니다.
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
ms.openlocfilehash: dc8a330bc09f37f7941534ed7c17d1ffd14d08c5
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/08/2020
ms.locfileid: "80875965"
---
# <a name="use-microsoft-authentication-library-for-javascript-to-work-with-azure-active-directory-b2c"></a>자바 스크립트에 대 한 마이크로소프트 인증 라이브러리를 사용 하 여 Azure 활성 디렉토리 B2C와 함께 작동

[자바 스크립트에 대한 마이크로 소프트 인증 라이브러리 (MSAL.js)를](https://github.com/AzureAD/microsoft-authentication-library-for-js) 사용하면 자바 스크립트 개발자는 [Azure Active Directory B2C (Azure AD B2C)를](https://docs.microsoft.com/azure/active-directory-b2c/)사용하여 소셜 및 로컬 ID로 사용자를 인증 할 수 있습니다. Azure AD B2C를 ID 관리 서비스로 사용하면 고객이 응용 프로그램을 사용할 때 프로필에 등록, 로그인 및 관리하는 방법을 사용자 지정하고 제어할 수 있습니다.

또한 Azure AD B2C를 사용하면 인증 프로세스 중에 응용 프로그램의 UI를 브랜딩하고 사용자 지정하여 고객에게 원활한 환경을 제공할 수 있습니다.

이 문서에서는 MSAL.js를 사용하여 Azure AD B2C에서 작업하는 방법을 설명하고 알고 있어야 하는 주요 사항을 요약합니다. 전체 토론 및 자습서를 보려면 [Azure AD B2C 설명서를](https://docs.microsoft.com/azure/active-directory-b2c/overview)참조하십시오.

## <a name="prerequisites"></a>사전 요구 사항

아직 자체 [Azure AD B2C 테넌트를](https://docs.microsoft.com/azure/active-directory-b2c/tutorial-create-tenant)만들지 않은 경우 지금 만들기를 시작합니다(이미 있는 경우 기존 Azure AD B2C 테넌트를 사용할 수도 있음).

이 데모에는 다음 두 부분이 포함되어 있습니다.

- 웹 API를 보호하는 방법을 확인할 수 있습니다.
- 단일 페이지 응용 프로그램을 등록하여 해당 *웹* API를 인증하고 호출하는 방법.

## <a name="nodejs-web-api"></a>노드.js 웹 API

> [!NOTE]
> 현재 노드에 대한 MSAL.js는 아직 개발 [중입니다(로드맵](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki#roadmap)참조). 한편 Microsoft에서 개발하고 지원하는 Node.js에 대한 인증 라이브러리인 [여권 azure-ad를](https://github.com/AzureAD/passport-azure-ad)사용하는 것이 좋습니다.

다음 단계는 **웹 API가** Azure AD B2C를 사용하여 자신을 보호하고 선택한 범위를 클라이언트 응용 프로그램에 노출하는 방법을 보여 줍니다.

### <a name="step-1-register-your-application"></a>1단계: 애플리케이션 등록

Azure AD B2C를 사용하여 웹 API를 보호하려면 먼저 웹 API를 등록해야 합니다. 자세한 단계는 [애플리케이션 등록](https://docs.microsoft.com/azure/active-directory-b2c/add-web-application?tabs=applications)을 참조하세요.

### <a name="step-2-download-the-sample-application"></a>2단계: 샘플 응용 프로그램 다운로드

샘플을 zip 파일로 다운로드하거나 GitHub에서 복제합니다.

```console
git clone https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi.git
```

### <a name="step-3-configure-authentication"></a>3단계: 인증 구성

1. 샘플에서 `config.js` 파일을 엽니다.

2. 응용 프로그램을 등록하는 동안 이전에 얻은 응용 프로그램 자격 증명으로 샘플을 구성합니다. 값을 clientID, 호스트, tenantId 및 정책 이름의 이름으로 대체하여 다음 코드 줄을 변경합니다.

```JavaScript
const clientID = "<Application ID for your Node.js Web API - found on Properties page in Azure portal e.g. 93733604-cc77-4a3c-a604-87084dd55348>";
const b2cDomainHost = "<Domain of your B2C host eg. fabrikamb2c.b2clogin.com>";
const tenantId = "<your-tenant-ID>.onmicrosoft.com"; // Alternatively, you can use your Directory (tenant) ID (GUID)
const policyName = "<Name of your sign in / sign up policy, e.g. B2C_1_signupsignin1>";
```

자세한 내용은 이 [Node.js B2C 웹 API 샘플을](https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi)확인하십시오.

---

## <a name="javascript-spa"></a>JavaScript SPA

다음 단계는 **단일 페이지 응용 프로그램에서** Azure AD B2C를 사용하여 보호된 웹 API를 등록, 로그인 및 호출하는 방법을 보여 줍니다.

### <a name="step-1-register-your-application"></a>1단계: 애플리케이션 등록

인증을 구현하려면 먼저 애플리케이션을 등록해야 합니다. 자세한 단계는 [애플리케이션 등록](https://docs.microsoft.com/azure/active-directory-b2c/tutorial-register-applications)을 참조하세요.

### <a name="step-2-download-the-sample-application"></a>2단계: 샘플 응용 프로그램 다운로드

샘플을 zip 파일로 다운로드하거나 GitHub에서 복제합니다.

```console
git clone https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp.git
```

### <a name="step-3-configure-authentication"></a>3단계: 인증 구성

응용 프로그램을 구성하는 데는 두 가지 사항이 있습니다.

- API 엔드포인트 및 노출된 범위 구성
- 인증 매개 변수 및 토큰 범위 구성

1. 샘플에서 `apiConfig.js` 파일을 엽니다.

2. 웹 API를 등록하는 동안 이전에 얻은 매개 변수로 샘플을 구성합니다. 값을 웹 API 및 노출된 범위의 주소로 대체하여 다음 코드 줄을 변경합니다.

   ```javascript
    // The current application coordinates were pre-registered in a B2C tenant.
    const apiConfig = {
        b2cScopes: ["https://fabrikamb2c.onmicrosoft.com/helloapi/demo.read"], //API scopes you exposed during api registration
        webApi: "https://fabrikamb2chello.azurewebsites.net/hello" 
    };
   ```

3. 샘플에서 `authConfig.js` 파일을 엽니다.

4. 단일 페이지 응용 프로그램을 등록하는 동안 이전에 얻은 매개 변수로 샘플을 구성합니다. 값을 ClientId, 기관 메타데이터 및 토큰 요청 범위로 대체하여 다음 코드 줄을 변경합니다.

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

자세한 내용은 이 [JavaScript B2C 단일 페이지 응용 프로그램 샘플을](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp)확인하십시오.

---

## <a name="next-steps"></a>다음 단계

다음에 대해 자세히 알아봅니다.
- [사용자 흐름](https://docs.microsoft.com/azure/active-directory-b2c/tutorial-create-user-flows)
- [사용자 지정 정책](https://docs.microsoft.com/azure/active-directory-b2c/custom-policy-get-started)
- [UX 사용자 지정](https://docs.microsoft.com/azure/active-directory-b2c/custom-policy-configure-user-input)
