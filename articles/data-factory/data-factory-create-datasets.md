<properties 
	pageTitle="Azure 데이터 팩터리의 데이터 집합 | Microsoft Azure" 
	description="Azure Data Factory 데이터 집합을 이해하고 만드는 방법을 알아봅니다." 
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
	ms.date="04/08/2016" 
	ms.author="spelluru"/>

# Azure 데이터 팩터리의 데이터 집합
Azure Data Factory의 데이터 집합은 파이프라인에서 작업의 입력 또는 출력으로 사용하려는 데이터에 대해 명명된 참조/포인터입니다. 데이터 집합은 테이블, 파일, 폴더, 문서를 비롯한 다양한 데이터 저장소 내의 데이터를 식별합니다.

데이터 팩터리를 만들 때 데이터 저장소를 데이터 팩터리에 연결하는 연결된 서비스를 만듭니다. **연결된 서비스**는 Azure Data Factory가 데이터 저장소에 **연결**하기 위해 필요한 정보를 정의합니다. 예를 들어 Azure 저장소 계정 및 Azure SQL 데이터베이스입니다. 연결된 서비스는 데이터 저장소에 액세스하는 메커니즘(주소, 프로토콜, 인증 체계 등)을 정의합니다.

Data Factory의 **데이터 집합**은 파이프라인에서 작업의 입력 또는 출력으로 사용할 수 있는 이 데이터 저장소 내의 데이터 구조(예: Blob 컨테이너, SQL 테이블)를 나타냅니다. 데이터 집합을 만든 후 파이프라인의 작업에 사용할 수 있습니다. 예를 들어 복사 작업/HDInsightHive 작업의 입력/출력 데이터 집합인 데이터 집합이 있을 수 있습니다.

> [AZURE.NOTE] Azure Data Factory를 처음 접하는 경우 Azure Data Factory 서비스 개요에 대한 [Azure Data Factory 소개](data-factory-introduction.md) 및 첫 번째 데이터 팩터리를 만드는 자습서에 대한 [첫 번째 데이터 팩터리 빌드](data-factory-build-your-first-pipeline.md)를 참조하세요. 이 두 문서는 본 문서를 더 잘 이해하는 데 필요한 배경 정보를 제공합니다.

## 데이터 집합 정의
Azure Data Factory의 데이터 집합은 다음과 같이 정의됩니다.


	{
	    "name": "<name of dataset>",
	    "properties": {
	        "type": "<type of dataset: AzureBlob, AzureSql etc...>",
			"external": <boolean flag to indicate external data. only for input datasets>,
	        "linkedServiceName": "<Name of the linked service that refers to a data store.>",
	        "structure": [
	            {
	                "name": "<Name of the column>",
	                "type": "<Name of the type>"
	            }
	        ],
	        "typeProperties": {
	            "<type specific property>": "<value>",
				"<type specific property 2>": "<value 2>",
	        },
	        "availability": {
	            "frequency": "<Specifies the time unit for data slice production. Supported frequency: Minute, Hour, Day, Week, Month>",
	            "interval": "<Specifies the interval within the defined frequency. For example, frequency set to 'Hour' and interval set to 1 indicates that new data slices should be produced hourly>"
	        },
	       "policy": 
	        {      
	        }
	    }
	}

다음 표에서는 위의 JSON에서 속성을 설명합니다.

