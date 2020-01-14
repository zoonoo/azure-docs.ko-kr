---
title: DatetimeV2 미리 작성 한 엔터티-LUIS
titleSuffix: Azure Cognitive Services
description: 이 아티클에는 LUIS(Language Understanding)의 datetimeV2 미리 작성된 엔터티가 포함됩니다.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 01/07/2020
ms.author: diberry
ms.openlocfilehash: 8c29ebd675bb6af66203c13824dacbe9ea2421a2
ms.sourcegitcommit: c32050b936e0ac9db136b05d4d696e92fefdf068
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/08/2020
ms.locfileid: "75732798"
---
# <a name="datetimev2-prebuilt-entity-for-a-luis-app"></a>LUIS 앱용 미리 빌드된 DatetimeV2 엔터티

**datetimeV2** 미리 작성된 엔터티는 날짜 및 시간 값을 추출합니다. 이러한 값은 사용할 클라이언트 프로그램에 대해 표준화된 형식으로 결정됩니다. 발언에 완전하지 않은 날짜 또는 시간이 포함되는 경우 LUIS에는 엔드포인트 응답의 _이전 및 이후 값이 모두_ 포함됩니다. 이 엔터티를 이미 학습했기 때문에 datetimeV2를 애플리케이션 의도에 포함하는 예제 발언을 추가할 필요가 없습니다.

## <a name="types-of-datetimev2"></a>datetimeV2 형식
DatetimeV2는 [인식자-텍스트](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/English/English-DateTime.yaml) GitHub 리포지토리에서 관리 됩니다.

## <a name="example-json"></a>예제 JSON

다음 utterance 및 해당 부분 JSON 응답은 아래에 나와 있습니다.

`8am on may 2nd 2019`

#### <a name="v3-responsetab1-1"></a>[V3 응답](#tab/1-1)

```json
"entities": {
    "datetimeV2": [
        {
            "type": "datetime",
            "values": [
                {
                    "timex": "2019-05-02T08",
                    "resolution": [
                        {
                            "value": "2019-05-02 08:00:00"
                        }
                    ]
                }
            ]
        }
    ]
}
```

#### <a name="v3-verbose-responsetab1-2"></a>[V3 자세한 정보 표시 응답](#tab/1-2)

```json

"entities": {
    "datetimeV2": [
        {
            "type": "datetime",
            "values": [
                {
                    "timex": "2019-05-02T08",
                    "resolution": [
                        {
                            "value": "2019-05-02 08:00:00"
                        }
                    ]
                }
            ]
        }
    ],
    "$instance": {
        "datetimeV2": [
            {
                "type": "builtin.datetimeV2.datetime",
                "text": "8am on may 2nd 2019",
                "startIndex": 0,
                "length": 19,
                "modelTypeId": 2,
                "modelType": "Prebuilt Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            }
        ]
    }
}
```

#### <a name="v2-responsetab1-3"></a>[V2 응답](#tab/1-3)

```json
"entities": [
  {
    "entity": "8am on may 2nd 2019",
    "type": "builtin.datetimeV2.datetime",
    "startIndex": 0,
    "endIndex": 18,
    "resolution": {
      "values": [
        {
          "timex": "2019-05-02T08",
          "type": "datetime",
          "value": "2019-05-02 08:00:00"
        }
      ]
    }
  }
]
 ```

