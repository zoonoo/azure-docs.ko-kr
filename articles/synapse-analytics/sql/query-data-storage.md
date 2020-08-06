---
title: SQL 주문형(미리 보기)을 사용하여 스토리지의 데이터 쿼리
description: 이 문서에서는 Azure Synapse Analytics 내에서 SQL 주문형(미리 보기) 리소스를 사용하여 Azure 스토리지를 쿼리하는 방법을 설명합니다.
services: synapse analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: sql
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: 93e6b373aa125facb3a3eddecc926438c919b335
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/31/2020
ms.locfileid: "87489744"
---
# <a name="query-storage-files-using-sql-on-demand-preview-resources-within-synapse-sql"></a>Synapse SQL 내에서 SQL 주문형(미리 보기) 리소스를 사용하여 스토리지 파일 쿼리

SQL 주문형(미리 보기)을 사용하면 데이터 레이크의 데이터를 쿼리할 수 있습니다. 이는 반정형 및 비정형 데이터 쿼리를 수용하는 T-SQL 쿼리 노출 영역을 제공합니다. 쿼리에 지원되는 T-SQL 측면은 다음과 같습니다.

- 대부분의 [SQL 함수 및 연산자](overview-features.md)를 포함하는 전체 [SELECT](/sql/t-sql/queries/select-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) 노출 영역.
- CREATE EXTERNAL TABLE AS SELECT([CETAS](develop-tables-cetas.md))는 [외부 테이블](develop-tables-external-tables.md)을 만든 다음, Transact-SQL SELECT 문의 결과를 Azure Storage에 병렬로 내보냅니다.

현재 지원되는 항목과 지원되지 않는 항목에 대한 자세한 내용은 [SQL 주문형 개요](on-demand-workspace-overview.md) 문서 또는 다음 문서를 참조하세요.
- [External table](develop-tables-external-tables.md) 및 [OPENROWSET](develop-openrowset.md) 함수를 사용하여 스토리지의 데이터를 읽는 방법을 배울 수 있는 [스토리지 액세스 개발](develop-storage-files-overview.md)
- SAS 인증 또는 작업 영역의 관리 ID를 사용하여 Synapse SQL이 스토리지에 액세스할 수 있게 설정하는 방법을 배울 수 있는 [스토리지 액세스 제어](develop-storage-files-storage-access-control.md)

## <a name="overview"></a>개요

SQL 주문형은 Azure Storage 파일에 있는 데이터를 적절히 쿼리할 수 있는 원활한 환경을 지원하기 위해 다음과 같은 추가 기능을 통해 [OPENROWSET](develop-openrowset.md) 함수를 사용합니다.

