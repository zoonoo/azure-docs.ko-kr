---
title: 시계열 예측 모델 자동 학습
titleSuffix: Azure Machine Learning service
description: Azure Machine Learning 서비스를 사용 하 여 자동화 된 Machine Learning을 사용 하 여 시계열 예측 회귀 모델을 학습 하는 방법을 알아봅니다.
services: machine-learning
author: trevorbye
ms.author: trbye
ms.service: machine-learning
ms.subservice: core
ms.reviewer: trbye
ms.topic: conceptual
ms.date: 06/20/2019
ms.openlocfilehash: 2a037a495a1e1ed211bd9a535891ccf75fdb140b
ms.sourcegitcommit: 32242bf7144c98a7d357712e75b1aefcf93a40cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/04/2019
ms.locfileid: "70278173"
---
# <a name="auto-train-a-time-series-forecast-model"></a>시계열 예측 모델 자동 학습

이 문서에서는 Azure Machine Learning 서비스에서 자동화 된 machine learning을 사용 하 여 시계열 예측 회귀 모델을 학습 하는 방법에 대해 알아봅니다. 예측 모델을 구성 하는 것은 자동화 된 machine learning을 사용 하 여 표준 회귀 모델을 설정 하는 것과 유사 하지만 시계열 데이터로 작업 하기 위한 특정 구성 옵션 및 전처리 단계가 있습니다. 다음 예제에서는 다음 방법을 보여 줍니다.

* 시계열 모델링에 대 한 데이터 준비
* [`AutoMLConfig`](/python/api/azureml-train-automl/azureml.train.automl.automlconfig) 개체의 특정 시계열 매개 변수 구성
* 시계열 데이터를 사용 하 여 예측 실행

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE2X1GW]

자동화 된 ML을 사용 하 여 기법과 접근 방식을 결합 하 고 권장 되는 고품질 시간 계열 예측을 얻을 수 있습니다. 자동화 된 시간 계열 실험은 다중 변형 회귀 문제로 처리 됩니다. 이전 시간 계열 값은 다른 예측 변수와 함께 회귀 변수의 추가 차원이 되도록 "피벗" 됩니다.

클래식 시계열 메서드와 달리이 방법은 학습 중에 여러 상황별 변수와 해당 변수 간의 관계를 자연스럽 게 통합 하는 이점을 제공 합니다. 실제 예측 응용 프로그램에서 여러 요인이 예측에 영향을 줄 수 있습니다. 예를 들어 판매를 예측 하는 경우 기록 추세의 상호 작용, 환율 및 가격은 모두 공동으로 판매 결과물을 구동 합니다. 추가 혜택은 회귀 모델의 모든 최신 혁신을 즉시 예측에 적용 하는 것입니다.

