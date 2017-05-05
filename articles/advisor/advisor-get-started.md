---
title: "Azure Advisor 시작 | Microsoft Docs"
description: "Azure Adviser를 시작합니다."
services: advisor
documentationcenter: NA
author: manbeenkohli
manager: carmonm
editor: 
ms.assetid: 
ms.service: advisor
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/10/2017
ms.author: makohli
translationtype: Human Translation
ms.sourcegitcommit: 197ebd6e37066cb4463d540284ec3f3b074d95e1
ms.openlocfilehash: a662841bebda460d4225e080f16705b3f16fdc46
ms.lasthandoff: 03/31/2017

---

# <a name="get-started-with-azure-advisor"></a>Azure Adviser 시작

Azure Portal을 통해 Advisor에 액세스하고, 권장 사항을 가져오고, 권장 사항을 검색하고, 권장 사항을 새로 고치는 방법을 알아봅니다.

## <a name="get-advisor-recommendations"></a>Advisor 권장 사항 가져오기

1. [Azure 포털](https://portal.azure.com)에 로그인합니다.

2. 왼쪽 창에서 **더 많은 서비스**를 클릭합니다.

3. 서비스 메뉴 창의 **모니터링 및 관리** 아래에서 **Azure Advisor**를 클릭합니다.  
 Advisor 대시보드가 표시됩니다.

   ![Azure Portal을 사용하여 Azure Advisor 액세스](./media/advisor-overview/advisor-azure-portal-menu.png) 

4. Advisor 대시보드에서 권장 사항을 받아보려는 구독을 선택합니다.  
Advisor 대시보드에 선택한 구독에 대한 개인화된 권장 사항이 표시됩니다. 

5. 특정 범주에 대한 권장 사항을 얻으려면 **고가용성**, **보안**, **성능** 또는 **비용** 탭 중 하나를 클릭합니다.
 
> [!NOTE]
> Advisor 권장 사항에 액세스하려면 먼저 Advisor에 *구독을 등록*해야 합니다. 구독은 *구독 소유자*가 Advisor 대시보드를 시작하고 **권장 사항 가져오기** 단추를 클릭할 때 등록됩니다. 이 작업은 *한 번만* 수행하면 됩니다. 구독이 등록된 후 구독, 리소스 그룹 또는 특정 리소스에 대한 *소유자*, *참여자* 또는 *리더*로 Advisor 권장 사항에 액세스할 수 있습니다.

  ![Azure Advisor 대시보드](./media/advisor-overview/advisor-all-tab.png)

## <a name="get-advisor-recommendation-details-and-implement-a-solution"></a>자세한 Advisor 권장 사항 가져오기 및 솔루션 구현

Advisor의 **권장 사항** 블레이드에서는 권장 사항에 대한 추가 정보를 제공합니다. 

1. [Azure Portal](https://portal.azure.com)에 로그인하고 [Azure Advisor](https://aka.ms/azureadvisordashboard)를 시작합니다.

2. **Advisor 권장 사항** 대시보드에서 **권장 사항 가져오기**를 클릭합니다.

3. 권장 사항 목록에서 자세하게 검토하려는 권장 사항을 클릭합니다.  
**권장 사항** 블레이드가 표시됩니다.

4. **권장 사항** 블레이드에서 잠재적 문제 해결을 위해 수행할 수 있는 작업에 대한 정보를 검토하거나 비용 절감 기회를 활용할 수 있습니다. 
  
  ![Advisor 권장 사항 블레이드](./media/advisor-overview/advisor-recommendation-action-example.png)

## <a name="search-for-advisor-recommendations"></a>Advisor 권장 사항 검색

특정 구독 또는 리소스 그룹에 대한 권장 사항을 검색할 수 있습니다. 또한 권장 사항을 상태별로 검색할 수도 있습니다.

1. [Azure Portal](https://portal.azure.com)에 로그인하고 [Azure Advisor](https://aka.ms/azureadvisordashboard)를 시작합니다.

2. 구독, 리소스 그룹 및 권장 사항 상태(**활성** 또는 **다시 알림**)를 필터링하여 권장 사항을 검색합니다.

3. 검색 필터 조건에 따라 Advisor 권장 사항 목록을 표시하려면 **권장 사항 가져오기**를 클릭합니다.

  ![Advisor 검색 필터 조건](./media/advisor-get-started/advisor-search.png)

## <a name="snooze-or-dismiss-advisor-recommendations"></a>Advisor 권장 사항 다시 알림 또는 해제

1. [Azure Portal](https://portal.azure.com)에 로그인하고 [Azure Advisor](https://aka.ms/azureadvisordashboard)를 시작합니다.

2. **권장 사항 가져오기**를 클릭하고 권장 사항 목록에서 권장 사항을 클릭합니다.

3. **권장 사항** 블레이드에서 **다시 알림**을 클릭합니다.  

   ![Advisor 권장 사항 작업 예제](./media/advisor-get-started/advisor-snooze.png)

4. 다시 알림 기간을 지정하거나, 권장 사항을 해제하려면 **안 함**을 선택합니다.


## <a name="next-steps"></a>다음 단계

Advisor에 대해 자세히 알아보려면 다음을 참조하세요.
* [Azure Advisor 소개](advisor-overview.md)
* [Advisor 고가용성 권장 사항](advisor-high-availability-recommendations.md)
* [Advisor 보안 권장 사항](advisor-security-recommendations.md)
-  [Advisor 성능 권장 사항](advisor-performance-recommendations.md)
* [Advisor 비용 권장 사항](advisor-performance-recommendations.md)

