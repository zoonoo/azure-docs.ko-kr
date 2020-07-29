---
title: Azure SQL Database의 쿼리 성능 문제 유형
description: 이 문서에서는 Azure SQL Database의 쿼리 성능 문제 유형에 대해 알아보고 이러한 문제를 사용 하 여 쿼리를 식별 하 고 해결 하는 방법에 대해서도 알아봅니다.
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
ms.openlocfilehash: b33d8db9d43b151cb0405ea24e0bea87e21cbdc9
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "84345345"
---
# <a name="detectable-types-of-query-performance-bottlenecks-in-azure-sql-database"></a>Azure SQL Database에서 쿼리 성능 병목 상태의 검색 가능한 유형
[!INCLUDE[appliesto-sqldb-sqlmi](includes/appliesto-sqldb-sqlmi.md)]

성능 병목 상태를 해결 하려는 경우 쿼리가 실행 중 상태 또는 대기 상태에 있는 동안 병목 현상이 발생 하는지 확인 하 여 시작 합니다. 이러한 결정에 따라 서로 다른 해결 방법이 적용 됩니다. 다음 다이어그램에서는 실행 중인 관련 문제 또는 대기 관련 문제를 일으킬 수 있는 요소를 이해 하는 데 도움을 줍니다. 각 문제 유형에 관련 된 문제 및 해결 방법은이 문서에 설명 되어 있습니다.

