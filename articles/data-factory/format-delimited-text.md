---
title: Azure 데이터 팩터리에서 구분된 텍스트 형식
description: 이 항목에서는 Azure 데이터 팩터리에서 구분된 텍스트 형식을 처리하는 방법에 대해 설명합니다.
author: linda33wj
manager: shwang
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 04/09/2020
ms.author: jingwang
ms.openlocfilehash: 696c175f55ecdb09b1b88f9766c4c678afe2c4ec
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81417098"
---
# <a name="delimited-text-format-in-azure-data-factory"></a>Azure 데이터 팩터리에서 구분된 텍스트 형식
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

**구분된 텍스트 파일을 구문 분석하거나 데이터를 제한된 텍스트 형식으로 쓰려면**이 문서를 따르십시오. 

구분된 텍스트 형식은 [아마존 S3,](connector-amazon-simple-storage-service.md) [Azure Blob,](connector-azure-blob-storage.md) [Azure Data Lake Storage Gen1,](connector-azure-data-lake-store.md)Azure Data Lake Storage [Gen1, Azure Data Lake Storage Gen2,](connector-azure-data-lake-storage.md) [Azure File Storage,](connector-azure-file-storage.md) [파일 시스템,](connector-file-system.md) [FTP,](connector-ftp.md) [Google 클라우드 스토리지,](connector-google-cloud-storage.md) [HDFS,](connector-hdfs.md) [HTTP](connector-http.md)및 [SFTP에](connector-sftp.md)대해 지원됩니다.

## <a name="dataset-properties"></a>데이터 세트 속성

데이터 세트 정의에 사용할 수 있는 섹션 및 속성의 전체 목록은 [데이터 세트](concepts-datasets-linked-services.md) 문서를 참조하세요. 이 섹션에서는 구분된 텍스트 데이터 집합에서 지원하는 속성 목록을 제공합니다.

