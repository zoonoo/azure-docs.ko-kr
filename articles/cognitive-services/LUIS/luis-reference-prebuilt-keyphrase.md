---
title: 키 구 미리 작성 한 엔터티-LUIS
titleSuffix: Azure Cognitive Services
description: 이 문서에는 LUIS(Language Understanding)의 keyPhrase 미리 빌드된 엔터티가 포함됩니다.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 09/27/2019
ms.author: diberry
ms.openlocfilehash: e55c0453c117c51e5a8e4986631516d3e61ed10b
ms.sourcegitcommit: 5f0f1accf4b03629fcb5a371d9355a99d54c5a7e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/30/2019
ms.locfileid: "71677589"
---
# <a name="keyphrase-prebuilt-entity-for-a-luis-app"></a>LUIS 앱용 keyPhrase 미리 빌드된 엔터티
키 구 엔터티는 utterance에서 다양 한 키 구를 추출 합니다. 키 구를 포함 하는 예제 길이 발언를 응용 프로그램에 추가할 필요가 없습니다. 키 구 엔터티는 [텍스트 분석](../text-analytics/overview.md) 기능의 일부로 [많은 문화권](luis-language-support.md#languages-supported) 에서 지원 됩니다. 

## <a name="resolution-for-prebuilt-keyphrase-entity"></a>미리 빌드된 keyPhrase 엔터티의 해결

#### <a name="v2-prediction-endpoint-responsetabv2"></a>[V2 예측 끝점 응답](#tab/V2)

다음 예제에서는 **builtin.keyPhrase** 엔터티의 해결을 보여 줍니다.

```json
{
  "query": "where is the educational requirements form for the development and engineering group",
  "topScoringIntent": {
    "intent": "GetJobInformation",
    "score": 0.182757929
  },
  "entities": [
    {
      "entity": "development",
      "type": "builtin.keyPhrase",
      "startIndex": 51,
      "endIndex": 61
    },
    {
      "entity": "educational requirements",
      "type": "builtin.keyPhrase",
      "startIndex": 13,
      "endIndex": 36
    }
  ]
}
```
#### <a name="v3-prediction-endpoint-responsetabv3"></a>[V3 예측 끝점 응답](#tab/V3)

다음 JSON은 `verbose` 매개 변수를 `false`로 설정 합니다.

```json
{
    "query": "where is the educational requirements form for the development and engineering group",
    "prediction": {
        "normalizedQuery": "where is the educational requirements form for the development and engineering group",
        "topIntent": "GetJobInformation",
        "intents": {
            "GetJobInformation": {
                "score": 0.157861546
            }
        },
        "entities": {
            "keyPhrase": [
                "educational requirements",
                "development"
            ]
        }
    }
}
```

다음 JSON은 `verbose` 매개 변수를 `true`로 설정 합니다.

```json
{
    "query": "where is the educational requirements form for the development and engineering group",
    "prediction": {
        "normalizedQuery": "where is the educational requirements form for the development and engineering group",
        "topIntent": "GetJobInformation",
        "intents": {
            "GetJobInformation": {
                "score": 0.157861546
            }
        },
        "entities": {
            "keyPhrase": [
                "educational requirements",
                "development"
            ],
            "$instance": {
                "keyPhrase": [
                    {
                        "type": "builtin.keyPhrase",
                        "text": "educational requirements",
                        "startIndex": 13,
                        "length": 24,
                        "modelTypeId": 2,
                        "modelType": "Prebuilt Entity Extractor",
                        "recognitionSources": [
                            "model"
                        ]
                    },
                    {
                        "type": "builtin.keyPhrase",
                        "text": "development",
                        "startIndex": 51,
                        "length": 11,
                        "modelTypeId": 2,
                        "modelType": "Prebuilt Entity Extractor",
                        "recognitionSources": [
                            "model"
                        ]
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

[percentage](luis-reference-prebuilt-percentage.md), [number](luis-reference-prebuilt-number.md) 및 [age](luis-reference-prebuilt-age.md) 엔터티에 대해 알아봅니다.
