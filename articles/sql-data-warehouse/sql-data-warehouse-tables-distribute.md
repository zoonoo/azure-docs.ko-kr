---
title: "SQL Data Warehouse의 테이블 배포 | Microsoft Docs"
description: "Azure SQL Data Warehouse에서 테이블 배포 시작"
services: sql-data-warehouse
documentationcenter: NA
author: barbkess
manager: jenniehubbard
editor: 
ms.assetid: 5ed4337f-7262-4ef6-8fd6-1809ce9634fc
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: tables
ms.date: 12/06/2017
ms.author: barbkess
ms.openlocfilehash: 82e17e575cdb227af2fabf94f01e94df22994aac
ms.sourcegitcommit: cc03e42cffdec775515f489fa8e02edd35fd83dc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/07/2017
---
# <a name="distributing-tables-in-sql-data-warehouse"></a>SQL Data Warehouse의 테이블 배포
> [!div class="op_single_selector"]
> * [개요][Overview]
> * [데이터 형식][Data Types]
> * [배포][Distribute]
> * [인덱스][Index]
> * [파티션][Partition]
> * [통계][Statistics]
> * [임시][Temporary]
>
>

SQL Data Warehouse는 방대한 병렬 처리(MPP) 분산 데이터베이스 시스템입니다.  여러 노드에 걸쳐 데이터와 처리 용량을 구분하여 SQL Data Warehouse는 모든 단일 시스템을 넘어서는 매우 큰 확장성을 제공할 수 있습니다.  SQL Data Warehouse 내에서 데이터를 배포하는 방법을 결정하는 일은 최적의 성능을 달성하는 데 있어서 가장 중요한 요소 중 하나입니다.   성능을 최적화하기 위한 핵심 요인은 데이터 이동을 최소화하는 것이며, 데이터 이동을 최소화하는 핵심 요인은 올바른 배포 전략을 선택하는 것입니다.

## <a name="understanding-data-movement"></a>데이터 이동 이해
MPP 시스템에서 각 테이블의 데이터는 여러 기본 데이터베이스 간에 분할됩니다.  MPP 시스템에서 가장 최적화된 쿼리는 다른 데이터베이스 간의 상호 작용 없이 개별 분산 데이터베이스에서 실행하기 위해 간단히 전달될 수 있습니다.  예를 들어 영업과 고객이란 두 테이블을 포함하는 판매 데이터를 가진 데이타베이스가 있다고 가정해 보겠습니다.  영업 테이블을 고객 테이블에 조인해야 하는 쿼리 있고 영업 및 고객 테이블을 고객 번호별로 나눠 각 고객이 다른 데이터베이스에 들어가게 한다면 영업과 고객을 조인하는 쿼리는 다른 데이터베이스에 대해 알지 못해도 각 데이터베이스 내에서 해결될 수 있습니다.  반면, 영업 데이터를 주문 번호별로 그리고 고객 데이터를 고객 번호별로 분할한 경우 지정된 데이터베이스는 각 고객에 대해 해당 데이터를 갖지 않으므로 영업 데이터를 고객 데이터에 조인하려는 경우 다른 데이터베이스에서 각 고객에 대한 데이터를 가져와야 합니다.  이 두 번째 예제에서는 두 테이블을 조인할 수 있도록 고객 데이터를 영업 데이터로 이동하기 위해 데이터 이동이 일어나야 합니다.  

