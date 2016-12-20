---
title: "Azure Blob Storage 간 데이터 복사 | Microsoft Docs"
description: "Azure Data Factory에서 Blob 데이터를 복사하는 방법을 알아봅니다. 샘플 사용: Azure Blob 저장소 및 Azure SQL 데이터베이스 간에 데이터를 복사하는 방법입니다."
keywords: "Blob 데이터, Azure Blob 복사"
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: monicar
ms.assetid: bec8160f-5e07-47e4-8ee1-ebb14cfb805d
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/12/2016
ms.author: jingwang
translationtype: Human Translation
ms.sourcegitcommit: 701d82971b7da92fb0946cbfc7f708ad32501ef3
ms.openlocfilehash: 69b530c085cc99959e66e75f2c8ebb391c9d26f4


---
# <a name="move-data-to-and-from-azure-blob-using-azure-data-factory"></a>Azure Data Factory를 사용하여 Azure Blob 간 데이터 이동
이 문서에서는 다른 데이터 저장소에서 Blob 데이터를 소싱하여 Azure Blob 간에 데이터를 이동하기 위해 Azure Data Factory에서 복사 작업을 사용하는 방법을 설명합니다. 이 문서는 복사 작업 및 지원되는 데이터 저장소 조합을 사용하여 데이터 이동의 일반적인 개요를 보여주는 [데이터 이동 작업](data-factory-data-movement-activities.md) 문서를 작성합니다.

