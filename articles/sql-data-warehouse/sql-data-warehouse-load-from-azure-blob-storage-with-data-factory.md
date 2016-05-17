<properties
   pageTitle="Azure Blob 저장소에서 Azure SQL 데이터 웨어하우스(Azure Data Factory)로 데이터 로드 | Microsoft Azure"
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
   ms.date="04/29/2016"
   ms.author="lodipalm;barbkess;sonyama"/>

# Azure Blob 저장소에서 Azure SQL 데이터 웨어하우스로 데이터 로드(Azure Data Factory)

> [AZURE.SELECTOR]
- [데이터 팩터리](sql-data-warehouse-load-from-azure-blob-storage-with-data-factory.md)
- [PolyBase](sql-data-warehouse-load-from-azure-blob-storage-with-polybase.md)

 이 자습서는 Azure Data Factory에서 파이프라인을 만들어 Azure 저장소 BLOB에서 SQL 데이터 웨어하우스로 데이터를 이동하는 방법을 보여 줍니다. 다음 단계에서 수행할 작업은 다음과 같습니다.

+ Azure 저장소 BLOB에서 샘플 데이터를 설정합니다.
+ Azure Data Factory로 리소스를 연결합니다.
+ 저장소 BLOB에서 SQL 데이터 웨어하우스로 데이터를 이동하는 파이프라인을 만듭니다.

>[AZURE.VIDEO loading-azure-sql-data-warehouse-with-azure-data-factory]


## 시작하기 전에

Azure Data Factory를 익히려면 [Azure Data Factory 소개][]를 참조하세요.

### 리소스 만들기 또는 식별

이 자습서를 시작하기 전에 다음 리소스를 마련해야 합니다.

   + **Azure 저장소 BLOB**: 이 자습서에서는 Azure Data Factory 파이프라인에 대한 데이터 원본으로 Azure BLOB 저장소를 사용하므로 샘플 데이터를 저장할 Azure BLOB 저장소가 필요합니다. 아직 없는 경우 [저장소 계정을 만드는][] 방법을 알아봅니다.

   + **SQL 데이터 웨어하우스**: 이 자습서는 Azure 저장소 BLOB에서 SQL 데이터 웨어하우스로 데이터를 이동하므로 AdventureWorksDW 샘플 데이터와 함께 로드되는 데이터 웨어하우스 온라인이 필요합니다. 데이터 웨어하우스가 아직 없는 경우 [프로비전하는][Create a SQL Data Warehouse] 방법을 알아봅니다. 데이터 웨어하우스가 있지만 샘플 데이터를 사용하여 프로비전하지 않은 경우 [수동으로 로드][Load sample data into SQL Data Warehouse]할 수 있습니다.

   + **Azure Data Factory**: Azure Data Factory는 실제 부하를 완료하므로 데이터 이동 파이프라인을 작성하는 데 사용할 수 있는 Azure Data Factory가 필요합니다. 아직 없는 경우 [Azure Data Factory 시작(Data Factory 편집기)][]의 1단계에서 만드는 방법을 알아봅니다.

   + **AZCopy**: 로컬 클라이언트에서 Azure 저장소 BLOB으로 샘플 데이터를 복사할 AZCopy가 필요합니다. 설치 지침은 [AZCopy 설명서][]를 참조하세요.

## 1단계: 샘플 데이터를 Azure 저장소 Blob에 복사

모든 부분이 준비되면 샘플 데이터를 Azure 저장소 Blob에 복사할 준비가 됩니다.

1. [샘플 데이터를 다운로드합니다][]. 이 데이터는 AdventureWorksDW 샘플 데이터에 3년의 판매 데이터를 추가합니다.

2. 이 AZCopy 명령을 사용하여 Azure 저장소 Blob에 3년 분량의 데이터를 복사합니다.

````
AzCopy /Source:<Sample Data Location>  /Dest:https://<storage account>.blob.core.windows.net/<container name> /DestKey:<storage key> /Pattern:FactInternetSales.csv
````


## 2단계: Azure Data Factory로 리소스를 연결합니다.

이제 데이터가 생성되었으므로 Azure 데이터 팩터리 파이프라인을 만들어 Azure Blob 저장소에서 SQL 데이터 웨어하우스로 데이터를 이동할 수 있습니다.

시작하려면 [Azure 포털][]을 열고 왼쪽 메뉴에서 사용자의 data factory를 선택합니다.

### 2\.1단계: 연결된 서비스 만들기

Azure 저장소 계정과 SQL 데이터 웨어하우스를 데이터 팩터리로 연결합니다.

1. 우선 데이터 팩터리의 '연결된 서비스' 섹션을 클릭한 다음 '새 데이터 저장소'를 클릭하여 등록 프로세스를 시작합니다. Azure 저장소를 등록할 이름을 선택하고 유형으로 Azure 저장소를 선택한 다음 계정 이름과 계정 키를 입력합니다.

2. SQL 데이터 웨어하우스를 등록하려면 '작성 및 배포' 섹션을 탐색하고 '새 데이터 저장소'와 'Azure SQL 데이터 웨어하우스'를 차례로 선택합니다. 이 템플릿에 붙여 넣은 다음 특정 정보를 입력합니다.

