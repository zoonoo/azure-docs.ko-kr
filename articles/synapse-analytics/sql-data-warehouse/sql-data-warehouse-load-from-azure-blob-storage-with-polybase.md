---
title: Synapse SQL data warehouse에 Contoso 소매 데이터 로드
description: PolyBase 및 T-sql 명령을 사용 하 여 Contoso retail 데이터에서 Synapse SQL로 두 개의 테이블을 로드 합니다.
services: synapse-analytics
author: kevinvngo
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 04/17/2018
ms.author: kevin
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 90da35b76bbe6ec933b3a1fd200f0f5bad643759
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85213315"
---
# <a name="load-contoso-retail-data-to-synapse-sql"></a>Synapse SQL에 Contoso retail 데이터 로드 

이 자습서에서는 PolyBase 및 T-sql 명령을 사용 하 여 Contoso retail 데이터에서 Synapse SQL data warehouse로 두 개의 테이블을 로드 하는 방법에 대해 알아봅니다.

이 자습서에서는 다음 작업을 수행합니다.

1. Azure Blob Storage에서 로드하기 위한 PolyBase 구성
2. 공용 데이터를 데이터베이스에 로드
3. 로드가 완료 된 후에 최적화를 수행합니다.

## <a name="before-you-begin"></a>시작하기 전에

이 자습서를 실행 하려면 Synapse SQL data warehouse가 이미 있는 Azure 계정이 필요 합니다. 프로 비전 된 데이터 웨어하우스가 없는 경우 [데이터 웨어하우스 만들기 및 서버 수준 방화벽 규칙 설정](create-data-warehouse-portal.md)을 참조 하세요.

## <a name="configure-the-data-source"></a>데이터 원본 구성

PolyBase는 T-SQL 외부 개체를 사용하여 외부 데이터의 위치와 특성을 정의합니다. 외부 개체 정의는 Synapse SQL data warehouse에 저장 됩니다. 데이터는 외부에 저장 됩니다.

## <a name="create-a-credential"></a>자격 증명 만들기

Contoso 공용 데이터를 로드 하는 경우 **이 단계를 건너뜁니다** . 사용자가 이미 액세스할 수 있으므로 공용 데이터에 안전 하 게 액세스할 필요가 없습니다.

사용자 고유의 데이터를 로드 하기 위한 템플릿으로이 자습서를 사용 하는 경우 **이 단계를 건너뛰지 마십시오** . 자격 증명을 통해 데이터에 액세스 하려면 다음 스크립트를 사용 하 여 데이터베이스 범위 자격 증명을 만듭니다. 그런 다음 데이터 원본의 위치를 정의할 때이를 사용 합니다.

```sql
-- A: Create a master key.
-- Only necessary if one does not already exist.
-- Required to encrypt the credential secret in the next step.

CREATE MASTER KEY;


-- B: Create a database scoped credential
-- IDENTITY: Provide any string, it is not used for authentication to Azure storage.
-- SECRET: Provide your Azure storage account key.


CREATE DATABASE SCOPED CREDENTIAL AzureStorageCredential
WITH
    IDENTITY = 'user',
    SECRET = '<azure_storage_account_key>'
;


-- C: Create an external data source
-- TYPE: HADOOP - PolyBase uses Hadoop APIs to access data in Azure blob storage.
-- LOCATION: Provide Azure storage account name and blob container name.
-- CREDENTIAL: Provide the credential created in the previous step.

CREATE EXTERNAL DATA SOURCE AzureStorage
WITH (
    TYPE = HADOOP,
    LOCATION = 'wasbs://<blob_container_name>@<azure_storage_account_name>.blob.core.windows.net',
    CREDENTIAL = AzureStorageCredential
);
```

## <a name="create-the-external-data-source"></a>외부 데이터 원본 만들기

이 [CREATE EXTERNAL DATA SOURCE](/sql/t-sql/statements/create-external-data-source-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) 명령을 사용 하 여 데이터의 위치와 데이터 형식을 저장 합니다.

```sql
CREATE EXTERNAL DATA SOURCE AzureStorage_west_public
WITH
(  
    TYPE = Hadoop
,   LOCATION = 'wasbs://contosoretaildw-tables@contosoretaildw.blob.core.windows.net/'
);
```

