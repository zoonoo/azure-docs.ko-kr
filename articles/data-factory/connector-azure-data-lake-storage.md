---
title: Data Factory를 사용하여 Azure Data Lake Storage Gen2 간에 데이터 복사 | Microsoft Docs
description: Azure Data Factory를 사용하여 Azure Data Lake Storage Gen2 간에 데이터를 복사하는 방법을 알아봅니다.
services: data-factory
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 05/24/2019
ms.author: jingwang
ms.openlocfilehash: 1e248a005b499227a667bebacf7244fc3df9c828
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/27/2019
ms.locfileid: "66239142"
---
# <a name="copy-data-to-or-from-azure-data-lake-storage-gen2-using-azure-data-factory"></a>Azure Data Factory를 사용하여 Azure Data Lake Storage Gen2 간에 데이터 복사

Azure Data Lake 저장소 Gen2 (ADLS Gen2)에 기본 제공 되는 빅 데이터 분석, 전용 기능 집합이 [Azure Blob storage](../storage/blobs/storage-blobs-introduction.md)합니다. 이를 사용하면 파일 시스템 및 개체 저장소 패러다임을 모두 사용하여 데이터를 조작할 수 있습니다.

이 문서에서는 Azure Data Lake 저장소 Gen2 간에 데이터를 복사 하는 방법을 설명 합니다. Azure Data Factory에 대해 자세히 알아보려면 [소개 문서](introduction.md)를 참조하세요.

## <a name="supported-capabilities"></a>지원되는 기능

이 Azure Data Lake 저장소 Gen2 커넥터는 다음 작업에 대 한 지원 됩니다.

- [복사 활동](copy-activity-overview.md) 사용 하 여 [원본/싱크 매트릭스를 지원 합니다.](copy-activity-overview.md)
- [데이터 흐름 매핑](concepts-data-flow-overview.md)
- [조회 작업](control-flow-lookup-activity.md)
- [GetMetadata 작업](control-flow-get-metadata-activity.md)

특히 이 커넥터는 다음을 지원합니다.

- 계정 키, 서비스 주체 또는 관리 ID를 Azure 리소스 인증에 사용하여 데이터 복사
- 파일을 있는 그대로 복사하거나 [지원되는 파일 형식 및 압축 코덱](supported-file-formats-and-compression-codecs.md)을 사용하여 파일을 구문 분석하거나 생성합니다.

>[!TIP]
>계층 구조 네임스페이스를 활성화하는 경우 현재 Blob과 ADLS Gen2 API 간에 작업의 운용성이 없습니다. "지정된 파일 시스템이 존재하지 않습니다."라는 상세 메시지와 함께 "ErrorCode=FilesystemNotFound" 오류가 발생하는 경우 ADLS Gen2 API가 아닌 Blob API를 통해 생성된 지정된 싱크 파일 시스템에서 야기됩니다. 이 문제를 해결하려면 Blob 컨테이너의 이름이 아닌 이름으로 새 파일 시스템을 지정합니다. 그러면 ADF가 데이터 복사 중에 자동으로 해당 파일을 만듭니다.

>[!NOTE]
>Azure Storage 방화벽 설정에서 _“Allow trusted Microsoft services to access this storage account”_ (신뢰할 수 있는 Microsoft 서비스가 이 스토리지 계정에 액세스하도록 허용) 옵션을 사용하도록 설정하면 ADF가 신뢰할 수 있는 Microsoft 서비스로 처리되지 않으므로 Azure Integration Runtime을 사용하여 Data Lake Storage Gen2에 연결할 수 없으며 사용 권한 없음 오류가 발생합니다. 대신 자체 호스팅 통합 런타임을 연결 수단으로 사용하세요.

## <a name="get-started"></a>시작하기

>[!TIP]
>Data Lake Storage Gen2 커넥터를 사용하는 연습을 보려면 [Azure Data Lake Storage Gen2에 데이터 로드](load-azure-data-lake-storage-gen2.md)를 참조하세요.

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

다음 섹션에서는 Data Lake Storage Gen2에 지정된 Data Factory 엔터티를 정의하는 데 사용되는 속성에 대해 자세히 설명합니다.

## <a name="linked-service-properties"></a>연결된 서비스 속성

Azure Data Lake Storage Gen2 커넥터에서 지원하는 인증 유형은 다음과 같습니다. 자세한 내용은 해당 섹션을 참조하세요.

