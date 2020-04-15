---
title: Azure 데이터 탐색기에서 또는 Azure에서 데이터를 복사합니다.
description: Azure Data Factory 파이프라인의 복사 작업을 사용하여 Azure Data Explorer로/에서 데이터를 복사하는 방법에 대해 알아봅니다.
services: data-factory
ms.author: orspodek
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.devlang: na
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 02/18/2020
ms.openlocfilehash: 5f0eada5184f2c8e36b0deb5a9d27a8a9230776d
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/14/2020
ms.locfileid: "81382758"
---
# <a name="copy-data-to-or-from-azure-data-explorer-by-using-azure-data-factory"></a>Azure 데이터 팩터리를 사용하여 Azure 데이터 탐색기에서 또는 Azure 데이터 탐색기에서 데이터를 복사합니다.

이 문서에서는 Azure Data Factory에서 복사 활동을 사용하여 Azure [데이터 탐색기에서](/azure/data-explorer/data-explorer-overview)데이터를 복사하는 방법을 설명합니다. 복사 활동에 대한 일반적인 개요를 제공하는 [복사 활동 개요](copy-activity-overview.md) 문서를 기반으로 합니다.

>[!TIP]
>일반적으로 Azure 데이터 팩터리 및 Azure 데이터 탐색기 통합에 대해 자세히 알아보려면 [Azure 데이터 탐색기와 Azure 데이터 팩터리 통합에](/azure/data-explorer/data-factory-integration)대해 자세히 알아봅니다.

## <a name="supported-capabilities"></a>지원되는 기능

이 Azure 데이터 탐색기 커넥터는 다음 활동에 대해 지원됩니다.

- [지원되는 소스/싱크 매트릭스로](copy-activity-overview.md) [활동 복사](copy-activity-overview.md)
- [조회 활동](control-flow-lookup-activity.md)

