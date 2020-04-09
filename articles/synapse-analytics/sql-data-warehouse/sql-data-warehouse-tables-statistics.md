---
title: 통계 작성, 업데이트
description: Synapse SQL 풀의 테이블에 쿼리 최적화 통계를 만들고 업데이트하기 위한 권장 사항 및 예제입니다.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 05/09/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 6f2af87cf5cef1b5a80bc16d962fba579b4ff309
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/09/2020
ms.locfileid: "80985867"
---
# <a name="table-statistics-in-synapse-sql-pool"></a>시냅스 SQL 풀의 테이블 통계

이 문서에서는 SQL 풀의 테이블에 대한 쿼리 최적화 통계를 만들고 업데이트하기 위한 권장 사항 및 예제를 찾을 수 있습니다.

## <a name="why-use-statistics"></a>통계를 사용하는 이유

SQL 풀이 데이터에 대해 더 많이 알수록 데이터에 대한 쿼리를 더 빠르게 실행할 수 있습니다. SQL 풀에 데이터를 로드한 후 데이터에 대한 통계를 수집하는 것은 쿼리를 최적화하기 위해 수행할 수 있는 가장 중요한 방법 중 하나입니다.

SQL 풀 쿼리 최적화 프로그램은 비용 기반 최적화 프로그램입니다. 다양한 쿼리 계획의 비용을 비교한 다음 가장 낮은 비용으로 계획을 선택합니다. 대부분의 경우 가장 빠르게 실행할 계획을 선택합니다.

예를 들어 최적화 프로그램이 쿼리를 필터링하는 날짜를 예측하면 한 행이 하나의 계획을 반환합니다. 선택한 날짜가 1백만 개의 행을 반환할 것으로 예상되면 다른 계획이 반환됩니다.

## <a name="automatic-creation-of-statistic"></a>통계의 자동 생성

데이터베이스 AUTO_CREATE_STATISTICS 옵션이 켜지면 SQL 풀은 들어오는 사용자 쿼리를 분석하여 누락된 통계를 분석합니다.

통계가 없는 경우 쿼리 최적화 프로그램은 쿼리 조건자 또는 조인 조건의 개별 열에 대한 통계를 만들어 쿼리 계획에 대한 카디널리티 예측을 개선합니다.

> [!NOTE]
> 통계 자동 생성은 현재 기본적으로 설정됩니다.

다음 명령을 실행하여 SQL 풀이 AUTO_CREATE_STATISTICS 구성되었는지 확인할 수 있습니다.

```sql
SELECT name, is_auto_create_stats_on
FROM sys.databases
```

SQL 풀에 AUTO_CREATE_STATISTICS 구성되지 않은 경우 다음 명령을 실행하여 이 속성을 사용하도록 설정하는 것이 좋습니다.

```sql
ALTER DATABASE <yourdatawarehousename>
SET AUTO_CREATE_STATISTICS ON
```

이러한 문은 통계의 자동 생성을 트리거합니다:

- SELECT
- INSERT-SELECT
- CTAS
- UPDATE
- Delete
- 조인을 포함하거나 술어의 존재가 감지될 때 설명

> [!NOTE]
> 통계 자동 생성은 임시 또는 외부 테이블에 만들어집니다.

통계의 자동 생성은 동기적으로 수행되므로 열에 통계가 없는 경우 쿼리 성능이 약간 저하될 수 있습니다. 단일 열에 대한 통계를 만드는 시간은 테이블의 크기에 따라 다릅니다.

측정 가능한 성능 저하를 방지하려면 시스템을 프로파일링하기 전에 벤치마크 워크로드를 실행하여 통계가 먼저 생성되었는지 확인해야 합니다.

> [!NOTE]
> 통계 생성은 다른 사용자 컨텍스트에서 [sys.dm_pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) 기록됩니다.

자동 통계를 만들 때 _WA_Sys_<16진수의 8자리 열 ID>_<16진수의 8자리 테이블 ID> 양식을 사용합니다. [DBCC SHOW_STATISTICS](/sql/t-sql/database-console-commands/dbcc-show-statistics-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) 명령을 실행하여 이미 생성된 통계를 볼 수 있습니다.

```sql
DBCC SHOW_STATISTICS (<table_name>, <target>)
```

