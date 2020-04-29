---
title: SQL 풀에 대 한 모범 사례
description: SQL 풀을 사용할 때 알아야 할 권장 사항 및 모범 사례입니다.
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81427797"
---
# <a name="best-practices-for-sql-pools-in-azure-synapse-analytics"></a>Azure Synapse Analytics의 SQL 풀에 대 한 모범 사례

이 문서에서는 Azure Synapse Analytics에서 SQL 풀의 성능을 최적화 하는 데 도움이 되는 모범 사례 모음을 제공 합니다. 아래에서 솔루션을 빌드할 때 중점적으로 살펴볼 기본 지침과 중요 영역을 찾을 수 있습니다. 각 섹션에서는 개념을 소개 하 고 개념을 더 자세히 설명 하는 더 자세한 문서를 제공 합니다.

## <a name="sql-pools-loading"></a>SQL 풀 로드

SQL 풀 로드 지침은 [데이터를 로드 하는 방법에 대 한 지침](data-loading-best-practices.md)을 참조 하세요.

## <a name="reduce-cost-with-pause-and-scale"></a>일시 중지 및 규모 조정으로 비용 절감

일시 중지 및 크기 조정을 통해 비용을 줄이는 방법에 대 한 자세한 내용은 [계산 관리](../sql-data-warehouse/sql-data-warehouse-manage-compute-overview.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)를 참조 하세요.

## <a name="maintain-statistics"></a>통계 유지 관리

SQL Server 자동으로 검색 하 여 열에 대 한 통계를 만들거나 업데이트 하는 동안 SQL 풀에서 통계를 수동으로 유지 관리 해야 합니다. SQL 풀 계획이 최적화 되도록 통계를 유지 관리 하는 것이 좋습니다.  최적화 프로그램으로 만든 계획은 사용 가능한 통계만큼 훌륭합니다.

> [!TIP]
> 모든 열에 샘플링된 통계를 만드는 것이 통계로 시작하는 쉬운 방법입니다.  

 데이터에 중요한 변경 내용이 있는 것 만큼 통계를 업데이트하는 것도 중요합니다.  보수적인 접근 방법은 매일 또는 각 로드 후 통계를 업데이트하는 것일 수 있습니다.  통계를 작성하고 업데이트하는 성능 및 비용 간의 장단점은 항상 있습니다.

통계 유지 관리 시간을 줄이려면 통계가 있는 열을 선택 하거나 가장 자주 업데이트 해야 합니다. 예를 들어 매일 새 값이 추가 될 수 있는 날짜 열을 업데이트 하려고 할 수 있습니다. 조인에 포함 된 열, WHERE 절에 사용 되는 열 및 GROUP BY에 있는 열에 대 한 통계를 사용 하는 데 초점을 둡니다.

통계에 대 한 추가 정보는 [테이블 통계 관리](develop-tables-statistics.md), [통계 만들기](/sql/t-sql/statements/create-statistics-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)및 [통계 업데이트](/sql/t-sql/statements/update-statistics-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) 문서에서 찾을 수 있습니다.

## <a name="group-insert-statements-into-batches"></a>INSERT 문을 일괄 처리로 그룹화

와 `INSERT INTO MyLookup VALUES (1, 'Type 1')`같이 INSERT 문이 있는 작은 테이블에 대 한 일회성 로드는 요구 사항에 따라 가장 적합 한 방법일 수 있습니다. 그러나 하루 종일 수천 또는 수백만 개의 행을 로드 해야 하는 경우에는 singleton 삽입이 최적이 아닐 수 있습니다.

이 문제를 해결 하는 한 가지 방법은 파일에 쓰는 프로세스 하나를 개발한 다음 다른 프로세스에서이 파일을 주기적으로 로드 하는 것입니다. 자세한 내용은 [삽입](/sql/t-sql/statements/insert-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) 문서를 참조 하세요.

## <a name="use-polybase-to-load-and-export-data-quickly"></a>PolyBase를 사용하여 데이터를 신속하고 로드하고 내보내기

