<properties
   pageTitle="SQL 데이터베이스로 마이그레이션하기 전에 SQL Server 데이터베이스 호환성 문제 해결"
   description="Microsoft Azure SQL 데이터베이스, 데이터베이스 마이그레이션, 호환성, SQL Azure 마이그레이션 마법사"
   services="sql-database"
   documentationCenter=""
   authors="carlrabeler"
   manager="jeffreyg"
   editor=""/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-management"
   ms.date="12/17/2015"
   ms.author="carlrab"/>

# SQL 데이터베이스로 마이그레이션하기 전에 SQL Server 데이터베이스 호환성 문제 해결

원본 SQL Server 데이터베이스가 호환되지 않는 것으로 확인될 경우 다양한 옵션을 사용하여 식별한 데이터베이스 호환성 문제를 해결할 수 있습니다.

> [AZURE.SELECTOR]
- Use [SQL Azure Migration Wizard](sql-database-cloud-migrate-fix-compatibility-issues.md)
- Use [SSDT](sql-database-cloud-migrate-fix-compatibility-issues-ssdt.md)
- Use [SSMS](sql-database-cloud-migrate-fix-compatibility-issues-ssms.md)

## SQL Server Management Studio 사용

SQL Server Management Studio를 사용하여 **ALTER DATABASE** 등 다양한 Transact-SQL 명령을 사용할 때 발생하는 호환성 문제를 해결합니다.

## 다음 단계: 마이그레이션 방법을 선택하고 마이그레이션 수행

[마이그레이션 방법을 선택합니다](sql-database-cloud-migrate.md#migrate-a-compatible-sql-server-database-to-sql-database).

<!---HONumber=AcomDC_1223_2015-->