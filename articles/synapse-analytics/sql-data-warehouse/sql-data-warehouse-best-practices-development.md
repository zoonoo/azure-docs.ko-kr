---
title: 개발 모범 사례
description: Synapse SQL 풀용 솔루션을 개발할 때 알아야 할 권장 사항 및 모범 사례입니다.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 09/04/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 13e149c45c720a5da6b051b7ce9581d74c64fa35
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/25/2020
ms.locfileid: "83834478"
---
# <a name="development-best-practices-for-synapse-sql-pool"></a>Synapse SQL 풀 개발 모범 사례

이 문서에서는 SQL 풀 솔루션을 개발할 때 도움이 되는 지침 및 모범 사례에 대해 설명합니다.

## <a name="tune-query-performance-with-new-product-enhancements"></a>새로운 제품 향상으로 쿼리 성능 조정

- [구체화된 뷰를 사용한 성능 조정](performance-tuning-materialized-views.md)
- [순서가 지정된 클러스터형 columnstore 인덱스를 사용한 성능 조정](performance-tuning-ordered-cci.md)
- [결과 집합 캐싱을 사용한 성능 조정](performance-tuning-result-set-caching.md)

## <a name="reduce-cost-with-pause-and-scale"></a>일시 중지 및 규모 조정으로 비용 절감

일시 중지 및 크기 조정을 통해 비용을 절감하는 방법에 대한 자세한 내용은 [컴퓨팅 관리](sql-data-warehouse-manage-compute-overview.md) 문서를 참조하세요.

## <a name="maintain-statistics"></a>통계 유지 관리

열에 대한 통계를 자동으로 검색하고 만들도록 SQL 풀을 구성할 수 있습니다.  최적화 프로그램으로 만든 쿼리 계획은 사용 가능한 통계만큼 훌륭합니다.  

데이터베이스에 대해 AUTO_CREATE_STATISTICS를 사용하도록 설정하고 쿼리에 사용된 열에 대한 통계를 항상 최신 상태를 유지하도록 매일 또는 각 로드 후에 통계를 업데이트하는 것이 좋습니다.

모든 통계를 업데이트하는 데 시간이 너무 오래 소요되는 경우 통계를 자주 업데이트해야 하는 열을 더 선별적으로 시도해볼 수 있습니다. 예를 들어 새 값이 매일 추가될 수 있는 날짜 열을 업데이트하려고 합니다.

> [!TIP]
> 조인에 포함된 열, WHERE 절에 사용된 열과 GROUP BY에 있는 열에서 통계를 업데이트하면 가장 큰 이점을 얻을 수 있습니다.

