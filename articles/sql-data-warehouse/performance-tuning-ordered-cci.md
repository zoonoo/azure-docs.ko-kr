---
title: Azure SQL Data Warehouse 순서가 지정 된 클러스터형 columnstore 인덱스로 성능 튜닝 | Microsoft Docs
description: 정렬 된 클러스터형 columnstore 인덱스를 사용 하 여 쿼리 성능을 향상 시킬 때 알아야 할 권장 사항 및 고려 사항입니다.
services: sql-data-warehouse
author: XiaoyuMSFT
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: development
ms.date: 09/05/2019
ms.author: xiaoyul
ms.reviewer: nibruno; jrasnick
ms.openlocfilehash: 41fbebcf4b85f6e48babba30c2d05fedb3e7a5c7
ms.sourcegitcommit: 909ca340773b7b6db87d3fb60d1978136d2a96b0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2019
ms.locfileid: "70985299"
---
# <a name="performance-tuning-with-ordered-clustered-columnstore-index"></a>순서가 지정 된 클러스터형 columnstore 인덱스로 성능 튜닝  

사용자가 Azure SQL Data Warehouse에서 columnstore 테이블을 쿼리하면 최적화 프로그램은 각 세그먼트에 저장 된 최소값과 최대값을 확인 합니다.  쿼리 조건자의 범위 밖에 있는 세그먼트는 디스크에서 메모리로 읽지 않습니다.  읽을 세그먼트 수와 총 크기가 작은 경우 쿼리는 더 빠른 성능을 얻을 수 있습니다.   

## <a name="ordered-vs-non-ordered-clustered-columnstore-index"></a>정렬 된 클러스터형 columnstore 인덱스 및 순서가 지정 되지 않은 클러스터형 columnstore 인덱스 
기본적으로 인덱스 옵션 없이 만든 각 Azure 데이터 웨어하우스 테이블에 대해 내부 구성 요소 (인덱스 작성기)는 순서가 지정 되지 않은 클러스터형 columnstore 인덱스 (CCI)를 만듭니다.  각 열의 데이터는 별도의 CCI 행 그룹 세그먼트로 압축 됩니다.  각 세그먼트의 값 범위에 대 한 메타 데이터가 있으므로 쿼리 조건자의 범위 밖에 있는 세그먼트는 쿼리 실행 중에 디스크에서 읽지 않습니다.  CCI는 가장 높은 수준의 데이터 압축을 제공 하 고 읽을 세그먼트 크기를 줄여 쿼리가 더 빠르게 실행 될 수 있도록 합니다. 그러나 인덱스 작성기가 데이터를 세그먼트로 압축 하기 전에 정렬 하지 않으므로 값 범위가 겹치는 세그먼트가 발생 하 여 디스크에서 더 많은 세그먼트를 읽고 쿼리가 완료 되는 데 시간이 오래 걸릴 수 있습니다.  

정렬 된 CCI를 만들 때 Azure SQL Data Warehouse 엔진은 인덱스 작성기가 인덱스 세그먼트로 압축 하기 전에 순서 키를 기준으로 메모리의 데이터를 정렬 합니다.  정렬 된 데이터를 사용 하는 경우 쿼리를 더 효율적으로 분할 하 여 디스크에서 읽을 세그먼트 수가 줄어들기 때문에 더 효율적으로 세그먼트를 제거 하 여 성능을 향상 시킬 수 있습니다.  모든 데이터를 한 번에 메모리에서 정렬할 수 있는 경우 세그먼트 겹치기를 방지할 수 있습니다.  데이터 웨어하우스 테이블의 데이터 크기가 큰 경우이 시나리오는 자주 발생 하지 않습니다.  

열에 대 한 세그먼트 범위를 확인 하려면 테이블 이름 및 열 이름으로 다음 명령을 실행 합니다.

```sql
SELECT o.name, pnp.index_id, pnp.rows, pnp.data_compression_desc, pnp.pdw_node_id, 
pnp.distribution_id, cls.segment_id, cls.column_id, cls.min_data_id, cls.max_data_id, cls.max_data_id-cls.min_data_id as difference
FROM sys.pdw_nodes_partitions AS pnp
   JOIN sys.pdw_nodes_tables AS Ntables ON pnp.object_id = NTables.object_id AND pnp.pdw_node_id = NTables.pdw_node_id
   JOIN sys.pdw_table_mappings AS Tmap  ON NTables.name = TMap.physical_name AND substring(TMap.physical_name,40, 10) = pnp.distribution_id
   JOIN sys.objects AS o ON TMap.object_id = o.object_id
   JOIN sys.pdw_nodes_column_store_segments AS cls ON pnp.partition_id = cls.partition_id AND pnp.distribution_id  = cls.distribution_id
   JOIN sys.columns as cols ON o.object_id = cols.object_id AND cls.column_id = cols.column_id
WHERE o.name = '<table_name>' and c.name = '<column_name>'
ORDER BY o.name, pnp.distribution_id, cls.min_data_id
```

