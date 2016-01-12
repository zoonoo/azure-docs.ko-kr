<properties
	pageTitle="Azure 데이터 팩터리 시작(Azure 리소스 관리자 템플릿)"
	description="이 자습서에서는 Azure 리소스 관리자 템플릿을 사용하여 샘플 Azure Data Factory 파이프라인을 만듭니다."
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
	ms.date="01/05/2016"
	ms.author="spelluru"/>

# Azure 리소스 관리자 템플릿을 사용하여 첫 번째 Azure Data Factory 파이프라인 빌드
> [AZURE.SELECTOR]
- [Tutorial Overview](data-factory-build-your-first-pipeline.md)
- [Using Data Factory Editor](data-factory-build-your-first-pipeline-using-editor.md)
- [Using PowerShell](data-factory-build-your-first-pipeline-using-powershell.md)
- [Using Visual Studio](data-factory-build-your-first-pipeline-using-vs.md)
- [Using Resource Manager Template](data-factory-build-your-first-pipeline-using-arm.md)


이 문서에서는 Azure 리소스 관리자(ARM) 템플릿을 사용하여 첫 번째 Azure Data Factory를 만드는 방법을 알아봅니다.


## 필수 조건
자습서 개요 항목에 나열된 필수 조건 외에도 다음이 설치되어 있어야 합니다.

- [자습서 개요](data-factory-build-your-first-pipeline.md) 문서를 읽고 진행하기 전에 필수 구성 요소 단계를 완료**해야** 합니다. 
- **Azure PowerShell를 설치합니다**. [Azure PowerShell을 설치 및 구성하는 방법](../powershell-install-configure.md) 문서의 지침을 수행하여 컴퓨터에 Azure PowerShell의 최신 버전을 설치합니다.
- 이 문서는 Azure Data Factory 서비스에 대한 개념적 개요를 제공하지 않습니다. 서비스에 대한 자세한 개요는 [Azure Data Factory 소개](data-factory-introduction.md)를 읽어보세요. 
- Azure 리소스 관리자(ARM) 템플릿에 대한 자세한 내용은 [Azure 리소스 관리자 템플릿 작성](../resource-group-authoring-templates.md)을 참조하세요. 
 

## 1단계: ARM 템플릿 만들기

**C:\\ADFGetStarted** 폴더에 다음과 같은 내용으로 **ADFTutorialARM.json**이라는 JSON 파일을 만듭니다.

> [AZURE.IMPORTANT]**storageAccountName** 및 **storageAccountKey** 변수에 대한 값을 변경합니다. 이름은 고유해야 하기 때문에 **dataFactoryName**도 변경합니다.

템플릿을 사용하면 다음 데이터 팩터리 엔터티를 만들 수 있습니다.

1. **TutorialDataFactoryARM**라는 **데이터 팩터리**입니다. 데이터 팩터리에는 하나 이상의 파이프라인이 포함될 수 있습니다. 파이프라인에는 하나 이상의 작업이 있을 수 있습니다. 예를 들어 원본에서 대상 데이터 저장소에 데이터를 복사하는 복사 작업 및 입력 데이터를 제품 출력 데이터로 변환하는 Hive 스크립트를 실행하는 HDInsight Hive 작업입니다. 
2. 두 개의 **연결된 서비스**는 **StorageLinkedService** 및 **HDInsightOnDemandLinkedService**입니다. 이러한 연결된 서비스는 Azure 저장소 계정 및 주문형 Azure HDInsight 클러스터를 데이터 팩터리에 연결합니다. Azure 저장소 계정은 이 샘플의 파이프라인에 대한 입력 및 출력 데이터를 가집니다. HDInsight 연결된 서비스는 이 샘플에서 파이프라인의 활동에 지정된 Hive 스크립트를 실행하는 데 사용됩니다. 시나리오에 사용되는 데이터 저장소/계산 서비스를 식별하고 연결된 서비스를 만들어 해당 서비스를 데이터 팩터리에 연결해야 합니다. 
3. 두 개의 (입/출력) **데이터 집합**은 **AzureBlobInput** 및 **AzureBlobOutput**입니다. 이러한 데이터 집합은 Hive 처리에 대한 입력 및 출력 데이터를 나타냅니다. 이러한 데이터 집합은 이 자습서의 앞부분에서 만든 **StorageLinkedService**를 참조합니다. 연결된 서비스는 Azure 저장소 계정을 가리키고 데이터 집합은 입력 및 출력 데이터를 가진 저장소의 컨테이너, 폴더, 파일 이름을 지정합니다.   

