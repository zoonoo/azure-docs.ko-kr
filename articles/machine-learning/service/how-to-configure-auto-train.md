---
title: 자동화된 ML 실험 만들기
titleSuffix: Azure Machine Learning service
description: 자동화된 기계 학습은 사용자를 위한 알고리즘을 선택하고 배포할 준비가 된 모델을 생성합니다. 자동화된 기계 학습 실험을 구성하는 데 사용할 수 있는 옵션에 대해 알아봅니다.
author: nacharya1
ms.author: nilesha
ms.reviewer: sgilley
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 05/02/2019
ms.custom: seodec18
ms.openlocfilehash: df05bd984667283b0ccc143ba14fff6b35d69144
ms.sourcegitcommit: 45e4466eac6cfd6a30da9facd8fe6afba64f6f50
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/07/2019
ms.locfileid: "66753168"
---
# <a name="configure-automated-ml-experiments-in-python"></a>자동화 된 기계 학습 실험에서 Python 구성

이 가이드에서는 자동화 된 machine learning 사용 하 여 실험의 다양 한 구성 설정을 정의 하는 방법에 알아봅니다 합니다 [Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)합니다. 자동화된 Machine Learning은 사용자를 위한 알고리즘과 하이퍼 매개 변수를 선택하고 배포할 준비가 된 모델을 생성합니다. 자동화된 Machine Learning 실험을 구성하는 데 사용할 수 있는 옵션에 대해 알아봅니다.

자동화된 Machine Learning 실험 예제를 보려면 [자습서: 자동화된 Machine Learning을 사용하여 분류 모델 학습](tutorial-auto-train-models.md) 또는 [클라우드의 자동화된 Machine Learning을 사용하여 모델 학습](how-to-auto-train-remote.md)을 참조하세요.

자동화된 기계 학습에서 사용할 수 있는 구성 옵션은 다음과 같습니다.

* 실험 유형 선택: 분류, 회귀, 시계열 예측
* 데이터 원본, 형식 및 데이터 가져오기
* 컴퓨팅 대상 선택: 로컬 또는 원격
* 자동화된 Machine Learning 실험 설정
* 자동화된 Machine Learning 실험 실행
* 모델 메트릭 탐색
* 모델 등록 및 배포

없는 코드 환경을 원한다 면 수도 있습니다 [자동화 된 기계 학습 실험을 Azure portal에서 만들](how-to-create-portal-experiments.md)합니다.

## <a name="select-your-experiment-type"></a>실험 유형 선택

실험을 시작하기 전에 해결하려는 기계 학습 문제의 종류를 결정해야 합니다. 자동화된 Machine Learning은 작업 유형으로 분류, 회귀 및 예측을 지원합니다.

