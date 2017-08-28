---
title: "WordPress 앱을 Azure Portal에 5분 안에 배포 | Microsoft Docs"
description: "WordPress 앱을 배포하여 App Service에서 웹앱을 간편하게 실행하는 방법을 알아봅니다. 결과를 즉시 확인합니다."
services: app-service\web
documentationcenter: 
author: cephalin
manager: erikre
editor: 
ms.assetid: 6feac128-c728-4491-8b79-962da9a40788
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/13/2017
ms.author: cephalin
ms.translationtype: Human Translation
ms.sourcegitcommit: 0921b01bc930f633f39aba07b7899ad60bd6a234
ms.openlocfilehash: 7ef5802866bf96859d3f44cdb58cbb412b08a700
ms.contentlocale: ko-kr
ms.lasthandoff: 03/01/2017

---
# <a name="deploy-a-wordpress-app-in-the-azure-portal-in-five-minutes"></a>WordPress 앱을 Azure Portal에 5분 안에 배포

이 자습서에서는 첫 번째 [WordPress](https://wordpress.org/) 웹앱을 몇 분 내에 [Azure App Service](../app-service/app-service-value-prop-what-is.md)에 배포하는 방법을 보여줍니다.

![WordPress 사이트](./media/app-service-web-get-started-php-portal/wpdashboard.png)

## <a name="prerequisites"></a>필수 조건
Microsoft Azure 계정이 있어야 합니다. 계정이 없는 경우 [무료 평가판을 등록](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F)하거나 [Visual Studio 구독자 혜택을 활성화](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F)할 수 있습니다.

> [!NOTE]
> Azure 계정 없이 [App Service를 체험](https://azure.microsoft.com/try/app-service/)할 수 있습니다. 시작 앱을 만들고 최대 한 시간 동안 해당 앱을 사용하여 재생합니다. -- 신용 카드는 필요하지 않으며 약정도 필요하지 않습니다.
> 
> 

## <a name="deploy-the-wordpress-app"></a>WordPress 앱 배포
1. [Azure 포털](https://portal.azure.com)에 로그인합니다.

2. [https://portal.azure.com/#create/WordPress.WordPress](https://portal.azure.com/#create/WordPress.WordPress)를 엽니다.

    이 링크는 새 WordPress 앱을 Azure Portal에서 즉시 구성할 수 있는 바로 가기입니다.

3. **앱 이름**에 웹앱 이름을 입력합니다. 이름이 `azurewebsites.net` 도메인에서 고유하면 녹색 확인 표시가 상자에 표시됩니다.
   
5. **리소스 그룹**에서 **새로 만들기**를 클릭하여 새 [리소스 그룹](../azure-resource-manager/resource-group-overview.md)을 만든 다음 이름을 지정합니다.

6. **데이터베이스 공급자**에서 **CleaDB**를 선택합니다.

7. **App Service 계획/위치** > **새로 만들기**를 클릭합니다. [App Service 계획](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md)을 다음과 같이 구성합니다.

    - **App Service 계획**에 원하는 이름을 입력합니다.
    - **위치**에서 계획을 호스트할 위치를 선택합니다.
    - **가격 책정 계층**을 클릭한 다음 **F1 무료** 또는 다른 적합한 계층을 선택한 다음 **선택**을 클릭합니다.
    - **확인**을 클릭합니다.

8. **데이터베이스** > **새로 만들기**를 클릭합니다. SQL Database를 다음과 같이 구성합니다.

    - **데이터베이스 이름**에 데이터베이스 이름을 입력합니다. 
    - **위치**에서 App Service 계획과 같은 위치를 선택합니다.
    - **가격 책정 계층**을 클릭한 다음 **수성** 또는 다른 적합한 계층을 선택한 다음 **선택**을 클릭합니다.
    - **약관**을 선택하고 **구입**을 클릭합니다.
    - **확인**을 클릭합니다.

9. **만들기**를 클릭합니다.

    WordPress 앱이 Azure에서 구성에 따라 만들어집니다. **배포를 시작했습니다.** 알림이 표시됩니다.

    ![배포가 시작됨 - Azure App Service의 첫 번째 WordPress](./media/app-service-web-get-started-php-portal/deployment-started.png)
   
## <a name="launch-and-manage-your-wordpress-web-app"></a>WordPress 웹앱 시작 및 관리

Azure에서 앱 배포가 완료되면 다른 알림이 표시됩니다.

![배포가 완료됨 - Azure App Service의 첫 번째 WordPress](./media/app-service-web-get-started-php-portal/deployment-succeeded.png)

1. 알림을 클릭합니다. 알림을 놓친 경우 알림 벨(![알림 벨 - Azure App Service의 첫 번째 WordPress](./media/app-service-web-get-started-dotnet-portal/notification.png))을 클릭하면 알림에 언제든 액세스할 수 있습니다.

    이제 웹앱의 관리 [블레이드](../azure-resource-manager/resource-group-portal.md#manage-resources)(*블레이드*: 가로로 열리는 포털 페이지)가 표시됩니다.

3. 개요 페이지의 맨 위에서 **찾아보기**를 클릭합니다.
   
    ![찾아보기 - Azure App Service의 첫 번째 WordPress](./media/app-service-web-get-started-php-portal/browse.png)

    이제 WordPress **시작** 페이지가 표시됩니다. WordPress 설치를 구성하고 사용을 시작하세요!

    ![WordPress 구성 - Azure App Service의 첫 번째 WordPress](./media/app-service-web-get-started-php-portal/wordpress-config.png)
    
## <a name="next-steps"></a>다음 단계
* [Azure에 Laravel 웹앱을 만들고 구성하여 배포](app-service-web-php-get-started.md) - Azure에서 PHP 웹앱을 실행하는 데 필요한 다음과 같은 기본 기술을 알아봅니다.

    * PowerShell/Bash로부터 Azure의 앱을 만들어 구성합니다.
    * PHP 버전을 설정합니다.
    * 루트 응용 프로그램 디렉터리에 있지 않은 시작 파일을 사용합니다.
    * Composer 자동화를 사용합니다.
    * 특정 환경 변수에 액세스합니다.
    * 일반적인 오류 문제를 해결합니다.

* [Azure App Service에 코드 배포](web-sites-deploy.md)- FTP 또는 소스 제어 리포지토리에서 배포하는 방법을 알아봅니다.
* [첫 번째 웹앱에 기능 추가](app-service-web-get-started-2.md) - Azure 앱을 한 단계 업그레이드합니다. 사용자를 인증합니다. 요구에 따라 규모를 조정합니다. 몇 가지 성능 경고를 설정합니다. 이 모든 작업이 클릭 몇 번으로 가능합니다.

