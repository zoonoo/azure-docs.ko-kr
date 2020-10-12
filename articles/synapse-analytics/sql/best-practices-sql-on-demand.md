---
title: SQL 주문형(미리 보기)에 대한 모범 사례
description: SQL 주문형(미리 보기)을 사용할 때 알아야 하는 권장 사항 및 모범 사례입니다.
services: synapse-analytics
author: filippopovic
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql
ms.date: 05/01/2020
ms.author: fipopovi
ms.reviewer: jrasnick
ms.openlocfilehash: 7bebfeba6da1493557d51777ba8438747e160750
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "85476277"
---
# <a name="best-practices-for-sql-on-demand-preview-in-azure-synapse-analytics"></a>Azure Synapse Analytics의 SQL 주문형(미리 보기) 모범 사례

이 문서에서는 SQL 주문형(미리 보기) 사용에 대한 모범 사례 컬렉션을 찾을 수 있습니다. SQL 주문형은 Azure Synapse Analytics의 리소스입니다.

## <a name="general-considerations"></a>일반적인 고려 사항

SQL 주문형을 사용하여 Azure 스토리지 계정의 파일을 쿼리할 수 있습니다. SQL 주문형은 로컬 스토리지 또는 수집 기능이 없습니다. 따라서 쿼리의 모든 대상 파일이 SQL 주문형 외부에 있습니다. 스토리지에서 파일 읽기와 관련된 모든 작업이 쿼리 성능에 영향을 줄 수 있습니다.

## <a name="colocate-your-azure-storage-account-and-sql-on-demand"></a>Azure Storage 계정 및 SQL 주문형 공동 배치

대기 시간을 최소화하려면 Azure 스토리지 계정과 SQL 주문형 엔드포인트를 같은 곳에 배치해야 합니다. 작업 영역을 만드는 동안 프로비저닝된 스토리지 계정과 엔드포인트는 같은 지역에 있습니다.

성능을 최적화하려면 SQL 주문형에서 다른 스토리지 계정에 액세스할 때 스토리지 계정과 SQL 주문형이 같은 지역에 있어야 합니다. 서로 다른 지역에 있으면 원격 지역과 엔드포인트의 지역 간에 데이터의 네트워크 전송에 걸리는 대기 시간이 증가합니다.

## <a name="azure-storage-throttling"></a>Azure Storage 제한

여러 애플리케이션과 서비스가 스토리지 계정에 액세스할 수 있습니다. 스토리지 제한은 애플리케이션, 서비스 및 SQL 주문형 워크로드에서 생성한 결합 IOPS 또는 처리량이 스토리지 계정의 제한을 초과할 때 발생합니다. 결과적으로 쿼리 성능에 매우 부정적인 영향을 미치게 됩니다.

SQL 주문형은 제한이 감지되면 이러한 문제를 해결하는 기능을 기본적으로 갖추고 있습니다. SQL 주문형은 제한이 해결될 때까지 느린 속도로 스토리지에 대한 요청을 만듭니다.

> [!TIP]
> 최적의 쿼리 실행을 위해, 쿼리를 실행하는 중에는 스토리지 계정에 다른 워크로드 부하를 추가해서는 안 됩니다.

## <a name="prepare-files-for-querying"></a>쿼리할 파일 준비

가능하다면 성능 향상을 위한 파일을 준비해도 됩니다.

