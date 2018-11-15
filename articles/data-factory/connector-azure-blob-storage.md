---
title: Data Factory를 사용하여 Azure Blob 저장소 간 데이터 복사 | Microsoft Docs
description: Data Factory를 사용하여 지원되는 원본 데이터 저장소에서 Azure Blob 저장소로 데이터를 복사하거나, Blob 저장소에서 지원되는 싱크 데이터 저장소로 데이터를 복사하는 방법을 알아봅니다.
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 10/31/2018
ms.author: jingwang
ms.openlocfilehash: 83be53edf240220726639b51381b487c5b742cee
ms.sourcegitcommit: 3dcb1a3993e51963954194ba2a5e42260d0be258
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/01/2018
ms.locfileid: "50754089"
---
# <a name="copy-data-to-or-from-azure-blob-storage-by-using-azure-data-factory"></a>Azure Data Factory를 사용하여 Azure Blob 저장소 간 데이터 복사
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [버전 1](v1/data-factory-azure-blob-connector.md)
> * [현재 버전](connector-azure-blob-storage.md)

이 문서에서는 Azure Data Factory에서 복사 활동을 사용하여 Azure Blob 저장소 간에 데이터를 복사하는 방법에 대해 설명합니다. 이 문서는 복사 작업에 대한 일반적인 개요를 제공하는 [복사 작업 개요](copy-activity-overview.md) 문서를 기반으로 합니다.

Azure Data Factory에 대해 자세히 알아보려면 [소개 문서](introduction.md)를 참조하세요.

## <a name="supported-capabilities"></a>지원되는 기능

지원되는 모든 원본 데이터 저장소의 데이터를 Blob 저장소에 복사할 수 있습니다. 또한 Blob 저장소의 데이터를 지원되는 모든 싱크 데이터 저장소에 복사할 수 있습니다. 복사 작업의 원본 또는 싱크로 지원되는 데이터 저장소 목록은 [지원되는 데이터 저장소](copy-activity-overview.md) 표를 참조하세요.

특히 이 Blob 저장소 커넥터는 다음을 지원합니다.

- 범용 Azure 저장소 계정 및 핫/쿨 Blob 저장소 간에 Blob을 복사합니다. 
- Azure 리소스 인증을 위해 계정 키, 서비스 공유 액세스 서명, 서비스 주체 또는 관리 ID를 사용하여 Blob을 복사합니다.
- 블록, 추가 또는 페이지 Blob에서 Blob을 복사하고, 블록 Blob에만 데이터를 복사합니다. Azure Premium Storage는 페이지 Blob으로 지원되므로 싱크로는 지원되지 않습니다.
- Blob을 있는 그대로 복사하거나 [지원되는 파일 형식 및 압축 코덱](supported-file-formats-and-compression-codecs.md)을 사용하여 Blob을 구문 분석하거나 생성합니다.

## <a name="get-started"></a>시작하기

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

다음 섹션에서는 Blob 저장소에 특정한 Data Factory 엔터티를 정의하는 데 사용되는 속성에 대해 자세히 설명합니다.

## <a name="linked-service-properties"></a>연결된 서비스 속성

Azure Blob 커넥터에서 지원하는 인증 유형은 다음과 같습니다. 자세한 내용은 해당 섹션을 참조하세요.

