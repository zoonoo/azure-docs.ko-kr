---
title: Azure Synapse Analytics의 SQL 주문형 (미리 보기)에 대 한 모범 사례
description: SQL 주문형 (미리 보기)에서 작업할 때 알아야 할 권장 사항 및 모범 사례입니다.
services: synapse-analytics
author: filippopovic
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 05/01/2020
ms.author: fipopovi
ms.reviewer: jrasnick
ms.openlocfilehash: 0015beadfea61fc31bf3f37232105b9cfd2ced71
ms.sourcegitcommit: 366e95d58d5311ca4b62e6d0b2b47549e06a0d6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/01/2020
ms.locfileid: "82692155"
---
# <a name="best-practices-for-sql-on-demand-preview-in-azure-synapse-analytics"></a>Azure Synapse Analytics의 SQL 주문형 (미리 보기)에 대 한 모범 사례

이 문서에서는 SQL 주문형 (미리 보기)를 사용 하기 위한 모범 사례 모음을 찾을 수 있습니다. SQL 주문형은 Azure Synapse Analytics 내에서 추가 리소스입니다.

## <a name="general-considerations"></a>일반적인 고려 사항

SQL 주문형 요청을 통해 Azure storage 계정에서 파일을 쿼리할 수 있습니다. 로컬 저장소 또는 수집 기능이 없습니다. 따라서 쿼리가 대상으로 하는 모든 파일은 주문형 SQL 외부에 있습니다. 저장소에서 파일 읽기와 관련 된 모든 작업은 쿼리 성능에 영향을 줄 수 있습니다.

## <a name="colocate-azure-storage-account-and-sql-on-demand"></a>Azure Storage 계정 및 SQL 요청 시 검색

대기 시간을 최소화 하려면 Azure storage 계정 및 SQL 주문형 끝점을 배치 합니다. 작업 영역을 만드는 동안 프로 비전 된 저장소 계정 및 끝점은 동일한 지역에 있습니다.

성능을 최적화 하려면 SQL 요청 시 다른 저장소 계정에 액세스 하는 경우 동일한 지역에 있는지 확인 합니다. 동일한 지역에 있지 않은 경우에는 원격 및 끝점의 지역 간에 데이터 네트워크 전송에 대 한 대기 시간이 증가 합니다.

## <a name="azure-storage-throttling"></a>Azure Storage 제한

여러 응용 프로그램 및 서비스에서 저장소 계정에 액세스할 수 있습니다. 저장소 제한은 응용 프로그램, 서비스 및 SQL 주문형 작업에서 생성 된 결합 된 IOPS 또는 처리량이 저장소 계정 제한을 초과 하는 경우에 발생 합니다. 따라서 쿼리 성능에 부정적인 영향을 미칠 수 있습니다.

제한이 감지 되 면 SQL 주문형은이 시나리오를 기본적으로 처리 합니다. SQL 주문형 요청은 조정이 해결 될 때까지 느린 속도로 저장소에 대 한 요청을 만듭니다.

> [!TIP]
> 최적의 쿼리 실행을 위해 쿼리를 실행 하는 동안 다른 워크 로드를 사용 하 여 저장소 계정을 스트레스 해서는 안 됩니다.

## <a name="prepare-files-for-querying"></a>쿼리할 파일 준비

가능 하면 더 나은 성능을 위해 파일을 준비할 수 있습니다.

