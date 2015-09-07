<properties
   pageTitle="SSMS를 사용하여 SQL 데이터베이스로 마이그레이션"
	description="Microsoft Azure SQL 데이터베이스, sql 데이터베이스 마이그레이션, ssms를 사용하여 마이그레이션"
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

#SSMS를 사용하여 호환 데이터베이스 마이그레이션

![SSMS 마이그레이션 다이어그램](./media/sql-database-migrate-ssms/01SSMSDiagram.png)

데이터베이스 스키마가 Azure SQL 데이터베이스와 호환되는 경우 마이그레이션은 데이터베이스를 Azure로 가져올 필요가 있습니다. SSMS를 사용하여 데이터베이스를 Azure SQL 데이터베이스에 ‘배포’하는 1단계 프로세스 또는 먼저 데이터베이스의 BACPAC를 내보낸 후 해당 BACPAC를 Azure SQL 서버에 새 데이터베이스로 가져오는 2단계 프로세스로 수행할 수 있습니다.

BACPAC를 내보낼 경우 로컬 파일 또는 Azure blob에 직접 내보낼 수 있습니다. 로컬로 내보낼 경우 Azure blob에 내보낸 BACPAC를 업로드할 수 있습니다. BACPAC 파일이 Azure blob에 저장되면 Azure 포털을 사용하여 데이터베이스로 BACPAC를 가져올 수 있습니다. Azure 포털에서 가져오기를 실행하면 가져오기 단계에서 대기 시간이 감소하므로 대용량 데이터베이스를 마이그레이션할 때 성능 및 안정성이 향상됩니다.

BACPAC를 사용하는 내보내기로 BACPAC를 내보내려면 개체의 하위 집합을 선택하는 반면 SSMS의 직접 배포는 항상 전체 스키마 및 모든 데이터를 배포합니다. 내보낸 BACPAC 파일에는 항상 완전한 데이터베이스 스키마가 포함되며 기본적으로 모든 테이블의 데이터가 포함됩니다. SSMS에서 배포하든 아니면 SSMS에서(또는 Azure 포털) 내보낸 후 가져오든, 내부에서 동일한 DAC 기술이 사용되며 결과도 같습니다.

이 옵션은 Azure SQL 데이터베이스와 호환되도록 데이터베이스를 업데이트한 후 마이그레이션하는 옵션 #2의 마지막 단계에서도 사용됩니다.

## SQL Server Management Studio의 최신 버전 가져오기

