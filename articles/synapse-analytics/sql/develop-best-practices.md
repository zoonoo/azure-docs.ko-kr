---
title: 시냅스 SQL을 위한 개발 모범 사례
description: Synapse SQL을 개발할 때 알아야 할 권장 사항 및 모범 사례입니다.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/15/2020
ms.author: xiaoyul
ms.reviewer: igorstan
ms.openlocfilehash: 3356817fbaef955e05358f9c92619f3c04f747d7
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81429617"
---
# <a name="development-best-practices-for-synapse-sql"></a>시냅스 SQL을 위한 개발 모범 사례
이 문서에서는 데이터 웨어하우스 솔루션을 개발할 때 도움이 되는 지침 및 모범 사례에 대해 설명합니다. 

## <a name="development-best-practices-for-synapse-sql"></a>시냅스 SQL을 위한 개발 모범 사례

### <a name="reduce-cost-with-pause-and-scale"></a>일시 중지 및 규모 조정으로 비용 절감

일시 중지 및 크기 조정을 통해 비용을 절감하는 방법에 대한 자세한 내용은 [컴퓨팅 관리](../sql-data-warehouse/sql-data-warehouse-manage-compute-overview.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)를 참조하세요.

### <a name="maintain-statistics"></a>통계 유지 관리

매일 또는 각 로드 후에 통계를 업데이트해야 합니다.  통계를 작성하고 업데이트하는 성능 및 비용 간의 장단점은 항상 있습니다. 모든 통계를 유지하는 데 시간이 너무 오래 걸리는 경우 통계가 있는 열이나 자주 업데이트해야 하는 열에 대해 더 선택적으로 설정해야 합니다.  

예를 들어 매일 새 값을 추가할 수 있는 날짜 열을 업데이트할 수 있습니다. **조인과 관련된 열, WHERE 절에 사용된 열 및 GROUP BY에 있는 열에 대한 통계를 통해 가장 많은 이점을 얻을 수 있습니다.**

또한 [참조 테이블 통계 관리,](develop-tables-statistics.md) [통계 생성,](develop-tables-statistics.md) [업데이트 통계][UPDATE STATISTICS].

### <a name="hash-distribute-large-tables"></a>해시 배포 대형 테이블

기본적으로 테이블은 라운드 로빈 분산됩니다.  이렇게 하면 사용자가 테이블을 배포하는 방법을 결정할 필요 없이 쉽게 테이블 만들기를 시작할 수 있습니다.  라운드 로빈 테이블은 일부 워크로드에 대해 충분히 수행할 수 있습니다. 그러나 대부분의 경우 배포 열을 선택하면 훨씬 더 나은 성능이 향상됩니다.  

열로 배포된 테이블이 라운드 로빈 테이블의 성능을 능가하는 가장 일반적인 예는 두 개의 대형 팩트 테이블이 조인된 경우입니다.  예를 들어 order_id 의해 배포되는 주문 테이블과 order_id 의해 배포되는 트랜잭션 테이블이 있는 경우 order_id 주문 테이블을 트랜잭션 테이블에 조인하면 이 쿼리는 통과 쿼리가 됩니다. 즉, 데이터 이동 작업을 제거합니다.  단계가 적을수록 쿼리는 빨라집니다.  데이터 이동이 적을수록 쿼리는 빨라집니다.

분산된 테이블을 로드할 때 로드가 느려지므로 들어오는 데이터가 배포 키로 정렬되지 않도록 합니다.  DISTRIBUTION 열을 선택하면 성능이 향상되는 방법과 CREATE TABLES 문의 WITH 절에서 분산 테이블을 정의하는 방법에 대한 자세한 내용은 아래 링크를 참조하십시오.

또한 [표 개요,][Table overview] [테이블 분포,][Table distribution] [테이블 분포 선택,][Selecting table distribution] [테이블 만들기,][CREATE TABLE] [선택으로 테이블 만들기][CREATE TABLE AS SELECT]를 참조하십시오.

### <a name="do-not-over-partition"></a>과도한 분할 피하기
데이터를 분할하는 것은 파티션 전환을 통해 데이터를 유지 관리하거나 파티션 제거를 통해 스캔을 최적화하는 데 효과적일 수 있지만 파티션이 너무 많면 쿼리 속도가 느려질 수 있습니다.  SQL Server에서 잘 작동할 수 있는 높은 세분성 분할 전략이 SQL 풀에서 잘 작동하지 않는 경우가 많습니다.  

