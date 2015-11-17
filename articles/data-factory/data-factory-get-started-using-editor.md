<properties 
	pageTitle="자습서: 데이터 팩터리 편집기를 사용하여 복사 작업이 있는 파이프라인 만들기" 
	description="이 자습서에서는 Azure 포털의 데이터 팩터리 편집기를 사용하여 복사 작업이 있는 Azure Data Factory 파이프라인을 만듭니다." 
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
	ms.topic="get-started-article" 
	ms.date="11/02/2015" 
	ms.author="spelluru"/>

# 자습서: 데이터 팩터리 편집기를 사용하여 복사 작업이 있는 파이프라인 만들기
> [AZURE.SELECTOR]
- [Tutorial Overview](data-factory-get-started.md)
- [Using Data Factory Editor](data-factory-get-started-using-editor.md)
- [Using PowerShell](data-factory-monitor-manage-using-powershell.md)
- [Using Visual Studio](data-factory-get-started-using-vs.md)



##자습서 내용
이 자습서에는 다음 단계가 포함되어 있습니다.

단계 | 설명
-----| -----------
[1단계: Azure Data Factory 만들기](#CreateDataFactory) | 이 단계에서는 **ADFTutorialDataFactory**라는 Azure Data Factory를 만듭니다.  
[2단계: 연결된 서비스 만들기](#CreateLinkedServices) | 이 단계에서는 2개의 연결된 서비스 **StorageLinkedService** 및 **AzureSqlLinkedService**를 만듭니다. StorageLinkedService는 Azure 저장소를 연결하고, AzureSqlLinkedService는 Azure SQL 데이터베이스를 ADFTutorialDataFactory에 연결합니다. 파이프라인에 대한 입력 데이터는 Azure Blob 저장소의 Blob 컨테이너에 있고, 출력 데이터는 Azure SQL 데이터베이스의 테이블에 저장됩니다. 따라서 이러한 두 데이터 저장소를 연결된 서비스로 데이터 팩터리에 추가합니다.      
[3단계: 입력 및 출력 테이블 만들기](#CreateInputAndOutputDataSets) | 이전 단계에서는 입출력 데이터가 포함된 데이터 저장소를 참조하는 연결된 서비스를 만들었습니다. 이 단계에서는 데이터 저장소에 저장된 입출력 데이터를 나타내는 2개의 데이터 팩터리 테이블 **EmpTableFromBlob** 및 **EmpSQLTable**을 정의합니다. EmpTableFromBlob에 대해 원본 데이터가 있는 Blob을 포함하는 Blob 컨테이너를 지정하고, EmpSQLTable에 대해 출력 데이터를 저장할 SQL 테이블을 지정합니다. 데이터 구조, 데이터 가용성 등의 기타 속성도 지정합니다. 
[4단계: 파이프라인 만들기 및 실행](#CreateAndRunAPipeline) | 이 단계에서는 ADFTutorialDataFactory에 **ADFTutorialPipeline**이라는 파이프라인을 만듭니다. 이 파이프라인에는 Azure Blob에서 출력 Azure SQL 테이블로 입력 데이터를 복사하는 **복사 작업**이 있습니다.
[5단계: 조각 및 파이프라인 모니터링](#MonitorDataSetsAndPipeline) | 이 단계에서는 Azure Preview 포털을 사용하여 입력 및 출력 테이블의 조각을 모니터링합니다.
 

## <a name="CreateDataFactory"></a>1단계: Azure Data Factory 만들기
이 단계에서는 Azure Preview 포털을 사용하여 **ADFTutorialDataFactory**라는 Azure Data Factory를 만듭니다.

1.	[Azure Preview 포털][azure-preview-portal]에 로그인한 후 왼쪽 아래에서 **새로 만들기**를 클릭하고 **만들기** 블레이드에서 **데이터 분석**을 선택한 다음 **데이터 분석** 블레이드에서 **데이터 팩터리**를 클릭합니다. 

	![새로 만들기->DataFactory][image-data-factory-new-datafactory-menu]

6. **새 데이터 팩터리** 블레이드에서 다음을 수행합니다.
	1. **ADFTutorialDataFactory**를 **이름**으로 입력합니다. 
	
  		![새 데이터 팩터리 블레이드][image-data-factory-getstarted-new-data-factory-blade]
	2. **리소스 그룹 이름**을 클릭하고 다음을 수행합니다.
		1. **새 리소스 그룹 만들기**를 클릭합니다.
		2. **리소스 그룹 만들기** 블레이드에서 리소스 그룹의 **이름**으로 **ADFTutorialResourceGroup**을 입력하고 **확인**을 클릭합니다. 

			![리소스 그룹 만들기][image-data-factory-create-resource-group]

		이 자습서의 일부 단계에서는 리소스 그룹에 **ADFTutorialResourceGroup**이라는 이름을 사용한다고 가정합니다. 리소스 그룹에 대한 자세한 내용은 [리소스 그룹을 사용하여 Azure 리소스 관리](resource-group-overview.md)를 참조하세요.  
7. **새 데이터 팩터리** 블레이드에서 **시작 보드에 추가**가 선택되어 있는지 확인합니다.
8. **새 데이터 팩터리** 블레이드에서 **만들기**를 클릭합니다.

	Azure Data Factory 이름은 전역적으로 고유해야 합니다. **데이터 팩터리 이름 “ADFTutorialDataFactory”를 사용할 수 없습니다.** 오류가 표시되는 경우 데이터 팩터리 이름을 변경하고(예: yournameADFTutorialDataFactory) 다시 만듭니다. 데이터 팩터리 아티팩트에 대한 명명 규칙은 [데이터 팩터리 - 명명 규칙](data-factory-naming-rules.md) 항목을 참조하세요.
	 
	![데이터 팩터리 이름을 사용할 수 없음][image-data-factory-name-not-available]
	
	> [AZURE.NOTE]데이터 팩터리의 이름은 나중에 DNS 이름으로 표시되므로 공개적으로 등록될 수도 있습니다.

9. 왼쪽의 **알림** 허브를 클릭하고 만들기 프로세스에서 제공하는 알림을 찾습니다. **알림** 블레이드가 열려 있으면 **X**를 클릭하여 닫습니다.
10. 만들기가 완료되면 아래와 같이 **데이터 팩터리** 블레이드가 표시됩니다.

    ![데이터 팩터리 홈페이지][image-data-factory-get-stated-factory-home-page]

## <a name="CreateLinkedServices"></a>2단계: 연결된 서비스 만들기
연결된 서비스는 데이터 저장소 또는 계산 서비스를 Azure Data Factory에 연결합니다. 데이터 저장소는 Azure 저장소, Azure SQL 데이터베이스 또는 온-프레미스 SQL Server 데이터베이스일 수 있습니다.

이 단계에서는 2개의 연결된 서비스 **StorageLinkedService** 및 **AzureSqlLinkedService**를 만듭니다. StorageLinkedService 연결된 서비스는 Azure 저장소 계정을 연결하고, AzureSqlLinkedService는 Azure SQL 데이터베이스를 **ADFTutorialDataFactory**에 연결합니다. 이 자습서 뒷부분에서는 StorageLinkedService의 Blob 컨테이너에서 AzureSqlLinkedService의 SQL 테이블로 데이터를 복사하는 파이프라인을 만듭니다.

### Azure 저장소 계정에 대한 연결된 서비스 만들기
1.	**데이터 팩터리** 블레이드에서 **작성자 및 배포** 타일을 클릭하여 데이터 팩터리에 대한 **편집기**를 시작합니다.

	![작성 및 배포 타일][image-author-deploy-tile]

	데이터 팩터리 편집기에 대한 자세한 개요는 [데이터 팩터리 편집기][data-factory-editor] 토픽을 참조하세요.
	 
5. **편집기**의 도구 모음에서 **새 데이터 저장소** 단추를 클릭하고 드롭다운 메뉴에서 **Azure 저장소**를 선택합니다. 오른쪽 창에 Azure 저장소 연결된 서비스를 만들기 위한 JSON 템플릿이 표시됩니다.

	![편집기 새 데이터 저장소 단추][image-editor-newdatastore-button]
    
6. **accountname** 및 **accountkey**를 Azure 저장소 계정의 계정 이름 및 계정 키 값으로 바꿉니다.

	![편집기 Blob 저장소 JSON][image-editor-blob-storage-json]
	
	JSON 속성에 대한 자세한 내용은 [JSON 스크립트 참조](http://go.microsoft.com/fwlink/?LinkId=516971)를 참조하세요.

6. 도구 모음에서 **배포**를 클릭하여 StorageLinkedService를 배포합니다. 제목 표시줄에 **연결된 서비스가 성공적으로 생성됨** 메시지가 표시되는지 확인합니다.

	![편집기 Blob 저장소 배포][image-editor-blob-storage-deploy]

### Azure SQL 데이터베이스에 대한 연결된 서비스 만들기
1. **데이터 팩터리 편집기**의 도구 모음에서 **새 데이터 저장소** 단추를 클릭하고 드롭다운 메뉴에서 **Azure SQL 데이터베이스**를 선택합니다. 오른쪽 창에 Azure SQL 연결된 서비스를 만들기 위한 JSON 템플릿이 표시됩니다.

	![편집기 Azure SQL 설정][image-editor-azure-sql-settings]

2. **servername**, **databasename**, ****username@servername** 및 **password**를 Azure SQL Server, 데이터베이스, 사용자 계정 및 암호로 바꿉니다.
3. 도구 모음에서 **배포**를 클릭하여 AzureSqlLinkedService를 만들고 배포합니다. 
   

## <a name="CreateInputAndOutputDataSets"></a>3단계: 입력 및 출력 테이블 만들기
이전 단계에서는 연결된 서비스 **StorageLinkedService** 및 **AzureSqlLinkedService**를 만들어 Azure 저장소 계정과 Azure SQL 데이터베이스를 데이터 팩터리 **ADFTutorialDataFactory**에 연결했습니다. 이 단계에서는 각각 StorageLinkedService 및 AzureSqlLinkedService로 참조되는 데이터 저장소에 저장된 입출력 데이터를 나타내는 2개의 데이터 팩터리 테이블 **EmpTableFromBlob** 및 **EmpSQLTable**을 정의합니다. EmpTableFromBlob에 대해 원본 데이터가 있는 Blob을 포함하는 Blob 컨테이너를 지정하고, EmpSQLTable에 대해 출력 데이터를 저장할 SQL 테이블을 지정합니다.

### 입력 테이블 만들기 
테이블은 사각형 데이터 집합이고 스키마가 있습니다. 이 단계에서는 **StorageLinkedService** 연결된 서비스가 나타내는 Azure 저장소의 Blob 컨테이너를 가리키는 **EmpBlobTable**이라는 테이블을 만듭니다.

1. 데이터 팩터리에 대한 **편집기**의 도구 모음에서 **새 데이터 집합** 단추를 클릭하고 드롭다운 메뉴에서 **Blob 테이블**을 클릭합니다. 
2. 오른쪽 창의 JSON을 다음 JSON 조각으로 바꿉니다. 

		{
		  "name": "EmpTableFromBlob",
		  "properties": {
		    "structure": [
		      {
		        "name": "FirstName",
		        "type": "String"
		      },
		      {
		        "name": "LastName",
		        "type": "String"
		      }
		    ],
		    "type": "AzureBlob",
		    "linkedServiceName": "StorageLinkedService",
		    "typeProperties": {
		      "folderPath": "adftutorial/",
			  "fileName": "emp.txt",
		      "format": {
		        "type": "TextFormat",
		        "columnDelimiter": ","
		      }
		    },
		    "external": true,
		    "availability": {
		      "frequency": "Hour",
		      "interval": 1
		    }
		  }
		}

		
     다음 사항에 유의하세요.
	
	- dataset **type**을 **AzureBlob**으로 설정합니다.
	- **linkedServiceName**을 **StorageLinkedService**로 설정합니다. 이 연결된 서비스는 2단계에서 만들었습니다.
	- **folderPath**를 **adftutorial** 컨테이너로 설정합니다. 또한 폴더 내의 Blob 이름을 지정할 수도 있습니다. Blob 이름을 지정하지 않으므로 컨테이너에 있는 모든 Blob의 데이터가 입력 데이터로 간주됩니다.  
	- format **type**을 **TextFormat**으로 설정합니다.
	- 텍스트 파일에는 **FirstName**과 **LastName**의 두 필드가 쉼표(**columnDelimiter**)로 구분되어 있습니다.	
	- **availability**를 **hourly**로 설정하므로(**frequency**를 **hour**로 설정하고 **interval**을 **1**로 설정함), 데이터 팩터리 서비스가 지정한 Blob 컨테이너(**adftutorial**)의 루트 폴더에서 입력 데이터를 1시간마다 찾습니다. 
	

	**입력** **테이블**의 **fileName**을 지정하지 않는 경우 입력 폴더(**folderPath**)의 모든 파일/Blob이 입력으로 간주됩니다. JSON에서 fileName을 지정하는 경우에는 지정한 파일/Blob만 입력으로 간주됩니다. 예제는 [자습서][adf-tutorial]의 샘플 파일을 참조하세요.
 
	**출력 테이블**의 **fileName**을 지정하지 않는 경우, **folderPath**에 생성되는 파일의 이름은 다음과 같은 형식으로 지정됩니다. Data.&lt;Guid&gt;.txt(예: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt).

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

	JSON 속성에 대한 자세한 내용은 [JSON 스크립트 참조](http://go.microsoft.com/fwlink/?LinkId=516971)를 참조하세요.

2. 도구 모음에서 **배포**를 클릭하여 **EmpTableFromBlob** 테이블을 만들고 배포합니다. 편집기의 제목 표시줄에 **테이블이 성공적으로 생성됨** 메시지가 표시되는지 확인합니다.

### 출력 테이블 만들기
이 단계에서는 **AzureSqlLinkedService** 연결된 서비스가 나타내는 Azure SQL 데이터베이스의 SQL 테이블을 가리키는 **EmpSQLTable**이라는 출력 테이블을 만듭니다.

1. 데이터 팩터리에 대한 **편집기**의 도구 모음에서 **새 데이터 집합** 단추를 클릭하고 드롭다운 메뉴에서 **Azure SQL 테이블**을 클릭합니다. 
2. 오른쪽 창의 JSON을 다음 JSON 조각으로 바꿉니다.

		{
		  "name": "EmpSQLTable",
		  "properties": {
		    "structure": [
		      {
		        "name": "FirstName",
		        "type": "String"
		      },
		      {
		        "name": "LastName",
		        "type": "String"
		      }
		    ],
		    "type": "AzureSqlTable",
		    "linkedServiceName": "AzureSqlLinkedService",
		    "typeProperties": {
		      "tableName": "emp"
		    },
		    "availability": {
		      "frequency": "Hour",
		      "interval": 1
		    }
		  }
		}

		
     다음 사항에 유의하세요.
	
	* dataset **type**을 **AzureSQLTable**로 설정합니다.
	* **linkedServiceName**을 **AzureSqlLinkedService**(2단계에서 만든 연결된 서비스)로 설정합니다.
	* **tablename**을 **emp**로 설정합니다.
	* 데이터베이스의 emp 테이블에는 세 개의 열 **ID**, **FirstName** 및 **LastName**이 있지만 ID는 ID 열이므로 여기서는 **FirstName**과 **LastName**만 지정하면 됩니다.
	* **availability**는 **hourly**(**frequency**는 **hour**로, **interval**은 **1**로 설정)로 설정됩니다. 데이터 팩터리 서비스는 Azure SQL 데이터베이스의 **emp** 테이블에 출력 데이터 조각을 1시간마다 생성합니다.


3. 도구 모음에서 **배포**를 클릭하여 **EmpSQLTable** 테이블을 만들고 배포합니다.


## <a name="CreateAndRunAPipeline"></a>4단계: 파이프라인 만들기 및 실행
이 단계에서는 **EmpTableFromBlob**을 입력으로 사용하고 **EmpSQLTable**을 출력으로 사용하는 **복사 작업**을 포함하는 파이프라인을 만듭니다.

1. 데이터 팩터리에 대한 **편집기**의 도구 모음에서 **새 파이프라인** 단추를 클릭합니다. 단추가 표시되지 않는 경우 도구 모음에서 **... (줄임표)**을 클릭합니다. 또는 트리 뷰에서 **파이프라인**을 마우스 오른쪽 단추로 클릭하고 **새 파이프라인**을 클릭할 수 있습니다.

	![편집기 새 파이프라인 단추][image-editor-newpipeline-button]
 
2. 오른쪽 창의 JSON을 다음 JSON 조각으로 바꿉니다.
		
		{
		  "name": "ADFTutorialPipeline",
		  "properties": {
		    "description": "Copy data from a blob to Azure SQL table",
		    "activities": [
		      {
		        "name": "CopyFromBlobToSQL",
		        "description": "Push Regional Effectiveness Campaign data to Azure SQL database",
		        "type": "Copy",
		        "inputs": [
		          {
		            "name": "EmpTableFromBlob"
		          }
		        ],
		        "outputs": [
		          {
		            "name": "EmpSQLTable"
		          }
		        ],
		        "typeProperties": {
		          "source": {
		            "type": "BlobSource"
		          },
		          "sink": {
		            "type": "SqlSink",
		            "writeBatchSize": 10000,
		            "writeBatchTimeout": "60:00:00"
		          }
		        },
		        "Policy": {
		          "concurrency": 1,
		          "executionPriorityOrder": "NewestFirst",
		          "retry": 0,
		          "timeout": "01:00:00"
		        }
		      }
		    ],
		    "start": "2015-07-12T00:00:00Z",
		    "end": "2015-07-13T00:00:00Z"
		  }
		} 

	다음 사항에 유의하세요.

	- activities 섹션에는 **type**이 **CopyActivity**로 설정된 작업 하나밖에 없습니다.
	- 작업에 대한 입력을 **EmpTableFromBlob**으로 설정하고 작업에 대한 출력을 **EmpSQLTable**로 설정합니다.
	- **transformation** 섹션에서 **BlobSource**를 원본 유형으로 지정하고 **SqlSink**를 싱크 유형으로 지정합니다.

	**start** 속성 값을 현재 날짜로 바꾸고 **end** 값을 다음 날짜로 바꿉니다. 날짜 부분만 지정하고 날짜/시간의 시간 부분은 건너뛸 수 있습니다. 예를 들어, "2015-02-03"은 "2015-02-03T00:00:00Z"과 동일합니다.
	
	start 및 end 날짜/시간은 둘 다 [ISO 형식](http://en.wikipedia.org/wiki/ISO_8601)(영문)이어야 합니다. 예: 2014-10-14T16:32:41Z. **end** 시간은 선택 사항이지만 이 자습서에서는 사용합니다.
	
	**end** 속성 값을 지정하지 않는 경우 "**start + 48시간**"으로 계산됩니다. 파이프라인을 무기한 실행하려면 **end** 속성 값으로 **9999-09-09**를 지정합니다.
	
	위의 예에서는 각 데이터 조각이 1시간마다 생성되므로 24개 데이터 조각이 있게 됩니다.
	
	JSON 속성에 대한 자세한 내용은 [JSON 스크립트 참조](http://go.microsoft.com/fwlink/?LinkId=516971)를 참조하세요.

4. 도구 모음에서 **배포**를 클릭하여 **ADFTutorialPipeline**을 만들고 배포합니다. **파이프라인이 성공적으로 생성됨** 메시지가 표시되는지 확인합니다.
5. 이제 **X**를 클릭하여 **편집기** 블레이드를 닫습니다. **X**를 다시 클릭하여 도구 모음 및 트리 뷰가 있는 ADFTutorialDataFactory 블레이드를 닫습니다. **저장하지 않은 편집 내용이 삭제됩니다** 메시지가 표시되면 **확인**을 클릭합니다.
6. **ADFTutorialDataFactory**의 **데이터 팩터리** 블레이드로 돌아갑니다.

**축하합니다.** Azure Data Factory, 연결된 서비스, 테이블 및 파이프라인을 성공적으로 만들고 파이프라인을 예약했습니다.
 
### 다이어그램 뷰에서 데이터 팩터리 보기 
1. **데이터 팩터리** 블레이드에서 **다이어그램**을 클릭합니다.

	![데이터 팩터리 블레이드 - 다이어그램 타일][image-datafactoryblade-diagramtile]

2. 다음과 유사한 다이어그램이 표시됩니다.

	![다이어그램 뷰][image-data-factory-get-started-diagram-blade]

	확대, 축소, 100% 확대, 크기에 맞게, 자동으로 파이프라인 및 테이블 위치 지정, 계보 정보 표시(선택한 항목의 업스트림 및 다운스트림 항목 강조 표시)를 수행할 수 있습니다. 개체(입출력 테이블 또는 파이프라인)를 두 번 클릭하면 해당 속성을 볼 수 있습니다. 
3. 다이어그램 뷰에서 **ADFTutorialPipeline**을 마우스 오른쪽 단추로 클릭하고 **파이프라인 열기**를 클릭합니다. 작업에 대한 입력 및 출력 데이터 집합과 함께 파이프라인의 작업이 표시됩니다. 이 자습서에서는 파이프라인(복사 작업)에 EmpTableBlob을 입력 데이터 집합으로, EmpSQLTable을 출력 데이터 집합으로 사용하는 작업 하나밖에 없습니다.   

	![파이프라인 열기](./media/data-factory-get-started-using-editor/DiagramView-OpenPipeline.png)

4. 왼쪽 위의 이동 경로에서 **데이터 팩터리**를 클릭하여 다이어그램 뷰로 돌아갑니다. 다이어그램 뷰에 모든 파이프라인이 표시됩니다. 이 예제에서는 하나의 파이프라인만 만들었습니다.
 

## <a name="MonitorDataSetsAndPipeline"></a>5단계: 데이터 집합 및 파이프라인 모니터링
이 단계에서는 Azure 포털을 사용하여 Azure Data Factory에서 어떤 일이 일어나는지 모니터링합니다. 또한 PowerShell cmdlet을 사용하여 데이터 집합과 파이프라인을 모니터링할 수도 있습니다. 모니터링에 cmdlet을 사용하는 방법에 대한 자세한 내용은 [PowerShell Cmdlet을 사용하여 데이터 팩터리 모니터링 및 관리][monitor-manage-using-powershell]를 참조하세요.

1. 아직 열지 않은 경우 [Azure 포털(Preview)][azure-preview-portal]로 이동합니다. 
2. **ADFTutorialDataFactory**에 대한 블레이드가 열려 있지 않으면 **시작 보드**에서 **ADFTutorialDataFactory**를 클릭하여 엽니다. 
3. 만든 테이블과 파이프라인의 개수 및 이름이 이 블레이드에 표시됩니다.

	![이름이 지정된 홈페이지][image-data-factory-get-started-home-page-pipeline-tables]

4. 이제 **데이터 집합** 타일을 클릭합니다.
5. **데이터 집합** 블레이드에서 **EmpTableFromBlob**을 클릭합니다. 이 테이블은 **ADFTutorialPipeline**의 입력 테이블입니다.

	![EmpTableFromBlob이 선택된 데이터 집합][image-data-factory-get-started-datasets-emptable-selected]   
5. **emp.txt** 파일이 항상 Blob 컨테이너 **adftutorial\\input**에 있으므로 현재 시간까지의 데이터 조각이 이미 생성되어 **Ready** 상태에 있습니다. 맨 아래의 **Recently failed slices(최근에 실패한 조각)** 섹션에 표시되는 조각이 없는지 확인합니다.

	**Recently updated slices(최근에 업데이트된 조각)** 및 **Recently failed slices(최근에 실패한 조각)** 목록은 둘 다 **마지막 업데이트 시간**을 기준으로 정렬됩니다. 조각의 업데이트 시간은 다음과 같은 상황에서 변경됩니다.
    

	-  **Set-AzureDataFactorySliceStatus**를 사용하거나 조각의 **조각** 블레이드에서 **실행**을 클릭하여 수동으로 조각 상태를 업데이트합니다.
	-  실행(예: 실행 시작, 실행 종료 및 실패, 실행 종료 및 성공 등)으로 인해 조각 상태가 변경됩니다.
 
	목록의 제목 또는 **...(줄임표)**을 클릭하여 더 큰 조각 목록을 표시합니다. 도구 모음의 **필터**를 클릭하여 조각을 필터링합니다.
	
	조각 시작/종료 시간을 기준으로 정렬된 데이터 조각을 보려면 **데이터 조각(조각 시간별)** 타일을 클릭합니다.

	![조각 시간별 데이터 조각][DataSlicesBySliceTime]

6. 이제 **데이터 집합** 블레이드에서 **EmpSQLTable**을 클릭합니다. 이 테이블은 **ADFTutorialPipeline**의 출력 테이블입니다.

	![데이터 집합 블레이드][image-data-factory-get-started-datasets-blade]



	 
6. 아래와 같이 **EmpSQLTable** 블레이드가 표시됩니다.

	![테이블 블레이드][image-data-factory-get-started-table-blade]
 
7. 현재 시간까지의 데이터 조각이 이미 생성되어 **Ready** 상태입니다. 맨 아래의 **Problem slices(문제 조각)** 섹션에 표시되는 조각이 없습니다.
8. **…(줄임표)**를 클릭하여 조각을 모두 표시합니다.

	![데이터 조각 블레이드][image-data-factory-get-started-dataslices-blade]

9. 목록에서 아무 데이터 조각이나 클릭하면 **데이터 조각** 블레이드가 표시됩니다.

	![데이터 조각 블레이드][image-data-factory-get-started-dataslice-blade]
  
	조각이 **Ready** 상태가 아닌 경우 **Upstream slices that are not ready(준비되지 않은 업스트림 조각)** 목록에서 Ready 상태가 아니고 현재 조각의 실행을 차단하는 업스트림 조각을 확인할 수 있습니다.

11. **데이터 조각** 블레이드의 맨 아래 목록에 모든 작업 실행이 표시됩니다. **작업 실행**을 클릭하여 **ACTIVITY RUN DETAILS(작업 실행 세부 정보)** 블레이드를 표시합니다.

	![작업 실행 세부 정보][image-data-factory-get-started-activity-run-details]

	
12. **X**를 클릭하여 모든 블레이드를 닫아 **ADFTutorialDataFactory**의 홈 블레이드로 돌아옵니다.
14. (선택 사항) **ADFTutorialDataFactory**의 홈 페이지에서 **파이프라인**을 클릭하고 **파이프라인** 블레이드에서 **ADFTutorialPipeline**을 클릭한 다음 입력 테이블(**Consumed**) 또는 출력 테이블(**Produced**)을 드릴스루합니다.
15. **SQL Server Management Studio**를 시작하고 Azure SQL 데이터베이스에 연결한 다음 데이터베이스의 **emp** 테이블에 행이 삽입되었는지 확인합니다.

	![SQL 쿼리 결과][image-data-factory-get-started-sql-query-results]


## 요약 
이 자습서에서는 Azure Blob에서 Azure SQL 데이터베이스로 데이터를 복사하는 Azure Data Factory를 만들었습니다. Azure Preview 포털을 사용하여 데이터 팩터리, 연결된 서비스, 테이블 및 파이프라인을 만들었습니다. 이 자습서에서 수행한 단계를 요약하면 다음과 같습니다.

1.	Azure **데이터 팩터리**를 만듭니다.
2.	데이터 저장소와 계산(**연결된 서비스**라고 함)을 데이터 팩터리에 연결하는** 연결된 서비스**를 만듭니다.
3.	파이프라인의 입력 데이터와 출력 데이터를 설명하는 **테이블**을 만듭니다.
4.	**파이프라인**을 만듭니다. 파이프라인은 하나 이상의 작업으로 구성되며 입력을 처리하고 출력을 생성합니다. 파이프라인에 대한 **시작** 시간 및 **종료** 시간을 지정하여 파이프라인에 대한 활성 기간을 설정합니다. 활성 기간은 데이터 조각이 생성되는 기간을 정의합니다. 


지원되는 작업 목록은 [파이프라인 및 작업][msdn-activities] 항목을 참조하고, 지원되는 연결된 서비스 목록은 MSDN 라이브러리의 [연결된 서비스][msdn-linkedservices] 항목을 참조하세요.
 
Azure PowerShell을 사용하여 이 자습서를 수행하려면 [Azure PowerShell을 사용하여 데이터 팩터리 만들기 및 모니터링][monitor-manage-using-powershell]을 참조하세요.


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
[adf-tutorial]: data-factory-tutorial.md
[use-custom-activities]: data-factory-use-custom-activities.md
[troubleshoot]: data-factory-troubleshoot.md
[data-factory-introduction]: data-factory-introduction.md
[data-factory-create-storage]: http://azure.microsoft.com/documentation/articles/storage-create-storage-account/#create-a-storage-account
[data-factory-create-sql-database]: ../sql-database-create-configure.md


[developer-reference]: http://go.microsoft.com/fwlink/?LinkId=516908
[cmdlet-reference]: http://go.microsoft.com/fwlink/?LinkId=517456

<!--Image references-->

[DataSlicesBySliceTime]: ./media/data-factory-get-started-using-editor/DataSlicesBySliceTime.png

[image-data-factory-getstarted-new-everything]: ./media/data-factory-get-started-using-editor/GetStarted-New-Everything.png

[image-data-factory-gallery-storagecachebackup]: ./media/data-factory-get-started-using-editor/getstarted-gallery-datastoragecachebackup.png

[image-data-factory-gallery-storagecachebackup-seeall]: ./media/data-factory-get-started-using-editor/getstarted-gallery-datastoragecachebackup-seeall.png

[image-data-factory-getstarted-data-services-data-factory-selected]: ./media/data-factory-get-started-using-editor/getstarted-data-services-data-factory-selected.png

[image-data-factory-getstarted-data-factory-create-button]: ./media/data-factory-get-started-using-editor/getstarted-data-factory-create-button.png

[image-data-factory-getstarted-new-data-factory-blade]: ./media/data-factory-get-started-using-editor/getstarted-new-data-factory.png

[image-data-factory-get-stated-factory-home-page]: ./media/data-factory-get-started-using-editor/getstarted-data-factory-home-page.png

[image-author-deploy-tile]: ./media/data-factory-get-started-using-editor/getstarted-author-deploy-tile.png

[image-editor-newdatastore-button]: ./media/data-factory-get-started-using-editor/getstarted-editor-newdatastore-button.png

[image-editor-blob-storage-json]: ./media/data-factory-get-started-using-editor/getstarted-editor-blob-storage-json.png

[image-editor-blob-storage-deploy]: ./media/data-factory-get-started-using-editor/getstarted-editor-blob-storage-deploy.png

[image-editor-azure-sql-settings]: ./media/data-factory-get-started-using-editor/getstarted-editor-azure-sql-settings.png

[image-editor-newpipeline-button]: ./media/data-factory-get-started-using-editor/getstarted-editor-newpipeline-button.png

[image-datafactoryblade-diagramtile]: ./media/data-factory-get-started-using-editor/getstarted-datafactoryblade-diagramtile.png


[image-data-factory-get-started-startboard]: ./media/data-factory-get-started-using-editor/getstarted-data-factory-startboard.png

[image-data-factory-get-started-linked-services-link]: ./media/data-factory-get-started-using-editor/getstarted-data-factory-linked-services-link.png

[image-data-factory-get-started-linked-services-add-store-button]: ./media/data-factory-get-started-using-editor/getstarted-linked-services-add-store-button.png

[image-data-factory-linked-services-get-started-new-data-store]: ./media/data-factory-get-started-using-editor/getstarted-linked-services-new-data-store.png

[image-data-factory-get-started-new-data-store-with-storage]: ./media/data-factory-get-started-using-editor/getstarted-linked-services-new-data-store-with-storage.png

[image-data-factory-get-started-storage-account-name-key]: ./media/data-factory-get-started-using-editor/getstarted-storage-account-name-key.png

[image-data-factory-get-started-linked-services-list-with-myblobstore]: ./media/data-factory-get-started-using-editor/getstarted-linked-services-list-with-myblobstore.png

[image-data-factory-get-started-linked-azure-sql-properties]: ./media/data-factory-get-started-using-editor/getstarted-linked-azure-sql-properties.png

[image-data-factory-get-started-azure-sql-connection-string]: ./media/data-factory-get-started-using-editor/getstarted-azure-sql-connection-string.png

[image-data-factory-get-started-linked-services-list-two-stores]: ./media/data-factory-get-started-using-editor/getstarted-linked-services-list-two-stores.png

[image-data-factory-get-started-storage-explorer]: ./media/data-factory-get-started-using-editor/getstarted-storage-explorer.png

[image-data-factory-get-started-diagram-link]: ./media/data-factory-get-started-using-editor/getstarted-diagram-link.png

[image-data-factory-get-started-diagram-blade]: ./media/data-factory-get-started-using-editor/getstarted-diagram-blade.png

[image-data-factory-get-started-home-page-pipeline-tables]: ./media/data-factory-get-started-using-editor/getstarted-datafactory-home-page-pipeline-tables.png

[image-data-factory-get-started-datasets-blade]: ./media/data-factory-get-started-using-editor/getstarted-datasets-blade.png

[image-data-factory-get-started-table-blade]: ./media/data-factory-get-started-using-editor/getstarted-table-blade.png

[image-data-factory-get-started-dataslices-blade]: ./media/data-factory-get-started-using-editor/getstarted-dataslices-blade.png

[image-data-factory-get-started-dataslice-blade]: ./media/data-factory-get-started-using-editor/getstarted-dataslice-blade.png

[image-data-factory-get-started-sql-query-results]: ./media/data-factory-get-started-using-editor/getstarted-sql-query-results.png

[image-data-factory-get-started-datasets-emptable-selected]: ./media/data-factory-get-started-using-editor/DataSetsWithEmpTableFromBlobSelected.png

[image-data-factory-get-started-activity-run-details]: ./media/data-factory-get-started-using-editor/ActivityRunDetails.png

[image-data-factory-create-resource-group]: ./media/data-factory-get-started-using-editor/CreateNewResourceGroup.png

[image-data-factory-preview-storage-key]: ./media/data-factory-get-started-using-editor/PreviewPortalStorageKey.png

[image-data-factory-database-connection-string]: ./media/data-factory-get-started-using-editor/DatabaseConnectionString.png

[image-data-factory-new-datafactory-menu]: ./media/data-factory-get-started-using-editor/NewDataFactoryMenu.png

[image-data-factory-sql-management-console]: ./media/data-factory-get-started-using-editor/getstarted-azure-sql-management-console.png

[image-data-factory-sql-management-console-2]: ./media/data-factory-get-started-using-editor/getstarted-azure-sql-management-console-2.png

[image-data-factory-name-not-available]: ./media/data-factory-get-started-using-editor/getstarted-data-factory-not-available.png
 

<!---HONumber=Nov15_HO3-->