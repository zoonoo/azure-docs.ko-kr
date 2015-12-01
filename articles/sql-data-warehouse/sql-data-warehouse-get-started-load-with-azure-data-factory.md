<properties
	pageTitle="Azure Data Factory를 사용하여 데이터 로드 | Microsoft Azure"
	description="Azure Data Factory를 사용하여 데이터를 로드하는 방법을 알아보세요."
	services="sql-data-warehouse"
	documentationCenter="NA"
	authors="lodipalm"
	manager="barbkess"
	editor=""
	tags="azure-sql-data-warehouse"/>
<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="11/19/2015"
   ms.author="lodipalm"/>

# Azure Data Factory를 사용하여 데이터 로드

> [AZURE.SELECTOR]
- [Data Factory](sql-data-warehouse-get-started-load-with-azure-data-factory.md)
- [PolyBase](sql-data-warehouse-get-started-load-with-polybase.md)
- [BCP](sql-data-warehouse-load-with-bcp.md)

 이 자습서 다음에는 Azure Data Factory에서 파이프라인을 만들어 Azure 저장소 BLOB에서 SQL 데이터 웨어하우스로 데이터를 이동하는 방법을 보여줍니다. 다음 단계에서 수행할 작업은 다음과 같습니다.

+ Azure 저장소 BLOB에서 샘플 데이터를 설정합니다.
+ Azure Data Factory로 리소스를 연결합니다.
+ 저장소 BLOB에서 SQL 데이터 웨어하우스로 데이터를 이동하는 파이프라인을 만듭니다.

>[AZURE.VIDEO loading-azure-sql-data-warehouse-with-azure-data-factory]

## 리소스

이 자습서를 완료하려면 다음 리소스가 필요합니다.

   + **Azure 저장소 Blob**: Azure 저장소 Blob이 파이프라인의 데이터 원본이 됩니다. 기존 Blob을 사용하거나 [새 Blob를 프로비전](../storage/storage-create-storage-account/)할 수 있습니다.

   + **SQL 데이터 웨어하우스**: 이 자습서에서는 데이터를 SQL 데이터 웨어하우스로 이동합니다. 인스턴스 설정이 없는 경우 [여기](sql-data-warehouse-get-started-provision.md)에서 방법을 배울 수 있습니다. 또한 AdventureWorks DW 데이터 집합으로 인스턴스를 설정해야 합니다. 샘플 데이터를 사용하여 데이터 웨어하우스를 프로비전하지 않은 경우 [수동으로 로드](sql-data-warehouse-get-started-manually-load-samples.md)할 수 있습니다.

   + **Azure Data Factory**: Azure Data Factory에서 실제 로드를 완료합니다. Azure Data Factory 설정 또는 파이프라인 만들기에 대한 자세한 정보가 필요할 경우 [여기](../data-factory/data-factory-build-your-first-pipeline-using-editor/)에서 참조하세요.

모든 항목이 준비되면 데이터를 준비하고 Azure Data Factory 파이프라인 만들기를 시작할 수 있습니다.

## 샘플 데이터

다양한 파이프라인 요소 이외에도 Azure Data Factory에서 데이터 로드를 연습하는 데 사용할 수 있는 몇 가지 샘플 데이터가 필요합니다.