너무 많은 파티션을 포함하면 각 파티션에 100만 개 미만의 행이 포함된 경우 클러스터형 columnstore 인덱스의 효율성이 떨어질 수도 있습니다. SQL 풀은 데이터를 60개의 데이터베이스로 분할합니다. 따라서 파티션이 100개인 테이블을 만들면 6000개의 파티션이 생성됩니다.  각 워크로드가 서로 다르므로 가장 좋은 방법은 분할 실험을 통해 사용자의 워크로드에 가장 적합한 방법을 찾는 것입니다.  

고려해야 할 한 가지 옵션은 SQL Server에서 작업한 것보다 낮은 세분성을 사용하는 것입니다.  예를 들어 매일 분할보다는 매주 또는 매달 분할을 사용하는 것이 좋습니다.

또한 [표 분할을][Table partitioning]참조하십시오.

### <a name="minimize-transaction-sizes"></a>트랜잭션 크기 최소화

INSERT, UPDATE, DELETE 문은 트랜잭션에서 실행되며 실패할 경우 롤백해야 합니다.  긴 롤백에 대한 가능성을 최소화하려면 가능한 경우 항상 트랜잭션 크기를 최소화합니다.  INSERT, UPDATE 및 DELETE 문을 부분으로 나누어 이를 수행할 수 있습니다.  예를 들어 1시간이 걸릴 것으로 예상되는 INSERT가 있는 경우 INSERT를 네 부분으로 나누면 각 실행을 15분으로 단축할 수 있습니다.

CTAS, TRUNCATE, DROP TABLE 또는 INSERT와 같은 특수한 로깅 사례를 최소한 활용하여 롤백 위험을 줄입니다.  롤백을 제거하는 다른 방법은 데이터 관리를 위한 파티션 전환과 같은 메타데이터 전용 작업을 사용하는 것입니다.  예를 들어 테이블에서 order_date가 2001년 10월인 모든 행을 제거하기 위해 DELETE 문을 실행하는 대신 데이터를 매달 분할한 후 해당 파티션을 다른 테이블의 비어 있는 파티션의 데이터로 전환할 수 있습니다(ALTER TABLE 예 참조).  

분할되지 않은 테이블의 경우 DELETE를 사용하는 대신, 테이블에 유지할 데이터를 기록하도록 CTAS를 사용하는 것이 좋습니다.  CTAS에 동일한 시간이 소요되는 경우 매우 최소한의 트랜잭션 로깅을 포함하므로 실행하기에 훨씬 더 안전한 작업이며 필요할 경우 신속하게 취소할 수 있습니다.

또한 [트랜잭션 이해,][Understanding transactions] [트랜잭션 최적화,][Optimizing transactions] [테이블 분할,][Table partitioning] [TRUNCATE 테이블,][TRUNCATE TABLE] [변경 테이블,][ALTER TABLE] [선택(CTAS)으로 테이블 만들기][Create table as select (CTAS)]를 참조하십시오.

### <a name="use-the-smallest-possible-column-size"></a>가능한 가장 작은 열 크기 사용

DDL을 정의할 때 데이터를 지원할 가장 작은 데이터 형식을 사용하면 쿼리 성능이 향상됩니다.  이것은 CHAR 및 VARCHAR 열에 대해 특히 중요합니다.  열에서 가장 긴 값이 25자인 경우 열을 VARCHAR(25)로 정의합니다.  모든 문자 열을 큰 기본 길이로 정의하지 마세요.  또한 반드시 필요한 경우에는 열을 NVARCHAR를 사용하는 것보다 VARCHAR로 정의하세요.

참고 항목: [테이블 개요][Table overview], [테이블 데이터 형식](develop-tables-data-types.md), [CREATE TABLE][CREATE TABLE]

### <a name="optimize-clustered-columnstore-tables"></a>클러스터형 Columnstore 테이블 최적화

클러스터된 columnstore 인덱스는 SQL 풀에 데이터를 저장할 수 있는 가장 효율적인 방법 중 하나입니다.  기본적으로 SQL 풀의 테이블은 클러스터된 ColumnStore로 만들어집니다.  columnstore 테이블에서 쿼리에 대해 최상의 성능을 얻으려면 좋은 세그먼트 품질을 갖는 것이 중요합니다.  메모리 부족 상황에서 행이 columnstore 테이블에 기록되는 경우 columnstore 세그먼트 품질이 저하될 수 있습니다.  