> [!IMPORTANT]
> Azure Blob Storage 컨테이너를 공용으로 만들도록 선택하는 경우 데이터가 데이터 센터에서 떠날 때 데이터 소유자로서 귀하에게 데이터 송신 요금이 부과될 것임을 염두에 두어야 합니다.

## <a name="configure-the-data-format"></a>데이터 형식 구성

데이터는 Azure Blob Storage에 텍스트 파일로 저장되고 각 필드는 구분 기호로 구분됩니다. SSMS에서 다음 CREATE EXTERNAL FILE FORMAT 명령을 실행 하 여 텍스트 파일의 데이터 형식을 지정 합니다. Contoso 데이터는 압축되어 있지 않으며 파이프로 구분됩니다.

```sql
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

## <a name="create-the-schema-for-the-external-tables"></a>외부 테이블에 대 한 스키마 만들기

이제 데이터 원본 및 파일 형식을 지정 했으므로 외부 테이블에 대 한 스키마를 만들 준비가 되었습니다.

데이터베이스에 Contoso 데이터를 저장할 수 있는 위치를 만들려면 스키마를 생성합니다.

```sql
CREATE SCHEMA [asb]
GO
```

## <a name="create-the-external-tables"></a>외부 테이블 만들기

다음 스크립트를 실행 하 여 FactOnlineSales 및 외부 테이블을 만듭니다. 여기에서 수행 하는 작업은 열 이름과 데이터 형식을 정의 하 고 Azure blob 저장소 파일의 위치 및 형식에 바인딩하는 것입니다. 정의가 데이터 웨어하우스에 저장 되 고 데이터가 계속 Azure Storage Blob에 있습니다.

**위치** 매개 변수는 Azure Storage Blob의 루트 폴더 아래에 있는 폴더입니다. 각 테이블은 서로 다른 폴더에 있습니다.

```sql
--DimProduct
CREATE EXTERNAL TABLE [asb].DimProduct (
    [ProductKey] [int] NOT NULL,
    [ProductLabel] [nvarchar](255) NULL,
    [ProductName] [nvarchar](500) NULL,
    [ProductDescription] [nvarchar](400) NULL,
    [ProductSubcategoryKey] [int] NULL,
    [Manufacturer] [nvarchar](50) NULL,
    [BrandName] [nvarchar](50) NULL,
    [ClassID] [nvarchar](10) NULL,
    [ClassName] [nvarchar](20) NULL,
    [StyleID] [nvarchar](10) NULL,
    [StyleName] [nvarchar](20) NULL,
    [ColorID] [nvarchar](10) NULL,
    [ColorName] [nvarchar](20) NOT NULL,
    [Size] [nvarchar](50) NULL,
    [SizeRange] [nvarchar](50) NULL,
    [SizeUnitMeasureID] [nvarchar](20) NULL,
    [Weight] [float] NULL,
    [WeightUnitMeasureID] [nvarchar](20) NULL,
    [UnitOfMeasureID] [nvarchar](10) NULL,
    [UnitOfMeasureName] [nvarchar](40) NULL,
    [StockTypeID] [nvarchar](10) NULL,
    [StockTypeName] [nvarchar](40) NULL,
    [UnitCost] [money] NULL,
    [UnitPrice] [money] NULL,
    [AvailableForSaleDate] [datetime] NULL,
    [StopSaleDate] [datetime] NULL,
    [Status] [nvarchar](7) NULL,
    [ImageURL] [nvarchar](150) NULL,
    [ProductURL] [nvarchar](150) NULL,
    [ETLLoadID] [int] NULL,
    [LoadDate] [datetime] NULL,
    [UpdateDate] [datetime] NULL
)
WITH
(
    LOCATION='/DimProduct/'
,   DATA_SOURCE = AzureStorage_west_public
,   FILE_FORMAT = TextFileFormat
,   REJECT_TYPE = VALUE
,   REJECT_VALUE = 0
)
;

