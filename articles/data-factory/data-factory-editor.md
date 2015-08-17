<properties 
	pageTitle="Azure 데이터 팩터리 편집기" 
	description="간단한 웹 기반 UI를 사용하여 연결 서비스, 테이블 및 파이프라인의 JSON 정의를 만들기, 편집 및 배포할 수 있는 Azure 데이터 팩터리 편집기에 대해 설명합니다." 
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
	ms.date="07/07/2015" 
	ms.author="spelluru"/>

# Azure 데이터 팩터리 편집기
Azure 데이터 팩터리 편집기는 Azure 미리 보기 포털의 일부인 경량 웹 편집기로, 모든 Azure 데이터 팩터리 엔터티의 JSON 파일을 만들기, 편집 및 배포할 수 있습니다. 그러면 데이터 팩터리 서비스와 함께 제공되는 JSON 템플릿을 사용하여 연결된 서비스, 데이터 집합 및 파이프라인을 만들 수 있습니다. 새 PowerShell인 경우, 이렇게 하면 Azure PowerShell에 설치하고 늘려 Azure 데이터 팩터리를 만들지 않아도 됩니다.

## 데이터 팩터리 편집기 시작
데이터 팩터리 편집기를 시작하려면 Azure 데이터 팩터리에 대한 **데이터 팩터리** 블레이드에서 **작성자 및 배포** 타일을 클릭합니다.

![작성 및 배포 타일][author-and-deploy-tile]

다음 그림에 표시된 것처럼 데이터 팩터리 편집기가 표시됩니다.
 
![데이터 팩터리 편집기][data=factory-editor]
 
## 연결된 서비스, 데이터 집합 및 파이프라인 새로 만들기
Azure 데이터 팩터리 엔터티를 만드는 데 사용할 수 있는 도구 모음에 단추 4개가 있습니다.
 
- **새로운 데이터 저장소**는 데이터 저장소 연결 서비스를 만듭니다. 이 단추를 클릭하면 Azure 저장소, Azure SQL 데이터베이스, 온-프레미스 SQL 서버 데이터베이스 옵션이 있는 메뉴가 표시됩니다.
- **새 계산**은 계산 연결 서비스를 만듭니다. 이 단추를 클릭하면 주문형 HDInsight 클러스터, HDInsight 클러스터, AzureML 연결 서비스 옵션이 있는 메뉴가 표시됩니다.      
- **새 데이터 집합**은 데이터 집합을 만듭니다. 이 단추를 클릭하면 Blob 테이블, Azure SQL 테이블, 온-프레미스 테이블 옵션이 표시됩니다.  
- **새 파이프라인**은 파이프라인을 만듭니다. 도구 모음에 이 단추가 표시되지 않으면 도구 모음에서 **... (줄임표)**를 클릭합니다.
 
### 저장소 연결 서비스를 만들려면
1. **새로운 데이터 저장소**를 클릭하고, 메뉴에서 옵션 중 하나를 클릭합니다.
 
	![새 데이터 저장소 메뉴][new-data-store-menu] 
2. 오른쪽의 **편집기 캔버스**에서 저장소 연결 서비스를 만들기 위한 JSON 템플릿이 나타납니다. 초안 노드는 **초안** 아래에 표시됩니다. 다음을 수행합니다.
	1. **Azure 저장소**의 경우: **<accountname >** 및 **<accountkey>**를 Azure 저장소 계정 이름 및 키로 대체합니다.
	2. **Azure SQL 데이터베이스**의 경우: **<servername>**을 Azure SQL 서버의 이름으로, **<databasename>**을 데이터베이스의 이름으로, **<username>@<servername>**을 사용자의 이름으로, **<password>**를 사용자 계정에 대한 암호로 대체합니다. 
	3. **온-프레미스 SQL 서버 데이터베이스**의 경우: **<servername>**을 온-프레미스 SQL 서버의 이름으로, **<databasename>**을 데이터베이스의 이름으로, **<username>**을 사용자의 이름으로, **<password>**를 사용자 계정에 대한 암호로 대체합니다.
