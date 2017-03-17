---
title: "Azure Portal에서 Cognitive Services API 계정 만들기 | Microsoft Docs"
description: "Azure Portal에서 Microsoft Cognitive Services API를 만드는 방법입니다."
services: machine-learning
documentationcenter: 
author: garyericson
manager: jhubbard
editor: cgronlun
ms.assetid: b6176bb2-3bb6-4ebf-84d1-3598ee6e01c6
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/28/2017
ms.author: garye;gibattag
translationtype: Human Translation
ms.sourcegitcommit: 1e6ae31b3ef2d9baf578b199233e61936aa3528e
ms.openlocfilehash: a19f56f0b87d992e445034989bd35a827a72f367
ms.lasthandoff: 03/03/2017


---

# <a name="create-a-cognitive-services-apis-account-in-the-azure-portal"></a>Azure Portal에서 Cognitive Services API 계정 만들기

Microsoft Cognitive Service API를 사용하려면 먼저 Azure Portal에서 계정을 만들어야 합니다.

1. [Azure 포털](http://portal.azure.com)에 로그인합니다.

2. **+새로 만들기**를 클릭합니다.

3. **인텔리전스 + 분석**, **Cognitive Services API(미리 보기)**를 차례로 선택합니다.

    ![Cognitive Services API 선택](media/cognitive-services-apis-create-account/select-cognitive-services-apis.png)

4. **만들기** 페이지에서 다음 정보를 제공합니다.

    -   **계정 이름:** 계정의 이름입니다. 설명이 포함된 이름(예: *AFaceAPIAccount*)을 사용하는 것이 좋습니다.

    -   **구독:** 참가자 이상의 권한이 있는 사용 가능한 Azure 구독 중 하나를 선택합니다.

    -   **API 형식:** 사용하려는 Cognitive Services API를 선택합니다. 사용 가능한 다양한 Cognitive Services API에 대한 자세한 내용은 [Cognitive Services](https://azure.microsoft.com/services/cognitive-services/) 사이트를 참조하세요.

    ![API 형식 선택](media/cognitive-services-apis-create-account/list-of-apis.png)

    -   **가격 책정 계층:** Cognitive Services 계정의 비용은 실제 사용량 및 선택한 옵션에 따라 다릅니다. 각 API의 가격 책정에 대한 자세한 내용은 [가격 책정 페이지](https://azure.microsoft.com/pricing/details/cognitive-services/)를 참조하세요.

    -   **리소스 그룹:** 리소스 그룹은 동일한 수명 주기, 권한 및 정책을 공유하는 리소스의 컬렉션입니다. 리소스 그룹에 대한 자세한 내용은 [포털을 통해 Azure 리소스 관리](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-portal)를 참조하세요.

    -   **리소스 그룹 위치:** 선택된 API가 전역(특정 위치에 바인딩되지 않음)인 경우에만 필요합니다. 그렇지만 API가 전역이고 특정 위치에 바인딩되지 않은 경우 Cognitive Services API 계정과 연결된 메타데이터가 상주할 리소스 그룹의 위치를 지정해야 합니다. 이 위치는 계정의 런타임 가용성에 영향을 주지 않습니다. 리소스 그룹에 대한 자세한 내용은 [포털을 통해 Azure 리소스 관리](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-portal)를 참조하세요.

    -   **API 설정:** [Azure 계정 관리자](https://docs.microsoft.com/azure/active-directory/active-directory-assign-admin-roles)가 명시적으로 사용하도록 설정할 때까지 기본적으로 계정을 만들 수 없습니다.

        이 설정 변경 내용은 현재 선택된 API 형식 및 위치나 왼쪽 패널의 리소스 그룹 위치에만 적용됩니다.

        ![Cognitive Services API 계정 만들기](media/cognitive-services-apis-create-account/create-account.png)

        > [!NOTE]
        > 업데이트 설정이 실패했다는 알림을 받으면 [계정 관리자](https://docs.microsoft.com/azure/billing/billing-add-change-azure-subscription-administrator#types-of-azure-admin-accounts)로 로그인되지 않습니다. 계정 관리자는 이전 단계에 따라 계정을 만들도록 설정해야 합니다.
        >
        > ![업데이트 설정 실패 메시지](./media/cognitive-services-apis-create-account/updatefailed.png)
        
        경우에 따라 계정 관리자는 구독에 대한 액세스 권한이 없을 수 있습니다. 그런 경우 서비스 관리자가 [구독 또는 서비스를 관리하는 Azure 관리자 역할 추가 또는 변경](https://docs.microsoft.com/azure/billing/billing-add-change-azure-subscription-administrator) 문서의 단계를 따르도록 합니다.
        
        구독의 계정 관리자 또는 서비스 관리자를 찾으려면 [Azure Portal](https://portal.azure.com)에서 구독을 선택한 후 __속성__을 선택합니다. __계정 관리자__ 및 __서비스 관리자__ 정보가 속성 블레이드 맨 아래에 표시됩니다.
        
        ![구독 속성](./media/cognitive-services-apis-create-account/subscription-properties.png)
    
    Microsoft에서는 사용자가 Cognitive Services로 보낸 데이터를 Microsoft 제품 및 서비스 개선을 위해 사용할 수 있습니다. 자세한 내용은 Online Services 약관의 [Microsoft Cognitive Services 섹션](http://www.microsoft.com/Licensing/product-licensing/products.aspx) 
   을 참조하세요.

5. Azure Portal 대시보드에 계정을 고정하려면 **대시보드에 고정**을 클릭합니다.

6. **만들기** 를 클릭하여 계정을 만듭니다.

Cognitive Services 계정이 성공적으로 배포되면 대시보드에서 타일을 클릭하여 계정 정보를 확인합니다.

**개요** 섹션의 **끝점 URL**과 **키** 섹션의 키를 사용하여 응용 프로그램에서 API 호출을 시작할 수 있습니다.

![계정 정보 표시](media/cognitive-services-apis-create-account/display-account.png)

![계정 키 표시](media/cognitive-services-apis-create-account/account-keys.png)

### <a name="next-steps"></a>다음 단계

- 사용 가능한 모든 Microsoft Cognitive Services에 대한 자세한 내용은 [Cognitive Services](https://azure.microsoft.com/services/cognitive-services/)를 참조하세요.

- 몇 가지 예제 Cognitive Services API 사용에 대한 빠른 시작 가이드를 보려면 다음을 참조하세요.
    - [감성, 핵심 문구, 토픽 및 언어를 검색하는 텍스트 분석 API 시작](cognitive-services-text-analytics-quick-start.md)
    - [Cognitive Services Recommendations API에 대한 빠른 시작 가이드](cognitive-services-recommendations-quick-start.md)
