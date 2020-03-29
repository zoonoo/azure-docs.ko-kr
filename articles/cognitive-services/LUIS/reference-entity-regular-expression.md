---
title: 정규식 엔터티 유형 - LUIS
titleSuffix: Azure Cognitive Services
description: 정규식은 원시 발화 텍스트에 적합합니다. 대/소문자를 무시하고 문화적 변형을 무시합니다.  정규식 일치는 토큰 수준이 아니라 문자 수준에서 맞춤법 검사 변경 후에 적용됩니다.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 09/29/2019
ms.author: diberry
ms.openlocfilehash: b9da76a80183f353a74d43e667bf6c9219eb6c05
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "74841220"
---
# <a name="regular-expression-entity"></a>정규식 엔터티

정규식 엔터티는 제공한 정규식 패턴을 기반으로 엔터티를 추출합니다.

정규식은 원시 발화 텍스트에 적합합니다. 대/소문자를 무시하고 문화적 변형을 무시합니다.  정규식 일치는 토큰 수준이 아니라 문자 수준에서 맞춤법 검사 변경 후에 적용됩니다. 많은 대괄호를 사용하는 것처럼 정규식이 너무 복잡한 경우 모델에 식을 추가할 수 없습니다. [.NET 정규식](https://docs.microsoft.com/dotnet/standard/base-types/regular-expressions) 라이브러리를 전부 사용하지는 않지만 일부를 사용합니다.

**다음과 같은 경우 이 엔터티가 적합합니다.**

* 데이터는 일관된 변형으로 일관되게 서식이 지정됩니다.
* 정규식에는 2개 이상의 중첩 수준이 필요하지 않습니다.

![정규식 엔터티](./media/luis-concept-entities/regex-entity.png)

## <a name="usage-considerations"></a>용도 고려 사항

정규식은 예상보다 더 많이 일치할 수 있습니다. 이 예제는 `one` 숫자 단어 와 같은 `two`일치 합니다. 예를 들어 다음 정규식은 다른 `one` 숫자와 함께 숫자와 일치합니다.

```javascript
(plus )?(zero|one|two|three|four|five|six|seven|eight|nine)(\s+(zero|one|two|three|four|five|six|seven|eight|nine))*
```

이 정규식 표현식은 `phone`와 같은 숫자로 끝나는 모든 단어와일치합니다. 이와 같은 문제를 해결하려면 정규식 일치 항목이 단어 경계를 고려해야 합니다. 이 예제에 대한 단어 경계를 사용하는 정규식은 다음 정규식에서 사용됩니다.

```javascript
\b(plus )?(zero|one|two|three|four|five|six|seven|eight|nine)(\s+(zero|one|two|three|four|five|six|seven|eight|nine))*\b
```

### <a name="example-json"></a>예제 JSON

을 `kb[0-9]{6}`정규식 엔터티 정의로 사용하는 경우 다음 JSON 응답은 쿼리에 대해 반환된 정규식 엔터티를 사용한 예제 발언입니다.

`When was kb123456 published?`:

#### <a name="v2-prediction-endpoint-response"></a>[V2 예측 엔드포인트 응답](#tab/V2)

```JSON
"entities": [
  {
    "entity": "kb123456",
    "type": "KB number",
    "startIndex": 9,
    "endIndex": 16
  }
]
```


#### <a name="v3-prediction-endpoint-response"></a>[V3 예측 엔드포인트 응답](#tab/V3)


쿼리 문자열에 설정된 `verbose=false` 경우 JSON입니다.

```json
"entities": {
    "KB number": [
        "kb123456"
    ]
}
```

쿼리 문자열에 설정된 `verbose=true` 경우 JSON입니다.

```json
"entities": {
    "KB number": [
        "kb123456"
    ],
    "$instance": {
        "KB number": [
            {
                "type": "KB number",
                "text": "kb123456",
                "startIndex": 9,
                "length": 8,
                "modelTypeId": 8,
                "modelType": "Regex Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            }
        ]
    }
}
```

* * *

## <a name="next-steps"></a>다음 단계

이 [자습서에서는](tutorial-regex-entity.md) **정규식** 엔터티를 사용하여 발언에서 일관되게 형식이 지정된 데이터를 추출하는 앱을 만듭니다.