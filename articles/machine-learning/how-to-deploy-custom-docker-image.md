---
title: 사용자 지정 Docker 이미지를 사용하여 모델 배포
titleSuffix: Azure Machine Learning
description: 사용자 지정 Docker 기본 이미지를 사용하여 Azure Machine Learning 모델을 배포하는 방법을 알아봅니다. Azure Machine Learning에서 기본 이미지를 제공하지만 자체 기본 이미지를 사용할 수도 있습니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: sagopal
author: saachigopal
ms.reviewer: larryfr
ms.date: 11/16/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python, deploy, devx-track-azurecli
ms.openlocfilehash: fb6d9a1a1ad341763c205a11b7a6a9acafda1ac4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104889742"
---
# <a name="deploy-a-model-using-a-custom-docker-base-image"></a>사용자 지정 Docker 기본 이미지를 사용하여 모델 배포

Azure Machine Learning을 사용하여 학습된 모델을 배포할 때 사용자 지정 Docker 기본 이미지를 사용하는 방법을 알아봅니다.

Azure Machine Learning은 지정되지 않은 경우 기본 Docker 이미지를 사용합니다. `azureml.core.runconfig.DEFAULT_CPU_IMAGE`에 사용되는 특정 Docker 이미지를 찾을 수 있습니다. Azure Machine Learning __환경__ 을 사용하여 특정 기본 이미지를 선택하거나 사용자가 제공하는 사용자 지정 이미지를 사용할 수도 있습니다.

기본 이미지는 배포에 대한 이미지를 만들 때 시작 지점으로 사용되고, 기본 운영 체제 및 구성 요소를 제공합니다. 다음으로 배포 프로세스에서 모델, Conda 환경 및 기타 자산과 같은 추가 구성 요소를 이미지에 추가합니다.

일반적으로, Docker를 사용하여 종속성을 관리하고, 구성 요소 버전에 대해 보다 엄격한 제어를 유지하거나, 배포하는 동안 시간을 절약하려는 경우 사용자 지정 기본 이미지를 만듭니다. 모델에 필요한 소프트웨어를 설치할 수도 있으며 이 경우 설치 프로세스에 시간이 오래 걸립니다. 기본 이미지를 만들 때 소프트웨어를 설치하면 각 배포에 대해 소프트웨어를 설치하지 않아도 됩니다.

> [!IMPORTANT]
> 모델을 배포하는 경우 웹 서버 또는 IoT Edge 구성 요소와 같은 핵심 구성 요소를 재정의할 수 없습니다. 이러한 구성 요소는 Microsoft에서 테스트하고 지원하는 알려진 작업 환경을 제공합니다.

> [!WARNING]
> Microsoft는 사용자 지정 이미지로 인해 발생하는 문제를 해결하는 데 도움을 주지 못할 수 없습니다. 문제가 발생하는 경우 기본 이미지 또는 Microsoft에서 제공하는 이미지 중 하나를 사용하여 문제가 이미지와 관련된 것인지 확인하라는 메시지가 표시될 수 있습니다.

이 문서는 다음과 같은 두 개의 섹션으로 구분됩니다.

* 사용자 지정 기본 이미지 만들기: 관리자 및 DevOps에게 사용자 지정 이미지를 만들고 Azure CLI 및 Machine Learning CLI를 사용하여 Azure Container Registry에 대한 인증을 구성하는 데 대한 정보를 제공합니다.
* 사용자 지정 기본 이미지를 사용하여 모델 배포: 데이터 과학자 및 DevOps/ML 엔지니어에게 Python SDK 또는 ML CLI에서 학습된 모델을 배포할 때 사용자 지정 이미지를 사용하는 데 대한 정보를 제공합니다.

## <a name="prerequisites"></a>사전 요구 사항

* Azure Machine Learning 작업 영역 자세한 내용은 [작업 영역 만들기](how-to-manage-workspace.md) 문서를 참조하세요.
* [Azure Machine Learning SDK](/python/api/overview/azure/ml/install) 
* [Azure CLI](/cli/azure/install-azure-cli)
* [Azure Machine Learning용 CLI 확장](reference-azure-machine-learning-cli.md)
* 인터넷에서 액세스할 수 있는 [Azure Container Registry](../container-registry/index.yml) 또는 기타 Docker 레지스트리입니다.
* 이 문서의 단계에서는 사용자가 모델 배포의 일부로 __유추 구성__ 개체를 만들고 사용하는 방법을 잘 알고 있다고 가정합니다. 자세한 내용은 [배포 위치 및 방법](how-to-deploy-and-where.md)을 참조하세요.

