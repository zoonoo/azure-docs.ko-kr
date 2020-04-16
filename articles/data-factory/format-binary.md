---
title: Azure 데이터 팩터리의 이진 형식
description: 이 항목에서는 Azure 데이터 팩터리에서 이진 형식을 처리하는 방법을 설명합니다.
author: linda33wj
manager: shwang
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 11/26/2019
ms.author: jingwang
ms.openlocfilehash: 4560560b3677030a66e277e96eb552d39f5c82c1
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81416334"
---
# <a name="binary-format-in-azure-data-factory"></a>Azure 데이터 팩터리의 이진 형식
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

바이너리 형식은 다음과 같은 커넥터에 대 한 지원 됩니다: [아마존 S3,](connector-amazon-simple-storage-service.md) [Azure Blob,](connector-azure-blob-storage.md) [Azure 데이터 호수 스토리지 Gen1,](connector-azure-data-lake-store.md)Azure 데이터 호수 스토리지 [Gen2,](connector-azure-data-lake-storage.md) [Azure 파일 스토리지,](connector-azure-file-storage.md) [파일 시스템,](connector-file-system.md) [FTP,](connector-ftp.md) [구글 클라우드 스토리지,](connector-google-cloud-storage.md) [HDFS,](connector-hdfs.md) [HTTP,](connector-http.md)및 [SFTP](connector-sftp.md).

[복사 활동,](copy-activity-overview.md) [GetMetadata 활동](control-flow-get-metadata-activity.md)또는 [활동 삭제에서](delete-activity.md)이진 데이터 집합을 사용할 수 있습니다. 이진 데이터 집합을 사용하는 경우 ADF는 파일 콘텐츠를 구문 분석하지 않고 있는 그대로 처리합니다. 

>[!NOTE]
>복사 활동에서 이진 데이터 집합을 사용하는 경우 이진 데이터 집합에서 이진 데이터 집합으로만 복사할 수 있습니다.

## <a name="dataset-properties"></a>데이터 세트 속성

데이터 세트 정의에 사용할 수 있는 섹션 및 속성의 전체 목록은 [데이터 세트](concepts-datasets-linked-services.md) 문서를 참조하세요. 이 섹션에서는 이진 데이터 집합에서 지원하는 속성 목록을 제공합니다.

| 속성         | Description                                                  | 필수 |
| ---------------- | ------------------------------------------------------------ | -------- |
| type             | 데이터 집합의 형식 속성은 **이진으로**설정해야 합니다. | 예      |
| 위치         | 파일의 위치 설정입니다. 각 파일 기반 커넥터에는 고유한 위치 유형과 `location`지원되는 속성이 있습니다. **커넥터 문서 -> 데이터 집합 속성 섹션의 세부 정보를 참조하십시오.** | 예      |
| 압축 | 파일 압축을 구성하는 속성 그룹입니다. 활동 실행 중에 압축/압축 해제를 수행하려는 경우 이 섹션을 구성합니다. | 예 |
| type | 이진 파일을 읽고 쓰는 데 사용되는 압축 코덱입니다. <br>허용된 값은 **bzip2,** **gzip,** **deflate,** **ZipDeflate입니다.** 파일을 저장할 때 사용할 수 있습니다.<br>복사 활동을 사용하여 ZipDeflate 파일의 압축을 풀고 파일 기반 싱크 데이터 저장소에 쓸 때 `<path specified in dataset>/<folder named as source zip file>/`파일이 폴더로 추출됩니다. | 예       |
| level | 압축 비율입니다. 데이터 집합이 활동 복사 싱크에 사용되는 경우에 적용됩니다.<br>허용된 값은 **최적** 또는 **가장 빠릅니다.**<br>- **가장 빠른 속도:** 결과 파일이 최적으로 압축되지 않은 경우에도 압축 작업이 가능한 한 빨리 완료되어야 합니다.<br>- **최적**: 작업을 완료하는 데 시간이 오래 걸리더라도 압축 작업을 최적으로 압축해야 합니다. 자세한 내용은 [압축 수준](https://msdn.microsoft.com/library/system.io.compression.compressionlevel.aspx) 항목을 참조하세요. | 예       |

다음은 Azure Blob 저장소의 이진 데이터 집합의 예입니다.

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

작업 정의에 사용할 수 있는 섹션 및 속성의 전체 목록은 [파이프라인](concepts-pipelines-activities.md) 문서를 참조하세요. 이 섹션에서는 이진 소스 및 싱크에서 지원하는 속성 목록을 제공합니다.

>[!NOTE]
>복사 활동에서 이진 데이터 집합을 사용하는 경우 이진 데이터 집합에서 이진 데이터 집합으로만 복사할 수 있습니다.

### <a name="binary-as-source"></a>소스로 바이너리

다음 속성은 복사 활동 *** \*소스\* *** 섹션에서 지원됩니다.

| 속성      | Description                                                  | 필수 |
| ------------- | ------------------------------------------------------------ | -------- |
| type          | 복사 활동 소스의 형식 속성은 **BinarySource**로 설정해야 합니다. | 예      |
| 매장 설정 | 데이터 저장소에서 데이터를 읽는 방법에 대한 속성 그룹입니다. 각 파일 기반 커넥터에는 에서 `storeSettings`자체 지원되는 읽기 설정이 있습니다. **커넥터 문서 -> 복사 활동 속성 섹션의 세부 정보를 참조하십시오.** | 예       |

### <a name="binary-as-sink"></a>싱크로 바이너리

다음 속성은 복사 활동 *** \*싱크\* *** 섹션에서 지원됩니다.

| 속성      | Description                                                  | 필수 |
| ------------- | ------------------------------------------------------------ | -------- |
| type          | 복사 활동 소스의 형식 속성은 **BinarySink로**설정해야 합니다. | 예      |
| 매장 설정 | 데이터 저장소에 데이터를 작성하는 방법에 대한 속성 그룹입니다. 각 파일 기반 커넥터에는 에서 `storeSettings`자체 지원되는 쓰기 설정이 있습니다. **커넥터 문서 -> 복사 활동 속성 섹션의 세부 정보를 참조하십시오.** | 예       |

## <a name="next-steps"></a>다음 단계

- [활동 개요 복사](copy-activity-overview.md)
- [GetMetadata 작업](control-flow-get-metadata-activity.md)
- [활동 삭제](delete-activity.md)
