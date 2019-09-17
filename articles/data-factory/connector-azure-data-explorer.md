---
title: Azure Data Factory를 사용하여 Azure Data Explorer로/에서 데이터 복사
description: Azure Data Factory 파이프라인의 복사 작업을 사용하여 Azure Data Explorer로/에서 데이터를 복사하는 방법에 대해 알아봅니다.
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
ms.date: 08/01/2019
ms.author: orspodek
ms.openlocfilehash: 5cb08ddafe2075ae27ced6d70894696025df0a86
ms.sourcegitcommit: a819209a7c293078ff5377dee266fa76fd20902c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/16/2019
ms.locfileid: "71010263"
---
# <a name="copy-data-to-or-from-azure-data-explorer-using-azure-data-factory"></a>Azure Data Factory를 사용하여 Azure Data Explorer로/에서 데이터 복사

이 문서에서는 Azure Data Factory의 복사 작업을 사용하여 [Azure Data Explorer](../data-explorer/data-explorer-overview.md)로/에서 데이터를 복사하는 방법을 설명합니다. 이 문서는 복사 작업에 대한 일반적인 개요를 제공하는 [복사 작업 개요](copy-activity-overview.md) 문서를 기반으로 합니다.

## <a name="supported-capabilities"></a>지원되는 기능

이 Azure 데이터 탐색기 커넥터는 다음과 같은 작업에 대해 지원 됩니다.

- [지원 되는 원본/싱크 매트릭스](copy-activity-overview.md) 를 사용 하 여 [복사 작업](copy-activity-overview.md)
- [조회 작업](control-flow-lookup-activity.md)

지원되는 모든 원본 데이터 저장소에서 Azure Data Explorer로 데이터를 복사할 수 있습니다. 또한 Azure Data Explorer에서 지원되는 모든 싱크 데이터 저장소로 데이터를 복사할 수 있습니다. 복사 작업의 원본 또는 싱크로 지원되는 데이터 저장소 목록은 [지원되는 데이터 저장소](copy-activity-overview.md) 표를 참조하세요.

>[!NOTE]
>자체 호스팅 Integration Runtime를 사용 하 여 온-프레미스 데이터 저장소와 Azure 데이터 탐색기 간에 데이터를 복사 하는 것은 버전 3.14부터 지원 됩니다.

Azure Data Explorer 커넥터를 사용하여 다음을 수행할 수 있습니다.

* **서비스 주체**와 함께 Azure AD(Azure Active Directory) 애플리케이션 토큰 인증을 사용하여 데이터를 복사합니다.
* 원본의 경우 KQL(Kusto) 쿼리를 사용하여 데이터를 검색합니다.
* 싱크의 경우 대상 테이블에 데이터를 추가합니다.

## <a name="getting-started"></a>시작

>[!TIP]
>Azure 데이터 탐색기 커넥터를 사용 하는 연습은 Azure Data Factory를 [사용 하 여 azure 데이터 탐색기 간에 데이터 복사](../data-explorer/data-factory-load-data.md)를 참조 하세요.

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

다음 섹션에서는 Azure Data Explorer 커넥터에 한정된 Data Factory 엔터티를 정의하는 데 사용되는 속성에 대해 자세히 설명합니다.

## <a name="linked-service-properties"></a>연결된 서비스 속성

Azure 데이터 탐색기 커넥터는 서비스 주체 인증을 사용 합니다. 다음 단계를 수행 하 여 서비스 주체를 가져오고 사용 권한을 부여 합니다.

