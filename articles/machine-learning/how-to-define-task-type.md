---
title: 자동화 된 기계 학습 실행을 위한 기계 학습 작업 정의
titleSuffix: Azure Machine Learning
description: 자동화 된 machine learning 실행을 위한 기계 학습 작업을 정의 하는 방법을 알아봅니다.
services: machine-learning
author: RachelKellam
ms.author: rakellam
ms.reviewer: nibaccam
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.date: 11/04/2019
ms.openlocfilehash: fef3f0347d963b8a7d5fe2f1ca771293e9c3d2a9
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "80475508"
---
# <a name="how-to-define-a-machine-learning-task"></a>Machine learning 작업을 정의 하는 방법 
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

이 문서에서는 지원 되는 machine learning 작업 및 자동화 된 기계 학습 (자동화 된 ML) 실험 실행을 위해이 작업을 정의 하는 방법에 대해 알아봅니다.


## <a name="what-is-a-machine-learning-task"></a><a name="What is a machine learning task?"></a>Machine learning 작업 이란?

기계 학습 태스크는 예측 모델을 만들어 해결할 문제의 유형을 나타냅니다. 자동화 된 ML은 분류, 회귀 및 시계열 예측을 포함 하 여 세 가지 유형의 작업을 지원 합니다.

작업 유형| Description| 예제
----|----|----
분류 | 데이터 집합에 있는 특정 행의 범주를 예측 하기 위한 작업입니다. | 신용 카드에 대 한 사기 감지. 대상 열은 *True* 또는 *False*범주가 있는 **사기 행위를 감지** 합니다. 이 경우 데이터의 각 행을 true 또는 false로 분류 합니다.
재발 | 연속 수량 출력을 예측 하기 위한 작업입니다. | 자동차 비용은 해당 기능에 따라 달라 **집니다.**
예측 |향후 추세를 파악 하는 데 필요한 예상 액을 만들기 위한 작업입니다.| 다음 48 시간 동안 에너지 수요를 예측 합니다. 대상 열은 **요청** 이며 예측 값은 에너지 수요의 패턴을 표시 하는 데 사용 됩니다.

자동화된 ML은 자동화 및 튜닝 프로세스 중에 다음 알고리즘을 지원합니다. 사용자는 알고리즘을 지정할 필요가 없습니다.

