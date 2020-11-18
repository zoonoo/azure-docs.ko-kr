---
title: Azure Data Factory를 사용하여 Hive에서 데이터 복사
description: Azure Data Factory 파이프라인의 복사 작업을 사용하여 Hive에서 지원되는 싱크 데이터 저장소로 데이터를 복사하는 방법에 대해 알아봅니다.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 11/17/2020
ms.author: jingwang
ms.openlocfilehash: 4207c4ddfcbab325b1ae119dcd200af30fc59f58
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/18/2020
ms.locfileid: "94844955"
---
# <a name="copy-and-transform-data-from-hive-using-azure-data-factory"></a>Azure Data Factory를 사용 하 여 Hive에서 데이터 복사 및 변환 
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

이 문서에서는 Azure Data Factory의 복사 작업을 사용하여 Hive에서 데이터를 복사하는 방법에 대해 설명합니다. 이 문서는 복사 작업에 대한 일반적인 개요를 제공하는 [복사 작업 개요](copy-activity-overview.md) 문서를 기반으로 합니다.

## <a name="supported-capabilities"></a>지원되는 기능

이 Hive 커넥터는 다음과 같은 작업에 대해 지원 됩니다.

- [지원되는 원본/싱크 매트릭스](copy-activity-overview.md)를 사용한 [복사 작업](copy-activity-overview.md)
- [조회 작업](control-flow-lookup-activity.md)

