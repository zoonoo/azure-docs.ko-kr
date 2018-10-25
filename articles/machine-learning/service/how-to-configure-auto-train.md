---
title: 자동화된 기계 학습 실험 구성 - Azure Machine Learning
description: 자동화된 기계 학습은 사용자를 위한 알고리즘을 선택하고 배포할 준비가 된 모델을 생성합니다. 자동화된 기계 학습 실험을 구성하는 데 사용할 수 있는 옵션에 대해 알아봅니다.
author: nacharya1
ms.author: nilesha
ms.reviewer: sgilley
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: 1aeb1315cfafbcdf3507a6e49d71e1f1e69b537c
ms.sourcegitcommit: 707bb4016e365723bc4ce59f32f3713edd387b39
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/19/2018
ms.locfileid: "49430190"
---
# <a name="configure-your-automated-machine-learning-experiment"></a>자동화된 기계 학습 실험 구성

자동화된 ML(자동화된 기계 학습)은 사용자를 위한 알고리즘과 하이퍼 매개 변수를 선택하고 배포할 준비가 된 모델을 생성합니다. 모델을 다운로드하여 추가로 사용자 지정할 수도 있습니다. 자동화된 ML 실험을 구성하는 데 사용할 수 있는 몇 가지 옵션이 있습니다. 이 가이드에서는 다양한 구성 설정을 정의하는 방법에 대해 알아봅니다.

자동화된 ML의 예제를 보려면 [자습서: 자동화된 기계 학습을 사용하여 분류 모델 학습](tutorial-auto-train-models.md) 또는 [클라우드의 자동화된 기계 학습을 사용하여 모델 학습](how-to-auto-train-remote.md)을 참조하세요.

자동화된 기계 학습에서 사용할 수 있는 구성 옵션은 다음과 같습니다.

* 실험 유형 선택(예: 분류, 회귀) 
* 데이터 원본, 형식 및 데이터 가져오기
* 계산 대상 선택(로컬 또는 원격)
* 자동화된 ML 실험 설정
* 자동화된 ML 실험 실행
* 모델 메트릭 탐색
* 모델 등록 및 배포

## <a name="select-your-experiment-type"></a>실험 유형 선택
실험을 시작하기 전에 해결하려는 기계 학습 문제의 종류를 결정해야 합니다. 자동화된 ML은 감독된 학습의 두 가지 범주인 분류와 회귀를 지원합니다. 자동화된 ML은 자동화 및 튜닝 프로세스 중에 다음 알고리즘을 지원합니다. 사용자는 알고리즘을 지정할 필요가 없습니다.
분류 | 회귀
--|--
sklearn.linear_model.LogisticRegression | sklearn.linear_model.ElasticNet
sklearn.linear_model.SGDClassifier  | sklearn.ensemble.GradientBoostingRegressor
sklearn.naive_bayes.BernoulliNB | sklearn.tree.DecisionTreeRegressor
sklearn.naive_bayes.MultinomialNB | sklearn.neighbors.KNeighborsRegressor
sklearn.svm.SVC | sklearn.linear_model.LassoLars
sklearn.svm.LinearSVC | sklearn.linear_model.SGDRegressor
sklearn.calibration.CalibratedClassifierCV |    sklearn.ensemble.RandomForestRegressor
sklearn.neighbors.KNeighborsClassifier |    sklearn.ensemble.ExtraTreesRegressor
sklearn.tree.DecisionTreeClassifier |   lightgbm.LGBMRegressor
sklearn.ensemble.RandomForestClassifier |
sklearn.ensemble.ExtraTreesClassifier   |
sklearn.ensemble.GradientBoostingClassifier |
lightgbm.LGBMClassifier |


## <a name="data-source-and-format"></a>데이터 원본 및 형식
자동화된 ML은 로컬 데스크톱 또는 클라우드의 Azure Blob Storage에 있는 데이터를 지원합니다. 데이터는 scikit-learn 지원 데이터 형식으로 읽을 수 있습니다. 데이터는 1) X(기능) 및 y(목표 변수 또는 레이블이라고도 함) numpy 배열 또는 2) pandas 데이터 프레임으로 읽을 수 있습니다. 

