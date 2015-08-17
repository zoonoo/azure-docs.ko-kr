<properties 
	pageTitle="데이터 팩터리 - 데이터 팩터리 및 기계 학습을 사용하여 예측 파이프라인 만들기 | Azure" 
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
	ms.date="08/04/2015" 
	ms.author="spelluru"/>

# Azure Data Factory 및 Azure 기계 학습을 사용하여 예측 파이프라인 만들기 
## 개요

Azure 데이터 팩터리를 사용하면 예측 분석을 위해 게시된 [Azure 기계 학습][azure-machine-learning] 웹 서비스를 활용하는 파이프라인을 쉽게 만들 수 있습니다. 그러면 Azure 데이터 팩터리를 사용 하여 데이터 이동 및 처리를 오케스트레이션한 다음 Azure 기계 학습을 사용하는 일괄 처리를 수행할 수 있습니다. 이를 위해 다음을 수행해야 합니다.

1. **AzureMLBatchScoring** 활동을 사용합니다.
2. 일괄 처리 실행 API에 대한 **요청 URI**입니다. 웹 서비스 페이지(아래 참조)에서 **일괄 처리 실행** 링크를 클릭하여 요청 URI를 찾을 수 있습니다.
3. 게시된 기계 학습 웹 서비스를 위한 **API 키** 게시한 웹 서비스를 클릭하여 이 정보를 찾을 수 있습니다. 

	![기계 학습 대시보드][machine-learning-dashboard]

**예측 파이프라인**에는 다음과 같은 부분이 포함됩니다.

-	입력 및 출력 테이블
-	Azure 저장소/Azure SQL 및 Azure ML 연결된 서비스
-	Azure ML 배치 평가 작업이 포함된 파이프라인

> [AZURE.NOTE]ADF(Azure Data Factory) 파이프라인에서 게시된 Azure 기계 학습 웹 서비스에 의해 노출되는 웹 서비스 매개 변수를 사용할 수 있습니다. 자세한 내용은 이 문서의 웹 서비스 매개 변수 섹션을 참조하세요.

## 예
이 예제에서는 Azure 저장소를 사용하여 입력 및 출력 데이터를 저장합니다. Azure 저장소를 사용하는 대신 Azure SQL 데이터베이스를 사용할 수도 있습니다.

이 예제를 수행하기 전에 [Azure 데이터 팩터리 시작][adf-getstarted] 자습서를 진행하고, 데이터 팩터리 편집기를 사용하여 이 예제의 데이터 팩터리 아티팩트(연결된 서비스, 테이블, 파이프라인)를 만드는 것이 좋습니다.
 

1. **Azure 저장소**에 대한 **연결된 서비스**를 만듭니다. 평가 입력 및 출력 파일이 서로 다른 저장소 계정에 있는 경우 연결된 서비스가 두 개 필요합니다. 다음은 JSON 예제입니다.

		{
		  "name": "StorageLinkedService",
		  "properties": {
		    "type": "AzureStorage",
		    "typeProperties": {
		      "connectionString": "DefaultEndpointsProtocol=https;AccountName=[acctName];AccountKey=[acctKey]"
		    }
		  }
		}

