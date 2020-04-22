---
title: 시계열 예측 모델 자동 학습
titleSuffix: Azure Machine Learning
description: Azure 기계 학습을 사용하여 자동화된 기계 학습을 사용하여 타임시리즈 예측 회귀 모델을 학습하는 방법을 알아봅니다.
services: machine-learning
author: trevorbye
ms.author: trbye
ms.service: machine-learning
ms.subservice: core
ms.reviewer: trbye
ms.topic: conceptual
ms.date: 03/09/2020
ms.openlocfilehash: 9f80156f61ad82e5563f1c38764c81297f5979f2
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/22/2020
ms.locfileid: "81767303"
---
# <a name="auto-train-a-time-series-forecast-model"></a>시계열 예측 모델 자동 학습
[!INCLUDE [aml-applies-to-basic-enterprise-sku](../../includes/aml-applies-to-basic-enterprise-sku.md)]

이 문서에서는 Azure 기계 학습에서 자동화된 기계 학습을 사용하여 타임시리즈 예측 회귀 모델을 학습하는 방법을 배웁니다. 예측 모델을 구성하는 것은 자동화된 기계 학습을 사용하여 표준 회귀 모델을 설정하는 것과 유사하지만, 타임시리즈 데이터 작업을 위해 특정 구성 옵션 및 전처리 단계가 있습니다. 다음 예제에서는 다음 방법을 보여 주며 다음과 같은 방법을 보여 주며 다음과 같은 방법을 보여 주며 다음과 같은 방법을 보여 주며 다음과 같은 방법을

* 열렬 모델링을 위한 데이터 준비
* [`AutoMLConfig`](/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig) 개체에서 특정 열계 매개변수 구성
* 타임시리즈 데이터로 예측 실행

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE2X1GW]

자동화된 ML을 사용하여 기술과 접근 방식을 결합하고 권장되는 고품질 의 시간계 예측을 얻을 수 있습니다. 자동화된 시간계 실험은 다변량 회귀 문제로 처리됩니다. 과거 타임계 값은 다른 예측 변수와 함께 회귀 변수에 대한 추가 차원이 되도록 "피벗"됩니다.

이 접근 방식은 기존 타임시리즈 방법과 달리 교육 중에 여러 컨텍스트 변수와 서로의 관계를 자연스럽게 통합할 수 있는 장점이 있습니다. 실제 예측 응용 프로그램에서는 여러 요인이 예측에 영향을 미칠 수 있습니다. 예를 들어 매출을 예측할 때 과거 추세, 환율 및 가격의 상호 작용이 모두 공동으로 판매 결과를 유도합니다. 또 다른 이점은 회귀 모델의 모든 최근 혁신이 예측에 즉시 적용된다는 것입니다.

