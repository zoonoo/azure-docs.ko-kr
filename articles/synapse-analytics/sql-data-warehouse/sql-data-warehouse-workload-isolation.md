---
title: 워크로드 격리
description: Azure Synapse Analytics에서 워크로드 그룹과 워크로드 격리를 설정하기 위한 지침입니다.
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
ms.openlocfilehash: d5acdab9fb6eec585c53cfe0d7149aafa7cdc6f9
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "80350120"
---
# <a name="azure-synapse-analytics-workload-group-isolation-preview"></a>Azure 시냅스 분석 워크로드 그룹 격리(미리 보기)

이 문서에서는 워크로드 그룹을 사용하여 워크로드 격리를 구성하고, 리소스를 포함하고, 쿼리 실행을 위한 런타임 규칙을 적용하는 방법을 설명합니다.

## <a name="workload-groups"></a>워크로드 그룹

워크로드 그룹은 요청 집합에 대한 컨테이너이며 워크로드 격리를 비롯한 워크로드 관리가 시스템에서 구성되는 방식의 기초가 됩니다.  워크로드 그룹은 워크로드 [그룹 만들기](/sql/t-sql/statements/create-workload-group-transact-sql?view=azure-sqldw-latest) 구문을 사용하여 만들어집니다.  간단한 워크로드 관리 구성을 통해 데이터 로드 및 사용자 쿼리를 관리할 수 있습니다.  예를 들어 명명된 `wgDataLoads` 워크로드 그룹은 시스템에 로드되는 데이터에 대한 워크로드 측면을 정의합니다. 또한 명명된 `wgUserQueries` 워크로드 그룹은 쿼리를 실행하는 사용자가 시스템에서 데이터를 읽을 수 있도록 워크로드 측면을 정의합니다.

다음 섹션에서는 워크로드 그룹이 격리, 포함, 리소스 정의 요청 및 실행 규칙을 준수하는 기능을 제공하는 방법을 강조표시합니다.

## <a name="workload-isolation"></a>워크로드 격리

워크로드 격리는 리소스가 워크로드 그룹에 대해 독점적으로 예약됨을 의미합니다.  워크로드 격리는 [create 워크로드 그룹](/sql/t-sql/statements/create-workload-group-transact-sql?view=azure-sqldw-latest) 구문에서 MIN_PERCENTAGE_RESOURCE 매개 변수를 0보다 큰 값으로 구성하여 수행됩니다.  엄격한 SLA를 준수해야 하는 지속적인 실행 워크로드의 경우 격리를 통해 워크로드 그룹에 항상 리소스를 사용할 수 있습니다. 

워크로드 격리를 구성하는 것은 보장된 동시성 수준을 암시적으로 정의합니다. 예를 들어 30%로 `MIN_PERCENTAGE_RESOURCE` 설정하고 2%로 `REQUEST_MIN_RESOURCE_GRANT_PERCENT` 설정된 워크로드 그룹은 15동시성을 보장합니다.  15~2%의 리소스 슬롯이 항상 워크로드 그룹 내에서 예약되므로 동시성 수준이 보장됩니다(구성 방법에 `REQUEST_*MAX*_RESOURCE_GRANT_PERCENT` 관계 없이).  보다 `REQUEST_MAX_RESOURCE_GRANT_PERCENT` `REQUEST_MIN_RESOURCE_GRANT_PERCENT` 크고 `MIN_PERCENTAGE_RESOURCE` 더 큰 경우 요청당 추가 리소스가 추가됩니다. `CAP_PERCENTAGE_RESOURCE`  `REQUEST_MAX_RESOURCE_GRANT_PERCENT` 같고 `REQUEST_MIN_RESOURCE_GRANT_PERCENT` `CAP_PERCENTAGE_RESOURCE` 보다 `MIN_PERCENTAGE_RESOURCE`큰 경우 추가 동시성이 가능합니다.  보장된 동시성을 결정하는 방법은 다음과 같습니다.

