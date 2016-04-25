<properties
   pageTitle="Azure SQL 데이터 웨어하우스의 분산 테이블에 대한 데이터 오차 관리 | Microsoft Azure"
   description="Azure SQL 데이터 웨어하우스에서 해시 분산 테이블의 모든 분산에 걸쳐 행이 불균일하게 분산된 경우 데이터 오차를 찾아서 해결합니다." 
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
   ms.date="04/07/2016"
   ms.author="jrj;barbkess;sonyama"/>

# Azure SQL 데이터 웨어하우스의 분산 테이블에 대한 데이터 오차 관리
이 자습서에서는 해시 분산 테이블의 데이터 오차를 식별하고 문제를 해결하기 위한 제안 사항을 제공합니다.

해시 분산 방법을 사용하여 테이블 데이터를 분산하는 경우 일부 분산에 불균형하게 다른 분산보다 데이터가 더 많은 오차가 발생할 수 있습니다. 분산 쿼리의 최종 결과는 가장 오래 실행되는 분산이 완료될 때까지 준비되지 않으므로 과도한 데이터 오차는 쿼리 성능에 영향을 줄 수 있습니다. 데이터 오차 정도에 따라 이 문제를 해결해야 할 수 있습니다.

이 자습서에서는 다음 작업을 수행합니다.

- 메타데이터를 사용하여 데이터 오차가 있는 테이블 확인
- 데이터 오차를 해결해야 하는 경우를 알기 위한 팁 학습
- 테이블을 다시 만들어 데이터 오차 해결


## 1단계: 데이터 오차를 찾는 뷰 만들기

다음 뷰를 만들어 데이터 오차가 있는 테이블을 식별합니다.

