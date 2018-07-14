---
title: 구문 목록을 사용하여 LUIS 예측을 개선하는 방법의 자습서 - Azure | Microsoft Docs
description: 이 자습서에서는 LUIS 앱에 구문 목록을 추가하고 점수 개선을 확인합니다.
services: cognitive-services
author: v-geberr
manager: kamran.iqbal
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 05/07/2017
ms.author: v-geberr
ms.openlocfilehash: feb8acb674fd2dc62b62c26da6a6b42515f30242
ms.sourcegitcommit: 301855e018cfa1984198e045872539f04ce0e707
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/19/2018
ms.locfileid: "36265974"
---
# <a name="tutorial-add-phrase-list-to-improve-predictions"></a>자습서: 구문 목록을 추가하여 예측 개선
이 자습서에서는 의도 점수의 정확도를 개선하고, 서로 교환 가능한 [구문 목록 기능](./luis-concept-feature.md)을 추가하여 같은 의미를 가진 단어(동의어)의 엔터티를 식별합니다.

> [!div class="checklist"]
* 새 앱 가져오기  
* 알려진 발화를 사용하여 끝점 쿼리 
* ‘알 수 없는’ 발화를 사용하여 끝점 쿼리
* 구문 목록을 추가하여 알 수 없는 발화 점수 개선
* 구문 목록을 사용할 때 엔터티가 있는지 확인

이 문서에서는 LUIS 응용 프로그램을 작성하기 위해 체험 [LUIS][LUIS] 계정이 필요합니다.

## <a name="import-a-new-app"></a>새 앱 가져오기
1. 이 자습서용으로 디자인된 [예제 LUIS 앱][LuisSampleApp]을 다운로드합니다. 다음 단계에서 사용하게 됩니다. 

