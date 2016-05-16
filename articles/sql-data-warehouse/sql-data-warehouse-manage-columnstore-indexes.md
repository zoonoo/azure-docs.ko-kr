<properties
   pageTitle="Azure SQL 데이터 웨어하우스의 columnstore 인덱스 관리 | Microsoft Azure"
   description="Azure SQL 데이터 웨어하우스의 압축 및 쿼리 성능을 개선시키기 위한 columnstore 인덱스 관리 자습서입니다."
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
   ms.date="05/02/2016"
   ms.author="jrj;barbkess;sonyama"/>

# Azure SQL 데이터 웨어하우스의 columnstore 인덱스 관리

이 자습서에서는 쿼리 성능을 향상시키기 위한 columnstore 인덱스 관리 방법을 설명합니다.

columnstore 인덱스의 쿼리는 인덱스가 백만 개(정확히는 1048576)의 행을 "행 그룹"으로 함께 압축할 때 가장 잘 실행됩니다. 최고의 압축 및 최상의 쿼리 성능을 제공합니다. 그러나 행 그룹이 백만 개보다 훨씬 적은 행을 가지는 경우가 발생할 수 있습니다. 행 그룹이 행으로 조밀하게 채워지지 않는 경우 조정하는 것이 좋습니다.

이 자습서에서는 다음 방법에 대해 알아봅니다.

- 메타데이터를 사용하여 행 그룹당 평균 행 수 확인
- 밀도가 낮은 행 그룹에 대한 근본 원인 고려
- 모든 행을 새 행 그룹으로 다시 압축하기 위해 columnstore 인덱스 다시 빌드 