table_name 표시할 통계가 포함된 테이블의 이름입니다. 이 테이블은 외부 테이블이 될 수 없습니다. 대상은 통계 정보를 표시할 대상 인덱스, 통계 또는 열의 이름입니다.

## <a name="update-statistics"></a>통계 업데이트

모범 사례 중 하나는 새로운 날짜가 추가되는 날마다 날짜 열에 통계를 업데이트하는 것입니다. 새 행이 SQL 풀에 로드될 때마다 새 로드 날짜 또는 트랜잭션 날짜가 추가됩니다. 이러한 추가 는 데이터 분포를 변경 하 고 통계를 최신 상태로 만듭니다.

값 분포가 일반적으로 변경되지 않으므로 고객 테이블의 국가/지역 열에 대한 통계를 업데이트할 필요가 없습니다. 고객 간의 배포가 상수라고 가정하는 경우, 테이블 변형에 새 행을 추가하면 데이터 배포를 변경하지 않습니다.

그러나 SQL 풀에 국가/지역만 포함되고 새 국가/지역의 데이터를 가져와 여러 국가/지역의 데이터가 저장되는 경우 국가/지역 열에 대한 통계를 업데이트해야 합니다.

통계를 업데이트하는 권장 사항은 다음과 같습니다.

|||
|-|-|
| **통계 업데이트의 빈도**  | 일반: 매일 </br> 데이터 로드 또는 변환 후 |
| **샘플링** |  10억 개 미만의 행은 기본 샘플링(20%)을 사용합니다. </br> 10억 개 이상의 행을 사용하면 2%의 샘플링을 사용합니다. |

쿼리 문제를 해결할 때 가장 먼저 묻는 질문 중 하나는 **"통계가 최신 상태입니까?"** 입니다.

이 질문은 데이터의 나이에 의해 대답 할 수있는 것이 아닙니다. 기본 데이터에 중대한 변경이 없는 경우 최신 통계 개체가 오래되었을 수 있습니다.

> [!TIP]
> 행 수가 상당히 변경되었거나 열에 대한 값의 분포에 중대한 변경이 있는 경우 *통계를 업데이트해야 하는 시간*입니다.

마지막으로 통계가 업데이트된 이후 테이블 내의 데이터가 변경되었는지 확인하는 동적 관리 보기가 없습니다. 통계의 나이를 알면 그림의 일부를 제공할 수 있습니다.

다음 쿼리를 사용하여 각 테이블에서 통계가 마지막으로 업데이트된 시간을 확인할 수 있습니다.

> [!NOTE]
> 열에 대한 값 분포에 중대한 변화가 있는 경우 마지막으로 업데이트된 시간에 관계없이 통계를 업데이트해야 합니다.

```sql
SELECT
    sm.[name] AS [schema_name],
    tb.[name] AS [table_name],
    co.[name] AS [stats_column_name],
    st.[name] AS [stats_name],
    STATS_DATE(st.[object_id],st.[stats_id]) AS [stats_last_updated_date]
FROM
    sys.objects ob
    JOIN sys.stats st
        ON  ob.[object_id] = st.[object_id]
    JOIN sys.stats_columns sc
        ON  st.[stats_id] = sc.[stats_id]
        AND st.[object_id] = sc.[object_id]
    JOIN sys.columns co
        ON  sc.[column_id] = co.[column_id]
        AND sc.[object_id] = co.[object_id]
    JOIN sys.types  ty
        ON  co.[user_type_id] = ty.[user_type_id]
    JOIN sys.tables tb
        ON  co.[object_id] = tb.[object_id]
    JOIN sys.schemas sm
        ON  tb.[schema_id] = sm.[schema_id]
WHERE
    st.[user_created] = 1;
```

예를 들어 SQL **풀의 날짜 열에는** 일반적으로 자주 통계 업데이트가 필요합니다. 새 행이 SQL 풀에 로드될 때마다 새 로드 날짜 또는 트랜잭션 날짜가 추가됩니다. 이러한 추가 는 데이터 분포를 변경 하 고 통계를 최신 상태로 만듭니다.

반대로, 고객 테이블의 성별 열에 대한 통계는 업데이트할 필요가 없습니다. 고객 간의 배포가 상수라고 가정하는 경우, 테이블 변형에 새 행을 추가하면 데이터 배포를 변경하지 않습니다.

SQL 풀에 성별이 하나만 있고 새 요구 사항으로 인해 여러 성별이 발생하는 경우 성별 열에 대한 통계를 업데이트해야 합니다.