[테이블 통계 관리](sql-data-warehouse-tables-statistics.md), [CREATE STATISTICS](sql-data-warehouse-tables-statistics.md) 및 [UPDATE STATISTICS](sql-data-warehouse-tables-statistics.md#update-statistics)도 참조하세요.

## <a name="hash-distribute-large-tables"></a>해시 배포 대형 테이블

기본적으로 테이블은 라운드 로빈 분산됩니다.  이 디자인을 통해 사용자는 해당 테이블이 배포되는 방식을 결정하지 않고도 테이블 생성을 쉽게 시작할 수 있습니다.  

라운드 로빈 테이블은 일부 워크로드에 대해서는 잘 작동하지만 대체로 배포 열을 선택하면 성능이 훨씬 향상됩니다.  열로 배포된 테이블이 라운드 로빈 테이블의 성능을 능가하는 가장 일반적인 예는 두 개의 대형 팩트 테이블이 조인된 경우입니다.  

예를 들어 order_id로 배포되는 주문 테이블과 order_id로 배포되는 트랜잭션 테이블이 있고 주문 테이블을 order_id로 트랜잭션 테이블에 조인하는 경우 이 쿼리는 통과 쿼리가 되어 데이터 이동 작업을 제거합니다.  단계가 적을수록 쿼리는 빨라집니다.  데이터 이동이 적을수록 쿼리는 빨라집니다.  

분산된 테이블을 로드할 때 로드가 느려지므로 들어오는 데이터가 배포 키로 정렬되지 않도록 합니다.  다음 문서에서는 배포 열을 선택하여 성능을 개선하는 방법과 CREATE TABLES 문의 WITH 절에서 분산된 테이블을 정의하는 방법에 대해 자세히 설명합니다.

[테이블 개요](sql-data-warehouse-tables-overview.md), [테이블 배포](sql-data-warehouse-tables-distribute.md), [테이블 배포 선택](https://blogs.msdn.microsoft.com/sqlcat/20../../choosing-hash-distributed-table-vs-round-robin-distributed-table-in-azure-sql-dw-service/), [CREATE TABLE](sql-data-warehouse-tables-overview.md) 및 [CREATE TABLE AS SELECT](sql-data-warehouse-develop-ctas.md)도 참조하세요.

## <a name="do-not-over-partition"></a>과도한 분할 피하기

데이터를 분할하면 파티션 전환 또는 파티션 제거로 스캔 최적화를 통해 데이터를 효율적으로 유지 관리할 수 있지만 과도하게 분할할 경우 쿼리 속도가 느려질 수 있습니다.  

SQL Server에서 제대로 작동할 수 있는 높은 세분성 분할 전략은 SQL 풀에서는 제대로 작동하지 않을 수 있습니다.  너무 많은 파티션을 포함하면 각 파티션에 100만 개 미만의 행이 포함된 경우 클러스터형 columnstore 인덱스의 효율성이 떨어질 수도 있습니다.  

배후에서 SQL 풀이 사용자 데이터를 60개 데이터베이스로 분할하므로 100개 파티션이 있는 테이블을 만들면 실제로는 6000개의 파티션이 생성됩니다.  각 워크로드가 서로 다르므로 가장 좋은 방법은 분할 실험을 통해 사용자의 워크로드에 가장 적합한 방법을 찾는 것입니다.  

> [!TIP]
> SQL Server에서 작업한 것보다 낮은 세분성을 사용하는 것이 좋습니다.  예를 들어 매일 분할보다는 매주 또는 매달 분할을 사용하는 것이 좋습니다.

[테이블 분할](sql-data-warehouse-tables-partition.md)도 참조하세요.

## <a name="minimize-transaction-sizes"></a>트랜잭션 크기 최소화

INSERT, UPDATE, DELETE 문은 트랜잭션에서 실행되며 실패할 경우 롤백해야 합니다.  긴 롤백에 대한 가능성을 최소화하려면 가능한 경우 항상 트랜잭션 크기를 최소화합니다.  INSERT, UPDATE 및 DELETE 문을 부분으로 나누어 이를 수행할 수 있습니다.  

예를 들어 1시간이 소요될 것으로 예상되는 INSERT가 있는 경우 가능하면 INSERT를 4개의 부분으로 나누어 각각 15분 이내에 실행되도록 합니다.  CTAS, TRUNCATE, DROP TABLE 또는 INSERT와 같은 특수한 로깅 사례를 최소한 활용하여 롤백 위험을 줄입니다.  

롤백을 제거하는 다른 방법은 데이터 관리를 위한 파티션 전환과 같은 메타데이터 전용 작업을 사용하는 것입니다.  예를 들어 테이블에서 order_date가 2001년 10월인 모든 행을 제거하기 위해 DELETE 문을 실행하는 대신 데이터를 매달 분할한 후 해당 파티션을 다른 테이블의 비어 있는 파티션의 데이터로 전환할 수 있습니다(ALTER TABLE 예 참조).  

분할되지 않은 테이블의 경우 DELETE를 사용하는 대신, 테이블에 유지할 데이터를 기록하도록 CTAS를 사용하는 것이 좋습니다.  CTAS에 동일한 시간이 소요되는 경우 최소한의 트랜잭션 로깅을 포함하므로 실행하기에 훨씬 더 안전한 작업이며 필요할 경우 신속하게 취소할 수 있습니다.

또한 [트랜잭션 이해](sql-data-warehouse-develop-transactions.md), [트랜잭션 최적](sql-data-warehouse-develop-best-practices-transactions.md), [테이블 분할](sql-data-warehouse-tables-partition.md), [TRUNCATE TABLE](/sql/t-sql/statements/truncate-table-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest), [ALTER TABLE](/sql/t-sql/statements/alter-table-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) 및 [CTAS(Create Table As Select)](sql-data-warehouse-develop-ctas.md)도 참조하세요.

## <a name="use-the-smallest-possible-column-size"></a>가능한 가장 작은 열 크기 사용

DDL을 정의할 때 데이터를 지원할 가장 작은 데이터 형식을 사용하면 쿼리 성능이 향상됩니다.  이 방법은 CHAR 및 VARCHAR 열에 특히 중요합니다.  

열에서 가장 긴 값이 25자인 경우 열을 VARCHAR(25)로 정의합니다.  모든 문자 열을 큰 기본 길이로 정의하지 마세요.  또한 반드시 필요한 경우에는 열을 NVARCHAR를 사용하는 것보다 VARCHAR로 정의하세요.

[테이블 개요](sql-data-warehouse-tables-overview.md), [테이블 데이터 형식](sql-data-warehouse-tables-data-types.md) 및 [CREATE TABLE](sql-data-warehouse-tables-overview.md)도 참조하세요.

## <a name="optimize-clustered-columnstore-tables"></a>클러스터형 Columnstore 테이블 최적화

columnstore 클러스터형 인덱스는 SQL 풀에 데이터를 저장할 수 있는 가장 효율적인 방법 중 하나입니다.  기본적으로 SQL 풀의 테이블은 클러스터형 ColumnStore로 만들어집니다.  

> [!NOTE]
> columnstore 테이블에서 쿼리에 대해 최적의 성능을 얻으려면 좋은 세그먼트 품질을 갖는 것이 중요합니다.  

메모리 부족 상황에서 행이 columnstore 테이블에 기록되는 경우 columnstore 세그먼트 품질이 저하될 수 있습니다.  세그먼트 품질은 압축된 행 그룹에 있는 행의 수로 측정할 수 있습니다.  

클러스터형 columnstore 테이블에 대해 세그먼트 품질을 감지하고 개선하는 단계별 지침은 [테이블 인덱스](sql-data-warehouse-tables-index.md) 문서의 [Columnstore 인덱스 품질 저하 원인](sql-data-warehouse-tables-index.md#causes-of-poor-columnstore-index-quality)을 참조하세요.  

고품질 columnstore 세그먼트가 중요하므로 데이터를 로드하기 위해서는 중간 규모 또는 대규모 리소스 클래스에 속하는 사용자 ID를 사용하는 것이 좋습니다. 낮은 [데이터 웨어하우스 단위](what-is-a-data-warehouse-unit-dwu-cdwu.md)를 사용하면 로드하는 사용자에게 더 큰 리소스 클래스를 할당하려는 것입니다.

columnstore 테이블은 일반적으로 테이블당 100만 개가 넘는 행이 있고 각 SQL 풀 테이블이 60개 테이블로 분할될 때까지 데이터를 압축된 columnstore 세그먼트에 푸시하지 않으므로 일반적으로 테이블에서 행 수가 6천만 개를 초과하지 않는 한 columnstore 테이블은 쿼리에 이점을 제공하지 않습니다.  

6천만 개 미만의 행이 있는 테이블의 경우 columnstore 인덱스를 포함하는 것이 적절하지 않을 수 있습니다.  오히려 상황을 악화시킬 수 있습니다.  

또한 데이터를 분할하는 경우 클러스터형 columnstore 인덱스의 이점을 얻기 위해 각 파티션에는 100개의 행을 포함하는 것이 좋습니다.  테이블에 파티션 수가 100개라면 클러스터형 columnstore에서 이점을 얻으려면 60억 개 이상의 행을 포함해야 합니다(60개 배포 *100개 파티션* 1백만 개 행).  

이 예에서 테이블에 60억 개의 행이 없으면 파티션 수를 줄이거나 대신 힙 테이블을 사용하는 것이 좋습니다.  또한 실험을 통해 columnstore 테이블 대신 보조 인덱스가 있는 힙 테이블로 더 나은 성능을 얻을 수 있는지도 확인할 수 있습니다.

> [!TIP]
> columnstore 테이블을 쿼리할 때 필요한 열만 선택하면 쿼리가 더 빨리 실행됩니다.  

[테이블 인덱스](sql-data-warehouse-tables-index.md), [Columnstore 인덱스 가이드](/sql/relational-databases/indexes/columnstore-indexes-overview?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) 및 [Columnstore 인덱스 다시 빌드](sql-data-warehouse-tables-index.md#rebuilding-indexes-to-improve-segment-quality)도 참조하세요.

## <a name="next-steps"></a>다음 단계

이 문서에서 원하는 내용을 찾지 못하면 이 페이지의 왼쪽에 있는 "문서 검색"을 사용하여 모든 Azure Synapse 문서를 검색해보세요.  

[Azure Synapse에 대한 Microsoft Q&A 질문 페이지](https://docs.microsoft.com/answers/topics/azure-synapse-analytics.html)는 다른 사용자와 Azure Synapse 제품 그룹에 질문을 게시할 수 있는 곳입니다.  Microsoft는 이 포럼을 적극적으로 모니터링하여 사용자의 질문에 다른 사용자나 당사 직원이 응답하도록 합니다.  

스택 오버플로에 질문하는 것을 선호하는 경우 [Azure SQL Data Warehouse 스택 오버플로 포럼](https://stackoverflow.com/questions/tagged/azure-sqldw)도 제공합니다.

[Azure Synapse 피드백](https://feedback.azure.com/forums/307516-sql-data-warehouse) 페이지를 사용하여 기능 요청을 수행할 수 있습니다.  요청을 추가하거나 다른 요청에 투표를 하면 기능의 순위를 지정하는 데 도움이 됩니다.
