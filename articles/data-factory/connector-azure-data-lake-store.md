---
title: Azure 데이터 레이크 저장소 Gen1 간에 데이터 복사 Data Factory를 사용 하 여 | Microsoft Docs
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
ms.date: 05/13/2019
ms.author: jingwang
ms.openlocfilehash: aedfa381f6520a5295467821097b38dd28dcd60c
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67057921"
---
# <a name="copy-data-to-or-from-azure-data-lake-storage-gen1-using-azure-data-factory"></a>Azure 데이터 레이크 저장소 Gen1에서 데이터를 복사할 Azure Data Factory를 사용 하 여
> [!div class="op_single_selector" title1="사용 중인 Azure Data Factory의 버전을 선택 합니다."]
> * [버전 1](v1/data-factory-azure-datalake-connector.md)
> * [현재 버전](connector-azure-data-lake-store.md)

이 문서에서는 Azure Data Lake 저장소 Gen1 간에 데이터를 복사 하는 방법을 설명 합니다. Azure Data Factory에 대해 자세히 알아보려면 [소개 문서](introduction.md)를 참조하세요.

## <a name="supported-capabilities"></a>지원되는 기능

이 Azure 데이터 레이크 저장소 Gen1 커넥터는 다음 작업에 대 한 지원 됩니다.

- [복사 활동](copy-activity-overview.md) 사용 하 여 [매트릭스를 원본 또는 싱크를 지원 합니다.](copy-activity-overview.md)
- [데이터 흐름 매핑](concepts-data-flow-overview.md)
- [조회 작업](control-flow-lookup-activity.md)
- [GetMetadata 작업](control-flow-get-metadata-activity.md)

특히,이 커넥터를 사용 하 여 수행할 수 있습니다.

- 인증의 다음 메서드 중 하나를 사용 하 여 파일을 복사 합니다: Azure 리소스에 대 한 사용자 또는 관리 되는 id 서비스입니다.
- 구문 분석 또는 사용 하 여 파일을 생성 하는 대로 파일을 복사 합니다 [지원 되는 파일 형식 및 압축 코덱](supported-file-formats-and-compression-codecs.md)합니다.

> [!IMPORTANT]
> 자체 호스팅된 통합 런타임을 사용 하 여 데이터를 복사 하는 경우 아웃 바운드 트래픽을 허용 하도록 회사 방화벽을 구성 `<ADLS account name>.azuredatalakestore.net` 고 `login.microsoftonline.com/<tenant>/oauth2/token` 포트 443에서. 후자는 액세스 토큰을 가져오기 위해 통합 런타임이 통신해야 하는 Azure 보안 토큰 서비스입니다.

## <a name="get-started"></a>시작하기

> [!TIP]
> Azure Data Lake Store 커넥터를 사용 하는 방법의 연습을 참조 하세요 [Azure Data Lake Store로 데이터 로드](load-azure-data-lake-store.md)합니다.

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

다음 섹션에서는 Azure Data Lake Store에 한정 된 Data Factory 엔터티를 정의 하는 데 사용 되는 속성에 대 한 정보를 제공 합니다.

## <a name="linked-service-properties"></a>연결된 서비스 속성

Azure Data Lake Store 연결된 서비스에 다음 속성이 지원됩니다.

| 자산 | 설명 | 필수 |
|:--- |:--- |:--- |
| type | `type` 속성은 **AzureDataLakeStore**로 설정해야 합니다. | 예 |
| dataLakeStoreUri | Azure Data Lake Store 계정에 대한 정보. 이 정보는 `https://[accountname].azuredatalakestore.net/webhdfs/v1` 또는 `adl://[accountname].azuredatalakestore.net/` 형식 중 하나를 사용합니다. | 예 |
| subscriptionId | Data Lake Store 계정이 속하는 Azure 구독 ID입니다. | 싱크에 필요 |
| resourceGroupName | Data Lake Store 계정이 속하는 Azure 리소스 그룹 이름입니다. | 싱크에 필요 |
| connectVia | 데이터 저장소에 연결하는 데 사용할 [통합 런타임](concepts-integration-runtime.md)입니다. 데이터 저장소가 개인 네트워크에 있는 경우 Azure integration runtime 또는 자체 호스팅된 integration runtime을 사용할 수 있습니다. 이 속성이 지정 되지 않으면 기본 Azure integration runtime 사용 됩니다. |아닙니다. |

### <a name="use-service-principal-authentication"></a>서비스 주체 인증 사용

