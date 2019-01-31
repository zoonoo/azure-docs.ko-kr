---
title: 미리 빌드된 DatetimeV2 엔터티
titleSuffix: Azure
description: 이 아티클에는 LUIS(Language Understanding)의 datetimeV2 미리 작성된 엔터티가 포함됩니다.
services: cognitive-services
author: diberry
manager: cgronlun
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 11/26/2018
ms.author: diberry
ms.openlocfilehash: c576168a89e81f5a06067ec0d3ba86e6f5eaeeca
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/29/2019
ms.locfileid: "55227978"
---
# <a name="datetimev2-prebuilt-entity-for-a-luis-app"></a>LUIS 앱용 미리 빌드된 DatetimeV2 엔터티

**datetimeV2** 미리 작성된 엔터티는 날짜 및 시간 값을 추출합니다. 이러한 값은 사용할 클라이언트 프로그램에 대해 표준화된 형식으로 결정됩니다. 발언에 완전하지 않은 날짜 또는 시간이 포함되는 경우 LUIS에는 엔드포인트 응답의 _이전 및 이후 값이 모두_ 포함됩니다. 이 엔터티를 이미 학습했기 때문에 datetimeV2를 애플리케이션 의도에 포함하는 예제 발언을 추가할 필요가 없습니다. 

## <a name="types-of-datetimev2"></a>datetimeV2 형식
DatetimeV2는 [Recognizers-text](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/English/English-DateTime.yaml) GitHub 리포지토리에서 관리됩니다.

## <a name="example-json"></a>예제 JSON 
다음 예제 JSON 응답에는 `datetime`이라는 하위 형식이 있는 `datetimeV2` 엔터티가 포함됩니다. 다른 종류의 datetimeV2 엔터티의 예제는 [datetimeV2 하위 형식](#subtypes-of-datetimev2)</a>을 참조하세요.

```json
"entities": [
  {
    "entity": "8am on may 2nd 2017",
    "type": "builtin.datetimeV2.datetime",
    "startIndex": 15,
    "endIndex": 30,
    "resolution": {
      "values": [
        {
          "timex": "2017-05-02T08",
          "type": "datetime",
          "value": "2017-05-02 08:00:00"
        }
      ]
    }
  }
]
  ```

## <a name="json-property-descriptions"></a>JSON 속성 설명

|속성 이름 |속성 형식 및 설명|
|---|---|
|엔터티|**문자열** - 날짜, 시간, 날짜 범위 또는 시간 범위라는 형식이 있는 발언에서 추출된 텍스트입니다.|
|형식|**문자열** - [datetimeV2의 하위 식형](#subtypes-of-datetimev2) 중 하나입니다.
|startIndex|**int** - 엔터티가 시작하는 발언의 인덱스입니다.|
|endIndex|**int** - 엔터티가 끝나는 발언의 인덱스입니다.|
|해결 방법|하나, 둘 또는 네 가지 [해결 방법](#values-of-resolution)이 있는 `values` 배열이 포함됩니다.|
|end|`value`와 동일한 형식인 시간 또는 날짜 범위의 끝 값입니다. `type`이 `daterange`, `timerange` 또는 `datetimerange`인 경우에만 사용합니다.|

## <a name="subtypes-of-datetimev2"></a>datetimeV2 하위 형식

**datetimeV2** 미리 작성된 엔터티에는 다음과 같은 하위 종류가 있으며 각각의 예제는 다음에 나오는 표에 제공됩니다.
* `date`
* `time`
* `daterange`
* `timerange`
* `datetimerange`
* `duration`
* `set`

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
|형식|하위 형식은 날짜/시간, 날짜, 시간, 날짜 범위, 시간 범위, 날짜/시간 범위, 기간, 집합과 같은 항목 중 하나가 될 수 있습니다.|
|값|**선택** yyyy:MM:dd(날짜), HH:mm:ss(시간) yyyy:MM:dd HH:mm:ss(날짜/시간) 형식의 날짜/시간 개체입니다. `type`이 `duration`인 경우 값은 초(기간)입니다. <br/> `type`이 `datetime` 또는 `date`, `time` 또는 기간인 경우에만 사용됩니다.|

## <a name="valid-date-values"></a>유효한 날짜 값

**datetimeV2**는 다음 범위 사이의 날짜를 지원합니다.

| Min | max |
|----------|-------------|
| 1900년 1월 1일   | 2099년 12월 31일 |

## <a name="ambiguous-dates"></a>모호한 날짜

날짜가 과거 또는 미래인 경우 LUIS에서는 모든 값을 제공합니다. 예를 들어 연도 없이 월 및 날짜를 포함하는 발언이 있습니다.  

예를 들어 발언 "5월 2일"을 가정합니다.
* 오늘 날짜가 2017년 5월 3일이면 LUIS에서는 "2017-05-02" 및 "2018-05-02" 모두를 값으로 제공합니다. 
* 오늘 날짜가 2017년 5월 1일이면 LUIS에서는 "2016-05-02" 및 "2017-05-02" 모두를 값으로 제공합니다.

다음 예제에서는 "5월 2일" 엔터티의 해결 방법을 보여줍니다. 이 해결 방법에서는 오늘 날짜가 2017년 5월 2일부터 2018년 5월 1일 사이의 날짜라고 가정합니다.
`timex` 필드의 `X`가 있는 필드는 발언에서 명시적으로 지정되지 않은 날짜의 일부입니다.

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
            "value": "2017-05-02"
          },
          {
            "timex": "XXXX-05-02",
            "type": "date",
            "value": "2018-05-02"
          }
        ]
      }
    }
  ]
