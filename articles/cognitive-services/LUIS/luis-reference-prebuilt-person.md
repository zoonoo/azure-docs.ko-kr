---
title: PersonName 미리 작성 한 엔터티-LUIS
titleSuffix: Azure Cognitive Services
description: 이 문서에는 Language Understanding(LUIS)의 personName 미리 빌드된 엔터티가 포함됩니다.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 05/07/2019
ms.author: diberry
ms.openlocfilehash: b5f4855c03c1c003df8f58b135cb809f1757e58f
ms.sourcegitcommit: 5f0f1accf4b03629fcb5a371d9355a99d54c5a7e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/30/2019
ms.locfileid: "71677477"
---
# <a name="personname-prebuilt-entity-for-a-luis-app"></a>LUIS 앱용 PersonName 미리 빌드된 엔터티
미리 빌드된 personName 엔터티는 사용자 이름을 검색합니다. 이 엔터티를 이미 학습했기 때문에 personName을 포함하는 예제 발화를 애플리케이션 의도에 추가할 필요가 없습니다. personName 엔터티는 영어 및 중국어 [문화권](luis-reference-prebuilt-entities.md)에서 지원됩니다.

## <a name="resolution-for-personname-entity"></a>personName 엔터티의 해결

#### <a name="v2-prediction-endpoint-responsetabv2"></a>[V2 예측 끝점 응답](#tab/V2)

다음 예제에서는 **builtin.personName** 엔터티의 해결을 보여 줍니다.

```json
{
  "query": "Is Jill Jones in Cairo?",
  "topScoringIntent": {
    "intent": "WhereIsEmployee",
    "score": 0.762141049
  },
  "entities": [
    {
      "entity": "Jill Jones",
      "type": "builtin.personName",
      "startIndex": 3,
      "endIndex": 12
    }
  ]
}
```
#### <a name="v3-prediction-endpoint-responsetabv3"></a>[V3 예측 끝점 응답](#tab/V3)


다음 JSON은 `verbose` 매개 변수를 `false`로 설정 합니다.

```json
{
    "query": "Is Jill Jones in Cairo?",
    "prediction": {
        "normalizedQuery": "is jill jones in cairo?",
        "topIntent": "None",
        "intents": {
            "None": {
                "score": 0.6544678
            }
        },
        "entities": {
            "personName": [
                "Jill Jones"
            ]
        }
    }
}
```

다음 JSON은 `verbose` 매개 변수를 `true`로 설정 합니다.

```json
{
    "query": "Is Jill Jones in Cairo?",
    "prediction": {
        "normalizedQuery": "is jill jones in cairo?",
        "topIntent": "None",
        "intents": {
            "None": {
                "score": 0.6544678
            }
        },
        "entities": {
            "personName": [
                "Jill Jones"
            ],
            "$instance": {
                "personName": [
                    {
                        "type": "builtin.personName",
                        "text": "Jill Jones",
                        "startIndex": 3,
                        "length": 10,
                        "modelTypeId": 2,
                        "modelType": "Prebuilt Entity Extractor"
                    }
                ]
            }
        }
    }
}
```

* * * 

## <a name="next-steps"></a>다음 단계

[V3 예측 끝점](luis-migration-api-v3.md)에 대해 자세히 알아보세요.

[email](luis-reference-prebuilt-email.md), [number](luis-reference-prebuilt-number.md) 및 [ordinal](luis-reference-prebuilt-ordinal.md) 엔터티에 대해 알아봅니다. 
