<properties
   pageTitle="SQL 데이터 웨어하우스의 테이블 파티션 | Microsoft Azure"
	description="솔루션 개발을 위한 Azure SQL 데이터 웨어하우스의 테이블 파티션을 위한 팁"
	services="sql-data-warehouse"
	documentationCenter="NA"
	authors="jrowlandjones"
	manager="barbkess"
	editor=""/>

<tags
   ms.service="sql-data-warehouse"
	ms.devlang="NA"
	ms.topic="article"
	ms.tgt_pltfrm="NA"
	ms.workload="data-services"
	ms.date="06/22/2015"
	ms.author="JRJ@BigBangData.co.uk;barbkess"/>

# SQL 데이터 웨어하우스의 테이블 파티션

SQL Server 파티션 정의를 SQL 데이터 웨어하우스에 마이그레이션하려면:

- 테이블을 만들 때 SQL Server 파티션 함수 및 스키마가 관리되므로 이를 제거합니다.
- 테이블을 만들 때 파티션을 정의합니다. 파티션 경계 지점 및 경계 지점을 효과적인 `RANGE RIGHT` 또는 `RANGE LEFT`가 되도록 할 것인지 여부를 지정하기만 하면 됩니다.

### 파티션 크기 조정
SQL DW를 사용하면 DBA는 테이블 형식에서 다음 옵션을 선택할 수 있습니다. 힙, 클러스터된 인덱스(CI) 및 클러스터된 열 저장소 인덱스(CCI) 또한 이러한 테이블 유형 각각의 경우 DBA는 분할 테이블을 분할할 수 있습니다. 즉, 성능 향상을 위해 여러 섹션으로 나눌 수 있습니다. 그러나 너무 많은 파티션이 있는 테이블을 만들면 상황에 따라 실제로 성능이 저하되거나 쿼리 오류가 발생할 수 있습니다. 특히 CCI 테이블에서 이것이 우려됩니다. 분할이 도움이 되려면 DBA에 분할을 사용하는 시기 및 만들려는 파티션 수를 이해하는 것이 중요합니다. 이러한 지침은 DBA가 해당 시나리오에 가장 효과적인 선택을 하도록 마련되었습니다.

일반적으로 테이블 파티션은 두 가지 기본 방식에서 유용합니다.

1. 파티션 전환을 사용해서 테이블의 섹션을 신속하게 자릅니다. 일반적으로 사용 되는 디자인은 팩트 테이블을 위한 것이며 미리 지정된 일정 기간 동안 행을 포함합니다. 예를 들어 판매 팩트 테이블은 지난 36개월 동안 데이터를 포함할 수 있습니다. 매월 말에 가장 오래된 달의 판매 데이터는 테이블에서 삭제됩니다. 가장 오래된 달의 모든 행을 삭제하여 이 작업을 수행할 수 있지만 행 단위로 많은 양의 데이터를 삭제하려면 시간이 매우 오래 걸릴 수 있습니다. 이 시나리오를 최적화하기 위해 SQL DW은 파티션 교체를 지원하여 파티션에서 행의 전체 집합을 단일 빠른 작업에서 삭제할 수 있습니다.   

2. 쿼리가 분할 열에 조건자를 배치하는 경우 분할을 사용하면 쿼리에서 쉽게 많은 행 집합의 처리를 제외할 수 있습니다.(즉, 파티션) 예를 들어 판매 팩트 테이블은 판매 날짜 필드를 사용하여 36개월로 분할된 다음 판매 날짜를 필터링하는 쿼리가 필터와 일치하지 않는 파티션의 처리를 건너뛸 수 있습니다. 실제로 이 방식으로 사용되는 분할은 개략적으로 세분화된 인덱스입니다.

