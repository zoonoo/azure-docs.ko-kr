---
title: SQL 주문형(미리 보기)을 사용하여 JSON 파일 쿼리
description: 이 섹션에서는 Azure Synapse Analytics에서 SQL 주문형을 사용하여 JSON 파일을 읽는 방법을 설명합니다.
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: ''
ms.date: 05/20/2020
ms.author: v-stazar
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: 7a8c9083ecbadbf63cf0ac65dc1803b478e939fe
ms.sourcegitcommit: 64fc70f6c145e14d605db0c2a0f407b72401f5eb
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/27/2020
ms.locfileid: "83873399"
---
# <a name="query-json-files-using-sql-on-demand-preview-in-azure-synapse-analytics"></a>Azure Synapse Analytics에서 SQL 주문형(미리 보기)을 사용하여 JSON 파일 쿼리

이 문서에서는 Azure Synapse Analytics에서 SQL 주문형(미리 보기)을 사용하여 쿼리를 작성하는 방법을 알아봅니다. 쿼리의 목표는 JSON 파일을 읽는 것입니다. 지원되는 형식은 [OPENROWSET](develop-openrowset.md)에 나열되어 있습니다.

## <a name="prerequisites"></a>사전 요구 사항

첫 번째 단계는 쿼리를 실행할 **데이터베이스 만들기**입니다. 그런 다음, 해당 데이터베이스에서 [설치 스크립트](https://github.com/Azure-Samples/Synapse/blob/master/SQL/Samples/LdwSample/SampleDB.sql)를 실행하여 개체를 초기화합니다. 이 설치 스크립트는 이러한 샘플에서 사용되는 데이터 원본, 데이터베이스 범위 자격 증명 및 외부 파일 형식을 만듭니다.

## <a name="sample-json-files"></a>샘플 JSON 파일

아래 섹션에는 JSON 파일을 읽는 샘플 스크립트가 포함되어 있습니다. 파일은 *json* 컨테이너, *books* 폴더에 저장되며, 다음과 같은 구조의 단일 서적 항목을 포함합니다.

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

JSON_VALUE 및 [JSON_QUERY](/sql/t-sql/functions/json-query-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)를 사용하여 JSON 파일을 처리하려면 JSON 파일을 스토리지에서 단일 열로 읽어야 합니다. 다음 스크립트는 *book1.xlsx* 파일을 단일 열로 읽습니다.

```sql
SELECT
    *
FROM
    OPENROWSET(
        BULK 'json/books/book1.json',
        DATA_SOURCE = 'SqlOnDemandDemo',
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
> 전체 JSON 파일을 단일 행 또는 열로 읽고 있습니다. 따라서 FIELDTERMINATOR, FIELDQUOTE 및 ROWTERMINATOR는 0x0b로 설정됩니다.

## <a name="query-json-files-using-json_value"></a>JSON_VALUE를 사용하여 JSON 파일 쿼리

다음 쿼리는 [JSON_VALUE](/sql/t-sql/functions/json-value-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)를 사용하여 *Probabilistic and Statistical Methods in Cryptology, An Introduction by Selected Topics*라는 제목의 서적에서 스칼라 값(제목, 출판사)을 검색하는 방법을 보여줍니다.

```sql
SELECT
    JSON_VALUE(jsonContent, '$.title') AS title,
    JSON_VALUE(jsonContent, '$.publisher') as publisher,
    jsonContent
FROM
    OPENROWSET(
        BULK 'json/books/*.json',
        DATA_SOURCE = 'SqlOnDemandDemo',
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

## <a name="query-json-files-using-json_query"></a>JSON_QUERY를 사용하여 JSON 파일 쿼리

다음 쿼리는 [JSON_QUERY](/sql/t-sql/functions/json-query-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)를 사용하여 *Probabilistic and Statistical Methods in Cryptology, An Introduction by Selected Topics*라는 제목의 서적에서 개체 및 배열(저자)을 검색합니다.

```sql
SELECT
    JSON_QUERY(jsonContent, '$.authors') AS authors,
    jsonContent
FROM
    OPENROWSET(
        BULK 'json/books/*.json',
        DATA_SOURCE = 'SqlOnDemandDemo',
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

다음 쿼리는 [OPENJSON](/sql/t-sql/functions/openjson-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)을 사용합니다. 이 쿼리는 *Probabilistic and Statistical Methods in Cryptology, An Introduction by Selected Topics*라는 제목의 서적에서 개체 및 속성을 검색합니다.

```sql
SELECT
    j.*
FROM
    OPENROWSET(
        BULK 'json/books/*.json',
        DATA_SOURCE = 'SqlOnDemandDemo',
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

이 시리즈의 다음 문서에서는 다음 작업을 수행하는 방법을 보여 줍니다.

- [폴더 및 여러 파일 쿼리](query-folders-multiple-csv-files.md)
- [보기 만들기 및 사용](create-use-views.md)
