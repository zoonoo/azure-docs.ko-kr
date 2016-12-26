---
title: "Azure Advisor 백업 소개 | Microsoft Docs"
description: "Azure Advisor를 사용하여 Azure 배포를 최적화합니다."
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
ms.sourcegitcommit: f67ca5996fdde0f0939ac46c2f6280f9d89af3d0
ms.openlocfilehash: a39a5555ce3af59b509a0522725addbb382ff7af

---

# <a name="introduction-to-azure-advisor"></a>Azure Advisor 소개

Azure Advisor, 주요 기능 및 자주 묻는 질문에 대해 알아봅니다.

## <a name="what-is-azure-advisor"></a>Azure Advisor란?
Azure 관리자는 Azure 배포를 최적화하기 위한 모범 사례를 따르는 데 도움이 되는 개인 설정된 클라우드 컨설턴트입니다. 리소스 구성 및 사용량 원격 분석을 수행하고 Azure 리소스의 경제성, 성능, 고가용성 및 보안을 개선하는 데 도움이 되는 해결 방법을 권장합니다.

Azure Advisor를 사용하면 다음과 같은 작업을 수행할 수 있습니다.
-   사전 대응이 가능하고, 실행 가능하고, 개인화된 모범 사례 권장 사항 
-   전체 Azure 사용을 줄일 수 있는 기회를 모색하면서 성능, 보안 및 고가용성 개선
-   인라인 작업을 포함하는 권장 사항 가져오기

[Azure Portal](https://aka.ms/azureadvisordashboard)을 통해 Advisor에 액세스할 수 있습니다. [포털](https://portal.azure.com)에 로그인하고 **찾아보기**를 선택한 다음 **Azure Advisor**로 스크롤합니다. Advisor 대시보드에 선택한 구독에 대한 개인화된 권장 사항이 표시됩니다. 권장 사항은 네 가지 범주로 나뉩니다. 

-   **고가용성** – 업무상 중요한 응용 프로그램의 연속성을 보장하고 향상시키는 데 도움이 됩니다. 자세한 내용은 [Advisor 고가용성 권장 사항](advisor-high-availability-recommendations.md)을 참조하세요.

-   **보안** - 잠재적인 보안 위반으로 이어질 수 있는 위협 및 취약점을 검색합니다. 자세한 내용은 [Advisor 보안 권장 사항](advisor-security-recommendations.md)을 참조하세요.

-   **성능** - 응용 프로그램의 속도를 향상시킵니다. 자세한 내용은 [Advisor 성능 권장 사항](advisor-performance-recommendations.md)을 참조하세요.

-   **비용** – 전체 Azure 사용을 최적화하고 사용량을 줄입니다. 자세한 내용은 [Advisor 비용 권장 사항](advisor-cost-recommendations.md)을 참조하세요.

  ![Advisor 권장 사항 유형](./media/advisor-overview/advisor-all-tab-examples.png)

> [!NOTE]
> Azure Advisor는 **소유자**, **참가자** 또는 **읽기 권한자** 역할이 할당된 구독에 대한 권장 사항을 생성합니다.

권장 사항을 클릭하여 추가 정보를 볼 수 있습니다. 기회를 활용하거나 문제를 해결하기 위해 수행할 수 있는 작업에 대해 알아볼 수도 있습니다. Advisor는 인라인 작업 또는 설명서 링크가 있는 권장 사항을 제공합니다. 인라인 작업을 클릭하면 "유도 사용자 경험"에 따라 구현됩니다. 설명서 링크를 클릭하면 작업을 수동으로 구현하는 방법을 설명하는 문서로 이동됩니다. 

Advisor는 시간 단위로 권장 사항을 업데이트합니다. 권장 사항에 대해 즉각적인 작업을 수행하지 않으려는 경우 1시간 후에 다시 알리거나 닫을 수 있습니다. 

## <a name="frequently-asked-questions"></a>질문과 대답

### <a name="how-do-i-access-advisor"></a>Advisor에 액세스하려면 어떻게 해야 하나요?
Azure Portal을 통해 Advisor에 액세스할 수 있습니다. 포털에 로그인하고 **찾아보기**를 선택한 다음 **Azure Advisor**로 스크롤합니다. 가상 컴퓨터 리소스 블레이드를 통해 Advisor 권장 사항을 볼 수도 있습니다. 가상 컴퓨터를 선택하고 메뉴에서 Advisor 권장 사항으로 스크롤합니다. 

### <a name="what-permissions-do-i-need-to-access-advisor"></a>Advisor에 액세스하려면 어떤 권한이 필요하나요?

Advisor 권장 사항을 보려면 하나 이상의 구독에 액세스할 수 있어야 합니다. 사용자의 역할은 구독에 대해 **소유자**, **참가자** 또는 **읽기 권한자** 중 하나여야 합니다. 구독 소유자 및 참가자는 권장 사항을 계산/생성할 수 있습니다. 그러나 **읽기 권한자** 역할이 있는 경우 Advisor 권장 사항을 볼 수만 있습니다. 

### <a name="how-often-are-advisor-recommendations-updated"></a>Advisor 권장 사항은 얼마나 자주 업데이트되나요?

Advisor 권장 사항은 시간별로 업데이트됩니다.

### <a name="what-resources-does-advisor-provide-recommendations-for"></a>Advisor는 어떤 리소스에 대해 권장 사항을 제공하나요?

Advisor는 가상 컴퓨터, 가용성 집합, App Services, SQL Server, SQL Database 및 Redis Cache에 대한 권장 사항을 제공합니다.

### <a name="can-i-snooze-or-dismiss-a-recommendation"></a>권장 사항을 다시 알리거나 해제할 수 있나요?

권장 사항을 다시 알리거나 해제하려면 **다시 알림** 단추 또는 링크를 클릭합니다. 다시 알림 기간을 지정하거나, 권장 사항을 해제하려면 **안 함**을 선택합니다.

## <a name="next-steps"></a>다음 단계

Advisor 권장 사항에 대한 자세한 내용은 다음 리소스를 참조하세요.

-  [Advisor 시작](advisor-get-started.md)
-  [Advisor 고가용성 권장 사항](advisor-high-availability-recommendations.md)
-  [Advisor 보안 권장 사항](advisor-security-recommendations.md)
-  [Advisor 성능 권장 사항](advisor-performance-recommendations.md)
-  [Advisor 비용 권장 사항](advisor-cost-recommendations.md)



<!--HONumber=Nov16_HO4-->


