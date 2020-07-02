---
title: '자습서: 기계 학습 엔터티를 사용하여 정형 데이터 추출 - LUIS'
description: 기계 학습 엔터티를 사용하여 발화에서 정형 데이터를 추출합니다. 추출 정확도를 높이려면 기능을 포함하는 하위 엔터티를 추가합니다.
ms.topic: tutorial
ms.date: 05/08/2020
ms.openlocfilehash: e16badfb2e024c5d82f1aed8a02aa901dca2c2a9
ms.sourcegitcommit: 32592ba24c93aa9249f9bd1193ff157235f66d7e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/01/2020
ms.locfileid: "85611019"
---
# <a name="tutorial-extract-structured-data-from-user-utterance-with-machine-learning-entities-in-language-understanding-luis"></a>자습서: LUIS(Language Understanding)에서 기계 학습 엔터티를 사용하여 사용자 발화에서 정형 데이터 추출

이 자습서에서는 기계 학습 엔터티를 사용하여 발화에서 정형 데이터를 추출합니다.

기계 학습 엔터티는 [기능](luis-concept-feature.md)이 포함된 하위 엔터티 엔터티를 제공하여 [모델 분해 개념](luis-concept-model.md#v3-authoring-model-decomposition)을 지원합니다.

**이 자습서에서 학습할 내용은 다음과 같습니다.**

> [!div class="checklist"]
> * 예제 앱 가져오기
> * 기계 학습 엔터티 추가
> * 하위 엔터티 및 기능 추가
> * 앱 학습, 테스트, 게시
> * 엔드포인트에서 엔터티 예측 가져오기

[!INCLUDE [LUIS Free account](includes/quickstart-tutorial-use-free-starter-key.md)]


## <a name="why-use-a-machine-learning-entity"></a>기계 학습 엔터티를 사용하는 이유

이 자습서에서는 기계 학습 엔터티를 추가하여 사용자의 발화에서 데이터를 추출합니다.

엔터티는 발화 내에서 추출할 데이터를 정의합니다. 여기에는 이름, 형식(가능한 경우), 모호한 경우 데이터의 해상도 및 데이터를 구성하는 정확한 텍스트를 데이터에 제공하는 것이 포함됩니다.

데이터를 정의하려면 다음을 수행해야 합니다.
* 엔터티 만들기
* 엔터티를 나타내는 텍스트의 레이블을 예제 발화 내에서 지정합니다. 이러한 레이블이 지정된 예제에서는 엔터티가 무엇이고 발화에서 해당 엔터티를 찾을 수 있는 위치를 LUIS에 알려줍니다.

## <a name="entity-decomposability-is-important"></a>엔터티 분해능은 중요합니다.

엔터티 분해능은 의도 예측과 엔터티를 사용한 데이터 추출에 모두 중요합니다.

데이터 추출을 위한 출발점이자 최상위 엔터티인 기계 학습 엔터티로 시작합니다. 그런 다음, 엔터티를 하위 엔터티로 분해합니다.

앱을 시작할 때 엔터티를 자세히 알아볼 수는 없지만, 기계 학습 엔터티로 시작한 다음, 앱이 완성됨에 따라 하위 엔터티로 분해하는 것이 가장 좋습니다.

이 자습서에서는 피자 앱의 주문을 나타내는 기계 학습 엔터티를 만듭니다. 엔터티가 주문 관련 텍스트, 끌어오기 크기 및 수량을 추출합니다.

`Please deliver one large cheese pizza to me` 발화에서는 `one large cheese pizza`를 주문으로 추출한 다음, 수량에 대해 `1`, 크기에 대해 `large`도 추출해야 합니다.

## <a name="download-json-file-for-app"></a>앱에 대한 JSON 파일 다운로드

[앱 JSON 파일](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-language-understanding/master/documentation-samples/tutorials/machine-learned-entity/pizza-intents-only.json)을 다운로드하고 저장합니다.

## <a name="import-json-file-for-app"></a>앱에 대한 JSON 파일 가져오기

[!INCLUDE [Import app steps](includes/import-app-steps.md)]

## <a name="create-machine-learned-entity"></a>기계 학습 엔터티 만들기

피자 주문에 대한 세부 정보를 추출하려면 최상위 기계 학습 `Order` 엔터티를 만듭니다.

1. **의도** 페이지에서 **OrderPizza** 의도를 선택합니다.

1. 발화 예제 목록에서 다음 발화를 선택합니다.

    |주문 발화 예제|
    |--|
    |`pickup a cheddar cheese pizza large with extra anchovies`|

    `pickup`의 가장 왼쪽 텍스트 바로 앞에서 선택을 시작한 다음(#1), 가장 오른쪽 텍스트인 `anchovies`로 바로 이동합니다(#2 - 레이블 지정 프로세스가 종료됨). 팝업 메뉴가 나타납니다. 팝업 상자에서 엔터티의 이름을 `Order`로 입력합니다(#3). 그런 다음, 목록에서 `Order Create new entity`를 선택 합니다(#4).

    ![전체 주문 텍스트의 시작 및 끝 레이블](media/tutorial-machine-learned-entity/mark-complete-order.png)

    > [!NOTE]
    > 엔터티가 항상 전체 발화가 되는 것은 아닙니다. 이 예제의 `pickup`은 주문을 받는 방법을 나타냅니다. 개념적 관점에서 `pickup`은 주문에 대한 레이블이 지정 된 엔터티의 일부여야 합니다.

1. **엔터티 형식 선택** 상자에서 **구조 추가**를 선택하고, **다음**을 선택합니다. 크기 및 수량과 같은 하위 엔터티를 추가하려면 구조가 필요합니다.

    ![엔터티에 구조 추가](media/tutorial-machine-learned-entity/add-structure-to-entity.png)

1. **하위 엔터티 추가(선택 사항)** 상자의 `Order` 행에서 **+** 를 선택한 다음, `Size` 및 `Quantity`을 하위 엔터티로 추가하고 **만들기**를 선택합니다.

    > [!div class="mx-imgBorder"]
    > ![엔터티에 구조 추가](media/tutorial-machine-learned-entity/add-subentities-when-creating-machine-learned-entity.png)

## <a name="edit-subentities-to-improve-extraction"></a>하위 엔터티를 편집하여 추출 향상

이전 단계에서는 엔터티 및 하위 엔터티를 만들었습니다. 추출을 개선하려면 하위 엔터티에 기능을 추가하면 됩니다.

### <a name="improve-size-extraction-with-phrase-list"></a>구 목록을 사용하여 크기 추출 향상

1. 왼쪽 메뉴에서 **엔터티**를 선택한 다음, **Order** 엔터티를 선택합니다.

1. **스키마 및 기능** 탭에서 **Size** 하위 엔터티를 선택한 다음, **+ 기능 추가**를 선택합니다.

1. 드롭다운 메뉴에서 **새 구 목록 만들기**를 선택합니다.

1. **새 구 목록 만들기** 상자에서 이름으로 `SizePhraselist`를 입력한 다음, `small`, `medium` 및 `large`의 값을 입력합니다. **제안** 상자가 채워지면 `extra large` 및 `xl`을 선택합니다. **만들기**를 선택하여 새로운 구 목록을 만듭니다.

    이 구 목록 기능은 예제 단어를 제공하여 `Size` 하위 엔터티에서 크기와 관련된 단어를 찾는 데 도움이 됩니다. 이 구 목록에 모든 크기 단어가 꼭 있어야 하는 것은 아니지만 크기를 나타내는 데 필요한 단어는 꼭 있어야 합니다.

### <a name="add-sizelist-entity"></a>SizeList 엔터티 추가

클라이언트 애플리케이션에서 인식하는 알려진 크기 목록을 추가해도 추출에 도움이 됩니다.

1. 왼쪽 메뉴에서 **엔터티**를 선택한 다음, **+ 만들기**를 선택합니다.

1. 이전 섹션에서 만든 `SizePhraselist`와 비교할 때 쉽게 구분할 수 있도록 엔터티 이름을 `SizeListentity`로 설정합니다.

1. 클라이언트 애플리케이션에 필요한 크기 `Small`, `Medium`, `Large` 및 `XLarge`를 추가한 다음, 각 크기의 동의어를 추가합니다. 동의어는 사용자가 챗봇에 입력하는 용어여야 합니다. 엔터티가 정규화된 값 또는 동의어와 정확히 일치하면 목록 엔터티를 사용하여 엔터티가 추출됩니다.

    |정규화된 값|동의어|
    |--|--|
    |작음|sm, sml, 매우 작음, 가장 작음|
    |중간|md, mdm, 보통, 평균, 중간|
    |큰|lg, lrg, 큼|
    |XLarge|xl, 가장 큼, 거대함|


    > [!div class="mx-imgBorder"]
    > ![엔터티에 구조 추가](media/tutorial-machine-learned-entity/size-list-entity-with-synonyms.png)

### <a name="add-feature-of-sizelist-entity"></a>SizeList 엔터티의 기능 추가

1. 왼쪽 메뉴에서 **엔터티**를 선택하여 엔터티 목록으로 돌아갑니다.

1. 엔터티 목록에서 **Order**를 선택합니다.

1. **스키마 및 기능** 탭에서 **Size** 엔터티를 선택한 다음, **+ 기능 추가**를 선택합니다.

1. 드롭다운 목록에서 **@ SizeListentity**를 선택합니다.

<a name="add-prebuilt-number-entity-to-app"></a>

### <a name="add-prebuilt-number-entity"></a>미리 빌드된 number 엔터티 사용

미리 빌드된 number 엔터티를 추가해도 추출에 도움이 됩니다.

1. 왼쪽 메뉴에서 **엔터티**를 선택한 다음, **미리 작성된 엔터티 추가**를 선택합니다.

1. 목록에서 **Number**를 선택하고 **완료**를 선택합니다.

1. 왼쪽 메뉴에서 **엔터티**를 선택하여 엔터티 목록으로 돌아갑니다.

### <a name="add-feature-of-prebuilt-number-entity"></a>미리 빌드된 number 엔터티 기능 추가

1. 엔터티 목록에서 **Order**를 선택합니다.

1. **스키마 및 기능** 탭에서 **Quantity** 엔터티를 선택한 다음, **+ 기능 추가**를 선택합니다.

1. 드롭다운 목록에서 **@ number**를 선택합니다.

<a name="create-subcomponent-entity-with-constraint-to-help-extract-data"></a>

## <a name="configure-required-features"></a>필요한 기능 구성

**Order** 엔터티에 대한 엔터티 세부 정보 페이지에서 **@ SizeList** 기능과 **@ number** 기능 둘 다 별표(`*`)를 선택합니다. 별표는 기능 이름과 동일한 레이블에 표시됩니다.

> [!div class="mx-imgBorder"]
> ![엔터티에 구조 추가](media/tutorial-machine-learned-entity/set-required-feature-on-subentity.png)

<a name="label-text-as-entities-in-example-utterances"></a>
<a name="label-example-utterance-to-teach-luis-about-the-entity"></a>

## <a name="label-example-utterances"></a>예제 발화의 레이블 지정

기계 학습 엔터티가 만들어지고 하위 엔터티에 기능이 생겼습니다. 추출 개선을 완료하려면 예제 발화의 레이블을 하위 엔터티로 지정해야 합니다.

1. 왼쪽 탐색 영역에서 **의도**를 선택한 다음, **OrderPizza** 의도를 선택합니다.

1. **엔터티 팔레트**를 열려면 상황에 맞는 도구에서 **@** 기호를 선택합니다.

1. 팔레트에서 각 엔터티 행을 선택한 다음, 팔레트 커서를 사용하여 각 예제 발화의 엔터티를 선택합니다. 모두 마쳤으면 엔터티 목록이 다음 이미지와 비슷할 것입니다.

    > [!div class="mx-imgBorder"]
    > ![필요한 기능 구성의 부분 스크린샷](media/tutorial-machine-learned-entity/labeled-example-utterances-for-machine-learned-entity.png)

## <a name="train-the-app"></a>앱 학습

앱을 학습시키려면 **학습**을 선택합니다. 학습은 새 엔터티 및 레이블이 지정된 발화와 같은 변경 내용을 활성 모델에 적용합니다.

## <a name="add-a-new-example-utterance"></a>새 예제 발화 추가

1. 학습 후에는 LUIS에서 기계 학습 엔터티를 얼마나 잘 이해하고 있는지 확인하기 위해 새 발화 예제를 `OrderPizza` 의도에 추가합니다.

    |주문 발화 예제|
    |--|
    |`I need a large pepperoni pizza`|

    전체 최상위 엔터티인 `Order`에 레이블이 지정되고, `Size` 하위 엔터티에도 점선으로 레이블이 지정됩니다.

    > [!div class="mx-imgBorder"]
    > ![엔터티를 사용하여 예측한 새 예제 발화의 부분 스크린샷](media/tutorial-machine-learned-entity/new-example-utterance-predicted-with-entity.png)

    점선은 현재 학습된 앱을 기반으로 하는 예측을 나타냅니다.

1. 예측을 레이블이 지정된 엔터티로 변경하려면 같은 행에서 확인 표시를 선택합니다.

    > [!div class="mx-imgBorder"]
    > ![엔터티를 사용하여 예측한 새 예제 발화의 부분 스크린샷](media/tutorial-machine-learned-entity/confirm-entity-prediction-for-new-example-utterance-added.png)

    이제 새 예제 발화 내에서 엔터티를 찾을 수 있으므로 기계 학습 엔터티가 작동합니다. 발화 예제를 추가할 때 엔터티가 올바르게 예측되지 않으면 엔터티와 하위 엔터티에 레이블을 지정합니다. 엔터티가 올바르게 예측되는 경우 예측을 확인해야 합니다.


## <a name="train-the-app-to-apply-the-entity-changes-to-the-app"></a>엔터티 변경 내용을 앱에 적용하도록 앱 학습

**학습**을 선택하여 이 새 발화로 앱을 학습시킵니다.

이 시점에서 주문에는 추출할 수 있는 몇 가지 세부 정보(크기, 수량 및 전체 주문 텍스트)가 있습니다. `Order` 엔터티를 피자 토핑, 크러스트 유형 및 추가 주문과 같이 추가로 구체화합니다. 이러한 각 항목은 `Order` 엔터티의 하위 엔터티로 만들어야 합니다.

## <a name="test-the-app-to-validate-the-changes"></a>앱을 테스트하여 변경 내용 유효성 검사

대화형 **테스트** 패널을 사용하여 앱을 테스트합니다. 이 프로세스를 통해 새 발화를 입력한 다음, 예측 결과를 살펴보고 학습된 활성 앱이 얼마나 잘 작동하는지 확인할 수 있습니다. 의도 예측은 상당히 신뢰할 수 있어야 하며(60% 이상), 엔터티 추출은 적어도 `Order` 엔터티를 선택해야 합니다. 이러한 발화 몇 개로는 모든 사례를 처리하기에 충분하지 않으므로 주문 엔터티의 세부 정보가 누락될 수 있습니다.

1. 맨 위 탐색에서 **테스트**를 선택합니다.
1. `2 small cheese pizzas for pickup` 발화를 입력하고 Enter 키를 선택합니다. 활성 모델이 의도를 올바르게 예측한 신뢰도가 60%를 넘습니다.


1. **검사**를 선택하여 엔터티 예측을 확인합니다.

    > [!div class="mx-imgBorder"]
    > ![대화형 테스트 패널에서 엔터티 예측 보기의 부분 스크린샷](media/tutorial-machine-learned-entity/interactive-test-panel-with-first-utterance-and-entity-predictions.png)

<a name="publish-with-sentiment-analysis"></a>

## <a name="publish-the-app-to-access-it-from-the-http-endpoint"></a>HTTP 엔드포인트에서 앱에 액세스할 수 있도록 앱 게시

챗봇 또는 다른 클라이언트 애플리케이션에서 LUIS 예측을 받으려면 앱을 엔드포인트에 게시해야 합니다.

1. 오른쪽 위 탐색에서 **게시**를 선택합니다.

    ![오른쪽 상단 메뉴의 엔드포인트 단추에 LUIS 게시 스크린샷](./media/howto-publish/publish-button.png)

1. **프로덕션** 슬롯, **설정 변경**, **감정 분석**, **완료**를 차례로 선택합니다.

    > [!div class="mx-imgBorder"]
    > ![엔드포인트에 LUIS 게시 스크린샷](./media/tutorial-machine-learned-entity/publish-with-sentiment-analysis.png)

1. 알림에서 **엔드포인트 URL에 액세스** 링크를 선택하여 **Azure Resources** 페이지로 이동합니다. 엔드포인트 URL은 **예제 쿼리**로 나열됩니다.

## <a name="get-intent-and-entity-prediction-from-http-endpoint"></a>HTTP 엔드포인트에서 의도 및 엔터티 예측 가져오기

1. [!INCLUDE [LUIS How to get endpoint first step](includes/howto-get-endpoint.md)]

1. 주소 표시줄의 URL 끝으로 이동하여 대화형 테스트 패널에 입력한 것과 동일한 쿼리로 _YOUR_QUERY_HERE_를 바꿉니다.

    `2 small cheese pizzas for pickup`

    마지막 쿼리 문자열 매개 변수는 발언 **쿼리**를 나타내는 `query`입니다.

    ```json
    {
        "query": "2 small cheese pizzas for pickup",
        "prediction": {
            "topIntent": "OrderPizza",
            "intents": {
                "OrderPizza": {
                    "score": 0.7812769
                },
                "None": {
                    "score": 0.0314020254
                },
                "Confirm": {
                    "score": 0.009299271
                },
                "Greeting": {
                    "score": 0.007551549
                }
            },
            "entities": {
                "Order": [
                    {
                        "Size": [
                            "small"
                        ],
                        "Quantity": [
                            2
                        ]
                    }
                ]
            }
        },
        "sentimentAnalysis":{
            "label":"neutral",
            "score":0.98
       }
    }
    ```


[!INCLUDE [LUIS How to clean up resources](includes/quickstart-tutorial-cleanup-resources.md)]

## <a name="related-information"></a>관련 정보

* [자습서 - 의도](luis-quickstart-intents-only.md)
* [개념 - 엔터티](luis-concept-entity-types.md) 개념 정보
* [개념 - 기능](luis-concept-feature.md) 개념 정보
* [학습 방법](luis-how-to-train.md)
* [게시 방법](luis-how-to-publish-app.md)
* [LUIS 포털에서 테스트하는 방법](luis-interactive-test.md)

## <a name="next-steps"></a>다음 단계

이 자습서에서 앱은 기계 학습 엔터티를 사용하여 사용자의 발화 의도를 찾고 해당 발화에서 세부 정보를 추출합니다. 기계 학습 엔터티를 사용하면 엔터티의 세부 정보를 분해할 수 있습니다.

> [!div class="nextstepaction"]
> [미리 작성된 keyphrase 엔터티 추가](luis-quickstart-intent-and-key-phrase.md)
