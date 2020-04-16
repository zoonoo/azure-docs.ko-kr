---
title: SQL 풀에 대한 모범 사례
description: SQL 풀로 작업할 때 알아야 할 권장 사항 및 모범 사례입니다.
services: synapse-analytics
author: mlee3gsd
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/15/2020
ms.author: martinle
ms.reviewer: igorstan
ms.openlocfilehash: b3dc111fe62cbae857f3369165ba29cf40e90342
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81427797"
---
# <a name="best-practices-for-sql-pools-in-azure-synapse-analytics"></a>Azure 시냅스 분석의 SQL 풀에 대한 모범 사례

이 문서에서는 Azure Synapse Analytics에서 SQL 풀에 대한 최적의 성능을 달성하는 데 도움이 되는 모범 사례 모음을 제공합니다. 아래에서 솔루션을 구축할 때 중점을 두어야 할 기본 지침과 중요한 영역을 확인할 수 있습니다. 각 섹션에서는 개념을 소개한 다음 개념을 보다 심층적인 세부 기사로 설명합니다.

## <a name="sql-pools-loading"></a>SQL 풀 로드

SQL 풀 로드 지침은 [데이터 로드에 대한 지침을](data-loading-best-practices.md)참조하십시오.

## <a name="reduce-cost-with-pause-and-scale"></a>일시 중지 및 규모 조정으로 비용 절감

일시 중지 및 크기 조정을 통해 비용을 절감하는 방법에 대한 자세한 내용은 [계산 관리를](../sql-data-warehouse/sql-data-warehouse-manage-compute-overview.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)참조하십시오.

## <a name="maintain-statistics"></a>통계 유지 관리

SQL Server는 열에 대한 통계를 자동으로 검색하고 생성하거나 업데이트하지만 SQL 풀에는 통계를 수동으로 유지 관리해야 합니다. SQL 풀 계획이 최적화되었는지 확인하기 위해 통계를 유지 관리해야 합니다.  최적화 프로그램으로 만든 계획은 사용 가능한 통계만큼 훌륭합니다.

> [!TIP]
> 모든 열에 샘플링된 통계를 만드는 것이 통계로 시작하는 쉬운 방법입니다.  

 데이터에 중요한 변경 내용이 있는 것 만큼 통계를 업데이트하는 것도 중요합니다.  보수적인 접근 방법은 매일 또는 각 로드 후 통계를 업데이트하는 것일 수 있습니다.  통계를 작성하고 업데이트하는 성능 및 비용 간의 장단점은 항상 있습니다.

통계 유지 관리 시간을 단축하려면 통계가 있는 열이나 가장 자주 업데이트해야 하는 열을 선택적으로 지정해야 합니다. 예를 들어 새 값이 매일 추가될 수 있는 날짜 열을 업데이트할 수 있습니다. 조인과 관련된 열, WHERE 절에 사용되는 열 및 GROUP BY에 있는 열에 대한 통계를 사용하는 데 중점을 둡니다.

통계에 대한 추가 정보는 [테이블 통계 관리,](develop-tables-statistics.md) [통계 만들기](/sql/t-sql/statements/create-statistics-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)및 통계 [업데이트](/sql/t-sql/statements/update-statistics-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) 문서에서 찾을 수 있습니다.

## <a name="group-insert-statements-into-batches"></a>INSERT 문을 일괄 처리로 그룹화

INSERT 문이 있는 작은 테이블에 일회성 로드가 `INSERT INTO MyLookup VALUES (1, 'Type 1')`필요에 따라 가장 좋은 방법일 수 있습니다. 그러나 하루 종일 수천 또는 수백만 개의 행을 로드해야하는 경우 singleton INSERTS가 최적이 아닌 것일 수 있습니다.

이 문제를 해결하는 한 가지 방법은 파일에 쓰는 프로세스를 개발한 다음 이 파일을 주기적으로 로드하는 다른 프로세스를 개발하는 것입니다. 자세한 내용은 [INSERT](/sql/t-sql/statements/insert-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) 문서를 참조하십시오.

