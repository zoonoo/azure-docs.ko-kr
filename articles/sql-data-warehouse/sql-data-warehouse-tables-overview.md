---
title: "SQL Data Warehouse의 테이블 개요 | Microsoft Docs"
description: "Azure SQL Data Warehouse 테이블로 시작"
services: sql-data-warehouse
documentationcenter: NA
author: barbkess
manager: jenniehubbard
editor: 
ms.assetid: 2114d9ad-c113-43da-859f-419d72604bdf
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: tables
ms.date: 12/06/2017
ms.author: barbkess
ms.openlocfilehash: d736ad0dbfb109500a150d2bc3f7c40b16206fdb
ms.sourcegitcommit: cc03e42cffdec775515f489fa8e02edd35fd83dc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/07/2017
---
# <a name="overview-of-tables-in-sql-data-warehouse"></a>SQL Data Warehouse의 테이블 개요
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

SQL Data Warehouse에서 테이블을 만드는 과정은 간단합니다.  기본 [CREATE TABLE][CREATE TABLE] 구문은 다른 데이터베이스를 사용하면서 가장 익숙해졌을 수 있는 일반적인 구문을 따릅니다.  테이블을 만들려면 테이블 및 열에 이름을 지정하고 각 열의 데이터 형식을 정의하면 됩니다.  다른 데이터베이스에서 테이블을 작성했으므로 이 작업은 아주 익숙해 보일 수도 있습니다.

```sql  
CREATE TABLE Customers (FirstName VARCHAR(25), LastName VARCHAR(25))
 ``` 

위의 예제에서는 FirstName 및 LastName의 두 열을 가진 Customers라는 테이블을 만듭니다.  각 열은 VARCHAR(25) 데이터 형식으로 정의됩니다. 이 형식에서는 데이터가 25자로 제한됩니다.  이러한 기본적인 테이블 특성 및 기타 특성은 다른 데이터베이스와 거의 동일합니다.  데이터 형식이 각 열에 대해 정의되어 데이터 무결성을 보장합니다.  I/O를 줄임으로써 성능 향상을 위해 인덱스를 추가할 수 있습니다.  데이터를 수정해야 하는 경우 성능 향상을 위해 분할을 추가할 수 있습니다.

SQL Data Warehouse 테이블 [이름 바꾸기][RENAME] 작업은 다음과 같습니다.

```sql  
RENAME OBJECT Customer TO CustomerOrig; 
 ```

## <a name="distributed-tables"></a>분산 테이블
SQL Data Warehouse와 같은 분산 시스템에 의해 도입된 새로운 기본 특성은 **배포 열**입니다.  배포 열은 표현 그대로입니다.  백그라운드에서 데이터를 분산 또는 분할하는 방법을 결정하는 열입니다.  배포 열을 지정하지 않고 테이블을 만들면 테이블이 **라운드 로빈**을 사용하여 자동으로 분산됩니다.  일부 시나리오에서는 라운드 로빈 테이블로 충분할 수 있지만 배포 열을 정의하면 쿼리 중에 데이터 이동을 크게 줄일 수 있으므로 성능이 최적화됩니다.  테이블에 적은 양의 데이터가 있는 경우 **복제** 배포 유형을 사용하여 테이블을 만들도록 선택하면 데이터가 각 계산 노드로 복사되므로 쿼리 실행 시에 데이터 이동 작업이 줄어듭니다. 배포 열을 선택하는 방법에 대한 자세한 내용을 [테이블 배포][Distribute] 를 참조하세요.

## <a name="indexing-and-partitioning-tables"></a>테이블 인덱싱 및 분할
더욱 수준 높은 방식으로 SQL Data Warehouse를 사용하고 성능을 최적화하기 위해 테이블 디자인에 대해 알아보려고 할 수 있습니다.  자세히 알아보려면 [테이블 데이터 형식][Data Types], [테이블 배포][Distribute], [테이블 인덱싱][Index], [테이블 분할][Partition]에 대한 문서를 참조하세요.

## <a name="table-statistics"></a>테이블 통계
통계는 SQL Data Warehouse의 성능을 극대화하는 데 매우 중요합니다.  Azure SQL Database에서 예상할 수 있는 것처럼 SQL Data Warehouse는 통계를 아직 자동으로 만들고 업데이트하지 않으므로 쿼리의 성능을 극대화하기 위해 [통계][Statistics]에 대한 문서를 반드시 읽어야 합니다.

