<properties title="Get started using Azure Data Factory" pageTitle="Azure 데이터 팩터리 시작" description="이 자습서에서는 Blob에서 Azure SQL 데이터베이스 인스턴스로 데이터를 복사하는 샘플 데이터 파이프라인을 만드는 방법을 보여 줍니다." metaKeywords=""  services="data-factory" solutions=""  documentationCenter="" authors="spelluru" manager="jhubbard" editor="monicar" />

<tags ms.service="data-factory" ms.workload="data-services" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="spelluru" />

# Azure 데이터 팩터리 시작
이 문서는 Azure 데이터 팩터리 사용을 시작할 때 도움이 됩니다. 이 문서의 자습서에서는 Azure 데이터 팩터리를 만들고 샘플 데이터를 Azure Blob 저장소에서 Azure SQL 데이터베이스에 복사하는 파이프라인을 데이터 팩터리에 만드는 방법을 보여 줍니다.

다음 목록은 개발자가 수행해야 하는 일반적인 단계입니다. 

1.	**Azure 데이터 팩터리**를 만듭니다.
2.	데이터 저장소 및 계산 서비스를 데이터 팩터리에 연결하는 **연결된 서비스**를 만듭니다.  예를 들어 연결 된 서비스는 Azure SQL 데이터베이스 또는 HDInsight 클러스터를 데이터 팩터리에 연결할 수 있습니다.
3.	파이프라인에 대한 입력 데이터 및 출력 데이터를 설명하는 **테이블**을 만듭니다. 테이블에서는 데이터 팩터리에 연결된 데이터 저장소 내에서 데이터의 실제 위치도 지정합니다. 예를 들어 테이블에서 Azure SQL 데이터베이스에 있는 SQL 테이블의 이름이나 Azure Blob에 있는 Blob 컨테이너의 이름을 지정할 수 있습니다. 
4.	**파이프라인**을 만듭니다. 파이프라인은 입력 데이터를 사용하고 출력 데이터를 생성하는 하나 이상의 작업으로 구성됩니다. 복사 작업은 원본 데이터 저장소에서 대상 데이터 자장소로 데이터를 복사하고 HDInsight 작업은 Hive/Pig 스크립트를 사용하여 입력 데이터를 처리합니다.  
5.	데이터 처리를 위한 파이프라인의 **활성 기간**을 지정합니다. 활성 기간은 데이터 조각이 생성되는 기간을 정의합니다. 파이프라인의 시작 날짜-시간 및 종료 날짜-시간을 지정하거나 파이프라인이 항상 실행되도록 할 수 있습니다.

이 자습서에서는 다음을 수행합니다. 

1.	**Azure 미리 보기 포털**을 사용하여 Azure 데이터 팩터리 및 데이터 저장소에 대한 연결된 서비스를 만듭니다.
2.	**Azure PowerShell**을 사용하여 테이블 및 파이프라인을 만듭니다. 이 릴리스의 포털에서는 테이블 및 파이프라인을 만들 수 없습니다.


##필수 조건
이 자습서를 시작하기 전에 다음이 있어야 합니다.

- Azure 구독. 구독을 얻는 방법에 대한 자세한 내용은 [구매 옵션] [azure-purchase-options], [회원 제안][azure-member-offers] 또는 [무료 평가판l][azure-free-trial]을 참조하세요.
- 컴퓨터에 [Azure PowerShell][download-azure-powershell]을 다운로드하여 설치합니다.
- [Azure 데이터 팩터리 소개][data-factory-introduction] 항목을 꼼꼼히 읽습니다.
- Azure 저장소 계정. 이 자습서에서는 Blob 저장소를 원본 데이터 저장소로 사용합니다.
- Azure SQL 데이터베이스. 이 자습서에서는 샘플 데이터베이스를 만들어 대상 데이터 저장소로 사용합니다.

##자습서 내용

