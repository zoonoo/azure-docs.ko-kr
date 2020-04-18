---
title: Azure 기계 학습 파이프라인 예약
titleSuffix: Azure Machine Learning
description: 파이썬에 대한 Azure 기계 학습 SDK를 사용하여 Azure 기계 학습 파이프라인을 예약합니다. 예약된 파이프라인을 사용하면 데이터 처리, 교육 및 모니터링과 같은 시간 소모적인 일상적인 작업을 자동화할 수 있습니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: laobri
author: lobrien
ms.date: 11/12/2019
ms.openlocfilehash: d9e1bff3d25a978b5159d8e6ab8ab2453df77ca3
ms.sourcegitcommit: d791f8f3261f7019220dd4c2dbd3e9b5a5f0ceaf
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/18/2020
ms.locfileid: "81640508"
---
# <a name="schedule-machine-learning-pipelines-with-azure-machine-learning-sdk-for-python"></a>파이썬에 대한 Azure 기계 학습 SDK로 기계 학습 파이프라인 예약

이 문서에서는 Azure에서 실행되도록 파이프라인을 프로그래밍 방식으로 예약하는 방법을 알아봅니다. 경과 된 시간 또는 파일 시스템 변경에 따라 일정을 만들도록 선택할 수 있습니다. 시간 기반 일정을 사용하여 데이터 드리프트 모니터링과 같은 일상적인 작업을 수행할 수 있습니다. 변경 기반 일정을 사용하여 새 데이터가 업로드되거나 이전 데이터를 편집하는 등 불규칙하거나 예측할 수 없는 변경에 대응할 수 있습니다. 일정을 만드는 방법을 학습한 후 일정을 검색하고 비활성화하는 방법을 배웁니다.

## <a name="prerequisites"></a>사전 요구 사항

