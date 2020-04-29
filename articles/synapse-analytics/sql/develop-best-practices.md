---
title: Synapse SQL에 대 한 개발 모범 사례
description: Synapse SQL에 대해 개발할 때 알아야 할 권장 사항 및 모범 사례입니다.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/15/2020
ms.author: xiaoyul
ms.reviewer: igorstan
ms.openlocfilehash: ed2638cfe4ab7e849e428729ccd17ffdeb6314af
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "82086354"
---
# <a name="development-best-practices-for-synapse-sql"></a>Synapse SQL에 대 한 개발 모범 사례
이 문서에서는 데이터 웨어하우스 솔루션을 개발할 때 도움이 되는 지침 및 모범 사례에 대해 설명합니다. 

## <a name="sql-pool-development-best-practices"></a>SQL 풀 개발 모범 사례

### <a name="reduce-cost-with-pause-and-scale"></a>일시 중지 및 규모 조정으로 비용 절감

일시 중지 및 크기 조정을 통해 비용을 절감하는 방법에 대한 자세한 내용은 [컴퓨팅 관리](../sql-data-warehouse/sql-data-warehouse-manage-compute-overview.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)를 참조하세요.

### <a name="maintain-statistics"></a>통계 유지 관리

매일 또는 각 로드 후에 통계를 업데이트해야 합니다.  통계를 작성하고 업데이트하는 성능 및 비용 간의 장단점은 항상 있습니다. 모든 통계를 유지 관리 하는 데 시간이 너무 오래 걸리는 경우 통계가 있는 열 또는 자주 업데이트 해야 하는 열을 선택 하는 것이 더 좋습니다.  

예를 들어 매일 새 값이 추가 될 수 있는 날짜 열을 업데이트 하려고 할 수 있습니다. 

> [!NOTE]
> 조인에 포함 된 열, WHERE 절에 사용 된 열 및 GROUP BY에 있는 열에 대 한 통계를 사용 하면 가장 큰 이점을 얻을 수 있습니다.

또한 [테이블 통계 관리](develop-tables-statistics.md), [통계 만들기](/sql/t-sql/statements/create-statistics-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), [통계 업데이트](/sql/t-sql/statements/update-statistics-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)를 참조 하세요.

### <a name="hash-distribute-large-tables"></a>해시 배포 대형 테이블

기본적으로 테이블은 라운드 로빈 분산됩니다.  이렇게 하면 사용자가 테이블을 배포 하는 방법을 결정 하지 않고도 간편 하 게 테이블을 만들 수 있습니다.  라운드 로빈 테이블은 일부 워크 로드에 대해 충분히 수행할 수 있습니다. 그러나 대부분의 경우에는 배포 열을 선택 하는 것이 훨씬 더 좋습니다.  

열로 배포된 테이블이 라운드 로빈 테이블의 성능을 능가하는 가장 일반적인 예는 두 개의 대형 팩트 테이블이 조인된 경우입니다.  

예를 들어 order_id에 의해 분산 된 orders 테이블과 order_id에 의해 배포 되는 트랜잭션 테이블을 사용할 경우 orders 테이블을 order_id의 트랜잭션 테이블에 조인할 때이 쿼리는 통과 쿼리가 됩니다. 

즉, 데이터 이동 작업을 제거 합니다.  단계가 적을수록 쿼리는 빨라집니다.  데이터 이동이 적을수록 쿼리는 빨라집니다.

> [!TIP]
> 분산된 테이블을 로드할 때 로드가 느려지므로 들어오는 데이터가 배포 키로 정렬되지 않도록 합니다.  

배포 열을 선택 하 여 성능을 향상 시킬 수 있는 방법 및 CREATE TABLES 문의 WITH 절에서 분산 테이블을 정의 하는 방법에 대 한 자세한 내용은 다음 링크를 참조 하십시오.

