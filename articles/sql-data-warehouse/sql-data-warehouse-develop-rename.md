<properties
   pageTitle="SQL 데이터 웨어하우스의 이름 바꾸기 | Microsoft Azure"
   description="솔루션 개발을 위한 Azure SQL 데이터 웨어하우스의 테이블 이름 변경을 위한 팁"
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
   ms.date="05/28/2016"
   ms.author="mausher;jrj;barbkess;sonyama"/>

# SQL 데이터 웨어하우스의 이름 바꾸기
SQL 데이터 웨어하우스는 [RENAME][] 문을 사용하여 테이블 이름을 바꿉니다. 이 문은 `sp_rename`을 사용하는 SQL Server와 다릅니다. 현재 사용자 테이블 이름만 바꿀 수 있습니다. 데이터베이스 및 외부 테이블의 이름을 바꿀 수 없습니다.

## 테이블 이름 변경

테이블의 이름을 변경하면 해당 테이블과 연결된 모든 개체와 속성이 새 테이블 이름을 참조하도록 업데이트됩니다. 예를 들어, 테이블 정의, 인덱스, 제약 및 권한이 업데이트됩니다. 뷰는 업데이트되지 않습니다.

테이블 이름을 바꾸기 위한 구문은 다음과 같습니다.

```sql
RENAME OBJECT dbo.Customer TO NewCustomer;
```

## 테이블 스키마 변경

개체가 속해 있는 스키마를 변경하려면 ALTER SCHEMA를 사용합니다.

```sql
ALTER SCHEMA dbo TRANSFER OBJECT::product.item;
```

## 테이블 이름 변경에는 배타적 테이블 잠금이 필요합니다.

사용 중인 동안에는 테이블 이름을 바꿀 수 없습니다. 테이블 이름 변경에는 테이블에 대한 배타적 잠금이 필요합니다. 테이블을 사용 중인 경우 테이블을 사용하는 세션을 종료해야 할 수 있습니다. 세션을 종료하려면 [KILL][] 명령을 사용합니다. 예: `KILL 'SID1234'`. 세션이 종료될 때 커밋되지 않은 모든 트랜잭션 작업이 롤백되므로 KILL을 사용할 때는 주의해야 합니다. [세션 및 요청][]에 대한 자세한 내용은 연결 문서를 참조하세요. 트랜잭션 쿼리 종료가 미치는 영향 및 롤백의 효과에 대한 자세한 내용은 [SQL 데이터 웨어하우스에 대해 트랜잭션 최적화][]를 참조하세요.


## 다음 단계
추가 T-SQL 참조에 대해서는 [T-SQL 참조][]를 참조하세요.

<!--Image references-->

<!--Article references-->
[development overview]: ./sql-data-warehouse-overview-develop.md
[세션 및 요청]: ./sql-data-warehouse-develop-connections.md#sessions-and-requests
[T-SQL 참조]: ./sql-data-warehouse-reference-tsql-statements.md
[SQL 데이터 웨어하우스에 대해 트랜잭션 최적화]: ./sql-data-warehouse-develop-best-practices-transactions.md

<!--MSDN references-->
[KILL]: https://msdn.microsoft.com/library/ms173730.aspx
[RENAME]: https://msdn.microsoft.com/library/mt631611.aspx

<!---HONumber=AcomDC_0601_2016-->