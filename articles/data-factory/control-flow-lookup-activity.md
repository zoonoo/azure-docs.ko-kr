---
title: Azure Data Factory에서 조회 작업 | Microsoft Docs
description: 조회 작업을 사용하여 외부 소스의 값을 조회하는 방법을 배웁니다. 이 출력을 다음 작업에서 추가로 참조할 수 있습니다.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
editor: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/27/2018
ms.author: shlo
ms.openlocfilehash: 7d6abb72fca71c213f9810784581a9af2dafb3a2
ms.sourcegitcommit: c3d53d8901622f93efcd13a31863161019325216
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2018
---
# <a name="lookup-activity-in-azure-data-factory"></a>Azure Data Factory에서 조회 작업
조회 작업을 사용하여 외부 소스의 레코드, 테이블 이름 또는 값을 읽거나 조회할 수 있습니다. 이 출력을 다음 작업에서 추가로 참조할 수 있습니다. 

조회 작업은 구성 파일 또는 데이터 원본에서 파일,레코드 또는 테이블 목록을 동적으로 검색하려는 경우에 유용합니다. 작업의 출력은 해당 항목에 대해서만 특정 처리 작업을 수행하기 위해 다른 작업에서 추가로 사용할 수 있습니다.

> [!NOTE]
> 이 문서는 현재 미리 보기 상태인 Azure Data Factory 버전 2에 적용됩니다. GA(일반 공급) 상태인 Data Factory 버전 1 서비스를 사용 중인 경우 [Data Factory 버전 1 설명서](v1/data-factory-introduction.md)를 참조하세요.

## <a name="supported-capabilities"></a>지원되는 기능

다음은 현재 조회를 지원하는 데이터 원본입니다.

- Amazon Redshift
- Azure Blob 저장소
- Azure Cosmos DB
- Azure Data Lake Store
- Azure 파일 저장소
- Azure SQL Database
- Azure SQL Data Warehouse
- Azure 테이블 저장소
- Dynamics 365
- Dynamics CRM
- 파일 시스템
- PostgreSQL
- Salesforce
- Salesforce 서비스 클라우드
- SFTP
- SQL Server

조회 작업에서 반환되는 최대 행 수는 **5000**이고, 최대 크기는 **10MB**입니다.

## <a name="syntax"></a>구문

```json
{
    "name": "LookupActivity",
    "type": "Lookup",
    "typeProperties": {
        "source": {
            "type": "<source type>"
            <additional source specific properties (optional)>
        },
        "dataset": { 
            "referenceName": "<source dataset name>",
            "type": "DatasetReference"
        },
        "firstRowOnly": false
    }
}
```

## <a name="type-properties"></a>형식 속성
Name | 설명 | 유형 | Required?
---- | ----------- | ---- | --------
dataset | 조회를 위한 데이터 집합 참조를 제공합니다. 자세한 내용은 해당하는 각 커넥터 문서의 "데이터 집합 속성" 섹션에서 확인하세요. | 키/값 쌍 | 예
원본 | 복사 작업 원본과 동일한 데이터 집합 관련 원본 속성을 포함하고 있습니다. 자세한 내용은 해당하는 각 커넥터 문서의 "복사 작업 속성" 섹션에서 확인하세요. | 키/값 쌍 | 예
firstRowOnly | 첫 번째 행만 반환할 것인지 아니면 모든 행을 반환할 것인지 여부를 나타냅니다. | BOOLEAN | 번호 기본값은 `true`입니다.

다음 사항에 유의하세요.

1. ByteArray 형식의 원본 열은 지원되지 않습니다.
2. 구조체는 데이터 집합 정의에서 지원되지 않습니다. 특히 텍스트 서식 파일의 경우 헤더 행을 사용해 열 이름을 제공할 수 있습니다.

## <a name="use-the-lookup-activity-result-in-a-subsequent-activity"></a>조회 작업 결과를 후속 작업에 사용

조회 결과는 작업 실행 결과의 `output` 섹션에 반환됩니다.