예제:

1.  numpy 배열

    ```python
    digits = datasets.load_digits()
    X_digits = digits.data 
    y_digits = digits.target
    ```

2.  pandas 데이터 프레임

    ```python
    Import pandas as pd
    df = pd.read_csv("https://automldemods.blob.core.windows.net/datasets/PlayaEvents2016,_1.6MB,_3.4k-rows.cleaned.2.tsv", delimiter="\t", quotechar='"') 
    # get integer labels 
    le = LabelEncoder() 
    le.fit(df["Label"].values) 
    y = le.transform(df["Label"].values) 
    df = df.drop(["Label"], axis=1) 
    df_train, _, y_train, _ = train_test_split(df, y, test_size=0.1, random_state=42)
    ```

## <a name="fetch-data-for-running-experiment-on-remote-compute"></a>원격 계산에서 실험을 실행하기 위한 데이터 가져오기

원격 계산을 사용하여 실험을 실행하는 경우 데이터 가져오기는 별도의 `get_data()` Python 스크립트로 래핑되어야 합니다. 이 스크립트는 자동화된 ML 실험이 실행되는 원격 계산에서 실행됩니다. `get_data`는 각 반복마다 연결을 통해 데이터를 가져올 필요가 없습니다. `get_data`가 없으면 원격 계산에서 실행할 때 실험이 실패합니다.

`get_data`의 예제는 다음과 같습니다.

