---
title: 복사 활동의 스키마 및 데이터 형식 매핑
description: Azure Data Factory 복사 작업에서 원본 데이터의 스키마와 데이터 형식을 싱크 데이터에 매핑하는 방법에 대해 알아봅니다.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 06/22/2020
ms.author: jingwang
ms.openlocfilehash: b48fb28a56cdc1c836233cd2bd03a1f9e750a0a7
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85249655"
---
# <a name="schema-and-data-type-mapping-in-copy-activity"></a>복사 활동의 스키마 및 데이터 형식 매핑
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

이 문서에서는 Azure Data Factory 복사 작업을 수행 하 여 원본 데이터에서 싱크 데이터로 스키마 매핑 및 데이터 형식 매핑을 수행 하는 방법을 설명 합니다.

## <a name="schema-mapping"></a>스키마 매핑

### <a name="default-mapping"></a>기본 매핑

기본적으로 복사 작업은 원본 데이터를 대/소문자 구분 방식으로 **열 이름으로** 싱크에 매핑합니다. 예를 들어 파일에 쓰는 경우와 같이 싱크가 없으면 원본 필드 이름이 싱크 이름으로 유지 됩니다. 이러한 기본 매핑은 원본에서 싱크로 실행으로의 유연한 스키마 및 스키마 드리프트를 지원 합니다. 원본 데이터 저장소에서 반환 된 모든 데이터는 싱크로 복사할 수 있습니다.

