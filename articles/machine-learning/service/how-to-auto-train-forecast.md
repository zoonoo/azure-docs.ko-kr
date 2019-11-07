---
title: 시계열 예측 모델 자동 학습
titleSuffix: Azure Machine Learning
description: Azure Machine Learning를 사용 하 여 자동화 된 Machine Learning을 사용 하 여 시계열 예측 회귀 모델을 학습 하는 방법을 알아봅니다.
services: machine-learning
author: trevorbye
ms.author: trbye
ms.service: machine-learning
ms.subservice: core
ms.reviewer: trbye
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 276e741a9462c19a3cba9ad1f9ac44e2da7ef1d3
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73580696"
---
# <a name="auto-train-a-time-series-forecast-model"></a>시계열 예측 모델 자동 학습
[!INCLUDE [aml-applies-to-basic-enterprise-sku](../../../includes/aml-applies-to-basic-enterprise-sku.md)]

이 문서에서는 Azure Machine Learning에서 자동화 된 machine learning을 사용 하 여 시계열 예측 회귀 모델을 학습 하는 방법에 대해 알아봅니다. 예측 모델을 구성 하는 것은 자동화 된 machine learning을 사용 하 여 표준 회귀 모델을 설정 하는 것과 유사 하지만 시계열 데이터로 작업 하기 위한 특정 구성 옵션 및 전처리 단계가 있습니다. 다음 예제에서는 다음 방법을 보여 줍니다.

* 시계열 모델링에 대 한 데이터 준비
* [`AutoMLConfig`](/python/api/azureml-train-automl/azureml.train.automl.automlconfig) 개체의 특정 시계열 매개 변수 구성
* 시계열 데이터를 사용 하 여 예측 실행

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE2X1GW]

자동화 된 ML을 사용 하 여 기법과 접근 방식을 결합 하 고 권장 되는 고품질 시간 계열 예측을 얻을 수 있습니다. 자동화 된 시간 계열 실험은 다중 변형 회귀 문제로 처리 됩니다. 이전 시간 계열 값은 다른 예측 변수와 함께 회귀 변수의 추가 차원이 되도록 "피벗" 됩니다.

클래식 시계열 메서드와 달리이 방법은 학습 중에 여러 상황별 변수와 해당 변수 간의 관계를 자연스럽 게 통합 하는 이점을 제공 합니다. 실제 예측 응용 프로그램에서 여러 요인이 예측에 영향을 줄 수 있습니다. 예를 들어 판매를 예측 하는 경우 기록 추세의 상호 작용, 환율 및 가격은 모두 공동으로 판매 결과물을 구동 합니다. 추가 혜택은 회귀 모델의 모든 최신 혁신을 즉시 예측에 적용 하는 것입니다.

