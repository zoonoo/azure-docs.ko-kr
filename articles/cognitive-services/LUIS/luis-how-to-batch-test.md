---
title: 1000개의 예제 발화를 사용한 일괄 테스트
titleSuffix: Azure Cognitive Services
description: LUIS(Language Understanding) 일괄 테스트 집합을 사용하여 잘못된 의도 및 엔터티가 있는 발화를 찾습니다.
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 09/06/2018
ms.author: diberry
ms.openlocfilehash: 1560b8e574b0932e258353648c73f030ef19d314
ms.sourcegitcommit: 922f7a8b75e9e15a17e904cc941bdfb0f32dc153
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/27/2018
ms.locfileid: "52333499"
---
# <a name="batch-testing-with-a-set-of-example-utterances"></a>예제 발화 집합으로 일괄 테스트

 일괄 테스트는 현재 학습된 모델의 포괄적인 테스트로, LUIS에서 성능을 측정할 수 있습니다. 

<a name="batch-testing"></a>

## <a name="import-a-dataset-file-for-batch-testing"></a>일괄 테스트용 데이터 세트 파일 가져오기

1. 위쪽 막대에서 **테스트**를 선택한 다음, **일괄 테스트 패널**을 선택합니다.

    ![일괄 테스트 링크](./media/luis-how-to-batch-test/batch-testing-link.png)

2. **데이터 집합 가져오기**를 선택합니다. **새 데이터 집합 가져오기** 대화 상자가 표시됩니다. **파일 선택**을 선택하고 테스트할 *1,000개 이하* 발언을 포함하는 올바른 [JSON 형식](luis-concept-batch-test.md#batch-file-format)의 JSON 파일을 찾습니다.

    가져오기 오류는 브라우저 위쪽의 빨간색 알림 표시줄에 보고됩니다. 가져오기에 오류가 있으면 데이터 세트가 생성되지 않습니다. 자세한 내용은 [일반 오류](luis-concept-batch-test.md#common-errors-importing-a-batch)를 참조하세요.

3. **데이터 집합 이름** 필드에 데이터 집합 파일의 이름을 입력합니다. 데이터 세트 파일에는 ‘레이블이 지정된 의도’ 및 ‘엔터티’를 포함하는 **발화 배열**이 포함되어 있습니다. 구문의 [예제 일괄 처리 파일](luis-concept-batch-test.md#batch-file-format)을 검토합니다. 

4. **완료**를 선택합니다. 데이터 세트 파일이 추가됩니다.

## <a name="run-rename-export-or-delete-dataset"></a>데이터 세트 실행, 이름 바꾸기, 내보내기 또는 삭제

데이터 세트를 실행하거나, 이름을 바꾸거나, 내보내거나 삭제하려면 데이터 세트 행 끝에 있는 줄임표(***...***) 단추를 사용합니다.

![데이터 세트 작업](./media/luis-how-to-batch-test/batch-testing-options.png)

## <a name="run-a-batch-test-on-your-trained-app"></a>학습된 앱에서 일괄 테스트 실행

테스트를 실행하려면 데이터 세트 이름을 선택합니다. 테스트가 완료되면 이 행에 데이터 세트의 테스트 결과가 표시됩니다.

![일괄 테스트 결과](./media/luis-how-to-batch-test/run-test.png)

다운로드 가능한 데이터 세트는 일괄 테스트용으로 업로드된 파일과 동일합니다.

|시스템 상태|의미|
|--|--|
|![테스트 성공 녹색 원 아이콘](./media/luis-how-to-batch-test/batch-test-result-green.png)|모든 발화가 성공했습니다.|
|![테스트 실패 빨간색 x 아이콘](./media/luis-how-to-batch-test/batch-test-result-red.png)|하나 이상의 발화 의도가 예측과 일치하지 않습니다.|
|![테스트 준비 완료 아이콘](./media/luis-how-to-batch-test/batch-test-result-blue.png)|테스트를 실행할 준비가 되었습니다.|

<a name="access-batch-test-result-details-in-a-visualized-view"></a>

## <a name="view-batch-test-results"></a>일괄 테스트 결과 보기 

일괄 테스트 결과를 검토하려면 **결과 보기**를 선택합니다.

![일괄 테스트 결과](./media/luis-how-to-batch-test/run-test-results.png)

<!-- Select the **See results** link that appears after you run the test. A scatter graph known as an error matrix displays. The data points represent the utterances in the dataset. Green points indicate correct prediction, and red ones indicate incorrect prediction. The filtering panel on the right side of the screen displays a list of all intents and entities in the app, with a green point for intents/entities that were predicted correctly in all dataset utterances, and a red point for those items with errors. Also, for each intent/entity, you can see the number of correct predictions out of the total utterances.-->


<a name="filter-chart-results-by-intent-or-entity"></a>  

## <a name="filter-chart-results"></a>차트 결과 필터링

특정 의도 또는 엔터티별로 차트를 필터링하려면 오른쪽 필터링 패널에서 의도 또는 엔터티를 선택합니다. 선택 항목에 따라 그래프에서 데이터 요소 및 해당 배포가 업데이트됩니다. 
 
![시각화된 일괄 테스트 결과](./media/luis-how-to-batch-test/filter-by-entity.png) 

## <a name="view-single-point-utterance-data"></a>단일 지점 발화 데이터 보기

차트에서 데이터 요소를 마우스로 가리키면 해당 예측의 확신도 점수가 표시됩니다. 데이터 요소를 선택하여 페이지 아래쪽의 발화 목록에서 해당 발화를 검색합니다. 

![선택한 발화](./media/luis-how-to-batch-test/selected-utterance.png)


<a name="relabel-utterances-and-retrain"></a>
<a name="false-test-results"></a>

## <a name="view-section-data"></a>섹션 데이터 보기

4섹션 차트의 오른쪽 위에서 **가양성**과 같은 섹션 이름을 선택합니다. 해당 섹션의 모든 발화가 목록의 차트 아래에 표시됩니다. 

![섹션별 선택한 발화](./media/luis-how-to-batch-test/selected-utterances-by-section.png)

이 이전 이미지에서 발화 `switch on`은 TurnAllOn 의도로 레이블이 지정되지만 None 의도의 예측을 수신했습니다. 이는 예상 예측을 만들기 위해 TurnAllOn 의도에 더 많은 예제 발화가 필요함을 나타냅니다. 

빨간색으로 표시된 차트의 두 섹션은 예상 예측과 일치하지 않는 발화를 나타냅니다. 이는 LUIS에 추가 학습이 필요한 발화를 나타냅니다. 

녹색으로 표시된 차트의 두 섹션은 예상 예측과 일치했습니다.

## <a name="next-steps"></a>다음 단계

테스트에서 LUIS 앱이 올바른 의도와 엔터티를 인식하지 못하는 것으로 나타나면 추가 발화에 레이블을 지정하거나 기능을 추가하여 LUIS 앱의 성능을 개선할 수 있습니다. 

* [LUIS로 제안된 발화에 레이블 지정](luis-how-to-review-endoint-utt.md) 
* [기능을 사용하여 LUIS 앱 성능 향상](luis-how-to-add-features.md) 
* [이 자습서를 통해 일괄 테스트 이해](luis-tutorial-batch-testing.md)
* [일괄 테스트 개념 알아보기](luis-concept-batch-test.md).
