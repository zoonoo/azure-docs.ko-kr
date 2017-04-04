---
title: "SQL Data Warehouse의 테이블에 대한 통계 관리 | Microsoft Docs"
description: "Azure SQL 데이터 웨어하우스에서 테이블에 대한 통계 시작"
services: sql-data-warehouse
documentationcenter: NA
author: jrowlandjones
manager: jhubbard
editor: 
ms.assetid: faa1034d-314c-4f9d-af81-f5a9aedf33e4
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: tables
ms.date: 10/31/2016
ms.author: jrj;barbkess
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: b2b99ec031ea26b4ab19e7327da035788661a0a8
ms.lasthandoff: 12/08/2016


---
# <a name="managing-statistics-on-tables-in-sql-data-warehouse"></a>SQL 데이터 웨어하우스의 테이블에 대한 통계 관리
> [!div class="op_single_selector"]
> * [개요][Overview]
> * [데이터 형식][Data Types]
> * [배포][Distribute]
> * [인덱스][Index]
> * [파티션][Partition]
> * [통계][Statistics]
> * [임시][Temporary]
> 
> 

SQL 데이터 웨어하우스가 데이터에 대해 더 많이 알수록 데이터에 대해 더 빠르게 쿼리를 실행할 수 있습니다.  SQL 데이터 웨어하우스에 데이터에 대한 정보를 전달하는 방법은 데이터에 대한 통계를 수집하는 것입니다.  데이터에 대해 통계를 수집하는 것이 쿼리를 최적화하는 데 있어서 가장 중요한 방법입니다.  통계는 SQL 데이터 웨어하우스가 최적의 쿼리 계획을 만드는 데 도움을 줍니다.  SQL 데이터 웨어하우스 쿼리 최적화 프로그램이 비용 기반 최적화 프로그램이기 때문입니다.  즉, 다양한 쿼리 계획의 비용과 비교한 후 최저 비용이면서 가장 빠르게 실행되는 계획을 선택합니다.

단일 열, 여러 열 또는 테이블의 인덱스에 대해 통계를 만들 수 있습니다.  통계는 범위 및 값의 선택도를 캡처하는 히스토그램에 저장됩니다.  최적화 프로그램이 쿼리에서 JOIN, GROUP BY, HAVING 및 WHERE 절을 평가해야 하는 경우 더욱 흥미롭습니다.  예를 들어 최적화 프로그램이 쿼리에서 필터링하는 날짜가 1개의 행을 반환할 것으로 예측할 경우 사용자가 선택한 날짜가 1백만 개의 행을 반환할 것으로 예측할 때와는 다른 계획을 선택할 수 있습니다.  통계를 만드는 것은 매우 중요하지만 마찬가지로 통계가 테이블의 현재 상태를 *정확하게* 반영하는 것도 중요합니다.  최신 통계가 있으면 최적화 프로그램에서 적합한 계획을 선택할 수 있습니다.  최적화 프로그램으로 만든 계획은 데이터에 대한 통계만큼 훌륭합니다.

통계를 만들고 업데이트하는 프로세스는 현재 수동 프로세스이지만 매우 간단하게 수행할 수 있습니다.  이러한 방식은 단일 열 및 인덱스에 대한 통계를 자동으로 만들고 업데이트하는 SQL Server와는 다릅니다.  아래 정보를 사용하여 데이터에 대한 통계 관리를 자동화할 수 있습니다. 

