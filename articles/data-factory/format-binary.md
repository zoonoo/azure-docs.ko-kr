---
title: Azure Data Factory의 이진 형식
description: 이 항목에서는 Azure Data Factory에서 이진 형식을 처리 하는 방법에 대해 설명 합니다.
author: linda33wj
manager: shwang
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 09/15/2020
ms.author: jingwang
ms.openlocfilehash: 297fb51dd1dd8f1dabdcf2fe9e0d2ead5c906c6f
ms.sourcegitcommit: 03662d76a816e98cfc85462cbe9705f6890ed638
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/15/2020
ms.locfileid: "90531834"
---
# <a name="binary-format-in-azure-data-factory"></a>Azure Data Factory의 이진 형식

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

다음 커넥터에 대해 이진 형식이 지원 됩니다. [Amazon S3](connector-amazon-simple-storage-service.md), [azure Blob](connector-azure-blob-storage.md), [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md), [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md), [Azure File Storage](connector-azure-file-storage.md), [파일 시스템](connector-file-system.md), [FTP](connector-ftp.md), [Google Cloud Storage](connector-google-cloud-storage.md), [HDFS](connector-hdfs.md), [HTTP](connector-http.md), [SFTP](connector-sftp.md).

[복사 작업](copy-activity-overview.md), [GetMetadata 작업](control-flow-get-metadata-activity.md)또는 [삭제 작업](delete-activity.md)에서 이진 데이터 집합을 사용할 수 있습니다. 이진 데이터 집합을 사용 하는 경우 ADF는 파일 콘텐츠를 구문 분석 하지 않고 그대로 처리 합니다. 

>[!NOTE]
>복사 작업에서 이진 데이터 집합을 사용 하는 경우 이진 데이터 집합에서 이진 데이터 집합 으로만 복사할 수 있습니다.

## <a name="dataset-properties"></a>데이터 세트 속성

데이터 세트 정의에 사용할 수 있는 섹션 및 속성의 전체 목록은 [데이터 세트](concepts-datasets-linked-services.md) 문서를 참조하세요. 이 섹션에서는 이진 데이터 집합에서 지 원하는 속성의 목록을 제공 합니다.

