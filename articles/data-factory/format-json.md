---
title: Azure 데이터 팩터리의 JSON 형식
description: 이 항목에서는 Azure 데이터 팩터리에서 JSON 형식을 처리하는 방법에 대해 설명합니다.
author: linda33wj
manager: shwang
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 02/05/2020
ms.author: jingwang
ms.openlocfilehash: 7dac8d21e3b45307284ece15ca5ddbcc69db909b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79260645"
---
# <a name="json-format-in-azure-data-factory"></a>Azure 데이터 팩터리의 JSON 형식

**JSON 파일을 구문 분석하거나 데이터를 JSON 형식으로 쓰려면**이 문서를 따르십시오. 

JSON 형식은 다음과 같은 커넥터에 대 한 지원 됩니다: [아마존 S3,](connector-amazon-simple-storage-service.md) [Azure Blob,](connector-azure-blob-storage.md) [Azure 데이터 호수 스토리지 Gen1,](connector-azure-data-lake-store.md)Azure 데이터 호수 스토리지 [Gen2,](connector-azure-data-lake-storage.md) [Azure 파일 스토리지,](connector-azure-file-storage.md)파일 [시스템,](connector-file-system.md) [FTP,](connector-ftp.md) [구글 클라우드 스토리지,](connector-google-cloud-storage.md) [HDFS,](connector-hdfs.md) [HTTP,](connector-http.md)및 [SFTP.](connector-sftp.md)

## <a name="dataset-properties"></a>데이터 세트 속성

데이터 세트 정의에 사용할 수 있는 섹션 및 속성의 전체 목록은 [데이터 세트](concepts-datasets-linked-services.md) 문서를 참조하세요. 이 섹션에서는 JSON 데이터 집합에서 지원하는 속성 목록을 제공합니다.

