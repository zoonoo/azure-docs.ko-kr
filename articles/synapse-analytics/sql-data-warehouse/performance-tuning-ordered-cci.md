---
title: 순서가 지정된 클러스터형 columnstore 인덱스를 사용한 성능 조정
description: 쿼리 성능을 향상시키기 위해 정렬된 클러스터된 columnstore 인덱스를 사용할 때 알아야 할 권장 사항 및 고려 사항입니다.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 09/05/2019
ms.author: xiaoyul
ms.reviewer: nibruno; jrasnick
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: 2113e5ac3563a22c5f2c6b755230b05fb9a2cb35
ms.sourcegitcommit: 3c318f6c2a46e0d062a725d88cc8eb2d3fa2f96a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/02/2020
ms.locfileid: "80583868"
---
# <a name="performance-tuning-with-ordered-clustered-columnstore-index"></a>순서가 지정된 클러스터형 columnstore 인덱스를 사용한 성능 조정  

사용자가 Synapse SQL 풀에서 열저장소 테이블을 쿼리할 때 최적화 프로그램은 각 세그먼트에 저장된 최소 값과 최대 값을 확인합니다.  쿼리 조건자의 범위를 벗어난 세그먼트는 디스크에서 메모리로 읽히지 않습니다.  읽을 세그먼트 의 수와 총 크기가 작은 경우 쿼리는 더 빠른 성능을 얻을 수 있습니다.   

## <a name="ordered-vs-non-ordered-clustered-columnstore-index"></a>정렬 된 것 과 비 정렬 된 클러스터된 열 저장소 인덱스

기본적으로 인덱스 옵션 없이 만든 각 테이블에 대해 내부 구성 요소(인덱스 빌더)는 순서가 지정되지 않은 클러스터된 열저장소 인덱스(CCI)를 만듭니다.  각 열의 데이터는 별도의 CCI 행 그룹 세그먼트로 압축됩니다.  각 세그먼트의 값 범위에는 메타데이터가 있으므로 쿼리 조건자의 범위를 벗어난 세그먼트는 쿼리 실행 중에 디스크에서 읽히지 않습니다.  CCI는 최고 수준의 데이터 압축을 제공하며 쿼리를 더 빠르게 실행할 수 있도록 읽을 세그먼트의 크기를 줄입니다. 그러나 인덱스 빌더는 데이터를 세그먼트로 압축하기 전에 데이터를 정렬하지 않으므로 값 범위가 겹치는 세그먼트가 발생할 수 있으므로 쿼리가 디스크에서 더 많은 세그먼트를 읽고 완료하는 데 시간이 오래 걸릴 수 있습니다.  

정렬된 CCI를 만들 때 Synapse SQL 엔진은 인덱스 빌더가 인덱스 세그먼트로 압축하기 전에 메모리의 기존 데이터를 순서 키로 정렬합니다.  정렬된 데이터를 사용하면 세그먼트 중복이 줄어들어 쿼리가 보다 효율적인 세그먼트 제거를 할 수 있으므로 디스크에서 읽을 세그먼트 수가 적기 때문에 성능이 빨라집니다.  모든 데이터를 한 번에 메모리에 정렬할 수 있는 경우 세그먼트 중복을 방지할 수 있습니다.  데이터 웨어하우스의 큰 테이블로 인해 이 시나리오는 자주 발생하지 않습니다.  

열의 세그먼트 범위를 확인하려면 테이블 이름과 열 이름으로 이 명령을 실행합니다.

```sql
SELECT o.name, pnp.index_id, 
cls.row_count, pnp.data_compression_desc, 
pnp.pdw_node_id, pnp.distribution_id, cls.segment_id, 
cls.column_id, 
cls.min_data_id, cls.max_data_id, 
cls.max_data_id-cls.min_data_id as difference
FROM sys.pdw_nodes_partitions AS pnp
   JOIN sys.pdw_nodes_tables AS Ntables ON pnp.object_id = NTables.object_id AND pnp.pdw_node_id = NTables.pdw_node_id
   JOIN sys.pdw_table_mappings AS Tmap  ON NTables.name = TMap.physical_name AND substring(TMap.physical_name,40, 10) = pnp.distribution_id
   JOIN sys.objects AS o ON TMap.object_id = o.object_id
   JOIN sys.pdw_nodes_column_store_segments AS cls ON pnp.partition_id = cls.partition_id AND pnp.distribution_id  = cls.distribution_id
JOIN sys.columns as cols ON o.object_id = cols.object_id AND cls.column_id = cols.column_id
WHERE o.name = '<Table Name>' and cols.name = '<Column Name>'  and TMap.physical_name  not like '%HdTable%'
ORDER BY o.name, pnp.distribution_id, cls.min_data_id 


```

