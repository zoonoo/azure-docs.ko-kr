---
title: "Azure Data Factory를 사용하여 Amazon 단순 저장소 서비스에서 데이터 복사 | Microsoft Docs"
description: "Azure Data Factory를 사용하여 Amazon S3(단순 저장 서비스)에서 지원되는 싱크 데이터 저장소로 데이터를 복사하는 방법에 대해 알아봅니다."
services: data-factory
author: linda33wj
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.topic: article
ms.date: 09/26/2017
ms.author: jingwang
ms.openlocfilehash: 3c066aab8641377705047f6b0ebd0e18c2afbac8
ms.sourcegitcommit: 38c9176c0c967dd641d3a87d1f9ae53636cf8260
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/06/2017
---
# <a name="copy-data-from-amazon-simple-storage-service-using-azure-data-factory"></a>Azure Data Factory를 사용하여 Amazon 단순 저장소 서비스에서 데이터 복사
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [버전 1 - GA](v1/data-factory-amazon-simple-storage-service-connector.md)
> * [버전 2 - 미리 보기](connector-amazon-simple-storage-service.md)

이 문서에서는 Azure Data Factory의 복사 작업을 사용하여 Azure Blob Storage 간에 데이터를 복사하는 방법을 간략하게 설명합니다. 이 문서는 복사 작업에 대한 일반적인 개요를 제공하는 [복사 작업 개요](copy-activity-overview.md) 문서를 기반으로 합니다.

> [!NOTE]
> 이 문서는 현재 미리 보기 상태인 Data Factory 버전 2에 적용됩니다. GA(일반 공급) 상태인 Data Factory 버전 1 서비스를 사용 중인 경우 [V1의 Amazon S3 커넥터](v1/data-factory-amazon-simple-storage-service-connector.md)를 참조하세요.

## <a name="supported-capabilities"></a>지원되는 기능