세그먼트 품질은 압축된 행 그룹에 있는 행의 수로 측정할 수 있습니다.  클러스터형 columnstore 테이블에 대해 세그먼트 품질을 감지하고 개선하는 단계별 지침은 [테이블 인덱스][Table indexes] 문서의 [Columnstore 인덱스 품질 저하 원인][Causes of poor columnstore index quality] 섹션을 참조하세요.  고품질 columnstore 세그먼트가 중요하기 때문에 데이터를 로드하기 위해서는 중간 규모 또는 대규모 리소스 클래스에 속하는 사용자 ID를 사용하는 것이 좋습니다. 낮은 [데이터 웨어하우스 단위](azure -synapse-resource-consumption-models.md)를 사용하면 로드하는 사용자에게 더 큰 리소스 클래스를 할당하려는 것입니다.

columnstore 테이블은 일반적으로 테이블당 1백만 개 이상의 행이 있고 각 SQL 풀테이블이 60개의 테이블로 분할될 때까지 압축된 columnstore 세그먼트로 데이터를 푸시하지 않으므로 테이블에 6천만 개 이상의 행이 있는 경우를 제외하면 열저장소 테이블은 쿼리에 도움이 되지 않습니다.  행이 6천만 개 미만인 테이블의 경우 columstore 인덱스를 갖는 것이 최적의 솔루션이 아닐 수 있습니다.  

또한 데이터를 분할하는 경우 클러스터형 columnstore 인덱스의 이점을 얻기 위해 각 파티션에는 100개의 행을 포함하는 것이 좋습니다.  테이블에 100개의 파티션이 있는 경우 클러스터된 열 저장소(60개의 배포판 *100개 파티션 1백만* 행)의 이점을 얻으려면 최소 60억 개의 행이 있어야 합니다.  

테이블에 60억 개의 행이 없는 경우 파티션 수를 줄이거나 대신 힙 테이블을 사용하는 것이 좋습니다.  또한 columnstore 테이블이 아닌 보조 인덱스가 있는 힙 테이블을 사용하여 더 나은 성능을 얻을 수 있는지 실험해 볼 수도 있습니다.

columnstore 테이블을 쿼리할 때 필요한 열만 선택하면 쿼리가 더 빨리 실행됩니다.  

또한 [테이블 인덱스,][Table indexes] [열 저장소 인덱스 가이드,][Columnstore indexes guide] [열 저장소 인덱스 다시 빌드][Rebuilding columnstore indexes]를 참조하십시오.

### <a name="next-steps"></a>다음 단계

이 문서에서 찾고 있는 내용을 찾지 못한 경우 이 페이지 왼쪽에 있는 "문서 검색"을 사용하여 모든 Azure SQL 풀 문서를 검색해 보십시오.  [Azure SQL 풀 포럼은][Azure SQL pool MSDN Forum] 다른 사용자와 SQL 풀 제품 그룹에 질문을 제기하는 곳입니다.  

Microsoft는 이 포럼을 적극적으로 모니터링하여 사용자의 질문에 다른 사용자나 당사 직원이 응답하도록 합니다.  스택 오버플로에 대한 질문을 하려는 경우 Azure [SQL 풀 스택 오버플로 포럼도][Azure SQL pool Stack Overflow Forum]있습니다.

Azure [SQL 풀 피드백][Azure SQL pool Feedback] 페이지를 사용하여 기능을 요청합니다.  요청을 추가하거나 다른 요청에 투표를 하면 기능의 순위를 지정하는 데 도움이 됩니다.

<!--Image references-->