참고 항목 [테이블 개요](develop-tables-overview.md), [테이블 배포](../sql-data-warehouse/sql-data-warehouse-tables-distribute.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json), [테이블 배포 선택](https://blogs.msdn.microsoft.com/sqlcat/20../../choosing-hash-distributed-table-vs-round-robin-distributed-table-in-azure-sql-dw-service/), [CREATE TABLE](/sql/t-sql/statements/create-table-azure-sql-data-warehouse?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)및 [CREATE TABLE 선택](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)합니다.

### <a name="do-not-over-partition"></a>과도한 분할 피하기
분할 데이터는 파티션 전환을 통해 데이터를 유지 관리 하거나 파티션 제거를 사용 하 여 검색을 최적화 하는 데 효과적일 수 있지만 너무 많은 파티션이 있으면 쿼리 속도가 느려질 수 있습니다.  종종 SQL 풀에서 제대로 작동 하지 않을 수 SQL Server에서 잘 작동 하는 높은 세분성 분할 전략이 있습니다.  

> [!NOTE]
> 종종 SQL 풀에서 제대로 작동 하지 않을 수 SQL Server에서 잘 작동 하는 높은 세분성 분할 전략이 있습니다.  

너무 많은 파티션을 포함하면 각 파티션에 100만 개 미만의 행이 포함된 경우 클러스터형 columnstore 인덱스의 효율성이 떨어질 수도 있습니다. SQL 풀은 데이터를 60 데이터베이스에 분할 합니다. 

따라서 100 파티션을 포함 하는 테이블을 만드는 경우에는 6000 파티션이 생성 됩니다.  각 워크로드가 서로 다르므로 가장 좋은 방법은 분할 실험을 통해 사용자의 워크로드에 가장 적합한 방법을 찾는 것입니다.  

고려해 야 할 한 가지 옵션은 SQL Server에서 작업 하는 것 보다 낮은 세분성을 사용 하는 것입니다.  예를 들어 매일 분할보다는 매주 또는 매달 분할을 사용하는 것이 좋습니다.

[테이블 분할](../sql-data-warehouse/sql-data-warehouse-tables-partition.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)도 참조 하세요.

### <a name="minimize-transaction-sizes"></a>트랜잭션 크기 최소화

INSERT, UPDATE, DELETE 문은 트랜잭션에서 실행되며 실패할 경우 롤백해야 합니다.  긴 롤백에 대한 가능성을 최소화하려면 가능한 경우 항상 트랜잭션 크기를 최소화합니다.  INSERT, UPDATE 및 DELETE 문을 부분으로 나누어 이를 수행할 수 있습니다.  

예를 들어 1 시간을 소요 하는 삽입이 있는 경우 삽입을 4 개의 부분으로 분할 하 여 각 실행을 15 분으로 단축할 수 있습니다.

> [!TIP]
> CTAS, TRUNCATE, DROP TABLE 또는 INSERT와 같은 특수한 로깅 사례를 최소한 활용하여 롤백 위험을 줄입니다.  

롤백을 제거하는 다른 방법은 데이터 관리를 위한 파티션 전환과 같은 메타데이터 전용 작업을 사용하는 것입니다.  

예를 들어 테이블에서 order_date가 2001년 10월인 모든 행을 제거하기 위해 DELETE 문을 실행하는 대신 데이터를 매달 분할한 후 해당 파티션을 다른 테이블의 비어 있는 파티션의 데이터로 전환할 수 있습니다(ALTER TABLE 예 참조).  

분할되지 않은 테이블의 경우 DELETE를 사용하는 대신, 테이블에 유지할 데이터를 기록하도록 CTAS를 사용하는 것이 좋습니다.  CTAS에 동일한 시간이 소요되는 경우 매우 최소한의 트랜잭션 로깅을 포함하므로 실행하기에 훨씬 더 안전한 작업이며 필요할 경우 신속하게 취소할 수 있습니다.

[트랜잭션 이해](develop-transactions.md), [트랜잭션 최적화](../sql-data-warehouse/sql-data-warehouse-develop-best-practices-transactions.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json), [테이블 분할](../sql-data-warehouse/sql-data-warehouse-tables-partition.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json), [TRUNCATE TABLE](/sql/t-sql/statements/truncate-table-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), [ALTER table](/sql/t-sql/statements/alter-table-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)및 [ctas (Create Table as select)](../sql-data-warehouse/sql-data-warehouse-develop-ctas.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)도 참조 하세요.

### <a name="use-the-smallest-possible-column-size"></a>가능한 가장 작은 열 크기 사용

DDL을 정의할 때 데이터를 지원할 가장 작은 데이터 형식을 사용하면 쿼리 성능이 향상됩니다.  이것은 CHAR 및 VARCHAR 열에 대해 특히 중요합니다.  

열에서 가장 긴 값이 25자인 경우 열을 VARCHAR(25)로 정의합니다.  모든 문자 열을 큰 기본 길이로 정의하지 마세요.  또한 반드시 필요한 경우에는 열을 NVARCHAR를 사용하는 것보다 VARCHAR로 정의하세요.

또한 [테이블 개요](develop-tables-overview.md), [테이블 데이터 형식](develop-tables-data-types.md)및 [CREATE TABLE](/sql/t-sql/statements/create-table-azure-sql-data-warehouse?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)을 참조 하세요.

### <a name="optimize-clustered-columnstore-tables"></a>클러스터형 Columnstore 테이블 최적화

클러스터형 columnstore 인덱스는 SQL 풀에 데이터를 저장할 수 있는 가장 효율적인 방법 중 하나입니다.  기본적으로 SQL 풀의 테이블은 클러스터형 ColumnStore로 생성 됩니다.  

columnstore 테이블에서 쿼리에 대해 최상의 성능을 얻으려면 좋은 세그먼트 품질을 갖는 것이 중요합니다.  메모리 부족 상황에서 행이 columnstore 테이블에 기록되는 경우 columnstore 세그먼트 품질이 저하될 수 있습니다.  

세그먼트 품질은 압축된 행 그룹에 있는 행의 수로 측정할 수 있습니다.  클러스터형 columnstore 테이블의 세그먼트 품질을 감지 하 고 개선 하는 방법에 대 한 단계별 지침은 [잘못 된 columnstore 인덱스 품질](../sql-data-warehouse/sql-data-warehouse-tables-index.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json#causes-of-poor-columnstore-index-quality) 및 [테이블 인덱스](../sql-data-warehouse/sql-data-warehouse-tables-index.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) 의 원인 문서를 참조 하세요.  

고품질 columnstore 세그먼트가 중요하기 때문에 데이터를 로드하기 위해서는 중간 규모 또는 대규모 리소스 클래스에 속하는 사용자 ID를 사용하는 것이 좋습니다. 낮은 [데이터 웨어하우스 단위](resource-consumption-models.md)를 사용하면 로드하는 사용자에게 더 큰 리소스 클래스를 할당하려는 것입니다.

Columnstore 테이블은 일반적으로 테이블당 100만 개 이상의 행이 있을 때까지 데이터를 압축 된 columnstore 세그먼트로 푸시 하지 않으며 각 SQL 풀 테이블이 60 테이블로 분할 되기 때문에 테이블에 6000만 개 이상의 행이 있는 경우를 제외 하 고 columnstore 테이블은 쿼리에 유용 하지 않습니다.  

> [!TIP]
> 6000만 개 미만의 행이 있는 테이블의 경우 columstore 인덱스를 사용 하는 것은 최적의 솔루션이 아닐 수 있습니다.  

또한 데이터를 분할하는 경우 클러스터형 columnstore 인덱스의 이점을 얻기 위해 각 파티션에는 100개의 행을 포함하는 것이 좋습니다.  테이블에 100 개의 파티션이 있는 경우 클러스터형 열 저장소 (60 배포판 *100 파티션* 100만 행)를 활용 하려면 60억 행 이상이 필요 합니다.  

테이블에 60억 행이 없는 경우 파티션 수를 줄이거나 힙 테이블을 대신 사용 하는 것이 좋습니다.  또한 columnstore 테이블이 아닌 보조 인덱스와 함께 힙 테이블을 사용 하면 더 나은 성능을 얻을 수 있는지를 시험해 볼 수 있습니다.

columnstore 테이블을 쿼리할 때 필요한 열만 선택하면 쿼리가 더 빨리 실행됩니다.  

[테이블 인덱스](../sql-data-warehouse/sql-data-warehouse-tables-index.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json), [columnstore 인덱스 가이드](/sql/relational-databases/indexes/columnstore-indexes-overview?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), [columnstore 인덱스 다시 작성](../sql-data-warehouse/sql-data-warehouse-tables-index.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json#rebuilding-indexes-to-improve-segment-quality)을 참조 하세요.

## <a name="sql-on-demand-development-best-practices"></a>SQL 주문형 개발 모범 사례

### <a name="general-considerations"></a>일반적인 고려 사항

SQL 주문형 요청을 통해 Azure storage 계정에서 파일을 쿼리할 수 있습니다. 로컬 저장소 또는 수집 기능이 없습니다. 즉, 쿼리가 대상으로 하는 모든 파일이 SQL 주문형 외부에 있습니다. 따라서 저장소에서 파일을 읽는 작업과 관련 된 모든 항목이 쿼리 성능에 영향을 줄 수 있습니다.

### <a name="colocate-azure-storage-account-and-sql-on-demand"></a>Azure Storage 계정 및 SQL 요청 시 검색

대기 시간을 최소화 하려면 Azure storage 계정 및 SQL 주문형 끝점을 배치 합니다. 작업 영역을 만드는 동안 프로 비전 된 저장소 계정 및 끝점은 동일한 지역에 있습니다.

성능을 최적화 하려면 SQL 요청 시 다른 저장소 계정에 액세스 하는 경우 동일한 지역에 있는지 확인 합니다. 그렇지 않으면 원격 지역에서 끝점의 지역으로 데이터 네트워크를 전송할 때 대기 시간이 증가 합니다.

### <a name="azure-storage-throttling"></a>Azure Storage 제한

여러 응용 프로그램 및 서비스에서 저장소 계정에 액세스할 수 있습니다. 응용 프로그램, 서비스 및 SQL 요청 시 작업에서 생성 된 결합 IOPS 또는 처리량이 저장소 계정의 한도를 초과 하면 저장소 제한이 발생 합니다. 저장소 제한이 발생 하면 쿼리 성능에 부정적인 영향을 미칠 수 있습니다.

제한이 감지 되 면 SQL 주문형은이 시나리오를 기본적으로 처리 합니다. SQL 주문형 요청은 조정이 해결 될 때까지 느린 속도로 저장소에 대 한 요청을 만듭니다. 

그러나 최적의 쿼리 실행을 위해 쿼리를 실행 하는 동안 다른 워크 로드를 사용 하 여 저장소 계정을 스트레스 하지 않는 것이 좋습니다.

### <a name="prepare-files-for-querying"></a>쿼리할 파일 준비

가능 하면 더 나은 성능을 위해 파일을 준비할 수 있습니다.

- CSV를 Parquet으로 변환 – Parquet은 칼럼 형식입니다. 압축 된 파일 크기는 동일한 데이터를 사용 하는 CSV 파일 보다 크기가 작기 때문에 SQL 요청 시에는 읽기에 필요한 시간과 저장소 요청이 줄어듭니다.
- 쿼리가 단일 큰 파일을 대상으로 하는 경우 여러 개의 작은 파일로 분할 하는 것이 좋습니다.
- CSV 파일 크기를 10GB 미만으로 유지 하세요.
- 단일 OPENROWSET 경로 또는 외부 테이블 위치에 대해 크기가 동일한 파일을 지정 하는 것이 좋습니다.
- 여러 폴더 또는 파일 이름에 파티션을 저장 하 여 데이터 분할- [파일 이름 및 filepath 함수를 사용 하 여 특정 파티션을 대상으로 지정을](#use-fileinfo-and-filepath-functions-to-target-specific-partitions)선택 합니다.

### <a name="use-fileinfo-and-filepath-functions-to-target-specific-partitions"></a>Fileinfo 및 filepath 함수를 사용 하 여 특정 파티션 대상 지정

데이터는 종종 파티션으로 구성 됩니다. 특정 폴더 및 파일을 쿼리하도록 SQL 주문형에 지시할 수 있습니다. 이렇게 하면 쿼리를 읽고 처리 하는 데 필요한 데이터 양과 파일 수가 줄어듭니다. 

따라서 성능을 향상 시킬 수 있습니다. 자세한 내용은 파일 [이름](develop-storage-files-overview.md#filename-function) 및 파일 [경로](develop-storage-files-overview.md#filepath-function) 함수 및 [특정 파일을 쿼리](query-specific-files.md)하는 방법에 대 한 예제를 확인 합니다.

저장소의 데이터가 분할 되지 않은 경우 이러한 함수를 사용 하 여 해당 파일을 대상으로 하는 쿼리를 최적화할 수 있도록 분할 하는 것이 좋습니다.

요청 시 SQL에서 [분할 된 Spark 테이블을 쿼리](develop-storage-files-spark-tables.md) 하는 경우 쿼리는 필요한 파일만 자동으로 대상으로 합니다.

### <a name="use-cetas-to-enhance-query-performance-and-joins"></a>CETAS를 사용 하 여 쿼리 성능 및 조인 향상

[CETAS](develop-tables-cetas.md) 는 SQL 주문형에서 사용할 수 있는 가장 중요 한 기능 중 하나입니다. CETAS는 외부 테이블 메타 데이터를 생성 하 고 SELECT 쿼리의 결과를 저장소 계정의 파일 집합으로 내보내는 병렬 작업입니다.

CETAS를 사용 하 여 조인 된 참조 테이블과 같이 자주 사용 되는 쿼리의 일부를 새 파일 집합에 저장할 수 있습니다. 나중에 여러 쿼리에서 공통 조인을 반복 하는 대신이 단일 외부 테이블에 조인할 수 있습니다. 

CETAS에서 Parquet 파일을 생성 하면 첫 번째 쿼리가이 외부 테이블을 대상으로 하 고 성능이 향상 될 때 통계가 자동으로 생성 됩니다.

### <a name="next-steps"></a>다음 단계

이 문서에서 제공 하지 않는 정보가 필요한 경우이 페이지의 왼쪽에 있는 "문서 검색"을 사용 하 여 모든 SQL 풀 문서를 검색 합니다.  [Sql 풀 포럼](https://social.msdn.microsoft.com/Forums/sqlserver/home?forum=AzureSQLDataWarehouse) 은 다른 사용자 및 Sql 풀 제품 그룹에 대 한 질문을 할 수 있는 장소입니다.  

Microsoft는 이 포럼을 적극적으로 모니터링하여 사용자의 질문에 다른 사용자나 당사 직원이 응답하도록 합니다.  Stack Overflow에 대 한 질문을 하는 것을 선호 하는 경우 [AZURE SQL 풀 Stack Overflow 포럼](https://stackoverflow.com/questions/tagged/azure-sqldw)도 있습니다.
 