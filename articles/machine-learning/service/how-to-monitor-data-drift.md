---
title: AKS 배포에서 데이터 드리프트 (미리 보기) 검색
titleSuffix: Azure Machine Learning service
description: Azure Machine Learning 서비스에서 Azure Kubernetes 서비스 배포 모델에 대 한 데이터 드리프트를 검색 합니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: copeters
author: cody-dkdc
ms.date: 07/08/2019
ms.openlocfilehash: c5484c37d89cc9ae880bbe17987bb47f3114b8a4
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/08/2019
ms.locfileid: "68847888"
---
# <a name="detect-data-drift-preview-on-models-deployed-to-azure-kubernetes-service-aks"></a>AKS (Azure Kubernetes Service)에 배포 된 모델에서 데이터 드리프트 (미리 보기) 검색

이 문서에서는 학습 데이터 집합 및 배포 된 모델의 유추 데이터 간에 데이터 드리프트를 모니터링 하는 방법에 대해 알아봅니다. 기계 학습의 컨텍스트에서 학습 된 기계 학습 모델은 드리프트로 인 한 예측 성능을 저하 시킬 수 있습니다. Azure Machine Learning 서비스를 사용 하면 데이터 드리프트를 모니터링할 수 있으며, 드리프트가 검색 되 면 서비스에서 사용자에 게 전자 메일 경고를 보낼 수 있습니다.

## <a name="what-is-data-drift"></a>데이터 드리프트 란?

프로덕션 환경에서 모델에 제공 되는 데이터가 모델 학습에 사용 되는 데이터와 다를 때 데이터 드리프트가 발생 합니다. 모델 정확성이 시간이 지남에 따라 저하 되는 가장 중요 한 이유 중 하나 이므로 데이터 드리프트를 모니터링 하면 모델 성능 문제를 감지 하는 데 도움이 됩니다. 

## <a name="what-can-i-monitor"></a>무엇을 모니터링할 수 있나요?

Azure Machine Learning 서비스를 사용 하 여 AKS에 배포 된 모델에 대 한 입력을 모니터링 하 고이 데이터를 모델에 대 한 학습 데이터 집합과 비교할 수 있습니다. 정기적으로 유추 데이터는 [스냅샷과 프로 파일링](how-to-explore-prepare-data.md)된 다음, 데이터 드리프트 분석을 생성 하기 위해 기준선 데이터 집합에 대해 계산 됩니다. 

+ 드리프트 계수 라고 하는 데이터 드리프트의 크기를 측정 합니다.
+ 기능에의 한 데이터 드리프트 기여를 측정 하 여 데이터 드리프트를 발생 시킨 기능을 알립니다.
+ 거리 메트릭을 측정 합니다. 현재 Wasserstein와 에너지 거리가 계산 됩니다.
+ 기능의 분포를 측정 합니다. 현재 커널 밀도 예측 및 히스토그램
+ 전자 메일로 데이터 드리프트에 경고를 보냅니다.

