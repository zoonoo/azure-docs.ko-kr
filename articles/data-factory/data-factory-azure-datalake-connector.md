<properties
	pageTitle="Azure Data Lake 저장소 간 데이터 이동 | Azure Data Factory"
	description="Azure 데이터 팩터리를 사용하여 Azure 데이터 레이크 저장소 간 데이터를 이동하는 방법에 대해 알아봅니다."
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
	ms.date="03/21/2016"
	ms.author="spelluru"/>

# Azure 데이터 팩터리를 사용하여 Azure 데이터 레이크 저장소 간 데이터 이동
이 문서에서는 Azure 데이터 팩토리에서 복사 작업을 사용하여 다른 데이터 저장소에서 Azure 데이터 레이크 저장소로, 그리고 Azure 데이터 레이크 저장소에서 다른 데이터 스토어로 데이터를 이동하는 방법을 간략하게 설명합니다. 이 문서는 복사 작업 및 지원되는 데이터 저장소 조합을 사용하여 데이터 이동의 일반적인 개요를 보여주는 [데이터 이동 활동](data-factory-data-movement-activities.md) 문서를 작성합니다.

> [AZURE.NOTE]
Azure 데이터 레이크 저장소 간에 데이터를 이동하려면 복사 작업을 사용하여 파이프라인을 만들기 전에 Activity 데이터 레이크 저장소 계정을 만들어야 합니다. Azure 데이터 레이크 저장소에 대해 알아보려면 [Azure 데이터 레이크 저장소 시작](../data-lake-store/data-lake-store-get-started-portal.md)을 참조하세요.
>  
> Data Factory, 연결된 서비스, 데이터 집합 및 파이프라인 만들기를 위한 자세한 단계는 [첫 파이프라인 빌드하기 자습서](data-factory-build-your-first-pipeline.md)를 검토하세요. Data Factory 편집기, Visual Studio 또는 Azure PowerShell에서 JSON 조각을 사용하여 Data Factory 엔터티를 만듭니다.

