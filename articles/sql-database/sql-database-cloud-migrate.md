<properties
   pageTitle="SQL 데이터베이스로 SQL Server 데이터베이스 마이그레이션 | Microsoft Azure"
   description="클라우드에서 Azure SQL 데이터베이스로 온-프레미스 SQL Server 데이터베이스를 마이그레이션하는 방법을 알아봅니다. 데이터베이스 마이그레이션 도구를 사용하여 데이터베이스 마이그레이션 전에 호환성을 테스트합니다."
   keywords="데이터베이스 마이그레이션, SQL Server 데이터베이스 마이그레이션, 데이터베이스 마이그레이션 도구, 데이터베이스 마이그레이션, SQL 데이터베이스 마이그레이션"
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

# 클라우드에서 SQL 데이터베이스로 SQL Server 데이터베이스 마이그레이션

이 문서에서는 온-프레미스 Azure SQL 2005 이상 데이터베이스를 Azure SQL 데이터베이스로 마이그레이션하는 방법에 대해 배웁니다. 이 데이터베이스 마이그레이션 프로세스에서는 기존 데이터베이스가 호환성 테스트를 통과할 경우 현재 환경의 SQL Server 데이터베이스에서 SQL 데이터베이스로 스키마 및 데이터를 마이그레이션합니다. [SQL 데이터베이스 V12](sql-database-v12-whats-new.md)를 사용하면 서버 수준 및 데이터베이스 간 작업 외에는 호환성 문제가 거의 없습니다. SQL Server 데이터베이스를 마이그레이션하려면 [부분적으로 지원되거나 지원되지 않는 기능](sql-database-transact-sql-information.md)에 의존하는 데이터베이스 및 응용 프로그램의 일부를 다시 엔지니어링하여 이러한 호환성 문제를 해결해야 합니다.

데이터베이스를 마이그레이션하려면 다음 단계를 수행해야 합니다.

- **호환성 테스트**: 먼저 [SQL 데이터베이스 V12](sql-database-v12-whats-new.md)를 사용하여 데이터베이스 호환성을 검사해야 합니다. 
- **호환성 문제가 있는 경우, 문제 해결**: 유효성 검사에 실패할 경우 유효성 검사 오류를 수정해야 합니다.  
- **마이그레이션 수행** 데이터베이스가 호환되면 한 가지 또는 여러 가지 방법을 사용하여 마이그레이션을 수행할 수 있습니다. 