SQL DW에 클러스터된 열 저장소 인덱스를 만들 경우 DBA는 행의 수라는 추가 요소를 고려해야 합니다. CCI 테이블은 높은 수준의 압축 작업을 수행할 수 있고 SQL DW은 쿼리 성능을 가속화하는 데 도움이 됩니다. 압축이 SQL DW에서 내부적으로 작동하는 방법으로 인해 데이터가 압축되기 전에 CCI 테이블의 각 파티션에 상당히 많은 수의 행이 있어야 합니다. 또한 SQL DW은 많은 배포에 데이터를 분산하고 각 배포는 파티션에서 추가적으로 세분화됩니다. 최적의 압축 및 성능을 위해 배포 및 파티션 당 최소 100,000개의 행이 필요합니다. 위 예제에서 사용 판매 팩트 테이블이 36개의 월별 파티션을 포함하고 SQL DW에 60개의 배포판이 있다면 판매 팩트 테이블은 모든 달이 채워지는 경우 매월 6백만 개의 행 또는 216만 개의 행을 포함합니다. 테이블이 권장되는 최소값 보다 훨씬 적은 행을 포함하면 DBA는 배포 당 행 수를 크게 하기 위해 적은 수의 파티션으로 테이블을 만들 것을 고려해야 합니다.


파티션 수준에서 현재 데이터베이스의 크기를 조정하려면 다음과 같은 쿼리를 사용합니다.

```
SELECT      s.[name]                        AS      [schema_name]
,           t.[name]                        AS      [table_name]
,           i.[name]                        AS      [index_name]
,           p.[partition_number]            AS      [partition_number]
,           SUM(a.[used_pages]*8.0)         AS      [partition_size_kb]
,           SUM(a.[used_pages]*8.0)/1024    AS      [partition_size_mb]
,           SUM(a.[used_pages]*8.0)/1048576 AS      [partition_size_gb]
,           p.[rows]                        AS      [partition_row_count]
,           rv.[value]                      AS      [partition_boundary_value]
,           p.[data_compression_desc]       AS      [partition_compression_desc]
FROM        sys.schemas s
JOIN        sys.tables t                    ON      t.[schema_id]         = s.[schema_id]
JOIN        sys.partitions p                ON      p.[object_id]         = t.[object_id]
JOIN        sys.allocation_units a          ON      a.[container_id]        = p.[partition_id]
JOIN        sys.indexes i                   ON      i.[object_id]         = p.[object_id]
                                            AND     i.[index_id]          = p.[index_id]
JOIN        sys.data_spaces ds              ON      ds.[data_space_id]    = i.[data_space_id]
LEFT JOIN   sys.partition_schemes ps        ON      ps.[data_space_id]    = ds.[data_space_id]
LEFT JOIN   sys.partition_functions pf      ON      pf.[function_id]      = ps.[function_id]
LEFT JOIN   sys.partition_range_values rv   ON      rv.[function_id]      = pf.[function_id]
                                            AND     rv.[boundary_id]      = p.[partition_number]
WHERE       p.[index_id] <=1
GROUP BY    s.[name]
,           t.[name]
,           i.[name]
,           p.[partition_number]
,           p.[rows]
,           rv.[value]
,           p.[data_compression_desc]
;
```

총 배포는 테이블을 만들 때 사용된 저장소 위치의 수와 일치합니다. 각 테이블이 배포 당 한 번 만들어지는 복잡한 방법입니다.

행의 수 및 각 파티션의 크기를 대략 예상할 수도 있습니다. 원본 데이터 웨어하우스의 파티션은 각 배포로 분리됩니다.

파티션 크기를 결정할 때 안내되는 다음 계산을 사용합니다.

MPP 파티션 크기 = SMP 파티션 크기 / 배포 수

다음 쿼리를 사용하여 SQL 데이터 웨어하우스 데이터베이스의 배포 수를 알아낼 수 있습니다.

```
SELECT  COUNT(*)
FROM    sys.pdw_distributions
;
```

이제 원본 시스템의 각 파티션 크기 및 SQLDW에 대한 예상 크기를 알 수 있으므로 파티션 경계를 결정할 수 있습니다.