- CSV를 Parquet으로 변환-Parquet은 칼럼 형식입니다. 압축 되므로 파일 크기가 동일한 데이터를 사용 하는 CSV 파일 보다 작습니다. 주문형 SQL에는 읽기에 필요한 시간과 저장소 요청이 줄어듭니다.
- 쿼리가 단일 큰 파일을 대상으로 하는 경우 여러 개의 작은 파일로 분할 하는 것이 좋습니다.
- CSV 파일 크기를 10gb 미만으로 유지 하세요.
- 단일 OPENROWSET 경로 또는 외부 테이블 위치에 대해 크기가 동일한 파일을 지정 하는 것이 좋습니다.
- 여러 폴더 또는 파일 이름에 파티션을 저장 하 여 데이터 분할- [파일 이름 및 filepath 함수를 사용 하 여 특정 파티션을 대상으로 지정을](#use-fileinfo-and-filepath-functions-to-target-specific-partitions)선택 합니다.

## <a name="push-wildcards-to-lower-levels-in-path"></a>경로에서 하위 수준으로 와일드 카드를 푸시합니다.

경로에 와일드 카드를 사용 하 여 [여러 파일 및 폴더를 쿼리할](develop-storage-files-overview.md#query-multiple-files-or-folders)수 있습니다. SQL 주문형은 저장소 계정에 있는 파일을 첫 번째 *에서 시작 하 여 저장소 API를 사용 하 여 나열 하 고 지정 된 경로와 일치 하지 않는 파일을 제거 합니다. 처음으로 파일 목록을 줄이면 지정 된 경로와 일치 하는 파일이 여러 개 있을 경우 성능을 향상 시킬 수 있습니다.

## <a name="use-appropriate-data-types"></a>적절 한 데이터 형식 사용

쿼리에 사용 되는 데이터 형식은 성능에 영향을 줍니다. 다음을 수행 하는 경우 더 나은 성능을 얻을 수 있습니다. 

- 가능한 가장 큰 값을 수용 하는 가장 작은 데이터 크기를 사용 합니다.
  - 최대 문자 값 길이가 30 자인 경우 문자 데이터 형식의 길이 30을 사용 합니다.
  - 모든 문자 열 값의 크기가 고정 된 경우 char 또는 nchar를 사용 합니다. 그렇지 않으면 varchar 또는 nvarchar를 사용 합니다.
  - 최대 정수 열 값이 500 이면이 값을 수용할 수 있는 가장 작은 데이터 형식 이므로 smallint를 사용 합니다. 정수 데이터 형식 범위는 [여기](https://docs.microsoft.com/sql/t-sql/data-types/int-bigint-smallint-and-tinyint-transact-sql?view=sql-server-ver15)에서 찾을 수 있습니다.
- 가능 하면 nvarchar 및 nchar 대신 varchar 및 char를 사용 합니다.
- 가능 하면 정수 기반 데이터 형식을 사용 합니다. 정렬, 조인 및 그룹화 작업은 문자 데이터 보다는 정수에서 더 빠르게 수행 됩니다.
- 스키마 유추를 사용 하는 경우 [유추 된 데이터 형식을 확인](#check-inferred-data-types)합니다.

## <a name="check-inferred-data-types"></a>유추 된 데이터 형식 검사

[스키마를 유추](query-parquet-files.md#automatic-schema-inference) 하면 파일 스키마를 몰라도 쿼리를 신속 하 게 작성 하 고 데이터를 탐색할 수 있습니다. 이러한 편안 하 게 인해 유추 된 데이터 형식이 실제로는 보다 큰 비용이 듭니다. 적절 한 데이터 형식이 사용 되는지 확인 하기 위해 소스 파일에 정보가 부족 한 경우에 발생 합니다. 예를 들어 Parquet 파일에는 최대 문자 열 길이에 대 한 메타 데이터가 포함 되어 있지 않으며 SQL 주문형 요청은 varchar (8000)로 유추 됩니다. 

[Sp_describe_first_results_set](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-describe-first-result-set-transact-sql?view=sql-server-ver15)를 사용 하 여 쿼리 결과 데이터 형식을 확인할 수 있습니다.

다음 예제에서는 유추 된 데이터 형식을 최적화 하는 방법을 보여 줍니다. 프로시저는 유추 된 데이터 형식을 표시 하는 데 사용 됩니다. 
```sql  
EXEC sp_describe_first_result_set N'
    SELECT
        vendor_id, pickup_datetime, passenger_count
    FROM 
        OPENROWSET(
            BULK ''https://sqlondemandstorage.blob.core.windows.net/parquet/taxi/*/*/*'',
            FORMAT=''PARQUET''
        ) AS nyc';
```

결과 집합은 다음과 같습니다.

|is_hidden|column_ordinal|이름|system_type_name|max_length|
|----------------|---------------------|----------|--------------------|-------------------||
|0|1|vendor_id|varchar (8000)|8000|
|0|2|pickup_datetime|datetime2(7)|8|
|0|3|passenger_count|int|4|

쿼리에 대해 유추 된 데이터 형식을 알고 있으면 적절 한 데이터 형식을 지정할 수 있습니다.

```sql  
SELECT
    vendor_id, pickup_datetime, passenger_count
FROM 
    OPENROWSET(
        BULK 'https://sqlondemandstorage.blob.core.windows.net/parquet/taxi/*/*/*',
        FORMAT='PARQUET'
    ) 
    WITH (
        vendor_id varchar(4), -- we used length of 4 instead of inferred 8000
        pickup_datetime datetime2,
        passenger_count int
    ) AS nyc;
```

## <a name="use-fileinfo-and-filepath-functions-to-target-specific-partitions"></a>Fileinfo 및 filepath 함수를 사용 하 여 특정 파티션 대상 지정

데이터는 종종 파티션으로 구성 됩니다. 특정 폴더 및 파일을 쿼리하도록 SQL 주문형에 지시할 수 있습니다. 이 함수를 통해 쿼리를 읽고 처리 하는 데 필요한 데이터 양과 파일 수를 줄일 수 있습니다. 추가 된 보너스는 더 나은 성능을 얻는 것입니다.

자세한 내용은 파일 [이름](develop-storage-files-overview.md#filename-function) 및 파일 [경로](develop-storage-files-overview.md#filepath-function) 함수 및 [특정 파일을 쿼리](query-specific-files.md)하는 방법에 대 한 예제를 확인 합니다.

> [!TIP]
> 항상 filepath 및 fileinfo 함수의 결과를 적절 한 데이터 형식으로 캐스팅 합니다. 문자 데이터 형식을 사용 하는 경우 적절 한 길이가 사용 되는지 확인 합니다.

저장 된 데이터가 분할 되지 않은 경우 분할 하는 것이 좋습니다. 이러한 함수를 사용 하 여 해당 파일을 대상으로 하는 쿼리를 최적화할 수 있습니다. 요청 시 SQL에서 [분할 된 Spark 테이블을 쿼리](develop-storage-files-spark-tables.md) 하는 경우 쿼리는 필요한 파일만 자동으로 대상으로 합니다.

## <a name="use-cetas-to-enhance-query-performance-and-joins"></a>CETAS를 사용 하 여 쿼리 성능 및 조인 향상

[CETAS](develop-tables-cetas.md) 는 SQL 주문형에서 사용할 수 있는 가장 중요 한 기능 중 하나입니다. CETAS는 외부 테이블 메타 데이터를 생성 하 고 SELECT 쿼리 결과를 저장소 계정의 파일 집합으로 내보내는 병렬 작업입니다.

CETAS를 사용 하 여 조인 된 참조 테이블과 같이 자주 사용 하는 쿼리 부분을 새 파일 집합에 저장할 수 있습니다. 그런 다음 여러 쿼리에서 공통 조인을 반복 하는 대신이 단일 외부 테이블에 조인할 수 있습니다.

CETAS에서 Parquet 파일을 생성 하면 첫 번째 쿼리가이 외부 테이블을 대상으로 하 여 성능이 향상 될 때 통계가 자동으로 생성 됩니다.

## <a name="next-steps"></a>다음 단계

일반적인 문제 및 해결 방법에 대해서는 [문제 해결](../sql-data-warehouse/sql-data-warehouse-troubleshoot.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) 문서를 참조 하세요. 주문형 SQL이 아닌 SQL 풀을 사용 하는 경우 특정 지침은 [sql 풀에 대 한 모범 사례](best-practices-sql-pool.md) 문서를 참조 하세요.
