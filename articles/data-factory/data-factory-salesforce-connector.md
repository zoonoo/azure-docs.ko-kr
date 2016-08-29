<properties
	pageTitle="Data Factory를 사용하여 Salesforce에서 데이터 이동 | Microsoft Azure"
	description="Azure Data Factory를 사용하여 Salesforce에서 데이터를 이동하는 방법에 대해 알아봅니다."
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
	ms.date="07/07/2016"
	ms.author="spelluru"/>

# Azure Data Factory를 사용하여 Salesforce에서 데이터 이동
이 문서에서는 Azure Data Factory의 복사 활동을 사용하여 Salesforce의 데이터를 [지원되는 원본 및 싱크](data-factory-data-movement-activities.md#supported-data-stores) 테이블에 있는 싱크 열에 나열된 데이터 저장소에 복사하는 방법을 설명합니다. 이 문서는 복사 작업 및 지원되는 데이터 저장소 조합을 사용하여 데이터 이동의 일반적인 개요를 보여주는 [데이터 이동 활동](data-factory-data-movement-activities.md) 문서를 작성합니다.

현재 Azure Data Factory는 Salesforce에서 [지원되는 싱크 데이터 저장소]((data-factory-data-movement-activities.md#supported-data-stores)(으)로 데이터를 이동하도록 지원하며 다른 데이터 저장소에서 Salesforce로 데이터를 이동하도록 지원하지 않습니다.

## 필수 조건
- Developer Edition, Professional Edition, Enterprise Edition 또는 Unlimited Edition 중 하나의 Salesforce 버전을 사용해야 합니다.
- API 권한을 사용하도록 설정해야 합니다. [권한 집합에 따라 Salesforce에서 API 액세스를 사용하도록 설정하는 방법](https://www.data2crm.com/migration/faqs/enable-api-access-salesforce-permission-set/)을 참조하세요.
- Salesforce에서 온-프레미스 데이터 저장소로 데이터를 복사하려면 온-프레미스 환경에 데이터 관리 게이트웨이 2.0이 설치되어 있어야 합니다.

## 데이터 복사 마법사
Salesforce의 데이터를 지원되는 싱크 데이터 저장소 중 하나에 복사하는 파이프라인을 만드는 가장 쉬운 방법은 데이터 복사 마법사를 사용하는 것입니다. 데이터 복사 마법사를 사용하여 파이프라인을 만드는 방법에 대한 빠른 연습은 [자습서: 복사 마법사를 사용하여 파이프라인 만들기](data-factory-copy-data-wizard-tutorial.md)를 참조하세요.

다음 예제에서는 [Azure 포털](data-factory-copy-activity-tutorial-using-azure-portal.md), [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) 또는 [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md)을 사용하여 파이프라인을 만드는 데 사용할 수 있는 샘플 JSON 정의를 제공합니다. 이 샘플은 Salesforce에서 Azure Blob 저장소로 데이터를 복사하는 방법을 보여 줍니다. 그러나 Azure Data Factory의 복사 작업을 사용하여 [여기](data-factory-data-movement-activities.md#supported-data-stores) 에 설명한 싱크로 데이터를 복사할 수 있습니다.

## 샘플: Salesforce에서 Azure Blob으로 데이터 복사
이 샘플은 1시간마다 Salesforce의 데이터를 Azure Blob으로 복사합니다. 이 예제에 사용된 JSON 속성은 예제 다음에 나오는 섹션에서 설명합니다. Azure Data Factory의 복사 작업을 사용하여 [데이터 이동 활동](data-factory-data-movement-activities.md#supported-data-stores) 문서에 나열한 싱크로 직접 데이터를 복사할 수 있습니다.

시나리오를 구현하도록 만들어야 하는 데이터 팩터리 아티팩트는 다음과 같습니다. 목록 다음에 나오는 섹션에서는 이러한 단계에 대한 세부 정보를 제공합니다.

- [Salesforce](#salesforce-linked-service-properties) 형식의 연결된 서비스
- [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service-properties) 형식의 연결된 서비스
- [RelationalTable](#salesforce-dataset-properties) 형식의 입력 [데이터 집합](data-factory-create-datasets.md)
- [AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties) 형식의 출력 [데이터 집합](data-factory-create-datasets.md)
- [RelationalSource](#relationalsource-type-properties) 및 [BlobSink](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties)를 사용하는 복사 작업의 [파이프라인](data-factory-create-pipelines.md)

**Salesforce 연결된 서비스**

이 예제에서는 **Salesforce** 연결된 서비스를 사용합니다. 이 연결된 서비스에서 지원하는 속성 목록은 [Salesforce 연결된 서비스](#salesforce-linked-service-properties) 섹션을 참조하세요. 보안 토큰을 재설정하거나 가져오는 방법에 대한 자세한 내용은 [보안 토큰 가져오기](https://help.salesforce.com/apex/HTViewHelpDoc?id=user_security_token.htm)를 참조하세요.

	{
		"name": "SalesforceLinkedService",
		"properties":
		{
			"type": "Salesforce",
			"typeProperties":
			{
				"username": "<user name>",
				"password": "<password>",
				"securityToken": "<security token>"
			}
		}
	}

**Azure 저장소 연결된 서비스**

	{
	  "name": "AzureStorageLinkedService",
	  "properties": {
	    "type": "AzureStorage",
	    "typeProperties": {
	      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
	    }
	  }
	}

**Salesforce 입력 데이터 집합**

	{
		"name": "SalesforceInput",
		"properties": {
			"linkedServiceName": "SalesforceLinkedService",
			"type": "RelationalTable",
			"typeProperties": {
				"tableName": "AllDataType__c"  
			},
			"availability": {
				"frequency": "Hour",
				"interval": 1
			},
			"external": true,
			"policy": {
				"externalData": {
					"retryInterval": "00:01:00",
					"retryTimeout": "00:10:00",
					"maximumRetry": 3
				}
			}
		}
	}

**external**을 **true**로 설정하면 데이터 집합이 Data Factory의 외부에 있으며 Data Factory의 활동에 의해 생성되지 않는다는 사실이 Data Factory 서비스에 전달됩니다.

> [AZURE.IMPORTANT] 모든 사용자 지정 개체에 대해 API 이름에 "\_\_c" 부분이 필요합니다.

![Data Factory - Salesforce 연결 - API 이름](media/data-factory-salesforce-connector/data-factory-salesforce-api-name.png)

**Azure Blob 출력 데이터 집합:**

데이터는 매시간 새 blob에 기록됩니다.(빈도: 1시간, 간격:1회)

	{
	    "name": "AzureBlobOutput",
	    "properties":
	    {
	        "type": "AzureBlob",
	        "linkedServiceName": "AzureStorageLinkedService",
	        "typeProperties":
	        {
	            "folderPath": "adfgetstarted/alltypes_c"
	        },
	        "availability":
	        {
	            "frequency": "Hour",
	            "interval": 1
	        }
	    }
	}


**복사 작업을 포함하는 파이프라인**

파이프라인은 위의 입력 및 출력 데이터 집합을 사용하도록 구성된 복사 작업을 포함하고 매시간 실행하도록 예약됩니다. 파이프라인 JSON 정의에서 **source** 형식은 **RelationalSource**로 설정되고 **sink** 형식은 **BlobSink**로 설정됩니다.

RelationalSource에서 지원하는 속성 목록은 [RelationalSource 형식 속성](#relationalsource-type-properties)을 참조하세요.

	{  
		"name":"SamplePipeline",
		"properties":{  
			"start":"2016-06-01T18:00:00",
			"end":"2016-06-01T19:00:00",
			"description":"pipeline with copy activity",
			"activities":[  
			{
				"name": "SalesforceToAzureBlob",
				"description": "Copy from Salesforce to an Azure blob",
				"type": "Copy",
				"inputs": [
				{
					"name": "SalesforceInput"
				}
				],
				"outputs": [
				{
					"name": "AzureBlobOutput"
				}
				],
				"typeProperties": {
					"source": {
						"type": "RelationalSource",
						"query": "SELECT Id, Col_AutoNumber__c, Col_Checkbox__c, Col_Currency__c, Col_Date__c, Col_DateTime__c, Col_Email__c, Col_Number__c, Col_Percent__c, Col_Phone__c, Col_Picklist__c, Col_Picklist_MultiSelect__c, Col_Text__c, Col_Text_Area__c, Col_Text_AreaLong__c, Col_Text_AreaRich__c, Col_URL__c, Col_Text_Encrypt__c, Col_Lookup__c FROM AllDataType__c"				
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

> [AZURE.IMPORTANT] 모든 사용자 지정 개체에 대해 API 이름에 "\_\_c" 부분이 필요합니다.

![Data Factory - Salesforce 연결 - API 이름](media/data-factory-salesforce-connector/data-factory-salesforce-api-name-2.png)

## Salesforce 연결된 서비스 속성

다음 테이블에서는 Salesforce 연결된 서비스에 지정된 JSON 요소에 대한 설명을 제공합니다.

| 속성 | 설명 | 필수 |
| -------- | ----------- | -------- |
| type | 형식 속성은 **Salesforce**로 설정되어야 합니다. | 예 |
| username |사용자 계정의 사용자 이름을 지정합니다. | 예 |
| password | 사용자 계정으로 password를 지정합니다. | 예 |
| securityToken | 사용자 계정에 대한 보안 토큰을 지정합니다. 보안 토큰을 재설정하거나 가져오는 방법에 대한 자세한 내용은 [보안 토큰 가져오기](https://help.salesforce.com/apex/HTViewHelpDoc?id=user_security_token.htm)를 참조하세요. 일반적으로 보안 토큰에 대해 자세히 알아보려면 [보안 및 API](https://developer.salesforce.com/docs/atlas.en-us.api.meta/api/sforce_api_concepts_security.htm)를 참조하세요. | 예 |

## Salesforce 데이터 집합 속성

데이터 집합 정의에 사용할 수 있는 섹션 및 속성의 전체 목록은 [데이터 집합 만들기](data-factory-create-datasets.md) 문서를 참조하세요. 구조, 가용성 및 JSON 데이터 집합의 정책과 같은 섹션이 모든 데이터 집합 형식에 대해 유사합니다(Azure SQL, Azure blob, Azure 테이블 등).

**typeProperties** 섹션은 데이터 집합의 각 형식에 따라 다르며 데이터 저장소에 있는 데이터의 위치에 대한 정보를 제공합니다. **RelationalTable** 데이터 집합 형식의 데이터 집합에 대한 typeProperties 섹션에는 다음 속성이 있습니다.

| 속성 | 설명 | 필수 |
| -------- | ----------- | -------- |
| tableName | Salesforce에 있는 테이블의 이름입니다. | 아니요(**RelationalSource**의 **쿼리**가 지정된 경우) |

> [AZURE.IMPORTANT]  모든 사용자 지정 개체에 대해 API 이름에 "\_\_c" 부분이 필요합니다.

![Data Factory - Salesforce 연결 - API 이름](media/data-factory-salesforce-connector/data-factory-salesforce-api-name.png)

## RelationalSource 형식 속성
활동 정의에 사용할 수 있는 섹션 및 속성의 전체 목록은 [파이프라인 만들기](data-factory-create-pipelines.md) 문서를 참조하세요. 이름, 설명, 입력과 출력 테이블 및 다양한 정책과 같은 속성은 모든 유형의 활동에 사용할 수 있습니다.

반면 작업의 typeProperties 섹션에서 사용할 수 있는 속성은 각 작업 형식에 따라 다르며 복사 작업의 경우 속성은 원본 및 싱크의 형식에 따라 다릅니다.

원본이 **RelationalSource** 형식인 복사 작업의 경우(Salesforce 포함) typeProperties 섹션에서 다음과 같은 속성을 사용할 수 있습니다.

| 속성 | 설명 | 허용되는 값 | 필수 |
| -------- | ----------- | -------------- | -------- |
| 쿼리 | 사용자 지정 쿼리를 사용하여 데이터를 읽습니다. | SQL-92 쿼리 또는 [SOQL(Salesforce Object Query Language)](https://developer.salesforce.com/docs/atlas.en-us.soql_sosl.meta/soql_sosl/sforce_api_calls_soql.htm) 쿼리입니다. 예: select * from MyTable\_\_c | 아니요(**데이터 집합**의 **tableName**이 지정된 경우) |

> [AZURE.IMPORTANT] 모든 사용자 지정 개체에 대해 API 이름에 "\_\_c" 부분이 필요합니다. <br> DateTime 열에 **where** 절을 포함하는 쿼리를 지정하는 경우 SOQL을 사용합니다. 예: $$Text.Format('SELECT Id, Name, BillingCity FROM Account WHERE LastModifiedDate >= {0:yyyy-MM-ddTHH:mm:ssZ} AND LastModifiedDate < {1:yyyy-MM-ddTHH:mm:ssZ}', WindowStart, WindowEnd), or SQL query e.g. $$Text.Format('SELECT * FROM Account WHERE LastModifiedDate >= {{ts'{0:yyyy-MM-dd HH:mm:ss}'}} AND LastModifiedDate < {{ts'{1:yyyy-MM-dd HH:mm:ss}'}}', WindowStart, WindowEnd).

![Data Factory - Salesforce 연결 - API 이름](media/data-factory-salesforce-connector/data-factory-salesforce-api-name-2.png)

## Salesforce 보고서에서 데이터 검색
{call "<report name>"}(예: "query": "{call "TestReport"}")(으)로 쿼리를 지정하여 Salesforce 보고서에서 데이터를 검색할 수 있습니다.

## Salesforce 요청 제한
Salesforce에는 총 API 요청 수와 동시 API 요청 수에 대한 제한이 있습니다. 자세한 내용은 [Salesforce API 요청 한도](http://resources.docs.salesforce.com/200/20/en-us/sfdc/pdf/salesforce_app_limits_cheatsheet.pdf) 문서에서 "API 요청 한도" 섹션을 참조하세요.

동시 요청 수가 이 제한을 초과하면 제한이 발생하며 임의 오류가 표시됩니다. 총 요청 수가 이 제한을 초과하면 Salesforce 계정은 24시간 동안 차단됩니다. 두 시나리오 모두에서 "REQUEST\_LIMIT\_EXCEEDED" 오류가 나타날 수도 있습니다.


[AZURE.INCLUDE [data-factory-structure-for-rectangualr-datasets](../../includes/data-factory-structure-for-rectangualr-datasets.md)]

### Salesforce에 대한 형식 매핑
Salesforce 형식 | .NET 기반 형식
--------------- | ---------------
자동 번호 | 문자열
확인란 | Boolean
통화 | Double
Date | DateTime
날짜/시간 | DateTime
Email | 문자열
Id | 문자열
관계 조회 | 문자열
다중 선택 선택 목록 | 문자열
Number | Double
백분율 | Double
Phone | 문자열
선택 목록 | 문자열
텍스트 | 문자열
텍스트 영역 | 문자열
텍스트 영역(Long) | 문자열
텍스트 영역(Rich) | 문자열
텍스트(암호화됨) | 문자열
URL | 문자열

[AZURE.INCLUDE [data-factory-column-mapping](../../includes/data-factory-column-mapping.md)]
[AZURE.INCLUDE [data-factory-structure-for-rectangualr-datasets](../../includes/data-factory-structure-for-rectangualr-datasets.md)]

## 성능 및 튜닝  
Azure Data Factory의 데이터 이동(복사 작업) 성능에 영향을 주는 주요 요소 및 최적화하는 다양한 방법에 대해 알아보려면 [복사 작업 성능 및 조정 가이드](data-factory-copy-activity-performance.md)를 참조하세요.

<!---HONumber=AcomDC_0817_2016-->