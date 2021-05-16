---
title: Azure Resource Graph의 Advisor 데이터
description: Azure Resource Graph의 Advisor 데이터 쿼리 만들기
ms.topic: article
ms.date: 03/12/2020
ms.author: sagupt
ms.openlocfilehash: 66bb500d419d1f5537afafd7a2df543ded8cc7ce
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "87057771"
---
# <a name="query-for-advisor-data-in-resource-graph-explorer-azure-resource-graph"></a>Resource Graph 탐색기의 Advisor 데이터 쿼리(Azure Resource Graph)

Advisor 리소스는 이제 [Azure Resource Graph로](https://azure.microsoft.com/features/resource-graph/) 온보딩됩니다. 이는 Advisor 권장 사항에 대한 다양한 대규모 고객 시나리오의 토대를 마련합니다. 다음은 이전에는 규모에 따라 수행할 수 없었지만 현재 Resource Graph를 사용하여 수행할 수 있는 몇 가지 시나리오입니다.
* Azure Portal의 모든 구독에 대해 복잡한 쿼리를 수행하는 기능 제공
* 범주 유형(예: 안정성, 성능) 및 영향 유형(높음, 중간, 낮음)으로 요약된 권장 사항
* 특정 권장 사항 유형에 대한 모든 권장 사항
* 권장 사항 범주별 영향을 받는 리소스 수

![Azure Resource Graph 탐색기의 Advisor](./media/azure-resource-graph-1.png)  


## <a name="advisor-resource-types-in-azure-graph"></a>Azure Graph의 Advisor 리소스 종류

[Resource Graph](../governance/resource-graph/index.yml)에서 사용 가능한 Advisor 리소스 종류: Advisor 리소스에서 쿼리하는 데 사용할 수 있는 리소스 종류는 3가지입니다. 다음은 Resource Graph에서 쿼리할 수 있는 리소스 목록입니다.
* Microsoft.Advisor/configurations
* Microsoft.Advisor/recommendations
* Microsoft.Advisor/suppressions

이러한 리소스 종류는 AdvisorResources로 이름이 지정된 새 테이블에 나열되며, Azure Portal의 Resource Graph 탐색기에서도 쿼리할 수 있습니다.


## <a name="next-steps"></a>다음 단계

Advisor 권장 사항에 대한 자세한 내용은 다음을 참조하세요.
* [Azure Advisor 소개](advisor-overview.md)
* [Advisor 시작](advisor-get-started.md)
* [Advisor 비용 권장 사항](advisor-cost-recommendations.md)
* [Advisor 안정성 권장 사항](advisor-high-availability-recommendations.md)
* [Advisor 성능 권장 사항](advisor-performance-recommendations.md)
* [Advisor 보안 권장 사항](advisor-security-recommendations.md)
* [Advisor 운영 우수성 권장 사항](advisor-operational-excellence-recommendations.md)
* [Advisor REST API](/rest/api/advisor/)
