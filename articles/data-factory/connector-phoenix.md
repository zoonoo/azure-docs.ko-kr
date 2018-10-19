---
title: Azure Data Factory를 사용하여 Phoenix에서 데이터 복사 | Microsoft Docs
description: Azure Data Factory 파이프라인의 복사 작업을 사용하여 Phoenix에서 지원되는 싱크 데이터 저장소로 데이터를 복사하는 방법에 대해 알아봅니다.
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
ms.date: 09/19/2018
ms.author: jingwang
ms.openlocfilehash: 78e432bf526ad270ae8543ad1be40727ed560d4b
ms.sourcegitcommit: ce526d13cd826b6f3e2d80558ea2e289d034d48f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/19/2018
ms.locfileid: "46367902"
---
# <a name="copy-data-from-phoenix-using-azure-data-factory"></a>Azure Data Factory를 사용하여 Phoenix에서 데이터 복사 

이 문서에서는 Azure Data Factory의 복사 작업을 사용하여 Phoenix에서 데이터를 복사하는 방법에 대해 설명합니다. 이 문서는 복사 작업에 대한 일반적인 개요를 제공하는 [복사 작업 개요](copy-activity-overview.md) 문서를 기반으로 합니다.

## <a name="supported-capabilities"></a>지원되는 기능

