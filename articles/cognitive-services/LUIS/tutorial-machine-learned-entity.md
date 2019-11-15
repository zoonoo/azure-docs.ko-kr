---
title: '자습서: 기계 학습된 엔터티를 사용하여 정형 데이터 추출 - LUIS'
titleSuffix: Azure Cognitive Services
description: 기계 학습된 엔터티를 사용하여 발화에서 정형 데이터를 추출합니다. 추출 정확도를 높이려면 설명자와 제약 조건이 있는 하위 구성 요소를 추가합니다.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 11/07/2019
ms.author: diberry
ms.openlocfilehash: 36b75f33b4fc9062d09fbc670a509594142f09bd
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73828352"
---
# <a name="tutorial-extract-structured-data-with-machine-learned-entities-in-language-understanding-luis"></a>자습서: LUIS(Language Understanding)에서 기계 학습된 엔터티를 사용하여 정형 데이터 추출

이 자습서에서는 기계 학습된 엔터티를 사용하여 발화에서 정형 데이터를 추출합니다. 

기계 학습된 엔터티는 하위 구성 요소 엔터티에 설명자와 제약 조건을 제공하여 [모델 분해 개념](luis-concept-model.md#v3-authoring-model-decomposition)을 지원합니다. 

[!INCLUDE [Uses preview portal](includes/uses-portal-preview.md)]

**이 자습서에서 학습할 내용은 다음과 같습니다.**

> [!div class="checklist"]
> * 예제 앱 가져오기
> * 기계 학습된 엔터티 추가 
> * 하위 구성 요소 추가
> * 하위 구성 요소의 설명자 추가
> * 하위 구성 요소의 제약 조건 추가
> * 앱 교육
> * 앱 테스트
> * 앱 게시
> * 엔드포인트에서 엔터티 예측 가져오기

[!INCLUDE [LUIS Free account](includes/quickstart-tutorial-use-free-starter-key.md)]


## <a name="why-use-a-machine-learned-entity"></a>기계 학습된 엔터티를 사용하는 이유

이 자습서에서는 기계 학습된 엔터티를 추가하여 발화에서 데이터를 추출합니다. 

엔터티의 목적은 추출할 데이터를 정의하는 것입니다. 여기에는 이름, 형식(가능한 경우), 모호한 경우 데이터의 해상도 및 데이터를 구성하는 정확한 텍스트를 데이터에 제공하는 것이 포함됩니다. 

엔터티를 정의하려면 엔터티를 만든 다음, 발화 예제에서 엔터티를 나타내는 텍스트에 레이블을 지정해야 합니다. 이러한 레이블이 지정된 예제에서는 엔터티가 무엇이고 발화에서 해당 엔터티를 찾을 수 있는 위치를 LUIS에 알려줍니다. 

## <a name="entity-decomposability-is-important"></a>엔터티 분해능은 중요합니다.

엔터티 분해능은 의도 예측과 데이터 추출에 모두 중요합니다. 

데이터 추출을 위한 시작 및 최상위 엔터티인 기계 학습된 엔터티로 시작합니다. 그런 다음, 엔터티를 클라이언트 애플리케이션에 필요한 부분으로 분해합니다. 

앱을 시작할 때 엔터티를 자세히 알아볼 수는 없지만, 기계 학습된 엔터티로 시작한 다음, 앱이 완성됨에 따라 하위 구성 요소로 분해하는 것이 가장 좋습니다.

실제로는 피자 앱의 주문을 나타내는 기계 학습된 엔터티를 만듭니다. 주문에는 주문을 이행하는 데 필요한 모든 부분이 있어야 합니다. 시작하기 위해 엔터티에는 주문과 관련된 모든 텍스트, 특히 끌어오기 크기와 수량이 포함됩니다. 

`deliver one large cheese pizza` 발화에서는 전체 발화를 주문으로 추출한 다음, `1` 및 `large`도 추출해야 합니다. 

토핑이나 크러스트와 같은 추가 분해가 있습니다. 이 자습서가 완료되면 이러한 하위 구성 요소를 기존 `Order` 엔터티에 자신 있게 추가할 수 있습니다.

## <a name="import-example-json-to-begin-app"></a>.json 예제를 가져와서 앱 시작

1.  [앱 JSON 파일](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/documentation-samples/tutorials/machine-learned-entity/pizza-intents-only.json)을 다운로드하고 저장합니다.

1. [미리 보기 LUIS 포털](https://preview.luis.ai)의 **내 앱** 페이지에서 **가져오기**를 선택한 다음, **JSON으로 가져오기**를 선택합니다. 이전 단계에서 저장한 JSON 파일을 찾습니다. 앱 이름은 변경할 필요가 없습니다. **완료**를 선택합니다.

1. **관리** 섹션의 **버전** 탭에서 버전을 선택한 다음, **복제**를 선택하여 버전을 복제하고, 이름을 `mach-learn`으로 지정합니다. 그런 다음, **완료**를 선택하여 복제 프로세스를 완료합니다. 버전 이름이 URL 경로의 일부로 사용되므로 이름에는 URL에 유효하지 않은 문자가 포함될 수 없습니다.

    > [!TIP] 
    > 앱을 수정하기 전에 복제를 수행하는 것이 좋습니다. 버전을 사용하여 완료한 경우 버전을 .json 또는 .lu 파일로 내보내고 소스 제어에 체크 인합니다.

1. **빌드**, **의도**를 차례로 선택하여 LUIS 앱의 기본 구성 요소를 확인합니다.

    ![[버전] 페이지에서 [의도] 페이지로 변경합니다.](media/tutorial-machine-learned-entity/new-version-imported-app.png)

## <a name="label-text-as-entities-in-example-utterances"></a>발화 예제에서 텍스트를 엔터티로 레이블 지정

피자 주문에 대한 세부 정보를 추출하려면 기계 학습된 최상위 `Order` 엔터티를 만듭니다.

1. **의도** 페이지에서 **OrderPizza** 의도를 선택합니다. 

1. 발화 예제 목록에서 다음 발화를 선택합니다. 

    |주문 발화 예제|
    |--|
    |`pickup a cheddar cheese pizza large with extra anchovies`|

    `pickup`의 가장 왼쪽 텍스트 바로 앞에서 선택을 시작한 다음(#1), 가장 오른쪽 텍스트인 `anchovies`로 바로 이동합니다(#2 - 레이블 지정 프로세스가 종료됨). 팝업 메뉴가 나타납니다. 팝업 상자에서 엔터티의 이름을 `Order`로 입력합니다(#3). 그런 다음, 목록에서 `Order - Create new entity`를 선택 합니다(#4).

    ![전체 주문 텍스트의 시작 및 끝 레이블](media/tutorial-machine-learned-entity/mark-complete-order.png)

    > [!NOTE]
    > 엔터티가 항상 전체 발화가 되는 것은 아닙니다. 이 특정의 경우 `pickup`은 주문을 받는 방법을 나타내므로 레이블이 지정된 주문 엔터티의 일부여야 합니다. 

1. **엔터티 형식 선택** 상자에서 **구조 추가**를 선택하고, **다음**을 선택합니다. 크기 및 수량과 같은 하위 구성 요소를 허용하려면 구조가 필요합니다.

    ![엔터티에 구조 추가](media/tutorial-machine-learned-entity/add-structure-to-entity.png)

1. **기계 학습된 엔터티 만들기** 상자의 **구조** 상자에서 `Size`를 추가한 다음, Enter 키를 선택합니다. 
1. **설명자**를 추가하려면 **크기 설명자** 영역에서 `+`를 선택한 다음, **새 구 목록 만들기**를 선택합니다.

1. **새 구 목록 설명자 만들기** 상자에서 이름으로 `SizeDescriptor`를 입력한 다음, `small`, `medium` 및 `large`의 값을 입력합니다. **제안** 상자가 채워지면 `extra large` 및 `xl`을 선택합니다. **완료**를 선택하여 새 구 목록을 만듭니다. 

    이 구 목록 설명자는 예제 단어를 제공하여 `Size` 하위 구성 요소에서 크기와 관련된 단어를 찾는 데 도움이 됩니다. 이 목록에는 모든 크기 단어를 포함할 필요가 없지만 크기를 나타내는 데 필요한 단어를 포함해야 합니다. 

    ![크기 하위 구성 요소에 대한 설명자 만들기](media/tutorial-machine-learned-entity/size-entity-size-descriptor-phrase-list.png)

1. **기계 학습된 엔터티 만들기** 창에서 **만들기**를 선택하여 `Size` 하위 구성 요소 만들기를 완료합니다.  

    `Size` 구성 요소가 있는 `Order` 엔터티가 만들어지지만, `Order` 엔터티만 발화에 적용되었습니다. 발화 예제에서 `Size` 엔터티 텍스트에 레이블을 지정해야 합니다. 

1. 동일한 발화 예제에서 단어를 선택한 다음, 드롭다운 목록에서 **Size** 엔터티를 선택하여 `large`의 **Size** 하위 구성 요소에 레이블을 지정합니다. 

    ![발화에서 텍스트의 크기 엔터티에 레이블을 지정합니다.](media/tutorial-machine-learned-entity/mark-and-create-size-entity.png)

    텍스트에 명시적으로 레이블을 지정하여 레이블 지정과 예측이 모두 일치하므로 실선이 텍스트 아래에 그어져 있습니다.

1. 나머지 발화의 `Order` 엔터티에는 크기 엔터티와 함께 레이블을 지정합니다. 텍스트의 대괄호는 레이블이 지정된 `Order` 엔터티와 내부의 `Size` 엔터티를 나타냅니다.

    |주문 발화 예제|
    |--|
    |`can i get [a pepperoni pizza and a can of coke] please`|
    |`can i get [a [small] pizza with onions peppers and olives]`|
    |`[delivery for a [small] pepperoni pizza]`|
    |`i need [2 [large] cheese pizzas 6 [large] pepperoni pizzas and 1 [large] supreme pizza]`|

    ![나머지 모든 발화 예제의 엔터티 및 하위 구성 요소 만들기](media/tutorial-machine-learned-entity/entity-subentity-labeled-not-trained.png)

    > [!CAUTION]
    > 하나의 피자를 암시하는 `a` 문자와 같은 암시적 데이터를 어떻게 처리하나요? 또는 피자가 예상되는 위치를 나타내는 `pickup` 및 `delivery`가 부족하나요? 또는 기본 크기 또는 작거나 큰 크기를 나타내는 크기가 부족하나요? 클라이언트 애플리케이션에서 비즈니스 규칙의 일부로 처리되는 암시적 데이터 처리를 처리하는 것이 좋습니다. 

1. 앱을 학습시키려면 **학습**을 선택합니다. 학습은 새 엔터티 및 레이블이 지정된 발화와 같은 변경 내용을 활성 모델에 적용합니다.

1. 학습 후에는 LUIS에서 기계 학습된 엔터티를 이해하고 있는 정도를 파악하기 위해 새 발화 예제를 추가합니다. 

    |주문 발화 예제|
    |--|
    |`pickup XL meat lovers pizza`|

    전체 최상위 엔터티인 `Order`에 레이블이 지정되고, `Size` 하위 구성 요소에도 점선으로 레이블이 지정됩니다. 이는 성공적인 예측입니다. 

    ![엔터티를 사용하여 예측된 새 발화 예제](media/tutorial-machine-learned-entity/new-example-utterance-predicted-with-entity.png)

    점선 링크는 예측을 나타냅니다. 

1. 예측을 레이블이 지정된 엔터티로 변경하려면 행을 선택한 다음, **엔터티 예측 확인**을 선택합니다.

    ![엔터티 예측 확인을 선택하여 예측 허용](media/tutorial-machine-learned-entity/confirm-entity-prediction-for-new-example-utterance.png)

    이 시점에서 새 발화 예제 내에서 엔터티를 찾을 수 있으므로 기계 학습된 엔터티가 작동합니다. 발화 예제를 추가할 때 엔터티가 올바르게 예측되지 않으면 엔터티와 하위 구성 요소에 레이블을 지정합니다. 엔터티가 올바르게 예측되는 경우 예측을 확인해야 합니다. 

## <a name="add-prebuilt-number-to-app-to-help-extract-data"></a>데이터를 추출하는 데 도움이 되는 미리 작성된 번호를 앱에 추가

주문 정보에는 피자의 수와 같이 주문의 품목 수도 포함되어야 합니다. 이 데이터를 추출하려면 기계 학습된 새 하위 구성 요소를 `Order`에 추가해야 하며, 해당 구성 요소에는 미리 작성된 번호의 제약 조건이 필요합니다. 엔터티를 미리 작성된 번호로 제한하면 텍스트가 숫자(`2`) 또는 텍스트(`two`)인지 여부에 관계없이 엔터티에서 숫자를 찾아서 추출합니다.

먼저 미리 작성된 번호를 앱에 추가합니다. 

1. 왼쪽 메뉴에서 **엔터티**를 선택한 다음, **+ 미리 작성된 엔터티 추가**를 선택합니다. 

1. **미리 작성된 엔터티 추가** 상자에서 **번호**를 검색하여 선택한 다음, **완료**를 선택합니다. 

    ![미리 빌드된 엔터티 추가](media/tutorial-machine-learned-entity/add-prebuilt-entity-as-constraint-to-quantity-subcomponent.png)

    미리 작성된 엔터티가 앱에 추가되었지만, 제약 조건은 아직 추가되지 않았습니다. 

## <a name="create-subcomponent-entity-with-constraint-to-help-extract-data"></a>데이터를 추출하는 데 도움이 되는 제약 조건이 있는 하위 구성 요소 엔터티 만들기

`Order` 엔터티에는 주문의 항목 수를 확인하는 `Quantity` 하위 구성 요소가 있어야 합니다. 추출된 데이터를 클라이언트 애플리케이션에서 즉시 사용할 수 있도록 수량을 숫자로 제한해야 합니다. 

제약 조건은 정확히 일치하는 항목(예: 목록 엔터티) 또는 정규식(예: 정규식 엔터티 또는 미리 작성된 엔터티)을 통해 텍스트 일치로 적용됩니다. 

1. **엔터티**, `Order` 엔터티를 차례로 선택합니다. 
1. **+ 구성 요소 추가**를 선택하고, 이름으로 `Quantity`를 입력한 다음, Enter 키를 선택하여 새 엔터티를 앱에 추가합니다.
1. 성공 알림 후 `Quantity` 하위 구성 요소, 제약 조건 연필 아이콘을 차례로 선택합니다.
1. 드롭다운 목록에서 미리 작성된 번호를 선택합니다. 

    ![미리 작성된 번호를 제약 조건으로 사용하여 수량 엔터티 만들기](media/tutorial-machine-learned-entity/create-constraint-from-prebuilt-number.png)

    제약 조건이 있는 엔터티가 만들어졌지만, 아직 발화 예제에는 적용되지 않았습니다.

    > [!NOTE]
    > 하위 구성 요소는 하위 구성 요소 내에서 최대 5개 수준까지 중첩될 수 있습니다. 이 방법은 이 문서에서 보여 주지 않지만 포털 및 API에서 사용할 수 있습니다.  

## <a name="label-example-utterance-with-subcomponent-for-quantity-to-teach-luis-about-the-entity"></a>엔터티에 대해 LUIS를 학습시키기 위해 수량 하위 구성 요소를 사용하여 발화 예제에 레이블 지정

1. 왼쪽 탐색 영역에서 **의도**를 선택한 다음, **OrderPizza** 의도를 선택합니다. 다음 발화의 세 개 숫자에 레이블이 지정되지만 시각적으로 `Order` 엔터티 줄 아래에 있습니다. 이 하위 수준은 엔터티를 찾았지만 `Order` 엔터티와 별도로 간주하지 않음을 의미합니다.

    ![미리 작성된 번호를 찾았지만 Order 엔터티와 별도로 간주하지 않음](media/tutorial-machine-learned-entity/prebuilt-number-not-part-of-order-entity.png)

1. 발화 예제에서 `2`를 선택한 다음, 목록에서 `Quantity`를 선택하여 `Quantity` 엔터티를 통해 숫자에 레이블을 지정합니다. 동일한 발화 예제에서 `6` 및 `1`에 레이블을 지정합니다.

    ![수량 엔터티를 사용하여 텍스트에 레이블 지정](media/tutorial-machine-learned-entity/mark-example-utterance-with-quantity-entity.png)  

## <a name="train-the-app-to-apply-the-entity-changes-to-the-app"></a>엔터티 변경 내용을 앱에 적용하도록 앱 학습

**학습**을 선택하여 이러한 새 발화를 통해 앱을 학습시킵니다.

![앱 학습 후 발화 예제 검토](media/tutorial-machine-learned-entity/trained-example-utterances.png)

이 시점에서 주문에는 추출할 수 있는 몇 가지 세부 정보(크기, 수량 및 전체 주문 텍스트)가 있습니다. `Order` 엔터티를 피자 토핑, 크러스트 유형 및 추가 주문과 같이 추가로 구체화합니다. 이러한 각 항목은 `Order` 엔터티의 하위 구성 요소로 만들어야 합니다. 

## <a name="test-the-app-to-validate-the-changes"></a>앱을 테스트하여 변경 내용 유효성 검사

대화형 **테스트** 패널을 사용하여 앱을 테스트합니다. 이 프로세스를 통해 새 발화를 입력한 다음, 예측 결과를 보고 활성 앱과 학습된 앱의 효율적인 작동 상태를 확인할 수 있습니다. 의도 예측은 상당히 신뢰할 수 있어야 하며(70% 이상), 엔터티 추출은 적어도 `Order` 엔터티를 선택해야 합니다. 5개 발화는 모든 사례를 처리하는 데 충분하지 않으므로 주문 엔터티의 세부 정보가 누락될 수 있습니다.

1. 맨 위 탐색에서 **테스트**를 선택합니다.
1. `deliver a medium veggie pizza` 발화를 입력하고 Enter 키를 선택합니다. 활성 모델은 70%를 초과하는 신뢰도로 올바른 의도를 예측했습니다. 

    ![새 발화를 입력하여 의도 테스트](media/tutorial-machine-learned-entity/interactive-test-panel-with-first-utterance.png)

1. **검사**를 선택하여 엔터티 예측을 확인합니다.

    ![대화형 테스트 패널에서 엔터티 예측 보기](media/tutorial-machine-learned-entity/interactive-test-panel-with-first-utterance-and-entity-predictions.png)

    크기가 올바르게 식별되었습니다. `OrderPizza` 의도의 발화 예제는 `medium` 크기의 예를 포함하지 않았지만, medium(중간)이 포함된 `SizeDescriptor` 구 목록의 설명자를 사용하고 있습니다.

    수량이 정확하게 예측되지 않았습니다. 이 문제를 해결하기 위해 해당 단어를 사용하는 발화 예제를 더 많이 추가하여 수량을 표시하고 해당 단어에 `Quantity` 엔터티로 레이블을 지정할 수 있습니다. 

## <a name="publish-the-app-to-access-it-from-the-http-endpoint"></a>HTTP 엔드포인트에서 앱에 액세스할 수 있도록 앱 게시

[!INCLUDE [LUIS How to Publish steps](includes/howto-publish.md)]

## <a name="get-intent-and-entity-prediction--from-http-endpoint"></a>HTTP 엔드포인트에서 의도 및 엔터티 예측 가져오기

1. [!INCLUDE [LUIS How to get endpoint first step](includes/howto-get-endpoint.md)]

1. 주소의 URL 끝으로 이동하여 대화형 테스트 패널에 입력한 것과 동일한 쿼리를 입력합니다. 

    `deliver a medium veggie pizza`

    마지막 쿼리 문자열 매개 변수는 발언 **쿼리**를 나타내는 `query`입니다. 

    ```json
    {
        "query": "deliver a medium veggie pizza",
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
                            "medium"
                        ],
                        "$instance": {
                            "Size": [
                                {
                                    "type": "Size",
                                    "text": "medium",
                                    "startIndex": 10,
                                    "length": 6,
                                    "score": 0.9955588,
                                    "modelTypeId": 1,
                                    "modelType": "Entity Extractor",
                                    "recognitionSources": [
                                        "model"
                                    ]
                                }
                            ]
                        }
                    }
                ],
                "$instance": {
                    "Order": [
                        {
                            "type": "Order",
                            "text": "a medium veggie pizza",
                            "startIndex": 8,
                            "length": 21,
                            "score": 0.7983857,
                            "modelTypeId": 1,
                            "modelType": "Entity Extractor",
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
    

[!INCLUDE [LUIS How to clean up resources](includes/quickstart-tutorial-cleanup-resources.md)]

## <a name="related-information"></a>관련 정보

* [자습서 - 의도](luis-quickstart-intents-only.md)
* [개념 - 엔터티](luis-concept-entity-types.md) 개념 정보
* [개념 - 기능](luis-concept-feature.md) 개념 정보
* [학습 방법](luis-how-to-train.md)
* [게시 방법](luis-how-to-publish-app.md)
* [LUIS 포털에서 테스트하는 방법](luis-interactive-test.md)

## <a name="next-steps"></a>다음 단계

이 자습서에서 앱은 기계 학습된 엔터티를 사용하여 사용자의 발화 의도를 찾고 해당 발화에서 세부 정보를 추출합니다. 기계 학습된 엔터티를 사용하면 엔터티의 세부 정보를 분해할 수 있습니다.  

> [!div class="nextstepaction"]
> [미리 작성된 keyphrase 엔터티 추가](luis-quickstart-intent-and-key-phrase.md)
