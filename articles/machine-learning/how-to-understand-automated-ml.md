---
title: AutoML 실험 결과 평가
titleSuffix: Azure Machine Learning
description: 자동화 된 각 기계 학습 실험 실행에 대 한 차트 및 메트릭을 확인 하 고 평가 하는 방법에 대해 알아봅니다.
services: machine-learning
author: gregorybchris
ms.author: chgrego
ms.reviewer: nibaccam
ms.service: machine-learning
ms.subservice: core
ms.date: 12/09/2020
ms.topic: conceptual
ms.custom: how-to, contperfq2, automl
ms.openlocfilehash: a3b3640922daf84357354efc389e20afea78d216
ms.sourcegitcommit: dea56e0dd919ad4250dde03c11d5406530c21c28
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/09/2020
ms.locfileid: "96937715"
---
# <a name="evaluate-automated-machine-learning-experiment-results"></a>자동화 된 machine learning 실험 결과 평가

이 문서에서는 자동화 된 기계 학습 (자동화 된 ML) 실험에서 학습 한 모델을 평가 하 고 비교 하는 방법에 대해 알아봅니다. 자동화 된 ML 실험을 수행 하는 과정에서 많은 실행이 만들어지고 각 실행은 모델을 만듭니다. 각 모델에 대해 자동화 된 ML은 모델의 성능을 측정 하는 데 도움이 되는 계산 메트릭과 차트를 생성 합니다. 

예를 들어, 자동화 된 ML은 실험 유형에 따라 다음 차트를 생성 합니다.

