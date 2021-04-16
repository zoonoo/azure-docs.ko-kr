---
title: '자습서: Machine Learning 시작 - Python'
titleSuffix: Azure Machine Learning
description: 개인 개발 환경에서 실행되는 Azure Machine Learning SDK for Python을 시작합니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: aminsaied
ms.author: amsaied
ms.reviewer: sgilley
ms.date: 02/11/2021
ms.custom: devx-track-python, contperf-fy21q3
adobe-target: true
ms.openlocfilehash: 69340245cdebda570b526afd4f11ac8314384b43
ms.sourcegitcommit: c3739cb161a6f39a9c3d1666ba5ee946e62a7ac3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/08/2021
ms.locfileid: "107210621"
---
# <a name="tutorial-get-started-with-azure-machine-learning-in-your-development-environment-part-1-of-4"></a>자습서: 개발 환경에서 Azure Machine Learning 시작(1/4부)

*4부로 구성된 이 자습서 시리즈* 에서는 Azure Machine Learning의 기본 사항을 알아보고, Azure 클라우드 플랫폼에서 작업 기반 Python 기계 학습 작업을 완료합니다. 

이 자습서 시리즈의 1부에서 수행하는 작업은 다음과 같습니다.

> [!div class="checklist"]
> * Azure Machine Learning SDK 설치
> * 코드에 대한 디렉터리 구조 설정
> * Azure Machine Learning 작업 영역을 만듭니다.
> * 로컬 개발 환경 구성
> * 모델 학습을 위한 클라우드 기반 리소스인 컴퓨팅 클러스터를 설정합니다.

이 자습서 시리즈는 **일괄** 작업을 제출하는 데 필요한 Azure Machine Learning 개념에 중점을 두고 있습니다. 여기서 코드는 클라우드에 제출되어 사용자 상호 작용 없이 백그라운드에서 실행됩니다. 이는 반복적으로 실행할 완료된 스크립트 또는 코드, 또는 컴퓨팅 집약 기계 학습 작업에 유용합니다.

Azure Machine Learning 사용을 시작하는 또 다른 좋은 방법은 Jupyter Notebooks를 사용하는 것입니다.  [자습서: Jupyter Notebooks에서 Azure Machine Learning 시작](tutorial-1st-experiment-sdk-setup.md)을 참조하세요.

## <a name="prerequisites"></a>필수 구성 요소