1. 우선 [샘플 데이터를 다운로드](https://migrhoststorage.blob.core.windows.net/adfsample/FactInternetSales.csv)합니다. 이 데이터는 이미 사용자의 샘플 데이터에 있는 샘플 데이터와 연결되어 추가 3년의 영업 데이터를 제공합니다.

2. 데이터가 다운로드되면 AZCopy에서 아래 스크립트를 실행하여 BLOB 저장소로 이동할 수 있습니다.

        AzCopy /Source:<Sample Data Location>  /Dest:https://<storage account>.blob.core.windows.net/<container name> /DestKey:<storage key> /Pattern:FactInternetSales.csv

	AZCopy를 설치 및 사용하는 방법에 대한 자세한 내용은 [AZCopy 설명서](../storage/storage-use-azcopy/)를 참조하세요.

이제 데이터가 준비되었으므로 데이터 팩터리로 이동하여 사용자의 저장소 계정에서 SQL 데이터 웨어하우스로 데이터를 이동할 파이프라인을 만들 수 있습니다.

## Azure Data Factory 사용

이제 모든 항목이 설정되었으므로 Azure Preview 포털에서 Azure Data Factory 인스턴스를 탐색하여 파이프라인 설정을 시작할 수 있습니다. 이 작업을 수행하려면 [Azure 포털](portal.azure.com)로 이동한 다음 왼쪽 메뉴에서 사용자의 데이터 팩터리를 선택합니다.

여기에서 3단계(서비스 연결, 데이터 집합 정의, 파이프라인 만들기)를 거쳐 Azure Data Factory 파이프라인을 설정하고 데이터 웨어하우스로 데이터를 전송합니다.

### 연결된 서비스 만들기

가장 먼저 Azure 저장소 계정과 SQL 데이터 웨어하우스를 데이터 팩터리로 연결합니다.

1. 우선 데이터 팩터리의 '연결된 서비스' 섹션을 클릭한 다음 '새 데이터 저장소'를 클릭하여 등록 프로세스를 시작합니다. 그런 다음 Azure 저장소를 등록할 이름을 선택하고 유형으로 Azure 저장소를 선택한 다음 계정 이름과 계정 키를 선택합니다.

2. SQL 데이터 웨어하우스를 등록하려면 '작성 및 배포' 섹션을 탐색한 다음 '새 데이터 저장소'와 'Azure SQL 데이터 웨어하우스'를 차례로 선택합니다. 그런 다음 아래 템플릿을 입력해야 합니다.

		{
		    "name": "<Linked Service Name>",
		    "properties": {
		        "description": "",
		        "type": "AzureSqlDW",
		        "typeProperties": {
		            "connectionString": "Data Source=tcp:<server name>.database.windows.net,1433;Initial Catalog=<server name>;Integrated Security=False;User ID=<user>@<servername>;Password=<password>;Connect Timeout=30;Encrypt=True"
		        }
		    }
		}

### 데이터 집합 등록

연결된 서비스를 만든 다음 데이터 집합을 정의해야 합니다. 여기서 저장소에서 데이터 웨어하우스로 이동하는 데이터의 구조를 정의하는 것을 의미합니다. 만들기에 대해 자세히 알아볼 수 있습니다.

1. 이 프로세스를 시작하려면 가장 먼저 사용자 데이터 팩터리의 '작성 및 배포' 섹션으로 이동합니다.

2. '새 데이터 집합'을 클릭한 다음 'Azure BLOB 저장소'를 클릭하여 저장소를 데이터 팩터리에 연결합니다. 아래 스크립트를 사용하여 Azure BLOB 저장소에서 데이터를 정의할 수 있습니다.

		{
			"name": "<Dataset Name>",
			"properties": {
				"type": "AzureBlob",
				"linkedServiceName": "<linked storage name>",
				"typeProperties": {
					"folderPath": "<containter name>",
					"fileName": "FactInternetSales.csv",
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



3. 이제 SQL 데이터 웨어하우스의 데이터 집합을 정의합니다. 마찬가지로 '새 데이터 집합'을 클릭한 다음 'Azure SQL 데이터 웨어하우스'를 클릭합니다.

		{
		  "name": "<dataset name>",
		  "properties": {
		    "type": "AzureSqlDWTable",
		    "linkedServiceName": "<linked data warehouse name>",
		    "typeProperties": {
		      "tableName": "FactInternetSales"
		    },
		    "availability": {
		      "frequency": "Hour",
		      "interval": 1
		    }
		  }
		}

		{
		  "name": "DWDataset",
		  "properties": {
			"type": "AzureSqlDWTable",
			"linkedServiceName": "AzureSqlDWLinkedService",
			"typeProperties": {
			  "tableName": "FactInternetSales"
			},
			"availability": {
			  "frequency": "Hour",
			  "interval": 1
			}
		  }
		}

### 파이프라인 설정

마지막으로 Azure Data Factory에서 파이프라인을 설정 및 실행합니다. 이 작업을 수행하면 실제 데이터 이동이 완료됩니다. [여기](../data-factory/data-factory-azure-sql-data-warehouse-connector/)에서 SQL 데이터 웨어하우스와 Azure Data Factory를 사용하여 완료할 수 있는 전체 작업을 볼 수 있습니다.

'작성 및 배포' 섹션에서 이제 '추가 명령'을 클릭한 다음 '새 파이프라인'을 클릭합니다. 파이프라인을 만든 다음 아래 코드를 사용하여 데이터를 데이터 웨어하우스로 전송할 수 있습니다.

	{
	"name": "<Pipeline Name>",
	"properties": {
		"description": "<Description>",
		"activities": [
			{
				"type": "Copy",
				"typeProperties": {
					"source": {
						"type": "BlobSource",
						"skipHeaderLineCount": 1
					},
					"sink": {
						"type": "SqlDWSink",
						"writeBatchSize": 0,
						"writeBatchTimeout": "00:00:10"
					}
				},
				"inputs": [
					{
						"name": "<Storage Dataset>"
					}
				],
				"outputs": [
					{
						"name": "<Data Warehouse Dataset>"
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
				"name": "Sample Copy",
				"description": "Copy Activity"
			}
		],
		"start": "<Date YYYY-MM-DD>",
		"end": "<Date YYYY-MM-DD>",
		"isPaused": false
	}
	}
	

<!---HONumber=AcomDC_1125_2015-->