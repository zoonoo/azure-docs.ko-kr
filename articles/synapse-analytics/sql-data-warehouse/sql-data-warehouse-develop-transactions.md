---
title: Synapse SQL 풀에서 트랜잭션 사용
description: 이 문서에는 Synapse SQL 풀에서 트랜잭션을 구현 하 고 솔루션을 개발 하기 위한 팁이 포함 되어 있습니다.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 03/22/2019
ms.author: xiaoyul
ms.reviewer: igorstan
ms.openlocfilehash: 558b16fc348728c507af1fa0260a67ccacefed0f
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81416140"
---
# <a name="use-transactions-in-synapse-sql-pool"></a>Synapse SQL 풀에서 트랜잭션 사용

이 문서에는 트랜잭션을 구현 하 고 SQL 풀에서 솔루션을 개발 하기 위한 팁이 포함 되어 있습니다.

## <a name="what-to-expect"></a>예상 프로그램

짐작할 수 있듯이 SQL 풀은 데이터 웨어하우스 워크 로드의 일부로 트랜잭션을 지원 합니다. 그러나 SQL 풀이 대규모로 유지 되도록 하기 위해 SQL Server에 비해 일부 기능이 제한 됩니다. 이 문서에서는 차이점을 강조 합니다.

## <a name="transaction-isolation-levels"></a>트랜잭션 격리 수준

SQL 풀은 ACID 트랜잭션을 구현 합니다. 트랜잭션 지원의 격리 수준은 커밋되지 않은 읽기의 기본값입니다.  Master 데이터베이스에 연결 된 경우 사용자 데이터베이스에 대 한 READ_COMMITTED_SNAPSHOT 데이터베이스 옵션을 설정 하 여 커밋된 스냅숏 격리를 읽도록 변경할 수 있습니다.  

사용 하도록 설정 되 면이 데이터베이스의 모든 트랜잭션은 커밋된 읽기 스냅숏 격리에서 실행 되 고 세션 수준에서 커밋되지 않은 읽기 설정은 적용 되지 않습니다. 자세한 내용은 [ALTER DATABASE SET 옵션 (transact-sql)](/sql/t-sql/statements/alter-database-transact-sql-set-options?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) 을 참조 하세요.

## <a name="transaction-size"></a>트랜잭션 크기

단일 데이터 수정 트랜잭션은 크기가 제한됩니다. 이러한 제한은 배포 기준으로 적용됩니다. 따라서 제한을 배포 수와 곱하여 전체 할당을 계산할 수 있습니다.

트랜잭션에 포함된 대략적인 최대 행 수를 구하려면 배포 용량을 각 행의 전체 크기로 나눕니다. 가변 길이 열의 경우에는 최대 크기를 사용하는 대신, 평균 열 길이를 사용하는 것을 고려합니다.

다음 표에서는 두 가지 가정을 만들었습니다.

* 균일한 데이터 분포가 발생했습니다.
* 평균 행 길이는 250바이트입니다.

## <a name="gen2"></a>2세대

| [DWU](../../sql-data-warehouse/sql-data-warehouse-overview-what-is.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) | 배포 당 단면 (GB) | 배포 수 | 최대 트랜잭션 크기 (GB) | # 배포당 행 수 | 트랜잭션당 최대 행 수 |
| --- | --- | --- | --- | --- | --- |
| DW100c |1 |60 |60 |4,000,000 |240,000,000 |
| DW200c |1.5 |60 |90 |6,000,000 |360,000,000 |
| DW300c |2.25 |60 |135 |9,000,000 |540,000,000 |
| DW400c |3 |60 |180 |12,000,000 |720,000,000 |
| DW500c |3.75 |60 |225 |15,000,000 |900,000,000 |
| DW1000c |7.5 |60 |450 |30,000,000 |1,800,000,000 |
| DW1500c |11.25 |60 |675 |45,000,000 |2,700,000,000 |
| DW2000c |15 |60 |900 |60,000,000 |3,600,000,000 |
| DW2500c |18.75 |60 |1125 |7500만 |45억 |
| DW3000c |22.5 |60 |1,350 |90,000,000 |5,400,000,000 |
| DW5000c |37.5 |60 |2250 |1억5000만 |90억 |
| DW6000c |45 |60 |2,700 |180,000,000 |10,800,000,000 |
| DW7500c |56.25 |60 |3375 |2억2500만 |135억 |
| DW10000c |75 |60 |4,500 |300,000,000 |180억 |
| DW15000c |112.5 |60 |6,750 |4억5000만 |270억 |
| DW30000c |225 |60 |13500 |900,000,000 |540억 |