## <a name="getting-started-with-statistics"></a>통계 시작
 모든 열에 샘플링된 통계를 만드는 것이 통계로 시작하는 쉬운 방법입니다.  통계를 최신 상태로 유지하는 것도 마찬가지로 중요하므로 보존적인 접근 방법은 매일 또는 각 로드 후 통계를 업데이트하는 것일 수 있습니다. 통계를 작성하고 업데이트하는 성능 및 비용 간의 장단점은 항상 있습니다.  모든 통계를 유지하는 데 시간이 너무 오래 소요되는 경우 어떤 열에 통계가 있고 어떤 열에서 자주 업데이트가 필요한지 더 선별적으로 시도해볼 수 있습니다.  예를 들어 매 로드 이후에만 새 값이 추가될 수 있는 것은 아니므로 매일 날짜 열을 업데이트하려고 합니다. JOIN, GROUP BY, HAVING 및 WHERE 절에 사용된 열에서 통계를 유지하면 가장 큰 이점을 얻게 됩니다.  많은 열이 SELECT 절에서만 사용되는 열이 테이블에 있으면 이러한 열에 대한 통계는 도움이 되지 않을 수 있으며, 좀 더 노력해서 통계가 도움이 될만한 열을 찾으면 통계를 유지 관리하는 시간도 단축할 수 있습니다.

## <a name="multi-column-statistics"></a>여러 열 통계
단일 열에서 통계를 생성하는 것 외에, 여러 열 통계를 사용하면 쿼리에 도움이 될 수 있습니다.  여러 열 통계는 열 목록에 대해 만든 통계입니다.  목록에서 첫 번째 열에 대한 단일 열 통계를 포함하며 또한 밀도라는 일부 열 간 상관 관계 정보를 포함합니다.  예를 들어 두 열에서 다른 테이블에 조인하는 테이블이 있는 경우 SQL 데이터 웨어하우스가 두 열 사이의 관계를 이해할 경우 계획을 더욱 최적화할 수 있다는 것을 알 수 있습니다.   여러 열 통계는 복합 조인 및 그룹 기준과 같은 일부 작업에 대한 쿼리 성능을 향상시킬 수 있습니다.

## <a name="updating-statistics"></a>통계 업데이트
통계 업데이트는 사용자 데이터베이스 관리 루틴의 중요한 부분입니다.  데이터베이스의 데이터 배포가 변경된 경우, 통계를 업데이트해야 합니다.  통계가 오래되면 차선의 쿼리 성능이 유지됩니다.

모범 사례 중 하나는 새로운 날짜가 추가되는 날마다 날짜 열에 통계를 업데이트하는 것입니다.  새 행이 데이터 웨어하우스에 로드될 때마다 새 부하 날짜나 트랜잭션 날짜가 추가됩니다. 데이터 분포를 변경하며 통계는 최신 상태가 아닙니다. 반대로, 값의 분포는 일반적으로 바뀌지 않기 때문에 customer 테이블의 국가 열에 대한 통계는 업데이트를 필요로 하지 않을 수 있습니다. 고객 간의 배포가 상수라고 가정하는 경우, 테이블 변형에 새 행을 추가하면 데이터 배포를 변경하지 않습니다. 그러나 데이터 웨어하우스에 하나의 국가만이 포함되어 있고 새로운 국가에서 데이터를 가지고 오는 경우에는 여러 국가로부터 가져온 데이터가 저장되어 있으므로 국가 열의 통계를 업데이트해야 합니다.

쿼리 문제를 해결할 때 첫 번째 질문 중 하나는 "통계가 최신입니까?"입니다.

이 질문은 데이터의 기간에 따라 응답할 수 있는 질문은 아닙니다. 기본 데이터에 중요한 변화가 없었다면 최신 통계 개체도 아주 오래되었을 수 있습니다. 행 수가 변경되었거나 지정된 된 열에 대한 값의 분포에 중요한 변화가 있다면 *이때* 통계를 업데이트해야 합니다.  

참고로 **SQL Server** (SQL 데이터 웨어하우스 아님)는 다음 상황에서 자동으로 통계를 업데이트합니다.

* 테이블에 0개 행이 있을 때 행을 추가하면 통계가 자동 업데이트됩니다.
* 500개보다 적은 수의 행을 가진 테이블에 500개 이상의 행을 추가하면(예: 499개 행에 500개 행을 추가하여 999개 행이 될 때) 자동 업데이트됩니다. 
* 500개 행 이상이 되면 500개의 추가 행 + 테이블 크기의 20%를 추가해야 통계를 자동 업데이트할 수 있습니다.

