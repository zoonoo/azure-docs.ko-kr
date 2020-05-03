---
title: JavaScript 단일 페이지 앱에서 인증 코드를 사용하여 사용자 로그인 | Azure
titleSuffix: Microsoft identity platform
description: JavaScript 앱이 Microsoft ID 플랫폼을 사용하여 액세스 토큰이 필요한 API를 호출하는 방법을 알아봅니다.
services: active-directory
author: hahamil
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 04/22/2020
ms.author: hahamil
ms.custom: aaddev, identityplatformtop40, scenarios:getting-started, languages:JavaScript
ROBOTS: NOINDEX
ms.openlocfilehash: d362db3a51848603c78d663c5b628192ff028d02
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/29/2020
ms.locfileid: "82209616"
---
# <a name="quickstart-sign-in-users-and-get-an-access-token-in-a-javascript-spa-using-the-auth-code-flow"></a>빠른 시작: 인증 코드 흐름을 사용하여 사용자를 로그인하고 JavaScript SPA에서 액세스 토큰을 가져옵니다. 

> [!IMPORTANT]
> 이 기능은 현재 미리 보기로 제공됩니다. [부속 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)에 동의하면 미리 보기를 사용할 수 있습니다. 이 기능의 몇 가지 측면은 GA(일반 공급) 전에 변경될 수 있습니다.


