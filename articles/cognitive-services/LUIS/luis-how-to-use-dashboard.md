---
title: 대시보드-Language Understanding
titleSuffix: Azure Cognitive Services
description: Analytics 대시보드를 보고 시각화 도구를 사용 하 여 의도 수정 합니다.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 05/22/2019
ms.author: diberry
ms.openlocfilehash: 055d113a2bc77f8de1b4b881718007c869470532
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/27/2019
ms.locfileid: "66236951"
---
# <a name="how-to-use-the-dashboard-to-improve-your-app"></a>대시보드를 사용 하 여 앱을 개선 하는 방법

찾기 및 예제에서는 길이 발언을 사용 하는 경우 학습 된 앱의 의도 사용 하 여 문제를 해결 합니다. 대시보드 수정 해야 하는 의도의 중요 정보를 사용 하 여 전체 앱 정보를 표시 합니다. 

분석은 반복 프로세스 반복을 변경 하 고 모델을 개선 하는 대시보드를 검토 합니다.

이 페이지에는 관련 분석 되지 않은 모든 예제에서는 길이 발언 라고 하는 의도에 앱에 대 한를 사용 해야 합니다. _패턴 전용_ 앱. 

## <a name="what-issues-can-be-fixed-from-dashboard"></a>대시보드에서 문제를 해결할 수 있습니다.

대시보드에서 주소가 지정 된 세 가지 문제는:

