---
title: Azure Data Factory를 사용하여 Office 365에서 데이터 복사(미리 보기) | Microsoft Docs
description: Azure Data Factory 파이프라인의 복사 작업을 사용하여 Office 365에서 지원되는 싱크 데이터 저장소로 데이터를 복사하는 방법에 대해 알아봅니다.
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
ms.date: 10/22/2018
ms.author: jingwang
ms.openlocfilehash: 82fb2241b5988bae9587807c03e7bec50e7c1677
ms.sourcegitcommit: 5c00e98c0d825f7005cb0f07d62052aff0bc0ca8
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/24/2018
ms.locfileid: "49955384"
---
# <a name="copy-data-from-office-365-into-azure-using-azure-data-factory-preview"></a>Azure Data Factory(미리 보기)를 사용하여 Office 365에서 Azure로 데이터 복사 

Azure Data Factory를 사용하면 Office 365 테넌트의 풍부한 조직 데이터를 확장 가능한 방식으로 Azure로 가져와서 분석 응용 프로그램을 빌드하고 이러한 중요 데이터 자산을 기반으로 인사이트를 추출할 수 있습니다. Privileged Access Management와 통합하면 Office 365의 큐레이팅된 중요한 데이터에 대한 보안 액세스 제어가 가능합니다.  Microsoft Graph 데이터 연결에 대한 자세한 내용은 [이 링크](https://github.com/OfficeDev/ManagedAccessMSGraph/wiki)를 참조하세요.

이 문서에서는 Azure Data Factory의 복사 작업을 사용하여 Office 365에서 데이터를 복사하는 방법에 대해 설명합니다. 이 문서는 복사 작업에 대한 일반적인 개요를 제공하는 [복사 작업 개요](copy-activity-overview.md) 문서를 기반으로 합니다.

## <a name="supported-capabilities"></a>지원되는 기능

현재 단일 복사 작업 내에서 **Office 365의 데이터를 [Azure Blob Storage](connector-azure-blob-storage.md), [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md) 및 [Azure Data Lake Storage Gen2 (미리 보기)](connector-azure-data-lake-storage.md)에 JSON 형식**(setOfObjects 형식)으로만 복사할 수 있습니다. Office 365를 다른 유형의 데이터 저장소나 다른 형식으로 로드하려면, 첫 번째 복사 작업을 후속 복사 작업과 연결하여 [지원되는 ADF 대상 저장소](copy-activity-overview.md#supported-data-stores-and-formats)로 데이터를 추가로 로드할 수 있습니다("지원되는 데이터 저장소 및 형식" 표의 "싱크로 지원" 열 참조).

>[!IMPORTANT]
>- 데이터 팩터리와 싱크 데이터 저장소가 포함된 Azure 구독은 Office 365 테넌트와 동일한 Azure AD(Azure Active Directory) 테넌트에 속해야 합니다.
>- 복사 작업에 사용된 Azure Integration Runtime 지역 및 대상이 Office 365 테넌트 사용자의 사서함이 있는 곳과 동일한 지역에 있어야 합니다. Azure IR 위치가 결정되는 방식을 이해하려면 [여기](concepts-integration-runtime.md#integration-runtime-location)를 참조하세요. 지원되는 Office 지역 및 해당되는 Azure 지역 목록은 [여기 표](https://github.com/OfficeDev/ManagedAccessMSGraph/wiki/Capabilities#data-regions)를 참조하세요.
>-  Office 365 데이터를 **Azure Blob Storage**에 대상으로 로드하는 경우 Azure Blob Storage에 대한 연결된 서비스를 정의할 때 **[서비스 주체 인증](connector-azure-blob-storage.md#service-principal-authentication)** 을 사용해야 하고 [계정 키](connector-azure-blob-storage.md#account-key-authentication), [공유 액세스 서명](connector-azure-blob-storage.md#shared-access-signature-authentication) 또는 [Azure 리소스에 대한 관리 ID](connector-azure-blob-storage.md#managed-identity) 인증은 사용하지 말아야 합니다.
>-  Office 365 데이터를 **Azure Data Lake Storage Gen1**에 대상으로 로드하는 경우 Azure Data Lake Storage Gen1에 대한 연결된 서비스를 정의할 때 [**서비스 주체 인증**](connector-azure-data-lake-store.md#using-service-principal-authentication)을 사용해야 하고 [Azure 리소스에 대한 관리 ID 인증](connector-azure-data-lake-store.md#managed-identity)은 사용하지 말아야 합니다.

## <a name="prerequisites"></a>필수 조건

Office 365에서 Azure로 데이터를 복사하려면 다음 필수 구성 요소 단계를 완료해야 합니다.

- Office 365 테넌트 관리자가 [여기](https://github.com/OfficeDev/ManagedAccessMSGraph/wiki/On-boarding)에 설명된 온보딩 작업을 완료해야 합니다.
- Azure Active Directory에서 Azure AD 웹 응용 프로그램을 만들고 구성합니다.  지침에 대해서는 [Azure AD 응용 프로그램 만들기](../active-directory/develop/howto-create-service-principal-portal.md#create-an-azure-active-directory-application)를 참조하세요.
- Office 365에 대한 연결된 서비스를 정의하는 데 사용되므로 다음 값을 적어둡니다.
    - 테넌트 ID.  지침은 [테넌트 ID 가져오기](../active-directory/develop/howto-create-service-principal-portal.md#get-tenant-id)를 참조하세요.
    - 응용 프로그램 ID 및 인증 키.  지침은 [응용 프로그램 ID 및 인증 키 가져오기](../active-directory/develop/howto-create-service-principal-portal.md#get-application-id-and-authentication-key)를 참조하세요.
- Azure AD 웹 응용 프로그램의 소유자로 데이터 액세스를 요청할 사용자 ID를 추가(Azure AD 웹 응용 프로그램 > 설정 > 소유자 > 소유자 추가를 통해)합니다.

## <a name="approving-new-data-access-requests"></a>새로운 데이터 액세스 요청 승인

이 컨텍스트(어떤 데이터 테이블이 액세스되고 있는지, 어떤 대상 계정에 데이터가 로드 중인지, 어떤 사용자 ID가 데이터 액세스를 요청하고 있는지의 조합)에 대한 데이터를 처음 요청하는 경우 복사 작업 상태가 "진행 중"으로 표시되고, [작업 아래 "세부 정보" 링크](copy-activity-overview.md#monitoring)를 클릭할 때만 “RequestingConsent”로 상태가 표시됩니다.  데이터 액세스 승인자 그룹의 멤버가 데이터 추출이 진행되기 전에 Privileged Access Management에서 요청을 승인해야 합니다.

승인자가 데이터 액세스 요청을 승인하는 방법은 [여기](https://github.com/OfficeDev/MS-Graph-Data-Connect/wiki/Approving-data-access-requests)를 참조하고 데이터 액세스 승인자 그룹을 설정하는 방법을 포함하여 Privileged Access Management와의 전반적인 통합에 대한 설명은 [여기](https://github.com/OfficeDev/MS-Graph-Data-Connect/wiki/Capabilities#integration-with-privileged-access-management)를 참조하세요.

## <a name="policy-validation"></a>정책 유효성 검사

ADF가 관리되는 앱의 일부로 생성되고 Azure 정책 할당이 관리 리소스 그룹 내의 리소스에 대해 수행되는 경우 복사 작업이 실행될 때마다 ADF가 정책 할당이 적용되는지 확인합니다. 지원 되는 정책 목록은 [여기](https://github.com/OfficeDev/ManagedAccessMSGraph/wiki/Capabilities#policies)를 참조하세요.

## <a name="getting-started"></a>시작

>[!TIP]
>Office 365 커넥터 사용 연습은 [Office 365의 데이터 로드](load-office-365-data.md) 문서를 참조하세요.

다음과 같은 도구 또는 SDK 중 하나를 사용하여 복사 작업으로 파이프라인을 만들 수 있습니다. 링크를 선택하면 복사 작업으로 파이프라인을 만드는 단계별 지침을 안내하는 자습서로 이동됩니다. 

- [Azure Portal](quickstart-create-data-factory-portal.md)
- [.NET SDK](quickstart-create-data-factory-dot-net.md)
- [Python SDK](quickstart-create-data-factory-python.md)
- [Azure PowerShell](quickstart-create-data-factory-powershell.md)
- [REST API](quickstart-create-data-factory-rest-api.md)
- [Azure Resource Manager 템플릿](quickstart-create-data-factory-resource-manager-template.md). 

다음 섹션에서는 Office 365 커넥터와 관련된 Data Factory 엔터티를 정의하는 데 사용되는 속성에 대해 자세히 설명합니다.

## <a name="linked-service-properties"></a>연결된 서비스 속성

Office 365 연결된 서비스에 대해 다음 속성이 지원됩니다.

| 자산 | 설명 | 필수 |
|:--- |:--- |:--- |
| 형식 | 형식 속성은 **Office365**로 설정해야 합니다. | yes |
| office365TenantId | Office 365 계정이 속하는 Azure 테넌트 ID입니다. | yes |
| servicePrincipalTenantId | Azure AD 웹 응용 프로그램이 상주하는 테넌트 정보를 지정합니다. | yes |
| servicePrincipalId | 응용 프로그램의 클라이언트 ID를 지정합니다. | yes |
| servicePrincipalKey | 응용 프로그램의 키를 지정합니다. 이 필드를 SecureString으로 표시하여 Data Factory에서 안전하게 저장합니다. | yes |
| connectVia | 데이터 저장소에 연결하는 데 사용할 Integration Runtime입니다.  지정하지 않으면 기본 Azure Integration Runtime을 사용합니다. | 아니요 |

>[!NOTE]
> **office365TenantId**와 **servicePrincipalTenantId** 사이의 차이점 및 제공할 해당 값:
>- 자체 조직에서 사용하기 위해 Office 365 데이터에 대한 응용 프로그램을 개발하는 엔터프라이즈 개발자인 경우, 두 속성 모두에 조직의 AAD 테넌트 ID인 동일한 테넌트 ID를 제공해야 합니다.
>- 고객용 응용 프로그램을 개발하는 ISV 개발자의 경우 office365TenantId가 고객(응용 프로그램을 설치하는 사용자)의 AAD 테넌트 ID이며 servicePrincipalTenantId가 회사의 AAD 테넌트 ID가 됩니다.

**예제:**

```json
{
    "name": "Office365LinkedService",
    "properties": {
        "type": "Office365",
        "typeProperties": {
            "office365TenantId": "<Office 365 tenant id>",
            "servicePrincipalTenantId": "<AAD app service principal tenant id>",
            "servicePrincipalId": "<AAD app service principal id>",
            "servicePrincipalKey": {
                "type": "SecureString",
                "value": "<AAD app service principal key>"
            }
        }
    }
}
```

## <a name="dataset-properties"></a>데이터 세트 속성

데이터 집합 정의에 사용할 수 있는 섹션 및 속성의 전체 목록은 [데이터 집합](concepts-datasets-linked-services.md) 문서를 참조하세요. 이 섹션에서는 Office 365 데이터 세트에서 지원하는 속성의 목록을 제공합니다.

Office 365의 데이터를 복사하려는 경우 다음과 같은 속성이 지원됩니다.

| 자산 | 설명 | 필수 |
|:--- |:--- |:--- |
| 형식 | 데이터 세트의 type 속성은 **Office365Table**로 설정해야 합니다. | yes |
| tableName | Office 365에서 추출할 데이터 세트의 이름입니다. 추출할 수 있는 Office 365 데이터 세트 목록은 [여기](https://github.com/OfficeDev/MS-Graph-Data-Connect/wiki/Capabilities#datasets)를 참조하세요. | yes |
| 조건자 | Office 365에서 추출하기 위해 특정 행을 필터링하는 데 사용할 수 있는 조건자 식입니다.  각 테이블에 대한 조건자 필터링 및 필터 식 형식에 사용할 수 있는 열을 알아보려면 [여기](https://github.com/OfficeDev/MS-Graph-Data-Connect/wiki/Capabilities#filters)를 참조하세요. | 아니요<br>(조건자가 지정되지 않는 경우 기본값은 최근 30일 동안의 데이터를 추출하는 것입니다.) |

**예제**

```json
{
    "name": "Office365Table1",
    "properties": {
        "type": "Office365Table",
        "linkedServiceName": {
            "referenceName": "<Office 365 linked service name>",
            "type": "LinkedServiceReference"
        },
        "structure": [
            {
                "name": "ReceivedDateTime",
                "type": "datetime"
            },
            {
                "name": "SentDateTime",
                "type": "datetime"
            },
            {
                "name": "HasAttachments",
                "type": "boolean"
            },
            {
                "name": "InternetMessageId",
                "type": "string"
            },
            {
                "name": "Subject",
                "type": "string"
            },
            {
                "name": "Importance",
                "type": "string"
            },
            {
                "name": "ParentFolderId",
                "type": "string"
            },
            {
                "name": "Sender",
                "type": "string"
            },
            {
                "name": "From",
                "type": "string"
            },
            {
                "name": "ToRecipients",
                "type": "string"
            },
            {
                "name": "CcRecipients",
                "type": "string"
            },
            {
                "name": "BccRecipients",
                "type": "string"
            },
            {
                "name": "ReplyTo",
                "type": "string"
            },
            {
                "name": "ConversationId",
                "type": "string"
            },
            {
                "name": "UniqueBody",
                "type": "string"
            },
            {
                "name": "IsDeliveryReceiptRequested",
                "type": "boolean"
            },
            {
                "name": "IsReadReceiptRequested",
                "type": "boolean"
            },
            {
                "name": "IsRead",
                "type": "boolean"
            },
            {
                "name": "IsDraft",
                "type": "boolean"
            },
            {
                "name": "WebLink",
                "type": "string"
            },
            {
                "name": "CreatedDateTime",
                "type": "datetime"
            },
            {
                "name": "LastModifiedDateTime",
                "type": "datetime"
            },
            {
                "name": "ChangeKey",
                "type": "string"
            },
            {
                "name": "Categories",
                "type": "string"
            },
            {
                "name": "Id",
                "type": "string"
            },
            {
                "name": "Attachments",
                "type": "string"
            }
        ],
        "typeProperties": {
            "tableName": "BasicDataSet_v0.Contact_v0",
            "predicate": "CreatedDateTime >= 2018-07-11T16:00:00.000Z AND CreatedDateTime <= 2018-07-18T16:00:00.000Z"
        }
    }
}
```

## <a name="copy-activity-properties"></a>복사 작업 속성

작업 정의에 사용할 수 있는 섹션 및 속성의 전체 목록은 [파이프라인](concepts-pipelines-activities.md) 문서를 참조하세요. 이 섹션에서는 Office 365 원본에서 지원하는 속성의 목록을 제공합니다.

### <a name="office-365-as-source"></a>Office 365를 원본으로

Office 365의 데이터를 복사하려면 복사 작업의 원본 형식을 **Office365Source**로 설정합니다. 복사 작업 **source** 섹션에서 추가 속성이 지원되지 않습니다.

**예제:**

```json
"activities": [
    {
        "name": "CopyFromO365ToBlob",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Office 365 input dataset name>",
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
                "type": "Office365Source"
            },
            "sink": {
                "type": "BlobSink"
            }
        }
    }
]
```

## <a name="next-steps"></a>다음 단계
Azure Data Factory에서 복사 작업의 원본 및 싱크로 지원되는 데이터 저장소 목록은 [지원되는 데이터 저장소](copy-activity-overview.md#supported-data-stores-and-formats)를 참조하세요.
