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
파티션 크기 조정은 SQL 데이터 웨어하우스에 대한 중요한 고려 사항입니다. 일반적으로 데이터 관리 작업 및 데이터 로딩 루틴은 한 번에 전체 테이블을 상대하기 위한 것이 아니라 개별 파티션을 대상으로 합니다. 이는 특히 CCI(clustered columnstores)와 관련됩니다. CCI는 상당한 양의 메모리를 소비할 수 있습니다. 따라서 분할 계획의 세분성을 수정하는 동안에는 관리 작업을 수행할 때 메모리가 부족하게 되는 크기로 파티션의 크기를 조정하지 않을 수 있습니다.

파티션의 세분성을 결정할 때 SQL 데이터 웨어하우스는 데이터를 배포로 자동으로 분산함을 기억하는 것이 중요합니다. 따라서 보통 SQL Server 데이터베이스의 한 파티션에 있는 한 테이블에 존재하는 데이터가 이제는 SQL 데이터 웨어하우스 데이터베이스의 여러 테이블에서 하나의 파티션에 존재합니다. 각 파티션에 의미 있는 숫자의 행을 유지하기 위해 일반적으로 파티션 경계 크기를 변경합니다. 예를 들어, 데이터 웨어하우스에 대해 일 수준의 분할을 사용한 경우 월 또는 분기와 같이 덜 세분화된 수준을 고려할 수 있습니다.

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
두 테이블 간에 파티션을 전환하려면 파티션을 각 해당 경계에 맞추고 테이블 정의와 일치하는지 확인해야 합니다. Check 제약 조건은 테이블에 있는 값의 범위를 적용하는 데 사용할 수 없으므로 원본 테이블은 대상 테이블과 동일한 파티션 경계를 포함해야 합니다. 이 경우가 아닌 경우 파티션 전환은 파티션 메타 데이터가 동기화되지 않아 실패합니다.

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

<!---HONumber=July15_HO4-->