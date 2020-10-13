---
title: Azure Data Factory JSON 형식
description: 이 항목에서는 Azure Data Factory에서 JSON 형식을 처리 하는 방법에 대해 설명 합니다.
author: linda33wj
manager: shwang
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 09/15/2020
ms.author: jingwang
ms.openlocfilehash: 0b80c2f125e7eddaa427b75e3e0fe2aff6c679be
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91334330"
---
# <a name="json-format-in-azure-data-factory"></a>Azure Data Factory JSON 형식

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Json **파일을 구문 분석 하거나 json 형식으로 데이터를 쓰려면**이 문서를 따르세요. 

다음 커넥터에 대해 JSON 형식이 지원 됩니다. [Amazon S3](connector-amazon-simple-storage-service.md), [azure Blob](connector-azure-blob-storage.md), [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md), [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md), [Azure File Storage](connector-azure-file-storage.md), [파일 시스템](connector-file-system.md), [FTP](connector-ftp.md), [Google Cloud Storage](connector-google-cloud-storage.md), [HDFS](connector-hdfs.md), [HTTP](connector-http.md), [SFTP](connector-sftp.md).

## <a name="dataset-properties"></a>데이터 세트 속성

데이터 세트 정의에 사용할 수 있는 섹션 및 속성의 전체 목록은 [데이터 세트](concepts-datasets-linked-services.md) 문서를 참조하세요. 이 섹션에서는 JSON 데이터 집합에서 지 원하는 속성의 목록을 제공 합니다.