테이블의 데이터가 마지막 통계 업데이트 이후로 변경되었는지를 확인할 수 있는 DMV가 없기 때문에 통계 기간을 아는 것이 일부 도움이 될 수 있습니다.  다음 쿼리를 사용하여 각 테이블에서 마지막으로 통계가 업데이트된 위치를 확인할 수 있습니다.  

> [!NOTE]
> 주어진 열에 대해 값의 분포에 중요한 변화가 있다면 마지막으로 업데이트된 시기와 관계없이 통계를 업데이트해야 합니다.  
> 
> 

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

예를 들어, 일반적으로 데이터 웨어하우스의 날짜 열은 자주 통계 업데이트가 필요합니다. 새 행이 데이터 웨어하우스에 로드될 때마다 새 부하 날짜나 트랜잭션 날짜가 추가됩니다. 데이터 분포를 변경하며 통계는 최신 상태가 아닙니다.  반대로, 고객 테이블의 성별 열에 대한 통계는 업데이트할 필요가 없습니다. 고객 간의 배포가 상수라고 가정하는 경우, 테이블 변형에 새 행을 추가하면 데이터 배포를 변경하지 않습니다. 그러나 데이터 웨어하우스가 한 가지 성별만을 포함하고 새 요구 사항의 결과가 여러 성별인 경우, 성별 열에서 통계를 업데이트해야 합니다.

자세한 설명은 MSDN에서 [통계][Statistics]를 참조하세요.

## <a name="implementing-statistics-management"></a>통계 관리 구현
데이터 로딩 프로세스를 확장하여 로드 끝에 통계가 업데이트되는지 확인하는 것이 좋습니다. 데이터 로드는 테이블이 값의 크기 및/또는 배포를 자주 변경하는 경우입니다. 따라서 일부 관리 프로세스를 구현할 수 있는 논리 위치입니다.

로드 프로세스 중에 통계를 업데이트하기 위해 다음 일부 가이딩 원칙이 제공됩니다.

* 로드된 각 테이블에 하나 이상의 업데이트된 통계 개체가 있는지 확인합니다. 통계 업데이트의 일부로 테이블 크기(행 개수 및 페이지 수) 정보를 업데이트합니다.
* JOIN, GROUP BY, ORDER BY 및 DISTINCT 절에 참여하는 열에 집중
* 이러한 값은 통계 히스토그램에 포함되지 않기 때문에 트랜잭션 날짜와 같은 "키 오름차순" 열을 업데이트하는 것이 좋습니다.
* 정적 배포 열은 자주 업데이트하지 않는 것이 좋습니다.
* 각 통계 개체가 연속으로 업데이트됩니다. `UPDATE STATISTICS <TABLE_NAME>` 의 간단한 구현은 특히 통계 개체가 많은 너비가 넓은 테이블에는 적합하지 않을 수 있습니다.

> [!NOTE]
> [키 오름차순]에 대한 자세한 내용은 SQL Server 2014 카디널리티 예측 모델 백서를 참조하세요.
> 
> 

자세한 설명은 MSDN에서 [카디널리티 예측][Cardinality Estimation]을 참조하세요.

## <a name="examples-create-statistics"></a>예제: 통계 작성
이 예제는 통계를 만들기 위한 다양한 옵션을 사용하는 방법을 보여줍니다. 각 열에 대해 사용하는 옵션은 데이터의 특징 및 열이 쿼리에서 사용되는 방법에 따라 다릅니다.

### <a name="a-create-single-column-statistics-with-default-options"></a>A. 기본 옵션으로 단일 열 통계 만들기
열에서 통계를 만들려면, 통계 개체에 대한 이름과 열 이름을 제공하면 됩니다.