2. [앱 만들기](Create-new-app.md#import-new-app)에 설명된 대로 [LUIS][LUIS] 웹 사이트에 다운로드한 파일을 새 앱으로 가져옵니다. 앱 이름은 “내 구문 목록 자습서”입니다. 여기에는 의도, 엔터티 및 발화가 포함됩니다. 

3. 앱을 [학습](luis-how-to-train.md)시킵니다. 학습이 완료될 때까지 [LUIS][LUIS] 웹 사이트에서 [대화형으로 테스트](interactive-test.md#interactive-testing)할 수 없습니다. 

4. [게시](PublishApp.md) 페이지에서 **예측된 모든 의도 점수 포함** 확인란을 선택합니다. 확인란이 선택되면 모든 의도가 반환됩니다. 확인란이 선택 취소되면 상위 의도만 반환됩니다. 

5. 앱을 [게시](PublishApp.md)합니다. 앱을 게시하면 HTTPS 끝점을 사용하여 테스트할 수 있습니다. 

## <a name="test-a-trained-utterance"></a>학습된 발화 테스트
게시된 끝점을 사용하여 앱이 이미 인식하는 발화를 쿼리합니다. LUIS는 이미 발화를 인식하므로 점수가 높고 엔터티가 검색됩니다.

1. [Language Understanding(LUIS)][LUIS] 웹 사이트의 새 앱 **게시** 페이지에서 **리소스 및 키** 섹션에 있는 끝점 URL을 선택합니다. 

    ![끝점 URL 게시](./media/luis-tutorial-interchangeable-phrase-list/luis-publish-url.png)

2. 브라우저에서 URL 끝의 `q=` 뒤에 다음 쿼리를 추가합니다.

    `I want a computer replacement`

    끝점은 다음 JSON으로 응답합니다.
    
    ```JSON
    {
      "query": "I want a computer replacement",
      "topScoringIntent": {
        "intent": "GetHardware",
        "score": 0.9735458
      },
      "intents": [
        {
          "intent": "GetHardware",
          "score": 0.9735458
        },
        {
          "intent": "None",
          "score": 0.07053033
        },
        {
          "intent": "Whois",
          "score": 0.03760778
        },
        {
          "intent": "CheckCalendar",
          "score": 0.02285902
        },
        {
          "intent": "CheckInventory",
          "score": 0.0110072717
        }
      ],
      "entities": [
        {
          "entity": "computer",
          "type": "Hardware",
          "startIndex": 9,
          "endIndex": 16,
          "score": 0.8465915
        }
      ]
    }
    ```

    앱이 이 발화로 학습되었으므로 의도 점수 0.973 및 엔터티 검색 점수 0.846은 높습니다. 발화는 **GetHardware** 의도 페이지의 LUIS 앱에 있습니다. 발화 텍스트 `computer`는 **Hardware** 엔터티로 레이블이 지정됩니다. 
    
    |상태|Word| 의도 점수 | 엔터티 점수 |
    |--|--|--|--|
    |학습됨| want | 0.973 | 0.846 |
    
    
## <a name="test-an-untrained-utterance"></a>학습되지 않은 발화 테스트
브라우저에서 동일한 게시된 끝점을 사용하여 앱이 아직 인식하지 못하는 발화로 쿼리합니다.

`I require a computer replacement`

이 발화는 이전 발화의 동의어를 사용합니다.

| 학습된 단어 | 학습되지 않은 동의어 |
|--|--|
| want | require |

끝점 응답은 다음과 같습니다.

```JSON
{
  "query": "I require a computer replacement",
  "topScoringIntent": {
    "intent": "GetHardware",
    "score": 0.840912163
  },
  "intents": [
    {
      "intent": "GetHardware",
      "score": 0.840912163
    },
    {
      "intent": "None",
      "score": 0.0972757638
    },
    {
      "intent": "Whois",
      "score": 0.0448251367
    },
    {
      "intent": "CheckCalendar",
      "score": 0.0291390456
    },
    {
      "intent": "CheckInventory",
      "score": 0.0137837809
    }
  ],
  "entities": []
}
```

| 상태 | Word | 의도 점수 | 엔터티 점수 |
|--|--|--|--|
| 학습됨| want | 0.973 | 0.846 |
| 학습되지 않음| require | 0.840 | - |

LUIS는 문장의 문법이 동일하다고 인식하므로 학습되지 않은 발화 의도 점수가 레이블이 지정된 발화의 점수보다 낮습니다. 그러나 LUIS는 발화의 의미가 동일하다고 인식하지는 않습니다. 또한 구문 목록이 없으면 **Hardware** 엔터티를 찾을 수 없습니다.

한 단어에 두 개 이상의 의미가 있을 수 있으므로 ‘want’ 및 ‘require’가 이 앱 도메인에서 같은 것을 의미함을 LUIS에 학습시켜야 합니다. 

## <a name="improve-the-score-of-untrained-utterance-with-phrase-list"></a>구문 목록으로 학습되지 않은 발화의 점수 개선 
1. 값이 `want`인 **want**라는 [구문 목록](luis-how-to-add-features.md) 기능을 추가한 다음, **Enter** 키를 선택합니다.

    > [!TIP]
    > 각 단어 또는 구문 뒤에 **Enter** 키를 선택합니다. 커서가 **값** 상자에 있는 동안 단어 또는 구문이 **구문 목록 값** 상자에 추가됩니다. 이 기능을 사용하여 많은 값을 빠르게 입력할 수 있습니다.

2. LUIS가 추천하는 단어를 보려면 **권장**을 선택합니다. 

    ![권장 값](./media/luis-tutorial-interchangeable-phrase-list/recommend.png)

3. 모든 단어를 추가합니다. `require`가 권장 목록에 없으면 필수 값으로 추가합니다. 

4. 이러한 단어는 동의어이므로 ‘서로 교환 가능한’ 설정을 유지하고 **저장**을 선택합니다.

    ![구문 목록 값](./media/luis-tutorial-interchangeable-phrase-list/phrase-list-values.png)

5. 위쪽 탐색 모음에서 **학습**을 선택하여 앱을 학습시키지만 게시하지는 않습니다. 이제 두 개의 모델이 있습니다. 두 모델의 값을 비교할 수 있습니다.

## <a name="compare-the-phrase-list-model-to-the-published-model"></a>구문 목록 모델을 게시된 모델과 비교
이 앱에서 게시된 모델은 동의어로 학습되지 않았습니다. 현재 편집된 모델에만 동의어의 구문 목록이 포함됩니다. 모델을 비교하려면 [대화형 테스트](interactive-test.md#interactive-testing)를 사용합니다. 

1. **테스트** 창을 열고 다음 발화를 입력합니다.

    `I require a computer replacement`

2. 검사 패널을 열려면 **검사**를 선택합니다. 

    ![검사 선택](./media/luis-tutorial-interchangeable-phrase-list/inspect-button.png)

3. 게시된 모델을 새 구문 목록 모델에 비교하려면 **게시된 버전과 비교**를 선택합니다.

    ![게시된 버전 및 현재 버전 검사](./media/luis-tutorial-interchangeable-phrase-list/inspect.png)

구문 목록을 추가한 후 발화 및 **하드웨어** 엔터티의 정확도가 높아진 것을 알 수 있습니다. 

|상태 | 구문 목록| 의도 점수 | 엔터티 점수 |
|--|--|--|--|
| 게시됨 | - | 0.84 | - |
| 현재 편집 중 |✔| 0.92 | 하드웨어 엔터티가 식별됨 |

> [!TIP]
> * [대화형 테스트](interactive-test.md#interactive-testing)를 사용하면 게시한 후에 적용된 모든 학습된 변경 내용과 게시된 모델을 비교할 수 있습니다. 
> * [끝점 테스트](PublishApp.md#test-your-published-endpoint-in-a-browser)를 사용하여 정확한 LUIS 응답 JSON을 볼 수 있습니다. 

## <a name="get-the-entity-score-with-the-endpoint-test"></a>끝점 테스트로 엔터티 점수 가져오기
엔터티 점수를 보려면 [모델을 게시](PublishApp.md)하고 끝점을 쿼리합니다. 

`I require a computer replacement`

```JSON
{
  "query": "I require a computer replacement",
  "topScoringIntent": {
    "intent": "GetHardware",
    "score": 0.916503668
  },
  "intents": [
    {
      "intent": "GetHardware",
      "score": 0.916503668
    },
    {
      "intent": "None",
      "score": 0.136505231
    },
    {
      "intent": "Whois",
      "score": 0.02778677
    },
    {
      "intent": "CheckInventory",
      "score": 0.0144592477
    },
    {
      "intent": "CheckCalendar",
      "score": 0.01401332
    }
  ],
  "entities": [
    {
      "entity": "computer",
      "type": "Hardware",
      "startIndex": 12,
      "endIndex": 19,
      "score": 0.5959917
    }
  ]
}
```

**Hardware** 엔터티는 구문 목록을 사용하여 0.595 점수를 보여줍니다. 구문 목록이 사용되기 전에는 엔터티가 검색되지 않았습니다. 

|상태 | 구문 목록| 의도 점수 | 엔터티 점수 |
|--|--|--|--|
| 게시됨 | - | 0.84 | - |
| 현재 편집 중 |✔| 0.92 | 0.595 |


## <a name="clean-up-resources"></a>리소스 정리
더 이상 필요하지 않은 경우 LUIS 앱을 삭제합니다. 이렇게 하려면 앱 목록에서 앱 이름 오른쪽에 있는 3개 점 메뉴(...)를 선택하고 **삭제**를 선택합니다. **앱을 삭제하시겠습니까?** 팝업 대화 상자에서 **확인**을 선택합니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [끝점 쿼리로 발화 예측 가져오기](luis-get-started-cs-get-intent.md)

[LUIS]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions
[LuisFeatures]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-concept-feature
[LuisSampleApp]: https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/phrase_list/interchangeable/luis-app-before-phrase-list.json
