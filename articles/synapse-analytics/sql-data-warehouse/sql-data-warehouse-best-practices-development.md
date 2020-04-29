---
title: 개발 모범 사례
description: Synapse SQL 풀에 대 한 솔루션을 개발할 때 알아야 할 권장 사항 및 모범 사례입니다.
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
ms.openlocfilehash: 9c4f08b143ab4a0d3e780f68f8d5ab823d4eae12
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "80745372"
---
# <a name="development-best-practices-for-synapse-sql-pool"></a>Synapse SQL 풀에 대 한 개발 모범 사례

이 문서에서는 SQL 풀 솔루션을 개발할 때의 지침과 모범 사례에 대해 설명 합니다.

## <a name="tune-query-performance-with-new-product-enhancements"></a>새로운 제품 향상으로 쿼리 성능 조정

- [구체화된 뷰를 사용한 성능 조정](performance-tuning-materialized-views.md)
- [순서가 지정된 클러스터형 columnstore 인덱스를 사용한 성능 조정](performance-tuning-ordered-cci.md)
- [결과 집합 캐싱을 사용한 성능 조정](performance-tuning-result-set-caching.md)

## <a name="reduce-cost-with-pause-and-scale"></a>일시 중지 및 규모 조정으로 비용 절감

일시 중지 및 크기 조정으로 비용을 줄이는 방법에 대 한 자세한 내용은 [계산 관리](sql-data-warehouse-manage-compute-overview.md) 문서를 참조 하세요.

## <a name="maintain-statistics"></a>통계 유지 관리

열에 대 한 통계를 자동으로 검색 하 고 만들도록 SQL 풀을 구성할 수 있습니다.  최적화 프로그램에서 생성 하는 쿼리 계획은 사용 가능한 통계 만큼만 사용할 수 있습니다.  

데이터베이스에 대 한 AUTO_CREATE_STATISTICS를 사용 하도록 설정 하 고 매일 또는 각 로드 후 통계를 업데이트 하 여 쿼리에 사용 되는 열에 대 한 통계가 항상 최신 상태를 유지 하도록 하는 것이 좋습니다.

모든 통계를 업데이트 하는 데 시간이 너무 오래 걸리면 자주 통계를 업데이트 해야 하는 열을 선택 하는 것이 좋습니다. 예를 들어 새 값이 매일 추가될 수 있는 날짜 열을 업데이트하려고 합니다.

> [!TIP]
> 조인에 포함 된 열, WHERE 절에 사용 된 열 및 GROUP BY에 있는 열에 대 한 통계를 업데이트 하 여 가장 많은 이점을 얻을 수 있습니다.

