<properties 
	pageTitle="Azure 기계 학습 배치 실행 작업을 사용하여 예측 파이프라인 만들기 | Microsoft Azure" 
	description="Azure Data Factory 및 Azure 기계 학습을 사용하여 예측 파이프라인을 만드는 방법을 설명합니다." 
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
	ms.date="08/24/2015" 
	ms.author="spelluru"/>

# Azure 기계 학습 배치 실행 작업을 사용하여 예측 파이프라인 만들기   
## 개요

> [AZURE.NOTE][Azure 데이터 팩터리 소개](data-factory-introduction.md) 및 [첫 번째 파이프라인 빌드](data-factory-build-your-first-pipeline.md) 문서를 참조하여 Azure 데이터 팩터리 서비스를 빠르게 시작합니다.

Azure 데이터 팩터리를 사용하면 예측 분석을 위해 게시된 [Azure 기계 학습][azure-machine-learning] 웹 서비스를 활용하는 파이프라인을 쉽게 만들 수 있습니다. Azure 데이터 팩터리를 사용하면 빅 데이터 파이프라인(예: Pig 및 Hive)을 사용하여 다양한 데이터 원본에서 수집한 데이터를 처리하고 Azure 기계 학습 웹 서비스를 사용하여 배치의 데이터에 예측을 만들 수 있습니다.

Azure 데이터 팩터리를 사용하여 데이터 이동 및 처리를 오케스트레이션한 다음 Azure 기계 학습을 사용하는 배치 실행을 수행할 수 있습니다. 이를 위해 다음을 수행해야 합니다.

1. Azure 기계 학습 연결된 서비스 만들기 다음이 필요합니다.
	1. 배치 실행 API에 대한 **요청 URI**. 웹 서비스 페이지(아래 참조)에서 **배치 실행** 링크를 클릭하여 요청 URI를 찾을 수 있습니다.
	1. 게시된 기계 학습 웹 서비스를 위한 **API 키**. 게시한 웹 서비스를 클릭하여 API 키를 찾을 수 있습니다. 
 2. **AzureMLBatchExecution** 작업을 사용합니다.

	![기계 학습 대시보드](./media/data-factory-azure-ml-batch-execution-activity/AzureMLDashboard.png)

	![배치 URI](./media/data-factory-azure-ml-batch-execution-activity/batch-uri.png)


## 시나리오: Azure Blob 저장소의 데이터를 참조하는 웹 서비스 입력/출력을 사용하여 실험
이 시나리오에서는 Azure 기계 학습 웹 서비스는 Azure Blob 저장소의 파일에서 데이터를 사용하여 예측을 만들고 Blob 저장소에 예측 결과를 저장합니다. 다음 JSON은 AzureMLBatchExecution 작업이 포함된 Azure 데이터 팩터리 파이프라인을 정의합니다. 작업에는 입력으로 데이터 집합 **DecisionTreeInputBlob** 및 출력으로 **DecisionTreeResultBlob**이 있습니다. **DecisionTreeInputBlob**은 **webServiceInput** JSON 속성을 사용하여 웹 서비스에 입력으로 전달되고 **DecisionTreeResultBlob**은 **webServiceOutputs** JSON 속성을 사용하여 웹 서비스에 출력으로 전달됩니다. 작업에 대한 입력/출력인 데이터 집합만 웹 서비스 입력 및 출력으로 전달될 수 있습니다.


	{
	  "name": "PredictivePipeline",
	  "properties": {
	    "description": "use AzureML model",
	    "activities": [
	      {
	        "name": "MLActivity",
	        "type": "AzureMLBatchExecution",
	        "description": "prediction analysis on batch input",
	        "inputs": [
	          {
	            "name": "DecisionTreeInputBlob"
	          }
	        ],
	        "outputs": [
	          {
	            "name": "DecisionTreeResultBlob"
	          }
	        ],
	        "linkedServiceName": "MyAzureMLLinkedService",
            "typeProperties":
            {
                "webServiceInput": "DecisionTreeInputBlob ",
                "webServiceOutputs": {
                    "output1": "DecisionTreeResultBlob "
                }                
            },
	        "policy": {
	          "concurrency": 3,
	          "executionPriorityOrder": "NewestFirst",
	          "retry": 1,
	          "timeout": "02:00:00"
	        }
	      }
	    ],
	    "start": "2015-02-13T00:00:00Z",
	    "end": "2015-02-14T00:00:00Z"
	  }
	}

