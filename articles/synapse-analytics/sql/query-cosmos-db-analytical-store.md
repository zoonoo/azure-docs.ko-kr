---
title: Azure Synapse Link에서 서버 리스 SQL 풀을 사용 하 여 Azure Cosmos DB 데이터 쿼리 (미리 보기)
description: 이 문서에서는 Azure Synapse Link (미리 보기)에서 SQL 주문형을 사용 하 여 Azure Cosmos DB를 쿼리 하는 방법에 대해 알아봅니다.
services: synapse analytics
author: jovanpop-msft
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: sql
ms.date: 09/15/2020
ms.author: jovanpop
ms.reviewer: jrasnick
ms.openlocfilehash: c5fa326fa05a34ae5b51054b867a766489b85c16
ms.sourcegitcommit: 4cb89d880be26a2a4531fedcc59317471fe729cd
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/27/2020
ms.locfileid: "92670704"
---
# <a name="query-azure-cosmos-db-data-with-serverless-sql-pool-in-azure-synapse-link-preview"></a>Azure Synapse Link에서 서버를 사용 하지 않는 SQL 풀을 사용 하 여 Azure Cosmos DB 데이터 쿼리 (미리 보기)

Synapse 서버를 사용 하지 않는 SQL 풀 (이전에는 SQL 요청 시)을 사용 하 여 트랜잭션 워크 로드의 성능에 영향을 주지 않고 [Azure Synapse Link](../../cosmos-db/synapse-link.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) 로 설정 된 Azure Cosmos DB 컨테이너의 데이터를 거의 실시간으로 분석할 수 있습니다. [분석 저장소](../../cosmos-db/analytical-store-introduction.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) 에서 데이터를 쿼리 하는 친숙 한 T-sql 구문과 t-sql 인터페이스를 통한 광범위 한 BI 및 임시 쿼리 도구에 대 한 통합 연결을 제공 합니다.