| 속성 | 설명 | 필수 | 기본값 |
| -------- | ----------- | -------- | ------- |
| name | 데이터 집합의 이름입니다. 명명 규칙은 [Azure Data Factory - 명명 규칙](data-factory-naming-rules.md)을 참조하세요. | 예 | 해당 없음 |
| type | 데이터 집합의 형식입니다. Azure Data Factory에서 지원되는 형식(예: AzureBlob, AzureSqlTable) 중 하나를 지정합니다. <br/><br/>자세한 내용은 [데이터 집합 형식](#Type)을 참조하세요. | 예 | 해당 없음 |
| structure | 데이터 집합의 스키마<br/><br/>자세한 내용은 [데이터 집합 구조](#Structure) 섹션을 참조하세요. | 번호 | 해당 없음 |
| typeProperties | 선택한 형식에 해당하는 속성입니다. 지원되는 형식 및 해당 속성에 대한 자세한 내용은 [데이터 집합 형식](#Type) 섹션을 참조하세요. | 예 | 해당 없음 |
| external | 데이터 집합이 데이터 팩터리 파이프라인에 의해 명시적으로 생성되는지를 지정하는 부울 플래그입니다. | 아니요 | false | 
| availability | 데이터 집합 생성에 대한 처리 창 또는 조각화 모델을 정의합니다. <br/><br/>자세한 내용은 [데이터 집합 가용성](#Availability) 항목을 참조하세요<br/><br/>모델을 조각화하는 데이터 집합에 대한 자세한 내용은 [일정 예약 및 실행](data-factory-scheduling-and-execution.md) 문서를 참조하세요. | 예 | 해당 없음
| policy | 데이터 집합 조각이 충족해야 하는 기준 또는 조건을 정의합니다. <br/><br/>자세한 내용은 [데이터 집합 정책](#Policy) 항목을 참조하세요. | 아니요 | 해당 없음 |

### 예제

아래는 **Azure SQL 데이터베이스**에서 **MyTable**이라는 테이블을 나타내는 데이터 집합의 예입니다.

	{
	    "name": "DatasetSample",
	    "properties": {
	        "type": "AzureSqlTable",
	        "linkedServiceName": "AzureSqlLinkedService",
	        "typeProperties": 
	        {
	            "tableName": "MyTable"
	        },
	        "availability": 
	        {
	            "frequency": "Day",
	            "interval": 1
	        }
	    }
	}

다음 사항에 유의하세요.

- type을 AzureSQLTable로 설정합니다.
- AzureSqlTable 형식과 관련된 tableName 형식 속성은 MyTable로 설정됩니다.
- linkedServiceName은 AzureSqlDatabase 형식의 연결된 서비스를 가리킵니다. 아래 연결된 서비스의 정의를 참조하세요. 
- availability frequency를 Day로 설정하고 interval은 1로 설정합니다. 이는 조각이 매일 생성되는 것을 의미합니다.  

AzureSqlLinkedService는 다음과 같이 정의됩니다.

	{
	    "name": "AzureSqlLinkedService",
	    "properties": {
	        "type": "AzureSqlDatabase",
	        "description": "",
	        "typeProperties": {
	            "connectionString": "Data Source=tcp:<servername>.database.windows.net,1433;Initial Catalog=<databasename>;User ID=<username>@<servername>;Password=<password>;Integrated Security=False;Encrypt=True;Connect Timeout=30"
	        }
	    }
	}

위의 JSON에서

- type을 AzureSqlDatabase로 설정합니다.
- connectionString 형식 속성은 Azure SQL 데이터베이스에 연결하는 정보를 지정합니다.  


여기에서 볼 수 있듯이 연결된 서비스는 Azure SQL 데이터베이스에 연결하는 방법을 정의하고 데이터 집합은 데이터 팩터리의 입력/출력으로 사용되는 테이블을 정의합니다. [파이프라인](data-factory-create-pipelines.md) JSON에서 작업 섹션은 데이터 집합이 입력 또는 출력 데이터 집합으로 사용되는지 여부를 지정합니다.


> [AZURE.IMPORTANT] 데이터 집합이 Azure Data Factory에서 생성되지 않는 한 **external**로 표시되어야 합니다. 이는 파이프라인에서 첫 번째 작업의 입력에 일반적으로 적용됩니다.

## <a name="Type"></a> 데이터 집합 형식
지원되는 데이터 원본 및 데이터 집합 형식을 정렬합니다. 데이터 집합의 유형 및 구성에 대한 내용은 [데이터 이동 활동](data-factory-data-movement-activities.md#supported-data-stores) 문서에서 항목을 참조하세요. 예를 들어 Azure SQL 데이터베이스의 데이터를 사용하는 경우 원본 또는 싱크 데이터 저장소로 Azure SQL 데이터베이스를 사용하는 방법에 대한 자세한 내용을 보려면 지원되는 데이터 저장소 목록에서 Azure SQL 데이터베이스를 클릭합니다.

## <a name="Structure"></a>데이터 집합 구조
**구조** 섹션은 데이터 집합의 스키마를 정의합니다. 이름 및 데이터 형식 열의 컬렉션을 포함합니다. 다음 예제에서는 데이터 집합에 3개의 열 slicetimestamp, projectname 및 pageviews가 있으며 형식은 각각 문자열, 문자열 및 10진수입니다.

	structure:  
	[ 
	    { "name": "slicetimestamp", "type": "String"},
	    { "name": "projectname", "type": "String"},
	    { "name": "pageviews", "type": "Decimal"}
	]

## <a name="Availability"></a> 데이터 집합 가용성
데이터 집합의 **가능성** 섹션은 데이터 집합에 대한 처리 주기(매시, 매일, 매주 등) 또는 조각화 모델을 정의합니다. 데이터 집합 조각화 및 종속성 모델에 대한 자세한 내용은 [예약 및 실행](data-factory-scheduling-and-execution.md) 문서를 참조하세요.

아래 availability 섹션에서는 출력 데이터 집합의 경우 시간마다 (또는) 입력 데이터 집합의 경우 사용할 수 있는 시간마다 데이터 집합이 생성되도록 지정합니다.

	"availability":	
	{	
		"frequency": "Hour",		
		"interval": 1	
	}

다음 표에서는 availability 섹션에서 사용할 수 있는 속성을 설명합니다.

| 속성 | 설명 | 필수 | 기본값 |
| -------- | ----------- | -------- | ------- |
| frequency | 데이터 집합 조각 생성을 위한 시간 단위를 지정합니다.<br/><br/>**지원되는 빈도**: 분, 시, 일, 주, 월 | 예 | 해당 없음 |
| interval | 빈도 승수를 지정합니다.<br/><br/>"빈도 x 간격"은 조각을 생성하는 빈도를 결정합니다.<br/><br/>데이터 집합을 시간 단위로 조각화해야 하는 경우 **Frequency**를 **Hour**로, **interval**을 **1**로 지정합니다.<br/><br/>**참고:** 빈도를 분으로 지정하면 15 이상으로 간격을 설정하는 것이 좋습니다. | 예 | 해당 없음 |
| style | 간격의 시작/끝에 조각을 생성해야 하는지를 지정합니다.<ul><li>StartOfInterval</li><li>EndOfInterval</li></ul><br/><br/>Frequency를 Month로 설정하고 style을 EndOfInterval로 설정하는 경우 조각을 월의 마지막 날에 생성합니다. style을 StartOfInterval로 설정하는 경우 조각을 달의 첫 번째 날에 생성합니다.<br/><br/>Frequency를 Day로 설정하고 style을 EndOfInterval로 설정하는 경우 조각을 일의 마지막 시간에 생성합니다.<br/><br/>Frequency를 Hour로 설정하고 style을 EndOfInterval로 설정하는 경우 조각을 시간의 끝에 생성합니다. 예를 들어 오후 1~2시 기간에 대한 조각은 오후 2시에 생성됩니다. | 아니요 | EndOfInterval |
| anchorDateTime | 스케줄러에서 사용하는 시간에 절대 위치를 정의하여 데이터 집합 조각 경계를 계산합니다. <br/><br/>**참고:** AnchorDateTime에 빈도보다 더 세분화된 날짜 부분이 있는 경우 더 세분화된 부분은 무시됩니다. <br/><br/>예를 들어 **간격**이 **매시간**(frequency: hour 및 interval:1회)이고 **AnchorDateTime**이 **분 및 초**를 포함하는 경우 AnchorDateTime의 **분 및 초** 부분은 무시됩니다. | 아니요 | 01/01/0001 |
| offset | 모든 데이터 집합 조각의 시작 및 끝이 이동에 의한 Timespan입니다. <br/><br/>**참고:** anchorDateTime 및 offset이 모두 지정되면 결과적으로 이동이 결합됩니다. | 아니요 | 해당 없음 |

### offset example

기본 자정 대신 오전 6시에 시작하는 일별 조각입니다.

	"availability":
	{
		"frequency": "Day",
		"interval": 1,
		"offset": "06:00:00"
	}

**빈도**는 **월**로 **간격**은 **1**(한 달에 한 번)로 설정: 매월 9일 오전 6시에 조각을 생성하려면 "09.06:00:00"에 대한 오프셋을 설정합니다. 이 경우 UTC 시간으로 해야 합니다.

12개월(빈도 = 월; 간격 = 12) 예약의 경우, 윤년이든 아니든 연도에 따라 오프셋: 60.00:00:00은 매년 3월 1일 또는 2일을 의미합니다(스타일 = StartOfInterval인 경우 년의 시작부터 60일).

### anchorDateTime 예제

**예제:** 2007-04-19T08:00:00에 시작하는 23시간 데이터 집합 조각

	"availability":	
	{	
		"frequency": "Hour",		
		"interval": 23,	
		"anchorDateTime":"2007-04-19T08:00:00"	
	}

### offset/style 예제

매달 특정 날짜 및 시간(매달 셋째 날 오전 8시라고 가정)에 데이터 집합이 필요한 경우 **오프셋** 태그를 사용하여 실행할 날짜 및 시간을 설정할 수 있습니다.

	{
	  "name": "MyDataset",
	  "properties": {
	    "type": "AzureSqlTable",
	    "linkedServiceName": "AzureSqlLinkedService",
	    "typeProperties": {
	      "tableName": "MyTable"
	    },
	    "availability": {
	      "frequency": "Month",
	      "interval": 1,
	      "offset": "3.08:10:00",
	      "style": "StartOfInterval"
	    }
	  }
	}


## <a name="Policy"></a>데이터 집합 정책

데이터 집합 정의의 **정책** 섹션에서 데이터 집합 조각이 충족해야 하는 기준 또는 조건을 정의합니다.

### 정책 유효성 검사

| 정책 이름 | 설명 | 에 적용 | 필수 | 기본값 |
| ----------- | ----------- | ---------- | -------- | ------- |
| minimumSizeMB | **Azure Blob**에서 데이터가 최소 크기 요구 사항(메가바이트)을 충족하는지 확인합니다. | Azure Blob | 아니요 | 해당 없음 |
|minimumRows | **Azure SQL 데이터베이스** 또는 **Azure 테이블**에서 데이터가 최소 행 수를 포함하는지 확인합니다. | <ul><li>Azure SQL 데이터베이스</li><li>Azure 테이블</li></ul> | 아니요 | 해당 없음

#### 예

**minimumSizeMB:**

	"policy":
	
	{
	    "validation":
	    {
	        "minimumSizeMB": 10.0
	    }
	}

**minimumRows**

	"policy":
	{
		"validation":
		{
			"minimumRows": 100
		}
	}

### 외부 데이터 집합

외부 데이터 집합은 데이터 팩토리에서 실행 중인 파이프라인에 의해 생성되지 않습니다. 데이터 집합이 **외부**로 표시된 경우 **ExternalData** 정책을 정의하여 데이터 집합 조각 가용성의 동작에 영향을 미칠 수 있습니다.

데이터 집합이 Azure Data Factory에서 생성되지 않는 한 **external**로 표시되어야 합니다. 이는 작업 또는 파이프라인 연결을 활용하고 있지 않는 한 파이프라인에서 첫 번째 작업의 입력에 일반적으로 적용됩니다.

| 이름 | 설명 | 필수 | 기본값 |
| ---- | ----------- | -------- | -------------- |
| dataDelay | 지정된 조각에 대한 외부 데이터의 가용성 확인을 지연하는 시간입니다. 예를 들어 데이터를 매시간 사용할 수 있어야 하는 경우 외부 데이터를 실제로 사용할 수 있고 해당 조각이 준비되었는지를 보는 확인은 dataDelay에 의해 지연될 수 있습니다.<br/><br/>현재 시간에만 적용됩니다. 예를 들어 지금이 오후 1시이고 이 값이 10분이라면 유효성 검사는 오후 1시 10분에 시작합니다.<br/><br/>이 설정은 과거의 조각(조각 종료 시간을 가진 조각 + dataDelay < 지금)에 영향을 주지 않습니다. 지연 없이 처리됩니다.<br/><br/>23:59 시간보다 큰 시간은 일.시간:분:초 형식을 사용하여 지정해야 합니다. 예를 들어 24시간을 지정하려면 24:00:00을 사용하는 대신 1.00:00:00을 사용합니다. 24:00:00을 사용하면 24일(24.00:00:00)로 처리됩니다. 1일 4시간의 경우 1:04:00:00을 지정합니다. | 아니요 | 0 |
| retryInterval | 오류 발생과 다음 다시 시도 사이의 대기 시간입니다. 현재 시간에 적용됩니다. 이전 시도가 실패한 경우 마지막 시도 후에 이 정도를 대기합니다. <br/><br/>지금이 오후 1시인 경우 첫 번째 시도를 시작합니다. 첫 번째 유효성 검사를 완료하는 기간이 1분이며 작업이 실패한 경우 다음 재시도는 1시 + 1분(기간) + 1분(다시 시도 간격) = 오후 1시 2분입니다. <br/><br/>과거 조각의 경우 지연 시간이 없습니다. 재시도는 즉시 발생합니다. | 아니요 | 00:01:00 (1분) | 
| retryTimeout | 각 다시 시도에 대한 제한 시간입니다.<br/><br/>10분으로 설정한 경우 유효성 검사를 10분 내에 완료해야 합니다. 유효성 검사를 수행하는 데 10분 보다 오래 걸리는 경우 재시도는 제한 시간을 초과합니다.<br/><br/>유효성 검사에 대한 모든 시도의 시간이 초과되면 조각에 TimedOut이 표시됩니다. | 아니요 | 00:10:00 (10분) |
| maximumRetry | 외부 데이터의 가용성을 검사한 횟수입니다. 허용되는 최대값은 10입니다. | 아니요 | 3 | 

## 범위가 지정된 데이터 집합
**datasets** 속성을 사용하여 파이프라인으로 범위가 지정되는 데이터 집합을 만들 수 있습니다. 이러한 데이터 집합은 다른 파이프라인이 아닌 이 파이프라인 내의 작업에서만 사용할 수 있습니다. 다음 예제에서는 파이프라인 내에서 사용될 두 개의 데이터 집합(InputDataset rdc 및 OutputDataset-rdc)을 사용하는 파이프라인을 정의합니다.

	{
	    "name": "CopyPipeline-rdc",
	    "properties": {
	        "activities": [
	            {
	                "type": "Copy",
	                "typeProperties": {
	                    "source": {
	                        "type": "BlobSource",
	                        "recursive": false
	                    },
	                    "sink": {
	                        "type": "BlobSink",
	                        "writeBatchSize": 0,
	                        "writeBatchTimeout": "00:00:00"
	                    }
	                },
	                "inputs": [
	                    {
	                        "name": "InputDataset-rdc"
	                    }
	                ],
	                "outputs": [
	                    {
	                        "name": "OutputDataset-rdc"
	                    }
	                ],
	                "scheduler": {
	                    "frequency": "Day",
	                    "interval": 1,
	                    "style": "StartOfInterval"
	                },
	                "name": "CopyActivity-0"
	            }
	        ],
	        "start": "2016-02-28T00:00:00Z",
	        "end": "2016-02-28T00:00:00Z",
	        "isPaused": false,
	        "pipelineMode": "OneTime",
	        "expirationTime": "15.00:00:00",
	        "datasets": [
	            {
	                "name": "InputDataset-rdc",
	                "properties": {
	                    "type": "AzureBlob",
	                    "linkedServiceName": "InputLinkedService-rdc",
	                    "typeProperties": {
	                        "fileName": "emp.txt",
	                        "folderPath": "adftutorial/input",
	                        "format": {
	                            "type": "TextFormat",
	                            "rowDelimiter": "\n",
	                            "columnDelimiter": ","
	                        }
	                    },
	                    "availability": {
	                        "frequency": "Day",
	                        "interval": 1
	                    },
	                    "external": true,
	                    "policy": {}
	                }
	            },
	            {
	                "name": "OutputDataset-rdc",
	                "properties": {
	                    "type": "AzureBlob",
	                    "linkedServiceName": "OutputLinkedService-rdc",
	                    "typeProperties": {
	                        "fileName": "emp.txt",
	                        "folderPath": "adftutorial/output",
	                        "format": {
	                            "type": "TextFormat",
	                            "rowDelimiter": "\n",
	                            "columnDelimiter": ","
	                        }
	                    },
	                    "availability": {
	                        "frequency": "Day",
	                        "interval": 1
	                    },
	                    "external": false,
	                    "policy": {}
	                }
	            }
	        ]
	    }
	}

<!---HONumber=AcomDC_0427_2016-->