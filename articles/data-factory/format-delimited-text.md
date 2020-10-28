---
title: Azure Data Factory에서 분리 된 텍스트 형식
description: 이 항목에서는 Azure Data Factory에서 구분 된 텍스트 형식을 처리 하는 방법에 대해 설명 합니다.
author: linda33wj
manager: shwang
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 10/16/2020
ms.author: jingwang
ms.openlocfilehash: 9d4322ff8f9d67ecfd1b55fbee36d5379b987fab
ms.sourcegitcommit: fb3c846de147cc2e3515cd8219d8c84790e3a442
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/27/2020
ms.locfileid: "92636377"
---
# <a name="delimited-text-format-in-azure-data-factory"></a>Azure Data Factory에서 분리 된 텍스트 형식

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

**구분 된 텍스트 파일을 구문 분석 하거나 데이터를 구분 된 텍스트 형식으로 쓰려면** 이 문서를 따르세요. 

구분 기호로 분리 된 텍스트 형식은 [Amazon S3](connector-amazon-simple-storage-service.md), [azure Blob](connector-azure-blob-storage.md), [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md), [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md), [Azure File Storage](connector-azure-file-storage.md), [파일 시스템](connector-file-system.md), [FTP](connector-ftp.md), [Google Cloud Storage](connector-google-cloud-storage.md), [HDFS](connector-hdfs.md), [HTTP](connector-http.md)및 [SFTP](connector-sftp.md)커넥터에 대해 지원 됩니다.

## <a name="dataset-properties"></a>데이터 세트 속성

데이터 세트 정의에 사용할 수 있는 섹션 및 속성의 전체 목록은 [데이터 세트](concepts-datasets-linked-services.md) 문서를 참조하세요. 이 섹션에서는 구분 기호로 분리 된 텍스트 데이터 집합에서 지 원하는 속성의 목록을 제공 합니다.

