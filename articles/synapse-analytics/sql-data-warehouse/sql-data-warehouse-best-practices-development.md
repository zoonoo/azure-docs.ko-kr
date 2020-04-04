---
title: 개발 모범 사례
description: Synapse SQL 풀에 대한 솔루션을 개발할 때 알아야 할 권장 사항 및 모범 사례입니다.
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
ms.openlocfilehash: 5857a10d0aaf0d0c37ab55a2d0d29e5315340c9f
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/03/2020
ms.locfileid: "80633644"
---
# <a name="development-best-practices-for-synapse-sql-pool"></a>시냅스 SQL 풀을 위한 개발 모범 사례
이 문서에서는 SQL 풀 솔루션을 개발할 때 의지침과 모범 사례에 대해 설명합니다. 

## <a name="tune-query-performance-with-new-product-enhancements"></a>새로운 제품 개선 사항으로 쿼리 성능 조정  
- [구체화된 뷰를 사용한 성능 조정](performance-tuning-materialized-views.md)
- [순서가 지정된 클러스터형 columnstore 인덱스를 사용한 성능 조정](performance-tuning-ordered-cci.md)
- [결과 집합 캐싱을 사용한 성능 조정](performance-tuning-result-set-caching.md)

## <a name="reduce-cost-with-pause-and-scale"></a>일시 중지 및 규모 조정으로 비용 절감
일시 중지 및 크기 조정을 통해 비용을 절감하는 방법에 대한 자세한 내용은 [계산 관리](sql-data-warehouse-manage-compute-overview.md) 문서를 참조하십시오. 

## <a name="maintain-statistics"></a>통계 유지 관리
SQL 풀을 구성하여 열에 대한 통계를 자동으로 검색하고 생성할 수 있습니다.  최적화 프로그램에 의해 생성된 쿼리 계획은 사용 가능한 통계만큼만 좋습니다.  

데이터베이스에 대한 AUTO_CREATE_STATISTICS 사용하도록 설정하고 쿼리에 사용되는 열에 대한 통계가 항상 최신 상태로 유지되도록 매일 또는 각 로드 후에 통계를 업데이트하는 것이 좋습니다. 

모든 통계를 업데이트하는 데 시간이 너무 오래 걸리는 경우 자주 통계 업데이트가 필요한 열에 대해 보다 선택적으로 시도할 수 있습니다. 예를 들어 새 값이 매일 추가될 수 있는 날짜 열을 업데이트하려고 합니다. 

> [!TIP]
> 조인과 관련된 열, WHERE 절에 사용된 열 및 GROUP BY에 있는 열에 대한 통계를 업데이트하면 가장 많은 이점을 얻을 수 있습니다.

