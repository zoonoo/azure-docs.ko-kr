<properties 
	pageTitle="데이터 팩터리 - 데이터 팩터리 및 기계 학습을 사용하여 예측 파이프라인 만들기 | Azure" 
	description="만드는 방법에 설명 합니다. Azure 기계 학습 및 Azuer 데이터 팩터리를 사용 하 여 예측 파이프라인 만들기" 
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
	ms.date="05/05/2015" 
	ms.author="spelluru"/>

# Azure 데이터 팩터리 및 Azure 기계 학습을 사용하여 예측 파이프라인 만들기 
## 개요
운용할 수 게시 [Azure 기계 학습][azure-machine-learning] Azure 데이터 팩터리 파이프라인 내에서 모델입니다. 이러한 파이프라인 예측 파이프라인 이라고 합니다. 예측 파이프라인을 만들려면 다음이 필요합니다.

-	게시된 작업 영역 모델의 API 키 및 배치 평가 URL(아래 이미지 참조)
-	Azure blob 저장소 입력된 CSV 파일 (또는)는 입력을 포함 하는 Azure SQL 데이터베이스를 보유 데이터를 기록 합니다. 
-	점수 매기기 결과 CSV 파일 (또는) 출력 데이터를 보유 하는 Azure SQL 데이터베이스를 보유 하는 Azure blob 저장소입니다. 

	![컴퓨터 학습 대시보드][machine-learning-dashboard]

	AzureMLLinkedService로에서 가져온 위의 그림에 표시 된에 대 한 URL을 평가 하는 일괄 처리에서 뺀 ' * * 도움말 * * ': https://ussouthcentral.services.azureml.net/workspaces/da9e350b758e44b2812a6218d507e216/services/8c91ff373a81416f8f8e0d96a1162681/jobs/

A **예측 파이프라인** 이러한 부분으로 구성 합니다.

-	입력 및 출력 테이블
-	Azure 저장소/Azure SQL 및 Azure ML 서비스 연결
-	Azure ML 배치 평가 작업이 포함된 파이프라인

> [AZURE.NOTE]Azure 데이터 팩터리 (ADF) 파이프라인에서 게시 된 Azure 컴퓨터 학습 웹 서비스에 의해 노출 되는 웹 서비스 매개 변수를 사용할 수 있습니다. 자세한 내용은이 문서에서는 웹 서비스 매개 변수 섹션을 참조 합니다.

## 예
이 예제에서는 입력 및 출력 데이터를 저장할 Azure 저장소를 사용 합니다. 또한 Azure 저장소를 사용 하는 대신 Azure SQL 데이터베이스를 사용할 수 있습니다.

통해 여러분이 개발 하는 것이 좋습니다는 [Azure 데이터 팩터리 시작][adf-getstarted] 이 예에서 데이터 팩터리 아티팩트 (예: 연결 된 서비스, 테이블, 파이프라인)를 만들려면 데이터 팩터리 편집기를 사용 하 고이 예제를 통해 되기 전에 자습서입니다.
 

1. Azure 저장소에 대한 연결된 서비스를 만듭니다. 평가 입력 및 출력 파일이 서로 다른 저장소 계정에 있는 경우 연결된 서비스가 두 개 필요합니다. 다음은 JSON 예제입니다.

		{
		    "name": "StorageLinkedService",
		    "properties":
		    {
		        "type": "AzureStorageLinkedService",
		        "connectionString": "DefaultEndpointsProtocol=https;AccountName=[acctName];AccountKey=[acctKey]"
		    }
		}

2. 입력 및 출력 Azure 데이터 팩터리 테이블을 만듭니다. 다른 데이터 팩터리 테이블과 달리 이러한 모두 포함 되도록 모두 참고 **folderPath** 및 **fileName** 값입니다. 분할을 사용하여 각 배치 실행(각 데이터 조각)이 고유한 입력 및 출력 파일을 처리하거나 생성하도록 할 수 있습니다. 입력을 CSV 파일 형식으로 변환하여 각 조각의 저장소 계정에 배치하는 업스트림 작업을 포함해야 할 것입니다. 이 경우 아래 예제에 표시된 “waitOnExternal” 설정은 포함하지 않으며, ScoringInputBlob은 다른 작업의 출력 테이블이 됩니다.

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
	
	Csv 파일을 평가 하는 일괄 처리에 열 머리글 행이 있어야 합니다. 사용 하는 경우는 **복사 활동** 만들기/move로 csv blob 저장소로, 싱크 속성을 설정 해야 **blobWriterAddHeader** 를 **true**. 예:
	
	     sink: 
	     {
	         "type": "BlobSink",     
	         "blobWriterAddHeader": true 
	     }
	 
	Csv 파일에 머리글 행이 없는 경우에 다음과 같은 오류가 표시 될 수 있습니다: **활동에 대 한 오류: 문자열을 읽는 동안 오류가 발생 합니다. 예기치 않은 토큰: StartObject 합니다. 경로 ', 줄 1, 1 위치**합니다.
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


