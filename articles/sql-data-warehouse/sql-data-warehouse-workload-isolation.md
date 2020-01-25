---
title: 워크로드 격리
description: Azure SQL Data Warehouse에서 작업 그룹을 사용 하 여 워크 로드 격리를 설정 하기 위한 지침입니다.
services: sql-data-warehouse
author: ronortloff
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: workload-management
ms.date: 01/23/2020
ms.author: rortloff
ms.reviewer: jrasnick
ms.custom: seo-lt-2019
ms.openlocfilehash: 86390132be0440b197b680803e5b6032670a7d1c
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/24/2020
ms.locfileid: "76721033"
---
# <a name="sql-data-warehouse-workload-group-isolation-preview"></a>작업 그룹 격리 SQL Data Warehouse (미리 보기)

이 문서에서는 작업 그룹을 사용 하 여 작업 격리를 구성 하 고, 리소스를 포함 하 고, 쿼리 실행을 위한 런타임 규칙을 적용 하는 방법을 설명 합니다.

## <a name="workload-groups"></a>워크로드 그룹

작업 그룹은 요청 집합에 대 한 컨테이너 이며 워크 로드 격리를 비롯 한 작업 관리를 시스템에 구성 하는 방법에 대 한 기본입니다.  작업 그룹은 [CREATE 작업 그룹](/sql/t-sql/statements/create-workload-group-transact-sql?view=azure-sqldw-latest) 구문을 사용 하 여 만듭니다.  간단한 워크 로드 관리 구성은 데이터 로드와 사용자 쿼리를 관리할 수 있습니다.  예를 들어 `wgDataLoads` 이라는 작업 그룹은 시스템에 로드 되는 데이터에 대 한 작업 측면을 정의 합니다. 또한 `wgUserQueries` 이라는 작업 그룹은 쿼리를 실행 하는 사용자가 시스템에서 데이터를 읽는 데 필요한 작업 측면을 정의 합니다.

다음 섹션에서는 작업 그룹이 격리, 포함 및 요청 리소스 정의를 정의 하 고 실행 규칙을 준수 하는 기능을 제공 하는 방법을 설명 합니다.

## <a name="workload-isolation"></a>워크로드 격리

워크 로드 격리는 작업 그룹에 대 한 리소스를 단독으로 예약 하는 것을 의미 합니다.  워크 로드 격리는 [작업 그룹 만들기](/sql/t-sql/statements/create-workload-group-transact-sql?view=azure-sqldw-latest) 구문에서 MIN_PERCENTAGE_RESOURCE 매개 변수를 0 보다 크게 구성 하 여 수행 됩니다.  엄격한 Sla를 준수 해야 하는 연속 실행 작업의 경우 격리는 작업 그룹에 대 한 리소스를 항상 사용할 수 있도록 합니다. 

워크 로드 격리를 구성 하면 보장 된 수준의 동시성이 암시적으로 정의 됩니다. 예를 들어 `MIN_PERCENTAGE_RESOURCE` 30%로 설정 되 고 `REQUEST_MIN_RESOURCE_GRANT_PERCENT` 2%로 설정 된 작업 그룹은 15 개의 동시성이 보장 됩니다.  리소스의 15-2% 슬롯은 `REQUEST_*MAX*_RESOURCE_GRANT_PERCENT` 구성 된 방법에 관계 없이 항상 작업 그룹 내에서 예약 되어 있으므로 동시성 수준이 보장 됩니다.  `REQUEST_MAX_RESOURCE_GRANT_PERCENT`이 `REQUEST_MIN_RESOURCE_GRANT_PERCENT` 보다 크고 `CAP_PERCENTAGE_RESOURCE`이 `MIN_PERCENTAGE_RESOURCE` 보다 크면 요청 마다 추가 리소스가 추가 됩니다.  `REQUEST_MAX_RESOURCE_GRANT_PERCENT`와 `REQUEST_MIN_RESOURCE_GRANT_PERCENT`가 같고 `CAP_PERCENTAGE_RESOURCE`이 `MIN_PERCENTAGE_RESOURCE`보다 크면 추가 동시성이 가능 합니다.  보장 된 동시성을 확인 하려면 아래 방법을 고려 하세요.

[보장 된 동시성] = [`MIN_PERCENTAGE_RESOURCE`]/[`REQUEST_MIN_RESOURCE_GRANT_PERCENT`]