### 워크로드 관리
테이블 파티션 의사 결정에 염두에 두어야 하는 정보의 마지막 부분이 워크로드 관리입니다. SQL 데이터 웨어하우스에서 쿼리 실행 중 각 배포에 할당된 최대 메모리는 이 기능을 통해 제어됩니다. [워크로드 관리]에 관한 추가 정보는 다음 문서를 참조하세요. 이상적으로 파티션의 크기는 Columnstore 인덱스 다시 작성과 같은 inmemory 작업과 함께 조정됩니다. 인덱스 다시 작성은 메모리 집약적 작업입니다. 따라서 파티션 인덱스 다시 작성이 메모리를 부족하게 하지 않아야 합니다. 쿼리에 사용할 수 있는 메모리의 양을 늘리면 기본 역할에서 사용할 수 있는 다른 역할 중 하나로 전환하여 구현할 수 있습니다.

배포 당 메모리의 할당에 관한 정보는 리소스 관리자 동적 관리 뷰를 쿼리하여 사용 가능합니다. 실제로 아래 그림 보다 작은 프로그램 메모리가 부여됩니다. 그러나 데이터 관리 작업에 대한 파티션의 크기를 조정할 때 사용할 수 있는 지침의 수준을 제공합니다.

```
SELECT  rp.[name]								AS [pool_name]
,       rp.[max_memory_kb]						AS [max_memory_kb]
,       rp.[max_memory_kb]/1024					AS [max_memory_mb]
,       rp.[max_memory_kb]/1048576				AS [mex_memory_gb]
,       rp.[max_memory_percent]					AS [max_memory_percent]
,       wg.[name]								AS [group_name]
,       wg.[importance]							AS [group_importance]
,       wg.[request_max_memory_grant_percent]	AS [request_max_memory_grant_percent]
FROM    sys.dm_pdw_nodes_resource_governor_workload_groups	wg
JOIN    sys.dm_pdw_nodes_resource_governor_resource_pools	rp ON wg.[pool_id] = rp.[pool_id]
WHERE   wg.[name] like 'SloDWGroup%'
AND     rp.[name]    = 'SloDWPool'
```

> [AZURE.NOTE]초대형 리소스 클래스에서 제공하는 메모리를 초과하는 파티션의 크기는 피합니다. 이 그림을 초과하여 파티션을 확대하는 경우, 최적의 압축 상태가 아닌 메모리 부족의 위험이 있습니다.

## 파티션 전환
두 테이블 간에 파티션을 전환하려면 파티션을 각 해당 경계에 맞추고 테이블 정의와 일치하는지 확인해야 합니다. Check 제약 조건은 테이블에 있는 값의 범위를 적용하는 데 사용할 수 없으므로 원본 테이블은 대상 테이블과 동일한 파티션 경계를 포함해야 합니다. 이 경우가 아닌 경우 파티션 전환은 파티션 메타데이터가 동기화되지 않아 실패합니다.

### 데이터를 포함하는 파티션을 분할하는 방법
데이터가 이미 들어 있는 파티션을 분할 하는 가장 효율적인 방법은 `CTAS` 문을 사용하는 것입니다. 분할된 테이블이 CCL(clustered columnstore)인 경우 테이블 파티션이 비어 있어야 분할될 수 있습니다.

다음은 최종 파티션에 하나의 행을 포함하는 샘플 분할된 columnstore 테이블입니다.

```
CREATE TABLE [dbo].[FactInternetSales]
(
        [ProductKey]            int          NOT NULL
    ,   [OrderDateKey]          int          NOT NULL
    ,   [CustomerKey]           int          NOT NULL
    ,   [PromotionKey]          int          NOT NULL
    ,   [SalesOrderNumber]      nvarchar(20) NOT NULL
    ,   [OrderQuantity]         smallint     NOT NULL
    ,   [UnitPrice]             money        NOT NULL
    ,   [SalesAmount]           money        NOT NULL
)
WITH
(   CLUSTERED COLUMNSTORE INDEX
,   DISTRIBUTION = HASH([ProductKey])
,   PARTITION   (   [OrderDateKey] RANGE RIGHT FOR VALUES
                    (20000101
                    )
                )
)
;

INSERT INTO dbo.FactInternetSales
VALUES (1,20010101,1,1,1,1,1,1)

CREATE STATISTICS Stat_dbo_FactInternetSales_OrderDateKey ON dbo.FactInternetSales(OrderDateKey)
```

