---
title: AKS 배포에서 데이터 드리프트 감지
titleSuffix: Azure Machine Learning
description: Azure Kubernetes 서비스에서 Azure 기계 학습에서 배포된 모델에서 데이터 드리프트(미리 보기)를 검색합니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: copeters
author: cody-dkdc
ms.date: 11/04/2019
ms.openlocfilehash: d1da7309b296b57db0c28d5b52fe91efa86709c8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75537007"
---
# <a name="detect-data-drift-preview-on-models-deployed-to-azure-kubernetes-service-aks"></a>Azure Kubernetes 서비스(AKS)에 배포된 모델에서 데이터 드리프트(미리 보기) 감지
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-enterprise-sku.md)]

이 문서에서는 학습 데이터 집합과 배포된 모델의 추론 데이터 간에 데이터 드리프트를 모니터링하는 방법을 알아봅니다. 기계 학습의 맥락에서 숙련된 기계 학습 모델은 드리프트로 인해 예측 성능이 저하될 수 있습니다. Azure 기계 학습을 사용하면 데이터 드리프트를 모니터링할 수 있으며 드리프트가 감지되면 서비스에서 전자 메일 경고를 보낼 수 있습니다.

## <a name="what-is-data-drift"></a>데이터 드리프트란 무엇입니까?

기계 학습의 맥락에서 데이터 드리프트는 모델 성능 저하로 이어지는 모델 입력 데이터의 변화입니다. 시간이 지남에 따라 모델 정확도가 저하되는 가장 큰 이유 중 하나이므로 데이터 드리프트를 모니터링하면 모델 성능 문제를 감지하는 데 도움이 됩니다. 

## <a name="what-can-i-monitor"></a>무엇을 모니터링할 수 있습니까?

Azure 기계 학습을 사용하면 AKS에 배포된 모델에 대한 입력을 모니터링하고 이 데이터를 모델의 학습 데이터 집합과 비교할 수 있습니다. 정기적으로 추론 데이터는 [스냅샷및 프로파일링된](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py)다음 기준 데이터 집합에 대해 계산되어 다음과 같은 데이터 드리프트 분석을 생성합니다. 

+ 드리프트 계수라고 하는 데이터 드리프트의 크기를 측정합니다.
+ 기능별로 데이터 드리프트 기여도를 측정하여 데이터 드리프트를 발생시킨 피처를 나타냅니다.
+ 거리 메트릭을 측정합니다. 현재 바서슈타인과 에너지 거리가 계산됩니다.
+ 피처 분포를 측정합니다. 현재 커널 밀도 추정 및 히스토그램.
+ 이메일로 데이터 드리프트에 경고를 보냅니다.

