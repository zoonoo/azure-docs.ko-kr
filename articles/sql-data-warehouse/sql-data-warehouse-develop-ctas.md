---
title: Azure SQL Data Warehouse의 CTAS(CREATE TABLE AS SELECT) | Microsoft Docs
description: 설명 및 솔루션 개발을 위한 Azure SQL Data Warehouse에서 CREATE TABLE AS SELECT (CTAS) 문의 예입니다.
services: sql-data-warehouse
author: mlee3gsd
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: implement
ms.date: 03/26/2019
ms.author: mlee3gsd
ms.reviewer: jrasnick
ms.custom: seoapril2019
ms.openlocfilehash: c8e9f3ccdfaee64f75443f6a4eb89a3df7c48b0e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60403747"
---
# <a name="create-table-as-select-ctas-in-azure-sql-data-warehouse"></a>Azure SQL Data Warehouse에서 CREATE TABLE AS SELECT (CTAS)

이 문서에서는 솔루션 개발을 위한 Azure SQL Data Warehouse에서 CREATE TABLE AS SELECT (CTAS) T-SQL 문을 설명 합니다. 코드 예제에 대해서도 설명 합니다.

## <a name="create-table-as-select"></a>CREATE TABLE AS SELECT

[CREATE TABLE AS SELECT](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse) (CTAS) 문은 사용할 수 있는 T-SQL 기능에서 가장 중요한 구문 중의 하나입니다. CTAS는 SELECT 문의 출력을 기반으로 새 테이블을 만드는 병렬 작업입니다. CTAS는 단일 명령을 사용하여 테이블로 데이터를 삽입하고 생성하는 간단하고 가장 빠른 방법입니다.

## <a name="selectinto-vs-ctas"></a>선택... Vs에. CTAS

[SELECT... INTO](/sql/t-sql/queries/select-into-clause-transact-sql) 문의 더 사용자 지정 가능한 버전이 CTAS 입니다.

다음은 간단한 SELECT...INTO의 예제입니다.

```sql
SELECT *
INTO    [dbo].[FactInternetSales_new]
FROM    [dbo].[FactInternetSales]
```

SELECT...INTO는 작업의 일부로 분산 방법 또는 인덱스 유형을 변경할 수 없습니다. `[dbo].[FactInternetSales_new]`를 생성하는 데 ROUND_ROBIN의 기본 분산 유형 및 클러스터형 COLUMNSTORE 인덱스의 기본 테이블 구조를 사용합니다.

다른 한편으로 CTAS를 사용하여 테이블 구조 유형뿐만 아니라 테이블 데이터의 분산을 지정할 수 있습니다. 이전 예제를 CTAS로 변환합니다.

```sql
CREATE TABLE [dbo].[FactInternetSales_new]
WITH
(
    DISTRIBUTION = ROUND_ROBIN
   ,CLUSTERED COLUMNSTORE INDEX
)
AS
SELECT  *
FROM    [dbo].[FactInternetSales]
;
```

> [!NOTE]
> CTAS 작업에서 색인만 변경하려는 경우, 원본 테이블은 해시 분산되며 동일한 배포 열과 데이터 형식을 유지합니다. 더 효율적인 이 작업을 수행하는 동안 배포 간 데이터 이동을 방지합니다.

## <a name="use-ctas-to-copy-a-table"></a>CTAS를 사용하여 테이블 복사

CTAS를 사용하는 가장 일반적인 방법은 DDL을 변경하기 위해 테이블의 복사본을 만드는 것입니다. 원래 `ROUND_ROBIN`으로 만든 테이블을 열에 분산된 테이블로 변경하려고 가정해 보겠습니다. CTAS는 배포 열을 변경하는 방법입니다. 또한 분할, 인덱싱 또는 열 유형을 변경하려면 CTAS를 사용할 수 있습니다.

`CREATE TABLE`에서 배포 열을 지정하지 않고 `ROUND_ROBIN` 기본 분산 유형을 사용하여 다음 테이블을 생성합니다.

