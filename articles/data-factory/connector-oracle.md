---
title: Azure Data Factory를 사용하여 Oracle 간 데이터 복사 | Microsoft Docs
description: 데이터 팩터리를 사용하여 지원되는 원본 저장소에서 Oracle 데이터베이스로 (또는) Oracle에서 지원되는 싱크 저장소로 데이터를 복사하는 방법에 대해 알아봅니다.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/14/2018
ms.author: jingwang
ms.openlocfilehash: 5039399ac875add02319e1a745d99344956c7bee
ms.sourcegitcommit: 0b4da003fc0063c6232f795d6b67fa8101695b61
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/05/2018
ms.locfileid: "37860217"
---
# <a name="copy-data-from-and-to-oracle-by-using-azure-data-factory"></a>Azure Data Factory를 사용하여 Oracle 간 데이터 복사
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [버전 1](v1/data-factory-onprem-oracle-connector.md)
> * [현재 버전](connector-oracle.md)

이 문서에서는 Azure Data Factory의 복사 작업을 사용하여 Oracle 데이터베이스 간에 데이터를 복사하는 방법을 설명합니다. 이 문서는 복사 작업에 대한 일반적인 개요를 제공하는 [복사 작업 개요](copy-activity-overview.md) 문서를 기반으로 합니다.

## <a name="supported-capabilities"></a>지원되는 기능

