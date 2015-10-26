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
   ms.date="10/12/2015"
   ms.author="carlrab"/>

# SQL Server 데이터베이스를 Azure SQL 데이터베이스로 마이그레이션

Azure SQL 데이터베이스 V12는 SQL Server 2014 및 SQL Server 2016과 엔진 호환성이 거의 완벽에 가깝습니다. 호환되는 데이터베이스에서 Azure SQL 데이터베이스로 마이그레이션하는 작업은 간단한 스키마 변경(필요한 경우) 또는 응용 프로그램 리엔지니어링이 거의 필요하지 않은 간단한 스키마 및 데이터 이동 작업입니다. 데이터베이스를 변경해야 할 경우 변경 범위는 Azure SQL Database V11보다 더 제한적입니다.

설계상 SQL Server의 서버 범위 기능은 Azure SQL 데이터베이스 V12에서 지원되지 않습니다. 이러한 기능에 의존하는 데이터베이스 및 응용 프로그램은 마이그레이션될 수 있기 전에 일부 엔지니어링을 다시 해야 합니다.

>[AZURE.NOTE]Microsoft Access, Sybase, MySQL Oracle, DB2와 같은 다른 유형의 데이터베이스를 Azure SQL 데이터베이스로 마이그레이션해야 할 경우 [SSMA(SQL Server Migration Assistant)](http://blogs.msdn.com/b/ssma/)를 참조하세요.

SQL Server 데이터베이스를 Azure SQL 데이터베이스로 마이그레이션하는 워크플로는 다음과 같습니다.

 1. [데이터베이스가 호환되는지 확인](#determine-if-your-database-is-compatible)
 2. [호환되지 않을 경우 데이터베이스 호환성 문제 해결](#fix-database-compatibility-issues)
 3. [호환되는 데이터베이스 마이그레이](#options-to-migrate-a-compatible-database-to-azure-sql-database)

## 데이터베이스가 호환되는지 확인
원본 데이터베이스가 호환되는지 여부를 확인하는 방법은 기본적으로 두 가지입니다. 데이터 계층 응용 프로그램 내보내기: 이 방법은 Management Studio의 마법사를 사용하며 콘솔에 오류 메시지를 표시합니다. - SQLPackage.exe: [sqlpackage.exe](https://msdn.microsoft.com/library/hh550080.aspx)는 Visual Studio 및 SQL Server에 기본적으로 포함되는 명령줄 유틸리티입니다. 이 방법은 보고서를 생성합니다.

> [AZURE.NOTE]세 번째 방법은 마찬가지로 추적 파일을 사용하여 호환성을 테스트합니다. 이 [SQL Azure 마이그레이션 마법사](http://sqlazuremw.codeplex.com/)는 Codeplex의 무료 도구입니다. 하지만 이 도구는 현재 Azure SQL Database V12에서 문제가 아니지만 Azure SQL Database V11에서 문제였던 호환성 오류를 찾을 수 있습니다.

데이터베이스 비호환성이 감지될 경우 데이터베이스를 Azure SQL 데이터베이스로 마이그레이션하기 전에 이러한 비호환성을 해결해야 합니다. 데이터베이스 호환성 문제를 해결하는 방법에 대한 지침은 [데이터베이스 호환성 문제 해결](#fix-database-compatibility-issues)을 참조하세요.

> [AZURE.IMPORTANT]이러한 옵션은 다른 수준의 SQL Server 데이터베이스(즉, 레벨 90, 100, 110) 사이에서 모든 호환성 문제를 찾지 않습니다. 기존 데이터베이스(레벨 80, 90, 100, 110)에서 마이그레이션하는 경우 우선 업그레이드 프로세스를 실행하고(최소한 개발 환경에서) SQL Server 2014 이상을 설치한 다음 Azure SQL 데이터베이스로 마이그레이션해야 합니다.

## sqlpackage.exe를 사용하여 데이터베이스 호환성 확인

1. 명령 프롬프트를 열고 최신 버전의 sqlpackage.exe가 포함된 디렉터리를 변경합니다. 이 유틸리티는 Visual Studio 및 SQL Server에 기본적으로 제공됩니다.
2. 다음 명령을 실행하고 < server_name >, < database_name >, < target_file >, < schema_name.table_name >, < output_file > 인수를 대체합니다. /p:TableName 인수를 사용하는 이유는 모든 테이블의 데이터를 내보내지 않고 Azure SQL DB V12로 내보내기에 대한 데이터 호환성만 테스트하기 때문입니다. 하지만 sqlpackage.exe의 내보내기 인수는 테이블 없는 추출을 지원하지 않으므로 하나의 작은 테이블을 지정해야 합니다. < output_file >에 오류 보고서가 포함됩니다.

	'sqlpackage.exe /Action:Export /ssn:< server_name > /sdn:< database_name > /tf:< target_file > /p:TableData=< schema_name.table_name > > < output_file > 2>&1'

	![작업 메뉴에서 데이터 계층 응용 프로그램 내보내기](./media/sql-database-cloud-migrate/TestForCompatibilityUsingSQLPackage01.png)

3. 출력 파일을 열고 호환성 오류(있는 경우)를 검토합니다. 데이터베이스 호환성 문제를 해결하는 방법에 대한 지침은 [데이터베이스 호환성 문제 해결](#fix-database-compatibility-issues)을 참조하세요.

	![작업 메뉴에서 데이터 계층 응용 프로그램 내보내기](./media/sql-database-cloud-migrate/TestForCompatibilityUsingSQLPackage02.png)

## 내보내기 데이터 계층 응용 프로그램을 사용하여 데이터베이스 호환성 확인

1. 버전 13.0.600.65 이상의 SQL Server Management Studio가 있는지 확인합니다. 새로운 버전의 Management Studio는 매월 업데이트되어 Azure 포털의 업데이트와 동기화 상태를 유지합니다.

 	 >[AZURE.IMPORTANT][최신](https://msdn.microsoft.com/library/mt238290.aspx) 버전의 SQL Server Management Studio를 다운로드합니다. 항상 최신 버전의 Management Studio를 사용하는 것이 좋습니다.

2. Management Studio를 열고 개체 탐색기에서 원본 데이터베이스에 연결합니다.
3. 개체 탐색기에서 원본 데이터베이스를 마우스 오른쪽 단추로 클릭하고 **작업**을 가리킨 다음 **데이터 계층 응용 프로그램 내보내기...**를 클릭합니다.

	![작업 메뉴에서 데이터 계층 응용 프로그램 내보내기](./media/sql-database-cloud-migrate/TestForCompatibilityUsingSSMS01.png)

4. 내보내기 마법사의 **설정** 탭에서 BACPAC 파일을 로컬 디스크 위치 또는 Azure BLOB에 저장하는 내보내기를 구성합니다. BACPAC 파일은 데이터베이스 호환성 문제가 없는 경우에만 저장됩니다. 호환성 문제가 있는 경우 콘솔에 표시됩니다.

	![설정 내보내기](./media/sql-database-cloud-migrate/TestForCompatibilityUsingSSMS02.png)

5. **고급 탭**을 클릭하고 **모두 선택** 확인란을 선택 취소하여 데이터 내보내기를 건너뜁니다. 이 시점의 목표는 단순히 호환성을 테스트하는 것입니다.

	![설정 내보내기](./media/sql-database-cloud-migrate/TestForCompatibilityUsingSSMS03.png)

6. **다음**을 클릭한 후 **마침**을 클릭합니다. 마법사가 스키마의 유효성을 확인한 다음 데이터베이스 호환성 문제가 나타납니다(있는 경우).

	![설정 내보내기](./media/sql-database-cloud-migrate/TestForCompatibilityUsingSSMS04.png)

7. 오류가 표시되지 않을 경우 데이터베이스가 호환되며 마이그레이션할 준비가 된 것입니다. 오류가 있을 경우 해결해야 합니다. 오류를 보려면 **스키마 유효성 검사**에 대해 **오류**를 클릭합니다. 이러한 오류를 해결하는 방법은 [데이터베이스 호환성 문제 해결](#fix-database-compatibility-issues)을 참조하세요.

	![설정 내보내기](./media/sql-database-cloud-migrate/TestForCompatibilityUsingSSMS05.png)

## 호환되는 데이터베이스를 Azure SQL 데이터베이스로 마이그레이션하는 옵션

- 중소규모 데이터베이스의 경우 [호환 가능한](#determine-if-your-database-is-compatible) SQL Server 2005 이후의 데이터베이스를 마이그레이션하려면 SQL Server Management Studio에서 [Microsoft Azure 데이터베이스에 배포 마법사](#use-the-deploy-database-to-microsoft-azure-database-wizard)를 실행하기만 하면 됩니다. 연결 문제(연결 없음, 낮은 대역폭, 시간 초과 문제)가 있는 경우 [BACPAC를 사용](#use-a-bacpac-to-migrate-a-database-to-azure-sql-database)하여 SQL Server 데이터베이스를 Azure SQL 데이터베이스로 마이그레이션할 수 있습니다.
- 중간-대규모 데이터베이스의 경우나 연결 문제가 있는 경우에는 [BACPAC를 사용](#use-a-bacpac-to-migrate-a-database-to-azure-sql-database)하여 SQL Server 데이터베이스를 Azure SQL 데이터베이스로 마이그레이션합니다. 이 방법에서는 SQL Server Management Studio를 사용하여 데이터와 스키마를 [BACPAC](https://msdn.microsoft.com/library/ee210546.aspx#Anchor_4) 파일(로컬 또는 Azure BLOB에 저장)로 내보낸 다음 BACPAC 파일을 Azure SQL 인스턴스로 가져옵니다. 또한 BACPAC를 Azure BLOB에 저장하는 경우 [Azure 포털](sql-database-import.md) 내에서나 [PowerShell을 사용](sql-database-import-powershell.md)하여 BACPAC 파일을 가져올 수 있습니다.
- 더 큰 데이터베이스의 경우 스키마와 데이터를 개별적으로 마이그레이션하여 최상의 성능을 얻습니다. 이 방법에서는 SQL Server Management Studio를 사용하여 스키마를 스크립팅하거나 Visual Studio에서 데이터베이스 프로젝트를 만든 다음 스키마를 Azure SQL 데이터베이스에 배포합니다. 스키마를 Azure SQL 데이터베이스로 가져온 후 [BCP](https://msdn.microsoft.com/library/ms162802.aspx)를 사용하여 데이터를 플랫 파일로 추출한 다음 이러한 파일을 Azure SQL 데이터베이스로 가져옵니다.

 ![SSMS 마이그레이션 다이어그램](./media/sql-database-migrate-ssms/01SSMSDiagram.png)

## Microsoft Azure 데이터베이스에 배포 마법사 사용

SQL Server Management Studio의 Microsoft Azure 데이터베이스에 배포 마법사는 [호환되는](#determine-if-your-database-is-compatible) SQL Server 2005 이상의 데이터베이스를 Azure SQL 논리 서버 인스턴스로 마이그레이션합니다.

> [AZURE.NOTE]아래 단계는 사용자가 이미 Azure SQL 논리 인스턴스를 프로비전하고 연결 정보를 알고 있는 경우를 가정합니다.

1. 버전 13.0.600.65 이상의 SQL Server Management Studio가 있는지 확인합니다. 새로운 버전의 Management Studio는 매월 업데이트되어 Azure 포털의 업데이트와 동기화 상태를 유지합니다.

	 >[AZURE.IMPORTANT][최신](https://msdn.microsoft.com/library/mt238290.aspx) 버전의 SQL Server Management Studio를 다운로드합니다. 항상 최신 버전의 Management Studio를 사용하는 것이 좋습니다.

2. Management Studio를 열고 개체 탐색기에서 원본 데이터베이스에 연결합니다.
3. 개체 탐색기에서 원본 데이터베이스를 마우스 오른쪽 단추로 클릭하고 **작업**을 가리킨 다음 **Microsoft Azure SQL 데이터베이스로 배포...**를 클릭합니다.

	![작업 메뉴에서 Azure에 배포 합니다.](./media/sql-database-cloud-migrate/MigrateUsingDeploymentWizard01.png)

4.	배포 마법사에서 Azure SQL 데이터베이스 서버에 대한 연결을 구성합니다.
5.	Azure SQL DB에서 데이터베이스에 대해 **새 데이터베이스 이름**을 입력하고 **Microsoft Azure SQL 데이터베이스 버전**(서비스 계층), **최대 데이터베이스 크기**, **서비스 목표**(성능 수준), 마이그레이션 프로세스 중 이 마법사가 만드는 BACPAC 파일의 **임시 파일 이름**을 입력합니다. 서비스 계층 및 성능 수준에 대한 자세한 내용은 [Azure SQL 데이터베이스 서비스 계층](sql-database-service-tiers.md)을 참조하세요.

	![설정 내보내기](./media/sql-database-cloud-migrate/MigrateUsingDeploymentWizard02.png)

6.	데이터베이스를 마이그레이션하도록 마법사를 완료합니다. 데이터베이스의 크기와 복잡성에 따라 배포는 몇 분에서 몇 시간이 걸릴 수 있습니다.
7.	개체 탐색기를 사용하여 Azure SQL 데이터베이스 서버에서 마이그레이션된 데이터베이스에 연결합니다.
8.	Azure 포털을 사용하여 데이터베이스와 해당 속성을 봅니다.

## BACPAC를 사용하여 SQL Server 데이터베이스를 Azure SQL 데이터베이스로 마이그레이션

중간-대규모 데이터베이스의 경우 또는 연결 문제가 있는 경우 마이그레이션 프로세스를 두 개의 개별 단계로 구분할 수 있습니다. 하나 또는 두 가지 방법을 사용하여 스키마와 데이터를 [BACPAC](https://msdn.microsoft.com/library/ee210546.aspx#Anchor_4) 파일로 내보낼 수 있습니다.

- [SQL Server Management Studio를 사용하여 BACPAC 파일로 내보내기](#export-a-compatible-sql-server-database-to-a-bacpac-file-using-sql-server-management-studio)
- [SqlPackage를 사용하여 BACPAC로 내보내기](#export-a-compatible-sql-server-database-to-a-bacpac-file-using-sqlpackage)

이 BACPAC를 로컬로 또는 Azure BLOB에 저장할 수 있습니다. 그런 다음 몇 가지 방법 중 하나를 사용하여 이 BACPAC 파일을 Azure SQL 데이터베이스로 가져올 수 있습니다.

- [SQL Server Management Studio를 사용하여 BACPAC 파일을 Azure SQL 데이터베이스로 가져오기](#import-from-a-bacpac-file-into-azure-sql-database-using-sql-server-management-studio)
- [SqlPackage를 사용하여 BACPAC 파일을 Azure SQL 데이터베이스로 가져오기](#import-from-a-bacpac-file-into-azure-sql-database-using-sqlpackage)
- [Azure 포털을 사용하여 BACPAC 파일을 Azure SQL 데이터베이스로 가져오기](sql-database-import.md)
- [PowerShell을 사용하여 BACPAC 파일을 Azure SQL 데이터베이스로 가져오기](sql-database-import-powershell.md)

## SQL Server Management Studio를 사용하여 호환되는 SQL Server 데이터베이스를 BACPAC 파일로 내보내기

아래 단계에 따라 Management Studio를 사용하여 [호환되는](#determine-if-your-database-is-compatible) SQL Server 데이터베이스를 BACPAC 파일로 내보냅니다.

1. 버전 13.0.600.65 이상의 SQL Server Management Studio가 있는지 확인합니다. 새로운 버전의 Management Studio는 매월 업데이트되어 Azure 포털의 업데이트와 동기화 상태를 유지합니다.

	 >[AZURE.IMPORTANT][최신](https://msdn.microsoft.com/library/mt238290.aspx) 버전의 SQL Server Management Studio를 다운로드합니다. 항상 최신 버전의 Management Studio를 사용하는 것이 좋습니다.

2. Management Studio를 열고 개체 탐색기에서 원본 데이터베이스에 연결합니다.

	![작업 메뉴에서 데이터 계층 응용 프로그램 내보내기](./media/sql-database-cloud-migrate/MigrateUsingBACPAC01.png)

3. 개체 탐색기에서 원본 데이터베이스를 마우스 오른쪽 단추로 클릭하고 **작업**을 가리킨 다음 **데이터 계층 응용 프로그램 내보내기...**를 클릭합니다.

	![작업 메뉴에서 데이터 계층 응용 프로그램 내보내기](./media/sql-database-cloud-migrate/TestForCompatibilityUsingSSMS01.png)

4. 내보내기 마법사에서 내보내기를 구성하여 로컬 디스크 위치 또는 Azure BLOB에 BACPAC 파일을 저장합니다. 내보낸 BACPAC 파일에는 항상 완전한 데이터베이스 스키마가 포함되며 기본적으로 모든 테이블의 데이터가 포함됩니다. 일부 또는 모든 테이블의 데이터를 제외하려면 고급 탭을 사용합니다. 예를 들어 모든 테이블이 아닌 참조 테이블에 대한 데이터만 내보내도록 선택할 수 있습니다.

	![설정 내보내기](./media/sql-database-cloud-migrate/MigrateUsingBACPAC02.png)

## SqlPackage를 사용하여 호환되는 SQL Server 데이터베이스를 BACPAC 파일로 내보내기

아래 단계를 따라 [SqlPackage.exe](https://msdn.microsoft.com/library/hh550080.aspx) 명령줄 유틸리티를 사용하여 [호환되는](#determine-if-your-database-is-compatible) 데이터베이스를 BACPAC 파일로 내보냅니다.

> [AZURE.NOTE]아래 단계는 사용자가 이미 Azure SQL 데이터베이스 서버를 프로비전하고 연결 정보를 알고 있으며 원본 데이터베이스가 호환됨을 확인한 경우를 가정합니다.

1. 명령 프롬프트를 열고 sqlpackage.exe 명령줄 유틸리티가 들어 있는 디렉터리를 변경합니다. 이 유틸리티는 Visual Studio 및 SQL Server에 기본적으로 제공됩니다.
2. 다음 명령을 실행하고 < server_name >, < database_name >, < target_file > 인수를 대체합니다.

	'sqlpackage.exe /Action:Export /ssn:< server_name > /sdn:< database_name > /tf:< target_file >

	![작업 메뉴에서 데이터 계층 응용 프로그램 내보내기](./media/sql-database-cloud-migrate/TestForCompatibilityUsingSQLPackage01b.png)

## SQL Server Management Studio를 사용하여 BACPAC 파일을 Azure SQL 데이터베이스로 가져오기

아래 단계에 따라 BACPAC 파일을 Azure SQL 데이터베이스로 가져옵니다.

> [AZURE.NOTE]아래 단계는 사용자가 이미 Azure SQL 논리 인스턴스를 프로비전하고 연결 정보를 알고 있는 경우를 가정합니다.

1. 버전 13.0.600.65 이상의 SQL Server Management Studio가 있는지 확인합니다. 새로운 버전의 Management Studio는 매월 업데이트되어 Azure 포털의 업데이트와 동기화 상태를 유지합니다.

	 >[AZURE.IMPORTANT][최신](https://msdn.microsoft.com/library/mt238290.aspx) 버전의 SQL Server Management Studio를 다운로드합니다. 항상 최신 버전의 Management Studio를 사용하는 것이 좋습니다.

2. Management Studio를 열고 개체 탐색기에서 원본 데이터베이스에 연결합니다.

	![작업 메뉴에서 데이터 계층 응용 프로그램 내보내기](./media/sql-database-cloud-migrate/MigrateUsingBACPAC01.png)

    BACPAC가 생성되면 Azure SQL 데이터베이스 서버에 연결하고 **Databases** 폴더를 마우스 오른쪽 단추로 클릭하고 **데이터 계층 응용 프로그램 가져오기...**를 클릭합니다.

    ![데이터 계층 응용 프로그램 메뉴 항목 가져오기](./media/sql-database-cloud-migrate/MigrateUsingBACPAC03.png)

3.	가져오기 마법사에서 방금 내보낸 BACPAC 파일을 선택하여 Azure SQL 데이터베이스에 새 데이터베이스를 만듭니다.

    ![설정 가져오기](./media/sql-database-cloud-migrate/MigrateUsingBACPAC04.png)

4.	Azure SQL DB에서 데이터베이스에 대해 **새 데이터베이스 이름**을 입력하고 **Microsoft Azure SQL 데이터베이스 버전**(서비스 계층), **최대 데이터베이스 크기**, **서비스 목표**(성능 수준)를 입력합니다.

    ![데이터베이스 설정](./media/sql-database-cloud-migrate/MigrateUsingBACPAC05.png)

5.	**다음**을 클릭한 다음 **마침**을 클릭하여 BACPAC 파일을 Azure SQL 데이터베이스 서버의 새 데이터베이스로 가져옵니다.

6. 개체 탐색기를 사용하여 Azure SQL 데이터베이스 서버에서 마이그레이션된 데이터베이스에 연결합니다.

7.	Azure 포털을 사용하여 데이터베이스와 해당 속성을 봅니다.

## SqlPackage를 사용하여 BACPAC 파일을 Azure SQL 데이터베이스로 가져오기

아래 단계를 따라 [SqlPackage.exe](https://msdn.microsoft.com/library/hh550080.aspx) 명령줄 유틸리티를 사용하여 호환되는 SQL Server 데이터베이스(또는 Azure SQL 데이터베이스)를 BACPAC 파일로 가져옵니다.

> [AZURE.NOTE]아래 단계는 사용자가 이미 Azure SQL 데이터베이스 서버를 프로비전하고 연결 정보를 알고 있는 경우를 가정합니다.

1. 명령 프롬프트를 열고 sqlpackage.exe 명령줄 유틸리티가 들어 있는 디렉터리를 변경합니다. 이 유틸리티는 Visual Studio 및 SQL Server에 기본적으로 제공됩니다.
2. 다음 명령을 실행하고 < server_name >, < database_name >, < user_name >, < password >, < source_file > 인수를 대체합니다.

	'sqlpackage.exe /Action:Import /tsn:< server_name > /tdn:< database_name > /tu:< user_name > /tp:< password > /sf:< target_file >

	![작업 메뉴에서 데이터 계층 응용 프로그램 내보내기](./media/sql-database-cloud-migrate/TestForCompatibilityUsingSQLPackage01c.png)


## 데이터베이스 호환성 문제 해결

원본 SQL Server 데이터베이스가 호환되지 않는 것으로 확인될 경우 다양한 옵션을 사용하여 [앞에서 식별한](#determine-if-your-database-is-compatible) 데이터베이스 호환성 문제를 해결할 수 있습니다.

- [SQL Azure 마이그레이션 마법사](http://sqlazuremw.codeplex.com/)를 사용합니다. 이 Codeplex 도구를 사용하여 호환되지 않는 원본 데이터베이스에서 T-SQL 스크립트를 생성하면 마법사가 이 스크립트를 SQL 데이터베이스와 호환되도록 변환합니다. 그런 다음 Azure 데이터베이스에 연결하여 스크립트를 실행합니다. 이 도구는 또한 추적 파일을 분석하여 호환성 문제를 확인합니다. 스크립트는 스키마만 사용하여 생성하거나 BCP 형식의 데이터를 포함할 수 있습니다. 단계별 지침을 포함한 추가 설명서는 [SQL Azure 마이그레이션 마법사](http://sqlazuremw.codeplex.com/)의 Codeplex에서 사용할 수 있습니다.  

 ![SAMW 마이그레이션 다이어그램](./media/sql-database-cloud-migrate/02SAMWDiagram.png)

 >[AZURE.NOTE]마법사에서 감지할 수 있는 모든 호환되지 않는 스키마를 내장된 변환 기능으로 처리할 수 있는 것은 아닙니다. 처리할 수 없는 호환되지 않는 스크립트는 오류로 보고되고 생성된 스크립트에 주석이 삽입됩니다. 많은 오류가 감지될 경우 Visual Studio 또는 SQL Server Management Studio를 사용하여 SQL Server 마이그레이션 마법사로 해결할 수 없는 각 오류를 단계적으로 해결합니다.

- Visual Studio 사용. Visual Studio를 사용하여 데이터베이스 스키마를 Visual Studio 데이터베이스 프로젝트로 가져온 다음 분석할 수 있습니다. 분석하려면 대한 대상 플랫폼을 SQL 데이터베이스 V12로 프로젝트로 지정하고 프로젝트를 빌드합니다. 빌드에 성공한 경우 데이터베이스가 호환됩니다. 빌드가 실패한 경우 Visual Studio ("SSDT")용 SQL Server Data Tools에서 오류를 해결할 수 있습니다. 프로젝트가 성공적으로 빌드되면 원본 데이터베이스의 복사본으로 다시 게시할 수 있으며 다음 SSDT에서 데이터 비교 기능을 사용하여 원본 데이터베이스에서 Azure SQL V12 호환 가능한 데이터베이스로 데이터를 복사할 수 있습니다. 업데이트된 이 데이터베이스를 [앞에서 설명](#options-to-migrate-a-compatible-database-to-azure-sql-database)한 옵션을 사용하여 Azure SQL 데이터베이스로 배포합니다.

 ![VSSSDT 마이그레이션 다이어그램](./media/sql-database-cloud-migrate/03VSSSDTDiagram.png)

 >[AZURE.NOTE]스키마만 마이그레이션해야 할 경우 Visual Studio에서 Azure SQL 데이터베이스로 직접 스키마를 게시할 수 있습니다. 데이터베이스 스키마가 마이그레이션 마법사 하나로 처리될 수 있는 것보다 많은 변경 내용이 필요한 경우 이 메서드를 사용합니다.

- SQL Server Management Studio. **ALTER DATABASE**와 같은 다양한 Transact-SQL 명령을 사용하여 Management Studio에서 문제를 해결할 수 있습니다.

<!---HONumber=Oct15_HO3-->