자세한 내용은 [통계](/sql/relational-databases/statistics/statistics?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)에 대한 일반 가이드를 참조하세요.

## <a name="implementing-statistics-management"></a>통계 관리 구현

데이터 로드 프로세스를 확장하여 동시 쿼리 간의 차단 또는 리소스 경합을 방지/최소화하기 위해 로드가 끝날 때 통계가 업데이트되도록 하는 것이 좋습니다.  

데이터 로드는 테이블이 값의 크기 및/또는 배포를 자주 변경하는 경우입니다. 데이터 로드는 일부 관리 프로세스를 구현하는 논리적 장소입니다.

통계를 업데이트하기 위한 다음 기본 원칙이 제공됩니다.

- 로드된 각 테이블에 하나 이상의 업데이트된 통계 개체가 있는지 확인합니다. 이렇게 하면 통계 업데이트의 일부로 테이블 크기(행 수 및 페이지 수) 정보가 업데이트됩니다.
- JOIN, GROUP BY, ORDER BY 및 DISTINCT 절에 참여하는 열에 집중합니다.
- 이러한 값은 통계 히스토그램에 포함되지 않으므로 트랜잭션 날짜와 같은 "오름차순 키" 열을 더 자주 업데이트하는 것이 좋습니다.
- 정적 배포 열은 자주 업데이트하지 않는 것이 좋습니다.
- 각 통계 개체는 순서대로 업데이트됩니다. `UPDATE STATISTICS <TABLE_NAME>`을 구현하는 것만이 항상 이상적인 것은 아닙니다(특히 통계 개체가 많이 있는 광범위한 테이블의 경우).

자세한 내용은 [카디널리티 예측](/sql/relational-databases/performance/cardinality-estimation-sql-server?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)을 참조합니다.

## <a name="examples-create-statistics"></a>예제: 통계 작성

이 예제는 통계를 만들기 위한 다양한 옵션을 사용하는 방법을 보여줍니다. 각 열에 대해 사용하는 옵션은 데이터의 특징 및 열이 쿼리에서 사용되는 방법에 따라 다릅니다.

### <a name="create-single-column-statistics-with-default-options"></a>기본 옵션으로 단일 열 통계 만들기

열에 통계를 만들려면 통계 개체의 이름과 열 이름을 제공합니다.

이 구문은 모든 기본 옵션을 사용합니다. 기본적으로 SQL 풀은 통계를 만들 때 테이블의 **20%를** 샘플링합니다.

```sql
CREATE STATISTICS [statistics_name] ON [schema_name].[table_name]([column_name]);
```

예를 들어:

```sql
CREATE STATISTICS col1_stats ON dbo.table1 (col1);
```

### <a name="create-single-column-statistics-by-examining-every-row"></a>모든 행을 검사하여 단일 열 통계 만들기

20%의 기본 샘플링 속도는 대부분의 상황에 충분합니다. 그러나 샘플링 비율을 조정할 수 있습니다.

전체 테이블을 샘플링하려면 이 구문을 사용합니다.

```sql
CREATE STATISTICS [statistics_name] ON [schema_name].[table_name]([column_name]) WITH FULLSCAN;
```

예를 들어:

```sql
CREATE STATISTICS col1_stats ON dbo.table1 (col1) WITH FULLSCAN;
```

### <a name="create-single-column-statistics-by-specifying-the-sample-size"></a>샘플 크기를 지정하여 단일 열 통계 만들기

또는 백분율로 샘플 크기를 지정할 수 있습니다.

```sql
CREATE STATISTICS col1_stats ON dbo.table1 (col1) WITH SAMPLE = 50 PERCENT;
```

### <a name="create-single-column-statistics-on-only-some-of-the-rows"></a>일부 행에 대해서만 단일 열 통계 만들기

테이블의 일부 행에 대한 통계를 만들 수도 있습니다. 이를 필터링된 통계라고 합니다.

예를 들어 크게 분할된 테이블의 특정 파티션을 쿼리할 때 필터링된 통계를 사용할 수 있습니다. 파티션 값만 통계를 만들어서 통계의 정확도가 향상되므로 쿼리 성능이 향상됩니다.

이 예에서는 값의 범위에서 통계를 만듭니다. 값은 파티션의 값 범위와 일치하도록 쉽게 정의할 수 있습니다.

