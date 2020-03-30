---
title: Azure 리소스 그래프의 관리자 데이터
description: Azure 리소스 그래프에서 어드바이저 데이터에 대한 쿼리 만들기
ms.topic: article
ms.date: 03/12/2020
ms.author: sagupt
ms.openlocfilehash: f8ad8fd450bc004d9caa2699922717f38d38b482
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79502452"
---
# <a name="query-for-advisor-data-in-resource-graph-explorer-azure-resource-graph"></a>리소스 그래프 탐색기의 관리자 데이터에 대한 쿼리(Azure 리소스 그래프)

이제 어드바이저 리소스가 [Azure 리소스 그래프에](https://azure.microsoft.com/features/resource-graph/)온보기됩니다. 이는 Advisor 권장 사항에 대한 많은 대규모 고객 시나리오에 기반을 두게 됩니다. 이전에는 대규모로 수행할 수 없었던 몇 가지 시나리오가 리소스 그래프를 사용하여 수행할 수 있습니다.
* Azure 포털의 모든 구독에 대해 복잡한 쿼리를 수행할 수 있는 기능 제공
* 범주 유형(예: 고가용성, 성능) 및 영향 유형(높음, 중간, 낮음)으로 요약된 권장 사항
* 특정 권장 사항 유형에 대한 모든 권장 사항
* 추천 범주별 영향을 받는 리소스 수

![Azure 리소스 그래프 탐색기의 고문](./media/azure-resource-graph-1.png)  


## <a name="advisor-resource-types-in-azure-graph"></a>Azure 그래프의 관리자 리소스 유형

[리소스 그래프에서](https://docs.microsoft.com/azure/governance/resource-graph/)사용 가능한 Advisor 리소스 유형 : Advisor 리소스에서 쿼리할 수 있는 3가지 리소스 유형이 있습니다. 리소스 그래프에서 쿼리할 수 있는 리소스 목록은 다음과 같습니다.
* 마이크로소프트.어드바이저/구성
* 마이크로소프트.어드바이저/권장 사항
* 마이크로소프트.어드바이저/억제

이러한 리소스 형식은 AdvisorResources라는 새 테이블 아래에 나열되며 Azure 포털의 리소스 그래프 탐색기에서도 쿼리할 수 있습니다.


## <a name="next-steps"></a>다음 단계

Advisor 권장 사항에 대한 자세한 내용은 다음을 참조하세요.
* [Azure Advisor 소개](advisor-overview.md)
* [Advisor 시작](advisor-get-started.md)
* [Advisor 비용 권장 사항](advisor-cost-recommendations.md)
* [Advisor 성능 권장 사항](advisor-performance-recommendations.md)
* [Advisor 보안 권장 사항](advisor-security-recommendations.md)
* [고문 운영 우수성 권장 사항](advisor-operational-excellence-recommendations.md)
* [어드바이저 레스트 API](https://docs.microsoft.com/rest/api/advisor/)