[동시성 보장]`MIN_PERCENTAGE_RESOURCE`= [`REQUEST_MIN_RESOURCE_GRANT_PERCENT`[ [ [ [ [ [

> [!NOTE] 
> min_percentage_resource 대한 특정 서비스 수준 최소 실행 가능한 값이 있습니다.  자세한 내용은 [유효 값을](/sql/t-sql/statements/create-workload-group-transact-sql?view=azure-sqldw-latest#effective-values) 참조하세요.

워크로드 격리가 없는 경우 요청은 공유 리소스 [풀에서](#shared-pool-resources) 작동합니다.  공유 풀의 리소스에 대한 액세스는 보장되지 않으며 [중요도에](sql-data-warehouse-workload-importance.md) 따라 할당됩니다.

워크로드 그룹에 활성 요청이 없는 경우에도 리소스가 워크로드 그룹에 할당되기 때문에 워크로드 격리를 구성하는 것은 주의해야 합니다. 격리를 과도하게 구성하면 전체 시스템 사용률이 감소할 수 있습니다.

사용자는 100% 워크로드 격리를 구성하는 워크로드 관리 솔루션을 피해야 합니다: 모든 워크로드 그룹에서 구성된 min_percentage_resource 합계가 100%일 때 100% 격리가 달성됩니다.  이러한 유형의 구성은 지나치게 제한적이고 엄격하므로 실수로 잘못 분류된 리소스 요청에 대한 여지가 거의 없습니다. 격리를 위해 구성되지 않은 워크로드 그룹에서 하나의 요청을 실행할 수 있도록 하는 조항이 있습니다. 이 요청에 할당된 리소스는 시스템 DMV에서 0으로 표시되고 시스템 예약 된 리소스에서 소규모 리소스 부여 수준을 차용합니다.

> [!NOTE] 
> 최적의 리소스 활용을 보장하기 위해 일부 격리를 활용하는 워크로드 관리 솔루션을 고려하여 SLA가 워크로드 [중요도에](sql-data-warehouse-workload-importance.md)따라 액세스되는 공유 리소스와 충족되고 혼합되도록 합니다.

## <a name="workload-containment"></a>워크로드 제약

워크로드 포함이란 워크로드 그룹이 사용할 수 있는 리소스의 양을 제한하는 것을 말합니다.  워크로드 포함은 create 워크로드 그룹 구문에서 CAP_PERCENTAGE_RESOURCE 매개 변수를 100 미만으로 구성하여 [달성됩니다.](/sql/t-sql/statements/create-workload-group-transact-sql?view=azure-sqldw-latest)  임시 쿼리를 통해 what-if 분석을 실행할 수 있도록 사용자가 시스템에 대한 읽기 액세스 가 필요한 시나리오를 고려합니다.  이러한 유형의 요청은 시스템에서 실행 중인 다른 워크로드에 부정적인 영향을 줄 수 있습니다.  포함을 구성하면 리소스의 양이 제한됩니다.

워크로드 포함을 구성하는 것은 최대 동시성 수준을 암시적으로 정의합니다.  CAP_PERCENTAGE_RESOURCE 60%로 설정하고 REQUEST_MIN_RESOURCE_GRANT_PERCENT 1%로 설정되어 워크로드 그룹에 최대 60개의 동시성 수준이 허용됩니다.  최대 동시성을 결정하기 위해 아래에 포함된 방법을 고려하십시오.

[최대 동시성]`CAP_PERCENTAGE_RESOURCE`= [`REQUEST_MIN_RESOURCE_GRANT_PERCENT`[ [ [ [ [ [

> [!NOTE] 
> 워크로드 그룹의 유효 CAP_PERCENTAGE_RESOURCE 0보다 큰 수준에서 MIN_PERCENTAGE_RESOURCE 워크로드 그룹을 만들 때 100%에 도달하지 않습니다.  효과적인 런타임 값은 [sys.dm_workload_management_workload_groups_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-workload-management-workload-group-stats-transact-sql?view=azure-sqldw-latest) 을 참조하십시오.

## <a name="resources-per-request-definition"></a>요청 정의당 리소스

워크로드 그룹은 [create 워크로드 그룹](/sql/t-sql/statements/create-workload-group-transact-sql?view=azure-sqldw-latest) 구문에서 REQUEST_MIN_RESOURCE_GRANT_PERCENT 및 REQUEST_MAX_RESOURCE_GRANT_PERCENT 매개 변수를 사용하여 요청당 할당되는 최소 및 최대 리소스 양을 정의하는 메커니즘을 제공합니다.  이 경우 리소스는 CPU와 메모리입니다.  이러한 값을 구성하면 시스템에서 수행할 수 있는 리소스의 양과 동시성 수준을 지정합니다.

> [!NOTE] 
> REQUEST_MAX_RESOURCE_GRANT_PERCENT REQUEST_MIN_RESOURCE_GRANT_PERCENT 지정된 값과 동일한 값으로 기본값을 지정하는 선택적 매개 변수입니다.

리소스 클래스를 선택하는 것과 마찬가지로 REQUEST_MIN_RESOURCE_GRANT_PERCENT 구성하면 요청에서 사용하는 리소스의 값이 설정됩니다.  설정 값으로 표시된 리소스의 양은 실행을 시작하기 전에 요청에 대한 할당에 대해 보장됩니다.  리소스 클래스에서 워크로드 그룹으로 마이그레이션하는 고객의 경우 [사용 방법](sql-data-warehouse-how-to-convert-resource-classes-workload-groups.md) 문서를 따라 리소스 클래스에서 워크로드 그룹으로 시작점으로 매핑하는 것이 좋습니다.

REQUEST_MAX_RESOURCE_GRANT_PERCENT REQUEST_MIN_RESOURCE_GRANT_PERCENT 보다 큰 값으로 구성하면 시스템에서 요청당 더 많은 리소스를 할당할 수 있습니다.  요청을 예약하는 동안 시스템은 공유 풀의 리소스 가용성과 현재 부하에 따라 요청에 대한 실제 리소스 할당을 REQUEST_MIN_RESOURCE_GRANT_PERCENT REQUEST_MAX_RESOURCE_GRANT_PERCENT 결정합니다. 시스템.  쿼리가 예약될 때 리소스는 공유 리소스 [풀에](#shared-pool-resources) 있어야 합니다.  

> [!NOTE] 
> REQUEST_MIN_RESOURCE_GRANT_PERCENT REQUEST_MAX_RESOURCE_GRANT_PERCENT 유효 MIN_PERCENTAGE_RESOURCE 및 CAP_PERCENTAGE_RESOURCE 값에 의존하는 유효 값을 갖습니다.  효과적인 런타임 값은 [sys.dm_workload_management_workload_groups_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-workload-management-workload-group-stats-transact-sql?view=azure-sqldw-latest) 을 참조하십시오.

## <a name="execution-rules"></a>실행 규칙

임시 보고 시스템에서 고객은 실수로 다른 사람의 생산성에 심각한 영향을 미치는 런어웨이 쿼리를 실행할 수 있습니다.  시스템 관리자는 시스템 리소스를 확보하기 위해 가출 쿼리를 죽이는 데 시간을 할애해야 합니다.  워크로드 그룹은 지정된 값을 초과한 쿼리를 취소하도록 쿼리 실행 시간 초과 규칙을 구성하는 기능을 제공합니다.  규칙은 [워크로드 그룹 만들기](/sql/t-sql/statements/create-workload-group-transact-sql?view=azure-sqldw-latest) `QUERY_EXECUTION_TIMEOUT_SEC` 구문에서 매개 변수를 설정하여 구성됩니다.

## <a name="shared-pool-resources"></a>공유 풀 리소스

공유 풀 리소스는 격리를 위해 구성되지 않은 리소스입니다.  MIN_PERCENTAGE_RESOURCE 설정된 워크로드 그룹은 공유 풀의 레버리지 리소스를 0으로 설정하여 요청을 실행합니다.  CAP_PERCENTAGE_RESOURCE MIN_PERCENTAGE_RESOURCE 초과하는 워크로드 그룹도 공유 리소스를 사용했습니다.  공유 풀에서 사용할 수 있는 리소스의 양은 다음과 같이 계산됩니다.

[공유 풀] = 100 `MIN_PERCENTAGE_RESOURCE` - [모든 워크로드 그룹의 합계]

공유 풀의 리소스에 대한 액세스는 [중요도에](sql-data-warehouse-workload-importance.md) 따라 할당됩니다.  중요도 수준이 동일한 요청은 선/선착순으로 공유 풀 리소스에 액세스합니다.

## <a name="next-steps"></a>다음 단계

- [빠른 시작: 워크로드 격리 구성](quickstart-configure-workload-isolation-tsql.md)
- [CREATE WORKLOAD GROUP](/sql/t-sql/statements/create-workload-group-transact-sql?view=azure-sqldw-latest)
- [리소스 클래스를 워크로드 그룹으로 변환합니다.](sql-data-warehouse-how-to-convert-resource-classes-workload-groups.md)
- [워크로드 관리 포털 모니터링](sql-data-warehouse-workload-management-portal-monitor.md).  