Azure SQL Database [Intelligent Insights](database/intelligent-insights-troubleshoot-performance.md#detectable-database-performance-patterns) 또는 SQL Server [dmv](database/monitoring-with-dmvs.md) 를 사용 하 여 이러한 유형의 성능 병목 상태를 검색할 수 있습니다.

![워크로드 상태](./media/identify-query-performance-issues/workload-states.png)

**실행 관련 문제**: 일반적으로 실행 관련 문제는 컴파일 문제와 관련 하 여 만족 스 럽 지 못한 쿼리 계획이 나 부족 하거나 남용 된 리소스와 관련 된 실행 문제를 야기 합니다.
**대기 관련 문제**: 대기 관련 문제는 일반적으로 다음과 같이 관련 됩니다.

- 잠금(차단)
- I/O
- TempDB 사용량과 관련 된 경합
- 메모리 부여 대기

## <a name="compilation-problems-resulting-in-a-suboptimal-query-plan"></a>컴파일 문제로 인해 만족 스 럽 지 못한 쿼리 계획

SQL 쿼리 최적화 프로그램에서 생성 된 최적이 아닌 계획은 쿼리 성능의 저하 때문일 수 있습니다. SQL 쿼리 최적화 프로그램은 누락 된 인덱스, 부실 통계, 처리할 행 수의 잘못 된 예상치 또는 필요한 메모리의 부정확 한 예상치로 인해 최적이 아닌 계획을 생성할 수 있습니다. 이전 또는 다른 인스턴스에서 쿼리가 더 빠르게 실행 된 경우 실제 실행 계획을 비교 하 여 서로 다른 지 확인 합니다.

- 다음 방법 중 하나를 사용 하 여 누락 된 인덱스를 식별 합니다.

  - [Intelligent Insights](database/intelligent-insights-troubleshoot-performance.md#missing-index)를 사용 합니다.
  - 단일 및 풀링된 데이터베이스에 대 한 [Database Advisor](database/database-advisor-implement-performance-recommendations.md) 입니다.
  - Dmv. 이 예에서는 누락 된 인덱스의 영향, Dmv를 사용 하 여 [누락 된 인덱스](database/performance-guidance.md#identifying-and-adding-missing-indexes) 를 검색 하는 방법 및 누락 된 인덱스 권장 사항을 구현할 때의 영향을 보여 줍니다.
- 더 나은 계획을 얻으려면 [쿼리 힌트](https://docs.microsoft.com/sql/t-sql/queries/hints-transact-sql-query)를 적용 하거나, [통계를 업데이트](https://docs.microsoft.com/sql/t-sql/statements/update-statistics-transact-sql)하거나, [인덱스를 다시 작성](https://docs.microsoft.com/sql/relational-databases/indexes/reorganize-and-rebuild-indexes) 하세요. 자동 [계획 수정](../azure-sql/database/automatic-tuning-overview.md) 기능을 사용 하 여 이러한 문제를 자동으로 완화 Azure SQL Database.

  이 [예에서는](database/performance-guidance.md#query-tuning-and-hinting) 매개 변수가 있는 쿼리로 인해 만족 스 럽 지 못한 쿼리 계획의 영향,이 조건을 검색 하는 방법 및 쿼리 힌트를 사용 하 여 해결 하는 방법을 보여 줍니다.

- 데이터베이스 호환성 수준을 변경 하 고 지능형 쿼리 처리를 구현 해 보세요. SQL 쿼리 최적화 프로그램은 데이터베이스의 호환성 수준에 따라 다른 쿼리 계획을 생성할 수 있습니다. 더 높은 호환성 수준은 보다 [지능적인 쿼리 처리 기능](https://docs.microsoft.com/sql/relational-databases/performance/intelligent-query-processing)을 제공 합니다.

  - 쿼리 처리에 대 한 자세한 내용은 [쿼리 처리 아키텍처 가이드](https://docs.microsoft.com/sql/relational-databases/query-processing-architecture-guide)를 참조 하세요.
  - 데이터베이스 호환성 수준을 변경 하 고 호환성 수준 간의 차이점에 대 한 자세한 내용은 [ALTER database](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-compatibility-level)를 참조 하십시오.
  - 카디널리티 예측에 대 한 자세한 내용은 [카디널리티 예측](https://docs.microsoft.com/sql/relational-databases/performance/cardinality-estimation-sql-server) 을 참조 하세요.

## <a name="resolving-queries-with-suboptimal-query-execution-plans"></a>쿼리 실행 계획이 최적이 아닌 쿼리 확인

다음 섹션에서는 최적이 아닌 쿼리 실행 계획을 사용 하 여 쿼리를 해결 하는 방법을 설명 합니다.

### <a name="queries-that-have-parameter-sensitive-plan-psp-problems"></a><a name="ParamSniffing"></a>PSP (매개 변수 구분 계획) 문제가 있는 쿼리

PSP (매개 변수 구분 계획) 문제는 쿼리 최적화 프로그램에서 특정 매개 변수 값 (또는 값 집합)에 대해서만 최적으로 사용 되는 쿼리 실행 계획을 생성 하 고 연속 실행에 사용 되는 매개 변수 값에 대해 캐시 된 계획이 최적이 아닌 경우에 발생 합니다. 최적이 아닌 계획은 쿼리 성능 문제를 발생 시키고 전체 작업 처리량을 저하 시킬 수 있습니다.

매개 변수 스니핑 및 쿼리 처리에 대 한 자세한 내용은 [쿼리-처리 아키텍처 가이드](/sql/relational-databases/query-processing-architecture-guide#ParamSniffing)를 참조 하세요.

여러 가지 해결 방법으로 PSP 문제를 완화할 수 있습니다. 각 해결 방법에는 장단점 및 단점이 있습니다.

- 각 쿼리 실행 시 [RECOMPILE](https://docs.microsoft.com/sql/t-sql/queries/hints-transact-sql-query) 쿼리 힌트를 사용합니다. 이 해결 방법을 사용할 경우 품질은 향상되지만 컴파일 시간 및 CPU가 늘어납니다. `RECOMPILE`높은 처리량이 필요한 작업의 경우에는이 옵션을 사용할 수 없는 경우가 많습니다.
- [옵션 (OPTIMIZE for ...)](https://docs.microsoft.com/sql/t-sql/queries/hints-transact-sql-query) 쿼리 힌트를 사용 하 여 실제 매개 변수 값을 대부분의 매개 변수 값에 적합 한 계획을 생성 하는 일반 매개 변수 값으로 재정의 합니다. 이 옵션을 사용하려면 최적의 매개 변수 값 및 관련 계획 특성을 적절히 이해해야 합니다.
- [옵션 (알 수 없는 경우 최적화)](https://docs.microsoft.com/sql/t-sql/queries/hints-transact-sql-query) 쿼리 힌트를 사용 하 여 실제 매개 변수 값을 재정의 하 고 대신 밀도 벡터 평균을 사용 합니다. 또한 로컬 변수에서 들어오는 매개 변수 값을 캡처한 다음 매개 변수 자체를 사용 하는 대신 조건자 내에서 지역 변수를 사용 하 여이 작업을 수행할 수 있습니다. 이 수정의 경우 평균 밀도가 *충분*해야 합니다.
- [DISABLE_PARAMETER_SNIFFING](https://docs.microsoft.com/sql/t-sql/queries/hints-transact-sql-query) 쿼리 힌트를 사용 하 여 매개 변수 검사를 완전히 사용 하지 않도록 설정 합니다.
- [Keepfixedplan](https://docs.microsoft.com/sql/t-sql/queries/hints-transact-sql-query) 쿼리 힌트를 사용 하 여 캐시에서 다시 컴파일이 발생 하지 않도록 합니다. 이 해결 방법에서는 적절 한 일반 계획이 캐시에 이미 있는 것으로 가정 합니다. 자동 통계 업데이트를 사용 하지 않도록 설정 하 여 적합 한 계획이 제거 될 가능성을 줄이고 새로운 잘못 된 계획을 컴파일할 수도 있습니다.
- 쿼리를 다시 작성 하 고 쿼리 텍스트에 힌트를 추가 하 여 [USE plan](https://docs.microsoft.com/sql/t-sql/queries/hints-transact-sql-query) 쿼리 힌트를 명시적으로 사용 하 여 계획을 강제로 적용 합니다. 또는 쿼리 저장소를 사용 하거나 [자동 튜닝](../azure-sql/database/automatic-tuning-overview.md)을 사용 하 여 특정 계획을 설정 합니다.
- 단일 프로시저를 각각을 조건부 논리 및 연결된 매개 변수 값을 기준으로 사용할 수 있는 중첩된 프로시저 세트로 바꿉니다.
- 정적 프로시저 정의를 대신하는 동적 문자열 실행을 만듭니다.

PSP 문제를 해결 하는 방법에 대 한 자세한 내용은 다음 블로그 게시물을 참조 하세요.

- [매개 변수](https://docs.microsoft.com/archive/blogs/queryoptteam/i-smell-a-parameter)
- [Conor vs. 동적 SQL과 프로시저 비교 및 매개 변수가 있는 쿼리에 대 한 계획 품질](https://blogs.msdn.microsoft.com/conor_cunningham_msft/2009/06/03/conor-vs-dynamic-sql-vs-procedures-vs-plan-quality-for-parameterized-queries/)
- [SQL Server의 SQL 쿼리 최적화 기술: 매개 변수 스니핑](https://www.sqlshack.com/query-optimization-techniques-in-sql-server-parameter-sniffing/)

### <a name="compile-activity-caused-by-improper-parameterization"></a>잘못 된 매개 변수화로 인 한 컴파일 작업

쿼리에 리터럴이 있으면 데이터베이스 엔진이 자동으로 문을 매개 변수화 하거나 사용자가 문을 명시적으로 매개 변수화 하 여 컴파일 수를 줄입니다. 동일한 패턴을 사용 하지만 다른 리터럴 값을 사용 하는 쿼리에 대 한 컴파일 수가 높으면 높은 CPU 사용량이 발생할 수 있습니다. 마찬가지로, 계속 해 서 리터럴을 포함 하는 쿼리를 일부만 매개 변수화 하는 경우 데이터베이스 엔진은 쿼리를 추가 하지 않습니다.

다음은 부분적으로 매개 변수가 있는 쿼리의 예입니다.

```sql
SELECT *
FROM t1 JOIN t2 ON t1.c1 = t2.c1
WHERE t1.c1 = @p1 AND t2.c2 = '961C3970-0E54-4E8E-82B6-5545BE897F8F'
```

이 예제에서는를 `t1.c1` 사용 `@p1` 하지만는 `t2.c2` 계속 리터럴로 GUID를 사용 합니다. 이 경우의 값을 변경 하면 `c2` 쿼리가 다른 쿼리로 처리 되 고 새 컴파일이 수행 됩니다. 이 예제에서 컴파일을 줄이려면 GUID도 매개 변수화 합니다.

다음 쿼리는 쿼리 해시가 쿼리를 올바르게 매개 변수화 했는지 여부를 확인 하는 쿼리 수를 보여 줍니다.

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

쿼리 실행 계획을 다시 컴파일하면 원래 캐시 된 계획과 다른 쿼리 계획이 생성 될 수 있습니다. 다음과 같은 다양 한 이유로 기존 원본 계획이 자동으로 다시 컴파일될 수 있습니다.

- 스키마의 변경 내용을 쿼리에서 참조 합니다.
- 테이블에 대 한 데이터 변경 내용은 쿼리에서 참조 됩니다.
- 쿼리 컨텍스트 옵션이 변경 되었습니다.

다음과 같은 다양 한 이유로 인해 컴파일된 계획이 캐시에서 분리 될 수 있습니다.

- 인스턴스 다시 시작
- 데이터베이스 범위 구성 변경
- 메모리 압력
- 캐시 지우기에 대 한 명시적 요청

RECOMPILE 힌트를 사용 하는 경우에는 계획이 캐시 되지 않습니다.

다시 컴파일 (또는 캐시 제거 후 새로 컴파일된)은 여전히 원래와 동일한 쿼리 실행 계획을 생성할 수 있습니다. 계획이 이전 또는 원래 계획에서 변경 되 면 다음과 같은 설명이 발생할 수 있습니다.

- **변경 된 물리적 디자인**: 예를 들어 새로 만든 인덱스는 쿼리 요구 사항을 보다 효과적으로 다룹니다. 새 인덱스는 쿼리 최적화 프로그램에서 원래 쿼리 실행의 첫 번째 버전에 대해 선택 된 데이터 구조를 사용 하는 것 보다 새 인덱스를 사용 하는 것이 더 최적 이라고 판단 하는 경우 새 컴파일에서 사용 될 수 있습니다. 참조 된 개체를 물리적으로 변경 하면 컴파일 시간에 새 계획을 선택할 수 있습니다.

- **서버 리소스 차이**: 한 시스템의 계획이 다른 시스템의 계획과 다를 경우 사용 가능한 프로세서 수와 같은 리소스 가용성이 생성 되는 계획에 영향을 줄 수 있습니다. 예를 들어 한 시스템에 더 많은 프로세서가 있는 경우 병렬 계획을 선택할 수 있습니다.

- **다른 통계**: 참조 된 개체와 연결 된 통계가 변경 되었거나 원래 시스템의 통계와 별개인 다를 수 있습니다. 통계 변경 및 다시 컴파일이 발생 하면 쿼리 최적화 프로그램은 변경 될 때부터 시작 하 여 통계를 사용 합니다. 수정 된 통계의 데이터 배포 및 빈도는 원래 컴파일과는 다를 수 있습니다. 이러한 변경 내용은 카디널리티 예상치를 만드는 데 사용 됩니다. *카디널리티 예상치* 는 논리적 쿼리 트리를 통해 전달 될 것으로 예상 되는 행 수입니다. 카디널리티 예상치를 변경 하면 다른 물리 연산자 및 관련 작업 순서를 선택할 수 있습니다. 통계를 약간만 변경 해도 변경 된 쿼리 실행 계획이 발생할 수 있습니다.

- **변경 된 데이터베이스 호환성 수준 또는 카디널리티 평가기 버전**: 데이터베이스 호환성 수준에 대 한 변경 내용으로 인해 다른 쿼리 실행 계획을 만들 수 있는 새로운 전략과 기능이 가능 합니다. 데이터베이스 호환성 수준 외에도 사용 하지 않거나 사용 하도록 설정 된 추적 플래그 4199 또는 데이터베이스 범위 구성 QUERY_OPTIMIZER_HOTFIXES 변경 된 상태는 컴파일 시간에 쿼리 실행 계획 선택에 영향을 줄 수 있습니다. 추적 플래그 9481 (레거시 CE 강제 적용) 및 2312 (강제 기본 CE)도 계획에 영향을 줍니다.

## <a name="resource-limits-issues"></a>리소스 제한 문제

만족 스 럽 지 못한 쿼리 계획 및 누락 된 인덱스와 관련 되지 않은 쿼리 성능 저하는 일반적으로 부족 하거나 남용 리소스와 관련이 있습니다. 쿼리 계획이 최적 이면 쿼리 (및 데이터베이스)가 데이터베이스, 탄력적 풀 또는 관리 되는 인스턴스의 리소스 제한에 도달 하는 것일 수 있습니다. 예를 들면 서비스 수준에 대 한 과도 한 로그 쓰기 처리량이 있을 수 있습니다.

- Azure Portal를 사용 하 여 리소스 문제 검색: 리소스 제한에 문제가 있는지 확인 하려면 [SQL Database 리소스 모니터링](database/monitor-tune-overview.md#azure-sql-database-and-azure-sql-managed-instance-resource-monitoring)을 참조 하세요. 단일 데이터베이스 및 탄력적 풀은 [Database Advisor 성능 권장 사항](database/database-advisor-implement-performance-recommendations.md) 및 [쿼리 성능 정보](database/query-performance-insight-use.md)를 참조 하세요.
- [Intelligent Insights](database/intelligent-insights-troubleshoot-performance.md#reaching-resource-limits) 를 사용 하 여 리소스 제한 검색
- [Dmv](database/monitoring-with-dmvs.md)를 사용 하 여 리소스 문제 검색:

  - [Dm_db_resource_stats](database/monitoring-with-dmvs.md#monitor-resource-use) DMV는 데이터베이스에 대 한 CPU, i/o 및 메모리 소비량을 반환 합니다. 데이터베이스에 활동이 없는 경우에도 15 초 간격 마다 하나의 행이 존재 합니다. 기록 데이터는 한 시간 동안 유지됩니다.
  - [Resource_stats](database/monitoring-with-dmvs.md#monitor-resource-use) DMV는 Azure SQL Database에 대 한 CPU 사용량 및 저장소 데이터를 반환 합니다. 데이터는 5 분 간격으로 수집 되 고 집계 됩니다.
  - [점증적으로 CPU 사용량이 높은 여러 개별 쿼리](database/monitoring-with-dmvs.md#many-individual-queries-that-cumulatively-consume-high-cpu)

리소스 부족으로 인 한 문제를 식별 하는 경우 리소스를 업그레이드 하 여 CPU 요구 사항을 충족 하기 위해 데이터베이스의 용량을 늘릴 수 있습니다. 자세한 내용은 [Azure SQL Database에서 단일 데이터베이스 리소스 크기 조정](database/single-database-scale.md) 및 [Azure SQL Database에서 탄력적 풀 리소스 크기](database/elastic-pool-scale.md)조정을 참조 하세요. 관리 되는 인스턴스의 크기를 조정 하는 방법에 대 한 자세한 내용은 [서비스 계층 리소스 제한](managed-instance/resource-limits.md#service-tier-characteristics) 을 참조 하세요.

## <a name="performance-problems-caused-by-increased-workload-volume"></a>작업 볼륨이 증가 하 여 발생 하는 성능 문제

응용 프로그램 트래픽과 작업 볼륨이 증가 하면 CPU 사용량이 증가할 수 있습니다. 그러나이 문제를 적절 하 게 진단 하려면 주의 해야 합니다. 높은 CPU 문제가 표시 되 면 다음 질문에 대답 하 여 작업 볼륨의 변경으로 인해 증가가 발생 하는지 여부를 확인 합니다.

- 응용 프로그램의 쿼리에서 CPU의 높은 문제가 발생 한 이유는 무엇 인가요?
- [식별할 수 있는 상위 CPU 소비 쿼리](database/monitoring-with-dmvs.md#the-cpu-issue-occurred-in-the-past):

  - 동일한 쿼리와 연결 된 여러 실행 계획이 있나요? 그렇다면 이유가 무엇입니까?
  - 실행 계획이 동일한 쿼리의 경우 실행 시간이 일치 했습니까? 실행 횟수가 증가 했습니까? 그렇다면 워크 로드 증가로 인해 성능 문제가 발생할 수 있습니다.

요약 하자면, 쿼리 실행 계획이 다르게 실행 되지 않았지만 CPU 사용량이 실행 수와 함께 증가 하는 경우 성능 문제는 워크 로드 증가와 관련이 있을 수 있습니다.

CPU 문제를 구동 하는 작업 볼륨 변경 사항을 항상 식별 하는 것은 쉽지 않습니다. 다음 항목을 고려합니다.

- **변경 된 리소스 사용량**: 예를 들어 오랜 시간 동안 CPU 사용량이 80%로 증가 하는 시나리오를 가정해 보겠습니다. CPU 사용 만으로는 작업 볼륨이 변경 된 것을 의미 하지 않습니다. 쿼리 실행 계획의 재발 및 데이터 배포의 변경 사항은 응용 프로그램이 동일한 작업을 실행 하는 경우에도 더 많은 리소스 사용에 기여할 수 있습니다.

- **새 쿼리의 모양**: 응용 프로그램에서 다른 시간에 새 쿼리 집합을 구동 하는 경우가 있습니다.

- **요청 수 증가 또는 감소**:이 시나리오는 작업의 가장 확실 한 측정값입니다. 쿼리 수가 많다고 해서 항상 리소스 사용률이 많은 것은 아닙니다. 그러나이 메트릭은 다른 요소가 변경 되지 않는다고 가정 하 고 여전히 중요 한 신호입니다.

Intelligent Insights를 사용 하 여 [작업 부하를 늘리고](database/intelligent-insights-troubleshoot-performance.md#workload-increase) [계획을 계획](database/intelligent-insights-troubleshoot-performance.md#plan-regression)합니다.

## <a name="waiting-related-problems"></a>대기 중인 관련 문제

최적화 되지 않은 계획과 실행 문제와 관련 된 *대기 관련* 문제를 제거 하면 일반적으로 쿼리는 일부 리소스를 대기 하는 것일 수 있습니다. 대기 관련 문제는 다음과 같은 경우에 발생할 수 있습니다.

- **차단**:

  한 쿼리는 데이터베이스의 개체에 대 한 잠금을 유지할 수 있지만 다른 쿼리는 동일한 개체에 액세스 하려고 합니다. [Dmv](database/monitoring-with-dmvs.md#monitoring-blocked-queries) 또는 [Intelligent Insights](database/intelligent-insights-troubleshoot-performance.md#locking)를 사용 하 여 차단 쿼리를 식별할 수 있습니다.
- **IO 문제**

  쿼리가 데이터 또는 로그 파일에 페이지가 기록 될 때까지 대기 하 고 있을 수 있습니다. 이 경우 `INSTANCE_LOG_RATE_GOVERNOR` `WRITE_LOG` DMV에서, 또는 wait 통계를 확인 합니다 `PAGEIOLATCH_*` . Dmv를 사용 하 여 [IO 성능 문제 파악](database/monitoring-with-dmvs.md#identify-io-performance-issues)을 참조 하세요.
- **TempDB 문제**

  작업에서 임시 테이블을 사용 하거나 계획에 TempDB를 사용 하는 경우 쿼리에 TempDB 처리량 문제가 있을 수 있습니다. Dmv를 사용 하 여 [TempDB 문제 식별](database/monitoring-with-dmvs.md#identify-tempdb-performance-issues)을 참조 하세요.
- **메모리 관련 문제**

  워크 로드에 충분 한 메모리가 없는 경우 페이지 수명 예상이 삭제 되거나 쿼리에 필요한 것 보다 더 많은 메모리가 확보 될 수 있습니다. 경우에 따라 쿼리 최적화 프로그램에서 기본 제공 되는 인텔리전스를 통해 메모리 관련 문제를 해결할 수 있습니다. Dmv를 사용 하 여 [메모리 부여 문제 파악](database/monitoring-with-dmvs.md#identify-memory-grant-wait-performance-issues)을 참조 하세요.

### <a name="methods-to-show-top-wait-categories"></a>상위 대기 범주를 표시 하는 메서드

이러한 메서드는 일반적으로 대기 유형의 최상위 범주를 표시 하는 데 사용 됩니다.

- [대기 시간이 증가](database/intelligent-insights-troubleshoot-performance.md#increased-wait-statistic) 하 여 성능이 저하 된 쿼리를 식별 하는 Intelligent Insights 사용
- [쿼리 저장소](https://docs.microsoft.com/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store) 를 사용 하 여 시간에 따른 각 쿼리에 대 한 대기 통계를 찾을 수 있습니다. 쿼리 저장소에서 대기 유형은 대기 범주로 결합됩니다. 대기 범주는 query_store_wait_stats의 대기 형식에 대 한 매핑을 찾을 수 있습니다 [.](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-query-store-wait-stats-transact-sql#wait-categories-mapping-table)
- [Dm_db_wait_stats](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-db-wait-stats-azure-sql-database) 를 사용 하 여 쿼리 작업을 수행 하는 동안 실행 된 스레드에서 발생 한 모든 대기에 대 한 정보를 반환 합니다. 이 집계 뷰를 사용 하 여 Azure SQL Database 및 특정 쿼리 및 일괄 처리와 관련 된 성능 문제를 진단할 수 있습니다. 쿼리는 리소스, 큐 대기 또는 외부 대기를 기다릴 수 있습니다.
- [Dm_os_waiting_tasks](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-os-waiting-tasks-transact-sql) 를 사용 하 여 일부 리소스에서 대기 중인 작업의 큐에 대 한 정보를 반환 합니다.

CPU를 많이 사용 하는 시나리오에서는 다음과 같은 경우 쿼리 저장소 및 대기 통계가 CPU 사용량을 반영 하지 않을 수 있습니다.

- CPU 사용량이 많은 쿼리가 아직 실행 되 고 있습니다.
- 장애 조치 (failover)가 발생 하면 CPU 사용량이 많은 쿼리가 실행 중입니다.

쿼리 저장소 및 대기 통계를 추적 하는 Dmv는 성공적으로 완료 되 고 시간이 초과 된 쿼리만의 결과만 표시 합니다. 문이 완료 될 때까지 현재 실행 중인 문의 데이터를 표시 하지 않습니다. 동적 관리 뷰를 사용 하 여 현재 실행 중인 쿼리와 관련 작업자 시간을 추적 하는 [dm_exec_requests](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql) .

> [!TIP]
> 추가 도구:
>
> - [TigerToolbox 대기 및 래치](https://github.com/Microsoft/tigertoolbox/tree/master/Waits-and-Latches)
> - [TigerToolbox usp_whatsup](https://github.com/Microsoft/tigertoolbox/tree/master/usp_WhatsUp)

## <a name="next-steps"></a>다음 단계

[SQL Database 모니터링 및 튜닝 개요](database/monitor-tune-overview.md)