이 구문은 모든 기본 옵션을 사용합니다. 기본적으로 SQL 데이터 웨어하우스가 통계를 만들 때 테이블의 20%를 샘플링합니다.

```sql
CREATE STATISTICS [statistics_name] ON [schema_name].[table_name]([column_name]);
```

예:

```sql
CREATE STATISTICS col1_stats ON dbo.table1 (col1);
```

### <a name="b-create-single-column-statistics-by-examining-every-row"></a>B. 모든 행을 검사하여 단일 열 통계 만들기
20%의 기본 샘플링 속도는 대부분의 상황에 충분합니다. 그러나 샘플링 비율을 조정할 수 있습니다.

전체 테이블을 샘플링하려면 이 구문을 사용합니다.

```sql
CREATE STATISTICS [statistics_name] ON [schema_name].[table_name]([column_name]) WITH FULLSCAN;
```

예:

```sql
CREATE STATISTICS col1_stats ON dbo.table1 (col1) WITH FULLSCAN;
```

### <a name="c-create-single-column-statistics-by-specifying-the-sample-size"></a>C. 샘플 크기를 지정하여 단일 열 통계 만들기
또는 백분율로 샘플 크기를 지정할 수 있습니다.

```sql
CREATE STATISTICS col1_stats ON dbo.table1 (col1) WITH SAMPLE = 50 PERCENT;
```

### <a name="d-create-single-column-statistics-on-only-some-of-the-rows"></a>D. 일부 행에 대해서만 단일 열 통계 만들기
다른 옵션의 경우, 테이블에 있는 행의 일부에 통계를 만들 수 있습니다. 이를 필터링된 통계라고 합니다.

예를 들어, 크게 분할된 테이블의 특정 파티션을 쿼리해야 하는 경우 필터링된 통계를 사용할 수 있습니다. 파티션 값만 통계를 만들어서 통계의 정확도가 향상되므로 쿼리 성능이 향상됩니다.

이 예에서는 값의 범위에서 통계를 만듭니다. 파티션의 값의 범위와 일치하도록 쉽게 값을 정의할 수 있습니다.

```sql
CREATE STATISTICS stats_col1 ON table1(col1) WHERE col1 > '2000101' AND col1 < '20001231';
```

> [!NOTE]
> 분산된 쿼리 계획을 선택하는 경우 필터링된 통계 사용을 고려하는 쿼리 최적화 프로그램의 경우, 쿼리는 통계 개체의 정의 내에서 적합해야 합니다. 이전 예제를 사용하면 쿼리의 where 절은 2000101과 20001231 사이에 col1 값을 지정해야 합니다.
> 
> 

### <a name="e-create-single-column-statistics-with-all-the-options"></a>E. 모든 옵션으로 단일 열 통계 만들기
물론, 옵션과 함께 결합할 수 있습니다. 다음 예제에서는 사용자 지정 샘플 크기로 필터링된 통계 개체를 만듭니다.

```sql
CREATE STATISTICS stats_col1 ON table1 (col1) WHERE col1 > '2000101' AND col1 < '20001231' WITH SAMPLE = 50 PERCENT;
```

전체 참조의 경우 MSDN에서 [CREATE STATISTICS][CREATE STATISTICS]를 참조하세요.

### <a name="f-create-multi-column-statistics"></a>F. 여러 열 통계 만들기
여러 열 통계를 만들려면, 이전 예제를 사용하지만 더 많은 열을 지정합니다.

> [!NOTE]
> 쿼리 결과에서 행의 수를 예상하는 데 사용되는 히스토그램은 통계 개체 정의에 나열된 첫 번째 열에 사용할 수만 있습니다.
> 
> 

이 예에서 히스토그램은 *product\_category*에 있습니다. 열 간 통계는 *product\_category* 및 *product\_sub_c\ategory*에서 계산됩니다.

