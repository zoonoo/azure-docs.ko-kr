<properties
   pageTitle="SQL 데이터 웨어하우스의 테이블 디자인 | Microsoft Azure"
   description="솔루션 개발을 위한 Azure SQL 데이터 웨어하우스의 테이블 디자인을 위한 팁"
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

# SQL 데이터 웨어하우스의 테이블 디자인 #
SQL 데이터 웨어하우스는 방대한 병렬 처리(MPP) 분산 데이터베이스 시스템입니다. 따라서 **배포판**이라고 하는 여러 다른 위치에 데이터를 저장합니다. 각 **배포**는 버킷과 같은 것으로 데이터 웨어하우스에서 데이터의 고유 하위 집합을 저장합니다. 여러 노드에 걸쳐 처리 기능 및 데이터를 분배하여 SQL 데이터 웨어하우스는 모든 단일 시스템을 넘어서 확장성이 클 수 있습니다.

SQL 데이터 웨어하우스에 테이블을 만들면 모든 배포판에 걸쳐 실제로 배포됩니다.

이 문서는 다음 항목을 설명합니다.

- 지원되는 데이터 원본
- 데이터 분포의 원칙
- 라운드 로빈 배포
- 해시 배포
- 테이블 분할
- 통계
- 지원되지 않는 기능

## 지원되는 데이터 원본
SQL 데이터 웨어하우스는 일반적인 비즈니스 데이터 형식을 지원합니다.

- **bigint**
- **binary**
- **bit**
- **char**
- **date**
- **datetime**
- **datetime2**
- **datetimeoffset**
- **decimal**
- **float**
- **int**
- **money**
- **nchar**
- **nvarchar**
- **real**
- **smalldatetime**
- **smallint**
- **smallmoney**
- **time**
- **tinyint**
- **varbinary**
- **varchar**

다음 쿼리를 사용하여 호환되지 않는 형식을 포함하는 데이터 웨어하우스에 대한 열을 식별할 수 있습니다.

```
SELECT  t.[name]
,       c.[name]
,       c.[system_type_id]
,       c.[user_type_id]
,       y.[is_user_defined]
,       y.[name]
FROM sys.tables  t
JOIN sys.columns c on t.[object_id]    = c.[object_id]
JOIN sys.types   y on c.[user_type_id] = y.[user_type_id]
WHERE y.[name] IN
                (   'geography'
                ,   'geometry'
                ,   'hierarchyid'
                ,   'image'
                ,   'ntext'
                ,   'numeric'
                ,   'sql_variant'
                ,   'sysname'
                ,   'text'
                ,   'timestamp'
                ,   'uniqueidentifier'
                ,   'xml'
                )

OR  (   y.[name] IN (  'nvarchar','varchar','varbinary')
    AND c.[max_length] = -1
    )
OR  y.[is_user_defined] = 1
;

```

쿼리는 지원하지 않는 모든 사용자 정의 데이터 형식을 포함합니다.

데이터베이스에 지원되지 않는 형식이 있는 경우, 걱정하지 마세요. 대신 사용할 수 있는 대체 방법이 다음에 제시됩니다.

다음 위치 대신

- **geometry**, varbinary 형식 사용
- **geography**, varbinary 형식 사용
- **hierarchyid**, 네이티브가 아닌 CLR 형식
- 텍스트 기반이 varchar/nvarchar를 사용하는 경우 **image**, **text**, **ntext**(작을수록 더 좋음)
- **nvarchar(max)**, 더 나은 성능을 위해 varchar(4000) 이하 사용
- **numeric**, decimal 사용
- **sql_variant**, 열을 강력한 형식의 열로 분할
- **sysname**, nvarchar(128) 사용
- **table**, 임시 테이블로 변환
- **timestamp**, datetime2 및 `CURRENT_TIMESTAMP` 함수를 사용하도록 코드 재작업. current_timestamp를 기본 제약 조건으로 사용할 수 없으며 값은 자동으로 업데이트되지 않습니다. rowversion 값을 타임스탬프 형식의 열에서 마이그레이션해야 하는 경우, NOT NULL 또는 NULL 행 버전 값으로 BINARY(8) 또는 VARBINARY(8)을 사용합니다.
- **varchar(max)**, 더 나은 성능을 위해 varchar(8000) 이하 사용
- **uniqueidentifier**, varbinary(8) 사용
- **사용자 정의 형식**, 가능한 경우 해당 네이티브 형식으로 다시 변환
- **xml**, 더 나은 성능을 위해 varchar(8000) 이하, 필요한 경우 열에서 분할

일부 지원:

- 기본 제약 조건은 리터럴 및 상수만 지원합니다. `GETDATE()` 또는 `CURRENT_TIMESTAMP`와 같은 명확하지 않은 식 또는 함수는 지원되지 않습니다.

> [AZURE.NOTE]가변 길이 열의 전체 길이 포함하여 가능한 최대 행 크기가 32,767 바이트를 초과하지 않도록 테이블을 정의합니다. 이 숫자를 초과할 수 있는 가변 길이 데이터로 행을 정의할 수 있는 경우, 테이블에 데이터를 삽입할 수 없습니다. 또한 실행 중인 쿼리에 대한 더 나은 처리량을 위해 변수 길이 열의 크기를 제한합니다.

