<properties
   pageTitle="SQL Server 데이터베이스를 Azure SQL 데이터베이스로 마이그레이션"
   description="Microsoft Azure SQL 데이터베이스, 데이터베이스 배포, 데이터베이스 마이그레이션, 데이터베이스 가져오기, 데이터베이스 내보내기, 마이그레이션 마법사"
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
   ms.date="01/05/2016"
   ms.author="carlrab"/>

# SQL Server 데이터베이스를 Azure SQL 데이터베이스로 마이그레이션

이 문서에서는 온-프레미스 Azure SQL 2005 이상 데이터베이스를 Azure SQL 데이터베이스로 마이그레이션하는 방법에 대해 배웁니다. 이 마이그레이션 프로세스에서는 기존 데이터베이스가 호환성 테스트를 통과할 경우 현재 환경의 SQL Server 데이터베이스에서 SQL 데이터베이스로 스키마 및 데이터를 마이그레이션합니다. [SQL 데이터베이스 V12](sql-database-v12-whats-new.md)를 사용하면 서버 수준 및 데이터베이스 간 작업 외에는 호환성 문제가 거의 없습니다. SQL Server 데이터베이스를 마이그레이션하려면 [부분적으로 지원되거나 지원되지 않는 기능](sql-database-transact-sql-information.md)에 의존하는 데이터베이스 및 응용 프로그램의 일부를 다시 엔지니어링하여 [이러한 호환성 문제를 해결](sql-database-cloud-migrate-fix-compatibility-issues.md)해야 합니다.

