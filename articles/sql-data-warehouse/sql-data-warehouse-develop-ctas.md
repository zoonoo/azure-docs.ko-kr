---
title: Azure SQL Data Warehouse의 CTAS(CREATE TABLE AS SELECT) | Microsoft Docs
description: 설명 및 예제 솔루션 개발을 위한 Azure SQL Data Warehouse에서 CREATE TABLE AS SELECT (CTAS) 문 사용 하 여 합니다.
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
ms.openlocfilehash: ea95a13277927b485bb9da3b75b84cce4337bf88
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/08/2019
ms.locfileid: "59280437"
---
# <a name="create-table-as-select-ctas-in-azure-sql-data-warehouse"></a>Azure SQL Data Warehouse에서 CREATE TABLE AS SELECT (CTAS)

설명 및 예제 솔루션 개발을 위한 Azure SQL Data Warehouse에서 CREATE TABLE AS SELECT (CTAS) T-SQL 문으로 코딩 합니다.

## <a name="create-table-as-select-ctas"></a>CREATE TABLE AS SELECT (CTAS)

[CTAS(CREATE TABLE AS SELECT)](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse) 문은 현재 제공되고 있는 가장 중요한 T-SQL 기능 중 하나로, CTAS는 SELECT 문의 출력을 기반으로 새 테이블을 만드는 병렬 작업입니다. CTAS는 만들고 단일 명령 사용 하 여 테이블로 데이터를 삽입 하는 간단 하 고 가장 빠른 방법입니다.

## <a name="selectinto-vs-ctas"></a>SELECT..INTO 및 CTAS

CTAS의 간주할 버전이 [선택... 에](/sql/t-sql/queries/select-into-clause-transact-sql) 문입니다.

아래는 간단한 SELECT..INTO 문의 예제입니다.

```sql
SELECT *
INTO    [dbo].[FactInternetSales_new]
FROM    [dbo].[FactInternetSales]
```

선택... 에 없도록 작업의 일부로 배포 메서드 또는 인덱스 유형을 변경할 수 있습니다. `[dbo].[FactInternetSales_new]` 기본 배포 유형 ROUND_ROBIN 및 기본 테이블 구조를 사용 하 여 클러스터형 COLUMNSTORE 인덱스로 만들어집니다.

둘 다 지정할 수는 CTAS를 사용 하 여 분포 테이블 구조 뿐만 아니라 테이블 데이터를 입력 합니다. 이전 예제를 CTAS 변환 합니다.

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
> `CTAS` 작업에서 색인만 변경하려고 하고 원본 테이블이 해시로 배포되는 경우 동일한 배포 열과 데이터 형식을 유지하면 `CTAS` 작업이 가장 잘 수행됩니다. 이렇게 하면 보다 효율적으로 작업하는 동안 배포 간 데이터 이동을 방지할 수 있습니다.

## <a name="using-ctas-to-copy-a-table"></a>CTAS를 사용하여 테이블 복사

에서는 가장 일반적인 이유 중 하나는 아마도 `CTAS` DDL을 변경 하려면 테이블의 복사본을 만드는 것입니다. 예를 들어 원래 만든 경우에 테이블로 `ROUND_ROBIN` 열에 분산 된 테이블을 변경 하려면 `CTAS` 배포 열 변경 됩니다. `CTAS` 분할 인덱싱, 또는 열 형식 변경에 사용할 수 있습니다.

기본 배포 유형을 사용 하 여이 테이블을 만든 경우를 가정해 `ROUND_ROBIN` 에서 배포 열을 지정 하지는 `CREATE TABLE`합니다.

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

이제 클러스터형 Columnstore 테이블의 성능을 활용하기 위해 클러스터형 Columnstore 인덱스로 이 테이블의 사본을 만들고자 합니다. 또한 이 테이블에 조인을 예상하기 때문에 테이블을 ProductKey로 배포하고자 하며 ProductKey에 조인하는 동안 데이터 이동을 피하려고 합니다. 마지막으로 이전 파티션을 삭제 하 여 이전 데이터를 신속 하 게 삭제할 수 있도록 OrderDateKey에 대 한 파티션을 추가 수도 있습니다. CTAS 문의 하 여 이전 테이블을 새 테이블로 복사는 다음과 같습니다.

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

