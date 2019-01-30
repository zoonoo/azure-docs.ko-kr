---
title: 포함 파일
description: 포함 파일
services: active-directory
documentationcenter: dev-center-name
author: navyasric
manager: mtillman
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/17/2018
ms.author: nacanuma
ms.custom: include file
ms.openlocfilehash: 0c4908315bd7c0e4d83c7aba735cc4d102216a28
ms.sourcegitcommit: c2c279cb2cbc0bc268b38fbd900f1bac2fd0e88f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/24/2018
ms.locfileid: "49988436"
---
## <a name="test-your-code"></a>코드 테스트

### <a name="test-with-node"></a>Node를 사용하여 테스트

Visual Studio를 사용하지 않는 경우 웹 서버가 시작되었는지 확인합니다.

1. **index.html** 파일의 위치를 기반으로 하는 TCP 포트에서 수신 대기하도록 서버를 구성합니다. Node의 경우 애플리케이션 폴더의 명령줄 프롬프트에서 다음 명령을 실행하여 포트를 수신 대기하도록 웹 서버를 시작합니다.

    ```bash
    npm install
    node server.js
    ```
1. 브라우저를 열고 http://<span></span>localhost:30662 또는 http://<span></span>localhost:{port}를 입력합니다. 여기서 **port**는 웹 서버가 수신 대기 중인 포트입니다. index.html 파일과 **로그인** 단추의 내용을 확인해야 합니다.

<p/><!-- -->

### <a name="test-with-visual-studio"></a>Visual Studio를 사용한 테스트

Visual Studio를 사용하는 경우 프로젝트 솔루션을 선택하고 **F5** 키를 눌러 프로젝트를 실행합니다. 브라우저가 열리고 http://<span></span>localhost:{port} 위치로 이동합니다. **로그인** 단추가 표시됩니다.

## <a name="test-your-application"></a>애플리케이션 테스트

브라우저에서 index.html 파일이 로드되면 **로그인**을 클릭합니다. Microsoft Azure AD(Azure Active Directory) v2.0 엔드포인트로 로그인하라는 메시지가 표시됩니다.

![JavaScript SPA 계정에 로그인](media/active-directory-develop-guidedsetup-javascriptspa-test/javascriptspascreenshot1.png)

### <a name="provide-consent-for-application-access"></a>애플리케이션 액세스에 대한 동의 제공

애플리케이션에 처음으로 로그인하면 애플리케이션이 사용자 프로필에 액세스하고 로그인할 수 있도록 동의하라는 메시지가 표시됩니다.

![애플리케이션 액세스에 대한 사용자 동의 제공](media/active-directory-develop-guidedsetup-javascriptspa-test/javascriptspaconsent.png)

### <a name="view-application-results"></a>애플리케이션 결과 보기

로그인하면 페이지에 표시된 Microsoft Graph API 응답에 사용자 프로필 정보가 반환된 것을 확인할 수 있습니다.

![Microsoft Graph API 호출로부터 예상된 결과](media/active-directory-develop-guidedsetup-javascriptspa-test/javascriptsparesults.png)

<!--start-collapse-->
### <a name="more-information-about-scopes-and-delegated-permissions"></a>범위 및 위임된 권한에 대한 자세한 내용

Microsoft Graph API는 `user.read` 범위가 있어야만 사용자 프로필을 읽을 수 있습니다. 이 범위는 등록 포털에서 등록된 모든 애플리케이션에서 기본적으로 자동 추가됩니다. 다른 Microsoft Graph용 API와 백 엔드 서버용 사용자 지정 API에는 추가 범위가 필요할 수 있습니다. 예를 들어 Microsoft Graph API는 `Calendars.Read` 범위가 있어야만 사용자 일정을 나열할 수 있습니다.

애플리케이션의 컨텍스트에서 사용자 일정에 액세스하려면 애플리케이션 등록 정보에 `Calendars.Read` 위임 권한을 추가합니다. 그런 다음, `Calendars.Read` 범위를 `acquireTokenSilent` 호출에 추가합니다.

>[!NOTE]
>범위 수를 늘리면 사용자에게 추가 동의를 요청하는 메시지가 표시될 수 있습니다.

백 엔드 API에 범위가 필요 없는 경우(권장되지 않음) 호출에서 `clientId`를 범위로 사용하여 토큰을 획득할 수 있습니다.

<!--end-collapse-->

[!INCLUDE [Help and support](./active-directory-develop-help-support-include.md)]