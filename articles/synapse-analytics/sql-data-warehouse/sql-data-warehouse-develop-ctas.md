---
title: CREATE TABLE AS SELECT (CTAS)
description: 솔루션 개발을 위한 Synapse SQL의 CTAS(SELECT) 만들기 테이블의 설명 및 예제입니다.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 03/26/2019
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seoapril2019, azure-synapse
ms.openlocfilehash: 8e1b75dfc6a979956ff4a2868027bb769bf7c4ed
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/03/2020
ms.locfileid: "80633541"
---
# <a name="create-table-as-select-ctas"></a>CREATE TABLE AS SELECT (CTAS)

이 문서에서는 솔루션 개발을 위한 Synapse SQL의 CTAS(SELECT) T-SQL 문으로 테이블을 만듭니다. 코드 예제도 제공합니다.

## <a name="create-table-as-select"></a>CREATE TABLE AS SELECT

[CTAS(선택)로 테이블 만들기](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) 는 사용 가능한 가장 중요한 T-SQL 기능 중 하나입니다. CTAS는 SELECT 문의 출력을 기반으로 새 테이블을 만드는 병렬 작업입니다. CTAS는 단일 명령으로 데이터를 만들고 테이블에 삽입하는 가장 간단하고 빠른 방법입니다.

## <a name="selectinto-vs-ctas"></a>선택... 인투 vs. CTAS

CTAS는 SELECT의 보다 사용자 정의 가능한 [버전입니다... INTO](/sql/t-sql/queries/select-into-clause-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) 문.

다음은 간단한 SELECT의 예입니다... 에:

```sql
SELECT *
INTO    [dbo].[FactInternetSales_new]
FROM    [dbo].[FactInternetSales]
```

선택... INTO는 작업의 일부로 배포 방법 또는 인덱스 형식을 변경할 수 없습니다. ROUND_ROBIN `[dbo].[FactInternetSales_new]` 기본 배포 유형과 CLUSTERED COLUMNSTORE INDEX의 기본 테이블 구조를 사용하여 만듭니다.

반면 CTAS를 사용하면 테이블 데이터의 분포와 테이블 구조 유형을 모두 지정할 수 있습니다. 이전 예제를 CTAS로 변환하려면 다음을 수행하십시오.

```sql
CREATE TABLE [dbo].[FactInternetSales_new]
WITH
(
 DISTRIBUTION = ROUND_ROBIN
 ,CLUSTERED COLUMNSTORE INDEX
)
AS
SELECT  *
FROM    [dbo].[FactInternetSales];
```

> [!NOTE]
> CTAS 작업에서 인덱스만 변경하려고 하고 원본 테이블이 해시 분산인 경우 동일한 배포 열 및 데이터 형식을 유지 관리합니다. 이렇게 하면 작업 중에 배포 간 데이터 이동이 방지되어 보다 효율적입니다.

## <a name="use-ctas-to-copy-a-table"></a>CTAS를 사용하여 테이블 복사

CTAS의 가장 일반적인 용도 중 하나는 DDL을 변경하기 위해 테이블의 복사본을 만드는 것입니다. 테이블을 원래 로 `ROUND_ROBIN`만든 다음 열에 분산된 테이블로 변경하려고 한다고 가정해 보겠습니다. CTAS는 배포 열을 변경하는 방법입니다. CTAS를 사용하여 분할, 인덱싱 또는 열 유형을 변경할 수도 있습니다.

에서 배포 열을 지정하지 않는 기본 배포 `ROUND_ROBIN`유형 의 을 사용하여 이 테이블을 만들었다고 가정해 보겠습니다. `CREATE TABLE`

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
    CustomerPONumber nvarchar(25));
```

이제 `Clustered Columnstore Index`를 사용하여 이 테이블의 새 복사본을 만들어 클러스터된 Columnstore 테이블의 성능을 활용할 수 있습니다. 또한 이 열에 대한 `ProductKey`조인을 예상하고 조인 하는 동안 데이터 이동을 방지하려고 하기 때문에 이 테이블을 에 배포하려고 합니다. `ProductKey` 마지막으로 `OrderDateKey`에 분할을 추가하려는 경우 이전 파티션을 삭제하여 이전 데이터를 신속하게 삭제할 수 있습니다. 다음은 이전 테이블을 새 테이블로 복사하는 CTAS 문입니다.

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

마지막으로 테이블 이름을 변경하여 새 테이블에서 교환한 다음 이전 테이블을 삭제할 수 있습니다.

```sql
RENAME OBJECT FactInternetSales TO FactInternetSales_old;
RENAME OBJECT FactInternetSales_new TO FactInternetSales;