Hive에서 지원되는 모든 싱크 데이터 저장소로 데이터를 복사할 수 있습니다. 복사 작업의 원본/싱크로 지원되는 데이터 저장소 목록은 [지원되는 데이터 저장소](copy-activity-overview.md#supported-data-stores-and-formats) 표를 참조하세요.

Azure Data Factory는 연결을 사용하는 기본 제공 드라이버를 제공합니다. 따라서 이 커넥터를 사용하여 드라이버를 수동으로 설치하지 않아도 됩니다.

## <a name="prerequisites"></a>필수 구성 요소

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

## <a name="getting-started"></a>시작

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

다음 섹션에서는 Hive 커넥터에 한정된 Data Factory 엔터티를 정의하는 데 사용되는 속성에 대해 자세히 설명합니다.

## <a name="linked-service-properties"></a>연결된 서비스 속성

다음은 Hive 연결된 서비스에 대해 지원되는 속성입니다.

| 속성 | 설명 | 필수 |
|:--- |:--- |:--- |
| type | type 속성은 **Hive** 로 설정해야 합니다. | 예 |
| host | 여러 호스트에 대해 '; '로 구분 된 Hive 서버의 IP 주소 또는 호스트 이름입니다 (serviceDiscoveryMode를 사용 하는 경우에만).  | 예 |
| 포트 | Hive 서버가 클라이언트 연결을 수신하는 데 사용하는 TCP 포트입니다. Azure HDInsights에 연결하는 경우 포트를 443으로 지정합니다. | 예 |
| serverType | Hive 서버의 유형입니다. <br/>허용되는 값은 **HiveServer1**, **HiveServer2**, **HiveThriftServer** 입니다. | 아니요 |
| thriftTransportProtocol | Thrift 계층에서 사용할 전송 프로토콜입니다. <br/>허용되는 값은 **Binary**, **SASL**, **HTTP** 입니다. | 예 |
| authenticationType | Hive 서버에 액세스하는 데 사용되는 인증 방법입니다. <br/>허용 되는 값은 **Anonymous**, **Username**, **UsernameAndPassword**, **windowsazurehdinsightservice입니다.** 입니다. Kerberos 인증은 지금은 지원 되지 않습니다. | 예 |
| serviceDiscoveryMode | true이면 ZooKeeper 서비스 사용을 나타내고, false이면 그렇지 않습니다.  | 아니요 |
| zooKeeperNameSpace | ZooKeeper에서 Hive 서버 2 노드가 추가되는 네임스페이스입니다.  | 아니요 |
| useNativeQuery | 드라이버가 네이티브 HiveQL 쿼리를 사용 하는지 여부를 지정 하거나 HiveQL에서 해당 형식으로 변환 합니다.  | 아니요 |
| 사용자 이름 | Hive 서버에 액세스하는 데 사용하는 사용자 이름입니다.  | 예 |
| password | 사용자에 해당하는 암호입니다. 이 필드를 SecureString으로 표시하여 Data Factory에 안전하게 저장하거나 [Azure Key Vault에 저장되는 비밀을 참조](store-credentials-in-key-vault.md)합니다. | 예 |
| httpPath | Hive 서버에 해당하는 부분 URL입니다.  | 예 |
| enableSsl | TLS를 사용 하 여 서버에 대 한 연결을 암호화할지 여부를 지정 합니다. 기본값은 false입니다.  | 아니요 |
| trustedCertPath | TLS를 통해 연결할 때 서버를 확인 하는 데 사용할 신뢰할 수 있는 CA 인증서를 포함 하는 pem 파일의 전체 경로입니다. 이 속성은 자체 호스팅 IR에서 TLS를 사용 하는 경우에만 설정할 수 있습니다. 기본값은 IR과 함께 설치된 cacerts.pem 파일입니다.  | 아니요 |
| useSystemTrustStore | 시스템 신뢰 저장소 또는 지정된 PEM 파일의 CA 인증서를 사용할지 여부를 지정합니다. 기본값은 false입니다.  | 아니요 |
| allowHostNameCNMismatch | TLS를 통해 연결할 때 CA에서 발급 한 TLS/SSL 인증서 이름이 서버의 호스트 이름과 일치 하도록 할지 여부를 지정 합니다. 기본값은 false입니다.  | 아니요 |
| allowSelfSignedServerCert | 서버의 자체 서명된 인증서를 허용할지 여부를 지정합니다. 기본값은 false입니다.  | 예 |
| connectVia | 데이터 저장소에 연결하는 데 사용할 [Integration Runtime](concepts-integration-runtime.md)입니다. [필수 조건](#prerequisites) 섹션에서 자세히 알아보세요. 지정하지 않으면 기본 Azure Integration Runtime을 사용합니다. |예 |
| storageReference | 매핑 데이터 흐름에서 데이터를 준비 하는 데 사용 되는 저장소 계정의 연결 된 서비스에 대 한 참조입니다. 이는 데이터 흐름 매핑에 Hive 연결 된 서비스를 사용 하는 경우에만 필요 합니다. | 예 |

**예:**

```json
{
    "name": "HiveLinkedService",
    "properties": {
        "type": "Hive",
        "typeProperties": {
            "host" : "<cluster>.azurehdinsight.net",
            "port" : "<port>",
            "authenticationType" : "WindowsAzureHDInsightService",
            "username" : "<username>",
            "password": {
                "type": "SecureString",
                "value": "<password>"
            }
        }
    }
}
```

## <a name="dataset-properties"></a>데이터 세트 속성

데이터 세트 정의에 사용할 수 있는 섹션 및 속성의 전체 목록은 [데이터 세트](concepts-datasets-linked-services.md) 문서를 참조하세요. 이 섹션에서는 Hive 데이터 세트에서 지원하는 속성의 목록을 제공합니다.

Hive에서 데이터를 복사하려면 데이터 세트의 type 속성을 **HiveObject** 로 설정합니다. 다음과 같은 속성이 지원됩니다.

| 속성 | 설명 | 필수 |
|:--- |:--- |:--- |
| type | 데이터 집합의 type 속성은 **HiveObject** 로 설정 해야 합니다. | 예 |
| 스키마 | 스키마의 이름입니다. |아니요(작업 원본에서 "query"가 지정된 경우)  |
| 테이블 | 테이블 이름입니다. |아니요(작업 원본에서 "query"가 지정된 경우)  |
| tableName | 스키마 파트를 포함 하는 테이블의 이름입니다. 이 속성은 이전 버전과의 호환성을 위해 지원됩니다. 새 워크로드의 경우 `schema` 및 `table`을 사용합니다. | 아니요(작업 원본에서 "query"가 지정된 경우) |

**예제**

```json
{
    "name": "HiveDataset",
    "properties": {
        "type": "HiveObject",
        "typeProperties": {},
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<Hive linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>복사 작업 속성

작업 정의에 사용할 수 있는 섹션 및 속성의 전체 목록은 [파이프라인](concepts-pipelines-activities.md) 문서를 참조하세요. 이 섹션에서는 Hive 원본에서 지원하는 속성의 목록을 제공합니다.

### <a name="hivesource-as-source"></a>HiveSource를 원본으로 설정

Hive에서 데이터를 복사하려면 복사 작업의 원본 형식을 **HiveSource** 로 설정합니다. 복사 작업 **source** 섹션에서 다음 속성이 지원됩니다.

| 속성 | 설명 | 필수 |
|:--- |:--- |:--- |
| type | 복사 작업 원본의 type 속성은 **HiveSource** 로 설정해야 합니다. | 예 |
| Query | 사용자 지정 SQL 쿼리를 사용하여 데이터를 읽습니다. 예: `"SELECT * FROM MyTable"` | 아니요(데이터 세트의 "tableName"이 지정된 경우) |

**예:**

```json
"activities":[
    {
        "name": "CopyFromHive",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Hive input dataset name>",
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
                "type": "HiveSource",
                "query": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="mapping-data-flow-properties"></a>매핑 데이터 흐름 속성

Hive 커넥터는 데이터 흐름 매핑에서 [인라인 데이터 집합](data-flow-source.md#inline-datasets) 원본으로 지원 됩니다. 쿼리를 사용 하거나 HDInsight의 Hive 테이블에서 직접 읽습니다. Hive 데이터는 데이터 흐름의 일부로 변환 되기 전에 저장소 계정에서 parquet 파일로 준비 됩니다. 

### <a name="source-properties"></a>원본 속성

다음 표에서는 hive 원본에서 지 원하는 속성을 나열 합니다. 이러한 속성은 **원본 옵션** 탭에서 편집할 수 있습니다.

| 이름 | 설명 | 필수 | 허용되는 값 | 데이터 흐름 스크립트 속성 |
| ---- | ----------- | -------- | -------------- | ---------------- |
| 스토어 | 저장소는 이어야 합니다. `hive` | 예 |  `hive` | store | 
| 서식 | 테이블이 나 쿼리를 읽고 있는지 여부 | 예 | `table` 또는 `query` | format |
| 스키마 이름 | 테이블에서 읽는 경우 원본 테이블의 스키마 |  예, 형식이 인 경우 `table` | 문자열 | schemaName |
| 테이블 이름 | 테이블에서 읽는 경우 테이블 이름 |   예, 형식이 인 경우 `table` | 문자열 | tableName |
| 쿼리 | Format이 이면 `query` Hive 연결 된 서비스에 대 한 원본 쿼리 | 예, 형식이 인 경우 `query` | 문자열 | Query |
| 상태의 | Hive 테이블은 항상 준비 됩니다. | 예 | `true` | 상태의 |
| 스토리지 컨테이너 | Hive에서 읽거나 Hive에 쓰기 전에 데이터를 준비 하는 데 사용 되는 저장소 컨테이너입니다. Hive 클러스터에는이 컨테이너에 대 한 액세스 권한이 있어야 합니다. | 예 | 문자열 | storageContainer |
| 준비 데이터베이스 | 연결 된 서비스에 지정 된 사용자 계정에 액세스할 수 있는 스키마/데이터베이스입니다. 준비 후에 외부 테이블을 만드는 데 사용 되며 나중에 삭제 됩니다. | no | `true` 또는 `false` | stagingDatabaseName |
| SQL 스크립트 이전 | 데이터를 읽기 전에 Hive 테이블에서 실행 하는 SQL 코드 | no | 문자열 | 보도 Qls |

#### <a name="source-example"></a>원본 예제

다음은 Hive 원본 구성의 예입니다.

![Hive 원본 예](media/data-flow/hive-source.png "[Hive 원본 예제")

이러한 설정은 다음 데이터 흐름 스크립트로 변환 됩니다.

```
source(
    allowSchemaDrift: true,
    validateSchema: false,
    ignoreNoFilesFound: false,
    format: 'table',
    store: 'hive',
    schemaName: 'default',
    tableName: 'hivesampletable',
    staged: true,
    storageContainer: 'khive',
    storageFolderPath: '',
    stagingDatabaseName: 'default') ~> hivesource
```
### <a name="known-limitations"></a>알려진 제한 사항

* 배열, 맵, 구조체 및 공용 구조체와 같은 복합 형식은 읽기에 대해 지원 되지 않습니다. 
* Hive 커넥터는 4.0 이상 버전의 Azure HDInsight에서 Hive 테이블만 지원 합니다 (Apache Hive 3.1.0).

## <a name="lookup-activity-properties"></a>조회 작업 속성

속성에 대한 자세한 내용을 보려면 [조회 작업](control-flow-lookup-activity.md)을 확인하세요.


## <a name="next-steps"></a>다음 단계
Azure Data Factory에서 복사 작업의 원본 및 싱크로 지원되는 데이터 저장소 목록은 [지원되는 데이터 저장소](copy-activity-overview.md#supported-data-stores-and-formats)를 참조하세요.
