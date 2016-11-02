<properties
   pageTitle="트랜잭션 복제를 사용하여 SQL 데이터베이스로 마이그레이션 | Microsoft Azure"
   description="Microsoft Azure SQL 데이터베이스, 데이터베이스 마이그레이션, 데이터베이스 가져오기, 트랜잭션 복제"
   services="sql-database"
   documentationCenter=""
   authors="CarlRabeler"
   manager="jhubbard"
   editor=""/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="sqldb-migrate"
   ms.date="08/23/2016"
   ms.author="carlrab"/>

# 트랜잭션 복제를 사용하여 Azure SQL Server 데이터베이스를 SQL 데이터베이스로 마이그레이션

> [AZURE.SELECTOR]
- [SSMS 마이그레이션 마법사](sql-database-cloud-migrate-compatible-using-ssms-migration-wizard.md)
- [BACPAC 파일로 내보내기](sql-database-cloud-migrate-compatible-export-bacpac-ssms.md)
- [BACPAC 파일에서 가져오기](sql-database-cloud-migrate-compatible-import-bacpac-ssms.md)
- [트랜잭션 복제자](sql-database-cloud-migrate-compatible-using-transactional-replication.md)

이 문서에서는 SQL Server 트랜잭션 복제를 사용하여 가동 중지 시간을 최소화하면서 호환되는 SQL Server 데이터베이스를 Azure SQL 데이터베이스로 마이그레이션하는 방법을 배웁니다.

## 트랜잭션 복제 아키텍처 이해

마이그레이션하는 동안 SQL Server 데이터베이스의 운영을 중지할 수 없는 경우 마이그레이션 솔루션으로 SQL Server 트랜잭션 복제를 사용할 수 있습니다. 이 솔루션을 사용하려면 마이그레이션할 온-프레미스 SQL Server 인스턴스에서 Azure SQL 데이터베이스를 구독자로 구성합니다. 온-프레미스 트랜잭션 복제 배포자는 새 트랜잭션이 계속 발생하는 동안 온-프레미스 데이터베이스의 데이터를 동기화합니다(게시자).

또한 트랜잭션 복제를 사용하여 온-프레미스 데이터베이스의 하위 집합을 마이그레이션할 수 있습니다. 사용자가 Azure SQL 데이터베이스로 복제하는 게시물을 복제되는 데이터베이스의 테이블 하위 집합으로 제한할 수 있습니다. 복제되는 각 테이블에 대해 데이터를 행의 하위 집합 및/또는 열의 하위 집합으로 제한할 수 있습니다.

트랜잭션 복제를 사용하면 데이터 또는 스키마에 대한 모든 변경 내용이 Azure SQL 데이터베이스에 표시됩니다. 동기화가 완료되고 마이그레이션 준비가 끝나면 Azure SQL 데이터베이스를 가리키도록 응용 프로그램의 연결 문자열을 변경합니다. 온-프레미스 데이터베이스에 남아 있는 모든 변경 사항이 트랜잭션 복제를 통해 Azure DB로 이동되고 모든 응용 프로그램이 Azure DB를 가리키면 트랜잭션 복제를 제거할 수 있습니다. Azure SQL 데이터베이스는 현재 프로덕션 시스템입니다.

 ![SeedCloudTR 다이어그램](./media/sql-database-cloud-migrate/SeedCloudTR.png)

## 트랜잭션 복제 요구 사항

트랜잭션 복제는 SQL Server 6.5부터 SQL Server에 내장 및 통합된 기술입니다. 대부분의 DBA가 경험한 적이 있는 성숙하고 검증된 기술입니다. 이제 [SQL Server 2016](https://www.microsoft.com/ko-KR/cloud-platform/sql-server)을 사용하여 Azure SQL 데이터베이스를 온-프레미스 게시의 [트랜잭션 복제 구독자](https://msdn.microsoft.com/library/mt589530.aspx)로 구성할 수 있습니다. Management Studio에서 설정하는 방법은 온-프레미스 서버에서 트랜잭션 복제 구독자를 설정하는 방법과 일치합니다. 이 시나리오는 게시자 및 배포자가 다음 SQL Server 버전 중 하나일 때 지원됩니다.

 - SQL Server 2016 이상
 - SQL Server 2014 SP1 CU3 이상
 - SQL Server 2014 RTM CU10 이상
 - SQL Server 2012 SP2 CU8 이상
 - SQL Server 2012 SP3 이상


> [AZURE.IMPORTANT] Microsoft Azure 및 SQL 데이터베이스에 대한 업데이트와 동기화 상태를 유지하려면 항상 최신 버전의 SQL Server Management Studio를 사용합니다. 이전 버전의 SQL Server Management Studio는 SQL 데이터베이스를 구독자로 설정할 수 없습니다. [SQL Server Management Studio를 업데이트합니다](https://msdn.microsoft.com/library/mt238290.aspx).


## 다음 단계

- [SQL Server Management Studio 최신 버전](https://msdn.microsoft.com/library/mt238290.aspx)
- [SSDT 최신 버전](https://msdn.microsoft.com/library/mt204009.aspx)
- [SQL Server 2016](https://www.microsoft.com/ko-KR/cloud-platform/sql-server)

## 추가 리소스

- [트랜잭션 복제자](https://msdn.microsoft.com/library/mt589530.aspx)
- [SQL 데이터베이스 V12](sql-database-v12-whats-new.md)
- [Transact-SQL의 부분적으로 지원되거나 지원되지 않는 기능](sql-database-transact-sql-information.md)
- [SQL Server Migration Assistant를 사용하여 SQL Server 이외의 데이터베이스 마이그레이션](http://blogs.msdn.com/b/ssma/)

<!---HONumber=AcomDC_0824_2016-->