```python
%%writefile $project_folder/get_data.py 
import pandas as pd 
from sklearn.model_selection 
import train_test_split 
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

`get_data` 스크립트에서 반환할 수 있는 항목은 다음과 같습니다.
키 | type |    상호 배타적 관계 | 설명
---|---|---|---
X | pandas 데이터 프레임 또는 numpy 배열 | data_train, label, columns |  학습할 모든 기능입니다.
y | pandas 데이터 프레임 또는 numpy 배열 |   label   | 학습할 데이터에 레이블을 지정합니다. 분류의 경우 정수 배열이어야 합니다.
X_valid | pandas 데이터 프레임 또는 numpy 배열   | data_train, label | _선택 사항_ 유효성을 검사할 모든 기능입니다. 지정하지 않으면 X가 학습과 유효성 검사 간에 분할됩니다.
y_valid |   pandas 데이터 프레임 또는 numpy 배열 | data_train, label | _선택 사항_ 유효성을 검사할 레이블 데이터입니다. 지정하지 않으면 y가 학습과 유효성 검사 간에 분할됩니다.
sample_weight | pandas 데이터 프레임 또는 numpy 배열 |   data_train, label, columns| _선택 사항_ 각 샘플에 대한 가중치입니다. 데이터 요소에 대해 서로 다른 가중치를 할당하려는 경우에 사용합니다. 
sample_weight_valid | pandas 데이터 프레임 또는 numpy 배열 | data_train, label, columns |    _선택 사항_ 각 유효성 검사 샘플에 대한 가중치입니다. 지정하지 않으면 sample_weight가 학습과 유효성 검사 간에 분할됩니다.
data_train |    pandas 데이터 프레임 |  X, y, X_valid, y_valid |    학습할 모든 데이터(기능 + 레이블)입니다.
label | string  | X, y, X_valid, y_valid |  레이블을 나타내는 data_train의 열입니다.
열 | 문자열 배열  ||  _선택 사항_ 기능에 사용할 열의 허용 목록입니다.
cv_splits_indices   | 정수 배열 ||  _선택 사항_ 교차 유효성 검사를 위해 데이터를 분할할 인덱스 목록입니다.

## <a name="train-and-validation-data"></a>데이터 학습 및 유효성 검사

get_data()를 통하거나 `AutoMLConfig` 메서드에서 직접 별도의 학습 및 유효성 검사 집합을 지정할 수 있습니다.

## <a name="cross-validation-split-options"></a>교차 유효성 검사 분할 옵션

### <a name="k-folds-cross-validation"></a>K 접기 교차 유효성 검사

`n_cross_validations` 설정을 사용하여 교차 유효성 검사의 수를 지정합니다. 학습 데이터 집합은 무작위로 동일한 크기의 `n_cross_validations` 접기로 분할됩니다. 각 교차 유효성 검사 라운드 중에 접기 중 하나는 나머지 접기에 대해 학습된 모델의 유효성 검사에 사용됩니다. 이 프로세스는 각 접기가 유효성 검사 집합으로 한 번 사용될 때까지 `n_cross_validations` 라운드 동안 반복됩니다. 마지막으로, 모든 `n_cross_validations` 라운드에 걸친 평균 점수가 보고되고 해당 모델이 전체 학습 데이터 집합에 대해 다시 학습됩니다.

### <a name="monte-carlo-cross-validation-aka-repeated-random-sub-sampling"></a>몬테카를로 교차 유효성 검사(반복된 무작위 하위 샘플링)

`validation_size`를 사용하여 유효성 검사에 사용해야 하는 학습 데이터 집합의 비율을 지정하고, `n_cross_validations`를 사용하여 교차 유효성 검사의 수를 지정합니다. 각 교차 유효성 검사 라운드 중에 나머지 데이터에 대해 학습된 모델의 유효성 검사를 위해 `validation_size` 크기의 하위 집합이 무작위로 선택됩니다. 마지막으로, 모든 `n_cross_validations` 라운드에 걸친 평균 점수가 보고되고 해당 모델이 전체 학습 데이터 집합에 대해 다시 학습됩니다.

### <a name="custom-validation-dataset"></a>사용자 지정 유효성 검사 데이터 집합

무작위 분할(일반적으로 시계열 데이터 또는 불균형 데이터)이 허용되지 않는 경우 사용자 지정 유효성 검사 데이터 집합을 사용합니다. 이를 통해 사용자 고유의 유효성 검사 데이터 집합을 지정할 수 있습니다. 모델은 무작위 데이터 집합 대신 지정된 유효성 검사 데이터 집합에 대해 평가됩니다.

## <a name="compute-to-run-experiment"></a>실험 실행 계산

다음으로, 모델을 학습할 위치를 결정합니다. 자동화된 ML 학습 실험은 소유하고 관리하는 계산 대상에서 실행됩니다. 

지원되는 계산 옵션은 다음과 같습니다.
1.  로컬 데스크톱 또는 랩톱과 같은 로컬 머신 - 일반적으로 데이터 집합이 작고 아직 탐색 단계에 있는 경우입니다.
2.  클라우드의 원격 머신(Linux를 실행하는 [Azure Data Science Virtual Machine](https://azure.microsoft.com/services/virtual-machines/data-science-virtual-machines/)) - 큰 데이터 집합이 있고 Azure 클라우드에서 사용할 수 있는 대형 머신으로 강화하려고 합니다. 
3.  Azure Batch AI 클러스터 - 자동화된 ML 반복을 확장하고 병렬로 실행하도록 설정할 수 있는 관리되는 클러스터입니다. 

<a name='configure-experiment'/>
## <a name="configure-your-experiment-settings"></a>실험 설정 구성

자동화된 ML 실험을 구성하는 데 사용할 수 있는 몇 가지 노브가 있습니다. 이러한 매개 변수는 `AutoMLConfig` 개체를 인스턴스화하여 설정됩니다.

일부 사례:

1.  반복당 최대 시간이 12,000초인 기본 메트릭으로 가중된 AUC를 사용하는 분류 실험이며, 이 실험은 50회 반복 및 2회 교차 유효성 검사 접기 후에 종료됩니다.

    ```python
    automl_classifier = AutoMLConfig(
        task='classification',
        primary_metric='AUC_weighted',
        max_time_sec=12000,
        iterations=50,
        X=X, 
        y=y,
        n_cross_validations=2)
    ```
2.  다음은 회귀 실험이 100회 반복된 후 종료되도록 설정한 예제이며, 각 반복이 5회 교차 유효성 검사 접기를 통해 최대 600초까지 지속됩니다.

    ````python
    automl_regressor = AutoMLConfig(
        task='regression',
        max_time_sec=600,
        iterations=100,
        primary_metric='r2_score',
        X=X, 
        y=y,
        n_cross_validations=5)
    ````

다음 표에는 실험에 사용할 수 있는 매개 변수 설정 및 해당 기본값이 나와 있습니다.

자산 |  설명 | 기본값
--|--|--
`task`  |기계 학습 문제의 유형을 지정합니다. 허용되는 값은 다음과 같습니다. <li>분류</li><li>회귀</li>    | 없음 |
`primary_metric` |모델 작성 시 최적화하려는 메트릭입니다. 예를 들어 accuracy(정확도)를 primary_metric으로 지정하면 자동화된 ML에서 최대 정확도의 모델을 찾습니다. 실험당 하나의 primary_metric만 지정할 수 있습니다. 허용되는 값은 다음과 같습니다. <br/>**분류**:<br/><li> accuracy  </li><li> AUC_weighted</li><li> precision_score_weighted </li><li> balanced_accuracy </li><li> average_precision_score_weighted </li><br/>**회귀**: <br/><li> normalized_mean_absolute_error </li><li> spearman_correlation </li><li> normalized_root_mean_squared_error </li><li> normalized_root_mean_squared_log_error</li><li> R2_score    </li> | 분류: accuracy <br/>회귀: spearman_correlation <br/> |
`exit_score` |  primary_metric에 대한 대상 값을 설정할 수 있습니다. primary_metric 대상을 충족하는 모델이 있으면 자동화된 ML에서 반복을 중지하고 실험이 종료됩니다. 이 값을 설정하지 않으면(기본값) 자동화된 ML 실험에서 반복에 지정된 반복 횟수만큼 계속 실행합니다. double 값을 사용합니다. 대상이 도달하지 못하면 자동화된 ML이 반복에 지정된 반복 횟수에 도달할 때까지 계속됩니다.|   없음
`iterations` |최대 반복 횟수입니다. 각 반복은 파이프라인에서 발생하는 학습 작업과 같습니다. 파이프라인은 데이터 전처리 및 모델입니다. 고품질 모델을 얻으려면 250개 이상을 사용합니다. | 100
`Concurrent_iterations`|    병렬로 실행할 최대 반복 횟수입니다. 이 설정은 원격 계산에서만 작동합니다.|    1
`max_cores_per_iteration`   | 단일 파이프라인을 학습하는 데 사용되는 계산 대상의 코어 수를 나타냅니다. 알고리즘에서 여러 개의 코어를 활용할 수 있으면 다중 코어 머신의 성능이 향상됩니다. -1로 설정하면 머신에서 사용 가능한 모든 코어를 사용할 수 있습니다.|  1
`max_time_sec` |    특정 반복에 걸리는 시간(초)을 제한합니다. 반복에서 지정된 시간을 초과하면 해당 반복이 취소됩니다. 설정되지 않으면 반복이 완료될 때까지 계속 실행됩니다. |   없음
`n_cross_validations`   |교차 유효성 검사 분할의 수입니다.| 없음
`validation_size`   |유효성 검사의 크기는 모든 학습 샘플에 대한 백분율로 설정됩니다.|  없음
`preprocess` | True/False <br/>True를 사용하면 실험에서 입력에 대한 전처리를 수행할 수 있습니다. 전처리의 하위 집합은 다음과 같습니다.<li>누락된 데이터: 누락된 데이터를 평균 숫자, 가장 많이 발생하는 텍스트로 대치합니다. </li><li>범주별 값: 데이터 형식이 숫자이고 고유 값 수가 5% 미만인 경우 단일 핫 인코딩으로 변환합니다. </li><li>기타: 전체 목록은 [GitHub 리포지토리](https://aka.ms/aml-notebooks)를 확인하세요.</li><br/>참고: 데이터가 매우 적으면 preprocess = true를 사용할 수 없습니다. |  False | 
`blacklist_algos`   | 자동화된 ML 실험에는 사용해 볼 수 있는 다양한 알고리즘이 있습니다. 실험에서 특정 알고리즘을 제외하도록 자동화된 ML을 구성합니다. 데이터 집합에서 제대로 작동하지 않는 알고리즘을 알고 있으면 유용합니다. 알고리즘을 제외하면 계산 리소스와 학습 시간을 절약할 수 있습니다.<br/>분류에 허용되는 값은 다음과 같습니다.<br/><li>logistic regression</li><li>SGD classifier</li><li>MultinomialNB</li><li>BernoulliNB</li><li>SVM</li><li>LinearSVM</li><li>kNN</li><li>DT</li><li>RF</li><li>extra trees</li><li>gradient boosting</li><li>lgbm_classifier</li><br/>회귀에 허용되는 값은 다음과 같습니다.<br/><li>Elastic net</li><li>Gradient boosting regressor</li><li>DT regressor</li><li>kNN regressor</li><li>Lasso lars</li><li>SGD regressor</li><li>RF regressor</li><li>extra trees regressor</li>|   없음
`verbosity` |가장 자세한 정보의 경우 INFO, 가장 적은 정보의 경우 CRITICAL인 로깅 수준을 제어합니다.<br/>허용되는 값은 다음과 같습니다.<br/><li>logging.INFO</li><li>logging.WARNING</li><li>logging.ERROR</li><li>logging.CRITICAL</li>  | logging.INFO</li> 
`X` | 학습할 모든 기능입니다. |  없음
`y` |   학습할 데이터에 레이블을 지정합니다. 분류의 경우 정수 배열이어야 합니다.|  없음
`X_valid`|_선택 사항_ 유효성을 검사할 모든 기능입니다. 지정하지 않으면 X가 학습과 유효성 검사 간에 분할됩니다. |   없음
`y_valid`   |_선택 사항_ 유효성을 검사할 레이블 데이터입니다. 지정하지 않으면 y가 학습과 유효성 검사 간에 분할됩니다.    | 없음
`sample_weight` |   _선택 사항_ 각 샘플에 대한 가중치입니다. 데이터 요소에 대해 서로 다른 가중치를 할당하려는 경우에 사용합니다. |   없음
`sample_weight_valid`   |   _선택 사항_ 각 유효성 검사 샘플에 대한 가중치입니다. 지정하지 않으면 sample_weight가 학습과 유효성 검사 간에 분할됩니다.   | 없음
`run_configuration` |   RunConfiguration 개체입니다.  원격 실행에 사용합니다. |없음
`data_script`  |    get_data 메서드를 포함한 파일의 경로입니다.  원격 실행에 필요합니다.   |없음


## <a name="run-experiment"></a>실험 실행

다음으로, 실험을 실행하여 모델 생성을 시작할 수 있습니다. `AutoMLConfig`를 `submit` 메서드에 전달하여 모델을 생성합니다.

```python
run = experiment.submit(automl_config, show_output=True)
```

>[!NOTE]
>먼저 종속성이 새 DSVM에 설치됩니다.  출력이 표시되는 데 최대 10분이 걸릴 수 있습니다.
>`show_output`을 True로 설정하면 출력이 콘솔에 표시됩니다.


## <a name="explore-model-metrics"></a>모델 메트릭 탐색
노트북을 사용하고 있으면 위젯 또는 인라인에서 결과를 볼 수 있습니다. 자세한 내용은 "모델 추적 및 평가"를 참조하세요. (자동화된 ML과 관련된 정보가 AML 콘텐츠에 포함되어 있는지 확인합니다.)

각 반복에 저장되는 메트릭은 다음과 같습니다.
* AUC_macro
* AUC_micro
* AUC_weighted
* accuracy
* average_precision_score_macro
* average_precision_score_micro
* average_precision_score_weighted
* balanced_accuracy
* f1_score_macro
* f1_score_micro
* f1_score_weighted
* log_loss
* norm_macro_recall
* precision_score_macro
* precision_score_micro
* precision_score_weighted
* recall_score_macro
* recall_score_micro
* recall_score_weighted
* weighted_accuracy

## <a name="next-steps"></a>다음 단계

[모델 배포 방법 및 위치](how-to-deploy-and-where.md)에 대해 자세히 알아봅니다.

[자동화된 ML을 사용하여 분류 모델을 학습하는 방법](tutorial-auto-train-models.md) 또는 [원격 리소스에서 자동화된 ML을 사용하여 학습하는 방법](how-to-auto-train-remote.md)에 대해 자세히 알아봅니다. 