분류 | 재발 | 시계열 예측
-- |-- |--
[로지스틱 회귀](https://scikit-learn.org/stable/modules/linear_model.html#logistic-regression)| [Elastic Net](https://scikit-learn.org/stable/modules/linear_model.html#elastic-net)| [Elastic Net](https://scikit-learn.org/stable/modules/linear_model.html#elastic-net)
[Light GBM](https://lightgbm.readthedocs.io/en/latest/index.html)|[Light GBM](https://lightgbm.readthedocs.io/en/latest/index.html)|[Light GBM](https://lightgbm.readthedocs.io/en/latest/index.html)
[Gradient Boosting](https://scikit-learn.org/stable/modules/ensemble.html#classification)|[Gradient Boosting](https://scikit-learn.org/stable/modules/ensemble.html#regression)|[Gradient Boosting](https://scikit-learn.org/stable/modules/ensemble.html#regression)
[의사 결정 트리](https://scikit-learn.org/stable/modules/tree.html#decision-trees)|[의사 결정 트리](https://scikit-learn.org/stable/modules/tree.html#regression)|[의사 결정 트리](https://scikit-learn.org/stable/modules/tree.html#regression)
[K Nearest Neighbors](https://scikit-learn.org/stable/modules/neighbors.html#nearest-neighbors-regression)|[K Nearest Neighbors](https://scikit-learn.org/stable/modules/neighbors.html#nearest-neighbors-regression)|[K Nearest Neighbors](https://scikit-learn.org/stable/modules/neighbors.html#nearest-neighbors-regression)
[Linear SVC](https://scikit-learn.org/stable/modules/svm.html#classification)|[LARS Lasso](https://scikit-learn.org/stable/modules/linear_model.html#lars-lasso)|[LARS Lasso](https://scikit-learn.org/stable/modules/linear_model.html#lars-lasso)
[C-SVC(Support Vector Classification)](https://scikit-learn.org/stable/modules/svm.html#classification)|[SGD(Stochastic Gradient Descent)](https://scikit-learn.org/stable/modules/sgd.html#regression)|[SGD(Stochastic Gradient Descent)](https://scikit-learn.org/stable/modules/sgd.html#regression)
[임의 포리스트](https://scikit-learn.org/stable/modules/ensemble.html#random-forests)|[임의 포리스트](https://scikit-learn.org/stable/modules/ensemble.html#random-forests)|[임의 포리스트](https://scikit-learn.org/stable/modules/ensemble.html#random-forests)
[Extremely Randomized Trees](https://scikit-learn.org/stable/modules/ensemble.html#extremely-randomized-trees)|[Extremely Randomized Trees](https://scikit-learn.org/stable/modules/ensemble.html#extremely-randomized-trees)|[Extremely Randomized Trees](https://scikit-learn.org/stable/modules/ensemble.html#extremely-randomized-trees)
[Xgboost](https://xgboost.readthedocs.io/en/latest/parameter.html)|[Xgboost](https://xgboost.readthedocs.io/en/latest/parameter.html)| [Xgboost](https://xgboost.readthedocs.io/en/latest/parameter.html)
[DNN 분류자](https://www.tensorflow.org/api_docs/python/tf/estimator/DNNClassifier)|[DNN 회귀 변수](https://www.tensorflow.org/api_docs/python/tf/estimator/DNNRegressor) | [DNN 회귀 변수](https://www.tensorflow.org/api_docs/python/tf/estimator/DNNRegressor)|
[DNN 선형 분류자](https://www.tensorflow.org/api_docs/python/tf/estimator/LinearClassifier)|[선형 회귀 변수](https://www.tensorflow.org/api_docs/python/tf/estimator/LinearRegressor)|[선형 회귀 변수](https://www.tensorflow.org/api_docs/python/tf/estimator/LinearRegressor)
[Naive Bayes](https://scikit-learn.org/stable/modules/naive_bayes.html#bernoulli-naive-bayes)||
[SGD(Stochastic Gradient Descent)](https://scikit-learn.org/stable/modules/sgd.html#sgd)||


### <a name="set-the-task-type"></a>작업 유형 설정
SDK 또는 Azure Machine Learning studio를 사용 하 여 자동화 된 ML 실험의 작업 유형을 설정할 수 있습니다.

`AutoMLConfig` 생성자의 `task` 매개 변수를 사용 하 여 실험 유형을 지정 합니다.

```python
from azureml.train.automl import AutoMLConfig

# task can be one of classification, regression, forecasting
automl_config = AutoMLConfig(task="classification")
```

Azure Machine Learning studio에서 자동화 된 ML 실험 실행 생성의 일부로 작업을 설정할 수 있습니다. 

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-enterprise-sku-inline.md)]

![작업 유형 선택](./media/how-to-define-task-type/task-type.png)


## <a name="next-steps"></a>다음 단계

+ Azure Machine Learning에서 [자동화 된 ml](concept-automated-ml.md) 에 대해 자세히 알아보세요.
+ Azure Machine Learning에서 [시계열 예측 모델을 자동으로 학습 하는](how-to-auto-train-forecast.md) 방법에 대해 자세히 알아보세요.
+ 자동화 된 [Machine Learning 분류](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-credit-card-fraud/auto-ml-classification-credit-card-fraud.ipynb) 자습서를 사용해 보세요.
+ 자동화 된 [Machine Learning 회귀](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/regression-hardware-performance-explanation-and-featurization/auto-ml-regression-hardware-performance-explanation-and-featurization.ipynb) 샘플 노트북을 사용해 보세요.