```sql
CREATE TABLE FactInternetSales
(
    ProductKey int NOT NULL,
    OrderDateKey int NOT NULL,
    DueDateKey int NOT NULL,
    ShipDateKey int NOT NULL,
    CustomerKey int NOT NULL,
    PromotionKey int NOT NULL,
    CurrencyKey int NOT NULL,
    SalesTerritoryKey int NOT NULL,
    SalesOrderNumber nvarchar(20) NOT NULL,
    SalesOrderLineNumber tinyint NOT NULL,
    RevisionNumber tinyint NOT NULL,
    OrderQuantity smallint NOT NULL,
    UnitPrice money NOT NULL,
    ExtendedAmount money NOT NULL,
    UnitPriceDiscountPct float NOT NULL,
    DiscountAmount float NOT NULL,
    ProductStandardCost money NOT NULL,
    TotalProductCost money NOT NULL,
    SalesAmount money NOT NULL,
    TaxAmt money NOT NULL,
    Freight money NOT NULL,
    CarrierTrackingNumber nvarchar(25),
    CustomerPONumber nvarchar(25)
);
```

이 테이블의 새 복사본을 `Clustered Columnstore Index`로 생성하면 Clustered Columnstore 테이블의 성능을 이용할 수 있습니다. `ProductKey`로 조인하는 동안 데이터 이동을 피할 수 있으며 이 열로 조인을 예상하기 때문에 `ProductKey`로 테이블을 분산할 수 있습니다. 마지막으로 `OrderDateKey`로 분할을 추가하여 이전 파티션을 삭제하여 오래된 데이터를 신속하게 삭제할 수 있습니다. 이전 테이블을 새 테이블로 복사하는 CTAS 문은 다음과 같습니다.

```sql
CREATE TABLE FactInternetSales_new
WITH
(
    CLUSTERED COLUMNSTORE INDEX,
    DISTRIBUTION = HASH(ProductKey),
    PARTITION
    (
        OrderDateKey RANGE RIGHT FOR VALUES
        (
        20000101,20010101,20020101,20030101,20040101,20050101,20060101,20070101,20080101,20090101,
        20100101,20110101,20120101,20130101,20140101,20150101,20160101,20170101,20180101,20190101,
        20200101,20210101,20220101,20230101,20240101,20250101,20260101,20270101,20280101,20290101
        )
    )
)
AS SELECT * FROM FactInternetSales;
```

마지막으로, 새 테이블을 전환하기 위해 테이블 이름을 변경한 다음 이전 테이블을 삭제합니다.

```sql
RENAME OBJECT FactInternetSales TO FactInternetSales_old;
RENAME OBJECT FactInternetSales_new TO FactInternetSales;

DROP TABLE FactInternetSales_old;
```

## <a name="use-ctas-to-work-around-unsupported-features"></a>CTAS를 사용하여 지원되지 않는 기능 해결

또한 아래에 나열된 많은 지원되지 않는 기능을 해결하려면 CTAS를 사용할 수 있습니다. 코드를 준수할 뿐만 아니라 SQL Data Warehouse에서 더 빠르게 종종 실행되기 때문에 이 방법이 종종 도움이 되는 것을 입증할 수 있습니다. 이 성능은 완전히 병렬화된 디자인의 결과입니다. 시나리오에는 다음이 포함됩니다.

* UPDATE에 대한 ANSI JOINS
* DELETE에 대한 ANSI JOIN
* MERGE 문

> [!TIP]
> "첫 번째로 CTAS"를 생각하십시오. 결과적으로 데이터를 더 많이 작성하더라도 CTAS를 사용하여 문제를 해결하는 것이 일반적으로 좋은 방법입니다.

## <a name="ansi-join-replacement-for-update-statements"></a>Update 문에 대한 ANSI 조인 대체

복잡한 업데이트를 가지고 있는 것을 확인할 수 있습니다. 업데이트는 UPDATE 또는 DELETE를 수행하기 위해 ANSI 조인 구문을 사용하여 두 개 이상의 테이블을 함께 조인합니다.

다음 테이블을 업데이트해야 한다고 가정해 보겠습니다.

```sql
CREATE TABLE [dbo].[AnnualCategorySales]
(    [EnglishProductCategoryName]    NVARCHAR(50)    NOT NULL
,    [CalendarYear]                    SMALLINT        NOT NULL
,    [TotalSalesAmount]                MONEY            NOT NULL
)
WITH
(
    DISTRIBUTION = ROUND_ROBIN
)
;
```

원래 쿼리는 다음 예제와 같습니다.

