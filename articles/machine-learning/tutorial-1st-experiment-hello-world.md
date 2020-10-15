---
title: '자습서: "Hello world!" Python 스크립트 실행'
titleSuffix: Azure Machine Learning
description: Azure Machine Learning 시작 시리즈의 2부에서는 간단한 "Hello World" Python 스크립트를 클라우드에 제출하는 방법을 보여 줍니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: aminsaied
ms.author: amsaied
ms.reviewer: sgilley
ms.date: 09/15/2020
ms.custom: devx-track-python
ms.openlocfilehash: 980347c658c65a0c08dfc50c08f50741fb9a00fd
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91372547"
---
# <a name="tutorial-run-a-hello-world-python-script-part-2-of-4"></a>자습서: "Hello world!" Python 스크립트 실행(2/4부)

이 자습서에서는 Python용 Azure Machine Learning SDK를 사용하여 Python "Hello World" 스크립트를 제출하고 실행하는 방법에 대해 알아봅니다.

이 자습서는 Azure Machine Learning의 기본 사항을 알아보고 Azure에서 작업 기반 기계 학습 작업을 완료하는 *4부로 구성된 자습서 시리즈 중 2부*입니다. 이 자습서는 [1부: Azure Machine Learning에 대한 로컬 머신 설정](tutorial-1st-experiment-sdk-setup-local.md)에서 완료한 작업을 기반으로 합니다.

이 자습서에서는 다음을 수행합니다.

> [!div class="checklist"]
> * "Hello world!" Python 스크립트를 로컬로 만들고 실행합니다.
> * "Hello world!"를 Azure Machine Learning에 제출하는 Python 제어 스크립트를 만듭니다.
> * 제어 스크립트에서 Azure Machine Learning 개념을 이해합니다.
> * "Hello world!" 스크립트를 제출하고 실행합니다.
> * 클라우드에서 코드 출력 보기

## <a name="prerequisites"></a>사전 요구 사항

- 아직 Azure Machine Learning 작업 영역이 없는 경우 [1부](tutorial-1st-experiment-sdk-setup-local.md)의 완료
- Python 언어 및 기계 학습 워크플로에 대한 입문 지식
- Visual Studio Code, Jupyter 및 PyCharm과 같은 로컬 개발 환경
- Python(버전 3.5 ~ 3.7)

## <a name="create-and-run-a-python-script-locally"></a>Python 스크립트 만들기 및 로컬로 실행

Azure Machine Learning 컴퓨팅 클러스터에서 실행하려는 코드를 저장하는 `src`라는 새 하위 디렉터리를 `tutorial` 디렉터리 아래에 만듭니다. `src` 하위 디렉터리에서 `hello.py` Python 스크립트를 만듭니다.

```python
# src/hello.py
print("Hello world!")
```

이제 프로젝트 디렉터리 구조는 다음과 같습니다.

```Bash
tutorial
└──.azureml
|  └──config.json
└──src
|  └──hello.py
└──01-create-workspace.py
└──02-create-compute.py
```

### <a name="test-your-script-locally"></a>로컬로 스크립트 테스트

즐겨 사용하는 IDE 또는 터미널을 사용하여 코드를 로컬로 실행할 수 있습니다. 코드를 로컬로 실행하면 코드의 대화형 디버깅을 활용할 수 있습니다.

```bash
cd <path/to/tutorial>
python ./src/hello.py
```

## <a name="create-a-control-script"></a>제어 스크립트 만들기

*제어 스크립트*를 사용하면 클라우드에서 `hello.py` 스크립트를 실행할 수 있습니다. 제어 스크립트를 사용하여 기계 학습 코드가 실행되는 방법과 위치를 제어합니다.  

tutorial 디렉터리에서 `03-run-hello.py`라는 새 Python 파일을 만들고, 다음 코드를 복사하여 해당 파일에 붙여넣습니다.

```python
# tutorial/03-run-hello.py
from azureml.core import Workspace, Experiment, Environment, ScriptRunConfig

ws = Workspace.from_config()
experiment = Experiment(workspace=ws, name='day1-experiment-hello')

config = ScriptRunConfig(source_directory='./src', script='hello.py', compute_target='cpu-cluster')

run = experiment.submit(config)
aml_url = run.get_portal_url()
print(aml_url)
```

### <a name="understand-the-code"></a>코드 이해

제어 스크립트의 작동 방식은 다음과 같이 설명됩니다.

