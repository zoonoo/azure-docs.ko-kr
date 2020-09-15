---
title: Azure Data Factory의 XML 형식
description: 이 항목에서는 Azure Data Factory에서 XML 형식을 처리 하는 방법에 대해 설명 합니다.
author: linda33wj
manager: shwang
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 09/15/2020
ms.author: jingwang
ms.openlocfilehash: 12e6ae9dd14ebafb1da6bfbcfef64e2d65e876d8
ms.sourcegitcommit: 03662d76a816e98cfc85462cbe9705f6890ed638
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/15/2020
ms.locfileid: "90531715"
---
# <a name="xml-format-in-azure-data-factory"></a>Azure Data Factory의 XML 형식

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

**XML 파일을 구문 분석**하려는 경우이 문서를 따릅니다. 

XML 형식은 [Amazon S3](connector-amazon-simple-storage-service.md), [azure Blob](connector-azure-blob-storage.md), [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md), [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md), [Azure File Storage](connector-azure-file-storage.md), [파일 시스템](connector-file-system.md), [FTP](connector-ftp.md), [Google Cloud Storage](connector-google-cloud-storage.md), [HDFS](connector-hdfs.md), [HTTP](connector-http.md)및 [SFTP](connector-sftp.md)커넥터에 대해 지원 됩니다. 원본으로 지원 되며 싱크로는 지원 되지 않습니다.

## <a name="dataset-properties"></a>데이터 세트 속성

데이터 세트 정의에 사용할 수 있는 섹션 및 속성의 전체 목록은 [데이터 세트](concepts-datasets-linked-services.md) 문서를 참조하세요. 이 섹션에서는 XML 데이터 집합에서 지 원하는 속성의 목록을 제공 합니다.

