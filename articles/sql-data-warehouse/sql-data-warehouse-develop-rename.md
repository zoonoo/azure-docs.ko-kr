<properties
   pageTitle="SQL 데이터 웨어하우스의 이름 바꾸기 | Microsoft Azure"
   description="솔루션 개발을 위한 Azure SQL 데이터 웨어하우스의 개체 및 데이터베이스 이름 바꾸기를 위한 팁."
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

# SQL 데이터 웨어하우스의 이름 바꾸기
SQL Server는 각각 sp_renamedb 및 sp_rename 저장된 프로시저를 통해 개체 및 데이터베이스 이름 바꾸기를 지원합니다.

SQL 데이터 웨어하우스는 DDL 구문을 사용하여 같은 목표를 달성합니다. DDL 명령은 RENAME OBJECT 및 RENAME DATABASE입니다.

## 개체 이름 바꾸기

이름은 이름이 변경된 개체에만 영향을 미치며 이름 변경의 전파는 없음을 이해하는 것이 중요합니다. 따라서 개체의 이전 이름을 사용하는 모든 뷰는 이전 이름을 가진 개체가 만들어질 때까지 유효하지 않습니다. 따라서 쌍으로 `RENAME OBJECT`가 자주 표시됩니다.

```
RENAME OBJECT product.item to item_old;
RENAME OBJECT product.item_new to item;
```

## 데이터베이스 이름 바꾸기

데이터베이스 이름 바꾸기는 개체 이름 바꾸기와 매우 유사합니다.

```
RENAME DATABASE AdventureWorks TO Contoso;
```

다른 사용자가 개체나 데이터베이스에 연결되어 있거나 사용 중인 경우 개체나 데이터베이스의 이름을 바꿀 수 없다는 점을 기억해야 합니다. 먼저 열려있는 세션을 종료해야 합니다. 세션을 종료하려면 [KILL][] 명령을 사용해야 합니다. KILL을 사용하는 경우 주의하세요. 실행되면 대상 세션이 종료되고 커밋되지 않은 모든 작업이 롤백됩니다.

> [AZURE.NOTE]SQL 데이터 웨어하우스에서 세션의 접두사는 'SID'입니다. KILL 명령을 호출할 때 세션 번호와 이 부분을 포함해야 합니다. 예를 들어, 실행할 올바른 권한이 있다는 가정 하에 KILL 'SID1234'는 1234 세션을 중지합니다.

## 세션 중지
데이터베이스의 이름을 바꾸려면, SQL 데이터 웨어하우스에 연결된 세션을 중지해야 합니다. 다음 쿼리는 KILL 명령의 고유 목록을 생성하여 연결(현재 세션에 대한 저장)을 지웁니다.

```
SELECT 'KILL '''+session_id+''''
FROM	sys.dm_pdw_exec_requests r
WHERE r.session_id <> SESSION_ID()
AND EXISTS
(	SELECT 	*
	FROM 	sys.dm_pdw_lock_waits w
	WHERE 	r.session_id = w.session_id
)
GROUP BY session_id
;
```

## 스키마 전환
개체가 속해 있는 스키마를 변경하는 것이 목적이라면 `ALTER SCHEMA`를 통해 이루어집니다.

```
ALTER SCHEMA dbo TRANSFER OBJECT::product.item;
```


## 다음 단계
더 많은 개발 팁은 [개발 개요][]를 참조하세요.

<!--Image references-->

<!--Article references-->
[개발 개요]: sql-data-warehouse-overview-develop.md

<!--MSDN references-->
[KILL]: https://msdn.microsoft.com/ko-kr/library/ms173730.aspx

<!--Other Web references-->
[Azure management portal]: http://portal.azure.com/

<!---HONumber=July15_HO3-->