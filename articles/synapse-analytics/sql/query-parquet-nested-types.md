---
title: SQL 주문형(미리 보기)을 사용하여 Parquet 중첩 형식 쿼리
description: 이 문서에서는 Parquet 중첩 형식을 쿼리하는 방법에 대해 알아봅니다.
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: sql
ms.date: 05/20/2020
ms.author: v-stazar
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: 60f2a1992750950b50995fcf36513e44e377004d
ms.sourcegitcommit: 5b8fb60a5ded05c5b7281094d18cf8ae15cb1d55
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/29/2020
ms.locfileid: "87386608"
---
# <a name="query-nested-types-in-parquet-and-json-files-using-sql-on-demand-preview-in-azure-synapse-analytics"></a>Azure Synapse Analytics에서 SQL 주문형 (미리 보기)을 사용 하 여 Parquet 및 JSON 파일의 중첩 형식 쿼리

이 문서에서는 Azure Synapse Analytics에서 SQL 주문형(미리 보기)을 사용하여 쿼리를 작성하는 방법에 대해 알아봅니다. 이 쿼리는 Parquet 중첩 형식을 읽습니다.
중첩 형식은 개체 또는 배열을 나타내는 복잡 한 구조입니다. 중첩 형식은 다음 위치에 저장할 수 있습니다. 
- 배열 및 개체를 포함 하는 여러 개의 복합 열을 [PARQUET](query-parquet-files.md) 수 있습니다.
- 복합 json 문서를 단일 열로 읽을 수 있는 계층적 [json 파일](query-json-files.md) .
- CosmosDB 컬렉션은 모든 문서에 복잡 한 중첩 된 속성 (현재는 제어 된 공개 미리 보기 상태)을 포함할 수 있습니다.

