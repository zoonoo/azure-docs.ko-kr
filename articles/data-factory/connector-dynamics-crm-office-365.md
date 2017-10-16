---
title: "Azure Data Factory를 사용하여 Dynamics CRM 및 365에서 데이터 복사 | Microsoft Docs"
description: "Azure Data Factory 파이프라인의 복사 작업을 사용하여 Dynamics CRM 및 365에서 지원되는 싱크 데이터 저장소로 데이터를 복사하는 방법에 대해 알아봅니다."
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/10/2017
ms.author: jingwang
ms.openlocfilehash: 74a4fca2ff5a0f913b5cb195f42482339e6200cb
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/11/2017
---
# <a name="copy-data-from-dynamics-365dynamics-crm-using-azure-data-factory"></a>Azure Data Factory를 사용하여 Dynamics 365/Dynamics CRM에서 데이터 복사
이 문서에서는 Azure Data Factory의 복사 작업을 사용하여 Dynamics 365/Dynamics CRM에서 데이터를 복사하는 방법을 설명합니다. 이 문서는 복사 작업에 대한 일반적인 개요를 제공하는 [복사 작업 개요](copy-activity-overview.md) 문서를 기반으로 합니다.

## <a name="supported-scenarios"></a>지원되는 시나리오

Dynamics 365/Dynamics CRM에서 지원되는 모든 싱크 데이터 저장소로 데이터를 복사할 수 있습니다. 복사 작업의 원본/싱크로 지원되는 데이터 저장소 목록은 [지원되는 데이터 저장소](copy-activity-overview.md#supported-data-stores-and-formats) 테이블을 참조하세요.

특히, 이 Dynamics 커넥터는 아래 Dynamics 버전 및 인증 유형을 지원합니다.

| Dynamics 버전 | 인증 형식 | 연결된 서비스 샘플 |
|:--- |:--- |:--- |
| Dynamics 365 온라인 <br> Dynamics CRM 온라인 | Office365 | [Dynamics 온라인 + Office365 인증](#dynamics-365-and-dynamics-crm-online) |
| IFD로 Dynamics 365 온-프레미스 <br> IFD로 Dynamics CRM 2016 온-프레미스 <br> IFD로 Dynamics CRM 2015 온-프레미스 | IFD | [IFD + IFD 인증으로 Dynamics 온-프레미스](#dynamics-365-and-dynamics-crm-on-premises-with-ifd) |

*IFD는 인터넷 연결 배포에 대해 짧습니다.*

> [!NOTE]
> Dynamics 커넥터를 사용하려면 Azure Key Vault에 암호를 저장하고 ADF 복사 작업에서 데이터 복사를 수행할 때 거기에서 끌어올 수 있도록 합니다. [연결된 서비스 속성](#linked-service-properties) 섹션에서 구성하는 방법을 참조하세요.

## <a name="getting-started"></a>시작
.NET SDK, Python SDK, Azure PowerShell, REST API 또는 Azure Resource Manager 템플릿을 사용하여 복사 작업으로 파이프라인을 만들 수 있습니다. 복사 작업을 사용하여 파이프라인을 만드는 단계별 지침은 [복사 작업 자습서](quickstart-create-data-factory-dot-net.md)를 참조하세요.

다음 섹션에서는 Dynamics에 한정된 Data Factory 엔터티를 정의하는 데 사용되는 속성에 대해 자세히 설명합니다.

## <a name="linked-service-properties"></a>연결된 서비스 속성

Dynamics 연결된 서비스에 다음 속성이 지원됩니다.

### <a name="dynamics-365-and-dynamics-crm-online"></a>Dynamics 365 및 Dynamics CRM Online

| 속성 | 설명 | 필수 |
|:--- |:--- |:--- |
| type | 형식 속성은 **Dynamics**로 설정해야 합니다. | 예 |
| deploymentType | Dynamics 인스턴스의 배포 유형입니다. Dynamics Online에 대해 **"Online"**이어야 합니다. | 예 |
| organizationName | Dynamics 인스턴스의 조직 이름입니다. | 아니요. 사용자와 연결된 둘 이상의 Dynamics 인스턴스가 있을 경우 지정해야 합니다. |
| authenticationType | Dynamics 서버에 연결하기 위한 인증 유형입니다. Dynamics Online에 대해 **"Office 365"**를 지정합니다. | 예 |
| username | Dynamics에 연결할 사용자 이름을 지정합니다. | 예 |
| 암호 | 사용자 이름에 지정한 사용자 계정의 암호를 지정합니다. Azure Key Vault에 암호를 입력하고 "AzureKeyVaultSecret"으로 암호를 구성해야 합니다. 자세한 내용은 [Key Vault에 자격 증명 저장](store-credentials-in-key-vault.md) 문서를 참조하세요. | 예 |

**예제: Office365 인증을 사용하여 Dynamics 온라인**

```json
{
    "name": "DynamicsLinkedService",
    "properties": {
        "type": "Dynamics",
        "description": "Dynamics online linked service using Office365 authentication",
        "typeProperties": {
            "deploymentType": "Online",
            "organizationName": "orga02d9c75",
            "authenticationType": "Office365",
            "username": "test@contoso.onmicrosoft.com",
            "password": {
                "type": "AzureKeyVaultSecret",
                "secretName": "mySecret",
                "store":{
                    "linkedServiceName": "<Azure Key Vault linked service>",
                    "type": "LinkedServiceReference"
                }
            }
        }
    }
}
```

### <a name="dynamics-365-and-dynamics-crm-on-premises-with-ifd"></a>IFD로 Dynamics 365 및 Dynamics CRM 온-프레미스

*Dyanmics 온라인과 비교하는 추가 속성은 "hostName" 및 "port"입니다.*

| 속성 | 설명 | 필수 |
|:--- |:--- |:--- |
| type | 형식 속성은 **Dynamics**로 설정해야 합니다. | 예 |
| deploymentType | Dynamics 인스턴스의 배포 유형입니다. IFD를 사용하는 Dynamics 온-프레미스에 대해 **"OnPremisesWithIfd"**여야 합니다.| 예 |
| **hostName** | 온-프레미스 Dynamics 서버의 호스트 이름입니다. | 예 |
| **port** | 온-프레미스 Dynamics 서버의 포트입니다. | 아니요(기본값: 443) |
| organizationName | Dynamics 인스턴스의 조직 이름입니다. | 예 |
| authenticationType | Dynamics 서버에 연결하기 위한 인증 유형입니다. IFD를 사용하는 Dynamics 온-프레미스에 대해 **"Ifd"**를 지정합니다. | 예 |
| username | Dynamics에 연결할 사용자 이름을 지정합니다. | 예 |
| 암호 | 사용자 이름에 지정한 사용자 계정의 암호를 지정합니다. Azure Key Vault에 암호를 입력하고 "AzureKeyVaultSecret"으로 암호를 구성해야 합니다. 자세한 내용은 [Key Vault에 자격 증명 저장](store-credentials-in-key-vault.md) 문서를 참조하세요. | 예 |

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
                "type": "AzureKeyVaultSecret",
                "secretName": "mySecret",
                "store":{
                    "linkedServiceName": "<Azure Key Vault linked service>",
                    "type": "LinkedServiceReference"
                }
            }
        }
    }
}
```

## <a name="dataset-properties"></a>데이터 집합 속성

데이터 집합 정의에 사용할 수 있는 섹션 및 속성의 전체 목록은 [데이터 집합](concepts-datasets-linked-services.md) 문서를 참조하세요. 이 섹션에서는 Dynamics 데이터 집합에서 지원하는 속성의 목록을 제공합니다.

Dynamics에서 데이터를 복사하려면 데이터 집합의 type 속성을 **DynamicsEntity**로 설정합니다. 다음과 같은 속성이 지원됩니다.

| 속성 | 설명 | 필수 |
|:--- |:--- |:--- |
| type | 데이터 집합의 type 속성을 **DynamicsEntity**로 설정해야 합니다. |예 |
| entityName | 검색할 엔터티의의 논리적 이름입니다. | 아니요(작업 원본에서 "query"가 지정된 경우) |

> [!IMPORTANT]
> 복사하려는 Dynamics 데이터에 대한 열 이름과 데이터 형식을 정의하는 **데이터 집합의 "structure" 섹션이 Dynamics에 필요합니다**. [데이터 집합 구조](concepts-datasets-linked-services.md#dataset-structure) 및 [Dynamics에 대한 데이터 형식 매핑](#data-type-mapping-for-dynamics)에서 자세히 알아봅니다.

**예제:**

```json
{
    "name": "DynamicsDataset",
    "properties": {
        "type": "DynamicsEntity",
        "structure": [
            {
                "name": "accountid",
                "type": "Guid"
            },
            {
                "name": "name",
                "type": "String"
            },
            {
                "name": "marketingonly",
                "type": "Boolean"
            },
            {
                "name": "modifiedon",
                "type": "Datetime"
            }
        ],
        "typePoperties": {
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

작업 정의에 사용할 수 있는 섹션 및 속성의 전체 목록은 [파이프라인](concepts-pipelines-activities.md) 문서를 참조하세요. 이 섹션에서는 Dynamics 원본에서 지원하는 속성의 목록을 제공합니다.

### <a name="dynamics-as-source"></a>원본으로 Dynamics

Dynamics에서 데이터를 복사하려면 복사 작업의 원본 형식을 **DynamicsSource**로 설정합니다. 복사 작업 **source** 섹션에서 다음 속성이 지원됩니다.

| 속성 | 설명 | 필수 |
|:--- |:--- |:--- |
| type | 형식 속성은 **DynamicsSource**로 설정해야 합니다.  | 예 |
| 쿼리  | FetchXML은 Microsoft Dynamics에 사용되는 전용 쿼리 언어(온라인 및 온-프레미스)입니다. 아래 예제를 참조하고 [FeachXML로 쿼리 작성](https://msdn.microsoft.com/en-us/library/gg328332.aspx)에서 자세히 알아봅니다. | 아니요(데이터 집합의 "entityName"이 지정된 경우)  |

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

## <a name="data-type-mapping-for-dynamics"></a>Dynamics에 대한 데이터 형식 매핑

Dynamics에서 데이터를 복사하는 경우 Dynamics 데이터 형식에서 Azure Data Factory 중간 데이터 형식으로 다음 매핑이 사용됩니다. 복사 작업에서 원본 스키마 및 데이터 형식을 싱크에 매핑하는 방법에 대한 자세한 내용은 [스키마 및 데이터 형식 매핑](copy-activity-schema-and-type-mapping.md)을 참조하세요.

아래 매핑 테이블을 사용하여 원본 Dynamics 데이터 형식에 따라 데이터 집합 구조에 해당 ADF 데이터 형식을 구성합니다.

| Dynamics 데이터 형식 | 데이터 팩터리 중간 데이터 형식 |
|:--- |:--- |
| AttributeTypeCode.BigInt | long |
| AttributeTypeCode.Boolean | Boolean |
| AttributeType.Customer | Guid |
| AttributeType.DateTime | Datetime |
| AttributeType.Decimal | 10진수 |
| AttributeType.Double | Double |
| AttributeType.EntityName | 문자열 |
| AttributeType.Integer | Int32 |
| AttributeType.Lookup | Guid |
| AttributeType.ManagedProperty | Boolean |
| AttributeType.Memo | 문자열 |
| AttributeType.Money | 10진수 |
| AttributeType.Owner | Guid |
| AttributeType.Picklist | Int32 |
| AttributeType.Uniqueidentifier | Guid |
| AttributeType.String | 문자열 |
| AttributeType.State | Int32 |
| AttributeType.Status | Int32 |

> [!NOTE]
> Dynamics 데이터 형식 AttributeType.CalendarRules 및 AttributeType.PartyList는 지원되지 않습니다.


## <a name="next-steps"></a>다음 단계
Azure Data Factory에서 복사 작업의 원본 및 싱크로 지원되는 데이터 저장소 목록은 [지원되는 데이터 저장소](copy-activity-overview.md##supported-data-stores-and-formats)를 참조하세요.