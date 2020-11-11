---
title: '빠른 시작: MSAL 노드를 사용하여 노드 웹앱에 인증 추가 | Azure'
titleSuffix: Microsoft identity platform
description: 이 빠른 시작에서는 Node.js 웹앱 및 Node.js용 MSAL(Microsoft 인증 라이브러리)을 사용하여 인증을 구현하는 방법을 알아봅니다.
services: active-directory
author: amikuma
manager: saeeda
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 10/22/2020
ms.author: amikuma
ms.custom: aaddev, scenarios:getting-started, languages:js, devx-track-js
ms.openlocfilehash: e223b5ae072a323ad56ed396c06580fea9b8b7ab
ms.sourcegitcommit: 2a8a53e5438596f99537f7279619258e9ecb357a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/06/2020
ms.locfileid: "94335250"
---
# <a name="quickstart-sign-in-users-and-get-an-access-token-in-a-node-web-app-using-the-auth-code-flow"></a>빠른 시작: 인증 코드 흐름을 사용하여 사용자를 로그인하고 노드 웹앱에서 액세스 토큰을 가져옵니다.

이 빠른 시작에서는 인증 코드 흐름을 사용하여 Node.js 웹앱에서 개인 계정, 회사 및 학교 계정에 로그인하는 방법을 설명하는 코드 샘플을 실행합니다. 또한 이 코드 샘플은 웹 API(이 경우에는 Microsoft Graph API)를 호출하기 위해 액세스 토큰을 가져오는 방법을 보여줍니다. 자세한 내용은 [샘플 작동 방식](#how-the-sample-works)을 참조하세요.

이 빠른 시작에서는 인증 코드 흐름과 함께 Node.js용 Microsoft 인증 라이브러리(MSAL 노드)를 사용합니다.

> [!IMPORTANT]
> MSAL 노드 [!INCLUDE [PREVIEW BOILERPLATE](../../../includes/active-directory-develop-preview.md)]

## <a name="prerequisites"></a>사전 요구 사항

* Azure 구독 - [체험용 Azure 구독 만들기](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* [Node.JS](https://nodejs.org/en/download/)
* [Visual Studio Code](https://code.visualstudio.com/download) 또는 다른 코드 편집기

> [!div renderon="docs"]
> ## <a name="register-and-download-your-quickstart-application"></a>빠른 시작 애플리케이션 등록 및 다운로드
>
> #### <a name="step-1-register-your-application"></a>1단계: 애플리케이션 등록
>
> 1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
> 1. 계정이 둘 이상의 테넌트에 대해 액세스를 제공하는 경우 오른쪽 위에 있는 계정을 선택한 다음, 사용할 Azure AD 테넌트로 포털 세션을 설정합니다.
> 1. [앱 등록](https://go.microsoft.com/fwlink/?linkid=2083908)을 선택합니다.
> 1. **새 등록** 을 선택합니다.
> 1. **애플리케이션 등록** 페이지가 나타나면 애플리케이션의 이름을 입력합니다.
> 1. **지원되는 계정 유형** 아래에서 **모든 조직 디렉터리의 계정 및 개인 Microsoft 계정** 을 선택합니다.
> 1. **리디렉션 URI** 값을 `http://localhost:3000/redirect`으로 설정합니다.
> 1. **등록** 을 선택합니다. 
> 1. 나중에 사용할 수 있도록 앱 **개요** 페이지에서 **애플리케이션(클라이언트) ID** 값을 기록해 둡니다.
> 1. **인증서 및 비밀** 에서 **새 클라이언트 암호** 를 선택합니다.  설명을 비워 두고 기본 만료를 그대로 둔 다음, **추가** 를 클릭합니다.
> 1. 나중에 사용하기 위해 **클라이언트 암호** 의 **값** 을 확인합니다.

#### <a name="step-2-download-the-project"></a>2단계: 프로젝트를 다운로드합니다.

> [!div renderon="docs"]
> Node.js를 사용하여 웹 서버로 프로젝트를 실행하려면 [주요 프로젝트 파일을 다운로드](https://github.com/Azure-Samples/ms-identity-node/archive/main.zip)합니다.

> [!div renderon="portal" class="sxs-lookup"]
> Node.js를 사용하여 웹 서버에서 프로젝트 실행

> [!div renderon="portal" class="sxs-lookup" id="autoupdate" class="nextstepaction"]
> [코드 샘플 다운로드](https://github.com/Azure-Samples/ms-identity-node/archive/main.zip)

> [!div renderon="docs"]
> #### <a name="step-3-configure-your-node-app"></a>3단계: 노드 앱 구성
>
> 프로젝트를 추출하고 *ms-identity-node-main* 폴더를 연 다음, *index.js* 파일을 엽니다.
> **를 애플리케이션(클라이언트) ID** 로 `clientID`를 설정합니다.
> **클라이언트 암호** 의 **값** 으로 `clientSecret`를 설정합니다.
>
>```javascript
>const config = {
>    auth: {
>        clientId: "Enter_the_Application_Id_Here",
>        authority: "https://login.microsoftonline.com/common",
>        clientSecret: "Enter_the_Client_Secret_Here"
>    },
>    system: {
>        loggerOptions: {
>            loggerCallback(loglevel, message, containsPii) {
>                console.log(message);
>            },
>            piiLoggingEnabled: false,
>            logLevel: msal.LogLevel.Verbose,
>        }
>    }
>};
> ```

> [!div renderon="docs"]
>
> 여기에 설명한 대로 `config` 섹션에서 값을 수정합니다.
>
> - `Enter_the_Application_Id_Here`는 등록한 애플리케이션의 **애플리케이션(클라이언트) ID** 입니다.
> - `Enter_the_Client_Secret_Here`는 등록한 애플리케이션의 **클라이언트 암호** 의 **값** 입니다.
>
> 기본 `authority` 값은 기본(글로벌) Azure 클라우드를 나타냅니다.
>
> ```javascript
> authority: "https://login.microsoftonline.com/common",
> ```
>
> > [!TIP]
> > **애플리케이션(클라이언트) ID** 값을 찾으려면 Azure Portal에서 앱 등록의 **개요** 페이지로 이동합니다. **인증서 및 비밀** 로 이동하여 새 **클라이언트 암호** 를 검색하거나 생성합니다.
>
> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-3-your-app-is-configured-and-ready-to-run"></a>3단계: 앱이 구성되었고 실행할 준비가 되었습니다.
>
> [!div renderon="docs"]
>
> #### <a name="step-4-run-the-project"></a>4단계: 프로젝트 실행

Node.js를 사용하여 프로젝트를 실행합니다.

1. 서버를 시작하려면 프로젝트의 디렉터리 내에서 다음 명령을 실행합니다.
    ```console
    npm install
    npm start
    ```
1. [https://www.microsoft.com]\(`http://localhost:3000/`) 로 이동합니다.

1. **로그인** 을 선택하여 로그인 프로세스를 시작합니다.

    사용자가 처음으로 로그인하면 애플리케이션이 사용자 프로필에 액세스하고 로그인할 수 있도록 동의하라는 메시지가 표시됩니다. 성공적으로 로그인되면 명령줄에 로그 메시지가 표시됩니다.

## <a name="more-information"></a>자세한 정보

### <a name="how-the-sample-works"></a>샘플 작동 방법

이 샘플은 실행될 때 localhost, 포트 3000에서 웹 서버를 호스팅합니다. 웹 브라우저가 이 사이트에 액세스하면 샘플은 즉시 사용자를 Microsoft 인증 페이지로 리디렉션합니다. 이 때문에 샘플에는 *html* 또는 표시 요소가 포함되지 않습니다. 인증에 성공하면 "OK" 메시지가 표시됩니다.

### <a name="msal-node"></a>MSAL 노드

MSAL 노드 라이브러리는 사용자를 로그인하고 Microsoft ID 플랫폼으로 보호되는 API 액세스에 사용되는 토큰을 요청합니다. Node.js 패키지 관리자(npm)를 사용하여 최신 버전을 다운로드할 수 있습니다.

```console
npm install @azure/msal-node
```

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [기존 웹앱에 인증 추가 - GitHub 코드 샘플 >](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/samples/msal-node-samples/standalone-samples/auth-code)
