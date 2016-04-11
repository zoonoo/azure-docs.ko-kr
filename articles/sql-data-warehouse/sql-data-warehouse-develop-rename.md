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
   ms.date="03/23/2016"
   ms.author="mausher;jrj;barbkess;sonyama"/>

# SQL 데이터 웨어하우스의 이름 바꾸기
SQL Server는 저장된 프로시저 `sp_renamedb`를 통한 데이터베이스 이름 변경을 지원하지만 SQL 데이터 웨어하우스는 해당 작업에 DDL 구문을 사용합니다. DDL 명령은 `RENAME OBJECT`입니다.

## 테이블 이름 변경

현재 테이블만 이름을 바꿀 수 있습니다. 테이블 이름을 바꾸기 위한 구문은 다음과 같습니다.

```sql
RENAME OBJECT dbo.Customer TO NewCustomer;
```

테이블의 이름을 변경하면 해당 테이블과 연결된 모든 개체와 속성이 새 테이블 이름을 참조하도록 업데이트됩니다. 예를 들어, 테이블 정의, 인덱스, 제약 및 권한이 업데이트됩니다. 뷰는 업데이트되지 않습니다.

## 외부 테이블 이름 변경

외부 테이블을 이름을 변경하면 SQL 데이터 웨어하우스 안에 있는 테이블 이름이 변경됩니다. 테이블의 외부 데이터 위치에는 영향이 없습니다.

## 테이블 스키마 변경
개체가 속해 있는 스키마를 변경하는 것이 목적이라면 ALTER SCHEMA를 통해 이루어집니다.

```sql
ALTER SCHEMA dbo TRANSFER OBJECT::product.item;
```

## 테이블 이름 변경에는 배타적 테이블 잠금이 필요합니다.

테이블을 사용 중에는 테이블 이름을 변경할 수 없습니다. 테이블 이름 변경에는 테이블에 대한 배타적 잠금이 필요합니다. 테이블을 사용 중인 경우 테이블을 사용하는 세션을 종료해야 할 수 있습니다. 세션을 종료하려면 [KILL][] 명령을 사용해야 합니다. 세션이 종료되고 커밋되지 않은 모든 작업이 롤백되면 `KILL`을 사용할 때 주의합니다. SQL 데이터 웨어하우스의 세션에는 'SID'라는 접두어가 붙습니다. KILL 명령을 호출할 때 세션 번호와 이 부분을 포함해야 합니다. 예: `KILL 'SID1234'`. [세션]에 대한 자세한 내용은 연결 문서를 참조하세요.


## 다음 단계
더 많은 개발 팁은 [개발 개요][]를 참조하세요.

<!--Image references-->

<!--Article references-->
[개발 개요]: sql-data-warehouse-overview-develop.md
[세션]: sql-data-warehouse-develop-connections.md


<!--MSDN references-->
[KILL]: https://msdn.microsoft.com/library/ms173730.aspx

<!---HONumber=AcomDC_0330_2016-->