<!--Article references-->
[Create a support ticket]: ../sql-data-warehouse/sql-data-warehouse-get-started-create-support-ticket.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json
[Create table as select (CTAS)]: ../sql-data-warehouse/sql-data-warehouse-develop-ctas.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json
[Table overview]:develop-tables-overview.md
[Table distribution]: ../sql-data-warehouse/sql-data-warehouse-tables-distribute.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json
[Table indexes]: ../sql-data-warehouse/sql-data-warehouse-tables-index.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json
[Causes of poor columnstore index quality]: ../sql-data-warehouse/sql-data-warehouse-tables-index.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json#causes-of-poor-columnstore-index-quality
[Rebuilding columnstore indexes]: ../sql-data-warehouse/sql-data-warehouse-tables-index.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json#rebuilding-indexes-to-improve-segment-quality
[Table partitioning]: ../sql-data-warehouse/sql-data-warehouse-tables-partition.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json
[Temporary tables]:develop-tables-temporary.md
[Guide for using PolyBase]:../sql-analytics/data-loading-best-practices.md
[Load data]: ../sql-data-warehouse/esign-elt-data-loading.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json
[Move data with Azure Data Factory]: ../../data-factory/transform-data-using-machine-learning.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json
[Load data with Azure Data Factory]: ../../data-factory/load-azure-sql-data-warehouse.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json
[Load data with bcp]: /sql/tools/bcp-utility
[Load data with PolyBase]: ../sql-data-warehouse/load-data-wideworldimportersdw.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json
[Monitor your workload using DMVs]: ../sql-data-warehouse/sql-data-warehouse-manage-monitor.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json
[Pause compute resources]: ../sql-data-warehouse/sql-data-warehouse-manage-compute-overview.md#pause-compute-bk?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json
[Resume compute resources]: ../sql-data-warehouse/sql-data-warehouse-manage-compute-overview.md#resume-compute-bk?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json
[Scale compute resources]: ../sql-data-warehouse/sql-data-warehouse-manage-compute-overview.md#scale-compute?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json
[Understanding transactions]:develop-transactions.md
[Optimizing transactions]: ../sql-data-warehouse/sql-data-warehouse-develop-best-practices-transactions.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json
[Troubleshooting]: ../sql-data-warehouse/sql-data-warehouse-troubleshoot.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json
[LABEL]:develop-label.md

<!--MSDN references-->
[ALTER TABLE]: /sql/t-sql/statements/alter-table-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest
[CREATE EXTERNAL FILE FORMAT]: /sql/t-sql/statements/create-external-file-format-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest
[CREATE STATISTICS]: /sql/t-sql/statements/create-statistics-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest
[CREATE TABLE]: /sql/t-sql/statements/create-table-azure-sql-data-warehouse?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest
[CREATE TABLE AS SELECT]: /sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest
[DBCC PDW_SHOWEXECUTIONPLAN]: /sql/t-sql/database-console-commands/dbcc-pdw-showexecutionplan-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest
[INSERT]: /sql/t-sql/statements/insert-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest
[OPTION]: /sql/t-sql/queries/option-clause-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest
[TRUNCATE TABLE]: /sql/t-sql/statements/truncate-table-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest
[UPDATE STATISTICS]: /sql/t-sql/statements/update-statistics-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest
[sys.dm_exec_sessions]: /sql/relational-databases/system-dynamic-management-views/sys-dm-exec-sessions-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest
[sys.dm_pdw_exec_requests]: /sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest
[sys.dm_pdw_request_steps]: /sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-request-steps-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest
[sys.dm_pdw_sql_requests]: /sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-sql-requests-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest
[sys.dm_pdw_dms_workers]: /sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-dms-workers-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest
[sys.dm_pdw_waits]: /sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-waits-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest
[Columnstore indexes guide]: /sql/relational-databases/indexes/columnstore-indexes-overview?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest

<!--Other Web references-->
[Selecting table distribution]: https://blogs.msdn.microsoft.com/sqlcat/20../../choosing-hash-distributed-table-vs-round-robin-distributed-table-in-azure-sql-dw-service/
[Azure SQL pool Feedback]: https://feedback.azure.com/forums/307516-sql-data-warehouse
[Azure SQL pool MSDN Forum]: https://social.msdn.microsoft.com/Forums/sqlserver/home?forum=AzureSQLDataWarehouse
[Azure SQL pool Stack Overflow Forum]:  https://stackoverflow.com/questions/tagged/azure-sqldw
[Azure SQL pool loading patterns and strategies]: https://blogs.msdn.microsoft.com/sqlcat/20../../azure-sql-data-warehouse-loading-patterns-and-strategies/

## <a name="development-best-practices-for-sql-on-demand-preview"></a>SQL 온디맨드 개발 모범 사례(미리 보기)

### <a name="general-considerations"></a>일반적인 고려 사항

SQL 온디맨드를 사용하면 Azure 저장소 계정의 파일을 쿼리할 수 있습니다. 로컬 저장소 또는 편집 기능이 없으므로 쿼리가 대상으로 하는 모든 파일이 주문형 SQL 외부에 있음을 의미합니다. 따라서 저장소에서 파일을 읽는 것과 관련된 모든 것이 쿼리 성능에 영향을 미칠 수 있습니다.

### <a name="colocate-azure-storage-account-and-sql-on-demand"></a>Azure 저장소 계정 및 SQL 온디맨드 할당

 
대기 시간을 최소화하려면 Azure 저장소 계정과 SQL 온디맨드 엔드포인트를 공동 배치합니다. 작업 영역을 만드는 동안 프로비전된 저장소 계정 및 끝점은 동일한 지역에 있습니다. 
 
