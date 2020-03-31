---
title: 복사 작업의 스키마 매핑
description: Azure Data Factory의 복사 작업에서 데이터를 복사할 때 원본 데이터의 스키마와 데이터 형식을 싱크 데이터에 매핑하는 방법에 대해 알아봅니다.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 02/13/2020
ms.author: jingwang
ms.openlocfilehash: 9ae07e2a471cc417b467092a2616a5a0cdafb1fe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79260814"
---
# <a name="schema-mapping-in-copy-activity"></a>복사 작업의 스키마 매핑

이 문서에서는 Azure Data Factory 복사 활동이 데이터 복사본을 실행할 때 원본 데이터에서 데이터 싱크에 이르는 스키마 매핑 및 데이터 형식 매핑을 수행하는 방법을 설명합니다.

## <a name="schema-mapping"></a>스키마 매핑

열 매핑은 원본에서 싱크로 데이터를 복사할 때 적용됩니다. 기본적으로 활동 **맵 소스 데이터를 복사하여 열 이름으로 가라앉습니다.** 필요에 따라 열 매핑을 사용자 지정하려면 [명시적](#explicit-mapping) 매핑을 지정할 수 있습니다. 보다 구체적으로 복사 작업:

1. 원본에서 데이터를 읽고 원본 스키마를 결정합니다.
2. 기본 열 매핑을 사용하여 이름으로 열을 매핑하거나 지정된 경우 명시적 열 매핑을 적용합니다.
3. 싱크에 데이터 쓰기

### <a name="explicit-mapping"></a>명시적 매핑

복사 활동 -> `translator`  ->  `mappings` 속성에 매핑할 열을 지정할 수 있습니다. 다음 예제에서는 구분된 텍스트에서 Azure SQL Database로 데이터를 복사하는 파이프라인의 복사 활동을 정의합니다.

```json
{
    "name": "CopyActivity",
    "type": "Copy",
    "inputs": [{
        "referenceName": "DelimitedTextInput",
        "type": "DatasetReference"
    }],
    "outputs": [{
        "referenceName": "AzureSqlOutput",
        "type": "DatasetReference"
    }],
    "typeProperties": {
        "source": { "type": "DelimitedTextSource" },
        "sink": { "type": "SqlSink" },
        "translator": {
            "type": "TabularTranslator",
            "mappings": [
                {
                    "source": {
                        "name": "UserId",
                        "type": "Guid"
                    },
                    "sink": {
                        "name": "MyUserId"
                    }
                }, 
                {
                    "source": {
                        "name": "Name",
                        "type": "String"
                    },
                    "sink": {
                        "name": "MyName"
                    }
                }, 
                {
                    "source": {
                        "name": "Group",
                        "type": "String"
                    },
                    "sink": {
                        "name": "MyGroup"
                    }
                }
            ]
        }
    }
}
```

다음 속성은 다음과 `translator`  ->  `mappings` > `source` 개체에서 `sink`지원됩니다.

| 속성 | 설명                                                  | 필수 |
| -------- | ------------------------------------------------------------ | -------- |
| name     | 소스 또는 싱크 열의 이름입니다.                           | yes      |
| ordinal  | 열 인덱스입니다. 1부터 시작합니다. <br>헤더 줄없이 구분된 텍스트를 사용할 때 적용및 필요합니다. | 예       |
| 경로     | 추출하거나 매핑할 각 필드에 대한 JSON 경로 표현식입니다. MongoDB/REST와 같은 계층 적 데이터를 신청하십시오.<br>루트 개체 아래의 필드의 경우 JSON 경로는 루트 $로 시작합니다. 속성에서 `collectionReference` 선택한 배열 내부필드의 경우 JSON 경로는 배열 요소에서 시작됩니다. | 예       |
| type     | 원본 또는 싱크 열의 데이터 팩터리 중간 데이터 형식입니다. | 예       |
| culture  | 원본 또는 싱크 열의 문화. <br>또는 에 `Datetime` 적용된 형식이 `Datetimeoffset`있는 경우 적용됩니다. 기본값은 `en-us`입니다. | 예       |
| format   | 형식 또는 `Datetimeoffset`.의 경우 `Datetime` 사용할 문자열 형식 날짜/시간 형식을 지정하는 방법은 [사용자 지정 날짜 및 시간 형식 문자열](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings)을 참조하세요. | 예       |

다음 속성은 다음과 `translator`  ->  `mappings` 같은 개체 `source` 와 `sink`함께 지원됩니다.

| 속성            | 설명                                                  | 필수 |
| ------------------- | ------------------------------------------------------------ | -------- |
| collectionReference | MongoDB/REST와 같은 계층적 데이터가 원본인 경우에만 지원됩니다.<br>동일한 패턴으로 **배열 필드 내부**의 개체에서 데이터를 반복 및 추출하고 행별 개체별로 변환하려면 교차 적용하도록 해당 배열의 JSON 경로를 지정합니다. | 예       |

### <a name="alternative-column-mapping"></a>대체 열 매핑

복사 활동 -> `translator`  ->  `columnMappings` 지정하여 테이블 형식 의 데이터 간에 매핑할 수 있습니다. 이 경우 입력 및 출력 데이터 집합 모두에 대해 "구조" 섹션이 필요합니다. 열 매핑은 **원본 데이터 세트 "structure"의 전체 열 또는 하위 집합을 싱크 데이터 세트 "structure"** 의 모든 열에 매핑하는 것을 지원합니다. 다음은 예외가 발생하는 오류 조건입니다.

* 원본 데이터 저장소 쿼리 결과에 입력 데이터 세트 "structure" 섹션에서 지정한 열 이름이 없습니다.
* 싱크 데이터 저장소(미리 정의된 스키마가 있는 경우)에 출력 데이터 세트 "structure" 섹션에서 지정한 열 이름이 없습니다.
* 매핑에서 지정한 것보다 싱크 데이터 세트의 "structure"에 열이 더 많거나 적습니다.
* 중복 매핑

다음 예제에서 입력 데이터 집합에는 구조가 있으며 온-프레미스 Oracle 데이터베이스의 테이블을 가리킵니다.

```json
{
    "name": "OracleDataset",
    "properties": {
        "structure":
         [
            { "name": "UserId"},
            { "name": "Name"},
            { "name": "Group"}
         ],
        "type": "OracleTable",
        "linkedServiceName": {
            "referenceName": "OracleLinkedService",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "SourceTable"
        }
    }
}
```

이 샘플에서 출력 데이터 집합에는 구조가 있으며 Salesfoce의 테이블을 가리킵니다.

```json
{
    "name": "SalesforceDataset",
    "properties": {
        "structure":
        [
            { "name": "MyUserId"},
            { "name": "MyName" },
            { "name": "MyGroup"}
        ],
        "type": "SalesforceObject",
        "linkedServiceName": {
            "referenceName": "SalesforceLinkedService",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "SinkTable"
        }
    }
}
```

다음 JSON은 파이프라인으로 복사 작업을 정의합니다. **번역기** -> 열을 사용하여 싱크의 열에 매핑된 원본의**열매핑** 속성입니다.

```json
{
    "name": "CopyActivity",
    "type": "Copy",
    "inputs": [
        {
            "referenceName": "OracleDataset",
            "type": "DatasetReference"
        }
    ],
    "outputs": [
        {
            "referenceName": "SalesforceDataset",
            "type": "DatasetReference"
        }
    ],
    "typeProperties":    {
        "source": { "type": "OracleSource" },
        "sink": { "type": "SalesforceSink" },
        "translator":
        {
            "type": "TabularTranslator",
            "columnMappings":
            {
                "UserId": "MyUserId",
                "Group": "MyGroup",
                "Name": "MyName"
            }
        }
    }
}
```

`"columnMappings": "UserId: MyUserId, Group: MyGroup, Name: MyName"` 구문을 사용하여 열 매핑을 지정하면 있는 그대로 지원됩니다.

### <a name="alternative-schema-mapping"></a>대체 스키마 매핑

복사 활동 -> `translator`  ->  `schemaMapping` 지정하여 계층적 모양의 데이터와 표 모양 의 데이터 간에 매핑할 수 있습니다(예: MongoDB/REST에서 텍스트 파일로 복사) 및 오라클에서 MongoDB용 Azure Cosmos DB의 API로 복사할 수 있습니다. 다음 속성은 복사 작업 `translator` 섹션에서 지원됩니다.

| 속성 | 설명 | 필수 |
|:--- |:--- |:--- |
| type | 복사 활동 변환기의 형식 속성을 **설정해야** 합니다. | yes |
| schemaMapping | **소스 측에서 싱크 측까지의**매핑 관계를 나타내는 키-값 쌍의 컬렉션입니다.<br/>- **키:** 소스를 나타냅니다. **테이블 형식 소스의**경우 데이터 집합 구조에 정의된 열 이름을 지정합니다. **계층적 소스의**경우 추출 및 매핑할 각 필드에 대한 JSON 경로 식을 지정합니다.<br>- **값:** 싱크를 나타냅니다. **테이블 형식 싱크의**경우 데이터 집합 구조에 정의된 열 이름을 지정합니다. **계층 적 싱크의**경우 추출 및 매핑할 각 필드에 대한 JSON 경로 표현식을 지정합니다. <br>계층 적 데이터의 경우 루트 개체 아래의 필드의 경우 JSON 경로는 루트 $로 시작합니다. 속성에서 `collectionReference` 선택한 배열 내부필드의 경우 JSON 경로는 배열 요소에서 시작됩니다.  | yes |
| collectionReference | 동일한 패턴으로 **배열 필드 내부**의 개체에서 데이터를 반복 및 추출하고 행별 개체별로 변환하려면 교차 적용하도록 해당 배열의 JSON 경로를 지정합니다. 이 속성은 계층적 데이터가 원본인 경우에만 지원됩니다. | 예 |

**예: 몽고DB에서 오라클로 복사:**

예를 들어 MongoDB 문서에 다음과 같은 콘텐츠가 들어 있는 경우:

```json
{
    "id": {
        "$oid": "592e07800000000000000000"
    },
    "number": "01",
    "date": "20170122",
    "orders": [
        {
            "prod": "p1",
            "price": 23
        },
        {
            "prod": "p2",
            "price": 13
        },
        {
            "prod": "p3",
            "price": 231
        }
    ],
    "city": [ { "name": "Seattle" } ]
}
```

그리고 *(order_pd 및 order_price)* 배열 내부의 데이터를 평면화하고 일반적인 루트 정보 *(number, date 및 city)* 로 크로스 조인하여 Azure SQL 테이블에 데이터를 다음 형식으로 복사하려는 경우:

| orderNumber | orderDate | order_pd | order_price | city |
| --- | --- | --- | --- | --- |
| 01 | 20170122 | P1 | 23 | Seattle |
| 01 | 20170122 | P2 | 13 | Seattle |
| 01 | 20170122 | P3 | 231 | Seattle |

다음 복사 작업 JSON 샘플처럼 스키마-매핑 규칙을 구성합니다.

```json
{
    "name": "CopyFromMongoDBToOracle",
    "type": "Copy",
    "typeProperties": {
        "source": {
            "type": "MongoDbV2Source"
        },
        "sink": {
            "type": "OracleSink"
        },
        "translator": {
            "type": "TabularTranslator",
            "schemaMapping": {
                "$.number": "orderNumber",
                "$.date": "orderDate",
                "prod": "order_pd",
                "price": "order_price",
                "$.city[0].name": "city"
            },
            "collectionReference":  "$.orders"
        }
    }
}
```

## <a name="data-type-mapping"></a>데이터 형식 매핑

복사 작업에서는 다음 2단계 방식으로 원본 형식을 싱크 형식에 매핑합니다.

1. 원시 원본 형식을 Azure Data Factory 중간 데이터 형식으로 변환
2. Azure Data Factory 중간 데이터 형식을 원시 싱크 형식으로 변환

각 커넥터 항목의 "데이터 형식 매핑" 섹션에서 원시 형식과 중간 형식 간의 매핑을 확인할 수 있습니다.

### <a name="supported-data-types"></a>지원되는 데이터 형식

Data Factory는 다음 중간 데이터 형식을 지원합니다: [데이터 집합 구조](concepts-datasets-linked-services.md#dataset-structure-or-schema) 구성에서 형식 정보를 구성할 때 아래 값을 지정할 수 있습니다.

* Byte[]
* 부울
* DateTime
* Datetimeoffset
* Decimal
* Double
* Guid
* Int16
* Int32
* Int64
* Single
* String
* Timespan

## <a name="next-steps"></a>다음 단계
다른 복사 작업 문서를 참조하세요.

- [활동 개요 복사](copy-activity-overview.md)