자동화된 Machine Learning은 자동화 및 튜닝 프로세스 중에 다음 알고리즘을 지원합니다. 사용자는 알고리즘을 지정할 필요가 없습니다. DNN 알고리즘은 학습 중 사용 가능한 이지만 자동화 된 ML DNN 모델을 작성 하지 않습니다.

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
[선형 분류자 DNN](https://www.tensorflow.org/api_docs/python/tf/estimator/LinearClassifier)|[선형 회귀 변수](https://www.tensorflow.org/api_docs/python/tf/estimator/LinearRegressor)|[선형 회귀 변수](https://www.tensorflow.org/api_docs/python/tf/estimator/LinearRegressor)
[Naive Bayes](https://scikit-learn.org/stable/modules/naive_bayes.html#bernoulli-naive-bayes)|
[SGD(Stochastic Gradient Descent)](https://scikit-learn.org/stable/modules/sgd.html#sgd)|

사용 하 여는 `task` 의 매개 변수는 `AutoMLConfig` 실험 형식을 지정 하는 생성자입니다.

```python
from azureml.train.automl import AutoMLConfig

# task can be one of classification, regression, forecasting
automl_config = AutoMLConfig(task="classification")
```

## <a name="data-source-and-format"></a>데이터 원본 및 형식
자동화된 Machine Learning은 로컬 데스크톱 또는 Azure Blob Storage와 같은 클라우드의 데이터를 지원합니다. 데이터는 scikit-learn 지원 데이터 형식으로 읽을 수 있습니다. 데이터를
* Numpy 배열 X(기능) 및 y(대상 변수 또는 레이블이라고도 함)로 읽을 수 있습니다.
* pandas 데이터 프레임

예제:

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

## <a name="fetch-data-for-running-experiment-on-remote-compute"></a>원격 계산에서 실험을 실행하기 위한 데이터 가져오기

원격 계산을 사용하여 실험을 실행하는 경우 데이터 가져오기는 별도의 `get_data()` Python 스크립트로 래핑되어야 합니다. 이 스크립트는 자동화된 Machine Learning 실험이 실행되는 원격 계산에서 실행됩니다. `get_data`는 각 반복마다 연결을 통해 데이터를 가져올 필요가 없습니다. `get_data`가 없으면 원격 계산에서 실행할 때 실험이 실패합니다.

`get_data`의 예제는 다음과 같습니다.

```python
%%writefile $project_folder/get_data.py
import pandas as pd
from sklearn.model_selection import train_test_split
from sklearn.preprocessing import LabelEncoder
def get_data(): # Burning man 2016 data
    df = pd.read_csv("https://automldemods.blob.core.windows.net/datasets/PlayaEvents2016,_1.6MB,_3.4k-rows.cleaned.2.tsv", delimiter="\t", quotechar='"')
    # get integer labels
    le = LabelEncoder()
    le.fit(df["Label"].values)
    y = le.transform(df["Label"].values)
    df = df.drop(["Label"], axis=1)
    df_train, _, y_train, _ = train_test_split(df, y, test_size=0.1, random_state=42)
    return { "X" : df, "y" : y }
```

`AutoMLConfig` 개체에서 `data_script` 매개 변수를 지정하고 아래와 비슷한 `get_data` 스크립트 파일에 대한 경로를 제공합니다.

```python
automl_config = AutoMLConfig(****, data_script=project_folder + "/get_data.py", **** )
```

`get_data` 스크립트는 다음을 반환할 수 있습니다.

키 | Type | 상호 배타적 관계    | 설명
---|---|---|---
X | pandas 데이터 프레임 또는 numpy 배열 | data_train, label, columns |  학습할 모든 기능입니다.
y | pandas 데이터 프레임 또는 numpy 배열 |   label   | 학습할 데이터에 레이블을 지정합니다. 분류의 경우 정수 배열이어야 합니다.
X_valid | pandas 데이터 프레임 또는 numpy 배열   | data_train, label | _선택적_ 데이터 유효성 검사 집합을 형성 하는 기능입니다. 지정하지 않으면 X가 학습과 유효성 검사 간에 분할됩니다.
y_valid |   pandas 데이터 프레임 또는 numpy 배열 | data_train, label | _선택 사항_ 유효성을 검사할 레이블 데이터입니다. 지정하지 않으면 y가 학습과 유효성 검사 간에 분할됩니다.
sample_weight | pandas 데이터 프레임 또는 numpy 배열 |   data_train, label, columns| _선택 사항_ 각 샘플에 대한 가중치입니다. 데이터 요소에 대해 서로 다른 가중치를 할당하려는 경우에 사용합니다.
sample_weight_valid | pandas 데이터 프레임 또는 numpy 배열 | data_train, label, columns |    _선택 사항_ 각 유효성 검사 샘플에 대한 가중치입니다. 지정하지 않으면 sample_weight가 학습과 유효성 검사 간에 분할됩니다.
data_train |    pandas 데이터 프레임 |  X, y, X_valid, y_valid |    학습할 모든 데이터(기능 + 레이블)입니다.
label | 문자열  | X, y, X_valid, y_valid |  레이블을 나타내는 data_train의 열입니다.
열 | 문자열 배열  ||  _선택 사항_ 기능에 사용할 열의 허용 목록입니다.
cv_splits_indices   | 정수 배열 ||  _선택 사항_ 교차 유효성 검사를 위해 데이터를 분할할 인덱스 목록입니다.

## <a name="train-and-validation-data"></a>데이터 학습 및 유효성 검사

get_data()를 통하거나 `AutoMLConfig` 메서드에서 직접 별도의 학습 및 유효성 검사 집합을 지정할 수 있습니다.

### <a name="k-folds-cross-validation"></a>K 접기 교차 유효성 검사

`n_cross_validations` 설정을 사용하여 교차 유효성 검사의 수를 지정합니다. 학습 데이터 집합은 무작위로 동일한 크기의 `n_cross_validations` 접기로 분할됩니다. 각 교차 유효성 검사 라운드 중에 접기 중 하나는 나머지 접기에 대해 학습된 모델의 유효성 검사에 사용됩니다. 이 프로세스는 각 접기가 유효성 검사 집합으로 한 번 사용될 때까지 `n_cross_validations` 라운드 동안 반복됩니다. 모든 `n_cross_validations` 라운드에 걸친 평균 점수가 보고되고 해당 모델이 전체 학습 데이터 세트에 대해 다시 학습됩니다.

### <a name="monte-carlo-cross-validation-repeated-random-sub-sampling"></a>몬테카를로 Carlo 교차 유효성 검사 (반복된 하위 무작위 샘플링)

`validation_size`를 사용하여 유효성 검사에 사용해야 하는 학습 데이터 세트의 비율을 지정하고, `n_cross_validations`를 사용하여 교차 유효성 검사의 수를 지정합니다. 각 교차 유효성 검사 라운드 중에 나머지 데이터에 대해 학습된 모델의 유효성 검사를 위해 `validation_size` 크기의 하위 집합이 무작위로 선택됩니다. 마지막으로, 모든 `n_cross_validations` 라운드에 걸친 평균 점수가 보고되고 해당 모델이 전체 학습 데이터 집합에 대해 다시 학습됩니다. 몬테카를로 시계열 예측에 대 한 지원 되지 않습니다.

### <a name="custom-validation-dataset"></a>사용자 지정 유효성 검사 데이터 세트

임의 분할에 적합 하지 않은 경우 사용자 지정 유효성 검사 데이터 집합, 일반적으로 시계열 데이터 또는 불균형 데이터를 사용 합니다. 사용자 고유의 유효성 검사 데이터 세트를 지정할 수 있습니다. 모델은 무작위 데이터 세트 대신 지정된 유효성 검사 데이터 세트에 대해 평가됩니다.

## <a name="compute-to-run-experiment"></a>실험 실행 계산

다음으로, 모델을 학습할 위치를 결정합니다. 자동화된 Machine Learning 실험은 다음 컴퓨팅 옵션에서 실행할 수 있습니다.
*   로컬 데스크톱 또는 랩톱과 같은 로컬 머신 - 일반적으로 데이터 세트가 작고 아직 탐색 단계에 있는 경우입니다.
*   클라우드의 원격 머신 - [Azure Machine Learning Managed Compute](concept-compute-target.md#amlcompute)는 Azure Virtual Machines 클러스터에서 Machine Learning 모델을 학습할 수 있도록 하는 관리형 서비스입니다.

로컬 및 원격 컴퓨팅 대상이 있는 예제 노트에 대해서는 [GitHub 사이트](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning)를 참조하세요.

*   Azure 구독에는 Azure Databricks 클러스터입니다. 자세한 내용은 다음 참조를 찾을 수 있습니다 [자동화 된 기계 학습에 대 한 설치 Azure Databricks 클러스터](how-to-configure-environment.md#azure-databricks)

참조 된 [GitHub 사이트](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/azure-databricks/automl) Azure Databricks에 노트북 예를 들어 있습니다.

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

세 가지 `task` 매개 변수 값에 적용할 알고리즘의 목록을 확인 합니다.  `whitelist` 또는 `blacklist` 매개 변수를 사용하여 사용 가능한 알고리즘 반복을 포함 또는 제외하도록 추가로 수정합니다. 지원 되는 모델 목록에서 찾을 수 있습니다 [SupportedAlgorithms 클래스](https://docs.microsoft.com/python/api/azureml-train-automl/azureml.train.automl.constants.supportedalgorithms?view=azure-ml-py)합니다.

### <a name="primary-metric"></a>기본 메트릭
기본 메트릭; 와 같이 위의 예에서는 최적화에 대 한 모델을 학습 하는 동안 사용할 메트릭을 결정 합니다. 기본 메트릭은 선택할 수 있습니다 선택 하면 작업 형식에 의해 결정 됩니다. 사용 가능한 메트릭 목록은 다음과 같습니다.

|분류 | 회귀 | 시계열 예측
|-- |-- |--
|accuracy| spearman_correlation | spearman_correlation
|AUC_weighted | normalized_root_mean_squared_error | normalized_root_mean_squared_error
|average_precision_score_weighted | r2_score | r2_score
|norm_macro_recall | normalized_mean_absolute_error | normalized_mean_absolute_error
|precision_score_weighted |

### <a name="data-preprocessing--featurization"></a>데이터 전처리 및 기능화 (featurization)

데이터의 모든 자동화 된 기계 학습 실험 [자동으로 크기를 조정 하 고 정규화](concept-automated-ml.md#preprocess) 알고리즘 잘 수행 하는 데 있습니다.  그러나 추가 전처리/기능화 (featurization), 누락 값 대체, 인코딩 및 변환 등을 사용할 수 있습니다. [어떤 기능화 (featurization) 포함에 대 한 자세한](how-to-create-portal-experiments.md#preprocess)합니다.

이 기능을 사용 하도록 설정 하려면 지정 `"preprocess": True` 에 대 한 합니다 [ `AutoMLConfig` 클래스](https://docs.microsoft.com/python/api/azureml-train-automl/azureml.train.automl.automlconfig?view=azure-ml-py)합니다.

### <a name="time-series-forecasting"></a>시계열 예측
시간 시계열 예측 작업 형식에 대 한 추가 매개 변수를 정의 해야 합니다.
1. time_column_name-학습 데이터가 포함 된 날짜/시간 계열의 열 이름을 정의 하는 필수 매개 변수입니다.
1. max_horizon-학습 데이터의 주기 성과에 따라를 예측 하려는 시간의 길이 정의 합니다. 예를 들어 매일 시간 조직을 사용 하 여 학습 데이터에 있으면 얼마나 멀리 확장에서 정의한 모델에 대 한 학습을 원하는 날짜입니다.
1. grain_column_names-개별 시계열 데이터를 학습 데이터를 포함 하는 열 이름을 정의 합니다. 예를 들어 저장소에서 특정 브랜드의 판매를 예측 하는 경우 store와 brand 열 수준이 열으로 정의할는 있습니다.

이러한 예제를 보려면 아래에서 사용 중인 설정을 notebook 예제를 사용할 수 [여기](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-orange-juice-sales/auto-ml-forecasting-orange-juice-sales.ipynb)합니다.

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

## <a name="run-experiment"></a>실험 실행

실행하려는 실험을 제출하고 모델을 생성합니다. `AutoMLConfig`를 `submit` 메서드에 전달하여 모델을 생성합니다.

```python
run = experiment.submit(automl_config, show_output=True)
```

>[!NOTE]
>새 머신에 먼저 종속성이 설치됩니다.  출력이 표시되는 데 최대 10분이 걸릴 수 있습니다.
>`show_output`을 `True`로 설정하면 출력이 콘솔에 표시됩니다.

### <a name="exit-criteria"></a>종료 기준
있는 몇 가지 옵션을 정의할 수 있습니다 실험을 완료 합니다.
1. 조건 없는-정의 하지 않는 경우 기본 메트릭이에서 더 이상 진행이 만들어질 때까지 계속 실험 하는 매개 변수를 종료 합니다.
1. 수의 반복-실행 하는 실험에 대 한 반복 횟수를 정의 합니다. 선택적 있습니다 iteration_timeout_minutes 각 반복 당 분의 시간 제한을 정의를 추가 합니다.
1. 시간-실험 하 여 계속 실행 기간 (분) 정의할 수 있습니다 설정에서 사용 하 여 experiment_timeout_minutes 후 종료 합니다.
1. Experiment_exit_score 기본 메트릭을 기준으로 점수에 도달한 후 실험을 완료 하도록 선택할 수 있습니다를 사용 하 여 점수-도달한 후 종료 합니다.

### <a name="explore-model-metrics"></a>모델 메트릭 탐색

Notebook을 사용 중인 경우 위젯 또는 인라인 학습 결과 볼 수 있습니다. 자세한 내용은 [모델 추적 및 평가](how-to-track-experiments.md#view-run-details)를 참조하세요.

## <a name="understand-automated-ml-models"></a>자동화 된 ML 모델 이해

자동화 된 기계 학습을 사용 하 여 생성 하는 모든 모델에는 다음 단계가 포함 됩니다.
+ 기능 엔지니어링 자동화 (경우 전처리 = True)
+ 크기 조정/정규화 및 hypermeter 값을 사용 하 여 알고리즘

에서는 투명 하 게 자동화 된 기계 학습에서 fitted_model 출력에서이 정보를 가져오려고 합니다.

```python
automl_config = AutoMLConfig(…)
automl_run = experiment.submit(automl_config …)
best_run, fitted_model = automl_run.get_output()
```

### <a name="automated-feature-engineering"></a>자동화 된 기능 엔지니어링

전처리의 목록을 보려면 및 [자동화 기능 엔지니어링](concept-automated-ml.md#preprocess) 이런 경우 전처리 = True입니다.

다음 예를 살펴보세요.
+ 입력된 기능 4 가지가 있습니다. (숫자)는 B (숫자) (숫자) C, D (DateTime)
+ 모든 고유 값을 사용 하 여 ID 열 이므로 숫자 기능 C를 삭제할
+ 숫자 기능 A와 B 값이 누락 및 따라서 평균으로 귀속 됩니다.
+ 날짜/시간 D 기능은 다른 엔지니어링 된 기능에 들어가지 않고 기능화

이러한 2를 사용 하 여 자세하게 파악 하려면 Api에 맞춤된 모델의 첫 번째 단계입니다.  참조 [샘플 notebook이](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning/forecasting-energy-demand)합니다.

+ API 1: `get_engineered_feature_names()` 엔지니어링 된 기능 이름 목록을 반환 합니다.

  Usage:
  ```python
  fitted_model.named_steps['timeseriestransformer']. get_engineered_feature_names ()
  ```

  ```
  Output: ['A', 'B', 'A_WASNULL', 'B_WASNULL', 'year', 'half', 'quarter', 'month', 'day', 'hour', 'am_pm', 'hour12', 'wday', 'qday', 'week']
  ```

  이 목록은 모든 엔지니어링 된 기능 이름입니다.

  >[!Note]
  >작업에 대 한 'timeseriestransformer' 사용 'datatransformer' '회귀' 또는 '분류' 작업에 대 한 '예측', 사용 =.

+ API 2: `get_featurization_summary()` 기능화 (featurization) 모든 입력된 기능에 대 한 요약을 반환 합니다.

  Usage:
  ```python
  fitted_model.named_steps['timeseriestransformer'].get_featurization_summary()
  ```

  >[!Note]
  >작업에 대 한 'timeseriestransformer' 사용 'datatransformer' '회귀' 또는 '분류' 작업에 대 한 '예측', 사용 =.

  출력
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
   |RawFeatureName|제공한 데이터 집합의 입력된 기능/열 이름입니다.|
   |TypeDetected|입력 기능 검색 된 데이터 형식입니다.|
   |삭제|입력된 기능 삭제 되었거나 사용 되는 경우를 나타냅니다.|
   |EngineeringFeatureCount|자동화 된 기능 엔지니어링 변환을 통해 생성 된 기능 수입니다.|
   |변환|엔지니어링 된 기능을 생성 하는 기능 입력에 적용 되는 변환의 목록입니다.|

### <a name="scalingnormalization-and-algorithm-with-hypermeter-values"></a>크기 조정/정규화 및 hypermeter 값을 사용 하 여 알고리즘:

파이프라인의 정규화 크기 조정 및 알고리즘/하이퍼 매개 변수 값을 이해 하려면 fitted_model.steps를 사용 합니다. [정규화 크기 조정에 대해 자세히 알아보려면](concept-automated-ml.md#preprocess)합니다. 샘플 출력은 다음과 같습니다.

```
[('RobustScaler', RobustScaler(copy=True, quantile_range=[10, 90], with_centering=True, with_scaling=True)), ('LogisticRegression', LogisticRegression(C=0.18420699693267145, class_weight='balanced', dual=False, fit_intercept=True, intercept_scaling=1, max_iter=100, multi_class='multinomial', n_jobs=1, penalty='l2', random_state=None, solver='newton-cg', tol=0.0001, verbose=0, warm_start=False))
```

자세한 세부 정보를 가져오려면 에서처럼이 도우미 함수를 사용 하 여 [샘플 notebook이](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification/auto-ml-classification.ipynb)합니다.

```python
from pprint import pprint
def print_model(model, prefix=""):
    for step in model.steps:
        print(prefix + step[0])
        if hasattr(step[1], 'estimators') and hasattr(step[1], 'weights'):
            pprint({'estimators': list(e[0] for e in step[1].estimators), 'weights': step[1].weights})
            print()
            for estimator in step[1].estimators:
                print_model(estimator[1], estimator[0]+ ' - ')
        else:
            pprint(step[1].get_params())
            print()

print_model(fitted_model)
```

특정 알고리즘 (이 예제의 RobustScalar 사용 하 여 LogisticRegression)를 사용 하 여 파이프라인에 대 한 샘플 출력은 다음과 같습니다.

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

## <a name="explain-the-model-interpretability"></a>(Interpretability) 모델을 설명

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

Azure Portal의 작업 영역에서 기능 중요도 차트를 시각화할 수 있습니다. 이 차트는 노트에서 Jupyter 위젯을 사용할 때도 표시됩니다. 차트에 대한 자세한 내용은 [샘플 Azure Machine Learning Service Notebook 문서](samples-notebooks.md)를 참조하세요.

```Python
from azureml.widgets import RunDetails
RunDetails(local_run).show()
```
![기능 중요도 그래프](./media/how-to-configure-auto-train/feature-importance.png)

모델 설명 및 기능 중요도 외부 자동화 된 기계 학습에서 SDK의 다른 영역에서 사용할 수 있습니다 하는 방법에 대 한 자세한 내용은 참조는 [개념](machine-learning-interpretability-explainability.md) interpretability 문서.

## <a name="next-steps"></a>다음 단계

[모델 배포 방법 및 위치](how-to-deploy-and-where.md)에 대해 자세히 알아봅니다.

에 대해 자세히 알아보세요 [자동화 된 machine learning 사용 하 여 회귀 모델을 학습 하는 방법을](tutorial-auto-train-models.md) 하거나 [사용 하 여 학습 하는 방법에는 machine learning 원격 리소스에 자동화 된](how-to-auto-train-remote.md)합니다.
