---
title: 정규식 엔터티 형식-LUIS
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
ms.openlocfilehash: ae46df875d588186cd083134820f349158d7e307
ms.sourcegitcommit: 8bae7afb0011a98e82cbd76c50bc9f08be9ebe06
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/01/2019
ms.locfileid: "71695211"
---
# <a name="regular-expression-entity"></a>정규식 엔터티 

정규식 엔터티는 사용자가 제공 하는 정규식 패턴을 기반으로 엔터티를 추출 합니다.

정규식은 원시 발화 텍스트에 적합합니다. 대/소문자를 무시하고 문화적 변형을 무시합니다.  정규식 일치는 토큰 수준이 아니라 문자 수준에서 맞춤법 검사 변경 후에 적용됩니다. 많은 대괄호를 사용하는 것처럼 정규식이 너무 복잡한 경우 모델에 식을 추가할 수 없습니다. 모든 [.Net Regex](https://docs.microsoft.com/dotnet/standard/base-types/regular-expressions) 라이브러리를 사용 하지만 일부는 사용 하지 않습니다. 

**엔터티는 다음과 같은 경우에 적합 합니다.**

* 데이터는 일관된 변형으로 일관되게 서식이 지정됩니다.
* 정규식에는 2개 이상의 중첩 수준이 필요하지 않습니다. 

![정규식 엔터티](./media/luis-concept-entities/regex-entity.png)

## <a name="usage-considerations"></a>사용 고려 사항

정규식이 일치 하는 것 보다 더 일치 하는 것으로 간주 될 수 있습니다. 이에 대 한 예는 `one` 및 `two`과 같은 숫자 단어 일치입니다. 예는 다른 숫자와 함께 `one`과 일치 하는 다음과 같은 regex입니다.

```javascript
(plus )?(zero|one|two|three|four|five|six|seven|eight|nine)(\s+(zero|one|two|three|four|five|six|seven|eight|nine))*
``` 

이 regex 식은 `phone`과 같이 이러한 숫자로 끝나는 단어도 찾습니다. 이와 같은 문제를 해결 하려면 regex와 일치 하는 단어 경계를 사용 해야 합니다. 이 예제에서 단어 경계를 사용 하는 regex는 다음 regex에 사용 됩니다.

```javascript
\b(plus )?(zero|one|two|three|four|five|six|seven|eight|nine)(\s+(zero|one|two|three|four|five|six|seven|eight|nine))*\b
```

### <a name="example-json"></a>예제 JSON

@No__t-0을 정규식 엔터티 정의로 사용할 경우 다음 JSON 응답은 쿼리에 대해 반환 된 정규식 엔터티를 사용 하는 utterance 예입니다.

`When was kb123456 published?`:

#### <a name="v2-prediction-endpoint-responsetabv2"></a>[V2 예측 끝점 응답](#tab/V2)

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


#### <a name="v3-prediction-endpoint-responsetabv3"></a>[V3 예측 끝점 응답](#tab/V3)


쿼리 문자열에 `verbose=false`이 설정 된 경우이는 JSON입니다.

```json
"entities": {
    "KB number": [
        "kb123456"
    ]
}
```

쿼리 문자열에 `verbose=true`이 설정 된 경우이는 JSON입니다.

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

이 [자습서](luis-quickstart-intents-regex-entity.md)에서는 **정규식** 엔터티를 사용 하 여 utterance에서 일관 되 게 서식이 지정 된 데이터를 추출 하는 앱을 만듭니다.