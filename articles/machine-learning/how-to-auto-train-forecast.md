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
ms.date: 03/09/2020
ms.openlocfilehash: 05d658c052c5bc12f49d957bb29ad085c269c57b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "82137371"
---
# <a name="auto-train-a-time-series-forecast-model"></a>시계열 예측 모델 자동 학습
[!INCLUDE [aml-applies-to-basic-enterprise-sku](../../includes/aml-applies-to-basic-enterprise-sku.md)]

이 문서에서는 Azure Machine Learning에서 자동화 된 machine learning을 사용 하 여 시계열 예측 회귀 모델을 구성 하 고 학습 하는 방법에 대해 알아봅니다. 

예측 모델을 구성 하는 것은 자동화 된 machine learning을 사용 하 여 표준 회귀 모델을 설정 하는 것과 유사 하지만 시계열 데이터로 작업 하기 위한 특정 구성 옵션 및 전처리 단계가 있습니다. 

예를 들어 예측을 확장 하는 미래 (예측 마감일) 뿐만 아니라 지연 등을 [구성할](#config) 수 있습니다. 자동화 된 ML은 데이터 집합 및 예측 horizons의 모든 항목에 대해 종종 내부적으로 분기 된 단일 모델을 학습 합니다. 따라서 모델 매개 변수를 예측 하는 데 더 많은 데이터를 사용할 수 있으며 보이지 않는 시리즈에 대 한 일반화가 가능 합니다.

다음 예제에서는 다음 방법을 보여 줍니다.

* 시계열 모델링에 대 한 데이터 준비
* [`AutoMLConfig`](/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig) 개체의 특정 시계열 매개 변수 구성
* 시계열 데이터를 사용 하 여 예측 실행

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE2X1GW]

기존 시계열 메서드와 달리 자동화 된 ML의 이전 시계열 값은 다른 예측 변수와 함께 회귀 변수의 추가 차원이 되도록 "피벗" 됩니다. 이 방법은 학습 중에 여러 컨텍스트 변수와 해당 변수 간의 관계를 통합 합니다. 여러 요인이 예측에 영향을 줄 수 있으므로이 방법은 실제 예측 시나리오에 적합 합니다. 예를 들어 판매를 예측 하는 경우 기록 추세의 상호 작용, 환율 및 가격은 모두 공동으로 판매 결과물을 구동 합니다. 

학습 데이터에서 추출 된 기능은 중요 한 역할을 합니다. 그리고 자동화 된 ML은 표준 전처리 단계를 수행 하 고 계절 효과를 캡처하고 예측 정확도를 최대화 하기 위한 추가 시계열 기능을 생성 합니다.

## <a name="time-series-and-deep-learning-models"></a>시계열 및 심층 학습 모델


자동화 된 ML의 심층 학습을 통해 시계열 데이터를 예측 하 고 다차원 데이터를 예측할 수 있습니다.

심층 학습 모델에는 세 가지 내장 기능이 있습니다.
1. 입력에서 출력으로의 임의 매핑에서 학습할 수 있습니다.
1. 여러 입력 및 출력을 지원 합니다.
1. 긴 시퀀스에 걸쳐 있는 입력 데이터의 패턴을 자동으로 추출할 수 있습니다.

