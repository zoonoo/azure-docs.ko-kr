<properties
   pageTitle="Azure SQL 데이터 웨어하우스의 테이블 크기 관리 | Microsoft Azure"
   description="테이블 및 파티션 크기를 확인합니다." 
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
   ms.date="04/13/2016"
   ms.author="jrj;barbkess;sonyama"/>

# Azure SQL 데이터 웨어하우스의 테이블 크기 관리

이 문서에서는 테이블 파티션의 크기를 식별하고 데이터베이스 크기를 이해하는 데 도움을 줍니다.


## 1단계: 테이블 크기를 요약하는 뷰 만들기

다음 뷰를 만들어 데이터 오차가 있는 테이블을 식별합니다.

```sql
CREATE VIEW dbo.vPartitionSizing
AS

WITH base
AS
(
SELECT 
	SUBSTRING(@@version,34,4)																AS build_number
,	DB_NAME()																				AS database_name
,	s.name																					AS schema_name
,	t.name																					AS table_name
,	QUOTENAME(s.name)+'.'+QUOTENAME(t.name)													AS two_part_name
,	nt.name																					AS node_table_name
,   i.[type]                                                                                AS table_type
,   i.[type_desc]                                                                           AS table_type_desc
,	tp.distribution_policy_desc	                        									AS distribution_policy_name
,	pn.pdw_node_id                                                                          AS pdw_node_id
,	pn.[type]																				AS pdw_node_type
,	pn.name																					AS pdw_node_name
,   di.distribution_id                                                                      AS dist_id
,   di.name                                                                                 AS dist_name
,   di.position                                                                             AS dist_position
,	nps.partition_number																	AS partition_nmbr
,	nps.reserved_page_count																	AS reserved_space_page_count
,	nps.reserved_page_count - nps.used_page_count											AS unused_space_page_count
,	nps.in_row_data_page_count + nps.row_overflow_used_page_count + nps.lob_used_page_count	AS  data_space_page_count
,	nps.reserved_page_count 
	- (nps.reserved_page_count - nps.used_page_count) 
	- (in_row_data_page_count+row_overflow_used_page_count+lob_used_page_count)				AS index_space_page_count
,	nps.[row_count]                                                                         AS row_count
from sys.schemas s
join sys.tables t								ON	s.schema_id			= t.schema_id
JOIN sys.indexes i                              ON  t.object_id         = i.object_id
                                                AND i.index_id          <= 1
join sys.pdw_table_distribution_properties	tp	ON	t.object_id			= tp.object_id
join sys.pdw_table_mappings tm					ON	t.object_id			= tm.object_id
join sys.pdw_nodes_tables nt					ON	tm.physical_name	= nt.name
join sys.dm_pdw_nodes pn 						ON  nt.pdw_node_id		= pn.pdw_node_id
join sys.pdw_distributions di                   ON  nt.distribution_id  = di.distribution_id
join sys.dm_pdw_nodes_db_partition_stats nps	ON	nt.object_id		= nps.object_id
												AND nt.pdw_node_id		= nps.pdw_node_id
                                                AND nt.distribution_id  = nps.distribution_id
)
, size
AS
(
SELECT	build_number
,		database_name
,		schema_name
,		table_name
,		two_part_name
,		node_table_name
,       table_type
,       table_type_desc
,		distribution_policy_name
,       dist_id
,       dist_name
,       dist_position
,		pdw_node_id
,		pdw_node_type
,		pdw_node_name
,		partition_nmbr
,		reserved_space_page_count
,		unused_space_page_count
,		data_space_page_count
,		index_space_page_count
,		row_count
,		(reserved_space_page_count * 8.0)				AS reserved_space_KB
,		(reserved_space_page_count * 8.0)/1000			AS reserved_space_MB
,		(reserved_space_page_count * 8.0)/1000000		AS reserved_space_GB
,		(reserved_space_page_count * 8.0)/1000000000	AS reserved_space_TB
,		(unused_space_page_count * 8.0)					AS unused_space_KB
,		(unused_space_page_count * 8.0)/1000			AS unused_space_MB
,		(unused_space_page_count * 8.0)/1000000			AS unused_space_GB
,		(unused_space_page_count * 8.0)/1000000000		AS unused_space_TB
,		(data_space_page_count * 8.0)					AS data_space_KB
,		(data_space_page_count * 8.0)/1000				AS data_space_MB
,		(data_space_page_count * 8.0)/1000000			AS data_space_GB
,		(data_space_page_count * 8.0)/1000000000		AS data_space_TB
,		(index_space_page_count * 8.0)	    			AS index_space_KB
,		(index_space_page_count * 8.0)/1000				AS index_space_MB
,		(index_space_page_count * 8.0)/1000000			AS index_space_GB
,		(index_space_page_count * 8.0)/1000000000		AS index_space_TB
FROM	base
)
SELECT	* 
FROM	size
;
```

