<properties
	pageTitle="Azure 데이터 팩터리 시작"
	description="이 자습서에서는 Blob에서 Azure SQL 데이터베이스 인스턴스로 데이터를 복사하는 샘플 데이터 파이프라인을 만드는 방법을 보여 줍니다."
	services="data-factory"
	documentationCenter=""
	authors="spelluru"
	manager="jhubbard"
	editor="monicar"/>

<tags
	ms.service="data-factory"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="hero-article" 
	ms.date="05/04/2015"
	ms.author="spelluru"/>

# Azure 데이터 팩터리 시작
> [AZURE.SELECTOR]
- [Tutorial Overview](data-factory-get-started.md)
- [Using Data Factory Editor](data-factory-get-started-using-editor.md)
- [Using PowerShell](data-factory-monitor-manage-using-powershell.md)

이 문서에서는 자습서를 사용 하면 Azure 데이터 팩터리 서비스를 사용 하 여를 빠르게 시작할 수 있습니다. 이 자습서에서는 Azure 데이터 팩터리를 만들고 Azure SQL 데이터베이스에는 Azure blob 저장소에서 데이터를 복사 하려면 데이터 공장에서 파이프라인을 만듭니다.

> [AZURE.NOTE]데이터 팩터리 서비스의 자세한 개요를 참조 하십시오.는 [Azure 데이터 팩터리 소개][data-factory-introduction] 문서입니다.

##이 자습서에 대 한 필수 구성 요소
이 자습서를 시작하기 전에 다음이 있어야 합니다.

- **Azure 구독**. 구독이 없는, 몇 시간 (분)에 무료 평가판 계정을 만들 수 있습니다. 참조는 [무료 평가판][azure-free-trial] 세부 정보에 대 한 문서.
- **Azure 저장소 계정**. Blob 저장소로 사용 합니다는 **소스** 참조 하는 Azure 저장소 계정이 없는 경우이 자습서에서는 데이터 저장소는 [저장소 계정을 만드는][data-factory-create-storage] 을 만드는 단계에 대 한 문서.
- **Azure SQL 데이터베이스**. 으로 Azure SQL 데이터베이스를 사용 합니다는 **대상** 이 자습서에서는 데이터를 저장 합니다. Azure SQL 데이터베이스 참조는 자습서에서 사용할 수 있는 없는 경우 [는 Azure SQL 데이터베이스 만들기 및 구성 하는 방법][data-factory-create-sql-database] 하나를 만들려고 합니다.
- **2014/SQL Server 2012 또는 Visual Studio 2013**. 데이터베이스에 결과 데이터를 볼 수 및 샘플 데이터베이스를 만들려면 SQL Server Management Studio 또는 Visual Studio를 사용 합니다.  

### 계정 이름 및 Azure 저장소 계정에 대 한 계정 키를 수집 합니다.
계정 이름 및이 자습서를 수행 하려면 Azure 저장소 계정의 계정 키 필요 합니다. 기록해 두고는 **계정 이름** 및 **계정 키** 아래 지침에 따라 Azure 저장소 계정에 대해:

1. 로그인 하는 [Azure 미리 보기 포털][azure-preview-portal].
2. 클릭 하 여 **찾아보기** 왼쪽 및 선택에 대 한 허브 **저장소 계정은**.
3. 에 **저장소 계정은** 블레이드를는 **Azure 저장소 계정** 이 자습서에서 사용 하려는 합니다.
4. 에 **저장소** 블레이드를 클릭 하 여 **키** 바둑판식으로 배열 합니다.
5. 에 **키 관리** 블레이드를 클릭 하 여 **복사** (이미지) 옆에 있는 단추가 **저장소 계정 이름** 텍스트 상자 및 저장/붙여넣기 것 어딘가에 (예: 텍스트 파일에).  
6. 복사 하거나 기록해 두고 이전 단계를 반복 하는 **기본 액세스 키**.
7. 클릭 하 여 모든 블레이드가 닫습니다 **X**.

