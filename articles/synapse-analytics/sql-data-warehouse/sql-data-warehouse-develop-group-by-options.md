---
title: 옵션으로 그룹화 사용
description: Azure Synapse Analytics의 전용 SQL 풀에 대해 옵션으로 그룹화를 구현하기 위한 팁입니다.
services: synapse-analytics
author: MSTehrani
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 04/17/2018
ms.author: emtehran
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: 3f0879aa9b6f9e084d0c51f0bb371740d333c1b6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98683259"
---
# <a name="group-by-options-for-dedicated-sql-pools-in-azure-synapse-analytics"></a>Azure Synapse Analytics의 전용 SQL 풀에 대한 옵션으로 그룹화

이 문서에서는 전용 SQL 풀에서 옵션으로 그룹화를 구현하기 위한 팁을 찾을 수 있습니다.

## <a name="what-does-group-by-do"></a>GROUP BY의 기능

[GROUP BY](/sql/t-sql/queries/select-group-by-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) T-SQL 절을 사용하여 데이터를 요약 행 집합으로 집계합니다. GROUP BY에는 전용 SQL 풀에서 지원하지 않는 몇 가지 옵션이 있습니다. 이러한 옵션에는 다음과 같은 해결 방법이 있습니다.

* GROUP BY with ROLLUP
* GROUPING SETS
* GROUP BY with CUBE

## <a name="rollup-and-grouping-sets-options"></a>롤업 및 그룹화 집합 옵션

여기서 가장 간단한 옵션은 명시적 구문에 의존하지 않고 UNION ALL을 사용하여 롤업을 수행하는 것입니다. 결과는 완전히 동일합니다.

다음은 ROLLUP 옵션과 함께GROUP BY 문을 사용하는 예제입니다.

```sql
SELECT [SalesTerritoryCountry]
,      [SalesTerritoryRegion]
,      SUM(SalesAmount)             AS TotalSalesAmount
FROM  dbo.factInternetSales s
JOIN  dbo.DimSalesTerritory t       ON s.SalesTerritoryKey       = t.SalesTerritoryKey
GROUP BY ROLLUP (
                        [SalesTerritoryCountry]
                ,       [SalesTerritoryRegion]
                )
;
```

ROLLUP을 사용하여, 앞의 예제는 다음과 같은 집계를 요청합니다.

* 국가 및 지역
* 국가
* 총합계

ROLLUP을 대체하고 동일한 결과를 반환하려면 UNION ALL을 사용하고 필요한 집계를 명시적으로 지정합니다.

```sql
SELECT [SalesTerritoryCountry]
,      [SalesTerritoryRegion]
,      SUM(SalesAmount) AS TotalSalesAmount
FROM  dbo.factInternetSales s
JOIN  dbo.DimSalesTerritory t     ON s.SalesTerritoryKey       = t.SalesTerritoryKey
GROUP BY
       [SalesTerritoryCountry]
,      [SalesTerritoryRegion]
UNION ALL
SELECT [SalesTerritoryCountry]
,      NULL
,      SUM(SalesAmount) AS TotalSalesAmount
FROM  dbo.factInternetSales s
JOIN  dbo.DimSalesTerritory t     ON s.SalesTerritoryKey       = t.SalesTerritoryKey
GROUP BY
       [SalesTerritoryCountry]
UNION ALL
SELECT NULL
,      NULL
,      SUM(SalesAmount) AS TotalSalesAmount
FROM  dbo.factInternetSales s
JOIN  dbo.DimSalesTerritory t     ON s.SalesTerritoryKey       = t.SalesTerritoryKey;
```

GROUPING SETS를 바꾸려면 샘플 원칙이 적용됩니다. 보려는 집계 수준에 대한 UNION ALL 섹션만 만들면 됩니다.

## <a name="cube-options"></a>큐브 옵션

UNION ALL 접근 방식을 사용하여 GROUP BY WITH CUBE를 만들 수 있습니다. 문제는 코드가 금세 번거롭고 다루기 힘들게 될 수 있다는 것입니다. 이 문제를 완화하기 위해 보다 발전된 접근 방식을 사용할 수 있습니다.