최적의 성능을 위해 SQL 온디맨드를 통해 다른 저장소 계정에 액세스하는 경우 동일한 지역에 있는지 확인합니다. 그렇지 않으면 원격 리전에서 엔드포인트 리전으로 데이터 네트워크 전송에 대한 대기 시간이 증가합니다.

### <a name="azure-storage-throttling"></a>Azure 저장소 제한

여러 응용 프로그램 및 서비스가 저장소 계정에 액세스할 수 있습니다. 응용 프로그램, 서비스 및 SQL 온디맨드 워크로드에서 생성된 결합된 IOPS 또는 처리량이 저장소 계정의 제한을 초과하면 저장소 제한이 발생합니다. 저장소 제한이 발생하면 쿼리 성능에 상당한 부정적인 영향을 미칩니다. 
 
제한이 검색되면 주문형 SQL이 이 시나리오를 기본 제공처리합니다. 주문형 SQL은 제한이 해결될 때까지 더 느린 속도로 저장소에 대한 요청을 만듭니다. 그러나 최적의 쿼리 실행을 위해 쿼리 실행 중에 저장소 계정에 다른 워크로드에 스트레스를 주는 것이 좋습니다.

### <a name="prepare-files-for-querying"></a>쿼리할 파일 준비

가능하면 더 나은 성능을 위해 파일을 준비할 수 있습니다.

- 마루로 CSV변환 - 마루는 열 형식입니다. 압축되기 때문에 동일한 데이터를 가진 CSV 파일보다 파일 크기가 작으며 SQL 온디맨드 에서 읽기 위한 시간과 저장소 요청이 줄어듭니다.
- 쿼리가 하나의 대용량 파일을 대상으로 하는 경우 여러 개의 작은 파일로 분할하면 이점이 있습니다.
- CSV 파일 크기를 10GB 미만으로 유지해 보십시오.
- 단일 OPENROWSET 경로 또는 외부 테이블 위치에 대해 크기가 동등한 파일을 두는 것이 좋습니다.
- 다른 폴더 또는 파일 이름에 파티션을 저장하여 데이터를 분할 - [특정 파티션을 대상으로 파일 이름 및 파일 경로 함수를 사용하여 확인합니다.](#use-fileinfo-and-filepath-functions-to-target-specific-partitions)

### <a name="use-fileinfo-and-filepath-functions-to-target-specific-partitions"></a>fileinfo 및 filepath 함수를 사용하여 특정 파티션을 타겟팅합니다.

데이터는 종종 파티션으로 구성됩니다. SQL에 요청 시 특정 폴더 및 파일을 쿼리하도록 지시할 수 있습니다. 이렇게 하면 쿼리에서 읽고 처리하는 데 필요한 파일 수와 데이터 양이 줄어듭니다. 따라서 더 나은 성능을 얻을 수 있습니다. 자세한 내용은 [파일 이름](develop-storage-files-overview.md#filename-function) 및 [파일 경로](develop-storage-files-overview.md#filepath-function) 함수 및 [특정 파일을 쿼리하는](query-specific-files.md)방법에 대한 예제를 확인합니다.

저장소의 데이터가 분할되지 않은 경우 이러한 함수를 사용하여 해당 파일을 대상으로 하는 쿼리를 최적화할 수 있도록 분할하는 것이 좋습니다.

주문형 SQL에서 [분할된 Spark 테이블을 쿼리할](develop-storage-files-spark-tables.md) 때 쿼리는 필요한 파일만 자동으로 대상으로 지정합니다.

### <a name="use-cetas-to-enhance-query-performance-and-joins"></a>CETAS를 사용하여 쿼리 성능 및 조인

[CETAS는](develop-tables-cetas.md) 주문형 SQL에서 사용할 수 있는 가장 중요한 기능 중 하나입니다. CETAS는 외부 테이블 메타데이터를 만들고 SELECT 쿼리의 결과를 저장소 계정의 파일 집합으로 내보내는 병렬 작업입니다.

CETAS를 사용하여 조인된 참조 테이블과 같이 자주 사용되는 쿼리 부분을 새 파일 집합에 저장할 수 있습니다. 나중에 여러 쿼리에서 일반적인 조인을 반복하는 대신 이 단일 외부 테이블에 조인할 수 있습니다. CETAS가 마루 파일을 생성하면 첫 번째 쿼리가 이 외부 테이블을 대상으로 할 때 통계가 자동으로 생성되고 성능이 향상됩니다.
