---
title: 모델에 대한 하이퍼 매개 변수 튜닝
titleSuffix: Azure Machine Learning
description: Azure Machine Learning를 사용 하 여 심층 학습 및 기계 학습 모델에 대 한 하이퍼 매개 변수를 효율적으로 조정 합니다.
ms.author: swatig
author: swatig007
ms.reviewer: sgilley
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.date: 03/30/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python, contperfq1
ms.openlocfilehash: b65553edf61d8e646e7238a5385656fe75a3156f
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/04/2020
ms.locfileid: "93311375"
---
# <a name="tune-hyperparameters-for-your-model-with-azure-machine-learning"></a>Azure Machine Learning을 사용하여 모델에 대한 하이퍼 매개 변수 튜닝


Azure Machine Learning [하이퍼 매개 변수 패키지](/python/api/azureml-train-core/azureml.train.hyperdrive?preserve-view=true&view=azure-ml-py)를 사용 하 여 효율적인 하이퍼 매개 변수 튜닝을 자동화 합니다. [AZURE MACHINE LEARNING SDK](/python/api/overview/azure/ml/?preserve-view=true&view=azure-ml-py)를 사용 하 여 하이퍼 매개 변수를 조정 하는 데 필요한 단계를 완료 하는 방법을 알아봅니다.

1. 매개 변수 검색 공간 정의
1. 최적화할 기본 메트릭 지정  
1. 낮은 실행 실행에 대 한 조기 종료 정책 지정
1. 리소스 할당
1. 정의 된 구성을 사용 하 여 실험 시작
1. 교육 실행 시각화
1. 모델에 가장 적합 한 구성 선택

## <a name="what-are-hyperparameters"></a>하이퍼 매개 변수란?

하이퍼 **매개 변수** 는 모델 학습 프로세스를 제어할 수 있는 조정 가능한 매개 변수입니다. 예를 들어 신경망을 사용 하 여 숨겨진 계층 수와 각 계층의 노드 수를 결정 합니다. 모델 성능은 하이퍼 매개 변수에 따라 크게 달라 집니다.

 하이퍼 **매개 변수 튜닝** 은 최상의 성능을 생성 하는 하이퍼 매개 변수의 구성을 찾는 프로세스입니다. 프로세스는 일반적으로 계산 비용이 많이 들고 수동입니다.

Azure Machine Learning를 사용 하 여 하이퍼 매개 변수 튜닝을 자동화 하 고 병렬로 실험을 실행 하 여 하이퍼 매개 변수를 효율적으로 최적화할 수 있습니다


## <a name="define-the-search-space"></a>검색 공간 정의

각 하이퍼 매개 변수에 대해 정의 된 값의 범위를 탐색 하 여 하이퍼 매개 변수를 튜닝 합니다.

하이퍼 매개 변수는 불연속 또는 연속 일 수 있으며 [매개 변수 식](/python/api/azureml-train-core/azureml.train.hyperdrive.parameter_expressions?preserve-view=true&view=azure-ml-py)에서 설명 하는 값의 분포를 가집니다.

### <a name="discrete-hyperparameters"></a>개별 하이퍼 매개 변수 

개별 하이퍼 매개 변수는 불연속 값 중 `choice`로 지정됩니다. `choice`는 다음이 될 수 있습니다.

* 하나 이상의 쉼표로 구분된 값
* `range` 개체
* 임의의 `list` 개체


```Python
    {
        "batch_size": choice(16, 32, 64, 128)
        "number_of_hidden_layers": choice(range(1,5))
    }
```

이 경우 `batch_size` 값 [16, 32, 64, 128] 중 하나는 `number_of_hidden_layers` [1, 2, 3, 4] 값 중 하나를 사용 합니다.

분포를 사용 하 여 다음과 같은 고급 불연속 하이퍼 매개 변수를 지정할 수도 있습니다.

* `quniform(low, high, q)` - 라운드와 같은 값 반환(uniform(low, high) / q) * q
* `qloguniform(low, high, q)` - 라운드와 같은 값 반환(exp(uniform(low, high)) / q) * q
* `qnormal(mu, sigma, q)` - 라운드와 같은 값 반환(normal(mu, sigma) / q) * q
* `qlognormal(mu, sigma, q)` - 라운드와 같은 값 반환(exp(normal(mu, sigma)) / q) * q

