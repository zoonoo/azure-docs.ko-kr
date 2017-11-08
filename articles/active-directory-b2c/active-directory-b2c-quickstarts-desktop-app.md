---
title: "Azure AD B2C 데스크톱 응용 프로그램 시험 사용 | Microsoft Docs"
description: "테스트 Azure AD B2C 환경을 사용하여 로그인, 등록, 프로필 편집 및 암호 재설정 사용자 경험을 시험 사용"
services: active-directory-b2c
documentationcenter: .net
author: saraford
manager: krassk
editor: PatAltimore
ms.assetid: 86293627-26fb-4e96-a76b-f263f9a945bd
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 10/31/2017
ms.author: saraford
ms.openlocfilehash: 9653d86dd5635016512bf6e6fafbf7195145ed07
ms.sourcegitcommit: 43c3d0d61c008195a0177ec56bf0795dc103b8fa
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/01/2017
---
# <a name="test-drive-a-desktop-application-configured-with-azure-ad-b2c"></a>Azure AD B2C로 구성된 데스크톱 응용 프로그램 시험 사용

Azure Active Directory B2C는 응용 프로그램, 비즈니스 및 고객을 보호하기 위한 클라우드 ID 관리 기능을 제공합니다.  이 빠른 시작에서는 샘플 WPF(Windows Presentation Foundation) 데스크톱 앱을 사용하여 다음을 보여 줍니다.

* **등록 또는 로그인** 정책을 사용하여 전자 메일 주소로 소셜 ID 공급자 또는 로컬 계정을 만들고 이러한 계정으로 로그인합니다. 
* **API를 호출**하여 Azure AD B2C 보호 리소스에서 표시 이름을 검색합니다.

## <a name="prerequisites"></a>필수 조건

