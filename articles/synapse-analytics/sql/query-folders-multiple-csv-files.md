---
title: SQL 주문형 (미리 보기)를 사용 하 여 폴더 및 여러 CSV 파일 쿼리
description: SQL 주문형 (미리 보기)는 Windows OS에서 사용 되는 와일드 카드와 유사한 와일드 카드를 사용 하 여 여러 파일/폴더를 읽을 수 있도록 지원 합니다.
services: synapse analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: ''
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: 8f8af7fab7113e38b91c3f5f1bcc41b4e4fba2c1
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81457368"
---
# <a name="query-folders-and-multiple-csv-files"></a>폴더 및 여러 CSV 파일 쿼리  

이 문서에서는 Azure Synapse Analytics에서 SQL 주문형 (미리 보기)를 사용 하 여 쿼리를 작성 하는 방법에 대해 알아봅니다.

SQL 주문형은 Windows OS에서 사용 되는 와일드 카드와 유사한 와일드 카드를 사용 하 여 여러 파일/폴더를 읽을 수 있도록 지원 합니다. 그러나 여러 와일드 카드를 사용할 수 있으므로 유연성이 향상 됩니다.

## <a name="prerequisites"></a>전제 조건

이 문서의 나머지 부분을 읽기 전에 아래에 나열 된 문서를 검토 해야 합니다.

- [처음 설정](query-data-storage.md#first-time-setup)
- [필수 구성 요소](query-data-storage.md#prerequisites)

## <a name="read-multiple-files-in-folder"></a>폴더의 여러 파일 읽기

*Csv/taxi* 폴더를 사용 하 여 샘플 쿼리를 따릅니다. 7 월 2016에서 6 월 2018로 NYC Taxi-노란색 Taxi 여행 레코드 데이터를 포함 합니다.

*Csv/taxi* 의 파일 이름은 year 및 month로 지정 됩니다.

- yellow_tripdata_2016 -07
- yellow_tripdata_2016 -08
- yellow_tripdata_2016 -09
- ...
- yellow_tripdata_2018 -04
- yellow_tripdata_2018 -05
- yellow_tripdata_2018 -06

각 파일의 구조는 다음과 같습니다.
        
    [First 10 rows of the CSV file](./media/querying-folders-and-multiple-csv-files/nyc-taxi.png)

## <a name="read-all-files-in-folder"></a>폴더의 모든 파일 읽기
    
아래 예제는 *csv/Taxi* 폴더에서 모든 NYC 노랑색 Taxi 데이터 파일을 읽고 연간 승객 및 탑승의 총 수를 반환 합니다. 또한 집계 함수의 사용법을 보여 줍니다.

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
> 단일 OPENROWSET로 액세스 되는 모든 파일에는 동일한 구조 (즉, 열 개수 및 데이터 형식)가 있어야 합니다.

### <a name="read-subset-of-files-in-folder"></a>폴더에 있는 파일의 하위 집합 읽기

아래 예제에서는 와일드 카드를 사용 하 여 *csv/Taxi* 폴더에서 2017 NYC 노란 Taxi 데이터 파일을 읽고 지불 유형 당 총 요금을 반환 합니다.

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
> 단일 OPENROWSET로 액세스 되는 모든 파일에는 동일한 구조 (즉, 열 개수 및 데이터 형식)가 있어야 합니다.

## <a name="read-folders"></a>폴더 읽기

OPENROWSET에 제공 하는 경로는 폴더에 대 한 경로일 수도 있습니다. 다음 섹션에는 이러한 쿼리 형식이 포함 되어 있습니다.

### <a name="read-all-files-from-specific-folder"></a>특정 폴더의 모든 파일 읽기

폴더의 [모든 파일 읽기](#read-all-files-in-folder)에 표시 된 것 처럼 파일 수준 와일드 카드를 사용 하 여 폴더의 모든 파일을 읽을 수 있습니다. 그러나 폴더를 쿼리하고 해당 폴더 내의 모든 파일을 사용 하는 방법이 있습니다.

OPENROWSET에서 제공 된 경로가 폴더를 가리키는 경우 해당 폴더의 모든 파일이 쿼리의 원본으로 사용 됩니다. 다음 쿼리는 *csv/taxi* 폴더에 있는 모든 파일을 읽습니다.

> [!NOTE]
> 아래 쿼리의 경로 끝에/가 있는지 확인 합니다. 폴더를 나타냅니다. /이 생략 된 경우 쿼리는 대신 *taxi* 이라는 파일을 대상으로 합니다.

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
> 단일 OPENROWSET로 액세스 되는 모든 파일에는 동일한 구조 (즉, 열 개수 및 데이터 형식)가 있어야 합니다.

### <a name="read-all-files-from-multiple-folders"></a>여러 폴더의 모든 파일 읽기

와일드 카드를 사용 하 여 여러 폴더에서 파일을 읽을 수 있습니다. 다음 쿼리는 이름이 *t* 로 시작 하 고 *i*로 끝나는 *csv* 폴더에 있는 모든 폴더의 모든 파일을 읽습니다.

> [!NOTE]
> 아래 쿼리의 경로 끝에/가 있는지 확인 합니다. 폴더를 나타냅니다. /이 생략 된 경우 쿼리는 대신 *t&ast;i* 라는 파일을 대상으로 합니다.

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
> 단일 OPENROWSET로 액세스 되는 모든 파일에는 동일한 구조 (즉, 열 개수 및 데이터 형식)가 있어야 합니다.

기준과 일치 하는 폴더가 하나만 있으므로 쿼리 결과는 [폴더의 모든 파일 읽기](#read-all-files-in-folder)와 동일 합니다.

## <a name="multiple-wildcards"></a>여러 와일드 카드

여러 경로 수준에서 여러 와일드 카드를 사용할 수 있습니다. 예를 들어, 이전 쿼리를 보강 하 여 2017 데이터만 있는 파일을 읽을 수 있으며, 이름이 *t* 로 시작 하 고 *i*로 끝나는 모든 폴더에서 파일을 읽을 수 있습니다.

> [!NOTE]
> 아래 쿼리의 경로 끝에/가 있는지 확인 합니다. 폴더를 나타냅니다. /이 생략 된 경우 쿼리는 대신 *t&ast;i* 라는 파일을 대상으로 합니다.
> 쿼리당 최대 10 개의 와일드 카드 제한이 있습니다.

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
> 단일 OPENROWSET로 액세스 되는 모든 파일에는 동일한 구조 (즉, 열 개수 및 데이터 형식)가 있어야 합니다.

기준과 일치 하는 폴더가 하나만 있으므로 쿼리 결과는 [폴더에 있는 파일의 하위 집합을 읽고](#read-subset-of-files-in-folder) [특정 폴더의 모든 파일을 읽습니다](#read-all-files-from-specific-folder). 더 복잡 한 와일드 카드 사용 시나리오는 [쿼리 Parquet 파일](query-parquet-files.md)에 설명 되어 있습니다.

## <a name="next-steps"></a>다음 단계

자세한 내용은 [Query 특정 파일](query-specific-files.md) 의 문서에서 찾을 수 있습니다.
