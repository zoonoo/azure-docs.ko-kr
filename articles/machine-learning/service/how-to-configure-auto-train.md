---
title: 자동화된 ML 실험 만들기
titleSuffix: Azure Machine Learning
description: 자동화된 기계 학습은 사용자를 위한 알고리즘을 선택하고 배포할 준비가 된 모델을 생성합니다. 자동화된 기계 학습 실험을 구성하는 데 사용할 수 있는 옵션에 대해 알아봅니다.
author: nacharya1
ms.author: nilesha
ms.reviewer: sgilley
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 07/10/2019
ms.custom: seodec18
ms.openlocfilehash: 2ef501af63628b47bc52d416930c90057569b5de
ms.sourcegitcommit: 0fab4c4f2940e4c7b2ac5a93fcc52d2d5f7ff367
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/17/2019
ms.locfileid: "71035022"
---
# <a name="configure-automated-ml-experiments-in-python"></a>Python에서 자동화 된 ML 실험 구성

이 가이드에서는 [AZURE MACHINE LEARNING SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)를 사용 하 여 자동화 된 machine learning 실험의 다양 한 구성 설정을 정의 하는 방법에 대해 알아봅니다. 자동화된 Machine Learning은 사용자를 위한 알고리즘과 하이퍼 매개 변수를 선택하고 배포할 준비가 된 모델을 생성합니다. 자동화된 Machine Learning 실험을 구성하는 데 사용할 수 있는 옵션에 대해 알아봅니다.

자동화된 Machine Learning 실험 예제를 보려면 [자습서: 자동화된 Machine Learning을 사용하여 분류 모델 학습](tutorial-auto-train-models.md) 또는 [클라우드의 자동화된 Machine Learning을 사용하여 모델 학습](how-to-auto-train-remote.md)을 참조하세요.

자동화된 기계 학습에서 사용할 수 있는 구성 옵션은 다음과 같습니다.

* 실험 유형 선택: 분류, 회귀 또는 시계열 예측
* 데이터 원본, 형식 및 데이터 가져오기
* 컴퓨팅 대상 선택: 로컬 또는 원격
* 자동화된 Machine Learning 실험 설정
* 자동화된 Machine Learning 실험 실행
* 모델 메트릭 탐색
* 모델 등록 및 배포

코드를 사용 하지 않으려는 경우 [Azure Portal에서 자동화 된 machine learning 실험을 만들](how-to-create-portal-experiments.md)수도 있습니다.

## <a name="select-your-experiment-type"></a>실험 유형 선택

실험을 시작하기 전에 해결하려는 기계 학습 문제의 종류를 결정해야 합니다. 자동화된 Machine Learning은 작업 유형으로 분류, 회귀 및 예측을 지원합니다.

자동화된 Machine Learning은 자동화 및 튜닝 프로세스 중에 다음 알고리즘을 지원합니다. 사용자는 알고리즘을 지정할 필요가 없습니다.

