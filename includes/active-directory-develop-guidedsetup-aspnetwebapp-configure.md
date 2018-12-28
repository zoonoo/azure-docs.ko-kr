---
title: 포함 파일
description: 포함 파일
services: active-directory
documentationcenter: dev-center-name
author: andretms
manager: mtillman
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/04/2018
ms.author: andret
ms.custom: include file
ms.openlocfilehash: 15db2192703971a8056df34343c427db11c8411a
ms.sourcegitcommit: c2c279cb2cbc0bc268b38fbd900f1bac2fd0e88f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/24/2018
ms.locfileid: "49988522"
---
## <a name="register-your-application"></a>애플리케이션 등록

애플리케이션을 등록하고 애플리케이션 등록 정보를 솔루션에 추가하는 두 가지 옵션이 있습니다.

### <a name="option-1-express-mode"></a>옵션 1: 기본 모드

다음을 수행하여 애플리케이션을 신속하게 등록할 수 있습니다.

1. [Microsoft 응용 프로그램 등록 포털](https://apps.dev.microsoft.com/portal/register-app?appType=serverSideWebApp&appTech=aspNetWebAppOwin&step=configure)을 통해 응용 프로그램을 등록합니다.
2. 응용 프로그램 이름과 이메일을 입력합니다.
3. [단계별 설정] 옵션이 선택되어 있는지 확인합니다.
4. 지침에 따라 응용 프로그램에 리디렉션 URL을 추가합니다.

### <a name="option-2-advanced-mode"></a>옵션 2: 고급 모드

애플리케이션을 등록하고 애플리케이션 등록 정보를 솔루션에 추가하려면 다음을 수행합니다.

1. [Microsoft 응용 프로그램 등록 포털](https://apps.dev.microsoft.com/portal/register-app)로 이동하여 응용 프로그램을 등록합니다.
2. 응용 프로그램 이름과 이메일을 입력합니다.
3. 안내식 설정 옵션이 선택 취소되어 있는지 확인합니다.
4. `Add Platform`, `Web`을 차례로 선택합니다.
5. Visual Studio로 돌아가 솔루션 탐색기에서 프로젝트를 선택하고 [속성] 창을 확인합니다([속성] 창이 보이지 않으면 F4 누르기).
6. SSL 사용을 `True`로 변경합니다.
7. Visual Studio에서 프로젝트를 마우스 오른쪽 단추로 클릭한 다음, **속성** 및 **웹** 탭을 선택합니다. *서버* 섹션에서 *프로젝트 URL*을 SSL URL이 되도록 변경합니다.
8. SSL URL을 복사하고 등록 포털의 리디렉션 URL 목록에서 리디렉션 URL 목록에 이 URL을 추가합니다.<br/><br/>![프로젝트 속성](media/active-directory-develop-guidedsetup-aspnetwebapp-configure/vsprojectproperties.png)<br />
9. 다음 내용을 루트 폴더에 있는 `web.config`의 `configuration\appSettings` 섹션 아래에 추가합니다.

    ```xml
    <add key="ClientId" value="Enter_the_Application_Id_here" />
    <add key="redirectUri" value="Enter_the_Redirect_URL_here" />
    <add key="Tenant" value="common" />
    <add key="Authority" value="https://login.microsoftonline.com/{0}/v2.0" />
    ```

10. `ClientId`를 방금 등록한 응용 프로그램 ID로 바꿉니다.
11. `redirectUri`를 프로젝트의 SSL URL로 바꿉니다.
