---
title: 자동화된 ML 실험 만들기
titleSuffix: Azure Machine Learning
description: 자동화된 기계 학습은 사용자를 위한 알고리즘을 선택하고 배포할 준비가 된 모델을 생성합니다. 자동화된 기계 학습 실험을 구성하는 데 사용할 수 있는 옵션에 대해 알아봅니다.
author: cartacioS
ms.author: sacartac
ms.reviewer: nibaccam
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.date: 05/20/2020
ms.topic: conceptual
ms.custom: how-to, tracking-python
ms.openlocfilehash: ec5776791f55a406b8015868dce83243b3f8efbd
ms.sourcegitcommit: 1b2d1755b2bf85f97b27e8fbec2ffc2fcd345120
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/04/2020
ms.locfileid: "87552393"
---
# <a name="configure-automated-ml-experiments-in-python"></a>Python에서 자동화된 ML 실험 구성
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

이 가이드에서는 [Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)를 사용하여 자동화된 Machine Learning 실험의 다양한 구성 설정을 정의하는 방법에 대해 알아봅니다. 자동화된 Machine Learning은 사용자를 위한 알고리즘과 하이퍼 매개 변수를 선택하고 배포할 준비가 된 모델을 생성합니다. 자동화된 Machine Learning 실험을 구성하는 데 사용할 수 있는 옵션에 대해 알아봅니다.

자동화된 Machine Learning 실험 예제를 보려면 [자습서: 자동화된 Machine Learning을 사용하여 분류 모델 학습](tutorial-auto-train-models.md) 또는 [클라우드의 자동화된 Machine Learning을 사용하여 모델 학습](how-to-auto-train-remote.md)을 참조하세요.

자동화된 기계 학습에서 사용할 수 있는 구성 옵션은 다음과 같습니다.

* 실험 유형 선택: 분류, 회귀 또는 시계열 예측
* 데이터 원본, 형식 및 데이터 가져오기
* 컴퓨팅 대상 선택: 로컬 또는 원격
* 자동화된 Machine Learning 실험 설정
* 자동화된 Machine Learning 실험 실행
* 모델 메트릭 탐색
* 모델 등록 및 배포

코드 없는 환경을 선호하는 경우 [Azure Machine Learning 스튜디오에서 자동화된 Machine Learning 만들기](how-to-use-automated-ml-for-ml-models.md)가 가능합니다.

## <a name="select-your-experiment-type"></a>실험 유형 선택

