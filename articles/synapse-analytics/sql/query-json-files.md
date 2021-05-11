---
title: 서버리스 SQL 풀을 사용하여 JSON 파일 쿼리
description: 이 섹션에서는 Azure Synapse Analytics에서 서버리스 SQL 풀을 사용하여 JSON 파일을 읽는 방법을 설명합니다.
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: sql
ms.date: 05/20/2020
ms.author: stefanazaric
ms.reviewer: jrasnick
ms.openlocfilehash: 5fcf688bbe8a5be2fc10b70950990b7b6ca71df8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103225594"
---
# <a name="query-json-files-using-serverless-sql-pool-in-azure-synapse-analytics"></a>Azure Synapse Analytics에서 서버리스 SQL 풀을 사용하여 JSON 파일 쿼리

이 문서에서는 Azure Synapse Analytics에서 서버리스 SQL 풀을 사용하여 쿼리를 작성하는 방법을 알아봅니다. 쿼리의 목표는 [OPENROWSET](develop-openrowset.md)를 사용하여 JSON 파일을 읽는 것입니다. 
- 여러 JSON 문서가 JSON 배열로 저장되는 표준 JSON 파일입니다.
- JSON 문서가 줄 바꿈 문자로 구분되는 줄 구분 JSON 파일입니다. 이러한 파일 형식에 대한 일반적인 확장명은 `jsonl`, `ldjson`, `ndjson`입니다.

## <a name="read-json-documents"></a>JSON 문서 읽기

JSON 파일의 콘텐츠를 확인하는 가장 쉬운 방법은 `OPENROWSET` 함수에 파일 URL을 제공하고, csv `FORMAT`을 지정하고, `fieldterminator` 및 `fieldquote`에 대해 `0x0b` 값을 설정하는 것입니다. 줄로 구분된 JSON 파일을 읽어야 하는 경우에는 이것으로 충분합니다. 클래식 JSON 파일이 있는 경우 `rowterminator`에 `0x0b` 값을 설정해야 합니다. `OPENROWSET` 함수는 JSON을 구문 분석하고 모든 문서를 다음 형식으로 반환합니다.

| doc |
| --- |
|{"date_rep":"2020-07-24","day":24,"month":7,"year":2020,"cases":3,"deaths":0,"geo_id":"AF"}|
|{"date_rep":"2020-07-25","day":25,"month":7,"year":2020,"cases":7,"deaths":0,"geo_id":"AF"}|
|{"date_rep":"2020-07-26","day":26,"month":7,"year":2020,"cases":4,"deaths":0,"geo_id":"AF"}|
|{"date_rep":"2020-07-27","day":27,"month":7,"year":2020,"cases":8,"deaths":0,"geo_id":"AF"}|

파일을 공개적으로 사용할 수 있거나 Azure AD ID로 이 파일에 액세스할 수 있는 경우 다음 예제와 같은 쿼리를 사용하여 파일의 내용을 확인해야 합니다.

### <a name="read-json-files"></a>JSON 파일 읽기

다음 샘플 쿼리는 JSON 및 줄로 구분된 JSON 파일을 읽고 모든 문서를 별도의 행으로 반환합니다.

```sql
select top 10 *
from openrowset(
        bulk 'https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/ecdc_cases/latest/ecdc_cases.jsonl',
        format = 'csv',
        fieldterminator ='0x0b',
        fieldquote = '0x0b'
    ) with (doc nvarchar(max)) as rows
go
select top 10 *
from openrowset(
        bulk 'https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/ecdc_cases/latest/ecdc_cases.json',
        format = 'csv',
        fieldterminator ='0x0b',
        fieldquote = '0x0b',
        rowterminator = '0x0b' --> You need to override rowterminator to read classic JSON
    ) with (doc nvarchar(max)) as rows
```

