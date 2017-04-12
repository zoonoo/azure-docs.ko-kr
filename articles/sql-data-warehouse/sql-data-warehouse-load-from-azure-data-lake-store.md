---
title: "로드 - Azure Data Lake Store에서 SQL Data Warehouse로| Microsoft Docs"
description: "PolyBase 외부 테이블을 사용하여 Azure Data Lake Store에서 Azure SQL Data Warehouse로 데이터를 로드하는 방법에 대해 알아봅니다."
services: sql-data-warehouse
documentationcenter: NA
author: ckarst
manager: barbkess
editor: 
ms.assetid: 
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: loading
ms.date: 01/25/2017
ms.author: cakarst;barbkess
translationtype: Human Translation
ms.sourcegitcommit: a087df444c5c88ee1dbcf8eb18abf883549a9024
ms.openlocfilehash: aca0e4cfdcfb3e3ed2e69ad8153b4c965b299806
ms.lasthandoff: 03/15/2017



---
# <a name="load-data-from-azure-data-lake-store-into-sql-data-warehouse"></a>Azure Data Lake Store에서 SQL Data Warehouse로 데이터 로드
이 문서는 PolyBase를 사용하여 ADLS(Azure Data Lake Store)에서 SQL Data Warehouse로 데이터를 로드하는 데 필요한 모든 단계를 제공합니다.
외부 테이블을 사용하여 ADLS에 저장된 데이터에 대해 임시 쿼리를 실행할 수 있는 동안 모범 사례로 SQL Data Warehouse로 데이터를 가져오는 것을 권장합니다.
예상 시간: 완료해야 하는 필수 구성 요소를 가지고 있다고 가정하는 10분.
>
이 자습서에서는 다음 방법에 대해 알아봅니다.

1. 외부 데이터베이스 개체를 만들어 Azure Data Lake Store에서 로드합니다.
2. Azure Data Lake Store 디렉터리에 연결합니다.
3. Azure SQL Data Warehouse에 데이터를 로드합니다.

## <a name="before-you-begin"></a>시작하기 전에
이 자습서를 실행하려면 다음이 필요합니다.

* 서비스 간 인증에 사용할 Azure Active Directory 응용 프로그램. 만들려면 [Active Directory 인증](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-authenticate-using-active-directory)을 따릅니다.

>[!NOTE] 
> SQL Data Warehouse에서 Azure Data Lake에 연결하려면 클라이언트 ID, 키 및 Active Directory 응용 프로그램의 OAuth2.0 토큰 끝점 값이 필요합니다. 이러한 값을 가져오는 방법에 대한 세부 정보는 위의 링크에 있습니다.

* SQL Server Management Studio 또는 SQL Server Data Tools, SSMS를 다운로드하고 연결하려면 [SSMS 쿼리](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-query-ssms)를 참조하세요.

* Azure SQL Data Warehouse, 만들려면 https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-get-started-provision을 따릅니다.

* 활성화된 암호화가 없는 Azure Data Lake Store. 만들려면 https://docs.microsoft.com/azure/data-lake-store/data-lake-store-get-started-portal을 따릅니다.




## <a name="configure-the-data-source"></a>데이터 원본 구성
PolyBase는 T-SQL 외부 개체를 사용하여 외부 데이터의 위치와 특성을 정의합니다. 외부 개체는 SQL Data Warehouse 및 참조에 저장되고 데이터 th는 외부에 저장됩니다.