데이터 이동이 항상 나쁜 것은 아닙니다. 때로 쿼리를 해결하기 위해 필요합니다.  그러나 이 추가 단계를 피할 수 있다면, 자연스럽게 쿼리는 빠르게 실행됩니다.  데이터 이동은 테이블이 조인되거나 집계가 수행될 때 흔히 발생합니다.  조인과 같은 한 가지 시나리오에 대한 최적화를 할 수 있는 한편 집계와 같은 다른 시나리오를 해결하기 위해 데이터 이동이 여전히 필요하므로 흔히 두 작업을 모두 해야 합니다.  어느 쪽이 더 적은 작업을 하는지 파악하는 것이 중요합니다.  대부분의 경우 일반적으로 조인된 열에 대형 팩트 테이블을 분산하는 것이 대부분의 데이터 이동을 줄이는 가장 효과적인 방법입니다.  조인 열에서의 데이터 배포는 집계와 관련된 열에서 데이터를 배포하는 것보다 데이터 이동을 줄이기 위한 훨씬 더 일반적인 메서드입니다.

## <a name="select-distribution-method"></a>배포 방법 선택
내부적으로 SQL Data Warehouse는 데이터를 60개의 데이터베이스로 나눕니다.  각 개별 데이터베이스를 **배포**라고 합니다.  데이터가 각 테이블에 로드되면 SQL Data Warehouse는 데이터를 이러한 60개 배포로 나누는 방법을 알아야 합니다.  

배포 방법은 테이블 수준에서 정의되며 현재 두 가지 방법이 있습니다.

1. **라운드 로빈** : 데이터를 임의로 균일하게 분산합니다.
2. **해시 분산** : 단일 열의 해싱 값에 따라 데이터를 분산합니다.

기본적으로 데이터 배포 메서드를 정의하지 않으면 테이블이 **라운드 로빈** 배포 방법을 사용하여 분산됩니다.  그러나 구현이 좀 더 정교해 질수록 **해시 분산** 테이블을 사용하여 데이터 이동을 최소화하고 쿼리 성능을 최적화하는 것을 고려할 수 있습니다.

### <a name="round-robin-tables"></a>라운드 로빈 테이블
라운드 로빈 데이터 배포 방법은 표현 그대로입니다.  데이터가 로드될 때 각 행은 단순히 다음 배포로 전송됩니다.  이 데이터 배포 방법은 항상 모든 배포에 무작위로 균일하게 데이터를 분산합니다.  즉, 데이터를 배치하는 라운드 로빈 프로세스 중에 정렬이 수행되지 않습니다.  이러한 이유로 라운드 로빈 배포를 임의 해시라고 합니다.  라운드 로빈 분산된 테이블을 사용하면 데이터를 이해하지 않아도 됩니다.  이러한 이유로 라운드 로빈 테이블은 종종 좋은 로드 대상이 됩니다.

기본적으로 배포 방법을 선택하지 않으면 라운드 로빈 배포 방법이 사용됩니다.  그러나 라운드 로빈 테이블은 사용하기 쉽지만 데이터가 시스템에서 무작위로 분산되므로 각 행이 어떤 배포에 포함되는지 보장할 수 없습니다.  결과적으로 경우에 따라 시스템은 쿼리를 해결하기 위해 먼저 데이터 이동 작업을 호출하여 데이터를 좀 더 나은 방식으로 구성해야 합니다.  이 추가 단계로 인해 쿼리 속도가 느려질 수 있습니다.

다음 시나리오에서는 테이블에 대해 라운드 로빈 배포를 사용하는 것이 좋습니다.

* 간단한 시작점으로 시작하는 경우
* 명백한 조인 키가 없는 경우
* 테이블의 해시 분산에 적합한 후보 열이 없는 경우
* 테이블이 다른 테이블과 공통 조인 키를 공유하지 않는 경우
* 조인이 쿼리의 다른 조인보다 덜 중요한 경우
* 테이블이 임시 준비 테이블인 경우

다음 두 예제에서는 라운드 로빈 테이블이 만들어집니다.

