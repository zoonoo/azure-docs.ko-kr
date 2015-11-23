<properties 
	pageTitle="파일 시스템 간 데이터 이동 | Azure Data Factory" 
	description="Azure Data Factory를 사용하여 온-프레미스 파일 시스템 간 데이터를 이동하는 방법에 대해 알아봅니다." 
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
	ms.date="11/09/2015" 
	ms.author="spelluru"/>

# Azure Data Factory를 사용하여 온-프레미스 파일 시스템 간 데이터 이동

이 문서는 데이터 팩터리 복사 작업을 사용하여 온-프레미스 파일 시스템에서 데이터를 이동하는 방법을 설명합니다. 이 문서는 복사 작업 및 지원되는 데이터 저장소 조합을 사용하여 데이터 이동의 일반적인 개요를 보여주는 [데이터 이동 활동](data-factory-data-movement-activities.md) 문서를 작성합니다.

데이터 팩터리는 데이터 관리 게이트웨이를 통해 온-프레미스 파일 시스템에서 연결을 지원합니다. 데이터 관리 게이트웨이 및 게이트웨이 설정에 대한 단계별 지침을 알아보려면 [온-프레미스 위치 및 클라우드 간 데이터 이동](data-factory-move-data-between-onprem-and-cloud.md) 문서를 참조하세요.

**참고:** 데이터 관리 게이트웨이와 달리 온-프레미스 파일 시스템에서 통신하는 데 다른 이진 파일을 설치할 필요가 없습니다.

## Linux 파일 공유 

다음 두 단계를 수행하여 서비스에 연결된 파일 서버와 Linux 파일 공유를 사용합니다.