4. 도구 모음에서 **배포**를 클릭하여 연결된 서비스를 배포합니다. **취소**를 클릭하여 만든 JSON 초안을 버립니다.
 
	![배포 단추][deploy-button]

1. 제목 표시줄에 배포 작업의 상태가 표시되어야 합니다.

	![배포 성공 메시지][deploy-success-message]
2. 배포 작업에 성공한 경우 왼쪽의 트리 보기에 생성된 연결 서비스가 표시되어야 합니다.
  
	![트리 뷰의 StorageLinkedService][storagelinkedservice-in-treview]

### 계산 연결 서비스를 만들려면
1. **새로운 계산**을 클릭하고, 메뉴에서 옵션 중 하나를 클릭합니다.
 
	![새 계산 메뉴][new-compute-menu] 
2. 오른쪽의 편집기 캔버스에서 계산 연결 서비스를 만들기 위한 JSON 템플릿이 나타납니다. 다음을 수행합니다.
	1. **주문형 HDInsight 클러스터**의 경우, 다음 속성에 대한 값을 지정합니다. 
		1. **clusterSize** 속성의 경우, 데이터 팩터리 서비스에서 런타임에 만드는 HDInsight 클러스터의 크기를 지정합니다. 
		2. **jobsContainer** 속성의 경우, 클러스터 로그를 저장할 기본 Blob 컨테이너의 이름을 지정합니다.
		3. **timeToLive** 속성의 경우, HDInsight 클러스터를 삭제하기 전에 허용된 유휴 시간을 지정합니다. 예를 들어, 00:05:00은 5분의 유휴 시간 후 클러스터가 삭제되어야 함을 나타냅니다.
		4. **version** 속성의 경우, 클러스터에 대 한 HDInsight 버전을 지정합니다(기본값: 버전 3.1).
		5. **linkedServiceName** 속성의 경우, Azure 저장소 연결 서비스가 HDInsight 클러스터와 연결되도록 지정합니다. 
	6. **HDInsight 클러스터**의 경우, 다음 속성에 대한 값을 지정합니다.
		1. **clusterUri** 속성의 경우, 사용자 고유의 HDInsight 클러스터에 대한 URL을 지정합니다. 
		2. **userName** 속성의 경우, 데이터 팩터리 서비스가 HDInsight 클러스터에 연결하는 데 사용해야 하는 사용자 계정을 지정합니다. 
		3. **password** 속성의 경우, 사용자 계정에 대한 암호를 지정합니다. 
		4. **linkedServiceName** 속성의 경우, HDInsight 클러스터와 연결된 Azure 저장소 연결 서비스를 지정합니다.
	5. **AzureML 연결 서비스**의 경우, 다음 속성을 지정합니다.
		1. **mlEndPoint** 속성의 경우, URL 점수 매기기 Azure 기계 학습 일괄 처리를 지정 합니다.
		2. **apiKey** 속성의 경우, 게시된 작업 영역 모델에 대한 API 키를 지정합니다.
3. 도구 모음에서 **배포**를 클릭하여 연결된 서비스를 배포합니다.

> [AZURE.NOTE]Azure 데이터 팩터리 연결 서비스를 정의하는 데 사용된 JSON 요소에 대한 설명은 MSDN Library의 항목에서 [연결 서비스][msdn-linkedservices-reference] 주제를 참조하세요.