Synapse SQL on demand는 모든 중첩 형식을 JSON 개체 및 배열로 포맷 하므로 [json 함수를 사용 하 여 복잡 한 개체를 추출 하거나 수정](https://docs.microsoft.com/sql/relational-databases/json/validate-query-and-change-json-data-with-built-in-functions-sql-server) 하거나 [OPENJSON 함수를 사용 하 여 json 데이터를 구문 분석할](https://docs.microsoft.com/sql/relational-databases/json/convert-json-data-to-rows-and-columns-with-openjson-sql-server)수 있습니다. 

중첩 된 개체를 사용 하 여 [Covid-19 Open Research 데이터 집합](https://azure.microsoft.com/services/open-datasets/catalog/covid-19-open-research/) JSON 파일에서 스칼라 및 개체 값을 추출 하는 쿼리의 한 가지 예는 다음과 같습니다. 

```sql
SELECT
    title = JSON_VALUE(doc, '$.metadata.title'),
    first_author = JSON_QUERY(doc, '$.metadata.authors[0]'),
    first_author_name = JSON_VALUE(doc, '$.metadata.authors[0].first'),
    complex_object = doc
FROM
    OPENROWSET(
        BULK 'https://azureopendatastorage.blob.core.windows.net/covid19temp/comm_use_subset/pdf_json/000b7d1517ceebb34e1e3e817695b6de03e2fa78.json',
        FORMAT='CSV', FIELDTERMINATOR ='0x0b', FIELDQUOTE = '0x0b', ROWTERMINATOR = '0x0b'
    )
    WITH ( doc varchar(MAX) ) AS docs;
```

`JSON_VALUE`함수는 지정 된 경로에 있는 필드에서 스칼라 값을 반환 합니다. `JSON_QUERY`함수는 지정 된 경로에 있는 필드에서 JSON으로 형식이 지정 된 개체를 반환 합니다.

> [!IMPORTANT]
> 이 예에서는 [Covid-19 Open Research 데이터 집합](https://azure.microsoft.com/services/open-datasets/catalog/covid-19-open-research/)의 파일을 사용 합니다. 이 페이지에서 해당 라이선스 및 데이터 구조를 참조 하세요.

## <a name="prerequisites"></a>사전 요구 사항

첫 번째 단계는 참조하는 데이터 원본을 통해 **데이터베이스를 만드는** 것입니다. 그런 다음, 해당 데이터베이스에서 [설치 스크립트](https://github.com/Azure-Samples/Synapse/blob/master/SQL/Samples/LdwSample/SampleDB.sql)를 실행하여 개체를 초기화합니다. 이 설치 스크립트는 이러한 샘플에서 사용되는 데이터 원본, 데이터베이스 범위 자격 증명 및 외부 파일 형식을 만듭니다.

## <a name="project-nested-or-repeated-data"></a>중첩되거나 반복되는 데이터 프로젝션

PARQUET 파일에는 복합 형식의 열이 여러 개 있을 수 있습니다. 이러한 열의 값은 JSON 텍스트로 형식이 지정 되 고 VARCHAR 열로 반환 됩니다. 다음 쿼리는 *structExample* 파일을 읽고 중첩 열의 값을 읽는 방법을 보여 줍니다. 

```sql
SELECT
    DateStruct, TimeStruct, TimestampStruct, DecimalStruct, FloatStruct
FROM
    OPENROWSET(
        BULK 'parquet/nested/structExample.parquet',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT='PARQUET'
    )
    WITH (
        DateStruct VARCHAR(8000),
        TimeStruct VARCHAR(8000),
        TimestampStruct VARCHAR(8000),
        DecimalStruct VARCHAR(8000),
        FloatStruct VARCHAR(8000)
    ) AS [r];
```

이 쿼리는 모든 중첩 된 개체의 내용이 JSON 텍스트로 반환 되는 다음 결과를 반환 합니다.

| DateStruct    | TimeStruct    | TimestampStruct   | DecimalStruct | FloatStruct |
| --- | --- | --- | --- | --- |
|{"Date": "2009-04-25"}| {"Time": "20:51:54.3598000"}|    {"Timestamp": "5501-04-08 12:13:57.4821000"}|    {"Decimal": 11143412.25350}| {"Float": 0.5}|
|{"Date": "1916-04-29"}| {"Time": "00:16:04.6778000"}|    {"Timestamp": "1990-06-30 20:50:52.6828000"}|    {"Decimal": 1963545.62800}|  {"Float":-2.125}|

다음 쿼리는 *justSimpleArray.parquet* 파일을 읽습니다. 중첩되거나 반복되는 데이터를 포함하여 Parquet 파일의 모든 열을 프로젝션합니다.

```sql
SELECT
    SimpleArray
FROM
    OPENROWSET(
        BULK 'parquet/nested/justSimpleArray.parquet',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT='PARQUET'
    ) AS [r];
```

이 쿼리는 다음 결과를 반환 합니다.

|SimpleArray|
| --- |
|[11, 12, 13]|
|[21, 22, 23]|

## <a name="read-properties-from-nested-object-columns"></a>중첩 된 개체 열에서 속성 읽기

`JSON_VALUE`함수를 사용 하면 JSON 텍스트로 형식이 지정 된 열에서 값을 반환할 수 있습니다.

```sql
SELECT
    title = JSON_VALUE(complex_column, '$.metadata.title'),
    first_author_name = JSON_VALUE(complex_column, '$.metadata.authors[0].first'),
    body_text = JSON_VALUE(complex_column, '$.body_text.text'),
    complex_column
FROM
    OPENROWSET( BULK 'https://azureopendatastorage.blob.core.windows.net/covid19temp/comm_use_subset/pdf_json/000b7d1517ceebb34e1e3e817695b6de03e2fa78.json',
                FORMAT='CSV', FIELDTERMINATOR ='0x0b', FIELDQUOTE = '0x0b', ROWTERMINATOR = '0x0b' ) WITH ( complex_column varchar(MAX) ) AS docs;
```

결과는 다음 표에 나와 있습니다.

|title  | first_author_name | body_text | complex_column |
| --- | --- | --- | --- |
| Epidemiolo에 대 한 보충 정보 ... | Julien   | -그림 S1: Phylogeny ... | `{    "paper_id": "000b7d1517ceebb34e1e3e817695b6de03e2fa78",    "metadata": {        "title": "Supplementary Information An eco-epidemiological study of Morbilli-related paramyxovirus infection in Madagascar bats reveals host-switching as the dominant macro-evolutionary mechanism",        "authors": [            {                "first": "Julien"` |

대부분의 경우에는 복잡 한 JSON 개체를 포함 하는 단일 열을 반환 하는 JSON 파일과는 다릅니다. PARQUET 파일은 여러 가지 복잡 해질 수 있습니다. 각 열에서 함수를 사용 하 여 중첩 열의 속성을 읽을 수 있습니다 `JSON_VALUE` . `OPENROWSET`절에서 중첩 된 속성의 경로를 직접 지정할 수 있습니다 `WITH` . 경로를 열 이름으로 설정 하거나 열 유형 뒤에 [JSON 경로 식을](https://docs.microsoft.com/sql/relational-databases/json/json-path-expressions-sql-server) 추가할 수 있습니다.

다음 쿼리는 *structExample* 파일을 읽고 중첩 열의 요소를 표시 하는 방법을 보여 줍니다. 중첩 된 값을 참조 하는 방법에는 두 가지가 있습니다.
- 형식 지정 뒤에 중첩 된 값 경로 식을 지정 합니다.
- 필드를 참조 하려면 "."를 사용 하 여 열 이름을 중첩 경로로 서식 지정 합니다.

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
        [DateValue] DATE '$.DateStruct.Date',
        [TimeStruct.Time] TIME,
        [TimestampStruct.Timestamp] DATETIME2,
        DecimalValue DECIMAL(18, 5) '$.DecimalStruct.Decimal',
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

결과는 다음 표에 나와 있습니다.

|SimpleArray    | FirstElement  | SecondElement | ThirdElement |
| --- | --- | --- | --- |
| [11, 12, 13] | 11   | 12 | 13 |
| [21, 22, 23] | 21   | 22 | 23 |

## <a name="access-sub-objects-from-complex-columns"></a>복합 열에서 하위 개체 액세스

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

절에서 반환 하려는 열을 명시적으로 참조할 수도 있습니다 `WITH` .

```sql
SELECT DocId,
    MapOfPersons,
    JSON_QUERY(MapOfPersons, '$."John Doe"') AS [John]
FROM
    OPENROWSET(
        BULK 'parquet/nested/mapExample.parquet',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT='PARQUET'
    ) 
    WITH (DocId bigint, MapOfPersons VARCHAR(max)) AS [r];
```

구조는 `MapOfPersons` 열로 반환 되 `VARCHAR` 고 JSON 문자열로 형식이 지정 됩니다.

## <a name="projecting-values-from-repeated-columns"></a>반복 되는 열의 값 프로젝션

일부 열에 스칼라 값의 배열 (예:)이 있는 경우 `[1,2,3]` 쉽게 확장 하 고 다음 스크립트를 사용 하 여 주 행과 조인할 수 있습니다.

```sql
SELECT
    SimpleArray, Element
FROM
    OPENROWSET(
        BULK 'parquet/nested/justSimpleArray.parquet',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT='PARQUET'
    ) AS arrays
    CROSS APPLY OPENJSON (SimpleArray) WITH (Element int '$') as array_values
```

## <a name="next-steps"></a>다음 단계

다음 문서에서는 [JSON 파일을 쿼리](query-json-files.md)하는 방법을 보여줍니다.
