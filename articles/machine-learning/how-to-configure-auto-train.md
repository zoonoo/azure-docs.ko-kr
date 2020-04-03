---
title: 자동화된 ML 실험 만들기
titleSuffix: Azure Machine Learning
description: 자동화된 기계 학습은 사용자를 위한 알고리즘을 선택하고 배포할 준비가 된 모델을 생성합니다. 자동화된 기계 학습 실험을 구성하는 데 사용할 수 있는 옵션에 대해 알아봅니다.
author: cartacioS
ms.author: sacartac
ms.reviewer: sgilley
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 03/09/2020
ms.custom: seodec18
ms.openlocfilehash: 03e1d4aa74d2f71ab2f32ac55f4ad3d46f672f5c
ms.sourcegitcommit: bc738d2986f9d9601921baf9dded778853489b16
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/02/2020
ms.locfileid: "80618548"
---
# <a name="configure-automated-ml-experiments-in-python"></a>Python에서 자동화된 ML 실험 구성
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

이 가이드에서는 [Azure 기계 학습 SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)를 사용하여 자동화된 기계 학습 실험의 다양한 구성 설정을 정의하는 방법을 알아봅니다. 자동화된 Machine Learning은 사용자를 위한 알고리즘과 하이퍼 매개 변수를 선택하고 배포할 준비가 된 모델을 생성합니다. 자동화된 Machine Learning 실험을 구성하는 데 사용할 수 있는 옵션에 대해 알아봅니다.

자동화된 기계 학습 실험의 예를 보려면 [자습서: 자동화된 기계 학습으로 분류 모델 학습](tutorial-auto-train-models.md) 또는 [클라우드에서 자동화된 기계 학습을 사용하여 모델을 교육합니다.](how-to-auto-train-remote.md)

자동화된 기계 학습에서 사용할 수 있는 구성 옵션은 다음과 같습니다.

* 실험 유형 선택: 분류, 회귀 또는 열렬 예측
* 데이터 원본, 형식 및 데이터 가져오기
* 컴퓨팅 대상 선택: 로컬 또는 원격
* 자동화된 Machine Learning 실험 설정
* 자동화된 Machine Learning 실험 실행
* 모델 메트릭 탐색
* 모델 등록 및 배포

코드 환경이 없는 경우 Azure [Machine Learning 스튜디오에서 자동화된 기계 학습 실험을 만들](how-to-use-automated-ml-for-ml-models.md)수도 있습니다.

## <a name="select-your-experiment-type"></a>실험 유형 선택

실험을 시작하기 전에 해결하려는 기계 학습 문제의 종류를 결정해야 합니다. 자동화된 기계 학습은 분류, 회귀 및 예측의 작업 유형을 지원합니다. [작업 유형에](how-to-define-task-type.md)대해 자세히 알아봅니다.

자동화된 Machine Learning은 자동화 및 튜닝 프로세스 중에 다음 알고리즘을 지원합니다. 사용자는 알고리즘을 지정할 필요가 없습니다.

