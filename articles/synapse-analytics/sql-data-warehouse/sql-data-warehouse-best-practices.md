---
title: Azure Synapse Analytics에서 SQL 풀 Synapse에 대 한 모범 사례 (이전의 SQL DW)
description: Azure Synapse Analytics (이전의 SQL DW)에서 SQL 풀 솔루션을 개발 하기 위한 권장 사항 및 모범 사례입니다.
services: synapse-analytics
author: mlee3gsd
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 11/04/2019
ms.author: martinle
ms.reviewer: igorstan
ms.openlocfilehash: 4462bf0fc2057922340eb01cb8c786dbc63ce290
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "80745344"
---
# <a name="best-practices-for-synapse-sql-pool-in-azure-synapse-analytics-formerly-sql-dw"></a>Azure Synapse Analytics에서 SQL 풀 Synapse에 대 한 모범 사례 (이전의 SQL DW)

이 문서는 [SQL 풀](sql-data-warehouse-overview-what-is.md) 배포에서 최적의 성능을 얻는 데 도움이 되는 모범 사례 모음입니다.  이 문서의 목적은 몇 가지 기본 지침을 제공 하 고 중요 한 주요 영역을 강조 표시 하는 것입니다.  

## <a name="reduce-cost-with-pause-and-scale"></a>일시 중지 및 규모 조정으로 비용 절감

일시 중지 및 크기 조정을 통해 비용을 절감하는 방법에 대한 자세한 내용은 [컴퓨팅 관리](sql-data-warehouse-manage-compute-overview.md)를 참조하세요.

## <a name="maintain-statistics"></a>통계 유지 관리

열에 대 한 통계를 자동으로 검색 하 고 만들도록 SQL 풀을 구성할 수 있습니다.  최적화 프로그램에서 생성 하는 쿼리 계획은 사용 가능한 통계 만큼만 사용할 수 있습니다.  

데이터베이스에 대 한 AUTO_CREATE_STATISTICS를 사용 하도록 설정 하 고 매일 또는 각 로드 후 통계를 업데이트 하 여 쿼리에 사용 되는 열에 대 한 통계가 항상 최신 상태를 유지 하도록 하는 것이 좋습니다.

모든 통계를 업데이트 하는 데 시간이 너무 오래 걸리면 자주 통계를 업데이트 해야 하는 열을 선택 하는 것이 좋습니다. 예를 들어 새 값이 매일 추가될 수 있는 날짜 열을 업데이트하려고 합니다.

> [!TIP]
> 조인에 포함 된 열, WHERE 절에 사용 된 열 및 GROUP BY에 있는 열에 대 한 통계를 업데이트 하 여 가장 많은 이점을 얻을 수 있습니다.

또한 [테이블 통계 관리](sql-data-warehouse-tables-statistics.md), [통계 만들기](/sql/t-sql/statements/create-statistics-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)및 [통계 업데이트](/sql/t-sql/statements/update-statistics-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)를 참조 하세요.

## <a name="use-dmvs-to-monitor-and-optimize-your-queries"></a>DMV를 사용하여 쿼리 모니터링 및 최적화

SQL 풀에는 쿼리 실행을 모니터링 하는 데 사용할 수 있는 여러 Dmv가 있습니다.  Dmv를 사용 하 여 워크 로드 모니터링 문서에서는 실행 중인 쿼리의 세부 정보를 확인 하는 방법에 대 한 단계별 지침을 설명 합니다.  

이러한 DMV에서 쿼리를 신속하게 찾으려면 쿼리에 LABEL 옵션을 사용하는 것이 도움이 될 수 있습니다.

또한 Dmv, [LABEL](sql-data-warehouse-develop-label.md), [OPTION](/sql/t-sql/queries/option-clause-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest), [dm_exec_sessions](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-sessions-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest), [dm_pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest), [dm_pdw_request_steps](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-request-steps-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest), sys. [dm_pdw_sql_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-sql-requests-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest), [dm_pdw_dms_workers](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-dms-workers-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest), [DBCC PDW_SHOWEXECUTIONPLAN](/sql/t-sql/database-console-commands/dbcc-pdw-showexecutionplan-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)및 [dm_pdw_waits](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-waits-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)를 [사용 하 여 워크 로드 모니터링](sql-data-warehouse-manage-monitor.md)을 참조 하세요.