### 새 데이터 집합을 만들려면
1. **새로운 데이터 집합**을 클릭하고, 메뉴에서 옵션 중 하나를 클릭합니다.
2. 오른쪽의 편집기 캔버스에서 데이터 집합을 만들기 위한 JSON 템플릿이 나타납니다. 다음을 수행합니다. 
	1. **Blob 테이블**의 경우, 다음 속성 값을 지정합니다.
	2. **Azure SQL 테이블** 또는 **온-프레미스 테이블**의 경우, 다음 속성에 대한 값을 지정합니다. 
		1. **위치** 섹션: 
			2. **linkedServiceName** 속성의 경우, Azure SQL/온-프레미스 SQL Server 데이터베이스를 참조하는 연결 서비스의 이름을 지정합니다.
			2. **tableName** 속성의 경우, 연결 서비스가 참조하는 Azure SQL 데이터베이스 인스턴스/온-프레미스 SQL 서버의 테이블 이름을 지정합니다.
		3. **가용성** 섹션:
			1. **frequency** 속성의 경우, 데이터 조각 프로덕션에 대한 시간 단위를 지정합니다. 지원되는 빈도 값은 분, 시간, 일, 주, 월입니다.
			2. **interval** 속성의 경우, 정의된 빈도 내에서 간격을 지정합니다. **frequency**는 **시간**으로 설정하고 **interval**은 **1**로 설정되면 새 데이터 조각이 매시간 생성해야 함을 나타냅니다. 
		3. **구조** 섹션: 
			1. 다음 예제와 같이 열의 이름과 형식을 지정합니다.
				
				    "structure":
	        		[
	                	{ "name": "FirstName", "type": "String"},
	                	{ "name": "LastName", "type": "String"}
	        		],
	     
> [AZURE.NOTE]Azure 데이터 팩터리 테이블을 정의하는 데 사용된 JSON 요소에 대한 설명은 MSDN Library에서 [테이블][msdn-tables-reference] 주제를 참조하세요.
 		           