다음 샘플은 Azure 데이터 레이크 저장소 및 Azure Blob 저장소 간에 데이터를 복사하는 방법을 보여 줍니다. 그러나 Azure 데이터 팩터리의 복사 작업을 사용하여 임의의 원본에서 [여기](data-factory-data-movement-activities.md#supported-data-stores)에 설명한 싱크로 **직접** 데이터를 복사할 수 있습니다.


## 샘플: Azure Blob에서 Azure 데이터 레이크 저장소로 데이터 복사
아래 샘플은 다음을 보여줍니다.

1.	[AzureStorage](#azure-storage-linked-service-properties) 형식의 연결된 서비스입니다.
2.	[AzureDataLakeStore](#azure-data-lake-linked-service-properties) 형식의 연결된 서비스입니다.
3.	[AzureBlob](#azure-blob-dataset-type-properties) 형식의 입력 [데이터 집합](data-factory-create-datasets.md)입니다.
4.	[AzureDataLakeStore](#azure-data-lake-dataset-type-properties) 형식의 출력 [데이터 집합](data-factory-create-datasets.md)입니다.
4.	[BlobSource](#azure-blob-copy-activity-type-properties) 및 [AzureDataLakeStoreSink](#azure-data-lake-copy-activity-type-properties)를 사용하는 복사 작업의 [파이프라인](data-factory-create-pipelines.md)입니다.

이 샘플은 Azure Blob 저장소에서 Azure 데이터 레이크 저장소로 매시간 시계열에 속한 데이터를 복사합니다. 이 샘플에 사용된 JSON 속성은 샘플 다음에 나오는 섹션에서 설명합니다.


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

**Azure 데이터 레이크 연결된 서비스:**

	{
	    "name": "AzureDataLakeStoreLinkedService",
	    "properties": {
	        "type": "AzureDataLakeStore",
	        "typeProperties": {
	            "dataLakeStoreUri": "https://<accountname>.azuredatalakestore.net/webhdfs/v1",
				"sessionId": "<session ID>",
	            "authorization": "<authorization URL>"
	        }
	    }
	}

### 데이터 팩터리 편집기를 사용하여 Azure 데이터 레이크 연결된 서비스를 만들려면
다음 절차에서는 데이터 팩터리 편집기를 사용하여 Azure 데이터 레이크 연결된 서비스를 만드는 단계를 제공합니다.

1. 명령 모음에서 **새 데이터 저장소**를 클릭하고 **Azure 데이터 레이크 저장소**를 선택합니다.
2. JSON 편집기에서 **dataLakeStoreUri** 속성에 Data Lake의 URI를 입력합니다.
3. 명령 모음에서 **권한 부여** 단추를 클릭합니다. 팝업 창이 표시됩니다.

	![권한 부여 단추](./media/data-factory-azure-data-lake-connector/authorize-button.png)

4. 이제 자격 증명을 사용하여 로그인하면 JSON의 **authorization** 속성에 값이 할당됩니다.
5. (선택 사항) JSON에서 **accountName**, **subscriptionID** 및 **resourceGroupName**과 같은 선택적 매개 변수의 값을 지정하거나 이러한 속성을 JSON에서 삭제합니다.
6. 명령 모음에서 **배포**를 클릭하여 연결된 서비스를 배포합니다.

> [AZURE.IMPORTANT] **권한 부여** 단추를 사용하여 생성된 권한 부여 코드는 잠시 후 만료됩니다. **토큰이 만료**되면 **권한 부여** 단추를 사용하여 **다시 인증**하고 연결된 서비스를 다시 배포해야 합니다. 자세한 내용은 [Azure 데이터 레이크 저장소 연결된 서비스](#azure-data-lake-store-linked-service-properties) 섹션을 참조하세요.



**Azure Blob 입력 데이터 집합:**

데이터는 매시간 새 blob에 선택됩니다.(빈도: 1시간, 간격:1회) Blob에 대한 폴더 경로 및 파일 이름은 처리 중인 조각의 시작 시간에 기반하여 동적으로 평가됩니다. 폴더 경로는 연도, 월 및 일 일부 시작 시간을 사용하고 파일 이름은 시작 시간의 시간 부분을 사용합니다. "external": "true" 설정은 데이터 팩터리 서비스에 이 테이블이 데이터 팩터리의 외부에 있으며 데이터 팩터리의 작업에 의해 생성되지 않는다는 점을 알려줍니다.

	{
	  "name": "AzureBlobInput",
	  "properties": {
	    "type": "AzureBlob",
	    "linkedServiceName": "StorageLinkedService",
	    "typeProperties": {
	      "Path": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}",
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


**Azure 데이터 레이크 출력 데이터 집합:**

이 샘플은 Azure 데이터 레이크 저장소로 데이터를 복사합니다. 새 데이터가 데이터 레이크 저장소로 매시간 복사됩니다.

	{
		"name": "AzureDataLakeStoreOutput",
	  	"properties": {
			"type": "AzureDataLakeStore",
		    "linkedServiceName": "AzureDataLakeStoreLinkedService",
		    "typeProperties": {
				"folderPath": "datalake/output/"
		    },
	    	"availability": {
	      		"frequency": "Hour",
	      		"interval": 1
	    	}
	  	}
	}



**복사 작업을 포함하는 파이프라인:**

파이프라인은 위의 입력 및 출력 데이터 집합을 사용하도록 구성된 복사 작업을 포함하고 매시간 실행하도록 예약됩니다. 파이프라인 JSON 정의에서 **source** 형식은 **BlobSource**로 설정되고 **sink** 형식은 **AzureDataLakeStoreSink**로 설정됩니다.

	{  
	    "name":"SamplePipeline",
	    "properties":
		{  
	    	"start":"2014-06-01T18:00:00",
	    	"end":"2014-06-01T19:00:00",
	    	"description":"pipeline with copy activity",
	    	"activities":
			[  
	      		{
	        		"name": "AzureBlobtoDataLake",
		        	"description": "Copy Activity",
		        	"type": "Copy",
		        	"inputs": [
		          	{
			            "name": "AzureBlobInput"
		          	}
		        	],
		        	"outputs": [
		          	{
			            "name": "AzureDataLakeStoreOutput"
		          	}
		        	],
		        	"typeProperties": {
			        	"source": {
		            		"type": "BlobSource",
			    			"treatEmptyAsNull": true,
		            		"blobColumnSeparators": ","
		          		},
		          		"sink": {
		            		"type": "AzureDataLakeStoreSink"
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

## 샘플: Azure 데이터 레이크 저장소에서 Azure Blob로 데이터 복사
아래 샘플은 다음을 보여줍니다.

1.	[AzureDataLakeStore](#azure-data-lake-linked-service-properties) 형식의 연결된 서비스입니다.
2.	[AzureStorage](#azure-storage-linked-service-properties) 형식의 연결된 서비스입니다.
3.	[AzureDataLakeStore](#azure-data-lake-dataset-type-properties) 형식의 입력 [데이터 집합](data-factory-create-datasets.md)입니다.
4.	[AzureBlob](#azure-blob-dataset-type-properties) 형식의 출력 [데이터 집합](data-factory-create-datasets.md)입니다.
5.	[AzureDataLakeStoreSource](#azure-data-lake-copy-activity-type-properties) 및 [BlobSink](#azure-blob-copy-activity-type-properties)를 사용하는 복사 작업의 [파이프라인](data-factory-create-pipelines.md)입니다.

이 샘플은 Azure 데이터 레이크 저장소에서 Azure Blob로 매시간 시계열에 속한 데이터를 복사합니다. 이 샘플에 사용된 JSON 속성은 샘플 다음에 나오는 섹션에서 설명합니다.

**Azure 데이터 레이크 저장소 연결된 서비스:**

	{
	    "name": "AzureDataLakeStoreLinkedService",
	    "properties": {
	        "type": "AzureDataLakeStore",
	        "typeProperties": {
	            "dataLakeStoreUri": "https://<accountname>.azuredatalakestore.net/webhdfs/v1",
				"sessionId": "<session ID>",
	            "authorization": "<authorization URL>"
	        }
	    }
	}

> [AZURE.NOTE] authorization URL을 가져오려면 이전 샘플의 단계를 참조하세요.

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

**Azure 데이터 레이크 입력 데이터 집합:**

**"external": true** 설정 및 **externalData** 정책 지정은 Azure Data Factory 서비스가 테이블이 데이터 팩터리의 외부에 있으며 데이터 공장의 활동에 의해 생성되지 않는다는 점을 알립니다.

	{
		"name": "AzureDataLakeStoreInput",
	  	"properties":
		{
	    	"type": "AzureDataLakeStore",
	    	"linkedServiceName": "AzureDataLakeStoreLinkedService",
		    "typeProperties": {
				"folderPath": "datalake/input/",
            	"fileName": "SearchLog.tsv",
            	"format": {
                	"type": "TextFormat",
	                "rowDelimiter": "\n",
    	            "columnDelimiter": "\t"
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

**복사 작업을 포함하는 파이프라인:**

파이프라인은 위의 입력 및 출력 데이터 집합을 사용하도록 구성된 복사 작업을 포함하고 매시간 실행하도록 예약됩니다. 파이프라인 JSON 정의에서 **source** 형식은 **AzureDataLakeStoreSource**로 설정되고 **sink** 형식은 **BlobSink**로 설정됩니다.


	{  
	    "name":"SamplePipeline",
	    "properties":{  
	    	"start":"2014-06-01T18:00:00",
	    	"end":"2014-06-01T19:00:00",
	    	"description":"pipeline for copy activity",
	    	"activities":[  
	      		{
	        		"name": "AzureDakeLaketoBlob",
		    	    "description": "copy activity",
		    	    "type": "Copy",
		    	    "inputs": [
		    	      {
		    	        "name": "AzureDataLakeStoreInput"
		    	      }
		    	    ],
		    	    "outputs": [
		    	      {
		    	        "name": "AzureBlobOutput"
		    	      }
		    	    ],
		    	    "typeProperties": {
		    	    	"source": {
		            		"type": "AzureDataLakeStoreSource",
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


## Azure 데이터 레이크 저장소 연결된 서비스 속성

Azure 저장소 연결된 서비스를 사용하여 Azure 저장소 계정을 Azure Data Factory에 연결할 수 있습니다. 다음 테이블은 Azure 저장소 연결된 서비스에 특정된 JSON 요소에 대한 설명을 제공합니다.

| 속성 | 설명 | 필수 |
| :-------- | :----------- | :-------- |
| type | type 속성은 **AzureDataLakeStore**로 설정되어야 합니다. | 예 |
| dataLakeStoreUri | Azure 데이터 레이크 저장소 계정에 대한 정보를 지정합니다. https://<Azure Data Lake account name>.azuredatalakestore.net/webhdfs/v1 형식입니다. | 예 |
| authorization | **데이터 팩터리 편집기**에서 **권한 부여** 단추를 클릭하고 자격 증명을 입력합니다. 그러면 자동 생성된 authorization URL이 이 속성에 할당됩니다. | 예 |
| sessionid | oauth authorization 세션에서 가져온 OAuth 세션 ID입니다. 각 세션 ID는 고유하며, 한 번만 사용할 수 있습니다. 데이터 팩터리 편집기를 사용하는 경우 자동으로 생성됩니다. | 예 |  
| accountName | 데이터 레이크 계정 이름 | 아니요 |
| subscriptionId | Azure 구독 ID | 아니요(지정하지 않으면 데이터 팩터리의 구독이 사용됨) |
| resourceGroupName | Azure 리소스 그룹 이름 | 아니요(지정하지 않으면 Data Factory의 리소스 그룹이 사용됨). |

## 토큰 만료 
**권한 부여** 단추를 사용하여 생성한 권한 부여 코드는 잠시 후 만료됩니다. 다양한 유형의 사용자 계정에 대한 만료 시간은 다음 표를 참조하세요. 인증 **토큰이 만료**되는 경우 다음과 같은 오류 메시지가 표시될 수 있습니다. "자격 증명 작업 오류: invalid\_grant - AADSTS70002: 자격 증명의 유효성 검사 오류 AADSTS70008: 제공된 액세스 권한 부여가 만료되었거나 해지됩니다. 추적 ID: d18629e8-af88-43c5-88e3-d8419eb1fca1 상관관계 ID: fac30a0c-6be6-4e02-8d69-a776d2ffefd7 타임스탬프: 2015-12-15 21-09-31Z".


| 사용자 유형 | 다음 시간 후에 만료 |
| :-------- | :----------- | 
| Azure Active Directory에서 관리되지 않는 사용자 계정(@hotmail.com, @live.com 등) | 12시간 |
| AAD(Azure Active Directory)에서 관리되는 사용자 계정 | 마지막 조각이 실행된 후 14일 <br/><br/>OAuth 기반 연결된 서비스를 기반으로 하는 조각이 14일마다 한 번 이상 실행된 경우 90일 |

토큰 만료 시간 전에 암호를 변경하면, 토큰이 즉시 만료되고 위와 같은 오류 메시지가 표시됩니다.

이 오류를 방지/해결하려면 **토큰이 만료**되면 **권한 부여** 단추를 사용하여 다시 인증하고 연결된 서비스를 다시 배포해야 합니다. 다음 섹션의 코드를 사용하여 프로그래밍 방식으로 **sessionId** 및 **권한 부여** 속성의 값을 생성할 수도 있습니다.

### 프로그래밍 방식으로 sessionId와 권한 부여 값을 생성하려면 

    if (linkedService.Properties.TypeProperties is AzureDataLakeStoreLinkedService ||
        linkedService.Properties.TypeProperties is AzureDataLakeAnalyticsLinkedService)
    {
        AuthorizationSessionGetResponse authorizationSession = this.Client.OAuth.Get(this.ResourceGroupName, this.DataFactoryName, linkedService.Properties.Type);

        WindowsFormsWebAuthenticationDialog authenticationDialog = new WindowsFormsWebAuthenticationDialog(null);
        string authorization = authenticationDialog.AuthenticateAAD(authorizationSession.AuthorizationSession.Endpoint, new Uri("urn:ietf:wg:oauth:2.0:oob"));

        AzureDataLakeStoreLinkedService azureDataLakeStoreProperties = linkedService.Properties.TypeProperties as AzureDataLakeStoreLinkedService;
        if (azureDataLakeStoreProperties != null)
        {
            azureDataLakeStoreProperties.SessionId = authorizationSession.AuthorizationSession.SessionId;
            azureDataLakeStoreProperties.Authorization = authorization;
        }

        AzureDataLakeAnalyticsLinkedService azureDataLakeAnalyticsProperties = linkedService.Properties.TypeProperties as AzureDataLakeAnalyticsLinkedService;
        if (azureDataLakeAnalyticsProperties != null)
        {
            azureDataLakeAnalyticsProperties.SessionId = authorizationSession.AuthorizationSession.SessionId;
            azureDataLakeAnalyticsProperties.Authorization = authorization;
        }
    }

코드에 사용되는 데이터 팩터리 클래스에 대한 세부 정보는 [AzureDataLakeStoreLinkedService 클래스](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakestorelinkedservice.aspx), [AzureDataLakeAnalyticsLinkedService 클래스](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakeanalyticslinkedservice.aspx) 및 [AuthorizationSessionGetResponse 클래스](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.authorizationsessiongetresponse.aspx) 항목을 참조하세요. WindowsFormsWebAuthenticationDialog 클래스의 Microsoft.IdentityModel.Clients.ActiveDirectory.WindowsForms.dll에 대한 참조를 추가해야 합니다.
 

## Azure 데이터 레이크 데이터 집합 형식 속성

데이터 집합 정의에 사용할 수 있는 JSON 섹션 및 속성의 전체 목록은 [데이터 집합 만들기](data-factory-create-datasets.md) 문서를 참조하세요. 구조, 가용성 및 JSON 데이터 집합의 정책과 같은 섹션이 모든 데이터 집합 형식에 대해 유사합니다.(SQL Azure, Azure Blob, Azure 테이블 등)

**typeProperties** 섹션은 데이터 집합의 각 형식에 따라 다르며 데이터 저장소에 있는 데이터의 위치, 서식 등에 대한 정보를 제공합니다. **AzureDataLakeStore** 데이터 집합 형식의 데이터 집합에 대한 typeProperties 섹션에는 다음 속성이 있습니다.

| 속성 | 설명 | 필수 |
| :-------- | :----------- | :-------- |
| folderPath | Azure 데이터 레이크 저장소의 컨테이너 및 폴더에 대한 경로입니다. | 예 |
| fileName | Azure Data Lake 저장소에 있는 파일의 이름입니다. fileName은 선택 사항이며 대/소문자를 구분합니다. <br/><br/>filename을 지정하면 활동(복사 포함)이 특정 파일에서 작동합니다.<br/><br/> fileName이 지정되지 않으면 복사는 입력 데이터 집합에 대한 folderPath의 모든 파일을 포함합니다.<br/><br/>fileName이 출력 데이터 집합에 대해 지정되지 않으면 생성된 파일의 이름이 Data.<Guid>.txt(예: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt) 형식으로 표시됩니다. | 아니요 |
| partitionedBy | partitionedBy는 선택적 속성입니다. 동적 folderPath 및 시계열 데이터에 대한 filename을 지정하는 데 사용할 수 있습니다. 예를 들어 folderPath는 매시간 데이터에 대한 매개 변수화됩니다. 자세한 내용과 예제는 아래 partitionedBy 속성 활용 섹션을 참조하세요. | 아니요 |
| format | **TextFormat**, **AvroFormat**, **JsonFormat** 및 **OrcFormat**과 같은 서식 유형이 지원됩니다. 이 중 하나로 서식에서 **type** 속성을 설정해야 합니다. 서식이 TextFormat인 경우 형식에 선택적 추가 속성을 지정할 수 있습니다. 세부 정보는 [TextFormat 지정](#specifying-textformat), [AvroFormat 지정](#specifying-avroformat), [JsonFormat 지정](#specifying-jsonformat), [OrcFormat 지정](#specifying-orcformat) 섹션을 참조하세요. | 아니요
| 압축 | 데이터에 대한 압축 유형 및 수준을 지정합니다. 지원되는 형식은 **GZip**, **Deflate** 및 **BZip2**이고 지원되는 수준은 **최적** 및 **가장 빠름**입니다. 현재 **AvroFormat** 또는 **OrcFormat**의 데이터에 대한 압축 설정은 지원되지 않습니다. 자세한 내용은 [압축 지원](#compression-support) 섹션을 참조하세요. | 아니요 |

### partitionedBy 속성 활용
위에서 설명한 것처럼 **partitionedBy** 섹션, 데이터 팩터리 매크로 및 시스템 변수를 사용하여 동적 folderPath 및 시계열 데이터와 파일 이름을 지정할 수 있습니다. 지정된 데이터 조각에 대한 시작 및 종료 시간을 나타내는 SliceStart 및 SliceEnd입니다.

시간 시계열 데이터 집합, 예약 및 조각에 대한 자세한 내용을 이해하려면 [데이터 집합 만들기](data-factory-create-datasets.md) 및 [일정 예약 및 실행](data-factory-scheduling-and-execution.md) 문서를 참조하세요.

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

[AZURE.INCLUDE [data-factory-file-format](../../includes/data-factory-file-format.md)]
 

### 압축 지원  
큰 데이터 집합을 처리하면 I/O 및 네트워크 병목 현상이 발생할 수 있습니다. 따라서 저장소의 압축된 데이터는 네트워크를 통해 데이터 전송의 속도를 높이고 디스크 공간을 절약할 수 없을 뿐만 아니라 빅 데이터 처리에서 상당한 성능 개선을 가져올 수 없습니다. 이 때 압축은 Azure Blob 또는 온-프레미스 파일 시스템과 같은 파일 기반 데이터 저장소에 대해 지원됩니다.

데이터 집합에 대한 압축을 지정하려면 다음 예제와 같이 데이터 집합 JSON의 **압축** 속성을 사용합니다.

	{  
		"name": "AzureDatalakeStoreDataSet",  
	  	"properties": {  
	    	"availability": {  
	    		"frequency": "Day",  
	    	  	"interval": 1  
	    	},  
	    	"type": "AzureDatalakeStore",  
	    	"linkedServiceName": "DataLakeStoreLinkedService",  
	    	"typeProperties": {  
	    		"fileName": "pagecounts.csv.gz",  
	    	  	"folderPath": "compression/file/",  
	    	  	"compression": {  
	    	    	"type": "GZip",  
	    	    	"level": "Optimal"  
	    	  	}  
    		}  
	  	}  
	}  
 
**압축** 섹션에는 두 가지 속성이 있습니다.
  
- **유형:** **GZIP**, **Deflate** 또는 **BZIP2**가 될 수 있는 압축 코덱  
- **수준:** **최적** 또는 **가장 빠름**이 될 수 있는 압축 비율 
	- **가장 빠름:** 결과 파일이 최적으로 압축되지 않은 경우에도 압축 작업을 최대한 빨리 완료해야 합니다. 
	- **최적**: 작업이 완료되는데 시간이 오래 걸리더라도 압축 작업이 최적으로 압축되어야 합니다. 
	
	자세한 내용은 [압축 수준](https://msdn.microsoft.com/library/system.io.compression.compressionlevel.aspx) 항목을 참조하세요.

위의 샘플 데이터 집합이 복사 작업의 출력으로 사용된다고 가정하면 복사 작업은 최적의 비율을 사용하여 GZIP 코덱으로 출력 데이터를 압축한 다음 압축된 데이터를 pagecounts.csv.gz라는 이름의 파일로 Azure 데이터 레이크 저장소에 작성합니다.

입력 데이터 집합 JSON에 압축 속성을 지정하는 경우 파이프라인은 원본에서 압축된 데이터를 읽을 수 있고 출력 데이터 집합 JSON 에서 속성을 지정하는 경우 복사 작업은 대상에 압축된 데이터를 작성할 수 있습니다. 다음은 몇 가지 샘플 시나리오입니다.

- Azure 데이터 레이크 저장소에서 GZIP 압축 데이터를 읽고 압축을 풀고 Azure SQL 데이터베이스에 결과 데이터를 작성합니다. 이 경우 압축 JSON 속성으로 입력 Azure 데이터 레이크 저장소 데이터 집합을 정의합니다. 
- 온-프레미스 파일 시스템에서 일반 텍스트 파일에서 데이터를 읽고 GZip 형식을 사용하여 압축하고 Azure 데이터 레이크 저장소에 압축된 데이터를 작성합니다. 이 경우 압축 JSON 속성으로 출력 Azure 데이터 레이크 데이터 집합을 정의합니다.  
- Azure 데이터 레이크 저장소에서 GZIP 압축 데이터를 읽고 압축을 풀고 BZIP2를 사용하여 압축하고 Azure 데이터 레이크 저장소에 결과 데이터를 작성합니다. 이 경우 GZIP으로 설정된 압축 유형으로 입력 Azure 데이터 레이크 저장소 데이터 집합을 정의하고 BZIP2로 설정된 압축 유형으로 출력 데이터 집합을 정의합니다.   


## Azure 데이터 레이크 복사 작업 형식 속성  
활동 정의에 사용할 수 있는 섹션 및 속성의 전체 목록은 [파이프라인 만들기](data-factory-create-pipelines.md) 문서를 참조하세요. 이름, 설명, 입력 및 출력 테이블, 다양한 정책 등과 같은 속성은 모든 유형의 활동에 사용할 수 있습니다.

반면 작업의 typeProperties 섹션에서 사용할 수 있는 속성은 각 작업 형식에 따라 다르며 복사 작업의 경우 속성은 원본 및 싱크의 형식에 따라 다릅니다

**AzureDataLakeStoreSource**는 **typeProperties** 섹션에서 다음 속성을 지원합니다.

| 속성 | 설명 | 허용되는 값 | 필수 |
| -------- | ----------- | -------------- | -------- |
| recursive | 하위 폴더에서 또는 지정된 폴더에서만 데이터를 재귀적으로 읽을지 여부를 나타냅니다. | True(기본값), False | 아니요 |



**AzureDataLakeStoreSink**는 **typeProperties** 섹션에서 다음 속성을 지원합니다.

| 속성 | 설명 | 허용되는 값 | 필수 |
| -------- | ----------- | -------------- | -------- |
| copyBehavior | 복사 동작을 지정합니다. | **PreserveHierarchy:** 대상 폴더의 파일 계층 구조를 유지합니다. 즉, 원본 폴더에 대한 원본 파일의 상대 경로가 대상 폴더에 대한 대상 파일의 상대 경로와 동일합니다.<br/><br/>**FlattenHierarchy:** 원본 폴더의 모든 파일이 대상 폴더의 첫 번째 수준이 됩니다. 대상 파일은 자동 생성된 이름을 갖습니다.<br/><br/>**MergeFiles:** 원본 폴더의 모든 파일을 하나의 파일로 병합합니다. 파일/Blob 이름이 지정된 경우 지정된 이름이 병합된 파일 이름이 됩니다. 그렇지 않으면 자동 생성된 파일 이름이 병합된 파일 이름이 됩니다. | 아니요 |


[AZURE.INCLUDE [data-factory-structure-for-rectangualr-datasets](../../includes/data-factory-structure-for-rectangualr-datasets.md)]

[AZURE.INCLUDE [data-factory-type-conversion-sample](../../includes/data-factory-type-conversion-sample.md)]

[AZURE.INCLUDE [data-factory-column-mapping](../../includes/data-factory-column-mapping.md)]

## 성능 및 튜닝  
Azure Data Factory의 데이터 이동(복사 작업) 성능에 영향을 주는 주요 요소 및 최적화하는 다양한 방법에 대해 알아보려면 [복사 작업 성능 및 조정 가이드](data-factory-copy-activity-performance.md)를 참조하세요.

<!---HONumber=AcomDC_0525_2016-->