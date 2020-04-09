---
title: ML 파이프라인이란?
titleSuffix: Azure Machine Learning
description: 이 문서에서는 파이썬용 Azure 기계 학습 SDK로 빌드할 수 있는 ML(기계 학습) 파이프라인의 이점을 알아봅니다. 기계 학습 파이프라인은 데이터 과학자가 기계 학습 워크플로를 구축, 최적화 및 관리하는 데 사용됩니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: laobri
author: lobrien
ms.date: 04/01/2020
ms.openlocfilehash: 0cefa78b6f52cc67df8817f68a9b793ab86b2a7f
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/08/2020
ms.locfileid: "80878581"
---
# <a name="what-are-azure-machine-learning-pipelines"></a>Azure 기계 학습 파이프라인이란 무엇입니까?

Azure 기계 학습 파이프라인을 사용하면 기계 학습 프로젝트에서 워크플로를 만들 수 있습니다. 이러한 워크플로에는 다음과 같은 여러 가지 이점이 있습니다. 

+ 단순성
+ 속도
+ 반복성
+ 유연성
+ 버전 전환 및 추적
+ 모듈화 
+ 품질 보증
+ 비용 관리

이러한 이점은 기계 학습 프로젝트가 순수 탐색을 넘어 반복으로 전환되는 즉시 중요해집니다. 간단한 1단계 파이프라인도 유용할 수 있습니다. 기계 학습 프로젝트는 종종 복잡한 상태에 있으며 단일 워크플로우의 정확한 달성을 사소한 프로세스로 만드는 데 큰 위안이 될 수 있습니다.

[첫 번째 파이프라인을 만드는](how-to-create-your-first-pipeline.md)방법에 대해 알아봅니다.

![Azure 기계 학습의 기계 학습 파이프라인](./media/concept-ml-pipelines/pipeline-flow.png)

<a name="compare"></a>
### <a name="which-azure-pipeline-technology-should-i-use"></a>어떤 Azure 파이프라인 기술을 사용해야 합니까?

Azure 클라우드는 각각 다른 용도의 여러 다른 파이프라인을 제공합니다. 다음 표에는 다양한 파이프라인과 파이프라인에 사용되는 파이프라인이 나열되어 있습니다.

