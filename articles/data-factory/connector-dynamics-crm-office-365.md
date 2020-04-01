---
title: 역학에서 데이터 복사(공통 데이터 서비스)
description: Azure Data Factory 파이프라인의 복사 작업을 사용하여 Microsoft Dynamics CRM 또는 Microsoft Dynamics 365(Common Data Service)에서 지원되는 싱크 데이터 저장소로, 혹은 지원되는 원본 데이터 저장소에서 Dynamics CRM 또는 Dynamics 365로 데이터를 복사하는 방법에 대해 알아봅니다.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.custom: seo-lt-2019
ms.date: 11/20/2019
ms.openlocfilehash: 9bbd2e3376f1da3fdf5b10d654a331ce258be5cf
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/31/2020
ms.locfileid: "80422098"
---
# <a name="copy-data-from-and-to-dynamics-365-common-data-service-or-dynamics-crm-by-using-azure-data-factory"></a>Azure Data Factory를 사용하여 Dynamics 365(Common Data Service) 또는 Dynamics CRM 간에 데이터 복사

이 문서에서는 Azure Data Factory의 복사 작업을 사용하여 Microsoft Dynamics 365 또는 Microsoft Dynamics CRM 간에 데이터를 복사하는 방법을 설명합니다. 복사 활동의 일반적인 개요를 제공하는 [복사 활동 개요](copy-activity-overview.md) 문서를 기반으로 합니다.

## <a name="supported-capabilities"></a>지원되는 기능

이 커넥터는 다음 활동에 대해 지원됩니다.

- [지원되는 소스/싱크 매트릭스로](copy-activity-overview.md) [활동 복사](copy-activity-overview.md)
- [조회 활동](control-flow-lookup-activity.md)

