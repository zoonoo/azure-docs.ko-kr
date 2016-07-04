<properties 
	pageTitle="DB2에서 데이터 이동 | Azure 데이터 팩터리" 
	description="Azure 데이터 팩터리를 사용하여 DB2 데이터베이스에서 데이터를 이동하는 방법에 대해 알아봅니다." 
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
	ms.date="06/16/2016" 
	ms.author="spelluru"/>

# Azure 데이터 팩터리를 사용하여 DB2에서 데이터 이동
이 문서는 Azure 데이터 팩토리에서 복사 활동을 사용하여 DB2에서 다른 데이터 저장소로 데이터를 이동하는 방법에 대해 설명합니다. 이 문서는 복사 작업 및 지원되는 데이터 저장소 조합을 사용하여 데이터 이동의 일반적인 개요를 보여주는 [데이터 이동 활동](data-factory-data-movement-activities.md) 문서를 작성합니다.

데이터 팩터리는 데이터 관리 게이트웨이를 사용하여 온-프레미스 DB2 원본에 연결을 지원합니다. 데이터 관리 게이트웨이 및 게이트웨이 설정에 대한 단계별 지침을 알아보려면 [온-프레미스 위치 및 클라우드 간 데이터 이동](data-factory-move-data-between-onprem-and-cloud.md) 문서를 참조하십시오.

**참고:**Azure IaaS VM에서 호스팅되는 경우 DB2에 연결하려면 게이트웨이를 활용해야 합니다. 또한 클라우드에서 호스트되는 DB2의 인스턴스에 연결하려고 하는 경우 IaaS VM에 게이트웨이 인스턴스를 설치할 수 있습니다.

현재 데이터 팩터리는 다른 데이터 저장소에서 DB2로가 아닌 DB2에서 다른 데이터 저장소로 데이터 이동만을 지원합니다.

## 설치 

