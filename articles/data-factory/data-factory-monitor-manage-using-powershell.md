<properties 
	pageTitle="자습서: 만들고 Azure PowerShell을 사용 하는 Azure 데이터 팩터리를 모니터링 합니다." 
	description="Azure PowerShell을 사용 하 여 만들고 Azure 데이터 팩터리를 모니터링 하는 방법을 알아봅니다." 
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
	ms.topic="article" 
	ms.date="05/04/2015" 
	ms.author="spelluru"/>

# 자습서: 만들고 Azure PowerShell을 사용 하 여 데이터 팩터리를 모니터링 합니다.
> [AZURE.SELECTOR]
- [Tutorial Overview](data-factory-get-started.md)
- [Using Data Factory Editor](data-factory-get-started-using-editor.md)
- [Using PowerShell](data-factory-monitor-manage-using-powershell.md)

 [Azure 데이터 팩터리 시작][adf-get-started] 만들고 사용 하 여 Azure 데이터 팩터리를 모니터링 하는 방법을 보여주는 자습서는 [Azure 미리 보기 포털][azure-preview-portal]. 이 자습서에서는 만들고 Azure PowerShell cmdlet을 사용 하 여 한 Azure 데이터 팩터리를 모니터링 합니다. Azure에서 데이터를 복사 하는이 자습서에서 만드는 데이터 공장에서 파이프라인을 Azure SQL 데이터베이스에 blob입니다.

> [AZURE.NOTE]이 문서는 모든 데이터 팩터리 cmdlet을 다루지 않습니다. 참조 [데이터 팩터리 Cmdlet 참조][cmdlet-reference] 데이터 팩터리 cmdlet에 대 한 포괄적인 설명서에 대 한 합니다.

##필수 조건
자습서 개요 항목에 나열 된 필수 구성 요소를 외에도 Azure powershell을 컴퓨터에 설치 해야 합니다. 다운로드 및 설치 하지 않은 경우이 이미, [Azure PowerShell][download-azure-powershell] 컴퓨터에 있습니다.

##자습서 내용
다음 표에서 자습서 및 해당 설명의 일부로 수행한 단계를 나열 합니다.