## 데이터 분포의 원칙

SQL 데이터 웨어하우스에 데이터를 배포하는 두 가지 선택 방법이 있습니다.

1. 단일 열에서 해싱 값을 기본으로 하는 데이터 배포
2. 데이터를 균등하지만 임의로 배포  

데이터 분포는 테이블 수준에서 결정됩니다. SQL 데이터 웨어하우스 데이터베이스의 각 테이블에 대해 이 결정을 내릴 수 있도록 모든 테이블이 분산됩니다.

첫 번째 옵션은 **라운드 로빈** 배포라고 하며 임의 해시라고도 합니다. 기본값 또는 유사 시 대기 옵션으로 생각할 수 있습니다.

두 번째 옵션은 **해시** 배포라고 합니다. 데이터 분포의 최적화된 형태로 고려할 수 있습니다. 테이블의 클러스터가 공통 조인 및/또는 집계 기준을 공유를 선호합니다.

## 라운드 로빈 배포

라운드 로빈 배포는 모든 분포에 걸쳐 데이터를 가능한 한 균등하게 분배하는 메서드입니다. 데이터의 행을 포함하는 버퍼는 각 배포에 대해 차례로 할당됩니다(이런 이유로 이름이 라운드 로빈임). 모든 데이터 버퍼가 할당될 때까지 프로세스가 반복됩니다. 어떤 단계에서도 라운드 로빈 배포 테이블에 정렬되거나 순서가 지정된 데이터는 없습니다. 이러한 이유로 라운드 로빈 배포를 임의 해시라고 합니다. 데이터는 배포에서 가능한 균등하게 분산됩니다.

다음은 라운드 로빈 분산된 테이블의 예입니다.

