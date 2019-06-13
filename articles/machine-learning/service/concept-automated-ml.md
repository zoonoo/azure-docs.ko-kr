---
title: 자동화된 ML 알고리즘 선택 및 튜닝
titleSuffix: Azure Machine Learning service
description: Azure Machine Learning Service에서 알고리즘을 자동으로 선택하고, 모델에 가장 적합한 알고리즘을 선택하기 위해 사용자가 제공한 매개 변수 및 기준에 따라 모델을 생성하여 시간을 절약하는 방법을 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
author: nacharya1
ms.author: nilesha
ms.date: 05/21/2019
ms.custom: seodec18
ms.openlocfilehash: 93eb0fba91ce5064d04a340e8b3e5b984ee73081
ms.sourcegitcommit: adb6c981eba06f3b258b697251d7f87489a5da33
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/04/2019
ms.locfileid: "66515562"
---
# <a name="what-is-automated-machine-learning"></a>자동화된 Machine Learning이란?

자동화 된 기계 학습, 기계 학습 모델 개발 시간 소모적이 고 반복적인 작업을 자동화 하는 과정은 자동화 된 ML 라고도 합니다. 데이터 과학자, 분석가 및 개발자가 모델 품질을 유지 하면서 높은 확장성, 효율성 및 생산성을 사용 하 여 ML 모델을 빌드할 수 있습니다.

기존 기계 학습 모델을 개발이 리소스를 많이 사용, 중요 한 도메인 정보 및 생성 하 고 수십 개의 모델을 비교 하는 시간을 요구 합니다. Azure Machine Learning을 학습 하 고 지정할 대상 메트릭을 사용 하 여 모델을 조정 하려는 경우 자동화 된 기계 학습을 적용 합니다. 서비스는 다음 기능 선택, 각 반복 학습 점수를 사용 하 여 모델을 생성 하는 위치를 사용 하 여 쌍을 이루는 기계 학습 알고리즘을 반복 합니다. 높을수록 점수, 더욱 모델 데이터를 "맞추기"로 간주 됩니다.

자동화 된 machine learning을 사용 하 여 유용한 쉽고 효율적 프로덕션이 준비 된 ML 모델을 시작 하는 데 걸리는 시간을 가속화할 수 있습니다.

## <a name="when-to-use-automated-ml"></a>자동화 된 기계 학습을 사용 하는 경우

자동화 된 ML 전유물이 기계 학습 모델 개발 프로세스 하 고 데이터 과학 전문 지식을 관계 없이 해당 사용자가 문제에 대 한 종단 간 기계 학습 파이프라인을 식별할 수 있습니다.

데이터 과학자, 분석가 및 산업의 개발자는 자동화 된 ML 따르면 됩니다.

+ 광범위 한 프로그래밍 지식 없이 기계 학습 솔루션 구현
+ 시간과 리소스가 절약
+ 데이터 과학에 대 한 유용한 정보를 활용 합니다.
+ Agile 문제 해결을 제공 합니다.

## <a name="how-automated-ml-works"></a>자동화 된 방법을 ML 작동

사용 하 여 **Azure Machine Learning 서비스**를 디자인 하 고 다음이 단계를 사용 하 여 자동화 된 ML 학습 실험을 실행할 수 있습니다.

1. **기계 학습 문제를 식별할** 해결 해야 할: 분류, 예측 또는 회귀

1. **원본 및 레이블이 지정 된 학습 데이터의 형식을 지정**: Pandas 데이터 프레임 또는 Numpy 배열을

1. **모델 학습을 위한 계산 대상 구성**와 같은 프로그램 [로컬 컴퓨터, Azure Machine Learning에서 계산, 원격 Vm 또는 Azure Databricks](how-to-set-up-training-targets.md)합니다.  자동화 된 교육에 알아봅니다 [원격 리소스에 대해](how-to-auto-train-remote.md)합니다.

1. **자동화 된 machine learning 매개 변수 구성** 다른 모델 하이퍼 매개 변수 설정을 통해 반복 횟수 전처리/기능화 (featurization), 고급 및 최상의 모델을 결정할 때 살펴봐야 할 메트릭이 무엇 인지 결정 하는 합니다.  자동 학습 실험에 대 한 설정을 구성할 수 있습니다 [Azure portal에서](how-to-create-portal-experiments.md) 하거나 [SDK를 사용 하 여](how-to-configure-auto-train.md)입니다.

1. **교육 실행을 제출 합니다.**

  ![자동화 된 Machine learning](./media/how-to-automated-ml/automl-concept-diagram2.png)