이제 새 테이블을 교환해 넣고 이전 테이블을 제거하도록 테이블 이름을 변경할 수 있게 되었습니다.

```sql
RENAME OBJECT FactInternetSales TO FactInternetSales_old;
RENAME OBJECT FactInternetSales_new TO FactInternetSales;

DROP TABLE FactInternetSales_old;
```

## <a name="using-ctas-to-work-around-unsupported-features"></a>CTAS를 사용하여 지원되지 않는 기능 해결

CTAS를 사용하면 아래에 나와 있는 여러 지원되지 않는 기능과 관련된 문제도 해결할 수 있습니다. 이 방법을 종종 증명할 수 윈/윈 상황 코드 될 준수 뿐만 아니라이 더 빠르게 SQL Data Warehouse에서 실행 되는 대로 합니다. 이 성능에는 완전히 병렬화 된 디자인의 결과입니다. CTAS로 해결할 수 있는 시나리오는 다음과 같습니다.

* UPDATE에 대한 ANSI JOINS
* DELETE에 대한 ANSI JOIN
* MERGE 문

> [!NOTE]
> "CTAS를 먼저" 생각해 보십시오. 사용 하 여 문제 해결 `CTAS` 는 일반적으로 좋은 방법, 결과적으로 더 많은 데이터를 작성 하는 경우에 합니다.

## <a name="ansi-join-replacement-for-update-statements"></a>Update 문에 대한 ANSI 조인 대체

함께 ANSI 조인 구문을 사용 하 여 UPDATE 또는 DELETE를 수행 하는 두 개 이상의 테이블을 조인 하는 복잡 한 업데이트가 있는 알 수 있습니다.

이 테이블을 업데이트해야 한다고 가정해 보겠습니다.

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

원래 쿼리 했을이 예제와 같습니다.

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

SQL Data Warehouse에서 ANSI 조인을 지원 하지 않습니다 합니다 `FROM` 절을 `UPDATE` 문을 수정 하지 않고 앞의 예제를 사용할 수 없습니다.

조합을 사용할 수는 `CTAS` 와 암시적 조인의 앞의 예제를 바꾸려면:

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

경우에 따라 데이터를 삭제 하기 위한 가장 좋은 방법은 사용 하는 것 `CTAS`, 특히 `DELETE` ANSI를 사용 하는 문이 조인 구문입니다. SQL Data Warehouse에서 ANSI 조인을 지원 하지 않으므로이 합니다 `FROM` 절을 `DELETE` 문입니다. 데이터를 삭제 하는 대신 유지 하려는 데이터를 선택 합니다.

변환된 DELETE 문의 예를 아래에서 볼 수 있습니다.

```sql
CREATE TABLE dbo.DimProduct_upsert
WITH
(   Distribution=HASH(ProductKey)
,   CLUSTERED INDEX (ProductKey)
)
AS -- Select Data you wish to keep
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

CTAS를 사용하면 Merge 문을 최소한 부분적으로는 대체할 수 있습니다. 삽입 및 업데이트를 단일 문으로 결합할 수 있습니다. 삭제 된 레코드를 제한 해야는 `SELECT` 문의 결과에서 생략할 수 있습니다.

다음 예제에서는 간단한 삽입입니다.

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

## <a name="ctas-recommendation-explicitly-state-data-type-and-nullability-of-output"></a>CTAS 권장 사항: 데이터 형식 및 출력의 null 허용 여부를 명시적으로 지정

코드를 마이그레이션하는 경우이 유형의 코딩 패턴에서 실행 하 여 확인할 수 있습니다.

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

원래 이 코드를 CTAS로 마이그레이션해야 한다고 생각하는 것이 좋습니다. 그러나 여기에 숨겨진된 문제가 있습니다.

다음 코드는 동일한 결과를 산출하지 않습니다.

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

참고로 열 "결과"는 데이터 형식 및 식의 null 허용 여부 값을 전달합니다. 데이터를 끄는 형식 앞으로 발생할 수 있습니다 값에 미묘한 차이가 주의 하지 않으면.

이 예제를 시도해 보세요.

```sql
SELECT result,result*@d
from result
;