|속성 이름 |속성 형식 및 설명|
|---|---|
|엔터티|**문자열** - 날짜, 시간, 날짜 범위 또는 시간 범위라는 형식이 있는 발언에서 추출된 텍스트입니다.|
|type|**문자열** - [datetimeV2의 하위 식형](#subtypes-of-datetimev2) 중 하나입니다.
|startIndex|**int** - 엔터티가 시작하는 발언의 인덱스입니다.|
|endIndex|**int** - 엔터티가 끝나는 발언의 인덱스입니다.|
|해결 방법|하나, 둘 또는 네 가지 [해결 방법](#values-of-resolution)이 있는 `values` 배열이 포함됩니다.|
|end|`value`와 동일한 형식인 시간 또는 날짜 범위의 끝 값입니다. `type`이 `daterange`, `timerange` 또는 `datetimerange`인 경우에만 사용합니다.|

* * *

## <a name="subtypes-of-datetimev2"></a>datetimeV2 하위 형식

**datetimeV2** 미리 작성된 엔터티에는 다음과 같은 하위 종류가 있으며 각각의 예제는 다음에 나오는 표에 제공됩니다.
* `date`
* `time`
* `daterange`
* `timerange`
* `datetimerange`


## <a name="values-of-resolution"></a>해결 방법의 값
* 발언의 날짜 또는 시간이 완벽하게 지정되고 모호하지 않은 경우 배열에는 하나의 요소가 포함됩니다.
* datetimeV2 값이 모호한 경우 배열에는 두 개의 요소가 포함됩니다. 특정 연도, 시간 또는 시간 범위가 부족하면 모호성에 포함됩니다. 예제는 [모호한 날짜](#ambiguous-dates)를 참조하세요. 오전 또는 오후에 대한 시간이 모호한 경우 모든 값이 포함됩니다.
* 발언에 모호성이 있는 두 개의 요소가 있으면 배열에는 4개의 요소가 포함됩니다. 이 모호성에는 다음이 있는 요소가 포함됩니다.
  * 연도 측면에서 모호한 날짜 또는 날짜 범위
  * 오전 또는 오후 측면에서 모호한 시간 또는 시간 범위 예: 4월 3일 3시

`values` 배열의 각 요소에는 다음 필드가 있을 수 있습니다.

|속성 이름|속성 설명|
|--|--|
|timex|[ISO 8601 표준](https://en.wikipedia.org/wiki/ISO_8601) 뒤에 오는 TIMEX 형식으로 표현된 시간, 날짜 또는 날짜 범위와 TimeML 언어를 사용하는 주석에 대한 TIMEX3 특성입니다. 이 주석은 [TIMEX 지침](http://www.timeml.org/tempeval2/tempeval2-trial/guidelines/timex3guidelines-072009.pdf)에 설명됩니다.|
|mod|`before``after`와 같은 값을 사용 하는 방법을 설명 하는 데 사용 되는 용어입니다.|
|type|하위 형식으로, `datetime`, `date`, `time`, `daterange`, `timerange`, `datetimerange`, `duration`, `set`항목 중 하나일 수 있습니다.|
|값|**(선택 사항)** Yyyy-MM-dd (date), HH: mm: ss (time) yyyy-mm-dd HH: MM: ss (datetime) 형식의 datetime 개체입니다. `type`이 `duration`인 경우 값은 초(기간)입니다. <br/> `type`이 `datetime` 또는 `date`, `time` 또는 기간인 경우에만 사용됩니다.|

## <a name="valid-date-values"></a>유효한 날짜 값

**datetimeV2**는 다음 범위 사이의 날짜를 지원합니다.

| 최소값 | 최대 |
|----------|-------------|
| 1900년 1월 1일   | 2099년 12월 31일 |

## <a name="ambiguous-dates"></a>모호한 날짜

날짜가 과거 또는 미래인 경우 LUIS에서는 모든 값을 제공합니다. 예를 들어 연도 없이 월 및 날짜를 포함하는 발언이 있습니다.

예를 들어 다음과 같은 utterance이 있다고 가정 합니다.

`May 2nd`

* 오늘 날짜가 2017년 5월 3일이면 LUIS에서는 "2017-05-02" 및 "2018-05-02" 모두를 값으로 제공합니다.
* 오늘 날짜가 2017년 5월 1일이면 LUIS에서는 "2016-05-02" 및 "2017-05-02" 모두를 값으로 제공합니다.

다음 예제에서는 "5월 2일" 엔터티의 해결 방법을 보여줍니다. 이 해결 방법에서는 오늘 날짜가 2017년 5월 2일부터 2018년 5월 1일 사이의 날짜라고 가정합니다.
`timex` 필드의 `X`가 있는 필드는 발언에서 명시적으로 지정되지 않은 날짜의 일부입니다.

## <a name="date-resolution-example"></a>날짜 확인 예


다음 utterance 및 해당 부분 JSON 응답은 아래에 나와 있습니다.

`May 2nd`

#### <a name="v3-responsetab2-1"></a>[V3 응답](#tab/2-1)

```json
"entities": {
    "datetimeV2": [
        {
            "type": "date",
            "values": [
                {
                    "timex": "XXXX-05-02",
                    "resolution": [
                        {
                            "value": "2019-05-02"
                        },
                        {
                            "value": "2020-05-02"
                        }
                    ]
                }
            ]
        }
    ]
}
```

#### <a name="v3-verbose-responsetab2-2"></a>[V3 자세한 정보 표시 응답](#tab/2-2)

```json
"entities": {
    "datetimeV2": [
        {
            "type": "date",
            "values": [
                {
                    "timex": "XXXX-05-02",
                    "resolution": [
                        {
                            "value": "2019-05-02"
                        },
                        {
                            "value": "2020-05-02"
                        }
                    ]
                }
            ]
        }
    ],
    "$instance": {
        "datetimeV2": [
            {
                "type": "builtin.datetimeV2.date",
                "text": "May 2nd",
                "startIndex": 0,
                "length": 7,
                "modelTypeId": 2,
                "modelType": "Prebuilt Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            }
        ]
    }
}
```

#### <a name="v2-responsetab2-3"></a>[V2 응답](#tab/2-3)

```json
  "entities": [
    {
      "entity": "may 2nd",
      "type": "builtin.datetimeV2.date",
      "startIndex": 0,
      "endIndex": 6,
      "resolution": {
        "values": [
          {
            "timex": "XXXX-05-02",
            "type": "date",
            "value": "2019-05-02"
          },
          {
            "timex": "XXXX-05-02",
            "type": "date",
            "value": "2020-05-02"
          }
        ]
      }
    }
  ]
```
* * *

## <a name="date-range-resolution-examples-for-numeric-date"></a>숫자 형식 날짜에 대한 날짜 범위 해결 방법 예제

`datetimeV2` 엔터티는 날짜 및 시간 범위를 추출합니다. `start` 및 `end` 필드는 범위의 시작과 끝을 지정합니다. Utterance `May 2nd to May 5th`LUIS는 현재 연도와 다음 연도 모두에 대 한 **daterange** 값을 제공 합니다. `timex` 필드에서 `XXXX` 값은 연도의 모호성을 나타냅니다. `P3D`는 기간이 3일임을 나타냅니다.

다음 utterance 및 해당 부분 JSON 응답은 아래에 나와 있습니다.

`May 2nd to May 5th`

#### <a name="v3-responsetab3-1"></a>[V3 응답](#tab/3-1)

```json

"entities": {
    "datetimeV2": [
        {
            "type": "daterange",
            "values": [
                {
                    "timex": "(XXXX-05-02,XXXX-05-05,P3D)",
                    "resolution": [
                        {
                            "start": "2019-05-02",
                            "end": "2019-05-05"
                        },
                        {
                            "start": "2020-05-02",
                            "end": "2020-05-05"
                        }
                    ]
                }
            ]
        }
    ]
}
```


#### <a name="v3-verbose-responsetab3-2"></a>[V3 자세한 정보 표시 응답](#tab/3-2)

```json

"entities": {
    "datetimeV2": [
        {
            "type": "daterange",
            "values": [
                {
                    "timex": "(XXXX-05-02,XXXX-05-05,P3D)",
                    "resolution": [
                        {
                            "start": "2019-05-02",
                            "end": "2019-05-05"
                        },
                        {
                            "start": "2020-05-02",
                            "end": "2020-05-05"
                        }
                    ]
                }
            ]
        }
    ],
    "$instance": {
        "datetimeV2": [
            {
                "type": "builtin.datetimeV2.daterange",
                "text": "May 2nd to May 5th",
                "startIndex": 0,
                "length": 18,
                "modelTypeId": 2,
                "modelType": "Prebuilt Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            }
        ]
    }
}
```

#### <a name="v2-responsetab3-3"></a>[V2 응답](#tab/3-3)

```json
"entities": [
    {
      "entity": "may 2nd to may 5th",
      "type": "builtin.datetimeV2.daterange",
      "startIndex": 0,
      "endIndex": 17,
      "resolution": {
        "values": [
          {
            "timex": "(XXXX-05-02,XXXX-05-05,P3D)",
            "type": "daterange",
            "start": "2019-05-02",
            "end": "2019-05-05"
          }
        ]
      }
    }
  ]
```
* * *

## <a name="date-range-resolution-examples-for-day-of-week"></a>요일에 대한 날짜 범위 해결 방법 예제

다음 예에서는 LUIS에서 **datetimeV2** 을 사용 하 여 utterance `Tuesday to Thursday`를 확인 하는 방법을 보여 줍니다. 이 예제에서 현재 날짜는 6월 19일입니다. LUIS에는 현재 날짜의 전후인 모든 날짜 범위에 대한 **daterange** 값이 포함됩니다.

다음 utterance 및 해당 부분 JSON 응답은 아래에 나와 있습니다.

`Tuesday to Thursday`

#### <a name="v3-responsetab4-1"></a>[V3 응답](#tab/4-1)

```json
"entities": {
    "datetimeV2": [
        {
            "type": "daterange",
            "values": [
                {
                    "timex": "(XXXX-WXX-2,XXXX-WXX-4,P2D)",
                    "resolution": [
                        {
                            "start": "2019-10-08",
                            "end": "2019-10-10"
                        },
                        {
                            "start": "2019-10-15",
                            "end": "2019-10-17"
                        }
                    ]
                }
            ]
        }
    ]
}
```

#### <a name="v3-verbose-responsetab4-2"></a>[V3 자세한 정보 표시 응답](#tab/4-2)

```json
"entities": {
    "datetimeV2": [
        {
            "type": "daterange",
            "values": [
                {
                    "timex": "(XXXX-WXX-2,XXXX-WXX-4,P2D)",
                    "resolution": [
                        {
                            "start": "2019-10-08",
                            "end": "2019-10-10"
                        },
                        {
                            "start": "2019-10-15",
                            "end": "2019-10-17"
                        }
                    ]
                }
            ]
        }
    ],
    "$instance": {
        "datetimeV2": [
            {
                "type": "builtin.datetimeV2.daterange",
                "text": "Tuesday to Thursday",
                "startIndex": 0,
                "length": 19,
                "modelTypeId": 2,
                "modelType": "Prebuilt Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            }
        ]
    }
}
```

#### <a name="v2-responsetab4-3"></a>[V2 응답](#tab/4-3)

```json
  "entities": [
    {
      "entity": "tuesday to thursday",
      "type": "builtin.datetimeV2.daterange",
      "startIndex": 0,
      "endIndex": 19,
      "resolution": {
        "values": [
          {
            "timex": "(XXXX-WXX-2,XXXX-WXX-4,P2D)",
            "type": "daterange",
            "start": "2019-04-30",
            "end": "2019-05-02"
          }
        ]
      }
    }
  ]
```
* * *

## <a name="ambiguous-time"></a>모호한 시간
시간 또는 시간 범위가 모호한 경우 값 배열에는 두 개의 시간 요소가 포함됩니다. 시간이 모호한 경우 값에는 오전 및 오후 시간이 모두 포함됩니다.

## <a name="time-range-resolution-example"></a>시간 범위 해결 방법 예제

API V3에서 DatetimeV2 JSON 응답이 변경 되었습니다. 다음 예제에서는 LUIS가 **datetimeV2**를 사용하여 시간 범위가 있는 발언을 확인하는 방법을 보여줍니다.

API v 2의 변경 내용:
* `datetimeV2.timex.type` 속성은 부모 수준에서 반환 되기 때문에 더 이상 반환 되지 않습니다 `datetimev2.type`.
* `datetimeV2.value` 속성이 `datetimeV2.timex`로 바뀌었습니다.

다음 utterance 및 해당 부분 JSON 응답은 아래에 나와 있습니다.

`from 6pm to 7pm`

#### <a name="v3-responsetab5-1"></a>[V3 응답](#tab/5-1)

다음 JSON은 `false`로 설정 된 `verbose` 매개 변수를 사용 합니다.

```JSON

"entities": {
    "datetimeV2": [
        {
            "type": "timerange",
            "values": [
                {
                    "timex": "(T18,T19,PT1H)",
                    "resolution": [
                        {
                            "start": "18:00:00",
                            "end": "19:00:00"
                        }
                    ]
                }
            ]
        }
    ]
}
```
#### <a name="v3-verbose-responsetab5-2"></a>[V3 자세한 정보 표시 응답](#tab/5-2)

다음 JSON은 `true`로 설정 된 `verbose` 매개 변수를 사용 합니다.

```json

"entities": {
    "datetimeV2": [
        {
            "type": "timerange",
            "values": [
                {
                    "timex": "(T18,T19,PT1H)",
                    "resolution": [
                        {
                            "start": "18:00:00",
                            "end": "19:00:00"
                        }
                    ]
                }
            ]
        }
    ],
    "$instance": {
        "datetimeV2": [
            {
                "type": "builtin.datetimeV2.timerange",
                "text": "from 6pm to 7pm",
                "startIndex": 0,
                "length": 15,
                "modelTypeId": 2,
                "modelType": "Prebuilt Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            }
        ]
    }
}
```
#### <a name="v2-responsetab5-3"></a>[V2 응답](#tab/5-3)

```json
  "entities": [
    {
      "entity": "6pm to 7pm",
      "type": "builtin.datetimeV2.timerange",
      "startIndex": 0,
      "endIndex": 9,
      "resolution": {
        "values": [
          {
            "timex": "(T18,T19,PT1H)",
            "type": "timerange",
            "start": "18:00:00",
            "end": "19:00:00"
          }
        ]
      }
    }
  ]
```

* * *

## <a name="time-resolution-example"></a>시간 확인 예

다음 utterance 및 해당 부분 JSON 응답은 아래에 나와 있습니다.

`8am`

#### <a name="v3-responsetab6-1"></a>[V3 응답](#tab/6-1)

```json
"entities": {
    "datetimeV2": [
        {
            "type": "time",
            "values": [
                {
                    "timex": "T08",
                    "resolution": [
                        {
                            "value": "08:00:00"
                        }
                    ]
                }
            ]
        }
    ]
}
```
#### <a name="v3-verbose-responsetab6-2"></a>[V3 자세한 정보 표시 응답](#tab/6-2)

```json
"entities": {
    "datetimeV2": [
        {
            "type": "time",
            "values": [
                {
                    "timex": "T08",
                    "resolution": [
                        {
                            "value": "08:00:00"
                        }
                    ]
                }
            ]
        }
    ],
    "$instance": {
        "datetimeV2": [
            {
                "type": "builtin.datetimeV2.time",
                "text": "8am",
                "startIndex": 0,
                "length": 3,
                "modelTypeId": 2,
                "modelType": "Prebuilt Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            }
        ]
    }
}
```
#### <a name="v2-responsetab6-3"></a>[V2 응답](#tab/6-3)

```json
"entities": [
  {
    "entity": "8am",
    "type": "builtin.datetimeV2.time",
    "startIndex": 0,
    "endIndex": 2,
    "resolution": {
      "values": [
        {
          "timex": "T08",
          "type": "time",
          "value": "08:00:00"
        }
      ]
    }
  }
]
```

* * *

## <a name="deprecated-prebuilt-datetime"></a>미리 작성된 날짜/시간 사용되지 않음

`datetime` 미리 빌드된 엔터티는 더 이상 사용되지 않으며 **datetimeV2**로 바뀌었습니다.

LUIS 앱에서 `datetime`을 `datetimeV2`로 바꾸려면 다음 단계를 완료합니다.

1. LUIS 웹 인터페이스의 **엔터티** 창을 엽니다.
2. **datetime** 미리 작성된 엔터티를 삭제합니다.
3. **미리 빌드된 엔터티 추가**를 클릭합니다.
4. **datetimeV2**를 선택하고 **저장**을 클릭합니다.

## <a name="next-steps"></a>다음 단계

[V3 예측 엔드포인트](luis-migration-api-v3.md)에 대해 자세히 알아봅니다.

[dimension](luis-reference-prebuilt-dimension.md), [email](luis-reference-prebuilt-email.md) 엔터티 및 [number](luis-reference-prebuilt-number.md)에 대해 알아봅니다.

