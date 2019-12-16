---
title: '자습서: 정규식 엔터티 - LUIS'
titleSuffix: Azure Cognitive Services
description: 정규식 엔터티를 사용하여 발화에서 데이터를 일관된 형식으로 추출합니다.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 12/04/2019
ms.author: diberry
ms.openlocfilehash: 8da47899e2d3d2c1d04e9cf4768a968e7893ce96
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/05/2019
ms.locfileid: "74840831"
---
# <a name="tutorial-get-well-formatted-data-from-the-utterance"></a>자습서: 발언에서 올바른 형식의 데이터 구하기
이 자습서에서는 발화에서 데이터를 일관된 형식으로 추출하는 정규식 엔터티를 만듭니다.

[!INCLUDE [Uses preview portal](includes/uses-portal-preview.md)]

**이 자습서에서 학습할 내용은 다음과 같습니다.**

<!-- green checkmark -->
> [!div class="checklist"]
> * 앱 가져오기
> * 의도 추가
> * 정규식 엔터티 추가
> * 추출된 데이터를 가져오기 위한 앱 학습, 게시 및 쿼리

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="regular-expression-entities"></a>정규식 엔터티

정규식 엔터티를 사용하여 발화에서 올바른 형식의 텍스트를 가져옵니다. 발언의 의도는 언제나 기계 학습을 사용하여 확인되지만, 이 특정 엔터티 형식에는 기계 학습이 적용되지 않습니다. 정규식 엔터티에는 [정규식](https://docs.microsoft.com/dotnet/standard/base-types/regular-expression-language-quick-reference)으로 일관되게 표현할 수 있는 텍스트가 적합합니다.

`Send pizza delivery time to x123432`

이 예제에서는 _짧은 코드_를 사용하여 문자 메시지를 보냅니다. 이 짧은 코드는 x 접두사가 붙은 5자리 또는 6자리 숫자 코드이며, `x\d{5,6}` 정규식을 사용하여 설명할 수 있습니다.

정규식 엔터티를 LUIS 앱에 추가하는 경우 텍스트에 정규식 엔터티를 사용하는 [레이블을 지정](label-entity-example-utterance.md)할 필요가 없습니다. 이는 모든 의도의 모든 발화에 적용됩니다.

## <a name="import-example-json-to-begin-app"></a>.json 예제를 가져와서 앱 시작

1.  [앱 JSON 파일](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/documentation-samples/tutorials/machine-learned-entity/pizza-tutorial-with-entities.json)을 다운로드하고 저장합니다.

[!INCLUDE [Import app steps](includes/import-app-steps.md)]

## <a name="create-intent-for-sending-confirmation-text-messages"></a>확인 문자 메시지를 보내기 위한 의도 만들기

1. **+ 만들기**를 선택하여 확인 텍스트를 보내기 위한 발화의 의도를 분류할 새 의도를 만듭니다.

1. 팝업 대화 상자에서 `ConfirmationText`을 입력하고 **완료**를 선택합니다.

1. 의도에 발화 예제를 추가합니다.

    |예제 발화|
    |--|
    |피자 배달 시간을 x123432로 보냅니다.|
    |x234567 텍스트는 시간입니다.|
    |x23987은 알림입니다.|

    기계 학습된 엔터티를 추출하려면 다양한 발화에 엔터티를 포함한 예제를 제공해야 하지만, 기계 학습되지 않은 엔터티를 사용하는 경우 변형이 중요하지 않습니다. 텍스트가 정규식과 일치하기만 하면 추출됩니다.

## <a name="use-the-regular-expression-entity-for-well-formatted-data"></a>올바른 형식의 데이터에 대해 정규식 엔터티 사용
텍스트 번호와 일치하는 정규식 엔터티를 만듭니다. 이 정규식은 텍스트와 일치하지만, 대/소문자 및 문화권 변형은 무시합니다.

1. 왼쪽 패널에서 **엔터티**를 선택합니다.

1. [엔터티] 목록 페이지에서 **+ 만들기**를 선택합니다.

1. 팝업 대화 상자에서 `ConfirmationTextRegEx`라는 새 엔터티 이름을 입력하고, 엔터티 형식으로 **RegEx**를 선택하고, **다음**을 선택합니다.

    > [!div class="mx-imgBorder"]
    > ![정규식 엔터티에 대한 엔터티 만들기 단계 시작](./media/luis-quickstart-intents-regex-entity/pizza-create-new-entity.png)

1. **정규식 엔터티 만들기**에서 **Regex** 값으로 `x\d{5,6}`를 입력한 다음, **만들기**를 선택합니다.

    > [!div class="mx-imgBorder"]
    > ![발화 예제에서 데이터를 추출하는 정규식 입력](./media/luis-quickstart-intents-regex-entity/pizza-set-regular-expression-for-new-entity.png)

1. 왼쪽 메뉴에서 **의도**를 선택한 다음, **ConfirmationText** 의도를 선택하여 발화에 레이블이 지정된 정규식을 표시합니다.

    > [!div class="mx-imgBorder"]
    > ![발화 예제에서 레이블이 지정된 정규식 보기](./media/luis-quickstart-intents-regex-entity/pizza-reg-ex-entity-shown-example-utterances-intent.png)

    이 엔터티는 기계 학습된 엔터티가 아니므로 발화에 적용되고, 만들어지는 즉시 LUIS 포털에 표시됩니다.

## <a name="train-the-app-before-testing-or-publishing"></a>테스트하거나 게시하기 전에 앱 학습

[!INCLUDE [LUIS How to Train steps](includes/howto-train.md)]

## <a name="publish-the-app-to-query-from-the-endpoint"></a>엔드포인트에서 쿼리할 앱 게시

[!INCLUDE [LUIS How to Publish steps](includes/howto-publish.md)]

## <a name="get-intent-and-entity-prediction-from-endpoint"></a>엔드포인트에서 의도 및 엔터티 예측 가져오기

1. [!INCLUDE [LUIS How to get endpoint first step](includes/howto-get-endpoint.md)]

2. 주소의 URL 끝으로 이동하여 다음 발언을 입력합니다.

    `Text my pizza delivery to x23456 x234567 x12345`

    마지막 쿼리 문자열 매개 변수는 발언 **쿼리**를 나타내는 `query`입니다.

    ```json
    {
        "query": "Text my pizza delivery to x23456 x234567 x12345",
        "prediction": {
            "topIntent": "ConfirmationText",
            "intents": {
                "ConfirmationText": {
                    "score": 0.7061845
                },
                "ModifyOrder": {
                    "score": 0.196021989
                },
                "None": {
                    "score": 0.02342912
                },
                "Test-Pizza": {
                    "score": 0.01213586
                },
                "CancelOrder": {
                    "score": 0.0063042324
                },
                "Confirmation": {
                    "score": 0.0058615827
                },
                "Greetings": {
                    "score": 0.00398947531
                }
            },
            "entities": {
                "ConfirmationTextRegEx": [
                    "x23456",
                    "x234567",
                    "x12345"
                ],
                "$instance": {
                    "ConfirmationTextRegEx": [
                        {
                            "type": "ConfirmationTextRegEx",
                            "text": "x23456",
                            "startIndex": 26,
                            "length": 6,
                            "modelTypeId": 8,
                            "modelType": "Regex Entity Extractor",
                            "recognitionSources": [
                                "model"
                            ]
                        },
                        {
                            "type": "ConfirmationTextRegEx",
                            "text": "x234567",
                            "startIndex": 33,
                            "length": 7,
                            "modelTypeId": 8,
                            "modelType": "Regex Entity Extractor",
                            "recognitionSources": [
                                "model"
                            ]
                        },
                        {
                            "type": "ConfirmationTextRegEx",
                            "text": "x12345",
                            "startIndex": 41,
                            "length": 6,
                            "modelTypeId": 8,
                            "modelType": "Regex Entity Extractor",
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

    LUIS는 정규식 엔터티를 사용하여 명명된 데이터를 추출합니다. 이 데이터는 프로그래밍 방식으로 JSON 응답을 받는 클라이언트 애플리케이션에 더 유용합니다.


[!INCLUDE [LUIS How to clean up resources](includes/quickstart-tutorial-cleanup-resources.md)]

## <a name="related-information"></a>관련 정보

* [개념 - 엔터티](luis-concept-entity-types.md)
* [정규식 엔터티의 JSON 참조](reference-entity-regular-expression.md?tabs=V3)
* [데이터를 추출하는 엔터티를 추가하는 방법](luis-how-to-add-entities.md)

## <a name="next-steps"></a>다음 단계
이 자습서에서는 새 의도를 만들고, 예제 발언을 추가하고, 발언에서 적절한 형식의 데이터를 추출하는 정규식 엔터티를 만들었습니다. 교육 및 앱 게시를 마친 후 엔드포인트 쿼리가 의도를 식별하고 추출된 데이터를 반환했습니다.

> [!div class="nextstepaction"]
> [목록 엔터티에 대해 알아보기](tutorial-list-entity.md)

