<properties
	pageTitle="데이터 팩터리 편집기를 사용하여 첫 번째 Azure Data Factory 파이프라인 빌드"
	description="이 자습서에서는 Azure 포털의 데이터 팩터리 편집기를 사용하여 샘플 Azure Data Factory 파이프라인을 만듭니다."
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

# 데이터 팩터리 편집기를 사용하여 첫 번째 Azure Data Factory 파이프라인 빌드(Azure 포털)
> [AZURE.SELECTOR]
- [Tutorial Overview](data-factory-build-your-first-pipeline.md)
- [Using Data Factory Editor](data-factory-build-your-first-pipeline-using-editor.md)
- [Using PowerShell](data-factory-build-your-first-pipeline-using-powershell.md)
- [Using Visual Studio](data-factory-build-your-first-pipeline-using-vs.md)


이 문서에서는 [Azure Preview 포털](https://portal.azure.com/)을 사용하여 첫 번째 파이프라인을 만드는 방법을 알아봅니다. 이 자습서는 다음과 같은 단계로 구성됩니다.

1.	데이터 팩터리 만들기
2.	연결된 서비스(데이터 저장소, 계산) 및 데이터 집합 만들기
3.	파이프라인 만들기

이 문서는 Azure Data Factory 서비스에 대한 개념적 개요를 제공하지 않습니다. 서비스에 대한 자세한 개요는 [Azure Data Factory 소개](data-factory-introduction.md) 문서를 참조하세요.

> [AZURE.IMPORTANT][자습서 개요](data-factory-build-your-first-pipeline.md) 문서를 살펴보고 이 자습서를 수행하기 전에 필수 단계를 완료합니다.

## 1단계: 데이터 팩터리 만들기

1.	[Azure Preview 포털](http://portal.azure.com/)에 로그인한 후 다음을 수행합니다.
	1.	왼쪽 메뉴에서 **새로 만들기**를 클릭합니다. 
	2.	**만들기** 블레이드에서 **데이터 분석**을 클릭합니다.
	3.	**데이터 분석** 블레이드에서 **데이터 팩터리**를 클릭합니다.

		![블레이드 만들기](./media/data-factory-build-your-first-pipeline-using-editor/create-blade.png)

2.	**새 데이터 팩터리** 블레이드에서 **DataFactoryMyFirstPipeline**을 이름으로 입력합니다.

	![새 데이터 팩터리 블레이드](./media/data-factory-build-your-first-pipeline-using-editor/new-data-factory-blade.png)

	> [AZURE.IMPORTANT]Azure Data Factory 이름은 전역적으로 고유해야 합니다. **데이터 팩터리 이름 “DataFactoryMyFirstPipeline”를 사용할 수 없습니다.** 오류가 표시되는 경우 데이터 팩터리 이름을 변경하고(예: yournameDataFactoryMyFirstPipeline) 다시 만듭니다. 데이터 팩터리 아티팩트에 대한 명명 규칙은 [데이터 팩터리 - 명명 규칙](data-factory-naming-rules.md) 항목을 참조하세요.
	>  
	> 데이터 팩터리의 이름은 나중에 DNS 이름으로 표시되므로 공개적으로 등록될 수도 있습니다.

3.	만들어 놓은 리소스 그룹이 없으면 리소스 그룹을 만들어야 합니다. 다음을 수행합니다.
	1.	**리소스 그룹 이름**을 클릭합니다.
	2.	**리소스 그룹** 블레이드에서 **새 리소스 그룹 만들기**를 선택합니다.
	3.	**리소스 그룹 만들기** 블레이드에서 **이름**으로 **ADF**를 입력합니다.
	4.	**확인**을 클릭합니다.
	
		![리소스 그룹 만들기](./media/data-factory-build-your-first-pipeline-using-editor/create-resource-group.png)
4.	리소스 그룹을 선택한 후에 데이터 팩터리를 만들려는 구독을 사용하고 있는지 확인합니다.
5.	**새 데이터 팩터리** 블레이드에서 **만들기**를 클릭합니다.
6.	다음과 같이 Azure Preview 포털의 **시작 보드**에 생성되는 데이터 팩터리가 표시됩니다.   

	![데이터 팩터리 만들기 상태](./media/data-factory-build-your-first-pipeline-using-editor/creating-data-factory-image.png)
7. 축하합니다. 첫 번째 데이터 팩터리 만들기가 완료되었습니다. 데이터 팩터리 만들기를 완료한 후에는 데이터 팩터리 페이지가 표시되며 여기에 데이터 팩터리의 내용이 표시됩니다. 	

	![데이터 팩터리 블레이드](./media/data-factory-build-your-first-pipeline-using-editor/data-factory-blade.png)

이후 단계에서는 이 자습서에서 사용할 연결된 서비스, 데이터 집합, 파이프라인을 만드는 방법을 알아봅니다.

## 2단계: 연결된 서비스 및 데이터 집합 만들기
이 단계에서는 Azure 저장소 계정과 주문형 Azure HDInsight 클러스터를 데이터 팩터리에 연결한 후 Hive 처리의 출력 데이터를 나타낼 데이터 집합을 만듭니다.

### Azure 저장소 연결된 서비스 만들기
1.	**DataFactoryFirstPipeline**의 **데이터 팩터리** 블레이드에서 **작성 및 배포**를 클릭합니다. 데이터 팩터리 편집기가 시작됩니다. 
	 
	![작성 및 배포 타일](./media/data-factory-build-your-first-pipeline-using-editor/data-factory-author-deploy.png)
2.	**새 데이터 저장소**를 클릭하고 **Azure 저장소**를 선택합니다.
	
	![Azure 저장소 연결된 서비스](./media/data-factory-build-your-first-pipeline-using-editor/azure-storage-linked-service.png)

	편집기에 Azure 저장소 연결된 서비스를 만들기 위한 JSON 스크립트가 표시됩니다. 
4. **계정 이름**을 Azure 저장소 계정 이름으로 변경하고 **계정 키**를 Azure 저장소 계정의 선택키로 변경합니다. 저장소 액세스 키를 확보하는 방법을 알아보려면 [저장소 액세스 키 보기, 복사 및 다시 생성](../storage/storage-create-storage-account.md#view-copy-and-regenerate-storage-access-keys)을 참조하세요.
5. 명령 모음에서 **배포**를 클릭하여 연결된 서비스를 배포합니다.

	![배포 단추](./media/data-factory-build-your-first-pipeline-using-editor/deploy-button.png)

### Azure HDInsight 연결된 서비스 만들기
Hive 스크립트를 실행하는데 사용될 주문형 HDInsight 클러스터의 연결된 서비스를 만듭니다.

1. **데이터 팩터리 편집기**의 명령 모음에서 **새 계산**을 클릭하고 **주문형 HDInsight 클러스터**를 선택합니다.

	![새 계산](./media/data-factory-build-your-first-pipeline-using-editor/new-compute-menu.png)
2. 아래 코드 조각을 복사하여 Draft-1 창에 붙여넣습니다. JSON 코드 조각은 주문형 HDInsight 클러스터를 만드는데 사용될 속성을 설명합니다. 

		{
		  "name": "HDInsightOnDemandLinkedService",
		  "properties": {
		    "type": "HDInsightOnDemand",
		    "typeProperties": {
		      "version": "3.1",
		      "clusterSize": 1,
		      "timeToLive": "00:30:00",
		      "linkedServiceName": "StorageLinkedService"
		    }
		  }
		}
	
	다음 테이블은 코드 조각에 사용된 JSON 속성에 대한 설명을 제공합니다.
	
	속성 | 설명
	-------- | -----------
	버전 | 생성되는 HDInsight 버전을 3.1로 지정합니다. 
	ClusterSize | 노드가 하나인 HDInsight 클러스터를 만듭니다. 
	TimeToLive | HDInsight 클러스터가 삭제되기 전 유휴 시간을 지정합니다.
	linkedServiceName | HDInsight에 의해 생성되는 로그를 저장하는데 사용될 저장소 계정을 지정합니다.
3. 명령 모음에서 **배포**를 클릭하여 연결된 서비스를 배포합니다. 
4. 왼쪽의 트리 뷰에서 StorageLinkedService 및 HDInsightOnDemandLinkedService가 모두 표시되는지 확인합니다.

	![연결된 서비스와 트리 뷰](./media/data-factory-build-your-first-pipeline-using-editor/tree-view-linked-services.png)
 
### 출력 데이터 집합 만들기
Azure Blob 저장소에 저장된 데이터를 나타내는 출력 데이터 집합을 만듭니다.

1. **데이터 팩터리 편집기**의 명령 모음에서 **새 데이터 집합**을 클릭하고 **Azure Blob 저장소**를 선택합니다.  

	![새 데이터 집합](./media/data-factory-build-your-first-pipeline-using-editor/new-data-set.png)
2. 아래 코드 조각을 복사하여 Draft-1 창에 붙여넣습니다. JSON 코드 조각에서 **AzureBlobOutput**이라는 데이터 집합을 만들고 Hive 스크립트에 의해 생성될 데이터의 구조를 지정합니다. 또한 결과가 **data**라는 Blob 컨테이너와 **partitioneddata** 폴더에 저장되도록 지정합니다. **가용성** 섹션은 출력 데이터 집합이 월 단위로 생성되도록 지정합니다.
	
		{
		  "name": "AzureBlobOutput",
		  "properties": {
		    "type": "AzureBlob",
		    "linkedServiceName": "StorageLinkedService",
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

3. 명령 모음에서 **배포**를 클릭하여 새로 만든 데이터 집합을 배포합니다.
4. 데이터 집합이 성공적으로 만들어졌는지 확인합니다.

	![연결된 서비스와 트리 뷰](./media/data-factory-build-your-first-pipeline-using-editor/tree-view-data-set.png)

## 3단계: 첫 번째 파이프라인 만들기
이 단계에서는 첫 번째 파이프라인을 만듭니다.

1. **데이터 팩터리 편집기**에서 **줄임표(…)**를 클릭하고 **새 파이프라인**을 클릭합니다.
	
	![새 파이프라인 단추](./media/data-factory-build-your-first-pipeline-using-editor/new-pipeline-button.png)
2. 아래 코드 조각을 복사하여 Draft-1 창에 붙여넣습니다.

	> [AZURE.IMPORTANT]**storageaccountname**을 JSON의 저장소 계정 이름으로 변경합니다.

		{
		  "name": "MyFirstPipeline",
		  "properties": {
		    "description": "My first Azure Data Factory pipeline",
		    "activities": [
		      {
		        "type": "HDInsightHive",
		        "typeProperties": {
		          "scriptPath": "script/partitionweblogs.hql",
		          "scriptLinkedService": "StorageLinkedService",
		          "defines": {
		            "partitionedtable": "wasb://data@<storageaccountname>.blob.core.windows.net/partitioneddata"
		          }
		        },
		        "outputs": [
		          {
		            "name": "AzureBlobOutput"
		          }
		        ],
                "scheduler": {
                    "frequency": "Month",
                    "interval": 1
                },
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
	
	Hive 스크립트 파일 **partitionweblogs.hql**은 Azure 저장소 계정(**StorageLinkedService**라는 scriptLinkedService에 의해 지정되는)과 **script**라는 컨테이너에 저장됩니다.

	**defines** 섹션은 Hive 스크립트에 Hive 구성 값(예: ${hiveconf:PartitionedData})으로 전달되는 런타임 설정을 지정하는 데 사용됩니다.

	파이프라인의 **start** 및 **end** 속성은 파이프라인의 활성 기간을 지정합니다.

	작업 JSON에서 Hive 스크립트가 연결된 서비스 **HDInsightOnDemandLinkedService**에 지정된 컴퓨터에서 실행되도록 지정합니다.
3. 명령 모음에서 **배포**를 클릭하여 파이프라인을 배포합니다.
4. 트리 뷰에 파이프라인이 표시되는지 확인합니다.

	![파이프라인과 트리 뷰](./media/data-factory-build-your-first-pipeline-using-editor/tree-view-pipeline.png)
5. 축하합니다. 첫 번째 파이프라인 만들기가 완료되었습니다.
6. **X**를 클릭하여 데이터 팩터리 편집기 블레이드를 닫고 데이터 팩터리 블레이드로 돌아가서 **다이어그램**을 클릭합니다.
  
	![다이어그램 타일](./media/data-factory-build-your-first-pipeline-using-editor/diagram-tile.png)
7. 다이어그램 뷰에 파이프라인의 개요와 이 자습서에 사용된 데이터 집합이 표시됩니다.
	
	![다이어그램 뷰](./media/data-factory-build-your-first-pipeline-using-editor/diagram-view-2.png) 
8. 다이어그램 뷰에서 **AzureBlobOutput** 데이터 집합을 두 번 클릭합니다. 현재 처리 중인 조각이 표시됩니다.

	![데이터 집합](./media/data-factory-build-your-first-pipeline-using-editor/dataset-blade.png)
9. 처리가 완료되면 **Ready**(준비) 상태에 조각이 표시됩니다. 주문형 HDInsight 클러스터 만들기는 일반적으로 시간이 소요됩니다. 

	![데이터 집합](./media/data-factory-build-your-first-pipeline-using-editor/dataset-slice-ready.png)	
10. 조각이 **Ready**(준비) 상태에 있으면 Blob 저장소의 **data** 컨테이너에 있는 **partitioneddata** 폴더에서 출력 데이터를 확인합니다.  
 

 

## 다음 단계
이 문서에서 파이프라인과 주문형 HDInsight 클러스터에서 Hive 스크립트를 실행하는 변환 작업(HDInsight 작업)을 만들었습니다. 복사 작업을 사용하여 Azure Blob에서 Azure SQL로 데이터를 복사하는 방법은 [자습서: Azure Blob에서 Azure SQL로 데이터 복사](./data-factory-get-started.md)를 참조하세요.
  

<!---HONumber=AcomDC_1125_2015-->