<properties title="Azure Data Factory - Create Predictive Pipelines using Data Factory and Azure Machine Learning" pageTitle="데이터 팩터리 - 데이터 팩터리 및 기계 학습을 사용하여 예측 파이프라인 만들기 | Azure" description="만드는 방법에 설명 합니다. Azure 기계 학습 및 Azuer 데이터 팩터리를 사용 하 여 예측 파이프라인 만들기" metaKeywords=""  services="data-factory" solutions=""  documentationCenter="" authors="spelluru" manager="jhubbard" editor="monicar" />

<tags ms.service="data-factory" ms.workload="data-services" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="12/05/2014" ms.author="spelluru" />

# Azure 데이터 팩터리 및 Azure 기계 학습을 사용하여 예측 파이프라인 만들기 
Azure 데이터 팩터리 파이프라인 내에서 게시된 [Azure 기계 학습][azure-machine-learning] 모델을 운영할 수 있습니다. 이러한 파이프라인을 예측 파이프라인이라고 합니다. 예측 파이프라인을 만들려면 다음이 필요합니다.

-	게시된 작업 영역 모델의 API 키 및 배치 평가 URL(아래 이미지 참조)
-	평가할 입력 CSV 파일이 저장된 Azure Blob 저장소
-	평가 결과 CSV 파일을 저장할 Azure Blob 저장소

	![Machine Learning Dashboard][machine-learning-dashboard]

	AzureMLLinkedService의 배치 평가 URL은 위 이미지에 표시된 대로 얻습니다('**help**' 제외).:  https://ussouthcentral.services.azureml.net/workspaces/da9e350b758e44b2812a6218d507e216/services/8c91ff373a81416f8f8e0d96a1162681/jobs/

**예측 파이프라인**에는 다음과 같은 부분이 포함됩니다.

-	입력 및 출력 테이블
-	Azure 저장소 및 Azure ML 연결된 서비스
-	Azure ML 배치 평가 작업이 포함된 파이프라인

## 예



1. Azure 저장소에 대한 연결된 서비스를 만듭니다. 평가 입력 및 출력 파일이 서로 다른 저장소 계정에 있는 경우 연결된 서비스가 두 개 필요합니다. 다음은 JSON 예제입니다.

		{
		    "name": "StorageLinkedService",
		    "properties":
		    {
		        "type": "AzureStorageLinkedService",
		        "connectionString": "DefaultEndpointsProtocol=https;AccountName=[acctName];AccountKey=[acctKey]"
		    }
		}

2. 입력 및 출력 Azure 데이터 팩터리 테이블을 만듭니다. 다른 데이터 팩터리 테이블과 달리 이러한 테이블은 **folderPath** 값과 **fileName** 값을 둘 다 포함해야 합니다. 분할을 사용하여 각 배치 실행(각 데이터 조각)이 고유한 입력 및 출력 파일을 처리하거나 생성하도록 할 수 있습니다. 입력을 CSV 파일 형식으로 변환하여 각 조각의 저장소 계정에 배치하는 업스트림 작업을 포함해야 할 것입니다. 이 경우 아래 예제에 표시된 "waitOnExternal" 설정은 포함하지 않으며, ScoringInputBlob은 다른 작업의 출력 테이블이 됩니다.

		{  
			"name":"ScoringInputBlob",
			"properties":
			{  
					"location":
					{  
						"type":"AzureBlobLocation",
						"folderPath":"azuremltesting/input",
						"fileName":"in.csv",
						"format":
						{ 
							"type":"TextFormat",
							"columnDelimiter":","
						},
						"linkedServiceName":"StorageLinkedService"
					},
					"availability":
					{  
						"frequency":"Day",
						"interval":1,
						"waitOnExternal":
						{
		                	"retryInterval": "00:01:00",
		                	"retryTimeout": "00:10:00",
		                	"maximumRetry": 3
		            	}
		      		}
		   		}
			}

