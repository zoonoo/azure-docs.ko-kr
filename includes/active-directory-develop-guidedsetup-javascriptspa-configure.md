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
ms.openlocfilehash: ec9eba4766da1afbbee568374de1ce06dc92ab2b
ms.sourcegitcommit: dec7947393fc25c7a8247a35e562362e3600552f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2019
ms.locfileid: "58203327"
---
## <a name="register-your-application"></a>애플리케이션 등록

1. [Azure Portal](https://portal.azure.com/)에 로그인하여 애플리케이션을 등록합니다.
1. 계정이 둘 이상의 테넌트에 대해 액세스를 제공하는 경우 오른쪽 위 모서리에 있는 계정을 선택하여 원하는 Azure AD 테넌트로 포털 세션을 설정합니다.
1. 왼쪽 탐색 창에서 **Azure Active Directory** 서비스, **앱 등록(미리 보기) > 새 등록**을 차례로 선택합니다.
1. **애플리케이션 등록** 페이지가 나타나면 애플리케이션의 이름을 입력합니다.
1. **지원되는 계정 유형** 아래에서 **모든 조직 디렉터리의 계정 및 개인 Microsoft 계정**을 선택합니다.
1. **리디렉션 URI** 섹션에서 **웹** 플랫폼을 선택한 다음, 사용하는 웹 서버에 맞게 애플리케이션의 URL로 값을 설정합니다. Visual Studio 및 Node에서 리디렉션 URL을 설정 및 가져오는 방법에 대한 지침은 아래 섹션을 참조하세요.
1. 작업을 마쳤으면 **등록**을 선택합니다.
1. 앱 **개요** 페이지에서 **애플리케이션(클라이언트) ID** 값을 기록해 둡니다.
1. 이 빠른 시작에서는 [암시적 허용 흐름](../articles/active-directory/develop/v2-oauth2-implicit-grant-flow.md)을 사용하도록 설정해야 합니다. 등록된 애플리케이션의 왼쪽 탐색 창에서 **인증**을 선택합니다.
1. **고급 설정**의 **암시적 허용**에서 **ID 토큰** 및 **액세스 토큰** 확인란을 둘 다 사용하도록 설정합니다. 이 앱은 사용자를 로그인하고 API를 호출해야 하므로 ID 토큰 및 액세스 토큰이 필요합니다.
1. **저장**을 선택합니다.

> #### <a name="setting-the-redirect-url-for-node"></a>Node의 리디렉션 URL 설정
> Node.js의 경우 *server.js* 파일에서 웹 서버 포트를 설정할 수 있습니다. 이 자습서는 참조를 위해 포트 30662를 사용하지만 사용 가능한 다른 포트도 사용할 수 있습니다. 아래 지침에 따라 애플리케이션 등록 정보에 리디렉션 URL을 설정합니다.<br/>
> - *애플리케이션 등록*으로 돌아가서 `http://localhost:30662/`을 `Redirect URL`로 설정하거나, 사용자 지정 TCP 포트를 사용하는 경우(여기서 *[포트]* 는 사용자 지정 TCP 포트 번호) `http://localhost:[port]/`를 사용합니다.

<p/>

> #### <a name="visual-studio-instructions-for-obtaining-the-redirect-url"></a>리디렉션 URL을 얻기 위한 Visual Studio 지침
> 리디렉션 URL을 가져오려면 다음 단계를 수행하세요.
> 1. **솔루션 탐색기**에서 프로젝트를 선택하고 **속성** 창을 확인합니다. **속성** 창이 보이지 않으면 **F4** 키를 누릅니다.
> 2. **URL**의 값을 클립보드로 복사합니다.<br/> ![](media/active-directory-develop-guidedsetup-javascriptspa-configure/vs-project-properties-screenshot.png)<br />
> 3. *애플리케이션 등록*으로 돌아가서 값을 **리디렉션 URL**로 설정합니다.

#### <a name="configure-your-javascript-spa"></a>JavaScript SPA 구성

1. 프로젝트 설정 중에 생성된 `index.html` 파일에서 애플리케이션 등록 정보를 추가합니다. 맨 위에 있는 다음 코드를 `index.html` 파일 본문의 `<script></script>` 태그 내에 추가합니다.

    ```javascript
    var applicationConfig = {
        clientID: "Enter_the_Application_Id_here",
        authority: "https://login.microsoftonline.com/common",
        graphScopes: ["user.read"],
        graphEndpoint: "https://graph.microsoft.com/v1.0/me"
    };
    ```

<ol start="2">
<li>
<code>Enter the application Id here</code>를 방금 등록한 애플리케이션 ID로 바꿉니다.
</li>
</ol>