분류 | 회귀 | 시계열 예측
|-- |-- |--
[물류 회귀](https://scikit-learn.org/stable/modules/linear_model.html#logistic-regression)| [Elastic Net](https://scikit-learn.org/stable/modules/linear_model.html#elastic-net)| [Elastic Net](https://scikit-learn.org/stable/modules/linear_model.html#elastic-net)
[Light GBM](https://lightgbm.readthedocs.io/en/latest/index.html)|[Light GBM](https://lightgbm.readthedocs.io/en/latest/index.html)|[Light GBM](https://lightgbm.readthedocs.io/en/latest/index.html)
[Gradient Boosting](https://scikit-learn.org/stable/modules/ensemble.html#classification)|[Gradient Boosting](https://scikit-learn.org/stable/modules/ensemble.html#regression)|[Gradient Boosting](https://scikit-learn.org/stable/modules/ensemble.html#regression)
[의사 결정 트리](https://scikit-learn.org/stable/modules/tree.html#decision-trees)|[의사 결정 트리](https://scikit-learn.org/stable/modules/tree.html#regression)|[의사 결정 트리](https://scikit-learn.org/stable/modules/tree.html#regression)
[K Nearest Neighbors](https://scikit-learn.org/stable/modules/neighbors.html#nearest-neighbors-regression)|[K Nearest Neighbors](https://scikit-learn.org/stable/modules/neighbors.html#nearest-neighbors-regression)|[K Nearest Neighbors](https://scikit-learn.org/stable/modules/neighbors.html#nearest-neighbors-regression)
[Linear SVC](https://scikit-learn.org/stable/modules/svm.html#classification)|[LARS Lasso](https://scikit-learn.org/stable/modules/linear_model.html#lars-lasso)|[LARS Lasso](https://scikit-learn.org/stable/modules/linear_model.html#lars-lasso)
[지원 벡터 분류(SVC)](https://scikit-learn.org/stable/modules/svm.html#classification)|[SGD(Stochastic Gradient Descent)](https://scikit-learn.org/stable/modules/sgd.html#regression)|[SGD(Stochastic Gradient Descent)](https://scikit-learn.org/stable/modules/sgd.html#regression)
[Random Forest](https://scikit-learn.org/stable/modules/ensemble.html#random-forests)|[Random Forest](https://scikit-learn.org/stable/modules/ensemble.html#random-forests)|[Random Forest](https://scikit-learn.org/stable/modules/ensemble.html#random-forests)
[Extremely Randomized Trees](https://scikit-learn.org/stable/modules/ensemble.html#extremely-randomized-trees)|[Extremely Randomized Trees](https://scikit-learn.org/stable/modules/ensemble.html#extremely-randomized-trees)|[Extremely Randomized Trees](https://scikit-learn.org/stable/modules/ensemble.html#extremely-randomized-trees)
[엑스부스트](https://xgboost.readthedocs.io/en/latest/parameter.html)|[엑스부스트](https://xgboost.readthedocs.io/en/latest/parameter.html)| [엑스부스트](https://xgboost.readthedocs.io/en/latest/parameter.html)
[DNN 분류기](https://www.tensorflow.org/api_docs/python/tf/estimator/DNNClassifier)|[DNN 회귀](https://www.tensorflow.org/api_docs/python/tf/estimator/DNNRegressor) | [DNN 회귀](https://www.tensorflow.org/api_docs/python/tf/estimator/DNNRegressor)|
[DNN 선형 분류기](https://www.tensorflow.org/api_docs/python/tf/estimator/LinearClassifier)|[선형 회귀](https://www.tensorflow.org/api_docs/python/tf/estimator/LinearRegressor)|[선형 회귀](https://www.tensorflow.org/api_docs/python/tf/estimator/LinearRegressor)
[Naive Bayes](https://scikit-learn.org/stable/modules/naive_bayes.html#bernoulli-naive-bayes)|[빠른 선형 회귀](https://docs.microsoft.com/python/api/nimbusml/nimbusml.linear_model.fastlinearregressor?view=nimbusml-py-latest)|[자동 아리마](https://www.alkaline-ml.com/pmdarima/modules/generated/pmdarima.arima.auto_arima.html#pmdarima.arima.auto_arima)
[SGD(Stochastic Gradient Descent)](https://scikit-learn.org/stable/modules/sgd.html#sgd)|[온라인 그라데이션 하강 회귀](https://docs.microsoft.com/python/api/nimbusml/nimbusml.linear_model.onlinegradientdescentregressor?view=nimbusml-py-latest)|[예언자](https://facebook.github.io/prophet/docs/quick_start.html)
|[평균 지각 분류기](https://docs.microsoft.com/python/api/nimbusml/nimbusml.linear_model.averagedperceptronbinaryclassifier?view=nimbusml-py-latest)||예보TCN
|[선형 SVM 분류기](https://docs.microsoft.com/python/api/nimbusml/nimbusml.linear_model.linearsvmbinaryclassifier?view=nimbusml-py-latest)||

생성자의 `task` 매개 `AutoMLConfig` 변수를 사용하여 실험 유형을 지정합니다.

```python
from azureml.train.automl import AutoMLConfig

# task can be one of classification, regression, forecasting
automl_config = AutoMLConfig(task = "classification")
```

## <a name="data-source-and-format"></a>데이터 원본 및 형식

자동화된 Machine Learning은 로컬 데스크톱 또는 Azure Blob Storage와 같은 클라우드의 데이터를 지원합니다. 데이터는 팬더 데이터 프레임 또는 **Azure 기계 학습 탭데이터 집합으로**읽을 수 **있습니다.**  [데이터 집합에 대해 자세히 알아봅니다.](how-to-create-register-datasets.md)

교육 데이터에 대한 요구 사항:
- 데이터는 테이블 형식이어야 합니다.
- 대상 열을 예측하는 값은 데이터에 있어야 합니다.

다음 코드 예제에서는 이러한 형식으로 데이터를 저장하는 방법을 보여 줍니다.

* 테이블 형식데이터 집합

  ```python
  from azureml.core.dataset import Dataset
  from azureml.opendatasets import Diabetes
  
  tabular_dataset = Diabetes.get_tabular_dataset()
  train_dataset, test_dataset = tabular_dataset.random_split(percentage=0.1, seed=42)
  label = "Y"
  ```

* pandas 데이터 프레임

  ```python
  import pandas as pd
  from sklearn.model_selection import train_test_split

  df = pd.read_csv("your-local-file.csv")
  train_data, test_data = train_test_split(df, test_size=0.1, random_state=42)
  label = "label-col-name"
  ```

## <a name="fetch-data-for-running-experiment-on-remote-compute"></a>원격 컴퓨팅에서 실험을 실행하기 위한 데이터 가져오기

원격 실행의 경우 원격 계산에서 학습 데이터에 액세스할 수 있어야 합니다. SDK의 클래스는 [`Datasets`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py) 다음과 같은 기능을 노출합니다.

* 정적 파일 또는 URL 소스의 데이터를 작업 공간으로 쉽게 전송
* 클라우드 컴퓨팅 리소스에서 실행할 때 교육 스크립트에 데이터를 사용할 수 있도록 합니다.

클래스를 사용하여 계산 대상에 데이터를 탑재하는 방법에 대한 방법을 참조하세요. [how-to](how-to-train-with-datasets.md#option-2--mount-files-to-a-remote-compute-target) `Dataset`

## <a name="train-and-validation-data"></a>데이터 학습 및 유효성 검사

생성자에서 직접 별도의 train 및 `AutoMLConfig` 유효성 검사 집합을 지정할 수 있습니다.

### <a name="k-folds-cross-validation"></a>K 접기 교차 유효성 검사

`n_cross_validations` 설정을 사용하여 교차 유효성 검사의 수를 지정합니다. 학습 데이터 집합은 무작위로 동일한 크기의 `n_cross_validations` 접기로 분할됩니다. 각 교차 유효성 검사 라운드 중에 접기 중 하나는 나머지 접기에 대해 학습된 모델의 유효성 검사에 사용됩니다. 이 프로세스는 각 접기가 유효성 검사 집합으로 한 번 사용될 때까지 `n_cross_validations` 라운드 동안 반복됩니다. 모든 `n_cross_validations` 라운드에 걸친 평균 점수가 보고되고 해당 모델이 전체 학습 데이터 세트에 대해 다시 학습됩니다.

### <a name="monte-carlo-cross-validation-repeated-random-sub-sampling"></a>몬테 카를로 교차 유효성 검사(반복된 무작위 하위 샘플링)

`validation_size`를 사용하여 유효성 검사에 사용해야 하는 학습 데이터 세트의 비율을 지정하고, `n_cross_validations`를 사용하여 교차 유효성 검사의 수를 지정합니다. 각 교차 유효성 검사 라운드 중에 나머지 데이터에 대해 학습된 모델의 유효성 검사를 위해 `validation_size` 크기의 하위 집합이 무작위로 선택됩니다. 마지막으로, 모든 `n_cross_validations` 라운드에 걸친 평균 점수가 보고되고 해당 모델이 전체 학습 데이터 집합에 대해 다시 학습됩니다. 몬테 카를로는 열렬 예측에 대 한 지원 되지 않습니다.

### <a name="custom-validation-dataset"></a>사용자 지정 유효성 검사 데이터 세트

임의 분할이 허용되지 않는 경우 사용자 지정 유효성 검사 데이터 집합(일반적으로 시간계 데이터 또는 불균형 데이터)을 사용합니다. 사용자 고유의 유효성 검사 데이터 세트를 지정할 수 있습니다. 모델은 무작위 데이터 세트 대신 지정된 유효성 검사 데이터 세트에 대해 평가됩니다.

## <a name="compute-to-run-experiment"></a>실험 실행 컴퓨팅

다음으로, 모델을 학습할 위치를 결정합니다. 자동화된 Machine Learning 실험은 다음 컴퓨팅 옵션에서 실행할 수 있습니다.
* 로컬 데스크톱 또는 랩톱과 같은 로컬 머신 - 일반적으로 데이터 세트가 작고 아직 탐색 단계에 있는 경우입니다.
* 클라우드의 원격 머신 - [Azure Machine Learning Managed Compute](concept-compute-target.md#amlcompute)는 Azure Virtual Machines 클러스터에서 Machine Learning 모델을 학습할 수 있도록 하는 관리형 서비스입니다.

  로컬 및 원격 컴퓨팅 대상이 있는 노트북의 예는 이 [GitHub 사이트를](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning) 참조하십시오.

* Azure 구독의 Azure Databricks 클러스터입니다. 자세한 내용은 여기에서 확인할 수 있습니다 - [자동화된 ML을 위한 Azure Databricks 클러스터 설정](how-to-configure-environment.md#azure-databricks)

  Azure Databricks가 있는 노트북의 예는 이 [GitHub 사이트를](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/azure-databricks/automl) 참조하십시오.

<a name='configure-experiment'></a>

## <a name="configure-your-experiment-settings"></a>실험 설정 구성

자동화된 Machine Learning 실험을 구성하는 데 사용할 수 있는 옵션에 대해 알아봅니다. 이러한 매개 변수는 `AutoMLConfig` 개체를 인스턴스화하여 설정됩니다. 매개 변수의 전체 목록은 [AutoMLConfig 클래스](/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig)를 참조하세요.

일부 사례:

1. 실험 시간 초과 분을 30분으로 설정하고 2개의 교차 유효성 검사 접기로 설정된 AUC를 기본 메트릭으로 가중치를 사용하는 분류 실험.

   ```python
       automl_classifier=AutoMLConfig(
       task='classification',
       primary_metric='AUC_weighted',
       experiment_timeout_minutes=30,
       blacklist_models=['XGBoostClassifier'],
       training_data=train_data,
       label_column_name=label,
       n_cross_validations=2)
   ```
2. 다음은 5개의 유효성 검사 교차 접기로 60분 후에 종료하도록 설정된 회귀 실험의 예입니다.

   ```python
      automl_regressor = AutoMLConfig(
      task='regression',
      experiment_timeout_minutes=60,
      whitelist_models=['KNN'],
      primary_metric='r2_score',
      training_data=train_data,
      label_column_name=label,
      n_cross_validations=5)
   ```

세 가지 `task` 다른 매개 변수 값(세 번째 작업 `forecasting`유형은 `regression` 및 유사한 알고리즘 풀을 작업으로 사용함)이 적용할 모델 목록을 결정합니다. `whitelist` 또는 `blacklist` 매개변수를 사용하여 포함하거나 제외할 사용 가능한 모델의 반복을 추가로 수정합니다. 지원되는 모델 목록은 [지원되는 모델](https://docs.microsoft.com/python/api/azureml-train-automl-client/azureml.train.automl.constants.supportedmodels) 클래스(분류, [예측](https://docs.microsoft.com/python/api/azureml-train-automl-client/azureml.train.automl.constants.supportedmodels.forecasting)및 [회귀)에서](https://docs.microsoft.com/python/api/azureml-train-automl-client/azureml.train.automl.constants.supportedmodels.regression)찾을 수 있습니다.[Classification](https://docs.microsoft.com/python/api/azureml-train-automl-client/azureml.train.automl.constants.supportedmodels.classification)

실험 시간 초과 실패를 방지하기 위해 자동화된 ML의 `experiment_timeout_minutes` 유효성 검사 서비스는 열 크기별 행이 1,000만 분을 초과하는 경우 최소 15분 또는 60분으로 설정해야 합니다.

### <a name="primary-metric"></a>기본 메트릭
기본 메트릭은 최적화를 위해 모델 학습 중에 사용할 메트릭을 결정합니다. 선택할 수 있는 사용 가능한 메트릭은 선택한 작업 유형에 따라 결정되며 다음 표에는 각 작업 유형에 대한 유효한 기본 메트릭이 표시됩니다.

|분류 | 회귀 | 시계열 예측
|-- |-- |--
|accuracy| spearman_correlation | spearman_correlation
|AUC_weighted | normalized_root_mean_squared_error | normalized_root_mean_squared_error
|average_precision_score_weighted | r2_score | r2_score
|norm_macro_recall | normalized_mean_absolute_error | normalized_mean_absolute_error
|precision_score_weighted |

[자동화된 기계 학습 결과 이해에서](how-to-understand-automated-ml.md)이러한 메트릭의 특정 정의에 대해 알아봅니다.

### <a name="data-featurization"></a>데이터 위화

자동화된 모든 기계 학습 실험에서 데이터의 [크기조정 및 정규화는](concept-automated-ml.md#preprocess) 서로 다른 규모의 기능에 민감한 *특정* 알고리즘을 돕기 위해 자동으로 조정됩니다.  그러나 누락된 값 대치, 인코딩 및 변환과 같은 추가 기능화를 활성화할 수도 있습니다. [어떤 위업이 포함되어 있는지 자세히 알아보십시오.](how-to-use-automated-ml-for-ml-models.md#featurization)

실험을 구성할 때 고급 설정을 `featurization`사용하도록 설정할 수 있습니다. 다음 표에서는 클래스에서 위화에 대해 허용되는 설정을 보여 [ `AutoMLConfig` 주시면](https://docs.microsoft.com/python/api/azureml-train-automl/azureml.train.automl.automlconfig?view=azure-ml-py)됩니다.

|위화화 구성 | 설명 |
| ------------- | ------------- |
|`"featurization":`&nbsp;`'FeaturizationConfig'`| 사용자 정의 위화 단계를 사용해야 함을 나타냅니다. [위업을 사용자 지정하는 방법에 대해 알아봅니다.](how-to-configure-auto-train.md#customize-feature-engineering)|
|`"featurization": 'off'`| featurization 단계가 자동으로 수행되어서는 안 함을 나타냅니다.|
|`"featurization": 'auto'`| 전처리의 일부로 데이터 [가드레일 및 위화 단계가](how-to-use-automated-ml-for-ml-models.md#advanced-featurization-options) 자동으로 수행된다는 것을 나타냅니다.|

> [!NOTE]
> 자동화된 기계 학습 위화 단계(기능 정규화, 누락된 데이터 처리, 텍스트를 숫자로 변환 등)는 기본 모델의 일부가 됩니다. 예측을 위해 모델을 사용하는 경우 학습 중에 적용된 동일한 위화 단계가 입력 데이터에 자동으로 적용됩니다.

### <a name="time-series-forecasting"></a>시계열 예측
열렬 `forecasting` 작업에는 구성 개체에 추가 매개 변수가 필요합니다.

1. `time_column_name`: 유효한 시간계를 포함하는 학습 데이터의 열 이름을 정의하는 필수 매개 변수입니다.
1. `max_horizon`: 학습 데이터의 주기도에 따라 예측할 시간을 정의합니다. 예를 들어 일일 시간 그레인이 있는 학습 데이터가 있는 경우 모델이 학습할 기간을 정의하는 것입니다.
1. `grain_column_names`: 학습 데이터에 개별 열열 데이터가 포함된 열의 이름을 정의합니다. 예를 들어 매장별로 특정 브랜드의 매출을 예측하는 경우 매장 및 브랜드 열을 그레인 열로 정의합니다. 각 그레인/그룹화에 대해 별도의 타임시리즈및 예측이 생성됩니다. 

아래 에서 사용되는 설정의 예는 [샘플 전자 필기장을](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-orange-juice-sales/auto-ml-forecasting-orange-juice-sales.ipynb)참조하십시오.

```python
# Setting Store and Brand as grains for training.
grain_column_names = ['Store', 'Brand']
nseries = data.groupby(grain_column_names).ngroups

# View the number of time series data with defined grains
print('Data contains {0} individual time-series.'.format(nseries))
```

```python
time_series_settings = {
    'time_column_name': time_column_name,
    'grain_column_names': grain_column_names,
    'drop_column_names': ['logQuantity'],
    'max_horizon': n_test_periods
}

automl_config = AutoMLConfig(task = 'forecasting',
                             debug_log='automl_oj_sales_errors.log',
                             primary_metric='normalized_root_mean_squared_error',
                             experiment_timeout_minutes=20,
                             training_data=train_data,
                             label_column_name=label,
                             n_cross_validations=5,
                             path=project_folder,
                             verbosity=logging.INFO,
                             **time_series_settings)
```

### <a name="ensemble-configuration"></a><a name="ensemble"></a>앙상블 구성

앙상블 모델은 기본적으로 활성화되어 있으며 자동화된 기계 학습 실행에서 최종 실행 반복으로 나타납니다. 현재 지원되는 앙상블 방법은 투표 및 스태킹입니다. 투표는 가중 평균을 사용하여 소프트 투표로 구현되고, 스태킹 구현은 첫 번째 레이어가 투표 앙상블과 동일한 모델을 가지며, 두 번째 레이어 모델은 첫 번째 레이어에서 모델의 최적의 조합을 찾는 데 사용되는 두 계층 구현을 사용한다. ONNX 모델을 사용 **중이거나** 모델 설명기능을 사용하도록 설정한 경우 스태킹이 비활성화되고 투표만 사용됩니다.

기본 스택 앙상블 동작을 변경 `kwargs` 하기 `AutoMLConfig` 위해 개체에서와 같이 제공할 수 있는 여러 기본 인수가 있습니다.

* `stack_meta_learner_type`: 메타 학습자는 개별 이기종 모델의 출력에 대해 학습된 모델입니다. 기본 메타 `LogisticRegression` 학습자는 분류 작업(또는 `LogisticRegressionCV` 교차 유효성 검사가 활성화된 경우)과 `ElasticNet` 회귀/예측 `ElasticNetCV` 작업(또는 교차 유효성 검사가 활성화된 경우)에 대한 것입니다. 이 매개 변수는 다음 문자열 `LogisticRegression`중 `LogisticRegressionCV` `LightGBMClassifier`하나일 `LightGBMRegressor`수 `LinearRegression`있습니다. `ElasticNet` `ElasticNetCV`
* `stack_meta_learner_train_percentage`: 메타 학습자 교육을 위해 예약할 교육 집합(학습 및 유효성 검사 유형 선택 시)의 비율을 지정합니다. 기본값은 `0.2`입니다.
* `stack_meta_learner_kwargs`: 메타 학습자의 초기화에 전달하는 선택적 매개 변수입니다. 이러한 매개변수 및 매개변수 형식은 해당 모델 생성자의 매개변수 및 매개변수 유형을 미러리하고 모델 생성자로 전달됩니다.

다음 코드는 개체에서 사용자 지정 앙상블 `AutoMLConfig` 동작을 지정하는 예제를 보여 주었습니다.

```python
ensemble_settings = {
    "stack_meta_learner_type": "LogisticRegressionCV",
    "stack_meta_learner_train_percentage": 0.3,
    "stack_meta_learner_kwargs": {
        "refit": True,
        "fit_intercept": False,
        "class_weight": "balanced",
        "multi_class": "auto",
        "n_jobs": -1
    }
}

automl_classifier = AutoMLConfig(
        task='classification',
        primary_metric='AUC_weighted',
        experiment_timeout_minutes=30,
        training_data=train_data,
        label_column_name=label,
        n_cross_validations=5,
        **ensemble_settings
        )
```

앙상블 교육은 기본적으로 활성화되어 있지만 `enable_voting_ensemble` 및 `enable_stack_ensemble` 부울 매개 변수를 사용하여 비활성화할 수 있습니다.

```python
automl_classifier = AutoMLConfig(
        task='classification',
        primary_metric='AUC_weighted',
        experiment_timeout_minutes=30,
        training_data=data_train,
        label_column_name=label,
        n_cross_validations=5,
        enable_voting_ensemble=False,
        enable_stack_ensemble=False
        )
```

## <a name="run-experiment"></a>실험 실행

자동화된 ML의 경우 `Experiment` 실험을 실행하는 데 사용되는 `Workspace` 명명된 개체인 개체를 만듭니다.

```python
from azureml.core.experiment import Experiment

ws = Workspace.from_config()

# Choose a name for the experiment and specify the project folder.
experiment_name = 'automl-classification'
project_folder = './sample_projects/automl-classification'

experiment = Experiment(ws, experiment_name)
```

실행하려는 실험을 제출하고 모델을 생성합니다. `AutoMLConfig`를 `submit` 메서드에 전달하여 모델을 생성합니다.

```python
run = experiment.submit(automl_config, show_output=True)
```

>[!NOTE]
>새 머신에 먼저 종속성이 설치됩니다.  출력이 표시되는 데 최대 10분이 걸릴 수 있습니다.
>`show_output`을 `True`로 설정하면 출력이 콘솔에 표시됩니다.

### <a name="exit-criteria"></a>종료 기준
실험을 종료하도록 정의할 수 있는 몇 가지 옵션이 있습니다.
1. 조건 없음: 종료 매개 변수를 정의하지 않으면 기본 메트릭에서 더 이상 진행되지 않을 때까지 실험이 계속됩니다.
1. 시간이 지난 후 종료: `experiment_timeout_minutes` 설정을 사용하면 실험을 계속 진행해야 하는 시간을 몇 분 만에 정의할 수 있습니다.
1. 점수에 도달한 후 종료: 기본 메트릭 점수에 도달한 후 실험을 사용하여 `experiment_exit_score` 실험을 완료합니다.

### <a name="explore-model-metrics"></a>모델 메트릭 탐색

전자 필기장에 있는 경우 위젯 또는 인라인으로 교육 결과를 볼 수 있습니다. 자세한 내용은 [모델 추적 및 평가](how-to-track-experiments.md#view-run-details)를 참조하세요.

## <a name="understand-automated-ml-models"></a>자동화된 ML 모델 이해

자동화된 ML을 사용하여 생성된 모든 모델에는 다음 단계가 포함됩니다.
+ 자동화된 기능 `"featurization": 'auto'`엔지니어링(경우)
+ 하이퍼매개 변수 값을 가진 크기 조정/정규화 및 알고리즘

자동화된 ML의 fitted_model 출력에서 이 정보를 얻으려면 투명하게 만듭니다.

```python
automl_config = AutoMLConfig(…)
automl_run = experiment.submit(automl_config …)
best_run, fitted_model = automl_run.get_output()
```

### <a name="automated-feature-engineering"></a>자동화된 기능 엔지니어링

에서 발생하는 전처리 및 [자동화된](concept-automated-ml.md#preprocess) 기능 `"featurization": 'auto'`엔지니어링 목록을 참조하십시오.

다음 예를 살펴보세요.
+ 입력 기능은 A(숫자), B(숫자), C(숫자), D(DateTime)의 네 가지 입력 기능이 있습니다.
+ 숫자 피쳐 C는 모든 고유 값을 가진 ID 열이므로 삭제됩니다.
+ 숫자 피처 A와 B에는 값이 누락되어 평균에 의해 대부됩니다.
+ DateTime 기능 D는 11 가지 엔지니어링 기능으로 위화됩니다.

피팅 모델의 첫 번째 단계에서 이러한 2API를 사용하여 자세한 내용은 이해하십시오.  [이 샘플 전자 필기장을](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning/forecasting-energy-demand)참조하십시오.

+ API 1: `get_engineered_feature_names()` 엔지니어링된 피쳐 이름 목록을 반환합니다.

  Usage:
  ```python
  fitted_model.named_steps['timeseriestransformer']. get_engineered_feature_names ()
  ```

  ```
  Output: ['A', 'B', 'A_WASNULL', 'B_WASNULL', 'year', 'half', 'quarter', 'month', 'day', 'hour', 'am_pm', 'hour12', 'wday', 'qday', 'week']
  ```

  이 목록에는 엔지니어링된 모든 피쳐 이름이 포함됩니다.

  >[!Note]
  >'타임시리즈 변압기'를 작업='예측'에 사용하고, '데이터 변압기'를 '회귀' 또는 '분류' 작업에 사용합니다.

+ API 2: `get_featurization_summary()` 모든 입력 기능에 대한 위화 요약을 반환합니다.

  Usage:
  ```python
  fitted_model.named_steps['timeseriestransformer'].get_featurization_summary()
  ```

  >[!Note]
  >'타임시리즈 변압기'를 작업='예측'에 사용하고, '데이터 변압기'를 '회귀' 또는 '분류' 작업에 사용합니다.

  출력:
  ```
  [{'RawFeatureName': 'A',
    'TypeDetected': 'Numeric',
    'Dropped': 'No',
    'EngineeredFeatureCount': 2,
    'Tranformations': ['MeanImputer', 'ImputationMarker']},
   {'RawFeatureName': 'B',
    'TypeDetected': 'Numeric',
    'Dropped': 'No',
    'EngineeredFeatureCount': 2,
    'Tranformations': ['MeanImputer', 'ImputationMarker']},
   {'RawFeatureName': 'C',
    'TypeDetected': 'Numeric',
    'Dropped': 'Yes',
    'EngineeredFeatureCount': 0,
    'Tranformations': []},
   {'RawFeatureName': 'D',
    'TypeDetected': 'DateTime',
    'Dropped': 'No',
    'EngineeredFeatureCount': 11,
    'Tranformations': ['DateTime','DateTime','DateTime','DateTime','DateTime','DateTime','DateTime','DateTime','DateTime','DateTime','DateTime']}]
  ```

   위치:

   |출력|정의|
   |----|--------|
   |원시 기능 이름|제공된 데이터 집합에서 피쳐/열 이름을 입력합니다.|
   |입력|입력 기능의 데이터 형식을 검색했습니다.|
   |Dropped|입력 기능이 삭제되었는지 또는 사용되었는지 를 나타냅니다.|
   |엔지니어링 특징 카운트|자동화된 피쳐 엔지니어링 변환을 통해 생성된 피쳐 수입니다.|
   |변환|엔지니어링피쳐를 생성하기 위해 입력 피쳐에 적용된 변환 목록입니다.|
   
### <a name="customize-feature-engineering"></a>피처 엔지니어링 사용자 지정
피쳐 엔지니어링을 사용자 `"featurization": FeaturizationConfig`지정하려면 을 지정합니다.

지원되는 사용자 지정에는 다음이 포함됩니다.

|사용자 지정|정의|
|--|--|
|열 용도 업데이트|지정된 열에 대한 피쳐 유형을 재정의합니다.|
|변압기 매개 변수 업데이트 |지정된 변압기에 대한 매개 변수를 업데이트합니다. 현재 Imputer (평균, 가장 빈번한 & 중앙값)와 HashOneHotEncoder를 지원합니다.|
|열 삭제 |기둥은 위업되는 에서 드롭합니다.|
|블록 변압기| 블록 변압기는 위화 공정에 사용할 수 있습니다.|

API 호출을 사용하여 FeaturizationConfig 개체만들기:
```python
featurization_config = FeaturizationConfig()
featurization_config.blocked_transformers = ['LabelEncoder']
featurization_config.drop_columns = ['aspiration', 'stroke']
featurization_config.add_column_purpose('engine-size', 'Numeric')
featurization_config.add_column_purpose('body-style', 'CategoricalHash')
#default strategy mean, add transformer param for for 3 columns
featurization_config.add_transformer_params('Imputer', ['engine-size'], {"strategy": "median"})
featurization_config.add_transformer_params('Imputer', ['city-mpg'], {"strategy": "median"})
featurization_config.add_transformer_params('Imputer', ['bore'], {"strategy": "most_frequent"})
featurization_config.add_transformer_params('HashOneHotEncoder', [], {"number_of_bits": 3})
```

### <a name="scalingnormalization-and-algorithm-with-hyperparameter-values"></a>하이퍼 매개 변수 값을 가진 크기 조정 / 정규화 및 알고리즘 :

파이프라인의 크기 조정/정규화 및 알고리즘/하이퍼매개 변수 값을 이해하려면 fitted_model.단계를 사용합니다. [크기 조정/정규화에 대해 자세히 알아봅니다.](concept-automated-ml.md#preprocess) 샘플 출력은 다음과 같습니다.

```
[('RobustScaler', RobustScaler(copy=True, quantile_range=[10, 90], with_centering=True, with_scaling=True)), ('LogisticRegression', LogisticRegression(C=0.18420699693267145, class_weight='balanced', dual=False, fit_intercept=True, intercept_scaling=1, max_iter=100, multi_class='multinomial', n_jobs=1, penalty='l2', random_state=None, solver='newton-cg', tol=0.0001, verbose=0, warm_start=False))
```

자세한 내용을 얻으려면 이 샘플 전자 [필기장에](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification/auto-ml-classification.ipynb)표시된 이 도우미 기능을 사용합니다.

```python
from pprint import pprint


def print_model(model, prefix=""):
    for step in model.steps:
        print(prefix + step[0])
        if hasattr(step[1], 'estimators') and hasattr(step[1], 'weights'):
            pprint({'estimators': list(
                e[0] for e in step[1].estimators), 'weights': step[1].weights})
            print()
            for estimator in step[1].estimators:
                print_model(estimator[1], estimator[0] + ' - ')
        else:
            pprint(step[1].get_params())
            print()


print_model(fitted_model)
```

다음 샘플 출력은 특정 알고리즘을 사용하는 파이프라인에 대한 것입니다(이 경우 RobustScalar를 사용한 로지스틱리그레션).

```
RobustScaler
{'copy': True,
'quantile_range': [10, 90],
'with_centering': True,
'with_scaling': True}

LogisticRegression
{'C': 0.18420699693267145,
'class_weight': 'balanced',
'dual': False,
'fit_intercept': True,
'intercept_scaling': 1,
'max_iter': 100,
'multi_class': 'multinomial',
'n_jobs': 1,
'penalty': 'l2',
'random_state': None,
'solver': 'newton-cg',
'tol': 0.0001,
'verbose': 0,
'warm_start': False}
```

### <a name="predict-class-probability"></a>클래스 확률 예측

자동화된 ML을 사용하여 생성된 모델에는 모두 오픈 소스 원본 클래스의 기능을 미러하는 래퍼 개체가 있습니다. 자동화된 ML에서 반환되는 대부분의 분류 `predict_proba()` 모델 래퍼 개체는 기능의 배열 유사 또는 희소 행렬 데이터 샘플을 받아들이고 각 샘플및 해당 클래스 확률의 n차원 배열을 반환하는 함수를 구현합니다.

위에서 동일한 호출을 사용하여 최상의 실행 및 피팅 모델을 검색했다고 `predict_proba()` 가정하면 피팅된 모델에서 `X_test` 직접 호출하여 모델 유형에 따라 적절한 형식으로 샘플을 제공할 수 있습니다.

```python
best_run, fitted_model = automl_run.get_output()
class_prob = fitted_model.predict_proba(X_test)
```

기본 모델이 함수를 `predict_proba()` 지원하지 않거나 형식이 올바르지 않으면 모델 클래스별 예외가 throw됩니다. 이 함수가 다양한 모델 유형에 대해 구현되는 방법에 대한 예제는 [RandomForest분류자](https://scikit-learn.org/stable/modules/generated/sklearn.ensemble.RandomForestClassifier.html#sklearn.ensemble.RandomForestClassifier.predict_proba) 및 [XGBoost](https://xgboost.readthedocs.io/en/latest/python/python_api.html) 참조 문서를 참조하십시오.

<a name="explain"></a>

## <a name="model-interpretability"></a>모델 해석력

모델 해석성을 사용하면 모델이 예측을 한 이유와 기본 피쳐 중요도 값을 이해할 수 있습니다. SDK에는 로컬 및 배포된 모델에 대한 교육 및 추론 시간 모두에서 모델 해석 기능을 활성화하기 위한 다양한 패키지가 포함되어 있습니다.

자동화된 기계 학습 실험 에서 해석 기능을 활성화하는 방법에 대한 코드 샘플의 [방법을](how-to-machine-learning-interpretability-automl.md) 참조하십시오.

자동화된 기계 학습 이외의 SDK의 다른 영역에서 모델 설명 및 기능 중요성을 활성화하는 방법에 대한 일반적인 내용은 해석가능성에 대한 [개념](how-to-machine-learning-interpretability.md) 문서를 참조하십시오.

## <a name="next-steps"></a>다음 단계

[모델 배포 방법 및 위치](how-to-deploy-and-where.md)에 대해 자세히 알아봅니다.

[자동화된 기계 학습을 사용하여 회귀 모델을 학습하는 방법](tutorial-auto-train-models.md) 또는 [원격 리소스에서 자동화된 기계 학습을 사용하여 학습하는 방법에](how-to-auto-train-remote.md)대해 자세히 알아봅니다.