## <a name="temporary-tables"></a>임시 테이블
임시 테이블은 로그온 중에만 존재하고 다른 사용자는 볼 수 없는 테이블입니다.  임시 테이블은 다른 사람들이 일시적인 결과를 보지 못하도록 하여 정리할 필요를 없애주는 유용한 방법일 수 있습니다.  임시 테이블은 로컬 저장소를 활용하므로 일부 작업에 대해 더 빠른 성능을 제공할 수 있습니다.  임시 테이블에 대한 자세한 내용은 [임시 테이블][Temporary] 문서를 참조하세요.

## <a name="external-tables"></a>외부 테이블
Polybase 테이블이라고도 하는 외부 테이블은 SQL Data Warehouse에서 쿼리될 수 있지만 SQL Data Warehouse 외부의 데이터를 가리킬 수 있는 테이블입니다.  예를 들어 Azure Blob Storage의 파일을 가리키는 외부 테이블을 만들 수 있습니다.  외부 테이블을 만들고 쿼리하는 방법에 대한 자세한 내용은 [Polybase 사용하여 데이터 로드][Load data with Polybase]를 참조하세요.  

## <a name="unsupported-table-features"></a>지원되지 않는 테이블 기능
SQL Data Warehouse에는 다른 데이터베이스에서 제공하는 동일한 테이블 기능을 많이 포함하지만 아직 지원되지 않는 기능도 일부 있습니다.  다음은 아직 지원되지 않는 일부 테이블 기능 목록입니다.

| 지원되지 않는 기능 |
| --- |
| 기본 키, 외래 키, 고유 및 검사 [테이블 제약 조건][Table Constraints] |
| [고유 인덱스][Unique Indexes] |
| [계산된 열][Computed Columns] |
| [스파스 열][Sparse Columns] |
| [사용자 정의 형식][User-Defined Types] |
| [시퀀스][Sequence] |
| [트리거][Triggers] |
| [인덱싱된 뷰][Indexed Views] |
| [동의어][Synonyms] |

## <a name="table-size-queries"></a>테이블 크기 쿼리
60개의 각 배포에서 한 테이블에 사용되는 공간 및 행을 식별하는 한 가지 간단한 방법은 [DBCC PDW_SHOWSPACEUSED][DBCC PDW_SHOWSPACEUSED]를 사용하는 것입니다.

```sql
DBCC PDW_SHOWSPACEUSED('dbo.FactInternetSales');
```

그러나 DBCC 명령을 사용하면 작업이 상당히 제한될 수 있습니다.  DMV(동적 관리 뷰)를 사용하면 훨씬 더 많은 세부 정보를 볼 수 있을 뿐만 아니라 쿼리 결과를 보다 강력하게 제어할 수 있습니다.  먼저 이 문서 및 다른 문서의 많은 예제에서 참조되는 이 뷰를 만들어보세요.