## 2단계: 뷰 쿼리

이제 Server를 만들었으면 다음 예제 쿼리를 실행하여 테이블 크기에 대해 자세히 알아봅니다.

### 데이터베이스에서 사용된 테이블 공간 요약

```sql
SELECT
	database_name
,	SUM(row_count)				as total_row_count
,	SUM(reserved_space_MB)		as total_reserved_space_MB
,	SUM(data_space_MB)			as total_data_space_MB
,	SUM(index_space_MB)			as total_index_space_MB
,	SUM(unused_space_MB)		as total_unused_space_MB
FROM dbo.vPartitionSizing
GROUP BY database_name
;
```

### 테이블별 사용된 테이블 공간

```sql
SELECT 
	two_part_name
,	SUM(row_count)				as table_row_count
,	SUM(reserved_space_GB)		as table_reserved_space_GB
,	SUM(data_space_GB)			as table_data_space_GB
,	SUM(index_space_GB)			as table_index_space_GB
,	SUM(unused_space_GB)		as table_unused_space_GB
FROM dbo.vPartitionSizing
GROUP BY two_part_name
;
```

### 기하 도형 테이블 형식별 테이블 공간

```sql
SELECT 
   table_type_desc
,	SUM(row_count)				as table_type_row_count
,	SUM(reserved_space_GB)		as table_type_reserved_space_GB
,	SUM(data_space_GB)			as table_type_data_space_GB
,	SUM(index_space_GB)			as table_type_index_space_GB
,	SUM(unused_space_GB)		as table_type_unused_space_GB
FROM dbo.vPartitionSizing
GROUP BY table_type_desc
;
```

### 배포별 테이블 공간

```sql
SELECT 
	pdw_node_id
,	distribution_policy_name
,	dist_name
,	SUM(row_count)				as total_node_distribution_row_count
,	SUM(reserved_space_MB)		as total_node_distribution_reserved_space_MB
,	SUM(data_space_MB)			as total_node_distribution_data_space_MB
,	SUM(index_space_MB)			as total_node_distribution_index_space_MB
,	SUM(unused_space_MB)		as total_node_distribution_unused_space_MB
FROM dbo.vPartitionSizing
GROUP BY 	distribution_policy_name
,			dist_name
,			pdw_node_id
ORDER BY    pdw_node_id
,           distribution_policy_name
,		    dist_name
;
```

## 3단계: 테이블 관리

더 이상 필요하지 않은 테이블이 있나요? 이러한 테이블을 삭제(drop)하거나 [CREATE EXTERNAL TABLE AS SELECT (CETAS)][]를 사용하여 Blob 저장소로 내보냅니다.

일부 테이블이 예상보다 큰가요? 분할하는 것이 좋을 수 있습니다. 자세한 내용은 [table partitions][]의 문서를 참조하세요.

데이터베이스가 구성된 최대 크기에 도달하고 있나요? [ALTER DATABASE][]를 사용하여 변경합니다.

## 다음 단계
테이블 분산에 대한 자세한 내용은 다음 문서를 참조하세요.

* [테이블 디자인][]
* [해시 배포][]

<!--Image references-->

<!--Article references-->
[테이블 디자인]: sql-data-warehouse-develop-table-design.md
[해시 배포]: sql-data-warehouse-develop-hash-distribution-key.md

<!--MSDN references-->

<!--Other Web references-->

<!---HONumber=AcomDC_0420_2016-->