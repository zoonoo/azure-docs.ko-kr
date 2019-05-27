---
title: MSAL(Microsoft 인증 라이브러리)를 사용하여 Azure AD B2C와 통합하는 방법 알아보기
description: MSAL(Microsoft 인증 라이브러리)을 통해 애플리케이션 개발자는 Azure AD B2C와 통합하고 보안 웹 API를 호출하기 위한 토큰을 획득할 수 있습니다. 이러한 웹 API에는 Microsoft Graph, 기타 Microsoft API, 타사 웹 API 또는 사용자의 웹 API가 있습니다.
services: active-directory
documentationcenter: dev-center-name
author: negoe
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/04/2019
ms.author: negoe
ms.reviewer: nacanuma
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6af78a593fb8fadb836d2dc4c02115d95a7f2712
ms.sourcegitcommit: f6c85922b9e70bb83879e52c2aec6307c99a0cac
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/11/2019
ms.locfileid: "65546027"
---
# <a name="integrate-microsoft-authentication-library-msal-with-azure-active-directory-b2c"></a>Azure Active Directory B2C와 MSAL(Microsoft 인증 라이브러리) 통합

MSAL(Microsoft 인증 라이브러리)을 사용하면 애플리케이션 개발자가 [Azure AD(Azure Active Directory) B2C](https://docs.microsoft.com/azure/active-directory-b2c/)를 사용하여 소셜 및 로컬 ID로 사용자를 인증할 수 있습니다. Azure AD(Azure Active Directory) B2C는 애플리케이션을 사용할 때 고객이 자신의 프로필을 등록, 로그인 및 관리하는 방법을 사용자 지정하고 제어할 수 있는 ID 관리 서비스입니다.

Azure AD(Azure Active Directory) B2C를 사용하면 UI(사용자 인터페이스)를 브랜드를 반영하고 사용자 지정하여 고객에게 원활한 환경을 제공할 수 있습니다.

이 자습서에서는 MSAL(Microsoft 인증 라이브러리)을 사용하여 Azure AD(Azure Active Directory) B2C와 통합하는 방법을 보여줍니다.


## <a name="prerequisites"></a>필수 조건

아직 고유한 [Azure AD B2C 테넌트](https://docs.microsoft.com/azure/active-directory-b2c/tutorial-create-tenant)를 만들지 않았다면 지금 만듭니다. 기존 Azure AD B2C 테넌트를 사용해도 됩니다. 

## <a name="javascript"></a>JavaScript

다음 샘플은 단일 페이지 애플리케이션에서 Azure AD B2C를 사용하여 사용자 등록과 로그인을 처리하고 보호되는 Web API 호출하는 방법을 보여줍니다.

### <a name="step-1-register-your-application"></a>1단계: 애플리케이션 등록

인증을 구현하려면 먼저 애플리케이션을 등록해야 합니다. 앱을 등록하려면 [애플리케이션 등록](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp#step-4-register-your-own-web-application-with-azure-ad-b2c)에서 자세한 단계를 수행하세요.

### <a name="steps-2-download-applications"></a>2단계: 애플리케이션 다운로드

GitHub에서 zip 파일을 다운로드하거나 샘플을 복제합니다.
>git clone https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp.git

### <a name="steps-3-authentication"></a>3단계: 인증

1. 샘플에서 index.html 파일을 엽니다.

2. 애플리케이션을 등록하는 동안 이전에 기록해 둔 애플리케이션 ID와 키를 사용하여 샘플을 구성합니다. 디렉터리 및 API의 이름으로 값을 바꿔서 코드의 다음 줄을 변경합니다.

```javascript
// The current application coordinates were pre-registered in a B2C directory.

const msalConfig = {
    auth:{
        clientId: "Enter_the_Application_Id_here",
        authority: "https://login.microsoftonline.com/tfp/<your-tenant-name>.onmicrosoft.com/<your-sign-in-sign-up-policy>",
        b2cScopes: ["https://<your-tenant-name>.onmicrosoft.com/hello/demo.read"],
        webApi: 'http://localhost:5000/hello',
  };

// create UserAgentApplication instance
const myMSALObj = new UserAgentApplication(msalConfig);
```

이 자습서에 사용된 [사용자 흐름](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-reference-policies) 이름은 B2C_1_signupsignin1입니다. 다른 사용자 흐름 이름을 사용하는 경우 해당 사용자 흐름 이름을 authority 값에 사용합니다.


### <a name="configure-application-to-use-b2clogincom"></a>`b2clogin.com`을 사용하도록 애플리케이션 구성

Azure AD(Azure Active Directory) B2C 애플리케이션에서 등록 및 로그인을 위한 ID 공급자를 설정할 때 `login.microsoftonline.com` 대신 `b2clogin.com`을 리디렉션 URL로 사용할 수 있습니다.

**`b2clogin.com`** 즉, 
`https://your-tenant-name.b2clogin.com/your-tenant-guid`가 다음과 같은 이유로 사용됩니다.

- 쿠키 헤더에서 Microsoft 서비스가 사용하는 공간이 줄어듭니다.
- URL에 더 이상 Microsoft에 대한 참조가 포함되지 않습니다. 예를 들어 Azure AD B2C 애플리케이션이 login.microsoftonline.com을 참조할 수 있습니다.


 'b2clogin.com'을 사용하려면 애플리케이션의 구성을 업데이트해야 합니다.  

1. ValidateAuthority 업데이트: **validateAuthority** 속성을 `false`로 설정합니다. **validateAuthority**를 false로 설정하면 리디렉션이 b2clogin.com에 허용됩니다.

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
> Azure AD B2C 애플리케이션은 사용자 흐름 참조 및 토큰 엔드포인트와 같은 여러 위치에서 login.microsoftonline.com을 참조할 수 있습니다. 권한 부여 엔드포인트, 토큰 엔드포인트 및 발급자가 your-tenant-name.b2clogin.com을 사용하도록 업데이트되었는지 확인합니다.

JavaScript(msal.js)용 Microsoft 인증 라이브러리 미리 보기 사용 방법에 대한 [MSAL JS 샘플](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp#single-page-application-built-on-msaljs-with-azure-ad-b2c)을 수행하면 액세스 토큰을 받고 Azure AD B2C에서 보호되는 API를 호출할 수 있습니다.

## <a name="next-steps"></a>다음 단계

다음에 대해 자세히 알아봅니다.

- [사용자 지정 정책](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-overview-custom)
- [사용자 인터페이스 사용자 지정](https://docs.microsoft.com/azure/active-directory-b2c/customize-ui-overview)