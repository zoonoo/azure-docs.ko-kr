---
title: Azure Resource Graph의 Advisor 데이터
description: Azure 리소스 그래프에서 관리자 데이터에 대 한 쿼리 만들기
ms.topic: article
ms.date: 03/12/2020
ms.author: sagupt
ms.openlocfilehash: c0786d1d09ff61ddd9c375c68b7199521e319a4f
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85117837"
---
# <a name="query-for-advisor-data-in-resource-graph-explorer-azure-resource-graph"></a>리소스 그래프 탐색기에서 관리자 데이터에 대 한 쿼리 (Azure 리소스 그래프)

Advisor 리소스는 이제 [Azure 리소스 그래프로](https://azure.microsoft.com/features/resource-graph/)등록 됩니다. 이는 Advisor 권장 사항에 대 한 다양 한 규모의 고객 시나리오에 대 한 토대를 제공 합니다. 규모에서 수행 하기 전에 불가능 했던 몇 가지 시나리오는 다음과 같습니다. 리소스 그래프를 사용 하 여 달성할 수 있습니다.
* Azure Portal의 모든 구독에 대해 복잡 한 쿼리를 수행 하는 기능을 제공 합니다.
* 범주 유형 (예: 안정성, 성능) 및 영향 유형 (높음, 중간, 낮음)으로 요약 된 권장 사항
* 특정 권장 사항 유형에 대 한 모든 권장 사항
* 권장 사항 범주별 영향을 받는 리소스 수 범주별

![Azure 리소스 그래프 탐색기의 Advisor](./media/azure-resource-graph-1.png)  


## <a name="advisor-resource-types-in-azure-graph"></a>Azure Graph의 Advisor 리소스 유형

[리소스 그래프](https://docs.microsoft.com/azure/governance/resource-graph/)에서 사용 가능한 Advisor 리소스 유형: advisor 리소스에서 쿼리 하는 데 사용할 수 있는 리소스 유형으로는 3 가지가 있습니다. 다음은 리소스 그래프에서 쿼리할 수 있는 리소스 목록입니다.
* Microsoft Advisor/구성
* Microsoft Advisor/권장 사항
* Microsoft Advisor/비 표시 오류

이러한 리소스 유형은 AdvisorResources로 이름이 지정 된 새 테이블에 나열 되며, Azure Portal의 리소스 그래프 탐색기에서 쿼리할 수도 있습니다.


## <a name="next-steps"></a>다음 단계

Advisor 권장 사항에 대한 자세한 내용은 다음을 참조하세요.
* [Azure Advisor 소개](advisor-overview.md)
* [Advisor 시작](advisor-get-started.md)
* [Advisor 비용 권장 사항](advisor-cost-recommendations.md)
* [Advisor 안정성 권장 사항](advisor-high-availability-recommendations.md)
* [Advisor 성능 권장 사항](advisor-performance-recommendations.md)
* [Advisor 보안 권장 사항](advisor-security-recommendations.md)
* [Advisor 작동의 뛰어난 권장 사항](advisor-operational-excellence-recommendations.md)
* [Advisor REST API](https://docs.microsoft.com/rest/api/advisor/)