--FactOnlineSales
CREATE EXTERNAL TABLE [asb].FactOnlineSales
(
    [OnlineSalesKey] [int]  NOT NULL,
    [DateKey] [datetime] NOT NULL,
    [StoreKey] [int] NOT NULL,
    [ProductKey] [int] NOT NULL,
    [PromotionKey] [int] NOT NULL,
    [CurrencyKey] [int] NOT NULL,
    [CustomerKey] [int] NOT NULL,
    [SalesOrderNumber] [nvarchar](20) NOT NULL,
    [SalesOrderLineNumber] [int] NULL,
    [SalesQuantity] [int] NOT NULL,
    [SalesAmount] [money] NOT NULL,
    [ReturnQuantity] [int] NOT NULL,
    [ReturnAmount] [money] NULL,
    [DiscountQuantity] [int] NULL,
    [DiscountAmount] [money] NULL,
    [TotalCost] [money] NOT NULL,
    [UnitCost] [money] NULL,
    [UnitPrice] [money] NULL,
    [ETLLoadID] [int] NULL,
    [LoadDate] [datetime] NULL,
    [UpdateDate] [datetime] NULL
)
WITH
(
    LOCATION='/FactOnlineSales/'
,   DATA_SOURCE = AzureStorage_west_public
,   FILE_FORMAT = TextFileFormat
,   REJECT_TYPE = VALUE
,   REJECT_VALUE = 0
)
;
```

## <a name="load-the-data"></a>데이터 로드

외부 데이터에 액세스 하는 방법에는 여러 가지가 있습니다.  외부 테이블에서 직접 데이터를 쿼리하거나 데이터 웨어하우스의 새 테이블에 데이터를 로드 하거나 기존 데이터 웨어하우스 테이블에 외부 데이터를 추가할 수 있습니다.  

### <a name="create-a-new-schema"></a>새 스키마를 만듭니다.

CTAS는 데이터가 포함된 새 테이블을 만듭니다.  먼저 contoso 데이터에 대한 스키마를 만듭니다.

```sql
CREATE SCHEMA [cso]
GO
```

### <a name="load-the-data-into-new-tables"></a>데이터를 새 테이블에 로드합니다.

Azure blob storage에서 데이터 웨어하우스 테이블로 데이터를 로드 하려면 [SELECT (transact-sql)](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) 문을 사용 하 여 CREATE TABLE 합니다. [Ctas](../sql-data-warehouse/sql-data-warehouse-develop-ctas.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) 를 사용 하 여 로드 하면 직접 만든 강력한 형식의 외부 테이블을 활용 합니다. 새 테이블에 데이터를 로드 하려면 테이블당 하나의 CTAS 문을 사용 합니다.

CTAS는 새 테이블을 만들고 select 문의 결과와 함께 새 테이블을 정보표시합니다. CTAS는 select 문의 결과에 부합하는 동일한 열과 데이터 형식을 가지도록 새 테이블을 정의합니다. 외부 테이블에서 모든 열을 선택하는 경우 새 테이블은 외부 테이블의 열과 데이터 형식의 복제본이 됩니다.

이 예제에서는 차원과 해시 분산 테이블로서 팩트 테이블을 만듭니다.

```sql
SELECT GETDATE();
GO

CREATE TABLE [cso].[DimProduct]            WITH (DISTRIBUTION = HASH([ProductKey]  ) ) AS SELECT * FROM [asb].[DimProduct]             OPTION (LABEL = 'CTAS : Load [cso].[DimProduct]             ');
CREATE TABLE [cso].[FactOnlineSales]       WITH (DISTRIBUTION = HASH([ProductKey]  ) ) AS SELECT * FROM [asb].[FactOnlineSales]        OPTION (LABEL = 'CTAS : Load [cso].[FactOnlineSales]        ');
```

### <a name="track-the-load-progress"></a>로드 진행률 추적

DMV(동적 관리 뷰)를 사용하여 로드 진행률을 추적할 수 있습니다.

```sql
-- To see all requests
SELECT * FROM sys.dm_pdw_exec_requests;

-- To see a particular request identified by its label
SELECT * FROM sys.dm_pdw_exec_requests as r
WHERE r.[label] = 'CTAS : Load [cso].[DimProduct]             '
      OR r.[label] = 'CTAS : Load [cso].[FactOnlineSales]        '
;

-- To track bytes and files
SELECT
    r.command,
    s.request_id,
    r.status,
    count(distinct input_name) as nbr_files,
    sum(s.bytes_processed)/1024/1024/1024 as gb_processed
FROM
    sys.dm_pdw_exec_requests r
    inner join sys.dm_pdw_dms_external_work s
        on r.request_id = s.request_id
WHERE
    r.[label] = 'CTAS : Load [cso].[DimProduct]             '
    OR r.[label] = 'CTAS : Load [cso].[FactOnlineSales]        '
GROUP BY
    r.command,
    s.request_id,
    r.status
ORDER BY
    nbr_files desc,
    gb_processed desc;