> [!NOTE] 
> Min_percentage_resource에 대 한 특정 서비스 수준 실행 가능한 최소 값이 있습니다.  자세한 내용은 [유효한 값](/sql/t-sql/statements/create-workload-group-transact-sql?view=azure-sqldw-latest#effective-values) 을 참조 하세요.

작업 격리가 없으면 요청은 리소스의 [공유 풀](#shared-pool-resources) 에서 작동 합니다.  공유 풀의 리소스에 대 한 액세스는 보장 되지 않으며 [중요도](sql-data-warehouse-workload-importance.md) 를 기준으로 할당 됩니다.

워크 로드 격리 구성은 작업 그룹에 활성 요청이 없는 경우에도 작업 그룹에 리소스가 할당 될 때 주의 해 서 수행 해야 합니다. Over-격리를 구성 하면 전반적인 시스템 사용률이 감소 될 수 있습니다.

사용자는 100%의 워크 로드 격리를 구성 하는 워크 로드 관리 솔루션을 피해 야 합니다 100. 모든 작업 그룹에 대해 구성 된 min_percentage_resource의 합계가 100%와 같으면%의 격리가 달성 됩니다.  이 유형의 구성은 지나치게 제한적 이며 고정 되어 실수로 잘못 분류 된 리소스 요청을 위한 공간을 거의 두지 않습니다. 격리를 위해 구성 되지 않은 작업 그룹에서 하나의 요청을 실행 하도록 허용 하는 프로 비전이 있습니다. 이 요청에 할당 된 리소스는 시스템 Dmv에 0으로 표시 되 고 시스템 예약 리소스에서 smallrc 수준의 리소스 부여를 받습니다.

> [!NOTE] 
> 리소스 사용률을 최적화 하기 위해 일부 격리를 활용 하는 워크 로드 관리 솔루션을 사용 하 여 Sla를 충족 하 고 [작업 중요도](sql-data-warehouse-workload-importance.md)에 따라 액세스 되는 공유 리소스를 혼합 합니다.

## <a name="workload-containment"></a>워크 로드 포함

워크 로드 포함은 작업 그룹에서 사용할 수 있는 리소스의 양을 제한 하는 것을 의미 합니다.  작업 포함은 [CREATE 작업 그룹](/sql/t-sql/statements/create-workload-group-transact-sql?view=azure-sqldw-latest) 구문에서 CAP_PERCENTAGE_RESOURCE 매개 변수를 100 미만으로 구성 하 여 수행 됩니다.  임시 쿼리를 통해 가상 분석을 실행할 수 있도록 사용자가 시스템에 대 한 읽기 액세스 권한을 필요로 하는 시나리오를 고려해 보세요.  이러한 유형의 요청은 시스템에서 실행 되는 다른 작업에 부정적인 영향을 미칠 수 있습니다.  제약을 구성 하면 리소스의 양이 제한 됩니다.

워크 로드 포함을 구성 하면 최대 수준의 동시성이 암시적으로 정의 됩니다.  CAP_PERCENTAGE_RESOURCE를 60%로 설정 하 고 REQUEST_MIN_RESOURCE_GRANT_PERCENT 1%로 설정 하면 작업 그룹에 대해 최대 60 수준까지 허용 됩니다.  최대 동시성을 확인 하려면 아래에 포함 된 방법을 고려 하세요.

[최대 동시성] = [`CAP_PERCENTAGE_RESOURCE`]/[`REQUEST_MIN_RESOURCE_GRANT_PERCENT`]

> [!NOTE] 
> 0 보다 큰 수준에서 MIN_PERCENTAGE_RESOURCE 있는 작업 그룹을 만든 경우 작업 그룹의 유효 CAP_PERCENTAGE_RESOURCE 100%에 도달 하지 않습니다.  유효 런타임 값은 [dm_workload_management_workload_groups_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-workload-management-workload-group-stats-transact-sql?view=azure-sqldw-latest) 를 참조 하세요.

## <a name="resources-per-request-definition"></a>요청 정의 당 리소스

작업 그룹은 [작업 만들기 그룹](/sql/t-sql/statements/create-workload-group-transact-sql?view=azure-sqldw-latest) 구문에서 REQUEST_MIN_RESOURCE_GRANT_PERCENT 및 REQUEST_MAX_RESOURCE_GRANT_PERCENT 매개 변수를 사용 하 여 요청당 할당 된 리소스의 최소 및 최대 양을 정의 하는 메커니즘을 제공 합니다.  이 경우 리소스는 CPU 및 메모리입니다.  이러한 값을 구성 하면 시스템에서 수행할 수 있는 리소스 양과 동시성 수준을 결정 합니다.

> [!NOTE] 
> REQUEST_MAX_RESOURCE_GRANT_PERCENT은 REQUEST_MIN_RESOURCE_GRANT_PERCENT에 대해 지정 된 것과 동일한 값을 기본값으로 설정 하는 선택적 매개 변수입니다.

리소스 클래스를 선택 하는 것 처럼 REQUEST_MIN_RESOURCE_GRANT_PERCENT를 구성 하면 요청에서 사용 하는 리소스의 값이 설정 됩니다.  집합 값으로 표시 되는 리소스의 양은 실행을 시작 하기 전에 요청에 할당 될 때 보장 됩니다.  리소스 클래스에서 작업 그룹으로 마이그레이션하는 고객의 경우 리소스 클래스에서 작업 그룹으로 시작 지점으로 매핑하 [는 방법](sql-data-warehouse-how-to-convert-resource-classes-workload-groups.md) 문서를 참조 하세요.

REQUEST_MIN_RESOURCE_GRANT_PERCENT 보다 큰 값으로 REQUEST_MAX_RESOURCE_GRANT_PERCENT를 구성 하면 시스템에서 요청당 추가 리소스를 할당할 수 있습니다.  요청을 예약 하는 동안 시스템은 REQUEST_MIN_RESOURCE_GRANT_PERCENT와 REQUEST_MAX_RESOURCE_GRANT_PERCENT 사이에 있는 요청에 대 한 실제 리소스 할당을 결정 합니다 .이는 공유 풀의 리소스 가용성과 컴퓨터.  쿼리가 예약 될 때 리소스의 [공유 풀](#shared-pool-resources) 에 리소스가 있어야 합니다.  

> [!NOTE] 
> REQUEST_MIN_RESOURCE_GRANT_PERCENT 및 REQUEST_MAX_RESOURCE_GRANT_PERCENT에는 유효한 MIN_PERCENTAGE_RESOURCE 및 CAP_PERCENTAGE_RESOURCE 값에 따라 달라 지는 유효 값이 있습니다.  유효 런타임 값은 [dm_workload_management_workload_groups_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-workload-management-workload-group-stats-transact-sql?view=azure-sqldw-latest) 를 참조 하세요.

## <a name="execution-rules"></a>실행 규칙

임시 보고 시스템에서 고객은 다른 사용자의 생산성에 심각한 영향을 주는 런어웨이 쿼리를 실수로 실행할 수 있습니다.  시스템 관리자는 시스템 리소스를 확보 하기 위해 런어웨이 쿼리를 종료 하는 데 시간을 할애 합니다.  작업 그룹은 지정 된 값을 초과 하는 쿼리를 취소 하도록 쿼리 실행 제한 시간 규칙을 구성 하는 기능을 제공 합니다.  규칙은 [CREATE 작업 그룹](/sql/t-sql/statements/create-workload-group-transact-sql?view=azure-sqldw-latest) 구문에서 `QUERY_EXECUTION_TIMEOUT_SEC` 매개 변수를 설정 하 여 구성 됩니다.

## <a name="shared-pool-resources"></a>공유 풀 리소스

공유 풀 리소스는 격리를 위해 구성 되지 않은 리소스입니다.  MIN_PERCENTAGE_RESOURCE가 0으로 설정 된 작업 그룹은 공유 풀의 리소스를 활용 하 여 요청을 실행 합니다.  CAP_PERCENTAGE_RESOURCE 보다 큰 작업 그룹에도 공유 리소스가 사용 MIN_PERCENTAGE_RESOURCE.  공유 풀에서 사용할 수 있는 리소스의 양은 다음과 같이 계산 됩니다.

[공유 풀] = 100-[모든 작업 그룹의 `MIN_PERCENTAGE_RESOURCE` 합계]

공유 풀의 리소스에 대 한 액세스는 [중요도](sql-data-warehouse-workload-importance.md) 를 기준으로 할당 됩니다.  중요도 수준이 같은 요청은 첫 번째/부터 처음으로 공유 풀 리소스에 액세스 합니다.

## <a name="next-steps"></a>다음 단계

- [빠른 시작: 워크 로드 격리 구성](quickstart-configure-workload-isolation-tsql.md)
- [작업 그룹 만들기](/sql/t-sql/statements/create-workload-group-transact-sql?view=azure-sqldw-latest)
- [리소스 클래스를 작업 그룹으로 변환](sql-data-warehouse-how-to-convert-resource-classes-workload-groups.md)합니다.
- [작업 관리 포털 모니터링](sql-data-warehouse-workload-management-portal-monitor.md).  
