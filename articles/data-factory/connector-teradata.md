---
title: Azure Data Factory를 사용하여 Teradata에서 데이터 복사 | Microsoft Docs
description: Teradata 데이터베이스의 데이터를 데이터 팩터리에서 싱크로 지원하는 데이터 저장소로 복사할 수 있는 데이터 팩터리 서비스의 Teradata 커넥터에 대해 알아봅니다.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 07/02/2019
ms.author: jingwang
ms.openlocfilehash: 63f28c8b6eaceed12e1f76e9c0c5984e3b63b500
ms.sourcegitcommit: d3b1f89edceb9bff1870f562bc2c2fd52636fc21
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/04/2019
ms.locfileid: "67561414"
---
# <a name="copy-data-from-teradata-using-azure-data-factory"></a>Azure Data Factory를 사용하여 Teradata에서 데이터 복사
> [!div class="op_single_selector" title1="사용 하는 Data Factory 서비스 버전을 선택 합니다."]
>
> * [버전 1](v1/data-factory-onprem-teradata-connector.md)
> * [현재 버전](connector-teradata.md)

이 문서에서는 Azure Data Factory의 복사 작업을 사용하여 Teradata 데이터베이스에서 데이터를 복사하는 방법을 설명합니다. 이 문서는 복사 작업에 대한 일반적인 개요를 제공하는 [복사 작업 개요](copy-activity-overview.md) 문서를 기반으로 합니다.

## <a name="supported-capabilities"></a>지원되는 기능