> [!NOTE] 
> 정렬된 CCI 테이블에서 동일한 DML 일괄 처리 또는 데이터 로드 작업으로 인해 발생하는 새 데이터가 해당 일괄 처리 내에서 정렬되며 테이블의 모든 데이터에 대한 전역 정렬이 없습니다.  사용자는 정렬된 CCI를 다시 빌드하여 테이블의 모든 데이터를 정렬할 수 있습니다.  Synapse SQL에서 열 저장소 인덱스 REBUILD는 오프라인 작업입니다.  분할된 테이블의 경우 REBUILD는 한 번에 하나의 파티션을 수행합니다.  다시 빌드중인 파티션의 데이터는 "오프라인"이며 해당 파티션에 대해 REBUILD가 완료될 때까지 사용할 수 없습니다. 

## <a name="query-performance"></a>쿼리 성능

정렬된 CCI에서 쿼리의 성능 향상은 쿼리 패턴, 데이터 크기, 데이터가 정렬되는 정도, 세그먼트의 물리적 구조 및 쿼리 실행을 위해 선택된 DWU 및 리소스 클래스에 따라 달라집니다.  사용자는 정렬된 CCI 테이블을 디자인할 때 순서 열을 선택하기 전에 이러한 모든 요소를 검토해야 합니다.

이러한 모든 패턴이 있는 쿼리는 일반적으로 정렬된 CCI를 사용하여 더 빠르게 실행됩니다.  
1. 쿼리에 같음, 부등분 또는 범위 조건자가 있습니다.
1. 술어 열과 정렬된 CCI 열은 동일합니다.  
1. 술어 열은 정렬된 CCI 열의 열 서수와 동일한 순서로 사용됩니다.  
 
이 예제에서 테이블 T1에는 Col_C, Col_B 및 Col_A 순서대로 정렬된 클러스터된 columnstore 인덱스가 있습니다.

```sql

CREATE CLUSTERED COLUMNSTORE INDEX MyOrderedCCI ON  T1
ORDER (Col_C, Col_B, Col_A)

```

쿼리 1의 성능은 다른 세 쿼리보다 정렬된 CCI의 이점을 더 많이 얻을 수 있습니다. 

```sql
-- Query #1: 

SELECT * FROM T1 WHERE Col_C = 'c' AND Col_B = 'b' AND Col_A = 'a';

-- Query #2

SELECT * FROM T1 WHERE Col_B = 'b' AND Col_C = 'c' AND Col_A = 'a';

-- Query #3
SELECT * FROM T1 WHERE Col_B = 'b' AND Col_A = 'a';

-- Query #4
SELECT * FROM T1 WHERE Col_A = 'a' AND Col_C = 'c';

```

## <a name="data-loading-performance"></a>데이터 로딩 성능

정렬된 CCI 테이블로 데이터를 로드하는 성능은 분할된 테이블과 유사합니다.  정렬된 CCI 테이블에 데이터를 로드하는 데 데이터 정렬 작업으로 인해 정렬되지 않은 CCI 테이블보다 오래 걸릴 수 있지만 정렬된 CCI를 사용하여 나중에 쿼리를 더 빠르게 실행할 수 있습니다.  

다음은 다른 스키마를 사용하여 테이블에 데이터를 로드하는 성능 비교 예제입니다.

![Performance_comparison_data_loading](./media/performance-tuning-ordered-cci/cci-data-loading-performance.png)


다음은 CCI와 정렬된 CCI 간의 쿼리 성능 비교 예제입니다.

![Performance_comparison_data_loading](./media/performance-tuning-ordered-cci/occi_query_performance.png)

 
## <a name="reduce-segment-overlapping"></a>세그먼트 겹침 감소

겹치는 세그먼트의 수는 정렬할 데이터의 크기, 사용 가능한 메모리 및 주문한 CCI 생성 중에 최대 병렬 처리(MAXDOP) 설정정도에 따라 달라집니다. 다음은 정렬된 CCI를 만들 때 세그먼트 중복을 줄이는 옵션입니다.