| 속성         | Description                                                  | 필수 |
| ---------------- | ------------------------------------------------------------ | -------- |
| type             | 데이터 집합의 type 속성은 **Binary**로 설정 되어야 합니다. | Yes      |
| 위치         | 파일의 위치 설정입니다. 각 파일 기반 커넥터에는의 고유한 위치 유형 및 지원 되는 속성이 있습니다 `location` . **커넥터 문서-> 데이터 집합 속성 섹션에서 세부 정보를 참조 하세요**. | Yes      |
| 압축 | 파일 압축을 구성 하는 속성의 그룹입니다. 작업 실행 중 압축/압축 해제를 수행 하려는 경우이 섹션을 구성 합니다. | 예 |
| type | 이진 파일을 읽고 쓰는 데 사용 되는 압축 코덱입니다. <br>허용 되는 값은 **bzip2**, **gzip**, **deflate**, **ZipDeflate**또는 **TarGzip**입니다. <br>**참고** 복사 작업을 사용 하 여 **ZipDeflate** / **TarGzip** 파일의 압축을 풀고 파일 기반 싱크 데이터 저장소에 쓸 때 기본적으로 파일은 폴더로 추출 됩니다. 즉 `<path specified in dataset>/<folder named as source compressed file>/` , `preserveZipFileNameAsFolder` / `preserveCompressionFileNameAsFolder` [복사 작업 원본](#binary-as-source) 에서를 사용 하 여 압축 된 파일의 이름을 폴더 구조로 유지할지 여부를 제어 합니다.| No       |
| 수준 | 압축 비율입니다. 복사 작업 싱크에서 데이터 집합을 사용 하는 경우에 적용 됩니다.<br>허용 되는 값은 **최적** 또는 **가장 빠릅니다**.<br>- **가장 빠름:** 압축 작업은 결과 파일이 최적으로 압축 되지 않은 경우에도 최대한 빨리 완료 되어야 합니다.<br>- **최적**: 작업을 완료 하는 데 시간이 더 오래 걸리는 경우에도 압축 작업을 최적으로 압축 해야 합니다. 자세한 내용은 [압축 수준](https://msdn.microsoft.com/library/system.io.compression.compressionlevel.aspx) 항목을 참조하세요. | No       |

다음은 Azure Blob Storage에서 이진 데이터 집합의 예입니다.

```json
{
    "name": "BinaryDataset",
    "properties": {
        "type": "Binary",
        "linkedServiceName": {
            "referenceName": "<Azure Blob Storage linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "location": {
                "type": "AzureBlobStorageLocation",
                "container": "containername",
                "folderPath": "folder/subfolder",
            },
            "compression": {
                "type": "ZipDeflate"
            }
        }
    }
}
```

## <a name="copy-activity-properties"></a>복사 작업 속성

작업 정의에 사용할 수 있는 섹션 및 속성의 전체 목록은 [파이프라인](concepts-pipelines-activities.md) 문서를 참조하세요. 이 섹션에서는 이진 원본 및 싱크에서 지 원하는 속성의 목록을 제공 합니다.

>[!NOTE]
>복사 작업에서 이진 데이터 집합을 사용 하는 경우 이진 데이터 집합에서 이진 데이터 집합 으로만 복사할 수 있습니다.

### <a name="binary-as-source"></a>소스로 이진

복사 작업 *** \* 원본 \* *** 섹션에서 지원 되는 속성은 다음과 같습니다.

| 속성      | Description                                                  | 필수 |
| ------------- | ------------------------------------------------------------ | -------- |
| type          | 복사 작업 원본의 type 속성을 **Binarysource**로 설정 해야 합니다. | Yes      |
| formatSettings | 속성 그룹입니다. 아래의 **이진 읽기 설정** 표를 참조 하세요. | No       |
| 나이 설정 | 데이터 저장소에서 데이터를 읽는 방법에 대 한 속성 그룹입니다. 각 파일 기반 커넥터에는의 고유한 지원 읽기 설정이 `storeSettings` 있습니다. **커넥터 문서-> 복사 작업 속성 섹션에서 세부 정보를 참조 하세요**. | No       |

다음에서 지원 되는 **이진 읽기 설정** `formatSettings` :

| 속성      | Description                                                  | 필수 |
| ------------- | ------------------------------------------------------------ | -------- |
| type          | FormatSettings의 유형을 **Binaryreadsettings**로 설정 해야 합니다. | Yes      |
| compressionProperties | 지정 된 압축 코덱에 대 한 데이터의 압축을 푸는 방법에 대 한 속성 그룹입니다. | No       |
| preserveZipFileNameAsFolder<br>(*의 `compressionProperties` -> `type` 경우 `ZipDeflateReadSettings` *) | **ZipDeflate** 압축을 사용 하 여 입력 데이터 집합을 구성할 때 적용 됩니다. 원본 zip 파일 이름을 복사 중에 폴더 구조로 유지할지 여부를 나타냅니다.<br>-True로 설정 하면 **(기본값)** Data Factory 압축을 푼 파일을에 씁니다 `<path specified in dataset>/<folder named as source zip file>/` .<br>- **False**로 설정 하면 압축을 푼 파일을에 직접 Data Factory 씁니다 `<path specified in dataset>` . 경쟁 또는 예기치 않은 동작을 방지 하기 위해 다른 원본 zip 파일에 중복 된 파일 이름이 없는지 확인 합니다.  | No |
| preserveCompressionFileNameAsFolder<br>(*의 `compressionProperties` -> `type` 경우 `TarGZipReadSettings` *) | **TarGzip** 압축을 사용 하 여 입력 데이터 집합을 구성할 때 적용 됩니다. 원본 압축 파일 이름을 복사 중에 폴더 구조로 유지할지 여부를 나타냅니다.<br>- **True (기본값)** 로 설정 하면 압축 해제 되는 파일을에 Data Factory 씁니다 `<path specified in dataset>/<folder named as source compressed file>/` . <br>- **False**로 설정 하면 압축 해제 한 파일을에 직접 쓸 Data Factory `<path specified in dataset>` . 경쟁 또는 예기치 않은 동작을 방지 하기 위해 다른 원본 파일에 중복 된 파일 이름이 없는지 확인 합니다. | No |

```json
"activities": [
    {
        "name": "CopyFromBinary",
        "type": "Copy",
        "typeProperties": {
            "source": {
                "type": "BinarySource",
                "storeSettings": {
                    "type": "AzureBlobStorageReadSettings",
                    "recursive": true,
                    "deleteFilesAfterCompletion": true
                },
                "formatSettings": {
                    "type": "BinaryReadSettings",
                    "compressionProperties": {
                        "type": "ZipDeflateReadSettings",
                        "preserveZipFileNameAsFolder": false
                    }
                }
            },
            ...
        }
        ...
    }
]
```

### <a name="binary-as-sink"></a>싱크로 이진 파일

복사 작업 *** \* 싱크 \* *** 섹션에서 지원 되는 속성은 다음과 같습니다.

| 속성      | Description                                                  | 필수 |
| ------------- | ------------------------------------------------------------ | -------- |
| type          | 복사 작업 원본의 type 속성은 **Binarysink**로 설정 되어야 합니다. | Yes      |
| 나이 설정 | 데이터 저장소에 데이터를 쓰는 방법에 대 한 속성 그룹입니다. 각 파일 기반 커넥터에는의 고유한 지원 쓰기 설정이 `storeSettings` 있습니다. **커넥터 문서-> 복사 작업 속성 섹션에서 세부 정보를 참조 하세요**. | 아니요       |

## <a name="next-steps"></a>다음 단계

- [복사 작업 개요](copy-activity-overview.md)
- [GetMetadata 작업](control-flow-get-metadata-activity.md)
- [삭제 작업](delete-activity.md)
