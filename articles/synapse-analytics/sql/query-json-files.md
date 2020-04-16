---
title: SQL 온디맨드(미리 보기)를 사용하여 JSON 파일 쿼리
description: 이 섹션에서는 Azure Synapse Analytics에서 주문형 SQL을 사용하여 JSON 파일을 읽는 방법을 설명합니다.
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: ''
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: 1054328216d0517b3f450ba4fe08f3bef32d68f7
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81431723"
---
# <a name="query-json-files-using-sql-on-demand-preview-in-azure-synapse-analytics"></a>Azure 시냅스 분석에서 SQL 온디맨드(미리 보기)를 사용하여 JSON 파일 쿼리

이 문서에서는 Azure Synapse Analytics에서 SQL 주문형(미리 보기)을 사용하여 쿼리를 작성하는 방법을 알아봅니다. 쿼리의 목적은 JSON 파일을 읽는 것입니다.

## <a name="prerequisites"></a>사전 요구 사항

이 문서의 나머지 부분을 읽기 전에 다음 문서를 검토하십시오.

- [처음 설정](query-data-storage.md#first-time-setup)
- [필수 구성 요소](query-data-storage.md#prerequisites)

## <a name="sample-json-files"></a>샘플 JSON 파일

아래 섹션에는 JSON 파일을 읽을 수 있는 샘플 스크립트가 포함되어 있습니다. 파일은 *json* 컨테이너, 폴더 *책에*저장되며 다음과 같은 구조의 단일 책 항목을 포함합니다.

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

JSON_VALUE [JSON_QUERY](/sql/t-sql/functions/json-query-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)사용하여 JSON 파일을 처리하려면 저장소에서 JSON 파일을 단일 열로 읽어야합니다. 다음 스크립트는 *book1.json* 파일을 단일 열로 읽습니다.

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
> 전체 JSON 파일을 단일 행 또는 열로 읽고 있습니다. 따라서 필드 터미네이터, 필드 인용 및 ROWTERMINATOR는 0x0b로 설정됩니다.

## <a name="query-json-files-using-json_value"></a>JSON_VALUE 사용하여 JSON 파일 쿼리

아래 쿼리는 [JSON_VALUE](/sql/t-sql/functions/json-value-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) 사용하여 *암호화의 확률적 및 통계 적 방법이라는*제목의 책에서 스칼라 값 (제목, 출판사)을 검색하는 방법을 보여 주며 선택한 기사에 의한 소개 :

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

## <a name="query-json-files-using-json_query"></a>JSON_QUERY 사용하여 JSON 파일 쿼리

다음 쿼리는 [JSON_QUERY](/sql/t-sql/functions/json-query-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) 사용하여 *암호화의 확률및 통계 적 방법이라는*제목의 책에서 개체 및 배열 (저자)을 검색하는 방법을 보여 주며 선택한 주제에 의한 소개 :

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

## <a name="query-json-files-using-openjson"></a>OPENJSON을 사용하여 JSON 파일 쿼리

다음 쿼리에서는 [OPENJSON](/sql/t-sql/functions/openjson-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)을 사용합니다. 그것은 *암호학의 확률적 및 통계 적 방법, 선택한 기사에 의한 소개라는*제목의 책 내에서 개체 및 속성을 검색합니다.

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
        jsonContent NVARCHAR(4000) --Note that you have to use NVARCHAR(4000) for OPENJSON to work.
    ) AS [r]
CROSS APPLY OPENJSON(jsonContent) AS j
WHERE
    JSON_VALUE(jsonContent, '$.title') = 'Probabilistic and Statistical Methods in Cryptology, An Introduction by Selected Topics';
```

## <a name="next-steps"></a>다음 단계

이 시리즈의 다음 문서에서는 다음 방법을 보여 줍니다.

- [폴더 및 여러 파일 쿼리](query-folders-multiple-csv-files.md)
- [뷰를 만들고 사용합니다.](create-use-views.md)
