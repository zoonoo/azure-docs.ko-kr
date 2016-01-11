<properties
	pageTitle="Azure 데이터 팩터리 시작(Azure PowerShell)"
	description="이 자습서에서는 Azure PowerShell을 사용하여 샘플 Azure Data Factory 파이프라인을 만듭니다."
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
	ms.date="12/18/2015"
	ms.author="spelluru"/>

# Azure 데이터 팩터리 시작(Azure PowerShell)
> [AZURE.SELECTOR]
- [Tutorial Overview](data-factory-build-your-first-pipeline.md)
- [Using Data Factory Editor](data-factory-build-your-first-pipeline-using-editor.md)
- [Using PowerShell](data-factory-build-your-first-pipeline-using-powershell.md)
- [Using Visual Studio](data-factory-build-your-first-pipeline-using-vs.md)
- [Using Resource Manager Template](data-factory-build-your-first-pipeline-using-arm.md)


이 문서에서는 Azure PowerShell을 사용하여 첫 번째 Azure Data Factory를 만드는 방법을 알아봅니다.


## 필수 조건
자습서 개요 항목에 나열된 필수 조건 외에도 다음이 설치되어 있어야 합니다.

- [자습서 개요](data-factory-build-your-first-pipeline.md) 문서를 읽고 진행하기 전에 필수 구성 요소 단계를 완료**해야** 합니다.
- **Azure PowerShell**. [Azure PowerShell을 설치 및 구성하는 방법](../powershell-install-configure.md) 문서의 지침을 수행하여 컴퓨터에 Azure PowerShell의 최신 버전을 설치합니다.
- (선택 사항) 이 문서는 모든 데이터 팩터리 cmdlet을 다루지 않습니다. 데이터 팩터리 cmdlet에 대한 포괄적인 설명서는 [데이터 팩터리 Cmdlet 참조](https://msdn.microsoft.com/library/dn820234.aspx)(영문)를 참조하세요. 

**버전 < 1.0**인 Azure PowerShell을 사용하는 경우 [여기][cmdlet-reference]에 설명된 cmdlet을 사용해야 합니다. 또한 데이터 팩터리 cmdlet을 사용하기 전에 다음 명령을 실행해야 합니다.
 
1. Azure PowerShell을 시작하고 다음 명령을 실행합니다. 이 자습서를 마칠 때까지 Azure PowerShell을 열어 두세요. 닫은 후 다시 여는 경우 이러한 명령을 다시 실행해야 합니다.
	1. **Add-AzureAccount**를 실행하고 Azure 포털에 로그인하는 데 사용하는 사용자 이름 및 암호를 입력합니다.
	2. **Get-AzureSubscription**을 실행하여 이 계정의 모든 구독을 확인합니다.
	3. **Select-AzureSubscription**을 실행하여 사용하려는 구독을 선택합니다. 이 구독은 Azure 포털에서 사용한 것과 같아야 합니다.
4. AzureResourceManager 모드로 전환합니다. Azure 데이터 팩터리 cmdlet은 **Switch-AzureMode AzureResourceManager** 모드에서 사용할 수 있습니다.


## 1단계: 데이터 팩터리 만들기

이 단계에서는 Azure PowerShell을 사용하여 **FirstDataFactoryPSH**라는 Azure Data Factory를 만듭니다. 데이터 팩터리에는 하나 이상의 파이프라인이 포함될 수 있습니다. 파이프라인에는 하나 이상의 작업이 있을 수 있습니다. 예를 들어 원본에서 대상 데이터 저장소에 데이터를 복사하는 복사 작업 및 입력 데이터를 제품 출력 데이터로 변환하는 Hive 스크립트를 실행하는 HDInsight Hive 작업입니다. 이 단계에서는 데이터 팩터리 만들기를 시작하겠습니다.

1. Azure PowerShell을 시작하고 다음 명령을 실행합니다. 이 자습서를 마칠 때까지 Azure PowerShell을 열어 두세요. 닫은 후 다시 여는 경우 이러한 명령을 다시 실행해야 합니다.
	- **Login-AzureRmAccount**를 실행하고 Azure 포털에 로그인하는 데 사용하는 사용자 이름 및 암호를 입력합니다.  
	- **Get-AzureSubscription**을 실행하여 이 계정의 모든 구독을 확인합니다.
	- **Select-AzureSubscription <Name of the subscription>**을 실행하여 사용하려는 구독을 선택합니다. 이 구독은 Azure 포털에서 사용한 것과 같아야 합니다.
3. 다음 명령을 실행하여 **ADFTutorialResourceGroup**이라는 Azure 리소스 그룹을 만듭니다.

		New-AzureRmResourceGroup -Name ADFTutorialResourceGroup  -Location "West US"

	이 자습서의 일부 단계에서는 ADFTutorialResourceGroup이라는 리소스 그룹을 사용한다고 가정합니다. 다른 리소스 그룹을 사용하는 경우 이 자습서에서 ADFTutorialResourceGroup 대신 해당 리소스 그룹을 사용해야 합니다.
4. **New-AzureRmDataFactory** cmdlet을 실행하여 **FirstDataFactoryPSH**라는 데이터 팩터리를 만듭니다.  

		New-AzureRmDataFactory -ResourceGroupName ADFTutorialResourceGroup -Name FirstDataFactoryPSH –Location "West US"

	> [AZURE.IMPORTANT]Azure Data Factory 이름은 전역적으로 고유해야 합니다. **데이터 팩터리 이름 “FirstDataFactoryPSH”를 사용할 수 없습니다.** 오류가 표시되는 경우 이름을 변경합니다.(예: yournameFirstDataFactoryPSH) 이 자습서의 단계를 수행하는 동안 ADFTutorialFactoryPSH 대신 이 이름을 사용합니다. 데이터 팩터리 아티팩트에 대한 명명 규칙은 [데이터 팩터리 - 명명 규칙](data-factory-naming-rules.md) 항목을 참조하세요.
	> 
	> 데이터 팩터리의 이름은 나중에 DNS 이름으로 표시되므로 공개적으로 등록될 수도 있습니다.

파이프라인을 만들기 전에 먼저 몇 가지 데이터 팩터리 엔터티를 만들어야 합니다. 먼저 데이터 저장소에 데이터 저장소/계산을 연결하는 연결된 서비스를 만들고 연결된 데이터 저장소에서 데이터를 나타내는 입력 및 출력 데이터 집합을 정의한 다음 이러한 데이터 집합을 사용하는 작업을 통해 파이프라인을 만듭니다.

## 2단계: 연결된 서비스 만들기 
이 단계에서는 Azure 저장소 계정 및 주문형 Azure HDInsight 클러스터를 데이터 팩터리에 연결합니다. Azure 저장소 계정은 이 샘플의 파이프라인에 대한 입력 및 출력 데이터를 가집니다. HDInsight 연결된 서비스는 이 샘플에서 파이프라인의 활동에 지정된 Hive 스크립트를 실행하는 데 사용됩니다. 시나리오에 사용되는 데이터 저장소/계산 서비스를 식별하고 연결된 서비스를 만들어 해당 서비스를 데이터 팩터리에 연결해야 합니다.

### Azure 저장소 연결된 서비스 만들기
이 단계에서는 Azure 저장소 계정을 데이터 팩터리에 연결합니다. 이 자습서에서는 동일한 Azure 저장소 계정을 사용하여 입력/출력 데이터 및 HQL 스크립트 파일을 저장합니다.

1. C:\ADFGetStarted 폴더에 다음과 같은 내용으로 StorageLinkedService.json이라는 JSON 파일을 만듭니다. 아직 없는 경우 ADFGetStarted 폴더를 만듭니다.

		{
	    	"name": "StorageLinkedService",
	    	"properties": {
	        	"type": "AzureStorage",
	        	"description": "",
	        	"typeProperties": {
	            	"connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
	        	}
	    	}
		}

	**계정 이름**을 Azure 저장소 계정 이름으로 변경하고 **계정 키**를 Azure 저장소 계정의 선택키로 변경합니다. 저장소 액세스 키를 확보하는 방법을 알아보려면 [저장소 액세스 키 보기, 복사 및 다시 생성](http://azure.microsoft.com/documentation/articles/storage-create-storage-account/#view-copy-and-regenerate-storage-access-keys)을 참조하세요.

2. Azure PowerShell에서 ADFGetStarted 폴더로 전환합니다.
3. **New-AzureRmDataFactoryLinkedService** cmdlet을 사용하여 연결된 서비스를 만들 수 있습니다. 이 cmdlet 및 이 자습서에서 사용하는 다른 데이터 팩터리 cmdlet의 경우 *ResourceGroupName* 및 *DataFactoryName* 매개 변수의 값을 전달해야 합니다. 또는 **Get-AzureRmDataFactory**를 사용하여 **DataFactory** 개체를 가져온 다음 cmdlet을 실행할 때마다 *ResourceGroupName* 및 *DataFactoryName*을 입력하지 않고 개체를 전달할 수 있습니다. 다음 명령을 실행하여 **Get-AzureRmDataFactory** cmdlet의 출력을 **$df** 변수에 할당합니다.

		$df=Get-AzureRmDataFactory -ResourceGroupName ADFTutorialResourceGroup -Name FirstDataFactoryPSH

4. 이제 **New-AzureRmDataFactoryLinkedService** cmdlet을 실행하여 연결된 **StorageLinkedService** 서비스를 만듭니다.

		New-AzureRmDataFactoryLinkedService $df -File .\StorageLinkedService.json

	**Get-AzureRmDataFactory** cmdlet을 실행하고 출력을 **$df** 변수에 할당하지 않은 경우 *ResourceGroupName* 및 *DataFactoryName* 매개 변수의 값을 다음과 같이 지정해야 합니다.

		New-AzureRmDataFactoryLinkedService -ResourceGroupName ADFTutorialResourceGroup -DataFactoryName FirstDataFactoryPSH -File .\StorageLinkedService.json

	자습서 중에 Azure PowerShell을 닫은 경우 자습서를 완료하기 위해 다음에 Azure PowerShell을 시작할 때 **Get-AzureRmDataFactory** cmdlet을 실행해야 합니다.

### Azure HDInsight 연결된 서비스 만들기
이 단계에서는 데이터 팩터리에 주문형 HDInsight 클러스터를 연결합니다. HDInsight 클러스터는 런타임 시 자동으로 만들어지며 처리가 완료되고 지정된 시간 동안 유휴 상태를 유지한 후에 삭제됩니다. 주문형 HDInsight 클러스터를 사용하는 대신 고유의 HDInsight 클러스터를 사용할 수 있습니다. 자세한 내용은 [연결된 서비스 계산](data-factory-compute-linked-services.md)을 참조하세요.

1. **C:\ADFGetStarted** 폴더에 다음과 같은 내용으로 **HDInsightOnDemandLinkedService**.json이라는 JSON 파일을 만듭니다.

		{
		  "name": "HDInsightOnDemandLinkedService",
		  "properties": {
		    "type": "HDInsightOnDemand",
		    "typeProperties": {
		      "version": "3.2",
		      "clusterSize": 1,
		      "timeToLive": "00:30:00",
		      "linkedServiceName": "StorageLinkedService"
		    }
		  }
		}

	다음 테이블은 코드 조각에 사용된 JSON 속성에 대한 설명을 제공합니다.

	| 속성 | 설명 |
	| :------- | :---------- |
	| 버전 | 생성되는 HDInsight 버전을 3.2로 지정합니다. | 
	| ClusterSize | 노드가 하나인 HDInsight 클러스터를 만듭니다. | 
	| TimeToLive | HDInsight 클러스터가 삭제되기 전 유휴 시간을 지정합니다. |
	| linkedServiceName | HDInsight에 의해 생성되는 로그를 저장하는데 사용될 저장소 계정을 지정합니다. |

2. **New-AzureRmDataFactoryLinkedService** cmdlet을 실행하여 HDInsightOnDemandLinkedService라는 연결된 서비스를 만듭니다.

		New-AzureRmDataFactoryLinkedService $df -File .\HDInsightOnDemandLinkedService.json


## 3단계: 데이터 집합 만들기
이 단계에서는 Hive 처리에 대한 입력 및 출력 데이터를 나타낼 데이터 집합을 만듭니다. 이러한 데이터 집합은 이 자습서의 앞부분에서 만든 **StorageLinkedService**를 참조합니다. 연결된 서비스는 Azure 저장소 계정을 가리키고 데이터 집합은 입력 및 출력 데이터를 가진 저장소의 컨테이너, 폴더, 파일 이름을 지정합니다.

### 입력 데이터 집합 만들기
1. **C:\ADFGetStarted** 폴더에 다음과 같은 내용으로 **InputTable.json**이라는 JSON 파일을 만듭니다.

		{
			"name": "AzureBlobInput",
		    "properties": {
		        "type": "AzureBlob",
		        "linkedServiceName": "StorageLinkedService",
		        "typeProperties": {
		            "fileName": "input.log",
		            "folderPath": "adfgetstarted/inputdata",
		            "format": {
		                "type": "TextFormat",
		                "columnDelimiter": ","
		            }
		        },
		        "availability": {
		            "frequency": "Month",
		            "interval": 1
		        },
		        "external": true,
		        "policy": {}
		    }
		} 

	앞의 예제에서 **AzureBlobOutput**이라는 데이터 집합을 만들고 JSON 코드 조각을 지정하며 파이프라인의 작업에 대한 입력 데이터를 나타내는 **AzureBlobInput**라는 데이터 집합을 만들었습니다. 또한 결과가 **adfgetstarted**라는 Blob 컨테이너 및 **inputdata**라는 폴더에 저장되도록 지정합니다.

	다음 테이블은 코드 조각에 사용된 JSON 속성에 대한 설명을 제공합니다.

	| 속성 | 설명 |
	| :------- | :---------- |
	| type | Azure blob 저장소에 데이터가 있기 때문에 형식 속성은 AzureBlob로 설정됩니다. |  
	| linkedServiceName | 이전에 만든 StorageLinkedService를 참조합니다. |
	| fileName | 이 속성은 선택 사항입니다. 이 속성을 생략하면 folderPath의 모든 파일을 선택합니다. 이 경우에 input.log만 처리됩니다. |
	| type | 로그 파일이 텍스트 형식이므로 TextFormat을 사용합니다. | 
	| columnDelimiter | 로그 파일의 열은 ,(쉼표)로 구분됩니다. |
	| frequency/interval | 월 및 간격을 설정한 빈도가 1인 경우 입력 조각은 매월 제공됩니다. | 
	| external | 입력 데이터가 데이터 팩터리 서비스에서 생성되지 않는 경우 이 속성은 true로 설정됩니다. | 

2. Azure PowerShell에서 다음 명령을 실행하여 데이터 팩터리 데이터 집합을 만듭니다.

		New-AzureRmDataFactoryDataset $df -File .\InputTable.json

### 출력 데이터 집합 만들기
Azure Blob 저장소에 저장된 출력 데이터를 나타내는 출력 데이터 집합을 만듭니다.

1. **C:\ADFGetStarted** 폴더에 다음과 같은 내용으로 **OutputTable.json**이라는 JSON 파일을 만듭니다.

		{
		  "name": "AzureBlobOutput",
		  "properties": {
		    "type": "AzureBlob",
		    "linkedServiceName": "StorageLinkedService",
		    "typeProperties": {
		      "folderPath": "adfgetstarted/partitioneddata",
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

	JSON 코드 조각에서 **AzureBlobOutput**이라는 데이터 집합을 만들고 Hive 스크립트에 의해 생성될 데이터의 구조를 지정합니다. 또한 결과가 **adfgetstarted**라는 Blob 컨테이너와 **partitioneddata**라는 폴더에 저장되도록 지정합니다. **가용성** 섹션은 출력 데이터 집합이 월 단위로 생성되도록 지정합니다.

2. Azure PowerShell에서 다음 명령을 실행하여 데이터 팩터리 데이터 집합을 만듭니다.

		New-AzureRmDataFactoryDataset $df -File .\OutputTable.json

## 3단계: 첫 번째 파이프라인 만들기
이 단계에서는 **HDInsightHive** 작업을 사용하여 첫 번째 파이프라인을 만듭니다. 입력 조각이 매월(빈도: 월, 간격: 1)이고 출력 조각이 매월 생성되며 작업에 대한 스케줄러 속성도 매월로 설정됩니다.(아래 참조) 출력 데이터 집합 및 작업 스케줄러에 대한 설정이 일치해야 합니다. 이번에는 출력 데이터 집합이 내용을 결정하므로 활동이 출력을 생성하지 않는 경우 출력 데이터 집합을 만들어야 합니다. 활동이 입력을 가져오지 않으면 입력 데이터 집합 만들기를 건너뛸 수 있습니다. 다음 JSON에서 사용되는 속성은 이 섹션의 끝에 설명되어 있습니다.


1. C:\ADFGetStarted 폴더에 다음과 같은 내용으로 MyFirstPipelinePSH.json이라는 JSON 파일을 만듭니다.

	> [AZURE.IMPORTANT]**storageaccountname**을 JSON의 저장소 계정 이름으로 변경합니다.
		
		{
		    "name": "MyFirstPipeline",
		    "properties": {
		        "description": "My first Azure Data Factory pipeline",
		        "activities": [
		            {
		                "type": "HDInsightHive",
		                "typeProperties": {
		                    "scriptPath": "adfgetstarted/script/partitionweblogs.hql",
		                    "scriptLinkedService": "StorageLinkedService",
		                    "defines": {
		                        "inputtable": "wasb://adfgetstarted@<storageaccountname>.blob.core.windows.net/inputdata",
		                        "partitionedtable": "wasb://adfgetstarted@<storageaccountname>.blob.core.windows.net/partitioneddata"
		                    }
		                },
		                "inputs": [
		                    {
		                        "name": "AzureBlobInput"
		                    }
		                ],
		                "outputs": [
		                    {
		                        "name": "AzureBlobOutput"
		                    }
		                ],
		                "policy": {
		                    "concurrency": 1,
		                    "retry": 3
		                },
		                "scheduler": {
		                    "frequency": "Month",
		                    "interval": 1
		                },
		                "name": "RunSampleHiveActivity",
		                "linkedServiceName": "HDInsightOnDemandLinkedService"
		            }
		        ],
		        "start": "2014-02-01T00:00:00Z",
		        "end": "2014-02-02T00:00:00Z",
		        "isPaused": false
		    }
		}

	JSON 코드 조각에서 Hive를 사용하여 HDInsight 클러스터에서 데이터를 처리하는 단일 작업으로 구성되는 파이프라인을 만듭니다.
	
	Hive 스크립트 파일 **partitionweblogs.hql**은 Azure 저장소 계정(**StorageLinkedService**라고 하는 scriptLinkedService에 의해 지정됨)과 **adfgetstarted** 컨테이너에 있는 **스크립트** 폴더에 저장됩니다.

	**defines** 섹션은 Hive 스크립트에 Hive 구성 값(예: ${hiveconf:inputtable}, ${hiveconf:partitionedtable})으로 전달되는 런타임 설정을 지정하는 데 사용됩니다.

	파이프라인의 **시작** 및 **끝** 속성은 파이프라인의 활성 기간을 지정합니다.

	작업 JSON에서 Hive 스크립트가 **linkedServiceName** – **HDInsightOnDemandLinkedService**에서 지정된 계산에서 실행되도록 지정합니다.
2.  Azure Blob 저장소의 **adfgetstarted/inputdata** 폴더에서 **input.log** 파일이 표시되는지 확인하고 다음 명령을 실행하여 파이프라인을 배포합니다. **시작** 및 **끝** 시간이 과거에 설정되고 **isPaused**가 false로 설정되었기 때문에 파이프라인(파이프라인의 활동)은 실행을 배포한 후에 즉시 실행됩니다. 

		New-AzureRmDataFactoryPipeline $df -File .\MyFirstPipelinePSH.json
5. 축하합니다. Azure PowerShell을 사용하여 첫 번째 파이프라인 만들기를 완료하였습니다.

### <a name="MonitorDataSetsAndPipeline"></a> 데이터 집합 및 파이프라인 모니터링
이 단계에서는 Azure PowerShell을 사용하여 Azure Data Factory에서 어떤 일이 일어나는지 모니터링합니다.

1. **Get-AzureRmDataFactory**를 실행하고 출력을 **$df** 변수에 할당합니다.

		$df=Get-AzureRmDataFactory -ResourceGroupName ADFTutorialResourceGroup -Name FirstDataFactoryPSH

2. **Get-AzureRmDataFactorySlice**를 실행하여 파이프라인의 출력 테이블인 **EmpSQLTable**의 모든 조각에 대한 세부 정보를 가져옵니다.

		Get-AzureRmDataFactorySlice $df -DatasetName AzureBlobOutput -StartDateTime 2014-02-01

	여기에 지정하는 StartDateTime은 파이프라인 JSON에 지정된 것과 동일한 시작 시간입니다. 다음과 유사한 결과가 표시됩니다.

		ResourceGroupName : ADFTutorialResourceGroup
		DataFactoryName   : FirstDataFactoryPSH
		DatasetName       : AzureBlobOutput
		Start             : 2/1/2014 12:00:00 AM
		End               : 3/1/2014 12:00:00 AM
		RetryCount        : 0
		State             : InProgress
		SubState          :
		LatencyStatus     :
		LongRetryCount    : 0


3. **Get-AzureRmDataFactoryRun**을 실행하여 특정 조각에 대한 작업 실행의 세부 정보를 가져옵니다.

		Get-AzureRmDataFactoryRun $df -DatasetName AzureBlobOutput -StartDateTime 2014-02-01

	다음과 유사한 결과가 표시됩니다.
		
		Id                  : 0f6334f2-d56c-4d48-b427-d4f0fb4ef883_635268096000000000_635292288000000000_AzureBlobOutput
		ResourceGroupName   : ADFTutorialResourceGroup
		DataFactoryName     : FirstDataFactoryPSH
		DatasetName         : AzureBlobOutput
		ProcessingStartTime : 12/18/2015 4:50:33 AM
		ProcessingEndTime   : 12/31/9999 11:59:59 PM
		PercentComplete     : 0
		DataSliceStart      : 2/1/2014 12:00:00 AM
		DataSliceEnd        : 3/1/2014 12:00:00 AM
		Status              : AllocatingResources
		Timestamp           : 12/18/2015 4:50:33 AM
		RetryAttempt        : 0
		Properties          : {}
		ErrorMessage        :
		ActivityName        : RunSampleHiveActivity
		PipelineName        : MyFirstPipeline
		Type                : Script

	**준비** 상태 또는 **실패** 상태에 조각이 표시될 때가지 이 cmdlet을 계속 실행합니다. 조각이 준비 상태에 있으면 출력 데이터에 대한 Blob 저장소의 **adfgetstarted** 컨테이너에 있는 **partitioneddata** 폴더를 확인합니다. 주문형 HDInsight 클러스터 만들기는 일반적으로 시간이 소요됩니다.

	![출력 데이터](./media/data-factory-build-your-first-pipeline-using-powershell/three-ouptut-files.png)
## 다음 단계
이 문서에서 파이프라인과 주문형 Azure HDInsight 클러스터에서 Hive 스크립트를 실행하는 변환 작업(HDInsight 작업)을 만들었습니다. 복사 작업을 사용하여 Azure Blob에서 Azure SQL로 데이터를 복사하는 방법은 [자습서: Azure Blob에서 Azure SQL로 데이터 복사](./data-factory-get-started.md)를 참조하세요.

### 참조
| 항목 | 설명 |
| :---- | :---- |
| [데이터 팩터리 cmdlet 참조](https://msdn.microsoft.com/library/azure/dn820234.aspx) | 데이터 팩터리 cmdlet에서 포괄적인 설명서를 참조하세요. |
| [파이프라인](data-factory-create-pipelines.md) | 이 문서는 Azure 데이터 팩터리의 파이프라인 및 시나리오 또는 비즈니스를 위한 활동과 종단 간 데이터 기반 워크플로 활용하는 방법을 이해하는 데 도움이 됩니다. |
| [데이터 집합](data-factory-create-datasets.md) | 이 문서는 Azure 데이터 팩터리의 데이터 집합을 이해하는 데 도움이 됩니다.
| [예약 및 실행](data-factory-scheduling-and-execution.md) | 이 문서에서는 Azure Data Factory 응용 프로그램 모델의 예약 및 실행에 대한 내용을 설명합니다. |
| [파이프라인 모니터링 및 관리](data-factory-monitor-manage-pipelines.md) | 이 문서는 파이프라인을 모니터링하고 관리하고 디버그하는 방법을 설명합니다. 경고를 생성하고 오류에 대한 알림을 받는 방법에 대한 정보도 제공합니다. |

[cmdlet-reference]: https://msdn.microsoft.com/library/azure/dn820234(v=azure.98).aspx

<!---HONumber=AcomDC_1223_2015-->