## <a name="tune-query-performance-with-new-product-enhancements"></a>새로운 제품 향상으로 쿼리 성능 조정

- [구체화된 뷰를 사용한 성능 조정](performance-tuning-materialized-views.md)
- [순서가 지정된 클러스터형 columnstore 인덱스를 사용한 성능 조정](performance-tuning-ordered-cci.md)
- [결과 집합 캐싱을 사용한 성능 조정](performance-tuning-result-set-caching.md)

## <a name="group-insert-statements-into-batches"></a>INSERT 문을 일괄 처리로 그룹화

INSERT 문을 사용 하 여 작은 테이블에 대 한 일회성 로드 또는 조회를 주기적으로 다시 로드 하는 경우와 같은 `INSERT INTO MyLookup VALUES (1, 'Type 1')`문을 사용 하 여 필요에 따라 수행할 수 있습니다.  

그러나 수천 또는 수백만 행을 하루 종일 로드해야 하는 경우 단일 항목 INSERTS만으로는 불가능할 수 있습니다.  대신 파일에 기록하는 프로세스를 개발하고 다른 프로세스를 주기적으로 함께 제공하여 이 파일을 로드합니다.

또한 [INSERT](/sql/t-sql/statements/insert-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)를 참조 하세요.

## <a name="use-polybase-to-load-and-export-data-quickly"></a>PolyBase를 사용하여 데이터를 신속하고 로드하고 내보내기

SQL 풀은 Azure Data Factory, PolyBase 및 BCP를 비롯 한 여러 도구를 통해 데이터를 로드 하 고 내보내는 것을 지원 합니다.  성능이 중요하지 않은 소량의 데이터에는 어떤 도구를 사용해도 사용자 요구 사항을 충족할 수 있습니다.  그러나 대량의 데이터를 로드 또는 내보내거나 빠른 성능이 필요한 경우 PolyBase가 가장 좋습니다.  

PolyBase는 MPP (대규모 병렬 처리) 아키텍처를 활용 하도록 설계 되었으며 다른 도구 보다 더 빨리 크고 많을 데이터를 로드 하 고 내보냅니다.  PolyBase 로드는 CTAS 또는 INSERT INTO를 사용하여 실행할 수 있습니다.  

> [!TIP]
> CTAS를 사용하여 트랜잭션 로깅을 최소화하고 CTAS는 데이터를 로드하는 가장 빠른 방법입니다.

또한 Azure Data Factory는 PolyBase 로드를 지원하며 CTAS와 비슷한 성능을 얻을 수 있습니다.  PolyBase는 Gzip 파일을 포함한 다양한 파일 형식을 지원합니다.  
  
> [!NOTE]
> Gzip 텍스트 파일을 사용할 때 처리량을 최대화 하려면 파일을 60 이상으로 분할 하 여 부하의 병렬 처리를 최대화 합니다.   총 처리량을 더 빠르게 하기 위해 데이터를 동시에 로드하는 것이 좋습니다.

