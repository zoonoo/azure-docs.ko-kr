---
title: Synapse SQL 내에서 SQL 주문형(미리 보기)을 사용하여 스토리지 파일 쿼리
description: Synapse SQL 내에서 SQL 주문형(미리 보기) 리소스를 사용하여 스토리지 파일을 쿼리하는 방법을 설명합니다.
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/19/2020
ms.author: v-stazar
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: 2126996620d6f891dde4e7530c057d2c7f31a996
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81676680"
---
# <a name="query-storage-files-using-sql-on-demand-preview-resources-within-synapse-sql"></a>Synapse SQL 내에서 SQL 주문형(미리 보기) 리소스를 사용하여 스토리지 파일 쿼리

SQL 주문형(미리 보기)을 사용하면 데이터 레이크의 데이터를 쿼리할 수 있습니다. 이는 반정형 및 비정형 데이터 쿼리를 수용하는 T-SQL 쿼리 노출 영역을 제공합니다.

쿼리에 지원되는 T-SQL 측면은 다음과 같습니다.

- 대부분의 SQL 함수, 연산자 등을 포함한 전체 [SELECT](/sql/t-sql/queries/select-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) 노출 영역입니다.
- CREATE EXTERNAL TABLE AS SELECT([CETAS](develop-tables-cetas.md))는 [외부 테이블](develop-tables-external-tables.md)을 만든 다음, Transact-SQL SELECT 문의 결과를 Azure Storage에 병렬로 내보냅니다.

현재 지원되는 항목과 지원되지 않는 항목에 대한 자세한 내용은 [SQL 주문형 개요](on-demand-workspace-overview.md) 문서를 참조하세요.

Azure AD 사용자가 쿼리를 실행하는 경우 기본적으로 Azure AD 통과 인증 프로토콜을 사용하여 스토리지 계정에 액세스합니다. 따라서 사용자가 가장되고 스토리지 수준의 권한이 확인됩니다. 요구 사항에 맞게 [스토리지 액세스를 제어](develop-storage-files-storage-access-control.md)할 수 있습니다.

## <a name="extensions"></a>확장

SQL 주문형은 Azure Storage 파일에 있는 데이터를 적절히 쿼리할 수 있는 원활한 환경을 지원하기 위해 다음과 같은 추가 기능을 통해 [OPENROWSET](develop-openrowset.md) 함수를 사용합니다.

