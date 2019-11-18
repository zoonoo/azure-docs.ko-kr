---
title: 컴퓨터에서 배운 엔터티 형식-LUIS
titleSuffix: Azure Cognitive Services
description: ''
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 11/11/2019
ms.author: diberry
ms.openlocfilehash: 2289e8ac7744a7b4cbee300e77efda89d29ee2f5
ms.sourcegitcommit: ae8b23ab3488a2bbbf4c7ad49e285352f2d67a68
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74017237"
---
# <a name="machine-learned-entity"></a>기계 학습된 엔터티 



**엔터티는 텍스트 데이터의 경우에 적합 합니다.**


![목록 엔터티](./media/luis-concept-entities/list-entity.png)

## <a name="example-json"></a>예제 JSON

앱에 공항(Sea-tac), 공항 코드(SEA), 우편 번호(98101) 및 전화 지역 코드(206)를 사용하는 도시를 포함하여 도시 이름의 변형을 허용하는 `Cities` 목록이 있다고 가정합니다.

|목록 항목|항목 동의어|
|---|---|
|`Seattle`|`sea-tac`, `sea`, `98101`, `206`, `+1` |
|`Paris`|`cdg`, `roissy`, `ory`, `75001`, `1`, `+33`|

`book 2 tickets to paris`

이전 발화에서 `paris` 단어는 `Cities` 목록 엔터티의 일부로 paris 항목에 매핑됩니다. 목록 엔터티는 항목 동의어뿐 아니라 항목의 정규화된 이름과도 일치합니다.

#### <a name="v2-prediction-endpoint-responsetabv2"></a>[V2 예측 엔드포인트 응답](#tab/V2)

```JSON
  "entities": [
    {
      "entity": "paris",
      "type": "Cities",
      "startIndex": 18,
      "endIndex": 22,
      "resolution": {
        "values": [
          "Paris"
        ]
      }
    }
  ]
```

#### <a name="v3-prediction-endpoint-responsetabv3"></a>[V3 예측 엔드포인트 응답](#tab/V3)


쿼리 문자열에 `verbose=false` 설정 된 경우이 JSON은 다음과 같습니다.

```json
"entities": {
    "Cities": [
        [
            "Paris"
        ]
    ]
}
```

쿼리 문자열에 `verbose=true` 설정 된 경우이 JSON은 다음과 같습니다.

```json
"entities": {
    "Cities": [
        [
            "Paris"
        ]
    ],
    "$instance": {
        "Cities": [
            {
                "type": "Cities",
                "text": "paris",
                "startIndex": 18,
                "length": 5,
                "modelTypeId": 5,
                "modelType": "List Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            }
        ]
    }
}
```

* * * 

|데이터 개체|엔터티 이름|값|
|--|--|--|
|엔터티 나열|`Cities`|`paris`|


## <a name="next-steps"></a>다음 단계

[목록](reference-entity-list.md) 엔터티 및 [정규식](reference-entity-regular-expression.md) 엔터티에 대해 알아봅니다.