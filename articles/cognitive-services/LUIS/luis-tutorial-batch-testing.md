---
title: 일괄 테스트를 사용하여 LUIS 예측 개선 | Microsoft Docs
titleSuffix: Azure
description: 일괄 테스트를 로드하고, 결과를 검토하며, 변경 사항으로 LUIS 예측을 개선합니다.
services: cognitive-services
author: v-geberr
manager: kamran.iqbal
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 03/19/2018
ms.author: v-geberr
ms.openlocfilehash: 5788f17f2724a0354a1db506971c2343c1800f01
ms.sourcegitcommit: 301855e018cfa1984198e045872539f04ce0e707
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/19/2018
ms.locfileid: "36266399"
---
# <a name="use-batch-testing-to-find-prediction-accuracy-issues"></a>일괄 테스트를 사용하여 예측 정확도 문제를 찾습니다.

이 자습서에서는 일괄 테스트를 사용하여 발화 예측 문제를 찾는 방법을 보여 줍니다.  

이 자습서에서는 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
* 일괄 테스트 파일 만들기 
* 일괄 테스트 실행
* 테스트 결과 검토
* 의도에 대한 오류 수정
* 다시 일괄 테스트

## <a name="prerequisites"></a>필수 조건

> [!div class="checklist"]
> * 이 문서에서 LUIS 응용 프로그램을 작성하려면 [LUIS][LUIS] 계정도 필요합니다.

