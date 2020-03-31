---
title: Azure Data Factory를 사용하여 Netezza에서 데이터 복사
description: Azure Data Factory 파이프라인의 복사 작업을 사용하여 Netezza에서 지원되는 싱크 데이터 저장소로 데이터를 복사하는 방법에 대해 알아봅니다.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 09/02/2019
ms.author: jingwang
ms.openlocfilehash: c51469997af23be7a5e1b88677ecadb37e10ac64
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79244538"
---
# <a name="copy-data-from-netezza-by-using-azure-data-factory"></a>Azure Data Factory를 사용하여 Netezza에서 데이터 복사

이 문서에서는 Azure Data Factory의 복사 작업을 사용하여 Netezza에서 데이터를 복사하는 방법에 대해 설명합니다. 이 문서는 복사 작업에 대한 일반적인 개요를 제공하는 [Azure Data Factory의 복사 작업](copy-activity-overview.md)을 기반으로 합니다.

>[!TIP]
>Netezza에서 Azure로의 데이터 마이그레이션 시나리오의 경우 [Azure 데이터 팩터리 사용에서 온-프레미스 Netezza 서버에서 Azure로 데이터를 마이그레이션하는](data-migration-guidance-netezza-azure-sqldw.md)방법에 대해 자세히 알아봅니다.

## <a name="supported-capabilities"></a>지원되는 기능

이 Netezza 커넥터는 다음 활동에 대해 지원됩니다.

- [지원되는 소스/싱크 매트릭스로](copy-activity-overview.md) [활동 복사](copy-activity-overview.md)
- [조회 활동](control-flow-lookup-activity.md)


