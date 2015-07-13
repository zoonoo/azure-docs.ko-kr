<properties
   pageTitle="SQL 데이터 웨어하우스 | Microsoft Azure의 CTAS(Create Table As Select)"
   description="솔루션 개발을 위해 Azure SQL 데이터 웨어하우스의 CTAS(Create Table As Select) 문으로 코딩에 대한 팁."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="jrowlandjones"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="06/26/2015"
   ms.author="JRJ@BigBangData.co.uk;barbkess"/>

# SQL 데이터 웨어하우스의 CTAS(Create Table As Select)
선택으로 테이블 만들기 또는 일명 CTAS는 사용할 수 있는 가장 중요한 T-SQL 기능 중 하나입니다. 이는 Select 문의 출력을 기반으로 새 테이블을 만드는 완전하게 병렬화된 연산입니다. CTAS를 원한다면 SELECT..INTO의 보강된 버전이라고 생각할 수 있습니다.

또한 위에 열거한 지원되지 않는 여러 기능을 해결하기 위해 CTAS를 사용할 수도 있습니다. 많은 경우 이는 코드가 규정을 준수하면서도 SQL 데이터 웨어하우스에서 더 빠르게 실행되는 윈/윈 상황으로 입증될 수 있습니다. 이는 완전히 병렬화된 디자인의 결과입니다.

> [AZURE.NOTE]"CTAS를 먼저" 생각해 보십시오. CTAS를 사용하여 문제를 해결할 수 있다고 생각한다면, 그 결과로 더 많은 데이터를 작성한다 하더라도 이는 일반적으로 최선의 문제 해결 방법입니다.

CTAS로 해결할 수 있는 시나리오는 다음과 같습니다.

- SELECT..INTO
- UPDATE에 대한 ANSI JOINS 
- DELETE에 대한 ANSI JOIN
- MERGE 문

이 문서에는 CTAS로 코딩하는 경우 몇 가지 모범 사례도 포함되어 있습니다.

## SELECT..INTO
사용자 솔루션의 여러 위치에 SELECT..INTO를 사용할 수 있습니다.

아래 SELECT..INTO 예제를 참조하세요.

```
SELECT *
INTO    #tmp_fct
FROM    [dbo].[FactInternetSales]
```

이 문을 CTAS로 변환하는 것은 매우 간단합니다.

```
CREATE TABLE #tmp_fct
WITH
(
    DISTRIBUTION = ROUND_ROBIN
,   LOCATION = USER_DB
)
AS
SELECT  *
FROM    [dbo].[FactInternetSales]
;
```

CTAS를 사용하면 데이터 배포 기본 설정 및 옵션 인덱스를 테이블로 지정할 수도 있습니다.

## Update 문에 대한 ANSI 조인 대체

UPDATE 또는 DELETE를 수행하기 위해 ANSI 조인 구문을 사용하여 둘 이상의 테이블을 조인하는 복잡한 업데이트가 있을 수 있습니다.

이 테이블을 업데이트해야 한다고 가정해 보겠습니다.

```
CREATE TABLE [dbo].[AnnualCategorySales]
(	[EnglishProductCategoryName]	NVARCHAR(50)	NOT NULL
,	[CalendarYear]					SMALLINT		NOT NULL
,	[TotalSalesAmount]				MONEY			NOT NULL
)
WITH
(
	DISTRIBUTION = ROUND_ROBIN
)
;
```

원래 쿼리는 다음과 같을 것입니다.

```
UPDATE	acs
SET		[TotalSalesAmount] = [fis].[TotalSalesAmount]
FROM	[dbo].[AnnualCategorySales] 	AS acs
JOIN	(
		SELECT	[EnglishProductCategoryName]
		,		[CalendarYear]
		,		SUM([SalesAmount])				AS [TotalSalesAmount]
		FROM	[dbo].[FactInternetSales]		AS s
		JOIN	[dbo].[DimDate]					AS d	ON s.[OrderDateKey]				= d.[DateKey]
		JOIN	[dbo].[DimProduct]				AS p	ON s.[ProductKey]				= p.[ProductKey]
		JOIN	[dbo].[DimProductSubCategory]	AS u	ON p.[ProductSubcategoryKey]	= u.[ProductSubcategoryKey]
		JOIN	[dbo].[DimProductCategory]		AS c	ON u.[ProductCategoryKey]		= c.[ProductCategoryKey]
		WHERE 	[CalendarYear] = 2004
		GROUP BY
				[EnglishProductCategoryName]
		,		[CalendarYear]
		) AS fis
ON	[acs].[EnglishProductCategoryName]	= [fis].[EnglishProductCategoryName]
AND	[acs].[CalendarYear]				= [fis].[CalendarYear]
;
```

