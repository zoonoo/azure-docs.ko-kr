---
title: Azure Data Factory의 Avro 형식
description: 이 항목에서는 Azure Data Factory에서 Avro 형식을 처리 하는 방법에 대해 설명 합니다.
author: linda33wj
manager: shwang
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 09/14/2020
ms.author: jingwang
ms.openlocfilehash: 558a03cee4d3183debac2492d798e40d49d58881
ms.sourcegitcommit: 51df05f27adb8f3ce67ad11d75cb0ee0b016dc5d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/14/2020
ms.locfileid: "90061634"
---
# <a name="avro-format-in-azure-data-factory"></a>Azure Data Factory의 Avro 형식

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

**Avro 파일을 구문 분석 하거나 데이터를 avro 형식으로 기록**하려는 경우이 문서를 따릅니다. 

Avro 형식은 [Amazon S3](connector-amazon-simple-storage-service.md), [azure Blob](connector-azure-blob-storage.md), [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md), [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md), [Azure File Storage](connector-azure-file-storage.md), [파일 시스템](connector-file-system.md), [FTP](connector-ftp.md), [Google Cloud Storage](connector-google-cloud-storage.md), [HDFS](connector-hdfs.md), [HTTP](connector-http.md)및 [SFTP](connector-sftp.md)커넥터에 대해 지원 됩니다.

## <a name="dataset-properties"></a>데이터 세트 속성

데이터 세트 정의에 사용할 수 있는 섹션 및 속성의 전체 목록은 [데이터 세트](concepts-datasets-linked-services.md) 문서를 참조하세요. 이 섹션에서는 Avro 데이터 집합에서 지 원하는 속성의 목록을 제공 합니다.

| 속성         | Description                                                  | 필수 |
| ---------------- | ------------------------------------------------------------ | -------- |
| type             | 데이터 집합의 type 속성은 **Avro**로 설정 되어야 합니다. | 예      |
| 위치         | 파일의 위치 설정입니다. 각 파일 기반 커넥터에는의 고유한 위치 유형 및 지원 되는 속성이 있습니다 `location` . **커넥터 문서-> 데이터 집합 속성 섹션에서 세부 정보를 참조 하세요**. | 예      |
| avroCompressionCodec | Avro 파일에 쓸 때 사용할 압축 코덱입니다. Avro 파일에서 읽을 때 Data Factory는 파일 메타 데이터를 기반으로 압축 코덱을 자동으로 결정 합니다.<br>지원 되는 형식은 "**none**" (기본값), "**deflate**", "**snappy**"입니다. 참고 현재 복사 작업은 Avro 파일을 읽거나 쓸 때 Snappy을 지원 하지 않습니다. | 아니요       |

> [!NOTE]
> Avro 파일에는 열 이름에 공백이 지원 되지 않습니다.

다음은 Azure Blob Storage의 Avro 데이터 집합 예입니다.

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

작업 정의에 사용할 수 있는 섹션 및 속성의 전체 목록은 [파이프라인](concepts-pipelines-activities.md) 문서를 참조하세요. 이 섹션에서는 Avro 원본 및 싱크에서 지 원하는 속성의 목록을 제공 합니다.

### <a name="avro-as-source"></a>Avro를 원본으로

복사 작업 *** \* 원본 \* *** 섹션에서 지원 되는 속성은 다음과 같습니다.

| 속성      | Description                                                  | 필수 |
| ------------- | ------------------------------------------------------------ | -------- |
| type          | 복사 작업 원본의 type 속성은 **Avrosource**로 설정 해야 합니다. | 예      |
| 나이 설정 | 데이터 저장소에서 데이터를 읽는 방법에 대 한 속성 그룹입니다. 각 파일 기반 커넥터에는의 고유한 지원 읽기 설정이 `storeSettings` 있습니다. **커넥터 문서-> 복사 작업 속성 섹션에서 세부 정보를 참조 하세요**. | 아니요       |

### <a name="avro-as-sink"></a>Avro로 Avro

복사 작업 *** \* 싱크 \* *** 섹션에서 지원 되는 속성은 다음과 같습니다.

| 속성      | Description                                                  | 필수 |
| ------------- | ------------------------------------------------------------ | -------- |
| type          | 복사 작업 원본의 type 속성은 **Avrosink**로 설정 해야 합니다. | 예      |
| formatSettings          | 속성 그룹입니다. 아래의 **Avro 쓰기 설정** 표를 참조 하세요.| 아니요      |
| 나이 설정 | 데이터 저장소에 데이터를 쓰는 방법에 대 한 속성 그룹입니다. 각 파일 기반 커넥터에는의 고유한 지원 쓰기 설정이 `storeSettings` 있습니다. **커넥터 문서-> 복사 작업 속성 섹션에서 세부 정보를 참조 하세요**. | 아니요       |

지원 되는 **Avro 쓰기 설정은** `formatSettings` 다음과 같습니다.

