---
title: 자동화 된 ML 결과 이해
titleSuffix: Azure Machine Learning
description: 자동화 된 각 기계 학습 실행에 대 한 차트 및 메트릭을 보고 이해 하는 방법에 대해 알아봅니다.
services: machine-learning
author: aniththa
ms.author: anumamah
ms.reviewer: nibaccam
ms.service: machine-learning
ms.subservice: core
ms.date: 12/05/2019
ms.topic: conceptual
ms.custom: how-to
ms.openlocfilehash: f0a41aa062cf4804587b97ce224f80c0bc4bf2b3
ms.sourcegitcommit: 3be3537ead3388a6810410dfbfe19fc210f89fec
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/10/2020
ms.locfileid: "89650577"
---
# <a name="understand-automated-machine-learning-results"></a>자동화 된 machine learning 결과 이해
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

이 문서에서는 자동화 된 각 기계 학습 실행에 대 한 차트 및 메트릭을 보고 이해 하는 방법에 대해 알아봅니다. 

다음에 대해 자세히 알아보세요.
+ [분류 모델에 대 한 메트릭 및 차트](#classification)
+ [회귀 모델에 대 한 메트릭 및 차트](#regression)
+ [모델 interpretability 및 기능 중요도](#explain-model)

## <a name="prerequisites"></a>필수 조건

* Azure 구독 Azure 구독이 없는 경우 시작하기 전에 체험 계정을 만듭니다. 지금 [Azure Machine Learning 평가판 또는 유료 버전](https://aka.ms/AMLFree)을 사용해 보세요.

* SDK 또는 Azure Machine Learning studio에서 자동화 된 machine learning 실행에 대 한 실험을 만듭니다.

    * SDK를 사용 하 여 [분류 모델](how-to-auto-train-remote.md) 또는 [회귀 모델](tutorial-auto-train-models.md) 작성
    * [Azure Machine Learning studio](how-to-use-automated-ml-for-ml-models.md) 를 사용 하 여 적절 한 데이터를 업로드 하 여 분류 또는 회귀 모델을 만듭니다.

## <a name="view-the-run"></a>실행 보기

자동화 된 기계 학습 실험을 실행 한 후에는 machine learning 작업 영역에서 실행 기록을 찾을 수 있습니다. 

1. 작업 영역으로 이동합니다.

1. 작업 영역의 왼쪽 패널에서 **실험**을 선택 합니다.

   ![실험 메뉴 스크린샷](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-experiment-menu.png)

1. 실험 목록에서 탐색 하려는 실험을 선택 합니다.

   [![실험 목록](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-experiment-list.png)](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-experiment-list-expanded.png)

1. 아래쪽 테이블에서 **실행**을 선택 합니다.

   [ ![ 실험 실행](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-experiment-run.png)](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-experiment-run-expanded.png))

1. 모델에서 더 탐색 하려는 모델의 **알고리즘 이름을** 선택 합니다.

   [![실험 모델](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-experiment-model.png)](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-experiment-model-expanded.png)

`RunDetails` [Jupyter 위젯을](https://docs.microsoft.com/python/api/azureml-widgets/azureml.widgets?view=azure-ml-py&preserve-view=true)사용할 때 실행 중에도 동일한 결과가 표시 됩니다.

## <a name="classification-results"></a><a name="classification"></a> 분류 결과

다음 메트릭과 차트는의 자동화 된 기계 학습 기능을 사용 하 여 작성 하는 모든 분류 모델에 사용할 수 있습니다 Azure Machine Learning

+ [메트릭](#classification-metrics)
+ [혼동 행렬](#confusion-matrix)
+ [전체 자릿수-회수 차트](#precision-recall-chart)
+ [ROC(수신기 작동 특성)](#roc)
+ [리프트 곡선](#lift-curve)
+ [게인 곡선](#gains-curve)
+ [보정 플롯](#calibration-plot)

### <a name="classification-metrics"></a>분류 메트릭

다음 메트릭은 분류 태스크에 대 한 각 실행 반복에 저장 됩니다.

메트릭|Description|계산|추가 매개 변수
--|--|--|--
AUC_macro| AUC는 Receiver Operating Characteristic Curve 아래의 영역입니다. Macro(매크로)는 각 클래스에 대한 AUC의 산술 평균입니다.  | [변동이](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.roc_auc_score.html) | average="macro"|
AUC_micro| AUC는 Receiver Operating Characteristic Curve 아래의 영역입니다. 마이크로는 각 클래스에서 참 긍정 및 거짓 긍정을 결합 하 여 전역적으로 계산 됩니다.| [변동이](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.roc_auc_score.html) | average="micro"|
AUC_weighted  | AUC는 Receiver Operating Characteristic Curve 아래의 영역입니다. 가중치는 각 클래스에 대 한 점수의 산술 평균 이며 각 클래스의 실제 인스턴스 수에 의해 가중치가 적용 됩니다.| [변동이](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.roc_auc_score.html)|average="weighted"
accuracy|Accuracy(정확도)는 true 레이블과 정확히 일치하는 예측된 레이블의 백분율입니다. |[변동이](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.accuracy_score.html) |없음|
average_precision_score_macro|Average precision(평균 정밀도)은 정밀도-재현율 곡선을 이전 임계값에서의 재현율 증가를 가중치로 사용하여 계산한 각 임계값에서 도달된 정밀도의 가중 평균으로 요약합니다. 매크로는 각 클래스의 평균 전체 자릿수 점수에 대 한 산술 평균입니다.|[변동이](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.average_precision_score.html)|average="macro"|
average_precision_score_micro|Average precision(평균 정밀도)은 정밀도-재현율 곡선을 이전 임계값에서의 재현율 증가를 가중치로 사용하여 계산한 각 임계값에서 도달된 정밀도의 가중 평균으로 요약합니다. 마이크로는 각 구분에서 참 긍정 및 거짓 긍정을 결합 하 여 전역적으로 계산 됩니다.|[변동이](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.average_precision_score.html)|average="micro"|
average_precision_score_weighted|Average precision(평균 정밀도)은 정밀도-재현율 곡선을 이전 임계값에서의 재현율 증가를 가중치로 사용하여 계산한 각 임계값에서 도달된 정밀도의 가중 평균으로 요약합니다. 가중치는 각 클래스에 대 한 평균 전체 자릿수 점수의 산술 평균 이며 각 클래스의 실제 인스턴스 수에 의해 가중치가 적용 됩니다.|[변동이](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.average_precision_score.html)|average="weighted"|
balanced_accuracy|Balanced accuracy(균형 정확도)는 각 클래스 재현율의 산술 평균입니다.|[변동이](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|average="macro"|
f1_score_macro|F1 score(F1 점수)는 정밀도 및 재현율의 조화 평균입니다. 매크로는 각 클래스에 대 한 F1 점수의 산술 평균입니다.|[변동이](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.f1_score.html)|average="macro"|
f1_score_micro|F1 score(F1 점수)는 정밀도 및 재현율의 조화 평균입니다. 마이크로는 전체 참 긍정, 거짓 부정 및 거짓 긍정을 계산 하 여 전역적으로 계산 됩니다.|[변동이](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.f1_score.html)|average="micro"|
f1_score_weighted|F1 score(F1 점수)는 정밀도 및 재현율의 조화 평균입니다. Weighted(가중치)는 각 클래스에 대한 F1 점수의 클래스 빈도별 평균입니다.|[변동이](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.f1_score.html)|average="weighted"|
log_loss|이 함수는 확률 분류자의 예측이 지정 된 경우 진정한 레이블의 부정적 로그 가능성으로 정의 된 신경망 등의 다항 () 로지스틱 회귀 및 확장에서 사용 되는 손실 함수입니다. 에서 true 레이블 yt를 사용 하는 단일 샘플 {0,1} 및 yt = 1 인 예상 확률 yt는 로그 손실은-Log P (yt&#124;yt) =-(yt log (yt) + (1-yt) log (1-yt))입니다.|[변동이](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.log_loss.html)|없음|
norm_macro_recall|Normalized Macro Recall(정규화된 매크로 재현율)은 임의 성능 점수가 0이고 완벽한 성능 점수가 1이 되도록 정규화된 매크로 재현율입니다. 이는 norm_macro_recall: = (recall_score_macro-R)/(1-R)로 구현 됩니다. 여기서 R은 임의 예측에 대해 예상 되는 recall_score_macro 값 (예: 이진 분류의 경우 R = 0.5, C 클래스 분류 문제의 경우 R = (1/C))입니다.|[변동이](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|average = "매크로" |
precision_score_macro|전체 자릿수는 레이블이 올바르게 지정 된 예측 요소의 백분율입니다. 매크로는 각 클래스에 대 한 전체 자릿수의 산술 평균입니다.|[변동이](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.precision_score.html)|average="macro"|
precision_score_micro|전체 자릿수는 레이블이 올바르게 지정 된 예측 요소의 백분율입니다. 마이크로는 총 참 긍정 및 거짓 긍정을 계산 하 여 전역적으로 계산 됩니다.|[변동이](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.precision_score.html)|average="micro"|
precision_score_weighted|전체 자릿수는 레이블이 올바르게 지정 된 예측 요소의 백분율입니다. 가중치가 적용 되는 각 클래스에 대 한 전체 자릿수의 산술 평균은 각 클래스의 실제 인스턴스 수로 가중치를 지정 합니다.|[변동이](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.precision_score.html)|average="weighted"|
recall_score_macro|회수는 특정 클래스의 요소에 레이블이 올바르게 지정 된 비율입니다. 매크로는 각 클래스에 대 한 회수의 산술 평균입니다.|[변동이](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|average="macro"|
recall_score_micro|회수는 특정 클래스의 요소에 레이블이 올바르게 지정 된 비율입니다. 마이크로는 총 참 긍정, 거짓 부정 및 거짓 긍정을 계산 하 여 전역적으로 계산 됩니다.|[변동이](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|average="micro"|
recall_score_weighted|회수는 특정 클래스의 요소에 레이블이 올바르게 지정 된 비율입니다. 가중치는 각 클래스에 대 한 최대 회수의 산술 평균 이며 각 클래스의 실제 인스턴스 수로 가중치가 지정 됩니다.|[변동이](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|average="weighted"|
weighted_accuracy|가중치가 적용 되는 정확도는 각 예제에 지정 된 가중치가 해당 예제의 true 클래스에서 true 인스턴스의 비율과 같음을 나타내는 정확도입니다.|[변동이](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.accuracy_score.html)|sample_weight는 대상의 각 요소에 대한 해당 클래스의 비율과 동일한 벡터입니다.|

### <a name="binary-vs-multiclass-metrics"></a>Binary 및 다중 클래스 메트릭

AutoML은 이진 메트릭과 다중 클래스 메트릭을 구별 하지 않습니다. 데이터 집합에 두 개 이상의 클래스가 있는지 여부와 상관 없이 동일한 유효성 검사 메트릭이 보고 됩니다. 그러나 일부 메트릭은 다중 클래스 분류를 위한 것입니다. 이러한 메트릭은 이진 데이터 집합에 적용 될 때 클래스를 클래스로 처리 하지 않습니다 `true` . 다중 클래스에 대해 명확 하 게 사용 되는 메트릭은 `micro` , `macro` 또는로 시작 `weighted` 합니다. 예를 들면,,, `average_precision_score` `f1_score` 및가 `precision_score` `recall_score` `AUC` 있습니다.

구체적인 예제에서는 다중 클래스를 계산 하는 대신 `tp / (tp + fn)` `micro` `macro` `weighted` 이진 분류 데이터 집합의 두 클래스에 대 한 평균 회수 (, 또는) 평균 회수를 계산 하는 대신 보다 명확 하 게 구분 합니다. 이는 클래스와 클래스에 대 한 회수를 개별적으로 계산한 `true` `false` 다음 두 클래스의 평균을 가져오는 것과 같습니다.

<a name="confusion-matrix"></a>

### <a name="confusion-matrix"></a>혼동 행렬

#### <a name="what-is-a-confusion-matrix"></a>혼동 행렬 이란?
혼동 행렬은 분류 모델의 성능을 설명하는 데 사용됩니다. 각 행은 데이터 집합의 실제 클래스 또는 실제 클래스의 인스턴스를 표시 하 고, 각 열은 모델에 의해 예측 된 클래스의 인스턴스를 나타냅니다. 

#### <a name="what-does-automated-ml-do-with-the-confusion-matrix"></a>자동 ML에서 혼동 행렬을 사용 하 여 수행 하는 작업은 무엇 인가요?
분류 문제를 위해 Azure Machine Learning은 빌드된 각 모델에 대한 혼동 행렬을 자동으로 제공합니다. 각 혼동 행렬에 대해 자동화 된 ML은 true 레이블 (행)과 비교 하 여 예측 된 각 레이블 (열)의 빈도를 표시 합니다. 이 어두운 색은 행렬의 특정 부분에서 더 높은 수입니다. 

#### <a name="what-does-a-good-model-look-like"></a>좋은 모델의 모양은 무엇입니까?
모델에서 제공 하는 예측 값과 데이터 집합의 실제 값을 비교 합니다. 이로 인해 모델의 값 대부분이 대각선을 따라 증가 하는 경우 기계 학습 모델의 정확도는 높아집니다. 즉, 모델이 올바른 값을 예측 합니다. 모델에 불균형 클래스가 있는 경우 혼동 행렬은 편향 모델을 검색 하는 데 도움이 됩니다.

##### <a name="example-1-a-classification-model-with-poor-accuracy"></a>예 1: 정확도가 낮은 분류 모델
![정확도가 낮은 분류 모델](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-confusion-matrix1.png)

##### <a name="example-2-a-classification-model-with-high-accuracy"></a>예 2: 정확도가 높은 분류 모델 
![정확도가 높은 분류 모델](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-confusion-matrix2.png)

##### <a name="example-3-a-classification-model-with-high-accuracy-and-high-bias-in-model-predictions"></a>예 3: 모델 예측에서 정확도 및 높은 바이어스가 높은 분류 모델
![모델 예측의 정확도 및 높은 바이어스가 높은 분류 모델](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-biased-model.png)

<a name="precision-recall-chart"></a>
### <a name="precision-recall-chart"></a>정밀도-리콜 차트
#### <a name="what-is-a-precision-recall-chart"></a>전체 자릿수 리콜 차트 란?
전체 자릿수 리콜 곡선은 전체 자릿수와 모델 회수 간의 관계를 보여 줍니다. 용어 전체 자릿수는 모델에서 모든 인스턴스의 레이블을 올바르게 표시 하는 기능을 나타냅니다. 리콜은 분류자가 특정 레이블의 인스턴스를 모두 찾을 수 있는 기능을 나타냅니다.

#### <a name="what-does-automated-ml-do-with-the-precision-recall-chart"></a>자동 ML은 전체 자릿수 리콜 차트를 어떻게 사용 하나요?

이 차트를 사용하면 각 모델의 정밀도-리콜 곡선을 비교하여 특정 비즈니스 문제에 대해 정밀도와 리콜 간에 허용되는 관계가 있는 모델을 확인할 수 있습니다. 이 차트에는 매크로 평균 정밀도-리콜, 마이크로 평균 정밀도-리콜 및 모델의 모든 클래스와 관련된 정밀도-리콜이 표시됩니다. 

매크로-평균은 각 클래스와 별개로 메트릭을 계산 하 고 평균을 취하여 모든 클래스를 동일 하 게 처리 합니다. 그러나 마이크로 평균은 평균 계산을 위해 모든 클래스의 기여를 집계 합니다. 데이터 집합에 클래스 불균형이 있는 경우에는 마이크로 평균이 좋습니다.

#### <a name="what-does-a-good-model-look-like"></a>좋은 모델의 모양은 무엇입니까?
비즈니스 문제의 목표에 따라 이상적인 전체 자릿수 리콜 곡선이 다를 수 있습니다. 몇 가지 예가 아래에 나와 있습니다.

##### <a name="example-1-a-classification-model-with-low-precision-and-low-recall"></a>예 1: 낮은 정밀도 및 낮은 회수를 포함 하는 분류 모델
![낮은 정밀도 및 낮은 회수를 포함 하는 분류 모델](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-precision-recall1.png)

##### <a name="example-2-a-classification-model-with-100-precision-and-100-recall"></a>예 2: ~ 100% precision 및 ~ 100% 회수를 사용 하는 분류 모델 
![분류 모델 높은 전체 자릿수 및 회수](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-precision-recall2.png)
<a name="roc"></a>
### <a name="roc-chart"></a>ROC 차트

#### <a name="what-is-a-roc-chart"></a>ROC 차트 란?
받는 사람 운영 특성 (또는 ROC)은 정확 하 게 분류 된 레이블과 특정 모델에 대해 잘못 분류 된 레이블의 그림입니다. ROC 곡선은 과반수 클래스가 소수 클래스에서 기여를 drown 수 있으므로 높은 클래스가 불균형 한 데이터 집합에서 모델을 학습 하는 경우 더 적을 수 있습니다.

#### <a name="what-does-automated-ml-do-with-the-roc-chart"></a>자동화 된 ML은 ROC 차트를 사용 하 여 수행 하는 작업은 무엇 인가요?
ROC 차트 아래의 영역을 올바르게 분류 된 샘플의 비율로 시각화할 수 있습니다. ROC 차트의 고급 사용자는 곡선 아래의 영역을 벗어나 서 참 긍정 및 거짓 긍정 요금에 대 한 intuition를 분류 임계값 또는 의사 결정 경계의 함수로 가져올 수 있습니다.

#### <a name="what-does-a-good-model-look-like"></a>좋은 모델의 모양은 무엇입니까?
왼쪽 위 모퉁이에 100% true 긍정 rate와 0% false 긍정 전송률이 가장 적합 한 ROC 곡선은 가장 적합 한 모델입니다. 임의 모델은 왼쪽 아래에서 오른쪽 위 모서리로 직선으로 표시 됩니다. 무작위 보다 나쁨은 y = x 줄 아래로 dip가 됩니다.

##### <a name="example-1-a-classification-model-with-low-true-labels-and-high-false-labels"></a>예 1: 작은 레이블 및 상위 거짓 레이블이 있는 분류 모델
![작은 레이블 및 상위 거짓 레이블이 있는 분류 모델](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-roc-1.png)

##### <a name="example-2-a-classification-model-with-high-true-labels-and-low-false-labels"></a>예 2: 중요 한 레이블 및 하위 false 레이블이 있는 분류 모델
![매우 중요 한 레이블 및 하위 false 레이블이 있는 분류 모델](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-roc-2.png)
<a name="lift-curve"></a>
### <a name="lift-chart"></a>리프트 차트
#### <a name="what-is-a-lift-chart"></a>리프트 차트 란?
리프트 차트는 분류 모델의 성능을 평가 하는 데 사용 됩니다. 리프트 차트는 임의의 모델에 비해 모델의 성능을 향상 시키는 방법을 보여 줍니다. 이를 통해 클래스의 수를 늘리는 것으로 분류 하기가 더 어려워집니다. 난수 모델에서는 두 개의 클래스가 있는 데이터 집합에 비해 10 개의 클래스가 있는 데이터 집합에서 더 많은 샘플을 잘못 예측 합니다.

#### <a name="what-does-automated-ml-do-with-the-lift-chart"></a>자동 ML은 리프트 차트를 사용 하 여 수행 하는 작업은 무엇 인가요?
특정 모델의 값 게인을 보기 위해 Azure Machine Learning을 사용하여 자동으로 빌드된 모델 리프트를 기준선과 비교할 수 있습니다.
#### <a name="what-does-a-good-model-look-like"></a>좋은 모델의 모양은 무엇입니까?

##### <a name="example-1-a-classification-model-that-does-worse-than-a-random-selection-model"></a>예 1: 임의 선택 모델 보다 더 나쁜 분류 모델
![임의 선택 모델 보다 더 나쁜 분류 모델](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-lift-curve1.png)
##### <a name="example-2-a-classification-model-that-performs-better-than-a-random-selection-model"></a>예 2: 임의 선택 모델 보다 더 나은 성능을 발휘 하는 분류 모델
![더 효과적으로 수행 되는 분류 모델](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-lift-curve2.png)
<a name="gains-curve"></a>
### <a name="cumulative-gains-chart"></a>누적 게인 차트
#### <a name="what-is-a-cumulative-gains-chart"></a>누적 게인 차트 란 무엇 인가요?

누적 게인 차트는 데이터의 각 부분에 따라 분류 모델의 성능을 평가 합니다. 차트는 데이터 집합의 각 백분위 수에 대해 정확 하 게 분류 된 샘플 수를 표시 합니다.

#### <a name="what-does-automated-ml-do-with-the-gains-chart"></a>자동 ML의 이점 차트는 어떻게 되나요?
누적 게인 차트를 사용하면 모델에서 원하는 게인에 해당하는 백분율을 사용하여 분류 경계를 선택하는 데 도움이 됩니다. 이 정보는 동반되는 리프트 차트의 결과를 살펴보는 또 다른 방법을 제공합니다.

#### <a name="what-does-a-good-model-look-like"></a>좋은 모델의 모양은 무엇입니까?
##### <a name="example-1-a-classification-model-with-minimal-gain"></a>예 1: 최소한의 획득으로 분류 모델 사용
![최소한의 이득을 갖춘 분류 모델](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-gains-curve1.png)

##### <a name="example-2-a-classification-model-with-significant-gain"></a>예 2: 상당한 이득을 가진 분류 모델
![상당한 이득을 가진 분류 모델](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-gains-curve2.png)
<a name="calibration-plot"></a>
### <a name="calibration-chart"></a>보정 차트

#### <a name="what-is-a-calibration-chart"></a>보정 차트 란?
보정 플롯은 예측 모델의 신뢰도를 표시하는 데 사용됩니다. 예측 확률과 실제 확률 간의 관계를 표시 하 여이를 수행 합니다. 여기서 "probability"는 특정 인스턴스가 일부 레이블 아래에 속할 가능성을 나타냅니다.
#### <a name="what-does-automated-ml-do-with-the-calibration-chart"></a>자동 ML에서 보정 차트를 사용 하 여 수행 하는 작업은 무엇 인가요?
모든 분류 문제에 대해 마이크로 평균, 매크로 평균 및 지정된 예측 모델의 각 클래스의 보정 선을 검토할 수 있습니다.

매크로-평균은 각 클래스와 별개로 메트릭을 계산 하 고 평균을 취하여 모든 클래스를 동일 하 게 처리 합니다. 그러나 마이크로 평균은 평균 계산을 위해 모든 클래스의 기여를 집계 합니다. 
#### <a name="what-does-a-good-model-look-like"></a>좋은 모델의 모양은 무엇입니까?
잘 보정 된 모델은 y = x 줄에 맞게 정렬 됩니다. 그러면 샘플이 각 클래스에 속하는 확률을 올바르게 예측할 수 있습니다. 신뢰할 수 있는 모델은 0과 1에 가까운 확률을 예측 하 여 각 샘플의 클래스에 대해 확실 하지 않을 수 있습니다.


##### <a name="example-1-a-well-calibrated-model"></a>예 1: 잘 보정 된 모델
![ 잘 보정 되는 모델](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-calib-curve1.png)

##### <a name="example-2-an-over-confident-model"></a>예 2: 보다 확실 한 모델
![보다 확실 한 모델](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-calib-curve2.png)

## <a name="regression-results"></a><a name="regression"></a> 회귀 결과

다음 메트릭과 차트는의 자동화 된 기계 학습 기능을 사용 하 여 작성 하는 모든 회귀 모델에 사용할 수 있습니다 Azure Machine Learning

+ [메트릭](#reg-metrics)
+ [예측 및 True](#pvt)
+ [나머지 히스토그램](#histo)


### <a name="regression-metrics"></a><a name="reg-metrics"></a> 회귀 메트릭

회귀 또는 예측 작업에 대해 각 실행 반복에 다음 메트릭이 저장 됩니다.

|메트릭|Description|계산|추가 매개 변수
--|--|--|--|
explained_variance|Explained variance(설명된 분산)는 수학 모델에서 지정된 데이터 세트의 편차가 고려되는 비율입니다. 오차 분산에 대한 원래 데이터의 분산 감소율입니다. 오차의 평균이 0이면 explained variance와 같습니다.|[변동이](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.explained_variance_score.html)|없음|
r2_score|R2는 평균을 출력하는 기준선 모델과 비교한 제곱 오차의 결정 계수 또는 환원율입니다. |[변동이](https://scikit-learn.org/0.16/modules/generated/sklearn.metrics.r2_score.html)|없음|
spearman_correlation|Spearman correlation(Spearman 상관 관계)은 두 데이터 세트 간 관계의 비모수 단조성 측정입니다. Pearson 상관 관계와 달리 Spearman 상관 관계는 두 데이터 세트가 모두 정규적으로 분포된다고 가정하지 않습니다. 다른 상관 계수처럼, 이 방식은 -1과 +1 사이에서 달라지며 0은 상관 관계가 없음을 의미합니다. - 1 또는 +1의 상관 관계는 정확히 단조성 관계를 의미합니다. 양의 상관 관계는 x가 증가할 때 y도 증가함을 의미합니다. 음의 상관 관계는 x가 증가할 때 y는 감소함을 의미합니다.|[변동이](https://docs.scipy.org/doc/scipy-0.16.1/reference/generated/scipy.stats.spearmanr.html)|없음|
mean_absolute_error|Mean absolute error(평균 절대 오차)는 목표와 예측 간 차이의 예상 절대값입니다.|[변동이](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_absolute_error.html)|없음|
normalized_mean_absolute_error|Normalized mean absolute error(정규화된 평균 절대 오차)는 평균 절대 오차를 데이터 범위로 나눈 것입니다.|[변동이](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_absolute_error.html)|데이터 범위로 나누기|
median_absolute_error|Median absolute error(중앙값 절대 오차)는 목표와 예측 간 모든 절대 차이의 중앙값입니다. 이 손실은 이상값보다 강력합니다.|[변동이](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.median_absolute_error.html)|없음|
normalized_median_absolute_error|Normalized median absolute error(정규화된 중앙값 절대 오차)는 중앙값 절대 오차를 데이터 범위로 나눈 것입니다.|[변동이](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.median_absolute_error.html)|데이터 범위로 나누기|
root_mean_squared_error|Root mean squared error(제곱 평균 오차)는 목표와 예측 간 예상 제곱 차이의 제곱근입니다.|[변동이](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_squared_error.html)|없음|
normalized_root_mean_squared_error|Normalized root mean squared error(정규화된 제곱 평균 오차)는 제곱 평균 오차를 데이터 범위로 나눈 것입니다.|[변동이](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_squared_error.html)|데이터 범위로 나누기|
root_mean_squared_log_error|Root mean squared log error(제곱 평균 로그 오차)는 예상 제곱 로그 오차의 제곱근입니다.|[변동이](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_squared_log_error.html)|없음|
normalized_root_mean_squared_log_error|Noramlized Root mean squared log error(정규화된 제곱 평균 로그 오차)는 제곱 평균 로그 오차를 데이터 범위로 나눈 것입니다.|[변동이](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_squared_log_error.html)|데이터 범위로 나누기|

### <a name="predicted-vs-true-chart"></a><a name="pvt"></a> 예측 및 실제 차트
#### <a name="what-is-a-predicted-vs-true-chart"></a>예측 된 차트 및 진정한 차트
예측 된 값과 True는 회귀 문제에 대 한 예측 값과 해당 하는 실제 값 간의 관계를 보여 줍니다. 예측된 값이 y=x 선에 가까울수록 예측 모델의 정확도가 향상되기 때문에 이 그래프를 사용하여 모델의 성능을 측정할 수 있습니다.

#### <a name="what-does-automated-ml-do-with-the-predicted-vs-true-chart"></a>자동화 된 ML은 예측 된 차트와 실제 차트에서 수행 하는 작업은 무엇 인가요?
각 실행 후에 각 회귀 모델에 대한 예측 대 실제 그래프를 확인할 수 있습니다. 데이터 개인 정보를 보호하기 위해 값은 함께 bin 처리되고 각 bin의 크기가 차트 영역의 아래쪽에 막대형 그래프로 표시됩니다. 밝은 음영 영역을 통해 오차 범위를 표시하는 예측 모델과 모델의 이상적인 값을 비교할 수 있습니다.

#### <a name="what-does-a-good-model-look-like"></a>좋은 모델의 모양은 무엇입니까?
##### <a name="example-1-a-classification-model-with-low-accuracy"></a>예 1: 정확도가 낮은 분류 모델
![예측 정확도가 낮은 회귀 모델](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-regression1.png)

##### <a name="example-2-a-regression-model-with-high-accuracy"></a>예 2: 정확도가 높은 회귀 모델 
[![예측에서 정확도가 높은 회귀 모델](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-regression2.png)](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-regression2-expanded.png)



### <a name="histogram-of-residuals-chart"></a><a name="histo"></a> 잔차 차트의 히스토그램
#### <a name="what-is-a-residuals-chart"></a>잔차 차트 란?
나머지는 예측 값과 실제 값 ()의 차이입니다 `y_pred - y_true` . 바이어스가 낮은 오차 범위를 표시하려면 나머지 히스토그램의 모양이 0을 중심으로 하는 벨 곡선이어야 합니다. 
#### <a name="what-does-automated-ml-do-with-the-residuals-chart"></a>자동 ML은 잔차 차트를 사용 하 여 수행 하는 작업은 무엇 인가요?
자동 ML은 예측의 오류 분포를 보여 주는 잔차 차트를 자동으로 제공 합니다.
#### <a name="what-does-a-good-model-look-like"></a>좋은 모델의 모양은 무엇입니까?
좋은 모델은 일반적으로 0을 중심으로 하는 잔차를 가집니다.

##### <a name="example-1-a-regression-model-with-bias-in-its-errors"></a>예 1: 오류가 발생 한 편차를 포함 하는 회귀 모델
![오류에서 바이어스를 사용 하 여 SA 회귀 모델](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-regression3.png)

##### <a name="example-2-a-regression-model-with-more-even-distribution-of-errors"></a>예제 2: 오류를 보다 균등 하 게 분산 하는 회귀 모델
![오류를 균등 하 게 배포 하는 회귀 모델](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-regression4.png)

## <a name="model-interpretability-and-feature-importance"></a><a name="explain-model"></a> 모델 interpretability 및 기능 중요도
자동화 된 ML은 실행에 대 한 machine learning interpretability 대시보드를 제공 합니다.
Interpretability 기능을 사용 하도록 설정 하는 방법에 대 한 자세한 내용은 자동화 된 ML 실험에서 interpretability를 사용 하도록 설정 [하는 방법](how-to-machine-learning-interpretability-automl.md) 을 참조 하세요.

> [!NOTE]
> ForecastTCN 모델은 현재 설명 클라이언트에서 지원 되지 않습니다. 이 모델은 최상의 모델로 반환 되 고 주문형 설명 실행을 지원 하지 않는 경우 설명 대시보드를 반환 하지 않습니다.

## <a name="next-steps"></a>다음 단계

+ Azure Machine Learning에서 [자동화된 ML](concept-automated-ml.md)을 자세히 알아봅니다.
+ 자동화 된 [Machine Learning 모델 설명](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/explain-model) 샘플 노트북을 사용해 보세요.
