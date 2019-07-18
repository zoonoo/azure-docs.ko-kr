---
title: 자습서 - 웹 애플리케이션에서 인증을 사용하도록 설정 - Azure Active Directory B2C | Microsoft Docs
description: Azure Active Directory B2C를 사용하여 ASP.NET 웹 애플리케이션에 대한 사용자 로그인을 제공하는 방법에 대한 자습서입니다.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.author: marsma
ms.date: 02/04/2019
ms.custom: mvc
ms.topic: tutorial
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: e9e497c8f10108b8e05314f35546345f7f812524
ms.sourcegitcommit: adb6c981eba06f3b258b697251d7f87489a5da33
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/04/2019
ms.locfileid: "66507796"
---
# <a name="tutorial-enable-authentication-in-a-web-application-using-azure-active-directory-b2c"></a>자습서: Azure Active Directory B2C를 사용하여 웹 애플리케이션에서 인증을 사용하도록 설정

이 자습서에서는 Azure AD(Azure Active Directory) B2C를 사용하여 로그인하고 ASP.NET 웹 애플리케이션에서 사용자를 로그인하고 등록하는 방법을 보여 줍니다. Azure AD B2C를 사용하면 애플리케이션이 개방형 표준 프로토콜을 사용하여 소셜 계정, 엔터프라이즈 계정 및 Azure Active Directory 계정을 인증할 수 있습니다.

이 자습서에서는 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * Azure AD B2C에서 애플리케이션 업데이트
> * 애플리케이션을 사용하도록 샘플 구성
> * 사용자 흐름을 사용하여 등록

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>필수 조건