Phoenix에서 지원되는 모든 싱크 데이터 저장소로 데이터를 복사할 수 있습니다. 복사 작업의 원본/싱크로 지원되는 데이터 저장소 목록은 [지원되는 데이터 저장소](copy-activity-overview.md#supported-data-stores-and-formats) 표를 참조하세요.

Azure Data Factory는 연결을 사용하는 기본 제공 드라이버를 제공합니다. 따라서 이 커넥터를 사용하여 드라이버를 수동으로 설치하지 않아도 됩니다.

## <a name="getting-started"></a>시작

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

다음 섹션에서는 Phoenix 커넥터에 한정된 Data Factory 엔터티를 정의하는 데 사용되는 속성에 대해 자세히 설명합니다.

## <a name="linked-service-properties"></a>연결된 서비스 속성

다음은 Phoenix 연결된 서비스에 대해 지원되는 속성입니다.

| 자산 | 설명 | 필수 |
|:--- |:--- |:--- |
| 형식 | type 속성은 **Phoenix**로 설정해야 합니다. | yes |
| host | Phoenix 서버의 IP 주소 또는 호스트 이름입니다. 즉, 192.168.222.160입니다.  | yes |
| 포트 | Phoenix 서버가 클라이언트 연결을 수신하는 데 사용하는 TCP 포트입니다. 기본값은 8765입니다. Azure HDInsights에 연결하는 경우 포트를 443으로 지정합니다. | 아니요 |
| httpPath | Phoenix 서버에 해당하는 부분 URL입니다. 즉, /gateway/sandbox/phoenix/version입니다. HDInsights를 사용 중인 경우 `/hbasephoenix0`을 지정합니다.  | 아니요 |
| authenticationType | Phoenix 서버에 연결하는 데 사용되는 인증 메커니즘입니다. <br/>허용되는 값은 **Anonymous**, **UsernameAndPassword**, **WindowsAzureHDInsightService**입니다. | yes |
| 사용자 이름 | Phoenix 서버에 연결하는 데 사용되는 사용자 이름입니다.  | 아니요 |
| 암호 | 사용자 이름에 해당하는 암호입니다. 이 필드를 SecureString으로 표시하여 Data Factory에 안전하게 저장하거나 [Azure Key Vault에 저장되는 비밀을 참조](store-credentials-in-key-vault.md)합니다. | 아니요 |
| enableSsl | 서버에 대한 연결이 SSL을 사용하여 암호화되는지 여부를 지정합니다. 기본값은 False입니다.  | 아니요 |
| trustedCertPath | SSL을 통해 연결할 때 서버를 확인하기 위한 신뢰할 수 있는 CA 인증서를 포함하는 .pem 파일의 전체 경로입니다. 이 속성은 자체 호스팅 IR에서 SSL을 사용하는 경우에만 설정할 수 있습니다. 기본값은 IR과 함께 설치된 cacerts.pem 파일입니다.  | 아니요 |
| useSystemTrustStore | 시스템 신뢰 저장소 또는 지정된 PEM 파일의 CA 인증서를 사용할지 여부를 지정합니다. 기본값은 False입니다.  | 아니요 |
| allowHostNameCNMismatch | SSL을 통해 연결할 때 CA 발급 인증서 이름이 서버의 호스트 이름과 일치하도록 할지 여부를 지정합니다. 기본값은 False입니다.  | 아니요 |
| allowSelfSignedServerCert | 서버의 자체 서명된 인증서를 허용할지 여부를 지정합니다. 기본값은 False입니다.  | 아니요 |
| connectVia | 데이터 저장소에 연결하는 데 사용할 [Integration Runtime](concepts-integration-runtime.md)입니다. 자체 호스팅 Integration Runtime 또는 Azure Integration Runtime을 사용할 수 있습니다(데이터 저장소를 공개적으로 액세스할 수 있는 경우). 지정하지 않으면 기본 Azure Integration Runtime을 사용합니다. |아니요 |

>[!NOTE]
>클러스터가 HDInsight와 같은 고정 세션을 지원하지 않으면 Http 경로 설정의 끝에 노드 인덱스를 명시적으로 추가합니다. 예를 들어 `/hbasephoenix` 대신 `/hbasephoenix0`을 지정합니다.

**예제:**

```json
{
    "name": "PhoenixLinkedService",
    "properties": {
        "type": "Phoenix",
        "typeProperties": {
            "host" : "<cluster>.azurehdinsight.net",
            "port" : "443",
            "httpPath" : "/hbasephoenix0",
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

## <a name="dataset-properties"></a>데이터 집합 속성

데이터 집합 정의에 사용할 수 있는 섹션 및 속성의 전체 목록은 [데이터 집합](concepts-datasets-linked-services.md) 문서를 참조하세요. 이 섹션에서는 Phoenix 데이터 집합에서 지원하는 속성의 목록을 제공합니다.

Phoenix에서 데이터를 복사하려면 데이터 집합의 type 속성을 **PhoenixObject**로 설정합니다. 이 형식의 데이터 집합에는 추가적인 형식별 속성이 없습니다.

**예제**

```json
{
    "name": "PhoenixDataset",
    "properties": {
        "type": "PhoenixObject",
        "linkedServiceName": {
            "referenceName": "<Phoenix linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>복사 작업 속성

작업 정의에 사용할 수 있는 섹션 및 속성의 전체 목록은 [파이프라인](concepts-pipelines-activities.md) 문서를 참조하세요. 이 섹션에서는 Phoenix 원본에서 지원하는 속성의 목록을 제공합니다.

### <a name="phoenixsource-as-source"></a>PhoenixSource를 원본으로 설정

Phoenix에서 데이터를 복사하려면 복사 작업의 원본 형식을 **PhoenixSource**로 설정합니다. 복사 작업 **source** 섹션에서 다음 속성이 지원됩니다.

| 자산 | 설명 | 필수 |
|:--- |:--- |:--- |
| 형식 | 복사 작업 원본의 type 속성은 **PhoenixSource**로 설정해야 합니다. | yes |
| 쿼리 | 사용자 지정 SQL 쿼리를 사용하여 데이터를 읽습니다. 예: `"SELECT * FROM MyTable"` | yes |

**예제:**

```json
"activities":[
    {
        "name": "CopyFromPhoenix",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Phoenix input dataset name>",
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
                "type": "PhoenixSource",
                "query": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="next-steps"></a>다음 단계
Azure Data Factory에서 복사 작업의 원본 및 싱크로 지원되는 데이터 저장소 목록은 [지원되는 데이터 저장소](copy-activity-overview.md#supported-data-stores-and-formats)를 참조하세요.
