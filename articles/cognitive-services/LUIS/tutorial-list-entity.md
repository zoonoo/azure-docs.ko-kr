---
title: '자습서: 목록 엔터티 - LUIS'
titleSuffix: Azure Cognitive Services
description: 미리 정의된 항목 목록과 일치하는 데이터를 가져옵니다. 목록의 각 항목과 정확하게 일치하는 동의어가 있을 수 있음
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 12/17/2019
ms.author: diberry
ms.openlocfilehash: 056c64657f42d56879928f518598206d45493f60
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75447779"
---
# <a name="tutorial-get-exact-text-matched-data-from-an-utterance-with-list-entity"></a>자습서: 목록 엔터티를 사용하여 발화에서 텍스트가 정확히 일치하는 데이터 가져오기

이 자습서에서는 미리 정의된 항목 목록과 정확히 일치하는 데이터를 가져오는 방법에 대해 알아봅니다.

**이 자습서에서 학습할 내용은 다음과 같습니다.**

<!-- green checkmark -->
> [!div class="checklist"]
> * 앱 가져오기 및 기존 의도 사용
> * 목록 엔터티 추가
> * 추출된 데이터를 가져오기 위한 앱 학습, 게시 및 쿼리

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="what-is-a-list-entity"></a>목록 엔터티란?

목록 엔터티는 발언의 단어와 정확히 일치하는 텍스트입니다. 목록의 각 항목은 동의어 목록을 포함할 수 있습니다. 정확히 일치하는 항목을 원하는 경우 목록 엔터티를 사용합니다.

가져온 이 피자 애플리케이션의 경우 다양한 유형의 피자 크러스트에 대한 목록 엔터티를 만듭니다.

다음과 같은 경우 목록 엔터티는 이러한 종류의 데이터에 적합합니다.