이전 예제를 사용하는 경우 첫 번째 단계는 만들고자 하는 집계의 모든 수준을 정의하는 ‘큐브’를 정의하는 것입니다.

이 경우 모든 수준이 생성되므로 두 파생 테이블의 CROSS JOIN을 기록해 둡니다. 코드의 나머지 부분은 서식을 지정하기 위한 것입니다.

```sql
CREATE TABLE #Cube
WITH
(   DISTRIBUTION = ROUND_ROBIN
,   LOCATION = USER_DB
)
AS
WITH GrpCube AS
(SELECT    CAST(ISNULL(Country,'NULL')+','+ISNULL(Region,'NULL') AS NVARCHAR(50)) as 'Cols'
,          CAST(ISNULL(Country+',','')+ISNULL(Region,'') AS NVARCHAR(50))  as 'GroupBy'
,          ROW_NUMBER() OVER (ORDER BY Country) as 'Seq'
FROM       ( SELECT 'SalesTerritoryCountry' as Country
             UNION ALL
             SELECT NULL
           ) c
CROSS JOIN ( SELECT 'SalesTerritoryRegion' as Region
             UNION ALL
             SELECT NULL
           ) r
)
SELECT Cols
,      CASE WHEN SUBSTRING(GroupBy,LEN(GroupBy),1) = ','
            THEN SUBSTRING(GroupBy,1,LEN(GroupBy)-1)
            ELSE GroupBy
       END AS GroupBy  --Remove Trailing Comma
,Seq
FROM GrpCube;
```

다음 이미지는 CTAS의 결과를 보여 줍니다.

![큐브별로 그룹화](./media/sql-data-warehouse-develop-group-by-options/sql-data-warehouse-develop-group-by-cube.png)

두 번째 단계는 중간 결과를 저장할 대상 테이블을 지정하는 것입니다.

```sql
DECLARE
 @SQL NVARCHAR(4000)
,@Columns NVARCHAR(4000)
,@GroupBy NVARCHAR(4000)
,@i INT = 1
,@nbr INT = 0
;
CREATE TABLE #Results
(
 [SalesTerritoryCountry] NVARCHAR(50)
,[SalesTerritoryRegion]  NVARCHAR(50)
,[TotalSalesAmount]      MONEY
)
WITH
(   DISTRIBUTION = ROUND_ROBIN
,   LOCATION = USER_DB
)
;
```

세 번째 단계는 집계를 수행하는 열의 큐브를 반복하는 것입니다. 쿼리는 #Cube 임시 테이블의 모든 행에 대해 한 번씩 실행됩니다. 결과는 #Results 임시 테이블에 저장됩니다.

```sql
SET @nbr =(SELECT MAX(Seq) FROM #Cube);

WHILE @i<=@nbr
BEGIN
    SET @Columns = (SELECT Cols    FROM #Cube where seq = @i);
    SET @GroupBy = (SELECT GroupBy FROM #Cube where seq = @i);

    SET @SQL ='INSERT INTO #Results
              SELECT '+@Columns+'
              ,      SUM(SalesAmount) AS TotalSalesAmount
              FROM  dbo.factInternetSales s
              JOIN  dbo.DimSalesTerritory t  
              ON s.SalesTerritoryKey = t.SalesTerritoryKey
              '+CASE WHEN @GroupBy <>''
                     THEN 'GROUP BY '+@GroupBy ELSE '' END

    EXEC sp_executesql @SQL;
    SET @i +=1;
END
```

마지막으로, #Results 임시 테이블에서 읽어서 결과를 반환할 수 있습니다.

```sql
SELECT *
FROM #Results
ORDER BY 1,2,3
;
```

코드를 섹션으로 분할하고 반복 구성을 생성하면 코드의 관리 및 유지가 더 쉬워집니다.

## <a name="next-steps"></a>다음 단계

더 많은 개발 팁은 [개발 개요](sql-data-warehouse-overview-develop.md)를 참조하세요.