> [AZURE.NOTE]AzureMLBatchExecution 작업의 입력 및 출력만 웹 서비스에 매개 변수로 전달될 수 있습니다. 예를 들어 위의 JSON 조각에서 DecisionTreeInputBlob은 webServiceInput 매개 변수를 통해 웹 서비스에 입력으로 전달되는 AzureMLBatchExecution 작업에 대한 입력입니다.

### 예

이 예제에서는 Azure 저장소를 사용하여 입력 및 출력 데이터를 저장합니다.

이 예제를 수행하기 전에 [데이터 팩터리를 사용하여 첫 번째 파이프라인 빌드][adf-build-1st-pipeline] 자습서를 진행하고, 데이터 팩터리 편집기를 사용하여 이 예제의 데이터 팩터리 아티팩트(연결된 서비스, 데이터 집합, 파이프라인)를 만드는 것이 좋습니다.
 

1. **Azure 저장소**에 대한 **연결된 서비스**를 만듭니다. 입력 및 출력 파일이 서로 다른 저장소 계정에 있는 경우 연결된 서비스가 두 개 필요합니다. 다음은 JSON 예제입니다.

		{
		  "name": "StorageLinkedService",
		  "properties": {
		    "type": "AzureStorage",
		    "typeProperties": {
		      "connectionString": "DefaultEndpointsProtocol=https;AccountName=[acctName];AccountKey=[acctKey]"
		    }
		  }
		}

2. **입력** Azure Data Factory **데이터 집합**을 만듭니다. 다른 데이터 팩터리 데이터 집합과 달리 이러한 데이터 집합은 **folderPath** 값과 **fileName** 값을 둘 다 포함해야 합니다. 분할을 사용하여 각 배치 실행(각 데이터 조각)이 고유한 입력 및 출력 파일을 처리하거나 생성하도록 할 수 있습니다. 입력을 CSV 파일 형식으로 변환하여 각 조각의 저장소 계정에 배치하는 업스트림 작업을 포함해야 할 것입니다. 이 경우 아래 예제에 표시된 **외부** 및 **externalData** 설정을 포함하지 않으며, DecisionTreeInputBlob은 다른 작업의 출력 데이터 집합이 됩니다.

		{
		  "name": "DecisionTreeInputBlob",
		  "properties": {
		    "type": "AzureBlob",
		    "linkedServiceName": "StorageLinkedService",
		    "typeProperties": {
		      "folderPath": "azuremltesting/input",
		      "fileName": "in.csv",
		      "format": {
		        "type": "TextFormat",
		        "columnDelimiter": ","
		      }
		    },
		    "external": true,
		    "availability": {
		      "frequency": "Day",
		      "interval": 1
		    },
		    "policy": {
		      "externalData": {
		        "retryInterval": "00:01:00",
		        "retryTimeout": "00:10:00",
		        "maximumRetry": 3
		      }
		    }
		  }
		}
	
	입력 csv 파일에는 열 머리글 행이 있어야 합니다. **복사 작업**을 사용하여 csv를 만들고 Blob 저장소로 이동하는 경우 싱크 속성 **blobWriterAddHeader**를 **true**로 설정해야 합니다. 예:
	
	     sink: 
	     {
	         "type": "BlobSink",     
	         "blobWriterAddHeader": true 
	     }
	 
	csv 파일에 머리글 행이 없는 경우 다음과 같은 오류가 표시될 수 있습니다. **Error in Activity: Error reading string. Unexpected token: StartObject. Path '', line 1, position 1**.
3. **출력** Azure Data Factory **데이터 집합**을 만듭니다. 이 예제에서는 분할을 사용하여 각 조각 실행의 고유한 출력 경로를 만듭니다. 이렇게 하지 않으면 작업에서 파일을 덮어씁니다.

		{
		  "name": "DecisionTreeResultBlob",
		  "properties": {
		    "type": "AzureBlob",
		    "linkedServiceName": "StorageLinkedService",
		    "typeProperties": {
		      "folderPath": "azuremltesting/scored/{folderpart}/",
		      "fileName": "{filepart}result.csv",
		      "partitionedBy": [
		        {
		          "name": "folderpart",
		          "value": {
		            "type": "DateTime",
		            "date": "SliceStart",
		            "format": "yyyyMMdd"
		          }
		        },
		        {
		          "name": "filepart",
		          "value": {
		            "type": "DateTime",
		            "date": "SliceStart",
		            "format": "HHmmss"
		          }
		        }
		      ],
		      "format": {
		        "type": "TextFormat",
		        "columnDelimiter": ","
		      }
		    },
		    "availability": {
		      "frequency": "Day",
		      "interval": 15
		    }
		  }
		}