Teradata 데이터베이스에서 지원되는 모든 싱크 데이터 저장소로 데이터를 복사할 수 있습니다. 복사 작업의 원본/싱크로 지원되는 데이터 저장소 목록은 [지원되는 데이터 저장소](copy-activity-overview.md#supported-data-stores-and-formats) 표를 참조하세요.

특히 이 Teradata 커넥터는 다음을 지원합니다.

- Teradata **14.10, 15.0, 15.10, 16.0, 16.10, 및 16.20 버전**합니다.
- **Basic** 또는 **Windows** 인증을 사용한 데이터 복사
- Teradata 원본에서 병렬 복사본입니다. 참조 [병렬 복사 Teradata에서](#parallel-copy-from-teradata) 세부 정보가 포함 된 섹션입니다.

> [!NOTE]
>
> Azure Data Factory는 기본 제공 ODBC 드라이버에 게 권한이 부여 되 고 유연한 연결 옵션 뿐만 아니라 기본-병렬 복사 성능을 향상 시키기 위해 제공 하는 자체 호스팅 Integration Runtime v3.18 이후 Teradata 커넥터를 업그레이드 합니다. Teradata로 계속 지원에 대 한.NET 데이터 공급자가 제공 되는 이전 Teradata 커넥터를 사용 하 여 모든 기존 워크 로드-있고, 앞으로 새 하나를 사용 하려면 제안 됩니다. 새 경로 적어 둡니다 다양 한 연결 된 서비스/데이터 집합 복사 원본 필요합니다. 구성에 대 한 자세한 내용은 해당 섹션을 참조 하세요.

## <a name="prerequisites"></a>필수 조건

Teradata에 공개적으로 액세스할 수 없는 경우 자체 호스팅 통합 런타임을 설정 해야 합니다. 통합 런타임에 대한 자세한 내용은 [자체 호스팅 통합 런타임](create-self-hosted-integration-runtime.md)을 참조하세요. Integration Runtime 버전 3.18에서에서 시작 하는 기본 제공 Teradata 드라이버에서는 따라서 수동으로 드라이버를 설치할 필요가 없습니다. 드라이버가 필요 "Visual C++ 재배포 가능 패키지 2012 업데이트 4"에서 자체 호스팅 IR 컴퓨터에 다운로드 [여기](https://www.microsoft.com/en-sg/download/details.aspx?id=30679) 아직 없는 경우 설치 합니다.

자체 호스팅 IR 3.18 보다 낮은 버전을 설치 해야 합니다 [.NET Data Provider for Teradata](https://go.microsoft.com/fwlink/?LinkId=278886) 버전 14 이상을 Integration Runtime 컴퓨터. 

## <a name="getting-started"></a>시작

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

다음 섹션에서는 Teradata 커넥터에 한정된 데이터 팩터리 엔터티를 정의하는 데 사용되는 속성에 대해 자세히 설명합니다.

## <a name="linked-service-properties"></a>연결된 서비스 속성

Teradata 연결된 서비스에 다음 속성이 지원됩니다.

| 자산 | 설명 | 필수 |
|:--- |:--- |:--- |
| type | type 속성을 다음으로 설정해야 합니다. **Teradata** | 예 |
| connectionString | Teradata 데이터베이스 인스턴스에 연결 하는 데 필요한 정보를 지정 합니다. 다음 샘플을 참조하세요.<br/>Azure Key Vault에 암호를 넣고, 연결 문자열에서 `password` 구성을 끌어올 수도 있습니다. 가리킵니다 [Azure Key Vault에 자격 증명을 저장](store-credentials-in-key-vault.md) 자세한 문서. | 예 |
| username | Teradata 데이터베이스에 연결할 사용자 이름을 지정합니다. Windows 인증을 사용할 때 적용 됩니다. | 아닙니다. |
| password | 사용자 이름에 지정한 사용자 계정의 암호를 지정합니다. 선택할 수도 있습니다 [Azure Key Vault에 저장 된 비밀을 참조할](store-credentials-in-key-vault.md)합니다. <br>Windows 인증을 사용 하는 경우 적용 하거나 기본 인증을 위한 Key Vault에 암호를 참조 합니다. | 아닙니다. |
| connectVia | 데이터 저장소에 연결하는 데 사용할 [Integration Runtime](concepts-integration-runtime.md)입니다. [필수 조건](#prerequisites)에 설명된 대로 자체 호스팅 Integration Runtime이 필요합니다. |예 |

**예제: 기본 인증 사용**

```json
{
    "name": "TeradataLinkedService",
    "properties": {
        "type": "Teradata",
        "typeProperties": {
            "connectionString": "DBCName=<server>;Uid=<username>;Pwd=<password>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**예제: Windows 인증 사용**

```json
{
    "name": "TeradataLinkedService",
    "properties": {
        "type": "Teradata",
        "typeProperties": {
            "connectionString": "DBCName=<server>",
            "username": "<username>",
            "password": "<password>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

> [!NOTE]
>
> 다음 페이로드를 사용 하 여 Teradata에 대 한.NET 데이터 공급자가 연결 된 서비스를 제공 하는 Teradata 사용한, 경우 여전히으로 지원 됩니다-있고, 앞으로 새 하나를 사용 하려면 제안 됩니다.

**이전 페이로드:**

```json
{
    "name": "TeradataLinkedService",
    "properties": {
        "type": "Teradata",
        "typeProperties": {
            "server": "<server>",
            "authenticationType": "<Basic/Windows>",
            "username": "<username>",
            "password": {
                "type": "SecureString",
                "value": "<password>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>데이터 세트 속성

데이터 세트 정의에 사용할 수 있는 섹션 및 속성의 전체 목록은 데이터 세트 문서를 참조하세요. 이 섹션에서는 Teradata 데이터 세트에서 지원하는 속성의 목록을 제공합니다.

Teradata에서 데이터를 복사할에 다음 속성이 지원 됩니다.

| 자산 | 설명 | 필수 |
|:--- |:--- |:--- |
| type | 데이터 세트의 type 속성을 다음으로 설정해야 합니다. **TeradataTable** | 예 |
| database | Teradata 데이터베이스의 이름입니다. | 아니요(작업 원본에서 "query"가 지정된 경우) |
| 테이블 | Teradata 데이터베이스의 테이블 이름입니다. | 아니요(작업 원본에서 "query"가 지정된 경우) |

**예제:**

```json
{
    "name": "TeradataDataset",
    "properties": {
        "type": "TeradataTable",
        "linkedServiceName": {
            "referenceName": "<Teradata linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {}
    }
}
```

> [!NOTE]
>
> 으로 계속 지원 됩니다 "RelationalTable" 형식 데이터 집합이 다음과 같이 사용할 때-있고, 앞으로 새 하나를 사용 하려면 제안 됩니다.

**이전 페이로드:**

```json
{
    "name": "TeradataDataset",
    "properties": {
        "type": "RelationalTable",
        "linkedServiceName": {
            "referenceName": "<Teradata linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {}
    }
}
```

## <a name="copy-activity-properties"></a>복사 작업 속성

작업 정의에 사용할 수 있는 섹션 및 속성의 전체 목록은 [파이프라인](concepts-pipelines-activities.md) 문서를 참조하세요. 이 섹션에서는 Teradata 원본에서 지원하는 속성의 목록을 제공합니다.

### <a name="teradata-as-source"></a>Teradata를 원본으로

> [!TIP]
>
> 자세히 알아보세요 [병렬 복사 Teradata에서](#parallel-copy-from-teradata) 효율적으로 데이터 분할을 사용 하 여 Teradata에서 데이터를 로드 하는 방법에 대 한 섹션입니다.

Teradata에서 데이터를 복사 하려면 복사 작업의 다음 속성을 지 **원본** 섹션:

| 자산 | 설명 | 필수 |
|:--- |:--- |:--- |
| type | 복사 작업 원본의 type 속성을 다음으로 설정해야 합니다. **TeradataSource** | 예 |
| query | 사용자 지정 SQL 쿼리를 사용하여 데이터를 읽습니다. 예: `"SELECT * FROM MyTable"`<br>분할 된 부하를 사용 하도록 설정 하면 쿼리에서 해당 기본 제공 파티션 매개 변수를 연결 해야 합니다. 예제를 참조 [병렬 복사 Teradata에서](#parallel-copy-from-teradata) 섹션입니다. | 아니요 (데이터 집합의 테이블은 지정 된 경우) |
| partitionOptions | 데이터 분할 Teradata에서 데이터를 로드 하는 데 사용 하는 옵션을 지정 합니다. <br>값은 허용: **None** (기본값) 이면 **해시** 하 고 **DynamicRange**합니다.<br>파티션 옵션을 사용 하는 경우 (없습니다 ' None'), 또한 구성 하십시오 **[ `parallelCopies` ](copy-activity-performance.md#parallel-copy)** 4 예를 들어 복사 활동에서 설정을 결정 하는 동시에 Teradata에서 데이터를 로드 하는 병렬 처리 수준이 데이터베이스입니다. | 아닙니다. |
| partitionSettings | 데이터 분할에 대 한 설정 그룹을 지정 합니다. <br>파티션 옵션 없는 경우 적용 `None`합니다. | 아닙니다. |
| partitionColumnName | 원본 열의 이름을 지정 **정수 형식의** 병렬 복사본에 대 한 범위를 분할 하 여 사용 되는 합니다. 지정 하지 않으면 테이블의 기본 키를 자동으로 검색 하 고 파티션 열으로 사용 됩니다. <br>파티션 옵션을 적용 `Hash` 또는 `DynamicRange`합니다. 원본 데이터를 검색할 쿼리를 사용 하는 경우 연결 `?AdfHashPartitionCondition` 또는 `?AdfRangePartitionColumnName` WHERE 절에 있습니다. 예제를 참조 하세요 [병렬 복사 Teradata에서](#parallel-copy-from-teradata) 섹션입니다. | 아닙니다. |
| partitionUpperBound | 파티션 열 복사할 데이터의 최대값입니다. <br>파티션 옵션을 적용 `DynamicRange`합니다. 원본 데이터를 검색할 쿼리를 사용 하는 경우 연결 `?AdfRangePartitionUpbound` WHERE 절에 있습니다. 예제를 참조 하세요 [병렬 복사 Teradata에서](#parallel-copy-from-teradata) 섹션입니다. | 아닙니다. |
| PartitionLowerBound | 파티션 열 복사할 데이터의 최소값입니다. <br>파티션 옵션을 적용 `DynamicRange`합니다. 원본 데이터를 검색할 쿼리를 사용 하는 경우 연결 `?AdfRangePartitionLowbound` WHERE 절에 있습니다. 예제를 참조 하세요 [병렬 복사 Teradata에서](#parallel-copy-from-teradata) 섹션입니다. | 아닙니다. |

> [!NOTE]
>
> 으로 계속 지원 됩니다 "RelationalSource" 형식 복사 원본이 사용 된 경우-이지만 Teradata (파티션 옵션)에서 새 기본 제공 병렬 로드를 지원 하지 않습니다. 앞으로이 새로 사용 하도록 제안 됩니다.

**예제: 파티션 없이 기본 쿼리를 사용 하 여 데이터 복사**

```json
"activities":[
    {
        "name": "CopyFromTeradata",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Teradata input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "TeradataSource",
                "query": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="parallel-copy-from-teradata"></a>Teradata에서 병렬 복사

데이터 팩터리 Teradata 커넥터 뛰어난 성능으로 병렬로 Teradata에서 데이터 복사를 분할 하는 기본 제공 데이터를 제공 합니다. 복사 작업에서 데이터 분할 옵션에 Teradata 원본->를 찾을 수 있습니다.

![파티션 옵션](./media/connector-teradata/connector-teradata-partition-options.png)

분할 된 복사본을 사용 하면 data factory는 파티션에서 데이터를 로드 하 여 Teradata 원본에 대해 병렬 쿼리를 실행 합니다. 병렬 처리 수준이 구성 하 고 제어를 통해 **[ `parallelCopies` ](copy-activity-performance.md#parallel-copy)** 복사 활동에 설정 합니다. 예를 들어 설정한 `parallelCopies` 4로 data factory에서 동시에 생성 및 Teradata 데이터베이스에서에서 데이터를 검색 하는 동안 각 부분 지정한 파티션 옵션 및 설정에 따라 4 개의 쿼리를 실행 합니다.

Teradata 데이터베이스에서 많은 양의 데이터를 로드 하는 경우에 특히 분할 데이터를 사용 하 여 병렬 복사를 사용 하려면 제안 됩니다. 다음은 다양 한 시나리오에 대 한 권장된 구성입니다.

| 시나리오                                                     | 제안 된 설정                                           |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| 큰 테이블에서 전체 로드                                   | **파티션 옵션**: 해시입니다. <br><br/>실행 중 데이터 팩터리 자동으로 검색 PK 열, 적용에 대해 해시 및 파티션에서 데이터를 복사 합니다. |
| 많은 양의 사용자 지정 쿼리를 사용 하 여 데이터 로드                 | **파티션 옵션**: 해시입니다.<br>**쿼리**: `SELECT * FROM <TABLENAME> WHERE ?AdfHashPartitionCondition AND <your_additional_where_clause>`합니다.<br>**파티션 열**: 해시 파티션 적용에 사용 된 열을 지정 합니다. 지정 하지 않으면 ADF Teradata 데이터 집합에서 지정한 테이블의 PK 열의 자동으로 검색 됩니다.<br><br>데이터 팩터리 교체를 실행 하는 동안 `?AdfHashPartitionCondition` 해시 파티션 논리와 Teradata에 대 한 송신 합니다. |
| 범위 분할에 고르게 분포 된 값이 있는 열이 정수일 필요, 사용자 지정 쿼리를 사용 하 여 데이터 대량 로드 | **파티션 옵션**: 동적 범위 분할 합니다.<br>**쿼리**: `SELECT * FROM <TABLENAME> WHERE ?AdfRangePartitionColumnName <= ?AdfRangePartitionUpbound AND ?AdfRangePartitionColumnName >= ?AdfRangePartitionLowbound AND <your_additional_where_clause>`합니다.<br>**파티션 열**: 데이터를 분할 하는 데 사용 하는 열을 지정 합니다. 정수 데이터 형식의 열에 대해 분할할 수 있습니다.<br>**파티션 상한** 하 고 **파티션 하한값**: 파티션 열만 데이터를 검색 하려면 한 및 상한 범위 사이 대해 필터링 할 것인지를 지정 합니다.<br><br>데이터 팩터리 교체를 실행 하는 동안 `?AdfRangePartitionColumnName`, `?AdfRangePartitionUpbound`, 및 `?AdfRangePartitionLowbound` 실제 열 이름 및 값을 사용 하 여 각각에 대 한 범위를 분할 및 Teradata에 보냅니다. <br>예를 들어, 1 및 4에서 병렬 복사 집합과 80으로 상한 하한값으로 설정 하 고 파티션 열 "ID" ADF [21, 40] [1, 20] 간에 ID 사용 하 여 4 개의 파티션 기준으로 데이터 검색 [41, 60] 및 [61, 80]. |

**예: 쿼리 해시 파티션**

```json
"source": {
    "type": "TeradataSource",
    "query": "SELECT * FROM <TABLENAME> WHERE ?AdfHashPartitionCondition AND <your_additional_where_clause>",
    "partitionOption": "Hash",
    "partitionSettings": {
        "partitionColumnName": "<hash_partition_column_name>"
    }
}
```

**동적 범위 분할을 사용 하 여 쿼리 예제:**

```json
"source": {
    "type": "TeradataSource",
    "query": "SELECT * FROM <TABLENAME> WHERE ?AdfRangePartitionColumnName <= ?AdfRangePartitionUpbound AND ?AdfRangePartitionColumnName >= ?AdfRangePartitionLowbound AND <your_additional_where_clause>",
    "partitionOption": "DynamicRange",
    "partitionSettings": {
        "partitionColumnName": "<dynamic_range_partition_column_name>",
        "partitionUpperBound": "<upper_value_of_partition_column>",
        "partitionLowerBound": "<lower_value_of_partition_column>"
    }
}
```

## <a name="data-type-mapping-for-teradata"></a>Teradata에 대한 데이터 형식 매핑

Teradata에서 데이터를 복사하는 경우 Teradata 데이터 형식에서 Azure Data Factory 중간 데이터 형식으로 다음 매핑이 사용됩니다. 복사 작업에서 원본 스키마 및 데이터 형식을 싱크에 매핑하는 방법에 대한 자세한 내용은 [스키마 및 데이터 형식 매핑](copy-activity-schema-and-type-mapping.md)을 참조하세요.

| Teradata 데이터 형식 | Data Factory 중간 데이터 형식 |
|:--- |:--- |
| BigInt |Int64 |
| Blob |Byte[] |
| Byte |Byte[] |
| ByteInt |Int16 |
| Char |String |
| Clob |String |
| Date |DateTime |
| Decimal |Decimal |
| Double |Double |
| Graphic |지원되지 않습니다. 명시적 캐스트 원본 쿼리에 적용 됩니다. |
| Integer |Int32 |
| Interval Day |지원되지 않습니다. 명시적 캐스트 원본 쿼리에 적용 됩니다. |
| Interval Day To Hour |지원되지 않습니다. 명시적 캐스트 원본 쿼리에 적용 됩니다. |
| Interval Day To Minute |지원되지 않습니다. 명시적 캐스트 원본 쿼리에 적용 됩니다. |
| Interval Day To Second |지원되지 않습니다. 명시적 캐스트 원본 쿼리에 적용 됩니다. |
| Interval Hour |지원되지 않습니다. 명시적 캐스트 원본 쿼리에 적용 됩니다. |
| Interval Hour To Minute |지원되지 않습니다. 명시적 캐스트 원본 쿼리에 적용 됩니다. |
| Interval Hour To Second |지원되지 않습니다. 명시적 캐스트 원본 쿼리에 적용 됩니다. |
| Interval Minute |지원되지 않습니다. 명시적 캐스트 원본 쿼리에 적용 됩니다. |
| Interval Minute To Second |지원되지 않습니다. 명시적 캐스트 원본 쿼리에 적용 됩니다. |
| Interval Month |지원되지 않습니다. 명시적 캐스트 원본 쿼리에 적용 됩니다. |
| Interval Second |지원되지 않습니다. 명시적 캐스트 원본 쿼리에 적용 됩니다. |
| Interval Year |지원되지 않습니다. 명시적 캐스트 원본 쿼리에 적용 됩니다. |
| Interval Year To Month |지원되지 않습니다. 명시적 캐스트 원본 쿼리에 적용 됩니다. |
| Number |Double |
| 기간 (Date) |지원되지 않습니다. 명시적 캐스트 원본 쿼리에 적용 됩니다. |
| 기간 (시간) |지원되지 않습니다. 명시적 캐스트 원본 쿼리에 적용 됩니다. |
| 기간 (시간 표준 시간대를 사용 하 여) |지원되지 않습니다. 명시적 캐스트 원본 쿼리에 적용 됩니다. |
| 기간 (타임 스탬프) |지원되지 않습니다. 명시적 캐스트 원본 쿼리에 적용 됩니다. |
| 기간 (표준 시간대를 사용 하 여 타임 스탬프) |지원되지 않습니다. 명시적 캐스트 원본 쿼리에 적용 됩니다. |
| SmallInt |Int16 |
| Time |TimeSpan |
| Time With Time Zone |TimeSpan |
| Timestamp |DateTime |
| Timestamp With Time Zone |DateTime |
| VarByte |Byte[] |
| VarChar |String |
| VarGraphic |지원되지 않습니다. 명시적 캐스트 원본 쿼리에 적용 됩니다. |
| xml |지원되지 않습니다. 명시적 캐스트 원본 쿼리에 적용 됩니다. |


## <a name="next-steps"></a>다음 단계
Azure Data Factory에서 복사 작업의 원본 및 싱크로 지원되는 데이터 저장소 목록은 [지원되는 데이터 저장소](copy-activity-overview.md#supported-data-stores-and-formats)를 참조하세요.