```SQL
-- Round Robin created by default
CREATE TABLE [dbo].[FactInternetSales]
(   [ProductKey]            int          NOT NULL
,   [OrderDateKey]          int          NOT NULL
,   [CustomerKey]           int          NOT NULL
,   [PromotionKey]          int          NOT NULL
,   [SalesOrderNumber]      nvarchar(20) NOT NULL
,   [OrderQuantity]         smallint     NOT NULL
,   [UnitPrice]             money        NOT NULL
,   [SalesAmount]           money        NOT NULL
)
;

-- Explicitly Created Round Robin Table
CREATE TABLE [dbo].[FactInternetSales]
(   [ProductKey]            int          NOT NULL
,   [OrderDateKey]          int          NOT NULL
,   [CustomerKey]           int          NOT NULL
,   [PromotionKey]          int          NOT NULL
,   [SalesOrderNumber]      nvarchar(20) NOT NULL
,   [OrderQuantity]         smallint     NOT NULL
,   [UnitPrice]             money        NOT NULL
,   [SalesAmount]           money        NOT NULL
)
WITH
(   CLUSTERED COLUMNSTORE INDEX
,   DISTRIBUTION = ROUND_ROBIN
)
;
```

> [!NOTE]
> 라운드 로빈이 기본 테이블 형식이지만, 테이블 레이아웃의 의도가 다른 사용자에게 명확하게 드러나도록, DDL에서 명시적으로 지정하는 것이 좋습니다.
>
>

### <a name="hash-distributed-tables"></a>해시 분산 테이블
**해시 분산** 알고리즘을 사용하여 테이블을 배포하면 쿼리 시간에 데이터 이동이 감소되어 다양한 시나리오에 대한 성능이 향상될 수 있습니다.  해시 분산 테이블이란 선택한 단일 열에서 해시 알고리즘을 사용하여 분산 데이터베이스 간에 분할되는 테이블입니다.  배포 열은 분산된 데이터베이스 간에 데이터가 분할되는 방법을 결정합니다.  해시 함수는 배포 열을 사용하여 배포에 행을 할당합니다.  해시 알고리즘 및 결과 배포는 결정적입니다.  동일한 데이터 형식의 동일한 값은 항상 동일한 배포를 갖게 됩니다.    

이 예제에서는 ID에 따라 분산된 테이블을 만듭니다.

```SQL
CREATE TABLE [dbo].[FactInternetSales]
(   [ProductKey]            int          NOT NULL
,   [OrderDateKey]          int          NOT NULL
,   [CustomerKey]           int          NOT NULL
,   [PromotionKey]          int          NOT NULL
,   [SalesOrderNumber]      nvarchar(20) NOT NULL
,   [OrderQuantity]         smallint     NOT NULL
,   [UnitPrice]             money        NOT NULL
,   [SalesAmount]           money        NOT NULL
)
WITH
(   CLUSTERED COLUMNSTORE INDEX
,  DISTRIBUTION = HASH([ProductKey])
)
;
```

## <a name="select-distribution-column"></a>배포 열 선택
테이블을 **해시 분산** 하기로 선택하는 경우 단일 배포 열을 선택해야 합니다.  배포 열을 선택할 때 세 가지 주요 요소를 고려해야 합니다.  

다음과 같은 단일 열을 선택합니다.

1. 업데이트되지 않는 열
2. 데이터를 고르게 분산하는 열(데이터 오차 방지에 필요)
3. 데이터 이동 최소화

### <a name="select-distribution-column-which-will-not-be-updated"></a>업데이트되지 않을 배포 열 선택
배포 열은 업데이트할 수 없으므로 정적 값을 갖는 열을 선택합니다.  업데이트해야 하는 열은 적합한 배포 후보가 아닙니다.  배포 열을 업데이트해야 하는 경우 먼저 해당 행을 삭제한 다음 새 행을 삽입합니다.

