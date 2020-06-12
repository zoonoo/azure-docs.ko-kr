---
title: SQL 주문형(미리 보기)을 사용하여 Parquet 중첩 형식 쿼리
description: 이 문서에서는 Parquet 중첩 형식을 쿼리하는 방법에 대해 알아봅니다.
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: ''
ms.date: 05/20/2020
ms.author: v-stazar
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: 82edee84317b5d542bf65e29514286f96c18bbcc
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/21/2020
ms.locfileid: "83744232"
---
# <a name="query-parquet-nested-types-using-sql-on-demand-preview-in-azure-synapse-analytics"></a>Azure Synapse Analytics에서 SQL 주문형(미리 보기)을 사용하여 Parquet 중첩 형식 쿼리

이 문서에서는 Azure Synapse Analytics에서 SQL 주문형(미리 보기)을 사용하여 쿼리를 작성하는 방법에 대해 알아봅니다.  이 쿼리는 Parquet 중첩 형식을 읽습니다.

## <a name="prerequisites"></a>사전 요구 사항

첫 번째 단계는 참조하는 데이터 원본을 통해 **데이터베이스를 만드는** 것입니다. 그런 다음, 해당 데이터베이스에서 [설치 스크립트](https://github.com/Azure-Samples/Synapse/blob/master/SQL/Samples/LdwSample/SampleDB.sql)를 실행하여 개체를 초기화합니다. 이 설치 스크립트는 이러한 샘플에서 사용되는 데이터 원본, 데이터베이스 범위 자격 증명 및 외부 파일 형식을 만듭니다.

## <a name="project-nested-or-repeated-data"></a>중첩되거나 반복되는 데이터 프로젝션

다음 쿼리는 *justSimpleArray.parquet* 파일을 읽습니다. 중첩되거나 반복되는 데이터를 포함하여 Parquet 파일의 모든 열을 프로젝션합니다.

```sql
SELECT
    *
FROM
    OPENROWSET(
        BULK 'parquet/nested/justSimpleArray.parquet',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT='PARQUET'
    ) AS [r];
```

## <a name="access-elements-from-nested-columns"></a>중첩된 열의 요소에 액세스

다음 쿼리는 *structExample.parquet* 파일을 읽고 중첩 열의 요소를 표시하는 방법을 보여줍니다.

```sql
SELECT
    *
FROM
    OPENROWSET(
        BULK 'parquet/nested/structExample.parquet',
        DATA_SOURCE = 'SqlOnDemandDemo',
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

다음 쿼리는 *justSimpleArray.parquet* 파일을 읽고 [JSON_VALUE](/sql/t-sql/functions/json-value-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)를 사용하여 배열 또는 맵과 같은 반복되는 열 내에서 **스칼라** 요소를 검색합니다.

```sql
SELECT
    *,
    JSON_VALUE(SimpleArray, '$[0]') AS FirstElement,
    JSON_VALUE(SimpleArray, '$[1]') AS SecondElement,
    JSON_VALUE(SimpleArray, '$[2]') AS ThirdElement
FROM
    OPENROWSET(
        BULK 'parquet/nested/justSimpleArray.parquet',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT='PARQUET'
    ) AS [r];
```

다음 쿼리는 *mapExample.parquet* 파일을 읽고 [JSON_QUERY](/sql/t-sql/functions/json-query-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)를 사용하여 배열 또는 맵과 같은 반복되는 열 내에서 **비스칼라** 요소를 검색합니다.

```sql
SELECT
    MapOfPersons,
    JSON_QUERY(MapOfPersons, '$."John Doe"') AS [John]
FROM
    OPENROWSET(
        BULK 'parquet/nested/mapExample.parquet',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT='PARQUET'
    ) AS [r];
```

## <a name="next-steps"></a>다음 단계

다음 문서에서는 [JSON 파일을 쿼리](query-json-files.md)하는 방법을 보여줍니다.
