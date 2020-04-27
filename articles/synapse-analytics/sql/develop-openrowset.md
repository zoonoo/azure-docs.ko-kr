---
title: SQL 주문형(미리 보기)에서 OPENROWSET를 사용하는 방법
description: 이 문서에서는 SQL 주문형(미리 보기)의 OPENROWSET 구문을 설명하고 인수 사용 방법을 알아봅니다.
services: synapse-analytics
author: filippopovic
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: fipopovi
ms.reviewer: jrasnick
ms.openlocfilehash: 6325d5555b01373b148dce69731ec64896d6e1fd
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81680504"
---
# <a name="how-to-use-openrowset-with-sql-on-demand-preview"></a>SQL 주문형(미리 보기)에서 OPENROWSET를 사용하는 방법

OPENROWSET(BULK...) 함수를 사용하면 Azure Storage의 파일에 액세스할 수 있습니다. SQL 주문형(미리 보기) 리소스의 내부에서 OPENROWSET 대량 행 집합 공급자는 OPENROWSET 함수를 호출하고 BULK 옵션을 지정하는 방법으로 액세스됩니다.  

OPENROWSET 함수는 쿼리의 FROM 절에서 테이블 이름 OPENROWSET인 것처럼 참조할 수 있습니다. 이 함수는 파일의 데이터를 읽어서 행 세트로 반환할 수 있는 기본 제공 BULK 공급자를 통해 대량 작업을 지원합니다.

OPENROWSET는 현재 SQL 풀에서 지원되지 않습니다.

## <a name="syntax"></a>구문

```syntaxsql
--OPENROWSET syntax for reading Parquet files
OPENROWSET  
( { BULK 'unstructured_data_path' , 
    FORMAT='PARQUET' }  
)  
[WITH ( {'column_name' 'column_type' }) ]
[AS] table_alias(column_alias,...n)

--OPENROWSET syntax for reading delimited text files
OPENROWSET  
( { BULK 'unstructured_data_path' , 
    FORMAT = 'CSV'
    [ <bulk_options> ] }  
)  
WITH ( {'column_name' 'column_type' [ 'column_ordinal'] })  
[AS] table_alias(column_alias,...n)
 
<bulk_options> ::=  
[ , FIELDTERMINATOR = 'char' ]    
[ , ROWTERMINATOR = 'char' ] 
[ , ESCAPE_CHAR = 'char' ] 
[ , FIRSTROW = 'first_row'  ]     
[ , FIELDQUOTE = 'quote_characters']
```

## <a name="arguments"></a>인수

쿼리할 대상 데이터를 포함하고 있는 입력 파일에 대해 두 가지 옵션을 선택할 수 있습니다. 유효한 값은 다음과 같습니다.

- 'CSV' - 행/열 구분 기호로 구분된 텍스트 파일을 포함합니다. 모든 문자를 필드 구분 기호로 사용할 수 있습니다. TSV: FIELDTERMINATOR = tab을 예로 들 수 있습니다.

- 'PARQUET' - Parquet 형식의 이진 파일 

**'unstructured_data_path'**

데이터에 대한 경로를 설정하는 unstructured_data_path는 다음과 같이 구성됩니다.  
'\<prefix>://\<storage_account_path>/\<storage_path>'
 
 
 아래에서 특정 외부 데이터 원본에 연결할 관련 스토리지 계정 경로를 찾을 수 있습니다. 

| 외부 데이터 원본       | 접두사 | 스토리지 계정 경로                                 |
| -------------------------- | ------ | ---------------------------------------------------- |
| Azure Blob Storage         | https  | \<storage_account>.blob.core.windows.net             |
| Azure Data Lake Store Gen1 | https  | \<storage_account>.azuredatalakestore.net/webhdfs/v1 |
| Azure Data Lake Store Gen2 | https  | \<storage_account>.dfs.core.windows.net              |
||||

'\<storage_path>'

 읽으려는 폴더 또는 파일을 가리키는 스토리지 내의 경로입니다. 경로가 컨테이너 또는 폴더를 가리키는 경우 해당 컨테이너 또는 폴더에서 모든 파일을 읽습니다. 하위 폴더의 파일은 포함되지 않습니다. 
 
 와일드카드 문자를 사용하여 여러 파일 또는 폴더를 대상으로 지정할 수 있습니다. 여러 비연속 와일드카드 문자를 사용할 수 있습니다.
다음은 */csv/population*으로 시작하는 모든 폴더에서 *population*으로 시작하는 모든 *csv* 파일을 읽는 예제입니다.  
`https://sqlondemandstorage.blob.core.windows.net/csv/population*/population*.csv`

unstructured_data_path를 폴더로 지정하면 SQL 주문형 쿼리가 해당 폴더에서 파일을 검색합니다. 

> [!NOTE]
> Hadoop 및 PolyBase와 달리 SQL 주문형은 하위 폴더를 반환하지 않습니다. 또한 Hadoop 및 PolyBase와 달리 SQL 주문형은 파일 이름이 밑줄(_) 또는 마침표(.)로 시작하는 파일을 반환합니다.

아래 예제에서 unstructured_data_path=`https://mystorageaccount.dfs.core.windows.net/webdata/`이면 SQL 주문형 쿼리는 mydata.txt 및 _hidden.txt의 행을 반환합니다. mydata2.txt 및 mydata3.txt는 하위 폴더에 있으므로 반환되지 않습니다.

![외부 테이블에 대한 재귀적 데이터](./media/develop-openrowset/folder-traversal.png)

`[WITH ( {'column_name' 'column_type' [ 'column_ordinal'] }) ]`

