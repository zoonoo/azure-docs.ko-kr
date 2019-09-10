---
title: 자동화 된 ML 결과 이해
titleSuffix: Azure Machine Learning service
description: 자동화 된 각 기계 학습 실행에 대 한 차트 및 메트릭을 보고 이해 하는 방법에 대해 알아봅니다.
services: machine-learning
author: nilesha
ms.author: nilesha
ms.reviewer: sgilley
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 07/22/2019
ms.openlocfilehash: 60ecd71419bbf65cceab257cb97a96929d24ec08
ms.sourcegitcommit: 65131f6188a02efe1704d92f0fd473b21c760d08
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/10/2019
ms.locfileid: "70860551"
---
# <a name="understand-automated-machine-learning-results"></a>자동화 된 machine learning 결과 이해

이 문서에서는 자동화 된 각 기계 학습 실행에 대 한 차트 및 메트릭을 보고 이해 하는 방법에 대해 알아봅니다. 

다음에 대해 자세히 알아봅니다.
+ [분류 모델에 대 한 메트릭, 차트 및 곡선](#classification)
+ [회귀 모델에 대 한 메트릭, 차트 및 그래프](#regression)
+ [모델 interpretability 및 기능 중요도](#explain-model)

## <a name="prerequisites"></a>필수 구성 요소

* Azure 구독. Azure 구독이 없는 경우 시작하기 전에 체험 계정을 만듭니다. [Azure Machine Learning Service의 평가판 또는 유료 버전](https://aka.ms/AMLFree)을 지금 사용해 보세요.

* SDK를 사용 하 여 Azure Portal 또는 작업 영역 방문 페이지 (미리 보기)에서 자동화 된 machine learning 실험을 만듭니다.

    * SDK를 사용 하 여 [분류 모델](how-to-auto-train-remote.md) 또는 [회귀 모델](tutorial-auto-train-models.md) 작성
    * [Azure Portal 또는 작업 영역 방문 페이지 (미리 보기)](how-to-create-portal-experiments.md) 를 사용 하 여 적절 한 데이터를 업로드 하 여 분류 또는 회귀 모델을 만들 수 있습니다.

## <a name="view-the-run"></a>실행 보기

자동화 된 기계 학습 실험을 실행 한 후에는 machine learning 서비스 작업 영역에서 실행 기록을 찾을 수 있습니다. 

1. 작업 영역으로 이동합니다.

1. 작업 영역의 왼쪽 패널에서 **실험**을 선택 합니다.

   ![실험 메뉴 스크린샷](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-experiment-menu.png)

1. 실험 목록에서 탐색 하려는 실험을 선택 합니다.

   [![실험 목록](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-experiment-list.png)](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-experiment-list-expanded.png)

1. 아래쪽 테이블에서 **실행 번호**를 선택 합니다.

   [ 실험실행![](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-experiment-run.png)](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-experiment-run-expanded.png))

1. 반복 테이블에서 추가로 탐색 하려는 모델의 **반복 번호** 를 선택 합니다.

   [![실험 모델](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-experiment-model.png)](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-experiment-model-expanded.png)

`RunDetails` [Jupyter 위젯을](https://docs.microsoft.com/python/api/azureml-widgets/azureml.widgets?view=azure-ml-py)사용할 때 실행 중에도 동일한 결과가 표시 됩니다.

## <a name="classification"></a>분류 결과

다음 메트릭과 차트는의 자동화 된 기계 학습 기능을 사용 하 여 작성 하는 모든 분류 모델에 사용할 수 있습니다 Azure Machine Learning

+ [Metrics](#classification-metrics)(메트릭)
+ [혼동 행렬](#confusion-matrix)
+ [정밀도-리콜 차트](#precision-recall-chart)
+ [ROC(수신기 작동 특성)](#roc)
+ [리프트 곡선](#lift-curve)
+ [게인 곡선](#gains-curve)
+ [보정 플롯](#calibration-plot)

### <a name="classification-metrics"></a>분류 메트릭

다음 메트릭은 분류 태스크에 대 한 각 실행 반복에 저장 됩니다.

|메트릭|Description|계산|추가 매개 변수
--|--|--|--|
AUC_Macro| AUC는 Receiver Operating Characteristic Curve 아래의 영역입니다. Macro(매크로)는 각 클래스에 대한 AUC의 산술 평균입니다.  | [계산](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.roc_auc_score.html) | average="macro"|
AUC_Micro| AUC는 Receiver Operating Characteristic Curve 아래의 영역입니다. 마이크로는 각 클래스에서 참 긍정 및 거짓 긍정을 결합 하 여 전역적으로 계산 됩니다.| [계산](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.roc_auc_score.html) | average="micro"|
AUC_Weighted  | AUC는 Receiver Operating Characteristic Curve 아래의 영역입니다. 가중치는 각 클래스에 대 한 점수의 산술 평균 이며 각 클래스의 실제 인스턴스 수에 의해 가중치가 적용 됩니다.| [계산](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.roc_auc_score.html)|average="weighted"
accuracy|Accuracy(정확도)는 true 레이블과 정확히 일치하는 예측된 레이블의 백분율입니다. |[계산](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.accuracy_score.html) |없음|
average_precision_score_macro|Average precision(평균 정밀도)은 정밀도-재현율 곡선을 이전 임계값에서의 재현율 증가를 가중치로 사용하여 계산한 각 임계값에서 도달된 정밀도의 가중 평균으로 요약합니다. 매크로는 각 클래스의 평균 전체 자릿수 점수에 대 한 산술 평균입니다.|[계산](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.average_precision_score.html)|average="macro"|
average_precision_score_micro|Average precision(평균 정밀도)은 정밀도-재현율 곡선을 이전 임계값에서의 재현율 증가를 가중치로 사용하여 계산한 각 임계값에서 도달된 정밀도의 가중 평균으로 요약합니다. 마이크로는 각 구분에서 참 긍정 및 거짓 긍정을 결합 하 여 전역적으로 계산 됩니다.|[계산](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.average_precision_score.html)|average="micro"|
average_precision_score_weighted|Average precision(평균 정밀도)은 정밀도-재현율 곡선을 이전 임계값에서의 재현율 증가를 가중치로 사용하여 계산한 각 임계값에서 도달된 정밀도의 가중 평균으로 요약합니다. 가중치는 각 클래스에 대 한 평균 전체 자릿수 점수의 산술 평균 이며 각 클래스의 실제 인스턴스 수에 의해 가중치가 적용 됩니다.|[계산](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.average_precision_score.html)|average="weighted"|
balanced_accuracy|Balanced accuracy(균형 정확도)는 각 클래스 재현율의 산술 평균입니다.|[계산](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|average="macro"|
f1_score_macro|F1 score(F1 점수)는 정밀도 및 재현율의 조화 평균입니다. 매크로는 각 클래스에 대 한 F1 점수의 산술 평균입니다.|[계산](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.f1_score.html)|average="macro"|
f1_score_micro|F1 score(F1 점수)는 정밀도 및 재현율의 조화 평균입니다. 마이크로는 전체 참 긍정, 거짓 부정 및 거짓 긍정을 계산 하 여 전역적으로 계산 됩니다.|[계산](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.f1_score.html)|average="micro"|
f1_score_weighted|F1 score(F1 점수)는 정밀도 및 재현율의 조화 평균입니다. Weighted(가중치)는 각 클래스에 대한 F1 점수의 클래스 빈도별 평균입니다.|[계산](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.f1_score.html)|average="weighted"|
log_loss|이 함수는 (다항) 로지스틱 회귀와 그 확장(예: 신경망)에 사용되는 손실 함수로, 지정된 확률적 분류자 예측에 대한 true 레이블의 음수 로그 유사도로 정의됩니다. 에서 {0,1} true 레이블 yt를 사용 하는 단일 샘플 및 yt = 1 인 예상 확률 yt의 경우 로그 손실은-log P (yt&#124;yt) =-(yt 로그 (yt) + (1-yt) 로그 (1-yt))입니다.|[계산](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.log_loss.html)|없음|
norm_macro_recall|Normalized Macro Recall(정규화된 매크로 재현율)은 임의 성능 점수가 0이고 완벽한 성능 점수가 1이 되도록 정규화된 매크로 재현율입니다. 이는 norm_macro_recall: = (recall_score_macro)/(1-R)로 구현 됩니다. 여기서 R은 임의 예측에 대해 예상 되는 recall_score_macro 값입니다 (예: 이진 분류의 경우 R = 0.5, C 클래스 분류 문제의 경우 R = (1/C)).|[계산](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|average = "매크로" |
precision_score_macro|Precision(정밀도)은 실제로 해당 클래스에 있는 특정 클래스로 레이블이 지정된 요소의 백분율입니다. 매크로는 각 클래스에 대 한 전체 자릿수의 산술 평균입니다.|[계산](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.precision_score.html)|average="macro"|
precision_score_micro|Precision(정밀도)은 실제로 해당 클래스에 있는 특정 클래스로 레이블이 지정된 요소의 백분율입니다. 마이크로는 총 참 긍정 및 거짓 긍정을 계산 하 여 전역적으로 계산 됩니다.|[계산](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.precision_score.html)|average="micro"|
precision_score_weighted|Precision(정밀도)은 실제로 해당 클래스에 있는 특정 클래스로 레이블이 지정된 요소의 백분율입니다. 가중치가 적용 되는 각 클래스에 대 한 전체 자릿수의 산술 평균은 각 클래스의 실제 인스턴스 수로 가중치를 지정 합니다.|[계산](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.precision_score.html)|average="weighted"|
recall_score_macro|Recall(재현율)은 특정 클래스에서 올바르게 레이블이 지정된 요소의 백분율입니다. 매크로는 각 클래스에 대 한 회수의 산술 평균입니다.|[계산](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|average="macro"|
recall_score_micro|Recall(재현율)은 특정 클래스에서 올바르게 레이블이 지정된 요소의 백분율입니다. 마이크로는 총 참 긍정, 거짓 부정 및 거짓 긍정을 계산 하 여 전역적으로 계산 됩니다.|[계산](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|average="micro"|
recall_score_weighted|Recall(재현율)은 특정 클래스에서 올바르게 레이블이 지정된 요소의 백분율입니다. 가중치는 각 클래스에 대 한 최대 회수의 산술 평균 이며 각 클래스의 실제 인스턴스 수로 가중치가 지정 됩니다.|[계산](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|average="weighted"|
weighted_accuracy|가중치가 적용 되는 정확도는 각 예제에 지정 된 가중치가 해당 예제의 true 클래스에서 true 인스턴스의 비율과 같음을 나타내는 정확도입니다.|[계산](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.accuracy_score.html)|sample_weight는 대상의 각 요소에 대한 해당 클래스의 비율과 동일한 벡터입니다.|

### <a name="confusion-matrix"></a>혼동 행렬

혼동 행렬은 분류 모델의 성능을 설명하는 데 사용됩니다. 각 행은 true 클래스 인스턴스를 표시하고, 각 열은 예측된 클래스 인스턴스를 나타냅니다. 혼동 행렬은 지정된 모델에 대해 올바르게 분류된 레이블과 잘못 분류된 레이블을 표시합니다.

분류 문제를 위해 Azure Machine Learning은 빌드된 각 모델에 대한 혼동 행렬을 자동으로 제공합니다. 각 혼동 행렬에 대해 자동화 된 ML은 각 예측 된 레이블의 빈도와 각 진정한 레이블 교차를 표시 합니다. 이 어두운 색은 행렬의 특정 부분에서 더 높은 수입니다. 가장 이상적인 색은 행렬의 대각선을 따라 하는 것입니다. 

예제 1: 정확성이 낮은 분류 모델 ![정확성이 낮은 분류 모델](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-confusion-matrix1.png)

예제 2: 정확성이 높은 분류 모델(이상적) ![정확성이 높은 분류 모델](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-confusion-matrix2.png)


### <a name="precision-recall-chart"></a>정밀도-리콜 차트

이 차트를 사용하면 각 모델의 정밀도-리콜 곡선을 비교하여 특정 비즈니스 문제에 대해 정밀도와 리콜 간에 허용되는 관계가 있는 모델을 확인할 수 있습니다. 이 차트에는 매크로 평균 정밀도-리콜, 마이크로 평균 정밀도-리콜 및 모델의 모든 클래스와 관련된 정밀도-리콜이 표시됩니다.

정밀도라는 용어는 분류자가 모든 인스턴스에 올바른 레이블을 지정할 수 있는 기능을 나타냅니다. 리콜은 분류자가 특정 레이블의 인스턴스를 모두 찾을 수 있는 기능을 나타냅니다. 정밀도-리콜 곡선은 이러한 두 개념 간의 관계를 표시합니다. 모델이 100% 정밀도와 100% 정확도를 갖는 것이 이상적입니다.

예제 1: 정밀도와 리콜이 낮은 분류 모델 ![정밀도와 리콜이 낮은 분류 모델](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-precision-recall1.png)

예제 2: ~100% 정밀도 및 ~100% 리콜인 분류 모델(이상적) ![정밀도와 리콜이 높은 분류 모델](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-precision-recall2.png)

### <a name="roc"></a>ROC

ROC(수신기 작동 특성)는 특정 모델에 대해 올바르게 분류된 레이블 및 잘못 분류된 레이블의 플롯입니다. ROC 곡선은 가양성 레이블을 표시하지 않으므로 바이어스가 높은 데이터 세트에서 모델을 학습할 때는 제공하는 정보가 적을 수 있습니다.

예제 1: 올바른 레이블이 낮고 잘못된 레이블이 높은 분류 모델 ![올바른 레이블이 낮고 잘못된 레이블이 높은 분류 모델](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-roc-1.png)

예제 2: 올바른 레이블이 높고 잘못된 레이블이 낮은 분류 모델 ![올바른 레이블이 높고 잘못된 레이블이 낮은 분류 모델](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-roc-2.png)

### <a name="lift-curve"></a>리프트 곡선

특정 모델의 값 게인을 보기 위해 Azure Machine Learning을 사용하여 자동으로 빌드된 모델 리프트를 기준선과 비교할 수 있습니다.

리프트 차트는 분류 모델의 성능을 평가하는 데 사용됩니다. 모델을 사용하지 않을 경우와 비교해서 모델을 사용할 경우 예상할 수 있는 성능 향상을 보여 줍니다. 

예제 1: 임의 선택 모델보다 모델 성능이 더 낮음 ![임의 선택 모델보다 성능이 더 낮은 분류 모델](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-lift-curve1.png)

예제 2: 임의 선택 모델보다 모델 성능이 더 높음 ![성능이 더 높은 분류 모델](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-lift-curve2.png)

### <a name="gains-curve"></a>게인 곡선

게인 차트는 데이터의 각 부분별로 분류 모델의 성능을 평가합니다. 데이터 세트의 각 백분위수에 대해, 임의 선택 모델과 비교해서 예상할 수 있는 성능 향상을 보여 줍니다.

누적 게인 차트를 사용하면 모델에서 원하는 게인에 해당하는 백분율을 사용하여 분류 경계를 선택하는 데 도움이 됩니다. 이 정보는 동반되는 리프트 차트의 결과를 살펴보는 또 다른 방법을 제공합니다.

예제 1: 최소 게인의 분류 모델 ![최소 게인의 분류 모델](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-gains-curve1.png)

예제 2: 상당한 게인의 분류 모델 ![상당한 게인의 분류 모델](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-gains-curve2.png)

### <a name="calibration-plot"></a>보정 플롯

모든 분류 문제에 대해 마이크로 평균, 매크로 평균 및 지정된 예측 모델의 각 클래스의 보정 선을 검토할 수 있습니다. 

보정 플롯은 예측 모델의 신뢰도를 표시하는 데 사용됩니다. 이 작업을 위해 예측 확률과 실제 확률 간의 관계를 보여 주며, 여기서 “확률”은 특정 인스턴스가 일부 레이블에서 속할 가능성을 나타냅니다. 잘 보정된 모델은 y=x 선과 일치하며, 이 경우 해당 예측의 신뢰도가 상당히 높음을 의미합니다. 신뢰도가 과도한 모델은 y=0 선과 일치하며, 이 경우 예측된 확률은 있지만 실제 확률이 없음을 의미합니다.

예제 1: 잘 보정된 모델 ![ 잘 보정된 모델](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-calib-curve1.png)

예제 2: 신뢰도가 과도한 모델 ![신뢰도가 과도한 모델](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-calib-curve2.png)

## <a name="regression"></a>회귀 결과

다음 메트릭과 차트는의 자동화 된 기계 학습 기능을 사용 하 여 작성 하는 모든 회귀 모델에 사용할 수 있습니다 Azure Machine Learning

+ [Metrics](#reg-metrics)(메트릭)
+ [예측 대 실제](#pvt)
+ [나머지 히스토그램](#histo)


### <a name="reg-metrics"></a>회귀 메트릭

회귀 또는 예측 작업에 대해 각 실행 반복에 다음 메트릭이 저장 됩니다.

|메트릭|Description|계산|추가 매개 변수
--|--|--|--|
explained_variance|Explained variance(설명된 분산)는 수학 모델에서 지정된 데이터 세트의 편차가 고려되는 비율입니다. 오차 분산에 대한 원래 데이터의 분산 감소율입니다. 오차의 평균이 0이면 explained variance와 같습니다.|[계산](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.explained_variance_score.html)|없음|
r2_score|R2는 평균을 출력하는 기준선 모델과 비교한 제곱 오차의 결정 계수 또는 환원율입니다. |[계산](https://scikit-learn.org/0.16/modules/generated/sklearn.metrics.r2_score.html)|없음|
spearman_correlation|Spearman correlation(Spearman 상관 관계)은 두 데이터 세트 간 관계의 비모수 단조성 측정입니다. Pearson 상관 관계와 달리 Spearman 상관 관계는 두 데이터 세트가 모두 정규적으로 분포된다고 가정하지 않습니다. 다른 상관 계수처럼, 이 방식은 -1과 +1 사이에서 달라지며 0은 상관 관계가 없음을 의미합니다. \- 1 또는 +1의 상관 관계는 정확히 단조성 관계를 의미합니다. 양의 상관 관계는 x가 증가할 때 y도 증가함을 의미합니다. 음의 상관 관계는 x가 증가할 때 y는 감소함을 의미합니다.|[계산](https://docs.scipy.org/doc/scipy-0.16.1/reference/generated/scipy.stats.spearmanr.html)|없음|
mean_absolute_error|Mean absolute error(평균 절대 오차)는 목표와 예측 간 차이의 예상 절대값입니다.|[계산](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_absolute_error.html)|없음|
normalized_mean_absolute_error|Normalized mean absolute error(정규화된 평균 절대 오차)는 평균 절대 오차를 데이터 범위로 나눈 것입니다.|[계산](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_absolute_error.html)|데이터 범위로 나누기|
median_absolute_error|Median absolute error(중앙값 절대 오차)는 목표와 예측 간 모든 절대 차이의 중앙값입니다. 이 손실은 이상값보다 강력합니다.|[계산](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.median_absolute_error.html)|없음|
normalized_median_absolute_error|Normalized median absolute error(정규화된 중앙값 절대 오차)는 중앙값 절대 오차를 데이터 범위로 나눈 것입니다.|[계산](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.median_absolute_error.html)|데이터 범위로 나누기|
root_mean_squared_error|Root mean squared error(제곱 평균 오차)는 목표와 예측 간 예상 제곱 차이의 제곱근입니다.|[계산](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_squared_error.html)|없음|
normalized_root_mean_squared_error|Normalized root mean squared error(정규화된 제곱 평균 오차)는 제곱 평균 오차를 데이터 범위로 나눈 것입니다.|[계산](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_squared_error.html)|데이터 범위로 나누기|
root_mean_squared_log_error|Root mean squared log error(제곱 평균 로그 오차)는 예상 제곱 로그 오차의 제곱근입니다.|[계산](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_squared_log_error.html)|없음|
normalized_root_mean_squared_log_error|Noramlized Root mean squared log error(정규화된 제곱 평균 로그 오차)는 제곱 평균 로그 오차를 데이터 범위로 나눈 것입니다.|[계산](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_squared_log_error.html)|데이터 범위로 나누기|

### <a name="pvt"></a>예측 및 True

예측 대 실제는 회귀 문제에 대한 예측된 값과 상호 연관된 실제 값 사이의 관계를 보여 줍니다. 예측된 값이 y=x 선에 가까울수록 예측 모델의 정확도가 향상되기 때문에 이 그래프를 사용하여 모델의 성능을 측정할 수 있습니다.

각 실행 후에 각 회귀 모델에 대한 예측 대 실제 그래프를 확인할 수 있습니다. 데이터 개인 정보를 보호하기 위해 값은 함께 bin 처리되고 각 bin의 크기가 차트 영역의 아래쪽에 막대형 그래프로 표시됩니다. 밝은 음영 영역을 통해 오차 범위를 표시하는 예측 모델과 모델의 이상적인 값을 비교할 수 있습니다.

예제 1: 예측 정확도가 낮은 회귀 모델 ![예측 정확도가 낮은 회귀 모델](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-regression1.png)

예제 2: 정확도가 높은 회귀 모델 예측의 정확도가 높은 회귀 [ ![모델](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-regression2.png)](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-regression2-expanded.png)



### <a name="histo"></a>잔차의 히스토그램

나머지는 관찰된 y - 예측된 y를 나타냅니다. 바이어스가 낮은 오차 범위를 표시하려면 나머지 히스토그램의 모양이 0을 중심으로 하는 벨 곡선이어야 합니다. 

예제 1: 오차에 바이어스가 있는 회귀 모델 ![오차에 바이어스가 있는 회귀 모델](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-regression3.png)

예제 2: 오차 분포가 더 균일한 회귀 모델 ![오차 분포가 더 균일한 회귀 모델](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-regression4.png)

## <a name="explain-model"></a>모델 interpretability 및 기능 중요도

기능 중요도를 사용 하면 각 기능이 모델 생성에 얼마나 중요 한지 확인할 수 있습니다. 이 계산은 실행 시간을 크게 증가 시킬 수 있으므로 기본적으로 해제 되어 있습니다.   모든 모델에 대 한 모델 설명을 사용 하도록 설정 하거나 가장 적합 한 모델만 설명할 수 있습니다.

모델 전체 및 예측 모델의 클래스별로 기능 중요도 점수를 검토할 수 있습니다. 각 클래스 및 전체와 비교해서 기능별로 중요도를 확인할 수 있습니다.

![기능 설명 기능](./media/how-to-understand-automated-ml/feature-importance.gif)

Interpretability 기능을 사용 하는 방법에 대 한 자세한 내용은 [Python에서 자동화 된 ML 실험 구성](how-to-configure-auto-train.md#explain-the-model-interpretability)을 참조 하세요.  최상의 모델을 설명 하는 예제는 [최상의 모델 설명](how-to-auto-train-remote.md#explain)을 참조 하세요.

## <a name="next-steps"></a>다음 단계

+ Azure Machine Learning에서 [자동화 된 ml](concept-automated-ml.md) 에 대해 자세히 알아보세요.
+ 자동화 된 [Machine Learning 모델 설명](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning/model-explanation) 샘플 노트북을 사용해 보세요.
