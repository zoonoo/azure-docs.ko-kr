---
title: ML 실행 로그 & 메트릭 모니터링 및 보기
titleSuffix: Azure Machine Learning
description: ML 실험을 모니터링 하 고 Jupyter 위젯을 사용 하 여 실행 메트릭과 Azure Machine Learning studio를 확인 합니다.
services: machine-learning
author: likebupt
ms.author: keli19
ms.reviewer: peterlu
ms.service: machine-learning
ms.subservice: core
ms.date: 07/30/2020
ms.topic: conceptual
ms.custom: how-to
ms.openlocfilehash: 47531da9c1e508281a57074df7aa10ffffe78810
ms.sourcegitcommit: 956dec4650e551bdede45d96507c95ecd7a01ec9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/09/2021
ms.locfileid: "102518741"
---
# <a name="monitor-and-view-ml-run-logs-and-metrics"></a>ML 실행 로그 및 메트릭 모니터링 및 보기

Azure Machine Learning 실행을 모니터링 하 고 로그를 보는 방법을 알아봅니다. 

실험을 실행 하면 로그 및 메트릭이 스트리밍됩니다.  또한 사용자 고유의를 추가할 수 있습니다.  방법을 알아보려면 [AZURE ML 학습 실행에서 로깅 사용](how-to-track-experiments.md)을 참조 하세요.

로그를 통해 실행에 대 한 오류 및 경고를 진단할 수 있습니다. 매개 변수 및 모델 정확도와 같은 성능 메트릭은 실행을 추적 하 고 모니터링 하는 데 도움이 됩니다.

이 문서에서는 다음 방법을 사용 하 여 로그를 보는 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * 스튜디오에서 모니터 실행
> * Jupyter Notebook 위젯을 사용 하 여 모니터 실행
> * 자동화 된 machine learning 실행 모니터링
> * 완료 시 출력 로그 보기
> * 스튜디오에서 출력 로그 보기

실험을 관리 하는 방법에 대 한 일반적인 정보는 [학습 실행 시작, 모니터링 및 취소](how-to-manage-runs.md)를 참조 하세요.

## <a name="monitor-runs-using-the-jupyter-notebook-widget"></a>Jupyter 노트북 위젯을 사용 하 여 실행 모니터링

**ScriptRunConfig** 메서드를 사용 하 여 실행을 제출 하는 경우 [Jupyter 위젯을](/python/api/azureml-widgets/azureml.widgets)사용 하 여 실행 진행률을 볼 수 있습니다. 실행 제출과 마찬가지로, 위젯은 비동기적이며 작업이 완료될 때까지 10~15초 간격으로 라이브 업데이트를 제공합니다.

실행이 완료될 때까지 기다리는 동안 Jupyter 위젯을 봅니다.
    
```python
from azureml.widgets import RunDetails
RunDetails(run).show()
```

![Jupyter 노트북 위젯의 스크린샷](./media/how-to-track-experiments/run-details-widget.png)

작업 영역에서 동일한 표시에 대한 링크를 가져올 수도 있습니다.

```python
print(run.get_portal_url())
```

## <a name="monitor-automated-machine-learning-runs"></a>자동화 된 machine learning 실행 모니터링

자동화 된 기계 학습 실행의 경우 이전 실행에서 차트에 액세스 하려면를 `<<experiment_name>>` 적절 한 실험 이름으로 바꿉니다.

```python
from azureml.widgets import RunDetails
from azureml.core.run import Run

experiment = Experiment (workspace, <<experiment_name>>)
run_id = 'autoML_my_runID' #replace with run_ID
run = Run(experiment, run_id)
RunDetails(run).show()
```

![자동화된 기계 학습을 위한 Jupyter Notebook 위젯](./media/how-to-track-experiments/azure-machine-learning-auto-ml-widget.png)

## <a name="show-output-upon-completion"></a>완료 시 출력 표시