```sql
UPDATE    acs
SET        [TotalSalesAmount] = [fis].[TotalSalesAmount]
FROM    [dbo].[AnnualCategorySales]     AS acs
JOIN    (
        SELECT    [EnglishProductCategoryName]
        ,        [CalendarYear]
        ,        SUM([SalesAmount])                AS [TotalSalesAmount]
        FROM    [dbo].[FactInternetSales]        AS s
        JOIN    [dbo].[DimDate]                    AS d    ON s.[OrderDateKey]                = d.[DateKey]
        JOIN    [dbo].[DimProduct]                AS p    ON s.[ProductKey]                = p.[ProductKey]
        JOIN    [dbo].[DimProductSubCategory]    AS u    ON p.[ProductSubcategoryKey]    = u.[ProductSubcategoryKey]
        JOIN    [dbo].[DimProductCategory]        AS c    ON u.[ProductCategoryKey]        = c.[ProductCategoryKey]
        WHERE     [CalendarYear] = 2004
        GROUP BY
                [EnglishProductCategoryName]
        ,        [CalendarYear]
        ) AS fis
ON    [acs].[EnglishProductCategoryName]    = [fis].[EnglishProductCategoryName]
AND    [acs].[CalendarYear]                = [fis].[CalendarYear]
;
```

SQL Data Warehouse에서 `UPDATE` 문의 `FROM` 절에서 ANSI 조인을 지원하지 않으므로 수정하지 않고 앞의 예제를 사용할 수 없습니다.

앞의 예제를 바꾸려면 CTAS와 암시적 조인의 조합을 사용할 수 있습니다.

```sql
-- Create an interim table
CREATE TABLE CTAS_acs
WITH (DISTRIBUTION = ROUND_ROBIN)
AS
SELECT    ISNULL(CAST([EnglishProductCategoryName] AS NVARCHAR(50)),0)    AS [EnglishProductCategoryName]
,        ISNULL(CAST([CalendarYear] AS SMALLINT),0)                         AS [CalendarYear]
,        ISNULL(CAST(SUM([SalesAmount]) AS MONEY),0)                        AS [TotalSalesAmount]
FROM    [dbo].[FactInternetSales]        AS s
JOIN    [dbo].[DimDate]                    AS d    ON s.[OrderDateKey]                = d.[DateKey]
JOIN    [dbo].[DimProduct]                AS p    ON s.[ProductKey]                = p.[ProductKey]
JOIN    [dbo].[DimProductSubCategory]    AS u    ON p.[ProductSubcategoryKey]    = u.[ProductSubcategoryKey]
JOIN    [dbo].[DimProductCategory]        AS c    ON u.[ProductCategoryKey]        = c.[ProductCategoryKey]
WHERE     [CalendarYear] = 2004
GROUP BY
        [EnglishProductCategoryName]
,        [CalendarYear]
;

-- Use an implicit join to perform the update
UPDATE  AnnualCategorySales
SET     AnnualCategorySales.TotalSalesAmount = CTAS_ACS.TotalSalesAmount
FROM    CTAS_acs
WHERE   CTAS_acs.[EnglishProductCategoryName] = AnnualCategorySales.[EnglishProductCategoryName]
AND     CTAS_acs.[CalendarYear]               = AnnualCategorySales.[CalendarYear]
;

--Drop the interim table
DROP TABLE CTAS_acs
;
```

## <a name="ansi-join-replacement-for-delete-statements"></a>Delete 문에 대한 ANSI 조인 대체

데이터를 삭제하기 위한 가장 좋은 방법은 CTAS를 사용하는 것이며, 특히 ANSI 조인 구문을 사용하는 `DELETE` 구문이 해당됩니다. SQL Data Warehouse는 `DELETE` 문의 `FROM` 절에서 ANSI 조인을 지원하지 않기 때문입니다. 데이터를 삭제하는 대신 유지하려는 데이터를 선택합니다.

다음은 변환된 `DELETE` 문 예제입니다.

```sql
CREATE TABLE dbo.DimProduct_upsert
WITH
(   Distribution=HASH(ProductKey)
,   CLUSTERED INDEX (ProductKey)
)
AS -- Select Data you want to keep
SELECT     p.ProductKey
,          p.EnglishProductName
,          p.Color
FROM       dbo.DimProduct p
RIGHT JOIN dbo.stg_DimProduct s
ON         p.ProductKey = s.ProductKey
;

RENAME OBJECT dbo.DimProduct        TO DimProduct_old;
RENAME OBJECT dbo.DimProduct_upsert TO DimProduct;
```