SQL Server는 이러한 각 작업을 수행하는 여러 방법을 제공합니다. 이 문서에서는 각 작업에 사용할 수 있는 방법을 개략적으로 살펴봅니다. 다음 다이어그램은 작업 단계 및 방법을 보여줍니다.

  ![VSSSDT 마이그레이션 다이어그램](./media/sql-database-cloud-migrate/03VSSSDTDiagram.png)
  
 > [AZURE.NOTE] Microsoft Access, Sybase, MySQL Oracle, DB2를 비롯한 비-SQL Server 데이터베이스를 Azure SQL 데이터베이스로 마이그레이션해야 할 경우 [SSMA(SQL Server Migration Assistant)](http://blogs.msdn.com/b/ssma/)를 참조하세요.

## 데이터베이스 마이그레이션 도구의 SQL Server 데이터베이스와 SQL 데이터베이스와의 호환성 테스트

데이터베이스 마이그레이션 프로세스를 시작하기 전에 다음 방법 중 하나를 사용하여 SQL 데이터베이스 호환성 문제를 테스트합니다.

> [AZURE.SELECTOR]
- [SSDT](sql-database-cloud-migrate-fix-compatibility-issues-ssdt.md)
- [SqlPackage](sql-database-cloud-migrate-determine-compatibility-sqlpackage.md)
- [SSMS](sql-database-cloud-migrate-determine-compatibility-ssms.md)
- [업그레이드 관리자](http://www.microsoft.com/download/details.aspx?id=48119)
- [SAMW](sql-database-cloud-migrate-fix-compatibility-issues.md)

- [Visual Studio용 SQL Server 데이터 도구("SSDT")](sql-database-cloud-migrate-fix-compatibility-issues-ssdt.md): SSDT는 가장 최근의 호환성 규칙을 사용하여 SQL 데이터베이스 V12의 호환성 문제를 감지합니다. 호환성 문제가 발견되면 이 도구에서 바로 발견된 문제를 해결할 수 있습니다. 현재는 이 방법을 사용하여 SQL 데이터베이스 V12의 호환성 문제를 테스트 및 수정하는 것이 좋습니다. 
- [SqlPackage](sql-database-cloud-migrate-determine-compatibility-sqlpackage.md): SqlPackage는 테스트를 수행하여 호환성 문제가 발견되면 감지된 호환성 문제가 포함된 보고서를 생성하는 명령 프롬프트 유틸리티입니다. 이 도구를 사용하는 경우 가장 최근의 호환성 규칙이 사용되도록 최신 버전을 사용해야 합니다. 오류가 감지되면 다른 도구를 사용하여 발견된 호환성 문제를 해결해야 합니다. SSDT를 사용하는 것이 좋습니다.  
- [SQL Server Management Studio의 데이터 계층 응용 프로그램 내보내기 마법사](sql-database-cloud-migrate-determine-compatibility-ssms.md): 이 마법사는 오류를 감지하여 화면에 표시합니다. 오류가 발견되지 않으면 작업을 계속 진행하여 SQL 데이터베이스에 마이그레이션을 완료할 수 있습니다. 오류가 감지되면 다른 도구를 사용하여 발견된 호환성 문제를 해결해야 합니다. SSDT를 사용하는 것이 좋습니다.
- [Microsoft SQL Server 2016 업그레이드 관리자 미리 보기](http://www.microsoft.com/download/details.aspx?id=48119): 현재 미리 보기 버전으로 제공되는 이 독립 실행형 도구는 SQL 데이터베이스 V12의 호환성 문제를 감지하여 보고서를 작성합니다. 이 도구는 아직 최신 호환성 규칙을 사용하지 않습니다. 오류가 발견되지 않으면 작업을 계속 진행하여 SQL 데이터베이스에 마이그레이션을 완료할 수 있습니다. 오류가 감지되면 다른 도구를 사용하여 발견된 호환성 문제를 해결해야 합니다. SSDT를 사용하는 것이 좋습니다. 
- [SQL Azure 마이그레이션 마법사("SAMW")](sql-database-cloud-migrate-fix-compatibility-issues.md): SAMW는 Azure SQL 데이터베이스 V11 호환성 규칙을 사용하여 Azure SQL 데이터베이스 V12 호환성 문제를 감지하는 CodePlex 도구입니다. 호환성 문제가 발견될 경우 이 도구에서 바로 일부 문제를 해결할 수 있습니다. 이 도구는 수정할 필요가 없는 비호환성까지 감지할 가능성이 있습니다. 하지만 최초로 제공된 Azure SQL 데이터베이스 마이그레이션 지원 도구이며 SQL Server 커뮤니티에서 다양한 지원을 받을 수 있습니다. 또한 이 도구는 도구 자체 내에서 마이그레이션을 완료할 수 있습니다. 

## 데이터베이스 마이그레이션 호환성 문제 해결

호환성 문제가 감지되면 SQL Server 데이터베이스 마이그레이션을 계속 진행하기 전에 해결해야 합니다. 원본 데이터베이스의 SQL Server 버전 및 마이그레이션하려는 데이터베이스의 복잡성에 따라 매우 다양한 호환성 문제가 발견될 수 있습니다. 원본 데이터베이스의 SQL Server 버전이 오래된 것일수록 더 많은 호환성 문제가 발견됩니다. 사용자가 선택한 검색 엔진을 사용하는 대상이 지정된 인터넷 검색 외에도 다음 리소스를 사용해 보세요.

- [Azure SQL 데이터베이스에서 지원되지 않는 하는 SQL Server 데이터베이스 기능](sql-database-transact-sql-information.md)
- [SQL Server 2016에서 사용이 중단된 데이터베이스 엔진 기능](https://msdn.microsoft.com/library/ms144262%28v=sql.130%29)
- [SQL Server 2014에서 사용이 중단된 데이터베이스 엔진 기능](https://msdn.microsoft.com/library/ms144262%28v=sql.120%29)
- [SQL Server 2012에서 사용이 중단된 데이터베이스 엔진 기능](https://msdn.microsoft.com/library/ms144262%28v=sql.110%29)
- [SQL Server 2008 R2에서 사용이 중단된 데이터베이스 엔진 기능](https://msdn.microsoft.com/library/ms144262%28v=sql.105%29)
- [SQL Server 2005에서 사용이 중단된 데이터베이스 엔진 기능](https://msdn.microsoft.com/library/ms144262%28v=sql.90%29)

인터넷을 검색하고 이러한 리소스를 사용하는 방법 외에도 [MSDN SQL Server 커뮤니티 포럼](https://social.msdn.microsoft.com/Forums/sqlserver/home?category=sqlserver) 또는 [StackOverflow](http://stackoverflow.com/)에서 비호환성 문제를 적절하게 해결하는 또 다른 리소스를 찾을 수 있습니다.

다음 데이터베이스 마이그레이션 도구 중 하나를 사용하여 발견된 문제를 해결합니다.

> [AZURE.SELECTOR]
- [SSDT](sql-database-cloud-migrate-fix-compatibility-issues-ssdt.md)
- [SSMS](sql-database-cloud-migrate-fix-compatibility-issues-ssms.md)
- [SAMW](sql-database-cloud-migrate-fix-compatibility-issues.md)

- [Visual Studio용 SQL Server 데이터 도구("SSDT")](sql-database-cloud-migrate-fix-compatibility-issues-ssdt.md) 사용: SSDT를 사용하려면 Visual Studio용 SQL Server 데이터 도구("SSDT")로 데이터베이스 스키마를 가져오고, SQL 데이터베이스 V12 배포용 프로젝트를 빌드하고, 발견된 모든 호환성 문제를 SSDT에서 해결한 다음 변경 내용을 원본 데이터베이스(또는 원본 데이터베이스의 복사본)과 동기화합니다. 현재는 이 방법을 사용하여 SQL 데이터베이스 V12의 호환성 문제를 테스트 및 수정하는 것이 좋습니다. [SSDT 사용 연습](sql-database-cloud-migrate-fix-compatibility-issues-ssdt.md) 링크를 따라가세요.
- ["SSMS"(SQL Server Management Studio)](sql-database-cloud-migrate-fix-compatibility-issues-ssms.md) 사용: SSMS를 사용하려면 Transact-SQL 명령을 실행하여 또 다른 도구로 발견된 오류를 해결합니다. 이 방법은 원본 데이터베이스에서 직접 데이터베이스 스키마를 수정할 수 있는 고급 사용자가 주로 사용합니다. 
- [SQL Azure 마이그레이션 마법사("SAMW")](sql-database-cloud-migrate-fix-compatibility-issues.md) 사용: SAMW를 사용하려면 원본 데이터베이스에서 Transact-SQL 스크립트를 생성합니다. 그러면 마법사가 가능한 때에 스키마와 SQL 데이터베이스 V12가 호환되도록 원본 데이터베이스를 변환합니다. 변환이 완료되면 SAMW가 SQL 데이터베이스 V12에 연결하여 스크립트를 실행합니다. 이 도구는 또한 추적 파일을 분석하여 호환성 문제를 확인합니다. 스크립트는 스키마만 사용하여 생성하거나 BCP 형식의 데이터를 포함할 수 있습니다.

## 호환되는 SQL Server 데이터베이스를 SQL 데이터베이스로 마이그레이션

호환되는 SQL Server 데이터베이스를 마이그레이션할 수 있도록 Microsoft에서는 다양한 시나리오에 대한 여러 마이그레이션 방법을 제공합니다. 허용되는 가동 중지 시간, SQL Server 데이터베이스의 크기 및 복잡성, Microsoft Azure 클라우드 연결 여부에 따라 선택하는 마이그레이션 방법이 달라집니다.

> [AZURE.SELECTOR]
- [SSMS 마이그레이션 마법사](sql-database-cloud-migrate-compatible-using-ssms-migration-wizard.md)
- [BACPAC 파일로 내보내기](sql-database-cloud-migrate-compatible-export-bacpac-ssms.md)
- [BACPAC 파일에서 가져오기](sql-database-cloud-migrate-compatible-import-bacpac-ssms.md)
- [트랜잭션 복제자](sql-database-cloud-migrate-compatible-using-transactional-replication.md)

마이그레이션 방법을 선택하기 위한 첫 번째 질문은 마이그레이션하는 동안 데이터베이스를 정지할 수 있는지 여부입니다. 활성 트랜잭션이 발생하는 동안 데이터베이스를 마이그레이션하면 데이터베이스가 불일치하고 데이터베이스가 손상될 수 있습니다. 클라이언트 연결을 비활성화하는 방법부터 [데이터베이스 스냅숏](https://msdn.microsoft.com/library/ms175876.aspx)을 만드는 방법까지 다양한 방법으로 데이터베이스를 정지할 수 있습니다.

가동 중지 시간을 최소로 줄이고 마이그레이션하려면 데이터베이스가 트랜잭션 복제에 대한 요구 사항을 충족하는 경우 [SQL Server 트랜잭션 복제](sql-database-cloud-migrate-compatible-using-transactional-replication.md)를 사용하세요. 약간의 가동 중지 시간이 허용되거나 나중에 마이그레이션할 수 있도록 프로덕션 데이터베이스의 테스트 마이그레이션을 수행하려면 다음 세가지 방법 중 하나를 고려해 보세요.

- [SSMS 마이그레이션 마법사](sql-database-cloud-migrate-compatible-using-ssms-migration-wizard.md): 중소규모 데이터베이스의 경우 호환되는 SQL Server 2005 이상 데이터베이스를 마이그레이션하려면 SQL Server Management Studio에서 [Microsoft Azure 데이터베이스에 배포 마법사](sql-database-cloud-migrate-compatible-using-ssms-migration-wizard.md)를 실행하기만 하면 됩니다.
- [BACPAC 파일로 내보낸](sql-database-cloud-migrate-compatible-export-bacpac-ssms.md) 다음 [BACPAC 파일에서 가져오기](sql-database-cloud-migrate-compatible-import-bacpac-ssms.md): 연결 문제(없음 연결, 낮은 대역폭 또는 시간 초과 문제)가 있고 데이터베이스 규모가 중간 이상인 경우 [BACPAC](https://msdn.microsoft.com/library/ee210546.aspx#Anchor_4) 파일을 사용합니다. 이 점을 염두에 두고 SQL Server 스키마 및 데이터를 BACPAC 파일로 내보낸 다음 SQL Server Management Studio의 데이터 계층 응용 프로그램 내보내기 마법사 또는 [SqlPackage](https://msdn.microsoft.com/library/hh550080.aspx) 명령 프롬프트 유틸리티를 사용하여 SQL 데이터베이스로 BACPAC 파일을 가져옵니다.
- BACPAC과 BCP를 함께 사용: 데이터베이스가 매우 큰 경우에는 [BACPAC](https://msdn.microsoft.com/library/ee210546.aspx#Anchor_4) 파일과 [BCP](https://msdn.microsoft.com/library/ms162802.aspx)를 함께 사용하여 병렬화하면 복잡성이 커지기는 하지만 성능이 향상됩니다. 이 점을 염두에 두고 스키마와 데이터를 따로 마이그레이션합니다.
 - [BACPAC 파일에만 스키마를 내보냅니다](sql-database-cloud-migrate-compatible-export-bacpac-ssms.md).
 - [BACPAC 파일의 스키마만 SQL 데이터베이스로 가져옵니다](sql-database-cloud-migrate-compatible-import-bacpac-ssms.md).
 - [BCP](https://msdn.microsoft.com/library/ms162802.aspx)를 사용하여 데이터를 플랫 파일로 추출한 다음 이러한 파일을 Azure SQL 데이터베이스로 [병렬 로드](https://technet.microsoft.com/library/dd425070.aspx)합니다.

	 ![SQL Server 데이터베이스 마이그레이션 - 클라우드로 SQL 데이터베이스 마이그레이션.](./media/sql-database-cloud-migrate/01SSMSDiagram_new.png)

## 다음 단계

- [Microsoft SQL Server 2016 업그레이드 관리자 미리 보기](http://www.microsoft.com/download/details.aspx?id=48119)
- [SSDT 최신 버전](https://msdn.microsoft.com/library/mt204009.aspx)
- [SQL Server Management Studio 최신 버전](https://msdn.microsoft.com/library/mt238290.aspx)

##추가 리소스

- [SQL 데이터베이스 V12](sql-database-v12-whats-new.md) [Transact-SQL의 부분적으로 지원되거나 지원되지 않는 기능](sql-database-transact-sql-information.md)
- [SQL Server Migration Assistant를 사용하여 SQL Server 이외의 데이터베이스 마이그레이션](http://blogs.msdn.com/b/ssma/)

<!---HONumber=AcomDC_0615_2016-->