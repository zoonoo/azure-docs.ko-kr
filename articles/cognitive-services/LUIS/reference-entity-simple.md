---
title: 단순 엔터티 형식-LUIS
titleSuffix: Azure Cognitive Services
description: 간단한 엔터티는 컴퓨터에서 학습 한 컨텍스트의 단일 개념을 설명 합니다. 간단한 엔터티를 사용 하 여 결과를 개선할 때 구 목록을 추가 합니다.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 09/29/2019
ms.author: diberry
ms.openlocfilehash: 8b92aa6057c81ec9442372c5b85918cb92196d61
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/06/2019
ms.locfileid: "74894752"
---
# <a name="simple-entity"></a>단순 엔터티

단순 엔터티는 단일 개념을 설명하고 기계 학습 컨텍스트에서 학습되는 일반 엔터티입니다. 단준 엔터티 이름이 일반적으로 회사 이름, 제품 이름 또는 이름의 기타 범주와 같은 이름이기 때문에 사용된 이름의 신호를 향상시키기 위해 간단한 엔터티를 사용하는 경우 [구 목록](luis-concept-feature.md)을 추가합니다.

**엔터티는 다음과 같은 경우에 적합 합니다.**

* 데이터는 일관되게 형식이 지정되지 않고 동일한 작업을 나타냅니다.

![단순 엔터티](./media/luis-concept-entities/simple-entity.png)

## <a name="example-json"></a>예제 JSON

`Bob Jones wants 3 meatball pho`

이전 발화에서 `Bob Jones`는 단순 `Customer` 엔터티로 레이블이 지정됩니다.

엔드포인트에서 반환된 데이터에는 엔터티 이름, 발화에서 검색된 텍스트, 검색된 텍스트의 위치 및 점수가 포함됩니다.

#### <a name="v2-prediction-endpoint-responsetabv2"></a>[V2 예측 엔드포인트 응답](#tab/V2)

```JSON
"entities": [
  {
  "entity": "bob jones",
  "type": "Customer",
  "startIndex": 0,
  "endIndex": 8,
  "score": 0.473899543
  }
]
```

#### <a name="v3-prediction-endpoint-responsetabv3"></a>[V3 예측 엔드포인트 응답](#tab/V3)

쿼리 문자열에 `verbose=false` 설정 된 경우이 JSON은 다음과 같습니다.

```json
"entities": {
    "Customer": [
        "Bob Jones"
    ]
}```

This is the JSON if `verbose=true` is set in the query string:

```json
"entities": {
    "Customer": [
        "Bob Jones"
    ],
    "$instance": {
        "Customer": [
            {
                "type": "Customer",
                "text": "Bob Jones",
                "startIndex": 0,
                "length": 9,
                "score": 0.9339134,
                "modelTypeId": 1,
                "modelType": "Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            }
        ]
    }
}
```

* * *

|데이터 개체|엔터티 이름|Value|
|--|--|--|
|단순 엔터티|`Customer`|`bob jones`|

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [패턴 구문 배우기](reference-pattern-syntax.md)