- [사용자 흐름을 생성](tutorial-create-user-flows.md)하여 애플리케이션에 사용자 환경을 사용하도록 설정합니다. 
- **ASP.NET 및 웹 개발** 워크로드가 있는 [Visual Studio 2019](https://www.visualstudio.com/downloads/)를 설치합니다.

## <a name="update-the-application"></a>애플리케이션 업데이트

필수 조건의 일부로 완료한 자습서에서 Azure AD B2C에 웹 애플리케이션을 추가했습니다. 이 자습서에서 샘플과의 통신을 사용하도록 설정하려면 Azure AD B2C의 애플리케이션에 리디렉트 URI를 추가해야 합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. Azure AD B2C 테넌트를 포함하는 디렉터리를 사용하려면 위쪽 메뉴에서 **디렉터리 및 구독 필터**를 클릭하고 테넌트가 포함된 디렉터리를 선택합니다.
3. Azure Portal의 왼쪽 상단 모서리에서 **모든 서비스**를 선택하고 **Azure AD B2C**를 검색하여 선택합니다.
4. **애플리케이션**을 선택한 후 *webapp1* 애플리케이션을 선택합니다.
5. **회신 URL** 아래에서 `https://localhost:44316`을 추가합니다.
6. **저장**을 선택합니다.
7. 속성 페이지에서 웹 애플리케이션을 구성할 때 사용할 애플리케이션 ID를 기록합니다.
8. **키**, **키 생성**을 차례로 선택하고 **저장**을 선택합니다. 웹 애플리케이션을 구성할 때 사용할 키를 기록합니다.

## <a name="configure-the-sample"></a>샘플 구성

이 자습서에서는 GitHub에서 다운로드할 수 있는 샘플을 구성합니다. 이 샘플은 ASP.NET을 사용하여 간단한 할 일 목록을 제공합니다. 이 샘플은 [Microsoft OWIN 미들웨어 구성 요소](https://docs.microsoft.com/aspnet/aspnet/overview/owin-and-katana/)를 사용합니다. GitHub에서 [zip 파일을 다운로드](https://github.com/Azure-Samples/active-directory-b2c-dotnet-webapp-and-webapi/archive/master.zip)하거나 샘플을 복제합니다. 경로의 총 문자 길이가 260자 미만인 폴더에 샘플 파일을 추출합니다.

```
git clone https://github.com/Azure-Samples/active-directory-b2c-dotnet-webapp-and-webapi.git
```

다음의 두 프로젝트는 샘플 솔루션에 있습니다.

- **TaskWebApp** - 작업 목록을 만들고 편집합니다. 이 샘플은 **등록 또는 로그인** 사용자 흐름을 사용하여 사용자를 등록하거나 로그인시킵니다.
- **TaskService** - 작업 목록 만들기, 읽기, 업데이트 및 삭제 기능을 지원합니다. API는 Azure AD B2C를 통해 보호되고 TaskWebApp에서 호출됩니다.

테넌트에서 등록된 애플리케이션을 사용하도록 샘플을 변경해야 하며, 여기에는 애플리케이션 ID와 이전에 기록한 키가 포함됩니다. 또한 만든 사용자 흐름도 구성해야 합니다. 샘플은 Web.config 파일에서 구성 값을 설정으로 정의합니다. 설정을 변경하려면 다음을 수행합니다.

1. Visual Studio에서 **B2C-WebAPI-DotNet** 솔루션을 엽니다.
2. **TaskWebApp** 프로젝트에서 **Web.config** 파일을 엽니다. `ida:Tenant`의 값을 앞에서 만든 테넌트 이름으로 바꿉니다. `ida:ClientId`의 값을 앞에서 기록한 응용 프로그램 ID로 바꿉니다. `ida:ClientSecret`의 값을 앞에서 기록한 키로 바꿉니다.
3. **Web.config** 파일에서 `ida:SignUpSignInPolicyId`의 값을 `b2c_1_signupsignin1`으로 바꿉니다. `ida:EditProfilePolicyId`의 값을 `b2c_1_profileediting1`로 바꿉니다. `ida:ResetPasswordPolicyId`의 값을 `b2c_1_passwordreset1`로 바꿉니다.

## <a name="run-the-sample"></a>샘플 실행

1. 솔루션 탐색기에서 **TaskWebApp** 프로젝트를 마우스 오른쪽 단추로 클릭한 후 **시작 프로젝트로 설정**을 클릭합니다.
2. **F5**키를 누릅니다. 기본 브라우저가 로컬 웹 사이트 주소(`https://localhost:44316/`)에서 시작됩니다.

### <a name="sign-up-using-an-email-address"></a>전자 메일 주소를 사용하여 등록

1. **등록/로그인**을 클릭하여 애플리케이션의 사용자로 등록합니다. **b2c_1_signupsignin1** 사용자 흐름이 사용됩니다.
2. Azure AD B2C에서 등록 링크가 있는 로그인 페이지를 제공합니다. 아직 계정이 없으므로 **지금 등록**을 선택합니다. 등록 워크플로에서 이메일 주소를 사용하여 사용자의 ID를 수집하고 확인하는 페이지를 제공합니다. 또한 가입 워크플로에서도 사용자 흐름에 정의된 사용자의 암호와 요청된 특성을 수집합니다.
3. 유효한 이메일 주소를 사용하고 확인 코드를 사용하여 유효성을 검사합니다. 암호를 설정합니다. 요청된 특성에 대한 값을 입력합니다. 

    ![등록 워크플로](media/active-directory-b2c-tutorials-web-app/sign-up-workflow.png)

4. **만들기**를 클릭하여 로컬 계정을 Azure AD B2C 테넌트에 만듭니다.

이제 사용자는 이메일 주소를 사용하여 로그인하고 웹 애플리케이션을 사용할 수 있습니다.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 다음 방법에 대해 알아보았습니다.

> [!div class="checklist"]
> * Azure AD B2C에서 애플리케이션 업데이트
> * 애플리케이션을 사용하도록 샘플 구성
> * 사용자 흐름을 사용하여 등록

> [!div class="nextstepaction"]
> [자습서: Azure Active Directory B2C를 사용하여 ASP.NET 웹 API 보호](active-directory-b2c-tutorials-web-api.md)
