---
title: Azure Data Factory JSON 형식 | Microsoft Docs
description: 이 항목에서는 Azure Data Factory에서 JSON 형식을 처리 하는 방법에 대해 설명 합니다.
author: linda33wj
manager: craigg
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 10/24/2019
ms.author: jingwang
ms.openlocfilehash: a01c95cbe9281c915e89572c07fcbc203193b962
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/25/2019
ms.locfileid: "72935490"
---
# <a name="json-format-in-azure-data-factory"></a>Azure Data Factory JSON 형식

Json **파일을 구문 분석 하거나 json 형식으로 데이터를 쓰려면**이 문서를 따르세요. 

다음 커넥터에 대해 JSON 형식이 지원 됩니다. [Amazon S3](connector-amazon-simple-storage-service.md), [azure Blob](connector-azure-blob-storage.md), [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md), [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md), [azure File Storage](connector-azure-file-storage.md), [파일 시스템](connector-file-system.md), [FTP](connector-ftp.md), [Google 클라우드 저장소](connector-google-cloud-storage.md), [HDFS](connector-hdfs.md), [HTTP](connector-http.md)및 [SFTP](connector-sftp.md).

## <a name="dataset-properties"></a>데이터 세트 속성

데이터 세트 정의에 사용할 수 있는 섹션 및 속성의 전체 목록은 [데이터 세트](concepts-datasets-linked-services.md) 문서를 참조하세요. 이 섹션에서는 JSON 데이터 집합에서 지 원하는 속성의 목록을 제공 합니다.