SELECT result,result*@d
from ctas_r
;
```

결과에 대해 저장된 값이 서로 다릅니다. 다른 식의 결과 열에 보관 된 값을 사용 하면 오류가 훨씬 더 심각해 합니다.

![CTAS 결과](media/sql-data-warehouse-develop-ctas/ctas-results.png)

이것이 데이터 마이그레이션에 대 한 중요 합니다. 두 번째 쿼리는 틀림 없이 더 정확 하지만 문제가 있습니다. 데이터는 원본 시스템과 비교할 때 다를 수 있으며 따라서 마이그레이션에서 무결성 문제가 발생합니다. 이는 "잘못된" 답이 실제로 정답인 드문 경우 중 하나입니다!

두 결과 간의 차이 표시 하는 이유는 암시적 캐스트 때문입니다. 첫 번째 예에서는 테이블 열 정의 정의합니다. 행이 삽입 되 면 암시적 형 변환이 발생 합니다. 두 번째 예제에서는 암시적 형식 변환이 없는 식이 열의 데이터 형식을 정의 합니다. 또한 참고로 두 번째 예제의 열은 null 허용으로 정의된 반면에 첫 번째 예제는 그렇지 않았습니다. 첫 번째 예제에서는 테이블을 만들 때 열의 null 허용 여부가 명시적으로 정의 되었습니다. 두 번째 예제에서는 왼쪽 된 식 및 기본적으로, NULL 정의가 있는 초래 합니다.

이러한 문제를 해결 하려면 명시적으로 설정 해야 형식 변환과 null 허용 여부는 CTAS 문의 SELECT 부분에서. Create table 부분에서 이러한 속성을 설정할 수 없습니다.

아래 예제에서는 코드를 수정하는 방법을 보여 줍니다.

```sql
DECLARE @d decimal(7,2) = 85.455
,       @f float(24)    = 85.455

CREATE TABLE ctas_r
WITH (DISTRIBUTION = ROUND_ROBIN)
AS
SELECT ISNULL(CAST(@d*@f AS DECIMAL(7,2)),0) as result
```

다음 사항에 유의하세요.

* CAST 또는 CONVERT가 사용되었을 수 있습니다.
* ISNULL은 COALESCE가 아닌 null 허용 여부를 강제 적용하는 데 사용됩니다.
* ISNULL은 가장 바깥쪽 함수입니다.
* ISNULL의 두 번째 부분은 상수 0

> [!NOTE]
> null 허용 여부를 올바르게 설정하려면 COALESCE가 아닌 ISNULL을 사용해야 합니다. COALESCE는 결정적 함수가 아니므로 식의 결과는 언제나 null을 허용합니다. ISNULL은 다릅니다. 이는 결정적입니다. 그러므로 ISNULL 함수의 두 번째 부분이 상수이거나 리터럴이면 결과 값은 NOT NULL이 됩니다.

계산의 무결성을 보장 테이블 파티션 전환에 대 한 중요 한 이기도 합니다. 팩트 테이블로 정의이 테이블이 있다고 가정해 보겠습니다.

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

그러나 구매량 필드는 계산 된 식입니다. 원본 데이터의 일부는 없습니다.

분할 된 데이터 집합을 만들려면 다음 코드를 사용 하려는:

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

쿼리는 전혀 문제 없이 실행될 것입니다. 문제는 파티션 전환 수행 하려고 할 때 제공 됩니다. 즉, 테이블 정의가 일치하지 않습니다. 테이블 정의 일치 하도록 CTAS를 수정 해야 추가 하는 `ISNULL` 열의 null 허용 여부 특성이 유지 하는 함수입니다.

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

형식 일관성과 CTAS에 null 허용 여부 속성을 유지 관리 하는 좋은 엔지니어링 구현 방식 임을 볼 수 있습니다. 이 방법은 계산의 무결성을 유지하는 데 도움이 되며 파티션 전환을 확실히 가능하게 해 줍니다.

참조 된 [CTAS](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse) 설명서. CTAS는 Azure SQL Data Warehouse의 가장 중요 한 문 중 하나입니다. 완전하게 이해해야 합니다.

## <a name="next-steps"></a>다음 단계

더 많은 개발 팁은 [개발 개요](sql-data-warehouse-overview-develop.md)를 참조하세요.

