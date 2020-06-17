---
title: SQL 주문형(미리 보기)에서 OPENROWSET를 사용하는 방법
description: 이 문서에서는 SQL 주문형(미리 보기)의 OPENROWSET 구문을 설명하고 인수 사용 방법을 알아봅니다.
services: synapse-analytics
author: filippopovic
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 05/07/2020
ms.author: fipopovi
ms.reviewer: jrasnick
ms.openlocfilehash: 9c2a2d7059e24b37b0f47d0b568a3929f296d8c6
ms.sourcegitcommit: 964af22b530263bb17fff94fd859321d37745d13
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/09/2020
ms.locfileid: "84560862"
---
# <a name="how-to-use-openrowset-with-sql-on-demand-preview"></a>SQL 주문형(미리 보기)에서 OPENROWSET를 사용하는 방법

`OPENROWSET(BULK...)` 함수를 사용하여 Azure Storage의 파일에 액세스할 수 있습니다. `OPENROWSET` 함수는 원격 데이터 소스의 콘텐츠(예: 파일)를 읽고 이 콘텐츠를 일련의 행으로 반환합니다. SQL 주문형(미리 보기) 리소스의 내부에서 OPENROWSET 대량 행 집합 공급자는 OPENROWSET 함수를 호출하고 BULK 옵션을 지정하는 방법으로 액세스됩니다.  

`OPENROWSET` 함수는 쿼리의 `FROM` 절에서 테이블 이름 `OPENROWSET`인 것처럼 참조될 수 있습니다. 이 함수는 파일의 데이터를 읽어서 행 세트로 반환할 수 있는 기본 제공 BULK 공급자를 통해 대량 작업을 지원합니다.

## <a name="data-source"></a>데이터 원본

Synapse SQL의 OPENROWSET 함수는 데이터 소스에서 파일 콘텐츠를 읽습니다. 데이터 소스는 Azure 스토리지 계정이며 `OPENROWSET` 함수에서 명시적으로 참조되거나 읽으려는 파일의 URL에서 동적으로 유추될 수 있습니다.
`OPENROWSET` 함수는 파일을 포함하는 데이터 소스를 지정하는 `DATA_SOURCE` 매개 변수를 선택적으로 포함할 수 있습니다.
- `DATA_SOURCE`가 없는 `OPENROWSET`는 `BULK` 옵션으로 지정된 URL 위치에서 파일의 콘텐츠를 직접 읽는 데 사용할 수 있습니다.

    ```sql
    SELECT *
    FROM OPENROWSET(BULK 'http://storage..../container/folder/*.parquet',
                    TYPE = 'PARQUET') AS file
    ```

이 방법은 사전 구성 없이 파일 콘텐츠를 쉽고 빠르게 읽을 수 있는 방법입니다. 이 옵션을 사용하면 기본 인증 옵션을 사용하여 스토리지에 액세스할 수 있습니다(Azure AD 로그인의 경우 Azure AD 통과, SQL 로그인의 경우 SAS 토큰). 

