---
title: Azure 데이터 팩터리의 아브로 형식
description: 이 항목에서는 Azure 데이터 팩터리에서 Avro 형식을 처리하는 방법에 대해 설명합니다.
author: linda33wj
manager: shwang
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: jingwang
ms.openlocfilehash: 931287fa2a4104069b101236bec9f76bb7193e8d
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81416344"
---
# <a name="avro-format-in-azure-data-factory"></a>Azure 데이터 팩터리의 아브로 형식
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

**Avro 파일을 구문 분석하거나 데이터를 Avro 형식으로 작성하려는**경우이 문서를 따르십시오. 

Avro 형식은 다음과 같은 커넥터에 대 한 지원 됩니다: [아마존 S3,](connector-amazon-simple-storage-service.md) [Azure Blob,](connector-azure-blob-storage.md) [Azure 데이터 호수 스토리지 Gen1,](connector-azure-data-lake-store.md)Azure 데이터 호수 스토리지 [Gen2,](connector-azure-data-lake-storage.md) [Azure 파일 스토리지,](connector-azure-file-storage.md) [파일 시스템,](connector-file-system.md) [FTP,](connector-ftp.md) [구글 클라우드 스토리지,](connector-google-cloud-storage.md) [HDFS,](connector-hdfs.md) [HTTP,](connector-http.md)및 [SFTP](connector-sftp.md).

## <a name="dataset-properties"></a>데이터 세트 속성

데이터 세트 정의에 사용할 수 있는 섹션 및 속성의 전체 목록은 [데이터 세트](concepts-datasets-linked-services.md) 문서를 참조하세요. 이 섹션에서는 Avro 데이터 집합에서 지원하는 속성 목록을 제공합니다.

| 속성         | Description                                                  | 필수 |
| ---------------- | ------------------------------------------------------------ | -------- |
| type             | 데이터 집합의 형식 속성은 **Avro로**설정해야 합니다. | 예      |
| 위치         | 파일의 위치 설정입니다. 각 파일 기반 커넥터에는 고유한 위치 유형과 `location`지원되는 속성이 있습니다. **커넥터 문서 -> 데이터 집합 속성 섹션의 세부 정보를 참조하십시오.** | 예      |
| 아브로압축코덱 | 아브로 파일에 쓸 때 사용하는 압축 코덱. Avro 파일에서 읽을 때 데이터 팩터리는 파일 메타데이터에 따라 압축 코덱을 자동으로 결정합니다.<br>지원되는 형식은 "**없음"**(기본값),**"수축**",**"스냅"입니다.** 참고 현재 복사 활동은 Avro 파일을 읽음/쓸 때 Snappy를 지원하지 않습니다. | 예       |

> [!NOTE]
> 열 이름의 공백은 Avro 파일에 대해 지원되지 않습니다.

다음은 Azure Blob 저장소의 Avro 데이터 집합의 예입니다.

```json
{
    "name": "AvroDataset",
    "properties": {
        "type": "Avro",
        "linkedServiceName": {
            "referenceName": "<Azure Blob Storage linked service name>",
            "type": "LinkedServiceReference"
        },
        "schema": [ < physical schema, optional, retrievable during authoring > ],
        "typeProperties": {
            "location": {
                "type": "AzureBlobStorageLocation",
                "container": "containername",
                "folderPath": "folder/subfolder",
            },
            "avroCompressionCodec": "snappy"
        }
    }
}
```

## <a name="copy-activity-properties"></a>복사 작업 속성

작업 정의에 사용할 수 있는 섹션 및 속성의 전체 목록은 [파이프라인](concepts-pipelines-activities.md) 문서를 참조하세요. 이 섹션에서는 Avro 소스 및 싱크에서 지원하는 속성 목록을 제공합니다.

### <a name="avro-as-source"></a>아브로 소스

다음 속성은 복사 활동 *** \*소스\* *** 섹션에서 지원됩니다.

| 속성      | Description                                                  | 필수 |
| ------------- | ------------------------------------------------------------ | -------- |
| type          | 복사 활동 소스의 형식 속성은 **AvroSource**로 설정해야 합니다. | 예      |
| 매장 설정 | 데이터 저장소에서 데이터를 읽는 방법에 대한 속성 그룹입니다. 각 파일 기반 커넥터에는 에서 `storeSettings`자체 지원되는 읽기 설정이 있습니다. **커넥터 문서 -> 복사 활동 속성 섹션의 세부 정보를 참조하십시오.** | 예       |

### <a name="avro-as-sink"></a>싱크대로 아브로

다음 속성은 복사 활동 *** \*싱크\* *** 섹션에서 지원됩니다.

| 속성      | Description                                                  | 필수 |
| ------------- | ------------------------------------------------------------ | -------- |
| type          | 복사 활동 소스의 형식 속성은 **AvroSink**로 설정해야 합니다. | 예      |
| 매장 설정 | 데이터 저장소에 데이터를 작성하는 방법에 대한 속성 그룹입니다. 각 파일 기반 커넥터에는 에서 `storeSettings`자체 지원되는 쓰기 설정이 있습니다. **커넥터 문서 -> 복사 활동 속성 섹션의 세부 정보를 참조하십시오.** | 예       |

## <a name="data-type-support"></a>데이터 형식 지원

### <a name="copy-activity"></a>복사 활동
Avro [복잡한 데이터 형식은](https://avro.apache.org/docs/current/spec.html#schema_complex) 복사 활동에서 지원되지 않습니다(레코드, 열거형, 배열, 맵, 공용 구조체 및 고정).

### <a name="data-flows"></a>데이터 흐름
데이터 흐름에서 Avro 파일로 작업할 때 복잡한 데이터 형식을 읽고 쓸 수 있지만 먼저 데이터 집합에서 실제 스키마를 지워야 합니다. 데이터 흐름에서 논리 투영을 설정하고 복잡한 구조인 열을 파생시킨 다음 해당 필드를 Avro 파일에 자동으로 매핑할 수 있습니다.

## <a name="next-steps"></a>다음 단계

- [활동 개요 복사](copy-activity-overview.md)
- [조회 활동](control-flow-lookup-activity.md)
- [GetMetadata 작업](control-flow-get-metadata-activity.md)