4. **AzureMLLinkedService** 형식의 **연결된 서비스**를 만들고 API 키 및 모델 배치 실행 URL을 제공합니다.
		
		{
		  "name": "MyAzureMLLinkedService",
		  "properties": {
		    "type": "AzureML",
		    "typeProperties": {
		      "mlEndpoint": "https://[batch execution endpoint]/jobs",
		      "apiKey": "[apikey]"
		    }
		  }
		}
5. 끝으로, **AzureMLBatchExecution**이 포함된 파이프라인을 작성합니다. 이 파이프라인은 입력 데이터 집합에서 입력 파일의 위치를 가져오고, Azure 기계 학습 배치 실행 API를 호출하며, 배치 실행 출력을 출력 데이터 집합의 지정된 Blob에 복사합니다. 

	> [AZURE.NOTE]AzureMLBatchExecution 작업에는 0개 이상의 입력 및 1개 이상의 출력이 있을 수 있습니다.

		{
		  "name": "PredictivePipeline",
		  "properties": {
		    "description": "use AzureML model",
		    "activities": [
		      {
		        "name": "MLActivity",
		        "type": "AzureMLBatchExecution",
		        "description": "prediction analysis on batch input",
		        "inputs": [
		          {
		            "name": "DecisionTreeInputBlob"
		          }
		        ],
		        "outputs": [
		          {
		            "name": "DecisionTreeResultBlob"
		          }
		        ],
		        "linkedServiceName": "MyAzureMLLinkedService",
                "typeProperties":
                {
                    "webServiceInput": "DecisionTreeInputBlob ",
                    "webServiceOutputs": {
                        "output1": "DecisionTreeResultBlob "
                    }                
                },
		        "policy": {
		          "concurrency": 3,
		          "executionPriorityOrder": "NewestFirst",
		          "retry": 1,
		          "timeout": "02:00:00"
		        }
		      }
		    ],
		    "start": "2015-02-13T00:00:00Z",
		    "end": "2015-02-14T00:00:00Z"
		  }
		}

	**시작** 및 **끝** 모두는 [ISO 형식](http://en.wikipedia.org/wiki/ISO_8601)이어야 합니다. 예: 2014-10-14T16:32:41Z. **end** 시간은 선택 사항입니다. **end** 속성 값을 지정하지 않는 경우 "**start + 48시간**"으로 계산됩니다. 파이프라인을 무기한 실행하려면 **end** 속성 값으로 **9999-09-09**를 지정합니다. JSON 속성에 대한 자세한 내용은 [JSON 스크립트 참조](https://msdn.microsoft.com/library/dn835050.aspx)를 참조하세요.

	> [AZURE.NOTE]AzureMLBatchExecution 작업에 대한 입력 지정은 선택 사항입니다.

## 시나리오: 다양한 저장소의 데이터를 참조하는 판독기/기록기 모듈을 사용하여 실험

Azure ML 실험을 만들 때 다른 일반적인 시나리오는 판독기 및 기록기기 모듈을 사용하는 것입니다. 판독기 모듈은 실험으로 데이터를 로드할 때 사용되고 기록기 모듈은 실험에서 데이터를 저장할 때 사용됩니다. 판독기 및 기록기 모듈에 대한 자세한 내용은 MSDN 라이브러리의 [판독기](https://msdn.microsoft.com/library/azure/dn905997.aspx) 및 [기록기](https://msdn.microsoft.com/library/azure/dn905984.aspx) 항목을 참조하세요.

판독기 및 작성기 모듈을 사용하는 경우 해당 판독기/기록기 모듈의 각 속성에 대해 웹 서비스 매개 변수를 사용하는 것이 좋습니다. 이러한 웹 매개 변수를 통해 런타임 중 값을 구성할 수 있습니다. 예를 들어 Azure SQL 데이터베이스: XXX.database.windows.net을 사용하는 판독기 모듈을 사용하여 실험을 만들 수 있습니다. 웹 서비스를 배포한 후 웹 서비스의 소비자가 YYY.database.windows.net이라는 다른 Azure SQL Server를 지정할 수 있도록 하려고 합니다. 웹 서비스 매개 변수를 사용하여 이 값을 구성할 수 있습니다.

> [AZURE.NOTE]웹 서비스 입력 및 출력은 웹 서비스 매개 변수와 다릅니다. 첫 번째 시나리오에서 Azure ML 웹 서비스에 대해 입력 및 출력을 지정할 수 있는 방법을 살펴보았습니다. 이 시나리오에서는 판독기/기록기 모듈의 속성에 해당하는 웹 서비스에 대한 매개 변수를 전달합니다.

웹 서비스 매개 변수를 사용하는 시나리오를 살펴보겠습니다. Azure 기계 학습 지원 데이터 원본(예: Azure SQL 데이터베이스) 중의 데이터를 읽는 판독기 모듈을 사용하는 Azure 기계 학습 웹 서비스를 배포했습니다. 배치 실행이 수행된 후 기록기 모듈(Azure SQL 데이터베이스)을 사용하여 결과가 기록됩니다. 웹 서비스 입력 및 출력이 실험에서 정의되지 않습니다. 이 경우 판독기 및 기록기 모듈에 대한 관련 웹 서비스 매개 변수를 설정하는 것이 좋습니다. 이렇게 하면 AzureMLBatchExecution 작업을 사용하는 경우 판독기/기록기 모듈을 구성할 수 있습니다. 다음과 같이 작업 JSON의 **globalParameters** 섹션에서 웹 서비스 매개 변수를 지정합니다.


	"typeProperties": {
		"globalParameters": {
			"Param 1": "Value 1",
			"Param 2": "Value 2"
		}
	}

다음 예제와 같이 웹 서비스 매개 변수 값을 전달하는 데 [데이터 팩터리 함수](https://msdn.microsoft.com/library/dn835056.aspx)를 사용할 수도 있습니다.

	"typeProperties": {
    	"globalParameters": {
    	   "Database query": "$$Text.Format('SELECT * FROM myTable WHERE timeColumn = \\'{0:yyyy-MM-dd HH:mm:ss}\\'', Time.AddHours(WindowStart, 0))"
    	}
  	}
 
> [AZURE.NOTE]웹 서비스 매개 변수는 대/소문자를 구분하므로 작업 JSON에서 지정한 이름이 웹 서비스에 의해 노출된 이름과 일치해야 합니다.

### Azure Blob에서 여러 파일의 데이터를 읽는 판독기 모듈 사용
빅 데이터 파이프라인(Pig, Hive 등)은 확장명 없이 하나 이상의 출력 파일을 만들 수 있습니다. 예를 들어 외부 Hive 테이블을 지정하는 경우 외부 Hive 테이블에 대한 데이터는 다음 이름 000000\_0으로 Azure Blob 저장소에 저장될 수 있습니다. 실험에서 판독기 모듈을 사용하여 여러 파일을 읽고 예측에 사용할 수 있습니다.

Azure 기계 학습 실험에서 판독기 모듈을 사용하는 경우 입력으로 Azure Blob를 지정할 수 있습니다. Azure blob 저장소에 있는 파일은 HDInsight에서 실행되는 Pig 및 Hive 스크립트에서 생성되는 출력 파일(예: 000000\_0)일 수 있습니다. 판독기 모듈을 사용하면 아래와 같이 파일을 포함하는 컨테이너/폴더를 가리키는 판독기 모듈의 **컨테이너, 디렉터리 또는 Blob에 대한 경로** 속성을 구성하여(확장명이 없는) 파일을 읽을 수 있습니다. **컨테이너/폴더에 있는 모든 파일을 지정하는(예를 들어data/aggregateddata/year=2014/month-6/*)** 별표(즉, *)는 실험의 일부로 읽어야 합니다.

![Azure Blob 속성](./media/data-factory-create-predictive-pipelines/azure-blob-properties.png)


### 예 
#### AzureMLBatchExecution 작업 및 웹 서비스 매개 변수가 포함된 파이프라인

	{
	  "name": "MLWithSqlReaderSqlWriter",
	  "properties": {
	    "description": "Azure ML model with sql azure reader/writer",
	    "activities": [
	      {
	        "name": "MLSqlReaderSqlWriterActivity",
	        "type": "AzureMLBatchExecution",
	        "description": "test",
	        "inputs": [
	          {
	            "name": "MLSqlInput"
	          }
	        ],
	        "outputs": [
	          {
	            "name": "MLSqlOutput"
	          }
	        ],
	        "linkedServiceName": "MLSqlReaderSqlWriterDecisionTreeModel",
            "typeProperties":
            {
                "webServiceInput": "MLSqlInput",
                "webServiceOutputs": {
                    "output1": "MLSqlOutput"
                }
	          	"globalParameters": {
	            	"Database server name": "<myserver>.database.windows.net",
		            "Database name": "<database>",
		            "Server user account name": "<user name>",
		            "Server user account password": "<password>"
	          	}              
            },
	        "policy": {
	          "concurrency": 1,
	          "executionPriorityOrder": "NewestFirst",
	          "retry": 1,
	          "timeout": "02:00:00"
	        },
	      }
	    ],
	    "start": "2015-02-13T00:00:00Z",
	    "end": "2015-02-14T00:00:00Z"
	  }
	}
 
위 JSON 예제에서

- 배포된 Azure 기계 학습 웹 서비스는 판독기 및 기록기 모듈을 사용하여 Azure SQL 데이터베이스에서/로 데이터를 읽고/쓸 수 있습니다. 이 웹 서비스는 다음 네 개의 매개 변수를 노출합니다. 데이터베이스 서버 이름, 데이터베이스 이름, 서버 사용자 계정 이름 및 서버 사용자 계정 암호  
- **시작** 및 **끝** 모두는 [ISO 형식](http://en.wikipedia.org/wiki/ISO_8601)이어야 합니다. 예: 2014-10-14T16:32:41Z. **end** 시간은 선택 사항입니다. **end** 속성 값을 지정하지 않는 경우 "**start + 48시간**"으로 계산됩니다. 파이프라인을 무기한 실행하려면 **end** 속성 값으로 **9999-09-09**를 지정합니다. JSON 속성에 대한 자세한 내용은 [JSON 스크립트 참조](https://msdn.microsoft.com/library/dn835050.aspx)를 참조하세요.
 

## 질문과 대답

**Q**: AzureMLBatchScoring 작업을 사용합니다. AzureMLBatchExecution 작업을 사용하도록 전환해야 합니까?

**A**: 예. AzureMLBatchScoring 작업을 사용하여 Azure 기계 학습과 통합하는 경우 최신 AzureMLBatchExecution 작업을 사용하는 것이 좋습니다. AzureMLBatchScoring 작업을 지양하며 향후 릴리스에서 제거될 예정입니다.

AzureMLBatchExecution 작업은2015년 8월 Azure SDK 및 Azure PowerShell 릴리스에서 도입되었습니다.

AzureMLBatchExecution 작업은 입력이 필요하지 않습니다(입력 종속성이 필요하지 않은 경우). 또한 웹 서비스 입력 및 웹 서비스 출력을 사용하거나 둘 다 사용하지 않으려 하는 여부에 대해 명시적이도록 합니다. 웹 서비스 입력/출력을 사용하도록 선택하는 경우 관련 Azure Blob 데이터 집합을 사용하도록 지정할 수 있습니다. 또한 웹 서비스에서 제공하는 웹 서비스 매개 변수의 값을 명확하게 지정할 수 있습니다.

AzureMLBatchScoring 작업을 계속해서 사용하려면 자세한 내용은 [Azure ML 배치 평가 작업](data-factory-create-predictive-pipelines.md) 문서를 참조하세요.


**Q:** 빅 데이터 파이프라인에서 생성된 여러 파일이 있습니다. 모든 파일에서 작동하도록 AzureMLBatchExecution 작업을 사용할 수 있습니까?

**A**: 예. 자세한 내용은 **Azure Blob에서 여러 파일의 데이터를 읽는 판독기 모듈 사용**을 참조하세요.


## 참고 항목

- [Azure 블로그 게시물: Azure 데이터 팩터리 및 Azure 기계 학습 시작하기](https://azure.microsoft.com/blog/getting-started-with-azure-data-factory-and-azure-machine-learning-4/)





[adf-build-1st-pipeline]: data-factory-build-your-first-pipeline.md

[azure-machine-learning]: http://azure.microsoft.com/services/machine-learning/


 

<!---HONumber=Oct15_HO3-->