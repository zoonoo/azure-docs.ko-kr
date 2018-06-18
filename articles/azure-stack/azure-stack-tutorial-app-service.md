---
title: 사용자에 게 웹 및 API 앱 사용할 수 있는 Azure 스택 | Microsoft Docs
description: 앱 서비스 리소스 공급자를 설치 하 고 만드는 자습서에서는 Azure 스택 사용자를 제공 하는 웹 및 API 앱을 만들 수를 제공 합니다.
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
ms.date: 06/05/2018
ms.author: jeffgilb
ms.reviewer: ''
ms.custom: mvc
ms.openlocfilehash: 0171dba639e480a04cdd1c7f23d546d01121fb42
ms.sourcegitcommit: 50f82f7682447245bebb229494591eb822a62038
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/08/2018
ms.locfileid: "35247401"
---
# <a name="tutorial-make-web-and-api-apps-available-to-your-azure-stack-users"></a>자습서: 웹 앱 및 API 앱을 사용할 수 있도록 사용자에 게 Azure 스택

Azure 스택 클라우드 관리자로 서 사용자가 제공을 만들 수 있습니다 (테 넌 트) Azure 함수 및 웹 및 API 응용 프로그램을 만듭니다. 이러한 주문형, 클라우드 기반 응용 프로그램에 사용자가 액세스할 수 있도록, 하 여 시간 및 리소스가 저장할 수 있습니다.

이 설정 하려면 다음을 수행 합니다.

> [!div class="checklist"]
> * 앱 서비스 리소스 공급자를 배포
> * 제품 만들기
> * 제품 테스트

## <a name="deploy-the-app-service-resource-provider"></a>앱 서비스 리소스 공급자를 배포

1. [Azure 스택 개발 키트 호스트 준비](azure-stack-app-service-before-you-get-started.md)합니다. 여기에 일부 앱을 만드는 데 필요한 SQL Server 리소스 공급자를 배포 합니다.
2. [설치 관리자 및 도우미 스크립트 다운로드](azure-stack-app-service-deploy.md)합니다.
3. [필요한 인증서를 만드는 도우미 스크립트를 실행](azure-stack-app-service-deploy.md)합니다.
4. [앱 서비스 리소스 공급자를 설치](azure-stack-app-service-deploy.md) (설치 하는 데 몇 시간이 걸립니다 및 표시 되도록 모든 작업자 역할입니다.)
5. [설치를 확인](azure-stack-app-service-deploy.md#validate-the-app-service-on-azure-stack-installation)합니다.

## <a name="create-an-offer"></a>제품 만들기

예를 들어 사용자가 DNN 웹 콘텐츠 관리 시스템을 만들 수 있는 제품을 만들 수 있습니다. SQL Server 서비스는 SQL Server 리소스 공급자를 설치 하 여 사용 하도록 이미 설정 해야 합니다.

1.  [할당량 설정](azure-stack-setting-quotas.md) 하 고 이름을 *AppServiceQuota*합니다. 선택 **Microsoft.Web** 에 대 한는 **Namespace** 필드입니다.
2.  [계획 만들기](azure-stack-create-plan.md)합니다. 이름을 *TestAppServicePlan*, 선택는 **Microsoft.SQL** 서비스 및 **AppService 할당량** 할당량입니다.

    > [!NOTE]
    > 사용자가 다른 응용 프로그램을 만들 수 있도록, 다른 서비스 계획에 필요할 수 있습니다. 예를 들어 Azure 함수 필요는 **Microsoft.Storage** Wordpress 반면 계획에서 서비스 **Microsoft.MySQL**합니다.

3.  [제안 만들기](azure-stack-create-offer.md), 이름을 **TestAppServiceOffer** 선택 하 고는 **TestAppServicePlan** 계획 합니다.

## <a name="test-the-offer"></a>제품 테스트

앱 서비스 리소스 공급자를 배포 하 고 제공 하는 서비스를 만들면 한 했으므로 사용자로 로그인 하 고, 제품을 구독 하 고, 응용 프로그램을 만들 수 있습니다.

이 예에서는 DNN 플랫폼 콘텐츠 관리 시스템을 만들겠습니다. 첫째, SQL 데이터베이스 및 DNN 웹 응용 프로그램 만듭니다.

### <a name="subscribe-to-the-offer"></a>제품 구독

1. Azure 스택 포털에 로그인 (https://portal.local.azurestack.external) 테 넌 트로 합니다.
2. 선택 **구독** >를 입력 **TestAppServiceSubscription** 아래 **표시 이름** > **제안을 선택**  >  **TestAppServiceOffer** > **만들**합니다.

### <a name="create-a-sql-database"></a>SQL 데이터베이스 만들기

1. 선택 **+**  >  **데이터 + 저장소** > **SQL 데이터베이스**합니다.
2. 다음 필드를 제외 하 고는 기본값을 유지 합니다.

    - **데이터베이스 이름**: DNNdb
    - **최대 크기 (MB)**: 100
    - **구독**: TestAppServiceOffer
    - **리소스 그룹**: DNN RG

3. 선택 **로그인 설정**를 데이터베이스에 대 한 자격 증명을 입력 한 다음 선택 **확인**합니다. 이 자습서의 뒷부분에 나오는 이러한 자격 증명을 사용 합니다.
4. 아래 **SKU** > SQL 호스팅 서버에 대해 만든 SQL SKU 선택 >를 클릭 한 **확인**합니다.
5. **만들기**를 선택합니다.

### <a name="create-a-dnn-app"></a>DNN 응용 프로그램 만들기

1. 선택 **+**  >  **스크롤하게** > **DNN 플랫폼 미리 보기** > **만들기** .
2. 입력 *DNNapp* 아래 **응용 프로그램 이름** 선택 **TestAppServiceOffer** 아래 **구독**합니다.
3. 선택 **필요한 설정 구성** > **새로 만들기** >를 입력 하는 **앱 서비스 계획** 이름입니다.
4. 선택 **가격 책정 계층** > **F1 무료** > **선택** > **확인**합니다.
5. 선택 **데이터베이스** 앞에서 만든 SQL 데이터베이스에 대 한 자격 증명을 입력 하 고 있습니다.
6. **만들기**를 선택합니다.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 다음 방법에 대해 알아보았습니다.

> [!div class="checklist"]
> * 앱 서비스 리소스 공급자를 배포
> * 제품 만들기
> * 제품 테스트

자세한 내용은 다음 자습서로 이동 하는 방법:

> [!div class="nextstepaction"]
> [Azure 및 Azure에 앱 배포 스택](user/azure-stack-solution-pipeline.md)
