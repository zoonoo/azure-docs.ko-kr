---
title: 포함 파일
description: 포함 파일
services: active-directory
documentationcenter: dev-center-name
author: navyasric
manager: mtillman
editor: ''
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/17/2018
ms.author: nacanuma
ms.custom: include file
ms.openlocfilehash: 07aac49e7aed7c95863a2058a9de3d1e8f2cd1ad
ms.sourcegitcommit: 715813af8cde40407bd3332dd922a918de46a91a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2018
ms.locfileid: "47060817"
---
## <a name="register-your-application"></a>응용 프로그램 등록

여러 가지 방법으로 응용 프로그램을 만들 수 있습니다. 하나를 선택하세요.

### <a name="option-1-register-your-application-express-mode"></a>옵션 1: 응용 프로그램 등록(기본 모드)
이제 *Microsoft 응용 프로그램 등록 포털*에서 등용 프로그램을 등록해야 합니다.

1.  [Microsoft 응용 프로그램 등록 포털](https://apps.dev.microsoft.com/portal/register-app?appType=singlePageApp&appTech=javascriptSpa&step=configure)을 통해 응용 프로그램을 등록합니다.
2.  응용 프로그램 이름과 이메일을 입력합니다.
3.  **단계별 설정** 옵션이 선택되어 있는지 확인합니다.
4.  지침에 따라 응용 프로그램 ID를 가져와 코드에 붙여넣습니다.

### <a name="option-2-register-your-application-advanced-mode"></a>옵션 2: 응용 프로그램 등록(고급 모드)

1. [Microsoft 응용 프로그램 등록 포털](https://apps.dev.microsoft.com/portal/register-app)로 이동하여 응용 프로그램을 등록합니다.
2. 응용 프로그램 이름과 이메일을 입력합니다.
3. **단계별 설정** 옵션이 선택 취소되어 있는지 확인합니다.
4.  **플랫폼 추가**를 클릭한 다음, **웹**을 선택합니다.
5. 웹 서버에 따라 응용 프로그램의 URL에 해당하는 **리디렉션 URL**을 추가합니다. Visual Studio 및 Node에서 리디렉션 URL을 설정 및 가져오는 방법에 대한 지침은 아래 섹션을 참조하세요.
6. **저장**을 선택합니다.

> #### <a name="setting-redirect-url-for-node"></a>Node에 대한 리디렉션 URL 설정
> Node.js의 경우 *server.js* 파일에서 웹 서버 포트를 설정할 수 있습니다. 이 자습서는 참조를 위해 포트 30662를 사용하지만 다른 포트도 자유롭게 사용 가능합니다. 어떤 경우든, 아래 지침에 따라 응용 프로그램 등록 정보에 리디렉션 URL을 설정합니다.<br/>
> - *응용 프로그램 등록 포털*로 돌아가서 `http://localhost:30662/`을 `Redirect URL`로 설정하거나 사용자 지정 TCP 포트를 사용하는 경우(여기서 *[포트]* 는 사용자 지정 TCP 포트 번호) `http://localhost:[port]/`를 사용하고 '저장'을 클릭합니다.

<p/>

> #### <a name="visual-studio-instructions-for-obtaining-the-redirect-url"></a>리디렉션 URL을 얻기 위한 Visual Studio 지침
> 리디렉션 URL을 가져오려면 다음 단계를 수행하세요.
> 1.    **솔루션 탐색기**에서 프로젝트를 선택하고 **속성** 창을 확인합니다. **속성** 창이 보이지 않으면 **F4** 키를 누릅니다.
> 2.    **URL**의 값을 클립보드로 복사합니다.<br/> ![](media/active-directory-develop-guidedsetup-javascriptspa-configure/vs-project-properties-screenshot.png)<br />
> 3.    *응용 프로그램 등록 포털*로 다시 돌아간 후 값을 **리디렉션 URL**로 붙여넣고 **저장**을 선택합니다.


#### <a name="configure-your-javascript-spa"></a>JavaScript SPA 구성

1.  프로젝트 설정 중에 생성된 `index.html` 파일에서 응용 프로그램 등록 정보를 추가합니다. 맨 위에 있는 다음 코드를 `index.html` 파일 본문의 `<script></script>` 태그 내에 추가합니다.

```javascript
var applicationConfig = {
    clientID: "[Enter the application Id here]",
    graphScopes: ["user.read"],
    graphEndpoint: "https://graph.microsoft.com/v1.0/me"
};
```
<ol start="3">
<li>
<code>Enter the application Id here</code>를 방금 등록한 응용 프로그램 ID로 바꿉니다.
</li>
</ol>
