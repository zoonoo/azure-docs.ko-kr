<properties
   pageTitle="인덱스 관리 | Microsoft Azure"
   description="인덱스를 관리하는 데 도움이 되는 지침"
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
   ms.date="03/18/2016"
   ms.author="jrj;barbkess;sonyama"/>

# 인덱스 관리
이 문서에서는 인덱스를 관리하기 위한 몇 가지 핵심 기술을 보여 줍니다.

## 인덱스 다시 작성 최적화
다음 두 가지 방법 중 하나로 인덱스를 다시 작성을 최적화할 수 있습니다.

1. 테이블 분할 및 파티션 수준 인덱스 다시 작성 실행
2. [CTAS][]를 사용하여 새 테이블에 데이터의 파티션을 만든 후 새 테이블에 파티션 전환 만들기

## 분할된 인덱스 다시 작성

다음은 단일 파티션을 다시 작성하는 방법의 예입니다.

```
ALTER INDEX ALL ON [dbo].[FactInternetSales] REBUILD Partition = 5
```

ALTER INDEX..REBUILD는 좀 더 작은 데이터 볼륨에 가장 적합하며 특히 columnstore 인덱스에 적합합니다. 열린 행 그룹, 닫힌 행 그룹 및 압축된 행 그룹이 다시 작성에 모두 포함됩니다. 그러나 파티션이 매우 큰 경우 `CTAS`가 좀 더 효율적인 작업이라는 것을 알 수 있습니다. 전체 인덱스 다시 작성의 예는 아래와 같습니다.

```
ALTER INDEX ALL ON [dbo].[DimProduct] REBUILD
```

이 구문에 대한 자세한 내용은 [ALTER INDEX][] 문서를 참조하세요.

## CTAS를 사용하여 파티션 다시 작성

다음은 CTAS를 사용하여 파티션을 다시 작성하는 방법의 예입니다.

```
-- Step 01. Select the partition of data and write it out to a new table using CTAS
CREATE TABLE [dbo].[FactInternetSales_20000101_20010101]
    WITH    (   DISTRIBUTION = HASH([ProductKey])
            ,   CLUSTERED COLUMNSTORE INDEX
            ,   PARTITION   (   [OrderDateKey] RANGE RIGHT FOR VALUES
                                (20000101,20010101
                                )
                            )
            )
AS
SELECT  *
FROM    [dbo].[FactInternetSales]
WHERE   [OrderDateKey] >= 20000101
AND     [OrderDateKey] <  20010101
;

-- Step 02. Create a SWITCH out table
 
CREATE TABLE dbo.FactInternetSales_20000101
    WITH    (   DISTRIBUTION = HASH(ProductKey)
            ,   CLUSTERED COLUMNSTORE INDEX
            ,   PARTITION   (   [OrderDateKey] RANGE RIGHT FOR VALUES
                                (20000101
                                )
                            )
            )
AS
SELECT *
FROM    [dbo].[FactInternetSales]
WHERE   1=2 -- Note this table will be empty

-- Step 03. Switch OUT the data 
ALTER TABLE [dbo].[FactInternetSales] SWITCH PARTITION 2 TO  [dbo].[FactInternetSales_20000101] PARTITION 2;

-- Step 04. Switch IN the rebuilt data
ALTER TABLE [dbo].[FactInternetSales_20000101_20010101] SWITCH PARTITION 2 TO  [dbo].[FactInternetSales] PARTITION 2;

```

## 다음 단계
파티션을 만들고 크기를 조정하는 방법에 대한 자세한 지침은 [테이블 분할][]에 대한 문서를 참조하세요. 또한 이 문서에는 파티션 경계를 식별하는 데 도움이 되는 예도 포함되어 있습니다.

추가 관리 팁을 보려면 [관리][] 개요를 참조하세요.

<!--Image references-->

<!--Article references-->
[CTAS]: sql-data-warehouse-develop-ctas.md
[테이블 분할]: sql-data-warehouse-develop-table-partitions.md
[관리]: sql-data-warehouse-manage-monitor.md

<!--MSDN references-->
[ALTER INDEX]: https://msdn.microsoft.com/ko-KR/library/ms188388.aspx

<!--Other Web references-->

<!---HONumber=AcomDC_0323_2016-->