```sql
CREATE VIEW dbo.vDistributionSkew
AS
WITH base
AS
(
SELECT 
	SUBSTRING(@@version,34,4)															AS  [build_number]
,	GETDATE()																			AS  [execution_time]
,	DB_NAME()																			AS  [database_name]
,	s.name																				AS  [schema_name]
,	t.name																				AS  [table_name]
,	QUOTENAME(s.name)+'.'+QUOTENAME(t.name)												AS  [two_part_name]
,	nt.[name]																			AS  [node_table_name]
,	ROW_NUMBER() OVER(PARTITION BY nt.[name] ORDER BY (SELECT NULL))					AS  [node_table_name_seq]
,	tp.[distribution_policy_desc]														AS  [distribution_policy_name]
,	nt.[distribution_id]																AS  [distribution_id]
,	nt.[pdw_node_id]																	AS  [pdw_node_id]
,	pn.[type]																			AS	[pdw_node_type]
,	pn.[name]																			AS	[pdw_node_name]
,	nps.[partition_number]																AS	[partition_nmbr]
,	nps.[reserved_page_count]															AS	[reserved_space_page_count]
,	nps.[reserved_page_count] - nps.[used_page_count]									AS	[unused_space_page_count]
,	nps.[in_row_data_page_count] 
	+ nps.[row_overflow_used_page_count] + nps.[lob_used_page_count]					AS  [data_space_page_count]
,	nps.[reserved_page_count] 
	- (nps.[reserved_page_count] - nps.[used_page_count]) 
	- ([in_row_data_page_count]+[row_overflow_used_page_count]+[lob_used_page_count])	AS  [index_space_page_count]
,	nps.[row_count]																		AS  [row_count]
from sys.schemas s
join sys.tables t								ON	s.[schema_id]			= t.[schema_id]
join sys.pdw_table_distribution_properties	tp	ON	t.[object_id]			= tp.[object_id]
join sys.pdw_table_mappings tm					ON	t.[object_id]			= tm.[object_id]
join sys.pdw_nodes_tables nt					ON	tm.[physical_name]		= nt.[name]
join sys.dm_pdw_nodes pn 						ON  nt.[pdw_node_id]		= pn.[pdw_node_id]
join sys.dm_pdw_nodes_db_partition_stats nps	ON	nt.[object_id]			= nps.[object_id]
												AND nt.[pdw_node_id]		= nps.[pdw_node_id]
												AND nt.[distribution_id]	= nps.[distribution_id]
)
, size
AS
(
SELECT	[build_number]
,		[execution_time]
,		[database_name]
,		[schema_name]
,		[table_name]
,		[two_part_name]
,		[node_table_name]
,		[node_table_name_seq]
,		[distribution_policy_name]
,		[distribution_id]
,		[pdw_node_id]
,		[pdw_node_type]
,		[pdw_node_name]
,		[partition_nmbr]
,		[reserved_space_page_count]
,		[unused_space_page_count]
,		[data_space_page_count]
,		[index_space_page_count]
,		[row_count]
,		([reserved_space_page_count] * 8.0)				AS [reserved_space_KB]
,		([reserved_space_page_count] * 8.0)/1000		AS [reserved_space_MB]
,		([reserved_space_page_count] * 8.0)/1000000		AS [reserved_space_GB]
,		([reserved_space_page_count] * 8.0)/1000000000	AS [reserved_space_TB]
,		([unused_space_page_count]   * 8.0)				AS [unused_space_KB]
,		([unused_space_page_count]   * 8.0)/1000		AS [unused_space_MB]
,		([unused_space_page_count]   * 8.0)/1000000		AS [unused_space_GB]
,		([unused_space_page_count]   * 8.0)/1000000000	AS [unused_space_TB]
,		([data_space_page_count]     * 8.0)				AS [data_space_KB]
,		([data_space_page_count]     * 8.0)/1000		AS [data_space_MB]
,		([data_space_page_count]     * 8.0)/1000000		AS [data_space_GB]
,		([data_space_page_count]     * 8.0)/1000000000	AS [data_space_TB]
,		([index_space_page_count]	 * 8.0)				AS [index_space_KB]
,		([index_space_page_count]	 * 8.0)/1000		AS [index_space_MB]
,		([index_space_page_count]	 * 8.0)/1000000		AS [index_space_GB]
,		([index_space_page_count]	 * 8.0)/1000000000	AS [index_space_TB]
FROM	base
)
SELECT	* 
FROM	size
;
```

## 2단계: 뷰 쿼리

뷰를 만들었으므로 이제 다음 예제 쿼리를 실행하여 데이터 오차가 있는 테이블을 식별합니다.

```sql
SELECT	[two_part_name]
,		[distribution_id]
,		[row_count]
,		[reserved_space_GB]
,		[unused_space_GB]
,		[data_space_GB]
,		[index_space_GB]
FROM	[dbo].[vDistributionSkew]
WHERE	[table_name] = 'FactInternetSales'
ORDER BY [row_count] DESC
```

>[AZURE.NOTE] ROUND\_ROBIN 분산 테이블은 오차가 없어야 합니다. 기본적으로 노드 간에 데이터가 균일하게 분산됩니다.

## 3단계: 데이터 오차를 해결해야 하는지 결정

테이블의 데이터 오차를 해결해야 하는지 결정하려면 워크로드의 데이터 볼륨 및 쿼리를 최대한 이해해야 합니다.

데이터 분산은 데이터 오차 최소화와 데이터 이동 최소화 간의 적절한 균형을 찾는 문제입니다. 이는 반대되는 목표일 수 있으며, 경우에 따라 데이터 이동을 줄이기 위해 데이터 오차를 유지할 수 있습니다. 예를 들어 분산 열이 종종 조인 및 집계에서 공유 열인 경우 데이터 이동을 최소화하게 됩니다. 데이터 이동을 최소화할 경우의 이점은 데이터 오차의 영향을 능가할 수 있습니다.

## 4단계: 데이터 오차 해결

데이터 오차를 해결하는 방법에는 다음 두 가지가 있습니다. 오차를 해결해야 하는 것으로 결정한 경우에만 두 방법 중 하나를 사용하세요.

