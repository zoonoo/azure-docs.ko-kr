---
title: 쿼리에서 파일 메타 데이터 사용
description: OPENROWSET 함수는 파일 이름 및/또는 폴더 경로를 기반으로 데이터를 필터링 하거나 분석 하기 위해 쿼리에서 사용 되는 모든 파일에 대 한 파일 및 경로 정보를 제공 합니다.
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: ''
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: 40a8e2c153ec3d8e7b4007340b9433a38f9ccc89
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81431554"
---
# <a name="using-file-metadata-in-queries"></a>쿼리에서 파일 메타 데이터 사용

SQL 주문형 쿼리 서비스는 [쿼리 폴더 및 여러 파일](query-folders-multiple-csv-files.md) 문서에 설명 된 대로 여러 파일 및 폴더의 주소를 지정할 수 있습니다. 이 문서에서는 쿼리에서 파일 및 폴더 이름에 대 한 메타 데이터 정보를 사용 하는 방법에 대해 알아봅니다.

경우에 따라 결과 집합의 특정 행과 상관 관계가 있는 파일 또는 폴더 원본을 알아야 할 수 있습니다.

함수 `filepath` 및 `filename` 를 사용 하 여 결과 집합에서 파일 이름 및/또는 경로를 반환할 수 있습니다. 또는 파일 이름 및/또는 폴더 경로를 기반으로 데이터를 필터링 하는 데 사용할 수 있습니다. 이러한 함수는 구문 섹션 [filename 함수](develop-storage-files-overview.md#filename-function) 및 [filepath 함수](develop-storage-files-overview.md#filepath-function)에 설명 되어 있습니다. 아래에서 샘플을 따라 간단한 설명을 찾을 수 있습니다.

## <a name="prerequisites"></a>전제 조건

이 문서의 나머지 부분을 읽기 전에 다음 필수 구성 요소를 검토 합니다.

- [처음 설정](query-data-storage.md#first-time-setup)
- [필수 구성 요소](query-data-storage.md#prerequisites)

## <a name="functions"></a>Functions

### <a name="filename"></a>파일 이름

이 함수는 행이 생성 된 파일 이름을 반환 합니다.

다음 샘플에서는 지난 3 달 동안 2017 NYC 노란색 Taxi 데이터 파일을 읽고 파일당 탑승 수를 반환 합니다. 쿼리의 OPENROWSET 부분은 읽을 파일을 지정 합니다.

```sql
SELECT
    r.filename() AS [filename]
    ,COUNT_BIG(*) AS [rows]
FROM OPENROWSET(
        BULK 'https://sqlondemandstorage.blob.core.windows.net/parquet/taxi/year=2017/month=9/*.parquet',
        FORMAT='PARQUET') AS [r]
GROUP BY
    r.filename()
ORDER BY
    [filename];
```

다음 예에서는 WHERE 절에서 *filename ()* 을 사용 하 여 읽을 파일을 필터링 하는 방법을 보여 줍니다. 쿼리의 OPENROWSET 부분에 있는 전체 폴더에 액세스 하 고 WHERE 절의 파일을 필터링 합니다.

결과는 이전 예제와 동일 합니다.

```sql
SELECT
    r.filename() AS [filename]
    ,COUNT_BIG(*) AS [rows]
FROM OPENROWSET(
    BULK 'https://sqlondemandstorage.blob.core.windows.net/parquet/taxi/year=2017/month=9/*.parquet',
    FORMAT='PARQUET') AS [r]
WHERE
    r.filename() IN ('yellow_tripdata_2017-10.parquet', 'yellow_tripdata_2017-11.parquet', 'yellow_tripdata_2017-12.parquet')
GROUP BY
    r.filename()
ORDER BY
    [filename];
```

### <a name="filepath"></a>Null

Filepath 함수는 전체 또는 부분 경로를 반환 합니다.

- 매개 변수 없이 호출 되는 경우 행이 생성 된 전체 파일 경로를 반환 합니다.
- 매개 변수를 사용 하 여 호출 하는 경우 매개 변수에 지정 된 위치에서 와일드 카드와 일치 하는 경로의 일부를 반환 합니다. 예를 들어 매개 변수 값 1은 첫 번째 와일드 카드와 일치 하는 경로의 일부를 반환 합니다.

다음 샘플에서는 지난 3 달 동안 2017 NYC 노란색 Taxi 데이터 파일을 읽습니다. 파일 경로 당 탑승 수를 반환 합니다. 쿼리의 OPENROWSET 부분은 읽을 파일을 지정 합니다.

```sql
SELECT
    r.filepath() AS filepath
    ,COUNT_BIG(*) AS [rows]
FROM OPENROWSET(
        BULK 'https://sqlondemandstorage.blob.core.windows.net/csv/taxi/yellow_tripdata_2017-1*.csv',
        FORMAT = 'CSV',
        FIRSTROW = 2
    )
    WITH (
        vendor_id INT,
        pickup_datetime DATETIME2,
        dropoff_datetime DATETIME2,
        passenger_count SMALLINT,
        trip_distance FLOAT,
        rate_code SMALLINT,
        store_and_fwd_flag SMALLINT,
        pickup_location_id INT,
        dropoff_location_id INT,
        payment_type SMALLINT,
        fare_amount FLOAT,
        extra FLOAT,
        mta_tax FLOAT,
        tip_amount FLOAT,
        tolls_amount FLOAT,
        improvement_surcharge FLOAT,
        total_amount FLOAT
    ) AS [r]
GROUP BY
    r.filepath()
ORDER BY
    filepath;
```

다음 예에서는 WHERE 절에서 *filepath ()* 를 사용 하 여 읽을 파일을 필터링 하는 방법을 보여 줍니다.

쿼리의 OPENROWSET 부분에 와일드 카드를 사용 하 고 WHERE 절에서 파일을 필터링 할 수 있습니다. 결과는 이전 예제와 동일 합니다.

```sql
SELECT
    r.filepath() AS filepath
    ,r.filepath(1) AS [year]
    ,r.filepath(2) AS [month]
    ,COUNT_BIG(*) AS [rows]
FROM OPENROWSET(
        BULK 'https://sqlondemandstorage.blob.core.windows.net/csv/taxi/yellow_tripdata_*-*.csv',
        FORMAT = 'CSV',
        FIRSTROW = 2
    )
WITH (
    vendor_id INT,
    pickup_datetime DATETIME2,
    dropoff_datetime DATETIME2,
    passenger_count SMALLINT,
    trip_distance FLOAT,
    rate_code SMALLINT,
    store_and_fwd_flag SMALLINT,
    pickup_location_id INT,
    dropoff_location_id INT,
    payment_type SMALLINT,
    fare_amount FLOAT,
    extra FLOAT,
    mta_tax FLOAT,
    tip_amount FLOAT,
    tolls_amount FLOAT,
    improvement_surcharge FLOAT,
    total_amount FLOAT
) AS [r]
WHERE
    r.filepath(1) IN ('2017')
    AND r.filepath(2) IN ('10', '11', '12')
GROUP BY
    r.filepath()
    ,r.filepath(1)
    ,r.filepath(2)
ORDER BY
    filepath;
```

## <a name="next-steps"></a>다음 단계

다음 문서에서는 [Parquet 파일을 쿼리](query-parquet-files.md)하는 방법에 대해 알아봅니다.