###  <a name="create-a-credential"></a>자격 증명 만들기
Azure Data Lake Store에 액세스하려면 다음 단계에서 사용되는 자격 증명 암호를 암호화하는 데이터베이스 마스터 키를 만들어야 합니다.
그런 다음 AAD에 서비스 주체 자격 증명 설정을 저장하는 데이터베이스 범위 자격 증명을 만듭니다. Windows Azure Storage Blob에 연결하는 데 PolyBase를 사용한 사용자의 경우 자격 증명 구문은 다릅니다.
Azure Data Lake Store에 연결하려면 **먼저** Azure Active Directory 응용 프로그램을 만들고, 액세스 키를 만들고, Azure Data Lake 리소스에 대한 액세스 권한을 응용 프로그램에 부여해야 합니다. 이러한 단계를 수행하기 위한 지침은 [여기](https://docs.microsoft.com/en-us/azure/data-lake-store/data-lake-store-authenticate-using-active-directory)에 있습니다.

```sql
-- A: Create a Database Master Key.
-- Only necessary if one does not already exist.
-- Required to encrypt the credential secret in the next step.
-- For more information on Master Key: https://msdn.microsoft.com/en-us/library/ms174382.aspx?f=255&MSPPError=-2147217396

CREATE MASTER KEY;


-- B: Create a database scoped credential
-- IDENTITY: Pass the client id and OAuth 2.0 Token Endpoint taken from your Azure Active Directory Application
-- SECRET: Provide your AAD Application Service Principal key.
-- For more information on Create Database Scoped Credential: https://msdn.microsoft.com/en-us/library/mt270260.aspx

CREATE DATABASE SCOPED CREDENTIAL ADLCredential
WITH
    IDENTITY = '<client_id>@<OAuth_2.0_Token_EndPoint>',
    SECRET = '<key>'
;

```


### <a name="create-the-external-data-source"></a>외부 데이터 원본 만들기
이 [CREATE EXTERNAL DATA SOURCE][CREATE EXTERNAL DATA SOURCE] 명령을 사용하여 데이터의 위치와 데이터의 형식을 저장합니다.
Azure Portal 및 www.portal.azure.com에서 ADL URI를 찾을 수 있습니다.

```sql
-- C: Create an external data source
-- TYPE: HADOOP - PolyBase uses Hadoop APIs to access data in Azure Data Lake Store.
-- LOCATION: Provide Azure Data Lake accountname and URI
-- CREDENTIAL: Provide the credential created in the previous step.

CREATE EXTERNAL DATA SOURCE AzureDataLakeStore
WITH (
    TYPE = HADOOP,
    LOCATION = 'adl://<AzureDataLake account_name>.azuredatalake.net',
    CREDENTIAL = ADLCredential
);
```



## <a name="configure-data-format"></a>데이터 형식 구성
ADLS에서 데이터를 가져오려면 외부 파일 형식을 지정해야 합니다. 이 명령에는 데이터를 설명하는 특정 형식 옵션이 있습니다.
다음은 파이프로 구분된 텍스트 파일인 자주 사용되는 파일 형식의 예입니다.
[외부 파일 형식 만들기][CREATE EXTERNAL FILE FORMAT]의 전체 목록은 T-SQL 설명서를 참조하세요.

```sql
-- D: Create an external file format
-- FIELD_TERMINATOR: Marks the end of each field (column) in a delimited text file
-- STRING_DELIMITER: Specifies the field terminator for data of type string in the text-delimited file.
-- DATE_FORMAT: Specifies a custom format for all date and time data that might appear in a delimited text file.
-- Use_Type_Default: Store all Missing values as NULL

CREATE EXTERNAL FILE FORMAT TextFileFormat
WITH
(   FORMAT_TYPE = DELIMITEDTEXT
,    FORMAT_OPTIONS    (   FIELD_TERMINATOR = '|'
                    ,    STRING_DELIMITER = ''
                    ,    DATE_FORMAT         = 'yyyy-MM-dd HH:mm:ss.fff'
                    ,    USE_TYPE_DEFAULT = FALSE
                    )
);
```

## <a name="create-the-external-tables"></a>외부 테이블 만들기
이제 데이터 원본과 파일 형식을 지정했으니 외부 테이블을 만들 준비가 완료되었습니다. 외부 테이블은 외부 데이터와 상호 작용하는 방식입니다. PolyBase는 재귀 디렉터리 탐색을 사용하여 location 매개 변수에서 지정한 디렉터리의 모든 하위 디렉터리에서 모든 파일을 읽습니다. 또한 다음 예제에서는 개체를 만드는 방법을 보여 줍니다. ADLS에 있는 데이터로 작업하려면 문을 사용자 지정해야 합니다.

```sql
-- D: Create an External Table
-- LOCATION: Folder under the ADLS root folder.
-- DATA_SOURCE: Specifies which Data Source Object to use.
-- FILE_FORMAT: Specifies which File Format Object to use
-- REJECT_TYPE: Specifies how you want to deal with rejected rows. Either Value or percentage of the total
-- REJECT_VALUE: Sets the Reject value based on the reject type.

-- DimProduct
CREATE EXTERNAL TABLE [dbo].[DimProduct_external] (
    [ProductKey] [int] NOT NULL,
    [ProductLabel] [nvarchar](255) NULL,
    [ProductName] [nvarchar](500) NULL
)
WITH
(
    LOCATION='/DimProduct/'
,   DATA_SOURCE = AzureDataLakeStore
,   FILE_FORMAT = TextFileFormat
,   REJECT_TYPE = VALUE
,   REJECT_VALUE = 0
)
;

```

## <a name="external-table-considerations"></a>외부 테이블 고려 사항
외부 테이블을 생성하는 것은 쉽지만 논의되어야 하는 몇 가지 미묘한 차이가 있습니다.

PolyBase를 사용하는 데이터 로드는 강력한 형식입니다. 즉, 수집되는 데이터의 각 행은 테이블 스키마 정의를 충족해야 합니다.
지정된 행이 스키마 정의와 일치하지 않는 경우 행은 로드에서 거부됩니다.

거부 형식 및 거부 값은 최종 테이블에 있어야 하는 행 수 또는 데이터의 비율을 정의하도록 합니다.
로드 중 거부 값에 도달하는 경우 로드는 실패합니다. 거부된 행의 가장 일반적인 원인은 스키마 정의 불일치입니다.
예를 들어 파일의 데이터가 문자열일 때 열이 int의 스키마로 잘못 지정된 경우 모든 행을 로드하지 못합니다.

위치는 데이터를 읽으려는 맨 위에 있는 디렉터리를 지정합니다.
이 경우 /DimProduct/ 아래에 하위 디렉터리가 있으면 PolyBase는 하위 디렉터리 내의 모든 데이터를 가져옵니다.

## <a name="load-the-data"></a>데이터 로드
Azure Data Lake Store에서 데이터를 로드하려면 [CREATE TABLE AS SELECT(Transact-SQL)][CREATE TABLE AS SELECT (Transact-SQL)] 문을 사용합니다. CTAS로 로드는 사용자가 만든 강력한 형식의 외부 테이블을 사용합니다.

CTAS는 새 테이블을 만들고 select 문의 결과와 함께 새 테이블을 정보표시합니다. CTAS는 select 문의 결과에 부합하는 동일한 열과 데이터 형식을 가지도록 새 테이블을 정의합니다. 외부 테이블에서 모든 열을 선택하는 경우 새 테이블은 외부 테이블의 열과 데이터 형식의 복제본이 됩니다.

이 예제에서는 외부 테이블 DimProduct_external에서 DimProduct라는 해시 분산 테이블을 만듭니다.

```sql

CREATE TABLE [dbo].[DimProduct]
WITH (DISTRIBUTION = HASH([ProductKey]  ) )
AS
SELECT * FROM [dbo].[DimProduct_external]
OPTION (LABEL = 'CTAS : Load [dbo].[DimProduct]');
```


## <a name="optimize-columnstore-compression"></a>Columnstore 압축을 최적화합니다.
기본적으로 SQL 데이터 웨어하우스는 클러스터형 columnstore 인덱스로 테이블을 저장합니다. 로드를 완료한 후 데이터 행 일부는 columnstore로 압축되지 않을 수 있습니다.  여기에는 다양한 이유가 있습니다. 자세한 내용은 [Columnstore 인덱스 관리][manage columnstore indexes]를 참조하세요.

로드 후 쿼리 성능과 columnstore 압축을 최적화하려면 모든 행을 압축하기 위해 columnstore 인덱스를 강제 적용할 테이블을 다시 빌드합니다.

```sql

ALTER INDEX ALL ON [dbo].[DimProduct] REBUILD;

```

Columnstore 인덱스 유지 관리에 대한 자세한 내용은 [Columnstore 인덱스 관리][manage columnstore indexes] 문서를 참조하세요.

## <a name="optimize-statistics"></a>통계를 최적화합니다.
로드 직후 단일 열 통계를 만드는 것이 가장 좋습니다. 통계에 대한 몇 가지 선택 사항이 있습니다. 예를 들어 모든 열에 단일 열 통계를 만드는 경우 모든 통계를 다시 작성하는 데 시간이 오래 걸릴 수 있습니다. 쿼리 조건자에 위치하지 않을 특정 열에 대해 알고 있다면 이들 열에 대한 통계 생성 과정은 건너뛸 수 있습니다.

단일 열 통계를 모든 테이블의 모든 열에 대해 만들기로 결정한 경우 [통계][statistics] 문서에 저장된 프로시저 코드 샘플 `prc_sqldw_create_stats`를 사용할 수 있습니다.

다음 예제는 통계를 만들기 위한 좋은 출발점이 됩니다. 차원 테이블의 각 열과 팩트 테이블의 각 조인 열의 단일 열 통계를 생성합니다. 이후 언제라도 다른 팩트 테이블 열에 단일 또는 여러 열 통계를 추가할 수 있습니다.


## <a name="achievement-unlocked"></a>목표를 달성했습니다!
이제 Azure SQL Data Warehouse에 데이터를 성공적으로 로드했습니다. 잘 하셨습니다!

##<a name="next-steps"></a>다음 단계
데이터 로드는 SQL Data Warehouse를 사용하여 데이터 웨어하우스 솔루션을 개발하는 첫 번째 단계입니다. [테이블](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-tables-overview) 및 [T-SQL](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-develop-loops.md)에서 개발 리소스를 확인하세요.


<!--Image references-->

<!--Article references-->
[Create a SQL Data Warehouse]: sql-data-warehouse-get-started-provision.md
[Load data into SQL Data Warehouse]: sql-data-warehouse-overview-load.md
[SQL Data Warehouse development overview]: sql-data-warehouse-overview-develop.md
[manage columnstore indexes]: sql-data-warehouse-tables-index.md
[Statistics]: sql-data-warehouse-tables-statistics.md
[CTAS]: sql-data-warehouse-develop-ctas.md
[label]: sql-data-warehouse-develop-label.md

<!--MSDN references-->
[CREATE EXTERNAL DATA SOURCE]: https://msdn.microsoft.com/library/dn935022.aspx
[CREATE EXTERNAL FILE FORMAT]: https://msdn.microsoft.com/library/dn935026.aspx
[CREATE TABLE AS SELECT (Transact-SQL)]: https://msdn.microsoft.com/library/mt204041.aspx
[sys.dm_pdw_exec_requests]: https://msdn.microsoft.com/library/mt203887.aspx
[REBUILD]: https://msdn.microsoft.com/library/ms188388.aspx

<!--Other Web references-->
[Microsoft Download Center]: http://www.microsoft.com/download/details.aspx?id=36433
[Load the full Contoso Retail Data Warehouse]: https://github.com/Microsoft/sql-server-samples/tree/master/samples/databases/contoso-data-warehouse/readme.md

