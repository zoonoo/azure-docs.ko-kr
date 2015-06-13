<properties 
	pageTitle="Azure 데이터 공장에서 복사 작업을 사용 하는 예" 
	description="녀석은 복사 활동에는 Azure 데이터 팩터리 예제를 제공합니다." 
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
	ms.date="04/14/2015" 
	ms.author="spelluru"/>

# Azure 데이터 공장에서 복사 작업을 사용 하는 예
파이프라인에서 **복사 작업**을 사용하여 원본에서 배치의 싱크(대상)로 데이터를 복사할 수 있습니다. 이 항목 데이터 팩터리 파이프라인에서 복사 활동 사용에 대 한 몇가지 예를 제공 합니다. 복사 작업 및 이를 지원하는 핵심 시나리오의 자세한 개요는 [Azure 데이터 팩터리를 사용하여 데이터 복사][adf-copyactivity]를 참조하세요.

## Azure blob에 온-프레미스 SQL Server 데이터베이스에서 데이터 복사
이 예제에서는 입력 테이블과 출력 테이블을 정의하고 온-프레미스 SQL Server 데이터베이스에서 Azure Blob으로 데이터를 복사하는 파이프라인 내의 복사 작업에서 이 테이블을 사용합니다.

### 가정
이 예제에서는 다음과 같은 Azure 데이터 팩터리 아티팩트를 이미가지고 있다고 가정 합니다.

* **ADF**라는 리소스 그룹
* **CopyFactory**라는 Azure 데이터 팩터리
* 명명 된 데이터 관리 게이트웨이 **mygateway** 만들어지고 온라인 상태입니다.  

### 소스 온-프레미스 SQL Server 데이터베이스에 대 한 연결 된 서비스 만들기
이 단계에서는 명명 된 연결 된 서비스를 만들 **MyOnPremisesSQLDB** 온-프레미스 SQL Server 데이터베이스를 가리키는 합니다.

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

- **유형** 로 설정 된 **OnPremisesSqlLinkedService**.
- **connectionString** SQL Server 데이터베이스에 대 한 연결 문자열로 설정 됩니다. 
- **gatewayName** 의 데이터 관리 게이트웨이 온-프레미스 컴퓨터에 설치 하 고 Azure 데이터 팩터리 서비스 포털에 등록 된 이름으로 설정 됩니다. 

