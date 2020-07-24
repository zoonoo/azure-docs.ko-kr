---
title: Salesforce 마케팅 클라우드에서 데이터 복사
description: Azure Data Factory 파이프라인의 복사 작업을 사용하여 Salesforce Marketing Cloud에서 지원되는 싱크 데이터 저장소로 데이터를 복사하는 방법에 대해 알아봅니다.
services: data-factory
documentationcenter: ''
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 07/17/2020
ms.openlocfilehash: 1f0fb1ee8580c0c7f6eb30228b65e0a3780ef0a8
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/23/2020
ms.locfileid: "87076807"
---
# <a name="copy-data-from-salesforce-marketing-cloud-using-azure-data-factory"></a>Azure Data Factory를 사용하여 Salesforce Marketing Cloud에서 데이터 복사

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

이 문서에서는 Azure Data Factory의 복사 작업을 사용하여 Salesforce Marketing Cloud에서 데이터를 복사하는 방법을 설명합니다. 이 문서는 복사 작업에 대한 일반적인 개요를 제공하는 [복사 작업 개요](copy-activity-overview.md) 문서를 기반으로 합니다.

## <a name="supported-capabilities"></a>지원되는 기능

이 Salesforce 마케팅 클라우드 커넥터는 다음과 같은 작업에 대해 지원 됩니다.

- [지원되는 원본/싱크 매트릭스](copy-activity-overview.md)를 사용한 [복사 작업](copy-activity-overview.md)
- [조회 작업](control-flow-lookup-activity.md)