```

## <a name="optimize-columnstore-compression"></a>Columnstore 압축을 최적화합니다.

기본적으로 Synapse SQL data warehouse는 클러스터형 columnstore 인덱스로 테이블을 저장 합니다. 로드를 완료한 후 데이터 행 일부는 columnstore로 압축되지 않을 수 있습니다.  이 문제가 발생 하는 이유는 여러 가지가 있습니다. 자세한 내용은 [Columnstore 인덱스 관리](sql-data-warehouse-tables-index.md)를 참조하세요.

로드 후 쿼리 성능과 columnstore 압축을 최적화하려면 모든 행을 압축하기 위해 columnstore 인덱스를 강제 적용할 테이블을 다시 빌드합니다.

```sql
SELECT GETDATE();
GO

ALTER INDEX ALL ON [cso].[DimProduct]               REBUILD;
ALTER INDEX ALL ON [cso].[FactOnlineSales]          REBUILD;
```

Columnstore 인덱스 유지 관리에 대한 자세한 내용은 [columnstore 인덱스 관리](sql-data-warehouse-tables-index.md) 문서를 참조하세요.

## <a name="optimize-statistics"></a>통계를 최적화합니다.

로드 직후 단일 열 통계를 만드는 것이 가장 좋습니다. 특정 열이 쿼리 조건자에 표시 되지 않는 것을 알고 있는 경우 해당 열에 대 한 통계 만들기를 건너뛸 수 있습니다. 모든 열에 단일 열 통계를 만드는 경우 모든 통계를 다시 작성 하는 데 시간이 오래 걸릴 수 있습니다.

단일 열 통계를 모든 테이블의 모든 열에 대해 만들기로 결정한 경우 [통계](sql-data-warehouse-tables-statistics.md) 문서에 저장된 프로시저 코드 샘플 `prc_sqldw_create_stats`를 사용할 수 있습니다.

다음 예제는 통계를 만들기 위한 좋은 출발점이 됩니다. 차원 테이블의 각 열과 팩트 테이블의 각 조인 열의 단일 열 통계를 생성합니다. 이후 언제라도 다른 팩트 테이블 열에 단일 또는 여러 열 통계를 추가할 수 있습니다.

```sql
CREATE STATISTICS [stat_cso_DimProduct_AvailableForSaleDate] ON [cso].[DimProduct]([AvailableForSaleDate]);
CREATE STATISTICS [stat_cso_DimProduct_BrandName] ON [cso].[DimProduct]([BrandName]);
CREATE STATISTICS [stat_cso_DimProduct_ClassID] ON [cso].[DimProduct]([ClassID]);
CREATE STATISTICS [stat_cso_DimProduct_ClassName] ON [cso].[DimProduct]([ClassName]);
CREATE STATISTICS [stat_cso_DimProduct_ColorID] ON [cso].[DimProduct]([ColorID]);
CREATE STATISTICS [stat_cso_DimProduct_ColorName] ON [cso].[DimProduct]([ColorName]);
CREATE STATISTICS [stat_cso_DimProduct_ETLLoadID] ON [cso].[DimProduct]([ETLLoadID]);
CREATE STATISTICS [stat_cso_DimProduct_ImageURL] ON [cso].[DimProduct]([ImageURL]);
CREATE STATISTICS [stat_cso_DimProduct_LoadDate] ON [cso].[DimProduct]([LoadDate]);
CREATE STATISTICS [stat_cso_DimProduct_Manufacturer] ON [cso].[DimProduct]([Manufacturer]);
CREATE STATISTICS [stat_cso_DimProduct_ProductDescription] ON [cso].[DimProduct]([ProductDescription]);
CREATE STATISTICS [stat_cso_DimProduct_ProductKey] ON [cso].[DimProduct]([ProductKey]);
CREATE STATISTICS [stat_cso_DimProduct_ProductLabel] ON [cso].[DimProduct]([ProductLabel]);
CREATE STATISTICS [stat_cso_DimProduct_ProductName] ON [cso].[DimProduct]([ProductName]);
CREATE STATISTICS [stat_cso_DimProduct_ProductSubcategoryKey] ON [cso].[DimProduct]([ProductSubcategoryKey]);
CREATE STATISTICS [stat_cso_DimProduct_ProductURL] ON [cso].[DimProduct]([ProductURL]);
CREATE STATISTICS [stat_cso_DimProduct_Size] ON [cso].[DimProduct]([Size]);
CREATE STATISTICS [stat_cso_DimProduct_SizeRange] ON [cso].[DimProduct]([SizeRange]);
CREATE STATISTICS [stat_cso_DimProduct_SizeUnitMeasureID] ON [cso].[DimProduct]([SizeUnitMeasureID]);
CREATE STATISTICS [stat_cso_DimProduct_Status] ON [cso].[DimProduct]([Status]);
CREATE STATISTICS [stat_cso_DimProduct_StockTypeID] ON [cso].[DimProduct]([StockTypeID]);
CREATE STATISTICS [stat_cso_DimProduct_StockTypeName] ON [cso].[DimProduct]([StockTypeName]);
CREATE STATISTICS [stat_cso_DimProduct_StopSaleDate] ON [cso].[DimProduct]([StopSaleDate]);
CREATE STATISTICS [stat_cso_DimProduct_StyleID] ON [cso].[DimProduct]([StyleID]);
CREATE STATISTICS [stat_cso_DimProduct_StyleName] ON [cso].[DimProduct]([StyleName]);
CREATE STATISTICS [stat_cso_DimProduct_UnitCost] ON [cso].[DimProduct]([UnitCost]);
CREATE STATISTICS [stat_cso_DimProduct_UnitOfMeasureID] ON [cso].[DimProduct]([UnitOfMeasureID]);
CREATE STATISTICS [stat_cso_DimProduct_UnitOfMeasureName] ON [cso].[DimProduct]([UnitOfMeasureName]);
CREATE STATISTICS [stat_cso_DimProduct_UnitPrice] ON [cso].[DimProduct]([UnitPrice]);
CREATE STATISTICS [stat_cso_DimProduct_UpdateDate] ON [cso].[DimProduct]([UpdateDate]);
CREATE STATISTICS [stat_cso_DimProduct_Weight] ON [cso].[DimProduct]([Weight]);
CREATE STATISTICS [stat_cso_DimProduct_WeightUnitMeasureID] ON [cso].[DimProduct]([WeightUnitMeasureID]);
CREATE STATISTICS [stat_cso_FactOnlineSales_CurrencyKey] ON [cso].[FactOnlineSales]([CurrencyKey]);
CREATE STATISTICS [stat_cso_FactOnlineSales_CustomerKey] ON [cso].[FactOnlineSales]([CustomerKey]);
CREATE STATISTICS [stat_cso_FactOnlineSales_DateKey] ON [cso].[FactOnlineSales]([DateKey]);
CREATE STATISTICS [stat_cso_FactOnlineSales_OnlineSalesKey] ON [cso].[FactOnlineSales]([OnlineSalesKey]);
CREATE STATISTICS [stat_cso_FactOnlineSales_ProductKey] ON [cso].[FactOnlineSales]([ProductKey]);
CREATE STATISTICS [stat_cso_FactOnlineSales_PromotionKey] ON [cso].[FactOnlineSales]([PromotionKey]);
CREATE STATISTICS [stat_cso_FactOnlineSales_StoreKey] ON [cso].[FactOnlineSales]([StoreKey]);
```

## <a name="achievement-unlocked"></a>목표를 달성했습니다!

데이터 웨어하우스에 공용 데이터를 성공적으로 로드 했습니다. 잘 하셨습니다!

이제 테이블 쿼리를 시작 하 여 데이터를 탐색할 수 있습니다. 다음 쿼리를 실행 하 여 브랜드 별 총 판매량을 확인할 수 있습니다.

```sql
SELECT  SUM(f.[SalesAmount]) AS [sales_by_brand_amount]
,       p.[BrandName]
FROM    [cso].[FactOnlineSales] AS f
JOIN    [cso].[DimProduct]      AS p ON f.[ProductKey] = p.[ProductKey]
GROUP BY p.[BrandName]
```

## <a name="next-steps"></a>다음 단계

전체 데이터 집합을 로드 하려면 예제 Microsoft SQL Server 샘플 리포지토리에서 [전체 Contoso 소매 데이터 웨어하우스 로드](https://github.com/Microsoft/sql-server-samples/tree/master/samples/databases/contoso-data-warehouse/readme.md) 를 실행 합니다.
더 많은 개발 팁은 [데이터 웨어하우스의 디자인 결정 및 코딩 기법](sql-data-warehouse-overview-develop.md)을 참조 하세요.