```
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

다음은 또한 라운드 로빈 분산된 테이블의 예입니다.

```
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
)
;
```

> [AZURE.NOTE]위의 두 번째 예제에서는 배포 키에 대한 언급이 없습니다. 라운드 로빈은 기본값이므로 절대적으로 필요하지 않습니다. 명시적이지만 테이블 디자인을 검토할 때 사용자의 동료가 사용자의 의도를 인식하는 것이 좋습니다.

데이터를 해시하는 분명한 키 열이 없는 경우 이 테이블 유형이 보통 사용됩니다. 이동 비용이 그다지 좋지 않을 수 있는 덜 중요한 테이블에서 사용될 수도 있습니다.

라운드 로빈 분산 테이블로 데이터를 로드하면 해시 테이블로 로드하는 것보다 빠를 수 있습니다. 라운드 로빈 분산된 테이블을 사용하면 데이터를 이해하거나 로드하기 전에 해시를 수행하지 않아도 됩니다. 이러한 이유로 라운드 로빈 테이블은 종종 좋은 로드 대상이 됩니다.

> [AZURE.NOTE]데이터가 라운드 로빈 배포인 경우 데이터는 *버퍼* 수준의 배포로 할당됩니다.

### 추천

다음 시나리오에서는 테이블에 대해 라운드 로빈 배포를 사용하는 것이 좋습니다.

- 명백한 조인 키가 없는 경우
- 후보 해시 배포 키를 알 수 없는 경우
- 테이블이 다른 테이블과 공통 조인 키를 공유하지 않는 경우
- 조인이 쿼리의 다른 조인보다 덜 중요한 경우
- 테이블이 초기 로드 테이블인 경우

## 해시 배포

해시 배포는 단일 열을 해시하여 데이터 집합을 배포에 분산하는 데 내부 함수를 사용합니다. 데이터가 해시되는 경우 배포에 할당되는 데이터에 명백한 순서가 없습니다. 그러나 해시 자체는 결정적 프로세스입니다. 이로 인해 해시 결과가 예측 가능합니다. 예를 들어, 값 10이 포함된 정수 열을 해싱하면 항상 동일한 해시 값을 생성합니다. 즉, 값 10을 포함한 해싱된 ***모든*** 정수 열이 동일한 배포에 할당됨을 의미합니다. 여러 테이블에서도 마찬가지입니다.

해시의 예측 가능성은 매우 중요합니다. 데이터를 읽고 테이블을 같이 조인한 경우 데이터를 배포하는 해시로 성능이 향상될 수 있음을 의미합니다.

다음과 같이, 해시 배포는 쿼리 최적화에 매우 효과적일 수 있습니다. 데이터 배포의 최적화된 형태로 간주되는 이유입니다.

> [AZURE.NOTE]기억하세요! 해시는 데이터의 값을 기반으로 하지 않으며 해시되는 데이터의 형식을 기반으로 합니다.

다음은 해시 ProductKey로 해시 배포된 테이블입니다.

```
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
,   DISTRIBUTION = HASH([ProductKey])
)
;
```

> [AZURE.NOTE]데이터가 해시 배포인 경우 데이터는 행 수준의 배포로 할당됩니다.

## 테이블 파티션
테이블 파티션이 지원되며 쉽게 정의될 수 있습니다.

예제 SQL 데이터 웨어하우스 분할된 `CREATE TABLE` 명령:

```
CREATE TABLE [dbo].[FactInternetSales]
(
    [ProductKey]            int          NOT NULL
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
,   DISTRIBUTION = HASH([ProductKey])
,   PARTITION   (   [OrderDateKey] RANGE RIGHT FOR VALUES
                    (20000101,20010101,20020101
                    ,20030101,20040101,20050101
                    )
                )
)
;
```

정의에 파티션 함수 또는 스키마가 없습니다. 테이블을 만들 때 주의해야 할 사항입니다. 분할 키가 될 열에 대한 경계 지점을 식별하기만 하면 됩니다.

## 통계

SQL 데이터 웨어하우스는 분산된 쿼리 최적화 프로그램을 사용하여 사용자가 쿼리 테이블을 사용할 때 적절한 쿼리 계획을 만듭니다. 만든 후에는 쿼리 계획은 데이터베이스에서 사용되는 전략 및 메서드를 제공하여 데이터에 액세스하고 사용자 요청을 처리합니다. SQL 데이터 웨어하우스의 쿼리 최적화 프로그램은 비용을 기반으로 합니다. 다른 말로 상대 비용에 따라 다양한 옵션(계획)을 비교하고 사용할 수 있는 가장 효율적인 계획을 선택합니다. 따라서 SQL 데이터 웨어하우스는 많은 정보를 제공받아 비용을 기반으로 결정해야 합니다. `STATISTICS`라는 데이터베이스 개체 및 테이블(테이블 크기)에 관한 통계 정보가 있습니다.

통계는 인덱스 또는 테이블의 단일 또는 여러 열에 대해 유지됩니다. 카디널리티 및 값의 선택도에 관한 중요한 정보를 포함한 비용 기반 최적화 프로그램을 제공합니다. 최적화 프로그램이 쿼리에서 JOIN, GROUP BY, HAVING 및 WHERE 절을 평가해야 하는 경우 더욱 흥미롭습니다. 그러므로 이 통계 개체에 포함된 정보가 *정확하게* 테이블의 현재 상태를 반영한다는 점이 매우 중요합니다. 비용의 정확도가 중요하다는 것을 반드시 이해해야 합니다. 통계가 테이블의 상태를 정확하게 반영하는 경우, 가장 낮은 비용을 위해 계획을 비교할 수 있습니다. 정확하지 않은 경우 SQL 데이터 웨어하우스는 잘못된 계획을 선택할 수 있습니다.

SQL 데이터 웨어하우스의 열 수준 통계는 사용자가 정의합니다.

즉 사용자 자신이 만들어야 합니다. 방금 학습했듯이 간과할 사항은 아닙니다. SQL Server와 SQL 데이터 웨어하우스 사이에 중요한 차이점이 있습니다. 열이 쿼리되면 SQL Server는 자동으로 통계를 만듭니다. 기본적으로 SQL Server는 이 통계를 자동으로 업데이트합니다. 그러나 SQL 데이터 웨어하우스에서는 통계를 수동으로 생성하고 수동으로 관리해야 합니다.

### 추천

통계를 생성하기 위한 다음 권장 사항을 적용합니다.

1. `WHERE`, `JOIN`, `GROUP BY`, `ORDER BY` 및 `DISTINCT` 절에서 사용된 열에 단일 열 통계 만들기
2. 복합 절에 여러 열 통계 생성
3. 통계를 정기적으로 업데이트 자동으로 수행되지 않는다는 점을 기억하세요!

>[AZURE.NOTE]일반적으로 SQL Server 데이터 웨어하우스는 전적으로 `AUTOSTATS`를 의존해서 열 통계를 최신 상태로 유지합니다. SQL Server 데이터 웨어하우스에 대해서도 좋은 것은 아닙니다. `AUTOSTATS`는 수백만 또는 수십억 개의 행을 포함하는 큰 팩트 테이블이 충분하지 않을 수 있는 변경의 20% 비율로 트리거됩니다. 그러므로 항상 통계 업데이트를 유지하여 통계가 테이블의 카디널리티를 정확히 반영하도록 하는 것이 좋습니다.

## 지원되지 않는 기능
SQL 데이터 웨어하우스는 이 기능을 사용하지 않거나 지원하지 않습니다.

- 기본 키
- 외래 키
- Check 제약 조건
- 고유 제약 조건
- 고유 인덱스
- 계산된 열
- 스파스 열
- 사용자 정의 형식
- 인덱싱된 뷰
- ID
- 시퀀스
- 트리거
- 동의어


## 다음 단계
더 많은 개발 팁은 [개발 개요][]를 참조하세요.

<!--Image references-->

<!--Article references-->
[개발 개요]: sql-data-warehouse-overview-develop.md

<!--MSDN references-->

<!--Other Web references-->

<!---HONumber=July15_HO3-->