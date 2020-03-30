---
title: 자동화된 ML 결과 이해
titleSuffix: Azure Machine Learning
description: 자동화된 각 기계 학습 실행에 대한 차트 및 메트릭을 보고 이해하는 방법을 알아봅니다.
services: machine-learning
author: RachelKellam
ms.author: rakellam
ms.reviewer: sgilley
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 12/05/2019
ms.openlocfilehash: c5f12da3606361b504d4581916d9645fa3cd24f0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79283460"
---
# <a name="understand-automated-machine-learning-results"></a>자동화된 Machine Learning 결과 이해
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

이 문서에서는 자동화된 각 기계 학습 실행의 차트 및 메트릭을 보고 이해하는 방법을 배웁니다. 

다음에 대해 자세히 알아봅니다.
+ [분류 모델의 메트릭, 차트 및 곡선](#classification)
+ [회귀 모델에 대한 메트릭, 차트 및 그래프](#regression)
+ [모델 해석성 및 기능 중요성](#explain-model)

## <a name="prerequisites"></a>사전 요구 사항

* Azure 구독 Azure 구독이 없는 경우 시작하기 전에 체험 계정을 만듭니다. 지금 [Azure Machine Learning 평가판 또는 유료 버전](https://aka.ms/AMLFree)을 사용해 보세요.

* SDK 또는 Azure 기계 학습 스튜디오에서 자동화된 기계 학습 실행을 위한 실험을 만듭니다.

    * SDK를 사용하여 [분류 모델](how-to-auto-train-remote.md) 또는 [회귀 모델](tutorial-auto-train-models.md) 작성
    * [Azure 기계 학습 스튜디오를](how-to-use-automated-ml-for-ml-models.md) 사용하여 적절한 데이터를 업로드하여 분류 또는 회귀 모델을 만듭니다.

## <a name="view-the-run"></a>달리기 보기

자동화된 기계 학습 실험을 실행한 후 실행 기록은 기계 학습 작업 영역에서 찾을 수 있습니다. 

1. 작업 영역으로 이동합니다.

1. 작업 영역의 왼쪽 패널에서 **실험을 선택합니다.**

   ![실험 메뉴 스크린샷](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-experiment-menu.png)

1. 실험 목록에서 탐색할 실험을 선택합니다.

   [![실험 목록](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-experiment-list.png)](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-experiment-list-expanded.png)

1. 아래쪽 테이블에서 **실행**을 선택합니다.

   [ ![실험 실행)](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-experiment-run.png)](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-experiment-run-expanded.png)

1. 모델에서 자세히 살펴볼 모델의 **알고리즘 이름을** 선택합니다.

   [![실험 모델](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-experiment-model.png)](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-experiment-model-expanded.png)

또한 `RunDetails` [Jupyter 위젯을](https://docs.microsoft.com/python/api/azureml-widgets/azureml.widgets?view=azure-ml-py)사용할 때 실행 중에 동일한 결과가 표시됩니다.

## <a name="classification-results"></a><a name="classification"></a>분류 결과

Azure Machine Learning의 자동화된 기계 학습 기능을 사용하여 빌드하는 모든 분류 모델에 대해 다음 메트릭 및 차트를 사용할 수 있습니다.

+ [메트릭](#classification-metrics)
+ [혼동 행렬](#confusion-matrix)
+ [정밀 리콜 차트](#precision-recall-chart)
+ [ROC(수신기 작동 특성)](#roc)
+ [리프트 곡선](#lift-curve)
+ [게인 곡선](#gains-curve)
+ [보정 플롯](#calibration-plot)

### <a name="classification-metrics"></a>분류 메트릭

분류 작업에 대한 각 실행 반복에 다음 메트릭이 저장됩니다.

메트릭|설명|계산|추가 매개 변수
--|--|--|--
AUC_Macro| AUC는 Receiver Operating Characteristic Curve 아래의 영역입니다. Macro(매크로)는 각 클래스에 대한 AUC의 산술 평균입니다.  | [계산](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.roc_auc_score.html) | average="macro"|
AUC_Micro| AUC는 Receiver Operating Characteristic Curve 아래의 영역입니다. 마이크로는 각 클래스의 진정한 긍정과 거짓 긍정을 결합하여 전 세계적으로 계산됩니다.| [계산](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.roc_auc_score.html) | average="micro"|
AUC_Weighted  | AUC는 Receiver Operating Characteristic Curve 아래의 영역입니다. 가중치는 각 클래스의 점수의 산술 평균이며 각 클래스의 실제 인스턴스 수에 의해 가중치가 가중됩니다.| [계산](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.roc_auc_score.html)|average="weighted"
accuracy|Accuracy(정확도)는 true 레이블과 정확히 일치하는 예측된 레이블의 백분율입니다. |[계산](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.accuracy_score.html) |None|
average_precision_score_macro|Average precision(평균 정밀도)은 정밀도-재현율 곡선을 이전 임계값에서의 재현율 증가를 가중치로 사용하여 계산한 각 임계값에서 도달된 정밀도의 가중 평균으로 요약합니다. 매크로는 각 클래스의 평균 정밀도 점수의 산술 평균입니다.|[계산](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.average_precision_score.html)|average="macro"|
average_precision_score_micro|Average precision(평균 정밀도)은 정밀도-재현율 곡선을 이전 임계값에서의 재현율 증가를 가중치로 사용하여 계산한 각 임계값에서 도달된 정밀도의 가중 평균으로 요약합니다. 마이크로는 각 컷오프에서 진정한 긍정과 거짓 긍정을 결합하여 전 세계적으로 계산됩니다.|[계산](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.average_precision_score.html)|average="micro"|
average_precision_score_weighted|Average precision(평균 정밀도)은 정밀도-재현율 곡선을 이전 임계값에서의 재현율 증가를 가중치로 사용하여 계산한 각 임계값에서 도달된 정밀도의 가중 평균으로 요약합니다. 가중치는 각 클래스의 평균 정밀도 점수의 산술 평균이며 각 클래스의 실제 인스턴스 수에 의해 가중치가 가중됩니다.|[계산](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.average_precision_score.html)|average="weighted"|
balanced_accuracy|Balanced accuracy(균형 정확도)는 각 클래스 재현율의 산술 평균입니다.|[계산](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|average="macro"|
f1_score_macro|F1 score(F1 점수)는 정밀도 및 재현율의 조화 평균입니다. 매크로는 각 클래스에 대한 F1 점수의 산술 평균입니다.|[계산](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.f1_score.html)|average="macro"|
f1_score_micro|F1 score(F1 점수)는 정밀도 및 재현율의 조화 평균입니다. 마이크로는 총 참 긍정, 거짓 부정 및 거짓 긍정을 계산하여 전 세계적으로 계산됩니다.|[계산](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.f1_score.html)|average="micro"|
f1_score_weighted|F1 score(F1 점수)는 정밀도 및 재현율의 조화 평균입니다. Weighted(가중치)는 각 클래스에 대한 F1 점수의 클래스 빈도별 평균입니다.|[계산](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.f1_score.html)|average="weighted"|
log_loss|이것은 확률 분류자의 예측을 주어진 실제 레이블의 음수 로그 가능성으로 정의 된 신경망과 같은 (다항식) 로지스틱 회귀 및 확장에 사용되는 손실 함수입니다. 실제 레이블 yt가 있는 {0,1} 단일 샘플의 경우 yt=1로 추정된 확률 yp의 경우, 로그 손실은 -log P(yt&#124;yp) = -(yt log(yp) + (1 - yt) 로그(1-yt))입니다.|[계산](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.log_loss.html)|None|
norm_macro_recall|Normalized Macro Recall(정규화된 매크로 재현율)은 임의 성능 점수가 0이고 완벽한 성능 점수가 1이 되도록 정규화된 매크로 재현율입니다. 이는 norm_macro_recall := (recall_score_macro - R)/(1 - R)에 의해 달성되며, 여기서 R은 임의 예측에 대한 recall_score_macro 예상 값입니다(즉, 이진 분류의 경우 R=0.5, C 클래스 분류 문제의 경우 R=(1/C)).|[계산](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|평균 = "매크로" |
precision_score_macro|정밀도는 올바르게 레이블이 지정된 긍정적으로 예측된 요소의 백분율입니다. 매크로는 각 클래스에 대한 정밀도의 산술 평균입니다.|[계산](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.precision_score.html)|average="macro"|
precision_score_micro|정밀도는 올바르게 레이블이 지정된 긍정적으로 예측된 요소의 백분율입니다. 마이크로는 총 참 긍정과 거짓 긍정을 계산하여 전 세계적으로 계산됩니다.|[계산](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.precision_score.html)|average="micro"|
precision_score_weighted|정밀도는 올바르게 레이블이 지정된 긍정적으로 예측된 요소의 백분율입니다. 가중치는 각 클래스의 정밀도 산술 평균이며 각 클래스의 실제 인스턴스 수에 의해 가중치가 가중됩니다.|[계산](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.precision_score.html)|average="weighted"|
recall_score_macro|리콜은 특정 클래스의 올바르게 레이블이 지정된 요소의 백분율입니다. 매크로는 각 클래스에 대한 리콜의 산술 평균입니다.|[계산](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|average="macro"|
recall_score_micro|리콜은 특정 클래스의 올바르게 레이블이 지정된 요소의 백분율입니다. 마이크로는 총 참 긍정, 거짓 부정 및 거짓 긍정을 계산하여 전 세계적으로 계산됩니다.|[계산](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|average="micro"|
recall_score_weighted|리콜은 특정 클래스의 올바르게 레이블이 지정된 요소의 백분율입니다. 가중치는 각 클래스의 실제 인스턴스 수에 의해 가중치가 가중된 각 클래스에 대한 리콜의 산술 평균입니다.|[계산](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|average="weighted"|
weighted_accuracy|가중 정확도는 각 예제에 부여된 가중치가 해당 예제의 실제 클래스에서 실제 인스턴스의 비율과 같은 정확도입니다.|[계산](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.accuracy_score.html)|sample_weight는 대상의 각 요소에 대한 해당 클래스의 비율과 동일한 벡터입니다.|

<a name="confusion-matrix"></a>

### <a name="confusion-matrix"></a>혼동 행렬

#### <a name="what-is-a-confusion-matrix"></a>혼동 행렬이란 무엇입니까?
혼동 행렬은 분류 모델의 성능을 설명하는 데 사용됩니다. 각 행은 데이터 집합의 true 또는 실제 클래스의 인스턴스를 표시하며 각 열은 모델에서 예측한 클래스의 인스턴스를 나타냅니다. 

#### <a name="what-does-automated-ml-do-with-the-confusion-matrix"></a>자동화된 ML은 혼동 매트릭스로 무엇을 합니까?
분류 문제를 위해 Azure Machine Learning은 빌드된 각 모델에 대한 혼동 행렬을 자동으로 제공합니다. 각 혼동 매트릭스에 대해 자동화된 ML은 실제 레이블(행)과 비교하여 각 예측 된 레이블(열)의 빈도를 표시합니다. 색상이 어두울수록 행렬의 특정 부분에서 개수가 높아지다. 

#### <a name="what-does-a-good-model-look-like"></a>좋은 모델은 어떤 모습일까요?
데이터 집합의 실제 값을 모델이 제공한 예측 값과 비교합니다. 따라서 기계 학습 모델은 대각선을 따라 대부분의 값이 있는 경우 기계 학습 모델의 정확도가 높아지므로 모델이 올바른 값을 예측했습니다. 모델에 클래스 불균형이 있는 경우 혼동 행렬은 편향된 모델을 검색하는 데 도움이 됩니다.

##### <a name="example-1-a-classification-model-with-poor-accuracy"></a>예 1: 정확도가 낮은 분류 모델
![정확도가 낮은 분류 모델](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-confusion-matrix1.png)

##### <a name="example-2-a-classification-model-with-high-accuracy"></a>예 2: 정확도가 높은 분류 모델 
![정확도가 높은 분류 모델](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-confusion-matrix2.png)

##### <a name="example-3-a-classification-model-with-high-accuracy-and-high-bias-in-model-predictions"></a>예 3: 모델 예측에서 정확도가 높고 바이어스가 높은 분류 모델
![모델 예측에서 정확도가 높고 바이어스가 높은 분류 모델](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-biased-model.png)

<a name="precision-recall-chart"></a>
### <a name="precision-recall-chart"></a>정밀도-리콜 차트
#### <a name="what-is-a-precision-recall-chart"></a>정밀 리콜 차트란 무엇입니까?
정밀도 회수 곡선은 모델의 정밀도와 리콜 간의 관계를 보여 주었습니다. 정밀도라는 용어는 모델이 모든 인스턴스에 올바르게 레이블을 지정하는 기능을 나타냅니다. 리콜은 분류자가 특정 레이블의 인스턴스를 모두 찾을 수 있는 기능을 나타냅니다.

#### <a name="what-does-automated-ml-do-with-the-precision-recall-chart"></a>자동 ML은 정밀 리콜 차트에서 무엇을 합니까?

이 차트를 사용하면 각 모델의 정밀도-리콜 곡선을 비교하여 특정 비즈니스 문제에 대해 정밀도와 리콜 간에 허용되는 관계가 있는 모델을 확인할 수 있습니다. 이 차트에는 매크로 평균 정밀도-리콜, 마이크로 평균 정밀도-리콜 및 모델의 모든 클래스와 관련된 정밀도-리콜이 표시됩니다. 

매크로 평균은 각 클래스와 독립적으로 메트릭을 계산한 다음 평균을 취하여 모든 클래스를 동일하게 처리합니다. 그러나 마이크로 평균은 평균을 계산하기 위해 모든 클래스의 기여도를 집계합니다. 데이터 집합에 클래스 불균형이 있는 경우 마이크로 평균이 바람직합니다.

#### <a name="what-does-a-good-model-look-like"></a>좋은 모델은 어떤 모습일까요?
비즈니스 문제의 목표에 따라 이상적인 정밀 리콜 곡선이 다를 수 있습니다. 몇 가지 예는 다음과 같습니다.

##### <a name="example-1-a-classification-model-with-low-precision-and-low-recall"></a>예 1: 정밀도가 낮고 리콜이 낮은 분류 모델
![정밀도가 낮고 리콜이 적은 분류 모델](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-precision-recall1.png)

##### <a name="example-2-a-classification-model-with-100-precision-and-100-recall"></a>예 2: ~100% 정밀도 및 ~100% 리콜을 가진 분류 모델 
![분류 모델 고정밀 및 리콜](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-precision-recall2.png)
<a name="roc"></a>
### <a name="roc-chart"></a>ROC 차트

#### <a name="what-is-a-roc-chart"></a>ROC 차트란?
ROC(수신기 작동 특성)는 특정 모델에 대해 올바르게 분류된 레이블 및 잘못 분류된 레이블의 플롯입니다. ROC 곡선은 가양성 레이블을 표시하지 않으므로 바이어스가 높은 데이터 세트에서 모델을 학습할 때는 제공하는 정보가 적을 수 있습니다.

#### <a name="what-does-automated-ml-do-with-the-roc-chart"></a>자동화된 ML은 ROC 차트에서 무엇을 합니까?
자동 ML은 매크로 평균 정밀 도용, 마이크로 평균 정밀 도리콜 및 모델의 모든 클래스와 관련된 정밀 도용을 생성합니다. 

매크로 평균은 각 클래스와 독립적으로 메트릭을 계산한 다음 평균을 취하여 모든 클래스를 동일하게 처리합니다. 그러나 마이크로 평균은 평균을 계산하기 위해 모든 클래스의 기여도를 집계합니다. 데이터 집합에 클래스 불균형이 있는 경우 마이크로 평균이 바람직합니다.

#### <a name="what-does-a-good-model-look-like"></a>좋은 모델은 어떤 모습일까요?
이상적으로, 모델은 100 % 진정한 긍정 비율에 가깝고 0 % 거짓 긍정 비율에 가깝습니다. 

##### <a name="example-1-a-classification-model-with-low-true-labels-and-high-false-labels"></a>예 1: 트루 라벨이 낮고 거짓 레이블이 높은 분류 모델
![트루 라벨이 낮고 거짓 레이블이 높은 분류 모델](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-roc-1.png)

##### <a name="example-2-a-classification-model-with-high-true-labels-and-low-false-labels"></a>예 2: 높은 트루 레이블과 낮은 거짓 레이블이 있는 분류 모델
![높은 트루 라벨과 낮은 거짓 레이블이 있는 분류 모델](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-roc-2.png)
<a name="lift-curve"></a>
### <a name="lift-chart"></a>리프트 차트
#### <a name="what-is-a-lift-chart"></a>리프트 차트란?
리프트 차트는 분류 모델의 성능을 평가하는 데 사용됩니다. 정확도 면에서 모델이 없는 모델에 비해 생성된 모델로 얼마나 더 잘 수행할 수 있는지 를 보여줍니다.
#### <a name="what-does-automated-ml-do-with-the-lift-chart"></a>자동 ML은 리프트 차트에서 무엇을 합니까?
특정 모델의 값 게인을 보기 위해 Azure Machine Learning을 사용하여 자동으로 빌드된 모델 리프트를 기준선과 비교할 수 있습니다.
#### <a name="what-does-a-good-model-look-like"></a>좋은 모델은 어떤 모습일까요?

##### <a name="example-1-a-classification-model-that-does-worse-than-a-random-selection-model"></a>예 1: 임의 선택 모델보다 더 나쁜 분류 모델
![임의 선택 모델보다 더 나쁜 분류 모델](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-lift-curve1.png)
##### <a name="example-2-a-classification-model-that-performs-better-than-a-random-selection-model"></a>예 2: 임의 선택 모델보다 성능이 좋은 분류 모델
![더 나은 성능을 제공하는 분류 모델](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-lift-curve2.png)
<a name="gains-curve"></a>
### <a name="gains-chart"></a>이득 차트
#### <a name="what-is-a-gains-chart"></a>이익 차트란 무엇입니까?

게인 차트는 데이터의 각 부분별로 분류 모델의 성능을 평가합니다. 데이터 세트의 각 백분위수에 대해, 임의 선택 모델과 비교해서 예상할 수 있는 성능 향상을 보여 줍니다.

#### <a name="what-does-automated-ml-do-with-the-gains-chart"></a>자동화된 ML은 이득 차트에서 무엇을 합니까?
누적 게인 차트를 사용하면 모델에서 원하는 게인에 해당하는 백분율을 사용하여 분류 경계를 선택하는 데 도움이 됩니다. 이 정보는 동반되는 리프트 차트의 결과를 살펴보는 또 다른 방법을 제공합니다.

#### <a name="what-does-a-good-model-look-like"></a>좋은 모델은 어떤 모습일까요?
##### <a name="example-1-a-classification-model-with-minimal-gain"></a>예 1: 게인이 최소화된 분류 모델
![게인이 최소화된 분류 모델](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-gains-curve1.png)

##### <a name="example-2-a-classification-model-with-significant-gain"></a>예 2: 상당한 게인이 있는 분류 모델
![상당한 이득을 가진 분류 모델](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-gains-curve2.png)
<a name="calibration-plot"></a>
### <a name="calibration-chart"></a>보정 차트

#### <a name="what-is-a-calibration-chart"></a>교정 차트란 무엇입니까?
보정 플롯은 예측 모델의 신뢰도를 표시하는 데 사용됩니다. "확률"이 특정 인스턴스가 일부 레이블 아래에 속할 가능성을 나타내는 예측 된 확률과 실제 확률 간의 관계를 보여 줌으로써 이 작업을 수행합니다.
#### <a name="what-does-automated-ml-do-with-the-calibration-chart"></a>자동 ML은 교정 차트에서 무엇을 합니까?
모든 분류 문제에 대해 마이크로 평균, 매크로 평균 및 지정된 예측 모델의 각 클래스의 보정 선을 검토할 수 있습니다.

매크로 평균은 각 클래스와 독립적으로 메트릭을 계산한 다음 평균을 취하여 모든 클래스를 동일하게 처리합니다. 그러나 마이크로 평균은 평균을 계산하기 위해 모든 클래스의 기여도를 집계합니다. 
#### <a name="what-does-a-good-model-look-like"></a>좋은 모델은 어떤 모습일까요?
 잘 보정된 모델은 y=x 선과 일치하며, 예측에 대해 합리적으로 확신할 수 있습니다. 신뢰도가 과도한 모델은 y=0 선과 일치하며, 이 경우 예측된 확률은 있지만 실제 확률이 없음을 의미합니다. 


##### <a name="example-1-a-well-calibrated-model"></a>예 1: 잘 보정된 모델
![ 더 잘 보정된 모델](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-calib-curve1.png)

##### <a name="example-2-an-over-confident-model"></a>예 2: 지나치게 자신감 있는 모델
![지나치게 자신감 있는 모델](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-calib-curve2.png)

## <a name="regression-results"></a><a name="regression"></a>회귀 결과

Azure Machine Learning의 자동화된 기계 학습 기능을 사용하여 빌드하는 모든 회귀 모델에 대해 다음 메트릭 및 차트를 사용할 수 있습니다.

+ [메트릭](#reg-metrics)
+ [예측 대 참](#pvt)
+ [나머지 히스토그램](#histo)


### <a name="regression-metrics"></a><a name="reg-metrics"></a>회귀 메트릭

다음 메트릭은 회귀 또는 예측 작업을 위해 각 실행 반복에 저장됩니다.

|메트릭|설명|계산|추가 매개 변수
--|--|--|--|
explained_variance|Explained variance(설명된 분산)는 수학 모델에서 지정된 데이터 세트의 편차가 고려되는 비율입니다. 오차 분산에 대한 원래 데이터의 분산 감소율입니다. 오차의 평균이 0이면 explained variance와 같습니다.|[계산](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.explained_variance_score.html)|None|
r2_score|R2는 평균을 출력하는 기준선 모델과 비교한 제곱 오차의 결정 계수 또는 환원율입니다. |[계산](https://scikit-learn.org/0.16/modules/generated/sklearn.metrics.r2_score.html)|None|
spearman_correlation|Spearman correlation(Spearman 상관 관계)은 두 데이터 세트 간 관계의 비모수 단조성 측정입니다. Pearson 상관 관계와 달리 Spearman 상관 관계는 두 데이터 세트가 모두 정규적으로 분포된다고 가정하지 않습니다. 다른 상관 계수처럼, 이 방식은 -1과 +1 사이에서 달라지며 0은 상관 관계가 없음을 의미합니다. - 1 또는 +1의 상관 관계는 정확히 단조성 관계를 의미합니다. 양의 상관 관계는 x가 증가할 때 y도 증가함을 의미합니다. 음의 상관 관계는 x가 증가할 때 y는 감소함을 의미합니다.|[계산](https://docs.scipy.org/doc/scipy-0.16.1/reference/generated/scipy.stats.spearmanr.html)|None|
mean_absolute_error|Mean absolute error(평균 절대 오차)는 목표와 예측 간 차이의 예상 절대값입니다.|[계산](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_absolute_error.html)|None|
normalized_mean_absolute_error|Normalized mean absolute error(정규화된 평균 절대 오차)는 평균 절대 오차를 데이터 범위로 나눈 것입니다.|[계산](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_absolute_error.html)|데이터 범위로 나누기|
median_absolute_error|Median absolute error(중앙값 절대 오차)는 목표와 예측 간 모든 절대 차이의 중앙값입니다. 이 손실은 이상값보다 강력합니다.|[계산](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.median_absolute_error.html)|None|
normalized_median_absolute_error|Normalized median absolute error(정규화된 중앙값 절대 오차)는 중앙값 절대 오차를 데이터 범위로 나눈 것입니다.|[계산](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.median_absolute_error.html)|데이터 범위로 나누기|
root_mean_squared_error|Root mean squared error(제곱 평균 오차)는 목표와 예측 간 예상 제곱 차이의 제곱근입니다.|[계산](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_squared_error.html)|None|
normalized_root_mean_squared_error|Normalized root mean squared error(정규화된 제곱 평균 오차)는 제곱 평균 오차를 데이터 범위로 나눈 것입니다.|[계산](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_squared_error.html)|데이터 범위로 나누기|
root_mean_squared_log_error|Root mean squared log error(제곱 평균 로그 오차)는 예상 제곱 로그 오차의 제곱근입니다.|[계산](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_squared_log_error.html)|None|
normalized_root_mean_squared_log_error|Noramlized Root mean squared log error(정규화된 제곱 평균 로그 오차)는 제곱 평균 로그 오차를 데이터 범위로 나눈 것입니다.|[계산](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_squared_log_error.html)|데이터 범위로 나누기|

### <a name="predicted-vs-true-chart"></a><a name="pvt"></a>예측 및 실제 차트
#### <a name="what-is-a-predicted-vs-true-chart"></a>예측 차트와 참 차트는 무엇입니까?
예측 과 True는 회귀 문제에 대한 예측 값과 상관 관계가 있는 참 값 간의 관계를 보여 주며, 이와 관련이 있습니다. 예측된 값이 y=x 선에 가까울수록 예측 모델의 정확도가 향상되기 때문에 이 그래프를 사용하여 모델의 성능을 측정할 수 있습니다.

#### <a name="what-does-automated-ml-do-with-the-predicted-vs-true-chart"></a>자동 ML은 예측 차트와 실제 차트에서 무엇을 합니까?
각 실행 후에 각 회귀 모델에 대한 예측 대 실제 그래프를 확인할 수 있습니다. 데이터 개인 정보를 보호하기 위해 값은 함께 bin 처리되고 각 bin의 크기가 차트 영역의 아래쪽에 막대형 그래프로 표시됩니다. 밝은 음영 영역을 통해 오차 범위를 표시하는 예측 모델과 모델의 이상적인 값을 비교할 수 있습니다.

#### <a name="what-does-a-good-model-look-like"></a>좋은 모델은 어떤 모습일까요?
##### <a name="example-1-a-classification-model-with-low-accuracy"></a>예 1: 정확도가 낮은 분류 모델
![예측에서 정확도가 낮은 회귀 모델](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-regression1.png)

##### <a name="example-2-a-regression-model-with-high-accuracy"></a>예 2: 높은 정확도의 회귀 모델 
[![예측에서 정확도가 높은 회귀 모델](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-regression2.png)](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-regression2-expanded.png)



### <a name="histogram-of-residuals-chart"></a><a name="histo"></a>잔차 의 히스토그램 차트
#### <a name="what-is-a-residuals-chart"></a>잔차 차트란 무엇입니까?
나머지는 관찰된 y - 예측된 y를 나타냅니다. 바이어스가 낮은 오차 범위를 표시하려면 나머지 히스토그램의 모양이 0을 중심으로 하는 벨 곡선이어야 합니다. 
#### <a name="what-does-automated-ml-do-with-the-residuals-chart"></a>자동 ML은 잔차 차트에서 무엇을 합니까?
자동 ML은 예측에서 오류 분포를 표시하는 잔차 차트를 자동으로 제공합니다.
#### <a name="what-does-a-good-model-look-like"></a>좋은 모델은 어떤 모습일까요?
양호한 모델은 일반적으로 종 곡선또는 0 주위에 오류가 있습니다.

##### <a name="example-1-a-regression-model-with-bias-in-its-errors"></a>예 1: 오류에 바이어스가 있는 회귀 모델
![오류에 바이어스가 있는 SA 회귀 모델](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-regression3.png)

##### <a name="example-2-a-regression-model-with-more-even-distribution-of-errors"></a>예 2: 오류의 균일한 분포가 있는 회귀 모델
![오류의 균일한 분포가 있는 회귀 모델](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-regression4.png)

## <a name="model-interpretability-and-feature-importance"></a><a name="explain-model"></a>모델 해석성 및 기능 중요성
자동화된 ML은 실행을 위한 기계 학습 해석 대시보드를 제공합니다.
해석 가능성 기능 사용에 대한 자세한 내용은 자동화된 ML 실험에서 해석 가능을 활성화하는 [방법을](how-to-machine-learning-interpretability-automl.md) 참조하십시오.

## <a name="next-steps"></a>다음 단계

+ Azure 기계 학습에서 [자동화된 ml에](concept-automated-ml.md) 대해 자세히 알아봅니다.
+ [자동화된 기계 학습 모델 설명](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/explain-model) 샘플 노트북을 사용해 보십시오.
