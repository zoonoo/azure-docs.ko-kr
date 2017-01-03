---
title: "Azure Advisor 보안 권장 사항 | Microsoft Docs"
description: "Azure Advisor를 사용하여 Azure 배포의 보안을 향상시킵니다."
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
ms.sourcegitcommit: a560aa9cc6c70fd3827f649627466c9071b8ab8b
ms.openlocfilehash: 4345e544d2e2bcc90ee2e3caf5bf6dfc19f01ca3

---
# <a name="advisor-security-recommendations"></a>Advisor 보안 권장 사항 관리

Advisor는 모든 Azure 리소스에 대한 권장 사항을 일관되고 통합된 보기로 표시합니다. 보안 권장 사항을 제공하기 위해 Azure Security Center와 통합됩니다. Advisor 대시보드의 **보안** 탭에서 보안 관련 권장 지침을 얻을 수 있습니다.

![Advisor 보안 탭](./media/advisor-security-recommendations/advisor-security-tab.png)

보안 센터는 Azure 리소스의 보안에 대한 향상된 가시성과 제어권을 통해 위협을 예방하고 감지하며 위협에 대응하는 데 도움이 됩니다. 여기서는 Azure 리소스의 보안 상태를 주기적으로 분석합니다. 보안 센터가 잠재적인 보안 취약점을 식별하는 경우 권장 사항을 만듭니다. 권장 사항은 필요한 컨트롤을 구성하는 과정을 안내합니다. 

![Advisor 보안 탭](./media/advisor-security-recommendations/advisor-security-recommendations.png)

보안 권장 사항에 대한 자세한 내용은 [Azure Security Center에서 보안 권장 사항 관리](https://azure.microsoft.com/en-us/documentation/articles/security-center-recommendations/)를 참조하세요.

## <a name="how-to-access-security-recommendations-in-azure-advisor"></a>Azure Advisor에서 보안 권장 사항에 액세스하는 방법

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. 왼쪽 탐색 창에서 **더 많은 서비스**를 클릭하고 서비스 메뉴 창에서 **모니터링 및 관리**로 스크롤한 후 **Azure Advisor**를 클릭합니다. 그러면 Advisor 대시보드가 시작됩니다. 
3. Advisor 대시보드에서 **보안** 탭을 클릭하고 권장 사항을 받아보려는 구독을 선택합니다.

> [!NOTE]
> Advisor는 **소유자, 참가자 또는 읽기 권한자** 역할이 할당된 구독에 대한 권장 사항을 생성합니다.  

## <a name="next-steps"></a>다음 단계

Advisor 권장 사항에 대한 자세한 내용은 다음 리소스를 참조하세요.
-  [Advisor 고가용성 권장 사항](advisor-high-availability-recommendations.md)
-  [Advisor 성능 권장 사항](advisor-performance-recommendations.md)
-  [Advisor 비용 권장 사항](advisor-performance-recommendations.md)
 



<!--HONumber=Nov16_HO3-->