더 큰 데이터를 제공 하는 경우 Microsoft의 ForecastTCN 같은 심층 학습 모델을 통해 결과 모델의 점수를 향상할 수 있습니다. [심층 학습을 위해 실험을 구성](#configure-a-dnn-enable-forecasting-experiment)하는 방법을 알아봅니다.

자동화 된 ML은 사용자에 게 기본 시계열 및 심층 학습 모델을 모두 권장 사항 시스템의 일부로 제공 합니다. 


모델| Description | 이점
----|----|---
Prophet (미리 보기)|Prophet는 계절 효과가 강 하 고 기록 데이터가 여러 개 있는 시계열에서 가장 잘 작동 합니다. | 정확한 & 빠르고, 이상 값이 강력 하 고, 데이터가 누락 되 고, 시계열이 대폭 변경 되었습니다.
자동 ARIMA (미리 보기)|ARIMA (자동 회귀 Integrated 이동 평균)는 데이터가 고정 되어 있을 때 가장 잘 수행 됩니다. 즉, 평균과 편차와 같은 통계 속성은 전체 집합에 대해 일정 합니다. 예를 들어 동전을 대칭 이동 하는 경우 오늘, 내일 또는 다음 연도의 대칭 이동 여부와 관계 없이 헤드를 가져오는 확률은 50%입니다.| 이전 값을 사용 하 여 미래 값을 예측 하므로 계열 계열에 적합 합니다.
ForecastTCN (미리 보기)| ForecastTCN는 데이터의 비선형 로컬 및 글로벌 추세 뿐만 아니라 시계열 간의 관계를 캡처하기 위해 가장 까다로운 예측 작업을 처리 하도록 설계 된 신경망 모델입니다.|데이터의 복잡 한 추세를 활용 하 고 데이터 집합의 가장 큰 값으로 쉽게 확장할 수 있습니다.

## <a name="prerequisites"></a>사전 요구 사항

* Azure Machine Learning 작업 영역 작업 영역을 만들려면 [Azure Machine Learning 작업 영역 만들기](how-to-manage-workspace.md)를 참조 하세요.
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

이 데이터 집합은 두 개의 서로 다른 매장 A와 B가 있는 회사의 일일 판매 데이터에 대 한 간단한 예입니다. 또한 모델에서 주간 계절성 `week_of_year` 를 검색할 수 있도록 하는 기능이 있습니다. 필드 `day_datetime` 는 일일 빈도가 있는 명확한 시계열을 나타내며 필드 `sales_quantity` 는 예측을 실행 하기 위한 대상 열입니다. Pandas 데이터 프레임로 데이터를 읽은 다음 `to_datetime` 함수를 사용 하 여 시계열이 `datetime` 형식 인지 확인 합니다.

```python
import pandas as pd
data = pd.read_csv("sample.csv")
data["day_datetime"] = pd.to_datetime(data["day_datetime"])
```

이 경우 데이터는 이미 시간 필드 `day_datetime`를 기준으로 오름차순으로 정렬 됩니다. 그러나 실험을 설정 하는 경우 올바른 시계열을 빌드하기 위해 원하는 시간 열이 오름차순으로 정렬 되어 있는지 확인 합니다. 데이터에 1000 레코드가 포함 되어 있다고 가정 하 고 데이터를 명확 하 게 분할 하 여 학습 및 테스트 데이터 집합을 만듭니다. 레이블 열 이름을 확인 하 고 레이블 열로 설정 합니다. 이 예에서 레이블은이 됩니다 `sales_quantity`. 그런 다음에서 `test_data` 레이블 필드를로 구분 하 `test_target` 여 집합을 구성 합니다.

```python
train_data = data.iloc[:950]
test_data = data.iloc[-50:]

label =  "sales_quantity"
 
test_labels = test_data.pop(label).values
```

> [!NOTE]
> 미래 가치를 예측 하기 위해 모델을 학습 하는 경우 학습에 사용 된 모든 기능을 사용 하 여 원하는 수평에 대해 예측을 실행할 수 있는지 확인 합니다. 예를 들어 수요 예측을 만들 때 현재 주가에 대 한 기능을 포함 하 여 학습 정확도를 대규모 수 있습니다. 그러나 장기적으로 예측 하려는 경우 미래 시계열 요소에 해당 하는 미래 스톡 값을 정확 하 게 예측할 수 없으며 모델 정확성이 떨어질 수 있습니다.

<a name="config"></a>

## <a name="train-and-validation-data"></a>데이터 학습 및 유효성 검사
`AutoMLConfig` 생성자에서 직접 학습 및 유효성 검사 집합을 별도로 지정할 수 있습니다.

### <a name="rolling-origin-cross-validation"></a>롤링 원본 교차 유효성 검사
시계열 예측 롤링 유효성 검사 (ROCV)는 일시적으로 일관 된 방식으로 시계열을 분할 하는 데 사용 됩니다. ROCV는 원본 시간 요소를 사용 하 여 계열을 학습 및 유효성 검사 데이터로 나눕니다. 원본에 시간을 슬라이딩으로 교차 유효성 검사 접기를 생성 합니다.  

![대체 텍스트](./media/how-to-auto-train-forecast/ROCV.svg)

이 전략은 시계열 데이터 무결성을 유지 하 고 데이터 누출의 위험을 제거 합니다. ROCV는 학습 및 유효성 검사 데이터를 함께 전달 하 고를 사용 하 여 `n_cross_validations`교차 유효성 검사 접기 수를 설정 하 여 예측 작업에 자동으로 사용 됩니다. 

```python
automl_config = AutoMLConfig(task='forecasting',
                             n_cross_validations=3,
                             ...
                             **time_series_settings)
```
[AutoMLConfig](#configure-and-run-experiment)에 대해 자세히 알아보세요.

## <a name="configure-and-run-experiment"></a>실험 구성 및 실행

예측 작업의 경우 자동화 된 machine learning은 시계열 데이터와 관련 된 전처리 및 예측 단계를 사용 합니다. 다음 전처리 단계가 실행 됩니다.

* 시간 계열 샘플 빈도 (예: 매시간, 매일, 매주)를 검색 하 고 없는 시간 요소에 대 한 새 레코드를 만들어 계열을 연속 해 서 만듭니다.
* 돌립니다 누락 값 (전방 채우기를 통해) 및 기능 열 (중앙값 열 값 사용)
* 여러 계열에서 고정 효과를 사용 하도록 설정 하는 그레인 기반 기능 만들기
* 계절 패턴을 학습 하는 데 도움이 되는 시간 기반 기능 만들기
* 범주 변수를 숫자 수량으로 인코딩

개체 [`AutoMLConfig`](https://docs.microsoft.com/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig?view=azure-ml-py) 는 자동화 된 machine learning 작업에 필요한 설정 및 데이터를 정의 합니다. 회귀 문제와 마찬가지로 작업 유형, 반복 횟수, 학습 데이터 및 교차 유효성 검사 수와 같은 표준 학습 매개 변수를 정의 합니다. 예측 작업의 경우 실험에 영향을 주는 추가 매개 변수를 설정 해야 합니다. 다음 표에서는 각 매개 변수 및 사용법을 설명 합니다.

| 매개&nbsp;변수 이름 | Description | 필수 |
|-------|-------|-------|
|`time_column_name`|시계열을 작성 하 고 해당 빈도를 유추 하는 데 사용 되는 입력 데이터의 datetime 열을 지정 하는 데 사용 됩니다.|✓|
|`grain_column_names`|입력 데이터에서 개별 계열 그룹을 정의 하는 이름입니다. 그레인을 정의 하지 않으면 데이터 집합은 하나의 시계열으로 간주 됩니다.||
|`max_horizon`|시계열 빈도 단위로 원하는 최대 예측 구간을 정의 합니다. 단위는 forecaster에서 예측 해야 하는 매월, 매주 학습 데이터의 시간 간격을 기준으로 합니다.|✓|
|`target_lags`|데이터의 빈도에 따라 대상 값의 지연에 사용 되는 행 수입니다. 지연 시간은 목록 또는 단일 정수로 표시 됩니다. 독립 변수와 종속 변수 간의 관계가 기본적으로 일치 하거나 상관 관계를 지정 하지 않는 경우에는 Lag를 사용 해야 합니다. 예를 들어, 제품 수요를 예측 하려는 경우 매월 수요는 특정 상품 3 개월 전에 가격에 따라 달라질 수 있습니다. 이 예에서는 모델이 올바른 관계에 대해 학습 하도록 대상 (수요)을 3 개월로 지연 시킬 수 있습니다.||
|`target_rolling_window_size`|예측 값을 생성 하는 데 사용할 수 *있는 기록 기간* <= 학습 집합 크기입니다. 생략 하는 경우 *n* 은 전체 학습 집합 크기입니다. 모델을 학습할 때 특정 분량의 기록만 고려 하려는 경우이 매개 변수를 지정 합니다.||
|`enable_dnn`|DNNs 예측을 사용 하도록 설정 합니다.||

자세한 내용은 [참조 설명서](/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig) 를 참조 하세요.

시간 계열 설정을 사전 개체로 만듭니다. 을 `time_column_name` 데이터 집합의 `day_datetime` 필드로 설정 합니다. `grain_column_names` 매개 변수를 정의 하 여 데이터에 대해 **두 개의 개별 시계열 그룹이** 생성 되도록 합니다. 하나는 매장 A 및 B입니다. 마지막으로 전체 `max_horizon` 테스트 집합에 대해 예측 하려면를 50로 설정 합니다. 를 사용 `target_rolling_window_size`하 여 예측 기간을 10 개로 설정 하 고 `target_lags` 매개 변수를 사용 하 여 두 기간의 대상 값에 대 한 단일 지연을 지정 합니다. 을 `target_rolling_window_size` 설정 하 고 `target_lags` 자동 `max_horizon`으로 이러한 값을 검색 하는 "자동"으로 설정 하는 것이 좋습니다. 아래 예제에서는 이러한 매개 변수에 "auto" 설정을 사용 했습니다. 

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

위의 코드 조각 `grain_column_names` 에서를 정의 하 여 automl은 여러 시계열이 라고도 하는 두 개의 개별 시계열 그룹을 만듭니다. 그레인을 정의 하지 않으면 AutoML은 데이터 집합이 단일 시계열 이라고 가정 합니다. 단일 시계열에 대 한 자세한 내용은 [energy_demand_notebook](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning/forecasting-energy-demand)를 참조 하세요.

이제 `forecasting` 작업 유형을 지정 `AutoMLConfig` 하 고 실험을 제출 하는 표준 개체를 만듭니다. 모델이 완료 된 후 최상의 실행 반복을 검색 합니다.

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

다음을 포함 하 여 고급 예측 구성의 자세한 코드 예제는 [예측 샘플 노트북](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning) 을 참조 하세요.

* [휴일 검색 및 기능화](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-bike-share/auto-ml-forecasting-bike-share.ipynb)
* [롤링 원본 교차 유효성 검사](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-energy-demand/auto-ml-forecasting-energy-demand.ipynb)
* [구성 가능한 지연](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-bike-share/auto-ml-forecasting-bike-share.ipynb)
* [창 롤링 집계 기능](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-energy-demand/auto-ml-forecasting-energy-demand.ipynb)
* [DNN](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-beer-remote/auto-ml-forecasting-beer-remote.ipynb)

### <a name="configure-a-dnn-enable-forecasting-experiment"></a>DNN 구성 예측 실험 사용

> [!NOTE]
> 자동화 Machine Learning의 예측에 대 한 DNN 지원은 미리 보기 상태 이며 로컬 실행을 지원 하지 않습니다.

예측을 위해 DNNs를 활용 하려면 AutoMLConfig의 `enable_dnn` 매개 변수를 true로 설정 해야 합니다. 

```python
automl_config = AutoMLConfig(task='forecasting',
                             enable_dnn=True,
                             ...
                             **time_series_settings)
```
[AutoMLConfig](#configure-and-run-experiment)에 대해 자세히 알아보세요.

또는 스튜디오에서 `Enable deep learning` 옵션을 선택할 수 있습니다.
![대체 텍스트](./media/how-to-auto-train-forecast/enable_dnn.png)

GPU Sku와 최소 두 개 이상의 노드를 계산 대상으로 사용 하는 AML 계산 클러스터를 사용 하는 것이 좋습니다. DNN 교육이 완료 될 때까지 충분 한 시간을 허용 하려면 실험 시간 제한을 최소 몇 시간으로 설정 하는 것이 좋습니다.
AML 계산 및 GPU를 포함 하는 VM 크기에 대 한 자세한 내용은 [Aml 계산 설명서](how-to-set-up-training-targets.md#amlcompute) 및 [gpu 최적화 가상 머신 크기 설명서](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-gpu)를 참조 하세요.

DNNs를 활용 하는 자세한 코드 예제는 [음료 프로덕션 예측 노트북](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-beer-remote/auto-ml-forecasting-beer-remote.ipynb) 을 확인 하세요.

### <a name="target-rolling-window-aggregation"></a>대상 롤링 창 집계
Forecaster에서 사용할 수 있는 가장 좋은 정보는 대상의 최신 값입니다. 대상의 누적 통계를 만들면 예측의 정확도가 증가할 수 있습니다. 대상 롤링 창 집계를 사용 하면 데이터 값의 롤링 집계를 기능으로 추가할 수 있습니다. 대상 롤링 windows를 사용 하도록 설정 `target_rolling_window_size` 하려면를 원하는 정수 창 크기로 설정 합니다. 

에너지 수요를 예측 하는 경우이에 대 한 예를 볼 수 있습니다. 3 일의 이동 창 기능을 추가 하 여 열이 아닌 공간의 열 변경에 대 한 고려를 할 수 있습니다. 아래 예제에서는 `target_rolling_window_size=3` `AutoMLConfig` 생성자에서을 설정 하 여이 창 크기를 3으로 만들었습니다. 표는 창 집계가 적용 될 때 발생 하는 기능 엔지니어링을 보여 줍니다. 최소값, 최대값 및 합계에 대 한 열은 정의 된 설정에 따라 3의 슬라이딩 윈도우에서 생성 됩니다. 각 행에는 새로운 계산 된 기능이 있습니다 .이 기능은 9 월 8 일에 대 한 타임 스탬프의 경우 2017 4:00am의 경우 최대, 최소 및 합계 값은 9 월 8 일 2017 1:00AM-3:00AM의 수요 값을 사용 하 여 계산 됩니다. 이 창에는 나머지 행에 대 한 데이터 채우기가 함께 이동 됩니다.

![대체 텍스트](./media/how-to-auto-train-forecast/target-roll.svg)

추가 컨텍스트 데이터로 이러한 추가 기능을 생성 하 고 사용 하면 학습 모델의 정확도를 높일 수 있습니다.

[대상 롤링 창 집계 기능](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-energy-demand/auto-ml-forecasting-energy-demand.ipynb)을 활용 하는 Python 코드 예제를 봅니다.

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

대신 `forecast()` 함수를 사용 해야 합니다. `predict()`이 함수를 사용 하면 예측이 시작 되는 시기를 지정할 수 있습니다. 다음 예제에서는 먼저의 `y_pred` 모든 값을로 `NaN`바꿉니다. 이 경우 일반적으로를 사용 하 `predict()`는 것 처럼 예측 원본은 학습 데이터의 끝에 있습니다. 그러나의 `y_pred` 두 번째 반만로 `NaN`바꾼 경우 함수는 처음 절반의 숫자 값을 수정 되지 않은 상태로 두고 두 번째 절반의 값을 `NaN` 예측 합니다. 함수는 예측 된 값과 정렬 된 기능을 모두 반환 합니다.

또한 `forecast_destination` `forecast()` 함수에서 매개 변수를 사용 하 여 지정 된 날짜까지 값을 예측할 수 있습니다.

```python
label_query = test_labels.copy().astype(np.float)
label_query.fill(np.nan)
label_fcst, data_trans = fitted_pipeline.forecast(
    test_data, label_query, forecast_destination=pd.Timestamp(2019, 1, 8))
```

`actual_labels` 실제 값과의 `predict_labels`예측 값 사이에 rmse (제곱 평균 제곱 오차)를 계산 합니다.

```python
from sklearn.metrics import mean_squared_error
from math import sqrt

rmse = sqrt(mean_squared_error(actual_labels, predict_labels))
rmse
```

전체적인 모델 정확도를 확인 했으므로 가장 현실적인 다음 단계는 모델을 사용 하 여 알 수 없는 미래 값을 예측 하는 것입니다. 테스트 집합과 `test_data` 동일한 형식으로 데이터 집합을 제공 하지만 미래 날짜/시간을 사용 하 여 결과 예측 집합은 각 시계열 단계에 대 한 예측 값입니다. 데이터 집합의 마지막 시계열 레코드가 12/31/2018에 대 한 것으로 가정 합니다. 다음 날에 대 한 수요를 예측 하려면 (또는 예측 해야 하는 여러 기간 <= `max_horizon`) 01/01/2019에 대 한 각 매장에 대해 단일 시계열 레코드를 만듭니다.

    day_datetime,store,week_of_year
    01/01/2019,A,1
    01/01/2019,A,1

필요한 단계를 반복 하 여이 미래 데이터를 데이터 프레임에 로드 한 다음 `best_run.predict(test_data)` 를 실행 하 여 미래 값을 예측 합니다.

> [!NOTE]
> 보다 큰 기간 수에 대해서는 `max_horizon`값을 예측할 수 없습니다. 현재 마감일을 초과 하는 미래 값을 예측 하려면 모델을 더 큰 수평선으로 다시 학습 해야 합니다.

## <a name="next-steps"></a>다음 단계

* 자동화 된 machine learning을 사용 하 여 실험을 만드는 방법을 알아보려면 [자습서](tutorial-auto-train-models.md) 를 따르세요.
* [AZURE MACHINE LEARNING SDK For Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) 참조 설명서를 확인 하세요.
