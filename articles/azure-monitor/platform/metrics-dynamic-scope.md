---
title: 메트릭 탐색기에서 여러 리소스 보기
description: Azure Monitor에서 여러 리소스를 시각화 하는 방법을 알아봅니다 메트릭 탐색기
author: ritaroloff
services: azure-monitor
ms.topic: conceptual
ms.date: 12/14/2020
ms.author: riroloff
ms.subservice: metrics
ms.openlocfilehash: 724809dbce3ca1b5a36f4da0ba5c03d0f78897f5
ms.sourcegitcommit: 77ab078e255034bd1a8db499eec6fe9b093a8e4f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/16/2020
ms.locfileid: "97577699"
---
# <a name="viewing-multiple-resources-in-metrics-explorer"></a>메트릭 탐색기에서 여러 리소스 보기

리소스 범위 선택기를 사용 하면 동일한 구독 및 지역에 있는 여러 리소스에서 메트릭을 볼 수 있습니다. 다음은 Azure Monitor 메트릭 탐색기에서 여러 리소스를 보는 방법에 대 한 지침입니다. 

## <a name="selecting-a-resource"></a>리소스 선택 

**Azure Monitor** 메뉴 또는 리소스 메뉴의 **모니터링** 섹션에서 **메트릭** 를 선택합니다. "범위 선택" 단추를 클릭 하 여 리소스 범위 선택기를 열면 메트릭을 확인할 리소스를 선택할 수 있습니다. 리소스의 메뉴에서 메트릭 탐색기를 연 경우에는 이미 채워야 합니다. 

![빨간색으로 강조 표시 된 리소스 범위 선택의 스크린샷](./media/metrics-charts/019.png)

## <a name="selecting-multiple-resources"></a>여러 리소스 선택 

일부 리소스 종류는 동일한 구독 및 위치에 있는 한 여러 리소스에 대해 메트릭을 쿼리 하는 기능을 사용 하도록 설정 했습니다. 이러한 리소스 형식은 "리소스 종류" 드롭다운에서 찾을 수 있습니다. 

![다중 리소스 호환 리소스의 드롭다운을 보여 주는 스크린샷 ](./media/metrics-charts/020.png)

> [!WARNING] 
> 여러 리소스, 리소스 그룹 또는 구독에 대 한 메트릭을 시각화 하려면 구독 수준에서의 모니터링 판독기 권한이 있어야 합니다. 이 작업을 수행 하려면 [이 문서의](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal)지침을 따르세요.

여러 리소스에 대 한 메트릭을 시각화 하려면 리소스 범위 선택에서 여러 리소스를 선택 하 여 시작 합니다. 

![여러 리소스를 선택 하는 방법을 보여 주는 스크린샷](./media/metrics-charts/021.png)

> [!NOTE]
> 동일한 리소스 유형, 위치 및 구독 내에서 여러 리소스를 선택할 수 있습니다. 이 조건 외부의 리소스는 선택할 수 없습니다. 

선택이 완료 되 면 "적용" 단추를 클릭 하 여 선택 항목을 저장 합니다. 

## <a name="selecting-a-resource-group-or-subscription"></a>리소스 그룹 또는 구독 선택 

> [!WARNING]
> 여러 리소스, 리소스 그룹 또는 구독에 대 한 메트릭을 시각화 하려면 구독 수준에서의 모니터링 판독기 권한이 있어야 합니다. 

다중 리소스 호환 형식의 경우 구독 또는 여러 리소스 그룹에서 메트릭을 쿼리할 수도 있습니다. 먼저 구독 또는 하나 이상의 리소스 그룹을 선택 합니다. 

![여러 리소스 그룹에 대해 쿼리 하는 방법을 보여 주는 스크린샷 ](./media/metrics-charts/022.png)

그런 다음, 리소스 종류 및 위치를 선택 해야 새 범위를 계속 적용할 수 있습니다. 

![선택한 리소스 그룹을 보여 주는 스크린샷 ](./media/metrics-charts/023.png)

또한 선택한 범위를 확장 하 여이가 적용 될 리소스를 확인할 수 있습니다.

![그룹 내에서 선택한 리소스를 보여 주는 스크린샷 ](./media/metrics-charts/024.png)

범위 선택을 마치면 "적용"을 클릭 하 여 선택 내용을 저장 합니다. 

## <a name="splitting-and-filtering-by-resource-group-or-resources"></a>리소스 그룹 또는 리소스를 기준으로 분할 및 필터링

리소스를 플롯 한 후 분할 및 필터링 도구를 사용 하 여 데이터에 대 한 자세한 정보를 얻을 수 있습니다. 

분할을 사용 하면 메트릭의 여러 세그먼트를 서로 비교 하는 방법을 시각화할 수 있습니다. 예를 들어 여러 리소스에 대 한 메트릭을 그릴 때 "분할 적용" 도구를 사용 하 여 리소스 id 또는 리소스 그룹 별로 분할할 수 있습니다. 이렇게 하면 여러 리소스나 리소스 그룹에서 단일 메트릭을 쉽게 비교할 수 있습니다.  

예를 들어, 다음은 9VMs 전반의 CPU 백분율 차트입니다. 리소스 id를 기준으로 분할 하 여 VM 당 CPU의 비율 차이를 쉽게 확인할 수 있습니다. 

![분할을 사용 하 여 VM 당 CPU 백분율을 확인 하는 방법을 보여 주는 스크린샷](./media/metrics-charts/026.png)

분할 외에도 필터링 기능을 사용 하 여 보려는 리소스 그룹만 표시할 수 있습니다.  예를 들어 특정 리소스 그룹의 Vm에 대 한 CPU 비율을 확인 하려는 경우 "필터 추가" 도구를 사용 하 여 리소스 그룹별로 필터링 할 수 있습니다. 이 예제에서는 TailspinToysDemo에서 리소스와 연결 된 메트릭을 제거 하는 명인를 기준으로 필터링 합니다. 

![리소스 그룹별로 필터링 하는 방법을 보여 주는 스크린샷](./media/metrics-charts/027.png)

## <a name="pinning-your-multi-resource-charts"></a>다중 리소스 차트 고정 

> [!WARNING] 
> 여러 리소스, 리소스 그룹 또는 구독에 대 한 메트릭을 시각화 하려면 구독 수준에서의 모니터링 판독기 권한이 있어야 합니다. 이 작업을 수행 하려면 [이 문서의](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal)지침을 따르세요. 

다중 리소스 차트를 고정 하려면 [여기](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-charts#create-alert-rules)의 지침을 따르세요. 

## <a name="next-steps"></a>다음 단계

* [메트릭 탐색기 문제 해결](metrics-troubleshoot.md)
* [Azure 서비스에 사용 가능한 메트릭 목록 보기](metrics-supported.md)
* [구성된 차트 예제 보기](metric-chart-samples.md)

