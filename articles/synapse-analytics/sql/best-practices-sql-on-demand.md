---
title: Azure Synapse 분석에서 주문형 SQL(미리 보기)에 대한 모범 사례
description: SQL 온디맨드(미리 보기)로 작업할 때 알아야 할 권장 사항 및 모범 사례입니다.
services: synapse-analytics
author: mlee3gsd
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/15/2020
ms.author: martinle
ms.reviewer: igorstan
ms.openlocfilehash: 1d4203141973c10fe7673f6ab9dedbc3bfdc8999
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81429071"
---
# <a name="best-practices-for-sql-on-demand-preview-in-azure-synapse-analytics"></a>Azure Synapse 분석에서 주문형 SQL(미리 보기)에 대한 모범 사례

이 문서에서는 SQL 주문형(미리 보기) 사용에 대한 모범 사례 모음을 찾을 수 있습니다. 주문형 SQL은 Azure Synapse 분석 내의 추가 리소스입니다.

## <a name="general-considerations"></a>일반적인 고려 사항

SQL 온디맨드를 사용하면 Azure 저장소 계정의 파일을 쿼리할 수 있습니다. 로컬 저장소 또는 저장 기능이 없습니다. 따라서 쿼리가 대상으로 하는 모든 파일은 주문형 SQL 외부에 있습니다. 저장소에서 파일을 읽는 것과 관련된 모든 것이 쿼리 성능에 영향을 미칠 수 있습니다.

## <a name="colocate-azure-storage-account-and-sql-on-demand"></a>Azure 저장소 계정 및 SQL 온디맨드 할당

대기 시간을 최소화하려면 Azure 저장소 계정과 SQL 온디맨드 엔드포인트를 공동 배치합니다. 작업 영역을 만드는 동안 프로비전된 저장소 계정 및 끝점은 동일한 지역에 있습니다.

최적의 성능을 위해 SQL 온디맨드를 통해 다른 저장소 계정에 액세스하는 경우 동일한 지역에 있는지 확인합니다. 동일한 지역에 없는 경우 원격 및 끝점 의 리전 간에 데이터의 네트워크 전송에 대한 대기 시간이 증가합니다.

## <a name="azure-storage-throttling"></a>Azure 저장소 제한

여러 응용 프로그램 및 서비스가 저장소 계정에 액세스할 수 있습니다. 저장소 제한은 응용 프로그램, 서비스 및 SQL 온디맨드 워크로드에서 생성된 결합된 IOPS 또는 처리량이 저장소 계정의 제한을 초과할 때 발생합니다. 따라서 쿼리 성능에 상당한 부정적인 영향을 미칩니다.

제한이 검색되면 주문형 SQL이 이 시나리오를 기본 제공처리합니다. 주문형 SQL은 제한이 해결될 때까지 더 느린 속도로 저장소에 대한 요청을 만듭니다.

> [!TIP]
> 최적의 쿼리 실행을 위해 쿼리 실행 중에 저장소 계정에 다른 워크로드에 스트레스를 주어서는 안 됩니다.

## <a name="prepare-files-for-querying"></a>쿼리할 파일 준비

가능하면 더 나은 성능을 위해 파일을 준비할 수 있습니다.

- 마루에 CSV를 변환 - 마루는 열 형식입니다. 압축되기 때문에 파일 크기는 동일한 데이터를 가진 CSV 파일보다 작습니다. SQL 온디맨드 스토리지는 주문형 SQL을 읽는 데 필요한 시간과 저장소 요청을 줄입니다.
- 쿼리가 하나의 대용량 파일을 대상으로 하는 경우 여러 개의 작은 파일로 분할하면 이점이 있습니다.
- CSV 파일 크기를 10GB 미만으로 유지해 보십시오.
- 단일 OPENROWSET 경로 또는 외부 테이블 위치에 대해 크기가 동등한 파일을 두는 것이 좋습니다.
- 다른 폴더 또는 파일 이름에 파티션을 저장하여 데이터를 분할 - [특정 파티션을 대상으로 파일 이름 및 파일 경로 함수를 사용하여 확인합니다.](#use-fileinfo-and-filepath-functions-to-target-specific-partitions)

## <a name="use-fileinfo-and-filepath-functions-to-target-specific-partitions"></a>fileinfo 및 filepath 함수를 사용하여 특정 파티션을 타겟팅합니다.

데이터는 종종 파티션으로 구성됩니다. SQL에 요청 시 특정 폴더 및 파일을 쿼리하도록 지시할 수 있습니다. 이 함수는 쿼리가 읽고 처리하는 데 필요한 파일 수와 데이터의 양을 줄입니다. 추가 보너스는 더 나은 성능을 얻을 수 있다는 것입니다.

자세한 내용은 [파일 이름](develop-storage-files-overview.md#filename-function) 및 [파일 경로](develop-storage-files-overview.md#filepath-function) 함수 및 [특정 파일을 쿼리하는](query-specific-files.md)방법에 대한 예제를 확인합니다.

저장된 데이터가 분할되지 않은 경우 이러한 함수를 사용하여 해당 파일을 대상으로 하는 쿼리를 최적화할 수 있도록 분할하는 것이 좋습니다. 주문형 SQL에서 [분할된 Spark 테이블을 쿼리할](develop-storage-files-spark-tables.md) 때 쿼리는 필요한 파일만 자동으로 대상으로 지정합니다.

## <a name="use-cetas-to-enhance-query-performance-and-joins"></a>CETAS를 사용하여 쿼리 성능 및 조인

[CETAS는](develop-tables-cetas.md) 주문형 SQL에서 사용할 수 있는 가장 중요한 기능 중 하나입니다. CETAS는 외부 테이블 메타데이터를 만들고 SELECT 쿼리 결과를 저장소 계정의 파일 집합으로 내보내는 병렬 작업입니다.

CETAS를 사용하여 조인된 참조 테이블과 같이 자주 사용되는 쿼리 부분을 새 파일 집합에 저장할 수 있습니다. 그런 다음 여러 쿼리에서 일반적인 조인을 반복하는 대신 이 단일 외부 테이블에 조인할 수 있습니다.

CETAS가 마루 파일을 생성하면 첫 번째 쿼리가 이 외부 테이블을 대상으로 할 때 통계가 자동으로 생성되어 성능이 향상됩니다.

## <a name="next-steps"></a>다음 단계

일반적인 문제 및 해결 방법은 [문제 해결](../sql-data-warehouse/sql-data-warehouse-troubleshoot.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) 문서를 검토합니다. SQL 온디맨드 대신 SQL 풀로 작업하는 경우 SQL [풀에 대한 모범 사례](best-practices-sql-pool.md) 문서를 참조하여 구체적인 지침을 참조하세요.