**데이터 팩터리 편집기 사용** 탭을 클릭하여 이 템플릿에서 사용된 JSON 속성에 대한 세부 정보가 있는 문서로 전환합니다.

	{
	    "contentVersion": "1.0.0.0",
	    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
	    "parameters": {
	    },
	    "variables": {
	        "dataFactoryName":  "TutorialDataFactoryARM",
	        "storageAccountName":  "<stroage account name>" ,
	        "storageAccountKey":  "<storage account key>",
	        "apiVersion": "2015-10-01",
	        "storageLinkedServiceName": "StorageLinkedService",
	        "hdInsightOnDemandLinkedServiceName": "HDInsightOnDemandLinkedService",
	        "blobInputDataset": "AzureBlobInput",
	        "blobOutputDataset": "AzureBlobOutput",
	        "singleQuote": "'"
	    },
	    "resources": [
	        {
	            "name": "[variables('dataFactoryName')]",
	            "apiVersion": "[variables('apiVersion')]",
	            "type": "Microsoft.DataFactory/datafactories",
	            "location": "westus",
	            "resources": [
	                {
	                    "dependsOn": [ "[concat('Microsoft.DataFactory/dataFactories/', variables('dataFactoryName'))]" ],
	                    "type": "linkedservices",
	                    "name": "[variables('storageLinkedServiceName')]",
	                    "apiVersion": "[variables('apiVersion')]",
	                    "properties": {
	                        "type": "AzureStorage",
	                        "typeProperties": {
	                            "connectionString": "[concat('DefaultEndpointsProtocol=https;AccountName=',variables('storageAccountName'),';AccountKey=',variables('storageAccountKey'))]"
	                        }
	                    }
	                },
	                {
	                    "dependsOn": [
	                        "[concat('Microsoft.DataFactory/dataFactories/', variables('dataFactoryName'))]",
	                        "[concat('Microsoft.DataFactory/dataFactories/', variables('dataFactoryName'), '/linkedservices/', variables('storageLinkedServiceName'))]"
	                    ],
	                    "type": "linkedservices",
	                    "name": "[variables('hdInsightOnDemandLinkedServiceName')]",
	                    "apiVersion": "[variables('apiVersion')]",
	                    "properties": {
	                        "type": "HDInsightOnDemand",
	                        "typeProperties": {
	                            "version": "3.2",
	                            "clusterSize": 1,
	                            "timeToLive": "00:30:00",
	                            "linkedServiceName": "StorageLinkedService"
	                        }
	                    }
	                },
	                {
	                    "dependsOn": [
	                        "[concat('Microsoft.DataFactory/dataFactories/', variables('dataFactoryName'))]",
	                        "[concat('Microsoft.DataFactory/dataFactories/', variables('dataFactoryName'), '/linkedServices/', variables('storageLinkedServiceName'))]"
	                    ],
	                    "type": "datasets",
	                    "name": "[variables('blobInputDataset')]",
	                    "apiVersion": "[variables('apiVersion')]",
						    "properties": {
						        "type": "AzureBlob",
						        "linkedServiceName": "StoraegLinkedService",
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
	                    },
	                {
	                    "dependsOn": [
	                        "[concat('Microsoft.DataFactory/dataFactories/', variables('dataFactoryName'))]",
	                        "[concat('Microsoft.DataFactory/dataFactories/', variables('dataFactoryName'), '/linkedServices/', variables('storageLinkedServiceName'))]"
	                    ],
	                    "type": "datasets",
	                    "name": "[variables('blobOutputDataset')]",
	                    "apiVersion": "[variables('apiVersion')]",
						    "properties": {
						        "published": false,
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
	                    },
	                    {
	                        "dependsOn": [
	                            "[concat('Microsoft.DataFactory/dataFactories/', variables('dataFactoryName'))]",
	                            "[concat('Microsoft.DataFactory/dataFactories/', variables('dataFactoryName'), '/linkedServices/', variables('storageLinkedServiceName'))]",
	                            "[concat('Microsoft.DataFactory/dataFactories/', variables('dataFactoryName'), '/linkedServices/', variables('hdInsightOnDemandLinkedServiceName'))]",
	                            "[concat('Microsoft.DataFactory/dataFactories/', variables('dataFactoryName'), '/datasets/', variables('blobInputDataset'))]",
	                            "[concat('Microsoft.DataFactory/dataFactories/', variables('dataFactoryName'), '/datasets/', variables('blobOutputDataset'))]"
	                        ],
	                        "type": "datapipelines",
	                        "name": "[variables('dataFactoryName')]",
	                        "apiVersion": "[variables('apiVersion')]",
						    "properties": {
						        "description": "My first Azure Data Factory pipeline",
						        "activities": [
						            {
						                "type": "HDInsightHive",
						                "typeProperties": {
						                    "scriptPath": "adfgetstarted/script/partitionweblogs.hql",
						                    "scriptLinkedService": "StorageLinkedService",
						                    "defines": {
		                        				"inputtable": "[concat('wasb://adfgetstarted@', variables('storageAccountName'), '.blob.core.windows.net/inputdata')]",
		                        				"partitionedtable": "[concat('wasb://adfgetstarted@', variables('storageAccountName'), '.blob.core.windows.net/partitioneddata')]"
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
	            ]
	        }
	    ]
	}



## 2단계: ARM 템플릿을 사용하여 데이터 팩터리 엔터티 배포

1. Azure PowerShell을 시작하고 다음 명령을 실행합니다. 이 자습서를 마칠 때까지 Azure PowerShell을 열어 두세요. 닫은 후 다시 여는 경우 이러한 명령을 다시 실행해야 합니다.
	- **Login-AzureRmAccount**를 실행하고 Azure 포털에 로그인하는 데 사용하는 사용자 이름 및 암호를 입력합니다.  
	- **Get-AzureSubscription**을 실행하여 이 계정의 모든 구독을 확인합니다.
	- **Select-AzureSubscription SubscriptionName**을 실행하여 사용하려는 구독을 선택합니다. 이 구독은 Azure 포털에서 사용한 것과 같아야 합니다.
1. 1단계에서에서 만든 ARM 템플릿을 사용하여 데이터 팩터리 엔터티를 배포하려면 다음 명령을 실행합니다. 

		New-AzureRmResourceGroupDeployment -Name MyARMDeployment -ResourceGroupName ADFTutorialResourceGroup -TemplateFile C:\ADFGetStarted\ADFTutorialARM.json

## 파이프라인 모니터링
 
1.	[Azure 포털](http://portal.azure.com/)에 로그인한 후, **찾아보기**를 클릭하고 **데이터 팩터리**를 선택합니다. ![찾아보기 -> 데이터 팩터리](./media/data-factory-build-your-first-pipeline-using-arm/BrowseDataFactories.png)
2.	**데이터 팩터리** 블레이드에서 만든 데이터 팩터리(**TutorialFactoryARM**)를 클릭합니다.	
2.	데이터 팩터리의 **데이터 팩터리** 블레이드에서 **다이어그램**을 클릭합니다. ![다이어그램 타일](./media/data-factory-build-your-first-pipeline-using-arm/DiagramTile.png)
4.	**다이어그램 보기**에 파이프라인의 개요와 이 자습서에 사용된 데이터 집합이 표시됩니다.
	
	![다이어그램 뷰](./media/data-factory-build-your-first-pipeline-using-arm/DiagramView.png) 
8. 다이어그램 뷰에서 **AzureBlobOutput** 데이터 집합을 두 번 클릭합니다. 현재 처리 중인 조각이 표시됩니다.

	![데이터 집합](./media/data-factory-build-your-first-pipeline-using-arm/AzureBlobOutput.png)
9. 처리가 완료되면 **준비** 상태인 조각이 표시됩니다. 주문형 HDInsight 클러스터 만들기는 일반적으로 시간이 소요됩니다.(대략 20분) 

	![데이터 집합](./media/data-factory-build-your-first-pipeline-using-arm/SliceReady.png)	
10. 조각이 **준비** 상태에 있으면 출력 데이터에 대한 blob 저장소의 **adfgetstarted** 컨테이너에 있는 **partitioneddata** 폴더를 확인합니다.  
 

<!---HONumber=AcomDC_0107_2016-->