<properties
   pageTitle="SQL 데이터 웨어하우스 | Microsoft Azure의 데이터 피벗 및 피벗 해제"
   description="솔루션 개발을 위한 Azure SQL 데이터 웨어하우스의 데이터 피벗 및 피벗 해제를 위한 팁."
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

# SQL 데이터 웨어하우스의 데이터 피벗 및 피벗 해제

CASE 문을 사용하여 SQL 데이터 웨어하우스의 데이터를 피벗할 수 있습니다.

이 문서에는 SQL Server에서 볼 수 있는 pivot 및 unpivot 구문을 사용하지 않고 테이블을 피벗 및 피벗 해제하는 방법에 관한 간단한 예제 두 개가 수록되어 있습니다.

## 피벗

```
CREATE TABLE AnnualSales_pvt
WITH    (   DISTRIBUTION = ROUND_ROBIN
        )
AS
WITH SalesPVT 
AS
(   SELECT  [EnglishProductCategoryName]
    ,       CASE WHEN [CalendarYear] = 2001 THEN SUM([SalesAmount]) ELSE 0 END AS 'CY_2001'
    ,       CASE WHEN [CalendarYear] = 2002 THEN SUM([SalesAmount]) ELSE 0 END AS 'CY_2002'
    ,       CASE WHEN [CalendarYear] = 2003 THEN SUM([SalesAmount]) ELSE 0 END AS 'CY_2003'
    ,       CASE WHEN [CalendarYear] = 2004 THEN SUM([SalesAmount]) ELSE 0 END AS 'CY_2004'
    FROM    [dbo].[FactInternetSales] s
    JOIN    [dbo].[DimDate] d               ON s.[OrderDateKey]          = d.[DateKey]
    JOIN    [dbo].[DimProduct] p            ON s.[ProductKey]            = p.[ProductKey]
    JOIN    [dbo].[DimProductSubCategory] u ON p.[ProductSubcategoryKey] = u.[ProductSubcategoryKey]
    JOIN    [dbo].[DimProductCategory] c    ON u.[ProductCategoryKey]    = c.[ProductCategoryKey]
    GROUP BY 
            [EnglishProductCategoryName]
    ,       [CalendarYear]
)
SELECT  [EnglishProductCategoryName]
,       SUM([CY_2001])  AS 'CY_2001'
,       SUM([CY_2002])  AS 'CY_2002'
,       SUM([CY_2003])  AS 'CY_2003'
,       SUM([CY_2004])  AS 'CY_2004'
FROM    SalesPVT
GROUP BY 
        [EnglishProductCategoryName]
;
```

## 피벗 해제

피벗 해제는 좀 더 복잡합니다. 그러나 `CASE`을 사용하여 쉽게 해결할 수 있습니다. 이를 위해 먼저 피벗 해제할 열 개수도 결정해야 합니다. 앞 예제에서는 열 4개를 피벗했습니다. 이 예제를 계속하겠습니다. 피벗 해제를 수행하기 위해 일시적으로 데이터 집합을 4배 단위로 증폭해야 합니다. 이 때문에 2단계 프로세스가 됩니다.

첫째, 행 4개가 포함된 임시 테이블을 만듭니다. 이 테이블을 사용하여 데이터를 증폭합니다.

```
CREATE TABLE #Nmbr
WITH 
(   DISTRIBUTION = ROUND_ROBIN
,   LOCATION     = USER_DB
)
AS
SELECT 1 AS 'Number'
UNION ALL
SELECT 2
UNION ALL
SELECT 3
UNION ALL
SELECT 4
OPTION (LABEL = 'CTAS : #Nmbr : CREATE')
;
```

두 번째 단계에서는 CASE를 사용하여 데이터를 조건부로 피벗 해제하여 집합을 다시 행으로 변환합니다. 이를 위해 첫 번째 단계에서 만든 임시 테이블 #Nmbr에 조인하여 카티션 곱을 생성해야 합니다.

```
SELECT  [EnglishProductCategoryName]
,       CASE    c.[Number]
                WHEN 1 THEN [CY_2001]
                WHEN 2 THEN [CY_2002]
                WHEN 3 THEN [CY_2003]
                WHEN 4 THEN [CY_2004]
        END as Sales
FROM AnnualSales_pvt
JOIN #Nmbr c ON 1=1
WHERE   CASE    c.[Number]
                WHEN 1 THEN CY_2001
                WHEN 2 THEN CY_2002
                WHEN 3 THEN CY_2003
                WHEN 4 THEN CY_2004
        END IS NOT NULL
OPTION (LABEL = 'Unpivot :  : SELECT')
;
```

마지막으로 반드시 임시 테이블을 삭제하여 정리를 수행해야 합니다.

```
DROP TABLE #Nmbr
;
```

## 다음 단계
더 많은 개발 팁은 [개발 개요][]를 참조하세요.

<!--Image references-->

<!--Article references-->
[개발 개요]: sql-data-warehouse-overview-develop.md

<!--MSDN references-->

<!--Other Web references-->

<!---HONumber=July15_HO4-->