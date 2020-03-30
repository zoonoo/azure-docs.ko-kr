---
title: Azure SQL 데이터베이스의 쿼리 성능 문제 유형
description: 이 문서에서는 Azure SQL Database의 쿼리 성능 문제 유형에 대해 알아보고 이러한 문제로 쿼리를 식별하고 해결하는 방법도 알아봅니다.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: jrasnick, carlrab
ms.date: 03/10/2020
ms.openlocfilehash: e155321c2727bf4ee871ef7be7b61b6a523ec1fc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79256134"
---
# <a name="detectable-types-of-query-performance-bottlenecks-in-azure-sql-database"></a>Azure SQL 데이터베이스에서 검색 가능한 유형의 쿼리 성능 병목 현상

성능 병목 현상을 해결하려고 할 때 쿼리가 실행 중인 상태또는 대기 상태에 있는 동안 병목 현상이 발생하는지 여부를 확인하는 것으로 시작합니다. 이 결정에 따라 다른 해상도가 적용됩니다. 다음 다이어그램을 사용하여 실행 중인 관련 문제 또는 대기 관련 문제를 일으킬 수 있는 요소를 이해할 수 있습니다. 각 문제 유형과 관련된 문제와 해결 방법을 이 문서에서설명합니다.

Azure SQL Database [지능형 인사이트](sql-database-intelligent-insights-troubleshoot-performance.md#detectable-database-performance-patterns) 또는 SQL 서버 [DMV를](sql-database-monitoring-with-dmvs.md) 사용하여 이러한 유형의 성능 병목 현상을 검색할 수 있습니다.

![워크로드 상태](./media/sql-database-monitor-tune-overview/workload-states.png)

**실행 관련 문제**: 실행 관련 문제는 일반적으로 컴파일 문제와 관련이 있어 최적이 아닌 쿼리 계획 또는 부족하거나 과도하게 사용된 리소스와 관련된 실행 문제가 발생합니다.
**대기 관련 문제**: 대기 관련 문제는 일반적으로 다음과 관련이 있습니다.

- 잠금(차단)
- I/O
- TempDB 사용과 관련된 경합
- 메모리 부여 대기

## <a name="compilation-problems-resulting-in-a-suboptimal-query-plan"></a>최적이 아닌 쿼리 계획의 결과로 인한 컴파일 문제

SQL 쿼리 최적화 프로그램에 의해 생성된 최적이 아닌 계획은 쿼리 성능 저하의 원인이 될 수 있습니다. SQL 쿼리 최적화 프로그램은 누락된 인덱스, 부실 한 통계, 처리할 행 수의 잘못된 추정 또는 필요한 메모리의 부정확한 추정으로 인해 최적이 아닌 계획을 생성할 수 있습니다. 쿼리가 과거 또는 다른 인스턴스(관리되는 인스턴스 또는 SQL Server 인스턴스)에서 더 빠르게 실행되었다는 것을 알고 있는 경우 실제 실행 계획을 비교하여 쿼리가 다른지 확인합니다.

- 다음 방법 중 하나를 사용하여 누락된 인덱스를 식별합니다.

  - [지능형 인사이트를](sql-database-intelligent-insights-troubleshoot-performance.md#missing-index)사용합니다.
  - 단일 및 풀화 된 데이터베이스에 대한 [데이터베이스 관리자.](sql-database-advisor.md)
  - Dmv. 이 예제에서는 누락된 인덱스의 영향, DMV를 사용하여 [누락된 인덱스를](sql-database-performance-guidance.md#identifying-and-adding-missing-indexes) 검색하는 방법 및 누락된 인덱스 권장 사항을 구현하는 데 미치는 영향을 보여 주며, 누락된 인덱스 권장 사항을 구현하는 데 미치는 영향을 보여 주며, 누락된 인덱스 권장 사항을 구현하는 방법을 보여 주며, 누락된 인덱스 권장 사항을 구현하는 방법을 보여 주며, 누락된 인덱스 권장 사항을 구현하는 방법을 보여 주며, 누락된 인덱스 권장 사항을 구현하는 방법을 보여 주실 수 있습니다.
- 더 나은 계획을 얻기 위해 [쿼리 힌트를](https://docs.microsoft.com/sql/t-sql/queries/hints-transact-sql-query)적용하거나 [통계를 업데이트하거나](https://docs.microsoft.com/sql/t-sql/statements/update-statistics-transact-sql) [인덱스를 다시 작성하십시오.](https://docs.microsoft.com/sql/relational-databases/indexes/reorganize-and-rebuild-indexes) Azure SQL Database에서 [자동 계획 수정을](sql-database-automatic-tuning.md) 사용하여 이러한 문제를 자동으로 완화합니다.

  이 [예제에서는](sql-database-performance-guidance.md#query-tuning-and-hinting) 매개 변수화된 쿼리로 인한 최적이 아닌 쿼리 계획의 영향, 이 조건을 검색하는 방법 및 쿼리 힌트를 사용하여 해결하는 방법을 보여 주며, 이를 해결하는 방법을 보여 주습니다.

- 데이터베이스 호환성 수준을 변경하고 지능형 쿼리 처리를 구현해 보십시오. SQL 쿼리 최적화 프로그램은 데이터베이스의 호환성 수준에 따라 다른 쿼리 계획을 생성할 수 있습니다. 호환성 수준이 높을수록 보다 [지능적인 쿼리 처리 기능을](https://docs.microsoft.com/sql/relational-databases/performance/intelligent-query-processing)제공합니다.

  - 쿼리 처리에 대한 자세한 내용은 [쿼리 처리 아키텍처 가이드를](https://docs.microsoft.com/sql/relational-databases/query-processing-architecture-guide)참조하십시오.
  - 데이터베이스 호환성 수준을 변경하고 호환성 수준 간의 차이점에 대해 자세히 알아보려면 [ALTER DATABASE](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-compatibility-level)를 참조하십시오.
  - 카디널리티 추정에 대한 자세한 내용은 [카디널리티 추정을](https://docs.microsoft.com/sql/relational-databases/performance/cardinality-estimation-sql-server) 참조하십시오.

## <a name="resolving-queries-with-suboptimal-query-execution-plans"></a>최적이 아닌 쿼리 실행 계획으로 쿼리 해결

다음 섹션에서는 최적이 아닌 쿼리 실행 계획을 사용하여 쿼리를 해결하는 방법에 대해 설명합니다.

### <a name="queries-that-have-parameter-sensitive-plan-psp-problems"></a><a name="ParamSniffing"></a>PSP(매개 변수 구분 계획) 문제가 있는 쿼리

PSP(매개 변수 중요 계획) 문제는 쿼리 최적화 프로그램이 특정 매개 변수 값(또는 값 집합)에 대해서만 최적인 쿼리 실행 계획을 생성하고 캐시된 계획이 연속으로 사용되는 매개 변수 값에 대해 최적이 아닌 경우에 발생합니다. 실행. 최적이 아닌 계획은 쿼리 성능 문제를 일으키고 전체 워크로드 처리량을 저하시킬 수 있습니다.

매개 변수 스니핑 및 쿼리 처리에 대한 자세한 내용은 [쿼리 처리 아키텍처 가이드를](/sql/relational-databases/query-processing-architecture-guide#ParamSniffing)참조하십시오.

몇 가지 해결 방법으로 PSP 문제를 해결할 수 있습니다. 각 해결 해결 에는 다음과 같은 장단점이 있습니다.

- 각 쿼리 실행 시 [RECOMPILE](https://docs.microsoft.com/sql/t-sql/queries/hints-transact-sql-query) 쿼리 힌트를 사용합니다. 이 해결 방법을 사용할 경우 품질은 향상되지만 컴파일 시간 및 CPU가 늘어납니다. 높은 `RECOMPILE` 처리량이 필요한 워크로드에서는 이 옵션을 사용할 수 없는 경우가 많습니다.
- [OPTION(OPTIMIZE FOR...)](https://docs.microsoft.com/sql/t-sql/queries/hints-transact-sql-query) 쿼리 힌트를 사용하여 대부분의 매개 변수 값 가능성에 충분한 계획을 생성하는 일반적인 매개 변수 값으로 실제 매개 변수 값을 재정의합니다. 이 옵션을 사용하려면 최적의 매개 변수 값 및 관련 계획 특성을 적절히 이해해야 합니다.
- [옵션(알 수 없음에 최적화)](https://docs.microsoft.com/sql/t-sql/queries/hints-transact-sql-query) 쿼리 힌트를 사용하여 실제 매개 변수 값을 재정의하고 대신 밀도 벡터 평균을 사용합니다. 또한 로컬 변수에서 들어오는 매개 변수 값을 캡처한 다음 매개 변수 자체대신 조건자 내에서 로컬 변수를 사용하여 이 작업을 수행할 수 있습니다. 이 수정을 위해 평균 밀도는 *충분해야*합니다.
- [DISABLE_PARAMETER_SNIFFING](https://docs.microsoft.com/sql/t-sql/queries/hints-transact-sql-query) 쿼리 힌트를 사용하여 매개 변수 스니핑을 완전히 사용하지 않도록 설정합니다.
- [KEEPFIXEDPLAN](https://docs.microsoft.com/sql/t-sql/queries/hints-transact-sql-query) 쿼리 힌트를 사용하여 캐시에서 재컴파일을 방지합니다. 이 해결 방법은 충분한 공통 계획이 이미 캐시에 있는 계획이라고 가정합니다. 또한 자동 통계 업데이트를 비활성화하여 좋은 계획이 제거되고 새로운 잘못된 계획이 컴파일될 가능성을 줄일 수 있습니다.
- [쿼리를](https://docs.microsoft.com/sql/t-sql/queries/hints-transact-sql-query) 다시 작성하고 쿼리 텍스트에 힌트를 추가하여 USE PLAN 쿼리 힌트를 명시적으로 사용하여 계획을 강제합니다. 또는 쿼리 저장소를 사용하거나 [자동 튜닝을](sql-database-automatic-tuning.md)사용하도록 설정하여 특정 계획을 설정합니다.
- 단일 프로시저를 각각을 조건부 논리 및 연결된 매개 변수 값을 기준으로 사용할 수 있는 중첩된 프로시저 세트로 바꿉니다.
- 정적 프로시저 정의를 대신하는 동적 문자열 실행을 만듭니다.

PSP 문제 해결에 대한 자세한 내용은 다음 블로그 게시물을 참조하세요.

- [나는 매개 변수 냄새](https://docs.microsoft.com/archive/blogs/queryoptteam/i-smell-a-parameter)
- [Conor vs. 동적 SQL vs. 프로시저 및 매개 변수화된 쿼리에 대한 계획 품질](https://blogs.msdn.microsoft.com/conor_cunningham_msft/2009/06/03/conor-vs-dynamic-sql-vs-procedures-vs-plan-quality-for-parameterized-queries/)
- [SQL Server의 SQL 쿼리 최적화 기술: 매개 변수 스니핑](https://www.sqlshack.com/query-optimization-techniques-in-sql-server-parameter-sniffing/)

### <a name="compile-activity-caused-by-improper-parameterization"></a>부적절한 매개 변수화로 인한 컴파일 작업

쿼리에 리터럴이 있는 경우 데이터베이스 엔진이 자동으로 문을 매개변수화하거나 사용자가 문과 명시적으로 매개 변수를 지정하여 컴파일 수를 줄입니다. 동일한 패턴을 사용 하지만 다른 리터럴 값을 사용 하 여 쿼리에 대 한 컴파일의 수가 높은 CPU 사용 될 수 있습니다. 마찬가지로 리터럴이 계속되는 쿼리만 부분적으로 매개 변수화하면 데이터베이스 엔진은 쿼리를 더 이상 매개 변수화하지 않습니다.

다음은 부분적으로 매개 변수화된 쿼리의 예입니다.

```sql
SELECT *
FROM t1 JOIN t2 ON t1.c1 = t2.c1
WHERE t1.c1 = @p1 AND t2.c2 = '961C3970-0E54-4E8E-82B6-5545BE897F8F'
```

이 예제에서는 `t1.c1` `@p1`GUID를 `t2.c2` 리터럴로 계속 수행합니다. 이 경우 에 대 한 `c2`값을 변경 하는 경우 쿼리 다른 쿼리로 처리 하 고 새 컴파일이 발생 합니다. 이 예제에서 컴파일을 줄이려면 GUID도 매개 변수화합니다.

다음 쿼리는 쿼리가 제대로 매개 변수화되었는지 여부를 확인하기 위해 쿼리 해시별 쿼리 수를 보여 주며 있습니다.

```sql
SELECT TOP 10
  q.query_hash
  , count (distinct p.query_id ) AS number_of_distinct_query_ids
  , min(qt.query_sql_text) AS sampled_query_text
FROM sys.query_store_query_text AS qt
  JOIN sys.query_store_query AS q
     ON qt.query_text_id = q.query_text_id
  JOIN sys.query_store_plan AS p
     ON q.query_id = p.query_id
  JOIN sys.query_store_runtime_stats AS rs
     ON rs.plan_id = p.plan_id
  JOIN sys.query_store_runtime_stats_interval AS rsi
     ON rsi.runtime_stats_interval_id = rs.runtime_stats_interval_id
WHERE
  rsi.start_time >= DATEADD(hour, -2, GETUTCDATE())
  AND query_parameterization_type_desc IN ('User', 'None')
GROUP BY q.query_hash
ORDER BY count (distinct p.query_id) DESC
```

### <a name="factors-that-affect-query-plan-changes"></a>쿼리 계획 변경에 영향을 주는 요소

쿼리 실행 계획 재컴파일으로 인해 원래 캐시된 계획과 다른 생성된 쿼리 계획이 생성될 수 있습니다. 기존 원래 계획은 여러 가지 이유로 자동으로 다시 컴파일될 수 있습니다.

- 스키마의 변경 내용은 쿼리에서 참조됩니다.
- 테이블에 대한 데이터 변경 사항은 쿼리에서 참조됩니다.
- 쿼리 컨텍스트 옵션이 변경되었습니다.

컴파일된 계획은 다음과 같은 여러 가지 이유로 캐시에서 배출될 수 있습니다.

- 인스턴스 다시 시작
- 데이터베이스 범위 구성 변경
- 메모리 압력
- 캐시를 지우기 위한 명시적 요청

RECOMPILE 힌트를 사용하는 경우 계획은 캐시되지 않습니다.

다시 컴파일(또는 캐시 제거 후 새 컴파일)으로 인해 원본과 동일한 쿼리 실행 계획이 생성될 수 있습니다. 계획이 이전 또는 원래 계획에서 변경되면 다음과 같은 설명이 있을 수 있습니다.

- **변경된 물리적 디자인**: 예를 들어 새로 생성된 인덱스는 쿼리의 요구 사항을 보다 효과적으로 다룹니다. 쿼리 최적화 프로그램이 쿼리 실행의 첫 번째 버전에 대해 원래 선택한 데이터 구조를 사용하는 것보다 새 인덱스를 사용하는 것이 더 최적이라고 판단하는 경우 새 인덱스를 새 컴파일에 사용할 수 있습니다. 참조된 개체를 물리적으로 변경하면 컴파일 타임에 새 계획 선택이 발생할 수 있습니다.

- **서버 리소스 차이:** 한 시스템의 계획이 다른 시스템의 계획과 다를 경우 사용 가능한 프로세서 수와 같은 리소스 가용성이 생성되는 계획에 영향을 미칠 수 있습니다. 예를 들어 한 시스템에 프로세서가 더 많은 경우 병렬 계획을 선택할 수 있습니다.

- **다른 통계**: 참조 된 객체와 관련된 통계가 변경되었거나 원래 시스템의 통계와 실질적으로 다를 수 있습니다. 통계가 변경되고 다시 컴파일이 발생하면 쿼리 최적화 프로그램은 변경된 시점부터 통계를 사용합니다. 수정된 통계의 데이터 분포 및 빈도는 원래 컴파일과 다를 수 있습니다. 이러한 변경 내용은 카디널리티 추정을 만드는 데 사용됩니다. *카디널리티 예측은* 논리 쿼리 트리를 통해 흐를 것으로 예상되는 행 수입니다. 카디널리티 예측을 변경하면 다른 물리적 연산자 및 관련 작업 주문을 선택할 수 있습니다. 통계를 약간 변경해도 쿼리 실행 계획이 변경될 수 있습니다.

- **변경된 데이터베이스 호환성 수준 또는 카디널리티 추정기 버전:** 데이터베이스 호환성 수준을 변경하면 다른 쿼리 실행 계획을 생성할 수 있는 새 전략 및 기능을 사용할 수 있습니다. 데이터베이스 호환성 수준 외에도 비활성화되거나 활성화된 추적 플래그 4199 또는 데이터베이스 범위 구성의 변경된 상태QUERY_OPTIMIZER_HOTFIXES 컴파일 타임의 쿼리 실행 계획 선택에도 영향을 미칠 수 있습니다. 추적 플래그 9481(강제 레거시 CE) 및 2312(강제 기본 CE)도 계획에 영향을 미칩니다.

## <a name="resource-limits-issues"></a>리소스 제한 문제

최적이 아닌 쿼리 계획 및 누락된 인덱스와 관련이 없는 느린 쿼리 성능은 일반적으로 부족하거나 과도하게 사용된 리소스와 관련이 있습니다. 쿼리 계획이 최적이면 쿼리(및 데이터베이스)가 데이터베이스, 탄력적 풀 또는 관리되는 인스턴스의 리소스 제한에 부딪칠 수 있습니다. 예를 들어 서비스 수준에 대한 초과 로그 쓰기 처리량이 있을 수 있습니다.

- Azure 포털을 사용하여 리소스 문제 검색: 리소스 제한이 문제인지 확인하려면 [SQL Database 리소스 모니터링을](sql-database-monitor-tune-overview.md#sql-database-resource-monitoring)참조하십시오. 단일 데이터베이스 및 탄력적 풀의 경우 [데이터베이스 관리자 성능 권장 사항](sql-database-advisor.md) 및 쿼리 성능 [인사이트를](sql-database-query-performance.md)참조하십시오.
- [지능형 인사이트를](sql-database-intelligent-insights-troubleshoot-performance.md#reaching-resource-limits) 사용하여 리소스 제한 감지
- [DMV를](sql-database-monitoring-with-dmvs.md)사용하여 리소스 문제 감지 :

  - [sys.dm_db_resource_stats](sql-database-monitoring-with-dmvs.md#monitor-resource-use) DMV는 SQL 데이터베이스에 대한 CPU, I/O 및 메모리 사용량을 반환합니다. 데이터베이스에 활동이 없는 경우에도 15초 간격마다 하나의 행이 존재합니다. 기록 데이터는 한 시간 동안 유지됩니다.
  - [sys.resource_stats](sql-database-monitoring-with-dmvs.md#monitor-resource-use) DMV는 Azure SQL Database에 대한 CPU 사용량 및 저장소 데이터를 반환합니다. 데이터는 5분 간격으로 수집되고 집계됩니다.
  - [점증적으로 CPU 사용량이 높은 여러 개별 쿼리](sql-database-monitoring-with-dmvs.md#many-individual-queries-that-cumulatively-consume-high-cpu)
  - 

문제를 부족한 리소스로 식별하는 경우 리소스를 업그레이드하여 SQL 데이터베이스의 용량을 늘려 CPU 요구 사항을 흡수할 수 있습니다. 자세한 내용은 [Azure SQL 데이터베이스의 단일 데이터베이스 리소스 크기 조정](sql-database-single-database-scale.md) 및 Azure SQL [데이터베이스의 탄력적 풀 리소스 확장을](sql-database-elastic-pool-scale.md)참조하십시오. 관리되는 인스턴스 의 확장에 대한 자세한 내용은 [서비스 계층 리소스 제한을](sql-database-managed-instance-resource-limits.md#service-tier-characteristics)참조하십시오.

## <a name="performance-problems-caused-by-increased-workload-volume"></a>워크로드 볼륨 증가로 인한 성능 문제

응용 프로그램 트래픽과 워크로드 볼륨이 증가하면 CPU 사용량이 증가할 수 있습니다. 그러나이 문제를 올바르게 진단하려면주의해야합니다. 높은 CPU 문제가 발생하면 다음 질문에 답하여 워크로드 볼륨의 변경으로 인해 증가가 발생하는지 여부를 확인합니다.

- 응용 프로그램의 쿼리가 높은 CPU 문제의 원인입니까?
- [식별할 수 있는 CPU 사용량이 가장 많은 쿼리의](sql-database-monitoring-with-dmvs.md#the-cpu-issue-occurred-in-the-past)경우:

  - 여러 실행 계획이 동일한 쿼리와 연결되나요? 그렇다면, 왜?
  - 동일한 실행 계획을 가진 쿼리의 경우 실행 시간이 일치합니까? 실행 횟수가 증가했습니까? 이 경우 워크로드가 증가하면 성능 문제가 발생할 수 있습니다.

요약하면 쿼리 실행 계획이 다르게 실행되지 않았지만 실행 횟수와 함께 CPU 사용량이 증가한 경우 성능 문제가 워크로드 증가와 관련이 있는 것일 수 있습니다.

CPU 문제를 유발하는 워크로드 볼륨 변경을 식별하는 것이 항상 쉬운 것은 아닙니다. 다음 항목을 고려합니다.

- **변경된 리소스 사용량**: 예를 들어 CPU 사용량이 장기간 80%로 증가한 시나리오를 생각해 보십시오. CPU 사용량만으로는 워크로드 볼륨이 변경된 것은 아닙니다. 쿼리 실행 계획의 회귀 및 데이터 배포의 변경은 응용 프로그램이 동일한 워크로드를 실행하는 경우에도 더 많은 리소스 사용에 기여할 수 있습니다.

- **새 쿼리의 모양**: 응용 프로그램이 다른 시간에 새 쿼리 집합을 구동할 수 있습니다.

- **요청 수의 증가 또는 감소**: 이 시나리오는 워크로드의 가장 확실한 척도입니다. 쿼리 수가 많다고 해서 항상 리소스 사용률이 많은 것은 아닙니다. 그러나 이 메트릭은 다른 요인이 변경되지 않았다고 가정할 때 여전히 중요한 신호입니다.

지능형 인사이트를 사용하여 [워크로드 증가를](sql-database-intelligent-insights-troubleshoot-performance.md#workload-increase) 감지하고 [회귀를 계획합니다.](sql-database-intelligent-insights-troubleshoot-performance.md#plan-regression)

## <a name="waiting-related-problems"></a>대기 관련 문제

최적이 아닌 계획및 실행 문제와 관련된 *대기 관련* 문제를 제거한 후에는 일반적으로 쿼리가 일부 리소스를 기다리고 있을 수 있습니다. 대기 관련 문제는 다음과 같은 문제로 인해 발생할 수 있습니다.

- **차단**:

  한 쿼리는 데이터베이스의 개체에 대한 잠금을 보유하는 반면 다른 쿼리는 동일한 개체에 액세스하려고 할 수 있습니다. [DMV](sql-database-monitoring-with-dmvs.md#monitoring-blocked-queries) 또는 [지능형 인사이트를](sql-database-intelligent-insights-troubleshoot-performance.md#locking)사용하여 차단 쿼리를 식별할 수 있습니다.
- **IO 문제**

  쿼리는 페이지가 데이터 또는 로그 파일에 기록되기를 기다리고 있을 수 있습니다. 이 경우 DMV에서 `INSTANCE_LOG_RATE_GOVERNOR`" `WRITE_LOG`또는 `PAGEIOLATCH_*` 대기 통계를 확인합니다. DMV를 사용하여 [IO 성능 문제를 식별합니다.](sql-database-monitoring-with-dmvs.md#identify-io-performance-issues)
- **TempDB 문제**

  워크로드에서 임시 테이블을 사용하거나 계획에 TempDB 유출이 있는 경우 쿼리에 TempDB 처리량에 문제가 있을 수 있습니다. DMV를 사용하여 [TempDB 문제를 ID로 ID를](sql-database-monitoring-with-dmvs.md#identify-tempdb-performance-issues)확인합니다.
- **메모리 관련 문제**

  워크로드에 메모리가 충분하지 않으면 페이지 수명이 떨어질 수 있으며 쿼리에 필요한 것보다 메모리가 적을 수 있습니다. 경우에 따라 쿼리 최적화 의 기본 제공 인텔리전스는 메모리 관련 문제를 해결합니다. DMV 를 사용하여 [메모리 부여 문제를 식별합니다.](sql-database-monitoring-with-dmvs.md#identify-memory-grant-wait-performance-issues)

### <a name="methods-to-show-top-wait-categories"></a>상위 대기 범주를 표시하는 방법

이러한 메서드는 일반적으로 대기 유형의 상위 범주를 표시 하는 데 사용 됩니다.

- 지능형 인사이트 사용으로 [대기 증가로](sql-database-intelligent-insights-troubleshoot-performance.md#increased-wait-statistic) 성능이 저하된 쿼리 식별
- [쿼리 저장소를](https://docs.microsoft.com/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store) 사용하여 시간에 따라 각 쿼리에 대한 대기 통계를 찾습니다. 쿼리 저장소에서 대기 유형은 대기 범주로 결합됩니다. [sys.query_store_wait_stats](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-query-store-wait-stats-transact-sql#wait-categories-mapping-table)에서 대기 범주의 매핑을 찾을 수 있습니다.
- [sys.dm_db_wait_stats](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-db-wait-stats-azure-sql-database) 사용하여 쿼리 작업 중에 실행된 스레드에서 발생하는 모든 대기에 대한 정보를 반환합니다. 이 집계된 보기를 사용하여 Azure SQL Database 및 특정 쿼리 및 일괄 처리에 대한 성능 문제를 진단할 수 있습니다. 쿼리는 리소스, 큐 대기 또는 외부 대기를 기다리는 동안 대기할 수 있습니다.
- [sys.dm_os_waiting_tasks](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-os-waiting-tasks-transact-sql) 사용하여 일부 리소스에서 대기 중인 작업 큐에 대한 정보를 반환합니다.

높은 CPU 시나리오에서 쿼리 저장소 및 대기 통계에 다음과 같은 경우 CPU 사용량이 반영되지 않을 수 있습니다.

- CPU 사용량이 많은 쿼리는 여전히 실행 중입니다.
- 장애 조치(failover)가 발생했을 때 CPU사용량이 많은 쿼리가 실행되고 있었습니다.

쿼리 저장소를 추적하고 통계를 기다리는 DMV는 성공적으로 완료되고 시간 적시간이 정된 쿼리에 대해서만 결과를 표시합니다. 문이 완료될 때까지 현재 실행 중인 문에 대한 데이터가 표시되지 않습니다. 동적 관리 보기 [sys.dm_exec_requests](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql) 사용하여 현재 실행 중인 쿼리 및 관련 작업자 시간을 추적합니다.

> [!TIP]
> 추가 도구:
>
> - [타이거툴박스 대기 및 래치](https://github.com/Microsoft/tigertoolbox/tree/master/Waits-and-Latches)
> - [타이거툴박스 usp_whatsup](https://github.com/Microsoft/tigertoolbox/tree/master/usp_WhatsUp)

## <a name="next-steps"></a>다음 단계

[SQL 데이터베이스 모니터링 및 튜닝 개요](sql-database-monitor-tune-overview.md)