예측을 확장 하는 미래 (예측 마감일) 뿐만 아니라 지연 등을 [구성할](#config) 수 있습니다. 자동화 된 ML은 데이터 집합 및 예측 horizons의 모든 항목에 대해 종종 내부적으로 분기 된 단일 모델을 학습 합니다. 따라서 모델 매개 변수를 예측 하는 데 더 많은 데이터를 사용할 수 있으며 보이지 않는 시리즈에 대 한 일반화가 가능 합니다.

학습 데이터에서 추출 된 기능은 중요 한 역할을 합니다. 그리고 자동화 된 ML은 표준 전처리 단계를 수행 하 고, 계절 효과를 캡처하고 예측 정확도를 최대화 하기 위한 추가 시계열 기능을 생성 합니다.

## <a name="time-series-and-deep-learning-models"></a>시계열 및 심층 학습 모델


자동화 된 ML은 사용자에 게 기본 시계열 및 심층 학습 모델을 모두 권장 사항 시스템의 일부로 제공 합니다. 이러한 학습자는 다음과 같습니다.
+ Prophet
+ 자동 ARIMA
+ ForecastTCN

자동화 된 ML의 심층 학습을 통해 시계열 데이터를 예측 하 고 다차원 데이터를 예측할 수 있습니다.

심층 학습 모델에는 세 가지 내장 capbailities 있습니다.
1. 입력에서 출력으로의 임의 매핑에서 학습할 수 있습니다.
1. 여러 입력 및 출력을 지원 합니다.
1. 긴 시퀀스에 걸쳐 있는 입력 데이터의 패턴을 자동으로 추출할 수 있습니다.

더 큰 데이터를 제공 하는 경우 Microsofts ' ForecastTCN와 같은 심층 학습 모델을 통해 결과 모델의 점수를 향상할 수 있습니다. 

기본 시계열 학습자는 자동화 된 ML의 일부로도 제공 됩니다. Prophet는 계절 효과가 강 하 고 기록 데이터가 여러 개 있는 시계열에서 가장 잘 작동 합니다. Prophet는 & 정확 하 고 이상 값에 대 한 강력 하 고, 누락 된 데이터 및 시계열의 극적인 변화입니다. 

ARIMA (자동 회귀 Integrated 이동 평균)는 시계열 예측에 사용 되는 인기 있는 통계 방법입니다. 이 예측 기술은 데이터에서 주기 등의 추세에 대 한 증거를 표시 하는 단기 예측 시나리오에서 일반적으로 사용 됩니다 .이 시나리오는 예측할 수 없으며 모델 또는 예측 하기 어려울 수 있습니다. 자동 ARIMA는 데이터를 고정 된 데이터로 변환 하 여 일관 되 고 신뢰할 수 있는 결과를 수신 합니다.

## <a name="prerequisites"></a>필수 조건

* Azure Machine Learning 작업 영역 작업 영역을 만들려면 [Azure Machine Learning 작업 영역 만들기](how-to-manage-workspace.md)를 참조 하세요.
* 이 문서에서는 자동화 된 machine learning 실험을 설정 하는 방법에 대 한 기본 지식이 있다고 가정 합니다. [자습서](tutorial-auto-train-models.md) 또는 [방법에](how-to-configure-auto-train.md) 따라 기본적인 자동화 된 기계 학습 실험 디자인 패턴을 볼 수 있습니다.

## <a name="preparing-data"></a>데이터 준비

자동화 된 machine learning 내에서 예측 회귀 태스크 유형과 회귀 작업 유형 간의 가장 중요 한 차이점은 유효한 시계열을 나타내는 데이터의 기능을 포함 하는 것입니다. 정규 시계열에는 잘 정의 되 고 일관 된 빈도가 있으며 연속 시간 범위의 모든 샘플 지점에 값이 있습니다. `sample.csv`파일의 다음 스냅숏을 살펴보십시오.

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

이 데이터 집합은 두 개의 서로 다른 매장, A와 B가 있는 회사의 일일 판매 데이터에 대 한 간단한 예입니다. 또한 모델에서 주간 계절성를 검색할 수 있도록 하는 `week_of_year` 기능을 사용할 수 있습니다. 필드 `day_datetime`는 매일 빈도가 있는 명확한 시계열을 나타내고, `sales_quantity` 필드는 예측을 실행 하기 위한 대상 열입니다. Pandas 데이터 프레임로 데이터를 읽은 다음 `to_datetime` 함수를 사용 하 여 시계열이 `datetime` 형식 인지 확인 합니다.

```python
import pandas as pd
data = pd.read_csv("sample.csv")
data["day_datetime"] = pd.to_datetime(data["day_datetime"])
```

이 경우 데이터는 `day_datetime`시간 필드를 기준으로 오름차순으로 정렬 됩니다. 그러나 실험을 설정 하는 경우 올바른 시계열을 빌드하기 위해 원하는 시간 열이 오름차순으로 정렬 되어 있는지 확인 합니다. 데이터에 1000 레코드가 포함 되어 있다고 가정 하 고 데이터를 명확 하 게 분할 하 여 학습 및 테스트 데이터 집합을 만듭니다. 레이블 열 이름을 확인 하 고 레이블 열로 설정 합니다. 이 예에서 레이블은 `sales_quantity`됩니다. 그런 다음 `test_data`의 레이블 필드를 분리 하 여 `test_target` 집합을 형성 합니다.

```python
train_data = data.iloc[:950]
test_data = data.iloc[-50:]

label =  "sales_quantity"
 
test_labels = test_data.pop(label).values
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

`AutoMLConfig` 개체는 자동화 된 machine learning 작업에 필요한 설정 및 데이터를 정의 합니다. 회귀 문제와 마찬가지로 작업 유형, 반복 횟수, 학습 데이터 및 교차 유효성 검사 수와 같은 표준 학습 매개 변수를 정의 합니다. 예측 작업의 경우 실험에 영향을 주는 추가 매개 변수를 설정 해야 합니다. 다음 표에서는 각 매개 변수 및 사용법을 설명 합니다.

| 매개 변수 | 설명 | 필수 |
|-------|-------|-------|
|`time_column_name`|시계열을 작성 하 고 해당 빈도를 유추 하는 데 사용 되는 입력 데이터의 datetime 열을 지정 하는 데 사용 됩니다.|✓|
|`grain_column_names`|입력 데이터에서 개별 계열 그룹을 정의 하는 이름입니다. 그레인을 정의 하지 않으면 데이터 집합은 하나의 시계열으로 간주 됩니다.||
|`max_horizon`|시계열 빈도 단위로 원하는 최대 예측 구간을 정의 합니다. 단위는 학습 데이터의 시간 간격 (예: 매월, 매주 forecaster)을 기반으로 합니다.|✓|
|`target_lags`|데이터의 빈도에 따라 대상 값의 지연에 사용 되는 행 수입니다. 이는 목록 또는 단일 정수로 표시 됩니다. 독립 변수와 종속 변수 간의 관계가 기본적으로 일치 하거나 상관 관계를 지정 하지 않는 경우에는 Lag를 사용 해야 합니다. 예를 들어, 제품 수요를 예측 하려는 경우 매월 수요는 특정 상품 3 개월 전에 가격에 따라 달라질 수 있습니다. 이 예에서는 모델이 올바른 관계에 대해 학습 하도록 대상 (수요)을 3 개월로 지연 시킬 수 있습니다.||
|`target_rolling_window_size`|예측 값을 생성 하는 데 사용할 수 *있는 기록 기간* < = 학습 집합 크기입니다. 생략 하는 경우 *n* 은 전체 학습 집합 크기입니다. 모델을 학습할 때 특정 분량의 기록만 고려 하려는 경우이 매개 변수를 지정 합니다.||
|`enable_dnn`|DNNs 예측을 사용 하도록 설정 합니다.||

자세한 내용은 [참조 설명서](https://docs.microsoft.com/python/api/azureml-train-automl/azureml.train.automl.automlconfig?view=azure-ml-py) 를 참조 하세요.

시간 계열 설정을 사전 개체로 만듭니다. `time_column_name`를 데이터 집합의 `day_datetime` 필드로 설정 합니다. `grain_column_names` 매개 변수를 정의 하 여 데이터에 대해 **두 개의 개별 시계열 그룹이** 생성 되도록 합니다. 저장소 A와 B에 대 한 하나입니다. 마지막으로 전체 테스트 집합을 예측 하기 위해 `max_horizon`를 50으로 설정 합니다. `target_rolling_window_size`를 사용 하 여 예측 기간을 10 개로 설정 하 고 `target_lags` 매개 변수를 사용 하 여 2 개 기간의 대상 값에 단일 지연 시간을 지정 합니다.

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

AutoML은 위의 코드 조각에서 `grain_column_names`를 정의 하 여 여러 시계열이 라고도 하는 두 개의 개별 시계열 그룹을 만듭니다. 그레인을 정의 하지 않으면 AutoML은 데이터 집합이 단일 시계열 이라고 가정 합니다. 단일 시계열에 대 한 자세한 내용은 [energy_demand_notebook](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning/forecasting-energy-demand)를 참조 하세요.

이제 표준 `AutoMLConfig` 개체를 만들고 `forecasting` 작업 유형을 지정 하 고 실험을 제출 합니다. 모델이 완료 된 후 최상의 실행 반복을 검색 합니다.

```python
from azureml.core.workspace import Workspace
from azureml.core.experiment import Experiment
from azureml.train.automl import AutoMLConfig
import logging

automl_config = AutoMLConfig(task='forecasting',
                             primary_metric='normalized_root_mean_squared_error',
                             experiment_timeout_minutes=15,
                             enable_early_stopping=True,
                             training_data=train_data,
                             label_column_name=label,
                             n_cross_validations=5,
                             enable_ensembling=False,
                             verbosity=logging.INFO,
                             **time_series_settings)

ws = Workspace.from_config()
experiment = Experiment(ws, "forecasting_example")
local_run = experiment.submit(automl_config, show_output=True)
best_run, fitted_model = local_run.get_output()
```

다음을 포함 하 여 고급 예측 구성의 자세한 코드 예제는 [에너지 수요 노트북](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-energy-demand/auto-ml-forecasting-energy-demand.ipynb) 을 참조 하세요.

* 휴일 검색 및 기능화
* 롤링 원본 교차 유효성 검사
* 구성 가능한 지연
* 창 롤링 집계 기능

### <a name="configure-a-dnn-enable-forecasting-experiment"></a>DNN 구성 예측 실험 사용

> [!NOTE]
> 자동 Machine Learning 예측에 대 한 DNN 지원은 미리 보기 상태입니다.

예측을 위해 DNNs를 활용 하려면 AutoMLConfig의 `enable_dnn` 매개 변수를 true로 설정 해야 합니다. 

DNNs를 사용 하려면 GPU Sku와 2 개 이상의 노드가 있는 AML 계산 클러스터를 계산 대상으로 사용 하는 것이 좋습니다. 자세한 내용은 [AML 계산 설명서](how-to-set-up-training-targets.md#amlcompute) 를 참조 하세요. Gpu를 포함 하는 VM 크기에 대 한 자세한 내용은 [gpu 최적화 가상 머신 크기](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-gpu) 를 참조 하세요.

DNN 교육이 완료 될 때까지 충분 한 시간을 허용 하려면 실험 시간 제한을 몇 시간 이상으로 설정 하는 것이 좋습니다.

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
predict_labels = fitted_model.predict(test_data)
actual_labels = test_labels.flatten()
```

또는 `predict()`대신 `forecast()` 함수를 사용 하 여 예측을 시작할 시기를 지정할 수 있습니다. 다음 예제에서는 먼저 `y_pred`의 모든 값을 `NaN`으로 바꿉니다. 이 경우에는 일반적으로 `predict()`를 사용할 때와 마찬가지로 예측 원본이 학습 데이터의 끝에 있습니다. 그러나 `y_pred`의 두 번째 반만 `NaN`로 바꾸면 함수는 처음 절반의 숫자 값을 수정 되지 않은 상태로 유지 하지만 두 번째 절반의 `NaN` 값을 예측 합니다. 함수는 예측 된 값과 정렬 된 기능을 모두 반환 합니다.

`forecast()` 함수에서 `forecast_destination` 매개 변수를 사용 하 여 지정 된 날짜까지 값을 예측할 수도 있습니다.

```python
label_query = test_labels.copy().astype(np.float)
label_query.fill(np.nan)
label_fcst, data_trans = fitted_pipeline.forecast(
    test_data, label_query, forecast_destination=pd.Timestamp(2019, 1, 8))
```

`actual_labels` 실제 값과 `predict_labels`의 예측 값 사이에서 RMSE (제곱 평균 오차)를 계산 합니다.

```python
from sklearn.metrics import mean_squared_error
from math import sqrt

rmse = sqrt(mean_squared_error(actual_lables, predict_labels))
rmse
```

전체적인 모델 정확도를 확인 했으므로 가장 현실적인 다음 단계는 모델을 사용 하 여 알 수 없는 미래 값을 예측 하는 것입니다. `test_data` 테스트 집합과 동일한 형식으로 데이터 집합을 제공 하 고 미래 날짜와 시간을 지정 하 고 결과 예측 집합은 각 시계열 단계에 대 한 예측 값입니다. 데이터 집합의 마지막 시계열 레코드가 12/31/2018에 대 한 것으로 가정 합니다. 다음 날에 대 한 수요를 예측 하려면 (또는 예측 해야 하는 여러 기간 < = `max_horizon`) 01/01/2019에 대 한 각 매장에 대해 단일 시계열 레코드를 만듭니다.

    day_datetime,store,week_of_year
    01/01/2019,A,1
    01/01/2019,A,1

필요한 단계를 반복 하 여이 미래 데이터를 데이터 프레임에 로드 한 다음 `best_run.predict(test_data)`를 실행 하 여 미래 가치를 예측 합니다.

> [!NOTE]
> `max_horizon`보다 큰 기간 수에 대해서는 값을 예측할 수 없습니다. 현재 마감일을 초과 하는 미래 값을 예측 하려면 모델을 더 큰 수평선으로 다시 학습 해야 합니다.

## <a name="next-steps"></a>다음 단계

* 자동화 된 machine learning을 사용 하 여 실험을 만드는 방법을 알아보려면 [자습서](tutorial-auto-train-models.md) 를 따르세요.
* [AZURE MACHINE LEARNING SDK For Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) 참조 설명서를 확인 하세요.
