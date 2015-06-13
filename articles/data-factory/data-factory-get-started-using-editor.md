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
	ms.topic="article" 
	ms.date="05/18/2015" 
	ms.author="spelluru"/>

# 자습서:를 만들고 데이터 팩터리 편집기를 사용 하 여 데이터 팩터리를 모니터링 합니다.
> [AZURE.SELECTOR]
- [Tutorial Overview](data-factory-get-started.md)
- [Using Data Factory Editor](data-factory-get-started-using-editor.md)
- [Using PowerShell](data-factory-monitor-manage-using-powershell.md)


##자습서 내용
이 자습서는 다음 단계를 포함 됩니다.

단계 | 설명
-----| -----------
[1 단계: 프로그램 Azure 데이터 팩터리 만들기](#CreateDataFactory) | 이 단계는 Azure 데이터 팩터리 라는 만듭니다 **ADFTutorialDataFactory**.  
[2 단계: 연결 된 서비스 만들기](#CreateLinkedServices) | 이 단계에서는 두 연결 된 서비스 만듭니다: **StorageLinkedService** 및 **AzureSqlLinkedService**. StorageLinkedService는 Azure 저장소 연결 및 AzureSqlLinkedService는 ADFTutorialDataFactory를 Azure SQL 데이터베이스를 연결 합니다. 파이프라인에 대 한 입력된 데이터는 Azure blob 저장소에서 blob 컨테이너에 있으며 출력 데이터는 Azure SQL 데이터베이스의 테이블에 저장 됩니다. 따라서 이러한 두 데이터 저장소로 연결 된 서비스에 추가한 데이터 팩터리입니다.      
[3 단계: 입력 및 출력 만듭니다 테이블](#CreateInputAndOutputDataSets) | 이전 단계에서 입/출력 데이터를 포함 하는 데이터 저장소를 참조 하는 연결 된 서비스를 만들었습니다. 이 단계에서는 두 테이블의 데이터 팩터리-정의 **EmpTableFromBlob** 및 **EmpSQLTable** -하는 데이터 저장소에 저장 된 입/출력 데이터를 나타냅니다. EmpTableFromBlob에 대 한 원본 데이터와는 EmpSQLTable에 대 한 blob이 포함 된 blob 컨테이너를 지정 합니다, 그리고 출력 데이터를 저장할 SQL 테이블을 지정 합니다. 데이터의 구조, 데이터, 등의 가용성과 같은 다른 속성을 지정 합니다... 
[4 단계: 만들고 파이프라인을 실행 합니다.](#CreateAndRunAPipeline) | 이 단계에서는 명명 된 파이프라인을 만듭니다 **ADFTutorialPipeline** 는 ADFTutorialDataFactory에 있습니다. 파이프라인을 갖습니다는 **복사 활동** 출력 Azure SQL 테이블에는 Azure에서 데이터를 입력 하는 복사 blob는 합니다.
[5 단계: 모니터링 슬라이스와 파이프라인](#MonitorDataSetsAndPipeline) | 이 단계에서는 Azure 미리 보기 포털을 사용 하 여 입력 및 출력 테이블의 조각을 모니터링 합니다.
 

## <a name="CreateDataFactory"></a>1 단계: 프로그램 Azure 데이터 팩터리 만들기
이 단계에서는 명명 된는 Azure 데이터 팩터리를 만들려면 Azure 미리 보기 포털 사용 **ADFTutorialDataFactory**.

1.	로그인 한 후는 [Azure 미리 보기 포털][azure-preview-portal], 클릭 **새로** 왼쪽 아래 모퉁이에서 선택 **데이터 분석** 에 **만들기** 블레이드를 클릭 하 고 **데이터 팩터리** 에 **데이터 분석** 블레이드. 

	![새로운 사용할->][image-data-factory-new-datafactory-menu]

6. 에 **새 데이터 팩터리** 블레이드:
	1. 입력 **ADFTutorialDataFactory** 에 대 한는 **이름**. 
	
  		![새 데이터 팩터리 블레이드][image-data-factory-getstarted-new-data-factory-blade]
	2. 클릭 하 여 **리소스 그룹 이름** 는 다음을 수행 합니다.
		1. 클릭 하 여 **새 리소스 그룹을 만들려면**.
		2. 에 **리소스 그룹 만들기** 블레이드, 입력 **ADFTutorialResourceGroup** 에 대 한는 **이름** 를클릭하고 리소스 그룹의 **확인**. 

			![리소스 그룹 만들기][image-data-factory-create-resource-group]

		이 자습서의 단계 중 일부 가정 이름을 사용 하 여: **ADFTutorialResourceGroup** 리소스 그룹입니다. 리소스 그룹에 대해 알아보려면 참조 [리소스 그룹을 사용 하 여 Azure 리소스를 관리할 수](resource-group-overview.md).  
7. 에 **새 데이터 팩터리** 블레이드를 있음을 **시작 보드에 추가** 을 선택 합니다.
8. 클릭 하 여 **만들기** 에 **새 데이터 팩터리** 블레이드입니다.

	> [AZURE.NOTE]Azure 데이터 팩터리 이름은 전역적으로 고유해야 합니다. 오류를 수신 하는 경우: **데이터 팩터리 이름을 "ADFTutorialDataFactory" 사용할 수 없으면**, 데이터 팩터리 (예: yournameADFTutorialDataFactory)의 이름을 변경 하 고 다시 만드십시오. 이 자습서의 나머지 단계를 수행 하는 동안 ADFTutorialFactory 대신이 이름을 사용 합니다. 참조 [데이터 팩터리-명명 규칙][data-factory-naming-rules] 데이터 팩터리 아티팩트에 대 한 명명 규칙에 대 한 항목입니다.
	 
	![데이터 팩터리 이름을 사용할 수 없음][image-data-factory-name-not-available]

9. 클릭 하 여 **알림을** 허브의 왼쪽에 만드는 과정에서 알림 찾습니다. 클릭 하 여 **X** 를 닫으려면는 **알림을** 블레이드 열려 있으면 합니다.
10. 나타납니다 생성이 완료 된 후의 **데이터 팩터리** 블레이드 아래와 같이 합니다.

    ![데이터 팩터리 홈페이지][image-data-factory-get-stated-factory-home-page]

## <a name="CreateLinkedServices"></a>2 단계: 연결 된 서비스 만들기
연결된 서비스는 데이터 저장소 또는 계산 서비스를 Azure 데이터 팩터리에 연결합니다. 데이터 저장소는 Azure 저장소, Azure SQL 데이터베이스 또는 온-프레미스 SQL Server 데이터베이스일 수 있습니다.

이 단계에서는 두 연결 된 서비스 만듭니다: **StorageLinkedService** 및 **AzureSqlLinkedService**. StorageLinkedService 연결 된 서비스 링크는 Azure 저장소 계정 및 AzureSqlLinkedService 링크를 Azure SQL 데이터베이스는 **ADFTutorialDataFactory**. SQL 테이블에 AzureSqlLinkedService StorageLinkedService blob 컨테이너에서 데이터를 복사 하는이 자습서의 뒷부분에서 파이프라인을 만들게 됩니다.

### Azure 저장소 계정에 대 한 연결 된 서비스 만들기
1.	에 **데이터 팩터리** 블레이드를 클릭 하 여 **작성자 및 배포** 타일을 시작는 **편집기** 데이터 팩터리에 대 한 합니다.

	![작성 및 배포 타일][image-author-deploy-tile]

	> [AZURE.NOTE]참조 [데이터 팩터리 편집기][data-factory-editor] 데이터 팩터리 편집기의 자세한 개요에 대 한 항목입니다.
	 
5. 에 **편집기**, 를 클릭 하 여 **새로운 데이터 저장소** 선택 도구 모음에서 단추 **Azure 저장소** 의 드롭다운 메뉴에서에서. 오른쪽 창에는 연결 된 Azure 저장소 서비스를 만들기 위한 JSON 템플릿을 표시 되어야 합니다.

	![새 데이터 저장소 단추 편집기][image-editor-newdatastore-button]
    
6. 대체 **accountname** 및 **accountkey** 계정 이름 및 Azure 저장소 계정에 대 한 계정 키 값입니다.

	![JSON 편집기 Blob 저장소][image-editor-blob-storage-json]
	
	> [AZURE.NOTE]참조 [JSON 스크립팅 참조](http://go.microsoft.com/fwlink/?LinkId=516971) JSON 속성에 대 한 세부 정보에 대 한 합니다.

6. 클릭 하 여 **배포** 는 StorageLinkedService를 배포 하려면 도구 모음입니다. 메시지가 표시 되는지 확인 **연결 된 서비스 만든 성공적으로** 제목 표시줄에 있습니다.

	![편집기 Blob 저장소 배포][image-editor-blob-storage-deploy]

### Azure SQL 데이터베이스에 대 한 연결 된 서비스 만들기
1. 에 **데이터 팩터리 편집기** , 를 클릭 하 여 **새로운 데이터 저장소** 선택 도구 모음에서 단추 **Azure SQL 데이터베이스** 의 드롭다운 메뉴에서에서. 오른쪽 창에서 Azure SQL 연결 서비스를 만들기 위한 JSON 템플릿을 표시 되어야 합니다.

	![Editr Azure SQL 설정][image-editor-azure-sql-settings]

2. 대체 **servername**, **databasename**, **username@servername**, 및 **암호** SQL Azure 서버, 데이터베이스, 사용자 계정 및 암호의 이름으로. 
3. 클릭 하 여 **배포** 만들기 및 배포는 AzureSqlLinkedService 도구 모음에 있습니다. 
   

## <a name="CreateInputAndOutputDataSets"></a>3 단계: 입력 및 출력 만듭니다 테이블
연결 된 서비스는 이전 단계에서 만든 **StorageLinkedService** 및 **AzureSqlLinkedService** 는 Azure 저장소 계정 및 Azure SQL 데이터베이스 데이터 팩터리 연결할: **ADFTutorialDataFactory**. 이 단계에서는 두 테이블의 데이터 팩터리-정의 **EmpTableFromBlob** 및 **EmpSQLTable** -StorageLinkedService 및 AzureSqlLinkedService 각각 참조 하는 데이터 저장소에 저장 된 입/출력 데이터를 나타내는 합니다. EmpTableFromBlob에 대 한 원본 데이터와 및 EmpSQLTable에 대 한 blob이 포함 된 blob 컨테이너를 지정 합니다, 그리고 출력 데이터를 저장할 SQL 테이블을 지정 합니다.

### 입력 테이블 만들기 
테이블은 사각형 데이터 집합이고 스키마가 있습니다. 이 단계에서는 명명 된 테이블을 만듭니다 **EmpBlobTable** 나타내는 Azure 저장소에서 blob 컨테이너를 가리키는 **StorageLinkedService** 서비스를 연결 합니다.

1. 에 **편집기** 데이터 팩터리를 클릭 합니다. **새 데이터 집합** 클릭 하 여 도구 모음에서 단추 **Blob 테이블** 의 드롭다운 메뉴에서에서. 
2. 오른쪽 창에서 다음 JSON 조각은 JSON을으로 바꾸십시오. 

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
            		"waitOnExternal": {}
       		 	}
    		}
		}

		
     다음 사항에 유의하세요.
	
	- 위치 **유형** 로 설정 된 **AzureBlobLocation**.
	- **linkedServiceName** 로 설정 된 **StorageLinkedService**. 2 단계에서에서이 연결 된 서비스를 만든 했습니다.
	- **folderPath** 로 설정 되는 **adftutorial** 컨테이너입니다. 또한 폴더 내의 Blob 이름을 지정할 수도 있습니다. Blob 이름을 지정하지 않으므로 컨테이너에 있는 모든 Blob의 데이터가 입력 데이터로 간주됩니다.  
	- 형식 **유형** 로 설정 된 **TextFormat**
	- – 텍스트 파일에는 두 필드가 **FirstName** 및 **LastName** – 구분 하 여 쉼표 문자 (* * columnDelimiter * *)	
	-  **가용성** 로 설정 된 **매시간** (* * 주파수 * *로 설정 된 **시간** 및 **간격** 로 설정 된 **1** ) 이므로 데이터 팩터리 서비스 데이터를 찾는 입력 매시간 blob 컨테이너에 루트 폴더에 (* * adftutorial * *) 사용자가 지정한 합니다. 
	

	지정 하지 않으면는 **fileName** 에 대 한 프로그램 **입력** **테이블**, 입력된 폴더에서 모든 파일/blob (* * folderPath * *) 입력으로 간주 됩니다. JSON에서 fileName을 지정하는 경우에는 지정한 파일/Blob만 입력으로 간주됩니다. 샘플 파일을 참조는 [자습서][adf-tutorial] 에 대 한 예입니다.
 
	지정 하지 않으면는 **fileName** 에 대 한 프로그램 **출력 테이블**, 생성 된 파일에는 **folderPath** 다음과 같은 형식으로 이름이 지정 됩니다: 데이터. & l t; Guid & g t;입니다. txt (예: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt.).

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

2. 클릭 하 여 **배포** 만들기 및 배포 도구 모음에는 **EmpTableFromBlob** 테이블입니다. 표시 되는지 확인은 **테이블 만든 성공적으로** 편집기의 제목 표시줄에는 메시지입니다.

### 출력 테이블 만들기
이 단계는 부분 이라는 출력 테이블을 만듭니다 **EmpSQLTable** 포인트 SQL 테이블에 Azure SQL 데이터베이스를 나타내는 **AzureSqlLinkedService** 서비스를 연결 합니다.

1. 에 **편집기** 데이터 팩터리를 클릭 합니다. **새 데이터 집합** 클릭 하 여 도구 모음에서 단추 **Azure SQL 테이블** 의 드롭다운 메뉴에서에서. 
2. 오른쪽 창에서 다음 JSON 조각은 JSON을으로 바꾸십시오.

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
            		"type": "AzureSqlTableLocation",
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
	* **linkedServiceName** 로 설정 된 **AzureSqlLinkedService** (2 단계에서에서이 연결 된 서비스 만든 했습니다).
	* **tablename** 로 설정 된 **emp**.
	* 3 개의 열이 – **ID**, **FirstName**, 및 **LastName** -데이터베이스에 emp 테이블에서 ID 이므로 id 열만 지정 해야 하지만 **FirstName** 및 **LastName** 여기입니다.
	*  **가용성** 로 설정 된 **매시간** (* * 주파수 * *로 설정 합니다. **시간** 및 **간격** 로 설정 **1**). 데이터 팩터리 서비스는 출력 데이터 조각에 1 시간 마다 생성 됩니다는 **emp** Azure SQL 데이터베이스의 테이블입니다.


3. 클릭 하 여 **배포** 만들기 및 배포 도구 모음에는 **EmpSQLTable** 테이블입니다.


## <a name="CreateAndRunAPipeline"></a>4 단계: 만들고 파이프라인을 실행 합니다.
포함 된 파이프라인을 만들면이 단계에서는 **복사 활동** 사용 하 여 **EmpTableFromBlob** 입력으로 및 **EmpSQLTable** 출력으로.

1. 에 **편집기** 데이터 팩터리를 클릭 합니다. **새 파이프라인** 도구 모음 단추입니다. 단추가 표시되지 않는 경우 도구 모음에서 **... (줄임표)**을 클릭합니다. 또는 트리 뷰에서 **파이프라인**을 마우스 오른쪽 단추로 클릭하고 **새 파이프라인**을 클릭할 수 있습니다.

	![새 파이프라인 단추 편집기][image-editor-newpipeline-button]
 
2. 오른쪽 창에서 다음 JSON 조각은 JSON을으로 바꾸십시오.

         {
			"name": "ADFTutorialPipeline",
			"properties":
			{	
				"description" : "Copy data from a blob to Azure SQL table",
				"activities":	
				[
					{
						"name": "CopyFromBlobToSQL",
						"description": "Push Regional Effectiveness Campaign data to Azure SQL database",
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

				"start": "2015-02-13T00:00:00Z",
        		"end": "2015-02-14T00:00:00Z",
        		"isPaused": false
      		}
		} 

	다음 사항에 유의하세요.

	- 작업 섹션에는 활동이 하나만 인 **유형** 로 설정 된 **CopyActivity**.
	- 활동으로 설정 됩니다 입력 **EmpTableFromBlob** 및 활동으로 설정 됩니다 출력 **EmpSQLTable**.
	- 에 **변환** 섹션 **BlobSource** 원본 유형으로 지정 된 및 **SqlSink** 싱크 형식으로 지정 됩니다.

	> [AZURE.NOTE]값을 바꿉니다는 **시작** 은 현재 날짜를 사용 하 여 속성 및 **끝** 다음날을 사용 하 여 값입니다. 날짜 부분에만 지정 하 고는 날짜 및 시간의 시간 부분을 건너뛸 수 있습니다. 예를들어, "2015-02-03", 즉 "2015-02-03T00:00:00Z" 모두 시작 및 끝 datetime에 있어야 합니다. [ISO 형식](http://en.wikipedia.org/wiki/ISO_8601). 예: 2014-10-14T16:32:41Z 합니다.  **끝** 시간은 선택적 이지만이 자습서에서 사용할 것입니다. 에 대 한 값을 지정 하지 않으면는 **끝** 로 계산 됩니다 속성을 "* * 시작 + 48 시간 * *"입니다. 파이프라인을 무기한으로 실행 하려면 지정 **9999-09-09** 에 대 한 값으로는 **끝** 속성입니다. 위의 예에서는 각 데이터 조각이 1시간마다 생성되므로 24개 데이터 조각이 있게 됩니다.
	
	> [AZURE.NOTE]참조 [JSON 스크립팅 참조](http://go.microsoft.com/fwlink/?LinkId=516971) JSON 속성에 대 한 세부 정보에 대 한 합니다.

4. 클릭 하 여 **배포** 만들기 및 배포 도구 모음에는 **ADFTutorialPipeline**. 표시 되는지 확인은 **파이프라인 만든 성공적으로** 메시지입니다.
5. 이제 닫습니다는 **편집기** 블레이드를 클릭 하 여 **X**. 클릭 하 여 **X** 를 다시 도구 모음 및 트리 보기와 ADFTutorialDataFactory 블레이드를 닫습니다. 표시 되 면 **저장 되지 않은 편집 내용을 무시 됩니다** 메시지를 클릭 하 여 **확인**.
6. 돌아갈 수는 **데이터 팩터리** 에 대 한 블레이드는 **ADFTutorialDataFactory**.

**축하합니다.** Azure 데이터 팩터리, 연결된 서비스, 테이블 및 파이프라인을 성공적으로 만들고 파이프라인을 예약했습니다.
 
### 다이어그램 뷰에서 데이터 팩터리 보기 
1. 에 **데이터 팩터리** 블레이드를 클릭 하 여 **다이어그램**.

	![데이터 팩터리 블레이드-다이어그램 타일][image-datafactoryblade-diagramtile]

2. 다음과 유사한 다이어그램이 표시됩니다.

	![다이어그램 보기][image-data-factory-get-started-diagram-blade]

	확대, 축소, 확대/축소 맞춤, 자동으로 파이프라인 및 테이블의 위치 및 계보 정보를 표시 하려면 (은 선택 된 항목의 항목을 업스트림 및 다운스트림 강조 하 고), 100%로 확대/축소 수 있습니다. 이중 blick (입/출력 테이블 또는 파이프라인)에 대 한 속성을 보려면 개체에 지정할 수 있습니다. 
3. 마우스 오른쪽 단추로 클릭 **ADFTutorialPipeline** 를클릭하고 다이어그램 보기에서 **개방 된 파이프라인이**. 활동의 활동에 대 한 입력 및 출력 데이터 집합을 함께 파이프라인에 표시 되어야 합니다. 이 자습서에서는 입력된 데이터 집합 및 출력 데이터 집합으로 EmpSQLTable EmpTableBlob와 파이프라인 (복사 작업)에 활동이 하나만 있어야 합니다.   

	![개방 된 파이프라인이](./media/data-factory-get-started-using-editor/DiagramView-OpenPipeline.png)

4. 클릭 하 여 **데이터 팩터리** 다이어그램 보기에 복귀할 왼쪽 위 모서리의 이동 경로에 있습니다. 다이어그램 보기는 모든 파이프라인을 표시합니다. 이 예제에서는 하나의 파이프라인을 만든만 있습니다.
 

## <a name="MonitorDataSetsAndPipeline"></a>5 단계: 데이터 집합 및 파이프라인 모니터링
이 단계에서는 Azure 포털을 사용하여 Azure 데이터 팩터리에서 어떤 일이 일어나는지 모니터링합니다. 또한 PowerShell cmdlet을 사용하여 데이터 집합과 파이프라인을 모니터링할 수도 있습니다. 모니터링에 대 한 cmdlet을 사용 하는 방법에 대 한 세부 정보를 참조 하십시오. [모니터 및 PowerShell Cmdlet을 사용 하 여 관리 데이터 팩터리][monitor-manage-using-powershell].

1. 이동 [Azure 포털 (미리 보기)][azure-preview-portal] 열어 없는 경우. 
2. 경우에 대 한 블레이드 **ADFTutorialDataFactory** 가 열려있지 않으면 클릭 하 여 엽니다 **ADFTutorialDataFactory** 에 **시작 보드**. 
3. 만든 테이블과 파이프라인의 개수 및 이름이 이 블레이드에 표시됩니다.

	![이름의 홈페이지][image-data-factory-get-started-home-page-pipeline-tables]

4. 이제 클릭 **데이터 집합** 바둑판식으로 배열 합니다.
5. 에 **데이터 집합** 블레이드를 클릭 하 여 **EmpTableFromBlob**. 이 대 한 입력된 테이블은는 **ADFTutorialPipeline**.

	![EmpTableFromBlob 선택 된 데이터 집합][image-data-factory-get-started-datasets-emptable-selected]   
5. 현재 시간까지 데이터 조각이 이미 생성 되었습니다 보이며 스페이스인 **준비** 때문에 **emp.txt** 파일이 blob 컨테이너에 항상: **adftutorial\input**. 확인 되지 조각에 표시 하는 **최근에 조각 실패 한** 아래쪽 섹션입니다.

	모두 **조각 최근에 업데이트** 및 **최근에 조각 실패 한** 목록을 기준으로 정렬 됩니다는 **마지막 업데이트 시간**. 다음과 같은 상황에서 조각의 업데이트 시간이 변경 됩니다.
    

	-  수동으로 업데이트할 있습니다 조각의 상태, 예를 사용 하 여는 **집합 AzureDataFactorySliceStatus** (또는)를 클릭 하 여 **실행** 에 **조각** 블레이드는 조각에 대 한 합니다.
	-  실행으로 인해 상태를 변경 하는 조각 (예: 시작을 실행 하는, 종료 하 고 실패 한 실행, 실행을 종료 하 고 성공, 등).
 
	목록 또는 의 제목을 클릭 **... (타원)** 더 큰 조각 목록을 확인 합니다. 클릭 하 여 **필터** 슬라이스를 필터링 하려면 도구 모음입니다.
	
	기준으로 정렬 된 조각 시작/종료 시간 대신 데이터 조각이 보려면 클릭 합니다. **(시간별 slice) 데이터 조각이** 바둑판식으로 배열 합니다.

	![Slice 시간별 데이터 조각][DataSlicesBySliceTime]

6. 이제는 **데이터 집합** 블레이드를 클릭 하 여는 **EmpSQLTable**. 이 대 한 출력 테이블은는 **ADFTutorialPipeline**.

	![데이터 집합 블레이드][image-data-factory-get-started-datasets-blade]



	 
6. 표시는 **EmpSQLTable** 아래와 같이 블레이드:

	![테이블 블레이드][image-data-factory-get-started-table-blade]
 
7. 현재 시간까지 데이터 조각이 이미 생성 되었습니다 보이며 스페이스인 **준비**. 없음 조각에 표시 된 **문제 조각** 아래쪽 섹션.
8. 클릭 **... (줄임표)** 모든 분할 영역을 확인 합니다.

	![데이터 조각 블레이드][image-data-factory-get-started-dataslices-blade]

9. 목록에서 데이터 조각을 아무 것 이나 클릭 하 고 표시 되어야는 **데이터 조각을** 블레이드입니다.

	![데이터 조각 블레이드][image-data-factory-get-started-dataslice-blade]
  
	조각에 없는 경우는 **준비** 상태를 준비 하지 않으며는 현재 조각에서 실행할 수 없도록 차단 여부를 지정 하는 업스트림 조각을 볼 수 있습니다는 **준비 하지 않은 업스트림 조각** 목록입니다.

11. 에 **데이터 조각을** 블레이드에서 나타납니다 아래쪽 목록에서 모든 활동을 실행 합니다. 클릭할는 **활동 실행** 참조 하는 **작업 실행 세부 정보** 블레이드입니다.

	![활동 실행 정보][image-data-factory-get-started-activity-run-details]

	
12. 클릭 하 여 **X** 에 대 한 홈 블레이드로 다시 가져올 때까지 모든 블레이드를 닫으려면는 **ADFTutorialDataFactory**.
14. (선택 사항) 클릭 하 여 **파이프라인** 에 대 한 홈페이지에서 **ADFTutorialDataFactory**, 클릭 **ADFTutorialPipeline** 에 **파이프라인** 블레이드, 및 입력된 테이블을 통해 드릴 (* * 사용 됨 * *) 출력 테이블 또는 (* * 생산 * *).
15. 시작 **SQL Server Management Studio**, Azure SQL 데이터베이스에 연결 하 고 행 삽입 확인는 **emp** 데이터베이스의 테이블에에서 있습니다.

	![sql 쿼리 결과][image-data-factory-get-started-sql-query-results]


## 요약 
이 자습서에서는 Azure Blob에서 Azure SQL 데이터베이스로 데이터를 복사하는 Azure 데이터 팩터리를 만들었습니다. 데이터 팩터리, 연결 된 서비스, 테이블 및 파이프라인을 만들려면 Azure 미리 보기 포털을 사용 합니다. 이 자습서에서 수행한 단계를 요약하면 다음과 같습니다.

1.	Azure 만들기 **데이터 팩터리**.
2.	만들 **서비스 연결** 데이터 저장소 및 계산을 연결 하는 (라고 **연결 된 서비스**) 데이터 팩터리입니다.
3.	만들 **테이블** 설명 하는 입력 데이터 및 파이프라인에 대 한 데이터를 출력 합니다.
4.	만들 **파이프라인**. 파이프라인은 하나 이상의 작업으로 구성되며 입력을 처리하고 출력을 생성합니다. 파이프라인에 대 한 활성 기간을 지정 하 여 설정 **시작** 시간 및 **끝** 파이프라인에 대 한 시간입니다. 활성 기간은 데이터 조각이 생성되는 기간을 정의합니다. 


> [AZURE.NOTE]지원 되는 작업 목록은 참조 [파이프라인 및 활동][msdn-activities] 항목 목록이 지원 되는 연결 된 서비스에 대 한 참조 및 [연결 된 서비스][msdn-linkedservices] MSDN 라이브러리의 항목입니다.
> 
> Azure PowerShell을 사용 하 여이 자습서를 수행 하려면 참조 [만들기 및 Azure PowerShell을 사용 하 여 데이터 팩터리 모니터][monitor-manage-using-powershell].

## 다음 단계

문서 | 설명
------ | ---------------
[Azure 데이터 팩터리 복사 작업을 사용 하 여 데이터를 복사 합니다.][copy-activity] | 이 문서에 대 한 자세한 설명을 제공 된 **복사 활동** 이 자습서에서 사용 합니다. 
[온-프레미스 데이터로 작업 하 여 파이프라인을 사용 하도록 설정][use-onpremises-datasources] | 이 문서에서 데이터를 복사 하는 방법을 보여주는 연습 과정에는 **온-프레미스 SQL Server 데이터베이스** Azure blob에 있습니다. 
[이동 하 고 데이터 팩터리를 사용 하 여 로그 파일을 처리 하는 자습서:][adf-tutorial] | 이 문서에서는 프로그램 **-종단간 연습** 구현 하는 방법을 표시 하는 **실제 시나리오** 통찰력에 로그 파일의에서 데이터를 변환할 Azure 데이터 팩터리를 사용 하 여 합니다.
[데이터 팩터리 문제를 해결 합니다.][troubleshoot] | 이 문서에서 설명 하는 방법을 **문제를 해결** Azure 데이터 팩터리를 발급 합니다. Azure SQL 데이터베이스의 테이블을 삭제하는 등 오류를 유발하여 이 문서의 연습을 ADFTutorialDataFactory에 대해 사용해 볼 수 있습니다. 
[Azure 데이터 팩터리 개발자 참조][developer-reference] | 개발자 참조에는 cmdlet, JSON 스크립트, 함수에 대 한 포괄적인 참조 콘텐츠 중... 


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

<!---HONumber=GIT-SubDir--> 