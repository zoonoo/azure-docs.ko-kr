---
title: 시냅스 SQL 풀에서 트랜잭션 사용
description: 이 문서에는 트랜잭션을 구현하고 Synapse SQL 풀에서 솔루션을 개발하기 위한 팁이 포함되어 있습니다.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 03/22/2019
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: d9578653ff8074fee8336df447caf119f79febe0
ms.sourcegitcommit: bd5fee5c56f2cbe74aa8569a1a5bce12a3b3efa6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/06/2020
ms.locfileid: "80745262"
---
# <a name="use-transactions-in-synapse-sql-pool"></a>시냅스 SQL 풀에서 트랜잭션 사용

이 문서에는 트랜잭션을 구현하고 SQL 풀에서 솔루션을 개발하기 위한 팁이 포함되어 있습니다.

## <a name="what-to-expect"></a>예상 프로그램

예상대로 SQL 풀은 데이터 웨어하우스 워크로드의 일부로 트랜잭션을 지원합니다. 그러나 SQL 풀을 대규모로 유지 관리하려면 SQL Server와 비교할 때 일부 기능이 제한됩니다. 이 문서에서는 차이점을 강조합니다.

## <a name="transaction-isolation-levels"></a>트랜잭션 격리 수준

SQL 풀은 ACID 트랜잭션을 구현합니다. 트랜잭션 지원의 격리 수준은 커밋되지 않은 읽기기본값입니다.  마스터 데이터베이스에 연결할 때 사용자 데이터베이스에 대한 READ_COMMITTED_SNAPSHOT 데이터베이스 옵션을 켜서 커밋된 스냅숏 격리 읽기로 변경할 수 있습니다.  

활성화되면 이 데이터베이스의 모든 트랜잭션이 READ COMMITTED SNAPSHOT 격리하에 실행되고 세션 수준에서 커밋되지 않은 READ 를 설정하면 적용되지 않습니다. 자세한 내용은 [데이터베이스 SET 변경 옵션(Transact-SQL)을](/sql/t-sql/statements/alter-database-transact-sql-set-options?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) 확인하십시오.

## <a name="transaction-size"></a>트랜잭션 크기

단일 데이터 수정 트랜잭션은 크기가 제한됩니다. 이러한 제한은 배포 기준으로 적용됩니다. 따라서 제한을 배포 수와 곱하여 전체 할당을 계산할 수 있습니다.

트랜잭션에 포함된 대략적인 최대 행 수를 구하려면 배포 용량을 각 행의 전체 크기로 나눕니다. 가변 길이 열의 경우에는 최대 크기를 사용하는 대신, 평균 열 길이를 사용하는 것을 고려합니다.

다음 표에서는 두 가지 가정이 수행되었습니다.

* 균일한 데이터 분포가 발생했습니다.
* 평균 행 길이는 250바이트입니다.

## <a name="gen2"></a>2세대

| [DWU](sql-data-warehouse-overview-what-is.md) | 분포당 상한선(GB) | 배포 수 | 최대 트랜잭션 크기(GB) | # 배포당 행 수 | 트랜잭션당 최대 행 수 |
| --- | --- | --- | --- | --- | --- |
| DW100c |1 |60 |60 |4,000,000 |240,000,000 |
| DW200c |1.5 |60 |90 |6,000,000 |360,000,000 |
| DW300c |2.25 |60 |135 |9,000,000 |540,000,000 |
| DW400c |3 |60 |180 |12,000,000 |720,000,000 |
| DW500c |3.75 |60 |225 |15,000,000 |900,000,000 |
| DW1000c |7.5 |60 |450 |30,000,000 |1,800,000,000 |
| DW1500c |11.25 |60 |675 |45,000,000 |2,700,000,000 |
| DW2000c |15 |60 |900 |60,000,000 |3,600,000,000 |
| DW2500c |18.75 |60 |1125 |75,000,000 |4,500,000,000 |
| DW3000c |22.5 |60 |1,350 |90,000,000 |5,400,000,000 |
| DW5000c |37.5 |60 |2,250 |150,000,000 |9,000,000,000 |
| DW6000c |45 |60 |2,700 |180,000,000 |10,800,000,000 |
| DW7500c |56.25 |60 |3,375 |225,000,000 |13,500,000,000 |
| DW10000c |75 |60 |4,500 |300,000,000 |18,000,000,000 |
| DW15000c |112.5 |60 |6,750 |450,000,000 |27,000,000,000 |
| DW30000c |225 |60 |13,500 |900,000,000 |54,000,000,000 |

