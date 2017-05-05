---
title: "데이터베이스 호환성 수준 130 - Azure SQL Database | Microsoft Docs"
description: "이 문서에서는 Azure SQL Database를 호환성 수준 130으로 실행하고 새로운 쿼리 최적화 프로그램과 쿼리 프로세서 기능의 이점을 활용하여 얻을 수 있는 이점에 대해 살펴봅니다. 또한 기존 SQL 응용 프로그램의 쿼리 성능에 발생할 수 있는 잠재적인 부작용도 해결합니다."
services: sql-database
documentationcenter: 
author: alainlissoir
manager: jhubbard
editor: 
ms.assetid: 8619f90b-7516-46dc-9885-98429add0053
ms.service: sql-database
ms.custom: monitor and tune
ms.workload: data-management
ms.devlang: NA
ms.tgt_pltfrm: NA
ms.topic: article
ms.date: 03/03/2017
ms.author: alainl
translationtype: Human Translation
ms.sourcegitcommit: 757d6f778774e4439f2c290ef78cbffd2c5cf35e
ms.openlocfilehash: a5e84ded4e7b574a24583be507902f9537328153
ms.lasthandoff: 04/10/2017


---
# <a name="improved-query-performance-with-compatibility-level-130-in-azure-sql-database"></a>Azure SQL 데이터베이스의 호환성 수준 130을 통해 개선된 쿼리 성능
Azure SQL 데이터베이스는 다양한 호환성 수준에서 수십만 개의 데이터베이스를 투명하게 실행하고 있으며, Microsoft SQL Server 해당 버전의 모든 고객을 위해 이전 버전과의 호환성을 유지 및 보장하고 있습니다.

이 문서에서는 Azure SQL Database를 호환성 수준 130으로 실행하고 새로운 쿼리 최적화 프로그램과 쿼리 프로세서 기능의 이점을 활용하여 얻을 수 있는 이점에 대해 살펴봅니다. 또한 기존 SQL 응용 프로그램의 쿼리 성능에 발생할 수 있는 잠재적인 부작용도 해결합니다.

지난 기록을 확인하자면, 기본 호환성 수준 대비 SQL 버전 내역은 다음과 같습니다.

* 100: SQL Server 2008 및 Azure SQL Database
* 110: SQL Server 2012 및 Azure SQL Database
* 120: SQL Server 2014 및 Azure SQL Database
* 130: SQL Server 2016 및 Azure SQL Database

> [!IMPORTANT]
> 기본 호환성 수준은 **새로 만든** 데이터베이스의 경우 130입니다.
> 
> 2016년 6월 중순 이전에 만든 데이터베이스의 호환성 수준은 만든 시기에 정해졌습니다. 이전 버전의 Azure SQL Database에서 마이그레이션된 데이터베이스의 호환성 수준은 100 또는 110입니다. 2016년 6월 중순 이전에 만든 서버에 대한 마스터 데이터베이스의 호환성 수준은 서버를 만든 시기에 정해졌으며 변경할 수 없습니다. 마스터 데이터베이스와 사용자 데이터베이스 간에 다른 호환성 수준은 사용자 데이터베이스의 기능 또는 성능에 영향을 미치지 않습니다.
> 

## <a name="about-compatibility-level-130"></a>호환성 수준 130에 대한 정보
먼저, 데이터베이스의 현재 호환성 수준을 알아보려면, 다음 Transact-SQL 문을 실행합니다.

```
SELECT compatibility_level
    FROM sys.databases
    WHERE name = '<YOUR DATABASE_NAME>’;
```


**새로** 생성되는 데이터베이스에 수준 130이 적용되는 상황이 벌어지기 전에, 매우 기본적인 쿼리 예제를 통해 무엇이 변하는 것인지, 그것을 통해 어떻게 이익을 얻을 수 있을지에 대해 검토하겠습니다.

관계형 데이터베이스에서의 쿼리 처리는 매우 복잡하며 본질적인 디자인에 대한 선택과 동작을 이해하려면 컴퓨터 공학과 수학에 대한 깊은 배경 지식이 필요합니다. 이 문서에서는, 최소의 기술적 지식이 있다면 누구나 호환성 수준의 변경이 미치는 영향을 이해하고 응용 프로그램에 어떠한 이점을 줄 수 있는지 판단할 수 있도록 하기 위하여 콘텐츠를 의도적으로 간소화하였습니다.

