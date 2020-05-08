---
title: SQL 주문형 (미리 보기)을 사용 하 여 JSON 파일 쿼리
description: 이 섹션에서는 Azure Synapse Analytics에서 SQL 주문형을 사용 하 여 JSON 파일을 읽는 방법을 설명 합니다.
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: ''
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: 0db022ff4fdf092c2286d2da333df2ea23406569
ms.sourcegitcommit: 366e95d58d5311ca4b62e6d0b2b47549e06a0d6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/01/2020
ms.locfileid: "82692710"
---
# <a name="query-json-files-using-sql-on-demand-preview-in-azure-synapse-analytics"></a>Azure Synapse Analytics에서 SQL 주문형 (미리 보기)를 사용 하 여 JSON 파일 쿼리

이 문서에서는 Azure Synapse Analytics에서 SQL 주문형 (미리 보기)를 사용 하 여 쿼리를 작성 하는 방법에 대해 알아봅니다. 쿼리의 목표는 JSON 파일을 읽는 것입니다.

## <a name="prerequisites"></a>사전 요구 사항

이 문서의 나머지 부분을 읽기 전에 다음 문서를 검토 하세요.

- [처음 설정](query-data-storage.md#first-time-setup)
- [필수 구성 요소](query-data-storage.md#prerequisites)

## <a name="sample-json-files"></a>샘플 JSON 파일

아래 섹션에는 JSON 파일을 읽는 샘플 스크립트가 포함 되어 있습니다. 파일은 *json* 컨테이너 및 폴더에 저장 되 고 다음과 같은 구조를 포함 하는 단일 서적 *항목을 포함*합니다.

```json
{
   "_id":"ahokw88",
   "type":"Book",
   "title":"The AWK Programming Language",
   "year":"1988",
   "publisher":"Addison-Wesley",
   "authors":[
      "Alfred V. Aho",
      "Brian W. Kernighan",
      "Peter J. Weinberger"
   ],
   "source":"DBLP"
}
```

## <a name="read-json-files"></a>JSON 파일 읽기

JSON_VALUE 및 [JSON_QUERY](/sql/t-sql/functions/json-query-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)를 사용 하 여 json 파일을 처리 하려면 json 파일을 저장소에서 단일 열로 읽어야 합니다. 다음 스크립트는 단일 열로 *book1.xlsx* 파일을 읽습니다.

```sql
SELECT
    *
FROM
    OPENROWSET(
        BULK 'https://sqlondemandstorage.blob.core.windows.net/json/books/book1.json',
        FORMAT='CSV',
        FIELDTERMINATOR ='0x0b',
        FIELDQUOTE = '0x0b',
        ROWTERMINATOR = '0x0b'
    )
    WITH (
        jsonContent varchar(8000)
    ) AS [r];
```

> [!NOTE]
> 전체 JSON 파일을 단일 행 또는 열로 읽습니다. 따라서 FIELDTERMINATOR, FIELDTERMINATOR 및 ROWTERMINATOR는 0x0b로 설정 됩니다.

## <a name="query-json-files-using-json_value"></a>JSON_VALUE를 사용 하 여 JSON 파일 쿼리

아래 쿼리에서는 [JSON_VALUE](/sql/t-sql/functions/json-value-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) 을 사용 하 여 *Cryptology의 책 확률 및 통계 방법에서 선택한 문서에 대 한 소개*로 스칼라 값 (제목, 게시자)을 검색 하는 방법을 보여 줍니다.

```sql
SELECT
    JSON_VALUE(jsonContent, '$.title') AS title,
    JSON_VALUE(jsonContent, '$.publisher') as publisher,
    jsonContent
FROM
    OPENROWSET(
        BULK 'https://sqlondemandstorage.blob.core.windows.net/json/books/*.json',
        FORMAT='CSV',
        FIELDTERMINATOR ='0x0b',
        FIELDQUOTE = '0x0b',
        ROWTERMINATOR = '0x0b'
    )
    WITH (
        jsonContent varchar(8000)
    ) AS [r]
WHERE
    JSON_VALUE(jsonContent, '$.title') = 'Probabilistic and Statistical Methods in Cryptology, An Introduction by Selected Topics';
```

## <a name="query-json-files-using-json_query"></a>JSON_QUERY를 사용 하 여 JSON 파일 쿼리

다음 쿼리는 [JSON_QUERY](/sql/t-sql/functions/json-query-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) 을 사용 하 여 *Cryptology의 확률 및 통계 방법에서 선택한 항목을 소개*하는 개체 및 배열 (작성자)을 검색 하는 방법을 보여 줍니다.

```sql
SELECT
    JSON_QUERY(jsonContent, '$.authors') AS authors,
    jsonContent
FROM
    OPENROWSET(
        BULK 'https://sqlondemandstorage.blob.core.windows.net/json/books/*.json',
        FORMAT='CSV',
        FIELDTERMINATOR ='0x0b',
        FIELDQUOTE = '0x0b',
        ROWTERMINATOR = '0x0b'
    )
    WITH (
        jsonContent varchar(8000)
    ) AS [r]
WHERE
    JSON_VALUE(jsonContent, '$.title') = 'Probabilistic and Statistical Methods in Cryptology, An Introduction by Selected Topics';
```

## <a name="query-json-files-using-openjson"></a>OPENJSON를 사용 하 여 JSON 파일 쿼리

다음 쿼리에서는 [OPENJSON](/sql/t-sql/functions/openjson-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)를 사용 합니다. *Cryptology의 확률 및 통계 메서드를 소개*하는 서적 내에서 개체와 속성을 검색 하 고, 선택 된 문서를 소개 합니다.

```sql
SELECT
    j.*
FROM
    OPENROWSET(
        BULK 'https://sqlondemandstorage.blob.core.windows.net/json/books/*.json',
        FORMAT='CSV',
        FIELDTERMINATOR ='0x0b',
        FIELDQUOTE = '0x0b',
        ROWTERMINATOR = '0x0b'
    )
    WITH (
        jsonContent NVARCHAR(max) -- Use appropriate length. Make sure JSON fits. 
    ) AS [r]
CROSS APPLY OPENJSON(jsonContent) AS j
WHERE
    JSON_VALUE(jsonContent, '$.title') = 'Probabilistic and Statistical Methods in Cryptology, An Introduction by Selected Topics';
```

## <a name="next-steps"></a>다음 단계

이 시리즈의 다음 문서에서는 다음을 수행 하는 방법을 보여 줍니다.

- [폴더 및 여러 파일 쿼리](query-folders-multiple-csv-files.md)
- [보기 만들기 및 사용](create-use-views.md)