### 서버 이름, 데이터베이스 이름 및 Azure SQL 데이터베이스에 대 한 사용자 계정을 수집합니다
SQL Azure 서버, 데이터베이스 및이 자습서를 수행 하는 사용자의 이름이 필요 합니다. 이름 적어둡니다 **서버**, **데이터베이스**, 및 **사용자** 아래 지침에 따라 Azure SQL 데이터베이스에 대 한:

1. 에 **Azure 미리 보기 포털**, 를 클릭 하 여 **찾아보기** 왼쪽 및 선택에 **SQL 데이터베이스**.
2. 에 **SQL 데이터베이스 블레이드**, 선택는 **데이터베이스** 이 자습서에서 사용 하려는 합니다. 기록해 두고는 **데이터베이스 이름**.  
3. 에 **SQL 데이터베이스** 블레이드를 클릭 하 여 **속성** 바둑판식으로 배열 합니다.
4. 에 대 한 값을 적어둡니다 **서버 이름** 및 **서버 관리자 로그인**.
5. 클릭 하 여 모든 블레이드가 닫습니다 **X**.

### SQL Azure 서버에 액세스할 수 있는 Azure 서비스를 허용 합니다.
되도록 **Azure 서비스에 대 한 액세스를 허용** 설정이 **ON** Azure SQL server에 대 한 데이터 팩터리 서비스는 SQL Azure 서버에 액세스할 수 있도록 합니다. 확인 하 고이 설정을 적용 하려면 다음 절차를 수행 합니다.

1. 클릭 하 여 **찾아보기** 를클릭하고 왼쪽에는 허브 **SQL 서버**.
2. 선택 **서버**, 를 클릭 하 고 **설정을** 에 **SQL SERVER** 블레이드입니다.
3. 에 **설정을** 블레이드를 클릭 하 여 **방화벽**.
4. 에 **방화벽 설정을** 블레이드를 클릭 하 여 **ON** 에 대 한 **Azure 서비스에 대 한 액세스를 허용**.
5. 클릭 하 여 모든 블레이드가 닫습니다 **X**.

### 자습서에서 사용할 Azure Blob 저장소 및 Azure SQL 데이터베이스 준비
이제 Azure blob 저장소 및 Azure SQL 데이터베이스에 자습서 준비 다음 단계를 수행 하 여:

1. 메모장을 시작 하 고 다음 텍스트를 붙여로 저장 **emp.txt** 를 **C:\ADFGetStarted** 하드 드라이브에 폴더입니다.

        John, Doe
		Jane, Doe

