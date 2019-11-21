---
title: Copy data in Dynamics (Common Data Service)
description: Azure Data Factory 파이프라인의 복사 작업을 사용하여 Microsoft Dynamics CRM 또는 Microsoft Dynamics 365(Common Data Service)에서 지원되는 싱크 데이터 저장소로, 혹은 지원되는 원본 데이터 저장소에서 Dynamics CRM 또는 Dynamics 365로 데이터를 복사하는 방법에 대해 알아봅니다.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.author: jingwang
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.custom: seo-lt-2019
ms.date: 10/25/2019
ms.openlocfilehash: b2eb6f877daf2fddaa5a61a958254cc8222ac6db
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/20/2019
ms.locfileid: "74218596"
---
# <a name="copy-data-from-and-to-dynamics-365-common-data-service-or-dynamics-crm-by-using-azure-data-factory"></a>Azure Data Factory를 사용하여 Dynamics 365(Common Data Service) 또는 Dynamics CRM 간에 데이터 복사

이 문서에서는 Azure Data Factory의 복사 작업을 사용하여 Microsoft Dynamics 365 또는 Microsoft Dynamics CRM 간에 데이터를 복사하는 방법을 설명합니다. 이 문서는 복사 활동에 대한 일반적인 개요를 제공하는 [복사 활동 개요](copy-activity-overview.md) 문서를 기반으로 합니다.

## <a name="supported-capabilities"></a>지원되는 기능

This connector is supported for the following activities:

- [Copy activity](copy-activity-overview.md) with [supported source/sink matrix](copy-activity-overview.md)
- [조회 작업](control-flow-lookup-activity.md)