```JSON
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
```

### 2\.2단계: 데이터 집합 정의

연결된 서비스를 만든 다음 데이터 집합을 정의해야 합니다. 여기서 저장소에서 데이터 웨어하우스로 이동하는 데이터의 구조를 정의하는 것을 의미합니다. 만들기에 대해 자세히 알아볼 수 있습니다.

1. 이 프로세스를 시작하려면 가장 먼저 사용자 데이터 팩터리의 '작성 및 배포' 섹션으로 이동합니다.

2. '새 데이터 집합'을 클릭한 다음 'Azure BLOB 저장소'를 클릭하여 저장소를 데이터 팩터리에 연결합니다. 아래 스크립트를 사용하여 Azure BLOB 저장소에서 데이터를 정의할 수 있습니다.

```JSON
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
```


3. 이제 SQL 데이터 웨어하우스의 데이터 집합을 정의합니다. 마찬가지로 '새 데이터 집합'을 클릭한 다음 'Azure SQL 데이터 웨어하우스'를 클릭합니다.

```JSON
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
```

## 3단계: 파이프라인 만들기 및 실행

마지막으로 Azure Data Factory에서 파이프라인을 설정 및 실행합니다. 이 작업을 수행하면 실제 데이터 이동이 완료됩니다. [여기][Move data to and from Azure SQL Data Warehouse using Azure Data Factory]에서 SQL 데이터 웨어하우스와 Azure Data Factory를 사용하여 완료할 수 있는 전체 작업을 볼 수 있습니다.

'작성 및 배포' 섹션에서 이제 '추가 명령'을 클릭한 다음 '새 파이프라인'을 클릭합니다. 파이프라인을 만든 다음 아래 코드를 사용하여 데이터를 데이터 웨어하우스로 전송할 수 있습니다.

```JSON
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
```

## 다음 단계

자세한 내용은 다음을 확인하여 시작합니다.

- [Azure Data Factory 학습 경로][].
- [Azure SQL 데이터 웨어하우스 커넥터][]. Azure SQL 데이터 웨어하우스와 함께 Azure 데이터 팩터리를 사용하기 위한 핵심 참조 항목입니다.


이러한 항목은 Azure 데이터 팩터리에 대한 자세한 정보를 제공합니다. Azure SQL 데이터베이스 또는 HDinsight를 설명하지만 해당 정보는 Azure SQL 데이터 웨어하우스에도 적용됩니다.

- [자습서: Azure Data Factory 시작][] Azure Data Factory를 사용하여 데이터를 처리하기 위한 핵심 자습서입니다. 이 자습서에서 HDInsight를 사용하여 월별 웹 로그를 변환 및 분석하는 첫 번째 파이프라인을 빌드합니다. 이 자습서에는 복사 작업이 없습니다.
- [자습서: Azure 저장소 BLOB에서 Azure SQL 데이터베이스로 데이터 복사][] 이 자습서에서는 Azure 저장소 Blob에서 Azure SQL 데이터베이스로 데이터를 복사하는 파이프라인을 Azure 데이터 팩터리에 만듭니다.
- [실제 시나리오 자습서][]. Azure 데이터 팩터리 사용에 대한 자세한 자습서입니다.

<!--Image references-->

<!--Article references-->
[AZCopy 설명서]: ../storage/storage-use-azcopy.md
[Azure SQL 데이터 웨어하우스 커넥터]: ../data-factory/data-factory-azure-sql-data-warehouse-connector.md
[BCP]: sql-data-warehouse-load-with-bcp.md
[Create a SQL Data Warehouse]: sql-data-warehouse-get-started-provision.md
[저장소 계정을 만드는]: ../storage/storage-create-storage-account.md#create-a-storage-account
[Data Factory]: sql-data-warehouse-get-started-load-with-azure-data-factory.md
[Azure Data Factory 시작(Data Factory 편집기)]: ../data-factory/data-factory-build-your-first-pipeline-using-editor.md
[Azure Data Factory 소개]: ../data-factory/data-factory-introduction.md
[Load sample data into SQL Data Warehouse]: sql-data-warehouse-get-started-manually-load-samples.md
[Move data to and from Azure SQL Data Warehouse using Azure Data Factory]: ../data-factory/data-factory-azure-sql-data-warehouse-connector.md
[PolyBase]: sql-data-warehouse-get-started-load-with-polybase.md
[실제 시나리오 자습서]: ../data-factory/data-factory-tutorial.md
[자습서: Azure 저장소 BLOB에서 Azure SQL 데이터베이스로 데이터 복사]: ../data-factory/data-factory-get-started
[자습서: Azure Data Factory 시작]: ../data-factory/data-factory-build-your-first-pipeline.md

<!--MSDN references-->

<!--Other Web references-->
[Azure Data Factory 학습 경로]: https://azure.microsoft.com/documentation/learning-paths/data-factory
[Azure 포털]: https://portal.azure.com
[샘플 데이터를 다운로드합니다]: https://migrhoststorage.blob.core.windows.net/adfsample/FactInternetSales.csv

<!---HONumber=AcomDC_0511_2016-->