```sql
CREATE STATISTICS stats_2cols ON table1 (product_category, product_sub_category) WHERE product_category > '2000101' AND product_category < '20001231' WITH SAMPLE = 50 PERCENT;
```

*product\_category* 및 *product\_sub\_category* 사이에 상관 관계가 있으므로, 이 열을 동시에 액세스하는 경우 다중 열 통계가 유용할 수 있습니다.

### <a name="g-create-statistics-on-all-the-columns-in-a-table"></a>G. 테이블의 모든 열에 대한 통계 만들기
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

### <a name="h-use-a-stored-procedure-to-create-statistics-on-all-columns-in-a-database"></a>H 저장된 프로시저를 사용하여 데이터베이스의 모든 열에서 통계를 만듭니다.
SQL 데이터 웨어하우스는 SQL Server에서 [sp_create_stats][]에 해당하는 시스템 저장 프로시저가 없습니다. 이 저장된 프로시저는 아직 통계가 없는 데이터베이스의 모든 열에 단일 열 통계 개체를 만듭니다.

데이터베이스 디자인으로 시작하는 데 도움이 됩니다. 사용자의 요구에 맞게 자유롭게 적용합니다.

```sql
CREATE PROCEDURE    [dbo].[prc_sqldw_create_stats]
(   @create_type    tinyint -- 1 default 2 Fullscan 3 Sample
,   @sample_pct     tinyint
)
AS

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
        THEN    CAST('CREATE STATISTICS '+QUOTENAME('stat_'+table_schema_name+ '_' + table_name + '_'+column_name)+' ON '+QUOTENAME(table_schema_name)+'.'+QUOTENAME(table_name)+'('+QUOTENAME(column_name)+') WITH SAMPLE '+@sample_pct+'PERCENT' AS VARCHAR(8000))
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

이 절차로 테이블의 모든 열에서 통계를 만들려면 프로시저를 호출하기만 하면 됩니다.

```sql
prc_sqldw_create_stats;
```

## <a name="examples-update-statistics"></a>예제: 통계 업데이트
통계를 업데이트하려면 다음을 수행할 수 있습니다.

1. 하나의 통계 개체를 업데이트합니다. 업데이트하려는 통계 개체의 이름을 지정합니다.
2. 테이블에 있는 모든 통계 개체를 업데이트합니다. 하나의 특정 통계 개체 대신 테이블의 이름을 지정합니다.

### <a name="a-update-one-specific-statistics-object"></a>A. 하나의 통계 개체 업데이트
특정 통계 개체를 업데이트하려면 다음 구문을 사용합니다.

```sql
UPDATE STATISTICS [schema_name].[table_name]([stat_name]);
```

예:

```sql
UPDATE STATISTICS [dbo].[table1] ([stats_col1]);
```

특정 통계 개체를 업데이트하여 통계를 관리하는 데 필요한 리소스와 시간을 최소화할 수 있습니다. 그러나 업데이트할 최상의 통계 개체를 선택하려면 필요합니다.

### <a name="b-update-all-statistics-on-a-table"></a>B. 테이블에 있는 모든 통계 업데이트
테이블에 있는 모든 통계 개체를 업데이트하기 위한 간단한 방법을 보여줍니다.

```sql
UPDATE STATISTICS [schema_name].[table_name];
```

예:

```sql
UPDATE STATISTICS dbo.table1;
```

이 명령문은 사용하기 쉽습니다. 테이블에 대한 모든 통계를 업데이트하므로 필요한 것보다 더 많은 작업을 수행할 수 있습니다 성능이 중요하지 않은 경우, 통계가 최신임을 보증하는 가장 완전하고 쉬운 방법입니다.

> [!NOTE]
> 테이블의 모든 통계를 업데이트하면 SQL 데이터 웨어하우스는 각 통계에 대한 테이블을 스캔하여 샘플링합니다. 테이블이 크고 열이 많고 통계가 많은 경우, 필요에 따라 개별 통계를 업데이트하는 것이 더욱 효율적일 수 있습니다.
> 
> 

`UPDATE STATISTICS` 절차 구현의 경우, [임시 테이블][Temporary] 문서를 참조하세요. 구현 방법은 위의 `CREATE STATISTICS` 절차와 약간 다르지만 최종 결과는 동일합니다.

전체 구문의 경우, MSDN에서 [통계 업데이트][Update Statistics]를 참조하세요.

## <a name="statistics-metadata"></a>통계 메타데이터
통계에 대한 정보를 찾는 데 사용할 수 있는 여러 시스템 뷰 및 함수가 있습니다. 예를 들어, 통계가 마지막으로 작성되거나 업데이트되는 시기를 알 수 있는 stats-date 함수를 사용하여 통계 개체가 최신이 아닌지 알 수 있습니다.

### <a name="catalog-views-for-statistics"></a>통계에 대한 카탈로그 뷰
이 시스템 뷰는 통계에 대한 정보를 제공합니다.

| 카탈로그 뷰 | 설명 |
|:--- |:--- |
| [sys.columns][sys.columns] |각 열에 대해 한 행입니다. |
| [sys.objects][sys.objects] |데이터베이스의 각 개체에 대해 한 행입니다. |
| [sys.schemas][sys.schemas] |데이터베이스의 각 스키마에 대해 한 행입니다. |
| [sys.stats][sys.stats] |각 통계 개체에 대해 한 행입니다. |
| [sys.stats_columns][sys.stats_columns] |통계 개체의 각 열에 대해 한 행입니다. sys.columns에 다시 연결합니다. |
| [sys.tables][sys.tables] |각 테이블에 대해 한 행입니다(외부 테이블 포함). |
| [sys.table_types][sys.table_types] |각 데이터 유형에 대해 한 행입니다. |

### <a name="system-functions-for-statistics"></a>통계에 대한 시스템 함수
이 시스템 함수는 통계를 작업할 때 유용합니다.

| 시스템 함수 | 설명 |
|:--- |:--- |
| [STATS_DATE][STATS_DATE] |통계 개체가 마지막으로 업데이트된 날짜입니다. |
| [DBCC SHOW_STATISTICS][DBCC SHOW_STATISTICS] |통계 개체로 인식되는 값의 분포에 대한 요약 수준 및 세부 정보를 제공합니다. |

### <a name="combine-statistics-columns-and-functions-into-one-view"></a>통계 열 및 함수를 하나의 보기로 결합
이 뷰는 통계와 관련된 열을 가져오며 [STATS_DATE()][] 함수의 결과입니다.

```sql
CREATE VIEW dbo.vstats_columns
AS
SELECT
        sm.[name]                           AS [schema_name]
