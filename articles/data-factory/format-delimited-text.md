---
title: Azure Data Factory에서 구분 기호로 분리 된 텍스트 형식 | Microsoft Docs
description: 이 항목에서는 Azure Data Factory에 구분 기호로 분리 된 텍스트 형식으로 처리 하는 방법을 설명 합니다.
author: linda33wj
manager: craigg
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 04/29/2019
ms.author: jingwang
ms.openlocfilehash: 407b8ba2fda35d3acbf1b425bb15fe20778613d7
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/13/2019
ms.locfileid: "65146012"
---
# <a name="delimited-text-format-in-azure-data-factory"></a>Azure Data Factory에서 구분 기호로 분리 된 텍스트 형식

하려는 경우이 문서를 따릅니다 **구분 기호로 분리 된 텍스트 파일 구문 분석 하거나 구분 기호로 분리 된 텍스트 형식으로 데이터를 쓰려면**합니다. 

다음 커넥터에 대 한 구분 기호로 분리 된 텍스트 형식으로 사용할 수 있습니다. [Amazon S3](connector-amazon-simple-storage-service.md), [Azure Blob](connector-azure-blob-storage.md)를 [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md)를 [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md)를 [Azure File Storage](connector-azure-file-storage.md), [파일 시스템](connector-file-system.md)를 [FTP](connector-ftp.md)를 [Google 클라우드 Storage](connector-google-cloud-storage.md)를 [HDFS](connector-hdfs.md)를 [HTTP](connector-http.md), 및 [ SFTP](connector-sftp.md)합니다.

## <a name="dataset-properties"></a>데이터 세트 속성

데이터 세트 정의에 사용할 수 있는 섹션 및 속성의 전체 목록은 [데이터 세트](concepts-datasets-linked-services.md) 문서를 참조하세요. 이 섹션에는 구분 기호로 분리 된 텍스트 데이터 집합에서 지 원하는 속성의 목록을 제공 합니다.

