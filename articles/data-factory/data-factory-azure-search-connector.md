---
title: "데이터 팩터리를 사용하여 Search 인덱스에 데이터 푸시 | Microsoft Docs"
description: "Azure 데이터 팩터리를 사용하여 Azure Search 인덱스에 데이터를 푸시하는 방법을 알아봅니다."
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: monicar
ms.assetid: f8d46e1e-5c37-4408-80fb-c54be532a4ab
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/20/2016
ms.author: jingwang
translationtype: Human Translation
ms.sourcegitcommit: 55c988bf74ff0f2e519e895a735dc68f3dc99855
ms.openlocfilehash: e2deed13106db9467eef181f25a0a226034df5a2

---

# <a name="push-data-to-an-azure-search-index-by-using-azure-data-factory"></a>Azure 데이터 팩터리를 사용하여 Azure Search 인덱스에 데이터 푸시
이 문서에서는 복사 작업을 사용하여 데이터 팩터리 서비스에서 지원하는 온-프레미스 데이터 저장소에서 Azure Search 인덱스로 데이터를 푸시하는 방법을 설명합니다. 지원되는 원본 데이터 저장소는 [지원되는 원본 및 싱크](data-factory-data-movement-activities.md#supported-data-stores-and-formats) 테이블의 원본 열에 나열됩니다. 이 문서는 복사 작업 및 지원되는 데이터 저장소 조합을 사용하여 데이터 이동의 일반적인 개요를 보여주는 [데이터 이동 활동](data-factory-data-movement-activities.md) 문서를 작성합니다.

## <a name="enabling-connectivity"></a>연결 사용
데이터 팩터리 서비스에서 온-프레미스 데이터 저장소에 연결하도록 허용하려면 온-프레미스 환경에 데이터 관리 게이트웨이를 설치해야 합니다. 원본 데이터 저장소를 호스트하는 동일한 컴퓨터에 게이트웨이를 설치하거나, 데이터 저장소와의 리소스 경합을 방지하기 위해 별도의 컴퓨터에 게이트웨이를 설치할 수 있습니다.

데이터 관리 게이트웨이는 온-프레미스 데이터 원본을 클라우드 서비스에 안전하고 관리되는 방식으로 연결합니다. 데이터 관리 게이트웨이에 대한 자세한 내용은 [온-프레미스 및 클라우드 간 데이터 이동](data-factory-move-data-between-onprem-and-cloud.md) 을 참조하세요.

## <a name="copy-data-wizard"></a>데이터 복사 마법사
지원되는 원본 데이터 저장소 중 하나에서 Azure Search로 데이터를 복사하는 파이프라인을 만드는 가장 쉬운 방법은 데이터 복사 마법사를 사용하는 것입니다. 빠른 연습을 위해서는 [자습서: 복사 마법사를 사용하여 파이프라인 만들기](data-factory-copy-data-wizard-tutorial.md)를 참조하세요.

다음 예제에서는 [Azure 포털](data-factory-copy-activity-tutorial-using-azure-portal.md), [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md), 또는 [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md)을 사용하여 파이프라인을 만드는 데 사용할 수 있는 샘플 JSON 정의를 제공하며, 이 샘플은 온-프레미스 SQL Server에서 Azure Search 인덱스로 데이터를 복사하는 방법을 보여 줍니다. 그러나 Azure Data Factory의 복사 작업을 사용하여 [여기](data-factory-data-movement-activities.md#supported-data-stores-and-formats)에 설명된 온-프레미스 데이터 저장소 중 하나에서 데이터를 복사할 수 있습니다.   

## <a name="sample-copy-data-from-on-premises-sql-server-to-azure-search-index"></a>샘플: 온-프레미스 SQL Server에서 Azure Search 인덱스로 데이터를 복사하는 방법

다음 샘플은 다음과 같은 내용을 보여 줍니다.

1.    [AzureSearch](#azure-search-linked-service-properties) 형식의 연결된 서비스
2.    [OnPremisesSqlServer](data-factory-sqlserver-connector.md#sql-server-linked-service-properties)형식의 연결된 서비스
3.    [SqlServerTable](data-factory-sqlserver-connector.md#sql-server-dataset-type-properties) 형식의 입력 [데이터 집합](data-factory-create-datasets.md)
4.    [AzureSearchIndex](#azure-search-index-dataset-properties) 형식의 출력 [데이터 집합](data-factory-create-datasets.md)
4.    [SqlSource](data-factory-sqlserver-connector.md#sql-server-copy-activity-type-properties) 및 [AzureSearchIndexSink](#azure-search-index-sink-properties)를 사용하는 복사 작업의 [파이프라인](data-factory-create-pipelines.md)

이 샘플은 온-프레미스 SQL Server 데이터베이스에서 Azure 검색 인덱스로 1시간마다 시계열 데이터를 복사합니다. 이 샘플에 사용된 JSON 속성은 샘플 다음에 나오는 섹션에서 설명합니다.

첫 번째 단계로 온-프레미스 컴퓨터에서 데이터 관리 게이트웨이를 설정합니다. 해당 지침은 [온-프레미스 위치와 클라우드 간에 데이터 이동](data-factory-move-data-between-onprem-and-cloud.md) 문서에 나와 있습니다.

**Azure Search 연결된 서비스:**

```JSON
{
    "name": "AzureSearchLinkedService",
       "properties": {
        "type": "AzureSearch",
           "typeProperties": {
            "url": "https://<service>.search.windows.net",
            "key": "<AdminKey>"
        }
       }
}
```

**SQL Server 연결된 서비스**

```JSON
{
  "Name": "SqlServerLinkedService",
  "properties": {
    "type": "OnPremisesSqlServer",
    "typeProperties": {
      "connectionString": "Data Source=<servername>;Initial Catalog=<databasename>;Integrated Security=False;User ID=<username>;Password=<password>;",
      "gatewayName": "<gatewayname>"
    }
  }
}
```

**SQL Server 입력 데이터 집합**

샘플은 Azure SQL에서 만든 "MyTable" 테이블에 시계열 데이터에 대한 "timestampcolumn"이라는 열이 포함되어 있다고 가정합니다. 단일 데이터 집합을 사용하여 동일한 데이터베이스 내 여러 테이블에 대해 쿼리를 실행할 수 있지만, 데이터 집합의 tableName typeProperty에 대해서는 단일 테이블이 사용되어야 합니다.

"external": "true"를 설정하면 데이터 집합이 Data Factory의 외부에 있으며 Data Factory의 활동에 의해 생성되지 않는다는 정보가 Data Factory 서비스에 전달됩니다.

```JSON
{
  "name": "SqlServerDataset",
  "properties": {
    "type": "SqlServerTable",
    "linkedServiceName": "SqlServerLinkedService",
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

**Azure Search 출력 데이터 집합:**

이 샘플은 **products**라는 Azure 검색 인덱스에 데이터를 복사합니다. Data Factory는 인덱스를 만들지 않습니다. 이 샘플을 테스트하려면 이 이름의 인덱스를 만듭니다. 입력 데이터 집합과 동일한 개수의 열이 있는 Azure 검색 인덱스를 만듭니다. 새 항목은 1시간마다 Azure 검색 인덱스에 추가됩니다.

```JSON
{
    "name": "AzureSearchIndexDataset",
    "properties": {
        "type": "AzureSearchIndex",
        "linkedServiceName": "AzureSearchLinkedService",
         "typeProperties" : {
            "indexName": "products",
        },
        "availability": {
            "frequency": "Minute",
            "interval": 15
        }
   }
}
```

**복사 작업을 포함하는 파이프라인:**

파이프라인은 입력 및 출력 데이터 집합을 사용하도록 구성된 복사 작업을 포함하고 매시간 실행하도록 예약됩니다. 파이프라인 JSON 정의에서 **source** 형식은 **SqlSource**로 설정되고 **sink** 형식은 **AzureSearchIndexSink**로 설정됩니다. **SqlReaderQuery** 속성에 지정된 SQL 쿼리는 과거 한 시간에서 복사할 데이터를 선택합니다.

```JSON
{  
    "name":"SamplePipeline",
    "properties":{  
    "start":"2014-06-01T18:00:00",
    "end":"2014-06-01T19:00:00",
    "description":"pipeline for copy activity",
    "activities":[  
      {
        "name": "SqlServertoAzureSearchIndex",
        "description": "copy activity",
        "type": "Copy",
        "inputs": [
          {
            "name": " SqlServerInput"
          }
        ],
        "outputs": [
          {
            "name": "AzureSearchIndexDataset"
          }
        ],
        "typeProperties": {
          "source": {
            "type": "SqlSource",
            "SqlReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd HH:mm}\\' AND timestampcolumn < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
          },
          "sink": {
            "type": "AzureSearchIndexSink"
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

클라우드 데이터 저장소에서 Azure Search로 데이터를 복사하는 경우 `executionLocation` 속성이 필수입니다. 아래는 복사 작업 `typeProperties`에서 필요한 변경 내용을 예제로 보여줍니다. 지원되는 값과 자세한 정보는[클라우드 데이터 저장소 간의 데이터 복사](data-factory-data-movement-activities.md#global)섹션을 확인합니다.

```JSON
"typeProperties": {
  "source": {
    "type": "BlobSource"
  },
  "sink": {
    "type": "AzureSearchIndexSink"
  },
  "executionLocation": "West US"
}
```

## <a name="azure-search-linked-service-properties"></a>Azure Search 연결된 서비스 속성

다음 표에는 Azure Search 연결된 서비스에 지정된 JSON 요소에 대한 설명이 나와 있습니다.

| 속성 | 설명 | 필수 |
| -------- | ----------- | -------- |
| type | 형식 속성은 **AzureSearch**로 설정되어야 합니다. | 예 |
| URL | Azure Search 서비스의 URL입니다. | 예 |
| key | Azure Search 서비스의 관리자 키입니다. | 예 |

## <a name="azure-search-index-dataset-properties"></a>Azure 검색 인덱스를 데이터 집합 속성

데이터 집합 정의에 사용할 수 있는 섹션 및 속성의 전체 목록은 [데이터 집합 만들기](data-factory-create-datasets.md) 문서를 참조하세요. 구조, 가용성 및 JSON 데이터 집합의 정책과 같은 섹션이 모든 데이터 집합 형식에 대해 유사합니다. **typeProperties** 섹션은 데이터 집합의 각 형식마다 다릅니다. **AzureSearchIndex** 데이터 집합 형식의 데이터 집합에 대한 typeProperties 섹션에는 다음 속성이 있습니다.

| 속성 | 설명 | 필수 |
| -------- | ----------- | -------- |
| type | 형식 속성은 **AzureSearchIndex**로 설정되어야 합니다.| 예 |
| indexName | Azure 검색 인덱스의 이름입니다. Data Factory는 인덱스를 만들지 않습니다. Azure Search에는 인덱스가 있어야 합니다. | 예 |


## <a name="azure-search-index-sink-properties"></a>Azure 검색 인덱스 싱크 속성
활동 정의에 사용할 수 있는 섹션 및 속성의 전체 목록은 [파이프라인 만들기](data-factory-create-pipelines.md) 문서를 참조하세요. 이름, 설명, 입력/출력 테이블, 다양한 정책 등의 속성은 모든 형식의 활동에 사용할 수 있습니다. 반면 활동의 typeProperties 섹션에서 사용할 수 있는 속성은 각 활동 형식에 따라 다릅니다. 복사 활동의 경우 이러한 속성은 소스 및 싱크의 형식에 따라 달라집니다.

복사 작업의 경우 원본이 **AzureSearchIndexSink** 형식이면 typeProperties 섹션에서 다음과 같은 속성을 사용할 수 있습니다.

| 속성 | 설명 | 허용되는 값 | 필수 |
| -------- | ----------- | -------------- | -------- |
| WriteBehavior | 문서가 인덱스에 이미 있는 경우 병합할지 또는 바꿀지를 지정합니다. [WriteBehavior 속성](#writebehavior-property)을 참조하세요.| 병합(기본값)<br/>업로드| 아니요 |
| writeBatchSize | 버퍼 크기가 writeBatchSize에 도달한 경우 Azure Search 인덱스에 데이터를 업로드합니다. 자세한 내용은 [WriteBatchSize 속성](#writebatchsize-property)을 참조하세요. | 1~1,000입니다. 기본값은 1,000입니다. | 아니요 |

### <a name="writebehavior-property"></a>WriteBehavior 속성
데이터를 쓸 때 AzureSearchSink가 삽입됩니다. 즉, 문서를 작성할 때 문서 키가 Azure Search 인덱스가 이미 있는 경우 Azure Search는 충돌 예외를 throw하지 않는 대신 기존 문서를 업데이트합니다.

AzureSearchSink는 AzureSearch SDK를 사용하여 다음 두 가지 삽입 동작을 제공합니다.

- **병합**: 새 문서의 모든 열을 기존 문서와 결합합니다. 새 문서에서 null 값을 가진 열의 경우 기존 문서의 값이 유지됩니다.
- **업로드**: 새 문서가 기존 문서를 대체합니다. 새 문서에 지정되지 않은 열의 경우 기본 문서에 null이 아닌 값이 있는지 여부에 상관없이 값이 null로 설정됩니다.

기본 동작은 **병합**입니다.

### <a name="writebatchsize-property"></a>writeBatchSize 속성
Azure Search 서비스는 일괄 처리로 문서 작성을 지원합니다. 일괄 처리는 1~1,000개의 동작을 포함할 수 있습니다. 하나의 동작에서 하나의 문서를 처리하여 업로드/병합 작업을 수행합니다.

### <a name="data-type-support"></a>데이터 형식 지원
다음 표에서는 Azure Search 데이터 형식이 지원되는지 여부를 지정합니다.

| Azure Search 데이터 형식 | Azure Search 싱크에서 지원됨 |
| ---------------------- | ------------------------------ |
| String | Y |
| Int32 | Y |
| Int64 | Y |
| Double | Y |
| Boolean | Y |
| DataTimeOffset | Y |
| 문자열 배열 | N |
| GeographyPoint | N |

## <a name="copy-from-a-cloud-source"></a>클라우드 원본에서 복사
클라우드 데이터 저장소에서 Azure Search로 데이터를 복사하는 경우 `executionLocation` 속성이 필수입니다. 아래는 복사 작업 `typeProperties`에서 필요한 변경 내용을 예제로 보여줍니다. 지원되는 값과 자세한 정보는[클라우드 데이터 저장소 간의 데이터 복사](data-factory-data-movement-activities.md#global)섹션을 확인합니다.

```JSON
"typeProperties": {
  "source": {
    "type": "BlobSource"
  },
  "sink": {
    "type": "AzureSearchIndexSink"
  },
  "executionLocation": "West US"
}
```

## <a name="specifying-structure-definition-for-rectangular-datasets"></a>사각형 데이터 집합의 구조 정의 지정
데이터 집합 JSON의 구조 섹션은 사각형 테이블(행 및 열 포함)에 대한 **선택적** 섹션으로, 해당 테이블에 대한 열 모음을 포함합니다. 형식 변환에 필요한 형식 정보를 제공하거나 열 매핑을 수행하기 위해 구조 섹션을 사용합니다. 다음 섹션에서는 이러한 기능을 자세히 설명합니다.

각 열에는 다음 속성이 포함됩니다.

| 속성 | 설명 | 필수 |
| -------- | ----------- | -------- |
| name | 열의 이름입니다. | 예 |
| type | 열의 데이터 형식입니다. 형식 정보를 지정해야 할 시기에 대한 자세한 내용은 [형식 변환](#type-conversion-sample) 섹션을 참조하세요. | 아니요 |
| culture | 지정된 형식이 .NET 형식 `Datetime` 또는 `Datetimeoffset`일 때 사용할 .NET 기반 culture입니다. 기본값은 `en-us`입니다.  | 아니요 |
| format | 지정된 형식이 .NET 형식 `Datetime` 또는 `Datetimeoffset`일 때 사용할 형식 문자열입니다. | 아니요 |

다음 샘플에서는 3개의 열 즉, `userid`, `name` 및 `lastlogindate`가 있는 테이블에 대한 구조 섹션 JSON을 보여 줍니다.

```JSON
"structure":
[
    { "name": "userid"},
    { "name": "name"},
    { "name": "lastlogindate"}
],
```
"structure" 정보를 포함할 시기 및 **structure** 섹션에 포함할 항목에 대해서는 다음 지침을 따르세요.

- 데이터 스키마 및 형식 정보를 데이터 자체와 함께 저장하는 **구조화된 데이터 원본**(예: SQL Server, Oracle, Azure 테이블 등)의 경우 싱크의 특정 열에 특정 원본 열을 매핑하고 해당 이름이 동일하지 않은 경우에만 "structure" 섹션을 지정합니다. 열 매핑 섹션에서 세부 정보를 참조하세요.

    위에서 설명했듯이 형식 정보는 "structure" 섹션의 선택 사항입니다. 구조화된 원본의 경우는 데이터 저장소에서 데이터 집합 정의의 일부로 형식 정보를 사용할 수 있으므로 "structure" 섹션을 포함할 때는 형식 정보를 포함하지 않아야 합니다.
- **읽기 데이터 원본(특히 Azure Blob)의 스키마인 경우** 스키마 또는 형식 정보를 데이터와 함께 저장하지 않고도 데이터를 저장할 수 있습니다. 이러한 유형의 데이터 원본에서 다음 2가지 경우는 "structure"를 포함해야 합니다.
    - 원본 열을 싱크 열을 매핑합니다.
    - 데이터 집합이 복사 작업의 원본인 경우 "structure"에 형식 정보를 제공할 수 있습니다. Data Factory는 싱크에 대한 네이티브 형식으로 변환에 이 형식 정보를 사용합니다. 자세한 내용은 [Azure Blob 간 데이터 이동](data-factory-azure-blob-connector.md)을 참조하세요.

### <a name="supported-net-based-types"></a>지원되는 .NET 기반 형식
Data Factory는 Azure Blob과 같은 읽기 데이터 원본의 스키마에 대해 "structure"에 형식 정보를 제공하기 위해 다음과 같은 CLS 규격 .NET 기반 형식 값을 지원합니다.

- Int16
- Int32
- Int64
- Single
- Double
- DECIMAL
- Bool
- 문자열
- Datetime
- Datetimeoffset
- Timespan

Datetime 및 Datetimeoffset의 경우 사용자 지정 Datetime 문자열의 구문 분석을 용이하게 하려면 "culture" 및 "format" 문자열을 지정할 수도 있습니다. 다음 섹션에서 형식 변환 샘플을 참조하세요.


[!INCLUDE [data-factory-type-conversion-sample](../../includes/data-factory-type-conversion-sample.md)]

[!INCLUDE [data-factory-column-mapping](../../includes/data-factory-column-mapping.md)]


## <a name="performance-and-tuning"></a>성능 및 튜닝  
데이터 이동(복사 작업) 성능에 영향을 주는 주요 요소 및 최적화하는 다양한 방법에 대해 알아보려면 [복사 작업 성능 및 조정 가이드](data-factory-copy-activity-performance.md) 를 참조하세요.

## <a name="next-steps"></a>다음 단계
다음 문서를 참조하세요.

* [복사 작업 자습서](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) .



<!--HONumber=Feb17_HO2-->


