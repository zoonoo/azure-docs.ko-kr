<properties
   pageTitle="테이블 분산 오차 관리 | Microsoft Azure"
   description="사용자가 분산 테이블의 분산 오차를 식별하는 데 도움이 되는 지침"
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
   ms.date="03/23/2016"
   ms.author="jrj;barbkess;sonyama"/>

# Columnstore 인덱스 관리
Columnstore 인덱스는 Azure SQL 데이터 웨어하우스의 핵심입니다. 인덱스를 최적의 상태로 유지하면 시스템 성능이 극대화됩니다.

이 문서에서는 테이블의 columnstore 인덱스 메타데이터를 확인하여 문제를 진단하고 신속하게 해결하는 방법을 설명합니다.

## Columnstore 메타데이터 쿼리
columnstore 인덱스의 밀도를 파악하려면 시스템 메타데이터에 대한 쿼리가 필요합니다. 다음은 확인할 수 있는 정보의 종류입니다.

```sql
CREATE VIEW dbo.vColumnstoreDensity
AS
WITH CSI
AS
(
SELECT
        SUBSTRING(@@version,34,4)                                               AS [build_number]
,       GETDATE()                                                               AS [execution_date]
,       DB_Name()                                                               AS [database_name]
,       t.name                                                                  AS [table_name]
,		COUNT(DISTINCT rg.[partition_number])									AS [table_partition_count]
,       SUM(rg.[total_rows])                                                    AS [row_count_total]
,       SUM(rg.[total_rows])/COUNT(DISTINCT rg.[distribution_id])               AS [row_count_per_distribution_MAX]
,		CEILING	(	(SUM(rg.[total_rows])*1.0/COUNT(DISTINCT rg.[distribution_id])
						)/1048576
				)																AS [rowgroup_per_distribution_MAX]
,       SUM(CASE WHEN rg.[State] = 1 THEN 1                   ELSE 0    END)    AS [OPEN_rowgroup_count]
,       SUM(CASE WHEN rg.[State] = 1 THEN rg.[total_rows]     ELSE 0    END)    AS [OPEN_rowgroup_rows]
,       MIN(CASE WHEN rg.[State] = 1 THEN rg.[total_rows]     ELSE NULL END)    AS [OPEN_rowgroup_rows_MIN]
,       MAX(CASE WHEN rg.[State] = 1 THEN rg.[total_rows]     ELSE NULL END)    AS [OPEN_rowgroup_rows_MAX]
,       AVG(CASE WHEN rg.[State] = 1 THEN rg.[total_rows]     ELSE NULL END)    AS [OPEN_rowgroup_rows_AVG]
,       SUM(CASE WHEN rg.[State] = 2 THEN 1                   ELSE 0    END)    AS [CLOSED_rowgroup_count]
,       SUM(CASE WHEN rg.[State] = 2 THEN rg.[total_rows]     ELSE 0    END)    AS [CLOSED_rowgroup_rows]
,       MIN(CASE WHEN rg.[State] = 2 THEN rg.[total_rows]     ELSE NULL END)    AS [CLOSED_rowgroup_rows_MIN]
,       MAX(CASE WHEN rg.[State] = 2 THEN rg.[total_rows]     ELSE NULL END)    AS [CLOSED_rowgroup_rows_MAX]
,       AVG(CASE WHEN rg.[State] = 2 THEN rg.[total_rows]     ELSE NULL END)    AS [CLOSED_rowgroup_rows_AVG]
,       SUM(CASE WHEN rg.[State] = 3 THEN 1                   ELSE 0    END)    AS [COMPRESSED_rowgroup_count]
,       SUM(CASE WHEN rg.[State] = 3 THEN rg.[total_rows]     ELSE 0    END)    AS [COMPRESSED_rowgroup_rows]
,       SUM(CASE WHEN rg.[State] = 3 THEN rg.[deleted_rows]   ELSE 0    END)    AS [COMPRESSED_rowgroup_rows_DELETED]
,       MIN(CASE WHEN rg.[State] = 3 THEN rg.[total_rows]     ELSE NULL END)    AS [COMPRESSED_rowgroup_rows_MIN]
,       MAX(CASE WHEN rg.[State] = 3 THEN rg.[total_rows]     ELSE NULL END)    AS [COMPRESSED_rowgroup_rows_MAX]
,       AVG(CASE WHEN rg.[State] = 3 THEN rg.[total_rows]     ELSE NULL END)    AS [COMPRESSED_rowgroup_rows_AVG]
FROM    sys.[pdw_nodes_column_store_row_groups] rg
JOIN    sys.[pdw_nodes_tables] nt                   ON  rg.[object_id]          = nt.[object_id]
                                                    AND rg.[pdw_node_id]        = nt.[pdw_node_id]
                                                    AND rg.[distribution_id]    = nt.[distribution_id]
JOIN    sys.[pdw_table_mappings] mp                 ON  nt.[name]               = mp.[physical_name]
JOIN    sys.[tables] t                              ON  mp.[object_id]          = t.[object_id]
GROUP BY
        t.[name]
)
SELECT  *
FROM    CSI
;
```