,       tb.[name]                           AS [table_name]
,       st.[name]                           AS [stats_name]
,       st.[filter_definition]              AS [stats_filter_defiinition]
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

## <a name="dbcc-showstatistics-examples"></a>DBCC SHOW_STATISTICS() 예
DBCC SHOW_STATISTICS()는 통계 개체 내에 있는 데이터를 보여줍니다. 이 데이터는 세 부분으로 제공합니다.

1. 헤더
2. 밀도 벡터
3. 히스토그램

통계에 대한 헤더 메타데이터입니다. 히스토그램은 통계 개체의 첫 번째 키 열에 값의 분포를 표시합니다. 밀도 벡터는 열 간 상관 관계를 측정합니다. SQLDW는 통계 개체의 데이터 중 하나를 사용하여 카디널리티 예상치를 계산합니다.

### <a name="show-header-density-and-histogram"></a>헤더, 밀도 및 히스토그램 표시
이 간단한 예제에서는 통계 개체의 모든 세 부분을 보여줍니다.

```sql
DBCC SHOW_STATISTICS([<schema_name>.<table_name>],<stats_name>)
```

예:

```sql
DBCC SHOW_STATISTICS (dbo.table1, stats_col1);
```

### <a name="show-one-or-more-parts-of-dbcc-showstatistics"></a>DBCC SHOW_STATISTICS();의 하나 이상의 파트를 표시합니다.
특정 부분 보기에만 관심이 있는 경우, `WITH` 절을 사용하고 보려는 부분을 지정합니다.