| 시나리오 | 기본 페르소나 | Azure 오퍼링 | OSS 오퍼링 | 정식 파이프 | 강점 | 
| -------- | --------------- | -------------- | ------------ | -------------- | --------- | 
| 모델 오케스트레이션(기계 학습) | 데이터 과학자 | Azure Machine Learning 파이프라인 | 쿠베플로우 파이프라인 | 데이터-> 모델 | 배포, 캐싱, 코드 우선, 재사용 | 
| 데이터 오케스트레이션(데이터 준비) | 데이터 엔지니어 | [Azure Data Factory 파이프라인](https://docs.microsoft.com/azure/data-factory/concepts-pipelines-activities) | Apache Airflow | 데이터 -> 데이터 | 강하게 입력된 움직임. 데이터 중심 활동. |
| 코드 & 앱 오케스트레이션(CI/CD) | 앱 개발자 / 옵스 | [Azure 데브옵스 파이프라인](https://azure.microsoft.com/services/devops/pipelines/) | Jenkins | 코드 + 모델 -> 앱/서비스 | 대부분의 개방적이고 유연한 활동 지원, 승인 대기열, 게이팅 단계 | 


## <a name="what-can-azure-ml-pipelines-do"></a>Azure ML 파이프라인은 무엇을 할 수 있습니까?

Azure 기계 학습 파이프라인은 전체 기계 학습 작업의 독립적으로 실행 가능한 워크플로입니다. 하위 작업은 파이프라인 내에서 일련의 단계로 캡슐화됩니다. Azure 기계 학습 파이프라인은 Python 스크립트를 호출하는 파이프라인처럼 간단할 수 있으므로 거의 모든 작업을 수행할 _수 있습니다._ 파이프라인은 다음과 같은 기계 학습 작업에 중점을 _두어야 합니다._

+ 데이터 준비(가져오기, 유효성 검사 및 정리, 마이닝/변환, 정규화 및 준비 포함)
+ 학습 구성(인수 매개 변수화, 파일 경로 및 구성 로깅/보고 포함)
+ 효율적이고 반복적으로 교육 및 검증. 효율성은 특정 데이터 하위 집합, 다른 하드웨어 컴퓨팅 리소스, 분산 처리 및 진행률 모니터링을 지정할 때 발생할 수 있습니다.
+ 배포(버전 관리, 크기 조정, 프로비저닝 및 액세스 제어 포함) 

독립적인 단계를 통해 여러 데이터 과학자가 컴퓨팅 리소스에 과부하없이 동시에 동일한 파이프라인에서 작업할 수 있습니다. 또한 별도의 단계를 수행하면 각 단계에 대해 서로 다른 계산 유형/크기를 쉽게 사용할 수 있습니다.

파이프라인 설계 후에는 종종 파이프라인의 학습 루프를 미세 조정합니다. 파이프라인을 다시 실행하면 실행이 업데이트된 학습 스크립트와 같이 다시 실행해야 하는 단계로 이동합니다. 다시 실행할 필요가 없는 단계는 건너뜁니다. 단계의 성취에 사용되는 변경되지 않은 스크립트에도 동일한 분석이 적용됩니다. 이 재사용 기능은 기본 데이터가 변경되지 않은 경우 데이터 수집 및 변환과 같이 비용이 많이 들고 시간이 많이 소요되는 단계를 실행하지 않도록 하는 데 도움이 됩니다.

Azure 기계 학습을 사용하면 파이프라인의 각 단계에 대해 PyTorch 또는 TensorFlow와 같은 다양한 도구 키트 및 프레임워크를 사용할 수 있습니다. Azure는 사용하는 다양한 [계산 대상을](concept-azure-machine-learning-architecture.md) 조정하므로 중간 데이터를 다운스트림 계산 대상과 공유할 수 있습니다.

Azure 포털 또는 [작업 영역 방문 페이지(미리 보기)에서](https://ml.azure.com) [파이프라인 실험에 대한 메트릭을](https://docs.microsoft.com/azure/machine-learning/how-to-track-experiments) 직접 추적할 수 있습니다. 파이프라인이 게시된 후 REST 끝점을 구성하여 모든 플랫폼 또는 스택에서 파이프라인을 다시 실행할 수 있습니다.

즉, 기계 학습 수명 주기의 모든 복잡한 작업은 파이프라인을 통해 도움이 될 수 있습니다. 다른 Azure 파이프라인 기술에는 고유한 강점이 있습니다. [Azure Data Factory 파이프라인은](https://docs.microsoft.com/azure/data-factory/concepts-pipelines-activities) 데이터 작업에 탁월하며 [Azure 파이프라인은](https://azure.microsoft.com/services/devops/pipelines/) 지속적인 통합 및 배포에 적합한 도구입니다. 그러나 기계 학습에 중점을 둔 경우 Azure 기계 학습 파이프라인이 워크플로 요구 사항에 가장 적합한 선택이 될 수 있습니다. 

## <a name="what-are-azure-ml-pipelines"></a>Azure ML 파이프라인이란 무엇입니까?

Azure ML 파이프라인은 정렬된 단계 시퀀스를 사용하여 전체 논리 워크플로를 수행합니다. 각 단계는 개별 처리 작업입니다. 파이프라인은 Azure 기계 학습 [실험의](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment.experiment?view=azure-ml-py)컨텍스트에서 실행됩니다.

ML 프로젝트의 초기 단계에서Azure 작업 영역 및 리소스 구성, 데이터 준비, 실행 구성, 학습 및 유효성 검사의 모든 작업을 수행하는 단일 Jupyter 노트북 또는 Python 스크립트를 사용해도 괜찮습니다. 그러나 함수와 클래스가 단일 명령 코드 블록보다 빠르게 선호되는 것처럼 ML 워크플로는 모놀리식 노트북이나 스크립트보다 빠르게 선호됩니다. 

ML 작업을 모듈화함으로써 파이프라인은 구성 요소가 "한 가지 를 잘 수행해야 한다"는 컴퓨터 과학 명령어를 지원합니다. 모듈화는 팀에서 프로그래밍할 때 프로젝트 성공에 매우 중요하지만, 혼자 작업할 때에도 작은 ML 프로젝트에도 각각 복잡성이 많은 별도의 작업이 포함됩니다. 작업에는 작업 영역 구성 및 데이터 액세스, 데이터 준비, 모델 정의 및 구성, 배포가 포함됩니다. 하나 이상의 작업의 출력이 다른 작업에 입력을 형성하는 동안 한 작업의 정확한 구현 세부 사항은 기껏해야 다음 작업의 관련이 없는 산만입니다. 최악의 경우 한 작업의 계산 상태가 다른 작업의 버그를 일으킬 수 있습니다. 

### <a name="analyzing-dependencies"></a>종속성 분석

대부분의 프로그래밍 에코시스템에는 리소스, 라이브러리 또는 컴파일 종속성을 오케스트레이션하는 도구가 있습니다. 일반적으로 이러한 도구는 파일 타임스탬프를 사용하여 종속성을 계산합니다. 파일이 변경되면 파일과 해당 종속 파일만 업데이트됩니다(다운로드, 다시 컴파일 또는 패키지). Azure ML 파이프라인은 이 개념을 크게 확장합니다. 기존 빌드 도구와 마찬가지로 파이프라인은 단계 간 종속성을 계산하고 필요한 재계산만 수행합니다. 

Azure ML 파이프라인의 종속성 분석은 단순한 타임스탬프보다 더 정교합니다. 모든 단계는 다른 하드웨어 및 소프트웨어 환경에서 실행될 수 있습니다. 데이터 준비는 시간이 많이 걸리는 프로세스일 수 있지만 강력한 GPU가 있는 하드웨어에서 실행할 필요가 없으며, 특정 단계에 OS 관련 소프트웨어가 필요할 수 있으며 분산 교육을 사용할 수 있습니다. 리소스 최적화를 위한 비용 절감은 상당할 수 있지만 하드웨어 및 소프트웨어 리소스의 다양한 변형을 수동으로 저글링하는 것은 부담스러울 수 있습니다. 단계 간에 전송하는 데이터에서 실수를 하지 않고 모든 작업을 수행하는 것이 더욱 어렵습니다. 

파이프라인은 이 문제를 해결합니다. Azure 기계 학습은 파이프라인 단계 간의 모든 종속성을 자동으로 오케스트레이션합니다. 이 오케스트레이션에는 Docker 이미지를 위아래로 회전하고, 컴퓨팅 리소스를 첨부 및 분리하고, 일관되고 자동적인 방식으로 단계 간에 데이터를 이동하는 것이 포함될 수 있습니다.

### <a name="reusing-results"></a>결과 재사용

또한 단계의 출력을 선택한 경우 다시 사용할 수 있습니다. 재사용을 가능성으로 지정하고 재계산을 트리거하는 업스트림 종속성이 없는 경우 파이프라인 서비스는 단계 결과의 캐시된 버전을 사용합니다. 이러한 재사용은 개발 시간을 크게 단축시킬 수 있습니다. 복잡한 데이터 준비 작업이 있는 경우 반드시 필요한 것보다 더 자주 다시 실행할 수 있습니다. 파이프라인은 이러한 걱정을 덜어줍니다: 필요한 경우 단계가 실행되며, 그렇지 않을 경우 실행되지 않습니다.

이 모든 종속성 분석, 오케스트레이션 및 활성화는 [파이프라인](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipeline(class)?view=azure-ml-py) 개체를 인스턴스화하고 `Experiment`에 전달한 다음 `submit()`을 호출할 때 Azure 기계 학습에서 처리됩니다. 

### <a name="coordinating-the-steps-involved"></a>관련 단계 조정

개체를 `Pipeline` 만들고 실행하면 다음과 같은 상위 수준 단계가 발생합니다.

+ 각 단계에 대해 서비스는 다음에 대한 요구 사항을 계산합니다.
    + 하드웨어 컴퓨팅 리소스
    + OS 리소스(도커 이미지)
    + 소프트웨어 리소스(Conda / 가상렌프 종속성)
    + 데이터 입력 
+ 서비스는 단계 간의 종속성을 결정하여 동적 실행 그래프를 생성합니다.
+ 실행 그래프의 각 노드가 실행되는 경우:
    + 서비스는 필요한 하드웨어 및 소프트웨어 환경을 구성합니다(기존 리소스 재사용).
    + 이 단계가 실행되어 포함된 `Experiment` 개체에 로깅 및 모니터링 정보를 제공합니다.
    + 단계가 완료되면 출력이 다음 단계의 입력으로 준비되거나 스토리지에 기록됩니다.
    + 더 이상 필요하지 않은 리소스가 최종 화되고 분리됩니다.

![파이프라인 단계](./media/concept-ml-pipelines/run_an_experiment_as_a_pipeline.png)

## <a name="building-pipelines-with-the-python-sdk"></a>파이썬 SDK로 파이프라인 구축

Azure [기계 학습 파이썬 SDK에서](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py)파이프라인은 `azureml.pipeline.core` 모듈에 정의된 파이썬 개체입니다. [파이프라인](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipeline%28class%29?view=azure-ml-py) 개체에는 하나 이상의 PipelineStep 개체의 정렬된 시퀀스가 포함되어 [있습니다.](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.builder.pipelinestep?view=azure-ml-py) 클래스는 `PipelineStep` 추상적이며 실제 단계는 [추정단계,](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.estimatorstep?view=azure-ml-py) [PythonScriptStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.pythonscriptstep?view=azure-ml-py)또는 [DataTransferStep과](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.datatransferstep?view=azure-ml-py)같은 하위 클래스입니다. [ModuleStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.modulestep?view=azure-ml-py) 클래스는 파이프라인 간에 공유할 수 있는 재사용 가능한 단계 시퀀스를 보유합니다. A는 `Pipeline` `Experiment`의 일부로 실행됩니다.

Azure ML 파이프라인은 Azure 기계 학습 작업 영역과 연결되고 파이프라인 단계는 해당 작업 영역 내에서 사용할 수 있는 계산 대상과 연결됩니다. 자세한 내용은 [Azure 포털에서 Azure 기계 학습 작업 영역 만들기 및 관리](https://docs.microsoft.com/azure/machine-learning/how-to-manage-workspace) 또는 Azure 기계 [학습에서 계산 대상이란 무엇입니까?](https://docs.microsoft.com/azure/machine-learning/concept-compute-target)

Azure 기계 학습에서 계산 대상은 ML 단계가 발생하는 환경입니다. 소프트웨어 환경은 원격 VM, Azure 기계 학습 계산, Azure Databricks, Azure 일괄 처리 등일 수 있습니다. GPU 지원, 메모리, 저장소 등에 따라 하드웨어 환경도 크게 달라질 수 있습니다. 각 단계에 대한 계산 대상을 지정하여 비용을 세밀하게 제어할 수 있습니다. 프로젝트의 특정 작업, 데이터 볼륨 및 성능 요구 사항에 대해 더 많거나 덜 강력한 리소스를 사용할 수 있습니다. 

## <a name="building-pipelines-with-the-designer"></a>디자이너와 파이프라인 구축

시각적 디자인 표면을 선호하는 개발자는 Azure 기계 학습 디자이너를 사용하여 파이프라인을 만들 수 있습니다. 작업 영역의 홈페이지의 **디자이너** 선택에서 이 도구에 액세스할 수 있습니다.  디자이너를 사용하면 단계단계를 설계 표면에 끌어놓을 수 있습니다. 신속한 개발을 위해 ML 작업의 스펙트럼에 걸쳐 기존 모듈을 사용할 수 있습니다. 기존 모듈은 데이터 변환에서 알고리즘 선택, 교육, 배포에 이르기까지 모든 것을 다룹니다. 또는 Python 스크립트에 정의 된 자신의 단계를 결합하여 완전히 사용자 정의 파이프 라인을 만들 수 있습니다.

파이프라인을 시각적으로 디자인하면 단계의 입력과 출력이 눈에 띄게 표시됩니다. 데이터 연결을 끌어서 놓을 수 있으므로 파이프라인의 데이터 흐름을 신속하게 이해하고 수정할 수 있습니다.
 
![Azure Machine Learning 디자이너 예](./media/concept-designer/designer-drag-and-drop.gif)

### <a name="understanding-the-execution-graph"></a>실행 그래프 이해

파이프라인 내의 단계에는 다른 단계에 대한 종속성이 있을 수 있습니다. Azure ML 파이프라인 서비스는 이러한 종속성을 분석하고 오케스트레이션하는 작업을 수행합니다. 결과 "실행 그래프"의 노드는 처리 단계입니다. 각 단계는 하드웨어와 소프트웨어의 특정 조합을 만들거나 다시 사용하고 캐시된 결과를 재사용하는 등의 포함될 수 있습니다. 이 실행 그래프의 오케스트레이션 및 최적화는 ML 단계의 속도를 크게 높이고 비용을 절감할 수 있습니다. 

단계는 독립적으로 실행되므로 단계 간에 흐르는 입력 및 출력 데이터를 보유하는 개체를 외부에서 정의해야 합니다. 이것은 [데이터 집합](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py)및 [파이프라인데이터](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py), 객체의 역할입니다. 이러한 데이터 개체는 저장소 구성을 캡슐화하는 [Datastore](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore%28class%29?view=azure-ml-py) 개체와 연결됩니다. `PipelineStep` 기본 클래스는 항상 `name` 문자열, `inputs`의 목록 및 `outputs`의 목록으로 만들어집니다. 일반적으로, 그것은 또한 의 `arguments` 목록을 가지고 있으며 종종 `resource_inputs`의 목록을 해야합니다. 하위 클래스에는 일반적으로 추가 인수도 있습니다(예: `PythonScriptStep` 스크립트의 파일 이름과 경로를 실행하려면 필요). 

실행 그래프는 비순환이지만 파이프라인은 되풀이 일정에 따라 실행할 수 있으며 파일 시스템에 상태 정보를 작성할 수 있는 Python 스크립트를 실행하여 복잡한 프로필을 만들 수 있습니다. 특정 단계가 병렬 또는 비동기적으로 실행될 수 있도록 파이프라인을 디자인하는 경우 Azure Machine Learning은 팬아웃 및 팬인의 종속성 분석 및 조정을 투명하게 처리합니다. 일반적으로 실행 그래프의 세부 사항에 대해 걱정할 필요는 없지만 [Pipeline.graph](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipeline.pipeline?view=azure-ml-py#attributes) 특성을 통해 사용할 수 있습니다. 


### <a name="a-simple-python-pipeline"></a>간단한 파이썬 파이프라인

이 코드 조각은 기본을 `Pipeline`만들고 실행하는 데 필요한 개체 및 호출을 보여 주며 다음과 같은 작업을 수행합니다.

```python
ws = Workspace.from_config() 
blob_store = Datastore(ws, "workspaceblobstore")
compute_target = ws.compute_targets["STANDARD_NC6"]
experiment = Experiment(ws, 'MyExperiment') 

input_data = Dataset.File.from_files(
    DataPath(datastore, '20newsgroups/20news.pkl'))

output_data = PipelineData("output_data", datastore=blob_store)

input_named = input_data.as_named_input('input')

steps = [ PythonScriptStep(
    script_name="train.py",
    arguments=["--input", input_named.as_download(), "--output", output_data],
    inputs=[input_data],
    outputs=[output_data],
    compute_target=compute_target,
    source_directory="myfolder"
) ]

pipeline = Pipeline(workspace=ws, steps=steps)

pipeline_run = experiment.submit(pipeline)
pipeline_run.wait_for_completion()
```

스니펫은 일반적인 Azure 기계 학습 `Workspace`개체, `Datastore`a , a , `Experiment` [[계산 대상]](https://docs.microsoft.com/python/api/azureml-core/azureml.core.computetarget?view=azure-ml-py)및 에서 시작합니다. 그런 다음 코드는 보유할 `input_data` `output_data`개체와 을 만듭니다. 배열에는 `steps` 데이터 개체를 `PythonScriptStep` 사용하고 `compute_target`에서 실행되는 단일 요소가 있습니다. 그런 다음 코드는 `Pipeline` 개체 자체를 인스턴스화하여 작업 영역 및 단계 배열을 전달합니다. Azure ML `experiment.submit(pipeline)` 파이프라인 실행을 시작하는 호출입니다. 파이프라인이 `wait_for_completion()` 완료될 때까지 블록에 대한 호출입니다. 

파이프라인을 데이터에 연결하는 방법에 대한 자세한 내용은 [Azure 기계 학습 및](concept-data.md) ML 파이프라인 [단계(파이썬) 간 데이터 이동](how-to-move-data-in-out-of-pipelines.md)에 대한 문서 데이터 액세스를 참조하십시오. 

## <a name="best-practices-when-using-pipelines"></a>파이프라인 사용 시 모범 사례

보시다시피 Azure ML 파이프라인을 만드는 것은 스크립트를 시작하는 것보다 약간 더 복잡합니다. 파이프라인에는 몇 개의 Python 개체를 구성하고 만들어야 합니다. 

파이프라인사용을 제안하는 몇 가지 상황:

* 팀 환경에서: ML 작업을 여러 독립적인 단계로 나누어 개발자가 독립적으로 프로그램을 작업하고 발전시킬 수 있도록 합니다. 

* 배포 중 이거나 배포 에 가까운 경우: 구성을 못 박고 예약된 및 이벤트 기반 작업을 사용하여 변경되는 데이터를 계속 유지합니다.

* ML 프로젝트의 초기 단계 또는 단독으로 작업하는 경우: 파이프라인을 사용하여 빌드를 자동화합니다. 새 아이디어를 구현하기 전에 구성 및 계산 상태를 다시 만드는 것에 대해 걱정하기 시작했다면 이는 파이프라인을 사용하여 워크플로를 자동화하는 것을 고려할 수 있는 신호입니다. 

캐시된 결과를 재사용하고, 계산 비용을 세분화하여 제어하고, 프로세스 격리를 처리하는 데 는 쉽지만 파이프라인에는 비용이 듭니다. 일부 안티 패턴은 다음과 같습니다:

* 파이프라인을 유일한 수단으로 사용하여 문제를 분리합니다. 파이썬의 내장 함수, 개체 및 모듈은 프로그래밍 방식의 혼동을 피하기 위해 먼 길을 갑니다! 파이프라인 단계는 함수 호출보다 훨씬 비쌉입니다.

* 파이프라인 단계 간의 강력한 결합. 종속 단계를 리팩터링하는 데 이전 단계의 출력을 수정해야 하는 경우가 많으면 별도의 단계가 현재 이점보다 더 많은 비용이 들 수 있습니다. 단계가 과도하게 결합되어 있는 또 다른 단서는 데이터가 아니라 처리를 제어하는 플래그가 되는 단계에 대한 인수입니다. 

* 계산 리소스를 조기에 최적화합니다. 예를 들어, 데이터 준비에는 여러 단계가 있으며 종종 "아, 병렬 `MpiStep` 프로그래밍에 사용할 수 있는 곳이 있지만, 여기에 덜 강력한 `PythonScriptStep` 컴퓨팅 대상을 사용할 수 있는 위치"를 볼 수 있습니다. 그리고 장기적으로, 그런 세분화 된 단계를 만드는 것은 가치가 증명 할 수 있습니다, 항상 다시 계산하는 것보다 캐시 된 결과를 사용할 가능성이 특히. 그러나 파이프 라인은 파이썬의 기본 `multiprocessing` 모듈을 대체하기위한 것이 아닙니다. 

프로젝트가 크거나 배포에 가까워질 때까지 파이프라인은 세분화되지 않고 거칠어야 합니다. ML 프로젝트를 _단계와_ 파이프라인을 특정 단계를 통해 이동할 수 있는 완벽한 워크플로우를 제공하는 것으로 생각하면 올바른 경로에 있습니다. 

## <a name="key-advantages"></a>주요 장점

기계 학습 워크플로우에 파이프라인을 사용할 때의 주요 이점은 다음과 같습니다.

|주요 장점|Description|
|:-------:|-----------|
|**무인&nbsp;실행**|단계가 병렬 또는 순차적으로 안정적이고 무인 방식으로 실행되도록 예약합니다. 데이터 준비 및 모델링은 며칠 또는 몇 주 동안 지속될 수 있으며 파이프라인을 사용하면 프로세스가 실행되는 동안 다른 작업에 집중할 수 있습니다. |
|**이종 계산**|이기종적이고 확장 가능한 컴퓨팅 리소스 및 스토리지 위치에서 안정적으로 조정되는 여러 파이프라인을 사용합니다. HDInsight, GPU 데이터 과학 VM 및 데이터브릭과 같은 다양한 컴퓨팅 대상에서 개별 파이프라인 단계를 실행하여 사용 가능한 컴퓨팅 리소스를 효율적으로 사용할 수 있습니다.|
|**재사용 가능**|재교육 및 일괄 처리 점수 매기기와 같은 특정 시나리오에 대한 파이프라인 템플릿을 만듭니다. 간단한 REST 호출을 통해 외부 시스템에서 게시된 파이프라인을 트리거합니다.|
|**추적 및 버전 관리**|반복하면서 데이터 및 결과 경로를 수동으로 추적하는 대신, Pipelines SDK를 사용하여 데이터 원본, 입력 및 출력의 이름과 버전을 명시적으로 지정합니다. 생산성 향상을 위해 스크립트와 데이터를 별도로 관리할 수도 있습니다.|
| **모듈화** | 우려 영역을 분리하고 변경 사항을 격리하면 소프트웨어가 더 높은 품질로 더 빠른 속도로 발전할 수 있습니다. | 
|**협업**|파이프라인을 사용하면 데이터 과학자가 기계 학습 설계 프로세스의 모든 영역에서 협업하는 동시에 파이프라인 단계에서 동시에 작업할 수 있습니다.|

### <a name="choosing-the-proper-pipelinestep-subclass"></a>적절한 파이프라인스텝 하위 클래스 선택

는 `PythonScriptStep` 추상에서 `PipelineStep`가장 유연한 하위 클래스입니다. 하위 클래스와 같은 `EstimatorStep` 다른 `DataTransferStep` 하위 클래스는 코드가 적고 특정 작업을 수행할 수 있습니다. 예를 들어 `EstimatorStep` 단계, `Estimator`an 및 계산 대상에 대한 이름을 전달하여 만들 수 있습니다. 또는 입력 및 출력, 파이프라인 매개 변수 및 인수를 재정의할 수 있습니다. 자세한 내용은 [추정기를 사용하여 Azure 기계 학습을 사용하는 모델 학습을](how-to-train-ml-models.md)참조하십시오. 

이를 `DataTransferStep` 통해 데이터 원본과 싱크 간에 데이터를 쉽게 이동할 수 있습니다. 이 전송을 수동으로 수행하는 코드는 간단하지만 반복적입니다. 대신 이름, 데이터 `DataTransferStep` 원본 및 데이터 싱크에 대한 참조 및 계산 대상을 사용하여 만들 수 있습니다. [DataTransferStep이 있는](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines/intro-to-pipelines/aml-pipelines-data-transfer.ipynb) 전자 필기장 Azure 기계 학습 파이프라인은 이러한 유연성을 보여 줍니다.

## <a name="modules"></a>모듈

파이프라인 단계는 이전 실행 결과를 다시 사용할 수 있지만 대부분의 경우 단계를 구성하면 필요한 스크립트와 종속 파일을 로컬로 사용할 수 있어야 한다고 가정합니다. 데이터 과학자가 기존 코드 위에 빌드하려는 경우 스크립트와 종속성은 종종 별도의 리포지토리에서 복제되어야 합니다.

모듈은 파이프라인 단계와 사용량이 비슷하지만 작업 영역을 통해 원활하게 버전 관리 기능을 제공하므로 대규모로 공동 작업및 재사용이 가능합니다. 모듈은 여러 파이프라인에서 재사용되도록 설계되었으며 특정 계산을 다른 사용 사례에 맞게 조정하도록 발전할 수 있습니다. 사용자는 외부 리포지토리를 사용하지 않고 작업 영역을 통해 다음 작업을 수행할 수 있습니다.

* 새 모듈 만들기 및 기존 모듈의 새 버전 게시
* 기존 버전 사용 더 이상 사용 하지 않는 경우
* 소비자가 해당 버전을 사용하지 못하도록 버전을 사용하지 않도록 표시
* 기본 버전 지정
* 작업 영역에서 버전별로 모듈을 검색하여 팀이 동일한 코드를 사용하고 있는지 확인합니다.

Azure 기계 학습 파이프라인에서 모듈을 생성, 연결 및 사용하는 방법에 대한 코드 예제는 [노트북을](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines/intro-to-pipelines/aml-pipelines-how-to-use-modulestep.ipynb) 참조하십시오.

## <a name="next-steps"></a>다음 단계

Azure ML 파이프라인은 초기 개발 단계에서 가치를 제공하기 시작하는 강력한 기능입니다. 팀과 프로젝트가 증가함에 따라 값이 증가합니다. 이 문서에서는 파이프라인이 Azure 기계 학습 파이썬 SDK로 지정되고 Azure에서 오케스트레이션되는 방법을 설명합니다. 몇 가지 기본 소스 코드를 보았고 사용 가능한 `PipelineStep` 몇 가지 클래스에 대해 소개했습니다. Azure ML 파이프라인을 사용하는 시기와 Azure에서 파이프라인을 실행하는 방법을 파악할 수 있어야 합니다. 


+ [첫 번째 파이프라인을 만드는](how-to-create-your-first-pipeline.md)방법에 대해 알아봅니다.

+ [대용량 데이터에 대한 일괄 처리 예측을 실행하는](tutorial-pipeline-batch-scoring-classification.md )방법에 대해 알아봅니다.

+ [파이프라인 코어](https://docs.microsoft.com/python/api/azureml-pipeline-core/?view=azure-ml-py) 및 [파이프라인 단계에](https://docs.microsoft.com/python/api/azureml-pipeline-steps/?view=azure-ml-py)대한 SDK 참조 문서를 참조하십시오.

+ [Azure 기계 학습 파이프라인을](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines)보여주는 Jupyter 노트북 예제를 사용해 보십시오. [이 서비스를 탐색하려면 전자 필기장을 실행하는](samples-notebooks.md)방법에 대해 알아봅니다.
