---
title: Mobile Engagement REST API를 사용한 인증 - 수동 설정
description: Mobile Engagement REST API에 대한 인증을 수동으로 설정하는 방법을 설명합니다.
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: ''
ms.assetid: 2e79f9c9-41e4-45ac-b427-3b8338675163
ms.service: mobile-engagement
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: mobile-multiple
ms.workload: mobile
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: 0d71908b34ddf8313aa45014420c9e63a00078c9
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2018
---
# <a name="authenticate-with-mobile-engagement-rest-apis---manual-setup"></a>Mobile Engagement REST API를 사용한 인증 - 수동 설치
> [!IMPORTANT]
> Azure Mobile Engagement는 2018/3/31에 사용이 중지됩니다. 이 페이지는 이후에 삭제됩니다.
> 

이 문서는 [Mobile Engagement REST API를 사용한 인증](mobile-engagement-api-authentication.md)에 대한 부록입니다. 전후 관계를 이해하려면 먼저 해당 문서를 참조해야 합니다. 또한 Azure Portal을 사용하여 Mobile Engagement REST API에 대한 일회성 인증 설정을 수행하는 다른 방법에 대해서도 설명합니다.

> [!NOTE]
> 다음 지침은 [이 Active Directory 가이드](../azure-resource-manager/resource-group-create-service-principal-portal.md)를 기반으로 하고 있습니다. Mobile Engagement API에 대한 인증 요구 사항에 맞게 사용자 지정됩니다. 다음 단계를 자세히 이해하려면 해당 항목을 참조하세요.

1. [Azure Portal](https://portal.azure.com/)을 통해 Azure 계정에 로그인합니다.
2. 왼쪽 창에서 **Active Directory** 를 선택합니다.

   ![Active Directory 선택][1]

3. 디렉터리에서 응용 프로그램을 보려면 **앱 등록**을 선택합니다.

   ![응용 프로그램 보기][3]

4. **새 응용 프로그램 등록**을 선택합니다.

   ![응용 프로그램 추가][4]

5. 응용 프로그램의 이름을 입력합니다. 응용 프로그램 유형은 **웹앱/API**로 그대로 두고 **다음** 단추를 선택합니다. **로그인 URL**에 대해 더미 URL을 제공할 수 있습니다. 이 시나리오에는 사용되지 않고 URL 자체의 유효성도 검사되지 않습니다.

   완료되면 제공한 이름의 Azure AD(Azure Active Directory) 앱이 있습니다. 여기서는 **AD\_APP\_NAME**이므로 적어 둡니다.

   ![앱 이름][8]

7. 앱 이름을 선택합니다.

8. **응용 프로그램 ID**를 찾아 적어둡니다. API 호출에 대한 **CLIENT\_ID**로 사용할 수 있는 클라이언트 ID입니다.

   ![응용 프로그램 ID 찾기][10]

9. 오른쪽에서 **키** 섹션을 찾습니다.

   ![키 섹션][11]

10. 새 키를 만들고 즉시 복사합니다. 다시 표시되지 않습니다.

    ![키 세부 정보가 있는 키 창][12]

    > [!IMPORTANT]
    > 이 키는 지정한 기간의 끝에서 만료됩니다. 시간이 되면 갱신해야 합니다. 그렇지 않으면 해당 API 인증이 더 이상 작동하지 않습니다. 이 키가 손상되었다고 생각되면 삭제하고 다시 만들 수 있습니다.
    >
    
11. 페이지 위쪽의 **엔드포인트** 단추를 선택합니다. 그런 다음 **OAuth 2.0 토큰 엔드포인트**를 복사합니다.

    ![엔드포인트 복사][14]

16. 이 엔드포인트는 `https://login.microsoftonline.com/<GUID>/oauth2/token` 형식이며, 여기서 URL의 GUID는 **TENANT_ID**입니다.

17. 다음으로, 이 앱에 대한 권한을 구성합니다. 프로세스를 시작하려면 [Azure Portal](https://portal.azure.com)로 이동합니다.

18. **리소스 그룹**을 선택한 다음, **MobileEngagement** 리소스 그룹을 찾습니다.

    ![MobileEngagement 찾기][15]

19. **MobileEngagement** 리소스 그룹을 선택한 다음, **모든 설정**을 선택합니다.

    ![MobileEngagement 설정 찾아보기][16]

20. **설정** 섹션에서 **사용자**를 선택합니다. 그런 다음 사용자를 추가하려면 **추가**를 선택합니다.

    ![사용자 추가][17]

21. **역할 선택**을 클릭합니다.

    ![역할 선택][18]

22. **소유자**를 선택합니다.

    ![역할로 소유자 선택][19]

23. 검색 상자에서 응용 프로그램의 이름(**AD\_APP\_NAME**)을 검색합니다. 여기에는 기본적으로 이 이름이 없습니다. 찾은 후에 선택합니다. 그런 다음 섹션 아래쪽의 **선택**을 클릭합니다.

    ![이름 선택][20]

24. **액세스 추가** 섹션에서 **1명 사용자, 0개 그룹**으로 표시됩니다. 변경 내용을 확인하려면 **확인**을 선택합니다.

    ![추가된 사용자 확인][21]

이제 필요한 Azure AD 구성이 완료되었으며, API를 호출하도록 완벽하게 설정되었습니다.

<!-- Images -->
[1]: ./media/mobile-engagement-api-authentication-manual/active-directory.png
[2]: ./media/mobile-engagement-api-authentication-manual/active-directory-details.png
[3]: ./media/mobile-engagement-api-authentication-manual/view-applications.png
[4]: ./media/mobile-engagement-api-authentication-manual/add-icon.png
[5]: ./media/mobile-engagement-api-authentication-manual/what-do-you-want-to-do.png
[6]: ./media/mobile-engagement-api-authentication-manual/tell-us-about-your-application.png
[7]: ./media/mobile-engagement-api-authentication-manual/app-properties.png
[8]: ./media/mobile-engagement-api-authentication-manual/aad-app.png
[9]: ./media/mobile-engagement-api-authentication-manual/configure-menu.png
[10]: ./media/mobile-engagement-api-authentication-manual/client-id.png
[11]: ./media/mobile-engagement-api-authentication-manual/client-secret.png
[12]: ./media/mobile-engagement-api-authentication-manual/keys.png
[13]: ./media/mobile-engagement-api-authentication-manual/view-endpoints.png
[14]: ./media/mobile-engagement-api-authentication-manual/app-endpoints.png
[15]: ./media/mobile-engagement-api-authentication-manual/resource-groups.png
[16]: ./media/mobile-engagement-api-authentication-manual/resource-groups-settings.png
[17]: ./media/mobile-engagement-api-authentication-manual/add-users.png
[18]: ./media/mobile-engagement-api-authentication-manual/add-role.png
[19]: ./media/mobile-engagement-api-authentication-manual/select-role.png
[20]: ./media/mobile-engagement-api-authentication-manual/add-user-select.png
[21]: ./media/mobile-engagement-api-authentication-manual/add-access-final.png