columnstore 인덱스에 대한 기본 사항을 알아 보려면 [Columnstore 가이드](https://msdn.microsoft.com/library/gg492088.aspx)를 참조하세요.

## 1단계: 행 그룹 메타데이터를 표시하는 뷰 만들기

이 뷰는 행 그룹당 평균 행을 계산합니다. 또한 행 그룹에 대한 추가 정보를 보여 줍니다.

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

## 2단계: 뷰 쿼리

뷰를 만들었으면 이제 columnstore 인덱스에 대한 행 그룹 메타데이터를 보기 위해 이 예제 쿼리를 실행합니다.

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

## 3단계: 결과 분석

쿼리를 실행했으면 데이터를 확인하고 결과를 분석할 수 있습니다. 다음 표는 행 그룹 분석에서 확인할 내용에 대해 설명합니다.


| 열 | 이 데이터를 사용하는 방법 |
| ---------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [table\_partition\_count] | 테이블이 분할되어 있으면 열려 있는 행 그룹의 숫자가 더 많을 것입니다. 이론상으로는 분산의 각 파티션에 열려 있는 행 그룹이 연결될 수 있습니다. 분석할 때 이 점을 고려해야 합니다. 분할된 작은 테이블의 경우 압축 성능이 향상되도록 파티션을 모두 제거하여 최적화할 수 있습니다. |
| [row\_count\_total] | 테이블에 대한 전체 행 개수입니다. 예를 들어 압축된 상태에서 행의 비율을 계산하는 데 이 값을 사용할 수 있습니다. |
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
| [CLOSED\_rowgroup\_rows] | 닫힌 행 그룹이 있는 경우 |
| [CLOSED\_rowgroup\_count] | 닫힌 행 그룹이 있더라도 그 수가 적어야 합니다. ALTER INDEX ... REORGANISE 명령을 사용하여 닫힌 행 그룹을 압축된 행 그룹으로 변환할 수 있습니다. 그러나 일반적으로는 이 작업이 필요하지 않습니다. 닫힌 그룹은 백그라운드 "튜플 이동기" 프로세스를 통해 자동으로 columnstore 행 그룹으로 변환됩니다. |
| [CLOSED\_rowgroup\_rows\_MIN] | 닫힌 행 그룹의 채우기 속도가 매우 높아야 합니다. 닫힌 행 그룹의 채우기 속도가 낮으면 columnstore의 추가 분석이 필요합니다. |
| [CLOSED\_rowgroup\_rows\_MAX] | 위와 동일합니다. |
| [CLOSED\_rowgroup\_rows\_AVG] | 위와 동일합니다. |


## 4단계: 근본 원인 검사

근본 원인을 검사하면 행 그룹에서 행 밀도를 개선하는 테이블 디자인, 로드 또는 기타 프로세스를 변경할 수 있어 압축 및 쿼리 성능을 개선할 수 있습니다.

다음 요소로 인해 columnstore 인덱스가 각 행 그룹당 1,048,576개보다 훨씬 적은 행을 가질 수 있으며 행이 압축된 행 그룹 대신 델타 행 그룹으로 이동할 수도 있습니다.

1. 많은 DML 작업
2. 작거나 지속적인 로드 작업
3. 너무 많은 파티션

### 많은 DML 작업** 

행을 업데이트 또는 삭제하는 많은 DML 작업은 columnstore에 비효율적입니다. 특히 행 그룹 내 대부분의 행이 수정될 때 더욱 그렇습니다.

- 압축 행 그룹에서 하나의 행을 삭제하는 것은 논리적으로 행을 삭제된 것으로 표시하는 것입니다. 이 행은 파티션 또는 테이블이 다시 빌드될 때까지 압축 행 그룹에 남아 있습니다.
- 행 삽입은 행을 델타 행 그룹이라 불리는 내부 rowstore 테이블에 추가합니다. 삽입된 행은 델타 행 그룹이 꽉 차서 닫힌 것으로 표시될 때까지 columnstore로 변환되지 않습니다. 행 그룹은 최대 용량인 1,048,576개 행에 도달하면 닫힙니다. 
- Columnstore 형식에서 행을 업데이트하면 논리적 삭제 후 삽입으로 처리됩니다. 삽입된 행은 델타 저장소에 저장됩니다.

파티션 정렬 배포당 102,400개 행의 대량 임계값을 초과하는 일괄 처리된 업데이트 및 삽입 작업은 columnstore 형식으로 직접 기록됩니다. 그러나 균일한 배포를 가정하여 한 작업에서 614만 4천 개 이상의 행을 수정해야 할 수 있습니다. 지정된 파티션 정렬 배포당 행 수가 102,400개보다 적은 경우 행이 델타 저장소로 이동하며 충분한 수의 행이 삽입될 때까지 또는 행 그룹을 닫도록 수정하거나 인덱스가 다시 빌드될 때까지 이곳에 유지됩니다.

### 작거나 지속적인 로드 작업

SQL 데이터 웨어하우스로 유입되는 작은 부하는 지속적인 부하라고도 합니다. 이들은 일반적으로 시스템에 의해 수집되는 거의 일정한 데이터 스트림을 나타냅니다. 그러나 이 스트림은 거의 지속적이므로 행의 볼륨은 특별히 크지 않습니다. 종종 데이터가 columnstore 형식으로 직접 로드하는 데 필요한 임계값보다 훨씬 큽니다.

이러한 상황에서는 데이터를 로드하기 전에 Azure Blob 저장소에 먼저 데이터를 배치하고 누적되도록 합니다. 이 기술을 *마이크로 일괄 처리*라고 합니다.

### 너무 많은 파티션

너무 많은 파티션이 있을 수 있습니다. MPP(대규모 병렬 처리) 아키텍처에서 하나의 사용자 정의 테이블은 내부적으로 60개의 테이블로 배포 및 구현됩니다. 따라서 모든 columnstore 인덱스는 60개의 columnstore 인덱스로 구현됩니다. 마찬가지로 모든 파티션이 이러한 60개의 columnstore 인덱스에서 구현됩니다. 이 점이 SMP(대칭 다중 처리) 아키텍처를 사용하는 SQL Server와의 **큰 차이점**입니다.

SMP SQL Server 테이블의 한 파티션에 1,048,576개 행을 로드하는 경우 columnstore로 압축됩니다. 그러나 SQL 데이터 웨어하우스 테이블의 한 파티션에 1,048,576개 행을 로드하는 경우 60개의 각 배포(균일한 데이터 배포 가정)에 17,467개 행만 배치됩니다. 17,467개 행은 배포당 임계값인 102,400개 행보다 적으므로 SQL 데이터 웨어하우스에서 데이터를 deltastore 행 그룹에 보유합니다. 따라서 행을 압축된 columnstore 형식으로 직접 로드하려면 614만 4천 4백 개(60x102,400) 이상의 행을 SQL 데이터 웨어하우스 테이블의 단일 파티션으로 로드해야 합니다.

## 5단계: 추가 계산 리소스 할당

특히 대형 테이블의 경우 인덱스를 다시 빌드하는 데 종종 추가 리소스가 필요합니다. SQL 데이터 웨어하우스는 사용자에게 더 많은 메모리를 할당할 수 있는 워크로드 관리 기능을 제공합니다. 인덱스 재작성에 더 많은 메모리를 예약하는 방법은 [동시성][] 문서의 워크로드 관리 섹션을 참조하세요.

## 6단계: 행 그룹당 평균 행 개수를 개선하기 위해 인덱스를 다시 빌드

기존의 압축 행 그룹을 병합하고 델타 행 그룹을 columnstore로 강제 지정하려면 인덱스를 다시 빌드해야 합니다. 일반적으로 전체 인덱스를 다시 빌드하기에는 너무 많은 데이터가 있으므로 하나 이상의 파티션을 다시 빌드하는 것이 가장 좋습니다. 다음의 두 가지 방법 중 하나로 SQL 데이터 웨어하우스에서 인덱스를 다시 빌드할 수 있습니다.

1. [ALTER INDEX][]를 사용하여 파티션을 다시 빌드합니다.
2. [CTAS][]를 사용하여 파티션을 새 테이블로 다시 만든 다음, 파티션 전환을 사용하여 파티션을 원래 테이블로 다시 이동합니다.

어떤 방식이 적합할까요? 데이터 양이 많은 경우 일반적으로 [CTAS][]가 [ALTER INDEX][]보다 빠릅니다. 데이터 양이 적은 경우 [ALTER INDEX][]가 더 사용하기 쉽습니다.

### 방법 #1: ALTER INDEX를 사용하여 오프라인으로 적은 양의 데이터를 다시 빌드

이 예제에서는 전체 columnstore 인덱스 및 단일 파티션을 다시 빌드하는 방법을 보여 줍니다. 대형 테이블에서는 단일 파티션을 다시 빌드하는 것이 실용적입니다. 이 작업은 오프라인 작업입니다.

```sql
-- Rebuild the entire clustered index
ALTER INDEX ALL ON [dbo].[DimProduct] REBUILD
```

```sql
-- Rebuild a single partition
ALTER INDEX ALL ON [dbo].[FactInternetSales] REBUILD Partition = 5
```

자세한 내용은 [Columnstore 인덱스 조각 모음](https://msdn.microsoft.com/library/dn935013.aspx#Anchor_1)의 ALTER INDEX REBUILD 섹션과 [ALTER INDEX(TRANSACT-SQL)](https://msdn.microsoft.com/library/ms188388.aspx) 구문 항목을 참조하세요.

### 방법 #2: CTAS를 사용하여 대용량 데이터를 온라인으로 다시 빌드하고 파티션 전환

이 예제에서는 [CTAS][]와 파티션 전환을 사용하여 테이블 파티션을 다시 빌드합니다.


```sql
-- Step 01. Select the partition of data and write it out to a new table using CTAS
CREATE TABLE [dbo].[FactInternetSales_20000101_20010101]
    WITH    (   DISTRIBUTION = HASH([ProductKey])
            ,   CLUSTERED COLUMNSTORE INDEX
            ,   PARTITION   (   [OrderDateKey] RANGE RIGHT FOR VALUES
                                (20000101,20010101
                                )
                            )
            )
AS
SELECT  *
FROM    [dbo].[FactInternetSales]
WHERE   [OrderDateKey] >= 20000101
AND     [OrderDateKey] <  20010101
;

-- Step 02. Create a SWITCH out table
 
CREATE TABLE dbo.FactInternetSales_20000101
    WITH    (   DISTRIBUTION = HASH(ProductKey)
            ,   CLUSTERED COLUMNSTORE INDEX
            ,   PARTITION   (   [OrderDateKey] RANGE RIGHT FOR VALUES
                                (20000101
                                )
                            )
            )
AS
SELECT *
FROM    [dbo].[FactInternetSales]
WHERE   1=2 -- Note this table will be empty

-- Step 03. Switch OUT the data 
ALTER TABLE [dbo].[FactInternetSales] SWITCH PARTITION 2 TO  [dbo].[FactInternetSales_20000101] PARTITION 2;

-- Step 04. Switch IN the rebuilt data
ALTER TABLE [dbo].[FactInternetSales_20000101_20010101] SWITCH PARTITION 2 TO  [dbo].[FactInternetSales] PARTITION 2;
```

`CTAS`를 사용하여 파티션을 다시 만드는 방법에 대한 자세한 내용은 [테이블 분할][] 및 [동시성][]을 참조하세요.


## 다음 단계

관리에 대한 추가 팁을 보려면 [관리][] 개요를 참조하세요.

<!--Image references-->

<!--Article references-->
[CTAS]: sql-data-warehouse-develop-ctas.md
[테이블 분할]: sql-data-warehouse-develop-table-partitions.md
[동시성]: sql-data-warehouse-develop-concurrency.md
[관리]: sql-data-warehouse-manage-monitor.md

<!--MSDN references-->
[ALTER INDEX]: https://msdn.microsoft.com/library/ms188388.aspx


<!--Other Web references-->

<!---HONumber=AcomDC_0504_2016-->