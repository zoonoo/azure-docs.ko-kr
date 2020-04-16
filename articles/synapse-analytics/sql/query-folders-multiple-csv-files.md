---
title: SQL 온디맨드(미리 보기)를 사용하여 쿼리 폴더 및 여러 CSV 파일
description: SQL 주문형(미리 보기)은 Windows OS에서 사용되는 와일드카드와 유사한 와일드카드를 사용하여 여러 파일/폴더 읽기를 지원합니다.
services: synapse analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: ''
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: 070574f0eec40c27eb05cec3bf0a84282b85050f
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81431801"
---
# <a name="query-folders-and-multiple-csv-files"></a>쿼리 폴더 및 여러 CSV 파일  

이 문서에서는 Azure Synapse Analytics에서 SQL 주문형(미리 보기)을 사용하여 쿼리를 작성하는 방법을 알아봅니다.

SQL 온디맨드 지원은 Windows OS에서 사용되는 와일드카드와 유사한 와일드카드를 사용하여 여러 파일/폴더읽기를 지원합니다. 그러나 여러 와일드카드가 허용되므로 유연성이 향상됩니다.

## <a name="prerequisites"></a>사전 요구 사항

이 문서의 나머지 부분을 읽기 전에 아래에 나열된 문서를 검토하십시오.

