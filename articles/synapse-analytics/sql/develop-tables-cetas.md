---
title: Synapse SQL의 CREATE EXTERNAL TABLE AS SELECT (CETAS)
description: Synapse SQL에서 CREATE EXTERNAL TABLE AS SELECT (CETAS) 사용
services: synapse-analytics
author: filippopovic
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: sql
ms.date: 09/15/2020
ms.author: fipopovi
ms.reviewer: jrasnick
ms.openlocfilehash: 93f23cdcfb3fb7107e3b1838b48b3e58ccc2d028
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/25/2020
ms.locfileid: "91288769"
---
# <a name="cetas-with-synapse-sql"></a>Synapse SQL의 CETAS

SQL 풀 또는 SQL 주문형(미리 보기)에서 CREATE EXTERNAL TABLE AS SELECT (CETAS)를 사용하여 다음 작업을 완료할 수 있습니다.  

- 외부 테이블 만들기
- Transact-SQL SELECT 문의 결과를 다음 위치에 병렬로 내보내기:

  - Hadoop은
  - Azure Storage Blob
  - Azure Data Lake Storage Gen2

## <a name="cetas-in-sql-pool"></a>SQL 풀의 CETAS

SQL 풀의 경우 CETAS 사용 및 구문에 대한 자세한 내용은 [CREATE EXTERNAL TABLE AS SELECT](/sql/t-sql/statements/create-external-table-as-select-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) 문서를 참조하세요. SQL 풀을 사용하는 CTAS에 대한 지침은 [CREATE TABLE AS SELECT](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) 문서를 참조하세요.

## <a name="cetas-in-sql-on-demand"></a>SQL 주문형의 CETAS

SQL 주문형을 사용하는 경우 CETAS는 외부 테이블을 만들고 쿼리 결과를 Azure Storage Blob 또는 Azure Data Lake Storage Gen2로 내보내는 데 사용됩니다.

## <a name="syntax"></a>구문

```syntaxsql
CREATE EXTERNAL TABLE [ [database_name  . [ schema_name ] . ] | schema_name . ] table_name
    WITH (
        LOCATION = 'path_to_folder',  
        DATA_SOURCE = external_data_source_name,  
        FILE_FORMAT = external_file_format_name  
)
    AS <select_statement>  
[;]

<select_statement> ::=  
    [ WITH <common_table_expression> [ ,...n ] ]  
    SELECT <select_criteria>
```

## <a name="arguments"></a>인수

*[ [ *database_name* . [ *schema_name* ] . ] | *schema_name* . ] *table_name**

만들려는 테이블의 한 부분에서 세 부분으로 이루어진 이름입니다. 외부 테이블의 경우 SQL 주문형은 테이블 메타데이터만 저장합니다. SQL 주문형에서는 실제 데이터가 이동하거나 저장되지 않습니다.

LOCATION = *'path_to_folder'*

SELECT 문의 결과를 외부 데이터 원본에 기록할 위치를 지정합니다. 루트 폴더는 외부 데이터 원본에 지정된 데이터 위치입니다. LOCATION은 폴더를 가리켜야 하며 뒤에 /가 있어야 합니다. 예: aggregated_data/

DATA_SOURCE = *external_data_source_name*