Dynamics 365(Common Data Service) 또는 Dynamics CRM에서 지원되는 모든 싱크 데이터 저장소로 데이터를 복사할 수 있습니다. 지원되는 모든 원본 데이터 저장소의 데이터를 Dynamics 365(Common Data Service) 또는 Dynamics CRM에 복사할 수도 있습니다. 복사 작업의 원본 또는 싱크로 지원되는 데이터 저장소 목록은 [지원되는 데이터 저장소](copy-activity-overview.md#supported-data-stores-and-formats) 테이블을 참조하세요.

This Dynamics connector supports Dynamics version 7.x to 9.x for both online or on-premises. 더 구체적으로 살펴보면 다음과 같습니다.

- Version 7.x maps to Dynamics CRM 2015
- Version 8.x maps to Dynamics CRM 2016 and the early version of Dynamics 365
- Version 9.x maps to the later version of Dynamics 365

Refer to the following table on the supported authentication types and configurations for respective Dynamics versions/products. IFD는 Internet-facing Deployment(인터넷 연결 배포)의 약어입니다.

| Dynamics 버전 | 인증 형식 | 연결된 서비스 샘플 |
|:--- |:--- |:--- |
| Dynamics 365 온라인 <br> Dynamics CRM Online | Office365 | [Dynamics Online + Office365 인증](#dynamics-365-and-dynamics-crm-online) |
| IFD로 Dynamics 365 온-프레미스 <br> IFD로 Dynamics CRM 2016 온-프레미스 <br> IFD로 Dynamics CRM 2015 온-프레미스 | IFD | [IFD + IFD 인증으로 Dynamics 온-프레미스](#dynamics-365-and-dynamics-crm-on-premises-with-ifd) |

구체적으로 Dynamics 365에 대해 다음 애플리케이션 유형이 지원됩니다.

- Dynamics 365 for Sales
- Dynamics 365 for Customer Service
- Dynamics 365 for Field Service
- Dynamics 365 for Project Service Automation
- Dynamics 365 for Marketing

다른 애플리케이션 유형(예: Finance and Operations, Talent 등)은 이 커넥터에서 지원되지 않습니다.

This Dynamics connector is built on top of [Dynamics XRM tooling](https://docs.microsoft.com/dynamics365/customer-engagement/developer/build-windows-client-applications-xrm-tools).

>[!TIP]
>**Dynamics 365 Finance and Operations**에서 데이터를 복사하려면 [Dynamics AX 커넥터](connector-dynamics-ax.md)를 사용할 수 있습니다.

## <a name="get-started"></a>시작하기

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

다음 섹션에서는 Dynamics에 한정된 Data Factory 엔터티를 정의하는 데 사용되는 속성에 대해 자세히 설명합니다.

## <a name="linked-service-properties"></a>연결된 서비스 속성

Dynamics 연결 서비스에 다음 속성이 지원됩니다.

### <a name="dynamics-365-and-dynamics-crm-online"></a>Dynamics 365 및 Dynamics CRM Online

| 자산 | 설명 | 필수 |
|:--- |:--- |:--- |
| type | The type property must be set to **Dynamics**, **DynamicsCrm**, or **CommonDataServiceForApps**. | yes |
| deploymentType | Dynamics 인스턴스의 배포 유형입니다. Dynamics Online에 대해 **"Online"** 이어야 합니다. | yes |
| serviceUri | Dynamics 인스턴스의 서비스 URL(예: `https://adfdynamics.crm.dynamics.com` ). | yes |
| authenticationType | Dynamics 서버에 연결하기 위한 인증 유형입니다. Dynamics Online에 대해 **"Office365"** 를 지정합니다. | yes |
| username | Dynamics에 연결할 사용자 이름을 지정합니다. | yes |
| 암호 | username에 지정한 사용자 계정의 암호를 지정합니다. 이 필드를 SecureString으로 표시하여 Data Factory에 안전하게 저장하거나 [Azure Key Vault에 저장되는 비밀을 참조](store-credentials-in-key-vault.md)합니다. | yes |
| connectVia | 데이터 저장소에 연결하는 데 사용할 [통합 런타임](concepts-integration-runtime.md)입니다. 지정하지 않으면 기본 Azure Integration Runtime을 사용합니다. | 원본에 연결된 서비스에 통합 런타임이 없는 경우 원본은 아니요, 싱크는 예입니다. |

>[!NOTE]
>Dynamics CRM/365 Online 인스턴스를 식별하는 선택적 "organizationName" 속성을 사용하기 위해 사용되는 Dynamics 커넥터. 계속 작동하겠지만, 인스턴스 검색 성능을 향상하려면 그 대신 새 "serviceUri" 속성을 지정하는 것이 좋습니다.

**예제: Office365 인증을 사용하여 Dynamics 온라인**

```json
{
    "name": "DynamicsLinkedService",
    "properties": {
        "type": "Dynamics",
        "description": "Dynamics online linked service using Office365 authentication",
        "typeProperties": {
            "deploymentType": "Online",
            "serviceUri": "https://adfdynamics.crm.dynamics.com",
            "authenticationType": "Office365",
            "username": "test@contoso.onmicrosoft.com",
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

### <a name="dynamics-365-and-dynamics-crm-on-premises-with-ifd"></a>IFD로 Dynamics 365 및 Dynamics CRM 온-프레미스

*Dyanmics Online에 비교되는 추가 속성은 "hostName" 및 "port"입니다.*

| 자산 | 설명 | 필수 |
|:--- |:--- |:--- |
| type | The type property must be set to **Dynamics**, **DynamicsCrm**, or **CommonDataServiceForApps**. | yes |
| deploymentType | Dynamics 인스턴스의 배포 유형입니다. IFD를 사용하는 Dynamics 온-프레미스에 대해 **"OnPremisesWithIfd"** 여야 합니다.| yes |
| hostName | 온-프레미스 Dynamics 서버의 호스트 이름입니다. | yes |
| 포트 | 온-프레미스 Dynamics 서버의 포트입니다. | 아니요(기본값: 443) |
| organizationName | Dynamics 인스턴스의 조직 이름입니다. | yes |
| authenticationType | Dynamics 서버에 연결하기 위한 인증 유형입니다. IFD를 사용하는 Dynamics 온-프레미스에 대해 **"Ifd"** 를 지정합니다. | yes |
| username | Dynamics에 연결할 사용자 이름을 지정합니다. | yes |
| 암호 | username에 지정한 사용자 계정의 암호를 지정합니다. 이 필드를 SecureString으로 표시하여 ADF에 안전하게 저장하도록 선택하거나, Azure Key Vault에 암호를 저장하고 복사 작업이 데이터 복사를 수행할 때 거기에서 끌어오도록 할 수 있습니다. [Key Vault에 자격 증명 저장](store-credentials-in-key-vault.md)에서 자세히 알아보세요. | yes |
| connectVia | 데이터 저장소에 연결하는 데 사용할 [통합 런타임](concepts-integration-runtime.md)입니다. 지정하지 않으면 기본 Azure Integration Runtime을 사용합니다. | 원본에는 아니요이고 싱크에는 예입니다 |

**예제: IFD 인증을 사용하여 IFD로 Dynamics 온-프레미스**

```json
{
    "name": "DynamicsLinkedService",
    "properties": {
        "type": "Dynamics",
        "description": "Dynamics on-premises with IFD linked service using IFD authentication",
        "typeProperties": {
            "deploymentType": "OnPremisesWithIFD",
            "hostName": "contosodynamicsserver.contoso.com",
            "port": 443,
            "organizationName": "admsDynamicsTest",
            "authenticationType": "Ifd",
            "username": "test@contoso.onmicrosoft.com",
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

데이터 세트 정의에 사용할 수 있는 섹션 및 속성의 전체 목록은 [데이터 세트](concepts-datasets-linked-services.md) 문서를 참조하세요. 이 섹션에서는 Dynamics 데이터 세트에서 지원하는 속성의 목록을 제공합니다.

To copy data from and to Dynamics, the following properties are supported.

| 자산 | 설명 | 필수 |
|:--- |:--- |:--- |
| type | The type property of the dataset must be set to **DynamicsEntity**, **DynamicsCrmEntity**, or **CommonDataServiceForAppsEntity**. |yes |
| entityName | 검색할 엔터티의의 논리적 이름입니다. | 원본에는 아니요(작업 원본에서 "query"가 지정된 경우)이고 싱크에는 예입니다. |

**예제:**

```json
{
    "name": "DynamicsDataset",
    "properties": {
        "type": "DynamicsEntity",
        "schema": [],
        "typeProperties": {
            "entityName": "account"
        },
        "linkedServiceName": {
            "referenceName": "<Dynamics linked service name>",
            "type": "linkedservicereference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>복사 작업 속성

작업 정의에 사용할 수 있는 섹션 및 속성의 전체 목록은 [파이프라인](concepts-pipelines-activities.md) 문서를 참조하세요. 이 섹션에서는 Dynamics 원본 및 싱크 형식에서 지원하는 속성의 목록을 제공합니다.

### <a name="dynamics-as-a-source-type"></a>원본 유형인 Dynamics

To copy data from Dynamics, the following properties are supported in the copy activity **source** section.

| 자산 | 설명 | 필수 |
|:--- |:--- |:--- |
| type | The type property of the copy activity source must be set to **DynamicsSource**, **DynamicsCrmSource**, or **CommonDataServiceForAppsSource**. | yes |
| 쿼리 | FetchXML은 Dynamics에 사용되는 전용 쿼리 언어(온라인 및 온-프레미스)입니다. 다음 예제를 참조하세요. To learn more, see [Build queries with FetchXML](https://msdn.microsoft.com/library/gg328332.aspx). | 아니요(데이터 세트의 "entityName"이 지정된 경우) |

>[!NOTE]
>PK 열은 FetchXML 쿼리에서 구성한 열 프로젝트에 포함되지 않은 경우에도 항상 복사됩니다.

> [!IMPORTANT]
>- When you copy data from Dynamics, explicit column mapping from Dynamics to sink is optional but highly recommanded to ensure a deterministic copy result.
>- When importing schema in authoring UI, ADF infers the schema by sampling the top rows from the Dynamics query result to initialize the source column list, in which case columns with no values in top rows will be omitted. The same behavior applies to copy executions if there is no explicit mapping. You can review and add more columns into the mapping, which will be honored during copy runtime.

**예제:**

```json
"activities":[
    {
        "name": "CopyFromDynamics",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Dynamics input dataset>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "DynamicsSource",
                "query": "<FetchXML Query>"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="sample-fetchxml-query"></a>샘플 FetchXML 쿼리

```xml
<fetch>
  <entity name="account">
    <attribute name="accountid" />
    <attribute name="name" />
    <attribute name="marketingonly" />
    <attribute name="modifiedon" />
    <order attribute="modifiedon" descending="false" />
    <filter type="and">
      <condition attribute ="modifiedon" operator="between">
        <value>2017-03-10 18:40:00z</value>
        <value>2017-03-12 20:40:00z</value>
      </condition>
    </filter>
  </entity>
</fetch>
```

### <a name="dynamics-as-a-sink-type"></a>싱크 형식인 Dynamics

To copy data to Dynamics, the following properties are supported in the copy activity **sink** section.

| 자산 | 설명 | 필수 |
|:--- |:--- |:--- |
| type | The type property of the copy activity sink must be set to **DynamicsSink**, **DynamicsCrmSink**, or **CommonDataServiceForAppsSink**. | yes |
| writeBehavior | 작업의 쓰기 동작입니다.<br/>허용되는 값은 **"Upsert"** 입니다. | yes |
| alternateKeyName | Specify the alternate key name defined on your entity to perform "Upsert". | 아닙니다. |
| writeBatchSize | 각 일괄 처리에서 Dynamics에 작성된 데이터의 행 수입니다. | 아니요(기본값: 10) |
| ignoreNullValues | 쓰기 작업 중에 (키 필드를 제외한) 입력 데이터에서 null 값을 무시할지를 나타냅니다.<br/>허용되는 값은 **true** 및 **false**입니다.<br>- **True**: Upsert/업데이트 작업을 수행할 때 대상 개체의 데이터를 변경하지 않고 유지합니다. 삽입 작업을 수행할 때 정의된 기본 값을 삽입합니다.<br/>- **False**: Upsert/업데이트 작업을 수행할 때 대상 개체의 데이터를 NULL로 업데이트합니다. 삽입 작업을 수행할 때 NULL 값을 삽입합니다. | 아니요(기본값: false) |

>[!NOTE]
>Dynamics 싱크에 대해 싱크 "**writeBatchSize**" 및 복사 작업 " **[parallelCopies](copy-activity-performance.md#parallel-copy)** "의 기본값은 모두 10입니다. 따라서 100개 레코드가 Dynamics에 동시에 제출됩니다.

Dynamics 365 Online의 경우 [조직당 동시 일괄 처리 호출 2개](https://msdn.microsoft.com/library/jj863631.aspx#Run-time%20limitations)라는 제한이 있습니다. 이 제한을 초과하면 첫 번째 요청이 실행되기도 전에 "서버 작업 중" 오류가 throw됩니다. "writeBatchSize"를 10 이하로 유지하면 이러한 동시 호출 수 제한을 피할 수 있습니다.

The optimal combination of "**writeBatchSize**" and "**parallelCopies**" depends on the schema of your entity e.g. number of columns, row size, number of plugins/workflows/workflow activities hooked up to those calls, etc. The default setting of 10 writeBatchSize * 10 parallelCopies is the recommendation according to Dynamics service, which would work for most Dynamics entities though may not be best performance. 복사 작업 설정의 조합을 조정하여 성능을 튜닝할 수 있습니다.

**예제:**

```json
"activities":[
    {
        "name": "CopyToDynamics",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Dynamics output dataset>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "DynamicsSink",
                "writeBehavior": "Upsert",
                "writeBatchSize": 10,
                "ignoreNullValues": true
            }
        }
    }
]
```

## <a name="data-type-mapping-for-dynamics"></a>Dynamics에 대한 데이터 형식 매핑

Dynamics에서 데이터를 복사하는 경우 Dynamics 데이터 형식에서 데이터 팩터리 중간 데이터 형식으로 다음 매핑이 사용됩니다. 복사 활동에서 원본 스키마와 데이터 형식을 싱크에 매핑하는 방법에 대한 자세한 내용은 [스키마 및 데이터 형식 매핑](copy-activity-schema-and-type-mapping.md)을 참조하세요.

다음 매핑 테이블을 사용하여 원본 Dynamics 데이터 형식에 따라 데이터 세트 구조에 해당 데이터 팩터리 데이터 형식을 구성합니다.

| Dynamics 데이터 형식 | Data Factory 중간 데이터 형식 | 원본으로 지원됨 | 싱크로 지원됨 |
|:--- |:--- |:--- |:--- |
| AttributeTypeCode.BigInt | 장기 | ✓ | ✓ |
| AttributeTypeCode.Boolean | Boolean | ✓ | ✓ |
| AttributeType.Customer | GUID | ✓ | |
| AttributeType.DateTime | DateTime | ✓ | ✓ |
| AttributeType.Decimal | 10진수 | ✓ | ✓ |
| AttributeType.Double | DOUBLE | ✓ | ✓ |
| AttributeType.EntityName | string | ✓ | ✓ |
| AttributeType.Integer | Int32 | ✓ | ✓ |
| AttributeType.Lookup | GUID | ✓ | ✓(단일 대상이 연결됨) |
| AttributeType.ManagedProperty | Boolean | ✓ | |
| AttributeType.Memo | string | ✓ | ✓ |
| AttributeType.Money | 10진수 | ✓ | ✓ |
| AttributeType.Owner | GUID | ✓ | |
| AttributeType.Picklist | Int32 | ✓ | ✓ |
| AttributeType.Uniqueidentifier | GUID | ✓ | ✓ |
| AttributeType.String | string | ✓ | ✓ |
| AttributeType.State | Int32 | ✓ | ✓ |
| AttributeType.Status | Int32 | ✓ | ✓ |

> [!NOTE]
> The Dynamics data types AttributeType.CalendarRules, AttributeType.MultiSelectPicklist and AttributeType.PartyList aren't supported.

## <a name="lookup-activity-properties"></a>Lookup activity properties

To learn details about the properties, check [Lookup activity](control-flow-lookup-activity.md).

## <a name="next-steps"></a>다음 단계
Data Factory에서 복사 활동을 통해 원본 및 싱크로 지원되는 데이터 저장소의 목록은 [지원되는 데이터 저장소](copy-activity-overview.md#supported-data-stores-and-formats)를 참조하세요.
