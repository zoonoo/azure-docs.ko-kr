---
title: 서버를 사용 하지 않는 SQL 풀을 사용 하 여 Parquet 파일 쿼리 (미리 보기)
description: 이 문서에서는 서버를 사용 하지 않는 SQL 풀 (미리 보기)을 사용 하 여 Parquet 파일을 쿼리 하는 방법에 대해 알아봅니다.
services: synapse analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: sql
ms.date: 05/20/2020
ms.author: v-stazar
ms.reviewer: jrasnick
ms.openlocfilehash: c8e454f523f081681d359dfd852c5f88d69a99d7
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/17/2020
ms.locfileid: "94661055"
---
# <a name="query-parquet-files-using-serverless-sql-pool-preview-in-azure-synapse-analytics"></a>Azure Synapse Analytics에서 서버를 사용 하지 않는 SQL 풀 (미리 보기)을 사용 하 여 Parquet 파일 쿼리

이 문서에서는 Parquet 파일을 읽는 서버 리스 SQL 풀 (미리 보기)를 사용 하 여 쿼리를 작성 하는 방법을 알아봅니다.

## <a name="quickstart-example"></a>빠른 시작 예제

`OPENROWSET` 함수를 사용 하면 파일에 대 한 URL을 제공 하 여 parquet 파일의 내용을 읽을 수 있습니다.

### <a name="read-parquet-file"></a>Parquet 파일 읽기

파일의 콘텐츠를 확인 하는 가장 쉬운 방법은 `PARQUET` 함수에 파일 URL을 제공 하 고 parquet를 지정 하는 것입니다 `OPENROWSET` `FORMAT` . 파일이 공개적으로 사용 가능한 경우 또는 Azure AD id가이 파일에 액세스할 수 있는 경우 다음 예제와 같이 쿼리를 사용 하 여 파일의 내용을 볼 수 있어야 합니다.

```sql
select top 10 *
from openrowset(
    bulk 'https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/ecdc_cases/latest/ecdc_cases.parquet',
    format = 'parquet') as rows
```

