<properties
	pageTitle="Azure PowerShell을 사용하여 첫 번째 Azure Data Factory 파이프라인 빌드"
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
	ms.date="11/02/2015"
	ms.author="spelluru"/>

# Azure PowerShell을 사용하여 첫 번째 Azure Data Factory 파이프라인 빌드
> [AZURE.SELECTOR]
- [Tutorial Overview](data-factory-build-your-first-pipeline.md)
- [Using Data Factory Editor](data-factory-build-your-first-pipeline-using-editor.md)
- [Using PowerShell](data-factory-build-your-first-pipeline-using-powershell.md)
- [Using Visual Studio](data-factory-build-your-first-pipeline-using-vs.md)


이 문서에서는 Azure PowerShell을 사용하여 첫 번째 파이프라인을 만드는 방법을 알아봅니다. 이 자습서는 다음과 같은 단계로 구성됩니다.

1.	데이터 팩터리 만들기
2.	연결된 서비스(데이터 저장소, 계산) 및 데이터 집합 만들기
3.	파이프라인 만들기.

이 문서는 Azure Data Factory 서비스에 대한 개념적 개요를 제공하지 않습니다. 서비스에 대한 자세한 개요는 [Azure Data Factory 소개](data-factory-introduction.md) 문서를 참조하세요.