> [AZURE.NOTE] Microsoft Access, Sybase, MySQL Oracle, DB2를 비롯한 비-SQL Server 데이터베이스를 Azure SQL 데이터베이스로 마이그레이션해야 할 경우 [SSMA(SQL Server Migration Assistant)](http://blogs.msdn.com/b/ssma/)를 참조하세요.

## SQL Server 데이터베이스가 SQL 데이터베이스로 마이그레이션하도록 호환되는지 확인

> [AZURE.SELECTOR]
- [SqlPackage](sql-database-cloud-migrate-determine-compatibility-sqlpackage.md)
- [SQL Server Management Studio](sql-database-cloud-migrate-determine-compatibility-ssms.md)

마이그레이션 프로세스를 시작하기 전에 다음 방법 중 하나를 사용하여 SQL 데이터베이스 호환성 문제를 테스트합니다.

- [SqlPackage 사용](sql-database-cloud-migrate-determine-compatibility-sqlpackage.md): SqlPackage는 테스트를 수행하여 호환성 문제가 발견되면 감지된 호환성 문제가 포함된 보고서를 생성하는 명령 프롬프트 유틸리티입니다.
- [SQL Server Management Studio 사용](sql-database-cloud-migrate-determine-compatibility-ssms.md): SQL Server Management Studio의 데이터 계층 응용 프로그램 내보내기 마법사는 감지된 오류를 화면에 표시합니다.

## 호환성 문제 해결

호환성 문제가 감지되면 마이그레이션을 계속 진행하기 전에 해당 호환성 문제를 해결해야 합니다.

- [SQL Azure 마이그레이션 마법사](sql-database-cloud-migrate-fix-compatibility-issues.md) 사용
- [Visual Studio용 SQL Server 데이터 도구](sql-database-cloud-migrate-fix-compatibility-issues-ssdt.md)
- [SQL Server Management Studio](sql-database-cloud-migrate-fix-compatibility-issues-ssms.md) 사용

## 호환되는 SQL Server 데이터베이스를 SQL 데이터베이스로 마이그레이션

호환되는 SQL Server 데이터베이스를 마이그레이션할 수 있도록 Microsoft에서는 다양한 시나리오에 대한 여러 마이그레이션 방법을 제공합니다. 허용되는 가동 중지 시간, SQL Server 데이터베이스의 크기 및 복잡성, Microsoft Azure 클라우드 연결 여부에 따라 선택하는 마이그레이션 방법이 달라집니다.

> [AZURE.SELECTOR]
- [SSMS Migration Wizard](sql-database-cloud-migrate-compatible-using-ssms-migration-wizard.md)
- [Export to BACPAC File](sql-database-cloud-migrate-compatible-export-bacpac-ssms.md)
- [Import from BACPAC File](sql-database-cloud-migrate-compatible-import-bacpac-ssms.md)
- [Transactional Replication](sql-database-cloud-migrate-compatible-using-transactional-replication.md)

마이그레이션 방법을 선택하기 위한 첫 번째 질문은 마이그레이션하는 동안 데이터베이스를 정지할 수 있는지 여부입니다. 활성 트랜잭션이 발생하는 동안 데이터베이스를 마이그레이션하면 데이터베이스가 불일치하고 데이터베이스가 손상될 수 있습니다. 클라이언트 연결을 비활성화하는 방법부터 [데이터베이스 스냅숏](https://msdn.microsoft.com/library/ms175876.aspx)을 만드는 방법까지 다양한 방법으로 데이터베이스를 정지할 수 있습니다.

가동 중지 시간을 최소로 줄이고 마이그레이션하려면 데이터베이스가 트랜잭션 복제에 대한 요구 사항을 충족하는 경우 [SQL Server 트랜잭션 복제](sql-database-cloud-migrate-compatible-using-transactional-replication.md)를 사용하세요. 약간의 가동 중지 시간이 허용되거나 나중에 마이그레이션할 수 있도록 프로덕션 데이터베이스의 테스트 마이그레이션을 수행하려면 다음 세가지 방법 중 하나를 고려해 보세요.

- [SSMS 마이그레이션 마법사](sql-database-cloud-migrate-compatible-using-ssms-migration-wizard.md): 중소규모 데이터베이스의 경우 호환되는 SQL Server 2005 이상 데이터베이스를 마이그레이션하려면 SQL Server Management Studio에서 [Microsoft Azure 데이터베이스에 배포 마법사](sql-database-cloud-migrate-compatible-using-ssms-migration-wizard.md)를 실행하기만 하면 됩니다. 
- [BACPAC 파일로 내보낸](sql-database-cloud-migrate-compatible-export-bacpac-ssms.md) 다음 [BACPAC 파일에서 가져오기](sql-database-cloud-migrate-compatible-import-bacpac-ssms.md): 연결 문제(없음 연결, 낮은 대역폭 또는 시간 초과 문제)가 있고 데이터베이스 규모가 중간 이상인 경우 [BACPAC](https://msdn.microsoft.com/library/ee210546.aspx#Anchor_4) 파일을 사용합니다. 이 점을 염두에 두고 SQL Server 스키마 및 데이터를 BACPAC 파일로 내보낸 다음 SQL Server Management Studio의 데이터 계층 응용 프로그램 내보내기 마법사 또는 [SqlPackage](https://msdn.microsoft.com/library/hh550080.aspx) 명령 프롬프트 유틸리티를 사용하여 SQL 데이터베이스로 BACPAC 파일을 가져옵니다.
- BACPAC과 BCP를 함께 사용: 데이터베이스가 매우 큰 경우에는 [BACPAC](https://msdn.microsoft.com/library/ee210546.aspx#Anchor_4) 파일과 [BCP](https://msdn.microsoft.com/library/ms162802.aspx)를 함께 사용하여 병렬화하면 복잡성이 커지기는 하지만 성능이 향상됩니다. 이 점을 염두에 두고 스키마와 데이터를 따로 마이그레이션합니다. 
 - [BACPAC 파일에만 스키마를 내보냅니다](sql-database-cloud-migrate-compatible-export-bacpac-ssms.md).
 - [BACPAC 파일의 스키마만 SQL 데이터베이스로 가져옵니다](sql-database-cloud-migrate-compatible-import-bacpac-ssms.md). 
 - [BCP](https://msdn.microsoft.com/library/ms162802.aspx)를 사용하여 데이터를 플랫 파일로 추출한 다음 이러한 파일을 Azure SQL 데이터베이스로 [병렬 로드](https://technet.microsoft.com/library/dd425070.aspx)합니다.

	 ![SSMS 마이그레이션 다이어그램](./media/sql-database-cloud-migrate/01SSMSDiagram_new.png)

<!---HONumber=AcomDC_0107_2016-->