## <a name="replace-merge-statements"></a>Merge 문 대체

CTAS를 사용하여 최소한 부분적으로 merge 문을 바꿀 수 있습니다. 단일 구문으로 `INSERT`와 `UPDATE`를 결합할 수 있습니다. 삭제된 레코드를 제한하려면 `SELECT` 문으로 결과에서 생략할 수 있습니다.

`UPSERT`의 예제입니다.

```sql
CREATE TABLE dbo.[DimProduct_upsert]
WITH
(   DISTRIBUTION = HASH([ProductKey])
,   CLUSTERED INDEX ([ProductKey])
)
AS
-- New rows and new versions of rows
SELECT      s.[ProductKey]
,           s.[EnglishProductName]
,           s.[Color]
FROM      dbo.[stg_DimProduct] AS s
UNION ALL  
-- Keep rows that are not being touched
SELECT      p.[ProductKey]
,           p.[EnglishProductName]
,           p.[Color]
FROM      dbo.[DimProduct] AS p
WHERE NOT EXISTS
(   SELECT  *
    FROM    [dbo].[stg_DimProduct] s
    WHERE   s.[ProductKey] = p.[ProductKey]
)
;

RENAME OBJECT dbo.[DimProduct]          TO [DimProduct_old];
RENAME OBJECT dbo.[DimProduct_upsert]  TO [DimProduct];
```

## <a name="explicitly-state-data-type-and-nullability-of-output"></a>명시적으로 출력의 데이터 형식 및 null 허용 여부 지정

코드를 마이그레이션하는 경우 다음 코딩 패턴의 유형을 실행하여 확인할 수 있습니다.

```sql
DECLARE @d decimal(7,2) = 85.455
,       @f float(24)    = 85.455

CREATE TABLE result
(result DECIMAL(7,2) NOT NULL
)
WITH (DISTRIBUTION = ROUND_ROBIN)

INSERT INTO result
SELECT @d*@f
;
```

다음 코드를 CTAS로 마이그레이션해야 하고 수정했다고 생각할 수 있습니다. 그러나 여기에 숨겨진 문제가 있습니다.

다음 코드는 동일한 결과를 생성하지 않습니다.

```sql
DECLARE @d decimal(7,2) = 85.455
,       @f float(24)    = 85.455
;

CREATE TABLE ctas_r
WITH (DISTRIBUTION = ROUND_ROBIN)
AS
SELECT @d*@f as result
;
```

참고로 "result" 열은 데이터 형식 및 식의 null 허용 여부 값을 전달합니다. 데이터 형식을 전달할 때 주의하지 않으면 값에 미묘한 차이가 발생할 수 있습니다.

다음 예제를 시도해 보세요.

```sql
SELECT result,result*@d
from result
;

SELECT result,result*@d
from ctas_r
;
```

result에 대해 저장된 값이 서로 다릅니다. result 열에 보관된 값에 다른 식이 사용되면 더 심각한 오류가 발생됩니다.

![스크린 샷의 CTAS 결과](media/sql-data-warehouse-develop-ctas/ctas-results.png)

이것이 데이터 마이그레이션에서 중요합니다. 두 번째 쿼리는 분명히 더 정확하지만 문제가 있습니다. 데이터는 원본 시스템과 다르게 비교되어 마이그레이션에서 무결성 문제가 발생합니다. 이는 "잘못된" 답이 실제로 정답인 드문 경우 중 하나입니다!

두 결과 간의 차이를 표시하는 이유는 암시적 캐스트 때문입니다. 첫 번째 예에서는 테이블이 열 정의를 지정합니다. 행이 삽입되면 암시적 형식 변환이 발생합니다. 두 번째 예제에서는 식이 열의 데이터 형식을 정의하여 암시적 형식 변환이 없습니다.

또한 첫 번째 예와 달리 두 번째 예제의 열은 null 허용 열로 정의됩니다. 첫 번째 예제에서는 테이블을 만들 때 열의 null 허용 여부가 명시적으로 정의되었습니다. 두 번째 예제에서는 기본적으로 NULL 정의가 식으로 남겨집니다.