- [여러 파일 또는 폴더 쿼리](#query-multiple-files-or-folders)
- [PARQUET 파일 형식](#parquet-file-format)
- [분리된 텍스트 작업에 대한 추가 옵션(필드 종결자, 행 종결자, 이스케이프 문자)](#additional-options-for-working-with-delimited-text)
- [선택한 열 하위 세트 읽기](#read-a-chosen-subset-of-columns)
- [스키마 유추](#schema-inference)
- [filename 함수](#filename-function)
- [filepath 함수](#filepath-function)
- [복합 형식 및 중첩되거나 반복되는 데이터 구조 작업](#work-with-complex-types-and-nested-or-repeated-data-structures)

### <a name="query-multiple-files-or-folders"></a>여러 파일 또는 폴더 쿼리

폴더 또는 폴더 세트 내의 파일 세트를 단일 엔터티 또는 행 세트로 처리하면서 이에 대해 T-SQL 쿼리를 실행하려면 파일 또는 폴더 세트에 대한 폴더 또는 패턴(와일드카드 사용)의 경로를 제공합니다.

다음 규칙이 적용됩니다.

- 패턴은 디렉터리 경로의 일부 또는 파일 이름에 나타날 수 있습니다.
- 여러 패턴이 동일한 디렉터리 단계 또는 파일 이름에 나타날 수 있습니다.
- 여러 와일드카드가 있는 경우 일치하는 모든 경로 내의 파일이 결과 파일 세트에 포함됩니다.

```
N'https://myaccount.blob.core.windows.net/myroot/*/mysubfolder/*.csv'
```

사용 예제는 [폴더 및 여러 파일 쿼리](query-folders-multiple-csv-files.md)를 참조하세요.

### <a name="parquet-file-format"></a>PARQUET 파일 형식

Parquet 원본 데이터를 쿼리하려면 FORMAT = 'PARQUET'를 사용합니다.

```syntaxsql
OPENROWSET
(
    { BULK 'data_file' ,
    { FORMATFILE = 'format_file_path' [ <bulk_options>] | SINGLE_BLOB | SINGLE_CLOB | SINGLE_NCLOB } }
)
AS table_alias(column_alias,...n)
<bulk_options> ::=
...
[ , FORMAT = {'CSV' | 'PARQUET'} ]
```

사용 예제는 [Parquet 파일 쿼리](query-parquet-files.md) 문서를 검토하세요.

### <a name="additional-options-for-working-with-delimited-text"></a>분리된 텍스트 작업에 대한 추가 옵션

CSV(분리된 텍스트) 파일 작업에 도입된 추가 매개 변수는 다음과 같습니다.

```syntaxsql
<bulk_options> ::=
...
[ , FIELDTERMINATOR = 'char' ]
[ , ROWTERMINATOR = 'char' ]
[ , ESCAPE_CHAR = 'char' ]
...
```

- ESCAPE_CHAR = 'char' - 자체 및 파일의 모든 구분 기호 값을 이스케이프하는 데 사용되는 파일의 문자를 지정합니다. 이스케이프 문자 뒤에 자체 또는 구분 기호 값 이외의 값이 있으면 값을 읽을 때 이스케이프 문자가 삭제됩니다.
ESCAPE_CHAR 매개 변수는 FIELDQUOTE를 사용하도록 설정되었는지 여부에 관계없이 적용됩니다. 이는 따옴표로 묶은 문자를 이스케이프하는 데 사용되지 않습니다. 따옴표로 묶은 문자는 Excel CSV 동작에 따라 큰따옴표를 사용하여 이스케이프됩니다.
- FIELDTERMINATOR ='field_terminator' - 사용할 필드 종결자를 지정합니다. 기본 필드 종결자는 쉼표(" **,** ")입니다.
- ROWTERMINATOR ='row_terminator' - 사용할 행 종결자를 지정합니다. 기본 행 종결자는 줄 바꿈 문자( **\r\n**)입니다.

### <a name="read-a-chosen-subset-of-columns"></a>선택한 열 하위 세트 읽기

읽으려는 열을 지정하기 위해 선택적 WITH 절을 OPENROWSET 문 내에 지정할 수 있습니다.

- CSV 데이터 파일이 있는 경우 모든 열을 읽으려면 열 이름과 해당 데이터 형식을 제공합니다. 열의 하위 세트를 원하는 경우 서수를 사용하여 원본 데이터 파일에서 서수별로 열을 선택합니다. 열은 서수 지정에 따라 바인딩됩니다.
- Parquet 데이터 파일이 있는 경우 원본 데이터 파일의 열 이름과 일치하는 열 이름을 제공합니다. 열은 이름에 따라 바인딩됩니다.

```syntaxsql
OPENROWSET
...
| BULK 'data_file',
{ FORMATFILE = 'format_file_path' [ <bulk_options>] | SINGLE_BLOB | SINGLE_CLOB | SINGLE_NCLOB } }
) AS table_alias(column_alias,...n) | WITH ( {'column_name' 'column_type' [ 'column_ordinal'] })
```

샘플은 [모든 열을 지정하지 않고 CSV 파일 읽기](query-single-csv-file.md#returning-subset-of-columns)를 참조하세요.

### <a name="schema-inference"></a>스키마 유추

OPENROWSET 문에서 WITH 절을 생략하면 기본 파일에서 스키마를 자동으로 검색(유추)하도록 서비스에 지시할 수 있습니다.

> [!NOTE]
> 이는 현재 PARQUET 파일 형식에만 적용됩니다.

```sql
OPENROWSET(
BULK N'path_to_file(s)', FORMAT='PARQUET');
```

### <a name="filename-function"></a>filename 함수

이 함수는 행이 제공되는 파일 이름을 반환합니다.

특정 파일을 쿼리하려면 [특정 파일 쿼리](query-specific-files.md#filename) 문서의 filename 섹션을 참조하세요.

### <a name="filepath-function"></a>filepath 함수

이 함수는 전체 경로 또는 경로의 일부를 반환합니다.

- 매개 변수 없이 호출하면 행이 제공되는 파일의 전체 경로를 반환합니다.
- 매개 변수를 사용하여 호출하면 매개 변수에 지정된 위치에 있는 와일드카드와 일치하는 경로의 일부를 반환합니다. 예를 들어 1의 매개 변수 값은 첫 번째 와일드카드와 일치하는 경로의 일부를 반환합니다.

자세한 내용은 [특정 파일 쿼리](query-specific-files.md#filepath) 문서의 filepath 섹션을 참조하세요.

### <a name="work-with-complex-types-and-nested-or-repeated-data-structures"></a>복합 형식 및 중첩되거나 반복되는 데이터 구조 작업

[Parquet](https://github.com/apache/parquet-format/blob/master/LogicalTypes.md#nested-types) 파일과 같이 중첩되거나 반복되는 데이터 형식으로 저장된 데이터를 사용할 때 원활한 환경을 사용하도록 설정하기 위해 SQL 주문형에서 아래 확장을 추가했습니다.

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

Struct와 같은 중첩된 열의 중첩된 요소에 액세스하려면 "점 표기법"을 사용하여 필드 이름을 경로에 연결합니다. OPENROWSET 함수의 WITH 절에 경로를 column_name으로 제공합니다.

구문 조각 예제는 다음과 같습니다.

```syntaxsql
    OPENROWSET
    (   BULK 'unstructured_data_path' ,
        FORMAT = 'PARQUET' )
    WITH ({'column_name' 'column_type',})
    [AS alias]
    'column_name' ::= '[field_name.] field_name'
```

기본적으로 OPENROWSET 함수는 원본 필드 이름 및 경로를 WITH 절에 제공된 열 이름과 일치시킵니다. 동일한 원본 Parquet 파일 내에서 서로 다른 중첩 수준에 포함된 요소는 WITH 절을 통해 액세스할 수 있습니다.

**반환 값**

- 함수는 지정된 요소와 지정된 경로에서 중첩 형식 그룹에 없는 모든 Parquet 형식에 대해 int, decimal 및 varchar와 같은 스칼라 값을 반환합니다.
- 경로가 중첩 형식의 요소를 가리키는 경우 함수에서 지정된 경로의 최상위 요소에서 시작하는 JSON 조각을 반환합니다. JSON 조각은 varchar(8000) 형식입니다.
- 지정된 column_name에서 속성을 찾을 수 없는 경우 함수에서 오류를 반환합니다.
- [경로 모드](/sql/relational-databases/json/json-path-expressions-sql-server?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest#PATHMODE)에 따라 지정된 column_path에서 속성을 찾을 수 없는 경우 함수에서 strict 모드인 경우 오류를 반환하거나 lax 모드인 경우 null을 반환합니다.

쿼리 샘플은 [Parquet 중첩 형식 쿼리](query-parquet-nested-types.md#access-elements-from-nested-columns) 문서의 '중첩된 열의 요소에 액세스' 섹션을 검토합니다.

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

## <a name="next-steps"></a>다음 단계

다른 파일 형식을 쿼리하고, 보기를 만들고 사용하는 방법에 대한 자세한 내용은 다음 문서를 참조하세요.

- [단일 CSV 파일 쿼리](query-single-csv-file.md)
- [Parquet 파일 쿼리](query-parquet-files.md)
- [JSON 파일 쿼리](query-json-files.md)
- [Parquet 중첩 형식 쿼리](query-parquet-nested-types.md)
- [폴더 및 여러 CSV 파일 쿼리](query-folders-multiple-csv-files.md)
- [쿼리에서 파일 메타데이터 사용](query-specific-files.md)
- [보기 만들기 및 사용](create-use-views.md)