SQL 풀은 Azure Data Factory, PolyBase 및 BCP를 비롯 한 여러 도구를 통해 데이터를 로드 하 고 내보내는 것을 지원 합니다.  성능이 중요하지 않은 소량의 데이터에는 어떤 도구를 사용해도 사용자 요구 사항을 충족할 수 있습니다.  

> [!NOTE]
> Polybase는 대량의 데이터를 로드 하거나 내보내는 경우 나 더 빠른 성능이 필요한 경우에 가장 적합 합니다.

PolyBase 로드는 CTAS 또는 INSERT INTO를 사용하여 실행할 수 있습니다. CTAS는 트랜잭션 로깅을 최소화 하 고 데이터를 로드 하는 가장 빠른 방법입니다. Azure Data Factory PolyBase 로드도 지원 하 고 CTAS와 비슷한 성능을 제공할 수 있습니다. PolyBase는 Gzip 파일을 비롯 한 다양 한 파일 형식을 지원 합니다.

Gzip 텍스트 파일을 사용할 때 처리량을 최대화 하려면 파일을 60 이상으로 분할 하 여 부하의 병렬 처리를 최대화 합니다.  총 처리량을 더 빠르게 하기 위해 데이터를 동시에 로드하는 것이 좋습니다. 이 섹션과 관련 된 항목에 대 한 추가 정보는 다음 문서에 포함 되어 있습니다.

