---
title: "Mobile Engagement REST API를 사용한 인증 - 수동 설치"
description: "Mobile Engagement REST API에 대한 인증을 수동으로 설정하는 방법을 설명합니다."
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: 
ms.assetid: 2e79f9c9-41e4-45ac-b427-3b8338675163
ms.service: mobile-engagement
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: mobile-multiple
ms.workload: mobile
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: 9d6132e1a01be489b8e8e28a0219cf8a0b50b318
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/11/2017
---
# <a name="authenticate-with-mobile-engagement-rest-apis---manual-setup"></a>Mobile Engagement REST API를 사용한 인증 - 수동 설치
이 부록 설명서는 [Mobile Engagement REST API를 사용한 인증](mobile-engagement-api-authentication.md)을 설명합니다. 먼저 읽고 상황을 파악하도록 합니다. Azure 포털을 사용하여 Mobile Engagement REST API에 대한 인증을 설정하기 위해 일회성 설치 프로세스를 수행하는 대체 방법을 설명합니다. 

> [!NOTE]
> 아래 지침은 [Active Directory 가이드](../azure-resource-manager/resource-group-create-service-principal-portal.md) 를 기반으로 하며 Mobile Engagement API에 대한 인증을 위해 필요한 항목을 사용자 지정합니다. 따라서 아래 단계를 자세히 이해하려면 참조합니다. 
> 
> 

1. [클래식 포털](https://manage.windowsazure.com/)을 통해 Azure 계정에 로그인합니다.
2. 왼쪽 창에서 **Active Directory** 를 선택합니다.
   
     ![Active Directory 선택][1]
3. Azure 포털에서 **기본 Active Directory** 를 선택합니다. 
   
     ![디렉터리 선택][2]
   
   > [!IMPORTANT]
   > 이 방법은 사용자 계정의 기본 Active Directory에서 작업할 경우 작동하며 사용자가 계정에 만든 Active Directory에서 수행하는 경우 작동하지 않습니다. 
   > 
   > 
4. 디렉터리에서 응용 프로그램을 보려면 **응용 프로그램**을 클릭합니다.
   
     ![응용 프로그램 보기][3]
5. **추가**를 클릭합니다. 
   
     ![응용 프로그램 추가][4]
6. **내 조직에서 개발 중인 응용 프로그램 추가**
   
     ![새 응용 프로그램][5]
7. 응용 프로그램의 이름을 입력하고 응용 프로그램의 유형을 **웹 응용 프로그램 및/또는 웹 API** 로 선택하고 다음 단추를 클릭합니다.
   
     ![응용 프로그램 이름 지정][6]
8. **로그인 URL** 및 **앱 ID URI**에 대한 더미 URL을 제공할 수 있습니다. 이것은 시나리오에 사용되지 않고 URL 자체의 유효성도 검사되지 않습니다.  
   
     ![응용 프로그램 속성][7]
9. 이 단계가 끝나면 다음과 같이 이전에 제공한 이름을 가진 AAD 앱이 있어야 합니다. 이것이 **AD\_APP\_NAME**이며 메모해 둡니다.  
   
     ![앱 이름][8]
10. 앱 이름을 클릭하고 **구성**을 클릭합니다.
    
      ![앱 구성][9]
11. API 호출에 **CLIENT\_ID**로 사용할 수 있는 클라이언트 ID를 메모해 둡니다. 
    
     ![앱 구성][10]
12. **키** 섹션으로 스크롤하고 가급적이면 2년(만료) 기간인 키를 추가하고 **저장**을 클릭합니다. 
    
     ![앱 구성][11]
13. 지금 표시되고 저장되지 않아서 다시 표시되지 않으므로 표시된 키에 대한 값을 즉시 복사합니다. 분실한 경우 새 키를 생성해야 합니다. API 호출에 대한 **CLIENT_SECRET**입니다. 
    
     ![앱 구성][12]
    
    > [!IMPORTANT]
    > 이 키는 지정한 기간 후에 만료 됩니다. 따라서 기간이 되어 갱신하지 않으면 API 인증은 더 이상 작동하지 않습니다.  또한 손상되었다고 생각하는 경우 이 키를 삭제하고 다시 만들 수 있습니다.
    > 
    > 
14. **끝점 보기** 단추를 클릭하면 **앱 끝점** 대화 상자가 열립니다. 
    
    ![][13]
15. 앱 끝점 대화 상자에서 **OAUTH 2.0 토큰 끝점**을 복사합니다. 
    
    ![][14]
16. 이 끝점은 URL의 GUID가 **TENANT_ID**인 다음과 같은 형식입니다. 기록해 둡니다. 
    
        https://login.microsoftonline.com/<GUID>/oauth2/token
17. 이제 이 앱에 권한을 구성하도록 진행합니다. 이를 위해 [Azure 포털](https://portal.azure.com)을 열어야 합니다. 
18. **리소스 그룹**을 열고 **Mobile Engagement** 리소스 그룹을 찾습니다.  
    
    ![][15]
19. **Mobile Engagement** 리소스 그룹을 찾고 거기서 **설정** 블레이드로 이동합니다. 
    
    ![][16]
20. 설정 블레이드에서 **사용자**를 클릭하고 **추가**를 클릭하여 사용자를 추가합니다. 
    
    ![][17]
21. **역할 선택**
    
    ![][18]
22. **소유자**
    
    ![][19]
23. 검색 상자에서 응용 프로그램 **AD\_APP\_NAME**의 이름을 검색합니다. 여기에 기본적으로 표시되지 않습니다. 찾게 되면 선택하고 블레이드 맨 아래에서 **선택** 을 클릭합니다. 
    
    ![][20]
24. **액세스 추가** 블레이드에서 **1 사용자, 0 그룹**으로 표시됩니다. 이 블레이드에서 **확인** 을 클릭하여 변경 내용을 확인합니다. 
    
    ![][21]

필요한 AAD 구성을 완료했으므로 API를 호출하도록 설정되었습니다. 

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
[11]: ./media/mobile-engagement-api-authentication-manual/client_secret.png
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