테이블 [통계 관리,](sql-data-warehouse-tables-statistics.md) [통계 만들기](sql-data-warehouse-tables-statistics.md)및 [통계 업데이트](sql-data-warehouse-tables-statistics.md#update-statistics)를 참조하십시오.

## <a name="hash-distribute-large-tables"></a>해시 배포 대형 테이블
기본적으로 테이블은 라운드 로빈 분산됩니다.  이 디자인을 사용하면 테이블을 배포하는 방법을 결정할 필요 없이 쉽게 테이블 만들기를 시작할 수 있습니다.  

라운드 로빈 테이블은 일부 워크로드에 대해서는 잘 작동하지만 대체로 배포 열을 선택하면 성능이 훨씬 향상됩니다.  열로 배포된 테이블이 라운드 로빈 테이블의 성능을 능가하는 가장 일반적인 예는 두 개의 대형 팩트 테이블이 조인된 경우입니다.  

예를 들어 order_id로 배포되는 주문 테이블과 order_id로 배포되는 트랜잭션 테이블이 있고 주문 테이블을 order_id로 트랜잭션 테이블에 조인하는 경우 이 쿼리는 통과 쿼리가 되어 데이터 이동 작업을 제거합니다.  단계가 적을수록 쿼리는 빨라집니다.  데이터 이동이 적을수록 쿼리는 빨라집니다.  

분산된 테이블을 로드할 때 로드가 느려지므로 들어오는 데이터가 배포 키로 정렬되지 않도록 합니다.  다음 문서에서는 배포 열을 선택하여 성능 향상에 대한 자세한 내용과 CREATE TABLES 문의 WITH 절에서 분산 테이블을 정의하는 방법을 제공합니다.

또한 [표 개요,](sql-data-warehouse-tables-overview.md) [테이블 분포,](sql-data-warehouse-tables-distribute.md) [테이블 분포 선택,](https://blogs.msdn.microsoft.com/sqlcat/20../../choosing-hash-distributed-table-vs-round-robin-distributed-table-in-azure-sql-dw-service/) [테이블 만들기](sql-data-warehouse-tables-overview.md)및 [선택으로 테이블 만들기](sql-data-warehouse-develop-ctas.md) 참조

## <a name="do-not-over-partition"></a>과도한 분할 피하기
데이터를 분할하는 것은 파티션 전환을 통해 데이터를 유지 관리하거나 파티션 제거를 통해 스캔을 최적화하는 데 효과적일 수 있지만 파티션이 너무 많면 쿼리 속도가 느려질 수 있습니다.  

SQL Server에서 잘 작동할 수 있는 높은 세분성 분할 전략이 SQL 풀에서 잘 작동하지 않는 경우가 종종 있습니다.  너무 많은 파티션을 포함하면 각 파티션에 100만 개 미만의 행이 포함된 경우 클러스터형 columnstore 인덱스의 효율성이 떨어질 수도 있습니다.  

SQL 풀은 데이터를 60개의 데이터베이스로 분할하므로 100개의 파티션이 있는 테이블을 만들면 실제로 6000개의 파티션이 생성됩니다.  각 워크로드가 서로 다르므로 가장 좋은 방법은 분할 실험을 통해 사용자의 워크로드에 가장 적합한 방법을 찾는 것입니다.  

> [!TIP]
> SQL Server에서 작업했을 수 있는 것보다 더 낮은 세분성을 사용하는 것이 좋습니다.  예를 들어 매일 분할보다는 매주 또는 매달 분할을 사용하는 것이 좋습니다.

또한 [표 분할을](sql-data-warehouse-tables-partition.md)참조하십시오.

## <a name="minimize-transaction-sizes"></a>트랜잭션 크기 최소화
INSERT, UPDATE, DELETE 문은 트랜잭션에서 실행되며 실패할 경우 롤백해야 합니다.  긴 롤백에 대한 가능성을 최소화하려면 가능한 경우 항상 트랜잭션 크기를 최소화합니다.  INSERT, UPDATE 및 DELETE 문을 부분으로 나누어 이를 수행할 수 있습니다.  

예를 들어, INSERT가 있는 경우 1시간이 걸릴 것으로 예상되는 경우, 가능하면 INSERT를 4개의 부분으로 나누면 각각 15분 안에 실행됩니다.  CTAS, TRUNCATE, DROP 테이블 또는 INSERT와 같은 특수 최소 로깅 사례를 빈 테이블에 활용하여 롤백 위험을 줄입니다.  

롤백을 제거하는 다른 방법은 데이터 관리를 위한 파티션 전환과 같은 메타데이터 전용 작업을 사용하는 것입니다.  예를 들어 테이블에서 order_date가 2001년 10월인 모든 행을 제거하기 위해 DELETE 문을 실행하는 대신 데이터를 매달 분할한 후 해당 파티션을 다른 테이블의 비어 있는 파티션의 데이터로 전환할 수 있습니다(ALTER TABLE 예 참조).  

분할되지 않은 테이블의 경우 CTAS를 사용하여 DELETE를 사용하는 대신 테이블에 보관할 데이터를 쓰는 것이 좋습니다.  CTAS에 동일한 시간이 걸리는 경우 트랜잭션 로깅이 최소화되고 필요한 경우 신속하게 취소할 수 있으므로 실행하는 것이 훨씬 안전합니다.

트랜잭션 [이해,](sql-data-warehouse-develop-transactions.md) [트랜잭션 최적화,](sql-data-warehouse-develop-best-practices-transactions.md) [테이블 분할,](sql-data-warehouse-tables-partition.md) [테이블 실행,](https://msdn.microsoft.com/library/ms177570.aspx) [테이블 변경](https://msdn.microsoft.com/library/ms190273.aspx)및 [선택(CTAS)으로 테이블 만들기](sql-data-warehouse-develop-ctas.md)를 참조하십시오.

## <a name="use-the-smallest-possible-column-size"></a>가능한 가장 작은 열 크기 사용
DDL을 정의할 때 데이터를 지원하는 가장 작은 데이터 형식을 사용하면 쿼리 성능이 향상됩니다.  이 방법은 CHAR 및 VARCHAR 열에 특히 중요합니다.  

열에서 가장 긴 값이 25자인 경우 열을 VARCHAR(25)로 정의합니다.  모든 문자 열을 큰 기본 길이로 정의하지 마세요.  또한 반드시 필요한 경우에는 열을 NVARCHAR를 사용하는 것보다 VARCHAR로 정의하세요.

또한 [테이블 개요,](sql-data-warehouse-tables-overview.md) [테이블 데이터 유형](sql-data-warehouse-tables-data-types.md)및 테이블 [만들기](sql-data-warehouse-tables-overview.md)를 참조하십시오.

## <a name="optimize-clustered-columnstore-tables"></a>클러스터형 Columnstore 테이블 최적화
클러스터된 columnstore 인덱스는 SQL 풀에 데이터를 저장할 수 있는 가장 효율적인 방법 중 하나입니다.  기본적으로 SQL 풀의 테이블은 클러스터된 ColumnStore로 만들어집니다.  

> [!NOTE]
> columnstore 테이블의 쿼리에 대한 최적의 성능을 얻으려면 세그먼트 품질이 양호한 것이 중요합니다.  

메모리 부족 상황에서 행이 columnstore 테이블에 기록되는 경우 columnstore 세그먼트 품질이 저하될 수 있습니다.  세그먼트 품질은 압축된 행 그룹에 있는 행의 수로 측정할 수 있습니다.  

그룹화 된 columnstore 테이블의 세그먼트 품질 검색 및 개선에 대한 단계별 지침은 [테이블 인덱스](sql-data-warehouse-tables-index.md) 문서의 [열저장소 인덱스 품질 저하의 원인을](sql-data-warehouse-tables-index.md#causes-of-poor-columnstore-index-quality) 참조하십시오.  

고품질 columnstore 세그먼트는 중요하므로 데이터를 로드하기 위해 중간 또는 큰 리소스 클래스에 있는 사용자 아이디를 사용하는 것이 좋습니다. 낮은 [데이터 웨어하우스 단위](what-is-a-data-warehouse-unit-dwu-cdwu.md)를 사용하면 로드하는 사용자에게 더 큰 리소스 클래스를 할당하려는 것입니다.

columnstore 테이블은 일반적으로 테이블당 1백만 개 이상의 행이 있고 각 SQL 풀테이블이 60개의 테이블로 분할될 때까지 압축된 columnstore 세그먼트로 데이터를 푸시하지 않으므로 일반적으로 columnstore 테이블의 행이 6천만 개 이상인 경우 쿼리에 이점을 주지 않습니다.  

6천만 개 미만의 행이 있는 테이블의 경우 columnstore 인덱스를 두는 것은 의미가 없을 수 있습니다.  오히려 상황을 악화시킬 수 있습니다.  

또한 데이터를 분할하는 경우 클러스터형 columnstore 인덱스의 이점을 얻기 위해 각 파티션에는 100개의 행을 포함하는 것이 좋습니다.  테이블에 파티션 수가 100개라면 클러스터형 columnstore에서 이점을 얻으려면 60억 개 이상의 행을 포함해야 합니다(60개 배포 * 100개 파티션 * 1백만 행).  

이 예에서 테이블에 60억 개의 행이 없으면 파티션 수를 줄이거나 대신 힙 테이블을 사용하는 것이 좋습니다.  또한 실험을 통해 columnstore 테이블 대신 보조 인덱스가 있는 힙 테이블로 더 나은 성능을 얻을 수 있는지도 확인할 수 있습니다.

> [!TIP]
> columnstore 테이블을 쿼리할 때 필요한 열만 선택하면 쿼리가 더 빨리 실행됩니다.  

또한 [테이블 인덱스,](sql-data-warehouse-tables-index.md) [열 저장소 인덱스 가이드](https://msdn.microsoft.com/library/gg492088.aspx)및 열 저장소 인덱스 다시[작성](sql-data-warehouse-tables-index.md#rebuilding-indexes-to-improve-segment-quality)을 참조하십시오.

## <a name="next-steps"></a>다음 단계
이 문서에서 찾고 있는 내용을 찾지 못하면 이 페이지 왼쪽에 있는 "문서 검색"을 사용하여 Azure Synapse 문서를 모두 검색해 보십시오.  

[Azure 시냅스 포럼은](https://social.msdn.microsoft.com/Forums/sqlserver/home?forum=AzureSQLDataWarehouse) 다른 사용자와 Azure Synapse 제품 그룹에 질문을 게시할 수 있는 장소입니다.  Microsoft는 이 포럼을 적극적으로 모니터링하여 사용자의 질문에 다른 사용자나 당사 직원이 응답하도록 합니다.  

스택 오버플로에 질문하는 것을 선호하는 경우 [Azure SQL Data Warehouse 스택 오버플로 포럼](https://stackoverflow.com/questions/tagged/azure-sqldw)도 제공합니다.

Azure [시냅스 피드백](https://feedback.azure.com/forums/307516-sql-data-warehouse) 페이지를 사용하여 기능을 요청합니다.  요청을 추가하거나 다른 요청에 투표를 하면 기능의 순위를 지정하는 데 도움이 됩니다.