```sql
CREATE STATISTICS stats_col1 ON table1(col1) WHERE col1 > '2000101' AND col1 < '20001231';
```

> [!NOTE]
> 분산된 쿼리 계획을 선택하는 경우 필터링된 통계 사용을 고려하는 쿼리 최적화 프로그램의 경우, 쿼리는 통계 개체의 정의 내에서 적합해야 합니다. 앞의 예제를 사용하는 경우 쿼리의 WHERE 절은 2000101과 20001231 사이의 col1 값을 지정해야 합니다.

### <a name="create-single-column-statistics-with-all-the-options"></a>모든 옵션으로 단일 열 통계 만들기

옵션을 함께 결합할 수도 있습니다. 다음 예제에서는 사용자 지정 샘플 크기의 필터링된 통계 개체를 만듭니다.

```sql
CREATE STATISTICS stats_col1 ON table1 (col1) WHERE col1 > '2000101' AND col1 < '20001231' WITH SAMPLE = 50 PERCENT;
```

전체 참조의 경우 [CREATE STATISTICS](/sql/t-sql/statements/create-statistics-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)를 참조하세요.

### <a name="create-multi-column-statistics"></a>여러 열 통계 만들기

다중 열 통계 개체를 만들려면 이전 예제를 사용하지만 더 많은 열을 지정합니다.

> [!NOTE]
> 쿼리 결과에서 행 수를 예측하는 데 사용되는 히스토그램은 통계 개체 정의에 나열된 첫 번째 열에 대해서만 사용할 수 있습니다.

이 예에서 히스토그램은 *product\_category*에 있습니다. 교차 열 통계는 *제품\_범주* 및 *제품\_sub_category*대해 계산됩니다.

```sql
CREATE STATISTICS stats_2cols ON table1 (product_category, product_sub_category) WHERE product_category > '2000101' AND product_category < '20001231' WITH SAMPLE = 50 PERCENT;
```

*product\_category* 및 *product\_sub\_category* 간에 상관 관계가 있으므로, 이러한 열을 동시에 액세스하는 경우 다중 열 통계 개체가 유용할 수 있습니다.

### <a name="create-statistics-on-all-columns-in-a-table"></a>테이블의 모든 열에 대한 통계 만들기

통계를 만드는 한 가지 방법은 테이블을 만든 후 CREATE STATISTICS 명령을 실행하는 것입니다.

```sql
CREATE TABLE dbo.table1
(
   col1 int
,  col2 int
,  col3 int
)
WITH
  (
    CLUSTERED COLUMNSTORE INDEX
  )
;

CREATE STATISTICS stats_col1 on dbo.table1 (col1);
CREATE STATISTICS stats_col2 on dbo.table2 (col2);
CREATE STATISTICS stats_col3 on dbo.table3 (col3);
```

### <a name="use-a-stored-procedure-to-create-statistics-on-all-columns-in-a-database"></a>저장된 프로시저를 사용하여 데이터베이스의 모든 열에서 통계를 만듭니다.

SQL 풀에는 SQL Server의 sp_create_stats 해당하는 시스템 저장 프로시저가 없습니다. 이 저장된 프로시저는 아직 통계가 없는 데이터베이스의 모든 열에 단일 열 통계 개체를 만듭니다.

다음 예제는 데이터베이스 설계를 시작하는 데 도움이 됩니다. 사용자의 요구에 맞게 자유롭게 적용합니다.