### <a name="select-distribution-column-which-will-distribute-data-evenly"></a>데이터를 균일하게 분산하는 배포 열 선택
분산 시스템은 가장 느린 배포 정도의 속도로 수행되므로 시스템에서 분산된 실행을 구현하려면 배포 간에 작업을 균일하게 분산하는 것이 중요합니다.  분산 시스템에서 작업이 분할되는 방식은 각 배포의 데이터가 있는 위치에 따라 다릅니다.  따라서 각 배포가 같은 작업을 포함하고 해당 작업 부분을 완료하는 데 동일한 시간이 소요되도록 데이터를 분산하기 위한 적합한 배포 열을 선택하는 것이 매우 중요합니다.  시스템 전체에서 작업이 적절하게 분할되면 분산된 작업 간에 데이터가 적절하게 분산됩니다.  데이터가 균등하게 분산되지 않은 상태를 **데이터 오차**라고 합니다.  

데이터를 균일하게 나누고 데이터 오차를 방지하려면 배포 열을 선택할 때 다음 사항을 고려합니다.

1. 많은 수의 고유 값을 포함하는 열을 선택합니다.
2. 고유 값이 많지 않은 열에는 데이터를 분산하지 않도록 합니다.
3. null 수가 많은 열에는 데이터를 분산하지 않도록 합니다.
4. 날짜 열에 데이터를 분산하지 않도록 합니다.

각 값은 60개 분산 중 하나로 해시되므로 데이터를 균일하게 분산하기 위해 고유한 값이 60개보다 많이 포함된 매우 고유한 열을 선택할 수 있습니다.  예를 들어 열에 고유 값이 40개만 있는 경우를 고려해 보겠습니다.  이 열을 배포 키로 선택한 경우 해당 테이블의 데이터는 최대 40개 분산에만 할당되며 20개 분산에는 데이터가 포함되지 않으므로 처리 작업도 수행되지 않습니다.  반대로, 다른 40개 배포는 데이터가 60개 넘는 배포에 균일하게 분산되는 경우보다 수행할 작업이 더 많아집니다.  이 시나리오가 데이터 오차를 보여 주는 예라 할 수 있습니다.

MPP 시스템에서 각 쿼리 단계는 모든 분산이 각기 할당된 작업을 완료할 때까지 기다립니다.  특정 분산이 다른 분산보다 더 많은 작업을 수행하는 경우 다른 분산은 작업량이 더 많은 분산의 작업이 완료될 때까지 기다려야 하므로 해당 리소스가 낭비됩니다.  모든 분산으로 작업이 균일하게 분산되지 않은 상태를 **처리 오차**라고 합니다.  처리 오차가 발생하면 워크로드를 모든 분산에 균일하게 분산할 수 있는 경우보다 쿼리가 더 느리게 실행됩니다.  데이터 오차가 있으면 처리 오차가 발생합니다.

null 값은 모두 같은 분산에 할당되므로 null이 많은 열에는 데이터를 분산하지 않도록 합니다. 날짜 열에 데이터를 분산해도 처리 오차가 발생할 수 있습니다. 지정된 날짜의 모든 데이터는 같은 분산에 할당되기 때문입니다. 여러 사용자가 모두 같은 날짜를 기준으로 필터링하는 쿼리를 실행하는 경우, 지정된 날짜가 분산 하나에만 포함되어 있으므로 60개 분산 중 하나만 모든 작업을 수행하게 됩니다. 따라서 이 시나리오에서는 데이터를 모든 분산에 균일하게 분산한 경우에 비해 쿼리 실행 속도가 60배 느려질 수 있습니다.

좋은 후보 열이 없으면 배포 방법으로 라운드 로빈을 사용하는 것을 고려합니다.

### <a name="select-distribution-column-which-will-minimize-data-movement"></a>데이터 이동을 최소화하는 배포 열 선택
올바른 배포 열을 선택하여 데이터 이동을 최소화하는 일은 SQL Data Warehouse의 성능을 최적화하기 위한 가장 중요한 전략 중 하나입니다.  데이터 이동은 테이블이 조인되거나 집계가 수행될 때 흔히 발생합니다.  `JOIN`, `GROUP BY`, `DISTINCT`, `OVER` 및 `HAVING` 절에 사용한 열이 모두 해시 분산 후보로 **적합**합니다.

