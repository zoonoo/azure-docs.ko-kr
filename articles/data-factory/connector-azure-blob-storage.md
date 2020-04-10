---
title: Azure Blob 저장소에서 데이터 복사 및 변환
description: 데이터 팩터리(Data Factory)를 사용하여 Blob 저장소에서 데이터를 복사하고 Blob 저장소에서 데이터를 변환하는 방법을 알아봅니다.
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 04/09/2020
ms.openlocfilehash: dd13a08b3c2f63baf509efbb730032edd4eba61a
ms.sourcegitcommit: ae3d707f1fe68ba5d7d206be1ca82958f12751e8
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/10/2020
ms.locfileid: "81011551"
---
# <a name="copy-and-transform-data-in-azure-blob-storage-by-using-azure-data-factory"></a>Azure 데이터 팩터리를 사용하여 Azure Blob 저장소에서 데이터 복사 및 변환

> [!div class="op_single_selector" title1="사용 중인 Data Factory 서비스 버전을 선택합니다."]
> * [버전 1](v1/data-factory-azure-blob-connector.md)
> * [현재 버전](connector-azure-blob-storage.md)

이 문서에서는 Azure 데이터 팩터리에서 복사 활동 사용 하 여 Azure Blob 저장소에서 데이터를 복사 하 고 데이터 흐름을 사용 하 여 Azure Blob 저장소에서 데이터를 변환 하는 방법을 설명 합니다. Azure Data Factory에 대해 자세히 알아보려면 [소개 문서](introduction.md)를 참조하세요.

>[!TIP]
>데이터 레이크 또는 데이터 웨어하우스 마이그레이션 시나리오의 경우 [Azure Data Factory 사용에서 데이터 레이크 또는 데이터 웨어하우스에서 Azure로 데이터를 마이그레이션하는](data-migration-guidance-overview.md)방법에 대해 자세히 알아봅니다.

## <a name="supported-capabilities"></a>지원되는 기능

이 Azure Blob 커넥터는 다음 활동에 대해 지원됩니다.

- [지원되는 소스/싱크 매트릭스로](copy-activity-overview.md) [활동 복사](copy-activity-overview.md)
- [데이터 흐름 매핑](concepts-data-flow-overview.md)
- [조회 활동](control-flow-lookup-activity.md)
- [GetMetadata 작업](control-flow-get-metadata-activity.md)
- [활동 삭제](delete-activity.md)

복사 활동의 경우 이 Blob 저장소 커넥터는 다음을 지원합니다.

