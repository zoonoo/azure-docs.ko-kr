---
title: SQL 풀에 대한 모범 사례
description: SQL 풀을 사용할 때 알아야 하는 권장 사항 및 모범 사례입니다.
services: synapse-analytics
author: mlee3gsd
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql
ms.date: 04/15/2020
ms.author: martinle
ms.reviewer: igorstan
ms.openlocfilehash: 8483fd2a1b33330b868fb21d71922377e906e6c8
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "85958424"
---
# <a name="best-practices-for-sql-pools-in-azure-synapse-analytics"></a>Azure Synapse Analytics의 SQL 풀에 대한 모범 사례

이 문서에서는 Azure Synapse Analytics에서 SQL 풀의 성능을 최적화하는 모범 사례 모음을 제공합니다. 아래에서는 솔루션을 빌드할 때 중점적으로 살펴볼 기본 지침과 중요 영역을 찾을 수 있습니다. 각 섹션에서는 개념을 소개한 다음, 개념을 보다 심도 있게 다루는 자세한 문서를 안내합니다.

## <a name="sql-pools-loading"></a>SQL 풀 로드

SQL 풀 로드 지침은 [데이터를 로드하기 위한 지침](data-loading-best-practices.md)을 참조하세요.

## <a name="reduce-cost-with-pause-and-scale"></a>일시 중지 및 규모 조정으로 비용 절감

일시 중지 및 스케일링을 통해 비용을 절감하는 방법에 대한 자세한 내용은 [컴퓨팅 관리](../sql-data-warehouse/sql-data-warehouse-manage-compute-overview.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)를 참조하세요.

## <a name="maintain-statistics"></a>통계 유지 관리

열에서 통계를 자동으로 감지하고 만들거나 업데이트하는 SQL Server와 달리, SQL 풀은 통계를 수동으로 유지 관리해야 합니다. SQL 풀 계획이 최적화되도록 통계를 유지 관리해야 합니다.  최적화 프로그램으로 만든 계획은 사용 가능한 통계만큼 훌륭합니다.

> [!TIP]
> 모든 열에 샘플링된 통계를 만드는 것이 통계로 시작하는 쉬운 방법입니다.  

데이터에 중요한 변경 내용이 있는 것 만큼 통계를 업데이트하는 것도 중요합니다.  보수적인 접근 방법은 매일 또는 각 로드 후 통계를 업데이트하는 것일 수 있습니다.  통계를 작성하고 업데이트하는 성능 및 비용 간의 장단점은 항상 있습니다.

통계 유지 관리 시간을 줄이려면 통계를 배치할 열 또는 가장 자주 업데이트할 열을 신중하게 고민해야 합니다. 예를 들어 새 값이 매일 추가될 수 있는 날짜 열을 업데이트해야 합니다. 조인에 포함된 열, WHERE 절에 사용된 열, GROUP BY에서 발견된 열에 대한 통계에 집중해야 합니다.

통계에 대한 추가 정보는 [테이블 통계 관리](develop-tables-statistics.md), [CREATE STATISTICS](/sql/t-sql/statements/create-statistics-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) 및 [UPDATE STATISTICS](/sql/t-sql/statements/update-statistics-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) 문서에서 찾을 수 있습니다.

## <a name="group-insert-statements-into-batches"></a>INSERT 문을 일괄 처리로 그룹화

요구 사항에 따라 `INSERT INTO MyLookup VALUES (1, 'Type 1')`처럼 INSERT 문을 사용하여 작은 테이블에 일회성으로 로드하는 것이 가장 적합한 방법일 수도 있습니다. 그러나 하루 종일 수천 또는 수백만 개의 행을 로드해야 하는 경우에는 싱글톤 INSERTS가 최적의 방법이 아닐 가능성이 높습니다.

이 문제를 해결하는 한 가지 방법은 파일에 데이터를 쓰는 프로세스를 하나 개발한 다음, 이 파일을 주기적으로 로드하는 또 다른 프로세스를 만드는 것입니다. 자세한 내용은 [INSERT](/sql/t-sql/statements/insert-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) 문서를 참조하세요.