Dynamics 365(Common Data Service) 또는 Dynamics CRM에서 지원되는 모든 싱크 데이터 저장소로 데이터를 복사할 수 있습니다. 지원되는 모든 원본 데이터 저장소의 데이터를 Dynamics 365(Common Data Service) 또는 Dynamics CRM에 복사할 수도 있습니다. 복사 작업의 원본 또는 싱크로 지원되는 데이터 저장소 목록은 [지원되는 데이터 저장소](copy-activity-overview.md#supported-data-stores-and-formats) 테이블을 참조하세요.

이 역학 커넥터는 온라인 또는 온-프레미스 모두에서 다이나믹 버전 7.x ~ 9.x를 지원합니다. 더 구체적으로 살펴보면 다음과 같습니다.

- 버전 7.x 역학 CRM 2015에지도
- 버전 8.x 는 역학 CRM 2016 및 동적 365의 초기 버전에 매핑
- 버전 9.x는 Dynamics 365의 이후 버전에 매핑됩니다.

각 역학 버전/제품에 대해 지원되는 인증 유형 및 구성에 대한 다음 표를 참조하십시오. IFD는 Internet-facing Deployment(인터넷 연결 배포)의 약어입니다.

| Dynamics 버전 | 인증 형식 | 연결된 서비스 샘플 |
|:--- |:--- |:--- |
| Common Data Service <br> Dynamics 365 온라인 <br> Dynamics CRM Online | AAD 서비스 주체 <br> Office365 | [역학 온라인 + AAD 서비스 주체 또는 Office365 인증](#dynamics-365-and-dynamics-crm-online) |
| IFD로 Dynamics 365 온-프레미스 <br> IFD로 Dynamics CRM 2016 온-프레미스 <br> IFD로 Dynamics CRM 2015 온-프레미스 | IFD | [IFD + IFD 인증으로 Dynamics 온-프레미스](#dynamics-365-and-dynamics-crm-on-premises-with-ifd) |

구체적으로 Dynamics 365에 대해 다음 애플리케이션 유형이 지원됩니다.

- Dynamics 365 for Sales
- Dynamics 365 for Customer Service
- Dynamics 365 for Field Service
- Dynamics 365 for Project Service Automation
- Dynamics 365 for Marketing

다른 애플리케이션 유형(예: Finance and Operations, Talent 등)은 이 커넥터에서 지원되지 않습니다.

이 역학 커넥터는 [Dynamics XRM 툴링](https://docs.microsoft.com/dynamics365/customer-engagement/developer/build-windows-client-applications-xrm-tools)위에 내장되어 있습니다.

>[!TIP]
>**Dynamics 365 Finance and Operations**에서 데이터를 복사하려면 [Dynamics AX 커넥터](connector-dynamics-ax.md)를 사용할 수 있습니다.

## <a name="get-started"></a>시작하기

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

다음 섹션에서는 Dynamics에 한정된 Data Factory 엔터티를 정의하는 데 사용되는 속성에 대해 자세히 설명합니다.

## <a name="linked-service-properties"></a>연결된 서비스 속성

Dynamics 연결 서비스에 다음 속성이 지원됩니다.

### <a name="dynamics-365-and-dynamics-crm-online"></a>Dynamics 365 및 Dynamics CRM Online

| 속성 | Description | 필수 |
|:--- |:--- |:--- |
| type | 형식 속성은 **Dynamics,** **DynamicsCrm**또는 **CommonDataServiceFor앱으로**설정해야 합니다. | 예 |
| deploymentType | Dynamics 인스턴스의 배포 유형입니다. Dynamics Online에 대해 **"Online"** 이어야 합니다. | 예 |
| serviceUri | Dynamics 인스턴스의 서비스 URL(예: `https://adfdynamics.crm.dynamics.com` ). | 예 |
| authenticationType | Dynamics 서버에 연결하기 위한 인증 유형입니다. 허용된 값은 **AADServicePrincipal** 또는 **"Office365"입니다.** | 예 |
| servicePrincipalId | Azure Active Directory 애플리케이션의 클라이언트 ID를 지정합니다. | 인증 사용 `AADServicePrincipal` 시 예 |
| 서비스 보안원칙 자격증명 유형 | 서비스 주체 인증에 사용할 자격 증명 유형을 지정합니다. 허용된 값은 **다음과** **ServicePrincipalCert**같습니다. | 인증 사용 `AADServicePrincipal` 시 예 |
| 서비스 보안원칙 증명 | 서비스 주체 자격 증명을 지정합니다. <br>자격 `ServicePrincipalKey` 증명 유형으로 `servicePrincipalCredential` 사용할 때 문자열(ADF는 연결된 서비스 배포 시 암호화) 또는 AKV의 비밀에 대한 참조일 수 있습니다. <br>자격 `ServicePrincipalCert` 증명으로 `servicePrincipalCredential` 사용할 때는 AKV의 인증서에 대한 참조여야 합니다. | 인증 사용 `AADServicePrincipal` 시 예 | 
| 사용자 이름 | Dynamics에 연결할 사용자 이름을 지정합니다. | 인증 사용 `Office365` 시 예 |
| password | username에 지정한 사용자 계정의 암호를 지정합니다. 이 필드를 SecureString으로 표시하여 Data Factory에 안전하게 저장하거나, [Azure Key Vault에 저장된 비밀을 참조](store-credentials-in-key-vault.md)합니다. | 인증 사용 `Office365` 시 예 |
| connectVia | 데이터 저장소에 연결하는 데 사용할 [통합 런타임](concepts-integration-runtime.md)입니다. 지정하지 않으면 기본 Azure Integration Runtime을 사용합니다. | 원본에 연결된 서비스에 통합 런타임이 없는 경우 원본은 아니요, 싱크는 예입니다. |

>[!NOTE]
>Dynamics CRM/365 Online 인스턴스를 식별하는 선택적 "organizationName" 속성을 사용하기 위해 사용되는 Dynamics 커넥터. 계속 작동하겠지만, 인스턴스 검색 성능을 향상하려면 그 대신 새 "serviceUri" 속성을 지정하는 것이 좋습니다.

**예: AAD 서비스 주체 + 키 인증을 사용하는 온라인 역학**

```json
{  
    "name": "DynamicsLinkedService",  
    "properties": {  
        "type": "Dynamics",  
        "typeProperties": {  
            "deploymentType": "Online",  
            "serviceUri": "https://adfdynamics.crm.dynamics.com",  
            "authenticationType": "AADServicePrincipal",  
            "servicePrincipalId": "<service principal id>",  
            "servicePrincipalCredentialType": "ServicePrincipalKey",  
            "servicePrincipalCredential": "<service principal key>"
        },  
        "connectVia": {  
            "referenceName": "<name of Integration Runtime>",  
            "type": "IntegrationRuntimeReference"  
        }  
    }  
}  
```
**예: AAD 서비스 주체 + 인증서 인증을 사용하는 온라인 역학**

```json
{ 
    "name": "DynamicsLinkedService", 
    "properties": { 
        "type": "Dynamics", 
        "typeProperties": { 
            "deploymentType": "Online", 
            "serviceUri": "https://adfdynamics.crm.dynamics.com", 
            "authenticationType": "AADServicePrincipal", 
            "servicePrincipalId": "<service principal id>", 
            "servicePrincipalCredentialType": "ServicePrincipalCert", 
            "servicePrincipalCredential": { 
                "type": "AzureKeyVaultSecret", 
                "store": { 
                    "referenceName": "<AKV reference>", 
                    "type": "LinkedServiceReference" 
                }, 
                "secretName": "<certificate name in AKV>" 
            } 
        }, 
        "connectVia": { 
            "referenceName": "<name of Integration Runtime>", 
            "type": "IntegrationRuntimeReference" 
        } 
    } 
} 
```

**예제: Office365 인증을 사용하여 Dynamics 온라인**

```json
{
    "name": "DynamicsLinkedService",
    "properties": {
        "type": "Dynamics",
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

| 속성 | Description | 필수 |
|:--- |:--- |:--- |
| type | 형식 속성은 **Dynamics,** **DynamicsCrm**또는 **CommonDataServiceFor앱으로**설정해야 합니다. | 예 |
| deploymentType | Dynamics 인스턴스의 배포 유형입니다. IFD를 사용하는 Dynamics 온-프레미스에 대해 **"OnPremisesWithIfd"** 여야 합니다.| 예 |
| hostName | 온-프레미스 Dynamics 서버의 호스트 이름입니다. | 예 |
| 포트 | 온-프레미스 Dynamics 서버의 포트입니다. | 아니요(기본값: 443) |
| organizationName | Dynamics 인스턴스의 조직 이름입니다. | 예 |
| authenticationType | Dynamics 서버에 연결하기 위한 인증 유형입니다. IFD를 사용하는 Dynamics 온-프레미스에 대해 **"Ifd"** 를 지정합니다. | 예 |
| 사용자 이름 | Dynamics에 연결할 사용자 이름을 지정합니다. | 예 |
| password | username에 지정한 사용자 계정의 암호를 지정합니다. 이 필드를 SecureString으로 표시하여 ADF에 안전하게 저장하도록 선택하거나, Azure Key Vault에 암호를 저장하고 복사 작업이 데이터 복사를 수행할 때 거기에서 끌어오도록 할 수 있습니다. [Key Vault에 자격 증명 저장](store-credentials-in-key-vault.md)에서 자세히 알아보세요. | 예 |
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

Dynamics에서 데이터를 복사하려면 다음 속성이 지원됩니다.

| 속성 | Description | 필수 |
|:--- |:--- |:--- |
| type | 데이터 집합의 형식 속성은 **DynamicsEntity,** **DynamicsCrmEntity**또는 **CommonDataServiceForApps 엔터티로**설정해야 합니다. |예 |
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

Dynamics에서 데이터를 복사하려면 복사 활동 **원본** 섹션에서 다음 속성이 지원됩니다.

| 속성 | Description | 필수 |
|:--- |:--- |:--- |
| type | 복사 활동 소스의 형식 속성은 **DynamicsSource,** **DynamicsCrmSource**또는 **CommonDataServiceForAppsSource로**설정해야 합니다. | 예 |
| Query | FetchXML은 Dynamics에 사용되는 전용 쿼리 언어(온라인 및 온-프레미스)입니다. 다음 예제를 참조하세요. 자세한 내용은 [FetchXML을 사용하여 쿼리 빌드를](https://msdn.microsoft.com/library/gg328332.aspx)참조하십시오. | 아니요(데이터 세트의 "entityName"이 지정된 경우) |

>[!NOTE]
>PK 열은 FetchXML 쿼리에서 구성한 열 프로젝트에 포함되지 않은 경우에도 항상 복사됩니다.

> [!IMPORTANT]
>- Dynamics에서 데이터를 복사할 때 Dynamics에서 싱크에 이르는 명시적 열 매핑은 선택 사항이지만 결정적인 복사 결과를 보장하기 위해 매우 많이 명령됩니다.
>- UI 작성시 스키마를 가져올 때 ADF는 Dynamics 쿼리 결과에서 상위 행을 샘플링하여 스키마를 유추하여 소스 열 목록을 초기화하며, 이 경우 최상위 행에 값이 없는 열은 생략됩니다. 명시적 매핑이 없는 경우 복사 실행에도 동일한 동작이 적용됩니다. 복사 런타임 중에 적용됩니다 매핑에 더 많은 열을 검토하고 추가할 수 있습니다.

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

데이터를 Dynamics에 복사하려면 복사 활동 **싱크** 섹션에서 다음 속성이 지원됩니다.

| 속성 | Description | 필수 |
|:--- |:--- |:--- |
| type | 복사 활동 싱크의 형식 속성은 **DynamicsSink,** **DynamicsCrmSink**또는 **CommonDataServiceForApps싱크로**설정해야 합니다. | 예 |
| writeBehavior | 작업의 쓰기 동작입니다.<br/>허용되는 값은 **"Upsert"** 입니다. | 예 |
| 대체키 이름 | "Upsert"를 수행하도록 엔터티에 정의된 대체 키 이름을 지정합니다. | 예 |
| writeBatchSize | 각 일괄 처리에서 Dynamics에 작성된 데이터의 행 수입니다. | 아니요(기본값: 10) |
| ignoreNullValues | 쓰기 작업 중에 (키 필드를 제외한) 입력 데이터에서 null 값을 무시할지를 나타냅니다.<br/>허용되는 값은 **true** 및 **false**입니다.<br>- **True**: Upsert/업데이트 작업을 수행할 때 대상 개체의 데이터를 변경하지 않고 유지합니다. 삽입 작업을 수행할 때 정의된 기본 값을 삽입합니다.<br/>- **False**: Upsert/업데이트 작업을 수행할 때 대상 개체의 데이터를 NULL로 업데이트합니다. 삽입 작업을 수행할 때 NULL 값을 삽입합니다. | 아니요(기본값: false) |

>[!NOTE]
>싱크의 기본값 **"writeBatchSize**" 및 복사 활동 "**[parallelCopys](copy-activity-performance-features.md#parallel-copy)**" 역학 싱크에 대 한 10입니다. 따라서 100개 레코드가 Dynamics에 동시에 제출됩니다.

Dynamics 365 Online의 경우 [조직당 동시 일괄 처리 호출 2개](https://msdn.microsoft.com/library/jj863631.aspx#Run-time%20limitations)라는 제한이 있습니다. 이 제한을 초과하면 첫 번째 요청이 실행되기도 전에 "서버 작업 중" 오류가 throw됩니다. "writeBatchSize"를 10 이하로 유지하면 이러한 동시 호출 수 제한을 피할 수 있습니다.

**"writeBatchSize**" 및 **"병렬 복사"** 의 최적 조합은 열 수, 행 크기, 해당 호출에 연결된 플러그인/워크플로/워크플로 활동 수 등과 같은 엔터티의 스키마에 따라 달라집니다. 기본 설정 10 writeBatchSize * 10 병렬복사는 대부분의 역학 엔터티에 대해 작동하지만 성능이 가장 좋지 않을 수 있는 Dynamics 서비스에 따라 권장 사항입니다. 복사 작업 설정의 조합을 조정하여 성능을 튜닝할 수 있습니다.

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
| AttributeTypeCode.BigInt | long | ✓ | ✓ |
| AttributeTypeCode.Boolean | 부울 | ✓ | ✓ |
| AttributeType.Customer | Guid | ✓ | |
| AttributeType.DateTime | DateTime | ✓ | ✓ |
| AttributeType.Decimal | Decimal | ✓ | ✓ |
| AttributeType.Double | Double | ✓ | ✓ |
| AttributeType.EntityName | String | ✓ | ✓ |
| AttributeType.Integer | Int32 | ✓ | ✓ |
| AttributeType.Lookup | Guid | ✓ | ✓(단일 대상이 연결됨) |
| AttributeType.ManagedProperty | 부울 | ✓ | |
| AttributeType.Memo | String | ✓ | ✓ |
| AttributeType.Money | Decimal | ✓ | ✓ |
| AttributeType.Owner | Guid | ✓ | |
| AttributeType.Picklist | Int32 | ✓ | ✓ |
| AttributeType.Uniqueidentifier | Guid | ✓ | ✓ |
| AttributeType.String | String | ✓ | ✓ |
| AttributeType.State | Int32 | ✓ | ✓ |
| AttributeType.Status | Int32 | ✓ | ✓ |

> [!NOTE]
> 역학 데이터 형식 특성 유형.CalendarRules, 특성 유형.MultiSelectPicklist 및 특성 유형.PartyList 지원 되지 않습니다.

## <a name="lookup-activity-properties"></a>조회 활동 속성

속성에 대한 자세한 내용을 보려면 [조회 활동을](control-flow-lookup-activity.md)선택합니다.

## <a name="next-steps"></a>다음 단계
Data Factory에서 복사 활동을 통해 원본 및 싱크로 지원되는 데이터 저장소의 목록은 [지원되는 데이터 저장소](copy-activity-overview.md#supported-data-stores-and-formats)를 참조하세요.