- 인덱스 빌더가 데이터를 세그먼트로 압축하기 전에 더 많은 메모리를 데이터 정렬에 사용할 수 있도록 상위 DWU에서 xlargerc 리소스 클래스를 사용합니다.  인덱스 세그먼트에 들어가면 데이터의 실제 위치를 변경할 수 없습니다.  세그먼트 내 또는 세그먼트 간에 데이터 정렬이 없습니다.  

- MAXDOP = 1로 정렬된 CCI를 만듭니다.  정렬된 CCI 만들기에 사용되는 각 스레드는 데이터의 하위 집합에서 작동하며 로컬로 정렬됩니다.  다른 스레드별로 정렬된 데이터 간에 전역 정렬이 없습니다.  병렬 스레드를 사용하면 정렬된 CCI를 만드는 시간을 줄일 수 있지만 단일 스레드를 사용하는 것보다 겹치는 세그먼트가 더 많이 생성됩니다.  현재 MAXDOP 옵션은 SELECT 테이블 만들기 명령을 사용하여 정렬된 CCI 테이블을 만드는 데만 지원됩니다.  인덱스 만들기 또는 테이블 만들기 명령을 통해 정렬된 CCI를 만들면 MAXDOP 옵션이 지원되지 않습니다. 예를 들면 다음과 같습니다.

```sql
CREATE TABLE Table1 WITH (DISTRIBUTION = HASH(c1), CLUSTERED COLUMNSTORE INDEX ORDER(c1) )
AS SELECT * FROM ExampleTable
OPTION (MAXDOP 1);
```

- 테이블에 로드하기 전에 정렬 키로 데이터를 미리 정렬합니다.

다음은 위의 권장 사항에 따라 세그먼트가 겹치지 않는 정렬된 CCI 테이블 분포의 예입니다. 주문한 CCI 테이블은 MAXDOP 1 및 xlargerc를 사용하는 20GB 힙 테이블의 CTAS를 통해 DWU1000c 데이터베이스에 생성됩니다.  CCI는 중복없이 BIGINT 열에 주문됩니다.  

![Segment_No_Overlapping](./media/performance-tuning-ordered-cci/perfect-sorting-example.png)

## <a name="create-ordered-cci-on-large-tables"></a>큰 테이블에서 정렬된 CCI 만들기

정렬된 CCI를 만드는 것은 오프라인 작업입니다.  파티션이 없는 테이블의 경우 정렬된 CCI 생성 프로세스가 완료될 때까지 사용자가 데이터에 액세스할 수 없습니다.   분할된 테이블의 경우 엔진이 파티션별로 정렬된 CCI 파티션을 생성하므로 사용자는 정렬된 CCI 생성이 진행되지 않는 파티션의 데이터에 계속 액세스할 수 있습니다.   이 옵션을 사용하여 큰 테이블에서 주문한 CCI 생성 중에 가동 중지 시간을 최소화할 수 있습니다. 

1.    대상 큰 테이블(Table_A)에 파티션을 만듭니다.
2.    테이블 A와 동일한 테이블 및 파티션 스키마를 사용하여 빈 순서가 Table_B CCI 테이블(Table_B)을 만듭니다.
3.    하나의 파티션을 테이블 A에서 테이블 B로 전환합니다.
4.    변경 된 파티션을 다시 Table_B Table_B Table_B> <alter index> <Ordered_CCI_Index <실행 파티션 = <테이블 B에서 <Partition_ID> 전환 된 파티션을 다시 빌드합니다.  
5.    Table_A 각 파티션에 대해 3단계와 4단계를 반복합니다.
6.    모든 파티션이 Table_A Table_B 전환되고 다시 빌드되면 Table_A 삭제하고 Table_B 이름을 Table_A. 

## <a name="examples"></a>예

**A. 주문된 열및 주문 서수 확인:**

```sql
SELECT object_name(c.object_id) table_name, c.name column_name, i.column_store_order_ordinal 
FROM sys.index_columns i 
JOIN sys.columns c ON i.object_id = c.object_id AND c.column_id = i.column_id
WHERE column_store_order_ordinal <>0
```

**B. 열 서수 변경을 수행하려면 주문 목록에서 열을 추가 또는 제거하거나 CCI에서 정렬된 CCI로 변경합니다.**

```sql
CREATE CLUSTERED COLUMNSTORE INDEX InternetSales ON  InternetSales
ORDER (ProductKey, SalesAmount)
WITH (DROP_EXISTING = ON)
```

## <a name="next-steps"></a>다음 단계

더 많은 개발 팁은 [개발 개요](sql-data-warehouse-overview-develop.md)를 참조하세요.