- [계정 키 인증](#account-key-authentication)
- [공유 액세스 서명 인증](#shared-access-signature-authentication)
- [서비스 주체 인증](#service-principal-authentication)
- [Azure 리소스 인증용 관리 ID](#managed-identity)

>[!NOTE]
>HDInsight, Azure Machine Learning 및 Azure SQL Data Warehouse PolyBase 로드는 Azure Blob 저장소 계정 키 인증만 지원합니다.

### <a name="account-key-authentication"></a>계정 키 인증

저장소 계정 키 인증을 사용하는 데 지원되는 속성은 다음과 같습니다.

| 자산 | 설명 | 필수 |
|:--- |:--- |:--- |
| 형식 | type 속성은 **AzureBlobStorage**(권장) 또는 **AzureStorage**로 설정해야 합니다(아래 참고 참조). |yes |
| connectionString | connectionString 속성에 대한 Storage에 연결하는 데 필요한 정보를 지정합니다. 이 필드를 SecureString으로 표시하여 Data Factory에 안전하게 저장하거나 [Azure Key Vault에 저장되는 비밀을 참조](store-credentials-in-key-vault.md)합니다. |yes |
| connectVia | 데이터 저장소에 연결하는 데 사용할 [통합 런타임](concepts-integration-runtime.md)입니다. Azure Integration Runtime 또는 자체 호스팅 Integration Runtime(데이터 저장소가 사설망에 있는 경우)을 사용할 수 있습니다. 지정하지 않으면 기본 Azure Integration Runtime을 사용합니다. |아니요 |

>[!NOTE]
>"AzureStorage" 유형의 연결된 서비스를 사용하는 경우에도 있는 그대로 계속 지원되지만, 앞으로 이 새로운 "AzureBlobStorage" 연결된 서비스 유형을 사용하는 것이 좋습니다.

**예제:**

```json
{
    "name": "AzureBlobStorageLinkedService",
    "properties": {
        "type": "AzureBlobStorage",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
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

공유 액세스 서명은 저장소 계정의 리소스에 대한 위임된 권한을 제공합니다. 공유 액세스 서명을 사용하여 저장소 계정의 개체에 대해 지정된 시간 동안 제한된 권한을 클라이언트에 부여할 수 있습니다. 계정 액세스 키를 공유할 필요가 없습니다. 공유 액세스 서명은 저장소 리소스에 대해 인증된 액세스에 필요한 모든 정보를 쿼리 매개 변수에 포함하는 URI입니다. 공유 액세스 서명을 사용하여 저장소 리소스에 액세스하려면 클라이언트에서 공유 액세스 서명을 해당 생성자 또는 메서드에 전달하기만 하면 됩니다. 공유 액세스 서명에 대한 자세한 내용은 [공유 액세스 서명: 공유 액세스 서명 모델 이해](../storage/common/storage-dotnet-shared-access-signature-part-1.md)를 참조하세요.

> [!NOTE]
>- Data Factory에서 이제 **서비스 공유 액세스 서명**과 **계정 공유 액세스 서명**이 모두 지원됩니다. 이러한 두 유형 및 이를 생성하는 방법에 대한 자세한 내용은 [공유 액세스 서명 유형](../storage/common/storage-dotnet-shared-access-signature-part-1.md#types-of-shared-access-signatures)을 참조하세요.
>- 이후 데이터 집합 구성에서 폴더 경로는 컨테이너 수준에서 시작하는 절대 경로입니다. SAS URI의 경로와 일치하는 경로를 구성해야 합니다.

> [!TIP]
> 저장소 계정에 대한 서비스 공유 액세스 서명을 생성하려면 다음 PowerShell 명령을 실행합니다. 자리 표시자를 바꾸고 필요한 권한을 부여합니다.
> `$context = New-AzureStorageContext -StorageAccountName <accountName> -StorageAccountKey <accountKey>`
> `New-AzureStorageContainerSASToken -Name <containerName> -Context $context -Permission rwdl -StartTime <startTime> -ExpiryTime <endTime> -FullUri`

공유 액세스 서명 인증을 사용하는 데 지원되는 속성은 다음과 같습니다.

| 자산 | 설명 | 필수 |
|:--- |:--- |:--- |
| 형식 | type 속성은 **AzureBlobStorage**(권장) 또는 **AzureStorage**로 설정해야 합니다(아래 참고 참조). |yes |
| sasUri | Blob, 컨테이너 또는 테이블과 같은 Storage 리소스에 대한 공유 액세스 서명 URI를 지정합니다. 이 필드를 SecureString으로 표시하여 Data Factory에 안전하게 저장하거나 [Azure Key Vault에 저장되는 비밀을 참조](store-credentials-in-key-vault.md)합니다. |yes |
| connectVia | 데이터 저장소에 연결하는 데 사용할 [통합 런타임](concepts-integration-runtime.md)입니다. Azure Integration Runtime 또는 자체 호스팅 Integration Runtime(데이터 저장소가 사설망에 있는 경우)을 사용할 수 있습니다. 지정하지 않으면 기본 Azure Integration Runtime을 사용합니다. |아니요 |

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
                "value": "<SAS URI of the Azure Storage resource>"
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
- URI는 필요에 따라 적절한 컨테이너/Blob 또는 테이블 수준에서 만들어야 합니다. Blob에 대한 공유 액세스 서명 URI를 사용하면 Data Factory에서 해당 특정 Blob에 액세스할 수 있습니다. Blob 저장소 컨테이너에 대한 공유 액세스 서명 URI를 사용하면 Data Factory에서 해당 컨테이너의 Blob을 반복할 수 있습니다. 나중에 더 많거나 적은 개체에 대한 액세스를 제공하거나 공유 액세스 서명 URI를 업데이트하려면 연결된 서비스를 새 URI로 업데이트해야 합니다.

### <a name="service-principal-authentication"></a>서비스 주체 인증

Azure Storage 서비스 주체 인증은 일반적으로 [Azure Active Directory를 사용하여 Azure Storage에 대한 액세스 인증](../storage/common/storage-auth-aad.md)을 참조하세요.

서비스 주체 인증을 사용하려면 다음 단계를 수행합니다.

1. [Azure AD 테넌트에 응용 프로그램 등록](../storage/common/storage-auth-aad-app.md#register-your-application-with-an-azure-ad-tenant)에 따라 Azure AD(Azure Active Directory)에 응용 프로그램 항목을 등록합니다. 연결된 서비스를 정의하는 데 사용되므로 다음 값을 적어둡니다.

    - 응용 프로그램 UI
    - 응용 프로그램 키
    - 테넌트 ID

2. Azure Blob 저장소에서 서비스 주체에게 적절한 권한을 부여합니다. 역할에 대한 자세한 내용은 [RBAC를 사용하여 Azure Storage 데이터에 대한 액세스 권한 관리](../storage/common/storage-auth-aad-rbac.md)를 참조하세요.

    - **원본의 경우** 액세스 제어(IAM)에서 **Storage Blob 데이터 읽기 권한자** 역할 이상을 부여합니다.
    - **싱크의 경우** 액세스 제어(IAM)에서 **Storage Blob 데이터 기여자** 역할 이상을 부여합니다.

Azure Blob 저장소 연결된 서비스에 지원되는 속성은 다음과 같습니다.

| 자산 | 설명 | 필수 |
|:--- |:--- |:--- |
| 형식 | type 속성은 **AzureBlobStorage**로 설정해야 합니다. |yes |
| serviceEndpoint | 패턴이 `https://<accountName>.blob.core.windows.net/`인 Azure Blob 저장소 서비스 엔드포인트를 지정합니다. |yes |
| servicePrincipalId | 응용 프로그램의 클라이언트 ID를 지정합니다. | yes |
| servicePrincipalKey | 응용 프로그램의 키를 지정합니다. 이 필드를 **SecureString**으로 표시하여 Data Factory에 안전하게 저장하거나, [Azure Key Vault에 저장된 비밀을 참조](store-credentials-in-key-vault.md)합니다. | yes |
| tenant | 응용 프로그램이 있는 테넌트 정보(도메인 이름 또는 테넌트 ID)를 지정합니다. Azure Portal의 오른쪽 위 모서리를 마우스로 가리켜 검색합니다. | yes |
| connectVia | 데이터 저장소에 연결하는 데 사용할 [통합 런타임](concepts-integration-runtime.md)입니다. Azure Integration Runtime 또는 자체 호스팅 Integration Runtime(데이터 저장소가 사설망에 있는 경우)을 사용할 수 있습니다. 지정하지 않으면 기본 Azure Integration Runtime을 사용합니다. |아니요 |

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

### <a name="managed-identity"></a>Azure 리소스 인증용 관리 ID

특정 데이터 팩터리를 나타내는 [Azure 리소스용 관리 ID](data-factory-service-identity.md)와 데이터 팩터리를 연결할 수 있습니다. 사용자 고유의 서비스 주체를 사용하는 것과 비슷하게 Blob 저장소 인증에 이 서비스 ID를 직접 사용할 수 있습니다. 이렇게 하면 이 지정된 팩터리에서 Blob 저장소 간에 데이터에 액세스하고 복사할 수 있습니다.

Azure Storage MSI 인증은 일반적으로 [Azure Active Directory를 사용하여 Azure Storage에 대한 액세스 인증](../storage/common/storage-auth-aad.md)을 참조하세요.

Azure 리소스 인증을 위해 관리 ID를 사용하려면 다음 단계를 따릅니다.

1. 팩터리와 함께 생성된 "서비스 ID 응용 프로그램 ID"의 값을 복사하여 [데이터 팩터리 서비스 ID를 검색](data-factory-service-identity.md#retrieve-service-identity)합니다.

2. Azure Blob 저장소에서 서비스 주체에게 적절한 권한을 부여합니다. 역할에 대한 자세한 내용은 [RBAC를 사용하여 Azure Storage 데이터에 대한 액세스 권한 관리](../storage/common/storage-auth-aad-rbac.md)를 참조하세요.

    - **원본의 경우** 액세스 제어(IAM)에서 **Storage Blob 데이터 읽기 권한자** 역할 이상을 부여합니다.
    - **싱크의 경우** 액세스 제어(IAM)에서 **Storage Blob 데이터 기여자** 역할 이상을 부여합니다.

Azure Blob 저장소 연결된 서비스에 지원되는 속성은 다음과 같습니다.

| 자산 | 설명 | 필수 |
|:--- |:--- |:--- |
| 형식 | type 속성은 **AzureBlobStorage**로 설정해야 합니다. |yes |
| serviceEndpoint | 패턴이 `https://<accountName>.blob.core.windows.net/`인 Azure Blob 저장소 서비스 엔드포인트를 지정합니다. |yes |
| connectVia | 데이터 저장소에 연결하는 데 사용할 [통합 런타임](concepts-integration-runtime.md)입니다. Azure Integration Runtime 또는 자체 호스팅 Integration Runtime(데이터 저장소가 사설망에 있는 경우)을 사용할 수 있습니다. 지정하지 않으면 기본 Azure Integration Runtime을 사용합니다. |아니요 |

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

## <a name="dataset-properties"></a>데이터 집합 속성

데이터 집합 정의에 사용할 수 있는 섹션 및 속성의 전체 목록은 [데이터 집합](concepts-datasets-linked-services.md) 문서를 참조하세요. 이 섹션에서는 Blob 저장소 데이터 집합에서 지원하는 속성 목록을 제공합니다.

Blob 저장소 간 데이터를 복사하려면 데이터 집합의 type 속성을 **AzureBlob**으로 설정합니다. 다음과 같은 속성이 지원됩니다.

| 자산 | 설명 | 필수 |
|:--- |:--- |:--- |
| 형식 | 데이터 집합의 type 속성은 **AzureBlob**으로 설정해야 합니다. |yes |
| folderPath | blob 저장소에서 컨테이너 및 폴더에 대한 경로입니다. 와일드카드 필터는 지원되지 않습니다. 예를 들어 myblobcontainer/myblobfolder/입니다. |yes |
| fileName | 지정된 "folderPath" 아래의 Blob에 대한 **이름 또는 와일드 카드 필터**입니다. 이 속성의 값을 지정하지 않으면 데이터 집합은 폴더에 있는 모든 Blob을 가리킵니다. <br/><br/>필터에 허용되는 와일드카드는 `*`(문자 0자 이상 일치) 및 `?`(문자 0자 또는 1자 일치)입니다.<br/>- 예 1: `"fileName": "*.csv"`<br/>- 예 2: `"fileName": "???20180427.txt"`<br/>`^`을 사용하여 실제 파일 이름 내에 와일드카드 또는 이 이스케이프 문자가 있는 경우 이스케이프합니다.<br/><br/>fileName이 출력 데이터 집합에 대해 지정되지 않고 **preserveHierarchy**가 활동 싱크에 지정되지 않으면, 복사 활동에서 자동으로 다음 패턴의 Blob 이름을 생성합니다. "*Data.[activity run id GUID].[GUID if FlattenHierarchy].[format if configured].[compression if configured]*" 예: "Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt.gz" |아니요 |
| format | 파일 기반 저장소(이진 복사본) 간에 파일을 있는 그대로 복사하려는 경우 입력 및 출력 데이터 집합 정의 둘 다에서 format 섹션을 건너뜁니다.<br/><br/>특정 형식으로 파일을 구문 분석하거나 생성하려면 **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat** 및 **ParquetFormat** 파일 형식 유형이 지원됩니다. **format**의 **type** 속성을 이 값 중 하나로 설정합니다. 자세한 내용은 [텍스트 형식](supported-file-formats-and-compression-codecs.md#text-format), [JSON 형식](supported-file-formats-and-compression-codecs.md#json-format), [Avro 형식](supported-file-formats-and-compression-codecs.md#avro-format), [Orc 형식](supported-file-formats-and-compression-codecs.md#orc-format) 및 [Parquet 형식](supported-file-formats-and-compression-codecs.md#parquet-format) 섹션을 참조하세요. |아니요(이진 복사 시나리오에만 해당) |
| 압축 | 데이터에 대한 압축 유형 및 수준을 지정합니다. 자세한 내용은 [지원되는 파일 형식 및 압축 코덱](supported-file-formats-and-compression-codecs.md#compression-support)을 참조하세요.<br/>지원되는 형식은 **GZip**, **Deflate**, **BZip2** 및 **ZipDeflate**입니다.<br/>**Optimal** 및 **Fastest** 수준이 지원됩니다. |아니요 |

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
            "fileName": "myfile.csv.gz",
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

## <a name="copy-activity-properties"></a>복사 작업 속성

작업 정의에 사용할 수 있는 섹션 및 속성의 전체 목록은 [파이프라인](concepts-pipelines-activities.md) 문서를 참조하세요. 이 섹션에서는 Blob 저장소 원본 및 싱크에서 지원되는 속성 목록을 제공합니다.

### <a name="blob-storage-as-a-source-type"></a>Blob 저장소를 원본 형식으로

Blob 저장소에서 데이터를 복사하려면 복사 활동의 source 형식을 **BlobSource**로 설정합니다. 복사 작업 **source** 섹션에서 다음 속성이 지원됩니다.

| 자산 | 설명 | 필수 |
|:--- |:--- |:--- |
| 형식 | 복사 활동 source의 type 속성은 **BlobSource**로 설정해야 합니다. |yes |
| recursive | 하위 폴더 또는 지정된 폴더에서만 데이터를 재귀적으로 읽을지 여부를 나타냅니다. recursive를 true로 설정하고 싱크가 파일 기반 저장소인 경우 빈 폴더 또는 하위 폴더가 싱크에 복사되거나 만들어지지 않습니다.<br/>허용되는 값은 **true**(기본값) 및 **false**입니다. | 아니요 |

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

### <a name="blob-storage-as-a-sink-type"></a>Blob 저장소를 싱크 형식으로

Blob 저장소에 데이터를 복사하려면 복사 활동의 sink 형식을 **BlobSink**로 설정합니다. **sink** 섹션에서 지원되는 속성은 다음과 같습니다.

| 자산 | 설명 | 필수 |
|:--- |:--- |:--- |
| 형식 | 복사 활동 sink의 type 속성은 **BlobSink**로 설정해야 합니다. |yes |
| copyBehavior | 원본이 파일 기반 데이터 저장소의 파일인 경우 복사 동작을 정의합니다.<br/><br/>허용되는 값은 다음과 같습니다.<br/><b>- PreserveHierarchy(기본값)</b>: 대상 폴더에서 파일 계층 구조를 유지합니다. 원본 폴더의 원본 파일 상대 경로는 대상 폴더의 대상 파일 상대 경로와 동일합니다.<br/><b>- FlattenHierarchy</b>: 원본 폴더의 모든 파일이 대상 폴더의 첫 번째 수준에 있게 됩니다. 대상 파일은 자동 생성된 이름을 갖습니다. <br/><b>- MergeFiles</b>: 원본 폴더의 모든 파일을 하나의 파일로 병합합니다. 병합되는 파일 이름은 지정된 파일 또는 Blob 이름이 적용됩니다. 그렇지 않으면 자동 생성되는 파일 이름이 적용됩니다. | 아니요 |

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

## <a name="next-steps"></a>다음 단계
Data Factory에서 복사 활동을 통해 원본 및 싱크로 지원되는 데이터 저장소의 목록은 [지원되는 데이터 저장소](copy-activity-overview.md##supported-data-stores-and-formats)를 참조하세요.