최신 버전의 SQL Server용 Microsoft SQL Server Management Studio를 사용하여 Azure 포털과 상호 작용 및 SSMS 내에서 도구에 대한 최신 업데이트가 있는지 확인합니다. SQL Server에 대해 최신 버전의 Microsoft SQL Server Management Studio를 사용하려면 마이그레이션할 데이터베이스와 인터넷에 연결된 클라이언트 컴퓨터에 [다운로드](https://msdn.microsoft.com/library/mt238290.aspx)하여 설치합니다.

##SSMS를 사용하여 Azure SQL 데이터베이스에 배포
1.	Azure 관리 포털을 사용하여 논리 서버 프로비전
2.	SSMS 개체 탐색기에서 원본 데이터베이스를 찾아서 **데이터 계층 응용 프로그램 내보내기…** 작업을 실행합니다.

	![작업 메뉴에서 Azure에 배포 합니다.](./media/sql-database-migrate-ssms/02MigrateusingSSMS.png)

3.	배포 마법사에서, 1단계에서 프로비전한 대상 Azure SQL 데이터베이스 논리 서버에 대한 연결을 구성합니다.
4.	데이터베이스 **이름**을 입력하고 **버전**(서비스 계층) 및 **서비스 목표**(성능 수준)를 설정합니다. 이 설정 구성에 관한 자세한 내용은 [Azure SQL 데이터베이스 서비스 계층](sql-database-service-tiers.md)을 참조하세요.

	![설정 내보내기](./media/sql-database-migrate-ssms/03MigrateusingSSMS.png)

5.	데이터베이스를 마이그레이션하도록 마법사를 완료합니다. 데이터베이스의 크기와 복잡성에 따라 배포는 몇 분에서 몇 시간이 걸릴 수 있습니다. 비 호환성 문제가 있으면 실제로 Azure에 배포하기 전에 스키마 유효성 검사 루틴이 오류를 신속하게 검색합니다. 데이터베이스 스키마가 SQL 데이터베이스와 호환되지 않는다는 오류가 발생할 경우 마이그레이션 옵션 #2을 사용합니다. [데이터베이스를 제자리에서 업데이트한 후 Azure SQL 데이터베이스에 배포](sql-database-migrate-visualstudio-ssdt.md)를 참조하세요.

##SSMS를 사용하여 BACPAC를 내보낸 후 SQL 데이터베이스로 가져오는 방법을 사용하세요.
배포 프로세스는 내보내기와 가져오기의 두 단계로 나눌 수 있습니다. 첫 번째 단계에서 BACPAC 파일이 생성되며, 이 파일은 두 번째 단계에서 입력으로 사용됩니다.

1.	Azure 관리 포털을 사용하여 논리 서버 프로비전
2.	SSMS 개체 탐색기에서 원본 데이터베이스를 찾아서 **Azure SQL 데이터베이스에 데이터베이스 배포…** 작업을 선택합니다.

	![작업 메뉴에서 데이터 계층 응용 프로그램 내보내기](./media/sql-database-migrate-ssms/04MigrateusingSSMS.png)

3. 내보내기 마법사에서 내보내기를 구성하여 로컬 디스크 위치 또는 Azure blob에 BACPAC 파일을 저장합니다. 내보낸 BACPAC 파일에는 항상 완전한 데이터베이스 스키마가 포함되며 기본적으로 모든 테이블의 데이터가 포함됩니다. 일부 또는 모든 테이블의 데이터를 제외하려면 고급 탭을 사용합니다. 예를 들어 참조 테이블에 대한 데이터만 내보내도록 선택할 수 있습니다.

	![설정 내보내기](./media/sql-database-migrate-ssms/05MigrateusingSSMS.png)

4.	BACPAC가 생성되면 1단계에서 만든 서버에 연결하고, **Databases** 폴더를 마우스 오른쪽 단추로 클릭하고, **데이터 계층 응용 프로그램 가져오기...**를 선택합니다.
	
	>[AZURE.NOTE]참고: Azure 관리 포털 내에서 Azure blob에 저장된 BACPAC 파일을 가져올 수 있습니다.

	![데이터 계층 응용 프로그램 메뉴 항목 가져오기](./media/sql-database-migrate-ssms/06MigrateusingSSMS.png)

5.	가져오기 마법사에서 방금 내보낸 BACPAC 파일을 선택하여 Azure SQL 데이터베이스에 새 데이터베이스를 만듭니다.

	![설정 가져오기](./media/sql-database-migrate-ssms/07MigrateusingSSMS.png)

6.	데이터베이스 이름을 입력하고 버전(서비스 계층) 및 서비스 목표(성능 수준)를 설정합니다.

7.	마법사를 완료하여 BACPAC 파일을 가져오고 Azure SQL 데이터베이스에 데이터베이스를 만듭니다.

	![데이터베이스 설정](./media/sql-database-migrate-ssms/08MigrateusingSSMS.png)

##대안
BACPAC 명령줄 유틸리티 sqlpackage.exe를 사용하여 데이터베이스를 배포하거나 BACPAC 파일을 내보내고 가져올 수도 있습니다 Sqlpackage.exe는 SSMS와 동일한 DAC 기술을 사용하기 때문에 결과도 동일합니다. 자세한 내용은 [MSDN에서 SqlPackage.exe](https://msdn.microsoft.com/library/hh550080.aspx)를 참조하세요.

<!---HONumber=August15_HO9-->