학습 중 Azure Machine Learning 서비스는 다양 한 다른 알고리즘 및 매개 변수는 병렬 파이프라인에서 만듭니다. 실험에 정의 된 종료 조건에 도달 했 되 면 중지 됩니다.

또한 실행 중에 수집 된 메트릭을 포함 하는 실행된 기록된 정보를 검사할 수 있습니다. 직렬화 하는 Python 개체를 생성 하는 교육 실행 (`.pkl` 파일) 모델 및 데이터 전처리를 포함 하는 합니다.

모델 빌드를 자동화 하는 동안 수도 있습니다 [어떻게 중요 하거나 관련 기능을 알아봅니다는](how-to-configure-auto-train.md#explain) 를 사용 하면 생성 된 모델입니다.

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE2Xc9t]

<a name="preprocess"></a>

## <a name="preprocessing"></a>전처리

모든 자동화 된 machine learning 실험에서에서 데이터는 기본 메서드를 사용 하 고 필요에 따라 고급 통해 전처리에 전처리 됩니다.

### <a name="automatic-preprocessing-standard"></a>자동 (표준)를 전처리 합니다.

모든 자동화 된 machine learning 실험에서에서 데이터는 자동으로 크기를 조정 하거나 정규화 알고리즘을 잘 수행 합니다.  모델 학습 중 각 모델에 적용할 수는 다음 크기 조정 또는 정규화 방법 중 하나입니다.

