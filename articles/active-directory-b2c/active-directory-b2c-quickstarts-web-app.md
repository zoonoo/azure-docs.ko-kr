---
title: "Azure AD B2C 사용 웹앱 시험 사용 | Microsoft Docs"
description: "테스트 Azure AD B2C 환경을 사용하여 로그인, 등록, 프로필 편집 및 암호 재설정 사용자 경험을 시험 사용"
services: active-directory-b2c
documentationcenter: .net
author: saraford
manager: mtillman
editor: PatAltimore
ms.assetid: 2ffb780d-2c51-4c2e-b8d6-39c40a81a77e
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 10/31/2017
ms.author: patricka
ms.openlocfilehash: bc56da695145f396a2899fb9dc7add3af9a549e8
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/11/2017
---
# <a name="test-drive-an-azure-ad-b2c-enabled-web-app"></a>Azure AD B2C 사용 웹앱 시험 사용

Azure Active Directory B2C는 응용 프로그램, 비즈니스 및 고객을 보호하기 위한 클라우드 ID 관리 기능을 제공합니다. 이 빠른 시작은 샘플 할 일 목록 앱을 사용하여 다음을 설명합니다.

> [!div class="checklist"]
> * 사용자 지정 로그인 페이지를 사용하여 로그인
> * 소셜 ID 공급자를 사용하여 로그인
> * Azure AD B2C 계정 및 사용자 프로필 만들기 및 관리
> * Azure AD B2C로 보호되는 웹 API 호출

## <a name="prerequisites"></a>필수 조건