DB2 데이터베이스에 연결할 데이터 관리 게이트웨이의 경우 데이터 관리 게이트웨이와 동일한 시스템에 [IBM DB2 데이터 서버 드라이버](http://go.microsoft.com/fwlink/p/?LinkID=274911)를 설치해야 합니다.

추가 설치 단계가 필요한 Windows 8의 경우 IBM DB2 데이터 서버 드라이버를 설치에 대해 IBM에서 보고한 문제가 있습니다. Windows 8의 IBM DB2 데이터 서버 드라이버에 대한 자세한 내용은 [http://www-01.ibm.com/support/docview.wss?uid=swg21618434](http://www-01.ibm.com/support/docview.wss?uid=swg21618434)를 참조하십시오.

> [AZURE.NOTE] 연결/게이트웨이 관련 문제 해결에 대한 팁은 [게이트웨이 문제 해결](data-factory-move-data-between-onprem-and-cloud.md#gateway-troubleshooting)을 참조하세요.


## 샘플: DB2에서 Azure Blob로 데이터 복사

이 샘플은 온-프레미스 DB2 데이터베이스에서 Azure Blob 저장소로 데이터를 복사하는 방법을 보여 줍니다. 그러나 Azure 데이터 팩터리의 복사 작업을 사용하여 [여기](data-factory-data-movement-activities.md#supported-data-stores)에 설명한 싱크로 **직접** 데이터를 복사할 수 있습니다.
 
이 샘플에는 다음 데이터 팩터리 엔터티가 있습니다.

1.	[OnPremisesDb2](data-factory-onprem-db2-connector.md#db2-linked-service-properties) 형식의 연결된 서비스
2.	[AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service-properties) 형식의 연결된 서비스 
3.	[RelationalTable](data-factory-onprem-db2-connector.md#db2-dataset-type-properties) 형식의 입력 [데이터 집합](data-factory-create-datasets.md)
4.	[AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties) 형식의 출력 [데이터 집합](data-factory-create-datasets.md) 
5.	[RelationalSource](data-factory-onprem-db2-connector.md#db2-copy-activity-type-properties) 및 [BlobSink](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties)를 사용하는 복사 작업의 [파이프라인](data-factory-create-pipelines.md) 

샘플은 DB2 데이터베이스의 쿼리 결과에서 blob에 매시간 데이터를 복사합니다. 이 샘플에 사용된 JSON 속성은 샘플 다음에 나오는 섹션에서 설명합니다.

첫 번째 단계로 [온-프레미스 위치 및 클라우드 간 데이터 이동](data-factory-move-data-between-onprem-and-cloud.md) 문서의 지침에 따라 데이터 관리 게이트웨이를 설정합니다.

**DB2 연결된 서비스:**

	{
	    "name": "OnPremDb2LinkedService",
	    "properties": {
	        "type": "OnPremisesDb2",
	        "typeProperties": {
	            "server": "<server>",
	            "database": "<database>",
	            "schema": "<schema>",
	            "authenticationType": "<authentication type>",
	            "username": "<username>",
	            "password": "<password>",
	            "gatewayName": "<gatewayName>"
	        }
	    }
	}


**Azure Blob 저장소 연결된 서비스:**

	{
	    "name": "AzureStorageLinkedService",
	    "properties": {
	        "type": "AzureStorageLinkedService",
			"typeProperties": {
	        	"connectionString": "DefaultEndpointsProtocol=https;AccountName=<AccountName>;AccountKey=<AccountKey>"
			}
	    }
	}

**입력된 데이터 집합 DB2:**

샘플은 DB2에서 만든 테이블 "MyTable"에 시계열 데이터에 대한 "timestamp" 라는 열이 포함되었다고 가정합니다.

"외부":true 설정 및 externalData 정책 지정은 테이블인 데이터 팩터리가 데이터 팩터리의 외부에 있으며 데이터 공장의 활동에 의해 생성되지 않는다는 점을 알려줍니다. **형식**은 **RelationalTable**로 설정됩니다.

	{
	    "name": "Db2DataSet",
	    "properties": {
	        "type": "RelationalTable",
	        "linkedServiceName": "OnPremDb2LinkedService",
	        "typeProperties": {},
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


**Azure Blob 출력 데이터 집합:**

데이터는 매시간 새 blob에 기록됩니다.(빈도: 1시간, 간격:1회) Blob에 대한 폴더 경로는 처리 중인 조각의 시작 시간에 기반하여 동적으로 평가됩니다. 폴더 경로는 시작 시간에서 연도, 월, 일 및 시간 부분을 사용합니다.

	{
	    "name": "AzureBlobDb2DataSet",
	    "properties": {
	        "type": "AzureBlob",
	        "linkedServiceName": "AzureStorageLinkedService",
	        "typeProperties": {
	            "folderPath": "mycontainer/db2/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
	            "format": {
	                "type": "TextFormat",
	                "rowDelimiter": "\n",
	                "columnDelimiter": "\t"
	            },
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
	        "availability": {
	            "frequency": "Hour",
	            "interval": 1
	        }
	    }
	}

**복사 작업을 포함하는 파이프라인:**

파이프라인은 위의 입력 및 출력 데이터 집합을 사용하도록 구성된 복사 작업을 포함하고 매시간 실행하도록 예약됩니다. 파이프라인 JSON 정의에서 **source** 형식은 **RelationalSource**로 설정되고 **sink** 형식은 **BlobSink**로 설정됩니다. **query** 속성에 지정된 SQL 쿼리는 Orders 테이블에서 데이터를 선택합니다.

	{
	    "name": "CopyDb2ToBlob",
	    "properties": {
	        "description": "pipeline for copy activity",
	        "activities": [
	            {
	                "type": "Copy",
	                "typeProperties": {
	                    "source": {
	                        "type": "RelationalSource",
	                        "query": "select * from "Orders""
	                    },
	                    "sink": {
	                        "type": "BlobSink"
	                    }
	                },
	                "inputs": [
	                    {
	                        "name": "Db2DataSet"
	                    }
	                ],
	                "outputs": [
	                    {
	                        "name": "AzureBlobDb2DataSet"
	                    }
	                ],
	                "policy": {
	                    "timeout": "01:00:00",
	                    "concurrency": 1
	                },
	                "scheduler": {
	                    "frequency": "Hour",
	                    "interval": 1
	                },
	                "name": "Db2ToBlob"
	            }
	        ],
	        "start": "2014-06-01T18:00:00Z",
	        "end": "2014-06-01T19:00:00Z"
	    }
	}


## DB2 연결된 서비스 속성

다음 테이블은 DB2 연결된 서비스에 특정된 JSON 요소에 대한 설명을 제공합니다.

| 속성 | 설명 | 필수 |
| -------- | ----------- | -------- | 
| type | 형식 속성은 **OnPremisesDB2**로 설정되어야 합니다. | 예 |
| server | DB2 서버의 이름입니다. | 예 |
| database | DB2 데이터베이스의 이름입니다. | 예 |
| schema | 데이터베이스에서 스키마의 이름입니다. schema 이름은 대/소문자를 구분합니다. | 아니요 |
| authenticationType | DB2 데이터베이스에 연결하는 데 사용되는 인증 형식입니다. 가능한 값은 익명, 기본 및 Windows입니다. | 예 |
| username | 기본 또는 Windows 인증을 사용하는 경우 사용자 이름을 지정합니다. | 아니요 |
| password | 사용자 이름에 지정한 사용자 계정의 암호를 지정합니다. | 아니요 |
| gatewayName | 데이터 팩터리 서비스가 온-프레미스 DB2 데이터 베이스에 연결하는 데 사용해야 하는 게이트웨이의 이름 | 예 |

온-프레미스 DB2 데이터 원본의 자격 증명 설정에 대한 자세한 내용은 [자격 증명 및 보안 설정](data-factory-move-data-between-onprem-and-cloud.md#set-credentials-and-security)을 참조하세요.


## DB2 데이터 집합 형식 속성

데이터 집합 정의에 사용할 수 있는 섹션 및 속성의 전체 목록은 [데이터 집합 만들기](data-factory-create-datasets.md) 문서를 참조하세요. 구조, 가용성 및 JSON 데이터 집합의 정책과 같은 섹션이 모든 데이터 집합 형식에 대해 유사합니다(Azure SQL, Azure blob, Azure 테이블 등).

typeProperties 섹션은 데이터 집합의 각 형식에 따라 다르며 데이터 저장소에 있는 데이터의 위치에 대한 정보를 제공합니다. RelationalTable 형식(DB2 데이터 집합을 포함)의 데이터 집합에 대한 typeProperties 섹션에는 다음 속성이 있습니다.

| 속성 | 설명 | 필수 |
| -------- | ----------- | -------- | 
| tableName | 연결된 서비스가 참조하는 DB2 데이터베이스 인스턴스에서 테이블의 이름입니다. tableName은 대/소문자를 구분합니다. | 아니요(**RelationalSource**의 **쿼리**가 지정된 경우) |

## DB2 복사 활동 형식 속성

활동 정의에 사용할 수 있는 섹션 및 속성의 전체 목록은 [파이프라인 만들기](data-factory-create-pipelines.md) 문서를 참조하세요. 이름, 설명, 입력 및 출력 테이블, 다양한 정책 등과 같은 속성은 모든 유형의 활동에 사용할 수 있습니다.

반면 작업의 typeProperties 섹션에서 사용할 수 있는 속성은 각 작업 형식에 따라 다르며 복사 작업의 경우 속성은 원본 및 싱크의 형식에 따라 다릅니다.

원본이 **RelationalSource** 형식인 복사 작업의 경우(DB2 포함) typeProperties 섹션에서 다음과 같은 속성을 사용할 수 있습니다.


| 속성 | 설명 | 허용되는 값 | 필수 |
| -------- | ----------- | -------- | -------------- |
| 쿼리 | 사용자 지정 쿼리를 사용하여 데이터를 읽습니다. | SQL 쿼리 문자열. 예: "query": "select * from "MySchema"."MyTable"" | 아니요(**데이터 집합**의 **tableName**이 지정된 경우)|

> [AZURE.NOTE] 스키마와 테이블 이름은 대/소문자를 구분하며 쿼리에서 ""(큰따옴표)로 묶어야 합니다.

**예제:**

 "query": "select * from "DB2ADMIN"."Customers""


[AZURE.INCLUDE [data-factory-structure-for-rectangualr-datasets](../../includes/data-factory-structure-for-rectangualr-datasets.md)]

## DB2에 대한 형식 매핑
[데이터 이동 활동](data-factory-data-movement-activities.md) 문서에서 설명한 것처럼 복사 작업은 다음 2단계 접근 방법을 사용하여 자동 형식 변환인 원본 형식에서 싱크 형식으로 자동 형식 변환을 수행합니다.

1. 네이티브 원본 형식에서 .NET 형식으로 변환
2. .NET 형식에서 네이티브 싱크 형식으로 변환

DB2에 데이터를 이동하는 경우 다음 매핑은 DB2 형식에서 .NET 형식에 사용됩니다.

DB2 데이터베이스 형식 | .NET Framework 형식 
----------------- | ------------------- 
SmallInt | Int16
Integer | Int32
BigInt | Int64
Real | 단일
Double | Double
Float | Double
10진수 | 10진수
DecimalFloat | 10진수
숫자 | 10진수
Date | Datetime
Time | TimeSpan
Timestamp | DateTime
Xml | 바이트
Char | 문자열
VarChar | 문자열
LongVarChar | String
DB2DynArray | 문자열
이진 | 바이트
VarBinary | 바이트
LongVarBinary | 바이트
Graphic | String
VarGraphic | 문자열
LongVarGraphic | String
Clob | String
Blob | 바이트
DbClob | String
SmallInt | Int16
Integer | Int32
BigInt | Int64
Real | 단일
Double | Double
Float | Double
10진수 | 10진수
DecimalFloat | 10진수
숫자 | 10진수
Date | Datetime
Time | TimeSpan
Timestamp | DateTime
Xml | 바이트
Char | String


[AZURE.INCLUDE [data-factory-column-mapping](../../includes/data-factory-column-mapping.md)]


[AZURE.INCLUDE [data-factory-type-repeatability-for-relational-sources](../../includes/data-factory-type-repeatability-for-relational-sources.md)]

## 성능 및 튜닝  
Azure Data Factory의 데이터 이동(복사 작업) 성능에 영향을 주는 주요 요소 및 최적화하는 다양한 방법에 대해 알아보려면 [복사 작업 성능 및 조정 가이드](data-factory-copy-activity-performance.md)를 참조하세요.

<!---HONumber=AcomDC_0622_2016-->