- `DATA_SOURCE`가 있는 `OPENROWSET`는 지정된 스토리지 계정의 파일에 액세스하는 데 사용할 수 있습니다.

    ```sql
    SELECT *
    FROM OPENROWSET(BULK '/folder/*.parquet',
                    DATA_SOURCE='storage', --> Root URL is in LOCATION of DATA SOURCE
                    TYPE = 'PARQUET') AS file
    ```


    이 옵션을 사용하면 데이터 소스에서 스토리지 계정의 위치를 구성하고 스토리지에 액세스하는 데 사용해야 하는 인증 방법을 지정할 수 있습니다. 
    
    > [!IMPORTANT]
    > `DATA_SOURCE`가 있는 `OPENROWSET`는 스토리지 파일에 쉽고 빠르게 액세스하는 방법을 제공하지만 제한된 인증 옵션을 제공합니다. 예를 들어 Azure AD 보안 주체는 [Azure AD ID](develop-storage-files-storage-access-control.md?tabs=user-identity#force-azure-ad-pass-through)를 사용해서만 파일에 액세스할 수 있으며 공개적으로 사용 가능한 파일에는 액세스할 수 없습니다. 더 강력한 인증 옵션이 필요하면 `DATA_SOURCE` 옵션을 사용하고 스토리지에 액세스하는 데 사용할 자격 증명을 정의하십시오.


## <a name="security"></a>보안

데이터베이스 사용자가 `OPENROWSET` 함수를 사용하려면 `ADMINISTER BULK OPERATIONS` 권한이 있어야 합니다.

또한 스토리지 관리자는 유효한 SAS 토큰을 제공하거나 Azure AD 보안 주체가 스토리지 파일에 액세스하도록 설정하여 사용자가 파일에 액세스할 수 있도록 해야 합니다. 스토리지 액세스 제어에 대한 자세한 내용은 [이 문서](develop-storage-files-storage-access-control.md)를 참조하세요.

`OPENROWSET`는 다음 규칙을 사용하여 스토리지 인증 방법을 결정합니다.
- `DATA_SOURCE`가 없는 `OPENROWSET`에서 인증 메커니즘은 호출자 유형에 따라 달라집니다.
  - Azure 스토리지에서 Azure AD 사용자가 기본 파일에 액세스하도록 허용하거나(예: 호출자에게 스토리지에 대한 Storage Reader 권한이 있는 경우) Synapse SQL 서비스에서 [Azure AD 통과 인증을 사용하도록 설정](develop-storage-files-storage-access-control.md#force-azure-ad-pass-through)하면 Azure AD 로그인은 자체 [Azure AD ID](develop-storage-files-storage-access-control.md?tabs=user-identity#supported-storage-authorization-types)를 사용해야만 파일에 액세스할 수 있습니다.
  - SQL 로그인도 `DATA_SOURCE` 없이 `OPENROWSET`를 사용하여 공개적으로 사용 가능한 파일, SAS 토큰으로 보호된 파일 또는 Synapse 작업 영역의 관리 ID에 액세스 할 수 있습니다. 스토리지 파일에 대한 액세스를 허용하려면 [서버 범위 자격 증명을 만들어야](develop-storage-files-storage-access-control.md#examples) 합니다. 
- `DATA_SOURCE`가 있는 `OPENROWSET`에서 인증 메커니즘은 참조된 데이터 원본에 할당된 데이터베이스 범위 자격 증명에 정의됩니다. 이 옵션을 사용하면 공개적으로 사용 가능한 스토리지에 액세스하거나 SAS 토큰, 작업 영역의 관리 ID 또는 [호출자의 Azure AD ID](develop-storage-files-storage-access-control.md?tabs=user-identity#supported-storage-authorization-types)(호출자가 Azure AD 보안 주체인 경우)를 사용하여 스토리지에 액세스할 수 있습니다. `DATA_SOURCE`가 공용이 아닌 Azure 스토리지를 참조하는 경우에는 [데이터베이스 범위 자격 증명](develop-storage-files-storage-access-control.md#examples)을 만들어서 `DATA SOURCE`에서 참조하여 스토리지 파일에 대한 액세스를 허용해야 합니다.

호출자에게 자격 증명에 대한 `REFERENCES` 권한이 있어야 이 권한을 사용하여 스토리지를 인증할 수 있습니다.

## <a name="syntax"></a>구문

```syntaxsql
--OPENROWSET syntax for reading Parquet files
OPENROWSET  
( { BULK 'unstructured_data_path' , [DATA_SOURCE = <data source name>, ]
    FORMAT='PARQUET' }  
)  
[WITH ( {'column_name' 'column_type' }) ]
[AS] table_alias(column_alias,...n)

--OPENROWSET syntax for reading delimited text files
OPENROWSET  
( { BULK 'unstructured_data_path' , [DATA_SOURCE = <data source name>, ] 
    FORMAT = 'CSV'
    [ <bulk_options> ] }  
)  
WITH ( {'column_name' 'column_type' [ 'column_ordinal'] })  
[AS] table_alias(column_alias,...n)
 
<bulk_options> ::=  
[ , FIELDTERMINATOR = 'char' ]    
[ , ROWTERMINATOR = 'char' ] 
[ , ESCAPE_CHAR = 'char' ] 
[ , FIRSTROW = 'first_row' ]     
[ , FIELDQUOTE = 'quote_characters' ]
[ , DATA_COMPRESSION = 'data_compression_method' ]
[ , PARSER_VERSION = 'parser_version' ]
```

## <a name="arguments"></a>인수

쿼리할 대상 데이터를 포함하고 있는 입력 파일에 대해 두 가지 옵션을 선택할 수 있습니다. 유효한 값은 다음과 같습니다.

- 'CSV' - 행/열 구분 기호로 구분된 텍스트 파일을 포함합니다. 모든 문자를 필드 구분 기호로 사용할 수 있습니다. TSV: FIELDTERMINATOR = tab을 예로 들 수 있습니다.

- 'PARQUET' - Parquet 형식의 이진 파일 

**'unstructured_data_path'**

데이터에 대한 경로를 설정하는 unstructured_data_path는 절대 또는 상대 경로일 수 있습니다.
- '\<prefix>://\<storage_account_path>/\<storage_path>' 형식의 절대 경로를 사용하면 사용자가 파일을 직접 읽을 수 있습니다.
- '<storage_path>' 형식의 상대 경로는 `DATA_SOURCE` 매개 변수와 함께 사용해야 하며 `EXTERNAL DATA SOURCE`에 정의된 <storage_account_path> 위치 내의 파일 패턴을 설명합니다. 

 아래에는 특정 외부 데이터 소스에 연결되는 관련 <storage account path> 값이 있습니다. 

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
    >
    
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

사용할 행 종결자를 지정합니다. 행 종결자를 지정하지 않으면 기본 종결자 중 하나가 사용됩니다. PARSER_VERSION = '1.0'의 기본 종결자는 \r\n, \n 및 \r입니다. PARSER_VERSION = '2.0'의 기본 종결자는 \r\n 및 \n입니다.

ESCAPE_CHAR = 'char'

자체 및 파일의 모든 구분 기호 값을 이스케이프하는 데 사용되는 파일의 문자를 지정합니다. 이스케이프 문자 뒤에 자체 또는 구분 기호 값 이외의 값이 있으면 값을 읽을 때 이스케이프 문자가 삭제됩니다. 

ESCAPE_CHAR 매개 변수는 FIELDQUOTE를 사용하도록 설정되었는지 여부에 관계없이 적용됩니다. 따옴표로 묶은 문자를 이스케이프하는 데 사용되지 않습니다. 따옴표로 묶은 문자는 Excel CSV 동작에 따라 큰따옴표를 사용하여 이스케이프됩니다.

FIRSTROW = 'first_row' 

로드할 첫 번째 행의 번호를 지정합니다. 기본값은 1입니다. 지정한 데이터 파일의 첫 번째 행을 나타냅니다. 행 번호는 행 종결자를 계산하여 결정됩니다. FIRSTROW는 1부터 시작합니다.

FIELDQUOTE = 'field_quote' 

CSV 파일에 따옴표 문자로 사용될 문자를 지정합니다. 지정하지 않으면 따옴표 문자(")가 사용됩니다. 

DATA_COMPRESSION = 'data_compression_method'

압축 방법을 지정합니다. 다음 압축 방법이 지원됩니다.

- org.apache.hadoop.io.compress.GzipCodec

PARSER_VERSION = 'parser_version'

파일을 읽을 때 사용할 파서 버전을 지정합니다. 현재 지원되는 CSV 파서 버전은 1.0 및 2.0입니다.

- PARSER_VERSION = '1.0'
- PARSER_VERSION = '2.0'

CSV 파서 버전 1.0은 기본값이고 기능이 풍부한 반면 2.0은 성능을 위해 빌드되었고 일부 옵션과 인코딩은 지원하지 않습니다. 

CSV 파서 버전 2.0 세부 정보:

- 일부 데이터 유형은 지원되지 않습니다.
- 최대 행 크기 제한은 8MB입니다.
- 다음 옵션은 지원되지 않습니다. DATA_COMPRESSION.
- 따옴표로 묶인 빈 문자열("")은 빈 문자열로 해석됩니다.

## <a name="examples"></a>예제

다음 예제는 population*.csv 파일에서 서수가 1과 4인 열 두 개만 반환합니다. 다음과 같이 파일에 헤더 행이 없기 때문에 첫 번째 줄에서 읽기를 시작합니다.

```sql
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
SELECT 
    TOP 1 *
FROM  
    OPENROWSET(
        BULK 'https://azureopendatastorage.blob.core.windows.net/censusdatacontainer/release/us_population_county/year=20*/*.parquet',
        FORMAT='PARQUET'
    ) AS [r]
```

파일을 나열할 수 없다는 오류가 표시되면 주문형 Synapse SQL에서 퍼블릭 스토리지에 액세스가 가능하도록 설정해야 합니다.
- SQL 로그인을 사용하는 경우에는 [퍼블릭 스토리지에 액세스할 수 있는 서버 범위 자격 증명을 생성](develop-storage-files-storage-access-control.md#examples)해야 합니다.
- Azure AD 보안 주체를 사용하여 퍼블릭 스토리지에 액세스하는 경우에는 [퍼블릭 스토리지에 액세스할 수 있는 서버 범위 자격 증명을 생성](develop-storage-files-storage-access-control.md#examples)해야 하고 [Azure AD 통과 인증](develop-storage-files-storage-access-control.md#disable-forcing-azure-ad-pass-through)을 사용하지 않도록 설정해야 합니다.

## <a name="next-steps"></a>다음 단계

더 많은 샘플을 보려면 [쿼리 데이터 스토리지 빠른 시작](query-data-storage.md)에서 `OPENROWSET를 사용하여 [CSV](query-single-csv-file.md), [PARQUET](query-parquet-files.md) 및 [JSON](query-json-files.md) 파일 형식을 읽는 방법을 알아보세요. [CETAS](develop-tables-cetas.md)를 사용하여 쿼리 결과를 Azure Storage에 저장하는 방법도 알아볼 수 있습니다.