| 속성         | Description                                                  | 필수 |
| ---------------- | ------------------------------------------------------------ | -------- |
| type             | 데이터 집합의 형식 속성은 **DelimitedText**로 설정해야 합니다. | 예      |
| 위치         | 파일의 위치 설정입니다. 각 파일 기반 커넥터에는 고유한 위치 유형과 `location`지원되는 속성이 있습니다.  | 예      |
| columnDelimiter  | 파일에서 열을 구분하는 데 사용되는 문자입니다. <br>기본값은 ** `,`쉼표입니다. ** 열 구분 기호가 구분 기호가 없는 빈 문자열로 정의되면 전체 줄이 단일 열로 간주됩니다.<br>현재 빈 문자열 또는 다중 문자로 열 구분 기호는 데이터 흐름을 매핑하기 위해지원되지만 복사 활동은 지원되지 않습니다.  | 예       |
| rowDelimiter     | 파일의 행을 구분하는 데 사용되는 단일 문자 또는 "\r\n"입니다. <br>기본값은 읽기시 다음과 같은 값 중 **하나입니다: ["\r\n", "\n", "\n"]** 및 **"\n" 또는 "\n" 또는 "\r\n"** 각각 매핑 데이터 흐름 및 복사 활동을 통해 쓰기에. <br>행 구분 기호가 구분 기호(빈 문자열)로 설정되면 열 구분 기호를 구분 기호(빈 문자열)로 설정해야 하며, 이는 전체 콘텐츠를 단일 값으로 처리하는 것을 의미합니다.<br>현재 빈 문자열로 행 구분 기호는 데이터 흐름을 매핑하는 데만 지원되지만 복사 활동은 지원되지 않습니다. | 예       |
| quoteChar        | 열 구분 기호가 포함된 경우 열 값을 인용하는 단일 문자입니다. <br>기본값은 **큰따옴표입니다.** `"` <br>데이터 흐름을 매핑하는 경우 빈 문자열이 될 `quoteChar` 수 없습니다. <br>Copy 활동의 경우 `quoteChar` 빈 문자열로 정의된 경우 따옴표 char가 없고 열 `escapeChar` 값이 따옴표가 지정되지 않으며 열 구분 기호 및 자체를 이스케이프하는 데 사용됩니다. | 예       |
| escapeChar       | 따옴표값 안에 따옴표를 이스케이프할 단일 문자입니다.<br>기본값은 **백슬래시 `\`입니다. ** <br>데이터 흐름을 매핑하는 경우 빈 문자열이 될 `escapeChar` 수 없습니다. <br/>Copy 활동의 경우 `escapeChar` 빈 문자열로 정의된 `quoteChar` 경우 빈 문자열로 설정해야 하며, 이 경우 모든 열 값에 구분 기호가 포함되지 않은지 확인해야 합니다. | 예       |
| firstRowAsHeader | 첫 번째 행을 열 이름이 있는 헤더 줄로 처리/만들지 여부를 지정합니다.<br>허용된 값은 **true** 및 false(기본값)입니다. **false** | 예       |
| nullValue        | null 값의 문자열 표현을 지정합니다. <br>기본값은 **빈 문자열입니다.** | 예       |
| encodingName     | 테스트 파일을 읽고 쓰는 데 사용되는 인코딩 유형입니다. <br>Allowed values are as follows: "UTF-8", "UTF-16", "UTF-16BE", "UTF-32", "UTF-32BE", "US-ASCII", "UTF-7", "BIG5", "EUC-JP", "EUC-KR", "GB2312", "GB18030", "JOHAB", "SHIFT-JIS", "CP875", "CP866", "IBM00858", "IBM037", "IBM273", "IBM437", "IBM500", "IBM737", "IBM775", "IBM850", "IBM852", "IBM855", "IBM857", "IBM860", "IBM861", "IBM863", "IBM864", "IBM865", "IBM869", "IBM870", "IBM01140", "IBM01141", "IBM01142", "IBM01143", "IBM01144", "IBM01145", "IBM01146", "IBM01147", "IBM01148", "IBM01149", "ISO-2022-JP", "ISO-2022-KR", "ISO-8859-1", "ISO-8859-2", "ISO-8859-3", "ISO-8859-4", "ISO-8859-5", "ISO-8859-6", "ISO-8859-7", "ISO-8859-8", "ISO-8859-9", "ISO-8859-13" , "ISO-8859-15", "윈도우-874", "윈도우-1250", "윈도우-1251", "윈도우-1252", "윈도우-1253", "윈도우-1254", "윈도우-1255", "윈도우-1256", "윈도우-1257", "윈도우-1257", "윈도우-1257", "윈도우-1257", "윈도우-1257", "윈도우-1257", "윈도우-1257", "윈도우-1257", "윈도우-1257", "윈도우-1257", "윈도우-1257", "윈도우-1257", "윈도우-1257", "윈도우-1257", "윈도우-1257", "윈도우-1257", "윈도우-1257", "윈도우-1257", "윈도우-1257", "윈도우-1257", "윈도우-1257", "윈도우-1257", "윈도우-1257", "윈도우-1257", "윈도우-1257", "<br>참고 매핑 데이터 흐름은 UTF-7 인코딩을 지원하지 않습니다. | 예       |
| 압축 코덱 | 텍스트 파일을 읽고 쓰는 데 사용되는 압축 코덱입니다. <br>허용된 값은 **bzip2,** **gzip,** **deflate,** **ZipDeflate,** **snappy,** 또는 **lz4입니다.** 기본값은 압축되지 않습니다. <br>**참고 현재** 복사 활동은 "스냅" & "lz4"를 지원하지 않으며 매핑 데이터 흐름은 "ZipDeflate"를 지원하지 않습니다. <br>**복사** 활동을 사용하여 ZipDeflate 파일의 압축을 풀고 파일 기반 싱크 데이터 저장소에 쓸 때 `<path specified in dataset>/<folder named as source zip file>/`파일이 폴더로 추출됩니다. | 예       |
| 압축 레벨 | 압축 비율입니다. <br>허용된 값은 **최적** 또는 **가장 빠릅니다.**<br>- **가장 빠른 속도:** 결과 파일이 최적으로 압축되지 않은 경우에도 압축 작업이 가능한 한 빨리 완료되어야 합니다.<br>- **최적**: 작업을 완료하는 데 시간이 오래 걸리더라도 압축 작업을 최적으로 압축해야 합니다. 자세한 내용은 [압축 수준](https://msdn.microsoft.com/library/system.io.compression.compressionlevel.aspx) 항목을 참조하세요. | 예       |

다음은 Azure Blob Storage에서 구분된 텍스트 데이터 집합의 예입니다.

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

작업 정의에 사용할 수 있는 섹션 및 속성의 전체 목록은 [파이프라인](concepts-pipelines-activities.md) 문서를 참조하세요. 이 섹션에서는 구분된 텍스트 원본 및 싱크에서 지원하는 속성 목록을 제공합니다.

### <a name="delimited-text-as-source"></a>텍스트가 소스로 구분됩니다. 

다음 속성은 복사 활동 *** \*소스\* *** 섹션에서 지원됩니다.

| 속성       | Description                                                  | 필수 |
| -------------- | ------------------------------------------------------------ | -------- |
| type           | 복사 활동 소스의 형식 속성은 **DelimitedTextSource**로 설정해야 합니다. | 예      |
| 형식설정 | 속성 그룹입니다. 아래의 **구분된 텍스트 읽기 설정** 표를 참조하십시오. | 예       |
| 매장 설정  | 데이터 저장소에서 데이터를 읽는 방법에 대한 속성 그룹입니다. 각 파일 기반 커넥터에는 에서 `storeSettings`자체 지원되는 읽기 설정이 있습니다. | 예       |

지원되는 **구분된** 텍스트 `formatSettings`읽기 설정 아래에서:

| 속성      | Description                                                  | 필수 |
| ------------- | ------------------------------------------------------------ | -------- |
| type          | 형식설정의 형식은 **구분된텍스트읽기설정으로**설정해야 합니다. | 예      |
| skipLineCount | 입력 파일에서 데이터를 읽을 때 건너뛸 **비어 있지 않은** 행의 수를 나타냅니다. <br>skipLineCount와 firstRowAsHeader가 모두 지정되면 먼저 줄을 건너뛴 다음, 입력 파일에서 헤더 정보를 읽습니다. | 예       |

### <a name="delimited-text-as-sink"></a>텍스트가 싱크로 구분

다음 속성은 복사 활동 *** \*싱크\* *** 섹션에서 지원됩니다.

| 속성       | Description                                                  | 필수 |
| -------------- | ------------------------------------------------------------ | -------- |
| type           | 복사 활동 소스의 형식 속성은 **DelimitedTextSink로**설정해야 합니다. | 예      |
| 형식설정 | 속성 그룹입니다. 아래의 **구분된 텍스트 쓰기 설정** 표를 참조하십시오. |          |
| 매장 설정  | 데이터 저장소에 데이터를 작성하는 방법에 대한 속성 그룹입니다. 각 파일 기반 커넥터에는 에서 `storeSettings`자체 지원되는 쓰기 설정이 있습니다.  | 예       |

지원되는 **구분된** 텍스트 `formatSettings`쓰기 설정 아래에서 :

| 속성      | Description                                                  | 필수                                              |
| ------------- | ------------------------------------------------------------ | ----------------------------------------------------- |
| type          | 형식 설정의 형식은 **구분텍스트쓰기 설정으로**설정해야 합니다. | 예                                                   |
| 파일 확장자 | 출력 파일의 이름을 지정하는 데 사용되는 `.csv`파일 `.txt`확장명(예: . . . 출력 DelimitedText `fileName` 데이터 집합에 지정 되지 않은 경우 지정 해야 합니다. 출력 데이터 집합에서 파일 이름이 구성되면 싱크 파일 이름으로 사용되며 파일 확장자 설정은 무시됩니다.  | 예, 출력 데이터 집합에 파일 이름이 지정되지 않은 경우 |

## <a name="mapping-data-flow-properties"></a>데이터 흐름 속성 매핑

매핑 데이터 흐름에서 [소스 변환](data-flow-source.md) 및 [싱크 변환에서](data-flow-sink.md) 자세한 내용을 알아봅니다.

## <a name="next-steps"></a>다음 단계

- [활동 개요 복사](copy-activity-overview.md)
- [데이터 흐름 매핑](concepts-data-flow-overview.md)
- [조회 활동](control-flow-lookup-activity.md)
- [GetMetadata 작업](control-flow-get-metadata-activity.md)
