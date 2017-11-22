---
title: "Azure Data Factory를 사용하여 Oracle 간 데이터 복사 | Microsoft Docs"
description: "Data Factory를 사용하여 지원되는 원본 저장소에서 Oracle 데이터베이스로 (또는) Oracle에서 지원되는 싱크 저장소로 데이터를 복사하는 방법에 대해 알아봅니다."
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/01/2017
ms.author: jingwang
ms.openlocfilehash: df9ba6367b2d2ce1b2c281ce483e63b0baa9feb6
ms.sourcegitcommit: dcf5f175454a5a6a26965482965ae1f2bf6dca0a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/10/2017
---
# <a name="copy-data-from-and-to-oracle-using-azure-data-factory"></a>Azure Data Factory를 사용하여 Oracle 간 데이터 복사
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [버전 1 - GA](v1/data-factory-onprem-oracle-connector.md)
> * [버전 2 - 미리 보기](connector-oracle.md)

이 문서에서는 Azure Data Factory의 복사 작업을 사용하여 Oracle 데이터베이스 간에 데이터를 복사하는 방법을 설명합니다. 이 문서는 복사 작업에 대한 일반적인 개요를 제공하는 [복사 작업 개요](copy-activity-overview.md) 문서를 기반으로 합니다.

> [!NOTE]
> 이 문서는 현재 미리 보기 상태인 Data Factory 버전 2에 적용됩니다. GA(일반 공급) 상태인 Data Factory 버전 1 서비스를 사용하는 경우 [V1의 Oracle 커넥터](v1/data-factory-onprem-oracle-connector.md)를 참조하세요.

## <a name="supported-capabilities"></a>지원되는 기능

