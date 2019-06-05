---
title: 모니터링 및 성능 튜닝 - Azure SQL Database | Microsoft Docs
description: 평가 및 개선을 통한 Azure SQL Database의 성능 튜닝 관련 팁.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: jrasnik, carlrab
manager: craigg
ms.date: 01/25/2019
ms.openlocfilehash: 2fa43fcd48736a3d044deb07ed690af580c3b987
ms.sourcegitcommit: c05618a257787af6f9a2751c549c9a3634832c90
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/30/2019
ms.locfileid: "66416285"
---
# <a name="monitoring-and-performance-tuning"></a>모니터링 및 성능 튜닝

쉽게 사용을 모니터링할 수 있습니다 리소스 추가 또는 제거 (CPU, 메모리, I/O)를 azure SQL Database 사용 잠재적인 문제를 해결 하 고 데이터베이스의 성능을 향상 시킬 권장 사항을 찾습니다. Azure SQL Database에는 데이터베이스 워크 로드에 맞게 조정 하 고 성능을 자동으로 최적화할 수 있도록 하려는 경우 데이터베이스에서 문제를 해결 자동으로 수 있는 많은 기능이 있습니다. 그러나 문제를 해결 해야 할 수 있는 몇 가지 사용자 지정 문제가 있습니다. 이 문서에서는 몇 가지 모범 사례 및 성능 문제를 해결 하는 데 사용할 수 있는 도구를 설명 합니다.

