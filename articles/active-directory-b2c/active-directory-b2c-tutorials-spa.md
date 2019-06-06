---
title: 자습서 - 단일 페이지 애플리케이션에서 인증을 사용하도록 설정 - Azure Active Directory B2C | Microsoft Docs
description: Azure Active Directory B2C를 사용하여 단일 페이지 애플리케이션(JavaScript)에 대한 사용자 로그인을 제공하는 방법에 대한 자습서입니다.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.author: marsma
ms.date: 02/04/2019
ms.custom: mvc
ms.topic: tutorial
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: 6e4be3a14a6cfba6b32bea8a77975e3e34ea012d
ms.sourcegitcommit: adb6c981eba06f3b258b697251d7f87489a5da33
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/04/2019
ms.locfileid: "66507807"
---
# <a name="tutorial-enable-authentication-in-a-single-page-application-using-azure-active-directory-b2c"></a>자습서: Azure Active Directory B2C를 사용하여 단일 페이지 애플리케이션에서 인증을 사용하도록 설정

이 자습서에서는 Azure AD(Azure Active Directory) B2C를 사용하여 단일 페이지 애플리케이션(SPA)에서 사용자를 로그인시키고 등록하는 방법을 보여줍니다. Azure AD B2C를 사용하면 애플리케이션이 개방형 표준 프로토콜을 사용하여 소셜 계정, 엔터프라이즈 계정 및 Azure Active Directory 계정을 인증할 수 있습니다.

이 자습서에서는 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * Azure AD B2C에서 애플리케이션 업데이트
> * 애플리케이션을 사용하도록 샘플 구성
> * 사용자 흐름을 사용하여 등록

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>필수 조건