### <a name="continuous-hyperparameters"></a>연속 하이퍼 매개 변수 

연속 하이퍼 매개 변수 변수는 연속 값 범위에 대 한 분포로 지정 됩니다.

* `uniform(low, high)` - low와 high 간에 균일하게 배포되는 값 반환
* `loguniform(low, high)` - 반환 값의 로그가 균일하게 배포되도록 exp(uniform(low, high))에 따라 그려진 값 반환
* `normal(mu, sigma)` - 평균 mu 및 표준 편차 sigma를 사용하여 일반적으로 배포되는 실제 값 반환
* `lognormal(mu, sigma)` - 반환 값의 로그가 균일하게 배포되도록 exp(normal(mu, sigma))에 따라 그려진 값 반환

매개 변수 공간 정의의 예제는 다음과 같습니다.

```Python
    {    
        "learning_rate": normal(10, 3),
        "keep_probability": uniform(0.05, 0.1)
    }
```

이 코드는 `learning_rate` 및 `keep_probability`라는 두 개의 매개 변수를 사용하여 검색 공간을 정의합니다. `learning_rate`는 평균 값 10과 표준 편차 3이 있는 정규 분포를 갖게 됩니다. `keep_probability`는 최솟값 0.05 및 최댓값 0.1이 있는 균일한 분포를 갖게 됩니다.

### <a name="sampling-the-hyperparameter-space"></a>하이퍼 매개 변수 공간 샘플링

하이퍼 매개 변수 공간에서 사용할 매개 변수 샘플링 방법을 지정 합니다. Azure Machine Learning은 다음 메서드를 지원 합니다.

* 무작위 샘플링
* 그리드 샘플링
* Bayesian 샘플링

#### <a name="random-sampling"></a>무작위 샘플링

[무작위 샘플링](/python/api/azureml-train-core/azureml.train.hyperdrive.randomparametersampling?preserve-view=true&view=azure-ml-py) 은 불연속 및 연속 hyperparameters 변수를 지원 합니다. 낮은 성능 실행의 조기 종료를 지원 합니다. 일부 사용자는 무작위 샘플링을 사용 하 여 초기 검색을 수행한 다음 검색 공간을 구체화 하 여 결과를 향상 시킵니다.

무작위 샘플링에서 하이퍼 매개 변수 값은 정의된 검색 공간에서 임의로 선택됩니다. 

```Python
from azureml.train.hyperdrive import RandomParameterSampling
from azureml.train.hyperdrive import normal, uniform, choice
param_sampling = RandomParameterSampling( {
        "learning_rate": normal(10, 3),
        "keep_probability": uniform(0.05, 0.1),
        "batch_size": choice(16, 32, 64, 128)
    }
)
```

#### <a name="grid-sampling"></a>그리드 샘플링

[그리드 샘플링](/python/api/azureml-train-core/azureml.train.hyperdrive.gridparametersampling?preserve-view=true&view=azure-ml-py) 은 불연속 하이퍼 매개 변수를 지원 합니다. 검색 공간을 철저 하 게 검색할 수 있는 경우 그리드 샘플링을 사용 합니다. 낮은 성능 실행의 조기 종료를 지원 합니다.

가능한 모든 값에 대해 간단한 그리드 검색을 수행 합니다. 그리드 샘플링은 하이퍼 매개 변수와 함께 사용할 수 있습니다 `choice` . 예를 들어 다음 공간에는 6 개의 샘플이 있습니다.

```Python
from azureml.train.hyperdrive import GridParameterSampling
from azureml.train.hyperdrive import choice
param_sampling = GridParameterSampling( {
        "num_hidden_layers": choice(1, 2, 3),
        "batch_size": choice(16, 32)
    }
)
```

#### <a name="bayesian-sampling"></a>Bayesian 샘플링

[Bayesian 샘플링](/python/api/azureml-train-core/azureml.train.hyperdrive.bayesianparametersampling?preserve-view=true&view=azure-ml-py) 은 Bayesian 최적화 알고리즘을 기반으로 합니다. 이전 샘플이 수행 된 방법에 따라 샘플을 선택 하 여 새 샘플에서 기본 메트릭을 향상 시킵니다.