:::row:::
   :::column span="":::
      `ws = Workspace.from_config()`
   :::column-end:::
   :::column span="2":::
      [작업 영역](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py&preserve-view=true)은 Azure Machine Learning 작업 영역에 연결되므로 Azure Machine Learning 리소스와 통신할 수 있습니다.
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      `experiment =  Experiment( ... )`
   :::column-end:::
   :::column span="2":::
      [실험](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment.experiment?view=azure-ml-py&preserve-view=true)은 여러 실행을 단일 이름으로 구성하는 간단한 방법을 제공합니다. 나중에 실험을 통해 수십 개의 실행 간에 메트릭을 쉽게 비교할 수 있는 방법을 확인할 수 있습니다.
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      `config = ScriptRunConfig( ... )` 
   :::column-end:::
   :::column span="2":::
      [ScriptRunConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.scriptrunconfig?view=azure-ml-py&preserve-view=true)에서 `hello.py` 코드를 래핑하고 작업 영역에 전달합니다. 이름에서 알 수 있듯이 이 클래스를 사용하여 Azure Machine Learning에서 _스크립트_를 _실행_하는 방법을 _구성_할 수 있습니다. 또한 스크립트가 실행되는 컴퓨팅 대상을 지정합니다. 이 코드에서 대상은 [설정 자습서](tutorial-1st-experiment-sdk-setup-local.md)에서 만든 컴퓨팅 클러스터입니다.
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      `run = experiment.submit(config)`
   :::column-end:::
   :::column span="2":::
       스크립트를 제출합니다. 이 제출을 [실행](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29?view=azure-ml-py&preserve-view=true)이라고 합니다. 실행은 코드의 단일 실행을 캡슐화합니다. 실행을 사용하여 스크립트 진행률을 모니터링하고, 출력을 캡처하고, 결과를 분석하고, 메트릭을 시각화하는 등의 작업을 수행합니다.
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      `aml_url = run.get_portal_url()` 
   :::column-end:::
   :::column span="2":::
        `run` 개체에서 코드 실행에 대한 핸들을 제공합니다. Python 스크립트에서 출력되는 URL을 사용하여 Azure Machine Learning 스튜디오에서 진행률을 모니터링합니다.  
   :::column-end:::
:::row-end:::

## <a name="submit-and-run-your-code-in-the-cloud"></a>클라우드에서 코드 제출 및 실행

제어 스크립트를 실행합니다. 그러면 [설정 자습서](tutorial-1st-experiment-sdk-setup-local.md)에서 만든 컴퓨팅 클러스터에서 `hello.py`가 실행됩니다.

```bash
python 03-run-hello.py
```

## <a name="monitor-your-code-in-the-cloud-by-using-the-studio"></a>스튜디오를 사용하여 클라우드에서 코드 모니터링

출력에는 다음과 같은 스튜디오에 대한 링크(`https://ml.azure.com/experiments/hello-world/runs/<run-id>?wsid=/subscriptions/<subscription-id>/resourcegroups/<resource-group>/workspaces/<workspace-name>`)가 포함됩니다.

링크를 따르고 **출력 + 로그** 탭으로 이동합니다. 여기서 다음과 같은 `70_driver_log.txt` 파일을 볼 수 있습니다.

```txt
 1: [2020-08-04T22:15:44.407305] Entering context manager injector.
 2: [context_manager_injector.py] Command line Options: Namespace(inject=['ProjectPythonPath:context_managers.ProjectPythonPath', 'RunHistory:context_managers.RunHistory', 'TrackUserError:context_managers.TrackUserError', 'UserExceptions:context_managers.UserExceptions'], invocation=['hello.py'])
 3: Starting the daemon thread to refresh tokens in background for process with pid = 31263
 4: Entering Run History Context Manager.
 5: Preparing to call script [ hello.py ] with arguments: []
 6: After variable expansion, calling script [ hello.py ] with arguments: []
 7:
 8: Hello world!
 9: Starting the daemon thread to refresh tokens in background for process with pid = 31263
10:
11:
12: The experiment completed successfully. Finalizing run...
13: Logging experiment finalizing status in history service.
14: [2020-08-04T22:15:46.541334] TimeoutHandler __init__
15: [2020-08-04T22:15:46.541396] TimeoutHandler __enter__
16: Cleaning up all outstanding Run operations, waiting 300.0 seconds
17: 1 items cleaning up...
18: Cleanup took 0.1812913417816162 seconds
19: [2020-08-04T22:15:47.040203] TimeoutHandler __exit__
```

8번 줄에 "Hello World!" 출력이 표시됩니다.

`70_driver_log.txt` 파일에는 실행의 표준 출력이 포함됩니다. 이 파일은 클라우드에서 원격 실행을 디버그할 때 유용할 수 있습니다.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 간단한 "Hello world!" 스크립트를 사용하여 Azure에서 실행했습니다. Azure Machine Learning 작업 영역에 연결하고, 실험을 만들고, `hello.py` 코드를 클라우드에 제출하는 방법을 확인했습니다.

다음 자습서에서는 `print("Hello world!")`보다 더 흥미로운 항목을 실행하여 이러한 학습을 기반으로 합니다.

> [!div class="nextstepaction"]
> [자습서: 모델 학습](tutorial-1st-experiment-sdk-train.md)

>[!NOTE] 
> 여기서 자습서 시리즈를 완료하고 다음 단계로 진행하지 않으려면 [리소스를 정리](tutorial-1st-experiment-bring-data.md#clean-up-resources)하세요.