* **ASP.NET 및 웹 개발** 워크로드가 있는 [Visual Studio 2017](https://www.visualstudio.com/downloads/) 
* Facebook, Google, Microsoft 또는 Twitter의 소셜 계정입니다.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="download-the-sample"></a>샘플 다운로드

GitHub에서 [샘플 응용 프로그램을 다운로드 또는 복제](https://github.com/Azure-Samples/active-directory-b2c-dotnet-webapp-and-webapi)합니다.

## <a name="run-the-app-in-visual-studio"></a>Visual Studio에서 앱 실행

샘플 응용 프로그램 프로젝트 폴더에서 Visual Studio를 통해 `B2C-WebAPI-DotNet.sln` 솔루션을 엽니다. 

이 솔루션은 두 프로젝트로 구성된 할 일 목록 샘플 응용 프로그램입니다.

* **TaskWebApp** – ASP.NET MVC 웹 응용 프로그램에서 사용자가 해당 할 일 목록 항목을 관리할 수 있습니다.  
* **TaskService** – 사용자의 할 일 목록 항목에서 수행된 작업을 관리하는 ASP.NET Web API 백 엔드입니다. 웹앱은 이 웹 API를 호출하고 결과를 표시합니다.

이 빠른 시작에서 동시에 `TaskWebApp` 및 `TaskService` 프로젝트를 둘 다 실행해야 합니다. 

1. Visual Studio 메뉴에서 **프로젝트 > 시작 프로젝트 설정...**을 차례로 선택합니다. 
2. **여러 시작 프로젝트** 라디오 단추를 선택합니다.
3. **시작**할 두 프로젝트에 대한 **작업**을 변경합니다. **확인**을 클릭합니다.

![Visual Studio에서 시작 페이지 설정](media/active-directory-b2c-quickstarts-web-app/setup-startup-projects.png)

**디버그 > 디버깅 시작**을 선택하여 두 응용 프로그램을 빌드하고 실행합니다. 각 응용 프로그램은 고유한 브라우저 탭에서 열립니다.

`https://localhost:44316/` - 이 페이지는 ASP.NET 웹 응용 프로그램입니다. 빠른 시작에서 이 응용 프로그램과 직접 상호 작용합니다.
`https://localhost:44332/` - 이 페이지는 ASP.NET 웹 응용 프로그램에 의해 호출되는 Web API입니다.

## <a name="create-an-account"></a>계정 만들기

ASP.NET 웹 응용 프로그램에서 **등록/로그인** 링크를 클릭하여 **등록 또는 로그인** 워크플로를 시작합니다. 계정을 만들 때 기존 소셜 ID 공급자 계정 또는 전자 메일 계정을 사용할 수 있습니다. 이 빠른 시작에서는 Facebook, Google, Microsoft 또는 Twitter의 소셜 ID 공급자 계정을 사용합니다.

![샘플 ASP.NET 웹앱](media/active-directory-b2c-quickstarts-web-app/web-app-sign-in.png)

### <a name="sign-up-using-a-social-identity-provider"></a>소셜 ID 공급자를 사용하여 등록

소셜 ID 공급자를 사용하여 등록하려면 사용할 ID 공급자의 단추를 클릭합니다. 

![로그인 또는 등록 공급자](media/active-directory-b2c-quickstarts-web-app/sign-in-or-sign-up-web.png)

소셜 계정 자격 증명을 사용하여 인증(로그인)하고 응용 프로그램에 사용자의 소셜 계정에서 정보를 읽도록 권한을 부여해야 합니다. 액세스를 부여하면 응용 프로그램은 이름 및 구/군/시와 같은 소셜 계정의 프로필 정보를 검색할 수 있습니다. 

ID 공급자에 대한 로그인 프로세스를 완료합니다. 예를 들어 Twitter를 선택하면 Twitter 자격 증명을 입력하고 **로그인**을 클릭합니다.

![소셜 계정을 사용하여 인증 및 권한 부여](media/active-directory-b2c-quickstarts-web-app/twitter-authenticate-authorize-web.png)

새 Azure AD B2C 계정 프로필 세부 정보는 소셜 계정의 정보로 미리 채워져 있습니다.

표시 이름, 직책 및 구/군/시 필드를 업데이트하고 **계속**을 클릭합니다.  입력하는 값은 Azure AD B2C 사용자 계정 프로필에 사용됩니다.

![새 계정 등록 프로필 세부 정보](media/active-directory-b2c-quickstarts-web-app/new-account-sign-up-profile-details-web.png)

다음을 성공적으로 수행했습니다.

> [!div class="checklist"]
> * ID 공급자를 사용하여 인증했습니다.
> * Azure AD B2C 사용자 계정을 만들었습니다. 

## <a name="edit-your-profile"></a>프로필 편집

Azure Active Directory B2C에는 사용자가 프로필을 업데이트할 수 있는 기능이 있습니다. 웹 응용 프로그램 메뉴 모음에서 프로필 이름을 클릭하고 **프로필 편집**을 선택하여 만든 프로필을 편집합니다.

![프로필 편집](media/active-directory-b2c-quickstarts-web-app/edit-profile-web.png)

**표시 이름** 및 **구/군/시**를 변경합니다.  **계속**을 클릭하여 프로필을 업데이트합니다.

![프로필 업데이트](media/active-directory-b2c-quickstarts-web-app/update-profile-web.png)

페이지 오른쪽 위의 표시 이름에 업데이트된 이름이 표시됩니다. 

## <a name="access-a-secured-web-api-resource"></a>보안 웹 API 리소스 액세스

**할 일 목록**을 클릭하여 할 일 목록 항목을 입력하고 수정합니다. ASP.NET 웹 응용 프로그램에는 사용자의 할 일 목록 항목에 대한 작업을 수행하기 위해 Web API 리소스 요청 권한에 대한 요청에서 액세스 토큰을 포함합니다. 

**새 항목** 텍스트 상자에 텍스트를 입력합니다. **추가**를 클릭하여 할 일 목록 항목을 추가하는 Azure AD B2C 보호 Web API를 호출합니다.

![할 일 목록 항목 추가](media/active-directory-b2c-quickstarts-web-app/add-todo-item-web.png)

Azure AD B2C 사용자 계정을 성공적으로 사용하여 Azure AD B2C 보안 웹 API를 부여받은 권한으로 호출했습니다.

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서 사용된 샘플은 다음을 포함하여 다른 Azure AD B2C 시나리오를 시도하는 데 사용할 수 있습니다.

* 이메일 주소를 사용하여 새 로컬 계정 만들기
* 로컬 계정 암호 재설정

사용자 고유의 Azure AD B2C 테넌트를 만드는 방법을 살펴보고 이 테넌트를 사용하여 실행할 샘플을 구성할 준비가 되었다면 다음 자습서를 사용해 보세요.

> [!div class="nextstepaction"]
> [Azure Active Directory B2C 등록, 로그인, 프로필 편집 및 암호 재설정을 사용하여 ASP.NET 웹앱 만들기](active-directory-b2c-devquickstarts-web-dotnet-susi.md)