반면 `WHERE` 절의 열은 쿼리에 참여하는 배포를 제한하여 처리 오차를 발생시키므로 해시 열 후보로 적합하지 **않습니다** .  데이터를 분산시키기에 적합해 보이지만 이와 같은 처리 오차를 발생시키기가 많은 열의 대표적인 예로 날짜 열이 있습니다.

일반적으로 두 개의 대형 팩트 테이블이 조인에 자주 포함될 경우 조인 열 중 하나에 두 테이블을 모두 배포함으로써 최적의 성능을 얻게 됩니다.  다른 대형 팩트 테이블에 조인되지 않는 테이블이 있는 경우 `GROUP BY` 절에서 자주 나오는 열을 찾아보세요.

조인하는 동안 데이터 이동을 피하기 위해서는 몇 가지 조건을 충족해야 합니다.

1. 조인에 관련된 테이블은 조인에 포함되는 열 중 **하나** 에서 해시 분산해야 합니다.
2. 조인 열의 데이터 형식은 두 테이블 간에 일치해야 합니다.
3. 같음 연산자로 열을 조인해야 합니다.
4. 조인 형식은 `CROSS JOIN`이 아닐 수 있습니다.

## <a name="troubleshooting-data-skew"></a>데이터 오차 문제 해결
해시 분산 방법을 사용하여 테이블 데이터를 분산하는 경우 일부 분산에 불균형하게 다른 분산보다 데이터가 더 많은 오차가 발생할 수 있습니다. 분산 쿼리의 최종 결과는 가장 오래 실행되는 분산이 완료될 때까지 대기해야 하므로 과도한 데이터 오차는 쿼리 성능에 영향을 줄 수 있습니다. 데이터 오차 정도에 따라 이 문제를 해결해야 할 수 있습니다.

### <a name="identifying-skew"></a>오차 식별
테이블을 오차 상태로 식별하는 간단한 방법은 `DBCC PDW_SHOWSPACEUSED`를 사용하는 것입니다.  각각의 60개 데이터베이스 배포에 저장된 테이블 행 수를 매우 빠르고 간편하게 보는 방법입니다.  가장 균형 있는 성능을 얻으려면 분산 테이블의 행을 모든 배포에 균등하게 나누어야 합니다.

```sql
-- Find data skew for a distributed table
DBCC PDW_SHOWSPACEUSED('dbo.FactInternetSales');
```

그러나 SQL Data Warehouse DMV(동적 관리 뷰)를 쿼리하는 경우 보다 자세한 분석을 수행할 수 있습니다.  분석을 시작하려면 [테이블 개요][Overview] 문서의 SQL을 사용하여 [dbo.vTableSizes][dbo.vTableSizes] 뷰를 만듭니다.  뷰가 생성되면 이 쿼리를 실행하여 데이터 오차가 10%보다 큰 테이블을 식별합니다.

```sql
select *
from dbo.vTableSizes
where two_part_name in
    (
    select two_part_name
    from dbo.vTableSizes
    where row_count > 0
    group by two_part_name
    having min(row_count * 1.000)/max(row_count * 1.000) > .10
    )
order by two_part_name, row_count
;
```

### <a name="resolving-data-skew"></a>데이터 오차 해결
모든 오차가 해결을 보장할 만큼 충분한 것은 아닙니다.  경우에 따라 일부 쿼리에서는 테이블 성능이 매우 중요해서 데이터 오차가 발생할 위험도 용인할 수 있습니다.  테이블의 데이터 오차를 해결해야 하는지 결정하려면 워크로드의 데이터 볼륨 및 쿼리를 최대한 이해해야 합니다.   오차의 영향을 확인하는 한 가지 방법은 [쿼리 모니터링][Query Monitoring] 문서의 단계를 사용하여 쿼리 성능에 오차가 미치는 영향, 특히 개별 분산에서 쿼리가 완료되는 데 소요되는 시간에 대한 영향을 모니터링하는 것입니다.