지원되는 모든 원본 데이터 저장소에서 Azure Data Explorer로 데이터를 복사할 수 있습니다. 또한 Azure Data Explorer에서 지원되는 모든 싱크 데이터 저장소로 데이터를 복사할 수 있습니다. 복사 활동이 원본 또는 싱크로 지원하는 데이터 저장소 목록은 [지원되는 데이터 저장소](copy-activity-overview.md#supported-data-stores-and-formats) 테이블을 참조하세요.

>[!NOTE]
>자체 호스팅 통합 런타임을 사용하여 온-프레미스 데이터 저장소를 통해 Azure Data Explorer에 데이터를 복사하는 것은 버전 3.14 이상에서 지원됩니다.

Azure 데이터 탐색기 커넥터를 사용하여 다음을 수행할 수 있습니다.

* **서비스 주체**와 함께 Azure AD(Azure Active Directory) 애플리케이션 토큰 인증을 사용하여 데이터를 복사합니다.
* 원본의 경우 KQL(Kusto) 쿼리를 사용하여 데이터를 검색합니다.
* 싱크의 경우 대상 테이블에 데이터를 추가합니다.

## <a name="getting-started"></a>시작

>[!TIP]
>Azure Data Explorer 커넥터의 연습은 Azure 데이터 팩터리 및 [데이터베이스에서 Azure 데이터 탐색기로의 대량 복사본을](/azure/data-explorer/data-factory-template) [사용하여 Azure 데이터 탐색기에서](/azure/data-explorer/data-factory-load-data) 부터 데이터 복사를 참조하세요.

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

다음 섹션에서는 Azure Data Explorer 커넥터에 한정된 Data Factory 엔터티를 정의하는 데 사용되는 속성에 대해 자세히 설명합니다.

## <a name="linked-service-properties"></a>연결된 서비스 속성

Azure 데이터 탐색기 커넥터는 서비스 주체 인증을 사용합니다. 다음 단계에 따라 서비스 주체를 얻고 권한을 부여합니다.

1. Azure [AD 테넌트를 사용 하](../storage/common/storage-auth-aad-app.md#register-your-application-with-an-azure-ad-tenant)여 응용 프로그램 등록의 단계에 따라 Azure Active Directory에 응용 프로그램 엔터티를 등록 합니다. 연결된 서비스를 정의하는 데 사용되므로 다음 값을 적어둡니다.

    - 애플리케이션 UI
    - 애플리케이션 키
    - 테넌트 ID

2. Azure 데이터 탐색기에서 서비스 주체에게 올바른 권한을 부여합니다. 역할 및 사용 권한 및 사용 권한 관리에 대한 자세한 내용은 [Azure Data Explorer 데이터베이스 사용 권한 관리를](/azure/data-explorer/manage-database-permissions) 참조하십시오. 일반적으로 다음을 수행해야 합니다.

    - **소스로**데이터베이스에 데이터베이스 **뷰어** 역할을 부여합니다.
    - **싱크로,** 데이터베이스에 적어도 **데이터베이스 인제스트자** 역할을 부여

>[!NOTE]
>데이터 팩터리 UI를 사용하여 작성하면 로그인 사용자 계정이 Azure Data Explorer 클러스터, 데이터베이스 및 테이블을 나열하는 데 사용됩니다. 이러한 작업에 대한 권한이 없는 경우 이름을 수동으로 입력합니다.

Azure 데이터 탐색기 링크 된 서비스에 대 한 다음 속성지원 됩니다.

| 속성 | 설명 | 필수 |
|:--- |:--- |:--- |
| type | **형식** 속성은 **AzureDataExplorer로**설정해야 합니다. | 예 |
| 엔드포인트(endpoint) | `https://<clusterName>.<regionName>.kusto.windows.net` 형식의 Azure Data Explorer 클러스터의 엔드포인트 URL입니다. | 예 |
| 데이터베이스 | 데이터베이스의 이름입니다. | 예 |
| tenant | 애플리케이션이 있는 테넌트 정보(도메인 이름 또는 테넌트 ID)를 지정합니다. 이를 [Kusto 연결 문자열에서](https://docs.microsoft.com/azure/kusto/api/connection-strings/kusto#application-authentication-properties)"권한 ID"라고 합니다. Azure 포털의 오른쪽 위 모서리에 있는 마우스 포인터를 마우스 포인터로 가져가서 검색합니다. | 예 |
| servicePrincipalId | 애플리케이션의 클라이언트 ID를 지정합니다. 이를 [Kusto 연결 문자열에서](https://docs.microsoft.com/azure/kusto/api/connection-strings/kusto#application-authentication-properties)"AAD 응용 프로그램 클라이언트 ID"라고 합니다. | 예 |
| servicePrincipalKey | 애플리케이션의 키를 지정합니다. 이를 [Kusto 연결 문자열에서](https://docs.microsoft.com/azure/kusto/api/connection-strings/kusto#application-authentication-properties)"AAD 응용 프로그램 키"라고 합니다. 이 필드를 **SecureString으로** 표시하여 데이터 팩터리에 안전하게 저장하거나 [Azure Key Vault에 저장된 보안 데이터를 참조합니다.](store-credentials-in-key-vault.md) | 예 |

**연결된 서비스 속성 예제:**

```json
{
    "name": "AzureDataExplorerLinkedService",
    "properties": {
        "type": "AzureDataExplorer",
        "typeProperties": {
            "endpoint": "https://<clusterName>.<regionName>.kusto.windows.net ",
            "database": "<database name>",
            "tenant": "<tenant name/id e.g. microsoft.onmicrosoft.com>",
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalKey": {
                "type": "SecureString",
                "value": "<service principal key>"
            }
        }
    }
}
```

## <a name="dataset-properties"></a>데이터 세트 속성

데이터 집합을 정의하는 데 사용할 수 있는 섹션 및 속성의 전체 목록은 [Azure 데이터 팩터리의 데이터 집합을](concepts-datasets-linked-services.md)참조하십시오. 이 섹션에는 Azure 데이터 탐색기 데이터 집합이 지원하는 속성이 나열됩니다.

Azure Data Explorer에 데이터를 복사하려면 데이터 세트의 형식 속성을 **AzureDataExplorerTable**로 설정합니다.

다음과 같은 속성이 지원됩니다.

| 속성 | 설명 | 필수 |
|:--- |:--- |:--- |
| type | **형식** 속성은 **AzureDataExplorerTable로**설정해야 합니다. | 예 |
| 테이블 | 연결된 서비스가 참조하는 테이블의 이름입니다. | 싱크의 경우 예이며, 원본의 경우 아니오입니다. |

**데이터 집합 속성 예제:**

```json
{
   "name": "AzureDataExplorerDataset",
    "properties": {
        "type": "AzureDataExplorerTable",
        "typeProperties": {
            "table": "<table name>"
        },
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<Azure Data Explorer linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>복사 작업 속성

활동을 정의하는 데 사용할 수 있는 섹션 및 속성의 전체 목록은 [Azure Data Factory의 파이프라인 및 활동을](concepts-pipelines-activities.md)참조하십시오. 이 섹션에서는 Azure Data 탐색기 원본 및 싱크가 지원하는 속성 목록을 제공합니다.

### <a name="azure-data-explorer-as-source"></a>Azure Data Explorer가 원본인 경우

Azure Data Explorer에서 데이터를 복사하려면 복사 작업 원본의 **형식** 속성을 **AzureDataExplorerSource**로 설정합니다. 다음 속성은 복사 활동 **소스** 섹션에서 지원됩니다.

| 속성 | 설명 | 필수 |
|:--- |:--- |:--- |
| type | 복사 활동 원본의 **형식** 속성을 **AzureDataExplorerSource로** 설정해야 합니다. | 예 |
| Query | [KQL 형식](/azure/kusto/query/)으로 제공되는 읽기 전용 요청입니다. 사용자 지정 KQL 쿼리를 참조로 사용합니다. | 예 |
| queryTimeout | 쿼리 요청 시간 시간 시간 전에 대기 시간입니다. 기본값은 10분(00:10:00)입니다. 허용되는 최대 값은 1시간(01:00:00)입니다. | 예 |
| 노트룬케이션 | 반환된 결과 집합을 트렁킨지 여부를 나타냅니다. 기본적으로 결과는 500,000개의 레코드 또는 64MB(MB) 후에 잘립니다. 활동의 올바른 동작을 보장하기 위해 잘림을 권장합니다. |예 |

>[!NOTE]
>기본적으로 Azure Data Explorer 원본의 크기 제한은 500,000개 레코드 또는 64MB입니다. 잘리지 않고 모든 레코드를 검색하려면 `set notruncation;` 쿼리 시작 부분에 지정할 수 있습니다. 자세한 내용은 [쿼리 제한을](https://docs.microsoft.com/azure/kusto/concepts/querylimits)참조하십시오.

**예제:**

```json
"activities":[
    {
        "name": "CopyFromAzureDataExplorer",
        "type": "Copy",
        "typeProperties": {
            "source": {
                "type": "AzureDataExplorerSource",
                "query": "TestTable1 | take 10",
                "queryTimeout": "00:10:00"
            },
            "sink": {
                "type": "<sink type>"
            }
        },
        "inputs": [
            {
                "referenceName": "<Azure Data Explorer input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ]
    }
]
```

### <a name="azure-data-explorer-as-sink"></a>Azure Data Explorer가 싱크인 경우

Azure Data Explorer로 데이터를 복사하려면 복사 작업 원본의 형식 속성을 **AzureDataExplorerSink**로 설정합니다. 다음 속성은 복사 활동 **싱크** 섹션에서 지원됩니다.

| 속성 | 설명 | 필수 |
|:--- |:--- |:--- |
| type | 복사 활동 싱크의 **형식** 속성을 **AzureDataExplorerSink**로 설정해야 합니다. | 예 |
| ingestionMappingName | Kusto 테이블에서 미리 만들어진 [매핑의](/azure/kusto/management/mappings#csv-mapping) 이름입니다. 원본에서 Azure Data Explorer(CSV/JSON/Avro 형식을 포함하여 [지원되는 모든 원본 저장소 및 형식에](copy-activity-overview.md#supported-data-stores-and-formats)적용되는) 열을 매핑하려면 복사 활동 [열 매핑(암시적으로](copy-activity-schema-and-type-mapping.md) 이름으로 또는 명시적으로 구성된 대로) 및/또는 Azure Data 탐색기 매핑을 사용할 수 있습니다. | 예 |
| 추가 속성 | Azure Data 탐색기 싱크에서 아직 설정하지 않은 수집 속성을 지정하는 데 사용할 수 있는 속성 백입니다. 특히, 섭취 태그를 지정하는 데 유용할 수 있습니다. [Azure 데이터 탐색 데이터 수집 문서에서](https://kusto.azurewebsites.net/docs/management/data-ingestion/index.html)자세히 알아봅니다. | 예 |

**예제:**

```json
"activities":[
    {
        "name": "CopyToAzureDataExplorer",
        "type": "Copy",
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "AzureDataExplorerSink",
                "ingestionMappingName": "<optional Azure Data Explorer mapping name>",
                "additionalProperties": {<additional settings for data ingestion>}
            }
        },
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Azure Data Explorer output dataset name>",
                "type": "DatasetReference"
            }
        ]
    }
]
```

## <a name="lookup-activity-properties"></a>조회 활동 속성

속성에 대한 자세한 내용은 [조회 활동을](control-flow-lookup-activity.md)참조하십시오.

## <a name="next-steps"></a>다음 단계

* Azure Data Factory의 복사 활동이 원본 및 싱크로 지원하는 데이터 저장소 목록은 [지원되는 데이터 저장소를](copy-activity-overview.md#supported-data-stores-and-formats)참조하십시오.

* [Azure 데이터 팩터리에서 Azure 데이터 탐색기로 데이터를 복사하는](/azure/data-explorer/data-factory-load-data)방법에 대해 자세히 알아봅니다.