- [계정 키 인증](#account-key-authentication)
- [서비스 주체 인증](#service-principal-authentication)
- [Azure 리소스 인증용 관리 ID](#managed-identity)

### <a name="account-key-authentication"></a>계정 키 인증

저장소 계정 키 인증을 사용하는 데 지원되는 속성은 다음과 같습니다.

| 자산 | 설명 | 필수 |
|:--- |:--- |:--- |
| type | 형식 속성은 **AzureBlobFS**로 설정되어야 합니다. |예 |
| url | `https://<accountname>.dfs.core.windows.net`의 패턴을 포함한 Data Lake Storage Gen2의 엔드포인트입니다. | 예 |
| accountKey | Data Lake Storage Gen2 서비스의 계정 키입니다. 이 필드를 SecureString으로 표시하여 Data Factory에 안전하게 저장하거나 [Azure Key Vault에 저장되는 비밀을 참조](store-credentials-in-key-vault.md)합니다. |예 |
| connectVia | 데이터 저장소에 연결하는 데 사용할 [통합 런타임](concepts-integration-runtime.md)입니다. Azure Integration Runtime 또는 자체 호스팅 Integration Runtime(데이터 저장소가 사설망에 있는 경우)을 사용할 수 있습니다. 지정하지 않으면 기본 Azure Integration Runtime을 사용합니다. |아닙니다. |

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

### <a name="service-principal-authentication"></a>서비스 주체 인증

서비스 주체 인증을 사용하려면 다음 단계를 수행합니다.

1. [Azure AD 테넌트에 애플리케이션 등록](../storage/common/storage-auth-aad-app.md#register-your-application-with-an-azure-ad-tenant)에 따라 Azure AD(Azure Active Directory)에 애플리케이션 항목을 등록합니다. 연결된 서비스를 정의하는 데 사용되므로 다음 값을 적어둡니다.

    - 애플리케이션 UI
    - 애플리케이션 키
    - 테넌트 ID

2. 서비스 주체 적절 한 권한을 부여 합니다.

    - **소스로**, Storage 탐색기에서 부여 적어도 **읽기 + 실행** 나열 및 폴더와 하위 폴더의 파일에 복사 하거나 부여 하는 권한을 **읽기** 단일 파일을 복사할 수 있는 권한을 합니다. 또는 액세스 제어 (IAM)에서 적어도 부여할 **Storage Blob 데이터 판독기** 역할입니다.
    - **싱크로**, Storage 탐색기에서 부여 적어도 **쓰기 + 실행** 폴더의 자식 항목을 만들 수 있는 권한이 있습니다. 또는 액세스 제어 (IAM)에서 적어도 부여할 **Storage Blob 데이터 기여자** 역할입니다.

>[!NOTE]
>목록에 부여 된 서비스 주체의 사용 권한을 설정 해야 하는 계정 수준에서 또는 연결 테스트를 시작 하는 폴더가 **IAM에서 "실행" 권한 사용 하 여 저장소 계정**합니다. 다음을 사용하는 경우에도 적용됩니다.
>- **데이터 복사 도구** - 복사 파이프라인을 작성합니다.
>- **Data Factory UI** - 연결을 테스트하고 작성 중에 폴더를 탐색합니다. 
>계정 수준에서 권한 부여에 문제가 있다면 작성 하는 동안 연결 테스트 및 입력된 경로 수동으로 건너뛸 수 있습니다. 복사 작업으로 복사 될 파일에 적절 한 권한이 있는 서비스 주체 부여는 계속 작동 합니다.

연결된 서비스에서 지원되는 속성은 다음과 같습니다.

| 자산 | 설명 | 필수 |
|:--- |:--- |:--- |
| type | 형식 속성은 **AzureBlobFS**로 설정되어야 합니다. |예 |
| url | `https://<accountname>.dfs.core.windows.net`의 패턴을 포함한 Data Lake Storage Gen2의 엔드포인트입니다. | 예 |
| servicePrincipalId | 애플리케이션의 클라이언트 ID를 지정합니다. | 예 |
| servicePrincipalKey | 애플리케이션의 키를 지정합니다. 이 필드를 **SecureString**으로 표시하여 Data Factory에 안전하게 저장하거나, [Azure Key Vault에 저장된 비밀을 참조](store-credentials-in-key-vault.md)합니다. | 예 |
| tenant | 애플리케이션이 있는 테넌트 정보(도메인 이름 또는 테넌트 ID)를 지정합니다. Azure Portal의 오른쪽 위 모서리를 마우스로 가리켜 검색합니다. | 예 |
| connectVia | 데이터 저장소에 연결하는 데 사용할 [통합 런타임](concepts-integration-runtime.md)입니다. Azure Integration Runtime 또는 자체 호스팅 Integration Runtime(데이터 저장소가 사설망에 있는 경우)을 사용할 수 있습니다. 지정하지 않으면 기본 Azure Integration Runtime을 사용합니다. |아닙니다. |

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

특정 데이터 팩터리를 나타내는 [Azure 리소스용 관리 ID](data-factory-service-identity.md)와 데이터 팩터리를 연결할 수 있습니다. 고유한 서비스 주체를 사용 하 여 ADLS Gen2 인증에 대 한 직접 관리 되는이 id를 사용할 수 있습니다. 이 지정 된 팩터리에 ADLS Gen2에서 /로 데이터를 액세스 및 복사를 허용합니다.

Azure 리소스 인증을 위해 관리 ID를 사용하려면 다음 단계를 따릅니다.

1. [Data factory 관리 id 정보를 검색할](data-factory-service-identity.md#retrieve-managed-identity) "서비스 ID 응용 프로그램 ID"의 팩터리와 함께 생성 된 값을 복사 하 여 합니다.

2. 관리 되는 id 적절 한 권한을 부여 합니다. 

    - **소스로**, Storage 탐색기에서 부여 적어도 **읽기 + 실행** 나열 및 폴더와 하위 폴더의 파일에 복사 하거나 부여 하는 권한을 **읽기** 단일 파일을 복사할 수 있는 권한을 합니다. 또는 액세스 제어 (IAM)에서 적어도 부여할 **Storage Blob 데이터 판독기** 역할입니다.
    - **싱크로**, Storage 탐색기에서 부여 적어도 **쓰기 + 실행** 폴더의 자식 항목을 만들 수 있는 권한이 있습니다. 또는 액세스 제어 (IAM)에서 적어도 부여할 **Storage Blob 데이터 기여자** 역할입니다.

>[!NOTE]
>목록에 관리 되는 id에 부여 된 사용 권한을 설정 해야 하는 계정 수준에서 또는 연결 테스트를 시작 하는 폴더가 **IAM에서 "실행" 권한 사용 하 여 저장소 계정**합니다. 다음을 사용하는 경우에도 적용됩니다.
>- **데이터 복사 도구** - 복사 파이프라인을 작성합니다.
>- **Data Factory UI** - 연결을 테스트하고 작성 중에 폴더를 탐색합니다. 
>계정 수준에서 권한 부여에 문제가 있다면 작성 하는 동안 연결 테스트 및 입력된 경로 수동으로 건너뛸 수 있습니다. 복사 작업으로 관리 되는 id에 복사할 파일에서 적절 한 권한이 있는 부여는 계속 작동 합니다.

>[!IMPORTANT]
>PolyBase를 사용 하 여 ADLS Gen2를 사용 하 여 id 인증을 관리 하는 경우 ADLS Gen2에서 데이터를 SQL DW로 로드 하는 경우 또한 # 1과 2의 단계를 따르고 있는지 확인 [이 지침은](../sql-database/sql-database-vnet-service-endpoint-rule-overview.md#impact-of-using-vnet-service-endpoints-with-azure-storage) 활성 Azure를 사용 하 여 SQL Database 서버를 등록 하려면 Directory (AAD) 및 SQL Database 서버;에 저장소 Blob 데이터 참가자 RBAC 역할 할당 나머지는 ADF에서 처리 됩니다. VNet 서비스 끝점을 사용 하 여 ADLS Gen2에 구성 된 경우 PolyBase에서 데이터를 로드 하는 데 관리 되는 id 인증을 사용 해야 합니다.

연결된 서비스에서 지원되는 속성은 다음과 같습니다.

| 자산 | 설명 | 필수 |
|:--- |:--- |:--- |
| type | 형식 속성은 **AzureBlobFS**로 설정되어야 합니다. |예 |
| url | `https://<accountname>.dfs.core.windows.net`의 패턴을 포함한 Data Lake Storage Gen2의 엔드포인트입니다. | 예 |
| connectVia | 데이터 저장소에 연결하는 데 사용할 [통합 런타임](concepts-integration-runtime.md)입니다. Azure Integration Runtime 또는 자체 호스팅 Integration Runtime(데이터 저장소가 사설망에 있는 경우)을 사용할 수 있습니다. 지정하지 않으면 기본 Azure Integration Runtime을 사용합니다. |아닙니다. |

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

데이터 세트 정의에 사용할 수 있는 섹션 및 속성의 전체 목록은 [데이터 세트](concepts-datasets-linked-services.md) 문서를 참조하세요. 

- 에 대 한 **Parquet 및 구분 기호로 분리 된 텍스트 형식**를 참조 하세요 [Parquet 및 구분 기호로 분리 된 텍스트 데이터 집합 형식](#parquet-and-delimited-text-format-dataset) 섹션입니다.
- 와 같은 다른 형식에 대 한 **ORC/Avro/JSON/이진 파일 형식**를 참조 [다른 형식으로 데이터 집합을](#other-format-dataset) 섹션입니다.

### <a name="parquet-and-delimited-text-format-dataset"></a>Parquet 및 구분 기호로 분리 된 텍스트 데이터 집합 형식

ADLS Gen2에서 데이터를 복사 하 **Parquet 또는 구분 기호로 분리 된 텍스트 형식**, 참조 [Parquet 형식](format-parquet.md) 하 고 [구분 기호로 분리 된 텍스트 형식으로](format-delimited-text.md) 형식 기반의 데이터 집합에 대 한 문서 및 지원 되는 설정입니다. 다음 속성에서 ADLS Gen2 지 `location` 형식 기반의 데이터 집합의 설정:

| 자산   | 설명                                                  | 필수 |
| ---------- | ------------------------------------------------------------ | -------- |
| type       | Type 속성은 아래 `location` 데이터 집합에서으로 설정 되어 있어야 **AzureBlobFSLocation**합니다. | 예      |
| fileSystem | ADLS Gen2 파일 시스템 이름입니다.                              | 아닙니다.       |
| folderPath | 지정된 된 파일 시스템에서 폴더 경로입니다. 와일드 카드 필터 폴더로 사용 하려는 경우이 설정은 건너뛰고 활동 원본 설정에서 지정 합니다. | 아닙니다.       |
| fileName   | 지정 된 파일 시스템 + folderPath 아래에 있는 파일 이름입니다. 와일드 카드를 사용 하 여 파일을 필터링 하려는 경우이 설정은 건너뛰고 활동 원본 설정에서 지정 합니다. | 아닙니다.       |

> [!NOTE]
> **AzureBlobFSFile** 다음 섹션에 언급 된 Parquet/텍스트 형식 사용 하 여 데이터 집합 형식으로 계속 지원-데이터 흐름 매핑 작동 하지 않습니다 하지만 이전 버전과 호환성에 대 한 복사/조회/GetMetadata 작업입니다. 앞으로이 새 모델을 사용 하도록 제안 된 및 UI를 작성 하는 ADF 이러한 새 형식 생성로 전환 되었습니다.

**예제:**

```json
{
    "name": "DelimitedTextDataset",
    "properties": {
        "type": "DelimitedText",
        "linkedServiceName": {
            "referenceName": "<ADLS Gen2 linked service name>",
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

### <a name="other-format-dataset"></a>다른 형식으로 데이터 집합

ADLS Gen2에서 데이터를 복사 하 **ORC/Avro/JSON/이진 형식**, 다음 속성이 지원 됩니다.

| 자산 | 설명 | 필수 |
|:--- |:--- |:--- |
| type | 데이터 세트의 형식 속성을 **AzureBlobFSFile**로 설정해야 합니다. |예 |
| folderPath | Data Lake Storage Gen2의 폴더에 대한 경로입니다. 지정하지 않으면 루트를 가리킵니다. <br/><br/>와일드카드 필터가 지원되며, 허용되는 와일드카드는 `*`(0개 이상의 문자 일치) 및 `?`(0-1개의 문자 일치)입니다. 실제 폴더 이름에 와일드카드 또는 이 이스케이프 문자가 있는 경우 `^`을 사용하여 이스케이프합니다. <br/><br/>예: 파일 시스템/폴더/에서 더 많은 예제를 참조 하세요 [폴더 및 파일 필터 예제](#folder-and-file-filter-examples)합니다. |아닙니다. |
| fileName | 지정된 "folderPath" 아래의 파일에 대한 **이름 또는 와일드 카드 필터**입니다. 이 속성의 값을 지정하지 않으면 데이터 세트는 폴더에 있는 모든 파일을 가리킵니다. <br/><br/>필터에 허용되는 와일드카드는 `*`(문자 0자 이상 일치) 및 `?`(문자 0자 또는 1자 일치)입니다.<br/>- 예 1: `"fileName": "*.csv"`<br/>- 예 2: `"fileName": "???20180427.txt"`<br/>`^`을 사용하여 실제 파일 이름 내에 와일드카드 또는 이 이스케이프 문자가 있는 경우 이스케이프합니다.<br/><br/>fileName이 출력 데이터 세트에 대해 지정되지 않고 **preserveHierarchy**가 작업 싱크에 지정되지 않으면, 복사 작업은 다음과 같은 패턴으로 파일 이름을 자동으로 생성합니다. "*데이터입니다. [작업 실행 ID GUID]. [GUID 경우 FlattenHierarchy]. [형식 구성 된 경우]입니다. [구성 된 경우에 압축]* ", 예: "Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt.gz"; 쿼리 대신 테이블 이름을 사용하여 테이블 형식 원본에서 복사하면, 이름 패턴이 " *[table name].[format].[compression if configured]* "입니다(예: "MyTable.csv"). |아닙니다. |
| modifiedDatetimeStart | 특성에 기반한 파일 필터링: 마지막으로 수정한 날짜 마지막 수정 시간이 `modifiedDatetimeStart`와 `modifiedDatetimeEnd` 사이의 시간 범위 내에 있으면 파일이 선택됩니다. 시간은 UTC 표준 시간대에 "2018-12-01T05:00:00Z" 형식으로 적용됩니다. <br/><br/> 대용량 파일에서에서 필터 파일 수행 하려는 경우이 설정을 사용 하 여 데이터 이동의 전반적인 성능을 영향을 알아야 합니다. <br/><br/> 속성에는 데이터 집합에 없는 파일 특성 필터를 적용할 것을 의미 하는 NULL 일 수 있습니다.  `modifiedDatetimeStart`에 datetime 값이 있지만 `modifiedDatetimeEnd`가 NULL이면, 마지막으로 수정된 특성이 datetime 값보다 크거나 같은 파일이 선택됩니다.  `modifiedDatetimeEnd`에 datetime 값이 있지만 `modifiedDatetimeStart`가 NULL이면, 마지막으로 수정된 특성이 datetime 값보다 작은 파일이 선택됩니다.| 아닙니다. |
| modifiedDatetimeEnd | 특성에 기반한 파일 필터링: 마지막으로 수정한 날짜 마지막 수정 시간이 `modifiedDatetimeStart`와 `modifiedDatetimeEnd` 사이의 시간 범위 내에 있으면 파일이 선택됩니다. 시간은 UTC 표준 시간대에 "2018-12-01T05:00:00Z" 형식으로 적용됩니다. <br/><br/> 대용량 파일에서에서 필터 파일 수행 하려는 경우이 설정을 사용 하 여 데이터 이동의 전반적인 성능을 영향을 알아야 합니다. <br/><br/> 속성에는 데이터 집합에 없는 파일 특성 필터를 적용할 것을 의미 하는 NULL 일 수 있습니다.  `modifiedDatetimeStart`에 datetime 값이 있지만 `modifiedDatetimeEnd`가 NULL이면, 마지막으로 수정된 특성이 datetime 값보다 크거나 같은 파일이 선택됩니다.  `modifiedDatetimeEnd`에 datetime 값이 있지만 `modifiedDatetimeStart`가 NULL이면, 마지막으로 수정된 특성이 datetime 값보다 작은 파일이 선택됩니다.| 아닙니다. |
| format | 파일 기반 저장소(이진 복사본) 간에 파일을 있는 그대로 복사하려는 경우 입력 및 출력 데이터 세트 정의 둘 다에서 format 섹션을 건너뜁니다.<br/><br/>특정 형식의 파일을 구문 분석하거나 생성하려는 경우, 지원되는 파일 형식 유형은 **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat** 및 **ParquetFormat**입니다. **format**의 **type** 속성을 이 값 중 하나로 설정합니다. 자세한 내용은 [텍스트 형식](supported-file-formats-and-compression-codecs.md#text-format), [JSON 형식](supported-file-formats-and-compression-codecs.md#json-format), [Avro 형식](supported-file-formats-and-compression-codecs.md#avro-format), [Orc 형식](supported-file-formats-and-compression-codecs.md#orc-format) 및 [Parquet 형식](supported-file-formats-and-compression-codecs.md#parquet-format) 섹션을 참조하세요. |아니요(이진 복사 시나리오에만 해당) |
| compression | 데이터에 대한 압축 유형 및 수준을 지정합니다. 자세한 내용은 [지원되는 파일 형식 및 압축 코덱](supported-file-formats-and-compression-codecs.md#compression-support)을 참조하세요.<br/>지원되는 형식은 **GZip**, **Deflate**, **BZip2** 및 **ZipDeflate**입니다.<br/>**Optimal** 및 **Fastest** 수준이 지원됩니다. |아닙니다. |

>[!TIP]
>폴더 아래에서 모든 파일을 복사하려면 **folderPath**만을 지정합니다.<br>지정된 이름의 단일 파일을 복사하려면 폴더 부분으로 **folderPath** 및 파일 이름으로 **fileName**을 지정합니다.<br>폴더 아래에서 파일의 하위 집합을 복사하려면 폴더 부분으로 **folderPath** 및 와일드카드 필터로 **fileName**을 지정합니다. 

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

작업 정의에 사용할 수 있는 섹션 및 속성의 전체 목록은 [복사 작업 구성](copy-activity-overview.md#configuration) 및 [파이프라인 및 작업](concepts-pipelines-activities.md) 아티클을 참조하세요. 이 섹션에서는 Data Lake Storage Gen2 원본 및 싱크에서 지원하는 속성 목록을 제공합니다.

### <a name="azure-data-lake-storage-gen2-as-a-source-type"></a>원본 형식인 Azure Data Lake Storage Gen2

- 복사본에 대 한 **Parquet 및 구분 기호로 분리 된 텍스트 형식**를 참조 하세요 [Parquet 및 구분 기호로 분리 된 텍스트 형식으로 원본](#parquet-and-delimited-text-format-source) 섹션.
- 와 같은 다른 형식에서 복사본에 대 한 **ORC/Avro/JSON/이진 파일 형식**를 참조 [다른 형식으로 원본](#other-format-source) 섹션입니다.

#### <a name="parquet-and-delimited-text-format-source"></a>Parquet 및 구분 기호로 분리 된 텍스트 형식으로 원본

ADLS Gen2에서 데이터를 복사 **Parquet 또는 구분 기호로 분리 된 텍스트 형식**를 참조 [Parquet 형식](format-parquet.md) 및 [구분 기호로 분리 된 텍스트 형식으로](format-delimited-text.md) 형식 기반의 복사 활동 원본에 대 한 문서 및 지원 되는 설정입니다. ADLS Gen2 아래에서 다음 속성이 지원 됩니다 `storeSettings` 형식 기반의 복사 원본에 설정 합니다.

| 자산                 | 설명                                                  | 필수                                      |
| ------------------------ | ------------------------------------------------------------ | --------------------------------------------- |
| type                     | 아래에 있는 type 속성은 `storeSettings` 으로 설정 되어 있어야 **AzureBlobFSReadSetting**합니다. | 예                                           |
| recursive                | 하위 폴더 또는 지정된 폴더에서만 데이터를 재귀적으로 읽을지 여부를 나타냅니다. recursive를 true로 설정하고 싱크가 파일 기반 저장소인 경우 빈 폴더 또는 하위 폴더가 싱크에 복사되거나 만들어지지 않습니다. 허용되는 값은 **true**(기본값) 및 **false**입니다. | 아닙니다.                                            |
| wildcardFolderPath       | 지정된 된 파일 시스템 필터 원본 폴더에 데이터 집합의 구성에서 와일드 카드 문자를 사용 하 여 폴더 경로입니다. <br>허용되는 와일드카드는 `*`(0개 이상의 문자 일치) 및 `?`(0-1개의 문자 일치)입니다. 실제 폴더 이름에 와일드카드 또는 이 이스케이프 문자가 있는 경우 `^`을 사용하여 이스케이프합니다. <br>더 많은 예는 [폴더 및 파일 필터 예제](#folder-and-file-filter-examples)를 참조하세요. | 아닙니다.                                            |
| wildcardFileName         | 지정 된 파일 시스템 + folderPath/wildcardFolderPath 필터 원본 파일에서 와일드 카드 문자를 사용 하 여 파일 이름입니다. <br>허용되는 와일드카드는 `*`(0개 이상의 문자 일치) 및 `?`(0-1개의 문자 일치)입니다. 실제 폴더 이름에 와일드카드 또는 이 이스케이프 문자가 있는 경우 `^`을 사용하여 이스케이프합니다.  더 많은 예는 [폴더 및 파일 필터 예제](#folder-and-file-filter-examples)를 참조하세요. | 경우에 예 `fileName` 데이터 집합에 지정 되지 않은 |
| modifiedDatetimeStart    | 특성에 기반한 파일 필터링: 마지막으로 수정한 날짜 마지막 수정 시간이 `modifiedDatetimeStart`와 `modifiedDatetimeEnd` 사이의 시간 범위 내에 있으면 파일이 선택됩니다. 시간은 UTC 표준 시간대에 "2018-12-01T05:00:00Z" 형식으로 적용됩니다. <br> 속성은 NULL일 수 있습니다. 이 경우 파일 특성 필터가 데이터 세트에 적용되지 않습니다.  `modifiedDatetimeStart`에 datetime 값이 있지만 `modifiedDatetimeEnd`가 NULL이면, 마지막으로 수정된 특성이 datetime 값보다 크거나 같은 파일이 선택됩니다.  `modifiedDatetimeEnd`에 datetime 값이 있지만 `modifiedDatetimeStart`가 NULL이면, 마지막으로 수정된 특성이 datetime 값보다 작은 파일이 선택됩니다. | 아닙니다.                                            |
| modifiedDatetimeEnd      | 위와 동일합니다.                                               | 아닙니다.                                            |
| maxConcurrentConnections | Storage 저장소에 동시에 연결할 연결 횟수입니다. 데이터 저장소에 대 한 동시 연결을 제한 하려는 경우에 지정 합니다. | 아닙니다.                                            |

> [!NOTE]
> Parquet/구분 기호로 분리 된 텍스트 형식에 대 한 **AzureBlobFSSource** 다음 섹션에 언급 된 형식 복사 활동 원본으로 계속 지원 됩니다-이전 버전과 호환성입니다. 앞으로이 새 모델을 사용 하도록 제안 된 및 UI를 작성 하는 ADF 이러한 새 형식 생성로 전환 되었습니다.

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

#### <a name="other-format-source"></a>다른 형식으로 원본

ADLS Gen2에서 데이터를 복사할 **ORC/Avro/JSON/이진 파일 형식**를 복사 작업에서 다음 속성이 지원 됩니다 **원본** 섹션:

| 자산 | 설명 | 필수 |
|:--- |:--- |:--- |
| type | 복사 작업 원본의 형식 속성을 **AzureBlobFSSource**로 설정해야 합니다. |예 |
| recursive | 하위 폴더 또는 지정된 폴더에서만 데이터를 재귀적으로 읽을지 여부를 나타냅니다. recursive를 true로 설정하고 싱크가 파일 기반 저장소인 경우 빈 폴더 또는 하위 폴더가 싱크에 복사되거나 만들어지지 않습니다.<br/>허용되는 값은 **true**(기본값) 및 **false**입니다. | 아닙니다. |
| maxConcurrentConnections | 데이터 저장소에 동시에 연결할 연결 횟수입니다. 데이터 저장소에 대 한 동시 연결을 제한 하려는 경우에 지정 합니다. | 아닙니다. |

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

- 복사에 대 한 **Parquet 및 구분 기호로 분리 된 텍스트 형식**를 참조 [Parquet 및 구분 기호로 분리 된 텍스트 형식 싱크](#parquet-and-delimited-text-format-sink) 섹션입니다.
- 와 같은 다른 형식으로 복사 **ORC/Avro/JSON/이진 파일 형식**, 참조 [다른 형식 싱크](#other-format-sink) 섹션입니다.

#### <a name="parquet-and-delimited-text-format-sink"></a>Parquet 및 구분 기호로 분리 된 텍스트 형식 싱크

ADLS Gen2로 데이터를 복사할 **Parquet 또는 구분 기호로 분리 된 텍스트 형식**, 참조 [Parquet 형식](format-parquet.md) 및 [구분 기호로 분리 된 텍스트 형식으로](format-delimited-text.md) 형식 기반의 복사 활동 sink에 대 한 문서 및 지원 되는 설정입니다. ADLS Gen2 아래에서 다음 속성이 지원 됩니다 `storeSettings` 형식 기반 복사 싱크를 설정 합니다.

| 자산                 | 설명                                                  | 필수 |
| ------------------------ | ------------------------------------------------------------ | -------- |
| type                     | 아래에 있는 type 속성은 `storeSettings` 으로 설정 되어 있어야 **AzureBlobFSWriteSetting**합니다. | 예      |
| copyBehavior             | 원본이 파일 기반 데이터 저장소의 파일인 경우 복사 동작을 정의합니다.<br/><br/>허용되는 값은 다음과 같습니다.<br/><b>- PreserveHierarchy(기본값)</b>: 대상 폴더에서 파일 계층 구조를 유지합니다. 원본 폴더의 원본 파일 상대 경로는 대상 폴더의 대상 파일 상대 경로와 동일합니다.<br/><b>- FlattenHierarchy</b>: 원본 폴더의 모든 파일이 대상 폴더의 첫 번째 수준에 있습니다. 대상 파일은 자동 생성된 이름을 갖습니다. <br/><b>- MergeFiles</b>: 원본 폴더의 모든 파일을 하나의 파일로 병합합니다. 파일 이름이 지정된 경우 병합되는 파일 이름은 지정된 이름입니다. 그렇지 않으면 자동 생성되는 파일 이름이 적용됩니다. | 아닙니다.       |
| maxConcurrentConnections | 데이터 저장소에 동시에 연결할 연결 횟수입니다. 데이터 저장소에 대 한 동시 연결을 제한 하려는 경우에 지정 합니다. | 아닙니다.       |

> [!NOTE]
> Parquet/구분 기호로 분리 된 텍스트 형식에 대 한 **AzureBlobFSSink** 으로 다음 섹션에 언급 된 형식 복사 활동 sink는 계속 지원-이전 버전과 호환성입니다. 앞으로이 새 모델을 사용 하도록 제안 된 및 UI를 작성 하는 ADF 이러한 새 형식 생성로 전환 되었습니다.

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

#### <a name="other-format-sink"></a>다른 형식 싱크

ADLS Gen2로 데이터를 복사할 **ORC/Avro/JSON/이진 파일 형식**에서 다음 속성이 지원 됩니다 합니다 **싱크** 섹션:

| 자산 | 설명 | 필수 |
|:--- |:--- |:--- |
| type | 복사 작업 싱크의 형식 속성은 **AzureBlobFSSink**로 설정해야 합니다. |예 |
| copyBehavior | 원본이 파일 기반 데이터 저장소의 파일인 경우 복사 동작을 정의합니다.<br/><br/>허용되는 값은 다음과 같습니다.<br/><b>- PreserveHierarchy(기본값)</b>: 대상 폴더에서 파일 계층 구조를 유지합니다. 원본 폴더의 원본 파일 상대 경로는 대상 폴더의 대상 파일 상대 경로와 동일합니다.<br/><b>- FlattenHierarchy</b>: 원본 폴더의 모든 파일이 대상 폴더의 첫 번째 수준에 있습니다. 대상 파일은 자동 생성된 이름을 갖습니다. <br/><b>- MergeFiles</b>: 원본 폴더의 모든 파일을 하나의 파일로 병합합니다. 파일 이름이 지정된 경우 병합되는 파일 이름은 지정된 이름입니다. 그렇지 않으면 자동 생성되는 파일 이름이 적용됩니다. | 아닙니다. |
| maxConcurrentConnections | 데이터 저장소에 동시에 연결할 연결 횟수입니다. 데이터 저장소에 대 한 동시 연결을 제한 하려는 경우에 지정 합니다. | 아닙니다. |

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
| `Folder*` | (비어 있음, 기본값 사용) | false | FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5.csv<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `Folder*` | (비어 있음, 기본값 사용) | true | FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File4.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `Folder*` | `*.csv` | false | FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5.csv<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `Folder*` | `*.csv` | true | FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |

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

## <a name="preserve-acls-from-data-lake-storage-gen1"></a>Data Lake Storage Gen1에서 Acl을 유지 합니다.

>[!TIP]
>복사에 대 한 데이터를 Azure 데이터 레이크 저장소 Gen1 Gen2에 일반적으로 볼 수 [Azure Data Lake 저장소 Gen1에서 Azure Data Factory를 사용 하 여 Gen2로 데이터 복사](load-azure-data-lake-storage-gen2-from-gen1.md) 연습 및 모범 사례를 사용 하 여 합니다.

Gen2로 Azure Data Lake 저장소 (ADLS) Gen1 파일 복사 하는 경우에 데이터와 함께 POSIX 액세스 제어 목록 (Acl)을 유지 하기 위해 선택할 수 있습니다. 세부 정보에 액세스 제어에 대 한 참조 [Azure Data Lake 저장소 Gen1의 액세스 제어](../data-lake-store/data-lake-store-access-control.md) 하 고 [Azure Data Lake 저장소 Gen2의 액세스 제어](../storage/blobs/data-lake-storage-access-control.md)입니다.

Azure Data Factory 복사 활동을 사용 하 여 Acl은 보존할 수 있습니다, 그리고 하나 이상의 형식을 선택할 수 있습니다.

- **ACL**: 복사 하 고 유지 **POSIX 액세스 제어 목록** 파일 및 디렉터리에 있습니다. 원본에서 싱크로 전체 기존 Acl을 복사 합니다. 
- **소유자**: 복사 하 고 유지 **소유 사용자** 파일 및 디렉터리입니다. ADLS Gen2 싱크에 대 한 슈퍼 사용자 액세스가 필요 합니다.
- **그룹**: 복사 하 고 유지 **소유 그룹** 파일 및 디렉터리입니다. 싱크에 ADLS Gen2 또는 소유 사용자 (소유 사용자 대상 그룹의 멤버 이기도)에 대 한 슈퍼 사용자 액세스가 필요 합니다.

Data Factory 복제는 지정 된 폴더와 파일 및 디렉터리 아래에 대 한 Acl을 폴더로 복사 되도록 지정 하는 경우 (경우 `recursive` 설정을 true로). 단일 파일에 Acl에서 복사 하도록 지정 하면 해당 파일이 복사 됩니다.

>[!IMPORTANT]
>Acl을 유지 하려는 경우는 높은 싱크도 Gen2 ADLS 계정에 대해 작동 하는 ADF에 대 한 충분 한 권한을 부여 했는지 확인 해야 합니다. 예를 들어, 계정 키 인증을 사용 하거나 서비스 주체, 관리 되는 id 저장소 Blob 데이터 소유자 역할을 할당 합니다.

를 구성한 경우 원본 ADLS Gen1으로 이진 복사 옵션/이진 파일 형식 및 싱크를 사용 하 여 ADLS Gen2로 이진 복사 옵션/이진 형식을 사용 하 여 찾을 수 있습니다 **유지** 옵션 **복사 데이터 도구 설정 페이지** 또는 **복사 활동-> 설정** 활동 제작에 대 한 탭 합니다.

![Gen2로 ADLS Gen1 ACL 유지](./media/connector-azure-data-lake-storage/adls-gen2-preserve-acl.png)

다음은 JSON 구성의 예 (참조 `preserve`): 

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

자세한 내용은 [소스 변환](data-flow-source.md) 하 고 [변환 싱크](data-flow-sink.md) 매핑 데이터 흐름에서.

## <a name="next-steps"></a>다음 단계

Data Factory에서 복사 활동을 통해 원본 및 싱크로 지원되는 데이터 저장소의 목록은 [지원되는 데이터 저장소](copy-activity-overview.md##supported-data-stores-and-formats)를 참조하세요.
