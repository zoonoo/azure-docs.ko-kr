---
title: 포함 파일
description: 포함 파일
services: active-directory
documentationcenter: dev-center-name
author: navyasric
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/17/2018
ms.author: nacanuma
ms.custom: include file
ms.openlocfilehash: 050bae64a62e90bdb74c93948109e255b69d7518
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67133406"
---
## <a name="register-your-application"></a>애플리케이션 등록

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.

1. 계정이 둘 이상의 테넌트에 대해 액세스를 제공하는 경우 오른쪽 위에 있는 계정을 선택한 다음, 사용할 Azure AD 테넌트로 포털 세션을 설정합니다.
1. 개발자용 Microsoft ID 플랫폼 [앱 등록](https://go.microsoft.com/fwlink/?linkid=2083908) 페이지로 이동합니다.
1. **애플리케이션 등록** 페이지가 나타나면 애플리케이션의 이름을 입력합니다.
1. **지원되는 계정 유형** 아래에서 **모든 조직 디렉터리의 계정 및 개인 Microsoft 계정**을 선택합니다.
1. **리디렉션 URI** 섹션의 드롭다운 목록에서 **웹** 플랫폼을 선택한 다음, 사용하는 웹 서버에 기반하는 애플리케이션 URL로 값을 설정합니다. 

   Visual Studio와 Node.js에서 리디렉션 URL을 설정하고 가져오는 방법에 대한 자세한 내용은 다음 두 섹션을 참조하세요.

1. **등록**을 선택합니다.
1. 나중에 사용할 수 있도록 앱 **개요** 페이지에서 **애플리케이션(클라이언트) ID** 값을 기록해 둡니다.
1. 이 빠른 시작에서는 [암시적 허용 흐름](../articles/active-directory/develop/v2-oauth2-implicit-grant-flow.md)을 사용하도록 설정해야 합니다. 등록된 애플리케이션의 왼쪽 창에서 **인증**을 선택합니다.
1. **고급 설정**의 **암시적 허용**에서 **ID 토큰** 및 **액세스 토큰** 확인란을 선택합니다. 이 앱은 사용자를 로그인하고 API를 호출해야 하므로 ID 토큰 및 액세스 토큰이 필요합니다.
1. **저장**을 선택합니다.

> #### <a name="set-a-redirect-url-for-nodejs"></a>Node.js에 대한 리디렉션 URL 설정
> Node.js의 경우 *server.js* 파일에서 웹 서버 포트를 설정할 수 있습니다. 이 자습서는 참조를 위해 포트 30662를 사용하지만 사용 가능한 다른 포트도 사용할 수 있습니다. 
>
> 애플리케이션 등록 정보에 리디렉션 URL을 설정하려면, **애플리케이션 등록** 창으로 다시 전환하고 다음 중 하나를 수행합니다.
>
> - *`http://localhost:30662/`* 를 **리디렉션 URL**로 설정합니다.
> - 사용자 지정 TCP 포트를 사용하는 경우에는, *`http://localhost:<port>/`* (여기서 *\<port>* 는 사용자 지정 TCP 포트 번호임)를 사용합니다.
>
> #### <a name="set-a-redirect-url-for-visual-studio"></a>Visual Studio에 대한 리디렉션 URL 설정
> Visual Studio에 대한 리디렉션 URL를 가져오려면 다음을 수행합니다.
> 1. **솔루션 탐색기**에서 프로젝트를 선택합니다.
>
>    **속성** 창이 열립니다. 열리지 않으면 **F4**를 누릅니다.
>
>    ![JavaScriptSPA 프로젝트 속성 창](media/active-directory-develop-guidedsetup-javascriptspa-configure/vs-project-properties-screenshot.png)
>
> 1. **URL** 값을 복사합니다.
 
> 1. **애플리케이션 등록** 창으로 다시 전환한 후, 복사한 값을 **리디렉션 URL**로 붙여넣습니다.

#### <a name="configure-your-javascript-spa"></a>JavaScript SPA 구성

1. 프로젝트 설정 중에 생성한 *index.html* 파일에서 애플리케이션 등록 정보를 추가합니다. 파일 맨 위에서 `<script></script>` 태그 안에 다음 코드를 추가합니다.

    ```javascript
    var msalConfig = {
        auth: {
            clientId: "<Enter_the_Application_Id_here>",
            authority: "https://login.microsoftonline.com/<Enter_the_Tenant_info_here>"
        },
        cache: {
            cacheLocation: "localStorage",
            storeAuthStateInCookie: true
        }
    };
    ```

    위치:
    - *\<Enter_the_Application_Id_here>* 는 등록한 애플리케이션에 대한 **애플리케이션(클라이언트) ID**입니다.
    - *\<Enter_the_Tenant_info_here>* 는 다음 옵션 중 하나로 설정됩니다.
       - 애플리케이션이 *이 조직 디렉터리의 계정*을 지원하는 경우 이 값을 **테넌트 ID** 또는 **테넌트 이름**(예: *contoso.microsoft.com*)으로 바꿉니다.
       - 애플리케이션이 *모든 조직 디렉터리의 계정*을 지원하는 경우 이 값을 **organizations**로 바꿉니다.
       - 애플리케이션에서 *모든 조직 디렉터리의 계정 및 개인 Microsoft 계정*을 지원하는 경우 이 값을 **common**으로 바꿉니다. *개인 Microsoft 계정만* 지원하도록 제한하려면 이 값을 **consumers**로 바꿉니다.