Oracle 데이터베이스에서 지원되는 모든 싱크 데이터 저장소로 데이터를 복사할 수 있습니다. 지원되는 모든 원본 데이터 저장소의 데이터를 Oracle에 복사할 수도 있습니다. 복사 작업의 원본 또는 싱크로 지원되는 데이터 저장소 목록은 [지원되는 데이터 저장소](copy-activity-overview.md#supported-data-stores-and-formats) 표를 참조하세요.

특히, 이 Oracle 커넥터는 Oracle 데이터베이스의 다음 버전을 지원합니다. 또한 기본 또는 OID 인증을 지원합니다.

- Oracle 12c R1(12.1)
- Oracle 11g R1, R2(11.1, 11.2)
- Oracle 10g R1, R2(10.1, 10.2)
- Oracle 9i R1, R2(9.0.1, 9.2)
- Oracle 8i R3(8.1.7)

> [!Note]
> Oracle 프록시 서버는 지원되지 않습니다.

## <a name="prerequisites"></a>필수 조건

공개적으로 액세스할 수 없는 Oracle 데이터베이스 간에 데이터를 복사하려면 자체 호스팅 통합 런타임을 설정해야 합니다. 통합 런타임에 대한 자세한 내용은 [자체 호스팅 통합 런타임](create-self-hosted-integration-runtime.md)을 참조하세요. 통합 런타임은 기본 제공 Oracle 드라이버를 제공합니다. 따라서 Oracle 데이터 복사 작업에는 드라이버를 수동으로 설치할 필요가 없습니다.

## <a name="get-started"></a>시작하기

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

다음 섹션에서는 Oracle 커넥터에 한정된 데이터 팩터리 엔터티를 정의하는 데 사용되는 속성에 대해 자세히 설명합니다.

## <a name="linked-service-properties"></a>연결된 서비스 속성

Oracle 연결 서비스에 다음 속성이 지원됩니다.

| 자산 | 설명 | 필수 |
|:--- |:--- |:--- |
| 형식 | type 속성은 **Oracle**로 설정해야 합니다. | 예 |
| connectionString | Oracle 데이터베이스 인스턴스에 연결하는 데 필요한 정보를 지정합니다. 이 필드를 SecureString으로 표시하여 Data Factory에 안전하게 저장하거나 [Azure Key Vault에 저장되는 비밀을 참조](store-credentials-in-key-vault.md)합니다.<br><br>**지원되는 연결 유형**: 데이터베이스를 식별하기 위해 **Oracle SID** 또는 **Oracle 서비스 이름**을 사용할 수 있습니다.<br>- SID를 사용하는 경우: `Host=<host>;Port=<port>;Sid=<sid>;User Id=<username>;Password=<password>;`<br>- 서비스 이름을 사용하는 경우: `Host=<host>;Port=<port>;ServiceName=<servicename>;User Id=<username>;Password=<password>;` | 예 |
| connectVia | 데이터 저장소에 연결하는 데 사용할 [통합 런타임](concepts-integration-runtime.md)입니다. 자체 호스팅 Integration Runtime 또는 Azure Integration Runtime을 사용할 수 있습니다(데이터 저장소를 공개적으로 액세스할 수 있는 경우). 지정하지 않으면 기본 Azure Integration Runtime을 사용합니다. |아니오 |

>[!TIP]
>“ORA-01025: UPI parameter out of range”(ORA-01025: UPI 매개 변수가 범위를 벗어남) 오류가 표시되고 Oracle이 8i 버전인 경우 연결 문자열에 `WireProtocolMode=1`을 추가하고 다시 시도합니다.

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

데이터 집합 정의에 사용할 수 있는 섹션 및 속성의 전체 목록은 [데이터 집합](concepts-datasets-linked-services.md) 문서를 참조하세요. 이 섹션에는 Oracle 데이터 집합에서 지원하는 속성의 목록을 제공합니다.

Oracle 간에 데이터를 복사하려면 데이터 집합의 형식 속성을 **OracleTable**로 설정합니다. 다음과 같은 속성이 지원됩니다.

| 자산 | 설명 | 필수 |
|:--- |:--- |:--- |
| 형식 | 데이터 집합의 type 속성을 **OracleTable**로 설정해야 합니다. | 예 |
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

### <a name="oracle-as-a-source-type"></a>원본 유형인 Oracle

Oracle에서 데이터를 복사하려면 복사 작업의 원본 형식을 **OracleSource**로 설정합니다. 복사 작업 **source** 섹션에서 다음 속성이 지원됩니다.

| 자산 | 설명 | 필수 |
|:--- |:--- |:--- |
| 형식 | 복사 작업 원본의 형식 속성을 **OracleSource**로 설정해야 합니다. | 예 |
| oracleReaderQuery | 사용자 지정 SQL 쿼리를 사용하여 데이터를 읽습니다. 예는 `"SELECT * FROM MyTable"`입니다. | 아니오 |

“oracleReaderQuery”를 지정하지 않으면 데이터 집합의 “structure” 섹션에 정의된 열은 쿼리(`select column1, column2 from mytable`)를 생성하는 데 사용되어 Oracle 데이터베이스에 대해 실행합니다. 데이터 집합 정의에 "structure"가 없는 경우 테이블에서 모든 열이 선택됩니다.

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

### <a name="oracle-as-a-sink-type"></a>싱크 형식인 Oracle 

Oracle에 데이터를 복사하려면 복사 작업의 싱크 형식을 **OracleSink**로 설정합니다. 복사 작업 **sink** 섹션에서 다음 속성이 지원됩니다.

| 자산 | 설명 | 필수 |
|:--- |:--- |:--- |
| 형식 | 복사 작업 싱크의 type 속성은 **OracleSink**로 설정해야 합니다. | 예 |
| writeBatchSize | 버퍼 크기가 writeBatchSize에 도달하는 경우 SQL 테이블에 데이터 삽입<br/>허용되는 값은 정수(행 수)입니다. |아니요(기본값: 10,000) |
| writeBatchTimeout | 시간이 초과되기 전에 완료하려는 배치 삽입 작업을 위한 대기 시간입니다.<br/>허용되는 값은 시간 범위입니다. 예를 들어 "00:30:00"(30분)입니다. | 아니오 |
| preCopyScript | 각 실행 시 Oracle에 데이터를 쓰기 전에 실행할 복사 작업에 대한 SQL 쿼리를 지정합니다. 이 속성을 사용하여 미리 로드된 데이터를 정리할 수 있습니다. | 아니오 |

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

Oracle 간에 데이터를 복사할 경우 Oracle 데이터 형식에서 데이터 팩터리 중간 데이터 형식으로 다음 매핑이 사용됩니다. 복사 활동에서 원본 스키마와 데이터 형식을 싱크에 매핑하는 방법에 대한 자세한 내용은 [스키마 및 데이터 형식 매핑](copy-activity-schema-and-type-mapping.md)을 참조하세요.

| Oracle 데이터 형식 | 데이터 팩터리 중간 데이터 형식 |
|:--- |:--- |
| BFILE |Byte[] |
| BLOB |Byte[]<br/>(Oracle 10g 이상에서만 지원됨) |
| CHAR |문자열 |
| CLOB |문자열 |
| DATE |Datetime |
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
| TIMESTAMP |Datetime |
| TIMESTAMP WITH LOCAL TIME ZONE |문자열 |
| TIMESTAMP WITH TIME ZONE |문자열 |
| UNSIGNED INTEGER |NUMBER |
| VARCHAR2 |문자열 |
| XML |문자열 |

> [!NOTE]
> 데이터 형식 INTERVAL YEAR TO MONTH 및 INTERVAL DAY TO SECOND는 지원되지 않습니다.


## <a name="next-steps"></a>다음 단계
Data Factory에서 복사 활동을 통해 원본 및 싱크로 지원되는 데이터 저장소의 목록은 [지원되는 데이터 저장소](copy-activity-overview.md##supported-data-stores-and-formats)를 참조하세요.
