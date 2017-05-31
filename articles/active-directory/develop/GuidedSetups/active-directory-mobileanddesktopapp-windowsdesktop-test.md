---
title: "Azure AD v2 Windows 데스크톱 시작 - 테스트 | Microsoft Docs"
description: "Windows Desktop .NET(XAML) 응용 프로그램이 Azure Active Directory v2 끝점으로 보호되는 액세스 토큰을 필요로 하는 API를 호출하는 방식"
services: active-directory
documentationcenter: dev-center-name
author: andretms
manager: mbaldwin
editor: 
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/09/2017
ms.author: andret
ms.custom: aaddev
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: bf8b96f7d7e73a978a66ed2f3dd307c348ef695f
ms.contentlocale: ko-kr
ms.lasthandoff: 05/10/2017


---
## <a name="test-your-code"></a>코드 테스트

응용 프로그램을 테스트하려면 `F5` 키를 눌러 Visual Studio에서 프로젝트를 실행합니다. 아래와 같이 주 창이 표시됩니다.

![샘플 스크린샷](media/active-directory-mobileanddesktopapp-windowsdesktop-test/samplescreenshot.png)

테스트할 준비가 되면 *Microsoft Graph API 호출*을 클릭하고 Microsoft Azure Active Directory(조직 계정) 또는 Microsoft 계정(live.com, outlook.com)을 사용해 로그인합니다. 처음인 경우 로그인하라는 창이 표시됩니다.

![로그인](media/active-directory-mobileanddesktopapp-windowsdesktop-test/signinscreenshot.png)

### <a name="consent"></a>동의
응용 프로그램에 처음으로 로그인하면 아래와 유사한 동의 화면이 표시됩니다. 여기서 명시적으로 수락해야 합니다.

![동의 화면](media/active-directory-mobileanddesktopapp-windowsdesktop-test/consentscreen.png)

### <a name="expected-results"></a>예상 결과
[API 호출 결과] 화면에 Microsoft Graph API 호출에서 반환된 사용자 프로필 정보가 표시됩니다.

[토큰 정보] 상자에 `AcquireTokenAsync` 또는 `AcquireTokenSilentAsync`를 통해 획득한 토큰에 대한 기본 정보가 표시됩니다.

|속성  |형식  |설명 |
|---------|---------|---------|
|이름 | {User Full name} |사용자의 이름과 성|
|사용자 이름 |<span>user@domain.com</span> |사용자를 식별하는 데 사용하는 사용자 이름|
|토큰 만료 |{DateTime}         |토큰이 만료되는 시간. MSAL에서는 필요한 경우 토큰을 갱신해 만료 날짜를 자동으로 연장합니다.|
|액세스 토큰 |{String}         |인증 헤더가 필요한 HTTP 요청으로 전송될 토큰 문자열|

<!--start-collapse-->
### <a name="more-information-about-scopes-and-delegated-permissions"></a>범위 및 위임된 권한에 대한 자세한 내용
사용자 프로필을 읽으려면 Graph API에는 `user.read` 범위가 필요합니다. 이 범위는 Microsoft 등록 포털에서 등록된 모든 응용 프로그램에서 기본적으로 자동 추가됩니다. 일부 다른 Graph API와 백 엔드 서버용 사용자 지정 API에는 추가 범위가 필요합니다. 예를 들어 Graph의 경우 사용자 일정을 나열하려면 `Calendars.Read`가 필요합니다. 응용 프로그램의 컨텍스트에서 사용자 일정에 액세스하려면 `Calendars.Read` 위임 응용 프로그램 등록 정보를 추가한 다음 `AcquireTokenAsync` 호출에 `Calendars.Read`를 추가해야 합니다. 범위 수를 늘리면 사용자에게 추가 동의를 요청하는 메시지가 표시될 수 있습니다.

백 엔드 API에 범위가 필요 없는 경우(권장되지 않음) `AcquireTokenAsync` 호출에서 `ClientId`를 범위로 사용할 수 있습니다.
<!--end-collapse-->