단계 | 설명
-----| -----------
[1단계: Azure 데이터 팩터리 만들기](#CreateDataFactory) | 이 단계에서는 **ADFTutorialDataFactory**라는 Azure 데이터 팩터리를 만듭니다. 
[2단계: 연결된 서비스 만들기](#CreateLinkedServices) | 이 단계에서는 2개의 연결된 서비스 **MyBlobStore** 및 **MyAzureSQLStore**를 만듭니다. MyBlobStore는 Azure 저장소를, MyAzureSQLStore는 Azure SQL 데이터베이스를 ADFTutorialDataFactory에 연결합니다.
[3단계: 입력 및 출력 데이터 집합 만들기](#CreateInputAndOutputDataSets) | 이 단계에서는 2개의 데이터 집합(**EmpTableFromBlob** 및 **EmpSQLTable**)을 정의하여 다음 단계에서 만들 ADFTutorialPipeline에서 **복사 작업**의 입력 및 출력으로 사용합니다.
[4단계: 파이프라인 만들기 및 실행](#CreateAndRunAPipeline) | 이 단계에서는 **ADFTutorialPipeline**이라는 파이프라인을 만듭니다. 이 파이프라인에는 Azure Blob에서 출력 Azure 데이터베이스 테이블로 데이터를 복사하는 **복사 작업**이 있습니다.
[5단계: 데이터 집합 및 파이프라인 모니터링](#MonitorDataSetsAndPipeline) | 이 단계에서는 Azure Management Studio를 사용하여 데이터 집합 및 파이프라인을 모니터링합니다.
 


## <a name="CreateDataFactory"></a>1단계: Azure 데이터 팩터리 만들기
이 단계에서는 Azure 미리 보기 포털을 사용하여 **ADFTutorialDataFactory**라는 Azure 데이터 팩터리를 만듭니다.

1.	[Azure 미리 보기 포털][azure-preview-portal]에 로그인한 후 왼쪽 맨 아래에서 **새로 만들기**를 클릭하고 **새로 만들기** 블레이드에서 **데이터 팩터리**를 클릭합니다. 

	![New->DataFactory][image-data-factory-new-datafactory-menu] 
	
	**새로 만들기** 블레이드에 **데이터 팩터리**가 표시되지 않는 경우 아래로 스크롤합니다.  


6. **새 데이터 팩터리** 블레이드에서 다음을 수행합니다.
	1. **ADFTutorialDataFactory**를 **이름**에 입력합니다. 
	
  		![New data factory blade][image-data-factory-getstarted-new-data-factory-blade]
	2. **리소스 그룹 이름**을 클릭하고 다음을 수행합니다.
		1. **새 리소스 그룹 만들기**를 클릭합니다.
		2. **리소스 그룹 만들기** 블레이드에서 **ADFTutorialResourceGroup**을 리소스 그룹의 **이름**에 입력하고 **확인**을 클릭합니다. 

			![Create Resource Group][image-data-factory-create-resource-group]

		이 자습서의 일부 단계에서는 **ADFTutorialResourceGroup**이라는 리소스 그룹을 사용한다고 가정합니다. 다른 리소스 그룹을 사용하는 경우에는 ADFTutorialResourceGroup 대신 여기에서 선택하는 리소스 그룹을 사용해야 합니다.  
7. **세 데이터 팩터리** 블레이드에서 **Add to Startboard**가 선택되어 있는지 확인합니다.
8. 새 데이터 팩터리** 블레이드에서 **만들기**를 클릭합니다.**
9. 왼쪽의 **알림** 허브를 클릭하고 만들기 프로세스에서 제공하는 알림을 찾습니다.
10. 만들기가 완료되면 데이터 팩터리 블레이드가 아래와 같이 표시됩니다.
    ![Data factory home page][image-data-factory-get-stated-factory-home-page]

11. 아래와 같이 **시작 보드**에서 **ADFTutorialFactory** 를 클릭하여 열 수도 있습니다.

    ![Startboard][image-data-factory-get-started-startboard]    

## <a name="CreateLinkedServices"></a>2단계: 연결된 서비스 만들기
연결된 서비스는 데이터 저장소 또는 계산 서비스를 Azure 데이터 팩터리에 연결합니다. 데이터 저장소는 Azure 저장소, Azure SQL 데이터베이스 또는 온-프레미스 SQL Server 데이터베이스일 수 있습니다.

이 단계에서는 2개의 연결된 서비스 **MyBlobStore** 및 **MyAzureSQLStore**를 만듭니다. MyBlobStore 연결된 서비스는 Azure 저장소 계정을, MyAzureSQLStore는 Azure SQL 데이터베이스를 **ADFTutorialDataFactory**에 연결합니다. 이 자습서 뒷부분에서는 MyBlobStore의 Blob 컨테이너에서 MyAzureSQLStore의 SQL 테이블로 데이터를 복사하는 파이프라인을 만듭니다.

### Azure 저장소 계정에 대한 연결된 서비스 만들기
1.	**데이터 팩터리** 블레이드에서 **연결된 서비스** 타일을 클릭하여 **연결된 서비스** 블레이드를 시작합니다.

    ![Linked services link][image-data-factory-get-started-linked-services-link]

2. **연결된 서비스** 블레이드에서 **데이터 저장소 추가**를 클릭합니다.

    ![Linked services add store][image-data-factory-get-started-linked-services-add-store-button]

3. **새 데이터 저장소** 블레이드에서 다음을 수행합니다.  
	1. 데이터 저장소의 **이름**을 입력합니다. 이 자습서에서는 **MyBlobStore**를 **이름**에 입력합니다.
	2. 데이터 저장소에 대한 **설명**(선택 사항)을 입력합니다.
	3. **유형**을 클릭합니다.
	4. **Azure 저장소 계정**을 선택하고 **확인**을 클릭합니다.
	
    ![New data store button][image-data-factory-linked-services-get-started-new-data-store]
  
4.  이제 아래와 같은 **새 데이터 저장소** 블레이드로 돌아옵니다.

    ![New data store blade][image-data-factory-get-started-new-data-store-with-storage]

5.  Azure 저장소 계정의 **계정 이름** 및 **계정 키**를 입력하고 **확인**을 클릭합니다.   

6. 새 데이터 저장소** 블레이드에서 **확인**을 클릭하면 연결된 서비스**의 **데이터 저장소** 목록에 ****myblobstore****가 표시되어야 합니다. 왼쪽의 **알림** 허브에 메시지가 있는지 확인합니다.

    ![linked services with blob store][image-data-factory-get-started-linked-services-list-with-myblobstore]

### Azure SQL 데이터베이스에 대한 연결된 서비스 만들기
1. **연결된 서비스**의 도구 모음에서 **데이터 저장소 추가**를 클릭하여 다른 데이터 원본(Azure SQL 데이터베이스)을 추가합니다.
2. 새 데이터 저장소 블레이드에서 다음을 수행합니다.
	1. 데이터 저장소의 **이름**을 입력합니다. 이 자습서에서는 **MyAzureSQLStore**를 **이름**에 입력합니다. 
	2. 저장소에 대한 **설명(선택 사항)**을 입력합니다.
	3. **유형**을 클릭하고 **Azure SQL 데이터베이스**를 선택합니다.
3. Azure SQL 데이터베이스에 대한 **서버**, **데이터베이스**, **사용자 이름** 및 **암호**를 입력하고 **확인**을 클릭합니다.

    ![Azure SQL properties][image-data-factory-get-started-linked-azure-sql-properties]

	
4. **새 데이터 저장소** 블레이드에서 **확인**을 클릭하면 **연결된 서비스** 블레이드에 두 저장소가 모두 표시됩니다.

    ![Linked services with two stores][image-data-factory-get-started-linked-services-list-two-stores]
    

## <a name="CreateInputAndOutputDataSets"></a>3단계: 입력 및 출력 테이블 만들기

이전 단계에서는 연결된 서비스 **MyBlobStore** 및 **MyAzureSQLStore**를 만들어 Azure 저장소 계정과 Azure SQL 데이터베이스를 데이터 팩터리 **ADFTutorialDataFactory**에 연결했습니다. 이 단계에서는 다음 단계에서 만들 파이프라인에 있는 복사 작업의 입력 및 출력 데이터를 나타내는 테이블을 만듭니다. 

테이블은 사각형 데이터 집합이며 현재 지원되는 유일한 데이터 집합 유형입니다. 입력 테이블은 MyBlobStore가 가리키는 Azure 저장소의 Blob 컨테이너를 참조하고 출력 테이블은 MyAzureSQLStore가 가리키는 Azure SQL 데이터베이스의 SQL 테이블을 참조합니다.  
 
현재 Azure 미리 보기 포털에서는 데이터 집합과 파이프라인을 만들 수 없으므로 Azure PowerShell cmdlet을 사용하여 테이블과 파이프라인을 만듭니다. 테이블을 만들려면 먼저 다음을 수행해야 합니다(자세한 단계는 목록 다음에 나옴).

* MyBlobStore가 가리키는 Azure Blob 저장소에 **adftutorial**이라는 Blob 컨테이너를 만듭니다. 
* 텍스트 파일 **emp.txt**를 ****adftutorial** 컨테이너의 input** 폴더에 대한 Blob으로 만들고 업로드합니다(**input/emp.txt**). 
* MyAzureSQLStore가 가리키는 Azure SQL 데이터베이스에 **emp**라는 테이블을 만듭니다.
* 하드 드라이브에 **ADFGetStarted**라는 폴더를 만듭니다.  

### 자습서에서 사용할 Azure Blob 저장소 및 Azure SQL 데이터베이스 준비
1. 메모장을 시작하고 다음 텍스트를 붙여 넣은 다음 **emp.txt**로 하드 드라이브의 **C:\ADFGetStarted** 폴더에 저장합니다. 

        John, Doe
		Jane, Doe
				
2. [Azure Storage Explorer](https://azurestorageexplorer.codeplex.com/)와 같은 도구를 사용하여 **adftutorial** 컨테이너를 만들고 **emp.txt** 파일을 이 컨테이너의 **input** 폴더로 업로드합니다(**input/emp.txt**).

    ![Azure Storage Explorer][image-data-factory-get-started-storage-explorer]
3. 다음 SQL 스크립트를 사용하여 **emp** 테이블을 Azure SQL 데이터베이스에 만듭니다. SQL Server Management Studio를 사용하여 Azure SQL 데이터베이스에 연결하고 SQL 스크립트를 실행할 수 있습니다.

        CREATE TABLE dbo.emp 
		(
			ID int IDENTITY(1,1) NOT NULL,
			FirstName varchar(50),
			LastName varchar(50),
		)
		GO

		CREATE CLUSTERED INDEX IX_emp_ID ON dbo.emp (ID); 
		GO
				

### 입력 테이블 만들기 
테이블은 사각형 데이터 집합이고 스키마가 있습니다. 이 단계에서는 **MyBlobStore** 연결된 서비스가 나타내는 Azure 저장소의 Blob 컨테이너를 가리키는 **EmpBlobTable**이라는 테이블을 만듭니다.


1. Blob의 emp.txt에 있는 데이터를 나타내는 데이터 팩터리 테이블에 대한 JSON 파일을 만듭니다. 메모장을 시작하고 다음 JSON 스크립트를 복사하여 C:\ADFGetStarted 폴더에 EmpBlobTable.json으로 저장합니다.


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
            		"folderPath": "adftutorial/input",
            		"format":
            		{
                		"type": "TextFormat",
                		"columnDelimiter": ","
            		},
            		"linkedServiceName": "MyBlobStore"
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
	
	- location **type**은 **AzureBlobLocation**으로 설정합니다.
	- **linkedServiceName**을 **MyBlobStore**로 설정합니다. 이 연결된 서비스는 2단계에서 만들었습니다.
	- **folderPath**를 **adftutorial** 컨테이너의 **input** 폴더로 설정합니다. 또한 폴더 내의 Blob 이름을 지정할 수도 있습니다. Blob 이름을 지정하지 않으므로 컨테이너에 있는 모든 Blob의 데이터가 입력 데이터로 간주됩니다.  
	- format **type**을 **TextFormat**으로 설정합니다.
	- - 텍스트 파일에는 두 개의 필드 **FirstName** 및 **ColumnName**이 쉼표(columDelimiter)로 구분되어 있습니다.	
	- **availability**는 **hourly**로 설정합니다(**frequency**는 **hour**로, **interval**은 **1**로 설정). 데이터 팩터리 서비스는 지정한 Blob 컨테이너(**adftutorial**)의 **input** 폴더에서 입력 데이터를 매시간 찾습니다.

	입력 테이블**의 **fileName**을 지정하지 않는 경우** 입력 폴더(**folderPath**)의 모든 파일/Blob이 입력으로 간주됩니다. JSON에서 fileName을 지정하는 경우에는 지정한 파일/Blob만 입력으로 간주됩니다. 예제는 [자습서][adf-tutorial](영문)의 샘플 파일을 참조하세요.
 
	출력 테이블**의 **fileName**을 지정하지 않는 경우** **folderPath**에 생성되는 파일은 Data.<GUID>.txt 형식으로 이름이 지정됩니다(예: : Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt.).

	**folderPath** 및 **fileName**을 **SliceStart** 시간을 기준으로 동적으로 설정하려면 partitionedBy 속성을 사용합니다. 다음 예제에서 folderPath는 SliceStart(처리 중인 조각의 시작 시간)의 Year, Month 및 Day를 사용하고 fileName은 SliceStart의 Hour를 사용합니다. 예를 들어 조각이 2014-10-20T08:00:00에 생성되는 경우 folderName은 wikidatagateway/wikisampledataout/2014/10/20으로 설정되고 fileName은 08.csv로 설정됩니다. 

	  	"folderPath": "wikidatagateway/wikisampledataout/{Year}/{Month}/{Day}",
        "fileName": "{Hour}.csv",
        "partitionedBy": 
        [
        	{ "name": "Year", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyy" } },
            { "name": "Month", "value": { "type": "DateTime", "date": "SliceStart", "format": "MM" } }, 
            { "name": "Day", "value": { "type": "DateTime", "date": "SliceStart", "format": "dd" } }, 
            { "name": "Hour", "value": { "type": "DateTime", "date": "SliceStart", "format": "hh" } } 
        ],

 

	JSON 속성에 대한 자세한 내용은 [JSON 스크립트 참조](http://go.microsoft.com/fwlink/?LinkId=516971)(영문)를 참조하세요.

2. **Azure PowerShell**을 시작하고 다음 명령을 실행하여 **AzureResourceManager** 모드로 전환합니다. Azure 데이터 팩터리 cmdlet은 **AzureResourceManager** 모드에서 사용할 수 있습니다.

         switch-azuremode AzureResourceManager
		

3. **New-AzureDataFactoryTable** cmdlet을 사용하여 **EmpBlobTable.json** 파일로 입력 테이블을 만듭니다.


         New-AzureDataFactoryTable  -ResourceGroupName ADFTutorialResourceGroup -DataFactoryName ADFTutorialDataFactory -File C:\ADFGetStarted\EmpBlobTable.json 

	이 cmdlet 및 다른 데이터 팩터리 cmdlet에 대한 자세한 내용은 [데이터 팩터리 Cmdlet 참조][cmdlet-reference](영문)를 참조하세요.
 
### 출력 테이블 만들기
이 단계에서는 **MyAzureSQLStore** 연결된 서비스가 나타내는 Azure SQL 데이터베이스의 SQL 테이블을 가리키는 **EmpSQLTable**이라는 출력 테이블을 만듭니다. 

1. Azure SQL 데이터베이스에 있는 데이터를 나타내는 데이터 팩터리에 대한 JSON 파일을 만듭니다. 메모장을 시작하고 다음 JSON 스크립트를 복사하여 **EmpSQLTable.json** 파일로 **C:\ADFGetStarted** 폴더에 저장합니다.



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
            		"linkedServiceName": "MyAzureSQLStore"
        		},
        		"availability": 
        		{
            		"frequency": "Hour",
            		"interval": 1            
        		}
    		}
		}


		
     다음 사항에 유의하세요. 
	
	* location **type**을 **AzureSQLTableLocation**으로 설정합니다.
	* **linkedServiceName**을 **MyAzureSQLStore** (2단계에서 만든 연결된 서비스)로 설정합니다.
	* **tablename**을 **emp**로 설정합니다.
	* 데이터베이스의 emp 테이블에는 **ID**, **FirstName** 및 **LastName**이라는 3개의 열이 있는데, ID는 ID 열이므로 여기서는 **FirstName** 및 **LastName**만 지정합니다.
	* **availability**는 **hourly**로 설정합니다(frequency는 hour, interval은 1로 설정).  데이터 팩터리 서비스는 Azure SQL 데이터베이스의 **emp** 테이블에 출력 데이터 조각을 한 시간마다 생성합니다.


2. **Azure PowerShell**에서 다음 명령을 실행하여 Azure SQL 데이터베이스의 **emp** 테이블을 나타내는 다른 데이터 팩터리 테이블을 만듭니다.



         New-AzureDataFactoryTable -DataFactoryName ADFTutorialDataFactory -File C:\ADFGetStarted\EmpSQLTable.json -ResourceGroupName ADFTutorialResourceGroup 



## <a name="CreateAndRunAPipeline"></a>4단계: 파이프라인 만들기 및 실행
이 단계에서는 EmpTableFromBlob**을 입력으로, **EmpSQLTable**을 출력으로 사용하는**복사 작업**을 포함하는 파이프라인을 만듭니다.**

1. 파이프라인에 대한 JSON 파일을 만듭니다. 메모장을 시작하고 다음 JSON 스크립트를 복사하여 **ADFTutorialPipeline.json** 파일로 **C:\ADFGetStarted** 폴더에 저장합니다.


         {
			"name": "ADFTutorialPipeline",
			"properties":
			{	
				"description" : "Copy data from a blob to Azure SQL table",
				"activities":	
				[
					{
						"name": "CopyFromBlobToSQL",
						"description": "Push Regional Effectiveness Campaign data to Sql Azure",
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
        		]
      		}
		} 

	다음 사항에 유의하세요.

	- activities 섹션에는 **type**이 **CopyActivity**로 설정된 작업 하나밖에 없습니다.
	- 작업에 대한 입력은 **EmpTableFromBlob**로, 작업에 대한 출력은 **EmpSQLTable**로 설정되어 있습니다.
	- **transformation** 섹션에서는 **BlobSource**가 원본 유형으로, **SqlSink**가 싱크 유형으로 지정되어 있습니다.

2. **New-AzureDataFactoryPipeline** cmdlet을 사용하여 앞에서 만든 **ADFTutorialPipeline.json** 파일로 파이프라인을 만듭니다.



         New-AzureDataFactoryPipeline  -ResourceGroupName ADFTutorialResourceGroup -DataFactoryName ADFTutorialDataFactory -File C:\ADFGetStarted\ADFTutorialPipeline.json  

3. 파이프라인이 만들어지면 데이터 처리가 수행되는 기간을 지정할 수 있습니다. 파이프라인의 활성 기간을 지정하면 각 Azure 데이터 팩터리 테이블에 대해 정의된 가용성 속성을 기준으로 데이터 조각이 처리되는 기간이 정의됩니다.  다음 PowerShell 명령을 실행하여 파이프라인에 대한 활성 기간을 설정하고 Y를 입력하여 확인합니다. 



         Set-AzureDataFactoryPipelineActivePeriod -ResourceGroupName ADFTutorialResourceGroup -DataFactoryName ADFTutorialDataFactory -StartDateTime 2014-09-29 -EndDateTime 2014-09-30 -Name ADFTutorialPipeline  

	> [WACOM.NOTE] **StartDateTime** 값을 현재 날짜로, **EndDateTime** 값을 다음 날짜로 바꿉니다. StartDateTime 및 EndDateTime은 UTC 시간이며 [ISO 형식](http://en.wikipedia.org/wiki/ISO_8601)이어야 합니다. 예를 들면 다음과 같습니다. 2014-10-14T16:32:41Z. **EndDateTime**은 선택 사항이지만 이 자습서에서는 사용합니다. 
	> **EndDateTime**을 지정하지 않는 경우 "**StartDateTime + 48시간**"으로 계산됩니다. 파이프라인을 무기한 실행하려면 **9/9/9999**를 **EndDateTime**으로 지정합니다.  
	
	위의 예에서는 각 데이터 조각이 1시간마다 생성되므로 24개 데이터 조각이 있게 됩니다.

4. **Azure 포털**에서 **ADFTutorialDataFactory**에 대한 **데이터 팩터리** 블레이드에서 **다이어그램**을 클릭합니다.

	![Diagram link][image-data-factory-get-started-diagram-link]
  
5. 다음과 유사한 다이어그램이 표시됩니다. (제목을 두 번 클릭하면 블레이드가 나타내는 아티팩트에 대한 자세한 내용이 표시됩니다.)

	![Diagram view][image-data-factory-get-started-diagram-blade]

**축하합니다.** Azure 데이터 팩터리, 연결된 서비스, 테이블 및 파이프라인을 성공적으로 만들고 파이프라인을 예약했습니다.

## <a name="MonitorDataSetsAndPipeline"></a>5단계: 데이터 집합 및 파이프라인 모니터링
이 단계에서는 Azure 포털을 사용하여 Azure 데이터 팩터리에서 어떤 일이 일어나는지 모니터링합니다. 또한 PowerShell cmdlet을 사용하여 데이터 집합과 파이프라인을 모니터링할 수도 있습니다. 모니터링에 cmdlet을 사용하는 방법에 대한 자세한 내용은 [PowerShell Cmdlet을 사용하여 데이터 팩터리 모니터링 및 관리][monitor-manage-using-powershell](영문)를 참조하세요.

1. 아직 열지 않은 경우 [Azure 포털(미리 보기)][azure-preview-portal]로 이동합니다. 
2. **ADFTutorialDataFactory**에 대한 블레이드가 열려 있지 않으면 **시작 보드**에서 **ADFTutorialDataFactory**를 클릭하여 엽니다. 
3. 이 블레이드에서 만든 테이블과 파이프라인의 개수와 이름이 표시됩니다.

	![home page with names][image-data-factory-get-started-home-page-pipeline-tables]

4. 이제 **데이터 집합** 타일을 클릭합니다.
5. **데이터 집합** 블레이드에서 **EmpTableFromBlob**을 클릭합니다. 이 테이블은 **ADFTutorialPipeline**의 입력 테이블입니다.

	![Datasets with EmpTableFromBlob selected][image-data-factory-get-started-datasets-emptable-selected]   
5. 현재 시간까지의 데이터 조각이 이미 생성되어 **준비** 상태에 있습니다. **emp.txt** 파일이 Blob 컨테이너 **adftutorial\input**에 항상 있기 때문입니다. 맨 아래의 **Problem slices** 섹션에 조각이 표시된 조각이 없는지 확인합니다.
6. 이제 **데이터 집합** 블레이드에서 **EmpSQLTable**을 클릭합니다. 이 테이블은 **ADFTutorialPipeline**의 출력 테이블입니다.

	![data sets blade][image-data-factory-get-started-datasets-blade]

6. 아래와 같이 **EmpSQLTable** 블레이드가 표시됩니다.

	![table blade][image-data-factory-get-started-table-blade]
 
7. 현재 시간까지의 데이터 조각이 이미 생성되어 **준비** 상태입니다. 맨 아래의 **Problem slices** 섹션에 표시된 조각이 없습니다.
8. **... (줄임표)**를 클릭하여 조각을 모두 표시합니다.

	![data slices blade][image-data-factory-get-started-dataslices-blade]

9. 목록에서 아무 데이터 조각이나 클릭하면 **데이터 조각** 블레이드가 표시됩니다.

	![data slice blade][image-data-factory-get-started-dataslice-blade]
  
11. **데이터 조각** 블레이드에서 맨 아래 목록에 모든 작업 실행이 표시됩니다. **activity run**을 클릭하여 **ACTIVITY RUN DETAILS** 블레이드를 표시합니다. 

	![Activity Run Details][image-data-factory-get-started-activity-run-details]

12. **X**를 클릭하여 모든 블레이드를 닫아 **ADFTutorialDataFactory**의 홈 블레이드로 돌아옵니다.
14. (선택 사항) ****ADFTutorialDataFactory**의 홈페이지에서 파이프라인**을 클릭하고, ****파이프라인** 블레이드에서 ADFTutorialPipeline**을 클릭하고, 입력 테이블(**Consumed**) 또는 출력 테이블(**Produced**)을 드릴스루합니다.
15. **SQL Server Management Studio**를 시작하고, Azure SQL 데이터베이스에 연결하고, 데이터베이스의 **emp** 테이블이 행이 삽입되었는지 확인합니다.

	![sql query results][image-data-factory-get-started-sql-query-results]


## 요약 
이 자습서에서는 Azure Blob에서 Azure SQL 데이터베이스로 데이터를 복사하는 Azure 데이터 팩터리를 만들었습니다. Azure 미리 보기 포털을 사용하여 데이터 팩터리와 연결된 서비스를 만들었습니다. Azure PowerShell cmdlet을 사용하여 테이블과 파이프라인을 만든 다음 파이프라인을 예약했습니다. 이 자습서에서 수행한 단계를 요약하면 다음과 같습니다.  

1.	Azure **데이터 팩터리**를 만듭니다.
2.	데이터 저장소와 계산(**연결된 서비스**라고 함)을 데이터 팩터리에 연결하는 **연결된 서비스**를 만듭니다.
3.	파이프라인의 입력 데이터와 출력 데이터를 설명하는 **테이블**을 만듭니다.
4.	**파이프라인**을 만듭니다. 파이프라인은 하나 이상의 작업으로 구성되며 입력과 처리하고 출력을 생성합니다. 
5.	데이터 처리를 위한 파이프라인의 **활성 기간**을 지정합니다. 활성 기간은 데이터 조각이 생성되는 기간을 정의합니다.

## 다음 단계

문서 | 설명
------ | ---------------
[Azure 데이터 팩터리를 사용하여 데이터 복사 - 복사 작업][copy-activity] | 이 문서에서는 이 자습서에서 사용한 **복사 작업**에 대해 자세히 설명합니다. 
[파이프라인에서 온-프레미스 데이터를 사용할 수 있도록 설정][use-onpremises-datasources] | 이 문서의 연습에서는 **온-프레미스 SQL Server 데이터베이스**에서 Azure Blob으로 데이터를 복사하는 방법을 보여 줍니다.
[데이터 팩터리에서 Pig 및 Hive 사용][use-pig-and-hive-with-data-factory] | 이 문서의 연습에서는 **HDInsight 작업**을 사용하여 **hive/pig** 스크립트로 입력 데이터를 처리하고 출력 데이터를 생성하는 방법을 보여 줍니다. 
[자습서: 데이터 팩터리를 사용하여 로그 파일 이동 및 처리][adf-tutorial] | 이 문서에서는 Azure 데이터 팩터리를 사용하는 **실제 시나리오**를 구현하여 로그 파일의 데이터에서 통찰력을 얻는 방법을 보여 주는 **완전한 연습**을 제공합니다.
[데이터 팩터리에서 사용자 지정 작업 사용][use-custom-activities] | 이 문서에서는 **사용자 지정 작업**을 만들어 파이프라인에서 사용하는 방법에 대한 단계별 지침이 포함된 연습을 제공합니다. 
[PowerShell을 사용하여 Azure 데이터 팩터리 모니터링 및 관리][monitor-manage-using-powershell] | 이 문서에서는 **Azure PowerShell cmdlet을 사용하여 Azure 데이터 팩터리를 ****모니터링 및 관리**하는 방법을 설명합니다. 문서에 있는 예제를 ADFTutorialDataFactory에 대해 사용해 볼 수 있습니다.
[데이터 팩터리 문제 해결][troubleshoot] | 이 문서에서는 Azure 데이터 팩터리 문제를 **해결**하는 방법에 대해 설명합니다. Azure SQL 데이터베이스의 테이블을 삭제하는 등 오류를 유발하여 이 문서의 연습을 ADFTutorialDataFactory에 대해 사용해 볼 수 있습니다. 
[Azure 데이터 팩터리 Cmdlet 참조][cmdlet-reference] | 이 참조 콘텐츠에는 모든 **데이터 팩터리 cmdlet**에 대한 내용이 자세히 나와 있습니다.
[Azure 데이터 팩터리 개발자 참조][developer-reference] | 개발\자 참조에는 cmdlet, JSON 스크립트, 함수 등에 대한 포괄적인 참조 콘텐츠가 포함되어 있습니다. 


<!--Link references-->
[azure-purchase-options]: http://azure.microsoft.com/ko-kr/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/ko-kr/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/ko-kr/pricing/free-trial/

[azure-preview-portal]: https://portal.azure.com/
[download-azure-powershell]: http://azure.microsoft.com/ko-kr/documentation/articles/install-configure-powershell

[monitor-manage-using-powershell]: ../data-factory-monitor-manage-using-powershell
[use-onpremises-datasources]: ../data-factory-use-onpremises-datasources
[adf-tutorial]: ../data-factory-tutorial
[use-custom-activities]: ../data-factory-use-custom-activities
[use-pig-and-hive-with-data-factory]: ../data-factory-pig-hive-activities
[copy-activity]: ../data-factory-copy-activity/
[troubleshoot]: ../data-factory-troubleshoot
[data-factory-introduction]: ../data-factory-introduction


[developer-reference]: http://go.microsoft.com/fwlink/?LinkId=516908
[cmdlet-reference]: http://go.microsoft.com/fwlink/?LinkId=517456

<!--Image references-->

[image-data-factory-getstarted-new-everything]: ./media/data-factory-get-started/GetStarted-New-Everything.png

[image-data-factory-gallery-storagecachebackup]: ./media/data-factory-get-started/getstarted-gallery-datastoragecachebackup.png

[image-data-factory-gallery-storagecachebackup-seeall]: ./media/data-factory-get-started/getstarted-gallery-datastoragecachebackup-seeall.png

[image-data-factory-getstarted-data-services-data-factory-selected]: ./media/data-factory-get-started/getstarted-data-services-data-factory-selected.png

[image-data-factory-getstarted-data-factory-create-button]: ./media/data-factory-get-started/getstarted-data-factory-create-button.png

[image-data-factory-getstarted-new-data-factory-blade]: ./media/data-factory-get-started/getstarted-new-data-factory.png

[image-data-factory-get-stated-factory-home-page]: ./media/data-factory-get-started/getstarted-data-factory-home-page.png

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