|문제|차트 색|설명|
|--|--|--|
|데이터 불균형|-|이 예제 길이 발언의 양에 따라 크게 달라질 때 발생 합니다. 모든 의도 할 _약_ 수가-None 의도 제외 하 고 예제 길이 발언. 앱의 총 길이 발언 양의 10%-15%만 있어야 합니다.<br><br> 데이터를 불균형 있지만 의도 정확도 특정 임계값을 초과 하는 경우 이러한 불균형 문제를 보고 되지 않습니다.<br><br>**시작이 문제를 사용 하 여 다른 문제의 근본 원인을 수 있습니다.**|
|명확 하지 않은 예측|주황색|상위 의도 및 다음 의도 점수 가까이로 인해 다음 학습 넘길 수는 경우 이런 [음수 샘플링](luis-how-to-train.md#train-with-all-data) 또는 의도에 추가 하는 자세한 예제 길이 발언. |
|잘못 된 예측|빨간색|이 예제 utterance 레이블이 지정 된 의도 (의도)에 대해 예측 하지 하는 경우 발생 합니다.|

올바른 예측은 파란색으로 표시 됩니다.

대시보드는 의도 영향을 알려 및 무엇을 해야 앱을 개선 하기 위해 제안 이러한 문제를 보여 줍니다. 

## <a name="before-app-is-trained"></a>앱을 학습 하기 전에 

앱을 학습 하기 전에 대시보드 수정에 대 한 모든 제안이 없습니다. 이러한 제안을 보려면 앱을 학습 합니다.  

## <a name="check-your-publishing-status"></a>게시 상태 확인

합니다 **게시 상태** 활성에 대 한 정보를 포함 하는 카드 버전의 마지막으로 게시 합니다. 

현재 사용 중인 버전 해결 하려는 버전 인지 확인 합니다. 

![대시보드 표시 앱의 외부 서비스 지역, 게시 및 끝점 적중 횟수를 집계 합니다.](./media/luis-how-to-use-dashboard/analytics-card-1-shows-app-summary-and-endpoint-hits.png)

이 또한 모든 외부 서비스, 게시 된 지역 나타나며 끝점 적중 횟수를 집계 합니다. 

## <a name="review-training-evaluation"></a>교육 평가 검토 합니다.

합니다 **교육 평가** 카드 영역에서 앱의 전체 정확도의 집계 요약을 포함 합니다. 점수 의도 품질을 나타냅니다. 

![교육 평가 카드에서 첫 번째 영역의 앱의 전체 정확도 대 한 정보를 포함합니다.](./media/luis-how-to-use-dashboard/analytics-card-2-shows-app-overall-accuracy.png)

차트를 올바르게 예측된 의도 및 다양 한 색으로 문제 영역을 나타냅니다. 대로 제안,이 점수 증가 사용 하 여 앱을 개선 합니다. 

제안 된 수정 문제 유형으로 분리 됩니다 및 앱에 대 한 가장 중요 한 합니다. 검토 하 고 의도 사용 당 문제를 해결 하려는 경우는 **[오류로 인 텐트](#intents-with-errors)** 페이지의 맨 아래에 카드. 

각 문제 영역에는 수정 해야 하는 의도 있습니다. 의도 이름을 선택 합니다 **의도** 대상이 눈에 띄도록 적용할 필터를 사용 하 여 페이지가 열립니다. 이 필터를 사용 하는 문제의 원인이 된 표현에 집중할 수 있습니다.

### <a name="compare-changes-across-versions"></a>버전 간에 변경 내용 비교

앱 변경 하기 전에 새 버전을 만듭니다. 새 버전에서는 의도 예제 표현에 제안된 된 변경 내용을 확인 한 다음 다시 학습 합니다. 대시보드 페이지에서 **교육 평가** 카드를 사용 하 여 합니다 **학습 된 버전에서 표시 변경** 에 변경 내용을 비교 합니다. 

![버전 간에 변경 내용 비교](./media/luis-how-to-use-dashboard/compare-improvement-across-versions.png)

### <a name="fix-version-by-adding-or-editing-example-utterances-and-retraining"></a>버전을 추가 하거나 예제 길이 발언을 편집 하 고 재 학습 하 여 해결

앱을 수정 하는 기본 방법을 추가 하거나 예제 길이 발언을 편집 및 다시 학습 됩니다. 새롭거나 변경 된 표현에 대 한 지침을 수행 해야 [길이 발언을 다양 한](luis-concept-utterance.md)합니다.

예제에서는 길이 발언 추가 사용자가 수행 해야 하는:

* 에 높은 수준의 다른 의도 길이 발언 이해
* 방법 한 가지 의도에서 길이 발언 인해 혼동할 수 있습니다 다른 의도 인식
* 경우는 자주 서로 혼동을 두 가지 의도 단일 의도를 축소할지 및 다른 데이터 엔터티를 사용 하 여 끌어내기 결정 수

### <a name="patterns-and-phrase-lists"></a>패턴 및 구 목록

분석 페이지를 사용 하는 경우 나타내지 않습니다 [패턴](luis-concept-patterns.md) 하거나 [구 목록](luis-concept-feature.md)합니다. 를 추가 하는 해당 하는 경우 잘못 되었거나 명확 하지 않은 예측에 도움이 될 수 있지만 데이터 불균형을 사용 하 여 도움이 되지 않습니다. 

### <a name="review-data-imbalance"></a>데이터 불균형 검토

시작이 문제를 사용 하 여 다른 문제의 근본 원인을 수 있습니다.

합니다 **데이터 불균형** 의도 목록 데이터 불균형을 해결 하려면 자세한 표현 해야 하는 의도 표시 합니다. 

**이 문제를 해결 하려면**:

* 자세한 길이 발언 의도를 추가한 다음 다시 학습 합니다. 

대시보드에서 제안 되는 경우가 아니면 길이 발언 None 의도를 추가 하지 마세요.

> [!Tip]
> 세 번째 섹션을 사용 하 여 페이지의 **의도 당 길이 발언** 사용 하 여는 **길이 발언 (number)** 의도는 자세한 길이 발언을 해야 하는 빠른 visual 가이드로 설정 합니다.  
    !['길이 발언 (번호)'를 사용 하 여 데이터 불균형을 사용 하 여 의도 찾습니다.](./media/luis-how-to-use-dashboard/predictions-per-intent-number-of-utterances.png)

### <a name="review-incorrect-predictions"></a>잘못 된 예측을 검토 합니다.

합니다 **잘못 된 예측** 의도 목록은 특정 의도 예제로 사용 되지만 다른 의도 대 한 예측 되는 길이 발언 있는 인 텐트입니다. 

**이 문제를 해결 하려면**:

* 다시 학습 의도를 더 구체적으로 길이 발언을 편집 합니다.
* 길이 발언을 너무 밀접 하 게 정렬 됩니다 및 다시 학습 하는 경우에 의도 결합 합니다.

### <a name="review-unclear-predictions"></a>명확 하지 않은 예측을 검토 합니다.

합니다 **명확 하지 않은 예측** 의도 목록은 의도 utterance에 대 한 상위 의도로 인해 다음 학습에 변경 될 수 있는 해당 가장 가까운 경쟁에서 충분히으로 없는 예측 점수를 사용 하 여 길이 발언을 사용 하 여 [ 음수 샘플링](luis-how-to-train.md#train-with-all-data)합니다.

**이 문제를 해결 하려면**;

* 다시 학습 의도를 더 구체적으로 길이 발언을 편집 합니다.
* 길이 발언을 너무 밀접 하 게 정렬 됩니다 및 다시 학습 하는 경우에 의도 결합 합니다.

## <a name="utterances-per-intent"></a>의도 당 길이 발언

이 카드는 인 텐트 전반적인 앱 상태를 보여 줍니다. 의도 및 재를 해결 한 문제에 대 한이 카드에서 눈에 보기를 계속 합니다.

다음 차트에는 문제를 해결 하려면 거의 문제 없이 잘 분산 된 응용 프로그램을 보여 줍니다.

![다음 차트에는 문제를 해결 하려면 거의 문제 없이 잘 분산 된 응용 프로그램을 보여 줍니다.](./media/luis-how-to-use-dashboard/utterance-per-intent-shows-data-balance.png)

다음 차트에는 많은 문제를 해결 하려면 잘못 분산 된 응용 프로그램을 보여 줍니다.

![다음 차트에는 문제를 해결 하려면 거의 문제 없이 잘 분산 된 응용 프로그램을 보여 줍니다.](./media/luis-how-to-use-dashboard/utterance-per-intent-shows-data-imbalance.png)

의도 대 한 정보를 가져오려는 각 의도 막대 위에 마우스를 가져갑니다. 

![다음 차트에는 문제를 해결 하려면 거의 문제 없이 잘 분산 된 응용 프로그램을 보여 줍니다.](./media/luis-how-to-use-dashboard/utterances-per-intent-with-details-of-errors.png)

사용 된 **정렬할** 해당 문제를 사용 하 여 가장 문제가 있는 의도에 집중할 수 있도록 의도 문제 유형별로 정렬 하는 기능입니다. 

## <a name="intents-with-errors"></a>오류를 사용 하 여 의도

이 카드를 사용 하면 특정 의도 대 한 문제를 검토할 수 있습니다. 이 카드의 기본 보기 가장 문제가 있는 인 텐트 이므로 노력을 집중할 수 있는 위치를 알 수 있습니다.

![오류 카드를 사용 하 여 의도 사용 하면 특정 의도 대 한 문제를 검토할 수 있습니다. 카드 노력을 집중할 수 있는 위치를 알 수 있도록 가장 문제가 의도를 기본적으로 필터링 됩니다.](./media/luis-how-to-use-dashboard/most-problematic-intents-with-errors.png)

상위 도넛형 차트는 세 가지 문제 유형에 서 의도 사용 하 여 문제를 보여 줍니다. 세 가지 문제 형식에 문제가 있는 경우 각 형식에 모든 경쟁 인 텐트와 함께 아래 차트 자체에 있습니다. 

### <a name="filter-intents-by-issue-and-percentage"></a>문제 및 백분율 의도 필터링 합니다.

카드의이 섹션에서는 사용 하는 오류 임계값 이하로 떨어지기는 예제 길이 발언을 찾을 수 있습니다. 이상적으로 중요 한 것으로 잘못 예측 해야 합니다. 비즈니스 및 고객 중심 백분율은입니다. 

익숙한 비즈니스에 대 한 임계값 비율을 결정 합니다. 

필터를 사용 하면 특정 문제를 사용 하 여 의도 찾을 수 있습니다.

|필터|제안 된 백분율|목적|
|--|--|--|
|가장 문제가 의도|-|**여기에서 시작** -다른 수정 보다 많은 앱이이 의도에서 대상이 눈에 띄도록 수정 향상 됩니다.|
|아래 올바른 예측|60%|선택한 의도에 길이 발언 올바른지 있지만 신뢰도 점수 임계값의 비율입니다. |
|위의 명확 하지 않은 예측|15%|선택한 의도에 가장 가까운 경쟁 의도 혼동 되는 길이 발언의 백분율입니다.|
|위의 잘못 된 예측|15%|잘못 예측 하는 선택한 의도에 길이 발언의 백분율입니다. |

### <a name="correct-prediction-threshold"></a>올바른 예측 임계값

확신할 예측 신뢰성 점수는 무엇입니까? 앱 개발의 부분을 60% 대상 수 있습니다. 사용 된 **예측 아래 해결** 수정 해야 하는 선택한 의도에 모든 길이 발언을 찾으려면 60%의 비율을 사용 하 여.

### <a name="unclear-or-incorrect-prediction-threshold"></a>명확 하지 않거나 잘못 된 예측 임계값

이러한 두 필터를 사용 하면 임계값 초과 선택한 의도에 길이 발언을 찾을 수 있습니다. 이러한 두 개의 백분율 오류 백분율로 생각할 수 있습니다. 예측에는 10 ~ 15% 오류율에 익숙한 경우이 값 보다 모든 길이 발언을 찾으려는 15%를 필터 임계값을 설정 합니다. 

## <a name="next-steps"></a>다음 단계

* [Azure 리소스 관리](luis-how-to-azure-subscription.md)