예측을 확장 하는 미래 (예측 마감일) 뿐만 아니라 지연 등을 [구성할](#config) 수 있습니다. 자동화 된 ML은 데이터 집합 및 예측 horizons의 모든 항목에 대해 종종 내부적으로 분기 된 단일 모델을 학습 합니다. 따라서 모델 매개 변수를 예측 하는 데 더 많은 데이터를 사용할 수 있으며 보이지 않는 시리즈에 대 한 일반화가 가능 합니다.

학습 데이터에서 추출 된 기능은 중요 한 역할을 합니다. 그리고 자동화 된 ML은 표준 전처리 단계를 수행 하 고, 계절 효과를 캡처하고 예측 정확도를 최대화 하기 위한 추가 시계열 기능을 생성 합니다.

## <a name="prerequisites"></a>전제 조건

* Azure Machine Learning 서비스 작업 영역. 작업 영역을 만들려면 [Azure Machine Learning 서비스 작업 영역 만들기](how-to-manage-workspace.md)를 참조 하세요.
* 이 문서에서는 자동화 된 machine learning 실험을 설정 하는 방법에 대 한 기본 지식이 있다고 가정 합니다. [자습서](tutorial-auto-train-models.md) 또는 [방법에](how-to-configure-auto-train.md) 따라 기본적인 자동화 된 기계 학습 실험 디자인 패턴을 볼 수 있습니다.

## <a name="preparing-data"></a>데이터 준비

자동화 된 machine learning 내에서 예측 회귀 태스크 유형과 회귀 작업 유형 간의 가장 중요 한 차이점은 유효한 시계열을 나타내는 데이터의 기능을 포함 하는 것입니다. 정규 시계열에는 잘 정의 되 고 일관 된 빈도가 있으며 연속 시간 범위의 모든 샘플 지점에 값이 있습니다. 파일 `sample.csv`의 다음 스냅숏을 살펴보십시오.

    day_datetime,store,sales_quantity,week_of_year
    9/3/2018,A,2000,36
    9/3/2018,B,600,36
    9/4/2018,A,2300,36
    9/4/2018,B,550,36
    9/5/2018,A,2100,36
    9/5/2018,B,650,36
    9/6/2018,A,2400,36
    9/6/2018,B,700,36
    9/7/2018,A,2450,36
    9/7/2018,B,650,36

이 데이터 집합은 두 개의 서로 다른 매장 a와 B가 있는 회사의 일일 판매 데이터에 대 한 간단한 예입니다. 또한 모델에서 주간 계절성 `week_of_year` 를 검색할 수 있도록 하는 기능이 있습니다. 필드 `day_datetime` 는 일일 빈도가 있는 명확한 시계열을 나타내며 필드 `sales_quantity` 는 예측을 실행 하기 위한 대상 열입니다. Pandas 데이터 프레임로 데이터를 읽은 다음 `to_datetime` 함수를 사용 하 여 시계열 `datetime` 이 형식 인지 확인 합니다.

```python
import pandas as pd
data = pd.read_csv("sample.csv")
data["day_datetime"] = pd.to_datetime(data["day_datetime"])
```

이 경우 데이터는 이미 시간 필드 `day_datetime`를 기준으로 오름차순으로 정렬 됩니다. 그러나 실험을 설정 하는 경우 올바른 시계열을 빌드하기 위해 원하는 시간 열이 오름차순으로 정렬 되어 있는지 확인 합니다. 데이터에 1000 레코드가 포함 되어 있다고 가정 하 고 데이터를 명확 하 게 분할 하 여 학습 및 테스트 데이터 집합을 만듭니다. 그런 다음 대상 필드 `sales_quantity` 를 구분 하 여 예측 학습 및 테스트 집합을 만듭니다.

```python
X_train = data.iloc[:950]
X_test = data.iloc[-50:]

y_train = X_train.pop("sales_quantity").values
y_test = X_test.pop("sales_quantity").values
```

> [!NOTE]
> 미래 가치를 예측 하기 위해 모델을 학습 하는 경우 학습에 사용 된 모든 기능을 사용 하 여 원하는 수평에 대해 예측을 실행할 수 있는지 확인 합니다. 예를 들어 수요 예측을 만들 때 현재 주가에 대 한 기능을 포함 하 여 학습 정확도를 대규모 수 있습니다. 그러나 장기적으로 예측 하려는 경우 미래 시계열 요소에 해당 하는 미래 스톡 값을 정확 하 게 예측할 수 없으며 모델 정확성이 떨어질 수 있습니다.

<a name="config"></a>
## <a name="configure-and-run-experiment"></a>실험 구성 및 실행

예측 작업의 경우 자동화 된 machine learning은 시계열 데이터와 관련 된 전처리 및 예측 단계를 사용 합니다. 다음 전처리 단계가 실행 됩니다.

* 시계열 샘플 빈도 (예: 매시간, 매일, 매주)를 검색 하 고 없는 시간 요소에 대해 새 레코드를 만들어 계열을 연속 해 서 만듭니다.
* 돌립니다 누락 값 (전방 채우기를 통해) 및 기능 열 (중앙값 열 값 사용)
* 여러 계열에서 고정 효과를 사용 하도록 설정 하는 그레인 기반 기능 만들기
* 계절 패턴을 학습 하는 데 도움이 되는 시간 기반 기능 만들기
* 범주 변수를 숫자 수량으로 인코딩

개체 `AutoMLConfig` 는 자동화 된 machine learning 작업에 필요한 설정 및 데이터를 정의 합니다. 회귀 문제와 마찬가지로 작업 유형, 반복 횟수, 학습 데이터 및 교차 유효성 검사 수와 같은 표준 학습 매개 변수를 정의 합니다. 예측 작업의 경우 실험에 영향을 주는 추가 매개 변수를 설정 해야 합니다. 다음 표에서는 각 매개 변수 및 사용법을 설명 합니다.

| 매개 변수 | Description | 필수 |
|-------|-------|-------|
|`time_column_name`|시계열을 작성 하 고 해당 빈도를 유추 하는 데 사용 되는 입력 데이터의 datetime 열을 지정 하는 데 사용 됩니다.|✓|
|`grain_column_names`|입력 데이터에서 개별 계열 그룹을 정의 하는 이름입니다. 그레인을 정의 하지 않으면 데이터 집합은 하나의 시계열으로 간주 됩니다.||
|`max_horizon`|시계열 빈도 단위로 원하는 최대 예측 구간을 정의 합니다. 단위는 학습 데이터의 시간 간격 (예: 매월, 매주 forecaster)을 기반으로 합니다.|✓|
|`target_lags`|*n* 모델 학습 전에 지연 대상 값에 대 한 기간입니다.||
|`target_rolling_window_size`|예측 값을 생성 하는 데 사용할 수 *있는 기록 기간* < = 학습 집합 크기입니다. 생략 하는 경우 *n* 은 전체 학습 집합 크기입니다.||

시간 계열 설정을 사전 개체로 만듭니다. `time_column_name` 을`day_datetime` 데이터 집합의 필드로 설정 합니다. 매개 변수 `grain_column_names` 를 정의 하 여 데이터에 대해 **두 개의 개별 시계열 그룹이** 생성 되도록 합니다. 즉, 매장 A와 B에 대해 하나씩, `max_horizon` 마지막으로 전체 테스트 집합에 대해 예측 하려면를 50로 설정 합니다. 를 사용 `target_rolling_window_size`하 여 예측 기간을 10 개로 설정 하 고 `target_lags` 매개 변수를 사용 하 여 대상 값 2 기간을 앞으로 지연 합니다.

```python
time_series_settings = {
    "time_column_name": "day_datetime",
    "grain_column_names": ["store"],
    "max_horizon": 50,
    "target_lags": 2,
    "target_rolling_window_size": 10,
    "preprocess": True,
}
```

> [!NOTE]
> 자동화된 기계 학습 사전 처리 단계(기능 정규화, 누락된 데이터 처리, 텍스트를 숫자로 변환 등)는 기본 모델의 일부가 됩니다. 예측에 모델을 사용하는 경우 학습 중에 적용되는 동일한 전처리 단계가 입력 데이터에 자동으로 적용됩니다.

이제 `forecasting` 작업 유형을 지정 `AutoMLConfig` 하 고 실험을 제출 하는 표준 개체를 만듭니다. 모델이 완료 된 후 최상의 실행 반복을 검색 합니다.

```python
from azureml.core.workspace import Workspace
from azureml.core.experiment import Experiment
from azureml.train.automl import AutoMLConfig
import logging

automl_config = AutoMLConfig(task='forecasting',
                             primary_metric='normalized_root_mean_squared_error',
                             iterations=10,
                             X=X_train,
                             y=y_train,
                             n_cross_validations=5,
                             enable_ensembling=False,
                             verbosity=logging.INFO,
                             **time_series_settings)

ws = Workspace.from_config()
experiment = Experiment(ws, "forecasting_example")
local_run = experiment.submit(automl_config, show_output=True)
best_run, fitted_model = local_run.get_output()
```

> [!NOTE]
> 교차 유효성 검사 (CV) 절차의 경우 시계열 데이터는 정식 K 접기 교차 유효성 검사 전략의 기본 통계 가정을 위반할 수 있으므로 자동화 된 machine learning은 만들 롤링 원본 유효성 검사 절차를 구현 합니다. 시계열 데이터에 대 한 교차 유효성 검사 접기. 이 절차를 사용 하려면 `n_cross_validations` `AutoMLConfig` 개체에 매개 변수를 지정 합니다. 유효성 검사를 무시 하 고 `X_valid` 및 `y_valid` 매개 변수를 사용 하 여 고유한 유효성 검사 집합을 사용할 수 있습니다.

### <a name="view-feature-engineering-summary"></a>기능 엔지니어링 요약 보기

자동화 된 machine learning의 시계열 작업 유형에 대 한 자세한 내용은 기능 엔지니어링 프로세스에서 세부 정보를 볼 수 있습니다. 다음 코드에서는 각 원시 기능과 함께 다음 특성을 보여 줍니다.

* 원시 기능 이름
* 이 원시 기능에서 구성 된 엔지니어링 된 기능의 수
* 형식이 검색 됨
* 기능이 삭제 되었는지 여부
* 원시 기능의 기능 변형 목록

```python
fitted_model.named_steps['timeseriestransformer'].get_featurization_summary()
```

## <a name="forecasting-with-best-model"></a>최적 모델로 예측

최상의 모델 반복을 사용 하 여 테스트 데이터 집합의 값을 예측 합니다.

```python
y_predict = fitted_model.predict(X_test)
y_actual = y_test.flatten()
```

`forecast()` 또는`predict()`대신 함수를 사용 하 여 예측이 시작 되어야 하는 시기를 지정할 수 있습니다. 다음 예제에서는 먼저의 `y_pred` 모든 값을로 `NaN`바꿉니다. 이 경우 일반적으로를 사용 하 `predict()`는 것 처럼 예측 원본은 학습 데이터의 끝에 있습니다. 그러나의 `y_pred` 두 번째 반만로 `NaN`바꾼 경우 함수는 처음 절반의 숫자 값을 수정 되지 않은 상태로 두고 두 번째 절반의 값을 `NaN` 예측 합니다. 함수는 예측 된 값과 정렬 된 기능을 모두 반환 합니다.

또한 `forecast_destination` 함수`forecast()` 에서 매개 변수를 사용 하 여 지정 된 날짜까지 값을 예측할 수 있습니다.

```python
y_query = y_test.copy().astype(np.float)
y_query.fill(np.nan)
y_fcst, X_trans = fitted_pipeline.forecast(
    X_test, y_query, forecast_destination=pd.Timestamp(2019, 1, 8))
```

`y_test` 실제 값과의 `y_pred`예측 값 사이에 rmse (제곱 평균 제곱 오차)를 계산 합니다.

```python
from sklearn.metrics import mean_squared_error
from math import sqrt

rmse = sqrt(mean_squared_error(y_actual, y_predict))
rmse
```

전체적인 모델 정확도를 확인 했으므로 가장 현실적인 다음 단계는 모델을 사용 하 여 알 수 없는 미래 값을 예측 하는 것입니다. 테스트 집합과 `X_test` 동일한 형식으로 데이터 집합을 제공 하 고, 미래 날짜/시간을 지정 하 고, 결과 예측 집합을 각 시계열 단계에 대 한 예측 값으로 제공 하면 됩니다. 데이터 집합의 마지막 시계열 레코드가 12/31/2018에 대 한 것으로 가정 합니다. 다음 날에 대 한 수요를 예측 하려면 (또는 예측 해야 하는 여러 기간 < = `max_horizon`) 01/01/2019에 대 한 각 매장에 대해 단일 시계열 레코드를 만듭니다.

    day_datetime,store,week_of_year
    01/01/2019,A,1
    01/01/2019,A,1

필요한 단계를 반복 하 여이 미래 데이터를 데이터 프레임에 로드 한 다음 `best_run.predict(X_test)` 를 실행 하 여 미래 값을 예측 합니다.

> [!NOTE]
> 보다 큰 기간 수에 대해서는 `max_horizon`값을 예측할 수 없습니다. 현재 마감일을 초과 하는 미래 값을 예측 하려면 모델을 더 큰 수평선으로 다시 학습 해야 합니다.

## <a name="next-steps"></a>다음 단계

* 자동화 된 machine learning을 사용 하 여 실험을 만드는 방법을 알아보려면 [자습서](tutorial-auto-train-models.md) 를 따르세요.
* [AZURE MACHINE LEARNING SDK For Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) 참조 설명서를 확인 하세요.
