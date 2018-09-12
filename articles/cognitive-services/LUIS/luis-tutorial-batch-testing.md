---
title: 일괄 테스트를 사용하여 LUIS 예측 개선 | Microsoft Docs
titleSuffix: Azure
description: 일괄 테스트를 로드하고, 결과를 검토하며, 변경 사항으로 LUIS 예측을 개선합니다.
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 08/02/2018
ms.author: diberry
ms.openlocfilehash: 5abaeaee87d54e82df29e75b89c83522b8746730
ms.sourcegitcommit: 2d961702f23e63ee63eddf52086e0c8573aec8dd
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/07/2018
ms.locfileid: "44158248"
---
# <a name="improve-app-with-batch-test"></a>일괄 테스트로 앱 개선

이 자습서에서는 일괄 테스트를 사용하여 발화 예측 문제를 찾는 방법을 보여 줍니다.  

이 자습서에서는 다음 방법에 대해 알아봅니다.

<!-- green checkmark -->
> [!div class="checklist"]
* 일괄 테스트 파일 만들기 
* 일괄 테스트 실행
* 테스트 결과 검토
* 오류 수정 
* 다시 일괄 테스트

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="before-you-begin"></a>시작하기 전에

[끝점 발언 검토](luis-tutorial-review-endpoint-utterances.md) 자습서의 Human Resources 앱이 없으면 JSON을 [LUIS](luis-reference-regions.md#luis-website) 웹 사이트의 새 앱으로 [가져옵니다](luis-how-to-start-new-app.md#import-new-app). 가져올 앱은 [LUIS-Samples](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/quickstarts/custom-domain-review-HumanResources.json) Github 리포지토리에 있습니다.

원래의 인사 관리 앱을 유지하려면 [설정](luis-how-to-manage-versions.md#clone-a-version) 페이지에서 버전을 복제하고 해당 이름을 `batchtest`로 지정합니다. 복제는 원래 버전에 영향을 주지 않고도 다양한 LUIS 기능을 사용할 수 있는 좋은 방법입니다. 

앱을 학습합니다.

## <a name="purpose-of-batch-testing"></a>일괄 테스트의 목적

일괄 테스트를 사용하면 레이블이 지정된 발언 및 엔터티의 알려진 집합을 사용하여 학습된 활성 모델의 상태가 유효한지 확인할 수 있습니다. JSON 형식의 일괄 처리 파일에서 발언을 추가하고, 발언 내에서 예측해야 하느 엔터티 레이블을 설정합니다. 

<!--The recommended test strategy for LUIS uses three separate sets of data: example utterances provided to the model, batch test utterances, and endpoint utterances. --> 이 자습서 이외에서 앱을 사용할 경우 의도에 이미 추가된 예제 발언을 사용하지 *않도록* 합니다. 예제 발언을 기준으로 일괄 테스트 발언을 확인하려면 앱을 [내보냅니다](luis-how-to-start-new-app.md#export-app). 앱 예제 발언을 일괄 테스트 발언과 비교합니다. 

일괄 테스트를 위한 요구 사항:

* 테스트당 최대 1000개 발언 
* 중복되지 않습니다. 
* 허용되는 엔터티 형식: 기계 학습된 단순, 계층 구조(부모만) 및 복합 엔터티만. 일괄 테스트는 기계 학습된 의도 및 엔터티에만 유용합니다.

## <a name="create-a-batch-file-with-utterances"></a>발언을 사용하여 일괄 처리 파일 만들기

1. [VSCode](https://code.visualstudio.com/)와 같은 텍스트 편집기에서 `HumanResources-jobs-batch.json`을 만듭니다. 

2. JSON 형식의 일괄 처리 파일에서 테스트에서 예측할 **의도**가 포함된 발언을 추가합니다. 

   [!code-json[Add the intents to the batch test file](~/samples-luis/documentation-samples/tutorial-batch-testing/HumanResources-jobs-batch.json "Add the intents to the batch test file")]

## <a name="run-the-batch"></a>일괄 처리 실행

1. 맨 위 탐색 모음에서 **테스트**를 선택합니다. 

2. 오른쪽 패널에서 **Batch testing panel**(일괄 테스트 패널)을 선택합니다. 

    [![일괄 테스트 패널이 강조 표시된 LUIS 앱의 스크린샷](./media/luis-tutorial-batch-testing/hr-batch-testing-panel-link.png)](./media/luis-tutorial-batch-testing/hr-batch-testing-panel-link.png#lightbox)

3. **데이터 집합 가져오기**를 선택합니다.

    [![데이터 집합 가져오기가 강조 표시된 LUIS 앱의 스크린샷](./media/luis-tutorial-batch-testing/hr-import-dataset-button.png)](./media/luis-tutorial-batch-testing/hr-import-dataset-button.png#lightbox)

4. `HumanResources-jobs-batch.json` 파일의 파일 시스템 위치를 선택합니다.

5. 데이터 집합의 이름을 `intents only`로 지정하고 **완료**를 선택합니다.

    ![파일 선택](./media/luis-tutorial-batch-testing/hr-import-new-dataset-ddl.png)

6. **실행** 단추를 선택합니다. 테스트가 완료될 때까지 기다립니다.

7. **See results**(결과 보기)를 선택합니다.

8. 그래프 및 범례에서 결과를 검토합니다.

    [![일괄 테스트 결과가 표시되는 LUIS 앱의 스크린샷](./media/luis-tutorial-batch-testing/hr-intents-only-results-1.png)](./media/luis-tutorial-batch-testing/hr-intents-only-results-1.png#lightbox)

## <a name="review-batch-results"></a>일괄 처리 결과 검토

일괄 처리 차트는 결과의 사분면으로 표시됩니다. 차트의 오른쪽이 필터입니다. 기본적으로 필터는 목록의 첫 번째 의도로 설정됩니다. 필터에는 모든 의도와 단순, 계층 구조(부모만) 및 복합 엔터티만 포함합니다. [차트의 섹션](luis-concept-batch-test.md#batch-test-results) 또는 차트 내의 한 점을 선택하면 차트 아래에 관련된 발언이 표시됩니다. 

차트 위로 마우스를 가져간 후 마우스 휠로 차트의 발언을 확대하거나 축소할 수 있습니다. 차트에 많은 점이 밀집되어 있을 때 이렇게 하면 편리합니다. 

차트는 사분면으로 구분되며 그중 두 개의 섹션이 빨간색으로 표시됩니다. **이러한 섹션에 집중해야 합니다**. 

### <a name="getjobinformation-test-results"></a>GetJobInformation 테스트 결과

필터에 표시되는 **GetJobInformation** 테스트 결과는 4개 예측 중 2개가 성공적이었음을 보여 줍니다. 1사분면 위에서 이름 **False positive**를 선택하고 차트 아래에서 발언을 확인합니다. 

![LUIS 일괄 테스트 발언](./media/luis-tutorial-batch-testing/hr-applyforjobs-false-positive-results.png)

발언 중 두 개가 올바른 의도 **GetJobInformation**이 아닌 **ApplyForJob**으로 예측되는 이유는 무엇인가요? 두 의도는 단어 선택 및 단어 배열 측면에서 매우 밀접하게 관련되어 있습니다. 또한 **GetJobInformation**보다 **ApplyForJob**에 대한 예제가 거의 3배 이상 더 많습니다. 이러한 예제 발언 수의 불균형 때문에 **ApplyForJob** 의도에 가중치가 더 부여됩니다. 

두 의도는 동일한 오류 수를 갖습니다. 한 의도에서 예측이 잘못되면 다른 의도에도 영향을 미칩니다. 발언이 한 의도에 대해 잘못 예측되었고, 다른 의도에 대해서도 잘못 예측되었으므로 둘 다 오류가 있습니다. 

![LUIS 일괄 테스트 필터 오류](./media/luis-tutorial-batch-testing/hr-intent-error-count.png)

**False positive** 섹션의 위쪽 점에 해당하는 발언은 `Can I apply for any database jobs with this resume?` 및 `Can I apply for any database jobs with this resume?`입니다. 첫 번째 발언의 경우 단어 `resume`이 **ApplyForJob**에서만 사용되었습니다. 두 번째 발언의 경우 단어 `apply`가 **ApplyForJob** 의도에서만 사용되었습니다.

## <a name="fix-the-app-based-on-batch-results"></a>일괄 처리 결과를 기반으로 앱 수정

이 섹션의 목적은 앱을 수정하여 **GetJobInformation**에 대해 모든 발언이 올바르게 예측되도록 하는 것입니다. 

간단해 보이는 해결 방법은 올바른 의도에 이러한 일괄 처리 파일 발언을 추가하는 것입니다. 그렇지만 원하는 방식이 아닐 것입니다. 이러한 발언을 예제로 추가하지 않으면서 LUIS에서 발언이 올바르게 예측되도록 할 것입니다. 

**ApplyForJob** 발언 수량이 **GetJobInformation**와 같아질 때까지 발언을 제거하는 방법에 대해서도 관심이 있을 수 있습니다. 이렇게 하면 테스트 결과는 수정되지만 LUIS에서 다음 번에 해당 의도를 정확하게 예측하지 못할 수 있습니다. 

첫 번째 해결 방법은 **GetJobInformation**에 발언을 더 추가하는 것입니다. 두 번째 해결 방법은 `resume` 및 `apply`와 같은 단어의 가중치를 **ApplyForJob** 의도에 맞게 줄이는 것입니다. 

### <a name="add-more-utterances-to-getjobinformation"></a>**GetJobInformation**에 발언을 더 추가

1. 위쪽 탐색 패널에서 **테스트** 단추를 선택하여 일괄 테스트 패널을 닫습니다. 

2. 의도 목록에서 **GetJobInformation**을 선택합니다. 

3. 길이, 단어 선택 및 단어 배열이 다른 발언을 더 추가하고 용어 `resume`, `c.v.` 및 `apply`도 포함해야 합니다.

    |**GetJobInformation** 의도에 대한 예제 발언|
    |--|
    |Does the new job in the warehouse for a stocker require that I apply with a resume?|
    |Where are the roofing jobs today?|
    |I heard there was a medical coding job that requires a resume.|
    |I would like a job helping college kids write their c.v.s. |
    |Here is my resume, looking for a new post at the community college using computers.|
    |What positions are available in child and home care?|
    |Is there an intern desk at the newspaper?|
    |My C.v. shows I'm good at analyzing procurement, budgets, and lost money. Is there anything for this type of work?|
    |Where are the earth drilling jobs right now?|
    |I've worked 8 years as an EMS driver. Any new jobs?|
    |New food handling jobs require application?|
    |How many new yard work jobs are available?|
    |Is there a new HR post for labor relations and negotiations?|
    |I have a masters in library and archive management. Any new positions?|
    |Are there any babysitting jobs for 13 year olds in the city today?|

    발언에서 **Job** 엔터티에 레이블을 지정하지 않도록 합니다. 이 자습서의 이 섹션에서는 의도 예측에만 주안점을 둡니다.

4. 오른쪽 상단 탐색 영역에서 **학습**을 선택하여 앱을 학습합니다.

## <a name="verify-the-fix-worked"></a>수정되었는지 확인

일괄 테스트의 발언이 올바르게 예측되는지 확인하려면 일괄 테스트를 다시 실행합니다.

1. 맨 위 탐색 모음에서 **테스트**를 선택합니다. 일괄 처리 결과가 계속 열려 있으면 **목록으로 돌아가기**를 선택합니다.  

2. 일괄 처리 이름 오른쪽에 있는 줄임표(***...***) 단추를 선택하고 **데이터 집합 실행**을 선택합니다. 일괄 테스트가 완료될 때까지 기다립니다. **결과 보기** 단추가 이제 녹색으로 표시됩니다. 이것은 전체 일괄 처리가 성공적으로 실행되었음을 나타냅니다.

3. **See results**(결과 보기)를 선택합니다. 모든 의도의 이름 왼쪽에 녹색 아이콘이 있어야 합니다. 

    ![일괄 처리 결과 단추가 강조 표시된 LUIS의 스크린샷](./media/luis-tutorial-batch-testing/hr-batch-test-intents-no-errors.png)

## <a name="create-batch-file-with-entities"></a>엔터티를 사용하여 일괄 처리 파일 만들기 

일괄 테스트에서 엔터티를 확인하려면 일괄 처리 JSON 파일에서 엔터티에 레이블이 지정되어 있어야 합니다. 기계 학습 엔터티인 단순, 계층 구조(부모만) 및 복합 엔터티만 사용됩니다. 기계 학습되지 않은 엔터티는 정규식 또는 명시적인 텍스트 일치를 통해 항상 확인되므로 추가할 필요가 없습니다.

전체 단어에 대한 엔터티 변형([토큰](luis-glossary.md#token)) 개수는 예측 품질에 영향을 줄 수 있습니다. 레이블이 지정된 발언과 함께 의도에 제공된 학습 데이터에 다양한 길이의 엔터티가 포함되는지 확인합니다. 

처음에 일괄 처리 파일을 작성하고 테스트할 때는 잘못 예측될 것으로 생각되는 소수의 발언과 엔터티 뿐만 아니라 잘 예측되는 것으로 알고 있는 소수의 발언 및 엔터티로 시작하는 것이 가장 좋습니다. 이렇게 하면 문제 영역에 빠르게 집중할 수 있습니다. 예측되지 않았던 몇 가지 다른 작업 이름을 사용하여 **GetJobInformation** 및 **ApplyForJob** 의도를 테스트한 후에 **Job** 엔터티의 특정 값에 예측 문제가 있는지 확인하기 위해 이 일괄 테스트 파일이 개발되었습니다. 

테스트 발언에 제공된 **Job** 엔터티의 값은 일반적으로 하나 또는 두 개의 단어로 되어 있지만 더 많은 단어를 포함하는 예제도 일부 있습니다. _직접 만든_ Human Resources 앱에 여러 단어의 작업 이름이 있는 경우 이 앱에서 **Job** 엔터티 레이블이 지정된 예제 발언은 잘 작동하지 않습니다.

1. [VSCode](https://code.visualstudio.com/)와 같은 텍스트 편집기에서 `HumanResources-entities-batch.json`을 만듭니다. 또는 LUIS-Samples Github 리포지토리에서 [파일](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/tutorial-batch-testing/HumanResources-entities-batch.json)을 다운로드합니다.


2. JSON 형식의 일괄 처리 파일에서 발언에서 엔터티의 위치 뿐만 아니라 테스트에서 예측하려는 **의도**가 있는 발언을 포함하는 개체 배열을 추가합니다. 엔터티는 토큰 기반이므로 한 문자에서 각 엔터티를 시작 및 중지해야 합니다. 발언 앞뒤에 공백을 포함하지 않도록 합니다. 공백이 있으면 일괄 처리 파일을 가져올 때 오류가 발생합니다.  

   [!code-json[Add the intents and entities to the batch test file](~/samples-luis/documentation-samples/tutorial-batch-testing/HumanResources-entities-batch.json "Add the intents and entities to the batch test file")]


## <a name="run-the-batch-with-entities"></a>엔터티를 사용하여 일괄 처리 실행

1. 맨 위 탐색 모음에서 **테스트**를 선택합니다. 

2. 오른쪽 패널에서 **Batch testing panel**(일괄 테스트 패널)을 선택합니다. 

3. **데이터 집합 가져오기**를 선택합니다.

4. `HumanResources-entities-batch.json` 파일의 파일 시스템 위치를 선택합니다.

5. 데이터 집합의 이름을 `entities`로 지정하고 **완료**를 선택합니다.

6. **실행** 단추를 선택합니다. 테스트가 완료될 때까지 기다립니다.

7. **See results**(결과 보기)를 선택합니다.

## <a name="review-entity-batch-results"></a>엔터티 일괄 처리 결과 검토

모든 의도가 올바르게 예측된 차트가 열립니다. 오른쪽 필터에서 아래로 스크롤하여 잘못된 엔터티 예측을 찾습니다. 

1. 필터에서 **Job** 엔터티를 선택합니다.

    ![필터의 잘못된 엔터터 예측](./media/luis-tutorial-batch-testing/hr-entities-filter-errors.png)

    엔터티 예측을 표시하도록 차트가 변경됩니다. 

2. 차트의 3사분면에서 **False Negative**를 선택합니다. 그런 다음, control + E 키보드 조합을 사용하여 토큰 보기로 전환합니다. 

    [![엔터티 예측의 토큰 보기](./media/luis-tutorial-batch-testing/token-view-entities.png)](./media/luis-tutorial-batch-testing/token-view-entities.png#lightbox)
    
    차트 아래의 발언을 검토하면 작업 이름에 `SQL`이 포함될 경우 일관된 오류가 나타납니다. 예제 발언과 Job 구 목록을 검토할 경우 SQL은 한 번만 사용되고 더 큰 작업 이름 `sql/oracle database administrator`의 일부에 불과한 것을 알 수 있습니다.

## <a name="fix-the-app-based-on-entity-batch-results"></a>엔터티 일괄 처리 결과를 기준으로 앱 수정

앱을 수정하려면 LUIS에서 SQL 작업 변형을 올바르게 판단해야 합니다. 이러한 수정에는 몇 가지 옵션이 있습니다. 

* 몇 가지 예제 발언을 명시적으로 추가합니다. 이러한 발언은 SQL을 사용하고 해당 단어에 Job 엔터티 레이블을 지정합니다. 
* 구 목록에 더 많은 SQL 작업을 명시적으로 추가

이러한 작업은 사용자가 수행합니다.

엔터티가 올바르게 예측되기 전에 [패턴](luis-concept-patterns.md)을 추가하면 문제가 해결되지 않습니다. 패턴의 모든 엔터티가 검색될 때까지 패턴이 일치되지 못하기 때문입니다. 

## <a name="what-has-this-tutorial-accomplished"></a>이 자습서에서 수행한 작업은 무엇입니까?

일괄 처리에서 오류를 찾고 모델을 수정하여 앱 예측 정확도가 크게 개선되었습니다. 

## <a name="clean-up-resources"></a>리소스 정리

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [패턴에 대해 알아보기](luis-tutorial-pattern.md)

