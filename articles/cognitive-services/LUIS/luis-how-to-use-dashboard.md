---
title: 대시보드 - 언어 이해 - LUIS
titleSuffix: Azure Cognitive Services
description: 학습된 앱의 대시보드를 통해 의도 및 엔터티를 수정합니다. 대시보드에는 전체 앱 정보가 표시되며 수정해야 하는 의도의 하이라이트가 표시됩니다.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 11/08/2019
ms.author: diberry
ms.openlocfilehash: d9ae126753f55349f9bf3eefd20bc4d222866af1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "73888207"
---
# <a name="how-to-use-the-dashboard-to-improve-your-app"></a>대시보드를 사용하여 앱을 개선하는 방법

예제 발언을 사용할 때 학습된 앱의 의도에 대한 문제를 찾아 수정합니다. 대시보드에는 전체 앱 정보가 표시되며 수정해야 하는 의도의 하이라이트가 표시됩니다. 

검토 대시보드 분석은 모델을 변경하고 개선할 때 반복하는 반복 프로세스입니다.

이 페이지에는 _패턴 전용_ 앱이라고 하는 의도에 예제 발언이 없는 앱에 대한 관련 분석이 없습니다. 

## <a name="what-issues-can-be-fixed-from-dashboard"></a>대시보드에서 해결할 수 있는 문제는 무엇입니까?

대시보드에서 해결되는 세 가지 문제는 다음과 같습니다.