> [AZURE.IMPORTANT] [자습서 개요](data-factory-build-your-first-pipeline.md) 문서를 살펴보고 이 자습서를 수행하기 전에 필수 단계를 완료합니다.
>   
> 이 문서는 모든 데이터 팩터리 cmdlet을 다루지 않습니다. 데이터 팩터리 cmdlet에 대한 포괄적인 설명서는 [데이터 팩터리 Cmdlet 참조][cmdlet-reference](영문)를 참조하세요.
>    
> Azure PowerShell 1.0 미리 보기를 사용하는 경우 [여기](https://msdn.microsoft.com/library/dn820234.aspx)에서 설명된 cmdlet을 사용해야 합니다. 예를 들어 New-AzureDataFactory를 사용하는 대신 New-AzureRMDataFactory를 사용합니다.

## 1단계: 데이터 팩터리 만들기

이 단계에서는 Azure PowerShell을 사용하여 ADFTutorialDataFactoryPSH라는 Azure Data Factory를 만듭니다.

1. Azure PowerShell을 시작하고 다음 명령을 실행합니다. 이 자습서를 마칠 때까지 Azure PowerShell을 열어 두세요. 닫은 후 다시 여는 경우 이러한 명령을 다시 실행해야 합니다.
	- **Add-AzureAccount**를 실행하고 Azure Preview 포털에 로그인하는 데 사용하는 사용자 이름 및 암호를 입력합니다.  
	- **Get-AzureSubscription**을 실행하여 이 계정의 모든 구독을 확인합니다.
	- **Select-AzureSubscription**을 실행하여 사용하려는 구독을 선택합니다. 이 구독은 미리 보기 포털에서 사용한 것과 같아야 합니다.
2. AzureResourceManager 모드로 전환합니다. Azure Data Factory cmdlet은 이 모드에서 사용할 수 있습니다.

		Switch-AzureMode AzureResourceManager
3. 다음 명령을 실행하여 *ADFTutorialResourceGroup*이라는 Azure 리소스 그룹을 만듭니다.

		New-AzureResourceGroup -Name ADFTutorialResourceGroup  -Location "West US"

	이 자습서의 일부 단계에서는 ADFTutorialResourceGroup이라는 리소스 그룹을 사용한다고 가정합니다. 다른 리소스 그룹을 사용하는 경우 이 자습서에서 ADFTutorialResourceGroup 대신 해당 리소스 그룹을 사용해야 합니다.
4. **New-AzureDataFactory** cmdlet을 실행하여 DataFactoryMyFirstPipelinePSH라는 데이터 팩터리를 만듭니다.  

		New-AzureDataFactory -ResourceGroupName ADFTutorialResourceGroup -Name DataFactoryMyFirstPipelinePSH –Location "West US"

	> [AZURE.IMPORTANT]Azure Data Factory 이름은 전역적으로 고유해야 합니다. **데이터 팩터리 이름 “DataFactoryMyFirstPipelinePSH”를 사용할 수 없습니다.** 오류가 표시되는 경우 이름을 변경합니다(예: yournameADFTutorialDataFactoryPSH). 이 자습서의 단계를 수행하는 동안 ADFTutorialFactoryPSH 대신 이 이름을 사용합니다. 데이터 팩터리 아티팩트에 대한 명명 규칙은 [데이터 팩터리 - 명명 규칙](data-factory-naming-rules.md) 항목을 참조하세요.
	> 
	> 데이터 팩터리의 이름은 나중에 DNS 이름으로 표시되므로 공개적으로 등록될 수도 있습니다.

이후 단계에서는 이 자습서에서 사용할 연결된 서비스, 데이터 집합, 파이프라인을 만드는 방법을 알아봅니다.

## 2단계: 연결된 서비스 및 데이터 집합 만들기
이 단계에서는 Azure 저장소 계정과 주문형 Azure HDInsight 클러스터를 데이터 팩터리에 연결한 후 Hive 처리의 출력 데이터를 나타낼 데이터 집합을 만듭니다.

### Azure 저장소 연결된 서비스 만들기
1.	C:\\ADFGetStartedPSH 폴더에 다음과 같은 내용으로 StorageLinkedService.json이라는 JSON 파일을 만듭니다. 아직 없는 경우 ADFGetStartedPSH 폴더를 만듭니다.

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

	**계정 이름**을 Azure 저장소 계정 이름으로 변경하고 **계정 키**를 Azure 저장소 계정의 선택키로 변경합니다. 저장소 액세스 키를 확보하는 방법을 알아보려면 [저장소 선택키 보기, 복사 및 다시 생성](http://azure.microsoft.com/documentation/articles/storage-create-storage-account/#view-copy-and-regenerate-storage-access-keys)을 참조하세요.

2.	Azure PowerShell에서 ADFGetStartedPSH 폴더로 전환합니다.
3.	**New-AzureDataFactoryLinkedService** cmdlet을 사용하여 연결된 서비스를 만들 수 있습니다. 이 cmdlet 및 이 자습서에서 사용하는 다른 데이터 팩터리 cmdlet의 경우 *ResourceGroupName* 및 *DataFactoryName* 매개 변수의 값을 전달해야 합니다. 또는 **Get AzureDataFactory**를 사용하여 **DataFactory** 개체를 가져온 다음 cmdlet을 실행할 때마다 *ResourceGroupName* 및 *DataFactoryName*을 입력하지 않고 개체를 전달할 수 있습니다. 다음 명령을 실행하여 **Get-AzureDataFactory** cmdlet의 출력을 **$df** 변수에 할당합니다.

		$df=Get-AzureDataFactory -ResourceGroupName ADFTutorialResourceGroup -Name DataFactoryMyFirstPipelinePSH

4.	이제 **New-AzureDataFactoryLinkedService** cmdlet을 실행하여 연결된 **StorageLinkedService** 서비스를 만듭니다.

		New-AzureDataFactoryLinkedService $df -File .\StorageLinkedService.json

	**Get AzureDataFactory** cmdlet을 실행하고 출력을 **$df** 변수에 할당하지 않은 경우 *ResourceGroupName* 및 *DataFactoryName* 매개 변수의 값을 다음과 같이 지정해야 합니다.

		New-AzureDataFactoryLinkedService -ResourceGroupName ADFTutorialResourceGroup -DataFactoryName ADFTutorialDataFactoryPSH -File .\StorageLinkedService.json

	자습서 중에 Azure PowerShell을 닫은 경우 자습서를 완료하기 위해 다음에 Azure PowerShell을 시작할 때 **Get-AzureDataFactory** cmdlet을 실행해야 합니다.

### Azure HDInsight 연결된 서비스 만들기
Hive 스크립트를 실행하는데 사용될 주문형 Azure HDInsight 클러스터의 연결된 서비스를 만듭니다.

1.	C:\\ADFGetStartedPSH 폴더에 다음과 같은 내용으로 HDInsightOnDemandLinkedService.json이라는 JSON 파일을 만듭니다.


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
2. **New-AzureDataFactoryLinkedService** cmdlet을 실행하여 HDInsightOnDemandLinkedService라는 연결된 서비스를 만듭니다.

		New-AzureDataFactoryLinkedService $df -File .\HDInsightOnDemandLinkedService.json


### 출력 데이터 집합 만들기
Azure Blob 저장소에 저장된 데이터를 나타내는 출력 데이터 집합을 만듭니다.

1.	C:\\ADFGetStartedPSH 폴더에 다음과 같은 내용으로 OutputTable.json이라는 JSON 파일을 만듭니다.

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

	이전 예제에서 **AzureBlobOutput**이라는 데이터 집합을 만들고 Hive 스크립트에 의해 생성될 데이터의 구조를 지정합니다. 또한 결과가 **data**라는 Blob 컨테이너와 **partitioneddata** 폴더에 저장되도록 지정합니다. **가용성** 섹션은 출력 데이터 집합이 월 단위로 생성되도록 지정합니다.

2. Azure PowerShell에서 다음 명령을 실행하여 데이터 팩터리 데이터 집합을 만듭니다.

		New-AzureDataFactoryDataset $df -File .\OutputTable.json

## 3단계: 첫 번째 파이프라인 만들기
이 단계에서는 첫 번째 파이프라인을 만듭니다.

1.	C:\\ADFGetStartedPSH 폴더에 다음과 같은 내용으로 MyFirstPipelinePSH.json이라는 JSON 파일을 만듭니다.

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

	이전 예제에서 Hive를 사용하여 HDInsight 클러스터에서 데이터를 처리하는 단일 작업으로 구성되는 파이프라인을 만듭니다.

	Hive 스크립트 파일 partitionweblogs.hql은 Azure 저장소 계정(StorageLinkedService라고 하는 scriptLinkedService에 의해 지정되는)과 **script** 컨테이너에 저장됩니다.

	**defines** 섹션은 Hive 스크립트에 Hive 구성 값(예: ${hiveconf:PartitionedData})으로 전달되는 런타임 설정을 지정하는 데 사용됩니다.

	파이프라인의 **start** 및 **end** 속성은 파이프라인의 활성 기간을 지정합니다.

	작업 JSON에서 Hive 스크립트가 연결된 서비스 **HDInsightOnDemandLinkedService**에서 지정된 컴퓨터에서 실행되도록 지정합니다.
2. 다음 명령을 실행하여 데이터 팩터리 테이블을 만듭니다.

		New-AzureDataFactoryPipeline $df -File .\MyFirstPipelinePSH.json
5. 축하합니다. Azure PowerShell을 사용하여 첫 번째 파이프라인 만들기를 완료하였습니다.

### <a name="MonitorDataSetsAndPipeline"></a> 데이터 집합 및 파이프라인 모니터링
이 단계에서는 Azure PowerShell을 사용하여 Azure Data Factory에서 어떤 일이 일어나는지 모니터링합니다.

1.	**Get-AzureDataFactory**를 실행하고 출력을 **$df** 변수에 할당합니다.

		$df=Get-AzureDataFactory -ResourceGroupName ADFTutorialResourceGroup -Name DataFactoryMyFirstPipelinePSH

2.	**Get-AzureDataFactorySlice**를 실행하여 파이프라인의 출력 테이블인 **EmpSQLTable**의 모든 조각에 대한 세부 정보를 가져옵니다.

		Get-AzureDataFactorySlice $df -TableName AzureBlobOutput -StartDateTime 2014-01-01

	여기에 지정하는 StartDateTime은 파이프라인 JSON에 지정된 것과 동일한 시작 시간입니다. 다음과 유사한 결과가 표시됩니다.

		ResourceGroupName : ADFTutorialResourceGroup
		DataFactoryName   : DataFactoryMyFirstPipelinePSH
		TableName         : AzureBlobOutput
		Start             : 1/1/2014 12:00:00 AM
		End               : 2/1/2014 12:00:00 AM
		RetryCount        : 0
		Status            : InProgress
		LatencyStatus     :
		LongRetryCount    : 0

3.	**Get-AzureDataFactoryRun**을 실행하여 특정 조각에 대한 작업 실행의 세부 정보를 가져옵니다.

		Get-AzureDataFactoryRun $df -TableName AzureBlobOutput -StartDateTime 2014-01-01

	다음과 유사한 결과가 표시됩니다.

		Id                  : 4dbc6a07-537d-4005-a53e-6b9a4b844089_635241312000000000_635268096000000000_AzureBlobOutput
		ResourceGroupName   : ADFTutorialResourceGroup
		DataFactoryName     : DataFactoryMyFirstPipelinePSH
		TableName           : AzureBlobOutput
		ProcessingStartTime : 7/7/2015 1:14:18 AM
		ProcessingEndTime   : 12/31/9999 11:59:59 PM
		PercentComplete     : 0
		DataSliceStart      : 1/1/2014 12:00:00 AM
		DataSliceEnd        : 2/1/2014 12:00:00 AM
		Status              : AllocatingResources
		Timestamp           : 7/7/2015 1:14:18 AM
		RetryAttempt        : 0
		Properties          : {}
		ErrorMessage        :
		ActivityName        : RunSampleHiveActivity
		PipelineName        : MyFirstPipeline
		Type                : Script

	Ready(준비) 상태 또는 Failed(실패) 상태에 조각이 표시될 때가지 이 cmdlet을 계속 실행합니다. 조각이 Ready(준비) 상태에 있으면 BLOB 저장소의 data 컨테이너에 있는 partitioneddata 폴더에서 출력 데이터를 확인합니다. 주문형 HDInsight 클러스터 만들기는 일반적으로 시간이 소요됩니다.

데이터 팩터리 cmdlet에 대한 포괄적인 설명서는 [데이터 팩터리 Cmdlet 참조](https://msdn.microsoft.com/library/azure/dn820234.aspx)(영문)를 참조하세요.



## 다음 단계
이 문서에서 파이프라인과 주문형 Azure HDInsight 클러스터에서 Hive 스크립트를 실행하는 변환 작업(HDInsight 작업)을 만들었습니다. 복사 작업을 사용하여 Azure Blob에서 Azure SQL로 데이터를 복사하는 방법은 [자습서: Azure Blob에서 Azure SQL로 데이터 복사](./data-factory-get-started.md)를 참조하세요.


[cmdlet-reference]: https://msdn.microsoft.com/library/azure/dn820234(v=azure.98).aspx

<!----HONumber=AcomDC_1125_2015-->