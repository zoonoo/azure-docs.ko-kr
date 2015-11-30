<properties 
	pageTitle="Oracle에서 데이터 이동 | Azure 데이터 팩터리" 
	description="Azure 데이터 팩터리를 사용하여 온-프레미스 Oracle 데이터베이스 간 데이터를 이동하는 방법에 대해 알아봅니다." 
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
	ms.date="11/12/2015" 
	ms.author="spelluru"/>

# Azure 데이터 팩터리를 사용하여 온-프레미스 Oracle에서 데이터 이동 

이 문서에서는 데이터 팩터리를 사용하여 Oracle에서 다른 데이터 저장소로 데이터를 이동하는 방법에 대해 간략하게 설명합니다. 이 문서는 복사 작업 및 지원되는 데이터 저장소 조합을 사용하여 데이터 이동의 일반적인 개요를 보여주는 [데이터 이동 활동](data-factory-data-movement-activities.md) 문서를 작성합니다.

## 설치 
Azure 데이터 팩터리 서비스가 사용자의 온-프레미스 Oracle 데이터베이스에 연결할 수 있도록 하려면 다음을 설치해야 합니다.

- 데이터 관리 게이트웨이를 데이터베이스를 호스팅하는 컴퓨터와 같은 컴퓨터에 설치하거나 데이터베이스와 리소스 경쟁을 피하려면 별도의 컴퓨터에 설치합니다. 데이터 관리 게이트웨이는 온-프레미스 데이터 원본을 클라우드 서비스에 안전하고 관리되는 방식으로 연결하는 소프트웨어입니다. 데이터 관리 게이트웨이에 대한 자세한 내용은 [온-프레미스 및 클라우드 간 데이터 이동](data-factory-move-data-between-onprem-and-cloud.md)을 참조하세요. 
- [Oracle Data Access Components (ODAC) for Windows](http://www.oracle.com/technetwork/topics/dotnet/downloads/index.html). 이 프로그램은 게이트웨이가 설치되어 있는 호스트 컴퓨터에 설치해야 합니다.


## 샘플: Oracle에서 Azure Blob로 데이터 복사

아래 샘플은 다음을 보여줍니다.

1.	[OnPremisesOracle](data-factory-onprem-oracle-connector.md#oracle-linked-service-properties) 형식의 연결된 서비스
2.	[AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service-properties) 형식의 연결된 서비스
3.	[OracleTable](data-factory-onprem-oracle-connector.md#oracle-dataset-type-properties) 형식의 입력 [데이터 집합](data-factory-create-datasets.md) 
4.	[AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties) 형식의 출력 [데이터 집합](data-factory-create-datasets.md)
5.	[OracleSource](data-factory-onprem-oracle-connector.md#oracle-copy-activity-type-properties)를 소스로, [BlobSink](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties)를 싱크로 사용하는 복사 작업의 [파이프라인](data-factory-create-pipelines.md)

샘플은 온-프레미스 Oracle 데이터베이스의 테이블에서 blob에 매시간 데이터를 복사합니다. 아래 샘플에 사용되는 다양한 속성에 대한 자세한 내용은 샘플 다음에 나오는 섹션의 다양한 속성에 대한 문서를 참조하세요.

**Oracle 연결된 서비스:**

	{
	  "name": "OnPremisesOracleLinkedService",
	  "properties": {
	    "type": "OnPremisesOracle",
	    "typeProperties": {
	      "ConnectionString": "data source=<data source>;User Id=<User Id>;Password=<Password>;",
	      "gatewayName": "<gateway name>"
	    }
	  }
	}

**Azure Blob 저장소 연결된 서비스:**

	{
	  "name": "StorageLinkedService",
	  "properties": {
	    "type": "AzureStorage",
	    "typeProperties": {
	      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<Account key>"
	    }
	  }
	}

**Oracle 입력 데이터 집합:**

샘플은 Oracle에서 만든 테이블 "MyTable"에 시계열 데이터에 대한 "timestampcolumn"이라는 열이 포함되어 있다고 가정합니다.

"external": "true"를 설정하고 externalData 정책을 지정함으로써 데이터 팩터리에 이 테이블이 데이터 팩터리의 외부에 있으며 데이터 팩터리의 작업에 의해 생성되지 않는다는 점을 알려줍니다.

	{
	    "name": "OracleInput",
	    "properties": {
	        "type": "OracleTable",
	        "linkedServiceName": "OnPremisesOracleLinkedService",
	        "typeProperties": {
	            "tableName": "MyTable"
	        },
	           "external": true,
	        "availability": {
	            "offset": "01:00:00",
	            "interval": "1",
	            "anchorDateTime": "2014-02-27T12:00:00",
	            "frequency": "Hour"
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

데이터는 매시간 새 blob에 기록됩니다(frequency: hour, interval: 1). Blob에 대한 폴더 경로 및 파일 이름은 처리 중인 조각의 시작 시간에 기반하여 동적으로 평가됩니다. 폴더 경로는 시작 시간에서 연도, 월, 일 및 시간 부분을 사용합니다.
	
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

파이프라인은 위의 입력 및 출력 데이터 집합을 사용하도록 구성된 복사 작업을 포함하고 매시간 실행하도록 예약됩니다. 파이프라인 JSON 정의에서 **source** 형식은 **RelationalSource**로 설정되고 **sink** 형식은 **BlobSink**로 설정됩니다. **oracleReaderQuery** 속성에 지정된 SQL 쿼리는 과거 한 시간에서 복사할 데이터를 선택합니다.

	
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
	            "name": " OracleInput"
	          }
	        ],
	        "outputs": [
	          {
	            "name": "AzureBlobOutput"
	          }
	        ],
	        "typeProperties": {
	          "source": {
	            "type": "OracleSource",
	            "oracleReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd HH:mm}\\' AND timestampcolumn < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
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

## Oracle 연결된 서비스 속성

다음 표에서는 Oracle 연결된 서비스와 관련된 JSON 요소에 대한 설명을 제공합니다.

속성 | 설명 | 필수
-------- | ----------- | --------
type | type 속성은 **OnPremisesOracle**로 설정되어야 합니다. | 예
connectionString | connectionString 속성에 대한 Oracle 데이터베이스 인스턴스에 연결하는 데 필요한 정보를 지정합니다. | 예 
gatewayName | 온-프레미스 Oracle 서버에 연결하는 데 사용할 게이트웨이 이름입니다. | 예

온-프레미스 Oracle 데이터 원본의 자격 증명 설정에 대한 자세한 내용은 [자격 증명 및 보안 설정](data-factory-move-data-between-onprem-and-cloud.md#setting-credentials-and-security)을 참조하세요.
## Oracle 데이터 집합 형식 속성

데이터 집합 정의에 사용할 수 있는 섹션 및 속성의 전체 목록은 [데이터 집합 만들기](data-factory-create-datasets.md) 문서를 참조하십시오. 구조, 가용성 및 JSON 데이터 집합의 정책과 같은 섹션이 모든 데이터 집합 형식에 대해 유사합니다(Oracle, Azure blob, Azure 테이블 등).
 
typeProperties 섹션은 데이터 집합의 각 형식에 따라 다르며 데이터 저장소에 있는 데이터의 위치에 대한 정보를 제공합니다. OracleTable 데이터 집합 형식의 데이터 집합에 대한 typeProperties 섹션에는 다음 속성이 있습니다.

속성 | 설명 | 필수
-------- | ----------- | --------
tableName | 연결된 서비스가 참조하는 Oracle 데이터베이스에 있는 테이블의 이름입니다. | 예

## Oracle 복사 작업 형식 속성

작업 정의에 사용할 수 있는 섹션 및 속성의 전체 목록은 [파이프라인 만들기](data-factory-create-pipelines.md) 문서를 참조하세요. 이름, 설명, 입력 및 출력 테이블, 다양한 정책 등과 같은 속성은 모든 유형의 활동에 사용할 수 있습니다.

**참고:** 복사 작업은 하나의 입력을 사용하여 하나의 출력을 생성합니다.

반면 작업의 typeProperties 섹션에서 사용할 수 있는 속성은 각 작업 형식에 따라 다르며 복사 작업의 경우 속성은 원본 및 싱크의 형식에 따라 다릅니다.

원본이 SqlSource 형식인 복사 작업의 경우 typeProperties 섹션에서 다음과 같은 속성을 사용할 수 있습니다.

속성 | 설명 |허용되는 값 | 필수
-------- | ----------- | ------------- | --------
oracleReaderQuery | 사용자 지정 쿼리를 사용하여 데이터를 읽습니다. | SQL 쿼리 문자열. 
예: select * from MyTable <p>지정하지 않는 경우 실행되는 SQL 문: select * from MyTable</p> | 아니요

[AZURE.INCLUDE [data-factory-structure-for-rectangualr-datasets](../../includes/data-factory-structure-for-rectangualr-datasets.md)]

### Oracle에 대한 형식 매핑

[데이터 이동 활동](data-factory-data-movement-activities.md) 문서에서 설명한 것처럼 복사 작업은 다음 2단계 접근 방법을 사용하여 원본 형식에서 싱크 형식까지 자동 형식 변환을 수행합니다.

1. 네이티브 원본 형식에서 .NET 형식으로 변환
2. .NET 형식에서 네이티브 싱크 형식으로 변환

Oracle에서 데이터를 이동하는 경우 Oracle 데이터 형식에서 .NET 형식에 그리고 그 반대로 다음 매핑을 사용합니다.

Oracle 데이터 형식 | .NET Framework 데이터 형식
---------------- | ------------------------
BFILE | Byte
BLOB | Byte
CHAR | 문자열
CLOB | String
DATE | DateTime
FLOAT | 10진수
INTEGER | 10진수
INTERVAL YEAR TO MONTH | Int32
INTERVAL DAY TO SECOND | TimeSpan
LONG | 문자열
LONG RAW | Byte
NCHAR | 문자열
NCLOB | 문자열
NUMBER | 10진수
NVARCHAR2 | 문자열
RAW | Byte
ROWID | String
TIMESTAMP | DateTime
TIMESTAMP WITH LOCAL TIME ZONE | DateTime
TIMESTAMP WITH TIME ZONE | DateTime
UNSIGNED INTEGER | Number
VARCHAR2 | 문자열
XML | String

## 문제 해결 팁

****문제: ** 다음 **오류 메시지**가 표시되었습니다. 복사 작업에 잘못된 매개 변수 'UnknownParameterName'이 있습니다. 자세한 메시지: 요청한 .Net Framework 데이터 공급자를 찾을 수 없습니다. 해당 항목이 설치되어 있지 않은 것 같습니다.

**가능한 원인**

1. .NET Framework Data Provider for Oracle이 설치되지 않았습니다.
2. .NET Framework Data Provider for Oracle이 .NET Framework 2.0에 설치되었고 .NET Framework 4.0 폴더에서 찾을 수 없습니다. 

**해결 방법**

1. .NET Provider for Oracle을 설치하지 않았으면 [해당 항목을 설치](http://www.oracle.com/technetwork/topics/dotnet/utilsoft-086879.html)한 후 시나리오를 다시 시도합니다. 
2. Provider를 설치한 후에도 오류 메시지가 표시되면 다음을 수행합니다. 
	1. <system disk>:\\Windows\\Microsoft.NET\\Framework64\\v2.0.50727\\CONFIG\\machine.config 폴더에서 .NET 2.0 machine config 파일을 엽니다.
	2. **Oracle Data Provider for .NET**을 검색하여 **system.data** -> **DbProviderFactories**에서 “<add name="Oracle Data Provider for .NET" invariant="Oracle.DataAccess.Client" description="Oracle Data Provider for .NET" type="Oracle.DataAccess.Client.OracleClientFactory, Oracle.DataAccess, Version=2.112.3.0, Culture=neutral, PublicKeyToken=89b483f429c47342" />”와 같은 항목을 찾습니다.
2.	이 항목을 v4.0 폴더 즉, <system disk>:\\Windows\\Microsoft.NET\\Framework64\\v4.0.30319\\Config\\machine.config의 machine.config 파일에 복사하고 버전을 4.xxx.x.x으로 변경합니다.
3.	“gacutil /i [provider path]”를 실행하여 전역 어셈블리 캐시(GAC)에 “<ODP.NET Installed Path>\\11.2.0\\client\_1\\odp.net\\bin\\4\\Oracle.DataAccess.dll”을 설치합니다.



[AZURE.INCLUDE [data-factory-column-mapping](../../includes/data-factory-column-mapping.md)]

<!---HONumber=Nov15_HO4-->