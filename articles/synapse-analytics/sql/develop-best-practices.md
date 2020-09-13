---
title: Synapse SQL 개발 모범 사례
description: Synapse SQL을 개발할 때 알아야 할 권장 사항 및 모범 사례입니다.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql
ms.date: 04/15/2020
ms.author: xiaoyul
ms.reviewer: igorstan
ms.openlocfilehash: d38029284a05ce3b8f9e9af96d3f632e874f874c
ms.sourcegitcommit: 3fc3457b5a6d5773323237f6a06ccfb6955bfb2d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/11/2020
ms.locfileid: "90032274"
---
# <a name="development-best-practices-for-synapse-sql"></a>Synapse SQL 개발 모범 사례
이 문서에서는 데이터 웨어하우스 솔루션을 개발할 때 도움이 되는 지침 및 모범 사례에 대해 설명합니다. 

## <a name="sql-pool-development-best-practices"></a>SQL 풀 개발 모범 사례

### <a name="reduce-cost-with-pause-and-scale"></a>일시 중지 및 규모 조정으로 비용 절감

일시 중지 및 크기 조정을 통해 비용을 절감하는 방법에 대한 자세한 내용은 [컴퓨팅 관리](../sql-data-warehouse/sql-data-warehouse-manage-compute-overview.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)를 참조하세요.

### <a name="maintain-statistics"></a>통계 유지 관리

매일 또는 각 로드 후에 통계를 업데이트해야 합니다.  통계를 작성하고 업데이트하는 성능 및 비용 간의 장단점은 항상 있습니다. 모든 통계를 유지하는 데 시간이 너무 오래 소요되는 경우 어떤 열에 통계가 있고 어떤 열에서 자주 업데이트가 필요한지 더 선별적으로 시도해볼 수 있습니다.  

예를 들어 매일 새 값이 추가될 수 있는 날짜 열을 업데이트할 수 있습니다. 

> [!NOTE]
> 조인에 포함된 열, WHERE 절에 사용된 열과 GROUP BY에 있는 열에서 통계를 유지하면 가장 큰 이점을 얻게 됩니다.

또한 [테이블 통계 관리](develop-tables-statistics.md), [CREATE STATISTICS](/sql/t-sql/statements/create-statistics-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), [UPDATE STATISTICS](/sql/t-sql/statements/update-statistics-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)도 참조하세요.

### <a name="hash-distribute-large-tables"></a>해시 배포 대형 테이블

기본적으로 테이블은 라운드 로빈 분산됩니다.  따라서 사용자는 해당 테이블이 배포되는 방식을 결정하지 않고도 테이블 생성을 간편하게 시작할 수 있습니다.  일부 워크로드의 경우 라운드 로빈 테이블만으로 충분할 수 있습니다. 그러나 대부분의 경우에는 배포 열을 선택하는 것이 훨씬 더 좋습니다.  

열로 배포된 테이블이 라운드 로빈 테이블의 성능을 능가하는 가장 일반적인 예는 두 개의 대형 팩트 테이블이 조인된 경우입니다.  

예를 들어 order_id로 배포되는 주문 테이블과 order_id로 배포되는 트랜잭션 테이블이 있고 주문 테이블을 order_id로 트랜잭션 테이블에 조인하는 경우 이 쿼리는 통과 쿼리가 됩니다. 

즉, 데이터 이동 작업이 제거됩니다.  단계가 적을수록 쿼리는 빨라집니다.  데이터 이동이 적을수록 쿼리는 빨라집니다.

> [!TIP]
> 분산된 테이블을 로드할 때 로드가 느려지므로 들어오는 데이터가 배포 키로 정렬되지 않도록 합니다.  

성능을 향상시킬 수 있도록 배포 열을 선택하는 방법과 CREATE TABLES 문의 WITH 절에서 분산된 테이블을 정의하는 방법은 아래 링크를 참조하세요.

