---
title: 시계열 예측 모델 자동 학습
titleSuffix: Azure Machine Learning
description: Azure Machine Learning을 사용하여 자동화된 기계 학습으로 시계열 예측 회귀 모델을 학습하는 방법을 알아봅니다.
services: machine-learning
author: nibaccam
ms.author: nibaccam
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.date: 03/09/2020
ms.openlocfilehash: ec2ef52978a24619103d9a0a2b7fb8ed1099c40b
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/23/2020
ms.locfileid: "87031595"
---
# <a name="auto-train-a-time-series-forecast-model"></a>시계열 예측 모델 자동 학습
[!INCLUDE [aml-applies-to-basic-enterprise-sku](../../includes/aml-applies-to-basic-enterprise-sku.md)]

이 문서에서는 [Azure Machine Learning Python SDK](https://docs.microsoft.com/python/api/overview/azure/ml/?view=azure-ml-py)에서 자동화된 기계 학습을 사용하여 시계열 예측 회귀 모델을 구성하고 학습 하는 방법을 알아봅니다. 

최소한의 코드를 사용하는 환경의 경우 [자습서: 자동화된 기계 학습으로 수요 예측](tutorial-automated-ml-forecast.md)을 통해 [Azure Machine Learning Studio](https://ml.azure.com/)에서 자동화된 기계 학습을 사용하는 시계열 예측 예제를 참조하세요.

예측 모델을 구성하는 것은 자동화된 기계 학습을 사용하여 표준 회귀 모델을 설정하는 것과 유사하지만 시계열 데이터로 작업하기 위한 특정 구성 옵션 및 전처리 단계가 있습니다. 

예를 들어, 앞으로 예측을 확장해야 하는 기간(예측 구간)과 지연 등을 [구성](#config)할 수 있습니다. 자동화된 ML은 데이터 세트 및 예측 구간의 모든 항목에 대해 단일 모델이지만 종종 내부적으로 분기된 모델을 학습합니다. 따라서 모델 매개 변수를 예측하는 데 더 많은 데이터를 사용할 수 있으며 보이지 않는 계열에 대한 일반화가 가능합니다.

다음 예제에서는 이 방법을 보여 줍니다.

* 시계열 모델링에 대한 데이터 준비
* [`AutoMLConfig`](/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig) 개체에서 특정 시계열 매개 변수 구성
* 시계열 데이터를 사용하여 예측 실행

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE2X1GW]

기존 시계열 방법과 달리, 자동화된 ML에서는 이전 시계열 값이 "피벗"되어 다른 예측 변수와 함께 회귀 변수의 추가 차원이 됩니다. 이 방법은 학습 중에 여러 컨텍스트 변수와 각 변수 간 관계를 통합합니다. 여러 요인이 예측에 영향을 줄 수 있으므로 이 방법은 실제 예측 시나리오에 적합합니다. 예를 들어, 판매를 예측하는 경우 판매 결과에 기록 추세의 상호 작용, 환율 및 가격이 모두 함께 영향을 미칩니다. 

학습 데이터에서 추출된 기능은 중요한 역할을 합니다. 또한 자동화된 ML은 표준 전처리 단계를 수행하고 계절적 효과를 캡처하고 예측 정확도를 최대화하기 위해 추가 시계열 기능을 생성합니다.

## <a name="time-series-and-deep-learning-models"></a>시계열 및 딥 러닝 모델

자동화된 ML의 딥 러닝을 사용하여 단변량 및 다변량 시계열 데이터를 예측할 수 있습니다.

딥 러닝 모델에는 다음과 같은 세 가지 기능이 내장되어 있습니다.
1. 입력에서 출력으로의 임의 매핑에서 학습할 수 있습니다.
1. 여러 입력 및 출력을 지원합니다.
1. 긴 시퀀스에 걸쳐 있는 입력 데이터의 패턴을 자동으로 추출할 수 있습니다.

더 큰 데이터가 제공될 경우 Microsoft의 ForecastTCN과 같은 딥 러닝 모델을 통해 결과 모델의 점수를 향상할 수 있습니다. [딥 러닝 실험 구성](#configure-a-dnn-enable-forecasting-experiment) 방법을 알아봅니다.

자동화된 ML은 네이티브 시계열 및 딥 러닝 모델을 권장 사항 시스템의 일부로 제공합니다. 

모델| Description | 이점
----|----|---
Prophet(미리 보기)|Prophet은 강력한 계절적 효과와 여러 계절의 기록 데이터를 포함하는 시계열에서 가장 잘 작동합니다. 이 모델을 활용 하려면를 사용 하 여 로컬에 설치 `pip install fbprophet` 합니다. | 시계열의 이상값, 누락된 데이터 및 획기적인 변경에 정확하고 빠르고 강력하게 작동합니다.
Auto-ARIMA(미리 보기)|ARIMA(자동 회귀 통합 이동 평균)는 데이터가 고정되어 있을 때 가장 잘 작동합니다. 즉, 평균 및 편차와 같은 통계 속성이 전체 세트에서 일정하게 유지됩니다. 예를 들어, 동전을 던질 때 앞면이 나올 확률은 오늘 던지든, 내일 던지든 또는 내년에 던지든 항상 50%입니다.| 과거의 값을 사용하여 미래의 값을 예측하므로 단변량 계열에 적합합니다.
ForecastTCN(미리 보기)| ForecastTCN은 가장 까다로운 예측 태스크를 처리하고 데이터의 비선형 로컬 및 전역 추세 뿐만 아니라 시계열 간 관계를 캡처하도록 디자인된 신경망 모델입니다.|데이터의 복잡한 추세를 활용하고 가장 큰 데이터 세트로 쉽게 확장할 수 있습니다.

## <a name="prerequisites"></a>사전 요구 사항

* Azure Machine Learning 작업 영역 작업 영역을 만들려면 [Azure Machine Learning 작업 영역 만들기](how-to-manage-workspace.md)를 참조하세요.
* 이 문서에서는 사용자에게 자동화된 기계 학습 실험을 설정하는 방법에 대한 기본 지식이 있다고 가정합니다. [자습서](tutorial-auto-train-models.md) 또는 [방법](how-to-configure-auto-train.md)에 따라 기본적인 자동화된 기계 학습 실험 디자인 패턴을 확인합니다.

## <a name="preparing-data"></a>데이터 준비

자동화된 기계 학습 내의 예측 회귀 태스크 유형과 회귀 태스크 유형 간의 가장 중요한 차이점은 유효한 시계열을 나타내는 기능을 데이터에 포함하는 것입니다. 정규 시계열에는 잘 정의되고 일관된 빈도가 있으며 연속 시간 범위의 모든 샘플 요소에 값이 있습니다. `sample.csv` 파일의 다음 스냅샷을 살펴보세요.

```output
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
```

이 데이터 세트는 두 개의 서로 다른 매장, A와 B가 있는 회사의 일별 판매 데이터를 포함하는 간단한 예제입니다. 또한 모델에서 주간 계절성을 검색할 수 있도록 하는 `week_of_year`에 대한 기능이 제공됩니다. 필드 `day_datetime`은 일별 빈도가 있는 명확한 시계열을 나타내고, `sales_quantity` 필드는 예측을 실행하기 위한 대상 열입니다. Pandas 데이터 프레임으로 데이터를 읽어온 다음, `to_datetime` 함수를 사용하여 시계열이 `datetime` 형식인지 확인합니다.

```python
import pandas as pd
data = pd.read_csv("sample.csv")
data["day_datetime"] = pd.to_datetime(data["day_datetime"])
```

이 경우 데이터는 이미 시간 필드 `day_datetime`의 오름차순으로 정렬됩니다. 그러나 실험을 설정하는 경우 올바른 시계열을 작성하기 위해 원하는 시간 열이 오름차순으로 정렬되어 있는지 확인합니다. 데이터에 1,000개의 레코드가 포함되어 있다고 가정하고 데이터를 명확하게 분할하여 학습 및 테스트 데이터 세트를 만듭니다. 레이블 열 이름을 확인하고 레이블로 설정합니다. 이 예제에서는 레이블이 `sales_quantity`가 됩니다. 그런 다음, `test_data`의 레이블 필드를 분리하여 `test_target` 세트를 형성합니다.

```python
train_data = data.iloc[:950]
test_data = data.iloc[-50:]

label =  "sales_quantity"
 
test_labels = test_data.pop(label).values
```

> [!NOTE]
> 미래 가치를 예측하기 위해 모델을 학습하는 경우 학습에 사용된 모든 기능을 의도한 구간에 대해 예측을 실행할 때도 사용할 수 있는지 확인합니다. 예를 들어, 수요 예측을 만들 때 현재 주가에 대한 기능을 포함하면 학습 정확도가 크게 높아질 수 있습니다. 그러나 장기 구간을 사용하여 예측하려는 경우 미래 시계열 요소에 해당하는 선물 주가를 정확하게 예측할 수 없으며 모델 정확성이 떨어질 수 있습니다.

<a name="config"></a>

## <a name="train-and-validation-data"></a>데이터 학습 및 유효성 검사
별도의 학습 및 유효성 검사 세트를 `AutoMLConfig` 생성자에서 직접 지정할 수 있습니다.

### <a name="rolling-origin-cross-validation"></a>이동 원본 교차 유효성 검사
ROCV(시계열 예측 이동 원본 유효성 검사)는 일시적으로 일관된 방식으로 시계열을 분할하는 데 사용됩니다. ROCV는 원본 시간 요소를 사용하여 계열을 학습 및 유효성 검사 데이터로 나눕니다. 원본을 시간에 따라 슬라이딩하여 교차 유효성 검사 겹을 생성합니다.  

![대체 텍스트](./media/how-to-auto-train-forecast/ROCV.svg)

이 전략은 시계열 데이터의 무결성을 유지하고 데이터 누출의 위험을 제거합니다. ROCV는 학습 및 유효성 검사 데이터를 함께 전달하고 `n_cross_validations`를 사용하여 교차 유효성 검사 겹 수를 설정함으로써 예측 작업에 자동으로 사용됩니다. 자동 ML이 교차 유효성 검사를 적용하여 [과잉 맞춤 모델을 방지](concept-manage-ml-pitfalls.md#prevent-over-fitting)하는 방법을 알아보세요.

```python
automl_config = AutoMLConfig(task='forecasting',
                             n_cross_validations=3,
                             ...
                             **time_series_settings)
```
[AutoMLConfig](#configure-and-run-experiment)에 대해 자세히 알아보세요.

## <a name="configure-and-run-experiment"></a>실험 구성 및 실행

예측 작업의 경우 자동화된 기계 학습은 시계열 데이터와 관련된 전처리 및 예측 단계를 사용합니다. 다음 전처리 단계가 실행됩니다.

* 시계열 샘플 빈도(예: 매시간, 매일, 매주)를 검색하고 없는 시간 요소의 새 레코드를 만들어 계열이 연속되도록 합니다.
* 누락된 값을 대상(전방 채우기를 통해) 및 기능 열(중앙값 열 값 사용)에 귀속
* 여러 계열에서 고정 효과를 사용하도록 설정하는 조직 기반 기능 만들기
* 계절적 패턴을 학습하는 데 도움이 되는 시간 기반 기능 만들기
* 범주 변수를 숫자 수량으로 인코딩

[`AutoMLConfig`](https://docs.microsoft.com/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig?view=azure-ml-py) 개체는 자동화된 기계 학습 태스크에 필요한 설정 및 데이터를 정의합니다. 회귀 문제와 마찬가지로 태스크 유형, 반복 횟수, 학습 데이터 및 교차 유효성 검사 수와 같은 표준 학습 매개 변수를 정의합니다. 예측 작업의 경우 실험에 영향을 주는 추가 매개 변수를 설정해야 합니다. 다음 표에서는 각 매개 변수와 사용법에 대해 설명합니다.

| 매개 변수&nbsp;이름 | Description | 필수 |
|-------|-------|-------|
|`time_column_name`|시계열을 작성하고 해당 빈도를 유추하는 데 사용되는 날짜/시간 열을 입력 데이터에 지정하는 데 사용됩니다.|✓|
|`grain_column_names`|입력 데이터에서 개별 계열 그룹을 정의하는 이름입니다. 조직을 정의하지 않으면 데이터 세트는 하나의 시계열로 간주됩니다.||
|`max_horizon`|원하는 최대 예측 구간을 시계열 빈도 단위로 정의합니다. 단위는 예측자가 예측해야 하는 학습 데이터의 시간 간격(예: 매월, 매주)을 기준으로 합니다.|✓|
|`target_lags`|데이터의 빈도에 따라 대상 값을 지연시킬 행 수입니다. 지연은 목록 또는 단일 정수로 표시됩니다. 지연은 독립 변수와 종속 변수 간 관계가 일치하지 않거나 기본적으로 상관 관계가 없는 경우에 사용해야 합니다. 예를 들어 제품에 대한 수요를 예측하려고 할 때 특정 월의 수요는 3개월 전 특정 상품 가격에 따라 달라질 수 있습니다. 이 예에서는 모델이 올바른 관계를 학습하도록 대상(수요)을 -3개월 지연시킬 수 있습니다.||
|`target_rolling_window_size`|예측 값(학습 세트 크기 이하)을 생성하는 데 사용할 *n*개 기록 기간입니다. 생략하면 *n*은 전체 학습 세트 크기입니다. 모델을 학습시킬 때 특정한 양의 기록만 고려하려는 경우 이 매개 변수를 지정합니다.||
|`enable_dnn`|예측 DNN을 사용하도록 설정합니다.||

자세한 내용은 [참조 설명서](/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig)를 참조하세요.

시계열 설정을 사전 개체로 만듭니다. `time_column_name`을 데이터 세트의 `day_datetime` 필드로 설정합니다. `grain_column_names` 매개 변수를 정의하여 데이터에 대해 매장 A 및 B에 대해 각각 하나씩 **두 개의 개별 시계열 그룹**이 생성되도록 합니다. 마지막으로 전체 테스트 세트를 예측하기 위해 `max_horizon`을 50으로 설정합니다. `target_rolling_window_size`를 사용하여 예측 기간을 10개로 설정하고 `target_lags` 매개 변수를 사용하여 두 기간의 대상 값 앞에 단일 지연을 지정합니다. `max_horizon`, `target_rolling_window_size` 및 `target_lags`을 "auto"로 설정하여 이러한 값을 자동으로 검색하도록 하는 것이 좋습니다. 아래 예제에서는 이러한 매개 변수에 "auto" 설정을 사용했습니다. 

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

AutoML은 위의 코드 조각에서 `grain_column_names`를 정의하여 다중 시계열이라고도 하는 두 개의 개별 시계열 그룹을 만듭니다. 조직을 정의하지 않으면 AutoML은 데이터 세트가 단일 시계열이라고 가정합니다. 단일 시계열에 대한 자세한 내용은 [energy_demand_notebook](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning/forecasting-energy-demand)을 참조하세요.

이제 표준 `AutoMLConfig` 개체를 만들고 `forecasting` 태스크 유형을 지정한 후 실험을 제출합니다. 모델이 완료된 후 최상의 실행 반복을 검색합니다.

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

다음을 포함하는 고급 예측 구성의 자세한 코드 예제는 [예측 샘플 Notebook](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning)을 참조하세요.

* [휴일 검색 및 기능화](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-bike-share/auto-ml-forecasting-bike-share.ipynb)
* [이동 원본 교차 유효성 검사](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-energy-demand/auto-ml-forecasting-energy-demand.ipynb)
* [구성 가능한 지연](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-bike-share/auto-ml-forecasting-bike-share.ipynb)
* [롤링 기간 집계 기능](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-energy-demand/auto-ml-forecasting-energy-demand.ipynb)
* [DNN](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-beer-remote/auto-ml-forecasting-beer-remote.ipynb)

### <a name="configure-a-dnn-enable-forecasting-experiment"></a>DNN 지원 예측 실험 구성

> [!NOTE]
> 자동화 기게 학습에서 DNN의 예측 지원은 미리 보기로 제공되며 로컬로 실행할 수 없습니다.

예측을 위해 DNN을 활용하려면 AutoMLConfig의 `enable_dnn` 매개 변수를 true로 설정해야 합니다. 

```python
automl_config = AutoMLConfig(task='forecasting',
                             enable_dnn=True,
                             ...
                             **time_series_settings)
```
[AutoMLConfig](#configure-and-run-experiment)에 대해 자세히 알아보세요.

또는 스튜디오에서 `Enable deep learning` 옵션을 선택할 수 있습니다.
![대체 텍스트](./media/how-to-auto-train-forecast/enable_dnn.png)

GPU SKU와 두 개 이상의 노드를 컴퓨팅 대상으로 사용하는 AML 컴퓨팅 클러스터를 사용하는 것이 좋습니다. DNN 학습이 완료될 때까지 충분한 시간을 허용하려면 실험 시간 제한을 최소 몇 시간으로 설정하는 것이 좋습니다.
AML 컴퓨팅 및 GPU를 포함하는 VM 크기에 대한 자세한 내용은 [AML 컴퓨팅 설명서](how-to-set-up-training-targets.md#amlcompute) 및 [GPU 최적화 가상 머신 크기 설명서](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-gpu)를 참조하세요.

DNN을 활용하는 자세한 코드 예제는 [음료 생산 예측 Notebook](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-beer-remote/auto-ml-forecasting-beer-remote.ipynb)을 참조하세요.

### <a name="target-rolling-window-aggregation"></a>대상 이동 기간 집계
종종 예측자가 보유할 수 있는 최상의 정보는 대상의 최신 값입니다. 대상의 누적 통계를 만들면 예측의 정확도가 높아질 수 있습니다. 대상 이동 기간 집계를 사용하면 데이터 값의 이동 집계를 기능으로 추가할 수 있습니다. 대상 이동 기간을 사용하도록 지정하려면 `target_rolling_window_size`를 원하는 정수 기간 크기로 설정합니다. 

에너지 수요를 예측하는 경우 이에 대한 예제를 볼 수 있습니다. 3일의 이동 기간 기능을 추가하여 가열된 공간의 열 변화를 계산할 수 있습니다. 아래 예제에서는 `AutoMLConfig` 생성자에서 `target_rolling_window_size=3`을 설정하여 크기가 3인 기간을 만들었습니다. 이 표에서는 기간 집계가 적용될 때 발생하는 기능 엔지니어링을 보여 줍니다. 최솟값, 최댓값 및 합계에 대한 열은 정의된 설정에 따라 3의 슬라이딩 윈도우에서 생성됩니다. 각 행에는 새 계산 기능이 있습니다. 2017년 9월 8일, 오전 4시의 타임스탬프에서 최댓값, 최솟값 및 합계는 2017년 9월 8일 오전 1시~오전 3시의 수요 값을 사용하여 계산됩니다. 이 3개의 기간은 함께 이동하면서 나머지 행의 데이터를 채웁니다.

![대체 텍스트](./media/how-to-auto-train-forecast/target-roll.svg)

이러한 추가 기능을 생성하고 추가 컨텍스트 데이터로 사용하면 학습 모델의 정확도를 높이는 데 도움이 됩니다.

[대상 이동 기간 집계 기능](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-energy-demand/auto-ml-forecasting-energy-demand.ipynb)을 활용하는 Python 코드 예제를 확인해 보세요.

### <a name="view-feature-engineering-summary"></a>기능 엔지니어링 요약 보기

자동화된 기계 학습의 시계열 태스크 유형은 기능 엔지니어링 프로세스의 세부 정보에서 확인할 수 있습니다. 다음 코드에서는 각 원시 기능과 함께 다음 기능을 보여 줍니다.

* 원시 기능 이름
* 이 원시 기능에서 구성된 엔지니어링된 기능의 수
* 감지된 형식
* 기능이 삭제되었는지 여부
* 원시 기능의 기능 변형 목록

```python
fitted_model.named_steps['timeseriestransformer'].get_featurization_summary()
```

## <a name="forecasting-with-best-model"></a>최적 모델로 예측

최상의 모델 반복을 사용하여 테스트 데이터 세트의 값을 예측합니다.

`forecast()` 함수를 `predict()` 대신 사용해야 합니다. 이 함수를 사용하여 예측이 시작되는 시기를 지정할 수 있습니다. 다음 예제에서는 먼저 `y_pred`의 모든 값을 `NaN`으로 바꿉니다. 이 경우에는 일반적으로 `predict()`를 사용할 때와 마찬가지로 예측 원본이 학습 데이터의 끝에 오게 됩니다. 그러나 `y_pred`의 두 번째 절반을 `NaN`으로 바꾸면 이 함수는 처음 절반의 숫자 값을 수정하지 않은 상태로 유지하지만 두 번째 절반의 `NaN` 값을 예측합니다. 이 함수는 예측된 값과 정렬된 기능을 모두 반환합니다.

`forecast()` 함수에서 `forecast_destination` 매개 변수를 사용하여 지정된 날짜까지의 값을 예측할 수도 있습니다.

```python
label_query = test_labels.copy().astype(np.float)
label_query.fill(np.nan)
label_fcst, data_trans = fitted_pipeline.forecast(
    test_data, label_query, forecast_destination=pd.Timestamp(2019, 1, 8))
```

`actual_labels` 실제 값과 `predict_labels`의 예측 값 사이에서 RMSE(제곱 평균 오차)를 계산합니다.

```python
from sklearn.metrics import mean_squared_error
from math import sqrt

rmse = sqrt(mean_squared_error(actual_labels, predict_labels))
rmse
```

전체적인 모델 정확도를 확인했으므로 가장 현실적인 다음 단계는 모델을 사용하여 알 수 없는 미래 가치를 예측하는 것입니다. 테스트 세트 `test_data`와 형식은 같지만 날짜/시간이 미래인 데이터 세트를 제공하면 결과 예측 세트는 각 시계열 단계에 대해 예측된 값입니다. 데이터 세트의 마지막 시계열 레코드가 2018년 12월 31일에 대한 것이라고 가정합니다. 다음 날(또는 예측해야 하는 `max_horizon` 이하 기간)의 수요를 예측하려면 2019년 1월 1일의 각 매장에 대한 단일 시계열 레코드를 만듭니다.

```output
day_datetime,store,week_of_year
01/01/2019,A,1
01/01/2019,A,1
```

필요한 단계를 반복하여 이 미래 데이터를 데이터 프레임에 로드하고 `best_run.predict(test_data)`를 실행하여 미래 가치를 예측합니다.

> [!NOTE]
> `max_horizon`보다 큰 기간 동안의 값은 예측할 수 없습니다. 현재 구간을 넘어서는 미래 가치를 예측하려면 모델을 더 큰 구간으로 다시 학습해야 합니다.

## <a name="next-steps"></a>다음 단계

* [자습서](tutorial-auto-train-models.md)에 따라 자동화된 기계 학습으로 실험을 만드는 방법을 알아봅니다.
* [Python용 Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) 참조 설명서를 확인합니다.