## <a name="gen1"></a>1세대

| [DWU](sql-data-warehouse-overview-what-is.md) | 분포당 상한선(GB) | 배포 수 | 최대 트랜잭션 크기(GB) | # 배포당 행 수 | 트랜잭션당 최대 행 수 |
| --- | --- | --- | --- | --- | --- |
| DW100 |1 |60 |60 |4,000,000 |240,000,000 |
| DW200 |1.5 |60 |90 |6,000,000 |360,000,000 |
| DW300 |2.25 |60 |135 |9,000,000 |540,000,000 |
| DW400 |3 |60 |180 |12,000,000 |720,000,000 |
| DW500 |3.75 |60 |225 |15,000,000 |900,000,000 |
| DW600 |4.5. |60 |270 |18,000,000 |1,080,000,000 |
| DW1000 |7.5 |60 |450 |30,000,000 |1,800,000,000 |
| DW1200 |9 |60 |540 |36,000,000 |2,160,000,000 |
| DW1500 |11.25 |60 |675 |45,000,000 |2,700,000,000 |
| DW2000 |15 |60 |900 |60,000,000 |3,600,000,000 |
| DW3000 |22.5 |60 |1,350 |90,000,000 |5,400,000,000 |
| DW6000 |45 |60 |2,700 |180,000,000 |10,800,000,000 |

트랜잭션 또는 작업 기준으로 트랜잭션 크기 제한이 적용되며 모든 동시 트랜잭션에서 적용되지는 않습니다. 따라서 각 트랜잭션은 이 크기의 데이터를 로그에 쓰도록 허용됩니다.

로그에 기록된 데이터의 양을 최적화하고 최소화하려면 [트랜잭션 모범 사례](sql-data-warehouse-develop-best-practices-transactions.md) 문서를 참조하세요.

> [!WARNING]
> 최대 트랜잭션 크기는 데이터가 균일하게 분산되는 HASH 또는 ROUND_ROBIN 분산 테이블에 대해서만 도달할 수 있습니다. 트랜잭션이 균일하지 않은 분산 방식으로 데이터를 쓰는 경우 최대 트랜잭션 크기에 도달하기 전에 제한에 도달할 가능성이 높습니다.
> <!--REPLICATED_TABLE-->

## <a name="transaction-state"></a>트랜잭션 상태

SQL 풀은 XACT_STATE() 함수를 사용하여 값 -2를 사용하여 실패한 트랜잭션을 보고합니다. 이 값은 트랜잭션이 실패하고 롤백만 표시함을 의미합니다.

> [!NOTE]
> XACT_STATE 함수에서-2 사용은 실패한 트랜잭션이 SQL Server와 다른 동작을 표시함을 나타냅니다. SQL Server는 값 -1를 사용하여 커밋할 수 없는 트랜잭션을 나타냅니다. SQL Server는 커밋할 수 없음으로 표시하지 않고 트랜잭션 내 일부 오류를 허용할 수 있습니다. 예를 들어 `SELECT 1/0` 오류가 발생하지만 트랜잭션을 커밋할 수 없는 상태로 강제변환하지 는 않습니다.

또한 SQL Server는 커밋할 수 없는 트랜잭션에서 읽기를 허용합니다. 그러나 SQL 풀에서는 이 작업을 수행할 수 없습니다. SQL 풀 트랜잭션 내에서 오류가 발생하면 자동으로 -2 상태가 되고 문이 롤백될 때까지 더 이상 선택 문을 만들 수 없습니다.

따라서 응용 프로그램 코드에서 XACT_STATE()를 사용하는지 확인하는 것이 중요합니다.

예를 들어 SQL Server에서 다음과 같은 트랜잭션이 표시될 수 있습니다.

