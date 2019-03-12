---
title: 웹 및 API apps를 Azure Stack 사용자에 게 사용할 수 있도록 | Microsoft Docs
description: App Service 리소스 공급자를 설치 하 고 만드는 자습서는 Azure Stack 사용자에 게를 제공 하는 웹 및 API apps를 만들 수를 제공 합니다.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/11/2019
ms.author: jeffgilb
ms.reviewer: anwestg
ms.custom: mvc
ms.lastreviewed: 11/05/2018
ms.openlocfilehash: f09ed70647eaa0275020bdac66e0fcf84d2ff00a
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/12/2019
ms.locfileid: "57760378"
---
# <a name="tutorial-make-web-and-api-apps-available-to-your-azure-stack-users"></a>자습서: 사용자에 게 웹 및 API apps에 Azure Stack

Azure Stack 클라우드 관리자에 사용자는 제품을 만들 수 있습니다 (테 넌 트) Azure Functions 및 웹 API 응용 프로그램을 만듭니다. 이러한 주문형, 클라우드 기반 앱에 사용자가 액세스할 수 있도록,으로 저장할 수 시간 및 리소스입니다.

이 설정 하려면 다음을 수행 해야 합니다.

> [!div class="checklist"]
> * App Service 리소스 공급자 배포
> * 제품 만들기
> * 제품 테스트

## <a name="deploy-the-app-service-resource-provider"></a>App Service 리소스 공급자 배포

1. [Azure Stack 개발 키트 호스트를 준비](azure-stack-app-service-before-you-get-started.md)합니다. 여기에 일부 앱을 만드는 데 필요한 SQL Server 리소스 공급자를 배포 합니다.
2. [설치 관리자 및 도우미 스크립트 다운로드](azure-stack-app-service-deploy.md)합니다.
3. [필요한 인증서를 만드는 도우미 스크립트를 실행](azure-stack-app-service-deploy.md)합니다.
4. [App Service 리소스 공급자를 설치](azure-stack-app-service-deploy.md) (설치 하는 데 몇 시간이 걸립니다 및 표시할 모든 작업자 역할에 대 한 합니다.)
5. [설치 유효성 검사](azure-stack-app-service-deploy.md#validate-the-app-service-on-azure-stack-installation)합니다.

## <a name="create-an-offer"></a>제품 만들기

예를 들어 사용자가 DNN 웹 콘텐츠 관리 시스템을 만들 수 있는 제품을 만들 수 있습니다. SQL Server 리소스 공급자를 설치 하 여 이미 사용 하는 SQL Server 서비스를 실행 해야 합니다.

1.  [할당량을 설정할](azure-stack-setting-quotas.md) 하 고 이름을 *AppServiceQuota*합니다. 선택 **Microsoft.Web** 에 대 한 합니다 **Namespace** 필드입니다.
2.  [계획을 만들려면](azure-stack-create-plan.md)합니다. 이름을 *TestAppServicePlan*를 선택 합니다 **Microsoft.SQL** 서비스와 **AppService 할당량** 할당량입니다.

    > [!NOTE]
    > 사용자가 다른 앱을 만들 수 있도록, 다른 서비스 계획에 필요할 수 있습니다. 예를 들어, Azure Functions 필요 합니다 **Microsoft.Storage** Wordpress 해야 하는 동안 계획에서 서비스 **Microsoft.MySQL**합니다.

3.  [제품을 만드는](azure-stack-create-offer.md), 이름을 **TestAppServiceOffer** 선택 합니다 **TestAppServicePlan** 계획 합니다.

## <a name="test-the-offer"></a>제품 테스트

App Service 리소스 공급자를 배포 하 고 제품을 생성, 했으므로 사용자로 로그인 하 고, 제품을 구독할 하 고, 앱 만들기 수 있습니다.

예를 들어 DNN 플랫폼 콘텐츠 관리 시스템을 만들겠습니다. 먼저, SQL database와 DNN 웹 앱을 만듭니다.

### <a name="subscribe-to-the-offer"></a>제품 구독

1. Azure Stack 포털에 로그인 (https://portal.local.azurestack.external) 테 넌 트입니다.
2. 선택 **subscription 얻기** >를 입력 **TestAppServiceSubscription** 아래에 있는 **표시 이름** > **제품 선택**  >  **TestAppServiceOffer** > **만들기**합니다.

### <a name="create-a-sql-database"></a>SQL 데이터베이스 만들기

1. 선택 **+**  >  **데이터 + 저장소** > **SQL Database**합니다.
2. 다음 필드를 제외 하 고 기본값을 유지 합니다.

    - **데이터베이스 이름**: DNNdb
    - **최대 크기 (mb)**: 100
    - **구독**: TestAppServiceOffer
    - **리소스 그룹**: DNN-RG

3. 선택 **로그인 설정**데이터베이스에 대 한 자격 증명을 입력 하 고 선택한 **확인**합니다. 이 자습서의 뒷부분에서 이러한 자격 증명을 사용 합니다.
4. 아래 **SKU** > SQL 호스팅 서버에 대해 만든 SQL SKU 선택 > 선택한 후 **확인**합니다.
5. **만들기**를 선택합니다.

### <a name="create-a-dnn-app"></a>DNN 앱 만들기

1. 선택 **+**  >  **스크롤하게** > **DNN 플랫폼 미리 보기** > **만들기** .
2. 입력 *DNNapp* 아래에서 **앱 이름** 선택한 **TestAppServiceOffer** 아래의 **구독**합니다.
3. 선택 **필요한 설정 구성** > **새로 만들기** >를 입력 하는 **App Service 계획** 이름입니다.
4. 선택 **가격 책정 계층** > **F1 무료** > **선택** > **확인**합니다.
5. 선택 **데이터베이스** 이전에 만든 SQL database에 대 한 자격 증명을 입력 합니다.
6. **만들기**를 선택합니다.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 다음 방법에 대해 알아보았습니다.

> [!div class="checklist"]
> * App Service 리소스 공급자 배포
> * 제품 만들기
> * 제품 테스트

에 대해 알아보려면 다음 자습서로 이동 하는 방법.

> [!div class="nextstepaction"]
> [Azure 및 Azure에 앱 배포 스택](user/azure-stack-solution-pipeline.md)