|크기 조정&nbsp;&&nbsp;정규화| 설명 |
| ------------- | ------------- |
| [StandardScaleWrapper](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.StandardScaler.html)  | 평균 제거 및 크기 조정 단위 분산 하 여 기능을 표준화  |
| [MinMaxScalar](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.MinMaxScaler.html)  | 각 기능을 해당 열의 최소값 및 최대값을 확장 하 여 기능 변환  |
| [MaxAbsScaler](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.MaxAbsScaler.html#sklearn.preprocessing.MaxAbsScaler) |해당 최대 절대값으로 각 기능 확장 |
| [RobustScalar](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.RobustScaler.html) |해당 변 위치 범위에 따라이 Scaler 기능 |
| [PCA](https://scikit-learn.org/stable/modules/generated/sklearn.decomposition.PCA.html) |데이터의 특이값 분해를 사용 하 여 하위 차원 공간에 프로젝션 하는 선형 차원성 감소 |
| [TruncatedSVDWrapper](https://scikit-learn.org/stable/modules/generated/sklearn.decomposition.TruncatedSVD.html) |이 변환기는 잘린된 단수형 SVD (특이값 분해)를 사용 하 여 선형 차원성 감소를 수행 합니다. PCA를 달리이 스 티 메이이 터 특이값 분해를 계산 하기 전에 데이터를 맞추지 않습니다. 즉, scipy.sparse 행렬을 사용 하 여 효율적으로 작업할 수 있습니다. |
| [SparseNormalizer](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.Normalizer.html) | 적어도 하나의 0이 아닌 구성 요소를 사용 하 여 각 샘플 (즉, 데이터 행렬의 각 행) 다른 샘플 독립적으로 확장 다시는 해당 norm (l1 또는 l2)이 1 |

### <a name="advanced-preprocessing-optional-featurization"></a>전처리 고급: 선택적 기능화 (featurization)

추가 고급 전처리 및 기능화 (featurization) 예: 누락 값 대체, 인코딩 및 변환을 사용할 수 있습니다. [어떤 기능화 (featurization) 포함에 대 한 자세한](how-to-create-portal-experiments.md#preprocess)합니다. 사용 하 여이 설정을 사용 하도록 설정 합니다.

+ Azure Portal: 선택 하는 **전처리** 에서 확인란을 선택 합니다 **고급 설정** [이 단계를 사용 하 여](how-to-create-portal-experiments.md).

+ Python SDK: 지정 `"preprocess": True` 에 대 한 합니다 [ `AutoMLConfig` 클래스](https://docs.microsoft.com/python/api/azureml-train-automl/azureml.train.automl.automlconfig?view=azure-ml-py)합니다.

## <a name="ensemble-models"></a>앙상블 모델

자동화 된 machine learning을 사용 하 여 앙상블 모델을 학습 시킬 수는 [정렬 된 앙상블 초기화를 사용 하 여 Caruana 앙상블 선택 알고리즘](http://www.niculescu-mizil.org/papers/shotgun.icml04.revised.rev2.pdf)합니다. 달리 단일 모델을 사용 하 여 여러 모델을 결합 함으로써 여 machine learning 결과 예측 성능을 개선 하는 앙상블 학습 합니다. 앙상블 반복 실행의 마지막 반복으로 표시 됩니다.

## <a name="training-metric-output"></a>메트릭 출력을 학습

여러 가지 방법으로 각 실행된 반복에 대 한 학습 정확도 메트릭을 볼 수 있습니다.

* Jupyter 위젯을 사용 합니다.
* 사용 된 `get_metrics()` 함수에서 `Run` 개체입니다.
* 실험에서 Azure portal에서 메트릭을 봅니다.

### <a name="classification-metrics"></a>분류 메트릭

다음 메트릭은 분류 작업에 대 한 실행된 각 반복에 저장 됩니다.

|메트릭|설명|계산|추가 매개 변수
--|--|--|--|
AUC_Macro| AUC는 Receiver Operating Characteristic Curve 아래의 영역입니다. Macro(매크로)는 각 클래스에 대한 AUC의 산술 평균입니다.  | [계산](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.roc_auc_score.html) | average="macro"|
AUC_Micro| AUC는 Receiver Operating Characteristic Curve 아래의 영역입니다. Micro(마이크로)는 각 클래스의 참 긍정과 거짓 긍정을 결합하여 전역적으로 컴퓨팅됩니다.| [계산](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.roc_auc_score.html) | average="micro"|
AUC_Weighted  | AUC는 Receiver Operating Characteristic Curve 아래의 영역입니다. Weighted(가중치)는 각 클래스의 true 인스턴스 수를 가중치로 적용하여 계산한 각 클래스 점수의 산술 평균입니다.| [계산](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.roc_auc_score.html)|average="weighted"
accuracy|Accuracy(정확도)는 true 레이블과 정확히 일치하는 예측된 레이블의 백분율입니다. |[계산](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.accuracy_score.html) |없음|
average_precision_score_macro|Average precision(평균 정밀도)은 정밀도-재현율 곡선을 이전 임계값에서의 재현율 증가를 가중치로 사용하여 계산한 각 임계값에서 도달된 정밀도의 가중 평균으로 요약합니다. Macro는 각 클래스 평균 정밀도 점수의 산술 평균입니다.|[계산](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.average_precision_score.html)|average="macro"|
average_precision_score_micro|Average precision(평균 정밀도)은 정밀도-재현율 곡선을 이전 임계값에서의 재현율 증가를 가중치로 사용하여 계산한 각 임계값에서 도달된 정밀도의 가중 평균으로 요약합니다. Micro(마이크로)는 각 차단에서의 참 긍정과 거짓 긍정을 결합하여 전역적으로 계산됩니다.|[계산](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.average_precision_score.html)|average="micro"|
average_precision_score_weighted|Average precision(평균 정밀도)은 정밀도-재현율 곡선을 이전 임계값에서의 재현율 증가를 가중치로 사용하여 계산한 각 임계값에서 도달된 정밀도의 가중 평균으로 요약합니다. Weighted(가중치)는 각 클래스의 true 인스턴스 수를 가중치로 적용하여 계산한 각 클래스에 대한 평균 정밀도 점수의 산술 평균입니다.|[계산](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.average_precision_score.html)|average="weighted"|
balanced_accuracy|Balanced accuracy(균형 정확도)는 각 클래스 재현율의 산술 평균입니다.|[계산](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|average="macro"|
f1_score_macro|F1 score(F1 점수)는 정밀도 및 재현율의 조화 평균입니다. Macro(매크로)는 각 클래스에 대한 F1 점수의 산술 평균입니다.|[계산](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.f1_score.html)|average="macro"|
f1_score_micro|F1 score(F1 점수)는 정밀도 및 재현율의 조화 평균입니다. Micro(마이크로)는 총 참 긍정, 거짓 부정 및 거짓 긍정을 계산하여 전역적으로 계산됩니다.|[계산](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.f1_score.html)|average="micro"|
f1_score_weighted|F1 score(F1 점수)는 정밀도 및 재현율의 조화 평균입니다. Weighted(가중치)는 각 클래스에 대한 F1 점수의 클래스 빈도별 평균입니다.|[계산](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.f1_score.html)|average="weighted"|
log_loss|이 함수는 (다항) 로지스틱 회귀와 그 확장(예: 신경망)에 사용되는 손실 함수로, 지정된 확률적 분류자 예측에 대한 true 레이블의 음수 로그 유사도로 정의됩니다. {0,1}에서 true 레이블 yt와 예상 확률 yp를 갖는 단일 샘플에서 yt = 1인 경우 로그 손실은 -log P(yt&#124;yp) = -(yt log(yp) + (1 - yt) log(1 - yp))입니다.|[계산](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.log_loss.html)|없음|
norm_macro_recall|Normalized Macro Recall(정규화된 매크로 재현율)은 임의 성능 점수가 0이고 완벽한 성능 점수가 1이 되도록 정규화된 매크로 재현율입니다. 이 값은 norm_macro_recall := (recall_score_macro - R)/(1 - R)로 얻습니다. 여기서 R은 임의 예측에 대한 recall_score_macro의 예상 값입니다[예: 이진 분류의 경우 R=0.5, C-클래스 분류 문제의 경우 R=(1/C)].|[계산](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|average = "macro", (recall_score_macro - R)/(1 - R)이 됩니다. 여기서 R은 임의 예측에 대한 recall_score_macro의 예상 값입니다[예: 이진 분류의 경우 R=0.5, C-클래스 분류 문제의 경우 R=(1/C)].|
precision_score_macro|Precision(정밀도)은 실제로 해당 클래스에 있는 특정 클래스로 레이블이 지정된 요소의 백분율입니다. Macro(매크로)는 각 클래스에 대한 정밀도의 산술 평균입니다.|[계산](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.precision_score.html)|average="macro"|
precision_score_micro|Precision(정밀도)은 실제로 해당 클래스에 있는 특정 클래스로 레이블이 지정된 요소의 백분율입니다. Micro(마이크로)는 총 참 긍정 및 거짓 긍정을 계산하여 전역적으로 계산됩니다.|[계산](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.precision_score.html)|average="micro"|
precision_score_weighted|Precision(정밀도)은 실제로 해당 클래스에 있는 특정 클래스로 레이블이 지정된 요소의 백분율입니다. Weighted(가중치)는 각 클래스의 true 인스턴스 수를 가중치로 적용하여 계산한 각 클래스 정밀도의 산술 평균입니다.|[계산](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.precision_score.html)|average="weighted"|
recall_score_macro|Recall(재현율)은 특정 클래스에서 올바르게 레이블이 지정된 요소의 백분율입니다. Macro(매크로)는 각 클래스에 대한 재현율의 산술 평균입니다.|[계산](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|average="macro"|
recall_score_micro|Recall(재현율)은 특정 클래스에서 올바르게 레이블이 지정된 요소의 백분율입니다. Micro(마이크로)는 총 참 긍정 및 거짓 부정을 계산하여 전역적으로 계산됩니다.|[계산](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|average="micro"|
recall_score_weighted|Recall(재현율)은 특정 클래스에서 올바르게 레이블이 지정된 요소의 백분율입니다. Weighted(가중치)는 각 클래스의 true 인스턴스 수를 가중치로 적용하여 계산한 각 클래스 재현율의 산술 평균입니다.|[계산](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|average="weighted"|
weighted_accuracy|Weighted accuracy(가중 정확도)는 각 예제에 제공된 가중치가 해당 예제의 true 클래스에 있는 true 인스턴스의 비율과 같아지는 정확도입니다.|[계산](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.accuracy_score.html)|sample_weight는 대상의 각 요소에 대한 해당 클래스의 비율과 동일한 벡터입니다.|

### <a name="regression-and-forecasting-metrics"></a>회귀 및 예측 메트릭

다음 메트릭은 회귀 또는 예측 작업에 대 한 실행된 각 반복에 저장 됩니다.

|메트릭|설명|계산|추가 매개 변수
--|--|--|--|
explained_variance|Explained variance(설명된 분산)는 수학 모델에서 지정된 데이터 세트의 편차가 고려되는 비율입니다. 오차 분산에 대한 원래 데이터의 분산 감소율입니다. 오차의 평균이 0이면 explained variance와 같습니다.|[계산](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.explained_variance_score.html)|없음|
r2_score|R2는 평균을 출력하는 기준선 모델과 비교한 제곱 오차의 결정 계수 또는 환원율입니다. 오차의 평균이 0이면 explained variance와 같습니다.|[계산](https://scikit-learn.org/0.16/modules/generated/sklearn.metrics.r2_score.html)|없음|
spearman_correlation|Spearman correlation(Spearman 상관 관계)은 두 데이터 세트 간 관계의 비모수 단조성 측정입니다. Pearson 상관 관계와 달리 Spearman 상관 관계는 두 데이터 세트가 모두 정규적으로 분포된다고 가정하지 않습니다. 다른 상관 계수처럼, 이 방식은 -1과 +1 사이에서 달라지며 0은 상관 관계가 없음을 의미합니다. - 1 또는 +1의 상관 관계는 정확히 단조성 관계를 의미합니다. 양의 상관 관계는 x가 증가할 때 y도 증가함을 의미합니다. 음의 상관 관계는 x가 증가할 때 y는 감소함을 의미합니다.|[계산](https://docs.scipy.org/doc/scipy-0.16.1/reference/generated/scipy.stats.spearmanr.html)|없음|
mean_absolute_error|Mean absolute error(평균 절대 오차)는 목표와 예측 간 차이의 예상 절대값입니다.|[계산](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_absolute_error.html)|없음|
normalized_mean_absolute_error|Normalized mean absolute error(정규화된 평균 절대 오차)는 평균 절대 오차를 데이터 범위로 나눈 것입니다.|[계산](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_absolute_error.html)|데이터 범위로 나누기|
median_absolute_error|Median absolute error(중앙값 절대 오차)는 목표와 예측 간 모든 절대 차이의 중앙값입니다. 이 손실은 이상값보다 강력합니다.|[계산](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.median_absolute_error.html)|없음|
normalized_median_absolute_error|Normalized median absolute error(정규화된 중앙값 절대 오차)는 중앙값 절대 오차를 데이터 범위로 나눈 것입니다.|[계산](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.median_absolute_error.html)|데이터 범위로 나누기|
root_mean_squared_error|Root mean squared error(제곱 평균 오차)는 목표와 예측 간 예상 제곱 차이의 제곱근입니다.|[계산](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_squared_error.html)|없음|
normalized_root_mean_squared_error|Normalized root mean squared error(정규화된 제곱 평균 오차)는 제곱 평균 오차를 데이터 범위로 나눈 것입니다.|[계산](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_squared_error.html)|데이터 범위로 나누기|
root_mean_squared_log_error|Root mean squared log error(제곱 평균 로그 오차)는 예상 제곱 로그 오차의 제곱근입니다.|[계산](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_squared_log_error.html)|없음|
normalized_root_mean_squared_log_error|Noramlized Root mean squared log error(정규화된 제곱 평균 로그 오차)는 제곱 평균 로그 오차를 데이터 범위로 나눈 것입니다.|[계산](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_squared_log_error.html)|데이터 범위로 나누기|


## <a name="use-with-onnx-in-c-apps"></a>ONNX 사용 C# 앱

Azure Machine Learning을 사용 하 여 자동화 된 기계 학습 Python 모델을 빌드 및 ONNX 형식으로 변환 하 게 사용할 수 있습니다. ONNX 런타임에서 지원 C#에서 자동으로 작성 된 모델을 사용할 수 있으므로 프로그램 C# 기록이 필요 또는 REST 끝점을 소개 하는 네트워크 대기 시간 없이 앱. 이 흐름의 예를 봅니다 [이 Jupyter notebook에서](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-with-onnx/auto-ml-classification-with-onnx.ipynb)합니다.

## <a name="automated-ml-across-microsoft"></a>Microsoft에서 자동화 된 기계 학습

자동화 된 기계 학습은 같은 다른 Microsoft 솔루션에서 사용할 수 있는:

|통합|설명|
|------------|-----------|
|[ML.NET](https://docs.microsoft.com/dotnet/machine-learning/automl-overview)|자동 모델 선택 및 교육 ML.NET를 사용 하 여 Visual Studio 및 Visual Studio Code를 사용 하 여.NET 앱에서 기계 학습 (미리 보기)를 자동화 합니다.|
|[HDIsnight](../../hdinsight/spark/apache-spark-run-machine-learning-automl.md)|동시에 HDInsight 클러스터에서 Spark의 ML에 자동화 된 교육 작업을 확장 합니다.|
|[PowerBI](https://docs.microsoft.com/power-bi/service-machine-learning-automated)|Power BI (미리 보기)에서 직접 기계 학습 모델을 호출 합니다.|

## <a name="next-steps"></a>다음 단계

예제를 통해 자동화된 Machine Learning을 사용하는 모델을 작성하는 방법을 알아봅니다.

+ 수행 된 [자습서: Azure 자동화된 Machine Learning을 사용하여 자동으로 분류 모델 학습시키기](tutorial-auto-train-models.md)

+ 자동 학습 실험에 대 한 설정을 구성 합니다.
  + Azure 포털 인터페이스에서 [이 단계를 사용 하 여](how-to-create-portal-experiments.md)입니다.
  + Python SDK를 사용 하 여 [이 단계를 사용 하 여](how-to-configure-auto-train.md)입니다.

+ 자동으로 시계열 데이터를 사용 하 여 학습 하는 방법을 알아봅니다 [이 단계를 사용 하 여](how-to-auto-train-forecast.md)입니다.

+ 사용해 [Jupyter 노트북 샘플](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/)