하이퍼 매개 변수 공간을 탐색 하는 데 충분 한 예산이 있는 경우 Bayesian 샘플링을 권장 합니다. 최상의 결과를 얻으려면 튜닝 되는 하이퍼 매개 변수 수의 20 배 이상으로 최대 실행 횟수를 권장 합니다. 

동시 실행 수는 튜닝 프로세스의 효율성에 영향을 줍니다. 병렬 처리 수준이 작을수록 이전에 완료 된 실행의 이점을 활용 하는 실행 횟수가 늘어남에 따라 더 적은 수의 동시 실행으로 인해 샘플링 수렴이 향상 될 수 있습니다.

Bayesian 샘플링은 `choice` `uniform` 검색 공간에 대 한, 및 배포만 지원 `quniform` 합니다.

```Python
from azureml.train.hyperdrive import BayesianParameterSampling
from azureml.train.hyperdrive import uniform, choice
param_sampling = BayesianParameterSampling( {
        "learning_rate": uniform(0.05, 0.1),
        "batch_size": choice(16, 32, 64, 128)
    }
)
```



## <a name="specify-primary-metric"></a><a name="specify-primary-metric-to-optimize"></a> 기본 메트릭 지정

하이퍼 매개 변수 튜닝을 최적화할 [기본 메트릭을](/python/api/azureml-train-core/azureml.train.hyperdrive.primarymetricgoal?preserve-view=true&view=azure-ml-py) 지정 합니다. 각 교육 실행은 기본 메트릭에 대해 평가됩니다. 초기 종료 정책은 기본 메트릭을 사용 하 여 낮은 성능 실행을 식별 합니다.

기본 메트릭에 대해 다음 특성을 지정 합니다.

* `primary_metric_name`: 기본 메트릭의 이름은 학습 스크립트에 의해 기록 된 메트릭의 이름과 정확 하 게 일치 해야 합니다.
* `primary_metric_goal`: `PrimaryMetricGoal.MAXIMIZE` 또는 `PrimaryMetricGoal.MINIMIZE`일 수 있으며 실행을 평가할 때 기본 메트릭을 최대화 또는 최소화할지 여부를 결정합니다. 

```Python
primary_metric_name="accuracy",
primary_metric_goal=PrimaryMetricGoal.MAXIMIZE
```

이 샘플에서는 "정확도"를 최대화 합니다.

### <a name="log-metrics-for-hyperparameter-tuning"></a><a name="log-metrics-for-hyperparameter-tuning"></a>하이퍼 매개 변수 튜닝에 대한 메트릭 기록

모델에 대 한 학습 스크립트는 HyperDrive에서 하이퍼 매개 변수 튜닝을 위해 액세스할 수 있도록 모델 학습 중에 기본 메트릭을 기록해 **야 합니다** .

다음 샘플 코드 조각을 사용 하 여 학습 스크립트에서 기본 메트릭을 기록 합니다.

```Python
from azureml.core.run import Run
run_logger = Run.get_context()
run_logger.log("accuracy", float(val_accuracy))
```

학습 스크립트는를 계산 `val_accuracy` 하 고이를 기본 메트릭 "정확도"로 기록 합니다. 메트릭이 기록 될 때마다 하이퍼 매개 변수 튜닝 서비스에서이를 수신 합니다. 보고 빈도를 결정 하는 것은 사용자의 결정입니다.

모델 학습 실행의 로깅 값에 대 한 자세한 내용은 [AZURE ML 학습 실행에서 로깅 사용](how-to-track-experiments.md)을 참조 하세요.

## <a name="specify-early-termination-policy"></a><a name="early-termination"></a> 조기 종료 정책 지정

조기 종료 정책으로 잘못 실행 되는 실행을 자동으로 종료 합니다. 초기 종료는 계산 효율성을 향상 시킵니다.

정책이 적용 되는 시기를 제어 하는 다음 매개 변수를 구성할 수 있습니다.