4. 연결 된 형식의 서비스 만들기: **AzureMLLinkedService**, API 키 및 모델 일괄 점수 매기기 URL 제공 합니다.
		
		{
		    "name": "MyAzureMLLinkedService",
		    "properties":
		    {
		        "type": "AzureMLLinkedService",
		        "mlEndpoint":"https://[batch scoring endpoint]/jobs",
		        "apiKey":"[apikey]"
		    }
		}

5. 마지막으로, 포함 하는 파이프라인을 작성 한 **AzureMLBatchScoringActivity**. 이 파이프라인은 입력 테이블에서 입력 파일의 위치를 가져오고, AzureML 배치 평가 API를 호출하며, 배치 평가 출력을 출력 테이블에 지정된 Blob에 복사합니다. 다른 데이터 팩터리 작업과 달리 AzureMLBatchScoringActivity는 입력 및 출력 테이블을 하나씩만 가질 수 있습니다.

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


## 웹 서비스 매개 변수
Azure 데이터 팩터리 (ADF) 파이프라인에서 게시 된 Azure 컴퓨터 학습 웹 서비스에 의해 노출 되는 웹 서비스 매개 변수를 사용할 수 있습니다. 만들고 수 있습니다 실험 Azure 기계 학습에 및 웹 서비스로 게시에서 해당 웹 서비스에 여러 ADF 파이프라인 또는 웹 서비스 매개 변수를 통해 다른 입력 값을 전달 하는 활동을 사용 하 여 합니다.

### 웹 서비스 매개 변수 값을 전달합니다.
추가 **변환** 섹션에 **AzureMLBatchScoringActivty** 다음 예와에서 같이 해당 섹션에서 웹 서비스 매개 변수 값을 지정 하는 JSON 파이프라인의 섹션:

	transformation: {
		webServiceParameters: {
			"Param 1": "Value 1",
			"Param 2": "Value 2"
		}
	}