> [!Tip]
> 아직 구독이 없는 경우 [체험 계정](https://azure.microsoft.com/free/)으로 등록할 수 있습니다.

## <a name="create-new-app"></a>새 앱 만들기
이 문서에서는 미리 빌드된 도메인 HomeAutomation을 사용합니다. 미리 빌드된 도메인에는 조명과 같은 HomeAutomation 장치를 제어하기 위한 의도, 엔터티 및 발화가 있습니다. 앱을 만들고, 도메인을 추가하고, 학습하고, 게시합니다.

1. [LUIS] 웹 사이트의 **MyApps** 페이지에서 **새 앱 만들기**를 선택하여 새 앱을 만듭니다. 

    ![새 앱 만들기](./media/luis-tutorial-batch-testing/create-app-1.png)

2. 대화 상자에 `Batchtest-HomeAutomation` 이름을 입력합니다.

    ![앱 이름 입력](./media/luis-tutorial-batch-testing/create-app-2.png)

3. 왼쪽 아래에서 **미리 빌드된 도메인**을 선택합니다. 

    ![미리 빌드된 도메인 선택](./media/luis-tutorial-batch-testing/prebuilt-domain-1.png)

4. HomeAutomation에 대해 **도메인 추가**를 선택합니다.

    ![HomeAutomation 도메인 추가](./media/luis-tutorial-batch-testing/prebuilt-domain-2.png)

5. 오른쪽 위 탐색 모음에서 **학습**을 선택합니다.

    ![학습 단추 선택](./media/luis-tutorial-batch-testing/train-button.png)

## <a name="batch-test-criteria"></a>일괄 테스트 기준
일괄 테스트에서는 한 번에 최대 1000개의 발화를 테스트할 수 있습니다. 일괄 처리에 중복이 없어야 합니다. 현재 발화 목록을 보려면 앱을 [내보냅니다](create-new-app.md#export-app).  

LUIS에 대한 테스트 전략에서는 모델 발화, 일괄 테스트 발화 및 끝점 발화의 세 가지 개별 데이터 집합을 사용합니다. 이 자습서에서는 모델 발화(의도에 추가됨)나 끝점 발화의 발화를 사용하고 있지 않은지 확인합니다. 

앱에 이미 있는 발화를 일괄 테스트에 사용하지 마세요.

```
'breezeway on please',
'change temperature to seventy two degrees',
'coffee bar on please',
'decrease temperature for me please',
'dim kitchen lights to 25 .',
'fish pond off please',
'fish pond on please',
'illuminate please',
'living room lamp on please',
'living room lamps off please',
'lock the doors for me please',
'lower your volume',
'make camera 1 off please',
'make some coffee',
'play dvd',
'set lights bright',
'set lights concentrate',
'set lights out bedroom',
'shut down my work computer',
'silence the phone',
'snap switch fan fifty percent',
'start master bedroom light .',
'theater on please',
'turn dimmer off',
'turn off ac please',
'turn off foyer lights',
'turn off living room light',
'turn off staircase',
'turn off venice lamp',
'turn on bathroom heater',
'turn on external speaker',
'turn on my bedroom lights .',
'turn on the furnace room lights',
'turn on the internet in my bedroom please',
'turn on thermostat please',
'turn the fan to high',
'turn thermostat on 70 .' 
```

## <a name="create-a-batch-to-test-intent-prediction-accuracy"></a>의도 예측 정확도를 테스트하는 일괄 처리 만들기
1. [VSCode](https://code.visualstudio.com/)와 같은 텍스트 편집기에서 `homeauto-batch-1.json`을 만듭니다. 

2. 테스트에서 예측할 **의도**가 포함된 발화를 추가합니다. 이 자습서에서 간단하게 만들기 위해 `HomeAutomation.TurnOn` 및 `HomeAutomation.TurnOff`의 발화를 사용하고 발화에서 `on` 및 `off` 텍스트를 전환합니다. `None` 의도의 경우 [도메인](luis-glossary.md#domain)(주제) 영역에 속하지 않는 몇 가지 발화를 추가합니다. 

    일괄 테스트 결과가 일괄 처리 JSON과 어떻게 상호 연관되는지 이해하려면 의도를 6개만 추가하세요.

    ```JSON
    [
        {
          "text": "lobby on please",
          "intent": "HomeAutomation.TurnOn",
          "entities": []
        },
        {
          "text": "change temperature to seventy one degrees",
          "intent": "HomeAutomation.TurnOn",
          "entities": []
        },
        {
          "text": "where is my pizza",
          "intent": "None",
          "entities": []
        },
        {
          "text": "help",
          "intent": "None",
          "entities": []
        },
        {
          "text": "breezeway off please",
          "intent": "HomeAutomation.TurnOff",
          "entities": []
        },
        {
          "text": "coffee bar off please",
          "intent": "HomeAutomation.TurnOff",
          "entities": []
        }
    ]
    ```

## <a name="run-the-batch"></a>일괄 처리 실행
1. 맨 위 탐색 모음에서 **테스트**를 선택합니다. 

    ![탐색 모음에서 테스트 선택](./media/luis-tutorial-batch-testing/test-1.png)

2. 오른쪽 패널에서 **Batch testing panel**(일괄 테스트 패널)을 선택합니다. 

    ![일괄 테스트 패널 선택](./media/luis-tutorial-batch-testing/test-2.png)

3. **데이터 집합 가져오기**를 선택합니다.

    ![데이터 집합 가져오기 선택](./media/luis-tutorial-batch-testing/test-3.png)

4. `homeauto-batch-1.json` 파일의 파일 시스템 위치를 선택합니다.

5. 데이터 집합 이름을 `set 1`로 지정합니다.

    ![파일 선택](./media/luis-tutorial-batch-testing/test-4.png)

6. **실행** 단추를 선택합니다. 테스트가 완료될 때까지 기다립니다.

    ![실행 선택](./media/luis-tutorial-batch-testing/test-5.png)

7. **See results**(결과 보기)를 선택합니다.

    ![결과 보기](./media/luis-tutorial-batch-testing/test-6.png)

8. 그래프 및 범례에서 결과를 검토합니다.

    ![일괄 처리 결과](./media/luis-tutorial-batch-testing/batch-result-1.png)

## <a name="review-batch-results"></a>일괄 처리 결과 검토
일괄 처리 결과는 두 개의 섹션에 있습니다. 맨 위 섹션에는 그래프와 범례가 있습니다. 그래프의 영역 이름을 선택하면 맨 아래 섹션에 발화가 표시됩니다.

모든 오류는 빨간색으로 표시됩니다. 그래프에는 네 개의 섹션이 있는데, 그중 두 개의 섹션이 빨간색으로 표시됩니다. **이러한 섹션에 집중해야 합니다**. 

오른쪽 위 섹션은 테스트에서 의도 또는 엔터티의 존재를 잘못 예측했음을 나타냅니다. 왼쪽 아래 섹션은 테스트에서 의도 또는 엔터티의 부재를 잘못 예측했음을 나타냅니다.

### <a name="homeautomationturnoff-test-results"></a>HomeAutomation.TurnOff 테스트 결과
범례에서 `HomeAutomation.TurnOff` 의도를 선택합니다. 범례에서 이름 왼쪽에 녹색 성공 아이콘이 있습니다. 이 의도에 대한 오류가 없습니다. 

![일괄 처리 결과](./media/luis-tutorial-batch-testing/batch-result-1.png)

### <a name="homeautomationturnon-and-none-intents-have-errors"></a>HomeAutomation.TurnOn 및 None 의도에 오류가 있음
다른 두 의도에 오류가 있습니다. 즉, 테스트 예측이 일괄 처리 파일 기대치와 일치하지 않음을 의미합니다. 범례에서 `None` 의도를 선택하여 첫 번째 오류를 검토합니다. 

![None 의도](./media/luis-tutorial-batch-testing/none-intent-failures.png)

오류는 차트에서 **가양성** 및 **가음성**이라는 빨간색 섹션에 나타납니다. 차트에서 **가음성** 섹션 이름을 선택하여 차트에서 실패한 발화를 확인합니다. 

![가음성 오류](./media/luis-tutorial-batch-testing/none-intent-false-negative.png)

실패한 발화 `help`는 `None` 의도로 예상되었지만 테스트에서는 `HomeAutomation.TurnOn` 의도를 예측했습니다.  

HomeAutomation.TurnOn과 None에 각각 하나씩 두 개의 오류가 있습니다. 둘 다 `help` 발화에 의해 발생되었는데, 이 발화가 None의 기대치를 충족하지 못하고 HomeAutomation.TurnOn 의도와 예기치 않게 일치했기 때문입니다. 

`None` 발화가 실패하는 이유를 확인하려면 현재 `None`에 있는 발화를 검토하세요. 

## <a name="review-none-intents-utterances"></a>None 의도의 발화 검토

1. 맨 위 탐색 모음에서 **테스트** 단추를 선택하여 **테스트** 패널을 닫습니다. 

2. 맨 위 탐색 패널에서 **빌드**를 선택합니다. 

3. 의도 목록에서 **None** 의도를 선택합니다.

4. Control+E를 선택하여 발화의 토큰 보기 확인 
    
    |None 의도의 발화|예측 점수|
    |--|--|
    |“decrease temperature for me please”|0.44|
    |“dim kitchen lights to 25.”|0.43|
    |“lower your volume”|0.46|
    |“turn on the internet in my bedroom please”|0.28|

## <a name="fix-none-intents-utterances"></a>None 의도의 발화 수정
    
`None`의 모든 발화는 앱 도메인 외부에 있어야 합니다. 이러한 발화는 HomeAutomation에 상대적이므로 잘못된 의도입니다. 

또한 LUIS는 50% 미만(<.50)의 예측 점수를 발화에 제공합니다. 다른 두 의도의 발화를 살펴보면 훨씬 더 높은 예측 점수가 표시됩니다. 예를 들어, LUIS에서 발화에 대한 점수가 낮은 경우, 발화가 LUIS에서 현재 의도와 다른 의도 간에 혼동되고 있다는 좋은 표시입니다. 

앱을 수정하려면 현재 `None` 의도에 있는 발화를 올바른 의도로 이동해야 하며 `None` 의도에는 적절한 새 의도가 필요합니다. 

`None` 의도에 있는 발화 중 세 개는 자동화 장치 설정을 낮추기 위한 것입니다. 이러한 발화는 `dim`, `lower` 또는 `decrease` 같은 단어를 사용합니다. 네 번째 발화는 인터넷을 켜도록 요청합니다. 네 발화 모두 장치를 켜거나 장치의 전원 수준을 변경하려는 것이므로 `HomeAutomation.TurnOn` 의도로 이동해야 합니다. 

이는 단 하나의 솔루션입니다. `ChangeSetting`이라는 새 의도를 만들고 dim, lower 및 decrease를 사용하는 발화를 새 의도로 이동할 수도 있습니다. 

## <a name="fix-the-app-based-on-batch-results"></a>일괄 처리 결과를 기반으로 앱 수정
네 개의 발화를 `HomeAutomation.TurnOn` 의도로 이동합니다. 

1. 발화 목록 위의 확인란을 선택하여 모든 발화를 선택합니다. 

2. **의도 다시 할당** 드롭다운에서 `HomeAutomation.TurnOn`을 선택합니다. 

    ![발화 이동](./media/luis-tutorial-batch-testing/move-utterances.png)

    네 개의 발화를 다시 할당하고 나면 `None` 의도에 대한 발화 목록이 비어 있습니다.

3. None 의도에 대해 네 개의 새 의도를 추가합니다.

    ```
    "fish"
    "dogs"
    "beer"
    "pizza"
    ```

    이러한 발화는 확실히 HomeAutomation의 도메인 외부에 있습니다. 각 발화를 입력하면서 해당 점수를 확인합니다. 점수가 낮거나 매우 낮을 수 있습니다(주위에 빨간색 상자 표시). 앱을 학습하면 8단계에서 점수가 훨씬 더 높아집니다. 

7. 발화에서 파란색 레이블을 선택하고 **Remove label**(레이블 제거)을 선택하여 레이블을 제거합니다.

8. 오른쪽 위 탐색 모음에서 **학습**을 선택합니다. 각 발화의 점수가 훨씬 더 높아집니다. 이제 `None` 의도에 대한 모든 점수가 .80보다 높아야 합니다. 

## <a name="verify-the-fix-worked"></a>수정되었는지 확인
일괄 테스트의 발화가 **None** 의도에 대해 올바르게 예측되는지 확인하려면 일괄 테스트를 다시 실행합니다.

1. 맨 위 탐색 모음에서 **테스트**를 선택합니다. 

2. 오른쪽 패널에서 **Batch testing panel**(일괄 테스트 패널)을 선택합니다. 

3. 일괄 처리 이름 오른쪽에 있는 세 개의 점(...)을 선택하고 **Run Dataset**(데이터 집합 실행)을 선택합니다. 일괄 테스트가 완료될 때까지 기다립니다.

    ![데이터 집합 실행](./media/luis-tutorial-batch-testing/run-dataset.png)

4. **See results**(결과 보기)를 선택합니다. 모든 의도의 이름 왼쪽에 녹색 아이콘이 있어야 합니다. 올바른 필터를 `HomeAutomation.Turnoff` 의도로 설정하고 차트 중간에 가장 가까운 오른쪽 위 패널에서 녹색 점을 선택합니다. 발화의 이름이 차트 아래의 표에 표시됩니다. `breezeway off please`의 점수가 매우 낮습니다. 선택적으로 이 점수를 높이려면 의도에 발화를 더 추가합니다. 

    ![데이터 집합 실행](./media/luis-tutorial-batch-testing/turnoff-low-score.png)

<!--
    The Entities section of the legend may have errors. That is the next thing to fix.

## Create a batch to test entity detection
1. Create `homeauto-batch-2.json` in a text editor such as [VSCode](https://code.visualstudio.com/). 

2. Utterances have entities identified with `startPos` and `endPost`. These two elements identify the entity before [tokenization](luis-glossary.md#token), which happens in some [cultures](luis-supported-languages.md#tokenization) in LUIS. If you plan to batch test in a tokenized culture, learn how to [extract](luis-concept-data-extraction.md#tokenized-entity-returned) the non-tokenized entities.

    Copy the following JSON into the file:

    ```JSON
    [
        {
          "text": "lobby on please",
          "intent": "HomeAutomation.TurnOn",
          "entities": [
            {
              "entity": "HomeAutomation.Room",
              "startPos": 0,
              "endPos": 4
            }
          ]
        },
        {
          "text": "change temperature to seventy one degrees",
          "intent": "HomeAutomation.TurnOn",
          "entities": [
            {
              "entity": "HomeAutomation.Operation",
              "startPos": 7,
              "endPos": 17
            }
          ]
        },
        {
          "text": "where is my pizza",
          "intent": "None",
          "entities": []
        },
        {
          "text": "help",
          "intent": "None",
          "entities": []
        },
        {
          "text": "breezeway off please",
          "intent": "HomeAutomation.TurnOff",
          "entities": [
            {
              "entity": "HomeAutomation.Room",
              "startPos": 0,
              "endPos": 9
            }
          ]
        },
        {
          "text": "coffee bar off please",
          "intent": "HomeAutomation.TurnOff",
          "entities": [
            {
              "entity": "HomeAutomation.Device",
              "startPos": 0,
              "endPos": 10
            }
          ]
        }
      ]
    ```

3. Import the batch file, following the [same instructions](#run-the-batch) as the first import, and name the dataset `set 2`. Run the test.

## Possible entity errors
Since the intents in the right-side filter of the test panel still pass the test, this section focuses on correct entity identification. 

Entity testing is diferrent than intents. An utterance will have only one top scoring intent, but it may have several entities. An utterance's entity may be correctly identified, may be incorrectly identified as an entity other than the one in the batch test, may overlap with other entities, or not identified at all. 

## Review entity errors
1. Select `HomeAutomation.Device` in the filter panel. The chart changes to show a single false positive and several true negatives. 

2. Select the False positive section name. The utterance for this chart point is displayed below the chart. The labeled intent and the predicted intent are the same, which is consistent with the test -- the intent prediction is correct. 

    The issue is that the HomeAutomation.Device was detected but the batch expected HomeAutomation.Room for the utterance "coffee bar off please". `Coffee bar` could be a room or a device, depending on the environment and context. As the model designer, you can either enforce the selection as `HomeAutomation.Room` or change the batch file to use `HomeAutomation.Device`. 

    If you want to reinforce that coffee bar is a room, you nee to add an utterances to LUIS that help LUIS decide a coffee bar is a room. 

    The most direct route is to add the utterance to the intent but that to add the utterance for every entity detection error is not the machine-learned solution. Another fix would be to add an utterance with `coffee bar`.

## Add utterance to help extract entity
1. Select the **Test** button on the top navigation to close the batch test panel.

2. On the `HomeAutomation.TurnOn` intent, add the utterance, `turn coffee bar on please`. The uttterance should have all three entities detected after you select enter. 

3. Select **Train** on the top navigation panel. Wait until training completes successfully.

3. Select **Test** on the top navigation panel to open the Batch testing pane again. 

4. If the list of datasets is not visible, select **Back to list**. Select the three dots (...) at the end of `Set 2` and select `Run Dataset`. Wait for the test to complete.

5. Select **See results** to review the test results.

6. 
-->
## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [예제 발화에 대한 자세한 정보](luis-how-to-add-example-utterances.md)

[LUIS]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions