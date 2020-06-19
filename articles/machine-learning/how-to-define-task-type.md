---
title: 자동화된 Machine Learning 실행을 위한 기계 학습 작업 정의
titleSuffix: Azure Machine Learning
description: 자동화된 Machine Learning 실행을 위한 기계 학습 작업을 정의하는 방법을 알아봅니다.
services: machine-learning
author: RachelKellam
ms.author: rakellam
ms.reviewer: nibaccam
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.date: 11/04/2019
ms.openlocfilehash: cb2fbcda7f30db24b876a66df22071f14df74814
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/19/2020
ms.locfileid: "83653257"
---
# <a name="how-to-define-a-machine-learning-task"></a>기계 학습 작업을 정의하는 방법 
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

이 문서에서는 지원되는 기계 학습 작업 및 자동화된 ML(자동화된 Machine Learning) 실험 실행을 위해 이 작업을 정의하는 방법을 알아봅니다.


## <a name="what-is-a-machine-learning-task"></a><a name="What is a machine learning task?"></a> 기계 학습 작업이란?

기계 학습 작업은 예측 모델을 만들어 해결하는 문제 유형을 나타냅니다. 자동화된 ML은 분류, 회귀 및 시계열 예측을 포함한 세 가지 작업 유형을 지원합니다.

작업 유형| Description| 예제
----|----|----
분류 | 데이터 세트에 있는 특정 행의 범주를 예측하는 작업입니다. | 신용 카드에 대한 사기 검색. 대상 열은 *True* 또는 *False* 범주가 포함된 **검색된 사기**가 됩니다. 이 경우 데이터의 각 행을 true 또는 false로 분류합니다.
회귀 | 연속 수량 출력을 예측하는 작업입니다. | 해당 기능을 기반으로 하는 자동차 비용의 경우 대상 열은 **가격**이 됩니다.
예측 |향후 추세의 방향을 파악할 때 정보 기반 추정치를 만드는 작업입니다.| 다음 48시간 동안 에너지 수요를 예측합니다. 대상 열은 **수요**가 되며 예측 값은 에너지 수요의 패턴을 표시하는 데 사용됩니다.

자동화된 ML은 자동화 및 튜닝 프로세스 중에 다음 알고리즘을 지원합니다. 사용자는 알고리즘을 지정할 필요가 없습니다.

분류 | 회귀 | 시계열 예측
-- |-- |--
[Logistic Regression](https://scikit-learn.org/stable/modules/linear_model.html#logistic-regression)| [Elastic Net](https://scikit-learn.org/stable/modules/linear_model.html#elastic-net)| [Elastic Net](https://scikit-learn.org/stable/modules/linear_model.html#elastic-net)
[Light GBM](https://lightgbm.readthedocs.io/en/latest/index.html)|[Light GBM](https://lightgbm.readthedocs.io/en/latest/index.html)|[Light GBM](https://lightgbm.readthedocs.io/en/latest/index.html)
[경사 부스팅](https://scikit-learn.org/stable/modules/ensemble.html#classification)|[경사 부스팅](https://scikit-learn.org/stable/modules/ensemble.html#regression)|[경사 부스팅](https://scikit-learn.org/stable/modules/ensemble.html#regression)
[Decision Tree](https://scikit-learn.org/stable/modules/tree.html#decision-trees)|[Decision Tree](https://scikit-learn.org/stable/modules/tree.html#regression)|[Decision Tree](https://scikit-learn.org/stable/modules/tree.html#regression)
[K Nearest Neighbors](https://scikit-learn.org/stable/modules/neighbors.html#nearest-neighbors-regression)|[K Nearest Neighbors](https://scikit-learn.org/stable/modules/neighbors.html#nearest-neighbors-regression)|[K Nearest Neighbors](https://scikit-learn.org/stable/modules/neighbors.html#nearest-neighbors-regression)
[Linear SVC](https://scikit-learn.org/stable/modules/svm.html#classification)|[LARS Lasso](https://scikit-learn.org/stable/modules/linear_model.html#lars-lasso)|[LARS Lasso](https://scikit-learn.org/stable/modules/linear_model.html#lars-lasso)
[C-SVC(Support Vector Classification)](https://scikit-learn.org/stable/modules/svm.html#classification)|[SGD(Stochastic Gradient Descent)](https://scikit-learn.org/stable/modules/sgd.html#regression)|[SGD(Stochastic Gradient Descent)](https://scikit-learn.org/stable/modules/sgd.html#regression)
[Random Forest](https://scikit-learn.org/stable/modules/ensemble.html#random-forests)|[Random Forest](https://scikit-learn.org/stable/modules/ensemble.html#random-forests)|[Random Forest](https://scikit-learn.org/stable/modules/ensemble.html#random-forests)
[Extremely Randomized Trees](https://scikit-learn.org/stable/modules/ensemble.html#extremely-randomized-trees)|[Extremely Randomized Trees](https://scikit-learn.org/stable/modules/ensemble.html#extremely-randomized-trees)|[Extremely Randomized Trees](https://scikit-learn.org/stable/modules/ensemble.html#extremely-randomized-trees)
[Xgboost](https://xgboost.readthedocs.io/en/latest/parameter.html)|[Xgboost](https://xgboost.readthedocs.io/en/latest/parameter.html)| [Xgboost](https://xgboost.readthedocs.io/en/latest/parameter.html)
[DNN 분류자](https://www.tensorflow.org/api_docs/python/tf/estimator/DNNClassifier)|[DNN 회귀 변수](https://www.tensorflow.org/api_docs/python/tf/estimator/DNNRegressor) | [DNN 회귀 변수](https://www.tensorflow.org/api_docs/python/tf/estimator/DNNRegressor)|
[DNN 선형 분류자](https://www.tensorflow.org/api_docs/python/tf/estimator/LinearClassifier)|[선형 회귀 변수](https://www.tensorflow.org/api_docs/python/tf/estimator/LinearRegressor)|[선형 회귀 변수](https://www.tensorflow.org/api_docs/python/tf/estimator/LinearRegressor)
[Naive Bayes](https://scikit-learn.org/stable/modules/naive_bayes.html#bernoulli-naive-bayes)||
[SGD(Stochastic Gradient Descent)](https://scikit-learn.org/stable/modules/sgd.html#sgd)||


### <a name="set-the-task-type"></a>작업 유형 설정
SDK 또는 Azure Machine Learning Studio를 사용하여 자동화된 ML 실험의 작업 유형을 설정할 수 있습니다.

`AutoMLConfig` 생성자에서 `task` 매개 변수를 사용하여 실험 유형을 지정합니다.

```python
from azureml.train.automl import AutoMLConfig

# task can be one of classification, regression, forecasting
automl_config = AutoMLConfig(task="classification")
```

Azure Machine Learning Studio에서 자동화된 ML 실험 실행을 만드는 동안 작업을 설정할 수 있습니다. 

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-enterprise-sku-inline.md)]

![작업 유형 선택](./media/how-to-define-task-type/task-type.png)


## <a name="next-steps"></a>다음 단계

+ Azure Machine Learning에서 [자동화된 ML](concept-automated-ml.md)을 자세히 알아봅니다.
+ Azure Machine Learning에서 [시계열 예측 모델 자동 학습](how-to-auto-train-forecast.md)을 자세히 알아봅니다.
+ [자동화된 Machine Learning 분류](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-credit-card-fraud/auto-ml-classification-credit-card-fraud.ipynb) 자습서를 사용해 봅니다.
+ [자동화된 Machine Learning 회귀](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/regression-explanation-featurization/auto-ml-regression-explanation-featurization.ipynb) 샘플 Notebook을 사용해 봅니다.

