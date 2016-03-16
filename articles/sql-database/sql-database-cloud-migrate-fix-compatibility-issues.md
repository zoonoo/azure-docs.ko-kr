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

## SQL Azure 마이그레이션 마법사 사용

[SQL Azure 마이그레이션 마법사](http://sqlazuremw.codeplex.com/) CodePlex 도구를 사용하여 호환되지 않는 원본 데이터베이스에서 T-SQL 스크립트를 생성하면 마법사가 이 스크립트를 SQL 데이터베이스와 호환되도록 변환합니다. 그런 다음 Azure 데이터베이스에 연결하여 스크립트를 실행합니다. 이 도구는 또한 추적 파일을 분석하여 호환성 문제를 확인합니다. 스크립트는 스키마만 사용하여 생성하거나 BCP 형식의 데이터를 포함할 수 있습니다. 단계별 참고 자료를 포함한 추가 설명서는 [SQL Azure 마이그레이션 마법사](http://sqlazuremw.codeplex.com/)의 CodePlex에서 사용할 수 있습니다.

 ![SAMW 마이그레이션 다이어그램](./media/sql-database-cloud-migrate/02SAMWDiagram.png)

  >[AZURE.NOTE]마법사에서 감지할 수 있는 모든 호환되지 않는 스키마를 기본 제공 변환으로 해결할 수 있는 것은 아닙니다. 처리할 수 없는 호환되지 않는 스크립트는 오류로 보고되고 생성된 스크립트에 주석이 삽입됩니다. 많은 오류가 감지될 경우 Visual Studio 또는 SQL Server Management Studio를 사용하여 SQL Server 마이그레이션 마법사로 해결할 수 없는 각 오류를 단계적으로 해결합니다.

## 다음 단계: 마이그레이션 방법을 선택하고 마이그레이션 수행

[마이그레이션 방법을 선택합니다](sql-database-cloud-migrate.md#migrate-a-compatible-sql-server-database-to-sql-database).

<!---HONumber=AcomDC_1223_2015-->