호환성 수준 130이 가져다 주는 것이 무엇인지 테이블에서 간략하게 살펴보겠습니다.  [ALTER DATABASE 호환성 수준(Transact-SQL)](https://msdn.microsoft.com/library/bb510680.aspx)에서 자세한 내용을 볼 수 있지만, 여기에 간략한 요약을 제공합니다.

* Insert-select 문의 삽입 작업이, 이전에는 단일 스레드였지만, 다중 스레드로 만들어 지거나 병렬 계획을 가질 수 있습니다.
* 메모리 액세스에 최적화된 테이블 및 테이블 변수 쿼리가, 이전에는 단일 스레드 작업이었지만, 이제 병렬 계획을 가질 수 있습니다.
* 메모리 액세스에 최적화된 테이블에 대한 통계를 이제 샘플링하고 자동으로 업데이트할 수 있습니다. 자세한 내용은 [What's New in Database Engine: In-Memory OLTP](https://msdn.microsoft.com/library/bb510411.aspx#InMemory) (데이터베이스 엔진의 새로운 기능: 메모리 내 OLTP)를 참조하세요.
* 배치 모드 및 행 모드가 열 저장소 인덱스와 함께 변경됩니다.
  * 열 저장소 인덱스를 통한 테이블 정렬이 이제 배치 모드로 수행됩니다.
  * 기간 이동 집계가 이제 배치 모드(예: T-SQL LAG/LEAD 문)로 진행됩니다.
  * 여러 개의 다른 절을 포함하는 열 저장소 테이블에 대한 쿼리가 배치 모드에서 수행됩니다.
  * 직렬 계획 또는 DOP=1 하에서 실행되는 쿼리도 배치 모드로 실행됩니다.
* 마지막으로 카디널리티 예측 향상이 호환성 수준 120에 포함되지만, 이 보다 낮은 호환성 수준(즉, 100 또는 110)에서 실행하는 경우, 호환성 수준 130으로 이동하면 이러한 향상이 동반되며, 응용 프로그램의 쿼리 성능에도 유용할 수 있습니다.

## <a name="practicing-compatibility-level-130"></a>호환성 수준 130 연습
우선, 일부 새로운 기능을 연습하기 위해 테이블, 인덱스 및 난수 데이터를 만들겠습니다. T-SQL 스크립트 예제는 SQL Server 2016, 또는 Azure SQL Database에서 실행할 수 있습니다. 하지만, Azure SQL 데이터베이스를 만드는 경우, 다중 스레딩을 허용하여 이러한 기능으로부터 이익을 얻기 위해 코어가 적어도 두 개 필요하기 때문에 최소 P2 데이터베이스를 선택해야 합니다.

```
-- Create a Premium P2 Database in Azure SQL Database

CREATE DATABASE MyTestDB
    (EDITION=’Premium’, SERVICE_OBJECTIVE=’P2′);
GO

-- Create 2 tables with a column store index on
-- the second one (only available on Premium databases)

CREATE TABLE T_source
    (Color varchar(10), c1 bigint, c2 bigint);

CREATE TABLE T_target
    (c1 bigint, c2 bigint);

CREATE CLUSTERED COLUMNSTORE INDEX CCI ON T_target;
GO

-- Insert few rows.

INSERT T_source VALUES
    (‘Blue’, RAND() * 100000, RAND() * 100000),
    (‘Yellow’, RAND() * 100000, RAND() * 100000),
    (‘Red’, RAND() * 100000, RAND() * 100000),
    (‘Green’, RAND() * 100000, RAND() * 100000),
    (‘Black’, RAND() * 100000, RAND() * 100000);

GO 200

INSERT T_source SELECT * FROM T_source;

GO 10
```


이제, 호환성 수준 130과 함께 제공되는 일부 쿼리 처리 기능을 살펴보겠습니다.

## <a name="parallel-insert"></a>병렬 삽입
다음 T-SQL 문을 실행하면 호환성 수준 120 및 130에서 INSERT 작업이 실행되며, 이것은 단일 스레드 모델(120) 및 다중 스레드 모델(130)에서 각각 작업을 실행됩니다.

```
-- Parallel INSERT … SELECT … in heap or CCI
-- is available under 130 only

SET STATISTICS XML ON;

ALTER DATABASE MyTestDB
    SET COMPATIBILITY_LEVEL = 120;
GO 

-- The INSERT part is in serial

INSERT t_target WITH (tablock)
    SELECT C1, COUNT(C2) * 10 * RAND()
        FROM T_source
        GROUP BY C1
    OPTION (RECOMPILE);

ALTER DATABASE MyTestDB
    SET COMPATIBILITY_LEVEL = 130
GO

-- The INSERT part is in parallel

INSERT t_target WITH (tablock)
    SELECT C1, COUNT(C2) * 10 * RAND()
        FROM T_source
        GROUP BY C1
    OPTION (RECOMPILE);

SET STATISTICS XML OFF;
```


실제 쿼리 계획을 요청하고, 그에 대한 그래픽 표현 또는 XML 콘텐츠를 살펴보면, 실행 중인 Cardinality Estimation 함수를 판단할 수 있습니다. 그림 1의 계획을 나란히 살펴보면, Column Store INSERT 실행이 120의 경우 직렬에서 130의 경우 병렬로 이동한 것을 명확하게 볼 수 있습니다. 또한, 130 계획의 반복기 아이콘이, 이제 반복기 실행도 실제로 병렬이라는 사실을 나타내는, 두 개의 병렬 화살표를 나타내도록 변경된 것이 보입니다. 대규모 INSERT 작업을 완료해야 하는 경우, 병렬 실행이, 데이터베이스에 대해 원하는 대로 사용할 수 있는 수의 코어에 연결되어 있으면, 상황에 따라서 최대 100배 빠른 성능을 낼 수 있습니다.

*그림 1: 호환성 수준 130에서 INSERT 작업은 직렬에서 병렬로 변경됩니다.*

![그림 1](./media/sql-database-compatibility-level-query-performance-130/figure-1.jpg)

## <a name="serial-batch-mode"></a>직렬 배치 모드
마찬가지로, 데이터 행을 처리할 때 호환성 수준 130으로 이동하면 배치 모드 처리가 가능합니다. 첫째, 배치 모드 작업은 열 저장소 인덱스가 있어야만 가능합니다. 둘째, 하나의 배치는 일반적으로 900행을 나타내고, 멀티 코어 CPU, 높은 메모리 처리량에 최적화된 코드 논리를 사용하며, 가능하면 열 저장소의 압축 데이터를 직접 활용합니다. 이러한 조건 하에서, SQL Server 2016은 한 번에 최대 900개의 행을 처리할 수 있고(한 번에 1개의 행이 아닌), 그 결과, 작업의 전반적인 오버헤드 비용이 전체 배치에 의해 공유되기 때문에, 행별 전체 비용이 감소됩니다. 이렇게 공유되는 작업 수가 열 저장소 압축과 결합되면 SELECT 배치 모드 작업에 관련된 대기 시간이 근본적으로 감소됩니다. 열 저장소 및 배치 모드에 대한 자세한 내용은 [Columnstore 인덱스 가이드](https://msdn.microsoft.com/library/gg492088.aspx)를 참조하세요.

```
-- Serial batch mode execution

SET STATISTICS XML ON;

ALTER DATABASE MyTestDB
    SET COMPATIBILITY_LEVEL = 120;
GO

-- The scan and aggregate are in row mode

SELECT C1, COUNT (C2)
    FROM T_target
    GROUP BY C1
    OPTION (MAXDOP 1, RECOMPILE);
GO

ALTER DATABASE MyTestDB
    SET COMPATIBILITY_LEVEL = 130;
GO 

-- The scan and aggregate are in batch mode,
-- and force MAXDOP to 1 to show that batch mode
-- also now works in serial mode.

SELECT C1, COUNT(C2)
    FROM T_target
    GROUP BY C1
    OPTION (MAXDOP 1, RECOMPILE);
GO

SET STATISTICS XML OFF;
```


그림 2의 쿼리 계획을 나란히 살펴보면, 처리 모드가 호환성 수준과 함께 변경된 것을 볼 수 있으며, 그 결과, 두 가지 호환성 모드에서 모두 함께 쿼리를 실행하면, 두 개의 배치가 처리된 시간에, 대부분의 처리 시간이 배치 모드(14%)와 비교하면 행 모드(86%)에 소요된 것을 볼 수 있습니다. 데이터 집합이 증가하면 이점이 증가합니다.

*그림 2: 호환성 수준 130에서 SELECT 작업은 직렬에서 배치 모드로 변경됩니다.*

![그림 2](./media/sql-database-compatibility-level-query-performance-130/figure-2.jpg)

## <a name="batch-mode-on-sort-execution"></a>배치 모드에서 정렬 실행
위의 예와 마찬가지로 정렬 작업을, 행 모드(호환성 수준 120)에서 배치 모드(호환성 수준 130)로 전환하면 SORT 작업의 성능이 같은 이유로 인해 향상됩니다.

```
-- Batch mode on sort execution

SET STATISTICS XML ON;

ALTER DATABASE MyTestDB
    SET COMPATIBILITY_LEVEL = 120;
GO

-- The scan and aggregate are in row mode

SELECT C1, COUNT(C2)
    FROM T_target
    GROUP BY C1
    ORDER BY C1
    OPTION (MAXDOP 1, RECOMPILE);
GO

ALTER DATABASE MyTestDB
    SET COMPATIBILITY_LEVEL = 130;
GO

-- The scan and aggregate are in batch mode,
-- and force MAXDOP to 1 to show that batch mode
-- also now works in serial mode.

SELECT C1, COUNT(C2)
    FROM T_target
    GROUP BY C1
    ORDER BY C1
    OPTION (MAXDOP 1, RECOMPILE);
GO

SET STATISTICS XML OFF;
```


그림 3에 나란히 보이는 것처럼, 행 모드의 정렬 작업은 비용의 81%를 나타내는 반면, 배치 모드는 비용의 19%만을 나타냅니다(각각 정렬의 81% 및 56%).

*그림 3: 호환성 수준 130에서 SORT 작업은 행에서 배치 모드로 변경됩니다.*

![그림 3](./media/sql-database-compatibility-level-query-performance-130/figure-3.png)

물론, 여기의 샘플은 행을 수만 개만 포함하며, 이것은 요즘 대부분의 SQL Server에서 사용할 수 있는 데이터를 살펴볼 때 매우 미미한 양입니다. 대신, 이것을 수백만 개의 행에 투영하면, 워크로드의 특성에 따라 매일 몇 분 동안의 실행을 절약하는 것으로 해석할 수 있습니다.

## <a name="cardinality-estimation-ce-improvements"></a>카디널리티 예측(CE) 향상
SQL Server 2014에 도입된, 호환성 수준 120 이상에서 실행되는 모든 데이터베이스는 새로운 카디널리티 예측 기능을 활용합니다. 본질적으로, 카디널리티 예측은 SQL Server에서 예상 비용을 기반으로 쿼리를 실행하는 방식을 결정하는 데 사용되는 논리입니다. 예측은 쿼리와 관련된 개체와 연결된 통계로 부터의 입력을 사용하여 계산됩니다. 사실상, 대략적으로, Cardinality Estimation 함수는 값의 분포, 별도의 값의 개수, 쿼리에 의해 참조되는 개체 및 테이블에 포함된 중복 개수에 대한 정보에 따른 행 개수에 대한 예측입니다. 이러한 예측이 잘못되는 경우에 대한 몇 가지 예를 들자면, 불필요한 메모리 허용(즉, TempDB 유출)으로 인해 불필요한 디스크 I/O가 발생하거나, 병렬 계획 실행 대신 직렬 계획 실행이 선택될 수 있습니다. 요컨대, 잘못된 예측은 전반적인 쿼리 실행 성능을 저하시킬 수 있습니다. 반면에, 우수한 예측, 보다 정확한 예측은 쿼리 실행 성능을 향상시킬 수 있습니다.

앞서 언급했듯이, 쿼리 최적화와 예측은 복잡한 사안이지만, 쿼리 계획 및 카디널리티 평가기에 대해 자세히 알아보려면, [Optimizing Your Query Plans with the SQL Server 2014 Cardinality Estimator](https://msdn.microsoft.com/library/dn673537.aspx) (SQL Server 2014 카디널리티 평가기를 사용하여 쿼리 계획 최적화)에서 문서를 참조하세요.

## <a name="which-cardinality-estimation-do-you-currently-use"></a>현재 어떤 카디널리티 예측을 사용하고 있습니까?
어떤 카디널리티 예측 하에서 쿼리가 실행되고 있는지를 알아보기 위해서 아래 쿼리 샘플을 사용하십시오. 첫째 예제는 호환성 수준 110에서 실행되며, 이것은 오래된 Cardinality Estimation 함수를 사용한다는 의미를 내포합니다.

```
-- Old CE

ALTER DATABASE MyTestDB
    SET COMPATIBILITY_LEVEL = 110;
GO

SET STATISTICS XML ON;

SELECT [c1]
    FROM [dbo].[T_target]
    WHERE [c1] > 20000;
GO

SET STATISTICS XML OFF;
```


실행이 완료되면, XML 링크를 클릭하고, 첫 번째 반복기의 속성을 살펴봅니다. CardinalityEstimationModelVersion이라는 속성 이름은 현재 70으로 설정되어 있습니다. 이것은 데이터베이스 호환성 수준이 SQL Server 7.0 버전(위의 T-SQL 문에서 볼 수 있듯이 110으로 설정되어 있음)으로 설정되어 있다는 것을 의미하는 것은 아니며, 70이라는 값은 단지 SQL Server 7.0 이상에서 사용할 수 있는 레거시 카디널리티 예측 기능을 나타내고, SQL Server 7.0 이상은 SQL Server 2014(호환성 수준 120이 제공되는)까지 중요한 수정 내용이 없었습니다.

*그림 4: 호환성 수준을 110 이하로 사용할 때 CardinalityEstimationModelVersion은 70으로 설정됩니다.*

![그림 4](./media/sql-database-compatibility-level-query-performance-130/figure-4.png)

아니면, 호환성 수준을 130으로 변경하고, ON으로 설정된 LEGACY_CARDINALITY_ESTIMATION 설정과 [ALTER DATABASE SCOPED CONFIGURATION](https://msdn.microsoft.com/library/mt629158.aspx)을 사용하여 새 Cardinality Estimation 함수의 사용을 비활성화할 수 있습니다. 이렇게 하면 Cardinality Estimation 함수의 측면에서 110을 사용하는 것과 똑같지만, 최신 쿼리 처리 호환성 수준을 사용합니다. 이렇게 하면, 최신 호환성 수준(즉, 배치 모드)에 제공되는 새로운 쿼리 처리 기능의 이점을 얻을 수 있지만, 필요한 경우 이전 카디널리티 예측 기능에 여전히 의존합니다.

```
-- Old CE

ALTER DATABASE MyTestDB
    SET COMPATIBILITY_LEVEL = 130;
GO

ALTER DATABASE
    SCOPED CONFIGURATION
    SET LEGACY_CARDINALITY_ESTIMATION = ON;
GO

SET STATISTICS XML ON;

SELECT [c1]
    FROM [dbo].[T_target]
    WHERE [c1] > 20000;
GO

SET STATISTICS XML OFF;
```


호환성 수준을 120 또는 130으로 변경하면 새로운 카디널리티 예측 기능을 사용할 수 있습니다. 이러한 경우 기본 CardinalityEstimationModelVersion은 120 또는 130으로 설정됩니다.

```
-- New CE

ALTER DATABASE MyTestDB
    SET COMPATIBILITY_LEVEL = 130;
GO

ALTER DATABASE
    SCOPED CONFIGURATION
    SET LEGACY_CARDINALITY_ESTIMATION = OFF;
GO

SET STATISTICS XML ON;

SELECT [c1]
    FROM [dbo].[T_target]
    WHERE [c1] > 20000;
GO

SET STATISTICS XML OFF;
```


*그림 5: 호환성 수준 130을 사용하면 CardinalityEstimationModelVersion이 130으로 설정됩니다.*

![그림 5](./media/sql-database-compatibility-level-query-performance-130/figure-5.jpg)

## <a name="witnessing-the-cardinality-estimation-differences"></a>카디널리티 예측 차이 살펴보기
이제, 조건자와 INNER JOIN과 WHERE 절이 포함된 약간 더 복잡한 쿼리를 실행하고 이전 Cardinality Estimation 함수의 행 개수 예측을 우선 살펴보겠습니다.

```
-- Old CE row estimate with INNER JOIN and WHERE clause

ALTER DATABASE MyTestDB
    SET COMPATIBILITY_LEVEL = 130;
GO

ALTER DATABASE
    SCOPED CONFIGURATION
    SET LEGACY_CARDINALITY_ESTIMATION = ON;
GO

SET STATISTICS XML ON;

SELECT T.[c2]
    FROM
                   [dbo].[T_source] S
        INNER JOIN [dbo].[T_target] T  ON T.c1=S.c1
    WHERE
        S.[Color] = ‘Red’  AND
        S.[c2] > 2000  AND
        T.[c2] > 2000
    OPTION (RECOMPILE);
GO

SET STATISTICS XML OFF;
```


이 쿼리를 효과적으로 실행하면 200,704개의 행이 반환되는 반면에, 이전 카디널리티 예측 기능은 194,284개의 행을 예측합니다. 물론, 앞서 언급했듯이, 이러한 행 개수 결과는 이전 샘플을 얼마나 자주 실행하느냐에 달려 있으며, 샘플을 실행할 때마다 샘플 테이블이 계속해서 다시 채워집니다. 물론, 쿼리의 조건자도 테이블 모양, 데이터 콘텐츠, 데이터가 서로 상관 관계를 맺는 방식과 별개로 실제 예측에 영향을 미칩니다.

*그림 6: 행 개수 예측은 194,284개 이거나 예측된 200,704개 행에서 6,000개 행이 해제됩니다.*

![그림 6](./media/sql-database-compatibility-level-query-performance-130/figure-6.jpg)

같은 방식으로, 이제 카디널리티 예측 기능을 사용하여 동일한 쿼리를 실행하겠습니다.

```
-- New CE row estimate with INNER JOIN and WHERE clause

ALTER DATABASE MyTestDB
    SET COMPATIBILITY_LEVEL = 130;
GO

ALTER DATABASE
    SCOPED CONFIGURATION
    SET LEGACY_CARDINALITY_ESTIMATION = OFF;
GO

SET STATISTICS XML ON;

SELECT T.[c2]
    FROM
                   [dbo].[T_source] S
        INNER JOIN [dbo].[T_target] T  ON T.c1=S.c1
    WHERE
        S.[Color] = ‘Red’  AND
        S.[c2] > 2000  AND
        T.[c2] > 2000
    OPTION (RECOMPILE);
GO

SET STATISTICS XML OFF;
```


행 예측이 202,877이거나, 이전 카디널리티 예측보다 훨씬 더 가깝고 큰 것을 볼 수 있습니다.

*그림 7: 행 개수 예측이 이제 194,284가 아니라 202,877입니다.*

![그림 7](./media/sql-database-compatibility-level-query-performance-130/figure-7.jpg)

실제로, 결과 집합은 200,704행입니다.(하지만 모든 것은 이전 샘플의 쿼리를 얼마나 자주 실행했는가에 달려 있고, 그 보다 더 중요한 것은, T-SQL이 RAND() 문을 사용하기 때문에, 실제 반환되는 값이 실행할 때마다 다를 수 있다는 점입니다.) 따라서, 이 예제에서는 202,877이 194,284보다 200,704에 훨씬 더 가깝기 때문에, 새 카디널리티 예측이 행의 개수를 더 잘 측정했습니다. 마지막으로 WHERE 절 조건자를 같음(예를 들면 “>” 대신)으로 변경하면, 일치하는 수가 얼마나 되느냐에 따라서, 이전 카디널리티 함수와 새로운 카디널리티 함수의 예측이 한층 더 달라질 수 있습니다.

물론, 이 경우, 실제 카운트에서 최대 6000개의 행이 해제되면 상황에 따라서 많은 데이터를 나타내지 않습니다. 이제, 이것을 더 복잡한 쿼리와 여러 개의 테이블에 걸친 수백만 개의 행으로 이동하면, 때때로 예측이 수백만 개의 행에 의해 해제될 수 있으며, 따라서, 잘못된 실행 계획을 선택하거나 부족한 메모리를 요청할 위험으로 인해 TempDB 유출이 발생하고, I/O가 훨씬 더 많아질 수 있습니다.

기회가 된다면, 가장 일반적인 쿼리와 데이터 집합을 사용하여 이러한 비교를 연습하고, 이전 예측과 새로운 예측에 얼마나 영향을 미치는지 직접 확인하세요. 경우에 따라 실제보다 더 밑돌 수도 있고, 결과 집합에 반환되는 실제 행 카운트에 더 가까울 수도 있습니다. 이 모든 것은 쿼리의 모양, Azure SQL Database의 특성, 데이터 집합의 본질 및 크기, 그리고 이들 항목에 대해 사용할 수 있는 통계에 달려 있습니다. Azure SQL Database 인스턴스를 만들었다면, 쿼리 최적화 프로그램은 이전 쿼리 실행에서 생성된 통계를 다시 사용하는 대신 정보를 처음부터 다시 구축해야 합니다. 따라서, 예측은 모든 서버 및 응용 프로그램 각각의 상황에 따라 매우 달라집니다. 이런 중요한 측면을 염두에 두어야 합니다.

## <a name="some-considerations-to-take-into-account"></a>고려해야 할 몇 가지 사항
대부분의 워크로드는 호환성 수준 130을 통해 이점을 갖게 되지만, 프로덕션 환경에 대한 호환성 수준을 채택하기 전에, 기본적으로 3가지 옵션이 있습니다.

1. 호환성 수준 130이로 이동하고, 성능 추이를 살펴봅니다. 성능이 떨어지는 경우에는, 호환성 수준을 이전 수준으로 되돌리거나, 130을 유지하고 카디널리티 예측만 레거시 모드로 되돌립니다.(앞서 설명했듯이, 이것만으로 문제를 해결할 수 있습니다.)
2. 비슷한 프로덕션 부하에서 기존 응용 프로그램을 철저히 테스트하여, 미세 조정하고, 프로덕션에 적용하기 전에 성능의 유효성을 검사합니다. 문제가 발생하는 경우, 위와 마찬가지로, 언제나 이전 호환성 수준으로 되돌릴 수 있고 아니면 카디널리티 예측만 레거시 모드로 되돌릴 수 있습니다.
3. 마지막 옵션이면서, 이러한 질문을 해결하는 최신의 방법은 쿼리 저장소를 활용하는 것입니다. 이것이 현재 권장되는 옵션입니다. 호환성 수준 130대비 120 이하의 쿼리 분석을 지원하기 위해, 쿼리 저장소를 사용하는 것은 매우 바람직합니다. 쿼리 저장소는 최신 버전의 Azure SQL Database에 제공되며, 쿼리 성능 문제 해결을 지원하도록 디자인되었습니다. 쿼리 저장소는 모든 쿼리에 대한 자세한 기록 정보를 수집하고 제공하는 데이터베이스에 대한 블랙박스라고 생각할 수 있습니다. 이것은 문제를 진단하고 해결하는 시간을 줄여서 성능에 대한 기술적 조사를 크게 간소화합니다. 자세한 내용은 [Query Store: A flight data recorder for your database](https://azure.microsoft.com/blog/query-store-a-flight-data-recorder-for-your-database/)(쿼리 저장소: 데이터베이스에 대한 블랙박스)를 참조하세요.

대략적으로, 호환성 수준 120 이하에서 실행 중인 데이터베이스 집합이 있고 그 중 일부를 130으로 이동할 계획이거나, 워크로드가 새 데이터베이스를 자동으로 프로비전하기 때문에 곧 130으로 기본 설정될 예정인 경우에는, 다음 사항을 고려하십시오.

* 프로덕션을 새 호환성 수준으로 변경하기 전에, 쿼리 저장소를 활성화합니다. 자세한 내용은 [Change the Database Compatibility Mode and Use the Query Store](https://msdn.microsoft.com/library/bb895281.aspx) (데이터베이스 호환성 모드 변경 및 쿼리 저장소 사용)을 참조하세요.
* 다음으로, 프로덕션과 유사한 환경의 대표적인 데이터와 쿼리를 사용하여 모든 중요한 워크로드를 테스트하여, 체감 성능과 쿼리 저장소에 의해 보고되는 성능을 비교합니다. 성능 저하를 체감한다면, 쿼리 저장소에서 회귀된 쿼리를 파악하여 쿼리 저장소의 옵션을 강제 적용하는 계획을(즉, 계획 고정) 사용할 수 있습니다. 이런 경우, 호환성 수준 130을 반드시 유지하고, 쿼리 저장소의 제시에 따라 이전 쿼리 계획을 사용합니다.
* Azure SQL Database의(SQL Server 2016을 실행하는) 새로운 기능과 성능을 활용하고 싶지만, 호환성 수준 130이 가져오는 변화에 신경이 쓰이는 경우에는, 최후의 수단으로, ALTER DATABASE 문을 사용하여 사용자의 워크로드에 적합한 수준으로 호환성 수준을 되돌리는 것을 고려할 수 있습니다. 하지만 먼저, 130을 사용하지 않는다면 근본적으로 이전 SQL Server 버전의 성능 수준을 유지하는 것이므로, 쿼리 저장소 계획 고정 옵션이 최고의 옵션이라는 점을 인지하시기 바랍니다.
* 다수의 데이터베이스에 걸쳐있는 다중 테넌트 응용 프로그램이 있다면, 이전에 프로비전한 데이터베이스와 새로 프로비전한 데이터베이스를 비롯한 모든 데이터베이스에 대해 일관적인 호환성 수준을 보장하기 위해, 데이터베이스의 프로비전 논리를 업데이트하는 것이 필요합니다. 응용 프로그램 워크로드 성능은 일부 데이터베이스가 다른 호환성 수준에서 실행되는 경우에 민감하게 영향을 받을 수 있기 때문에, 모든 사용자에게 전반적으로 동일한 환경을 제공하려면, 모든 데이터베이스에 대해 일관적인 호환성 수준을 적용할 필요가 있습니다. 필수 사항은 아니며, 응용 프로그램이 호환성 수준에 의해 어떻게 영향을 받는가에 따라 달라질 수 있습니다.
* 마지막으로, 카디널리티 예측과 관련하여, 새로운 조건 하에서 프로덕션 워크로드를 테스트하고 카디널리티 예측 향상을 통해 응용 프로그램에 이로운 점이 있는지를 측정하는 것이 좋습니다.

## <a name="conclusion"></a>결론
Azure SQL 데이터베이스를 사용하여 SQL Server 2016의 모든 개선 사항을 활용하면 쿼리 실행이 분명히 개선됩니다. 물론, 다른 새로운 기능과 마찬가지로, 데이터베이스 워크로드가 최고로 작동되는 정확한 조건을 측정하기 위해 적절한 평가가 수행되어야 합니다. 경험에 의하면, 대부분의 워크로드는 호환성 수준 130 하에서, 새로운 쿼리 처리 기능 및 새 카디널리티 예측을 처리하면서, 최소한 투명하게 실행될 것으로 예상됩니다. 그렇긴 하지만, 현실적으로, 언제나 예외는 있으며 적절하고 부단하게 주의를 기울이는 것이 이러한 개선 사항의 이점을 얼마나 활용할 수 있는지를 결정하는 중요한 척도가 될 수 있습니다. 다시 말하지만, 쿼리 저장소는 이러한 작업을 수행하는데 큰 도움이 될 수 있습니다.

## <a name="references"></a>참조
* [데이터베이스 엔진의 새로운 기능](https://msdn.microsoft.com/library/bb510411.aspx#InMemory)
* [블로그: 쿼리 저장소: 데이터베이스에 대한 블랙박스, 작성자: Borko Novakovic, 2016년 6월 8일](https://azure.microsoft.com/blog/query-store-a-flight-data-recorder-for-your-database/)
* [ALTER DATABASE 호환성 수준(Transact-SQL)](https://msdn.microsoft.com/library/bb510680.aspx)
* [ALTER DATABASE SCOPED CONFIGURATION](https://msdn.microsoft.com/library/mt629158.aspx)
* [Azure SQL Database에 대한 호환성 수준 130](https://azure.microsoft.com/updates/compatibility-level-130-for-azure-sql-database-v12/)
* [SQL Server 2014 카디널리티 평가기를 사용하여 쿼리 계획 최적화](https://msdn.microsoft.com/library/dn673537.aspx)
* [Columnstore 인덱스 가이드](https://msdn.microsoft.com/library/gg492088.aspx)
* [블로그: Azure SQL 데이터베이스의 호환성 수준 130을 통해 개선된 쿼리 성능, 작성자: Alain Lissoir, 2016년 5월 6일](https://blogs.msdn.microsoft.com/sqlserverstorageengine/2016/05/06/improved-query-performance-with-compatibility-level-130-in-azure-sql-database/)