1. [Azure AD 테넌트에 애플리케이션 등록](../storage/common/storage-auth-aad-app.md#register-your-application-with-an-azure-ad-tenant)에 따라 Azure AD(Azure Active Directory)에 애플리케이션 항목을 등록합니다. 연결된 서비스를 정의하는 데 사용되므로 다음 값을 적어둡니다.

    - 애플리케이션 ID
    - 애플리케이션 키
    - 테넌트 ID

2. Azure 데이터 탐색기에서 서비스 주체에 게 적절 한 권한을 부여 합니다. 역할 및 권한에 대 한 자세한 정보와 함께 [Azure 데이터 탐색기 데이터베이스 사용 권한 관리](../data-explorer/manage-database-permissions.md) 및 사용 권한 관리를 참조 하세요. 일반적으로 다음을 수행 해야 합니다.

    - **원본으로**데이터베이스에 **데이터베이스 뷰어** 역할을 하나 이상 부여 합니다.
    - **싱크로**데이터베이스에 적어도 **데이터베이스 수집기** 역할을 부여 합니다.

>[!NOTE]
>ADF UI를 사용 하 여 작성 하는 경우 로그인 사용자 계정은 Azure 데이터 탐색기 클러스터, 데이터베이스 및 테이블을 나열 하는 데 사용 됩니다. 이러한 작업에 대 한 권한이 없으면 이름을 수동으로 입력 합니다.

Azure Data Explorer 연결된 서비스에 다음 속성이 지원됩니다.

| 속성 | 설명 | 필수 |
|:--- |:--- |:--- |
| type | **형식** 속성을 **AzureDataExplorer**로 설정해야 합니다. | 예 |
| endpoint | `https://<clusterName>.<regionName>.kusto.windows.net` 형식의 Azure Data Explorer 클러스터의 엔드포인트 URL입니다. | 예 |
| database | 데이터베이스의 이름입니다. | 예 |
| 테넌트(tenant) | 애플리케이션이 있는 테넌트 정보(도메인 이름 또는 테넌트 ID)를 지정합니다. 이는 [Kusto 연결 문자열](https://docs.microsoft.com/azure/kusto/api/connection-strings/kusto#application-authentication-properties)에서 일반적으로 "**기관 ID**"로 인식 됩니다. Azure Portal의 오른쪽 위를 마우스로 가리켜 검색합니다. | 예 |
| servicePrincipalId | 애플리케이션의 클라이언트 ID를 지정합니다. 이는 일반적으로 [Kusto 연결 문자열](https://docs.microsoft.com/azure/kusto/api/connection-strings/kusto#application-authentication-properties)에서 "**AAD 응용 프로그램 클라이언트 ID**"로 인식 됩니다. | 예 |
| servicePrincipalKey | 애플리케이션의 키를 지정합니다. 이는 일반적으로 [Kusto 연결 문자열](https://docs.microsoft.com/azure/kusto/api/connection-strings/kusto#application-authentication-properties)에서 "**AAD 응용 프로그램 키**"로 인식 됩니다. 이 필드를 **SecureString**으로 표시하여 Data Factory에 안전하게 저장하거나, [Azure Key Vault에 저장된 비밀을 참조](store-credentials-in-key-vault.md)합니다. | 예 |

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

데이터 세트 정의에 사용할 수 있는 섹션 및 속성의 전체 목록은 [데이터 세트](concepts-datasets-linked-services.md) 문서를 참조하세요. 이 섹션에서는 Azure Data Explorer 데이터 세트에서 지원하는 속성 목록을 제공합니다.

Azure Data Explorer에 데이터를 복사하려면 데이터 세트의 형식 속성을 **AzureDataExplorerTable**로 설정합니다.

다음과 같은 속성이 지원됩니다.

| 속성 | 설명 | 필수 |
|:--- |:--- |:--- |
| type | **형식** 속성을 **AzureDataExplorerTable**로 설정해야 합니다. | 예 |
| table | 연결된 서비스가 참조하는 테이블의 이름입니다. | 싱크의 경우 예이며, 원본의 경우 아니오입니다. |

**데이터 세트 속성 예제**

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

작업 정의에 사용할 수 있는 섹션 및 속성의 전체 목록은 [파이프라인](concepts-pipelines-activities.md) 문서를 참조하세요. 이 섹션에서는 Azure Data Explorer 원본 및 싱크에서 지원하는 속성 목록을 제공합니다.

### <a name="azure-data-explorer-as-source"></a>Azure Data Explorer가 원본인 경우

Azure Data Explorer에서 데이터를 복사하려면 복사 작업 원본의 **형식** 속성을 **AzureDataExplorerSource**로 설정합니다. 복사 작업 **source** 섹션에서 다음 속성이 지원됩니다.

| 속성 | 설명 | 필수 |
|:--- |:--- |:--- |
| type | 복사 작업 원본의 **형식** 속성을 다음으로 설정해야 합니다. **AzureDataExplorerSource** | 예 |
| query | [KQL 형식](/azure/kusto/query/)으로 제공되는 읽기 전용 요청입니다. 사용자 지정 KQL 쿼리를 참조로 사용합니다. | 예 |
| queryTimeout | 쿼리 요청 전의 대기 시간이 초과되었습니다. 기본값은 10분(00:10:00)이며, 허용되는 최댓값은 1시간(01:00:00)입니다. | 아니요 |
| noTruncation | 반환 된 결과 집합을 잘라낼 지 여부를 나타냅니다. 기본적으로 결과는 50만 레코드 또는 64 MB 이후 잘립니다. 작업의 적절 한 동작을 수행 하는 데는 잘림이 좋습니다. |아니요 |

>[!NOTE]
>기본적으로 Azure 데이터 탐색기 원본의 크기 제한은 50만 레코드 또는 64 MB입니다. 잘림 없이 모든 레코드를 검색 하려면 쿼리의 시작 부분에 `set notruncation;` 을 지정 하면 됩니다. 자세한 내용은 [쿼리 제한](https://docs.microsoft.com/azure/kusto/concepts/querylimits) 을 참조 하세요.

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

Azure Data Explorer로 데이터를 복사하려면 복사 작업 원본의 형식 속성을 **AzureDataExplorerSink**로 설정합니다. 복사 작업 **sink** 섹션에서 다음 속성이 지원됩니다.

| 속성 | 설명 | 필수 |
|:--- |:--- |:--- |
| type | 복사 작업 싱크의 **형식** 속성을 다음으로 설정해야 합니다. **AzureDataExplorerSink** | 예 |
| ingestionMappingName | Kusto 테이블에서 미리 생성 된 **[매핑의](/azure/kusto/management/mappings#csv-mapping)** 이름입니다. 원본에서 Azure 데이터 탐색기로 열을 매핑하려면 (CSV/JSON/Avro 형식을 포함 하 여 **[지원 되는 모든 원본 저장소/형식](copy-activity-overview.md#supported-data-stores-and-formats)** 등) 복사 작업 [열 매핑](copy-activity-schema-and-type-mapping.md) (이름으로 암시적으로 또는 명시적으로 구성 된 경우)을 사용할 수 있습니다. Azure 데이터 탐색기 매핑을/또는 합니다. | 아니요 |

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
                "ingestionMappingName": "<optional Azure Data Explorer mapping name>"
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

## <a name="lookup-activity-properties"></a>조회 작업 속성

속성에 대 한 자세한 내용을 보려면 [조회 작업](control-flow-lookup-activity.md)을 확인 하세요.

## <a name="next-steps"></a>다음 단계

* Azure Data Factory에서 복사 작업의 원본 및 싱크로 지원되는 데이터 저장소 목록은 [지원되는 데이터 저장소](copy-activity-overview.md#supported-data-stores-and-formats)를 참조하세요.

* [Azure Data Factory에서 Azure 데이터 탐색기로 데이터를 복사 하](/azure/data-explorer/data-factory-load-data)는 방법에 대해 자세히 알아보세요.