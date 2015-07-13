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

이 문서의 자습서는 Azure 데이터 팩터리 서비스 사용을 빠르게 시작하는 데 도움이 됩니다. 이 자습서에서는 Azure 데이터 팩터리를 만들고 Azure Blob 저장소에서 Azure SQL 데이터베이스로 데이터를 복사하는 파이프라인을 데이터 팩터리에 만듭니다.

> [AZURE.NOTE]데이터 팩터리 서비스에 대한 자세한 개요는 [Azure 데이터 팩터리 소개][data-factory-introduction] 문서를 참조하세요.

##자습서의 필수 조건
이 자습서를 시작하기 전에 다음이 있어야 합니다.

- **Azure 구독**. 구독이 없는 경우 몇 분 만에 무료 평가판 계정을 만들 수 있습니다. 자세한 내용은 [무료 평가판][azure-free-trial] 문서를 참조하세요.
- **Azure 저장소 계정**. 이 자습서에서는 Blob 저장소를 **원본** 데이터 저장소로 사용합니다. Azure 저장소 계정이 없는 경우 새로 만드는 단계는 [저장소 계정 만들기][data-factory-create-storage] 문서를 참조하세요.
- **Azure SQL 데이터베이스**. 이 자습서에서는 Azure SQL 데이터베이스를 **대상** 데이터 저장소로 사용합니다. 자습서에서 사용할 수 있는 Azure SQL 데이터베이스가 없는 경우 [Azure SQL 데이터베이스를 만들고 구성하는 방법][data-factory-create-sql-database]을 참조하여 새로 만드세요.
- **SQL Server 2012/2014 또는 Visual Studio 2013**. SQL Server Management Studio 또는 Visual Studio를 사용하여 샘플 데이터베이스를 만들고 데이터베이스에서 결과 데이터를 확인합니다.  

### Azure 저장소 계정의 계정 이름과 계정 키를 수집합니다.
이 자습서를 수행하려면 Azure 저장소 계정의 계정 이름과 계정 키가 필요합니다. 아래 지침에 따라 Azure 저장소 계정의 **계정 이름**과 **계정 키**를 적어둡니다.

1. [Azure Preview 포털][azure-preview-portal]에 로그인합니다.
2. 왼쪽의 **찾아보기** 허브를 클릭하고 **저장소 계정**을 선택합니다.
3. **저장소 계정** 블레이드에서, 이 자습서에서 사용하려는 **Azure 저장소 계정**을 선택합니다.
4. **저장소** 블레이드에서 **키** 타일을 클릭합니다.
5. **키 관리** 블레이드에서 **저장소 계정 이름** 텍스트 상자 옆에 있는 **복사**(이미지) 단추를 클릭하고 텍스트 파일 등에 저장/붙여넣습니다.  
6. 이전 단계를 반복하여 **기본 액세스 키**를 복사하거나 적어둡니다.
7. **X**를 클릭하여 모든 블레이드를 닫습니다.

### Azure SQL 데이터베이스의 서버 이름, 데이터베이스 이름 및 사용자 계정을 수집합니다.
이 자습서를 수행하려면 Azure SQL Server, 데이터베이스 및 사용자의 이름이 필요합니다. 아래 지침에 따라 Azure SQL 데이터베이스의 **서버**, **데이터베이스** 및 **사용자**의 이름을 적어둡니다.

1. **Azure Preview 포털**에서 왼쪽의 **찾아보기**를 클릭하고 **SQL 데이터베이스**를 선택합니다.
2. **SQL 데이터베이스 블레이드**에서, 이 자습서에서 사용하려는 **데이터베이스**를 선택합니다. **데이터베이스 이름**을 적어둡니다.  
3. **SQL 데이터베이스** 블레이드에서 **속성** 타일을 클릭합니다.
4. **서버 이름** 및 **서버 관리자 로그인**의 값을 적어둡니다.
5. **X**를 클릭하여 모든 블레이드를 닫습니다.