## <a name="create-a-custom-base-image"></a>사용자 지정 기본 이미지 만들기

이 섹션에서는 Azure Container Registry를 사용하여 Docker 이미지를 저장한다고 가정합니다. Azure Machine Learning에 대한 사용자 지정 이미지를 만들 계획인 경우 다음 검사 목록을 사용합니다.

* Azure Machine Learning 작업 영역에 대해 만든 Azure Container Registry를 사용하시겠습니까 아니면 독립 실행형 Azure Container Registry를 사용하시겠습니까?

    __작업 영역에 대한 컨테이너 레지스트리__ 에 저장된 이미지를 사용하는 경우 레지스트리를 인증할 필요가 없습니다. 인증은 작업 영역에서 처리합니다.

    > [!WARNING]
    > 작업 영역에 대한 Azure Container Registry는 작업 영역을 사용하여 __처음으로 모델을 학습시키거나 배포할 때 생성__ 됩니다. 새 작업 영역을 만들었지만 모델을 학습시키거나 만들지 않은 경우 작업 영역에 대한 Azure Container Registry는 존재하지 않습니다.

    __독립 실행형 컨테이너 레지스트리__ 에 저장된 이미지를 사용하는 경우 최소한 읽기 액세스 권한이 있는 서비스 주체를 구성해야 합니다. 그런 다음 레지스트리의 이미지를 사용하는 모든 사용자에게 서비스 주체 ID(사용자 이름) 및 암호를 제공합니다. 컨테이너 레지스트리를 공개적으로 액세스할 수 있도록 설정하는 경우는 예외입니다.

    프라이빗 Azure Container Registry를 만드는 방법에 대한 자세한 내용은 [프라이빗 컨테이너 레지스트리 만들기](../container-registry/container-registry-get-started-azure-cli.md)를 참조하세요.

    Azure Container Registry에서 서비스 주체를 사용하는 방법에 대한 자세한 내용은 [서비스 주체로 Azure Container Registry 인증](../container-registry/container-registry-auth-service-principal.md)을 참조하세요.

* Azure Container Registry 및 이미지 정보: 이미지 이름을 사용해야 하는 모든 사용자에게 제공합니다. 예를 들어, 이름이 `myregistry`인 레지스트리에 저장된 `myimage`(이)라는 이미지는 모델 배포에 이미지를 사용하는 경우 `myregistry.azurecr.io/myimage`(으)로 참조됩니다.

### <a name="image-requirements"></a>이미지 요구 사항

Azure Machine Learning은 다음 소프트웨어를 제공하는 Docker 이미지만 지원합니다.
* Ubuntu 16.04 이상.
* Conda 4.5.# 이상.
* Python 3.6 이상.

데이터 세트를 사용하려면 libfuse-dev 패키지를 설치하세요. 또한 필요할 수 있는 모든 사용자 공간 패키지를 설치해야 합니다.