> [!Note]
> 이 서비스는 (미리 보기)에 있으며 구성 옵션이 제한되어 있습니다. 자세한 내용 및 업데이트는 [API 설명서](https://docs.microsoft.com/python/api/azureml-datadrift/) 및 [릴리스 노트를](azure-machine-learning-release-notes.md) 참조하십시오. 

### <a name="how-data-drift-is-monitored-in-azure-machine-learning"></a>Azure 기계 학습에서 데이터 드리프트를 모니터링하는 방법

Azure 기계 학습을 사용하여 데이터 집합 또는 배포를 통해 데이터 드리프트를 모니터링합니다. 데이터 드리프트를 모니터링하기 위해 모델의 학습 데이터 집합인 기준 데이터 집합이 지정됩니다. 배포에서 수집된 두 번째 데이터 집합(일반적으로 배포에서 수집된 입력 데이터 모델)은 기준 데이터 집합에 대해 테스트됩니다. 두 데이터 집합은 프로파일화되고 데이터 드리프트 모니터링 서비스에 입력됩니다. 기계 학습 모델은 두 데이터 집합 간의 차이점을 검색하도록 학습됩니다. 모델의 성능은 드리프트 계수로 변환되어 두 데이터 집합 간의 드리프트 크기를 측정합니다. [모델 해석성을](how-to-machine-learning-interpretability.md)사용하여 드리프트 계수에 기여하는 피쳐가 계산됩니다. 데이터 집합 프로필에서 각 기능에 대한 통계 정보가 추적됩니다. 

## <a name="prerequisites"></a>사전 요구 사항

- Azure 구독 계정이 없는 경우 시작하기 전에 무료 계정을 만드세요. 지금 [Azure Machine Learning 평가판 또는 유료 버전](https://aka.ms/AMLFree)을 사용해 보세요.

- Python용 Azure Machine Learning SDK가 설치되었습니다. [Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py)의 지침에 따라 다음 작업을 수행합니다.

    - Miniconda 환경 만들기
    - Python용 Azure Machine Learning SDK 설치

- [Azure 기계 학습 작업 영역](how-to-manage-workspace.md).

- 작업 영역 [구성 파일](how-to-configure-environment.md#workspace).

- 다음 명령을 사용하여 데이터 드리프트 SDK를 설치합니다.

    ```shell
    pip install azureml-datadrift
    ```

- 모델의 학습 데이터에서 [데이터 집합을](how-to-create-register-datasets.md) 만듭니다.

- 모델을 등록할 때 학습 데이터 [집합을 지정합니다.](concept-model-management-and-deployment.md) 다음 예제에서는 매개 `datasets` 변수를 사용하여 학습 데이터 집합을 지정하는 방법을 보여 줍니다.

    ```python
    model = Model.register(model_path=model_file,
                        model_name=model_name,
                        workspace=ws,
                        datasets=datasets)

    print(model_name, image_name, service_name, model)
    ```

- [모델 데이터 수집을 사용하여](how-to-enable-data-collection.md) 모델의 AKS 배포에서 데이터를 수집하고 `modeldata` Blob 컨테이너에서 데이터가 수집되고 있는지 확인합니다.

## <a name="configure-data-drift"></a>데이터 드리프트 구성
실험에 대한 데이터 드리프트를 구성하려면 다음 Python 예제에서 와 같이 종속성을 가져옵니다. 

이 예제에서는 개체 구성을 [`DataDriftDetector`](/python/api/azureml-datadrift/azureml.datadrift.datadriftdetector.datadriftdetector) 보여 줍니다.

```python
# Import Azure ML packages
from azureml.core import Experiment, Run, RunDetails
from azureml.datadrift import DataDriftDetector, AlertConfiguration

# if email address is specified, setup AlertConfiguration
alert_config = AlertConfiguration('your_email@contoso.com')

# create a new DataDriftDetector object
datadrift = DataDriftDetector.create(ws, model.name, model.version, services, frequency="Day", alert_config=alert_config)
    
print('Details of Datadrift Object:\n{}'.format(datadrift))
```

## <a name="submit-a-datadriftdetector-run"></a>데이터 드리프트 검출기 실행 제출

객체를 `DataDriftDetector` 구성하면 모델에 지정된 날짜에 [데이터 드리프트 실행을](https://docs.microsoft.com/python/api/azureml-datadrift/azureml.datadrift.datadriftdetector.datadriftdetector#run-target-date--services-none--compute-target-none--create-compute-target-false--feature-list-none--drift-threshold-none-) 제출할 수 있습니다. 실행의 일부로 `drift_threshold` 매개 변수를 설정하여 DataDriftDetector 경고를 활성화합니다. [datadrift_coefficient](#visualize-drift-metrics) 위에 `drift_threshold`있으면 전자 메일이 전송됩니다.

```python
# adhoc run today
target_date = datetime.today()

# create a new compute - creates datadrift-server
run = datadrift.run(target_date, services, feature_list=feature_list, create_compute_target=True)

# or specify existing compute cluster
run = datadrift.run(target_date, services, feature_list=feature_list, compute_target='cpu-cluster')

# show details of the data drift run
exp = Experiment(ws, datadrift._id)
dd_run = Run(experiment=exp, run_id=run.id)
RunDetails(dd_run).show()
```

## <a name="visualize-drift-metrics"></a>드리프트 메트릭 시각화

<a name="metrics"></a>

DataDriftDetector 실행을 제출하면 데이터 드리프트 작업에 대해 각 실행 반복에 저장된 드리프트 메트릭을 볼 수 있습니다.


|메트릭|설명|
--|--|
wasserstein_distance|1차원 수치 분포에 대해 정의된 통계 거리입니다.|
energy_distance|1차원 수치 분포에 대해 정의된 통계 거리입니다.|
datadrift_coefficient|매튜의 상관 계수와 유사하게 계산되지만 이 출력은 0에서 1사이의 실제 숫자입니다. 드리프트의 컨텍스트에서 0은 드리프트를 나타내고 1은 최대 드리프트를 나타냅니다.|
datadrift_contribution|드리프트에 기여하는 기능의 특징 중요성.|

드리프트 메트릭을 보는 방법에는 여러 가지가 있습니다.

* `RunDetails` [주피터 위젯을](https://docs.microsoft.com/python/api/azureml-widgets/azureml.widgets?view=azure-ml-py)사용합니다.
* 모든 `datadrift` [`get_metrics()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29?view=azure-ml-py#get-metrics-name-none--recursive-false--run-type-none--populate-false-) 실행 개체에서 함수를 사용합니다.
* [Azure 기계 학습 스튜디오에서](https://ml.azure.com)작업 영역의 **모델** 섹션에서 메트릭을 봅니다.

다음 Python 예제에서는 관련 데이터 드리프트 메트릭을 플로팅하는 방법을 보여 줍니다. 반환된 메트릭을 사용하여 사용자 지정 시각화를 빌드할 수 있습니다.

```python
# start and end are datetime objects 
drift_metrics = datadrift.get_output(start_time=start, end_time=end)

# Show all data drift result figures, one per service.
# If setting with_details is False (by default), only the data drift magnitude will be shown; if it's True, all details will be shown.
drift_figures = datadrift.show(with_details=True)
```

![Azure 기계 학습에서 검색된 데이터 드리프트 보기](./media/how-to-monitor-data-drift/drift_show.png)


## <a name="schedule-data-drift-scans"></a>데이터 드리프트 스캔 예약 

데이터 드리프트 감지를 사용하도록 설정하면 DataDriftDetector가 지정된 예약 된 주파수에서 실행됩니다. datadrift_coefficient 지정된 `drift_threshold`실행에 도달하면 이메일이 전송됩니다. 

```python
datadrift.enable_schedule()
datadrift.disable_schedule()
```

데이터 드리프트 검출기의 구성은 [Azure 기계 학습 스튜디오의](https://ml.azure.com)작업 영역의 **세부 정보** 탭의 **모델** 에서 볼 수 있습니다.

[![Azure 기계 학습 스튜디오 데이터 드리프트](./media/how-to-monitor-data-drift/drift-config.png)](media/how-to-monitor-data-drift/drift-config-expanded.png)

## <a name="view-results-in-your-azure-machine-learning-studio"></a>Azure 기계 학습 스튜디오에서 결과 보기

[Azure 기계 학습 스튜디오에서](https://ml.azure.com)작업 영역에서 결과를 보려면 모델 페이지로 이동합니다. 모델의 세부 정보 탭에 데이터 드리프트 구성이 표시됩니다. 이제 **데이터 드리프트** 탭을 사용하여 데이터 드리프트 메트릭을 시각화할 수 있습니다. 

[![Azure 기계 학습 스튜디오 데이터 드리프트](./media/how-to-monitor-data-drift/drift-ui.png)](media/how-to-monitor-data-drift/drift-ui-expanded.png)

## <a name="receiving-drift-alerts"></a>드리프트 경고 수신

드리프트 계수 경고 임계값을 설정하고 전자 메일 주소를 제공하면 드리프트 계수가 임계값을 초과할 때마다 [Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/overview) 전자 메일 경고가 자동으로 전송됩니다. 

사용자 지정 경고 및 작업을 설정하기 위해 모든 데이터 드리프트 메트릭은 Azure 기계 학습 작업 영역과 함께 만든 [Application Insights](how-to-enable-app-insights.md) 리소스에 저장됩니다. 전자 메일 경고에서 응용 프로그램 인사이트 쿼리에 대한 링크를 따를 수 있습니다.

![데이터 드리프트 이메일 경고](./media/how-to-monitor-data-drift/drift_email.png)

## <a name="retrain-your-model-after-drift"></a>드리프트 후 모델 재학습

데이터 드리프트가 배포된 모델의 성능에 부정적인 영향을 미치는 경우 모델을 다시 학습해야 합니다. 이렇게 하려면 다음 단계를 계속 진행합니다.

* 수집된 데이터를 조사하고 데이터를 준비하여 새 모델을 학습합니다.
* 기차/테스트 데이터로 분할합니다.
* 새 데이터를 사용하여 모델을 다시 학습합니다.
* 새로 생성된 모델의 성능을 평가합니다.
* 프로덕션 모델보다 성능이 우수한 경우 새 모델을 배포합니다.

## <a name="next-steps"></a>다음 단계

* 데이터 드리프트사용의 전체 예는 [Azure ML 데이터 드리프트 노트북](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/monitor-models/data-drift/drift-on-aks.ipynb)을 참조하십시오. 이 Jupyter 노트북은 [Azure Open Dataset을](https://docs.microsoft.com/azure/open-datasets/overview-what-are-open-datasets) 사용하여 모델을 학습하여 날씨를 예측하고, AKS에 배포하고, 데이터 드리프트를 모니터링하는 방법을 보여 줍니다. 

* 데이터 집합 [모니터를 사용하여 데이터 드리프트를 감지합니다.](how-to-monitor-datasets.md)

* 데이터 드리프트가 일반 공급으로 이동함에 따라 질문, 의견 또는 제안에 크게 감사드립니다. 아래의 제품 피드백 버튼을 사용하십시오! 