| 자산         | 설명                                                  | 필수 |
| ---------------- | ------------------------------------------------------------ | -------- |
| 형식             | 데이터 집합의 type 속성 설정 해야 합니다 **DelimitedText**합니다. | 예      |
| location         | 파일의 위치 설정입니다. 각 파일 기반 커넥터에는 고유한 위치 형식이 및 지원 되는 속성에서 `location`합니다. **자세한 내용은 커넥터 문서에서-> 데이터 집합 속성 섹션**합니다. | 예      |
| columnDelimiter  | 파일의 열을 구분 하는 데 사용 하는 문자입니다. 현재 데이터 흐름 매핑 하지만 하지 복사 작업에 대 한 다중 문자 구분 기호 에서만 지원 됩니다. <br>기본값은 **쉼표 `,`** 경우 열 구분 기호 즉, 전체 줄 구분 기호는 단일 열으로 만들어진 빈 문자열로 정의 됩니다. | 아닙니다.       |
| rowDelimiter     | 단일 문자 또는 "\r\n" 파일의 행을 구분 하는 데 사용 합니다.<br>기본값은 다음 값 중 하나 **읽기: ["\r\n", "\r", "\n"]** , 및 **"\n" 또는 "\r\n" 쓰기 시** 매핑 데이터 흐름에서 각각의 복사 작업입니다. <br>때 `rowDelimiter` 구분 기호 (빈 문자열)으로 설정 됩니다는 `columnDelimiter` 으로 단일 값으로 전체 콘텐츠를 처리 하려면 즉, 구분 기호 (빈 문자열)으로 설정 되어야 합니다. | 아닙니다.       |
| quoteChar        | 열 구분 기호를 포함 하는 경우 열 값을 인용 하는 단일 문자입니다. <br>기본값은 **큰따옴표** `"`합니다. <br>매핑 데이터 흐름에 대 한 `quoteChar` 빈 문자열일 수 없습니다. <br>복사 작업의 경우 `quoteChar` 정의 빈 문자열로 의미 하지 인용 문자는 열 값에 따옴표가 사용 되지 및 `escapeChar` 자체 및 열 구분 기호를 이스케이프 하는 데 사용 됩니다. | 아닙니다.       |
| escapeChar       | 따옴표로 묶인된 값 내의 따옴표를 이스케이프 하는 단일 문자입니다.<br>기본값은 **백슬래시 `\`** 합니다. <br>매핑 데이터 흐름에 대 한 `escapeChar` 빈 문자열일 수 없습니다. <br/>복사 작업의 경우 `escapeChar` 빈 문자열로 정의 됩니다는 `quoteChar` 빈 문자열도로, 경우 모든 열 값 구분 기호를 포함 하지 않는지를 확인 해야 합니다. | 아닙니다.       |
| firstRowAsHeader | Treat/확인 첫 번째 행 머리글 줄으로 열의 이름 사용 하 여 지정 합니다.<br>허용 되는 값은 **true** 하 고 **false** (기본값). | 아닙니다.       |
| nullValue        | Null 값의 문자열 표현을 지정합니다. <br>기본값은 **빈 문자열**합니다. | 아닙니다.       |
| encodingName     | 읽기/쓰기 테스트 파일을 사용 하는 인코딩 형식입니다. <br>허용 된 값은 다음과 같습니다. "UTF-8", "U T F-16", "UTF-16BE", "UTF-32", "UTF-32BE" "US ASCII", "U T F-7", "BIG5", "EUC JP", "EUC KR", "GB2312", "GB18030", "조합", "SHIFT JIS", "CP875", "CP866", "IBM00858", "IBM037", "IBM273", "IBM437", "IBM500", "IBM737", "IBM775", "IBM850", " IBM852 ","IBM855","IBM857","IBM860","IBM861","IBM863","IBM864","IBM865","IBM869","IBM870","IBM01140","IBM01141","IBM01142","IBM01143","IBM01144","IBM01145","IBM01146","IBM01147","IBM01148","IBM01149"," ISO-2022-JP "," ISO-2022-한국 "," ISO-8859-1 "," ISO-8859-2 "," ISO-8859-3 "," ISO-8859-4 "," ISO-8859-5 "," ISO-8859-6 "," ISO-8859-7 "," ISO-8859-8 "," ISO-8859-9 "," ISO-8859-13 "," ISO-8859-15 "," WINDOWS-874 "," WINDOWS-1250 "," WINDOWS-1251 "," WINDOWS-1252 "," WINDOWS-1253 "," WINDOWS-1254 "," WINDOWS-1255 "," WINDOWS-1256 "," WINDOWS-1257 "," WINDOWS-1258 "입니다.<br>데이터 흐름 매핑 참고 utf-7 인코딩을 지원 하지 않습니다. | 아닙니다.       |
| compressionCodec | 텍스트 파일 읽기/쓰기에 사용 되는 압축 코덱. <br>허용 되는 값은 **bzip2**를 **gzip**를 **deflate**를 **ZipDeflate**를 **snappy**, 또는 **lz4**합니다. 에 파일을 저장할 때 사용 합니다. <br>데이터 흐름 매핑 수 없습니다. "ZipDeflate" 및 "snappy" 및 "lz4" 복사 작업 지원 하지 않습니다 현재 note 합니다. | 아닙니다.       |
| compressionLevel | 압축 비율입니다. <br>허용 되는 값은 **최적** 하거나 **Fastest**합니다.<br>- **가장 빠른:** 결과 파일이 최적으로 압축되지 않은 경우에도 압축 작업을 최대한 빨리 완료해야 합니다.<br>- **최적의**: 작업이 완료되는데 시간이 오래 걸리더라도 압축 작업이 최적으로 압축되어야 합니다. 자세한 내용은 [압축 수준](https://msdn.microsoft.com/library/system.io.compression.compressionlevel.aspx) 항목을 참조하세요. | 아닙니다.       |

다음은 Azure Blob Storage에 구분 기호로 분리 된 텍스트 데이터 집합의 예:

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
            "firstRowAsHeader": true,
            "compressionCodec": "gzip"
        }
    }
}
```

## <a name="copy-activity-properties"></a>복사 작업 속성

