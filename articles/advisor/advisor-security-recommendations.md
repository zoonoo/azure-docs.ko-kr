---
title: "Azure Advisor 보안 권장 사항 | Microsoft Docs"
description: "Azure Advisor를 사용하여 Azure 배포의 보안을 향상시킵니다."
services: advisor
documentationcenter: NA
author: KumudD
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
ms.translationtype: Human Translation
ms.sourcegitcommit: 197ebd6e37066cb4463d540284ec3f3b074d95e1
ms.openlocfilehash: 53be05593c3733ccb27979a3a026414013125779
ms.contentlocale: ko-kr
ms.lasthandoff: 03/31/2017

---
# <a name="advisor-security-recommendations"></a>Advisor 보안 권장 사항 관리

Azure Advisor는 모든 Azure 리소스에 대한 권장 사항을 일관되고 통합된 보기로 표시합니다. 보안 권장 사항을 제공하기 위해 Azure Security Center와 통합됩니다. Advisor 대시보드의 **보안** 탭에서 보안 관련 권장 지침을 얻을 수 있습니다.

![Advisor 보안 단추](./media/advisor-security-recommendations/advisor-security-tab.png)

보안 센터는 Azure 리소스의 보안에 대한 향상된 가시성과 제어권을 통해 위협을 예방하고 감지하며 위협에 대응하는 데 도움이 됩니다. 여기서는 Azure 리소스의 보안 상태를 주기적으로 분석합니다. 보안 센터가 잠재적인 보안 취약점을 식별하는 경우 권장 사항을 만듭니다. 권장 사항은 필요한 컨트롤을 구성하는 과정을 안내합니다. 

![Advisor 보안 탭](./media/advisor-security-recommendations/advisor-security-recommendations.png)

보안 권장 사항에 대한 자세한 내용은 [Azure Security Center에서 보안 권장 사항 관리](https://azure.microsoft.com/en-us/documentation/articles/security-center-recommendations/)를 참조하세요.

## <a name="how-to-access-security-recommendations-in-azure-advisor"></a>Azure Advisor에서 보안 권장 사항에 액세스하는 방법

1. [Azure 포털](https://portal.azure.com)에 로그인합니다.

2. 왼쪽 창에서 **더 많은 서비스**를 클릭합니다.

3. 서비스 메뉴 창의 **모니터링 및 관리** 아래에서 **Azure Advisor**를 클릭합니다.  
 Advisor 대시보드가 표시됩니다.

4. Advisor 대시보드에서 **보안** 탭을 클릭합니다.

5. 권장 사항을 받아보려는 구독을 선택하고 **권장 사항 가져오기**를 클릭합니다.

> [!NOTE]
> Advisor 권장 사항에 액세스하려면 먼저 Advisor에 *구독을 등록*해야 합니다. 구독은 *구독 소유자*가 Advisor 대시보드를 시작하고 **권장 사항 가져오기** 단추를 클릭할 때 등록됩니다. 이 작업은 *한 번만* 수행하면 됩니다. 구독이 등록된 후 구독, 리소스 그룹 또는 특정 리소스에 대한 *소유자*, *참여자* 또는 *리더*로 Advisor 권장 사항에 액세스할 수 있습니다.

## <a name="next-steps"></a>다음 단계

Advisor 권장 사항에 대한 자세한 내용은 다음을 참조하세요.
* [Advisor 소개](advisor-overview.md)
* [Advisor 시작](advisor-get-started.md)
* [Advisor 비용 권장 사항](advisor-performance-recommendations.md)
* [Advisor 성능 권장 사항](advisor-performance-recommendations.md)
* [Advisor 고가용성 권장 사항](advisor-high-availability-recommendations.md)


 