> [!Note]
> 이 서비스는 (미리 보기)에 있으며 구성 옵션에서 제한 됩니다. 세부 정보 및 업데이트는 [API 설명서](https://docs.microsoft.com/python/api/azureml-contrib-datadrift/?view=azure-ml-py) 및 [릴리스 정보](azure-machine-learning-release-notes.md) 를 참조 하세요. 

### <a name="how-data-drift-is-monitored-in-azure-machine-learning-service"></a>Azure Machine Learning service에서 데이터 드리프트를 모니터링 하는 방법

데이터 드리프트는 Azure Machine Learning 서비스를 사용 하 여 데이터 집합 또는 배포를 통해 모니터링 됩니다. 데이터 드리프트를 모니터링 하기 위해 기본 데이터 집합 (일반적으로 모델에 대 한 학습 데이터 집합)이 지정 됩니다. 두 번째 데이터 집합 (일반적으로 배포에서 수집 된 입력 데이터 모델)은 기준선 데이터 집합에 대해 테스트 됩니다. 데이터 집합은 모두 [프로 파일링](how-to-explore-prepare-data.md#explore-with-summary-statistics) 되 고 데이터 드리프트 모니터링 서비스에 입력 됩니다. 기계 학습 모델을 학습 하 여 두 데이터 집합 간의 차이를 검색 합니다. 모델의 성능은 두 데이터 집합 간의 드리프트 크기를 측정 하는 드리프트 계수로 변환 됩니다. [모델 interpretability](machine-learning-interpretability-explainability.md)를 사용 하 여 드리프트 계수에 영향을 주는 기능을 계산 합니다. 데이터 집합 프로필에서 각 기능에 대 한 통계 정보를 추적 합니다. 

## <a name="prerequisites"></a>필수 구성 요소

- Azure 구독. 계정이 없는 경우 시작 하기 전에 무료 계정을 만듭니다. [Azure Machine Learning Service의 평가판 또는 유료 버전](https://aka.ms/AMLFree)을 지금 사용해 보세요.

- Python 용 Azure Machine Learning SDK가 설치 되어 있습니다. [AZURE MACHINE LEARNING SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py) 의 지침을 사용 하 여 다음을 수행할 수 있습니다.

    - Miniconda 환경 만들기
    - Python용 Azure Machine Learning SDK 설치

- [Azure Machine Learning 서비스 작업 영역](how-to-manage-workspace.md)입니다.

- 작업 영역 [구성 파일](how-to-configure-environment.md#workspace)입니다.

- 다음 명령을 사용 하 여 데이터 드리프트 SDK를 설치 합니다.

    ```shell
    pip install azureml-contrib-datadrift
    ```

- 모델의 학습 데이터에서 [데이터 집합](how-to-create-register-datasets.md) 을 만듭니다.

- 모델을 [등록할](concept-model-management-and-deployment.md) 때 학습 데이터 집합을 지정 합니다. 다음 예제에서는 `datasets` 매개 변수를 사용 하 여 학습 데이터 집합을 지정 하는 방법을 보여 줍니다.

    ```python
    model = Model.register(model_path=model_file,
                        model_name=model_name,
                        workspace=ws,
                        datasets=datasets)

    print(model_name, image_name, service_name, model)
    ```

- 모델 [데이터 컬렉션을 사용](how-to-enable-data-collection.md) 하 여 모델의 AKS 배포에서 데이터를 수집 하 고 `modeldata` blob 컨테이너에서 데이터를 수집 하 고 있는지 확인 합니다.

## <a name="configure-data-drift"></a>데이터 드리프트 구성
실험에 대 한 데이터 드리프트를 구성 하려면 다음 Python 예제에서 볼 수 있듯이 종속성을 가져옵니다. 

이 예제에서는 개체를 [`DataDriftDetector`](https://docs.microsoft.com/python/api/azureml-contrib-datadrift/azureml.contrib.datadrift.datadriftdetector.datadriftdetector?view=azure-ml-py) 구성 하는 방법을 보여 줍니다.

```python
# Import Azure ML packages
from azureml.core import Experiment, Run, RunDetails
from azureml.contrib.datadrift import DataDriftDetector, AlertConfiguration

# if email address is specified, setup AlertConfiguration
alert_config = AlertConfiguration('your_email@contoso.com')

# create a new DatadriftDetector object
datadrift = DataDriftDetector.create(ws, model.name, model.version, services, frequency="Day", alert_config=alert_config)
    
print('Details of Datadrift Object:\n{}'.format(datadrift))
```

## <a name="submit-a-datadriftdetector-run"></a>DataDriftDetector 실행 제출

구성 된 개체를 사용 하 여 모델에 대해 지정 된 날짜에 [데이터 드리프트 실행](https://docs.microsoft.com/python/api/azureml-contrib-datadrift/azureml.contrib.datadrift.datadriftdetector%28class%29?view=azure-ml-py#run-target-date--services--compute-target-name-none--create-compute-target-false--feature-list-none--drift-threshold-none-) 을 제출할 수 있습니다. `DataDriftDetector` 실행의 일부로 매개 변수를 `drift_threshold` 설정 하 여 datadriftdetector 경고를 사용 하도록 설정 합니다. [Datadrift_coefficient](#metrics) 가 지정 `drift_threshold`된 보다 위에 있으면 전자 메일이 전송 됩니다.

```python
# adhoc run today
target_date = datetime.today()

# create a new compute - creates datadrift-server
run = datadrift.run(target_date, services, feature_list=feature_list, create_compute_target=True)

# or specify existing compute cluster
run = datadrift.run(target_date, services, feature_list=feature_list, compute_target='cpu-cluster')

# show details of the data drift run
exp = Experiment(ws, datadrift._id)
dd_run = Run(experiment=exp, run_id=run)
RunDetails(dd_run).show()
```

## <a name="visualize-drift-metrics"></a>드리프트 메트릭 시각화

<a name="metrics"></a>

DataDriftDetector 실행을 제출한 후에는 데이터 드리프트 태스크에 대 한 각 실행 반복에 저장 된 드리프트 메트릭을 볼 수 있습니다.


|메트릭|Description|
--|--|
wasserstein_distance|1 차원 숫자 분포에 대해 정의 된 통계 거리입니다.|
energy_distance|1 차원 숫자 분포에 대해 정의 된 통계 거리입니다.|
datadrift_coefficient|Matthew의 상관 계수와 유사 하 게 계산 되지만이 출력은 0에서 1 사이의 실수입니다. 드리프트 컨텍스트에서 0은 드리프트를 나타내고 1은 최대 드리프트를 나타냅니다.|
datadrift_contribution|드리프트에 영향을 주는 기능의 중요 한 기능입니다.|

여러 가지 방법으로 드리프트 메트릭을 볼 수 있습니다.

* [Jupyter 위젯을 사용 합니다.](https://docs.microsoft.com/python/api/azureml-widgets/azureml.widgets?view=azure-ml-py) `RunDetails`
* [`get_metrics()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29?view=azure-ml-py#get-metrics-name-none--recursive-false--run-type-none--populate-false-) 모든`datadrift` 실행 개체에 대해 함수를 사용 합니다.
* 모델에서 Azure Portal의 메트릭을 확인 합니다.

다음 Python 예제에서는 관련 데이터 드리프트 메트릭을 그리는 방법을 보여 줍니다. 반환 된 메트릭을 사용 하 여 사용자 지정 시각화를 빌드할 수 있습니다.

```python
# start and end are datetime objects 
drift_metrics = datadrift.get_output(start_time=start, end_time=end)

# Show all data drift result figures, one per serivice.
# If setting with_details is False (by default), only the data drift magnitude will be shown; if it's True, all details will be shown.
drift_figures = datadrift.show(with_details=True)
```

![Azure Machine Learning에서 검색 한 데이터 드리프트 참조](media/how-to-monitor-data-drift/drift_show.png)


## <a name="schedule-data-drift-scans"></a>데이터 드리프트 검색 예약 

데이터 드리프트 검색을 사용 하도록 설정 하면 DataDriftDetector가 지정 된 예약 된 빈도로 실행 됩니다. Datadrift_coefficient가 지정 `drift_threshold`된에 도달 하면 예약 된 각 실행에 대 한 전자 메일이 전송 됩니다. 

```python
datadrift.enable_schedule()
datadrift.disable_schedule()
```

데이터 드리프트 탐지기 구성은 Azure Portal의 모델 정보 페이지에서 볼 수 있습니다.

![데이터 드리프트 구성 Azure Portal](media/how-to-monitor-data-drift/drift_config.png)

## <a name="view-results-in-azure-ml-workspace-ui"></a>Azure ML 작업 영역 UI에서 결과 보기

Azure ML 작업 영역 UI에서 결과를 보려면 모델 페이지로 이동 합니다. 모델의 자세히 탭에 데이터 드리프트 구성이 표시 됩니다. 이제 데이터 드리프트 메트릭을 시각화 하는 ' 데이터 드리프트 (미리 보기) ' 탭을 사용할 수 있습니다. 

![Azure Portal 데이터 드리프트](media/how-to-monitor-data-drift/drift_ui.png)

## <a name="receiving-drift-alerts"></a>드리프트 경고 수신

드리프트 계수 경고 임계값을 설정 하 고 전자 메일 주소를 제공 하 여 [Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/overview) 전자 메일 경고는 드리프트 계수가 임계값을 초과 하는 경우 자동으로 전송 됩니다. 

사용자 지정 경고 및 작업을 설정 하기 위해 모든 데이터 드리프트 메트릭은 Azure Machine Learning 서비스 작업 영역과 함께 생성 된 [Application Insights](how-to-enable-app-insights.md) 리소스에 저장 됩니다. Application Insights 쿼리에 대 한 전자 메일 경고의 링크를 따를 수 있습니다.

![데이터 드리프트 전자 메일 경고](media/how-to-monitor-data-drift/drift_email.png)

## <a name="retrain-your-model-after-drift"></a>드리프트 후 모델 다시 학습

데이터가 배포 된 모델의 성능에 부정적인 영향을 주는 경우 모델을 다시 학습 시간이 됩니다. 다음 [ 메서드는이전](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#diff-rhs-dataset--compute-target-none--columns-none-
) 학습 데이터 집합 및 새 학습 데이터 집합 간에 변경 된 내용을 초기에 제공 합니다. `diff()` 

```python
from azureml.core import Dataset

old_training_dataset.diff(new_training_dataset)
```

이전 코드의 출력에 따라 모델을 다시 학습 할 수 있습니다. 이렇게 하려면 다음 단계를 진행 합니다.

* 수집 된 데이터를 조사 하 고 데이터를 준비 하 여 새 모델을 학습 합니다.
* 학습/테스트 데이터로 분할 합니다.
* 새 데이터를 사용 하 여 모델을 다시 학습 합니다.
* 새로 생성 된 모델의 성능을 평가 합니다.
* 성능이 프로덕션 모델 보다 나은 경우 새 모델을 배포 합니다.

## <a name="next-steps"></a>다음 단계

* 데이터 드리프트 사용에 대 한 전체 예제는 [AZURE ML 데이터 드리프트 노트북](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/monitor-models/data-drift/azure-ml-datadrift.ipynb)을 참조 하세요. 이 Jupyter Notebook는 [Azure Open 데이터 집합](https://docs.microsoft.com/azure/open-datasets/overview-what-are-open-datasets) 을 사용 하 여 날씨를 예측 하 고, AKS에 배포 하 고, 데이터 드리프트를 모니터링 하는 모델을 학습 하는 방법을 보여 줍니다. 

* 데이터 드리프트가 일반 공급으로 전환 될 때 질문, 설명 또는 제안을 크게 감사 합니다. 아래의 제품 사용자 의견 단추를 사용 하세요. 
