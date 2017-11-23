---
title: "Azure Data Factory에서 조회 작업 | Microsoft Docs"
description: "조회 작업을 사용하여 외부 소스에서 값을 조회하는 방법을 알아봅니다. 이 출력을 다음 작업에서 추가로 참조할 수 있습니다."
services: data-factory
documentationcenter: 
author: sharonlo101
manager: jhubbard
editor: shlo
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/31/2017
ms.author: spelluru
ms.openlocfilehash: d498705ef7f714b4f15b8d2722053bf3081b5045
ms.sourcegitcommit: a036a565bca3e47187eefcaf3cc54e3b5af5b369
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/17/2017
---
# <a name="lookup-activity-in-azure-data-factory"></a>Azure Data Factory에서 조회 작업
조회 작업은 모든 외부 소스에서 레코드/테이블 이름/값을 읽거나 조회하는 데 사용할 수 있습니다. 이 출력을 다음 작업에서 추가로 참조할 수 있습니다. 

조회에 대해 현재 지원되는 데이터 원본은 다음과 같습니다.
- Azure Blob의 JSON 파일
- 온-프레미스 JSON 파일
- Azure SQL Database(쿼리에서 변환된 JSON 데이터)
- Azure Table Storage(쿼리에서 변환된 JSON 데이터)

조회 작업은 구성 파일이나 데이터 소스에서 파일/레코드/테이블의 목록을 동적으로 검색하려는 경우에 유용합니다. 작업의 출력은 해당 항목에 대해서만 특정 처리 작업을 수행하기 위해 다른 작업에서 추가로 사용할 수 있습니다.

> [!NOTE]
> 이 문서는 현재 미리 보기 상태인 Data Factory 버전 2에 적용됩니다. GA(일반 공급) 상태인 Data Factory 버전 1 서비스를 사용 중인 경우 [Data Factory V1 설명서](v1/data-factory-introduction.md)를 참조하세요.


## <a name="example"></a>예제
이 예제에서 복사 작업은 Azure SQL Database에 있는 SQL 테이블의 데이터를 Azure Blob Storage에 복사합니다. SQL 테이블의 이름은 Blob Storage의 JSON 파일에 저장됩니다. 조회 작업은 런타임 시 테이블 이름을 조회합니다. 이 방법을 사용하면 파이프라인/데이터 집합을 다시 배포하지 않아도 동적으로 JSON을 수정할 수 있습니다. 

### <a name="pipeline"></a>파이프라인
이 파이프라인은 **조회** 및 **복사**, 두 작업을 포함합니다. 

- 조회 작업은 Azure Blob Storage의 위치를 참조하는 LookupDataset를 사용하도록 구성됩니다. 조회 작업은 이 위치에 있는 JSON 파일에서 SQL 테이블의 이름을 읽습니다. 
- 복사 작업은 조회 작업의 출력(SQL 테이블의 이름)을 사용합니다. 원본 데이터 집합(SourceDataset)의 tableName은 조회 작업의 출력을 사용하도록 구성됩니다. 복사 작업은 SQL 테이블의 데이터를 Azure Blob Storage에서 SinkDataset로 지정된 위치에 복사합니다. 


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
                        "sqlReaderQuery": "select * from @{activity('LookupActivity').output.tableName}" 
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
조회 데이터 집합은 AzureStorageLinkedService로 지정된 Azure Storage의 lookup 폴더에 있는 sourcetable.json 파일을 참조합니다. 

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
원본 데이터 집합은 SQL 테이블의 이름인 조회 작업의 출력을 사용합니다. 복사 작업은 SQL 테이블의 데이터를 Azure Blob Storage에서 싱크 데이터 집합으로 지정된 위치에 복사합니다. 

```json
{
    "name": "SourceDataset",
    "properties": {
        "type": "AzureSqlTable",
        "typeProperties":{
            "tableName": "@{activity('LookupActivity').output.tableName}"
        },
        "linkedServiceName": {
            "referenceName": "AzureSqlLinkedService",
            "type": "LinkedServiceReference"
        }
    }
}
```

### <a name="sink-dataset-for-the-copy-activity"></a>복사 작업의 싱크 데이터 집합
복사 작업은 SQL 테이블의 데이터를 Azure Storage에서 AzureStorageLinkedService로 지정된 csv 폴더의 filebylookup.csv 파일에 복사합니다. 

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
이 저장소 계정에는 SQL 테이블의 이름이 있는 JSON 파일이 있습니다. 

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
이 Azure SQL Database에는 Blob Storage에 복사할 데이터가 있습니다. 

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



## <a name="type-properties"></a>형식 속성
이름 | 설명 | 형식 | 필수
---- | ----------- | ---- | --------
데이터 집합 | 데이터 집합의 특성은 조회에 대한 데이터 집합 참조를 제공하는 것입니다. 현재 지원되는 데이터 집합 형식은 다음과 같습니다.<ul><li>FileShareDataset</li><li>AzureBlobDataset</li><li>AzureSqlTableDataset</li><li>AzureTableDataset</li> | 키/값 쌍 | 예
원본 | 데이터 집합별 원본 속성, 복사 작업 원본과 동일 | 키/값 쌍 | 아니요
firstRowOnly | 첫 번째 행 또는 모든 행을 반환합니다. | 부울 | 아니요

## <a name="next-steps"></a>다음 단계
Data Factory에서 지원하는 다른 제어 흐름 작업을 참조하세요. 

- [파이프라인 실행 작업](control-flow-execute-pipeline-activity.md)
- [ForEach 작업](control-flow-for-each-activity.md)
- [메타데이터 작업 가져오기](control-flow-get-metadata-activity.md)
- [웹 작업](control-flow-web-activity.md)
