---
title: AKS 배포에서 데이터 드리프트 (미리 보기)를 검색 합니다.
titleSuffix: Azure Machine Learning service
description: Azure Kubernetes Service에서 데이터 드리프트 Azure Machine Learning 서비스에서 모델 배포를 검색 하는 방법에 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: copeters
author: cody-dkdc
ms.date: 06/20/2019
ms.openlocfilehash: c446c8236ca64948f0bb6a8354a83579cc6ff24c
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/28/2019
ms.locfileid: "67443950"
---
# <a name="detect-data-drift-preview-on-models-deployed-to-azure-kubernetes-service"></a>Azure Kubernetes Service에 배포 된 모델에서 데이터 드리프트 (미리 보기)를 검색 합니다.
이 문서에서는 배포 된 모델의 데이터 학습 데이터 집합 및 유추 간의 데이터 드리프트를 모니터링 하는 방법을 알아봅니다. 

## <a name="what-is-data-drift"></a>데이터 드리프트 란?

데이터 드리프트를 개념 드리프트 라고도 모델 정확도 시간에 지나면서 가치가 저하 되므로 여기서는 가장 중요 한 이유 중 하나입니다. 프로덕션 환경에서 모델을 제공 하는 데이터 모델의 학습에 사용 되는 데이터와 다른 경우 발생 합니다. Azure Machine Learning 서비스는 데이터 드리프트를 모니터링할 수 있습니다 및 드리프트가 검색 되 면 서비스에 전자 메일 경고를 보낼 수 있습니다.  