SQL 데이터 웨어하우스는 ANSI 조인을 지원하지 않으므로 이 코드를 약간 변경해야 복사할 수 있습니다.

CTAS와 암시적 조인의 조합을 사용하여 이 코드를 대체할 수 있습니다.

```
-- Create an interim table
CREATE TABLE CTAS_acs
WITH (DISTRIBUTION = ROUND_ROBIN)
AS
SELECT	ISNULL(CAST([EnglishProductCategoryName] AS NVARCHAR(50)),0)	AS [EnglishProductCategoryName]
,		ISNULL(CAST([CalendarYear] AS SMALLINT),0) 						AS [CalendarYear]
,		ISNULL(CAST(SUM([SalesAmount]) AS MONEY),0)						AS [TotalSalesAmount]
FROM	[dbo].[FactInternetSales]		AS s
JOIN	[dbo].[DimDate]					AS d	ON s.[OrderDateKey]				= d.[DateKey]
JOIN	[dbo].[DimProduct]				AS p	ON s.[ProductKey]				= p.[ProductKey]
JOIN	[dbo].[DimProductSubCategory]	AS u	ON p.[ProductSubcategoryKey]	= u.[ProductSubcategoryKey]
JOIN	[dbo].[DimProductCategory]		AS c	ON u.[ProductCategoryKey]		= c.[ProductCategoryKey]
WHERE 	[CalendarYear] = 2004
GROUP BY
		[EnglishProductCategoryName]
,		[CalendarYear]
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

## Delete 문에 대한 ANSI 조인 대체
CTAS를 사용하여 데이터를 삭제하는 것이 최선의 방법인 경우가 있습니다. 단순히 데이터를 삭제하는 대신 유지 하려는 데이터를 선택합니다. SQL 데이터 웨어하우스에서 지원되지 않는 ANSI 조이닝 구문을 사용하는 DELETE 문의 경우 특히 이 방법을 사용해야 합니다.

변환된 DELETE 문의 예를 아래에서 볼 수 있습니다.

```
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

## Merge 문 대체
Merge 문을 최소한 일부는 CTAS를 사용하여 대체할 수 있습니다. `INSERT` 및 `UPDATE`를 단일 문으로 통합할 수 있습니다. 삭제된 모든 레코드를 두 번째 문에서 종료해야 합니다.

`UPSERT`의 예를 아래에서 볼 수 있습니다.

```
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
RENAME OBJECT dbo.[DimpProduct_upsert]  TO [DimProduct];

```

## CTAS 권장 사항: 데이터 형식 및 출력의 null 허용 여부를 명시적으로 지정

코드를 마이그레이션하는 경우 이 유형의 코딩 패턴을 볼 수 있습니다.

```
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

원래 이 코드를 CTAS로 마이그레이션해야 한다고 생각하는 것이 좋습니다. 그러나 여기에는 숨겨진 문제가 있습니다.

다음 코드는 동일한 결과를 산출하지 않습니다.

```
DECLARE @d decimal(7,2) = 85.455
,       @f float(24)    = 85.455
;

CREATE TABLE ctas_r
WITH (DISTRIBUTION = ROUND_ROBIN)
AS
SELECT @d*@f as result
;
```

참고로 열 "결과"는 데이터 형식 및 식의 null 허용 여부 값을 전달합니다. 이 때문에 주의하지 않으면 값에 미묘한 차이가 발생할 수 있습니다.

한 예로 다음을 시도합니다.

```
SELECT result,result*@d
from result
;

