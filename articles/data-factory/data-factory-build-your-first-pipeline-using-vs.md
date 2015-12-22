<properties
	pageTitle="Visual Studio를 사용하여 첫 번째 Azure Data Factory 파이프라인 빌드"
	description="이 자습서에서는 Visual Studio를 사용하여 샘플 Azure Data Factory 파이프라인을 만듭니다."
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
	ms.date="11/02/2015"
	ms.author="spelluru"/>

# Visual Studio를 사용하여 첫 번째 Azure Data Factory 파이프라인 빌드
> [AZURE.SELECTOR]
- [Tutorial Overview](data-factory-build-your-first-pipeline.md)
- [Using Data Factory Editor](data-factory-build-your-first-pipeline-using-editor.md)
- [Using PowerShell](data-factory-build-your-first-pipeline-using-powershell.md)
- [Using Visual Studio](data-factory-build-your-first-pipeline-using-vs.md)
- [Using Resource Manager Template](data-factory-build-your-first-pipeline-using-arm.md)


이 문서에서는 Visual Studio를 사용하여 첫 번째 파이프라인을 만드는 방법을 알아봅니다. 이 자습서는 다음과 같은 단계로 구성됩니다.

2.	연결된 서비스(데이터 저장소, 계산) 만들기
3.	데이터 집합 만들기
3.	파이프라인 만들기
4.	데이터 팩터리를 만들고 연결된 서비스, 데이터 집합 및 파이프라인 배포 

이 문서는 Azure Data Factory 서비스에 대한 개념적 개요를 제공하지 않습니다. 서비스에 대한 자세한 개요는 [Azure Data Factory 소개](data-factory-introduction.md) 문서를 참조하세요.

> [AZURE.IMPORTANT][자습서 개요](data-factory-build-your-first-pipeline.md) 문서를 살펴보고 이 자습서를 수행하기 전에 필수 단계를 완료합니다.

## 연습: Visual Studio를 사용하여 데이터 팩터리 엔터티 만들기 및 배포 

### 필수 구성 요소

다음 항목이 컴퓨터에 설치되어 있어야 합니다.