* [사용자 흐름을 생성](tutorial-create-user-flows.md)하여 애플리케이션에 사용자 환경을 사용하도록 설정합니다. 
* **ASP.NET 및 웹 개발** 워크로드가 있는 [Visual Studio 2019](https://www.visualstudio.com/downloads/)를 설치합니다.
* [.NET Core 2.0.0 SDK](https://www.microsoft.com/net/core) 이상 설치
* [Node.js](https://nodejs.org/en/download/)

## <a name="update-the-application"></a>애플리케이션 업데이트

필수 조건의 일부로 완료한 자습서에서 Azure AD B2C에 웹 애플리케이션을 추가했습니다. 이 자습서에서 샘플과의 통신을 사용하도록 설정하려면 Azure AD B2C의 애플리케이션에 리디렉트 URI를 추가해야 합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. Azure AD B2C 테넌트를 포함하는 디렉터리를 사용하려면 위쪽 메뉴에서 **디렉터리 및 구독 필터**를 클릭하고 테넌트가 포함된 디렉터리를 선택합니다.
3. Azure Portal의 왼쪽 상단 모서리에서 **모든 서비스**를 선택하고 **Azure AD B2C**를 검색하여 선택합니다.
4. **애플리케이션**을 선택한 후 *webapp1* 애플리케이션을 선택합니다.
5. **회신 URL** 아래에서 `http://localhost:6420`을 추가합니다.
6. **저장**을 선택합니다.
7. 속성 페이지에서 웹 애플리케이션을 구성할 때 사용할 애플리케이션 ID를 기록합니다.
8. **키**, **키 생성**을 차례로 선택하고 **저장**을 선택합니다. 웹 애플리케이션을 구성할 때 사용할 키를 기록합니다.

## <a name="configure-the-sample"></a>샘플 구성

이 자습서에서는 GitHub에서 다운로드할 수 있는 샘플을 구성합니다. 이 샘플은 단일 페이지 애플리케이션에서 등록, 로그인 및 보호되는 Web API 호출에 Azure AD B2C를 사용하는 방법을 보여줍니다.

GitHub에서 [zip 파일을 다운로드](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp/archive/master.zip)하거나 샘플을 복제합니다.

```
git clone https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp.git
```

설정을 변경하려면 다음을 수행합니다.

1. 샘플에서 `index.html` 파일을 엽니다.
2. 이전에 기록해 둔 애플리케이션 ID와 키를 사용하여 샘플을 구성합니다. 디렉터리 및 API의 이름으로 값을 바꿔서 코드의 다음 줄을 변경합니다.

    ```javascript
    // The current application coordinates were pre-registered in a B2C directory.
    var applicationConfig = {
        clientID: '<Application ID>',
        authority: "https://contoso.b2clogin.com/tfp/contoso.onmicrosoft.com/B2C_1_signupsignin1",
        b2cScopes: ["https://contoso.onmicrosoft.com/demoapi/demo.read"],
        webApi: 'https://contosohello.azurewebsites.net/hello',
    };
    ```

    이 자습서에서 사용된 사용자 흐름 이름은 **B2C_1_signupsignin1**입니다. 다른 사용자 흐름 이름을 사용하는 경우 `authority` 값에 해당 사용자 흐름 이름을 사용합니다.

## <a name="run-the-sample"></a>샘플 실행

1. Node.js 명령 프롬프트를 시작합니다.
2. Node.js 샘플이 포함된 디렉터리로 변경합니다. 예: `cd c:\active-directory-b2c-javascript-msal-singlepageapp`
3. 다음 명령을 실행합니다.

    ```
    npm install && npm update
    node server.js
    ```

    콘솔 창에는 앱이 호스트되는 포트 번호가 표시됩니다.
    
    ```
    Listening on port 6420...
    ```

4. 애플리케이션을 보려면 브라우저를 사용하여 `http://localhost:6420` 주소로 이동합니다.

이 샘플은 등록, 로그인, 프로필 편집 및 암호 재설정을 지원합니다. 이 자습서에서는 사용자가 이메일 주소를 사용하여 앱에 등록하는 방법을 중점적으로 설명합니다.

### <a name="sign-up-using-an-email-address"></a>전자 메일 주소를 사용하여 등록

1. **로그인**을 클릭하여 애플리케이션의 사용자로 등록합니다. 여기서는 이전 단계에서 정의한 **B2C_1_signupsignin1** 사용자 흐름을 사용합니다.
2. Azure AD B2C에서 등록 링크가 있는 로그인 페이지를 제공합니다. 아직 계정이 없으므로 **지금 등록** 링크를 클릭합니다. 
3. 등록 워크플로에서 이메일 주소를 사용하여 사용자의 ID를 수집하고 확인하는 페이지를 제공합니다. 또한 가입 워크플로에서도 사용자 흐름에 정의된 사용자의 암호와 요청된 특성을 수집합니다.

    유효한 이메일 주소를 사용하고 확인 코드를 사용하여 유효성을 검사합니다. 암호를 설정합니다. 요청된 특성에 대한 값을 입력합니다. 

    ![등록 워크플로](media/active-directory-b2c-tutorials-desktop-app/sign-up-workflow.png)

4. **만들기**를 클릭하여 로컬 계정을 Azure AD B2C 디렉터리에 만듭니다.

이제 사용자는 이메일 주소를 사용하여 로그인하고 SPA 앱을 사용할 수 있습니다.

> [!NOTE]
> 로그인하면 앱에 "권한 부족" 오류가 표시됩니다. 데모 디렉터리에서 리소스에 액세스하려고 시도하기 때문에 이 오류가 표시되는 것입니다. 액세스 토큰이 Azure AD 디렉터리에만 유효하므로 API 호출에 권한이 부여되지 않습니다. 그 다음 자습서를 계속 진행하여 디렉터리의 보호되는 웹 API 키를 만드세요.

## <a name="next-steps"></a>다음 단계

이 문서에서는 다음 방법에 대해 알아보았습니다.

> [!div class="checklist"]
> * Azure AD B2C에서 애플리케이션 업데이트
> * 애플리케이션을 사용하도록 샘플 구성
> * 사용자 흐름을 사용하여 등록

> [!div class="nextstepaction"]
> [자습서: Azure Active Directory B2C를 사용하여 단일 페이지 앱에서 ASP.NET Core Web API로의 액세스 권한 부여](active-directory-b2c-tutorials-spa-webapi.md)