- [여러 파일 또는 폴더 쿼리](#query-multiple-files-or-folders)
- [PARQUET 파일 형식](#query-parquet-files)
- [CSV 및 구분 기호로 분리된 텍스트 쿼리(필드 종결자, 행 종결자, 이스케이프 문자)](#query-csv-files)
- [선택한 열 하위 집합 읽기](#read-a-chosen-subset-of-columns)
- [스키마 유추](#schema-inference)
- [filename 함수](#filename-function)
- [filepath 함수](#filepath-function)
- [복합 형식 및 중첩되거나 반복되는 데이터 구조 작업](#work-with-complex-types-and-nested-or-repeated-data-structures)

## <a name="query-parquet-files"></a>PARQUET 파일 쿼리

Parquet 원본 데이터를 쿼리하려면 FORMAT = 'PARQUET'를 사용합니다.

```syntaxsql
SELECT * FROM
OPENROWSET( BULK N'https://myaccount.dfs.core.windows.net//mycontainer/mysubfolder/data.parquet', FORMAT = 'PARQUET') 
WITH (C1 int, C2 varchar(20), C3 as varchar(max)) as rows
```

사용 예제는 [Parquet 파일 쿼리](query-parquet-files.md) 문서를 검토하세요.

## <a name="query-csv-files"></a>CSV 파일 쿼리

CSV 원본 데이터를 쿼리하려면 FORMAT = 'CSV'를 사용합니다. CSV 파일을 쿼리할 때 다음과 같이 `OPENROWSET` 함수의 일부로 CSV 파일의 스키마를 지정할 수 있습니다.

```sql
SELECT * FROM
OPENROWSET( BULK N'https://myaccount.dfs.core.windows.net/mycontainer/mysubfolder/data.csv', FORMAT = 'CSV', PARSER_VERSION='2.0') 
WITH (C1 int, C2 varchar(20), C3 as varchar(max)) as rows
```

구문 분석 규칙을 사용자 지정 CSV 형식으로 조정하는 데 사용할 수 있는 다음과 같은 추가 옵션이 있습니다.
- ESCAPE_CHAR = 'char' - 자체 및 파일의 모든 구분 기호 값을 이스케이프하는 데 사용되는 파일의 문자를 지정합니다. 이스케이프 문자 뒤에 자체 또는 구분 기호 값 이외의 값이 있으면 값을 읽을 때 이스케이프 문자가 삭제됩니다.
ESCAPE_CHAR 매개 변수는 FIELDQUOTE를 사용하도록 설정되었는지 여부에 관계없이 적용됩니다. 따옴표로 묶은 문자를 이스케이프하는 데 사용되지 않습니다. 따옴표 문자는 다른 따옴표 문자로 이스케이프해야 합니다. 따옴표로 묶은 문자는 값이 따옴표 문자로 캡슐화된 경우에만 열 값 내에 나타날 수 있습니다.
- FIELDTERMINATOR ='field_terminator' - 사용할 필드 종결자를 지정합니다. 기본 필드 종결자는 쉼표(" **,** ")입니다.
- ROWTERMINATOR ='row_terminator' - 사용할 행 종결자를 지정합니다. 기본 행 종결자는 줄 바꿈 문자( **\r\n**)입니다.

## <a name="file-schema"></a>파일 스키마

Synapse SQL의 SQL 언어를 사용하면 `OPENROWSET` 함수의 일부로 파일의 스키마를 정의하고 모든 열 또는 일부 열을 읽을 수 있습니다. 또는 이 함수가 스키마 유추를 사용하여 파일의 열 형식을 자동으로 확인하려고 시도합니다.

### <a name="read-a-chosen-subset-of-columns"></a>선택한 열 하위 세트 읽기

읽으려는 열을 지정하려면 `OPENROWSET` 문 내에 선택적 WITH 절을 입력하면 됩니다.

- CSV 데이터 파일이 있는 경우 모든 열을 읽으려면 열 이름과 해당 데이터 형식을 제공합니다. 열의 하위 세트를 원하는 경우 서수를 사용하여 원본 데이터 파일에서 서수별로 열을 선택합니다. 열은 서수 지정에 따라 바인딩됩니다.
- Parquet 데이터 파일이 있는 경우 원본 데이터 파일의 열 이름과 일치하는 열 이름을 제공합니다. 열은 이름을 기준으로 바인딩됩니다.

```sql
SELECT * FROM
OPENROWSET( BULK N'https://myaccount.dfs.core.windows.net/mycontainer/mysubfolder/data.parquet', FORMAT = 'PARQUET') 
WITH (
      C1 int, 
      C2 varchar(20),
      C3 as varchar(max)
) as rows
```

열마다 `WITH` 절에서 열 이름과 형식을 지정해야 합니다.
샘플은 [모든 열을 지정하지 않고 CSV 파일 읽기](query-single-csv-file.md#return-a-subset-of-columns)를 참조하세요.

## <a name="schema-inference"></a>스키마 유추

`OPENROWSET` 문에서 WITH 절을 생략하면 기본 파일에서 스키마를 자동으로 검색(유추)하도록 서비스에 지시할 수 있습니다.

> [!NOTE]
> 이는 현재 PARQUET 파일 형식에만 적용됩니다.

```sql
SELECT * FROM
OPENROWSET( BULK N'https://myaccount.dfs.core.windows.net/mycontainer/mysubfolder/data.parquet', FORMAT = 'PARQUET') 
```

최적의 성능을 위해 [적절한 유추된 데이터 형식](best-practices-sql-on-demand.md#check-inferred-data-types)이 사용되는지 확인합니다. 

## <a name="query-multiple-files-or-folders"></a>여러 파일 또는 폴더 쿼리

폴더 또는 폴더 세트 내의 파일 세트를 단일 엔터티 또는 행 세트로 처리하면서 이에 대해 T-SQL 쿼리를 실행하려면 파일 또는 폴더 세트에 대한 폴더 또는 패턴(와일드카드 사용)의 경로를 제공합니다.

다음 규칙이 적용됩니다.

- 패턴은 디렉터리 경로의 일부 또는 파일 이름에 나타날 수 있습니다.
- 여러 패턴이 동일한 디렉터리 단계 또는 파일 이름에 나타날 수 있습니다.
- 여러 와일드카드가 있는 경우 일치하는 모든 경로 내의 파일이 결과 파일 세트에 포함됩니다.

```sql
SELECT * FROM
OPENROWSET( BULK N'https://myaccount.dfs.core.windows.net/myroot/*/mysubfolder/*.parquet', FORMAT = 'PARQUET' ) as rows
```

사용 예제는 [폴더 및 여러 파일 쿼리](query-folders-multiple-csv-files.md)를 참조하세요.

## <a name="file-metadata-functions"></a>파일 메타데이터 함수

### <a name="filename-function"></a>filename 함수

이 함수는 행이 제공되는 파일 이름을 반환합니다. 

특정 파일을 쿼리하려면 [특정 파일 쿼리](query-specific-files.md#filename) 문서의 filename 섹션을 참조하세요.

반환 데이터 형식은 nvarchar(1024)입니다. 성능을 최적화하려면 항상 filename 함수의 결과를 적절한 데이터 형식으로 캐스팅합니다. 문자 데이터 형식을 사용하는 경우 적절한 길이가 사용되는지 확인합니다.

### <a name="filepath-function"></a>filepath 함수

이 함수는 전체 경로 또는 경로의 일부를 반환합니다.

- 매개 변수 없이 호출하면 행이 제공되는 파일의 전체 경로를 반환합니다.
- 매개 변수를 사용하여 호출하면 매개 변수에 지정된 위치에 있는 와일드카드와 일치하는 경로의 일부를 반환합니다. 예를 들어 1의 매개 변수 값은 첫 번째 와일드카드와 일치하는 경로의 일부를 반환합니다.

자세한 내용은 [특정 파일 쿼리](query-specific-files.md#filepath) 문서의 filepath 섹션을 참조하세요.

반환 데이터 형식은 nvarchar(1024)입니다. 성능을 최적화하려면 항상 filepath 함수의 결과를 적절한 데이터 형식으로 캐스팅합니다. 문자 데이터 형식을 사용하는 경우 적절한 길이가 사용되는지 확인합니다.

## <a name="work-with-complex-types-and-nested-or-repeated-data-structures"></a>복합 형식 및 중첩되거나 반복되는 데이터 구조 작업

[Parquet](https://github.com/apache/parquet-format/blob/master/LogicalTypes.md#nested-types) 파일처럼 중첩되거나 반복되는 데이터 형식으로 저장된 데이터에 원활한 환경을 사용하도록 설정하기 위해, SQL 주문형에는 다음 확장이 추가되었습니다.

#### <a name="project-nested-or-repeated-data"></a>중첩되거나 반복되는 데이터 프로젝션

데이터를 프로젝션하려면 중첩된 데이터 형식의 열이 포함된 Parquet 파일에 대해 SELECT 문을 실행합니다. 출력에서 중첩된 값은 JSON으로 직렬화되고, varchar(8000) SQL 데이터 형식으로 반환됩니다.

```sql
    SELECT * FROM
    OPENROWSET
    (   BULK 'unstructured_data_path' ,
        FORMAT = 'PARQUET' )
    [AS alias]
```

자세한 내용은 [Parquet 중첩 형식 쿼리](query-parquet-nested-types.md#project-nested-or-repeated-data) 문서의 '중첩되거나 반복되는 데이터 프로젝션' 섹션을 참조하세요.

#### <a name="access-elements-from-nested-columns"></a>중첩된 열의 요소에 액세스

Struct와 같은 중첩된 열의 중첩된 요소에 액세스하려면 "점 표기법"을 사용하여 필드 이름을 경로에 연결합니다. `OPENROWSET` 함수의 WITH 절에서 경로를 column_name 형식으로 입력합니다.

구문 조각 예제는 다음과 같습니다.

```syntaxsql
    OPENROWSET
    (   BULK 'unstructured_data_path' ,
        FORMAT = 'PARQUET' )
    WITH ({'column_name' 'column_type',})
    [AS alias]
    'column_name' ::= '[field_name.] field_name'
```

기본적으로 `OPENROWSET` 함수는 원본 필드 이름과 경로를 WITH 절에 입력된 열 이름과 매칭합니다. 동일한 원본 Parquet 파일 내에서 서로 다른 중첩 수준에 포함된 요소는 WITH 절을 통해 액세스할 수 있습니다.

**반환 값**

- 함수는 지정된 요소와 지정된 경로에서 중첩 형식 그룹에 없는 모든 Parquet 형식에 대해 int, decimal 및 varchar와 같은 스칼라 값을 반환합니다.
- 경로가 중첩 형식의 요소를 가리키는 경우 함수에서 지정된 경로의 최상위 요소에서 시작하는 JSON 조각을 반환합니다. JSON 조각은 varchar(8000) 형식입니다.
- 지정된 column_name에서 속성을 찾을 수 없는 경우 함수에서 오류를 반환합니다.
- [경로 모드](/sql/relational-databases/json/json-path-expressions-sql-server?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest#PATHMODE)에 따라 지정된 column_path에서 속성을 찾을 수 없는 경우 함수에서 strict 모드인 경우 오류를 반환하거나 lax 모드인 경우 null을 반환합니다.

쿼리 샘플은 [Parquet 중첩 형식 쿼리](query-parquet-nested-types.md#read-properties-from-nested-object-columns) 문서의 '중첩된 열의 요소에 액세스' 섹션을 검토합니다.

#### <a name="access-elements-from-repeated-columns"></a>반복된 열의 요소에 액세스

Array 또는 Map의 요소와 같은 반복된 열의 요소에 액세스하려면 프로젝션하여 제공해야 하는 모든 스칼라 요소에 [JSON_VALUE](/sql/t-sql/functions/json-value-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) 함수를 사용합니다.

- 첫 번째 매개 변수로 중첩되거나 반복되는 열
- 두 번째 매개 변수로 액세스할 요소 또는 속성을 지정하는 [JSON 경로](/sql/relational-databases/json/json-path-expressions-sql-server?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)

반복된 열의 비 스칼라 요소에 액세스하려면 프로젝션하여 제공해야 하는 모든 비 스칼라 요소에 대해 [JSON_QUERY](/sql/t-sql/functions/json-query-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) 함수를 사용합니다.

- 첫 번째 매개 변수로 중첩되거나 반복되는 열
- 두 번째 매개 변수로 액세스할 요소 또는 속성을 지정하는 [JSON 경로](/sql/relational-databases/json/json-path-expressions-sql-server?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)

아래 구문 조각을 참조하세요.

```syntaxsql
    SELECT
       { JSON_VALUE (column_name, path_to_sub_element), }
       { JSON_QUERY (column_name [ , path_to_sub_element ]), )
    FROM
    OPENROWSET
    (   BULK 'unstructured_data_path' ,
        FORMAT = 'PARQUET' )
    [AS alias]
```

[Parquet 중첩 형식 쿼리](query-parquet-nested-types.md#access-elements-from-repeated-columns) 문서에서 반복된 열의 요소에 액세스하는 쿼리 샘플을 찾을 수 있습니다.

## <a name="query-samples"></a>쿼리 샘플

샘플 쿼리를 사용하여 다양한 형식의 데이터를 쿼리하는 방법에 대해 자세히 알아볼 수 있습니다.

### <a name="tools"></a>도구

쿼리를 실행하는 데 필요한 도구:
    - Azure Synapse Studio(미리 보기)
    - Azure Data Studio
    - SQL Server Management Studio

### <a name="demo-setup"></a>데모 설정

첫 번째 단계는 쿼리를 실행할 **데이터베이스 만들기**입니다. 그런 다음, 해당 데이터베이스에서 [설치 스크립트](https://github.com/Azure-Samples/Synapse/blob/master/SQL/Samples/LdwSample/SampleDB.sql)를 실행하여 개체를 초기화합니다. 

이 설치 스크립트는 이러한 샘플의 데이터를 읽는 데 사용되는 데이터 원본, 데이터베이스 범위 자격 증명 및 외부 파일 형식을 만듭니다.

> [!NOTE]
> 데이터베이스는 실제 데이터가 아닌 메타데이터를 보는 용도로만 사용됩니다.  사용할 데이터베이스 이름을 적어 둡니다. 나중에 필요합니다.

```sql
CREATE DATABASE mydbname;
```

### <a name="provided-demo-data"></a>제공된 데모 데이터

데모 데이터에는 다음 데이터 세트가 포함되어 있습니다.

- 뉴욕시 택시 - 노란색 택시 주행 기록 - CSV 및 Parquet 형식으로 설정된 뉴욕시 공개 데이터 세트의 일부
- CSV 형식의 인구 데이터 세트
- 중첩 열이 있는 샘플 Parquet 파일
- JSON 형식의 서적

| 폴더 경로                                                  | Description                                                  |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| /csv/                                                        | CSV 형식의 데이터에 대한 부모 폴더                         |
| /csv/population/<br />/csv/population-unix/<br />/csv/population-unix-hdr/<br />/csv/population-unix-hdr-escape<br />/csv/population-unix-hdr-quoted | 다른 CSV 형식의 인구 데이터 파일이 있는 폴더 |
| /csv/taxi/                                                   | CSV 형식의 뉴욕시 공용 데이터 파일이 있는 폴더              |
| /parquet/                                                    | Parquet 형식의 데이터에 대한 부모 폴더                     |
| /parquet/taxi                                                | Hive/Hadoop 파티션 구성표를 사용하여 연도 및 월별로 분할된 Parquet 형식의 뉴욕시 공용 데이터 파일입니다. |
| /parquet/nested/                                             | 중첩 열이 있는 샘플 Parquet 파일                     |
| /json/                                                       | JSON 형식의 데이터에 대한 부모 폴더                        |
| /json/books/                                                 | 서적 데이터가 포함된 JSON 파일                                   |


## <a name="next-steps"></a>다음 단계

다른 파일 형식을 쿼리하고, 보기를 만들고 사용하는 방법에 대한 자세한 내용은 다음 문서를 참조하세요.

- [CSV 파일 쿼리](query-single-csv-file.md)
- [Parquet 파일 쿼리](query-parquet-files.md)
- [JSON 파일 쿼리](query-json-files.md)
- [중첩 값 쿼리](query-parquet-nested-types.md)
- [폴더 및 여러 CSV 파일 쿼리](query-folders-multiple-csv-files.md)
- [쿼리에서 파일 메타데이터 사용](query-specific-files.md)
- [보기 만들기 및 사용](create-use-views.md)
