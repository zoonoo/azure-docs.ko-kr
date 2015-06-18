<properties
   pageTitle="SSMS를 사용하여 SQL 데이터베이스로 마이그레이션"
   description="Microsoft Azure SQL 데이터베이스, sql 데이터베이스 마이그레이션, ssms를 사용하여 마이그레이션"
   services="sql-database"
   documentationCenter=""
   authors="pehteh"
   manager="jeffreyg"
   editor="monicar"/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-management"
   ms.date="04/14/2015"
   ms.author="pehteh"/>

#SSMS를 사용하여 호환 데이터베이스 마이그레이션 

![대체 텍스트](./media/sql-database-migrate-ssms/01SSMSDiagram.png)

데이터베이스 스키마가 이미 Azure SQL 데이터베이스와 호환되는 경우 마이그레이션이 매우 간단합니다. 변환 작업이 필요 없기 때문에 데이터베이스를 Azure로 가져오기만 하면 마이그레이션이 완료됩니다. SSMS를 사용하여 데이터베이스를 Azure SQL 데이터베이스에 배포하는 1단계 프로세스 또는 BACPAC를 내보낸 후 Azure SQL 데이터베이스 서버로 가져와서 새 데이터베이스를 만드는 2단계 프로세스를 통해 이 작업을 수행할 수 있습니다.

포털을 사용하여 내보낸 BACPAC를 Azure 저장소에 업로드한 후 가져올 수 있습니다. 클라우드에서 가져오기를 실행하면 가져오기 단계에서 대기 시간이 감소하므로 대용량 데이터베이스를 마이그레이션할 때 성능 및 안정성이 향상됩니다.

SSMS에서 직접 배포하는 방법은 항상 스키마와 데이터를 함께 배포하는 반면, 내보낸 후 가져오는 방법은 항상 스키마를 배포하고 모든 테이블 또는 테이블 하위 집합에서 데이터를 배포하는 옵션을 제공합니다. SSMS에서 배포하든 아니면 SSMS에서(또는 나중에 포털에서) 내보낸 후 가져오든, 내부에서 동일한 DAC 기술이 사용되며 결과도 같습니다.

이 옵션은 Azure SQL 데이터베이스와 호환되도록 데이터베이스를 업데이트한 후 마이그레이션하는 옵션 3의 마지막 단계에서도 사용됩니다.

##SSMS를 사용하여 Azure SQL 데이터베이스에 배포
1.	마이그레이션된 데이터베이스의 대상 서버 만들기에서 설명하는 대로 Azure 포털을 사용하여 서버를 프로비전합니다.
2. SSMS 개체 탐색기에서 원본 데이터베이스를 찾아서 **Windows Azure SQL 데이터베이스에 데이터베이스 배포…** 작업을 실행합니다.

	![대체 텍스트](./media/sql-database-migrate-ssms/02MigrateusingSSMS.png)

3.	배포 마법사에서, 이전 단계에서 프로비전한 대상 Azure SQL 데이터베이스 서버에 대한 연결을 구성합니다.
4.	데이터베이스 **이름**을 입력하고 **버전**(서비스 계층) 및 **서비스 목표**(성능 수준)를 설정합니다. 이러한 설정을 구성하는 방법에 대한 자세한 내용은 마이그레이션에 대한 데이터베이스 성능 수준/가격 책정 계층 선택을 참조하세요. 

	![대체 텍스트](./media/sql-database-migrate-ssms/03MigrateusingSSMS.png)

5.	마법사를 완료하여 데이터베이스 마이그레이션을 실행합니다. 데이터베이스의 크기와 복잡성에 따라 배포에 몇 분에서 몇 시간이 걸릴 수 있습니다. 데이터베이스 스키마가 SQL 데이터베이스와 호환되지 않는다는 오류가 발생할 경우 다른 방법을 사용해야 합니다.
##SSMS를 사용하여 BACPAC를 내보낸 후 SQL 데이터베이스로 가져오는 방법을 사용하세요.
배포 프로세스는 내보내기와 가져오기의 두 단계로 나눌 수 있습니다. 첫 번째 단계에서는 BACPAC 파일이 생성됩니다. 이 파일은 두 번째 단계에서 입력으로 사용됩니다.

1.	마이그레이션된 데이터베이스의 대상 서버 만들기에서 설명하는 대로 최신 SQL 데이터베이스 업데이트를 사용하여 서버를 프로비전합니다.
2.	SSMS 개체 탐색기에서 원본 데이터베이스를 찾아서 **Windows Azure SQL 데이터베이스에 데이터베이스 배포…** 작업을 선택합니다.

	![대체 텍스트](./media/sql-database-migrate-ssms/04MigrateusingSSMS.png)

3. 내보내기 마법사에서 BACPAC 파일을 로컬에 저장하도록 내보내기를 구성합니다. 내보낸 BACPAC 파일에는 항상 완전한 데이터베이스 스키마가 포함되며 기본적으로 모든 테이블의 데이터가 포함됩니다. 일부 또는 모든 테이블의 데이터를 제외하려면 고급 탭을 사용하세요. 예를 들어 참조 테이블에 대한 데이터만 내보내도록 선택할 수 있습니다.
	>[AZURE.NOTE]참고: Azure 관리 포털이 Azure에서 실행되는 가져오기를 지원할 경우 내보내는 BACPAC 파일을 Azure 저장소에 저장하고 클라우드에서 가져오기를 실행할 수 있습니다.

	![대체 텍스트](./media/sql-database-migrate-ssms/05MigrateusingSSMS.png)

4.	BACPAC 파일이 생성되면 1단계에서 만든 서버에 연결하고, 데이터베이스 폴더를 마우스 오른쪽 단추로 클릭 하고, 데이터 계층 응용 프로그램 가져오기...를 선택합니다.

	![대체 텍스트](./media/sql-database-migrate-ssms/06MigrateusingSSMS.png)

5.	가져오기 마법사에서 방금 내보낸 BACPAC 파일을 선택하여 Azure SQL 데이터베이스에 새 데이터베이스를 만듭니다.

	![대체 텍스트](./media/sql-database-migrate-ssms/07MigrateusingSSMS.png)

6.	데이터베이스 이름을 입력하고 버전(서비스 계층) 및 서비스 목표(성능 수준)를 설정합니다.
	 
7.	마법사를 완료하여 BACPAC 파일을 가져오고 Azure SQL 데이터베이스에 데이터베이스를 만듭니다.

	![대체 텍스트](./media/sql-database-migrate-ssms/08MigrateusingSSMS.png)
 
##대안
BACPAC 명령줄 유틸리티 sqlpackage.exe를 사용하여 데이터베이스를 배포하거나 BACPAC 파일을 내보내고 가져올 수도 있습니다 Sqlpackage.exe는 SSMS와 동일한 DAC 기술을 사용하기 때문에 결과도 동일합니다. 자세한 내용은 [여기](https://msdn.microsoft.com/library/hh550080.aspx)서 확인할 수 있습니다.

<!---HONumber=58--> 