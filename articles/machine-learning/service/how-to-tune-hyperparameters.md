---
title: Azure Machine Learning을 사용하여 모델에 대한 하이퍼 매개 변수 튜닝
description: Azure Machine Learning 서비스를 사용하여 딥 러닝/기계 학습 모델에 대한 하이퍼 매개 변수를 튜닝하는 방법을 알아봅니다. 매개 변수 검색 공간을 정의하고, 최적화할 기본 메트릭을 지정하고, 성능이 불량한 구성을 초기 종료하는 방법을 살펴봅니다.
ms.author: swatig
author: swatig007
ms.reviewer: sgilley
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: b6370fd9125c5b14df781b27e028c139175b7589
ms.sourcegitcommit: b7e5bbbabc21df9fe93b4c18cc825920a0ab6fab
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/27/2018
ms.locfileid: "47405804"
---
# <a name="tune-hyperparameters-for-your-model"></a>모델에 대한 하이퍼 매개 변수 튜닝

이 문서에서는 모델에 대한 하이퍼 매개 변수를 효율적으로 튜닝하는 것을 알아봅니다. 매개 변수 검색 공간을 정의하고, 최적화할 기본 메트릭을 지정하고, 성능이 불량한 구성을 초기 종료하는 방법을 살펴봅니다. 또한 다양한 학습 실행을 시각화하고 모델에 대한 성능이 훌륭한 구성을 선택합니다.

## <a name="what-are-hyperparameters"></a>하이퍼 매개 변수란?
하이퍼 매개 변수는 학습 프로세스 자체를 제어하는 모델을 학습하기 전에 선택한 조정할 수 있는 매개 변수입니다. 예를 들어 심층 신경망을 학습하기 전에 네트워크에서 숨겨진 계층 수 및 각 계층의 노드 수를 결정해야 합니다. 이러한 값은 일반적으로 학습 프로세스 중 일관되게 유지됩니다.

딥 러닝/기계 학습 시나리오에서 모델 성능은 선택한 하이퍼 매개 변수 값에 크게 의존합니다. 하이퍼 매개 변수 탐색의 목표는 원하는 성능을 제공하는 구성을 찾도록 다양한 하이퍼 매개 변수 구성을 검색하는 것입니다. 일반적으로 하이퍼 매개 변수 탐색 프로세스는 검색 공간이 방대하고 각 구성을 평가하는 데 많은 비용이 들 수 있으므로 매우 수동적입니다.

Azure Machine Learning을 통해 효율적인 방식으로 이 하이퍼 매개 변수 탐색을 자동화하여 상당한 시간과 리소스를 절약할 수 있습니다. 탐색할 하이퍼 매개 변수 값의 범위 및 이 탐색에 대해 실행될 최대 학습 수를 지정할 수 있습니다. 그러면 시스템은 다른 매개 변수 구성을 사용하여 여러 동시 학습 실행을 자동으로 시작하고 사용자가 선택한 메트릭을 기준으로 측정했을 때 최적의 성능을 제공하는 구성을 찾습니다. 성능이 낮은 학습 실행은 자동으로 초기 종료되어 계산 리소스의 낭비가 줄어듭니다. 이러한 리소스는 다른 하이퍼 매개 변수 구성을 탐색하는 데 대신 사용됩니다.

Azure Machine Learning 서비스를 사용하여 모델에 대한 하이퍼 매개 변수를 튜닝하려면 다음을 수행해야 합니다.
* 하이퍼 매개 변수 검색 공간 정의
* 최적화할 기본 메트릭 지정
* 초기 종료 정책 지정
* 하이퍼 매개 변수 튜닝에 대한 리소스 할당
* 위의 구성을 사용하여 실험 시작

## <a name="define-the-hyperparameter-search-space"></a>하이퍼 매개 변수 검색 공간 정의
Azure Machine Learning 서비스는 각 하이퍼 매개 변수에 대해 정의된 값의 범위를 탐색하여 하이퍼 매개 변수를 튜닝합니다.

### <a name="types-of-hyperparameters"></a>하이퍼 매개 변수 형식
각 하이퍼 매개 변수는 불연속 또는 연속일 수 있습니다.

#### <a name="discrete-hyperparameters"></a>개별 하이퍼 매개 변수 
개별 하이퍼 매개 변수는 불연속 값 중 `choice`로 지정될 수 있습니다. `choice`는 하나 이상의 쉼표로 구분된 값, `range` 개체 또는 임의의 `list` 개체를 사용할 수 있습니다. 예를 들면 다음과 같습니다.  

