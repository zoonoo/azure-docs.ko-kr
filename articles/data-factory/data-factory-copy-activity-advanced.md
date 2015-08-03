<properties 
	pageTitle="Azure Data Factory에서 복사 작업을 사용하는 고급 시나리오" 
	description="Azure Data Factory에서 복사 작업을 사용하는 고급 시나리오를 설명합니다." 
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
	ms.date="07/21/2015" 
	ms.author="spelluru"/>

# Azure Data Factory에서 복사 작업을 사용하는 고급 시나리오 
## 개요
파이프라인에서 **복사 작업**을 사용하여 원본에서 배치의 싱크(대상)로 데이터를 복사할 수 있습니다. 이 항목에서는 복사 작업이 지원하는 고급 시나리오를 설명합니다. 복사 작업 및 이를 지원하는 핵심 시나리오의 자세한 개요는 [Azure Data Factory를 사용하여 데이터 복사][adf-copyactivity]를 참조하세요.


## 구조 정의를 사용한 열 필터링
테이블 유형에 따라서는 기본 데이터 원본에 있는 열보다 적은 수의 열을 테이블 정의의 **Structure** 정의에 지정하여 원본에 있는 열의 하위 집합을 지정할 수 있습니다. 다음 표에서는 다양한 테이블 유형의 열 필터링 논리에 대한 정보를 제공합니다.

| 테이블 유형 | 열 필터링 논리 |
|-------------------|----------------------- |
| AzureBlobLocation |JSON 테이블의 Structure 정의는 Blob의 구조와 일치해야 합니다. 열의 하위 집합을 선택하려면 다음 변환 규칙-열 매핑 섹션에서 설명하는 열 매핑 기능을 사용합니다. | 
| AzureSqlTableLocation 및 OnPremisesSqlServerTableLocation | SqlReaderQuery 속성이 복사 작업 정의의 일부로 지정된 경우 테이블의 Structure 정의는 쿼리의 선택한 열과 정렬되어야 합니다. SqlReaderQuery 속성이 지정되지 않은 경우 복사 작업은 테이블 정의의 Structure 정의에서 지정된 열에 따라 SELECT 쿼리를 자동으로 구성합니다. |
| AzureTableLocation | 테이블 정의에서 Structure 섹션에는 기본 Azure 테이블에 있는 전체 집합이나 열의 하위 집합이 포함될 수 있습니다.

## 변환 규칙-열 매핑
열 매핑을 사용하여 원본 테이블의 열이 싱크 테이블의 열에 매핑되는 방법을 지정할 수 있습니다. 다음과 같은 시나리오가 지원됩니다.

- 원본 테이블 "structure"에 이 있는 모든 열을 대상 테이블 "structure"에 매핑합니다.
- 원본 테이블 "structure"에 있는 열의 하위 집합이 모든 대상 테이블 "structure"에 매핑됩니다.

다음은 지원되지 않으면 예외가 throw됩니다.

- 대상에 열이 더 적거나 많음
- 중복 매핑
- SQL 쿼리 결과에 열 이름 없음

특별히, 두 Azure Blob 간에 데이터를 복사 하는 동안 다음과 같은 3개의 시나리오를 충족하지 않는 한 복사 작업은 대부분 직접 이진 데이터 복사로 처리됩니다.


1. 입력 및 출력 테이블의 형식이 다르면 복사 작업은 형식 변환을 수행합니다.
2. 입력된 테이블은 여러 파일을 포함할 수 있는 폴더로 지정되며 출력 테이블은 파일로 지정되는 경우, 복사 활동은 소스 폴더 아래 파일을 하나의 단일 싱크 파일로 병합합니다.
3. "columnMapping"을 지정 하는 경우, 복사 작업이 해당 데이터 변환을 수행합니다.