데이터 분산은 데이터 오차 최소화와 데이터 이동 최소화 간의 적절한 균형을 찾는 문제입니다. 이는 반대되는 목표일 수 있으며, 경우에 따라 데이터 이동을 줄이기 위해 데이터 오차를 유지할 수 있습니다. 예를 들어 분산 열이 종종 조인 및 집계에서 공유 열인 경우 데이터 이동을 최소화하게 됩니다. 데이터 이동을 최소화할 경우의 이점은 데이터 오차의 영향을 능가할 수 있습니다.

데이터 오차를 해결하는 일반적인 방법은 다른 분산 열이 있는 테이블을 다시 만드는 것입니다. 기존 테이블에서 배포 열을 변경할 방법이 없으므로 테이블의 배포를 변경하는 방법은 [CTAS][]를 사용하여 다시 만드는 것입니다.  데이터 오차를 해결하는 방법의 두 가지 예제는 다음과 같습니다.

### <a name="example-1-re-create-the-table-with-a-new-distribution-column"></a>예제 1: 새 배포 열로 테이블 다시 만들기
이 예제에서는 [CTAS][]를 사용하여 다른 해시 분산 열로 테이블을 다시 만듭니다.

```sql
CREATE TABLE [dbo].[FactInternetSales_CustomerKey]
WITH (  CLUSTERED COLUMNSTORE INDEX
     ,  DISTRIBUTION =  HASH([CustomerKey])
     ,  PARTITION       ( [OrderDateKey] RANGE RIGHT FOR VALUES (   20000101, 20010101, 20020101, 20030101
                                                                ,   20040101, 20050101, 20060101, 20070101
                                                                ,   20080101, 20090101, 20100101, 20110101
                                                                ,   20120101, 20130101, 20140101, 20150101
                                                                ,   20160101, 20170101, 20180101, 20190101
                                                                ,   20200101, 20210101, 20220101, 20230101
                                                                ,   20240101, 20250101, 20260101, 20270101
                                                                ,   20280101, 20290101
                                                                )
                        )
    )
AS
SELECT  *
FROM    [dbo].[FactInternetSales]
OPTION  (LABEL  = 'CTAS : FactInternetSales_CustomerKey')
;

--Create statistics on new table
CREATE STATISTICS [ProductKey] ON [FactInternetSales_CustomerKey] ([ProductKey]);
CREATE STATISTICS [OrderDateKey] ON [FactInternetSales_CustomerKey] ([OrderDateKey]);
CREATE STATISTICS [CustomerKey] ON [FactInternetSales_CustomerKey] ([CustomerKey]);
CREATE STATISTICS [PromotionKey] ON [FactInternetSales_CustomerKey] ([PromotionKey]);
CREATE STATISTICS [SalesOrderNumber] ON [FactInternetSales_CustomerKey] ([SalesOrderNumber]);
CREATE STATISTICS [OrderQuantity] ON [FactInternetSales_CustomerKey] ([OrderQuantity]);
CREATE STATISTICS [UnitPrice] ON [FactInternetSales_CustomerKey] ([UnitPrice]);
CREATE STATISTICS [SalesAmount] ON [FactInternetSales_CustomerKey] ([SalesAmount]);

--Rename the tables
RENAME OBJECT [dbo].[FactInternetSales] TO [FactInternetSales_ProductKey];
RENAME OBJECT [dbo].[FactInternetSales_CustomerKey] TO [FactInternetSales];
```

### <a name="example-2-re-create-the-table-using-round-robin-distribution"></a>예제 2: 라운드 로빈 배포를 사용하여 테이블 다시 만들기
이 예제에서는 [CTAS][]를 사용하여 해시 분산 대신, 라운드 로빈으로 테이블을 다시 만듭니다. 이렇게 변경하면 데이터 이동이 증가하지만 데이터가 균일하게 배포됩니다.

