<properties 
   pageTitle="Visual Studio 및 SSDT를 사용하여 마이그레이션" 
   description="Microsoft Azure SQL 데이터베이스, 데이터베이스 마이그레이션, 데이터베이스 가져오기, 데이터베이스 내보내기, 마이그레이션 마법사" 
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
   ms.date="08/24/2015"
   ms.author="carlrab"/>

#데이터베이스를 제자리에서 업데이트한 후 Azure SQL 데이터베이스에 배포

![대체 텍스트](./media/sql-database-migrate-visualstudio-ssdt/01VSSSDTDiagram.png)

해당 데이터베이스가 Azure SQL 데이터베이스에서 지원되지 않는 SQL Swever 기능을 사용하기 때문에 Azure SQL 데이터베이스 V12로 온-프레미스 데이터베이스 마이그레이션할 때 스키마 변경이 필요한 경우 또는 지원되지 않는 기능이 온-프레미스 데이터베이스 내에 표시되는지 테스트하기 위해 이 옵션을 사용합니다.

Visual Studio 2013 업데이트 4 이상이 포함된 [최신 Visual Studio용 SQL Server Data Tools](https://msdn.microsoft.com/library/mt204009.aspx)를 사용하세요.

이 옵션을 사용하여:

 - Visual Studio용 SQL Server 데이터 도구("SSDT")는 원본 데이터베이스에서 데이터베이스 프로젝트를 만드는 데 먼저 사용됩니다. 
 - 그런 다음 프로젝트의 대상 플랫폼을 Azure SQL 데이터베이스 V12로 설정하고 모든 호환성 문제를 식별할 수 있도록 프로젝트를 빌드합니다. 
 - 프로젝트가 빌드되면 스키마를 원본 데이터베이스의 복사본에 다시 게시합니다.(원본 데이터베이스를 덮어쓰지 않음)
 - SSDT에서 데이터 비교 기능을 사용하여 새로 만든 Azure SQL 호환 데이터베이스에 원본 데이터베이스를 비교한 다음 원본 데이터베이스에서 데이터를 사용하여 새 데이터베이스를 업데이트합니다. 
 - 그런 다음 SSMS를 사용하여 업데이트된 데이터베이스를 Azure에 직접 또는 BACPAC 파일을 내보낸 후 가져와서 배포합니다.
 
>[AZURE.NOTE]참고: 스키마 전용 배포가 필요한 경우에는 업데이트된 스키마를 Visual Studio에서 Azure SQL 데이터베이스로 직접 게시할 수 있습니다.

## 마이그레이션 단계

1.	Visual Studio에서 **SQL Server 개체 탐색기**를 엽니다. **SQL Server 추가**를 사용하여 마이그레이션할 데이터베이스가 포함된 SQL Server 인스턴스에 연결합니다. 탐색기에서 데이터베이스를 찾아 마우스 오른쪽 단추로 클릭하고 **새 프로젝트 만들기…**를 선택합니다. 

	![대체 텍스트](./media/sql-database-migrate-visualstudio-ssdt/02MigrateSSDT.png)

2.	가져오기 설정을 **응용 프로그램 범위 개체만 가져오기**로 구성합니다. 다음의 참조된 로그인, 사용 권한 및 데이터베이스 설정을 가져오는 옵션의 선택을 취소합니다.

	![대체 텍스트](./media/sql-database-migrate-visualstudio-ssdt/03MigrateSSDT.png)

3.	**시작**을 클릭하여 데이터베이스를 가져오고 프로젝트를 만듭니다. 이 프로젝트에는 데이터베이스의 각 개체에 대한 T-SQL 스크립트 파일이 포함될 것입니다. 스크립트 파일은 프로젝트 내의 폴더에서 중첩됩니다.

	![대체 텍스트](./media/sql-database-migrate-visualstudio-ssdt/04MigrateSSDT.png)

4.	Visual Studio 솔루션 탐색기에서 데이터베이스 프로젝트를 마우스 오른쪽 단추로 클릭하고 속성을 선택합니다. **프로젝트 설정** 페이지가 열릴 것입니다. 이 페이지에서 대상 플랫폼을 Microsoft Azure SQL 데이터베이스 V12로 구성해야 합니다.

	![대체 텍스트](./media/sql-database-migrate-visualstudio-ssdt/05MigrateSSDT.png)

5.	프로젝트를 마우스 오른쪽 단추로 클릭하고 **빌드**를 선택하여 프로젝트를 빌드합니다.

	![대체 텍스트](./media/sql-database-migrate-visualstudio-ssdt/06MigrateSSDT.png)

6.	**오류 목록**은 비호환성 각각을 표시합니다. 이 경우 사용자 이름은 NT AUTHORITY\\NETWORK SERVICE는 호환되지 않습니다. 호환되지 않으므로 주석으로 처리하거나 제거합니다.(데이터베이스 솔루션에서 이 로그인 및 역할을 제거에 미치는 영향을 짚어봄)

	![대체 텍스트](./media/sql-database-migrate-visualstudio-ssdt/07MigrateSSDT.png)
7.	스크립트를 두 번 클릭하여 쿼리 창에서 스크립트를 열고 주석 처리한 다음 실행합니다. ![대체 텍스트](./media/sql-database-migrate-visualstudio-ssdt/08MigrateSSDT.png)

8.	오류가 남지 않을 때까지 비호환성을 포함하는 각 스크립트에 이 프로세스를 반복합니다. ![대체 텍스트](./media/sql-database-migrate-visualstudio-ssdt/09MigrateSSDT.png)
9.	데이터베이스에 오류가 없으면 프로젝트를 마우스 오른쪽 단추로 클릭하고 **게시**를 선택하여 빌드한 후 원본 데이터베이스의 복사본(적어도 처음에는 복사본을 사용하기를 권장)에 데이터베이스를 게시합니다. 
 - 원본 SQL Server 버전(SQL Server 2014 이전)에 따라 게시하기 전에 배포가 가능하도록 프로젝트의 대상 플랫폼을 다시 설정해야 할 수 있습니다. 
 - 기존 SQL Server 데이터베이스를 마이그레이션하는 경우 상위 SQL Server 버전으로 데이터베이스를 처음 마이그레이션하는 경우를 제외하고 원본 SQL Server에서 지원되지 않는 기능을 프로젝트에 가져오면 안 됩니다. 

	![대체 텍스트](./media/sql-database-migrate-visualstudio-ssdt/10MigrateSSDT.png)

	![대체 텍스트](./media/sql-database-migrate-visualstudio-ssdt/11MigrateSSDT.png)

10.	마법사에서 어떤 변경 사항이 있는지 이해하려면 SQL Server 개체 탐색기에서 원본 데이터베이스를 마우스 오른쪽 단추로 클릭하고 **데이터 비교**를 클릭하여 원본 데이터베이스에 프로젝트를 비교합니다. 데이터베이스의 Azure SQL V12 버전을 선택하고 **마침**을 클릭합니다.

	![대체 텍스트](./media/sql-database-migrate-visualstudio-ssdt/12MigrateSSDT.png)

	![대체 텍스트](./media/sql-database-migrate-visualstudio-ssdt/13MigrateSSDT.png)

12.	검색된 차이점을 검토한 다음 **업데이트 대상**을 클릭하여 원본 데이터베이스에서 Azure SQL V12 데이터베이스로 데이터를 마이그레이션합니다.

	![대체 텍스트](./media/sql-database-migrate-visualstudio-ssdt/14MigrateSSDT.png)

14.	SSMS를 사용하여 Azure SQL V12 호환 데이터베이스 스키마 및 데이터를 Azure SQL 데이터베이스에 배포합니다. [SSMS를 사용하여 호환 데이터베이스 마이그레이션](sql-database-migrate-ssms.md)을 참조하세요.

<!---HONumber=Oct15_HO3-->