2. 와 같은 도구를 사용 하 여 [Azure 저장소 탐색기](https://azurestorageexplorer.codeplex.com/) 를 만들려면는 **adftutorial** 컨테이너 및 업로드 하는 **emp.txt** 컨테이너에는 파일입니다.

    ![Azure 저장소 탐색기][image-data-factory-get-started-storage-explorer]
3. 다음 SQL 스크립트를 사용 하 여 만들려는 **emp** Azure SQL 데이터베이스의 테이블에에서 있습니다.  


        CREATE TABLE dbo.emp
		(
			ID int IDENTITY(1,1) NOT NULL,
			FirstName varchar(50),
			LastName varchar(50),
		)
		GO

		CREATE CLUSTERED INDEX IX_emp_ID ON dbo.emp (ID);

	**SQL Server 2012/2014이 컴퓨터에 설치 된 경우:** 에서 지침에 따라 [2 단계: SQL Server Management Studio를 사용 하 여 관리 Azure SQL 데이터베이스의 SQL 데이터베이스에 연결][sql-management-studio] 문서에서는 SQL Azure 서버에 연결 하 고 SQL 스크립트를 실행 합니다. 이 문서에서는 릴리스 관리 포털을 사용 하는 참고 (http://manage.windowsazure.com), 미리 보기 포털 없습니다 (http://portal.azure.com), 한 Azure SQL server에 대 한 방화벽을 구성 하 합니다.

	**컴퓨터에 설치 된 Visual Studio 2013 있으면:** 에 [Azure 미리 보기 포털](http://portal.azure.com), 클릭 **찾아보기** 허브에서 왼쪽된을 클릭 **SQL 서버**, 데이터베이스를 선택 하 고 클릭 **Visual Studio에서 열려있는** Azure SQL server에 연결 하 고 스크립트를 실행할 도구 모음의 단추입니다. SQL Azure 서버에 액세스 하는 클라이언트에 허용 되지 않으면, Azure SQL server 컴퓨터 (IP 주소)에서 액세스를 허용 하도록 방화벽을 구성 해야 합니다. Azure SQL server에 대 한 방화벽을 구성 하는 단계에 대 한 위의 문서를 참조 하십시오.


다음을 수행합니다.

- 클릭 하 여 [데이터 팩터리 편집기를 사용 하 여](data-factory-get-started-using-editor.md) Azure 포털의 일부인 데이터 팩터리 편집기를 사용 하 여이 자습서를 수행 하려면 맨 위쪽에 링크 합니다.
- 클릭 하 여 [PowerShell을 사용한](data-factory-monitor-manage-using-powershell.md) Azure PowerShell을 사용 하 여이 자습서를 수행 하려면 맨 위쪽에 링크 합니다.


<!--Link references-->
[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[msdn-activities]: https://msdn.microsoft.com/library/dn834988.aspx
[msdn-linkedservices]: https://msdn.microsoft.com/library/dn834986.aspx
[data-factory-naming-rules]: https://msdn.microsoft.com/library/azure/dn835027.aspx

[azure-preview-portal]: https://portal.azure.com/
[download-azure-powershell]: http://azure.microsoft.com/documentation/articles/install-configure-powershell
[sql-management-studio]: http://azure.microsoft.com/documentation/articles/sql-database-manage-azure-ssms/#Step2
[sql-cmd-exe]: https://msdn.microsoft.com/library/azure/ee336280.aspx

[data-factory-editor]: data-factory-editor.md
[monitor-manage-using-powershell]: data-factory-monitor-manage-using-powershell.md
[use-onpremises-datasources]: data-factory-use-onpremises-datasources.md
[adf-tutorial]: data-factory-tutorial.md
[use-custom-activities]: data-factory-use-custom-activities.md
[use-pig-and-hive-with-data-factory]: data-factory-pig-hive-activities.md
[copy-activity]: data-factory-copy-activity.md
[troubleshoot]: data-factory-troubleshoot.md
[data-factory-introduction]: data-factory-introduction.md
[data-factory-create-storage]: http://azure.microsoft.com/documentation/articles/storage-create-storage-account/#create-a-storage-account
[data-factory-create-sql-database]: ../sql-database-create-configure.md


[developer-reference]: http://go.microsoft.com/fwlink/?LinkId=516908
[cmdlet-reference]: http://go.microsoft.com/fwlink/?LinkId=517456

<!--Image references-->

[DataSlicesBySliceTime]: ./media/data-factory-get-started/DataSlicesBySliceTime.png

[image-data-factory-getstarted-new-everything]: ./media/data-factory-get-started/GetStarted-New-Everything.png

[image-data-factory-gallery-storagecachebackup]: ./media/data-factory-get-started/getstarted-gallery-datastoragecachebackup.png

[image-data-factory-gallery-storagecachebackup-seeall]: ./media/data-factory-get-started/getstarted-gallery-datastoragecachebackup-seeall.png

[image-data-factory-getstarted-data-services-data-factory-selected]: ./media/data-factory-get-started/getstarted-data-services-data-factory-selected.png

[image-data-factory-getstarted-data-factory-create-button]: ./media/data-factory-get-started/getstarted-data-factory-create-button.png

[image-data-factory-getstarted-new-data-factory-blade]: ./media/data-factory-get-started/getstarted-new-data-factory.png

[image-data-factory-get-stated-factory-home-page]: ./media/data-factory-get-started/getstarted-data-factory-home-page.png

[image-author-deploy-tile]: ./media/data-factory-get-started/getstarted-author-deploy-tile.png

[image-editor-newdatastore-button]: ./media/data-factory-get-started/getstarted-editor-newdatastore-button.png

[image-editor-blob-storage-json]: ./media/data-factory-get-started/getstarted-editor-blob-storage-json.png

[image-editor-blob-storage-deploy]: ./media/data-factory-get-started/getstarted-editor-blob-storage-deploy.png

[image-editor-azure-sql-settings]: ./media/data-factory-get-started/getstarted-editor-azure-sql-settings.png

[image-editor-newpipeline-button]: ./media/data-factory-get-started/getstarted-editor-newpipeline-button.png

[image-datafactoryblade-diagramtile]: ./media/data-factory-get-started/getstarted-datafactoryblade-diagramtile.png


[image-data-factory-get-started-startboard]: ./media/data-factory-get-started/getstarted-data-factory-startboard.png

[image-data-factory-get-started-linked-services-link]: ./media/data-factory-get-started/getstarted-data-factory-linked-services-link.png

[image-data-factory-get-started-linked-services-add-store-button]: ./media/data-factory-get-started/getstarted-linked-services-add-store-button.png

[image-data-factory-linked-services-get-started-new-data-store]: ./media/data-factory-get-started/getstarted-linked-services-new-data-store.png

[image-data-factory-get-started-new-data-store-with-storage]: ./media/data-factory-get-started/getstarted-linked-services-new-data-store-with-storage.png

[image-data-factory-get-started-storage-account-name-key]: ./media/data-factory-get-started/getstarted-storage-account-name-key.png

[image-data-factory-get-started-linked-services-list-with-myblobstore]: ./media/data-factory-get-started/getstarted-linked-services-list-with-myblobstore.png

[image-data-factory-get-started-linked-azure-sql-properties]: ./media/data-factory-get-started/getstarted-linked-azure-sql-properties.png

[image-data-factory-get-started-azure-sql-connection-string]: ./media/data-factory-get-started/getstarted-azure-sql-connection-string.png

[image-data-factory-get-started-linked-services-list-two-stores]: ./media/data-factory-get-started/getstarted-linked-services-list-two-stores.png

[image-data-factory-get-started-storage-explorer]: ./media/data-factory-get-started/getstarted-storage-explorer.png

[image-data-factory-get-started-diagram-link]: ./media/data-factory-get-started/getstarted-diagram-link.png

[image-data-factory-get-started-diagram-blade]: ./media/data-factory-get-started/getstarted-diagram-blade.png

[image-data-factory-get-started-home-page-pipeline-tables]: ./media/data-factory-get-started/getstarted-datafactory-home-page-pipeline-tables.png

[image-data-factory-get-started-datasets-blade]: ./media/data-factory-get-started/getstarted-datasets-blade.png

[image-data-factory-get-started-table-blade]: ./media/data-factory-get-started/getstarted-table-blade.png

[image-data-factory-get-started-dataslices-blade]: ./media/data-factory-get-started/getstarted-dataslices-blade.png

[image-data-factory-get-started-dataslice-blade]: ./media/data-factory-get-started/getstarted-dataslice-blade.png

[image-data-factory-get-started-sql-query-results]: ./media/data-factory-get-started/getstarted-sql-query-results.png

[image-data-factory-get-started-datasets-emptable-selected]: ./media/data-factory-get-started/DataSetsWithEmpTableFromBlobSelected.png

[image-data-factory-get-started-activity-run-details]: ./media/data-factory-get-started/ActivityRunDetails.png

[image-data-factory-create-resource-group]: ./media/data-factory-get-started/CreateNewResourceGroup.png

[image-data-factory-preview-storage-key]: ./media/data-factory-get-started/PreviewPortalStorageKey.png

[image-data-factory-database-connection-string]: ./media/data-factory-get-started/DatabaseConnectionString.png

[image-data-factory-new-datafactory-menu]: ./media/data-factory-get-started/NewDataFactoryMenu.png

[image-data-factory-sql-management-console]: ./media/data-factory-get-started/getstarted-azure-sql-management-console.png

[image-data-factory-sql-management-console-2]: ./media/data-factory-get-started/getstarted-azure-sql-management-console-2.png

[image-data-factory-name-not-available]: ./media/data-factory-get-started/getstarted-data-factory-not-available.png

<!---HONumber=GIT-SubDir--> 