---
title: SQL 주문형(미리 보기)을 사용하여 JSON 파일 쿼리
description: 이 섹션에서는 Azure Synapse Analytics에서 SQL 주문형을 사용하여 JSON 파일을 읽는 방법을 설명합니다.
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: sql
ms.date: 05/20/2020
ms.author: v-stazar
ms.reviewer: jrasnick
ms.openlocfilehash: 86ed3f005788627166c65b30398279f04388546c
ms.sourcegitcommit: 50802bffd56155f3b01bfb4ed009b70045131750
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91930852"
---
# <a name="query-json-files-using-sql-on-demand-preview-in-azure-synapse-analytics"></a>Azure Synapse Analytics에서 SQL 주문형(미리 보기)을 사용하여 JSON 파일 쿼리

이 문서에서는 Azure Synapse Analytics에서 SQL 주문형(미리 보기)을 사용하여 쿼리를 작성하는 방법을 알아봅니다. 쿼리의 목표는 [OPENROWSET](develop-openrowset.md)을 사용 하 여 JSON 파일을 읽는 것입니다. 
- 여러 JSON 문서가 JSON 배열로 저장 되는 표준 JSON 파일입니다.
- 줄로 구분 된 JSON 파일. JSON 문서는 줄 바꿈 문자로 구분 됩니다. 이러한 파일 형식에 대 한 일반적인 확장은 `jsonl` , `ldjson` 및 `ndjson` 입니다.

## <a name="read-json-documents"></a>JSON 문서 읽기

JSON 파일의 콘텐츠를 확인 하는 가장 쉬운 방법은 함수에 파일 URL을 제공 하 고 `OPENROWSET` csv를 지정 하 `FORMAT` 고 `0x0b` 및에 대 한 값을 설정 하 `fieldterminator` 는 것입니다 `fieldquote` . 줄로 구분 된 JSON 파일을 읽어야 하는 경우에는 이것으로 충분 합니다. 클래식 JSON 파일이 있는 경우에는에 대 한 값을 설정 `0x0b` 해야 `rowterminator` 합니다. `OPENROWSET` 함수는 JSON을 구문 분석 하 고 모든 문서를 다음 형식으로 반환 합니다.

| doc |
| --- |
|{"date_rep": "2020-07-24", "day": 24, "month": 7, "year": 2020, "case": 3, "deaths": 0, "geo_id": "AF"}|
|{"date_rep": "2020-07-25", "day": 25, "month": 7, "year": 2020, "case": 7, "deaths": 0, "geo_id": "AF"}|
|{"date_rep": "2020-07-26", "day": 26, "month": 7, "year": 2020, "case": 4, "deaths": 0, "geo_id": "AF"}|
|{"date_rep": "2020-07-27", "day": 27, "month": 7, "year": 2020, "case": 8, "deaths": 0, "geo_id": "AF"}|

파일을 공개적으로 사용할 수 있는 경우 또는 Azure AD id가이 파일에 액세스할 수 있는 경우 다음 예제와 같은 쿼리를 사용 하 여 파일의 내용이 표시 되어야 합니다.

### <a name="read-json-files"></a>JSON 파일 읽기

다음 샘플 쿼리는 JSON 및 줄로 구분 된 JSON 파일을 읽고 모든 문서를 별도의 행으로 반환 합니다.

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

위의 샘플 쿼리의 JSON 문서에는 개체의 배열이 포함 되어 있습니다. 이 쿼리는 각 개체를 결과 집합에서 별도의 행으로 반환 합니다. 이 파일에 액세스할 수 있는지 확인 합니다. 파일이 SAS 키 또는 사용자 지정 id를 사용 하 여 보호 되는 경우 [sql 로그인에 대 한 서버 수준 자격 증명](develop-storage-files-storage-access-control.md?tabs=shared-access-signature#server-scoped-credential)을 설정 해야 합니다. 

### <a name="data-source-usage"></a>데이터 원본 사용

이전 예에서는 파일에 대 한 전체 경로를 사용 합니다. 또는 저장소의 루트 폴더를 가리키는 위치를 사용 하 여 외부 데이터 원본을 만들고 해당 데이터 원본 및 함수에 있는 파일에 대 한 상대 경로를 사용할 수 있습니다 `OPENROWSET` .

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

데이터 원본이 SAS 키 또는 사용자 지정 id를 사용 하 여 보호 되는 경우 [데이터베이스 범위 자격 증명을 사용 하 여 데이터 원본을](develop-storage-files-storage-access-control.md?tabs=shared-access-signature#database-scoped-credential)구성할 수 있습니다.

다음 섹션에서는 다양 한 유형의 JSON 파일을 쿼리 하는 방법을 볼 수 있습니다.

## <a name="parse-json-documents"></a>JSON 문서 구문 분석

이전 예의 쿼리는 결과 집합의 개별 행에서 모든 JSON 문서를 단일 문자열로 반환 합니다. 함수 및를 사용 `JSON_VALUE` `OPENJSON` 하 여 JSON 문서의 값을 구문 분석 하 고 다음 예제와 같이 관계형 값으로 반환할 수 있습니다.

| 날짜 \_ 담당자 | cases | 지역 \_ id |
| --- | --- | --- |
| 2020-07-24 | 3 | AF |
| 2020-07-25 | 7 | AF |
| 2020-07-26 | 4 | AF |
| 2020-07-27 | 8| AF |

### <a name="sample-json-document"></a>샘플 JSON 문서

쿼리 예제는 다음과 같은 구조를 사용 하 여 문서를 포함 하는 *json* 파일을 읽습니다.

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
> 이러한 문서가 줄로 구분 된 JSON으로 저장 된 경우 `FIELDTERMINATOR` 및 `FIELDQUOTE` 를 0x0b로 설정 해야 합니다. 표준 JSON 형식이 있는 경우 `ROWTERMINATOR` 를 0x0b로 설정 해야 합니다.

### <a name="query-json-files-using-json_value"></a>JSON_VALUE를 사용하여 JSON 파일 쿼리

다음 쿼리는 [JSON_VALUE](/sql/t-sql/functions/json-value-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) 을 사용 하 여 JSON 문서에서 스칼라 값 (제목, 게시자)을 검색 하는 방법을 보여 줍니다.

```sql
select
    JSON_VALUE(doc, '$.date_rep') AS date_reported,
    JSON_VALUE(doc, '$.countries_and_territories') AS country,
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

### <a name="query-json-files-using-openjson"></a>OPENJSON을 사용하여 JSON 파일 쿼리

다음 쿼리는 [OPENJSON](/sql/t-sql/functions/openjson-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)을 사용합니다. 세르비아에서 보고 되는 COVID 통계를 검색 합니다.

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

## <a name="next-steps"></a>다음 단계

이 시리즈의 다음 문서에서는 다음 작업을 수행하는 방법을 보여 줍니다.

- [폴더 및 여러 파일 쿼리](query-folders-multiple-csv-files.md)
- [보기 만들기 및 사용](create-use-views.md)
