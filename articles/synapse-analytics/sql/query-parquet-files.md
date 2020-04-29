---
title: SQL 주문형 (미리 보기)을 사용 하 여 Parquet 파일 쿼리
description: 이 문서에서는 SQL 주문형 (미리 보기)를 사용 하 여 Parquet 파일을 쿼리 하는 방법에 대해 알아봅니다.
services: synapse analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: ''
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: 0b272a8c8ce81fc40585014e5930f5d7b1b5f2c0
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81431697"
---
# <a name="query-parquet-files-using-sql-on-demand-preview-in-azure-synapse-analytics"></a>Azure Synapse Analytics에서 SQL 주문형 (미리 보기)을 사용 하 여 Parquet 파일 쿼리

이 문서에서는 Parquet 파일을 읽을 SQL 주문형 (미리 보기)를 사용 하 여 쿼리를 작성 하는 방법에 대해 알아봅니다.

## <a name="prerequisites"></a>전제 조건

이 문서의 나머지 부분을 읽기 전에 다음 문서를 검토 하세요.

- [처음 설정](query-data-storage.md#first-time-setup)
- [필수 구성 요소](query-data-storage.md#prerequisites)

## <a name="dataset"></a>데이터 세트

CSV 파일을 읽는 것과 같은 방법으로 Parquet 파일을 쿼리할 수 있습니다. 유일한 차이점은 FILEFORMAT 매개 변수를 PARQUET로 설정 해야 한다는 것입니다. 이 문서의 예에서는 Parquet 파일 읽기의 세부 정보를 보여 줍니다.

> [!NOTE]
> Parquet 파일을 읽을 때 OPENROWSET WITH 절에 열을 지정할 필요가 없습니다. SQL 주문형 요청은 Parquet 파일의 메타 데이터를 활용 하 고 이름으로 열을 바인딩합니다.

샘플 쿼리에는 *parquet/taxi* 폴더를 사용 합니다. 7 월 2016 NYC Taxi-노란색 Taxi 여행 레코드 데이터를 포함 합니다. 6 월 2018 까지입니다.

데이터는 연도 및 월별로 분할 되 고 폴더 구조는 다음과 같습니다.

- year = 2016
  - 월 = 6
  - ...
  - 월 = 12
- 년 = 2017
  - 월 = 1
  - ...
  - 월 = 12
- 년 = 2018
  - 월 = 1
  - ...
  - 월 = 6

## <a name="query-set-of-parquet-files"></a>Parquet 파일의 쿼리 집합

Parquet 파일을 쿼리할 때 관심 있는 열만 지정할 수 있습니다.

```sql
SELECT
        YEAR(pickup_datetime),
        passenger_count,
        COUNT(*) AS cnt
FROM  
    OPENROWSET(
        BULK 'https://sqlondemandstorage.blob.core.windows.net/parquet/taxi/*/*/*',
        FORMAT='PARQUET'
    ) WITH (
        pickup_datetime DATETIME2,
        passenger_count INT
    ) AS nyc
GROUP BY
    passenger_count,
    YEAR(pickup_datetime)
ORDER BY
    YEAR(pickup_datetime),
    passenger_count;
```

## <a name="automatic-schema-inference"></a>자동 스키마 유추

Parquet 파일을 읽을 때 OPENROWSET WITH 절을 사용할 필요가 없습니다. 열 이름 및 데이터 형식은 Parquet 파일에서 자동으로 읽습니다.

아래 샘플에서는 Parquet 파일에 대 한 자동 스키마 유추 기능을 보여 줍니다. 스키마를 지정 하지 않고 9 월 2017에 행 수를 반환 합니다.

> [!NOTE]
> Parquet 파일을 읽을 때 OPENROWSET WITH 절에 열을 지정할 필요가 없습니다. 이 경우 SQL 주문형 쿼리 서비스는 Parquet 파일에서 메타 데이터를 활용 하 고 이름을 기준으로 열을 바인딩합니다.

```sql
SELECT
    COUNT_BIG(*)
FROM
    OPENROWSET(
        BULK 'https://sqlondemandstorage.blob.core.windows.net/parquet/taxi/year=2017/month=9/*.parquet',
        FORMAT='PARQUET'
    ) AS nyc;
```

### <a name="query-partitioned-data"></a>분할 된 데이터 쿼리

이 샘플에서 제공 하는 데이터 집합은 별도의 하위 폴더로 분할 (분할) 됩니다. Filepath 함수를 사용 하 여 특정 파티션을 대상으로 지정할 수 있습니다. 이 예에서는 2017의 처음 3 개월에 대 한 년, 월 및 payment_type의 요금 금액을 보여 줍니다.

> [!NOTE]
> SQL 요청 시 쿼리는 Hive/Hadoop 파티션 구성표와 호환 됩니다.

```sql
SELECT
    nyc.filepath(1) AS [year],
    nyc.filepath(2) AS [month],
    payment_type,
    SUM(fare_amount) AS fare_total
FROM
    OPENROWSET(
        BULK 'https://sqlondemandstorage.blob.core.windows.net/parquet/taxi/year=*/month=*/*.parquet',
        FORMAT='PARQUET'
    ) AS nyc
WHERE
    nyc.filepath(1) = 2017
    AND nyc.filepath(2) IN (1, 2, 3)
    AND pickup_datetime BETWEEN CAST('1/1/2017' AS datetime) AND CAST('3/31/2017' AS datetime)
GROUP BY
    nyc.filepath(1),
    nyc.filepath(2),
    payment_type
ORDER BY
    nyc.filepath(1),
    nyc.filepath(2),
    payment_type;
```

## <a name="type-mapping"></a>형식 매핑

Parquet 파일에는 모든 열에 대 한 형식 설명이 포함 되어 있습니다. 다음 표에서는 Parquet 형식이 SQL 네이티브 형식에 매핑되는 방법을 설명 합니다.

| Parquet 형식 | Parquet logical type (annotation) | SQL 데이터 형식 |
| --- | --- | --- |
| BOOLEAN | | bit |
| BINARY/BYTE_ARRAY | | varbinary |
| DOUBLE | | float |
| FLOAT | | real |
| INT32 | | int |
| INT64 | | bigint |
| INT96 | |datetime2 |
| FIXED_LEN_BYTE_ARRAY | |binary |
| BINARY |UTF8 |varchar \*(UTF8 데이터 정렬) |
| BINARY |STRING |varchar \*(UTF8 데이터 정렬) |
| BINARY |열거형|varchar \*(UTF8 데이터 정렬) |
| BINARY |UUID |uniqueidentifier |
| BINARY |DECIMAL |decimal |
| BINARY |JSON |varchar (max) \*(UTF8 데이터 정렬) |
| BINARY |BSON |varbinary(max) |
| FIXED_LEN_BYTE_ARRAY |DECIMAL |decimal |
| BYTE_ARRAY |INTERVAL |varchar (max), 표준화 된 형식으로 직렬화 됨 |
| INT32 |INT (8, true) |smallint |
| INT32 |INT (16, true) |smallint |
| INT32 |INT (32, true) |int |
| INT32 |INT (8, false) |tinyint |
| INT32 |INT (16, false) |int |
| INT32 |INT (32, false) |bigint |
| INT32 |DATE |date |
| INT32 |DECIMAL |decimal |
| INT32 |시간 (MILLIS)|time |
| INT64 |INT (64, true) |bigint |
| INT64 |INT (64, false) |decimal (20, 0) |
| INT64 |DECIMAL |decimal |
| INT64 |시간 (마이크로 S/NANOS) |time |
|INT64 |TIMESTAMP (MILLIS/마이크로 S/NANOS) |datetime2 |
|[복합 형식](https://github.com/apache/parquet-format/blob/master/LogicalTypes.md#lists) |명단 등록 |varchar (max), JSON으로 직렬화 됨 |
|[복합 형식](https://github.com/apache/parquet-format/blob/master/LogicalTypes.md#maps)|MAP|varchar (max), JSON으로 직렬화 됨 |

## <a name="next-steps"></a>다음 단계

다음 문서로 이동 하 여 [Parquet 중첩 형식을 쿼리](query-parquet-nested-types.md)하는 방법을 알아보세요.