또한 [데이터 로드](design-elt-data-loading.md), [PolyBase 사용 가이드](guidance-for-loading-data.md), [SQL 풀 로드 패턴 및 전략](https://blogs.msdn.microsoft.com/sqlcat/20../../), Azure Data Factory 데이터 [로드]( ../../data-factory/load-azure-sql-data-warehouse.md), [Azure Data Factory 데이터 이동](../../data-factory/transform-data-using-machine-learning.md), [외부 파일 형식 만들기](/sql/t-sql/statements/create-external-file-format-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)및 [ctas (create table as select)](sql-data-warehouse-develop-ctas.md)를 참조 하세요.

## <a name="load-then-query-external-tables"></a>외부 테이블 로드 후 쿼리

외부 테이블이라고도 하는 Polybase는 데이터를 로드하는 가장 빠른 방법일 수 있지만 쿼리에 적합하지는 않습니다. Polybase 테이블은 현재 Azure blob 파일 및 Azure Data Lake 저장소만 지원 합니다. 이러한 파일에는 지원 컴퓨팅 리소스가 없습니다.  

따라서 SQL 풀은이 작업을 오프 로드할 수 없으므로 데이터를 읽기 위해 tempdb로 로드 하 여 전체 파일을 읽어야 합니다.  따라서 이 데이터를 쿼리하는 쿼리가 여러 개 있는 경우 이 데이터를 한 번 로드한 후 쿼리에서 로컬 테이블을 사용하는 것이 더 좋습니다.

또한 [PolyBase 사용에 대 한 가이드를](guidance-for-loading-data.md)참조 하세요.

## <a name="hash-distribute-large-tables"></a>해시 배포 대형 테이블

기본적으로 테이블은 라운드 로빈 분산됩니다.  따라서 사용자는 해당 테이블이 배포되는 방식을 결정하지 않고도 테이블 생성을 간편하게 시작할 수 있습니다.  라운드 로빈 테이블은 일부 워크로드에 대해서는 잘 작동하지만 대체로 배포 열을 선택하면 성능이 훨씬 향상됩니다.  

열로 배포된 테이블이 라운드 로빈 테이블의 성능을 능가하는 가장 일반적인 예는 두 개의 대형 팩트 테이블이 조인된 경우입니다.  

예를 들어 order_id로 배포되는 주문 테이블과 order_id로 배포되는 트랜잭션 테이블이 있고 주문 테이블을 order_id로 트랜잭션 테이블에 조인하는 경우 이 쿼리는 통과 쿼리가 되어 데이터 이동 작업을 제거합니다.  단계가 적을수록 쿼리는 빨라집니다.  데이터 이동이 적을수록 쿼리는 빨라집니다.  

> [!TIP]
> 분산된 테이블을 로드할 때 로드가 느려지므로 들어오는 데이터가 배포 키로 정렬되지 않도록 합니다.  

배포 열을 선택 하 여 성능을 향상 시킬 수 있는 방법 및 CREATE TABLE 문의 WITH 절에서 분산 테이블을 정의 하는 방법에 대 한 자세한 내용은 다음 링크를 참조 하십시오.

참고 항목 [테이블 개요](sql-data-warehouse-tables-overview.md), [테이블 배포](sql-data-warehouse-tables-distribute.md), [테이블 배포 선택](https://blogs.msdn.microsoft.com/sqlcat/20../../choosing-hash-distributed-table-vs-round-robin-distributed-table-in-azure-sql-dw-service/), [CREATE TABLE](/sql/t-sql/statements/create-table-azure-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) [CREATE TABLE 선택](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)합니다.

## <a name="do-not-over-partition"></a>과도한 분할 피하기

분할 데이터는 파티션 전환을 통해 데이터를 유지 관리 하거나 파티션 제거를 사용 하 여 검색을 최적화 하는 데 효과적일 수 있지만 너무 많은 파티션이 있으면 쿼리 속도가 느려질 수 있습니다.  경우에 따라 SQL Server에서 잘 작동 하는 높은 세분성 분할 전략이 SQL 풀에서 제대로 작동 하지 않을 수 있습니다.  

너무 많은 파티션을 포함하면 각 파티션에 100만 개 미만의 행이 포함된 경우 클러스터형 columnstore 인덱스의 효율성이 떨어질 수도 있습니다.  내부적으로 SQL 풀은 데이터를 60 데이터베이스로 분할 하므로 100 파티션이 포함 된 테이블을 만들면 실제로 6000 파티션이 발생 합니다.  

각 워크로드가 서로 다르므로 가장 좋은 방법은 분할 실험을 통해 사용자의 워크로드에 가장 적합한 방법을 찾는 것입니다.  SQL Server에서 작업한 것보다 낮은 세분성을 고려합니다.  예를 들어 매일 분할보다는 매주 또는 매달 분할을 사용하는 것이 좋습니다.

[테이블 분할](sql-data-warehouse-tables-partition.md)도 참조 하세요.

## <a name="minimize-transaction-sizes"></a>트랜잭션 크기 최소화

INSERT, UPDATE, DELETE 문은 트랜잭션에서 실행되며 실패할 경우 롤백해야 합니다.  긴 롤백에 대한 가능성을 최소화하려면 가능한 경우 항상 트랜잭션 크기를 최소화합니다.  INSERT, UPDATE 및 DELETE 문을 부분으로 나누어 이를 수행할 수 있습니다.  

예를 들어 1 시간을 소요 하는 삽입이 있는 경우 가능 하면 삽입을 4 개의 부분으로 분할 합니다. 그러면 각각 15 분 이내에 실행 됩니다.  CTAS, TRUNCATE, DROP TABLE 또는 INSERT to empty tables와 같은 특수 한 최소 로깅 사례를 활용 하 여 롤백 위험을 줄입니다.  

롤백을 제거하는 다른 방법은 데이터 관리를 위한 파티션 전환과 같은 메타데이터 전용 작업을 사용하는 것입니다.  예를 들어 order_date가 2001 년 10 월에 있었던 테이블의 모든 행을 삭제 하는 DELETE 문을 실행 하는 대신 매월 데이터를 분할 한 다음 다른 테이블의 빈 파티션에 대 한 데이터를 사용 하 여 파티션을 전환할 수 있습니다 ( [ALTER table](/sql/t-sql/statements/alter-table-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) 예제 참조).  

분할 되지 않은 테이블의 경우 DELETE를 사용 하는 대신 CTAS를 사용 하 여 테이블에 유지할 데이터를 작성 하는 것이 좋습니다.  CTAS가 같은 시간을 사용 하는 경우 최소한의 트랜잭션 로깅을 사용 하 고 필요한 경우 신속 하 게 취소할 수 있으므로 실행 하는 것이 훨씬 안전 합니다.

[트랜잭션 이해](sql-data-warehouse-develop-transactions.md), [트랜잭션 최적화](sql-data-warehouse-develop-best-practices-transactions.md), [테이블 분할](sql-data-warehouse-tables-partition.md), [TRUNCATE TABLE](/sql/t-sql/statements/truncate-table-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest), [ALTER table](/sql/t-sql/statements/alter-table-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)및 [ctas (Create Table as select)](sql-data-warehouse-develop-ctas.md)도 참조 하세요.

## <a name="reduce-query-result-sizes"></a>쿼리 결과 크기 줄이기

이 단계를 통해 많은 쿼리 결과로 인해 발생 하는 클라이언트 쪽 문제를 방지할 수 있습니다.  쿼리를 편집 하 여 반환 되는 행 수를 줄일 수 있습니다. 일부 쿼리 생성 도구를 사용 하면 각 쿼리에 "top N" 구문을 추가할 수 있습니다.  쿼리 결과를 임시 테이블에 CETAS 다음 하위 수준 처리를 위해 PolyBase 내보내기를 사용할 수도 있습니다.

## <a name="use-the-smallest-possible-column-size"></a>가능한 가장 작은 열 크기 사용

DDL을 정의할 때 데이터를 지원할 가장 작은 데이터 형식을 사용 하면 쿼리 성능이 향상 됩니다.  이 방법은 CHAR 및 VARCHAR 열에 특히 중요 합니다.  

열에서 가장 긴 값이 25자인 경우 열을 VARCHAR(25)로 정의합니다.  모든 문자 열을 큰 기본 길이로 정의하지 마세요.  또한 반드시 필요한 경우에는 열을 NVARCHAR를 사용하는 것보다 VARCHAR로 정의하세요.

참고 항목 [테이블 개요](sql-data-warehouse-tables-overview.md), [테이블 데이터 형식](sql-data-warehouse-tables-data-types.md), [CREATE TABLE](/sql/t-sql/statements/create-table-azure-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)합니다.

## <a name="use-temporary-heap-tables-for-transient-data"></a>임시 데이터에 대해 임시 힙 테이블 사용

데이터를 일시적으로 방문 하는 경우 힙 테이블을 사용 하면 전체 프로세스가 더 빠르게 수행 될 수 있습니다.  추가 변환을 실행하기 전의 준비 과정으로만 데이터를 로드하는 경우 힙 테이블에 테이블을 로드하는 것이 클러스터형 columnstore 테이블에 데이터를 로드하는 것보다 훨씬 더 빠릅니다.  

또한 데이터를 임시 테이블에 로드하면 테이블을 영구 스토리지에 로드할 때보다 훨씬 빠르게 로드됩니다.  임시 테이블은 "#"으로 시작 하 고이를 만든 세션 에서만 액세스할 수 있으므로 제한 된 시나리오 에서만 작동할 수 있습니다.

힙 테이블은 CREATE TABLE의 WITH 절에 정의됩니다.  임시 테이블을 사용하는 경우 해당 임시 테이블에서도 통계를 작성해야 합니다.

[임시 테이블](sql-data-warehouse-tables-temporary.md), [CREATE TABLE](/sql/t-sql/statements/create-table-azure-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest), [CREATE TABLE AS SELECT](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)도 참조 하세요.

## <a name="optimize-clustered-columnstore-tables"></a>클러스터형 Columnstore 테이블 최적화

클러스터형 columnstore 인덱스는 SQL 풀에 데이터를 저장할 수 있는 가장 효율적인 방법 중 하나입니다.  기본적으로 SQL 풀의 테이블은 클러스터형 ColumnStore로 생성 됩니다.  columnstore 테이블에서 쿼리에 대해 최상의 성능을 얻으려면 좋은 세그먼트 품질을 갖는 것이 중요합니다.  

메모리 부족 상황에서 행이 columnstore 테이블에 기록되는 경우 columnstore 세그먼트 품질이 저하될 수 있습니다.  세그먼트 품질은 압축된 행 그룹에 있는 행의 수로 측정할 수 있습니다.  클러스터형 columnstore 테이블에 대 한 세그먼트 품질을 감지 하 고 개선 하는 단계별 지침은 [테이블 인덱스](sql-data-warehouse-tables-index.md) 에서 [columnstore 인덱스 품질 저하의 원인](sql-data-warehouse-tables-index.md#causes-of-poor-columnstore-index-quality) 을 참조 하세요.  

고품질 columnstore 세그먼트가 중요 하기 때문에 데이터를 로드 하기 위해 중간 규모 또는 대규모 리소스 클래스에 있는 사용자 Id를 사용 하는 것이 좋습니다. 낮은 [데이터 웨어하우스 단위](what-is-a-data-warehouse-unit-dwu-cdwu.md)를 사용하면 로드하는 사용자에게 더 큰 리소스 클래스를 할당하려는 것입니다.

Columnstore 테이블은 일반적으로 테이블당 100만 개 이상의 행이 있을 때까지 데이터를 압축 된 columnstore 세그먼트로 푸시 하지 않으며 각 SQL 풀 테이블이 60 테이블로 분할 되기 때문에 (예를 들어) 테이블에 6000만 개 이상의 행이 있는 경우를 제외 하 고 columnstore 테이블은 쿼리에 유용 하지 않습니다.  6천만 개 미만의 행이 있는 테이블의 경우 columnstore 인덱스를 포함하는 것이 적절하지 않을 수 있습니다.  오히려 상황을 악화시킬 수 있습니다.  

또한 데이터를 분할하는 경우 클러스터형 columnstore 인덱스의 이점을 얻기 위해 각 파티션에는 100개의 행을 포함하는 것이 좋습니다.  테이블에 100 개의 파티션이 있는 경우 클러스터형 열 저장소 (60 배포판 *100 파티션* 100만 행)를 활용 하려면 60억 행 이상이 필요 합니다.  

이 예에서 테이블에 60억 개의 행이 없으면 파티션 수를 줄이거나 대신 힙 테이블을 사용하는 것이 좋습니다.  또한 실험을 통해 columnstore 테이블 대신 보조 인덱스가 있는 힙 테이블로 더 나은 성능을 얻을 수 있는지도 확인할 수 있습니다.

> [!TIP]
> columnstore 테이블을 쿼리할 때 필요한 열만 선택하면 쿼리가 더 빨리 실행됩니다.  

참고 항목: [테이블 인덱스](sql-data-warehouse-tables-index.md), [Columnstore 인덱스 가이드](/sql/relational-databases/indexes/columnstore-indexes-overview?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest), [Columnstore 인덱스 다시 빌드](sql-data-warehouse-tables-index.md#rebuilding-indexes-to-improve-segment-quality)

## <a name="use-larger-resource-class-to-improve-query-performance"></a>더 큰 리소스 클래스를 사용하여 쿼리 성능 향상

SQL 풀은 쿼리에 메모리를 할당 하는 방법으로 리소스 그룹을 사용 합니다.  기본적으로 모든 사용자는 배포 당 100 MB의 메모리를 부여 하는 작은 리소스 클래스에 할당 됩니다.  항상 60개의 배포가 있고 각 배포에는 최소 100MB가 부여되므로 시스템 전체에서 총 메모리 할당량은 6,000MB이거나 6GB가 약간 못됩니다.  

큰 조인 또는 클러스터형 columnstore 테이블에 로드와 같은 특정 쿼리는 큰 메모리를 할당하는 것이 도움이 됩니다.  순수한 검색과 같은 일부 쿼리는 아무런 이점도 제공 하지 않습니다.  그러나 더 큰 리소스 클래스를 사용 하면 동시성이 감소 되므로 모든 사용자를 큰 리소스 클래스로 이동 하기 전에 이러한 영향을 고려 하는 것이 좋습니다.

또한 [워크 로드 관리를 위한 리소스 클래스를](resource-classes-for-workload-management.md)참조 하세요.

## <a name="use-smaller-resource-class-to-increase-concurrency"></a>더 작은 리소스 클래스를 사용하여 동시성 증가

사용자 쿼리 시간이 오래 걸리는 것으로 보이는 경우 사용자가 더 큰 리소스 클래스에서 실행 중이 고 다른 쿼리를 큐에 대기 시키는 많은 동시성 슬롯을 사용 하 고 있을 수 있습니다.  사용자 쿼리가 큐에 대기되었는지 확인하려면 `SELECT * FROM sys.dm_pdw_waits` 를 실행하여 모든 행이 반환되었는지 확인합니다.

또한 [워크 로드 관리를 위한 리소스 클래스](resource-classes-for-workload-management.md), [dm_pdw_waits](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-waits-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)을 참조 하세요.

## <a name="other-resources"></a>기타 리소스

또한 일반적인 문제 및 해결 방법에 대해서는 [문제 해결](sql-data-warehouse-troubleshoot.md) 문서를 참조하세요.

이 문서에서 원하는 항목을 찾지 못한 경우이 페이지의 왼쪽에 있는 "문서 검색"을 사용 하 여 모든 Azure Synapse 문서를 검색 해 보세요.  [Azure Synapse 포럼](https://social.msdn.microsoft.com/Forums/sqlserver/home?forum=AzureSQLDataWarehouse) 은 다른 사용자 및 Azure Synapse 제품 그룹에 질문을 게시할 수 있는 장소입니다. Microsoft는 이 포럼을 적극적으로 모니터링하여 사용자의 질문에 다른 사용자나 당사 직원이 응답하도록 합니다.  

Stack Overflow에 대 한 질문을 하는 것을 선호 하는 경우 [Azure Synapse Stack Overflow 포럼](https://stackoverflow.com/questions/tagged/azure-sqldw)도 있습니다.

기능 요청을 만들려면 [Azure Synapse 피드백](https://feedback.azure.com/forums/307516-sql-data-warehouse) 페이지를 사용 하세요.  요청을 추가하거나 다른 요청에 투표를 하면 기능의 순위를 지정하는 데 도움이 됩니다.