| 자산         | 설명                                                  | 필수 |
| ---------------- | ------------------------------------------------------------ | -------- |
| type             | 데이터 집합의 type 속성은 **Json**으로 설정 해야 합니다. | yes      |
| location         | 파일의 위치 설정입니다. 각 파일 기반 커넥터에는 `location`에서 고유한 위치 유형 및 지원 되는 속성이 있습니다. **커넥터 문서-> 데이터 집합 속성 섹션에서 세부 정보를 참조 하세요**. | yes      |
| encodingName     | 테스트 파일을 읽고 쓰는 데 사용 되는 인코딩 형식입니다. <br>허용 되는 값은 다음과 같습니다. "UTF-8", "UTF-16", "UTF-16 be", "UTF-32", "UTF-16-32BE", "US-ASCII", "UTF-7", "BIG5", "EUC-JP", "EUC-KR", "GB2312", "GB18030", "조합", "CP875", "", "CP866", "IBM00858", "IBM037", "IBM273", "IBM437", "IBM500", " IBM737", "IBM775", "IBM850", "IBM852", "IBM855", "IBM857", "IBM860", "IBM861", "IBM863", "IBM864", "IBM865", "IBM869", "IBM870", "IBM01140", "IBM01141", "IBM01142", "IBM01143", "IBM01144", "IBM01145", "IBM01146", "IBM01147", "IBM01148", "IBM01149" , "ISO-2022-JP", "ISO-2022-KR", "ISO-8859-1", "ISO-8859-2", "ISO-8859-3", "ISO-8859-4", "ISO-8859-5", "ISO-8859-6", "ISO-8859-7", "ISO-8859-8", "ISO-8859-9", "ISO-8859-13", "ISO-8859-15", "WINDOWS-874", "WINDOWS-1250", "WINDOWS-1251", " WINDOWS-1252 "," WINDOWS-1253 "," WINDOWS-1254 "," WINDOWS-1255 "," WINDOWS-1256 "," WINDOWS-1257 "," WINDOWS-1258 "입니다.| 아닙니다.       |
| compressionCodec | 텍스트 파일을 읽고 쓰는 데 사용 되는 압축 코덱입니다. <br>허용 되는 값은 **bzip2**, **gzip**, **deflate**, **ZipDeflate**, **snappy**또는 **lz4**입니다. 를 사용 하 여 파일을 저장할 수 있습니다. <br>참고 현재 복사 작업은 "snappy" & "lz4"를 지원 하지 않습니다. | 아닙니다.       |
| compressionLevel | 압축 비율입니다. <br>허용 되는 값은 **최적** 또는 **가장 빠릅니다**.<br>- **가장 빠름:** 압축 작업은 결과 파일이 최적으로 압축 되지 않은 경우에도 최대한 빨리 완료 되어야 합니다.<br>- **최적**: 작업을 완료 하는 데 시간이 더 오래 걸리는 경우에도 압축 작업을 최적으로 압축 해야 합니다. 자세한 내용은 [압축 수준](https://msdn.microsoft.com/library/system.io.compression.compressionlevel.aspx) 항목을 참조하세요. | 아닙니다.       |

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
```

## <a name="copy-activity-properties"></a>복사 작업 속성

작업 정의에 사용할 수 있는 섹션 및 속성의 전체 목록은 [파이프라인](concepts-pipelines-activities.md) 문서를 참조하세요. 이 섹션에서는 JSON 원본 및 싱크에서 지 원하는 속성의 목록을 제공 합니다.

### <a name="json-as-source"></a>JSON을 소스로

복사 작업 ***\*원본\**** 섹션에서 지원 되는 속성은 다음과 같습니다.

| 자산      | 설명                                                  | 필수 |
| ------------- | ------------------------------------------------------------ | -------- |
| type          | 복사 작업 원본의 type 속성은 **JSONSource**로 설정 해야 합니다. | yes      |
| 나이 설정 | 데이터 저장소에서 데이터를 읽는 방법에 대 한 속성 그룹입니다. 각 파일 기반 커넥터에는 `storeSettings`에서 고유한 지원 읽기 설정이 있습니다. **커넥터 문서-> 복사 작업 속성 섹션에서 세부 정보를 참조 하세요**. | 아닙니다.       |

### <a name="json-as-sink"></a>JSON을 싱크로

복사 작업 ***\*싱크\**** 섹션에서 지원 되는 속성은 다음과 같습니다.

| 자산      | 설명                                                  | 필수 |
| ------------- | ------------------------------------------------------------ | -------- |
| type          | 복사 작업 원본의 type 속성은 **JSONSink**로 설정 해야 합니다. | yes      |
| formatSettings | 속성 그룹입니다. 아래의 **JSON 쓰기 설정** 표를 참조 하세요. | 아닙니다.       |
| 나이 설정 | 데이터 저장소에 데이터를 쓰는 방법에 대 한 속성 그룹입니다. 각 파일 기반 커넥터에는 `storeSettings`에서 자체 지원 되는 쓰기 설정이 있습니다. **커넥터 문서-> 복사 작업 속성 섹션에서 세부 정보를 참조 하세요**. | 아닙니다.       |

`formatSettings`에서 지원 되는 **JSON 쓰기 설정** :

| 자산      | 설명                                                  | 필수                                              |
| ------------- | ------------------------------------------------------------ | ----------------------------------------------------- |
| type          | FormatSettings의 형식은 **JsonWriteSetting**로 설정 해야 합니다. | yes                                                   |
| filePattern |각 JSON 파일에 저장된 데이터의 패턴을 나타냅니다. 사용 가능한 값은 **setOfObjects** 및 **arrayOfObjects**이고 **기본값**은 **setOfObjects**입니다. 이러한 패턴에 대한 자세한 내용은 [JSON 파일 패턴](#json-file-patterns) 섹션을 참조하세요. |아닙니다. |

### <a name="json-file-patterns"></a>JSON 파일 패턴

복사 작업은 다음과 같은 JSON 파일 패턴을 자동으로 검색 하 고 구문 분석할 수 있습니다. 

- **유형 I: setOfObjects**

    각 파일에 단일 개체 또는 줄로 구분된/연결된 여러 개체가 포함되어 있습니다. 
    복사 작업 싱크에서이 옵션을 선택 하면 복사 작업에서 각 개체 (줄로 구분)를 사용 하 여 단일 JSON 파일을 생성 합니다.

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

데이터 흐름 매핑의 [원본 변환](data-flow-source.md) 및 [싱크 변환](data-flow-sink.md) 에서 자세한 정보를 알아보세요.

## <a name="next-steps"></a>다음 단계

- [복사 작업 개요](copy-activity-overview.md)
- [데이터 흐름 매핑](concepts-data-flow-overview.md)
- [조회 작업](control-flow-lookup-activity.md)
- [GetMetadata 작업](control-flow-get-metadata-activity.md)
