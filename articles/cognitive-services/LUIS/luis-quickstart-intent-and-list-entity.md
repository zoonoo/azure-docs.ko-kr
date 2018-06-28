---
title: 정확한 텍스트 일치 나열된 데이터를 가져오도록 LUIS 앱을 만드는 자습서 - Azure | Microsoft Docs
description: 이 자습서에서는 의도 및 목록 엔터티를 사용하는 간단한 LUIS 앱을 만들어서 이 빠른 시작에서 데이터를 추출하는 방법을 알아봅니다.
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: luis
ms.topic: tutorial
ms.date: 05/07/2018
ms.author: v-geberr
ms.openlocfilehash: 33394dff1091f27c79c74d8648a90724ba8d6698
ms.sourcegitcommit: 301855e018cfa1984198e045872539f04ce0e707
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/19/2018
ms.locfileid: "36264830"
---
# <a name="tutorial-create-app-using-a-list-entity"></a>자습서: 목록 엔터티를 사용하여 앱 만들기
이 자습서에서는 미리 정의된 목록과 일치하는 데이터를 가져오는 방법을 보여주는 앱을 만듭니다. 

<!-- green checkmark -->
> [!div class="checklist"]
> * 엔터티 목록 이해 
> * OrderDrinks 의도를 사용하여 음료 도메인에 대한 새 LUIS 앱 만들기
> * _없음_ 의도 추가 및 예제 발언 추가
> * 목록 엔터티를 추가하여 발언에서 음료 항목 추출
> * 앱 학습 및 게시
> * 앱의 엔드포인트를 쿼리하여 LUIS JSON 응답 확인

이 아티클에서는 LUIS 앱을 작성하기 위해 체험 [LUIS][LUIS] 계정이 필요합니다.

## <a name="purpose-of-the-list-entity"></a>목록 엔터티의 목적
이 앱은 `1 coke and 1 milk please`와 같은 음료 주문을 받고 음료의 종류와 같은 데이터를 반환합니다. 음료의 **목록** 엔터티는 정확한 텍스트 일치 항목을 찾고 해당 일치 항목을 반환합니다. 

데이터 값이 알려진 집합인 경우 목록 엔터티는 이러한 종류의 데이터에 적합합니다. 속어 및 약어를 비롯한 음료 이름은 달라질 수 있지만 자주 변경되지는 않습니다. 