DROP TABLE FactInternetSales_old;
```

## <a name="use-ctas-to-work-around-unsupported-features"></a>CTAS를 사용하여 지원되지 않는 기능 해결

CTAS를 사용하여 아래에 나열된 지원되지 않는 여러 기능을 해결할 수도 있습니다. 이 메서드는 코드가 규정을 준수할 뿐만 아니라 Synapse SQL에서 더 빠르게 실행되는 경우가 많기 때문에 종종 유용할 수 있습니다. 이 성능은 완전히 병렬화된 설계의 결과입니다. 시나리오는 다음과 같습니다.

* UPDATE에 대한 ANSI JOINS
* DELETE에 대한 ANSI JOIN
* MERGE 문

> [!TIP]
> "CTAS 먼저"를 생각해 보십시오. 결과적으로 더 많은 데이터를 작성하는 경우에도 CTAS를 사용하여 문제를 해결하는 것이 좋습니다.

## <a name="ansi-join-replacement-for-update-statements"></a>update 문에 대한 ANSI 조인 대체

복잡한 업데이트가 있을 수 있습니다. 업데이트는 ANSI 조인 구문을 사용하여 두 개 이상의 테이블을 함께 조인하여 UPDATE 또는 DELETE를 수행합니다.

이 테이블을 업데이트해야 했다고 가정해 보십시오.

```sql
CREATE TABLE [dbo].[AnnualCategorySales]
( [EnglishProductCategoryName]    NVARCHAR(50)    NOT NULL
, [CalendarYear]                    SMALLINT        NOT NULL
, [TotalSalesAmount]                MONEY            NOT NULL
)
WITH
(
    DISTRIBUTION = ROUND_ROBIN
);
```

원래 쿼리는 다음과 같은 예제를 보았을 수 있습니다.

```sql
UPDATE    acs
SET        [TotalSalesAmount] = [fis].[TotalSalesAmount]
FROM    [dbo].[AnnualCategorySales]     AS acs
JOIN    (
        SELECT [EnglishProductCategoryName]
        , [CalendarYear]
        , SUM([SalesAmount])                AS [TotalSalesAmount]
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
AND    [acs].[CalendarYear]                = [fis].[CalendarYear];
```

Synapse SQL은 명령문 `FROM` 절에서 ANSI `UPDATE` 조인을 지원하지 않으므로 수정하지 않고는 이전 예제를 사용할 수 없습니다.

CTAS와 암시적 조인의 조합을 사용하여 이전 예제를 대체할 수 있습니다.

```sql
-- Create an interim table
CREATE TABLE CTAS_acs
WITH (DISTRIBUTION = ROUND_ROBIN)
AS
SELECT    ISNULL(CAST([EnglishProductCategoryName] AS NVARCHAR(50)),0) AS [EnglishProductCategoryName]
, ISNULL(CAST([CalendarYear] AS SMALLINT),0)  AS [CalendarYear]
, ISNULL(CAST(SUM([SalesAmount]) AS MONEY),0)  AS [TotalSalesAmount]
FROM    [dbo].[FactInternetSales]        AS s
JOIN    [dbo].[DimDate]                    AS d    ON s.[OrderDateKey]                = d.[DateKey]
JOIN    [dbo].[DimProduct]                AS p    ON s.[ProductKey]                = p.[ProductKey]
JOIN    [dbo].[DimProductSubCategory]    AS u    ON p.[ProductSubcategoryKey]    = u.[ProductSubcategoryKey]
JOIN    [dbo].[DimProductCategory]        AS c    ON u.[ProductCategoryKey]        = c.[ProductCategoryKey]
WHERE     [CalendarYear] = 2004
GROUP BY [EnglishProductCategoryName]
, [CalendarYear];

-- Use an implicit join to perform the update
UPDATE  AnnualCategorySales
SET     AnnualCategorySales.TotalSalesAmount = CTAS_ACS.TotalSalesAmount
FROM    CTAS_acs
WHERE   CTAS_acs.[EnglishProductCategoryName] = AnnualCategorySales.[EnglishProductCategoryName]
AND     CTAS_acs.[CalendarYear]  = AnnualCategorySales.[CalendarYear] ;

--Drop the interim table
DROP TABLE CTAS_acs;
```

## <a name="ansi-join-replacement-for-delete-statements"></a>Delete 문에 대한 ANSI 조인 대체

경우에 따라 데이터를 삭제하는 가장 좋은 방법은 특히 `DELETE` ANSI 조인 구문을 사용하는 문에 CTAS를 사용하는 것입니다. 이는 Synapse SQL이 명령문 절에서 ANSI 조인을 `FROM` `DELETE` 지원하지 않기 때문입니다. 데이터를 삭제하는 대신 보관할 데이터를 선택합니다.

다음은 변환된 `DELETE` 문의 예입니다.

```sql
CREATE TABLE dbo.DimProduct_upsert
WITH
(   Distribution=HASH(ProductKey)
,   CLUSTERED INDEX (ProductKey)
)
AS -- Select Data you want to keep
SELECT p.ProductKey
, p.EnglishProductName
,  p.Color
FROM  dbo.DimProduct p
RIGHT JOIN dbo.stg_DimProduct s
ON p.ProductKey = s.ProductKey;

RENAME OBJECT dbo.DimProduct TO DimProduct_old;
RENAME OBJECT dbo.DimProduct_upsert TO DimProduct;
```

## <a name="replace-merge-statements"></a>Merge 문 대체

CTAS를 사용하여 병합 문을 적어도 부분적으로 바꿀 수 있습니다. `INSERT` 와 를 단일 `UPDATE` 문으로 결합할 수 있습니다. 삭제된 레코드는 결과에서 `SELECT` 생략하도록 명령문에서 제한되어야 합니다.

다음 예제는 다음과 `UPSERT`같은 경우입니다.

```sql
CREATE TABLE dbo.[DimProduct_upsert]
WITH
(   DISTRIBUTION = HASH([ProductKey])
,   CLUSTERED INDEX ([ProductKey])
)
AS
-- New rows and new versions of rows
SELECT s.[ProductKey]
, s.[EnglishProductName]
, s.[Color]
FROM      dbo.[stg_DimProduct] AS s
UNION ALL  
-- Keep rows that are not being touched
SELECT      p.[ProductKey]
, p.[EnglishProductName]
, p.[Color]
FROM      dbo.[DimProduct] AS p
WHERE NOT EXISTS
(   SELECT  *
    FROM    [dbo].[stg_DimProduct] s
    WHERE   s.[ProductKey] = p.[ProductKey]
);

RENAME OBJECT dbo.[DimProduct]          TO [DimProduct_old];
RENAME OBJECT dbo.[DimProduct_upsert]  TO [DimProduct];
```

## <a name="explicitly-state-data-type-and-nullability-of-output"></a>데이터 형식 및 출력의 null 허용 여부를 명시적으로 지정

코드를 마이그레이션할 때 다음과 같은 유형의 코딩 패턴을 실행할 수 있습니다.

```sql
DECLARE @d decimal(7,2) = 85.455
,       @f float(24)    = 85.455

CREATE TABLE result
(result DECIMAL(7,2) NOT NULL
)
WITH (DISTRIBUTION = ROUND_ROBIN)

INSERT INTO result
SELECT @d*@f;
```

이 코드를 CTAS로 마이그레이션해야 한다고 생각할 수 있으며 올바른 것입니다. 그러나 여기에 숨겨진 문제가 있습니다.

다음 코드는 동일한 결과를 생성하지 않습니다.

```sql
DECLARE @d decimal(7,2) = 85.455
, @f float(24)    = 85.455;

CREATE TABLE ctas_r
WITH (DISTRIBUTION = ROUND_ROBIN)
AS
SELECT @d*@f as result;
```

참고로 "결과" 열은 식의 데이터 형식 및 NULL 허용 여부 값을 이월합니다. 데이터 형식을 앞으로 전달하면 주의하지 않으면 값의 미묘한 차이가 발생할 수 있습니다.

다음 예제를 시도해 보십시오.

```sql
SELECT result,result*@d
from result;

SELECT result,result*@d
from ctas_r;
```

결과에 저장된 값이 다릅니다. 결과 열의 지속된 값이 다른 식에서 사용되면 오류가 더욱 중요해집니다.

![CTAS 결과의 스크린샷](./media/sql-data-warehouse-develop-ctas/ctas-results.png)

이는 데이터 마이그레이션에 중요합니다. 두 번째 쿼리가 더 정확하더라도 문제가 있습니다. 데이터는 원본 시스템에 비해 다르며 마이그레이션의 무결성에 대한 질문으로 이어집니다. 이는 "오답"이 실제로는 정답인, 드문 경우 중 하나입니다!

두 결과 간의 차이가 발생하는 이유는 암시적 형식 캐스팅 때문입니다. 첫 번째 예제에서 테이블은 열 정의를 정의합니다. 행이 삽입되면 암시적 형식 변환이 발생합니다. 두 번째 예제에서는 식이 열의 데이터 형식을 정의하므로 암시적 형식 변환이 없습니다.

두 번째 예제의 열은 NULLable 열로 정의되었지만 첫 번째 예제에서는 열이 정의되지 않았습니다. 첫 번째 예제에서 테이블을 만들 때 열 nullability가 명시적으로 정의되었습니다. 두 번째 예제에서는 식에 남아 있으며 기본적으로 NULL 정의가 발생합니다.

이러한 문제를 해결하려면 CTAS 문의 SELECT 부분에서 형식 변환 및 null가능성을 명시적으로 설정해야 합니다. '테이블 만들기'에서는 이러한 속성을 설정할 수 없습니다.
다음 예제에서는 코드를 수정 하는 방법을 보여 줍니다.

```sql
DECLARE @d decimal(7,2) = 85.455
, @f float(24)    = 85.455

CREATE TABLE ctas_r
WITH (DISTRIBUTION = ROUND_ROBIN)
AS
SELECT ISNULL(CAST(@d*@f AS DECIMAL(7,2)),0) as result
```

다음 사항에 유의하세요.

* CAST 또는 CONVERT를 사용할 수 있습니다.
* ISNULL을 사용 하 여, 하지 유합, NULLability를 강제로. 다음 참고 를 참조하십시오.
* ISNULL은 가장 바깥쪽 함수입니다.
* ISNULL의 두 번째 부분은 상수 0입니다.

> [!NOTE]
> 무효화가 올바르게 설정되려면 ISNULL을 사용하는 것이 중요하며 COALESCE는 사용하지 않는 것이 중요합니다. COALESCE는 결정적 함수가 아니므로 식의 결과는 항상 NULLable이 됩니다. ISNULL은 다릅니다. 그것은 결정적입니다. 따라서 ISNULL 함수의 두 번째 부분이 상수 또는 리터럴인 경우 결과 값은 NULL이 아닙니다.

테이블 파티션 전환에는 계산의 무결성을 보장하는 것도 중요합니다. 이 테이블을 팩트 테이블로 정의한 경우를 가정합니다.

```sql
CREATE TABLE [dbo].[Sales]
(
    [date]      INT     NOT NULL
, [product]   INT     NOT NULL
, [store]     INT     NOT NULL
, [quantity]  INT     NOT NULL
, [price]     MONEY   NOT NULL
, [amount]    MONEY   NOT NULL
)
WITH
(   DISTRIBUTION = HASH([product])
,   PARTITION   (   [date] RANGE RIGHT FOR VALUES
                    (20000101,20010101,20020101
                    ,20030101,20040101,20050101
                    )
                )
);
```

그러나 양 필드는 계산된 식입니다. 원본 데이터의 일부가 아닙니다.

분할된 데이터 집합을 만들려면 다음 코드를 사용할 수 있습니다.

```sql
CREATE TABLE [dbo].[Sales_in]
WITH
( DISTRIBUTION = HASH([product])
, PARTITION   (   [date] RANGE RIGHT FOR VALUES
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
OPTION (LABEL = 'CTAS : Partition IN table : Create');
```

쿼리가 완벽하게 잘 실행됩니다. 파티션 스위치를 수행하려고 할 때 문제가 발생합니다. 테이블 정의가 일치하지 않습니다. 테이블 정의를 일치하게 하려면 CTAS를 수정하여 `ISNULL` 열을 nullability 특성을 보존하는 함수를 추가합니다.

```sql
CREATE TABLE [dbo].[Sales_in]
WITH
( DISTRIBUTION = HASH([product])
, PARTITION   (   [date] RANGE RIGHT FOR VALUES
                    (20000101,20010101
                    )
                )
)
AS
SELECT
  [date]
, [product]
, [store]
, [quantity]
, [price]
, ISNULL(CAST([quantity]*[price] AS MONEY),0) AS [amount]
FROM [stg].[source]
OPTION (LABEL = 'CTAS : Partition IN table : Create');
```

CTAS에서 형식 일관성 및 nullability 속성을 유지하는 것이 엔지니어링 모범 사례임을 확인할 수 있습니다. 계산의 무결성을 유지하는 데 도움이 되며 파티션 전환이 가능하도록 보장합니다.

CTAS는 시냅스 SQL에서 가장 중요한 문 중 하나입니다. 완전하게 이해해야 합니다. [CTAS 설명서를](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)참조하십시오.

## <a name="next-steps"></a>다음 단계

더 많은 개발 팁은 [개발 개요](sql-data-warehouse-overview-develop.md)를 참조하세요.