작업 정의에 사용할 수 있는 섹션 및 속성의 전체 목록은 [파이프라인](concepts-pipelines-activities.md) 문서를 참조하세요. 이 섹션에는 구분 기호로 분리 된 텍스트 원본 및 싱크에서 지 원하는 속성의 목록을 제공 합니다.

### <a name="delimited-text-as-source"></a>원본으로 구분 된 텍스트 

복사 작업에서 다음 속성이 지원 됩니다 ***\*소스\**** 섹션입니다.

| 자산       | 설명                                                  | 필수 |
| -------------- | ------------------------------------------------------------ | -------- |
| 형식           | 복사 활동 source의 type 속성 설정 해야 합니다 **DelimitedTextSource**합니다. | 예      |
| formatSettings | 그룹 속성입니다. 가리킵니다 **구분 기호로 분리 됨 텍스트 설정을 읽을** 표에서 합니다. | 아닙니다.       |
| storeSettings  | 그룹 데이터 저장소에서 데이터를 읽는 방법에 대 한 속성입니다. 각 파일 기반 커넥터 설정이 고유한 지원 되는 읽기에서 `storeSettings`합니다. **자세한 내용은 커넥터 문서에서 복사 활동 속성 섹션->** 합니다. | 아닙니다.       |

지원 **구분 기호로 분리 된 텍스트 설정을 읽을** 아래에서 `formatSettings`:

| 자산      | 설명                                                  | 필수 |
| ------------- | ------------------------------------------------------------ | -------- |
| 형식          | FormatSettings 유형의 변경 해야 **DelimitedTextReadSetting**합니다. | 예      |
| skipLineCount | 입력 파일에서 데이터를 읽을 때 건너뛸 **비어 있지 않은** 행의 수를 나타냅니다. <br>SkipLineCount와 firstRowAsHeader를 둘 다 지정하면 해당하는 줄을 먼저 건너뛴 다음 입력 파일에서 머리글 정보를 읽습니다. | 아닙니다.       |

### <a name="delimited-text-as-sink"></a>싱크로 구분 기호로 분리 된 텍스트

복사 작업에서 다음 속성이 지원 됩니다 ***\*싱크\**** 섹션입니다.

| 자산       | 설명                                                  | 필수 |
| -------------- | ------------------------------------------------------------ | -------- |
| 형식           | 복사 활동 source의 type 속성 설정 해야 합니다 **DelimitedTextSink**합니다. | 예      |
| formatSettings | 그룹 속성입니다. 가리킵니다 **구분 기호로 분리 된 텍스트에 설정 쓰기** 표에서 합니다. |          |
| storeSettings  | 그룹 데이터 저장소로 데이터를 작성 하는 방법에 대 한 속성입니다. 각 파일 기반 커넥터에서 자체 지원 되는 쓰기 설정이 `storeSettings`합니다. **자세한 내용은 커넥터 문서에서 복사 활동 속성 섹션->** 합니다. | 아닙니다.       |

지원 **구분 기호로 분리 된 텍스트에 설정을 씁니다** 아래에서 `formatSettings`:

| 자산      | 설명                                                  | 필수                                              |
| ------------- | ------------------------------------------------------------ | ----------------------------------------------------- |
| 형식          | FormatSettings 유형의 변경 해야 **DelimitedTextWriteSetting**합니다. | 예                                                   |
| fileExtension | 예: 출력 파일 이름을 지정 하는 데 사용 하는 파일 확장명 `.csv`, `.txt`합니다. 여야 합니다 될 때 지정 되는 `fileName` 출력에 지정 되지 않은 DelimitedText 데이터 집합입니다. | 출력 데이터 집합의 파일 이름이 지정 되지 않은 경우 예 |

## <a name="mapping-data-flow-properties"></a>데이터 흐름 속성 매핑

자세한 내용은 [소스 변환](data-flow-source.md) 하 고 [변환 싱크](data-flow-sink.md) 매핑 데이터 흐름에서.

## <a name="next-steps"></a>다음 단계

- [복사 작업 개요](copy-activity-overview.md)
- [데이터 흐름 매핑](concepts-data-flow-overview.md)
- [조회 작업](control-flow-lookup-activity.md)
- [GetMetadata 작업](control-flow-get-metadata-activity.md)