## <a name="app-intents"></a>앱 의도
의도는 사용자가 원하는 항목의 범주입니다. 이 앱에는 OrderDrink 및 없음이라는 두 개의 의도가 있습니다. [None](luis-concept-intent.md#none-intent-is-fallback-for-app) 의도는 앱 외부의 다른 항목을 나타냅니다.  

## <a name="list-entity-is-an-exact-text-match"></a>목록 엔터티는 정확한 텍스트 일치 항목입니다.
엔터티의 목적은 발언에서 텍스트의 부분을 찾고 분류하는 것입니다. [목록](luis-concept-entity-types.md) 엔터티는 단어 또는 구의 정확한 일치 항목에서 허용됩니다.  

이 음료 앱의 경우 LUIS는 표준 순서를 만들고 채울 수 있는 방식으로 음료 순서를 추출합니다. LUIS를 사용하면 발언에 변형, 약어 및 속어가 포함될 수 있습니다. 

사용자의 간단한 예제 발언은 다음과 같습니다.

```
2 glasses of milk
3 bottles of water
2 cokes
```

발언의 약어 또는 속어 버전은 다음과 같습니다.

```
5 milk
3 h2o
1 pop
```
 
목록 엔터티는 물에 대해 `h2o`가 일치하고 청량 음료에 대해 `pop`가 일치합니다.  

## <a name="what-luis-does"></a>LUIS에서 수행하는 작업
발언의 의도 및 엔터티가 식별되고, [추출되고](luis-concept-data-extraction.md#list-entity-data), [엔드포인트](https://aka.ms/luis-endpoint-apis)의 JSON에서 반환되면 LUIS가 완료됩니다. 호출 응용 프로그램 또는 챗봇은 앱 또는 챗봇이 설계된 방식으로 해당 JSON 응답을 사용하고 요청을 충족합니다. 

## <a name="create-a-new-app"></a>새 앱 만들기
1. [LUIS][LUIS] 웹 사이트에 로그인합니다. LUIS 엔드포인트를 게시해야 하는 [지역][LUIS-regions]에 로그인해야 합니다.

2. [LUIS][LUIS] 웹 사이트에서 **새 앱 만들기**를 선택합니다.  

    ![새 앱 만들기](./media/luis-quickstart-intent-and-list-entity/app-list.png)

3. 팝업 대화 상자에서 `MyDrinklist`라는 이름을 입력합니다. 

    ![앱 이름 MyDrinkList 지정](./media/luis-quickstart-intent-and-list-entity/create-app-dialog.png)

4. 해당 프로세스가 완료되면 앱에 **없음** 의도가 있는 **의도** 페이지가 표시됩니다. 

    [![](media/luis-quickstart-intent-and-list-entity/intents-page-none-only.png "의도 페이지 스크린샷")](media/luis-quickstart-intent-and-list-entity/intents-page-none-only.png#lightbox)

## <a name="create-a-new-intent"></a>새 의도 만들기

1. **의도** 페이지에서 **새 의도 만들기**를 선택합니다. 

    [![](media/luis-quickstart-intent-and-list-entity/create-new-intent.png "새 의도 만들기 단추가 강조 표시된 의도 페이지 스크린샷")](media/luis-quickstart-intent-and-list-entity/create-new-intent.png#lightbox)

2. `OrderDrinks`라는 새 의도 이름을 입력합니다. 이 의도는 사용자가 음료를 주문하려고 할 때마다 선택해야 합니다.

    의도를 만들면 식별하려는 정보의 기본 범주가 만들어집니다. 범주 이름을 지정하면 LUIS 쿼리 결과를 사용하는 다른 응용 프로그램에서 해당 범주 이름을 사용하여 적절한 대답을 찾거나 적절한 작업을 수행할 수 있습니다. LUIS는 이러한 질문에 답변하지 않으며, 자연어로 요청되는 정보의 유형만 식별합니다. 

    [![](media/luis-quickstart-intent-and-list-entity/intent-create-dialog-order-drinks.png "새 OrderDrings 의도를 만드는 스크린샷")](media/luis-quickstart-intent-and-list-entity/intent-create-dialog-order-drinks.png#lightbox)

3. 사용자가 요청해야 하는 `OrderDrinks` 의도에 다음과 같은 몇 가지 발언을 추가합니다.

    | 예제 발언|
    |--|
    |내 방으로 콜라 2병과 물 한 병을 보내주세요.|
    |라임향 페리에 2병|
    |h20|

    [![](media/luis-quickstart-intent-and-list-entity/intent-order-drinks-utterance.png "OrderDrinks 의도 페이지에 발언을 입력하는 스크린샷")](media/luis-quickstart-intent-and-list-entity/intent-order-drinks-utterance.png#lightbox)

## <a name="add-utterances-to-none-intent"></a>없음 의도에 발언 추가

LUIS 앱에는 현재 **없음** 의도에 대한 발언이 없습니다. 앱에서 답변하지 않도록 하려는 발언이 필요하므로 **없음** 의도에 발언이 있어야 합니다. 이 의도를 비워 두지 마세요. 

1. 왼쪽 패널에서 **의도**를 선택합니다. 

    [![](media/luis-quickstart-intent-and-list-entity/left-panel-intents.png "왼쪽 패널에서 의도 링크를 선택하는 스크린샷")](media/luis-quickstart-intent-and-list-entity/left-panel-intents.png#lightbox)

2. **없음** 의도를 선택합니다. 사용자가 입력할 수 있지만 앱과 관련이 없는 세 개의 발언을 추가합니다.

    | 예제 발언|
    |--|
    |취소!|
    |안녕|
    |무슨 일이 일어나고 있나요?|

## <a name="when-the-utterance-is-predicted-for-the-none-intent"></a>없음 의도에 발언이 예측되는 경우
챗봇과 같은 LUIS 호출 응용 프로그램에서 LUIS가 발언에 대해 **없음** 의도를 반환하는 경우, 봇에서는 사용자가 대화를 종료하려는지 물어볼 수 있습니다. 사용자가 종료하지 않으려고 하는 경우, 봇에서 대화를 계속하기 위해 더 많은 지침을 제공할 수도 있습니다. 

**없음** 의도에서 엔터티가 작동합니다. 최상의 점수 매기기 의도가 **없음**이지만 챗봇에 대해 의미 있는 엔터티가 추출된 경우 챗봇은 고객의 의도에 집중하는 질문을 계속할 수 있습니다. 

## <a name="create-a-menu-entity-from-the-intent-page"></a>의도 페이지에서 메뉴 엔터티 만들기
이제 두 의도에 발언이 있으므로 LUIS는 음료를 이해해야 합니다. 다시 `OrderDrinks` 의도를 탐색하고 다음 단계에 따라 발언에서 음료의 레이블을 지정(표시)합니다.

1. 왼쪽 패널에서 **의도**를 선택하여 `OrderDrinks` 의도로 돌아갑니다.

2. 의도 목록에서 `OrderDrinks`를 선택합니다.

3. `Please send 2 cokes and a bottle of water to my room` 발언에서 `water`라는 단어를 선택합니다. 새 엔터티를 만들기 위해 맨 위에 있는 입력란에서 드롭다운 메뉴가 나타납니다. 텍스트 상자에서 `Drink` 엔터티 이름을 입력한 다음, 드롭다운 메뉴에서 **새 엔터티 만들기**를 선택합니다. 

    [![](media/luis-quickstart-intent-and-list-entity/intent-label-h2o-in-utterance.png "발언에서 단어를 선택하여 새 엔터티를 만드는 스크린샷")](media/luis-quickstart-intent-and-list-entity/intent-label-h2o-in-utterance.png#lightbox)

4. 팝업 창에서 **목록** 엔터티 형식을 선택합니다. 동의어 `h20`을 추가합니다. 각 동의어 뒤에 Enter 키를 선택합니다. `perrier`를 동의어 목록에 추가하지 마세요. 해당 단어는 다음 단계에서 예제로 추가됩니다. **완료**를 선택합니다.

    [![](media/luis-quickstart-intent-and-list-entity/create-list-ddl.png "새 엔터티를 구성하는 스크린샷")](media/luis-quickstart-intent-and-list-entity/create-list-ddl.png#lightbox)

5. 엔터티를 만들었으므로 물에 대한 동의어를 선택하여 물에 대한 다른 동의어의 레이블을 지정한 다음, 드롭다운 목록에서 `Drink`를 선택합니다. 오른쪽의 메뉴에 따른 다음, `Set as synonym`을 선택한 다음, `water`를 선택합니다.

    [![](media/luis-quickstart-intent-and-list-entity/intent-label-perriers.png "기존 엔터티를 사용하여 발언의 레이블을 지정하는 스크린샷")](media/luis-quickstart-intent-and-list-entity/intent-label-perriers.png#lightbox)

## <a name="modify-the-list-entity-from-the-entity-page"></a>엔터티 페이지에서 목록 엔터티 수정
음료 목록 엔터티가 만들어지지만 많은 항목 및 동의어는 없습니다. 용어, 약어 및 속어 중 일부를 알고 있는 경우 **엔터티** 페이지의 목록에서 입력하는 것이 빠릅니다  

1. 왼쪽 패널에서 **엔터티**를 선택합니다.

    [![](media/luis-quickstart-intent-and-list-entity/intent-select-entities.png "왼쪽 패널에서 엔터티를 선택하는 스크린샷")](media/luis-quickstart-intent-and-list-entity/intent-select-entities.png#lightbox)

2. 엔터티 목록에서 `Drink`를 선택합니다.

    [![](media/luis-quickstart-intent-and-list-entity/entities-select-drink-entity.png "엔터티 목록에서 음료 엔터티를 선택하는 스크린샷")](media/luis-quickstart-intent-and-list-entity/entities-select-drink-entity.png#lightbox)

3. 텍스트 상자에 `Soda pop`을 입력한 다음, Enter 키를 선택합니다. 탄산음료에 광범위하게 적용되는 용어입니다. 모든 문화권에는 이러한 종류의 음료에 대한 별명 또는 속어가 있습니다.

    [![](media/luis-quickstart-intent-and-list-entity/drink-entity-enter-canonical-name.png "정식 이름을 입력하는 스크린샷")](media/luis-quickstart-intent-and-list-entity/drink-entity-enter-canonical-name.png#lightbox)

4. `Soda pop`와 같은 줄에 다음과 같은 동의어를 입력합니다. 

    ```
    coke
    cokes
    coca-cola
    coca-colas
    ```

    동의어에는 구, 문장 부호, 소유격 및 복수형이 포함될 수 있습니다. 목록 엔터티이 정확한 텍스트 일치 항목이므로(대소문자 제외) 동의어에는 여러 변형이 있어야 합니다. 쿼리 로그에서 자세한 변형을 배우거나 엔드포인트 도달을 검토하려면 목록을 확장할 수 있습니다. 

    이 아티클에는 예제를 간소화하는 몇 가지 동의어가 있습니다. 프로덕션 수준 LUIS 앱에는 많은 동의어가 있으며 정기적으로 검토되고 확장됩니다. 

    [![](media/luis-quickstart-intent-and-list-entity/drink-entity-enter-synonyms.png "동의어를 추가하는 스크린샷")](media/luis-quickstart-intent-and-list-entity/drink-entity-enter-synonyms.png#lightbox)

## <a name="train-the-luis-app"></a>LUIS 앱 학습
LUIS는 학습될 때까지 의도와 엔터티(모델)에 대한 변경 내용을 인식하지 못합니다. 

1. LUIS 웹 사이트의 오른쪽 위에서 **학습** 단추를 선택합니다.

    ![앱 학습](./media/luis-quickstart-intent-and-list-entity/train-button.png)

2. 웹 사이트의 위쪽에 성공이 확인된 녹색 상태 표시줄이 표시되면 학습이 완료됩니다.

    ![학습 성공](./media/luis-quickstart-intent-and-list-entity/trained.png)

## <a name="publish-the-app-to-get-the-endpoint-url"></a>앱을 게시하여 엔드포인트 URL 가져오기
챗봇 또는 다른 응용 프로그램에서 LUIS 예측을 얻으려면 앱을 게시해야 합니다. 

1. LUIS 웹 사이트의 오른쪽 위에서 **게시** 단추를 선택합니다. 

    [![](media/luis-quickstart-intent-and-list-entity/publish.png "게시 단추를 선택하는 스크린샷")](media/luis-quickstart-intent-and-list-entity/publish.png#lightbox)

2. 프로덕션 슬롯과 **게시** 단추를 선택합니다. 

    [![](media/luis-quickstart-intent-and-list-entity/publish-to-production.png "프로덕션 슬롯 단추에 대한 게시를 선택하는 스크린샷")](media/luis-quickstart-intent-and-list-entity/publish-to-production.png#lightbox)

3. 웹 사이트의 위쪽에 성공이 확인된 녹색 상태 표시줄이 표시되면 게시가 완료됩니다.

## <a name="query-the-endpoint-with-a-different-utterance"></a>다른 발언을 사용하여 엔드포인트 쿼리
1. **게시** 페이지의 아래쪽에서 **엔드포인트** 링크를 선택합니다. 그러면 주소 표시줄에 엔드포인트 URL이 있는 다른 브라우저 창이 열립니다. 

    [![](media/luis-quickstart-intent-and-list-entity/publish-select-endpoint.png "게시 페이지에서 엔드포인트 URL 스크린샷")](media/luis-quickstart-intent-and-list-entity/publish-select-endpoint.png#lightbox)

2. 주소의 URL 끝으로 이동하고 `2 cokes and 3 waters`를 입력합니다. 마지막 쿼리 문자열 매개 변수는 발언 **쿼리**를 나타내는 `q`입니다. 이 발언은 레이블이 지정된 발언과 같지 않으므로 유용한 테스트이며 `cokes` 및 `waters`라는 두 종류의 음료를 포함한 `OrderDrinks` 의도를 반환해야 합니다.

```
{
  "query": "2 cokes and 3 waters",
  "topScoringIntent": {
    "intent": "OrderDrinks",
    "score": 0.999998569
  },
  "intents": [
    {
      "intent": "OrderDrinks",
      "score": 0.999998569
    },
    {
      "intent": "None",
      "score": 0.23884207
    }
  ],
  "entities": [
    {
      "entity": "cokes",
      "type": "Drink",
      "startIndex": 2,
      "endIndex": 6,
      "resolution": {
        "values": [
          "Soda pop"
        ]
      }
    },
    {
      "entity": "waters",
      "type": "Drink",
      "startIndex": 14,
      "endIndex": 19,
      "resolution": {
        "values": [
          "h20"
        ]
      }
    }
  ]
}
```

## <a name="where-is-the-natural-language-processing-in-the-list-entity"></a>목록 엔터티에서 자연어를 처리하는 위치는 어디인가요? 
목록 엔터티가 정확한 텍스트 일치 항목이기 때문에 자연어 처리(또는 기계 학습)을 사용하지 않습니다. LUIS는 자연어 처리(또는 기계 학습)를 사용하여 올바른 최상위 점수 매기기 의도를 선택합니다. 또한 발언은 둘 이상의 엔터티 또는 둘 이상의 엔터티 형식을 혼합한 것일 수 있습니다. **간단한** 엔터티와 같은 자연어 처리(또는 기계 학습) 엔터티를 비롯한 앱의 모든 엔터티에 대해 각 발언이 처리됩니다.

## <a name="what-has-this-luis-app-accomplished"></a>이 LUIS 앱에서 수행한 작업은?
이 앱에서는 단지 2개의 의도와 목록 엔터티를 사용하여 자연어 쿼리 의도를 확인하고 추출된 데이터를 반환했습니다. 

이제 챗봇에는 주 작업(`OrderDrinks`)을 결정하기에 충분한 정보 및 음료 목록 엔터티에서 주문된 음료의 종류가 있습니다. 

## <a name="where-is-this-luis-data-used"></a>이 LUIS 데이터가 사용되는 위치는? 
LUIS는 이 요청을 통해 수행됩니다. 챗봇과 같은 호출 응용 프로그램은 엔터티에서 topScoringIntent 결과 및 데이터를 사용하여 다음 단계를 진행할 수 있습니다. LUIS는 봇 또는 호출 응용 프로그램에 대해 프로그래밍 방식 작업을 수행하지 않습니다. LUIS는 사용자의 의도가 무엇인지만 결정합니다. 

## <a name="clean-up-resources"></a>리소스 정리
더 이상 필요하지 않은 경우 LUIS 앱을 삭제합니다. 이렇게 하려면 앱 목록에서 앱 이름 오른쪽에 있는 3개 점 메뉴(...)를 선택하고 **삭제**를 선택합니다. **앱을 삭제하시겠습니까?** 팝업 대화 상자에서 **확인**을 선택합니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [정규식 엔터티를 추가하는 방법 알아보기](luis-quickstart-intents-regex-entity.md)

**숫자** [미리 빌드된 엔터티](luis-how-to-add-entities.md#add-prebuilt-entity)를 추가하여 숫자를 추출할 수 있습니다. 

<!--References-->
[LUIS]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions#luis-website
[LUIS-regions]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions#publishing-regions
