---
title: SQL 주문형 (미리 보기)을 사용 하 여 Parquet 중첩 형식 쿼리
description: 이 문서에서는 Parquet 중첩 형식을 쿼리 하는 방법에 대해 알아봅니다.
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: ''
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: a1e3d3c7494aa75b3f6d481d12135316791772d4
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81431658"
---
# <a name="query-parquet-nested-types-using-sql-on-demand-preview-in-azure-synapse-analytics"></a>Azure Synapse Analytics에서 SQL 주문형 (미리 보기)을 사용 하 여 Parquet 중첩 형식 쿼리

이 문서에서는 Azure Synapse Analytics에서 SQL 주문형 (미리 보기)를 사용 하 여 쿼리를 작성 하는 방법에 대해 알아봅니다.  이 쿼리는 Parquet 중첩 형식을 읽습니다.

## <a name="prerequisites"></a>전제 조건

이 문서의 나머지 부분을 읽기 전에 다음 문서를 검토 하세요.

- [처음 설정](query-data-storage.md#first-time-setup)
- [필수 구성 요소](query-data-storage.md#prerequisites)

## <a name="project-nested-or-repeated-data"></a>중첩되거나 반복되는 데이터 프로젝션

다음 쿼리는 parquet 파일을 읽습니다 *justSimpleArray* . 중첩 되거나 반복 되는 데이터를 포함 하 여 Parquet 파일의 모든 열을 프로젝션 합니다.

```sql
SELECT
    *
FROM
    OPENROWSET(
        BULK 'https://sqlondemandstorage.blob.core.windows.net/parquet/nested/justSimpleArray.parquet',
        FORMAT='PARQUET'
    ) AS [r];
```

## <a name="access-elements-from-nested-columns"></a>중첩된 열의 요소에 액세스

다음 쿼리는 *structExample* 파일을 읽고 중첩 열의 요소를 표시 하는 방법을 보여 줍니다.

```sql
SELECT
    *
FROM
    OPENROWSET(
        BULK 'https://sqlondemandstorage.blob.core.windows.net/parquet/nested/structExample.parquet',
        FORMAT='PARQUET'
    )
    WITH (
        -- you can see original n"sted columns values by uncommenting lines below
        --DateStruct VARCHAR(8000),
        [DateStruct.Date] DATE,
        --TimeStruct VARCHAR(8000),
        [TimeStruct.Time] TIME,
        --TimestampStruct VARCHAR(8000),
        [TimestampStruct.Timestamp] DATETIME2,
        --DecimalStruct VARCHAR(8000),
        [DecimalStruct.Decimal] DECIMAL(18, 5),
        --FloatStruct VARCHAR(8000),
        [FloatStruct.Float] FLOAT
    ) AS [r];
```

## <a name="access-elements-from-repeated-columns"></a>반복된 열의 요소에 액세스

다음 쿼리는 *justSimpleArray* 파일을 읽고 [JSON_VALUE](/sql/t-sql/functions/json-value-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) 를 사용 하 여 배열 또는 맵과 같은 반복 열에서 **스칼라** 요소를 검색 합니다.

```sql
SELECT
    *,
    JSON_VALUE(SimpleArray, '$[0]') AS FirstElement,
    JSON_VALUE(SimpleArray, '$[1]') AS SecondElement,
    JSON_VALUE(SimpleArray, '$[2]') AS ThirdElement
FROM
    OPENROWSET(
        BULK 'https://sqlondemandstorage.blob.core.windows.net/parquet/nested/justSimpleArray.parquet',
        FORMAT='PARQUET'
    ) AS [r];
```

다음 쿼리는 *mapexample* 파일을 읽고 [JSON_QUERY](/sql/t-sql/functions/json-query-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) 를 사용 하 여 배열 또는 맵과 같은 반복 되는 열 내에서 **비 스칼라** 요소를 검색 합니다.

```sql
SELECT
    MapOfPersons,
    JSON_QUERY(MapOfPersons, '$."John Doe"') AS [John]
FROM
    OPENROWSET(
        BULK 'https://sqlondemandstorage.blob.core.windows.net/parquet/nested/mapExample.parquet',
        FORMAT='PARQUET'
    ) AS [r];
```

## <a name="next-steps"></a>다음 단계

다음 문서에서는 [JSON 파일을 쿼리](query-json-files.md)하는 방법을 보여 줍니다.