* Azure 구독 Azure 구독이 없는 경우 [무료 계정을](https://aka.ms/AMLFree)만듭니다.

* 파이썬용 Azure 기계 학습 SDK가 설치된 파이썬 환경입니다. 자세한 내용은 [Azure 기계 학습을 사용하여 학습 및 배포를 위해 재사용 가능한 환경 만들기 및 관리를 참조하세요.](how-to-use-environments.md)

* 게시된 파이프라인이 있는 기계 학습 작업 영역입니다. [Azure 기계 학습 SDK를 사용하여 기계 학습 파이프라인 만들기 및 실행에](how-to-create-your-first-pipeline.md)내장된 파이프라인을 사용할 수 있습니다.

## <a name="initialize-the-workspace--get-data"></a>데이터 얻기 & 작업 영역 초기화

파이프라인을 예약하려면 작업 영역, 게시된 파이프라인의 식별자 및 일정을 만들려는 실험의 이름에 대한 참조가 필요합니다. 다음 코드로 이러한 값을 얻을 수 있습니다.

```Python
import azureml.core
from azureml.core import Workspace
from azureml.pipeline.core import Pipeline, PublishedPipeline
from azureml.core.experiment import Experiment

ws = Workspace.from_config()

experiments = Experiment.list(ws)
for experiment in experiments:
    print(experiment.name)

published_pipelines = PublishedPipeline.list(ws)
for published_pipeline in  published_pipelines:
    print(f"{published_pipeline.name},'{published_pipeline.id}'")

experiment_name = "MyExperiment" 
pipeline_id = "aaaaaaaaa-bbbb-cccc-dddd-eeeeeeeeeeee" 
```

## <a name="create-a-schedule"></a>일정 만들기

파이프라인을 반복적으로 실행하려면 일정을 만듭니다. 파이프라인, `Schedule` 실험 및 트리거를 연결합니다. 트리거는 실행 사이의`ScheduleRecurrence` 대기 를 설명하는 a이거나 변경 내용을 감시할 디렉터리를 지정하는 Datastore 경로일 수 있습니다. 두 경우 모두 파이프라인 식별자와 일정을 만들 실험 의 이름이 필요합니다.

파이썬 파일 의 맨 위에서 `Schedule` `ScheduleRecurrence` 다음 클래스를 가져옵니다.

```python

from azureml.pipeline.core.schedule import ScheduleRecurrence, Schedule
```

### <a name="create-a-time-based-schedule"></a>시간 기반 일정 만들기

`ScheduleRecurrence` 생성자는 "분", "시간", "일", "주" 또는 "월" 중 하나여야 하는 필수 `frequency` 인수를 가지고 있습니다. 또한 일정 시작 사이에 `interval` 경과해야 하는 단위 `frequency` 수를 지정하는 정수 인수가 필요합니다. 선택적 인수를 사용하면 [ScheduleRecurrence SDK 문서에](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.schedule.schedulerecurrence?view=azure-ml-py)자세히 설명된 대로 시작 시간에 대해 보다 구체적으로 설명할 수 있습니다.

15분마다 실행을 시작하는 a `Schedule` 만들기:

```python
recurrence = ScheduleRecurrence(frequency="Minute", interval=15)
recurring_schedule = Schedule.create(ws, name="MyRecurringSchedule", 
                            description="Based on time",
                            pipeline_id=pipeline_id, 
                            experiment_name=experiment_name, 
                            recurrence=recurrence)
```

### <a name="create-a-change-based-schedule"></a>변경 기반 일정 만들기

파일 변경에 의해 트리거되는 파이프라인은 시간 기반 일정보다 더 효율적일 수 있습니다. 예를 들어 파일이 변경되거나 새 파일이 데이터 디렉터리에 추가될 때 전처리 단계를 수행할 수 있습니다. 데이터 스토어에 대한 변경 내용이나 데이터 스토어 내의 특정 디렉터리 내에서 변경 내용을 모니터링할 수 있습니다. 특정 디렉터리를 모니터링하는 경우 해당 디렉터리 하위 디렉터리 내에서 변경해도 실행이 _트리거되지 않습니다._

파일 반응성 `Schedule`파일을 만들려면 `datastore` [Schedule.create](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.schedule.schedule?view=azure-ml-py#create-workspace--name--pipeline-id--experiment-name--recurrence-none--description-none--pipeline-parameters-none--wait-for-provisioning-false--wait-timeout-3600--datastore-none--polling-interval-5--data-path-parameter-name-none--continue-on-step-failure-none--path-on-datastore-none---workflow-provider-none---service-endpoint-none-)에 대한 호출에서 매개 변수를 설정해야 합니다. 폴더를 모니터링하려면 인수를 `path_on_datastore` 설정합니다.

인수를 `polling_interval` 사용하면 데이터스토어에서 변경 사항을 확인하는 빈도를 분(분)으로 지정할 수 있습니다.

파이프라인이 [DataPath](https://docs.microsoft.com/python/api/azureml-core/azureml.data.datapath.datapath?view=azure-ml-py) [PipelineParameter로](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelineparameter?view=azure-ml-py)구성된 경우 `data_path_parameter_name` 인수를 설정하여 해당 변수를 변경된 파일의 이름으로 설정할 수 있습니다.

```python
datastore = Datastore(workspace=ws, name="workspaceblobstore")

reactive_schedule = Schedule.create(ws, name="MyReactiveSchedule", description="Based on time",
                            pipeline_id=pipeline_id, experiment_name=experiment_name, datastore=datastore, data_path_parameter_name="input_data")
```

### <a name="optional-arguments-when-creating-a-schedule"></a>일람표 를 만들 때 선택적 인수

앞에서 설명한 인수 외에도 `status` 비활성 일정을 만들도록 `"Disabled"` 인수를 설정할 수 있습니다. 마지막으로 파이프라인의 기본 오류 동작을 재정의하는 부울을 전달할 `continue_on_step_failure` 수 있습니다.

### <a name="use-azure-logic-apps-for-more-complex-workflows"></a>보다 복잡한 워크플로우에 Azure 논리 앱 사용

Azure Logic Apps는 보다 복잡한 워크플로를 지원하며 Azure 기계 학습 파이프라인보다 훨씬 광범위하게 통합됩니다. 자세한 [내용은 논리 앱에서 기계 학습 파이프라인 실행 트리거를](how-to-trigger-published-pipeline.md) 참조하십시오.

## <a name="view-your-scheduled-pipelines"></a>예약된 파이프라인 보기

웹 브라우저에서 Azure 기계 학습으로 이동합니다. 탐색 패널의 **끝점** 섹션에서 **파이프라인 끝점을**선택합니다. 그러면 작업 영역에 게시된 파이프라인 목록으로 이동합니다.

![AML의 파이프라인 페이지](./media/how-to-schedule-pipelines/scheduled-pipelines.png)

이 페이지에서는 작업 영역의 모든 파이프라인에 대한 요약 정보(이름, 설명, 상태 등)를 볼 수 있습니다. 파이프라인을 클릭하여 드릴인합니다. 결과 페이지에 파이프라인에 대한 자세한 정보가 있으며 개별 실행으로 드릴다운할 수 있습니다.

## <a name="deactivate-the-pipeline"></a>파이프라인 비활성화

게시되었지만 예약되지 않은 이 있는 이 `Pipeline` 있는 경우 다음을 사용 중지할 수 있습니다.

```python
pipeline = PublishedPipeline.get(ws, id=pipeline_id)
pipeline.disable()
```

파이프라인이 예약된 경우 먼저 일정을 취소해야 합니다. 포털에서 또는 실행하여 일정 식별자를 검색합니다.

```python
ss = Schedule.list(ws)
for s in ss:
    print(s)
```

비활성화하려는 작업이 `schedule_id` 완료되면 다음을 실행하십시오.

```python
def stop_by_schedule_id(ws, schedule_id):
    s = next(s for s in Schedule.list(ws) if s.id == schedule_id)
    s.disable()
    return s

stop_by_schedule_id(ws, schedule_id)
```

그런 다음 `Schedule.list(ws)` 다시 실행하면 빈 목록이 표시됩니다.

## <a name="next-steps"></a>다음 단계

이 문서에서는 파이썬용 Azure 기계 학습 SDK를 사용하여 두 가지 방법으로 파이프라인을 예약했습니다. 경과된 클럭 시간에 따라 하나의 일정이 반복됩니다. 다른 일정은 파일이 지정된 `Datastore` 저장소또는 해당 저장소의 디렉터리 내에서 수정된 경우 실행됩니다. 포털을 사용하여 파이프라인 및 개별 실행을 검사하는 방법을 살펴보는 방법을 살펴보셨습니다. 마지막으로 파이프라인 실행을 중지하도록 일정을 사용하지 않도록 설정하는 방법을 배웠습니다.

자세한 내용은 다음을 참조하세요.

> [!div class="nextstepaction"]
> [일괄 처리 채점에 Azure Machine Learning 파이프라인 사용](tutorial-pipeline-batch-scoring-classification.md)

* [파이프라인에](concept-ml-pipelines.md) 대해 자세히 알아보기
* [Jupyter를 통해 Azure 기계 학습 을 탐색하는](samples-notebooks.md) 방법에 대해 자세히 알아보기