### 방법 1: 다른 분산 열이 있는 테이블 다시 만들기

데이터 오차를 해결하는 일반적인 방법은 다른 분산 열이 있는 테이블을 다시 만드는 것입니다. 해시 분산 열을 선택하는 방법에 대한 지침은 [해시 분산][]을 참조하세요. 이 예제에서는 [CTAS][]를 사용하여 다른 배포 열이 있는 테이블을 다시 만듭니다.

```sql
CREATE TABLE [dbo].[FactInternetSales_CustomerKey] 
WITH (  CLUSTERED COLUMNSTORE INDEX
     ,  DISTRIBUTION =  HASH([CustomerKey])
     ,  PARTITION       ( [OrderDateKey] RANGE RIGHT FOR VALUES (   20000101, 20010101, 20020101, 20030101
                                                                ,   20040101, 20050101, 20060101, 20070101
                                                                ,   20080101, 20090101, 20100101, 20110101
                                                                ,   20120101, 20130101, 20140101, 20150101
                                                                ,   20160101, 20170101, 20180101, 20190101
                                                                ,   20200101, 20210101, 20220101, 20230101
                                                                ,   20240101, 20250101, 20260101, 20270101
                                                                ,   20280101, 20290101
                                                                )
                        )
    )
AS
SELECT  *
FROM    [dbo].[FactInternetSales]
OPTION  (LABEL  = 'CTAS : FactInternetSales_CustomerKey')
;

--Rename the objects
RENAME OBJECT [dbo].[FactInternetSales] TO [FactInternetSales_ProductKey];
RENAME OBJECT [dbo].[FactInternetSales_CustomerKey] TO [FactInternetSales];
```

### 방법 2: 라운드 로빈 분산을 사용하여 테이블 다시 만들기

이 예제에서는 해시 배포 대신 라운드 로빈을 사용하여 테이블을 다시 만듭니다. 이 변경으로 인해 균일한 데이터 분산이 생성됩니다. 그러나 일반적으로 쿼리에 대한 데이터 이동이 증가합니다.

```sql
CREATE TABLE [dbo].[FactInternetSales_ROUND_ROBIN] 
WITH (  CLUSTERED COLUMNSTORE INDEX
     ,  DISTRIBUTION =  ROUND_ROBIN
     ,  PARTITION       ( [OrderDateKey] RANGE RIGHT FOR VALUES (   20000101, 20010101, 20020101, 20030101
                                                                ,   20040101, 20050101, 20060101, 20070101
                                                                ,   20080101, 20090101, 20100101, 20110101
                                                                ,   20120101, 20130101, 20140101, 20150101
                                                                ,   20160101, 20170101, 20180101, 20190101
                                                                ,   20200101, 20210101, 20220101, 20230101
                                                                ,   20240101, 20250101, 20260101, 20270101
                                                                ,   20280101, 20290101
                                                                )
                        )
    )
AS
SELECT  *
FROM    [dbo].[FactInternetSales]
OPTION  (LABEL  = 'CTAS : FactInternetSales_ROUND_ROBIN')
;

--Rename the objects
RENAME OBJECT [dbo].[FactInternetSales] TO [FactInternetSales_HASH];
RENAME OBJECT [dbo].[FactInternetSales_ROUND_ROBIN] TO [FactInternetSales];
```

## 다음 단계
테이블 분산에 대한 자세한 내용은 다음 문서를 참조하세요.

* [테이블 디자인][]
* [해시 배포][]

<!--Image references-->

<!--Article references-->
[테이블 디자인]: sql-data-warehouse-develop-table-design.md
[해시 배포]: sql-data-warehouse-develop-hash-distribution-key.md
[해시 분산]: sql-data-warehouse-develop-hash-distribution-key.md

<!--MSDN references-->

<!--Other Web references-->

<!---HONumber=AcomDC_0413_2016-->