| 속성         | 설명                                                  | 필수 |
| ---------------- | ------------------------------------------------------------ | -------- |
| type             | 데이터 집합의 형식 속성은 **Json으로**설정해야 합니다. | yes      |
| 위치         | 파일의 위치 설정입니다. 각 파일 기반 커넥터에는 고유한 위치 유형과 `location`지원되는 속성이 있습니다. **커넥터 문서 -> 데이터 집합 속성 섹션의 세부 정보를 참조하십시오.** | yes      |
| encodingName     | 테스트 파일을 읽고 쓰는 데 사용되는 인코딩 유형입니다. <br>허용된 값은 다음과 같습니다: "UTF-8", "UTF-16", "UTF-16BE", "UTF-32", "UTF-32BE", "US-ASCII", "UTF-7", "BIG5", "EUC-JP", "EUC-JP", "GB2312", "GB2312", "GB18030", "JOHAB", "SHIFT-JIS", "CP875", "CP866", "IBM00858", "IBM0037", "IBM273", "IBM273", "IBM273", "IBM273", "IBM273", "IBM273", "IBM273", "IBM273", "IBM273", "IBM273", "IBM273", "IBM273", "IBM273", "IBM273", "IBM273", "IBM273", "IBM273", "IBM273", "IBM273", "IBM273", "IBM273", "IBM273", "IBM273", "IBM273", "IBM273", "IBM273", "IBM273", "IBM273", "IBM273", "IBM273", "IBM273", "IBM273", "IBM273 IBM737", "IBM775", "IBM850", "IBM852", "IBM855", "IBM857", "IBM860", "IBM860", "IBM861", "IBM863", "IBM864", "IBM865", "IBM869", "IBM870", "IBM01140", "IBM01141", "IBM01142", "IBM01143", "IBM011444", "IBM01145", "IBM01146", "IBM01147", "IBM01147", "IBM01148", "IBM01148", "IBM01142", "IBM01142", "IBM01142", "IBM01142", "IBM011442", "IBM011444", "IBM01147", "IBM01147", "IBM01147", "IBM01148", "IBM01148", "IBM011442", "IBM011442", "IBM011444", "IBM011447", "IBM01147", "IBM011448", "IBM01148", "IBM01144", "IBM0114142", "IBM011144 , "ISO-2022-JP", "ISO-2022-KR", "ISO-8859-1", "ISO-8859-2", "ISO-8859-3", "ISO-8859-4", "ISO-8859-5", "ISO-8859-6", "ISO-8859-7", "ISO-8859-8", "ISO-8859-9", "ISO-8859-13", "ISO-8859-15", "WINDOWS-874", "WINDOWS-1250", "WINDOWS-1250", "WINDOWS-125", "WINDOWS-1250", "WINDOWS-1250", "WINDOWS-1250", "WINDOWS-1250", "WINDOWS-1259", "WINDOWS-1259", "WINDOWS-125", "125", "ISO-8859-8", "ISO-8859-8", "ISO-8859-9", "ISO-8859-9", "ISO-8859-13", "ISO-8859-13", "WINDOWS-1250", "WINDOWS-1250", "WINDOWS-1250", "WINDOWS-125", "WINDOWS-1259", "WINDOWS-1250", "WINDOWS 윈도우-1252", "윈도우-1253", "윈도우-1254", "윈도우-1255", "윈도우-1256", "윈도우-1257", "윈도우-1258".| 예       |
| 압축 코덱 | 텍스트 파일을 읽고 쓰는 데 사용되는 압축 코덱입니다. <br>허용된 값은 **bzip2,** **gzip,** **deflate,** **ZipDeflate,** **snappy,** 또는 **lz4입니다.** 파일을 저장할 때 사용할 수 있습니다. <br>참고 현재 복사 활동은 "lz4"& "스냅"을 지원하지 않습니다.<br>복사 활동을 사용하여 ZipDeflate 파일의 압축을 풀고 파일 기반 싱크 데이터 저장소에 쓸 때 `<path specified in dataset>/<folder named as source zip file>/`파일이 폴더로 추출됩니다. | 예       |
| 압축 레벨 | 압축 비율입니다. <br>허용된 값은 **최적** 또는 **가장 빠릅니다.**<br>- **가장 빠른 속도:** 결과 파일이 최적으로 압축되지 않은 경우에도 압축 작업이 가능한 한 빨리 완료되어야 합니다.<br>- **최적**: 작업을 완료하는 데 시간이 오래 걸리더라도 압축 작업을 최적으로 압축해야 합니다. 자세한 내용은 [압축 수준](https://msdn.microsoft.com/library/system.io.compression.compressionlevel.aspx) 항목을 참조하세요. | 예       |

다음은 Azure Blob 저장소의 JSON 데이터 집합의 예입니다.

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
```

## <a name="copy-activity-properties"></a>복사 작업 속성

작업 정의에 사용할 수 있는 섹션 및 속성의 전체 목록은 [파이프라인](concepts-pipelines-activities.md) 문서를 참조하세요. 이 섹션에서는 JSON 소스 및 싱크에서 지원하는 속성 목록을 제공합니다.

### <a name="json-as-source"></a>JSON 소스

다음 속성은 복사 활동 *** \*소스\* *** 섹션에서 지원됩니다.

| 속성      | 설명                                                  | 필수 |
| ------------- | ------------------------------------------------------------ | -------- |
| type          | 복사 활동 소스의 형식 속성은 **JSONSource**로 설정해야 합니다. | yes      |
| 매장 설정 | 데이터 저장소에서 데이터를 읽는 방법에 대한 속성 그룹입니다. 각 파일 기반 커넥터에는 에서 `storeSettings`자체 지원되는 읽기 설정이 있습니다. **커넥터 문서 -> 복사 활동 속성 섹션의 세부 정보를 참조하십시오.** | 예       |

### <a name="json-as-sink"></a>JSON AS 싱크대

다음 속성은 복사 활동 *** \*싱크\* *** 섹션에서 지원됩니다.

| 속성      | 설명                                                  | 필수 |
| ------------- | ------------------------------------------------------------ | -------- |
| type          | 복사 활동 소스의 형식 속성은 **JSONSink로**설정해야 합니다. | yes      |
| 형식설정 | 속성 그룹입니다. 아래의 **JSON 쓰기 설정** 표를 참조하십시오. | 예       |
| 매장 설정 | 데이터 저장소에 데이터를 작성하는 방법에 대한 속성 그룹입니다. 각 파일 기반 커넥터에는 에서 `storeSettings`자체 지원되는 쓰기 설정이 있습니다. **커넥터 문서 -> 복사 활동 속성 섹션의 세부 정보를 참조하십시오.** | 예       |

**지원JSON 쓰기** `formatSettings`설정 아래에서 :

| 속성      | 설명                                                  | 필수                                              |
| ------------- | ------------------------------------------------------------ | ----------------------------------------------------- |
| type          | 형식설정의 형식은 **JsonWriteSettings로**설정해야 합니다. | yes                                                   |
| filePattern |각 JSON 파일에 저장된 데이터의 패턴을 나타냅니다. 사용 가능한 값은 **setOfObjects** 및 **arrayOfObjects**이고 **기본값은** **setOfObjects**. 이러한 패턴에 대한 자세한 내용은 [JSON 파일 패턴](#json-file-patterns) 섹션을 참조하세요. |예 |

### <a name="json-file-patterns"></a>JSON 파일 패턴

복사 활동은 JSON 파일의 다음 패턴을 자동으로 감지하고 구문 분석할 수 있습니다. 

- **유형 I: setOfObjects**

    각 파일에 단일 개체 또는 줄로 구분된/연결된 여러 개체가 포함되어 있습니다. 
    복사 활동 싱크에서 이 옵션을 선택하면 복사 활동은 줄당 각 개체가 있는 단일 JSON 파일을 생성합니다(줄 구분).

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

    * **줄로 구분된 JSON 예제**

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

## <a name="mapping-data-flow-properties"></a>데이터 흐름 속성 매핑

JSON 파일 형식은 데이터 흐름 매핑에서 싱크 및 소스로 모두 사용할 수 있습니다.

### <a name="creating-json-structures-in-a-derived-column"></a>파생 된 열에 JSON 구조 만들기

파생 된 열 식 빌더를 통해 데이터 흐름에 복잡한 열을 추가할 수 있습니다. 파생 된 열 변환에서 새 열을 추가하고 파란색 상자를 클릭하여 식 작성기의 열기를 엽니다. 열을 복잡하게 만들려면 JSON 구조를 수동으로 입력하거나 UX를 사용하여 하위 열을 대화식으로 추가할 수 있습니다.

#### <a name="using-the-expression-builder-ux"></a>식 작성기 UX 사용

출력 스키마 측면 창에서 열 위로 마우스를 가져가면 더하기 아이콘을 클릭합니다. 열을 복잡한 유형으로 만들려면 **하위 열 추가를** 선택합니다.

![하위 열 추가](media/data-flow/addsubcolumn.png "하위 열 추가")

같은 방법으로 열과 하위 열을 추가할 수 있습니다. 복잡하지 않은 각 필드에 대해 오른쪽의 식 편집기에서 식을 추가할 수 있습니다.

![복잡한 열](media/data-flow/complexcolumn.png "복잡한 열")

#### <a name="entering-the-json-structure-manually"></a>JSON 구조에 수동으로 진입

JSON 구조를 수동으로 추가하려면 새 열을 추가하고 편집기에서 식을 입력합니다. 식은 다음과 같은 일반적인 형식을 따릅니다.

```
@(
    field1=0,
    field2=@(
        field1=0
    )
)
```

"complexColumn"이라는 열에 대해 이 식을 입력한 경우 다음 JSON으로 싱크에 기록됩니다.

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

#### <a name="sample-manual-script-for-complete-hierarchical-definition"></a>완벽한 계층적 정의를 위한 수동 스크립트 샘플
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

### <a name="source-format-options"></a>소스 형식 옵션

JSON 데이터 집합을 데이터 흐름의 원본으로 사용하면 5개의 추가 설정을 설정할 수 있습니다. 이러한 설정은 **소스 옵션** 탭의 **JSON 설정** 아코디언에서 찾을 수 있습니다.  

![JSON 설정](media/data-flow/json-settings.png "JSON 설정")

#### <a name="default"></a>기본값

기본적으로 JSON 데이터는 다음과 같은 형식으로 읽습니다.

```
{ "json": "record 1" }
{ "json": "record 2" }
{ "json": "record 3" }
```

#### <a name="single-document"></a>단일 문서

**단일 문서를** 선택하면 매핑 데이터 흐름이 각 파일에서 하나의 JSON 문서를 읽습니다. 

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

#### <a name="unquoted-column-names"></a>인용되지 않은 열 이름

**따옴표가 지정되지 않은 열 이름을** 선택하면 데이터 흐름 매핑은 따옴표로 둘러싸여 있지 않은 JSON 열을 읽습니다. 

```
{ json: "record 1" }
{ json: "record 2" }
{ json: "record 3" }
```

#### <a name="has-comments"></a>댓글이 있습니다.

JSON 데이터에 C 또는 C++ 스타일 주석이 있는 경우 **주석 을 선택합니다.**

``` json
{ "json": /** comment **/ "record 1" }
{ "json": "record 2" }
{ /** comment **/ "json": "record 3" }
```

#### <a name="single-quoted"></a>단일 견적

JSON 필드와 값이 큰따옴표 대신 따옴표를 사용하는 **경우** 따옴표로 된 Single을 선택합니다.

```
{ 'json': 'record 1' }
{ 'json': 'record 2' }
{ 'json': 'record 3' }
```

#### <a name="backslash-escaped"></a>백슬래시 탈출

JSON 데이터에서 문자를 이스케이프하는 데 백슬래시가 사용되는 경우 **따옴표로 인용된 Single을** 선택합니다.

```
{ "json": "record 1" }
{ "json": "\} \" \' \\ \n \\n record 2" }
{ "json": "record 3" }
```

## <a name="next-steps"></a>다음 단계

- [활동 개요 복사](copy-activity-overview.md)
- [데이터 흐름 매핑](concepts-data-flow-overview.md)
- [조회 활동](control-flow-lookup-activity.md)
- [GetMetadata 작업](control-flow-get-metadata-activity.md)