Netezza에서 지원되는 모든 싱크 데이터 저장소로 데이터를 복사할 수 있습니다. 복사 작업에서 원본 및 싱크로 지원되는 데이터 저장소의 목록은 [지원되는 데이터 저장소 및 형식](copy-activity-overview.md#supported-data-stores-and-formats)을 참조하세요.

Netezza 커넥터는 소스에서 병렬 복사를 지원합니다. 자세한 내용은 [Netezza의 병렬 복사본을](#parallel-copy-from-netezza) 참조하십시오.

Azure Data Factory는 연결을 허용하는 기본 제공 드라이버를 제공합니다. 이 커넥터를 사용하기 위해 드라이버를 수동으로 설치할 필요가 없습니다.

## <a name="prerequisites"></a>사전 요구 사항

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

## <a name="get-started"></a>시작

.NET SDK, Python SDK, Azure PowerShell, REST API 또는 Azure Resource Manager 템플릿을 통해 복사 작업을 사용하는 파이프라인을 만들 수 있습니다. 복사 작업이 포함된 파이프라인을 만드는 단계별 지침은 [복사 작업 자습서](quickstart-create-data-factory-dot-net.md)를 참조하세요.

다음 섹션에서는 Netezza 커넥터에 한정된 Data Factory 엔터티를 정의하는 데 사용되는 속성에 대해 자세히 설명합니다.

## <a name="linked-service-properties"></a>연결된 서비스 속성

다음 속성은 Netezza 연결 서비스에 지원됩니다.

| 속성 | 설명 | 필수 |
|:--- |:--- |:--- |
| type | **형식** 속성은 **Netezza**로 설정되어야 합니다. | yes |
| connectionString | Netezza에 연결할 ODBC 연결 문자열입니다. <br/>Azure Key Vault에 암호를 넣고, 연결 문자열에서 `pwd` 구성을 끌어올 수도 있습니다. 자세한 내용은 다음 샘플 및 [Azure Key Vault에 자격 증명 저장](store-credentials-in-key-vault.md) 문서를 참조하세요. | yes |
| connectVia | 데이터 저장소에 연결하는 데 사용할 [통합 런타임](concepts-integration-runtime.md)입니다. [필수 구성 조건](#prerequisites) 섹션에서 자세히 알아보십시오. 지정하지 않으면 기본 Azure Integration Runtime이 사용됩니다. |예 |

일반적인 연결 문자열은 `Server=<server>;Port=<port>;Database=<database>;UID=<user name>;PWD=<password>`입니다. 다음 표에서는 설정 가능한 여러 속성을 설명합니다.

| 속성 | 설명 | 필수 |
|:--- |:--- |:--- |
| SecurityLevel | 드라이버가 데이터 저장소에 연결하는 데 사용하는 보안(SSL/TLS) 수준입니다. 예: `SecurityLevel=preferredSecured`. 지원되는 값은 다음과 같습니다.<br/>- **Only unsecured**(**onlyUnSecured**): 드라이버가 SSL을 사용하지 않습니다.<br/>- **Preferred unsecured(preferredUnSecured)(기본값)**: 서버가 선택 사항을 제공하면 드라이버는 SSL을 사용하지 않습니다. <br/>- **기본 보안(기본 설정)**: 서버가 선택 을 제공하는 경우 드라이버는 SSL을 사용합니다. <br/>- **Only secured(onlySecured)**: SSL 연결을 사용할 수 있는 경우가 아니면 드라이버가 연결되지 않습니다. | 예 |
| CaCertFile | 서버에 사용되는 SSL 인증서의 전체 경로입니다. 예: `CaCertFile=<cert path>;`| 예(SSL을 사용하는 경우) |

**예제**

```json
{
    "name": "NetezzaLinkedService",
    "properties": {
        "type": "Netezza",
        "typeProperties": {
            "connectionString": "Server=<server>;Port=<port>;Database=<database>;UID=<user name>;PWD=<password>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**예제: Azure Key Vault에 암호 저장**

```json
{
    "name": "NetezzaLinkedService",
    "properties": {
        "type": "Netezza",
        "typeProperties": {
            "connectionString": "Server=<server>;Port=<port>;Database=<database>;UID=<user name>;",
            "pwd": { 
                "type": "AzureKeyVaultSecret", 
                "store": { 
                    "referenceName": "<Azure Key Vault linked service name>", 
                    "type": "LinkedServiceReference" 
                }, 
                "secretName": "<secretName>" 
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

이 섹션에서는 Netezza 데이터 세트에서 지원하는 속성의 목록을 제공합니다.

데이터 세트 정의에 사용할 수 있는 섹션 및 속성의 전체 목록은 [데이터 세트](concepts-datasets-linked-services.md)를 참조하세요.

Netezza에서 데이터를 복사하려면 데이터 세트의 **형식** 속성을 **NetezzaTable**로 설정합니다. 다음과 같은 속성이 지원됩니다.

| 속성 | 설명 | 필수 |
|:--- |:--- |:--- |
| type | 데이터 집합의 형식 속성을 다음으로 설정해야 **합니다.** | yes |
| 스키마 | 스키마의 이름입니다. |아니요(작업 원본에서 "query"가 지정된 경우)  |
| 테이블 | 테이블 이름입니다. |아니요(작업 원본에서 "query"가 지정된 경우)  |
| tableName | 스키마가 있는 테이블의 이름입니다. 이 속성은 이전 버전과의 호환성을 위해 지원됩니다. 새 `schema` `table` 워크로드에 사용하고 사용합니다. | 아니요(작업 원본에서 "query"가 지정된 경우) |

**예제**

```json
{
    "name": "NetezzaDataset",
    "properties": {
        "type": "NetezzaTable",
        "linkedServiceName": {
            "referenceName": "<Netezza linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {}
    }
}
```

## <a name="copy-activity-properties"></a>복사 활동 속성

이 섹션에서는 Netezza 원본에서 지원하는 속성의 목록을 제공합니다.

작업 정의에 사용할 수 있는 섹션 및 속성의 전체 목록은 [파이프라인](concepts-pipelines-activities.md)을 참조하세요.

### <a name="netezza-as-source"></a>Netezza를 원본으로

>[!TIP]
>데이터 분할을 사용하여 Netezza의 데이터를 효율적으로 로드하려면 [Netezza](#parallel-copy-from-netezza) 섹션에서 병렬 복사본에서 자세히 알아보십시오.

Netezza에서 데이터를 복사하려면 복사 작업의 **원본** 형식을 **NetezzaSource**로 설정합니다. 다음 속성은 활동 복사 **원본** 섹션에서 지원됩니다.

| 속성 | 설명 | 필수 |
|:--- |:--- |:--- |
| type | 복사 작업 원본의 **형식** 속성을 **NetezzaSource**로 설정해야 합니다. | yes |
| Query | 사용자 지정 SQL 쿼리를 사용하여 데이터를 읽습니다. 예: `"SELECT * FROM MyTable"` | 아니요(데이터 세트의 "tableName"이 지정된 경우) |
| 파티션옵션 | Netezza에서 데이터를 로드하는 데 사용되는 데이터 분할 옵션을 지정합니다. <br>허용 값은 **None** 없음(기본값), **데이터 슬라이스**및 **동적 범위**입니다.<br>파티션 옵션이 활성화되면(즉, `None`그렇지 않음) Netezza 데이터베이스에서 데이터를 동시에 로드하는 병렬 [`parallelCopies`](copy-activity-performance.md#parallel-copy) 처리 정도는 복사 활동을 설정하여 제어됩니다. | 예 |
| 파티션설정 | 데이터 분할에 대한 설정 그룹을 지정합니다. <br>파티션 옵션이 아닌 `None`경우 적용합니다. | 예 |
| 파티션열이름 | 병렬 복사본에 대한 범위 분할에서 사용할 **정수 형식의** 원본 열 이름을 지정합니다. 지정하지 않으면 테이블의 기본 키가 자동으로 감지되어 파티션 열로 사용됩니다. <br>파티션 옵션이 `DynamicRange`있는 경우 적용됩니다. 쿼리를 사용하여 원본 데이터를 검색하는 `?AdfRangePartitionColumnName` 경우 WHERE 절에 연결합니다. [네테자 섹션에서 병렬 복사본의 예제를](#parallel-copy-from-netezza) 참조하십시오. | 예 |
| 파티션어바운드 | 데이터를 복사할 파티션 열의 최대값입니다. <br>파티션 옵션이 `DynamicRange`있는 경우 적용합니다. 쿼리를 사용하여 원본 데이터를 검색하는 경우 WHERE 절을 연결합니다. `?AdfRangePartitionUpbound` 예를 들어 [Netezza](#parallel-copy-from-netezza) 섹션의 병렬 복사본을 참조하십시오. | 예 |
| 파티션로바운드 | 데이터를 복사할 파티션 열의 최소값입니다. <br>파티션 옵션이 `DynamicRange`있는 경우 적용됩니다. 쿼리를 사용하여 원본 데이터를 검색하는 `?AdfRangePartitionLowbound` 경우 WHERE 절을 연결합니다. 예를 들어 [Netezza](#parallel-copy-from-netezza) 섹션의 병렬 복사본을 참조하십시오. | 예 |

**예제:**

```json
"activities":[
    {
        "name": "CopyFromNetezza",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Netezza input dataset name>",
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
                "type": "NetezzaSource",
                "query": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="parallel-copy-from-netezza"></a>네테자에서 병렬 사본

데이터 팩터리 네테자 커넥터는 Netezza의 데이터를 병렬로 복사하기 위해 기본 제공 데이터 분할을 제공합니다. 복사 활동의 **소스** 테이블에서 데이터 분할 옵션을 찾을 수 있습니다.

![파티션 옵션의 스크린샷](./media/connector-netezza/connector-netezza-partition-options.png)

분할된 복사본을 사용하도록 설정하면 Data Factory는 Netezza 원본에 대해 병렬 쿼리를 실행하여 파티션별로 데이터를 로드합니다. 평행 정도는 복사 [`parallelCopies`](copy-activity-performance.md#parallel-copy) 활동의 설정에 의해 제어됩니다. 예를 들어 4로 `parallelCopies` 설정하면 Data Factory는 지정된 파티션 옵션 및 설정에 따라 4개의 쿼리를 동시에 생성하고 실행하며 각 쿼리는 Netezza 데이터베이스에서 데이터의 일부를 검색합니다.

Netezza 데이터베이스에서 많은 양의 데이터를 로드할 때 특히 데이터 분할과 병렬 복사본을 사용하도록 설정하는 것이 좋습니다. 다음은 다양한 시나리오에 대한 권장 구성입니다. 파일 기반 데이터 저장소에 데이터를 복사할 때 여러 파일(폴더 이름만 지정)으로 폴더에 쓰도록 명령되며, 이 경우 단일 파일에 쓰는 것보다 성능이 더 좋습니다.

| 시나리오                                                     | 권장 설정                                           |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| 큰 테이블에서 전체 로드.                                   | **파티션 옵션**: 데이터 조각. <br><br/>실행 하는 동안 Data Factory [Netezza의 기본 제공 데이터 조각을](https://www.ibm.com/support/knowledgecenter/en/SSULQD_7.2.1/com.ibm.nz.adm.doc/c_sysadm_data_slices_parts_disks.html)기반으로 데이터를 자동으로 분할 하 고 파티션별로 데이터를 복사 합니다. |
| 사용자 지정 쿼리를 사용하여 많은 양의 데이터를 로드합니다.                 | **파티션 옵션**: 데이터 조각.<br>**쿼리** `SELECT * FROM <TABLENAME> WHERE mod(datasliceid, ?AdfPartitionCount) = ?AdfDataSliceCondition AND <your_additional_where_clause>`: .<br>실행 하는 동안 Data `?AdfPartitionCount` Factory (복사 활동에 설정 된 `?AdfDataSliceCondition` 병렬 복사 번호)와 데이터 조각 파티션 논리로 대체 하 고 Netezza로 보냅니다. |
| 범위 분할에 대해 균등하게 분산된 값을 가진 정수 열을 갖는 사용자 지정 쿼리를 사용하여 많은 양의 데이터를 로드합니다. | **파티션 옵션**: 동적 범위 파티션.<br>**쿼리** `SELECT * FROM <TABLENAME> WHERE ?AdfRangePartitionColumnName <= ?AdfRangePartitionUpbound AND ?AdfRangePartitionColumnName >= ?AdfRangePartitionLowbound AND <your_additional_where_clause>`: .<br>**분할 열**: 데이터를 분할하는 데 사용되는 열을 지정합니다. 정수 데이터 형식을 사용하여 열에 대해 분할할 수 있습니다.<br>**파티션 상한** 및 **파티션 하한**: 파티션 열에 대해 필터링하여 하위 범위와 상위 범위 간에만 데이터를 검색할지 지정합니다.<br><br>실행 하는 동안 Data `?AdfRangePartitionColumnName` `?AdfRangePartitionUpbound`Factory `?AdfRangePartitionLowbound` 는 에서 를 대체 하고 각 파티션에 대한 실제 열 이름 및 값 범위로 Netezza로 보냅니다. <br>예를 들어, 파티션 열 "ID"가 하한을 1로 설정하고 상한을 80으로 설정하고 병렬 복사본을 4로 설정한 경우 Data Factory는 4개의 파티션으로 데이터를 검색합니다. 그들의 아이디는 각각 [1,20], [21, 40], [41, 60], [61, 80] 사이입니다. |

**예: 데이터 조각 파티션이 있는 쿼리**

```json
"source": {
    "type": "NetezzaSource",
    "query": "SELECT * FROM <TABLENAME> WHERE mod(datasliceid, ?AdfPartitionCount) = ?AdfDataSliceCondition AND <your_additional_where_clause>",
    "partitionOption": "DataSlice"
}
```

**예: 동적 범위 파티션이 있는 쿼리**

```json
"source": {
    "type": "NetezzaSource",
    "query": "SELECT * FROM <TABLENAME> WHERE ?AdfRangePartitionColumnName <= ?AdfRangePartitionUpbound AND ?AdfRangePartitionColumnName >= ?AdfRangePartitionLowbound AND <your_additional_where_clause>",
    "partitionOption": "DynamicRange",
    "partitionSettings": {
        "partitionColumnName": "<dynamic_range_partition_column_name>",
        "partitionUpperBound": "<upper_value_of_partition_column>",
        "partitionLowerBound": "<lower_value_of_partition_column>"
    }
}
```

## <a name="lookup-activity-properties"></a>조회 활동 속성

속성에 대한 자세한 내용을 보려면 [조회 활동을](control-flow-lookup-activity.md)선택합니다.


## <a name="next-steps"></a>다음 단계

Azure Data Factory의 복사 작업에서 원본 및 싱크로 지원되는 데이터 저장소 목록은 [지원되는 데이터 저장소 및 형식](copy-activity-overview.md#supported-data-stores-and-formats)을 참조하세요.
