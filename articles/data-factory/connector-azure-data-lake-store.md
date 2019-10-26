---
title: Data Factory를 사용 하 여 Azure Data Lake Storage Gen1 간에 데이터 복사 Microsoft Docs
description: Data Factory를 사용하여 지원되는 원본 데이터 저장소에서 Azure Data Lake Store로 또는 Data Lake Store에서 지원되는 싱크 저장소로 데이터를 복사하는 방법에 대해 알아봅니다.
services: data-factory
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 10/24/2019
ms.author: jingwang
ms.openlocfilehash: 0e9cd42da54b4934dbf7d254e12fc740829ff8c4
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/25/2019
ms.locfileid: "72931155"
---
# <a name="copy-data-to-or-from-azure-data-lake-storage-gen1-using-azure-data-factory"></a>Azure Data Factory를 사용 하 여 Azure Data Lake Storage Gen1 간에 데이터 복사
> [!div class="op_single_selector" title1="사용 중인 Azure Data Factory의 버전을 선택 합니다."]
> * [버전 1](v1/data-factory-azure-datalake-connector.md)
> * [현재 버전](connector-azure-data-lake-store.md)

이 문서에서는 Azure Data Lake Storage Gen1 간에 데이터를 복사 하는 방법을 설명 합니다. Azure Data Factory에 대해 자세히 알아보려면 [소개 문서](introduction.md)를 참조하세요.

## <a name="supported-capabilities"></a>지원되는 기능

이 Azure Data Lake Storage Gen1 커넥터는 다음과 같은 작업에 대해 지원 됩니다.

- [지원 되는 원본/싱크 매트릭스](copy-activity-overview.md) 를 사용 하 여 [복사 작업](copy-activity-overview.md) 
- [데이터 흐름 매핑](concepts-data-flow-overview.md)
- [조회 작업](control-flow-lookup-activity.md)
- [GetMetadata 작업](control-flow-get-metadata-activity.md)
- [작업 삭제](delete-activity.md)

특히이 커넥터를 사용 하 여 다음을 수행할 수 있습니다.

- 다음 인증 방법 중 하나를 사용 하 여 파일을 복사 합니다. 서비스 주체 또는 Azure 리소스에 대 한 관리 되는 id입니다.
- 파일을 있는 그대로 복사 하거나 [지원 되는 파일 형식 및 압축 코덱](supported-file-formats-and-compression-codecs.md)을 사용 하 여 파일을 구문 분석 하거나 생성 합니다.

> [!IMPORTANT]
> 자체 호스팅 통합 런타임을 사용 하 여 데이터를 복사 하는 경우 포트 443에 대 한 아웃 바운드 트래픽을 `<ADLS account name>.azuredatalakestore.net` 하 고 `login.microsoftonline.com/<tenant>/oauth2/token` 하도록 회사 방화벽을 구성 합니다. 후자는 액세스 토큰을 가져오기 위해 통합 런타임이 통신해야 하는 Azure 보안 토큰 서비스입니다.

## <a name="get-started"></a>시작하기

> [!TIP]
> Azure Data Lake Store 커넥터를 사용 하는 방법에 대 한 연습은 [Azure Data Lake Store에 데이터 로드](load-azure-data-lake-store.md)를 참조 하세요.

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

다음 섹션에서는 Azure Data Lake Store에 한정 된 Data Factory 엔터티를 정의 하는 데 사용 되는 속성에 대 한 정보를 제공 합니다.

## <a name="linked-service-properties"></a>연결된 서비스 속성

Azure Data Lake Store 연결된 서비스에 다음 속성이 지원됩니다.

| 자산 | 설명 | 필수 |
|:--- |:--- |:--- |
| type | `type` 속성은 **AzureDataLakeStore**로 설정해야 합니다. | yes |
| dataLakeStoreUri | Azure Data Lake Store 계정에 대한 정보. 이 정보는 `https://[accountname].azuredatalakestore.net/webhdfs/v1` 또는 `adl://[accountname].azuredatalakestore.net/` 형식 중 하나를 사용합니다. | yes |
| subscriptionId | Data Lake Store 계정이 속하는 Azure 구독 ID입니다. | 싱크에 필요 |
| resourceGroupName | Data Lake Store 계정이 속하는 Azure 리소스 그룹 이름입니다. | 싱크에 필요 |
| connectVia | 데이터 저장소에 연결하는 데 사용할 [통합 런타임](concepts-integration-runtime.md)입니다. 데이터 저장소가 개인 네트워크에 있는 경우 Azure integration runtime 또는 자체 호스팅 integration runtime을 사용할 수 있습니다. 이 속성을 지정 하지 않으면 기본 Azure 통합 런타임이 사용 됩니다. |아닙니다. |