- CSV 및 JSON을 Parquet으로 변환합니다. Parquet은 칼럼 형식입니다. 또한 압축되므로 동일한 데이터를 포함하는 CSV 또는 JSON 파일보다 파일 크기가 작습니다. SQL 주문형이 파일을 읽는 데 필요한 시간과 스토리지 요청이 줄어듭니다.
- 쿼리 대상이 대형 파일 하나인 경우 여러 개의 작은 파일로 분할하는 것이 유리합니다.
- 되도록이면 CSV 파일 크기를 10GB 미만으로 유지하세요.
- 단일 OPENROWSET 경로 또는 외부 테이블 LOCATION에 대해 크기가 동일한 파일을 지정하는 것이 좋습니다.
- 파티션을 다른 폴더 또는 파일 이름에 저장하여 데이터를 분할합니다. [ 및 filepath 함수를 사용하여 특정 파티션을 대상으로 지정](#use-filename-and-filepath-functions-to-target-specific-partitions)을 참조하세요.

## <a name="push-wildcards-to-lower-levels-in-the-path"></a>와일드카드를 푸시하여 경로에서 수준 내리기

경로에 와일드카드를 사용하여 [여러 파일 및 폴더를 쿼리](query-data-storage.md#query-multiple-files-or-folders)할 수 있습니다. SQL 주문형은 스토리지 API를 사용하는 첫 번째 *에서 시작하여 스토리지 계정에서 파일을 나열합니다. 지정된 경로와 일치하지 않는 파일은 제거됩니다. 초기 파일 목록을 줄이면 지정된 경로의 첫 번째 와일드카드까지 일치하는 파일이 여러 개 있을 때 성능을 높일 수 있습니다.

## <a name="use-appropriate-data-types"></a>적절한 데이터 형식 사용

쿼리에서 사용하는 데이터 형식은 성능에 영향을 줍니다. 이러한 지침을 따르면 더 나은 성능을 얻을 수 있습니다. 

- 가능한 가장 큰 값을 수용하는 가장 작은 데이터 크기를 사용합니다.
  - 최대 문자 값 길이가 30자인 경우 길이가 30인 문자 데이터 형식을 사용합니다.
  - 모든 문자 열 값의 크기가 고정된 경우 **char** 또는 **nchar**를 사용합니다. 그렇지 않으면 **varchar** 또는 **nvarchar**를 사용합니다.
  - 최대 정수 열 값이 500이면 이 값을 수용할 수 있는 가장 작은 데이터 형식인 **smallint**를 사용합니다. 정수 데이터 형식 범위는 [이 문서](https://docs.microsoft.com/sql/t-sql/data-types/int-bigint-smallint-and-tinyint-transact-sql?view=sql-server-ver15)에서 찾을 수 있습니다.
- 가능하다면 **nvarchar** 및 **nchar** 대신 **varchar** 및 **char**을 사용합니다.
- 가능하다면 정수 기반 데이터 형식을 사용합니다. SORT, JOIN 및 GROUP BY 작업은 문자 데이터보다 정수에서 더 빠르게 수행됩니다.
- 스키마 유추를 사용하는 경우 [유추한 데이터 형식을 확인](#check-inferred-data-types)합니다.

## <a name="check-inferred-data-types"></a>유추한 데이터 형식 확인

[스키마 유추](query-parquet-files.md#automatic-schema-inference)는 파일 스키마를 알지 못해도 신속하게 쿼리를 작성하고 데이터를 탐색하는 데 도움을 줍니다. 이 편의를 위해 유추된 데이터 형식이 실제 데이터 형식보다 크게 유지됩니다. 이 상황은 적절한 데이터 형식을 사용하기 위해 필요한 소스 파일의 정보가 충분하지 않을 때 발생합니다. 예를 들어 Parquet 파일에는 최대 문자 열 길이에 대한 메타데이터가 없습니다. 따라서 SQL 주문형은 이 길이를 varchar(8000)로 유추합니다.

[sp_describe_first_results_set](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-describe-first-result-set-transact-sql?view=sql-server-ver15)를 사용하여 쿼리의 결과 데이터 형식을 확인할 수 있습니다.

다음 예제에서는 유추한 데이터 형식을 최적화하는 방법을 보여줍니다. 이 프로시저는 유추한 데이터 형식을 보여 주는 데 사용됩니다. 
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

결과 세트:

|is_hidden|column_ordinal|name|system_type_name|max_length|
|----------------|---------------------|----------|--------------------|-------------------||
|0|1|vendor_id|varchar(8000)|8000|
|0|2|pickup_datetime|datetime2(7)|8|
|0|3|passenger_count|int|4|

쿼리에 대해 유추한 데이터 형식을 알고 있으면 다음과 같이 적절한 데이터 형식을 지정할 수 있습니다.

```sql  
SELECT
    vendor_id, pickup_datetime, passenger_count
FROM 
    OPENROWSET(
        BULK 'https://sqlondemandstorage.blob.core.windows.net/parquet/taxi/*/*/*',
        FORMAT='PARQUET'
    ) 
    WITH (
        vendor_id varchar(4), -- we used length of 4 instead of the inferred 8000
        pickup_datetime datetime2,
        passenger_count int
    ) AS nyc;
```

## <a name="use-filename-and-filepath-functions-to-target-specific-partitions"></a>filename 및 filepath 함수를 사용하여 특정 파티션을 대상으로 지정

데이터는 종종 여러 파티션으로 구성됩니다. 특정 폴더 및 파일을 쿼리하도록 SQL 주문형에 지시할 수 있습니다. 이렇게 하면 쿼리에서 읽고 처리해야 하는 파일 수와 데이터 양이 줄어듭니다. 그로 인한 보너스로 성능이 향상됩니다.

자세한 내용은 [filename](query-data-storage.md#filename-function) 및 [filepath](query-data-storage.md#filepath-function) 함수에 대해 읽어보고 [특정 파일 쿼리](query-specific-files.md) 예제를 참조하세요.

> [!TIP]
> 항상 filepath 및 filename 함수의 결과를 적절한 데이터 형식으로 캐스팅하세요. 문자 데이터 형식을 사용하는 경우 적절한 길이를 사용해야 합니다.

> [!NOTE]
> 파티션 제거에 사용되는 filepath 및 filename 함수는 현재 Azure Synapse Analytics용 Apache Spark에서 생성된 테이블마다 자동으로 생성된 외부 테이블을 제외한 나머지 외부 테이블을 지원하지 않습니다.

저장된 데이터가 분할되지 않는 경우 분할하는 것이 좋습니다. 이러한 방식으로 이러한 함수를 사용하여 해당 파일을 대상으로 하는 쿼리를 최적화할 수 있습니다. SQL 주문형에서 [Azure Synapse 테이블에 대한 분할된 Apache Spark를 쿼리](develop-storage-files-spark-tables.md)할 때 쿼리는 자동으로 필요한 파일만 대상으로 지정합니다.

## <a name="use-parser_version-20-to-query-csv-files"></a>PARSER_VERSION 2.0을 사용하여 CSV 파일 쿼리

CSV 파일을 쿼리할 때 성능 최적화 파서를 사용할 수 있습니다. 자세한 내용은 [PARSER_VERSION](develop-openrowset.md)을 참조하세요.

## <a name="use-cetas-to-enhance-query-performance-and-joins"></a>CETAS를 사용하여 쿼리 성능 및 조인 향상

[CETAS](develop-tables-cetas.md)는 SQL 주문형에서 제공하는 가장 중요한 기능 중 하나입니다. CETAS는 외부 테이블 메타데이터를 만들고 SELECT 쿼리 결과를 스토리지 계정의 파일 세트로 내보내는 병렬 작업입니다.

CETAS를 사용하면 조인된 참조 테이블처럼 쿼리에서 자주 사용하는 부분을 새 파일 세트에 저장할 수 있습니다. 그러면 여러 쿼리에서 공통 조인을 반복하는 대신, 이 단일 외부 테이블에 조인할 수 있습니다.

CETAS에서 Parquet 파일을 생성하면 첫 번째 쿼리가 이 외부 테이블을 대상으로 할 때 자동으로 통계가 생성되므로 성능이 향상됩니다.

## <a name="azure-ad-pass-through-performance"></a>Azure AD 통과 성능

SQL 주문형을 사용하면 Azure AD(Azure Active Directory) 통과 또는 SAS 자격 증명을 사용하여 스토리지의 파일에 액세스할 수 있습니다. Azure AD 통과를 사용하면 SAS에 비해 속도가 느릴 수 있습니다.

보다 빠른 성능이 필요하다면 Azure AD 통과 성능이 향상될 때까지 SAS 자격 증명을 사용하여 스토리지에 액세스하세요.

## <a name="next-steps"></a>다음 단계

일반적인 문제에 대한 해결 방법은 [문제 해결](../sql-data-warehouse/sql-data-warehouse-troubleshoot.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) 문서를 검토하세요. SQL 주문형이 아닌 SQL 풀을 사용하는 경우 관련 지침은 [SQL 풀에 대한 모범 사례](best-practices-sql-pool.md)를 참조하세요.