## <a name="gen1"></a>1세대

| [DWU](../../sql-data-warehouse/sql-data-warehouse-overview-what-is.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) | 배포 당 단면 (GB) | 배포 수 | 최대 트랜잭션 크기 (GB) | # 배포당 행 수 | 트랜잭션당 최대 행 수 |
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

SQL 풀은 XACT_STATE () 함수를 사용 하 여-2 값을 사용 하 여 실패 한 트랜잭션을 보고 합니다. 이 값은 트랜잭션이 실패하고 롤백만 표시함을 의미합니다.

> [!NOTE]
> XACT_STATE 함수에서-2 사용은 실패한 트랜잭션이 SQL Server와 다른 동작을 표시함을 나타냅니다. SQL Server는 값 -1를 사용하여 커밋할 수 없는 트랜잭션을 나타냅니다. SQL Server는 커밋할 수 없음으로 표시하지 않고 트랜잭션 내 일부 오류를 허용할 수 있습니다. 예를 들어 `SELECT 1/0` 은 오류를 발생 시킬 수 있지만 트랜잭션을 커밋할 수 없는 상태로 강제 적용 하지는 않습니다.

또한 SQL Server는 커밋할 수 없는 트랜잭션에서 읽기를 허용합니다. 그러나 SQL 풀을 사용 하면이 작업을 수행할 수 없습니다. SQL 풀 트랜잭션 내에서 오류가 발생 하는 경우-2 상태가 자동으로 시작 되며 문이 롤백될 때까지 추가 select 문을 수행할 수 없습니다.

따라서 코드를 수정 해야 할 수 있으므로 응용 프로그램 코드에서 XACT_STATE ()를 사용 하는지 확인 하는 것이 중요 합니다.

예를 들어 SQL Server에서 다음과 같은 트랜잭션이 표시 될 수 있습니다.

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

Msg 111233, Level 16, State 1, Line 1 111233; 현재 트랜잭션이 중단되었으며 보류 중인 변경 내용은 롤백되었습니다. 이 문제의 원인은 rollback 전용 상태의 트랜잭션이 DDL, DML 또는 SELECT 문 이전에 명시적으로 롤백되지 않는 것입니다.

또한 ERROR_* 함수의 출력도 제공되지 않습니다.

SQL 풀에서 코드는 약간 변경 해야 합니다.

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

또한 SQL 풀은 ERROR_LINE () 함수를 구현 하거나 지원 하지 않습니다. 코드에이를 사용 하는 경우 SQL 풀과 호환 되도록 제거 해야 합니다.

동등한 기능을 구현하는 대신 코드에서 쿼리 레이블을 사용합니다. 자세한 내용은 [레이블](sql-data-warehouse-develop-label.md) 문서를 참조하세요.

## <a name="using-throw-and-raiserror"></a>THROW 및 RAISERROR 사용

THROW는 SQL 풀에서 예외를 발생 시키는 최신 구현 이지만 RAISERROR도 지원 됩니다. 그러나 다음 몇 가지 사항에 주의해야 합니다.

* 사용자 정의 오류 메시지 번호는 THROW에 대해 100,000 - 150,000 범위에 있을 수 없습니다.
* RAISERROR 오류 메시지는 50,000으로 고정됩니다.
* sys.messages 사용은 지원되지 않습니다.

## <a name="limitations"></a>제한 사항

SQL 풀에는 트랜잭션과 관련 된 몇 가지 다른 제한 사항이 있습니다.

다음과 같습니다.

* 분산된 트랜잭션이 없습니다
* 중첩된 트랜잭션이 허용되지 않습니다.
* 저장 점수를 사용할 수 없습니다.
* 명명된 트랜잭션이 없습니다.
* 표시된 트랜잭션이 없습니다.
* 사용자 정의 트랜잭션 내에 CREATE TABLE과 같은 DDL 지원은 없습니다.

## <a name="next-steps"></a>다음 단계

트랜잭션을 최적화하는 방법에 대한 자세한 내용은 [트랜잭션 모범 사례](sql-data-warehouse-develop-best-practices-transactions.md)를 참조하세요. 다른 SQL 풀 모범 사례에 대 한 자세한 내용은 [sql 풀 모범 사례](sql-data-warehouse-best-practices.md)를 참조 하세요.
