---
title: Microsoft 인증 라이브러리를 사용하여 애플리케이션이 Azure AD B2C와 상호 운용될 수 있는 방법 알아보기
description: MSAL(Microsoft 인증 라이브러리)을 통해 애플리케이션은 Azure AD B2C와 상호 운용되고 보안 웹 API를 호출하기 위한 토큰을 획득할 수 있습니다. 이러한 웹 API에는 Microsoft Graph, 기타 Microsoft API, 타사 웹 API 또는 사용자의 웹 API가 있습니다.
services: active-directory
documentationcenter: dev-center-name
author: negoe
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/16/2019
ms.author: negoe
ms.reviewer: nacanuma
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0eea0fd03b1df49e912a867b0c667ff0fd28c08a
ms.sourcegitcommit: cd70273f0845cd39b435bd5978ca0df4ac4d7b2c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/18/2019
ms.locfileid: "71097612"
---
# <a name="use-microsoft-authentication-library-to-interoperate-with-azure-active-directory-b2c"></a>MSAL(Microsoft 인증 라이브러리)를 사용하여 Azure Active Directory B2C와 상호 운용

MSAL(Microsoft 인증 라이브러리)을 사용하면 애플리케이션 개발자가 [Azure AD B2C(Azure Active Directory B2C)](https://docs.microsoft.com/azure/active-directory-b2c/)를 사용하여 소셜 및 로컬 ID로 사용자를 인증할 수 있습니다. Azure AD B2C는 ID 관리 서비스입니다. 이를 사용하여 고객이 애플리케이션을 사용할 때 자신의 프로필을 등록하고 로그인하고 관리하는 방법을 사용자 지정하고 제어할 수 있습니다.

Azure AD B2C를 사용하면 UI를 브랜드를 반영하고 사용자 지정하여 고객에게 원활한 환경을 제공할 수 있습니다.

이 자습서에서는 MSAL을 사용하여 Azure AD B2C와 상호 운용되는 방법을 보여 줍니다.

## <a name="prerequisites"></a>필수 구성 요소

아직 고유한 [Azure AD B2C 테넌트](https://docs.microsoft.com/azure/active-directory-b2c/tutorial-create-tenant)를 만들지 않았다면 지금 만듭니다. 기존 Azure AD B2C 테넌트를 사용해도 됩니다.

## <a name="javascript"></a>JavaScript

다음 단계는 단일 페이지 애플리케이션에서 Azure AD B2C를 사용하여 사용자 등록과 로그인을 처리하고 보호되는 웹 API를 호출하는 방법을 보여줍니다.

### <a name="step-1-register-your-application"></a>1단계: 애플리케이션 등록

인증을 구현하려면 먼저 애플리케이션을 등록해야 합니다. 자세한 단계는 [애플리케이션 등록](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp#step-4-register-your-own-web-application-with-azure-ad-b2c)을 참조하세요.

### <a name="step-2-download-the-sample-application"></a>2단계: 샘플 애플리케이션 다운로드

샘플을 zip 파일로 다운로드하거나 GitHub에서 복제합니다.

```
git clone https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp.git
```

### <a name="step-3-configure-authentication"></a>3단계: 인증 구성

1. 샘플에서 **index.html** 파일을 엽니다.

1. 응용 프로그램을 등록 하는 동안 이전에 기록한 클라이언트 ID 및 키를 사용 하 여 샘플을 구성 합니다. 디렉터리 및 API의 이름으로 값을 바꿔서 코드의 다음 줄을 변경합니다.

   ```javascript
   // The current application coordinates were pre-registered in a B2C tenant.

    var appConfig = {
        b2cScopes: ["https://fabrikamb2c.onmicrosoft.com/helloapi/demo.read"],
        webApi: "https://fabrikamb2chello.azurewebsites.net/hello"
    };

    const msalConfig = {
        auth: {
            clientId: "e760cab2-b9a1-4c0d-86fb-ff7084abd902" //This is your client/application ID
            authority: "https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/b2c_1_susi", //This is your tenant info
            validateAuthority: false
        },
        cache: {
            cacheLocation: "localStorage",
            storeAuthStateInCookie: true
        }
    };
    // create UserAgentApplication instance
    const myMSALObj = new Msal.UserAgentApplication(msalConfig);

   ```

이 자습서의 [사용자 흐름](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-reference-policies) 이름은 **B2C_1_signupsignin1**입니다. 다른 사용자 흐름 이름을 사용하는 경우 **authority** 값을 해당 이름으로 설정합니다.

### <a name="step-4-configure-your-application-to-use-b2clogincom"></a>4단계: `b2clogin.com`을 사용하도록 애플리케이션 구성

`login.microsoftonline.com` 대신 `b2clogin.com`을 리디렉션 URL로 사용할 수 있습니다. 등록 및 로그인을 위해 ID 공급자를 설정하는 경우 Azure AD B2C 애플리케이션에서 이 작업을 수행합니다.

`https://your-tenant-name.b2clogin.com/your-tenant-guid`의 컨텍스트에서 `b2clogin.com`을 사용하면 다음과 같은 결과가 발생합니다.

- Microsoft 서비스는 쿠키 헤더의 공간을 적게 사용합니다.
- URL에 더 이상 Microsoft에 대한 참조가 포함되지 않습니다. 예를 들어 Azure AD B2C 애플리케이션이 `login.microsoftonline.com`을 참조할 수 있습니다.

 `b2clogin.com`을 사용하려면 애플리케이션의 구성을 업데이트해야 합니다.  

- **validateAuthority** 속성을 `false`로 설정하여 `b2clogin.com`을 통해 리디렉션이 발생할 수 있도록 합니다.

다음 예제에서는 이 속성을 설정하는 방법을 보여 줍니다.

```javascript
// The current application coordinates were pre-registered in a B2C directory.

const msalConfig = {
    auth:{
        clientId: "Enter_the_Application_Id_here",
        authority: "https://contoso.b2clogin.com/tfp/contoso.onmicrosoft.com/B2C_1_signupsignin1",
        b2cScopes: ["https://contoso.onmicrosoft.com/demoapi/demo.read"],
        webApi: 'https://contosohello.azurewebsites.net/hello',
        validateAuthority: false;

};
// create UserAgentApplication instance
const myMSALObj = new UserAgentApplication(msalConfig);
```

> [!NOTE]
> Azure AD B2C 애플리케이션은 사용자 흐름 참조 및 토큰 엔드포인트 등의 여러 위치에서 `login.microsoftonline.com`을 참조할 수 있습니다. 권한 부여 엔드포인트, 토큰 엔드포인트 및 발급자가 `your-tenant-name.b2clogin.com`을 사용하도록 업데이트되었는지 확인합니다.

JavaScript용 MSAL Preview(MSAL.js)를 사용하는 방법을 보려면 [이 MSAL JavaScript 샘플](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp#single-page-application-built-on-msaljs-with-azure-ad-b2c)을 따라 작업해보세요. 샘플은 액세스 토큰을 가져오고 Azure AD B2C로 보호되는 API를 호출합니다.

## <a name="next-steps"></a>다음 단계

다음에 대해 자세히 알아봅니다.

- [사용자 지정 정책](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-overview-custom)
- [사용자 인터페이스 사용자 지정](https://docs.microsoft.com/azure/active-directory-b2c/customize-ui-overview)