> [AZURE.NOTE]통계 개체를 만들어 해당 테이블 메타데이터가 보다 정확함을 확인합니다. 통계 작성을 생략하는 경우 SQL 데이터 웨어하우스는 기본값을 사용합니다. 통계에 대한 자세한 내용은 [통계][]를 검토하세요.

`sys.partitions` 카탈로그 뷰를 사용하여 행 개수에 대해 쿼리할 수 있습니다.

```
SELECT  QUOTENAME(s.[name])+'.'+QUOTENAME(t.[name]) as Table_name
,       i.[name] as Index_name
,       p.partition_number as Partition_nmbr
,       p.[rows] as Row_count
,       p.[data_compression_desc] as Data_Compression_desc
FROM    sys.partitions p
JOIN    sys.tables     t    ON    p.[object_id]   = t.[object_id]
JOIN    sys.schemas    s    ON    t.[schema_id]   = s.[schema_id]
JOIN    sys.indexes    i    ON    p.[object_id]   = i.[object_Id]
                            AND   p.[index_Id]    = i.[index_Id]
WHERE t.[name] = 'FactInternetSales'
```

이 테이블을 분할하는 경우 오류가 발생합니다.

```
ALTER TABLE FactInternetSales SPLIT RANGE (20020101)
```

파티션이 비어 있어 Msg 35346, 수준 15, 상태 1, 44행 ALTER PARTITION 문의 SPLIT 절이 실패했습니다. Columnstore 인덱스가 테이블에 있는 경우 빈 파티션만 분할할 수 있습니다. ALTER PARTITION 문을 실행한 다음 ALTER PARTITION 완료된 후에 Columnstore 인덱스를 다시 작성하기 전에 Columnstore 인덱스를 비활성화하는 것이 좋습니다.

그러나 `CTAS`를 사용하여 데이터를 저장할 새 테이블을 만듭니다.

```
CREATE TABLE dbo.FactInternetSales_20010101
    WITH    (   DISTRIBUTION = HASH(ProductKey)
            ,   CLUSTERED COLUMNSTORE INDEX
            ,   PARTITION   (   [OrderDateKey] RANGE RIGHT FOR VALUES
                                (20010101
                                )
                            )
            )
AS
SELECT *
FROM	FactInternetSales
WHERE	1=2
```

파티션 경계가 정렬되므로 전환이 허용됩니다. 이후에 나눌 수 있는 빈 파티션이 있는 원본 테이블이 남습니다.

```
ALTER TABLE FactInternetSales SWITCH PARTITION 2 TO  FactInternetSales_20010101 PARTITION 2

ALTER TABLE FactInternetSales SPLIT RANGE (20020101)
```

`CTAS`를 사용하여 새 파티션 경계에 데이터를 정렬하고 데이터를 다시 기본 테이블로 전환하는 것이 남았습니다.

```
CREATE TABLE [dbo].[FactInternetSales_20010101_20020101]
    WITH    (   DISTRIBUTION = HASH([ProductKey])
            ,   CLUSTERED COLUMNSTORE INDEX
            ,   PARTITION   (   [OrderDateKey] RANGE RIGHT FOR VALUES
                                (20010101,20020101
                                )
                            )
            )
AS
SELECT  *
FROM	[dbo].[FactInternetSales_20010101]
WHERE	[OrderDateKey] >= 20010101
AND     [OrderDateKey] <  20020101

ALTER TABLE FactInternetSales_20010101_20020101 SWITCH PARTITION 3 TO  FactInternetSales PARTITION 3
```

