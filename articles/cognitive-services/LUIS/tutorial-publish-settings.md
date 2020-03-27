---
title: '자습서: 게시 설정 - LUIS'
titleSuffix: Azure Cognitive Services
description: 이 자습서에서는 예측을 향상시키기 위해 게시 설정을 변경합니다.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 12/13/2019
ms.author: diberry
ms.openlocfilehash: 2df32c20bebf4243f383a0cccd8f037721533602
ms.sourcegitcommit: fe6c9a35e75da8a0ec8cea979f9dec81ce308c0e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/26/2020
ms.locfileid: "75890380"
---
# <a name="tutorial--add-sentiment-analysis-as-a-publishing-setting"></a>자습서:  감정 분석을 게시 설정으로 추가

이 자습서에서는 감정 분석을 추출하도록 게시 설정을 수정한 다음, LUIS 엔드포인트를 쿼리하여 반환된 사용자 발화의 감정을 확인합니다.

**이 자습서에서 학습할 내용은 다음과 같습니다.**

<!-- green checkmark -->
> [!div class="checklist"]
> * 감정 분석을 게시 설정으로 추가
> * 게시된 엔드포인트에서 발화의 감정 가져오기

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="sentiment-analysis-is-a-publish-setting"></a>감정 분석은 게시 설정

다음 발언에서는 감정의 예제를 보여줍니다.

|데이터|점수|발화|
|:--|:--|:--|
|부정|0.01 |피자가 끔찍했습니다.|
|긍정|0.97 |치즈 피자가 훌륭했습니다.|

감정 분석은 모든 발화에 적용되는 게시 설정입니다. 설정되면 앱에서 레이블을 데이터에 지정할 필요 없이 발화의 감정을 반환합니다.

게시 설정이므로 의도 또는 엔터티 페이지에 레이블이 표시되지 않습니다. [대화형 테스트](luis-interactive-test.md#view-sentiment-results) 창이나, 엔드포인트 URL에서 테스트할 때 볼 수 있습니다.

## <a name="import-example-json-to-begin-app"></a>.json 예제를 가져와서 앱 시작

1.  [앱 JSON 파일](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-language-understanding/master/documentation-samples/tutorials/machine-learned-entity/pizza-tutorial-with-entities.json)을 다운로드하고 저장합니다.

[!INCLUDE [Import app steps](includes/import-app-steps.md)]

## <a name="train-the-app"></a>앱 학습

[!INCLUDE [LUIS How to Train steps](includes/howto-train.md)]

## <a name="configure-app-to-include-sentiment-analysis"></a>감정 분석을 포함하도록 앱 구성

1. 상단 메뉴에서 **게시**를 선택합니다. 감정 분석은 게시 설정입니다.

1. **프로덕션 슬롯**, **설정 변경**을 차례로 선택합니다.
1. [감정 분석] 설정을 **켜기**로 설정합니다.

    ![게시 설정으로 감정 분석 켜기](./media/luis-quickstart-intent-and-sentiment-analysis/select-sentiment-publishing-setting.png)

## <a name="get-the-sentiment-of-an-utterance-from-the-endpoint"></a>엔드포인트에서 발언의 감정 가져오기

1. [!INCLUDE [LUIS How to get endpoint first step](includes/howto-get-endpoint.md)]

1. 주소의 URL 끝으로 이동하여 다음 발언을 입력합니다.

    `Deliver 2 of the best cheese pizzas ever!!!`

    마지막 쿼리 문자열 매개 변수는 발언 **쿼리**를 나타내는 `query`입니다. 이 발언은 레이블이 지정된 발언과 같지 않으므로 유용한 테스트이며 감정 분석이 추출된 `OrderPizza` 의도가 반환되어야 합니다.

    ```json
    {
        "query": "Deliver 2 of the best cheese pizzas ever!!!",
        "prediction": {
            "topIntent": "OrderPizza",
            "intents": {
                "OrderPizza": {
                    "score": 0.4659952
                },
                "None": {
                    "score": 0.16901511
                },
                "Confirm": {
                    "score": 0.00421415
                },
                "Greeting": {
                    "score": 0.00109914376
                }
            },
            "entities": {
                "number": [
                    2
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
                    ]
                }
            },
            "sentiment": {
                "label": "positive",
                "score": 0.861665964
            }
        }
    }
    ```

    감정 분석은 86%의 점수로 긍정적입니다.

## <a name="clean-up-resources"></a>리소스 정리

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="related-information"></a>관련 정보

* 감정 분석은 Cognitive Service [Text Analytics](../Text-Analytics/index.yml)에서 제공합니다. 기능은 Text Analytics [지원 언어](luis-language-support.md#languages-supported)로 제한됩니다.
* [학습 방법](luis-how-to-train.md)
* [게시 방법](luis-how-to-publish-app.md)
* [LUIS 포털에서 테스트하는 방법](luis-interactive-test.md)


## <a name="next-steps"></a>다음 단계
이 자습서에서는 발화 전체에서 감정 값을 추출하기 위해 감정 분석을 게시 설정으로 추가합니다.

> [!div class="nextstepaction"]
> [HR 앱에서 엔드포인트 발언 검토](luis-tutorial-review-endpoint-utterances.md)

