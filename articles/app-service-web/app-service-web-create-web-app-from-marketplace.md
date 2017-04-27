---
title: "Azure Marketplace에서 웹앱 만들기 | Microsoft Docs"
description: "Azure 포털을 사용하여 Azure 마켓플레이스에서 새 WordPress 웹앱을 만드는 방법에 대해 알아보세요."
services: app-service\web
documentationcenter: 
author: rmcmurray
manager: erikre
editor: 
ms.assetid: 972a296d-f927-470b-8534-0f2cb9eac223
ms.service: app-service-web
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 04/25/2017
ms.author: robmcm
translationtype: Human Translation
ms.sourcegitcommit: b1a633a86bd1b5997d5cbf66b16ec351f1043901
ms.openlocfilehash: a04c7129cd2e16c129f3e4b8e8e40f76ff37114d
ms.lasthandoff: 01/20/2017


---
# <a name="create-a-web-app-from-the-azure-marketplace"></a>Azure 마켓플레이스에서 웹앱 만들기
<!-- Note: This article replaces web-sites-php-web-site-gallery.md -->

[!INCLUDE [tabs](../../includes/app-service-web-get-started-nav-tabs.md)]

Azure 마켓플레이스에서 Microsoft, 타사 및 오픈 소스 소프트웨어에서 개발된 다양한 인기 웹 앱을 사용할 수 있습니다. 예를 들어 WordPress, Umbraco CMS, Drupal 등이 있습니다. 이러한 웹앱은 이 WordPress 예제에서 [PHP], [.NET], [Node.js], [Java], [Python] 등과 같은 광범위하고 인기 있는 프레임워크에서 작성됩니다. Azure 마켓플레이스에서 웹앱을 만들려면 유일하게 필요한 소프트웨어는 [Azure 포털]에 대해 사용하는 브라우저입니다.

이 자습서에서는 다음 방법에 대해 알아봅니다.

* Azure 마켓플레이스 템플릿을 기반으로 하는 Azure 앱 서비스에서 웹앱 찾기 및 만들기
* 새 웹앱에 Azure 앱 서비스 설정 구성
* 웹앱 시작 및 관리

이 자습서에서는 학습 목표에 따라 Azure 마켓플레이스에서 WordPress 블로그 사이트를 배포합니다. 이 자습서의 단계를 완료하면 WordPress 사이트를 소유하고 클라우드에서 실행하게 됩니다.

![WordPress 웹앱 대시보드 예제][WordPressDashboard1]

이 자습서에서 배포하는 WordPress 사이트는 데이터베이스에 MySQL을 사용합니다. 데이터베이스에 대신 SQL 데이터베이스를 사용하려면 Azure 마켓플레이스를 통해서도 사용 가능한 [프로젝트 Nami]를 참조하세요.

> [!NOTE]
> 이 자습서를 완료하려면 Microsoft Azure 계정이 필요합니다. 계정이 없는 경우 [Visual Studio 구독자 혜택을 활성화][activate]하거나 [무료 평가판을 등록][free trial]할 수 있습니다.
> 
> Azure 계정을 등록하기 전에 Azure 앱 서비스를 시작하려는 경우 [앱 서비스 평가]로 이동하세요. 여기서 신용 카드와 약정 없이 앱 서비스에서 수명이 짧은 스타터 웹앱을 즉시 만들 수 있습니다.
> 
> 

## <a name="find-and-create-a-web-app-in-azure-app-service"></a>Azure 앱 서비스에서 웹앱 찾기 및 만들기
1. [Azure 포털]에 로그인합니다.
2. **새로 만들기**를 클릭합니다.
   
    ![새 Azure 리소스 만들기][MarketplaceStart]
3. **WordPress**를 검색한 다음 **WordPress**를 클릭합니다. (MySQL 대신 SQL 데이터베이스를 사용하려는 경우 **프로젝트 Nami**를 검색합니다.)
   
    ![마켓플레이스에서 WordPress 검색][MarketplaceSearch]