뷰가 만들어지면 columnstore 메타데이터를 쉽게 분석할 수 있습니다. 아래는 예제 쿼리입니다.

```sql
SELECT	[table_name]
,		[table_partition_count]
,		[row_count_total]
,		[row_count_per_distribution_MAX]
,		[COMPRESSED_rowgroup_rows]
,		[COMPRESSED_rowgroup_rows_AVG]
,		[COMPRESSED_rowgroup_rows_DELETED]
,		[COMPRESSED_rowgroup_count]
,		[OPEN_rowgroup_count]
,		[OPEN_rowgroup_rows]
,		[CLOSED_rowgroup_count]
,		[CLOSED_rowgroup_rows]
FROM	[dbo].[vColumnstoreDensity]
WHERE	[table_name] = 'FactInternetSales'
```

## Columnstore 밀도 개선
쿼리를 실행했으면 데이터를 확인하고 결과를 분석할 수 있습니다.

여러 데이터를 살펴보아야 합니다.

| 열 | 이 데이터를 사용하는 방법 |
| ---------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [table\_partition\_count] | 테이블이 분할되어 있으면 열려 있는 행 그룹의 숫자가 더 많을 것입니다. 이론상으로는 분산의 각 파티션에 열려 있는 행 그룹이 연결될 수 있습니다. 분석할 때 이 점을 고려해야 합니다. 분할된 작은 테이블의 경우 압축 성능이 향상되도록 파티션을 모두 제거하여 최적화할 수 있습니다. |
| [row\_count\_total] | 테이블의 총 행 수. 예를 들어 이 값을 사용하여 압축 상태인 행의 비율을 계산할 수 있습니다. |
| [row\_count\_per\_distribution\_MAX] | 모든 행이 균등하게 분산되는 경우 이 값은 분산당 대상 행 수가 됩니다. 이 값을 compressed\_rowgroup\_count와 비교해 보세요. |
| [COMPRESSED\_rowgroup\_rows] | 테이블에서 columnstore 형식의 총 행 수 |
| [COMPRESSED\_rowgroup\_rows\_AVG] | 평균 행 수가 행 그룹의 최대 행 수보다 훨씬 적은 경우에는 CTAS 또는 ALTER INDEX REBUILD를 사용하여 데이터를 다시 압축하는 옵션을 고려해야 합니다. |
| [COMPRESSED\_rowgroup\_count] | columnstore 형식인 행 그룹의 수. 테이블에 비해 이 숫자가 매우 높으면 columnstore 밀도가 낮다는 뜻입니다. |
| [COMPRESSED\_rowgroup\_rows\_DELETED] | 행이 columnstore 형식으로 논리적으로 삭제됩니다. 테이블 크기에 비해 이 숫자가 더 높으면 행이 실제로 제거되도록 파티션을 다시 만들거나 인덱스를 다시 작성하는 옵션을 고려해 보아야 합니다. |
| [COMPRESSED\_rowgroup\_rows\_MIN] | 이 열을 AVG 및 MAX 열과 함께 사용하여 columnstore의 행 그룹에 대한 값 범위를 이해할 수 있습니다. 이 숫자가 로드 임계값(파티션 정렬 분산당 102,400)보다 낮으면 데이터 로드에서 최적화를 사용할 수 있습니다. |
| [COMPRESSED\_rowgroup\_rows\_MAX] | 위와 동일합니다. |
| [OPEN\_rowgroup\_count] | 열려 있는 행 그룹은 정상입니다. 테이블 분산당(60) 행 그룹이 하나 열려 있다고 예상하는 것이 합리적일 것입니다. 이 숫자가 과도하게 높으면 여러 파티션에 데이터를 로드한다는 뜻입니다. 이 숫자가 적절하게 유지되도록 분할 전략을 확인하세요. |
| [OPEN\_rowgroup\_rows] | 각 행 그룹은 최대 1,048,576개 행을 포함할 수 있습니다. 이 값을 사용하여 현재 열려 있는 행 그룹이 얼마나 찼는지 확인할 수 있습니다. |
| [OPEN\_rowgroup\_rows\_MIN] | 열려 있는 그룹은 데이터가 테이블로 서서히 로드되고 있거나 이전 부하가 나머지 행을 다 채우고 이 행 그룹까지 넘어 왔음을 뜻합니다. MIN, MAX, AVG 열을 사용하여 열려 있는 행 그룹에 데이터가 얼마나 있는지 확인할 수 있습니다. 작은 테이블의 경우 모든 데이터가 100%일 수 있습니다! 이 경우에는 ALTER INDEX REBUILD를 사용하여 데이터를 강제로 columnstore로 만들어야 합니다. |
| [OPEN\_rowgroup\_rows\_MAX] | 위와 동일합니다. |
| [OPEN\_rowgroup\_rows\_AVG] | 위와 동일합니다. |
| [CLOSED\_rowgroup\_rows] | 닫힌 행 그룹이 있는 경우 닫힌 행 그룹 행을 살펴보고 온전성을 확인하세요. |
| [CLOSED\_rowgroup\_count] | 닫힌 행 그룹이 있더라도 그 수가 적어야 합니다. ALTER INDEX ... REORGANISE 명령을 사용하여 닫힌 행 그룹을 압축된 행 그룹으로 변환할 수 있습니다. 그러나 일반적으로는 이 작업이 필요하지 않습니다. 닫힌 그룹은 백그라운드 "튜플 이동기" 프로세스를 통해 자동으로 columnstore 행 그룹으로 변환됩니다. |
| [CLOSED\_rowgroup\_rows\_MIN] | 닫힌 행 그룹의 채우기 속도가 매우 높아야 합니다. 닫힌 행 그룹의 채우기 속도가 낮으면 columnstore의 추가 분석이 필요합니다. |
| [CLOSED\_rowgroup\_rows\_MAX] | 위와 동일합니다. |
| [CLOSED\_rowgroup\_rows\_AVG] | 위와 동일합니다. |

