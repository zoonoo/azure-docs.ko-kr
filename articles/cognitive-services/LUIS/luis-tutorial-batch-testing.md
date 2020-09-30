---
title: '자습서: 문제를 찾기 위한 일괄 처리 테스트 - LUIS'
description: 이 자습서에서는 일괄 처리 테스트를 사용하여 LUIS(Language Understanding) 앱의 품질에 대한 유효성을 검사하는 방법을 보여 줍니다.
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 05/07/2020
ms.openlocfilehash: 7dd181f8cd398dd683296b446028b398a9800b53
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/25/2020
ms.locfileid: "91298320"
---
# <a name="tutorial-batch-test-data-sets"></a>자습서: 일괄 처리 테스트 데이터 세트

이 자습서에서는 일괄 처리 테스트를 사용하여 LUIS(Language Understanding) 앱의 품질에 대한 유효성을 검사하는 방법을 보여 줍니다.

일괄 테스트를 사용하면 레이블이 지정된 발언 및 엔터티의 알려진 집합을 사용하여 학습된 활성 모델의 상태가 유효한지 확인할 수 있습니다. JSON 형식의 일괄 처리 파일에서 발언을 추가하고, 발언 내에서 예측해야 하느 엔터티 레이블을 설정합니다.

일괄 테스트를 위한 요구 사항:

* 테스트당 최대 1000개 발언
* 중복되지 않습니다.
* 허용되는 엔터티 형식: 기계 학습된 엔터티만

이 자습서 이외의 앱을 사용하는 경우 이미 앱에 추가된 발화 예제를 사용하지 *마세요*.

**이 자습서에서 학습할 내용은 다음과 같습니다.**

<!-- green checkmark -->
> [!div class="checklist"]
> * 예제 앱 가져오기
> * 일괄 테스트 파일 만들기
> * 일괄 테스트 실행
> * 테스트 결과 검토

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="import-example-app"></a>예제 앱 가져오기

`1 pepperoni pizza on thin crust`와 같은 피자 주문을 받는 앱을 가져옵니다.

1.  [앱 JSON 파일](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/luis/apps/pizza-with-machine-learned-entity.json?raw=true)을 다운로드하고 저장합니다.