```Python
    {    
        "batch_size": choice(16, 32, 64, 128)
        "number_of_hidden_layers": choice(range(1,5))
    }
```

이 경우 batch_size는 [16, 32, 64, 128] 값 중 하나를 사용할 수 있으며 number_of_hidden_layers는 [1, 2, 3, 4] 값 중 하나를 사용할 수 있습니다.

고급 불연속 하이퍼 매개 변수는 배포를 사용하여 지정될 수도 있습니다. 다음 배포가 지원됩니다.
* `quniform(low, high, q)` - 라운드와 같은 값 반환(uniform(low, high) / q) * q
* `qloguniform(low, high, q)` - 라운드와 같은 값 반환(exp(uniform(low, high)) / q) * q
* `qnormal(mu, sigma, q)` - 라운드와 같은 값 반환(normal(mu, sigma) / q) * q
* `qlognormal(mu, sigma, q)` - 라운드와 같은 값 반환(exp(normal(mu, sigma)) / q) * q

#### <a name="continuous-hyperparameters"></a>연속 하이퍼 매개 변수 
연속 하이퍼 매개 변수는 연속 값의 범위에 대해 배포로 지정될 수 있습니다. 지원되는 배포는 다음을 포함합니다.
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

이 예제에서는 두 개의 매개 변수(learning_rate 및 keep_probability)를 사용하여 검색 공간을 정의합니다. learning_rate는 평균 값 10과 3의 표준 편차가 있는 정규 분포를 갖게 됩니다. keep_probability는 0.05의 최솟값 및 0.1의 최댓값을 갖는 균일한 분포를 갖게 됩니다.

### <a name="sampling-the-hyperparameter-space"></a>하이퍼 매개 변수 공간 샘플링
사용자는 또한 지정된 하이퍼 매개 변수 공간 정의를 통해 사용하도록 매개 변수 샘플링 방법을 지정합니다. Azure Machine Learning 서비스는 무작위 샘플링, 그리드 샘플링 및 Bayesian 샘플링을 지원합니다.

#### <a name="random-sampling"></a>무작위 샘플링
무작위 샘플링에서 하이퍼 매개 변수 값은 정의된 검색 공간에서 임의로 선택됩니다. 무작위 샘플링은 불연속 및 연속 하이퍼 매개 변수를 포함하는 검색 공간을 허용합니다. 예를 들면 다음과 같습니다.

```Python
from azureml.train.hyperdrive import RandomParameterSampling
param_sampling = RandomParameterSampling( {
        "learning_rate": normal(10, 3),
        "keep_probability": uniform(0.05, 0.1),
        "batch_size": choice(16, 32, 64, 128)
    }
)
```

#### <a name="grid-sampling"></a>그리드 샘플링
그리드 샘플링은 정의된 검색 공간에서 사용할 수 있는 모든 값을 통해 간단한 그리드 검색을 수행합니다. `choice`를 사용하여 지정된 하이퍼 매개 변수를 통해서만 사용할 수 있습니다. 예를 들어 다음 공간에는 총 6개의 샘플이 있습니다.

```Python
from azureml.train.hyperdrive import GridParameterSampling
param_sampling = GridParameterSampling( {
        "num_hidden_layers": choice(1, 2, 3),
        "batch_size": choice(16, 32)
    }
)
```

#### <a name="bayesian-sampling"></a>Bayesian 샘플링
Bayesian 샘플링은 Bayesian 최적화 알고리즘을 기반으로 하며 다음에 샘플링할 하이퍼 매개 변수 값에 대한 지능적인 선택을 합니다. 새 샘플이 보고된 기본 메트릭을 개선하기 위해 이전 샘플이 수행한 방법에 따라 이 샘플을 선택합니다.

Bayesian 샘플링을 사용할 때 동시 실행 수는 튜닝 프로세스의 효율성에 영향을 줍니다. 일반적으로 동시 실행 수가 작을수록 샘플링 수렴이 향상될 수 있습니다. 이는 병렬 처리 수준이 작을수록 이전에 완료된 실행을 활용하는 실행 수가 늘어나기 때문입니다.

Bayesian 샘플링은 검색 공간을 통해 `choice` 및 `uniform` 배포만을 지원합니다. 예를 들면 다음과 같습니다. 

```Python
from azureml.train.hyperdrive import BayesianParameterSampling
param_sampling = BayesianParameterSampling( {
        "learning_rate": uniform(0.05, 0.1),
        "batch_size": choice(16, 32, 64, 128)
    }
)
```