단계 | 설명
-----| -----------
[1 단계: 프로그램 Azure 데이터 팩터리 만들기](#CreateDataFactory) | 이 단계는 Azure 데이터 팩터리 라는 만듭니다 **ADFTutorialDataFactoryPSH**. 
[2 단계: 연결 된 서비스 만들기](#CreateLinkedServices) | 이 단계에서는 두 연결 된 서비스 만듭니다: **StorageLinkedService** 및 **AzureSqlLinkedService**. StorageLinkedService 링크는 Azure 저장소 및 AzureSqlLinkedService는 ADFTutorialDataFactoryPSH를 Azure SQL 데이터베이스를 연결 합니다.
[3 단계: 입력 및 출력 만듭니다 데이터 집합](#CreateInputAndOutputDataSets) | 이 단계에서는 두 데이터 집합을 정의 합니다 (* * EmpTableFromBlob * * 및 **EmpSQLTable**)에 대 한 입력 및 출력 테이블로 사용 되는 **복사 활동** 에서 다음 단계에서 만들 ADFTutorialPipeline 합니다.
[4 단계: 만들고 파이프라인을 실행 합니다.](#CreateAndRunAPipeline) | 이 단계에서는 명명 된 파이프라인을 만듭니다 **ADFTutorialPipeline** 데이터 공장에서: **ADFTutorialDataFactoryPSH**... 파이프라인을 갖습니다는 **복사 활동** 는 Azure에서 데이터를 복사 하는 출력 Azure 데이터베이스 테이블에는 blob입니다.
[5 단계: 데이터 집합 및 파이프라인 모니터링](#MonitorDataSetsAndPipeline) | 이 단계에서는 데이터 집합으로,이 단계에서는 Azure PowerShell을 사용 하 여 파이프라인을 모니터링 합니다.

## <a name="CreateDataFactory"></a>1 단계: 프로그램 Azure 데이터 팩터리 만들기
이 단계에서는 명명 된는 Azure 데이터 팩터리를 만들려면 Azure PowerShell을 사용 **ADFTutorialDataFactoryPSH**.

1. 시작 **Azure PowerShell** 하 고 다음 명령을 실행 합니다. 이 자습서의 끝까지 Azure PowerShell을 열어 두십시오. 닫고 다시 열 경우 이러한 명령을 다시 실행 해야 합니다.
	- 실행 **Add-azureaccount** 사용자 이름 및 Azure 미리 보기 포털에 로그인 하는데 사용할 암호를 입력 합니다.  
	- 실행 **Get-azuresubscription** 이 계정에 대 한 모든 구독을 볼 수 있습니다.
	- 실행 **Select-azuresubscription** 를 사용 하려면 구독을 선택 합니다. 이 구독은 Azure 미리 보기 포털에서 사용한 것과 같아야 합니다. 
2. 전환 **AzureResourceManager** Azure 데이터 팩터리 cmdlet으로 모드는이 모드에서 사용할 수 있습니다.

		Switch-AzureMode AzureResourceManager 
3. 명명 된 Azure 리소스 그룹 만들기: **ADFTutorialResourceGroup** 다음 명령을 실행 하 여 합니다.
   
		New-AzureResourceGroup -Name ADFTutorialResourceGroup  -Location "West US"

	명명 된 리소스 그룹을 사용 하는 것으로 가정이 자습서의 단계 중 일부 **ADFTutorialResourceGroup**. 다른 리소스 그룹을 사용 하는 경우에이 자습서에서 ADFTutorialResourceGroup 대신 사용 해야 합니다. 
4. 실행 하는 **새로 AzureDataFactory** 라는 데이터 팩터리를 만드는 cmdlet: **ADFTutorialDataFactoryPSH**.  

		New-AzureDataFactory -ResourceGroupName ADFTutorialResourceGroup -Name ADFTutorialDataFactoryPSH –Location "West US"


	> [AZURE.NOTE]Azure 데이터 팩터리 이름은 전역적으로 고유해야 합니다. 오류를 수신 하는 경우: **데이터 팩터리 이름을 "ADFTutorialDataFactoryPSH" 사용할 수 없으면**, 이름 (예: yournameADFTutorialDataFactoryPSH)을 변경 합니다. 이 자습서의 단계를 수행 하는 동안 ADFTutorialFactoryPSH 대신이 이름을 사용 합니다.

## <a name="CreateLinkedServices"></a>2 단계: 연결 된 서비스 만들기
연결된 서비스는 데이터 저장소 또는 계산 서비스를 Azure 데이터 팩터리에 연결합니다. 데이터 저장소는 Azure 저장소, Azure SQL 데이터베이스 또는 입력된 데이터를 포함 하거나 데이터 팩터리 파이프라인에 대 한 출력 데이터를 저장 하는 온-프레미스 SQL Server 데이터베이스를 수 있습니다. 계산 서비스는 입력된 데이터를 처리 하 고 출력 데이터를 생성 하는 서비스.

이 단계에서는 두 연결 된 서비스 만듭니다: **StorageLinkedService** 및 **AzureSqlLinkedService**. StorageLinkedService 연결 된 서비스 링크는 Azure 저장소 계정에 대 한 AzureSqlLinkedService 링크는 Azure SQL 데이터베이스 데이터 팩터리: **ADFTutorialDataFactoryPSH**. SQL 테이블에 AzureSqlLinkedService StorageLinkedService blob 컨테이너에서 데이터를 복사 하는이 자습서의 뒷부분에서 파이프라인을 만들게 됩니다.

### Azure 저장소 계정에 대한 연결된 서비스 만들기
1.	라는 JSON 파일을 만듭니다 **StorageLinkedService.json** 에 **C:\ADFGetStartedPSH** 다음 내용을 사용 합니다. 이미 존재 하지 않는 경우에 ADFGetStartedPSH 폴더를 만듭니다.

		{
		    "name": "StorageLinkedService",
		    "properties":
		    {
		        "type": "AzureStorageLinkedService",
		        "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
		    }
		}
2.	에 **Azure PowerShell**, 전환할는 **ADFGetStartedPSH** 폴더입니다. 
3.	사용할 수는 **새로 AzureDataFactoryLinkedService** 연결 된 서비스를 만드는 cmdlet. 이 cmdlet 및 다른 데이터 팩터리 cmdlet이이 자습서에서 사용 하 여 필요에 대 한 값을 전달할 수는 **ResourceGroupName** 및 **DataFactoryName** 매개 변수입니다. 사용할 수 있습니다 **Get AzureDataFactory** 사용할 개체를 가져오고 ResourceGroupName 및 DataFactoryName 매번 입력 하지 않고 개체를 전달 하는 cmdlet을 실행 합니다. 출력을 할당 하려면 다음 명령을 실행 하는 **Get AzureDataFactory** 변수에 cmdlet: **$df**. 

		$df=Get-AzureDataFactory -ResourceGroupName ADFTutorialResourceGroup -Name ADFTutorialDataFactoyPSH

4.	이제 실행 하는 **새로 AzureDataFactoryLinkedService** 연결 된 서비스를 만드는 cmdlet: **StorageLinkedService**.

		New-AzureDataFactoryLinkedService $df -File .\StorageLinkedService.json

	눈치채 실행 하는 경우는 **Get AzureDataFactory** cmdlet 출력을 할당 하 고 **$df** 변수를 하는 것 ResourceGroupName 및 DataFactoryName 매개 변수의 값을 다음과 같이 지정 합니다.
		
		New-AzureDataFactoryLinkedService -ResourceGroupName ADFTutorialResourceGroup -DataFactoryName ADFTutorialDataFactoryPSH -File .\StorageLinkedService.json

	자습서 중에 Azure PowerShell을 닫은 경우 있습니다 됩니다 번 이상 실행 한 Get AzureDataFactory cmdlet 다음 자습서를 완료 하려면 Azure PowerShell을 시작 합니다.

### Azure SQL 데이터베이스에 대한 연결된 서비스 만들기
1.	다음과 같은 내용으로 AzureSqlLinkedService.json 라는 JSON 파일을 만듭니다.

		{
		    "name": "AzureSqlLinkedService",
		    "properties":
		    {
		        "type": "AzureSqlLinkedService",
		        "connectionString": "Server=tcp:<server>.database.windows.net,1433;Database=<databasename>;User ID=user@server;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
		    }
		}
2.	연결 된 서비스를 만들려면 다음 명령을 실행 합니다. 
	
		New-AzureDataFactoryLinkedService $df -File .\AzureSqlLinkedService.json

	> [AZURE.NOTE]확인 하는 **Azure 서비스에 대 한 액세스를 허용** Azure SQL server에 대 한 설정이 켜져 있습니다. 확인 하 고 설정 하려면 다음 절차를 수행 합니다.
	>
	> <ol>
<li>클릭 하 여 <b>찾아보기</b> 를클릭하고 왼쪽에는 허브 <b>SQL 서버</b>.</li>
<li>서버를 선택 하 고 클릭 <b>설정을</b> 에 <b>SQL SERVER</b> 블레이드입니다.</li>
<li>에 <b>설정을</b> 블레이드를 클릭 하 여 <b>방화벽</b>.</li>
<li>에 <b>Firewalll 설정을</b> 블레이드를 클릭 하 여 <b>ON</b> 에 대 한 <b>Azure 서비스에 대 한 액세스를 허용</b>.</li>
<li>클릭 하 여 <b>활성</b> 전환 하려면 왼쪽에는 허브는 <b>데이터 팩터리</b> 에 있던 블레이드입니다.</li>
</ol>

## <a name="CreateInputAndOutputDataSets"></a>3 단계: 입력 및 출력 만듭니다 테이블

연결 된 서비스는 이전 단계에서 만든 **StorageLinkedService** 및 **AzureSqlLinkedService** 는 Azure 저장소 계정 및 Azure SQL 데이터베이스 데이터 팩터리 연결할: **ADFTutorialDataFactoryPSH**. 이 단계에서는 입력을 나타내는 및 다음 단계에서 만들 파이프라인에서 복사 작업에 대 한 데이터를 출력 하는 테이블을 만듭니다.

테이블은 사각형 데이터 집합이며 현재 지원되는 유일한 데이터 집합 유형입니다. 출력 테이블을 가리키는 AzureSqlLinkedService Azure SQL 데이터베이스의 SQL 테이블에 참조 및이 자습서에서는 입력된 테이블 가리키는 해당 StorageLinkedService Azure 저장소에서 blob 컨테이너를 말합니다.

### 자습서에서 사용할 Azure Blob 저장소 및 Azure SQL 데이터베이스 준비
자습서를 통해 수행한 경우이 단계를 건너뛸 [Azure 데이터 팩터리 시작][adf-get-started] 문서입니다.

이 자습서는 Azure blob 저장소 및 Azure SQL 데이터베이스를 준비 하려면 다음 단계를 수행 해야 합니다.
 
* 명명 된 blob 컨테이너 만들기 **adftutorial** Azure에서 blob 저장소는 **StorageLinkedService** 를 가리킵니다. 
* 만들기 및 텍스트 파일을 업로드 **emp.txt**, 에 blob으로는 **adftutorial** 컨테이너입니다. 
* 라는 테이블을 만들어 **emp** SQL Azure에서에서 Azure SQL 데이터베이스에서 데이터베이스를 **AzureSqlLinkedService** 를 가리킵니다.


1. 메모장을 시작 하 고 다음 텍스트를 붙여로 저장 **emp.txt** 를 **C:\ADFGetStartedPSH** 하드 드라이브에 폴더입니다. 

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

	컴퓨터에 설치 된 SQL Server 2014 있으면:에서 지침에 따라 [2 단계: SQL Server Management Studio를 사용 하 여 관리 Azure SQL 데이터베이스의 SQL 데이터베이스에 연결][sql-management-studio] 문서에서는 SQL Azure 서버에 연결 하 고 SQL 스크립트를 실행 합니다.

	컴퓨터에 설치 된 Visual Studio 2013 있으면: Azure 미리 보기 포털에서 ([http://portal.azure.com](http://portal.sazure.com))를 클릭 하 여 **찾아보기** 허브에서 왼쪽된을 클릭 **SQL 서버**, 데이터베이스를 선택 하 고 클릭 **Visual Studio에서 열려있는** Azure SQL server에 연결 하 고 스크립트를 실행할 도구 모음의 단추. SQL Azure 서버에 액세스 하는 클라이언트에 허용 되지 않으면, Azure SQL server 컴퓨터 (IP 주소)에서 액세스를 허용 하도록 방화벽을 구성 해야 합니다. Azure SQL server에 대 한 방화벽을 구성 하는 단계에 대 한 위의 문서를 참조 하십시오.
		
### 입력 테이블 만들기 
테이블은 사각형 데이터 집합이고 스키마가 있습니다. 이 단계에서는 명명 된 테이블을 만듭니다 **EmpBlobTable** 나타내는 Azure 저장소에서 blob 컨테이너를 가리키는 **StorageLinkedService** 서비스를 연결 합니다. 이 blob 컨테이너 (* * adftutorial * *) 파일에 입력된 데이터를 포함: **emp.txt**.

1.	라는 JSON 파일을 만듭니다 **EmpBlobTable.json** 에 **C:\ADFGetStartedPSH** 다음 내용 사용 하 여 폴더:

		{
	    	"name": "EmpTableFromBlob",
	        "properties":
	        {
	            "structure":  
	                [ 
	                { "name": "FirstName", "type": "String"},
	                { "name": "LastName", "type": "String"}
	            ],
	            "location": 
	            {
	                "type": "AzureBlobLocation",
	                "folderPath": "adftutorial/",
	                "format":
	                {
	                    "type": "TextFormat",
	                    "columnDelimiter": ","
	                },
	                "linkedServiceName": "StorageLinkedService"
	            },
	            "availability": 
	            {
	                "frequency": "hour",
	                "interval": 1,
	                "waitonexternal": {}
	                }
	        }
		}
	
	다음 사항에 유의하세요.
	
	- 위치 **유형** 로 설정 된 **AzureBlobLocation**.
	- **linkedServiceName** 로 설정 된 **StorageLinkedService**. 
	- **folderPath** 로 설정 되는 **adftutorial** 컨테이너입니다. 또한 폴더 내의 Blob 이름을 지정할 수도 있습니다. Blob 이름을 지정하지 않으므로 컨테이너에 있는 모든 Blob의 데이터가 입력 데이터로 간주됩니다.  
	- 형식 **유형** 로 설정 된 **TextFormat**
	- – 텍스트 파일에는 두 필드가 **FirstName** 및 **LastName** – 구분 하 여 쉼표 문자 (* * columnDelimiter * *)	
	-  **가용성** 로 설정 된 **매시간** (* * 주파수 * *로 설정 된 **시간** 및 **간격** 로 설정 된 **1** ) 이므로 데이터 팩터리 서비스 데이터를 찾는 입력 매시간 blob 컨테이너에 루트 폴더에 (* * adftutorial * *) 사용자가 지정한 합니다.

	지정 하지 않으면는 **fileName** 에 대 한 프로그램 **입력** **테이블**, 입력된 폴더에서 모든 파일/blob (* * folderPath * *) 입력으로 간주 됩니다. JSON에서 fileName을 지정하는 경우에는 지정한 파일/Blob만 입력으로 간주됩니다. 샘플 파일을 참조는 [자습서][adf-tutorial] 에 대 한 예입니다.
 
	지정 하지 않으면는 **fileName** 에 대 한 프로그램 **출력 테이블**, 생성 된 파일에는 **folderPath** 다음과 같은 형식으로 이름이 지정 됩니다: 데이터. < Guid >.txt (예: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt.).

	**SliceStart** 시간을 기반으로 **folderPath** 및 **fileName**을 설정하려면, **partitionedBy** 속성을 사용합니다. 다음 예제에서 folderPath는 SliceStart(처리 중인 조각의 시작 시간)의 Year, Month 및 Day를 사용하고 fileName은 SliceStart의 Hour를 사용합니다. 예를 들어 조각이 2014-10-20T08:00:00에 생성되는 경우 folderName은 wikidatagateway/wikisampledataout/2014/10/20으로 설정되고 fileName은 08.csv로 설정됩니다.

	  	"folderPath": "wikidatagateway/wikisampledataout/{Year}/{Month}/{Day}",
        "fileName": "{Hour}.csv",
        "partitionedBy": 
        [
        	{ "name": "Year", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyy" } },
            { "name": "Month", "value": { "type": "DateTime", "date": "SliceStart", "format": "MM" } }, 
            { "name": "Day", "value": { "type": "DateTime", "date": "SliceStart", "format": "dd" } }, 
            { "name": "Hour", "value": { "type": "DateTime", "date": "SliceStart", "format": "hh" } } 
        ],

	> [AZURE.NOTE]참조 [JSON 스크립팅 참조](http://go.microsoft.com/fwlink/?LinkId=516971) JSON 속성에 대 한 세부 정보에 대 한 합니다.

2.	데이터 팩터리 테이블을 만들려면 다음 명령을 실행 합니다.

		New-AzureDataFactoryTable $df -File .\EmpBlobTable.json

### 출력 테이블 만들기
이 단계는 부분 이라는 출력 테이블을 만듭니다 **EmpSQLTable** SQL 테이블을 가리키는 (* * emp * *)로 표시 되는 Azure SQL 데이터베이스에는 **AzureSqlLinkedService** 서비스를 연결 합니다. 에 입력된 blob에서 데이터를 복사 하는 파이프라인의 **emp** 테이블입니다.

1.	라는 JSON 파일을 만듭니다 **EmpSQLTable.json** 에 **C:\ADFGetStartedPSH** 다음 내용 사용 하 여 폴더입니다.
		
		{
		    "name": "EmpSQLTable",
		    "properties":
		    {
		        "structure":
		        [
		            { "name": "FirstName", "type": "String"},
		            { "name": "LastName", "type": "String"}
		        ],
		        "location":
		        {
		            "type": "AzureSQLTableLocation",
		            "tableName": "emp",
		            "linkedServiceName": "AzureSqlLinkedService"
		        },
		        "availability": 
		        {
		            "frequency": "Hour",
		            "interval": 1            
		        }
		    }
		}

     다음 사항에 유의하세요.
	
	* 위치 **유형** 로 설정 된 **AzureSQLTableLocation**.
	* **linkedServiceName** 로 설정 된 **AzureSqlLinkedService**.
	* **tablename** 로 설정 된 **emp**.
	* 3 개의 열이 – **ID**, **FirstName**, 및 **LastName** -데이터베이스에 emp 테이블에서 ID 이므로 id 열만 지정 해야 하지만 **FirstName** 및 **LastName** 여기입니다.
	*  **가용성** 로 설정 된 **매시간** (* * 주파수 * *로 설정 합니다. **시간** 및 **간격** 로 설정 **1**). 데이터 팩터리 서비스는 출력 데이터 조각에 1 시간 마다 생성 됩니다는 **emp** Azure SQL 데이터베이스의 테이블입니다.

2.	데이터 팩터리 테이블을 만들려면 다음 명령을 실행 합니다.
	
		New-AzureDataFactoryTable $df -File .\EmpSQLTable.json


## <a name="CreateAndRunAPipeline"></a>4 단계: 만들고 파이프라인을 실행 합니다.
포함 된 파이프라인을 만들면이 단계에서는 **복사 활동** 사용 하 여 **EmpTableFromBlob** 입력으로 및 **EmpSQLTable** 출력으로.

1.	라는 JSON 파일을 만듭니다 **ADFTutorialPipeline.json** 에 **C:\ADFGetStartedPSH** 다음 내용 사용 하 여 폴더: 

		{
		    "name": "ADFTutorialPipeline",
		    "properties":
		    {   
		        "description" : "Copy data from an Azure blob to an Azure SQL table",
		        "activities":   
		        [
		            {
		                "name": "CopyFromBlobToSQL",
		                "description": "Copy data from the adftutorial blob container to emp SQL table",
		                "type": "CopyActivity",
		                "inputs": [ {"name": "EmpTableFromBlob"} ],
		                "outputs": [ {"name": "EmpSQLTable"} ],     
		                "transformation":
		                {
		                    "source":
		                    {                               
		                        "type": "BlobSource"
		                    },
		                    "sink":
		                    {
		                        "type": "SqlSink"
		                    }   
		                },
		                "Policy":
		                {
		                    "concurrency": 1,
		                    "executionPriorityOrder": "NewestFirst",
		                    "style": "StartOfInterval",
		                    "retry": 0,
		                    "timeout": "01:00:00"
		                }       
		            }
		        ],
		        "start": "2015-03-03T00:00:00Z",
		        "end": "2015-03-04T00:00:00Z"
		    }
		}  

	다음 사항에 유의하세요.

	- 작업 섹션에는 활동이 하나만 인 **유형** 로 설정 된 **CopyActivity**.
	- 활동으로 설정 됩니다 입력 **EmpTableFromBlob** 및 활동으로 설정 됩니다 출력 **EmpSQLTable**.
	- 에 **변환** 섹션 **BlobSource** 원본 유형으로 지정 된 및 **SqlSink** 싱크 형식으로 지정 됩니다.

	> [AZURE.NOTE]값을 바꿉니다는 **시작** 은 현재 날짜를 사용 하 여 속성 및 **끝** 다음날을 사용 하 여 값입니다. 모두 시작 및 끝 datetime에 있어야 합니다. [ISO 형식](http://en.wikipedia.org/wiki/ISO_8601). 예: 2014-10-14T16:32:41Z 합니다.  **끝** 시간은 선택적 이지만이 자습서에서 사용할 것입니다. 에 대 한 값을 지정 하지 않으면는 **끝** 로 계산 됩니다 속성을 "* * 시작 + 48 시간 * *"입니다. 파이프라인을 무기한으로 실행 하려면 지정 **9/9/9999** 에 대 한 값으로는 **끝** 속성입니다. 위의 예에서는 각 데이터 조각이 1시간마다 생성되므로 24개 데이터 조각이 있게 됩니다.
	
	> [JSON 스크립팅 참조](http://go.microsoft.com/fwlink/?LinkId=516971)
2.	데이터 팩터리 테이블을 만들려면 다음 명령을 실행 합니다. 
		
		New-AzureDataFactoryPipeline $df -File .\ADFTutorialPipeline.json

**축하합니다.** Azure 데이터 팩터리, 연결된 서비스, 테이블 및 파이프라인을 성공적으로 만들고 파이프라인을 예약했습니다.

## <a name="MonitorDataSetsAndPipeline"></a>5 단계: 데이터 집합 및 파이프라인 모니터링
이 단계에서는 Azure PowerShell는 Azure 데이터 공장에서 무슨 모니터링을 사용 합니다.

1.	실행 **Get AzureDataFactory** 출력 $df 변수에 할당 합니다.

		$df=Get-AzureDataFactory -ResourceGroupName ADFTutorialResourceGroup -Name ADFTutorialDataFactoryPSH
 
2.	실행 **Get AzureDataFactorySlice** 의 모든 조각에 대 한 세부 정보를 얻을 **EmpSQLTable**, 파이프라인의 출력 테이블인 합니다.

		Get-AzureDataFactorySlice $df -TableName EmpSQLTable -StartDateTime 2015-03-03T00:00:00

	> [AZURE.NOTE]연도, 월 및 날짜 부분은 교체는 **시작 날짜 시간** 현재 연도, 월 및 날짜 매개 변수입니다. 이것은 일치 해야는 **시작** JSON 파이프라인의 값입니다.

	다음 날짜의 오전 12 시에는 현재 날짜의 오전 12에서에서 각 시간에 대해 하나씩 24 개의 조각이 표시 되어야 합니다.
	
	**첫번째 조각:**

		ResourceGroupName : ADFTutorialResourceGroup
		DataFactoryName   : ADFTutorialDataFactoryPSH
		TableName         : EmpSQLTable
		Start             : 3/3/2015 12:00:00 AM
		End               : 3/3/2015 1:00:00 AM
		RetryCount        : 0
		Status            : PendingExecution
		LatencyStatus     :
		LongRetryCount    : 0

	**마지막 조각:**

		ResourceGroupName : ADFTutorialResourceGroup
		DataFactoryName   : ADFTutorialDataFactoryPSH
		TableName         : EmpSQLTable
		Start             : 3/4/2015 11:00:00 PM
		End               : 3/4/2015 12:00:00 AM
		RetryCount        : 0
		Status            : PendingExecution
		LatencyStatus     : 
		LongRetryCount    : 0

3.	실행 **Get AzureDataFactoryRun** 활동에 대 한 실행의 세부 정보를 얻을 **특정** 조각입니다. 값을 변경 하는 **시작 날짜 시간** 일치 하도록 매개 변수는 **시작** 위의 출력에서 조각의 시간입니다. 값은 **시작 날짜 시간** 에 있어야 [ISO 형식](http://en.wikipedia.org/wiki/ISO_8601). 예: 2014-03-03T22:00:00Z 합니다.

		Get-AzureDataFactoryRun $df -TableName EmpSQLTable -StartDateTime 2015-03-03T22:00:00

	다음과 비슷한 결과가 나타나야 합니다.

		Id                  : 3404c187-c889-4f88-933b-2a2f5cd84e90_635614488000000000_635614524000000000_EmpSQLTable
		ResourceGroupName   : ADFTutorialResourceGroup
		DataFactoryName     : ADFTutorialDataFactoryPSH
		TableName           : EmpSQLTable
		ProcessingStartTime : 3/3/2015 11:03:28 PM
		ProcessingEndTime   : 3/3/2015 11:04:36 PM
		PercentComplete     : 100
		DataSliceStart      : 3/8/2015 10:00:00 PM
		DataSliceEnd        : 3/8/2015 11:00:00 PM
		Status              : Succeeded
		Timestamp           : 3/8/2015 11:03:28 PM
		RetryAttempt        : 0
		Properties          : {}
		ErrorMessage        :
		ActivityName        : CopyFromBlobToSQL
		PipelineName        : ADFTutorialPipeline
		Type                : Copy

> [AZURE.NOTE]참조 [데이터 팩터리 Cmdlet 참조][cmdlet-reference] 데이터 팩터리 cmdlet에 대 한 포괄적인 설명서에 대 한 합니다.

## 다음 단계

문서 | 설명
------ | ---------------
[Azure 데이터 팩터리 복사 작업을 사용 하 여 데이터를 복사 합니다.][copy-activity] | 이 문서에 대 한 자세한 설명을 제공 된 **복사 활동** 이 자습서에서 사용 합니다. 
[온-프레미스 데이터로 작업 하 여 파이프라인을 사용 하도록 설정][use-onpremises-datasources] | 이 문서에서 데이터를 복사 하는 방법을 보여주는 연습 과정에는 **온-프레미스 SQL Server 데이터베이스** Azure blob에 있습니다. 
[사용 하 여 Pig 및 Hive 데이터 팩터리로][use-pig-and-hive-with-data-factory] | 이 기사에 사용 하는 방법을 보여주는 연습 과정 **HDInsight 활동** 실행 하는 **hive/pig** 출력 데이터를 생성 하는 입력된 데이터를 처리 하는 스크립트입니다.
[이동 하 고 데이터 팩터리를 사용 하 여 로그 파일을 처리 하는 자습서:][adf-tutorial] | 이 문서에서는 프로그램 **-종단간 연습** 구현 하는 방법을 표시 하는 **실제 시나리오** 통찰력에 로그 파일의에서 데이터를 변환할 Azure 데이터 팩터리를 사용 하 여 합니다.
[데이터 공장에서 사용자 지정 활동을 사용 합니다.][use-custom-activities] | 만들기 위한 단계별 지침이 포함 된 연습을 제공 하는이 문서는 **사용자 지정 활동** 파이프라인에서 사용 하 고 있습니다. 
[데이터 팩터리 문제를 해결 합니다.][troubleshoot] | 이 문서에서 설명 하는 방법을 **문제를 해결** Azure 데이터 팩터리를 발급 합니다. Azure SQL 데이터베이스의 테이블을 삭제하는 등 오류를 유발하여 이 문서의 연습을 ADFTutorialDataFactory에 대해 사용해 볼 수 있습니다. 
[Azure 데이터 팩터리 Cmdlet 참조][cmdlet-reference] | 이 참조 콘텐츠는 모든에 대 한 세부 정보는 **데이터 팩터리 cmdlet**.
[Azure 데이터 팩터리 개발자 참조][developer-reference] | 개발자 참조에는 cmdlet, JSON 스크립트, 함수에 대 한 포괄적인 참조 콘텐츠 중... 

[copy-activity]: data-factory-copy-activity.md
[use-onpremises-datasources]: data-factory-use-onpremises-datasources.md
[use-pig-and-hive-with-data-factory]: data-factory-pig-hive-activities.md
[adf-tutorial]: data-factory-tutorial.md
[use-custom-activities]: data-factory-use-custom-activities.md
[troubleshoot]: data-factory-troubleshoot.md
[developer-reference]: http://go.microsoft.com/fwlink/?LinkId=516908

[cmdlet-reference]: https://msdn.microsoft.com/library/dn820234.aspx
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[data-factory-create-storage]: ../storage-create-storage-account.md

[adf-get-started]: data-factory-get-started.md
[azure-preview-portal]: http://portal.azure.com
[download-azure-powershell]: ../powershell-install-configure.md
[data-factory-create-sql-database]: ../sql-database-create-configure.md
[data-factory-introduction]: data-factory-introduction.md

[image-data-factory-get-started-storage-explorer]: ./media/data-factory-monitor-manage-using-powershell/getstarted-storage-explorer.png

[sql-management-studio]: ../sql-database-manage-azure-ssms.md#Step2

<!---HONumber=GIT-SubDir--> 