분류 | 회귀 | 시계열 예측
|-- |-- |--
[Logistic Regression](https://scikit-learn.org/stable/modules/linear_model.html#logistic-regression)| [Elastic Net](https://scikit-learn.org/stable/modules/linear_model.html#elastic-net)| [Elastic Net](https://scikit-learn.org/stable/modules/linear_model.html#elastic-net)
[Light GBM](https://lightgbm.readthedocs.io/en/latest/index.html)|[Light GBM](https://lightgbm.readthedocs.io/en/latest/index.html)|[Light GBM](https://lightgbm.readthedocs.io/en/latest/index.html)
[Gradient Boosting](https://scikit-learn.org/stable/modules/ensemble.html#classification)|[Gradient Boosting](https://scikit-learn.org/stable/modules/ensemble.html#regression)|[Gradient Boosting](https://scikit-learn.org/stable/modules/ensemble.html#regression)
[Decision Tree](https://scikit-learn.org/stable/modules/tree.html#decision-trees)|[Decision Tree](https://scikit-learn.org/stable/modules/tree.html#regression)|[Decision Tree](https://scikit-learn.org/stable/modules/tree.html#regression)
[K Nearest Neighbors](https://scikit-learn.org/stable/modules/neighbors.html#nearest-neighbors-regression)|[K Nearest Neighbors](https://scikit-learn.org/stable/modules/neighbors.html#nearest-neighbors-regression)|[K Nearest Neighbors](https://scikit-learn.org/stable/modules/neighbors.html#nearest-neighbors-regression)
[Linear SVC](https://scikit-learn.org/stable/modules/svm.html#classification)|[LARS Lasso](https://scikit-learn.org/stable/modules/linear_model.html#lars-lasso)|[LARS Lasso](https://scikit-learn.org/stable/modules/linear_model.html#lars-lasso)
[C-SVC(Support Vector Classification)](https://scikit-learn.org/stable/modules/svm.html#classification)|[SGD(Stochastic Gradient Descent)](https://scikit-learn.org/stable/modules/sgd.html#regression)|[SGD(Stochastic Gradient Descent)](https://scikit-learn.org/stable/modules/sgd.html#regression)
[Random Forest](https://scikit-learn.org/stable/modules/ensemble.html#random-forests)|[Random Forest](https://scikit-learn.org/stable/modules/ensemble.html#random-forests)|[Random Forest](https://scikit-learn.org/stable/modules/ensemble.html#random-forests)
[Extremely Randomized Trees](https://scikit-learn.org/stable/modules/ensemble.html#extremely-randomized-trees)|[Extremely Randomized Trees](https://scikit-learn.org/stable/modules/ensemble.html#extremely-randomized-trees)|[Extremely Randomized Trees](https://scikit-learn.org/stable/modules/ensemble.html#extremely-randomized-trees)
[Xgboost](https://xgboost.readthedocs.io/en/latest/parameter.html)|[Xgboost](https://xgboost.readthedocs.io/en/latest/parameter.html)| [Xgboost](https://xgboost.readthedocs.io/en/latest/parameter.html)
[DNN 분류자](https://www.tensorflow.org/api_docs/python/tf/estimator/DNNClassifier)|[DNN 회귀 변수](https://www.tensorflow.org/api_docs/python/tf/estimator/DNNRegressor) | [DNN 회귀 변수](https://www.tensorflow.org/api_docs/python/tf/estimator/DNNRegressor)|
[DNN 선형 분류자](https://www.tensorflow.org/api_docs/python/tf/estimator/LinearClassifier)|[선형 회귀 변수](https://www.tensorflow.org/api_docs/python/tf/estimator/LinearRegressor)|[선형 회귀 변수](https://www.tensorflow.org/api_docs/python/tf/estimator/LinearRegressor)
[Naive Bayes](https://scikit-learn.org/stable/modules/naive_bayes.html#bernoulli-naive-bayes)|
[SGD(Stochastic Gradient Descent)](https://scikit-learn.org/stable/modules/sgd.html#sgd)|

생성자의 매개 변수를 사용 하 여 실험 유형을 지정 합니다. `task` `AutoMLConfig`

```python
from azureml.train.automl import AutoMLConfig

# task can be one of classification, regression, forecasting
automl_config = AutoMLConfig(task="classification")
```

## <a name="data-source-and-format"></a>데이터 원본 및 형식
자동화된 Machine Learning은 로컬 데스크톱 또는 Azure Blob Storage와 같은 클라우드의 데이터를 지원합니다. 데이터는 scikit-learn 지원 데이터 형식으로 읽을 수 있습니다. 데이터를
* Numpy 배열 X(기능) 및 y(대상 변수 또는 레이블이라고도 함)로 읽을 수 있습니다.
* pandas 데이터 프레임

>[!Important]
> 데이터 학습을 위한 요구 사항:
>* 데이터는 테이블 형식 이어야 합니다.
>* 예측 하려는 값 (대상 열)이 데이터에 있어야 합니다.

예를 들면 다음과 같습니다.

*   numpy 배열

    ```python
    digits = datasets.load_digits()
    X_digits = digits.data
    y_digits = digits.target
    ```

*   pandas 데이터 프레임

    ```python
    import pandas as pd
    from sklearn.model_selection import train_test_split
    df = pd.read_csv("https://automldemods.blob.core.windows.net/datasets/PlayaEvents2016,_1.6MB,_3.4k-rows.cleaned.2.tsv", delimiter="\t", quotechar='"')
    # get integer labels
    y = df["Label"]
    df = df.drop(["Label"], axis=1)
    df_train, _, y_train, _ = train_test_split(df, y, test_size=0.1, random_state=42)
    ```

## <a name="fetch-data-for-running-experiment-on-remote-compute"></a>원격 컴퓨팅에서 실험을 실행하기 위한 데이터 가져오기

원격 실행의 경우 원격 계산에서 데이터에 액세스할 수 있도록 해야 합니다. 데이터 저장소에 데이터를 업로드 하 여이 작업을 수행할 수 있습니다.

을 사용 하 `datastore`는 예제는 다음과 같습니다.

```python
    import pandas as pd
    from sklearn import datasets

    data_train = datasets.load_digits()

    pd.DataFrame(data_train.data[100:,:]).to_csv("data/X_train.csv", index=False)
    pd.DataFrame(data_train.target[100:]).to_csv("data/y_train.csv", index=False)

    ds = ws.get_default_datastore()
    ds.upload(src_dir='./data', target_path='digitsdata', overwrite=True, show_progress=True)
```

### <a name="define-dprep-references"></a>참조 정의

아래와 유사한 자동화 된 machine learning `AutoMLConfig` 개체로 전달 되는 d 및 y를 eprep 참조로 정의 합니다.

```python

    X = dprep.auto_read_file(path=ds.path('digitsdata/X_train.csv'))
    y = dprep.auto_read_file(path=ds.path('digitsdata/y_train.csv'))


    automl_config = AutoMLConfig(task = 'classification',
                                 debug_log = 'automl_errors.log',
                                 path = project_folder,
                                 run_configuration=conda_run_config,
                                 X = X,
                                 y = y,
                                 **automl_settings
                                )
```

## <a name="train-and-validation-data"></a>데이터 학습 및 유효성 검사

`AutoMLConfig` 메서드에서 직접 별도의 학습 및 유효성 검사 집합을 지정할 수 있습니다.

### <a name="k-folds-cross-validation"></a>K 접기 교차 유효성 검사

`n_cross_validations` 설정을 사용하여 교차 유효성 검사의 수를 지정합니다. 학습 데이터 집합은 무작위로 동일한 크기의 `n_cross_validations` 접기로 분할됩니다. 각 교차 유효성 검사 라운드 중에 접기 중 하나는 나머지 접기에 대해 학습된 모델의 유효성 검사에 사용됩니다. 이 프로세스는 각 접기가 유효성 검사 집합으로 한 번 사용될 때까지 `n_cross_validations` 라운드 동안 반복됩니다. 모든 `n_cross_validations` 라운드에 걸친 평균 점수가 보고되고 해당 모델이 전체 학습 데이터 세트에 대해 다시 학습됩니다.

### <a name="monte-carlo-cross-validation-repeated-random-sub-sampling"></a>Monte 몬테카를로 교차 유효성 검사 (반복 무작위 하위 샘플링)

`validation_size`를 사용하여 유효성 검사에 사용해야 하는 학습 데이터 세트의 비율을 지정하고, `n_cross_validations`를 사용하여 교차 유효성 검사의 수를 지정합니다. 각 교차 유효성 검사 라운드 중에 나머지 데이터에 대해 학습된 모델의 유효성 검사를 위해 `validation_size` 크기의 하위 집합이 무작위로 선택됩니다. 마지막으로, 모든 `n_cross_validations` 라운드에 걸친 평균 점수가 보고되고 해당 모델이 전체 학습 데이터 집합에 대해 다시 학습됩니다. Monte 몬테카를로는 시계열 예측에 대해 지원 되지 않습니다.

### <a name="custom-validation-dataset"></a>사용자 지정 유효성 검사 데이터 세트

임의 분할이 허용 되지 않는 경우 사용자 지정 유효성 검사 데이터 집합 사용 (일반적으로 시계열 데이터 또는 불균형 데이터) 사용자 고유의 유효성 검사 데이터 세트를 지정할 수 있습니다. 모델은 무작위 데이터 세트 대신 지정된 유효성 검사 데이터 세트에 대해 평가됩니다.

## <a name="compute-to-run-experiment"></a>실험 실행 컴퓨팅

다음으로, 모델을 학습할 위치를 결정합니다. 자동화된 Machine Learning 실험은 다음 컴퓨팅 옵션에서 실행할 수 있습니다.
*   로컬 데스크톱 또는 랩톱과 같은 로컬 머신 - 일반적으로 데이터 세트가 작고 아직 탐색 단계에 있는 경우입니다.
*   클라우드의 원격 머신 - [Azure Machine Learning Managed Compute](concept-compute-target.md#amlcompute)는 Azure Virtual Machines 클러스터에서 Machine Learning 모델을 학습할 수 있도록 하는 관리형 서비스입니다.

로컬 및 원격 컴퓨팅 대상이 있는 예제 노트에 대해서는 [GitHub 사이트](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning)를 참조하세요.

*   Azure 구독의 Azure Databricks 클러스터 여기에서 자세한 내용을 확인할 수 있습니다. [자동화 된 ML에 대 한 Azure Databricks 클러스터 설정](how-to-configure-environment.md#azure-databricks)

Azure Databricks 있는 노트북 예는 [GitHub 사이트](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/azure-databricks/automl) 를 참조 하세요.

<a name='configure-experiment'></a>

## <a name="configure-your-experiment-settings"></a>실험 설정 구성

자동화된 Machine Learning 실험을 구성하는 데 사용할 수 있는 옵션에 대해 알아봅니다. 이러한 매개 변수는 `AutoMLConfig` 개체를 인스턴스화하여 설정됩니다. 매개 변수의 전체 목록은 [AutoMLConfig 클래스](https://docs.microsoft.com/python/api/azureml-train-automl/azureml.train.automl.automlconfig?view=azure-ml-py)를 참조하세요.

일부 사례:

1.  반복당 최대 시간이 12,000초인 기본 메트릭으로 가중된 AUC를 사용하는 분류 실험이며, 이 실험은 50회 반복 및 2회 교차 유효성 검사 접기 후에 종료됩니다.

    ```python
    automl_classifier = AutoMLConfig(
        task='classification',
        primary_metric='AUC_weighted',
        max_time_sec=12000,
        iterations=50,
        blacklist_models='XGBoostClassifier',
        X=X,
        y=y,
        n_cross_validations=2)
    ```
2.  다음은 회귀 실험이 100회 반복된 후 종료되도록 설정한 예제이며, 각 반복이 5회 교차 유효성 검사 접기를 통해 최대 600초까지 지속됩니다.

    ```python
    automl_regressor = AutoMLConfig(
        task='regression',
        max_time_sec=600,
        iterations=100,
        whitelist_models='kNN regressor'
        primary_metric='r2_score',
        X=X,
        y=y,
        n_cross_validations=5)
    ```

세 가지 다른 `task` 매개 변수 값에 따라 적용할 알고리즘 목록이 결정 됩니다.  `whitelist` 또는 `blacklist` 매개 변수를 사용하여 사용 가능한 알고리즘 반복을 포함 또는 제외하도록 추가로 수정합니다. 지원 되는 모델 목록은 [Supportedalgorithms 클래스](https://docs.microsoft.com/python/api/azureml-train-automl/azureml.train.automl.constants.supportedalgorithms?view=azure-ml-py)에서 찾을 수 있습니다.

### <a name="primary-metric"></a>기본 메트릭
기본 메트릭 위의 예제에 나와 있는 것 처럼 최적화를 위해 모델 학습 중에 사용할 메트릭을 결정 합니다. 선택할 수 있는 기본 메트릭은 선택한 작업 유형에 따라 결정 됩니다. 다음은 사용 가능한 메트릭의 목록입니다.

[자동화 된 기계 학습 결과 이해](how-to-understand-automated-ml.md)에서 이러한 정의에 대해 알아봅니다.

|분류 | 회귀 | 시계열 예측
|-- |-- |--
|accuracy| spearman_correlation | spearman_correlation
|AUC_weighted | normalized_root_mean_squared_error | normalized_root_mean_squared_error
|average_precision_score_weighted | r2_score | r2_score
|norm_macro_recall | normalized_mean_absolute_error | normalized_mean_absolute_error
|precision_score_weighted |

### <a name="data-preprocessing--featurization"></a>데이터 전처리 & 기능화

자동화 된 모든 기계 학습 실험에서 데이터의 [크기를 자동으로 조정 하 고](concept-automated-ml.md#preprocess) 알고리즘을 통해 알고리즘을 효율적으로 수행할 수 있습니다.  그러나 누락 값 대체, 인코딩 및 변환과 같은 추가 전처리/기능화을 사용할 수도 있습니다. [기능화 포함 된 항목에 대해 자세히 알아보세요](how-to-create-portal-experiments.md#preprocess).

이 기능화를 사용 하도록 설정 `"preprocess": True` 하려면 [ `AutoMLConfig` 클래스](https://docs.microsoft.com/python/api/azureml-train-automl/azureml.train.automl.automlconfig?view=azure-ml-py)에 대해를 지정 합니다.

> [!NOTE]
> 자동화된 기계 학습 사전 처리 단계(기능 정규화, 누락된 데이터 처리, 텍스트를 숫자로 변환 등)는 기본 모델의 일부가 됩니다. 예측에 모델을 사용하는 경우 학습 중에 적용되는 동일한 전처리 단계가 입력 데이터에 자동으로 적용됩니다.

### <a name="time-series-forecasting"></a>시계열 예측
시계열 예측 작업 유형에는 정의할 추가 매개 변수가 있습니다.
1. time_column_name-날짜/시간 계열을 포함 하는 학습 데이터의 열 이름을 정의 하는 필수 매개 변수입니다.
1. max_horizon-학습 데이터의 주기를 기준으로 예측 하려는 시간을 정의 합니다. 예를 들어 일일 시간 조직 학습 데이터가 있는 경우 모델을 학습 하는 데 사용할 일 수를 정의 합니다.
1. grain_column_names-학습 데이터의 개별 시계열 데이터를 포함 하는 열의 이름을 정의 합니다. 예를 들어 매장에서 특정 브랜드의 판매를 예측 하는 경우 매장 및 브랜드 열을 그레인 열로 정의 합니다.

아래에서 사용 되는 이러한 설정의 예를 참조 하세요. 노트북 예는 [여기](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-orange-juice-sales/auto-ml-forecasting-orange-juice-sales.ipynb)에서 제공 됩니다.

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

automl_config = AutoMLConfig(task='forecasting',
                             debug_log='automl_oj_sales_errors.log',
                             primary_metric='normalized_root_mean_squared_error',
                             iterations=10,
                             X=X_train,
                             y=y_train,
                             n_cross_validations=5,
                             path=project_folder,
                             verbosity=logging.INFO,
                             **time_series_settings)
```

### <a name="ensemble"></a>앙상블 구성

앙상블 모델은 기본적으로 사용 하도록 설정 되어 있으며 자동화 된 machine learning 실행에서 최종 실행 반복으로 나타납니다. 현재 지원 되는 앙상블 메서드는 투표 및 누적입니다. 응답은가 중 평균을 사용 하 여 소프트 응답으로 구현 되 고, 스택 구현은 두 계층 구현을 사용 합니다. 여기서 첫 번째 계층은 투표 앙상블와 동일한 모델을 사용 하 고 두 번째 계층 모델은 두 계층 모델을 사용 하 여 첫 번째 계층의 모델입니다. ONNX 모델을 사용 **하거나** explainability를 사용 하는 경우에는 누적이 사용 하지 않도록 설정 되며 투표만 사용 됩니다.

기본 stack 앙상블 동작을 변경 하기 위해 `kwargs` `AutoMLConfig` 개체에서로 제공할 수 있는 여러 기본 인수가 있습니다.

* `stack_meta_learner_type`: 학습자은 개별 다른 유형의 모델의 출력에 대해 학습 된 모델입니다. 기본 메타 학습자는 분류 `LogisticRegression` 작업 `LogisticRegressionCV` (교차 유효성 검사를 사용 하도록 설정 된 경우) 및 `ElasticNet` 회귀/예측 작업 ( `ElasticNetCV` 교차 유효성 검사를 사용 하도록 설정한 경우)에 대 한 것입니다. `LogisticRegression`이 매개 변수는 `LogisticRegressionCV` ,`LinearRegression`, `LightGBMClassifier`, `ElasticNet` ,,`ElasticNetCV`또는 문자열 중 하나일 수 있습니다. `LightGBMRegressor`
* `stack_meta_learner_train_percentage`: 학습자를 학습 하기 위해 예약할 학습 및 유효성 검사 유형을 선택할 때 학습 집합의 비율을 지정 합니다. 기본값은 `0.2`여야 합니다.
* `stack_meta_learner_kwargs`: meta 학습자의 이니셜라이저에 전달할 선택적 매개 변수입니다. 이러한 매개 변수 및 매개 변수 형식은 해당 모델 생성자에서 이러한 매개 변수를 미러링 하 고 모델 생성자에 전달 됩니다.

다음 코드에서는 `AutoMLConfig` 개체에서 사용자 지정 앙상블 동작을 지정 하는 예를 보여 줍니다.

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
        iterations=20,
        X=X_train,
        y=y_train,
        n_cross_validations=5,
        **ensemble_settings
        )
```

앙상블 교육은 기본적으로 사용 하도록 설정 되어 있지만 및 `enable_voting_ensemble` `enable_stack_ensemble` 부울 매개 변수를 사용 하 여 사용 하지 않도록 설정할 수 있습니다.

```python
automl_classifier = AutoMLConfig(
        task='classification',
        primary_metric='AUC_weighted',
        iterations=20,
        X=X_train,
        y=y_train,
        n_cross_validations=5,
        enable_voting_ensemble=False,
        enable_stack_ensemble=False
        )
```

## <a name="run-experiment"></a>실험 실행

자동화 된 ML의 경우 실험 `Experiment` 을 실행 하는 `Workspace` 데 사용 되는의 명명 된 개체인 개체를 만듭니다.

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

### <a name="exit-criteria"></a>종료 조건
실험을 완료 하기 위해 정의할 수 있는 몇 가지 옵션이 있습니다.
1. 조건 없음-종료 매개 변수를 정의 하지 않으면 기본 메트릭에 대 한 추가 진행률이 표시 되지 않을 때까지 실험을 계속 합니다.
1. 반복 횟수-실험에서 실행할 반복 횟수를 정의 합니다. 선택적으로 iteration_timeout_minutes를 추가 하 여 각 반복 당 시간 제한 (분)을 정의할 수 있습니다.
1. 시간이 지난 후 종료-설정에서 experiment_timeout_minutes를 사용 하 여 실험 실행을 계속할 시간 (분)을 정의할 수 있습니다.
1. 점수를 받은 후 종료-experiment_exit_score를 사용 하 여 기본 메트릭에 기반 하는 점수에 도달한 후 실험을 완료 하도록 선택할 수 있습니다.

### <a name="explore-model-metrics"></a>모델 메트릭 탐색

노트에서 학습 결과를 보거나 인라인으로 볼 수 있습니다. 자세한 내용은 [모델 추적 및 평가](how-to-track-experiments.md#view-run-details)를 참조하세요.

## <a name="understand-automated-ml-models"></a>자동화 된 ML 모델 이해

자동화 된 ML을 사용 하 여 생성 된 모든 모델에는 다음 단계가 포함 됩니다.
+ 자동화 된 기능 엔지니어링 (전처리 = True 인 경우)
+ Hypermeter 값을 사용 하 여 크기 조정/정규화 및 알고리즘

자동 ML의 fitted_model 출력에서이 정보를 가져오도록 투명 하 게 만듭니다.

```python
automl_config = AutoMLConfig(…)
automl_run = experiment.submit(automl_config …)
best_run, fitted_model = automl_run.get_output()
```

### <a name="automated-feature-engineering"></a>자동화 된 기능 엔지니어링

전처리 = True 일 때 발생 하는 전처리 및 [자동화 된 기능 엔지니어링](concept-automated-ml.md#preprocess) 목록을 참조 하세요.

다음 예제를 고려해 보세요.
+ 다음과 같은 4 가지 입력 기능이 있습니다. (숫자), B (숫자), C (숫자), D (DateTime)
+ 숫자 기능 C는 모든 고유 값을 포함 하는 ID 열 이므로 삭제 됩니다.
+ 숫자 기능 A와 B에는 누락 된 값이 있으므로 mean 귀속 됩니다.
+ DateTime 기능 D는 11 가지 다른 엔지니어링 된 기능에 기능화 됩니다.

적합 한 모델의 첫 번째 단계에서이 2 개의 Api를 사용 하 여 더 자세히 이해 합니다.  [이 샘플 노트북](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning/forecasting-energy-demand)을 참조 하세요.

+ API 1: `get_engineered_feature_names()` 엔지니어링 된 기능 이름의 목록을 반환 합니다.

  Usage:
  ```python
  fitted_model.named_steps['timeseriestransformer']. get_engineered_feature_names ()
  ```

  ```
  Output: ['A', 'B', 'A_WASNULL', 'B_WASNULL', 'year', 'half', 'quarter', 'month', 'day', 'hour', 'am_pm', 'hour12', 'wday', 'qday', 'week']
  ```

  이 목록에는 모든 엔지니어링 된 기능 이름이 포함 됩니다.

  >[!Note]
  >작업 = ' 예측 '에 ' timeseriestransformer '를 사용 하 고, ' 회귀 ' 또는 ' 분류 ' 작업에 ' datatransformer '를 사용 합니다.

+ API 2: `get_featurization_summary()` 모든 입력 기능에 대 한 기능화 요약을 반환 합니다.

  Usage:
  ```python
  fitted_model.named_steps['timeseriestransformer'].get_featurization_summary()
  ```

  >[!Note]
  >작업 = ' 예측 '에 ' timeseriestransformer '를 사용 하 고, ' 회귀 ' 또는 ' 분류 ' 작업에 ' datatransformer '를 사용 합니다.

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

   각 항목이 나타내는 의미는 다음과 같습니다.

   |출력|정의|
   |----|--------|
   |RawFeatureName|제공 된 데이터 집합의 입력 기능/열 이름입니다.|
   |TypeDetected|입력 기능의 데이터 형식이 검색 되었습니다.|
   |놓도록|입력 기능을 삭제 하거나 사용 했는지 여부를 나타냅니다.|
   |EngineeringFeatureCount|자동화 된 기능 엔지니어링 변환을 통해 생성 된 기능의 수입니다.|
   |변환|엔지니어링 된 기능을 생성 하기 위해 입력 기능에 적용 되는 변환 목록입니다.|

### <a name="scalingnormalization-and-algorithm-with-hypermeter-values"></a>Hypermeter 값을 사용 하는 크기 조정/정규화 및 알고리즘:

파이프라인에 대 한 크기 조정/정규화 및 알고리즘/하이퍼 매개 변수 값을 이해 하려면 fitted_model를 사용 합니다. [크기 조정/정규화에 대해 자세히 알아보세요](concept-automated-ml.md#preprocess). 샘플 출력은 다음과 같습니다.

```
[('RobustScaler', RobustScaler(copy=True, quantile_range=[10, 90], with_centering=True, with_scaling=True)), ('LogisticRegression', LogisticRegression(C=0.18420699693267145, class_weight='balanced', dual=False, fit_intercept=True, intercept_scaling=1, max_iter=100, multi_class='multinomial', n_jobs=1, penalty='l2', random_state=None, solver='newton-cg', tol=0.0001, verbose=0, warm_start=False))
```

자세한 내용을 보려면 [이 샘플 노트북](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification/auto-ml-classification.ipynb)에 표시 된이 도우미 함수를 사용 합니다.

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

다음은 특정 알고리즘을 사용 하는 파이프라인에 대 한 샘플 출력입니다 (이 경우 RobustScalar와 함께 LogisticRegression).

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

<a name="explain"></a>

## <a name="explain-the-model-interpretability"></a>모델 설명 (interpretability)

자동화된 Machine Learning을 사용하면 기능 중요도를 이해할 수 있습니다.  학습 프로세스 동안 모델의 전역 기능 중요도를 얻을 수 있습니다.  분류 시나리오의 경우 클래스 수준 기능 중요도를 얻을 수도 있습니다.  기능 중요도를 얻으려면 유효성 검증 데이터 세트(X_valid)를 제공해야 합니다.

기능 중요도를 생성하는 방법에는 다음 두 가지가 있습니다.

*   실험이 완료되면 모든 반복에 대해 `explain_model` 메서드를 사용할 수 있습니다.

    ```python
    from azureml.train.automl.automlexplainer import explain_model

    shap_values, expected_values, overall_summary, overall_imp, per_class_summary, per_class_imp = \
        explain_model(fitted_model, X_train, X_test)

    #Overall feature importance
    print(overall_imp)
    print(overall_summary)

    #Class-level feature importance
    print(per_class_imp)
    print(per_class_summary)
    ```

*   모든 반복의 기능 중요도를 보려면 AutoMLConfig에서 `model_explainability` 플래그를 `True`로 설정하세요.

    ```python
    automl_config = AutoMLConfig(task = 'classification',
                                 debug_log = 'automl_errors.log',
                                 primary_metric = 'AUC_weighted',
                                 max_time_sec = 12000,
                                 iterations = 10,
                                 verbosity = logging.INFO,
                                 X = X_train,
                                 y = y_train,
                                 X_valid = X_test,
                                 y_valid = y_test,
                                 model_explainability=True,
                                 path=project_folder)
    ```

    완료되면 retrieve_model_explanation 메서드를 사용하여 특정 반복의 기능 중요도를 검색할 수 있습니다.

    ```python
    from azureml.train.automl.automlexplainer import retrieve_model_explanation

    shap_values, expected_values, overall_summary, overall_imp, per_class_summary, per_class_imp = \
        retrieve_model_explanation(best_run)

    #Overall feature importance
    print(overall_imp)
    print(overall_summary)

    #Class-level feature importance
    print(per_class_imp)
    print(per_class_summary)
    ```

실행 개체를 사용 하 여 기능 중요도를 보려면 URL을 표시 합니다.

```
automl_run.get_portal_url()
```

작업 영역에서 Azure Portal 또는 [작업 영역 방문 페이지 (미리 보기)](https://ml.azure.com)에서 기능 중요도 차트를 시각화할 수 있습니다. 노트북에서 [jupyter 위젯을](https://docs.microsoft.com/python/api/azureml-widgets/azureml.widgets?view=azure-ml-py) 사용 `RunDetails` 하는 경우에도 차트가 표시 됩니다. 차트에 대 한 자세한 내용은 자동화 된 [machine learning 결과 이해](how-to-understand-automated-ml.md)를 참조 하세요.

```Python
from azureml.widgets import RunDetails
RunDetails(automl_run).show()
```

![기능 중요도 그래프](./media/how-to-configure-auto-train/feature-importance.png)

자동화 된 machine learning 외부에서 SDK의 다른 영역에 모델 설명과 기능 중요도를 사용 하도록 설정 하는 방법에 대 한 자세한 내용은 interpretability의 [개념](machine-learning-interpretability-explainability.md) 문서를 참조 하세요.

## <a name="next-steps"></a>다음 단계

[모델 배포 방법 및 위치](how-to-deploy-and-where.md)에 대해 자세히 알아봅니다.

[자동화 된 machine learning을 사용 하 여 회귀 모델을 학습](tutorial-auto-train-models.md) 하는 방법 또는 [원격 리소스에서 자동화 된 machine learning을 사용 하 여 학습](how-to-auto-train-remote.md)하는 방법에 대해 자세히 알아보세요.