Salesforce Marketing Cloud에서 지원되는 모든 싱크 데이터 저장소로 데이터를 복사할 수 있습니다. 복사 작업의 원본/싱크로 지원되는 데이터 저장소 목록은 [지원되는 데이터 저장소](copy-activity-overview.md#supported-data-stores-and-formats) 표를 참조하세요.

Salesforce 마케팅 클라우드 커넥터는 OAuth 2 인증을 지원 하 고 레거시 및 고급 패키지 유형을 모두 지원 합니다. 커넥터는 [Salesforce 마케팅 클라우드 REST API](https://developer.salesforce.com/docs/atlas.en-us.mc-apis.meta/mc-apis/index-api.htm)위에 빌드됩니다.

>[!NOTE]
>이 커넥터는 사용자 지정 개체 또는 사용자 지정 데이터 확장의 검색을 지원하지 않습니다.

## <a name="getting-started"></a>시작

.NET SDK, Python SDK, Azure PowerShell, REST API 또는 Azure Resource Manager 템플릿을 사용하여 복사 작업으로 파이프라인을 만들 수 있습니다. 복사 작업을 사용 하 여 파이프라인을 만드는 단계별 지침은 [복사 작업 자습서](quickstart-create-data-factory-dot-net.md) 를 참조 하세요.

다음 섹션에서는 Salesforce Marketing Cloud 커넥터에 한정된 데이터 팩터리 엔터티를 정의하는 데 사용되는 속성에 대해 자세히 설명합니다.

## <a name="linked-service-properties"></a>연결된 서비스 속성

Salesforce Marketing Cloud 연결된 서비스에 다음 속성이 지원됩니다.

| 속성 | Description | 필수 |
|:--- |:--- |:--- |
| type | 형식 속성은 **SalesforceMarketingCloud**로 설정되어야 합니다. | 예 |
| connectionProperties | Salesforce Marketing Cloud에 연결 하는 방법을 정의 하는 속성 그룹입니다. | 예 |
| ***에서 `connectionProperties` 다음을 수행 합니다.*** | | |
| authenticationType | 사용할 인증 방법을 지정합니다. 허용 되는 값은 `Enhanced sts OAuth 2.0` 또는 `OAuth_2.0` 입니다.<br><br>Salesforce Marketing Cloud 레거시 패키지 `OAuth_2.0` 는를 지원 하지만 향상 된 패키지 요구는 지원 `Enhanced sts OAuth 2.0` 합니다. <br>2019 년 8 월 1 일부 터는 Salesforce Marketing 클라우드가 레거시 패키지를 만드는 기능을 제거 했습니다. 모든 새 패키지는 향상 된 패키지입니다. | 예 |
| host | 향상 된 패키지의 경우 호스트는 "mc" 문자로 시작 하는 28 자 문자열로 표시 되는 하위 [도메인](https://developer.salesforce.com/docs/atlas.en-us.mc-apis.meta/mc-apis/your-subdomain-tenant-specific-endpoints.htm) 이어야 합니다 (예: `mc563885gzs27c5t9-63k636ttgm` ). <br>레거시 패키지의 경우를 지정 `www.exacttargetapis.com` 합니다. | 예 |
| clientId | Salesforce Marketing Cloud 애플리케이션과 관련된 클라이언트 ID입니다.  | 예 |
| clientSecret | Salesforce Marketing Cloud 애플리케이션과 관련된 클라이언트 암호입니다. 이 필드를 SecureString으로 표시 하 여 ADF에 안전 하 게 저장 하거나, Azure Key Vault에 암호를 저장 하 고, 데이터 복사를 수행할 때 ADF 복사 작업을 끌어올 수 있습니다. [Key Vault에서 자격 증명 저장](store-credentials-in-key-vault.md)에서 자세히 알아보세요. | 예 |
| useEncryptedEndpoints | 데이터 원본 엔드포인트가 HTTPS를 사용하여 암호화되는지 여부를 지정합니다. 기본값은 true입니다.  | 아니요 |
| useHostVerification | TLS를 통해 연결할 때 서버 인증서의 호스트 이름이 서버의 호스트 이름과 일치 해야 하는지 여부를 지정 합니다. 기본값은 true입니다.  | 아니요 |
| usePeerVerification | TLS를 통해 연결할 때 서버의 id를 확인할 지 여부를 지정 합니다. 기본값은 true입니다.  | 아니요 |

**예: 향상 된 패키지에 대해 향상 된 STS OAuth 2 인증 사용** 

```json
{
    "name": "SalesforceMarketingCloudLinkedService",
    "properties": {
        "type": "SalesforceMarketingCloud",
        "typeProperties": {
            "connectionProperties": {
                "host": "<subdomain e.g. mc563885gzs27c5t9-63k636ttgm>",
                "authenticationType": "Enhanced sts OAuth 2.0",
                "clientId": "<clientId>",
                "clientSecret": {
                     "type": "SecureString",
                     "value": "<clientSecret>"
                },
                "useEncryptedEndpoints": true,
                "useHostVerification": true,
                "usePeerVerification": true
            }
        }
    }
}

```

**예: 레거시 패키지에 OAuth 2 인증 사용** 

```json
{
    "name": "SalesforceMarketingCloudLinkedService",
    "properties": {
        "type": "SalesforceMarketingCloud",
        "typeProperties": {
            "connectionProperties": {
                "host": "www.exacttargetapis.com",
                "authenticationType": "OAuth_2.0",
                "clientId": "<clientId>",
                "clientSecret": {
                     "type": "SecureString",
                     "value": "<clientSecret>"
                },
                "useEncryptedEndpoints": true,
                "useHostVerification": true,
                "usePeerVerification": true
            }
        }
    }
}

```

다음 페이로드를 사용 하 여 Salesforce Marketing Cloud 연결 된 서비스를 사용 하는 경우 계속 해 서 있는 그대로 지원 되지만, 앞으로는 향상 된 패키지 지원을 추가 하는 새로운 기능을 사용 하는 것이 좋습니다.

```json
{
    "name": "SalesforceMarketingCloudLinkedService",
    "properties": {
        "type": "SalesforceMarketingCloud",
        "typeProperties": {
            "clientId": "<clientId>",
            "clientSecret": {
                 "type": "SecureString",
                 "value": "<clientSecret>"
            },
            "useEncryptedEndpoints": true,
            "useHostVerification": true,
            "usePeerVerification": true
        }
    }
}

```

## <a name="dataset-properties"></a>데이터 세트 속성

데이터 세트 정의에 사용할 수 있는 섹션 및 속성의 전체 목록은 [데이터 세트](concepts-datasets-linked-services.md) 문서를 참조하세요. 이 섹션에서는 Salesforce Marketing Cloud 데이터 세트에서 지원하는 속성의 목록을 제공합니다.

Salesforce Marketing Cloud에서 데이터를 복사하려면 데이터 세트의 형식 속성을 **SalesforceMarketingCloudObject**로 설정합니다. 다음과 같은 속성이 지원됩니다.

| 속성 | Description | 필수 |
|:--- |:--- |:--- |
| type | 데이터 집합의 type 속성은 **SalesforceMarketingCloudObject** 로 설정 해야 합니다. | 예 |
| tableName | 테이블 이름입니다. | 아니요(작업 원본에서 "query"가 지정된 경우) |

**예제**

```json
{
    "name": "SalesforceMarketingCloudDataset",
    "properties": {
        "type": "SalesforceMarketingCloudObject",
        "typeProperties": {},
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<SalesforceMarketingCloud linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>복사 작업 속성

작업 정의에 사용할 수 있는 섹션 및 속성의 전체 목록은 [파이프라인](concepts-pipelines-activities.md) 문서를 참조하세요. 이 섹션에서는 Salesforce Marketing Cloud 원본에서 지원하는 속성의 목록을 제공합니다.

### <a name="salesforce-marketing-cloud-as-source"></a>원본으로써의 Salesforce Marketing Cloud

Salesforce Marketing Cloud에서 데이터를 복사하려면 복사 작업의 원본 형식을 **SalesforceMarketingCloudSource**로 설정합니다. 복사 작업 **source** 섹션에서 다음 속성이 지원됩니다.

| 속성 | Description | 필수 |
|:--- |:--- |:--- |
| type | 복사 작업 원본의 형식 속성을 **SalesforceMarketingCloudSource**로 설정해야 합니다. | 예 |
| Query | 사용자 지정 SQL 쿼리를 사용하여 데이터를 읽습니다. 예: `"SELECT * FROM MyTable"` | 아니요(데이터 세트의 "tableName"이 지정된 경우) |

**예:**

```json
"activities":[
    {
        "name": "CopyFromSalesforceMarketingCloud",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<SalesforceMarketingCloud input dataset name>",
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
                "type": "SalesforceMarketingCloudSource",
                "query": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="lookup-activity-properties"></a>조회 작업 속성

속성에 대한 자세한 내용을 보려면 [조회 작업](control-flow-lookup-activity.md)을 확인하세요.

## <a name="next-steps"></a>다음 단계
Azure Data Factory에서 복사 작업의 원본 및 싱크로 지원되는 데이터 저장소 목록은 [지원되는 데이터 저장소](copy-activity-overview.md#supported-data-stores-and-formats)를 참조하세요.