- [데이터 로드](data-loading-overview.md)
- [PolyBase 사용 지침](data-loading-best-practices.md)
- [Azure SQL 풀 로드 패턴 및 전략](https://blogs.msdn.microsoft.com/sqlcat/20../../azure-sql-data-warehouse-loading-patterns-and-strategies/)
- [Azure Data Factory를 사용 하 여 데이터 로드](../../data-factory/load-azure-sql-data-warehouse.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
- [Azure Data Factory를 사용하여 데이터 이동](../../data-factory/transform-data-using-machine-learning.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
- [CREATE EXTERNAL FILE FORMAT](/sql/t-sql/statements/create-external-file-format-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
- [CTAS (Create table as select)](../sql-data-warehouse/sql-data-warehouse-develop-ctas.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)

## <a name="load-then-query-external-tables"></a>외부 테이블 로드 후 쿼리

Polybase는 쿼리에 적합 하지 않습니다. SQL 풀의 Polybase 테이블은 현재 Azure blob 파일 및 Azure Data Lake 저장소만 지원 합니다. 이러한 파일에는이를 지 원하는 계산 리소스가 없습니다. 따라서 SQL 풀은이 작업을 오프 로드할 수 없으며 데이터를 읽을 수 있도록 tempdb에 로드 하 여 전체 파일을 읽어야 합니다.

이 데이터를 쿼리 하는 쿼리를 여러 개 사용 하는 경우이 데이터를 한 번 로드 하 고 쿼리에서 로컬 테이블을 사용 하는 것이 좋습니다. 자세한 Polybase 지침은 [polybase 사용 가이드](data-loading-best-practices.md) 문서에 포함 되어 있습니다.

## <a name="hash-distribute-large-tables"></a>해시 배포 대형 테이블

기본적으로 테이블은 라운드 로빈 분산됩니다.   이 기본값을 사용 하면 사용자가 테이블을 배포 하는 방법을 결정 하지 않고도 쉽게 테이블을 만들 수 있습니다. 라운드 로빈 테이블은 일부 워크 로드에 대해 충분히 수행할 수 있습니다. 그러나 대부분의 경우에는 배포 열이 더 나은 성능을 제공 합니다.  

라운드 로빈 테이블을 수행 하는 열을 기준으로 분산 된 테이블의 가장 일반적인 예는 두 개의 큰 팩트 테이블이 조인 되는 경우입니다.  

예를 들어 order_id에 의해 분산 된 orders 테이블과 order_id를 통해 트랜잭션 테이블을 분산 하는 경우 order_id의 트랜잭션 테이블에 orders 테이블을 조인할 때이 쿼리는 통과 쿼리가 됩니다. 그런 다음 데이터 이동 작업을 제거 합니다. 단계가 적을수록 쿼리는 빨라집니다. 데이터 이동이 적을수록 쿼리는 빨라집니다.

> [!NOTE]
> 분산 테이블을 로드할 때 들어오는 데이터는 배포 키에서 정렬 되지 않아야 합니다. 이렇게 하면 부하가 느려집니다.

아래에 제공 된 문서 링크를 통해 배포 열을 선택 하 여 성능 향상에 대 한 추가 정보를 얻을 수 있습니다. 또한 CREATE TABLE 문의 WITH 절에서 분산 테이블을 정의 하는 방법에 대 한 정보를 찾을 수 있습니다.

- [테이블 개요](develop-tables-overview.md)
- [테이블 배포](../sql-data-warehouse/sql-data-warehouse-tables-distribute.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
- [테이블 배포 선택](https://blogs.msdn.microsoft.com/sqlcat/20../../choosing-hash-distributed-table-vs-round-robin-distributed-table-in-azure-sql-dw-service/)
- [CREATE TABLE](/sql/t-sql/statements/create-table-azure-sql-data-warehouse?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
- [CREATE TABLE AS SELECT](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)

## <a name="do-not-over-partition"></a>과도한 분할 피하기

분할 데이터는 파티션 전환을 통해 데이터를 유지 관리 하거나 파티션 제거를 사용 하 여 검색을 최적화 하는 데 효과적일 수 있지만 너무 많은 파티션이 있으면 쿼리 속도가 느려질 수 있습니다.  종종 SQL 풀에서 제대로 작동 하지 않을 수 SQL Server에서 잘 작동 하는 높은 세분성 분할 전략이 있습니다.  

파티션이 너무 많으면 각 파티션에 100만 개 미만의 행이 있는 경우 클러스터형 columnstore 인덱스의 효율성이 저하 될 수 있습니다. SQL 풀은 데이터를 자동으로 60 데이터베이스로 분할 합니다. 따라서 100 파티션을 포함 하는 테이블을 만드는 경우에는 6000 파티션이 생성 됩니다. 각 워크 로드는 서로 다르므로 분할을 시험해 보고 작업에 가장 적합 한 것을 확인 하는 것이 가장 좋습니다.  

고려해 야 할 한 가지 옵션은 SQL Server를 사용 하 여 구현 된 것 보다 낮은 세분성을 사용 하는 것입니다. 예를 들어 일별 파티션 대신 주별 또는 월별 파티션을 사용 하는 것이 좋습니다.

분할에 대 한 자세한 내용은 [테이블 분할](../sql-data-warehouse/sql-data-warehouse-tables-partition.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) 문서에 자세히 설명 되어 있습니다.

## <a name="minimize-transaction-sizes"></a>트랜잭션 크기 최소화

INSERT, UPDATE 및 DELETE 문이 트랜잭션에서 실행 됩니다. 오류가 발생 하면 롤백해야 합니다. 긴 롤백에 대 한 가능성을 줄이기 위해 가능한 경우 트랜잭션 크기를 최소화 합니다.  INSERT, UPDATE 및 DELETE 문을 파트로 나누면 트랜잭션 크기를 최소화할 수 있습니다. 예를 들어 1 시간을 소요 하는 삽입이 있는 경우 삽입을 네 부분으로 나눌 수 있습니다. 그러면 각 실행이 15 분으로 단축 됩니다.  

> [!TIP]
> CTAS, TRUNCATE, DROP TABLE 또는 빈 테이블에 삽입과 같은 특수 최소 로깅 사례를 활용 하 여 롤백 위험을 줄입니다.  

롤백을 제거하는 다른 방법은 데이터 관리를 위한 파티션 전환과 같은 메타데이터 전용 작업을 사용하는 것입니다.  예를 들어 order_date가 2001 년 10 월에 있었던 테이블의 모든 행을 삭제 하는 DELETE 문을 실행 하는 대신 매월 데이터를 분할할 수 있습니다. 그런 다음 다른 테이블에서 빈 파티션의 데이터로 파티션을 전환할 수 있습니다 (ALTER TABLE 예제 참조).  

분할 되지 않은 테이블의 경우 DELETE를 사용 하는 대신 CTAS를 사용 하 여 테이블에 유지할 데이터를 작성 하는 것이 좋습니다.  CTAS가 같은 시간을 사용 하는 경우 최소한의 트랜잭션 로깅을 사용 하 고 필요한 경우 신속 하 게 취소할 수 있으므로 실행 하는 것이 훨씬 안전 합니다.

이 섹션과 관련 된 콘텐츠에 대 한 자세한 내용은 아래 문서에 포함 되어 있습니다.

- [CTAS (Create table as select)](../sql-data-warehouse/sql-data-warehouse-develop-ctas.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
- [트랜잭션 이해](develop-transactions.md)
- [트랜잭션 최적화](../sql-data-warehouse/sql-data-warehouse-develop-best-practices-transactions.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
- [테이블 분할](../sql-data-warehouse/sql-data-warehouse-tables-partition.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
- [TRUNCATE TABLE](/sql/t-sql/statements/truncate-table-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
- [ALTER TABLE](/sql/t-sql/statements/alter-table-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)

## <a name="reduce-query-result-sizes"></a>쿼리 결과 크기 줄이기

쿼리 결과 크기를 줄이면 쿼리 결과가 클 때 발생 하는 클라이언트 쪽 문제를 방지할 수 있습니다.  쿼리를 편집 하 여 반환 되는 행 수를 줄일 수 있습니다. 일부 쿼리 생성 도구를 사용 하면 각 쿼리에 "top N" 구문을 추가할 수 있습니다.  쿼리 결과를 임시 테이블에 CETAS 다음 하위 수준 처리를 위해 PolyBase 내보내기를 사용할 수도 있습니다.

## <a name="use-the-smallest-possible-column-size"></a>가능한 가장 작은 열 크기 사용

DDL을 정의 하는 경우 데이터를 지 원하는 가장 작은 데이터 형식을 사용 하 여 쿼리 성능을 향상 시킵니다.  이 권장 사항은 CHAR 및 VARCHAR 열에 특히 중요 합니다.  열에서 가장 긴 값이 25자인 경우 열을 VARCHAR(25)로 정의합니다.  모든 문자 열을 큰 기본 길이로 정의하지 마세요.  또한 NVARCHAR를 사용 하는 대신 필요한 모든 열을 VARCHAR로 정의 합니다.

위의 정보와 관련 된 중요 한 개념에 대 한 자세한 내용은 [테이블 개요](develop-tables-overview.md), [테이블 데이터 형식](develop-tables-data-types.md)및 [CREATE TABLE](/sql/t-sql/statements/create-table-azure-sql-data-warehouse?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) 문서를 참조 하세요.

## <a name="use-temporary-heap-tables-for-transient-data"></a>임시 데이터에 대해 임시 힙 테이블 사용

SQL 풀에서 일시적으로 데이터를 방문 하는 경우 힙 테이블은 일반적으로 전체 프로세스를 빠르게 수행 합니다.  더 많은 변환을 실행 하기 전에 데이터를 준비 하기 위해 데이터를 로드 하는 경우 테이블을 힙 테이블에 로드 하는 것이 클러스터형 columnstore 테이블에 데이터를 로드 하는 것 보다 빠릅니다.  

데이터를 임시 테이블에 로드 하면 테이블을 영구 저장소에 로드 하는 것 보다 훨씬 빠르게 로드 됩니다.  임시 테이블은 "#"으로 시작 하 고이를 만든 세션 에서만 액세스할 수 있습니다. 따라서 제한 된 시나리오 에서만 작동할 수 있습니다. 힙 테이블은 CREATE TABLE의 WITH 절에 정의됩니다.  임시 테이블을 사용하는 경우 해당 임시 테이블에서도 통계를 작성해야 합니다.

추가 지침은 [임시 테이블](/sql/t-sql/statements/alter-table-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), [CREATE TABLE](/sql/t-sql/statements/create-table-azure-sql-data-warehouse?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)및 [CREATE TABLE](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) 문서를 참조 하세요.

## <a name="optimize-clustered-columnstore-tables"></a>클러스터형 Columnstore 테이블 최적화

클러스터형 columnstore 인덱스는 SQL 풀에 데이터를 저장할 수 있는 가장 효율적인 방법 중 하나입니다.  기본적으로 SQL 풀의 테이블은 클러스터형 ColumnStore로 생성 됩니다.  columnstore 테이블에서 쿼리에 대해 최상의 성능을 얻으려면 좋은 세그먼트 품질을 갖는 것이 중요합니다.  메모리 부족 상황에서 행이 columnstore 테이블에 기록되는 경우 columnstore 세그먼트 품질이 저하될 수 있습니다.  

세그먼트 품질은 압축 된 행 그룹의 행 수로 측정 될 수 있습니다. 클러스터형 columnstore 테이블에 대 한 세그먼트 품질을 감지 하 고 개선 하는 단계별 지침은 [테이블 인덱스](../sql-data-warehouse/sql-data-warehouse-tables-index.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) 에서 [columnstore 인덱스 품질 저하의 원인](../sql-data-warehouse/sql-data-warehouse-tables-index.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json#causes-of-poor-columnstore-index-quality) 을 참조 하세요.  

고품질 columnstore 세그먼트가 중요 하기 때문에 데이터를 로드 하기 위해 중간 규모 또는 대규모 리소스 클래스에 있는 사용자 Id를 사용 하는 것이 좋습니다. 낮은 [데이터 웨어하우스 단위](resource-consumption-models.md)를 사용하면 로드하는 사용자에게 더 큰 리소스 클래스를 할당하려는 것입니다.

Columnstore 테이블은 일반적으로 테이블당 100만 개 이상의 행이 있을 때까지 데이터를 압축 된 columnstore 세그먼트로 푸시 하지 않습니다. 각 SQL 풀 테이블은 60 테이블로 분할 됩니다. 따라서 테이블에 6000만 개 이상의 행이 있는 경우를 제외 하 고 columnstore 테이블은 쿼리에 유용 하지 않습니다.  

> [!TIP]
> 6000만 개 미만의 행이 있는 테이블의 경우 columnstore 인덱스를 사용 하는 것은 최적의 솔루션이 아닐 수 있습니다.  

데이터를 분할 하는 경우 클러스터형 columnstore 인덱스의 이점을 얻기 위해 각 파티션에 100만 행이 있어야 합니다.  100 파티션을 포함 하는 테이블의 경우 클러스터형 열 저장소 (60 배포판 *100 파티션* 100만 행)를 활용 하려면 최소 60억 행이 있어야 합니다.  

테이블에 60억 행이 없는 경우 두 가지 기본 옵션이 있습니다. 파티션 수를 줄이거나 힙 테이블을 대신 사용 하는 것이 좋습니다.  또한 columnstore 테이블이 아닌 보조 인덱스와 함께 힙 테이블을 사용 하면 더 나은 성능을 얻을 수 있는지를 시험해 볼 수 있습니다.

columnstore 테이블을 쿼리할 때 필요한 열만 선택하면 쿼리가 더 빨리 실행됩니다.  테이블 및 columnstore 인덱스에 대 한 자세한 내용은 [테이블 인덱스](../sql-data-warehouse/sql-data-warehouse-tables-index.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json), [columnstore 인덱스 가이드](/sql/relational-databases/indexes/columnstore-indexes-overview?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), [columnstore 인덱스 다시 작성](../sql-data-warehouse/sql-data-warehouse-tables-index.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest#rebuilding-indexes-to-improve-segment-quality) 문서에 나와 있습니다.

## <a name="use-larger-resource-class-to-improve-query-performance"></a>더 큰 리소스 클래스를 사용하여 쿼리 성능 향상

SQL 풀은 쿼리에 메모리를 할당 하는 방법으로 리소스 그룹을 사용 합니다. 처음에는 모든 사용자가 배포 당 100 MB의 메모리를 부여 하는 작은 리소스 클래스에 할당 됩니다.  항상 60 배포판이 있습니다. 각 배포에는 최소 100 MB가 제공 됩니다. 총 시스템 전체 메모리 할당은 6000 MB 또는 6gb 미만입니다.  

큰 조인 또는 클러스터형 columnstore 테이블에 로드와 같은 특정 쿼리는 큰 메모리를 할당하는 것이 도움이 됩니다.  순수한 검색과 같은 일부 쿼리에는 아무런 이점도 없습니다. 더 큰 리소스 클래스를 활용 하면 동시성에 영향을 줍니다. 따라서 모든 사용자를 많은 리소스 클래스로 이동 하기 전에 이러한 사실을 염두에 두어야 합니다.

리소스 클래스에 대 한 자세한 내용은 [워크 로드 관리를 위한 리소스 클래스](../sql-data-warehouse/resource-classes-for-workload-management.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) 문서를 참조 하세요.

## <a name="use-smaller-resource-class-to-increase-concurrency"></a>더 작은 리소스 클래스를 사용 하 여 동시성 향상

사용자 쿼리에서 긴 지연이 발생 하는 경우 사용자는 더 큰 리소스 클래스에서 실행 중일 수 있습니다. 이 시나리오는 다른 쿼리를 큐에 대기 시킬 수 있는 동시성 슬롯의 소비를 촉진 합니다.  사용자 쿼리를 큐에 대기 하는지 확인 하려면 `SELECT * FROM sys.dm_pdw_waits` 를 실행 하 여 행이 반환 되는지 확인 합니다.

워크 로드 관리 및 [dm_pdw_waits](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-waits-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) 문서 [에 대 한 리소스 클래스](../sql-data-warehouse/resource-classes-for-workload-management.md) 는 추가 정보를 제공 합니다.

## <a name="use-dmvs-to-monitor-and-optimize-your-queries"></a>DMV를 사용하여 쿼리 모니터링 및 최적화

SQL 풀에는 쿼리 실행을 모니터링 하는 데 사용할 수 있는 여러 Dmv가 있습니다.  아래의 모니터링 문서에서는 실행 중인 쿼리의 세부 정보를 보는 방법에 대 한 단계별 지침을 안내 합니다.  이러한 DMV에서 쿼리를 신속하게 찾으려면 쿼리에 LABEL 옵션을 사용하는 것이 도움이 될 수 있습니다. 자세한 내용은 아래 목록에 포함 된 문서를 참조 하세요.

- [DMV를 사용하여 작업 모니터링](../sql-data-warehouse/sql-data-warehouse-manage-monitor.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)

- [레이블](develop-label.md)
- [OPTION](/sql/t-sql/queries/option-clause-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
- [sys.dm_exec_sessions](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-sessions-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
- [sys.dm_pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
- [sys.dm_pdw_request_steps](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-request-steps-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
- [sys.dm_pdw_sql_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-sql-requests-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
- [sys.dm_pdw_dms_workers](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-dms-workers-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
- [DBCC PDW_SHOWEXECUTIONPLAN](/sql/t-sql/database-console-commands/dbcc-pdw-showexecutionplan-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
- [sys.dm_pdw_waits](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-waits-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)

## <a name="next-steps"></a>다음 단계

또한 일반적인 문제 및 해결 방법에 대해서는 [문제 해결](../sql-data-warehouse/sql-data-warehouse-troubleshoot.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) 문서를 참조 하세요.

이 문서에서 제공 하지 않는 정보가 필요한 경우이 페이지의 왼쪽에 있는 "문서 검색"을 사용 하 여 모든 SQL 풀 문서를 검색 합니다.  [Sql 풀 포럼](https://social.msdn.microsoft.com/Forums/sqlserver/home?forum=AzureSQLDataWarehouse) 은 다른 사용자 및 Sql 풀 제품 그룹에 대 한 질문을 할 수 있는 장소입니다.  

Microsoft는 이 포럼을 적극적으로 모니터링하여 사용자의 질문에 다른 사용자나 당사 직원이 응답하도록 합니다.  Stack Overflow에 대 한 질문을 하는 것을 선호 하는 경우 [AZURE SQL 풀 Stack Overflow 포럼](https://stackoverflow.com/questions/tagged/azure-sqldw)도 있습니다.

기능 요청에 대해서는 [AZURE SQL 풀 사용자 의견](https://feedback.azure.com/forums/307516-sql-data-warehouse) 페이지를 사용 하세요.  요청을 추가 하거나 다른 요청을 추가 하면 대부분의 주문형 기능에 집중 하는 데 도움이 됩니다.
