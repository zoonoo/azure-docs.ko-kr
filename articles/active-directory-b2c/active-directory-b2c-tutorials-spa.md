---
title: 자습서 - 단일 페이지 애플리케이션에서 인증을 사용하도록 설정 - Azure Active Directory B2C
description: Azure Active Directory B2C를 사용하여 단일 페이지 애플리케이션(JavaScript)에 대한 사용자 로그인을 제공하는 방법을 알아봅니다.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.author: marsma
ms.date: 07/24/2019
ms.custom: mvc
ms.topic: tutorial
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: 8683169e0f8464cdcf52600968f67f3622e61445
ms.sourcegitcommit: 0e59368513a495af0a93a5b8855fd65ef1c44aac
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/15/2019
ms.locfileid: "69509576"
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

이 자습서의 단계를 진행하려면 다음과 같은 Azure AD B2C 리소스가 있어야 합니다.

* [Azure AD B2C 테넌트](tutorial-create-tenant.md)
* 테넌트에서 [등록된 애플리케이션](tutorial-register-applications.md)
* 테넌트에서 [만든 사용자 흐름](tutorial-create-user-flows.md)

또한 로컬 개발 환경에서 다음 항목이 필요합니다.

* 코드 편집기(예: [Visual Studio Code](https://code.visualstudio.com/) 또는 [Visual Studio 2019](https://www.visualstudio.com/downloads/))
* [.NET Core SDK 2.2](https://dotnet.microsoft.com/download) 이상
* [Node.JS](https://nodejs.org/en/download/)

## <a name="update-the-application"></a>애플리케이션 업데이트

필수 조건의 일부로 완료한 두 번째 자습서에서 Azure AD B2C에 웹 애플리케이션을 등록했습니다. 이 자습서에서 샘플과의 통신을 사용하도록 설정하려면 Azure AD B2C의 애플리케이션에 리디렉트 URI를 추가해야 합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. Azure AD B2C 테넌트를 포함하는 디렉터리를 사용하려면 위쪽 메뉴에서 **디렉터리 및 구독 필터**를 선택하고 테넌트가 포함된 디렉터리를 선택합니다.
1. Azure Portal의 왼쪽 상단 모서리에서 **모든 서비스**를 선택한 다음, **Azure AD B2C**를 검색하여 선택합니다.
1. **애플리케이션**을 선택한 후 *webapp1* 애플리케이션을 선택합니다.
1. **회신 URL** 아래에서 `http://localhost:6420`을 추가합니다.
1. **저장**을 선택합니다.
1. 속성 페이지에서 **애플리케이션 ID**를 기록해둡니다. 앱 ID는 나중에 나오는 단계에서 단일 페이지 웹 애플리케이션의 코드를 업데이트할 때 사용됩니다.

## <a name="get-the-sample-code"></a>샘플 코드 가져오기

이 자습서에서는 GitHub에서 다운로드한 코드 샘플을 구성합니다. 이 샘플은 단일 페이지 애플리케이션에서 가입, 로그인 및 보호되는 Web API 호출에 Azure AD B2C를 사용하는 방법을 보여줍니다.

GitHub에서 [zip 파일을 다운로드](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp/archive/master.zip)하거나 샘플을 복제합니다.

```
git clone https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp.git
```

## <a name="update-the-sample"></a>샘플 업데이트

샘플을 확보했으면 이전 단계에서 기록해둔 Azure AD B2C 테넌트 이름과 애플리케이션 ID로 코드를 업데이트합니다.

1. 샘플 디렉터리의 루트에서 `index.html` 파일을 엽니다.
1. `msalConfig` 정의에서 **clientId** 값을 이전 단계에서 기록해둔 애플리케이션 ID로 수정합니다. 그 다음, **authority** URI 값을 Azure AD B2C 테넌트 이름으로 업데이트합니다. URI는 필수 구성 요소 중 하나에서 만든 등록/로그인 사용자 흐름의 이름(예: *B2C_1_signupsignin1*)으로 업데이트합니다.

    ```javascript
    var msalConfig = {
        auth: {
            clientId: "00000000-0000-0000-0000-000000000000", //This is your client ID
            authority: "https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/b2c_1_susi", //This is your tenant info
            validateAuthority: false
        },
        cache: {
            cacheLocation: "localStorage",
            storeAuthStateInCookie: true
        }
    };
    ```

    이 자습서에서 사용된 사용자 흐름 이름은 **B2C_1_signupsignin1**입니다. 다른 사용자 흐름 이름을 사용하는 경우에는 `authority` 값에 해당 이름을 지정합니다.

## <a name="run-the-sample"></a>샘플 실행

1. 콘솔 창을 열고 샘플이 포함된 디렉터리로 변경합니다. 예:

    ```console
    cd active-directory-b2c-javascript-msal-singlepageapp
    ```
1. 다음 명령을 실행합니다.

    ```
    npm install && npm update
    node server.js
    ```

    콘솔 창에 로컬에서 실행 중인 Node.js 서버의 포트 번호가 표시됩니다.

    ```
    Listening on port 6420...
    ```

1. 브라우저에서 `http://localhost:6420`으로 이동하여 애플리케이션을 확인합니다.

이 샘플은 등록, 로그인, 프로필 편집 및 암호 재설정을 지원합니다. 이 자습서에서는 사용자가 이메일 주소를 사용하여 앱에 등록하는 방법을 중점적으로 설명합니다.

### <a name="sign-up-using-an-email-address"></a>전자 메일 주소를 사용하여 등록

1. **로그인**을 선택하여 이전 단계에서 지정한 *B2C_1_signupsignin1* 사용자 흐름을 시작합니다.
1. Azure AD B2C에서 등록 링크가 있는 로그인 페이지를 제공합니다. 아직 계정이 없으므로 **지금 가입** 링크를 선택합니다.
1. 등록 워크플로에서 이메일 주소를 사용하여 사용자의 ID를 수집하고 확인하는 페이지를 제공합니다. 또한 가입 워크플로에서도 사용자 흐름에 정의된 사용자의 암호와 요청된 특성을 수집합니다.

    유효한 이메일 주소를 사용하고 확인 코드를 사용하여 유효성을 검사합니다. 암호를 설정합니다. 요청된 특성에 대한 값을 입력합니다.

    ![로그인/등록 사용자 흐름에서 제공되는 등록 페이지](./media/active-directory-b2c-tutorials-desktop-app/sign-up-workflow.PNG)

1. **만들기**를 선택하여 로컬 계정을 Azure AD B2C 디렉터리에 만듭니다.

**만들기**를 선택하면 가입 페이지가 닫히고 로그인 페이지가 다시 나타납니다.

이제 자신의 이메일 주소와 암호를 사용하여 애플리케이션에 로그인할 수 있습니다.

### <a name="error-insufficient-permissions"></a>오류: 권한 부족

로그인하면 앱에 권한 부족 오류가 표시됩니다. - 이것은 **예상된** 상황입니다.

```Output
ServerError: AADB2C90205: This application does not have sufficient permissions against this web resource to perform the operation.
Correlation ID: ce15bbcc-0000-0000-0000-494a52e95cd7
Timestamp: 2019-07-20 22:17:27Z
```

웹 애플리케이션이 데모 디렉터리인 *fabrikamb2c*로 보호되는 웹 API에 액세스하려고 하기 때문에 이 오류가 발생합니다. 액세스 토큰이 Azure AD 디렉터리에만 유효하므로 API 호출에 권한이 부여되지 않습니다.

이 오류를 수정하려면 시리즈의 다음 자습서를 계속 진행하여([다음 단계](#next-steps) 참조) 디렉터리에 대한 보호되는 웹 API 키를 만드세요.

## <a name="next-steps"></a>다음 단계

이 문서에서는 다음 방법에 대해 알아보았습니다.

> [!div class="checklist"]
> * Azure AD B2C에서 애플리케이션 업데이트
> * 애플리케이션을 사용하도록 샘플 구성
> * 사용자 흐름을 사용하여 등록

이제 시리즈의 다음 자습서로 이동하여 SPA에서 보호된 웹 API에 대한 액세스 권한을 부여하세요.

> [!div class="nextstepaction"]
> [자습서: Azure AD B2C를 사용하여 SPA에서 ASP.NET Core 웹 API에 대한 액세스 권한 부여 >](active-directory-b2c-tutorials-spa-webapi.md)