| 분류| 회귀/예측 |
| ----------------------------------------------------------- | ---------------------------------------- |
| [혼동 행렬](#confusion-matrix)                       | [잔차 히스토그램](#residuals)        |
| [ROC (받는 사람 운영 특징) 곡선](#roc-curve) | [예측 및 true](#predicted-vs-true) |
| [정밀도-재호출 (PR) 곡선](#precision-recall-curve)      |                                          |
| [리프트 곡선](#lift-curve)                                   |                                          |
| [누적 이점 곡선](#cumulative-gains-curve)           |                                          |
| [보정 곡선](#calibration-curve)                     |                     


## <a name="prerequisites"></a>사전 요구 사항

- Azure 구독 Azure 구독이 없는 경우 시작 하기 전에 [무료 계정을 만듭니다](https://aka.ms/AMLFree) .
- 다음 중 하나를 사용 하 여 만든 Azure Machine Learning 실험
  - [Azure Machine Learning studio](how-to-use-automated-ml-for-ml-models.md) (코드 필요 없음)
  - [Azure Machine Learning PYTHON SDK](how-to-configure-auto-train.md)

## <a name="view-run-results"></a>실행 결과 보기

자동화 된 ML 실험을 완료 한 후에는 다음을 통해 실행 기록을 찾을 수 있습니다.
  - [Azure Machine Learning studio](overview-what-is-machine-learning-studio.md) 를 사용 하는 브라우저
  - [Rundetails jupyter 위젯을](/python/api/azureml-widgets/azureml.widgets.rundetails?view=azure-ml-py&preserve-view=true) 사용 하는 jupyter 노트북

다음 단계와 비디오에서는 스튜디오에서 실행 기록과 모델 평가 메트릭과 차트를 보는 방법을 보여 줍니다.

1. [Studio에 로그인](https://ml.azure.com/) 하 고 작업 영역으로 이동 합니다.
1. 왼쪽 메뉴에서 **실험** 을 선택 합니다.
1. 실험 목록에서 실험을 선택 합니다.
1. 페이지 아래쪽의 테이블에서 자동 ML 실행을 선택 합니다.
1. **모델** 탭에서 평가 하려는 모델의 **알고리즘 이름을** 선택 합니다.
1. **메트릭** 탭에서 왼쪽의 확인란을 사용 하 여 메트릭 및 차트를 봅니다.

![Studio에서 메트릭을 확인 하는 단계](./media/how-to-understand-automated-ml/how-to-studio-metrics.gif)

## <a name="classification-metrics"></a>분류 메트릭

자동 ML은 실험을 위해 생성 된 각 분류 모델에 대 한 성능 메트릭을 계산 합니다. 이러한 메트릭은 scikit 학습 구현을 기반으로 합니다. 

많은 분류 메트릭이 두 클래스에서 이진 분류에 대해 정의 되 고, 클래스에 대 한 평균을 계산 하 여 다중 클래스 분류에 대해 하나의 점수를 생성 해야 합니다. Scikit-여러 가지 평균 메서드를 제공 합니다 .이 중 세 가지는 자동화 된 ML에서 **매크로**, **마이크로** 및 **가중치** 를 노출 합니다.

- **매크로** -각 클래스에 대 한 메트릭을 계산 하 고 가중치가 적용 되지 않은 평균을 사용 합니다.
- **마이크로** -클래스와 무관 하 게 총 참 긍정, 거짓 부정 및 거짓 긍정을 계산 하 여 전역으로 메트릭을 계산 합니다.
- **가 중** -각 클래스의 메트릭을 계산 하 고 클래스 당 샘플 수에 따라가 중 평균을 사용 합니다.

각 평균 방법의 이점이 있지만 적절 한 메서드를 선택할 때 발생 하는 한 가지 일반적인 고려 사항은 클래스 불균형입니다. 클래스의 샘플 수가 다른 경우 소수 클래스가 과반수 클래스에 동일한 가중치를 지정 하는 경우 매크로 평균을 사용 하는 것이 더 유용할 수 있습니다. [자동화 된 ML에서 이진 vs 다중 클래스 메트릭에](#binary-vs-multiclass-classification-metrics)대해 자세히 알아보세요. 

다음 표에서는 실험을 위해 생성 된 각 분류 모델에 대해 자동화 된 ML에서 계산 하는 모델 성능 메트릭을 요약 하 여 보여 줍니다. 자세한 내용은 각 메트릭의 **계산** 필드에 연결 된 scikit-학습 설명서를 참조 하세요. 

|메트릭|Description|계산|
|--|--|---|
|AUC | CC는 [받는 사람 운영 특성 곡선](#roc-curve)의 면적입니다.<br><br> **목표:** 1에 가까울수록 좋음 <br> **범위:** [0, 1]<br> <br>지원 되는 메트릭 이름에는 다음이 포함 됩니다. <li>`AUC_macro`-각 클래스에 대 한 각 클래스의 산술 평균입니다.<li> `AUC_micro`각 클래스에서 참 긍정 및 거짓 긍정을 결합 하 여 계산 됩니다. <li> `AUC_weighted`, 각 클래스의 점수에 대 한 산술 평균은 각 클래스의 실제 인스턴스 수에 의해 가중치가 적용 됩니다.   |[계산](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.roc_auc_score.html) | 
|accuracy| 정확도는 진정한 클래스 레이블과 정확히 일치 하는 예측의 비율입니다. <br> <br>**목표:** 1에 가까울수록 좋음 <br> **범위:** [0, 1]|[계산](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.accuracy_score.html)|
|average_precision|Average precision(평균 정밀도)은 정밀도-재현율 곡선을 이전 임계값에서의 재현율 증가를 가중치로 사용하여 계산한 각 임계값에서 도달된 정밀도의 가중 평균으로 요약합니다. <br><br> **목표:** 1에 가까울수록 좋음 <br> **범위:** [0, 1]<br> <br>지원 되는 메트릭 이름에는 다음이 포함 됩니다.<li>`average_precision_score_macro`-각 클래스의 평균 전체 자릿수 점수의 산술 평균입니다.<li> `average_precision_score_micro`각 구분에서 참 긍정 및 거짓 긍정을 결합 하 여 계산 됩니다.<li>`average_precision_score_weighted`각 클래스에 대 한 평균 전체 자릿수 점수의 산술 평균은 각 클래스의 실제 인스턴스 수에 의해 가중치가 적용 됩니다.|[계산](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.average_precision_score.html)|
balanced_accuracy|Balanced accuracy(균형 정확도)는 각 클래스 재현율의 산술 평균입니다.<br> <br>**목표:** 1에 가까울수록 좋음 <br> **범위:** [0, 1]|[계산](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|
f1_score|F1 score(F1 점수)는 정밀도 및 재현율의 조화 평균입니다. 거짓 긍정 및 거짓 네거티브를 모두 적절 하 게 분산 하 여 측정 합니다. 그러나이 경우에는 진정한 부정이 적용 되지 않습니다. <br> <br>**목표:** 1에 가까울수록 좋음 <br> **범위:** [0, 1]<br> <br>지원 되는 메트릭 이름에는 다음이 포함 됩니다.<li>  `f1_score_macro`: 각 클래스에 대 한 F1 점수의 산술 평균입니다. <li> `f1_score_micro`: 총 참 긍정, 거짓 부정 및 가양성을 계산 하 여 계산 합니다. <li> `f1_score_weighted`: 각 클래스에 대 한 F1 점수의 클래스 빈도로 가중치가 적용 된 평균입니다.|[계산](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.f1_score.html)|
log_loss|이 함수는 확률 분류자의 예측이 지정 된 경우 진정한 레이블의 부정적 로그 가능성으로 정의 된 신경망 등의 다항 () 로지스틱 회귀 및 확장에서 사용 되는 손실 함수입니다. <br><br> **목표:** 0에 가까울수록 좋음 <br> **범위:** [0, inf)|[계산](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.log_loss.html)|
norm_macro_recall| 정규화 된 매크로 회수는 정규화 되 고 정규화 된 매크로 회수로, 임의 성능의 점수는 0이 고 완벽 한 성능은 1입니다. <br> <br>**목표:** 1에 가까울수록 좋음 <br> **범위:** [0, 1] |`(recall_score_macro - R)`&nbsp;/&nbsp;`(1 - R)` <br><br>여기서 `R` 는 `recall_score_macro` 임의 예측에 대해 예상 되는 값입니다.<br><br>`R = 0.5`&nbsp;&nbsp;이진 분류의 경우 &nbsp; . <br>`R = (1 / C)` C 클래스 분류 문제의 경우.|
matthews_correlation | Matthews 상관 계수는 한 클래스에 다른 샘플이 포함 된 경우에도 사용할 수 있는 정확도의 균형 있는 측정값입니다. 계수 1은 완벽 한 예측, 0 무작위 예측 및-1 역 예측을 나타냅니다.<br><br> **목표:** 1에 가까울수록 좋음 <br> **범위:** [-1, 1]|[계산](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.matthews_corrcoef.html)|
정밀도|정밀도는 음수 샘플을 양수로 지정 하지 않는 모델의 기능입니다. <br><br> **목표:** 1에 가까울수록 좋음 <br> **범위:** [0, 1]<br> <br>지원 되는 메트릭 이름에는 다음이 포함 됩니다. <li> `precision_score_macro`-각 클래스에 대 한 전체 자릿수의 산술 평균입니다. <li> `precision_score_micro`총 참 긍정 및 거짓 긍정을 계산 하 여 전역적으로 계산 됩니다. <li> `precision_score_weighted`각 클래스에 대 한 전체 자릿수의 산술 평균은 각 클래스의 실제 인스턴스 수로 가중치가 지정 됩니다.|[계산](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.precision_score.html)|
재현율| 회수는 모든 긍정 샘플을 검색 하는 모델의 기능입니다. <br><br> **목표:** 1에 가까울수록 좋음 <br> **범위:** [0, 1]<br> <br>지원 되는 메트릭 이름에는 다음이 포함 됩니다. <li>`recall_score_macro`: 각 클래스에 대 한 회수의 산술 평균입니다. <li> `recall_score_micro`: 총 참 긍정, 거짓 부정 및 거짓 긍정을 계산 하 여 전역적으로 계산 합니다.<li> `recall_score_weighted`: 각 클래스에 대 한 각 클래스의 실제 인스턴스 수로 가중치가 적용 되는 각 클래스에 대 한 회수의 산술 평균입니다.|[계산](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|
weighted_accuracy|가중치가 적용 된 정확도는 각 샘플이 동일한 클래스에 속하는 총 샘플 수로 가중치가 적용 되는 정확도입니다. <br><br>**목표:** 1에 가까울수록 좋음 <br>**범위:** [0, 1]|[계산](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.accuracy_score.html)|

### <a name="binary-vs-multiclass-classification-metrics"></a>이진 및 다중 클래스 분류 메트릭 비교

자동화 된 ML은 이진 메트릭과 다중 클래스 메트릭을 구별 하지 않습니다. 데이터 집합에 두 개 이상의 클래스가 있는지 여부와 상관 없이 동일한 유효성 검사 메트릭이 보고 됩니다. 그러나 일부 메트릭은 다중 클래스 분류를 위한 것입니다. 이러한 메트릭은 이진 데이터 집합에 적용 될 때 클래스를 클래스로 처리 하지 않습니다 `true` . 다중 클래스에 대해 명확 하 게 사용 되는 메트릭은 `micro` , `macro` 또는로 시작 `weighted` 합니다. 예를 들면,,, `average_precision_score` `f1_score` 및가 `precision_score` `recall_score` `AUC` 있습니다.

예를 들어, 다중 클래스를 계산 하는 대신 `tp / (tp + fn)` `micro` `macro` `weighted` 이진 분류 데이터 집합의 두 클래스에 대 한 평균 회수 (, 또는)의 평균을 계산 합니다. 이는 클래스와 클래스에 대 한 회수를 `true` 별도로 계산한 `false` 다음이 두 값의 평균을 가져오는 것과 같습니다.

## <a name="confusion-matrix"></a>혼동 행렬

혼동 행렬은 기계 학습 모델에서 분류 모델에 대 한 예측 오류를 만드는 방법에 대 한 시각적 개체를 제공 합니다. 이름의 단어 "혼동"은 모델 "혼동" 또는 잘못 된 레이블 지정 샘플에서 제공 됩니다. `i`혼동 행렬의 행 및 열에 있는 셀은 `j` 클래스에 속하고 `C_i` 모델에 의해 클래스로 분류 된 계산 데이터 집합의 샘플 수를 포함 합니다 `C_j` .

스튜디오에서 더 진한 셀은 더 많은 예제를 나타냅니다. 드롭다운에서 **정규화** 된 보기를 선택 하면 각 행렬 행을 정규화 하 여 클래스로 예측 되는 클래스의 비율을 표시 `C_i` `C_j` 합니다. 기본 **원시** 보기의 장점으로 실제 클래스 배포의 불균형으로 인해 모델이 소주주 클래스의 샘플을 잘못 분류 하 고 있는지 여부를 확인할 수 있습니다 .이는 불균형 데이터 집합에서 일반적인 문제입니다.

좋은 모델의 혼동 행렬에는 대각선을 따라 대부분의 샘플이 포함 됩니다.

### <a name="confusion-matrix-for-a-good-model"></a>좋은 모델에 대 한 혼동 행렬 
![좋은 모델에 대 한 혼동 행렬 ](./media/how-to-understand-automated-ml/chart-confusion-matrix-good.png)

### <a name="confusion-matrix-for-a-bad-model"></a>잘못 된 모델에 대 한 혼동 행렬
![잘못 된 모델에 대 한 혼동 행렬](./media/how-to-understand-automated-ml/chart-confusion-matrix-bad.png)

## <a name="roc-curve"></a>ROC 곡선

의사 결정 임계값이 변경 될 때 ROC (받는 사람 운영 특징) 곡선은 진정한 긍정 률 (TPR)과 가양성 (FPR) 간의 관계를 플롯 합니다. 대다수 클래스에서 대규모 클래스의 기여를 drown 수 있으므로, 높은 클래스가 불균형 한 데이터 집합에 대 한 모델을 학습 하는 경우에는 ROC 곡선의 정보를 줄일 수 있습니다.

곡선의 면적 ()은 올바르게 분류 된 샘플의 비율로 해석할 수 있습니다. 좀 더 정확 하 게 말하자면, 분류자는 임의로 선택한 긍정 샘플에서 무작위로 선택한 긍정 샘플의 순위를 지정할 확률입니다. 곡선의 도형은 TPR와 FPR 간의 관계에 대 한 intuition를 분류 임계값 또는 의사 결정 경계의 함수로 제공 합니다.

차트의 왼쪽 위 모퉁이에 접근 하는 곡선이 가능한 최상의 모델인 100% TPR 및 0% FPR에 근접 하 고 있습니다. 임의 모델은 `y = x` 왼쪽 아래 모서리에서 오른쪽 위에 있는 선을 따라 ROC 곡선을 생성 합니다. 임의 모델 보다 더 나쁜 것은 선 아래에 ROC 곡선이 있습니다 `y = x` .
> [!TIP]
> 분류 실험의 경우 자동화 된 ML 모델에 대해 생성 된 각 꺾은선형 차트를 사용 하 여 클래스 당 모델을 평가 하거나 모든 클래스에 대해 평균을 계산할 수 있습니다. 차트의 오른쪽에 있는 범례에서 클래스 레이블을 클릭 하 여 이러한 여러 보기 간에 전환할 수 있습니다.
### <a name="roc-curve-for-a-good-model"></a>좋은 모델에 대 한 ROC 곡선
![좋은 모델에 대 한 ROC 곡선](./media/how-to-understand-automated-ml/chart-roc-curve-good.png)

### <a name="roc-curve-for-a-bad-model"></a>잘못 된 모델에 대 한 ROC curve
![잘못 된 모델에 대 한 ROC curve](./media/how-to-understand-automated-ml/chart-roc-curve-bad.png)

## <a name="precision-recall-curve"></a>정밀도-회수 곡선

정확도 리콜 곡선은 의사 결정 임계값이 변경 될 때 precision과 재호출 간의 관계를 플롯 합니다. 회수는 모든 긍정 샘플 및 전체 자릿수를 검색 하는 모델의 기능입니다. 음수 샘플을 긍정으로 레이블 지정 하지 않는 것이 좋습니다. 일부 비즈니스 문제는 가양성을 방지 하는 것이 중요 합니다.
> [!TIP]
> 분류 실험의 경우 자동화 된 ML 모델에 대해 생성 된 각 꺾은선형 차트를 사용 하 여 클래스 당 모델을 평가 하거나 모든 클래스에 대해 평균을 계산할 수 있습니다. 차트의 오른쪽에 있는 범례에서 클래스 레이블을 클릭 하 여 이러한 여러 보기 간에 전환할 수 있습니다.
### <a name="precision-recall-curve-for-a-good-model"></a>좋은 모델에 대 한 전체 자릿수 리콜 곡선
![좋은 모델에 대 한 전체 자릿수 리콜 곡선](./media/how-to-understand-automated-ml/chart-precision-recall-curve-good.png)

### <a name="precision-recall-curve-for-a-bad-model"></a>잘못 된 모델에 대 한 전체 자릿수 리콜 곡선
![잘못 된 모델에 대 한 전체 자릿수 리콜 곡선](./media/how-to-understand-automated-ml/chart-precision-recall-curve-bad.png)

## <a name="cumulative-gains-curve"></a>누적 이점 곡선

누적 된 획득 곡선은 예측 확률의 순서로 샘플을 고려 하는 경우 고려 되는 샘플 비율의 함수로 올바르게 분류 된 긍정 샘플 비율을 플롯 합니다.

이득을 계산 하려면 먼저 모든 샘플을 모델에 의해 예측 된 최고에서 최저 확률로 정렬 합니다. 그런 다음 `x%` 최고 신뢰 예측을 수행 합니다. 긍정 샘플의 총 수로 검색 되는 긍정 샘플 수를 나누어 `x%` 얻을 수 있습니다. 누적 이득은 긍정 클래스에 속할 가능성이 가장 높은 데이터의 일부를 고려할 때 검색 하는 긍정 샘플의 백분율입니다.

완벽 한 모델은 모든 음수 샘플 위의 모든 긍정 샘플에 순위를 지정 하 여 두 개의 직선 세그먼트로 구성 된 누적 이득을 제공 합니다. 첫 번째는에서로의 경사를 포함 하는 선입니다 `1 / x` `(0, 0)` `(x, 1)` `x` . 여기서는 긍정 클래스에 속하는 샘플의 비율입니다 ( `1 / num_classes` 클래스가 분산 된 경우). 두 번째는의 가로 줄입니다 `(x, 1)` `(1, 1)` . 첫 번째 세그먼트에서 모든 긍정 샘플은 올바르게 분류 되 고 누적 된 이점은로 `100%` 간주 되는 첫 번째 샘플 내에서로 이동 `x%` 합니다.

`y = x` `x%` 전체 긍정 샘플의 경우에만 고려 되는 샘플의 경우 기준 무작위 모델에는 누적 된 향상 곡선이 있습니다 `x%` . 완벽 한 모델은 왼쪽 위 모퉁이에 접촉 하는 마이크로 평균 곡선을 포함 하 고 누적 증가율이 100%가 될 때까지 기울기가 있는 매크로 평균 선을 포함 하 여 `1 / num_classes` 데이터 백분율이 100이 될 때까지 좌우 됩니다.
> [!TIP]
> 분류 실험의 경우 자동화 된 ML 모델에 대해 생성 된 각 꺾은선형 차트를 사용 하 여 클래스 당 모델을 평가 하거나 모든 클래스에 대해 평균을 계산할 수 있습니다. 차트의 오른쪽에 있는 범례에서 클래스 레이블을 클릭 하 여 이러한 여러 보기 간에 전환할 수 있습니다.
### <a name="cumulative-gains-curve-for-a-good-model"></a>좋은 모델에 대 한 누적 향상 곡선
![좋은 모델에 대 한 누적 향상 곡선](./media/how-to-understand-automated-ml/chart-cumulative-gains-curve-good.png)

### <a name="cumulative-gains-curve-for-a-bad-model"></a>잘못 된 모델에 대 한 누적 향상 곡선
![잘못 된 모델에 대 한 누적 향상 곡선](./media/how-to-understand-automated-ml/chart-cumulative-gains-curve-bad.png)

## <a name="lift-curve"></a>리프트 곡선

리프트 곡선은 임의의 모델에 비해 모델의 성능을 향상 시키는 방법을 보여 줍니다. 리프트는 임의 모델의 누적 획득에 대 한 누적 증가율의 비율로 정의 됩니다.

이러한 상대적 성능은 클래스의 수를 늘릴 때 분류가 더 어려워집니다. (난수 모델은 두 클래스를 사용 하는 데이터 집합에 비해 10 개 클래스를 포함 하는 데이터 집합에서 더 높은 샘플의 비율을 잘못 예측)

기준선 리프트 곡선은 `y = 1` 모델 성능이 임의 모델과 일치 하는 선입니다. 일반적으로 적절 한 모델에 대 한 리프트 곡선은 해당 차트에서 더 높은 것으로, x 축에서 더 높은 모델을 보여 줍니다. 모델의 예측이 가장 확실 하 게 되 면 임의 추측 보다 여러 번 수행 됩니다.

> [!TIP]
> 분류 실험의 경우 자동화 된 ML 모델에 대해 생성 된 각 꺾은선형 차트를 사용 하 여 클래스 당 모델을 평가 하거나 모든 클래스에 대해 평균을 계산할 수 있습니다. 차트의 오른쪽에 있는 범례에서 클래스 레이블을 클릭 하 여 이러한 여러 보기 간에 전환할 수 있습니다.
### <a name="lift-curve-for-a-good-model"></a>좋은 모델에 대 한 리프트 곡선
![좋은 모델에 대 한 리프트 곡선](./media/how-to-understand-automated-ml/chart-lift-curve-good.png)
 
### <a name="lift-curve-for-a-bad-model"></a>잘못 된 모델에 대 한 리프트 곡선
![잘못 된 모델에 대 한 리프트 곡선](./media/how-to-understand-automated-ml/chart-lift-curve-bad.png)

## <a name="calibration-curve"></a>보정 곡선

보정 곡선은 각 신뢰 수준에서 긍정 샘플의 비율에 대해 모델의 정확도를 예측 하 여 그립니다. 잘 보정 된 모델은 100% 신뢰도를 할당 하는 예측의 100%를 올바르게 분류 하 고, 예측의 50%는 50% 신뢰도를 할당 하 고, 예측의 20%는 20% 신뢰도를 할당 합니다. 완벽 하 게 보정 된 모델은 `y = x` 모델이 각 클래스에 속하는 확률을 완벽 하 게 예측 하는 줄 뒤에 보정 곡선을 포함 합니다.

신뢰할 수 있는 모델은 0과 1에 가까운 확률을 초과 예측 하 고, 각 샘플의 클래스에 대해 확실 하지 않을 수 있으며, 보정 곡선은 뒤로 "S"와 유사 하 게 보입니다. 신뢰할 수 있는 모델은 예측 하는 클래스에 낮은 평균의 확률을 할당 하 고 연결 된 보정 곡선은 "S"와 유사 하 게 표시 됩니다. 보정 곡선은 모델의 기능을 정확 하 게 분류 하는 것이 아니라 예측에 신뢰도를 올바르게 할당 하는 기능을 나타냅니다. 모델에서 낮은 신뢰도 및 높은 불확실성을 정확 하 게 할당 하는 경우 잘못 된 모델은 올바른 보정 곡선을 사용할 수 있습니다.

> [!NOTE]
> 보정 곡선은 샘플 수를 구분 하므로 작은 유효성 검사 집합은 해석 하기 어려울 수 있는 잡음이 있는 결과를 생성할 수 있습니다. 이는 모델이 잘 보정 되지 않는다는 의미는 아닙니다.

### <a name="calibration-curve-for-a-good-model"></a>좋은 모델의 보정 곡선
![좋은 모델의 보정 곡선](./media/how-to-understand-automated-ml/chart-calibration-curve-good.png)

### <a name="calibration-curve-for-a-bad-model"></a>잘못 된 모델의 보정 곡선
![잘못 된 모델의 보정 곡선](./media/how-to-understand-automated-ml/chart-calibration-curve-bad.png)

## <a name="regressionforecasting-metrics"></a>회귀/예측 메트릭

자동 ML은 회귀 인지 예측 실험 인지에 관계 없이 생성 된 각 모델에 대해 동일한 성능 메트릭을 계산 합니다. 이러한 메트릭은 정규화를 사용 하 여 서로 다른 범위의 데이터에 대해 학습 된 모델을 비교할 수 있습니다. 자세히 알아보려면 [메트릭 정규화](#metric-normalization)를 참조 하세요.  

다음 표에서는 회귀 및 예측 실험에 대해 생성 된 모델 성능 메트릭을 요약 합니다. 이러한 메트릭은 분류 메트릭과 마찬가지로 scikit 배우기 구현을 기반으로 합니다. 적절 한 scikit 학습 설명서는 **계산** 필드에서 적절 하 게 연결 됩니다.

|메트릭|Description|계산|
--|--|--|
explained_variance|설명 된 분산은 모델에서 대상 변수의 변형에 대해 설명 하는 범위를 측정 합니다. 오차 분산에 대한 원래 데이터의 분산 감소율입니다. 오류의 평균은 0 인 경우 결정 계수와 같습니다 (아래 r2_score 참조). <br> <br> **목표:** 1에 가까울수록 좋음 <br> **범위:** (-inf, 1]|[계산](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.explained_variance_score.html)|
mean_absolute_error|절대 평균 오차는 대상과 예측의 절대값의 예상 값입니다.<br><br> **목표:** 0에 가까울수록 좋음 <br> **범위:** [0, inf) <br><br> 종류 <br>`mean_absolute_error` <br>  `normalized_mean_absolute_error`mean_absolute_error 데이터의 범위로 구분 됩니다. | [계산](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_absolute_error.html)|
median_absolute_error|Median absolute error(중앙값 절대 오차)는 목표와 예측 간 모든 절대 차이의 중앙값입니다. 이 손실은 이상값보다 강력합니다.<br><br> **목표:** 0에 가까울수록 좋음 <br> **범위:** [0, inf)<br><br>종류 <br> `median_absolute_error`<br> `normalized_median_absolute_error`: median_absolute_error 데이터의 범위로 구분 됩니다. |[계산](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.median_absolute_error.html)|
r2_score|R ^ 2는 평균을 출력 하는 기준선 모델과 비교 했을 때의 결정 계수 또는 백분율 감소입니다. <br> <br> **목표:** 1에 가까울수록 좋음 <br> **범위:** (-inf, 1]|[계산](https://scikit-learn.org/0.16/modules/generated/sklearn.metrics.r2_score.html)|
root_mean_squared_error |RMSE (제곱 평균 제곱 오차)는 대상과 예측 간의 예상 제곱의 제곱근입니다. 비편향 평가기의 경우 RMSE는 표준 편차와 같습니다.<br> <br> **목표:** 0에 가까울수록 좋음 <br> **범위:** [0, inf)<br><br>종류<br> `root_mean_squared_error` <br> `normalized_root_mean_squared_error`: root_mean_squared_error 데이터의 범위로 구분 됩니다. |[계산](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_squared_error.html)|
root_mean_squared_log_error|제곱근 평균 제곱 로그 오류는 예상 되는 제곱 로그 오류의 제곱근입니다.<br><br>**목표:** 0에 가까울수록 좋음 <br> **범위:** [0, inf) <br> <br>종류 <br>`root_mean_squared_log_error` <br> `normalized_root_mean_squared_log_error`: root_mean_squared_log_error 데이터의 범위로 구분 됩니다.  |[계산](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_squared_log_error.html)|
spearman_correlation| Spearman correlation(Spearman 상관 관계)은 두 데이터 세트 간 관계의 비모수 단조성 측정입니다. Pearson 상관 관계와 달리 Spearman 상관 관계는 두 데이터 세트가 모두 정규적으로 분포된다고 가정하지 않습니다. 다른 상관 관계 계수와 마찬가지로 스페어만는-1과 1 사이의 차이가 없음을 의미 합니다. -1 또는 1의 상관 관계는 정확한 단조 관계를 의미 합니다. <br><br> 스페어만은 순위 순서 상관 관계 메트릭입니다. 즉 예측 또는 실제 값을 변경 해도 예측 또는 실제 값의 순위 순서를 변경 하지 않는 경우에는 스페어만 결과가 변경 되지 않습니다.<br> <br> **목표:** 1에 가까울수록 좋음 <br> **범위:** [-1, 1]|[계산](https://docs.scipy.org/doc/scipy-0.16.1/reference/generated/scipy.stats.spearmanr.html)|

### <a name="metric-normalization"></a>메트릭 정규화

자동화 된 ML은 다양 한 범위의 데이터에 대해 학습 된 모델을 비교할 수 있는 회귀 및 예측 메트릭을 정규화 합니다. 범위가 더 큰 데이터에 대해 학습 된 모델의 경우 해당 오류를 정규화 하지 않으면 더 작은 범위의 데이터로 학습 된 모델 보다 더 높은 오류가 발생 합니다.

오류 메트릭을 정규화 하는 표준 방법은 없지만 자동화 된 ML은 데이터의 범위에 따라 오류를 분할 하는 일반적인 방법을 사용 합니다. `normalized_error = error / (y_max - y_min)`

시계열 데이터에 대 한 예측 모델을 평가할 때 자동화 된 ML은 시계열 ID (그레인) 마다 정규화가 발생 하는지 확인 하기 위해 추가 단계를 수행 합니다. 각 시계열에는 대상 값의 분포가 서로 다를 수 있기 때문입니다.
## <a name="residuals"></a>잔차

잔차 차트는 회귀 및 예측 실험을 위해 생성 된 잔차 (예측 오류)의 히스토그램입니다. 잔차는 `y_predicted - y_true` 모든 샘플에 대해 계산 된 다음 모델 바이어스를 표시 하는 히스토그램으로 표시 됩니다.

이 예제에서 두 모델은 실제 값 보다 낮은 예측에 약간의 차이가 있습니다. 실제 대상의 불균형 분포를 사용 하는 데이터 집합에는 일반적이 지 않지만 모델 성능 저하를 나타냅니다. 좋은 모델에는 극단에서 잔차가 거의 없는 0에서 최대 잔차 분포를 사용할 수 있습니다. 더 나쁜 모델은 0에 대 한 샘플 수와 함께 분산 된 잔차 분포를 가집니다.

### <a name="residuals-chart-for-a-good-model"></a>좋은 모델에 대 한 잔차 차트
![좋은 모델에 대 한 잔차 차트](./media/how-to-understand-automated-ml/chart-residuals-good.png)

### <a name="residuals-chart-for-a-bad-model"></a>잘못 된 모델에 대 한 잔차 차트
![잘못 된 모델에 대 한 잔차 차트](./media/how-to-understand-automated-ml/chart-residuals-bad.png)

## <a name="predicted-vs-true"></a>예측 및 true

회귀 및 예측 실험의 경우 예측 및 실제 차트는 대상 기능 (true/actual 값)과 모델의 예측 간의 관계를 그립니다. True 값은 x 축을 따라 범주화 되며 각 bin에 대해 평균 예측 값이 오차 막대로 그려집니다. 이를 통해 모델이 특정 값을 예측 하는 것을 중심으로 확인할 수 있습니다. 줄은 평균 예측을 표시 하 고 음영 처리 된 영역은 평균 예측의 차이를 나타냅니다.

가장 일반적인 true 값이 가장 낮은 차이를 가진 가장 정확한 예측을 포함 하는 경우가 많습니다. 실제 값이 거의 없는 이상적인 줄에서 추세 선 사이의 거리는 이상 `y = x` 값에 대 한 모델 성능을 측정 하는 좋은 방법입니다. 차트 아래쪽의 히스토그램을 사용 하 여 실제 데이터 분산에 대 한 이유를 확인할 수 있습니다. 배포가 스파스 인 추가 데이터 샘플을 포함 하면 보이지 않는 데이터의 모델 성능을 향상 시킬 수 있습니다.

이 예제에서 더 나은 모델은 이상적인 줄에 더 가까운 예측 및 실제 줄을 포함 합니다 `y = x` .

### <a name="predicted-vs-true-chart-for-a-good-model"></a>좋은 모델에 대 한 예측 및 실제 차트
![좋은 모델에 대 한 예측 및 실제 차트](./media/how-to-understand-automated-ml/chart-predicted-true-good.png)

### <a name="predicted-vs-true-chart-for-a-bad-model"></a>잘못 된 모델에 대 한 예측 및 실제 차트
![잘못 된 모델에 대 한 예측 및 실제 차트](./media/how-to-understand-automated-ml/chart-predicted-true-bad.png)

## <a name="model-explanations-and-feature-importances"></a>모델 설명 및 기능 importances

모델 평가 메트릭과 차트는 모델의 일반 품질을 측정 하는 데 적합 하지만, 책임이 있는 AI를 실행할 때 예측을 만드는 데 사용 되는 모델의 데이터 집합 기능을 검사 하는 것이 중요 합니다. 자동화 된 ML은 데이터 집합 기능의 상대적 기여도를 측정 하 고 보고 하는 모델 interpretability 대시보드를 제공 합니다.

![기능 importances](./media/how-to-understand-automated-ml/how-to-feature-importance.gif)

스튜디오에서 interpretability 대시보드를 보려면 다음을 수행 합니다.

1. [Studio에 로그인](https://ml.azure.com/) 하 고 작업 영역으로 이동 합니다.
2. 왼쪽 메뉴에서 **실험** 을 선택 합니다.
3. 실험 목록에서 실험을 선택 합니다.
4. 페이지 아래쪽의 테이블에서 AutoML run을 선택 합니다.
5. **모델** 탭에서 설명 하려는 모델의 **알고리즘 이름을** 선택 합니다.
6. 설명 탭에서 모델이 가장 적합 한 경우에 대 **한 설명이 이미** 생성 된 것을 볼 수 있습니다.
7. 새 설명을 만들려면 **설명 모델** 을 선택 하 고 설명을 계산할 원격 계산을 선택 합니다.

> [!NOTE]
> ForecastTCN 모델은 현재 자동화 된 ML 설명에서 지원 되지 않으며 다른 예측 모델은 interpretability 도구에 대 한 액세스가 제한 될 수 있습니다.

## <a name="next-steps"></a>다음 단계
* 자동화 된 [machine learning 모델 설명 샘플 노트북](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/explain-model)을 사용해 보세요.
* [자동화 된 ML의 담당 AI 제품](how-to-machine-learning-interpretability-automl.md)에 대해 자세히 알아보세요.
* 자동 ML 관련 질문에 대해서는에 문의 askautomatedml@microsoft.com 하세요.
