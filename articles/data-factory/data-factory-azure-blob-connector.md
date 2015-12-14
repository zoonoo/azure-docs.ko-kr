<properties 
	pageTitle="Azure Blob 간 데이터 이동 | Azure 데이터 팩터리" 
	description="Azure Data Factory를 사용하여 Azure Blob 저장소 간 데이터를 이동하는 방법에 대해 알아봅니다." 
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
	ms.date="11/03/2015" 
	ms.author="spelluru"/>

# Azure Data Factory를 사용하여 Azure Blob 간 데이터 이동
이 문서에서는 Azure 데이터 팩토리에서 복사 작업을 사용하여 다른 데이터 저장소와 Azure Blob 간에 데이터를 이동하는 방법을 간략하게 설명합니다. 이 문서는 복사 작업 및 지원되는 데이터 저장소 조합을 사용하여 데이터 이동의 일반적인 개요를 보여주는 [데이터 이동 활동](data-factory-data-movement-activities.md) 문서를 작성합니다.

## 샘플: Azure Blob에서 Azure SQL로 데이터 복사
아래 샘플은 다음을 보여줍니다.

1.	[AzureSqlDatabase](data-factory-azure-sql-connector.md#azure-sql-linked-service-properties) 형식의 연결된 서비스입니다.
2.	[AzureStorage](#azure-storage-linked-service-properties) 형식의 연결된 서비스입니다.
3.	[AzureBlob](#azure-blob-dataset-type-properties) 형식의 입력 [데이터 집합](data-factory-create-datasets.md)입니다.
4.	[AzureSqlTable](data-factory-azure-sql-connector.md#azure-sql-dataset-type-properties) 형식의 출력 [데이터 집합](data-factory-create-datasets.md)입니다.
4.	[BlobSource](#azure-blob-copy-activity-type-properties) 및 [SqlSink](data-factory-azure-sql-connector.md#azure-sql-copy-activity-type-properties)를 사용하는 복사 작업의 [파이프라인](data-factory-create-pipelines.md)입니다.

샘플은 Azure blob에서 Azure SQL 데이터베이스의 테이블로 매시간 시계열에 속한 데이터를 복사합니다. 이 샘플에 사용된 JSON 속성은 샘플 다음에 나오는 섹션에서 설명합니다.

**Azure SQL 연결된 서비스:**

	{
	  "name": "AzureSqlLinkedService",
	  "properties": {
	    "type": "AzureSqlDatabase",
	    "typeProperties": {
	      "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
	    }
	  }
	}

**Azure 저장소 연결된 서비스:**

	{
	  "name": "StorageLinkedService",
	  "properties": {
	    "type": "AzureStorage",
	    "typeProperties": {
	      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
	    }
	  }
	}

**Azure Blob 입력 데이터 집합:**

데이터는 매시간 새 blob에 선택됩니다.(빈도: 1시간, 간격:1회) Blob에 대한 폴더 경로 및 파일 이름은 처리 중인 조각의 시작 시간에 기반하여 동적으로 평가됩니다. 폴더 경로는 연도, 월 및 일 일부 시작 시간을 사용하고 파일 이름은 시작 시간의 시간 부분을 사용합니다. "external": "true" 설정은 데이터 팩터리 서비스에 이 테이블이 데이터 팩터리의 외부에 있으며 데이터 팩터리의 활동에서 생성되지 않았음을 알립니다.

	{
	  "name": "AzureBlobInput",
	  "properties": {
	    "type": "AzureBlob",
	    "linkedServiceName": "StorageLinkedService",
	    "typeProperties": {
	      "folderPath": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}/",
	      "fileName": "{Hour}.csv",
	      "partitionedBy": [
	        {
	          "name": "Year",
	          "value": {
	            "type": "DateTime",
	            "date": "SliceStart",
	            "format": "yyyy"
	          }
	        },
	        {
	          "name": "Month",
	          "value": {
	            "type": "DateTime",
	            "date": "SliceStart",
	            "format": "%M"
	          }
	        },
	        {
	          "name": "Day",
	          "value": {
	            "type": "DateTime",
	            "date": "SliceStart",
	            "format": "%d"
	          }
	        },
	        {
	          "name": "Hour",
	          "value": {
	            "type": "DateTime",
	            "date": "SliceStart",
	            "format": "%H"
	          }
	        }
	      ],
	      "format": {
	        "type": "TextFormat",
	        "columnDelimiter": ",",
	        "rowDelimiter": "\n"
	      }
	    },
	    "external": true,
	    "availability": {
	      "frequency": "Hour",
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

**Azure SQL 출력 데이터 집합:**

샘플은 Azure SQL 데이터베이스의 "MyTable"이라는 테이블에 데이터를 복사합니다. Blob CSV 파일을 포함하려 하면 같은 수의 열을 사용하여 Azure SQL 데이터베이스에 테이블을 만들어야 합니다. 새 행은 매시간 테이블에 추가됩니다.

	{
	  "name": "AzureSqlOutput",
	  "properties": {
	    "type": "AzureSqlTable",
	    "linkedServiceName": "AzureSqlLinkedService",
	    "typeProperties": {
	      "tableName": "MyOutputTable"
	    },
	    "availability": {
	      "frequency": "Hour",
	      "interval": 1
	    }
	  }
	}

**복사 작업을 포함하는 파이프라인:**

파이프라인은 위의 입력 및 출력 데이터 집합을 사용하도록 구성된 복사 작업을 포함하고 매시간 실행하도록 예약됩니다. 파이프라인 JSON 정의에서 **원본** 형식은 **BlobSource**로 설정되고 **싱크** 형식은 **SqlSink**로 설정됩니다.

	{  
	    "name":"SamplePipeline",
	    "properties":{  
	    "start":"2014-06-01T18:00:00",
	    "end":"2014-06-01T19:00:00",
	    "description":"pipeline with copy activity",
	    "activities":[  
	      {
	        "name": "AzureBlobtoSQL",
	        "description": "Copy Activity",
	        "type": "Copy",
	        "inputs": [
	          {
	            "name": "AzureBlobInput"
	          }
	        ],
	        "outputs": [
	          {
	            "name": "AzureSqlOutput"
	          }
	        ],
	        "typeProperties": {
	          "source": {
	            "type": "BlobSource",
	            "blobColumnSeparators": ","
	          },
	          "sink": {
	            "type": "SqlSink"
	          }
	        },
	       "scheduler": {
	          "frequency": "Hour",
	          "interval": 1
	        },
	        "policy": {
	          "concurrency": 1,
	          "executionPriorityOrder": "OldestFirst",
	          "retry": 0,
	          "timeout": "01:00:00"
	        }
	      }
	      ]
	   }
	}

## 샘플: Azure SQL에서 Azure Blob으로 데이터 복사
아래 샘플은 다음을 보여줍니다.

1.	[AzureSqlDatabase](data-factory-azure-sql-connector.md#azure-sql-linked-service-properties) 형식의 연결된 서비스입니다.
2.	[AzureStorage](#azure-storage-linked-service-properties) 형식의 연결된 서비스입니다.
3.	[AzureSqlTable](data-factory-azure-sql-connector.md#azure-sql-dataset-type-properties) 형식의 입력 [데이터 집합](data-factory-create-datasets.md)입니다.
4.	[AzureBlob](#azure-blob-dataset-type-properties) 형식의 출력 [데이터 집합](data-factory-create-datasets.md)입니다.
4.	[SqlSource](data-factory-azure-sql-connector.md#azure-sql-copy-activity-type-properties) 및 [BlobSink](#azure-blob-copy-activity-type-properties)를 사용하는 복사 작업의 [파이프라인](data-factory-create-pipelines.md)입니다.


샘플은 Azure SQL 데이터베이스의 테이블에서 blob로 매시간 시계열에 속한 데이터를 복사합니다. 이 샘플에 사용된 JSON 속성은 샘플 다음에 나오는 섹션에서 설명합니다.

**Azure SQL 연결된 서비스:**

	{
	  "name": "AzureSqlLinkedService",
	  "properties": {
	    "type": "AzureSqlDatabase",
	    "typeProperties": {
	      "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
	    }
	  }
	}

**Azure 저장소 연결된 서비스:**

	{
	  "name": "StorageLinkedService",
	  "properties": {
	    "type": "AzureStorage",
	    "typeProperties": {
	      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
	    }
	  }
	}

**Azure SQL 입력 데이터 집합:**

샘플은 Azure SQL에서 만든 테이블 "MyTable"에 시계열 데이터에 대한 "timestampcolumn" 라는 열이 포함되었다고 가정합니다.

"외부":"true" 설정 및 externalData 정책 지정은 Azure Data Factory 서비스가 테이블이 데이터 팩터리의 외부에 있으며 데이터 공장의 활동에 의해 생성되지 않는다는 점을 알립니다.

	{
	  "name": "AzureSqlInput",
	  "properties": {
	    "type": "AzureSqlTable",
	    "linkedServiceName": "AzureSqlLinkedService",
	    "typeProperties": {
	      "tableName": "MyTable"
	    },
	    "external": true,
	    "availability": {
	      "frequency": "Hour",
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

**Azure Blob 출력 데이터 집합:**

데이터는 매시간 새 blob에 기록됩니다.(빈도: 1시간, 간격:1회) Blob에 대한 폴더 경로는 처리 중인 조각의 시작 시간에 기반하여 동적으로 평가됩니다. 폴더 경로는 시작 시간에서 연도, 월, 일 및 시간 부분을 사용합니다.
	
	{
	  "name": "AzureBlobOutput",
	  "properties": {
	    "type": "AzureBlob",
	    "linkedServiceName": "StorageLinkedService",
	    "typeProperties": {
	      "folderPath": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}/",
	      "partitionedBy": [
	        {
	          "name": "Year",
	          "value": {
	            "type": "DateTime",
	            "date": "SliceStart",
	            "format": "yyyy"
	          }
	        },
	        {
	          "name": "Month",
	          "value": {
	            "type": "DateTime",
	            "date": "SliceStart",
	            "format": "%M"
	          }
	        },
	        {
	          "name": "Day",
	          "value": {
	            "type": "DateTime",
	            "date": "SliceStart",
	            "format": "%d"
	          }
	        },
	        {
	          "name": "Hour",
	          "value": {
	            "type": "DateTime",
	            "date": "SliceStart",
	            "format": "%H"
	          }
	        }
	      ],
	      "format": {
	        "type": "TextFormat",
	        "columnDelimiter": "\t",
	        "rowDelimiter": "\n"
	      }
	    },
	    "availability": {
	      "frequency": "Hour",
	      "interval": 1
	    }
	  }
	}

**복사 작업을 포함하는 파이프라인:**

파이프라인은 위의 입력 및 출력 데이터 집합을 사용하도록 구성된 복사 작업을 포함하고 매시간 실행하도록 예약됩니다. 파이프라인 JSON 정의에서 **원본** 형식은 **SqlSource**로 설정되고 **싱크** 형식은 **BlobSink**로 설정됩니다. **SqlReaderQuery** 속성에 지정된 SQL 쿼리는 과거 한 시간에서 복사할 데이터를 선택합니다.


	{  
	    "name":"SamplePipeline",
	    "properties":{  
	    	"start":"2014-06-01T18:00:00",
	    	"end":"2014-06-01T19:00:00",
	    	"description":"pipeline for copy activity",
	    	"activities":[  
	      		{
	        		"name": "AzureSQLtoBlob",
		    	    "description": "copy activity",
		    	    "type": "Copy",
		    	    "inputs": [
		    	      {
		    	        "name": "AzureSQLInput"
		    	      }
		    	    ],
		    	    "outputs": [
		    	      {
		    	        "name": "AzureBlobOutput"
		    	      }
		    	    ],
		    	    "typeProperties": {
		    	    	"source": {
		            		"type": "SqlSource",
			            	"SqlReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd HH:mm}\\' AND timestampcolumn < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
		          		},
		          		"sink": {
		            		"type": "BlobSink"
		          		}
		        	},
		       		"scheduler": {
		          		"frequency": "Hour",
		          		"interval": 1
		        	},
		        	"policy": {
		          		"concurrency": 1,
		          		"executionPriorityOrder": "OldestFirst",
		          		"retry": 0,
		          		"timeout": "01:00:00"
		        	}
		      	}
		     ]
		}
	}

## Azure 저장소 연결된 서비스 속성

Azure 저장소 연결된 서비스를 사용하여 Azure 저장소 계정을 Azure Data Factory에 연결할 수 있습니다. 다음 테이블은 Azure 저장소 연결된 서비스에 특정된 JSON 요소에 대한 설명을 제공합니다.

| 속성 | 설명 | 필수 |
| -------- | ----------- | -------- |
| type | 형식 속성은 **AzureStorage**로 설정되어야 합니다. | 예 |
| connectionString | connectionString 속성에 대한 Azure 저장소에 연결하는 데 필요한 정보를 지정합니다. Azure 클래식 포털에서 Azure 저장소에 대한 connectionString을 얻을 수 있습니다. | 예 |

## Azure Blob 데이터 집합 형식 속성

데이터 집합 정의에 사용할 수 있는 JSON 섹션 및 속성의 전체 목록은 [데이터 집합 만들기](data-factory-create-datasets.md) 문서를 참조하세요. 구조, 가용성 및 JSON 데이터 집합의 정책과 같은 섹션이 모든 데이터 집합 형식에 대해 유사합니다.(SQL Azure, Azure Blob, Azure 테이블 등)

**typeProperties** 섹션은 데이터 집합의 각 형식에 따라 다르며 데이터 저장소에 있는 데이터의 위치, 서식 등에 대한 정보를 제공합니다. **AzureBlob** 데이터 집합 형식의 데이터 집합에 대한 typeProperties 섹션에는 다음 속성이 있습니다.

| 속성 | 설명 | 필수 |
| -------- | ----------- | -------- | 
| folderPath | blob 저장소에서 컨테이너 및 폴더에 대한 경로입니다. 예제: myblobcontainer\\myblobfolder\\ | 예 |
| fileName | <p>Blob의 이름입니다. fileName은 선택 사항입니다. </p><p>filename을 지정하면 활동(복사 포함)이 특정 Blob에서 작동합니다.</p><p> fileName이 지정되지 않으면 복사는 입력 데이터 집합에 대한 folderPath의 모든 Blob를 포함합니다.</p><p>fileName이 출력 데이터 집합에 대해 지정되지 않으면 다음 이 서식에서 생성된 파일의 이름이 표시됩니다. 데이터.<Guid>.txt (예를 들어: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt</p> | 아니요 |
| partitionedBy | partitionedBy는 선택적 속성입니다. 동적 folderPath 및 시계열 데이터에 대한 filename을 지정하는 데 사용할 수 있습니다. 예를 들어 folderPath는 매시간 데이터에 대한 매개 변수화됩니다. 자세한 내용과 예제는 아래 [partitionedBy 속성 활용 섹션](#Leveraging-partitionedBy-property)을 참조하세요. | 아니요
| format | **TextFormat**, **AvroFormat**과 같은 두 서식 유형이 지원됩니다. 값이 있으면 이 중 하나로 서식에서 형식 속성을 설정해야 합니다. 서식이 TextFormat인 경우 형식에 선택적 추가 속성을 지정할 수 있습니다. 자세한 내용은 아래 [TextFormat 지정](#specifying-textformat) 섹션을 참조하세요. | 아니요
| 압축 | 데이터에 대한 압축 유형 및 수준을 지정합니다. 지원되는 형식은 GZip, Deflate 및 BZip2이고 지원되는 수준은 최적 및 가장 빠름입니다. 자세한 내용은 [압축 지원](#compression-support) 섹션을 참조하세요. | 아니요 |

### partitionedBy 속성 활용
위에서 설명한 것처럼 **partitionedBy** 섹션, 데이터 팩터리 매크로 및 시스템 변수(지정된 데이터 조각에 대한 시작 및 종료 시간을 나타내는 SliceStart 및 SliceEnd)를 사용하여 동적 folderPath 및 시계열 데이터에 대해 및 filename을 지정할 수 있습니다.

partitionedBy 섹션에서 사용할 수 있는 데이터 팩터리 시스템 변수 및 함수에 대해 자세히 알아보려면 [데이터 팩터리 시스템 변수](data-factory-scheduling-and-execution.md#data-factory-system-variables) 및 [데이터 팩터리 함수 참조](data-factory-scheduling-and-execution.md#data-factory-functions-reference)를 참조하세요.

시간 시계열 데이터 집합, 예약 및 조각에 대한 자세한 내용은 [데이터 집합 만들기](data-factory-create-datasets.md) 및 [일정 예약 및 실행](data-factory-scheduling-and-execution.md) 문서를 참조하세요.

#### 샘플 1

	"folderPath": "wikidatagateway/wikisampledataout/{Slice}",
	"partitionedBy": 
	[
	    { "name": "Slice", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyyMMddHH" } },
	],

위의 예제에서 {Slice}는 지정된 형식(YYYYMMDDHH)의 데이터 팩터리 시스템 변수 SliceStart 값으로 대체 됩니다. SliceStart는 조각의 시작 시간을 가리킵니다. folderPath는 각 조각에 따라 다릅니다. 예를 들어 wikidatagateway/wikisampledataout/2014100103 또는 wikidatagateway/wikisampledataout/2014100104입니다

#### 샘플 2

	"folderPath": "wikidatagateway/wikisampledataout/{Year}/{Month}/{Day}",
	"fileName": "{Hour}.csv",
	"partitionedBy": 
	 [
	    { "name": "Year", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyy" } },
	    { "name": "Month", "value": { "type": "DateTime", "date": "SliceStart", "format": "MM" } }, 
	    { "name": "Day", "value": { "type": "DateTime", "date": "SliceStart", "format": "dd" } }, 
	    { "name": "Hour", "value": { "type": "DateTime", "date": "SliceStart", "format": "hh" } } 
	],

위의 예제에서 SliceStart의 연도, 월, 일 및 시간은 folderPath 및 fileName 속성에서 사용하는 별도 변수로 추출됩니다.

### TextFormat 지정

형식이 **TextFormat**으로 설정된 경우 **Format** 섹션에서 다음과 같은 **선택적** 속성을 지정할 수 있습니다.

| 속성 | 설명 | 필수 |
| -------- | ----------- | -------- |
| columnDelimiter | 파일에서 문자는 열 구분 기호로 사용됩니다. 이 태그는 선택 사항입니다. 기본값은 쉼표(,)입니다. | 아니요 |
| rowDelimiter | 파일에서 문자는 원시 구분 기호로 사용됩니다. 이 태그는 선택 사항입니다. 기본값은 다음 중 하나입니다. ["\\r\\n", "\\r", "\\n"] | 아니요 |
| escapeChar | <p>열 구분 기호를 이스케이프하는 데 사용되는 특수 문자가 콘텐츠에 표시됩니다. 이 태그는 선택 사항입니다. 기본값은 없습니다. 이 속성에 한 개의 문자만을 지정해야 합니다.</p><p>예를 들어 열 구분 기호로 쉼표(,)가 있지만 텍스트에서 쉼표 문자를 쓰려는 경우(예: "Hello, world") '$'를 이스케이프 문자로 정의하고 원본에서 "$Hello, world" 문자열을 사용할 수 있습니다.</p><p>테이블에 escapeChar 및 quoteChar 모두를 지정할 수 없습니다.</p> | 아니요 | 
| quoteChar | <p>특수 문자는 문자열 값을 따옴표로 묶는 데 사용됩니다. 인용 문자 내의 열 및 행 구분 기호는 문자열 값의 일부로 간주됩니다. 이 태그는 선택 사항입니다. 기본값은 없습니다. 이 속성에 한 개의 문자만을 지정해야 합니다.</p><p>예를 들어 열 구분 기호로 쉼표(,)가 있지만 텍스트에서 쉼표 문자를 쓰려는 경우(예: <Hello  world>) ‘"’를 인용 문자로 정의하고 원본에서 <"Hello, world"> 문자열을 사용할 수 있습니다. 이 속성은 입력 및 출력 테이블 모두에 적용됩니다.</p><p>테이블에 escapeChar 및 quoteChar을 모두 지정할 수 없습니다.</p> | 아니요 |
| nullValue | <p>Blob 파일 콘텐츠에서 null 값을 나타내는 데 사용되는 문자입니다. 이 태그는 선택 사항입니다. 기본값은 "\\N"입니다.</p><p>예를 들어 위의 예제에 따라 blob의 "NaN"이 예를 들어 SQL Server에 복사되는 동안 null 값으로 변환됩니다.</p> | 아니요 |
| encodingName | 인코딩 이름을 지정합니다. 올바른 인코딩 이름 목록은 [Encoding.EncodingName Property](https://msdn.microsoft.com/library/system.text.encoding.aspx) 속성을 참조하세요. 예: windows-1250 또는 shift\_jis 기본값은 UTF-8입니다. | 아니요 | 

#### 샘플
다음 예제는 TextFormat에 대한 서식 속성 중 일부를 보여줍니다.

	"typeProperties":
	{
	    "folderPath": "mycontainer/myfolder",
	    "fileName": "myblobname"
	    "format":
	    {
	        "type": "TextFormat",
	        "columnDelimiter": ",",
	        "rowDelimiter": ";",
	        "quoteChar": """,
	        "NullValue": "NaN"
	    }
	},

quoteChar 대신 escapeChar를 사용하려면 quoteChar가 있는 해당 줄을 다음으로 바꿉니다.

	"escapeChar": "$",

### AvroFormat 지정
서식이 AvroFormat으로 설정된 경우 typeProperties 섹션 내의 서식 섹션에서 속성을 지정할 필요가 없습니다. 예제:

	"format":
	{
	    "type": "AvroFormat",
	}

Hive 테이블에서 Avro 형식을 사용하려는 경우 [Apache Hive의 자습서](https://cwiki.apache.org/confluence/display/Hive/AvroSerDe)를 참조하세요.

[AZURE.INCLUDE [data-factory-compression](../../includes/data-factory-compression.md)]


## Azure Blob 복사 활동 형식 속성  
활동 정의에 사용할 수 있는 섹션 및 속성의 전체 목록은 [파이프라인 만들기](data-factory-create-pipelines.md) 문서를 참조하세요. 이름, 설명, 입력 및 출력 테이블, 다양한 정책 등과 같은 속성은 모든 유형의 활동에 사용할 수 있습니다.

반면 작업의 typeProperties 섹션에서 사용할 수 있는 속성은 각 작업 형식에 따라 다르며 복사 작업의 경우 속성은 원본 및 싱크의 형식에 따라 다릅니다

**BlobSource**은 **typeProperties** 섹션에서 다음 속성을 지원합니다.

| 속성 | 설명 | 허용되는 값 | 필수 |
| -------- | ----------- | -------------- | -------- | 
| treatEmptyAsNull | Null 또는 빈 문자열을 null 값으로 처리할지 여부를 지정합니다. | TRUE<br/>FALSE | 아니요 |
| skipHeaderLineCount | 건너뛰어야 하는 줄 수를 나타냅니다. 입력 데이터 집합이 **TextFormat**을 사용하는 경우에만 해당합니다. | 0에서 Max. 사이의 정수입니다. | 아니요 | 
| recursive | 하위 폴더에서 또는 지정된 폴더에서만 데이터를 재귀적으로 읽을지 여부를 나타냅니다. | True(기본값), False | 아니요 | 


**BlobSink**는 **typeProperties** 섹션에서 다음 속성을 지원합니다.

| 속성 | 설명 | 허용되는 값 | 필수 |
| -------- | ----------- | -------------- | -------- |
| blobWriterAddHeader | 열 정의의 헤더를 추가할지를 지정합니다. | TRUE<br/>FALSE(기본값) | 아니요 |
| copyBehavior | 원본이 BlobSource 또는 FileSystem인 경우 복사 동작을 정의합니다. | <p>CopyBehavior 속성에 대한 세 가지 가능한 값이 있습니다. </p><ul><li>**PreserveHierarchy:** 대상 폴더의 파일 계층 구조를 유지합니다. 즉, 원본 폴더에 대한 원본 파일의 상대 경로가 대상 폴더에 대한 대상 파일의 상대 경로와 동일합니다.</li><li>**FlattenHierarchy:** 원본 폴더의 모든 파일이 대상 폴더의 첫 번째 수준이 됩니다. 대상 파일은 자동 생성된 이름을 갖습니다. </li><li>**MergeFiles:(기본값)** 원본 폴더의 모든 파일을 하나의 파일로 병합합니다. 파일/Blob 이름이 지정된 경우 지정된 이름이 병합된 파일 이름이 됩니다. 그렇지 않으면 자동 생성된 파일 이름이 병합된 파일 이름이 됩니다.</li></ul> | 아니요 |

### recursive 및 copyBehavior 예제
이 섹션에서는 다양한 recursive 및 copyBehavior 값 조합에 대한 복사 작업의 결과 동작을 설명합니다.

recursive | copyBehavior | 결과 동작
--------- | ------------ | --------
true | preserveHierarchy | <p>다음 구조를 가진 원본 폴더 Folder1의 경우:</p> <p>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5</p>대상 폴더 Folder1은 원본과 동일한 구조를 갖게 됩니다.<p>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5</p>.  
true | flattenHierarchy | <p>다음 구조를 가진 원본 폴더 Folder1의 경우:</p> <p>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5</p>대상 Folder1은 다음 구조를 갖게 됩니다. <p>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1에 대해 자동으로 생성된 이름<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2에 대해 자동으로 생성된 이름<br/>&nbsp;&nbsp;&nbsp;&nbsp;File3에 대해 자동으로 생성된 이름<br/>&nbsp;&nbsp;&nbsp;&nbsp;File4에 대해 자동으로 생성된 이름<br/>&nbsp;&nbsp;&nbsp;&nbsp;File5에 대해 자동으로 생성된 이름</p>
true | mergeFiles | <p>다음 구조를 가진 원본 폴더 Folder1의 경우:</p> <p>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5</p>대상 Folder1은 다음 구조를 갖게 됩니다. <p>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1 + File2 + File3 + File4 + File 5 콘텐츠는 자동으로 생성된 파일 이름을 가진 하나의 파일로 병합됩니다.</p>
false | preserveHierarchy | <p>다음 구조를 가진 원본 폴더 Folder1의 경우:</p> <p>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5</p>대상 폴더 Folder1은 다음 구조를 갖게 됩니다.<p>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/></p><p>File3, File4, File5를 가진 Subfolder1은 선택되지 않습니다.</p>.
false | flattenHierarchy | <p>다음 구조를 가진 원본 폴더 Folder1의 경우:</p> <p>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5</p>대상 폴더 Folder1은 다음 구조를 갖게 됩니다.<p>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1에 대해 자동으로 생성된 이름<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2에 대해 자동으로 생성된 이름<br/></p><p>File3, File4, File5를 가진 Subfolder1은 선택되지 않습니다.</p>.
false | mergeFiles | <p>다음 구조를 가진 원본 폴더 Folder1의 경우:</p> <p>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5</p>대상 폴더 Folder1은 다음 구조를 갖게 됩니다.<p>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1 + File2 콘텐츠는 자동으로 생성된 파일 이름을 가진 하나의 파일로 병합됩니다. File1에 대해 자동으로 생성된 이름</p><p>File3, File4, File5를 가진 Subfolder1은 선택되지 않습니다.</p>.

  


[AZURE.INCLUDE [data-factory-structure-for-rectangualr-datasets](../../includes/data-factory-structure-for-rectangualr-datasets.md)]

[AZURE.INCLUDE [data-factory-type-conversion-sample](../../includes/data-factory-type-conversion-sample.md)]

[AZURE.INCLUDE [data-factory-column-mapping](../../includes/data-factory-column-mapping.md)]

<!---HONumber=AcomDC_1203_2015-->