> [!NOTE]
> Bayesian 샘플링은 현재 모든 초기 종료 정책을 지원하지 않습니다([초기 종료 정책 지정](#specify-an-early-termination-policy) 참조). Bayesian 매개 변수 샘플링을 사용하는 경우 정책을 `None`으로 설정해야 합니다. Bayesian 샘플링을 사용하여 종료 정책을 지정하지 않으면 동일한 효과를 갖습니다.
>
> ```Python
> early_termination_policy = None
> ```

## <a name="specify-a-primary-metric-to-optimize"></a>최적화할 기본 메트릭 지정
하이퍼 매개 변수를 튜닝하는 경우 하이퍼 매개 변수 실험을 최적화할 기본 메트릭을 지정해야 합니다. 각 학습 실행은 이 기본 메트릭에 대해 평가되며 성능이 불량한 실행(기본 메트릭이 초기 종료 정책에서 설정된 기준을 충족하지 않음)은 종료됩니다. 기본 메트릭 이름을 지정하는 것 외에도 기본 메트릭을 최대화하거나 최소화할지 최적화의 목표를 지정해야 합니다.
* `primary_metric_name`: 최적화할 기본 메트릭의 이름입니다. 기본 메트릭 이름은 학습 스크립트에서 기록된 메트릭의 이름과 정확히 일치해야 합니다. [하이퍼 매개 변수 튜닝에 대한 메트릭 기록](#log-metrics-for-hyperparameter-tuning)을 참조하세요.
* `primary_metric_goal`: `PrimaryMetricGoal.MAXIMIZE` 또는 `PrimaryMetricGoal.MINIMIZE`일 수 있으며 실행을 평가할 때 기본 메트릭을 최대화 또는 최소화할지 여부를 결정합니다. 

예를 들면 다음과 같습니다.
```Python
primary_metric_name="accuracy",
primary_metric_goal=PrimaryMetricGoal.MAXIMIZE
```
"정확도"를 최대화할 실행을 최적화합니다.

### <a name="log-metrics-for-hyperparameter-tuning"></a>하이퍼 매개 변수 튜닝에 대한 메트릭 기록
하이퍼 매개 변수 튜닝에 Azure Machine Learning 서비스를 사용하려면 모델이 실행되는 동안 모델에 대한 학습 스크립트는 관련 메트릭을 보고해야 합니다. 사용자는 서비스가 실행 성능 평가에 사용할 기본 메트릭을 지정하고, 학습 스크립트는 이 메트릭을 기록해야 합니다. [최적화할 기본 메트릭 지정](#specify-a-primary-metric-to-optimize)을 참조하세요.

다음 샘플 코드 조각을 사용하여 이 메트릭을 기록할 학습 스크립트를 업데이트할 수 있습니다.

```Python
from azureml.core.run import Run
run_logger = Run.get_submitted_run()
run_logger.log("accuracy", float(val_accuracy))
```

이 예제에서 학습 스크립트는 `val_accuracy`를 계산하고 기본 메트릭으로 사용되는 이 "정확도"를 기록합니다. 이 메트릭을 보고하는 빈도를 결정하는 모델 개발자의 몫입니다.

## <a name="specify-an-early-termination-policy"></a>초기 종료 정책 지정
Azure Machine Learning 서비스를 사용하여 하이퍼 매개 변수를 튜닝할 때 성능이 불량한 실행은 자동으로 초기 종료됩니다. 이는 리소스의 낭비를 줄이고 다른 매개 변수 구성을 탐색하는 데 이러한 리소스를 대신 사용합니다.

초기 종료 정책을 사용할 때 사용자는 정책이 적용되는 경우를 제어하는 다음 매개 변수를 구성할 수 있습니다.
* `evaluation_interval`: 정책 적용에 대한 빈도입니다. 학습 스크립트에서 기본 메트릭을 기록할 때마다 한 번의 간격으로 계산됩니다. 따라서 1의 `evaluation_interval`은 학습 스크립트에서 기본 메트릭을 보고할 때마다 정책을 적용합니다. 2의 `evaluation_interval`은 학습 스크립트에서 기본 메트릭을 보고할 때마다 하나 걸러 한 번씩 정책을 적용합니다. 이는 선택적 매개 변수이며 지정되지 않은 경우 `evaluation_interval`은 기본적으로 1로 설정됩니다.
* `delay_evaluation`: 지정된 간격 동안 첫 번째 정책 평가를 지연합니다. 이는 학습 실행의 이른 종료를 방지하도록 간격의 초기 최소 수에 대해 모든 구성이 실행되도록 허용하는 선택적 매개 변수입니다. 지정된 경우 정책은 delay_evaluation보다 크거나 같은 evaluation_interval의 모든 배수마다 적용됩니다.

Azure Machine Learning 서비스는 다음 초기 종료 정책을 지원합니다.

### <a name="bandit-policy"></a>산적 정책
산적 정책은 slack 요소/slack 양 및 평가 간격을 기반으로 하는 종료 정책입니다. 이 정책은 기본 메트릭이 성능이 훌륭한 학습 실행에 대해 지정된 slack 요소/slack 양 내에 있지 않은 모든 실행을 초기 종료합니다. 다음 구성 매개 변수를 사용합니다.
* `slack_factor` 또는 `slack_amount`: 성능이 훌륭한 학습 실행에 대해 허용된 slack입니다. `slack_factor`는 비율로 허용되는 slack을 지정합니다. `slack_amount`는 비율 대신에 절대 양으로 허용되는 slack을 지정합니다.

    예를 들어 간격 10에 적용되는 산적 정책을 사용하는 것이 좋습니다. 간격 10에서 성능이 훌륭한 실행이 기본 메트릭을 최대화하는 목표로 기본 메트릭 0.8을 보고했다고 가정합니다. 정책이 0.2의 `slack_factor`로 지정된 경우 간격 10에서 최상의 메트릭이 0.66(0.8/(1+`slack_factor`))보다 작은 모든 학습 실행이 종료됩니다. 대신 정책이 0.2의 `slack_amount`로 지정된 경우 간격 10에서 최상의 메트릭이 0.6(0.8 - `slack_amount`)보다 작은 모든 학습 실행이 종료됩니다.
* `evaluation_interval`: 정책 적용에 대한 빈도입니다(선택적 매개 변수).
* `delay_evaluation`: 지정된 간격 동안 첫 번째 정책 평가를 지연합니다(선택적 매개 변수).

다음 예제를 살펴보세요.

```Python
from azureml.train.hyperdrive import BanditPolicy
early_termination_policy = BanditPolicy(slack_factor = 0.1, evaluation_interval=1, delay_evaluation=5)
```

이 예제에서는 평가 간격 5에서 시작하여 메트릭이 보고될 때 모든 간격에서 초기 종료 정책이 적용됩니다. 최상의 메트릭이 성능이 훌륭한 실행의 (1/(1+0.1) 또는 91%보다 작은 모든 실행이 종료됩니다.

### <a name="median-stopping-policy"></a>중앙값 중지 정책
중앙값 중지 정책은 해당 실행에서 보고되는 기본 메트릭의 실행 평균을 기반으로 하는 초기 종료 정책입니다. 이 정책은 모든 학습 실행에서 실행 평균을 계산하고 성능이 실행 평균의 중앙값보다 좋지 않은 실행을 종료합니다. 이 정책은 다음 구성 매개 변수를 사용합니다.
* `evaluation_interval`: 정책 적용에 대한 빈도입니다(선택적 매개 변수).
* `delay_evaluation`: 지정된 간격 동안 첫 번째 정책 평가를 지연합니다(선택적 매개 변수).

다음 예제를 살펴보세요.

```Python
from azureml.train.hyperdrive import MedianStoppingPolicy
early_termination_policy = MedianStoppingPolicy(evaluation_interval=1, delay_evaluation=5)
```

이 예제에서는 평가 간격 5에서 시작하여 모든 간격에서 초기 종료 정책이 적용됩니다. 실행의 최적 기본 메트릭이 모든 학습 실행의 간격 1:5에서 실행 평균의 중앙값보다 좋지 않은 경우 실행은 간격 5에서 종료됩니다.

### <a name="truncation-selection-policy"></a>잘림 선택 정책
잘림 선택 정책은 각 평가 간격에서 성능이 가장 낮은 실행의 지정된 백분율을 취소합니다. 실행은 기본 메트릭의 해당 성능에 따라 비교되고 가장 낮은 X%가 종료됩니다. 다음 구성 매개 변수를 사용합니다.
* `truncation_percentage`: 각 평가 간격에서 종료할 성능이 가장 낮은 실행의 백분율입니다. 이는 1에서 99 사이의 정수여야 합니다.
* `evaluation_interval`: 정책 적용에 대한 빈도입니다(선택적 매개 변수).
* `delay_evaluation`: 지정된 간격 동안 첫 번째 정책 평가를 지연합니다(선택적 매개 변수).

다음 예제를 살펴보세요.

```Python
from azureml.train.hyperdrive import TruncationSelectionPolicy
early_termination_policy = TruncationSelectionPolicy(evaluation_interval=1, truncation_percentage=20, delay_evaluation=5)
```

이 예제에서는 평가 간격 5에서 시작하여 모든 간격에서 초기 종료 정책이 적용됩니다. 간격 5에서 해당 성능이 간격 5의 모든 실행에서 가장 낮은 20%의 성능에 있는 경우 실행은 간격 5에서 종료됩니다.

### <a name="no-termination-policy"></a>종료 정책 없음
모든 학습 실행을 완료될 때까지 실행하려는 경우 NoTerminationPolicy를 사용합니다. 초기 종료 정책이 적용되지 않습니다. 예를 들면 다음과 같습니다. 

```Python
from azureml.train.hyperdrive import NoTerminationPolicy
early_termination_policy = NoTerminationPolicy()
```

### <a name="default-policy"></a>기본 정책
정책이 지정되지 않은 경우 하이퍼 매개 변수 튜닝 서비스는 기본적으로 `evaluation_interval` 1 및 `delay_evaluation` 5로 중앙값 중지 정책을 사용합니다. 이는 일반적인 설정이며, 기본 메트릭에서 손실 없이 약 25%-35% 절감을 제공할 수 있습니다(계산 데이터에 따라).

## <a name="allocate-resources-for-hyperparameter-tuning"></a>하이퍼 매개 변수 튜닝에 대한 리소스 할당
학습 실행의 최대 총 수 및 경우에 따라 하이퍼 매개 변수 튜닝 실험에 대한 최대 지속 시간(분)을 지정하여 하이퍼 매개 변수 튜닝 실험에 대한 리소스 예산을 제어할 수 있습니다. 
* `max_total_runs`: 생성될 학습 실행 최대 총 수입니다. 이는 상한이며 더 적은 실행을 가질 수 있습니다(예: 하이퍼 매개 변수 공간이 한정되어 있고 더 적은 샘플을 갖는 경우). 값은 1에서 1000 사이의 숫자여야 합니다.
* `max_duration_minutes`: 하이퍼 매개 변수 튜닝 실험의 최대 지속 시간(분)입니다. 이는 선택적 매개 변수이며 있는 경우 이 기간 후 실행될 수 있는 모든 실행은 자동으로 취소됩니다.

>[!NOTE] 
>`max_total_runs` 및 `max_duration_minutes`가 지정된 경우 이러한 두 임계값 중 첫 번째 임계값에 도달하면 하이퍼 매개 변수 튜닝 실험은 종료됩니다.

또한 하이퍼 매개 변수 튜닝 검색 중 동시에 실행될 학습 실행의 최대 수를 지정할 수 있습니다.
* `max_concurrent_runs`: 모든 지정된 순간에서 동시에 실행될 실행의 최대 수입니다. 지정되지 않는 경우 모든 `max_total_runs`는 병렬로 시작됩니다. 지정된 경우 1과 100 사이의 숫자여야 합니다.

>[!NOTE] 
>동시 실행 수는 지정된 계산 대상에서 사용할 수 있는 리소스에서 제어됩니다. 따라서 계산 대상이 원하는 동시성에 대한 사용 가능한 리소스를 갖도록 해야 합니다.

이 예제에서와 같이 하이퍼 매개 변수 튜닝에 대한 리소스를 할당할 수 있습니다.
```Python
max_total_runs=20,
max_concurrent_runs=4
```
한 번에 4개의 구성을 실행하는 최대 20개의 총 실행을 사용하도록 하이퍼 매개 변수 튜닝 실험을 구성합니다.

## <a name="configure-your-hyperparameter-tuning-experiment"></a>하이퍼 매개 변수 튜닝 실험 구성
위의 섹션에서 정의된 하이퍼 매개 변수 검색 공간, 초기 종료 정책, 기본 메트릭 및 리소스 할당을 사용하여 하이퍼 매개 변수 튜닝 실험을 구성할 수 있습니다. 또한 샘플링된 하이퍼 매개 변수를 사용하여 호출될 `estimator`를 제공해야 합니다. `estimator`는 실행하는 학습 스크립트, 작업당 리소스(단일 또는 다중 gpu) 및 사용할 계산 대상을 설명합니다. 하이퍼 매개 변수 튜닝 실험에 대한 동시성은 사용 가능한 리소스에서 제어되므로 `estimator`에서 지정된 계산 대상에 원하는 동시성에 대한 충분한 리소스가 있어야 합니다. (추정에 대한 자세한 내용은 [모델을 학습하는 방법](how-to-train-ml-models.md)을 참조하세요.)

하이퍼 매개 변수 튜닝 실험을 구성할 수 있는 예제는 다음과 같습니다.

```Python
from azureml.train.hyperdrive import HyperDriveRunConfig
hyperdrive_run_config = HyperDriveRunConfig(estimator=estimator,
                          hyperparameter_sampling=param_sampling, 
                          policy=early_termination_policy,
                          primary_metric_name="accuracy", 
                          primary_metric_goal=PrimaryMetricGoal.MAXIMIZE,
                          max_total_runs=100,
                          max_concurrent_runs=4)
```

## <a name="submit-your-hyperparameter-tuning-experiment"></a>하이퍼 매개 변수 튜닝 실험 제출
하이퍼 매개 변수 튜닝 구성을 정의한 후에 이 구성을 사용하여 실험을 제출할 수 있습니다.

```Python
from azureml.core.experiment import Experiment
experiment = Experiment(workspace, experiment_name)
hyperdrive_run = experiment.submit(hyperdrive_run_config)
```

여기서 `experiment_name`은 하이퍼 매개 변수 튜닝 실험에 할당하려는 이름이며, `workspace`는 실험을 만들려는 작업 영역입니다(실험에 대한 자세한 내용은 [링크](/concept-azure-machine-learning-architecture.md) 참조).

## <a name="visualize-your-hyperparameter-tuning-experiment"></a>하이퍼 매개 변수 튜닝 실험 시각화
Azure Machine Learning SDK는 학습 실행의 진행률을 시각화하는 데 사용할 수 있는 Notebook 위젯을 제공합니다. 다음 코드 조각은 한 곳에서 모든 하이퍼 매개 변수 튜닝 실행을 시각화하는 데 사용할 수 있습니다.

```Python
from azureml.train.widgets import RunDetails
RunDetails(hyperdrive_run).show()
```

그러면 각 하이퍼 매개 변수 구성의 학습 실행에 대한 세부 정보가 있는 테이블이 표시됩니다. 예를 들면 다음과 같습니다.

![하이퍼 매개 변수 튜닝 테이블](media/how-to-tune-hyperparameters/HyperparameterTuningTable.png)

학습 진행률에 따라 각 실행의 성능을 시각화할 수도 있습니다. 예를 들면 다음과 같습니다.

![하이퍼 매개 변수 튜닝 플롯](media/how-to-tune-hyperparameters/HyperparameterTuningPlot.png)

마지막으로 병렬 좌표 플롯을 사용하여 개별 하이퍼 매개 변수의 성능 및 값 간의 상관 관계를 시각적으로 식별할 수 있습니다. 예를 들면 다음과 같습니다. 

![하이퍼 매개 변수 튜닝 병렬 좌표](media/how-to-tune-hyperparameters/HyperparameterTuningParallelCoordinates.png)

또는 Azure 웹 포털에서도 모든 하이퍼 매개 변수 튜닝 실행을 시각화할 수 있습니다. 웹 포털에서 실험을 보는 방법에 대한 자세한 내용은 [링크](/how-to-track-experiments.md/#view-the-experiment-in-the-web-portal)를 참조하세요. 예를 들면 다음과 같습니다.

![하이퍼 매개 변수 튜닝 포털](media/how-to-tune-hyperparameters/HyperparameterTuningPortal.png)

## <a name="find-the-configuration-that-resulted-in-the-best-performance"></a>최적의 성능을 제공하는 구성 찾기
모든 하이퍼 매개 변수 튜닝 실행이 완료되면 다음 코드 조각을 사용하여 성능이 훌륭한 구성 및 해당 하이퍼 매개 변수 값을 식별할 수 있습니다.

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
다음을 참조하세요. 
* Tensorflow 모델의 하이퍼 매개 변수 튜닝에 대한 자습서의 경우 `training/03.train-hyperparameter-tune-deploy-with-tensorflow/03.train-hyperparameter-tune-deploy-with-tensorflow.ipynb` 

이 Notebook을 다운로드하려면 다음 단계를 수행합니다.

[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>다음 단계
* [실험 추적](how-to-track-experiments.md)
* [학습된 모델 배포](how-to-deploy-and-where.md)