또한 [테이블 통계 관리](sql-data-warehouse-tables-statistics.md), [통계 만들기](sql-data-warehouse-tables-statistics.md)및 [통계 업데이트](sql-data-warehouse-tables-statistics.md#update-statistics)를 참조 하세요.

## <a name="hash-distribute-large-tables"></a>해시 배포 대형 테이블

기본적으로 테이블은 라운드 로빈 분산됩니다.  이 디자인을 사용 하면 사용자가 테이블을 배포 하는 방법을 결정 하지 않고도 쉽게 테이블을 만들 수 있습니다.  

라운드 로빈 테이블은 일부 워크로드에 대해서는 잘 작동하지만 대체로 배포 열을 선택하면 성능이 훨씬 향상됩니다.  열로 배포된 테이블이 라운드 로빈 테이블의 성능을 능가하는 가장 일반적인 예는 두 개의 대형 팩트 테이블이 조인된 경우입니다.  

예를 들어 order_id로 배포되는 주문 테이블과 order_id로 배포되는 트랜잭션 테이블이 있고 주문 테이블을 order_id로 트랜잭션 테이블에 조인하는 경우 이 쿼리는 통과 쿼리가 되어 데이터 이동 작업을 제거합니다.  단계가 적을수록 쿼리는 빨라집니다.  데이터 이동이 적을수록 쿼리는 빨라집니다.  

분산된 테이블을 로드할 때 로드가 느려지므로 들어오는 데이터가 배포 키로 정렬되지 않도록 합니다.  다음 문서에서는 배포 열을 선택 하 고 CREATE TABLES 문의 WITH 절에서 분산 테이블을 정의 하는 방법에 대 한 자세한 정보를 제공 합니다.

참고 항목 [테이블 개요](sql-data-warehouse-tables-overview.md), [테이블 배포](sql-data-warehouse-tables-distribute.md), [테이블 배포 선택](https://blogs.msdn.microsoft.com/sqlcat/20../../choosing-hash-distributed-table-vs-round-robin-distributed-table-in-azure-sql-dw-service/), [CREATE TABLE](sql-data-warehouse-tables-overview.md)및 [CREATE TABLE 선택](sql-data-warehouse-develop-ctas.md)

## <a name="do-not-over-partition"></a>과도한 분할 피하기

분할 데이터는 파티션 전환을 통해 데이터를 유지 관리 하거나 파티션 제거를 사용 하 여 검색을 최적화 하는 데 효과적일 수 있지만 너무 많은 파티션이 있으면 쿼리 속도가 느려질 수 있습니다.  

SQL Server에서 잘 작동할 수 있는 높은 세분성 분할 전략은 SQL 풀에서 제대로 작동 하지 않을 수 있습니다.  너무 많은 파티션을 포함하면 각 파티션에 100만 개 미만의 행이 포함된 경우 클러스터형 columnstore 인덱스의 효율성이 떨어질 수도 있습니다.  

내부적으로 SQL 풀은 데이터를 60 데이터베이스로 분할 하므로 100 파티션이 포함 된 테이블을 만들면 실제로 6000 파티션이 발생 합니다.  각 워크로드가 서로 다르므로 가장 좋은 방법은 분할 실험을 통해 사용자의 워크로드에 가장 적합한 방법을 찾는 것입니다.  

> [!TIP]
> SQL Server에서 사용 하는 것 보다 더 낮은 세분성을 사용 하는 것이 좋습니다.  예를 들어 매일 분할보다는 매주 또는 매달 분할을 사용하는 것이 좋습니다.

[테이블 분할](sql-data-warehouse-tables-partition.md)도 참조 하세요.

## <a name="minimize-transaction-sizes"></a>트랜잭션 크기 최소화

INSERT, UPDATE, DELETE 문은 트랜잭션에서 실행되며 실패할 경우 롤백해야 합니다.  긴 롤백에 대한 가능성을 최소화하려면 가능한 경우 항상 트랜잭션 크기를 최소화합니다.  INSERT, UPDATE 및 DELETE 문을 부분으로 나누어 이를 수행할 수 있습니다.  

예를 들어 1 시간이 소요 될 것으로 예상 되는 삽입이 있는 경우 가능한 경우 삽입을 4 개의 부분으로 분할 합니다. 그러면 각각 15 분 이내에 실행 됩니다.  CTAS, TRUNCATE, DROP TABLE 또는 INSERT to empty tables와 같은 특수 한 최소 로깅 사례를 활용 하 여 롤백 위험을 줄입니다.  

롤백을 제거하는 다른 방법은 데이터 관리를 위한 파티션 전환과 같은 메타데이터 전용 작업을 사용하는 것입니다.  예를 들어 테이블에서 order_date가 2001년 10월인 모든 행을 제거하기 위해 DELETE 문을 실행하는 대신 데이터를 매달 분할한 후 해당 파티션을 다른 테이블의 비어 있는 파티션의 데이터로 전환할 수 있습니다(ALTER TABLE 예 참조).  

분할 되지 않은 테이블의 경우 DELETE를 사용 하는 대신 CTAS를 사용 하 여 테이블에 유지할 데이터를 작성 하는 것이 좋습니다.  CTAS가 같은 시간을 사용 하는 경우 최소한의 트랜잭션 로깅을 사용 하 고 필요한 경우 신속 하 게 취소할 수 있으므로 실행 하는 것이 훨씬 안전 합니다.

[트랜잭션 이해](sql-data-warehouse-develop-transactions.md), [트랜잭션 최적화](sql-data-warehouse-develop-best-practices-transactions.md), [테이블 분할](sql-data-warehouse-tables-partition.md), [TRUNCATE TABLE](/sql/t-sql/statements/truncate-table-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest), [ALTER table](/sql/t-sql/statements/alter-table-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)및 [ctas (Create Table as select)](sql-data-warehouse-develop-ctas.md)도 참조 하세요.

## <a name="use-the-smallest-possible-column-size"></a>가능한 가장 작은 열 크기 사용

DDL을 정의할 때 데이터를 지원할 가장 작은 데이터 형식을 사용 하면 쿼리 성능이 향상 됩니다.  이 방법은 CHAR 및 VARCHAR 열에서 특히 중요 합니다.  

열에서 가장 긴 값이 25자인 경우 열을 VARCHAR(25)로 정의합니다.  모든 문자 열을 큰 기본 길이로 정의하지 마세요.  또한 반드시 필요한 경우에는 열을 NVARCHAR를 사용하는 것보다 VARCHAR로 정의하세요.

또한 [테이블 개요](sql-data-warehouse-tables-overview.md), [테이블 데이터 형식](sql-data-warehouse-tables-data-types.md)및 [CREATE TABLE](sql-data-warehouse-tables-overview.md)을 참조 하세요.

## <a name="optimize-clustered-columnstore-tables"></a>클러스터형 Columnstore 테이블 최적화

클러스터형 columnstore 인덱스는 SQL 풀에 데이터를 저장할 수 있는 가장 효율적인 방법 중 하나입니다.  기본적으로 SQL 풀의 테이블은 클러스터형 ColumnStore로 생성 됩니다.  

> [!NOTE]
> Columnstore 테이블에 대 한 쿼리에 대 한 최적의 성능을 얻으려면 적절 한 세그먼트 품질이 중요 합니다.  

메모리 부족 상황에서 행이 columnstore 테이블에 기록되는 경우 columnstore 세그먼트 품질이 저하될 수 있습니다.  세그먼트 품질은 압축된 행 그룹에 있는 행의 수로 측정할 수 있습니다.  

클러스터형 columnstore 테이블에 대 한 세그먼트 품질을 감지 하 고 개선 하는 단계별 지침은 [테이블 인덱스](sql-data-warehouse-tables-index.md) 에서 [columnstore 인덱스 품질 저하의 원인](sql-data-warehouse-tables-index.md#causes-of-poor-columnstore-index-quality) 을 참조 하세요.  

고품질 columnstore 세그먼트가 중요 하기 때문에 데이터를 로드 하기 위해 중간 규모 또는 대규모 리소스 클래스에 있는 사용자 Id를 사용 하는 것이 좋습니다. 낮은 [데이터 웨어하우스 단위](what-is-a-data-warehouse-unit-dwu-cdwu.md)를 사용하면 로드하는 사용자에게 더 큰 리소스 클래스를 할당하려는 것입니다.

Columnstore 테이블은 일반적으로 테이블당 100만 개 이상의 행이 있을 때까지 데이터를 압축 된 columnstore 세그먼트로 푸시 하지 않으며 각 SQL 풀 테이블이 60 테이블로 분할 되기 때문에 일반적으로 테이블에 6000만 개 이상의 행이 있는 경우를 제외 하 고는 columnstore 테이블이 쿼리를 제공 하지 않습니다.  

6000만 개 미만의 행이 있는 테이블의 경우 columnstore 인덱스를 사용 하는 것은 의미가 없습니다.  오히려 상황을 악화시킬 수 있습니다.  

또한 데이터를 분할하는 경우 클러스터형 columnstore 인덱스의 이점을 얻기 위해 각 파티션에는 100개의 행을 포함하는 것이 좋습니다.  테이블에 100 개의 파티션이 있는 경우 클러스터형 열 저장소 (60 배포판 *100 파티션* 100만 행)를 활용 하려면 60억 행 이상이 필요 합니다.  

이 예에서 테이블에 60억 개의 행이 없으면 파티션 수를 줄이거나 대신 힙 테이블을 사용하는 것이 좋습니다.  또한 실험을 통해 columnstore 테이블 대신 보조 인덱스가 있는 힙 테이블로 더 나은 성능을 얻을 수 있는지도 확인할 수 있습니다.

> [!TIP]
> columnstore 테이블을 쿼리할 때 필요한 열만 선택하면 쿼리가 더 빨리 실행됩니다.  

[테이블 인덱스](sql-data-warehouse-tables-index.md), [columnstore 인덱스 가이드](/sql/relational-databases/indexes/columnstore-indexes-overview?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest), [columnstore 인덱스 다시 작성](sql-data-warehouse-tables-index.md#rebuilding-indexes-to-improve-segment-quality)을 참조 하세요.

## <a name="next-steps"></a>다음 단계

이 문서에서 원하는 항목을 찾지 못한 경우이 페이지의 왼쪽에 있는 "문서 검색"을 사용 하 여 모든 Azure Synapse 문서를 검색 해 보세요.  

[Azure Synapse 포럼](https://social.msdn.microsoft.com/Forums/sqlserver/home?forum=AzureSQLDataWarehouse) 은 다른 사용자 및 Azure Synapse 제품 그룹에 질문을 게시할 수 있는 장소입니다.  Microsoft는 이 포럼을 적극적으로 모니터링하여 사용자의 질문에 다른 사용자나 당사 직원이 응답하도록 합니다.  

스택 오버플로에 질문하는 것을 선호하는 경우 [Azure SQL Data Warehouse 스택 오버플로 포럼](https://stackoverflow.com/questions/tagged/azure-sqldw)도 제공합니다.

[Azure Synapse 피드백](https://feedback.azure.com/forums/307516-sql-data-warehouse) 페이지를 사용 하 여 기능 요청을 수행할 수 있습니다.  요청을 추가하거나 다른 요청에 투표를 하면 기능의 순위를 지정하는 데 도움이 됩니다.
