---
title: Azure Data Factory ORC 형식
description: 이 항목에서는 Azure Data Factory에서 ORC 형식을 처리 하는 방법을 설명 합니다.
author: linda33wj
manager: shwang
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 09/28/2020
ms.author: jingwang
ms.openlocfilehash: 9e6b8511164cd7e9a855a70d9edba4ce6492c3a3
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91404727"
---
# <a name="orc-format-in-azure-data-factory"></a>Azure Data Factory ORC 형식

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

**ORC 파일을 구문 분석 하거나 데이터를 ORC 형식으로 기록**하려는 경우이 문서를 따릅니다. 

ORC 형식은 다음 커넥터에 대해 지원 됩니다. [Amazon S3](connector-amazon-simple-storage-service.md), [azure Blob](connector-azure-blob-storage.md), [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md), [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md), [Azure File Storage](connector-azure-file-storage.md), [파일 시스템](connector-file-system.md), [FTP](connector-ftp.md), [Google Cloud Storage](connector-google-cloud-storage.md), [HDFS](connector-hdfs.md), [HTTP](connector-http.md), [SFTP](connector-sftp.md).

## <a name="dataset-properties"></a>데이터 세트 속성

데이터 세트 정의에 사용할 수 있는 섹션 및 속성의 전체 목록은 [데이터 세트](concepts-datasets-linked-services.md) 문서를 참조하세요. 이 섹션에서는 ORC 데이터 집합에서 지 원하는 속성의 목록을 제공 합니다.

| 속성         | 설명                                                  | 필수 |
| ---------------- | ------------------------------------------------------------ | -------- |
| type             | 데이터 집합의 type 속성은 **Orc**로 설정 해야 합니다. | 예      |
| 위치         | 파일의 위치 설정입니다. 각 파일 기반 커넥터에는의 고유한 위치 유형 및 지원 되는 속성이 있습니다 `location` . **커넥터 문서-> 데이터 집합 속성 섹션에서 세부 정보를 참조 하세요**. | 예      |
| compressionCodec         | ORC 파일에 쓸 때 사용할 압축 코덱입니다. ORC 파일에서 읽을 때 데이터 팩터리는 파일 메타 데이터를 기반으로 압축 코덱을 자동으로 결정 합니다.<br>지원 되는 형식은 **none**, **zlib**, **snappy** (기본값) 및 **lzo**입니다. 참고 현재 복사 작업은 읽기/쓰기 ORC 파일을 사용할 때 LZO을 지원 하지 않습니다. | 아니요      |

다음은 Azure Blob Storage ORC 데이터 집합의 예입니다.

```json
{
    "name": "OrcDataset",
    "properties": {
        "type": "Orc",
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
            }
        }
    }
}
```

다음 사항에 유의하세요.

* 복합 데이터 형식(구조체, 매핑, 목록, 공용 구조체)은 지원되지 않습니다.
* 열 이름에는 공백이 지원되지 않습니다.

## <a name="copy-activity-properties"></a>복사 작업 속성

작업 정의에 사용할 수 있는 섹션 및 속성의 전체 목록은 [파이프라인](concepts-pipelines-activities.md) 문서를 참조하세요. 이 섹션에서는 ORC 원본 및 싱크에서 지 원하는 속성의 목록을 제공 합니다.

### <a name="orc-as-source"></a>원본으로 ORC

복사 작업 *** \* 원본 \* *** 섹션에서 지원 되는 속성은 다음과 같습니다.

| 속성      | 설명                                                  | 필수 |
| ------------- | ------------------------------------------------------------ | -------- |
| type          | 복사 작업 원본의 type 속성은 **Orcsource**로 설정 해야 합니다. | 예      |
| 나이 설정 | 데이터 저장소에서 데이터를 읽는 방법에 대 한 속성 그룹입니다. 각 파일 기반 커넥터에는의 고유한 지원 읽기 설정이 `storeSettings` 있습니다. **커넥터 문서-> 복사 작업 속성 섹션에서 세부 정보를 참조 하세요**. | 아니요       |

### <a name="orc-as-sink"></a>ORC

복사 작업 *** \* 싱크 \* *** 섹션에서 지원 되는 속성은 다음과 같습니다.

| 속성      | 설명                                                  | 필수 |
| ------------- | ------------------------------------------------------------ | -------- |
| type          | 복사 작업 싱크의 type 속성은 **Orcsink**로 설정 해야 합니다. | 예      |
| formatSettings | 속성 그룹입니다. 아래의 **ORC write settings** 표를 참조 하세요. |    아니요      |
| 나이 설정 | 데이터 저장소에 데이터를 쓰는 방법에 대 한 속성 그룹입니다. 각 파일 기반 커넥터에는의 고유한 지원 쓰기 설정이 `storeSettings` 있습니다. **커넥터 문서-> 복사 작업 속성 섹션에서 세부 정보를 참조 하세요**. | 아니요       |