2. **입력** Azure Data Factory **테이블**을 만듭니다. 다른 데이터 팩터리 테이블과 달리 이러한 테이블은 **folderPath** 값과 **fileName** 값을 둘 다 포함해야 합니다. 분할을 사용하여 각 배치 실행(각 데이터 조각)이 고유한 입력 및 출력 파일을 처리하거나 생성하도록 할 수 있습니다. 입력을 CSV 파일 형식으로 변환하여 각 조각의 저장소 계정에 배치하는 업스트림 작업을 포함해야 할 것입니다. 이 경우 아래 예제에 표시된 **외부** 및 **externalData** 설정을 포함하지 않으며, ScoringInputBlob은 다른 작업의 출력 테이블이 됩니다.

		{
		  "name": "ScoringInputBlob",
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
	
	csv 파일을 평가하는 배치에는 열 머리글 행이 있어야 합니다. **복사 작업**을 사용하여 csv를 만들고 Blob 저장소로 이동하는 경우 싱크 속성 **blobWriterAddHeader**를 **true**로 설정해야 합니다. 예:
	
	     sink: 
	     {
	         "type": "BlobSink",     
	         "blobWriterAddHeader": true 
	     }
	 
	csv 파일에 머리글 행이 없는 경우 다음과 같은 오류가 표시될 수 있습니다. **Error in Activity: Error reading string. Unexpected token: StartObject. Path '', line 1, position 1**.
3. **출력** Azure Data Factory **테이블**을 만듭니다. 이 예제에서는 분할을 사용하여 각 조각 실행의 고유한 출력 경로를 만듭니다. 이렇게 하지 않으면 작업에서 파일을 덮어씁니다.

		{
		  "name": "ScoringResultBlob",
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

4. **AzureMLLinkedService** 형식의 **연결된 서비스**를 만들고 API 키 및 모델 배치 평가 URL을 제공합니다.
		
		{
		  "name": "MyAzureMLLinkedService",
		  "properties": {
		    "type": "AzureML",
		    "typeProperties": {
		      "mlEndpoint": "https://[batch scoring endpoint]/jobs",
		      "apiKey": "[apikey]"
		    }
		  }
		}
5. 끝으로, **AzureMLBatchScoringActivity**가 포함된 파이프라인을 작성합니다. 이 파이프라인은 입력 테이블에서 입력 파일의 위치를 가져오고, AzureML 배치 평가 API를 호출하며, 배치 평가 출력을 출력 테이블에 지정된 Blob에 복사합니다. 다른 데이터 팩터리 작업과 달리 AzureMLBatchScoringActivity는 입력 및 출력 테이블을 하나씩만 가질 수 있습니다.

		{
		  "name": "PredictivePipeline",
		  "properties": {
		    "description": "use AzureML model",
		    "activities": [
		      {
		        "name": "MLActivity",
		        "type": "AzureMLBatchScoring",
		        "description": "prediction analysis on batch input",
		        "inputs": [
		          {
		            "name": "ScoringInputBlob"
		          }
		        ],
		        "outputs": [
		          {
		            "name": "ScoringResultBlob"
		          }
		        ],
		        "linkedServiceName": "MyAzureMLLinkedService",
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

## 웹 서비스 매개 변수
ADF(Azure Data Factory) 파이프라인에서 게시된 Azure 기계 학습 웹 서비스에 의해 노출되는 웹 서비스 매개 변수를 사용할 수 있습니다. Azure 기계 학습에서 실험을 만들고 웹 서비스로 게시한 다음 웹 서비스 매개 변수를 통해 다른 입력을 전달하여 여러 ADF 파이프라인 또는 작업에서 해당 웹 서비스를 사용할 수 있습니다.

### 웹 서비스 매개 변수 값 전달
파이프라인 JSON의 **AzureMLBatchScoringActivty** 섹션에 **typeProperties** 섹션을 추가하여 다음 예제와 같이 해당 섹션의 웹 서비스 매개 변수 값을 지정합니다.

	"typeProperties": {
		"webServiceParameters": {
			"Param 1": "Value 1",
			"Param 2": "Value 2"
		}
	}


다음 예제와 같이 웹 서비스 매개 변수 값을 전달하는 데 [데이터 팩터리 함수](https://msdn.microsoft.com/library/dn835056.aspx)를 사용할 수도 있습니다.

	"typeProperties": {
    	"webServiceParameters": {
    	   "Database query": "$$Text.Format('SELECT * FROM myTable WHERE timeColumn = \\'{0:yyyy-MM-dd HH:mm:ss}\\'', Time.AddHours(WindowStart, 0))"
    	}
  	}
 
> [AZURE.NOTE]웹 서비스 매개 변수는 대/소문자를 구분하므로 작업 JSON에서 지정한 이름이 웹 서비스에 의해 노출된 이름과 일치해야 합니다.

### 판독기 및 작성기 모듈

웹 서비스 매개 변수를 사용하는 일반적인 시나리오는 Azure SQL 판독기 및 기록기 사용입니다. 판독기 모듈은 Azure 기계 학습 스튜디오 외부의 데이터 관리 서비스에서 실험으로 데이터를 로드하는 데 사용되고, 기록기 모듈은 실험의 데이터를 Azure 기계 학습 스튜디오 외부의 데이터 관리 서비스에 저장하는 데 사용됩니다. Azure Blob/Azure SQL 판독기/기록기에 대한 자세한 내용은 MSDN 라이브러리의 [판독기](https://msdn.microsoft.com/library/azure/dn905997.aspx) 및 [기록기](https://msdn.microsoft.com/library/azure/dn905984.aspx) 항목을 참조하세요. 이전 섹션의 예제에서는 Azure Blob 판독기 및 Azure Blob 기록기를 사용했습니다. 이 섹션에서는 Azure SQL 판독기 및 Azure SQL 기록기를 사용하는 방법을 설명합니다.

#### 데이터 원본인 Azure SQL
Azure 기계 학습 스튜디오에서 실험을 빌드하고 Azure SQL 판독기를 입력으로 사용하여 웹 서비스를 게시할 수 있습니다. Azure SQL 판독기에는 웹 서비스 매개 변수로 노출될 수 있는 연결 속성이 있으며, 런타임에 배치 평가 요청에서 연결 속성 값을 전달할 수 있습니다.

런타임에 데이터 팩터리 서비스는 입력 데이터 팩터리 테이블의 세부 정보를 사용하여 웹 서비스 매개 변수를 채웁니다. 데이터 팩터리 서비스와의 이 통합이 작동하려면 웹 서비스 매개 변수에 기본 이름(데이터베이스 서버 이름, 데이터베이스 이름, 서버 사용자 계정 이름, 서버 사용자 계정 암호)을 사용해야 합니다.

추가 웹 서비스 매개 변수가 있는 경우 작업 JSON의 **webServiceParameters** 섹션을 사용합니다. 이 섹션에서 Azure SQL 판독기 매개 변수의 값을 지정하는 경우 해당 값이 입력 Azure SQL 연결된 서비스에서 선택된 값을 재정의합니다. WebServiceParameters 섹션에서 직접 Azure SQL 판독기의 값을 지정하지 않는 것이 좋습니다. 이 섹션을 사용하여 추가 매개 변수의 값을 전달합니다.

Azure Data Factory 파이프라인을 통해 Azure SQL 판독기를 사용하려면 다음을 수행합니다.

- **Azure SQL 연결된 서비스**를 만듭니다. 
- **AzureSqlTable**을 사용하는 데이터 팩터리 **테이블**을 만듭니다.
- 파이프라인 JSON에서 데이터 팩터리 **테이블**을 **AzureMLBatchScoringActivity**의 **입력**으로 설정합니다. 



#### 데이터 싱크인 Azure SQL
Azure SQL 판독기와 마찬가지로, Azure SQL 기록기의 속성도 웹 서비스 매개 변수로 노출될 수 있습니다. Azure SQL 기록기는 입력 테이블 또는 출력 테이블과 연관된 연결된 서비스의 설정을 사용합니다. 다음 표에서는 출력 연결된 서비스와 비교하여 입력 연결된 서비스가 사용되는 경우를 설명합니다.

| 출력/입력 | 입력이 Azure SQL임 | 입력이 Azure Blob임 |
| ------------ | ------------------ | ------------------- |
| 출력이 Azure SQL임 | <p>데이터 팩터리 서비스는 입력 연결된 서비스의 연결 문자열 정보를 사용하여 "데이터베이스 서버 이름", "데이터베이스 이름", "서버 사용자 계정 이름", "서버 사용자 계정 암호"라는 이름으로 웹 서비스 매개 변수를 생성합니다. Azure 기계 학습 스튜디오에서는 웹 서비스 매개 변수에 이러한 기본 이름을 사용해야 합니다.</p><p>Azure 기계 학습 모델의 Azure SQL 판독기 및 Azure SQL 기록기가 위에서 언급한 동일한 웹 서비스 매개 변수를 공유하는 경우 아무 문제도 없습니다. 동일한 웹 서비스 매개 변수를 공유하지 않는 경우, 예를 들어 Azure SQL 기록기가 매개 변수 이름으로 데이터베이스 서버 이름1, 데이터베이스 이름1, 서버 사용자 계정 이름1, 서버 사용자 계정 암호1(끝에 '1' 포함)을 사용하는 경우 작업 JSON의 webServiceParameters 섹션에서 이러한 출력 웹 서비스 매개 변수의 값을 전달해야 합니다.</p><p>작업 JSON의 webServiceParameters 섹션을 사용하여 다른 웹 서비스 매개 변수에 대한 값을 전달할 수 있습니다.</p> | <p>데이터 팩터리 서비스는 OUTPUT 연결된 서비스의 연결 문자열 정보를 사용하여 "데이터베이스 서버 이름", "데이터베이스 이름", "서버 사용자 계정 이름", "서버 사용자 계정 암호"라는 이름으로 웹 서비스 매개 변수를 생성합니다. Azure 기계 학습 스튜디오에서는 웹 서비스 매개 변수에 이러한 기본 이름을 사용해야 합니다.</p><p>작업 JSON의 webServiceParameters 섹션을 사용하여 다른 웹 서비스 매개 변수에 대한 값을 전달할 수 있습니다. <p>입력 Blob이 입력 위치로 사용됩니다.</p> |
|출력이 Azure Blob임 | 데이터 팩터리 서비스는 INPUT 연결된 서비스의 연결 문자열 정보를 사용하여 "데이터베이스 서버 이름", "데이터베이스 이름", "서버 사용자 계정 이름", "서버 사용자 계정 암호"라는 이름으로 웹 서비스 매개 변수를 생성합니다. Azure 기계 학습 스튜디오에서는 웹 서비스 매개 변수에 이러한 기본 이름을 사용해야 합니다. | <p>작업 JSON의 WebServiceParameters 섹션을 사용하여 웹 서비스 매개 변수에 대한 값을 전달해야 합니다.</p><p>Blob이 입력 및 출력 위치로 사용됩니다.</p> |
    

> [AZURE.NOTE]Azure SQL 기록기가 ID 열을 덮어쓰는 경우 키 위반이 발생할 수 있습니다. 이 상황을 방지하려면 출력 테이블을 구조화해야 합니다.
> 
> 스테이징 테이블을 저장 프로시저 작업과 함께 사용하여 행을 병합하거나 평가 전에 데이터를 자를 수 있습니다. 이 접근 방법을 사용하는 경우 executionPolicy의 동시성 설정을 1로 설정합니다.

#### 원본인 Azure Blob

Azure 기계 학습 실험에서 판독기 모듈을 사용하는 경우 입력으로 Azure Blob를 지정할 수 있습니다. Azure blob 저장소에 있는 파일은 HDInsight에서 실행되는 Pig 및 Hive 스크립트에서 생성되는 출력 파일(예: 000000\_0)일 수 있습니다. 판독기 모듈을 사용하면 아래와 같이 파일을 포함하는 컨테이너 폴더를 가리키는 판독기 모듈의 **컨테이너, 디렉터리 또는 blob에 대한 경로** 속성을 구성하여 (확장명이 없는) 파일을 읽을 수 있습니다. **컨테이너/폴더에 있는 모든 파일을 지정하는(예를 들어data/aggregateddata/year=2014/month-6/*)** 별표(즉, *)는 실험의 일부로 읽어야 합니다.

![Azure Blob 속성](./media/data-factory-create-predictive-pipelines/azure-blob-properties.png)

### 웹 서비스 매개 변수 사용 예제
#### AzureMLBatchScoringActivity 및 웹 서비스 매개 변수가 포함된 파이프라인

	{
	  "name": "MLWithSqlReaderSqlWriter",
	  "properties": {
	    "description": "Azure ML model with sql azure reader/writer",
	    "activities": [
	      {
	        "name": "MLSqlReaderSqlWriterActivity",
	        "type": "AzureMLBatchScoring",
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
	        "linkedServiceName": "MLSqlReaderSqlWriterScoringModel",
	        "policy": {
	          "concurrency": 1,
	          "executionPriorityOrder": "NewestFirst",
	          "retry": 1,
	          "timeout": "02:00:00"
	        },
	        "typeProperties": {
	          "webServiceParameters": {
	            "Database server name1": "output.database.windows.net",
	            "Database name1": "outputDatabase",
	            "Server user account name1": "outputUser",
	            "Server user account password1": "outputPassword",
	            "Comma separated list of columns to be saved": "CustID, Scored Labels, Scored Probabilities",
	            "Data table name": "BikeBuyerPredicted"
	          }
	        }
	      }
	    ],
	    "start": "2015-02-13T00:00:00Z",
	    "end": "2015-02-14T00:00:00Z"
	  }
	}
 
위 JSON 예제에서

- Azure 기계 학습 모델은 Azure SQL 판독기와 Azure SQL 기록기를 둘 다 사용합니다.
- 웹 서비스를 통해 노출되는 경우 매개 변수에 기본 이름이 사용됩니다.
	- **판독기**의 경우: 데이터베이스 서버 이름, 데이터베이스 이름, 서버 사용자 계정 이름 및 서버 사용자 계정 암호
	- **기록기**의 경우: 데이터베이스 서버 이름1, 데이터베이스 이름1, 서버 사용자 계정 이름1 및 서버 사용자 계정 암호1
	
		이 경우 판독기와 기록기는 매개 변수를 공유하지 않습니다.  
- 데이터 팩터리 서비스는 이름이 **데이터베이스 서버 이름**, **데이터베이스 이름**, **서버 사용자 계정 이름** 및 **서버 사용자 계정 암호**로, 입력 판독기의 이름과 일치하는 웹 서비스 매개 변수의 값을 자동으로 생성합니다. 따라서 아래 작업 JSON에서 **webServiceParameters**를 통해 이러한 매개 변수의 값을 명시적으로 전달할 필요가 없습니다.  
- 기록기 매개 변수('1' 접미사가 있는 매개 변수)는 데이터 팩터리 서비스에 의해 자동으로 채워지지 않습니다. 따라서 작업 JSON의 **webServiceParameters** 섹션에서 이러한 매개 변수의 값을 지정해야 합니다.  
- **고객 ID**, **점수가 매겨진 레이블** 및 **점수가 매겨진 확률**은 쉼표로 구분된 열로 저장됩니다. 
- 이 예제의 **데이터 테이블 이름**은 출력 데이터베이스의 테이블에 해당합니다.
- **시작** 및 **끝** 모두는 [ISO 형식](http://en.wikipedia.org/wiki/ISO_8601)이어야 합니다. 예: 2014-10-14T16:32:41Z. **end** 시간은 선택 사항입니다. **end** 속성 값을 지정하지 않는 경우 "**start + 48시간**"으로 계산됩니다. 파이프라인을 무기한 실행하려면 **end** 속성 값으로 **9999-09-09**를 지정합니다. JSON 속성에 대한 자세한 내용은 [JSON 스크립트 참조](https://msdn.microsoft.com/library/dn835050.aspx)를 참조하세요.




## 참고 항목

문서 | 설명
------ | ---------------
[Azure 데이터 팩터리 개발자 참조][developer-reference] | 개발자 참조에는 cmdlet, JSON 스크립트, .NET 클래스 라이브러리, 함수 등에 대한 포괄적인 참조 콘텐츠가 포함되어 있습니다. 

[adf-introduction]: data-factory-introduction.md
[adf-getstarted]: data-factory-get-started.md
[use-onpremises-datasources]: data-factory-use-onpremises-datasources.md
[use-pig-and-hive-with-data-factory]: data-factory-pig-hive-activities.md
[adf-tutorial]: data-factory-tutorial.md
[use-custom-activities]: data-factory-use-custom-activities.md
[troubleshoot]: data-factory-troubleshoot.md
[data-factory-introduction]: data-factory-introduction.md
[developer-reference]: http://go.microsoft.com/fwlink/p/?LinkId=516908

[azure-machine-learning]: http://azure.microsoft.com/services/machine-learning/
[machine-learning-dashboard]: ./media/data-factory-create-predictive-pipelines/AzureMLDashboard.png

 

<!---HONumber=August15_HO6-->