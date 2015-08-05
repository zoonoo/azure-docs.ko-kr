<properties 
	pageTitle="Azure 데이터 팩터리에서 복사 작업을 사용하는 예제" 
	description="Azure 데이터 팩터리에서 복사 작업을 사용하는 예제를 제공합니다." 
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
	ms.date="07/07/2015" 
	ms.author="spelluru"/>

# Azure 데이터 팩터리에서 복사 작업을 사용하는 예제
파이프라인에서 **복사 작업**을 사용하여 원본에서 배치의 싱크(대상)로 데이터를 복사할 수 있습니다. 이 항목에서는 데이터 팩터리 파이프라인에서 복사 작업을 사용하는 몇 가지 예를 제공합니다. 복사 작업 및 이를 지원하는 핵심 시나리오의 자세한 개요는 [Azure 데이터 팩터리를 사용하여 데이터 복사][adf-copyactivity]를 참조하세요.

## 온-프레미스 SQL Server 데이터베이스에서 Azure Blob으로 데이터 복사
이 예제에서는 입력 테이블과 출력 테이블을 정의하고 온-프레미스 SQL Server 데이터베이스에서 Azure Blob으로 데이터를 복사하는 파이프라인 내의 복사 작업에서 이 테이블을 사용합니다.

### 가정
이 예제에서는 사용자에게 이미 다음과 같은 Azure 데이터 팩터리 아티팩트가 있다고 가정합니다.

* **ADF**라는 리소스 그룹
* **CopyFactory**라는 Azure 데이터 팩터리
* **mygateway**라는 데이터 관리 게이트웨이가 만들어지고 온라인 상태입니다.  

### 소스 온-프레미스 SQL Server 데이터베이스에 대한 연결된 서비스 만들기
이 단계에서는 온-프레미스 SQL Server 데이터베이스를 가리키는 **MyOnPremisesSQLDB**라는 연결된 서비스를 만듭니다.

	{
	    "name": "MyOnPremisesSQLDB",
	    "properties":
	    {
	        "type": "OnPremisesSqlLinkedService",
	        "connectionString": "Data Source=<servername>;Initial Catalog=<database>;Integrated Security=False;User ID=<username>;Password=<password>;",
	        "gatewayName": "mygateway"
	    }
	}

다음 사항에 유의하세요.

- **type**을 **OnPremisesSqlLinkedService**로 설정합니다.
- **connectionString**을 SQL Server 데이터베이스에 대한 연결 문자열로 설정합니다. 
- **gatewayName**을 온-프레미스 컴퓨터에 설치하고 Azure 데이터 팩터리 서비스 포털에 등록한 데이터 관리 게이트웨이의 이름으로 설정합니다. 

