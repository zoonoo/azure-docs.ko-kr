---
title: SAP HANA의 데이터 복사
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
ms.date: 02/17/2020
ms.openlocfilehash: e1a3ff32956e8a8530684ba7f300f06d0c032227
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/31/2020
ms.locfileid: "80421124"
---
# <a name="copy-data-from-sap-hana-using-azure-data-factory"></a>Azure Data Factory를 사용하여 SAP HANA에서 데이터 복사
> [!div class="op_single_selector" title1="사용 중인 Data Factory 서비스 버전을 선택합니다."]
> * [버전 1](v1/data-factory-sap-hana-connector.md)
> * [현재 버전](connector-sap-hana.md)

이 문서에서는 Azure Data Factory의 복사 작업을 사용하여 SAP HANA 데이터베이스에서 데이터를 복사하는 방법을 설명합니다. 이 문서는 복사 작업에 대한 일반적인 개요를 제공하는 [복사 작업 개요](copy-activity-overview.md) 문서를 기반으로 합니다.

>[!TIP]
>SAP 데이터 통합 시나리오에 대한 ADF의 전반적인 지원에 대해 알아보려면 자세한 소개, 비교 및 지침이 있는 [Azure Data Factory 백서를 사용하는 SAP 데이터 통합을](https://github.com/Azure/Azure-DataFactory/blob/master/whitepaper/SAP%20Data%20Integration%20using%20Azure%20Data%20Factory.pdf) 참조하세요.

## <a name="supported-capabilities"></a>지원되는 기능

이 SAP HANA 커넥터는 다음 활동에 대해 지원됩니다.

- [지원되는 소스/싱크 매트릭스로](copy-activity-overview.md) [활동 복사](copy-activity-overview.md)
- [조회 활동](control-flow-lookup-activity.md)

SAP HANA 데이터베이스에서 지원되는 모든 싱크 데이터 저장소로 데이터를 복사할 수 있습니다. 복사 작업의 원본/싱크로 지원되는 데이터 저장소 목록은 [지원되는 데이터 저장소](copy-activity-overview.md#supported-data-stores-and-formats) 테이블을 참조하세요.

특히 이 SAP HANA 커넥터는 다음을 지원합니다.

- SAP HANA 데이터베이스의 모든 버전에서 데이터 복사
- **HANA 정보 모델(분석** 및 계산 뷰) 및 **행/열 테이블의**데이터 복사.
- **Basic** 또는 **Windows** 인증을 사용한 데이터 복사
- SAP HANA 소스에서 병렬 복사. 자세한 내용은 [SAP HANA의 병렬 복사본을](#parallel-copy-from-sap-hana) 참조하십시오.

> [!TIP]
> SAP HANA 데이터 저장소**로** 데이터를 복사하려면 일반 ODBC 커넥터를 사용합니다. 자세한 내용은 [SAP HANA 싱크](connector-odbc.md#sap-hana-sink)를 참조하세요. 따라서 형식이 다른 SAP HANA 커넥터 및 ODBC 커넥터에 대한 연결된 서비스는 재사용할 수 없습니다.

## <a name="prerequisites"></a>사전 요구 사항

이 SAP HANA 커넥터를 사용하려면 다음을 수행해야 합니다.

- 자체 호스팅 Integration Runtime을 설정합니다. 자세한 내용은 [자체 호스팅 통합 런타임](create-self-hosted-integration-runtime.md) 문서를 참조하십시오.
- Integration Runtime 컴퓨터에 SAP HANA ODBC 드라이버를 설치합니다. SAP HANA ODBC 드라이버는 [SAP 소프트웨어 다운로드 센터](https://support.sap.com/swdc)에서 다운로드할 수 있습니다. **SAP HANA CLIENT for Windows**라는 키워드를 사용하여 검색합니다.

## <a name="getting-started"></a>시작

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

다음 섹션에서는 SAP HANA 커넥터에 한정된 Data Factory 엔터티를 정의하는 데 사용되는 속성에 대해 자세히 설명합니다.

## <a name="linked-service-properties"></a>연결된 서비스 속성

SAP HANA 연결된 서비스에 다음 속성이 지원됩니다.

| 속성 | Description | 필수 |
|:--- |:--- |:--- |
| type | 형식 속성은 **SapHana**로 설정해야 합니다. | 예 |
| connectionString | **기본 인증** 또는 **Windows 인증을**사용하여 SAP HANA에 연결하는 데 필요한 정보를 지정합니다. 다음 샘플을 참조하세요.<br>연결 문자열에서 서버/포트는 필수(기본 포트는 30015)이며 기본 인증을 사용할 때 사용자 이름과 암호가 필수입니다. 추가 고급 설정은 [SAP HANA ODBC 연결 속성을](<https://help.sap.com/viewer/0eec0d68141541d1b07893a39944924e/2.0.02/en-US/7cab593774474f2f8db335710b2f5c50.html>) 참조하십시오.<br/>Azure Key Vault에 암호를 입력하고 연결 문자열에서 암호 구성을 가져올 수도 있습니다. 자세한 내용은 Azure Key Vault 문서의 [자격 증명 저장을](store-credentials-in-key-vault.md) 참조하십시오. | 예 |
| userName | Windows 인증을 사용할 때 사용자 이름을 지정합니다. 예: `user@domain.com` | 예 |
| password | 사용자 계정으로 password를 지정합니다. 이 필드를 SecureString으로 표시하여 Data Factory에 안전하게 저장하거나, [Azure Key Vault에 저장된 비밀을 참조](store-credentials-in-key-vault.md)합니다. | 예 |
| connectVia | 데이터 저장소에 연결하는 데 사용할 [통합 런타임입니다.](concepts-integration-runtime.md) [필수 조건](#prerequisites)에 설명된 대로 자체 호스팅 Integration Runtime이 필요합니다. |예 |

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

다음 페이로드와 함께 SAP HANA 연결 서비스를 사용하는 경우 앞으로 새 페이로드를 사용하는 것이 좋습니다.

**예제:**

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

데이터 집합을 정의하는 데 사용할 수 있는 섹션 및 속성의 전체 목록은 [데이터 집합](concepts-datasets-linked-services.md) 문서를 참조하세요. 이 섹션에는 SAP HANA 데이터 세트에서 지원하는 속성의 목록을 제공합니다.

SAP HANA의 데이터를 복사하려면 다음 속성이 지원됩니다.

| 속성 | Description | 필수 |
|:--- |:--- |:--- |
| type | 데이터 집합의 형식 속성을 다음과 같은 **SapHanaTable** 으로 설정해야 합니다. | 예 |
| 스키마 | SAP HANA 데이터베이스의 스키마 이름입니다. | 아니요(작업 원본에서 "query"가 지정된 경우) |
| 테이블 | SAP HANA 데이터베이스의 테이블 이름입니다. | 아니요(작업 원본에서 "query"가 지정된 경우) |

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

형식이 지정된 데이터 집합을 사용하는 `RelationalTable` 경우 계속 지원되지만 앞으로 새 데이터 집합을 사용하는 것이 좋습니다.

## <a name="copy-activity-properties"></a>복사 작업 속성

작업 정의에 사용할 수 있는 섹션 및 속성의 전체 목록은 [파이프라인](concepts-pipelines-activities.md) 문서를 참조하세요. 이 섹션에는 SAP HANA 원본에서 지원하는 속성의 목록을 제공합니다.

### <a name="sap-hana-as-source"></a>SAP HANA를 원본으로

>[!TIP]
>데이터 분할을 사용하여 [SAP HANA의](#parallel-copy-from-sap-hana) 데이터를 효율적으로 수집하려면 SAP HANA 섹션의 병렬 복사본에서 자세히 알아보십시오.

SAP HANA의 데이터를 복사하려면 복사 활동 **소스** 섹션에서 다음 속성이 지원됩니다.

| 속성 | Description | 필수 |
|:--- |:--- |:--- |
| type | 복사 활동 소스의 형식 속성을 설정해야 **합니다.** | 예 |
| Query | SAP HANA 인스턴스에서 데이터를 읽을 SQL 쿼리를 지정합니다. | 예 |
| 파티션옵션 | SAP HANA에서 데이터를 수집하는 데 사용되는 데이터 분할 옵션을 지정합니다. [SAP HANA](#parallel-copy-from-sap-hana) 섹션에서 병렬 복사에 대해 자세히 알아보십시오.<br>허용 값은 **다음과** 같습니다 없음(기본값), **물리적파티션OfTable,** **SapHanaDynamicRange**. [SAP HANA](#parallel-copy-from-sap-hana) 섹션에서 병렬 복사에 대해 자세히 알아보십시오. `PhysicalPartitionsOfTable`테이블에서 데이터를 복사할 때만 사용할 수 있지만 쿼리는 사용할 수 없습니다. <br>파티션 옵션이 활성화되면(즉, `None`그렇지 않음) SAP HANA에서 데이터를 동시에 로드하는 병렬 [`parallelCopies`](copy-activity-performance-features.md#parallel-copy) 처리 정도는 복사 활동의 설정에 의해 제어됩니다. | False |
| 파티션설정 | 데이터 분할에 대한 설정 그룹을 지정합니다.<br>파티션 옵션이 `SapHanaDynamicRange`있는 경우 적용합니다. | False |
| 파티션열이름 | 병렬 복사본에 파티션에서 사용할 원본 열의 이름을 지정합니다. 지정하지 않으면 인덱스 또는 테이블의 기본 키가 자동으로 감지되어 파티션 열로 사용됩니다.<br>파티션 옵션이 `SapHanaDynamicRange`있는 경우 적용됩니다. 쿼리를 사용하여 원본 데이터를 검색하는 `?AdfHanaDynamicRangePartitionCondition` 경우 WHERE 절에 연결합니다. [SAP HANA 섹션의 병렬 복사본 예제를](#parallel-copy-from-sap-hana) 참조하십시오. | 파티션을 `SapHanaDynamicRange` 사용할 때 예. |
| packetSize | 데이터를 여러 블록으로 분할하도록 네트워크 패킷 크기(킬로바이트)를 지정합니다. 복사할 데이터가 많은 경우 패킷 크기를 늘리면 대부분의 경우 SAP HANA의 읽기 속도가 빨라진다. 패킷 크기를 조정할 때 성능 테스트를 권장합니다. | 아니요.<br>기본값은 2048(2MB)입니다. |

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

형식복사 원본을 사용하는 `RelationalSource` 경우 계속 지원되지만 앞으로 새 복사 소스를 사용하는 것이 좋습니다.

## <a name="parallel-copy-from-sap-hana"></a>SAP HANA의 병렬 사본

데이터 팩터리 SAP HANA 커넥터는 SAP HANA의 데이터를 병렬로 복사하는 기본 제공 데이터 분할을 제공합니다. 복사 활동의 **소스** 테이블에서 데이터 분할 옵션을 찾을 수 있습니다.

![파티션 옵션의 스크린샷](./media/connector-sap-hana/connector-sap-hana-partition-options.png)

분할된 복사본을 사용하도록 설정하면 Data Factory는 SAP HANA 원본에 대해 병렬 쿼리를 실행하여 파티션별로 데이터를 검색합니다. 평행 정도는 복사 [`parallelCopies`](copy-activity-performance-features.md#parallel-copy) 활동의 설정에 의해 제어됩니다. 예를 들어 4로 `parallelCopies` 설정하면 Data Factory는 지정된 파티션 옵션 및 설정에 따라 4개의 쿼리를 동시에 생성하고 실행하며 각 쿼리는 SAP HANA에서 데이터 일부를 검색합니다.

특히 SAP HANA에서 많은 양의 데이터를 수집할 때 데이터 분할과 병렬 복사를 사용하도록 설정하는 것이 좋습니다. 다음은 다양한 시나리오에 대한 권장 구성입니다. 파일 기반 데이터 저장소에 데이터를 복사할 때 폴더에 여러 파일(폴더 이름만 지정)으로 쓰는 것이 좋습니다.

| 시나리오                                           | 권장 설정                                           |
| -------------------------------------------------- | ------------------------------------------------------------ |
| 큰 테이블에서 전체 로드.                        | **파티션 옵션**: 테이블의 실제 파티션입니다. <br><br/>실행 하는 동안 데이터 팩터리는 지정 된 SAP HANA 테이블의 실제 파티션 형식을 자동으로 감지 하 고 해당 파티션 전략을 선택 합니다.<br>- **범위 분할**: 테이블에 대해 정의된 파티션 열 및 파티션 범위를 가져옵니다. <br>- **해시 분할**: 해시 파티션 키를 파티션 열로 사용한 다음 ADF 계산 범위를 기반으로 데이터를 분할하고 복사합니다. <br>- **라운드 로빈 분할** 또는 **파티션 없음**: 기본 키를 파티션 열로 사용한 다음 ADF 계산 범위를 기반으로 데이터를 분할하고 복사합니다. |
| 사용자 지정 쿼리를 사용하여 많은 양의 데이터를 로드합니다. | **파티션 옵션**: 동적 범위 파티션.<br>**쿼리** `SELECT * FROM <TABLENAME> WHERE ?AdfHanaDynamicRangePartitionCondition AND <your_additional_where_clause>`: .<br>**파티션 열**: 동적 범위 파티션을 적용하는 데 사용되는 열을 지정합니다. <br><br>실행 하는 동안 Data Factory는 먼저 지정된 파티션 열의 값 범위를 계산 하 여 고유 파티션 열 값 및 ADF 병렬 복사 설정 `?AdfHanaDynamicRangePartitionCondition` 의 수에 따라 여러 버킷에 행을 균등 하 게 배포 하 고 각 파티션에 대 한 파티션 열 값 범위를 필터링 으로 대체 하 고 SAP HANA로 보냅니다.<br><br>여러 열을 파티션 열로 사용하려는 경우 각 열의 값을 쿼리의 하나의 열로 연결하고 ADF에서 파티션 `SELECT * FROM (SELECT *, CONCAT(<KeyColumn1>, <KeyColumn2>) AS PARTITIONCOLUMN FROM <TABLENAME>) WHERE ?AdfHanaDynamicRangePartitionCondition`열로 지정할 수 있습니다. |

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
| 빈 텍스트            | String                         |
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
| real               | Single                         |
| SECONDDATE         | DateTime                       |
| 짧은 텍스트          | String                         |
| 스몰데피몰       | Decimal                        |
| SMALLINT           | Int16                          |
| ST지오메타이드 타입     | Byte[]                         |
| ST포인트 타입        | Byte[]                         |
| TEXT               | String                         |
| TIME               | TimeSpan                       |
| TINYINT            | Byte                           |
| VARCHAR            | String                         |
| timestamp          | DateTime                       |
| VARBINARY          | Byte[]                         |

## <a name="lookup-activity-properties"></a>조회 활동 속성

속성에 대한 자세한 내용을 보려면 [조회 활동을](control-flow-lookup-activity.md)선택합니다.

## <a name="next-steps"></a>다음 단계
Azure Data Factory에서 복사 작업의 원본 및 싱크로 지원되는 데이터 저장소 목록은 [지원되는 데이터 저장소](copy-activity-overview.md#supported-data-stores-and-formats)를 참조하세요.