```sql
CREATE VIEW dbo.vTableSizes
AS
WITH base
AS
(
SELECT 
 GETDATE()                                                             AS  [execution_time]
, DB_NAME()                                                            AS  [database_name]
, s.name                                                               AS  [schema_name]
, t.name                                                               AS  [table_name]
, QUOTENAME(s.name)+'.'+QUOTENAME(t.name)                              AS  [two_part_name]
, nt.[name]                                                            AS  [node_table_name]
, ROW_NUMBER() OVER(PARTITION BY nt.[name] ORDER BY (SELECT NULL))     AS  [node_table_name_seq]
, tp.[distribution_policy_desc]                                        AS  [distribution_policy_name]
, c.[name]                                                             AS  [distribution_column]
, nt.[distribution_id]                                                 AS  [distribution_id]
, i.[type]                                                             AS  [index_type]
, i.[type_desc]                                                        AS  [index_type_desc]
, nt.[pdw_node_id]                                                     AS  [pdw_node_id]
, pn.[type]                                                            AS  [pdw_node_type]
, pn.[name]                                                            AS  [pdw_node_name]
, di.name                                                              AS  [dist_name]
, di.position                                                          AS  [dist_position]
, nps.[partition_number]                                               AS  [partition_nmbr]
, nps.[reserved_page_count]                                            AS  [reserved_space_page_count]
, nps.[reserved_page_count] - nps.[used_page_count]                    AS  [unused_space_page_count]
, nps.[in_row_data_page_count] 
    + nps.[row_overflow_used_page_count] 
    + nps.[lob_used_page_count]                                        AS  [data_space_page_count]
, nps.[reserved_page_count] 
 - (nps.[reserved_page_count] - nps.[used_page_count]) 
 - ([in_row_data_page_count] 
         + [row_overflow_used_page_count]+[lob_used_page_count])       AS  [index_space_page_count]
, nps.[row_count]                                                      AS  [row_count]
from 
    sys.schemas s
INNER JOIN sys.tables t
    ON s.[schema_id] = t.[schema_id]
INNER JOIN sys.indexes i
    ON  t.[object_id] = i.[object_id]
    AND i.[index_id] <= 1
INNER JOIN sys.pdw_table_distribution_properties tp
    ON t.[object_id] = tp.[object_id]
INNER JOIN sys.pdw_table_mappings tm
    ON t.[object_id] = tm.[object_id]
INNER JOIN sys.pdw_nodes_tables nt
    ON tm.[physical_name] = nt.[name]
INNER JOIN sys.dm_pdw_nodes pn
    ON  nt.[pdw_node_id] = pn.[pdw_node_id]
INNER JOIN sys.pdw_distributions di
    ON  nt.[distribution_id] = di.[distribution_id]
INNER JOIN sys.dm_pdw_nodes_db_partition_stats nps
    ON nt.[object_id] = nps.[object_id]
    AND nt.[pdw_node_id] = nps.[pdw_node_id]
    AND nt.[distribution_id] = nps.[distribution_id]
LEFT OUTER JOIN (select * from sys.pdw_column_distribution_properties where distribution_ordinal = 1) cdp
    ON t.[object_id] = cdp.[object_id]
LEFT OUTER JOIN sys.columns c
    ON cdp.[object_id] = c.[object_id]
    AND cdp.[column_id] = c.[column_id]
)
, size
AS
(
SELECT
   [execution_time]
,  [database_name]
,  [schema_name]
,  [table_name]
,  [two_part_name]
,  [node_table_name]
,  [node_table_name_seq]
,  [distribution_policy_name]
,  [distribution_column]
,  [distribution_id]
,  [index_type]
,  [index_type_desc]
,  [pdw_node_id]
,  [pdw_node_type]
,  [pdw_node_name]
,  [dist_name]
,  [dist_position]
,  [partition_nmbr]
,  [reserved_space_page_count]
,  [unused_space_page_count]
,  [data_space_page_count]
,  [index_space_page_count]
,  [row_count]
,  ([reserved_space_page_count] * 8.0)                                 AS [reserved_space_KB]
,  ([reserved_space_page_count] * 8.0)/1000                            AS [reserved_space_MB]
,  ([reserved_space_page_count] * 8.0)/1000000                         AS [reserved_space_GB]
,  ([reserved_space_page_count] * 8.0)/1000000000                      AS [reserved_space_TB]
,  ([unused_space_page_count]   * 8.0)                                 AS [unused_space_KB]
,  ([unused_space_page_count]   * 8.0)/1000                            AS [unused_space_MB]
,  ([unused_space_page_count]   * 8.0)/1000000                         AS [unused_space_GB]
,  ([unused_space_page_count]   * 8.0)/1000000000                      AS [unused_space_TB]
,  ([data_space_page_count]     * 8.0)                                 AS [data_space_KB]
,  ([data_space_page_count]     * 8.0)/1000                            AS [data_space_MB]
,  ([data_space_page_count]     * 8.0)/1000000                         AS [data_space_GB]
,  ([data_space_page_count]     * 8.0)/1000000000                      AS [data_space_TB]
,  ([index_space_page_count]  * 8.0)                                   AS [index_space_KB]
,  ([index_space_page_count]  * 8.0)/1000                              AS [index_space_MB]
,  ([index_space_page_count]  * 8.0)/1000000                           AS [index_space_GB]
,  ([index_space_page_count]  * 8.0)/1000000000                        AS [index_space_TB]
FROM base
)
SELECT * 
FROM size
;
```

### <a name="table-space-summary"></a>테이블 공간 요약
이 쿼리는 테이블에 의해 행 및 공간을 반환합니다.  가장 큰 테이블이 어느 테이블인지 그리고 해당 테이블이 라운드 로빈인지, 복제되는지 또는 해시 분산되는지 확인하는 것이 좋은 쿼리입니다.  해시 분산 테이블의 경우 배포 열도 보여 줍니다.  대부분의 경우 가장 큰 테이블은 클러스터된 columnstore 인덱스로 배포된 해시여야 합니다.