## <a name="use-polybase-to-load-and-export-data-quickly"></a>PolyBase를 사용하여 데이터를 신속하고 로드하고 내보내기

SQL 풀은 Azure Data Factory, PolyBase, BCP 등의 여러 도구를 통해 데이터 로드 및 내보내기를 지원합니다.  성능이 중요하지 않은 소량의 데이터에는 어떤 도구를 사용해도 사용자 요구 사항을 충족할 수 있습니다.  

> [!NOTE]
> 대량의 데이터를 로드하거나 내보낼 때 또는 더 빠른 성능이 필요한 경우에는 Polybase가 가장 좋습니다.

PolyBase 로드는 CTAS 또는 INSERT INTO를 사용하여 실행할 수 있습니다. CTAS를 사용하면 트랜잭션 로깅을 최소화하고 데이터를 가장 빠르게 로드할 수 있습니다. Azure Data Factory 역시 PolyBase 로드를 지원하며 CTAS와 비슷한 성능을 얻을 수 있습니다. PolyBase는 Gzip 파일을 포함하여 다양한 파일 형식을 지원합니다.

Gzip 텍스트 파일을 사용할 때 처리량을 최대화하려면 파일을 60개 이상의 파일로 나누어 로드의 병렬 처리를 최대화합니다. 총 처리량을 더 빠르게 하기 위해 데이터를 동시에 로드하는 것이 좋습니다. 이 섹션과 관련된 토픽에 대한 추가 정보는 다음 문서에 포함되어 있습니다.