## 인덱스 관리
[CTAS][]를 사용하여 파티션을 만들거나 [ALTER INDEX][]를 사용하여 인덱스 자체를 다시 작성하는 방법으로 행 그룹을 다시 압축할 수 있습니다. [CTAS][]는 일반적으로 [ALTER INDEX][]보다 작업 속도가 빠르며, 특히 대형 테이블 또는 파티션일 때 더욱 그렇습니다. 그러나 소형 테이블 또는 파티션인 경우에는 [ALTER INDEX][]가 훨씬 편리한 경우가 종종 있습니다.

>[AZURE.NOTE] ALTER INDEX...REBUILD는 오프라인 작업입니다. ALTER INDEX...REORGANISE는 온라인 작업입니다. 그러나 REORGANISE는 열려 있는 행 그룹을 건드리지 않습니다. 열려 있는 행 그룹을 포함하려면 ALTER INDEX...REBUILD가 필요합니다.

특히 대형 테이블의 경우 인덱스를 다시 작성하면 추가 리소스의 이점을 얻는 경우가 많습니다. Azure SQL 데이터 웨어하우스는 사용자에게 더 많은 메모리를 할당할 수 있는 워크로드 관리 기능을 제공합니다. 인덱스 재작성에 더 많은 메모리를 예약하는 방법은 [동시성][] 문서의 워크로드 관리 섹션을 참조하세요.

## 다음 단계
`CTAS`를 사용하여 파티션을 다시 만드는 자세한 방법은 다음 문서를 참조하세요.

* [테이블 분할][]
* [동시성][]

인덱스 관리에 대한 자세한 내용은 [인덱스 관리][] 문서를 참조하세요.

추가 관리 팁을 보려면 [관리][] 개요를 참조하세요.

<!--Image references-->

<!--Article references-->
[CTAS]: sql-data-warehouse-develop-ctas.md
[테이블 분할]: sql-data-warehouse-develop-table-partitions.md
[동시성]: sql-data-warehouse-develop-concurrency.md
[관리]: sql-data-warehouse-manage-monitor.md
[인덱스 관리]: sql-data-warehouse-manage-indexes.md

<!--MSDN references-->
[ALTER INDEX]: https://msdn.microsoft.com/ko-KR/library/ms188388.aspx


<!--Other Web references-->

<!---HONumber=AcomDC_0330_2016-->