이 빠른 시작에서는 인증 코드 흐름과 함께 MSAL.js 2.0를 사용합니다. 암시적 흐름과 함께 MSAL.js 1.0을 사용하려면 [이 빠른 시작](https://docs.microsoft.com/azure/active-directory/develop/quickstart-v2-javascript)을 참조하세요.

이 빠른 시작에서는 코드 샘플을 사용하여 JavaScript SPA(단일 페이지 애플리케이션)에서 개인 계정, 회사 및 학교 계정의 사용자에 로그인하는 방법을 알아봅니다. 또한 JavaScript SPA는 Microsoft Graph API 또는 웹 API를 호출하는 액세스 토큰을 가져올 수 있습니다. 자세한 내용은 [샘플 작동 방식](#how-the-sample-works)을 참조하세요.

## <a name="prerequisites"></a>사전 요구 사항

* Azure 구독 - [체험용 Azure 구독 만들기](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* [Node.JS](https://nodejs.org/en/download/)
* [Visual Studio Code](https://code.visualstudio.com/download)(프로젝트 파일 편집)


> [!div renderon="docs"]
> ## <a name="register-and-download-your-quickstart-application"></a>빠른 시작 애플리케이션 등록 및 다운로드
> 빠른 시작 애플리케이션을 시작하려면 다음 옵션 중 하나를 사용합니다.
>
> ### <a name="option-1-express-register-and-auto-configure-your-app-and-then-download-your-code-sample"></a>옵션 1(Express): 앱을 등록하고 자동 구성한 다음, 코드 샘플 다운로드
>
> 1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
> 1. 계정이 둘 이상의 테넌트에 대해 액세스를 제공하는 경우 오른쪽 위에 있는 계정을 선택한 다음, 사용할 Azure AD(Azure Active Directory) 테넌트로 포털 세션을 설정합니다.
> 1. [앱 등록](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade/quickStartType/JavascriptSpaQuickstartPage/sourceType/docs)을 선택합니다.
> 1. 애플리케이션의 이름을 입력합니다.
> 1. **지원되는 계정 유형** 아래에서 **모든 조직 디렉터리의 계정 및 개인 Microsoft 계정**을 선택합니다.
> 1. **등록**을 선택합니다.
> 1. 빠른 시작 창으로 이동하여 지침에 따라 새 애플리케이션을 다운로드하고 자동으로 구성합니다.
>
> ### <a name="option-2-manual-register-and-manually-configure-your-application-and-code-sample"></a>옵션 2(수동): 애플리케이션 및 코드 샘플을 등록하고 수동으로 구성
>
> #### <a name="step-1-register-your-application"></a>1단계: 애플리케이션 등록
>
> 1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
>
> 1. 계정이 둘 이상의 테넌트에 대해 액세스를 제공하는 경우 오른쪽 위에 있는 계정을 선택한 다음, 사용할 Azure AD 테넌트로 포털 세션을 설정합니다.
> 1. [앱 등록](https://go.microsoft.com/fwlink/?linkid=2083908)을 선택합니다.
> 1. **새 등록**을 선택합니다.
> 1. **애플리케이션 등록** 페이지가 나타나면 애플리케이션의 이름을 입력합니다.
> 1. **지원되는 계정 유형** 아래에서 **모든 조직 디렉터리의 계정 및 개인 Microsoft 계정**을 선택합니다.
> 1. **등록**을 선택합니다. 나중에 사용할 수 있도록 앱 **개요** 페이지에서 **애플리케이션(클라이언트) ID** 값을 기록해 둡니다.
> 1. 등록된 애플리케이션의 왼쪽 창에서 **인증**을 선택합니다.
> 1. **플랫폼 구성**에서 **플랫폼 추가**를 선택합니다. 왼쪽에 패널이 열립니다. 여기에서 **단일 페이지 애플리케이션** 영역을 선택합니다.
> 1. 왼쪽에서 **리디렉션 URI** 값을 `http://localhost:3000/`으로 설정합니다. 
> 1. **구성**을 선택합니다.

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-1-configure-your-application-in-the-azure-portal"></a>1단계: Azure Portal에서 애플리케이션 구성
> 이 빠른 시작의 코드 샘플이 작동하도록 하려면 `redirectUri`를 `http://localhost:3000/`으로 추가해야 합니다.
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [이러한 변경 내용 적용]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![이미 구성됨](media/quickstart-v2-javascript/green-check.png) 이러한 특성을 사용하여 애플리케이션을 구성합니다.

#### <a name="step-2-download-the-project"></a>2단계: 프로젝트를 다운로드합니다.

> [!div renderon="docs"]
> Node.js를 사용하여 웹 서버로 프로젝트를 실행하려면 [주요 프로젝트 파일을 다운로드](https://github.com/Azure-Samples/ms-identity-javascript-v2/archive/quickstart.zip)합니다.

> [!div renderon="portal" class="sxs-lookup"]
> Node.js를 사용하여 웹 서버에서 프로젝트 실행

> [!div renderon="portal" id="autoupdate" class="nextstepaction" class="sxs-lookup"]
> [코드 샘플 다운로드](https://github.com/Azure-Samples/ms-identity-javascript-v2/archive/quickstart.zip)

> [!div renderon="docs"]
> #### <a name="step-3-configure-your-javascript-app"></a>3단계: JavaScript 앱 구성
>
> *app* 폴더에서 *authConfig.js*를 편집하고 `msalConfig` 아래에서 `clientID`, `authority` 및 `redirectUri` 값을 설정합니다.
>
> ```javascript
>
>  // Config object to be passed to Msal on creation
>  const msalConfig = {
>    auth: {
>      clientId: "Enter_the_Application_Id_Here",
>      authority: "Enter_the_Cloud_Instance_Id_HereEnter_the_Tenant_Info_Here",
>      redirectUri: "Enter_the_Redirect_Uri_Here",
>    },
>    cache: {
>      cacheLocation: "sessionStorage", // This configures where your cache will be stored
>      storeAuthStateInCookie: false, // Set this to "true" if you are having issues on IE11 or Edge
>    }
>  };
>
>```

> [!div renderon="portal" class="sxs-lookup"]
> > [!NOTE]
> > `Enter_the_Supported_Account_Info_Here`

> [!div renderon="docs"]
>
> 위치:
> - *\<Enter_the_Application_Id_Here>* 는 등록한 애플리케이션에 대한 **애플리케이션(클라이언트) ID**입니다.
> - *\<Enter_the_Cloud_Instance_Id_Here>* 는 Azure 클라우드의 인스턴스입니다. 주 또는 글로벌 Azure 클라우드의 경우 *https://login.microsoftonline.com/* 을 입력하면 됩니다. **국가별** 클라우드(예제: 중국)의 경우 [국가별 클라우드](https://docs.microsoft.com/azure/active-directory/develop/authentication-national-cloud)를 참조하세요.
> - *\<Enter_the_Tenant_info_here>* 는 다음 옵션 중 하나로 설정됩니다.
>    - 애플리케이션이 *이 조직 디렉터리의 계정*을 지원하는 경우 이 값을 **테넌트 ID** 또는 **테넌트 이름**(예: *contoso.microsoft.com*)으로 바꿉니다.
>    - 애플리케이션이 *모든 조직 디렉터리의 계정*을 지원하는 경우 이 값을 **organizations**으로 바꿉니다.
>    - 애플리케이션에서 *모든 조직 디렉터리의 계정 및 개인 Microsoft 계정*을 지원하는 경우 이 값을 **common**으로 바꿉니다. *개인 Microsoft 계정만* 지원하도록 제한하려면 이 값을 **consumers**로 바꿉니다.
> - *\<Enter_the_Redirect_Uri_Here>* 는 `http://localhost:3000`입니다.
> > [!TIP]
> > **애플리케이션(클라이언트) ID**, **디렉터리(테넌트) ID** 및 **지원되는 계정 유형**의 값을 찾아보려면 Azure Portal에서 앱 등록의 **개요** 페이지로 이동합니다.
>
> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-3-your-app-is-configured-and-ready-to-run"></a>3단계: 앱이 구성되었고 실행할 준비가 되었습니다.
> 앱 속성 값을 사용하여 프로젝트를 구성했습니다.

> [!div renderon="docs"]
>
> 그런 다음, 동일한 폴더에 있는 *graphConfig.js* 파일을 편집하여 `apiConfig` 개체에 대한 `graphMeEndpoint` 및 `graphMailEndpoint`를 설정합니다.
> ```javascript
>   // Add here the endpoints for MS Graph API services you would like to use.
>   const graphConfig = {
>     graphMeEndpoint: "Enter_the_Graph_Endpoint_Herev1.0/me",
>     graphMailEndpoint: "Enter_the_Graph_Endpoint_Herev1.0/me/messages"
>   };
>
>   // Add here scopes for access token to be used at MS Graph API endpoints.
>   const tokenRequest = {
>       scopes: ["Mail.Read"]
>   };
> ```
>

> [!div renderon="docs"]
>
> *\<Enter_the_Graph_Endpoint_Here>* 는 API 호출이 수행될 엔드포인트입니다. 주 또는 글로벌 Microsoft Graph API 서비스의 경우 `https://graph.microsoft.com`을 입력합니다. 자세한 내용은 [국가별 클라우드 배포](https://docs.microsoft.com/graph/deployments)를 참조하세요.
>
> #### <a name="step-4-run-the-project"></a>4단계: 프로젝트 실행

[Node.js](https://nodejs.org/en/download/)를 사용하여 웹 서버에서 프로젝트 실행:

1. 서버를 시작하려면 프로젝트의 디렉터리 내에서 다음 명령을 실행합니다.
    ```bash
    npm install
    npm start
    ```
1. [https://www.microsoft.com]\(`http://localhost:3000/`) 로 이동합니다.

1. **로그인**을 선택하여 로그인 프로세스를 시작한 다음, Microsoft Graph API를 호출합니다.

    사용자가 처음으로 로그인하면 애플리케이션이 사용자 프로필에 액세스하고 로그인할 수 있도록 동의하라는 메시지가 표시됩니다. 성공적으로 로그인한 후에는 사용자 프로필 정보가 페이지에 표시되어야 합니다.

## <a name="more-information"></a>자세한 정보

### <a name="how-the-sample-works"></a>샘플 작동 방법

![샘플 JavaScript SPA 작동 방법: 1. SPA가 로그인을 시작합니다. 2. SPA는 Microsoft ID 플랫폼에서 ID 토큰을 획득합니다. 3. SPA는 획득 토큰을 호출합니다. 4. Microsoft ID 플랫폼은 SPA에 액세스 토큰을 반환합니다. 5. SPA는 Microsoft Graph API에 대한 액세스 토큰을 사용하여 HTTP GET 요청을 수행합니다. 6. Graph API는 SPA에 HTTP 응답을 반환합니다.](media/quickstart-v2-javascript/javascriptspa-intro.svg)

### <a name="msaljs"></a>msal.js

MSAL.js 라이브러리는 사용자를 로그인하고 Microsoft ID 플랫폼으로 보호되는 API 액세스에 사용되는 토큰을 요청합니다. 샘플의 *index.html* 파일에는 라이브러리에 대한 참조가 포함되어 있습니다.

```html
<script type="text/javascript" src="https://alcdn.msftauth.net/lib/1.2.1/js/msal.js" integrity="sha384-9TV1245fz+BaI+VvCjMYL0YDMElLBwNS84v3mY57pXNOt6xcUYch2QLImaTahcOP" crossorigin="anonymous"></script>
```
> [!TIP]
> 이전 버전을 [MSAL.js 릴리스](https://github.com/AzureAD/microsoft-authentication-library-for-js/releases)에서 최근에 릴리스된 버전으로 바꿀 수 있습니다.

또는 Node.js가 설치된 경우 Node.js 패키지 관리자(npm)를 사용하여 최신 버전을 다운로드할 수 있습니다.

```batch
npm install msal
```

## <a name="next-steps"></a>다음 단계

[MSAL.js GitHub 리포지토리](https://github.com/AzureAD/microsoft-authentication-library-for-js)에는 추가 라이브러리 설명서, FAQ가 포함되어 있으며, 문제 지원을 제공합니다.

이 빠른 시작에 대한 애플리케이션 빌드에 대한 더 자세한 단계별 가이드는 다음을 참조하세요.

> [!div class="nextstepaction"]
> [MS Graph에 로그인하고 호출하는 자습서](https://docs.microsoft.com/azure/active-directory/develop/tutorial-v2-javascript-auth-code)