* `evaluation_interval`: 정책을 적용 하는 빈도입니다. 학습 스크립트에서 기본 메트릭을 기록할 때마다 한 번의 간격으로 계산됩니다. `evaluation_interval`1의는 학습 스크립트에서 기본 메트릭을 보고할 때마다 정책을 적용 합니다. `evaluation_interval`2의는 다른 시간 마다 정책을 적용 합니다. 지정하지 않으면 `evaluation_interval`은 기본적으로 1로 설정됩니다.
* `delay_evaluation`: 지정된 간격 동안 첫 번째 정책 평가를 지연합니다. 모든 구성이 최소 간격으로 실행 되도록 허용 하 여 학습 실행의 조기 종료를 방지 하는 선택적 매개 변수입니다. 지정된 경우 정책은 delay_evaluation보다 크거나 같은 evaluation_interval의 모든 배수마다 적용됩니다.

Azure Machine Learning는 다음과 같은 조기 종료 정책을 지원 합니다.
* [산적 정책](#bandit-policy)
* [중앙값 정책 중지](#median-stopping-policy)
* [잘림 선택 영역 정책](#truncation-selection-policy)
* [종료 정책 없음](#no-termination-policy-default)


### <a name="bandit-policy"></a>산적 정책

[산적 정책은](/python/api/azureml-train-core/azureml.train.hyperdrive.banditpolicy?preserve-view=true&view=azure-ml-py#&preserve-view=truedefinition) 여유 시간 비율/여유 공간 및 평가 간격을 기준으로 합니다. 기본 메트릭이 가장 적합 한 실행에 비해 지정 된 여유 비율/여유 시간 범위 내에 있지 않은 경우 산적이 실행 됩니다.

> [!NOTE]
> Bayesian 샘플링은 조기 종료를 지원 하지 않습니다. Bayesian 샘플링을 사용 하는 경우를 설정 `early_termination_policy = None` 합니다.

다음 구성 매개 변수를 지정 합니다.

* `slack_factor` 또는 `slack_amount`: 성능이 훌륭한 학습 실행에 대해 허용된 slack입니다. `slack_factor`는 비율로 허용되는 slack을 지정합니다. `slack_amount`는 비율 대신에 절대 양으로 허용되는 slack을 지정합니다.

    예를 들어 10 시간 간격으로 산적 정책이 적용 되는 경우를 가정 합니다. 주 메트릭이 보고 되는 간격 10에서 가장 잘 실행 되는 실행은 기본 메트릭을 최대화 하는 목표로 0.8입니다. 정책에서 0.2의를 지정 하는 경우 `slack_factor` 간격 10에서 가장 좋은 메트릭이 0.66 (0.8/(1 +)) 보다 작은 학습 실행이 `slack_factor` 종료 됩니다.
* `evaluation_interval`: (선택 사항) 정책을 적용 하는 빈도
* `delay_evaluation`: (선택 사항) 지정 된 간격 만큼 첫 번째 정책 평가를 지연 합니다.


```Python
from azureml.train.hyperdrive import BanditPolicy
early_termination_policy = BanditPolicy(slack_factor = 0.1, evaluation_interval=1, delay_evaluation=5)
```

이 예제에서는 평가 간격 5에서 시작하여 메트릭이 보고될 때 모든 간격에서 초기 종료 정책이 적용됩니다. 최상의 메트릭이 성능이 훌륭한 실행의 (1/(1+0.1) 또는 91%보다 작은 모든 실행이 종료됩니다.

### <a name="median-stopping-policy"></a>중앙값 중지 정책

[중앙값 중지](/python/api/azureml-train-core/azureml.train.hyperdrive.medianstoppingpolicy?preserve-view=true&view=azure-ml-py) 는 실행에서 보고 되는 기본 메트릭의 평균 실행을 기반으로 하는 초기 종료 정책입니다. 이 정책은 모든 학습 실행의 평균 실행을 계산 하 고 평균 중앙값 보다 더 나쁜 기본 메트릭 값을 사용 하 여 실행을 종료 합니다.

이 정책은 다음 구성 매개 변수를 사용합니다.
* `evaluation_interval`: 정책 적용에 대한 빈도입니다(선택적 매개 변수).
* `delay_evaluation`: 지정된 간격 동안 첫 번째 정책 평가를 지연합니다(선택적 매개 변수).


```Python
from azureml.train.hyperdrive import MedianStoppingPolicy
early_termination_policy = MedianStoppingPolicy(evaluation_interval=1, delay_evaluation=5)
```

이 예제에서는 평가 간격 5에서 시작하여 모든 간격에서 초기 종료 정책이 적용됩니다. 가장 적합 한 기본 메트릭이 모든 학습 실행에서 1:5 간격으로 실행 되는 평균 중앙값 보다 더 나쁜 경우 실행은 간격 5에서 종료 됩니다.

### <a name="truncation-selection-policy"></a>잘림 선택 영역 정책

[잘림 선택](/python/api/azureml-train-core/azureml.train.hyperdrive.truncationselectionpolicy?preserve-view=true&view=azure-ml-py) 은 각 평가 간격에서 가장 적게 수행 되는 실행의 백분율을 취소 합니다. 주 메트릭을 사용 하 여 실행을 비교 합니다. 

이 정책은 다음 구성 매개 변수를 사용합니다.

* `truncation_percentage`: 각 평가 간격에서 종료할 성능이 가장 낮은 실행의 백분율입니다. 1에서 99 사이의 정수 값입니다.
* `evaluation_interval`: (선택 사항) 정책을 적용 하는 빈도
* `delay_evaluation`: (선택 사항) 지정 된 간격 만큼 첫 번째 정책 평가를 지연 합니다.


```Python
from azureml.train.hyperdrive import TruncationSelectionPolicy
early_termination_policy = TruncationSelectionPolicy(evaluation_interval=1, truncation_percentage=20, delay_evaluation=5)
```

이 예제에서는 평가 간격 5에서 시작하여 모든 간격에서 초기 종료 정책이 적용됩니다. 간격 5의 성능이 간격 5에서 모든 실행의 최저 20%에 해당 하는 경우 실행은 interval 5에서 종료 됩니다.

### <a name="no-termination-policy-default"></a>종료 정책 없음 (기본값)

정책이 지정 되지 않은 경우 하이퍼 매개 변수 튜닝 서비스를 사용 하면 모든 학습 실행이 완료 될 수 있습니다.

```Python
policy=None
```

### <a name="picking-an-early-termination-policy"></a>조기 종료 정책 선택

* 유망한 작업을 종료 하지 않고 절감 액을 제공 하는 보수적인 정책은 1과 5를 사용 하는 중앙값 중지 정책을 고려 `evaluation_interval` `delay_evaluation` 하세요. 이는 일반적인 설정이며, 기본 메트릭에서 손실 없이 약 25%-35% 절감을 제공할 수 있습니다(계산 데이터에 따라).
* 보다 적극적인 절감 효과를 위해 더 작은 여유 공간이 나 잘림 선택 정책으로 더 큰 (잘림 비율 포함) 산적 정책을 사용 합니다.

## <a name="allocate-resources"></a>리소스 할당

최대 학습 실행 수를 지정 하 여 리소스 예산을 제어 합니다.

* `max_total_runs`: 학습 실행의 최대 수입니다. 1에서 1000 사이의 정수 여야 합니다.
* `max_duration_minutes`: (선택 사항) 하이퍼 매개 변수 튜닝 실험의 최대 지속 시간 (분)입니다. 이 기간이 취소 된 후에 실행 됩니다.

>[!NOTE] 
>`max_total_runs` 및 `max_duration_minutes`가 모두 지정된 경우 이러한 두 임계값 중 첫 번째 임계값에 도달하면 하이퍼 매개 변수 조정 실험은 종료됩니다.

또한 하이퍼 매개 변수 조정 검색 중 동시에 실행할 학습 실행의 최대 수를 지정합니다.

* `max_concurrent_runs`: (선택 사항) 동시에 실행할 수 있는 최대 실행 수입니다. 지정 하지 않으면 모든 실행이 병렬로 시작 됩니다. 지정 된 경우은 1에서 100 사이의 정수 여야 합니다.

>[!NOTE] 
>동시 실행 수는 지정된 컴퓨팅 대상에서 사용할 수 있는 리소스에서 제어됩니다. 계산 대상에 원하는 동시성에 사용할 수 있는 리소스가 있는지 확인 합니다.

```Python
max_total_runs=20,
max_concurrent_runs=4
```

이 코드는 최대 20 개의 총 실행을 사용 하 여 한 번에 4 개의 구성을 실행 하도록 하이퍼 매개 변수 튜닝 실험을 구성 합니다.

## <a name="configure-experiment"></a>실험 구성

하이퍼 [매개 변수 튜닝](/python/api/azureml-train-core/azureml.train.hyperdrive.hyperdriverunconfig?preserve-view=true&view=azure-ml-py) 실험을 구성 하려면 다음을 제공 합니다.
* 정의 된 하이퍼 매개 변수 검색 공간
* 조기 종료 정책
* 기본 메트릭
* 리소스 할당 설정
* ScriptRunConfig `src`

ScriptRunConfig는 샘플링 된 하이퍼 매개 변수를 사용 하 여 실행 되는 학습 스크립트입니다. 작업 당 리소스 (단일 또는 다중 노드) 및 사용할 계산 대상을 정의 합니다.

> [!NOTE]
>에 지정 된 계산 대상에는 `src` 동시성 수준을 충족 하기에 충분 한 리소스가 있어야 합니다. ScriptRunConfig에 대 한 자세한 내용은 [학습 실행 구성](how-to-set-up-training-targets.md)을 참조 하세요.

하이퍼 매개 변수 조정 실험 구성

```Python
from azureml.train.hyperdrive import HyperDriveConfig
hd_config = HyperDriveConfig(run_config=src,
                             hyperparameter_sampling=param_sampling,
                             policy=early_termination_policy,
                             primary_metric_name="accuracy",
                             primary_metric_goal=PrimaryMetricGoal.MAXIMIZE,
                             max_total_runs=100,
                             max_concurrent_runs=4)
```

## <a name="submit-experiment"></a>실험 제출

하이퍼 매개 변수 튜닝 구성을 정의한 후 실험을 [제출 합니다](/python/api/azureml-core/azureml.core.experiment%28class%29?preserve-view=true&view=azure-ml-py#&preserve-view=truesubmit-config--tags-none----kwargs-).

```Python
from azureml.core.experiment import Experiment
experiment = Experiment(workspace, experiment_name)
hyperdrive_run = experiment.submit(hd_config)
```

## <a name="warm-start-your-hyperparameter-tuning-experiment-optional"></a>하이퍼 매개 변수 튜닝 실험 준비 (선택 사항)

모델에 가장 적합 한 하이퍼 매개 변수 값을 찾으려면 반복적인 프로세스가 될 수 있습니다. 이전의 5 개 실행의 정보를 다시 사용 하 여 하이퍼 매개 변수 튜닝을 가속화할 수 있습니다.


웜 시작은 샘플링 방법에 따라 다르게 처리 됩니다.
- **Bayesian 샘플링** : 이전 실행에서의 평가판은 새 샘플을 선택 하 고 기본 메트릭을 개선 하기 위한 이전 기술 자료로 사용 됩니다.
- **무작위 샘플링** 또는 **그리드 샘플링** : 조기 종료는 이전 실행의 정보를 사용 하 여 실행이 제대로 수행 되지 않는 것을 확인 합니다. 

준비 하려는 부모 실행 목록을 지정 합니다.

```Python
from azureml.train.hyperdrive import HyperDriveRun

warmstart_parent_1 = HyperDriveRun(experiment, "warmstart_parent_run_ID_1")
warmstart_parent_2 = HyperDriveRun(experiment, "warmstart_parent_run_ID_2")
warmstart_parents_to_resume_from = [warmstart_parent_1, warmstart_parent_2]
```

하이퍼 매개 변수 튜닝 실험을 취소 한 경우 마지막 검사점에서 학습 실행을 다시 시작할 수 있습니다. 그러나 학습 스크립트는 검사점 논리를 처리 해야 합니다.

학습 실행은 동일한 하이퍼 매개 변수 구성을 사용 하 고 출력 폴더를 탑재 해야 합니다. 학습 스크립트는 `resume-from` 학습 실행을 다시 시작할 검사점 또는 모델 파일이 포함 된 인수를 수락 해야 합니다. 다음 코드 조각을 사용 하 여 개별 학습 실행을 다시 시작할 수 있습니다.

```Python
from azureml.core.run import Run

resume_child_run_1 = Run(experiment, "resume_child_run_ID_1")
resume_child_run_2 = Run(experiment, "resume_child_run_ID_2")
child_runs_to_resume = [resume_child_run_1, resume_child_run_2]
```

하이퍼 매개 변수 튜닝 실험을 구성 하 여 이전 실험에서 웜 시작 하거나 선택적 매개 변수와 구성에서 개별 학습 실행을 다시 시작할 수 있습니다 `resume_from` `resume_child_runs` .

```Python
from azureml.train.hyperdrive import HyperDriveConfig

hd_config = HyperDriveConfig(run_config=src,
                             hyperparameter_sampling=param_sampling,
                             policy=early_termination_policy,
                             resume_from=warmstart_parents_to_resume_from,
                             resume_child_runs=child_runs_to_resume,
                             primary_metric_name="accuracy",
                             primary_metric_goal=PrimaryMetricGoal.MAXIMIZE,
                             max_total_runs=100,
                             max_concurrent_runs=4)
```

## <a name="visualize-experiment"></a>실험 시각화

[노트북 위젯을](/python/api/azureml-widgets/azureml.widgets.rundetails?preserve-view=true&view=azure-ml-py) 사용 하 여 학습 실행의 진행률을 시각화할 수 있습니다. 다음 코드 조각은 Jupyter Notebook에서 모든 하이퍼 매개 변수 조정 실행을 시각화합니다.

```Python
from azureml.widgets import RunDetails
RunDetails(hyperdrive_run).show()
```

이 코드는 각 하이퍼 매개 변수 구성의 학습 실행에 대한 세부 정보가 있는 표를 표시합니다.

![하이퍼 매개 변수 튜닝 테이블](./media/how-to-tune-hyperparameters/HyperparameterTuningTable.png)

학습 진행률에 따라 각 실행의 성능을 시각화할 수도 있습니다. 

![하이퍼 매개 변수 튜닝 플롯](./media/how-to-tune-hyperparameters/HyperparameterTuningPlot.png)

병렬 좌표 플롯을 사용 하 여 개별 하이퍼 매개 변수의 성능과 값 간의 상관 관계를 시각적으로 식별할 수 있습니다. 

[![하이퍼 매개 변수 튜닝 병렬 좌표](./media/how-to-tune-hyperparameters/HyperparameterTuningParallelCoordinates.png)](media/how-to-tune-hyperparameters/hyperparameter-tuning-parallel-coordinates-expanded.png)

Azure 웹 포털에서 하이퍼 매개 변수 튜닝 실행을 모두 시각화할 수도 있습니다. 포털에서 실험을 보는 방법에 대 한 자세한 내용은 [실험을 추적 하는 방법](how-to-monitor-view-training-logs.md#view-the-experiment-in-the-web-portal)을 참조 하세요.

## <a name="find-the-best-model"></a>최적 모델 찾기

모든 하이퍼 매개 변수 튜닝 실행이 완료 되 면 가장 잘 수행 하는 구성 및 하이퍼 매개 변수 값을 식별 합니다.

```Python
best_run = hyperdrive_run.get_best_run_by_primary_metric()
best_run_metrics = best_run.get_metrics()
parameter_values = best_run.get_details()['runDefinition']['Arguments']

print('Best Run Id: ', best_run.id)
print('\n Accuracy:', best_run_metrics['accuracy'])
print('\n learning rate:',parameter_values[3])
print('\n keep probability:',parameter_values[5])
print('\n batch size:',parameter_values[7])
```

## <a name="sample-notebook"></a>샘플 노트북
이 폴더의 학습-hyperparameter-* 노트북을 참조 하세요.
* [how-to-use-azureml/ml-frameworks](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/ml-frameworks)

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>다음 단계
* [실험 추적](how-to-track-experiments.md)
* [학습된 모델 배포](how-to-deploy-and-where.md)