```sql
SELECT 
     database_name
,    schema_name
,    table_name
,    distribution_policy_name
,      distribution_column
,    index_type_desc
,    COUNT(distinct partition_nmbr) as nbr_partitions
,    SUM(row_count)                 as table_row_count
,    SUM(reserved_space_GB)         as table_reserved_space_GB
,    SUM(data_space_GB)             as table_data_space_GB
,    SUM(index_space_GB)            as table_index_space_GB
,    SUM(unused_space_GB)           as table_unused_space_GB
FROM 
    dbo.vTableSizes
GROUP BY 
     database_name
,    schema_name
,    table_name
,    distribution_policy_name
,      distribution_column
,    index_type_desc
ORDER BY
    table_reserved_space_GB desc
;
```

### <a name="table-space-by-distribution-type"></a>배포 유형별 테이블 공간
```sql
SELECT 
     distribution_policy_name
,    SUM(row_count)                as table_type_row_count
,    SUM(reserved_space_GB)        as table_type_reserved_space_GB
,    SUM(data_space_GB)            as table_type_data_space_GB
,    SUM(index_space_GB)           as table_type_index_space_GB
,    SUM(unused_space_GB)          as table_type_unused_space_GB
FROM dbo.vTableSizes
GROUP BY distribution_policy_name
;
```

### <a name="table-space-by-index-type"></a>인덱스 유형별 테이블 공간
```sql
SELECT 
     index_type_desc
,    SUM(row_count)                as table_type_row_count
,    SUM(reserved_space_GB)        as table_type_reserved_space_GB
,    SUM(data_space_GB)            as table_type_data_space_GB
,    SUM(index_space_GB)           as table_type_index_space_GB
,    SUM(unused_space_GB)          as table_type_unused_space_GB
FROM dbo.vTableSizes
GROUP BY index_type_desc
;
```

### <a name="distribution-space-summary"></a>배포 공간 요약
```sql
SELECT 
    distribution_id
,    SUM(row_count)                as total_node_distribution_row_count
,    SUM(reserved_space_MB)        as total_node_distribution_reserved_space_MB
,    SUM(data_space_MB)            as total_node_distribution_data_space_MB
,    SUM(index_space_MB)           as total_node_distribution_index_space_MB
,    SUM(unused_space_MB)          as total_node_distribution_unused_space_MB
FROM dbo.vTableSizes
GROUP BY     distribution_id
ORDER BY    distribution_id
;
```

## <a name="next-steps"></a>다음 단계
자세히 알아보려면 [테이블 데이터 형식][Data Types], [테이블 배포][Distribute], [테이블 인덱싱][Index],  [테이블 분할][Partition], [테이블 통계 유지 관리][Statistics] 및 [임시 테이블][Temporary]에 대한 문서를 참조하세요.  모범 사례에 대한 자세한 내용은 [SQL Data Warehouse 모범 사례][SQL Data Warehouse Best Practices]를 참조하세요.

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
[Load data with Polybase]: ./sql-data-warehouse-load-from-azure-blob-storage-with-polybase.md

<!--MSDN references-->
[CREATE TABLE]: https://msdn.microsoft.com/library/mt203953.aspx
[RENAME]: https://msdn.microsoft.com/library/mt631611.aspx
[DBCC PDW_SHOWSPACEUSED]: https://msdn.microsoft.com/library/mt204028.aspx
[Table Constraints]: https://msdn.microsoft.com/library/ms188066.aspx
[Computed Columns]: https://msdn.microsoft.com/library/ms186241.aspx
[Sparse Columns]: https://msdn.microsoft.com/library/cc280604.aspx
[User-Defined Types]: https://msdn.microsoft.com/library/ms131694.aspx
[Sequence]: https://msdn.microsoft.com/library/ff878091.aspx
[Triggers]: https://msdn.microsoft.com/library/ms189799.aspx
[Indexed Views]: https://msdn.microsoft.com/library/ms191432.aspx
[Synonyms]: https://msdn.microsoft.com/library/ms177544.aspx
[Unique Indexes]: https://msdn.microsoft.com/library/ms188783.aspx

<!--Other Web references-->