### Azure 서비스가 Azure SQL Server에 액세스하도록 허용
데이터 팩터리 서비스가 Azure SQL Server에 액세스할 수 있도록 Azure SQL Server에 대해 **Azure 서비스에 대한 액세스 허용** 설정이 **ON**인지 확인합니다. 이 설정을 확인하고 켜려면 다음을 수행합니다.

1. 왼쪽의 **찾아보기** 허브를 클릭하고 **SQL Server**를 클릭합니다.
2. **서버**를 선택하고 **SQL SERVER** 블레이드에서 **설정**을 클릭합니다.
3. **설정** 블레이드에서 **방화벽**을 클릭합니다.
4. **방화벽 설정** 블레이드에서 **Azure 서비스에 대한 액세스 허용**에 대해 **ON**을 클릭합니다.
5. **X**를 클릭하여 모든 블레이드를 닫습니다.

### 자습서에서 사용할 Azure Blob 저장소 및 Azure SQL 데이터베이스 준비
이제 다음 단계를 수행하여 자습서에서 사용할 Azure Blob 저장소 및 Azure SQL 데이터베이스를 준비합니다.

1. 메모장을 시작하고 다음 텍스트를 붙여 넣은 다음 **emp.txt**로 하드 드라이브의 **C:\ADFGetStarted** 폴더에 저장합니다.

        John, Doe
		Jane, Doe

2. [Azure 저장소 탐색기](https://azurestorageexplorer.codeplex.com/)와 같은 도구를 사용하여 **adftutorial** 컨테이너를 만들고 **emp.txt** 파일을 이 컨테이너에 업로드합니다.

    ![Azure 저장소 탐색기][image-data-factory-get-started-storage-explorer]
3. 다음 SQL 스크립트를 사용하여 **emp** 테이블을 Azure SQL 데이터베이스에 만듭니다.  


        CREATE TABLE dbo.emp
		(
			ID int IDENTITY(1,1) NOT NULL,
			FirstName varchar(50),
			LastName varchar(50),
		)
		GO

		CREATE CLUSTERED INDEX IX_emp_ID ON dbo.emp (ID);

	**SQL Server 2012/2014가 컴퓨터에 설치된 경우:** [2단계: SQL Server Management Studio를 사용하여 Azure SQL 데이터베이스 관리의 SQL 데이터베이스에 연결][sql-management-studio] 문서의 지침에 따라 Azure SQL Server에 연결하고 SQL 스크립트를 실행합니다. 이 문서에서는 릴리스 관리 포털(http://manage.windowsazure.com), Preview 포털 http://portal.azure.com) 아님)을 사용하여 Azure SQL Server의 방화벽을 구성합니다.

	**Visual Studio 2013이 컴퓨터에 설치된 경우:** [Azure Preview 포털](http://portal.azure.com)에서 왼쪽의 **찾아보기** 허브를 클릭하고 **SQL Server**를 클릭한 다음 데이터베이스를 선택하고 도구 모음의 **Visual Studio에서 열기** 단추를 클릭하여 Azure SQL Server에 연결한 다음 스크립트를 실행합니다. 클라이언트가 Azure SQL Server에 액세스할 수 없는 경우 컴퓨터(IP 주소)의 액세스를 허용하도록 Azure SQL Server의 방화벽을 구성해야 합니다. Azure SQL Server의 방화벽을 구성하는 단계는 위의 문서를 참조하세요.


다음을 수행합니다.

- 맨 위의 [데이터 팩터리 편집기 사용](data-factory-get-started-using-editor.md) 링크를 클릭하여 Azure 포털의 일부인 데이터 팩터리 편집기를 통해 자습서를 수행합니다.
- 맨 위의 [PowerShell 사용](data-factory-monitor-manage-using-powershell.md) 링크를 클릭하여 Azure PowerShell을 통해 자습서를 수행합니다.


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
 

<!---HONumber=62-->