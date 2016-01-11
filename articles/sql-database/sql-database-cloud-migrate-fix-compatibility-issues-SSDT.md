<properties
   pageTitle="SQL 데이터베이스로 마이그레이션하기 전에 SQL Server 데이터베이스 호환성 문제 해결"
   description="Microsoft Azure SQL 데이터베이스, 데이터베이스 마이그레이션, 호환성, SQL Azure 마이그레이션 마법사, SSDT"
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
- Use [SSMS](sql-database-cloud-migrate-fix-compatibility-issues-SSMS.md)

## Visual Studio용 SQL Server Data Tools 사용

Visual Studio용 SSDT("SQL Server Data Tools")를 사용하여 데이터베이스 스키마를 Visual Studio 데이터베이스 프로젝트로 가져온 다음 분석할 수 있습니다. 분석하려면 대한 대상 플랫폼을 SQL 데이터베이스 V12로 프로젝트로 지정하고 프로젝트를 빌드합니다. 빌드에 성공한 경우 데이터베이스가 호환됩니다. 빌드가 실패한 경우 SSDT(또는 이 항목에서 설명한 다른 도구 중 하나)에서 오류를 해결할 수 있습니다. 프로젝트가 성공적으로 빌드되면 원본 데이터베이스의 복사본으로 다시 게시할 수 있으며 다음 SSDT에서 데이터 비교 기능을 사용하여 원본 데이터베이스에서 Azure SQL V12 호환 가능한 데이터베이스로 데이터를 복사할 수 있습니다. 그런 다음 이 업데이트된 데이터베이스를 마이그레이션할 수 있습니다. 이 옵션을 사용하려면 [최신 버전의 SSDT](https://msdn.microsoft.com/library/mt204009.aspx)를 다운로드합니다.

  ![VSSSDT 마이그레이션 다이어그램](./media/sql-database-cloud-migrate/03VSSSDTDiagram.png)

  >[AZURE.NOTE]스키마만 마이그레이션해야 할 경우 Visual Studio에서 Azure SQL 데이터베이스로 직접 스키마를 게시할 수 있습니다. 데이터베이스 스키마가 마이그레이션 마법사 하나로 처리될 수 있는 것보다 많은 변경 내용이 필요한 경우 이 메서드를 사용합니다.

## 다음 단계: 마이그레이션 방법을 선택하고 마이그레이션 수행

[마이그레이션 방법을 선택합니다](sql-database-cloud-migrate.md#migrate-a-compatible-sql-server-database-to-sql-database).

<!---HONumber=AcomDC_1223_2015-->