---
title: 빠른 시작 - Azure Active Directory B2C를 사용하여 단일 페이지 앱에 대한 로그인 설정 | Microsoft Docs
description: Azure Active Directory B2C를 사용하여 계정 로그인을 제공하는 샘플 단일 페이지 애플리케이션을 실행합니다.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: quickstart
ms.date: 10/24/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: dff1716e8886d1ef72f4d2ad4fbca4bcab86a348
ms.sourcegitcommit: adb6c981eba06f3b258b697251d7f87489a5da33
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/04/2019
ms.locfileid: "66509786"
---
# <a name="quickstart-set-up-sign-in-for-a-single-page-app-using-azure-active-directory-b2c"></a>빠른 시작: Azure Active Directory B2C를 사용하여 단일 페이지 앱에 대한 로그인 설정

Azure AD(Azure Active Directory) B2C는 애플리케이션, 비즈니스 및 고객을 보호하기 위한 클라우드 ID 관리 기능을 제공합니다. Azure AD B2C를 사용하면 응용 프로그램에서 개방형 표준 프로토콜을 사용하여 소셜 계정 및 엔터프라이즈 계정을 인증할 수 있습니다. 이 빠른 시작에서는 단일 페이지 응용 프로그램에서 소셜 ID 공급자를 사용하여 로그인하고 Azure AD B2C로 보호되는 웹 API를 호출합니다.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>필수 조건

- **ASP.NET 및 웹 개발** 워크로드가 있는 [Visual Studio 2019](https://www.visualstudio.com/downloads/)
- [Node.js](https://nodejs.org/en/download/)
- Facebook, Google, Microsoft 또는 Twitter의 소셜 계정입니다.
- GitHub에서 [Zip 파일을 다운로드](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp/archive/master.zip)하거나 샘플 웹앱을 복제합니다.

    ```
    git clone https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp.git
    ```

## <a name="run-the-application"></a>애플리케이션 실행

1. Node.js 명령 프롬프트에서 다음 명령을 실행하여 서버를 시작합니다. 

    ```
    cd active-directory-b2c-javascript-msal-singlepageapp
    npm install && npm update
    node server.js
    ```

    Server.js는 localhost에서 수신 대기 중인 포트 번호를 출력합니다.

    ```
    Listening on port 6420...
    ```

2. 응용 프로그램의 URL로 이동합니다. 예: `http://localhost:6420`

## <a name="sign-in-using-your-account"></a>계정을 사용하여 로그인

1. **로그인**을 클릭하여 워크플로를 시작합니다.

    ![브라우저의 샘플 앱](media/active-directory-b2c-quickstarts-spa/sample-app-spa.png)

    이 샘플에서는 소셜 ID 공급자를 사용하거나 이메일 주소를 사용하여 로컬 계정을 만드는 등의 여러 가지 등록 옵션을 지원합니다. 이 빠른 시작에서는 Facebook, Google, Microsoft 또는 Twitter의 소셜 ID 공급자 계정을 사용합니다. 

2. Azure AD B2C는 샘플 웹앱용으로 Wingtip Toys라는 가상의 브랜드에 대한 사용자 지정 로그인 페이지를 제공합니다. 소셜 ID 공급자를 사용하여 등록하려면 사용할 ID 공급자의 단추를 클릭합니다.

    ![로그인 또는 등록 공급자](media/active-directory-b2c-quickstarts-spa/sign-in-or-sign-up-spa.png)

    소셜 계정 자격 증명을 사용하여 인증(로그인)하고 사용자의 소셜 계정에서 정보를 읽도록 애플리케이션에 권한을 부여합니다. 액세스를 부여하면 애플리케이션은 이름 및 구/군/시와 같은 소셜 계정의 프로필 정보를 검색할 수 있습니다. 

3. ID 공급자에 대한 로그인 프로세스를 완료합니다.

## <a name="access-a-protected-api-resource"></a>보호된 API 리소스 액세스

**웹 API 호출**을 클릭하여 웹 API 호출에서 반환된 표시 이름을 JSON 개체로 지정합니다. 

![웹 API 응답](media/active-directory-b2c-quickstarts-spa/call-api-spa.png)

샘플 단일 페이지 응용 프로그램은 보호되는 웹 API 리소스에 대한 요청에 액세스 토큰을 포함합니다.

## <a name="clean-up-resources"></a>리소스 정리

다른 Azure AD B2C 빠른 시작 또는 자습서를 사용하려는 경우 Azure AD B2C 테넌트를 사용할 수 있습니다. 더 이상 필요하지 않으면 [Azure AD B2C 테넌트를 삭제](active-directory-b2c-faqs.md#how-do-i-delete-my-azure-ad-b2c-tenant)할 수 있습니다.

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 샘플 단일 페이지 애플리케이션을 사용하여 다음 작업을 수행합니다.

* 사용자 지정 로그인 페이지를 사용하여 로그인
* 소셜 ID 공급자를 사용하여 로그인
* Azure AD B2C 계정 만들기
* Azure AD B2C로 보호되는 웹 API 호출

사용자 고유의 Azure AD B2C 테넌트 만들기를 시작하세요.

> [!div class="nextstepaction"]
> [Azure Portal에서 Azure Active Directory B2C 테넌트 만들기](tutorial-create-tenant.md)
