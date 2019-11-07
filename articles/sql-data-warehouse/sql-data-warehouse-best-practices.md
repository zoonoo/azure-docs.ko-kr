---
title: Azure Synapse Analytics (이전의 SQL DW)의 SQL 분석에 대 한 모범 사례
description: Azure Synapse Analytics (이전의 SQL DW)에서 SQL Analytics 솔루션을 개발 하기 위한 권장 사항 및 모범 사례입니다.
services: sql-data-warehouse
author: mlee3gsd
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: design
ms.date: 11/04/2019
ms.author: martinle
ms.reviewer: igorstan
ms.openlocfilehash: 6a9d7c4ed020f447e9512c7bc2edd0caecf3400d
ms.sourcegitcommit: 359930a9387dd3d15d39abd97ad2b8cb69b8c18b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73646169"
---
# <a name="best-practices-for-sql-analytics-in-azure-synapse-analytics-formerly-sql-dw"></a>Azure Synapse Analytics (이전의 SQL DW)의 SQL 분석에 대 한 모범 사례

이 문서는 [SQL 분석](sql-data-warehouse-overview-what-is.md#sql-analytics-and-sql-pool-in-azure-synapse) 배포에서 최적의 성능을 얻는 데 도움이 되는 모범 사례 모음입니다.  이 문서의 목적은 몇 가지 기본 지침을 제공 하 고 중요 한 주요 영역을 강조 표시 하는 것입니다.  각 섹션에서는 개념을 소개 하 고 개념을 더 자세히 설명 하는 더 자세한 문서를 제공 합니다. 항목의 시퀀스는 중요도 순으로 정렬 됩니다. 

## <a name="reduce-cost-with-pause-and-scale"></a>일시 중지 및 규모 조정으로 비용 절감

일시 중지 및 크기 조정을 통해 비용을 절감하는 방법에 대한 자세한 내용은 [컴퓨팅 관리](sql-data-warehouse-manage-compute-overview.md)를 참조하세요. 

## <a name="maintain-statistics"></a>통계 유지 관리
열에 대 한 통계를 자동으로 검색 하 고 만들려면 Azure SQL Data Warehouse를 구성할 수 있습니다.  최적화 프로그램에서 생성 하는 쿼리 계획은 사용 가능한 통계 만큼만 사용할 수 있습니다.  데이터베이스에 대해 AUTO_CREATE_STATISTICS를 사용 하도록 설정 하 고 매일 또는 각 로드 후 통계를 업데이트 하 여 쿼리에 사용 되는 열에 대 한 통계가 항상 최신 상태를 유지 하도록 하는 것이 좋습니다. 

모든 통계를 업데이트 하는 데 시간이 너무 오래 걸리면 자주 통계를 업데이트 해야 하는 열을 선택 하는 것이 좋습니다. 예를 들어 새 값이 매일 추가될 수 있는 날짜 열을 업데이트하려고 합니다. **조인에 포함 된 열, WHERE 절에 사용 된 열 및 GROUP BY에 있는 열에 대 한 통계를 업데이트 하 여 가장 많은 이점을 얻을 수 있습니다.**

참고 항목: [테이블 통계 관리][Manage table statistics], [CREATE STATISTICS][CREATE STATISTICS], [UPDATE STATISTICS][UPDATE STATISTICS]

## <a name="use-dmvs-to-monitor-and-optimize-your-queries"></a>DMV를 사용하여 쿼리 모니터링 및 최적화
SQL Analytics에는 쿼리 실행을 모니터링 하는 데 사용할 수 있는 여러 Dmv가 있습니다.  다음 모니터링 문서는 쿼리 실행의 세부 정보를 확인하는 방법에 대한 단계별 지침을 안내합니다.  이러한 DMV에서 쿼리를 신속하게 찾으려면 쿼리에 LABEL 옵션을 사용하는 것이 도움이 될 수 있습니다.

참고 항목: [DMV를 사용하여 워크로드 모니터링][Monitor your workload using DMVs], [LABEL][LABEL], [OPTION][OPTION], [sys.dm_exec_sessions][sys.dm_exec_sessions], [sys.dm_pdw_exec_requests][sys.dm_pdw_exec_requests], [sys.dm_pdw_request_steps][sys.dm_pdw_request_steps], [sys.dm_pdw_sql_requests][sys.dm_pdw_sql_requests], [sys.dm_pdw_dms_workers], [DBCC PDW_SHOWEXECUTIONPLAN][DBCC PDW_SHOWEXECUTIONPLAN], [sys.dm_pdw_waits][sys.dm_pdw_waits]

## <a name="tune-query-performance-with-new-product-enhancements"></a>새로운 제품 향상으로 쿼리 성능 조정
- [구체화 된 뷰로 성능 조정](https://docs.microsoft.com/azure/sql-data-warehouse/performance-tuning-materialized-views)
- [순서가 지정 된 클러스터형 columnstore 인덱스로 성능 튜닝](https://docs.microsoft.com/azure/sql-data-warehouse/performance-tuning-ordered-cci)
- [결과 집합 캐싱을 사용한 성능 조정](https://docs.microsoft.com/azure/sql-data-warehouse/performance-tuning-result-set-caching)

## <a name="group-insert-statements-into-batches"></a>INSERT 문을 일괄 처리로 그룹화
INSERT 문을 이용한 작은 테이블에 대한 일회성 로드 또는 조회의 정기적인 다시 로드는 `INSERT INTO MyLookup VALUES (1, 'Type 1')`와 같은 문으로 사용자 요구에 대해 제대로 수행될 수 있습니다.  그러나 수천 또는 수백만 행을 하루 종일 로드해야 하는 경우 단일 항목 INSERTS만으로는 불가능할 수 있습니다.  대신 파일에 기록하는 프로세스를 개발하고 다른 프로세스를 주기적으로 함께 제공하여 이 파일을 로드합니다.

참고 항목: [INSERT][INSERT]

## <a name="use-polybase-to-load-and-export-data-quickly"></a>PolyBase를 사용하여 데이터를 신속하고 로드하고 내보내기

 SQL Analytics는 Azure Data Factory, PolyBase 및 BCP를 비롯 한 여러 도구를 통해 데이터를 로드 하 고 내보내는 것을 지원 합니다.  성능이 중요하지 않은 소량의 데이터에는 어떤 도구를 사용해도 사용자 요구 사항을 충족할 수 있습니다.  그러나 대량의 데이터를 로드 또는 내보내거나 빠른 성능이 필요한 경우 PolyBase가 가장 좋습니다.  
 
 PolyBase는 MPP (대규모 병렬 처리) 아키텍처를 활용 하도록 설계 되었으며 다른 도구 보다 더 빨리 크고 많을 데이터를 로드 하 고 내보냅니다.  PolyBase 로드는 CTAS 또는 INSERT INTO를 사용하여 실행할 수 있습니다.  **CTAS를 사용하여 트랜잭션 로깅을 최소화하고 CTAS는 데이터를 로드하는 가장 빠른 방법입니다.** 
 
  또한 Azure Data Factory는 PolyBase 로드를 지원하며 CTAS와 비슷한 성능을 얻을 수 있습니다.  PolyBase는 Gzip 파일을 포함한 다양한 파일 형식을 지원합니다.  **Gzip 텍스트 파일을 사용할 때 처리량을 최대화 하려면 파일을 60 이상으로 분할 하 여 부하의 병렬 처리를 최대화 합니다.**  총 처리량을 더 빠르게 하기 위해 데이터를 동시에 로드하는 것이 좋습니다.

또한 [데이터 로드][Load data], [PolyBase 사용 가이드][Guide for using PolyBase], [SQL 풀 로드 패턴 및 전략][Azure SQL Data Warehouse loading patterns and strategies], [Azure Data Factory 데이터 로드][Load Data with Azure Data Factory], [Azure Data Factory 데이터 이동][Move data with Azure Data Factory], [외부 파일 형식 만들기][CREATE EXTERNAL FILE FORMAT], 만들기를 참조 하세요. [ CTAS (table as select)][Create table as select (CTAS)]

## <a name="load-then-query-external-tables"></a>외부 테이블 로드 후 쿼리
외부 테이블이라고도 하는 Polybase는 데이터를 로드하는 가장 빠른 방법일 수 있지만 쿼리에 적합하지는 않습니다. Polybase 테이블은 현재 Azure blob 파일 및 Azure Data Lake 저장소만 지원 합니다. 이러한 파일에는 지원 컴퓨팅 리소스가 없습니다.  

따라서 SQL Analytics는이 작업을 오프 로드할 수 없으므로 데이터를 읽기 위해 tempdb로 로드 하 여 전체 파일을 읽어야 합니다.  따라서 이 데이터를 쿼리하는 쿼리가 여러 개 있는 경우 이 데이터를 한 번 로드한 후 쿼리에서 로컬 테이블을 사용하는 것이 더 좋습니다.

참고 항목: [PolyBase 사용 지침][Guide for using PolyBase]

## <a name="hash-distribute-large-tables"></a>해시 배포 대형 테이블
기본적으로 테이블은 라운드 로빈 분산됩니다.  따라서 사용자는 해당 테이블이 배포되는 방식을 결정하지 않고도 테이블 생성을 간편하게 시작할 수 있습니다.  라운드 로빈 테이블은 일부 워크로드에 대해서는 잘 작동하지만 대체로 배포 열을 선택하면 성능이 훨씬 향상됩니다.  열로 배포된 테이블이 라운드 로빈 테이블의 성능을 능가하는 가장 일반적인 예는 두 개의 대형 팩트 테이블이 조인된 경우입니다.  

예를 들어 order_id로 배포되는 주문 테이블과 order_id로 배포되는 트랜잭션 테이블이 있고 주문 테이블을 order_id로 트랜잭션 테이블에 조인하는 경우 이 쿼리는 통과 쿼리가 되어 데이터 이동 작업을 제거합니다.  단계가 적을수록 쿼리는 빨라집니다.  데이터 이동이 적을수록 쿼리는 빨라집니다.  

분산된 테이블을 로드할 때 로드가 느려지므로 들어오는 데이터가 배포 키로 정렬되지 않도록 합니다.  배포 열을 선택 하 여 성능을 향상 시킬 수 있는 방법 및 CREATE TABLE 문의 WITH 절에서 분산 테이블을 정의 하는 방법에 대 한 자세한 내용은 아래 링크를 참조 하십시오.

참고 항목: [테이블 개요][Table overview], [테이블 배포][Table distribution], [테이블 배포 선택][Selecting table distribution], [CREATE TABLE][CREATE TABLE], [CREATE TABLE AS SELECT][CREATE TABLE AS SELECT]

## <a name="do-not-over-partition"></a>과도한 분할 피하기
분할 데이터는 파티션 전환을 통해 데이터를 유지 관리 하거나 파티션 제거를 사용 하 여 검색을 최적화 하는 데 효과적일 수 있지만 너무 많은 파티션이 있으면 쿼리 속도가 느려질 수 있습니다.  종종 SQL Server에서 잘 작동 하는 높은 세분성 분할 전략은 SQL Analytics에서 제대로 작동 하지 않을 수 있습니다.  

너무 많은 파티션을 포함하면 각 파티션에 100만 개 미만의 행이 포함된 경우 클러스터형 columnstore 인덱스의 효율성이 떨어질 수도 있습니다.  SQL Analytics는 내부적으로 데이터를 60 데이터베이스로 분할 하므로 100 파티션이 포함 된 테이블을 만들면 실제로 6000 파티션이 발생 합니다.  

각 워크로드가 서로 다르므로 가장 좋은 방법은 분할 실험을 통해 사용자의 워크로드에 가장 적합한 방법을 찾는 것입니다.  SQL Server에서 작업한 것보다 낮은 세분성을 고려합니다.  예를 들어 매일 분할보다는 매주 또는 매달 분할을 사용하는 것이 좋습니다.

참고 항목: [테이블 분할][Table partitioning]

## <a name="minimize-transaction-sizes"></a>트랜잭션 크기 최소화
INSERT, UPDATE, DELETE 문은 트랜잭션에서 실행되며 실패할 경우 롤백해야 합니다.  긴 롤백에 대한 가능성을 최소화하려면 가능한 경우 항상 트랜잭션 크기를 최소화합니다.  INSERT, UPDATE 및 DELETE 문을 부분으로 나누어 이를 수행할 수 있습니다.  

예를 들어 1 시간을 소요 하는 삽입이 있는 경우 가능 하면 삽입을 4 개의 부분으로 분할 합니다. 그러면 각각 15 분 이내에 실행 됩니다.  CTAS, TRUNCATE, DROP TABLE 또는 INSERT와 같은 특수한 로깅 사례를 최소한 활용하여 롤백 위험을 줄입니다.  

롤백을 제거하는 다른 방법은 데이터 관리를 위한 파티션 전환과 같은 메타데이터 전용 작업을 사용하는 것입니다.  예를 들어 테이블에서 order_date가 2001년 10월인 모든 행을 제거하기 위해 DELETE 문을 실행하는 대신 데이터를 매달 분할한 후 해당 파티션을 다른 테이블의 비어 있는 파티션의 데이터로 전환할 수 있습니다(ALTER TABLE 예 참조).  

분할 되지 않은 테이블의 경우 DELETE를 사용 하는 대신 CTAS를 사용 하 여 테이블에 유지할 데이터를 작성 하는 것이 좋습니다.  CTAS가 같은 시간을 사용 하는 경우 최소한의 트랜잭션 로깅을 사용 하 고 필요한 경우 신속 하 게 취소할 수 있으므로 실행 하는 것이 훨씬 안전 합니다.

참고 항목: [트랜잭션 이해][Understanding transactions], [트랜잭션 최적][Optimizing transactions], [테이블 분할][Table partitioning], [TRUNCATE TABLE][TRUNCATE TABLE], [ALTER TABLE][ALTER TABLE], [CTAS(Create Table As Select)][Create table as select (CTAS)]

## <a name="reduce-query-result-sizes"></a>쿼리 결과 크기 줄이기  
이 단계를 통해 많은 쿼리 결과로 인해 발생 하는 클라이언트 쪽 문제를 방지할 수 있습니다.  쿼리를 편집 하 여 반환 되는 행 수를 줄일 수 있습니다. 일부 쿼리 생성 도구를 사용 하면 각 쿼리에 "top N" 구문을 추가할 수 있습니다.  쿼리 결과를 임시 테이블에 CETAS 다음 하위 수준 처리를 위해 PolyBase 내보내기를 사용할 수도 있습니다.

## <a name="use-the-smallest-possible-column-size"></a>가능한 가장 작은 열 크기 사용
DDL을 정의할 때 데이터를 지원할 가장 작은 데이터 형식을 사용 하면 쿼리 성능이 향상 됩니다.  이것은 CHAR 및 VARCHAR 열에 대해 특히 중요합니다.  열에서 가장 긴 값이 25자인 경우 열을 VARCHAR(25)로 정의합니다.  모든 문자 열을 큰 기본 길이로 정의하지 마세요.  또한 반드시 필요한 경우에는 열을 NVARCHAR를 사용하는 것보다 VARCHAR로 정의하세요.

참고 항목: [테이블 개요][Table overview], [테이블 데이터 형식][Table data types], [CREATE TABLE][CREATE TABLE]

## <a name="use-temporary-heap-tables-for-transient-data"></a>임시 데이터에 대해 임시 힙 테이블 사용
데이터를 일시적으로 방문 하는 경우 힙 테이블을 사용 하면 전체 프로세스가 더 빠르게 수행 될 수 있습니다.  변환을 추가로 실행하기 전에 준비하기 위해서만 데이터를 방문하는 경우 테이블을 힙 테이블에 로드하면 데이터를 클러스터형 columnstore 테이블에 로드할 때보다 훨씬 빨라집니다.  

또한 데이터를 임시 테이블에 로드하면 테이블을 영구 스토리지에 로드할 때보다 훨씬 빠르게 로드됩니다.  임시 테이블은 "#"으로 시작 하 고이를 만든 세션 에서만 액세스할 수 있으므로 제한 된 시나리오 에서만 작동할 수 있습니다.   힙 테이블은 CREATE TABLE의 WITH 절에 정의됩니다.  임시 테이블을 사용하는 경우 해당 임시 테이블에서도 통계를 작성해야 합니다.

참고 항목: [임시 테이블][Temporary tables], [CREATE TABLE][CREATE TABLE], [CREATE TABLE AS SELECT][CREATE TABLE AS SELECT]

## <a name="optimize-clustered-columnstore-tables"></a>클러스터형 Columnstore 테이블 최적화
클러스터형 columnstore 인덱스는 SQL Analytics에 데이터를 저장할 수 있는 가장 효율적인 방법 중 하나입니다.  기본적으로 SQL Analytics의 테이블은 클러스터형 ColumnStore로 생성 됩니다.  columnstore 테이블에서 쿼리에 대해 최상의 성능을 얻으려면 좋은 세그먼트 품질을 갖는 것이 중요합니다.  

메모리 부족 상황에서 행이 columnstore 테이블에 기록되는 경우 columnstore 세그먼트 품질이 저하될 수 있습니다.  세그먼트 품질은 압축된 행 그룹에 있는 행의 수로 측정할 수 있습니다.  클러스터형 columnstore 테이블에 대해 세그먼트 품질을 감지하고 개선하는 단계별 지침은 [테이블 인덱스][Causes of poor columnstore index quality] 문서의 [Columnstore 인덱스 품질 저하 원인][Table indexes] 섹션을 참조하세요.  

고품질 columnstore 세그먼트가 중요 하기 때문에 데이터를 로드 하기 위해 중간 규모 또는 대규모 리소스 클래스에 있는 사용자 Id를 사용 하는 것이 좋습니다. 낮은 [데이터 웨어하우스 단위](what-is-a-data-warehouse-unit-dwu-cdwu.md)를 사용하면 로드하는 사용자에게 더 큰 리소스 클래스를 할당하려는 것입니다.

Columnstore 테이블은 일반적으로 테이블당 100만 개 이상의 행이 있을 때까지 데이터를 압축 된 columnstore 세그먼트로 푸시 하지 않으며 각 SQL 분석 테이블은 60 테이블로 분할 됩니다. 즉, 다음과 같은 경우에는 columnstore 테이블이 쿼리에 유용 하지 않습니다. 테이블에 6000만 개 이상의 행이 있습니다.  6천만 개 미만의 행이 있는 테이블의 경우 columnstore 인덱스를 포함하는 것이 적절하지 않을 수 있습니다.  오히려 상황을 악화시킬 수 있습니다.  

또한 데이터를 분할하는 경우 클러스터형 columnstore 인덱스의 이점을 얻기 위해 각 파티션에는 100개의 행을 포함하는 것이 좋습니다.  테이블에 파티션 수가 100개라면 클러스터형 columnstore에서 이점을 얻으려면 60억 개 이상의 행을 포함해야 합니다(60개 배포 * 100개 파티션 * 1백만 행).  

이 예에서 테이블에 60억 개의 행이 없으면 파티션 수를 줄이거나 대신 힙 테이블을 사용하는 것이 좋습니다.  또한 실험을 통해 columnstore 테이블 대신 보조 인덱스가 있는 힙 테이블로 더 나은 성능을 얻을 수 있는지도 확인할 수 있습니다.

columnstore 테이블을 쿼리할 때 필요한 열만 선택하면 쿼리가 더 빨리 실행됩니다.  

참고 항목: [테이블 인덱스][Table indexes], [Columnstore 인덱스 가이드][Columnstore indexes guide], [Columnstore 인덱스 다시 빌드][Rebuilding columnstore indexes]

## <a name="use-larger-resource-class-to-improve-query-performance"></a>더 큰 리소스 클래스를 사용하여 쿼리 성능 향상
SQL Analytics는 리소스 그룹을 사용 하 여 쿼리에 메모리를 할당 합니다.  기본적으로 모든 사용자는 배포 당 100 MB의 메모리를 부여 하는 작은 리소스 클래스에 할당 됩니다.  항상 60개의 배포가 있고 각 배포에는 최소 100MB가 부여되므로 시스템 전체에서 총 메모리 할당량은 6,000MB이거나 6GB가 약간 못됩니다.  

큰 조인 또는 클러스터형 columnstore 테이블에 로드와 같은 특정 쿼리는 큰 메모리를 할당하는 것이 도움이 됩니다.  순수 검색과 같은 일부 쿼리에는 어떤 이점도 보이지 않습니다.  대칭 이동에서 더 큰 리소스 클래스를 사용 하면 동시성이 감소 하므로 모든 사용자를 큰 리소스 클래스로 이동 하기 전에 이러한 영향을 고려해 야 할 것입니다.

또한 [워크로드 관리를 위한 리소스 클래스](resource-classes-for-workload-management.md)도 참조하세요.

## <a name="use-smaller-resource-class-to-increase-concurrency"></a>더 작은 리소스 클래스를 사용하여 동시성 증가
사용자 쿼리 시간이 오래 걸리는 것으로 생각 되는 경우 사용자가 더 큰 리소스 클래스에서 실행 중이 고 다른 쿼리를 큐에 대기 시키는 많은 동시성 슬롯을 사용 하는 것일 수 있습니다.  사용자 쿼리가 큐에 대기되었는지 확인하려면 `SELECT * FROM sys.dm_pdw_waits` 를 실행하여 모든 행이 반환되었는지 확인합니다.

또한 [워크 로드 관리를 위한 리소스 클래스](resource-classes-for-workload-management.md), [sys. dm _ 대기 대기][sys.dm_pdw_waits]

## <a name="other-resources"></a>기타 리소스
또한 일반적인 문제 및 해결 방법에 대해서는 [문제 해결][Troubleshooting] 문서를 참조하세요.

이 문서에서 원하는 항목을 찾지 못한 경우이 페이지의 왼쪽에 있는 "문서 검색"을 사용 하 여 모든 Azure Synapse 문서를 검색 해 보세요.  [Azure Synapse 포럼][Azure SQL Data Warehouse MSDN Forum] 은 다른 사용자 및 Azure Synapse 제품 그룹에 질문할 수 있는 장소입니다. 

Microsoft는 이 포럼을 적극적으로 모니터링하여 사용자의 질문에 다른 사용자나 당사 직원이 응답하도록 합니다.  Stack Overflow에 대 한 질문을 하는 것을 선호 하는 경우 [Azure Synapse Stack Overflow 포럼][Azure SQL Data Warehouse Stack Overflow Forum]도 있습니다.

마지막으로, [Azure Synapse 피드백][Azure SQL Data Warehouse Feedback] 페이지를 사용 하 여 기능 요청을 수행 하세요.  요청을 추가하거나 다른 요청에 투표를 하면 기능의 순위를 지정하는 데 도움이 됩니다.

<!--Image references-->

<!--Article references-->
[Create a support ticket]: ./sql-data-warehouse-get-started-create-support-ticket.md
[Create table as select (CTAS)]: ./sql-data-warehouse-develop-ctas.md
[Table overview]: ./sql-data-warehouse-tables-overview.md
[Table data types]: ./sql-data-warehouse-tables-data-types.md
[Table distribution]: ./sql-data-warehouse-tables-distribute.md
[Table indexes]: ./sql-data-warehouse-tables-index.md
[Causes of poor columnstore index quality]: ./sql-data-warehouse-tables-index.md#causes-of-poor-columnstore-index-quality
[Rebuilding columnstore indexes]: ./sql-data-warehouse-tables-index.md#rebuilding-indexes-to-improve-segment-quality
[Table partitioning]: ./sql-data-warehouse-tables-partition.md
[Manage table statistics]: ./sql-data-warehouse-tables-statistics.md
[Temporary tables]: ./sql-data-warehouse-tables-temporary.md
[Guide for using PolyBase]: ./guidance-for-loading-data.md
[Load data]: ./design-elt-data-loading.md
[Move data with Azure Data Factory]: ../data-factory/transform-data-using-machine-learning.md
[Load data with Azure Data Factory]: ../data-factory/load-azure-sql-data-warehouse.md
[Load data with bcp]: /sql/tools/bcp-utility
[Load data with PolyBase]: ./load-data-wideworldimportersdw.md
[Monitor your workload using DMVs]: ./sql-data-warehouse-manage-monitor.md
[Pause compute resources]: ./sql-data-warehouse-manage-compute-overview.md#pause-compute-bk
[Resume compute resources]: ./sql-data-warehouse-manage-compute-overview.md#resume-compute-bk
[Scale compute resources]: ./sql-data-warehouse-manage-compute-overview.md#scale-compute
[Understanding transactions]: ./sql-data-warehouse-develop-transactions.md
[Optimizing transactions]: ./sql-data-warehouse-develop-best-practices-transactions.md
[Troubleshooting]: ./sql-data-warehouse-troubleshoot.md
[LABEL]: ./sql-data-warehouse-develop-label.md

<!--MSDN references-->
[ALTER TABLE]: https://msdn.microsoft.com/library/ms190273.aspx
[CREATE EXTERNAL FILE FORMAT]: https://msdn.microsoft.com/library/dn935026.aspx
[CREATE STATISTICS]: https://msdn.microsoft.com/library/ms188038.aspx
[CREATE TABLE]: https://msdn.microsoft.com/library/mt203953.aspx
[CREATE TABLE AS SELECT]: https://msdn.microsoft.com/library/mt204041.aspx
[DBCC PDW_SHOWEXECUTIONPLAN]: https://msdn.microsoft.com/library/mt204017.aspx
[INSERT]: https://msdn.microsoft.com/library/ms174335.aspx
[OPTION]: https://msdn.microsoft.com/library/ms190322.aspx
[TRUNCATE TABLE]: https://msdn.microsoft.com/library/ms177570.aspx
[UPDATE STATISTICS]: https://msdn.microsoft.com/library/ms187348.aspx
[sys.dm_exec_sessions]: https://msdn.microsoft.com/library/ms176013.aspx
[sys.dm_pdw_exec_requests]: https://msdn.microsoft.com/library/mt203887.aspx
[sys.dm_pdw_request_steps]: https://msdn.microsoft.com/library/mt203913.aspx
[sys.dm_pdw_sql_requests]: https://msdn.microsoft.com/library/mt203889.aspx
[sys.dm_pdw_dms_workers]: https://msdn.microsoft.com/library/mt203878.aspx
[sys.dm_pdw_waits]: https://msdn.microsoft.com/library/mt203893.aspx
[Columnstore indexes guide]: https://msdn.microsoft.com/library/gg492088.aspx

<!--Other Web references-->
[Selecting table distribution]: https://blogs.msdn.microsoft.com/sqlcat/20../../choosing-hash-distributed-table-vs-round-robin-distributed-table-in-azure-sql-dw-service/
[Azure SQL Data Warehouse Feedback]: https://feedback.azure.com/forums/307516-sql-data-warehouse
[Azure SQL Data Warehouse MSDN Forum]: https://social.msdn.microsoft.com/Forums/sqlserver/home?forum=AzureSQLDataWarehouse
[Azure SQL Data Warehouse Stack Overflow Forum]:  https://stackoverflow.com/questions/tagged/azure-sqldw
[Azure SQL Data Warehouse loading patterns and strategies]: https://blogs.msdn.microsoft.com/sqlcat/20../../azure-sql-data-warehouse-loading-patterns-and-strategies/
