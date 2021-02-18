---
title: Azure 메트릭 탐색기에서 여러 리소스 보기
description: Azure 메트릭 탐색기를 사용 하 여 여러 리소스를 시각화 하는 방법을 알아봅니다.
author: ritaroloff
services: azure-monitor
ms.topic: conceptual
ms.date: 12/14/2020
ms.author: riroloff
ms.subservice: metrics
ms.openlocfilehash: 176acea34dead929986acb807b465593f9ffda5a
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/17/2021
ms.locfileid: "100615124"
---
# <a name="view-multiple-resources-in-the-azure-metrics-explorer"></a>Azure 메트릭 탐색기에서 여러 리소스 보기

리소스 범위 선택기를 사용 하면 동일한 구독 및 지역에 있는 여러 리소스에서 메트릭을 볼 수 있습니다. 이 문서에서는 Azure Monitor의 Azure 메트릭 탐색기 기능을 사용 하 여 여러 리소스를 보는 방법을 설명 합니다. 

## <a name="select-a-resource"></a>리소스 선택 

**Azure Monitor** 메뉴 또는 리소스 메뉴의 **모니터링** 섹션에서 **메트릭** 를 선택합니다. 그런 다음 **범위 선택** 을 선택 하 여 범위 선택기를 엽니다. 

범위 선택기를 사용 하 여 메트릭이 표시 되는 리소스를 선택 합니다. 리소스의 메뉴에서 메트릭 탐색기를 연 경우 범위를 채워야 합니다. 

![리소스 범위 선택을 여는 방법을 보여 주는 스크린샷](./media/metrics-dynamic-scope/019.png)

## <a name="select-multiple-resources"></a>여러 리소스 선택 

일부 리소스 종류는 여러 리소스에 대 한 메트릭을 쿼리할 수 있습니다. 메트릭은 동일한 구독 및 위치 내에 있어야 합니다. **리소스 종류** 메뉴의 맨 위에서 이러한 리소스 종류를 찾습니다.

![여러 리소스와 호환 되는 리소스의 메뉴를 보여 주는 스크린샷](./media/metrics-dynamic-scope/020.png)

> [!WARNING] 
> 여러 리소스, 리소스 그룹 또는 구독에 대 한 메트릭을 시각화 하려면 구독 수준에서의 모니터링 판독기 권한이 있어야 합니다. 자세한 내용은 [Azure Portal를 사용 하 여 Azure 역할 할당 추가 또는 제거](../../role-based-access-control/role-assignments-portal.md)를 참조 하세요.

여러 리소스에 대 한 메트릭을 시각화 하려면 리소스 범위 선택에서 여러 리소스를 선택 하 여 시작 합니다. 

![여러 리소스를 선택 하는 방법을 보여 주는 스크린샷](./media/metrics-dynamic-scope/021.png)

> [!NOTE]
> 선택한 리소스는 동일한 리소스 유형, 위치 및 구독 내에 있어야 합니다. 이러한 조건에 맞지 않는 리소스는 선택할 수 없습니다. 

완료 되 면 **적용** 을 선택 하 여 선택 내용을 저장 합니다. 

## <a name="select-a-resource-group-or-subscription"></a>리소스 그룹 또는 구독 선택 

> [!WARNING]
> 여러 리소스, 리소스 그룹 또는 구독에 대 한 메트릭을 시각화 하려면 구독 수준에서의 모니터링 판독기 권한이 있어야 합니다. 

여러 리소스와 호환 되는 유형의 경우 구독 또는 여러 리소스 그룹에서 메트릭을 쿼리할 수 있습니다. 먼저 구독 또는 하나 이상의 리소스 그룹을 선택 합니다. 

![여러 리소스 그룹에 대해 쿼리 하는 방법을 보여 주는 스크린샷](./media/metrics-dynamic-scope/022.png)

리소스 종류 및 위치를 선택 합니다. 

![선택한 리소스 그룹을 보여 주는 스크린샷](./media/metrics-dynamic-scope/023.png)

선택한 범위를 확장 하 여 선택 항목이 적용 되는 리소스를 확인할 수 있습니다.

![그룹 내에서 선택한 리소스를 보여 주는 스크린샷](./media/metrics-dynamic-scope/024.png)

범위 선택을 마치면 **적용** 을 선택 합니다. 

## <a name="split-and-filter-by-resource-group-or-resources"></a>리소스 그룹 또는 리소스를 기준으로 분할 및 필터링

리소스를 플롯 한 후 분할 및 필터링을 사용 하 여 데이터에 대 한 자세한 정보를 얻을 수 있습니다. 

분할을 사용 하면 메트릭의 여러 세그먼트를 서로 비교 하는 방법을 시각화할 수 있습니다. 예를 들어 여러 리소스에 대 한 메트릭을 그릴 때 분할 **적용** 을 선택 하 여 리소스 ID 또는 리소스 그룹별로 분할할 수 있습니다. 분할을 사용 하면 여러 리소스나 리소스 그룹에서 단일 메트릭을 비교할 수 있습니다.  

예를 들어 다음 차트는 9 개 Vm에서 CPU 백분율을 보여 줍니다. 리소스 ID로 분할 하는 경우 VM에서 CPU가 어떻게 다른 지 확인할 수 있습니다. 

![분할을 사용 하 여 Vm 전반의 CPU 비율을 확인 하는 방법을 보여 주는 스크린샷](./media/metrics-dynamic-scope/026.png)

분할과 함께 필터링을 사용 하 여 보려는 리소스 그룹만 표시할 수 있습니다.  예를 들어 특정 리소스 그룹의 Vm에 대 한 CPU 비율을 보려면 **필터 추가** 를 선택 하 여 리소스 그룹별로 필터링 할 수 있습니다. 

이 예제에서는 TailspinToysDemo를 기준으로 필터링 합니다. 여기서 필터는 명인의 리소스와 관련 된 메트릭을 제거 합니다. 

![리소스 그룹별로 필터링 하는 방법을 보여 주는 스크린샷](./media/metrics-dynamic-scope/027.png)

## <a name="pin-multiple-resource-charts"></a>여러 리소스 차트 고정 

리소스 그룹 및 구독 간의 메트릭을 시각화 하는 여러 리소스 차트를 사용 하려면 사용자에 게 구독 수준에서의 *모니터링 판독기* 권한이 있어야 합니다. 여러 리소스 차트를 고정 하는 대시보드의 모든 사용자에 게 충분 한 권한이 있는지 확인 합니다. 자세한 내용은 [Azure Portal를 사용 하 여 Azure 역할 할당 추가 또는 제거](../../role-based-access-control/role-assignments-portal.md)를 참조 하세요.

여러 리소스 차트를 대시보드에 고정 하려면 [대시보드에 고정](../essentials/metrics-charts.md#pinning-to-dashboards)을 참조 하세요. 

## <a name="next-steps"></a>다음 단계

* [메트릭 탐색기 문제 해결](../essentials/metrics-troubleshoot.md)
* [Azure 서비스에 사용 가능한 메트릭 목록 보기](../platform/metrics-supported.md)
* [구성된 차트 예제 보기](../essentials/metric-chart-samples.md)