서비스 주체 인증을 사용하려면 Azure Active Directory에서 애플리케이션 엔터티를 등록한 후 이 엔터티에 Data Lake Store 액세스 권한을 부여합니다. 자세한 단계는 [서비스 간 인증](../data-lake-store/data-lake-store-authenticate-using-active-directory.md)을 참조하세요. 연결된 서비스를 정의하는 데 사용되므로 다음 값을 적어둡니다.

- 애플리케이션 UI
- 애플리케이션 키
- 테넌트 ID

>[!IMPORTANT]
> Data Lake Store에서 서비스 주체 적절 한 권한을 부여 합니다.
>- **원본으로**: **데이터 탐색기** > **액세스**에서 **읽기 + 실행** 이상의 권한을 부여하여 폴더 및 하위 폴더의 파일을 나열하고 복사합니다. 또는 단일 파일을 복사할 수 있는 **읽기** 권한을 부여할 수 있습니다. **이 폴더 및 모든 하위 폴더**에 재귀적으로 추가하고 **액세스 권한 및 기본 권한 항목**으로 추가하도록 선택할 수 있습니다. 계정 수준 액세스 제어 (IAM)에서 필요한 사항은 없습니다.
>- **싱크로**: **데이터 탐색기** > **액세스**에서 **쓰기 + 실행** 이상의 권한을 부여하여 폴더에 하위 항목을 만듭니다. **이 폴더 및 모든 하위 폴더**에 재귀적으로 추가하고 **액세스 권한 및 기본 권한 항목**으로 추가하도록 선택할 수 있습니다. Azure integration runtime을 사용 하 여 복사할 경우 (원본과 싱크가 모두 클라우드에)에 있는 IAM 부여 적어도 **판독기** Data Factory가 Data Lake Store의 지역을 감지 하기 위해 역할입니다. 이 IAM 역할을 방지하려면 Data Lake Store의 위치에 명시적으로 [Azure Integration Runtime을 만듭니다](create-azure-integration-runtime.md#create-azure-ir). 예를 들어, Data Lake Store 유럽 서 부에 있으면 Azure 통합 런타임 만들기 "유럽 서 부입니다."로 설정 하는 위치를 사용 하 여 다음 예제에서와 같이 Data Lake Store 연결 된 서비스에 연결 합니다.

>[!NOTE]
>폴더를 루트부터 나열하려면 **“실행” 권한을 가진 루트 수준에서** 부여할 서비스 주체의 권한을 설정해야 합니다. 다음을 사용하는 경우에도 적용됩니다.
>- **데이터 복사 도구** 복사 파이프라인을 작성 하려면.
>- **Data Factory UI** - 연결을 테스트하고 작성 중에 폴더를 탐색합니다.
>를 루트 수준에서 권한을 부여 하는 방법에 대 한 문제가 있는 경우 연결 테스트를 건너뛰고 작성 하는 동안 경로 수동으로 입력 합니다. 복사 작업으로 복사 될 파일에 적절 한 권한이 있는 서비스 주체는 부여 작동 합니다.

다음과 같은 속성이 지원됩니다.

| 속성 | 설명 | 필수 |
|:--- |:--- |:--- |
| servicePrincipalId | 애플리케이션의 클라이언트 ID를 지정합니다. | 예 |
| servicePrincipalKey | 애플리케이션의 키를 지정합니다. 이 필드를 `SecureString`으로 표시하여 Data Factory에 안전하게 저장하거나, [Azure Key Vault에 저장된 비밀을 참조](store-credentials-in-key-vault.md)합니다. | 예 |
| tenant | 도메인 이름과 같은 테 넌 트 정보를 지정 하거나 테 넌 트 ID, 응용 프로그램이 상주 하는 합니다. Azure Portal의 오른쪽 위 모서리에 마우스를 이동하여 검색할 수 있습니다. | 예 |

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

특정 데이터 팩터리를 나타내는 [Azure 리소스용 관리 ID](data-factory-service-identity.md)와 데이터 팩터리를 연결할 수 있습니다. 고유한 서비스 주체를 사용 하 여 Data Lake Store 인증에 대 한 직접 관리 되는이 id를 사용할 수 있습니다. 이 지정된 팩터리는 Data Lake Store 간에 데이터에 액세스하고 복사할 수 있습니다.

Azure 리소스 인증에 관리 ID를 사용하려면

1. [Data factory 관리 id 정보를 검색할](data-factory-service-identity.md#retrieve-managed-identity) 를 "서비스 Id 응용 프로그램 ID"의 팩터리와 함께 생성 된 값을 복사 하 여 합니다.
2. 이러한 정보에 따라 서비스 주체에서 수행한 동일한 방식으로 Data Lake Store로 관리 되는 id 액세스를 부여 합니다.

>[!IMPORTANT]
> Data Lake Store에서 data factory 관리 identity 적절 한 권한을 부여 해야 합니다.
>- **원본으로**: **데이터 탐색기** > **액세스**에서 **읽기 + 실행** 이상의 권한을 부여하여 폴더 및 하위 폴더의 파일을 나열하고 복사합니다. 또는 단일 파일을 복사할 수 있는 **읽기** 권한을 부여할 수 있습니다. **이 폴더 및 모든 하위 폴더**에 재귀적으로 추가하고 **액세스 권한 및 기본 권한 항목**으로 추가하도록 선택할 수 있습니다. 계정 수준 액세스 제어 (IAM)에서 필요한 사항은 없습니다.
>- **싱크로**: **데이터 탐색기** > **액세스**에서 **쓰기 + 실행** 이상의 권한을 부여하여 폴더에 하위 항목을 만듭니다. **이 폴더 및 모든 하위 폴더**에 재귀적으로 추가하고 **액세스 권한 및 기본 권한 항목**으로 추가하도록 선택할 수 있습니다. Azure integration runtime을 사용 하 여 복사할 경우 (원본과 싱크가 모두 클라우드에)에 있는 IAM 부여 적어도 **판독기** Data Factory가 Data Lake Store의 지역을 감지 하기 위해 역할입니다. 이 IAM 역할을 방지하려면 Data Lake Store의 위치에 명시적으로 [Azure Integration Runtime을 만듭니다](create-azure-integration-runtime.md#create-azure-ir). 다음 예제에서와 같이 Data Lake Store 연결 된 서비스에 연결 합니다.

>[!NOTE]
>목록에 폴더를 루트에서 시작, 관리 되는 id에 부여 된 사용 권한을 설정 해야 합니다 **"실행" 권한 사용 하 여 루트 수준**합니다. 다음을 사용하는 경우에도 적용됩니다.
>- **데이터 복사 도구** 복사 파이프라인을 작성 하려면.
>- **Data Factory UI** - 연결을 테스트하고 작성 중에 폴더를 탐색합니다.
>를 루트 수준에서 권한을 부여 하는 방법에 대 한 문제가 있는 경우 연결 테스트를 건너뛰고 작성 하는 동안 경로 수동으로 입력 합니다. 복사 작업으로 관리 되는 id 복사 될 파일에 적절 한 권한이 부여 되는 작동 합니다.

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

- Parquet에 구분 기호로 분리 된 텍스트 형식 참조를 [Parquet 및 구분 기호로 분리 된 텍스트 데이터 집합 형식](#parquet-and-delimited-text-format-dataset) 섹션입니다.
- ORC, Avro, JSON 또는 이진 형식이 같은 다른 형식에 대 한 참조를 [다른 형식으로 데이터 집합을](#other-format-dataset) 섹션입니다.

### <a name="parquet-and-delimited-text-format-dataset"></a>Parquet 및 구분 기호로 분리 된 텍스트 데이터 집합 형식

참조를 복사 하려면 데이터를 Azure Data Lake Store Gen1에서 parquet 또는 구분 기호로 분리 된 텍스트 형식으로 [Parquet 형식](format-parquet.md) 하 고 [구분 기호로 분리 된 텍스트 형식으로](format-delimited-text.md) 형식 기반의 데이터 집합 및 지원 되는 설정에 대 한 문서. Azure 데이터 레이크 저장소 Gen1 아래에서 다음 속성이 지원 됩니다 `location` 형식 기반의 데이터 집합의 설정:

| 자산   | 설명                                                  | 필수 |
| ---------- | ------------------------------------------------------------ | -------- |
| 형식       | Type 속성은 아래 `location` 데이터 집합에서으로 설정 되어 있어야 **AzureDataLakeStoreLocation**합니다. | 예      |
| folderPath | 폴더 경로입니다. 와일드 카드를 사용 하 여 필터링 하려는 경우 폴더는이 설정을 건너뛰고 활동 원본 설정에서 지정 합니다. | 아닙니다.       |
| fileName   | 지정 된 folderPath에서 파일 이름입니다. 와일드 카드를 사용 하 여 파일을 필터링 하려는 경우이 설정은 건너뛰고 활동 원본 설정에서 지정 합니다. | 아닙니다.       |

> [!NOTE]
>
> 합니다 **AzureDataLakeStoreFile** 다음 섹션에서 언급 하는 parquet 또는 텍스트 형식으로 데이터 집합 형식에는 복사, 조회 및 이전 버전과 호환성을 위해 GetMetadata 작업은 여전히 지원 됩니다. 하지만 매핑 데이터 흐름 기능을 사용 하 여 작동 하지 않습니다. 앞으로이 새 모델을 사용 하는 것이 좋습니다. 이러한 새 형식을 생성 하는 Data Factory UI를 작성 합니다.

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

### <a name="other-format-dataset"></a>다른 형식으로 데이터 집합

데이터를 ORC, Avro, JSON 또는 이진 형식에서 Azure Data Lake Store Gen1에서 복사 하려면 다음 속성이 지원 됩니다.

| 자산 | 설명 | 필수 |
|:--- |:--- |:--- |
| 형식 | 데이터 집합의 type 속성 설정 해야 합니다 **AzureDataLakeStoreFile**합니다. |예 |
| folderPath | Data Lake Store의 폴더 경로입니다. 지정하지 않으면 루트를 가리킵니다. <br/><br/>와일드 카드 필터는 지원. 와일드 카드는 허용 `*` (0 개 이상의 문자 일치) 및 `?` (0과 일치 한 문자 또는). 사용 하 여 `^` 에 실제 폴더 이름에 와일드 카드 또는 내에서이 이스케이프 문자를 이스케이프 합니다. <br/><br/>예를 들어: rootfolder/subfolder/입니다. 더 많은 예는 [폴더 및 파일 필터 예제](#folder-and-file-filter-examples)를 참조하세요. |아닙니다. |
| fileName | 지정 된 "folderPath"에 있는 파일에 대 한 이름 또는 와일드 카드 필터입니다. 이 속성의 값을 지정하지 않으면 데이터 세트는 폴더에 있는 모든 파일을 가리킵니다. <br/><br/>허용 되는 와일드 카드를 필터에 대 한 `*` (0 개 이상의 문자 일치) 및 `?` (0과 일치 또는 단일 문자).<br/>- 예 1: `"fileName": "*.csv"`<br/>- 예 2: `"fileName": "???20180427.txt"`<br/>사용 하 여 `^` 실제 파일 이름에 와일드 카드 또는 내에서이 이스케이프 문자를 이스케이프 합니다.<br/><br/>fileName이 출력 데이터 세트에 대해 지정되지 않고 **preserveHierarchy**가 작업 싱크에 지정되지 않으면, 복사 작업은 다음과 같은 패턴으로 파일 이름을 자동으로 생성합니다. "*데이터입니다. [작업 실행 ID GUID]. [GUID 경우 FlattenHierarchy]. [형식 구성 된 경우]입니다. [구성 된 경우에 압축]* ", 예를 들어," Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt.gz "입니다. 이름 패턴은 쿼리 하는 대신 테이블 이름을 사용 하 여 테이블 형식 원본에서 복사 하는 경우 " *[테이블 이름]. [ 형식]입니다. [구성 된 경우에 압축]* ", 예를 들어,"MyTable.csv"입니다. |아닙니다. |
| modifiedDatetimeStart | 마지막으로 수정한 날짜 특성을 기반으로 하는 파일 필터입니다. 마지막으로 수정 된 시간 사이의 시간 범위 내에 있으면 파일이 선택 됩니다 `modifiedDatetimeStart` 고 `modifiedDatetimeEnd`입니다. 시간 형식의 UTC 표준 시간대에 적용 됩니다 "2018-12-01T05:00:00Z"입니다. <br/><br/> 대용량 파일을 사용 하 여 필터 파일 수행 하려는 경우이 설정을 사용 하 여 데이터 이동의 전반적인 성능이 저하 됩니다. <br/><br/> 속성에는 파일 특성 필터가 적용 되지 않은 데이터 집합에는 NULL 일 수입니다. 때 `modifiedDatetimeStart` 날짜/시간 값이 있지만 `modifiedDatetimeEnd` null, 즉, 마지막으로 수정 된 속성을 가진 보다 큽니다. 파일 또는 datetime 값 같음 선택 됩니다. 때 `modifiedDatetimeEnd` 날짜/시간 값이 있지만 `modifiedDatetimeStart` 가 null 인 경우 즉, 인 마지막으로 수정 된 속성을 사용 하면 날짜/시간 값 보다 작으면 파일이 선택 됩니다.| 아닙니다. |
| modifiedDatetimeEnd | 마지막으로 수정한 날짜 특성을 기반으로 하는 파일 필터입니다. 마지막으로 수정 된 시간 사이의 시간 범위 내에 있으면 파일이 선택 됩니다 `modifiedDatetimeStart` 고 `modifiedDatetimeEnd`입니다. 시간 형식의 UTC 표준 시간대에 적용 됩니다 "2018-12-01T05:00:00Z"입니다. <br/><br/> 대용량 파일을 사용 하 여 필터 파일 수행 하려는 경우이 설정을 사용 하 여 데이터 이동의 전반적인 성능이 저하 됩니다. <br/><br/> 속성에는 파일 특성 필터가 적용 되지 않은 데이터 집합에는 NULL 일 수입니다. 때 `modifiedDatetimeStart` 날짜/시간 값이 있지만 `modifiedDatetimeEnd` null, 즉, 마지막으로 수정 된 속성을 가진 보다 큽니다. 파일 또는 datetime 값 같음 선택 됩니다. 때 `modifiedDatetimeEnd` 날짜/시간 값이 있지만 `modifiedDatetimeStart` 가 null 인 경우 즉, 인 마지막으로 수정 된 속성을 사용 하면 날짜/시간 값 보다 작으면 파일이 선택 됩니다.| 아닙니다. |
| format | 파일 기반 저장소 (이진 복사) 간에 있는 그대로 파일 복사 하려는 경우 입력 및 출력 데이터 집합 정의 둘 다에서 형식 섹션을 건너뜁니다.<br/><br/>특정 형식의 파일을 구문 분석하거나 생성하려는 경우, 지원되는 파일 형식 유형은 **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat** 및 **ParquetFormat**입니다. **format**의 **type** 속성을 이 값 중 하나로 설정합니다. 자세한 내용은 [텍스트 형식](supported-file-formats-and-compression-codecs.md#text-format), [JSON 형식](supported-file-formats-and-compression-codecs.md#json-format), [Avro 형식](supported-file-formats-and-compression-codecs.md#avro-format), [Orc 형식](supported-file-formats-and-compression-codecs.md#orc-format) 및 [Parquet 형식](supported-file-formats-and-compression-codecs.md#parquet-format) 섹션을 참조하세요. |아니요(이진 복사 시나리오에만 해당) |
| compression | 데이터에 대한 압축 유형 및 수준을 지정합니다. 자세한 내용은 [지원되는 파일 형식 및 압축 코덱](supported-file-formats-and-compression-codecs.md#compression-support)을 참조하세요.<br/>지원되는 형식은 **GZip**, **Deflate**, **BZip2** 및 **ZipDeflate**입니다.<br/>**Optimal** 및 **Fastest** 수준이 지원됩니다. |아닙니다. |


>[!TIP]
>폴더 아래에서 모든 파일을 복사하려면 **folderPath**만을 지정합니다.<br>특정 이름 가진 단일 파일을 복사 하려면 지정 **folderPath** 폴더 파트와 및 **fileName** 파일 이름입니다.<br>폴더 아래에 있는 파일의 하위 집합을 복사 하려면 지정 **folderPath** 폴더 파트와 및 **fileName** 와일드 카드 필터를 사용 합니다. 

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

- Parquet 또는 구분 기호로 분리 된 텍스트 형식으로 복사할 참조를 [Parquet 및 구분 기호로 분리 된 텍스트 형식으로 원본](#parquet-and-delimited-text-format-source) 섹션입니다.
- ORC, Avro, JSON 또는 이진 형식 등의 다른 형식에서 복사할 참조를 [다른 형식으로 원본](#other-format-source) 섹션입니다.

#### <a name="parquet-and-delimited-text-format-source"></a>Parquet 및 구분 기호로 분리 된 텍스트 형식으로 원본

Parquet 또는 구분 기호로 분리 된 텍스트 형식으로 Azure Data Lake Store Gen1에서 데이터를 복사, 참조를 [Parquet 형식](format-parquet.md) 하 고 [구분 기호로 분리 된 텍스트 형식으로](format-delimited-text.md) 형식 기반 복사 작업 원본과 지원 되는 설정에 대 한 문서. Azure 데이터 레이크 저장소 Gen1 아래에서 다음 속성이 지원 됩니다 `storeSettings` 형식 기반 복사 원본에 설정 합니다.

| 자산                 | 설명                                                  | 필수                                      |
| ------------------------ | ------------------------------------------------------------ | --------------------------------------------- |
| type                     | 아래에 있는 type 속성은 `storeSettings` 으로 설정 되어 있어야 **AzureDataLakeStoreReadSetting**합니다. | 예                                           |
| recursive                | 하위 폴더 또는 지정된 폴더에서만 데이터를 재귀적으로 읽을지 여부를 나타냅니다. 재귀로 설정 된 경우 true이 고 싱크에 파일 기반 저장소, 빈 폴더 또는 하위 폴더 복사 되지 않습니다 또는 싱크에 작성 합니다. 허용되는 값은 **true**(기본값) 및 **false**입니다. | 아닙니다.                                            |
| wildcardFolderPath       | 소스 폴더를 필터링 하려면 와일드 카드 문자를 사용 하 여 폴더 경로입니다. <br>와일드 카드는 허용 `*` (0 개 이상의 문자 일치) 및 `?` (0과 일치 한 문자 또는). 사용 하 여 `^` 에 실제 폴더 이름에 와일드 카드 또는 내에서이 이스케이프 문자를 이스케이프 합니다. <br>더 많은 예는 [폴더 및 파일 필터 예제](#folder-and-file-filter-examples)를 참조하세요. | 아닙니다.                                            |
| wildcardFileName         | 필터 소스 파일에 지정 된 folderPath/wildcardFolderPath에서 와일드 카드 문자를 사용 하 여 파일 이름입니다. <br>와일드 카드는 허용 `*` (0 개 이상의 문자 일치) 및 `?` (0과 일치 한 문자 또는). 사용 하 여 `^` 에 실제 폴더 이름에 와일드 카드 또는 내에서이 이스케이프 문자를 이스케이프 합니다. 더 많은 예는 [폴더 및 파일 필터 예제](#folder-and-file-filter-examples)를 참조하세요. | 경우에 예 `fileName` 데이터 집합에서 지정 하지 않으면 |
| modifiedDatetimeStart    | 마지막으로 수정한 날짜 특성을 기반으로 하는 파일 필터입니다. 마지막으로 수정 된 시간 사이의 시간 범위 내에 있으면 파일이 선택 됩니다 `modifiedDatetimeStart` 고 `modifiedDatetimeEnd`입니다. 시간 형식의 UTC 표준 시간대에 적용 됩니다 "2018-12-01T05:00:00Z"입니다. <br> 속성에는 파일 특성 필터가 적용 되지 않은 데이터 집합에는 NULL 일 수입니다. 때 `modifiedDatetimeStart` 날짜/시간 값이 있지만 `modifiedDatetimeEnd` null, 즉, 마지막으로 수정 된 속성을 가진 보다 큽니다. 파일 또는 datetime 값 같음 선택 됩니다. 때 `modifiedDatetimeEnd` 날짜/시간 값이 있지만 `modifiedDatetimeStart` 가 null 인 경우 즉, 인 마지막으로 수정 된 속성을 사용 하면 날짜/시간 값 보다 작으면 파일이 선택 됩니다. | 아닙니다.                                            |
| modifiedDatetimeEnd      | 위와 동일합니다.                                               | 아닙니다.                                            |
| maxConcurrentConnections | Storage 저장소에 동시에 연결 하는 연결 횟수입니다. 데이터 저장소에 대 한 동시 연결을 제한 하려는 경우에 지정 합니다. | 아닙니다.                                            |

> [!NOTE]
> Parquet에 구분 기호로 분리 된 텍스트 형식으로 된 **AzureDataLakeStoreSource** 다음 섹션에 언급 된 형식 복사 활동 source는 이전 버전과 호환성을 위해 그대로 계속 지원 됩니다. 앞으로이 새 모델을 사용 하는 것이 좋습니다. 이러한 새 형식을 생성 하는 Data Factory UI를 작성 합니다.

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

#### <a name="other-format-source"></a>다른 형식으로 원본

ORC, Avro, JSON 또는 이진 형식에서 Azure Data Lake Store Gen1에서 데이터를 복사 하려면 복사 작업의 다음 속성을 지 **원본** 섹션:

| 자산 | 설명 | 필수 |
|:--- |:--- |:--- |
| 형식 | 합니다 `type` 복사 활동 source의 속성으로 설정 되어 있어야 **AzureDataLakeStoreSource**합니다. |예 |
| recursive | 하위 폴더 또는 지정된 폴더에서만 데이터를 재귀적으로 읽을지 여부를 나타냅니다. 때 `recursive` true이 고 싱크에로 설정 되는 파일 기반 저장소에 빈 폴더 또는 하위 폴더 복사 또는 싱크에 생성 되지 않습니다. 허용되는 값은 **true**(기본값) 및 **false**입니다. | 아닙니다. |
| maxConcurrentConnections | 데이터 저장소에 동시에 연결할 연결 횟수입니다. 데이터 저장소에 대 한 동시 연결을 제한 하려는 경우에 지정 합니다. | 아닙니다. |

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

- Parquet 및 구분 기호로 분리 된 텍스트 형식에 복사 하려면 참조는 [Parquet 및 구분 기호로 분리 된 텍스트 형식 싱크](#parquet-and-delimited-text-format-sink) 섹션입니다.
- ORC, Avro, JSON 또는 이진 형식 등의 다른 형식에 복사 하려면 참조는 [다른 형식 싱크](#other-format-sink) 섹션입니다.

#### <a name="parquet-and-delimited-text-format-sink"></a>Parquet 및 구분 기호로 분리 된 텍스트 형식 싱크

데이터를 복사할 Azure Data Lake Store Gen1 parquet 또는 구분 기호로 분리 된 텍스트 형식으로, 참조를 [Parquet 형식](format-parquet.md) 하 고 [구분 기호로 분리 된 텍스트 형식으로](format-delimited-text.md) 형식 기반 복사 활동 싱크 및 지원 되는 설정에 대 한 문서. Azure 데이터 레이크 저장소 Gen1 아래에서 다음 속성이 지원 됩니다 `storeSettings` 형식 기반 복사 싱크를 설정 합니다.

| 자산                 | 설명                                                  | 필수 |
| ------------------------ | ------------------------------------------------------------ | -------- |
| type                     | 아래에 있는 type 속성은 `storeSettings` 으로 설정 되어 있어야 **AzureDataLakeStoreWriteSetting**합니다. | 예      |
| copyBehavior             | 원본이 파일 기반 데이터 저장소의 파일인 경우 복사 동작을 정의합니다.<br/><br/>허용되는 값은 다음과 같습니다.<br/><b>- PreserveHierarchy(기본값)</b>: 대상 폴더에서 파일 계층 구조를 유지합니다. 원본 폴더의 원본 파일 상대 경로는 대상 폴더의 대상 파일 상대 경로와 동일합니다.<br/><b>- FlattenHierarchy</b>: 원본 폴더의 모든 파일이 대상 폴더의 첫 번째 수준에 있습니다. 대상 파일은 자동 생성된 이름을 갖습니다. <br/><b>- MergeFiles</b>: 원본 폴더의 모든 파일을 하나의 파일로 병합합니다. 파일 이름이 지정된 경우 병합되는 파일 이름은 지정된 이름입니다. 그렇지 않으면 자동 생성되는 파일 이름이 적용됩니다. | 아닙니다.       |
| maxConcurrentConnections | 데이터 저장소에 동시에 연결할 연결 횟수입니다. 데이터 저장소에 대 한 동시 연결을 제한 하려는 경우에 지정 합니다. | 아닙니다.       |

> [!NOTE]
> Parquet에 구분 기호로 분리 된 텍스트 형식으로 된 **AzureDataLakeStoreSink** 다음 섹션에 언급 된 형식 복사 활동 sink는 이전 버전과 호환성을 위해 그대로 계속 지원 됩니다. 앞으로이 새 모델을 사용 하는 것이 좋습니다. 이러한 새 형식을 생성 하는 Data Factory UI를 작성 합니다.

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

#### <a name="other-format-sink"></a>다른 형식 싱크

ORC, Avro, JSON 또는 이진 형식에서 Azure Data Lake Store Gen1에 데이터를 복사 하려면 다음 속성의 지 합니다 **싱크** 섹션:

| 자산 | 설명 | 필수 |
|:--- |:--- |:--- |
| 형식 | 합니다 `type` 복사 활동 sink의 속성으로 설정 되어 있어야 **AzureDataLakeStoreSink**합니다. |예 |
| copyBehavior | 원본이 파일 기반 데이터 저장소의 파일인 경우 복사 동작을 정의합니다.<br/><br/>허용되는 값은 다음과 같습니다.<br/><b>- PreserveHierarchy(기본값)</b>: 대상 폴더에서 파일 계층 구조를 유지합니다. 원본 폴더의 원본 파일 상대 경로는 대상 폴더의 대상 파일 상대 경로와 동일합니다.<br/><b>- FlattenHierarchy</b>: 원본 폴더의 모든 파일이 대상 폴더의 첫 번째 수준에 있습니다. 대상 파일은 자동 생성된 이름을 갖습니다. <br/><b>- MergeFiles</b>: 원본 폴더의 모든 파일을 하나의 파일로 병합합니다. 파일 이름이 지정된 경우 병합되는 파일 이름은 지정된 이름입니다. 그렇지 않은 경우 파일 이름이 자동으로 생성됩니다. | 아닙니다. |
| maxConcurrentConnections | 데이터 저장소에 동시에 연결할 연결 횟수입니다. 데이터 저장소에 대 한 동시 연결을 제한 하려는 경우에 지정 합니다. | 아닙니다. |

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
| `Folder*` | (빈, 기본값 사용) | false | FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5.csv<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `Folder*` | (빈, 기본값 사용) | true | FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File4.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `Folder*` | `*.csv` | false | FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5.csv<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `Folder*` | `*.csv` | true | FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |

### <a name="examples-of-behavior-of-the-copy-operation"></a>복사 작업의 동작 예제

이 섹션에서는 다양한 `recursive` 및 `copyBehavior` 값 조합에 대한 복사 작업의 결과 동작을 설명합니다.

| recursive | copyBehavior | 원본 폴더 구조 | 결과 대상 |
|:--- |:--- |:--- |:--- |
| true |preserveHierarchy | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | Folder1 대상이 다음과 같이 원본 폴더와 동일한 구조로 만들어집니다.<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5. |
| true |flattenHierarchy | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | Folder1 대상 폴더가 다음과 같은 구조로 만들어집니다. <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1에 대해 자동 생성된 이름<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2에 대해 자동 생성된 이름<br/>&nbsp;&nbsp;&nbsp;&nbsp;File3에 대해 자동 생성된 이름<br/>&nbsp;&nbsp;&nbsp;&nbsp;File4에 대해 자동 생성된 이름<br/>&nbsp;&nbsp;&nbsp;&nbsp;File5에 대해 자동 생성된 이름 |
| true |mergeFiles | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | Folder1 대상 폴더가 다음과 같은 구조로 만들어집니다. <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1과 File2 + File3 + file4 및 File5 내용이 자동 생성 된 파일 이름이 있는 하나의 파일로 병합 됩니다. |
| false |preserveHierarchy | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | Folder1 대상 폴더가 다음과 같은 구조로 만들어집니다.<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/><br/>File3, File4, file5를 가진 subfolder1은 선택 되지 않습니다. |
| false |flattenHierarchy | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | Folder1 대상 폴더가 다음과 같은 구조로 만들어집니다.<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1에 대해 자동 생성된 이름<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2에 대해 자동 생성된 이름<br/><br/>File3, File4, file5를 가진 subfolder1은 선택 되지 않습니다. |
| false |mergeFiles | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | Folder1 대상 폴더가 다음과 같은 구조로 만들어집니다.<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1 +과 File2의 내용이 자동 생성 된 파일 이름이 있는 하나의 파일로 병합 됩니다. File1에 대해 자동 생성된 이름<br/><br/>File3, File4, file5를 가진 subfolder1은 선택 되지 않습니다. |

## <a name="preserve-acls-to-data-lake-storage-gen2"></a>Acl 데이터 레이크 저장소 Gen2로 유지

복제 하려는 경우 액세스 제어 목록 (Acl 데이터 파일과 함께 데이터 레이크 저장소 Gen1에서 Data Lake 저장소 Gen2로 업그레이드 하는 경우), 참조 [Data Lake 저장소 Gen1에서 Acl 유지](connector-azure-data-lake-storage.md#preserve-acls-from-data-lake-storage-gen1)합니다.

## <a name="mapping-data-flow-properties"></a>데이터 흐름 속성 매핑

에 대해 자세히 알아보세요 [소스 변환](data-flow-source.md) 하 고 [변환 싱크](data-flow-sink.md) 매핑 데이터 흐름 기능에서입니다.

## <a name="next-steps"></a>다음 단계

Azure Data Factory에서 복사 작업의 원본 및 싱크로 지원되는 데이터 저장소 목록은 [지원되는 데이터 저장소](copy-activity-overview.md##supported-data-stores-and-formats)를 참조하세요.