* 다음 워크로드와 함께 [Visual Studio 2017](https://www.visualstudio.com/downloads/)을 설치합니다.
    - **.NET 데스크톱 개발**

* Facebook, Google, Microsoft 또는 Twitter의 소셜 계정입니다. 소셜 계정이 없는 경우 유효한 전자 메일 주소가 필요합니다.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="download-the-sample"></a>샘플 다운로드

GitHub에서 [샘플 응용 프로그램을 다운로드 또는 복제](https://github.com/Azure-Samples/active-directory-b2c-dotnet-desktop)합니다.

## <a name="run-the-app-in-visual-studio"></a>Visual Studio에서 앱 실행

샘플 응용 프로그램 프로젝트 폴더에서 Visual Studio를 통해 `active-directory-b2c-wpf.sln` 솔루션을 엽니다. 

**디버그 > 디버깅 시작**을 선택하여 응용 프로그램을 빌드하고 실행합니다. 

## <a name="create-an-account"></a>계정 만들기

**로그인**을 클릭하여 **등록 또는 로그인** 워크플로를 시작합니다. 계정을 만들 때 기존 소셜 ID 공급자 계정 또는 전자 메일 계정을 사용할 수 있습니다.

![샘플 응용 프로그램](media/active-directory-b2c-quickstarts-desktop-app/wpf-sample-application.png)

### <a name="sign-up-using-a-social-identity-provider"></a>소셜 ID 공급자를 사용하여 등록

소셜 ID 공급자를 사용하여 등록하려면 사용할 ID 공급자의 단추를 클릭합니다. 전자 메일 주소를 사용하려는 경우 [전자 메일 주소를 사용하여 등록](#sign-up-using-an-email-address) 섹션으로 이동합니다.

![로그인 또는 등록 공급자](media/active-directory-b2c-quickstarts-desktop-app/sign-in-or-sign-up-wpf.png)

소셜 계정 자격 증명을 사용하여 인증(로그인)하고 응용 프로그램에 사용자의 소셜 계정에서 정보를 읽도록 권한을 부여해야 합니다. 액세스를 부여하면 응용 프로그램은 이름 및 구/군/시와 같은 소셜 계정의 프로필 정보를 검색할 수 있습니다. 

![소셜 계정을 사용하여 인증 및 권한 부여](media/active-directory-b2c-quickstarts-desktop-app/twitter-authenticate-authorize-wpf.png)

새 계정 프로필 세부 정보는 소셜 계정의 정보로 미리 채워집니다. 원할 경우 세부 정보를 수정하고 **계속**을 클릭합니다.

![새 계정 등록 프로필 세부 정보](media/active-directory-b2c-quickstarts-desktop-app/new-account-sign-up-profile-details-wpf.png)

ID 공급자를 사용하는 새 Azure AD B2C 사용자 계정을 성공적으로 만들었습니다. 로그인하면 *토큰 정보* 입력란에 액세스 토큰이 표시됩니다. 액세스 토큰은 API 리소스에 액세스할 때 사용됩니다.

![권한 부여 토큰](media/active-directory-b2c-quickstarts-desktop-app/twitter-auth-token.png)

다음 단계: [프로필 편집](#edit-your-profile) 섹션으로 이동합니다.

### <a name="sign-up-using-an-email-address"></a>전자 메일 주소를 사용하여 등록

소셜 계정을 사용하여 인증을 제공하지 않으려는 경우 유효한 전자 메일 주소를 사용하여 Azure AD B2C 사용자 계정을 만들 수 있습니다. Azure AD B2C 로컬 사용자 계정은 ID 공급자로 Azure Active Directory를 사용합니다. 전자 메일 주소를 사용하려면 **계정이 없으세요? 지금 등록하세요.** 링크를 클릭합니다.

![전자 메일을 사용하여 로그인 또는 등록](media/active-directory-b2c-quickstarts-desktop-app/sign-in-or-sign-up-email-wpf.png)

유효한 전자 메일 주소를 입력하고 **확인 코드 보내기**를 클릭합니다. 유효한 전자 메일 주소는 Azure AD B2C에서 확인 코드를 받는 데 필요합니다.

전자 메일에서 수신한 확인 코드를 입력하고 **코드 확인**을 클릭합니다.

프로필 정보를 추가하고 **만들기**를 클릭합니다.

![전자 메일을 사용하여 새 계정 등록](media/active-directory-b2c-quickstarts-desktop-app/sign-up-new-account-profile-email-wpf.png)

새 Azure AD B2C 로컬 사용자 계정을 성공적으로 만들었습니다. 로그인하면 *토큰 정보* 입력란에 액세스 토큰이 표시됩니다. 액세스 토큰은 API 리소스에 액세스할 때 사용됩니다.

![권한 부여 토큰](media/active-directory-b2c-quickstarts-desktop-app/twitter-auth-token.png)

## <a name="edit-your-profile"></a>프로필 편집

Azure Active Directory B2C에는 사용자가 프로필을 업데이트할 수 있는 기능이 있습니다. **프로필 편집**을 클릭하여 만든 프로필을 편집합니다.

![프로필 편집](media/active-directory-b2c-quickstarts-desktop-app/edit-profile-wpf.png)

만든 계정과 연결된 ID 공급자를 선택합니다. 예를 들어 계정을 만들 때 ID 공급자로 Twitter를 사용한 경우 연결된 프로필 세부 정보를 수정하려면 Twitter를 선택합니다.

![편집할 프로필에 연결된 공급자 선택](media/active-directory-b2c-quickstarts-desktop-app/edit-account-choose-provider-wpf.png)

**표시 이름** 또는 **구/군/시**를 변경합니다. 

![프로필 업데이트](media/active-directory-b2c-quickstarts-desktop-app/update-profile-wpf.png)

새 액세스 토큰이 *토큰 정보* 텍스트 상자에 표시됩니다. 프로필의 변경 내용을 확인하려는 경우 액세스 토큰을 복사한 후 토큰 디코더 https://jwt.ms에 붙여 넣습니다.

![권한 부여 토큰](media/active-directory-b2c-quickstarts-desktop-app/twitter-auth-token.png)

## <a name="access-a-resource"></a>리소스 액세스

**API 호출**을 클릭하여 Azure AD B2C 보안 리소스 https://fabrikamb2chello.azurewebsites.net/hello에 대한 요청을 만듭니다. 

![API 호출](media/active-directory-b2c-quickstarts-desktop-app/call-api-wpf.png)

응용 프로그램에는 요청의 *토큰 정보* 텍스트 상자에 표시되는 액세스 토큰이 포함되어 있습니다. API는 액세스 토큰에 포함된 표시 이름을 다시 전송합니다.

## <a name="next-steps"></a>다음 단계

다음 단계는 자체 Azure AD B2C 테넌트를 만들고 해당 테넌트를 사용하여 실행되도록 샘플을 구성하는 것입니다. 

> [!div class="nextstepaction"]
> [Azure Portal에서 Azure Active Directory B2C 테넌트 만들기](active-directory-b2c-get-started.md)
