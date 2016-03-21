<properties
   pageTitle="SQL 데이터 웨어하우스의 트랜잭션 | Microsoft Azure"
   description="솔루션 개발을 위한 Azure SQL 데이터 웨어하우스의 트랜잭션 구현을 위한 팁"
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
   ms.date="03/03/2016"
   ms.author="jrj;barbkess;sonyama"/>

# SQL 데이터 웨어하우스의 트랜잭션

기대한 것처럼 SQL 데이터 웨어하우스는 모든 트랜잭션 속성에 대한 지원을 제공합니다. 그러나 SQL 데이터 웨어하우스의 성능은 SQL Server와 비교할 때 일부 기능이 제한되는 수준으로 유지됩니다. 이 문서는 차이점을 강조 표시하고 다른 부분을 표시합니다.

## 트랜잭션 격리 수준
SQL 데이터 웨어하우스는 ACID 트랜잭션을 구현합니다. 그러나, 트랜잭션 지원의 격리는 `READ UNCOMMITTED`로 제한되며 변경할 수 없습니다. 다양한 코딩 메서드를 구현하여 더티 읽기를 염려하는 경우 이를 방지할 수 있습니다. 가장 인기 있는 메서드는 CTAS 및 테이블 파티션 전환(슬라이딩 창 패턴이라고 하는)을 모두 사용하여 사용자 준비 중인 데이터 쿼리를 방지합니다. 데이터를 사전 필터링하는 뷰가 일반적인 접근 방법이기도 합니다.

## 트랜잭션 상태
SQL 데이터 웨어하우스는 XACT\_STATE() 함수를 사용하여 값 -2를 사용하는 실패한 트랜잭션을 보고합니다. 트랜잭션이 실패하고 롤백만 표시함을 의미합니다.

> [AZURE.NOTE] XACT\_STATE 함수에서-2 사용은 실패한 트랜잭션이 SQL Server와 다른 동작을 표시함을 나타냅니다. SQL Server는 값 -1를 사용하여 커밋할 수 없는 트랜잭션을 나타냅니다. SQL Server는 커밋할 수 없음으로 표시하지 않고 트랜잭션 내 일부 오류를 허용할 수 있습니다. 예를 들어 SELECT 1/0은 오류를 발생시키지만 커밋할 수 없는 상태로 트랜잭션을 강제 적용하지 않습니다. 또한 SQL Server는 커밋할 수 없는 트랜잭션에서 읽기를 허용합니다. 그러나 SQLDW에서는 이 경우가 아닙니다. SQLDW 트랜잭션 내부에서 오류가 발생 하는 경우 -2 상태를 자동으로 입력하며 SELECT 1/0 오류를 포함합니다. 따라서 XACT\_STATE()를 사용하는 지 알기 위해 해당 응용 프로그램 코드를 확인하는 것이 중요합니다.

SQL Server에서 다음과 같은 코드 조각이 나타날 수 있습니다.

```
BEGIN TRAN
    BEGIN TRY
        DECLARE @i INT;
        SET     @i = CONVERT(int,'ABC');
    END TRY
    BEGIN CATCH

        DECLARE @xact smallint = XACT_STATE();

        SELECT  ERROR_NUMBER()    AS ErrNumber
        ,       ERROR_SEVERITY()  AS ErrSeverity
        ,       ERROR_STATE()     AS ErrState
        ,       ERROR_PROCEDURE() AS ErrProcedure
        ,       ERROR_MESSAGE()   AS ErrMessage
        ;

        ROLLBACK TRAN;

    END CATCH;
```

`SELECT` 문이 `ROLLBACK` 문 전에 실행됩니다. 또한 `@xact` 변수의 설정은 `SELECT`가 아닌 DECLARE를 사용합니다.

SQL 데이터 웨어하우스에서 코드는 다음과 같아야 합니다.

```
BEGIN TRAN
    BEGIN TRY
        DECLARE @i INT;
        SET     @i = CONVERT(int,'ABC');
    END TRY
    BEGIN CATCH

        ROLLBACK TRAN;

        DECLARE @xact smallint = XACT_STATE();

        SELECT  ERROR_NUMBER()    AS ErrNumber
        ,       ERROR_SEVERITY()  AS ErrSeverity
        ,       ERROR_STATE()     AS ErrState
        ,       ERROR_PROCEDURE() AS ErrProcedure
        ,       ERROR_MESSAGE()   AS ErrMessage
        ;
    END CATCH;

SELECT @xact;
```

`CATCH` 블록에서 오류 정보를 읽기 전에 트랜잭션의 롤백이 발생해야 합니다.

## Error\_Line() 함수
SQL 데이터 웨어하우스가 ERROR\_LINE() 함수를 구현하거나 지원하는 것 또한 주목할 가치가 있습니다. 이 코드에 있는 경우 SQL 데이터 웨어하우스와 호환되도록 제거해야 합니다. 동등한 기능을 구현하는 대신 코드에서 쿼리 레이블을 사용합니다. 이 기능에 대한 자세한 내용은 [쿼리 레이블] 문서를 참조하세요.

## THROW 및 RAISERROR 사용
THROW는 SQL 데이터 웨어하우스에서 예외를 발생시키기 위한 가장 최신 구현이지만 RAISERROR도 지원됩니다. 그러나 다음 몇 가지 사항에 주의해야 합니다.

- 사용자 정의 오류 메시지 번호는 THROW에 대해 100,000 - 150,000 범위에 있을 수 없습니다.
- RAISERROR 오류 메시지는 50,000으로 고정됩니다.
- sys.messages 사용은 지원되지 않습니다.

## 제한 사항
SQL 데이터 웨어하우스에는 트랜잭션과 관련된 몇 가지 기타 제한 사항이 있습니다.

다음과 같습니다.

- 분산된 트랜잭션이 없습니다
- 중첩된 트랜잭션이 허용되지 않습니다.
- 저장 점수를 사용할 수 없습니다.

## 다음 단계
더 많은 개발 팁은 [개발 개요][]를 참조하세요.

<!--Image references-->

<!--Article references-->
[개발 개요]: sql-data-warehouse-overview-develop.md

<!--MSDN references-->

<!--Other Web references-->

<!---HONumber=AcomDC_0309_2016-->