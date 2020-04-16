---
title: 아마존 단순 스토리지 서비스(S3)에서 데이터 복사
description: Azure Data Factory를 사용하여 Amazon S3(단순 저장 서비스)에서 지원되는 싱크 데이터 스토리지로 데이터를 복사하는 방법에 대해 알아봅니다.
services: data-factory
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 12/13/2019
ms.openlocfilehash: 6975c876bb31e36b7b6268d284f747fd51803d63
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81415465"
---
# <a name="copy-data-from-amazon-simple-storage-service-using-azure-data-factory"></a>Azure Data Factory를 사용하여 Amazon 단순 스토리지 서비스에서 데이터 복사
> [!div class="op_single_selector" title1="사용 중인 Data Factory 서비스 버전을 선택합니다."]
>
> * [버전 1](v1/data-factory-amazon-simple-storage-service-connector.md)
> * [현재 버전](connector-amazon-simple-storage-service.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

이 문서에서는 Amazon 단순 스토리지 서비스(Amazon S3)에서 데이터를 복사하는 방법을 설명합니다. Azure Data Factory에 대해 자세히 알아보려면 [소개 문서](introduction.md)를 참조하세요.

>[!TIP]
>Amazon S3에서 Azure 저장소로의 데이터 마이그레이션 시나리오의 경우 [Azure 데이터 팩터리 사용에서 Amazon S3에서 Azure 저장소로 데이터를 마이그레이션하는](data-migration-guidance-s3-azure-storage.md)방법에 대해 자세히 알아봅니다.

## <a name="supported-capabilities"></a>지원되는 기능

이 Amazon S3 커넥터는 다음 활동에 대해 지원됩니다.

- [지원되는 소스/싱크 매트릭스로](copy-activity-overview.md) [활동 복사](copy-activity-overview.md)
- [조회 활동](control-flow-lookup-activity.md)
- [GetMetadata 작업](control-flow-get-metadata-activity.md)
- [활동 삭제](delete-activity.md)

특히, Amazon S3 커넥터는 이 파일을 있는 그대로 복사 또는 [지원되는 파일 형식 및 압축 코덱](supported-file-formats-and-compression-codecs.md)을 사용하여 파일 붙여넣기를 지원합니다. [복사 하는 동안 파일 메타 데이터 보존을](#preserve-metadata-during-copy)선택할 수도 있습니다. 커넥터는 [AWS 서명 버전 4를](https://docs.aws.amazon.com/general/latest/gr/signature-version-4.html) 사용하여 S3에 대한 요청을 인증합니다.

>[!TIP]
>이 Amazon S3 커넥터를 사용하여 **S3 호환 스토리지 공급 기업**(예: [Google Cloud Storage](connector-google-cloud-storage.md))의 데이터를 복사할 수 있습니다. 연결된 서비스 구성에서 해당 서비스 URL을 지정합니다.

## <a name="required-permissions"></a>필요한 사용 권한

Amazon S3에서 데이터를 복사하려면 다음과 같은 권한이 부여되어 있는지 확인합니다.

- **복사 활동 실행:** `s3:GetObject` `s3:GetObjectVersion` : 및 Amazon S3 개체 작업의 경우.
- **데이터 팩터리 GUI 작성의 경우** `s3:ListAllMyBuckets` : Amazon `s3:ListBucket` / `s3:GetBucketLocation` S3 버킷 작업 권한은 테스트 연결 및 파일 경로 탐색과 같은 작업에 추가로 필요합니다. 이러한 권한을 부여하지 않으려면 연결된 서비스 만들기 페이지에서 테스트 연결을 건너뛰고 데이터 세트 설정에서 직접 경로를 지정합니다.

Amazon S3 사용 권한의 전체 목록은 [정책에서 사용 권한 지정](https://docs.aws.amazon.com/AmazonS3/latest/dev/using-with-s3-actions.html)을 참조하세요.

## <a name="getting-started"></a>시작

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)] 

다음 섹션에서는 Amazon S3에 한정된 Data Factory 엔터티를 정의하는 데 사용되는 속성에 대해 자세히 설명합니다.

## <a name="linked-service-properties"></a>연결된 서비스 속성

Amazon S3 연결된 서비스에 다음 속성이 지원됩니다.

| 속성 | Description | 필수 |
|:--- |:--- |:--- |
| type | 형식 속성은 **AmazonS3**으로 설정되어야 합니다. | 예 |
| accessKeyId | 비밀 액세스 키의 ID입니다. |예 |
| secretAccessKey | 비밀 액세스 키 자체입니다. 이 필드를 SecureString으로 표시하여 Data Factory에 안전하게 저장하거나, [Azure Key Vault에 저장된 비밀을 참조](store-credentials-in-key-vault.md)합니다. |예 |
| serviceUrl | 공식 Amazon S3 서비스가 아닌 S3 호환 스토리지 공급 기업의 데이터를 복사하는 경우 사용자 지정 S3 엔드포인트를 지정합니다. 예를 들어 Google Cloud Storage에서 데이터를 복사하려면 `https://storage.googleapis.com`을 지정합니다. | 예 |
| connectVia | 데이터 저장소에 연결하는 데 사용할 [통합 런타임입니다.](concepts-integration-runtime.md) Azure Integration Runtime 또는 자체 호스팅 Integration Runtime을 사용할 수 있습니다(데이터 저장소가 프라이빗 네트워크에 있는 경우). 지정하지 않으면 기본 Azure Integration Runtime을 사용합니다. |예 |

>[!TIP]
>공식 Amazon S3 서비스가 아닌 S3 호환 스토리지의 데이터를 복사하는 경우 사용자 지정 S3 서비스 URL을 지정합니다.

>[!NOTE]
>이 커넥터를 사용하려면 Amazon S3에서 데이터를 복사하기 위해 IAM 계정에 대한 액세스 키가 필요합니다. [임시 보안 자격 증명](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_credentials_temp.html)은 지원되지 않습니다.
>

다음은 예제입니다.

```json
{
    "name": "AmazonS3LinkedService",
    "properties": {
        "type": "AmazonS3",
        "typeProperties": {
            "accessKeyId": "<access key id>",
            "secretAccessKey": {
                "type": "SecureString",
                "value": "<secret access key>"
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

데이터 세트 정의에 사용할 수 있는 섹션 및 속성의 전체 목록은 [데이터 세트](concepts-datasets-linked-services.md) 문서를 참조하세요. 

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

형식 기반 데이터 집합의 설정에서 `location` Amazon S3에 대해 다음 속성이 지원됩니다.

| 속성   | Description                                                  | 필수 |
| ---------- | ------------------------------------------------------------ | -------- |
| type       | 데이터 집합에서 `location` 아래의 형식 속성은 **AmazonS3Location**으로 설정해야 합니다. | 예      |
| bucketName | S3 버킷 이름입니다.                                          | 예      |
| folderPath | 지정된 버킷 아래의 폴더 경로입니다. 와일드카드를 사용하여 폴더를 필터링하려면 이 설정을 건너뛰고 활동 소스 설정에서 지정합니다. | 예       |
| fileName   | 지정된 버킷 + folder 패스 아래의 파일 이름입니다. 와일드카드를 사용하여 파일을 필터링하려면 이 설정을 건너뛰고 활동 소스 설정에서 지정합니다. | 예       |
| 버전 | S3 버전 관리를 사용하도록 설정하면 S3 개체의 버전입니다. 지정하지 않으면 최신 버전이 가져옵니다. |예 |

**예제:**

```json
{
    "name": "DelimitedTextDataset",
    "properties": {
        "type": "DelimitedText",
        "linkedServiceName": {
            "referenceName": "<Amazon S3 linked service name>",
            "type": "LinkedServiceReference"
        },
        "schema": [ < physical schema, optional, auto retrieved during authoring > ],
        "typeProperties": {
            "location": {
                "type": "AmazonS3Location",
                "bucketName": "bucketname",
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

작업 정의에 사용할 수 있는 섹션 및 속성의 전체 목록은 [파이프라인](concepts-pipelines-activities.md) 문서를 참조하세요. 이 섹션에서는 Amazon S3 원본에서 지원하는 속성의 목록을 제공합니다.

### <a name="amazon-s3-as-source"></a>원본으로 Amazon S3

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

형식 기반 복사 소스의 설정에서 `storeSettings` Amazon S3에 대해 다음 속성이 지원됩니다.

| 속성                 | Description                                                  | 필수                                                    |
| ------------------------ | ------------------------------------------------------------ | ----------------------------------------------------------- |
| type                     | 아래의 `storeSettings` 형식 속성은 **AmazonS3ReadSettings**로 설정해야 합니다. | 예                                                         |
| recursive                | 하위 폴더 또는 지정된 폴더에서만 데이터를 재귀적으로 읽을지 여부를 나타냅니다. recursive를 true로 설정하고 싱크가 파일 기반 저장소인 경우 빈 폴더 또는 하위 폴더가 싱크에 복사되거나 만들어지지 않습니다. 허용된 **true** 값은 true(기본값) 및 **false입니다.** | 예                                                          |
| 접두사                   | 원본 개체를 필터링하기 위해 데이터 집합에 구성된 지정된 버킷 아래의 S3 개체 키에 대한 접두사입니다. 이 접두사로 시작하는 키를 가진 개체가 선택됩니다. <br>속성이 `wildcardFileName` `wildcardFolderPath` 지정되지 않은 경우에만 적용됩니다. | 예                                                          |
| 와일드 카드폴더패스       | 원본 폴더를 필터링하기 위해 데이터 집합에 구성된 지정된 버킷 아래에 와일드카드 문자가 있는 폴더 경로입니다. <br>허용되는 와일드카드는 `*`(0개 이상의 문자 일치) 및 `?`(0-1개의 문자 일치)입니다. 실제 폴더 이름에 와일드카드 또는 이 이스케이프 문자가 있는 경우 `^`을 사용하여 이스케이프합니다. <br>더 많은 예는 [폴더 및 파일 필터 예제](#folder-and-file-filter-examples)를 참조하세요. | 예                                                          |
| 와일드카드파일이름         | 지정된 버킷 아래에 와일드카드 문자가 있는 파일 이름 + folderPath/와일드카드FolderPath를 사용하여 소스 파일을 필터링합니다. <br>허용되는 와일드카드는 `*`(0개 이상의 문자 일치) 및 `?`(0-1개의 문자 일치)입니다. 실제 폴더 이름에 와일드카드 또는 이 이스케이프 문자가 있는 경우 `^`을 사용하여 이스케이프합니다.  더 많은 예는 [폴더 및 파일 필터 예제](#folder-and-file-filter-examples)를 참조하세요. | 데이터 `fileName` 집합에 `prefix` 있고 지정되지 않은 경우 예 |
| modifiedDatetimeStart    | 파일은 특성: 마지막으로 수정된 특성을 기반으로 필터링합니다. 마지막 수정 시간이 `modifiedDatetimeStart`와 `modifiedDatetimeEnd` 사이의 시간 범위 내에 있으면 파일이 선택됩니다. 시간은 UTC 표준 시간대에 "2018-12-01T05:00:00Z" 형식으로 적용됩니다. <br> 속성은 NULL일 수 있습니다. 이 경우 파일 특성 필터가 데이터 세트에 적용되지 않습니다.  `modifiedDatetimeStart`에 datetime 값이 있지만 `modifiedDatetimeEnd`가 NULL이면, 마지막으로 수정된 특성이 datetime 값보다 크거나 같은 파일이 선택됩니다.  `modifiedDatetimeEnd`에 datetime 값이 있지만 `modifiedDatetimeStart`가 NULL이면, 마지막으로 수정된 특성이 datetime 값보다 작은 파일이 선택됩니다. | 예                                                          |
| modifiedDatetimeEnd      | 위와 동일합니다.                                               | 예                                                          |
| maxConcurrentConnections | 저장소 저장소에 동시에 연결할 연결 수입니다. 데이터 저장소에 대한 동시 연결을 제한하려는 경우에만 지정합니다. | 예                                                          |

**예제:**

```json
"activities":[
    {
        "name": "CopyFromAmazonS3",
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
                    "type": "AmazonS3ReadSettings",
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

### <a name="folder-and-file-filter-examples"></a>폴더 및 파일 필터 예제

이 섹션에서는 와일드카드 필터가 있는 폴더 경로 및 파일 이름의 결과 동작에 대해 설명합니다.

| bucket | key | recursive | 원본 폴더 구조 및 필터 결과(굵게 표시된 파일이 검색됨)|
|:--- |:--- |:--- |:--- |
| bucket | `Folder*/*` | false | bucket<br/>&nbsp;&nbsp;&nbsp;&nbsp;FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| bucket | `Folder*/*` | true | bucket<br/>&nbsp;&nbsp;&nbsp;&nbsp;FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**파일3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File4.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| bucket | `Folder*/*.csv` | false | bucket<br/>&nbsp;&nbsp;&nbsp;&nbsp;FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| bucket | `Folder*/*.csv` | true | bucket<br/>&nbsp;&nbsp;&nbsp;&nbsp;FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**파일3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |

## <a name="preserve-metadata-during-copy"></a>복사 하는 동안 메타데이터 보존

Amazon S3에서 Azure 데이터 레이크 저장소 Gen2/Azure Blob로 파일을 복사할 때 데이터와 함께 파일 메타데이터를 보존하도록 선택할 수 있습니다. [메타데이터 보존에서](copy-activity-preserve-metadata.md#preserve-metadata)자세히 알아보십시오.

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

| 속성 | Description | 필수 |
|:--- |:--- |:--- |
| type | 데이터 세트의 type 속성을 **AmazonS3Object**로 설정해야 합니다. |예 |
| bucketName | S3 버킷 이름입니다. 와일드카드 필터는 지원되지 않습니다. |Copy/Lookup 활동의 경우 예, GetMetadata 활동의 경우 아니요 |
| key | 지정된 버킷에서 S3 개체 키의 **이름 또는 와일드카드 필터**입니다. "prefix" 속성이 지정되어 있지 않은 경우에만 적용됩니다. <br/><br/>와일드카드 필터가 폴더 부분과 파일 이름 부분에 모두 지원됩니다. 허용되는 와일드카드는 `*`(문자 0자 이상 일치) 및 `?`(문자 0자 또는 1자 일치)입니다.<br/>- 예 1: `"key": "rootfolder/subfolder/*.csv"`<br/>- 예 2: `"key": "rootfolder/subfolder/???20180427.txt"`<br/>더 많은 예는 [폴더 및 파일 필터 예제](#folder-and-file-filter-examples)를 참조하세요. 실제 폴더/파일 이름에 와일드카드 또는 이 이스케이프 문자가 있는 경우 `^`을 사용하여 이스케이프합니다. |예 |
| 접두사 | S3 개체 키에 대한 접두사입니다. 이 접두사로 시작하는 키를 가진 개체가 선택됩니다. "key" 속성이 지정되어 있지 않은 경우에만 적용됩니다. |예 |
| 버전 | S3 버전 관리를 사용하도록 설정하면 S3 개체의 버전입니다. 지정하지 않으면 최신 버전이 가져옵니다. |예 |
| modifiedDatetimeStart | 파일은 특성: 마지막으로 수정된 특성을 기반으로 필터링합니다. 마지막 수정 시간이 `modifiedDatetimeStart`와 `modifiedDatetimeEnd` 사이의 시간 범위 내에 있으면 파일이 선택됩니다. 시간은 UTC 표준 시간대에 "2018-12-01T05:00:00Z" 형식으로 적용됩니다. <br/><br/> 방대한 양의 파일에서 파일 필터를 수행하려는 경우 이 설정을 사용하도록 설정하면 데이터 이동의 전반적인 성능이 영향을 받을 수 있습니다. <br/><br/> 속성은 NULL일 수 있습니다. 이 경우 파일 특성 필터가 데이터 세트에 적용되지 않습니다.  `modifiedDatetimeStart`에 datetime 값이 있지만 `modifiedDatetimeEnd`가 NULL이면, 마지막으로 수정된 특성이 datetime 값보다 크거나 같은 파일이 선택됩니다.  `modifiedDatetimeEnd`에 datetime 값이 있지만 `modifiedDatetimeStart`가 NULL이면, 마지막으로 수정된 특성이 datetime 값보다 작은 파일이 선택됩니다.| 예 |
| modifiedDatetimeEnd | 파일은 특성: 마지막으로 수정된 특성을 기반으로 필터링합니다. 마지막 수정 시간이 `modifiedDatetimeStart`와 `modifiedDatetimeEnd` 사이의 시간 범위 내에 있으면 파일이 선택됩니다. 시간은 UTC 표준 시간대에 "2018-12-01T05:00:00Z" 형식으로 적용됩니다. <br/><br/> 방대한 양의 파일에서 파일 필터를 수행하려는 경우 이 설정을 사용하도록 설정하면 데이터 이동의 전반적인 성능이 영향을 받을 수 있습니다. <br/><br/> 속성은 NULL일 수 있습니다. 이 경우 파일 특성 필터가 데이터 세트에 적용되지 않습니다.  `modifiedDatetimeStart`에 datetime 값이 있지만 `modifiedDatetimeEnd`가 NULL이면, 마지막으로 수정된 특성이 datetime 값보다 크거나 같은 파일이 선택됩니다.  `modifiedDatetimeEnd`에 datetime 값이 있지만 `modifiedDatetimeStart`가 NULL이면, 마지막으로 수정된 특성이 datetime 값보다 작은 파일이 선택됩니다.| 예 |
| format | 파일 기반 저장소(이진 복사본) 간에 있는 **파일로 파일을 복사하려면** 입력 및 출력 데이터 집합 정의모두에서 형식 섹션을 건너뜁니다.<br/><br/>특정 형식으로 파일을 생성하거나 구문 분석하려는 경우 **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**과 같은 파일 형식 유형이 지원됩니다. format의 **type** 속성을 이 값 중 하나로 설정합니다. 자세한 내용은 [텍스트 형식](supported-file-formats-and-compression-codecs-legacy.md#text-format), [Json 형식](supported-file-formats-and-compression-codecs-legacy.md#json-format), [Avro 형식](supported-file-formats-and-compression-codecs-legacy.md#avro-format), [Orc 형식](supported-file-formats-and-compression-codecs-legacy.md#orc-format) 및 [Parquet 형식](supported-file-formats-and-compression-codecs-legacy.md#parquet-format) 섹션을 참조하세요. |아니요(이진 복사 시나리오에만 해당) |
| 압축 | 데이터에 대한 압축 유형 및 수준을 지정합니다. 자세한 내용은 [지원되는 파일 형식 및 압축 코덱](supported-file-formats-and-compression-codecs-legacy.md#compression-support)을 참조하세요.<br/>지원되는 유형은 **GZip,** **deflate,** **BZip2**및 **ZipDeflate입니다.**<br/>지원되는 수준은 **다음과** 같습니다. **Fastest** |예 |

>[!TIP]
>폴더에서 모든 파일을 복사하려면 버킷으로 **bucketName** 및 폴더 부분으로 **prefix**를 지정합니다.<br>지정된 이름의 단일 파일을 복사하려면 버킷으로 **bucketName** 및 폴더 및 파일 이름 부분으로 **key**를 지정합니다.<br>폴더에서 파일의 하위 집합을 복사하려면 버킷으로 **bucketName** 및 폴더 및 와일드카드 필터 부분으로 **key**를 지정합니다.

**예제: 접두사 사용**

```json
{
    "name": "AmazonS3Dataset",
    "properties": {
        "type": "AmazonS3Object",
        "linkedServiceName": {
            "referenceName": "<Amazon S3 linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "bucketName": "testbucket",
            "prefix": "testFolder/test",
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

**예제: 키 및 버전 사용(선택 사항)**

```json
{
    "name": "AmazonS3Dataset",
    "properties": {
        "type": "AmazonS3",
        "linkedServiceName": {
            "referenceName": "<Amazon S3 linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "bucketName": "testbucket",
            "key": "testFolder/testfile.csv.gz",
            "version": "XXXXXXXXXczm0CJajYkHf0_k6LhBmkcL",
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

| 속성 | Description | 필수 |
|:--- |:--- |:--- |
| type | 복사 작업 원본의 형식 속성을 **FileSystemSource**로 설정해야 합니다. |예 |
| recursive | 하위 폴더에서 또는 지정된 폴더에서만 데이터를 재귀적으로 읽을지 여부를 나타냅니다. recursive가 true로 설정되고 싱크가 파일 기반 저장소인 경우 싱크에서 빈 폴더/하위 폴더가 복사/생성되지 않습니다.<br/>허용된 값은 다음과 같습니다: true(기본값), **false** **true** | 예 |
| maxConcurrentConnections | 동시에 데이터 저장소에 연결할 연결 수입니다. 데이터 저장소에 대한 동시 연결을 제한하려는 경우에만 지정합니다. | 예 |

**예제:**

```json
"activities":[
    {
        "name": "CopyFromAmazonS3",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Amazon S3 input dataset name>",
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
                "type": "FileSystemSource",
                "recursive": true
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="next-steps"></a>다음 단계
Azure Data Factory에서 복사 작업의 원본 및 싱크로 지원되는 데이터 저장소 목록은 [지원되는 데이터 저장소](copy-activity-overview.md#supported-data-stores-and-formats)를 참조하세요.
