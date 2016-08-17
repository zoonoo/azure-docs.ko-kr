<properties
	pageTitle="SQL Server 간 데이터 이동 | Azure 데이터 팩터리"
	description="Azure 데이터 팩터리를 사용하여 온-프레미스 또는 Azure VM에 있는 SQL Server 데이터베이스 간에 데이터를 이동하는 방법에 대해 알아봅니다."
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
	ms.date="06/15/2016"
	ms.author="spelluru"/>

# Azure 데이터 팩터리를 사용하여 온-프레미스 또는 IaaS(Azure VM) SQL Server 간 데이터 이동

이 문서에서는 Azure 데이터 팩토리에서 복사 작업을 사용하여 다른 데이터 저장소와 SQL Server 간에 데이터를 이동하는 방법을 간략하게 설명합니다. 이 문서는 복사 작업 및 지원되는 데이터 저장소 조합을 사용하여 데이터 이동의 일반적인 개요를 보여주는 [데이터 이동 활동](data-factory-data-movement-activities.md) 문서를 작성합니다.

## 연결 사용

SQL Server가 호스팅되는 온-프레미스 또는 Azure IaaS(Infrastructure-as-a-Service) VM에 연결하는 데 필요한 개념과 단계는 같습니다. 두 경우 모두 연결에 데이터 관리 게이트웨이를 활용해야 합니다.

데이터 관리 게이트웨이 및 게이트웨이 설정에 대한 단계별 지침을 알아보려면 [온-프레미스 위치 및 클라우드 간 데이터 이동](data-factory-move-data-between-onprem-and-cloud.md) 문서를 참조하세요. SQL Server에 연결하기 위해서는 게이트웨이 인스턴스를 설정해야 합니다.

성능 향상을 위해 게이트웨이를 동일한 온-프레미스 컴퓨터 또는 클라우드 VM 인스턴스에 SQL Server로 설치할 수 있지만, 리소스 충돌을 방지하기 위해 별도의 컴퓨터 또는 클라우드 VM에 설치하는 것이 좋습니다.