- Azure 구독 Azure 구독이 없는 경우 시작하기 전에 체험 계정을 만듭니다. [Azure Machine Learning](https://aka.ms/AMLFree)을 사용해 보세요.
- Python 가상 환경을 관리하고 패키지를 설치하는 [Anaconda](https://www.anaconda.com/download/) 또는 [Miniconda](https://www.anaconda.com/download/).  
- conda 사용에 익숙하지 않은 경우 [conda 시작](https://conda.io/projects/conda/en/latest/user-guide/getting-started.html)을 참조하세요.
- Python 스크립트를 만들 수 있는 모든 IDE 또는 텍스트 편집기입니다.

## <a name="install-the-azure-machine-learning-sdk"></a>Azure Machine Learning SDK 설치

이 자습서에서는 Azure Machine Learning SDK for Python을 사용합니다. Python 종속성 문제를 방지하려면 분리된 환경을 만듭니다. 이 자습서 시리즈는 conda를 사용하여 해당 환경을 만듭니다. `venv`, `virtualenv` 또는 docker와 같은 다른 솔루션을 사용하려면 Python 버전 3.5 이상, 3.9 이하를 사용해야 합니다.

시스템에 conda가 설치되어 있는지 확인합니다.
    
```bash
conda --version
```
    
이 명령이 `conda not found` 오류를 반환하면 [Miniconda를 다운로드하여 설치합니다](https://docs.conda.io/en/latest/miniconda.html). 

Conda를 설치했으면 터미널 또는 Anaconda 프롬프트 창을 사용하여 새 환경을 만듭니다.

```bash
conda create -n tutorial python=3.8
```

다음으로, 만든 conda 환경에 Azure Machine Learning SDK를 설치합니다.

```bash
conda activate tutorial
pip install azureml-core
```
    
> [!NOTE]
> Azure Machine Learning SDK 설치가 완료되는 데 약 2분이 소요됩니다.
>
> 시간 초과 오류가 발생하는 경우 `pip install --default-timeout=100 azureml-core`를 대신 사용해보세요.


> [!div class="nextstepaction"]
> [SDK를 설치했습니다.](?success=install-sdk#dir) [문제가 발생했습니다.](https://www.research.net/r/7C8Z3DN?issue=install-sdk)

## <a name="create-a-directory-structure-for-code"></a><a name="dir"></a>코드에 대한 디렉터리 구조 만들기

이 자습서에서는 다음과 같은 간단한 디렉터리를 설정하는 것이 좋습니다.

:::image type="content" source="media/tutorial-1st-experiment-sdk-local/directory-structure-1.png" alt-text="디렉터리 구조: .azureml 하위 디렉터리가 있는 자습서 상위 수준":::


- `tutorial`: 프로젝트의 최상위 디렉터리
- `.azureml`: Azure Machine Learning 구성 파일을 저장하기 위한 숨겨진 하위 디렉터리입니다.

예를 들어 bash 창에서 이를 만들려면 다음을 수행합니다.

```bash
mkdir tutorial
cd tutorial
mkdir .azureml
```

> [!TIP]
> 그래픽 창에서 구조를 만들거나 보려면 먼저 숨겨진 파일 및 폴더를 보고 만들 수 있는 기능을 사용하도록 설정합니다.
>
> * Mac Finder 창에서 **명령 + Shift +** 를 사용합니다. 숨겨진 파일/폴더를 표시하거나 숨깁니다.  
> * Windows 10 파일 탐색기에서 [숨김 파일 및 폴더를 보는 방법](https://support.microsoft.com/en-us/windows/view-hidden-files-and-folders-in-windows-10-97fbc472-c603-9d90-91d0-1166d1d9f4b5)을 참조하세요. 
> * Linux 그래픽 인터페이스에서 **Ctrl + h** 또는 **보기** 메뉴를 사용하고 **숨김 파일 표시** 확인란을 선택합니다.




> [!div class="nextstepaction"]
> [디렉터리를 만들었습니다.](?success=create-dir#workspace) [문제가 발생했습니다.](https://www.research.net/r/7C8Z3DN?issue=create-dir)

## <a name="create-an-azure-machine-learning-workspace"></a><a name="workspace"></a>Azure Machine Learning 작업 영역 만들기

작업 영역은 Azure Machine Learning에 대한 최상위 리소스이며, 다음을 수행할 수 있는 중앙 위치입니다.

- 리소스(예: 컴퓨팅) 관리
- 자산(예: Notebook, 환경, 데이터 세트, 파이프라인, 모델 및 엔드포인트) 저장
- 다른 팀 멤버와 협업

최상위 디렉터리 `tutorial`에서 다음 코드를 사용하여 `01-create-workspace.py`라는 새 Python 파일을 추가합니다. 매개 변수(이름, 구독 ID, 리소스 그룹 및 [location](https://azure.microsoft.com/global-infrastructure/services/?products=machine-learning-service))에 사용자 기본 설정을 적용합니다.

코드를 대화형 세션에서 또는 Python 파일로 실행할 수 있습니다.

>[!NOTE]
> 로컬 개발 환경(예: 컴퓨터)을 사용하는 경우 다음 코드를 처음 실행할 때 *디바이스 코드* 를 사용하여 작업 영역에 인증하라는 메시지가 표시됩니다. 화면상의 지침을 따릅니다.

```python
# tutorial/01-create-workspace.py
from azureml.core import Workspace

ws = Workspace.create(name='<my_workspace_name>', # provide a name for your workspace
                      subscription_id='<azure-subscription-id>', # provide your subscription ID
                      resource_group='<myresourcegroup>', # provide a resource group name
                      create_resource_group=True,
                      location='<NAME_OF_REGION>') # For example: 'westeurope' or 'eastus2' or 'westus2' or 'southeastasia'.

# write out the workspace details to a configuration file: .azureml/config.json
ws.write_config(path='.azureml')
```

활성화된 *tutorial1* conda 환경이 있는 창에서 `tutorial` 디렉토리에서 이 코드를 실행합니다.

```bash
cd <path/to/tutorial>
python ./01-create-workspace.py
```

> [!TIP]
> 이 코드를 실행하면 구독에 대한 액세스 권한이 없다는 오류가 표시됩니다. 인증 옵션에 대한 자세한 내용은 [작업 영역 만들기](how-to-manage-workspace.md?tab=python#create-multi-tenant)를 참조하세요.


*01-create-workspace.py* 를 성공적으로 실행하면 폴더 구조는 다음과 같습니다.

:::image type="content" source="media/tutorial-1st-experiment-sdk-local/directory-structure-2.png" alt-text="01-create-workspace.py를 실행한 후에 config.json 파일이 .azureml 하위 디렉터리에 표시됨":::

`.azureml/config.json` 파일에는 Azure Machine Learning 작업 영역에 연결하는 데 필요한 메타데이터가 포함되어 있습니다. 즉, 구독 ID, 리소스 그룹 및 작업 영역 이름이 포함됩니다. 

> [!NOTE]
> `config.json`의 콘텐츠는 비밀이 아닙니다. 이러한 세부 정보를 공유하는 것은 괜찮습니다.
>
> Azure Machine Learning 작업 영역과 상호 작용하려면 인증도 필요합니다.

> [!div class="nextstepaction"]
> [작업 영역을 만들었습니다.](?success=create-workspace#cluster) [문제가 발생했습니다.](https://www.research.net/r/7C8Z3DN?issue=create-workspace)

## <a name="create-an-azure-machine-learning-compute-cluster"></a><a name="cluster"></a> Azure Machine Learning 컴퓨팅 클러스터 만들기

`02-create-compute.py`라는 Python 스크립트를 `tutorial` 최상위 디렉터리에 만듭니다. 다음 코드로 채워 0~4개의 노드 사이에서 자동으로 크기 조정되는 Azure Machine Learning 컴퓨팅 클러스터를 만듭니다.

```python
# tutorial/02-create-compute.py
from azureml.core import Workspace
from azureml.core.compute import ComputeTarget, AmlCompute
from azureml.core.compute_target import ComputeTargetException

ws = Workspace.from_config() # This automatically looks for a directory .azureml

# Choose a name for your CPU cluster
cpu_cluster_name = "cpu-cluster"

# Verify that the cluster does not exist already
try:
    cpu_cluster = ComputeTarget(workspace=ws, name=cpu_cluster_name)
    print('Found existing cluster, use it.')
except ComputeTargetException:
    compute_config = AmlCompute.provisioning_configuration(vm_size='STANDARD_D2_V2',
                                                           idle_seconds_before_scaledown=2400,
                                                           min_nodes=0,
                                                           max_nodes=4)
    cpu_cluster = ComputeTarget.create(ws, cpu_cluster_name, compute_config)

cpu_cluster.wait_for_completion(show_output=True)
```

활성화된 *tutorial1* conda 환경이 있는 창에서 Python 파일을 실행합니다.

```bash
python ./02-create-compute.py
```


> [!NOTE]
> 클러스터가 만들어지면 0개의 노드가 프로비저닝됩니다. 작업을 제출할 때까지 클러스터에 대한 비용이 *발생하지 않습니다*. 이 클러스터가 2,400초(40분) 동안 유휴 상태이면 축소됩니다.

이제 폴더 구조는 다음과 같습니다.

:::image type="content" source="media/tutorial-1st-experiment-sdk-local/directory-structure-3.png" alt-text="자습서 디렉터리에 02-create-compute.py 추가":::

> [!div class="nextstepaction"]
> [컴퓨팅 클러스터를 만들었습니다.](?success=create-compute-cluster#next-steps) [문제가 발생했습니다.](https://www.research.net/r/7C8Z3DN?issue=create-compute-cluster)

## <a name="view-in-the-studio"></a>스튜디오에서 보기

[Azure Machine Learning Studio](https://ml.azure.com)에 로그인하여 만든 작업 영역 및 컴퓨팅 인스턴스를 확인합니다.

1. 작업 영역을 만드는 데 사용한 **구독** 을 선택합니다.
1. 만든 **Machine Learning 작업 영역**, *tutorial-ws* 를 선택합니다.
1. 작업 영역이 로드되면 왼쪽에서 **컴퓨팅** 을 선택합니다.
1. 상단에서 **컴퓨팅 클러스터** 탭을 선택합니다.

:::image type="content" source="media/tutorial-1st-experiment-sdk-local/compute-instance-in-studio.png" alt-text="스크린샷: 작업 영역에서 컴퓨팅 인스턴스를 봅니다.":::

이 보기는 유휴 노드, 사용 중인 노드 및 프로비저닝되지 않은 노드의 수와 함께 프로비저닝된 컴퓨팅 클러스터를 표시합니다.  아직 클러스터를 사용하지 않았으므로 현재 모든 노드가 프로비저닝되지 않습니다.

## <a name="next-steps"></a>다음 단계

이 설정 자습서에서는 다음을 수행했습니다.

- Azure Machine Learning 작업 영역을 만들었습니다.
- 로컬 개발 환경을 설정했습니다.
- Azure Machine Learning 컴퓨팅 클러스터를 만들었습니다.

이 자습서의 다른 부분에서는 다음을 학습합니다.

* 2부. Azure Machine Learning SDK for Python을 사용하여 클라우드에서 코드를 실행합니다.
* 3부. 모델 학습에 사용하는 Python 환경을 관리합니다.
* 4부. 데이터를 Azure에 업로드하고 해당 데이터를 학습에 사용합니다.

다음 자습서를 계속 진행하여 스크립트를 Azure Machine Learning 컴퓨팅 클러스터에 제출하는 과정을 살펴봅니다.

> [!div class="nextstepaction"]
> [자습서: “Hello World!” Python 스크립트를 Azure에서 실행](tutorial-1st-experiment-hello-world.md)