### <a name="use-service-principal-authentication"></a>서비스 주체 인증 사용

서비스 주체 인증을 사용 하려면 다음 단계를 수행 합니다.

1. Azure Active Directory에 응용 프로그램 엔터티를 등록 하 고 Data Lake Store에 대 한 액세스 권한을 부여 합니다. 자세한 단계는 [서비스 간 인증](../data-lake-store/data-lake-store-authenticate-using-active-directory.md)을 참조하세요. 연결된 서비스를 정의하는 데 사용되므로 다음 값을 적어둡니다.

    - 애플리케이션 UI
    - 애플리케이션 키
    - 테넌트 ID

2. 서비스 주체에 게 적절 한 권한을 부여 합니다. [Azure Data Lake Storage Gen1의 Access control](../data-lake-store/data-lake-store-access-control.md#common-scenarios-related-to-permissions)Data Lake Storage Gen1에서 사용 권한이 작동 하는 방법에 대 한 예제를 참조 하십시오.

    - **원본으로**: **데이터 탐색기** > **액세스**에서 복사할 파일에 대 한 **읽기** 권한과 함께 루트를 비롯 한 모든 업스트림 폴더에 대해 최소 **실행** 권한을 부여 합니다. **이 폴더 및 모든 하위 폴더**에 재귀적으로 추가하고 **액세스 권한 및 기본 권한 항목**으로 추가하도록 선택할 수 있습니다. 계정 수준 액세스 제어 (IAM)에 대 한 요구 사항은 없습니다.
    - **Sink**: **데이터 탐색기** > **Access**에서 싱크 폴더에 대 한 **쓰기** 권한과 함께 루트를 비롯 한 모든 업스트림 폴더에 대해 최소 **실행** 권한을 부여 합니다. **이 폴더 및 모든 하위 폴더**에 재귀적으로 추가하고 **액세스 권한 및 기본 권한 항목**으로 추가하도록 선택할 수 있습니다. Azure integration runtime을 사용 하 여 복사 하는 경우 (원본 및 싱크가 모두 클라우드에 있는 경우) IAM에서 Data Lake Store 지역을 Data Factory 검색할 수 있도록 적어도 **판독기** 역할을 부여 합니다. 이 IAM 역할을 방지하려면 Data Lake Store의 위치에 명시적으로 [Azure Integration Runtime을 만듭니다](create-azure-integration-runtime.md#create-azure-ir). 예를 들어 Data Lake Store 유럽 서부에 있는 경우 위치를 "유럽 서부"로 설정 하 여 Azure integration runtime을 만듭니다. 다음 예제와 같이 Data Lake Store 연결 된 서비스에서 연결 합니다.

다음과 같은 속성이 지원됩니다.

| 자산 | 설명 | 필수 |
|:--- |:--- |:--- |
| servicePrincipalId | 애플리케이션의 클라이언트 ID를 지정합니다. | yes |
| servicePrincipalKey | 애플리케이션의 키를 지정합니다. 이 필드를 `SecureString`으로 표시하여 Data Factory에 안전하게 저장하거나, [Azure Key Vault에 저장된 비밀을 참조](store-credentials-in-key-vault.md)합니다. | yes |
| tenant | 응용 프로그램이 있는 테 넌 트 정보 (예: 도메인 이름 또는 테 넌 트 ID)를 지정 합니다. Azure Portal의 오른쪽 위 모서리에 마우스를 이동하여 검색할 수 있습니다. | yes |

**예제:**

```json
{
    "name": "AzureDataLakeStoreLinkedService",
    "properties": {
        "type": "AzureDataLakeStore",
        "typeProperties": {
            "dataLakeStoreUri": "https://<accountname>.azuredatalakestore.net/webhdfs/v1",
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalKey": {
                "type": "SecureString",
                "value": "<service principal key>"
            },
            "tenant": "<tenant info, e.g. microsoft.onmicrosoft.com>",
            "subscriptionId": "<subscription of ADLS>",
            "resourceGroupName": "<resource group of ADLS>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="managed-identity"></a> Azure 리소스 인증에 관리 ID 사용

특정 데이터 팩터리를 나타내는 [Azure 리소스용 관리 ID](data-factory-service-identity.md)와 데이터 팩터리를 연결할 수 있습니다. 사용자 고유의 서비스 주체를 사용 하는 것과 마찬가지로이 관리 되는 id를 Data Lake Store 인증에 직접 사용할 수 있습니다. 이 지정된 팩터리는 Data Lake Store 간에 데이터에 액세스하고 복사할 수 있습니다.

Azure 리소스 인증에 관리 되는 id를 사용 하려면 다음 단계를 수행 합니다.

1. 팩터리와 함께 생성 된 "서비스 Id 응용 프로그램 ID"의 값을 복사 하 여 [데이터 팩터리 관리 id 정보를 검색 합니다](data-factory-service-identity.md#retrieve-managed-identity) .

2. Data Lake Store에 대 한 관리 id 액세스 권한을 부여 합니다. [Azure Data Lake Storage Gen1의 Access control](../data-lake-store/data-lake-store-access-control.md#common-scenarios-related-to-permissions)Data Lake Storage Gen1에서 사용 권한이 작동 하는 방법에 대 한 예제를 참조 하십시오.

    - **원본으로**: **데이터 탐색기** > **액세스**에서 복사할 파일에 대 한 **읽기** 권한과 함께 루트를 비롯 한 모든 업스트림 폴더에 대해 최소 **실행** 권한을 부여 합니다. **이 폴더 및 모든 하위 폴더**에 재귀적으로 추가하고 **액세스 권한 및 기본 권한 항목**으로 추가하도록 선택할 수 있습니다. 계정 수준 액세스 제어 (IAM)에 대 한 요구 사항은 없습니다.
    - **Sink**: **데이터 탐색기** > **Access**에서 싱크 폴더에 대 한 **쓰기** 권한과 함께 루트를 비롯 한 모든 업스트림 폴더에 대해 최소 **실행** 권한을 부여 합니다. **이 폴더 및 모든 하위 폴더**에 재귀적으로 추가하고 **액세스 권한 및 기본 권한 항목**으로 추가하도록 선택할 수 있습니다. Azure integration runtime을 사용 하 여 복사 하는 경우 (원본 및 싱크가 모두 클라우드에 있는 경우) IAM에서 Data Lake Store 지역을 Data Factory 검색할 수 있도록 적어도 **판독기** 역할을 부여 합니다. 이 IAM 역할을 방지하려면 Data Lake Store의 위치에 명시적으로 [Azure Integration Runtime을 만듭니다](create-azure-integration-runtime.md#create-azure-ir). 다음 예제와 같이 Data Lake Store 연결 된 서비스에서 연결 합니다.

Azure Data Factory에서 연결된 서비스의 일반 Data Lake Store 정보 이외의 속성을 지정할 필요가 없습니다.

**예제:**

```json
{
    "name": "AzureDataLakeStoreLinkedService",
    "properties": {
        "type": "AzureDataLakeStore",
        "typeProperties": {
            "dataLakeStoreUri": "https://<accountname>.azuredatalakestore.net/webhdfs/v1",
            "subscriptionId": "<subscription of ADLS>",
            "resourceGroupName": "<resource group of ADLS>"
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

다음 속성은 형식 기반 데이터 집합의 `location` 설정에서 Azure Data Lake Store Gen1에 대해 지원 됩니다.

| 자산   | 설명                                                  | 필수 |
| ---------- | ------------------------------------------------------------ | -------- |
| type       | 데이터 집합의 `location`에 있는 type 속성은 **AzureDataLakeStoreLocation**로 설정 해야 합니다. | yes      |
| folderPath | 폴더에 대 한 경로입니다. 와일드 카드를 사용 하 여 폴더를 필터링 하려면이 설정을 건너뛰고 활동 원본 설정에서 지정 합니다. | 아닙니다.       |
| fileName   | 지정 된 folderPath의 파일 이름입니다. 와일드 카드를 사용 하 여 파일을 필터링 하려는 경우이 설정을 건너뛰고 작업 원본 설정에서 지정 합니다. | 아닙니다.       |

**예제:**

```json
{
    "name": "DelimitedTextDataset",
    "properties": {
        "type": "DelimitedText",
        "linkedServiceName": {
            "referenceName": "<ADLS Gen1 linked service name>",
            "type": "LinkedServiceReference"
        },
        "schema": [ < physical schema, optional, auto retrieved during authoring > ],
        "typeProperties": {
            "location": {
                "type": "AzureDataLakeStoreLocation",
                "folderPath": "root/folder/subfolder"
            },
            "columnDelimiter": ",",
            "quoteChar": "\"",
            "firstRowAsHeader": true,
            "compressionCodec": "gzip"
        }
    }
}
```

### <a name="legacy-dataset-model"></a>레거시 데이터 집합 모델

>[!NOTE]
>다음 데이터 집합 모델은 이전 버전과의 호환성을 위해 그대로 계속 지원 됩니다. 앞의 섹션에서 설명한 새 모델을 사용 하는 것이 좋습니다. 그러면 ADF 제작 UI가 새 모델을 생성 하도록 전환 됩니다.

| 자산 | 설명 | 필수 |
|:--- |:--- |:--- |
| type | 데이터 집합의 type 속성은 **AzureDataLakeStoreFile**로 설정 해야 합니다. |yes |
| folderPath | Data Lake Store의 폴더 경로입니다. 지정하지 않으면 루트를 가리킵니다. <br/><br/>와일드 카드 필터가 지원 됩니다. 허용 되는 와일드 카드는 `*` (0 개 이상의 문자와 일치) 및 `?` (0 개 또는 단일 문자와 일치)입니다. 실제 폴더 이름에 와일드 카드 또는이 이스케이프 문자가 포함 된 경우 `^`를 사용 하 여 이스케이프 합니다. <br/><br/>예: rootfolder/하위 폴더/. 더 많은 예는 [폴더 및 파일 필터 예제](#folder-and-file-filter-examples)를 참조하세요. |아닙니다. |
| fileName | 지정 된 "folderPath" 아래에 있는 파일에 대 한 이름 또는 와일드 카드 필터입니다. 이 속성의 값을 지정하지 않으면 데이터 세트는 폴더에 있는 모든 파일을 가리킵니다. <br/><br/>필터의 경우 허용 되는 와일드 카드는 `*` (0 개 이상의 문자와 일치) 및 `?` (0 개 또는 단일 문자와 일치)입니다.<br/>- 예 1: `"fileName": "*.csv"`<br/>- 예 2: `"fileName": "???20180427.txt"`<br/>실제 파일 이름에 와일드 카드 또는이 이스케이프 문자가 포함 된 경우 `^`를 사용 하 여 이스케이프 합니다.<br/><br/>FileName이 출력 데이터 집합에 대해 지정 되지 않고 **preserveHierarchy** 가 활동 싱크에 지정 되지 않은 경우 복사 작업은 다음 패턴으로 파일 이름을 자동으로 생성 합니다. "*Data. [ 작업 실행 ID입니다. [GUID if FlattenHierarchy]입니다. [구성 된 경우 형식]. [압축이 구성 된 경우]* "(예:" 0a405f8a-93ff-4c6f-b3be-f69616f1df7a. release.tar.gz "). 쿼리 대신 테이블 이름을 사용 하 여 테이블 형식 원본에서 복사 하는 경우 이름 패턴은 " *[table name]. [ format]. [압축이 구성 된 경우]* "(예:" MyTable "). |아닙니다. |
| modifiedDatetimeStart | 마지막으로 수정한 특성을 기반으로 하는 파일 필터입니다. 파일은 마지막으로 수정한 시간이 `modifiedDatetimeStart`와 `modifiedDatetimeEnd`사이의 시간 범위 내에 있는 경우 선택 됩니다. 시간은 "2018-12-01T05:00:00Z" 형식으로 UTC 표준 시간대에 적용 됩니다. <br/><br/> 대용량 파일을 사용 하 여 파일을 필터링 하려는 경우이 설정을 사용 하면 데이터 이동의 전반적인 성능이 영향을 받습니다. <br/><br/> 속성은 NULL 일 수 있습니다. 즉, 파일 특성 필터가 데이터 집합에 적용 되지 않습니다. `modifiedDatetimeStart`에 datetime 값이 있지만 `modifiedDatetimeEnd` NULL 이면 마지막으로 수정 된 특성이 datetime 값 보다 크거나 같은 파일이 선택 된 것입니다. `modifiedDatetimeEnd`에 datetime 값이 있지만 `modifiedDatetimeStart` NULL 이면 마지막으로 수정 된 특성이 datetime 값 보다 작은 파일이 선택 된 것입니다.| 아닙니다. |
| modifiedDatetimeEnd | 마지막으로 수정한 특성을 기반으로 하는 파일 필터입니다. 파일은 마지막으로 수정한 시간이 `modifiedDatetimeStart`와 `modifiedDatetimeEnd`사이의 시간 범위 내에 있는 경우 선택 됩니다. 시간은 "2018-12-01T05:00:00Z" 형식으로 UTC 표준 시간대에 적용 됩니다. <br/><br/> 대용량 파일을 사용 하 여 파일을 필터링 하려는 경우이 설정을 사용 하면 데이터 이동의 전반적인 성능이 영향을 받습니다. <br/><br/> 속성은 NULL 일 수 있습니다. 즉, 파일 특성 필터가 데이터 집합에 적용 되지 않습니다. `modifiedDatetimeStart`에 datetime 값이 있지만 `modifiedDatetimeEnd` NULL 이면 마지막으로 수정 된 특성이 datetime 값 보다 크거나 같은 파일이 선택 된 것입니다. `modifiedDatetimeEnd`에 datetime 값이 있지만 `modifiedDatetimeStart` NULL 이면 마지막으로 수정 된 특성이 datetime 값 보다 작은 파일이 선택 된 것입니다.| 아닙니다. |
| format | 파일 기반 저장소 (이진 복사) 간에 파일을 있는 그대로 복사 하려는 경우 입력 및 출력 데이터 집합 정의 둘 다에서 형식 섹션을 건너뜁니다.<br/><br/>특정 형식으로 파일을 구문 분석하거나 생성하려면 **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat** 및 **ParquetFormat** 파일 형식 유형이 지원됩니다. **format**의 **type** 속성을 이 값 중 하나로 설정합니다. 자세한 내용은 [텍스트 형식](supported-file-formats-and-compression-codecs.md#text-format), [JSON 형식](supported-file-formats-and-compression-codecs.md#json-format), [Avro 형식](supported-file-formats-and-compression-codecs.md#avro-format), [Orc 형식](supported-file-formats-and-compression-codecs.md#orc-format) 및 [Parquet 형식](supported-file-formats-and-compression-codecs.md#parquet-format) 섹션을 참조하세요. |아니요(이진 복사 시나리오에만 해당) |
| 압축 | 데이터에 대한 압축 유형 및 수준을 지정합니다. 자세한 내용은 [지원되는 파일 형식 및 압축 코덱](supported-file-formats-and-compression-codecs.md#compression-support)을 참조하세요.<br/>지원되는 형식은 **GZip**, **Deflate**, **BZip2** 및 **ZipDeflate**입니다.<br/>**Optimal** 및 **Fastest** 수준이 지원됩니다. |아닙니다. |

>[!TIP]
>폴더 아래에서 모든 파일을 복사하려면 **folderPath**만을 지정합니다.<br>특정 이름의 단일 파일을 복사 하려면 폴더 부분과 파일 **이름으로 파일** 이름으로 **folderPath** 를 지정 합니다.<br>폴더 아래에 있는 파일의 하위 집합을 복사 하려면 와일드 카드 필터를 사용 하 여 폴더 파트 및 **파일 이름** 으로 **folderPath** 를 지정 합니다. 

**예제:**

```json
{
    "name": "ADLSDataset",
    "properties": {
        "type": "AzureDataLakeStoreFile",
        "linkedServiceName":{
            "referenceName": "<ADLS linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "folderPath": "datalake/myfolder/",
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

작업 정의에 사용할 수 있는 섹션 및 속성의 전체 목록은 [파이프라인](concepts-pipelines-activities.md)을 참조하세요. 이 섹션에서는 Azure Data Lake Store 원본 및 싱크에서 지원하는 속성 목록을 제공합니다.

### <a name="azure-data-lake-store-as-source"></a>Azure Data Lake Store를 원본으로

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

다음 속성은 형식 기반 복사 원본의 `storeSettings` 설정에서 Azure Data Lake Store Gen1에 대해 지원 됩니다.

| 자산                 | 설명                                                  | 필수                                      |
| ------------------------ | ------------------------------------------------------------ | --------------------------------------------- |
| type                     | `storeSettings` 아래의 type 속성은 **AzureDataLakeStoreReadSetting**로 설정 해야 합니다. | yes                                           |
| recursive                | 하위 폴더 또는 지정된 폴더에서만 데이터를 재귀적으로 읽을지 여부를 나타냅니다. Recursive가 true로 설정 되 고 싱크가 파일 기반 저장소 인 경우 빈 폴더 또는 하위 폴더가 싱크에 복사 되거나 생성 되지 않습니다. 허용되는 값은 **true**(기본값) 및 **false**입니다. | 아닙니다.                                            |
| wildcardFolderPath       | 원본 폴더를 필터링 할 와일드 카드 문자가 포함 된 폴더 경로입니다. <br>허용 되는 와일드 카드는 `*` (0 개 이상의 문자와 일치) 및 `?` (0 개 또는 단일 문자와 일치)입니다. 실제 폴더 이름에 와일드 카드 또는이 이스케이프 문자가 포함 된 경우 `^`를 사용 하 여 이스케이프 합니다. <br>더 많은 예는 [폴더 및 파일 필터 예제](#folder-and-file-filter-examples)를 참조하세요. | 아닙니다.                                            |
| wildcardFileName         | 소스 파일을 필터링 하기 위해 지정 된 folderPath/wildcardFolderPath 아래의 와일드 카드 문자가 포함 된 파일 이름입니다. <br>허용 되는 와일드 카드는 `*` (0 개 이상의 문자와 일치) 및 `?` (0 개 또는 단일 문자와 일치)입니다. 실제 폴더 이름에 와일드 카드 또는이 이스케이프 문자가 포함 된 경우 `^`를 사용 하 여 이스케이프 합니다. 더 많은 예는 [폴더 및 파일 필터 예제](#folder-and-file-filter-examples)를 참조하세요. | `fileName` 데이터 집합에 지정 되지 않은 경우에는 예입니다. |
| modifiedDatetimeStart    | 마지막으로 수정한 특성을 기반으로 하는 파일 필터입니다. 파일은 마지막으로 수정한 시간이 `modifiedDatetimeStart`와 `modifiedDatetimeEnd`사이의 시간 범위 내에 있는 경우 선택 됩니다. 시간은 "2018-12-01T05:00:00Z" 형식으로 UTC 표준 시간대에 적용 됩니다. <br> 속성은 NULL 일 수 있습니다. 즉, 파일 특성 필터가 데이터 집합에 적용 되지 않습니다. `modifiedDatetimeStart`에 datetime 값이 있지만 `modifiedDatetimeEnd` NULL 이면 마지막으로 수정 된 특성이 datetime 값 보다 크거나 같은 파일이 선택 된 것입니다. `modifiedDatetimeEnd`에 datetime 값이 있지만 `modifiedDatetimeStart` NULL 이면 마지막으로 수정 된 특성이 datetime 값 보다 작은 파일이 선택 된 것입니다. | 아닙니다.                                            |
| modifiedDatetimeEnd      | 위와 동일합니다.                                               | 아닙니다.                                            |
| maxConcurrentConnections | 저장소 저장소에 동시에 연결 하기 위한 연결 수입니다. 데이터 저장소에 대 한 동시 연결 수를 제한 하려는 경우에만를 지정 합니다. | 아닙니다.                                            |

**예제:**

```json
"activities":[
    {
        "name": "CopyFromADLSGen1",
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
                    "type": "AzureDataLakeStoreReadSetting",
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

#### <a name="legacy-source-model"></a>레거시 원본 모델

>[!NOTE]
>다음 복사 원본 모델은 이전 버전과의 호환성을 위해 그대로 계속 지원 됩니다. 위에서 언급 한 새 모델을 사용 하는 것이 좋습니다. 그러면 ADF 제작 UI가 새 모델을 생성 하도록 전환 됩니다.

| 자산 | 설명 | 필수 |
|:--- |:--- |:--- |
| type | 복사 작업 원본의 `type` 속성은 **AzureDataLakeStoreSource**로 설정 해야 합니다. |yes |
| recursive | 하위 폴더 또는 지정된 폴더에서만 데이터를 재귀적으로 읽을지 여부를 나타냅니다. `recursive`이 true로 설정 되 고 싱크가 파일 기반 저장소 인 경우 빈 폴더 또는 하위 폴더가 싱크에 복사 되거나 생성 되지 않습니다. 허용되는 값은 **true**(기본값) 및 **false**입니다. | 아닙니다. |
| maxConcurrentConnections | 데이터 저장소에 동시에 연결 하기 위한 연결 수입니다. 데이터 저장소에 대 한 동시 연결 수를 제한 하려는 경우에만를 지정 합니다. | 아닙니다. |

**예제:**

```json
"activities":[
    {
        "name": "CopyFromADLSGen1",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<ADLS Gen1 input dataset name>",
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
                "type": "AzureDataLakeStoreSource",
                "recursive": true
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="azure-data-lake-store-as-sink"></a>Azure Data Lake Store를 싱크로

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

다음은 형식 기반 복사 싱크의 `storeSettings` 설정에서 Azure Data Lake Store Gen1에 대해 지원 되는 속성입니다.

| 자산                 | 설명                                                  | 필수 |
| ------------------------ | ------------------------------------------------------------ | -------- |
| type                     | `storeSettings` 아래의 type 속성은 **AzureDataLakeStoreWriteSetting**로 설정 해야 합니다. | yes      |
| copyBehavior             | 원본이 파일 기반 데이터 저장소의 파일인 경우 복사 동작을 정의합니다.<br/><br/>허용되는 값은 다음과 같습니다.<br/><b>- PreserveHierarchy(기본값)</b>: 대상 폴더에서 파일 계층 구조를 유지합니다. 원본 폴더의 원본 파일 상대 경로는 대상 폴더의 대상 파일 상대 경로와 동일합니다.<br/><b>- FlattenHierarchy</b>: 원본 폴더의 모든 파일이 대상 폴더의 첫 번째 수준에 있게 됩니다. 대상 파일은 자동 생성된 이름을 갖습니다. <br/><b>- MergeFiles</b>: 원본 폴더의 모든 파일을 하나의 파일로 병합합니다. 파일 이름이 지정된 경우 병합되는 파일 이름은 지정된 이름입니다. 그렇지 않으면 자동 생성되는 파일 이름이 적용됩니다. | 아닙니다.       |
| maxConcurrentConnections | 데이터 저장소에 동시에 연결 하기 위한 연결 수입니다. 데이터 저장소에 대 한 동시 연결 수를 제한 하려는 경우에만를 지정 합니다. | 아닙니다.       |

**예제:**

```json
"activities":[
    {
        "name": "CopyToADLSGen1",
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
                    "type": "AzureDataLakeStoreWriteSetting",
                    "copyBehavior": "PreserveHierarchy"
                }
            }
        }
    }
]
```

#### <a name="legacy-sink-model"></a>레거시 싱크 모델

>[!NOTE]
>다음 복사 싱크 모델은 이전 버전과의 호환성을 위해 그대로 계속 지원 됩니다. 위에서 언급 한 새 모델을 사용 하는 것이 좋습니다. 그러면 ADF 제작 UI가 새 모델을 생성 하도록 전환 됩니다.

| 자산 | 설명 | 필수 |
|:--- |:--- |:--- |
| type | 복사 작업 싱크의 `type` 속성은 **AzureDataLakeStoreSink**로 설정 해야 합니다. |yes |
| copyBehavior | 원본이 파일 기반 데이터 저장소의 파일인 경우 복사 동작을 정의합니다.<br/><br/>허용되는 값은 다음과 같습니다.<br/><b>- PreserveHierarchy(기본값)</b>: 대상 폴더에서 파일 계층 구조를 유지합니다. 원본 폴더의 원본 파일 상대 경로는 대상 폴더의 대상 파일 상대 경로와 동일합니다.<br/><b>- FlattenHierarchy</b>: 원본 폴더의 모든 파일이 대상 폴더의 첫 번째 수준에 있게 됩니다. 대상 파일은 자동 생성된 이름을 갖습니다. <br/><b>- MergeFiles</b>: 원본 폴더의 모든 파일을 하나의 파일로 병합합니다. 파일 이름이 지정된 경우 병합되는 파일 이름은 지정된 이름입니다. 그렇지 않은 경우 파일 이름이 자동으로 생성됩니다. | 아닙니다. |
| maxConcurrentConnections | 데이터 저장소에 동시에 연결 하기 위한 연결 수입니다. 데이터 저장소에 대 한 동시 연결 수를 제한 하려는 경우에만를 지정 합니다. | 아닙니다. |

**예제:**

```json
"activities":[
    {
        "name": "CopyToADLSGen1",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<ADLS Gen1 output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "AzureDataLakeStoreSink",
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

### <a name="examples-of-behavior-of-the-copy-operation"></a>복사 작업의 동작 예제

이 섹션에서는 다양한 `recursive` 및 `copyBehavior` 값 조합에 대한 복사 작업의 결과 동작을 설명합니다.

| recursive | copyBehavior | 원본 폴더 구조 | 결과 대상 |
|:--- |:--- |:--- |:--- |
| true |preserveHierarchy | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | Folder1 대상이 다음과 같이 원본 폴더와 동일한 구조로 만들어집니다.<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5. |
| true |flattenHierarchy | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | Folder1 대상 폴더가 다음과 같은 구조로 만들어집니다. <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1에 대해 자동 생성된 이름<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2에 대해 자동 생성된 이름<br/>&nbsp;&nbsp;&nbsp;&nbsp;File3에 대해 자동 생성된 이름<br/>&nbsp;&nbsp;&nbsp;&nbsp;File4에 대해 자동 생성된 이름<br/>&nbsp;&nbsp;&nbsp;&nbsp;File5에 대해 자동 생성된 이름 |
| true |mergeFiles | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | Folder1 대상 폴더가 다음과 같은 구조로 만들어집니다. <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1 + File2 + File3 + File4 + File5.txt 콘텐츠는 자동으로 생성 된 파일 이름으로 하나의 파일로 병합 됩니다. |
| false |preserveHierarchy | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | Folder1 대상 폴더가 다음과 같은 구조로 만들어집니다.<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/><br/>File3, File4 및 File5.txt를 사용 하는 Subfolder1는 선택 되지 않습니다. |
| false |flattenHierarchy | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | Folder1 대상 폴더가 다음과 같은 구조로 만들어집니다.<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1에 대해 자동 생성된 이름<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2에 대해 자동 생성된 이름<br/><br/>File3, File4 및 File5.txt를 사용 하는 Subfolder1는 선택 되지 않습니다. |
| false |mergeFiles | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | Folder1 대상 폴더가 다음과 같은 구조로 만들어집니다.<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1 + File2 콘텐츠가 자동 생성 된 파일 이름을 가진 하나의 파일로 병합 됩니다. File1에 대해 자동 생성된 이름<br/><br/>File3, File4 및 File5.txt를 사용 하는 Subfolder1는 선택 되지 않습니다. |

## <a name="preserve-acls-to-data-lake-storage-gen2"></a>Data Lake Storage Gen2에 Acl 유지

Data Lake Storage Gen1에서 Data Lake Storage Gen2로 업그레이드 하는 경우 데이터 파일과 함께 Acl (액세스 제어 목록)을 복제 하려면 [Data Lake Storage Gen1에서 Acl 유지](connector-azure-data-lake-storage.md#preserve-acls-from-data-lake-storage-gen1)를 참조 하세요.

## <a name="mapping-data-flow-properties"></a>데이터 흐름 속성 매핑

데이터 흐름 매핑 기능에서 [원본 변환](data-flow-source.md) 및 [싱크 변환](data-flow-sink.md) 에 대해 자세히 알아보세요.

## <a name="lookup-activity-properties"></a>조회 작업 속성

속성에 대 한 자세한 내용을 보려면 [조회 작업](control-flow-lookup-activity.md)을 확인 하세요.

## <a name="getmetadata-activity-properties"></a>GetMetadata 활동 속성

속성에 대 한 자세한 내용을 보려면 [GetMetadata 활동](control-flow-get-metadata-activity.md) 을 확인 하세요. 

## <a name="delete-activity-properties"></a>작업 속성 삭제

속성에 대 한 자세한 내용을 보려면 [삭제 작업](delete-activity.md) 을 선택 합니다.

## <a name="next-steps"></a>다음 단계

Azure Data Factory에서 복사 작업의 원본 및 싱크로 지원되는 데이터 저장소 목록은 [지원되는 데이터 저장소](copy-activity-overview.md##supported-data-stores-and-formats)를 참조하세요.