WITH 절을 사용하여 파일에서 읽을 열을 지정할 수 있습니다.

- CSV 데이터 파일의 경우 모든 열을 읽으려면 열 이름과 해당 데이터 형식을 입력합니다. 열의 하위 세트를 원하는 경우 서수를 사용하여 원본 데이터 파일에서 서수를 기준으로 열을 선택합니다. 열은 서수 지정을 기준으로 바인딩됩니다. 

> [!IMPORTANT]
> CSV 파일의 경우 WITH 절이 필수입니다.
- Parquet 데이터 파일의 경우 원본 데이터 파일의 열 이름과 일치하는 열 이름을 입력합니다. 열은 이름을 기준으로 바인딩됩니다. WITH 절을 생략하면 Parquet 파일의 모든 열이 반환됩니다.

column_name은 출력 열의 이름입니다. 이 이름을 입력하면 이 이름이 원본 파일의 열 이름을 재정의합니다.

column_type은 출력 열의 데이터 형식입니다. 여기서 암시적 데이터 형식 변환이 수행됩니다.

column_ordinal은 원본 파일에 있는 열의 서수입니다. Parquet 파일은 이름을 기준으로 바인딩되므로 이 인수가 무시됩니다. 다음 예제에서는 CSV 파일의 두 번째 열만 반환합니다.

```sql
WITH (
    --[country_code] VARCHAR (5) COLLATE Latin1_General_BIN2,
    [country_name] VARCHAR (100) COLLATE Latin1_General_BIN2 2
    --[year] smallint,
    --[population] bigint
)
```

**\<bulk_options>**

FIELDTERMINATOR ='field_terminator'

사용할 필드 종결자를 지정합니다. 기본 필드 종결자는 쉼표(" **,** ")입니다.

ROWTERMINATOR ='row_terminator'`

사용할 행 종결자를 지정합니다. 기본 행 종결자는 as \r\n 같은 줄 바꿈 문자입니다.

ESCAPE_CHAR = 'char'

자체 및 파일의 모든 구분 기호 값을 이스케이프하는 데 사용되는 파일의 문자를 지정합니다. 이스케이프 문자 뒤에 자체 또는 구분 기호 값 이외의 값이 있으면 값을 읽을 때 이스케이프 문자가 삭제됩니다. 

ESCAPE_CHAR 매개 변수는 FIELDQUOTE를 사용하도록 설정되었는지 여부에 관계없이 적용됩니다. 따옴표로 묶은 문자를 이스케이프하는 데 사용되지 않습니다. 따옴표로 묶은 문자는 Excel CSV 동작에 따라 큰따옴표를 사용하여 이스케이프됩니다.

FIRSTROW = 'first_row' 

로드할 첫 번째 행의 번호를 지정합니다. 기본값은 1입니다. 지정한 데이터 파일의 첫 번째 행을 나타냅니다. 행 번호는 행 종결자를 계산하여 결정됩니다. FIRSTROW는 1부터 시작합니다.

FIELDQUOTE = 'field_quote' 

CSV 파일에 따옴표 문자로 사용될 문자를 지정합니다. 지정하지 않으면 따옴표 문자(")가 사용됩니다. 

## <a name="examples"></a>예

다음 예제는 population*.csv 파일에서 서수가 1과 4인 열 두 개만 반환합니다. 다음과 같이 파일에 헤더 행이 없기 때문에 첫 번째 줄에서 읽기를 시작합니다.

```sql
/* make sure you have credentials for storage account access created
IF EXISTS (SELECT * FROM sys.credentials WHERE name = 'https://azureopendatastorage.blob.core.windows.net/censusdatacontainer')
DROP CREDENTIAL [https://azureopendatastorage.blob.core.windows.net/censusdatacontainer]
GO

CREATE CREDENTIAL [https://azureopendatastorage.blob.core.windows.net/censusdatacontainer]  
WITH IDENTITY='SHARED ACCESS SIGNATURE',  
SECRET = ''
GO
*/

SELECT * 
FROM OPENROWSET(
        BULK 'https://sqlondemandstorage.blob.core.windows.net/csv/population/population*.csv',
        FORMAT = 'CSV',
        FIRSTROW = 1
    )
WITH (
    [country_code] VARCHAR (5) COLLATE Latin1_General_BIN2 1,
    [population] bigint 4
) AS [r]
```



다음 예제는 열 이름 및 데이터 형식을 지정하지 않고 Parquet 형식의 인구 조사 데이터 세트에서 첫 번째 행의 모든 열을 반환합니다. 

```sql
/* make sure you have credentials for storage account access created
IF EXISTS (SELECT * FROM sys.credentials WHERE name = 'https://azureopendatastorage.blob.core.windows.net/censusdatacontainer')
DROP CREDENTIAL [https://azureopendatastorage.blob.core.windows.net/censusdatacontainer]
GO

CREATE CREDENTIAL [https://azureopendatastorage.blob.core.windows.net/censusdatacontainer]  
WITH IDENTITY='SHARED ACCESS SIGNATURE',  
SECRET = ''
GO
*/

SELECT 
    TOP 1 *
FROM  
    OPENROWSET(
        BULK 'https://azureopendatastorage.blob.core.windows.net/censusdatacontainer/release/us_population_county/year=20*/*.parquet',
        FORMAT='PARQUET'
    ) AS [r]
```



## <a name="next-steps"></a>다음 단계

더 많은 샘플을 보려면 [빠른 시작](query-data-storage.md)으로 이동하거나 [CETAS](develop-tables-cetas.md)를 사용하여 쿼리 결과를 Azure Storage에 저장하세요.