Azure Cosmos DB를 쿼리 하는 경우 대부분의 [SQL 함수 및 연산자](overview-features.md)를 포함 하 여 전체 [선택](/sql/t-sql/queries/select-transact-sql?view=sql-server-ver15) 노출 영역이 [OPENROWSET](develop-openrowset.md) 함수를 통해 지원 됩니다. Azure Blob Storage Azure Data Lake Storage 또는 [create external table as select](develop-tables-cetas.md#cetas-in-sql-on-demand)를 사용 하 여 데이터와 함께 Azure Cosmos DB에서 데이터를 읽는 쿼리 결과를 저장할 수도 있습니다. 현재 [CETAS](develop-tables-cetas.md#cetas-in-sql-on-demand)를 사용 하 여 Azure Cosmos DB에 서버 리스 SQL 풀 쿼리 결과를 저장할 수 없습니다.

이 문서에서는 Synapse 링크를 사용 하는 Azure Cosmos DB 컨테이너에서 데이터를 쿼리 하는 서버를 사용 하지 않는 SQL 풀로 쿼리를 작성 하는 방법을 배웁니다. 그런 다음 Azure Cosmos DB 컨테이너를 통해 서버 리스 SQL 풀 뷰를 빌드하고 [이](./tutorial-data-analyst.md) 자습서의 Power BI 모델에 연결 하는 방법에 대해 자세히 알아볼 수 있습니다. 

> [!IMPORTANT]
> 이 자습서에서는 [Azure Cosmos DB 잘 정의 된 스키마](../../cosmos-db/analytical-store-introduction.md#schema-representation) 와 함께 컨테이너를 사용 하 여 나중에 지원 되는 쿼리 환경을 제공 합니다. 서버를 사용 하지 않는 SQL 풀에서 [Azure Cosmos DB 전체 충실도 스키마](#full-fidelity-schema) 를 제공 하는 쿼리 환경은 미리 보기 피드백에 따라 변경 될 임시 동작입니다. `OPENROWSET`쿼리 experinece 변경 되 고 잘 정의 된 스키마를 사용 하 여 정렬 될 수 있으므로 공개 미리 보기 중에 함수에서 제공 하는 스키마에 의존 하지 마십시오. 의견을 제공 하려면 [Synapse 링크 제품 팀](mailto:cosmosdbsynapselink@microsoft.com) 에 문의 하세요.

## <a name="overview"></a>개요

Azure Cosmos DB 분석 저장소에서 데이터 쿼리 및 분석을 지원 하기 위해 서버를 사용 하지 않는 SQL 풀은 다음 구문을 사용 합니다 `OPENROWSET` .

```sql
OPENROWSET( 
       'CosmosDB',
       '<Azure Cosmos DB connection string>',
       <Container name>
    )  [ < with clause > ] AS alias
```

Azure Cosmos DB 연결 문자열은 Azure Cosmos DB 계정 이름, 데이터베이스 이름, 데이터베이스 계정 마스터 키 및 작동 하는 선택적 영역 이름을 지정 합니다 `OPENROWSET` . 

> [!IMPORTANT]
> 뒤에 별칭을 사용 하는지 확인 `OPENROWSET` 합니다. 함수 뒤에 별칭을 지정 하지 않으면 서버를 사용 하지 않는 SQL 끝점에 대 한 연결 문제를 야기 하는 [알려진 문제가](#known-issues) Synapse `OPENROWSET` .

연결 문자열의 형식은 다음과 같습니다.
```sql
'account=<database account name>;database=<database name>;region=<region name>;key=<database account master key>'
```

구문에서 따옴표 없이 Azure Cosmos DB 컨테이너 이름이 지정 됩니다 `OPENROWSET` . 컨테이너 이름에 특수 문자 (예: 대시 '-')가 있는 경우이 이름은 `[]` 구문에서 (대괄호) 안에 래핑됩니다 `OPENROWSET` .

> [!NOTE]
> 서버를 사용 하지 않는 SQL 풀은 Azure Cosmos DB 트랜잭션 저장소 쿼리를 지원 하지 않습니다.

## <a name="sample-data-set"></a>샘플 데이터 세트

이 문서의 예는에 대 한 유럽 센터의 데이터를 기반으로 합니다 [(예: 질병 방지 및 제어 (ECDC) COVID-19 사례](https://azure.microsoft.com/services/open-datasets/catalog/ecdc-covid-19-cases/) 및 [Covid-19 Open Research 데이터 집합 (코드-19), doi: 10.5281/zenodo. 3715505)](https://azure.microsoft.com/services/open-datasets/catalog/covid-19-open-research/). 

이러한 페이지에서 데이터의 라이선스와 구조를 확인 하 고 [Ecdc](https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/ecdc_cases/latest/ecdc_cases.json) 및 [Cord19](https://azureopendatastorage.blob.core.windows.net/covid19temp/comm_use_subset/pdf_json/000b7d1517ceebb34e1e3e817695b6de03e2fa78.json) 데이터 집합에 대 한 샘플 데이터를 다운로드할 수 있습니다.

이 문서의 내용을 따르려면 서버를 사용 하지 않는 SQL 풀을 사용 하 여 Cosmos DB 데이터를 쿼리 하는 방법 보여주는 다음 리소스를 만들어야 합니다.
* [Synapse Link가 설정](../../cosmos-db/configure-synapse-link.md) 된 Azure Cosmos DB 데이터베이스 계정
* 이라는 Azure Cosmos DB 데이터베이스 `covid`
* 라는 두 개의 Azure Cosmos DB 컨테이너 `EcdcCases` 와 `Cord19` 위의 예제 데이터 집합을 로드 했습니다.

## <a name="explore-azure-cosmos-db-data-with-automatic-schema-inference"></a>자동 스키마 유추를 사용 하 여 Azure Cosmos DB 데이터 탐색

Azure Cosmos DB에서 데이터를 탐색 하는 가장 쉬운 방법은 자동 스키마 유추 기능을 활용 하는 것입니다. 문에서 절을 생략 하면 서버를 사용 `WITH` `OPENROWSET` 하지 않는 SQL 풀에서 Azure Cosmos DB 컨테이너의 분석 저장소 스키마를 자동으로 검색 (유추) 하도록 지시할 수 있습니다.

```sql
SELECT TOP 10 *
FROM OPENROWSET( 
       'CosmosDB',
       'account=MyCosmosDbAccount;database=covid;region=westus2;key=C0Sm0sDbKey==',
       EcdcCases) as documents
```
위의 예에서는 서버를 사용 하지 않는 SQL 풀에서 `covid` `MyCosmosDbAccount` Azure Cosmos DB 키 (위 예제의 더미)를 사용 하 여 인증 된 Azure Cosmos DB 계정에서 데이터베이스에 연결 하도록 지시 합니다. 그러면 `EcdcCases` 지역에서 컨테이너의 분석 저장소에 액세스 하 게 `West US 2` 됩니다. 특정 속성의 프로젝션이 없으므로 `OPENROWSET` 함수는 Azure Cosmos DB 항목의 모든 속성을 반환 합니다.

동일한 Azure Cosmos DB 데이터베이스의 다른 컨테이너에서 데이터를 탐색 해야 하는 경우 동일한 연결 문자열을 사용 하 고 세 번째 매개 변수로 필요한 컨테이너를 참조할 수 있습니다.

```sql
SELECT TOP 10 *
FROM OPENROWSET( 
       'CosmosDB',
       'account=MyCosmosDbAccount;database=covid;region=westus2;key=C0Sm0sDbKey==',
       Cord19) as cord19
```

## <a name="explicitly-specify-schema"></a>명시적으로 스키마 지정

의 자동 스키마 유추 기능은 `OPENROWSET` 간단 하 고 사용 하기 쉬운 querience을 제공 하지만, 비즈니스 시나리오에서는 Azure Cosmos DB 데이터의 관련 속성을 읽기 전용으로 지정 하도록 스키마를 명시적으로 지정 해야 할 수 있습니다.

`OPENROWSET` 컨테이너의 데이터에서 읽을 속성을 명시적으로 지정 하 고 해당 데이터 형식을 지정할 수 있습니다. 다음 구조를 포함 하는 [Ecdc COVID 데이터 집합](https://azure.microsoft.com/services/open-datasets/catalog/ecdc-covid-19-cases/) 의 일부 데이터를 Azure Cosmos DB으로 가져왔습니다.

```json
{"date_rep":"2020-08-13","cases":254,"countries_and_territories":"Serbia","geo_id":"RS"}
{"date_rep":"2020-08-12","cases":235,"countries_and_territories":"Serbia","geo_id":"RS"}
{"date_rep":"2020-08-11","cases":163,"countries_and_territories":"Serbia","geo_id":"RS"}
```

Azure Cosmos DB의 이러한 플랫 JSON 문서를 Synapse SQL의 행 및 열 집합으로 표시할 수 있습니다. `OPENROWSET` 함수를 사용 하면 절에서 읽을 속성의 하위 집합 및 정확한 열 형식을 지정할 수 있습니다 `WITH` .

```sql
SELECT TOP 10 *
FROM OPENROWSET(
      'CosmosDB',
      'account=MyCosmosDbAccount;database=covid;region=westus2;key=C0Sm0sDbKey==',
       EcdcCases
    ) with ( date_rep varchar(20), cases bigint, geo_id varchar(6) ) as rows
```

이 쿼리의 결과는 다음과 같을 수 있습니다.

| date_rep | cases | geo_id |
| --- | --- | --- |
| 2020-08-13 | 254 | RS |
| 2020-08-12 | 235 | RS |
| 2020-08-11 | 163 | RS |

Azure Cosmos DB 값에 사용 해야 하는 SQL 형식에 대 한 자세한 내용은이 문서의 끝에 있는 [sql 형식 매핑 규칙](#azure-cosmos-db-to-sql-type-mappings) 을 검토 하세요.

## <a name="querying-nested-objects-and-arrays"></a>중첩 된 개체 및 배열 쿼리

Azure Cosmos DB를 사용 하면 중첩 된 개체 또는 배열로 작성 하 여 더 복잡 한 데이터 모델을 나타낼 수 있습니다. Azure Cosmos DB에 대 한 Synapse 링크의 autosync 기능은 서버 리스 SQL 풀에서 다양 한 쿼리를 허용 하는 중첩 된 데이터 형식 처리를 포함 하 여 분석 저장소에서 스키마 표현을 관리 합니다.

예를 들어 [코드-19](https://azure.microsoft.com/services/open-datasets/catalog/covid-19-open-research/) 데이터 집합에는 다음 구조를 따라 JSON 문서가 있습니다.

```json
{
    "paper_id": <str>,                   # 40-character sha1 of the PDF
    "metadata": {
        "title": <str>,
        "authors": <array of objects>    # list of author dicts, in order
        ...
     }
     ...
}
```

Azure Cosmos DB의 중첩 된 개체와 배열은 함수가 읽을 때 쿼리 결과에 JSON 문자열로 표시 됩니다 `OPENROWSET` . Sql 열에서 sql 열로 이러한 복합 형식의 값을 읽는 한 가지 옵션은 SQL JSON 함수를 사용 하는 것입니다.

```sql
SELECT
    title = JSON_VALUE(metadata, '$.title'),
    authors = JSON_QUERY(metadata, '$.authors'),
    first_author_name = JSON_VALUE(metadata, '$.authors[0].first')
FROM
    OPENROWSET(
      'CosmosDB',
      'account=MyCosmosDbAccount;database=covid;region=westus2;key=C0Sm0sDbKey==',
       Cord19
    WITH ( metadata varchar(MAX) ) AS docs;
```

이 쿼리의 결과는 다음과 같을 수 있습니다.

| title | authors | first_autor_name |
| --- | --- | --- |
| Epidemi에 대 한 보충 정보 ... |   `[{"first":"Julien","last":"Mélade","suffix":"","affiliation":{"laboratory":"Centre de Recher…` | Julien |  

다른 옵션으로 절을 사용 하는 경우 개체에서 중첩 된 값의 경로를 지정할 수도 있습니다 `WITH` .

```sql
SELECT
    *
FROM
    OPENROWSET(
      'CosmosDB',
      'account=MyCosmosDbAccount;database=covid;region=westus2;key=C0Sm0sDbKey==',
       Cord19
    WITH ( title varchar(1000) '$.metadata.title',
           authors varchar(max) '$.metadata.authors'
    ) AS docs;
```

서버를 사용 하지 않는 [SQL 풀의 중첩 된 구조](query-parquet-nested-types.md)및 [Synapse 링크의 복합 데이터 형식을](../how-to-analyze-complex-schema.md) 분석 하는 방법에 대해 자세히 알아보세요.

> [!IMPORTANT]
> 대신 텍스트에서 예기치 않은 문자가 표시 `MÃƒÂ©lade` `Mélade` 되는 경우 데이터베이스 데이터 정렬이 [UTF8](https://docs.microsoft.com/sql/relational-databases/collations/collation-and-unicode-support#utf8) 데이터 정렬로 설정 되지 않습니다. 
> 과 같은 일부 SQL 문을 사용 하 여 [데이터베이스의 데이터 정렬을](https://docs.microsoft.com/sql/relational-databases/collations/set-or-change-the-database-collation#to-change-the-database-collation) 일부 UTF8 데이터 정렬로 변경 `ALTER DATABASE MyLdw COLLATE LATIN1_GENERAL_100_CI_AS_SC_UTF8` 합니다.


## <a name="flattening-nested-arrays"></a>중첩 된 배열 평면화

Azure Cosmos DB 데이터에 [Cord19](https://azure.microsoft.com/services/open-datasets/catalog/covid-19-open-research/) 데이터 집합의 작성자 배열과 같은 중첩 된 subarrays 있을 수 있습니다.

```json
{
    "paper_id": <str>,                      # 40-character sha1 of the PDF
    "metadata": {
        "title": <str>,
        "authors": [                        # list of author dicts, in order
            {
                "first": <str>,
                "middle": <list of str>,
                "last": <str>,
                "suffix": <str>,
                "affiliation": <dict>,
                "email": <str>
            },
            ...
        ],
        ...
}
```

경우에 따라 최상위 항목 (메타 데이터)의 속성을 배열 (작성자)의 모든 요소와 "조인" 해야 할 수도 있습니다. 서버를 사용 하지 않는 SQL 풀을 사용 하면 `OPENJSON` 중첩 된 배열에 함수를 적용 하 여 중첩 된 구조를 평면화 할 수 있습니다.

```sql
SELECT
    *
FROM
    OPENROWSET(
      'CosmosDB',
      'account=MyCosmosDbAccount;database=covid;region=westus2;key=C0Sm0sDbKey==',
       Cord19
    ) WITH ( title varchar(1000) '$.metadata.title',
             authors varchar(max) '$.metadata.authors' ) AS docs
      CROSS APPLY OPENJSON ( authors )
                  WITH (
                       first varchar(50),
                       last varchar(50),
                       affiliation nvarchar(max) as json
                  ) AS a
```

이 쿼리의 결과는 다음과 같을 수 있습니다.

| title | authors | first | last | 정보 |
| --- | --- | --- | --- | --- |
| Epidemi에 대 한 보충 정보 ... |   `[{"first":"Julien","last":"Mélade","suffix":"","affiliation":{"laboratory":"Centre de Recher…` | Julien | Mélade | `   {"laboratory":"Centre de Recher…` |
Epidemi에 대 한 보충 정보 ... | `[{"first":"Nicolas","last":"4#","suffix":"","affiliation":{"laboratory":"","institution":"U…` | Nicolas | 3-4 # |`{"laboratory":"","institution":"U…` | 
| Epidemi에 대 한 보충 정보 ... |   `[{"first":"Beza","last":"Ramazindrazana","suffix":"","affiliation":{"laboratory":"Centre de Recher…` | Beza | Ramazindrazana | `{"laboratory":"Centre de Recher…` |
| Epidemi에 대 한 보충 정보 ... |   `[{"first":"Olivier","last":"Flores","suffix":"","affiliation":{"laboratory":"UMR C53 CIRAD, …` | Olivier | Flores |`{"laboratory":"UMR C53 CIRAD, …` |     

> [!IMPORTANT]
> 대신 텍스트에서 예기치 않은 문자가 표시 `MÃƒÂ©lade` `Mélade` 되는 경우 데이터베이스 데이터 정렬이 [UTF8](https://docs.microsoft.com/sql/relational-databases/collations/collation-and-unicode-support#utf8) 데이터 정렬로 설정 되지 않습니다. 
> 과 같은 일부 SQL 문을 사용 하 여 [데이터베이스의 데이터 정렬을](https://docs.microsoft.com/sql/relational-databases/collations/set-or-change-the-database-collation#to-change-the-database-collation) 일부 UTF8 데이터 정렬로 변경 `ALTER DATABASE MyLdw COLLATE LATIN1_GENERAL_100_CI_AS_SC_UTF8` 합니다.

## <a name="azure-cosmos-db-to-sql-type-mappings"></a>SQL 형식 매핑 Azure Cosmos DB

Azure Cosmos DB 트랜잭션 저장소는 스키마에 관계 없이 분석 저장소는 분석 쿼리 성능을 최적화 하기 위해 스키마 화 된 됩니다. Synapse 링크의 autosync 기능을 사용 하 여 Azure Cosmos DB는 분석 저장소에서 중첩 된 데이터 형식 처리를 포함 하는 스키마 표현을 관리 합니다. 서버를 사용 하지 않는 SQL 풀에서는 분석 저장소를 쿼리 하므로 Azure Cosmos DB 입력 데이터 형식을 SQL 데이터 형식에 매핑하는 방법을 이해 하는 것이 중요 합니다.

SQL (Core) API의 Azure Cosmos DB 계정은 숫자, 문자열, 부울, null, 중첩 된 개체 또는 배열의 JSON 속성 유형을 지원 합니다. 에서 절을 사용 하는 경우 이러한 JSON 형식과 일치 하는 SQL 형식을 선택 해야 `WITH` `OPENROWSET` 합니다. Azure Cosmos DB에서 다른 속성 유형에 사용 해야 하는 SQL 열 유형 아래를 참조 하십시오.

| Azure Cosmos DB 속성 유형 | SQL 열 형식 |
| --- | --- |
| 부울 | bit |
| 정수 | bigint |
| Decimal | float |
| String | varchar (UTF8 데이터베이스 데이터 정렬) |
| 날짜/시간 (ISO 형식 문자열) | varchar (30) |
| 날짜 시간 (unix 타임 스탬프) | bigint |
| Null | `any SQL type` 
| 중첩 된 개체 또는 배열 | varchar (max) (UTF8 데이터베이스 데이터 정렬), JSON 텍스트로 직렬화 됨 |

## <a name="full-fidelity-schema"></a>전체 충실도 스키마

전체 충실도 스키마 Azure Cosmos DB 컨테이너의 모든 속성에 대해 값과 가장 일치 하는 항목을 모두 기록 합니다.
`OPENROWSET` 전체 충실도 스키마를 사용 하는 컨테이너에 대 한 함수는 각 셀에 형식과 실제 값을 모두 제공 합니다. 다음 쿼리는 전체 충실도 스키마를 사용 하 여 컨테이너에서 항목을 읽는 것으로 가정 하겠습니다.

```sql
SELECT *
FROM OPENROWSET(
      'CosmosDB',
      'account=MyCosmosDbAccount;database=covid;region=westus2;key=C0Sm0sDbKey==',
       EcdcCases
    ) as rows
```

이 쿼리의 결과는 JSON 텍스트로 형식이 지정 된 형식 및 값을 반환 합니다. 

| date_rep | cases | geo_id |
| --- | --- | --- |
| {"date": "2020-08-13"} | {"int32": "254"} | {"string": "RS"} |
| {"date": "2020-08-12"} | {"int32": "235"}| {"string": "RS"} |
| {"date": "2020-08-11"} | {"int32": "316"} | {"string": "RS"} |
| {"date": "2020-08-10"} | {"int32": "281"} | {"string": "RS"} |
| {"date": "2020-08-09"} | {"int32": "295"} | {"string": "RS"} |
| {"string": "2020/08/08"} | {"int32": "312"} | {"string": "RS"} |
| {"date": "2020-08-07"} | {"float64": "339.0"} | {"string": "RS"} |

모든 값에 대해 Cosmos DB 컨테이너 항목에서 식별 된 형식을 확인할 수 있습니다. 속성에 대 한 대부분의 값은 `date_rep` `date` 값을 포함 하지만이 중 일부는 Cosmos DB의 문자열로 잘못 저장 됩니다. Full 충실도 스키마는 올바르게 형식화 된 `date` 값과 잘못 된 형식의 값을 모두 반환 `string` 합니다.
사례 수는 값으로 저장 된 정보 `int32` 이지만 10 진수로 입력 된 값이 하나 있습니다. 이 값의 `float64` 형식은입니다. 가장 큰 수를 초과 하는 값이 있는 경우 `int32` 형식으로 저장 됩니다 `int64` . `geo_id`이 예제의 모든 값은 형식으로 저장 됩니다 `string` .

> [!IMPORTANT]
> Full 충실도 스키마는 예상 형식 및 형식이 잘못 입력 된 값을 모두 제공 합니다.
> 전체 품질 분석 저장소에서 corection를 적용 하려면 Azure Cosmos DB 컨테이너에 잘못 된 형식의 값을 정리 해야 합니다. 

Mongo DB API 종류의 Azure Cosmos DB 계정을 쿼리하려면 [여기](../../cosmos-db/analytical-store-introduction.md#analytical-schema)에서 사용할 확장 속성 이름 및 분석 저장소에서 전체 충실도 스키마 표현에 대해 자세히 알아볼 수 있습니다.

Full 충실도 스키마를 쿼리 하는 동안 SQL 형식을 명시적으로 지정 하 고 절에 속성 유형 Cosmos DB 필요 `WITH` 합니다. 다음 예제에서는 속성에 대해 `string` 가 올바른 형식이 `geo_id` 고 `int32` 속성에 올바른 형식이 있다고 가정 합니다 `cases` .

```sql
SELECT geo_id, cases = SUM(cases)
FROM OPENROWSET(
      'CosmosDB'
      'account=MyCosmosDbAccount;database=covid;region=westus2;key=C0Sm0sDbKey==',
       EcdcCases
    ) WITH ( geo_id VARCHAR(50) '$.geo_id.string',
             cases INT '$.cases.int32'
    ) as rows
GROUP BY geo_id
```

다른 형식의 값은 및 열에 반환 되지 `geo_id` 않으며 `cases` 쿼리는 `NULL` 이러한 셀의 값을 반환 합니다. 이 쿼리는 `cases` 식에서 지정 된 형식의만 참조 합니다 ( `cases.int32` ). Cosmos DB 컨테이너에서 정리할 수 없는 다른 형식 (,)의 값이 있는 경우 `cases.int64` `cases.float64` 절에서 명시적으로 참조 하 `WITH` 고 결과를 결합 해야 합니다. 다음 쿼리는 `int32` `int64` `float64` 열에 저장 된, 및를 집계 합니다 `cases` .

```sql
SELECT geo_id, cases = SUM(cases_int) + SUM(cases_bigint) + SUM(cases_float)
FROM OPENROWSET(
      'CosmosDB',
      'account=MyCosmosDbAccount;database=covid;region=westus2;key=C0Sm0sDbKey==',
       EcdcCases
    ) WITH ( geo_id VARCHAR(50) '$.geo_id.string', 
             cases_int INT '$.cases.int32',
             cases_bigint BIGINT '$.cases.int64',
             cases_float FLOAT '$.cases.float64'
    ) as rows
GROUP BY geo_id
```

이 예에서 사례 수는 `int32` , 또는 값으로 저장 되며, `int64` `float64` 국가 당 사례 수를 계산 하기 위해 모든 값을 추출 해야 합니다. 

## <a name="known-issues"></a>알려진 문제

- 함수 뒤에 별칭을 지정 **해야** 합니다 `OPENROWSET` (예: `OPENROWSET (...) AS function_alias` ). 별칭을 생략 하면 연결 문제가 발생할 수 있으며 서버를 사용 하지 않는 SQL 끝점은 일시적으로 사용할 수 없습니다 Synapse. 이 문제는 11 월 2020에 해결 될 예정입니다.
- 서버를 사용 하지 않는 SQL 풀에서 [Azure Cosmos DB 전체 충실도 스키마](#full-fidelity-schema) 를 제공 하는 쿼리 환경은 미리 보기 피드백에 따라 변경 되는 임시 동작입니다. `OPENROWSET`쿼리 환경이 잘 정의 된 스키마에 맞게 정렬 될 수 있으므로 공개 미리 보기 중에 함수에서 제공 하는 스키마를 사용 하지 마십시오. 의견을 제공 하려면 [Synapse 링크 제품 팀](mailto:cosmosdbsynapselink@microsoft.com) 에 문의 하세요.

가능한 오류 및 문제 해결 작업은 다음 표에 나와 있습니다.

| 오류 | 근본 원인 |
| --- | --- |
| 구문 오류:<br/> -' Openrowset ' 근처의 구문이 잘못 되었습니다.<br/> - `...` 은 (는) 인식할 수 없는 대량 OPENROWSET 공급자 옵션입니다.<br/> -근처의 구문이 잘못 되었습니다. `...` | 가능한 근본 원인<br/> -' CosmosDB '를 첫 번째 매개 변수로 사용 하지 않습니다.<br/> -세 번째 매개 변수에서 식별자 대신 문자열 리터럴을 사용 합니다.<br/> -세 번째 매개 변수 (컨테이너 이름)를 지정 하지 않습니다. |
| CosmosDB 연결 문자열에 오류가 있습니다. | -계정, 데이터베이스, 키가 지정 되지 않았습니다. <br/> -인식 되지 않는 연결 문자열에 옵션이 있습니다.<br/> -세미콜론 `;` 은 연결 문자열의 끝에 배치 됩니다. |
| ' 잘못 된 계정 이름 ' 또는 ' 잘못 된 데이터베이스 이름 ' 오류로 인해 CosmosDB 경로를 확인 하지 못했습니다. | 지정 된 계정 이름, 데이터베이스 이름 또는 컨테이너를 찾을 수 없거나, 지정 된 컬렉션에서 분석 저장소를 사용 하도록 설정 하지 않았습니다.|
| ' 잘못 된 비밀 값 ' 또는 ' 암호가 null 이거나 비어 있음 ' 오류로 인해 CosmosDB 경로를 확인 하지 못했습니다. | 계정 키가 잘못 되었거나 없습니다. |
| `column name`형식의 열 `type name` 이 외부 데이터 형식과 호환 되지 않습니다.`type name` | 지정 된 열 유형 in `WITH` 절이 Cosmos DB 컨테이너의 유형과 일치 하지 않습니다. Azure Cosmos DB 섹션에서 설명한 대로 열 유형을 [SQL 형식 매핑](#azure-cosmos-db-to-sql-type-mappings) 또는 사용 유형으로 변경 하십시오 `VARCHAR` . |
| `NULL`모든 셀의 값을 포함 하는 열입니다. | 절에서 열 이름 또는 경로 식이 잘못 되었을 수 있습니다 `WITH` . 절에서 열 이름 (또는 열 유형 뒤의 경로 식)은 `WITH` Cosmos DB collection의 일부 속성 이름과 일치 해야 합니다. 비교는 **대/소문자를 구분**  합니다 (예: `productCode` 및 `ProductCode` 는 다른 속성). |

[Azure Synapse 피드백 페이지](https://feedback.azure.com/forums/307516-azure-synapse-analytics?category_id=387862)에서 제안 및 문제를 보고할 수 있습니다.

## <a name="next-steps"></a>다음 단계

자세한 내용은 다음 문서를 참조하세요.

- [Azure Synapse Link를 사용 하 여 Power BI 및 서버를 사용 하지 않는 Synapse SQL 풀 사용](../../cosmos-db/synapse-link-power-bi.md)
- [주문형 SQL에서 뷰를 만들고 사용 하는 방법](create-use-views.md) 
- [Azure Cosmos DB에 대 한 SQL 주문형 뷰를 빌드하고 DirectQuery를 통해 Power BI 모델에 연결 하는 방법에 대 한 자습서](./tutorial-data-analyst.md)
