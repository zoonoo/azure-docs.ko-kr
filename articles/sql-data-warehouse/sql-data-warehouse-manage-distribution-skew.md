<properties
   pageTitle="테이블 분산 오차 관리 | Microsoft Azure"
   description="분산 테이블의 분산 오차를 식별하는 데 도움이 되는 지침"
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
   ms.date="03/18/2016"
   ms.author="jrj;barbkess;sonyama"/>

# 테이블 분산 오차 관리
해시 분산 방법을 사용하여 테이블 데이터를 분산하는 경우는 데이터의 분산에 "오차"가 발생할 가능성이 있습니다.

일부 분산이 다른 분산보다 지나치게 많은 데이터를 포함하는 경우 이 테이블에 오차가 있다고 할 수 있습니다. 오차 정도에 따라 이 문제를 해결하는 것이 좋습니다. 과도한 데이터 오차가 발생하면 분산된 계산 리소스가 균등하게 사용되지 않으므로 쿼리 성능에 영향을 미치게 됩니다.

이 문서는 해시 분산 테이블에서 데이터 오차를 확인하는 데 도움을 주기 위해 작성되었습니다.

## 분산 오차 찾기

아래 뷰와 같은 쿼리는 오차가 있는 테이블을 식별하는 데 유용할 수 있습니다.

```
CREATE VIEW dbo.vDistributionSkew
AS
WITH base
AS
(
select 
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
,		([reserved_space_page_count] * 8.0)/1024		AS [reserved_space_MB]
,		([reserved_space_page_count] * 8.0)/1048576		AS [reserved_space_GB]
,		([reserved_space_page_count] * 8.0)/1073741824	AS [reserved_space_TB]
,		([unused_space_page_count]   * 8.0)				AS [unused_space_KB]
,		([unused_space_page_count]   * 8.0)/1024		AS [unused_space_MB]
,		([unused_space_page_count]   * 8.0)/1048576		AS [unused_space_GB]
,		([unused_space_page_count]   * 8.0)/1073741824	AS [unused_space_TB]
,		([data_space_page_count]     * 8.0)				AS [data_space_KB]
,		([data_space_page_count]     * 8.0)/1024		AS [data_space_MB]
,		([data_space_page_count]     * 8.0)/1048576		AS [data_space_GB]
,		([data_space_page_count]     * 8.0)/1073741824	AS [data_space_TB]
,		([index_space_page_count]	 * 8.0)				AS [index_space_KB]
,		([index_space_page_count]	 * 8.0)/1024		AS [index_space_MB]
,		([index_space_page_count]	 * 8.0)/1048576		AS [index_space_GB]
,		([index_space_page_count]	 * 8.0)/1073741824	AS [index_space_TB]
FROM	base
)
SELECT	* 
FROM	size
;
```

뷰가 만들어지면 아래와 같은 쿼리를 사용하여 테이블의 오차 부분이 유효한지 간단히 확인할 수 있습니다.

```
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

## 데이터 오차 해결
오차를 유지하는 것이 필요한 경우가 있습니다. 일반적으로 테이블이 공유 배포 키에서 조인되는 경우가 여기에 해당합니다.

그러나 데이터 오차를 해결하려면 일반적으로 다른 열을 선택합니다. HASH 대신 ROUND\_ROBIN을 사용할 수도 있습니다.

다른 열 선택에 대한 추가 지침을 보려면 [해시 분산][] 문서에서 권장 사항 섹션을 참조하세요.

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

<!---HONumber=AcomDC_0323_2016-->