> [!Note]
> 이 서비스 (미리 보기)는 구성 옵션의 제한. 참조 하세요 우리의 [API 설명서](https://docs.microsoft.com/python/api/azureml-contrib-datadrift/?view=azure-ml-py) 하 고 [릴리스](azure-machine-learning-release-notes.md) 세부 정보 및 업데이트에 대 한 합니다. 

## <a name="what-can-i-monitor"></a>무엇을 모니터링할 수 있습니까?
Azure Machine Learning 서비스를 사용 하 여 AKS에 배포 된 모델에 대 한 입력을 모니터링할 수 있으며 모델의 학습 데이터 집합에이 데이터를 비교할 수 있습니다. 정기적으로 유추 데이터가 [스냅숏 생성 및 프로 파일링](how-to-explore-prepare-data.md), 다음 데이터 드리프트 분석을 생성 하기 위해 기준 데이터 집합에 대해 계산 하는: 

+ 데이터 드리프트를 드리프트 계수 호출의 크기를 측정 합니다.
+ 측정값 데이터 기여 기능 데이터 드리프트 발생을 알리는 기능별 드리프트 합니다.
+ 측정값에는 메트릭을 거리입니다. 현재 Wasserstein 및 에너지 거리 계산 됩니다.
+ 기능의 배포를 측정합니다. 현재 커널 밀도 예측 및 히스토그램입니다.
+ 데이터 경고 전자 메일을 통해 드리프트를 보냅니다.

이러한 메트릭을 계산 하는 방법에 대 한 내용은 참조는 [데이터 드리프트 개념](concept-data-drift.md) 문서.

## <a name="prerequisites"></a>필수 조건

- Azure 구독이 없는 경우 시작하기 전에 체험 계정을 만듭니다. [Azure Machine Learning Service의 평가판 또는 유료 버전](https://aka.ms/AMLFree)을 지금 사용해 보세요.

- Azure Machine Learning 서비스 작업 영역 및 Python용 Azure Machine Learning SDK가 설치되어 있어야 합니다. [개발 환경 구성 방법](how-to-configure-environment.md) 문서를 사용하여 이러한 필수 구성 요소를 충족하는 방법을 알아보세요.

- [환경을 설정](how-to-configure-environment.md), 한 다음 데이터 드리프트 다음 명령을 사용 하 여 SDK를 설치 합니다.

    ```
    pip install azureml-contrib-datadrift
    ```

- 만들기는 [데이터 집합](how-to-create-register-datasets.md) 모델의 학습 데이터에서.

- 학습 데이터 집합을 지정할 때 [등록](concept-model-management-and-deployment.md) 모델입니다. 다음 예제는 `datasets` 학습 데이터 집합을 지정 하려면 매개 변수:

    ```python
    model = Model.register(model_path=model_file,
                        model_name=model_name,
                        workspace=ws,
                        datasets=datasets)

    print(model_name, image_name, service_name, model)
    ```

- [모델 데이터 수집을 사용 하도록 설정](how-to-enable-data-collection.md) 에서 모델의 AKS 배포에서 데이터를 수집 하 고 데이터를 확인 하려면 수집 되는 `modeldata` blob 컨테이너입니다.

## <a name="import-dependencies"></a>종속성 가져오기 
이 가이드에서 사용 되는 종속성을 가져옵니다.

```python
# Azure ML service packages 
from azureml.core import Experiment, Run, RunDetails
from azureml.contrib.datadrift import DataDriftDetector, AlertConfiguration
``` 

## <a name="configure-data-drift"></a>데이터 드리프트를 구성 합니다. 

다음 Python 예제를 구성 하는 방법을 보여 줍니다는 `DataDriftDetector` 개체:

```python
# if email address is specified, setup AlertConfiguration
alert_config = AlertConfiguration('your_email@contoso.com')

# create a new DatadriftDetector object
datadrift = DataDriftDetector.create(ws, model.name, model.version, services, frequency="Day", alert_config=alert_config)
    
print('Details of Datadrift Object:\n{}'.format(datadrift))
```

자세한 내용은 참조는 `[DataDrift](https://docs.microsoft.com/python/api/azureml-contrib-datadrift/?view=azure-ml-py)` 클래스 참조 문서입니다.

## <a name="submit-a-datadriftdetector-run"></a>DataDriftDetector 실행 제출

사용 하 여 합니다 `DataDriftDetector` 구성 개체를 제출할 수 있습니다를 [실행 하는 데이터 드리프트](https://docs.microsoft.com/python/api/azureml-contrib-datadrift/azureml.contrib.datadrift.datadriftdetector%28class%29?view=azure-ml-py#run-target-date--services--compute-target-name-none--create-compute-target-false--feature-list-none--drift-threshold-none-) 모델에 대 한 지정된 된 날짜에 합니다. 

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

## <a name="visualize-drift-metrics"></a>드리프트 메트릭을 시각화합니다

다음 Python 예제에서는 관련 데이터 드리프트 메트릭을 그리는 방법을 보여 줍니다. 반환 된 메트릭을 사용 하 여 사용자 지정 시각화를 만들 수 있습니다.

```python
# start and end are datetime objects 
drift_metrics = datadrift.get_output(start_time=start, end_time=end)

# Show all data drift result figures, one per serivice.
# If setting with_details is False (by default), only the data drift magnitude will be shown; if it's True, all details will be shown.
drift_figures = datadrift.show(with_details=True)
```

![Azure Machine Learning에서 검색 데이터 드리프트를 참조 하세요.](media/how-to-monitor-data-drift/drift_show.png)

계산 되는 메트릭에 대 한 자세한 내용은 참조는 [데이터 드리프트 개념](concept-data-drift.md) 문서.

## <a name="schedule-data-drift-scans"></a>일정 데이터 드리프트 검색 

데이터 드리프트 검색을 사용 하도록 설정 하면는 DataDriftDetector 지정, 예약 된 빈도로 실행 됩니다. 드리프트 계수를 지정 된 임계값을 초과 하는 경우 전자 메일이 전송 됩니다. 

```python
datadrift.enable_schedule()
datadrift.disable_schedule()
```

Azure portal에서 모델 세부 정보 페이지에서 데이터 드리프트 감지기의 구성을 볼 수 있습니다.

![Azure portal 데이터 드리프트 구성](media/how-to-monitor-data-drift/drift_config.png)

## <a name="view-results-in-azure-ml-workspace-ui"></a>Azure ML 작업 영역 UI에서 결과 보기

Azure ML 작업 영역 UI에 결과 보려면 모델 페이지로 이동 합니다. 모델의 세부 정보 탭에서 데이터 드리프트 구성이 표시 됩니다. ' 데이터 드리프트 (미리 보기)' 탭 데이터 드리프트 메트릭을 시각화 출시 되었습니다. 

![Azure portal 데이터 드리프트](media/how-to-monitor-data-drift/drift_ui.png)

## <a name="receiving-drift-alerts"></a>드리프트 경고 수신

경고 임계값 및 전자 메일 주소를 제공 하 여 드리프트 계수를 설정 하는 [Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/overview) 드리프트 계수는 임계값을 초과 하는 때마다 전자 메일 알림을 자동으로 전송 됩니다. 설정 사용자 지정 경고 및 작업을 하기 위해, 모든 데이터 드리프트 메트릭은 Azure Machine Learning 서비스 작업 영역은 함께 만든 Application Insights 리소스에 저장 됩니다. Application Insights 쿼리를 전자 메일 경고의 링크를 따르면 됩니다.

![데이터 드리프트 전자 메일 경고](media/how-to-monitor-data-drift/drift_email.png)

## <a name="next-steps"></a>다음 단계

* 데이터 드리프트를 사용 하는 전체 예제를 보려면 합니다 [Azure ML 데이터 드리프트 notebook](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/data-drift/azure-ml-datadrift.ipynb)합니다. 이 Jupyter Notebook 사용을 보여 줍니다.는 [Azure 공개 데이터 집합](https://docs.microsoft.com/azure/open-datasets/overview-what-are-open-datasets) 날씨 예측 모델을 학습 하려면 AKS에 배포 및 데이터 드리프트를 모니터링 합니다. 

* 크게 주시면 사용자 질문, 의견 또는 제안 데이터 드리프트 가용성 일반 방향으로 이동 합니다. 아래 제품 피드백 단추를 사용 합니다. 