### 샘플 1 – SQL Server에서 Azure Blob으로의 열 매핑
이 샘플에서는 **입력 테이블**을 다음과 같이 정의합니다. 입력 테이블에는 구조가 있으며 구조에서는 SQL Server 데이터베이스에 있는 SQL 테이블을 가리킵니다.
         
		{
		    "name": "MyOnPremTable",
    		"properties":
    		{
				"structure": 
				[
            		{ "name": "userid", "type": "String"},
            		{ "name": "name", "type": "String"},
            		{ "name": "group", "type": "Decimal"}
				],
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

이 샘플에서는 **출력 테이블**이 다음과 같이 정의됩니다. 출력 테이블에는 구조가 있으며 구조에서는 Azure Blob 저장소에 있는 Blob을 가리킵니다.
         
		
	{
		"name": "MyDemoBlob",
		"properties":
		{
    		"structure":
			[
        	    { "name": "myuserid", "type": "String"},
        	    { "name": "mygroup", "type": "String"},
        	    { "name": "myname", "type": "Decimal"}
			],
			"location":
    		{
    	    	"type": "AzureBlobLocation",
		        "folderPath": "MyContainer/MySubFolder",
				"fileName": "MyBlobName",
    	    	"linkedServiceName": "MyLinkedService",
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

**출력 테이블**의 **fileName**을 지정하지 않는 경우, **folderPath**에 생성되는 파일의 이름은 다음과 같은 형식으로 지정됩니다. Data.<Guid>.txt (예: : Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt.).

**SliceStart** 시간을 기반으로 **folderPath** 및 **fileName**을 설정하려면, **partitionedBy** 속성을 사용합니다. 다음 예제에서 **folderPath**는 SliceStart(처리 중인 조각의 시작 시간)의 Year, Month 및 Day를 사용하고 fileName은 SliceStart의 Hour를 사용합니다. 예를 들어 조각이 2014-10-20T08:00:00에 생성되는 경우 folderName은 wikidatagateway/wikisampledataout/2014/10/20으로 설정되고 fileName은 08.csv로 설정됩니다.

  	"folderPath": "wikidatagateway/wikisampledataout/{Year}/{Month}/{Day}",
    "fileName": "{Hour}.csv",
    "partitionedBy": 
    [
    	{ "name": "Year", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyy" } },
        { "name": "Month", "value": { "type": "DateTime", "date": "SliceStart", "format": "MM" } }, 
        { "name": "Day", "value": { "type": "DateTime", "date": "SliceStart", "format": "dd" } }, 
        { "name": "Hour", "value": { "type": "DateTime", "date": "SliceStart", "format": "hh" } } 
    ],

#### 샘플 – 열 매핑 정의
이 샘플에서는 파이프라인의 작업을 다음과 같이 정의합니다. 원본의 열과 싱크(**columnMappings**)의 열 간 매핑은 **Translator** 속성을 사용하여 이루어집니다.

	{
		"name": "CopyActivity",
		"description": "description", 
		"type": "CopyActivity",
		"inputs":  [ { "name": "MyOnPremTable"  } ],
		"outputs":  [ { "name": "MyDemoBlob" } ],
		"transformation":
		{
			"source":
			{
				"type": "SqlSource"
    		},
			"sink":
			{
            	"type": "BlobSink"
			},
			"Translator": 
			{
      			"type": "TabularTranslator",
      			"ColumnMappings": "UserId: MyUserId, Group: MyGroup, Name: MyName"
    		}
		}
	}

![열 매핑][image-data-factory-column-mapping-1]

### 예제 2 – SQL 쿼리를 사용하여 SQL Server에서 Azure Blob으로 열 매핑
이 샘플에서는 이전 샘플의 테이블이 아니라 SQL 쿼리를 사용하여 온-프레미스 SQL Server에서 데이터를 추출하며 쿼리 결과의 열이 원본 아티팩트에 매핑된 다음 대상 아티팩트 매핑됩니다. 이 샘플의 목적에 맞게 쿼리에서는 5개 열을 반환합니다.

	{
		"name": "CopyActivity",
		"description": "description", 
		"type": "CopyActivity",
		"inputs":  [ { "name": "InputSqlDA"  } ],
		"outputs":  [ { "name": "OutputBlobDA" } ],
		"transformation":
		{
			"source":
			{
				"type": "SqlSource",
				"SqlReaderQuery": "$$Text.Format('SELECT * FROM MyTable WHERE StartDateTime = \'{0:yyyyMMdd-HH}\'', SliceStart)"
			},
			"sink":
			{
            	"type": "BlobSink"
			},
			"Translator": 
			{
      			"type": "TabularTranslator",
      			"ColumnMappings": "UserId: MyUserId, Group: MyGroup,Name: MyName"
    		}
		}
	}

![열 매핑 2][image-data-factory-column-mapping-2]

## 복사 작업에서 데이터 형식 처리

테이블 정의의 Structure 섹션에 지정된 데이터 형식은 **BlobSource**에 대해서만 적용됩니다. 아래 표에서는 다른 유형의 원본 및 싱크에 대해 데이터 형식이 어떻게 처리되는지 설명합니다.

| 원본/싱크 | 데이터 형식 처리 논리 |
| ----------- | ------------------------ |
| SqlSource | 테이블 정의의 Structure 섹션에서 정의된 데이터 형식은 무시됩니다. 기본 SQL 데이터베이스에 정의된 데이터 형식은 복사 작업 중에 데이터 추출에 사용됩니다. |
| SqlSink | 테이블 정의의 Structure 섹션에서 정의된 데이터 형식은 무시됩니다. 기본 원본 및 대상의 데이터 형식이 비교되며, 형식 불일치가 있는 경우 암시적 형식 변환이 수행됩니다. |
| BlobSource | BlobSource에서 BlobSink로 전송하는 경우 형식 변환은 없으며 테이블 정의의 Structure 섹션에서 정의된 데이터 형식은 무시됩니다. BlobSink 이외의 다른 대상의 경우 테이블 정의의 Structure 섹션에서 정의된 데이터 형식이 적용됩니다. Structure가 테이블 정의에서 지정되지 않은 경우 BlobSource 테이블의 format 속성에 따라 형식 처리가 달라집니다. TextFormat: 모든 열 형식은 문자열로 처리되고 모든 열 이름은 "Prop_<0-N>"로 설정됩니다. AvroFormat: Avro 파일에 기본적으로 제공되는 열 형식 및 이름을 사용합니다.
| BlobSink | 테이블 정의의 Structure 섹션에서 정의된 데이터 형식은 무시됩니다. 기본 입력 데이터 저장소에 정의된 데이터 형식이 사용됩니다. Avro 직렬화에 대해서는 열이 Null 허용으로 지정됩니다. |
| AzureTableSource | 테이블 정의의 Structure 섹션에서 정의된 데이터 형식은 무시됩니다. 기본 Azure 테이블에 정의된 데이터 형식이 사용됩니다. |
| AzureTableSink | 테이블 정의의 Structure 섹션에서 정의된 데이터 형식은 무시됩니다. 기본 입력 데이터 저장소에 정의된 데이터 형식이 사용됩니다. |

**참고:** Azure 테이블은 데이터 형식의 제한된 집합만 지원합니다. [테이블 서비스 데이터 모델 이해][azure-table-data-type]를 참조하세요.

## SQL 싱크에 대한 저장 프로시저 호출
SQL Server 또는 Azure SQL 데이터베이스로 데이터를 복사할 때 사용자 지정 저장 프로시저를 구성하고 추가 매개 변수로 호출할 수 있습니다.
### 예
1. 출력 테이블의 JSON 정의를 다음과 같이 정의합니다(Azure SQL 데이터베이스 테이블 예로 설명).

    	{
    		"name": "MyAzureSQLTable",
    		"properties":
    		{
    			"location":
    			{
    				"type": "AzureSqlTableLocation",
    				"tableName": "Marketing",
    				"linkedServiceName": "AzureSqlLinkedService"
    			},
    			"availability":
    			{
    				"frequency": "Hour",
    				"interval": 1
    			}
    		}
    	}

2. 복사 작업 JSON의 **SqlSink** 섹션은 다음과 같이 정의합니다. 데이터를 삽입하는 동안 저장된 프로시저를 호출하려면 **SqlWriterStoredProcedureName** 및 **SqlWriterTableType** 속성이 모두 필요합니다.

		"sink":
	    {
			"type": "SqlSink",
	        "SqlWriterTableType": "MarketingType",
		    "SqlWriterStoredProcedureName": "spOverwriteMarketing",	
			"storedProcedureParameters":
					{
                    	"stringData": 
						{
                        	"value": "str1"		
						}
                    }
	    }

3. 데이터베이스에서 **SqlWriterStoredProcedureName**과 동일한 이름으로 저장된 프로시저를 정의합니다. 지정된 원본에서 입력 데이터를 처리하고 출력 테이블로 삽입합니다. 저장된 프로시저의 매개 변수 이름은 테이블 JSON 파일에 정의된 **tableName**과 동일해야 합니다.

		CREATE PROCEDURE spOverwriteMarketing @Marketing [dbo].[MarketingType] READONLY, @stringData varchar(256)
		AS
		BEGIN
			DELETE FROM [dbo].[Marketing] where ProfileID = @stringData
    		INSERT [dbo].[Marketing](ProfileID, State)
    		SELECT * FROM @Marketing
		END


4. 데이터베이스에서 **SqlWriterTableType**과 동일한 이름으로 테이블 형식을 정의합니다. 테이블 형식의 스키마가 입력 데이터에서 반환된 스키마와 동일해야 합니다.

		CREATE TYPE [dbo].[MarketingType] AS TABLE(
    	    [ProfileID] [varchar](256) NOT NULL,
    	    [State] [varchar](256) NOT NULL,
    	)

저장된 프로시저 기능은 [테이블 값 매개 변수][table-valued-parameters]을 이용합니다.

## 텍스트 파일의 인코딩을 지정합니다.
UTF-8 인코딩을 많이 사용하지만, 기록 이유로 인해 Azure Blob의 시간 텍스트 파일은 다른 인코딩을 따릅니다. **encodingName** 속성을 사용하면 TextFormat 형식의 테이블에 대한 코드 페이지 이름으로 인코딩을 지정할 수 있습니다. 올바른 인코딩 이름 목록은 Encoding.EncodingName 속성을 참조하세요. 예: windows-1250 또는 shift_jis 기본값은 UTF-8입니다. 올바른 인코딩 이름은 [인코딩 클래스](https://msdn.microsoft.com/library/system.text.encoding.aspx)를 참조하세요.

## 참고 항목

- [복사 작업을 사용하는 예][copy-activity-examples]
- [Azure Data Factory를 사용하여 데이터 복사][adf-copyactivity]
- [복사 작업 - JSON 스크립팅 참조](https://msdn.microsoft.com/library/dn835035.aspx)
- [비디오: Azure Data Factory 복사 작업 소개][copy-activity-video]


[copy-activity-video]: http://azure.microsoft.com/documentation/videos/introducing-azure-data-factory-copy-activity/
[table-valued-parameters]: http://msdn.microsoft.com/library/bb675163.aspx


[adfgetstarted]: data-factory-get-started.md
[adf-copyactivity]: data-factory-copy-activity.md
[use-onpremises-datasources]: data-factory-use-onpremises-datasources.md
[copy-activity-examples]: data-factory-copy-activity-examples.md

[json-script-reference]: http://go.microsoft.com/fwlink/?LinkId=516971
[cmdlet-reference]: http://go.microsoft.com/fwlink/?LinkId=517456
[azure-table-data-type]: https://msdn.microsoft.com/ko-kr/library/azure/dd179338.aspx

[image-data-factory-copy-actvity]: ./media/data-factory-copy-activity/VPNTopology.png
[image-data-factory-column-mapping-1]: ./media/data-factory-copy-activity-advanced/ColumnMappingSample1.png
[image-data-factory-column-mapping-2]: ./media/data-factory-copy-activity-advanced/ColumnMappingSample2.png
 

<!---HONumber=July15_HO4-->