3. 이 출력 예제에서는 분할을 사용하여 각 조각 실행의 고유한 출력 경로를 만듭니다. 이렇게 하지 않으면 작업에서 파일을 덮어씁니다.

		{  
		   "name":"ScoringResultBlob",
		   "properties":
			{  
		        "location":
				{  
		            "type":"AzureBlobLocation",
		            "folderPath": "azuremltesting/scored/{folderpart}/",
		            "fileName": "{filepart}result.csv",
		            "partitionedBy": [ 
		                 { "name": "folderpart", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyyMMdd" } },
		                 { "name": "filepart", "value": { "type": "DateTime", "date": "SliceStart", "format": "HHmmss" } } 
		             ], 
		            "format":{  
		              "type":"TextFormat",
		              "columnDelimiter":","
		            },
		            "linkedServiceName":"StorageLinkedService"
		        },
		        "availability":
				{  
		            "frequency":"Day",
		            "interval":15
		        }
		   }
		}


4. 유형이 **AzureMLLinkedService**인 연결된 서비스를 만들어 API 키 및 모델 배치 평가 URL을 제공합니다.
		
		{
		    "name": "MyAzureMLLinkedService",
		    "properties":
		    {
		        "type": "AzureMLLinkedService",
		        "mlEndpoint":"https://[batch scoring endpoint]/jobs",
		        "apiKey":"[apikey]"
		    }
		}

5. 마지막으로 **AzureMLBatchScoringActivity**가 포함된 파이프라인을 작성합니다. 이 파이프라인은 입력 테이블에서 입력 파일의 위치를 가져오고, AzureML 배치 평가 API를 호출하며, 배치 평가 출력을 출력 테이블에 지정된 Blob에 복사합니다. 다른 데이터 팩터리 작업과 달리 AzureMLBatchScoringActivity는 입력 및 출력 테이블을 하나씩만 가질 수 있습니다.

		 {
		    "name": "PredictivePipeline",
		    "properties":
		    {
		        "description" : "use AzureML model",
		        "activities":
		        [
		         {  
		            "name":"MLActivity",
		            "type":"AzureMLBatchScoringActivity",
		            "description":"prediction analysis on batch input",
		            "inputs": [ { "name": "ScoringInputBlob" } ],
		            "outputs":[ { "name": "ScoringResultBlob" } ],
		            "linkedServiceName":"MyAzureMLLinkedService",
		            "policy":{  
		               "concurrency":3,
		               "executionPriorityOrder":"NewestFirst",
		               "retry":1,
		               "timeout":"02:00:00"
		            }
		         }
		        ]
		    }
		}

## 참고 항목

문서 | 설명
------ | ---------------
[Azure 데이터 팩터리 소개][adf-introduction] | 이 문서는 Azure 데이터 팩터리 서비스 개요와 Azure 데이터 팩터리 서비스가 지원하는 시나리오를 제공합니다.
[Azure 데이터 팩터리 시작][adf-getstarted] | 이 문서는 샘플 데이터 팩터리를 만들고 모니터링하는 단계별 지침을 제공하는 기본 자습서를 제공합니다.
[파이프라인에서 온-프레미스 데이터를 사용할 수 있도록 설정][use-onpremises-datasources](영문) | 이 문서의 연습에서는 온-프레미스 SQL Server 데이터베이스에서 Azure Blob으로 데이터를 복사하는 방법을 보여 줍니다.
[데이터 팩터리에서 Pig 및 Hive 사용][use-pig-and-hive-with-data-factory] | 이 문서의 연습에서는 HDInsight 작업을 사용하여 hive/pig 스크립트로 입력 데이터를 처리하고 출력 데이터를 생성하는 방법을 보여 줍니다. 
[자습서: 데이터 팩터리를 사용하여 로그 파일 이동 및 처리][adf-tutorial](영문) | 이 문서에서는 Azure 데이터 팩터리를 사용하는 실제 시나리오를 구현하여 로그 파일의 데이터에서 통찰력을 얻는 방법을 보여 주는 종단 간 연습을 제공합니다.
[데이터 팩터리에서 사용자 지정 작업 사용][use-custom-activities](영문) | 이 문서에서는 사용자 지정 작업을 만들어 파이프라인에서 사용하는 방법에 대한 단계별 지침이 포함된 연습을 제공합니다. 
[데이터 팩터리 문제 해결][troubleshoot](영문) | 이 문서에서는 Azure 데이터 팩터리 문제를 해결하는 방법에 대해 설명합니다. Azure SQL 데이터베이스의 테이블을 삭제하는 등 오류를 유발하여 이 문서의 연습을 ADFTutorialDataFactory에 대해 사용해 볼 수 있습니다. 
[Azure 데이터 팩터리 개발자 참조][developer-reference](영문) | 개발자 참조에는 cmdlet, JSON 스크립트, 함수 등에 대한 포괄적인 참조 콘텐츠가 포함되어 있습니다. 

[adf-introduction]: ../data-factory-introduction
[adf-getstarted]: ../data-factory-get-started
[use-onpremises-datasources]: ../data-factory-use-onpremises-datasources
[use-pig-and-hive-with-data-factory]: ../data-factory-pig-hive-activities
[adf-tutorial]: ../data-factory-tutorial
[use-custom-activities]: ../data-factory-use-custom-activities
[troubleshoot]: ../data-factory-troubleshoot
[data-factory-introduction]: ../data-factory-introduction  
[developer-reference]: http://go.microsoft.com/fwlink/?LinkId=516908

[azure-machine-learning]: http://azure.microsoft.com/ko-kr/services/machine-learning/
[machine-learning-dashboard]: ./media/data-factory-create-predictive-pipelines/AzureMLDashboard.png


<!--HONumber=35.2-->
