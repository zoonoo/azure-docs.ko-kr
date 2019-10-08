---
title: '빠른 시작: 브라우저를 사용하여 의도 알기 - LUIS'
titleSuffix: Azure Cognitive Services
description: 이 빠른 시작에서는 사용 가능한 공용 LUIS 앱을 통해 브라우저의 대화형 텍스트에서 사용자의 의도를 판단합니다.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: quickstart
ms.date: 09/27/2019
ms.author: diberry
ms.openlocfilehash: 6282e768ebc51d0d4ec2b15f057727f207a7d81a
ms.sourcegitcommit: 6fe40d080bd1561286093b488609590ba355c261
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/01/2019
ms.locfileid: "71703619"
---
# <a name="quickstart-get-intent-with-a-browser"></a>빠른 시작: 브라우저로 의도 알기

LUIS 예측 엔드포인트가 반환하는 값을 이해하려면 웹 브라우저에서 예측 결과를 확인하세요. 

## <a name="prerequisites"></a>필수 조건

공용 앱을 쿼리하려면 다음이 필요합니다.

* 사용자 고유의 LUIS(Language Understanding) 키. 아직 키를 만들 구독이 없는 경우 [체험 계정](https://azure.microsoft.com/free/)으로 등록할 수 있습니다.
* 공용 앱 ID는 `df67dcdb-c37d-46af-88e1-8b97951ca1c2`입니다. 

## <a name="use-the-browser-to-see-predictions"></a>브라우저를 사용하여 예측 보기

1. 웹 브라우저를 엽니다. 
1. 아래 전체 URL을 사용하여 `{your-key}`를 사용자의 고유한 LUIS 키로 바꿉니다. 요청은 GET 요청이며 LUIS 키가 있는 권한 부여를 쿼리 문자열 매개 변수로 포함합니다.

    #### <a name="v2-prediction-endpoint-requesttabv2"></a>[V2 예측 엔드포인트 요청](#tab/V2)
    
    **GET** 엔드포인트 요청의 V2 URL 형식은 다음과 같습니다.
    
    `
    https://westus.api.cognitive.microsoft.com/luis/v2.0/apps/df67dcdb-c37d-46af-88e1-8b97951ca1c2?subscription-key={your-key}&q=turn on all lights
    `
    
    #### <a name="v3-prediction-endpoint-requesttabv3"></a>[V3 예측 엔드포인트 요청](#tab/V3)
    
    
    **GET** 엔드포인트(슬롯) 요청의 V3 URL 형식은 다음과 같습니다.
    
    `
    https://westus.api.cognitive.microsoft.com/luis/v3.0-preview/apps/df67dcdb-c37d-46af-88e1-8b97951ca1c2/slots/production/predict?query=turn on all lights&subscription-key={your-key}
    `
    
    * * *

1. 브라우저 창에 URL을 붙여넣고 Enter 키를 누릅니다. LUIS에서 `HomeAutomation.TurnOn` 의도(최고 의도)와 `on` 값이 있는 `HomeAutomation.Operation` 엔터티를 검색한다고 나타내는 JSON 결과가 브라우저에 표시됩니다.

    #### <a name="v2-prediction-endpoint-responsetabv2"></a>[V2 예측 엔드포인트 응답](#tab/V2)

    ```json
    {
        "query": "turn on all lights",
        "topScoringIntent": {
            "intent": "HomeAutomation.TurnOn",
            "score": 0.5375382
        },
        "entities": [
            {
                "entity": "on",
                "type": "HomeAutomation.Operation",
                "startIndex": 5,
                "endIndex": 6,
                "score": 0.724984169
            }
        ]
    }
    ```

    #### <a name="v3-prediction-endpoint-responsetabv3"></a>[V3 예측 엔드포인트 응답](#tab/V3)

    ```JSON
    {
        "query": "turn on all lights",
        "prediction": {
            "normalizedQuery": "turn on all lights",
            "topIntent": "HomeAutomation.TurnOn",
            "intents": {
                "HomeAutomation.TurnOn": {
                    "score": 0.5375382
                }
            },
            "entities": {
                "HomeAutomation.Operation": [
                    "on"
                ]
            }
        }
    }
    ```

    * * *

1. 모든 의도를 보려면 적절한 쿼리 문자열 매개 변수를 추가합니다. 

    #### <a name="v2-prediction-endpointtabv2"></a>[V2 예측 엔드포인트](#tab/V2)

    QueryString의 끝에 `verbose=true`를 추가하여 **모든 의도를 표시**합니다.

    `
    https://westus.api.cognitive.microsoft.com/luis/v2.0/apps/df67dcdb-c37d-46af-88e1-8b97951ca1c2?q=turn on all lights&subscription-key={your-key}&verbose=true
    `

    ```json
    {
        "query": "turn on all lights",
        "topScoringIntent": {
            "intent": "HomeAutomation.TurnOn",
            "score": 0.5375382
        },
        "intents": [
            {
                "intent": "HomeAutomation.TurnOn",
                "score": 0.5375382
            },
            {
                "intent": "None",
                "score": 0.08687421
            },
            {
                "intent": "HomeAutomation.TurnOff",
                "score": 0.0207554
            }
        ],
        "entities": [
            {
                "entity": "on",
                "type": "HomeAutomation.Operation",
                "startIndex": 5,
                "endIndex": 6,
                "score": 0.724984169
            }
        ]
    }
    ```

    #### <a name="v3-prediction-endpointtabv3"></a>[V3 예측 엔드포인트](#tab/V3)

    QueryString의 끝에 `show-all-intents=true`를 추가하여 **모든 의도를 표시**합니다.

    `
    https://westus.api.cognitive.microsoft.com/luis/v3.0-preview/apps/df67dcdb-c37d-46af-88e1-8b97951ca1c2/slots/production/predict?query=turn on all lights&subscription-key={your-key}&show-all-intents=true
    `

    ```JSON
    {
        "query": "turn on all lights",
        "prediction": {
            "normalizedQuery": "turn on all lights",
            "topIntent": "HomeAutomation.TurnOn",
            "intents": {
                "HomeAutomation.TurnOn": {
                    "score": 0.5375382
                },
                "HomeAutomation.TurnOff": {
                     "score": 0.0207554
                },
                "None": {
                     "score": 0.08687421
                }
            },
            "entities": {
                "HomeAutomation.Operation": [
                    "on"
                ]
            }
        }
    }
    ```

    * * * 

## <a name="next-steps"></a>다음 단계

[V3 예측 엔드포인트](luis-migration-api-v3.md)에 대해 자세히 알아봅니다.

> [!div class="nextstepaction"]
> [LUIS 포털에서 앱 만들기](get-started-portal-build-app.md)