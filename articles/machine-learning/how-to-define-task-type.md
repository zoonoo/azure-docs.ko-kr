---
title: 자동화된 기계 학습 실행을 위한 기계 학습 작업 정의
titleSuffix: Azure Machine Learning
description: 자동화된 기계 학습 실행을 위한 기계 학습 작업을 정의하는 방법 알아보기
services: machine-learning
author: RachelKellam
ms.author: rakellam
ms.reviewer: nibaccam
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.date: 11/04/2019
ms.openlocfilehash: a58959904559cd3b2cec27762f2df93cfea72abd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75540530"
---
# <a name="how-to-define-a-machine-learning-task"></a>기계 학습 작업을 정의하는 방법 
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

이 문서에서는 지원되는 기계 학습 작업과 자동화된 기계 학습(자동화된 ML) 실험 실행을 위해 이를 정의하는 방법을 알아봅니다.


## <a name="what-is-a-machine-learning-task"></a><a name="What is a machine learning task?"></a>기계 학습 작업이란 무엇입니까?

기계 학습 태스크는 예측 모델을 만들어 해결되는 문제 유형을 나타냅니다. 자동 ML은 분류, 회귀 및 열렬 예측을 포함한 세 가지 유형의 작업을 지원합니다.

작업 유형| Description| 예제
----|----|----
분류 | 데이터 집합에서 특정 행의 범주를 예측하는 작업입니다. | 신용 카드의 사기 탐지. 대상 열은 *True* 또는 *False*범주로 **사기가 검색됩니다.** 이 경우 데이터의 각 행을 참 또는 거짓으로 분류합니다.
회귀 | 연속 수량 출력을 예측하기 위한 작업입니다. | 그 기능에 따라 자동차 비용, 대상 열은 **가격이**될 것입니다.
예측 |미래 동향의 방향을 결정할 때 정보에 입각한 견적을 만드는 작업입니다.| 다음 48시간 동안 에너지 수요를 예측합니다. 대상 열은 **수요가** 되고 예측 값은 에너지 수요의 패턴을 표시하는 데 사용됩니다.

자동화된 ML은 자동화 및 튜닝 프로세스 중에 다음 알고리즘을 지원합니다. 사용자는 알고리즘을 지정할 필요가 없습니다.

분류 | 회귀 | 시계열 예측
-- |-- |--
[물류 회귀](https://scikit-learn.org/stable/modules/linear_model.html#logistic-regression)| [Elastic Net](https://scikit-learn.org/stable/modules/linear_model.html#elastic-net)| [Elastic Net](https://scikit-learn.org/stable/modules/linear_model.html#elastic-net)
[Light GBM](https://lightgbm.readthedocs.io/en/latest/index.html)|[Light GBM](https://lightgbm.readthedocs.io/en/latest/index.html)|[Light GBM](https://lightgbm.readthedocs.io/en/latest/index.html)
[Gradient Boosting](https://scikit-learn.org/stable/modules/ensemble.html#classification)|[Gradient Boosting](https://scikit-learn.org/stable/modules/ensemble.html#regression)|[Gradient Boosting](https://scikit-learn.org/stable/modules/ensemble.html#regression)
[의사 결정 트리](https://scikit-learn.org/stable/modules/tree.html#decision-trees)|[의사 결정 트리](https://scikit-learn.org/stable/modules/tree.html#regression)|[의사 결정 트리](https://scikit-learn.org/stable/modules/tree.html#regression)
[K Nearest Neighbors](https://scikit-learn.org/stable/modules/neighbors.html#nearest-neighbors-regression)|[K Nearest Neighbors](https://scikit-learn.org/stable/modules/neighbors.html#nearest-neighbors-regression)|[K Nearest Neighbors](https://scikit-learn.org/stable/modules/neighbors.html#nearest-neighbors-regression)
[Linear SVC](https://scikit-learn.org/stable/modules/svm.html#classification)|[LARS Lasso](https://scikit-learn.org/stable/modules/linear_model.html#lars-lasso)|[LARS Lasso](https://scikit-learn.org/stable/modules/linear_model.html#lars-lasso)
[C-SVC(Support Vector Classification)](https://scikit-learn.org/stable/modules/svm.html#classification)|[SGD(Stochastic Gradient Descent)](https://scikit-learn.org/stable/modules/sgd.html#regression)|[SGD(Stochastic Gradient Descent)](https://scikit-learn.org/stable/modules/sgd.html#regression)
[Random Forest](https://scikit-learn.org/stable/modules/ensemble.html#random-forests)|[Random Forest](https://scikit-learn.org/stable/modules/ensemble.html#random-forests)|[Random Forest](https://scikit-learn.org/stable/modules/ensemble.html#random-forests)
[Extremely Randomized Trees](https://scikit-learn.org/stable/modules/ensemble.html#extremely-randomized-trees)|[Extremely Randomized Trees](https://scikit-learn.org/stable/modules/ensemble.html#extremely-randomized-trees)|[Extremely Randomized Trees](https://scikit-learn.org/stable/modules/ensemble.html#extremely-randomized-trees)
[엑스부스트](https://xgboost.readthedocs.io/en/latest/parameter.html)|[엑스부스트](https://xgboost.readthedocs.io/en/latest/parameter.html)| [엑스부스트](https://xgboost.readthedocs.io/en/latest/parameter.html)
[DNN 분류기](https://www.tensorflow.org/api_docs/python/tf/estimator/DNNClassifier)|[DNN 회귀](https://www.tensorflow.org/api_docs/python/tf/estimator/DNNRegressor) | [DNN 회귀](https://www.tensorflow.org/api_docs/python/tf/estimator/DNNRegressor)|
[DNN 선형 분류기](https://www.tensorflow.org/api_docs/python/tf/estimator/LinearClassifier)|[선형 회귀](https://www.tensorflow.org/api_docs/python/tf/estimator/LinearRegressor)|[선형 회귀](https://www.tensorflow.org/api_docs/python/tf/estimator/LinearRegressor)
[Naive Bayes](https://scikit-learn.org/stable/modules/naive_bayes.html#bernoulli-naive-bayes)||
[SGD(Stochastic Gradient Descent)](https://scikit-learn.org/stable/modules/sgd.html#sgd)||


### <a name="set-the-task-type"></a>작업 유형 설정
SDK 또는 Azure 기계 학습 스튜디오를 통해 자동화된 ML 실험에 대한 작업 유형을 설정할 수 있습니다.

생성자의 `task` 매개 `AutoMLConfig` 변수를 사용하여 실험 유형을 지정합니다.

```python
from azureml.train.automl import AutoMLConfig

# task can be one of classification, regression, forecasting
automl_config = AutoMLConfig(task="classification")
```

Azure 기계 학습 스튜디오에서 자동화된 ML 실험 실행 생성의 일부로 작업을 설정할 수 있습니다. 

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-enterprise-sku-inline.md)]

![작업 유형 선택](./media/how-to-define-task-type/task-type.png)


## <a name="next-steps"></a>다음 단계

+ Azure 기계 학습에서 [자동화된 ml에](concept-automated-ml.md) 대해 자세히 알아봅니다.
+ Azure 기계 [학습에서 시계열 예측 모델 자동 학습에](how-to-auto-train-forecast.md) 대해 자세히 알아보기
+ [자동화된 기계 학습 분류](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning/model-explanation) 자습서를 사용해 보십시오.
+ [자동화된 기계 학습 회귀](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning/model-explanation) 샘플 전자 필기장을 사용해 보십시오.