- [데이터 로드](../sql-data-warehouse/design-elt-data-loading.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
- [PolyBase 사용 지침](data-loading-best-practices.md)
- [Azure SQL 풀 로드 패턴 및 전략](https://blogs.msdn.microsoft.com/sqlcat/20../../azure-sql-data-warehouse-loading-patterns-and-strategies/)
- [Azure Data Factory를 사용하여 데이터 로드](../../data-factory/load-azure-sql-data-warehouse.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
- [Azure Data Factory를 사용하여 데이터 이동](../../data-factory/transform-data-using-machine-learning.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
- [CREATE EXTERNAL FILE FORMAT](/sql/t-sql/statements/create-external-file-format-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
- [Create Table As Select(CTAS)](../sql-data-warehouse/sql-data-warehouse-develop-ctas.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)

## <a name="load-then-query-external-tables"></a>외부 테이블 로드 후 쿼리

Polybase는 쿼리에 가장 적합한 선택이 아닙니다. SQL 풀용 Polybase 테이블은 현재 Azure Blob 파일 및 Azure Data Lake 스토리지만 지원합니다. 이러한 파일에는 지원 컴퓨팅 리소스가 없습니다. 따라서 SQL 풀은 이 작업을 오프로드할 수 없으므로, 데이터를 읽으려면 전체 파일을 tempdb에 로드하여 읽어야 합니다.

이 데이터한 쿼리가 여러 개 있는 경우 이 데이터를 한 번 로드한 후 쿼리에서 로컬 테이블을 사용하는 것이 더 좋습니다. 자세한 Polybase 지침은 [PolyBase 사용 가이드](data-loading-best-practices.md) 문서에 포함되어 있습니다.

## <a name="hash-distribute-large-tables"></a>해시 배포 대형 테이블

기본적으로 테이블은 라운드 로빈 분산됩니다.   이 기본 설정 때문에 사용자는 해당 테이블이 분산되는 방식을 결정하지 않고도 테이블 생성을 간편하게 시작할 수 있습니다. 일부 워크로드는 라운드 로빈 테이블만으로 충분할 수 있습니다. 그러나 대부분은 분산 열이 더 나은 성능을 제공합니다.  

열을 통해 분산된 테이블의 성능이 라운드 로빈 테이블보다 우수한 가장 대표적인 예는 두 개의 대형 팩트 테이블이 조인된 경우입니다.  

예를 들어 order_id를 사용하여 분산되는 주문 테이블과 마찬가지로 order_id를 사용하여 분산되는 트랜잭션 테이블이 있는 상태에서 주문 테이블을 order_id의 트랜잭션 테이블에 조인하면 이 쿼리는 통과 쿼리가 됩니다. 그러면 데이터 이동 작업이 사라집니다. 단계가 적을수록 쿼리는 빨라집니다. 데이터 이동이 적을수록 쿼리는 빨라집니다.

> [!NOTE]
> 분산 테이블을 로드할 때 수신 데이터가 분산 키에 정렬되면 안 됩니다. 그러면 로드 속도가 느려집니다.

아래에 제공된 문서 링크는 분산 열을 선택하여 성능을 개선하는 방법에 대한 추가 정보를 제공합니다. 또한 CREATE TABLE 문의 WITH 절에서 분산 테이블을 정의하는 방법도 찾을 수 있습니다.

- [테이블 개요](develop-tables-overview.md)
- [테이블 분산](../sql-data-warehouse/sql-data-warehouse-tables-distribute.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
- [테이블 배포 선택](https://blogs.msdn.microsoft.com/sqlcat/20../../choosing-hash-distributed-table-vs-round-robin-distributed-table-in-azure-sql-dw-service/)
- [CREATE TABLE](/sql/t-sql/statements/create-table-azure-sql-data-warehouse?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
- [CREATE TABLE AS SELECT](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)

## <a name="do-not-over-partition"></a>과도한 분할 피하기

데이터를 분할하면 파티션 전환 또는 파티션 제거로 스캔 최적화를 통해 데이터를 효율적으로 유지 관리할 수 있지만, 과도하게 분할하면 쿼리 속도가 느려질 수 있습니다.  SQL Server에서 제대로 작동할 수 있는 높은 세분성 분할 전략은 SQL 풀에서는 제대로 작동하지 않을 수 있습니다.  

파티션이 너무 많으면 각 파티션에 100만 개 미만의 행이 포함된 경우 클러스터형 columnstore 인덱스의 효율성이 떨어질 수 있습니다. SQL 풀은 데이터를 자동으로 60개 데이터베이스로 분할합니다. 따라서 100개 파티션을 포함하는 테이블을 만드는 경우 결과적으로 6,000개의 파티션이 생성됩니다. 각 워크로드가 서로 다르므로 가장 좋은 방법은 분할 실험을 통해 사용자의 워크로드에 가장 적합한 방법을 찾는 것입니다.  

SQL Server를 사용하여 구현한 것보다 낮은 세분성을 사용하는 옵션을 고려해 볼 수 있습니다. 예를 들어 매일 분할보다는 매주 또는 매달 분할을 고려해 볼 수 있습니다.

분할에 대한 자세한 내용은 [테이블 분할](../sql-data-warehouse/sql-data-warehouse-tables-partition.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) 문서에 설명되어 있습니다.

## <a name="minimize-transaction-sizes"></a>트랜잭션 크기 최소화

INSERT, UPDATE 및 DELETE 문은 트랜잭션에서 실행됩니다. 이러한 명령문이 실패하면 롤백되어야 합니다. 긴 롤백의 가능성을 줄이려면 되도록이면 트랜잭션 크기를 최소화합니다.  INSERT, UPDATE 및 DELETE 문을 여러 부분으로 나누어 트랙잭션 크기를 최소화할 수 있습니다. 예를 들어 1시간이 소요될 것으로 예상되는 INSERT가 있는 경우 INSERT를 네 부분으로 나눌 수 있습니다. 그러면 각 실행이 15분으로 단축됩니다.  

> [!TIP]
> CTAS, TRUNCATE, DROP TABLE 또는 INSERT와 같은 특수한 최소 로깅 사례를 활용하여 테이블을 비우면 롤백 위험이 감소합니다.  

롤백을 제거하는 다른 방법은 데이터 관리를 위한 파티션 전환과 같은 메타데이터 전용 작업을 사용하는 것입니다.  예를 들어 DELETE 문을 실행하여 order_date가 2001년 10월인 테이블의 모든 행을 삭제하는 대신, 매월 데이터를 분할할 수 있습니다. 그런 다음, 다른 테이블의 빈 파티션에 사용할 데이터가 포함된 파티션으로 전환할 수 있습니다(ALTER TABLE 예제 참조).  

분할되지 않은 테이블의 경우 DELETE를 사용하는 대신, 테이블에 유지할 데이터를 기록하도록 CTAS를 사용하는 것이 좋습니다.  CTAS에 동일한 시간이 소요되는 경우 최소 트랜잭션 로깅을 사용하며 필요할 때 신속하게 취소할 수 있다는 뜻이므로 훨씬 안전하게 실행할 수 있습니다.

이 섹션과 관련된 콘텐츠에 대한 자세한 내용은 아래 문서에 포함되어 있습니다.

- [Create Table As Select(CTAS)](../sql-data-warehouse/sql-data-warehouse-develop-ctas.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
- [트랜잭션 이해](develop-transactions.md)
- [트랜잭션 최적화](../sql-data-warehouse/sql-data-warehouse-develop-best-practices-transactions.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
- [테이블 분할](../sql-data-warehouse/sql-data-warehouse-tables-partition.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
- [TRUNCATE TABLE](/sql/t-sql/statements/truncate-table-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
- [ALTER TABLE](/sql/t-sql/statements/alter-table-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)

## <a name="reduce-query-result-sizes"></a>쿼리 결과 크기 축소

쿼리 결과 크기를 축소하면 대형 쿼리 결과로 인해 발생하는 클라이언트 쪽 문제를 방지할 수 있습니다.  쿼리를 편집하여 반환되는 행 수를 줄일 수 있습니다. 일부 쿼리 생성 도구를 사용하면 각 쿼리에 "top N" 구문을 추가할 수 있습니다.  또한 쿼리 결과를 임시 테이블로 CETAS한 다음, PolyBase 내보내기를 사용하여 하위 수준 처리를 수행할 수 있습니다.

## <a name="use-the-smallest-possible-column-size"></a>가능한 가장 작은 열 크기 사용

DDL을 정의할 때, 이 과정에서 데이터를 지원하는 가장 작은 데이터 형식을 사용하면 쿼리 성능이 향상됩니다.  CHAR 및 VARCHAR 열에 특히 권장하는 방법입니다.  열에서 가장 긴 값이 25자인 경우 열을 VARCHAR(25)로 정의합니다.  모든 문자 열을 큰 기본 길이로 정의하지 마세요.  또한 꼭 필요한 경우에는 NVARCHAR을 사용하지 말고 열을 VARCHAR로 정의하세요.

위의 정보와 관련된 중요한 개념에 대한 자세한 내용은 [테이블 개요](develop-tables-overview.md), [테이블 데이터 형식](develop-tables-data-types.md) 및 [CREATE TABLE](/sql/t-sql/statements/create-table-azure-sql-data-warehouse?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) 문서를 참조하세요.

## <a name="use-temporary-heap-tables-for-transient-data"></a>임시 데이터에 대해 임시 힙 테이블 사용

SQL 풀에서 일시적으로 데이터를 가져올 때 힙 테이블을 사용하면 일반적으로 전체 프로세스가 더 빨라집니다.  추가 변환을 실행하기 전의 준비 과정으로만 데이터를 로드하는 경우에는 힙 테이블에 테이블을 로드하는 것이 클러스터형 columnstore 테이블에 데이터를 로드하는 것보다 빠릅니다.  

데이터를 임시 테이블에 로드하면 테이블을 영구 스토리지에 로드할 때보다 훨씬 빠르게 로드됩니다.  임시 테이블은 "#"으로 시작되고 해당 테이블을 만든 세션에서만 액세스할 수 있으므로 제한된 시나리오에서만 작동할 수 있습니다. 힙 테이블은 CREATE TABLE의 WITH 절에 정의됩니다.  임시 테이블을 사용하는 경우 해당 임시 테이블에서도 통계를 작성해야 합니다.

추가 지침은 [임시 테이블](/sql/t-sql/statements/alter-table-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), [CREATE TABLE](/sql/t-sql/statements/create-table-azure-sql-data-warehouse?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) 및 [CREATE TABLE AS SELECT](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) 문서를 참조하세요.

## <a name="optimize-clustered-columnstore-tables"></a>클러스터형 Columnstore 테이블 최적화

columnstore 클러스터형 인덱스는 SQL 풀에 데이터를 저장할 수 있는 가장 효율적인 방법 중 하나입니다.  기본적으로 SQL 풀의 테이블은 클러스터형 ColumnStore로 만들어집니다.  columnstore 테이블에서 쿼리에 대해 최상의 성능을 얻으려면 좋은 세그먼트 품질을 갖는 것이 중요합니다.  메모리 부족 상황에서 행이 columnstore 테이블에 기록되는 경우 columnstore 세그먼트 품질이 저하될 수 있습니다.  

세그먼트 품질은 압축된 행 그룹에 있는 행의 수로 측정할 수 있습니다. 클러스터형 columnstore 테이블에 대해 세그먼트 품질을 감지하고 개선하는 단계별 지침은 [테이블 인덱스](../sql-data-warehouse/sql-data-warehouse-tables-index.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) 문서의 [Columnstore 인덱스 품질 저하 원인](../sql-data-warehouse/sql-data-warehouse-tables-index.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json#causes-of-poor-columnstore-index-quality)을 참조하세요.  

고품질 columnstore 세그먼트가 중요하므로 데이터를 로드하기 위해서는 중간 규모 또는 대규모 리소스 클래스에 속하는 사용자 ID를 사용하는 것이 좋습니다. 낮은 [데이터 웨어하우스 단위](resource-consumption-models.md)를 사용하면 로드하는 사용자에게 더 큰 리소스 클래스를 할당하려는 것입니다.

Columnstore 테이블은 일반적으로 테이블당 행 수가 100만 개를 초과하기 전에는 데이터를 압축된 columnstore 세그먼트로 푸시하지 않습니다. 각 SQL 풀 테이블은 60개 테이블로 분할됩니다. 따라서 테이블의 행 수가 6천만 개 미만이면 columnstore 테이블을 사용해도 도움이 되지 않습니다.  

> [!TIP]
> 행 수가 6천만 개 미만인 테이블에는 columstore 인덱스가 최적의 솔루션이 아닐 수 있습니다.  

데이터를 분할할 때, 클러스터형 columnstore 인덱스의 이점을 얻으려면 각 파티션의 행 수가 1백만 개를 넘어야 합니다.  파티션 수가 100개인 테이블에 클러스터형 columnstore를 사용하여 이득을 보려면 행 수가 60억 개 이상이어야 합니다(60개 배포판 *100개 파티션* 1백만 개 행).  

테이블의 행 수가 60억 개 미만일 때 선택할 수 있는 두 가지 주요 옵션이 있습니다. 하나는 파티션 수를 줄이는 것이고, 다른 하나는 힙 테이블을 사용하는 것입니다.  columnstore 테이블 대신 보조 인덱스가 있는 힙 테이블을 사용하면 성능이 향상되는지 실험해 보는 것도 좋습니다.

columnstore 테이블을 쿼리할 때 필요한 열만 선택하면 쿼리가 더 빨리 실행됩니다.  테이블 및 columnstore 인덱스에 대한 자세한 내용은 [테이블 인덱스](../sql-data-warehouse/sql-data-warehouse-tables-index.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json), [Columnstore 인덱스 가이드](/sql/relational-databases/indexes/columnstore-indexes-overview?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) 및 [columnstore 인덱스 다시 빌드](../sql-data-warehouse/sql-data-warehouse-tables-index.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest#rebuilding-indexes-to-improve-segment-quality) 문서에서 찾을 수 있습니다.

## <a name="use-larger-resource-class-to-improve-query-performance"></a>더 큰 리소스 클래스를 사용하여 쿼리 성능 향상

SQL 풀은 메모리를 쿼리에 할당하는 방법으로 리소스 그룹을 사용합니다. 처음에는 모든 사용자가 배포당 100MB의 메모리를 부여하는 소형 리소스 클래스에 할당됩니다.  항상 60개 배포판이 있습니다. 각 배포판에는 최소 100MB가 제공됩니다. 총 시스템 전체 메모리 할당은 6,000MB 또는 6GB 미만입니다.  

큰 조인 또는 클러스터형 columnstore 테이블에 로드와 같은 특정 쿼리는 큰 메모리를 할당하는 것이 도움이 됩니다.  순수 검색과 같은 일부 쿼리는 아무런 이점이 없습니다. 더 큰 리소스 클래스를 사용하면 동시성에 영향을 줍니다. 모든 사용자를 큰 리소스 클래스로 이동하기 전에 이 점을 고려해야 합니다.

리소스 클래스에 대한 추가 정보는 [워크로드 관리를 위한 리소스 클래스](../sql-data-warehouse/resource-classes-for-workload-management.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) 문서를 참조하세요.

## <a name="use-smaller-resource-class-to-increase-concurrency"></a>더 작은 리소스 클래스를 사용하여 동시성 향상

사용자 쿼리에서 긴 지연이 발생하는 경우 사용자가 더 큰 리소스 클래스에서 실행 중인 것일 수 있습니다. 이 경우 동시성 슬롯이 사용될 가능성이 높으며, 이로 인해 다른 쿼리가 큐에 추가될 수 있습니다.  사용자 쿼리가 큐에 추가되었는지 확인하려면 `SELECT * FROM sys.dm_pdw_waits`를 실행하여 모든 행이 반환되었는지 확인합니다.

[워크로드 관리를 위한 리소스 클래스](../sql-data-warehouse/resource-classes-for-workload-management.md) 및 [sys.dm_pdw_waits](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-waits-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) 문서에 자세한 내용이 설명되어 있습니다.

## <a name="use-dmvs-to-monitor-and-optimize-your-queries"></a>DMV를 사용하여 쿼리 모니터링 및 최적화

SQL 풀에는 쿼리 실행을 모니터링하는 데 사용할 수 있는 여러 DMV가 있습니다.  아래의 모니터링 문서에서는 쿼리 실행 세부 정보를 살펴보는 방법에 대한 단계별 지침을 제공합니다.  이러한 DMV에서 쿼리를 신속하게 찾으려면 쿼리에 LABEL 옵션을 사용하는 것이 도움이 될 수 있습니다. 자세한 내용은 아래 목록의 문서를 참조하세요.

- [DMV를 사용하여 작업 모니터링](../sql-data-warehouse/sql-data-warehouse-manage-monitor.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)

- [LABEL](develop-label.md)
- [OPTION](/sql/t-sql/queries/option-clause-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
- [sys.dm_exec_sessions](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-sessions-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
- [sys.dm_pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
- [sys.dm_pdw_request_steps](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-request-steps-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
- [sys.dm_pdw_sql_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-sql-requests-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
- [sys.dm_pdw_dms_workers](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-dms-workers-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
- [DBCC PDW_SHOWEXECUTIONPLAN](/sql/t-sql/database-console-commands/dbcc-pdw-showexecutionplan-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
- [sys.dm_pdw_waits](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-waits-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)

## <a name="next-steps"></a>다음 단계

[문제 해결](../sql-data-warehouse/sql-data-warehouse-troubleshoot.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) 문서에서 일반적인 문제 및 해결 방법을 확인하세요.

이 문서에서 제공하지 않는 정보가 필요한 경우 [Azure Synapse에 대한 Microsoft Q&A 페이지](https://docs.microsoft.com/answers/topics/azure-synapse-analytics.html)에서 다른 사용자와 SQL 풀 제품 그룹에게 궁금한 내용을 질문할 수 있습니다.  

Microsoft는 이 포럼을 적극적으로 모니터링하여 사용자의 질문에 다른 사용자나 당사 직원이 응답하도록 합니다.  Stack Overflow에서 질문하기를 원하는 경우 [Azure SQL 풀 Stack Overflow 포럼](https://stackoverflow.com/questions/tagged/azure-sqldw)을 사용하면 됩니다.

기능 요청은 [Azure SQL 풀 피드백](https://feedback.azure.com/forums/307516-sql-data-warehouse) 페이지를 사용하세요.  요청을 추가하거나 다른 요청을 추천해 주시면 저희가 가장 수요가 많은 기능에 집중하는 데 도움이 됩니다.