## <a name="data-loading-performance"></a>데이터 로드 성능

정렬 된 CCI 테이블로 데이터를 로드 하는 성능은 분할 된 테이블로 데이터를 로드 하는 것과 비슷합니다.  
데이터를 정렬 하기 때문에 순서가 지정 되지 않은 CCI 테이블에 데이터를 로드 하는 것 보다 시간이 더 오래 걸릴 수 있습니다.  

다음은 서로 다른 스키마를 사용 하는 테이블로 데이터를 로드 하는 경우의 성능 비교 예제입니다.
![Performance_comparison_data_loading](media/performance-tuning-ordered-cci/cci-data-loading-performance.png)
 
## <a name="reduce-segment-overlapping"></a>겹치는 세그먼트 줄이기
다음은 CTAS 또는 데이터가 있는 기존 테이블을 통해 새 테이블에 정렬 된 CCI를 만들 때 겹치는 세그먼트를 추가로 줄이는 옵션입니다.

- 더 큰 리소스 클래스를 사용 하 여 인덱스 작성기가 세그먼트를 압축 하기 전에 메모리에서 더 많은 데이터를 한 번에 정렬할 수 있습니다.  인덱스 세그먼트에서 데이터의 물리적 위치를 변경할 수 없습니다.  세그먼트 또는 세그먼트 사이에는 데이터 정렬이 없습니다.  

- 낮은 병렬 처리 수준을 사용 합니다 (예: DOP = 1).  순서가 지정 된 CCI 생성에 사용 되는 각 스레드는 데이터의 하위 집합에서 작동 하며 로컬로 정렬 됩니다.  여러 스레드에 의해 정렬 된 데이터에는 전역 정렬이 없습니다.  병렬 스레드를 사용 하면 정렬 된 CCI를 만드는 시간을 줄일 수 있지만 단일 스레드를 사용 하는 것 보다 더 겹치는 세그먼트를 생성 합니다. 
- Azure SQL Data Warehouse 테이블로 로드 하기 전에 정렬 키를 기준으로 데이터를 미리 정렬 합니다.

## <a name="create-ordered-cci-on-large-tables"></a>많은 테이블에 순서가 지정 된 CCI 만들기
정렬 된 CCI를 만드는 작업은 오프 라인 작업입니다.  파티션이 없는 테이블의 경우에는 순서가 지정 된 CCI 생성 프로세스가 완료 될 때까지 사용자가 데이터에 액세스할 수 없습니다.   분할 된 테이블의 경우 엔진은 파티션에 의해 순서가 지정 된 CCI 파티션을 만들기 때문에, 사용자는 정렬 된 CCI 생성이 아직 진행 되지 않는 파티션에서 데이터에 계속 액세스할 수 있습니다.   이 옵션을 사용 하 여 많은 테이블에서 순서가 지정 된 CCI를 만드는 동안 가동 중지 시간을 최소화할 수 있습니다. 

1.  대상 대량 테이블 (테이블 A 라고 함)에 파티션을 만듭니다.
2.  테이블 A와 동일한 테이블 및 파티션 스키마를 사용 하 여 빈 정렬 된 CCI 테이블 (테이블 B 라고 함)을 만듭니다.
3.  테이블 A에서 테이블 B로 파티션 하나를 전환 합니다.
4.  ALTER INDEX < Ordered_CCI_Index를 실행 하 여 테이블 B에서 다시 작성 > 하 여 전환 된 파티션을 다시 작성 합니다.  
5.  표 A의 각 파티션에 대해 3 단계와 4 단계를 반복 합니다.
6.  모든 파티션이 테이블 A에서 테이블 B로 전환 되 고 다시 작성 된 후 테이블 A를 삭제 하 고 테이블 B의 이름을 Table A로 변경 합니다. 

## <a name="examples"></a>예

**A. 정렬 된 열 및 주문 서 수를 확인 하려면:**
```sql
SELECT object_name(c.object_id) table_name, c.name column_name, i.column_store_order_ordinal 
FROM sys.index_columns i 
JOIN sys.columns c ON i.object_id = c.object_id AND c.column_id = i.column_id
WHERE column_store_order_ordinal <>0
```

**B. 열 서 수를 변경 하려면 순서 목록에서 열을 추가 또는 제거 하거나 CCI에서 정렬 된 CCI로 변경 합니다.**
```sql
CREATE CLUSTERED COLUMNSTORE INDEX InternetSales ON  InternetSales
ORDER (ProductKey, SalesAmount)
WITH (DROP_EXISTING = ON)
```

## <a name="next-steps"></a>다음 단계
더 많은 개발 팁은 [SQL Data Warehouse 개발 개요](sql-data-warehouse-overview-develop.md)를 참조하세요.
