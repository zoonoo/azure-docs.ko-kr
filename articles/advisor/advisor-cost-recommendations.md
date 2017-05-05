---
title: "Azure Advisor 비용 권장 사항 | Microsoft Docs"
description: "Azure Advisor를 사용하여 Azure 배포 비용을 최적화합니다."
services: advisor
documentationcenter: NA
author: kumudd
manager: carmonm
editor: 
ms.assetid: 
ms.service: advisor
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/16/2016
ms.author: kumud
translationtype: Human Translation
ms.sourcegitcommit: 197ebd6e37066cb4463d540284ec3f3b074d95e1
ms.openlocfilehash: 5eef2116f238b477fa8de46ce7b25728c393739c
ms.lasthandoff: 03/31/2017

---

# <a name="advisor-cost-recommendations"></a>Advisor 비용 권장 사항

Advisor는 유휴 및 사용 미달 리소스를 식별하여 전체적인 Azure 사용을 최적화하고 줄이는 데 도움을 줍니다. Advisor 대시보드의 **비용** 탭에서 비용 관련 권장 지침을 얻을 수 있습니다.

![Advisor 비용 탭](./media/advisor-cost-recommendations/advisor-cost-tab2.png)

## <a name="optimize-virtual-machine-spend-by-resizing-underutilized-instances"></a>사용률이 낮은 인스턴스의 크기를 조정하여 가상 컴퓨터 소비 최적화 
특정 응용 프로그램 시나리오에서는 기본적으로 사용률이 낮을 수 있으나 가상 컴퓨터의 크기와 수를 관리하여 비용을 절감할 수도 있습니다. Advisor는 14일 동안 가상 컴퓨터 사용량을 모니터링하고 사용률이 낮은 가상 컴퓨터를 식별합니다. 4일 이상 CPU 사용률이 5% 이하이고 네트워크 사용량이 7MB 이하인 가상 컴퓨터는 사용률이 낮은 가상 컴퓨터로 간주됩니다.

Advisor에는 가상 컴퓨터를 계속 실행할 때의 예상 비용이 표시되므로 해당 가상 컴퓨터를 종료하거나 크기를 조정하도록 선택할 수 있습니다.  

![가상 컴퓨터의 크기를 조정하기 위한 Advisor 비용 권장 사항](./media/advisor-cost-recommendations/advisor-cost-resizevms.png)

## <a name="use-a-cost-effective-solution-to-manage-performance-goals-of-multiple-sql-databases"></a>비용 효율적인 솔루션을 사용하여 여러 SQL Database의 성능 목표 관리
Advisor는 Elastic Database 풀을 만들 경우 도움이 되는 SQL Server 인스턴스를 식별합니다. Elastic Database 풀은 다양한 사용 패턴을 지닌 여러 데이터베이스에 대한 성능 목표를 관리하기 위한 간단하고 비용 효율적인 솔루션을 제공합니다. Azure 탄력적 풀에 대한 자세한 내용은 [Azure 탄력적 풀이란?](https://azure.microsoft.com/en-us/documentation/articles/sql-database-elastic-pool/)을 참조하세요.

![Elastic Database 풀에 대한 Advisor 비용 권장 사항](./media/advisor-cost-recommendations/advisor-cost-elasticdbpools.png)

## <a name="how-to-access-cost-recommendations-in-azure-advisor"></a>Azure Advisor에서 비용 권장 사항에 액세스하는 방법

1. [Azure 포털](https://portal.azure.com)에 로그인합니다.

2. 왼쪽 창에서 **더 많은 서비스**를 클릭합니다.

3. 서비스 메뉴 창의 **모니터링 및 관리** 아래에서 **Azure Advisor**를 클릭합니다.  
 Advisor 대시보드가 표시됩니다.

4. Advisor 대시보드에서 **비용** 탭을 클릭합니다.

5. 권장 사항을 받아보려는 구독을 선택하고 **권장 사항 가져오기**를 클릭합니다.

> [!NOTE]
> Advisor 권장 사항에 액세스하려면 먼저 Advisor에 *구독을 등록*해야 합니다. 구독은 *구독 소유자*가 Advisor 대시보드를 시작하고 **권장 사항 가져오기** 단추를 클릭할 때 등록됩니다. 이 작업은 *한 번만* 수행하면 됩니다. 구독이 등록된 후 구독, 리소스 그룹 또는 특정 리소스에 대한 *소유자*, *참여자* 또는 *리더*로 Advisor 권장 사항에 액세스할 수 있습니다.

## <a name="next-steps"></a>다음 단계

Advisor 권장 사항에 대한 자세한 내용은 다음을 참조하세요.
* [Advisor 소개](advisor-overview.md)
* [시작](advisor-get-started.md)
* [Advisor 성능 권장 사항](advisor-cost-recommendations.md)
* [Advisor 고가용성 권장 사항](advisor-cost-recommendations.md)
* [Advisor 보안 권장 사항](advisor-cost-recommendations.md)

