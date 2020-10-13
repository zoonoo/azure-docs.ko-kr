---
title: Dynamics에서 데이터 복사 (Common Data Service)
description: Data factory 파이프라인의 복사 작업을 사용 하 여 Microsoft Dynamics CRM 또는 Microsoft Dynamics 365 (Common Data Service)에서 지원 되는 싱크 데이터 저장소로 또는 지원 되는 원본 데이터 저장소에서 Dynamics CRM 또는 Dynamics 365로 데이터를 복사 하는 방법에 대해 알아봅니다.
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
ms.date: 09/23/2020
ms.openlocfilehash: 942cbda3652692acc8eedf2ec9508bb501a60547
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91332103"
---
# <a name="copy-data-from-and-to-dynamics-365-common-data-service-or-dynamics-crm-by-using-azure-data-factory"></a>Azure Data Factory를 사용하여 Dynamics 365(Common Data Service) 또는 Dynamics CRM 간에 데이터 복사
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

이 문서에서는 Azure Data Factory의 복사 작업을 사용 하 여 Microsoft Dynamics 365 및 Microsoft Dynamics CRM 간에 데이터를 복사 하는 방법을 설명 합니다. 이 문서는 복사 작업에 대 한 일반적인 개요를 제공 하는 [복사 작업 개요](copy-activity-overview.md) 문서를 기반으로 합니다.

## <a name="supported-capabilities"></a>지원되는 기능

이 커넥터는 다음과 같은 작업에 대해 지원 됩니다.

- [지원 되는 원본 및 싱크 행렬이](copy-activity-overview.md) 포함 된 [복사 작업](copy-activity-overview.md)
- [조회 작업](control-flow-lookup-activity.md)