예측이 확장될 수 있는 거리(예측 수평선)와 지연 등을 [구성할](#config) 수 있습니다. 자동화된 ML은 데이터 집합 및 예측 지평의 모든 항목에 대해 단일 하지만 종종 내부적으로 분기된 모델을 학습합니다. 따라서 모델 매개 변수를 추정하고 보이지 않는 계열에 대한 일반화가 가능해지는 데 더 많은 데이터를 사용할 수 있습니다.

학습 데이터에서 추출된 기능은 중요한 역할을 합니다. 또한 자동화된 ML은 표준 전처리 단계를 수행하고 추가 타임계 기능을 생성하여 계절적 효과를 포착하고 예측 정확도를 극대화합니다.

## <a name="time-series-and-deep-learning-models"></a>타임시리즈 및 딥 러닝 모델


자동 ML은 사용자에게 권장 시스템의 일부로 네이티브 타임시리즈 및 딥 러닝 모델을 모두 제공합니다. 이러한 학습자는 다음과 같습니다.
+ 예언자 (미리보기)
+ 자동 ARIMA(미리 보기)
+ 예측TCN (미리보기)

자동화된 ML의 딥 러닝을 통해 일변량 및 다변량 타임시리즈 데이터를 예측할 수 있습니다.

딥 러닝 모델에는 다음과 같은 세 가지 기본 기능이 있습니다.
1. 입력에서 출력에 이르는 임의의 매핑에서 배울 수 있습니다.
1. 여러 입력 및 출력을 지원합니다.
1. 긴 시퀀스에 걸쳐 입력 데이터에서 패턴을 자동으로 추출할 수 있습니다.

더 큰 데이터를 감안할 때 Microsoft의 ForecastTCN과 같은 딥 러닝 모델은 결과 모델의 점수를 향상시킬 수 있습니다. [딥 러닝을 위해 실험을 구성하는](#configure-a-dnn-enable-forecasting-experiment)방법에 대해 알아봅니다.

네이티브 타임시리즈 학습자는 자동화된 ML의 일부로도 제공됩니다. Prophet는 계절에 따라 강한 효과와 여러 시즌의 역사적 데이터가 있는 타임시리즈와 가장 잘 어갖 효과를 내며 작업합니다. Prophet는 정확하고 & 빠르며 이상값에 견고하며 데이터가 누락되었으며 타임 시리즈의 극적인 변화가 있습니다. 

자동 회귀 통합 이동 평균(ARIMA)은 시계열 예측에 널리 사용되는 통계 방법입니다. 이 예측 기법은 데이터가 예측 불가능하고 모델링 또는 예측하기 어려울 수 있는 주기와 같은 추세의 증거를 보여주는 단기 예측 시나리오에서 일반적으로 사용됩니다. Auto-ARIMA는 데이터를 고정 된 데이터로 변환하여 일관되고 신뢰할 수있는 결과를 제공합니다.

## <a name="prerequisites"></a>사전 요구 사항

* Azure Machine Learning 작업 영역 작업 영역을 만들려면 [Azure 기계 학습 작업 영역 만들기를](how-to-manage-workspace.md)참조하십시오.
* 이 문서에서는 자동화된 기계 학습 실험 설정에 대한 기본적인 친숙함을 가정합니다. 기본 자동화 된 기계 학습 실험 디자인 패턴을 보려면 [자습서](tutorial-auto-train-models.md) 또는 [방법을](how-to-configure-auto-train.md) 따르십시오.

## <a name="preparing-data"></a>데이터 준비

자동화된 기계 학습 내에서 예측 회귀 작업 유형과 회귀 작업 유형 간의 가장 중요한 차이점은 유효한 시간계를 나타내는 데이터의 기능을 포함하는 것입니다. 일반 열류는 잘 정의되고 일관된 주파수를 가지며 연속 시간 범위의 모든 샘플 지점에서 값을 가합니다. 파일의 `sample.csv`다음 스냅숏을 고려합니다.

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

이 데이터 집합은 A와 B라는 두 개의 서로 다른 매장이 있는 회사의 일일 판매 `week_of_year` 데이터의 간단한 예이며, 또한 모델이 주간 계절성을 감지할 수 있는 기능이 있습니다. 필드는 `day_datetime` 일일 빈도가 있는 깨끗한 시간계를 `sales_quantity` 나타내며 필드는 예측을 실행하는 대상 열입니다. 데이터를 Pandas 데이터 프레임으로 읽은 다음 `to_datetime` 이 함수를 사용하여 `datetime` 시계열이 유형인지 확인합니다.

```python
import pandas as pd
data = pd.read_csv("sample.csv")
data["day_datetime"] = pd.to_datetime(data["day_datetime"])
```

이 경우 데이터는 이미 시간 필드별로 `day_datetime`오름차순으로 정렬됩니다. 그러나 실험을 설정할 때 원하는 시간 열이 오름차순으로 정렬되어 유효한 열열을 작성해야 합니다. 데이터에 1,000개의 레코드가 포함되어 있다고 가정하고 데이터에 결정적인 분할을 만들어 학습 및 테스트 데이터 집합을 만듭니다. 레이블 열 이름을 식별하고 레이블로 설정합니다. 이 예제에서는 레이블이 `sales_quantity`됩니다. 그런 다음 레이블 `test_data` 필드를 분리하여 집합을 형성합니다. `test_target`

```python
train_data = data.iloc[:950]
test_data = data.iloc[-50:]

label =  "sales_quantity"
 
test_labels = test_data.pop(label).values
```

> [!NOTE]
> 미래 가치를 예측하기 위한 모델을 학습할 때 의도한 수평선에 대한 예측을 실행할 때 교육에 사용되는 모든 기능을 사용할 수 있는지 확인합니다. 예를 들어 현재 주가에 대한 기능을 포함하여 수요 예측을 만들 때 교육 정확도가 크게 향상될 수 있습니다. 그러나 긴 수평선으로 예측하려는 경우 향후 시간계 포인트에 해당하는 미래 주식 가치를 정확하게 예측하지 못할 수 있으며 모델 정확도가 저하될 수 있습니다.

<a name="config"></a>
## <a name="configure-and-run-experiment"></a>실험 구성 및 실행

자동화된 기계 학습은 예측 작업을 위해 시열 데이터와 관련된 전처리 및 추정 단계를 사용합니다. 다음 전처리 단계가 실행됩니다.

* 시계열 샘플 빈도(예: 시간별, 일별, 주간)를 감지하고 부재 시간 점에 대한 새 레코드를 만들어 계열을 연속적으로 만듭니다.
* 대상에서 누락된 값(정방향 채우기을 통해) 및 피쳐 열(중앙값 열 값 사용)
* 다양한 계열에 걸쳐 고정 효과를 활성화하기 위해 그레인 기반 기능 만들기
* 계절패턴 학습에 도움이 되는 시간 기반 기능 생성
* 범주형 변수를 숫자 수량으로 인코딩

개체는 [`AutoMLConfig`](https://docs.microsoft.com/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig?view=azure-ml-py) 자동화된 기계 학습 작업에 필요한 설정 및 데이터를 정의합니다. 회귀 문제와 마찬가지로 작업 유형, 반복 횟수, 학습 데이터 및 교차 유효성 검사 수와 같은 표준 학습 매개 변수를 정의합니다. 예측 작업의 경우 실험에 영향을 주는 추가 매개 변수를 설정해야 합니다. 다음 표는 각 매개 변수와 해당 용도에 대해 설명합니다.

| 매개&nbsp;변수 이름 | 설명 | 필수 |
|-------|-------|-------|
|`time_column_name`|시간계를 작성하고 빈도를 유추하는 데 사용되는 입력 데이터의 날짜 시간 열을 지정하는 데 사용됩니다.|✓|
|`grain_column_names`|입력 데이터의 개별 계열 그룹을 정의하는 이름입니다. 그레인이 정의되지 않은 경우 데이터 집합은 하나의 타임계열로 가정합니다.||
|`max_horizon`|시간계열 주파수 단위로 원하는 최대 예측 수평선을 정의합니다. 단위는 교육 데이터의 시간 간격(예: 월별, 매주 예측이 예측해야 하는 시간 간격)을 기반으로 합니다.|✓|
|`target_lags`|데이터 빈도에 따라 대상 값을 지연하는 행 수입니다. 지연은 목록 또는 단일 정수로 표시됩니다. 독립 변수와 종속 변수 간의 관계가 기본적으로 일치하거나 상관 관계가 없는 경우 Lag를 사용해야 합니다. 예를 들어, 제품에 대한 수요를 예측하려고 할 때, 어떤 달의 수요는 3 개월 전에 특정 상품의 가격에 따라 달라질 수 있습니다. 이 예제에서는 모델이 올바른 관계에 대해 학습하도록 대상(수요)을 3개월 씩 부정적으로 지연시킬 수 있습니다.||
|`target_rolling_window_size`|*n* 예측 된 값을 생성하는 데 사용할 기록 기간, <= 교육 세트 크기. 생략된 경우 *n은* 전체 학습 세트 크기입니다. 모델을 학습할 때 일정량의 기록만 고려할 때 이 매개변수를 지정합니다.||
|`enable_dnn`|DNN 예측을 활성화합니다.||

자세한 내용은 [참조 문서를](/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig) 참조하십시오.

시간열 설정을 사전 개체로 만듭니다. 데이터 `time_column_name` 집합의 `day_datetime` 필드로 설정합니다. `grain_column_names` 데이터에 대해 두 **개의 별도의 타임계 그룹이** 만들어지도록 매개변수를 정의합니다. 저장소 A와 B. 마지막으로 전체 `max_horizon` 테스트 집합을 예측하기 위해 50으로 설정합니다. 을 사용하여 예측 기간을 10개의 마침표로 `target_rolling_window_size`설정하고 매개 변수를 사용하여 `target_lags` 두 기간 앞의 대상 값에 대한 단일 지연을 지정합니다. 이러한 값을 자동으로 `max_horizon` `target_rolling_window_size` `target_lags` 감지하는 "자동"을 설정하고 설정하는 것이 좋습니다. 아래 예제에서는 이러한 매개 변수에 대해 "자동" 설정이 사용되었습니다. 

```python
time_series_settings = {
    "time_column_name": "day_datetime",
    "grain_column_names": ["store"],
    "max_horizon": "auto",
    "target_lags": "auto",
    "target_rolling_window_size": "auto",
    "preprocess": True,
}
```

> [!NOTE]
> 자동화된 기계 학습 사전 처리 단계(기능 정규화, 누락된 데이터 처리, 텍스트를 숫자로 변환 등)는 기본 모델의 일부가 됩니다. 예측에 모델을 사용하는 경우 학습 중에 적용되는 동일한 전처리 단계가 입력 데이터에 자동으로 적용됩니다.

AutoML은 `grain_column_names` 위의 코드 조각에서 정의하여 여러 시계열이라고도 하는 두 개의 별도의 시계열 그룹을 만듭니다. 그레인이 정의되지 않은 경우 AutoML은 데이터 집합이 단일 시계열이라고 가정합니다. 단일 연열에 대한 자세한 내용은 [energy_demand_notebook.](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning/forecasting-energy-demand)

이제 `forecasting` 작업 `AutoMLConfig` 유형을 지정하여 표준 개체를 만들고 실험을 제출합니다. 모델이 완료되면 최상의 실행 반복을 검색합니다.

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

다음을 포함한 고급 예측 구성에 대한 자세한 코드 예제는 [예측 샘플 노트북을](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning) 참조하십시오.

* [휴일 감지 및 위화](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-bike-share/auto-ml-forecasting-bike-share.ipynb)
* [롤링 오리진 교차 검증](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-energy-demand/auto-ml-forecasting-energy-demand.ipynb)
* [구성 가능한 지연](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-bike-share/auto-ml-forecasting-bike-share.ipynb)
* [롤링 윈도우 집계 기능](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-energy-demand/auto-ml-forecasting-energy-demand.ipynb)
* [Dnn](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-beer-remote/auto-ml-forecasting-beer-remote.ipynb)

### <a name="configure-a-dnn-enable-forecasting-experiment"></a>DNN 구성 예측 실험

> [!NOTE]
> 자동화된 기계 학습에서 예측에 대한 DNN 지원은 미리 보기로 되어 있으며 로컬 실행에는 지원되지 않습니다.

DNN을 예측에 활용하려면 AutoMLConfig의 `enable_dnn` 매개 변수를 true로 설정해야 합니다. 

```python
automl_config = AutoMLConfig(task='forecasting',
                             enable_dnn=True,
                             ...
                             **time_series_settings)
```
[자동 MLConfig 에](#configure-and-run-experiment)대해 자세히 알아보십시오.

또는 스튜디오에서 `Enable deep learning` 옵션을 선택할 수 있습니다.
![대체 텍스트](./media/how-to-auto-train-forecast/enable_dnn.png)

GPU SCO가 있는 AML Compute 클러스터와 두 개 이상의 노드를 계산 대상으로 사용하는 것이 좋습니다. DNN 교육이 완료될 때까지 충분한 시간을 허용하려면 실험 시간 시간을 최소 2시간으로 설정하는 것이 좋습니다.
GPU를 포함하는 AML 컴퓨팅 및 VM 크기에 대한 자세한 내용은 [AML 컴퓨팅 설명서](how-to-set-up-training-targets.md#amlcompute) 및 [GPU 최적화 가상 머신 크기 설명서를](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-gpu)참조하십시오.

DN을 활용하는 자세한 코드 예제는 [음료 생산 예측 노트북을](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-beer-remote/auto-ml-forecasting-beer-remote.ipynb) 참조하십시오.

### <a name="view-feature-engineering-summary"></a>피처 엔지니어링 요약 보기

자동화된 기계 학습의 타임시리즈 작업 유형의 경우 기능 엔지니어링 프로세스의 세부 정보를 볼 수 있습니다. 다음 코드는 다음 특성과 함께 각 원시 기능을 보여 주며 다음과 같은 특성을 보여 주며 다음과 같은 특성을 보여 주며 다음과 같은 특성을 보여 주며 다음과 같은 특성을 보여 주며 다음과 같은 특성

* 원시 피쳐 이름
* 이 원시 기능으로 구성된 엔지니어링 피쳐 수
* 검색된 형식
* 피처 삭제 여부
* 원시 피쳐의 피쳐 변환 목록

```python
fitted_model.named_steps['timeseriestransformer'].get_featurization_summary()
```

## <a name="forecasting-with-best-model"></a>최고의 모델로 예측

최상의 모델 반복을 사용하여 테스트 데이터 집합의 값을 예측합니다.

이 `forecast()` 함수는 `predict()`예측을 시작해야 하는 시기의 사양을 허용하는 대신 사용해야 합니다. 다음 예제에서는 먼저 모든 값을 `y_pred` 로 `NaN`바꿉꿉을 입니다. 예측 원본은 일반적으로 를 사용할 `predict()`때와 마찬가지로 이 경우 학습 데이터의 끝에 표시됩니다. 그러나 후반부만 `y_pred` 대체하면 `NaN`함수는 첫 번째 절반의 숫자 값을 수정하지 않고 그대로 두지만 후반의 값은 `NaN` 예측합니다. 함수는 예측된 값과 정렬된 피쳐를 모두 반환합니다.

함수의 매개 `forecast_destination` 변수를 `forecast()` 사용하여 지정된 날짜까지 값을 예측할 수도 있습니다.

```python
label_query = test_labels.copy().astype(np.float)
label_query.fill(np.nan)
label_fcst, data_trans = fitted_pipeline.forecast(
    test_data, label_query, forecast_destination=pd.Timestamp(2019, 1, 8))
```

`actual_labels` 실제 값과 `predict_labels`에서 예측된 값 간에 RMSE(근위평균 제곱 오차)를 계산합니다.

```python
from sklearn.metrics import mean_squared_error
from math import sqrt

rmse = sqrt(mean_squared_error(actual_labels, predict_labels))
rmse
```

전체 모델 정확도가 결정되었으므로 가장 현실적인 다음 단계는 모델을 사용하여 알 수 없는 미래 값을 예측하는 것입니다. 테스트 집합과 `test_data` 동일한 형식으로 데이터 집합을 제공하지만 향후 날짜 시간과 함께 결과 예측 집합은 각 시간계 열렬 단계의 예측 값입니다. 데이터 집합의 마지막 타임시리즈 레코드가 2018년 12월 31일이라고 가정합니다. 다음 날(또는 예측해야 하는 만큼의 기간) 수요를 예측하려면 <= `max_horizon`01/01/2019에 대한 각 저장소에 대한 단일 시계열 레코드를 만듭니다.

    day_datetime,store,week_of_year
    01/01/2019,A,1
    01/01/2019,A,1

이 이후 데이터를 데이터 프레임에 로드한 다음 `best_run.predict(test_data)` 실행하여 미래 값을 예측하는 데 필요한 단계를 반복합니다.

> [!NOTE]
> 값은 `max_horizon`보다 큰 기간 의 수에 대해 예측할 수 없습니다. 현재 지평선을 넘어 미래 가치를 예측하려면 모델을 더 큰 수평선으로 다시 학습해야 합니다.

## <a name="next-steps"></a>다음 단계

* 자동화된 기계 학습을 사용하여 실험을 만드는 방법을 알아보려면 [자습서를](tutorial-auto-train-models.md) 따르십시오.
* 파이썬 참조 설명서에 [대한 Azure 기계 학습 SDK를](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) 봅니다.
