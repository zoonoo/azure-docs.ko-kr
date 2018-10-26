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
ms.date: 09/13/2018
ms.author: andret
ms.custom: include file
ms.openlocfilehash: e87a63a0dad5e1f93b1bea62039abee8ded78ab7
ms.sourcegitcommit: c2c279cb2cbc0bc268b38fbd900f1bac2fd0e88f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/24/2018
ms.locfileid: "49988342"
---
## <a name="test-your-app"></a>앱 테스트

1. 장치/에뮬레이터로 코드를 실행하는 방법을 설명합니다.
2. Azure Active Directory 계정(회사 또는 학교 계정) 또는 Microsoft 계정(live.com, outlook.com)을 사용하여 로그인해 봅니다. 

    ![응용 프로그램 테스트](media/active-directory-develop-guidedsetup-android-test/mainwindow.png)
    <br/><br/>
    ![사용자 이름 및 암호 입력](media/active-directory-develop-guidedsetup-android-test/usernameandpassword.png)

### <a name="consent-to-your-app"></a>앱 사용 권한 동의

사용자가 응용 프로그램에 처음 로그인하면 다음과 같이 앱에 필요한 권한 제공에 동의하라는 메시지가 표시됩니다. 

![응용 프로그램 액세스에 대한 사용자 동의 제공](media/active-directory-develop-guidedsetup-android-test/androidconsent.png)

### <a name="success"></a>성공!

로그인하여 동의하고 나면 앱에 Microsoft Graph API의 응답이 표시됩니다. 이 특정 호출에서는 **/me** 엔드포인트를 호출하며 [사용자 프로필](https://developer.microsoft.com/graph/docs/api-reference/v1.0/api/user_get)을 반환합니다. 다른 Microsoft Graph 엔드포인트 목록은 [Microsoft Graph API 개발자 설명서](https://developer.microsoft.com/graph/docs#common-microsoft-graph-queries)를 참조하세요.

<!--start-collapse-->
### <a name="scopes-and-delegated-permissions"></a>범위 및 위임된 권한

Microsoft Graph API에서 사용자 프로필을 읽으려면 *User.Read* 범위가 필요합니다. 이 범위는 응용 프로그램 등록 포털에서 등록된 모든 앱에 자동으로 포함됩니다. 다른 API에는 추가 범위가 필요합니다. 예를 들어 Microsoft Graph API에서 사용자 일정을 나열하려면 *Calendars.Read* 범위가 있어야 합니다.

사용자 일정에 액세스하려면 응용 프로그램 등록 정보에 *Calendars.Read* 위임 권한을 추가합니다. 그런 다음 `acquireTokenSilent` 호출에 *Calendars.Read* 범위를 추가합니다. 

> [!NOTE]
> 앱 등록을 변경하면 사용자에게 추가 동의를 요청하는 메시지가 표시될 수 있습니다.

<!--end-collapse-->

[!INCLUDE [Help and support](active-directory-develop-help-support-include.md)]