* 데이터 값이 알려진 집합입니다.
* 집합이 이 엔터티 형식의 최대 LUIS [경계](luis-boundaries.md)를 초과하지 않습니다.
* 발언의 텍스트가 동의어 또는 정식 이름과 정확히 일치합니다. LUIS는 정확한 텍스트 일치를 벗어나는 목록을 사용하지 않습니다. 형태소 분석, 복수형 및 기타 변형은 목록 엔터티만으로는 확인되지 않습니다. 변형을 관리하려면 선택적인 텍스트 구문이 포함된 [패턴](reference-pattern-syntax.md#syntax-to-mark-optional-text-in-a-template-utterance)을 사용하는 것이 좋습니다.

> [!CAUTION]
> 설명자로 목록 엔터티 또는 구 목록이 있는 기계 학습된 엔터티를 사용할지가 확실하지 않은 경우 구 목록이 있는 기계 학습된 엔터티를 설명자로 사용하는 것이 가장 유연하고 적합합니다. 이 방법을 사용하면 LUIS에서 추출할 데이터의 값을 학습하고 확장할 수 있습니다.

## <a name="import-example-json-and-add-utterances"></a>.json 예제 가져오기 및 발화 추가

1.  [앱 JSON 파일](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-language-understanding/master/documentation-samples/tutorials/machine-learned-entity/pizza-tutorial-with-entities.json)을 다운로드하고 저장합니다.

    [!INCLUDE [Import app steps](includes/import-app-steps.md)]

1. 가져온 앱에는 `OrderPizza` 의도가 있습니다. 이 의도를 선택하고, 새 크러스트 유형을 사용하여 몇 가지 발화를 추가합니다.

    |새 발화|
    |--|--|
    |작은 팬 크러스트 페퍼로니 피자를 주문하세요.|
    |얇은 크러스트 하와이안 피자 3개입니다.|
    |막대 빵과 함께 2개의 크러스트 치즈 피자를 배달합니다.|
    |두꺼운 크러스트 피자 1개를 픽업합니다.|
    |매우 두꺼운 페퍼로니 피자 1개입니다.|

## <a name="crust-list-entity"></a>크러스트 목록 엔터티

**OrderPizza** 의도에 크러스트 유형이 포함된 발화 예제가 있으므로, 이제 LUIS는 크러스트 유형을 나타내는 단어를 이해해야 합니다.

기본 이름과 동의어의 예는 다음과 같습니다.

|정식 이름|동의어|
|--|--|
|매우 두꺼운 피자|매우 두껍게<br>매우 두꺼운 피자 크러스트<br>두껍게<br>두꺼운 크러스트|
|팬|레귤러<br>오리지널<br>노멀<br>레귤러 크러스트<br>오리지널 크러스트<br>노멀 크러스트|
|치즈|치즈 크러스트|
|얇게|얇은 크러스트<br>매우 얇게<br>매우 얇은 크러스트|

1. 왼쪽 패널에서 **엔터티**를 선택합니다.

1. **+ 만들기**를 선택합니다.

1. 엔터티 팝업 대화 상자에서 엔터티 이름으로 `CrustList`를 입력하고 엔터티 형식으로 **목록**을 입력합니다. **다음**을 선택합니다.

    > [!div class="mx-imgBorder"]
    > ![새 엔터티 만들기 팝업 대화 상자의 스크린샷](media/luis-quickstart-intent-and-list-entity/create-pizza-crust-list-entity.png)

1. **목록 엔터티 만들기** 페이지에서 정식 이름 및 각 정식 이름에 대한 동의어를 입력한 다음, **만들기**를 선택합니다.

    > [!div class="mx-imgBorder"]
    > ![항목을 목록 엔터티에 추가하는 스크린샷](media/luis-quickstart-intent-and-list-entity/add-pizza-crust-items-list-entity.png)

    목록 엔터티를 LUIS 앱에 추가하는 경우 텍스트에 목록 엔터티를 사용하는 [레이블을 지정](label-entity-example-utterance.md)할 필요가 없습니다. 이는 모든 의도의 모든 발화에 적용됩니다.

## <a name="train-the-app-before-testing-or-publishing"></a>테스트하거나 게시하기 전에 앱 학습

[!INCLUDE [LUIS How to Train steps](includes/howto-train.md)]

## <a name="publish-the-app-to-query-from-the-endpoint"></a>엔드포인트에서 쿼리할 앱 게시

[!INCLUDE [LUIS How to Publish steps](includes/howto-publish.md)]

## <a name="get-intent-and-entity-prediction-from-endpoint"></a>엔드포인트에서 의도 및 엔터티 예측 가져오기

1. [!INCLUDE [LUIS How to get endpoint first step](includes/howto-get-endpoint.md)]

2. 주소의 URL 끝으로 이동하여 다음 발언을 입력합니다.

    `Deliver 2 deep dish hawaiian pizzas and a thin pepperoni`

    마지막 쿼리 문자열 매개 변수는 발언 **쿼리**를 나타내는 `query`입니다.


    ```json
    {
        "query": "Deliver 2 deep dish hawaiian pizzas and a thin pepperoni",
        "prediction": {
            "topIntent": "OrderPizza",
            "intents": {
                "OrderPizza": {
                    "score": 0.9957229
                },
                "None": {
                    "score": 0.016832687
                },
                "Confirm": {
                    "score": 0.0015708931
                },
                "Greeting": {
                    "score": 0.00057060417
                }
            },
            "entities": {
                "number": [
                    2
                ],
                "CrustList": [
                    [
                        "Deep dish"
                    ],
                    [
                        "Thin"
                    ]
                ],
                "$instance": {
                    "number": [
                        {
                            "type": "builtin.number",
                            "text": "2",
                            "startIndex": 8,
                            "length": 1,
                            "modelTypeId": 2,
                            "modelType": "Prebuilt Entity Extractor",
                            "recognitionSources": [
                                "model"
                            ]
                        }
                    ],
                    "CrustList": [
                        {
                            "type": "CrustList",
                            "text": "deep dish",
                            "startIndex": 10,
                            "length": 9,
                            "modelTypeId": 5,
                            "modelType": "List Entity Extractor",
                            "recognitionSources": [
                                "model"
                            ]
                        },
                        {
                            "type": "CrustList",
                            "text": "thin",
                            "startIndex": 42,
                            "length": 4,
                            "modelTypeId": 5,
                            "modelType": "List Entity Extractor",
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

    크러스트 유형은 정확히 일치하는 텍스트 항목으로 검색되어 JSON 응답으로 반환되었습니다. 이 정보는 클라이언트 애플리케이션에서 주문을 처리하는 데 사용합니다.

[!INCLUDE [LUIS How to clean up resources](includes/quickstart-tutorial-cleanup-resources.md)]

## <a name="related-information"></a>관련 정보

* [목록 엔터티](luis-concept-entity-types.md#list-entity) 개념 정보
* [학습 방법](luis-how-to-train.md)
* [게시 방법](luis-how-to-publish-app.md)
* [LUIS 포털에서 테스트하는 방법](luis-interactive-test.md)
* [개념 - 엔터티](luis-concept-entity-types.md)
* [정규식 엔터티의 JSON 참조](reference-entity-regular-expression.md?tabs=V3)
* [데이터를 추출하는 엔터티를 추가하는 방법](luis-how-to-add-entities.md)

## <a name="next-steps"></a>다음 단계
이 자습서에서는 발화 예제를 추가한 다음, 발화에서 정확히 일치하는 텍스트를 추출하는 목록 엔터티를 만들었습니다. 교육 및 앱 게시를 마친 후 엔드포인트 쿼리가 의도를 식별하고 추출된 데이터를 반환했습니다.

> [!div class="nextstepaction"]
> [역할을 사용하여 미리 작성된 엔터티 추가](tutorial-entity-roles.md)