### 파이프라인을 만들고 활성화하려면 
1. 도구 모음에서 **새 파이프라인**을 클릭합니다. **새 파이프라인** 단추가 없으면 **... (줄임표)**를 클릭하여 봅니다.   
2. 오른쪽의 편집기 캔버스에서 파이프라인을 만들기 위한 JSON 템플릿이 나타납니다. 다음을 수행합니다. 
	1. **description** 속성의 경우, 파이프라인에 대한 설명을 지정합니다.
	2. **activities** 섹션에서 파이프라인에 활동을 추가합니다. 예:
	 
			"activities":	
			[
				{
					"name": "CopyFromBlobToSQL",
					"description": "Push Regional Effectiveness Campaign data to Azure SQL",
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
	3. **start** 속성의 경우, 데이터 처리가 시작되거나 데이터 조각이 처리되는 시기를 지정합니다. 예: 2014-05-01T00:00:00Z
	4. **end** 속성의 경우, 데이터 처리의 종료 시점을 지정합니다. 예: 2014-05-01T00:00:00Z       

> [AZURE.NOTE]Azure 데이터 팩터리 파이프라인을 정의하는 데 사용된 JSON 요소에 대한 설명은 MSDN Library에서 [파이프라인 및 활동][msdn-pipelines-reference] 주제를 참조하세요.

## 파이프라인 JSON에 활동 정의를 추가하려면
도구 모음에서 **활동 추가**를 클릭하여 활동 정의를 파이프라인 JSON에 추가할 수 있습니다. 이 단추를 클릭하면 파이프라인에 추가하려는 작업의 유형을 선택합니다.

![활동 옵션 추가][add-activity-options]

Azure SQL 데이터베이스에서 Azure Blob 저장소로 데이터를 복사하고 HDInsight 클러스터에서 Pig 스크립트를 사용하여 Blob 저장소의 데이터를 처리하려면, 먼저 **복사 작업**을 추가한 다음 **Pig 작업**을 파이프라인에 추가합니다. 이렇게 하면 파이프라인 JSON의 활동 섹션에 두 섹션이 만들어집니다. Pig 작업은 Pig 변환을 포함한 HDInsight 활동일 뿐입니다.

	"activities": [
    	{
    		"name": "CopyFromTabletoBlob",
	        "type": "CopyActivity",
			...
		}
		{
			"name": "ProcessBlobDataWithPigScript",
            "type": "HDInsightActivity",
			...
			"transformation": {
            	"type": "Pig",
				...
			}
		}
	]

## 파이프라인 시작
JSON에서 start 및 end 속성에 대한 값을 지정하여 파이프라인의 시작 날짜-시간 및 종료 날짜-시간을 지정할 수 있습니다.

 	{
	    "name": "ADFTutorialPipeline",
	    "properties":
	    {   
	        "description" : "Copy data from a blob to Azure SQL table",
	        "activities":   
	        [
				...
	        ],
	
	        "start": "2015-02-13T00:00:00Z",
	        "end": "2015-02-14T00:00:00Z",
	        "isPaused": false
	    }
	} 
  
## 편집기의 초안
초안을 사용하면 컨텍스트 전환이나 데이터 팩터리에서 다른 엔터티를 탐색 중인 경우 작업을 임시로 저장할 수 있습니다. 초안의 수명은 브라우저 세션과 연관됩니다. 브라우저를 닫거나 다른 컴퓨터를 사용하는 경우 초안을 사용하지 못할 수 있습니다.

## 데이터 팩터리 엔터티의 JSON 초안을 삭제 하려면
도구 모음에서 **취소** 단추를 클릭하여 Azure 데이터 팩터리 엔터티의 JSON 정의를 버릴 수 있습니다.

## 데이터 팩터리 엔터티를 복제하려면
트리 뷰에서 엔티티를 선택하고 도구 모음의 **복제** 단추를 클릭하여 기존 Azure 데이터 팩터리 엔터티(연결 서비스, 테이블 또는 파이프라인)를 복제할 수 있습니다.

![데이터 팩터리 엔터티 복제][clone-datafactory-entity]

트리 보기의 **초안** 노드에 생성된 새 초안이 표시됩니다.

## 데이터 팩터리 엔터티를 삭제하려면
Azure 데이터 팩터리 엔터티(연결 서비스, 테이블 또는 파이프라인)를 삭제하고, 트리 뷰에서 엔터티를 선택하고 도구 모음에서 **삭제**를 클릭하거나(또는) 엔터티를 마우스 오른쪽 단추로 클릭하고 **삭제**를 클릭합니다.

![데이터 팩터리 엔터티 삭제][delete-datafactory-entity]

## 참고 항목
데이터 팩터리 편집기를 사용하여 Azure 데이터 팩터리를 만드는 단계별 지침은 [Azure 데이터 팩터리 시작][data-factory-get-started]을 참조하세요.

[msdn-tables-reference]: https://msdn.microsoft.com/library/dn835002.aspx
[msdn-linkedservices-reference]: https://msdn.microsoft.com/library/dn834986.aspx
[msdn-pipelines-reference]: https://msdn.microsoft.com/library/dn834988.aspx

[data-factory-get-started]: data-factory-get-started.md

[author-and-deploy-tile]: ./media/data-factory-editor/author-and-deploy-tile.png
[data=factory-editor]: ./media/data-factory-editor/data-factory-editor.png
[new-data-store-menu]: ./media/data-factory-editor/new-data-store-menu.png
[new-compute-menu]: ./media/data-factory-editor/new-compute-menu.png
[deploy-button]: ./media/data-factory-editor/deploy-button.png
[deploy-success-message]: ./media/data-factory-editor/deploy-success-message.png
[storagelinkedservice-in-treview]: ./media/data-factory-editor/storagelinkedservice-in-treeview.png
[delete-datafactory-entity]: ./media/data-factory-editor/delete-datafactory-entity.png
[clone-datafactory-entity]: ./media/data-factory-editor/clone-datafactory-entity.png
[add-activity-options]: ./media/data-factory-editor/add-activity-options.png

<!---HONumber=August15_HO6-->