```sql
CREATE PROCEDURE    [dbo].[prc_sqldw_create_stats]
(   @create_type    tinyint -- 1 default 2 Fullscan 3 Sample
,   @sample_pct     tinyint
)
AS

IF @create_type IS NULL
BEGIN
    SET @create_type = 1;
END;

IF @create_type NOT IN (1,2,3)
BEGIN
    THROW 151000,'Invalid value for @stats_type parameter. Valid range 1 (default), 2 (fullscan) or 3 (sample).',1;
END;

IF @sample_pct IS NULL
BEGIN;
    SET @sample_pct = 20;
END;

IF OBJECT_ID('tempdb..#stats_ddl') IS NOT NULL
BEGIN;
    DROP TABLE #stats_ddl;
END;

CREATE TABLE #stats_ddl
WITH    (   DISTRIBUTION    = HASH([seq_nmbr])
        ,   LOCATION        = USER_DB
        )
AS
WITH T
AS
(
SELECT      t.[name]                        AS [table_name]
,           s.[name]                        AS [table_schema_name]
,           c.[name]                        AS [column_name]
,           c.[column_id]                   AS [column_id]
,           t.[object_id]                   AS [object_id]
,           ROW_NUMBER()
            OVER(ORDER BY (SELECT NULL))    AS [seq_nmbr]
FROM        sys.[tables] t
JOIN        sys.[schemas] s         ON  t.[schema_id]       = s.[schema_id]
JOIN        sys.[columns] c         ON  t.[object_id]       = c.[object_id]
LEFT JOIN   sys.[stats_columns] l   ON  l.[object_id]       = c.[object_id]
                                    AND l.[column_id]       = c.[column_id]
                                    AND l.[stats_column_id] = 1
LEFT JOIN    sys.[external_tables] e    ON    e.[object_id]        = t.[object_id]
WHERE       l.[object_id] IS NULL
AND            e.[object_id] IS NULL -- not an external table
)
SELECT  [table_schema_name]
,       [table_name]
,       [column_name]
,       [column_id]
,       [object_id]
,       [seq_nmbr]
,       CASE @create_type
        WHEN 1
        THEN    CAST('CREATE STATISTICS '+QUOTENAME('stat_'+table_schema_name+ '_' + table_name + '_'+column_name)+' ON '+QUOTENAME(table_schema_name)+'.'+QUOTENAME(table_name)+'('+QUOTENAME(column_name)+')' AS VARCHAR(8000))
        WHEN 2
        THEN    CAST('CREATE STATISTICS '+QUOTENAME('stat_'+table_schema_name+ '_' + table_name + '_'+column_name)+' ON '+QUOTENAME(table_schema_name)+'.'+QUOTENAME(table_name)+'('+QUOTENAME(column_name)+') WITH FULLSCAN' AS VARCHAR(8000))
        WHEN 3
        THEN    CAST('CREATE STATISTICS '+QUOTENAME('stat_'+table_schema_name+ '_' + table_name + '_'+column_name)+' ON '+QUOTENAME(table_schema_name)+'.'+QUOTENAME(table_name)+'('+QUOTENAME(column_name)+') WITH SAMPLE '+CONVERT(varchar(4),@sample_pct)+' PERCENT' AS VARCHAR(8000))
        END AS create_stat_ddl
FROM T
;

DECLARE @i INT              = 1
,       @t INT              = (SELECT COUNT(*) FROM #stats_ddl)
,       @s NVARCHAR(4000)   = N''
;

WHILE @i <= @t
BEGIN
    SET @s=(SELECT create_stat_ddl FROM #stats_ddl WHERE seq_nmbr = @i);

    PRINT @s
    EXEC sp_executesql @s
    SET @i+=1;
END

DROP TABLE #stats_ddl;
```

기본값을 사용하여 테이블의 모든 열에 대한 통계를 만들려면 저장 프로시저를 실행합니다.

```sql
EXEC [dbo].[prc_sqldw_create_stats] 1, NULL;
```

fullscan을 사용하여 테이블의 모든 열에 대한 통계를 만들려면 이 프로시저를 호출합니다.

```sql
EXEC [dbo].[prc_sqldw_create_stats] 2, NULL;
```

테이블의 모든 열에서 샘플링된 통계를 만들려면 3을 입력하고 샘플 퍼센트를 입력합니다. 이 절차는 20%의 샘플 레이트(sample rate)를 사용합니다.

```sql
EXEC [dbo].[prc_sqldw_create_stats] 3, 20;
```

## <a name="examples-update-statistics"></a>예제: 통계 업데이트

통계를 업데이트하려면 다음을 수행할 수 있습니다.

- 하나의 통계 개체를 업데이트합니다. 업데이트하려는 통계 개체의 이름을 지정합니다.
- 테이블에 있는 모든 통계 개체를 업데이트합니다. 하나의 특정 통계 개체 대신 테이블의 이름을 지정합니다.

### <a name="update-one-specific-statistics-object"></a>하나의 통계 개체 업데이트

특정 통계 개체를 업데이트하려면 다음 구문을 사용합니다.

```sql
UPDATE STATISTICS [schema_name].[table_name]([stat_name]);
```

예를 들어:

```sql
UPDATE STATISTICS [dbo].[table1] ([stats_col1]);
```

