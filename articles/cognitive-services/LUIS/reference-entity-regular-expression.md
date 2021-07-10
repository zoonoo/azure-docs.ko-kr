---
title: 정규식 엔터티 형식 - LUIS
description: 정규식은 원시 발화 텍스트에 적합합니다. 대/소문자를 무시하고 문화적 변형을 무시합니다.  정규식 일치는 토큰 수준이 아니라 문자 수준에서 맞춤법 검사 변경 후에 적용됩니다.
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 05/05/2021
ms.openlocfilehash: e53d21cc4e28f6c388b4e4f980a3352021fc2bcb
ms.sourcegitcommit: 2cb7772f60599e065fff13fdecd795cce6500630
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/06/2021
ms.locfileid: "108803407"
---
# <a name="regular-expression-entity"></a>정규식 엔터티

정규식 엔터티는 사용자가 제공하는 정규식 패턴을 기반으로 하여 엔터티를 추출합니다.

정규식은 원시 발화 텍스트에 적합합니다. 대/소문자를 무시하고 문화적 변형을 무시합니다.  정규식 일치는 토큰 수준에서 맞춤법 검사를 변경한 후에 적용됩니다. 많은 대괄호를 사용하는 것처럼 정규식이 너무 복잡한 경우 모델에 식을 추가할 수 없습니다. [.NET Regex](/dotnet/standard/base-types/regular-expressions) 라이브러리의 전체가 아니라 일부를 사용합니다.

**다음과 같은 경우 이 엔터티가 적합합니다.**

* 데이터는 일관된 변형으로 일관되게 서식이 지정됩니다.
* 정규식에는 2개 이상의 중첩 수준이 필요하지 않습니다.

![정규식 엔터티](./media/luis-concept-entities/regex-entity.png)

## <a name="example-json"></a>예제 JSON

`kb[0-9]{6}`를 정규식 엔터티 정의로 사용하는 경우, 다음 JSON 응답은 쿼리에 대해 반환된 정규식 엔터티가 포함된 음성 예입니다.

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


쿼리 문자열에 `verbose=false`가 설정된 경우의 JSON입니다.

```json
"entities": {
    "KB number": [
        "kb123456"
    ]
}
```

쿼리 문자열에 `verbose=true`가 설정된 경우의 JSON입니다.

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

엔터티에 대해 자세히 알아봅니다.

* [개념](luis-concept-entity-types.md)
* [만드는 방법](luis-how-to-add-entities.md)
