---
title: 매핑 데이터 흐름에서 데이터 변환 구문 분석
description: 포함된 열 문서 구문 분석
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/08/2021
ms.openlocfilehash: 4db9503ea84ae13148a89a03048c73399413e5cc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101710195"
---
# <a name="parse-transformation-in-mapping-data-flow"></a>매핑 데이터 흐름의 구문 분석 변환

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

구문 분석 변환을 사용하여 문서 형식으로 된 데이터의 열을 구문 분석합니다. 구문 분석할 수 있는 현재 지원되는 포함 문서 형식은 JSON 및 구분 기호로 분리된 텍스트입니다.

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RWykdO]

## <a name="configuration"></a>구성

구문 분석 변환 구성 패널에서 먼저 인라인으로 구문 분석하려는 열에 포함된 데이터 형식을 선택합니다. 구문 분석 변환에는 다음과 같은 구성 설정도 포함됩니다.

![구문 분석 설정](media/data-flow/data-flow-parse-1.png "구문 분석")

### <a name="column"></a>열

파생 열 및 집계와 마찬가지로 여기에서 종료되는 열을 드롭다운 선택기에서 선택하여 수정할 수 있습니다. 또는 여기에 새 열의 이름을 입력할 수 있습니다. ADF는 구문 분석된 원본 데이터를 이 열에 저장합니다.

### <a name="expression"></a>식

식 작성기를 사용하여 구문 분석에 사용할 원본을 설정합니다. 이는 구문 분석하려는 자체 포함 데이터가 있는 원본 열을 선택하는 것만큼 간단할 수도 있고, 구문 분석할 복잡한 식을 만들 수도 있습니다.

### <a name="output-column-type"></a>출력 열 형식

다음은 단일 열에 기록될 구문 분석에서 대상 출력 스키마를 구성하는 위치입니다.

![구문 분석 예제](media/data-flow/data-flow-parse-2.png "구문 분석 예제")

이 예제에서는 일반 텍스트이지만 JSON 구조로 형식이 지정된 들어오는 필드 "jsonString"의 구문 분석을 정의했습니다. 다음 스키마를 사용하여 구문 분석된 결과를 "json"이라는 새 열에 JSON으로 저장하겠습니다.

```(trade as boolean, customers as string[])```

검사 탭 및 데이터 미리 보기를 참조하여 출력이 올바르게 매핑되었는지 확인합니다.

## <a name="examples"></a>예

```
source(output(
        name as string,
        location as string,
        satellites as string[],
        goods as (trade as boolean, customers as string[], orders as (orderId as string, orderTotal as double, shipped as (orderItems as (itemName as string, itemQty as string)[]))[])
    ),
    allowSchemaDrift: true,
    validateSchema: false,
    ignoreNoFilesFound: false,
    documentForm: 'documentPerLine') ~> JsonSource
source(output(
        movieId as string,
        title as string,
        genres as string
    ),
    allowSchemaDrift: true,
    validateSchema: false,
    ignoreNoFilesFound: false) ~> CsvSource
JsonSource derive(jsonString = toString(goods)) ~> StringifyJson
StringifyJson parse(json = jsonString ? (trade as boolean,
        customers as string[]),
    format: 'json',
    documentForm: 'arrayOfDocuments') ~> ParseJson
CsvSource derive(csvString = 'Id|name|year\n\'1\'|\'test1\'|\'1999\'') ~> CsvString
CsvString parse(csv = csvString ? (id as integer,
        name as string,
        year as string),
    format: 'delimited',
    columnNamesAsHeader: true,
    columnDelimiter: '|',
    nullValue: '',
    documentForm: 'documentPerLine') ~> ParseCsv
ParseJson select(mapColumn(
        jsonString,
        json
    ),
    skipDuplicateMapInputs: true,
    skipDuplicateMapOutputs: true) ~> KeepStringAndParsedJson
ParseCsv select(mapColumn(
        csvString,
        csv
    ),
    skipDuplicateMapInputs: true,
    skipDuplicateMapOutputs: true) ~> KeepStringAndParsedCsv
```

## <a name="data-flow-script"></a>데이터 흐름 스크립트

### <a name="syntax"></a>구문

### <a name="examples"></a>예

```
parse(json = jsonString ? (trade as boolean,
                                customers as string[]),
                format: 'json',
                documentForm: 'singleDocument') ~> ParseJson

parse(csv = csvString ? (id as integer,
                                name as string,
                                year as string),
                format: 'delimited',
                columnNamesAsHeader: true,
                columnDelimiter: '|',
                nullValue: '',
                documentForm: 'documentPerLine') ~> ParseCsv
```    

## <a name="next-steps"></a>다음 단계

* [평면화된 변환](data-flow-flatten.md)을 사용하여 행을 열로 피벗합니다.
* [파생 열 변환](data-flow-derived-column.md)을 사용하여 열을 행으로 피벗합니다.