## <a name="use-polybase-to-load-and-export-data-quickly"></a>PolyBase를 사용하여 데이터를 신속하고 로드하고 내보내기

SQL 풀은 Azure 데이터 팩터리, PolyBase 및 BCP를 비롯한 여러 도구를 통해 데이터 로드 및 내보내기를 지원합니다.  성능이 중요하지 않은 소량의 데이터에는 어떤 도구를 사용해도 사용자 요구 사항을 충족할 수 있습니다.  

> [!NOTE]
> Polybase는 대용량 데이터를 로드하거나 내보낼 때 가장 좋은 선택이거나 더 빠른 성능이 필요합니다.

PolyBase 로드는 CTAS 또는 INSERT INTO를 사용하여 실행할 수 있습니다. CTAS는 트랜잭션 로깅을 최소화하고 데이터를 로드하는 가장 빠른 방법입니다. 또한 Azure 데이터 팩터리는 PolyBase 로드를 지원하며 CTAS와 유사한 성능을 얻을 수 있습니다. PolyBase는 Gzip 파일을 포함한 다양한 파일 형식을 지원합니다.

Gzip 텍스트 파일을 사용할 때 처리량을 최대화하려면 파일을 60개 이상의 파일로 분할하여 로드의 병렬성을 최대화합니다.  총 처리량을 더 빠르게 하기 위해 데이터를 동시에 로드하는 것이 좋습니다. 이 섹션과 관련된 항목에 대한 추가 정보는 다음 문서에 포함되어 있습니다.