온-프레미스 SQL 연결된 서비스를 정의하는 JSON 요소에 대한 자세한 내용은 [온-프레미스 SQL 연결된 서비스](https://msdn.microsoft.com/library/dn893523.aspx)를 참조하세요.
 
### 대상 Azure Blob에 대한 연결된 서비스 만들기
이 단계에서는 Azure Blob 저장소를 가리키는 **MyAzureStorage**라는 연결된 서비스를 만듭니다.

	{
	    "name": "MyAzureStorage",
	    "properties":
	    {
	        "type": "AzureStorageLinkedService",
	        "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>" "
	    }
	}

다음 사항에 유의하세요.

- **type**을 **AzureStorageLinkedService**로 설정합니다.
- **connectionString** - Azure 저장소에 대한 계정 이름 및 계정 키를 지정합니다.

Azure 저장소 연결된 서비스를 정의하는 JSON 요소에 대한 자세한 내용은 [Azure 저장소 연결된 서비스](https://msdn.microsoft.com/library/dn893522.aspx)를 참조하세요.

### 입력 테이블 JSON
다음 JSON 스크립트는 **MyOnPremisesSQLDB** 연결 서비스에 정의된 온-프레미스 SQL Server 데이터베이스에서 **MyTable** SQL 테이블을 참조하는 입력 테이블을 정의합니다. **name**은 Azure 데이터 팩터리 테이블의 이름이고 **tableName**은 SQL Server 데이터베이스에 있는 SQL 테이블의 이름입니다.

         
	{
		"name": "MyOnPremTable",
    	"properties":
   		{
			"location":
    		{
    			"type": "OnPremisesSqlServerTableLocation",
    			"tableName": "MyTable",
    			"linkedServiceName": "MyOnPremisesSQLDB"
    		},
    		"availability":
   			{
    			"frequency": "Hour",
    			"interval": 1
   			}
 		}
	}

다음 사항에 유의하세요.

- **type**을 **OnPremisesSqlServerTableLocation**으로 설정합니다.
- **tableName**을 원본 데이터가 들어 있는 **MyTable**로 설정합니다. 
- **linkedServiceName**을 온-프레미스 SQL 데이터베이스에 대해 만든 연결된 서비스인 **MyOnPremisesSQLDB**로 설정합니다.

SQL Server 테이블을 참조하는 데이터 팩터리 테이블을 정의하는 JSON 요소에 대한 자세한 내용은 [온-프레미스 SQL 위치 속성](https://msdn.microsoft.com/library/dn894089.aspx#OnPremSQL)을 참조하세요.

### 출력 테이블 JSON
다음 JSON 스크립트는 Blob 폴더 **MySubFolder** 및 Blob 컨테이너 **MyContainer**의 Azure Blob **MyBlob**을 참조하는 **MyAzureBlob**이라는 출력 테이블을 정의합니다.
         
	{
   		"name": "MyAzureBlob",
	    "properties":
    	{
    		"location":
    		{
        		"type": "AzureBlobLocation",
        		"folderPath": "MyContainer/MySubFolder",
        		"fileName": "MyBlob",
        		"linkedServiceName": "MyAzureStorage",
        		"format":
        		{
            		"type": "TextFormat",
            		"columnDelimiter": ",",
            		"rowDelimiter": ";",
             		"EscapeChar": "$",
             		"NullValue": "NaN"
        		}
    		},
        	"availability":
      		{
       			"frequency": "Hour",
       			"interval": 1
      		}
   		}
	}

다음 사항에 유의하세요.
 
- **type**을 **AzureBlobLocation**으로 설정합니다.
- **folderPath**를 복사한 데이터를 보유하는 Blob이 들어 있는 **MyContainer/MySubFolder**로 설정합니다. 
- **fileName**을 출력 데이터를 보유하는 Blob인 **MyBlob**으로 설정합니다.
- **linkedServiceName**을 Azure 저장소에 대해 만든 연결된 서비스인 **MyAzureStorge**로 설정합니다.    

Azure Blob을 참조하는 데이터 팩터리 테이블을 정의하는 JSON 요소에 대한 자세한 내용은 [Azure Blob 위치 속성](https://msdn.microsoft.com/library/dn894089.aspx#AzureBlob)을 참조하세요.

### 파이프라인(복사 작업 포함) JSON
이 예제에서는 **CopyActivityPipeline** 파이프라인을 다음 속성으로 정의합니다.

- **type** 속성을 **CopyActivity**로 설정합니다.
- **MyOnPremTable**을 입력으로 지정합니다(**inputs** 태그).
- **MyAzureBlob**을 출력으로 지정합니다(**outputs** 태그).
- **Transformation** 섹션에는 **source** 및 **sink**라는 두 개의 하위 섹션이 있습니다. 원본의 유형을 **SqlSource**로 설정하고 싱크의 유형을 **BlobSink**로 설정합니다. **sqlReaderQuery**는 원본에 대해 수행할 변환(프로젝션)을 정의합니다. 모든 속성에 대한 자세한 내용은[ JSON 스크립트 참조](https://msdn.microsoft.com/library/dn835050.aspx)를 참조하세요.

         
		{
		    "name": "CopyActivityPipeline",
    		"properties":
    		{
				"description" : "This is a sample pipeline to copy data from SQL Server to Azure Blob",
        		"activities":
        		[
      				{
						"name": "CopyActivity",
						"description": "description", 
						"type": "CopyActivity",
						"inputs":  [ { "name": "MyOnPremTable"  } ],
						"outputs":  [ { "name": "MyAzureBlob" } ],
						"transformation":
	    				{
							"source":
							{
								"type": "SqlSource",
                    			"sqlReaderQuery": "select * from MyTable"
							},
							"sink":
							{
                        		"type": "BlobSink"
							}
	    				}
      				}
        		]
    		}
		}

데이터 팩터리 파이프라인을 정의하는 JSON 요소에 대한 자세한 내용은 [파이프라인 JSON 참조](https://msdn.microsoft.com/library/dn834988.aspx)를 참조하고, SqlSource의 속성(예: 예제의 **sqlReaderQuery**) 및 BlobSink에 대해서는 [지원되는 원본 및 싱크](https://msdn.microsoft.com/library/dn894007.aspx)를 참조하세요.


## 온-프레미스 파일 시스템에서 Azure Blob으로 데이터 복사
복사 작업을 사용하여 온-프레미스 파일 시스템(Windows/Linux 네트워크 공유 또는 Windows 로컬 호스트)에서 Azure Blob으로 파일을 복사할 수 있습니다. 호스트는 Samba가 구성된 Windows 또는 Linux일 수 있습니다. 데이터 관리 게이트웨이는 호스트에 연결할 수 있는 Windows 컴퓨터에 설치해야 합니다.

### 가정
이 예제에서는 다음을 가정합니다.

- **Host** - 파일 시스템을 호스트하는 서버의 이름(**\contoso**)입니다.
- **Folder** - 입력 파일이 들어 있는 폴더의 이름(**marketingcampaign\regionaldata\{slice})입니다. 여기서 파일은 2014121112(2014년, 12월, 11일, 12시)와 같이 {slice}라는 폴더에 분할됩니다. 

### 온-프레미스 파일 시스템 연결된 서비스 만들기
다음 샘플 JSON을 사용하여 **FolderDataStore**라는 **OnPremisesFileSystemLinkedService** 형식의 연결된 서비스를 만들 수 있습니다.

	{
	    "name": "FolderDataStore",
	    "properties": {
	        "type": "OnPremisesFileSystemLinkedService",
	        "host": "\\contoso",
	        "userId": "username",
	        "password": "password",
	        "gatewayName": "ContosoGateway"
	    }
	}

> [AZURE.NOTE]JSON 파일의 호스트 및 폴더 이름에는 이스케이프 문자 ''를 사용해야 합니다. **\Contoso**의 경우 **\\Contoso**를 사용합니다.

온-프레미스 파일 시스템 연결된 서비스를 정의하는 JSON 요소에 대한 자세한 내용은 [온-프레미스 파일 시스템 연결된 서비스](https://msdn.microsoft.com/library/dn930836.aspx)를 참조하세요.

### 대상 Azure Blob에 대한 연결된 서비스 만들기
다음 샘플 JSON을 사용하여 **MyAzureStorage**라는 **AzureStorageLinkedSerivce** 형식의 연결된 서비스를 만들 수 있습니다.

	{
	    "name": "MyAzureStorage",
	    "properties":
	    {
	        "type": "AzureStorageLinkedService",
	        "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>" "
	    }
	}

Azure 저장소 연결된 서비스를 정의하는 JSON 요소에 대한 자세한 내용은 [Azure 저장소 연결된 서비스](https://msdn.microsoft.com/library/dn893522.aspx)를 참조하세요.

### 입력 테이블 만들기
다음 JSON 스크립트는 앞에서 만든 온-프레미스 파일 시스템 연결된 서비스를 참조하는 입력 테이블을 정의합니다.

	{
	    "name": "OnPremFileSource",
	    "properties": {
	        "location": {
	            "type": "OnPremisesFileSystemLocation",
	            "folderPath": "marketingcampaign\regionaldata\{Slice}",
	            "partitionedBy": [
	                { "name": "Slice", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyyMMddHH" } }
	            ],
	            "linkedServiceName": "FolderDataStore"
	        },
	        "availability": {
	            "waitOnExternal": { },
	            "frequency": "Hour",
	            "interval": 24
	        }
	    }
	}

온-프레미스 파일 시스템을 참조하는 데이터 팩터리 테이블을 정의하는 JSON 요소에 대한 자세한 내용은 [온-프레미스 파일 시스템 위치 속성](https://msdn.microsoft.com/library/dn894089.aspx#OnPremFileSystem)을 참조하세요.

### 출력 테이블 만들기
다음 JSON 스크립트는 Blob 폴더 **MySubFolder** 및 Blob 컨테이너 **MyContainer**의 Azure Blob **MyBlob**을 참조하는 **AzureBlobDest**라는 출력 테이블을 정의합니다.
         
	{
   		"name": "AzureBlobDest",
	    "properties":
    	{
    		"location":
    		{
        		"type": "AzureBlobLocation",
        		"folderPath": "MyContainer/MySubFolder",
        		"fileName": "MyBlob",
        		"linkedServiceName": "MyAzureStorage",
        		"format":
        		{
            		"type": "TextFormat",
            		"columnDelimiter": ",",
            		"rowDelimiter": ";",
             		"EscapeChar": "$",
             		"NullValue": "NaN"
        		}
    		},
        	"availability":
      		{
       			"frequency": "Hour",
       			"interval": 1
      		}
   		}
	}

Azure Blob을 참조하는 데이터 팩터리 테이블을 정의하는 JSON 요소에 대한 자세한 내용은 [Azure Blob 위치 속성](https://msdn.microsoft.com/library/dn894089.aspx#AzureBlob)을 참조하세요.

### 파이프라인 만들기
다음 파이프라인 JSON은 온-프레미스 파일 시스템에서 대상 Azure Blob으로 데이터를 복사하는 복사 작업이 있는 파이프라인을 정의합니다.
 
	{
	    "name": "CopyFileToBlobPipeline",
	    "properties": {
	        "activities": [
	            {
	                "name": "Ingress",
	                "type": "CopyActivity",
	                "inputs": [ { "name": "OnPremFileSource" } ],
	                "outputs": [ { "name": "AzureBlobDest" } ],
	                "transformation": {
	                    "source": {
	                        "type": "FileSystemSource"
	                    },
	                    "sink": {
	                        "type": "BlobSink"
	                    }
	                },
	                "policy": {
	                    "concurrency": 4,
	                    "timeout": "00:05:00"
	                }
	            }
	        ]
	    }
	}

이 예제의 파이프라인은 구문 분석이나 변환을 수행하지 않고 콘텐츠를 이진으로 복사합니다. **동시성**을 활용하여 파일 조각을 병렬로 복사할 수 있습니다. 이 기능은 과거에 이미 발생한 조각을 이동하려는 경우에 유용합니다.

> [AZURE.NOTE]다른 사용자 계정으로 UNC 경로를 통해 동일한 호스트에서 동시 복사 작업을 수행하면 "동일한 사용자가 둘 이상의 사용자 이름으로 서버 또는 공유 리소스에 다중 연결할 수 없습니다."와 같은 오류가 발생할 수 있습니다. 이것은 보안상 적용되는 운영 체제의 제한 사항입니다. 다른 게이트웨이를 사용하여 복사 작업을 예약하거나 호스트 내에 게이트웨이를 설치하고 UNC 경로 대신 "localhost" 또는 "local"을 사용합니다.

데이터 팩터리 파이프라인을 정의하는 JSON 요소에 대한 자세한 내용은 [파이프라인 JSON 참조](https://msdn.microsoft.com/library/dn834988.aspx)를 참조하고, FileSystemSource의 속성 및 BlobSink에 대해서는 [지원되는 원본 및 싱크](https://msdn.microsoft.com/library/dn894007.aspx)를 참조하세요.

### 파일 시스템 테이블을 사용하는 추가 시나리오

#### 특정 폴더 아래에 있는 모든 파일 복사
샘플 JSON에서는 **folderPath**만 지정합니다.

	{
	    "name": "OnPremFileSource",
	    "properties": {
	        "location": {
	            "type": "OnPremisesFileSystemLocation",
	            "folderPath": "marketingcampaign\regionaldata\na",
	            "linkedServiceName": "FolderDataStore"
	        },
	        ...
	    }
	}
 
#### 특정 폴더 아래에 있는 모든 CSV 파일 복사
**fileFilter**를 ***.csv**로 설정합니다.

	{
	    "name": "OnPremFileSource",
	    "properties": {
	        "location": {
	            "type": "OnPremisesFileSystemLocation",
	            "folderPath": "marketingcampaign\regionaldata\na",
	            "fileFilter": "*.csv",
	            "linkedServiceName": "FolderDataStore"
	        },
	        ...
	    }
	}

#### 특정 파일 복사
**fileFiter**를 특정 파일(**201501.csv**)로 설정합니다.

	{
	    "name": "OnPremFileSource",
	    "properties": {
	        "location": {
	            "type": "OnPremisesFileSystemLocation",
	            "folderPath": "marketingcampaign\regionaldata\na",
	            "fileFilter": "201501.csv",
	            "linkedServiceName": "FolderDataStore"
	        },
	        ...
	    }
	}

## 온-프레미스 Oracle 데이터베이스에서 Azure Blob으로 데이터 복사
복사 작업을 사용하여 온-프레미스 Oracle 데이터베이스에서 Azure Blob으로 파일을 복사할 수 있습니다.

### 온-프레미스 Oracle 데이터베이스에 대한 연결된 서비스 만들기
다음 JSON을 사용하여 온-프레미스 Oracle 데이터베이스를 가리키는 연결된 서비스를 만들 수 있습니다. **type**을 **OnPremisesOracleLinkedService**로 설정합니다.

	{
	    "name": "OnPremOracleSource",
	    "properties": {
	        "type": "OnPremisesOracleLinkedService",
	        "ConnectionString": "data source=ds;User Id=uid;Password=pwd;",
	        "gatewayName": "SomeGateway"
	    }
	}

온-프레미스 Oracle 연결된 서비스를 정의하는 JSON 요소에 대한 자세한 내용은 [온-프레미스 Oracle 연결된 서비스](https://msdn.microsoft.com/library/dn948537.aspx)를 참조하세요.

### 대상 Azure Blob에 대한 연결된 서비스 만들기
다음 샘플 JSON을 사용하여 **MyAzureStorage**라는 **AzureStorageLinkedSerivce** 형식의 연결된 서비스를 만들 수 있습니다.

	{
	    "name": "AzureBlobDest",
	    "properties":
	    {
	        "type": "AzureStorageLinkedService",
	        "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>" "
	    }
	}

Azure 저장소 연결된 서비스를 정의하는 JSON 요소에 대한 자세한 내용은 [Azure 저장소 연결된 서비스](https://msdn.microsoft.com/library/dn893522.aspx)를 참조하세요.

### 입력 테이블 만들기
다음 샘플 JSON을 사용하여 온-프레미스 Oracle 데이터베이스의 테이블을 참조하는 Azure 데이터 팩터리 테이블을 만들 수 있습니다. **location type**을 **OnPremisesOracleTableLocation**으로 설정합니다.

	{
	    "name": "TableOracle",
	    "properties": {
	        "location": {
	            "type": "OnPremisesOracleTableLocation",
	            "tableName": "LOG",
	            "linkedServiceName": "OnPremOracleSource"
	        },
	        "availability": {
	            "frequency": "Day",
	            "interval": "1",
	            "waitOnExternal": {}
	        },
	        "policy": {}
	    }
	} 

온-프레미스 Oracle 데이터베이스의 테이블을 참조하는 데이터 팩터리 테이블을 정의하는 JSON 요소에 대한 자세한 내용은 [온-프레미스 Oracle 위치 속성](https://msdn.microsoft.com/library/dn894089.aspx#Oracle)을 참조하세요.

### 출력 테이블 만들기
다음 JSON 스크립트는 Blob 폴더 **MySubFolder** 및 Blob 컨테이너 **MyContainer**의 Azure Blob **MyBlob**을 참조하는 **MyAzureBlob**이라는 출력 테이블을 정의합니다.
         
	{
   		"name": "MyAzureBlob",
	    "properties":
    	{
    		"location":
    		{
        		"type": "AzureBlobLocation",
        		"folderPath": "MyContainer/MySubFolder",
        		"fileName": "MyBlob",
        		"linkedServiceName": "AzureBlobDest",
        		"format":
        		{
            		"type": "TextFormat",
            		"columnDelimiter": ",",
            		"rowDelimiter": ";",
             		"EscapeChar": "$",
             		"NullValue": "NaN"
        		}
    		},
        	"availability":
      		{
       			"frequency": "Hour",
       			"interval": 1
      		}
   		}
	}

Azure Blob을 참조하는 데이터 팩터리 테이블을 정의하는 JSON 요소에 대한 자세한 내용은 [Azure Blob 위치 속성](https://msdn.microsoft.com/library/dn894089.aspx#AzureBlob)을 참조하세요.

### 파이프라인 만들기
다음 샘플 파이프라인에는 Oracle 데이터베이스 테이블에서 Azure 저장소 Blob으로 데이터를 복사하는 복사 작업이 있습니다.

	{
	    "name": "PipelineCopyOracleToBlob",
	    "properties": {
	        "activities": [
	            {
	                "name": "CopyActivity",
	                "description": "copy slices of oracle records to azure blob",
	                "type": "CopyActivity",
	                "inputs": [ { "name": "TableOracle" } ],
	                "outputs": [ { "name": "TableAzureBlob" } ],
	                "transformation": {
	                    "source": {
	                        "type": "OracleSource",
	                        "oracleReaderQuery": "$$Text.Format('select * from LOG where "Timestamp" >= to_date(\'{0:yyyy-MM-dd}\', \'YYYY-MM-DD\') AND "Timestamp" < to_date(\'{1:yyyy-MM-dd}\', \'YYYY-MM-DD\')', SliceStart, SliceEnd)"
	                    },
	                    "sink": {
	                        "type": "BlobSink"
	                    }
	                },
	                "policy": {
	                    "concurrency": 3,
	                    "timeout": "00:05:00"
	                }
	            }
	        ],
	        "start": "2015-03-01T00:00:00Z",
	        "end": "2015-03-15T00:00:00Z",
	        "isPaused": false
	    }
	}

데이터 팩터리 파이프라인을 정의하는 JSON 요소에 대한 자세한 내용은 [파이프라인 JSON 참조](https://msdn.microsoft.com/library/dn834988.aspx)를 참조하고, OracleSource의 속성 및 BlobSink에 대해서는 [지원되는 원본 및 싱크](https://msdn.microsoft.com/library/dn894007.aspx)를 참조하세요.

## 참고 항목

- [Azure 데이터 팩터리를 사용하여 데이터 복사][adf-copyactivity]
- [복사 작업 - JSON 스크립팅 참조](https://msdn.microsoft.com/library/dn835035.aspx)
- [비디오: Azure 데이터 팩터리 복사 작업 소개][copy-activity-video]


[adf-copyactivity]: data-factory-copy-activity.md
[copy-activity-video]: http://azure.microsoft.com/documentation/videos/introducing-azure-data-factory-copy-activity/

<!----HONumber=July15_HO4-->