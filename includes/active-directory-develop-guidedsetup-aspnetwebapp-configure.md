---
title: 포함 파일
description: 포함 파일
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/11/2019
ms.author: jmprieur
ms.custom: include file
ms.openlocfilehash: 2cdc6ea01e6c3555740102f319d0f4e8e4fc1c22
ms.sourcegitcommit: 1c2cf60ff7da5e1e01952ed18ea9a85ba333774c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/12/2019
ms.locfileid: "59528533"
---
## <a name="register-your-application"></a>애플리케이션 등록

애플리케이션을 등록하고 애플리케이션 등록 정보를 솔루션에 추가하는 두 가지 옵션이 있습니다.

### <a name="option-1-express-mode"></a>옵션 1: 기본 모드

다음을 수행하여 애플리케이션을 신속하게 등록할 수 있습니다.

1. 새 [Azure Portal - 앱 등록](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/AspNetWebAppQuickstartPage/sourceType/docs) 창으로 이동합니다.
1. 애플리케이션 이름을 입력하고 **등록**을 클릭합니다.
1. 지침에 따라 클릭 한 번으로 새 애플리케이션을 다운로드하고 자동으로 구성합니다.

### <a name="option-2-advanced-mode"></a>옵션 2: 고급 모드

애플리케이션을 등록하고 앱의 등록 정보를 솔루션에 수동으로 추가하려면 다음 단계를 따르세요.

1. Visual Studio 갤러리로 이동한 후
   1. 솔루션 탐색기에서 프로젝트를 선택하고 속성 창을 확인합니다. 속성 창이 보이지 않으면 F4 키를 누릅니다.
   1. SSL 사용을 `True`로 변경합니다.
   1. Visual Studio에서 프로젝트를 마우스 오른쪽 단추로 클릭한 다음, **속성** 및 **웹** 탭을 선택합니다. *서버* 섹션에서 *프로젝트 URL*을 SSL URL이 되도록 변경합니다.
   1. SSL URL을 복사합니다. 다음 단계에 나오는 등록 포털의 리디렉션 URL 목록에 있는 리디렉션 URL 목록에 이 URL을 추가합니다.<br/><br/>![프로젝트 속성](media/active-directory-develop-guidedsetup-aspnetwebapp-configure/vsprojectproperties.png)<br />
1. [Azure Portal](https://portal.azure.com)에 회사 또는 학교 계정, 개인 Microsoft 계정으로 로그인합니다.
1. 계정이 둘 이상의 테넌트에 대해 액세스를 제공하는 경우 오른쪽 위 모서리에 있는 계정을 선택하여 원하는 Azure AD 테넌트로 포털 세션을 설정합니다.
1. 개발자용 Microsoft ID 플랫폼 [앱 등록](https://go.microsoft.com/fwlink/?linkid=2083908) 페이지로 이동합니다.
1. **새 등록**을 선택합니다.
1. **애플리케이션 등록** 페이지가 표시되면 애플리케이션의 등록 정보를 입력합니다.
   1. **이름** 섹션에서 앱의 사용자에게 표시되는 의미 있는 애플리케이션 이름(예: `ASPNET-Tutorial`)을 입력합니다.
   1. 1단계에 Visual Studio에서 복사한 SSL URL(예: `https://localhost:44368/`)을 **회신 URL**에 추가하고 **등록**을 클릭합니다.
1. **인증** 메뉴를 선택하고 **암시적 허용** 아래에서 **ID 토큰**을 설정한 후 **저장을 선택합니다**.
1. 다음 내용을 루트 폴더에 있는 `web.config`의 `configuration\appSettings` 섹션 아래에 추가합니다.

    ```xml
    <add key="ClientId" value="Enter_the_Application_Id_here" />
    <add key="redirectUri" value="Enter_the_Redirect_URL_here" />
    <add key="Tenant" value="common" />
    <add key="Authority" value="https://login.microsoftonline.com/{0}/v2.0" />
    ```

1. `ClientId`를 방금 등록한 응용 프로그램 ID로 바꿉니다.
1. `redirectUri`를 프로젝트의 SSL URL로 바꿉니다.