SELECT result,result*@d
from ctas_r
;
```

결과에 대해 저장된 값이 서로 다릅니다. 결과 열에 유지되는 값은 다른 식에 사용되므로 오류가 훨씬 더 중요해집니다.

![][1]

데이터 마이그레이션의 경우 이 값이 특히 중요합니다. 두 번째 쿼리가 더 정확한 것은 분명하지만 한 가지 문제가 있습니다. 데이터는 원본 시스템과 비교할 때 다를 수 있으며 따라서 마이그레이션에서 무결성 문제가 발생합니다. 이는 "잘못된" 답이 실제로 정답인 드문 경우 중 하나입니다!

두 결과 사이의 이러한 차이가 발생하는 이유는 암시적 형식 캐스팅에 바탕을 두고 있습니다. 첫 번째 예제에서는 테이블에 열 정의를 지정합니다. 행이 삽입될 때 암시적 형식 변환이 발생합니다. 두 번째 예제에서는 식이 해당 열의 데이터 형식을 정의하므로 암시적 형 변환이 발생하지 않습니다. 또한 참고로 두 번째 예제의 열은 null 허용으로 정의된 반면에 첫 번째 예제는 그렇지 않았습니다. 첫 번째 예제에서는 테이블이 생성될 때 열의 null 허용 여부가 명시적으로 정의되었습니다. 두 번째 예제에서는 식을 그대로 두기만 했는데, 기본적으로 이렇게 하면 null 정의가 발생할 수 있습니다.

이 문제를 해결하려면 CTAS 문의 SELECT부분에 형식 변환 및 null 허용 여부를 명시적으로 설정해야 합니다. 테이블 생성 부분에서는 이러한 속성을 설정할 수 없습니다.

아래 예제에서는 코드를 수정하는 방법을 보여 줍니다.

```
DECLARE @d decimal(7,2) = 85.455
,       @f float(24)    = 85.455

CREATE TABLE ctas_r
WITH (DISTRIBUTION = ROUND_ROBIN)
AS
SELECT ISNULL(CAST(@d*@f AS DECIMAL(7,2)),0) as result
```

유의할 사항: - CAST 또는 CONVERT가 사용되었을 수 있습니다. - ISNULL은 COALESCE가 아닌 null 허용 여부를 강제 적용하기 위해 사용됩니다. - ISNULL의 두 번째 부분은 상수, 즉 0입니다.

> [AZURE.NOTE]null 허용 여부를 올바르게 설정하려면 COALESCE가 아닌 ISNULL을 사용해야 합니다. COALESCE는 결정적 함수가 아니므로 식의 결과는 언제나 null을 허용합니다. ISNULL은 다릅니다. 이는 결정적입니다. 따라서 ISNULL 함수의 두 번째 부분이 상수 또는 리터럴이면 결과 값은 NOT NULL입니다.

이 팁은 계산의 무결성을 보장하기 위해 중요할 뿐만 아니라, 테이블 파티션 전환을 위해서도 중요합니다. 이 테이블을 사용자의 실제 정보로 정의했다고 가정해 보겠습니다.

```
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

그러나 값 필드는 원본 데이터의 일부가 아닌 계산된 식입니다.

분할된 데이터 집합을 만들려면 다음을 수행해야 합니다.

```
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

쿼리는 전혀 문제 없이 실행될 것입니다. 문제는 파티션 전환을 수행하려고 할 때 발생합니다. 즉, 테이블 정의가 일치하지 않습니다. 테이블 정의를 일치시키려면 CTAS를 수정해야 합니다.

```
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

따라서 CTAS에 대한 형식 일관성 및 null 허용 여부 속성 유지가 좋은 엔지니어링 모범 사례임을 알 수 있습니다. 이 방법은 계산의 무결성을 유지하는 데 도움이 되며 파티션 전환을 확실히 가능하게 해 줍니다.

[CTAS][] 사용에 대한 자세한 내용은 MSDN을 참조하세요. 이는 Azure SQL 데이터 웨어하우스의 매우 중요한 문 중 하나이므로, 완전하게 이해해야 합니다.

## 다음 단계
더 많은 개발 팁은 [개발 개요][]를 참조하세요.

<!--Image references-->
[1]: media/sql-data-warehouse-develop-ctas/ctas-results.png

<!--Article references-->
[개발 개요]: sql-data-warehouse-overview-develop.md

<!--MSDN references-->
[CTAS]: https://msdnstage.redmond.corp.microsoft.com/ko-kr/library/mt204041.aspx

<!--Other Web references-->

<!---HONumber=July15_HO1-->