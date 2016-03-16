<properties
   pageTitle="문제 해결 | Microsoft Azure"
   description="SQL 데이터 웨어하우스 문제를 해결합니다."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="TwoUnder"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="01/07/2016"
   ms.author="mausher;sonyama;barbkess"/>

# 문제 해결
다음 항목에서는 Azure SQL 데이터 웨어하우스를 사용하는 고객에게 발생할 수 있는 일반적인 문제 중 일부를 설명합니다.

## 연결
다음과 같은 몇 가지 일반적인 이유로 인해 Azure SQL 데이터 웨어하우스 연결에 실패할 수 있습니다.

- 방화벽 규칙이 설정되지 않음
- 지원되지 않는 도구/프로토콜 사용

### 방화벽 규칙
Azure SQL 데이터베이스가 알려진 IP 주소만 데이터베이스에 액세스할 수 있도록 서버 및 데이터베이스 수준 방화벽으로 보호됩니다. 방화벽은 연결하기 전에 IP 주소 액세스를 허용해야 하기 때문에 기본적으로 안전합니다.

액세스를 위해 방화벽을 구성하려면 [프로비전](sql-data-warehouse-get-started-provision.md) 페이지의 [클라이언트 IP에 대한 서버 방화벽 액세스 구성](sql-data-warehouse-get-started-provision.md/#step-4-configure-server-firewall-access-for-your-client-ip) 섹션의 단계를 따르세요.

### 지원되지 않는 도구/프로토콜 사용
SQL 데이터 웨어하우스는 [Visual Studio 2013/2015](sql-data-warehouse-get-started-connect.md)를 개발자 환경으로 지원하며 클라이언트 연결의 경우 [SQL Server Native Client 10/11(ODBC)](https://msdn.microsoft.com/library/ms131415.aspx)을 지원합니다.

자세한 내용은 [연결](sql-data-warehouse-get-started-connect.md) 페이지를 참조하세요.

## 쿼리 성능
SQL 데이터 웨어하우스는 통계를 포함하여 쿼리를 실행하기 위한 일반적인 SQL Server 구문을 사용합니다. [통계](sql-data-warehouse-develop-statistics.md)는 데이터베이스 열에 있는 값의 범위 및 빈도에 대한 정보를 포함하는 개체입니다. 쿼리 엔진은 이러한 통계를 사용하여 쿼리 실행을 최적화하고 쿼리 성능을 개선합니다. 다음 쿼리를 사용하여 마지막으로 통계가 업데이트된 위치를 확인할 수 있습니다.

```
SELECT
	sm.[name]								    AS [schema_name],
	tb.[name]								    AS [table_name],
	co.[name]									AS [stats_column_name],
	st.[name]									AS [stats_name],
	STATS_DATE(st.[object_id],st.[stats_id])	AS [stats_last_updated_date]
FROM
	sys.objects				AS ob
	JOIN sys.stats			AS st	ON	ob.[object_id]		= st.[object_id]
	JOIN sys.stats_columns	AS sc	ON	st.[stats_id]		= sc.[stats_id]
									AND	st.[object_id]		= sc.[object_id]
	JOIN sys.columns		AS co	ON	sc.[column_id]		= co.[column_id]
									AND	sc.[object_id]		= co.[object_id]
	JOIN sys.types           AS ty	ON	co.[user_type_id]	= ty.[user_type_id]
	JOIN sys.tables          AS tb	ON	co.[object_id]		= tb.[object_id]
	JOIN sys.schemas         AS sm	ON	tb.[schema_id]		= sm.[schema_id]
WHERE
	1=1 
	AND st.[user_created] = 1;
```

자세한 내용은 [통계](sql-data-warehouse-develop-statistics.md) 페이지를 참조하세요.

## 주요 성능 개념

일부 추가적인 주요 성능 및 확장성 개념을 이해하는 데 도움이 필요하다면 다음 문서들을 참조하십시오.

- [성능 및 확장][]
- [동시성 모델][]
- [테이블 디자인][]
- [테이블에 대한 해시 배포 키를 선택합니다.][]
- [성능 향상을 위해 통계][]

## 다음 단계
[개발 개요][] 문서에서 SQL 데이터 웨어하우스 솔루션 구축에 관한 지침을 참조할 수 있습니다.

<!--Image references-->

<!--Article references-->

[성능 및 확장]: sql-data-warehouse-performance-scale.md
[동시성 모델]: sql-data-warehouse-develop-concurrency.md
[테이블 디자인]: sql-data-warehouse-develop-table-design.md
[테이블에 대한 해시 배포 키를 선택합니다.]: sql-data-warehouse-develop-hash-distribution-key
[성능 향상을 위해 통계]: sql-data-warehouse-develop-statistics.md
[개발 개요]: sql-data-warehouse-overview-develop.md

<!--MSDN references-->

<!--Other web references-->

<!---HONumber=AcomDC_0114_2016-->