- Linux 서버에 [Samba](https://www.samba.org/)를 설치합니다.
- Windows 서버에 데이터 관리 게이트웨이를 설치하고 구성합니다. Linux 서버에 게이트웨이 설치가 지원되지 않습니다. 
 
## 샘플: 온-프레미스 파일 시스템에서 Azure Blob으로 데이터 복사

아래 샘플은 다음을 보여줍니다.

1.	[OnPremisesFileServer](data-factory-onprem-file-system-connector.md#onpremisesfileserver-linked-service-properties) 형식의 연결된 서비스
2.	[AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service-properties) 형식의 연결된 서비스
3.	[FileShare](data-factory-onprem-file-system-connector.md#on-premises-file-system-dataset-type-properties) 형식의 입력 [데이터 집합](data-factory-create-datasets.md)
4.	[AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties) 형식의 출력 [데이터 집합](data-factory-create-datasets.md)
4.	[FileSystemSource](data-factory-onprem-file-system-connector.md#file-share-copy-activity-type-properties) 및 [BlobSink](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties)를 사용하는 복사 작업의 [파이프라인](data-factory-create-pipelines.md) 

아래 샘플은 온-프레미스 파일 시스템에서 Azure blob로 매시간 시계열에 속한 데이터를 복사합니다. 이 샘플에 사용된 JSON 속성은 샘플 다음에 나오는 섹션에서 설명합니다.

첫 번째 단계로 [온-프레미스 위치 및 클라우드 간 데이터 이동](data-factory-move-data-between-onprem-and-cloud.md) 문서의 수행 설치 지침에 따라 데이터 관리 게이트웨이를 설정합니다.

**온-프레미스 파일 서버 연결된 서비스:**

	{
	  "Name": "OnPremisesFileServerLinkedService",
	  "properties": {
	    "type": "OnPremisesFileServer",
	    "typeProperties": {
	      "host": "\\Contosogame-Asia",
	      "userid": "Admin",
	      "password": "123456",
	      "gatewayName": "mygateway"
	    }
	  }
	}

**Azure Blob 저장소 연결된 서비스:**

	{
	  "name": "StorageLinkedService",
	  "properties": {
	    "type": "AzureStorage",
	    "typeProperties": {
	      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
	    }
	  }
	}

**온-프레미스 파일 시스템 입력 데이터 집합**

데이터는 시간 세분성으로 특정 날짜와 시간을 반영하는 경로 및 filename이 있는 새 파일에서 매시간 선택됩니다.

"외부":"true" 설정 및 externalData 정책 지정은 Azure Data Factory 서비스가 테이블이 데이터 팩터리의 외부에 있으며 데이터 공장의 활동에 의해 생성되지 않는다는 점을 알립니다.

	{
	  "name": "OnpremisesFileSystemInput",
	  "properties": {
	    "type": " FileShare",
	    "linkedServiceName": " OnPremisesFileServerLinkedService ",
	    "typeProperties": {
	      "folderPath": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}",
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
	      ]
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
	      "folderPath": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
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
	            "format": "%HH"
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

**복사 작업:**

파이프라인은 위의 입력 및 출력 데이터 집합을 사용하도록 구성된 복사 작업을 포함하고 매시간 실행하도록 예약됩니다. 파이프라인 JSON 정의에서 **소스** 형식은 **FileSystemSource**로 설정되고 **싱크** 형식은 **BlobSink**로 설정됩니다.
	
	{  
	    "name":"SamplePipeline",
	    "properties":{  
	    "start":"2015-06-01T18:00:00",
	    "end":"2015-06-01T19:00:00",
	    "description":"Pipeline for copy activity",
	    "activities":[  
	      {
	        "name": "OnpremisesFileSystemtoBlob",
	        "description": "copy activity",
	        "type": "Copy",
	        "inputs": [
	          {
	            "name": "OnpremisesFileSystemInput"
	          }
	        ],
	        "outputs": [
	          {
	            "name": "AzureBlobOutput"
	          }
	        ],
	        "typeProperties": {
	          "source": {
	            "type": "FileSystemSource"
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

##샘플: Azure SQL에서 온-프레미스 파일 시스템으로 데이터 복사 

아래 샘플은 다음을 보여줍니다.

1.	AzureSqlDatabase 형식의 연결된 서비스입니다.
2.	OnPremisesFileServer 형식의 연결된 서비스입니다.
3.	AzureSqlTable 형식의 입력 데이터 집합입니다. 
3.	FileShare 형식의 출력 데이터 집합입니다.
4.	SqlSource 및 FileSystemSink를 사용하는 복사 작업의 파이프라인입니다.

샘플은 Azure SQL 데이터베이스의 테이블에서 온-프레미스 파일 시스템으로 매시간 시계열에 속한 데이터를 복사합니다. 이 샘플에 사용된 JSON 속성은 샘플 다음에 나오는 섹션에서 설명합니다.

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

**온-프레미스 파일 서버 연결된 서비스:**

	{
	  "Name": "OnPremisesFileServerLinkedService",
	  "properties": {
	    "type": "OnPremisesFileServer",
	    "typeProperties": {
	      "host": "\\Contosogame-Asia",
	      "userid": "Admin",
	      "password": "123456",
	      "gatewayName": "mygateway"
	    }
	  }
	}

**Azure SQL 입력 데이터 집합:**

샘플은 Azure SQL에서 만든 테이블 "MyTable"에 시계열 데이터에 대한 "timestampcolumn" 라는 열이 포함되었다고 가정합니다.

"외부":"true" 설정 및 externalData 정책 지정은 데이터 팩터리 서비스가 테이블이 데이터 팩터리의 외부에 있으며 데이터 공장의 활동에 의해 생성되지 않는다는 점을 알립니다.

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

**온-프레미스 파일 시스템 출력 데이터 집합:**

데이터는 시간 세분성으로 특정 날짜와 시간을 반영하는 blob에 대한 경로가 있는 새 파일로 매시간 복사됩니다.

	{
	  "name": "OnpremisesFileSystemOutput",
	  "properties": {
	    "type": "FileShare",
	    "linkedServiceName": " OnPremisesFileServerLinkedService ",
	    "typeProperties": {
	      "folderPath": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}",
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
	            "format": "%HH"
	          }
	        }
	      ]
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

**복사 작업이 있는 파이프라인:** 파이프라인에는 위의 입력 및 출력 데이터 집합을 사용하도록 구성된 복사 활동을 포함하고 1시간마다 실행하도록 예약됩니다. 파이프라인 JSON 정의에서 **소스** 형식은 **SqlSource**로 설정되고 **싱크** 형식은 **FileSystemSink**로 설정됩니다. **SqlReaderQuery** 속성에 지정된 SQL 쿼리는 과거 한 시간에서 복사할 데이터를 선택합니다.

	
	{  
	    "name":"SamplePipeline",
	    "properties":{  
	    "start":"2015-06-01T18:00:00",
	    "end":"2015-06-01T20:00:00",
	    "description":"pipeline for copy activity",
	    "activities":[  
	      {
	        "name": "AzureSQLtoOnPremisesFile",
	        "description": "copy activity",
	        "type": "Copy",
	        "inputs": [
	          {
	            "name": "AzureSQLInput"
	          }
	        ],
	        "outputs": [
	          {
	            "name": "OnpremisesFileSystemOutput"
	          }
	        ],
	        "typeProperties": {
	          "source": {
	            "type": "SqlSource",
	            "SqlReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd}\\' AND timestampcolumn < \\'{1:yyyy-MM-dd}\\'', WindowStart, WindowEnd)"
	          },
	          "sink": {
	            "type": "FileSystemSink"
	          }
	        },
	       "scheduler": {
	          "frequency": "Hour",
	          "interval": 1
	        },
	        "policy": {
	          "concurrency": 1,
	          "executionPriorityOrder": "OldestFirst",
	          "retry": 3,
	          "timeout": "01:00:00"
	        }
	      }
	     ]
	   }
	}

## 연결된 OnPremisesFileServer 서비스 속성

온-프레미스 파일 서버 연결된 서비스로 Azure Data Factory에 온-프레미스 파일 시스템을 연결할 수 있습니다. 다음 테이블은 온-프레미스 파일 서버 연결된 서비스에 특정된 JSON 요소에 대한 설명을 제공합니다.

속성 | 설명 | 필수
-------- | ----------- | --------
type | 형식 속성은 **OnPremisesFileServer**로 설정해야 합니다. | 예 
host | 서버의 호스트 이름입니다. '\\'를 다음 예제와 같이 이스케이프 문자로 사용합니다. 공유가 :\\servername인 경우 \\servername를 지정합니다.<p>파일 시스템이 게이트웨이 컴퓨터에 로컬인 경우 로컬 또는 localhost를 사용합니다. 파일 시스템이 게이트웨이 컴퓨터와 다른 서버에 있는 경우 \\servername를 사용합니다.</p> | 예
userid | 서버에 액세스 권한이 있는 사용자의 ID 지정 | 아니요(encryptedCredential을 선택하는 경우)
password | 사용자에 암호 지정(userid) | 아니요(encryptedcredential을 선택하는 경우) 
encryptedCredential | New-AzureDataFactoryEncryptValue cmdlet을 실행하여 얻을 수 있는 암호화된 자격 증명을 지정합니다<p>**참고:** 버전 0.8.14 이상의 Azure PowerShell을 사용하여 OnPremisesFileSystemLinkedService로 설정된 형식 매개 변수로 New-AzureDataFactoryEncryptValue와 같은 cmdlet을 사용해야 합니다</p> | 아니요(일반 텍스트에 userid 및 암호를 지정하는 경우)
gatewayName | 데이터 팩터리 서비스가 온-프레미스 파일 서버에 연결하는 데 사용해야 하는 게이트웨이의 이름 | 예

온-프레미스 파일 시스템 데이터 원본의 자격 증명 설정에 대한 자세한 내용은 [자격 증명 및 보안 설정](data-factory-move-data-between-onprem-and-cloud.md#setting-credentials-and-security)을 참조하세요.

**예제: 일반 텍스트에 사용자 이름 및 암호 사용**
	
	{
	  "Name": "OnPremisesFileServerLinkedService",
	  "properties": {
	    "type": "OnPremisesFileServer",
	    "typeProperties": {
	      "host": "\\Contosogame-Asia",
	      "userid": "Admin",
	      "password": "123456",
	      "gatewayName": "mygateway"
	    }
	  }
	}
	
**예제: encryptedcredential 사용**

	{
	  "Name": " OnPremisesFileServerLinkedService ",
	  "properties": {
	    "type": "OnPremisesFileServer",
	    "typeProperties": {
	      "host": "localhost",
	      "encryptedCredential": "WFuIGlzIGRpc3Rpbmd1aXNoZWQsIG5vdCBvbmx5IGJ5xxxxxxxxxxxxxxxxx",
	      "gatewayName": "mygateway"
	    }
	  }
	}

## 온-프레미스 파일 시스템 데이터 집합 형식 속성

데이터 집합 정의에 사용할 수 있는 섹션 및 속성의 전체 목록은 [데이터 집합 만들기](data-factory-create-datasets.md) 문서를 참조하세요. 구조, 가용성 및 JSON 데이터 집합의 정책과 같은 섹션이 모든 데이터 집합 형식에 대해 유사합니다.(SQL Azure, Azure Blob, Azure 테이블, 온-프레미스 파일 시스템 등)

typeProperties 섹션은 데이터 집합의 각 형식에 따라 다르며 데이터 저장소에 있는 데이터의 위치, 서식 등에 대한 정보를 제공합니다. **FileShare** 데이터 집합 형식의 데이터 집합에 대한 typeProperties 섹션에는 다음 속성이 있습니다.

속성 | 설명 | 필수
-------- | ----------- | --------
folderPath | 파일의 경로입니다. 예제: myfolder<p>문자열의 특수 문자에 이스케이프 문자 '\\' 사용. 예를 들어 folder\\subfolder, folder\\subfolder 및 d:\\samplefolder에 d:\\samplefolder를 지정합니다.</p><p>**partitionBy**로 이를 결합하여 조각 시작/종료 날짜-시간에 따라 폴더 경로를 가질 수 있습니다.</p> | 예
fileName | 폴더에서 특정 파일을 참조하기 위해 테이블을 사용하려는 경우 **folderPath**에 있는 파일의 이름을 지정합니다. 이 속성에 값을 지정하지 않으면 테이블은 폴더에 있는 모든 파일을 가리킵니다.<p>출력 데이터 집합에 fileName을 지정하지 않으면 생성된 파일의 이름은 다음 이 서식에 있습니다.</p><p>Data.<Guid>.txt(예를 들어 Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt</p> | 아니요
partitionedBy | 동적 folderPath, 시계열 데이터에 대 한 filename을 지정 하려면 partitionedBy는 활용할 수 있습니다. 예를 들어 매시간 데이터에 대한 매개 변수가 있는 folderPath입니다. | 아니요
형식 | **TextFormat**, **AvroFormat**와 같은 두 서식 유형이 지원됩니다. 값이 있으면 이 중 하나로 서식에서 형식 속성을 설정해야 합니다. forAvroFormatmat이 TextFormat인 경우 형식에 선택적 추가 속성을 지정할 수 있습니다. 자세한 내용은 아래 형식 섹션을 참조하세요. | 아니요
fileFilter | 모든 파일이 아닌 folderPath의 파일 하위 집합을 선택하는데 사용할 필터를 지정합니다. <p>허용되는 값은 다음과 같습니다. * (여러 문자) 및 ? (한 개의 문자).</p><p>예제 1: "fileFilter": "*.log"</p>예제 2: "fileFilter":2014-1-?.txt"</p><p>**참고**: fileFilter는 입력 FileShare 데이터 집합에 적용됩니다.</p> | 아니요
| 압축 | 데이터에 대한 압축 유형 및 수준을 지정합니다. 지원되는 형식은 GZip, Deflate 및 BZip2이고 지원되는 수준은 최적 및 가장 빠름입니다. 자세한 내용은 [압축 지원](#compression-support) 섹션을 참조하세요. | 아니요 |

> [AZURE.NOTE]filename 및 fileFilter는 동시에 사용할 수 없습니다.

### partionedBy 속성 활용

위에서 설명했듯이 동적 folderPath, partitionedBy된 시계열 데이터에 대한 filename을 지정할 수 있습니다. 지정된 데이터 조각에 대한 논리적 기간을 나타내는 데이터 팩터리 매크로 및 시스템 변수 SliceStart, SliceEnd를 사용하여 이 작업을 수행할 수 있습니다.

시간 시계열 데이터 집합, 예약 및 조각에 대한 자세한 내용을 이해하려면 [데이터 집합 만들기](data-factory-create-datasets.md), [일정 예약 및 실행](data-factory-scheduling-and-execution.md) 및 [파이프라인 만들기](data-factory-create-pipelines.md) 문서를 참조하세요.

#### 샘플 1:

	"folderPath": "wikidatagateway/wikisampledataout/{Slice}",
	"partitionedBy": 
	[
	    { "name": "Slice", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyyMMddHH" } },
	],

위의 예제에서 {Slice}는 지정된 형식(YYYYMMDDHH)의 데이터 팩터리 시스템 변수 SliceStart 값으로 대체 됩니다. SliceStart는 조각의 시작 시간을 가리킵니다. folderPath는 각 조각에 따라 다릅니다. 예를 들어 wikidatagateway/wikisampledataout/2014100103 또는 wikidatagateway/wikisampledataout/2014100104입니다.

#### 샘플 2:

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

형식이 **TextFormat**으로 설정된 경우 **typeProperties** 섹션 내의 **Format** 섹션에서 다음과 같은 **선택적** 속성을 지정할 수 있습니다.

속성 | 설명 | 필수
-------- | ----------- | --------
columnDelimiter | 파일에서 문자는 열 구분 기호로 사용됩니다. 기본값은 쉼표(,)입니다. | 아니요
rowDelimiter | 파일에서 문자는 원시 구분 기호로 사용됩니다. 기본값은 다음 중 하나입니다. ["\\r\\n", "\\r", "\\n"] | 아니요
escapeChar | 열 구분 기호를 이스케이프 하는데 사용되는 특수 문자가 내용에 표시됩니다. 기본값은 없습니다. 이 속성에 한 개의 문자만을 지정해야 합니다.<p>예를 들어 열 구분 기호로 쉼표(,)가 있지만 텍스트에서 쉼표 문자를 쓰려는 경우(예: "Hello, world") '$'를 이스케이프 문자로 정의하고 원본에서 "$Hello, world" 문자열을 사용할 수 있습니다.</p><p>테이블에 escapeChar 및 quoteChar 모두를 지정할 수 없습니다.</p> | 아니요
quoteChar | 특수 문자는 문자열 값을 따옴표로 묶는 데 사용됩니다. 인용 문자 내의 열 및 행 구분 기호는 문자열 값의 일부로 간주됩니다. 기본값은 없습니다. 이 속성에 한 개의 문자만을 지정해야 합니다.<p>예를 들어 열 구분 기호로 쉼표(,)가 있지만 텍스트에서 쉼표 문자를 쓰려는 경우(예: <Hello  world>) ‘"’를 인용 문자로 정의하고 원본에서 <"Hello, world"> 문자열을 사용할 수 있습니다. 이 속성은 입력 및 출력 테이블 모두에 적용됩니다.</p><p>테이블에 escapeChar 및 quoteChar을 모두 지정할 수 없습니다.</p> | 아니요
nullValue | Blob 파일 내용에서 null 값을 나타내는 데 사용되는 문자입니다. 기본값은 “\\N”입니다.> | 아니요
encodingName | 인코딩 이름을 지정합니다. 올바른 인코딩 이름 목록은 Encoding.EncodingName 속성을 참조하세요. <p>예: windows-1250 또는 shift\_jis. 기본값은 UTF-8입니다.</p> | 아니요

#### 샘플:

다음 예제는 **TextFormat**에 대한 형식 속성 중 일부를 보여 줍니다.

	"typeProperties":
	{
	    "folderPath": "MyFolder",
	    "fileName": "MyFileName"
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

형식이 **AvroFormat**으로 설정된 경우 typeProperties 섹션 내의 Format 섹션에서 속성을 지정할 필요가 없습니다. 예제:

	"format":
	{
	    "type": "AvroFormat",
	}
	
후속 Hive 테이블에서 Avro 형식을 사용하려면 [Apache Hive 자습서](https://cwiki.apache.org/confluence/display/Hive/AvroSerDe)를 참조하세요.

[AZURE.INCLUDE [data-factory-compression](../../includes/data-factory-compression.md)]

## 파일 공유 복사 활동 형식 속성

**FileSystemSource**는 다음 속성을 지원합니다.

| 속성 | 설명 | 허용되는 값 | 필수 |
| -------- | ----------- | -------------- | -------- |
| recursive | 하위 폴더에서 또는 지정된 폴더에서만 데이터를 재귀적으로 읽을지 여부를 나타냅니다. | True, False(기본값)| 아니요 | 

**FileSystemSink**는 다음 속성을 지원합니다.

| 속성 | 설명 | 허용되는 값 | 필수 |
| -------- | ----------- | -------------- | -------- |
| copyBehavior | 원본이 BlobSource 또는 FileSystem인 경우 복사 동작을 정의합니다. | <p>CopyBehavior 속성에 대한 세 가지 가능한 값이 있습니다. </p><ul><li>**PreserveHierarchy:** 대상 폴더의 파일 계층 구조를 유지합니다. 즉, 원본 폴더에 대한 원본 파일의 상대 경로가 대상 폴더에 대한 대상 파일의 상대 경로와 동일합니다.</li><li>**FlattenHierarchy:** 원본 폴더의 모든 파일이 대상 폴더의 첫 번째 수준이 됩니다. 대상 파일은 자동 생성된 이름을 갖습니다. </li><li>**MergeFiles:** 원본 폴더의 모든 파일을 하나의 파일로 병합합니다. 파일/Blob 이름이 지정된 경우 지정된 이름이 병합된 파일 이름이 됩니다. 그렇지 않으면 자동 생성된 파일 이름이 병합된 파일 이름이 됩니다.</li></ul> | 아니요 |

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

[AZURE.INCLUDE [data-factory-column-mapping](../../includes/data-factory-column-mapping.md)]








 

<!---HONumber=Nov15_HO3-->