---
title: 워크로드 관리 포털 모니터링
description: Azure Synapse 분석에서 워크로드 관리 포털 모니터링에 대한 지침입니다.
services: synapse-analytics
author: ronortloff
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 02/04/2020
ms.author: rortloff
ms.reviewer: jrasnick
ms.custom: azure-synapse
ms.openlocfilehash: b1f21a996f7394def4d6b1e8bde9a5ccdf703dbb
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/03/2020
ms.locfileid: "80632416"
---
# <a name="azure-synapse-analytics--workload-management-portal-monitoring-preview"></a>Azure 시냅스 분석 – 워크로드 관리 포털 모니터링(미리 보기)

이 문서에서는 [워크로드 그룹](sql-data-warehouse-workload-isolation.md#workload-groups) 리소스 사용률 및 쿼리 활동을 모니터링하는 방법을 설명합니다.
Azure 메트릭 탐색기를 구성하는 방법에 대한 자세한 내용은 Azure 메트릭 탐색기로 시작하기 문서를 [참조하세요.](../../azure-monitor/platform/metrics-getting-started.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)  시스템 리소스 사용량을 모니터링하는 방법에 대한 자세한 내용은 Azure Synapse 분석 모니터링 설명서의 [리소스 사용률](sql-data-warehouse-concept-resource-utilization-query-activity.md#resource-utilization) 섹션을 참조하십시오.
워크로드 관리 모니터링을 위해 제공되는 워크로드 그룹 메트릭에는 리소스 할당 및 쿼리 활동의 두 가지 범주가 있습니다.  이러한 메트릭은 워크로드 그룹별로 분할 및 필터링할 수 있습니다.  메트릭은 시스템 정의(리소스 클래스 워크로드 그룹) 또는 사용자 [정의(CREATE 워크로드 그룹](https://docs.microsoft.com/sql/t-sql/statements/create-workload-group-transact-sql?view=azure-sqldw-latest) 구문을 사용하는 사용자가 만든)인지에 따라 분할 및 필터링할 수 있습니다.

## <a name="workload-management-metric-definitions"></a>워크로드 관리 메트릭 정의

|메트릭 이름                    |설명  |집계 형식 |
|-------------------------------|-------------|-----------------|
|유효 한도 리소스 백분율 | *유효 한도 리소스 백분율은* 다른 워크로드 그룹에 할당된 *유효 최소 리소스 백분율을* 고려하여 워크로드 그룹에서 액세스할 수 있는 리소스 비율에 대한 하드 제한입니다. *유효 한도 리소스 백분율* 메트릭은 `CAP_PERCENTAGE_RESOURCE` CREATE [워크로드 그룹](https://docs.microsoft.com/sql/t-sql/statements/create-workload-group-transact-sql?view=azure-sqldw-latest) 구문의 매개 변수를 사용하여 구성됩니다.  유효 값은 여기에 설명되어 있습니다.<br><br>예를 들어 워크로드 `DataLoads` 그룹이 = `CAP_PERCENTAGE_RESOURCE` 100으로 만들어지고 다른 워크로드 그룹이 유효 최소 리소스 백분율이 25%인 경우 `DataLoads` 워크로드 그룹의 *유효 한도 리소스 백분율은* 75%입니다.<br><br>*유효 한도 리소스 백분율은* 워크로드 그룹이 달성할 수 있는 동시성(따라서 잠재적인 처리량)의 상한을 결정합니다.  *현재 유효 한도 리소스 백분율* 메트릭에서 보고하는 것 이상으로 추가 `CAP_PERCENTAGE_RESOURCE`처리량이 `MIN_PERCENTAGE_RESOURCE` 필요한 경우 , 을 증가시키고 다른 워크로드 그룹을 줄이거나 인스턴스를 확장하여 리소스를 추가합니다.  을 `REQUEST_MIN_RESOURCE_GRANT_PERCENT` 줄이면 동시성을 높일 수 있지만 전체 처리량은 증가하지 않을 수 있습니다.| 최소, 평균, 최대, 평균, 최대, |
|유효 최소 리소스 백분율 |*유효 최소 리소스 백분율은* 서비스 수준 최소를 고려하여 워크로드 그룹에 대해 예약되고 격리된 리소스의 최소 백분율입니다.  유효 최소 리소스 백분율 메트릭은 `MIN_PERCENTAGE_RESOURCE` [CREATE 워크로드 그룹](https://docs.microsoft.com/sql/t-sql/statements/create-workload-group-transact-sql?view=azure-sqldw-latest) 구문의 매개 변수를 사용하여 구성됩니다.  유효 값은 [여기에](https://docs.microsoft.com/sql/t-sql/statements/create-workload-group-transact-sql?view=azure-sqldw-latest#effective-values)설명되어 있습니다.<br><br>이 메트릭이 필터링되지 않고 분할되지 않은 경우 합계 집계 유형을 사용하여 시스템에서 구성된 총 워크로드 격리를 모니터링합니다.<br><br>*유효 최소 리소스 백분율은* 워크로드 그룹이 달성할 수 있는 보장된 동시성(따라서 보장된 처리량)의 하한을 결정합니다.  *현재 유효 최소 리소스 백분율* 메트릭에 의해 보고된 것 이상으로 `MIN_PERCENTAGE_RESOURCE` 보장된 추가 리소스가 필요한 경우 워크로드 그룹에 대해 구성된 매개 변수를 늘립니다.  을 `REQUEST_MIN_RESOURCE_GRANT_PERCENT` 줄이면 동시성을 높일 수 있지만 전체 처리량은 증가하지 않을 수 있습니다. |최소, 평균, 최대, 평균, 최대,|
|워크로드 그룹 활성 쿼리  |이 메트릭은 워크로드 그룹 내의 활성 쿼리를 보고합니다.  이 메트릭을 사용하면 필터링되지 않고 분할되지 않은 모든 활성 쿼리가 시스템에서 실행됩니다.|합계         |
|워크로드 그룹 최대 리소스 백분율별 할당 |이 메트릭은 워크로드 그룹당 *유효 한도 리소스 백분율을* 기준으로 리소스 의 백분율 할당을 표시합니다.  이 메트릭은 워크로드 그룹의 효과적인 활용도를 제공합니다.<br><br>*유효 한도 리소스 백분율이* 75%이고 25%로 `REQUEST_MIN_RESOURCE_GRANT_PERCENT` 구성된 워크로드 그룹을 `DataLoads` 고려합니다.  `DataLoads` *필터링된 최대 리소스 백분율 값별 워크로드 그룹 할당은* 33%(25% / 75%)입니다. 이 워크로드 그룹에서 단일 쿼리가 실행중인 경우<br><br>이 메트릭을 사용하여 워크로드 그룹의 사용률을 식별합니다.  값이 100%에 가까우면 워크로드 그룹에서 사용할 수 있는 모든 리소스가 사용 중임을 나타냅니다.  또한 *워크로드 그룹 큐에 대기된* 동일한 워크로드 그룹에 대해 0보다 큰 값을 표시하는 쿼리 메트릭은 워크로드 그룹이 할당된 경우 추가 리소스를 활용함을 나타냅니다.  반대로 이 메트릭이 일관되게 낮고 *워크로드 그룹 활성 쿼리가* 낮으면 워크로드 그룹이 활용되지 않습니다.  이 상황은 유효 *캡 리소스 백분율이* 0보다 큰 경우 [특히](#underutilized-workload-isolation)문제가 됩니다.|최소, 평균, 최대, 평균, 최대, |
|시스템 백분율별 워크로드 그룹 할당 | 이 메트릭은 전체 시스템을 기준으로 리소스의 백분율 할당을 표시합니다.<br><br>25%로 `DataLoads` `REQUEST_MIN_RESOURCE_GRANT_PERCENT` 구성된 워크로드 그룹을 고려합니다.  *시스템 백분율 값별 워크로드 그룹 할당은* `DataLoads` 25%(25%/100%)가 됩니다. 이 워크로드 그룹에서 단일 쿼리가 실행중인 경우|최소, 평균, 최대, 평균, 최대, |
|워크로드 그룹 쿼리 시간 시간 |시간 만료된 워크로드 그룹에 대한 쿼리입니다.  이 메트릭에 의해 보고된 쿼리 시간 시간은 쿼리실행이 시작된 후에만 실행됩니다(잠금 또는 리소스 대기로 인한 대기 시간은 포함되지 않음).<br><br>쿼리 시간 표시는 워크로드 `QUERY_EXECUTION_TIMEOUT_SEC` 그룹 [만들기](https://docs.microsoft.com/sql/t-sql/statements/create-workload-group-transact-sql?view=azure-sqldw-latest) 구문의 매개 변수를 사용하여 구성됩니다.  값을 늘리면 쿼리 시간 시간 시간이 줄어들 수 있습니다.<br><br>시간 시간 `REQUEST_MIN_RESOURCE_GRANT_PERCENT` 시간을 줄이고 쿼리당 더 많은 리소스를 할당하기 위해 워크로드 그룹에 대한 매개 변수를 늘리는 것이 좋습니다.  증가하면 `REQUEST_MIN_RESOURCE_GRANT_PERCENT` 워크로드 그룹의 동시성 양이 줄어듭니다. |합계 |
|워크로드 그룹 큐에 대기된 쿼리 | 현재 대기 중인 워크로드 그룹에 대한 쿼리는 실행을 시작하기 위해 대기중입니다.  쿼리는 리소스 또는 잠금을 대기하기 때문에 큐일 수 있습니다.<br><br>쿼리는 여러 가지 이유로 대기중일 수 있습니다.  시스템에 오버로드되고 동시성 수요가 사용 가능한 것보다 큰 경우 쿼리가 큐에 대기합니다.<br><br>CREATE 워크로드 그룹 문에서 `CAP_PERCENTAGE_RESOURCE` 매개 변수를 늘려 워크로드 [그룹에](https://docs.microsoft.com/sql/t-sql/statements/create-workload-group-transact-sql?view=azure-sqldw-latest) 리소스를 추가하는 것이 좋습니다.  유효 `CAP_PERCENTAGE_RESOURCE` *한도 리소스 백분율* 메트릭보다 큰 경우 다른 워크로드 그룹에 대해 구성된 워크로드 격리가 이 워크로드 그룹에 할당된 리소스에 영향을 미칩니다.  다른 `MIN_PERCENTAGE_RESOURCE` 워크로드 그룹을 낮추거나 인스턴스를 확장하여 리소스를 추가하는 것이 좋습니다. |합계 |

## <a name="monitoring-scenarios-and-actions"></a>시나리오 및 작업 모니터링

다음은 문제를 해결하기 위한 관련 작업과 함께 문제 해결을 위한 워크로드 관리 메트릭 사용량을 강조하기 위한 일련의 차트 구성입니다.

### <a name="underutilized-workload-isolation"></a>활용도가 낮은 워크로드 격리

다음 워크로드 그룹 및 분류자 `wgPriority` 구성에서 명명된 워크로드 그룹이 만들어지고 *TheCEO가* `membername` `wcCEOPriority` 워크로드 분류기를 사용하여 워크로드 그룹에 매핑되는 경우를 고려합니다.  `wgPriority` 워크로드 그룹에는 25%의 워크로드 격리가`MIN_PERCENTAGE_RESOURCE` 구성됩니다(= 25).  *TheCEO가* 제출한 각 쿼리에는 시스템 리소스의 5%(=`REQUEST_MIN_RESOURCE_GRANT_PERCENT` 5)가 제공됩니다.

```sql
CREATE WORKLOAD GROUP wgPriority
WITH ( MIN_PERCENTAGE_RESOURCE = 25
      ,CAP_PERCENTAGE_RESOURCE = 50
      ,REQUEST_MIN_RESOURCE_GRANT_PERCENT = 5);

CREATE WORKLOAD CLASSIFIER wcCEOPriority
WITH ( WORKLOAD_GROUP = 'wgPriority'
      ,MEMBERNAME = 'TheCEO');
```

아래 차트는 다음과 같이 구성됩니다.<br>
메트릭 1: *유효 최소 리소스 백분율(평균* 집계, `blue line`)<br>
메트릭 2: *시스템 백분율별 워크로드 그룹 할당(평균* 집계, `purple line`)<br>
필터: [워크로드 그룹] =`wgPriority`<br>
![활용도가 낮은 wg.png](./media/sql-data-warehouse-workload-management-portal-monitor/underutilized-wg.png) 차트는 25%의 워크로드 격리로 평균 10%만 사용되고 있음을 보여줍니다.  이 경우 `MIN_PERCENTAGE_RESOURCE` 매개 변수 값을 10~15사이로 낮추고 시스템의 다른 워크로드가 리소스를 사용할 수 있도록 허용할 수 있습니다.

### <a name="workload-group-bottleneck"></a>워크로드 그룹 병목 현상

다음 워크로드 그룹 및 분류자 `wgDataAnalyst` 구성에서 명명된 워크로드 그룹이 만들어지고 *DataAnalyst가* `membername` `wcDataAnalyst` 워크로드 분류기를 사용하여 워크로드 그룹에 매핑되는 경우를 고려합니다.  `wgDataAnalyst` 워크로드 그룹에는 6%의 워크로드 격리가`MIN_PERCENTAGE_RESOURCE` 구성되어 있으며(= 6)`CAP_PERCENTAGE_RESOURCE` 및 리소스 제한이 9%(= 9)입니다.  *DataAnalyst에서* 제출한 각 쿼리에는 시스템 리소스의`REQUEST_MIN_RESOURCE_GRANT_PERCENT` 3%(= 3)가 제공됩니다.

```sql
CREATE WORKLOAD GROUP wgDataAnalyst  
WITH ( MIN_PERCENTAGE_RESOURCE = 6
      ,CAP_PERCENTAGE_RESOURCE = 9
      ,REQUEST_MIN_RESOURCE_GRANT_PERCENT = 3);

CREATE WORKLOAD CLASSIFIER wcDataAnalyst
WITH ( WORKLOAD_GROUP = 'wgDataAnalyst'
      ,MEMBERNAME = 'DataAnalyst');
```

아래 차트는 다음과 같이 구성됩니다.<br>
메트릭 1: *유효 한도 리소스 백분율(평균* 집계, `blue line`)<br>
메트릭 2: *최대 리소스 백분율별 워크로드 그룹 할당(평균* 집계, `purple line`)<br>
메트릭 3: *워크로드 그룹 큐에 대기된* `turquoise line`쿼리(합계 집계, )<br>
필터: [워크로드 그룹] =`wgDataAnalyst`<br>
![병 목 -wg](./media/sql-data-warehouse-workload-management-portal-monitor/bottle-necked-wg.png) 차트는 리소스에 대한 9 % 상한을 가진 워크로드 그룹이 90 % + *(최대 리소스 백분율 메트릭에 의한 워크로드 그룹*할당에서)을 활용한다는 것을 보여줍니다.  *워크로드 그룹 큐에 대기된*쿼리 메트릭에서 와 같이 쿼리가 꾸준히 큐에 대기하고 있습니다.  이 경우 값을 `CAP_PERCENTAGE_RESOURCE` 9% 이상으로 늘리면 더 많은 쿼리가 동시에 실행될 수 있습니다.  다른 `CAP_PERCENTAGE_RESOURCE` 워크로드 그룹에서 격리되지 않고 사용 가능한 리소스가 충분하다고 가정합니다.  *유효 한도 리소스 백분율 메트릭을*확인하여 증가된 한도를 확인합니다.  더 많은 처리량이 필요한 경우 `REQUEST_MIN_RESOURCE_GRANT_PERCENT` 3보다 큰 값으로 늘리는 것도 고려하십시오.  을 `REQUEST_MIN_RESOURCE_GRANT_PERCENT` 늘리면 쿼리를 더 빠르게 실행할 수 있습니다.

## <a name="next-steps"></a>다음 단계

- [빠른 시작: T-SQL을 사용하여 워크로드 격리 구성](quickstart-configure-workload-isolation-tsql.md)<br>
- [CREATE WORKLOAD GROUP(Transact-SQL)](https://docs.microsoft.com/sql/t-sql/statements/create-workload-group-transact-sql?view=azure-sqldw-latest)<br>
- [워크로드 분류자 만들기(Transact-SQL)](https://docs.microsoft.com/sql/t-sql/statements/create-workload-classifier-transact-sql?view=azure-sqldw-latest)<br>
- [리소스 사용률 모니터링](sql-data-warehouse-concept-resource-utilization-query-activity.md)