| 속성      | Description                                                  | 필수                                              |
| ------------- | ------------------------------------------------------------ | ----------------------------------------------------- |
| type          | FormatSettings의 형식을 **Avrowritesettings**로 설정 해야 합니다. | 예                                                   |
| maxRowsPerFile | 폴더에 데이터를 쓸 때 여러 파일에 쓰도록 선택 하 고 파일당 최대 행 수를 지정할 수 있습니다.  | 아니요 |
| fileNamePrefix | 여러 파일에 데이터를 쓸 때 파일 이름 접두사를 지정 합니다 .이 패턴은 `<fileNamePrefix>_00000.<fileExtension>` 입니다. 지정 하지 않으면 파일 이름 접두사가 자동으로 생성 됩니다. 원본이 파일 기반 저장소나 [파티션 옵션 사용 데이터 저장소](copy-activity-performance-features.md)인 경우에는이 속성이 적용 되지 않습니다.  | 아니요 |

## <a name="mapping-data-flow-properties"></a>매핑 데이터 흐름 속성

데이터 흐름 매핑에서는 [Azure Blob Storage](connector-azure-blob-storage.md#mapping-data-flow-properties), [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md#mapping-data-flow-properties)및 [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md#mapping-data-flow-properties)데이터 저장소에서 avro 형식을 읽고 쓸 수 있습니다.

### <a name="source-properties"></a>원본 속성

다음 표에서는 avro 원본에서 지 원하는 속성을 나열 합니다. 이러한 속성은 **원본 옵션** 탭에서 편집할 수 있습니다.

| 이름 | Description | 필수 | 허용되는 값 | 데이터 흐름 스크립트 속성 |
| ---- | ----------- | -------- | -------------- | ---------------- |
| 와일드 카드 경로 | 와일드 카드 경로와 일치 하는 모든 파일이 처리 됩니다. 데이터 집합에 설정 된 폴더 및 파일 경로를 재정의 합니다. | no | String[] | wildcardPaths |
| 파티션 루트 경로 | 분할 된 파일 데이터의 경우 분할 된 폴더를 열로 읽기 위해 파티션 루트 경로를 입력할 수 있습니다. | no | String | 파티션 (partitionRootPath) |
| 파일 목록 | 소스에서 처리할 파일을 나열 하는 텍스트 파일을 가리키고 있는지 여부 | no | `true` 또는 `false` | fileList |
| 파일 이름을 저장할 열 | 원본 파일 이름 및 경로를 사용 하 여 새 열을 만듭니다. | no | String | rowUrlColumn |
| 완료 후 | 처리 후 파일을 삭제 하거나 이동 합니다. 컨테이너 루트에서 파일 경로가 시작 됩니다. | no | 삭제: `true` 또는 `false` <br> 옮기고 `['<from>', '<to>']` | purgeFiles <br> moveFiles |
| 마지막으로 수정한 사람 필터링 | 마지막으로 변경 된 시간에 따라 파일을 필터링 하도록 선택 | no | 타임스탬프 | modifiedAfter <br> modifiedBefore |

### <a name="sink-properties"></a>싱크 속성

다음 표에서는 avro 싱크에 의해 지원 되는 속성을 나열 합니다. 이러한 속성은 **설정** 탭에서 편집할 수 있습니다.

| 이름 | Description | 필수 | 허용되는 값 | 데이터 흐름 스크립트 속성 |
| ---- | ----------- | -------- | -------------- | ---------------- |
| 폴더 지우기 | 쓰기 전에 대상 폴더를 지운 경우 | no | `true` 또는 `false` | truncate |
| 파일 이름 옵션 | 작성 된 데이터의 명명 형식입니다. 기본적으로 파티션 당 한 파일은 형식입니다. `part-#####-tid-<guid>` | no | 패턴: 문자열 <br> 파티션 당: String [] <br> Column: String의 데이터로 <br> 단일 파일로 출력: `['<fileName>']`  | filePattern <br> 파티션 파일 이름 <br> rowUrlColumn <br> 파티션 파일 이름 |
| 모두 인용 | 모든 값을 따옴표로 묶습니다. | no | `true` 또는 `false` | quoteAll |

## <a name="data-type-support"></a>데이터 형식 지원

### <a name="copy-activity"></a>복사 활동
Avro [복합 데이터 형식은](https://avro.apache.org/docs/current/spec.html#schema_complex) 복사 작업에서 지원 되지 않습니다 (레코드, 열거형, 배열, 맵, 공용 구조체 및 고정).

### <a name="data-flows"></a>데이터 흐름
데이터 흐름에서 Avro 파일을 사용 하는 경우 복잡 한 데이터 형식을 읽고 쓸 수 있지만 먼저 물리적 스키마를 데이터 집합에서 지워야 합니다. 데이터 흐름에서 논리적 프로젝션을 설정 하 고 복잡 한 구조인 열을 파생 시킨 다음 이러한 필드를 Avro 파일에 자동으로 매핑할 수 있습니다.

## <a name="next-steps"></a>다음 단계

- [복사 작업 개요](copy-activity-overview.md)
- [조회 작업](control-flow-lookup-activity.md)
- [GetMetadata 작업](control-flow-get-metadata-activity.md)
