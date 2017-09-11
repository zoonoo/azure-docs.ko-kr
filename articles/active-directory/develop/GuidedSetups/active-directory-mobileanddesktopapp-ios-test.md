---
title: "Azure AD v2 iOS 시작 - 테스트 | Microsoft Docs"
description: "iOS(Swift) 응용 프로그램이 Azure Active Directory v2 끝점으로 보호되는 액세스 토큰을 필요로 하는 API를 호출하는 방식"
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
ms.date: 05/09/2017
ms.author: andret
ms.translationtype: HT
ms.sourcegitcommit: b309108b4edaf5d1b198393aa44f55fc6aca231e
ms.openlocfilehash: 2420723eed9ae8ba1ba6156482ef4b4de9ce2fc0
ms.contentlocale: ko-kr
ms.lasthandoff: 08/15/2017

---
## <a name="test-querying-the-microsoft-graph-api-from-your-ios-application"></a>iOS 응용 프로그램에서 Microsoft Graph API 쿼리 테스트

`Command` + `R`를 눌러 시뮬레이터에서 코드를 실행합니다.

![샘플 스크린샷](media/active-directory-mobileanddesktopapp-ios-test/iostestscreenshot.png)

테스트할 준비가 되면 *'Microsoft Graph API 호출'*을 누릅니다. 그러면 사용자 이름 및 암호를 입력하라는 메시지가 표시됩니다.

### <a name="consent"></a>동의
응용 프로그램에 처음으로 로그인하면 아래와 유사한 동의 화면이 표시됩니다. 여기서 명시적으로 수락해야 합니다.

![동의 화면](media/active-directory-mobileanddesktopapp-ios-test/iosconsentscreen.png)

### <a name="expected-results"></a>예상 결과
*로깅* 섹션에 Microsoft Graph API 호출에서 반환된 사용자 프로필 정보가 표시되어야 합니다.

<!--start-collapse-->
### <a name="more-information-about-scopes-and-delegated-permissions"></a>범위 및 위임된 권한에 대한 자세한 내용

Microsoft Graph API는 `user.read` 범위가 있어야만 사용자 프로필을 읽을 수 있습니다. 이 범위는 Microsoft 등록 포털에서 등록된 모든 응용 프로그램에서 기본적으로 자동 추가됩니다. 일부 다른 Microsoft Graph용 API와 백 엔드 서버용 사용자 지정 API에는 추가 범위가 필요할 수 있습니다. 예를 들어 Microsoft Graph의 경우 사용자 일정을 나열하려면 `Calendars.Read` 범위가 필요합니다. 응용 프로그램의 컨텍스트에서 사용자 일정에 액세스하려면 응용 프로그램 등록 정보에 `Calendars.Read` 위임 권한을 추가한 다음 `acquireTokenSilent` 호출에 `Calendars.Read` 범위를 추가해야 합니다. 범위 수를 늘리면 사용자에게 추가 동의를 요청하는 메시지가 표시될 수 있습니다.

<!--end-collapse-->




