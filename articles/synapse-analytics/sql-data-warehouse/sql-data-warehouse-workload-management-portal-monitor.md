---
title: 워크로드 관리 포털 모니터링
description: Azure Synapse Analytics의 워크로드 관리 포털 모니터링에 대한 지침입니다.
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
ms.openlocfilehash: 31533eefbfae63e0eb4049d2eabaf6b853340636
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/19/2020
ms.locfileid: "83590250"
---
# <a name="azure-synapse-analytics--workload-management-portal-monitoring"></a>Azure Synapse Analytics – 워크로드 관리 포털 모니터링

이 문서에서는 [작업 그룹](sql-data-warehouse-workload-isolation.md#workload-groups) 리소스 사용률 및 쿼리 작업을 모니터링하는 방법에 대해 설명합니다.
Azure Metric Explorer를 구성하는 방법에 대한 자세한 내용은 [Azure Metric Explorer 시작](../../azure-monitor/platform/metrics-getting-started.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) 문서를 참조하세요.  시스템 리소스 사용량을 모니터링하는 방법에 대한 자세한 내용은 Azure Synapse Analytics 모니터링 설명서의 [리소스 사용률](sql-data-warehouse-concept-resource-utilization-query-activity.md#resource-utilization) 섹션을 참조하세요.
워크로드 관리를 모니터링하기 위해 제공되는 두 가지 범주의 작업 그룹 메트릭(리소스 할당 및 쿼리 작업)이 있습니다.  이러한 메트릭은 작업 그룹별로 분할 및 필터링할 수 있습니다.  메트릭은 시스템 정의(리소스 클래스 작업 그룹)인지 또는 사용자 정의(사용자가 [CREATE WORKLOAD GROUP](/sql/t-sql/statements/create-workload-group-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) 구문을 사용하여 만듦)인지에 따라 분할하고 필터링할 수 있습니다.

## <a name="workload-management-metric-definitions"></a>워크로드 관리 메트릭 정의

|메트릭 이름                    |Description  |집계 형식 |
|-------------------------------|-------------|-----------------|
|유효 상한 리소스 비율 | *유효 상한 리소스 비율*은 다른 작업 그룹에 할당된 *유효 최소 리소스 비율*을 고려하여 작업 그룹에서 액세스할 수 있는 리소스의 비율에 대한 엄격한 제한입니다. *유효 상한 리소스 비율* 메트릭은 [CREATE WORKLOAD GROUP](/sql/t-sql/statements/create-workload-group-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) 구문에서 `CAP_PERCENTAGE_RESOURCE` 매개 변수를 사용하여 구성됩니다.  유효 값은 여기서 설명하고 있습니다.<br><br>예를 들어 `CAP_PERCENTAGE_RESOURCE` = 100인 `DataLoads` 작업 그룹과 유효 최소 리소스 비율이 25%인 다른 작업 그룹을 만드는 경우 `DataLoads` 작업 그룹에 대한 *유효 상한 리소스 비율*은 75%입니다.<br><br>*유효 상한 리소스 비율*은 작업 그룹에서 달성할 수 있는 동시성(이에 따라 잠재적 처리량)의 상한을 결정합니다.  *유효 상한 리소스 비율* 메트릭에서 현재 보고한 것보다 더 많은 처리량이 필요한 경우 `CAP_PERCENTAGE_RESOURCE`를 늘리거나 다른 작업 그룹의 `MIN_PERCENTAGE_RESOURCE`를 줄이거나 인스턴스를 강화하여 더 많은 리소스를 추가합니다.  `REQUEST_MIN_RESOURCE_GRANT_PERCENT`를 줄이면 동시성은 증가하지만 전체 처리량이 증가하지 않을 수 있습니다.| 최소, 평균, 최대 |
|유효 최소 리소스 비율 |*유효 최소 리소스 비율*은 서비스 수준 최솟값을 고려하여 작업 그룹에 예약되고 격리되는 리소스의 최소 비율입니다.  유효 최소 리소스 비율 메트릭은 [CREATE WORKLOAD GROUP](/sql/t-sql/statements/create-workload-group-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) 구문에서 `MIN_PERCENTAGE_RESOURCE` 매개 변수를 사용하여 구성됩니다.  유효 값은 [여기](/sql/t-sql/statements/create-workload-group-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest#effective-values)서 설명하고 있습니다.<br><br>이 메트릭이 필터링되지 않고 분할되지 않는 경우 합계 집계 유형을 사용하여 시스템에 구성된 총 워크로드 격리를 모니터링합니다.<br><br>*유효 최소 리소스 비율*은 작업 그룹에서 달성할 수 있는 보장된 동시성(이에 따라 보장된 처리량)의 하한을 결정합니다.  *유효 최소 리소스 비율* 메트릭에서 현재 보고하는 것보다 더 많은 보장된 리소스가 필요한 경우 작업 그룹에 구성된 `MIN_PERCENTAGE_RESOURCE` 매개 변수를 늘립니다.  `REQUEST_MIN_RESOURCE_GRANT_PERCENT`를 줄이면 동시성은 증가하지만 전체 처리량이 증가하지 않을 수 있습니다. |최소, 평균, 최대|
|작업 그룹 활성 쿼리  |이 메트릭은 작업 그룹 내의 활성 쿼리를 보고합니다.  필터링되지 않고 분할되지 않은 이 메트릭을 사용하면 시스템에서 실행 중인 모든 활성 쿼리가 표시됩니다.|합계         |
|최대 리소스 비율별 작업 그룹 할당 |이 메트릭은 작업 그룹당 *유효 상한 리소스 비율*에 대한 상대적 리소스 할당률을 표시합니다.  이 메트릭은 작업 그룹의 효과적인 사용률을 제공합니다.<br><br>*유효 상한 리소스 비율*이 75%이고 `REQUEST_MIN_RESOURCE_GRANT_PERCENT`가 25%로 구성된 `DataLoads` 작업 그룹을 고려해 보세요.  이 작업 그룹에서 단일 쿼리가 실행된 경우 `DataLoads`로 필터링된 *최대 리소스 비율별 작업 그룹 할당* 값은 33%(25%/75%)입니다.<br><br>이 메트릭을 사용하여 작업 그룹의 사용률을 식별합니다.  값이 100%에 가까우면 작업 그룹에 사용할 수 있는 모든 리소스가 사용되고 있음을 나타냅니다.  또한 0보다 큰 값을 표시하는 동일한 작업 그룹에 대한 *작업 그룹 큐에 대기 중인 쿼리 메트릭*은 할당된 작업 그룹에서 추가 리소스를 사용할 것임을 나타냅니다.  반대로 이 메트릭이 지속적으로 낮고 *작업 그룹 활성 쿼리*가 낮은 경우 작업 그룹이 사용되지 않는 것입니다.  *유효 상한 리소스 비율*이 0보다 크면 [미달 사용 워크로드 격리](#underutilized-workload-isolation)를 나타내므로 이 상황은 특히 문제가 됩니다.|최소, 평균, 최대 |
|시스템 비율별 작업 그룹 할당 | 이 메트릭은 전체 시스템에 대한 상대적인 리소스 할당률을 표시합니다.<br><br>`REQUEST_MIN_RESOURCE_GRANT_PERCENT`가 25%로 구성된 `DataLoads` 작업 그룹을 고려해 보세요.  이 작업 그룹에서 단일 쿼리가 실행된 경우 `DataLoads`로 필터링된 *시스템 비율별 작업 그룹 할당* 값은 25%(25%/100%)입니다.|최소, 평균, 최대 |
|작업 그룹 쿼리 시간 제한 |시간이 초과된 작업 그룹에 대한 쿼리입니다.  이 메트릭에서 보고한 쿼리 시간 제한은 쿼리 실행이 시작된 후에만 해당됩니다(잠금 또는 리소스 대기로 인한 대기 시간은 포함되지 않음).<br><br>쿼리 시간 제한은 [CREATE WORKLOAD GROUP](/sql/t-sql/statements/create-workload-group-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) 구문에서 `QUERY_EXECUTION_TIMEOUT_SEC` 매개 변수를 사용하여 구성됩니다.  값을 늘리면 쿼리 시간 제한 수를 줄일 수 있습니다.<br><br>시간 제한을 줄이고 쿼리당 더 많은 리소스를 할당하려면 작업 그룹에 대한 `REQUEST_MIN_RESOURCE_GRANT_PERCENT` 매개 변수를 늘리는 것이 좋습니다.  `REQUEST_MIN_RESOURCE_GRANT_PERCENT`를 늘리면 작업 그룹의 동시성 양이 줄어듭니다. |합계 |
|작업 그룹 큐에 대기 중인 쿼리 | 실행을 시작하려고 대기 중인 현재 큐에 있는 작업 그룹에 대한 쿼리입니다.  쿼리는 리소스 또는 잠금을 기다리고 있으므로 큐에 넣을 수 있습니다.<br><br>쿼리는 여러 가지 이유로 기다리고 있을 수 있습니다.  시스템이 오버로드되고 동시성 요구가 사용 가능한 것보다 큰 경우 쿼리가 큐에서 대기합니다.<br><br>[CREATE WORKLOAD GROUP](/sql/t-sql/statements/create-workload-group-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) 문에서 `CAP_PERCENTAGE_RESOURCE` 매개 변수를 늘려 더 많은 리소스를 작업 그룹에 추가하는 것이 좋습니다.  `CAP_PERCENTAGE_RESOURCE`가 *유효 상한 리소스 비율* 메트릭보다 크면 다른 작업 그룹에 구성된 워크로드 격리에서 이 작업 그룹에 할당된 리소스에 영향을 줍니다.  다른 작업 그룹의 `MIN_PERCENTAGE_RESOURCE`를 줄이거나 인스턴스를 강화하여 더 많은 리소스를 추가하는 것이 좋습니다. |합계 |

## <a name="monitoring-scenarios-and-actions"></a>모니터링 시나리오 및 작업

문제를 해결하기 위한 관련 작업과 함께 문제를 해결하기 위해 워크로드 관리 메트릭을 사용하는 방법을 강조하기 위한 일련의 차트 구성은 다음과 같습니다.

### <a name="underutilized-workload-isolation"></a>미달 사용 워크로드 격리

`wgPriority`라는 작업 그룹이 만들어지고 *TheCEO* `membername`이 `wcCEOPriority` 워크로드 분류자를 사용하여 매핑되는 다음 작업 그룹 및 분류자 구성을 고려해 보세요.  `wgPriority` 작업 그룹에는 25%의 워크로드 격리가 구성되어 있습니다(`MIN_PERCENTAGE_RESOURCE` = 25).  *TheCEO*에서 제출하는 각 쿼리에는 5%의 시스템 리소스가 제공됩니다(`REQUEST_MIN_RESOURCE_GRANT_PERCENT` = 5).

```sql
CREATE WORKLOAD GROUP wgPriority
WITH ( MIN_PERCENTAGE_RESOURCE = 25
      ,CAP_PERCENTAGE_RESOURCE = 50
      ,REQUEST_MIN_RESOURCE_GRANT_PERCENT = 5);

CREATE WORKLOAD CLASSIFIER wcCEOPriority
WITH ( WORKLOAD_GROUP = 'wgPriority'
      ,MEMBERNAME = 'TheCEO');
```

아래 차트가 다음과 같이 구성됩니다.<br>
메트릭 1: *유효 최소 리소스 비율* (평균 집계, `blue line`)<br>
메트릭 2: *시스템 비율별 작업 그룹 할당*(평균 집계, `purple line`)<br>
필터: [작업 그룹] = `wgPriority`<br>
![underutilized-wg.png](./media/sql-data-warehouse-workload-management-portal-monitor/underutilized-wg.png) 이 차트에서는 25%의 워크로드 격리 상태에서 평균 10%만 사용되고 있음을 보여 줍니다.  이 경우 `MIN_PERCENTAGE_RESOURCE` 매개 변수 값을 10 또는 15 사이로 낮추고 시스템의 다른 워크로드에서 리소스를 사용할 수 있습니다.

### <a name="workload-group-bottleneck"></a>작업 그룹 병목 상태

`wgDataAnalyst`라는 작업 그룹이 만들어지고 *DataAnalyst* `membername`이 `wcDataAnalyst` 워크로드 분류자를 사용하여 매핑되는 다음 작업 그룹 및 분류자 구성을 고려해 보세요.  `wgDataAnalyst` 작업 그룹에는 6%의 워크로드 격리가 구성되어 있고(`MIN_PERCENTAGE_RESOURCE` = 6), 리소스 제한이 9%입니다(`CAP_PERCENTAGE_RESOURCE` = 9).  *DataAnalyst*에서 제출하는 각 쿼리에는 3%의 시스템 리소스가 제공됩니다(`REQUEST_MIN_RESOURCE_GRANT_PERCENT` = 3).

```sql
CREATE WORKLOAD GROUP wgDataAnalyst  
WITH ( MIN_PERCENTAGE_RESOURCE = 6
      ,CAP_PERCENTAGE_RESOURCE = 9
      ,REQUEST_MIN_RESOURCE_GRANT_PERCENT = 3);

CREATE WORKLOAD CLASSIFIER wcDataAnalyst
WITH ( WORKLOAD_GROUP = 'wgDataAnalyst'
      ,MEMBERNAME = 'DataAnalyst');
```

아래 차트가 다음과 같이 구성됩니다.<br>
메트릭 1: *유효 상한 리소스 비율*(평균 집계, `blue line`)<br>
메트릭 2: *최대 리소스 비율별 작업 그룹 할당*(평균 집계, `purple line`)<br>
메트릭 3: *작업 그룹 큐에 대기 중인 쿼리*(합계 집계, `turquoise line`)<br>
필터: [작업 그룹] = `wgDataAnalyst`<br>
![bottle-necked-wg](./media/sql-data-warehouse-workload-management-portal-monitor/bottle-necked-wg.png) 이 차트에서는 9%의 리소스 상한 상태에서 작업 그룹이 90% 이상 사용되고 있음을 보여 줍니다(*최대 리소스 비율별 작업 그룹 할당 메트릭*).  *작업 그룹 큐에 대기 중인 쿼리 메트릭*에서와 같이 쿼리가 큐에서 안정적으로 대기하고 있습니다.  이 경우 `CAP_PERCENTAGE_RESOURCE`를 9%보다 큰 값으로 늘리면 더 많은 쿼리를 동시에 실행할 수 있습니다.  `CAP_PERCENTAGE_RESOURCE`를 늘리면 사용 가능한 리소스가 충분하고 다른 작업 그룹에서 격리되지 않은 것으로 가정합니다.  *유효 상한 리소스 비율 메트릭*을 확인하여 상한이 증가했는지 확인합니다.  더 많은 처리량이 필요한 경우 `REQUEST_MIN_RESOURCE_GRANT_PERCENT`를 3보다 큰 값으로 늘릴 수도 있습니다.  `REQUEST_MIN_RESOURCE_GRANT_PERCENT`를 늘리면 쿼리가 더 빨리 실행될 수 있습니다.

## <a name="next-steps"></a>다음 단계

- [빠른 시작: T-SQL을 사용하여 워크로드 격리 구성](quickstart-configure-workload-isolation-tsql.md)<br>
- [CREATE WORKLOAD GROUP(Transact-SQL)](/sql/t-sql/statements/create-workload-group-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)<br>
- [CREATE WORKLOAD CLASSIFIER(Transact-SQL)](/sql/t-sql/statements/create-workload-classifier-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)<br>
- [리소스 사용률 모니터링](sql-data-warehouse-concept-resource-utilization-query-activity.md)