- [처음 설정](query-data-storage.md#first-time-setup)
- [필수 구성 요소](query-data-storage.md#prerequisites)

## <a name="read-multiple-files-in-folder"></a>폴더에서 여러 파일 읽기

*csv/택시* 폴더를 사용하여 샘플 쿼리를 따릅니다. 그것은 NYC 택시가 포함되어 있습니다 - 노란색 택시 여행 기록 데이터 7 월 2016 에서 6 월 2018.

*csv/택시의* 파일은 연도 및 월 이름을 따서 명명됩니다.

- yellow_tripdata_2016-07.csv
- yellow_tripdata_2016-08.csv
- yellow_tripdata_2016-09.csv
- ...
- yellow_tripdata_2018-04.csv
- yellow_tripdata_2018-05.csv
- yellow_tripdata_2018-06.csv

각 파일에는 다음과 같은 구조가 있습니다.
        
    [First 10 rows of the CSV file](./media/querying-folders-and-multiple-csv-files/nyc-taxi.png)

## <a name="read-all-files-in-folder"></a>폴더의 모든 파일 읽기
    
아래 예제는 *csv/Taxi* 폴더의 모든 NYC 옐로우 택시 데이터 파일을 읽고 연간 총 승객 및 차량 수를 반환합니다. 또한 집계 함수의 사용법도 보여 주며,

```sql
SELECT 
    YEAR(pickup_datetime) as [year],
    SUM(passenger_count) AS passengers_total,
    COUNT(*) AS [rides_total]
FROM OPENROWSET(
    BULK 'https://sqlondemandstorage.blob.core.windows.net/csv/taxi/*.*',
        FORMAT = 'CSV', 
        FIRSTROW = 2
    )
    WITH (
        vendor_id VARCHAR(100) COLLATE Latin1_General_BIN2, 
        pickup_datetime DATETIME2, 
        dropoff_datetime DATETIME2,
        passenger_count INT,
           trip_distance FLOAT,
        rate_code INT,
        store_and_fwd_flag VARCHAR(100) COLLATE Latin1_General_BIN2,
           pickup_location_id INT,'
        dropoff_location_id INT,
           payment_type INT,
        fare_amount FLOAT,
        extra FLOAT,
        mta_tax FLOAT,
        tip_amount FLOAT,
        tolls_amount FLOAT,
        improvement_surcharge FLOAT,
        total_amount FLOAT
    ) AS nyc
GROUP BY
    YEAR(pickup_datetime)
ORDER BY
    YEAR(pickup_datetime);
```

> [!NOTE]
> 단일 OPENROWSET으로 액세스하는 모든 파일은 동일한 구조(예: 열 수 및 해당 데이터 형식)를 가져야 합니다.

### <a name="read-subset-of-files-in-folder"></a>폴더에 있는 파일의 하위 집합 읽기

아래 예제는 와일드카드를 사용하여 *csv/Taxi* 폴더의 2017 NYC 옐로우 택시 데이터 파일을 읽고 결제 유형별 총 요금 금액을 반환합니다.

```sql
SELECT 
    payment_type,  
    SUM(fare_amount) AS fare_total
FROM OPENROWSET(
    BULK 'https://sqlondemandstorage.blob.core.windows.net/csv/taxi/yellow_tripdata_2017-*.csv',
        FORMAT = 'CSV', 
        FIRSTROW = 2
    )
    WITH (
        vendor_id VARCHAR(100) COLLATE Latin1_General_BIN2, 
        pickup_datetime DATETIME2, 
        dropoff_datetime DATETIME2,
        passenger_count INT,
        trip_distance FLOAT,
        rate_code INT,
        store_and_fwd_flag VARCHAR(100) COLLATE Latin1_General_BIN2,
        pickup_location_id INT,
        dropoff_location_id INT,
        payment_type INT,
        fare_amount FLOAT,
        extra FLOAT,
        mta_tax FLOAT,
        tip_amount FLOAT,
        tolls_amount FLOAT,
        improvement_surcharge FLOAT,
        total_amount FLOAT
    ) AS nyc
GROUP BY payment_type
ORDER BY payment_type;
```

> [!NOTE]
> 단일 OPENROWSET으로 액세스하는 모든 파일은 동일한 구조(예: 열 수 및 해당 데이터 형식)를 가져야 합니다.

## <a name="read-folders"></a>폴더 읽기

OPENROWSET에 제공하는 경로는 폴더에 대한 경로일 수도 있습니다. 다음 섹션에는 이러한 쿼리 유형이 포함됩니다.

### <a name="read-all-files-from-specific-folder"></a>특정 폴더의 모든 파일 읽기

폴더의 모든 파일 읽기에 표시된 대로 파일 수준 와일드카드를 사용하여 [폴더의 모든 파일을](#read-all-files-in-folder)읽을 수 있습니다. 그러나 폴더를 쿼리하고 해당 폴더 내의 모든 파일을 사용하는 방법이 있습니다.

OPENROWSET에 제공된 경로가 폴더를 가리키는 경우 해당 폴더의 모든 파일이 쿼리의 소스로 사용됩니다. 다음 쿼리는 *csv/taxi* 폴더의 모든 파일을 읽습니다.

> [!NOTE]
> 아래 쿼리에서 경로끝에 있는 /의 존재를 기록합니다. 폴더를 나타냅니다. /를 생략하면 쿼리는 *대신 택시라는* 파일을 대상으로 합니다.

```sql
SELECT
    YEAR(pickup_datetime) as [year],
    SUM(passenger_count) AS passengers_total,
    COUNT(*) AS [rides_total]
FROM OPENROWSET(
    BULK 'https://sqlondemandstorage.blob.core.windows.net/csv/taxi/',
        FORMAT = 'CSV', 
        FIRSTROW = 2
    )
    WITH (
        vendor_id VARCHAR(100) COLLATE Latin1_General_BIN2, 
        pickup_datetime DATETIME2, 
        dropoff_datetime DATETIME2,
        passenger_count INT,
        trip_distance FLOAT,
        rate_code INT,
        store_and_fwd_flag VARCHAR(100) COLLATE Latin1_General_BIN2,
        pickup_location_id INT,
        dropoff_location_id INT,
        payment_type INT,
        fare_amount FLOAT,
        extra FLOAT,
        mta_tax FLOAT,
        tip_amount FLOAT,
        tolls_amount FLOAT,
        improvement_surcharge FLOAT,
        total_amount FLOAT
    ) AS nyc
GROUP BY
    YEAR(pickup_datetime)
ORDER BY
    YEAR(pickup_datetime);
```

> [!NOTE]
> 단일 OPENROWSET으로 액세스하는 모든 파일은 동일한 구조(예: 열 수 및 해당 데이터 형식)를 가져야 합니다.

### <a name="read-all-files-from-multiple-folders"></a>여러 폴더의 모든 파일 읽기

와일드카드를 사용하여 여러 폴더에서 파일을 읽을 수 있습니다. 다음 쿼리는 *t로* 시작하고 *i로*끝나는 이름이 있는 *csv* 폴더에 있는 모든 폴더의 모든 파일을 읽습니다.

> [!NOTE]
> 아래 쿼리에서 경로끝에 있는 /의 존재를 기록합니다. 폴더를 나타냅니다. /를 생략하면 쿼리는 *&ast;t i라는* 파일을 대신 대상으로 지정합니다.

```sql
SELECT
    YEAR(pickup_datetime) as [year],
    SUM(passenger_count) AS passengers_total,
    COUNT(*) AS [rides_total]
FROM OPENROWSET(
    BULK 'https://sqlondemandstorage.blob.core.windows.net/csv/t*i/', 
        FORMAT = 'CSV', 
        FIRSTROW = 2
    )
    WITH (
        vendor_id VARCHAR(100) COLLATE Latin1_General_BIN2, 
        pickup_datetime DATETIME2, 
        dropoff_datetime DATETIME2,
        passenger_count INT,
        trip_distance FLOAT,
        rate_code INT,
        store_and_fwd_flag VARCHAR(100) COLLATE Latin1_General_BIN2,
        pickup_location_id INT,
        dropoff_location_id INT,
        payment_type INT,
        fare_amount FLOAT,
        extra FLOAT,
        mta_tax FLOAT,
        tip_amount FLOAT,
        tolls_amount FLOAT,
        improvement_surcharge FLOAT,
        total_amount FLOAT
    ) AS nyc
GROUP BY
    YEAR(pickup_datetime)
ORDER BY
    YEAR(pickup_datetime);
```

> [!NOTE]
> 단일 OPENROWSET으로 액세스하는 모든 파일은 동일한 구조(예: 열 수 및 해당 데이터 형식)를 가져야 합니다.

조건과 일치하는 폴더가 하나만 있으므로 쿼리 결과는 [폴더의 모든 파일 읽기와](#read-all-files-in-folder)동일합니다.

## <a name="multiple-wildcards"></a>여러 와일드카드

다른 경로 수준에서 여러 와일드카드를 사용할 수 있습니다. 예를 들어, t로 시작하고 *i로*끝나는 모든 폴더에서 2017 데이터만 *t* 있는 파일을 읽을 수 있도록 이전 쿼리를 보강할 수 있습니다.

> [!NOTE]
> 아래 쿼리에서 경로끝에 있는 /의 존재를 기록합니다. 폴더를 나타냅니다. /를 생략하면 쿼리는 *&ast;t i라는* 파일을 대신 대상으로 지정합니다.
> 쿼리당 최대 와일드카드는 10개로 제한됩니다.

```sql
SELECT
    YEAR(pickup_datetime) as [year],
    SUM(passenger_count) AS passengers_total,
    COUNT(*) AS [rides_total]
FROM OPENROWSET(
    BULK 'https://sqlondemandstorage.blob.core.windows.net/csv/t*i/yellow_tripdata_2017-*.csv',
        FORMAT = 'CSV', 
        FIRSTROW = 2
    )
    WITH (
        vendor_id VARCHAR(100) COLLATE Latin1_General_BIN2, 
        pickup_datetime DATETIME2, 
        dropoff_datetime DATETIME2,
        passenger_count INT,
        trip_distance FLOAT,
        rate_code INT,
        store_and_fwd_flag VARCHAR(100) COLLATE Latin1_General_BIN2,
        pickup_location_id INT,
        dropoff_location_id INT,
        payment_type INT,
        fare_amount FLOAT,
        extra FLOAT,
        mta_tax FLOAT,
        tip_amount FLOAT,
        tolls_amount FLOAT,
        improvement_surcharge FLOAT,
        total_amount FLOAT
    ) AS nyc
GROUP BY
    YEAR(pickup_datetime)
ORDER BY
    YEAR(pickup_datetime);
```

> [!NOTE]
> 단일 OPENROWSET으로 액세스하는 모든 파일은 동일한 구조(예: 열 수 및 해당 데이터 형식)를 가져야 합니다.

조건과 일치하는 폴더가 하나만 있기 때문에 쿼리 결과는 [폴더의 파일 하위 집합 읽기](#read-subset-of-files-in-folder) 및 특정 [폴더의 모든 파일 읽기와](#read-all-files-from-specific-folder)동일합니다. 보다 복잡한 와일드카드 사용 시나리오는 [쿼리 Parquet 파일에서](query-parquet-files.md)다룹니다.

## <a name="next-steps"></a>다음 단계

자세한 내용은 에서 찾을 수 [있습니다쿼리 특정 파일](query-specific-files.md) 문서.