* **`firstRowOnly`가 `true`(기본값)로 설정되면** 출력 형식은 다음 코드와 같습니다. 조회 결과는 고정된 `firstRow` 키 아래에 있습니다. 결과를 후속 작업에 사용하려면 `@{activity('MyLookupActivity').output.firstRow.TableName}` 패턴을 사용합니다.

    ```json
    {
        "firstRow":
        {
            "Id": "1",
            "TableName" : "Table1"
        }
    }
    ```

* **`firstRowOnly`가 `false`로 설정되면** 출력 형식은 다음 코드와 같습니다. `count` 필드는 반환되는 레코드 수를 나타내며, 구체적인 값은 고정된 `value` 배열 아래에 표시됩니다. 이 경우 조회 작업 후에는 주로 [Foreach 작업](control-flow-for-each-activity.md)이 이어집니다. `@activity('MyLookupActivity').output.value` 패턴을 사용하여 `value` 배열을 ForEach 작업 `items` 필드에 전달할 수 있습니다. `value` 배열의 요소에 액세스하려면 `@{activity('lookupActivity').output.value[zero based index].propertyname}` 구문을 따릅니다. 예: `@{activity('lookupActivity').output.value[0].tablename}`.

    ```json
    {
        "count": "2",
        "value": [
            {
                "Id": "1",
                "TableName" : "Table1"
            },
            {
                "Id": "2",
                "TableName" : "Table2"
            }
        ]
    } 
    ```

## <a name="example"></a>예
이 예에서 복사 작업은 Azure SQL Database 인스턴스의 SQL 테이블에서 Azure Blob Storage로 데이터를 복사합니다. SQL 테이블의 이름은 Blob 저장소의 JSON 파일에 저장됩니다. 조회 작업은 런타임 시 테이블 이름을 조회합니다. 이 방법을 사용하면 파이프라인 또는 데이터집합을 다시 배포하지 않고도 동적으로 JSON을 수정할 수 있습니다. 

이 예제는 첫 번째 행만 조회합니다. 모든 행을 조회하고 결과를 ForEach 작업과 연결하려면 [Azure Data Factory를 사용하여 여러 테이블 대량 복사](tutorial-bulk-copy.md)의 샘플을 참조하세요.

### <a name="pipeline"></a>파이프라인
이 파이프라인은 *조회* 및 *복사* 작업을 포함하고 있습니다. 

- 조회 작업은 Azure Blob Storage의 위치를 참조하는 LookupDataset를 사용하도록 구성됩니다. 조회 작업은 이 위치에 있는 JSON 파일에서 SQL 테이블의 이름을 읽습니다. 
- 복사 작업은 조회 작업의 출력(SQL 테이블의 이름)을 사용합니다. 원본 데이터 집합(SourceDataset)의 tableName 속성은 조회 작업의 출력을 사용하도록 구성됩니다. 복사 작업은 SQL 테이블의 데이터를 SinkDataset 속성을 통해 지정된 Azure Blob Storage의 위치로 복사합니다. 


```json
{
    "name": "LookupPipelineDemo",
    "properties": {
        "activities": [
            {
                "name": "LookupActivity",
                "type": "Lookup",
                "typeProperties": {
                    "source": {
                        "type": "BlobSource"
                    },
                    "dataset": { 
                        "referenceName": "LookupDataset", 
                        "type": "DatasetReference" 
                    }
                }
            },
            {
                "name": "CopyActivity",
                "type": "Copy",
                "typeProperties": {
                    "source": { 
                        "type": "SqlSource", 
                        "sqlReaderQuery": "select * from @{activity('LookupActivity').output.firstRow.tableName}" 
                    },
                    "sink": { 
                        "type": "BlobSink" 
                    }
                },                
                "dependsOn": [ 
                    { 
                        "activity": "LookupActivity", 
                        "dependencyConditions": [ "Succeeded" ] 
                    }
                 ],
                "inputs": [ 
                    { 
                        "referenceName": "SourceDataset", 
                        "type": "DatasetReference" 
                    } 
                ],
                "outputs": [ 
                    { 
                        "referenceName": "SinkDataset", 
                        "type": "DatasetReference" 
                    } 
                ]
            }
        ]
    }
}
```