- [데이터 로드](data-loading-overview.md)
- [PolyBase 사용 지침](data-loading-best-practices.md)
- [Azure SQL 풀 로드 패턴 및 전략](https://blogs.msdn.microsoft.com/sqlcat/20../../azure-sql-data-warehouse-loading-patterns-and-strategies/)
- [Azure 데이터 팩터리로 데이터 로드](../../data-factory/load-azure-sql-data-warehouse.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
- [Azure Data Factory를 사용하여 데이터 이동](../../data-factory/transform-data-using-machine-learning.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
- [외부 파일 형식 만들기](/sql/t-sql/statements/create-external-file-format-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
- [선택으로 테이블 만들기(CTAS)](../sql-data-warehouse/sql-data-warehouse-develop-ctas.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)

## <a name="load-then-query-external-tables"></a>외부 테이블 로드 후 쿼리

폴리베이스는 쿼리에 적합하지 않습니다. SQL 풀에 대한 폴리베이스 테이블은 현재 Azure Blob 파일 및 Azure Data Lake 저장소만 지원합니다. 이러한 파일에는 백업하는 계산 리소스가 없습니다. 따라서 SQL 풀은 이 작업을 오프로드할 수 없으며 데이터를 읽을 수 있도록 tempdb에 로드하여 전체 파일을 읽어야 합니다.

이 데이터를 쿼리하기 위한 쿼리가 여러 개 있는 경우 이 데이터를 한 번 로드하고 쿼리에서 로컬 테이블을 사용하도록 하는 것이 좋습니다. PolyBase 사용 설명서에는 [PolyBase](data-loading-best-practices.md) 지침이 포함되어 있습니다.

## <a name="hash-distribute-large-tables"></a>해시 배포 대형 테이블

기본적으로 테이블은 라운드 로빈 분산됩니다.   이 기본값을 사용하면 테이블을 배포하는 방법을 결정할 필요 없이 쉽게 테이블 만들기를 시작할 수 있습니다. 라운드 로빈 테이블은 일부 워크로드에 대해 충분히 수행할 수 있습니다. 그러나 대부분의 경우 배포 열은 더 나은 성능을 제공합니다.  

라운드 로빈 테이블을 능가하는 열에서 배포되는 테이블의 가장 일반적인 예는 두 개의 큰 팩트 테이블이 조인되는 경우입니다.  

예를 들어 order_id 의해 분산된 주문 테이블이 있고 order_id 의해 분산된 트랜잭션 테이블이 있는 경우 order_id 주문 테이블을 트랜잭션 테이블에 조인하면 이 쿼리는 통과 쿼리가 됩니다. 그런 다음 데이터 이동 작업이 제거됩니다. 단계가 적을수록 쿼리는 빨라집니다. 데이터 이동이 적을수록 쿼리는 빨라집니다.

> [!NOTE]
> 분산 테이블을 로드할 때 들어오는 데이터는 배포 키에서 정렬해서는 안 됩니다. 이렇게 하면 부하가 느려집니다.

아래에 제공된 문서 링크는 배포 열을 선택하여 성능 향상에 대한 추가 세부 정보를 제공합니다. 또한 CREATE TABLE 문의 WITH 절에서 분산 테이블을 정의하는 방법에 대한 정보를 찾을 수 있습니다.

- [테이블 개요](develop-tables-overview.md)
- [테이블 분포](../sql-data-warehouse/sql-data-warehouse-tables-distribute.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
- [테이블 배포 선택](https://blogs.msdn.microsoft.com/sqlcat/20../../choosing-hash-distributed-table-vs-round-robin-distributed-table-in-azure-sql-dw-service/)
- [CREATE TABLE](/sql/t-sql/statements/create-table-azure-sql-data-warehouse?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
- [CREATE TABLE AS SELECT](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)

## <a name="do-not-over-partition"></a>과도한 분할 피하기

데이터를 분할하는 것은 파티션 전환을 통해 데이터를 유지 관리하거나 파티션 제거를 통해 스캔을 최적화하는 데 효과적일 수 있지만 파티션이 너무 많면 쿼리 속도가 느려질 수 있습니다.  SQL Server에서 잘 작동할 수 있는 높은 세분성 분할 전략이 SQL 풀에서 잘 작동하지 않는 경우가 많습니다.  

파티션이 너무 많으면 각 파티션에 1백만 개 미만의 행이 있는 경우 클러스터된 columnstore 인덱스의 효율성이 떨어질 수 있습니다. SQL 풀은 데이터를 60개의 데이터베이스로 자동으로 분할합니다. 따라서 파티션이 100개인 테이블을 만들면 6000개의 파티션이 생성됩니다. 각 워크로드는 서로 다르므로 가장 좋은 방법은 파티션을 실험하여 워크로드에 가장 적합한 작업을 확인하는 것입니다.  

고려해야 할 한 가지 옵션은 SQL Server를 사용하여 구현한 것보다 낮은 세분성을 사용하는 것입니다. 예를 들어, 일일 파티션 대신 주간 또는 월별 파티션을 사용하는 것이 좋습니다.

분할에 대한 자세한 내용은 [테이블 분할](../sql-data-warehouse/sql-data-warehouse-tables-partition.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) 문서에서 자세히 설명합니다.

## <a name="minimize-transaction-sizes"></a>트랜잭션 크기 최소화

INSERT, 업데이트 및 DELETE 문은 트랜잭션에서 실행됩니다. 실패하면 롤백해야 합니다. 긴 롤백 가능성을 줄이려면 가능하면 트랜잭션 크기를 최소화합니다.  INSERT, UPDATE 및 DELETE 문을 부분으로 나누어 트랜잭션 크기를 최소화할 수 있습니다. 예를 들어 1시간이 걸릴 것으로 예상되는 INSERT가 있는 경우 INSERT를 네 부분으로 나누면 됩니다. 그러면 각 실행이 15분으로 단축됩니다.  

> [!TIP]
> CTAS, TRUNCATE, DROP 테이블 또는 INSERT와 같은 특수 최소 로깅 사례를 빈 테이블에 활용하여 롤백 위험을 줄입니다.  

롤백을 제거하는 다른 방법은 데이터 관리를 위한 파티션 전환과 같은 메타데이터 전용 작업을 사용하는 것입니다.  예를 들어 DELETE 문을 실행하여 order_date 2001년 10월에 있던 테이블의 모든 행을 삭제하는 대신 데이터를 매월 분할할 수 있습니다. 그런 다음 다른 테이블의 빈 파티션에 대한 데이터로 파티션을 전환할 수 있습니다(ALTER TABLE 예제 참조).  

분할되지 않은 테이블의 경우 CTAS를 사용하여 DELETE를 사용하는 대신 테이블에 보관할 데이터를 쓰는 것이 좋습니다.  CTAS에 동일한 시간이 걸리는 경우 트랜잭션 로깅이 최소화되고 필요한 경우 신속하게 취소할 수 있으므로 실행하는 것이 훨씬 안전합니다.

이 섹션과 관련된 콘텐츠에 대한 자세한 내용은 아래 문서에 포함되어 있습니다.

- [선택으로 테이블 만들기(CTAS)](../sql-data-warehouse/sql-data-warehouse-develop-ctas.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
- [트랜잭션 이해](develop-transactions.md)
- [트랜잭션 최적화](../sql-data-warehouse/sql-data-warehouse-develop-best-practices-transactions.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
- [테이블 분할](../sql-data-warehouse/sql-data-warehouse-tables-partition.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
- [트런케이트 테이블](/sql/t-sql/statements/truncate-table-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
- [테이블 변경](/sql/t-sql/statements/alter-table-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)

## <a name="reduce-query-result-sizes"></a>쿼리 결과 크기 줄이기

쿼리 결과 크기를 줄이면 큰 쿼리 결과로 인한 클라이언트 측 문제를 방지할 수 있습니다.  쿼리를 편집하여 반환되는 행 수를 줄일 수 있습니다. 일부 쿼리 생성 도구를 사용하면 각 쿼리에 "상위 N" 구문을 추가할 수 있습니다.  쿼리 결과를 임시 테이블에 CETAS한 다음 하위 수준 처리에 PolyBase 내보내기를 사용할 수도 있습니다.

## <a name="use-the-smallest-possible-column-size"></a>가능한 가장 작은 열 크기 사용

DDL을 정의할 때 데이터를 지원하는 가장 작은 데이터 형식을 사용하여 쿼리 성능을 향상시킵니다.  이 권장 사항은 CHAR 및 VARCHAR 열에 특히 중요합니다.  열에서 가장 긴 값이 25자인 경우 열을 VARCHAR(25)로 정의합니다.  모든 문자 열을 큰 기본 길이로 정의하지 마세요.  또한 NVARCHAR를 사용하는 대신 필요한 모든 경우 열을 VARCHAR로 정의합니다.

위의 정보와 관련된 필수 개념에 대한 자세한 내용은 [표 개요,](develop-tables-overview.md) [표 데이터 유형](develop-tables-data-types.md)및 테이블 [만들기](/sql/t-sql/statements/create-table-azure-sql-data-warehouse?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) 문서를 참조하십시오.

## <a name="use-temporary-heap-tables-for-transient-data"></a>임시 데이터에 대해 임시 힙 테이블 사용

SQL 풀에 데이터를 일시적으로 방문하면 힙 테이블이 일반적으로 전체 프로세스를 더 빠르게 만듭니다.  더 많은 변환을 실행하기 전에 데이터를 스테이지로 만 로드하는 경우 테이블을 힙 테이블에 로드하는 것이 클러스터된 columnstore 테이블에 데이터를 로드하는 것보다 빠를 수 있습니다.  

임시 테이블에 데이터를 로드하면 테이블을 영구 저장소로 로드하는 것보다 훨씬 빠르게 로드됩니다.  임시 테이블은 "#"로 시작하여 테이블을 만든 세션에서만 액세스할 수 있습니다. 따라서 제한된 시나리오에서만 작동할 수 있습니다. 힙 테이블은 CREATE TABLE의 WITH 절에 정의됩니다.  임시 테이블을 사용하는 경우 해당 임시 테이블에서도 통계를 작성해야 합니다.

추가 지침은 [임시 테이블,](/sql/t-sql/statements/alter-table-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) [테이블 만들기](/sql/t-sql/statements/create-table-azure-sql-data-warehouse?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)및 선택 으로 [테이블 만들기](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) 문서를 참조하십시오.

## <a name="optimize-clustered-columnstore-tables"></a>클러스터형 Columnstore 테이블 최적화

클러스터된 columnstore 인덱스는 SQL 풀에 데이터를 저장할 수 있는 가장 효율적인 방법 중 하나입니다.  기본적으로 SQL 풀의 테이블은 클러스터된 ColumnStore로 만들어집니다.  columnstore 테이블에서 쿼리에 대해 최상의 성능을 얻으려면 좋은 세그먼트 품질을 갖는 것이 중요합니다.  메모리 부족 상황에서 행이 columnstore 테이블에 기록되는 경우 columnstore 세그먼트 품질이 저하될 수 있습니다.  

세그먼트 품질은 압축된 행 그룹의 행 수로 측정할 수 있습니다. 그룹화 된 columnstore 테이블의 세그먼트 품질 검색 및 개선에 대한 단계별 지침은 [테이블 인덱스](../sql-data-warehouse/sql-data-warehouse-tables-index.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) 문서의 [열저장소 인덱스 품질 저하의 원인을](../sql-data-warehouse/sql-data-warehouse-tables-index.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json#causes-of-poor-columnstore-index-quality) 참조하십시오.  

고품질 columnstore 세그먼트는 중요하므로 데이터를 로드하기 위해 중간 또는 큰 리소스 클래스에 있는 사용자 아이디를 사용하는 것이 좋습니다. 낮은 [데이터 웨어하우스 단위](resource-consumption-models.md)를 사용하면 로드하는 사용자에게 더 큰 리소스 클래스를 할당하려는 것입니다.

Columnstore 테이블은 일반적으로 테이블당 1백만 개 이상의 행이 있을 때까지 압축된 columnstore 세그먼트로 데이터를 푸시하지 않습니다. 각 SQL 풀테이블은 60개의 테이블로 분할됩니다. 따라서 열 저장소 테이블테이블에 6천만 개 이상의 행이 있는 경우 쿼리에 이점이 없습니다.  

> [!TIP]
> 행이 6천만 개 미만인 테이블의 경우 columnstore 인덱스가 있는 것이 최적의 솔루션이 아닐 수 있습니다.  

데이터를 분할하는 경우 클러스터된 columnstore 인덱스의 이점을 얻으려면 각 파티션에 1백만 개의 행이 있어야 합니다.  파티션이 100개인 테이블의 경우 클러스터된 열 저장소(60개의 배포판 *100개 파티션 100개* 행)의 이점을 활용하려면 최소 60억 개의 행이 있어야 합니다.  

테이블에 60억 개의 행이 없는 경우 두 가지 기본 옵션이 있습니다. 파티션 수를 줄이거나 대신 힙 테이블을 사용하는 것이 좋습니다.  또한 columnstore 테이블이 아닌 보조 인덱스가 있는 힙 테이블을 사용하여 더 나은 성능을 얻을 수 있는지 실험해 볼 수도 있습니다.

columnstore 테이블을 쿼리할 때 필요한 열만 선택하면 쿼리가 더 빨리 실행됩니다.  테이블 및 열 저장소 인덱스에 대한 자세한 내용은 [테이블 인덱스,](../sql-data-warehouse/sql-data-warehouse-tables-index.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) [Columnstore 인덱스 가이드](/sql/relational-databases/indexes/columnstore-indexes-overview?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)및 열 저장소 인덱스 다시 빌드 문서에서 찾을 수 [있습니다.](../sql-data-warehouse/sql-data-warehouse-tables-index.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest#rebuilding-indexes-to-improve-segment-quality)

## <a name="use-larger-resource-class-to-improve-query-performance"></a>더 큰 리소스 클래스를 사용하여 쿼리 성능 향상

SQL 풀은 쿼리에 메모리를 할당하는 방법으로 리소스 그룹을 사용합니다. 처음에는 모든 사용자가 배포당 100MB의 메모리를 부여하는 작은 리소스 클래스에 할당됩니다.  항상 60개의 분포가 있습니다. 각 배포에는 최소 100MB가 제공됩니다. 총 시스템 전체 메모리 할당은 6,000MB 또는 6GB 미만입니다.  

큰 조인 또는 클러스터형 columnstore 테이블에 로드와 같은 특정 쿼리는 큰 메모리를 할당하는 것이 도움이 됩니다.  순수 검사와 같은 일부 쿼리에는 이점이 없습니다. 더 큰 리소스 클래스를 사용하면 동시성에 영향을 미칩니다. 따라서 모든 사용자를 대규모 리소스 클래스로 이동하기 전에 이러한 사실을 염두에 두어야 합니다.

리소스 클래스에 대한 자세한 내용은 워크로드 관리 문서에 [대한 리소스 클래스를](../sql-data-warehouse/resource-classes-for-workload-management.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) 참조하십시오.

## <a name="use-smaller-resource-class-to-increase-concurrency"></a>더 작은 리소스 클래스를 사용하여 동시성 증가

사용자 쿼리가 오래 지연되는 경우 사용자가 더 큰 리소스 클래스에서 실행중일 수 있습니다. 이 시나리오에서는 동시성 슬롯의 소비를 촉진하므로 다른 쿼리가 큐에 대기될 수 있습니다.  사용자 쿼리가 큐에 대기되어 `SELECT * FROM sys.dm_pdw_waits` 있는지 확인하려면 실행하여 행이 반환되는지 확인합니다.

[워크로드 관리를 위한 리소스 클래스](../sql-data-warehouse/resource-classes-for-workload-management.md) 및 [sys.dm_pdw_waits](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-waits-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) 문서에서 자세한 정보를 제공합니다.

## <a name="use-dmvs-to-monitor-and-optimize-your-queries"></a>DMV를 사용하여 쿼리 모니터링 및 최적화

SQL 풀에는 쿼리 실행을 모니터링하는 데 사용할 수 있는 여러 DMV가 있습니다.  아래 모니터링 문서에서는 실행 쿼리의 세부 정보를 보는 방법에 대한 단계별 지침을 안내합니다.  이러한 DMV에서 쿼리를 신속하게 찾으려면 쿼리에 LABEL 옵션을 사용하는 것이 도움이 될 수 있습니다. 자세한 내용은 아래 목록에 포함된 문서를 참조하십시오.

- [DMV를 사용하여 작업 모니터링](../sql-data-warehouse/sql-data-warehouse-manage-monitor.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)

- [레이블](develop-label.md)
- [옵션](/sql/t-sql/queries/option-clause-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
- [sys.dm_exec_sessions](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-sessions-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
- [sys.dm_pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
- [sys.dm_pdw_request_steps](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-request-steps-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
- [sys.dm_pdw_sql_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-sql-requests-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
- [sys.dm_pdw_dms_workers](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-dms-workers-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
- [DBCC PDW_SHOWEXECUTIONPLAN](/sql/t-sql/database-console-commands/dbcc-pdw-showexecutionplan-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
- [sys.dm_pdw_waits](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-waits-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)

## <a name="next-steps"></a>다음 단계

또한 일반적인 문제 및 해결 방법은 [문제 해결](../sql-data-warehouse/sql-data-warehouse-troubleshoot.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) 문서를 참조하십시오.

이 문서에서 제공하지 않은 정보가 필요한 경우 이 페이지 왼쪽에 있는 "문서 검색"을 사용하여 모든 SQL 풀 문서를 검색합니다.  [SQL 풀 포럼은](https://social.msdn.microsoft.com/Forums/sqlserver/home?forum=AzureSQLDataWarehouse) 다른 사용자와 SQL 풀 제품 그룹에 질문을 제기할 수 있는 장소입니다.  

Microsoft는 이 포럼을 적극적으로 모니터링하여 사용자의 질문에 다른 사용자나 당사 직원이 응답하도록 합니다.  스택 오버플로에 대한 질문을 하려는 경우 Azure [SQL 풀 스택 오버플로 포럼도](https://stackoverflow.com/questions/tagged/azure-sqldw)있습니다.

기능 요청의 경우 [Azure SQL 풀 피드백](https://feedback.azure.com/forums/307516-sql-data-warehouse) 페이지를 사용합니다.  요청을 추가하거나 다른 요청에 대한 투표를 통해 가장 수요가 많은 기능에 집중할 수 있습니다.