## <a name="supported-sources-and-sinks"></a>지원되는 원본 및 싱크
복사 작업의 원본 및 싱크로 지원되는 데이터 저장소 목록은 [지원되는 데이터 저장소](data-factory-data-movement-activities.md#supported-data-stores-and-formats) 테이블을 참조하세요. 모든 지원되는 원본 데이터 저장소에서 Azure Blob Storage로 또는 Azure Blob Storage에서 모든 지원되는 싱크 데이터 저장소로 데이터를 이동할 수 있습니다.

복사 작업은 범용 Azure Storage 계정 및 핫/쿨 Blob 저장소 간의 데이터 복사를 모두 지원합니다. 이 작업은 블록에서 읽기, 추가 또는 페이지 Blob를 지원하며 쓰기는 블록 Blob만 지원합니다.

## <a name="create-pipeline"></a>파이프라인 만들기
다른 도구/API를 사용하여 Azure Blob Storage 간에 데이터를 이동하는 복사 작업으로 파이프라인을 만들 수 있습니다.  

* 복사 마법사
* Azure 포털
* Visual Studio
* Azure PowerShell
* .NET API
* REST API

다양한 방법의 복사 작업을 포함하는 파이프라인을 만들기 위한 단계별 지침은 [복사 작업 자습서](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)를 참조하세요.

## <a name="copy-data-wizard"></a>데이터 복사 마법사
Azure Blob 저장소 간에 데이터를 복사하는 파이프라인을 만드는 가장 쉬운 방법은 데이터 복사 마법사를 사용하는 것입니다. 데이터 복사 마법사를 사용하여 파이프라인을 만드는 방법에 대한 빠른 연습은 [자습서: 복사 마법사를 사용하여 파이프라인 만들기](data-factory-copy-data-wizard-tutorial.md)를 참조하세요.

다음 예에서는 [Azure 포털](data-factory-copy-activity-tutorial-using-azure-portal.md), [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) 또는 [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md)을 사용하여 파이프라인을 만드는 데 사용할 수 있는 샘플 JSON 정의를 제공합니다. Azure Blob 저장소 및 Azure SQL 데이터베이스 간에 데이터를 복사하는 방법을 보여 줍니다. 그러나 임의의 원본에서 지원되는 싱크로 **직접** 데이터를 복사할 수 있습니다. 자세한 내용은 [복사 작업을 사용하여 데이터 이동](data-factory-data-movement-activities.md)에서 "지원되는 데이터 저장소 및 형식" 섹션을 참조하세요.

## <a name="sample-copy-data-from-azure-blob-to-azure-sql"></a>샘플: Azure Blob에서 Azure SQL로 데이터 복사
다음 샘플은 다음과 같은 내용을 보여 줍니다.

1. [AzureSqlDatabase](data-factory-azure-sql-connector.md#azure-sql-linked-service-properties) 형식의 연결된 서비스입니다.
2. [AzureStorage](#azure-storage-linked-service-properties) 형식의 연결된 서비스
3. [AzureBlob](#azure-blob-dataset-type-properties) 형식의 입력 [데이터 집합](data-factory-create-datasets.md)입니다.
4. [AzureSqlTable](data-factory-azure-sql-connector.md#azure-sql-dataset-type-properties) 형식의 출력 [데이터 집합](data-factory-create-datasets.md)입니다.
5. [BlobSource](#azure-blob-copy-activity-type-properties) 및 [SqlSink](data-factory-azure-sql-connector.md#azure-sql-copy-activity-type-properties)를 사용하는 복사 작업의 [파이프라인](data-factory-create-pipelines.md)입니다.

샘플은 Azure Blob에서 Azure SQL 테이블로 매시간 시계열 데이터를 복사합니다. 이 샘플에 사용된 JSON 속성은 샘플 다음에 나오는 섹션에서 설명합니다.

**Azure SQL 연결된 서비스:**

```json
{
  "name": "AzureSqlLinkedService",
  "properties": {
    "type": "AzureSqlDatabase",
    "typeProperties": {
      "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
    }
  }
}
```
**Azure 저장소 연결된 서비스:**

```json
{
  "name": "StorageLinkedService",
  "properties": {
    "type": "AzureStorage",
    "typeProperties": {
      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
    }
  }
}
```
Azure Data Factory는 두 가지 유형의 Azure Storage 연결된 서비스: **AzureStorage** 및 **AzureStorageSas**를 제공합니다. 첫 번째 것의 경우 계정 키를 포함하는 연결 문자열을 지정하고 이후 것의 경우 SAS(공유 액세스 서명) Uri를 지정합니다. 자세한 내용은 [연결된 서비스](#linked-services) 섹션을 참조하세요.  

**Azure Blob 입력 데이터 집합:**

데이터는 매시간 새 blob에 선택됩니다(frequency: hour, interval: 1). Blob에 대한 폴더 경로 및 파일 이름은 처리 중인 조각의 시작 시간에 기반하여 동적으로 평가됩니다. 폴더 경로에는 시작 시간의 년/월/일 부분이 사용되고 파일 이름에는 시작 시간의 시간 부분이 사용됩니다. "external": "true"를 설정하면 테이블이 Data Factory의 외부에 있으며 Data Factory의 활동에 의해 생성되지 않는다는 사실이 Data Factory에 전달됩니다.

```json
{
  "name": "AzureBlobInput",
  "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "folderPath": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}/",
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
```
**Azure SQL 출력 데이터 집합:**

샘플은 Azure SQL 데이터베이스의 "MyTable"이라는 테이블에 데이터를 복사합니다. Blob CSV 파일을 포함하려 하면 같은 수의 열을 사용하여 Azure SQL 데이터베이스에 테이블을 만듭니다. 새 행은 매시간 테이블에 추가됩니다.

```json
{
  "name": "AzureSqlOutput",
  "properties": {
    "type": "AzureSqlTable",
    "linkedServiceName": "AzureSqlLinkedService",
    "typeProperties": {
      "tableName": "MyOutputTable"
    },
    "availability": {
      "frequency": "Hour",
      "interval": 1
    }
  }
}
```
**복사 작업을 포함하는 파이프라인:**

파이프라인은 입력 및 출력 데이터 집합을 사용하도록 구성된 복사 작업을 포함하고 매시간 실행하도록 예약됩니다. 파이프라인 JSON 정의에서 **원본** 형식은 **BlobSource**로 설정되고 **싱크** 형식은 **SqlSink**로 설정됩니다.

```json
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
            "name": "AzureSqlOutput"
          }
        ],
        "typeProperties": {
          "source": {
            "type": "BlobSource"
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
```
## <a name="sample-copy-data-from-azure-sql-to-azure-blob"></a>샘플: Azure SQL에서 Azure Blob으로 데이터 복사
다음 샘플은 다음과 같은 내용을 보여 줍니다.

1. [AzureSqlDatabase](data-factory-azure-sql-connector.md#azure-sql-linked-service-properties) 형식의 연결된 서비스입니다.
2. [AzureStorage](#azure-storage-linked-service-properties) 형식의 연결된 서비스
3. [AzureSqlTable](data-factory-azure-sql-connector.md#azure-sql-dataset-type-properties) 형식의 입력 [데이터 집합](data-factory-create-datasets.md)입니다.
4. [AzureBlob](#azure-blob-dataset-type-properties) 형식의 출력 [데이터 집합](data-factory-create-datasets.md)
5. [SqlSource](data-factory-azure-sql-connector.md#azure-sql-copy-activity-type-properties) 및 [BlobSink](#azure-blob-copy-activity-type-properties)를 사용하는 복사 작업의 [파이프라인](data-factory-create-pipelines.md)입니다.

샘플은 Azure SQL 테이블에서 Azure Blob으로 매시간 시계열 데이터를 복사합니다. 이 샘플에 사용된 JSON 속성은 샘플 다음에 나오는 섹션에서 설명합니다.

**Azure SQL 연결된 서비스:**

```json
{
  "name": "AzureSqlLinkedService",
  "properties": {
    "type": "AzureSqlDatabase",
    "typeProperties": {
      "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
    }
  }
}
```
**Azure 저장소 연결된 서비스:**

```json
{
  "name": "StorageLinkedService",
  "properties": {
    "type": "AzureStorage",
    "typeProperties": {
      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
    }
  }
}
```
Azure Data Factory는 두 가지 유형의 Azure Storage 연결된 서비스: **AzureStorage** 및 **AzureStorageSas**를 제공합니다. 첫 번째 것의 경우 계정 키를 포함하는 연결 문자열을 지정하고 이후 것의 경우 SAS(공유 액세스 서명) Uri를 지정합니다. 자세한 내용은 [연결된 서비스](#linked-services) 섹션을 참조하세요.  

**Azure SQL 입력 데이터 집합:**

샘플은 Azure SQL에서 만든 테이블 "MyTable"에 시계열 데이터에 대한 "timestampcolumn"라는 열이 포함되었다고 가정합니다.

"external": "true"를 설정하면 테이블이 Data Factory의 외부에 있으며 Data Factory의 활동에 의해 생성되지 않는다는 사실이 Data Factory 서비스에 전달됩니다.

```json
{
  "name": "AzureSqlInput",
  "properties": {
    "type": "AzureSqlTable",
    "linkedServiceName": "AzureSqlLinkedService",
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
```

**Azure Blob 출력 데이터 집합:**

데이터는 매시간 새 blob에 기록됩니다.(빈도: 1시간, 간격:1회) Blob에 대한 폴더 경로는 처리 중인 조각의 시작 시간에 기반하여 동적으로 평가됩니다. 폴더 경로는 시작 시간에서 연도, 월, 일 및 시간 부분을 사용합니다.

```json
{
  "name": "AzureBlobOutput",
  "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "folderPath": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}/",
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
```

**복사 작업을 포함하는 파이프라인:**

파이프라인은 입력 및 출력 데이터 집합을 사용하도록 구성된 복사 작업을 포함하고 매시간 실행하도록 예약됩니다. 파이프라인 JSON 정의에서 **source** 형식은 **SqlSource**로 설정되고 **sink** 형식은 **BlobSink**로 설정됩니다. **SqlReaderQuery** 속성에 지정된 SQL 쿼리는 과거 한 시간에서 복사할 데이터를 선택합니다.

```json
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
                    "name": "AzureSQLInput"
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
```
## <a name="linked-services"></a>연결된 서비스
샘플에서는 Azure Storage 계정을 데이터 팩터리에 연결하는 데 **AzureStorage** 형식의 연결된 서비스를 사용했습니다. 다음 테이블은 Azure 저장소 연결된 서비스에 특정된 JSON 요소에 대한 설명을 제공합니다.

Azure Blob 저장소를 Azure Data Factory에 연결하는 데 사용할 수 있는 두 가지 유형의 연결된 서비스가 있습니다. **AzureStorage** 연결된 서비스 및 **AzureStorageSas** 연결된 서비스입니다. Azure 저장소 연결된 서비스는 Azure 저장소에 대한 전역 액세스로 Data Factory를 제공합니다. 반면 Azure 저장소 SAS(공유 액세스 서명) 연결된 서비스는 Azure 저장소에 대한 제한/시간 제한 액세스로 Data Factory를 제공합니다. 이 두 연결된 서비스에는 다른 차이가 없습니다. 필요에 맞는 연결된 서비스를 선택합니다. 다음 섹션에서는 이러한 두 연결된 서비스에 대한 자세한 정보를 제공합니다.

[!INCLUDE [data-factory-azure-storage-linked-services](../../includes/data-factory-azure-storage-linked-services.md)]

## <a name="azure-blob-dataset-type-properties"></a>Azure Blob 데이터 집합 형식 속성
샘플에서는 Azure Blob 저장소의 Blob 컨테이너 및 폴더를 나타내는 데 **AzureBlob** 형식의 데이터 집합을 사용했습니다.

데이터 집합 정의에 사용할 수 있는 JSON 섹션 및 속성의 전체 목록은 [데이터 집합 만들기](data-factory-create-datasets.md) 문서를 참조하세요. 구조, 가용성 및 JSON 데이터 집합의 정책과 같은 섹션이 모든 데이터 집합 형식에 대해 유사합니다(Azure SQL, Azure blob, Azure 테이블 등).

**typeProperties** 섹션은 데이터 집합의 각 형식에 따라 다르며 데이터 저장소에 있는 데이터의 위치, 서식 등에 대한 정보를 제공합니다. **AzureBlob** 데이터 집합 형식의 데이터 집합에 대한 typeProperties 섹션에는 다음 속성이 있습니다.

| 속성 | 설명 | 필수 |
| --- | --- | --- |
| folderPath |blob 저장소에서 컨테이너 및 폴더에 대한 경로입니다. 예제: myblobcontainer\myblobfolder\ |예 |
| fileName |Blob의 이름입니다. fileName은 선택 사항이며 대/소문자를 구분합니다.<br/><br/>filename을 지정하는 경우 활동(복사 포함)은 특정 Blob에서 작동합니다.<br/><br/>fileName을 지정하지 않으면 복사는 입력 데이터 집합에 대한 folderPath에 모든 Blob을 포함합니다.<br/><br/>fileName이 출력 데이터 집합에 대해 지정되지 않으면 생성된 파일의 이름은 다음 형식을 사용합니다. Data.<Guid>.txt(예: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt) |아니요 |
| partitionedBy |partitionedBy는 선택적 속성입니다. 동적 folderPath 및 시계열 데이터에 대한 filename을 지정하는 데 사용할 수 있습니다. 예를 들어 folderPath는 매시간 데이터에 대한 매개 변수화됩니다. 자세한 내용과 예제는 [partitionedBy 속성 사용 섹션](#using-partitionedBy-property)을 참조하세요. |아니요 |
| format |**TextFormat**, **AvroFormat**, **JsonFormat**, **OrcFormat**, **ParquetFormat**과 같은 서식 유형이 지원됩니다. 이 값 중 하나로 서식에서 **type** 속성을 설정합니다. 세부 정보는 [TextFormat 지정](#specifying-textformat), [AvroFormat 지정](#specifying-avroformat), [JsonFormat 지정](#specifying-jsonformat), [OrcFormat 지정](#specifying-orcformat) 및 [ParquetFormat 지정](#specifying-parquetformat) 섹션을 참조하세요. 파일 기반 저장소(이진 복사) 간에 파일을 있는 그대로 복사하려는 경우 입력 및 출력 데이터 집합 정의 둘 다에서 형식 섹션을 건너뛸 수 있습니다. |아니요 |
| 압축 |데이터에 대한 압축 유형 및 수준을 지정합니다. 지원되는 형식은 **GZip**, **Deflate** 및 **BZip2**이고 지원되는 수준은 **최적** 및 **가장 빠름**입니다. 자세한 내용은 [압축 지원](#compression-support) 섹션을 참조하세요. 현재 **AvroFormat**, **OrcFormat** 또는 **ParquetFormat**의 데이터에 대한 압축 설정은 지원되지 않습니다. 이러한 형식의 경우 데이터 팩터리는 메타데이터에 있는 압축 코덱을 사용하여 데이터를 읽습니다. 그러나 이러한 형식의 파일에 쓸 때 데이터 팩터리는 해당 형식에 대한 기본 압축 코드를 선택합니다. 예를 들어 ParquetFormat에 대해 SNAPPY를 OrcFormat에 대해 ZLIB을 사용합니다. |아니요 |

### <a name="using-partitionedby-property"></a>partitionedBy 속성 사용
이전 섹션에서 설명한 것처럼 **partitionedBy** 섹션, Data Factory 매크로 및 시스템 변수를 사용하여 동적 folderPath 및 시계열 데이터와 파일 이름을 지정할 수 있습니다. 지정된 데이터 조각에 대한 시작 및 종료 시간을 나타내는 SliceStart 및 SliceEnd입니다.

시간 시계열 데이터 집합, 예약 및 조각에 대한 자세한 내용은 [데이터 집합 만들기](data-factory-create-datasets.md) 및 [일정 예약 및 실행](data-factory-scheduling-and-execution.md) 문서를 참조하세요.

#### <a name="sample-1"></a>샘플 1

```json
"folderPath": "wikidatagateway/wikisampledataout/{Slice}",
"partitionedBy":
[
    { "name": "Slice", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyyMMddHH" } },
],
```

이 예제에서 {Slice}는 지정된 형식(YYYYMMDDHH)의 Data Factory 시스템 변수 SliceStart 값으로 대체됩니다. SliceStart는 조각의 시작 시간을 가리킵니다. folderPath는 각 조각에 따라 다릅니다. 예를 들어 wikidatagateway/wikisampledataout/2014100103 또는 wikidatagateway/wikisampledataout/2014100104입니다

#### <a name="sample-2"></a>샘플 2

```json
"folderPath": "wikidatagateway/wikisampledataout/{Year}/{Month}/{Day}",
"fileName": "{Hour}.csv",
"partitionedBy":
 [
    { "name": "Year", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyy" } },
    { "name": "Month", "value": { "type": "DateTime", "date": "SliceStart", "format": "MM" } },
    { "name": "Day", "value": { "type": "DateTime", "date": "SliceStart", "format": "dd" } },
    { "name": "Hour", "value": { "type": "DateTime", "date": "SliceStart", "format": "hh" } }
],
```

이 예제에서 SliceStart의 연도, 월, 일 및 시간은 folderPath 및 fileName 속성에서 사용하는 별도 변수로 추출됩니다.

[!INCLUDE [data-factory-file-format](../../includes/data-factory-file-format.md)]

[!INCLUDE [data-factory-compression](../../includes/data-factory-compression.md)]

## <a name="azure-blob-copy-activity-type-properties"></a>Azure Blob 복사 활동 형식 속성
활동 정의에 사용할 수 있는 섹션 및 속성의 전체 목록은 [파이프라인 만들기](data-factory-create-pipelines.md) 문서를 참조하세요. 이름, 설명, 입력/출력 데이터 집합, 정책 등의 속성은 모든 유형의 활동에 사용할 수 있습니다.

반면 활동의 typeProperties 섹션에서 사용할 수 있는 속성은 각 활동 형식에 따라 다릅니다. 복사 작업의 경우 원본 및 싱크의 형식에 따라 달라집니다.

Azure Blob Storage에서 데이터를 이동하는 경우 복사 작업의 원본 유형을 **BlobSource**로 설정합니다. 마찬가지로 Azure Blob Storage로 데이터를 이동하는 경우 복사 작업의 싱크 유형을 **BlobSink**로 설정합니다. 이 섹션에서는 BlobSource 및 BlobSink에서 지원되는 속성의 목록을 제공합니다.

**BlobSource**는 **typeProperties** 섹션에서 다음 속성을 지원합니다.

| 속성 | 설명 | 허용되는 값 | 필수 |
| --- | --- | --- | --- |
| recursive |하위 폴더에서 또는 지정된 폴더에서만 데이터를 재귀적으로 읽을지 여부를 나타냅니다. |True(기본값), False |아니요 |

**BlobSink**는 **typeProperties** 섹션에서 다음 속성을 지원합니다.

| 속성 | 설명 | 허용되는 값 | 필수 |
| --- | --- | --- | --- |
| copyBehavior |원본이 BlobSource 또는 FileSystem인 경우 복사 동작을 정의합니다. |**PreserveHierarchy:** 대상 폴더에서 파일 계층 구조를 유지합니다. 원본 폴더의 원본 파일 상대 경로는 대상 폴더의 대상 파일 상대 경로와 동일합니다.<br/><br/>**FlattenHierarchy**: 원본 폴더의 모든 파일은 대상 폴더의 첫 번째 수준에 있게 됩니다. 대상 파일은 자동 생성된 이름을 갖습니다. <br/><br/>**MergeFiles:(기본값)** 원본 폴더의 모든 파일을 하나의 파일로 병합합니다. 파일/Blob 이름이 지정된 경우 지정된 이름이 병합된 파일 이름이 됩니다. 그렇지 않으면 자동 생성된 파일 이름이 병합된 파일 이름이 됩니다. |아니요 |

**BlobSource**에서도 이전 버전과의 호환성을 위해 이러한 두 속성을 지원합니다.

* **treatEmptyAsNull**: Null 또는 빈 문자열을 null 값으로 처리할지 여부를 지정합니다.
* **skipHeaderLineCount** - 건너뛰어야 하는 줄 수를 지정합니다. 입력 데이터 집합이 TextFormat을 사용하는 경우에만 해당합니다.

마찬가지로, **BlobSink**는 이전 버전과의 호환성을 위해 다음 속성을 지원합니다.

* **blobWriterAddHeader**: 출력 데이터 집합에 쓰는 동안 열 정의의 헤더를 추가할지 여부를 지정합니다.

이제 데이터 집합은 동일한 기능을 구현하는 다음 속성을 지원합니다. **treatEmptyAsNull**, **skipLineCount**, **firstRowAsHeader**

다음 표에서는 Blob 원본/싱크 속성 대신 새 데이터 집합 속성을 사용하는 지침을 제공합니다.

| 복사 작업 속성 | 데이터 집합 속성 |
|:--- |:--- |
| BlobSource에서 skipHeaderLineCount |skipLineCount 및 firstRowAsHeader. 줄을 먼저 건너뛴 다음 첫 번째 행을 헤더로 읽습니다. |
| BlobSource에서 treatEmptyAsNull |입력 데이터 집합에서 treatEmptyAsNull |
| BlobSink에서 blobWriterAddHeader |출력 데이터 집합에서 firstRowAsHeader |

이러한 속성에 대한 자세한 내용은 [TextFormat 지정](#specifying-textformat) 섹션을 참조하세요.    

### <a name="recursive-and-copybehavior-examples"></a>recursive 및 copyBehavior 예제
이 섹션에서는 다양한 recursive 및 copyBehavior 값 조합에 대한 복사 작업의 결과 동작을 설명합니다.

| recursive | copyBehavior | 결과 동작 |
| --- | --- | --- |
| true |preserveHierarchy |다음 구조를 가진 원본 폴더 Folder1의 경우:  <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>Folder1 대상 폴더가 다음과 같이 원본 폴더와 동일한 구조로 만들어집니다.<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5. |
| true |flattenHierarchy |다음 구조를 가진 원본 폴더 Folder1의 경우:  <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>Folder1 대상 폴더가 다음과 같은 구조로 만들어집니다. <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1에 대해 자동으로 생성된 이름<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2에 대해 자동 생성된 이름<br/>&nbsp;&nbsp;&nbsp;&nbsp;File3에 대해 자동 생성된 이름<br/>&nbsp;&nbsp;&nbsp;&nbsp;File4에 대해 자동 생성된 이름<br/>&nbsp;&nbsp;&nbsp;&nbsp;File5에 대해 자동 생성된 이름 |
| true |mergeFiles |다음 구조를 가진 원본 폴더 Folder1의 경우:  <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>Folder1 대상 폴더가 다음과 같은 구조로 만들어집니다. <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1, File2, File3, File4 및 File5의 파일 내용이 자동 생성된 파일 이름을 사용하는 파일 하나로 병합됩니다. |
| false |preserveHierarchy |다음 구조를 가진 원본 폴더 Folder1의 경우:  <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>Folder1 대상 폴더가 다음과 같은 구조로 만들어집니다.<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/><br/><br/>File3, File4, File5를 가진 Subfolder1은 선택되지 않습니다. |
| false |flattenHierarchy |다음 구조를 가진 원본 폴더 Folder1의 경우: <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>Folder1 대상 폴더가 다음과 같은 구조로 만들어집니다.<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1에 대해 자동으로 생성된 이름<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2에 대해 자동 생성된 이름<br/><br/><br/>File3, File4, File5를 가진 Subfolder1은 선택되지 않습니다. |
| false |mergeFiles |다음 구조를 가진 원본 폴더 Folder1의 경우: <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>Folder1 대상 폴더가 다음과 같은 구조로 만들어집니다.<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1과 File2의 파일 내용이 자동 생성된 파일 이름을 사용하는 파일 하나로 병합됩니다. File1에 대해 자동으로 생성된 이름<br/><br/>File3, File4, File5를 가진 Subfolder1은 선택되지 않습니다. |

[!INCLUDE [data-factory-structure-for-rectangualr-datasets](../../includes/data-factory-structure-for-rectangualr-datasets.md)]

[!INCLUDE [data-factory-type-conversion-sample](../../includes/data-factory-type-conversion-sample.md)]

[!INCLUDE [data-factory-column-mapping](../../includes/data-factory-column-mapping.md)]

## <a name="performance-and-tuning"></a>성능 및 튜닝
Azure Data Factory의 데이터 이동(복사 작업) 성능에 영향을 주는 주요 요소 및 최적화하는 다양한 방법에 대해 알아보려면 [복사 작업 성능 및 조정 가이드](data-factory-copy-activity-performance.md)를 참조하세요.



<!--HONumber=Nov16_HO3-->