Azure ML은 사용자 지정 이미지를 만드는 대신 선택적으로 활용(또는 참조)할 수 있는 Microsoft Container Registry에 게시된 CPU 및 GPU 기본 이미지 세트를 유지합니다. 이러한 이미지에 대한 Dockerfiles를 보려면 [Azure/AzureML-Containers](https://github.com/Azure/AzureML-Containers) GitHub 리포지토리를 참조하세요.

GPU 이미지의 경우 Azure ML은 현재 cuda9 및 cuda10 기본 이미지를 모두 제공합니다. 이러한 기본 이미지에 설치되는 주요 종속성은 다음과 같습니다.

| 종속성 | IntelMPI CPU | OpenMPI CPU | IntelMPI GPU | OpenMPI GPU |
| --- | --- | --- | --- | --- |
| miniconda | ==4.5.11 | ==4.5.11 | ==4.5.11 | ==4.5.11 |
| mpi | intelmpi==2018.3.222 |openmpi==3.1.2 |intelmpi==2018.3.222| openmpi==3.1.2 |
| cuda | - | - | 9.0/10.0 | 9.0/10.0/10.1 |
| cudnn | - | - | 7.4/7.5 | 7.4/7.5 |
| nccl | - | - | 2.4 | 2.4 |
| git | 2.7.4 | 2.7.4 | 2.7.4 | 2.7.4 |

CPU 이미지는 ubuntu 16.04에서 빌드됩니다. cuda9에 대한 GPU 이미지는 nvidia/cuda:9.0-cudnn7-devel-ubuntu16.04에서 빌드됩니다. cuda10에 대한 GPU 이미지는 nvidia/cuda:10.0-cudnn7-devel-ubuntu16.04에서 빌드됩니다.
<a id="getname"></a>

> [!IMPORTANT]
> 사용자 지정 Docker 이미지를 사용하는 경우 재현성을 더 효율적으로 보장하기 위해 패키지 버전을 고정하는 것이 좋습니다.

### <a name="get-container-registry-information"></a>컨테이너 레지스트리 정보 가져오기

이 섹션에서는 Azure Machine Learning 작업 영역에 대한 Azure Container Registry의 이름을 확인하는 방법에 대해 알아봅니다.

> [!WARNING]
> 작업 영역에 대한 Azure Container Registry는 작업 영역을 사용하여 __처음으로 모델을 학습시키거나 배포할 때 생성__ 됩니다. 새 작업 영역을 만들었지만 모델을 학습시키거나 만들지 않은 경우 작업 영역에 대한 Azure Container Registry는 존재하지 않습니다.

Azure Machine Learning을 사용하여 이미 모델을 학습시키거나 배포한 경우 작업 영역에 대한 컨테이너 레지스트리가 만들어진 것입니다. 이 컨테이너 레지스트리의 이름을 찾으려면 다음 단계를 사용합니다.

1. 새로운 셸 또는 명령 프롬프트를 열고 다음 명령을 사용하여 Azure 구독을 인증합니다.

    ```azurecli-interactive
    az login
    ```

    프롬프트에 따라 구독을 인증합니다.

    [!INCLUDE [select-subscription](../../includes/machine-learning-cli-subscription.md)] 

2. 다음 명령을 사용하여 작업 영역에 대한 컨테이너 레지스트리를 나열합니다. `<myworkspace>`을(를) Azure Machine Learning 작업 영역 이름으로 바꿉니다. `<resourcegroup>`을(를) 작업 영역이 포함된 Azure 리소스 그룹으로 바꿉니다.

    ```azurecli-interactive
    az ml workspace show -w <myworkspace> -g <resourcegroup> --query containerRegistry
    ```

    [!INCLUDE [install extension](../../includes/machine-learning-service-install-extension.md)]

    반환되는 정보는 다음 텍스트와 유사합니다.

    ```text
    /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.ContainerRegistry/registries/<registry_name>
    ```

    `<registry_name>` 값은 작업 영역에 대한 Azure Container Registry의 이름입니다.

### <a name="build-a-custom-base-image"></a>사용자 지정 기본 이미지 빌드

이 섹션의 단계에서는 Azure Container Registry에서 사용자 지정 Docker 이미지를 만드는 과정을 안내합니다. 샘플 dockerfiles에 대해서는 [Azure/AzureML-Containers](https://github.com/Azure/AzureML-Containers) GitHub 리포지토리를 참조하세요.

1. `Dockerfile`(이)라는 새 텍스트 파일을 만들고 파일의 내용으로 다음을 사용합니다.

    ```text
    FROM ubuntu:16.04

    ARG CONDA_VERSION=4.7.12
    ARG PYTHON_VERSION=3.7
    ARG AZUREML_SDK_VERSION=1.13.0
    ARG INFERENCE_SCHEMA_VERSION=1.1.0

    ENV LANG=C.UTF-8 LC_ALL=C.UTF-8
    ENV PATH /opt/miniconda/bin:$PATH
    ENV DEBIAN_FRONTEND=noninteractive

    RUN apt-get update --fix-missing && \
        apt-get install -y wget bzip2 && \
        apt-get install -y fuse && \
        apt-get clean -y && \
        rm -rf /var/lib/apt/lists/*

    RUN useradd --create-home dockeruser
    WORKDIR /home/dockeruser
    USER dockeruser

    RUN wget --quiet https://repo.anaconda.com/miniconda/Miniconda3-${CONDA_VERSION}-Linux-x86_64.sh -O ~/miniconda.sh && \
        /bin/bash ~/miniconda.sh -b -p ~/miniconda && \
        rm ~/miniconda.sh && \
        ~/miniconda/bin/conda clean -tipsy
    ENV PATH="/home/dockeruser/miniconda/bin/:${PATH}"

    RUN conda install -y conda=${CONDA_VERSION} python=${PYTHON_VERSION} && \
        pip install azureml-defaults==${AZUREML_SDK_VERSION} inference-schema==${INFERENCE_SCHEMA_VERSION} &&\
        conda clean -aqy && \
        rm -rf ~/miniconda/pkgs && \
        find ~/miniconda/ -type d -name __pycache__ -prune -exec rm -rf {} \;
    ```

2. 셸 또는 명령 프롬프트에서 다음을 사용하여 Azure Container Registry를 인증합니다. `<registry_name>`을(를) 이미지를 저장하려는 컨테이너 레지스트리의 이름으로 바꿉니다.

    ```azurecli-interactive
    az acr login --name <registry_name>
    ```

3. Dockerfile을 업로드하고 빌드하려면 다음 명령을 사용합니다. `<registry_name>`을(를) 이미지를 저장하려는 컨테이너 레지스트리의 이름으로 바꿉니다.

    ```azurecli-interactive
    az acr build --image myimage:v1 --registry <registry_name> --file Dockerfile .
    ```

    > [!TIP]
    > 이 예제에서는 `:v1`의 태그가 이미지에 적용됩니다. 태그가 제공되지 않으면 `:latest`의 태그가 적용됩니다.

    빌드 프로세스 중에 정보는 명령줄로 다시 스트리밍됩니다. 빌드에 성공하면 다음 텍스트와 유사한 메시지가 표시됩니다.

    ```text
    Run ID: cda was successful after 2m56s
    ```

Azure Container Registry를 사용하여 이미지를 빌드하는 방법에 대한 자세한 내용은 [Azure Container Registry 작업을 사용하여 컨테이너 이미지 빌드 및 실행](../container-registry/container-registry-quickstart-task-cli.md)을 참조하세요.

Azure Container Registry에 기존 이미지를 업로드하는 방법에 대한 자세한 내용은 [첫 번째 이미지를 프라이빗 Docker 컨테이너 레지스트리로 푸시](../container-registry/container-registry-get-started-docker-cli.md)를 참조하세요.

## <a name="use-a-custom-base-image"></a>사용자 지정 기본 이미지 사용

사용자 지정 이미지를 사용하려면 다음 정보가 필요합니다.

* __이미지 이름__. 예를 들어, `mcr.microsoft.com/azureml/o16n-sample-user-base/ubuntu-miniconda:latest`은(는) Microsoft에서 제공하는 단순한 Docker 이미지에 대한 경로입니다.

    > [!IMPORTANT]
    > 사용자가 만든 사용자 지정 이미지의 경우 이미지에 사용된 태그를 포함해야 합니다. 예를 들어, `:v1`와(과) 같은 특정 태그로 이미지를 만든 경우입니다. 이미지를 만들 때 특정 태그를 사용하지 않은 경우 `:latest`의 태그가 적용됩니다.

* 이미지가 __프라이빗 리포지토리__ 에 있는 경우 다음 정보가 필요합니다.

    * 레지스트리 __주소__. 예들 들어 `myregistry.azureecr.io`입니다.
    * 레지스트리에 대한 읽기 권한이 있는 서비스 주체 __사용자 이름__ 및 __암호__.

    이 정보가 없는 경우 이미지를 포함하는 Azure Container Registry에 대해 관리자에게 문의하십시오.

### <a name="publicly-available-base-images"></a>공개적으로 사용 가능한 기본 이미지

Microsoft는 공개적으로 액세스할 수 있는 리포지토리에 여러 docker 이미지를 제공하며, 이 섹션의 단계에서 사용할 수 있습니다.

| 이미지 | 설명 |
| ----- | ----- |
| `mcr.microsoft.com/azureml/o16n-sample-user-base/ubuntu-miniconda` | Azure Machine Learning에 대한 핵심 이미지 |
| `mcr.microsoft.com/azureml/onnxruntime:latest` | CPU 추론용 ONNX 런타임 포함 |
| `mcr.microsoft.com/azureml/onnxruntime:latest-cuda` | GPU용 ONNX 런타임 및 CUDA 포함 |
| `mcr.microsoft.com/azureml/onnxruntime:latest-tensorrt` | GPU용 ONNX 런타임 및 TensorRT 포함 |
| `mcr.microsoft.com/azureml/onnxruntime:latest-openvino-vadm` | Movidius<sup>TM</sup> MyriadX VPU 기반 Intel<sup></sup> Vision Accelerator Design용 ONNX 런타임 및 OpenVINO 포함 |
| `mcr.microsoft.com/azureml/onnxruntime:latest-openvino-myriad` | Intel<sup></sup> Movidius<sup>TM</sup> USB 스틱용 ONNX 런타임 및 OpenVINO 포함 |

ONNX 런타임 기본 이미지에 대한 자세한 내용은 GitHub 리포지토리의 [ONNX 런타임 dockerfile 섹션](https://github.com/microsoft/onnxruntime/blob/master/dockerfiles/README.md)을 참조하십시오.

> [!TIP]
> 이러한 이미지는 공개적으로 사용할 수 있으므로 사용할 때 주소, 사용자 이름 또는 암호를 제공할 필요가 없습니다.

자세한 내용은 GitHub의 [Azure Machine Learning 컨테이너](https://github.com/Azure/AzureML-Containers) 리포지토리를 참조하세요.

### <a name="use-an-image-with-the-azure-machine-learning-sdk"></a>Azure Machine Learning SDK를 사용하여 이미지 사용

**작업 영역에 대한 Azure Container Registry** 또는 **공개적으로 액세스할 수 있는 컨테이너 레지스트리** 에 저장된 이미지를 사용하려면 다음과 같은 [환경](/python/api/azureml-core/azureml.core.environment.environment) 특성을 설정합니다.

+ `docker.enabled=True`
+ `docker.base_image`: 레지스트리 및 경로를 이미지로 설정합니다.

```python
from azureml.core.environment import Environment
# Create the environment
myenv = Environment(name="myenv")
# Enable Docker and reference an image
myenv.docker.enabled = True
myenv.docker.base_image = "mcr.microsoft.com/azureml/o16n-sample-user-base/ubuntu-miniconda:latest"
```

작업 영역에 없는 __프라이빗 컨테이너 레지스트리__ 의 이미지를 사용하려면 `docker.base_image_registry`을(를) 사용하여 리포지토리의 주소와 사용자 이름 및 암호를 지정해야 합니다.

```python
# Set the container registry information
myenv.docker.base_image_registry.address = "myregistry.azurecr.io"
myenv.docker.base_image_registry.username = "username"
myenv.docker.base_image_registry.password = "password"

myenv.inferencing_stack_version = "latest"  # This will install the inference specific apt packages.

# Define the packages needed by the model and scripts
from azureml.core.conda_dependencies import CondaDependencies
conda_dep = CondaDependencies()
# you must list azureml-defaults as a pip dependency
conda_dep.add_pip_package("azureml-defaults")
myenv.python.conda_dependencies=conda_dep
```

pip 종속성으로 1.0.45 이상 버전의 azureml-defaults를 추가해야 합니다. 이 패키지에는 모델을 웹 서비스로 호스팅하는 데 필요한 기능이 포함되어 있습니다. 또한 환경의 inferencing_stack_version 속성을 "최신"으로 설정해야 합니다. 이렇게 하면 웹 서비스에 필요한 특정 apt 패키지가 설치됩니다. 

환경을 정의한 후 [InferenceConfig](/python/api/azureml-core/azureml.core.model.inferenceconfig) 개체와 함께 사용하여 모델 및 웹 서비스가 실행될 유추 환경을 정의합니다.

```python
from azureml.core.model import InferenceConfig
# Use environment in InferenceConfig
inference_config = InferenceConfig(entry_script="score.py",
                                   environment=myenv)
```

이 시점에서 배포를 계속할 수 있습니다. 예를 들어 다음 코드 조각은 유추 구성 및 사용자 지정 이미지를 사용하여 웹 서비스를 로컬로 배포합니다.

```python
from azureml.core.webservice import LocalWebservice, Webservice

deployment_config = LocalWebservice.deploy_configuration(port=8890)
service = Model.deploy(ws, "myservice", [model], inference_config, deployment_config)
service.wait_for_deployment(show_output = True)
print(service.state)
```

배포에 대한 자세한 내용은 [Azure Machine Learning을 사용하여 모델 배포](how-to-deploy-and-where.md)를 참조하세요.

Python 환경을 사용자 지정하는 방법에 대한 자세한 내용은 [학습 및 배포 환경 만들기 및 관리](how-to-use-environments.md)를 참조하세요. 

### <a name="use-an-image-with-the-machine-learning-cli"></a>Machine Learning CLI를 사용하여 이미지 사용

> [!IMPORTANT]
> 현재 Machine Learning CLI는 작업 영역 또는 공개적으로 액세스할 수 있는 리포지토리에 대해 Azure Container Registry의 이미지를 사용할 수 있습니다. 독립 실행형 프라이빗 레지스트리의 이미지는 사용할 수 없습니다.

Machine Learning CLI를 사용하여 모델을 배포하기 전에 사용자 지정 이미지를 사용하는 [환경](/python/api/azureml-core/azureml.core.environment.environment)을 만듭니다. 그런 다음 환경을 참조하는 유추 구성 파일을 만듭니다. 유추 구성 파일에서 직접 환경을 정의할 수도 있습니다. 다음 JSON 문서는 공용 컨테이너 레지스트리에서 이미지를 참조하는 방법을 보여 줍니다. 이 예제에서 환경 변수는 다음과 같이 인라인으로 정의됩니다.

```json
{
    "entryScript": "score.py",
    "environment": {
        "docker": {
            "arguments": [],
            "baseDockerfile": null,
            "baseImage": "mcr.microsoft.com/azureml/o16n-sample-user-base/ubuntu-miniconda:latest",
            "enabled": false,
            "sharedVolumes": true,
            "shmSize": null
        },
        "environmentVariables": {
            "EXAMPLE_ENV_VAR": "EXAMPLE_VALUE"
        },
        "name": "my-deploy-env",
        "python": {
            "baseCondaEnvironment": null,
            "condaDependencies": {
                "channels": [
                    "conda-forge"
                ],
                "dependencies": [
                    "python=3.6.2",
                    {
                        "pip": [
                            "azureml-defaults",
                            "azureml-telemetry",
                            "scikit-learn",
                            "inference-schema[numpy-support]"
                        ]
                    }
                ],
                "name": "project_environment"
            },
            "condaDependenciesFile": null,
            "interpreterPath": "python",
            "userManagedDependencies": false
        },
        "version": "1"
    }
}
```

이 파일은 `az ml model deploy` 명령과 함께 사용됩니다. `--ic` 매개 변수는 유추 구성 파일을 지정하는 데 사용됩니다.

```azurecli
az ml model deploy -n myservice -m mymodel:1 --ic inferenceconfig.json --dc deploymentconfig.json --ct akscomputetarget
```

ML CLI를 사용하여 모델을 배포하는 방법에 대한 자세한 내용은 [Azure Machine Learning에 대한 CLI 확장](reference-azure-machine-learning-cli.md#model-registration-profiling-deployment) 문서의 "모델 등록, 프로파일링 및 배포" 섹션을 참조하세요.

## <a name="next-steps"></a>다음 단계

* [배포 위치 및 방법](how-to-deploy-and-where.md)에 대해 자세히 알아보세요.
* [Azure Pipelines를 사용하여 기계 학습 모델을 학습시키고 배포](/azure/devops/pipelines/targets/azure-machine-learning)하는 방법을 알아보세요.