소스가 머리글 줄이 없는 텍스트 파일인 경우 원본에 열 이름이 포함 되지 않으므로 [명시적 매핑이](#explicit-mapping) 필요 합니다.

### <a name="explicit-mapping"></a>명시적 매핑

명시적 매핑을 지정 하 여 필요에 따라 원본에서 싱크로 열/필드 매핑을 사용자 지정할 수도 있습니다. 명시적 매핑을 사용 하면 부분 원본 데이터만 싱크로 복사 하거나, 다른 이름을 가진 싱크에 원본 데이터를 매핑하거나, 테이블 형식/계층 구조 데이터의 모양을 변경할 수 있습니다. 복사 작업:

1. 원본에서 데이터를 읽고 소스 스키마를 확인 합니다.
2. 정의 된 매핑을 적용 합니다.
3. 싱크에 데이터를 씁니다.

다음에 대해 자세히 알아보세요.

- [테이블 형식 소스에서 테이블 형식 싱크로](#tabular-source-to-tabular-sink)
- [계층적 원본에서 테이블 형식 싱크로](#hierarchical-source-to-tabular-sink)
- [계층 구조 싱크로 테이블 형식/계층 구조 원본](#tabularhierarchical-source-to-hierarchical-sink)

UI를 제작 하는 Data Factory > 복사 작업-> 매핑 탭에서 매핑을 구성 하거나 복사 작업-> 속성에서 프로그래밍 방식으로 매핑을 지정할 수 있습니다 `translator` . 다음 속성은 `translator`  ->  `mappings` `source` `sink` 데이터를 매핑할 특정 열/필드를 가리키는 및 > 배열 > 개체에서 지원 됩니다.

| 속성 | 설명                                                  | 필수 |
| -------- | ------------------------------------------------------------ | -------- |
| name     | 원본 또는 싱크 열/필드의 이름입니다. 테이블 형식 소스 및 싱크에 적용 됩니다. | 예      |
| ordinal  | 열 인덱스입니다. 1부터 시작 합니다. <br>머리글 줄 없이 분리 된 텍스트를 사용 하는 경우 적용 하 고 필요 합니다. | 아니요       |
| 경로     | 추출 하거나 매핑할 각 필드에 대 한 JSON 경로 식입니다. 계층 구조 원본 및 싱크에 대해 적용 합니다 (예: Cosmos DB, MongoDB 또는 REST 커넥터).<br>루트 개체 아래에 있는 필드의 경우 JSON 경로는 root로 시작 하 `$` 고, 속성으로 선택한 배열 내에 있는 필드의 경우 `collectionReference` json 경로는를 사용 하지 않고 배열 요소에서 시작 `$` 합니다. | 아니요       |
| type     | 원본 또는 싱크 열의 중간 데이터 형식을 Data Factory 합니다. 일반적으로이 속성을 지정 하거나 변경할 필요가 없습니다. [데이터 형식 매핑에](#data-type-mapping)대해 자세히 알아보세요. | 예       |
| culture  | 원본 또는 싱크 열의 문화권입니다. Type이 또는 인 경우에 적용 됩니다 `Datetime` `Datetimeoffset` . 기본값은 `en-us`입니다.<br>일반적으로이 속성을 지정 하거나 변경할 필요가 없습니다. [데이터 형식 매핑에](#data-type-mapping)대해 자세히 알아보세요. | 아니요       |
| format   | 형식이 또는 일 때 사용할 서식 문자열입니다 `Datetime` `Datetimeoffset` . 날짜/시간 형식을 지정하는 방법은 [사용자 지정 날짜 및 시간 형식 문자열](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings)을 참조하세요. 일반적으로이 속성을 지정 하거나 변경할 필요가 없습니다. [데이터 형식 매핑에](#data-type-mapping)대해 자세히 알아보세요. | 아니요       |

다음은 외에도에서 지원 되는 속성 `translator` 입니다 `mappings` .

| 속성            | 설명                                                  | 필요한 공간 |
| ------------------- | ------------------------------------------------------------ | -------- |
| collectionReference | 계층 구조 원본에서 데이터를 복사 하는 경우 (예: Cosmos DB, MongoDB 또는 REST 커넥터)에 적용 됩니다.<br>동일한 패턴으로 **배열 필드 내부**의 개체에서 데이터를 반복 및 추출하고 행별 개체별로 변환하려면 교차 적용하도록 해당 배열의 JSON 경로를 지정합니다. | 아니요       |

#### <a name="tabular-source-to-tabular-sink"></a>테이블 형식 소스에서 테이블 형식 싱크로

예를 들어 Salesforce에서 Azure SQL Database로 데이터를 복사 하 고 다음 세 개의 열을 명시적으로 매핑하는 경우입니다.

1. 복사 작업-> 매핑 탭에서 **스키마 가져오기** 단추를 클릭 하 여 원본 및 싱크 스키마를 모두 가져옵니다.

2. 필요한 필드를 매핑하고 나머지를 제외/삭제 합니다.

![테이블 형식에 테이블 형식 매핑](media/copy-activity-schema-and-type-mapping/map-tabular-to-tabular.png)

복사 작업 페이로드에서 동일한 매핑을 다음과 같이 구성할 수 있습니다 (참조 `translator` ).

```json
{
    "name": "CopyActivityTabularToTabular",
    "type": "Copy",
    "typeProperties": {
        "source": { "type": "SalesforceSource" },
        "sink": { "type": "SqlSink" },
        "translator": {
            "type": "TabularTranslator",
            "mappings": [
                {
                    "source": { "name": "Id" },
                    "sink": { "name": "CustomerID" }
                },
                {
                    "source": { "name": "Name" },
                    "sink": { "name": "LastName" }
                },
                {
                    "source": { "name": "LastModifiedDate" },
                    "sink": { "name": "ModifiedDate" }
                }
            ]
        }
    },
    ...
}
```

헤더 줄이 없는 구분 기호로 분리 된 텍스트 파일에서 데이터를 복사 하려면 열이 이름 대신 서 수로 표시 됩니다. 

```json
{
    "name": "CopyActivityTabularToTabular",
    "type": "Copy",
    "typeProperties": {
        "source": { "type": "DelimitedTextSource" },
        "sink": { "type": "SqlSink" },
        "translator": {
            "type": "TabularTranslator",
            "mappings": [
                {
                    "source": { "ordinal": "1" },
                    "sink": { "name": "CustomerID" }
                }, 
                {
                    "source": { "ordinal": "2" },
                    "sink": { "name": "LastName" }
                }, 
                {
                    "source": { "ordinal": "3" },
                    "sink": { "name": "ModifiedDate" }
                }
            ]
        }
    },
    ...
}
```

#### <a name="hierarchical-source-to-tabular-sink"></a>계층적 원본에서 테이블 형식 싱크로

계층적 원본에서 테이블 형식 싱크로 데이터를 복사 하는 경우 복사 작업은 다음과 같은 기능을 지원 합니다.

- 개체 및 배열에서 데이터를 추출 합니다.
- 배열의 동일한 패턴을 사용 하 여 여러 개체를 교차 적용 합니다 .이 경우 하나의 JSON 개체를 테이블 형식 결과의 여러 레코드로 변환할 수 있습니다.

고급 계층적-테이블 형식 변환의 경우 [데이터 흐름](concepts-data-flow-overview.md)을 사용할 수 있습니다. 

예를 들어 다음 내용이 포함 된 원본 MongoDB 문서가 있는 경우:

```json
{
    "id": {
        "$oid": "592e07800000000000000000"
    },
    "number": "01",
    "date": "20170122",
    "orders": [
        {
            "prod": "p1",
            "price": 23
        },
        {
            "prod": "p2",
            "price": 13
        },
        {
            "prod": "p3",
            "price": 231
        }
    ],
    "city": [ { "name": "Seattle" } ]
}
```

또한 헤더를 사용 하 여 다음 형식으로 텍스트 파일에 복사 하려는 경우 *(order_pd 및 order_price)* 배열 내에 데이터를 평면화 하 고 공통 루트 정보 *(숫자, 날짜 및 도시)* 를 사용 하 여 크로스 조인을 수행 합니다.

| orderNumber | orderDate | order_pd | order_price | city    |
| ----------- | --------- | -------- | ----------- | ------- |
| 01          | 20170122  | P1       | 23          | Seattle |
| 01          | 20170122  | P2       | 13          | Seattle |
| 01          | 20170122  | P3       | 231         | Seattle |

Data Factory 제작 UI에서 이러한 매핑을 정의할 수 있습니다.

1. 복사 작업-> 매핑 탭에서 **스키마 가져오기** 단추를 클릭 하 여 원본 및 싱크 스키마를 모두 가져옵니다. 스키마를 가져올 때 최상위 개체를 샘플링 하는 Data Factory, 필드가 표시 되지 않는 경우 계층 구조에서 올바른 계층에 추가 하 고 기존 필드 이름을 마우스로 가리킨 다음 노드, 개체 또는 배열을 추가 하도록 선택할 수 있습니다.

2. 데이터를 반복 하 고 추출할 배열을 선택 합니다. 이는 자동으로 **컬렉션 참조**로 채워집니다. 참고 이러한 작업에는 단일 배열만 지원 됩니다.

3. 필요한 필드를 싱크에 매핑합니다. Data Factory는 계층 구조 쪽에 해당 하는 JSON 경로를 자동으로 결정 합니다.

![UI를 사용 하 여 테이블 형식에 계층적 매핑](media/copy-activity-schema-and-type-mapping/map-hierarchical-to-tabular-ui.png)

**고급 편집기**로 전환할 수도 있습니다 .이 경우 필드의 JSON 경로를 직접 보고 편집할 수 있습니다. 이 보기에서 새 매핑을 추가 하도록 선택한 경우 JSON 경로를 지정 합니다.

![고급 편집기를 사용 하 여 테이블 형식에 계층적 매핑](media/copy-activity-schema-and-type-mapping/map-hierarchical-to-tabular-advanced-editor.png)

복사 작업 페이로드에서 동일한 매핑을 다음과 같이 구성할 수 있습니다 (참조 `translator` ).


```json
{
    "name": "CopyActivityHierarchicalToTabular",
    "type": "Copy",
    "typeProperties": {
        "source": { "type": "MongoDbV2Source" },
        "sink": { "type": "DelimitedTextSink" },
        "translator": {
            "type": "TabularTranslator",
            "mappings": [
                {
                    "source": { "path": "$['number']" },
                    "sink": { "name": "orderNumber" }
                },
                {
                    "source": { "path": "$['date']" },
                    "sink": { "name": "orderDate" }
                },
                {
                    "source": { "path": "['prod']" },
                    "sink": { "name": "order_pd" }
                },
                {
                    "source": { "path": "['price']" },
                    "sink": { "name": "order_price" }
                },
                {
                    "source": { "path": "$['city'][0]['name']" },
                    "sink": { "name": "city" }
                }
            ],
            "collectionReference": "$['orders']"
        }
    },
    ...
}
```

#### <a name="tabularhierarchical-source-to-hierarchical-sink"></a>계층 구조 싱크로 테이블 형식/계층 구조 원본

사용자 환경 흐름은 [계층적 원본에서 테이블 형식 싱크로](#hierarchical-source-to-tabular-sink)유사 합니다. 

테이블 형식 소스에서 계층 싱크로 데이터를 복사 하는 경우 개체 안의 배열에 대 한 쓰기는 지원 되지 않습니다.

계층적 원본에서 계층적 싱크로 데이터를 복사 하는 경우 개체/배열을 선택 하 고 내부 필드를 건드리지 않고 싱크로 매핑하여 전체 계층의 계층 구조를 추가로 유지할 수 있습니다.

고급 데이터 변경 변환을 위해 [데이터 흐름](concepts-data-flow-overview.md)을 사용할 수 있습니다. 

### <a name="parameterize-mapping"></a>매핑 매개 변수화

템플릿 화 파이프라인을 만들어 많은 수의 개체를 동적으로 복사 하려면 [기본 매핑을](#default-mapping) 사용할 수 있는지 또는 해당 개체에 대 한 [명시적 매핑을](#explicit-mapping) 정의 해야 하는지 여부를 결정 합니다.

명시적 매핑이 필요한 경우 다음을 수행할 수 있습니다.

1. 파이프라인 수준에서 개체 형식으로 매개 변수를 정의 합니다 (예:) `mapping` .

2. 매핑 매개 변수화: 복사 작업-> 매핑 탭에서 동적 콘텐츠 추가를 선택 하 고 위의 매개 변수를 선택 합니다. 활동 페이로드는 다음과 같습니다.

    ```json
    {
        "name": "CopyActivityHierarchicalToTabular",
        "type": "Copy",
        "typeProperties": {
            "source": {...},
            "sink": {...},
            "translator": {
                "value": "@pipeline().parameters.mapping",
                "type": "Expression"
            },
            ...
        }
    }
    ```

3. 매핑 매개 변수에 전달할 값을 생성 합니다. 이 클래스는 전체 정의 개체 여야 하며 `translator` , [명시적 매핑의](#explicit-mapping) 샘플 섹션을 참조 하세요. 예를 들어 테이블 형식 원본에서 테이블 형식 싱크 복사의 경우 값은 이어야 합니다 `{"type":"TabularTranslator","mappings":[{"source":{"name":"Id"},"sink":{"name":"CustomerID"}},{"source":{"name":"Name"},"sink":{"name":"LastName"}},{"source":{"name":"LastModifiedDate"},"sink":{"name":"ModifiedDate"}}]}` .

## <a name="data-type-mapping"></a>데이터 형식 매핑

복사 작업은 다음과 같은 흐름을 사용 하 여 싱크 형식 매핑에 대 한 소스 형식을 수행 합니다. 

1. 원본 네이티브 데이터 형식에서 Azure Data Factory 중간 데이터 형식으로 변환 합니다.
2. 필요에 따라 중간 데이터 형식을 자동으로 변환 하 여 [기본 매핑과](#default-mapping) [명시적 매핑에](#explicit-mapping)적용 되는 해당 싱크 형식을 일치 시킵니다.
3. Azure Data Factory 중간 데이터 형식에서 싱크 네이티브 데이터 형식으로 변환 합니다.

복사 작업은 현재 부울, 바이트, 바이트 배열, Datetime, DatetimeOffset, Decimal, Double, GUID, Int16, Int32, Int64, SByte, Single, String, Timespan, UInt16, UInt32 및 UInt64와 같은 중간 데이터 형식을 지원 합니다.

원본에서 싱크로의 중간 형식 간에는 다음과 같은 데이터 형식 변환이 지원 됩니다.

| Source\Sink | 부울 | 바이트 배열 | Decimal | 날짜/시간 <small>(1)</small> | 부동 소수점 <small>(2)</small> | GUID | 정수 <small>(3)</small> | String | TimeSpan |
| ----------- | ------- | ---------- | ------- | ---------------------------- | ------------------------------ | ---- | -------------------------- | ------ | -------- |
| 부울     | ✓       |            | ✓       |                              | ✓                              |      | ✓                          | ✓      |          |
| 바이트 배열  |         | ✓          |         |                              |                                |      |                            | ✓      |          |
| 날짜/시간   |         |            |         | ✓                            |                                |      |                            | ✓      |          |
| Decimal     | ✓       |            | ✓       |                              | ✓                              |      | ✓                          | ✓      |          |
| 부동 소수점 | ✓       |            | ✓       |                              | ✓                              |      | ✓                          | ✓      |          |
| GUID        |         |            |         |                              |                                | ✓    |                            | ✓      |          |
| 정수     | ✓       |            | ✓       |                              | ✓                              |      | ✓                          | ✓      |          |
| String      | ✓       | ✓          | ✓       | ✓                            | ✓                              | ✓    | ✓                          | ✓      | ✓        |
| TimeSpan    |         |            |         |                              |                                |      |                            | ✓      | ✓        |

(1) 날짜/시간에는 DateTime 및 DateTimeOffset이 포함 됩니다.

(2) 부동 소수점에는 Single 및 Double이 포함 됩니다.

(3) 정수에는 SByte, Byte, Int16, UInt16, Int32, UInt32, Int64 및 UInt64가 포함 됩니다.

> [!NOTE]
> - 현재 이러한 데이터 형식 변환은 표 형식 데이터 간에 복사할 때 지원 됩니다. 계층 구조 원본/싱크는 지원 되지 않습니다. 즉, 원본 및 싱크 중간 형식 간에 시스템에서 정의 된 데이터 형식 변환이 없습니다.
> - 이 기능은 최신 데이터 집합 모델에서 작동 합니다. UI에서이 옵션이 표시 되지 않으면 새 데이터 집합을 만들어 보세요.

다음은 데이터 형식 변환에 대 한 복사 작업에서 지원 되는 속성입니다 ( `translator` programmatical authoring 섹션 아래).

| 속성                         | 설명                                                  | 필요한 공간 |
| -------------------------------- | ------------------------------------------------------------ | -------- |
| typeConversion                   | 새 데이터 형식 변환 환경을 사용 하도록 설정 합니다. <br>이전 버전과의 호환성으로 인해 기본값은 false입니다.<br><br>6 월 2020 일 이후 Data Factory authoring UI를 통해 만들어진 새 복사 작업의 경우,이 데이터 형식 변환은 기본적으로 최상의 환경에서 사용 하도록 설정 되며, 해당 시나리오에 대 한 복사 작업-> 매핑 탭에서 다음과 같은 형식 변환 설정을 확인할 수 있습니다. <br>파이프라인을 프로그래밍 방식으로 만들려면 속성을 true로 명시적으로 설정 하 여 사용 하도록 설정 해야 `typeConversion` 합니다.<br>이 기능을 해제 하기 전에 만든 기존 복사 활동의 경우 이전 버전과의 호환성을 위해 Data Factory 제작 UI에 대 한 형식 변환 옵션이 표시 되지 않습니다. | 아니요       |
| typeConversionSettings           | 형식 변환 설정의 그룹입니다. `typeConversion`가로 설정 된 경우 적용 됩니다 `true` . 다음 속성은 모두이 그룹에 있습니다. | 아니요       |
| *에서`typeConversionSettings`* |                                                              |          |
| allowDataTruncation              | 복사 중에 다른 유형의 싱크로 원본 데이터를 변환할 때 데이터 잘림을 허용 합니다 (예: decimal에서 integer로, DatetimeOffset에서 Datetime으로). <br>기본값은 true입니다. | 아니요       |
| treatBooleanAsNumber             | 부울을 숫자로 처리 합니다 (예: 1).<br>기본값은 False입니다. | 아니요       |
| dateTimeFormat                   | 표준 시간대 오프셋과 문자열 (예:)이 없는 날짜 사이를 변환할 때의 형식 문자열 `yyyy-MM-dd HH:mm:ss.fff` 입니다.  자세한 내용은 [사용자 지정 날짜 및 시간 형식 문자열](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings) 을 참조 하세요. | 아니요       |
| dateTimeOffsetFormat             | 표준 시간대 오프셋과 문자열 (예:)을 사용 하 여 날짜 사이를 변환할 때의 형식 문자열 `yyyy-MM-dd HH:mm:ss.fff zzz` 입니다.  자세한 내용은 [사용자 지정 날짜 및 시간 형식 문자열](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings) 을 참조 하세요. | 아니요       |
| Times및 형식                   | 기간과 문자열을 변환할 때 형식 문자열입니다 (예:) `dd\.hh\:mm` . 자세한 내용은 [사용자 지정 TimeSpan 형식 문자열](https://docs.microsoft.com/dotnet/standard/base-types/custom-timespan-format-strings) 을 참조 하세요. | 예       |
| culture                          | 형식 (예: 또는)을 변환할 때 사용할 문화권 정보 `en-us` 입니다 `fr-fr` . | 아니요       |

**예:**

```json
{
    "name": "CopyActivity",
    "type": "Copy",
    "typeProperties": {
        "source": {
            "type": "ParquetSource"
        },
        "sink": {
            "type": "SqlSink"
        },
        "translator": {
            "type": "TabularTranslator",
            "typeConversion": true,
            "typeConversionSettings": {
                "allowDataTruncation": true,
                "treatBooleanAsNumber": true,
                "dateTimeFormat": "yyyy-MM-dd HH:mm:ss.fff",
                "dateTimeOffsetFormat": "yyyy-MM-dd HH:mm:ss.fff zzz",
                "timeSpanFormat": "dd\.hh\:mm",
                "culture": "en-gb"
            }
        }
    },
    ...
}
```

## <a name="legacy-models"></a>레거시 모델

> [!NOTE]
> 원본 열/필드를 싱크로 매핑하기 위한 다음 모델은 이전 버전과의 호환성을 위해 계속 지원 됩니다. [스키마 매핑에서](#schema-mapping)언급 된 새 모델을 사용 하는 것이 좋습니다. Data Factory authoring UI가 새 모델을 생성 하도록 전환 되었습니다.

### <a name="alternative-column-mapping-legacy-model"></a>대체 열 매핑 (레거시 모델)

복사 작업-> `translator`  ->  `columnMappings` 를 지정 하 여 테이블 형식 데이터를 매핑할 수 있습니다. 이 경우 입력 및 출력 데이터 집합 모두에 "structure" 섹션이 필요 합니다. 열 매핑은 **원본 데이터 세트 "structure"의 전체 열 또는 하위 집합을 싱크 데이터 세트 "structure"** 의 모든 열에 매핑하는 것을 지원합니다. 다음은 예외가 발생하는 오류 조건입니다.

- 원본 데이터 저장소 쿼리 결과에 입력 데이터 세트 "structure" 섹션에서 지정한 열 이름이 없습니다.
- 싱크 데이터 저장소(미리 정의된 스키마가 있는 경우)에 출력 데이터 세트 "structure" 섹션에서 지정한 열 이름이 없습니다.
- 매핑에서 지정한 것보다 싱크 데이터 세트의 "structure"에 열이 더 많거나 적습니다.
- 중복 매핑

다음 예의 입력 데이터 집합에는 구조가 있으며이는 온-프레미스 Oracle 데이터베이스의 테이블을 가리킵니다.

```json
{
    "name": "OracleDataset",
    "properties": {
        "structure":
         [
            { "name": "UserId"},
            { "name": "Name"},
            { "name": "Group"}
         ],
        "type": "OracleTable",
        "linkedServiceName": {
            "referenceName": "OracleLinkedService",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "SourceTable"
        }
    }
}
```

이 샘플에서 출력 데이터 집합은 구조를 포함 하 고 Salesfoce의 테이블을 가리킵니다.

```json
{
    "name": "SalesforceDataset",
    "properties": {
        "structure":
        [
            { "name": "MyUserId"},
            { "name": "MyName" },
            { "name": "MyGroup"}
        ],
        "type": "SalesforceObject",
        "linkedServiceName": {
            "referenceName": "SalesforceLinkedService",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "SinkTable"
        }
    }
}
```

다음 JSON은 파이프라인으로 복사 작업을 정의합니다. 원본에서 열은 **변환기**열  ->  **매핑** 속성을 사용 하 여 싱크의 열에 매핑됩니다.

```json
{
    "name": "CopyActivity",
    "type": "Copy",
    "inputs": [
        {
            "referenceName": "OracleDataset",
            "type": "DatasetReference"
        }
    ],
    "outputs": [
        {
            "referenceName": "SalesforceDataset",
            "type": "DatasetReference"
        }
    ],
    "typeProperties":    {
        "source": { "type": "OracleSource" },
        "sink": { "type": "SalesforceSink" },
        "translator":
        {
            "type": "TabularTranslator",
            "columnMappings":
            {
                "UserId": "MyUserId",
                "Group": "MyGroup",
                "Name": "MyName"
            }
        }
    }
}
```

`"columnMappings": "UserId: MyUserId, Group: MyGroup, Name: MyName"` 구문을 사용하여 열 매핑을 지정하면 있는 그대로 지원됩니다.

### <a name="alternative-schema-mapping-legacy-model"></a>대체 스키마 매핑 (레거시 모델)

복사 작업->를 지정 `translator`  ->  `schemaMapping` 하 여 계층적 데이터와 테이블 형식 데이터 사이에 매핑할 수 있습니다. 예를 들어 MongoDB/REST에서 텍스트 파일로 복사 하 고 Oracle에서 Azure Cosmos DB의 MongoDB 용 API로 복사 합니다. 다음 속성은 복사 작업 `translator` 섹션에서 지원됩니다.

| 속성            | 설명                                                  | 필수 |
| :------------------ | :----------------------------------------------------------- | :------- |
| type                | 복사 작업 변환기의 type 속성은 **TabularTranslator** 로 설정 해야 합니다. | 예      |
| schemaMapping       | **소스 쪽에서 싱크 쪽으로의**매핑 관계를 나타내는 키-값 쌍의 컬렉션입니다.<br/>- **키:** 원본을 나타냅니다. **테이블 형식 원본의**경우 데이터 집합 구조에 정의 된 열 이름을 지정 합니다. **계층 구조 원본**에서 추출 및 매핑할 각 필드에 대 한 JSON 경로 식을 지정 합니다.<br>- **값:** 싱크를 나타냅니다. **테이블 형식 싱크에**대해 데이터 집합 구조에 정의 된 열 이름을 지정 합니다. **계층적 싱크에**대해 추출 및 매핑할 각 필드에 대 한 JSON 경로 식을 지정 합니다. <br>계층적 데이터의 경우 루트 개체 아래의 필드에 대해 JSON 경로는 root $로 시작 합니다. 속성으로 선택한 배열 내에 있는 필드의 경우 `collectionReference` JSON 경로는 배열 요소에서 시작 합니다. | 예      |
| collectionReference | 동일한 패턴으로 **배열 필드 내부**의 개체에서 데이터를 반복 및 추출하고 행별 개체별로 변환하려면 교차 적용하도록 해당 배열의 JSON 경로를 지정합니다. 이 속성은 계층적 데이터가 원본인 경우에만 지원됩니다. | 아니요       |

**예: MongoDB에서 Oracle로 복사 합니다.**

예를 들어 MongoDB 문서에 다음과 같은 콘텐츠가 들어 있는 경우:

```json
{
    "id": {
        "$oid": "592e07800000000000000000"
    },
    "number": "01",
    "date": "20170122",
    "orders": [
        {
            "prod": "p1",
            "price": 23
        },
        {
            "prod": "p2",
            "price": 13
        },
        {
            "prod": "p3",
            "price": 231
        }
    ],
    "city": [ { "name": "Seattle" } ]
}
```

그리고 *(order_pd 및 order_price)* 배열 내부의 데이터를 평면화하고 일반적인 루트 정보 *(number, date 및 city)* 로 크로스 조인하여 Azure SQL 테이블에 데이터를 다음 형식으로 복사하려는 경우:

| orderNumber | orderDate | order_pd | order_price | city    |
| ----------- | --------- | -------- | ----------- | ------- |
| 01          | 20170122  | P1       | 23          | Seattle |
| 01          | 20170122  | P2       | 13          | Seattle |
| 01          | 20170122  | P3       | 231         | Seattle |

다음 복사 작업 JSON 샘플처럼 스키마-매핑 규칙을 구성합니다.

```json
{
    "name": "CopyFromMongoDBToOracle",
    "type": "Copy",
    "typeProperties": {
        "source": {
            "type": "MongoDbV2Source"
        },
        "sink": {
            "type": "OracleSink"
        },
        "translator": {
            "type": "TabularTranslator",
            "schemaMapping": {
                "$.number": "orderNumber",
                "$.date": "orderDate",
                "prod": "order_pd",
                "price": "order_price",
                "$.city[0].name": "city"
            },
            "collectionReference":  "$.orders"
        }
    }
}
```

## <a name="next-steps"></a>다음 단계
다른 복사 작업 문서를 참조하세요.

- [복사 작업 개요](copy-activity-overview.md)
