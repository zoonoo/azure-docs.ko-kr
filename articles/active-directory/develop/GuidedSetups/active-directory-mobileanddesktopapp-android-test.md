---
title: "Azure AD v2 Android 시작 - 테스트 | Microsoft Docs"
description: "Android 앱이 액세스 토큰을 얻고 Azure Active Directory v2 끝점에서 액세스 토큰이 필요한 Microsoft Graph API를 한 개 이상 호출할 수 있는 방법"
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
ms.sourcegitcommit: ef74361c7a15b0eb7dad1f6ee03f8df707a7c05e
ms.openlocfilehash: 8523f46d6a352c9a6625ddeacc5abe2b4bbf977e
ms.contentlocale: ko-kr
ms.lasthandoff: 07/06/2017


---
## <a name="test-your-code"></a>코드 테스트

1. 장치/에뮬레이터에 코드를 배포합니다.
2. 테스트할 준비가 되면 Microsoft Azure Active Directory(조직 계정) 또는 Microsoft 계정(live.com, outlook.com)을 사용해 로그인합니다. 

![샘플 스크린샷](media/active-directory-mobileanddesktopapp-android-test/mainwindow.png)
<br/><br/>
![로그인](media/active-directory-mobileanddesktopapp-android-test/usernameandpassword.png)

### <a name="consent"></a>동의
사용자가 응용 프로그램에 처음으로 로그인하면 아래와 유사한 동의 화면이 표시됩니다. 사용자는 여기서 명시적으로 수락해야 합니다. 

![동의](media/active-directory-mobileanddesktopapp-android-test/androidconsent.png)


### <a name="expected-results"></a>예상 결과
사용자 프로필 https://graph.microsoft.com/v1.0/me를 얻는 데 사용된 Microsoft Graph API ‘me’ 끝점에 대한 호출 결과가 표시되어야 합니다. 일반적인 Microsoft Graph 끝점 목록은 이 [문서](https://developer.microsoft.com/graph/docs#common-microsoft-graph-queries)를 참조하세요.

<!--start-collapse-->
### <a name="more-information-about-scopes-and-delegated-permissions"></a>범위 및 위임된 권한에 대한 자세한 내용
사용자 프로필을 읽으려면 Graph API에는 `user.read` 범위가 필요합니다. 이 범위는 Microsoft 등록 포털을 통해 등록된 모든 응용 프로그램에 기본적으로 추가됩니다. 일부 다른 Graph API와 백 엔드 서버용 사용자 지정 API에는 추가 범위가 필요합니다. 예를 들어 Graph의 경우 사용자 일정을 나열하려면 `Calendars.Read`가 필요합니다. 응용 프로그램의 컨텍스트에서 사용자 일정에 액세스하려면 위임된 응용 프로그램 등록 정보를 추가한 다음 `AcquireTokenAsync` 호출에 `Calendars.Read`를 추가해야 합니다. 범위 수를 늘리면 사용자에게 추가 동의를 요청하는 메시지가 표시될 수 있습니다.

백 엔드 API에 범위가 필요 없는 경우(권장되지 않음) `AcquireTokenAsync` 호출에서 `ClientId`를 범위로 사용할 수 있습니다.
<!--end-collapse-->