이 파일에 액세스할 수 있는지 확인 합니다. 파일이 SAS 키 또는 사용자 지정 Azure id를 사용 하 여 보호 되는 경우 [sql 로그인에 대 한 서버 수준 자격 증명](develop-storage-files-storage-access-control.md?tabs=shared-access-signature#server-scoped-credential)을 설정 해야 합니다.

> [!IMPORTANT]
> `Latin1_General_100_CI_AS_SC_UTF8`PARQUET 파일의 문자열 값이 utf-8 인코딩을 사용 하 여 인코딩 되므로 utf-8 데이터베이스 데이터 정렬 (예:)을 사용 하 고 있는지 확인 합니다.
> PARQUET 파일 및 데이터 정렬에서 텍스트 인코딩이 일치 하지 않으면 예기치 않은 변환 오류가 발생할 수 있습니다.
> 다음 T-sql 문을 사용 하 여 현재 데이터베이스의 기본 데이터 정렬을 쉽게 변경할 수 있습니다. `alter database current collate Latin1_General_100_CI_AI_SC_UTF8`

### <a name="data-source-usage"></a>데이터 원본 사용

이전 예에서는 파일에 대 한 전체 경로를 사용 합니다. 또는 저장소의 루트 폴더를 가리키는 위치를 사용 하 여 외부 데이터 원본을 만들고 해당 데이터 원본 및 함수에 있는 파일에 대 한 상대 경로를 사용할 수 있습니다 `OPENROWSET` .

```sql
create external data source covid
with ( location = 'https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/ecdc_cases' );
go
select top 10 *
from openrowset(
        bulk 'latest/ecdc_cases.parquet',
        data_source = 'covid',
        format = 'parquet'
    ) as rows
```

데이터 원본이 SAS 키 또는 사용자 지정 id를 사용 하 여 보호 되는 경우 [데이터베이스 범위 자격 증명을 사용 하 여 데이터 원본을](develop-storage-files-storage-access-control.md?tabs=shared-access-signature#database-scoped-credential)구성할 수 있습니다.

### <a name="explicitly-specify-schema"></a>명시적으로 스키마 지정

`OPENROWSET` 에서는 절을 사용 하 여 파일을 읽을 열을 명시적으로 지정할 수 있습니다 `WITH` .

```sql
select top 10 *
from openrowset(
        bulk 'latest/ecdc_cases.parquet',
        data_source = 'covid',
        format = 'parquet'
    ) with ( date_rep date, cases int, geo_id varchar(6) ) as rows
```

> [!IMPORTANT]
> 절의 모든 문자열 열에 대해 일부 UTF-8 데이터 정렬 (예:)을 지정 `Latin1_General_100_CI_AS_SC_UTF8` `WITH` 하거나 데이터베이스 수준에서 utf-8 데이터 정렬을 설정 하는 것이 explicilty 합니다.
> 파일의 텍스트 인코딩과 문자열 열 데이터 정렬의 불일치 때문에 예기치 않은 변환 오류가 발생할 수 있습니다.
> 다음 T-sql 문을 사용 하 여 현재 데이터베이스의 기본 데이터 정렬을 쉽게 변경할 수 있습니다. `alter database current collate Latin1_General_100_CI_AI_SC_UTF8`
> 다음 정의를 사용 하 여 열 형식에 대 한 데이터 정렬을 쉽게 설정할 수 있습니다. `geo_id varchar(6) collate Latin1_General_100_CI_AI_SC_UTF8`

다음 섹션에서는 다양 한 유형의 PARQUET 파일을 쿼리 하는 방법을 볼 수 있습니다.

## <a name="prerequisites"></a>사전 요구 사항

첫 번째 단계는 [NYC Yellow Taxi](https://azure.microsoft.com/services/open-datasets/catalog/nyc-taxi-limousine-commission-yellow-taxi-trip-records/) 스토리지 계정을 참조하는 데이터 원본을 통해 **데이터베이스를 만드는** 것입니다. 그런 다음, 해당 데이터베이스에서 [설치 스크립트](https://github.com/Azure-Samples/Synapse/blob/master/SQL/Samples/LdwSample/SampleDB.sql)를 실행하여 개체를 초기화합니다. 이 설치 스크립트는 이러한 샘플에서 사용되는 데이터 원본, 데이터베이스 범위 자격 증명 및 외부 파일 형식을 만듭니다.

## <a name="dataset"></a>데이터 세트

이 샘플에는 [NYC Yellow Taxi](https://azure.microsoft.com/services/open-datasets/catalog/nyc-taxi-limousine-commission-yellow-taxi-trip-records/) 데이터 세트가 사용됩니다. [CSV 파일을 읽는](query-parquet-files.md) 것과 동일한 방법으로 Parquet 파일을 쿼리할 수 있습니다. 유일한 차이점은 `FILEFORMAT` 매개 변수를 `PARQUET`로 설정해야 한다는 점입니다. 이 문서의 예제에서는 Parquet 파일 읽기의 세부 정보를 보여줍니다.

## <a name="query-set-of-parquet-files"></a>Parquet 파일의 쿼리 집합

Parquet 파일을 쿼리할 때 관심 있는 열만 지정할 수 있습니다.

```sql
SELECT
        YEAR(tpepPickupDateTime),
        passengerCount,
        COUNT(*) AS cnt
FROM  
    OPENROWSET(
        BULK 'puYear=2018/puMonth=*/*.snappy.parquet',
        DATA_SOURCE = 'YellowTaxi',
        FORMAT='PARQUET'
    ) WITH (
        tpepPickupDateTime DATETIME2,
        passengerCount INT
    ) AS nyc
GROUP BY
    passengerCount,
    YEAR(tpepPickupDateTime)
ORDER BY
    YEAR(tpepPickupDateTime),
    passengerCount;
```

## <a name="automatic-schema-inference"></a>자동 스키마 유추

Parquet 파일을 읽을 때 OPENROWSET WITH 절을 사용할 필요가 없습니다. 열 이름 및 데이터 형식은 Parquet 파일에서 자동으로 읽습니다.

아래 샘플에서는 Parquet 파일에 대한 자동 스키마 유추 기능을 보여줍니다. 스키마를 지정하지 않고 2017년 9월의 행 수를 반환합니다.

> [!NOTE]
> Parquet 파일을 읽을 때 OPENROWSET WITH 절에 열을 지정할 필요가 없습니다. 이 경우 서버를 사용 하지 않는 SQL 풀 쿼리 서비스는 Parquet 파일에서 메타 데이터를 활용 하 고 이름으로 열을 바인딩합니다.

```sql
SELECT TOP 10 *
FROM  
    OPENROWSET(
        BULK 'puYear=2018/puMonth=*/*.snappy.parquet',
        DATA_SOURCE = 'YellowTaxi',
        FORMAT='PARQUET'
    ) AS nyc
```

### <a name="query-partitioned-data"></a>분할된 데이터 쿼리

이 샘플에 제공된 데이터 세트는 별도의 하위 폴더로 분할(파티션)됩니다. filepath 함수를 사용하여 특정 파티션을 대상으로 지정할 수 있습니다. 이 예제에서는 2017년 첫 3개월 동안의 년, 월 및 payment_type별 요금을 보여줍니다.

> [!NOTE]
> 서버를 사용 하지 않는 SQL 풀 쿼리는 Hive/Hadoop 파티션 구성표와 호환 됩니다.

```sql
SELECT
        YEAR(tpepPickupDateTime),
        passengerCount,
        COUNT(*) AS cnt
FROM  
    OPENROWSET(
        BULK 'puYear=*/puMonth=*/*.snappy.parquet',
        DATA_SOURCE = 'YellowTaxi',
        FORMAT='PARQUET'
    ) nyc
WHERE
    nyc.filepath(1) = 2017
    AND nyc.filepath(2) IN (1, 2, 3)
    AND tpepPickupDateTime BETWEEN CAST('1/1/2017' AS datetime) AND CAST('3/31/2017' AS datetime)
GROUP BY
    passengerCount,
    YEAR(tpepPickupDateTime)
ORDER BY
    YEAR(tpepPickupDateTime),
    passengerCount;
```

## <a name="type-mapping"></a>형식 매핑

Parquet type을 SQL native type으로 매핑하면 [Parquet에 대 한 형식 매핑을](develop-openrowset.md#type-mapping-for-parquet)확인 합니다.

## <a name="next-steps"></a>다음 단계

다음 문서로 진행하여 [Parquet 중첩 형식을 쿼리](query-parquet-nested-types.md)하는 방법을 알아봅니다.