지원 되는 **ORC 쓰기 설정은** `formatSettings` 다음과 같습니다.

| 속성      | 설명                                                  | 필수                                              |
| ------------- | ------------------------------------------------------------ | ----------------------------------------------------- |
| type          | FormatSettings의 형식은 **Orcwritesettings**로 설정 해야 합니다. | 예                                                   |
| maxRowsPerFile | 폴더에 데이터를 쓸 때 여러 파일에 쓰도록 선택 하 고 파일당 최대 행 수를 지정할 수 있습니다.  | 아니요 |
| fileNamePrefix | `maxRowsPerFile`이 구성 된 경우에 적용 됩니다.<br> 여러 파일에 데이터를 쓸 때 파일 이름 접두사를 지정 합니다 .이 패턴은 `<fileNamePrefix>_00000.<fileExtension>` 입니다. 지정 하지 않으면 파일 이름 접두사가 자동으로 생성 됩니다. 원본이 파일 기반 저장소나 [파티션 옵션 사용 데이터 저장소](copy-activity-performance-features.md)인 경우에는이 속성이 적용 되지 않습니다.  | 아니요 |

## <a name="mapping-data-flow-properties"></a>매핑 데이터 흐름 속성

데이터 흐름 매핑에서는 [Azure Blob Storage](connector-azure-blob-storage.md#mapping-data-flow-properties), [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md#mapping-data-flow-properties)및 [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md#mapping-data-flow-properties)데이터 저장소에서 ORC 형식을 읽고 쓸 수 있습니다.

ORC 데이터 집합을 사용 하거나 [인라인 데이터 집합](data-flow-source.md#inline-datasets)을 사용 하 여 ORC 파일을 가리킬 수 있습니다.

### <a name="source-properties"></a>원본 속성

아래 표에서는 ORC 원본에서 지 원하는 속성을 나열 합니다. 이러한 속성은 **원본 옵션** 탭에서 편집할 수 있습니다.

인라인 데이터 집합을 사용 하는 경우 [데이터 집합 속성](#dataset-properties) 섹션에 설명 된 속성과 동일한 추가 파일 설정이 표시 됩니다.

| Name | 설명 | 필수 | 허용되는 값 | 데이터 흐름 스크립트 속성 |
| ---- | ----------- | -------- | -------------- | ---------------- |
| 형식 | 형식은 이어야 합니다. `orc` | 예 | `orc` | format |
| 와일드 카드 경로 | 와일드 카드 경로와 일치 하는 모든 파일이 처리 됩니다. 데이터 집합에 설정 된 폴더 및 파일 경로를 재정의 합니다. | 아니요 | String[] | wildcardPaths |
| 파티션 루트 경로 | 분할 된 파일 데이터의 경우 분할 된 폴더를 열로 읽기 위해 파티션 루트 경로를 입력할 수 있습니다. | 아니요 | String | 파티션 (partitionRootPath) |
| 파일 목록 | 소스에서 처리할 파일을 나열 하는 텍스트 파일을 가리키고 있는지 여부 | 아니요 | `true` 또는 `false` | fileList |
| 파일 이름을 저장할 열 | 원본 파일 이름 및 경로를 사용 하 여 새 열을 만듭니다. | 아니요 | String | rowUrlColumn |
| 완료 후 | 처리 후 파일을 삭제 하거나 이동 합니다. 컨테이너 루트에서 파일 경로가 시작 됩니다. | 아니요 | 삭제: `true` 또는 `false` <br> 옮기고 `[<from>, <to>]` | purgeFiles <br> moveFiles |
| 마지막으로 수정한 사람 필터링 | 마지막으로 변경 된 시간에 따라 파일을 필터링 하도록 선택 | 아니요 | 타임스탬프 | modifiedAfter <br> modifiedBefore |
| 파일을 찾을 수 없음 | True 이면 파일이 없는 경우 오류가 throw 되지 않습니다. | 아니요 | `true` 또는 `false` | ignoreNoFilesFound |

### <a name="source-example"></a>원본 예제

ORC 원본 구성의 연결 된 데이터 흐름 스크립트는 다음과 같습니다.

```
source(allowSchemaDrift: true,
    validateSchema: false,
    rowUrlColumn: 'fileName',
    format: 'orc') ~> OrcSource
```

### <a name="sink-properties"></a>싱크 속성

아래 표에서는 ORC 싱크에서 지 원하는 속성을 나열 합니다. 이러한 속성은 **설정** 탭에서 편집할 수 있습니다.

인라인 데이터 집합을 사용 하는 경우 [데이터 집합 속성](#dataset-properties) 섹션에 설명 된 속성과 동일한 추가 파일 설정이 표시 됩니다.

| Name | 설명 | 필수 | 허용되는 값 | 데이터 흐름 스크립트 속성 |
| ---- | ----------- | -------- | -------------- | ---------------- |
| 형식 | 형식은 이어야 합니다. `orc` | 예 | `orc` | format |
| 폴더 지우기 | 쓰기 전에 대상 폴더를 지운 경우 | 아니요 | `true` 또는 `false` | truncate |
| 파일 이름 옵션 | 작성 된 데이터의 명명 형식입니다. 기본적으로 파티션 당 한 파일은 형식입니다. `part-#####-tid-<guid>` | 아니요 | 패턴: 문자열 <br> 파티션 당: String [] <br> Column: String의 데이터로 <br> 단일 파일로 출력: `['<fileName>']` | filePattern <br> 파티션 파일 이름 <br> rowUrlColumn <br> 파티션 파일 이름 |

### <a name="sink-example"></a>싱크 예제

ORC sink 구성의 연결 된 데이터 흐름 스크립트는 다음과 같습니다.

```
OrcSource sink(
    format: 'orc',
    filePattern:'output[n].orc',
    truncate: true,
    allowSchemaDrift: true,
    validateSchema: false,
    skipDuplicateMapInputs: true,
    skipDuplicateMapOutputs: true) ~> OrcSink
```

## <a name="using-self-hosted-integration-runtime"></a>자체 호스팅 Integration Runtime 사용

> [!IMPORTANT]
> 온-프레미스 및 클라우드 데이터 저장소 간에 자체 호스팅되는 Integration Runtime의 복사 기능을 위해 ORC 파일을 있는 **그대로**복사 하지 않는 경우 IR 컴퓨터에 **64 비트 JRE 8 (Java Runtime Environment) 또는 openjdk** 및 **Microsoft Visual C++ 2010 재배포 가능 패키지** 를 설치 해야 합니다. 자세한 내용은 다음 단락을 참조 하세요.

ORC 파일 직렬화/deserialization을 사용 하 여 자체 호스팅 IR에서 실행 되는 경우 ADF는 먼저 JRE에 대 한 레지스트리를 확인 하 여 *`(SOFTWARE\JavaSoft\Java Runtime Environment\{Current Version}\JavaHome)`* (찾을 수 없는 경우) OpenJDK의 시스템 변수를 확인 하 여 Java 런타임을 찾습니다 *`JAVA_HOME`* .

- **JRE를 사용 하려면**: 64 비트 IR에 64 비트 JRE가 필요 합니다. [여기](https://go.microsoft.com/fwlink/?LinkId=808605)서 찾을 수 있습니다.
- **OpenJDK를 사용 하려면**IR 버전 3.13부터 지원 됩니다. 다른 모든 필수 OpenJDK 어셈블리와 함께 jvm.dll을 자체 호스팅 IR 머신으로 패키지하고, 이에 따라 JAVA_HOME 시스템 환경 변수를 설정합니다.
- **Visual C++ 2010 재배포 가능 패키지를 설치 하려면**Visual C++ 2010 재배포 가능 패키지가 자체 호스팅 IR 설치와 함께 설치 되지 않습니다. [여기](https://www.microsoft.com/download/details.aspx?id=14632)서 찾을 수 있습니다.

> [!TIP]
> 자체 호스팅 Integration Runtime를 사용 하 여 데이터를 ORC 형식으로 복사 하 고 "java를 호출할 때 오류가 발생 했습니다. **OutOfMemoryError: java 힙 공간**"을 사용 하 `_JAVA_OPTIONS` 는 경우 자체 호스팅 IR을 호스트 하는 컴퓨터의 환경 변수를 추가 하 여 JVM의 최소/최대 힙 크기를 조정 하 여 이러한 복사본을 강화 한 후 파이프라인을 다시 실행할 수 있습니다.

![자체 호스팅 IR에서 JVM 힙 크기 설정](./media/supported-file-formats-and-compression-codecs/set-jvm-heap-size-on-selfhosted-ir.png)

예: 변수 `_JAVA_OPTIONS`를 `-Xms256m -Xmx16g` 값으로 설정합니다. 플래그 `Xms`는 JVM(Java Virtual Machine)의 초기 메모리 할당 풀을 지정하고, `Xmx`는 최대 메모리 할당 풀을 지정합니다. 즉, JVM은 `Xms`의 메모리 양으로 시작하고 최대 `Xmx`의 메모리 양을 사용할 수 있음을 의미합니다. 기본적으로 ADF는 최소 64 m b 및 최대 1G를 사용 합니다.

## <a name="next-steps"></a>다음 단계

- [복사 작업 개요](copy-activity-overview.md)
- [조회 작업](control-flow-lookup-activity.md)
- [GetMetadata 작업](control-flow-get-metadata-activity.md)