| 속성         | Description                                                  | 필수 |
| ---------------- | ------------------------------------------------------------ | -------- |
| type             | 데이터 집합의 type 속성은 **DelimitedText** 로 설정 해야 합니다. | 예      |
| 위치         | 파일의 위치 설정입니다. 각 파일 기반 커넥터에는의 고유한 위치 유형 및 지원 되는 속성이 있습니다 `location` .  | 예      |
| columnDelimiter  | 파일에서 열을 구분 하는 데 사용 되는 문자입니다. <br>기본값은 **쉼표 `,`** 입니다. 열 구분 기호가 빈 문자열 (구분 기호가 없음을 의미 함)로 정의 된 경우 전체 줄은 단일 열로 사용 됩니다.<br>현재 빈 문자열 또는 다중 문자로 된 열 구분 기호는 데이터 흐름을 매핑하는 경우에만 지원 되 고 복사 작업은 지원 되지 않습니다.  | 아니요       |
| rowDelimiter     | 파일에서 행을 구분 하는 데 사용 되는 단일 문자 또는 "\r\n"입니다. <br>기본값은 **읽기의 경우 ["\r\n", "\r", "\n"]** , **"\n" 또는 "\r\n"** 의 값 중 하나는 각각 데이터 흐름 및 복사 작업을 매핑하는 것입니다. <br>행 구분 기호를 구분 기호 (빈 문자열)로 설정한 경우에는 열 구분 기호를 구분 기호 (빈 문자열)로 설정 하지 않아야 합니다. 따라서 전체 콘텐츠를 단일 값으로 처리 해야 합니다.<br>현재 행 구분 기호는 데이터 흐름을 매핑하는 경우에만 지원 되 고 복사 작업에는 지원 되지 않습니다. | 아니요       |
| quoteChar        | 열 구분 기호를 포함 하는 경우 열 값을 따옴표로 묶을 단일 문자입니다. <br>기본값은 큰따옴표입니다 **double quotes** `"` . <br>`quoteChar`이 빈 문자열로 정의 되 면 따옴표 문자 및 열 값이 따옴표로 묶여 있지 않으며 `escapeChar` 열 구분 기호를 이스케이프 하는 데 사용 됩니다. | 아니요       |
| escapeChar       | 따옴표 붙은 값 안에서 따옴표를 이스케이프할 단일 문자입니다.<br>기본값은 **백슬래시 `\`** 입니다. <br>`escapeChar`이 빈 문자열로 정의 된 경우는 `quoteChar` 빈 문자열로도 설정 해야 합니다 .이 경우 모든 열 값에 구분 기호가 포함 되지 않아야 합니다. | 아니요       |
| firstRowAsHeader | 첫 번째 행을 열의 이름을 가진 머리글 줄로 처리할지 여부를 지정 합니다.<br>허용 되는 값은 **true** 및 **false** (기본값)입니다.<br>첫 번째 행이 false 인 경우 UI 데이터 미리 보기 및 조회 작업 출력에서 열 이름을 Prop_ {n} (0부터 시작)으로 자동 생성 하 고, 복사 작업에는 원본에서 싱크로의 [명시적 매핑이](copy-activity-schema-and-type-mapping.md#explicit-mapping) 필요 하 고 (1부터 시작) 데이터 흐름 목록을 매핑하고 이름이 인 열을 Column_ {n} (1부터 시작)로 찾습니다.  | 아니요       |
| nullValue        | Null 값의 문자열 표현을 지정 합니다. <br>기본값은 **빈 문자열** 입니다. | 아니요       |
| encodingName     | 테스트 파일을 읽고 쓰는 데 사용 되는 인코딩 형식입니다. <br>허용 되는 값은 다음과 같습니다. "UTF-8", "UTF-16", "UTF-16 be", "32 UTF-8", "UTF-32BE", "US-ASCII", "UTF-7", "BIG5", "EUC-JP", "EUC-KR", "GB2312", "GB18030", "조합", "SHIFT-JIS", "CP875", "CP866", "IBM00858", "IBM037", "IBM273", "IBM437", "IBM500", "IBM737", "IBM775", "IBM850", "IBM852", "IBM855", "IBM857", "IBM860", "IBM861", "IBM863", "IBM864", "IBM865", "IBM869", "IBM870", "IBM01140", "IBM01141", "IBM01142", "IBM01143", "IBM01144", "IBM01145", "IBM01146", "IBM01147", "IBM01148", "IBM01149", "ISO-2022-JP", "ISO-2022-KR", "ISO-8859-1", "ISO-8859-2", "ISO-8859-3", "ISO-8859-4", "ISO-8859-5", "ISO-8859-6", "ISO-8859-7", "ISO-8859-8", "ISO-8859-9", "ISO-8859-13" , "ISO-8859-15", "WINDOWS-874", "WINDOWS-1250", "WINDOWS-1251", "WINDOWS-1252", "WINDOWS-1253", "WINDOWS-1254", "WINDOWS-1255", "WINDOWS-1256", "WINDOWS-1257", "WINDOWS-1258".<br>참고 매핑 데이터 흐름은 UTF-7 인코딩을 지원 하지 않습니다. | 아니요       |
| compressionCodec | 텍스트 파일을 읽고 쓰는 데 사용 되는 압축 코덱입니다. <br>허용 되는 값은 **bzip2** , **gzip** , **deflate** , **ZipDeflate** , **TarGzip** , **snappy** 또는 **lz4** 입니다. 기본값은 압축 되지 않습니다. <br>**참고** 현재 복사 작업은 "snappy" & "lz4"을 지원 하지 않으며 매핑 데이터 흐름은 "ZipDeflate"를 지원 하지 않습니다. <br>**참고** 복사 작업을 사용 하 여 **ZipDeflate** / **TarGzip** 파일의 압축을 풀고 파일 기반 싱크 데이터 저장소에 쓸 때 기본적으로 파일은 폴더로 추출 됩니다. 즉 `<path specified in dataset>/<folder named as source compressed file>/` , `preserveZipFileNameAsFolder` / `preserveCompressionFileNameAsFolder` [복사 작업 원본](#delimited-text-as-source) 에서를 사용 하 여 압축 된 파일의 이름을 폴더 구조로 유지할지 여부를 제어 합니다. | 아니요       |
| compressionLevel | 압축 비율입니다. <br>허용 되는 값은 **최적** 또는 **가장 빠릅니다** .<br>- **가장 빠름:** 압축 작업은 결과 파일이 최적으로 압축 되지 않은 경우에도 최대한 빨리 완료 되어야 합니다.<br>- **최적** : 작업을 완료 하는 데 시간이 더 오래 걸리는 경우에도 압축 작업을 최적으로 압축 해야 합니다. 자세한 내용은 [압축 수준](/dotnet/api/system.io.compression.compressionlevel) 항목을 참조하세요. | 아니요       |

다음은 Azure Blob Storage에서 분리 된 텍스트 데이터 집합의 예입니다.

```json
{
    "name": "DelimitedTextDataset",
    "properties": {
        "type": "DelimitedText",
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
            "columnDelimiter": ",",
            "quoteChar": "\"",
            "escapeChar": "\"",
            "firstRowAsHeader": true,
            "compressionCodec": "gzip"
        }
    }
}
```

## <a name="copy-activity-properties"></a>복사 작업 속성

작업 정의에 사용할 수 있는 섹션 및 속성의 전체 목록은 [파이프라인](concepts-pipelines-activities.md) 문서를 참조하세요. 이 섹션에서는 구분 기호로 분리 된 텍스트 원본 및 싱크에서 지 원하는 속성의 목록을 제공 합니다.

### <a name="delimited-text-as-source"></a>원본으로 구분 된 텍스트 

복사 작업 **_ \_ 원본 \*** * 섹션에서 지원 되는 속성은 다음과 같습니다.

| 속성       | Description                                                  | 필수 |
| -------------- | ------------------------------------------------------------ | -------- |
| type           | 복사 작업 원본의 type 속성은 **DelimitedTextSource** 로 설정 해야 합니다. | 예      |
| formatSettings | 속성 그룹입니다. 아래의 **구분 된 텍스트 읽기 설정** 표를 참조 하세요. |  아니요       |
| 나이 설정  | 데이터 저장소에서 데이터를 읽는 방법에 대 한 속성 그룹입니다. 각 파일 기반 커넥터에는의 고유한 지원 읽기 설정이 `storeSettings` 있습니다. | 아니요       |

지원 되는 **구분 된 텍스트 읽기 설정** `formatSettings` :

| 속성      | Description                                                  | 필수 |
| ------------- | ------------------------------------------------------------ | -------- |
| type          | FormatSettings의 형식은 **DelimitedTextReadSettings** 로 설정 해야 합니다. | 예      |
| skipLineCount | 입력 파일에서 데이터를 읽을 때 건너뛸 **비어 있지 않은** 행의 수를 나타냅니다. <br>skipLineCount와 firstRowAsHeader가 모두 지정되면 먼저 줄을 건너뛴 다음, 입력 파일에서 헤더 정보를 읽습니다. | 아니요       |
| compressionProperties | 지정 된 압축 코덱에 대 한 데이터의 압축을 푸는 방법에 대 한 속성 그룹입니다. | 아니요       |
| preserveZipFileNameAsFolder<br>( *의 `compressionProperties` -> `type` 경우 `ZipDeflateReadSettings`* ) |  **ZipDeflate** 압축을 사용 하 여 입력 데이터 집합을 구성할 때 적용 됩니다. 원본 zip 파일 이름을 복사 중에 폴더 구조로 유지할지 여부를 나타냅니다.<br>-True로 설정 하면 **(기본값)** Data Factory 압축을 푼 파일을에 씁니다 `<path specified in dataset>/<folder named as source zip file>/` .<br>- **False** 로 설정 하면 압축을 푼 파일을에 직접 Data Factory 씁니다 `<path specified in dataset>` . 경쟁 또는 예기치 않은 동작을 방지 하기 위해 다른 원본 zip 파일에 중복 된 파일 이름이 없는지 확인 합니다.  | 아니요 |
| preserveCompressionFileNameAsFolder<br>( *의 `compressionProperties` -> `type` 경우 `TarGZipReadSettings`* )  | **TarGzip** 압축을 사용 하 여 입력 데이터 집합을 구성할 때 적용 됩니다. 원본 압축 파일 이름을 복사 중에 폴더 구조로 유지할지 여부를 나타냅니다.<br>- **True (기본값)** 로 설정 하면 압축 해제 되는 파일을에 Data Factory 씁니다 `<path specified in dataset>/<folder named as source compressed file>/` . <br>- **False** 로 설정 하면 압축 해제 한 파일을에 직접 쓸 Data Factory `<path specified in dataset>` . 경쟁 또는 예기치 않은 동작을 방지 하기 위해 다른 원본 파일에 중복 된 파일 이름이 없는지 확인 합니다. | 아니요 |

```json
"activities": [
    {
        "name": "CopyFromDelimitedText",
        "type": "Copy",
        "typeProperties": {
            "source": {
                "type": "DelimitedTextSource",
                "storeSettings": {
                    "type": "AzureBlobStorageReadSettings",
                    "recursive": true
                },
                "formatSettings": {
                    "type": "DelimitedTextReadSettings",
                    "skipLineCount": 3,
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

### <a name="delimited-text-as-sink"></a>싱크로 분리 된 텍스트

복사 작업 **_ \_ 싱크 \*** * 섹션에서 지원 되는 속성은 다음과 같습니다.

| 속성       | Description                                                  | 필수 |
| -------------- | ------------------------------------------------------------ | -------- |
| type           | 복사 작업 원본의 type 속성은 **DelimitedTextSink** 로 설정 해야 합니다. | 예      |
| formatSettings | 속성 그룹입니다. 아래의 **구분 된 텍스트 쓰기 설정** 표를 참조 하세요. |    아니요      |
| 나이 설정  | 데이터 저장소에 데이터를 쓰는 방법에 대 한 속성 그룹입니다. 각 파일 기반 커넥터에는의 고유한 지원 쓰기 설정이 `storeSettings` 있습니다.  | 아니요       |

지원 되는 **구분 된 텍스트 쓰기 설정** `formatSettings` :

| 속성      | Description                                                  | 필수                                              |
| ------------- | ------------------------------------------------------------ | ----------------------------------------------------- |
| type          | FormatSettings의 형식은 **DelimitedTextWriteSettings** 로 설정 해야 합니다. | 예                                                   |
| fileExtension | 출력 파일의 이름을 사용 하는 데 사용 되는 파일 확장명 (예:) `.csv` `.txt` 입니다. `fileName`출력 DelimitedText 데이터 집합에서가 지정 되지 않은 경우 지정 해야 합니다. 출력 데이터 집합에서 파일 이름이 구성 되 면 싱크 파일 이름으로 사용 되 고 파일 확장명 설정이 무시 됩니다.  | 출력 데이터 집합에 파일 이름이 지정 되지 않은 경우 예 |
| maxRowsPerFile | 폴더에 데이터를 쓸 때 여러 파일에 쓰도록 선택 하 고 파일당 최대 행 수를 지정할 수 있습니다.  | 아니요 |
| fileNamePrefix | `maxRowsPerFile`이 구성 된 경우에 적용 됩니다.<br> 여러 파일에 데이터를 쓸 때 파일 이름 접두사를 지정 합니다 .이 패턴은 `<fileNamePrefix>_00000.<fileExtension>` 입니다. 지정 하지 않으면 파일 이름 접두사가 자동으로 생성 됩니다. 원본이 파일 기반 저장소나 [파티션 옵션 사용 데이터 저장소](copy-activity-performance-features.md)인 경우에는이 속성이 적용 되지 않습니다.  | 아니요 |

## <a name="mapping-data-flow-properties"></a>매핑 데이터 흐름 속성

데이터 흐름 매핑에서는 [Azure Blob Storage](connector-azure-blob-storage.md#mapping-data-flow-properties), [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md#mapping-data-flow-properties)및 [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md#mapping-data-flow-properties)데이터 저장소에서 구분 된 텍스트 형식을 읽고 쓸 수 있습니다.

### <a name="source-properties"></a>원본 속성

다음 표에서는 구분 된 텍스트 원본에서 지 원하는 속성을 나열 합니다. 이러한 속성은 **원본 옵션** 탭에서 편집할 수 있습니다.

| Name | Description | 필수 | 허용되는 값 | 데이터 흐름 스크립트 속성 |
| ---- | ----------- | -------- | -------------- | ---------------- |
| 와일드 카드 경로 | 와일드 카드 경로와 일치 하는 모든 파일이 처리 됩니다. 데이터 집합에 설정 된 폴더 및 파일 경로를 재정의 합니다. | 아니요 | String[] | wildcardPaths |
| 파티션 루트 경로 | 분할 된 파일 데이터의 경우 분할 된 폴더를 열로 읽기 위해 파티션 루트 경로를 입력할 수 있습니다. | 아니요 | String | 파티션 (partitionRootPath) |
| 파일 목록 | 소스에서 처리할 파일을 나열 하는 텍스트 파일을 가리키고 있는지 여부 | 아니요 | `true` 또는 `false` | fileList |
| 여러 줄 행 | 원본 파일에 여러 줄에 걸쳐 있는 행이 포함 되어 있는지를 나타내는 경우 여러 줄 값은 따옴표로 묶어야 합니다. | 아니요 `true` 또는 `false` | multiLineRow |
| 파일 이름을 저장할 열 | 원본 파일 이름 및 경로를 사용 하 여 새 열을 만듭니다. | 아니요 | String | rowUrlColumn |
| 완료 후 | 처리 후 파일을 삭제 하거나 이동 합니다. 컨테이너 루트에서 파일 경로가 시작 됩니다. | 아니요 | 삭제: `true` 또는 `false` <br> 옮기고 `['<from>', '<to>']` | purgeFiles <br> moveFiles |
| 마지막으로 수정한 사람 필터링 | 마지막으로 변경 된 시간에 따라 파일을 필터링 하도록 선택 | 아니요 | 타임스탬프 | modifiedAfter <br> modifiedBefore |
| 파일을 찾을 수 없음 | True 이면 파일이 없는 경우 오류가 throw 되지 않습니다. | 아니요 | `true` 또는 `false` | ignoreNoFilesFound |

### <a name="source-example"></a>원본 예제

아래 그림은 데이터 흐름 매핑에서 분리 된 텍스트 원본 구성의 예입니다.

![DelimitedText 원본](media/data-flow/delimited-text-source.png)

연결 된 데이터 흐름 스크립트는 다음과 같습니다.

```
source(
    allowSchemaDrift: true,
    validateSchema: false,
    multiLineRow: true,
    wildcardPaths:['*.csv']) ~> CSVSource
```

### <a name="sink-properties"></a>싱크 속성

아래 표에는 구분 된 텍스트 싱크에 의해 지원 되는 속성이 나와 있습니다. 이러한 속성은 **설정** 탭에서 편집할 수 있습니다.

| Name | Description | 필수 | 허용되는 값 | 데이터 흐름 스크립트 속성 |
| ---- | ----------- | -------- | -------------- | ---------------- |
| 폴더 지우기 | 쓰기 전에 대상 폴더를 지운 경우 | 아니요 | `true` 또는 `false` | truncate |
| 파일 이름 옵션 | 작성 된 데이터의 명명 형식입니다. 기본적으로 파티션 당 한 파일은 형식입니다. `part-#####-tid-<guid>` | 아니요 | 패턴: 문자열 <br> 파티션 당: String [] <br> Column: String의 데이터로 <br> 단일 파일로 출력: `['<fileName>']`  | filePattern <br> 파티션 파일 이름 <br> rowUrlColumn <br> 파티션 파일 이름 |
| 모두 인용 | 모든 값을 따옴표로 묶습니다. | 아니요 | `true` 또는 `false` | quoteAll |

### <a name="sink-example"></a>싱크 예제

아래 그림은 데이터 흐름 매핑에서 분리 된 텍스트 싱크 구성의 예입니다.

![DelimitedText 싱크](media/data-flow/delimited-text-sink.png)

연결 된 데이터 흐름 스크립트는 다음과 같습니다.

```
CSVSource sink(allowSchemaDrift: true,
    validateSchema: false,
    truncate: true,
    skipDuplicateMapInputs: true,
    skipDuplicateMapOutputs: true) ~> CSVSink
```

## <a name="next-steps"></a>다음 단계

- [복사 작업 개요](copy-activity-overview.md)
- [매핑 데이터 흐름](concepts-data-flow-overview.md)
- [조회 작업](control-flow-lookup-activity.md)
- [GetMetadata 작업](control-flow-get-metadata-activity.md)