|문제|차트 색상|설명|
|--|--|--|
|데이터 불균형|-|이는 예제 발언의 양이 크게 다를 때 발생합니다. None 의도를 제외한 모든 의도에는 _거의_ 동일한 수의 예제 발언이 있어야 합니다. 앱에서 발언의 총 수량의 10%-15%만 가져야 합니다.<br><br> 데이터가 불균형하지만 의도 정확도가 특정 임계값을 초과하는 경우 이 불균형은 문제로 보고되지 않습니다.<br><br>**이 문제로 시작 - 그것은 다른 문제의 근본 원인일 수 있습니다.**|
|불분명한 예측|주황색|이는 음수 [샘플링](luis-how-to-train.md#train-with-all-data) 또는 의도에 추가된 예제 발언으로 인해 최상위 의도 및 다음 의도의 점수가 다음 교육에서 뒤집을 수 있을 만큼 충분히 가까우면 발생합니다. |
|잘못된 예측|빨강|이 문제는 레이블이 지정된 의도(의도)에 대해 예제 발화가 예측되지 않을 때 발생합니다.|

올바른 예측은 파란색으로 표시됩니다.

대시보드는 이러한 문제를 표시하고 영향을 받는 의도를 알려주며 앱을 개선하기 위해 수행해야 할 작업을 제안합니다. 

## <a name="before-app-is-trained"></a>앱을 학습하기 전에 

앱을 학습하기 전에 대시보드에 수정에 대한 제안 사항이 포함되어 있지 않습니다. 이러한 제안을 보려면 앱을 교육합니다.  

## <a name="check-your-publishing-status"></a>게시 상태 확인

**게시 상태** 카드에는 활성 버전의 마지막 게시에 대한 정보가 포함되어 있습니다. 

활성 버전이 수정하려는 버전이 있는지 확인합니다. 

![대시보드에는 앱의 외부 서비스, 게시된 지역 및 집계된 끝점 조회가 표시됩니다.](./media/luis-how-to-use-dashboard/analytics-card-1-shows-app-summary-and-endpoint-hits.png)

또한 외부 서비스, 게시된 지역 및 집계된 끝점 조회를 보여 주기도 합니다. 

## <a name="review-training-evaluation"></a>교육 평가 검토

**교육 평가** 카드에는 지역별로 앱의 전반적인 정확도에 대한 집계된 요약이 포함되어 있습니다. 점수는 의도 품질을 나타냅니다. 

![교육 평가 카드에는 앱의 전반적인 정확도에 대한 첫 번째 정보 영역이 포함되어 있습니다.](./media/luis-how-to-use-dashboard/analytics-card-2-shows-app-overall-accuracy.png)

차트는 올바르게 예측된 의도와 색상이 다른 문제 영역을 나타냅니다. 당신이 제안과 함께 응용 프로그램을 개선으로, 이 점수가 증가한다. 

제안된 수정 프로그램은 문제 유형별로 구분되며 앱에서 가장 중요한 수정 프로그램입니다. 의도에 따라 문제를 검토하고 해결하려면 페이지 하단에 오류 카드가 **[있는 의도를](#intents-with-errors)** 사용합니다. 

각 문제 영역에는 수정해야 하는 의도가 있습니다. 의도 이름을 선택하면 의도 **페이지가** 발언에 적용된 필터와 함께 열립니다. 이 필터를 사용하면 문제를 일으키는 발언에 집중할 수 있습니다.

### <a name="compare-changes-across-versions"></a>버전 간 변경 사항 비교

앱을 변경하기 전에 새 버전을 만듭니다. 새 버전에서는 의도의 예제 발언을 제안한 변경 내용을 변경한 다음 다시 학습합니다. 대시보드 페이지의 교육 **평가** 카드에서 **학습된 버전의 변경 내용 표시를** 사용하여 변경 내용을 비교합니다. 

![버전 간 변경 사항 비교](./media/luis-how-to-use-dashboard/compare-improvement-across-versions.png)

### <a name="fix-version-by-adding-or-editing-example-utterances-and-retraining"></a>예제 발언 및 재교육 추가 또는 편집하여 버전 수정

앱을 수정하는 기본 방법은 예제 발언을 추가하거나 편집하고 다시 학습하는 것입니다. 새 발언이나 변경된 발언은 다양한 발언에 대한 지침을 따라야 [합니다.](luis-concept-utterance.md)

예제 발언을 추가하는 것은 다음과 같은 사람이 수행해야 합니다.

* 발언의 다른 의도에 어떤 이해가 있습니다.
* 한 의도의 발언이 다른 의도와 혼동될 수 있는 방법을 알고 있습니다.
* 서로 자주 혼동되는 두 의도를 하나의 의도로 축소해야 하는지 여부를 결정할 수 있습니다. 이 경우 엔터티를 사용하여 다른 데이터를 가져와야 합니다.

### <a name="patterns-and-phrase-lists"></a>패턴 및 구 목록

분석 페이지는 [패턴](luis-concept-patterns.md) 또는 [구 목록을](luis-concept-feature.md)사용할 시기를 나타내지 않습니다. 추가하면 부정확하거나 명확하지 않은 예측에 도움이 될 수 있지만 데이터 불균형에는 도움이 되지 않습니다. 

### <a name="review-data-imbalance"></a>데이터 불균형 검토

이 문제로 시작 - 그것은 다른 문제의 근본 원인일 수 있습니다.

데이터 불균형 의도 목록에는 데이터 **불균형을** 해결하기 위해 더 많은 발언이 필요한 의도가 표시됩니다. 

**이 문제를 해결하려면**:

* 의도에 더 많은 발언을 추가한 다음 다시 학습합니다. 

대시보드에 제안된 경우가 아니면 None 의도에 발언을 추가하지 마십시오.

> [!Tip]
> 페이지의 세 번째 섹션인 **발언(숫자)** 설정이 있는 **의도당 발언은** 의도에 더 많은 발언이 필요한 빠른 시각적 가이드로 사용합니다.  
    !['발화(숫자)'를 사용하여 데이터 불균형이 있는 의도를 찾습니다.](./media/luis-how-to-use-dashboard/predictions-per-intent-number-of-utterances.png)

### <a name="review-incorrect-predictions"></a>잘못된 예측 검토

**잘못된 예측** 의도 목록에는 특정 의도의 예로 사용되지만 다른 의도에 대해 예측되는 발언이 있는 의도가 표시됩니다. 

**이 문제를 해결하려면**:

* 의도에 더 구체적으로 발언편집하고 다시 학습합니다.
* 발언이 너무 밀접하게 정렬되어 다시 학습되는 경우 의도를 결합합니다.

### <a name="review-unclear-predictions"></a>불분명한 예측 검토

**불분명한 예측** 의도 목록은 가장 가까운 라이벌과 는 거리가 멀지 않은 예측 점수가 있는 발언이 있는 의도를 보여 주며, 발언에 대한 최상위 의도는 [음수 샘플링으로](luis-how-to-train.md#train-with-all-data)인해 다음 교육에서 변경될 수 있습니다.

**이 문제를 해결하려면;**

* 의도에 더 구체적으로 발언편집하고 다시 학습합니다.
* 발언이 너무 밀접하게 정렬되어 다시 학습되는 경우 의도를 결합합니다.

## <a name="utterances-per-intent"></a>의도당 발화

이 카드는 의도 에 걸쳐 전체 응용 프로그램 상태를 보여줍니다. 의도를 수정하고 다시 학습할 때 이 카드에서 문제를 계속 살펴본다.

다음 차트는 수정할 문제가 거의 없는 균형 잡힌 앱을 보여 주며 있습니다.

![다음 차트는 수정할 문제가 거의 없는 균형 잡힌 앱을 보여 주며 있습니다.](./media/luis-how-to-use-dashboard/utterance-per-intent-shows-data-balance.png)

다음 차트는 해결해야 할 많은 문제가 있는 균형이 맞지 않습니다.

![다음 차트는 수정할 문제가 거의 없는 균형 잡힌 앱을 보여 주며 있습니다.](./media/luis-how-to-use-dashboard/utterance-per-intent-shows-data-imbalance.png)

각 의도의 막대 위로 마우스를 가져가서 의도에 대한 정보를 가져옵니다. 

![다음 차트는 수정할 문제가 거의 없는 균형 잡힌 앱을 보여 주며 있습니다.](./media/luis-how-to-use-dashboard/utterances-per-intent-with-details-of-errors.png)

**정렬** 기능을 사용하여 문제 유형별로 의도를 정렬하여 해당 문제와 관련하여 가장 문제가 있는 의도에 집중할 수 있습니다. 

## <a name="intents-with-errors"></a>오류가 있는 의도

이 카드를 사용하면 특정 의도에 대한 문제를 검토할 수 있습니다. 이 카드의 기본 보기는 가장 문제가 되는 의도이므로 노력을 집중할 위치를 알 수 있습니다.

![오류 카드가 있는 의도를 사용하면 특정 의도에 대한 문제를 검토할 수 있습니다. 카드는 기본적으로 가장 문제가 있는 의도로 필터링되므로 노력을 집중할 위치를 알 수 있습니다.](./media/luis-how-to-use-dashboard/most-problematic-intents-with-errors.png)

상단 도넛 형 차트는 세 가지 문제 유형에 걸쳐 의도문제를 보여줍니다. 세 가지 문제 유형에 문제가 있는 경우 각 유형에는 경쟁 의도와 함께 아래 차트가 있습니다. 

### <a name="filter-intents-by-issue-and-percentage"></a>문제 및 백분율별로 의도 필터링

카드의 이 섹션에서는 오류 임계값을 벗어나는 예제 발언을 찾을 수 있습니다. 이상적으로는 올바른 예측이 중요하도록 하는 것이 좋습니다. 이 비율은 비즈니스와 고객 중심입니다. 

비즈니스에 익숙한 임계값 백분율을 결정합니다. 

필터를 사용하면 특정 문제가 있는 의도를 찾을 수 있습니다.

|Assert|권장 비율|목적|
|--|--|--|
|가장 문제가 되는 의도|-|**여기에서 시작** - 이 의도에서 발언을 수정하면 다른 수정 사항보다 앱이 더 많이 향상됩니다.|
|아래의 정확한 예측|60%|선택한 의도에서 정확하지만 신뢰도 점수가 임계값 미만인 발언의 백분율입니다. |
|위의 불분명한 예측|15%|가장 가까운 경쟁 의도와 혼동되는 선택한 의도의 발언 비율입니다.|
|위의 잘못된 예측|15%|선택한 의도에서 잘못 예측된 발언의 백분율입니다. |

### <a name="correct-prediction-threshold"></a>올바른 예측 임계값

자신감 있는 예측 신뢰도 점수는 무엇입니까? 앱 개발 을 시작할 때 60%가 목표가 될 수 있습니다. 60%의 백분율로 **아래 올바른 예측을** 사용하여 수정해야 하는 선택한 의도에서 발언을 찾을 수 있습니다.

### <a name="unclear-or-incorrect-prediction-threshold"></a>명확하지 않거나 잘못된 예측 임계값

이 두 필터를 사용하면 임계값을 초과하여 선택한 의도에서 발언을 찾을 수 있습니다. 이 두 백분율을 오류 백분율로 생각할 수 있습니다. 예측에 대해 10-15%의 오류율이 편안한 경우 필터 임계값을 15%로 설정하여 이 값 이상의 모든 발언을 찾습니다. 

## <a name="next-steps"></a>다음 단계

* [Azure 리소스 관리](luis-how-to-azure-subscription.md)
