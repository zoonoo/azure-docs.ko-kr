---
title: 워크 로드 관리 포털 모니터링
description: Azure Synapse Analytics의 워크 로드 관리 포털 모니터링에 대 한 지침입니다.
services: sql-data-warehouse
author: ronortloff
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: workload-management
ms.date: 01/14/2020
ms.author: rortloff
ms.reviewer: jrasnick
ms.custom: seo-lt-2019
ms.openlocfilehash: fd9bd846beba718cb305907d4d0c5a613d2ef816
ms.sourcegitcommit: dbcc4569fde1bebb9df0a3ab6d4d3ff7f806d486
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/15/2020
ms.locfileid: "76029935"
---
# <a name="azure-synapse-analytics--workload-management-portal-monitoring-preview"></a>Azure Synapse Analytics – 워크 로드 관리 포털 모니터링 (미리 보기)
이 문서에서는 [작업 그룹](sql-data-warehouse-workload-isolation.md#workload-groups) 리소스 사용률 및 쿼리 작업을 모니터링 하는 방법을 설명 합니다. Azure 메트릭 탐색기 구성 하는 방법에 대 한 자세한 내용은 [azure 메트릭 탐색기 시작](../azure-monitor/platform/metrics-getting-started.md) 문서를 참조 하세요.  시스템 리소스 사용량을 모니터링 하는 방법에 대 한 자세한 내용은 Azure SQL Data Warehouse 모니터링 설명서의 [리소스 사용률](sql-data-warehouse-concept-resource-utilization-query-activity.md#resource-utilization) 섹션을 참조 하십시오.
워크 로드 관리를 모니터링 하기 위해 제공 되는 작업 그룹 메트릭에는 리소스 할당과 쿼리 작업 이라는 두 가지 범주가 있습니다.  이러한 메트릭은 작업 그룹별로 분할 하 고 필터링 할 수 있습니다.  시스템 정의 (리소스 클래스 작업 그룹) 또는 사용자 정의 ( [작업 그룹 구문 만들기](https://docs.microsoft.com/sql/t-sql/statements/create-workload-group-transact-sql?view=azure-sqldw-latest) 를 사용 하 여 사용자가 만듦)에 따라 메트릭을 분할 하 고 필터링 할 수 있습니다.

## <a name="workload-management-metric-definitions"></a>워크 로드 관리 메트릭 정의

|메트릭 이름                    |Description  |집계 형식 |
|-------------------------------|-------------|-----------------|
|유효 캡 리소스 비율 | *유효 캡 리소스 백분율* 은 작업 그룹에서 액세스할 수 있는 리소스의 비율에 대 한 하드 제한이 며, 다른 작업 그룹에 할당 된 *유효 최소 리소스 비율* 을 고려 합니다. [작업 그룹 만들기](https://docs.microsoft.com/sql/t-sql/statements/create-workload-group-transact-sql?view=azure-sqldw-latest) 구문에서 `CAP_PERCENTAGE_RESOURCE` 매개 변수를 사용 하 여 *유효 캡 리소스 백분율* 메트릭이 구성 됩니다.  유효한 값은 여기에 설명 되어 있습니다.<br><br>예를 들어 작업 그룹 `DataLoads` `CAP_PERCENTAGE_RESOURCE` = 100을 사용 하 여 생성 되 고, 다른 작업 그룹이 유효 min 리소스 비율 25%로 생성 된 경우 `DataLoads` 작업 그룹의 *유효 cap 리소스 비율은* 75%가 됩니다.<br><br>*유효 캡 리소스 백분율* 은 작업 그룹이 달성할 수 있는 동시성의 상한 (따라서 잠재적 처리량)을 결정 합니다.  *유효한 cap 리소스 비율* 메트릭에 의해 현재 보고 되는 것 보다 더 많은 처리량이 필요한 경우 `CAP_PERCENTAGE_RESOURCE`를 늘리거나 다른 작업 그룹의 `MIN_PERCENTAGE_RESOURCE`를 줄이거나 인스턴스를 확장 하 여 더 많은 리소스를 추가 합니다.  `REQUEST_MIN_RESOURCE_GRANT_PERCENT`을 줄이면 동시성이 증가 하지만 전반적인 처리량은 증가 하지 않을 수 있습니다.| 최소, 평균, 최대 |
|유효 min 리소스 백분율 |*유효 min resource percent* 는 서비스 수준 최소를 고려 하 여 작업 그룹에 대해 예약 되 고 격리 된 리소스의 최소 비율입니다.  [작업 그룹 만들기](https://docs.microsoft.com/sql/t-sql/statements/create-workload-group-transact-sql?view=azure-sqldw-latest) 구문에서 `MIN_PERCENTAGE_RESOURCE` 매개 변수를 사용 하 여 유효 min 리소스 백분율 메트릭이 구성 됩니다.  유효한 값은 [여기](https://docs.microsoft.com/sql/t-sql/statements/create-workload-group-transact-sql?view=azure-sqldw-latest#effective-values)에 설명 되어 있습니다.<br><br>이 메트릭이 필터링 되지 않고 분할 되지 않은 경우 Sum 집계 유형을 사용 하 여 시스템에 구성 된 총 워크 로드 격리를 모니터링 합니다.<br><br>*유효 min 리소스 백분율* 은 작업 그룹이 달성할 수 있는 보장 된 동시성의 하한값 (따라서 처리량이 보장 되는 처리량)을 결정 합니다.  현재 *min resource percent* 메트릭에 의해 보고 되는 것 이상의 추가 보장 된 리소스가 필요한 경우 작업 그룹에 대해 구성 된 `MIN_PERCENTAGE_RESOURCE` 매개 변수를 늘립니다.  `REQUEST_MIN_RESOURCE_GRANT_PERCENT`을 줄이면 동시성이 증가 하지만 전반적인 처리량은 증가 하지 않을 수 있습니다. |최소, 평균, 최대|
|작업 그룹 활성 쿼리  |이 메트릭은 작업 그룹 내의 활성 쿼리를 보고 합니다.  필터링 되지 않은이 메트릭과 분할 되지 않은 메트릭을 사용 하 여 시스템에서 실행 중인 모든 활성 쿼리를 표시 합니다.|합계         |
|최대 리소스% 별 작업 그룹 할당 |이 메트릭은 작업 그룹당 *유효 cap 리소스 비율* 을 기준으로 하는 리소스의 백분율 할당을 표시 합니다.  이 메트릭은 작업 그룹의 효과적인 사용률을 제공 합니다.<br><br>75%의 *유효 캡 리소스 백분율이* 있는 작업 그룹 `DataLoads` 25%로 구성 된 `REQUEST_MIN_RESOURCE_GRANT_PERCENT`을 고려 하십시오.  `DataLoads`로 필터링 된 *max resource percent 값을 기준으로 하는 작업 그룹 할당* 은 33% (25%/75%)입니다. 단일 쿼리가이 작업 그룹에서 실행 되 고 있는 경우<br><br>이 메트릭을 사용 하 여 작업 그룹의 사용률을 식별할 수 있습니다.  100%에 가까운 값은 작업 그룹에서 사용할 수 있는 모든 리소스를 사용 중임을 나타냅니다.  또한 0 보다 큰 값을 표시 하는 동일한 작업 그룹에 대 한 작업 그룹의 *대기 중인 쿼리 메트릭은* 할당 된 경우 작업 그룹이 추가 리소스를 활용 함을 의미 합니다.  반대로이 메트릭이 지속적으로 적고 *작업 그룹 활성 쿼리가* 낮은 경우 작업 그룹을 사용 하지 않는 것입니다.  이 상황은 *유효 캡 리소스 백분율이* [미달 사용 작업 격리](#underutilized-workload-isolation)를 나타내는 0 보다 큰 경우에 특히 문제가 됩니다.|최소, 평균, 최대 |
|시스템 percent 별 작업 그룹 할당 | 이 메트릭은 전체 시스템에 상대적인 리소스 할당 비율을 표시 합니다.<br><br>25%로 구성 된 `REQUEST_MIN_RESOURCE_GRANT_PERCENT` `DataLoads` 작업 그룹을 고려 합니다.  *시스템 백분율 값으로 `DataLoads` 필터링 된 작업 그룹 할당* 은 25% (25%/100%)가 됩니다. 단일 쿼리가이 작업 그룹에서 실행 되 고 있는 경우|최소, 평균, 최대 |
|작업 그룹 쿼리 시간 제한 |시간이 초과 된 작업 그룹에 대 한 쿼리입니다.  이 메트릭에 의해 보고 된 쿼리 시간 제한은 쿼리가 실행을 시작한 후에만 수행 됩니다 (잠금 또는 리소스 대기로 인 한 대기 시간 포함 안 함).<br><br>쿼리 제한 시간은 [CREATE 작업 그룹](https://docs.microsoft.com/sql/t-sql/statements/create-workload-group-transact-sql?view=azure-sqldw-latest) 구문에서 `QUERY_EXECUTION_TIMEOUT_SEC` 매개 변수를 사용 하 여 구성 됩니다.  값을 늘려도 쿼리 제한 시간 수를 줄일 수 있습니다.<br><br>작업 그룹에 대 한 `REQUEST_MIN_RESOURCE_GRANT_PERCENT` 매개 변수를 늘려서 시간 초과를 줄이고 쿼리당 추가 리소스를 할당 하는 것이 좋습니다.  `REQUEST_MIN_RESOURCE_GRANT_PERCENT`를 높이면 작업 그룹의 동시성 양이 줄어듭니다. |합계 |
|작업 그룹 큐에 대기 중인 쿼리 | 현재 실행을 시작할 때까지 대기 중인 작업 그룹에 대 한 쿼리입니다.  쿼리는 리소스나 잠금을 기다리고 있으므로 큐가 될 수 있습니다.<br><br>쿼리는 다양 한 이유를 기다릴 수 있습니다.  시스템이 오버 로드 되 고 동시성 요구가 사용 가능한 수보다 크면 쿼리가 큐에 대기 합니다.<br><br>[CREATE 워크 로드 그룹](https://docs.microsoft.com/sql/t-sql/statements/create-workload-group-transact-sql?view=azure-sqldw-latest) 문에서 `CAP_PERCENTAGE_RESOURCE` 매개 변수를 늘려서 작업 그룹에 리소스를 더 추가 하는 것이 좋습니다.  `CAP_PERCENTAGE_RESOURCE`가 *유효 cap 리소스 비율* 메트릭에 해당 하는 경우 다른 작업 그룹에 대해 구성 된 워크 로드 격리는이 작업 그룹에 할당 된 리소스에 영향을 줍니다.  다른 작업 그룹의 `MIN_PERCENTAGE_RESOURCE`을 낮추거나 인스턴스를 확장 하 여 리소스를 더 추가 하는 것이 좋습니다. |합계 |

## <a name="monitoring-scenarios-and-actions"></a>모니터링 시나리오 및 작업
다음은 문제를 해결 하기 위해 관련 작업과 함께 문제 해결을 위해 워크 로드 관리 메트릭 사용을 강조 표시 하는 일련의 차트 구성입니다.

### <a name="underutilized-workload-isolation"></a>미달 사용 작업 격리
`wgPriority` 이라는 작업 그룹을 만들고, `wcCEOPriority` 워크 로드 분류자를 사용 하 여 *TheCEO* `membername`에 매핑되는 다음 작업 그룹 및 분류자 구성을 고려 합니다.  `wgPriority` 작업 그룹에는 25%의 워크 로드 격리가 구성 되어 있습니다 (`MIN_PERCENTAGE_RESOURCE` = 25).  *TheCEO* 에서 제출한 각 쿼리에는 시스템 리소스의 5% (`REQUEST_MIN_RESOURCE_GRANT_PERCENT` = 5)가 제공 됩니다.
```sql
CREATE WORKLOAD GROUP wgPriority 
WITH ( MIN_PERCENTAGE_RESOURCE = 25   
      ,CAP_PERCENTAGE_RESOURCE = 50 
      ,REQUEST_MIN_RESOURCE_GRANT_PERCENT = 5); 

CREATE WORKLOAD CLASSIFIER wcCEOPriority 
WITH ( WORKLOAD_GROUP = 'wgPriority'
      ,MEMBERNAME = 'TheCEO');
```
아래 차트는 다음과 같이 구성 됩니다.<br>
메트릭 1: *유효 min 리소스 백분율* (평균 집계, `blue line`)<br>
메트릭 2: *시스템 percent 별 작업 그룹 할당* (Avg aggregation, `purple line`)<br>
필터: [작업 그룹] = `wgPriority`<br>
![underutilized-wg는 25% 워크 로드 격리를 사용 하 여 평균에 10%만 사용 하 고 있음을 보여 줍니다](media/sql-data-warehouse-workload-management-portal-monitor/underutilized-wg.png).  이 경우 `MIN_PERCENTAGE_RESOURCE` 매개 변수 값이 10 또는 15 사이에서 감소 하 고 시스템의 다른 작업에서 리소스를 사용할 수 있습니다.

### <a name="workload-group-bottleneck"></a>작업 그룹 병목 상태
`wgDataAnalyst` 이라는 작업 그룹을 만들고, `wcDataAnalyst` 워크 로드 분류자를 사용 하 여 *Dataanalyst* `membername` 매핑되는 다음 작업 그룹 및 분류자 구성을 고려 합니다.  `wgDataAnalyst` 작업 그룹에는 6%의 워크 로드 격리가 구성 되어 있으며 (`MIN_PERCENTAGE_RESOURCE` = 6) 리소스 제한은 9% (`CAP_PERCENTAGE_RESOURCE` = 9)입니다.  *Dataanalyst* 제출한 각 쿼리에는 시스템 리소스의 3% (`REQUEST_MIN_RESOURCE_GRANT_PERCENT` = 3)가 제공 됩니다.

```sql
CREATE WORKLOAD GROUP wgDataAnalyst  
WITH ( MIN_PERCENTAGE_RESOURCE = 6   
      ,CAP_PERCENTAGE_RESOURCE = 9 
      ,REQUEST_MIN_RESOURCE_GRANT_PERCENT = 3); 

CREATE WORKLOAD CLASSIFIER wcDataAnalyst 
WITH ( WORKLOAD_GROUP = 'wgDataAnalyst'
      ,MEMBERNAME = 'DataAnalyst');
```
아래 차트는 다음과 같이 구성 됩니다.<br>
메트릭 1: *유효 캡 리소스 백분율* (평균 집계, `blue line`)<br>
메트릭 2: *최대 리소스 percent 별 작업 그룹 할당* (Avg aggregation, `purple line`)<br>
메트릭 3: *작업 그룹 큐에 대기 중인 쿼리* (합계 집계, `turquoise line`)<br>
필터: [작업 그룹] = `wgDataAnalyst`<br>
![necked-wg](media/sql-data-warehouse-workload-management-portal-monitor/bottle-necked-wg.png)는 리소스에서 9% 캡이 포함 된 차트를 보여 줍니다. 작업 그룹은 90% 이상 사용 됩니다 ( *최대 리소스 비율 메트릭 별 작업 그룹 할당*).  *작업 그룹 큐에 대기 중인 쿼리 메트릭에*표시 된 것 처럼 쿼리를 안정적으로 큐에 저장 합니다.  이 경우, `CAP_PERCENTAGE_RESOURCE` 9% 보다 큰 값으로 증가 하면 더 많은 쿼리를 동시에 실행할 수 있습니다.  `CAP_PERCENTAGE_RESOURCE`를 늘리려면 사용 가능한 리소스가 충분 하 고 다른 작업 그룹에 의해 격리 되지 않은 것으로 가정 합니다.  *유효 cap 리소스 백분율 메트릭을*확인 하 여 단면 증가를 확인 합니다.  더 많은 처리량이 필요한 경우에는 `REQUEST_MIN_RESOURCE_GRANT_PERCENT` 3 보다 큰 값으로 늘릴 수도 있습니다.  `REQUEST_MIN_RESOURCE_GRANT_PERCENT`를 높이면 쿼리가 더 빠르게 실행 될 수 있습니다.

## <a name="next-steps"></a>다음 단계
[빠른 시작: T-sql을 사용 하 여 워크 로드 격리 구성](quickstart-configure-workload-isolation-tsql.md)<br>
[작업 그룹 만들기 (Transact-sql)](https://docs.microsoft.com/sql/t-sql/statements/create-workload-group-transact-sql?view=azure-sqldw-latest)<br>
[워크 로드 분류자 만들기 (Transact-sql)](https://docs.microsoft.com/sql/t-sql/statements/create-workload-classifier-transact-sql?view=azure-sqldw-latest)<br>
[리소스 사용률 모니터링](sql-data-warehouse-concept-resource-utilization-query-activity.md)