Dynamics 365(Common Data Service) 또는 Dynamics CRM에서 지원되는 모든 싱크 데이터 저장소로 데이터를 복사할 수 있습니다. 지원되는 모든 원본 데이터 저장소의 데이터를 Dynamics 365(Common Data Service) 또는 Dynamics CRM에 복사할 수도 있습니다. 복사 작업에서 원본 및 싱크로 지원 되는 데이터 저장소 목록은 [지원 되는 데이터 저장소](copy-activity-overview.md#supported-data-stores-and-formats) 표를 참조 하세요.

이 Dynamics 커넥터는 온라인 및 온-프레미스 모두에 대해 Dynamics 버전 7부터 9까지 지원 합니다. 더 구체적으로 살펴보면 다음과 같습니다.

- 버전 7은 Dynamics CRM 2015에 매핑됩니다.
- 버전 8은 dynamics CRM 2016 및 Dynamics 365의 초기 버전에 매핑됩니다.
- 버전 9는 Dynamics 365의 최신 버전에 매핑됩니다.

Dynamics 버전 및 제품에 대해 지원 되는 인증 유형 및 구성의 다음 표를 참조 하세요.

| Dynamics 버전 | 인증 형식 | 연결된 서비스 샘플 |
|:--- |:--- |:--- |
| Common Data Service <br/><br/> Dynamics 365 온라인 <br/><br/> Dynamics CRM 온라인 | Azure Active Directory (Azure AD) 서비스 주체 <br/><br/> Office 365 | [Dynamics online 및 Azure AD 서비스-주 또는 Office 365 인증](#dynamics-365-and-dynamics-crm-online) |
| IFD (인터넷 연결 배포)를 통한 Dynamics 365 온-프레미스 <br/><br/> IFD로 Dynamics CRM 2016 온-프레미스 <br/><br/> IFD로 Dynamics CRM 2015 온-프레미스 | IFD | [IFD 및 IFD 인증을 사용 하는 Dynamics 온-프레미스](#dynamics-365-and-dynamics-crm-on-premises-with-ifd) |

구체적으로 Dynamics 365에 대해 다음 애플리케이션 유형이 지원됩니다.

- Dynamics 365 for Sales
- Dynamics 365 for Customer Service
- Dynamics 365 for Field Service
- Dynamics 365 for Project Service Automation
- Dynamics 365 for Marketing

이 커넥터는 재무, 작업 및 인재 같은 다른 응용 프로그램 유형을 지원 하지 않습니다.

이 Dynamics 커넥터는 [DYNAMICS XRM 도구](https://docs.microsoft.com/dynamics365/customer-engagement/developer/build-windows-client-applications-xrm-tools)를 기반으로 빌드됩니다.

>[!TIP]
>Dynamics 365 재무 및 작업에서 데이터를 복사 하려면 [DYNAMICS AX 커넥터](connector-dynamics-ax.md)를 사용할 수 있습니다.

## <a name="prerequisites"></a>필수 구성 요소

Azure AD 서비스 주체 인증과 함께이 커넥터를 사용 하려면 Common Data Service 또는 Dynamics에서 S2S (서버 간) 인증을 설정 해야 합니다. 자세한 단계는 [이 문서](https://docs.microsoft.com/powerapps/developer/common-data-service/build-web-applications-server-server-s2s-authentication) 를 참조 하세요.

## <a name="get-started"></a>시작

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

다음 섹션에서는 Dynamics에 한정된 Data Factory 엔터티를 정의하는 데 사용되는 속성에 대해 자세히 설명합니다.

## <a name="linked-service-properties"></a>연결된 서비스 속성

Dynamics 연결 서비스에 다음 속성이 지원됩니다.

### <a name="dynamics-365-and-dynamics-crm-online"></a>Dynamics 365 및 Dynamics CRM online

| 속성 | 설명 | 필수 |
|:--- |:--- |:--- |
| type | Type 속성은 "Dynamics", "DynamicsCrm" 또는 "CommonDataServiceForApps"로 설정 해야 합니다. | 예 |
| deploymentType | Dynamics 인스턴스의 배포 유형입니다. Dynamics online의 경우이 값은 "Online" 이어야 합니다. | 예 |
| serviceUri | 브라우저에서 액세스 하는 것과 동일한 Dynamics 인스턴스의 서비스 URL입니다. 예를 들면 "https:// \<organization-name> [x] .com.com"입니다. | 예 |
| authenticationType | Dynamics 서버에 연결하기 위한 인증 유형입니다. 유효한 값은 "AADServicePrincipal" 및 "Office365"입니다. | 예 |
| servicePrincipalId | Azure AD 응용 프로그램의 클라이언트 ID입니다. | 인증이 "AADServicePrincipal" 인 경우 예 |
| servicePrincipalCredentialType | 서비스 사용자 인증에 사용할 자격 증명 형식입니다. 유효한 값은 "ServicePrincipalKey" 및 "ServicePrincipalCert"입니다. | 인증이 "AADServicePrincipal" 인 경우 예 |
| servicePrincipalCredential | 서비스 주체 자격 증명입니다. <br/><br/>"ServicePrincipalKey"을 자격 증명 유형으로 사용 하는 경우은 `servicePrincipalCredential` 연결 된 서비스 배포 시 Azure Data Factory 암호화 하는 문자열일 수 있습니다. 또는 Azure Key Vault의 비밀에 대 한 참조일 수 있습니다. <br/><br/>자격 증명으로 "ServicePrincipalCert"를 사용 하는 경우은 `servicePrincipalCredential` Azure Key Vault의 인증서에 대 한 참조 여야 합니다. | 인증이 "AADServicePrincipal" 인 경우 예 |
| 사용자 이름 | Dynamics에 연결할 사용자 이름입니다. | 인증이 "Office365" 인 경우 예 |
| password | 사용자 이름으로 지정한 사용자 계정의 암호입니다. 이 필드를 "SecureString"으로 표시 하 여 Data Factory에 안전 하 게 저장 하거나 [Azure Key Vault에 저장 된 암호를 참조](store-credentials-in-key-vault.md)합니다. | 인증이 "Office365" 인 경우 예 |
| connectVia | 데이터 저장소에 연결하는 데 사용할 [통합 런타임](concepts-integration-runtime.md)입니다. 값을 지정 하지 않으면 속성은 기본 Azure integration runtime을 사용 합니다. | 원본 연결 된 서비스에 통합 런타임이 없는 경우 원본에 대해 아니요, 싱크에 대해 예 |

>[!NOTE]
>Dynamics 커넥터는 이전에 선택적 **organizationName** 속성을 사용 하 여 dynamics CRM 또는 dynamics 365 online 인스턴스를 식별 했습니다. 해당 속성이 계속 작동 하는 동안에는 새 **serviceUri** 속성을 대신 지정 하 여 인스턴스 검색의 성능을 향상 시키는 것이 좋습니다.

#### <a name="example-dynamics-online-using-azure-ad-service-principal-and-key-authentication"></a>예: Azure AD 서비스를 사용 하는 Dynamics online-주체 및 키 인증

```json
{  
    "name": "DynamicsLinkedService",  
    "properties": {  
        "type": "Dynamics",  
        "typeProperties": {  
            "deploymentType": "Online",  
            "serviceUri": "https://<organization-name>.crm[x].dynamics.com",  
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
#### <a name="example-dynamics-online-using-azure-ad-service-principal-and-certificate-authentication"></a>예: Azure AD 서비스를 사용 하는 Dynamics online-주체 및 인증서 인증

```json
{ 
    "name": "DynamicsLinkedService", 
    "properties": { 
        "type": "Dynamics", 
        "typeProperties": { 
            "deploymentType": "Online", 
            "serviceUri": "https://<organization-name>.crm[x].dynamics.com", 
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

#### <a name="example-dynamics-online-using-office-365-authentication"></a>예: Office 365 인증을 사용 하는 Dynamics online

```json
{
    "name": "DynamicsLinkedService",
    "properties": {
        "type": "Dynamics",
        "typeProperties": {
            "deploymentType": "Online",
            "serviceUri": "https://<organization-name>.crm[x].dynamics.com",
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

Dynamics online과 비교 되는 추가 속성은 **호스트 이름** 및 **포트**입니다.

| 속성 | 설명 | 필수 |
|:--- |:--- |:--- |
| type | Type 속성은 "Dynamics", "DynamicsCrm" 또는 "CommonDataServiceForApps"로 설정 해야 합니다. | 예. |
| deploymentType | Dynamics 인스턴스의 배포 유형입니다. IFD를 사용 하는 Dynamics 온-프레미스의 경우이 값은 "OnPremisesWithIfd" 이어야 합니다.| 예. |
| hostName | 온-프레미스 Dynamics 서버의 호스트 이름입니다. | 예. |
| 포트 | 온-프레미스 Dynamics 서버의 포트입니다. | 아니요. 기본값은 443입니다. |
| organizationName | Dynamics 인스턴스의 조직 이름입니다. | 예. |
| authenticationType | Dynamics 서버에 연결하기 위한 인증 유형입니다. IFD를 사용하는 Dynamics 온-프레미스에 대해 "Ifd"를 지정합니다. | 예. |
| 사용자 이름 | Dynamics에 연결할 사용자 이름입니다. | 예. |
| password | 사용자 이름에 대해 지정한 사용자 계정의 암호입니다. 이 필드를 "SecureString"으로 표시 하 여 Data Factory에 안전 하 게 저장할 수 있습니다. 또는 Key Vault에 암호를 저장 하 고 복사 작업이 데이터 복사를 수행할 때 거기에서 끌어올 수 있도록 합니다. [Key Vault에 자격 증명 저장](store-credentials-in-key-vault.md)에서 자세히 알아봅니다. | 예. |
| connectVia | 데이터 저장소에 연결하는 데 사용할 [통합 런타임](concepts-integration-runtime.md)입니다. 값을 지정 하지 않으면 속성은 기본 Azure integration runtime을 사용 합니다. | 원본에는 아니요이 고 싱크에는 예입니다. |

#### <a name="example-dynamics-on-premises-with-ifd-using-ifd-authentication"></a>예제: IFD 인증을 사용하여 IFD로 Dynamics 온-프레미스

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

Dynamics에서 Dynamics로 데이터를 복사 하려면 다음 속성이 지원 됩니다.

| 속성 | 설명 | 필수 |
|:--- |:--- |:--- |
| type | 데이터 집합의 type 속성은 "DynamicsEntity", "DynamicsCrmEntity" 또는 "CommonDataServiceForAppsEntity"로 설정 해야 합니다. |예 |
| entityName | 검색할 엔터티의의 논리적 이름입니다. | 작업 원본이 "query"로 지정 되 고 싱크에 대해 예로 지정 된 경우 원본에 대해 아니요 |

#### <a name="example"></a>예제

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

Dynamics에서 데이터를 복사 하기 위해 복사 작업 **원본** 섹션은 다음 속성을 지원 합니다.

| 속성 | 설명 | 필수 |
|:--- |:--- |:--- |
| type | 복사 작업 원본의 type 속성은 "DynamicsSource", "DynamicsCrmSource" 또는 "CommonDataServiceForAppsSource"로 설정 해야 합니다. | 예 |
| Query | FetchXML은 Dynamics online 및 온-프레미스에서 사용 되는 전용 쿼리 언어입니다. 다음 예제를 참조하세요. 자세히 알아보려면 [FetchXML로 쿼리 작성](https://msdn.microsoft.com/library/gg328332.aspx)을 참조 하세요. | `entityName`데이터 집합에서이 지정 되지 않은 경우 |

>[!NOTE]
>PK 열은 FetchXML 쿼리에서 구성한 열 프로젝트에 포함되지 않은 경우에도 항상 복사됩니다.

> [!IMPORTANT]
>- Dynamics에서 데이터를 복사 하는 경우 Dynamics에서 sink로의 명시적 열 매핑은 선택 사항입니다. 하지만 결정적 복사 결과를 보장 하기 위해 매핑을 적극 권장 합니다.
>- Data Factory authoring UI에서 스키마를 가져올 때 스키마를 유추 합니다. 이렇게 하려면 Dynamics 쿼리 결과에서 상위 행을 샘플링 하 여 원본 열 목록을 초기화 합니다. 이 경우 상위 행에 값이 없는 열은 생략 됩니다. 명시적 매핑이 없는 경우에도 복사 실행에 동일한 동작이 적용 됩니다. 복사 런타임에 적용 되는 추가 열을 검토 하 고 매핑에 추가할 수 있습니다.

#### <a name="example"></a>예제

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

Dynamics에 데이터를 복사 하기 위해 복사 작업 **싱크** 섹션은 다음 속성을 지원 합니다.

| 속성 | 설명 | 필수 |
|:--- |:--- |:--- |
| type | 복사 작업 싱크의 type 속성은 "DynamicsSink", "DynamicsCrmSink" 또는 "CommonDataServiceForAppsSink"로 설정 해야 합니다. | 예. |
| writeBehavior | 작업의 쓰기 동작입니다. 값은 "Upsert" 이어야 합니다. | 예 |
| alternateKeyName | Upsert을 수행 하기 위해 엔터티에 정의 된 대체 키 이름입니다. | 아니요. |
| writeBatchSize | 각 일괄 처리에서 Dynamics에 작성된 데이터의 행 수입니다. | 아니요. 기본값은 10입니다. |
| ignoreNullValues | 쓰기 작업 중에 키 필드가 아닌 입력 데이터에서 null 값을 무시할지 여부를 지정 합니다.<br/><br/>유효한 값은 **TRUE** 및 **FALSE**입니다.<ul><li>**TRUE**: upsert 또는 update 작업을 수행할 때 대상 개체의 데이터를 변경 하지 않고 유지 합니다. 삽입 작업을 수행할 때 정의된 기본 값을 삽입합니다.</li><li>**FALSE**: upsert 또는 Update 작업을 수행할 때 대상 개체의 데이터를 null 값으로 업데이트 합니다. 삽입 작업을 수행 하는 경우 null 값을 삽입 합니다.</li></ul> | 아니요. 기본값은 **FALSE**입니다. |

>[!NOTE]
>Dynamics 싱크에 대 한 싱크 **Writebatchsize** 및 복사 작업 **[parallelCopies](copy-activity-performance-features.md#parallel-copy)** 의 기본값은 10입니다. 따라서 100 레코드는 기본적으로 Dynamics에 동시에 전송 됩니다.

Dynamics 365 online의 경우 [조직 당 두 개의 동시 일괄 처리 호출](https://msdn.microsoft.com/library/jj863631.aspx#Run-time%20limitations)수가 제한 됩니다. 이 제한을 초과 하면 첫 번째 요청이 실행 되기 전에 "서버 사용 중" 예외가 throw 됩니다. 동시 호출의 제한을 방지 하려면 **Writebatchsize** 를 10 개 이하로 유지 합니다.

**Writebatchsize** 및 **parallelCopies** 의 최적 조합은 엔터티의 스키마에 따라 결정 됩니다. 스키마 요소에는 열 수, 행 크기 및 해당 호출에 연결 된 플러그 인, 워크플로 또는 워크플로 작업 수가 포함 됩니다. **Writebatchsize** (10) &times; **parallelCopies** (10)의 기본 설정은 Dynamics 서비스에 따라 권장 되는 사항입니다. 이 값은 최상의 성능을 제공 하지 않을 수 있지만 대부분의 Dynamics 엔터티에 대해 작동 합니다. 복사 작업 설정의 조합을 조정하여 성능을 튜닝할 수 있습니다.

#### <a name="example"></a>예제

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

Dynamics에서 데이터를 복사 하는 경우 다음 표에서는 Dynamics 데이터 형식에서 Data Factory 중간 데이터 형식으로의 매핑을 보여 줍니다. 복사 활동이 소스 스키마에 매핑되는 방식과 데이터 형식이 싱크로 매핑되는 방법을 알아보려면 [스키마 및 데이터 형식 매핑](copy-activity-schema-and-type-mapping.md)을 참조 하세요.

다음 매핑 테이블을 사용 하 여 원본 Dynamics 데이터 형식에 따라 데이터 집합 구조에 해당 하는 Data Factory 데이터 형식을 구성 합니다.

| Dynamics 데이터 형식 | Data Factory 중간 데이터 형식 | 원본으로 지원됨 | 싱크로 지원됨 |
|:--- |:--- |:--- |:--- |
| AttributeTypeCode.BigInt | long | ✓ | ✓ |
| AttributeTypeCode.Boolean | 부울 | ✓ | ✓ |
| AttributeType.Customer | GUID | ✓ | ✓ ( [지침](#writing-data-to-a-lookup-field)참조) |
| AttributeType.DateTime | DateTime | ✓ | ✓ |
| AttributeType.Decimal | Decimal | ✓ | ✓ |
| AttributeType.Double | Double | ✓ | ✓ |
| AttributeType.EntityName | String | ✓ | ✓ |
| AttributeType.Integer | Int32 | ✓ | ✓ |
| AttributeType.Lookup | GUID | ✓ | ✓ ( [지침](#writing-data-to-a-lookup-field)참조) |
| AttributeType.ManagedProperty | 부울 | ✓ | |
| AttributeType.Memo | String | ✓ | ✓ |
| AttributeType.Money | Decimal | ✓ | ✓ |
| AttributeType.Owner | GUID | ✓ | ✓ ( [지침](#writing-data-to-a-lookup-field)참조) |
| AttributeType.Picklist | Int32 | ✓ | ✓ |
| AttributeType.Uniqueidentifier | GUID | ✓ | ✓ |
| AttributeType.String | String | ✓ | ✓ |
| AttributeType.State | Int32 | ✓ | ✓ |
| AttributeType.Status | Int32 | ✓ | ✓ |

> [!NOTE]
> Dynamics 데이터 형식 **attributetype. CalendarRules**, **Attributetype. Multiselectpicklist**및 **attributetype.partylist** 는 지원 되지 않습니다.

## <a name="writing-data-to-a-lookup-field"></a>조회 필드에 데이터 쓰기

고객 및 소유자와 같은 여러 대상이 포함 된 조회 필드에 데이터를 쓰려면 다음 지침과 예제를 따르세요.

1. 원본에 필드 값과 해당 대상 엔터티 이름이 모두 포함 되도록 합니다.
   - 모든 레코드가 동일한 대상 엔터티에 매핑되는 경우 다음 조건 중 하나를 확인 합니다.
      - 원본 데이터에는 대상 엔터티 이름을 저장 하는 열이 있습니다.
      - 대상 엔터티를 정의 하기 위해 복사 작업 원본에 추가 열을 추가 했습니다.
   - 다른 레코드가 다른 대상 엔터티에 매핑되는 경우 원본 데이터에 해당 대상 엔터티 이름을 저장 하는 열이 있는지 확인 합니다.

1. 값 및 엔터티 참조 열을 모두 원본에서 싱크에 매핑합니다. 엔터티-참조 열은 특수 명명 패턴을 사용 하 여 가상 열에 매핑되어야 합니다 `{lookup_field_name}@EntityReference` . 이 열은 실제로 Dynamics에 존재 하지 않습니다. 이 열은 지정 된 멀티 타기 팅 조회 필드의 메타 데이터 열인지 여부를 나타내는 데 사용 됩니다.

예를 들어 원본에 다음 두 개의 열이 있다고 가정 합니다.

- **GUID**형식의 **Customerfield** 열 (Dynamics의 대상 엔터티의 기본 키 값)입니다.
- 대상 엔터티의 논리적 이름인 **String**형식의 **대상** 열입니다.

또한 이러한 데이터를 **Customer**형식의 싱크 Dynamics 엔터티 필드인 **customerfield** 로 복사 하려는 경우를 가정 합니다.

복사-작업 열 매핑에서 두 열을 다음과 같이 매핑합니다.

- **CustomerField** Customerfield를 **customerfield**로 이 매핑은 표준 필드 매핑입니다.
- **Customerfield \@ EntityReference**를 **대상** 으로 합니다. 싱크 열은 엔터티 참조를 나타내는 가상 열입니다. 스키마를 가져오면 표시 되지 않으므로 매핑에 이러한 필드 이름을 입력 합니다.

![Dynamics 조회-필드 열 매핑](./media/connector-dynamics-crm-office-365/connector-dynamics-lookup-field-column-mapping.png)

모든 원본 레코드가 동일한 대상 엔터티에 매핑되고 원본 데이터가 대상 엔터티 이름을 포함 하지 않는 경우 바로 가기: 복사 작업 원본에서 추가 열을 추가 합니다. 패턴을 사용 하 여 새 열의 이름을로 `{lookup_field_name}@EntityReference` 지정한 다음, 값을 대상 엔터티 이름으로 설정 하 고, 일반적으로 열 매핑을 진행 합니다. 원본 및 싱크 열 이름이 동일한 경우에는 기본적으로 복사 작업에서 열을 이름으로 매핑하며 명시적 열 매핑을 건너뛸 수도 있습니다.

![Dynamics lookup-필드 엔터티-참조 열 추가](./media/connector-dynamics-crm-office-365/connector-dynamics-add-entity-reference-column.png)

## <a name="lookup-activity-properties"></a>조회 작업 속성

속성에 대 한 자세한 내용을 보려면 [조회 작업](control-flow-lookup-activity.md)을 참조 하세요.

## <a name="next-steps"></a>다음 단계
Data Factory에서 복사 작업을 원본 및 싱크로 사용할 수 있도록 하는 데이터 저장소 목록은 [지원 되는 데이터 저장소](copy-activity-overview.md#supported-data-stores-and-formats)를 참조 하세요.