[테이블 개요](develop-tables-overview.md), [테이블 배포](../sql-data-warehouse/sql-data-warehouse-tables-distribute.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json), [테이블 배포 선택](https://blogs.msdn.microsoft.com/sqlcat/20../../choosing-hash-distributed-table-vs-round-robin-distributed-table-in-azure-sql-dw-service/), [CREATE TABLE](/sql/t-sql/statements/create-table-azure-sql-data-warehouse?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) 및 [CREATE TABLE AS SELECT](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)도 참조하세요.

### <a name="do-not-over-partition"></a>과도한 분할 피하기
데이터를 분할하면 파티션 전환 또는 파티션 제거로 스캔 최적화를 통해 데이터를 효율적으로 유지 관리할 수 있지만 과도하게 분할할 경우 쿼리 속도가 느려질 수 있습니다.  SQL Server에서 제대로 작동할 수 있는 높은 세분성 분할 전략은 SQL 풀에서는 제대로 작동하지 않을 수 있습니다.  

> [!NOTE]
> SQL Server에서 제대로 작동할 수 있는 높은 세분성 분할 전략은 SQL 풀에서는 제대로 작동하지 않을 수 있습니다.  

너무 많은 파티션을 포함하면 각 파티션에 100만 개 미만의 행이 포함된 경우 클러스터형 columnstore 인덱스의 효율성이 떨어질 수도 있습니다. SQL 풀은 데이터를 60개의 데이터베이스로 분할합니다. 

따라서 100개 파티션을 포함하는 테이블을 만드는 경우 결과적으로 6,000개의 파티션이 생성됩니다.  각 워크로드가 서로 다르므로 가장 좋은 방법은 분할 실험을 통해 사용자의 워크로드에 가장 적합한 방법을 찾는 것입니다.  

고려해야 할 한 가지 옵션은 SQL Server에서 작업한 것보다 낮은 세분성을 사용해야 한다는 좋습니다.  예를 들어 매일 분할보다는 매주 또는 매달 분할을 사용하는 것이 좋습니다.

[테이블 분할](../sql-data-warehouse/sql-data-warehouse-tables-partition.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)도 참조하세요.

### <a name="minimize-transaction-sizes"></a>트랜잭션 크기 최소화

INSERT, UPDATE, DELETE 문은 트랜잭션에서 실행되며 실패할 경우 롤백해야 합니다.  긴 롤백에 대한 가능성을 최소화하려면 가능한 경우 항상 트랜잭션 크기를 최소화합니다.  INSERT, UPDATE 및 DELETE 문을 부분으로 나누어 이를 수행할 수 있습니다.  

예를 들어 1시간이 소요될 것으로 예상되는 INSERT가 있는 경우 INSERT를 4개의 부분으로 나누어 각 실행을 15분으로 단축할 수 있습니다.

> [!TIP]
> CTAS, TRUNCATE, DROP TABLE 또는 INSERT와 같은 특수한 로깅 사례를 최소한 활용하여 롤백 위험을 줄입니다.  

롤백을 제거하는 다른 방법은 데이터 관리를 위한 파티션 전환과 같은 메타데이터 전용 작업을 사용하는 것입니다.  

예를 들어 테이블에서 order_date가 2001년 10월인 모든 행을 제거하기 위해 DELETE 문을 실행하는 대신 데이터를 매달 분할한 후 해당 파티션을 다른 테이블의 비어 있는 파티션의 데이터로 전환할 수 있습니다(ALTER TABLE 예 참조).  

분할되지 않은 테이블의 경우 DELETE를 사용하는 대신, 테이블에 유지할 데이터를 기록하도록 CTAS를 사용하는 것이 좋습니다.  CTAS에 동일한 시간이 소요되는 경우 매우 최소한의 트랜잭션 로깅을 포함하므로 실행하기에 훨씬 더 안전한 작업이며 필요할 경우 신속하게 취소할 수 있습니다.

또한 [트랜잭션 이해](develop-transactions.md), [트랜잭션 최적](../sql-data-warehouse/sql-data-warehouse-develop-best-practices-transactions.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json), [테이블 분할](../sql-data-warehouse/sql-data-warehouse-tables-partition.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json), [TRUNCATE TABLE](/sql/t-sql/statements/truncate-table-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), [ALTER TABLE](/sql/t-sql/statements/alter-table-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) 및 [CTAS(Create Table As Select)](../sql-data-warehouse/sql-data-warehouse-develop-ctas.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)도 참조하세요.

### <a name="use-the-smallest-possible-column-size"></a>가능한 가장 작은 열 크기 사용

DDL을 정의할 때 데이터를 지원할 가장 작은 데이터 형식을 사용하면 쿼리 성능이 향상됩니다.  이것은 CHAR 및 VARCHAR 열에 대해 특히 중요합니다.  

열에서 가장 긴 값이 25자인 경우 열을 VARCHAR(25)로 정의합니다.  모든 문자 열을 큰 기본 길이로 정의하지 마세요.  또한 반드시 필요한 경우에는 열을 NVARCHAR를 사용하는 것보다 VARCHAR로 정의하세요.

[테이블 개요](develop-tables-overview.md), [테이블 데이터 형식](develop-tables-data-types.md) 및 [CREATE TABLE](/sql/t-sql/statements/create-table-azure-sql-data-warehouse?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)도 참조하세요.

### <a name="optimize-clustered-columnstore-tables"></a>클러스터형 Columnstore 테이블 최적화

columnstore 클러스터형 인덱스는 SQL 풀에 데이터를 저장할 수 있는 가장 효율적인 방법 중 하나입니다.  기본적으로 SQL 풀의 테이블은 클러스터형 ColumnStore로 만들어집니다.  

columnstore 테이블에서 쿼리에 대해 최상의 성능을 얻으려면 좋은 세그먼트 품질을 갖는 것이 중요합니다.  메모리 부족 상황에서 행이 columnstore 테이블에 기록되는 경우 columnstore 세그먼트 품질이 저하될 수 있습니다.  

세그먼트 품질은 압축된 행 그룹에 있는 행의 수로 측정할 수 있습니다.  클러스터형 columnstore 테이블에 대해 세그먼트 품질을 감지하고 개선하는 단계별 지침은 [테이블 인덱스](../sql-data-warehouse/sql-data-warehouse-tables-index.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) 문서의 [Columnstore 인덱스 품질 저하 원인](../sql-data-warehouse/sql-data-warehouse-tables-index.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json#causes-of-poor-columnstore-index-quality)을 참조하세요.  

고품질 columnstore 세그먼트가 중요하기 때문에 데이터를 로드하기 위해서는 중간 규모 또는 대규모 리소스 클래스에 속하는 사용자 ID를 사용하는 것이 좋습니다. 낮은 [데이터 웨어하우스 단위](resource-consumption-models.md)를 사용하면 로드하는 사용자에게 더 큰 리소스 클래스를 할당하려는 것입니다.

columnstore 테이블은 일반적으로 테이블당 100만 개가 넘는 행이 있고 각 SQL 풀 테이블이 60개 테이블로 분할될 때까지 데이터를 압축된 columnstore 세그먼트에 푸시하지 않으므로 테이블에서 행 수가 6천만 개를 초과하지 않는 한 columnstore 테이블은 쿼리에 이점을 제공하지 않습니다.  

> [!TIP]
> 행 수가 6천만 개 미만인 테이블에는 columstore 인덱스가 최적의 솔루션이 아닐 수 있습니다.  

또한 데이터를 분할하는 경우 클러스터형 columnstore 인덱스의 이점을 얻기 위해 각 파티션에는 100개의 행을 포함하는 것이 좋습니다.  테이블에 파티션 수가 100개라면 클러스터형 columnstore에서 이점을 얻으려면 60억 개 이상의 행을 포함해야 합니다(60개 배포 *100개 파티션* 1백만 개 행).  

테이블에 60억 개의 행이 없으면 파티션 수를 줄이거나 대신 힙 테이블을 사용하는 것이 좋습니다.  또한 실험을 통해 columnstore 테이블 대신 보조 인덱스가 있는 힙 테이블을 사용하여 더 나은 성능을 얻을 수 있는지도 확인할 수 있습니다.

columnstore 테이블을 쿼리할 때 필요한 열만 선택하면 쿼리가 더 빨리 실행됩니다.  

또한 [테이블 인덱스](../sql-data-warehouse/sql-data-warehouse-tables-index.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json), [Columnstore 인덱스 가이드](/sql/relational-databases/indexes/columnstore-indexes-overview?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), [Columnstore 인덱스 다시 빌드](../sql-data-warehouse/sql-data-warehouse-tables-index.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json#rebuilding-indexes-to-improve-segment-quality)도 참조하세요.

## <a name="sql-on-demand-development-best-practices"></a>SQL 주문형 개발 모범 사례

### <a name="general-considerations"></a>일반적인 고려 사항

SQL 주문형을 사용하여 Azure 스토리지 계정의 파일을 쿼리할 수 있습니다. 로컬 스토리지 또는 수집 기능이 없습니다. 즉, 쿼리가 대상으로 하는 모든 파일이 SQL 주문형 외부에 있습니다. 따라서 스토리지에서 파일 읽기와 관련된 모든 작업이 쿼리 성능에 영향을 줄 수 있습니다.

### <a name="colocate-azure-storage-account-and-sql-on-demand"></a>Azure Storage 계정 및 SQL 주문형 공동 배치

대기 시간을 최소화하려면 Azure 스토리지 계정과 SQL 주문형 엔드포인트를 같은 곳에 배치해야 합니다. 작업 영역을 만드는 동안 프로비저닝된 스토리지 계정과 엔드포인트는 같은 지역에 있습니다.

성능을 최적화하려면 SQL 주문형에서 다른 스토리지 계정에 액세스할 때 스토리지 계정과 SQL 주문형이 같은 지역에 있어야 합니다. 그렇지 않으면 원격 지역에서 엔드포인트 지역으로의 데이터 네트워크 전송에 걸리는 대기 시간이 증가합니다.

### <a name="azure-storage-throttling"></a>Azure Storage 제한

여러 애플리케이션과 서비스가 스토리지 계정에 액세스할 수 있습니다. 애플리케이션, 서비스 및 SQL 주문형 워크로드에서 생성한 결합 IOPS 또는 처리량이 스토리지 계정의 제한을 초과하면 스토리지 제한이 발생합니다. 스토리지 제한이 발생하면 쿼리 성능에 부정적인 영향을 미칠 수 있습니다.

SQL 주문형은 제한이 감지되면 이 시나리오를 처리하는 기능을 기본적으로 갖추고 있습니다. SQL 주문형은 제한이 해결될 때까지 느린 속도로 스토리지에 대한 요청을 만듭니다. 

그렇지만 최적의 쿼리 실행을 위해 쿼리를 실행하는 중에는 스토리지 계정에 다른 워크로드 부하를 추가하지 않는 것이 좋습니다.

### <a name="prepare-files-for-querying"></a>쿼리할 파일 준비

가능하다면 성능 향상을 위한 파일을 준비해도 됩니다.

- CSV를 Parquet로 변환합니다. Parquet는 열 형식입니다. 압축된 파일 크기는 동일한 데이터를 사용하는 CSV 파일보다 크기가 작기 때문에 SQL 주문형이 파일을 읽는 데 필요한 시간과 스토리지 요청이 줄어듭니다.
- 쿼리 대상이 대형 파일 하나인 경우 여러 개의 작은 파일로 분할하는 것이 유리합니다.
- 되도록이면 CSV 파일 크기를 10GB 미만으로 유지하세요.
- 단일 OPENROWSET 경로 또는 외부 테이블 LOCATION에 대해 크기가 동일한 파일을 지정하는 것이 좋습니다.
- 파티션을 여러 폴더 또는 파일 이름에 저장하여 데이터 분할 - [filename 및 filepath 함수를 사용하여 특정 파티션을 대상으로 지정](#use-fileinfo-and-filepath-functions-to-target-specific-partitions)을 선택합니다.

### <a name="use-fileinfo-and-filepath-functions-to-target-specific-partitions"></a>fileinfo 및 filepath 함수를 사용하여 특정 파티션을 대상으로 지정

데이터는 종종 여러 파티션으로 구성됩니다. 특정 폴더 및 파일을 쿼리하도록 SQL 주문형에 지시할 수 있습니다. 이 경우 쿼리에서 읽고 처리해야 하는 파일 수와 데이터 양이 줄어듭니다. 

따라서 성능이 개선됩니다. 자세한 내용은 [filename](query-data-storage.md#filename-function) 및 [filepath](query-data-storage.md#filepath-function) 함수와 [특정 파일을 쿼리하는 방법](query-specific-files.md)을 확인하세요.

스토리지의 데이터가 분할되지 않은 경우 데이터를 분할하면 두 함수를 사용하여 이러한 파일을 대상으로 하는 쿼리를 최적화할 수 있습니다.

SQL 주문형에서 [Azure Synapse 외부 테이블에 대한 분할된 Apache Spark를 쿼리](develop-storage-files-spark-tables.md)할 때 쿼리는 자동으로 필요한 파일만 대상으로 지정합니다.

### <a name="use-cetas-to-enhance-query-performance-and-joins"></a>CETAS를 사용하여 쿼리 성능 및 조인 향상

[CETAS](develop-tables-cetas.md)는 SQL 주문형에서 제공하는 가장 중요한 기능 중 하나입니다. CETAS는 외부 테이블 메타데이터를 만들고 SELECT 쿼리 결과를 스토리지 계정의 파일 세트로 내보내는 병렬 작업입니다.

CETAS를 사용하면 조인된 참조 테이블처럼 쿼리에서 자주 사용하는 부분을 새 파일 세트에 저장할 수 있습니다. 나중에 여러 쿼리에서 공통 조인을 반복하는 대신, 이 단일 외부 테이블에 조인할 수 있습니다. 

CETAS에서 Parquet 파일을 생성하면 첫 번째 쿼리가 이 외부 테이블을 대상으로 할 때 자동으로 통계가 생성되며 성능이 향상됩니다.

### <a name="next-steps"></a>다음 단계

이 문서에 제공되지 않는 정보가 필요한 경우 이 페이지 왼쪽에 있는 "문서 검색"을 사용하여 모든 SQL 풀 문서를 검색합니다.  [SQL 풀에 대한 Microsoft Q&A 질문 페이지](https://docs.microsoft.com/answers/topics/azure-synapse-analytics.html)는 다른 사용자와 SQL 풀 제품 그룹에 질문을 게시할 수 있는 곳입니다.  

Microsoft는 이 포럼을 적극적으로 모니터링하여 사용자의 질문에 다른 사용자나 당사 직원이 응답하도록 합니다.  스택 오버플로에 질문하는 것을 선호하는 경우 [Azure SQL 풀 Stack Overflow 포럼](https://stackoverflow.com/questions/tagged/azure-sqldw)도 제공합니다.
 