다음 샘플은 SQL Server 및 Azure Blob 저장소 간에 데이터를 복사하는 방법을 보여 줍니다. 그러나 Azure 데이터 팩터리의 복사 작업을 사용하여 임의의 원본에서 [여기](data-factory-data-movement-activities.md#supported-data-stores)에 설명한 싱크로 **직접** 데이터를 복사할 수 있습니다.

## 샘플: SQL Server에서 Azure Blob로 데이터 복사

아래 샘플은 다음을 보여줍니다.

1.	[OnPremisesSqlServer](data-factory-sqlserver-connector.md#sql-server-linked-service-properties) 형식의 연결된 서비스
2.	[AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service-properties) 형식의 연결된 서비스
3.	[SqlServerTable](data-factory-sqlserver-connector.md#sql-server-dataset-type-properties) 형식의 입력 [데이터 집합](data-factory-create-datasets.md)
4.	[AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties) 형식의 출력 [데이터 집합](data-factory-create-datasets.md)
4.	[SqlSource](data-factory-sqlserver-connector.md#sql-server-copy-activity-type-properties) 및 [BlobSink](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties)를 사용하는 복사 작업의 [파이프라인](data-factory-create-pipelines.md)

샘플은 SQL Server 데이터베이스의 테이블에서 blob로 매시간 시계열에 속한 데이터를 복사합니다. 이 샘플에 사용된 JSON 속성은 샘플 다음에 나오는 섹션에서 설명합니다.

첫 번째 단계로 [온-프레미스 위치 및 클라우드 간 데이터 이동](data-factory-move-data-between-onprem-and-cloud.md) 문서의 지침에 따라 데이터 관리 게이트웨이를 설정합니다.

**SQL Server 연결된 서비스**

	{
	  "Name": "SqlServerLinkedService",
	  "properties": {
	    "type": "OnPremisesSqlServer",
	    "typeProperties": {
	      "connectionString": "Data Source=<servername>;Initial Catalog=<databasename>;Integrated Security=False;User ID=<username>;Password=<password>;",
	      "gatewayName": "<gatewayname>"
	    }
	  }
	}

**Azure Blob 저장소 연결된 서비스**

	{
	  "name": "StorageLinkedService",
	  "properties": {
	    "type": "AzureStorage",
	    "typeProperties": {
	      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
	    }
	  }
	}

**SQL Server 입력 데이터 집합**

샘플은 Azure SQL에서 만든 "MyTable" 테이블에 시계열 데이터에 대한 "timestampcolumn"이라는 열이 포함되어 있다고 가정합니다. 단일 데이터 집합을 사용하여 동일한 데이터베이스 내 여러 테이블에 대해 쿼리를 실행할 수 있지만, 데이터 집합의 tableName typeProperty에 대해서는 단일 테이블이 사용되어야 합니다.

"external": "true"를 설정하고 externalData 정책을 지정함으로써 Azure Data Factory 서비스에 테이블이 데이터 팩터리의 외부에 있으며 데이터 팩터리의 작업에 의해 생성되지 않는다는 점을 알려줍니다.

	{
	  "name": "SqlServerInput",
	  "properties": {
	    "type": "SqlServerTable",
	    "linkedServiceName": "SqlServerLinkedService",
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

**Azure Blob 출력 데이터 집합**

데이터는 매시간 새 blob에 기록됩니다(frequency: hour, interval: 1). Blob에 대한 폴더 경로는 처리 중인 조각의 시작 시간에 기반하여 동적으로 평가됩니다. 폴더 경로는 시작 시간에서 연도, 월, 일 및 시간 부분을 사용합니다.

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
	            "format": "MM"
	          }
	        },
	        {
	          "name": "Day",
	          "value": {
	            "type": "DateTime",
	            "date": "SliceStart",
	            "format": "dd"
	          }
	        },
	        {
	          "name": "Hour",
	          "value": {
	            "type": "DateTime",
	            "date": "SliceStart",
	            "format": "HH"
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

**복사 작업을 포함하는 파이프라인**

파이프라인은 위의 입력 및 출력 데이터 집합을 사용하도록 구성된 복사 작업을 포함하고 매시간 실행하도록 예약됩니다. 파이프라인 JSON 정의에서 **source** 형식은 **SqlSource**으로 설정되고 **sink** 형식은 **BlobSink**로 설정됩니다. **SqlReaderQuery** 속성에 지정된 SQL 쿼리는 과거 한 시간에서 복사할 데이터를 선택합니다.


	{  
	    "name":"SamplePipeline",
	    "properties":{  
	    "start":"2014-06-01T18:00:00",
	    "end":"2014-06-01T19:00:00",
	    "description":"pipeline for copy activity",
	    "activities":[  
	      {
	        "name": "SqlServertoBlob",
	        "description": "copy activity",
	        "type": "Copy",
	        "inputs": [
	          {
	            "name": " SqlServerInput"
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


위의 예에서 **sqlReaderQuery**는 SqlSource에 지정됩니다. 복사 작업은 데이터를 가져오는 SQL Server 데이터베이스 원본에 대해 이 쿼리를 실행합니다. 또는 **sqlReaderStoredProcedureName** 및 **storedProcedureParameters**를 지정하여 저장 프로시저를 지정할 수 있습니다(저장 프로시저가 매개 변수를 사용하는 경우). sqlReaderQuery는 입력 데이터 집합에 의해 참조되는 데이터베이스 내의 여러 테이블을 참조할 수 있습니다. 데이터 집합의 tableName typeProperty로 설정된 테이블 집합으로만 제한되지 않습니다.


sqlReaderQuery 또는 sqlReaderStoredProcedureName 중 하나를 지정하지 않으면 JSON 데이터 집합의 구조 섹션에 정의된 열이 SQL Server 데이터베이스에 대해 실행되는 쿼리를 작성하는 데 사용됩니다(mytable에서 column1, column2 선택). 데이터 집합 정의에 구조가 없는 경우 모든 열은 테이블에서 선택됩니다.


SqlSource 및 BlobSink에서 지원하는 속성 목록은 [Sql 원본](#sqlsource) 섹션 및 [BlobSink](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties)를 참조하세요.

## 샘플: Azure Blob에서 SQL Server로 데이터 복사

아래 샘플은 다음을 보여줍니다.

1.	[OnPremisesSqlServer](data-factory-sqlserver-connector.md#sql-server-linked-service-properties) 형식의 연결된 서비스
2.	[AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service-properties) 형식의 연결된 서비스
3.	[AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties) 형식의 입력 [데이터 집합](data-factory-create-datasets.md)
4.	[SqlServerTable](data-factory-sqlserver-connector.md#sql-server-dataset-type-properties) 형식의 출력 [데이터 집합](data-factory-create-datasets.md)
4.	[BlobSource](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties) 및 [SqlSink](data-factory-sqlserver-connector.md#sql-server-copy-activity-type-properties)를 사용하는 복사 작업의 [파이프라인](data-factory-create-pipelines.md)

샘플은 Azure blob에서 SQL Server 데이터베이스의 테이블로 매시간 시계열에 속한 데이터를 복사합니다. 이 샘플에 사용된 JSON 속성은 샘플 다음에 나오는 섹션에서 설명합니다.

**SQL Server 연결된 서비스**

	{
	  "Name": "SqlServerLinkedService",
	  "properties": {
	    "type": "OnPremisesSqlServer",
	    "typeProperties": {
	      "connectionString": "Data Source=<servername>;Initial Catalog=<databasename>;Integrated Security=False;User ID=<username>;Password=<password>;",
	      "gatewayName": "<gatewayname>"
	    }
	  }
	}

**Azure Blob 저장소 연결된 서비스**

	{
	  "name": "StorageLinkedService",
	  "properties": {
	    "type": "AzureStorage",
	    "typeProperties": {
	      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
	    }
	  }
	}

**Azure Blob 입력 데이터 집합**

데이터는 매시간 새 blob에 선택됩니다(frequency: hour, interval: 1). Blob에 대한 폴더 경로 및 파일 이름은 처리 중인 조각의 시작 시간에 기반하여 동적으로 평가됩니다. 폴더 경로는 연도, 월 및 일 일부 시작 시간을 사용하고 파일 이름은 시작 시간의 시간 부분을 사용합니다. "external": "true" 설정은 데이터 팩터리 서비스에 이 테이블이 데이터 팩터리의 외부에 있으며 데이터 팩터리의 작업에 의해 생성되지 않는다는 점을 알려줍니다.

	{
	  "name": "AzureBlobInput",
	  "properties": {
	    "type": "AzureBlob",
	    "linkedServiceName": "StorageLinkedService",
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
	            "format": "MM"
	          }
	        },
	        {
	          "name": "Day",
	          "value": {
	            "type": "DateTime",
	            "date": "SliceStart",
	            "format": "dd"
	          }
	        },
	        {
	          "name": "Hour",
	          "value": {
	            "type": "DateTime",
	            "date": "SliceStart",
	            "format": "HH"
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

**SQL Server 출력 데이터 집합**

샘플은 SQL Server의 "MyTable"이라는 테이블에 데이터를 복사합니다. Blob CSV 파일에 포함되는 것과 같은 수의 열을 사용하여 SQL Server에 테이블을 만들어야 합니다. 새 행은 매시간 테이블에 추가됩니다.

	{
	  "name": "SqlServerOutput",
	  "properties": {
	    "type": "SqlServerTable",
	    "linkedServiceName": "SqlServerLinkedService",
	    "typeProperties": {
	      "tableName": "MyOutputTable"
	    },
	    "availability": {
	      "frequency": "Hour",
	      "interval": 1
	    }
	  }
	}

**복사 작업을 포함하는 파이프라인**

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
	            "name": " SqlServerOutput "
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

## SQL Server 연결된 서비스 속성

다음 표에서는 SQL Server 연결된 서비스와 관련된 JSON 요소에 대한 설명을 제공합니다.

| 속성 | 설명 | 필수 |
| -------- | ----------- | -------- |
| type | type 속성은 **OnPremisesSqlServer**로 설정해야 합니다. | 예 |
| connectionString | SQL 인증 또는 Windows 인증을 사용하여 온-프레미스 SQL Server 데이터베이스에 연결하는 데 필요한 connectionString 정보를 지정합니다. | 예 |
| gatewayName | 데이터 팩터리 서비스가 온-프레미스 SQL Server 데이터베이스에 연결하는 데 사용해야 하는 게이트웨이의 이름입니다. | 예 |
| username | Windows 인증을 사용하는 경우 사용자 이름을 지정합니다. 예: **domainname\\username**. | 아니요 |
| password | 사용자 이름에 지정한 사용자 계정의 암호를 지정합니다. | 아니요 |

**New-AzureRmDataFactoryEncryptValue** cmdlet를 사용하여 자격 증명을 암호화하고 다음 예제와 같이 연결 문자열에 해당 자격 증명을 사용할 수 있습니다(**EncryptedCredential** 속성).

	"connectionString": "Data Source=<servername>;Initial Catalog=<databasename>;Integrated Security=True;EncryptedCredential=<encrypted credential>",

### 샘플

**SQL 인증을 사용하는 JSON**

	{
	    "name": "MyOnPremisesSQLDB",
	    "properties":
	    {
	        "type": "OnPremisesSqlLinkedService",
			"typeProperties": {
	        	"connectionString": "Data Source=<servername>;Initial Catalog=MarketingCampaigns;Integrated Security=False;User ID=<username>;Password=<password>;",
	        	"gatewayName": "<gateway name>"
			}
	    }
	}

**Windows 인증을 사용하는 JSON**

사용자 이름 및 암호가 지정된 경우 게이트웨이는 이러한 정보로 지정된 사용자 계정을 가장하여 온-프레미스 SQL Server 데이터베이스에 연결합니다. 그렇지 않은 경우 게이트웨이는 게이트웨이의 보안 컨텍스트(시작 계정)를 사용하여 SQL Server로 직접 연결합니다.

	{
	     "Name": " MyOnPremisesSQLDB",
	     "Properties":
	     {
	         "type": "OnPremisesSqlLinkedService",
			 "typeProperties": {
	         	"ConnectionString": "Data Source=<servername>;Initial Catalog=MarketingCampaigns;Integrated Security=True;",
	         	"username": "<domain\\username>",
	         	"password": "<password>",
	         	"gatewayName": "<gateway name>"
			}
	     }
	}

SQL Server 데이터 원본의 자격 증명 설정에 대한 자세한 내용은 [자격 증명 및 보안 설정](data-factory-move-data-between-onprem-and-cloud.md#set-credentials-and-security)을 참조하세요.

## SQL Server 데이터집합 형식 속성

데이터 집합 정의에 사용할 수 있는 섹션 및 속성의 전체 목록은 [데이터 집합 만들기](data-factory-create-datasets.md) 문서를 참조하세요. 구조, 가용성 및 JSON 데이터 집합의 정책과 같은 섹션이 모든 데이터 집합 형식에 대해 유사합니다(SQL Server, Azure blob, Azure 테이블 등).

typeProperties 섹션은 데이터 집합의 각 형식에 따라 다르며 데이터 저장소에 있는 데이터의 위치에 대한 정보를 제공합니다. **SqlServerTable** 데이터 집합 형식의 **typeProperties** 섹션에는 다음 속성이 있습니다.

| 속성 | 설명 | 필수 |
| -------- | ----------- | -------- |
| tableName | 연결된 서비스가 참조하는 SQL Server 데이터베이스 인스턴스에서 테이블의 이름입니다. | 예 |

## SQL Server 복사 작업 형식 속성

활동 정의에 사용할 수 있는 섹션 및 속성의 전체 목록은 [파이프라인 만들기](data-factory-create-pipelines.md) 문서를 참조하세요. 이름, 설명, 입력 및 출력 테이블, 다양한 정책 등과 같은 속성은 모든 유형의 활동에 사용할 수 있습니다.

> [AZURE.NOTE] 복사 작업은 하나의 입력을 가지고 하나의 출력을 생성합니다.

반면 작업의 typeProperties 섹션에서 사용할 수 있는 속성은 각 작업 형식에 따라 다르며 복사 작업의 경우 속성은 원본 및 싱크의 형식에 따라 다릅니다.

### SqlSource

원본이 **SqlSource** 형식인 복사 작업의 경우 **typeProperties** 섹션에서 다음과 같은 속성을 사용할 수 있습니다.

| 속성 | 설명 | 허용되는 값 | 필수 |
| -------- | ----------- | -------------- | -------- |
| sqlReaderQuery | 사용자 지정 쿼리를 사용하여 데이터를 읽습니다. | SQL 쿼리 문자열입니다. 예: select * from MyTable. 입력 데이터 집합에 의해 참조되는 데이터베이스의 여러 테이블을 참조할 수 있습니다. 지정하지 않는 경우 실행되는 SQL 문: select from MyTable. | 아니요 |
| sqlReaderStoredProcedureName | 원본 테이블에서 데이터를 읽는 저장 프로시저의 이름입니다. | 저장 프로시저의 이름입니다. | 아니요 |
| storedProcedureParameters | 저장 프로시저에 대한 매개 변수입니다. | 이름/값 쌍입니다. 매개 변수의 이름 및 대소문자와, 저장 프로시저 매개변수의 이름 및 대소문자와 일치해야 합니다. | 아니요 |

**sqlReaderQuery**가 SqlSource에 지정되면 복사 작업은 데이터를 가져오는 SQL Server 데이터베이스 원본에 대해 이 쿼리를 실행합니다.

또는 **sqlReaderStoredProcedureName** 및 **storedProcedureParameters**를 지정하여 저장 프로시저를 지정할 수 있습니다(저장 프로시저가 매개 변수를 사용하는 경우).

sqlReaderQuery 또는 sqlReaderStoredProcedureName 중 하나를 지정하지 않으면 JSON 데이터 집합의 구조 섹션에 정의된 열이 SQL Server 데이터베이스에 대해 실행되는 쿼리를 작성하는 데 사용됩니다(mytable에서 column1, column2 선택). 데이터 집합 정의에 구조가 없는 경우 모든 열은 테이블에서 선택됩니다.

> [AZURE.NOTE] **sqlReaderStoredProcedureName**을 사용하는 경우에도 데이터 집합 JSON에서 **tableName** 속성 값을 지정해야 합니다. 이는 제품의 현재 제한 사항입니다. 그러나 이 테이블에 대해 수행되는 유효성 검사는 없습니다.

### SqlSink

**SqlSink**는 다음 속성을 지원합니다.


| 속성 | 설명 | 허용되는 값 | 필수 |
| -------- | ----------- | -------------- | -------- |
| writeBatchTimeout | 시간이 초과되기 전에 완료하려는 배치 삽입 작업을 위한 대기 시간입니다. | timespan<br/><br/> 예: “00:30:00”(30분). | 아니요 |
| writeBatchSize | 버퍼 크기가 writeBatchSize에 도달하는 경우 SQL 테이블에 데이터 삽입 | 정수(행 수) | 아니요(기본값: 10000)
| sqlWriterCleanupScript | 사용자는 데이터의 특정 조각을 정리할 수 있도록 실행하는 복사 작업에 쿼리를 지정했습니다. 자세한 내용은 아래 반복성 섹션을 참조하세요. | 쿼리 문입니다. | 아니요 |
| sliceIdentifierColumnName | 사용자는 자동 생성된 조각 식별자로 채워진 복사 작업에 열 이름을 지정하여 다시 실행하는 경우 특정 조각의 데이터를 정리하는 데 사용합니다. 자세한 내용은 아래 반복성 섹션을 참조하세요. | 이진(32) 데이터 형식이 있는 열의 열 이름입니다. | 아니요 |
| sqlWriterStoredProcedureName | 대상 테이블에 대한 데이터 Upsert(업데이트/삽입)를 수행하는 저장 프로시저의 이름입니다. | 저장 프로시저의 이름입니다. | 아니요 |
| storedProcedureParameters | 저장 프로시저에 대한 매개 변수입니다. | 이름/값 쌍입니다. 매개 변수의 이름 및 대소문자와, 저장 프로시저 매개변수의 이름 및 대소문자와 일치해야 합니다. | 아니요 |
| sqlWriterTableType | 위의 저장 프로시저에서 사용할 사용자가 지정한 테이블 형식 이름입니다. 복사 작업을 사용하면 이 테이블 형식으로 임시 테이블에서 사용할 수 있는 데이터를 이동시킵니다. 그러면 저장 프로시저 코드가 복사되는 데이터를 기존 데이터와 병합할 수 있습니다. | 테이블 유형 이름 | 아니요 |

## 연결 문제 해결

1. 원격 연결을 허용하도록 SQL Server를 구성합니다. **SQL Server Management Studio**를 시작하고 **서버**를 마우스 오른쪽 단추로 클릭하고 **속성**을 클릭합니다. 목록에서 **연결**을 선택하고 **서버에 대한 원격 연결 허용**을 선택합니다.

	![원격 연결 사용](.\media\data-factory-sqlserver-connector\AllowRemoteConnections.png)

	자세한 단계를 보려면 [원격 액세스 서버 구성 옵션 구성](https://msdn.microsoft.com/library/ms191464.aspx)을 참조하세요.
2. **SQL Server 구성 관리자**를 시작합니다. 사용하려는 인스턴스에 대한 **SQL Server 네트워크 구성**을 확장하고 **MSSQLSERVER용 프로토콜**을 선택합니다. 오른쪽 창에 프로토콜이 표시됩니다. **TCP/IP**를 마우스 오른쪽 단추로 클릭하고 **사용**을 클릭하여 TCP/IP를 사용하도록 설정합니다.

	![TCP/IP 사용](.\media\data-factory-sqlserver-connector\EnableTCPProptocol.png)

	TCP/IP 프로토콜을 사용하는 다른 방법 및 자세한 내용은 [서버 네트워크 프로토콜 사용 또는 사용 안 함](https://msdn.microsoft.com/library/ms191294.aspx)을 참조하세요.
3. 같은 창에서 **TCP/IP**를 두 번 클릭하여 **TCP/IP 속성** 창을 시작합니다.
4. **IP 주소** 탭으로 전환합니다. 아래로 스크롤하여 **IPAll** 섹션을 확인합니다. **TCP 포트**(기본값은 **1433**)를 기록해 둡니다.
5. 컴퓨터에 **Windows 방화벽에 대한 규칙**을 만들어 이 포트를 통해 들어오는 트래픽을 허용합니다.
6. **연결 확인**: 다른 컴퓨터에서 SQL Server Management Studio를 통해 정규화된 이름을 사용하여 SQL Server에 연결합니다. 예: <컴퓨터>.<도메인>.corp.<회사>.com,1433.

	> [AZURE.IMPORTANT]
	자세한 내용은 [포트 및 보안 고려 사항](data-factory-move-data-between-onprem-and-cloud.md#port-and-security-considerations)을 참조하세요.
	>   
	> 연결/게이트웨이 관련 문제 해결에 대한 팁은 [게이트웨이 문제 해결](data-factory-data-management-gateway.md#troubleshoot-gateway-issues)을 참조하세요.

## 대상 데이터베이스의 ID 열
이 섹션에서는 ID 열이 없는 원본 테이블에서 ID 열이 있는 대상 테이블로 데이터를 복사하는 예제를 제공합니다.

**원본 테이블:**

	create table dbo.SourceTbl
	(
	       name varchar(100),
	       age int
	)

**대상 테이블:**

	create table dbo.TargetTbl
	(
	       id int identity(1,1),
	       name varchar(100),
	       age int
	)


대상 테이블에 ID 열이 있는지 확인합니다.

**원본 데이터 집합 JSON 정의**

	{
	    "name": "SampleSource",
	    "properties": {
	        "published": false,
	        "type": " SqlServerTable",
	        "linkedServiceName": "TestIdentitySQL",
	        "typeProperties": {
	            "tableName": "SourceTbl"
	        },
	        "availability": {
	            "frequency": "Hour",
	            "interval": 1
	        },
	        "external": true,
	        "policy": {}
	    }
	}

**대상 데이터 집합 JSON 정의**

	{
	    "name": "SampleTarget",
	    "properties": {
	        "structure": [
	            { "name": "name" },
	            { "name": "age" }
	        ],
	        "published": false,
	        "type": "AzureSqlTable",
	        "linkedServiceName": "TestIdentitySQLSource",
	        "typeProperties": {
	            "tableName": "TargetTbl"
	        },
	        "availability": {
	            "frequency": "Hour",
	            "interval": 1
	        },
	        "external": false,
	        "policy": {}
	    }
	}


원본 테이블과 대상 테이블의 스키마가 서로 다릅니다(대상에 ID가 포함된 추가 열이 있음). 이 시나리오에서는 ID 열을 포함하지 않는 대상 데이터 집합 정의에서 **structure** 속성을 지정해야 합니다.

[AZURE.INCLUDE [data-factory-type-repeatability-for-sql-sources](../../includes/data-factory-type-repeatability-for-sql-sources.md)]


[AZURE.INCLUDE [data-factory-sql-invoke-stored-procedure](../../includes/data-factory-sql-invoke-stored-procedure.md)]


[AZURE.INCLUDE [data-factory-structure-for-rectangualr-datasets](../../includes/data-factory-structure-for-rectangualr-datasets.md)]


### SQL server 및 Azure SQL에 대한 형식 매핑

[데이터 이동 활동](data-factory-data-movement-activities.md) 문서에서 설명한 대로 복사 작업은 다음 2단계 접근 방식을 사용하여 원본 형식에서 싱크 형식으로 자동 형식 변환을 수행합니다.

1. 네이티브 원본 형식에서 .NET 형식으로 변환
2. .NET 형식에서 네이티브 싱크 형식으로 변환

Azure SQL, SQL server, Sybase에서 데이터를 이동하는 경우 SQL 형식에서 .NET 형식에 그리고 그 반대로 다음 매핑을 사용합니다.

매핑은 ADO.NET에 대한 SQL Server 데이터 형식 매핑과 같습니다.

| SQL Server 데이터베이스 엔진 형식 | .NET Framework 형식 |
| ------------------------------- | ------------------- |
| bigint | Int64 |
| binary | Byte |
| bit | Boolean |
| char | String, Char |
| date | DateTime |
| Datetime | DateTime |
| datetime2 | DateTime |
| Datetimeoffset | DateTimeOffset |
| 10진수 | 10진수 |
| FILESTREAM 특성(varbinary(max)) | Byte |
| Float | Double |
| 이미지 | Byte |
| int | Int32 |
| money | 10진수 |
| nchar | String, Char |
| ntext | String, Char |
| numeric | 10진수 |
| nvarchar | String, Char |
| real | 단일 |
| rowversion | Byte |
| smalldatetime | DateTime |
| smallint | Int16 |
| smallmoney | 10진수 |
| sql\_variant | 개체 * |
| 텍스트 | String, Char |
| 실시간 | TimeSpan |
| timestamp | Byte |
| tinyint | Byte |
| uniqueidentifier | Guid |
| varbinary | Byte |
| varchar | String, Char |
| xml | Xml |


[AZURE.INCLUDE [data-factory-type-conversion-sample](../../includes/data-factory-type-conversion-sample.md)]


[AZURE.INCLUDE [data-factory-column-mapping](../../includes/data-factory-column-mapping.md)]

## 성능 및 튜닝  
Azure Data Factory의 데이터 이동(복사 작업) 성능에 영향을 주는 주요 요소 및 최적화하는 다양한 방법에 대해 알아보려면 [복사 작업 성능 및 조정 가이드](data-factory-copy-activity-performance.md)를 참조하세요.

<!---HONumber=AcomDC_0803_2016-->