```

## <a name="date-range-resolution-examples-for-numeric-date"></a>숫자 형식 날짜에 대한 날짜 범위 해결 방법 예제

`datetimeV2` 엔터티는 날짜 및 시간 범위를 추출합니다. `start` 및 `end` 필드는 범위의 시작과 끝을 지정합니다. "5월 2년부터 5월 5일" 발언의 경우 LUIS에서는 올해 및 내년 모두에 **날짜 범위** 값을 제공합니다. `timex` 필드에서 `XXXX` 값은 연도의 모호성을 나타냅니다. `P3D`는 기간이 3일임을 나타냅니다.

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
            "start": "2017-05-02",
            "end": "2017-05-05"
          },
          {
            "timex": "(XXXX-05-02,XXXX-05-05,P3D)",
            "type": "daterange",
            "start": "2018-05-02",
            "end": "2018-05-05"
          }
        ]
      }
    }
  ]
```

## <a name="date-range-resolution-examples-for-day-of-week"></a>요일에 대한 날짜 범위 해결 방법 예제

다음 예제에서는 LUIS가 **datetimeV2**를 사용하여 "화요일부터 목요일" 발언을 확인하는 방법을 보여줍니다. 이 예제에서 현재 날짜는 6월 19일입니다. LUIS에는 현재 날짜의 전후인 모든 날짜 범위에 대한 **daterange** 값이 포함됩니다.

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
            "start": "2017-06-13",
            "end": "2017-06-15"
          },
          {
            "timex": "(XXXX-WXX-2,XXXX-WXX-4,P2D)",
            "type": "daterange",
            "start": "2017-06-20",
            "end": "2017-06-22"
          }
        ]
      }
    }
  ]
```
## <a name="ambiguous-time"></a>모호한 시간
시간 또는 시간 범위가 모호한 경우 값 배열에는 두 개의 시간 요소가 포함됩니다. 시간이 모호한 경우 값에는 오전 및 오후 시간이 모두 포함됩니다.

## <a name="time-range-resolution-example"></a>시간 범위 해결 방법 예제

다음 예제에서는 LUIS가 **datetimeV2**를 사용하여 시간 범위가 있는 발언을 확인하는 방법을 보여줍니다.

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

## <a name="deprecated-prebuilt-datetime"></a>미리 작성된 날짜/시간 사용되지 않음

`datetime` 미리 빌드된 엔터티는 더 이상 사용되지 않으며 **datetimeV2**로 바뀌었습니다. 

LUIS 앱에서 `datetime`을 `datetimeV2`로 바꾸려면 다음 단계를 완료합니다.

1. LUIS 웹 인터페이스의 **엔터티** 창을 엽니다. 
2. **datetime** 미리 작성된 엔터티를 삭제합니다.
3. **미리 빌드된 엔터티 추가**를 클릭합니다.
4. **datetimeV2**를 선택하고 **저장**을 클릭합니다.

## <a name="next-steps"></a>다음 단계

[dimension](luis-reference-prebuilt-dimension.md), [email](luis-reference-prebuilt-email.md) 엔터티 및 [number](luis-reference-prebuilt-number.md)에 대해 알아봅니다. 