실험을 시작하기 전에 해결하려는 기계 학습 문제의 종류를 결정해야 합니다. 자동화된 Machine Learning은 작업 유형으로 분류, 회귀 및 예측을 지원합니다. [작업 유형](concept-automated-ml.md#when-to-use-automl-classify-regression--forecast)에 대해 자세히 알아보세요.

자동화된 Machine Learning은 자동화 및 튜닝 프로세스 중에 다음 알고리즘을 지원합니다. 사용자는 알고리즘을 지정할 필요가 없습니다.

> [!NOTE]
> 자동 ML에서 만든 모델을 [ONNX 모델](concept-onnx.md)로 내보내려는 경우 *가 표시된 알고리즘만 ONNX 형식으로 변환할 수 있습니다. [모델을 ONNX로 변환](concept-automated-ml.md#use-with-onnx)하는 방법에 대해 자세히 알아보세요. <br> <br> ONNX는 현재 분류 및 회귀 작업만 지원한다는 사실도 기억하세요. 

분류 | 회귀 | 시계열 예측
|-- |-- |--
[로지스틱 회귀](https://scikit-learn.org/stable/modules/linear_model.html#logistic-regression)* | [Elastic Net](https://scikit-learn.org/stable/modules/linear_model.html#elastic-net)* | [Elastic Net](https://scikit-learn.org/stable/modules/linear_model.html#elastic-net)
[Light GBM](https://lightgbm.readthedocs.io/en/latest/index.html)* |[Light GBM](https://lightgbm.readthedocs.io/en/latest/index.html)*|[Light GBM](https://lightgbm.readthedocs.io/en/latest/index.html)
[경사 부스팅](https://scikit-learn.org/stable/modules/ensemble.html#classification)* |[경사 부스팅](https://scikit-learn.org/stable/modules/ensemble.html#regression)* |[경사 부스팅](https://scikit-learn.org/stable/modules/ensemble.html#regression)
[의사 결정 트리](https://scikit-learn.org/stable/modules/tree.html#decision-trees)* |[의사 결정 트리](https://scikit-learn.org/stable/modules/tree.html#regression)* |[Decision Tree](https://scikit-learn.org/stable/modules/tree.html#regression)
[K Nearest Neighbors](https://scikit-learn.org/stable/modules/neighbors.html#nearest-neighbors-regression)* |[K Nearest Neighbors](https://scikit-learn.org/stable/modules/neighbors.html#nearest-neighbors-regression)* |[K Nearest Neighbors](https://scikit-learn.org/stable/modules/neighbors.html#nearest-neighbors-regression)
[선형 SVC](https://scikit-learn.org/stable/modules/svm.html#classification)* |[LARS Lasso](https://scikit-learn.org/stable/modules/linear_model.html#lars-lasso)* |[LARS Lasso](https://scikit-learn.org/stable/modules/linear_model.html#lars-lasso)
[SVC(Support Vector Classification)](https://scikit-learn.org/stable/modules/svm.html#classification)* |[SGD(Stochastic Gradient Descent)](https://scikit-learn.org/stable/modules/sgd.html#regression)* |[SGD(Stochastic Gradient Descent)](https://scikit-learn.org/stable/modules/sgd.html#regression)
[임의 포리스트](https://scikit-learn.org/stable/modules/ensemble.html#random-forests)* |[임의 포리스트](https://scikit-learn.org/stable/modules/ensemble.html#random-forests)* |[Random Forest](https://scikit-learn.org/stable/modules/ensemble.html#random-forests)
[Extremely Randomized Trees](https://scikit-learn.org/stable/modules/ensemble.html#extremely-randomized-trees)* |[Extremely Randomized Trees](https://scikit-learn.org/stable/modules/ensemble.html#extremely-randomized-trees)* |[Extremely Randomized Trees](https://scikit-learn.org/stable/modules/ensemble.html#extremely-randomized-trees)
[Xgboost](https://xgboost.readthedocs.io/en/latest/parameter.html)* |[Xgboost](https://xgboost.readthedocs.io/en/latest/parameter.html)* | [Xgboost](https://xgboost.readthedocs.io/en/latest/parameter.html)
[평균 퍼셉트론 분류자](https://docs.microsoft.com/python/api/nimbusml/nimbusml.linear_model.averagedperceptronbinaryclassifier?view=nimbusml-py-latest)|[온라인 그라데이션 하강 회귀 변수](https://docs.microsoft.com/python/api/nimbusml/nimbusml.linear_model.onlinegradientdescentregressor?view=nimbusml-py-latest) |[자동 ARIMA](https://www.alkaline-ml.com/pmdarima/modules/generated/pmdarima.arima.auto_arima.html#pmdarima.arima.auto_arima)
[Naive Bayes](https://scikit-learn.org/stable/modules/naive_bayes.html#bernoulli-naive-bayes)* |[고속 선형 회귀 변수](https://docs.microsoft.com/python/api/nimbusml/nimbusml.linear_model.fastlinearregressor?view=nimbusml-py-latest)|[Prophet](https://facebook.github.io/prophet/docs/quick_start.html)
[SGD(Stochastic Gradient Descent)](https://scikit-learn.org/stable/modules/sgd.html#sgd)* ||ForecastTCN
|[선형 SVM 분류자](https://docs.microsoft.com/python/api/nimbusml/nimbusml.linear_model.linearsvmbinaryclassifier?view=nimbusml-py-latest)*||

`AutoMLConfig` 생성자에서 `task` 매개 변수를 사용하여 실험 유형을 지정합니다.

```python
from azureml.train.automl import AutoMLConfig

# task can be one of classification, regression, forecasting
automl_config = AutoMLConfig(task = "classification")
```

## <a name="data-source-and-format"></a>데이터 원본 및 형식

자동화된 Machine Learning은 로컬 데스크톱 또는 Azure Blob Storage와 같은 클라우드의 데이터를 지원합니다. 데이터를 **Pandas 데이터 프레임** 또는 **Azure Machine Learning TabularDataset**로 읽어 들일 수 있습니다.  [데이터 세트](how-to-create-register-datasets.md)에 대해 자세히 알아보세요.

학습 데이터 요구 사항:
- 데이터는 테이블 형식이어야 합니다.
- 예측하려는 값(대상 열)이 데이터에 있어야 합니다.

다음 코드 예제에서는 데이터를 이러한 형식으로 저장하는 방법을 보여줍니다.

* TabularDataset

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

원격 실행의 경우 원격 컴퓨팅에서 학습 데이터에 액세스할 수 있어야 합니다. SDK의 [`Datasets`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py) 클래스는 다음과 같은 기능을 제공합니다.

* 정적 파일 또는 URL 원본에서 작업 영역으로 데이터를 쉽게 전송
* 클라우드 컴퓨팅 리소스에서 실행할 때 데이터를 학습 스크립트에 사용 가능

`Dataset` 클래스를 사용하여 컴퓨팅 대상에 데이터를 탑재하는 예제는 [방법](how-to-train-with-datasets.md#mount-files-to-remote-compute-targets)을 참조하세요.

## <a name="train-and-validation-data"></a>데이터 학습 및 유효성 검사

생성자에서 다음 옵션을 사용 하 여 별도의 학습 및 유효성 검사 집합을 직접 지정할 수 있습니다 `AutoMLConfig` . AutoML 실험의 [데이터 분할 및 교차 유효성 검사를 구성 하는 방법](how-to-configure-cross-validation-data-splits.md) 에 대해 자세히 알아보세요. 

### <a name="k-folds-cross-validation"></a>K 접기 교차 유효성 검사

`n_cross_validations` 설정을 사용하여 교차 유효성 검사의 수를 지정합니다. 학습 데이터 집합은 무작위로 동일한 크기의 `n_cross_validations` 접기로 분할됩니다. 각 교차 유효성 검사 라운드 중에 접기 중 하나는 나머지 접기에 대해 학습된 모델의 유효성 검사에 사용됩니다. 이 프로세스는 각 접기가 유효성 검사 집합으로 한 번 사용될 때까지 `n_cross_validations` 라운드 동안 반복됩니다. 모든 `n_cross_validations` 라운드에 걸친 평균 점수가 보고되고 해당 모델이 전체 학습 데이터 세트에 대해 다시 학습됩니다.

autoML이 교차 유효성 검사를 적용하여 [과잉 맞춤 모델을 방지](concept-manage-ml-pitfalls.md#prevent-over-fitting)하는 방법을 알아보세요.

### <a name="monte-carlo-cross-validation-repeated-random-sub-sampling"></a>몬테카를로 교차 유효성 검사(반복 무작위 하위 샘플링)

`validation_size`를 사용하여 유효성 검사에 사용해야 하는 학습 데이터 세트의 비율을 지정하고, `n_cross_validations`를 사용하여 교차 유효성 검사의 수를 지정합니다. 각 교차 유효성 검사 라운드 중에 나머지 데이터에 대해 학습된 모델의 유효성 검사를 위해 `validation_size` 크기의 하위 집합이 무작위로 선택됩니다. 마지막으로, 모든 `n_cross_validations` 라운드에 걸친 평균 점수가 보고되고 해당 모델이 전체 학습 데이터 집합에 대해 다시 학습됩니다. 몬테카를로는 시계열 예측을 지원하지 않습니다.

### <a name="custom-validation-dataset"></a>사용자 지정 유효성 검사 데이터 세트

무작위 분할(일반적으로 시계열 데이터 또는 불균형 데이터)이 허용되지 않는 경우 사용자 지정 유효성 검사 데이터 세트를 사용합니다. 사용자 고유의 유효성 검사 데이터 세트를 지정할 수 있습니다. 모델은 무작위 데이터 세트 대신 지정된 유효성 검사 데이터 세트에 대해 평가됩니다. [SDK를 사용 하 여 사용자 지정 유효성 검사 집합을 구성 하는 방법](how-to-configure-cross-validation-data-splits.md#provide-validation-data)에 대해 자세히 알아보세요.

## <a name="compute-to-run-experiment"></a>실험 실행 컴퓨팅

다음으로, 모델을 학습할 위치를 결정합니다. 자동화된 Machine Learning 실험은 다음 컴퓨팅 옵션에서 실행할 수 있습니다.
* 로컬 데스크톱 또는 랩톱과 같은 로컬 머신 - 일반적으로 데이터 세트가 작고 아직 탐색 단계에 있는 경우입니다.
* 클라우드의 원격 머신 - [Azure Machine Learning Managed Compute](concept-compute-target.md#amlcompute)는 Azure Virtual Machines 클러스터에서 Machine Learning 모델을 학습할 수 있도록 하는 관리형 서비스입니다. 

  로컬 및 원격 컴퓨팅 대상을 사용하는 Notebook 예제는 이 [GitHub 사이트](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning)를 참조하세요.

* Azure 구독의 Azure Databricks 클러스터 자세한 내용은 [자동화된 ML에 대한 Azure Databricks 클러스터 설정](how-to-configure-environment.md#azure-databricks)에서 확인할 수 있습니다.

  Azure Databricks를 사용하는 Notebook의 예제는 [이 GitHub 사이트](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/azure-databricks/automl)를 참조하세요.

<a name='configure-experiment'></a>

## <a name="configure-your-experiment-settings"></a>실험 설정 구성

자동화된 Machine Learning 실험을 구성하는 데 사용할 수 있는 옵션에 대해 알아봅니다. 이러한 매개 변수는 `AutoMLConfig` 개체를 인스턴스화하여 설정됩니다. 매개 변수의 전체 목록은 [AutoMLConfig 클래스](/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig)를 참조하세요.

일부 사례:

1. AUC 가중치를 기본 메트릭으로 사용하며 실험 제한 시간(분)이 30분으로 설정되고 교차 유효성 검사 접기가 2회인 분류 실험.

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
2. 다음은 5회의 유효성 검사 교차 접기를 사용하여 60분 후에 종료되도록 설정된 회귀 실험의 예입니다.

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

세 가지 `task` 매개 변수 값(세 번째 작업 유형은 `forecasting`이며 `regression` 작업과 유사한 알고리즘 풀을 사용)에 따라 적용할 모델 목록이 결정됩니다. `whitelist` 또는 `blacklist` 매개 변수를 사용하여 가용 모델을 포함 또는 제외하도록 반복을 추가로 수정합니다. 지원되는 모델 목록은 ([분류](https://docs.microsoft.com/python/api/azureml-train-automl-client/azureml.train.automl.constants.supportedmodels.classification), [예측](https://docs.microsoft.com/python/api/azureml-train-automl-client/azureml.train.automl.constants.supportedmodels.forecasting) 및 [회귀](https://docs.microsoft.com/python/api/azureml-train-automl-client/azureml.train.automl.constants.supportedmodels.regression))에 대한 [SupportedModels 클래스](https://docs.microsoft.com/python/api/azureml-train-automl-client/azureml.train.automl.constants.supportedmodels)에서 찾을 수 있습니다.

실험 제한 시간 오류를 방지하기 위해, 자동화된 ML의 유효성 검사 서비스는 행 * 열 크기가 1000만을 초과할 경우 `experiment_timeout_minutes`를 최소 15분 또는 60분으로 설정하도록 요구합니다.

### <a name="primary-metric"></a>기본 메트릭
기본 메트릭은 최적화를 위해 모델을 학습시키는 동안 사용할 메트릭을 결정합니다. 선택하는 작업 유형에 따라 선택 가능한 메트릭이 결정되며, 다음 표에서는 각 작업 유형에 유효한 기본 메트릭을 보여줍니다.

|분류 | 회귀 | 시계열 예측
|-- |-- |--
|accuracy| spearman_correlation | spearman_correlation
|AUC_weighted | normalized_root_mean_squared_error | normalized_root_mean_squared_error
|average_precision_score_weighted | r2_score | r2_score
|norm_macro_recall | normalized_mean_absolute_error | normalized_mean_absolute_error
|precision_score_weighted |

[자동화된 Machine Learning 결과 이해](how-to-understand-automated-ml.md)에서 이러한 메트릭의 정의에 대해 알아보세요.

### <a name="data-featurization"></a>데이터 기능화

모든 자동화된 Machine Learning 실험에서, 스케일이 다른 기능에 중요한 *특정* 알고리즘을 지원할 수 있도록 데이터가 [자동으로 스케일링 및 정규화](how-to-configure-auto-features.md#)됩니다.  그러나 누락 값 대체, 인코딩, 변환 등의 추가 기능화를 사용하도록 설정할 수도 있습니다.

개체에서 실험을 구성할 때 `AutoMLConfig` 설정을 사용 하거나 사용 하지 않도록 설정할 수 있습니다 `featurization` . 다음 표는 [AutoMLConfig 클래스](/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig)에서 기능화에 허용되는 설정을 보여줍니다.

|기능화 구성 | Description |
| ------------- | ------------- |
|`"featurization": 'auto'`| 전처리의 일부로 [데이터 가드 레일 및 기능화 단계](how-to-configure-auto-features.md#featurization)가 자동으로 수행된다는 것을 나타냅니다. **기본 설정**|
|`"featurization": 'off'`| 기능화 단계를 자동으로 수행하면 안 된다는 것을 나타냅니다.|
|`"featurization":`&nbsp;`'FeaturizationConfig'`| 사용자 지정된 기능화 단계를 사용해야 한다는 것을 나타냅니다. [기능화를 사용자 지정하는 방법을 알아보세요](how-to-configure-auto-features.md#customize-featurization).|

> [!NOTE]
> 자동화된 Machine Learning 기능화 단계(기능 정규화, 누락된 데이터 처리, 텍스트를 숫자로 변환 등)는 기본 모델의 일부가 됩니다. 예측에 모델을 사용하는 경우 학습 중에 적용되는 동일한 기능화 단계가 입력 데이터에 자동으로 적용됩니다.

### <a name="time-series-forecasting"></a>시계열 예측
시계열 `forecasting` 작업에는 다음과 같은 구성 개체의 추가 매개 변수가 필요합니다.

1. `time_column_name`: 유효한 시계열을 포함하는 학습 데이터의 열 이름을 정의하는 필수 매개 변수입니다.
1. `forecast_horizon`: 예측 하려는 기간을 정의 합니다. 정수 가로선은 시계열 frequency의 단위입니다. 예를 들어 일별 빈도로 학습 데이터가 있는 경우 모델을 학습 하는 데 사용할 일 수를 정의 합니다.
1. `time_series_id_column_names`: 타임 스탬프를 사용 하는 여러 행이 있는 데이터의 시계열을 고유 하 게 식별 하는 열을 정의 합니다. 예를 들어 매장에서 특정 브랜드의 판매를 예측 하는 경우 매장 및 브랜드 열을 시계열 식별자로 정의 합니다. 각 그룹화에 대해 별도의 예측이 생성 됩니다. 시계열 식별자가 정의 되지 않은 경우 데이터 집합은 하나의 시계열으로 간주 됩니다.

아래에 사용되는 설정의 예제는 [샘플 Notebook](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-orange-juice-sales/auto-ml-forecasting-orange-juice-sales.ipynb)을 참조하세요.

```python
# Setting Store and Brand as time series identifiers for training.
time_series_id_column_names = ['Store', 'Brand']
nseries = data.groupby(time_series_id_column_names).ngroups

# View the number of time series data with defined time series identifiers
print('Data contains {0} individual time-series.'.format(nseries))
```

```python
time_series_settings = {
    'time_column_name': time_column_name,
    'time_series_id_column_names': time_series_id_column_names,
    'drop_column_names': ['logQuantity'],
    'forecast_horizon': n_test_periods
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

### <a name="ensemble-configuration"></a><a name="ensemble"></a> 앙상블 구성

앙상블 모델은 기본적으로 사용하도록 설정하며, 자동화된 Machine Learning 실행에서 최종 실행 반복으로 표시됩니다. 현재 지원되는 앙상블 메서드는 투표 및 스택입니다. 투표는 가중 평균을 사용하는 소프트 투표로 구현되고 스택 구현은 2계층 구현을 사용하는데, 여기서 첫 번째 계층은 투표 앙상블과 동일한 모델을 사용하고, 두 번째 계층 모델은 첫 번째 계층에서 최적의 모델 조합을 찾는 데 사용됩니다. ONNX 모델을 사용하는 경우 **또는** 모델 설명 가능성을 사용하도록 설정한 경우 스택이 사용되지 않고 투표만 사용됩니다.

기본 앙상블 동작을 변경하기 위해 `AutoMLConfig` 개체에 `kwargs`로 제공할 수 있는 여러 기본 인수가 있습니다.

* `ensemble_download_models_timeout_sec`: **VotingEnsemble** 및 **StackEnsemble** 모델이 생성되는 동안 이전 자식 실행에서 여러 맞춤 모델이 다운로드됩니다. `AutoMLEnsembleException: Could not find any models for running ensembling` 오류가 발생할 경우 모델 다운로드 시간을 더 길게 해야 할 수도 있습니다. 모델을 병렬로 다운로드하는 기본값은 300초이고 최대 시간 제한은 없습니다. 시간이 더 필요한 경우 이 매개 변수를 300초보다 큰 값으로 구성합니다. 

  > [!NOTE]
  >  시간 제한에 도달했을 때 다운로드된 모델이 있으면 그때까지 다운로드된 모델을 사용하여 앙상블이 진행됩니다. 시간 제한 내에 완료하려면 반드시 모든 모델을 다운로드해야 하는 것은 아닙니다.

다음 매개 변수는 **StackEnsemble** 모델에만 적용됩니다. 

* `stack_meta_learner_type`: 메타 학습자은 개별 이기종 모델의 출력에 대해 학습된 모델입니다. 기본 메타 학습자는 분류 작업인 경우 `LogisticRegression`(또는 교차 유효성 검사를 사용하는 경우 `LogisticRegressionCV`)이고, 회귀/예측 작업인 경우 `ElasticNet`(교차 유효성 검사를 사용하는 경우 `ElasticNetCV`)입니다. 이 매개 변수는 문자열 `LogisticRegression`, `LogisticRegressionCV`, `LightGBMClassifier`, `ElasticNet`, `ElasticNetCV`, `LightGBMRegressor` 또는 `LinearRegression` 중 하나입니다.

* `stack_meta_learner_train_percentage`: (학습의 학습 및 유효성 검사 유형을 선택할 때) 메타 학습자의 학습을 위해 예약할 학습 세트의 비율을 지정합니다. 기본값은 `0.2`입니다. 

* `stack_meta_learner_kwargs`: 메타 학습자의 이니셜라이저에 전달할 선택적 매개 변수입니다. 이러한 매개 변수 및 매개 변수 형식은 해당 모델 생성자의 매개 변수 및 매개 변수 형식을 미러링하며, 모델 생성자에 전달됩니다.

다음 코드는 `AutoMLConfig` 개체에서 사용자 지정 앙상블 동작을 지정하는 예를 보여줍니다.

```python
ensemble_settings = {
    "ensemble_download_models_timeout_sec": 600
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

앙상블 학습은 기본적으로 사용하도록 설정되지만, `enable_voting_ensemble` 및 `enable_stack_ensemble` 부울 매개 변수를 사용하여 비활성화할 수 있습니다.

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

자동화된 ML의 경우 실험을 실행하는 데 사용되는 `Workspace`의 명명된 개체인 `Experiment` 개체를 만들 수 있습니다.

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

### <a name="exit-criteria"></a><a name="exit"></a> 종료 기준

실험을 종료하도록 정의할 수 있는 몇 가지 옵션이 있습니다.
1. 조건 없음: 종료 매개 변수를 정의하지 않으면 기본 메트릭이 더 이상 진행되지 않을 때까지 실험이 계속됩니다.
1. 일정 시간 후 종료: 설정에서 `experiment_timeout_minutes`를 사용하여 실험이 계속 실행되는 시간(분)을 정의할 수 있습니다.
1. 일정 점수에 도달한 후 종료: `experiment_exit_score`를 사용하면 기본 메트릭 점수에 도달한 후 실험이 완료됩니다.

### <a name="explore-model-metrics"></a>모델 메트릭 탐색

Notebook을 사용 중이면 위젯 또는 인라인에서 결과를 볼 수 있습니다. 자세한 내용은 [모델 추적 및 평가](how-to-monitor-view-training-logs.md#monitor-automated-machine-learning-runs)를 참조하세요.

웹 서비스에 배포할 모델을 다운로드 하거나 등록 하는 방법에 대 한 자세한 내용은 모델을 배포 하는 [방법 및 위치](how-to-deploy-and-where.md)를 참조 하세요.

## <a name="understand-automated-ml-models"></a>자동화된 ML 모델의 이해

자동화된 ML을 사용하여 생성되는 모든 모델은 다음 단계를 거치게 됩니다.
+ 자동화된 기능 엔지니어링(`"featurization": 'auto'`인 경우)
+ 하이퍼 매개 변수 값을 사용한 스케일링/정규화 및 알고리즘

자동화된 ML의 fitted_model 출력에서 가져올 수 있도록 이 정보를 투명하게 만들었습니다.

```python
automl_config = AutoMLConfig(…)
automl_run = experiment.submit(automl_config …)
best_run, fitted_model = automl_run.get_output()
```

### <a name="automated-feature-engineering"></a>자동화된 기능 엔지니어링

`"featurization": 'auto'`일 때 발생하는 전처리 및 [자동화된 기능 엔지니어링]() 목록을 참조하세요.

다음 예를 살펴보세요.
+ 입력 기능으로 A(숫자), B(숫자), C(숫자), D(날짜/시간)가 있습니다.
+ 숫자 기능 C는 모든 값이 고유한 ID 열 이므로 삭제됩니다.
+ 숫자 기능 A와 B에는 누락된 값이 있으므로 평균으로 귀속됩니다.
+ 날짜/시간 기능 D는 엔지니어링된 기능 11개로 기능화됩니다.

맞춤 모델의 첫 번째 단계에서 다음 2개 API를 사용하여 더 자세히 알아보세요.  [이 샘플 Notebook](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning/forecasting-energy-demand)을 참조하세요.

+ API 1: `get_engineered_feature_names()`는 엔지니어링된 기능 이름 목록을 반환합니다.

  Usage:
  ```python
  fitted_model.named_steps['timeseriestransformer']. get_engineered_feature_names ()
  ```

  ```
  Output: ['A', 'B', 'A_WASNULL', 'B_WASNULL', 'year', 'half', 'quarter', 'month', 'day', 'hour', 'am_pm', 'hour12', 'wday', 'qday', 'week']
  ```

  이 목록에는 엔지니어링된 기능 이름이 모두 포함됩니다.

  >[!Note]
  >'예측' 작업에는 'timeseriestransformer'를 사용하고, '회귀' 또는 '분류' 작업에는 'datatransformer'를 사용합니다.

+ API 2: `get_featurization_summary()`는 모든 입력 기능에 대한 기능화 요약 정보를 반환합니다.

  Usage:
  ```python
  fitted_model.named_steps['timeseriestransformer'].get_featurization_summary()
  ```

  >[!Note]
  >'예측' 작업에는 'timeseriestransformer'를 사용하고, '회귀' 또는 '분류' 작업에는 'datatransformer'를 사용합니다.

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
   |RawFeatureName|제공된 데이터 세트의 입력 기능/열 이름입니다.|
   |TypeDetected|입력 기능의 데이터 형식이 감지되었습니다.|
   |Dropped|입력 기능이 삭제되었는지 아니면 사용되었는지 여부를 나타냅니다.|
   |EngineeringFeatureCount|자동화된 기능 엔지니어링 변환을 통해 생성된 기능의 수입니다.|
   |변환|엔지니어링된 기능을 생성하기 위해 입력 기능에 적용된 변환 목록입니다.|
### <a name="scalingnormalization-and-algorithm-with-hyperparameter-values"></a>하이퍼 매개 변수 값을 사용한 스케일링/정규화 및 알고리즘:

파이프라인에 대한 스케일링/정규화 및 알고리즘/하이퍼 매개 변수 값을 이해하려면 fitted_model.steps를 사용합니다. [스케일링/정규화에 대해 자세히 알아보세요](how-to-configure-auto-features.md). 샘플 출력은 다음과 같습니다.

```
[('RobustScaler', RobustScaler(copy=True, quantile_range=[10, 90], with_centering=True, with_scaling=True)), ('LogisticRegression', LogisticRegression(C=0.18420699693267145, class_weight='balanced', dual=False, fit_intercept=True, intercept_scaling=1, max_iter=100, multi_class='multinomial', n_jobs=1, penalty='l2', random_state=None, solver='newton-cg', tol=0.0001, verbose=0, warm_start=False))
```

자세한 내용을 보려면 다음 도우미 함수를 사용하세요. 

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


print_model(model)
```

다음은 특정 알고리즘을 사용하는 파이프라인의 샘플 출력입니다(여기서는 RobustScalar과 함께 LogisticRegression 사용).

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

자동화된 ML을 사용하여 생성되는 모든 모델은 해당 오픈 소스 클래스의 기능을 미러링하는 래퍼 개체를 갖고 있습니다. 자동화된 ML에서 반환하는 대부분의 분류 모델 래퍼 개체는 기능(X 값)의 배열 형식 또는 스파스 행렬 데이터 샘플을 허용하는 `predict_proba()` 함수를 구현하고, 각 샘플의 n차원 배열 및 해당 클래스 확률을 반환합니다.

위에서 동일한 호출을 사용하여 가장 적합한 실행과 맞춤 모델을 검색했다면 맞춤 모델에서 직접 `predict_proba()`를 호출하고 모델 유형에 맞는 적절한 형식으로 `X_test`를 입력할 수 있습니다.

```python
best_run, fitted_model = automl_run.get_output()
class_prob = fitted_model.predict_proba(X_test)
```

기본 모델이 `predict_proba()` 함수를 지원하지 않거나 형식이 잘못된 경우 모델 클래스 관련 예외가 throw됩니다. 이 함수를 다른 모델 유형에 맞게 구현하는 방법의 예제는 [RandomForestClassifier](https://scikit-learn.org/stable/modules/generated/sklearn.ensemble.RandomForestClassifier.html#sklearn.ensemble.RandomForestClassifier.predict_proba) 및 [XGBoost](https://xgboost.readthedocs.io/en/latest/python/python_api.html) 참조 문서에서 확인할 수 있습니다.

<a name="explain"></a>

## <a name="model-interpretability"></a>모델 해석력

모델 해석력을 통해 모델이 예측을 수행한 이유와 기본 기능 중요도 값을 이해할 수 있습니다. SDK에는 로컬 모델과 배포된 모델의 학습 및 유추 시간에 모델 해석력 기능을 사용할 수 있도록 다양한 패키지가 포함되어 있습니다.

특히 자동화된 Machine Learning 실험 내에서 해석력 기능을 사용하도록 설정하는 방법에 대한 코드 샘플은 [방법](how-to-machine-learning-interpretability-automl.md)을 참조하세요.

자동화된 Machine Learning 외부의 다른 SDK 영역에서 모델 설명 및 기능 중요도를 사용하는 방법에 대한 일반적인 내용은 해석력에 대한 [개념](how-to-machine-learning-interpretability.md) 문서를 참조하세요.

> [!NOTE]
> ForecastTCN 모델은 현재 설명 클라이언트에서 지원 되지 않습니다. 이 모델은 최상의 모델로 반환 되 고 주문형 설명 실행을 지원 하지 않는 경우 설명 대시보드를 반환 하지 않습니다.

## <a name="next-steps"></a>다음 단계

+ [모델 배포 방법 및 위치](how-to-deploy-and-where.md)에 대해 자세히 알아봅니다.

+ [자동화된 Machine Learning을 사용하여 회귀 모델을 학습시키는 방법](tutorial-auto-train-models.md) 또는 [원격 리소스에서 자동화된 Machine Learning을 사용하여 학습하는 방법](how-to-auto-train-remote.md)에 대해 자세히 알아봅니다.
+ [많은 모델 솔루션 가속기](https://aka.ms/many-models)에서 autoML을 사용하여 여러 모델을 학습시키는 방법을 알아봅니다.