외부 데이터가 저장될 위치를 포함하는 외부 데이터 원본 개체의 이름을 지정합니다. 외부 데이터 원본을 만들려면 [CREATE EXTERNAL DATA SOURCE(Transact-SQL)](develop-tables-external-tables.md#create-external-data-source)를 사용합니다.

FILE_FORMAT = *external_file_format_name*

외부 데이터 파일에 대한 형식을 포함하는 외부 파일 형식 개체의 이름을 지정합니다. 외부 파일 형식을 만들려면 [CREATE EXTERNAL FILE FORMAT (Transact-SQL)](develop-tables-external-tables.md#create-external-file-format)을 사용합니다. 현재는 FORMAT_TYPE=PARQUET 및 FORMAT_TYPE=DELIMITEDTEXT인 외부 파일 형식만 지원합니다.

WITH *<common_table_expression>*

CTE(공통 테이블 식)라고도 하는 임시로 이름이 지정된 결과 집합을 지정합니다. 자세한 내용은 [WITH common_table_expression (Transact-SQL)](/sql/t-sql/queries/with-common-table-expression-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)을 참조하세요.

SELECT <select_criteria>

새 테이블을 SELECT 문의 결과로 채웁니다. *select_criteria*는새 테이블에 복사할 데이터를 결정하는 SELECT 문의 본문입니다. SELECT 문에 대한 자세한 내용은 [SELECT (Transact-SQL)](/sql/t-sql/queries/select-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)을 참조하세요.

> [!NOTE]
> SELECT 문의 ORDER BY 절은 CETAS에서 지원되지 않습니다.

## <a name="permissions"></a>사용 권한

CETAS가 작동하려면 폴더 콘텐츠를 나열하고 LOCATION 폴더에 쓸 수 있는 권한이 있어야 합니다.

## <a name="examples"></a>예

다음 예제에서는 CETAS를 사용하여 연도 및 상태별로 집계한 총 인구 수를 population_ds 데이터 원본에 있는 aggregated_data 폴더에 저장합니다.

이 샘플은 이전에 만든 자격 증명, 데이터 원본 및 외부 파일 형식을 사용합니다. [외부 테이블](develop-tables-external-tables.md) 문서를 참조하세요. 같은 데이터 원본의 다른 폴더에 쿼리 결과를 저장하려면 LOCATION 인수를 변경하면 됩니다. 

다른 스토리지 계정에 결과를 저장하려면 DATA_SOURCE 인수에 대한 다른 데이터 원본을 만들어서 사용하면 됩니다.

> [!NOTE]
> 다음 샘플에서는 퍼블릭 Azure Open Data 스토리지 계정을 사용합니다. 읽기 전용입니다. 이러한 쿼리를 실행하려면 쓰기 권한이 있는 데이터 원본을 제공해야 합니다.

```sql
-- use CETAS to export select statement with OPENROWSET result to  storage
CREATE EXTERNAL TABLE population_by_year_state
WITH (
    LOCATION = 'aggregated_data/',
    DATA_SOURCE = population_ds,  
    FILE_FORMAT = census_file_format
)  
AS
SELECT decennialTime, stateName, SUM(population) AS population
FROM
    OPENROWSET(BULK 'https://azureopendatastorage.blob.core.windows.net/censusdatacontainer/release/us_population_county/year=*/*.parquet',
    FORMAT='PARQUET') AS [r]
GROUP BY decennialTime, stateName
GO

-- you can query the newly created external table
SELECT * FROM population_by_year_state
```

다음 샘플에서는 외부 테이블을 CETAS의 원본으로 사용합니다. 이 샘플은 이전에 만든 자격 증명, 데이터 원본, 외부 파일 형식 및 외부 테이블 형식을 사용합니다. [외부 테이블](develop-tables-external-tables.md) 문서를 참조하세요.

```sql
-- use CETAS with select from external table
CREATE EXTERNAL TABLE population_by_year_state
WITH (
    LOCATION = 'aggregated_data/',
    DATA_SOURCE = population_ds,  
    FILE_FORMAT = census_file_format
)  
AS
SELECT decennialTime, stateName, SUM(population) AS population
FROM census_external_table
GROUP BY decennialTime, stateName
GO

-- you can query the newly created external table
SELECT * FROM population_by_year_state
```

## <a name="supported-data-types"></a>지원되는 데이터 형식

CETAS를 사용하여 다음 SQL 데이터 형식으로 결과 세트를 저장할 수 있습니다.

- binary
- varbinary
- char
- varchar
- date
- time
- datetime2
- decimal
- numeric
- float
- real
- bigint
- int
- smallint
- tinyint
- bit

> [!NOTE]
> LOB는 CETAS와 함께 사용할 수 없습니다.

다음 데이터 형식은 CETAS의 SELECT 부분에 사용할 수 없습니다.

- nchar
- nvarchar
- Datetime
- smalldatetime
- datetimeoffset
- money
- smallmoney
- uniqueidentifier

## <a name="next-steps"></a>다음 단계

[Apache Spark for Azure Synapse 외부 테이블](develop-storage-files-spark-tables.md)을 쿼리해 보세요.
