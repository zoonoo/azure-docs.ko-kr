---
title: Data Factory를 사용 하 여 Azure Data Lake Storage Gen2 간에 데이터 복사 Microsoft Docs
description: Azure Data Factory를 사용 하 여 Azure Data Lake Storage Gen2 간에 데이터를 복사 하는 방법에 대해 알아봅니다.
services: data-factory
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: jingwang
ms.openlocfilehash: e21ae2f8eda4521effa5b7db686fe72241aa4cdb
ms.sourcegitcommit: 32242bf7144c98a7d357712e75b1aefcf93a40cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/04/2019
ms.locfileid: "70276276"
---
# <a name="copy-data-to-or-from-azure-data-lake-storage-gen2-using-azure-data-factory"></a>Azure Data Factory를 사용하여 Azure Data Lake Storage Gen2 간에 데이터 복사

Azure Data Lake Storage Gen2 (ADLS Gen2)은 [Azure Blob Storage](../storage/blobs/storage-blobs-introduction.md)에 기본 제공 되는 빅 데이터 분석 전용 기능 집합입니다. 파일 시스템 및 개체 저장소 패러다임을 모두 사용 하 여 데이터와 상호 작용 하는 데 사용할 수 있습니다.

이 문서에서는 Azure Data Lake Storage Gen2 간에 데이터를 복사 하는 방법을 설명 합니다. Azure Data Factory에 대해 자세히 알아보려면 [소개 문서](introduction.md)를 참조하세요.

## <a name="supported-capabilities"></a>지원되는 기능

이 Azure Data Lake Storage Gen2 커넥터는 다음과 같은 작업에 대해 지원 됩니다.

- [지원 되는 원본 또는 싱크 행렬이](copy-activity-overview.md) 포함 된 [복사 작업](copy-activity-overview.md)
- [데이터 흐름 매핑](concepts-data-flow-overview.md)
- [조회 작업](control-flow-lookup-activity.md)
- [GetMetadata 작업](control-flow-get-metadata-activity.md)

특히이 커넥터를 사용 하 여 다음을 수행할 수 있습니다.

- Azure 리소스 인증을 위해 계정 키, 서비스 주체 또는 관리 id를 사용 하 여 데이터를 복사 합니다.
- 파일을 있는 그대로 복사 하거나 [지원 되는 파일 형식 및 압축 코덱을](supported-file-formats-and-compression-codecs.md)사용 하 여 파일을 구문 분석 하거나 생성 합니다.

>[!TIP]
>계층적 네임 스페이스를 사용 하도록 설정 하는 경우 현재는 Blob과 Data Lake Storage Gen2 Api 간의 작업 상호 운용성이 없습니다. "지정 된 파일 시스템이 없습니다." 라는 메시지와 함께 "ErrorCode = FilesystemNotFound" 오류가 발생 하는 경우에는 다른 곳에서 Data Lake Storage Gen2 API 대신 Blob API를 통해 만들어진 지정 된 싱크 파일 시스템에 의해 발생 합니다. 이 문제를 해결 하려면 Blob 컨테이너의 이름으로 존재 하지 않는 이름을 사용 하 여 새 파일 시스템을 지정 합니다. 그런 다음 Data Factory 데이터를 복사 하는 동안 해당 파일 시스템을 자동으로 만듭니다.

>[!NOTE]
>Azure Storage 방화벽 설정에서 **신뢰할 수 있는 Microsoft 서비스에서이 저장소 계정에 액세스 하도록 허용** 옵션을 사용 하도록 설정 하면 Azure integration runtime이 Data Lake Storage Gen2에 연결 되지 않고 금지 된 오류가 표시 됩니다. Data Factory 신뢰할 수 있는 Microsoft 서비스로 처리 되지 않기 때문에 오류 메시지가 나타납니다. 대신 자체 호스팅 통합 런타임을 사용 하 여 연결 하세요.

## <a name="get-started"></a>시작

>[!TIP]
>Data Lake Storage Gen2 커넥터를 사용 하는 방법에 대 한 연습은 [Azure Data Lake Storage Gen2에 데이터 로드](load-azure-data-lake-storage-gen2.md)를 참조 하세요.

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

다음 섹션에서는 Data Lake Storage Gen2에 한정 된 Data Factory 엔터티를 정의 하는 데 사용 되는 속성에 대 한 정보를 제공 합니다.

## <a name="linked-service-properties"></a>연결된 서비스 속성

Azure Data Lake Storage Gen2 커넥터는 다음 인증 유형을 지원 합니다. 자세한 내용은 해당 섹션을 참조 하세요.