4. WordPress 앱에 대한 설명을 읽은 후 **만들기**를 클릭합니다.
   
    ![WordPress 웹앱 만들기][MarketplaceCreate]

## <a name="configure-azure-app-service-settings-for-your-new-web-app"></a>새 웹앱에 Azure 앱 서비스 설정 구성
1. 새 웹앱을 만든 후에 다음 단계를 완료하는 데 사용할 WordPress 설정 블레이드가 표시됩니다.
   
    ![WordPress 웹앱 설정 구성][ConfigStart]
2. **웹앱** 상자에서 웹앱에 대한 이름을 입력합니다.
   
    웹앱의 URL이 *{name}*.azurewebsites.net이기 때문에 이 이름은 azurewebsites.net 도메인에서 고유해야 합니다. 입력한 이름이 고유하지 않으면 빨간색 느낌표가 텍스트 상자에 나타납니다.
   
    ![WordPress 웹앱 이름 구성][ConfigAppName]
3. 구독이 둘 이상 있는 경우 사용하려는 구독을 선택합니다.
   
    ![웹앱의 구독 구성][ConfigSubscription]
4. **리소스 그룹** 을 선택하거나 새로 만듭니다.
   
    리소스 그룹에 대한 자세한 내용은 [Azure Resource Manager 개요][ResourceGroups]를 참조하세요.
   
    ![웹앱의 리소스 그룹 구성][ConfigResourceGroup]
5. **앱 서비스 계획/위치** 을 선택하거나 새로 만듭니다.
   
    App Service 계획에 대한 자세한 내용은 [Azure App Service 계획 개요][AzureAppServicePlans]를 참조하세요.
   
    ![웹앱의 서비스 계획 구성][ConfigServicePlan]
6. **데이터베이스**를 클릭하고 **새 MySQL 데이터베이스** 블레이드에서 MySQL 데이터베이스를 구성하는 데 필요한 값을 제공합니다.
   
    a. 새 이름을 입력하거나 기본 이름을 사용합니다.
   
    b. **데이터베이스 형식**을 **공유**로 설정해 둡니다.
   
    c. 웹앱에 사용자가 선택한 동일한 위치를 선택합니다.
   
    d. 가격 책정 계층을 선택합니다. **Mercury** 를 사용해도 좋습니다.
   
    e. **새 MySQL 데이터베이스 만들기** 블레이드에서 법률 약관에 동의한 다음 **확인**을 클릭합니다.
   
    ![웹앱의 데이터베이스 설정 구성][ConfigDatabase]
7. **WordPress** 블레이드에서 법적 조건에 동의한 다음 **만들기**를 클릭합니다.
   
    ![웹앱 설정을 마치고 확인을 클릭][ConfigFinished]
   
    Azure 앱 서비스는 일반적으로 일 분 내에 웹앱을 만듭니다. 포털 페이지의 위쪽에 종 아이콘을 클릭하여 진행률을 확인할 수 있습니다.
   
    ![진행률 표시기][ConfigProgress]

## <a name="launch-and-manage-your-wordpress-web-app"></a>WordPress 웹앱 시작 및 관리
1. 웹앱 만들기가 완료되면 Azure 포털에서 응용 프로그램을 만든 리소스 그룹으로 이동하고 웹앱 및 데이터베이스를 볼 수 있습니다.
   
    전구 아이콘이 있는 추가 리소스는 [Application Insights][ApplicationInsights]이며 웹앱에 대한 모니터링 서비스를 제공합니다.
2. **리소스 그룹** 블레이드에서 웹앱 줄을 클릭합니다.
   
    ![WordPress 웹앱 선택][WordPressSelect]
3. 웹앱 블레이드에서 **찾아보기**를 클릭합니다.
   
    ![WordPress 웹앱으로 이동][WordPressBrowse]
4. WordPress 블로그에 사용할 언어를 선택하라는 메시지가 표시되면 원하는 언어를 선택한 다음 **계속**을 클릭합니다.
   
    ![WordPress 웹앱의 언어 구성][WordPressLanguage]