### <a name="lookup-dataset"></a>조회 데이터 집합
조회 데이터 집합은 AzureStorageLinkedService 형식으로 지정된 Azure Storage 조회 폴더의 *sourcetable.json* 파일을 참조합니다. 

```json
{
    "name": "LookupDataset",
    "properties": {
        "type": "AzureBlob",
        "typeProperties": {
            "folderPath": "lookup",
            "fileName": "sourcetable.json",
            "format": {
                "type": "JsonFormat",
                "filePattern": "SetOfObjects"
            }
        },
        "linkedServiceName": {
            "referenceName": "AzureStorageLinkedService",
            "type": "LinkedServiceReference"
        }
    }
}
```

### <a name="source-dataset-for-the-copy-activity"></a>복사 작업의 원본 데이터 집합
원본 데이터 집합은 SQL 테이블의 이름인 조회 작업의 출력을 사용합니다. 복사 작업은 이 SQL 테이블의 데이터를 싱크 데이터 집합을 통해 지정된 Azure Blob Storage의 위치로 복사합니다. 

```json
{
    "name": "SourceDataset",
    "properties": {
        "type": "AzureSqlTable",
        "typeProperties":{
            "tableName": "@{activity('LookupActivity').output.firstRow.tableName}"
        },
        "linkedServiceName": {
            "referenceName": "AzureSqlLinkedService",
            "type": "LinkedServiceReference"
        }
    }
}
```

### <a name="sink-dataset-for-the-copy-activity"></a>복사 작업의 싱크 데이터 집합
복사 작업은 SQL 테이블의 데이터를 AzureStorageLinkedService 속성을 통해 지정된 Azure 저장소의 *csv* 폴더에 있는 *filebylookup.csv* 파일로 복사합니다. 

```json
{
    "name": "SinkDataset",
    "properties": {
        "type": "AzureBlob",
        "typeProperties": {
            "folderPath": "csv",
            "fileName": "filebylookup.csv",
            "format": {
                "type": "TextFormat"                                                                    
            }
        },
        "linkedServiceName": {
            "referenceName": "AzureStorageLinkedService",
            "type": "LinkedServiceReference"
        }
    }
}
```

### <a name="azure-storage-linked-service"></a>Azure Storage 연결된 서비스
이 저장소 계정에는 SQL 테이블의 이름을 사용하는 JSON 파일이 포함되어 있습니다. 

```json
{
    "properties": {
        "type": "AzureStorage",
        "typeProperties": {
            "connectionString": {
                "value": "DefaultEndpointsProtocol=https;AccountName=<StorageAccountName>;AccountKey=<StorageAccountKey>",
                "type": "SecureString"
            }
        }
    },
        "name": "AzureStorageLinkedService"
}
```

### <a name="azure-sql-database-linked-service"></a>Azure SQL Database 연결된 서비스
이 Azure SQL Database 인스턴스에는 Blob 저장소로 복사할 데이터가 포함되어 있습니다. 

```json
{
    "name": "AzureSqlLinkedService",
    "properties": {
        "type": "AzureSqlDatabase",
        "description": "",
        "typeProperties": {
            "connectionString": {
                "value": "Server=<server>;Initial Catalog=<database>;User ID=<user>;Password=<password>;",
                "type": "SecureString"
            }
        }
    }
}
```

### <a name="sourcetablejson"></a>sourcetable.json

#### <a name="set-of-objects"></a>개체의 집합

```json
{
  "Id": "1",
  "tableName": "Table1",
}
{
   "Id": "2",
  "tableName": "Table2",
}
```

#### <a name="array-of-objects"></a>개체의 배열

```json
[ 
    {
        "Id": "1",
          "tableName": "Table1",
    }
    {
        "Id": "2",
        "tableName": "Table2",
    }
]
```

## <a name="next-steps"></a>다음 단계
Data Factory에서 지원하는 다른 제어 흐름 작업을 살펴봅니다. 

- [파이프라인 실행 작업](control-flow-execute-pipeline-activity.md)
- [ForEach 작업](control-flow-for-each-activity.md)
- [메타데이터 가져오기 작업](control-flow-get-metadata-activity.md)
- [웹 작업](control-flow-web-activity.md)