| 속성         | 설명                                                  | 필수 |
| ---------------- | ------------------------------------------------------------ | -------- |
| type             | 데이터 집합의 type 속성은 **Json**으로 설정 해야 합니다. | 예      |
| 위치         | 파일의 위치 설정입니다. 각 파일 기반 커넥터에는의 고유한 위치 유형 및 지원 되는 속성이 있습니다 `location` . **커넥터 문서-> 데이터 집합 속성 섹션에서 세부 정보를 참조 하세요**. | 예      |
| encodingName     | 테스트 파일을 읽고 쓰는 데 사용 되는 인코딩 형식입니다. <br>허용 되는 값은 다음과 같습니다. "UTF-8", "UTF-16", "UTF-16 be", "32 UTF-8", "UTF-32BE", "US-ASCII", "UTF-7", "BIG5", "EUC-JP", "EUC-KR", "GB2312", "GB18030", "조합", "SHIFT-JIS", "CP875", "CP866", "IBM00858", "IBM037", "IBM273", "IBM437", "IBM500", "IBM737", "IBM775", "IBM850", "IBM852", "IBM855", "IBM857", "IBM860", "IBM861", "IBM863", "IBM864", "IBM865", "IBM869", "IBM870", "IBM01140", "IBM01141", "IBM01142", "IBM01143", "IBM01144", "IBM01145", "IBM01146", "IBM01147", "IBM01148", "IBM01149", "ISO-2022-JP", "ISO-2022-KR", "ISO-8859-1", "ISO-8859-2", "ISO-8859-3", "ISO-8859-4", "ISO-8859-5", "ISO-8859-6", "ISO-8859-7", "ISO-8859-8", "ISO-8859-9", "ISO-8859-13" , "ISO-8859-15", "WINDOWS-874", "WINDOWS-1250", "WINDOWS-1251", "WINDOWS-1252", "WINDOWS-1253", "WINDOWS-1254", "WINDOWS-1255", "WINDOWS-1256", "WINDOWS-1257", "WINDOWS-1258".| 아니요       |
| 압축 | 파일 압축을 구성 하는 속성의 그룹입니다. 작업 실행 중 압축/압축 해제를 수행 하려는 경우이 섹션을 구성 합니다. | 예 |
| type<br/>(*아래 `compression` *) | JSON 파일을 읽고 쓰는 데 사용 되는 압축 코덱입니다. <br>허용 되는 값은 **bzip2**, **gzip**, **deflate**, **ZipDeflate**, **TarGzip**, **snappy**또는 **lz4**입니다. 기본값은 압축 되지 않습니다.<br>**참고** 현재 복사 작업은 "snappy" & "lz4"을 지원 하지 않으며 매핑 데이터 흐름은 "ZipDeflate"를 지원 하지 않습니다.<br>**참고** 복사 작업을 사용 하 여 **ZipDeflate** / **TarGzip** 파일의 압축을 풀고 파일 기반 싱크 데이터 저장소에 쓸 때 기본적으로 파일은 폴더로 추출 됩니다. 즉 `<path specified in dataset>/<folder named as source compressed file>/` , `preserveZipFileNameAsFolder` / `preserveCompressionFileNameAsFolder` [복사 작업 원본](#json-as-source) 에서를 사용 하 여 압축 된 파일의 이름을 폴더 구조로 유지할지 여부를 제어 합니다.| 아니요.  |
| 수준<br/>(*아래 `compression` *) | 압축 비율입니다. <br>허용 되는 값은 **최적** 또는 **가장 빠릅니다**.<br>- **가장 빠름:** 압축 작업은 결과 파일이 최적으로 압축 되지 않은 경우에도 최대한 빨리 완료 되어야 합니다.<br>- **최적**: 작업을 완료 하는 데 시간이 더 오래 걸리는 경우에도 압축 작업을 최적으로 압축 해야 합니다. 자세한 내용은 [압축 수준](https://msdn.microsoft.com/library/system.io.compression.compressionlevel.aspx) 항목을 참조하세요. | 아니요       |

다음은 Azure Blob Storage에서 JSON 데이터 집합의 예입니다.

```json
{
    "name": "JSONDataset",
    "properties": {
        "type": "Json",
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
            "compression": {
                "type": "gzip"
            }
        }
    }
}
```

## <a name="copy-activity-properties"></a>복사 작업 속성

작업 정의에 사용할 수 있는 섹션 및 속성의 전체 목록은 [파이프라인](concepts-pipelines-activities.md) 문서를 참조하세요. 이 섹션에서는 JSON 원본 및 싱크에서 지 원하는 속성의 목록을 제공 합니다.

JSON 파일에서 데이터를 추출 하 고, 데이터를 싱크 데이터 저장소/형식 또는 [스키마 매핑에서](copy-activity-schema-and-type-mapping.md)그 반대로 매핑하는 방법에 대해 알아봅니다.

### <a name="json-as-source"></a>JSON을 소스로

복사 작업 *** \* 원본 \* *** 섹션에서 지원 되는 속성은 다음과 같습니다.

| 속성      | 설명                                                  | 필수 |
| ------------- | ------------------------------------------------------------ | -------- |
| type          | 복사 작업 원본의 type 속성은 **JSONSource**로 설정 해야 합니다. | 예      |
| formatSettings | 속성 그룹입니다. 아래의 **JSON 읽기 설정** 표를 참조 하세요. | 아니요       |
| 나이 설정 | 데이터 저장소에서 데이터를 읽는 방법에 대 한 속성 그룹입니다. 각 파일 기반 커넥터에는의 고유한 지원 읽기 설정이 `storeSettings` 있습니다. **커넥터 문서-> 복사 작업 속성 섹션에서 세부 정보를 참조 하세요**. | 아니요       |

다음에서 지원 되는 **JSON 읽기 설정** `formatSettings` :

| 속성      | 설명                                                  | 필수 |
| ------------- | ------------------------------------------------------------ | -------- |
| type          | FormatSettings의 형식은 **JsonReadSettings**로 설정 해야 합니다. | 예      |
| compressionProperties | 지정 된 압축 코덱에 대 한 데이터의 압축을 푸는 방법에 대 한 속성 그룹입니다. | 아니요       |
| preserveZipFileNameAsFolder<br>(*의 `compressionProperties` -> `type` 경우 `ZipDeflateReadSettings` *)  | **ZipDeflate** 압축을 사용 하 여 입력 데이터 집합을 구성할 때 적용 됩니다. 원본 zip 파일 이름을 복사 중에 폴더 구조로 유지할지 여부를 나타냅니다.<br>-True로 설정 하면 **(기본값)** Data Factory 압축을 푼 파일을에 씁니다 `<path specified in dataset>/<folder named as source zip file>/` .<br>- **False**로 설정 하면 압축을 푼 파일을에 직접 Data Factory 씁니다 `<path specified in dataset>` . 경쟁 또는 예기치 않은 동작을 방지 하기 위해 다른 원본 zip 파일에 중복 된 파일 이름이 없는지 확인 합니다.  | 아니요 |
| preserveCompressionFileNameAsFolder<br>(*의 `compressionProperties` -> `type` 경우 `TarGZipReadSettings` *) | **TarGzip** 압축을 사용 하 여 입력 데이터 집합을 구성할 때 적용 됩니다. 원본 압축 파일 이름을 복사 중에 폴더 구조로 유지할지 여부를 나타냅니다.<br>- **True (기본값)** 로 설정 하면 압축 해제 되는 파일을에 Data Factory 씁니다 `<path specified in dataset>/<folder named as source compressed file>/` . <br>- **False**로 설정 하면 압축 해제 한 파일을에 직접 쓸 Data Factory `<path specified in dataset>` . 경쟁 또는 예기치 않은 동작을 방지 하기 위해 다른 원본 파일에 중복 된 파일 이름이 없는지 확인 합니다. | 아니요 |

### <a name="json-as-sink"></a>JSON을 싱크로

복사 작업 *** \* 싱크 \* *** 섹션에서 지원 되는 속성은 다음과 같습니다.

| 속성      | 설명                                                  | 필수 |
| ------------- | ------------------------------------------------------------ | -------- |
| type          | 복사 작업 원본의 type 속성은 **JSONSink**로 설정 해야 합니다. | 예      |
| formatSettings | 속성 그룹입니다. 아래의 **JSON 쓰기 설정** 표를 참조 하세요. | 아니요       |
| 나이 설정 | 데이터 저장소에 데이터를 쓰는 방법에 대 한 속성 그룹입니다. 각 파일 기반 커넥터에는의 고유한 지원 쓰기 설정이 `storeSettings` 있습니다. **커넥터 문서-> 복사 작업 속성 섹션에서 세부 정보를 참조 하세요**. | 아니요       |

다음에서 지원 되는 **JSON 쓰기 설정** `formatSettings` :

| 속성      | 설명                                                  | 필수                                              |
| ------------- | ------------------------------------------------------------ | ----------------------------------------------------- |
| type          | FormatSettings의 형식은 **JsonWriteSettings**로 설정 해야 합니다. | 예                                                   |
| filePattern |각 JSON 파일에 저장된 데이터의 패턴을 나타냅니다. 허용 되는 값은 **Setofobjects** (JSON 줄) 및 **arrayofobjects**입니다. **기본값** 은 **Setofobjects**입니다. 이러한 패턴에 대한 자세한 내용은 [JSON 파일 패턴](#json-file-patterns) 섹션을 참조하세요. |아니요 |

### <a name="json-file-patterns"></a>JSON 파일 패턴

JSON 파일에서 데이터를 복사 하는 경우 복사 작업은 다음과 같은 JSON 파일 패턴을 자동으로 검색 하 고 구문 분석할 수 있습니다. JSON 파일에 데이터를 쓸 때 복사 작업 싱크에서 파일 패턴을 구성할 수 있습니다.

- **유형 I: setOfObjects**

    각 파일에는 단일 개체, JSON 줄 또는 연결 된 개체가 포함 됩니다.

    * **단일 개체 JSON 예제**

        ```json
        {
            "time": "2015-04-29T07:12:20.9100000Z",
            "callingimsi": "466920403025604",
            "callingnum1": "678948008",
            "callingnum2": "567834760",
            "switch1": "China",
            "switch2": "Germany"
        }
        ```

    * **JSON 줄 (싱크에 대 한 기본값)**

        ```json
        {"time":"2015-04-29T07:12:20.9100000Z","callingimsi":"466920403025604","callingnum1":"678948008","callingnum2":"567834760","switch1":"China","switch2":"Germany"}
        {"time":"2015-04-29T07:13:21.0220000Z","callingimsi":"466922202613463","callingnum1":"123436380","callingnum2":"789037573","switch1":"US","switch2":"UK"}
        {"time":"2015-04-29T07:13:21.4370000Z","callingimsi":"466923101048691","callingnum1":"678901578","callingnum2":"345626404","switch1":"Germany","switch2":"UK"}
        ```

    * **연결된 JSON 예제**

        ```json
        {
            "time": "2015-04-29T07:12:20.9100000Z",
            "callingimsi": "466920403025604",
            "callingnum1": "678948008",
            "callingnum2": "567834760",
            "switch1": "China",
            "switch2": "Germany"
        }
        {
            "time": "2015-04-29T07:13:21.0220000Z",
            "callingimsi": "466922202613463",
            "callingnum1": "123436380",
            "callingnum2": "789037573",
            "switch1": "US",
            "switch2": "UK"
        }
        {
            "time": "2015-04-29T07:13:21.4370000Z",
            "callingimsi": "466923101048691",
            "callingnum1": "678901578",
            "callingnum2": "345626404",
            "switch1": "Germany",
            "switch2": "UK"
        }
        ```

- **유형 II: arrayOfObjects**

    각 파일에 개체 배열이 포함됩니다.

    ```json
    [
        {
            "time": "2015-04-29T07:12:20.9100000Z",
            "callingimsi": "466920403025604",
            "callingnum1": "678948008",
            "callingnum2": "567834760",
            "switch1": "China",
            "switch2": "Germany"
        },
        {
            "time": "2015-04-29T07:13:21.0220000Z",
            "callingimsi": "466922202613463",
            "callingnum1": "123436380",
            "callingnum2": "789037573",
            "switch1": "US",
            "switch2": "UK"
        },
        {
            "time": "2015-04-29T07:13:21.4370000Z",
            "callingimsi": "466923101048691",
            "callingnum1": "678901578",
            "callingnum2": "345626404",
            "switch1": "Germany",
            "switch2": "UK"
        }
    ]
    ```

## <a name="mapping-data-flow-properties"></a>매핑 데이터 흐름 속성

데이터 흐름 매핑에서는 [Azure Blob Storage](connector-azure-blob-storage.md#mapping-data-flow-properties), [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md#mapping-data-flow-properties)및 [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md#mapping-data-flow-properties)데이터 저장소에서 JSON 형식을 읽고 쓸 수 있습니다.

### <a name="source-properties"></a>원본 속성

다음 표에서는 json 원본에서 지 원하는 속성을 나열 합니다. 이러한 속성은 **원본 옵션** 탭에서 편집할 수 있습니다.

| Name | 설명 | 필수 | 허용되는 값 | 데이터 흐름 스크립트 속성 |
| ---- | ----------- | -------- | -------------- | ---------------- |
| 와일드 카드 경로 | 와일드 카드 경로와 일치 하는 모든 파일이 처리 됩니다. 데이터 집합에 설정 된 폴더 및 파일 경로를 재정의 합니다. | 아니요 | String[] | wildcardPaths |
| 파티션 루트 경로 | 분할 된 파일 데이터의 경우 분할 된 폴더를 열로 읽기 위해 파티션 루트 경로를 입력할 수 있습니다. | 아니요 | String | 파티션 (partitionRootPath) |
| 파일 목록 | 소스에서 처리할 파일을 나열 하는 텍스트 파일을 가리키고 있는지 여부 | 아니요 | `true` 또는 `false` | fileList |
| 파일 이름을 저장할 열 | 원본 파일 이름 및 경로를 사용 하 여 새 열을 만듭니다. | 아니요 | String | rowUrlColumn |
| 완료 후 | 처리 후 파일을 삭제 하거나 이동 합니다. 컨테이너 루트에서 파일 경로가 시작 됩니다. | 아니요 | 삭제: `true` 또는 `false` <br> 옮기고 `['<from>', '<to>']` | purgeFiles <br> moveFiles |
| 마지막으로 수정한 사람 필터링 | 마지막으로 변경 된 시간에 따라 파일을 필터링 하도록 선택 | 아니요 | 타임스탬프 | modifiedAfter <br> modifiedBefore |
| 단일 문서 | 데이터 흐름 매핑 각 파일에서 하나의 JSON 문서를 읽습니다. | 아니요 | `true` 또는 `false` | singleDocument |
| 따옴표 붙지 않은 열 이름 | 따옴표 **붙지 않은 열 이름을** 선택 하는 경우 데이터 흐름 매핑이 따옴표로 묶여 있지 않은 JSON 열을 읽습니다. | 아니요 | `true` 또는 `false` |  unquotedColumnNames |
| 설명 있음 | JSON 데이터에 C 또는 c + + 스타일 주석이 있으면 **주석 포함** 을 선택 합니다. | 아니요 | `true` 또는 `false` | asComments |
| 작은따옴표 | 따옴표로 묶여 있지 않은 JSON 열을 읽습니다. | 아니요 | `true` 또는 `false` | singleQuoted |
| 백슬래시가 이스케이프 되었습니다. | 백슬래시를 사용 하 여 JSON 데이터의 문자를 이스케이프 하는 경우 **백슬래시 이스케이프** 를 선택 합니다. | 아니요 | `true` 또는 `false` | backslashEscape |
| 파일을 찾을 수 없음 | True 이면 파일이 없는 경우 오류가 throw 되지 않습니다. | 아니요 | `true` 또는 `false` | ignoreNoFilesFound |

### <a name="source-format-options"></a>원본 형식 옵션

데이터 흐름에서 JSON 데이터 집합을 원본으로 사용 하면 5 개의 추가 설정을 지정할 수 있습니다. 이러한 설정은 **원본 옵션** 탭의 **JSON 설정** 아코디언 아래에서 찾을 수 있습니다.  

![JSON 설정](media/data-flow/json-settings.png "JSON 설정")

#### <a name="default"></a>기본값

기본적으로 JSON 데이터는 다음 형식으로 읽습니다.

```
{ "json": "record 1" }
{ "json": "record 2" }
{ "json": "record 3" }
```

#### <a name="single-document"></a>단일 문서

**단일 문서** 를 선택 하는 경우 데이터 흐름 매핑은 각 파일에서 하나의 JSON 문서를 읽습니다. 

``` json
File1.json
{
    "json": "record 1"
}
File2.json
{
    "json": "record 2"
}
File3.json
{
    "json": "record 3"
}
```
> [!NOTE]
> JSON 데이터를 미리 볼 때 데이터 흐름에서 "corrupt_record" 라는 오류를 throw 하는 경우 데이터에 JSON 파일의 단일 문서가 포함 될 수 있습니다. "단일 문서"를 설정 하면 해당 오류를 지워야 합니다.

#### <a name="unquoted-column-names"></a>따옴표 붙지 않은 열 이름

따옴표 **붙지 않은 열 이름을** 선택 하는 경우 데이터 흐름 매핑이 따옴표로 묶여 있지 않은 JSON 열을 읽습니다. 

```
{ json: "record 1" }
{ json: "record 2" }
{ json: "record 3" }
```

#### <a name="has-comments"></a>설명 있음

JSON 데이터에 C 또는 c + + 스타일 주석이 있으면 **주석 포함** 을 선택 합니다.

``` json
{ "json": /** comment **/ "record 1" }
{ "json": "record 2" }
{ /** comment **/ "json": "record 3" }
```

#### <a name="single-quoted"></a>작은따옴표

JSON 필드와 값에 큰따옴표 대신 작은따옴표를 사용 하는 경우 **작은따옴표** 를 선택 합니다.

```
{ 'json': 'record 1' }
{ 'json': 'record 2' }
{ 'json': 'record 3' }
```

#### <a name="backslash-escaped"></a>백슬래시가 이스케이프 되었습니다.

백슬래시를 사용 하 여 JSON 데이터의 문자를 이스케이프 하면 **백슬래시 이스케이프** 를 선택 합니다.

```
{ "json": "record 1" }
{ "json": "\} \" \' \\ \n \\n record 2" }
{ "json": "record 3" }
```

### <a name="sink-properties"></a>싱크 속성

아래 표에는 json 싱크에 의해 지원 되는 속성이 나와 있습니다. 이러한 속성은 **설정** 탭에서 편집할 수 있습니다.

| Name | 설명 | 필수 | 허용되는 값 | 데이터 흐름 스크립트 속성 |
| ---- | ----------- | -------- | -------------- | ---------------- |
| 폴더 지우기 | 쓰기 전에 대상 폴더를 지운 경우 | 아니요 | `true` 또는 `false` | truncate |
| 파일 이름 옵션 | 작성 된 데이터의 명명 형식입니다. 기본적으로 파티션 당 한 파일은 형식입니다. `part-#####-tid-<guid>` | 아니요 | 패턴: 문자열 <br> 파티션 당: String [] <br> Column: String의 데이터로 <br> 단일 파일로 출력: `['<fileName>']`  | filePattern <br> 파티션 파일 이름 <br> rowUrlColumn <br> 파티션 파일 이름 |

### <a name="creating-json-structures-in-a-derived-column"></a>파생 열에 JSON 구조 만들기

파생 열 식 작성기를 통해 데이터 흐름에 복잡 한 열을 추가할 수 있습니다. 파생 열 변환에서 새 열을 추가 하 고 파란색 상자를 클릭 하 여 식 작성기를 엽니다. 열을 복잡 하 게 만들려면 JSON 구조를 수동으로 입력 하거나 UX를 사용 하 여 하위 열을 대화형으로 추가할 수 있습니다.

#### <a name="using-the-expression-builder-ux"></a>식 작성기 UX 사용

출력 스키마 쪽 창에서 열 위로 마우스를 이동 하 고 더하기 아이콘을 클릭 합니다. 열을 복합 형식으로 만들려면 하위 **열 추가** 를 선택 합니다.

![하위 열 추가](media/data-flow/derive-add-subcolumn.png "하위 열 추가")

동일한 방식으로 열 및 하위 열을 더 추가할 수 있습니다. 복합 필드가 아닌 각 필드에 대해 식 편집기의 오른쪽에 식을 추가할 수 있습니다.

![복합 열 추가](media/data-flow/derive-complex-column.png "열 추가")

#### <a name="entering-the-json-structure-manually"></a>수동으로 JSON 구조 입력

JSON 구조를 수동으로 추가 하려면 새 열을 추가 하 고 편집기에 식을 입력 합니다. 식의 일반적인 형식은 다음과 같습니다.

```
@(
    field1=0,
    field2=@(
        field1=0
    )
)
```

"ComplexColumn" 이라는 열에 대해이 식을 입력 한 경우 다음 JSON으로 싱크에 기록 됩니다.

```
{
    "complexColumn": {
        "field1": 0,
        "field2": {
            "field1": 0
        }
    }
}
```

#### <a name="sample-manual-script-for-complete-hierarchical-definition"></a>전체 계층 정의에 대 한 예제 수동 스크립트
```
@(
    title=Title,
    firstName=FirstName,
    middleName=MiddleName,
    lastName=LastName,
    suffix=Suffix,
    contactDetails=@(
        email=EmailAddress,
        phone=Phone
    ),
    address=@(
        line1=AddressLine1,
        line2=AddressLine2,
        city=City,
        state=StateProvince,
        country=CountryRegion,
        postCode=PostalCode
    ),
    ids=[
        toString(CustomerID), toString(AddressID), rowguid
    ]
)
```

## <a name="next-steps"></a>다음 단계

- [복사 작업 개요](copy-activity-overview.md)
- [매핑 데이터 흐름](concepts-data-flow-overview.md)
- [조회 작업](control-flow-lookup-activity.md)
- [GetMetadata 작업](control-flow-get-metadata-activity.md)