5. WordPress **시작** 페이지에서 WordPress에 필요한 구성 정보를 입력한 다음 **WordPress 설치**를 클릭합니다.
   
    ![WordPress 웹앱의 설정 구성][WordPressConfigure]
6. **시작** 페이지에서 만든 자격 증명을 사용하여 로그인합니다.  
7. 사이트 대시보드 페이지가 열리고 입력한 정보가 표시됩니다.    
   
    ![WordPress 대시보드 보기][WordPressDashboard2]

## <a name="next-steps"></a>다음 단계
이 자습서에서는 Azure 마켓플레이스에서 예제 웹앱을 만들고 배포하는 방법을 살펴보았습니다.

앱 서비스 웹앱으로 작업하는 방법에 대한 자세한 정보는 페이지의 왼쪽(넓은 브라우저 창의 경우) 또는 페이지의 위쪽(좁은 브라우저 창의 경우)에서 링크를 참조하세요.

Azure에서 WordPress 웹앱을 개발하는 방법은 [Azure App Service에서 WordPress 개발][WordPressOnAzure]을 참조하세요.

<!-- URL List -->

[PHP]: https://azure.microsoft.com/develop/php/
[.NET]: https://azure.microsoft.com/develop/net/
[Node.js]: https://azure.microsoft.com/develop/nodejs/
[Java]: https://azure.microsoft.com/develop/java/
[Python]: https://azure.microsoft.com/develop/python/
[activate]: https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/
[free trial]: https://azure.microsoft.com/pricing/free-trial/
[앱 서비스 평가]: https://azure.microsoft.com/try/app-service/
[ResourceGroups]: ../azure-resource-manager/resource-group-overview.md
[AzureAppServicePlans]: ../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md
[ApplicationInsights]: https://azure.microsoft.com/services/application-insights/
[Azure 포털]: https://portal.azure.com/
[프로젝트 Nami]: http://projectnami.org/
[WordPressOnAzure]: ./develop-wordpress-on-app-service-web-apps.md

<!-- IMG List -->

[MarketplaceStart]: ./media/app-service-web-create-web-app-from-marketplace/marketplacestart.png
[MarketplaceSearch]: ./media/app-service-web-create-web-app-from-marketplace/marketplacesearch.png
[MarketplaceCreate]: ./media/app-service-web-create-web-app-from-marketplace/marketplacecreate.png
[ConfigStart]: ./media/app-service-web-create-web-app-from-marketplace/configstart.png
[ConfigAppName]: ./media/app-service-web-create-web-app-from-marketplace/configappname.png
[ConfigSubscription]: ./media/app-service-web-create-web-app-from-marketplace/configsubscription.png
[ConfigResourceGroup]: ./media/app-service-web-create-web-app-from-marketplace/configresourcegroup.png
[ConfigServicePlan]: ./media/app-service-web-create-web-app-from-marketplace/configserviceplan.png
[ConfigDatabase]: ./media/app-service-web-create-web-app-from-marketplace/configdatabase.png
[ConfigFinished]: ./media/app-service-web-create-web-app-from-marketplace/configfinished.png
[ConfigProgress]: ./media/app-service-web-create-web-app-from-marketplace/configprogress.png
[WordPressSelect]: ./media/app-service-web-create-web-app-from-marketplace/wpselect.png
[WordPressBrowse]: ./media/app-service-web-create-web-app-from-marketplace/wpbrowse.png
[WordPressLanguage]: ./media/app-service-web-create-web-app-from-marketplace/wplanguage.png
[WordPressDashboard1]: ./media/app-service-web-create-web-app-from-marketplace/wpdashboard1.png
[WordPressDashboard2]: ./media/app-service-web-create-web-app-from-marketplace/wpdashboard2.png
[WordPressConfigure]: ./media/app-service-web-create-web-app-from-marketplace/wpconfigure.png

