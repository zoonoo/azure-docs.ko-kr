---
title: "Azure AD v2 JS SPA 단계별 설치 - 테스트 | Microsoft Docs"
description: "JavaScript SPA 응용 프로그램이 Azure Active Directory v2 끝점으로 보호되는 액세스 토큰을 필요로 하는 API를 호출하는 방식"
services: active-directory
documentationcenter: dev-center-name
author: andretms
manager: mbaldwin
editor: 
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 06/01/2017
ms.author: andret
ms.translationtype: HT
ms.sourcegitcommit: 847eb792064bd0ee7d50163f35cd2e0368324203
ms.openlocfilehash: c559c80781da3631a783d96539622c4c89fe7e17
ms.contentlocale: ko-kr
ms.lasthandoff: 08/19/2017

---
## <a name="test-your-code"></a>코드 테스트

> ### <a name="testing-with-visual-studio"></a>Visual Studio로 테스트
> Visual Studio를 사용하는 경우 `F5` 키를 눌러 프로젝트를 실행합니다. 브라우저가 열리고 *http://localhost:{port}* 로 이동됩니다. 여기서 *Microsoft Graph API 호출* 단추가 표시됩니다.

<p/><!-- -->

> ### <a name="testing-with-python-or-another-web-server"></a>Python 또는 다른 웹 서버를 사용하여 테스트
> Visual Studio를 사용하지 않는 경우에는 웹 서버가 시작되었는지와 *index.html* 파일을 포함하는 폴더를 기준으로 TCP 포트에서 수신 대기하도록 구성되어 있는지 확인합니다. Python의 경우 해당 앱의 폴더에서 prompt/ terminal 명령을 실행하여 포트에서 수신 대기할 수 있습니다.
> 
> ```bash
> python -m http.server 8080
> ```
>  그런 후 브라우저를 열고 *http://localhost:8080* 또는 *http://localhost:{port}*를 입력합니다. 여기서 *port*는 웹 서버가 수신 대기 중인 포트입니다. *Microsoft Graph API 호출* 단추를 사용하여 index.html 페이지의 내용을 확인하는 것이 좋습니다.

## <a name="test-your-application"></a>응용 프로그램 테스트

브라우저에서 *index.html*이 로드되면 *Microsoft Graph API 호출* 단추를 클릭합니다. 이번이 처음이면 브라우저는 로그인하라는 메시지를 표시하는 Microsoft Azure Active Directory v2 끝점으로 리디렉션됩니다.
 
![샘플 스크린샷](media/active-directory-singlepageapp-javascriptspa-test/javascriptspascreenshot1.png)


### <a name="consent"></a>동의
응용 프로그램에 처음으로 로그인하면 다음과 유사한 동의 화면이 표시됩니다. 여기서 동의해야 합니다.

 ![동의 화면](media/active-directory-singlepageapp-javascriptspa-test/javascriptspaconsent.png)


### <a name="expected-results"></a>예상 결과
Microsoft Graph API 호출에서 반환된 사용자 프로필 정보가 표시되어야 합니다.
 
 ![결과](media/active-directory-singlepageapp-javascriptspa-test/javascriptsparesults.png)

*액세스 토큰* 및 *ID 토큰 클레임* 상자에서 가져온 토큰에 대한 기본 정보도 볼 수 있습니다.

<!--start-collapse-->
### <a name="more-information-about-scopes-and-delegated-permissions"></a>범위 및 위임된 권한에 대한 자세한 내용

Microsoft Graph API는 `user.read` 범위가 있어야만 사용자 프로필을 읽을 수 있습니다. 이 범위는 Microsoft 등록 포털에서 등록된 모든 응용 프로그램에서 기본적으로 자동 추가됩니다. 일부 다른 Microsoft Graph용 API와 백 엔드 서버용 사용자 지정 API에는 추가 범위가 필요할 수 있습니다. 예를 들어 Microsoft Graph의 경우 사용자 일정을 나열하려면 `Calendars.Read` 범위가 필요합니다. 응용 프로그램의 컨텍스트에서 사용자 일정에 액세스하려면 응용 프로그램 등록 정보에 `Calendars.Read` 위임 권한을 추가한 다음 `acquireTokenSilent` 호출에 `Calendars.Read` 범위를 추가해야 합니다. 범위 수를 늘리면 사용자에게 추가 동의를 요청하는 메시지가 표시될 수 있습니다.

백 엔드 API에 범위가 필요 없는 경우(권장되지 않음) `acquireTokenSilent` 및/또는 `acquireTokenRedirect` 호출에서 `clientId`를 범위로 사용할 수 있습니다.

<!--end-collapse-->