1. [LUIS 포털](https://www.luis.ai)에 로그인하고 **구독** 및 **제작 리소스**를 선택하여 해당 제작 리소스에 할당된 앱을 확인합니다.
1. JSON을 새 앱으로 가져오고 앱 이름을 `Pizza app`으로 지정합니다.


1. 탐색 영역의 오른쪽 위 모서리에서 **학습**을 선택하여 앱을 학습시킵니다.

## <a name="what-should-the-batch-file-utterances-include"></a>배치 파일 발화에 포함되는 내용

배치 파일에는 시작 및 끝 위치를 포함하여 레이블이 지정된 최상위 수준의 기계 학습 엔터티가 있는 발화가 포함되어야 합니다. 발화는 이미 앱에 있는 예제의 일부가 아니어야 합니다. 의도 또는 엔터티에 대해 긍정적으로 예측하려는 발화여야 합니다.

의도 및/또는 엔터티별로 테스트를 분리하거나 모든 테스트(최대 1,000개 발화)를 동일한 파일에 포함할 수 있습니다.

## <a name="batch-file"></a>일괄 처리 파일

JSON 예제에는 테스트 파일의 모양을 설명하는 레이블이 지정된 하나의 엔터티가 있는 하나의 발화가 포함되어 있습니다. 사용자 고유의 테스트에는 올바른 의도 및 레이블이 지정된 기계 학습 엔터티가 포함된 발화가 많이 있어야 합니다.

1. 텍스트 편집기에서 `pizza-with-machine-learned-entity-test.json`을 만들거나 [다운로드](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/luis/batch-tests/pizza-with-machine-learned-entity-test.json?raw=true)합니다.

2. JSON 형식의 배치 파일에서 테스트에서 예측하려는 **의도**가 포함된 발화를 추가합니다.

   [!code-json[Add the intents to the batch test file](~/samples-cognitive-services-data-files/luis/batch-tests/pizza-with-machine-learned-entity-test.json "Add the intent to the batch test file")]

## <a name="run-the-batch"></a>일괄 처리 실행

1. 맨 위 탐색 모음에서 **테스트**를 선택합니다.

2. 오른쪽 패널에서 **Batch testing panel**(일괄 테스트 패널)을 선택합니다.

3. **데이터 세트 가져오기**를 선택합니다.

    > [!div class="mx-imgBorder"]
    > ![데이터 세트 가져오기가 강조 표시된 LUIS 앱의 스크린샷](./media/luis-tutorial-batch-testing/import-dataset-button.png)

4. `pizza-with-machine-learned-entity-test.json` 파일의 파일 위치를 선택합니다.

5. 데이터 세트의 이름을 `pizza test`로 지정하고 **완료**를 선택합니다.

    > [!div class="mx-imgBorder"]
    > ![파일 선택](./media/luis-tutorial-batch-testing/import-dataset-modal.png)

6. **실행** 단추를 선택합니다.

7. **See results**(결과 보기)를 선택합니다.

8. 그래프 및 범례에서 결과를 검토합니다.

## <a name="review-batch-results-for-intents"></a>의도에 대한 일괄 처리 결과 검토

테스트 결과는 활성 버전에 대해 테스트 발화가 예측된 방법을 그래픽으로 보여 줍니다.

일괄 처리 차트는 결과의 사분면으로 표시됩니다. 차트의 오른쪽이 필터입니다. 필터에는 의도와 엔터티가 포함됩니다. [차트의 섹션](luis-concept-batch-test.md#batch-test-results) 또는 차트 내의 한 점을 선택하면 차트 아래에 관련된 발언이 표시됩니다.

차트 위로 마우스를 가져간 후 마우스 휠로 차트의 발언을 확대하거나 축소할 수 있습니다. 차트에 많은 점이 밀집되어 있을 때 이렇게 하면 편리합니다.

차트는 사분면으로 구분되며 그중 두 개의 섹션이 빨간색으로 표시됩니다.

1. 필터 목록에서 **ModifyOrder** 의도를 선택합니다.

    > [!div class="mx-imgBorder"]
    > ![필터 목록에서 ModifyOrder 의도 선택](./media/luis-tutorial-batch-testing/select-intent-from-filter-list.png)

    발화는 **진양성**으로 예측됩니다. 즉 발화가 배치 파일에 나열된 긍정 예측과 일치합니다.

    > [!div class="mx-imgBorder"]
    > ![긍정 예측과 성공적으로 일치하는 발화](./media/luis-tutorial-batch-testing/intent-predicted-true-positive.png)

    필터 목록의 녹색 확인 표시도 각 의도에 대한 테스트의 성공을 나타냅니다. 각 의도에 대해 발화가 일괄 처리 테스트에 나열되지 않은 의도에 대한 부정 테스트로 테스트되었으므로 다른 모든 의도는 1/1 긍정 점수로 나열됩니다.

1. **확인** 의도를 선택합니다. 이 의도는 일괄 처리 테스트에 나열되지 않으므로 일괄 처리 테스트에 나열된 발화에 대한 부정 테스트입니다.

    > [!div class="mx-imgBorder"]
    > ![배치 파일에 나열되지 않은 의도에 대해 성공적으로 부정으로 예측된 발화](./media/luis-tutorial-batch-testing/true-negative-intent.png)

    필터의 녹색 텍스트와 그리드에서 표시한 대로 부정 테스트에 성공했습니다.

## <a name="review-batch-test-results-for-entities"></a>엔터티에 대한 일괄 처리 테스트 결과 검토

하위 엔터티가 있는 머신 엔터티인 ModifyOrder 엔터티는 최상위 엔터티가 일치하는지 여부를 표시하고 하위 엔터티를 예측하는 방법을 표시합니다.

1. 필터 목록에서 **ModifyOrder** 엔터티를 선택한 다음, 그리드에서 원을 선택합니다.

1. 엔터티 예측이 차트 아래에 표시됩니다. 표시에는 예상과 일치하는 예측의 실선 및 예상과 일치하지 않는 예측의 점선이 포함됩니다.

    > [!div class="mx-imgBorder"]
    > ![배치 파일에서 성공적으로 예측된 엔터티 부모 항목](./media/luis-tutorial-batch-testing/labeled-entity-prediction.png)

## <a name="finding-errors-with-a-batch-test"></a>일괄 처리 테스트에서 오류 찾기

이 자습서에서는 테스트를 실행하고 결과를 해석하는 방법을 살펴보았습니다. 테스트 원칙 또는 실패한 테스트에 응답하는 방법은 다루지 않았습니다.

* 테스트하는 경우 다르지만 관련된 의도에 대해 예측할 수 있는 발화를 포함하여 긍정 또는 부정 발화를 모두 포함해야 합니다.
* 실패한 발화의 경우 다음 작업을 수행한 다음, 테스트를 다시 실행합니다.
    * 의도와 엔터티에 대한 현재 예제를 검토하고, 활성 버전의 발화 예제가 의도 및 엔터티 레이블 지정에 모두 올바른지 확인합니다.
    * 앱에서 의도와 엔터티를 예측하는 데 도움이 되는 기능을 추가합니다.
    * 더 많은 긍정 발화 예제를 추가합니다.
    * 의도 전체에 걸친 발화 예제의 균형을 검토합니다.

## <a name="clean-up-resources"></a>리소스 정리

[!INCLUDE [LUIS How to clean up resources](./includes/cleanup-resources-preview-portal.md)]

## <a name="next-step"></a>다음 단계

이 자습서에서는 일괄 처리 테스트를 사용하여 현재 모델의 유효성을 검사했습니다.

> [!div class="nextstepaction"]
> [패턴에 대해 알아보기](luis-tutorial-pattern.md)