특정 통계 개체를 업데이트하여 통계를 관리하는 데 필요한 리소스와 시간을 최소화할 수 있습니다. 이렇게 하려면 업데이트할 최상의 통계 개체를 선택해야 합니다.

### <a name="update-all-statistics-on-a-table"></a>테이블에 대한 모든 통계 업데이트

테이블의 모든 통계 개체를 업데이트하는 간단한 방법은 다음과 같은 것입니다.

```sql
UPDATE STATISTICS [schema_name].[table_name];
```

예를 들어:

```sql
UPDATE STATISTICS dbo.table1;
```

업데이트 통계 문은 사용하기 쉽습니다. 테이블에 대한 *모든* 통계를 업데이트하므로 필요한 것보다 더 많은 작업을 수행할 수 있습니다 성능이 문제가 되지 않는 경우 통계가 최신 상태임을 보장하는 가장 쉽고 완벽한 방법입니다.

> [!NOTE]
> 테이블의 모든 통계를 업데이트할 때 SQL 풀은 각 통계 개체에 대한 테이블을 샘플링하는 검사를 수행합니다. 테이블이 크고 많은 열과 통계가 있는 경우 필요에 따라 개별 통계를 업데이트하는 것이 더 효율적일 수 있습니다.

`UPDATE STATISTICS` 프로시저의 구현은 [임시 테이블](sql-data-warehouse-tables-temporary.md)을 참조하세요. 구현 방법은 앞의 `CREATE STATISTICS` 프로시저와 약간 다르지만 그 결과는 동일합니다.

전체 구문의 경우, [통계 업데이트](/sql/t-sql/statements/update-statistics-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)를 참조하세요.

## <a name="statistics-metadata"></a>통계 메타데이터

통계에 대한 정보를 찾는 데 사용할 수 있는 몇 가지 시스템 뷰 및 함수가 있습니다. 예를 들어 통계가 마지막으로 만들어지거나 업데이트된 시기를 확인하는 stats-date 함수를 사용하여 통계 개체가 오래되었는지 확인할 수 있습니다.

### <a name="catalog-views-for-statistics"></a>통계에 대한 카탈로그 뷰

이 시스템 뷰는 통계에 대한 정보를 제공합니다.

| 카탈로그 뷰 | Description |
|:--- |:--- |
| [sys.columns](/sql/relational-databases/system-catalog-views/sys-columns-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) |각 열에 대해 한 행입니다. |
| [sys.objects](/sql/relational-databases/system-catalog-views/sys-objects-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) |데이터베이스의 각 개체에 대해 한 행입니다. |
| [sys.schemas](/sql/relational-databases/system-catalog-views/sys-objects-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) |데이터베이스의 각 스키마에 대해 한 행입니다. |
| [sys.stats](/sql/relational-databases/system-catalog-views/sys-stats-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) |각 통계 개체에 대해 한 행입니다. |
| [sys.stats_columns](/sql/relational-databases/system-catalog-views/sys-stats-columns-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) |통계 개체의 각 열에 대해 한 행입니다. sys.columns에 다시 연결합니다. |
| [sys.tables](/sql/relational-databases/system-catalog-views/sys-tables-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) |각 테이블에 대해 한 행입니다(외부 테이블 포함). |
| [sys.table_types](/sql/relational-databases/system-catalog-views/sys-table-types-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) |각 데이터 유형에 대해 한 행입니다. |

### <a name="system-functions-for-statistics"></a>통계에 대한 시스템 함수

이 시스템 함수는 통계를 작업할 때 유용합니다.

| 시스템 함수 | Description |
|:--- |:--- |
| [STATS_DATE](/sql/t-sql/functions/stats-date-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) |통계 개체가 마지막으로 업데이트된 날짜입니다. |
| [DBCC SHOW_STATISTICS](/sql/t-sql/database-console-commands/dbcc-show-statistics-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) |통계 개체에서 인식되는 값의 분포에 대한 요약 수준 및 세부 정보 |

### <a name="combine-statistics-columns-and-functions-into-one-view"></a>통계 열 및 함수를 하나의 보기로 결합

이 뷰는 통계와 관련된 열 및 STATS_DATE() 함수의 결과를 모두 제공합니다.

```sql
CREATE VIEW dbo.vstats_columns
AS
SELECT
        sm.[name]                           AS [schema_name]
,       tb.[name]                           AS [table_name]
,       st.[name]                           AS [stats_name]
,       st.[filter_definition]              AS [stats_filter_definition]
,       st.[has_filter]                     AS [stats_is_filtered]
,       STATS_DATE(st.[object_id],st.[stats_id])
                                            AS [stats_last_updated_date]
,       co.[name]                           AS [stats_column_name]
,       ty.[name]                           AS [column_type]
,       co.[max_length]                     AS [column_max_length]
,       co.[precision]                      AS [column_precision]
,       co.[scale]                          AS [column_scale]
,       co.[is_nullable]                    AS [column_is_nullable]
,       co.[collation_name]                 AS [column_collation_name]
,       QUOTENAME(sm.[name])+'.'+QUOTENAME(tb.[name])
                                            AS two_part_name
,       QUOTENAME(DB_NAME())+'.'+QUOTENAME(sm.[name])+'.'+QUOTENAME(tb.[name])
                                            AS three_part_name
FROM    sys.objects                         AS ob
JOIN    sys.stats           AS st ON    ob.[object_id]      = st.[object_id]
JOIN    sys.stats_columns   AS sc ON    st.[stats_id]       = sc.[stats_id]
                            AND         st.[object_id]      = sc.[object_id]
JOIN    sys.columns         AS co ON    sc.[column_id]      = co.[column_id]
                            AND         sc.[object_id]      = co.[object_id]
JOIN    sys.types           AS ty ON    co.[user_type_id]   = ty.[user_type_id]
JOIN    sys.tables          AS tb ON  co.[object_id]        = tb.[object_id]
JOIN    sys.schemas         AS sm ON  tb.[schema_id]        = sm.[schema_id]
WHERE   1=1
AND     st.[user_created] = 1
;
```

## <a name="dbcc-show_statistics-examples"></a>DBCC SHOW_STATISTICS() 예

DBCC SHOW_STATISTICS()는 통계 개체 내에 있는 데이터를 보여줍니다. 이 데이터는 다음 세 부분으로 제공됩니다.

- 헤더
- 밀도 벡터
- 히스토그램

통계에 대한 헤더 메타데이터입니다. 히스토그램은 통계 개체의 첫 번째 키 열에 값의 분포를 표시합니다. 밀도 벡터는 열 간 상관 관계를 측정합니다.

> [!NOTE]
> SQL 풀은 통계 개체의 데이터를 사용하여 카디널리티 추정을 계산합니다.

### <a name="show-header-density-and-histogram"></a>헤더, 밀도 및 히스토그램 표시

이 간단한 예제에서는 통계 개체의 세 부분을 모두 보여 줍니다.

```sql
DBCC SHOW_STATISTICS([<schema_name>.<table_name>],<stats_name>)
```

예를 들어:

```sql
DBCC SHOW_STATISTICS (dbo.table1, stats_col1);
```

### <a name="show-one-or-more-parts-of-dbcc-show_statistics"></a>하나 이상의 DBCC SHOW_STATISTICS() 부분 표시

특정 부분을 보는 데만 관심이 있는 경우 `WITH` 절을 사용하고 보려는 부분을 지정합니다.

```sql
DBCC SHOW_STATISTICS([<schema_name>.<table_name>],<stats_name>) WITH stat_header, histogram, density_vector
```

예를 들어:

```sql
DBCC SHOW_STATISTICS (dbo.table1, stats_col1) WITH histogram, density_vector
```

## <a name="dbcc-show_statistics-differences"></a>DBCC SHOW_STATISTICS() 차이점

DBCC SHOW_STATISTICS()는 SQL Server와 비교하여 SQL 풀에서 더 엄격하게 구현됩니다.

- 문서화되지 않은 기능은 지원되지 않습니다.
- Stats_stream은 사용할 수 없습니다.
- 통계 데이터의 특정 하위 집합에 대한 결과를 조인할 수 없습니다. 예를 들어 DENSITY_VECTOR STAT_HEADER.
- NO_INFOMSGS는 메시지 비표시에 대해 설정할 수 없습니다.
- 대괄호는 통계 이름 주위에 사용할 수 없습니다.
- 열 이름을 사용하여 통계 개체를 식별할 수 없습니다.
- 2767 사용자 지정 오류는 지원되지 않습니다.

## <a name="next-steps"></a>다음 단계

쿼리 성능 추가 향상은 [작업 모니터링](sql-data-warehouse-manage-monitor.md) 참조