데이터의 이동을 완료한 후에 각 분할에 있는 데이터의 새 배포를 정확히 반영되도록 대상 테이블에서 통계를 새로 고치는 것이 좋습니다.

```
UPDATE STATISTICS [dbo].[FactInternetSales]
```

### 소스 제어를 분할하는 테이블
소스 제어 시스템에서 사용자 테이블 정의가 **녹슬지** 않도록 다음 방법을 고려하는 것이 좋습니다.

1. 파티션 값이 없는 분할된 테이블로 테이블을 만듭니다.

```
CREATE TABLE [dbo].[FactInternetSales]
(
    [ProductKey]            int          NOT NULL
,   [OrderDateKey]          int          NOT NULL
,   [CustomerKey]           int          NOT NULL
,   [PromotionKey]          int          NOT NULL
,   [SalesOrderNumber]      nvarchar(20) NOT NULL
,   [OrderQuantity]         smallint     NOT NULL
,   [UnitPrice]             money        NOT NULL
,   [SalesAmount]           money        NOT NULL
)
WITH
(   CLUSTERED COLUMNSTORE INDEX
,   DISTRIBUTION = HASH([ProductKey])
,   PARTITION   (   [OrderDateKey] RANGE RIGHT FOR VALUES
                    ()
                )
)
;
```

2. `SPLIT` 배포 프로세스의 일부로서의 테이블:

```
-- Create a table containing the partition boundaries

CREATE TABLE #partitions
WITH
(
    LOCATION = USER_DB
,   DISTRIBUTION = HASH(ptn_no)
)
AS
SELECT  ptn_no
,       ROW_NUMBER() OVER (ORDER BY (ptn_no)) as seq_no
FROM    (
        SELECT CAST(20000101 AS INT) ptn_no
        UNION ALL
        SELECT CAST(20010101 AS INT)
        UNION ALL
        SELECT CAST(20020101 AS INT)
        UNION ALL
        SELECT CAST(20030101 AS INT)
        UNION ALL
        SELECT CAST(20040101 AS INT)
        ) a
;

-- Iterate over the partition boundaries and split the table

DECLARE @c INT = (SELECT COUNT(*) FROM #partitions)
,       @i INT = 1                     --iterator for while loop
,       @q NVARCHAR(4000)              --query
,       @p NVARCHAR(20)     = N''      --partition_number
,       @s NVARCHAR(128)    = N'dbo'   --schema
,       @t NVARCHAR(128)    = N'table' --table
;

WHILE @i <= @c
BEGIN
    SET @p = (SELECT ptn_no FROM #partitions WHERE seq_no = @i);
    SET @q = (SELECT N'ALTER TABLE '+@s+N'.'+@t+N' SPLIT RANGE ('+@p+N');');

    -- PRINT @q;
    EXECUTE sp_executesql @q;

    SET @i+=1;
END

-- Code clean-up

DROP TABLE #partitions;
```

이 방법으로 소스 제어의 코드는 정적으로 유지되며 파티션 경계 값은 동적이 될 수 있습니다. 시간이 지남에 따라 웨어하우스가 발전됩니다.

>[AZURE.NOTE]파티션 전환은 SQL Server에 비해 몇 가지 차이점이 있습니다. 이 주제에 대해 자세히 알아보려면 [코드 마이그레이션][]를 읽어 봅니다.


## 다음 단계
SQL 데이터 웨어하우스로 데이터베이스 스키마를 성공적으로 마이그레이션한 후에 다음 문서 중 하나를 진행할 수 있습니다.

- [데이터 마이그레이션][]
- [코드 마이그레이션][]

<!--Image references-->

<!-- Article references -->
[코드 마이그레이션]: sql-data-warehouse-migrate-code.md
[데이터 마이그레이션]: sql-data-warehouse-migrate-data.md
[통계]: sql-data-warehouse-develop-statistics.md
[워크로드 관리]: sql-data-warehouse-develop-concurrency.md

<!-- MSDN Articles -->

<!-- Other web references -->

<!---HONumber=August15_HO9-->