- [계정 키 인증](#account-key-authentication)
- [서비스 주체 인증](#service-principal-authentication)
- [Azure 리소스 인증용 관리 ID](#managed-identity)

>[!NOTE]
>PolyBase를 사용 하 여 데이터를 SQL Data Warehouse 로드 하는 경우 원본 Data Lake Storage Gen2 Virtual Network 끝점으로 구성 된 경우 PolyBase에서 요구 하는 대로 관리 되는 id 인증을 사용 해야 합니다. 추가 구성 필수 구성 요소가 포함 된 [관리 되는 id 인증](#managed-identity) 섹션을 참조 하세요.

### <a name="account-key-authentication"></a>계정 키 인증

스토리지 계정 키 인증을 사용하는 데 지원되는 속성은 다음과 같습니다.

| 속성 | 설명 | 필수 |
|:--- |:--- |:--- |
| type | 형식 속성은 **AzureBlobFS**로 설정되어야 합니다. |예 |
| url | 패턴을 사용 하는 Data Lake Storage Gen2에 `https://<accountname>.dfs.core.windows.net`대 한 끝점입니다. | 예 |
| accountKey | Data Lake Storage Gen2에 대 한 계정 키입니다. 이 필드를 SecureString으로 표시하여 Data Factory에 안전하게 저장하거나 [Azure Key Vault에 저장되는 비밀을 참조](store-credentials-in-key-vault.md)합니다. |예 |
| connectVia | 데이터 저장소에 연결하는 데 사용할 [통합 런타임](concepts-integration-runtime.md)입니다. 데이터 저장소가 개인 네트워크에 있는 경우 Azure integration runtime 또는 자체 호스팅 integration runtime을 사용할 수 있습니다. 이 속성을 지정 하지 않으면 기본 Azure 통합 런타임이 사용 됩니다. |아니요 |

>[!NOTE]
>계정 키 인증을 사용 하는 경우 보조 ADLS 파일 시스템 끝점이 지원 되지 않습니다. 다른 인증 유형을 사용할 수 있습니다.

**예제:**

```json
{
    "name": "AzureDataLakeStorageGen2LinkedService",
    "properties": {
        "type": "AzureBlobFS",
        "typeProperties": {
            "url": "https://<accountname>.dfs.core.windows.net", 
            "accountkey": { 
                "type": "SecureString", 
                "value": "<accountkey>" 
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="service-principal-authentication"></a>사용자 주체 인증

서비스 주체 인증을 사용 하려면 다음 단계를 수행 합니다.

1. [AZURE ad 테 넌 트에 응용 프로그램 등록](../storage/common/storage-auth-aad-app.md#register-your-application-with-an-azure-ad-tenant)의 단계를 수행 하 여 Azure Active Directory (azure ad)에 응용 프로그램 엔터티를 등록 합니다. 연결된 서비스를 정의하는 데 사용되므로 다음 값을 적어둡니다.

    - 애플리케이션 ID
    - 애플리케이션 키
    - 테넌트 ID

2. 서비스 주체에 게 적절 한 권한을 부여 합니다. [파일 및 디렉터리에 대 한 액세스 제어 목록](../storage/blobs/data-lake-storage-access-control.md#access-control-lists-on-files-and-directories) 에서 Data Lake Storage Gen2의 사용 권한에 대 한 자세한 정보

    - **원본으로**: Storage 탐색기에서 복사할 파일에 대 한 **읽기** 권한과 함께 소스 파일 시스템에서 시작 하는 최소한 **실행** 권한을 부여 합니다. 또는 액세스 제어 (IAM)에서 적어도 **저장소 Blob 데이터 판독기** 역할을 부여 합니다.
    - **싱크로**: Storage 탐색기에서 싱크 폴더에 대 한 **쓰기** 권한과 함께 싱크 파일 시스템에서 시작 하는 최소한 **실행** 권한을 부여 합니다. 또는 액세스 제어 (IAM)에서 적어도 **저장소 Blob 데이터 참가자** 역할을 부여 합니다.

>[!NOTE]
>계정 수준에서 시작 하거나 연결을 테스트 하기 위해 폴더를 나열 하려면 **IAM에서 "저장소 Blob 데이터 판독기" 권한이 있는 저장소 계정**에 부여 되는 서비스 주체의 권한을 설정 해야 합니다. 다음을 사용하는 경우에도 적용됩니다.
>- **데이터 복사 도구** 를 작성 하 여 복사 파이프라인을 작성 합니다.
>- **Data Factory UI** - 연결을 테스트하고 작성 중에 폴더를 탐색합니다. 
>계정 수준에서 사용 권한을 부여 하는 방법에 대해 궁금한 사항이 있으면 제작 하는 동안 연결 테스트를 건너뛰고 사용 권한이 부여 된 부모 경로를 입력 한 다음 지정 된 경로에서 검색을 선택 합니다. 복사 작업은 복사 될 파일에 대 한 적절 한 사용 권한으로 서비스 사용자에 게 부여 된 경우에만 작동 합니다.

이러한 속성은 연결 된 서비스에 대해 지원 됩니다.

| 속성 | 설명 | 필수 |
|:--- |:--- |:--- |
| type | 형식 속성은 **AzureBlobFS**로 설정되어야 합니다. |예 |
| url | 패턴을 사용 하는 Data Lake Storage Gen2에 `https://<accountname>.dfs.core.windows.net`대 한 끝점입니다. | 예 |
| servicePrincipalId | 애플리케이션의 클라이언트 ID를 지정합니다. | 예 |
| servicePrincipalKey | 애플리케이션의 키를 지정합니다. Data Factory에 안전 하 게 `SecureString` 저장 하려면이 필드를로 표시 합니다. 또는 [Azure Key Vault에 저장 된 암호를 참조할](store-credentials-in-key-vault.md)수 있습니다. | 예 |
| 테넌트(tenant) | 애플리케이션이 있는 테넌트 정보(도메인 이름 또는 테넌트 ID)를 지정합니다. Azure Portal의 오른쪽 위 모서리에 마우스를 가져가면 검색 합니다. | 예 |
| connectVia | 데이터 저장소에 연결하는 데 사용할 [통합 런타임](concepts-integration-runtime.md)입니다. 데이터 저장소가 개인 네트워크에 있는 경우 Azure integration runtime 또는 자체 호스팅 integration runtime을 사용할 수 있습니다. 지정 하지 않으면 기본 Azure 통합 런타임이 사용 됩니다. |아니요 |

**예제:**

```json
{
    "name": "AzureDataLakeStorageGen2LinkedService",
    "properties": {
        "type": "AzureBlobFS",
        "typeProperties": {
            "url": "https://<accountname>.dfs.core.windows.net", 
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

특정 데이터 팩터리를 나타내는 [Azure 리소스용 관리 ID](data-factory-service-identity.md)와 데이터 팩터리를 연결할 수 있습니다. 사용자 고유의 서비스 주체를 사용 하는 것과 마찬가지로이 관리 되는 id를 Data Lake Storage Gen2 인증에 직접 사용할 수 있습니다. 이 지정 된 팩터리를 사용 하 여 Data Lake Storage Gen2에서 데이터에 액세스 하 고 복사할 수 있습니다.

Azure 리소스 인증에 관리 되는 id를 사용 하려면 다음 단계를 수행 합니다.

1. 팩터리와 함께 생성 된 **서비스 id 응용 프로그램 id** 의 값을 복사 하 여 [관리 되는 Data Factory Id 정보를 검색 합니다](data-factory-service-identity.md#retrieve-managed-identity) .

2. 관리 id에 적절 한 권한을 부여 합니다. [파일 및 디렉터리에 대 한 액세스 제어 목록](../storage/blobs/data-lake-storage-access-control.md#access-control-lists-on-files-and-directories)에서 Data Lake Storage Gen2의 사용 권한에 대해 자세히 알아보세요.

    - **원본으로**: Storage 탐색기에서 복사할 파일에 대 한 **읽기** 권한과 함께 소스 파일 시스템에서 시작 하는 최소한 **실행** 권한을 부여 합니다. 또는 액세스 제어 (IAM)에서 적어도 **저장소 Blob 데이터 판독기** 역할을 부여 합니다.
    - **싱크로**: Storage 탐색기에서 싱크 폴더에 대 한 **쓰기** 권한과 함께 싱크 파일 시스템에서 시작 하는 최소한 **실행** 권한을 부여 합니다. 또는 액세스 제어 (IAM)에서 적어도 **저장소 Blob 데이터 참가자** 역할을 부여 합니다.

>[!NOTE]
>계정 수준에서 시작 하거나 연결을 테스트 하기 위해 폴더를 나열 하려면 **IAM에서 "저장소 Blob 데이터 판독기" 권한을 사용 하 여 저장소 계정**에 부여 되는 관리 id의 권한을 설정 해야 합니다. 다음을 사용하는 경우에도 적용됩니다.
>- **데이터 복사 도구** 를 작성 하 여 복사 파이프라인을 작성 합니다.
>- **Data Factory UI** - 연결을 테스트하고 작성 중에 폴더를 탐색합니다. 
>계정 수준에서 사용 권한을 부여 하는 방법에 대해 궁금한 사항이 있으면 제작 하는 동안 연결 테스트를 건너뛰고 사용 권한이 부여 된 부모 경로를 입력 한 다음 지정 된 경로에서 검색을 선택 합니다. 복사 작업은 복사 될 파일에 대 한 적절 한 사용 권한으로 서비스 사용자에 게 부여 된 경우에만 작동 합니다.

>[!IMPORTANT]
>PolyBase를 사용 하 여 Data Lake Storage Gen2에서 SQL Data Warehouse로 데이터를 로드 하는 경우 Data Lake Storage Gen2에 대해 관리 id 인증을 사용 하는 경우 [이 가이드](../sql-database/sql-database-vnet-service-endpoint-rule-overview.md#impact-of-using-vnet-service-endpoints-with-azure-storage) 의 1 단계와 2 단계 SQL Database를 수행 해야 합니다. Azure Active Directory (Azure AD) 및 2) 저장소 Blob 데이터 참가자 역할을 SQL Database 서버에 할당 합니다. 나머지는 Data Factory에 의해 처리 됩니다. Data Lake Storage Gen2 Azure Virtual Network 끝점을 사용 하 여 구성 된 경우 PolyBase를 사용 하 여 데이터를 로드 하려면 PolyBase에서 요구 하는 대로 관리 되는 id 인증을 사용 해야 합니다.

이러한 속성은 연결 된 서비스에 대해 지원 됩니다.

| 속성 | 설명 | 필수 |
|:--- |:--- |:--- |
| type | 형식 속성은 **AzureBlobFS**로 설정되어야 합니다. |예 |
| url | 패턴을 사용 하는 Data Lake Storage Gen2에 `https://<accountname>.dfs.core.windows.net`대 한 끝점입니다. | 예 |
| connectVia | 데이터 저장소에 연결하는 데 사용할 [통합 런타임](concepts-integration-runtime.md)입니다. 데이터 저장소가 개인 네트워크에 있는 경우 Azure integration runtime 또는 자체 호스팅 integration runtime을 사용할 수 있습니다. 지정 하지 않으면 기본 Azure 통합 런타임이 사용 됩니다. |아니요 |

**예제:**

```json
{
    "name": "AzureDataLakeStorageGen2LinkedService",
    "properties": {
        "type": "AzureBlobFS",
        "typeProperties": {
            "url": "https://<accountname>.dfs.core.windows.net", 
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>데이터 세트 속성

데이터 집합을 정의 하는 데 사용할 수 있는 섹션 및 속성의 전체 목록은 [데이터 집합](concepts-datasets-linked-services.md)을 참조 하세요.

- **Parquet,로 구분 된 텍스트, Avro 및 이진 형식의**경우 [Parquet, 구분 된 텍스트 및 이진 형식 데이터 집합](#format-based-dataset) 섹션을 참조 하세요.
- **ORC/JSON 형식과**같은 다른 형식에 대해서는 [다른 형식 데이터 집합](#other-format-dataset) 섹션을 참조 하세요.

### <a name="format-based-dataset"></a>Parquet, 구분 기호로 분리 된 텍스트, Avro 및 이진 형식 데이터 집합

**Parquet, 구분 된 텍스트, avro 또는 이진 형식**간에 데이터를 복사 하려면 형식 기반 데이터 집합 및 지원 되는 설정에 대 한 [Parquet 형식](format-parquet.md), [구분 된 텍스트 형식](format-delimited-text.md), [avro 형식](format-avro.md) 및 [이진 형식](format-binary.md) 문서를 참조 하세요. 다음은 형식 기반 데이터 집합의 `location` 설정에서 Data Lake Storage Gen2에 대해 지원 되는 속성입니다.

| 속성   | 설명                                                  | 필수 |
| ---------- | ------------------------------------------------------------ | -------- |
| type       | 데이터 집합의 아래 `location` 에 있는 type 속성은 **azureblobfslocation**으로 설정 해야 합니다. | 예      |
| fileSystem | Data Lake Storage Gen2 파일 시스템 이름입니다.                              | 아니요       |
| folderPath | 지정 된 파일 시스템 아래에 있는 폴더의 경로입니다. 와일드 카드를 사용 하 여 폴더를 필터링 하려면이 설정을 건너뛰고 활동 원본 설정에서 지정 합니다. | 아니요       |
| fileName   | 지정 된 파일 시스템 + folderPath의 파일 이름입니다. 와일드 카드를 사용 하 여 파일을 필터링 하려는 경우이 설정을 건너뛰고 작업 원본 설정에서 지정 합니다. | 아니요       |

> [!NOTE]
> 이전 버전과의 호환성을 위해 다음 섹션에서 언급 한 parquet 또는 텍스트 형식이 포함 된 **Azureblobfsfile** 형식 데이터 집합은 여전히 복사, 조회 또는 GetMetadata 작업에 대 한 그대로 지원 됩니다. 하지만 데이터 흐름 매핑 기능에서는 작동 하지 않습니다. 앞으로이 새 모델을 사용 하는 것이 좋습니다. Data Factory 제작 UI는 이러한 새 형식을 생성 합니다.

**예제:**

```json
{
    "name": "DelimitedTextDataset",
    "properties": {
        "type": "DelimitedText",
        "linkedServiceName": {
            "referenceName": "<Data Lake Storage Gen2 linked service name>",
            "type": "LinkedServiceReference"
        },
        "schema": [ < physical schema, optional, auto retrieved during authoring > ],
        "typeProperties": {
            "location": {
                "type": "AzureBlobFSLocation",
                "fileSystem": "filesystemname",
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

### <a name="other-format-dataset"></a>기타 형식 데이터 집합

**ORC/JSON 형식의**Data Lake Storage Gen2 간에 데이터를 복사 하려면 다음 속성이 지원 됩니다.

| 속성 | 설명 | 필수 |
|:--- |:--- |:--- |
| type | 데이터 세트의 형식 속성을 **AzureBlobFSFile**로 설정해야 합니다. |예 |
| folderPath | Data Lake Storage Gen2 폴더의 경로입니다. 지정하지 않으면 루트를 가리킵니다. <br/><br/>와일드 카드 필터가 지원 됩니다. 허용 되는 `*` 와일드 카드는 (0 개 이상의 문자 `?` 를 찾습니다.) 및 (는 0 또는 단일 문자와 일치)입니다. 실제 `^` 폴더 이름에 와일드 카드가 있거나이 이스케이프 문자가 내부에 있는 경우를 사용 하 여를 이스케이프 합니다. <br/><br/>예: 파일 시스템/폴더/ 더 많은 예는 [폴더 및 파일 필터 예제](#folder-and-file-filter-examples)를 참조하세요. |아니요 |
| fileName | 지정 된 "folderPath" 아래에 있는 파일에 대 한 이름 또는 와일드 카드 필터입니다. 이 속성의 값을 지정하지 않으면 데이터 세트는 폴더에 있는 모든 파일을 가리킵니다. <br/><br/>필터의 경우 허용 되는 와일드 `*` 카드는 (0 개 이상의 문자와 일치 `?` ) 및 (는 0 또는 단일 문자와 일치)입니다.<br/>- 예 1: `"fileName": "*.csv"`<br/>- 예 2: `"fileName": "???20180427.txt"`<br/>실제 `^` 파일 이름에 와일드 카드가 있거나이 이스케이프 문자가 내부에 있는 경우를 사용 하 여를 이스케이프 합니다.<br/><br/>fileName이 출력 데이터 세트에 대해 지정되지 않고 **preserveHierarchy**가 작업 싱크에 지정되지 않으면, 복사 작업은 다음과 같은 패턴으로 파일 이름을 자동으로 생성합니다. "*데이터. [작업 실행 ID GUID]. [GUID if FlattenHierarchy]입니다. [구성 된 경우 형식]. [압축이 구성 된 경우]* "(예:" 0a405f8a-93ff-4c6f-b3be-f69616f1df7a. release.tar.gz "). 쿼리 대신 테이블 이름을 사용 하 여 테이블 형식 원본에서 복사 하는 경우 이름 패턴은 " *[table name]. [ format]. [압축이 구성 된 경우]* "(예:" MyTable "). |아니요 |
| modifiedDatetimeStart | 마지막으로 수정한 특성을 기반으로 하는 파일 필터입니다. 마지막으로 수정한 시간이와 `modifiedDatetimeStart` `modifiedDatetimeEnd`사이의 시간 범위 내에 있는 경우 파일이 선택 됩니다. 시간은 "2018-12-01T05:00:00Z" 형식으로 UTC 표준 시간대에 적용 됩니다. <br/><br/> 대용량 파일을 사용 하 여 파일을 필터링 하려는 경우이 설정을 사용 하면 데이터 이동의 전반적인 성능이 영향을 받습니다. <br/><br/> 속성은 NULL 일 수 있습니다. 즉, 파일 특성 필터가 데이터 집합에 적용 되지 않습니다. 이 `modifiedDatetimeStart` datetime 값을 포함 하지만 `modifiedDatetimeEnd` 가 NULL 인 경우 마지막으로 수정 된 특성이 datetime 값 보다 크거나 같은 파일이 선택 되어 있음을 의미 합니다. 이 `modifiedDatetimeEnd` datetime 값을 포함 하지만 `modifiedDatetimeStart` 가 NULL 인 경우 마지막으로 수정 된 특성이 datetime 값 보다 작은 파일을 선택 했음을 의미 합니다.| 아니요 |
| modifiedDatetimeEnd | 마지막으로 수정한 특성을 기반으로 하는 파일 필터입니다. 마지막으로 수정한 시간이와 `modifiedDatetimeStart` `modifiedDatetimeEnd`사이의 시간 범위 내에 있는 경우 파일이 선택 됩니다. 시간은 "2018-12-01T05:00:00Z" 형식으로 UTC 표준 시간대에 적용 됩니다. <br/><br/> 대용량 파일을 사용 하 여 파일을 필터링 하려는 경우이 설정을 사용 하면 데이터 이동의 전반적인 성능이 영향을 받습니다. <br/><br/> 속성은 NULL 일 수 있습니다. 즉, 파일 특성 필터가 데이터 집합에 적용 되지 않습니다. 이 `modifiedDatetimeStart` datetime 값을 포함 하지만 `modifiedDatetimeEnd` 가 NULL 인 경우 마지막으로 수정 된 특성이 datetime 값 보다 크거나 같은 파일이 선택 되어 있음을 의미 합니다. 이 `modifiedDatetimeEnd` datetime 값을 포함 하지만 `modifiedDatetimeStart` 가 NULL 인 경우 마지막으로 수정 된 특성이 datetime 값 보다 작은 파일을 선택 했음을 의미 합니다.| 아니요 |
| format | 파일 기반 저장소(이진 복사본) 간에 파일을 있는 그대로 복사하려는 경우 입력 및 출력 데이터 세트 정의 둘 다에서 format 섹션을 건너뜁니다.<br/><br/>특정 형식의 파일을 구문 분석하거나 생성하려는 경우, 지원되는 파일 형식 유형은 **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat** 및 **ParquetFormat**입니다. **format**의 **type** 속성을 이 값 중 하나로 설정합니다. 자세한 내용은 [텍스트 형식](supported-file-formats-and-compression-codecs.md#text-format), [JSON 형식](supported-file-formats-and-compression-codecs.md#json-format), [Avro 형식](supported-file-formats-and-compression-codecs.md#avro-format), [ORC 형식](supported-file-formats-and-compression-codecs.md#orc-format)및 [Parquet 형식](supported-file-formats-and-compression-codecs.md#parquet-format) 섹션을 참조 하세요. |아니요(이진 복사 시나리오에만 해당) |
| compression | 데이터에 대한 압축 유형 및 수준을 지정합니다. 자세한 내용은 [지원되는 파일 형식 및 압축 코덱](supported-file-formats-and-compression-codecs.md#compression-support)을 참조하세요.<br/>지원되는 형식은 **GZip**, **Deflate**, **BZip2** 및 **ZipDeflate**입니다.<br/>**Optimal** 및 **Fastest** 수준이 지원됩니다. |아니요 |

>[!TIP]
>폴더 아래에서 모든 파일을 복사하려면 **folderPath**만을 지정합니다.<br>지정 된 이름의 단일 파일을 복사 하려면 폴더 부분과 파일 **이름으로 파일** 이름으로 **folderPath** 를 지정 합니다.<br>폴더 아래에 있는 파일의 하위 집합을 복사 하려면 와일드 카드 필터를 사용 하 여 폴더 파트 및 **파일 이름** 으로 **folderPath** 를 지정 합니다. 

**예제:**

```json
{
    "name": "ADLSGen2Dataset",
    "properties": {
        "type": "AzureBlobFSFile",
        "linkedServiceName": {
            "referenceName": "<Azure Data Lake Storage Gen2 linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "folderPath": "myfilesystem/myfolder",
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

## <a name="copy-activity-properties"></a>복사 작업 속성

활동을 정의 하는 데 사용할 수 있는 섹션 및 속성의 전체 목록은 [복사 활동 구성](copy-activity-overview.md#configuration) 및 [파이프라인 및 활동](concepts-pipelines-activities.md)을 참조 하세요. 이 섹션에서는 Data Lake Storage Gen2 원본 및 싱크에서 지원하는 속성 목록을 제공합니다.

### <a name="azure-data-lake-storage-gen2-as-a-source-type"></a>원본 형식으로 Azure Data Lake Storage Gen2

- **Parquet, 구분 된 텍스트, Avro 및 이진 형식**에서 복사 하려면 [Parquet, 구분 된 텍스트 및 이진 형식 원본](#format-based-source) 섹션을 참조 하세요.
- **ORC/JSON 형식과**같은 다른 형식에서 복사 하려면 [다른 형식 소스](#other-format-source) 섹션을 참조 하세요.

#### <a name="format-based-source"></a>Parquet, 구분 된 텍스트, Avro 및 이진 형식 원본

**Parquet, 구분 된 텍스트, avro 또는 이진 형식**에서 데이터를 복사 하려면 형식 기반 복사 작업 원본 및 지원 되는 설정에 대 한 [Parquet 형식](format-parquet.md), [구분 된 텍스트 형식](format-delimited-text.md), [avro 형식](format-avro.md) 및 [이진 형식](format-binary.md) 문서를 참조 하세요. . 다음은 형식 기반 복사 원본에서 `storeSettings` 설정의 Data Lake Storage Gen2에 대해 지원 되는 속성입니다.

| 속성                 | 설명                                                  | 필수                                      |
| ------------------------ | ------------------------------------------------------------ | --------------------------------------------- |
| type                     | 아래의 `storeSettings` type 속성은 **azureblobfsreadsetting**으로 설정 해야 합니다. | 예                                           |
| recursive                | 하위 폴더 또는 지정된 폴더에서만 데이터를 재귀적으로 읽을지 여부를 나타냅니다. Recursive가 true로 설정 되 고 싱크가 파일 기반 저장소 인 경우 빈 폴더 또는 하위 폴더가 싱크에 복사 되거나 생성 되지 않습니다. 허용되는 값은 **true**(기본값) 및 **false**입니다. | 아니요                                            |
| wildcardFolderPath       | 원본 폴더를 필터링 하기 위해 데이터 집합에 구성 된 지정 된 파일 시스템 아래에 와일드 카드 문자가 포함 된 폴더 경로입니다. <br>허용 되는 `*` 와일드 카드는 (0 개 이상의 문자 `?` 를 찾습니다.) 및 (는 0 또는 단일 문자와 일치)입니다. 실제 `^` 폴더 이름에 와일드 카드 또는이 이스케이프 문자가 포함 된 경우를 사용 하 여를 이스케이프 합니다. <br>더 많은 예는 [폴더 및 파일 필터 예제](#folder-and-file-filter-examples)를 참조하세요. | 아니요                                            |
| wildcardFileName         | 소스 파일을 필터링 하는 지정 된 파일 시스템 + folderPath/wildcardFolderPath 아래의 와일드 카드 문자가 포함 된 파일 이름입니다. <br>허용 되는 `*` 와일드 카드는 (0 개 이상의 문자 `?` 를 찾습니다.) 및 (는 0 또는 단일 문자와 일치)입니다. 실제 `^` 폴더 이름에 와일드 카드 또는이 이스케이프 문자가 포함 된 경우를 사용 하 여를 이스케이프 합니다. 더 많은 예는 [폴더 및 파일 필터 예제](#folder-and-file-filter-examples)를 참조하세요. | 데이터 집합 `fileName` 에이 지정 되지 않은 경우 예 |
| modifiedDatetimeStart    | 마지막으로 수정한 특성을 기반으로 하는 파일 필터입니다. 마지막으로 수정한 시간이와 `modifiedDatetimeStart` `modifiedDatetimeEnd`사이의 시간 범위 내에 있는 경우 파일이 선택 됩니다. 시간은 "2018-12-01T05:00:00Z" 형식으로 UTC 표준 시간대에 적용 됩니다. <br> 속성은 NULL 일 수 있습니다. 즉, 데이터 집합에 파일 특성 필터가 적용 되지 않습니다. 에 `modifiedDatetimeStart` datetime 값이 있지만 `modifiedDatetimeEnd` 가 NULL 인 경우 마지막으로 수정 된 특성이 datetime 값 보다 크거나 같은 파일이 선택 되어 있음을 의미 합니다. 에 `modifiedDatetimeEnd` datetime 값이 있지만 `modifiedDatetimeStart` 가 NULL 인 경우 마지막으로 수정 된 특성이 datetime 값 보다 작은 파일이 선택 되어 있음을 의미 합니다. | 아니요                                            |
| modifiedDatetimeEnd      | 위와 동일합니다.                                               | 아니요                                            |
| maxConcurrentConnections | 저장소 저장소에 동시에 연결 하기 위한 연결 수입니다. 데이터 저장소에 대 한 동시 연결 수를 제한 하려는 경우에만를 지정 합니다. | 아니요                                            |

> [!NOTE]
> Parquet 또는 구분 된 텍스트 형식의 경우 다음 섹션에 언급 된 **Azureblobfssource** 형식 복사 작업 원본은 이전 버전과의 호환성을 위해 계속 해 서 지원 됩니다. 앞으로이 새 모델을 사용 하는 것이 좋습니다. Data Factory 제작 UI는 이러한 새 형식을 생성 합니다.

**예제:**

```json
"activities":[
    {
        "name": "CopyFromADLSGen2",
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
                    "type": "DelimitedTextReadSetting",
                    "skipLineCount": 10
                },
                "storeSettings":{
                    "type": "AzureBlobFSReadSetting",
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

#### <a name="other-format-source"></a>기타 서식 원본

**ORC/JSON 형식의**Data Lake Storage Gen2에서 데이터를 복사 하려면 복사 작업 **원본** 섹션에서 다음 속성을 지원 합니다.

| 속성 | 설명 | 필수 |
|:--- |:--- |:--- |
| type | 복사 작업 원본의 형식 속성을 **AzureBlobFSSource**로 설정해야 합니다. |예 |
| recursive | 하위 폴더 또는 지정된 폴더에서만 데이터를 재귀적으로 읽을지 여부를 나타냅니다. Recursive가 true로 설정 되 고 싱크가 파일 기반 저장소 인 경우 빈 폴더 또는 하위 폴더가 싱크에 복사 되거나 생성 되지 않습니다.<br/>허용되는 값은 **true**(기본값) 및 **false**입니다. | 아니요 |
| maxConcurrentConnections | 데이터 저장소에 동시에 연결 하기 위한 연결 수입니다. 데이터 저장소에 대 한 동시 연결 수를 제한 하려는 경우에만를 지정 합니다. | 아니요 |

**예제:**

```json
"activities":[
    {
        "name": "CopyFromADLSGen2",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<ADLS Gen2 input dataset name>",
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
                "type": "AzureBlobFSSource",
                "recursive": true
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="azure-data-lake-storage-gen2-as-a-sink-type"></a>싱크 형식인 Azure Data Lake Storage Gen2

- **Parquet, 구분 된 텍스트, Avro 또는 이진 형식**으로 복사 하려면 [Parquet, 구분 된 텍스트 및 이진 형식 싱크](#format-based-sink) 섹션을 참조 하세요.
- **ORC/JSON 형식과**같은 다른 형식으로 복사 하려면 [기타 형식 싱크](#other-format-sink) 섹션을 참조 하세요.

#### <a name="format-based-sink"></a>Parquet, 구분 기호로 분리 된 텍스트, Avro 및 이진 형식 싱크

**Parquet, 구분 된 텍스트, avro 또는 이진 형식**으로 데이터를 복사 하려면 형식 기반 복사 작업 싱크 및 지원 되는 설정에 대 한 [Parquet 형식](format-parquet.md), [구분 된 텍스트 형식](format-delimited-text.md), [avro 형식](format-avro.md) 및 [이진 형식](format-binary.md) 문서를 참조 하세요. 다음은 형식 기반 복사 싱크의 `storeSettings` 설정에서 Data Lake Storage Gen2에 대해 지원 되는 속성입니다.

| 속성                 | 설명                                                  | 필수 |
| ------------------------ | ------------------------------------------------------------ | -------- |
| type                     | 아래의 `storeSettings` type 속성은 **azureblobfswritesetting**으로 설정 해야 합니다. | 예      |
| copyBehavior             | 원본이 파일 기반 데이터 저장소의 파일인 경우 복사 동작을 정의합니다.<br/><br/>허용되는 값은 다음과 같습니다.<br/><b>- PreserveHierarchy(기본값)</b>: 대상 폴더에서 파일 계층 구조를 유지합니다. 원본 폴더의 원본 파일 상대 경로는 대상 폴더의 대상 파일 상대 경로와 동일합니다.<br/><b>- FlattenHierarchy</b>: 원본 폴더의 모든 파일이 대상 폴더의 첫 번째 수준에 있습니다. 대상 파일은 자동 생성된 이름을 갖습니다. <br/><b>- MergeFiles</b>: 원본 폴더의 모든 파일을 하나의 파일로 병합합니다. 파일 이름이 지정된 경우 병합되는 파일 이름은 지정된 이름입니다. 그렇지 않으면 자동 생성되는 파일 이름이 적용됩니다. | 아니요       |
| maxConcurrentConnections | 데이터 저장소에 동시에 연결 하기 위한 연결 수입니다. 데이터 저장소에 대 한 동시 연결 수를 제한 하려는 경우에만를 지정 합니다. | 아니요       |

> [!NOTE]
> Parquet 또는 구분 된 텍스트 형식의 경우 다음 섹션에 언급 된 **Azureblobfssink** 형식 복사 작업 싱크가 이전 버전과의 호환성을 위해 계속 해 서 지원 됩니다. 앞으로이 새 모델을 사용 하는 것이 좋습니다. Data Factory 제작 UI는 이러한 새 형식을 생성 합니다.

**예제:**

```json
"activities":[
    {
        "name": "CopyToADLSGen2",
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
                    "type": "AzureBlobFSWriteSetting",
                    "copyBehavior": "PreserveHierarchy"
                }
            }
        }
    }
]
```

#### <a name="other-format-sink"></a>기타 형식 싱크

**ORC/JSON 형식**으로 Data Lake Storage Gen2에 데이터를 복사 하려면 **sink** 섹션에서 다음 속성을 지원 합니다.

| 속성 | 설명 | 필수 |
|:--- |:--- |:--- |
| type | 복사 작업 싱크의 형식 속성은 **AzureBlobFSSink**로 설정해야 합니다. |예 |
| copyBehavior | 원본이 파일 기반 데이터 저장소의 파일인 경우 복사 동작을 정의합니다.<br/><br/>허용되는 값은 다음과 같습니다.<br/><b>- PreserveHierarchy(기본값)</b>: 대상 폴더에서 파일 계층 구조를 유지합니다. 원본 폴더의 원본 파일 상대 경로는 대상 폴더의 대상 파일 상대 경로와 동일합니다.<br/><b>- FlattenHierarchy</b>: 원본 폴더의 모든 파일이 대상 폴더의 첫 번째 수준에 있습니다. 대상 파일은 자동 생성된 이름을 갖습니다. <br/><b>- MergeFiles</b>: 원본 폴더의 모든 파일을 하나의 파일로 병합합니다. 파일 이름이 지정된 경우 병합되는 파일 이름은 지정된 이름입니다. 그렇지 않으면 자동 생성되는 파일 이름이 적용됩니다. | 아니요 |
| maxConcurrentConnections | 데이터 저장소에 동시에 연결 하기 위한 연결 수입니다. 데이터 저장소에 대 한 동시 연결 수를 제한 하려는 경우에만를 지정 합니다. | 아니요 |

**예제:**

```json
"activities":[
    {
        "name": "CopyToADLSGen2",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<ADLS Gen2 output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "AzureBlobFSSink",
                "copyBehavior": "PreserveHierarchy"
            }
        }
    }
]
```

### <a name="folder-and-file-filter-examples"></a>폴더 및 파일 필터 예제

이 섹션에서는 와일드카드 필터가 있는 폴더 경로 및 파일 이름의 결과 동작에 대해 설명합니다.

| folderPath | fileName | recursive | 원본 폴더 구조 및 필터 결과(**굵게** 표시된 파일이 검색됨)|
|:--- |:--- |:--- |:--- |
| `Folder*` | (비어 있고 기본값 사용) | false | FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5.csv<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `Folder*` | (비어 있고 기본값 사용) | true | FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File4.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `Folder*` | `*.csv` | false | FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5.csv<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `Folder*` | `*.csv` | true | FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |

### <a name="some-recursive-and-copybehavior-examples"></a>일부 recursive 및 copyBehavior 예제

이 섹션에서는 여러 가지 재귀 및 복사 동작 값 조합에 대 한 복사 작업의 결과 동작을 설명 합니다.

| recursive | copyBehavior | 원본 폴더 구조 | 결과 대상 |
|:--- |:--- |:--- |:--- |
| true |preserveHierarchy | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | Folder1 대상이 다음과 같이 원본 폴더와 동일한 구조로 만들어집니다.<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 |
| true |flattenHierarchy | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | Folder1 대상 폴더가 다음과 같은 구조로 만들어집니다. <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1에 대해 자동 생성된 이름<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2에 대해 자동 생성된 이름<br/>&nbsp;&nbsp;&nbsp;&nbsp;File3에 대해 자동 생성된 이름<br/>&nbsp;&nbsp;&nbsp;&nbsp;File4에 대해 자동 생성된 이름<br/>&nbsp;&nbsp;&nbsp;&nbsp;File5에 대해 자동 생성된 이름 |
| true |mergeFiles | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | Folder1 대상 폴더가 다음과 같은 구조로 만들어집니다. <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1, File2, File3, File4 및 File5의 내용이 자동 생성된 파일 이름이 있는 하나의 파일로 병합됩니다. |
| false |preserveHierarchy | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | Folder1 대상 폴더가 다음과 같은 구조로 만들어집니다. <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/><br/>File3, File4 및 File5.txt를 사용 하는 Subfolder1는 선택 되지 않습니다. |
| false |flattenHierarchy | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | Folder1 대상 폴더가 다음과 같은 구조로 만들어집니다. <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1에 대해 자동 생성된 이름<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2에 대해 자동 생성된 이름<br/><br/>File3, File4 및 File5.txt를 사용 하는 Subfolder1는 선택 되지 않습니다. |
| false |mergeFiles | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | Folder1 대상 폴더가 다음과 같은 구조로 만들어집니다. <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1과 File2의 내용이 자동 생성된 파일 이름이 있는 하나의 파일로 병합됩니다. File1에 대해 자동 생성된 이름<br/><br/>File3, File4 및 File5.txt를 사용 하는 Subfolder1는 선택 되지 않습니다. |

## <a name="preserve-acls-from-data-lake-storage-gen1"></a>Data Lake Storage Gen1에서 Acl 유지

>[!TIP]
>Azure Data Lake Storage Gen1에서 일반적인 방법으로 데이터를 Gen2에 복사 하려면 연습 및 모범 사례에 대 한 [Azure Data Factory를 사용 하 여 Azure Data Lake Storage Gen1에서 Gen2로 데이터 복사](load-azure-data-lake-storage-gen2-from-gen1.md) 를 참조 하세요.

Azure Data Lake Storage Gen1에서 Gen2로 파일을 복사 하는 경우 POSIX Acl (access control 목록)을 데이터와 함께 유지 하도록 선택할 수 있습니다. 액세스 제어에 대 한 자세한 내용은 [Azure Data Lake Storage Gen1의 액세스 제어](../data-lake-store/data-lake-store-access-control.md) 및 [Azure Data Lake Storage Gen2에서 access control](../storage/blobs/data-lake-storage-access-control.md)을 참조 하세요.

Azure Data Factory 복사 작업을 사용 하 여 다음 유형의 Acl을 유지할 수 있습니다. 하나 이상의 형식을 선택할 수 있습니다.

- **ACL**: 파일 및 디렉터리에 대 한 POSIX 액세스 제어 목록을 복사 하 고 유지 합니다. 원본에서 싱크로 전체 기존 Acl을 복사 합니다. 
- **소유자**: 파일 및 디렉터리를 소유 하는 사용자를 복사 하 고 유지 합니다. 싱크 Data Lake Storage Gen2에 대 한 슈퍼 사용자 액세스가 필요 합니다.
- **그룹**: 소유 하는 파일 및 디렉터리 그룹을 복사 하 고 유지 합니다. 싱크 Data Lake Storage Gen2 또는 소유 사용자 (소유 하는 사용자도 대상 그룹의 멤버인 경우)에 대 한 슈퍼 사용자 액세스 권한이 필요 합니다.

폴더에서 복사 하도록 지정 하는 경우이 true로 설정 된 경우 `recursive` 지정 된 폴더와 해당 폴더에 있는 파일 및 디렉터리에 대 한 acl을 Data Factory 복제 합니다. 단일 파일에서 복사 하도록 지정 하면 해당 파일에 대 한 Acl이 복사 됩니다.

>[!IMPORTANT]
>Acl을 유지 하도록 선택 하는 경우 싱크 Data Lake Storage Gen2 계정에 대해 작동 하는 Data Factory에 대 한 충분 한 권한을 부여 해야 합니다. 예를 들어 계정 키 인증을 사용 하거나 저장소 Blob 데이터 소유자 역할을 서비스 주체 또는 관리 id에 할당 합니다.

이진 복사 옵션 또는 이진 형식을 사용 하 여 Data Lake Storage Gen2를 이진 복사 옵션 또는 이진 형식으로 Data Lake Storage Gen1로 구성 하는 경우 **데이터 복사 도구 설정** 페이지 또는에서 **보존** 옵션을 찾을 수 있습니다. 활동 제작을 위한 **복사 활동** > **설정** 탭입니다.

![Gen2에 대 한 Data Lake Storage Gen1 ACL 유지](./media/connector-azure-data-lake-storage/adls-gen2-preserve-acl.png)

다음은 JSON 구성의 예입니다 (참조 `preserve`). 

```json
"activities":[
    {
        "name": "CopyFromGen1ToGen2",
        "type": "Copy",
        "typeProperties": {
            "source": {
                "type": "AzureDataLakeStoreSource",
                "recursive": true
            },
            "sink": {
                "type": "AzureBlobFSSink",
                "copyBehavior": "PreserveHierarchy"
            },
            "preserve": [
                "ACL",
                "Owner",
                "Group"
            ]
        },
        "inputs": [
            {
                "referenceName": "<Azure Data Lake Storage Gen1 input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Azure Data Lake Storage Gen2 output dataset name>",
                "type": "DatasetReference"
            }
        ]
    }
]
```

## <a name="mapping-data-flow-properties"></a>데이터 흐름 속성 매핑

데이터 흐름 매핑 기능에서 [원본 변환](data-flow-source.md) 및 [싱크 변환](data-flow-sink.md) 에 대해 자세히 알아보세요.

## <a name="next-steps"></a>다음 단계

Data Factory에서 복사 활동을 통해 원본 및 싱크로 지원되는 데이터 저장소의 목록은 [지원되는 데이터 저장소](copy-activity-overview.md##supported-data-stores-and-formats)를 참조하세요.
