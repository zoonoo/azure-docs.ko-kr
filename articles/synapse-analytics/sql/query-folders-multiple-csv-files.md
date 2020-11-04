---
title: 서버를 사용 하지 않는 SQL 풀을 사용 하 여 폴더 및 여러 파일 쿼리 (미리 보기)
description: 서버를 사용 하지 않는 SQL 풀 (미리 보기)은 Windows OS에서 사용 되는 와일드 카드와 유사한 와일드 카드를 사용 하 여 여러 파일/폴더를 읽을 수 있도록 지원 합니다.
services: synapse analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: sql
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick
ms.openlocfilehash: 71ed590440a8c7e37a071b4eadfc09977ef91d5e
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/04/2020
ms.locfileid: "93310841"
---
# <a name="query-folders-and-multiple-files"></a>폴더 및 여러 파일 쿼리  

이 문서에서는 Azure Synapse Analytics에서 서버를 사용 하지 않는 SQL 풀 (미리 보기)를 사용 하 여 쿼리를 작성 하는 방법을 알아봅니다.

서버를 사용 하지 않는 SQL 풀은 Windows OS에서 사용 되는 와일드 카드와 유사한 와일드 카드를 사용 하 여 여러 파일/폴더 읽기를 지원 합니다. 그러나 여러 와일드 카드를 사용할 수 있으므로 유연성이 향상 됩니다.

## <a name="prerequisites"></a>필수 구성 요소

첫 번째 단계는 쿼리를 실행할 **데이터베이스를 만드는** 것입니다. 그런 다음, 해당 데이터베이스에서 [설치 스크립트](https://github.com/Azure-Samples/Synapse/blob/master/SQL/Samples/LdwSample/SampleDB.sql)를 실행하여 개체를 초기화합니다. 이 설치 스크립트는 이러한 샘플에서 사용되는 데이터 원본, 데이터베이스 범위 자격 증명 및 외부 파일 형식을 만듭니다.

*Csv/taxi* 폴더를 사용 하 여 샘플 쿼리를 따릅니다. 7 월 2016에서 6 월 2018로 NYC Taxi-노란색 Taxi 여행 레코드 데이터를 포함 합니다. *Csv/taxi* 의 파일은 다음 패턴을 사용 하 여 year 및 month로 이름이 지정 됩니다. <year> - <month> csv: yellow_tripdata_

## <a name="read-all-files-in-folder"></a>폴더의 모든 파일 읽기
    
아래 예제는 *csv/Taxi* 폴더에서 모든 NYC 노랑색 Taxi 데이터 파일을 읽고 연간 승객 및 탑승의 총 수를 반환 합니다. 또한 집계 함수의 사용법을 보여 줍니다.

```sql
SELECT 
    YEAR(pickup_datetime) as [year],
    SUM(passenger_count) AS passengers_total,
    COUNT(*) AS [rides_total]
FROM OPENROWSET(
        BULK 'csv/taxi/*.csv',
        DATA_SOURCE = 'sqlondemanddemo',
        FORMAT = 'CSV', PARSER_VERSION = '2.0',
        FIRSTROW = 2
    )
    WITH (
        pickup_datetime DATETIME2 2, 
        passenger_count INT 4
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
        BULK 'csv/taxi/yellow_tripdata_2017-*.csv',
        DATA_SOURCE = 'sqlondemanddemo',
        FORMAT = 'CSV', PARSER_VERSION = '2.0',
        FIRSTROW = 2
    )
    WITH (
        payment_type INT 10,
        fare_amount FLOAT 11
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
        BULK 'csv/taxi/',
        DATA_SOURCE = 'sqlondemanddemo',
        FORMAT = 'CSV', PARSER_VERSION = '2.0',
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

와일드 카드를 사용 하 여 여러 폴더에서 파일을 읽을 수 있습니다. 다음 쿼리는 이름이 *t* 로 시작 하 고 *i* 로 끝나는 *csv* 폴더에 있는 모든 폴더의 모든 파일을 읽습니다.

> [!NOTE]
> 아래 쿼리의 경로 끝에/가 있는지 확인 합니다. 폴더를 나타냅니다. /이 생략 된 경우 쿼리는 대신 *t &ast; i* 라는 파일을 대상으로 합니다.

```sql
SELECT
    YEAR(pickup_datetime) as [year],
    SUM(passenger_count) AS passengers_total,
    COUNT(*) AS [rides_total]
FROM OPENROWSET(
        BULK 'csv/t*i/', 
        DATA_SOURCE = 'sqlondemanddemo',
        FORMAT = 'CSV', PARSER_VERSION = '2.0',
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

여러 경로 수준에서 여러 와일드 카드를 사용할 수 있습니다. 예를 들어, 이전 쿼리를 보강 하 여 2017 데이터만 있는 파일을 읽을 수 있으며, 이름이 *t* 로 시작 하 고 *i* 로 끝나는 모든 폴더에서 파일을 읽을 수 있습니다.

> [!NOTE]
> 아래 쿼리의 경로 끝에/가 있는지 확인 합니다. 폴더를 나타냅니다. /이 생략 된 경우 쿼리는 대신 *t &ast; i* 라는 파일을 대상으로 합니다.
> 쿼리당 최대 10 개의 와일드 카드 제한이 있습니다.

```sql
SELECT
    YEAR(pickup_datetime) as [year],
    SUM(passenger_count) AS passengers_total,
    COUNT(*) AS [rides_total]
FROM OPENROWSET(
        BULK 'csv/t*i/yellow_tripdata_2017-*.csv',
        DATA_SOURCE = 'sqlondemanddemo',
        FORMAT = 'CSV', PARSER_VERSION = '2.0',
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

자세한 내용은 [쿼리 관련 파일](query-specific-files.md) 문서에서 찾을 수 있습니다.