- 범용 Azure 스토리지 계정 및 핫/쿨 Blob Storage 간에 Blob을 복사합니다. 
- Azure 리소스 인증을 위해 계정 키, 서비스 공유 액세스 서명, 서비스 주체 또는 관리 ID를 사용하여 Blob을 복사합니다.
- 블록, 추가 또는 페이지 Blob에서 Blob을 복사하고, 블록 Blob에만 데이터를 복사합니다.
- Blob을 있는 그대로 복사하거나 [지원되는 파일 형식 및 압축 코덱](supported-file-formats-and-compression-codecs.md)을 사용하여 Blob을 구문 분석하거나 생성합니다.
- [복사 하는 동안 파일 메타 데이터를 유지](#preserve-metadata-during-copy)합니다.

>[!IMPORTANT]
>신뢰할 수 **있는 Microsoft 서비스 허용을** Azure Storage 방화벽 설정에서 이 저장소 계정 옵션에 액세스할 수 있도록 설정하고 Azure 통합 런타임을 사용하여 Blob Storage에 연결하려면 [관리되는 ID 인증을](#managed-identity)사용해야 합니다.

## <a name="get-started"></a>시작하기

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

다음 섹션에서는 Blob Storage에 특정한 Data Factory 엔터티를 정의하는 데 사용되는 속성에 대해 자세히 설명합니다.

## <a name="linked-service-properties"></a>연결된 서비스 속성

Azure Blob 커넥터에서 지원하는 인증 유형은 다음과 같습니다. 자세한 내용은 해당 섹션을 참조하세요.

- [계정 키 인증](#account-key-authentication)
- [공유 액세스 서명 인증](#shared-access-signature-authentication)
- [서비스 주체 인증](#service-principal-authentication)
- [Azure 리소스 인증을 위한 관리 ID](#managed-identity)

>[!NOTE]
>PolyBase를 사용하여 데이터를 SQL 데이터 웨어하우스에 로드하는 경우 원본 또는 스테이징 Blob 저장소가 가상 네트워크 끝점으로 구성된 경우 PolyBase에서 요구하는 대로 관리되는 ID 인증을 사용하고 버전 3.18 이상의 자체 호스팅 통합 런타임을 사용해야 합니다. 더 많은 구성 전제 조건이 있는 [관리되는 ID 인증](#managed-identity) 섹션을 참조하십시오.

>[!NOTE]
>HDInsights 및 Azure 기계 학습 활동은 Azure Blob 저장소 계정 키 인증만 지원합니다.

### <a name="account-key-authentication"></a>계정 키 인증

스토리지 계정 키 인증을 사용하는 데 지원되는 속성은 다음과 같습니다.

| 속성 | 설명 | 필수 |
|:--- |:--- |:--- |
| type | type 속성은 **AzureBlobStorage**(권장) 또는 **AzureStorage**로 설정해야 합니다(아래 참고 참조). |예 |
| connectionString | connectionString 속성에 대한 Storage에 연결하는 데 필요한 정보를 지정합니다. <br/> Azure Key Vault에 계정 키를 넣고, 연결 문자열에서 `accountKey` 구성을 끌어올 수도 있습니다. 자세한 내용은 다음 샘플 및 [Azure Key Vault에 자격 증명 저장](store-credentials-in-key-vault.md) 문서를 참조하세요. |예 |
| connectVia | 데이터 저장소에 연결하는 데 사용할 [통합 런타임](concepts-integration-runtime.md)입니다. Azure Integration Runtime 또는 자체 호스팅 Integration Runtime(데이터 저장소가 사설망에 있는 경우)을 사용할 수 있습니다. 지정하지 않으면 기본 Azure Integration Runtime을 사용합니다. |예 |

>[!NOTE]
>계정 키 인증을 사용할 때 보조 Blob 서비스 끝점이 지원되지 않습니다. 다른 인증 유형을 사용할 수 있습니다.

>[!NOTE]
>"AzureStorage" 유형의 연결된 서비스를 사용하는 경우에도 있는 그대로 계속 지원되지만, 앞으로 이 새로운 "AzureBlobStorage" 연결된 서비스 유형을 사용하는 것이 좋습니다.

**예제:**

```json
{
    "name": "AzureBlobStorageLinkedService",
    "properties": {
        "type": "AzureBlobStorage",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**예제: Azure Key Vault에 계정 키 저장**

```json
{
    "name": "AzureBlobStorageLinkedService",
    "properties": {
        "type": "AzureBlobStorage",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;",
            "accountKey": { 
                "type": "AzureKeyVaultSecret", 
                "store": { 
                    "referenceName": "<Azure Key Vault linked service name>", 
                    "type": "LinkedServiceReference" 
                }, 
                "secretName": "<secretName>" 
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }            
    }
}
```

### <a name="shared-access-signature-authentication"></a>공유 액세스 서명 인증

공유 액세스 서명은 스토리지 계정의 리소스에 대한 위임된 권한을 제공합니다. 공유 액세스 서명을 사용하여 스토리지 계정의 개체에 대해 지정된 시간 동안 제한된 권한을 클라이언트에 부여할 수 있습니다. 계정 액세스 키를 공유할 필요가 없습니다. 공유 액세스 서명은 스토리지 리소스에 대해 인증된 액세스에 필요한 모든 정보를 쿼리 매개 변수에 포함하는 URI입니다. 공유 액세스 서명을 사용하여 스토리지 리소스에 액세스하려면 클라이언트에서 공유 액세스 서명을 해당 생성자 또는 메서드에 전달하기만 하면 됩니다. 공유 액세스 서명에 대한 자세한 내용은 [공유 액세스 서명: 공유 액세스 서명 모델 이해](../storage/common/storage-dotnet-shared-access-signature-part-1.md)를 참조하세요.

> [!NOTE]
>- Data Factory에서 이제 **서비스 공유 액세스 서명**과 **계정 공유 액세스 서명**이 모두 지원됩니다. 공유 액세스 서명에 대한 자세한 내용은 [SAS(공유 액세스 서명)를 사용하여 Azure Storage 리소스에 대한 제한된 액세스 권한 부여를](../storage/common/storage-sas-overview.md)참조하십시오.
>- 이후 데이터 세트 구성에서 폴더 경로는 컨테이너 수준에서 시작하는 절대 경로입니다. SAS URI의 경로와 일치하는 경로를 구성해야 합니다.

공유 액세스 서명 인증을 사용하는 데 지원되는 속성은 다음과 같습니다.

| 속성 | 설명 | 필수 |
|:--- |:--- |:--- |
| type | type 속성은 **AzureBlobStorage**(권장) 또는 **AzureStorage**로 설정해야 합니다(아래 참고 참조). |예 |
| sasUri | 스토리지 리소스(예: Blob/컨테이너)에 대한 공유 액세스 서명 URI를 지정합니다. <br/>이 필드를 SecureString으로 표시하여 Data Factory에서 안전하게 저장합니다. 또한 Azure Key Vault에 SAS 토큰을 넣어 자동 회전을 활용하고 토큰 부분을 제거할 수도 있습니다. 자세한 내용은 다음 샘플 및 [Azure Key Vault에 자격 증명 저장](store-credentials-in-key-vault.md) 문서를 참조하세요. |예 |
| connectVia | 데이터 저장소에 연결하는 데 사용할 [통합 런타임](concepts-integration-runtime.md)입니다. Azure Integration Runtime 또는 자체 호스팅 Integration Runtime(데이터 저장소가 사설망에 있는 경우)을 사용할 수 있습니다. 지정하지 않으면 기본 Azure Integration Runtime을 사용합니다. |예 |

>[!NOTE]
>"AzureStorage" 유형의 연결된 서비스를 사용하는 경우에도 있는 그대로 계속 지원되지만, 앞으로 이 새로운 "AzureBlobStorage" 연결된 서비스 유형을 사용하는 것이 좋습니다.

**예제:**

```json
{
    "name": "AzureBlobStorageLinkedService",
    "properties": {
        "type": "AzureBlobStorage",
        "typeProperties": {
            "sasUri": {
                "type": "SecureString",
                "value": "<SAS URI of the Azure Storage resource e.g. https://<container>.blob.core.windows.net/?sv=<storage version>&amp;st=<start time>&amp;se=<expire time>&amp;sr=<resource>&amp;sp=<permissions>&amp;sip=<ip range>&amp;spr=<protocol>&amp;sig=<signature>>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**예제: Azure Key Vault에 계정 키 저장**

```json
{
    "name": "AzureBlobStorageLinkedService",
    "properties": {
        "type": "AzureBlobStorage",
        "typeProperties": {
            "sasUri": {
                "type": "SecureString",
                "value": "<SAS URI of the Azure Storage resource without token e.g. https://<container>.blob.core.windows.net/>"
            },
            "sasToken": { 
                "type": "AzureKeyVaultSecret", 
                "store": { 
                    "referenceName": "<Azure Key Vault linked service name>", 
                    "type": "LinkedServiceReference" 
                }, 
                "secretName": "<secretName>" 
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

공유 액세스 서명 URI를 만들 때 고려해야 할 사항은 다음과 같습니다.

- 데이터 팩터리에서 연결된 서비스(읽기, 쓰기, 읽기/쓰기)를 사용하는 방법에 따라 개체에 대한 적절한 읽기/쓰기 권한을 설정합니다.
- **만료 시간**을 적절하게 설정합니다. Storage 개체에 대한 액세스가 파이프라인의 활성 기간 내에 만료되지 않도록 합니다.
- URI는 필요에 따라 적절한 컨테이너/Blob에 만들어야 합니다. Blob에 대한 공유 액세스 서명 URI를 사용하면 Data Factory에서 해당 특정 Blob에 액세스할 수 있습니다. Blob 스토리지 컨테이너에 대한 공유 액세스 서명 URI를 사용하면 Data Factory에서 해당 컨테이너의 Blob을 반복할 수 있습니다. 나중에 더 많거나 적은 개체에 대한 액세스를 제공하거나 공유 액세스 서명 URI를 업데이트하려면 연결된 서비스를 새 URI로 업데이트해야 합니다.

### <a name="service-principal-authentication"></a>서비스 주체 인증

Azure Storage 서비스 주체 인증은 일반적으로 [Azure Active Directory를 사용하여 Azure Storage에 대한 액세스 인증](../storage/common/storage-auth-aad.md)을 참조하세요.

서비스 주체 인증을 사용하려면 다음 단계를 수행합니다.

1. [Azure AD 테넌트에 애플리케이션 등록](../storage/common/storage-auth-aad-app.md#register-your-application-with-an-azure-ad-tenant)에 따라 Azure AD(Azure Active Directory)에 애플리케이션 항목을 등록합니다. 연결된 서비스를 정의하는 데 사용되므로 다음 값을 적어둡니다.

    - 애플리케이션 UI
    - 애플리케이션 키
    - 테넌트 ID

2. Azure Blob Storage에서 서비스 주체에게 적절한 권한을 부여합니다. 역할에 대한 자세한 내용은 [RBAC를 사용하여 Azure Storage 데이터에 대한 액세스 권한 관리](../storage/common/storage-auth-aad-rbac.md)를 참조하세요.

    - **원본으로**의 액세스 제어(IAM)에서 최소한 **Storage Blob 데이터 읽기 권한자** 역할을 부여합니다.
    - **싱크의 경우** 액세스 제어(IAM)에서 **Storage Blob 데이터 기여자** 역할 이상을 부여합니다.

Azure Blob Storage 연결된 서비스에 지원되는 속성은 다음과 같습니다.

| 속성 | 설명 | 필수 |
|:--- |:--- |:--- |
| type | type 속성은 **AzureBlobStorage**로 설정해야 합니다. |예 |
| serviceEndpoint | 패턴이 `https://<accountName>.blob.core.windows.net/`인 Azure Blob Storage 서비스 엔드포인트를 지정합니다. |예 |
| servicePrincipalId | 애플리케이션의 클라이언트 ID를 지정합니다. | 예 |
| servicePrincipalKey | 애플리케이션의 키를 지정합니다. 이 필드를 **SecureString**으로 표시하여 Data Factory에 안전하게 저장하거나, [Azure Key Vault에 저장된 비밀을 참조](store-credentials-in-key-vault.md)합니다. | 예 |
| tenant | 애플리케이션이 있는 테넌트 정보(도메인 이름 또는 테넌트 ID)를 지정합니다. Azure Portal의 오른쪽 위 모서리를 마우스로 가리켜 검색합니다. | 예 |
| connectVia | 데이터 저장소에 연결하는 데 사용할 [통합 런타임](concepts-integration-runtime.md)입니다. Azure Integration Runtime 또는 자체 호스팅 Integration Runtime(데이터 저장소가 사설망에 있는 경우)을 사용할 수 있습니다. 지정하지 않으면 기본 Azure Integration Runtime을 사용합니다. |예 |

>[!NOTE]
>서비스 주체 인증은 "AzureBlobStorage" 유형 연결된 서비스에서만 지원되고, 이전의 "AzureStorage" 유형 연결된 서비스에서는 지원되지 않습니다.

**예제:**

```json
{
    "name": "AzureBlobStorageLinkedService",
    "properties": {
        "type": "AzureBlobStorage",
        "typeProperties": {            
            "serviceEndpoint": "https://<accountName>.blob.core.windows.net/",
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalKey": {
                "type": "SecureString",
                "value": "<service principal key>"
            },
            "tenant": "<tenant info, e.g. microsoft.onmicrosoft.com>" 
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="managed-identities-for-azure-resources-authentication"></a><a name="managed-identity"></a>Azure 리소스 인증에 대한 관리되는 ID

특정 데이터 팩터리를 나타내는 [Azure 리소스용 관리 ID](data-factory-service-identity.md)와 데이터 팩터리를 연결할 수 있습니다. 이 관리되는 ID를 자신의 서비스 주체를 사용하는 것과 유사한 Blob 저장소 인증에 직접 사용할 수 있습니다. 이렇게 하면 이 지정된 팩터리에서 Blob Storage 간에 데이터에 액세스하고 복사할 수 있습니다.

일반적으로 Azure Storage 인증을 위해 [Azure Active Directory를 사용하여 Azure Storage에 대한 액세스 인증](../storage/common/storage-auth-aad.md)을 참조하세요. Azure 리소스 인증을 위해 관리 ID를 사용하려면 다음 단계를 따릅니다.

1. 팩터리와 함께 생성된 **관리ID 개체 ID** 값을 복사하여 [데이터 팩터리 관리 ID 정보를 검색합니다.](data-factory-service-identity.md#retrieve-managed-identity)

2. Azure Blob Storage에서 적절한 사용 권한을 관리 ID에 부여합니다. 역할에 대한 자세한 내용은 [RBAC를 사용하여 Azure Storage 데이터에 대한 액세스 권한 관리](../storage/common/storage-auth-aad-rbac.md)를 참조하세요.

    - **원본으로**의 액세스 제어(IAM)에서 최소한 **Storage Blob 데이터 읽기 권한자** 역할을 부여합니다.
    - **싱크의 경우** 액세스 제어(IAM)에서 **Storage Blob 데이터 기여자** 역할 이상을 부여합니다.

>[!IMPORTANT]
>PolyBase를 사용하여 Blob의 데이터를 SQL 데이터 웨어하우스에 로드하는 경우 Blob에 대한 관리되는 ID 인증을 사용하는 경우 [이 가이드에서](../sql-database/sql-database-vnet-service-endpoint-rule-overview.md#impact-of-using-vnet-service-endpoints-with-azure-storage) 1단계와 2단계를 수행하여 Azure Active Directory(Azure AD)를 사용하여 SQL Database 서버를 등록하고 2) SQL Database 서버에 저장소 Blob 데이터 기여자 역할을 할당합니다. 나머지는 데이터 팩터리에서 처리합니다. Blob 저장소가 Azure 가상 네트워크 끝점으로 구성된 경우 PolyBase를 사용하여 데이터를 로드하려면 PolyBase에서 필요에 따라 관리되는 ID 인증을 사용해야 합니다.

Azure Blob Storage 연결된 서비스에 지원되는 속성은 다음과 같습니다.

| 속성 | 설명 | 필수 |
|:--- |:--- |:--- |
| type | type 속성은 **AzureBlobStorage**로 설정해야 합니다. |예 |
| serviceEndpoint | 패턴이 `https://<accountName>.blob.core.windows.net/`인 Azure Blob Storage 서비스 엔드포인트를 지정합니다. |예 |
| connectVia | 데이터 저장소에 연결하는 데 사용할 [통합 런타임](concepts-integration-runtime.md)입니다. Azure Integration Runtime 또는 자체 호스팅 Integration Runtime(데이터 저장소가 사설망에 있는 경우)을 사용할 수 있습니다. 지정하지 않으면 기본 Azure Integration Runtime을 사용합니다. |예 |

> [!NOTE]
> Azure 리소스 인증을 위한 관리 ID는 "AzureBlobStorage" 유형 연결된 서비스에서만 지원되고, 이전의 "AzureStorage" 유형 연결된 서비스에서는 지원되지 않습니다. 

**예제:**

```json
{
    "name": "AzureBlobStorageLinkedService",
    "properties": {
        "type": "AzureBlobStorage",
        "typeProperties": {            
            "serviceEndpoint": "https://<accountName>.blob.core.windows.net/"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>데이터 세트 속성

데이터 세트 정의에 사용할 수 있는 섹션 및 속성의 전체 목록은 [데이터 세트](concepts-datasets-linked-services.md) 문서를 참조하세요. 

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

형식 기반 데이터 집합의 설정에서 `location` Azure Blob에 대해 다음 속성이 지원됩니다.

| 속성   | 설명                                                  | 필수 |
| ---------- | ------------------------------------------------------------ | -------- |
| type       | 데이터 집합에서 위치의 형식 속성을 **AzureBlobStorageLocation**로 설정해야 합니다. | 예      |
| container  | Blob 컨테이너입니다.                                          | 예      |
| folderPath | 지정된 컨테이너 아래의 폴더 경로입니다. 와일드카드를 사용하여 폴더를 필터링하려면 이 설정을 건너뛰고 활동 소스 설정에서 지정합니다. | 예       |
| fileName   | 지정된 컨테이너 + folderPath 아래의 파일 이름입니다. 와일드카드를 사용하여 파일을 필터링하려면 이 설정을 건너뛰고 활동 소스 설정에서 지정합니다. | 예       |

**예제:**

```json
{
    "name": "DelimitedTextDataset",
    "properties": {
        "type": "DelimitedText",
        "linkedServiceName": {
            "referenceName": "<Azure Blob Storage linked service name>",
            "type": "LinkedServiceReference"
        },
        "schema": [ < physical schema, optional, auto retrieved during authoring > ],
        "typeProperties": {
            "location": {
                "type": "AzureBlobStorageLocation",
                "container": "containername",
                "folderPath": "folder/subfolder"
            },
            "columnDelimiter": ",",
            "quoteChar": "\"",
            "firstRowAsHeader": true,
            "compressionCodec": "gzip"
        }
    }
}
```

## <a name="copy-activity-properties"></a>복사 작업 속성

작업 정의에 사용할 수 있는 섹션 및 속성의 전체 목록은 [파이프라인](concepts-pipelines-activities.md) 문서를 참조하세요. 이 섹션에서는 Blob Storage 원본 및 싱크에서 지원되는 속성 목록을 제공합니다.

### <a name="blob-storage-as-a-source-type"></a>Blob Storage를 원본 형식으로

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

형식 기반 복사 원본의 설정에서 `storeSettings` Azure Blob에 대해 다음 속성이 지원됩니다.

| 속성                 | 설명                                                  | 필수                                      |
| ------------------------ | ------------------------------------------------------------ | --------------------------------------------- |
| type                     | 아래의 `storeSettings` 형식 속성은 **AzureBlobStorageReadSettings로**설정해야 합니다. | 예                                           |
| recursive                | 하위 폴더 또는 지정된 폴더에서만 데이터를 재귀적으로 읽을지 여부를 나타냅니다. recursive를 true로 설정하고 싱크가 파일 기반 저장소인 경우 빈 폴더 또는 하위 폴더가 싱크에 복사되거나 만들어지지 않습니다. 허용된 **true** 값은 true(기본값) 및 **false입니다.** | 예                                            |
| 접두사                   | 데이터 집합에서 원본 Blob을 필터링하도록 구성된 지정된 컨테이너 아래의 Blob 이름에 대한 접두사입니다. 이 접두사로 이름이 시작되는 Blob이 선택됩니다. <br>속성이 `wildcardFileName` `wildcardFolderPath` 지정되지 않은 경우에만 적용됩니다. | 예                                                          |
| 와일드 카드폴더패스       | 원본 폴더를 필터링하기 위해 데이터 집합에 구성된 지정된 컨테이너 아래에 와일드카드 문자가 있는 폴더 경로입니다. <br>허용되는 와일드카드는 `*`(0개 이상의 문자 일치) 및 `?`(0-1개의 문자 일치)입니다. 실제 폴더 이름에 와일드카드 또는 이 이스케이프 문자가 있는 경우 `^`을 사용하여 이스케이프합니다. <br>더 많은 예는 [폴더 및 파일 필터 예제](#folder-and-file-filter-examples)를 참조하세요. | 예                                            |
| 와일드카드파일이름         | 지정된 컨테이너 아래에 와일드카드 문자가 있는 파일 이름 + folderPath/와일드카드FolderPath를 사용하여 소스 파일을 필터링합니다. <br>허용되는 와일드카드는 `*`(0개 이상의 문자 일치) 및 `?`(0-1개의 문자 일치)입니다. 실제 폴더 이름에 와일드카드 또는 이 이스케이프 문자가 있는 경우 `^`을 사용하여 이스케이프합니다.  더 많은 예는 [폴더 및 파일 필터 예제](#folder-and-file-filter-examples)를 참조하세요. | 데이터 `fileName` 집합에 지정되지 않은 경우 예 |
| modifiedDatetimeStart    | 파일은 특성: 마지막으로 수정된 특성을 기반으로 필터링합니다. 마지막 수정 시간이 `modifiedDatetimeStart`와 `modifiedDatetimeEnd` 사이의 시간 범위 내에 있으면 파일이 선택됩니다. 시간은 UTC 표준 시간대에 "2018-12-01T05:00:00Z" 형식으로 적용됩니다. <br> 속성은 NULL일 수 있습니다. 이 경우 파일 특성 필터가 데이터 세트에 적용되지 않습니다.  `modifiedDatetimeStart`에 datetime 값이 있지만 `modifiedDatetimeEnd`가 NULL이면, 마지막으로 수정된 특성이 datetime 값보다 크거나 같은 파일이 선택됩니다.  `modifiedDatetimeEnd`에 datetime 값이 있지만 `modifiedDatetimeStart`가 NULL이면, 마지막으로 수정된 특성이 datetime 값보다 작은 파일이 선택됩니다. | 예                                            |
| modifiedDatetimeEnd      | 위와 동일합니다.                                               | 예                                            |
| maxConcurrentConnections | 저장소 저장소에 동시에 연결할 연결 수입니다. 데이터 저장소에 대한 동시 연결을 제한하려는 경우에만 지정합니다. | 예                                            |

> [!NOTE]
> Parquet/구분된 텍스트 형식의 경우 다음 섹션에 언급된 **BlobSource** 형식 복사 활동 소스는 이전 버전과의 호환성을 위해 있는 것처럼 계속 지원됩니다. 앞으로 이 새 모델을 사용하는 것이 좋습니다.

**예제:**

```json
"activities":[
    {
        "name": "CopyFromBlob",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Delimited text input dataset name>",
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
                "type": "DelimitedTextSource",
                "formatSettings":{
                    "type": "DelimitedTextReadSettings",
                    "skipLineCount": 10
                },
                "storeSettings":{
                    "type": "AzureBlobStorageReadSettings",
                    "recursive": true,
                    "wildcardFolderPath": "myfolder*A",
                    "wildcardFileName": "*.csv"
                }
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="blob-storage-as-a-sink-type"></a>Blob Storage를 싱크 형식으로

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

형식 기반 복사 싱크의 설정에서 `storeSettings` Azure Blob에 대해 다음 속성이 지원됩니다.

| 속성                 | 설명                                                  | 필수 |
| ------------------------ | ------------------------------------------------------------ | -------- |
| type                     | 아래의 `storeSettings` 형식 속성은 **AzureBlobStorageWriteSettings로**설정해야 합니다. | 예      |
| copyBehavior             | 원본이 파일 기반 데이터 저장소의 파일인 경우 복사 동작을 정의합니다.<br/><br/>허용된 값은<br/><b>- PreserveHierarchy(기본값)</b>: 대상 폴더에서 파일 계층 구조를 유지합니다. 원본 폴더의 원본 파일 상대 경로는 대상 폴더의 대상 파일 상대 경로와 동일합니다.<br/><b>- 병합 계층 :</b>소스 폴더의 모든 파일은 대상 폴더의 첫 번째 수준에 있습니다. 대상 파일은 자동 생성된 이름을 갖습니다. <br/><b>- MergeFiles</b>: 원본 폴더의 모든 파일을 하나의 파일로 병합합니다. 병합되는 파일 이름은 지정된 파일 또는 Blob 이름이 적용됩니다. 그렇지 않으면 자동 생성되는 파일 이름이 적용됩니다. | 예       |
| 블록크기인MB | Blob을 차단하는 데이터를 작성하는 데 사용되는 MB의 블록 크기를 지정합니다. 블록 [Blob에 대해](https://docs.microsoft.com/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs#about-block-blobs)자세히 알아보십시오. <br/>허용값은 **4MB에서 100MB 사이입니다.** <br/>기본적으로 ADF는 원본 저장소 유형 및 데이터에 따라 블록 크기를 자동으로 결정합니다. Blob에 이진이 아닌 복사본의 경우 기본 블록 크기는 최대 4.95TB 데이터에 맞게 100MB입니다. 특히 네트워크 부족으로 자체 호스팅 통합 런타임을 사용하면 작업 시간 시간 또는 성능 문제가 발생하는 경우 데이터가 크지 않을 수 있습니다. 블록 크기를 명시적으로 지정할 수 있지만 blockSizeInMB*50000이 데이터를 저장할 만큼 충분히 큰지 확인하면 복사 활동 실행이 실패합니다. | 예 |
| maxConcurrentConnections | 저장소 저장소에 동시에 연결할 연결 수입니다. 데이터 저장소에 대한 동시 연결을 제한하려는 경우에만 지정합니다. | 예       |

**예제:**

```json
"activities":[
    {
        "name": "CopyFromBlob",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Parquet output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "ParquetSink",
                "storeSettings":{
                    "type": "AzureBlobStorageWriteSettings",
                    "copyBehavior": "PreserveHierarchy"
                }
            }
        }
    }
]
```

### <a name="folder-and-file-filter-examples"></a>폴더 및 파일 필터 예제

이 섹션에서는 와일드카드 필터가 있는 폴더 경로 및 파일 이름의 결과 동작에 대해 설명합니다.

| folderPath | fileName | recursive | 소스 폴더 구조 및 필터 **결과(굵게 표시된** 파일이 검색됩니다)|
|:--- |:--- |:--- |:--- |
| `container/Folder*` | (비어 있음, 기본값 사용) | false | container<br/>&nbsp;&nbsp;&nbsp;&nbsp;FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `container/Folder*` | (비어 있음, 기본값 사용) | true | container<br/>&nbsp;&nbsp;&nbsp;&nbsp;FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**파일3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File4.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `container/Folder*` | `*.csv` | false | container<br/>&nbsp;&nbsp;&nbsp;&nbsp;FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `container/Folder*` | `*.csv` | true | container<br/>&nbsp;&nbsp;&nbsp;&nbsp;FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**파일3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |

### <a name="some-recursive-and-copybehavior-examples"></a>일부 recursive 및 copyBehavior 예제

이 섹션에서는 다양한 recursive 및 copyBehavior 값 조합에 대한 복사 작업의 결과 동작을 설명합니다.

| recursive | copyBehavior | 원본 폴더 구조 | 결과 대상 |
|:--- |:--- |:--- |:--- |
| true |preserveHierarchy | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | Folder1 대상 폴더가 다음과 같이 원본 폴더와 동일한 구조로 만들어집니다.<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 |
| true |flattenHierarchy | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | Folder1 대상 폴더가 다음과 같은 구조로 만들어집니다. <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1에 대해 자동 생성된 이름<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2에 대해 자동 생성된 이름<br/>&nbsp;&nbsp;&nbsp;&nbsp;File3에 대해 자동 생성된 이름<br/>&nbsp;&nbsp;&nbsp;&nbsp;File4에 대해 자동 생성된 이름<br/>&nbsp;&nbsp;&nbsp;&nbsp;File5에 대해 자동 생성된 이름 |
| true |mergeFiles | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | Folder1 대상 폴더가 다음과 같은 구조로 만들어집니다. <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1, File2, File3, File4 및 File5의 내용이 자동 생성된 파일 이름이 있는 하나의 파일로 병합됩니다. |
| false |preserveHierarchy | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | Folder1 대상 폴더가 다음 구조로 만들어집니다. <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/><br/>File3, File4, File5를 포함한 Subfolder1은 선택되지 않습니다. |
| false |flattenHierarchy | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | Folder1 대상 폴더가 다음 구조로 만들어집니다. <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1에 대해 자동 생성된 이름<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2에 대해 자동 생성된 이름<br/><br/>File3, File4, File5를 포함한 Subfolder1은 선택되지 않습니다. |
| false |mergeFiles | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | Folder1 대상 폴더가 다음과 같은 구조로 만들어집니다.<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1과 File2의 내용이 자동 생성된 파일 이름이 있는 하나의 파일로 병합됩니다. File1에 대해 자동 생성된 이름<br/><br/>File3, File4, File5를 포함한 Subfolder1은 선택되지 않습니다. |

## <a name="preserve-metadata-during-copy"></a>복사 하는 동안 메타데이터 보존

Amazon S3/Azure Blob/Azure Data Lake 저장소 Gen2에서 Azure 데이터 레이크 저장소 Gen2/Azure Blob에 파일을 복사하는 경우 데이터와 함께 파일 메타데이터를 보존하도록 선택할 수 있습니다. [메타데이터 보존에서](copy-activity-preserve-metadata.md#preserve-metadata)자세히 알아보십시오.

## <a name="mapping-data-flow-properties"></a>데이터 흐름 속성 매핑

매핑 데이터 흐름에서 데이터를 변환할 때 JSON, Avro, 구분 된 텍스트 또는 마루 형식으로 Azure Blob 저장소에서 파일을 읽고 쓸 수 있습니다. 자세한 내용은 매핑 데이터 흐름 기능의 [소스 변환](data-flow-source.md) 및 [싱크 변환을](data-flow-sink.md) 참조하십시오.

### <a name="source-transformation"></a>소스 변환

원본 변환에서 Azure Blob Storage의 컨테이너, 폴더 또는 개별 파일을 읽을 수 있습니다. **소스 옵션** 탭을 사용하면 파일을 읽는 방법을 관리할 수 있습니다. 

![소스 옵션](media/data-flow/sourceOptions1.png "소스 옵션")

**와일드카드 경로:** 와일드카드 패턴을 사용하면 ADF가 단일 소스 변환에서 일치하는 각 폴더와 파일을 반복하도록 지시합니다. 이는 단일 흐름 내에서 여러 파일을 처리하는 효과적인 방법입니다. 기존 와일드카드 패턴 위로 마우스를 가져간 경우 나타나는 + 기호와 함께 여러 와일드카드 일치 패턴을 추가합니다.

원본 컨테이너에서 패턴과 일치하는 일련의 파일을 선택합니다. 데이터 집합에 컨테이너만 지정할 수 있습니다. 따라서 와일드카드 경로에는 루트 폴더의 폴더 경로도 포함되어야 합니다.

와일드카드 예:

* ```*```모든 문자 집합을 나타냅니다.
* ```**```재귀 디렉터리 중첩을 나타냅니다.
* ```?```한 문자 를 대체합니다.
* ```[]```대괄호에 있는 더 많은 문자 중 하나와 일치합니다.

* ```/data/sales/**/*.csv```/데이터/판매에서 모든 CSV 파일을 가져옵니다.
* ```/data/sales/20??/**/```20 세기의 모든 파일을 가져옵니다.
* ```/data/sales/*/*/*.csv```/데이터/판매에서 CSV 파일 2단계 가져옵니다.
* ```/data/sales/2004/*/12/[XY]1?.csv```두 자리 숫자로 접두사 X 또는 Y로 시작하여 12 월에 2004 년에 모든 csv 파일을 가져옵니다.

**파티션 루트 경로:** ```key=value``` 파일 소스에 형식(예: year=2019)으로 폴더를 분할한 경우 해당 파티션 폴더 트리의 최상위 수준을 데이터 흐름 데이터 스트림의 열 이름에 할당할 수 있습니다.

먼저 분할된 폴더인 모든 경로와 읽으려는 리프 파일을 포함하도록 와일드카드를 설정합니다.

![파티션 소스 파일 설정](media/data-flow/partfile2.png "파티션 파일 설정")

파티션 루트 경로 설정을 사용하여 폴더 구조의 최상위 수준을 정의합니다. 데이터 미리 보기를 통해 데이터 내용을 볼 때 ADF가 각 폴더 수준에 있는 해결된 파티션을 추가하는 것을 볼 수 있습니다.

![파티션 루트 경로](media/data-flow/partfile1.png "파티션 루트 경로 미리 보기")

**파일 목록:** 파일 집합입니다. 처리할 상대 경로 파일 목록이 포함된 텍스트 파일을 만듭니다. 이 텍스트 파일을 가리킵니다.

**파일 이름을 저장할 열:** 원본 파일의 이름을 데이터의 열에 저장합니다. 파일 이름 문자열을 저장하려면 여기에 새 열 이름을 입력합니다.

**완료 후:** 데이터 흐름이 실행된 후 원본 파일로 아무 작업을 수행하지 않거나 원본 파일을 삭제하거나 원본 파일을 이동하도록 선택합니다. 이동 경로는 상대적입니다.

소스 파일을 사후 처리 후 다른 위치로 이동하려면 먼저 파일 작업을 위해 "이동"을 선택합니다. 그런 다음 "from" 디렉토리를 설정합니다. 경로에 와일드카드를 사용하지 않는 경우 "from" 설정은 원본 폴더와 동일한 폴더가 됩니다.

와일드카드가 있는 원본 경로가 있는 경우 구문은 다음과 같습니다.

```/data/sales/20??/**/*.csv```

"from"를

```/data/sales```

그리고 "에"로

```/backup/priorSales```

이 경우 /data/sales에서 소스된 모든 파일은 /backup/priorSales로 이동됩니다.

> [!NOTE]
> 파일 작업은 파이프라인에서 데이터 흐름 실행 작업을 사용하는 파이프라인 실행(파이프라인 디버그 또는 실행 실행)에서 데이터 흐름을 시작할 때만 실행됩니다. 파일 작업은 데이터 흐름 디버그 모드에서 *실행되지 않습니다.*

**마지막으로 수정한 필터:** 마지막으로 수정된 날짜 범위를 지정하여 처리하는 파일을 필터링할 수 있습니다. 모든 날짜 시간은 UTC에 있습니다. 

### <a name="sink-properties"></a>싱크 속성

싱크 변환에서 Azure Blob Storage의 컨테이너 또는 폴더에 쓸 수 있습니다. **설정** 탭을 사용하면 파일작성 방법을 관리할 수 있습니다.

![싱크 옵션](media/data-flow/file-sink-settings.png "싱크 옵션")

**폴더 지우기:** 데이터를 작성하기 전에 대상 폴더의 지우기 여부를 결정합니다.

**파일 이름 옵션:** 대상 폴더에서 대상 파일의 이름을 지정하는 방법을 결정합니다. 파일 이름 옵션은 다음과 같습니다.
   * **기본값**: 스파크는 PART 기본값을 기반으로 파일의 이름을 지정할 수 있도록 허용합니다.
   * **패턴**: 파티션당 출력 파일을 열거하는 패턴을 입력합니다. 예를 들어 **대출[n].csv는** loans1.csv, loans2.csv 등을 생성합니다.
   * **파티션당 : 파티션당**하나의 파일 이름을 입력합니다.
   * **열의 데이터로**: 출력 파일을 열 값으로 설정합니다. 경로는 대상 폴더가 아닌 데이터 집합 컨테이너를 기준으로 합니다. 데이터 집합에 폴더 경로가 있으면 재정의됩니다.
   * **단일 파일에 출력**: 분할된 출력 파일을 하나의 명명된 파일로 결합합니다. 경로는 데이터 집합 폴더를 기준으로 합니다. te 병합 작업은 노드 크기에 따라 실패할 수 있습니다. 이 옵션은 큰 데이터 집합에는 권장되지 않습니다.

**모두 인용:** 따옴표의 모든 값을 둘러싸는지 여부를 결정합니다.

## <a name="lookup-activity-properties"></a>조회 활동 속성

속성에 대한 자세한 내용을 보려면 [조회 활동을](control-flow-lookup-activity.md)선택합니다.

## <a name="getmetadata-activity-properties"></a>GetMetadata 활동 속성

속성에 대한 자세한 내용을 보려면 [GetMetadata 활동을](control-flow-get-metadata-activity.md) 선택합니다. 

## <a name="delete-activity-properties"></a>활동 속성 삭제

속성에 대한 자세한 내용을 보려면 [활동 삭제를](delete-activity.md) 선택합니다.

## <a name="legacy-models"></a>레거시 모델

>[!NOTE]
>다음 모델은 이전 버전과의 호환성을 위해 있는 것처럼 계속 지원됩니다. 위의 섹션에서 언급한 새 모델을 사용하는 것이 좋습니다.

### <a name="legacy-dataset-model"></a>레거시 데이터 집합 모델

| 속성 | 설명 | 필수 |
|:--- |:--- |:--- |
| type | 데이터 집합의 형식 속성은 **AzureBlob**로 설정해야 합니다. |예 |
| folderPath | Blob Storage에서 컨테이너 및 폴더에 대한 경로입니다. <br/><br/>와일드카드 필터가 컨테이너 이름을 제외한 경로에 지원됩니다. 허용되는 와일드카드는 `*`(0개 이상의 문자 일치) 및 `?`(0-1개의 문자 일치)입니다. 실제 폴더 이름에 와일드카드 또는 이 이스케이프 문자가 있는 경우 `^`을 사용하여 이스케이프합니다. <br/><br/>예: myblobcontainer/myblobfolder/(더 많은 예는 [폴더 및 파일 필터 예제](#folder-and-file-filter-examples) 참조) |Copy/Lookup 활동의 경우 예, GetMetadata 활동의 경우 아니요 |
| fileName | 지정된 "folderPath" 아래의 Blob에 대한 **이름 또는 와일드 카드 필터**입니다. 이 속성의 값을 지정하지 않으면 데이터 세트는 폴더에 있는 모든 Blob을 가리킵니다. <br/><br/>필터에 허용되는 와일드카드는 `*`(문자 0자 이상 일치) 및 `?`(문자 0자 또는 1자 일치)입니다.<br/>- 예 1: `"fileName": "*.csv"`<br/>- 예 2: `"fileName": "???20180427.txt"`<br/>`^`을 사용하여 실제 파일 이름 내에 와일드카드 또는 이 이스케이프 문자가 있는 경우 이스케이프합니다.<br/><br/>fileName 출력 데이터 집합에 대 한 지정 되지 않은 및 **preserveHierarchy** 활동 싱크에 지정 되지 않은 경우 복사 활동은 자동으로 다음 패턴으로 Blob 이름을 생성 합니다.* 활동 실행 ID GUID]. [지침 이면 병합]. [구성된 경우 형식]을 지정합니다. [압축 구성하는 경우]*"예를 들어, "Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt.gz"; 쿼리 대신 테이블 이름을 사용하여 테이블 형식의 원본에서 복사하는 경우 이름 패턴은 *"[테이블 이름]입니다.] 형식]을 지정합니다. [압축 구성 하는 경우]*", 예를 들어 "MyTable.csv". |예 |
| modifiedDatetimeStart | 파일은 특성: 마지막으로 수정된 특성을 기반으로 필터링합니다. 마지막 수정 시간이 `modifiedDatetimeStart`와 `modifiedDatetimeEnd` 사이의 시간 범위 내에 있으면 파일이 선택됩니다. 시간은 UTC 표준 시간대에 "2018-12-01T05:00:00Z" 형식으로 적용됩니다. <br/><br/> 방대한 양의 파일에서 파일 필터를 수행하려는 경우 이 설정을 사용하도록 설정하면 데이터 이동의 전반적인 성능이 영향을 받을 수 있습니다. <br/><br/> 속성은 NULL일 수 있으며 이는 데이터 집합에 파일 특성 필터가 적용되지 않는다는 의미입니다.  `modifiedDatetimeStart`에 datetime 값이 있지만 `modifiedDatetimeEnd`가 NULL이면, 마지막으로 수정된 특성이 datetime 값보다 크거나 같은 파일이 선택됩니다.  `modifiedDatetimeEnd`에 datetime 값이 있지만 `modifiedDatetimeStart`가 NULL이면, 마지막으로 수정된 특성이 datetime 값보다 작은 파일이 선택됩니다.| 예 |
| modifiedDatetimeEnd | 파일은 특성: 마지막으로 수정된 특성을 기반으로 필터링합니다. 마지막 수정 시간이 `modifiedDatetimeStart`와 `modifiedDatetimeEnd` 사이의 시간 범위 내에 있으면 파일이 선택됩니다. 시간은 UTC 표준 시간대에 "2018-12-01T05:00:00Z" 형식으로 적용됩니다. <br/><br/> 방대한 양의 파일에서 파일 필터를 수행하려는 경우 이 설정을 사용하도록 설정하면 데이터 이동의 전반적인 성능이 영향을 받을 수 있습니다. <br/><br/> 속성은 NULL일 수 있으며 이는 데이터 집합에 파일 특성 필터가 적용되지 않는다는 의미입니다.  `modifiedDatetimeStart`에 datetime 값이 있지만 `modifiedDatetimeEnd`가 NULL이면, 마지막으로 수정된 특성이 datetime 값보다 크거나 같은 파일이 선택됩니다.  `modifiedDatetimeEnd`에 datetime 값이 있지만 `modifiedDatetimeStart`가 NULL이면, 마지막으로 수정된 특성이 datetime 값보다 작은 파일이 선택됩니다.| 예 |
| format | 파일 기반 저장소(이진 복사본) 간에 파일을 있는 그대로 복사하려는 경우 입력 및 출력 데이터 세트 정의 둘 다에서 format 섹션을 건너뜁니다.<br/><br/>특정 형식으로 파일을 구문 분석하거나 생성하려면 **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat** 및 **ParquetFormat** 파일 형식 유형이 지원됩니다. **format**의 **type** 속성을 이 값 중 하나로 설정합니다. 자세한 내용은 [텍스트 형식](supported-file-formats-and-compression-codecs-legacy.md#text-format), [JSON 형식](supported-file-formats-and-compression-codecs-legacy.md#json-format), [Avro 형식](supported-file-formats-and-compression-codecs-legacy.md#avro-format), [Orc 형식](supported-file-formats-and-compression-codecs-legacy.md#orc-format) 및 [Parquet 형식](supported-file-formats-and-compression-codecs-legacy.md#parquet-format) 섹션을 참조하세요. |아니요(이진 복사 시나리오에만 해당) |
| 압축 | 데이터에 대한 압축 유형 및 수준을 지정합니다. 자세한 내용은 [지원되는 파일 형식 및 압축 코덱](supported-file-formats-and-compression-codecs-legacy.md#compression-support)을 참조하세요.<br/>지원되는 형식은 **GZip**, **Deflate**, **BZip2** 및 **ZipDeflate**입니다.<br/>**Optimal** 및 **Fastest** 수준이 지원됩니다. |예 |

>[!TIP]
>폴더 아래에서 모든 Blob을 복사하려면 **folderPath**만을 지정합니다.<br>지정된 이름의 단일 Blob을 복사하려면 폴더 부분으로 **folderPath** 및 파일 이름으로 **fileName**을 지정합니다.<br>폴더 아래에서 Blob의 하위 집합을 복사하려면 폴더 부분으로 **folderPath** 및 와일드카드 필터로 **fileName**을 지정합니다. 

**예제:**

```json
{
    "name": "AzureBlobDataset",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": {
            "referenceName": "<Azure Blob storage linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "folderPath": "mycontainer/myfolder",
            "fileName": "*",
            "modifiedDatetimeStart": "2018-12-01T05:00:00Z",
            "modifiedDatetimeEnd": "2018-12-01T06:00:00Z",
            "format": {
                "type": "TextFormat",
                "columnDelimiter": ",",
                "rowDelimiter": "\n"
            },
            "compression": {
                "type": "GZip",
                "level": "Optimal"
            }
        }
    }
}
```

### <a name="legacy-copy-activity-source-model"></a>레거시 복사 활동 소스 모델

| 속성 | 설명 | 필수 |
|:--- |:--- |:--- |
| type | 복사 활동 소스의 형식 속성은 **BlobSource**로 설정해야 합니다. |예 |
| recursive | 하위 폴더 또는 지정된 폴더에서만 데이터를 재귀적으로 읽을지 여부를 나타냅니다. recursive를 true로 설정하고 싱크가 파일 기반 저장소인 경우 빈 폴더 또는 하위 폴더가 싱크에 복사되거나 만들어지지 않습니다.<br/>허용된 **true** 값은 true(기본값) 및 **false입니다.** | 예 |
| maxConcurrentConnections | 저장소 저장소에 동시에 연결할 연결 수입니다. 데이터 저장소에 대한 동시 연결을 제한하려는 경우에만 지정합니다. | 예 |

**예제:**

```json
"activities":[
    {
        "name": "CopyFromBlob",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Azure Blob input dataset name>",
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
                "type": "BlobSource",
                "recursive": true
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="legacy-copy-activity-sink-model"></a>레거시 복사 활동 싱크 모델

| 속성 | 설명 | 필수 |
|:--- |:--- |:--- |
| type | 복사 활동 싱크의 형식 속성은 **BlobSink로**설정해야 합니다. |예 |
| copyBehavior | 원본이 파일 기반 데이터 저장소의 파일인 경우 복사 동작을 정의합니다.<br/><br/>허용된 값은<br/><b>- PreserveHierarchy(기본값)</b>: 대상 폴더에서 파일 계층 구조를 유지합니다. 원본 폴더의 원본 파일 상대 경로는 대상 폴더의 대상 파일 상대 경로와 동일합니다.<br/><b>- 병합 계층 :</b>소스 폴더의 모든 파일은 대상 폴더의 첫 번째 수준에 있습니다. 대상 파일은 자동 생성된 이름을 갖습니다. <br/><b>- MergeFiles</b>: 원본 폴더의 모든 파일을 하나의 파일로 병합합니다. 병합되는 파일 이름은 지정된 파일 또는 Blob 이름이 적용됩니다. 그렇지 않으면 자동 생성되는 파일 이름이 적용됩니다. | 예 |
| maxConcurrentConnections | 저장소 저장소에 동시에 연결할 연결 수입니다. 데이터 저장소에 대한 동시 연결을 제한하려는 경우에만 지정합니다. | 예 |

**예제:**

```json
"activities":[
    {
        "name": "CopyToBlob",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Azure Blob output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "BlobSink",
                "copyBehavior": "PreserveHierarchy"
            }
        }
    }
]
```

## <a name="next-steps"></a>다음 단계

Data Factory에서 복사 활동을 통해 원본 및 싱크로 지원되는 데이터 저장소의 목록은 [지원되는 데이터 저장소](copy-activity-overview.md#supported-data-stores-and-formats)를 참조하세요.