이러한 문제를 해결하려면 CTAS 문의 SELECT 부분에서 명시적으로 형식 변환과 null 허용 여부를 설정해야 합니다. CREATE TABLE에서 이러한 속성을 설정할 수 없습니다.
다음 예제에서는 코드를 수정 하는 방법에 설명 합니다.

```sql
DECLARE @d decimal(7,2) = 85.455
,       @f float(24)    = 85.455

CREATE TABLE ctas_r
WITH (DISTRIBUTION = ROUND_ROBIN)
AS
SELECT ISNULL(CAST(@d*@f AS DECIMAL(7,2)),0) as result
```

다음 사항에 유의하세요.

* CAST 또는 CONVERT를 사용할 수 있습니다.
* Null 허용 여부를 강제하기 위해 COALESCE가 아닌 ISNULL을 사용합니다. 다음 참고를 참조하세요.
* ISNULL은 가장 바깥쪽 함수입니다.
* ISNULL의 두 번째 부분은 상수 0입니다.

> [!NOTE]
> 가 올바르게 설정 되어 null 허용 여부, 것 ISNULL과 COALESCE 하지 사용 해야 합니다. COALESCE는 결정적 함수 및 따라서 식의 결과 항상 null을 허용 합니다. ISNULL은 다릅니다. 결정적입니다. 그러므로 ISNULL 함수의 두 번째 부분은 상수 또는 리터럴, 결과 값 됩니다 NOT NULL입니다.

계산의 무결성을 보장하는 것은 테이블 파티션 전환에서 또한 중요합니다. 팩트 테이블로 정의된 다음 테이블이 있다고 가정해 보겠습니다.

```sql
CREATE TABLE [dbo].[Sales]
(
    [date]      INT     NOT NULL
,   [product]   INT     NOT NULL
,   [store]     INT     NOT NULL
,   [quantity]  INT     NOT NULL
,   [price]     MONEY   NOT NULL
,   [amount]    MONEY   NOT NULL
)
WITH
(   DISTRIBUTION = HASH([product])
,   PARTITION   (   [date] RANGE RIGHT FOR VALUES
                    (20000101,20010101,20020101
                    ,20030101,20040101,20050101
                    )
                )
)
;
```

그러나 amount 필드는 계산된 식입니다. 원본 데이터의 일부가 아닙니다.

분할된 데이터 집합을 만들려면 다음 코드를 사용합니다.

```sql
CREATE TABLE [dbo].[Sales_in]
WITH
(   DISTRIBUTION = HASH([product])
,   PARTITION   (   [date] RANGE RIGHT FOR VALUES
                    (20000101,20010101
                    )
                )
)
AS
SELECT
    [date]
,   [product]
,   [store]
,   [quantity]
,   [price]
,   [quantity]*[price]  AS [amount]
FROM [stg].[source]
OPTION (LABEL = 'CTAS : Partition IN table : Create')
;
```

쿼리는 완벽하게 실행됩니다. 문제는 파티션을 전환하려고 할 때 발생됩니다. 테이블 정의와 일치하지 않습니다. 테이블 정의와 일치시키려면 열의 null 허용 여부 특성을 유지하는 `ISNULL` 함수를 추가하도록 CTAS를 수정합니다.

```sql
CREATE TABLE [dbo].[Sales_in]
WITH
(   DISTRIBUTION = HASH([product])
,   PARTITION   (   [date] RANGE RIGHT FOR VALUES
                    (20000101,20010101
                    )
                )
)
AS
SELECT
    [date]
,   [product]
,   [store]
,   [quantity]
,   [price]   
,   ISNULL(CAST([quantity]*[price] AS MONEY),0) AS [amount]
FROM [stg].[source]
OPTION (LABEL = 'CTAS : Partition IN table : Create');
```

형식 일관성과 CTAS에 null 허용 여부 속성을 유지하는 엔지니어링 모범 사례를 확인할 수 있습니다. 계산에서 무결성을 유지하고 또한 파티션 전환이 가능한지를 확인합니다.

CTAS는 SQL Data Warehouse의 가장 중요한 구문 중 하나입니다. 완전하게 이해하십시오. [CTAS 설명서](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse)를 확인합니다.

## <a name="next-steps"></a>다음 단계

더 많은 개발 팁은 [개발 개요](sql-data-warehouse-overview-develop.md)를 참조하세요.

