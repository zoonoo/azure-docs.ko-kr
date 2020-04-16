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
ms.date: 09/04/2019
ms.author: jingwang
ms.openlocfilehash: 3720d917d71fa4e8c5a14bb60fdc7c405be4bfdb
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81410450"
---
# <a name="copy-data-from-hive-using-azure-data-factory"></a>Azure Data Factory를 사용하여 Hive에서 데이터 복사 
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

이 문서에서는 Azure Data Factory의 복사 작업을 사용하여 Hive에서 데이터를 복사하는 방법에 대해 설명합니다. 이 문서는 복사 작업에 대한 일반적인 개요를 제공하는 [복사 작업 개요](copy-activity-overview.md) 문서를 기반으로 합니다.

## <a name="supported-capabilities"></a>지원되는 기능

이 Hive 커넥터는 다음 활동에 대해 지원됩니다.

- [지원되는 소스/싱크 매트릭스로](copy-activity-overview.md) [활동 복사](copy-activity-overview.md)
- [조회 활동](control-flow-lookup-activity.md)

Hive에서 지원되는 모든 싱크 데이터 저장소로 데이터를 복사할 수 있습니다. 복사 작업의 원본/싱크로 지원되는 데이터 저장소 목록은 [지원되는 데이터 저장소](copy-activity-overview.md#supported-data-stores-and-formats) 표를 참조하세요.

Azure Data Factory는 연결을 사용하는 기본 제공 드라이버를 제공합니다. 따라서 이 커넥터를 사용하여 드라이버를 수동으로 설치하지 않아도 됩니다.

## <a name="prerequisites"></a>사전 요구 사항

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

## <a name="getting-started"></a>시작

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

다음 섹션에서는 Hive 커넥터에 한정된 Data Factory 엔터티를 정의하는 데 사용되는 속성에 대해 자세히 설명합니다.

## <a name="linked-service-properties"></a>연결된 서비스 속성

다음은 Hive 연결된 서비스에 대해 지원되는 속성입니다.

| 속성 | Description | 필수 |
|:--- |:--- |:--- |
| type | type 속성은 **Hive**로 설정해야 합니다. | 예 |
| host | 여러 호스트에 대해 ';'로 구분되는 Hive 서버의 IP 주소 또는 호스트 이름(serviceDiscoveryMode가 활성화된 경우에만).  | 예 |
| 포트 | Hive 서버가 클라이언트 연결을 수신하는 데 사용하는 TCP 포트입니다. Azure HDInsights에 연결하는 경우 포트를 443으로 지정합니다. | 예 |
| serverType | Hive 서버의 유형입니다. <br/>허용되는 값은 **HiveServer1**, **HiveServer2**, **HiveThriftServer**입니다. | 예 |
| thriftTransportProtocol | Thrift 계층에서 사용할 전송 프로토콜입니다. <br/>허용되는 값은 **Binary**, **SASL**, **HTTP**입니다. | 예 |
| authenticationType | Hive 서버에 액세스하는 데 사용되는 인증 방법입니다. <br/>허용되는 값은 **Anonymous**, **Username**, **UsernameAndPassword**, **WindowsAzureHDInsightService**입니다. | 예 |
| serviceDiscoveryMode | true이면 ZooKeeper 서비스 사용을 나타내고, false이면 그렇지 않습니다.  | 예 |
| zooKeeperNameSpace | ZooKeeper에서 Hive 서버 2 노드가 추가되는 네임스페이스입니다.  | 예 |
| useNativeQuery | 드라이버가 기본 HiveQL 쿼리를 사용하는지 또는 HiveQL에서 동일한 형식으로 변환하는지 여부를 지정합니다.  | 예 |
| 사용자 이름 | Hive 서버에 액세스하는 데 사용하는 사용자 이름입니다.  | 예 |
| password | 사용자에 해당하는 암호입니다. 이 필드를 SecureString으로 표시하여 Data Factory에 안전하게 저장하거나, [Azure Key Vault에 저장된 비밀을 참조](store-credentials-in-key-vault.md)합니다. | 예 |
| httpPath | Hive 서버에 해당하는 부분 URL입니다.  | 예 |
| enableSsl | 서버에 대한 연결이 TLS를 사용하여 암호화되는지 여부를 지정합니다. 기본값은 False입니다.  | 예 |
| trustedCertPath | TLS를 통해 연결할 때 서버를 확인하기 위한 신뢰할 수 있는 CA 인증서가 포함된 .pem 파일의 전체 경로입니다. 이 속성은 자체 호스팅 IR에서 TLS를 사용할 때만 설정할 수 있습니다. 기본값은 IR과 함께 설치된 cacerts.pem 파일입니다.  | 예 |
| useSystemTrustStore | 시스템 신뢰 저장소 또는 지정된 PEM 파일의 CA 인증서를 사용할지 여부를 지정합니다. 기본값은 False입니다.  | 예 |
| allowHostNameCNMismatch | TLS를 통해 연결할 때 서버의 호스트 이름과 일치하도록 CA에서 발급한 TLS/SSL 인증서 이름을 요구할지 여부를 지정합니다. 기본값은 False입니다.  | 예 |
| allowSelfSignedServerCert | 서버의 자체 서명된 인증서를 허용할지 여부를 지정합니다. 기본값은 False입니다.  | 예 |
| connectVia | 데이터 저장소에 연결하는 데 사용할 [통합 런타임입니다.](concepts-integration-runtime.md) [필수 구성 조건](#prerequisites) 섹션에서 자세히 알아보십시오. 지정하지 않으면 기본 Azure Integration Runtime을 사용합니다. |예 |

**예제:**

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

데이터 집합을 정의하는 데 사용할 수 있는 섹션 및 속성의 전체 목록은 [데이터 집합](concepts-datasets-linked-services.md) 문서를 참조하세요. 이 섹션에서는 Hive 데이터 세트에서 지원하는 속성의 목록을 제공합니다.

Hive에서 데이터를 복사하려면 데이터 세트의 type 속성을 **HiveObject**로 설정합니다. 다음과 같은 속성이 지원됩니다.

| 속성 | Description | 필수 |
|:--- |:--- |:--- |
| type | 데이터 집합의 형식 속성을 다음과 같은 **HiveObject로** 설정해야 합니다. | 예 |
| 스키마 | 스키마의 이름입니다. |아니요(작업 원본에서 "query"가 지정된 경우)  |
| 테이블 | 테이블 이름입니다. |아니요(작업 원본에서 "query"가 지정된 경우)  |
| tableName | 스키마 파트를 포함한 테이블 의 이름입니다. 이 속성은 이전 버전과의 호환성을 위해 지원됩니다. 새 워크로드의 `schema` 경우 `table`및 를 사용합니다. | 아니요(작업 원본에서 "query"가 지정된 경우) |

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

Hive에서 데이터를 복사하려면 복사 작업의 원본 형식을 **HiveSource**로 설정합니다. 다음 속성은 복사 활동 **소스** 섹션에서 지원됩니다.

| 속성 | Description | 필수 |
|:--- |:--- |:--- |
| type | 복사 작업 원본의 type 속성은 **HiveSource**로 설정해야 합니다. | 예 |
| Query | 사용자 지정 SQL 쿼리를 사용하여 데이터를 읽습니다. 예: `"SELECT * FROM MyTable"` | 아니요(데이터 세트의 "tableName"이 지정된 경우) |

**예제:**

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

## <a name="lookup-activity-properties"></a>조회 활동 속성

속성에 대한 자세한 내용을 보려면 [조회 활동을](control-flow-lookup-activity.md)선택합니다.


## <a name="next-steps"></a>다음 단계
Azure Data Factory에서 복사 작업의 원본 및 싱크로 지원되는 데이터 저장소 목록은 [지원되는 데이터 저장소](copy-activity-overview.md#supported-data-stores-and-formats)를 참조하세요.
