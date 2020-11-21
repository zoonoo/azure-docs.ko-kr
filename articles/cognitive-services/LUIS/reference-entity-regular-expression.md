---
title: 정규식 엔터티 형식-LUIS
description: 정규식은 원시 발화 텍스트에 적합합니다. 대/소문자를 무시하고 문화적 변형을 무시합니다.  정규식 일치는 토큰 수준이 아니라 문자 수준에서 맞춤법 검사 변경 후에 적용됩니다.
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 04/14/2020
ms.openlocfilehash: 18e44ec43e1169aa054e6e5b4591ccd8611a7f4d
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/21/2020
ms.locfileid: "95025228"
---
# <a name="regular-expression-entity"></a>정규식 엔터티

정규식 엔터티는 사용자가 제공하는 정규식 패턴을 기반으로 하여 엔터티를 추출합니다.

정규식은 원시 발화 텍스트에 적합합니다. 대/소문자를 무시하고 문화적 변형을 무시합니다.  정규식 일치는 토큰 수준이 아니라 문자 수준에서 맞춤법 검사 변경 후에 적용됩니다. 많은 대괄호를 사용하는 것처럼 정규식이 너무 복잡한 경우 모델에 식을 추가할 수 없습니다. 모든 [.Net Regex](/dotnet/standard/base-types/regular-expressions) 라이브러리를 사용 하지만 일부는 사용 하지 않습니다.

**다음과 같은 경우 이 엔터티가 적합합니다.**

* 데이터는 일관된 변형으로 일관되게 서식이 지정됩니다.
* 정규식에는 2개 이상의 중첩 수준이 필요하지 않습니다.

![정규식 엔터티](./media/luis-concept-entities/regex-entity.png)

## <a name="usage-considerations"></a>용도 고려 사항

정규식이 일치 하는 것 보다 더 일치 하는 것으로 간주 될 수 있습니다. 이에 대 한 예는 및와 같은 숫자 단어 일치입니다 `one` `two` . 예를 들면 다음 regex를 사용 하 여 숫자 `one` 와 다른 숫자를 일치 시킵니다.

```javascript
(plus )?(zero|one|two|three|four|five|six|seven|eight|nine)(\s+(zero|one|two|three|four|five|six|seven|eight|nine))*
```

이 regex 식도와 같이 이러한 숫자로 끝나는 단어를 찾습니다 `phone` . 이와 같은 문제를 해결 하려면 regex와 일치 하는 단어 경계를 사용 해야 합니다. 이 예제에서 단어 경계를 사용 하는 regex는 다음 regex에 사용 됩니다.

```javascript
\b(plus )?(zero|one|two|three|four|five|six|seven|eight|nine)(\s+(zero|one|two|three|four|five|six|seven|eight|nine))*\b
```

### <a name="example-json"></a>예제 JSON

`kb[0-9]{6}`정규식 엔터티 정의로를 사용 하는 경우 다음 JSON 응답은 쿼리에 대해 반환 된 정규식 엔터티를 사용 하는 utterance 예입니다.

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


이는 `verbose=false` 쿼리 문자열에가 설정 된 경우 JSON입니다.

```json
"entities": {
    "KB number": [
        "kb123456"
    ]
}
```

이는 `verbose=true` 쿼리 문자열에가 설정 된 경우 JSON입니다.

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

엔터티에 대 한 자세한 정보:

* [개념](luis-concept-entity-types.md)
* [만드는 방법](luis-how-to-add-entities.md)