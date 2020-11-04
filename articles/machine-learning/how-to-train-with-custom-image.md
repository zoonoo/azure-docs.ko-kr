---
title: 사용자 지정 Docker 이미지를 사용 하 여 모델 학습
titleSuffix: Azure Machine Learning
description: 사용자 고유의 Docker 이미지를 사용 하거나 Microsoft에서 큐 레이트 하 여 Azure Machine Learning에서 모델을 학습 하는 방법에 대해 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: sagopal
author: saachigopal
ms.date: 10/20/2020
ms.topic: conceptual
ms.custom: how-to
ms.openlocfilehash: 23b59c80c8e44cf6473a2de9be9807eaf8a756c6
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/04/2020
ms.locfileid: "93310549"
---
# <a name="train-a-model-by-using-a-custom-docker-image"></a>사용자 지정 Docker 이미지를 사용 하 여 모델 학습

이 문서에서는 Azure Machine Learning를 사용 하 여 모델을 학습 하는 경우 사용자 지정 Docker 이미지를 사용 하는 방법에 대해 알아봅니다. 이 문서의 예제 스크립트를 사용 하 여 나선형 신경망을 만들어 pet 이미지를 분류 합니다. 

Azure Machine Learning 기본 Docker 기본 이미지를 제공 합니다. Azure Machine Learning 환경을 사용 하 여 유지 관리 되는 [Azure Machine Learning 기본](https://github.com/Azure/AzureML-Containers) 이미지 중 하나 또는 고유한 [사용자 지정 이미지](how-to-deploy-custom-docker-image.md#create-a-custom-base-image)와 같은 다른 기본 이미지를 지정할 수도 있습니다. 사용자 지정 기본 이미지를 사용 하면 종속성을 긴밀 하 게 관리 하 고 학습 작업을 실행할 때 구성 요소 버전을 보다 강력 하 게 제어할 수 있습니다.

## <a name="prerequisites"></a>필수 구성 요소

이러한 환경 중 하나에서 코드를 실행 합니다.

* 계산 인스턴스 Azure Machine Learning (다운로드 또는 설치 필요 없음):
  * SDK 및 샘플 리포지토리를 사용 하 여 미리 로드 된 전용 노트북 서버를 만들려면 [환경 및 작업 영역 설정](tutorial-1st-experiment-sdk-setup.md) 자습서를 완료 합니다.
  * Azure Machine Learning [예제 리포지토리](https://github.com/Azure/azureml-examples)에서 **노트북**  >  **fastai**  >  **resnet34-. ipynb** 디렉터리로 이동 하 여 완료 된 노트북을 찾습니다. 
* 사용자 고유의 Jupyter Notebook 서버:
  * [작업 영역 구성 파일](how-to-configure-environment.md#workspace)을 만듭니다.
  * [Azure Machine Learning SDK](/python/api/overview/azure/ml/install?preserve-view=true&view=azure-ml-py)를 설치합니다. 
  * 인터넷에서 사용할 수 있는 [Azure container registry](../container-registry/index.yml) 또는 기타 Docker 레지스트리를 만듭니다.

## <a name="set-up-a-training-experiment"></a>학습 실험 설정

이 섹션에서는 작업 영역을 초기화 하 고, 환경을 정의 하 고, 계산 대상을 구성 하 여 학습 실험을 설정 합니다.

### <a name="initialize-a-workspace"></a>작업 영역 초기화

[Azure Machine Learning 작업 영역은](concept-workspace.md) 서비스에 대 한 최상위 리소스입니다. 사용자가 만든 모든 아티팩트를 사용 하 여 작업할 수 있는 중앙 집중식 환경을 제공 합니다. Python SDK에서 개체를 만들어 작업 영역 아티팩트에 액세스할 수 있습니다 [`Workspace`](/python/api/azureml-core/azureml.core.workspace.workspace?preserve-view=true&view=azure-ml-py) .

`Workspace` [필수 구성](#prerequisites)요소로 만든 파일의 config.js에서 개체를 만듭니다.

```Python
from azureml.core import Workspace

ws = Workspace.from_config()
```

### <a name="define-your-environment"></a>환경 정의

개체를 만들고 `Environment` Docker를 사용 하도록 설정 합니다.

```python
from azureml.core import Environment

fastai_env = Environment("fastai2")
fastai_env.docker.enabled = True
```

다음 코드에서 지정 된 기본 이미지는 분산 심층 학습 기능을 허용 하는 fast.ai 라이브러리를 지원 합니다. 자세한 내용은 [Fast.ai Docker Hub 리포지토리](https://hub.docker.com/u/fastdotai)를 참조 하세요. 

사용자 지정 Docker 이미지를 사용 하는 경우 Python 환경이 이미 제대로 설정 되어 있을 수 있습니다. 이 경우 `user_managed_dependencies` `True` 사용자 지정 이미지의 기본 제공 Python 환경을 사용 하도록 플래그를로 설정 합니다. 기본적으로 Azure Machine Learning는 사용자가 지정한 종속성이 있는 Conda 환경을 작성 합니다. 서비스는 기본 이미지에 설치한 Python 라이브러리를 사용 하는 대신 해당 환경에서 스크립트를 실행 합니다.

```python
fastai_env.docker.base_image = "fastdotai/fastai2:latest"
fastai_env.python.user_managed_dependencies = True
```

#### <a name="use-a-private-container-registry-optional"></a>개인 컨테이너 레지스트리 사용 (선택 사항)

작업 영역에 없는 개인 컨테이너 레지스트리에서 이미지를 사용 하려면를 사용 `docker.base_image_registry` 하 여 리포지토리의 주소와 사용자 이름 및 암호를 지정 합니다.

```python
# Set the container registry information.
fastai_env.docker.base_image_registry.address = "myregistry.azurecr.io"
fastai_env.docker.base_image_registry.username = "username"
fastai_env.docker.base_image_registry.password = "password"
```

#### <a name="use-a-custom-dockerfile-optional"></a>사용자 지정 Dockerfile 사용 (선택 사항)

사용자 지정 Dockerfile도 사용할 수 있습니다. 비 Python 패키지를 종속성으로 설치 해야 하는 경우이 방법을 사용 합니다. 기본 이미지를로 설정 해야 `None` 합니다.

```python 
# Specify Docker steps as a string. 
dockerfile = r"""
FROM mcr.microsoft.com/azureml/base:intelmpi2018.3-ubuntu16.04
RUN echo "Hello from custom container!"
"""

# Set the base image to None, because the image is defined by Dockerfile.
fastai_env.docker.base_image = None
fastai_env.docker.base_dockerfile = dockerfile

# Alternatively, load the string from a file.
fastai_env.docker.base_image = None
fastai_env.docker.base_dockerfile = "./Dockerfile"
```

Azure Machine Learning 환경을 만들고 관리 하는 방법에 대 한 자세한 내용은 [소프트웨어 환경 만들기 및 사용](how-to-use-environments.md)을 참조 하세요. 

### <a name="create-or-attach-a-compute-target"></a>계산 대상 만들기 또는 연결

모델 학습을 위한 [계산 대상을](concept-azure-machine-learning-architecture.md#compute-targets) 만들어야 합니다. 이 자습서에서는를 `AmlCompute` 학습 계산 리소스로 만듭니다.

만들기는 `AmlCompute` 몇 분 정도 걸립니다. `AmlCompute`리소스가 이미 작업 영역에 있는 경우이 코드는 생성 프로세스를 건너뜁니다.

다른 Azure 서비스와 마찬가지로 `AmlCompute` Azure Machine Learning 서비스와 연결 된 특정 리소스 (예:)에 대 한 제한이 있습니다. 자세한 내용은 [기본 한도 및 더 높은 할당량을 요청 하는 방법](how-to-manage-quotas.md)을 참조 하세요.

```python
from azureml.core.compute import ComputeTarget, AmlCompute
from azureml.core.compute_target import ComputeTargetException

# Choose a name for your cluster.
cluster_name = "gpu-cluster"

try:
    compute_target = ComputeTarget(workspace=ws, name=cluster_name)
    print('Found existing compute target.')
except ComputeTargetException:
    print('Creating a new compute target...')
    compute_config = AmlCompute.provisioning_configuration(vm_size='STANDARD_NC6',
                                                           max_nodes=4)

    # Create the cluster.
    compute_target = ComputeTarget.create(ws, cluster_name, compute_config)

    compute_target.wait_for_completion(show_output=True)

# Use get_status() to get a detailed status for the current AmlCompute.
print(compute_target.get_status().serialize())
```

## <a name="configure-your-training-job"></a>교육 작업 구성

이 자습서에서는 [GitHub](https://github.com/Azure/azureml-examples/blob/main/code/models/fastai/pets-resnet34/train.py)의 교육 스크립트 *train.py* 을 사용 합니다. 실제로 사용자 지정 학습 스크립트를 사용 하 고 Azure Machine Learning를 사용 하 여 실행할 수 있습니다.

`ScriptRunConfig`원하는 [계산 대상](how-to-set-up-training-targets.md)에서 실행 되도록 작업을 구성 하는 리소스를 만듭니다.

```python
from azureml.core import ScriptRunConfig

src = ScriptRunConfig(source_directory='fastai-example',
                      script='train.py',
                      compute_target=compute_target,
                      environment=fastai_env)
```

## <a name="submit-your-training-job"></a>학습 작업 제출

개체를 사용 하 여 학습 실행을 제출 하는 경우 `ScriptRunConfig` `submit` 메서드는 형식의 개체를 반환 합니다 `ScriptRun` . 반환 된 `ScriptRun` 개체는 학습 실행에 대 한 정보에 대 한 프로그래밍 방식의 액세스를 제공 합니다. 

```python
from azureml.core import Experiment

run = Experiment(ws,'fastai-custom-image').submit(src)
run.wait_for_completion(show_output=True)
```

> [!WARNING]
> Azure Machine Learning는 전체 원본 디렉터리를 복사 하 여 학습 스크립트를 실행 합니다. 업로드 하지 않으려는 중요 한 데이터가 있는 경우 [무시 파일](how-to-save-write-experiment-files.md#storage-limits-of-experiment-snapshots) 을 사용 하거나 원본 디렉터리에이 파일을 포함 하지 마세요. 대신 데이터 [저장소](/python/api/azureml-core/azureml.data?preserve-view=true&view=azure-ml-py)를 사용 하 여 데이터에 액세스 합니다.

## <a name="next-steps"></a>다음 단계
이 문서에서는 사용자 지정 Docker 이미지를 사용 하 여 모델을 학습 했습니다. Azure Machine Learning에 대해 자세히 알아보려면 다음 문서를 참조 하세요.
* 학습 중 [실행 메트릭을 추적](how-to-track-experiments.md) 합니다.
* 사용자 지정 Docker 이미지를 사용 하 여 [모델을 배포](how-to-deploy-custom-docker-image.md) 합니다.