참조 [온-프레미스 SQL 연결 된 서비스](https://msdn.microsoft.com/library/dn893523.aspx) JSON에 대 한 세부 정보에 대 한 온-프레미스 SQL 정의 하기 위한 요소는 서비스를 연결 합니다.
 
### Azure blob 대상에 대 한 연결 된 서비스 만들기
이 단계에서는 명명 된 연결 된 서비스를 만들 **MyAzureStorage** 는 Azure blob 저장소를 가리키는 합니다.

	{
	    "name": "MyAzureStorage",
	    "properties":
	    {
	        "type": "AzureStorageLinkedService",
	        "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>" "
	    }
	}

다음 사항에 유의하세요.

- **유형** 로 설정 된 **AzureStorageLinkedService**.
- **connectionString** -계정 이름을 지정 하 고 계정 Azure 저장소에 대 한 키입니다.

참조 [Azure 저장소 연결 된 서비스](https://msdn.microsoft.com/library/dn893522.aspx) JSON 요소를 사용 하는 Azure 저장소를 정의 하는 방법에 대 한 세부 정보에 대 한 서비스를 연결 합니다.

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

- **유형** 로 설정 된 **OnPremisesSqlServerTableLocation**.
- **tableName** 로 설정 된 **MyTable**, 원본 데이터가 들어 있습니다. 
- **linkedServiceName** 로 설정 된 **MyOnPremisesSQLDB**, 온-프레미스 SQL 데이터베이스에 대해 만든 서비스를 연결 합니다.

참조 [온-프레미스 SQL 위치 속성](https://msdn.microsoft.com/library/dn894089.aspx#OnPremSQL) JSON 요소를 사용 하는 SQL Server 테이블을 참조 하는 데이터 팩터리 테이블을 정의 하는 방법에 대 한 세부 정보에 대 한 합니다.

### 출력 테이블 JSON
출력 테이블을 정의 하는 다음 JSON 스크립트: **MyAzureBlob**, Azure blob를 나타냄: **MyBlob** blob 폴더에서: **MySubFolder** blob 컨테이너에: **MyContainer**.
         
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
 
- **유형** 로 설정 된 **AzureBlobLocation**.
- **folderPath** 로 설정 된 **MyContainer/MySubFolder**, 복사한 데이터를 보유 하는 blob을 포함 하 합니다. 
- **fileName** 로 설정 된 **MyBlob**, 출력 데이터를 보유 하는 blob입니다.
- **linkedServiceName** 로 설정 된 **MyAzureStorge**, Azure 저장소에 대해 만든 서비스를 연결 합니다.    

참조 [Azure blob 위치 속성](https://msdn.microsoft.com/library/dn894089.aspx#AzureBlob) JSON 요소를 사용 하는 Azure blob를 참조 하는 데이터 팩터리 테이블을 정의 하는 방법에 대 한 세부 정보에 대 한 합니다.

### 파이프라인(복사 작업 포함) JSON
이 예제에서는 **CopyActivityPipeline** 파이프라인을 다음 속성으로 정의합니다.

- **type** 속성을 **CopyActivity**로 설정합니다.
- **MyOnPremTable**을 입력으로 지정합니다(**inputs** 태그).
- **MyAzureBlob**을 출력으로 지정합니다(**outputs** 태그).
- **Transformation** 섹션에는 **source** 및 **sink**라는 두 개의 하위 섹션이 있습니다. 원본의 유형을 **SqlSource**로 설정하고 싱크의 유형을 **BlobSink**로 설정합니다. **sqlReaderQuery**는 원본에 대해 수행할 변환(프로젝션)을 정의합니다. 모든 속성에 대한 자세한 내용은[ JSON 스크립트 참조][json-script-reference]를 참조하세요.

         
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

참조 [파이프라인 JSON 참조](https://msdn.microsoft.com/library/dn834988.aspx) JSON 데이터 팩터리 파이프라인을 정의 하는 요소에 대 한 세부 정보에 대 한 및 [지원 원본 및 싱크](https://msdn.microsoft.com/library/dn894007.aspx) SqlSource의 속성에 대 한 (예: **sqlReaderQuery **예제에서) 및 BlobSink. 


## Azure blob에는 온-프레미스 파일 시스템에서 데이터를 복사
Azure Blob에는 온-프레미스 파일 시스템 (Windows/Linux 네트워크 공유 또는 로컬 Windows 호스트)에서 파일을 복사 하려면 복사 작업을 사용할 수 있습니다. 호스트는 Windows 또는 Linux samba 구성 될 수 있습니다. 데이터 관리 게이트웨이 호스트에 연결할 수 있는 Windows 컴퓨터에 설치 되어야 합니다.

### 가정
이 예에서는 다음을 가정합니다.

- **호스트** -파일 시스템을 호스팅하는 서버 이름이: **\contoso**.
- **폴더** -입력된 파일이 들어 있는 폴더의 이름은: **marketingcampaign\regionaldata\ {슬라이스} 2014121112 (2014 년, 12, 월, 일 11 12 시간)와 같은 {슬라이스} 라는 폴더에서 파일 분할 되는 위치입니다. 
### 온-프레미스 파일 시스템 연결 서비스 만들기
JSON 이라는 연결 된 서비스를 생성 하는데 사용할 수는 다음 샘플 **FolderDataStore** 형식의 **OnPremisesFileSystemLinkedService**.

	{
	    "name": "FolderDataStore",
	    "properties": {
	        "type": "OnPremisesFileSystemLinkedService",
	        "host": "\contoso",
	        "userId": "username",
	        "password": "password",
	        "gatewayName": "ContosoGateway"
	    }
	}

> [AZURE.NOTE]이스케이프 문자를 사용 하 여 '의 호스트 및 JSON 파일 폴더 이름에 대 한 합니다. 에 대 한 **\Contoso**, 를 사용 하 여 **\Contoso**.

참조 [온-프레미스 파일 시스템에 대 한 연결 된 서비스](https://msdn.microsoft.com/library/dn930836.aspx) JSON 요소를 사용 하는 온-프레미스 파일 시스템을 정의 하는 방법에 대 한 세부 정보에 대 한 서비스를 연결 합니다.

### Azure blob 대상에 대 한 연결 된 서비스 만들기
JSON 이라는 연결 된 서비스를 생성 하는데 사용할 수는 다음 샘플 **MyAzureStorage** 형식의 **AzureStorageLinkedSerivce**.

	{
	    "name": "MyAzureStorage",
	    "properties":
	    {
	        "type": "AzureStorageLinkedService",
	        "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>" "
	    }
	}

참조 [Azure 저장소 연결 된 서비스](https://msdn.microsoft.com/library/dn893522.aspx) JSON 요소를 사용 하는 Azure 저장소를 정의 하는 방법에 대 한 세부 정보에 대 한 서비스를 연결 합니다.

### 입력된 테이블 만들기
다음 JSON 스크립트 참조 하는 온-프레미스 파일 연결 서비스 앞에서 만든 입력된 테이블을 정의 합니다.

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

참조 [온-프레미스 파일 시스템 위치 속성](https://msdn.microsoft.com/library/dn894089.aspx#OnPremFileSystem) JSON 요소를 사용 하는 온-프레미스 파일 시스템에 참조 하는 데이터 팩터리 테이블을 정의 하는 방법에 대 한 세부 정보에 대 한 합니다.

### 출력 수 만들기
출력 테이블을 정의 하는 다음 JSON 스크립트: **AzureBlobDest**, Azure blob를 나타냄: **MyBlob** blob 폴더에서: **MySubFolder** blob 컨테이너에: **MyContainer**.
         
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

참조 [Azure blob 위치 속성](https://msdn.microsoft.com/library/dn894089.aspx#AzureBlob) JSON 요소를 사용 하는 Azure blob를 참조 하는 데이터 팩터리 테이블을 정의 하는 방법에 대 한 세부 정보에 대 한 합니다.

### 파이프라인 만들기
다음 파이프라인 JSON 파이프라인 대상 Azure blob에 온-프레미스 파일 시스템에서 데이터를 복사 하는 복사 작업을 정의 합니다.
 
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

이 예에서 파이프라인의 내용을 복사 이진으로 없이 구문 분석 하거나 모든 변환을 수행 합니다. 활용할 수 있는 고 지 사항 **동시성** 병렬로 파일의 조각을 복사 합니다. 과거에서 이미 발생 한 조각 이동 하려는 경우에 유용 합니다.

> [AZURE.NOTE]다른 사용자 계정으로 UNC 경로 통해 동일한 호스트를 사용 하 여 동시 복사본 활동 "서버 또는 둘 이상의 사용자 이름을 사용 하 여 동일한 사용자가 공유 리소스에 여러 연결이 허용 되지 않습니다."와 같은 오류가 발생할 수 있습니다. 이것은 보안상의 이유로 운영 체제의 제한 합니다. 다른 게이트웨이 사용 하 여는 복사본 활동 예약 하거나 호스트 내에서 게이트웨이 설치 하 고 UNC 경로 대신 "localhost" 또는 "로컬"을 사용 합니다.

참조 [파이프라인 JSON 참조](https://msdn.microsoft.com/library/dn834988.aspx) JSON 데이터 팩터리 파이프라인을 정의 하는 요소에 대 한 세부 정보에 대 한 및 [지원 원본 및 싱크](https://msdn.microsoft.com/library/dn894007.aspx) FileSystemSource 및 BlobSink의 속성에 대 한 합니다.

### 파일 시스템 테이블을 사용 하는 것에 대 한 추가 시나리오

#### 특정 폴더 아래에 있는 모든 파일을 복사 합니다.
참고만 **folderPath** 샘플 JSON에에서 지정 됩니다.

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
 
#### 특정 폴더 아래에 있는 모든 CSV 파일을 복사 합니다.
이때는 **fileFilter** 로 설정 된 ***.csv**.

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

#### 특정 파일을 복사 합니다.
이때는 **fileFiter** 특정 파일에 설정 된: **201501.csv**.

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

## Azure blob에 온-프레미스 Oracle 데이터베이스에서 데이터 복사
Azure Blob에 온-프레미스 온-프레미스 Oracle 데이터베이스에서 파일을 복사 하려면 복사 작업을 사용할 수 있습니다.

### 온-프레미스 Oracle 데이터베이스에 대 한 연결 된 서비스 만들기
다음 JSON 온-프레미스 Oracle 데이터베이스를 가리키는 연결 된 서비스를 만들 데 사용할 수 있습니다.  **유형** 로 설정 된 **OnPremisesOracleLinkedService**.

	{
	    "name": "OnPremOracleSource",
	    "properties": {
	        "type": "OnPremisesOracleLinkedService",
	        "ConnectionString": "data source=ds;User Id=uid;Password=pwd;",
	        "gatewayName": "SomeGateway"
	    }
	}

참조 [온-프레미스 Oracle 연결 된 서비스](https://msdn.microsoft.com/library/dn948537.aspx) JSON 요소를 사용 하는 온-프레미스 Oracle을 정의 하는 방법에 대 한 세부 정보에 대 한 서비스를 연결 합니다.

### Azure blob 대상에 대 한 연결 된 서비스 만들기
JSON 이라는 연결 된 서비스를 생성 하는데 사용할 수는 다음 샘플 **MyAzureStorage** 형식의 **AzureStorageLinkedSerivce**.

	{
	    "name": "AzureBlobDest",
	    "properties":
	    {
	        "type": "AzureStorageLinkedService",
	        "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>" "
	    }
	}

참조 [Azure 저장소 연결 된 서비스](https://msdn.microsoft.com/library/dn893522.aspx) JSON 요소를 사용 하는 Azure 저장소를 정의 하는 방법에 대 한 세부 정보에 대 한 서비스를 연결 합니다.

### 입력된 테이블 만들기
온-프레미스 Oracle 데이터베이스에서 테이블을 참조 하는 Azure 데이터 팩터리 테이블을 만들려면 다음 샘플 JSON를 사용할 수 있습니다.  **위치 유형** 로 설정 된 **OnPremisesOracleTableLocation**.

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

참조 [온-프레미스 Oracle 위치 속성](https://msdn.microsoft.com/library/dn894089.aspx#Oracle) JSON 요소를 사용 하는 온-프레미스 Oracle 데이터베이스에서 테이블을 참조 하는 데이터 팩터리 테이블을 정의 하는 방법에 대 한 세부 정보에 대 한 합니다.

### 출력 테이블 만들기
출력 테이블을 정의 하는 다음 JSON 스크립트: **MyAzureBlob**, Azure blob를 나타냄: **MyBlob** blob 폴더에서: **MySubFolder** blob 컨테이너에: **MyContainer**.
         
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

참조 [Azure blob 위치 속성](https://msdn.microsoft.com/library/dn894089.aspx#AzureBlob) JSON 요소를 사용 하는 Azure blob를 참조 하는 데이터 팩터리 테이블을 정의 하는 방법에 대 한 세부 정보에 대 한 합니다.

### 파이프라인 만들기
다음 샘플 파이프라인에는 Azure 저장소 blob에 Oracle 데이터베이스 테이블에서 데이터를 복사 하는 복사 작업.

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

참조 [파이프라인 JSON 참조](https://msdn.microsoft.com/library/dn834988.aspx) JSON 데이터 팩터리 파이프라인을 정의 하는 요소에 대 한 세부 정보에 대 한 및 [지원 원본 및 싱크](https://msdn.microsoft.com/library/dn894007.aspx) OracleSource 및 BlobSink의 속성에 대 한 합니다.

## 참고 항목

- [Azure 데이터 팩터리를 사용하여 데이터 복사][adf-copyactivity]
- [복사 작업 - JSON 스크립팅 참조](https://msdn.microsoft.com/library/dn835035.aspx)
- [비디오: Azure 데이터 팩터리 복사 작업 소개][copy-activity-video]


[adf-copyactivity]: data-factory-copy-activity.md
[copy-activity-video]: http://azure.microsoft.com/documentation/videos/introducing-azure-data-factory-copy-activity/

<!---HONumber=GIT-SubDir--> 