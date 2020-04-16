---
title: 주문형 SQL을 사용하여 마루 파일 쿼리(미리 보기)
description: 이 문서에서는 SQL 주문형(미리 보기)을 사용하여 Parquet 파일을 쿼리하는 방법을 배웁니다.
services: synapse analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: ''
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: 0b272a8c8ce81fc40585014e5930f5d7b1b5f2c0
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81431697"
---
# <a name="query-parquet-files-using-sql-on-demand-preview-in-azure-synapse-analytics"></a>Azure 시냅스 분석에서 SQL 주문형(미리 보기)을 사용하여 Parquet 파일을 쿼리합니다.

이 문서에서는 Parquet 파일을 읽을 SQL 주문형(미리 보기)을 사용하여 쿼리를 작성하는 방법을 알아봅니다.

## <a name="prerequisites"></a>사전 요구 사항

이 문서의 나머지 부분을 읽기 전에 다음 문서를 검토하십시오.

- [처음 설정](query-data-storage.md#first-time-setup)
- [필수 구성 요소](query-data-storage.md#prerequisites)

## <a name="dataset"></a>데이터 세트

CSV 파일을 읽는 것과 동일한 방식으로 Parquet 파일을 쿼리할 수 있습니다. 유일한 차이점은 FILEFORMAT 매개 변수를 PARQUET로 설정해야 한다는 것입니다. 이 문서의 예는 마루 파일을 읽는 세부 사항을 보여줍니다.

> [!NOTE]
> 마루 파일을 읽을 때 OPENROWSET WITH 절에서 열을 지정할 필요가 없습니다. SQL 온디맨드에서는 Parquet 파일의 메타데이터를 활용하고 이름으로 열을 바인딩합니다.

샘플 쿼리에 대 한 폴더 *쪽모이 개비/택시를* 사용 합니다. 그것은 뉴욕 택시가 포함되어 있습니다 - 7 월 2016에서 노란색 택시 여행 기록 데이터. 2018년 6월까지

데이터는 연도 및 월별로 분할되며 폴더 구조는 다음과 같습니다.

- 연도=2016
  - 월=6
  - ...
  - 월=12
- 연도=2017
  - 월=1
  - ...
  - 월=12
- 연도=2018
  - 월=1
  - ...
  - 월=6

## <a name="query-set-of-parquet-files"></a>마루 파일의 쿼리 집합

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

## <a name="automatic-schema-inference"></a>자동 스키마 추론

마루 파일을 읽을 때 OPENROWSET with 절을 사용할 필요가 없습니다. 열 이름과 데이터 형식은 마루 파일에서 자동으로 읽습니다.

아래 샘플에서는 Parquet 파일에 대한 자동 스키마 추론 기능을 보여 주십을 보여 주십습니다. 스키마를 지정하지 않고 2017년 9월에 행 수를 반환합니다.

> [!NOTE]
> 마루 파일을 읽을 때 OPENROWSET WITH 절에서 열을 지정할 필요가 없습니다. 이 경우 SQL 주문형 쿼리 서비스는 Parquet 파일의 메타데이터를 활용하고 이름으로 열을 바인딩합니다.

```sql
SELECT
    COUNT_BIG(*)
FROM
    OPENROWSET(
        BULK 'https://sqlondemandstorage.blob.core.windows.net/parquet/taxi/year=2017/month=9/*.parquet',
        FORMAT='PARQUET'
    ) AS nyc;
```

### <a name="query-partitioned-data"></a>분할된 데이터 쿼리

이 샘플에 제공된 데이터 집합은 별도의 하위 폴더로 분할(분할)됩니다. filepath 함수를 사용하여 특정 파티션을 대상으로 지정할 수 있습니다. 이 예제에서는 2017년 첫 3개월 동안의 연도, 월 및 payment_type 요금을 보여 군요.

> [!NOTE]
> SQL 주문형 쿼리는 Hive/Hadoop 분할 체계와 호환됩니다.

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

마루 파일에는 모든 열에 대한 형식 설명이 포함되어 있습니다. 다음 표는 Parquet 형식이 SQL 네이티브 형식에 매핑되는 방법을 설명합니다.

| 마루 타입 | 마루 논리 유형(부수기) | SQL 데이터 형식 |
| --- | --- | --- |
| BOOLEAN | | bit |
| 바이너리 / BYTE_ARRAY | | varbinary |
| DOUBLE | | float |
| FLOAT | | real |
| Int32 | | int |
| INT64 | | bigint |
| INT96 | |datetime2 |
| FIXED_LEN_BYTE_ARRAY | |binary |
| BINARY |UTF8 |바르차르(UTF8 \*데이터 정렬) |
| BINARY |STRING |바르차르(UTF8 \*데이터 정렬) |
| BINARY |열거형|바르차르(UTF8 \*데이터 정렬) |
| BINARY |UUID |uniqueidentifier |
| BINARY |DECIMAL |decimal |
| BINARY |JSON |바르차(최대) \*(UTF8 데이터 정렬) |
| BINARY |BSON |varbinary(max) |
| FIXED_LEN_BYTE_ARRAY |DECIMAL |decimal |
| BYTE_ARRAY |INTERVAL |표준화된 형식으로 직렬화된 바르차(max) |
| Int32 |INT(8, 트루) |smallint |
| Int32 |INT (16, 사실) |smallint |
| Int32 |INT (32, 사실) |int |
| Int32 |INT (8, 거짓) |tinyint |
| Int32 |INT (16, 거짓) |int |
| Int32 |INT (32, 거짓) |bigint |
| Int32 |DATE |date |
| Int32 |DECIMAL |decimal |
| Int32 |시간 (밀리스)|time |
| INT64 |INT (64, 트루) |bigint |
| INT64 |INT (64, 거짓) |소수점 (20,0) |
| INT64 |DECIMAL |decimal |
| INT64 |시간(마이크로/나노) |time |
|INT64 |타임스탬프(밀리/마이크로/나노) |datetime2 |
|[복합 유형](https://github.com/apache/parquet-format/blob/master/LogicalTypes.md#lists) |명단 등록 |JSON으로 직렬화된 바르차(max) |
|[복합 유형](https://github.com/apache/parquet-format/blob/master/LogicalTypes.md#maps)|MAP|JSON으로 직렬화된 바르차(max) |

## <a name="next-steps"></a>다음 단계

마루 [중첩 형식을 쿼리하는](query-parquet-nested-types.md)방법을 알아보려면 다음 문서로 이동합니다.