위 샘플 쿼리의 JSON 문서에는 개체의 배열이 포함되어 있습니다. 이 쿼리는 각 개체를 결과 집합에서 별도의 행으로 반환합니다. 이 파일에 액세스할 수 있는지 확인합니다. 파일이 SAS 키 또는 사용자 지정 ID로 보호되는 경우 [SQL 로그인에 대한 서버 수준 자격 증명](develop-storage-files-storage-access-control.md?tabs=shared-access-signature#server-scoped-credential)을 설정해야 합니다. 

### <a name="data-source-usage"></a>데이터 원본 사용

이전 예제에서는 파일에 전체 경로를 사용했습니다. 전체 경로 대신 스토리지의 루트 폴더를 가리키는 위치를 사용하여 외부 데이터 원본을 만들고 해당 데이터 원본 및 `OPENROWSET` 함수에서 파일의 상대 경로를 사용할 수 있습니다.

```sql
create external data source covid
with ( location = 'https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/ecdc_cases' );
go
select top 10 *
from openrowset(
        bulk 'latest/ecdc_cases.jsonl',
        data_source = 'covid',
        format = 'csv',
        fieldterminator ='0x0b',
        fieldquote = '0x0b'
    ) with (doc nvarchar(max)) as rows
go
select top 10 *
from openrowset(
        bulk 'latest/ecdc_cases.json',
        data_source = 'covid',
        format = 'csv',
        fieldterminator ='0x0b',
        fieldquote = '0x0b',
        rowterminator = '0x0b' --> You need to override rowterminator to read classic JSON
    ) with (doc nvarchar(max)) as rows
```

데이터 원본이 SAS 키 또는 사용자 지정 ID를 통해 보호되는 경우, [데이터베이스 범위 자격 증명을 사용하여 데이터 원본](develop-storage-files-storage-access-control.md?tabs=shared-access-signature#database-scoped-credential)을 구성할 수 있습니다.

다음 섹션에서는 다양한 유형의 JSON 파일을 쿼리하는 방법을 볼 수 있습니다.

## <a name="parse-json-documents"></a>JSON 문서 구문 분석

이전 예제에서 쿼리는 결과 집합의 개별 행에서 모든 JSON 문서를 단일 문자열로 반환했습니다. 다음 예제와 같이 `JSON_VALUE` 및 `OPENJSON` 함수를 사용하여 JSON 문서의 값을 구문 분석하고 이를 관계형 값으로 반환할 수 있습니다.

| date\_rep | cases | geo\_id |
| --- | --- | --- |
| 2020-07-24 | 3 | AF |
| 2020-07-25 | 7 | AF |
| 2020-07-26 | 4 | AF |
| 2020-07-27 | 8| AF |

### <a name="sample-json-document"></a>샘플 JSON 문서

쿼리 예제는 다음과 같은 구조를 사용하는 문서를 포함하는 *json* 파일을 읽습니다.

```json
{
    "date_rep":"2020-07-24",
    "day":24,"month":7,"year":2020,
    "cases":13,"deaths":0,
    "countries_and_territories":"Afghanistan",
    "geo_id":"AF",
    "country_territory_code":"AFG",
    "continent_exp":"Asia",
    "load_date":"2020-07-25 00:05:14",
    "iso_country":"AF"
}
```

> [!NOTE]
> 이러한 문서가 줄로 구분된 JSON으로 저장된 경우 `FIELDTERMINATOR` 및 `FIELDQUOTE`를 0x0b로 설정해야 합니다. 표준 JSON 형식이 있는 경우 `ROWTERMINATOR`를 0x0b로 설정해야 합니다.

### <a name="query-json-files-using-json_value"></a>JSON_VALUE를 사용하여 JSON 파일 쿼리

아래 쿼리에서는 [JSON_VALUE](/sql/t-sql/functions/json-value-transact-sql?view=azure-sqldw-latest&preserve-view=true)를 사용하여 JSON 문서에서 스칼라 값(`date_rep`, `countries_and_territories`, `cases`)을 검색하는 방법을 보여줍니다.

```sql
select
    JSON_VALUE(doc, '$.date_rep') AS date_reported,
    JSON_VALUE(doc, '$.countries_and_territories') AS country,
    CAST(JSON_VALUE(doc, '$.deaths') AS INT) as fatal,
    JSON_VALUE(doc, '$.cases') as cases,
    doc
from openrowset(
        bulk 'latest/ecdc_cases.jsonl',
        data_source = 'covid',
        format = 'csv',
        fieldterminator ='0x0b',
        fieldquote = '0x0b'
    ) with (doc nvarchar(max)) as rows
order by JSON_VALUE(doc, '$.geo_id') desc
```

JSON 문서에서 JSON 속성을 추출한 후에는 열 별칭을 정의하고 필요에 따라 텍스트 값을 일부 형식으로 캐스팅할 수 있습니다.

### <a name="query-json-files-using-openjson"></a>OPENJSON을 사용하여 JSON 파일 쿼리

다음 쿼리는 [OPENJSON](/sql/t-sql/functions/openjson-transact-sql?view=azure-sqldw-latest&preserve-view=true)을 사용합니다. 이는 세르비아에 보고된 COVID 통계를 검색합니다.

```sql
select
    *
from openrowset(
        bulk 'latest/ecdc_cases.jsonl',
        data_source = 'covid',
        format = 'csv',
        fieldterminator ='0x0b',
        fieldquote = '0x0b'
    ) with (doc nvarchar(max)) as rows
    cross apply openjson (doc)
        with (  date_rep datetime2,
                cases int,
                fatal int '$.deaths',
                country varchar(100) '$.countries_and_territories')
where country = 'Serbia'
order by country, date_rep desc;
```
결과는 `JSON_VALUE` 함수를 사용하여 반환되는 결과와 기능적으로 동일합니다. 경우에 따라 `OPENJSON`이 `JSON_VALUE`에 비해 이점이 있을 수 있습니다.
- `WITH` 절에서 모든 속성에 대해 열 별칭과 형식을 명시적으로 설정할 수 있습니다. `SELECT` 목록의 모든 열에는 `CAST` 함수를 넣을 필요가 없습니다.
- 많은 수의 속성을 반환하는 경우 `OPENJSON`이 더 빠를 수 있습니다. 1~2개의 속성만 반환하는 경우 `OPENJSON` 함수에서 오버헤드가 발생할 수 있습니다.
- 각 문서에 대해 배열을 구문 분석해야 하는 경우 `OPENJSON` 함수를 사용하고 이를 부모 행에 조인해야 합니다.

## <a name="next-steps"></a>다음 단계

이 시리즈의 다음 문서에서는 다음 작업을 수행하는 방법을 보여 줍니다.

- [폴더 및 여러 파일 쿼리](query-folders-multiple-csv-files.md)
- [보기 만들기 및 사용](create-use-views.md)