데이터베이스는 문제 없이 실행 중인지 확인 하기 위해 수행 해야 하는 두 가지 주요 작업이 있습니다.
- [데이터베이스 성능 모니터링](#monitoring-database-performance) 하려면 데이터베이스에 할당 된 리소스 워크 로드를 처리할 수 있는지 확인 합니다. 리소스 제한 도달 했는지을 표시 하는 경우에 상위 리소스 소비 쿼리를 식별 하 고, 최적화 또는 서비스 계층을 업그레이드 하 여 더 많은 리소스를 추가 해야 합니다.
- [성능 문제를 해결](#troubleshoot-performance-issues) 잠재적인 문제가 발생 한 이유를 식별 하기 위해 문제 및 문제를 해결 하는 작업의 근본 원인을 식별 합니다.

## <a name="monitoring-database-performance"></a>데이터베이스 성능 모니터링

Azure에서 SQL 데이터베이스의 성능 모니터링은 데이터베이스에 대해 선택한 데이터베이스 성능 수준을 기준으로 리소스 사용률을 모니터링하는 것으로 시작합니다. 다음 리소스를 모니터링 해야 합니다.
 - **CPU 사용량** -CPU 사용률이 100%를 긴 시간 동안에서 연결 하려는 확인 해야 합니다. 데이터베이스 또는 인스턴스 업그레이드 또는 식별 하 고 대부분의 계산 능력을 사용 하는 쿼리를 튜닝 해야 할 수 있습니다 있다는 의미일 수 있습니다.
 - **대기 작업 통계** -확인 해야 할 몇 가지 리소스에 대 한 대기 중인 쿼리 하는 이유입니다. Queriesmig 인출 하거나 등 몇 가지 리소스 제한에 도달 하기 때문에 대기 중인 데이터베이스 파일에 저장 된 데이터를 기다립니다.
 - **IO 사용량** -기본 저장소 IO 제한을 접근할 수를 확인 해야 합니다.
 - **메모리 사용량** -확인 해야 하 고 데이터베이스 또는 인스턴스의 Vcore 수에 비례 하는 사용 가능한 메모리 양이 충분 워크 로드에 대 한 합니다. 페이지 수명 예상은 페이지가 신속 하 게 메모리에서 제거를 나타낼 수 있는 매개 변수 중 하나입니다.

Azure SQL Database **advice 도움이 될 수 있는 문제를 해결 하 고 잠재적 성능 문제를 해결 제공**합니다. 개선 및 검토 하 여 리소스를 변경 하지 않고 쿼리 성능을 최적화할 기회를 쉽게 식별할 수 있습니다 [성능 튜닝 권장 사항](sql-database-advisor.md)합니다. 인덱스 누락 및 최적화되지 않은 쿼리는 데이터베이스 성능을 저하시키는 일반적인 원인입니다. 이러한 튜닝 권장 사항을 적용하여 워크로드의 성능을 개선할 수 있습니다. 또한 Azure SQL 데이터베이스를 통해 식별된 모든 권장 사항을 적용하고 이를 통해 데이터베이스 성능이 개선되는지 확인하여 [쿼리의 성능을 자동으로 최적화](sql-database-automatic-tuning.md)할 수 있습니다.

데이터베이스 성능 문제 해결 및 모니터링에 대한 다음과 같은 옵션이 있습니다.

- [Azure Portal](https://portal.azure.com)에서 **SQL Database**를 클릭하고 데이터베이스를 선택한 후 모니터링 차트를 사용하여 최대값에 근접한 리소스를 찾습니다. 기본적으로 DTU 사용량이 표시됩니다. **편집** 을 클릭하여 표시된 시간 범위 및 값을 변경합니다.
- SQL Server Management Studio와 같은 도구 등의 많은 유용한 보고서를 제공할를 [성능 대시보드](https://docs.microsoft.com/sql/relational-databases/performance/performance-dashboard?view=sql-server-2017) 리소스 사용률을 모니터링 하 고 상위 리소스 소비 쿼리를 식별할 수 있습니다 위치 또는 [쿼리 저장소가](https://docs.microsoft.com/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store#Regressed)재발 된 성능이 사용 하 여 쿼리를 식별할 수 있습니다.
- 사용 하 여 [Query Performance Insight](sql-database-query-performance.md) 는 [Azure portal](https://portal.azure.com) 가장 많은 리소스를 소비 하는 쿼리를 식별 하려면. 이 기능은 단일 데이터베이스 및 탄력적 풀에서 사용할 수만 있습니다.
- [SQL Database Advisor](sql-database-advisor-portal.md)를 사용하여 인덱스 만들기 및 삭제, 쿼리 매개 변수화, 스키마 문제 해결에 대한 권장 사항을 확인합니다. 이 기능은 단일 데이터베이스 및 탄력적 풀에서 사용할 수만 있습니다.
- 데이터베이스 성능 자동 모니터링에 [Azure SQL Intelligent Insights](sql-database-intelligent-insights.md)를 사용합니다. 성능 문제가 검색되면 문제의 세부 정보와 RCA(근본 원인 분석)가 포함된 진단 로그가 생성됩니다. 성능 개선 권장 향상이 제공됩니다(가능한 경우).
- [자동 튜닝을 사용](sql-database-automatic-tuning-enable.md)하고 Azure SQL 데이터베이스에서 식별된 성능 문제를 자동으로 수정하도록 합니다.
- [DMV(동적 관리 뷰)](sql-database-monitoring-with-dmvs.md), [확장 이벤트](sql-database-xevent-db-diff-from-svr.md) 및 [쿼리 저장소](https://docs.microsoft.com/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store)를 사용하여 성능 문제에 대한 구체적인 내용을 볼 수 있습니다.

> [!TIP]
> 위의 방법 중 하나 이상을 사용하여 성능 문제를 식별한 후 Azure SQL Database의 성능을 향상시키는 데 사용할 수 있는 기술을 찾으려면 [성능 지침](sql-database-performance-guidance.md)을 참조하세요.

## <a name="troubleshoot-performance-issues"></a>성능 문제 해결

성능 문제를 진단하고 해결하려면 먼저 각 워크로드 상태와 관련된 성능 문제를 일으키는 각 활성 쿼리의 상태 및 조건을 이해해야 합니다. Azure SQL Database 성능을 개선하려면 애플리케이션의 각 활성 쿼리 요청이 실행 중이거나 대기 상태인 것을 이해해야 합니다. Azure SQL Database의 성능 문제를 해결하려면 이 문서를 읽을 때 다음 차트를 염두에 두고 성능 문제를 진단하고 해결하세요.

![워크로드 상태](./media/sql-database-monitor-tune-overview/workload-states.png)

성능 문제가 있는 워크로드의 경우 문제 원인이 CPU 경합일 수도 있고(**실행 관련** 조건), 또는 개별 쿼리가 무언가를 기다리기 때문일 수도 있습니다(**대기 관련** 조건).

원인 또는 **실행 관련** 문제가 될 수 있습니다.
- **컴파일 문제가** -SQL 쿼리 최적화 프로그램은 오래 된 통계, 처리할 행 수가 잘못 된 예측 또는 예상 되는 데 필요한 메모리 때문에 최적 계획을 생성할 수 있습니다. 다른 인스턴스 (관리 되는 인스턴스 또는 SQL Server 인스턴스) 또는 이전에는 쿼리가 더 빠르게 실행 된 경우 실제 실행 계획 하 고 비교 하는 다양 한 합니다. 쿼리 힌트 또는 다시 작성 통계 더 나은 계획을 가져올 인덱스를 적용 하려고 합니다. 자동 계획 수정 자동으로 이러한 문제를 완화 하기 위해 Azure SQL Database에서 사용 하도록 설정 합니다.
- **실행 문제** -최적의 쿼리 계획은 다음 로그 쓰기 처리량 등 데이터베이스의 일부 리소스 제한에 도달할 것이 또는 조각 모음 된 인덱스 다시 작성 해야 하는 사용할 수 있습니다. 리소스를 소비 하는 동시 쿼리 수가 많은 실행 문제의 원인을 수도 있습니다. **대기 중인 관련** 효율적으로 실행 중인 쿼리는 일부 리소스에 대 한 대기 것 이므로 실행 문제와 관련 된 대부분의 문제는 합니다.

원인 또는 **대기 중인 관련** 문제가 될 수 있습니다.
- **차단** -다른 사용자가 동일한 개체에 액세스 하려고 하는 동안 하나의 쿼리가 데이터베이스의 일부 개체에 잠금을 보유할 수 있습니다. DMV를 사용 하 여 또는 모니터링 도구 차단 쿼리를 쉽게 식별할 수 있습니다.
- **IO 문제** -쿼리 데이터 또는 로그 파일을 쓸 수 있도록 페이지를 기다리고 있을 수 있습니다. 이 경우에 표시 됩니다 `INSTANCE_LOG_RATE_GOVERNOR`, `WRITE_LOG`, 또는 `PAGEIOLATCH_*` DMV의 통계를 기다립니다.
- **TempDB 문제** -는 많은 수의 임시 테이블을 사용 하는 경우 TempDB 처리량은 문제가 있을 쿼리 계획에서 많은 TempDB 유출 하는 것이 표시 됩니다. 
- **메모리 관련 문제** -없을 수 있습니다 메모리가 부족 하 여 워크 로드에 대 한 페이지 수명 예상 사용자를 삭제 하거나 쿼리는 필요한 것 보다 적은 메모리 부여를 시작 합니다. 일부 경우에 쿼리 최적화 프로그램의 기본 제공 인텔리전스는 이러한 문제 해결 됩니다.
 
다음 섹션에서 설명 됩니다 식별 하 고 이러한 문제 중 일부를 해결 하는 방법.

## <a name="running-related-performance-issues"></a>실행 관련 성능 문제

일반적인 지침으로, CPU 사용률이 지속적으로 80% 이상인 경우 실행 관련 성능 문제가 있는 것입니다. 실행 관련 문제가 있는 경우 문제의 원인이 CPU 리소스 부족일 수도 있고, 다음 조건 중 하나일 수도 있습니다.

- 실행 중인 쿼리가 너무 많음
- 컴파일 쿼리가 너무 많음
- 하나 이상의 실행 중인 쿼리가 최적이 아닌 쿼리 계획을 사용

실행 관련 성능 문제가 있는 것으로 확인되면 한 가지 이상의 방법을 사용하여 정확한 문제를 식별해야 합니다. 실행 관련 문제를 식별하는 가장 일반적인 방법은 다음과 같습니다.

- [Azure Portal](sql-database-manage-after-migration.md#monitor-databases-using-the-azure-portal)을 사용하여 CPU 사용률을 모니터링합니다.
- 다음 [동적 관리 뷰](sql-database-monitoring-with-dmvs.md)를 사용합니다.

  - [sys.dm_db_resource_stats](sql-database-monitoring-with-dmvs.md#monitor-resource-use)는 Azure SQL Database 데이터베이스에 대한 CPU, I/O 및 메모리 사용량을 반환합니다. 데이터베이스에 작업이 없는 경우에도 한 행은 15초 간격으로 존재합니다. 기록 데이터는 한 시간 동안 유지됩니다.
  - [sys.resource_stats](sql-database-monitoring-with-dmvs.md#monitor-resource-use)는 Azure SQL Database에 대한 CPU 사용량 및 스토리지 데이터를 반환합니다. 데이터는 5분 미만 간격으로 수집되고 집계됩니다.

> [!IMPORTANT]
> 이러한 DMV를 사용하여 CPU 사용률 문제를 해결하는 T-SQL 쿼리 집합은 [CPU 성능 문제 식별](sql-database-monitoring-with-dmvs.md#identify-cpu-performance-issues)을 참조하세요.

### <a name="ParamSniffing"></a> 쿼리 매개 변수가 중요 쿼리 실행 계획 문제가 문제 해결

PSP(매개 변수가 중요한 계획) 문제는 쿼리 최적화 프로그램이 특정 매개 변수 값(또는 값 세트)에 대해서만 최적 상태인 쿼리 실행 계획을 생성하고 캐시된 계획이 연속 실행에 사용되는 매개 변수 값에 대한 최적이 아닌 시나리오를 나타냅니다. 최적이 아닌 계획을 사용하면 쿼리 성능 문제가 발생하고 전반적인 워크로드 처리량이 저하될 수 있습니다. 매개 변수 스니핑 및 쿼리 처리에 대 한 자세한 내용은 참조는 [쿼리 처리 아키텍처 가이드](/sql/relational-databases/query-processing-architecture-guide#ParamSniffing)합니다.

문제를 완화하는 데 사용되는 몇 가지 해결 방법과 관련된 절충안 및 단점은 다음과 같습니다.

- 각 쿼리 실행 시 [RECOMPILE](https://docs.microsoft.com/sql/t-sql/queries/hints-transact-sql-query) 쿼리 힌트를 사용합니다. 이 해결 방법을 사용할 경우 품질은 향상되지만 컴파일 시간 및 CPU가 늘어납니다. 경우에 따라 높은 처리량이 필요한 워크로드에는 `RECOMPILE` 옵션을 사용하지 못할 수 있습니다.
- 실제 매개 변수 값을 대부분의 매개 변수 값에 충분히 적합한 계획을 생성하는 일반적인 매개 변수 값으로 재정의하려면 [OPTION (OPTIMIZE FOR…)](https://docs.microsoft.com/sql/t-sql/queries/hints-transact-sql-query) 쿼리 힌트를 사용합니다.   이 옵션을 사용하려면 최적의 매개 변수 값 및 관련 계획 특성을 적절히 이해해야 합니다.
- 밀도 벡터 평균을 사용하는 대신, 실제 매개 변수 값을 재정의하려면 [OPTION (OPTIMIZE FOR UNKNOWN)](https://docs.microsoft.com/sql/t-sql/queries/hints-transact-sql-query) 쿼리 힌트를 사용합니다. 이 작업을 수행하는 또 다른 방법은 수신 매개 변수 값을 로컬 변수에 캡처하고, 매개 변수 자체를 사용하는 대신 조건자 내에서 해당 로컬 변수를 사용하는 것입니다. 평균 밀도는 이러한 특정 해결 방법에 *충분히 적절해야* 합니다.
- [DISABLE_PARAMETER_SNIFFING](https://docs.microsoft.com/sql/t-sql/queries/hints-transact-sql-query) 쿼리 힌트를 사용하여 매개 변수 검색을 완전히 사용하지 않도록 설정합니다.
- 캐시에 있는 동안 다시 컴파일하지 않으려면 [KEEPFIXEDPLAN](https://docs.microsoft.com/sql/t-sql/queries/hints-transact-sql-query) 쿼리 힌트를 사용합니다. 이 해결 방법은 *충분히 좋은* 일반 계획이 캐시에 이미 있다고 가정합니다. 적절한 계획이 제거되고 새로운 나쁜 계획이 컴파일될 가능성을 줄이기 위해 통계 자동 업데이트도 사용하지 않도록 설정할 수 있습니다.
- [USE PLAN](https://docs.microsoft.com/sql/t-sql/queries/hints-transact-sql-query) 쿼리 힌트를 명시적으로 사용하여 계획을 강제로 적용합니다(명시적으로 지정하거나, 쿼리 저장소를 사용하여 특정 계획을 설정하거나, [자동 튜닝](sql-database-automatic-tuning.md)을 사용하도록 설정).
- 단일 프로시저를 각각을 조건부 논리 및 연결된 매개 변수 값을 기준으로 사용할 수 있는 중첩된 프로시저 세트로 바꿉니다.
- 정적 프로시저 정의를 대신하는 동적 문자열 실행을 만듭니다.

이러한 유형의 문제를 해결하는 방법에 대한 자세한 내용은 다음을 참조하세요.

- 이렇게 [매개 변수를 찾았습니다](https://blogs.msdn.microsoft.com/queryoptteam/2006/03/31/i-smell-a-parameter/) 블로그 게시물
- 이 [dynamic sql versus plan quality for parameterized queries](https://blogs.msdn.microsoft.com/conor_cunningham_msft/2009/06/03/conor-vs-dynamic-sql-vs-procedures-vs-plan-quality-for-parameterized-queries/)(동적 SQL 및 매개 변수가 있는 쿼리의 계획 품질) 블로그 게시물
- 이 [SQL Server에서 SQL 쿼리 최적화 기술: 매개 변수 스니핑](https://www.sqlshack.com/query-optimization-techniques-in-sql-server-parameter-sniffing/) 블로그 게시물

### <a name="troubleshooting-compile-activity-due-to-improper-parameterization"></a>부적절한 매개 변수화로 인한 컴파일 활동 문제 해결

쿼리에 리터럴이 있는 경우 데이터베이스 엔진이 자동으로 문을 매개 변수화하도록 선택하거나, 사용자가 컴파일 수를 줄이기 위해 문을 명시적으로 매개 변수화할 수 있습니다. 패턴이 같지만 다른 리터럴 값을 사용하는 쿼리를 여러 번 컴파일하면 CPU 사용률이 높아질 수 있습니다. 마찬가지로, 리터럴을 계속 포함하는 쿼리를 부분적으로만 매개 변수화해도 데이터베이스 엔진이 추가로 매개 변수화하지는 않습니다.  다음은 부분적으로 매개 변수가 있는 쿼리 예제입니다.

```sql
SELECT * FROM t1 JOIN t2 ON t1.c1 = t2.c1
WHERE t1.c1 = @p1 AND t2.c2 = '961C3970-0E54-4E8E-82B6-5545BE897F8F'
```

이전 예제에서 `t1.c1`은 `@p1`을 사용하지만 `t2.c2`는 계속해서 GUID를 리터럴로 사용합니다. 이러한 경우 `c2` 값을 변경하면 쿼리는 다른 쿼리로 처리되고 새 컴파일이 발생합니다. 이전 예제에서 컴파일을 줄이려면 GUID도 매개 변수화해야 합니다.

다음 쿼리는 쿼리가 적절히 매개 변수화되었는지를 확인하기 위한 쿼리 해시별 쿼리 수를 보여 줍니다.

```sql
SELECT  TOP 10  
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
### <a name="factors-influencing-query-plan-changes"></a>쿼리 계획 변경이 영향을 주는 요인

쿼리 실행 계획 다시 원래 캐시 된 항목에서 다른 생성 된 쿼리 계획을 될 수 있습니다. 다양 한 이유가 왜 기존 원래 계획 수 자동으로 다시 컴파일할 수 있습니다.
- 쿼리에서 참조 하 고 스키마의 변경 내용
- 쿼리에서 참조 하 고 테이블의 데이터 변경 내용 
- 쿼리 컨텍스트 옵션 변경 

다양 한 이유로, 인스턴스가 다시 시작을 포함 하 여 캐시에서 컴파일된 계획을 꺼낼 수 있습니다, 그리고 데이터베이스 범위 구성 변경, 메모리 부족 및 캐시를 지우려면 명시적 요청 합니다. 또한 RECOMPILE 힌트를 사용 하 여 계획 캐시 되지 않으며 의미 합니다.

다시 컴파일 (또는 캐시 제거 후 새 컴파일이) 원래 관찰 하나에서 동일한 쿼리 실행 계획을 생성 여전히 발생할 수 있습니다.  그러나 이전 또는 원래 계획에 비해 계획 변경 내용을 인 경우 쿼리 실행 계획을 변경 하는 이유는 가장 일반적인 설명은 다음과 같습니다.

- **물리적 디자인 변경**합니다. 예를 들어, 새 인덱스 생성 보다 효율적으로 처리 쿼리의 요구 사항을 결정 하는 쿼리 최적화 프로그램 경우 새로운 컴파일에 사용할 수 있습니다는 원래 선택한 첫 번째 버전에 대 한 데이터 구조를 사용 하는 보다는 새 인덱스를 활용 하 여 좀 더 최적의 쿼리 실행 합니다.  참조 된 개체를 실제로 변경 된 내용이 컴파일 타임에는 새 계획 선택 될 수 있습니다.

- **서버 리소스 차이점**합니다. 프로세스를 "시스템" 및 "시스템 B" – 리소스의 가용성 사용 가능한 프로세서 수 등에서 하나의 계획이 서로 다른 시나리오에서 영향을 줄 수 어떤 계획을 생성 합니다.  예를 들어, 하나의 시스템에 프로세서 수가 높을수록 병렬 계획을 선택할 수 있습니다. 

- **다른 통계**합니다. 참조 된 개체와 관련 된 통계 변경 되었거나 원래 시스템의 통계에 실질적인 다릅니다.  통계를 변경 하 고 재컴파일을 발생 하는 경우 쿼리 최적화 프로그램 시간에서 해당 특정 시점을 기준으로 통계를 사용 합니다. 수정 된 통계는 크게 다른 데이터 배포와 원래 컴파일할 때 이런 경우가 아니라면 있는 주파수 있을 수 있습니다.  이러한 변경 내용은 카디널리티 예상치는 (논리적 쿼리 트리를 통과 하도록 예상 행 수)을 예측 하는 데 사용 됩니다.  카디널리티 예상치의 변경 내용을 다른 물리적 연산자 및 순서-작업의 연결된을 선택 하도록 발생할 수 있습니다.  변경 된 쿼리 실행 계획 통계 라도 변경 될 수 있습니다.

- **변경 된 데이터베이스 호환성 수준 또는 카디널리티 평가기 버전**합니다.  데이터베이스 호환성 수준 변경 하거나 새로운 전략 및 다른 쿼리 실행 계획에서 발생할 수 있는 기능을 설정할 수 있습니다.  데이터베이스 호환성 수준 이상의 사용 하지 않도록 설정 또는 4199 추적 플래그를 사용 하도록 설정 QUERY_OPTIMIZER_HOTFIXES 수도 영향을 미칩니다. 데이터베이스 범위 구성의 상태를 변경 컴파일 시간 실행 계획 옵션을 쿼리 합니다.  추적 플래그 9481 (force 레거시 CE) 및 2312 (기본 CE를 적용)에 영향을 주는 계획 됩니다. 

### <a name="resolve-problem-queries-or-provide-more-resources"></a>문제 쿼리 해결 또는 더 많은 리소스 제공

문제를 식별한 후에는 문제 쿼리를 튜닝하거나 컴퓨팅 크기 또는 서비스 계층을 업그레이드하여 CPU 요구 사항을 충족하도록 Azure SQL 데이터베이스의 용량을 늘릴 수 있습니다. 단일 데이터베이스의 리소스 크기 조정에 대한 자세한 내용은 [Azure SQL Database에서 단일 데이터베이스 리소스 크기 조정](sql-database-single-database-scale.md) 및 [Azure SQL Database에서 탄력적 풀 리소스 크기 조정](sql-database-elastic-pool-scale.md)을 참조하세요. 관리되는 인스턴스 크기 조정에 대한 자세한 내용은 [인스턴스 수준 리소스 제한](sql-database-managed-instance-resource-limits.md#instance-level-resource-limits)을 참조하세요.

### <a name="determine-if-running-issues-due-to-increase-workload-volume"></a>워크로드 볼륨 증가를 가져오는 문제 확인

애플리케이션 트래픽 및 워크로드 증가로 인해 CPU 사용률이 증가할 수 있지만 이 문제를 적절히 진단하려면 주의해야 합니다. CPU 사용량이 많은 시나리오에서는 다음 질문에 답변하여 실제로 CPU가 워크로드 볼륨 변경으로 인해 증가하는지 확인합니다.

1. 애플리케이션의 쿼리가 CPU 증가 문제의 원인이 되나요?
2. CPU를 상당히 많이 사용하는 쿼리의 경우(식별 가능한 경우):

   - 동일한 쿼리와 연결된 실행 계획이 많이 있는지 확인합니다. 이러한 경우라면 이유를 확인합니다.
   - 동일한 실행 계획이 있는 쿼리의 경우, 실행 시간이 일관되는지와 실행 수가 증가하는지 확인합니다. 이러한 경우 워크로드 증가로 인해 성능 문제가 발생할 가능성이 높습니다.

즉, 쿼리 실행 계획은 다르게 실행되지만 CPU 사용률은 실행 수에 따라 증가할 경우 워크로드 증가와 관련된 성능 문제가 있을 수 있습니다.

워크로드 볼륨 변경이 CPU 문제를 유발한다는 결론을 항상 쉽게 내릴 수 있는 것은 아닙니다.   고려할 요인: 

- **리소스 사용량 변경**

  예를 들어, 장시간 동안 CPU가 80%까지 증가하는 시나리오를 고려해 보세요.  CPU 사용률만으로는 워크로드 볼륨이 달라지지 않습니다.  애플리케이션이 정확히 동일한 워크로드를 실행하더라도 쿼리 실행 계획 회귀 및 데이터 분포 변경 또한 리소스 사용량 증가에 영향을 줄 수 있습니다.

- **새 쿼리 표시**

   애플리케이션이 서로 다른 시간에 새 쿼리 세트를 구동할 수 있습니다.

- **요청 수가 증가 또는 감소**

   이 시나리오는 워크로드를 가장 명확히 나타내는 측정 방식입니다. 쿼리 수가 많다고 해서 항상 리소스 사용률이 많은 것은 아닙니다. 그렇지만 이 메트릭은 다른 요소가 변경되지 않을 경우 중대한 신호가 됩니다.

## <a name="waiting-related-performance-issues"></a>대기 관련 성능 문제

CPU 사용량이 높은 실행 관련 성능 문제가 아닌 것으로 확인되면 대기 관련 성능 문제가 발생한 것입니다. 즉, CPU가 다른 리소스를 기다리느라 CPU 리소스가 효율적으로 사용되지 못하고 있습니다. 이 경우 그 다음으로 할 일은 CPU 리소스가 무엇을 기다리는지 확인하는 것입니다. 다음은 상위 대기 유형 범주를 표시하는 가장 일반적인 방법입니다.

- [쿼리 저장소](https://docs.microsoft.com/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store)는 시간에 따른 쿼리당 대기 통계를 제공합니다. 쿼리 저장소에서 대기 유형은 대기 범주로 결합됩니다. 대기 범주와 대기 유형의 매핑은 [sys.query_store_wait_stats](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-query-store-wait-stats-transact-sql#wait-categories-mapping-table)에서 사용할 수 있습니다.
- [sys.dm_db_wait_stats](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-db-wait-stats-azure-sql-database)는 작업 중에 실행된 스레드로 인해 발생한 모든 대기에 대한 정보를 반환합니다. 이 집계 보기를 사용하여 Azure SQL Database와 특정 쿼리 및 일괄 처리의 성능 문제를 진단할 수도 있습니다.
- [sys.dm_os_waiting_tasks](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-os-waiting-tasks-transact-sql)는 일부 리소스에서 대기 중인 작업의 대기 큐에 대한 정보를 반환합니다.

높은 CPU 시나리오에서는 다음과 같은 두 가지 이유 때문에 쿼리 저장소 및 대기 통계가 항상 CPU 사용률을 반영하지 않을 수도 있습니다.

- CPU를 많이 소비하는 쿼리가 여전히 실행 중일 수 있으며 쿼리가 완료되지 않았습니다.
- 장애 조치(Failover)가 발생했을 때 CPU를 많이 소비하는 쿼리가 실행 중이었습니다.

쿼리 저장소 및 대기 통계 추적 동적 관리 뷰는 성공적으로 완료되고 제한 시간을 초과한 쿼리의 결과만 표시하고, 현재 실행 중인 문에 대한 데이터는 표시하지 않습니다(완료될 때까지). 동적 관리 뷰 [sys.dm_exec_requests](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql)를 사용하여 현재 실행 중인 쿼리와 관련된 작업자 시간을 추적할 수 있습니다.

이전 차트에 표시된 것처럼, 가장 일반적인 대기는 다음과 같습니다.

- 잠금(차단)
- 입력/출력
- `tempdb` 관련 콘텐츠
- 메모리 부여 대기

> [!IMPORTANT]
> 이러한 DMV를 사용하여 대기 관련 문제를 해결하는 T-SQL 쿼리 집합은 다음 항목을 참조하세요.
>
> - [I/O 성능 문제 식별](sql-database-monitoring-with-dmvs.md#identify-io-performance-issues)
> - [`tempdb` 성능 문제 식별](sql-database-monitoring-with-dmvs.md#identify-io-performance-issues)
> - [메모리 부여 대기 식별](sql-database-monitoring-with-dmvs.md#identify-memory-grant-wait-performance-issues)
> - [TigerToolbox-대기 및 래치](https://github.com/Microsoft/tigertoolbox/tree/master/Waits-and-Latches)
> - [TigerToolbox - usp_whatsup](https://github.com/Microsoft/tigertoolbox/tree/master/usp_WhatsUp)

## <a name="improving-database-performance-with-more-resources"></a>보다 많은 리소스와 함께 데이터베이스 성능 개선

마지막으로, 데이터베이스의 성능을 향상시킬 수 있는 실행 가능한 항목이 더 없으면 Azure SQL Database에서 사용할 수 있는 리소스의 양을 변경할 수 있습니다. 단일 데이터베이스의 [DTU 서비스 계층](sql-database-service-tiers-dtu.md)을 변경하여 더 많은 리소스를 할당하거나 탄력적 풀의 eDTU를 언제든지 늘릴 수 있습니다. 또는 [vCore 기반 구매 모델](sql-database-service-tiers-vcore.md)을 사용하는 경우, 서비스 계층을 변경하거나 데이터베이스에 할당된 리소스를 늘릴 수 있습니다.

1. 단일 데이터베이스의 경우 필요에 따라 [서비스 계층을 변경](sql-database-single-database-scale.md)하거나 [리소스를 계산](sql-database-single-database-scale.md)하여 데이터베이스 성능을 향상시킬 수 있습니다.
2. 여러 데이터베이스의 경우 [탄력적 풀](sql-database-elastic-pool-guidance.md)을 사용하여 자동으로 리소스 규모를 조정할 수 있습니다.

## <a name="tune-and-refactor-application-or-database-code"></a>애플리케이션 또는 데이터베이스 코드 조정 및 리팩터링

데이터베이스를 보다 최적으로 사용하도록 애플리케이션 코드를 변경하거나 인덱스를 변경하며 계획을 강제 적용하거나 힌트를 사용하여 사용자 워크로드에 맞게 데이터베이스를 수동으로 조정할 수 있습니다. 수동 튜닝 및 코드 다시 작성에 대한 몇 가지 지침 및 팁은 [성능 지침 항목](sql-database-performance-guidance.md) 문서에서 확인합니다.

## <a name="next-steps"></a>다음 단계

- Azure SQL Database에서 자동 조정을 사용하도록 설정하고 자동 조정 기능으로 작업을 완벽하게 관리하려면 [자동 조정 사용](sql-database-automatic-tuning-enable.md)을 참조하세요.
- 수동 튜닝을 사용하려면 [Azure Portal에서 튜닝 권장 사항](sql-database-advisor-portal.md)을 검토하고 쿼리의 성능을 개선하는 권장 사항을 직접 적용합니다.
- [Azure SQL Database 서비스 계층](sql-database-performance-guidance.md)을 변경하여 데이터베이스에 제공되는 리소스를 변경합니다.
