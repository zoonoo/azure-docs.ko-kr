---
title: SQL 주문형(미리 보기)을 사용하여 Parquet 파일 쿼리
description: 이 문서에서는 SQL 주문형(미리 보기)을 사용하여 Parquet 파일을 쿼리하는 방법에 대해 설명합니다.
services: synapse analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: sql
ms.date: 05/20/2020
ms.author: v-stazar
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: 4bab1ef4588a705f0dd6cdb34be8272868f826e9
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85207569"
---
# <a name="query-parquet-files-using-sql-on-demand-preview-in-azure-synapse-analytics"></a>Azure Synapse Analytics에서 SQL 주문형(미리 보기)을 사용하여 Parquet 파일 쿼리

이 문서에서는 Parquet 파일을 읽는 SQL 주문형(미리 보기)을 사용하여 쿼리를 작성하는 방법을 알아봅니다.

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
> Parquet 파일을 읽을 때 OPENROWSET WITH 절에 열을 지정할 필요가 없습니다. 이 경우 SQL 주문형 쿼리 서비스는 Parquet 파일의 메타데이터를 활용하여 열을 이름별로 바인딩합니다.

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
> SQL 주문형 쿼리는 Hive/Hadoop 파티션 구성표와 호환됩니다.

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

Parquet 파일에는 모든 열에 대한 형식 설명이 포함되어 있습니다. 다음 표에서는 Parquet 형식이 SQL 네이티브 형식에 매핑되는 방법을 설명합니다.

| Parquet 형식 | Parquet 논리 형식(주석) | SQL 데이터 형식 |
| --- | --- | --- |
| BOOLEAN | | bit |
| BINARY / BYTE_ARRAY | | varbinary |
| DOUBLE | | float |
| FLOAT | | real |
| INT32 | | int |
| INT64 | | bigint |
| INT96 | |datetime2 |
| FIXED_LEN_BYTE_ARRAY | |binary |
| BINARY |UTF8 |varchar \*(UTF8 데이터 정렬) |
| BINARY |STRING |varchar \*(UTF8 데이터 정렬) |
| BINARY |ENUM|varchar \*(UTF8 데이터 정렬) |
| BINARY |UUID |uniqueidentifier |
| BINARY |DECIMAL |decimal |
| BINARY |JSON |varchar(max) \*(UTF8 데이터 정렬) |
| BINARY |BSON |varbinary(max) |
| FIXED_LEN_BYTE_ARRAY |DECIMAL |decimal |
| BYTE_ARRAY |INTERVAL |varchar(max), 표준화된 형식으로 직렬화됨 |
| INT32 |INT(8, true) |smallint |
| INT32 |INT(16, true) |smallint |
| INT32 |INT(32, true) |int |
| INT32 |INT(8, false) |tinyint |
| INT32 |INT(16, false) |int |
| INT32 |INT(32, false) |bigint |
| INT32 |DATE |date |
| INT32 |DECIMAL |decimal |
| INT32 |TIME(MILLIS )|time |
| INT64 |INT(64, true) |bigint |
| INT64 |INT(64, false) |decimal(20,0) |
| INT64 |DECIMAL |decimal |
| INT64 |TIME(MICROS / NANOS) |time |
|INT64 |TIMESTAMP(MILLIS / MICROS / NANOS) |datetime2 |
|[복합 형식](https://github.com/apache/parquet-format/blob/master/LogicalTypes.md#lists) |명단 등록 |varchar(max), JSON으로 직렬화됨 |
|[복합 형식](https://github.com/apache/parquet-format/blob/master/LogicalTypes.md#maps)|MAP|varchar(max), JSON으로 직렬화됨 |

## <a name="next-steps"></a>다음 단계

다음 문서로 진행하여 [Parquet 중첩 형식을 쿼리](query-parquet-nested-types.md)하는 방법을 알아봅니다.
