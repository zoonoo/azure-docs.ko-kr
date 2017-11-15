---
title: "Azure Advisor 비용 권장 사항 | Microsoft Docs"
description: "Azure Advisor를 사용하여 Azure 배포 비용을 최적화합니다."
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
ms.openlocfilehash: 7b9c7037271fabd67c1ada80420ad72c340e46bb
ms.sourcegitcommit: ce934aca02072bdd2ec8d01dcbdca39134436359
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/08/2017
---
# <a name="advisor-cost-recommendations"></a>Advisor 비용 권장 사항

Advisor는 유휴 및 사용 미달 리소스를 식별하여 전체적인 Azure 사용을 최적화하고 줄이는 데 도움을 줍니다. Advisor 대시보드의 **비용** 탭에서 비용 관련 권장 지침을 얻을 수 있습니다.

## <a name="optimize-virtual-machine-spend-by-resizing-or-shutting-down-underutilized-instances"></a>사용량이 낮은 인스턴스의 크기를 조정하거나 종료하여 가상 컴퓨터 소비 최적화 
특정 응용 프로그램 시나리오에서는 기본적으로 사용률이 낮을 수 있으나 가상 컴퓨터의 크기와 수를 관리하여 비용을 절감할 수도 있습니다. Advisor는 14일 동안 가상 컴퓨터 사용량을 모니터링하고 사용률이 낮은 가상 컴퓨터를 식별합니다. 4일 이상 CPU 사용률이 5% 이하이고 네트워크 사용량이 7MB 이하인 가상 컴퓨터는 사용률이 낮은 가상 컴퓨터로 간주됩니다.

Advisor에는 가상 컴퓨터를 계속 실행할 때의 예상 비용이 표시되므로 해당 가상 컴퓨터를 종료하거나 크기를 조정하도록 선택할 수 있습니다.

사용량이 낮은 가상 컴퓨터를 더 적극적으로 식별하고 싶은 경 구독당 평균 CPU 사용률 규칙을 조정할 수 있습니다.

## <a name="use-a-cost-effective-solution-to-manage-performance-goals-of-multiple-sql-databases"></a>비용 효율적인 솔루션을 사용하여 여러 SQL Database의 성능 목표 관리
Advisor는 Elastic Database 풀을 만들 경우 도움이 되는 SQL Server 인스턴스를 식별합니다. Elastic Database 풀은 다양한 사용 패턴을 지닌 여러 데이터베이스에 대한 성능 목표를 관리하기 위한 간단하고 비용 효율적인 솔루션을 제공합니다. Azure 탄력적 풀에 대한 자세한 내용은 [Azure 탄력적 풀이란?](https://azure.microsoft.com/en-us/documentation/articles/sql-database-elastic-pool/)을 참조하세요.

## <a name="reduce-costs-by-eliminating-unprovisioned-expressroute-circuits"></a>프로비저닝되지 않은 ExpressRoute 회로를 제거하여 비용 절감
Advisor는 공급자 상태가 1개월 이상 *프로비저닝되지 않음*인 ExpressRoute 회로를 식별하고, 연결 공급자로 회로를 프로비저닝할 계획이 아닌 경우 회로를 삭제할 것을 권장합니다.

## <a name="how-to-access-cost-recommendations-in-azure-advisor"></a>Azure Advisor에서 비용 권장 사항에 액세스하는 방법

1. [Azure Portal](https://portal.azure.com)에 로그인하고 [Advisor](https://aka.ms/azureadvisordashboard)를 엽니다.

2.  Advisor 대시보드에서 **비용** 탭을 클릭합니다.

## <a name="next-steps"></a>다음 단계

Advisor 권장 사항에 대한 자세한 내용은 다음을 참조하세요.
* [Advisor 소개](advisor-overview.md)
* [시작](advisor-get-started.md)
* [Advisor 성능 권장 사항](advisor-cost-recommendations.md)
* [Advisor 고가용성 권장 사항](advisor-cost-recommendations.md)
* [Advisor 보안 권장 사항](advisor-cost-recommendations.md)