```sql
CREATE TABLE [dbo].[FactInternetSales_ROUND_ROBIN]
WITH (  CLUSTERED COLUMNSTORE INDEX
     ,  DISTRIBUTION =  ROUND_ROBIN
     ,  PARTITION       ( [OrderDateKey] RANGE RIGHT FOR VALUES (   20000101, 20010101, 20020101, 20030101
                                                                ,   20040101, 20050101, 20060101, 20070101
                                                                ,   20080101, 20090101, 20100101, 20110101
                                                                ,   20120101, 20130101, 20140101, 20150101
                                                                ,   20160101, 20170101, 20180101, 20190101
                                                                ,   20200101, 20210101, 20220101, 20230101
                                                                ,   20240101, 20250101, 20260101, 20270101
                                                                ,   20280101, 20290101
                                                                )
                        )
    )
AS
SELECT  *
FROM    [dbo].[FactInternetSales]
OPTION  (LABEL  = 'CTAS : FactInternetSales_ROUND_ROBIN')
;

--Create statistics on new table
CREATE STATISTICS [ProductKey] ON [FactInternetSales_ROUND_ROBIN] ([ProductKey]);
CREATE STATISTICS [OrderDateKey] ON [FactInternetSales_ROUND_ROBIN] ([OrderDateKey]);
CREATE STATISTICS [CustomerKey] ON [FactInternetSales_ROUND_ROBIN] ([CustomerKey]);
CREATE STATISTICS [PromotionKey] ON [FactInternetSales_ROUND_ROBIN] ([PromotionKey]);
CREATE STATISTICS [SalesOrderNumber] ON [FactInternetSales_ROUND_ROBIN] ([SalesOrderNumber]);
CREATE STATISTICS [OrderQuantity] ON [FactInternetSales_ROUND_ROBIN] ([OrderQuantity]);
CREATE STATISTICS [UnitPrice] ON [FactInternetSales_ROUND_ROBIN] ([UnitPrice]);
CREATE STATISTICS [SalesAmount] ON [FactInternetSales_ROUND_ROBIN] ([SalesAmount]);

--Rename the tables
RENAME OBJECT [dbo].[FactInternetSales] TO [FactInternetSales_HASH];
RENAME OBJECT [dbo].[FactInternetSales_ROUND_ROBIN] TO [FactInternetSales];
```

## <a name="next-steps"></a>다음 단계
테이블 디자인에 대한 자세한 내용은 [분산][Distribute], [인덱스][Index], [파티션][Partition], [데이터 형식][Data Types], [통계][Statistics] 및 [임시 테이블][Temporary] 문서를 참조하세요.

모범 사례의 개요는 [SQL Data Warehouse 모범 사례][SQL Data Warehouse Best Practices]를 참조하세요.

<!--Image references-->

<!--Article references-->
[Overview]: ./sql-data-warehouse-tables-overview.md
[Data Types]: ./sql-data-warehouse-tables-data-types.md
[Distribute]: ./sql-data-warehouse-tables-distribute.md
[Index]: ./sql-data-warehouse-tables-index.md
[Partition]: ./sql-data-warehouse-tables-partition.md
[Statistics]: ./sql-data-warehouse-tables-statistics.md
[Temporary]: ./sql-data-warehouse-tables-temporary.md
[SQL Data Warehouse Best Practices]: ./sql-data-warehouse-best-practices.md
[Query Monitoring]: ./sql-data-warehouse-manage-monitor.md
[dbo.vTableSizes]: ./sql-data-warehouse-tables-overview.md#table-size-queries

<!--MSDN references-->
[DBCC PDW_SHOWSPACEUSED()]: https://msdn.microsoft.com/library/mt204028.aspx

<!--Other Web references-->
