---
title: SAP HANA에서 데이터 복사
description: Azure Data Factory 파이프라인의 복사 작업을 사용하여 SAP HANA에서 지원되는 싱크 데이터 저장소로 데이터를 복사하는 방법에 대해 알아봅니다.
services: data-factory
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 04/22/2020
ms.openlocfilehash: ac351e688eba274c989b4b475c6d61607b9ea5c1
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "84219298"
---
# <a name="copy-data-from-sap-hana-using-azure-data-factory"></a>Azure Data Factory를 사용하여 SAP HANA에서 데이터 복사
> [!div class="op_single_selector" title1="사용 중인 Data Factory 서비스 버전을 선택합니다."]
> * [버전 1](v1/data-factory-sap-hana-connector.md)
> * [현재 버전](connector-sap-hana.md)
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

이 문서에서는 Azure Data Factory의 복사 작업을 사용하여 SAP HANA 데이터베이스에서 데이터를 복사하는 방법을 설명합니다. 이 문서는 복사 작업에 대한 일반적인 개요를 제공하는 [복사 작업 개요](copy-activity-overview.md) 문서를 기반으로 합니다.

>[!TIP]
>SAP 데이터 통합 시나리오에서 ADF의 전반적인 지원에 대한 자세한 내용은 [Azure Data Factory를 사용하여 SAP 데이터 통합 백서](https://github.com/Azure/Azure-DataFactory/blob/master/whitepaper/SAP%20Data%20Integration%20using%20Azure%20Data%20Factory.pdf)에서 자세한 소개, 비교 및 지침을 참조하세요.

## <a name="supported-capabilities"></a>지원되는 기능

이 SAP HANA 커넥터는 다음과 같은 작업에 대해 지원 됩니다.

- [지원되는 원본/싱크 매트릭스](copy-activity-overview.md)를 사용한 [복사 작업](copy-activity-overview.md)
- [조회 작업](control-flow-lookup-activity.md)

SAP HANA 데이터베이스에서 지원되는 모든 싱크 데이터 저장소로 데이터를 복사할 수 있습니다. 복사 작업의 원본/싱크로 지원되는 데이터 저장소 목록은 [지원되는 데이터 저장소](copy-activity-overview.md#supported-data-stores-and-formats) 테이블을 참조하세요.

특히 이 SAP HANA 커넥터는 다음을 지원합니다.

- SAP HANA 데이터베이스의 모든 버전에서 데이터 복사
- **HANA 정보 모델** (예: 분석 및 계산 뷰)에서 데이터를 복사 하 고 **행/열 테이블**을 복사 합니다.
- **Basic** 또는 **Windows** 인증을 사용한 데이터 복사
- SAP HANA 원본에서의 병렬 복사 자세한 내용은 [SAP HANA에서 병렬 복사](#parallel-copy-from-sap-hana) 섹션을 참조 하세요.

> [!TIP]
> SAP HANA 데이터 저장소**로** 데이터를 복사하려면 일반 ODBC 커넥터를 사용합니다. 자세한 내용은 [SAP HANA 싱크](#sap-hana-sink) 섹션을 참조 하세요. 따라서 형식이 다른 SAP HANA 커넥터 및 ODBC 커넥터에 대한 연결된 서비스는 재사용할 수 없습니다.

## <a name="prerequisites"></a>사전 요구 사항

이 SAP HANA 커넥터를 사용하려면 다음을 수행해야 합니다.

- 자체 호스팅 Integration Runtime을 설정합니다. 자세한 내용은 [자체 호스팅 Integration Runtime](create-self-hosted-integration-runtime.md) 문서를 참조 하세요.
- Integration Runtime 컴퓨터에 SAP HANA ODBC 드라이버를 설치합니다. SAP HANA ODBC 드라이버는 [SAP 소프트웨어 다운로드 센터](https://support.sap.com/swdc)에서 다운로드할 수 있습니다. **SAP HANA CLIENT for Windows**라는 키워드를 사용하여 검색합니다.

## <a name="getting-started"></a>시작

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

다음 섹션에서는 SAP HANA 커넥터에 한정된 Data Factory 엔터티를 정의하는 데 사용되는 속성에 대해 자세히 설명합니다.

## <a name="linked-service-properties"></a>연결된 서비스 속성

SAP HANA 연결된 서비스에 다음 속성이 지원됩니다.

| 속성 | 설명 | 필수 |
|:--- |:--- |:--- |
| type | 형식 속성은 **SapHana**로 설정해야 합니다. | 예 |
| connectionString | **기본 인증** 또는 **Windows 인증**을 사용 하 여 SAP HANA에 연결 하는 데 필요한 정보를 지정 합니다. 다음 샘플을 참조하세요.<br>연결 문자열에서 서버/포트는 필수 (기본 포트는 30015)이 고, 기본 인증을 사용 하는 경우 사용자 이름 및 암호는 필수입니다. 고급 설정에 대 한 자세한 내용은 [SAP HANA ODBC 연결 속성](<https://help.sap.com/viewer/0eec0d68141541d1b07893a39944924e/2.0.02/en-US/7cab593774474f2f8db335710b2f5c50.html>) 을 참조 하세요.<br/>Azure Key Vault에 암호를 입력 하 고 연결 문자열에서 암호 구성을 끌어올 수도 있습니다. 자세한 내용은 [Azure Key Vault 문서의 자격 증명 저장](store-credentials-in-key-vault.md) 을 참조 하세요. | 예 |
| userName | Windows 인증을 사용 하는 경우 사용자 이름을 지정 합니다. 예: `user@domain.com` | 아니요 |
| password | 사용자 계정으로 password를 지정합니다. 이 필드를 SecureString으로 표시하여 Data Factory에 안전하게 저장하거나 [Azure Key Vault에 저장되는 비밀을 참조](store-credentials-in-key-vault.md)합니다. | 예 |
| connectVia | 데이터 저장소에 연결하는 데 사용할 [Integration Runtime](concepts-integration-runtime.md)입니다. [필수 조건](#prerequisites)에 설명된 대로 자체 호스팅 Integration Runtime이 필요합니다. |예 |

**예: 기본 인증 사용**

```json
{
    "name": "SapHanaLinkedService",
    "properties": {
        "type": "SapHana",
        "typeProperties": {
            "connectionString": "SERVERNODE=<server>:<port (optional)>;UID=<userName>;PWD=<Password>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**예: Windows 인증 사용**

```json
{
    "name": "SapHanaLinkedService",
    "properties": {
        "type": "SapHana",
        "typeProperties": {
            "connectionString": "SERVERNODE=<server>:<port (optional)>;",
            "userName": "<username>", 
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

다음 페이로드를 사용 하 여 SAP HANA 연결 된 서비스를 사용 하는 경우에는 계속 해 서 새 항목을 사용 하는 것이 좋습니다.

**예:**

```json
{
    "name": "SapHanaLinkedService",
    "properties": {
        "type": "SapHana",
        "typeProperties": {
            "server": "<server>:<port (optional)>",
            "authenticationType": "Basic",
            "userName": "<username>",
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

데이터 세트 정의에 사용할 수 있는 섹션 및 속성의 전체 목록은 [데이터 세트](concepts-datasets-linked-services.md) 문서를 참조하세요. 이 섹션에는 SAP HANA 데이터 세트에서 지원하는 속성의 목록을 제공합니다.

SAP HANA에서 데이터를 복사 하기 위해 지원 되는 속성은 다음과 같습니다.

| 속성 | 설명 | 필수 |
|:--- |:--- |:--- |
| type | 데이터 집합의 type 속성은 **SapHanaTable** 로 설정 해야 합니다. | 예 |
| 스키마 | SAP HANA 데이터베이스의 스키마 이름입니다. | 아니요(작업 원본에서 "query"가 지정된 경우) |
| 테이블 | SAP HANA 데이터베이스에 있는 테이블의 이름입니다. | 아니요(작업 원본에서 "query"가 지정된 경우) |

**예제:**

```json
{
    "name": "SAPHANADataset",
    "properties": {
        "type": "SapHanaTable",
        "typeProperties": {
            "schema": "<schema name>",
            "table": "<table name>"
        },
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<SAP HANA linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

`RelationalTable` 형식의 데이터 세트를 사용하고 있는 경우 현재까지 지원되지만 앞으로는 새 형식을 사용하는 것이 좋습니다.

## <a name="copy-activity-properties"></a>복사 작업 속성

작업 정의에 사용할 수 있는 섹션 및 속성의 전체 목록은 [파이프라인](concepts-pipelines-activities.md) 문서를 참조하세요. 이 섹션에는 SAP HANA 원본에서 지원하는 속성의 목록을 제공합니다.

### <a name="sap-hana-as-source"></a>SAP HANA를 원본으로

>[!TIP]
>데이터 분할을 사용 하 여 SAP HANA에서 데이터를 효율적으로 수집 하려면 [SAP HANA에서 병렬 복사](#parallel-copy-from-sap-hana) 섹션을 자세히 알아보세요.

SAP HANA에서 데이터를 복사 하려면 복사 작업 **원본** 섹션에서 다음 속성을 지원 합니다.

| 속성 | 설명 | 필수 |
|:--- |:--- |:--- |
| type | 복사 작업 원본의 type 속성은 **SapHanaSource** 로 설정 해야 합니다. | 예 |
| Query | SAP HANA 인스턴스에서 데이터를 읽을 SQL 쿼리를 지정합니다. | 예 |
| partitionOptions | SAP HANA에서 데이터를 수집 하는 데 사용 되는 데이터 분할 옵션을 지정 합니다. [SAP HANA 섹션의 병렬 복사](#parallel-copy-from-sap-hana) 에서 자세히 알아보세요.<br>Allow 값은 **None**   (기본값), **PhysicalPartitionsOfTable**, **SapHanaDynamicRange**입니다. [SAP HANA 섹션의 병렬 복사](#parallel-copy-from-sap-hana) 에서 자세히 알아보세요. `PhysicalPartitionsOfTable`테이블에서 데이터를 복사 하는 경우에만 사용할 수 있고 쿼리는 사용할 수 없습니다. <br>파티션 옵션을 사용 하도록 설정 하는 경우 (즉,이 아님 `None` ) SAP HANA에서 데이터를 동시에 로드 하는 병렬 처리 수준이 [`parallelCopies`](copy-activity-performance-features.md#parallel-copy) 복사 작업의 설정에 의해 제어 됩니다. | False |
| partitionSettings | 데이터 분할에 대한 설정 그룹을 지정합니다.<br>파티션 옵션이 `SapHanaDynamicRange`인 경우에 적용합니다. | False |
| partitionColumnName | 병렬 복사를 위해 파티션에서 사용할 원본 열의 이름을 지정 합니다. 지정 하지 않으면 테이블의 인덱스 또는 기본 키가 자동으로 검색 되어 파티션 열로 사용 됩니다.<br>파티션 옵션이 인 경우에 적용 됩니다  `SapHanaDynamicRange` . 쿼리를 사용 하 여 원본 데이터를 검색 하는 경우  `?AdfHanaDynamicRangePartitionCondition` WHERE 절에 후크 합니다. [SAP HANA의 병렬 복사](#parallel-copy-from-sap-hana) 섹션에서 예제를 참조 하세요. | 파티션을 사용 하는 경우에는 예 `SapHanaDynamicRange` 입니다. |
| packetSize | 데이터를 여러 블록으로 분할 하는 네트워크 패킷 크기 (Kb)를 지정 합니다. 복사할 데이터가 많은 경우 패킷 크기를 늘리면 대부분의 경우 SAP HANA에서 읽기 속도를 높일 수 있습니다. 패킷 크기를 조정할 때 성능 테스트를 수행 하는 것이 좋습니다. | 아니요.<br>기본값은 2048 (2MB)입니다. |

**예제:**

```json
"activities":[
    {
        "name": "CopyFromSAPHANA",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<SAP HANA input dataset name>",
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
                "type": "SapHanaSource",
                "query": "<SQL query for SAP HANA>"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

형식화 된 복사 원본을 사용 하 고 있는 경우에 `RelationalSource` 는 계속 해 서 새 항목을 사용 하는 것이 좋습니다.

## <a name="parallel-copy-from-sap-hana"></a>SAP HANA의 병렬 복사

Data Factory SAP HANA 커넥터는 SAP HANA에서 병렬로 데이터를 복사 하는 기본 제공 데이터 분할을 제공 합니다. 복사 작업의 **원본** 테이블에서 데이터 분할 옵션을 찾을 수 있습니다.

![파티션 옵션의 스크린샷](./media/connector-sap-hana/connector-sap-hana-partition-options.png)

분할 된 복사를 사용 하도록 설정 하면 Data Factory SAP HANA 원본에 대해 병렬 쿼리를 실행 하 여 파티션으로 데이터를 검색 합니다. 병렬 수준은 복사 작업의 [`parallelCopies`](copy-activity-performance-features.md#parallel-copy) 설정에 의해 제어됩니다. 예를 들어를 4로 설정 하는 경우 `parallelCopies` Data Factory는 지정 된 파티션 옵션 및 설정을 기반으로 4 개의 쿼리를 동시에 생성 하 고 실행 하며 각 쿼리는 SAP HANA에서 데이터의 일부를 검색 합니다.

특히 SAP HANA에서 많은 양의 데이터를 수집 하는 경우 데이터 분할으로 병렬 복사를 사용 하도록 설정 하는 것이 좋습니다. 다양한 시나리오에 대해 권장되는 구성은 다음과 같습니다. 파일 기반 데이터 저장소로 데이터를 복사할 때 폴더에 여러 파일 (폴더 이름만 지정)로 쓰는 것이 좋습니다 .이 경우에는 단일 파일에 쓰는 것 보다 성능이 좋습니다.

| 시나리오                                           | 제안된 설정                                           |
| -------------------------------------------------- | ------------------------------------------------------------ |
| 큰 테이블에서 전체 로드입니다.                        | **파티션 옵션**: 테이블의 실제 파티션입니다. <br><br/>실행 중에 Data Factory는 지정 된 SAP HANA 테이블의 실제 파티션 유형을 자동으로 검색 하 고 해당 파티션 전략을 선택 합니다.<br>- **범위 분할**: 테이블에 대해 정의 된 파티션 열 및 파티션 범위를 가져온 다음 데이터를 범위로 복사 합니다. <br>- **해시 분할**: 해시 파티션 키를 파티션 열로 사용 하 고, ADF 계산 범위를 기준으로 데이터를 분할 하 고 복사 합니다. <br>- **라운드 로빈 분할** 또는 **파티션 없음**: 기본 키를 파티션 열로 사용한 다음, ADF 계산 범위를 기준으로 데이터를 분할 하 고 복사 합니다. |
| 사용자 지정 쿼리를 사용하여 많은 양의 데이터를 로드합니다. | **파티션 옵션**: 동적 범위 파티션.<br>**쿼리**:`SELECT * FROM <TABLENAME> WHERE ?AdfHanaDynamicRangePartitionCondition AND <your_additional_where_clause>`.<br>**파티션 열**: 동적 범위 파티션을 적용 하는 데 사용 되는 열을 지정 합니다. <br><br>실행 하는 동안 Data Factory는 지정 된 파티션 열의 값 범위를 계산 하 여 고유 파티션 열 값 및 ADF 병렬 복사 설정의 수에 따라 여러 버킷의 행을 균등 하 게 분산 하 고,는 `?AdfHanaDynamicRangePartitionCondition` 각 파티션의 파티션 열 값 범위 필터링으로 대체 하 여 SAP HANA으로 보냅니다.<br><br>여러 열을 파티션 열로 사용 하려는 경우 각 열의 값을 쿼리에서 하나의 열로 연결 하 고와 같이 ADF에서 파티션 열로 지정할 수 있습니다 `SELECT * FROM (SELECT *, CONCAT(<KeyColumn1>, <KeyColumn2>) AS PARTITIONCOLUMN FROM <TABLENAME>) WHERE ?AdfHanaDynamicRangePartitionCondition` . |

**예: 테이블의 실제 파티션이 있는 쿼리**

```json
"source": {
    "type": "SapHanaSource",
    "partitionOption": "PhysicalPartitionsOfTable"
}
```

**예: 동적 범위 파티션이 있는 쿼리**

```json
"source": {
    "type": "SapHanaSource",
    "query": "SELECT * FROM <TABLENAME> WHERE ?AdfHanaDynamicRangePartitionCondition AND <your_additional_where_clause>",
    "partitionOption": "SapHanaDynamicRange",
    "partitionSettings": {
        "partitionColumnName": "<Partition_column_name>"
    }
}
```

## <a name="data-type-mapping-for-sap-hana"></a>SAP HANA에 대한 데이터 형식 매핑

SAP HANA에서 데이터를 복사하는 경우 SAP HANA 데이터 형식에서 Azure Data Factory 중간 데이터 형식으로 다음 매핑이 사용됩니다. 복사 작업에서 원본 스키마 및 데이터 형식을 싱크에 매핑하는 방법에 대한 자세한 내용은 [스키마 및 데이터 형식 매핑](copy-activity-schema-and-type-mapping.md)을 참조하세요.

| SAP HANA 데이터 형식 | Data Factory 중간 데이터 형식 |
| ------------------ | ------------------------------ |
| ALPHANUM           | String                         |
| bigint             | Int64                          |
| BINARY             | Byte[]                         |
| BINTEXT            | String                         |
| BLOB               | Byte[]                         |
| BOOL               | Byte                           |
| CLOB               | String                         |
| DATE               | DateTime                       |
| DECIMAL            | Decimal                        |
| DOUBLE             | Double                         |
| FLOAT              | Double                         |
| INTEGER            | Int32                          |
| NCLOB              | String                         |
| NVARCHAR           | String                         |
| 실수               | Single                         |
| SECONDDATE         | DateTime                       |
| SHORTTEXT          | String                         |
| SMALLDECIMAL       | Decimal                        |
| SMALLINT           | Int16                          |
| STGEOMETRYTYPE     | Byte[]                         |
| STPOINTTYPE        | Byte[]                         |
| TEXT               | String                         |
| TIME               | TimeSpan                       |
| TINYINT            | Byte                           |
| VARCHAR            | String                         |
| timestamp          | DateTime                       |
| VARBINARY          | Byte[]                         |

## <a name="sap-hana-sink"></a>SAP HANA 싱크

현재 SAP HANA 커넥터는 싱크로 지원 되지 않지만 SAP HANA 드라이버와 일반 ODBC 커넥터를 사용 하 여 SAP HANA에 데이터를 쓸 수 있습니다. 

[필수 구성 요소](#prerequisites) 에 따라 자체 호스트 된 Integration Runtime를 설정 하 고 SAP HANA ODBC 드라이버를 먼저 설치 합니다. 다음 예제와 같이 ODBC 연결 된 서비스를 만들어 SAP HANA 데이터 저장소에 연결 하 고 그에 따라 ODBC 형식의 데이터 집합 및 복사 작업 싱크를 만듭니다. 자세한 내용은 [ODBC connector](connector-odbc.md) 문서를 참조 하세요.

```json
{
    "name": "SAPHANAViaODBCLinkedService",
    "properties": {
        "type": "Odbc",
        "typeProperties": {
            "connectionString": "Driver={HDBODBC};servernode=<HANA server>.clouddatahub-int.net:30015",
            "authenticationType": "Basic",
            "userName": "<username>",
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

## <a name="lookup-activity-properties"></a>조회 작업 속성

속성에 대한 자세한 내용을 보려면 [조회 작업](control-flow-lookup-activity.md)을 확인하세요.

## <a name="next-steps"></a>다음 단계
Azure Data Factory에서 복사 작업의 원본 및 싱크로 지원되는 데이터 저장소 목록은 [지원되는 데이터 저장소](copy-activity-overview.md#supported-data-stores-and-formats)를 참조하세요.