모든 지원되는 원본 데이터 저장소에서 Azure Data Lake Store로 데이터를 복사하거나 Azure Data Lake Store에서 모든 지원되는 싱크 데이터 저장소로 데이터를 복사할 수 있습니다. 복사 작업의 원본 또는 싱크로 지원되는 데이터 저장소 목록은 [지원되는 데이터 저장소](copy-activity-overview.md#supported-data-stores-and-formats) 표를 참조하세요.

특히, Amazon S3 커넥터는 이 파일을 있는 그대로 복사 또는 [지원되는 파일 형식 및 압축 코덱](supported-file-formats-and-compression-codecs.md)을 사용하여 파일 붙여넣기를 지원합니다.

## <a name="required-permissions"></a>필요한 사용 권한

Amazon S3에서 데이터를 복사하려면 다음과 같은 권한이 부여되어 있는지 확인합니다.

- Amazon S3 개체 작업에 대한 `s3:GetObject` 및 `s3:GetObjectVersion`.
- Amazon S3 버킷 작업에 대한 `s3:ListBucket`. Data Factory 복사 마법사를 사용하는 경우 `s3:ListAllMyBuckets`도 필요합니다.

Amazon S3 사용 권한의 전체 목록은 [정책에서 사용 권한 지정](http://docs.aws.amazon.com/amazons3/latest/dev/using-with-s3-actions.html)을 참조하세요.

## <a name="getting-started"></a>시작
.NET SDK, Python SDK, Azure PowerShell, REST API 또는 Azure Resource Manager 템플릿을 사용하여 복사 작업으로 파이프라인을 만들 수 있습니다. 복사 작업을 사용하여 파이프라인을 만드는 단계별 지침은 [복사 작업 자습서](quickstart-create-data-factory-dot-net.md)를 참조하세요. 

다음 섹션에서는 Amazon S3에 한정된 Data Factory 엔터티를 정의하는 데 사용되는 속성에 대해 자세히 설명합니다.

## <a name="linked-service-properties"></a>연결된 서비스 속성

Amazon S3 연결된 서비스에 다음 속성이 지원됩니다.

| 속성 | 설명 | 필수 |
|:--- |:--- |:--- |
| type | 형식 속성은 **AmazonS3**으로 설정되어야 합니다. | 예 |
| accessKeyID | 비밀 액세스 키의 ID입니다. |예 |
| secretAccessKey | 비밀 액세스 키 자체입니다. 이 필드를 SecureString으로 표시합니다. |예 |
| connectVia | 데이터 저장소에 연결하는 데 사용할 [Integration Runtime](concepts-integration-runtime.md)입니다. Azure Integration Runtime 또는 자체 호스팅 Integration Runtime을 사용할 수 있습니다(데이터 저장소가 개인 네트워크에 있는 경우). 지정하지 않으면 기본 Azure Integration Runtime을 사용합니다. |아니요 |

>[!NOTE]
>이 커넥터를 사용하려면 Amazon S3에서 데이터를 복사하기 위해 IAM 계정에 대한 액세스 키가 필요합니다. [임시 보안 자격 증명](http://docs.aws.amazon.com/IAM/latest/UserGuide/id_credentials_temp.html)은 지원되지 않습니다.
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

## <a name="dataset-properties"></a>데이터 집합 속성

데이터 집합 정의에 사용할 수 있는 섹션 및 속성의 전체 목록은 데이터 집합 문서를 참조하세요. 이 섹션에서는 Amazon S3 데이터 집합에서 지원하는 속성의 목록을 제공합니다.

Amazon S3에서 데이터를 복사하려면 데이터 집합의 type 속성을 **AmazonS3Object**로 설정합니다. 다음과 같은 속성이 지원됩니다.

| 속성 | 설명 | 필수 |
|:--- |:--- |:--- |
| type | 데이터 집합의 type 속성을 **AmazonS3Object**로 설정해야 합니다. |예 |
| bucketName | S3 버킷 이름입니다. |예 |
| key | S3 개체 키입니다. 접두사가 지정되어 있지 않은 경우에만 적용됩니다. |아니요 |
| 접두사 | S3 개체 키에 대한 접두사입니다. 이 접두사로 시작하는 키를 가진 개체가 선택됩니다. 키가 지정되어 있지 않은 경우에만 적용됩니다. |아니요 |
| 버전 | S3 버전 관리를 사용하도록 설정하면 S3 개체의 버전입니다. |아니요 |
| format | 파일 기반 저장소(이진 복사) 간에 **파일을 있는 그대로 복사**하려는 경우 입력 및 출력 데이터 집합 정의 둘 다에서 형식 섹션을 건너뜁니다.<br/><br/>특정 형식으로 파일을 생성하거나 구문 분석하려는 경우 **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**과 같은 파일 형식 유형이 지원됩니다. 이 값 중 하나로 서식에서 **type** 속성을 설정합니다. 자세한 내용은 [텍스트 형식](supported-file-formats-and-compression-codecs.md#text-format), [Json 형식](supported-file-formats-and-compression-codecs.md#json-format), [Avro 형식](supported-file-formats-and-compression-codecs.md#avro-format), [Orc 형식](supported-file-formats-and-compression-codecs.md#orc-format) 및 [Parquet 형식](supported-file-formats-and-compression-codecs.md#parquet-format) 섹션을 참조하세요. |아니요(이진 복사 시나리오에만 해당) |
| 압축 | 데이터에 대한 압축 유형 및 수준을 지정합니다. 자세한 내용은 [지원되는 파일 형식 및 압축 코덱](supported-file-formats-and-compression-codecs.md#compression-support)을 참조하세요.<br/>지원되는 형식은 **GZip**, **Deflate**, **BZip2** 및 **ZipDeflate**입니다.<br/>지원되는 수준은 **최적** 및 **가장 빠름**입니다. |아니요 |

> [!NOTE]
> **bucketName + 키**는 S3 개체의 위치를 지정합니다. 여기서 버킷은 S3 개체에 대한 루트 컨테이너이며 키는 S3 개체의 전체 경로입니다.

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

## <a name="copy-activity-properties"></a>복사 작업 속성

작업 정의에 사용할 수 있는 섹션 및 속성의 전체 목록은 [파이프라인](concepts-pipelines-activities.md) 문서를 참조하세요. 이 섹션에서는 Azure Data Lake 원본 및 싱크에서 지원하는 속성 목록을 제공합니다.

### <a name="amazon-s3-as-source"></a>원본으로 Amazon S3

Amazon S3에서 데이터를 복사하려면 복사 작업의 원본 형식을 **FileSystemSource**로 설정합니다(Amazon S3 포함). 복사 작업 **source** 섹션에서 다음 속성이 지원됩니다.

| 속성 | 설명 | 필수 |
|:--- |:--- |:--- |
| type | 복사 작업 원본의 형식 속성을 **FileSystemSource**로 설정해야 합니다. |예 |
| recursive | 하위 폴더에서 또는 지정된 폴더에서만 데이터를 재귀적으로 읽을지 여부를 나타냅니다.<br/>허용되는 값은 **true**(기본값), **false**입니다. | 아니요 |

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
Azure Data Factory에서 복사 작업의 원본 및 싱크로 지원되는 데이터 저장소 목록은 [지원되는 데이터 저장소](copy-activity-overview.md##supported-data-stores-and-formats)를 참조하세요.