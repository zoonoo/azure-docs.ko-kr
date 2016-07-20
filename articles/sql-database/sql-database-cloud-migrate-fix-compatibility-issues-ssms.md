<properties
   pageTitle="SQL 데이터베이스로 마이그레이션하기 전에 SQL Server Management Studio를 사용하여 SQL Server 데이터베이스 호환성 문제 해결 | Microsoft Azure"
   description="Microsoft Azure SQL 데이터베이스, 데이터베이스 마이그레이션, 호환성, SQL Azure 마이그레이션 마법사"
   services="sql-database"
   documentationCenter=""
   authors="carlrabeler"
   manager="jhubbard"
   editor=""/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="sqldb-migrate"
   ms.date="06/07/2016"
   ms.author="carlrab"/>

# SQL 데이터베이스로 마이그레이션하기 전에 SQL Server Management Studio를 사용하여 SQL Server 데이터베이스 호환성 문제 해결

> [AZURE.SELECTOR]
- [SQL Azure 마이그레이션 마법사](sql-database-cloud-migrate-fix-compatibility-issues.md) 사용
- [SSDT](sql-database-cloud-migrate-fix-compatibility-issues-ssdt.md) 사용
- [SSMS](sql-database-cloud-migrate-fix-compatibility-issues-ssms.md) 사용

고급 사용자는 Azure SQL 데이터베이스로 마이그레이션하기 전에 SQL Server Management Studio를 사용하여 SQL Server 데이터베이스 호환성 문제를 해결할 수 있습니다.


> [AZURE.IMPORTANT] Microsoft Azure 및 SQL 데이터베이스에 대한 업데이트와 동기화 상태를 유지하려면 항상 최신 버전의 Management Studio를 사용하는 것이 좋습니다. [SQL Server Management Studio를 업데이트합니다](https://msdn.microsoft.com/library/mt238290.aspx).


## SQL Server Management Studio 사용

SQL Server Management Studio를 사용하여 **ALTER DATABASE** 등 다양한 Transact-SQL 명령을 사용할 때 발생하는 호환성 문제를 해결합니다. 이 방법은 주로 라이브 데이터베이스에서 Transact-SQL을 사용하는 데 익숙한 고급 사용자를 위한 방법입니다. 그렇지 않은 경우 SSDT를 사용하는 것이 좋습니다.



## 다음 단계

- [SSDT 최신 버전](https://msdn.microsoft.com/library/mt204009.aspx)
- [SQL Server Management Studio 최신 버전](https://msdn.microsoft.com/library/mt238290.aspx)
- [호환되는 SQL Server 데이터베이스를 SQL 데이터베이스로 마이그레이션](sql-database-cloud-migrate.md#migrate-a-compatible-sql-server-database-to-sql-database)

## 추가 리소스

- [SQL 데이터베이스 V12](sql-database-v12-whats-new.md)
- [Transact-SQL의 부분적으로 지원되거나 지원되지 않는 기능](sql-database-transact-sql-information.md)
- [SQL Server Migration Assistant를 사용하여 SQL Server 이외의 데이터베이스 마이그레이션](http://blogs.msdn.com/b/ssma/)

<!---HONumber=AcomDC_0706_2016-->