사용할 수도 있습니다 [데이터 팩터리 함수](https://msdn.microsoft.com/library/dn835056.aspx) 다음 예와에서 같이 서비스 매개 변수는 웹에 대 한 값을 전달 합니다.

	transformation: {
    	webServiceParameters: {
    	   "Database query": "$$Text.Format('SELECT * FROM myTable WHERE timeColumn = \'{0:yyyy-MM-dd HH:mm:ss}\'', Time.AddHours(SliceStart, 0))"
    	}
  	}
 
> [AZURE.NOTE]웹 서비스 매개 변수는 대/소문자 구분, 하므로 작업에서 지정한 이름을 JSON 일치 하도록 웹 서비스에 의해 노출 된 것입니다.

### Azure SQL 판독기 및 작성기
웹 서비스 매개 변수를 사용 하는 일반적인 시나리오에는 Azure SQL 판독기 및 작성기의 사용입니다. 기록기 모듈은 Azure 컴퓨터 학습 Studio 외부 데이터 관리 서비스에 실험에서 데이터를 저장 및 데이터 관리 서비스를 Azure 컴퓨터 학습 Studio 외부에서 실험에 데이터를 로드 판독기 모듈 사용 됩니다. Azure Blob/Azure SQL 판독기/기록기에 대 한 세부 정보를 참조 하십시오. [판독기](https://msdn.microsoft.com/library/azure/dn905997.aspx) 및 [기록기](https://msdn.microsoft.com/library/azure/dn905984.aspx) MSDN Library에 대 한 항목입니다. 이전 섹션의 예제에서는 Azure Blob 판독기 및 작성기 Azure Blob을 사용 합니다. 이 섹션에서는 Azure SQL 판독기 및 Azure SQL 기록기를 사용 하 여 설명 합니다.

#### Azure SQL 판독기
Azure ML Studio에서 실험을 작성 하 고 입력에는 Azure SQL 판독기를 통해 웹 서비스를 게시할 수 있습니다. Azure SQL 판독기에 웹 서비스를 매개 변수로 노출 될 수 있는 연결 속성이 요청을 평가 하는 일괄 처리에서 런타임에 전달할 연결 속성에 대 한 값을 허용 합니다.

런타임에 웹 서비스 매개 변수를 작성 하는 입력된 데이터 팩터리 테이블에서 세부 정보 데이터 팩터리 서비스에 의해 사용 됩니다. Note 해야 사용 하는 웹 서비스 매개 변수에 대 한 기본 이름 (데이터베이스 서버 이름, 데이터베이스 이름, 서버 사용자 계정 이름, 서버 사용자 계정 암호)이 통합 데이터 팩터리 서비스와 작동 하도록 합니다.

모든 추가 웹 서비스 매개 변수를 사용 하는 경우는 **webServiceParameters** JSON 활동의 섹션입니다. 이 섹션에서는 Azure SQL 판독기 매개 변수에 대 한 값을 지정 하면 값 입력된 Azure SQL 연결 서비스에서 선택 값을 재정의 합니다. WebServiceParameters 섹션에서 직접 Azure SQL 판독기에 대 한 값을 지정 하는 것은 좋지 않습니다. 에 대 한 추가 매개 변수 값을 전달 하는 섹션을 사용 합니다.

Azure 데이터 팩터리 파이프라인을 통해는 Azure SQL 판독기를 사용 하려면 다음을 수행 합니다.

- 만들기는 **SQL Azure 연결 서비스**. 
- 데이터 팩터리 만들기 **테이블** 사용 하 여 **AzureSqlTableLocation**.
- 해당 데이터 팩터리 설정 **테이블** 으로 **입력** 에 대 한는 **AzureMLBatchScoringActivity** JSON 파이프라인에서. 



#### Azure SQL 기록기
마찬가지로 Azure SQL 판독기는 Azure SQL 기록기 있을 수 있습니다 또한 웹 서비스 매개 변수로 노출 하는 속성입니다. Azure SQL 기록기는 설정 중 하나에서 입력된 테이블 또는 출력 테이블에 연결 된 연결 된 서비스를 사용 합니다. 다음 표에서 출력 및 연결 된 서비스를 사용 하는 입력에서 서비스를 연결 하는 경우를 설명 합니다.

<table>
<tr>
<td>출력/입력</td>
<td><b>입력이 SQL Azure</b></td>
<td><b>입력이 Azure Blob</b></td>
</tr>
<tr>
<td><b>출력은 SQL Azure</b></td>
<td><p>데이터 팩터리 서비스 연결 된 입력 서비스에서 연결 문자열 정보를 사용 하 여 이름으로 웹 서비스 매개 변수를 생성: "데이터베이스 서버 이름", "데이터베이스 이름", "서버 사용자 계정 이름", "서버 사용자 계정 암호"입니다. Azure ML Studio에서 웹 서비스 매개 변수에 대 한 이러한 기본 이름을 사용 해야 하는 참고 합니다.</p>
<p>Azure SQL 판독기 및 Azure ML 모델에서 Azure SQL 기록기는 위에서 언급 한 동일한 웹 서비스 매개 변수를 공유 하는 경우 세밀 하 게 됩니다. 공유 하지 않으므로 동일한 웹 서비스 paramers, 예를들어, Azure SQL 기록기에 매개 변수 이름을 사용 하는 경우 경우: name1 서버, 데이터베이스 name1, 서버 사용자 계정 name1, 서버 사용자 데이터베이스 ('1' 끝)로 password1 account, JSON 활동의 webServiceParameters 섹션에서 이러한 출력 웹 서비스 매개 변수 값을 전달 해야 합니다.</p>
<p>
JSON 활동의 webServiceParameters 섹션을 사용 하 여 다른 웹 서비스 매개 변수의 값을 전달할 수 있습니다.  
</p>

</td>
<td>
<p>데이터 팩터리 서비스 연결 된 출력 서비스에서 연결 문자열 정보를 사용 하 여 이름으로 웹 서비스 매개 변수를 생성: "데이터베이스 서버 이름", "데이터베이스 이름", "서버 사용자 계정 이름", "서버 사용자 계정 암호"입니다. Azure ML Studio에서 웹 서비스 매개 변수에 대 한 이러한 기본 이름을 사용 해야 하는 참고 합니다.</p>
<p>JSON 활동의 webServiceParameters 섹션을 사용 하 여 다른 웹 서비스 매개 변수의 값을 전달할 수 있습니다. <p>입력된 blob의 위치를 입력된으로 사용 됩니다.</p>
</td>
</tr>
<tr>
<td><b>출력은 Azure Blob</b></td>
<td>데이터 팩터리 서비스 연결 된 입력 서비스에서 연결 문자열 정보를 사용 하 여 이름으로 웹 서비스 매개 변수를 생성: "데이터베이스 서버 이름", "데이터베이스 이름", "서버 사용자 계정 이름", "서버 사용자 계정 암호"입니다. Azure ML Studio에서 웹 서비스 매개 변수에 대 한 이러한 기본 이름을 사용 해야 하는 참고 합니다.
</td>
<td>
<p>JSON 활동의 WebServiceParameters 섹션을 사용 하 여 모든 웹 서비스 매개 변수 값을 전달 해야 합니다.</p> 

<p>Blob 위치를 입력 및 출력으로 사용 됩니다.</p>

</td>
<tr>

</table>

> [AZURE.NOTE]Azure SQL 기록기는 id 열을 덮어쓰지는 키 위반이 발생할 수 있습니다. 이 상황을 방지 하려면 출력 테이블을 구성 하는 확인 해야 합니다.
> 
> 행을 병합 하거나 평가 하기 전에 데이터를 자르는 데 저장 프로시저 활동과 준비 테이블을 사용할 수 있습니다. 이 방법을 사용 하는 경우 실행 정책의 동시성 설정을 1로 설정 합니다.

### 웹 서비스 매개 변수를 사용 하 여의 예
#### 웹 서비스 매개 변수를 사용 하는 AzureMLBatchScoringActivity 포함 된 파이프라인

	{
		"name": "MLWithSqlReaderSqlWriter",
	  	"properties": {
		    "description": "Azure ML model with sql azure reader/writer",
		    "activities": [
		    	{
		    	    "name": "MLSqlReaderSqlWriterActivity",
		    	    "type": "AzureMLBatchScoringActivity",
		    	    "description": "test",
		        	"inputs": [ { "name": "MLSqlInput" } ],
		        	"outputs": [ { "name": "MLSqlOutput" } ],
		        	"linkedServiceName": "MLSqlReaderSqlWriterScoringModel",
		        	"policy": {
		          		"concurrency": 1,
			          	"executionPriorityOrder": "NewestFirst",
			          "retry": 1,
			          "timeout": "02:00:00"
			        },
			        transformation: {
			        	webServiceParameters: {
		            		"Database server name1": "output.database.windows.net",
				            "Database name1": "outputDatabase",
		            		"Server user account name1": "outputUser",
		            		"Server user account password1": "outputPassword",
			           		"Comma separated list of columns to be saved": "CustID, Scored Labels, Scored Probabilities",
		    		        "Data table name": "BikeBuyerPredicted" 
		          		}  
		        	}
		      	}
	    	]
		}
	}
 
위의 JSON 예제:

- Azure SQL 판독기 및 Azure SQL 기록기를 모두 Azure ML 모델 사용
- 매개 변수에 대 한 기본 이름이 사용 되므로 웹 서비스를 통해 노출 하는 경우
	- 에 대 한는 **판독기**: 데이터베이스 서버 이름, 데이터베이스 이름, 서버 사용자 계정 이름 및 서버 사용자 계정 암호입니다.
	- 에 대 한는 **기록기**: 데이터베이스 서버 name1, 데이터베이스 name1, 서버 사용자 계정 name1, 및 사용자 계정 password1 서버.
	
		판독기 및 작성기 공유 하지 않습니다 매개 변수가 경우 참고 합니다.  
- 데이터 팩터리 서비스에 대 한 이름으로 웹 서비스 매개 변수 값을 자동으로 생성 **데이터베이스 서버 이름**, **데이터베이스 이름**, **서버 사용자 계정 이름**, 및 **서버 사용자 계정 암호**, 입력된 판독기의 이름과 일치는 합니다. 명시적으로 이러한 매개 변수를 통해에 대 한 값을 전달할 필요가 없습니다 따라서 **webServiceParameters** 활동 JSON 아래에 있습니다.  
- 매개 변수 ('1' 접미사가 선언은) 기록기를 자동으로 채워지지 데이터 팩터리 서비스에 의해. 따라서 이러한 매개 변수에서 값을 지정 해야는 **webServiceParameters** JSON 활동의 섹션입니다.  
- **고객 ID**, **레이블 점수가**, 및 **확률 점수가 부여 됩니다** 쉼표로 구분 된 열으로 저장 됩니다. 
-  **데이터 테이블 이름** 이 예제의 출력 데이터베이스의 테이블에 해당 합니다.




## 참고 항목

문서 | 설명
------ | ---------------
[Azure 데이터 팩터리 개발자 참조][developer-reference] | 개발자 참조는 포괄적인 참조 콘텐츠가 cmdlet, JSON 스크립트,.NET 클래스 라이브러리, 함수 등... 

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

<!---HONumber=GIT-SubDir--> 