<properties
   pageTitle="Azure SQL 데이터베이스로 마이그레이션하기 전에 SQL Server Management Studio를 사용하여 SQL 데이터베이스 호환성 확인 | Microsoft Azure"
   description="Microsoft Azure SQL 데이터베이스, 데이터베이스 마이그레이션, SQL 데이터베이스 호환성, 데이터 계층 응용 프로그램 내보내기 마법사"
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
   ms.date="08/29/2016"
   ms.author="carlrab"/>

# Azure SQL 데이터베이스로 마이그레이션하기 전에 SQL Server Management Studio를 사용하여 SQL 데이터베이스 호환성 확인

> [AZURE.SELECTOR]
- [SSDT](sql-database-cloud-migrate-fix-compatibility-issues-ssdt.md)
- [SqlPackage](sql-database-cloud-migrate-determine-compatibility-sqlpackage.md)
- [SSMS](sql-database-cloud-migrate-determine-compatibility-ssms.md)
- [업그레이드 관리자](http://www.microsoft.com/download/details.aspx?id=48119)
- [SAMW](sql-database-cloud-migrate-fix-compatibility-issues.md)
 
이 문서에서는 SQL Server Management Studio의 데이터 계층 응용 프로그램 내보내기 마법사를 사용하여 SQL Server 데이터베이스가 SQL 데이터베이스로 마이그레이션하는 데 호환되는지 확인하는 방법을 배웁니다.

## SQL Server Management Studio 사용

1. 최신 버전의 SQL Server Management Studio가 있는지 확인합니다. 새로운 버전의 Management Studio는 매월 업데이트되어 Azure 포털의 업데이트와 동기화 상태를 유지합니다.

 	 > [AZURE.IMPORTANT] Microsoft Azure 및 SQL 데이터베이스에 대한 업데이트와 동기화 상태를 유지하려면 항상 최신 버전의 Management Studio를 사용하는 것이 좋습니다. [SQL Server Management Studio를 업데이트합니다](https://msdn.microsoft.com/library/mt238290.aspx).

2. Management Studio를 열고 개체 탐색기에서 원본 데이터베이스에 연결합니다.
3. 개체 탐색기에서 원본 데이터베이스를 마우스 오른쪽 단추로 클릭하고 **작업**을 가리킨 다음 **데이터 계층 응용 프로그램 내보내기...**를 클릭합니다.

	![작업 메뉴에서 데이터 계층 응용 프로그램 내보내기](./media/sql-database-cloud-migrate/TestForCompatibilityUsingSSMS01.png)

4. 내보내기 마법사에서 **다음**을 클릭한 다음 **설정** 탭에서 BACPAC 파일을 로컬 디스크 위치 또는 Azure BLOB에 저장하는 내보내기를 구성합니다. 데이터베이스 호환성 문제가 없으면 BACPAC 파일이 저장됩니다. 호환성 문제가 있는 경우 콘솔에 해당 문제가 표시됩니다.

	![설정 내보내기](./media/sql-database-cloud-migrate/TestForCompatibilityUsingSSMS02.png)

5. 데이터 내보내기를 건너뛰려면 **고급 탭**을 클릭하고 **모두 선택** 확인란을 선택 취소합니다. 이 시점의 목표는 단순히 호환성을 테스트하는 것입니다.

	![설정 내보내기](./media/sql-database-cloud-migrate/TestForCompatibilityUsingSSMS03.png)

6. **다음**을 클릭한 후 **마침**을 클릭합니다. 마법사가 스키마의 유효성을 확인하고 나면 데이터베이스 호환성 문제(있는 경우)가 표시됩니다.

	![설정 내보내기](./media/sql-database-cloud-migrate/TestForCompatibilityUsingSSMS04.png)

7. 오류가 표시되지 않을 경우 데이터베이스가 호환되며 마이그레이션할 준비가 된 것입니다. 오류가 있을 경우 해결해야 합니다. 오류를 보려면 **스키마 유효성 검사**에 대해 **오류**를 클릭합니다. ![설정 내보내기](./media/sql-database-cloud-migrate/TestForCompatibilityUsingSSMS05.png)

8.	*.BACPAC 파일이 성공적으로 생성되면 데이터베이스가 SQL 데이터베이스와 호환되며, 이제 마이그레이션 준비가 완료되었습니다.

## 다음 단계

- [SSDT 최신 버전](https://msdn.microsoft.com/library/mt204009.aspx)
- [SQL Server Management Studio 최신 버전](https://msdn.microsoft.com/library/mt238290.aspx)
- [데이터베이스 마이그레이션 호환성 문제 해결](sql-database-cloud-migrate.md#fix-database-migration-compatibility-issues)
- [호환되는 SQL Server 데이터베이스를 SQL 데이터베이스로 마이그레이션](sql-database-cloud-migrate.md#migrate-a-compatible-sql-server-database-to-sql-database)

## 추가 리소스

- [SQL 데이터베이스 V12](sql-database-v12-whats-new.md)
- [Transact-SQL의 부분적으로 지원되거나 지원되지 않는 기능](sql-database-transact-sql-information.md)
- [SQL Server Migration Assistant를 사용하여 SQL Server 이외의 데이터베이스 마이그레이션](http://blogs.msdn.com/b/ssma/)

<!---HONumber=AcomDC_0831_2016-->