- Visual Studio 2013 또는 Visual Studio 2015
- Visual Studio 2013 또는 Visual Studio 2015용 Azure SDK를 다운로드합니다. [Azure 다운로드 페이지](http://azure.microsoft.com/downloads/)로 이동하고 **.NET** 섹션에서 **VS 2013** 또는 **VS 2015**를 클릭합니다.
- [VS 2013](https://visualstudiogallery.msdn.microsoft.com/754d998c-8f92-4aa7-835b-e89c8c954aa5) 또는 [VS 2015](https://visualstudiogallery.msdn.microsoft.com/371a4cf9-0093-40fa-b7dd-be3c74f49005)용 최신 Azure Data Factory 플러그 인을 다운로드합니다. Visual Studio 2013을 사용하는 경우 메뉴에서 **도구** -> **확장 및 업데이트** -> **온라인** -> **Visual Studio 갤러리** -> **Visual Studio용 Microsoft Azure Data Factory 도구** -> **업데이트**를 클릭하여 플러그 인을 업데이트할 수도 있습니다. 
	
	

### Visual Studio 프로젝트 만들기 
1. **Visual Studio 2013** 또는 **Visual Studio 2015**를 시작합니다. **파일**을 클릭하고 **새로 만들기**를 가리킨 다음 **프로젝트**를 클릭합니다. **새 프로젝트** 대화 상자가 나타납니다.  
2. **새 프로젝트** 대화 상자에서 **DataFactory** 템플릿을 선택하고 **빈 데이터 팩터리 프로젝트**를 클릭합니다.   

	![새 프로젝트 대화 상자](./media/data-factory-build-your-first-pipeline-using-vs/new-project-dialog.png)

3. 프로젝트의 **이름**, **위치** 및 **솔루션**의 이름을 입력한 다음 **확인**을 클릭합니다.

	![솔루션 탐색기](./media/data-factory-build-your-first-pipeline-using-vs/solution-explorer.png)

### 연결된 서비스 만들기
이 단계에서는 Azure 저장소 계정과 주문형 Azure HDInsight 클러스터를 데이터 팩터리에 연결한 후 Hive 처리의 출력 데이터를 나타낼 데이터 집합을 만듭니다.


#### Azure 저장소 연결된 서비스 만들기


4. 솔루션 탐색기에서 **연결된 서비스**를 마우스 오른쪽 단추로 클릭하고 **추가**를 가리킨 다음 **새 항목**을 클릭합니다.      
5. **새 항목 추가** 대화 상자의 목록에서 **Azure 저장소 연결된 서비스**를 선택한 다음 **추가**를 클릭합니다. 

	![새 연결된 서비스](./media/data-factory-build-your-first-pipeline-using-vs/new-linked-service-dialog.png)
 
3. **accountname** 및 **accountkey**를 Azure 저장소 계정 이름 및 해당 키로 바꿉니다.

	![Azure 저장소 연결된 서비스](./media/data-factory-build-your-first-pipeline-using-vs/azure-storage-linked-service.png)

4. **AzureStorageLinkedService1.json** 파일을 저장합니다.

#### Azure HDInsight 연결된 서비스 만들기
Hive 스크립트를 실행하는데 사용될 주문형 HDInsight 클러스터의 연결된 서비스를 만듭니다.

1. **솔루션 탐색기**에서 **연결된 서비스**를 마우스 오른쪽 단추로 클릭하고 **추가**를 가리킨 다음 **새 항목**을 클릭합니다.
2. **주문형 HDInsight 연결된 서비스**를 선택하고 **추가**를 클릭합니다. 
3. **JSON**을 다음으로 바꿉니다.

		{
		  "name": "HDInsightOnDemandLinkedService",
		  "properties": {
		    "type": "HDInsightOnDemand",
		    "typeProperties": {
		      "version": "3.2",
		      "clusterSize": 1,
		      "timeToLive": "00:30:00",
		      "linkedServiceName": "AzureStorageLinkedService1"
		    }
		  }
		}
	
	다음 테이블은 코드 조각에 사용된 JSON 속성에 대한 설명을 제공합니다.
	
	속성 | 설명
	-------- | -----------
	버전 | 생성되는 HDInsight 버전을 3.2로 지정합니다. 
	ClusterSize | 노드가 하나인 HDInsight 클러스터를 만듭니다. 
	TimeToLive | HDInsight 클러스터가 삭제되기 전 유휴 시간을 지정합니다.
	linkedServiceName | HDInsight에 의해 생성되는 로그를 저장하는데 사용될 저장소 계정을 지정합니다.

4. **HDInsightOnDemandLinkedService1.json** 파일을 저장합니다.
 
### 출력 데이터 집합 만들기
Azure Blob 저장소에 저장된 데이터를 나타내는 출력 데이터 집합을 만듭니다.

1. **솔루션 탐색기**에서 마우스 오른쪽 단추를 클릭하고 **추가**를 가리킨 다음 **새 항목**을 클릭합니다. 
2. 목록에서 **Azure Blob**을 선택하고 **추가**를 클릭합니다. 
3. 편집기에서 **JSON**을 다음과 같이 변경합니다. JSON 코드 조각에서 **AzureBlobOutput**이라는 데이터 집합을 만들고 Hive 스크립트에 의해 생성될 데이터의 구조를 지정합니다. 또한 결과가 **data**라는 Blob 컨테이너와 **partitioneddata** 폴더에 저장되도록 지정합니다. **availability** 섹션은 출력 데이터 집합이 월 단위로 생성되도록 지정합니다.
	
		{
		  "name": "AzureBlobOutput",
		  "properties": {
		    "type": "AzureBlob",
		    "linkedServiceName": "AzureStorageLinkedService1",
		    "typeProperties": {
		      "folderPath": "data/partitioneddata",
		      "format": {
		        "type": "TextFormat",
		        "columnDelimiter": ","
		      }
		    },
		    "availability": {
		      "frequency": "Month",
		      "interval": 1
		    }
		  }
		}

4. **AzureBlobLocation1.json** 파일을 저장합니다.


### 첫 번째 파이프라인 만들기
이 단계에서는 첫 번째 파이프라인을 만듭니다.

1. **솔루션 탐색기**에서 **파이프라인**을 마우스 오른쪽 단추로 클릭하고 **추가**를 가리킨 다음 **새 항목**을 클릭합니다. 
2. 목록에서 **Hive 변환 파이프라인**을 선택하고 **추가**를 클릭합니다. 
3. **JSON**을 다음 코드 조각으로 바꿉니다.

	> [AZURE.IMPORTANT]**storageaccountname**을 저장소 계정 이름으로 바꿉니다.

		{
		  "name": "MyFirstPipeline",
		  "properties": {
		    "description": "My first Azure Data Factory pipeline",
		    "activities": [
		      {
		        "type": "HDInsightHive",
		        "typeProperties": {
		          "scriptPath": "script/partitionweblogs.hql",
		          "scriptLinkedService": "AzureStorageLinkedService1",
		          "defines": {
		            "partitionedtable": "wasb://data@<storageaccountname>.blob.core.windows.net/partitioneddata"
		          }
		        },
		        "outputs": [
		          {
		            "name": "AzureBlobOutput"
		          }
		        ],
		        "policy": {
		          "concurrency": 1,
		          "retry": 3
		        },
		        "name": "RunSampleHiveActivity",
		        "linkedServiceName": "HDInsightOnDemandLinkedService"
		      }
		    ],
		    "start": "2014-01-01",
		    "end": "2014-01-02"
		  }
		}

 	JSON 코드 조각에서 Hive를 사용하여 HDInsight 클러스터에서 데이터를 처리하는 단일 작업으로 구성되는 파이프라인을 만듭니다.
	
	Hive 스크립트 파일 **partitionweblogs.hql**은 Azure 저장소 계정(**AzureStorageLinkedService1**이라고 하는 scriptLinkedService에 의해 지정됨)과 **script**라는 컨테이너에 저장됩니다.

	**defines** 섹션은 Hive 스크립트에 Hive 구성 값(예: ${hiveconf:PartitionedData})으로 전달되는 런타임 설정을 지정하는 데 사용됩니다.

	파이프라인의 **start** 및 **end** 속성은 파이프라인의 활성 기간을 지정합니다.

	작업 JSON에서 Hive 스크립트가 연결된 서비스 **HDInsightOnDemandLinkedService**에 지정된 컴퓨터에서 실행되도록 지정합니다.
3. **HiveActivity1.json** 파일을 저장합니다.

### 종속성으로 Partitionweblogs.hql 추가 

1. **솔루션 탐색기** 창에서 **종속성**을 마우스 오른쪽 단추로 클릭하고 **추가**를 가리킨 다음 **기존 항목**을 클릭합니다.  
2. **C:\\ADFGettingStarted**로 이동하고 **partitionweblogs.hql** 파일을 선택한 다음 **추가**를 클릭합니다. 

다음 단계에서 솔루션을 게시할 때 HQL 파일은 Blob 저장소의 스크립트 컨테이너로 업로드됩니다.

### 데이터 팩터리 엔터티 게시/배포

18. 솔루션 탐색기에서 프로젝트를 마우스 오른쪽 단추로 클릭하고 **게시**를 클릭합니다. 
19. **Microsoft 계정에 로그인** 대화 상자가 표시되면 Azure 구독이 있는 계정의 자격 증명을 입력하고 **로그인**을 클릭합니다.
20. 다음 대화 상자가 표시됩니다.

	![게시 대화 상자](./media/data-factory-build-your-first-pipeline-using-vs/publish.png)

21. 데이터 팩터리 구성 페이지에서 다음을 수행합니다.
	1. **새 데이터 팩터리 만들기** 옵션을 선택합니다.
	2. **이름**에 **FirstPipelineUsingVS**를 입력합니다. 
	
		> [AZURE.IMPORTANT]Azure Data Factory 이름은 전역적으로 고유해야 합니다. 게시할 때 **데이터 팩터리 이름 “FirstPipelineUsingVS”를 사용할 수 없습니다.** 오류가 표시되는 경우 이름을 변경합니다(예: yournameFirstPipelineUsingVS). 데이터 팩터리 아티팩트에 대한 명명 규칙은 [데이터 팩터리 - 명명 규칙](data-factory-naming-rules.md) 항목을 참조하세요.
		> 
		> 데이터 팩터리의 이름은 나중에 DNS 이름으로 표시되므로 공개적으로 등록될 수도 있습니다.
	3. **구독** 필드에서 올바른 구독을 선택합니다. 
	4. 생성되는 데이터 팩터리의 **리소스 그룹**을 선택합니다. 
	5. 데이터 팩터리의 **지역**을 선택합니다. 
	6. **다음**을 클릭하여 **항목 게시** 페이지로 전환합니다. **다음** 단추를 사용할 수 없는 경우 **Tab** 키를 눌러 이름 필드에서 나갑니다. 
23. **항목 게시** 페이지에서 모든 데이터 팩터리 엔터티가 선택되었는지 확인하고 **다음**을 클릭하여 **요약** 페이지로 전환합니다.     
24. 요약을 검토한 후 **다음**을 클릭하여 배포 프로세스를 시작하고 **배포 상태**를 봅니다.
25. **배포 상태** 페이지에 배포 프로세스의 상태가 표시됩니다. 배포가 완료되면 마침을 클릭합니다. 
 

## 서버 탐색기를 사용하여 데이터 팩터리 엔터티를 검토합니다.

1. **Visual Studio**의 메뉴에서 **보기**를 클릭한 다음 **서버 탐색기**를 클릭합니다.
2. 서버 탐색기 창에서 **Azure**를 확장한 다음 **데이터 팩터리**를 확장합니다. **Visual Studio에 로그인**이 표시되면 Azure 구독과 연결된 **계정**을 입력하고 **계속**을 클릭합니다. **암호**를 입력하고 **로그인**을 클릭합니다. Visual Studio에서는 구독에 있는 모든 Azure Data Factory에 대한 정보를 가져오려고 시도합니다. **데이터 팩터리 작업 목록** 창에 이 작업의 상태가 표시됩니다.

	![서버 탐색기](./media/data-factory-build-your-first-pipeline-using-vs/server-explorer.png)
3. 데이터 팩터리를 마우스 오른쪽 단추로 클릭하고 **새 프로젝트로 데이터 팩터리 내보내기**를 선택하여 기존 데이터 팩터리에 따라 Visual Studio 프로젝트를 만들 수 있습니다.

	![데이터 팩터리 내보내기](./media/data-factory-build-your-first-pipeline-using-vs/export-data-factory-menu.png)

## Visual Studio용 데이터 팩터리 도구 업데이트

Visual Studio용 Azure Data Factory 도구를 업데이트하려면 다음을 수행합니다.

1. 메뉴에서 **도구**를 클릭하고 **확장 및 업데이트**를 선택합니다.
2. 왼쪽 창에서 **업데이트**를 선택한 다음 **Visual Studio 갤러리**를 선택합니다.
3. **Visual Studio용 Azure Data Factory 도구**를 선택하고 **업데이트**를 클릭합니다. 이 항목이 표시되지 않으면 이미 최신 버전의 도구가 있는 것입니다. 

Azure 포털을 사용하여 이 자습서에서 만든 파이프라인 및 데이터 집합을 모니터링하는 방법에 대한 지침은 [데이터 집합 및 파이프라인 모니터링](data-factory-monitor-manage-pipelines.md)을 참조하세요.
 

## 다음 단계
이 문서에서 파이프라인과 주문형 HDInsight 클러스터에서 Hive 스크립트를 실행하는 변환 작업(HDInsight 작업)을 만들었습니다. 복사 작업을 사용하여 Azure Blob에서 Azure SQL로 데이터를 복사하는 방법은 [자습서: Azure Blob에서 Azure SQL로 데이터 복사](data-factory-get-started.md)를 참조하세요.
  

<!---HONumber=AcomDC_1217_2015-->