---
title: '자습서: Machine Learning 시작 - Python'
titleSuffix: Azure Machine Learning
description: 이 자습서에서는 개인 개발 환경에서 실행되는 Azure Machine Learning SDK for Python을 시작합니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: aminsaied
ms.author: amsaied
ms.reviewer: sgilley
ms.date: 09/15/2020
ms.custom: devx-track-python
ms.openlocfilehash: 2e01721b4b414455b47a394087192696e1ecb025
ms.sourcegitcommit: 4064234b1b4be79c411ef677569f29ae73e78731
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/28/2020
ms.locfileid: "92892737"
---
# <a name="tutorial-get-started-with-azure-machine-learning-in-your-development-environment-part-1-of-4"></a>자습서: 개발 환경에서 Azure Machine Learning 시작(1/4부)

*4부로 구성된 이 자습서 시리즈* 에서는 Azure Machine Learning의 기본 사항을 알아보고, Azure 클라우드 플랫폼에서 작업 기반 Python 기계 학습 작업을 완료합니다. 

이 자습서 시리즈의 1부에서 수행하는 작업은 다음과 같습니다.

> [!div class="checklist"]
> * Azure Machine Learning SDK 설치
> * 코드에 대한 디렉터리 구조 설정
> * Azure Machine Learning 작업 영역을 만듭니다.
> * 로컬 개발 환경 구성
> * 컴퓨팅 클러스터 설정

> [!NOTE]
> 이 자습서 시리즈는 컴퓨팅 집약적이거나 재현 가능성이 필요한 Python *작업 기반* 기계 학습 작업에 적합한 Azure Machine Learning 개념을 중점적으로 다룹니다. 예비 워크플로에 더 관심이 있는 경우 [Azure Machine Learning 컴퓨팅 인스턴스의 Jupyter 또는 RStudio](tutorial-1st-experiment-sdk-setup.md)를 대신 사용할 수 있습니다.

## <a name="prerequisites"></a>필수 조건

- Azure 구독 Azure 구독이 없는 경우 시작하기 전에 체험 계정을 만듭니다. [Azure Machine Learning](https://aka.ms/AMLFree)을 사용해 보세요.
- Python 및 [Machine Learning 개념](concept-azure-machine-learning-architecture.md)에 대한 이해. 예를 들어 환경, 학습, 점수 매기기 등이 있습니다.
- Visual Studio Code, Jupyter 및 PyCharm과 같은 로컬 개발 환경
- Python(버전 3.5 ~ 3.7).


## <a name="install-the-azure-machine-learning-sdk"></a>Azure Machine Learning SDK 설치

이 자습서에서는 Azure Machine Learning SDK for Python을 사용합니다.

가장 친숙한 도구(예: Conda 및 pip)를 사용하여 이 자습서 전체에서 사용할 Python 환경을 설정할 수 있습니다. pip를 통해 Python용 Azure Machine Learning SDK를 Python 환경에 설치합니다.

```bash
pip install azureml-sdk
```

## <a name="create-a-directory-structure-for-code"></a>코드에 대한 디렉터리 구조 만들기
이 자습서에서는 다음과 같은 간단한 디렉터리를 설정하는 것이 좋습니다.

```markdown
tutorial
└──.azureml
```

- `tutorial`: 프로젝트의 최상위 디렉터리
- `.azureml`: Azure Machine Learning 구성 파일을 저장하기 위한 숨겨진 하위 디렉터리입니다.

## <a name="create-an-azure-machine-learning-workspace"></a>Azure Machine Learning 작업 영역 만들기

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

`tutorial` 디렉터리에서 다음 코드를 실행합니다.

```bash
cd <path/to/tutorial>
python ./01-create-workspace.py
```

> [!TIP]
> 이 코드를 실행하면 구독에 대한 액세스 권한이 없다는 오류가 표시됩니다. 인증 옵션에 대한 자세한 내용은 [작업 영역 만들기](how-to-manage-workspace.md?tab=python#create-multi-tenant)를 참조하세요.


*01-create-workspace.py* 를 성공적으로 실행하면 폴더 구조는 다음과 같습니다.

```markdown
tutorial
└──.azureml
|  └──config.json
└──01-create-workspace.py
```

`.azureml/config.json` 파일에는 Azure Machine Learning 작업 영역에 연결하는 데 필요한 메타데이터가 포함되어 있습니다. 즉, 구독 ID, 리소스 그룹 및 작업 영역 이름이 포함됩니다. 

> [!NOTE]
> `config.json`의 콘텐츠는 비밀이 아닙니다. 이러한 세부 정보를 공유하는 것은 괜찮습니다.
>
> Azure Machine Learning 작업 영역과 상호 작용하려면 인증도 필요합니다.

## <a name="create-an-azure-machine-learning-compute-cluster"></a>Azure Machine Learning 컴퓨팅 클러스터 만들기

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
                                                           idle_seconds_before_scaledown=2400)
    cpu_cluster = ComputeTarget.create(ws, cpu_cluster_name, compute_config)

cpu_cluster.wait_for_completion(show_output=True)
```

Python 파일을 실행합니다.

```bash
python ./02-create-compute.py
```


> [!NOTE]
> 클러스터가 만들어지면 0개의 노드가 프로비저닝됩니다. 작업을 제출할 때까지 클러스터에 대한 비용이 *발생하지 않습니다*. 이 클러스터가 2,400초(40분) 동안 유휴 상태이면 축소됩니다.

이제 폴더 구조는 다음과 같습니다.

```bash
tutorial
└──.azureml
|  └──config.json
└──01-create-workspace.py
└──02-create-compute.py
```

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