Oracle 데이터베이스에서 지원되는 싱크 데이터 저장소로 또는 지원되는 원본 데이터 저장소에서 Oracle 데이터베이스로 데이터를 복사할 수 있습니다. 복사 작업의 원본/싱크로 지원되는 데이터 저장소 목록은 [지원되는 데이터 저장소](copy-activity-overview.md#supported-data-stores-and-formats) 표를 참조하세요.

특히, 이 Oracle 커넥터는 Oracle 데이터베이스의 다음 버전을 지원하고 기본 또는 OID 인증을 지원합니다.

    - Oracle 12c R1(12.1)
    - Oracle 11g R1, R2(11.1, 11.2)
    - Oracle 10g R1, R2(10.1, 10.2)
    - Oracle 9i R1, R2(9.0.1, 9.2)
    - Oracle 8i R3(8.1.7)

## <a name="prerequisites"></a>필수 조건

공개적으로 액세스할 수 없는 Oracle 데이터베이스 간에 데이터를 복사하려면 자체 호스팅 Integration Runtime을 설정해야 합니다. 통합 런타임에 대한 자세한 내용은 [자체 호스팅 Integration Runtime](create-self-hosted-integration-runtime.md) 문서를 참조하세요. Integration Runtime은 기본 제공 Oracle 드라이버를 제공하므로 Oracle 간에 데이터를 복사할 때 수동으로 드라이버를 설치할 필요가 없습니다.

## <a name="getting-started"></a>시작
.NET SDK, Python SDK, Azure PowerShell, REST API 또는 Azure Resource Manager 템플릿을 사용하여 복사 작업으로 파이프라인을 만들 수 있습니다. 복사 작업을 사용하여 파이프라인을 만드는 단계별 지침은 [복사 작업 자습서](quickstart-create-data-factory-dot-net.md)를 참조하세요.

다음 섹션에서는 Oracle 커넥터에 한정된 Data Factory 엔터티를 정의하는 데 사용되는 속성에 대해 자세히 설명합니다.

## <a name="linked-service-properties"></a>연결된 서비스 속성

Oracle 연결된 서비스에 다음 속성이 지원됩니다.

| 속성 | 설명 | 필수 |
|:--- |:--- |:--- |
| type | 형식 속성은 **Oracle**로 설정해야 합니다. | 예 |
| connectionString | Oracle 데이터베이스 인스턴스에 연결하는 데 필요한 정보를 지정합니다. 이 필드를 SecureString으로 표시합니다. | 예 |
| connectVia | 데이터 저장소에 연결하는 데 사용할 [Integration Runtime](concepts-integration-runtime.md)입니다. 자체 호스팅 Integration Runtime 또는 Azure Integration Runtime을 사용할 수 있습니다(데이터 저장소를 공개적으로 액세스할 수 있는 경우). 지정하지 않으면 기본 Azure Integration Runtime을 사용합니다. |아니요 |

**예제:**

```json
{
    "name": "OracleLinkedService",
    "properties": {
        "type": "Oracle",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Host=<host>;Port=<port>;Sid=<sid>;User Id=<username>;Password=<password>;"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>데이터 집합 속성

데이터 집합 정의에 사용할 수 있는 섹션 및 속성의 전체 목록은 데이터 집합 문서를 참조하세요. 이 섹션에는 Oracle 데이터 집합에서 지원하는 속성의 목록을 제공합니다.

Oracle 간에 데이터를 복사하려면 데이터 집합의 형식 속성을 **OracleTable**로 설정합니다. 다음과 같은 속성이 지원됩니다.

| 속성 | 설명 | 필수 |
|:--- |:--- |:--- |
| type | 데이터 집합의 형식 속성을 **OracleTable**로 설정해야 합니다. | 예 |
| tableName |연결된 서비스가 참조하는 Oracle 데이터베이스에 있는 테이블의 이름입니다. | 예 |

**예제:**

```json
{
    "name": "OracleDataset",
    "properties":
    {
        "type": "OracleTable",
        "linkedServiceName": {
            "referenceName": "<Oracle linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "MyTable"
        }
    }
}
```

## <a name="copy-activity-properties"></a>복사 작업 속성

작업 정의에 사용할 수 있는 섹션 및 속성의 전체 목록은 [파이프라인](concepts-pipelines-activities.md) 문서를 참조하세요. 이 섹션에서는 Oracle 원본 및 싱크에서 지원하는 속성 목록을 제공합니다.

### <a name="oracle-as-source"></a>Oracle을 원본으로

Oracle에서 데이터를 복사하려면 복사 작업의 원본 형식을 **OracleSource**로 설정합니다. 복사 작업 **source** 섹션에서 다음 속성이 지원됩니다.

| 속성 | 설명 | 필수 |
|:--- |:--- |:--- |
| type | 복사 작업 원본의 형식 속성을 **OracleSource**로 설정해야 합니다. | 예 |
| oracleReaderQuery | 사용자 지정 SQL 쿼리를 사용하여 데이터를 읽습니다. 예: `"SELECT * FROM MyTable"` | 아니요 |

“oracleReaderQuery”를 지정하지 않으면 데이터 집합의 “structure” 섹션에 정의된 열은 쿼리(`select column1, column2 from mytable`)를 생성하는 데 사용되어 Oracle 데이터베이스에 대해 실행합니다. 데이터 집합 정의에 구조가 없는 경우 모든 열은 테이블에서 선택됩니다.

**예제:**

```json
"activities":[
    {
        "name": "CopyFromOracle",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Oracle input dataset name>",
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
                "type": "OracleSource",
                "oracleReaderQuery": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="oracle-as-sink"></a>Oracle을 싱크로

Oracle에 데이터를 복사하려면 복사 작업의 싱크 형식을 **OracleSink**로 설정합니다. 복사 작업 **sink** 섹션에서 다음 속성이 지원됩니다.

| 속성 | 설명 | 필수 |
|:--- |:--- |:--- |
| type | 복사 작업 싱크의 형식 속성은 **OracleSink**로 설정해야 합니다. | 예 |
| writeBatchSize | 버퍼 크기가 writeBatchSize에 도달하는 경우 SQL 테이블에 데이터 삽입<br/>허용되는 값은 정수(행 수)입니다. |아니요(기본값: 10000) |
| writeBatchTimeout | 시간이 초과되기 전에 완료하려는 배치 삽입 작업을 위한 대기 시간입니다.<br/>허용되는 값은 시간 범위입니다. 예: “00:30:00”(30분). | 아니요 |
| preCopyScript | 각 실행 시 Oracle에 데이터를 쓰기 전에 실행할 복사 작업에 대한 SQL 쿼리를 지정합니다. 이 속성을 사용하여 미리 로드된 데이터를 정리할 수 있습니다. | 아니요 |

**예제:**

```json
"activities":[
    {
        "name": "CopyToOracle",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Oracle output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "OracleSink"
            }
        }
    }
]
```

## <a name="data-type-mapping-for-oracle"></a>Oracle에 대한 데이터 형식 매핑

Oracle 간에 데이터를 복사할 경우 Oracle 데이터 형식에서 Azure Data Factory 중간 데이터 형식으로 다음 매핑이 사용됩니다. 복사 작업에서 원본 스키마 및 데이터 형식을 싱크에 매핑하는 방법에 대한 자세한 내용은 [스키마 및 데이터 형식 매핑](copy-activity-schema-and-type-mapping.md)을 참조하세요.

| Oracle 데이터 형식 | 데이터 팩터리 중간 데이터 형식 |
|:--- |:--- |
| BFILE |Byte[] |
| BLOB |Byte[] |
| CHAR |문자열 |
| CLOB |문자열 |
| DATE |DateTime |
| FLOAT |Decimal, 문자열(전체 자릿수의 경우 > 28) |
| INTEGER |Decimal, 문자열(전체 자릿수의 경우 > 28) |
| LONG |문자열 |
| LONG RAW |Byte[] |
| NCHAR |문자열 |
| NCLOB |문자열 |
| NUMBER |Decimal, 문자열(전체 자릿수의 경우 > 28) |
| NVARCHAR2 |문자열 |
| RAW |Byte[] |
| ROWID |문자열 |
| TIMESTAMP |DateTime |
| TIMESTAMP WITH LOCAL TIME ZONE |문자열 |
| TIMESTAMP WITH TIME ZONE |문자열 |
| UNSIGNED INTEGER |NUMBER |
| VARCHAR2 |문자열 |
| XML |문자열 |

> [!NOTE]
> 데이터 형식 INTERVAL YEAR TO MONTH 및 INTERVAL DAY TO SECOND는 지원되지 않습니다.


## <a name="next-steps"></a>다음 단계
Azure Data Factory에서 복사 작업의 원본 및 싱크로 지원되는 데이터 저장소 목록은 [지원되는 데이터 저장소](copy-activity-overview.md##supported-data-stores-and-formats)를 참조하세요.