| 속성         | 설명                                                  | 필수 |
| ---------------- | ------------------------------------------------------------ | -------- |
| type             | 데이터 집합의 type 속성은 **Xml**로 설정 해야 합니다. | 예      |
| 위치         | 파일의 위치 설정입니다. 각 파일 기반 커넥터에는의 고유한 위치 유형 및 지원 되는 속성이 있습니다 `location` . **커넥터 문서-> 데이터 집합 속성 섹션에서 세부 정보를 참조 하세요**. | 예      |
| encodingName     | 테스트 파일을 읽고 쓰는 데 사용 되는 인코딩 형식입니다. <br>허용 되는 값은 다음과 같습니다. "UTF-8", "UTF-16", "UTF-16 be", "32 UTF-8", "UTF-32BE", "US-ASCII", "UTF-7", "BIG5", "EUC-JP", "EUC-KR", "GB2312", "GB18030", "조합", "SHIFT-JIS", "CP875", "CP866", "IBM00858", "IBM037", "IBM273", "IBM437", "IBM500", "IBM737", "IBM775", "IBM850", "IBM852", "IBM855", "IBM857", "IBM860", "IBM861", "IBM863", "IBM864", "IBM865", "IBM869", "IBM870", "IBM01140", "IBM01141", "IBM01142", "IBM01143", "IBM01144", "IBM01145", "IBM01146", "IBM01147", "IBM01148", "IBM01149", "ISO-2022-JP", "ISO-2022-KR", "ISO-8859-1", "ISO-8859-2", "ISO-8859-3", "ISO-8859-4", "ISO-8859-5", "ISO-8859-6", "ISO-8859-7", "ISO-8859-8", "ISO-8859-9", "ISO-8859-13" , "ISO-8859-15", "WINDOWS-874", "WINDOWS-1250", "WINDOWS-1251", "WINDOWS-1252", "WINDOWS-1253", "WINDOWS-1254", "WINDOWS-1255", "WINDOWS-1256", "WINDOWS-1257", "WINDOWS-1258".| 아니요       |
| nullValue | Null 값의 문자열 표현을 지정 합니다.<br/>기본값은 **빈 문자열**입니다. | 아니요 |
| 압축 | 파일 압축을 구성 하는 속성의 그룹입니다. 작업 실행 중 압축/압축 해제를 수행 하려는 경우이 섹션을 구성 합니다. | 예 |
| type<br>(*아래 `compression` *) | XML 파일을 읽고 쓰는 데 사용 되는 압축 코덱입니다. <br>허용 되는 값은 **bzip2**, **gzip**, **deflate**, **ZipDeflate**, **TarGzip**, **snappy**또는 **lz4**입니다. 기본값은 압축 되지 않습니다.<br>**참고** 현재 복사 작업은 "snappy" & "lz4"을 지원 하지 않으며 매핑 데이터 흐름은 "ZipDeflate"를 지원 하지 않습니다.<br>**참고** 복사 작업을 사용 하 여 **ZipDeflate** / **TarGzip** 파일의 압축을 풀고 파일 기반 싱크 데이터 저장소에 쓸 때 기본적으로 파일은 폴더로 추출 됩니다. 즉 `<path specified in dataset>/<folder named as source compressed file>/` , `preserveZipFileNameAsFolder` / `preserveCompressionFileNameAsFolder` [복사 작업 원본](#xml-as-source) 에서를 사용 하 여 압축 된 파일의 이름을 폴더 구조로 유지할지 여부를 제어 합니다. | 아니요.  |
| 수준<br/>(*아래 `compression` *) | 압축 비율입니다. <br>허용 되는 값은 **최적** 또는 **가장 빠릅니다**.<br>- **가장 빠름:** 압축 작업은 결과 파일이 최적으로 압축 되지 않은 경우에도 최대한 빨리 완료 되어야 합니다.<br>- **최적**: 작업을 완료 하는 데 시간이 더 오래 걸리는 경우에도 압축 작업을 최적으로 압축 해야 합니다. 자세한 내용은 [압축 수준](https://msdn.microsoft.com/library/system.io.compression.compressionlevel.aspx) 항목을 참조하세요. | 아니요       |

다음은 Azure Blob Storage에 대 한 XML 데이터 집합의 예입니다.

```json
{
    "name": "XMLDataset",
    "properties": {
        "type": "Xml",
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

작업 정의에 사용할 수 있는 섹션 및 속성의 전체 목록은 [파이프라인](concepts-pipelines-activities.md) 문서를 참조하세요. 이 섹션에서는 XML 원본에서 지 원하는 속성의 목록을 제공 합니다.

[스키마 매핑에서](copy-activity-schema-and-type-mapping.md)XML 데이터 및 싱크 데이터 저장소/형식을 매핑하는 방법에 대해 알아봅니다. XML 파일을 미리 볼 때 데이터는 JSON 계층 구조와 함께 표시 되며 JSON 경로를 사용 하 여 필드를 가리킵니다.

### <a name="xml-as-source"></a>원본으로 XML

복사 작업 *** \* 원본 \* *** 섹션에서 지원 되는 속성은 다음과 같습니다. [XML 커넥터 동작](#xml-connector-behavior)에서 자세히 알아보세요.

| 속성      | 설명                                                  | 필수 |
| ------------- | ------------------------------------------------------------ | -------- |
| type          | 복사 작업 원본의 type 속성을 **Xmlsource**로 설정 해야 합니다. | 예      |
| formatSettings | 속성 그룹입니다. 아래의 **XML 읽기 설정** 표를 참조 하세요. | 아니요       |
| 나이 설정 | 데이터 저장소에서 데이터를 읽는 방법에 대 한 속성 그룹입니다. 각 파일 기반 커넥터에는의 고유한 지원 읽기 설정이 `storeSettings` 있습니다. **커넥터 문서-> 복사 작업 속성 섹션에서 세부 정보를 참조 하세요**. | 아니요       |

에서 지원 되는 **XML 읽기 설정** `formatSettings` :

| 속성      | 설명                                                  | 필수 |
| ------------- | ------------------------------------------------------------ | -------- |
| type          | FormatSettings의 형식은 **Xmlreadsettings**로 설정 되어야 합니다. | 예      |
| validationMode | XML 스키마의 유효성을 검사할지 여부를 지정 합니다.<br>허용 되는 값은 **none** (기본값, 유효성 검사 안 함), **xsd** (xsd 사용 유효성 검사), **dtd** (dtd를 사용 하 여 유효성 검사)입니다. | 아니요 |
| namespacePrefixes | Xml 파일을 구문 분석할 때 필드의 이름을 결정 하는 데 사용 되는 네임 스페이스 URI-접두사 매핑<br/>XML 파일에 네임 스페이스 및 네임 스페이스가 사용 되는 경우 기본적으로 필드 이름은 XML 문서에 있는 것과 동일 합니다.<br>이 맵에서 네임 스페이스 URI에 대해 정의 된 항목이 있는 경우 필드 이름은 `prefix:fieldName` 입니다. | 아니요 |
| compressionProperties | 지정 된 압축 코덱에 대 한 데이터의 압축을 푸는 방법에 대 한 속성 그룹입니다. | 아니요       |
| preserveZipFileNameAsFolder<br>(*의 `compressionProperties` -> `type` 경우 `ZipDeflateReadSettings` *)  | **ZipDeflate** 압축을 사용 하 여 입력 데이터 집합을 구성할 때 적용 됩니다. 원본 zip 파일 이름을 복사 중에 폴더 구조로 유지할지 여부를 나타냅니다.<br>-True로 설정 하면 **(기본값)** Data Factory 압축을 푼 파일을에 씁니다 `<path specified in dataset>/<folder named as source zip file>/` .<br>- **False**로 설정 하면 압축을 푼 파일을에 직접 Data Factory 씁니다 `<path specified in dataset>` . 경쟁 또는 예기치 않은 동작을 방지 하기 위해 다른 원본 zip 파일에 중복 된 파일 이름이 없는지 확인 합니다.  | 아니요 |
| preserveCompressionFileNameAsFolder<br>(*의 `compressionProperties` -> `type` 경우 `TarGZipReadSettings` *) | **TarGzip** 압축을 사용 하 여 입력 데이터 집합을 구성할 때 적용 됩니다. 원본 압축 파일 이름을 복사 중에 폴더 구조로 유지할지 여부를 나타냅니다.<br>- **True (기본값)** 로 설정 하면 압축 해제 되는 파일을에 Data Factory 씁니다 `<path specified in dataset>/<folder named as source compressed file>/` . <br>- **False**로 설정 하면 압축 해제 한 파일을에 직접 쓸 Data Factory `<path specified in dataset>` . 경쟁 또는 예기치 않은 동작을 방지 하기 위해 다른 원본 파일에 중복 된 파일 이름이 없는지 확인 합니다. | 아니요 |

## <a name="mapping-data-flow-properties"></a>매핑 데이터 흐름 속성

데이터 흐름 매핑에서는 [Azure Blob Storage](connector-azure-blob-storage.md#mapping-data-flow-properties), [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md#mapping-data-flow-properties)및 [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md#mapping-data-flow-properties)데이터 저장소에서 XML 형식을 읽고 쓸 수 있습니다. Xml 데이터 집합을 사용 하거나 [인라인 데이터 집합](data-flow-source.md#inline-datasets)을 사용 하 여 xml 파일을 가리킬 수 있습니다.

### <a name="source-properties"></a>원본 속성

다음 표에서는 XML 원본에서 지 원하는 속성을 나열 합니다. 이러한 속성은 **원본 옵션** 탭에서 편집할 수 있습니다. [XML 커넥터 동작](#xml-connector-behavior)에서 자세히 알아보세요. 인라인 데이터 집합을 사용 하는 경우 [데이터 집합 속성](#dataset-properties) 섹션에 설명 된 속성과 동일한 추가 파일 설정이 표시 됩니다. 

| 이름 | 설명 | 필수 | 허용되는 값 | 데이터 흐름 스크립트 속성 |
| ---- | ----------- | -------- | -------------- | ---------------- |
| 와일드 카드 경로 | 와일드 카드 경로와 일치 하는 모든 파일이 처리 됩니다. 데이터 집합에 설정 된 폴더 및 파일 경로를 재정의 합니다. | 아니요 | String[] | wildcardPaths |
| 파티션 루트 경로 | 분할 된 파일 데이터의 경우 분할 된 폴더를 열로 읽기 위해 파티션 루트 경로를 입력할 수 있습니다. | 예 | String | 파티션 (partitionRootPath) |
| 파일 목록 | 소스에서 처리할 파일을 나열 하는 텍스트 파일을 가리키고 있는지 여부 | 아니요 | `true` 또는 `false` | fileList |
| 파일 이름을 저장할 열 | 원본 파일 이름 및 경로를 사용 하 여 새 열을 만듭니다. | 예 | String | rowUrlColumn |
| 완료 후 | 처리 후 파일을 삭제 하거나 이동 합니다. 컨테이너 루트에서 파일 경로가 시작 됩니다. | 아니요 | 삭제: `true` 또는 `false` <br> 옮기고 `['<from>', '<to>']` | purgeFiles <br>moveFiles |
| 마지막으로 수정한 사람 필터링 | 마지막으로 변경 된 시간에 따라 파일을 필터링 하도록 선택 | 아니요 | 타임스탬프 | modifiedAfter <br>modifiedBefore |
| 유효성 검사 모드 | XML 스키마의 유효성을 검사할지 여부를 지정 합니다. | 아니요 | `None` (기본값, 유효성 검사 없음)<br>`xsd` (XSD를 사용 하 여 유효성 검사)<br>`dtd` DTD를 사용 하 여 유효성을 검사 합니다. | validationMode |
| 네임스페이스 | XML 파일을 구문 분석할 때 네임 스페이스를 사용할지 여부를 지정 합니다. | 아니요 | `true` (기본값) 또는 `false` | 네임스페이스 |
| 네임 스페이스 접두사 쌍 | Xml 파일을 구문 분석할 때 필드의 이름을 결정 하는 데 사용 되는 네임 스페이스 URI-접두사 매핑<br/>XML 파일에 네임 스페이스 및 네임 스페이스가 사용 되는 경우 기본적으로 필드 이름은 XML 문서에 있는 것과 동일 합니다.<br>이 맵에서 네임 스페이스 URI에 대해 정의 된 항목이 있는 경우 필드 이름은 `prefix:fieldName` 입니다. | 아니요 | 패턴이 포함 된 배열`['URI1'->'prefix1','URI2'->'prefix2']` | namespacePrefixes |

### <a name="xml-source-script-example"></a>XML 원본 스크립트 예제

아래 스크립트는 데이터 집합 모드를 사용 하 여 데이터 흐름을 매핑하는 XML 원본 구성의 예입니다.

```
source(allowSchemaDrift: true,
    validateSchema: false,
    validationMode: 'xsd',
    namespaces: true) ~> XMLSource
```

아래 스크립트는 인라인 데이터 집합 모드를 사용 하는 XML 원본 구성의 예입니다.

```
source(allowSchemaDrift: true,
    validateSchema: false,
    format: 'xml',
    fileSystem: 'filesystem',
    folderPath: 'folder',
    validationMode: 'xsd',
    namespaces: true) ~> XMLSource
```

## <a name="xml-connector-behavior"></a>XML 커넥터 동작

XML을 원본으로 사용 하는 경우 다음에 유의 하십시오.

- XML 특성:

    - 요소의 특성은 계층 구조에서 요소의 하위 필드로 구문 분석 됩니다.
    - 특성 필드의 이름은 패턴을 따릅니다 `@attributeName` .

- XML 스키마 유효성 검사:

    - 스키마의 유효성을 검사 하지 않거나 XSD 또는 DTD를 사용 하 여 스키마의 유효성을 검사 하도록 선택할 수 있습니다.
    - XSD 또는 DTD를 사용 하 여 XML 파일의 유효성을 검사 하는 경우에는 XSD/DTD를 상대 경로를 통해 XML 파일 내에서 참조 해야 합니다.

- 네임 스페이스 처리:

    - 데이터 흐름을 사용 하는 경우 네임 스페이스를 비활성화할 수 있습니다 .이 경우 네임 스페이스를 정의 하는 특성이 일반 특성으로 구문 분석 됩니다.
    - 네임 스페이스를 사용 하는 경우 요소와 특성의 이름은 패턴을 따릅니다       `namespaceUri,elementName` `namespaceUri,@attributeName` . 소스에서 각 네임 스페이스 URI에 대 한 네임 스페이스 접두사를 정의할 수 있습니다 .이 경우에는 요소 및 특성의 이름이 패턴을 따릅니다 `definedPrefix:elementName` `definedPrefix:@attributeName` .

- 값 열:

    - XML 요소에 단순 텍스트 값과 특성/자식 요소가 둘 다 있는 경우 단순 텍스트 값은 기본 제공 필드 이름을 포함 하는 "값 열"의 값으로 구문 분석 됩니다 `_value_` . 그리고가 적용 되는 경우에도 요소의 네임 스페이스를 상속 합니다.

## <a name="next-steps"></a>다음 단계

- [복사 작업 개요](copy-activity-overview.md)
- [매핑 데이터 흐름](concepts-data-flow-overview.md)
- [조회 작업](control-flow-lookup-activity.md)
- [GetMetadata 작업](control-flow-get-metadata-activity.md)