**ScriptRunConfig** 를 사용하면 ```run.wait_for_completion(show_output = True)```를 사용하여 모델 학습이 완료된 시점을 표시할 수 있습니다. ```show_output``` 플래그는 자세한 정보를 출력합니다. 자세한 내용은 [로깅 사용 방법](how-to-track-experiments.md#scriptrun-logs)의 ScriptRunConfig 섹션을 참조 하세요.

<a id="queryrunmetrics"></a>

## <a name="view-run-metrics"></a>실행 메트릭 보기

## <a name="via-the-sdk"></a>SDK를 통해
```run.get_metrics()```를 사용하여 학습된 모델의 메트릭을 볼 수 있습니다. 아래 예제를 참조하세요. 

```python
from azureml.core import Run
run = Run.get_context()
run.log('metric-name', metric_value)

metrics = run.get_metrics()
# metrics is of type Dict[str, List[float]] mapping mertic names
# to a list of the values for that metric in the given run.

metrics.get('metric-name')
# list of metrics in the order they were recorded
```

<a name="view-the-experiment-in-the-web-portal"></a>

## <a name="view-run-records-in-the-studio"></a>스튜디오에서 실행 레코드 보기

[Azure Machine Learning studio](https://ml.azure.com)에서 기록 된 메트릭을 포함 하 여 완료 된 실행 레코드를 찾아볼 수 있습니다.

**실험** 탭으로 이동 합니다. 실험 전체에서 작업 영역의 모든 실행을 보려면 **모든 실행** 탭을 선택 합니다. 상단 메뉴 모음에서 실험 필터를 적용 하 여 특정 실험의 실행을 드릴 다운할 수 있습니다.

개별 실험 보기의 경우 **모든 실험** 탭을 선택 합니다. 실험 실행 대시보드에서 각 실행에 대 한 추적 된 메트릭과 로그를 볼 수 있습니다. 

실행 목록 테이블을 편집 하 여 여러 실행을 선택 하 고 실행에 대 한 마지막, 최소 또는 최대 로그 값을 표시할 수도 있습니다. 여러 실행에서 기록 된 메트릭 값과 집계를 비교 하려면 차트를 사용자 지정 합니다. 

![Azure Machine Learning 스튜디오에서 세부 정보 실행](media/how-to-track-experiments/experimentation-tab.gif)

### <a name="view-log-files-for-a-run"></a>실행에 대 한 로그 파일 보기 

로그 파일은 Azure ML 워크 로드를 디버깅 하기 위한 필수 리소스입니다. 로그 및 출력을 보려면 특정 실행으로 드릴 다운 합니다.  

1. **실험** 탭으로 이동 합니다.
1. 특정 실행에 대 한 runID를 선택 합니다.
1. 페이지 맨 위에서 **출력 및 로그를** 선택 합니다.

:::image type="content" source="media/how-to-monitor-view-training-logs/view-logs.png" alt-text="실행의 출력 및 로그 섹션 스크린샷":::

아래 표에는이 섹션에서 볼 수 있는 폴더의 로그 파일 내용이 나와 있습니다.

> [!NOTE]
> 모든 실행에 대 한 모든 파일이 반드시 표시 되는 것은 아닙니다. 예를 들어 20_image_build_log * .txt는 새 이미지가 작성 된 경우에만 나타납니다 (예: 환경을 변경 하는 경우).

#### <a name="azureml-logs-folder"></a>`azureml-logs` 폴더

|파일  |Description  |
|---------|---------|
|20_image_build_log.txt     | 훈련 환경에 대 한 Docker 이미지 빌드 로그 (선택 사항, 실행 당 하나) 환경을 업데이트할 때만 적용 됩니다. 그렇지 않으면 AML에서 캐시 된 이미지를 다시 사용 합니다. 성공 하는 경우 해당 이미지에 대 한 이미지 레지스트리 세부 정보를 포함 합니다.         |
|55_azureml 실행-<node_id # C1.txt     | 호스트 도구의 stdout/stderr 로그가 노드당 하나입니다. 이미지를 계산 대상으로 끌어옵니다. 이 로그는 계산 리소스를 보호 한 경우에만 표시 됩니다.         |
|65_job_prep-<node_id # C1.txt     |   작업 준비 스크립트의 stdout/stderr 로그 (노드당 하나)입니다. 코드를 다운로드 하 여 대상 및 데이터 저장소 (요청 된 경우)를 계산 합니다.       |
|70_driver_log (_x) .txt      |  AML 컨트롤 스크립트 및 고객 교육 스크립트의 stdout/stderr 로그 (프로세스 당 하나) **스크립트의 표준 출력입니다. 코드의 로그 (예: print 문)가 표시 되는 위치입니다.** 대부분의 경우 여기에서 로그를 모니터링 하 게 됩니다.       |
|70_mpi_log.txt     |   MPI 프레임 워크 로그, 선택 사항, 실행 당 하나 MPI 실행에만 해당 됩니다.   |
|75_job_post-<node_id # C1.txt     |  작업 릴리스 스크립트의 stdout/stderr 로그 (노드당 하나)입니다. 로그를 보내고 계산 리소스를 Azure로 다시 릴리스 합니다.        |
|process_info.js      |   노드에서 실행 중인 프로세스를 표시 합니다.  |
|process_status.js      | 프로세스 상태를 표시 합니다. 즉, 프로세스가 시작 되지 않은 경우에는 실행 중 또는 완료 됨입니다.         |

#### <a name="logs--azureml-folder"></a>`logs > azureml` 폴더

|파일  |Description  |
|---------|---------|
|110_azureml .log      |         |
|job_prep_azureml .log     |   작업 준비에 대 한 시스템 로그        |
|job_release_azureml .log     | 작업 릴리스에 대 한 시스템 로그        |

#### <a name="logs--azureml--sidecar--node_id-folder"></a>`logs > azureml > sidecar > node_id` 폴더

사이드카를 사용 하도록 설정 하면 사이드카 컨테이너 내에서 작업 준비 및 작업 해제 스크립트가 실행 됩니다.  각 노드에 대해 하나의 폴더가 있습니다. 

|파일  |Description  |
|---------|---------|
|start_cms.txt     |  사이드카 컨테이너가 시작 될 때 시작 되는 프로세스의 로그       |
|prep_cmd.txt      |   가 실행 될 때 입력 한 ContextManagers 로그 `job_prep.py` (이 중 일부는로 스트리밍 됨 `azureml-logs/65-job_prep` )       |
|release_cmd.txt     |  가 실행 될 때 종료 되는 ComtextManagers 로그 `job_release.py`        |

#### <a name="other-folders"></a>기타 폴더

다중 계산 클러스터에 대 한 작업 교육의 경우 각 노드 IP의 로그가 표시 됩니다. 각 노드의 구조는 단일 노드 작업과 동일 합니다. 전체 실행, stderr 및 stdout 로그에 대해 하나의 추가 로그 폴더가 있습니다.

자동 Ml 또는 교육 작업을 실행 하는 Docker 컨테이너와 같은 학습 중 다양 한 원본에서 정보를 기록 Azure Machine Learning 합니다. 이러한 로그는 대부분 문서화 되어 있지 않습니다. 문제가 발생하여 Microsoft 지원에 문의하는 경우 이러한 로그를 사용하여 문제를 해결할 수 있습니다.

## <a name="monitor-a-compute-cluster"></a>계산 클러스터 모니터링

브라우저에서 특정 계산 대상에 대 한 실행을 모니터링 하려면 다음 단계를 사용 합니다.

1. [Azure Machine Learning studio](https://ml.azure.com/)에서 작업 영역을 선택 하 고 페이지의 왼쪽에서 __계산__ 을 선택 합니다.

1. 학습에 사용되는 컴퓨팅 대상 목록을 표시하려면 __학습 클러스터__ 를 선택합니다. 그런 다음 클러스터를 선택합니다.

    ![학습 클러스터 선택](./media/how-to-track-experiments/select-training-compute.png)

1. __실행__ 을 선택합니다. 이 클러스터를 사용하는 실행 목록이 표시됩니다. 특정 실행에 대한 세부 정보를 보려면 __실행__ 열의 링크를 사용합니다. 실험에 대한 세부 정보를 보려면 __실험__ 열의 링크를 사용합니다.

    ![학습 클러스터에 대한 실행 선택](./media/how-to-track-experiments/show-runs-for-compute.png)
    
    > [!TIP]
    > 학습 계산 대상은 공유 리소스 이므로 지정 된 시간에 큐에 대기 중이거나 활성 상태가 여러 개 있을 수 있습니다.
    > 
    > 실행에는 자식 실행이 포함될 수 있으므로 하나의 학습 작업으로 여러 항목이 생성될 수 있습니다.

실행이 완료되면 이 페이지에 더 이상 표시되지 않습니다. 완료된 실행에 대한 정보를 보려면 스튜디오의 __실험__ 섹션을 방문하여 실험 및 실행을 선택합니다. 자세한 내용은 [완료 된 실행에 대 한 메트릭 보기](#view-the-experiment-in-the-web-portal)섹션을 참조 하세요.


## <a name="next-steps"></a>다음 단계

Azure Machine Learning를 사용 하는 방법을 알아보려면 다음 단계를 수행 하세요.

* [Azure Machine Learning 디자이너에서 실험을 추적 하 고 로그를 사용 하도록 설정](how-to-track-designer-experiments.md)하는 방법을 알아봅니다.

* 최상의 모델을 등록하고 배포하는 방법에 대한 예제는 [Azure Machine Learning으로 이미지 분류 모델 학습](tutorial-train-models-with-aml.md) 자습서를 참조하세요.
