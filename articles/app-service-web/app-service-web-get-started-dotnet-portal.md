---
title: "Umbraco 웹앱을 Azure Portal에&5;분 안에 배포 | Microsoft Docs"
description: "샘플 ASP.NET 앱을 배포하여 App Service에서 웹앱을 실행하는 작업이 얼마나 쉬운지 알아봅니다. 결과를 즉시 확인합니다."
services: app-service\web
documentationcenter: 
author: cephalin
manager: erikre
editor: 
ms.assetid: b1e6bd58-48d1-4007-9d6c-53fd6db061e3
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 02/10/2017
ms.author: cephalin
translationtype: Human Translation
ms.sourcegitcommit: 0921b01bc930f633f39aba07b7899ad60bd6a234
ms.openlocfilehash: fa3f31cdd708729071876ffad707bea70567da83
ms.lasthandoff: 03/01/2017


---
# <a name="deploy-an-umbraco-web-app-in-the-azure-portal-in-five-minutes"></a>Umbraco 웹앱을 Azure Portal에&5;분 안에 배포

이 자습서는 [Umbraco](https://our.umbraco.org/) 웹앱을 [Azure App Service](../app-service/app-service-value-prop-what-is.md)에 몇 분 이내에 배포하는 데 도움이 됩니다.

![Umbraco 앱](./media/app-service-web-get-started-dotnet-portal/defaultpage.png)

## <a name="prerequisites"></a>필수 조건
Microsoft Azure 계정이 있어야 합니다. 계정이 없는 경우 [무료 평가판을 등록](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F)하거나 [Visual Studio 구독자 혜택을 활성화](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F)할 수 있습니다.

> [!NOTE]
> Azure 계정 없이 [App Service를 체험](https://azure.microsoft.com/try/app-service/)할 수 있습니다. 시작 앱을 만들고 최대 한 시간 동안 해당 앱을 사용하여 재생합니다. -- 신용 카드는 필요하지 않으며 약정도 필요하지 않습니다.
> 
> 

## <a name="deploy-the-aspnet-app"></a>ASP.NET 앱 배포
1. [Azure 포털](https://portal.azure.com)에 로그인합니다.

2. [https://portal.azure.com/#create/umbracoorg.UmbracoCMS](https://portal.azure.com/#create/umbracoorg.UmbracoCMS)를 엽니다.

    이 링크는 새 Umbraco 앱을 Azure Portal에서 즉시 구성할 수 있는 바로 가기입니다.

3. **앱 이름**에 웹앱 이름을 입력합니다. 이름이 `azurewebsites.net` 도메인에서 고유하면 녹색 확인 표시가 상자에 표시됩니다.
   
5. **리소스 그룹**에서 **새로 만들기**를 클릭하여 새 [리소스 그룹](../azure-resource-manager/resource-group-overview.md)을 만든 다음 이름을 지정합니다.

7. **App Service 계획/위치** > **새로 만들기**를 클릭합니다. [App Service 계획](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md)을 다음과 같이 구성합니다.

    - **App Service 계획**에 원하는 이름을 입력합니다.
    - **위치**에서 계획을 호스트할 위치를 선택합니다.
    - **가격 책정 계층**을 클릭한 다음 **F1 무료** 또는 다른 적합한 계층을 선택한 다음 **선택**을 클릭합니다.
    - **확인**을 클릭합니다.

    Umbraco CMS 구성이 다음 스크린샷과 같은 모양으로 표시됩니다.

    ![구성 진행 중 - Azure App Service의 첫 번째 Umbraco](./media/app-service-web-get-started-dotnet-portal/configure-in-progress.png)

12. **SQL Database** > **새 데이터베이스 만들기**를 클릭합니다. SQL Database를 다음과 같이 구성합니다.

    - **이름**에 이름(예: **myDB**)을 입력합니다.
    - **가격 책정 계층**을 클릭한 다음 **F 무료** 또는 다른 적합한 계층을 선택한 다음 **선택**을 클릭합니다.
    - **대상 서버** > **새 서버 만들기**를 클릭합니다. 데이터베이스 서버를 다음과 같이 구성합니다.

        - **서버 이름**에 서버 이름을 입력합니다. 이름이 `.database.windows.net` 도메인에서 고유하면 녹색 확인 표시가 상자에 표시됩니다.
        - **서버 관리자 로그인**에 원하는 관리자 이름을 입력합니다.
        - **암호** 및 **암호 확인**에 원하는 암호를 입력합니다.
        - 위치에서 웹앱에 사용한 것과 같은 위치를 선택합니다.
        - **Azure 서비스의 서버 액세스 허용**이 선택되어 있는지 확인합니다.
        - **선택**을 클릭합니다.
    
    - **선택**을 클릭합니다.

13. **웹앱 설정**을 클릭하고 데이터베이스 사용자 이름 및 암호를 지정하고 **확인**을 클릭합니다.

    Umbraco CMS 구성이 다음 스크린샷과 같은 모양으로 표시됩니다.

    ![구성 완료 - Azure App Service의 첫 번째 Umbraco](./media/app-service-web-get-started-dotnet-portal/configure-complete.png)

14. **만들기**를 클릭합니다.
    
    Umbraco 앱이 Azure에서 구성에 따라 만들어집니다. **배포를 시작했습니다.** 알림이 표시됩니다.

    ![배포 성공 - Azure App Service의 첫 번째 Umbraco](./media/app-service-web-get-started-dotnet-portal/deployment-started.png)
   
## <a name="launch-and-manage-your-umrbaco-web-app"></a>Umrbaco 웹앱 시작 및 관리

Azure에서 앱 배포가 완료되면 다른 알림이 표시됩니다.

![배포 성공 - Azure App Service의 첫 번째 Umbraco](./media/app-service-web-get-started-dotnet-portal/deployment-succeeded.png)

1. 알림을 클릭합니다. 알림을 놓친 경우 알림 벨(![알림 벨 - Azure App Service의 첫 번째 Umbraco](./media/app-service-web-get-started-dotnet-portal/notification.png))을 클릭하면 알림에 언제든 액세스할 수 있습니다.

    이제 웹앱의 관리 [블레이드](../azure-resource-manager/resource-group-portal.md#manage-resources)(*블레이드*: 가로로 열리는 포털 페이지)가 표시됩니다.

3. 개요 페이지의 맨 위에서 **찾아보기**를 클릭합니다.
   
    ![찾아보기 - Azure App Service의 첫 번째 Umbraco](./media/app-service-web-get-started-dotnet-portal/browse.png)

    이제 Umbraco **시작** 페이지가 표시됩니다. Umbraco 설치를 구성하고 사용을 시작하세요!

    ![Umbraco 구성 - Azure App Service의 첫 번째 Umbraco](./media/app-service-web-get-started-dotnet-portal/umbraco-config.png)
    
## <a name="next-steps"></a>다음 단계
* [Visual Studio를 사용하여 ASP.NET 웹앱을 Azure App Service에 배포](web-sites-dotnet-get-started.md) - 포함된 응용 프로그램 템플릿 중 하나를 사용하여 Visual Studio에서 Azure 웹앱을 새로 만드는 방법을 알아봅니다.
* [Azure App Service에 코드 배포](web-sites-deploy.md)- FTP 또는 소스 제어 리포지토리에서 배포하는 방법을 알아봅니다.
* [첫 번째 웹앱에 기능 추가](app-service-web-get-started-2.md) - Azure 앱을 한 단계 업그레이드합니다. 사용자를 인증합니다. 요구에 따라 규모를 조정합니다. 몇 가지 성능 경고를 설정합니다. 이 모든 작업이 클릭 몇 번으로 가능합니다.