```sql
SET NOCOUNT ON;
DECLARE @xact_state smallint = 0;

BEGIN TRAN
    BEGIN TRY
        DECLARE @i INT;
        SET     @i = CONVERT(INT,'ABC');
    END TRY
    BEGIN CATCH
        SET @xact_state = XACT_STATE();

        SELECT  ERROR_NUMBER()    AS ErrNumber
        ,       ERROR_SEVERITY()  AS ErrSeverity
        ,       ERROR_STATE()     AS ErrState
        ,       ERROR_PROCEDURE() AS ErrProcedure
        ,       ERROR_MESSAGE()   AS ErrMessage
        ;

        IF @@TRANCOUNT > 0
        BEGIN
            ROLLBACK TRAN;
            PRINT 'ROLLBACK';
        END

    END CATCH;

IF @@TRANCOUNT >0
BEGIN
    PRINT 'COMMIT';
    COMMIT TRAN;
END

SELECT @xact_state AS TransactionState;
```

위의 코드는 다음과 같은 오류 메시지를 제공합니다.

Msg 111233, Level 16, State 1, Line 1 111233; 현재 트랜잭션이 중단되었으며 보류 중인 변경 내용은 롤백되었습니다. 이 문제의 원인은 DDL, DML 또는 SELECT 문 앞에 롤백되지 않는 롤백 전용 상태의 트랜잭션입니다.

또한 ERROR_* 함수의 출력도 제공되지 않습니다.

SQL 풀에서 코드를 약간 변경해야 합니다.

```sql
SET NOCOUNT ON;
DECLARE @xact_state smallint = 0;

BEGIN TRAN
    BEGIN TRY
        DECLARE @i INT;
        SET     @i = CONVERT(INT,'ABC');
    END TRY
    BEGIN CATCH
        SET @xact_state = XACT_STATE();

        IF @@TRANCOUNT > 0
        BEGIN
            ROLLBACK TRAN;
            PRINT 'ROLLBACK';
        END

        SELECT  ERROR_NUMBER()    AS ErrNumber
        ,       ERROR_SEVERITY()  AS ErrSeverity
        ,       ERROR_STATE()     AS ErrState
        ,       ERROR_PROCEDURE() AS ErrProcedure
        ,       ERROR_MESSAGE()   AS ErrMessage
        ;
    END CATCH;

IF @@TRANCOUNT >0
BEGIN
    PRINT 'COMMIT';
    COMMIT TRAN;
END

SELECT @xact_state AS TransactionState;
```

이제 예상되는 동작이 진행됩니다. 트랜잭션의 오류가 관리되고 ERROR_* 함수는 예상대로 값을 제공합니다.

변경된 부분은 트랜잭션의 ROLLBACK이 CATCH 블록의 오류 정보를 읽기 전에 발생해야 한다는 것입니다.

## <a name="error_line-function"></a>Error_Line() 함수

또한 SQL 풀은 ERROR_LINE() 함수를 구현하거나 지원하지 않는다는 점에 유의해야 합니다. 코드에 이 문제가 있는 경우 SQL 풀을 준수하려면 제거해야 합니다.

동등한 기능을 구현하는 대신 코드에서 쿼리 레이블을 사용합니다. 자세한 내용은 [레이블](sql-data-warehouse-develop-label.md) 문서를 참조하세요.

## <a name="using-throw-and-raiserror"></a>THROW 및 RAISERROR 사용

THROW는 SQL 풀에서 예외를 발생시키는 보다 현대적인 구현이지만 RAISERROR도 지원됩니다. 그러나 다음 몇 가지 사항에 주의해야 합니다.

* 사용자 정의 오류 메시지 번호는 THROW에 대해 100,000 - 150,000 범위에 있을 수 없습니다.
* RAISERROR 오류 메시지는 50,000으로 고정됩니다.
* sys.messages 사용은 지원되지 않습니다.

## <a name="limitations"></a>제한 사항

SQL 풀에는 트랜잭션과 관련된 몇 가지 다른 제한 사항이 있습니다.

다음과 같습니다.

* 분산된 트랜잭션이 없습니다
* 중첩된 트랜잭션이 허용되지 않습니다.
* 저장 점수를 사용할 수 없습니다.
* 명명된 트랜잭션이 없습니다.
* 표시된 트랜잭션이 없습니다.
* 사용자 정의 트랜잭션 내에 CREATE TABLE과 같은 DDL 지원은 없습니다.

## <a name="next-steps"></a>다음 단계

트랜잭션을 최적화하는 방법에 대한 자세한 내용은 [트랜잭션 모범 사례](sql-data-warehouse-develop-best-practices-transactions.md)를 참조하세요. 다른 SQL 풀 모범 사례에 대해 자세히 알아보려면 [SQL 풀 모범 사례를](sql-data-warehouse-best-practices.md)참조하세요.