```sql
DBCC SHOW_STATISTICS([<schema_name>.<table_name>],<stats_name>) WITH stat_header, histogram, density_vector
```

예:

```sql
DBCC SHOW_STATISTICS (dbo.table1, stats_col1) WITH histogram, density_vector
```

## <a name="dbcc-showstatistics-differences"></a>DBCC SHOW_STATISTICS() 차이점
DBCC SHOW_STATISTICS()는 SQL Server와 비교하여 SQL 데이터 웨어하우스에서 보다 엄격하게 구현됩니다.

1. 문서화되지 않은 기능은 지원되지 않습니다.
2. Stats_stream를 사용할 수 없습니다.
3. 통계 데이터의 특정 하위 집합에 대한 결과를 조인할 수는 없습니다(STAT_HEADER 조인 DENSITY_VECTOR).
4. NO_INFOMSGS는 메시지 제거에 대해 설정할 수 없습니다.
5. 통계 이름에 대괄호를 사용할 수 없습니다.
6. 통계 개체를 식별할 열 이름을 사용할 수 없습니다.
7. 사용자 지정 오류 2767이 지원되지 않습니다.

## <a name="next-steps"></a>다음 단계
자세한 내용은 MSDN에서 [DBCC SHOW_STATISTICS][DBCC SHOW_STATISTICS]를 참조하세요.  자세히 알아보려면 [테이블 개요][Overview], [테이블 데이터 유형][Data Types], [테이블 배포][Distribute], [테이블 인덱싱][Index], [테이블 분할][Partition] 및 [임시 테이블][Temporary]에 대한 문서를 참조하세요.  모범 사례에 대한 자세한 내용은 [SQL Data Warehouse 모범 사례][SQL Data Warehouse Best Practices]를 참조하세요.  

<!--Image references-->

<!--Article references-->
[Overview]: ./sql-data-warehouse-tables-overview.md
[Data Types]: ./sql-data-warehouse-tables-data-types.md
[Distribute]: ./sql-data-warehouse-tables-distribute.md
[Index]: ./sql-data-warehouse-tables-index.md
[Partition]: ./sql-data-warehouse-tables-partition.md
[Statistics]: ./sql-data-warehouse-tables-statistics.md
[Temporary]: ./sql-data-warehouse-tables-temporary.md
[SQL Data Warehouse Best Practices]: ./sql-data-warehouse-best-practices.md

<!--MSDN references-->  
[Cardinality Estimation]: https://msdn.microsoft.com/library/dn600374.aspx
[CREATE STATISTICS]: https://msdn.microsoft.com/library/ms188038.aspx
[DBCC SHOW_STATISTICS]:https://msdn.microsoft.com/library/ms174384.aspx
[Statistics]: https://msdn.microsoft.com/library/ms190397.aspx
[STATS_DATE]: https://msdn.microsoft.com/library/ms190330.aspx
[sys.columns]: https://msdn.microsoft.com/library/ms176106.aspx
[sys.objects]: https://msdn.microsoft.com/library/ms190324.aspx
[sys.schemas]: https://msdn.microsoft.com/library/ms190324.aspx
[sys.stats]: https://msdn.microsoft.com/library/ms177623.aspx
[sys.stats_columns]: https://msdn.microsoft.com/library/ms187340.aspx
[sys.tables]: https://msdn.microsoft.com/library/ms187406.aspx
[sys.table_types]: https://msdn.microsoft.com/library/bb510623.aspx
[UPDATE STATISTICS]: https://msdn.microsoft.com/library/ms187348.aspx

<!--Other Web references-->  

