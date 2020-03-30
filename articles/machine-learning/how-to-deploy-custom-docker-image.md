---
title: 사용자 지정 Docker 이미지로 모델 배포
titleSuffix: Azure Machine Learning
description: Azure 기계 학습 모델을 배포할 때 사용자 지정 Docker 기본 이미지를 사용하는 방법을 알아봅니다. Azure Machine Learning은 기본 기본 이미지를 제공하지만 고유한 기본 이미지를 사용할 수도 있습니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: jordane
author: jpe316
ms.reviewer: larryfr
ms.date: 03/16/2020
ms.openlocfilehash: 1f11d6667c22990b3cba2079959bec6f413d5951
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80296948"
---
# <a name="deploy-a-model-using-a-custom-docker-base-image"></a>사용자 지정 Docker 기본 이미지를 사용하여 모델 배포
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Azure 기계 학습을 사용하여 학습된 모델을 배포할 때 사용자 지정 Docker 기본 이미지를 사용하는 방법을 알아봅니다.

학습된 모델을 웹 서비스 또는 IoT Edge 장치에 배포하면 들어오는 요청을 처리하는 웹 서버가 포함된 패키지가 만들어집니다.

Azure 기계 학습은 기본 Docker 기본 이미지를 제공하므로 만드는 것에 대해 걱정할 필요가 없습니다. Azure 기계 학습 __환경을__ 사용하여 특정 기본 이미지를 선택하거나 제공하는 사용자 지정 이미지를 사용할 수도 있습니다.

기본 이미지는 배포를 위해 이미지를 만들 때 시작점으로 사용됩니다. 기본 운영 체제 및 구성 요소를 제공합니다. 그런 다음 배포 프로세스는 모델, conda 환경 및 기타 자산과 같은 추가 구성 요소를 배포하기 전에 이미지에 추가합니다.

일반적으로 Docker를 사용하여 종속성을 관리하고, 구성 요소 버전을 보다 엄격하게 제어하거나, 배포 하는 동안 시간을 절약하려는 경우 사용자 지정 기본 이미지를 만듭니다. 예를 들어 특정 버전의 파이썬, Conda 또는 기타 구성 요소를 표준화할 수 있습니다. 설치 프로세스에 시간이 오래 걸리는 모델에 필요한 소프트웨어를 설치할 수도 있습니다. 기본 이미지를 만들 때 소프트웨어를 설치하면 각 배포에 대해 소프트웨어를 설치할 필요가 없습니다.

> [!IMPORTANT]
> 모델을 배포할 때 웹 서버 또는 IoT Edge 구성 요소와 같은 핵심 구성 요소를 재정의할 수 없습니다. 이러한 구성 요소는 Microsoft에서 테스트하고 지원하는 알려진 작업 환경을 제공합니다.

> [!WARNING]
> Microsoft는 사용자 지정 이미지로 인한 문제 해결을 도와주지 못할 수 있습니다. 문제가 발생하면 기본 이미지 또는 Microsoft에서 제공하는 이미지 중 하나를 사용하여 문제가 이미지와 관련이 있는지 확인하라는 메시지가 표시될 수 있습니다.

이 문서는 두 부분으로 나뉩니다.

* 사용자 지정 기본 이미지 만들기: Azure CLI 및 기계 학습 CLI를 사용하여 사용자 지정 이미지를 만들고 Azure 컨테이너 레지스트리에 인증을 구성하는 방법에 대한 정보를 관리자 및 DevOps에 제공합니다.
* 사용자 지정 기본 이미지를 사용하여 모델 배포: Python SDK 또는 ML CLI에서 학습된 모델을 배포할 때 사용자 지정 이미지를 사용하는 데이터 과학자 및 DevOps/ ML 엔지니어에게 정보를 제공합니다.

## <a name="prerequisites"></a>사전 요구 사항

* Azure 기계 학습 작업 그룹입니다. 자세한 내용은 작업 [영역 만들기](how-to-manage-workspace.md) 문서를 참조하십시오.
* [Azure 기계 학습 SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py). 
* [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).
* [Azure 기계 학습에 대한 CLI 확장.](reference-azure-machine-learning-cli.md)
* 인터넷에서 액세스할 수 있는 [Azure 컨테이너 레지스트리](/azure/container-registry) 또는 기타 Docker 레지스트리입니다.
* 이 문서의 단계는 모델 배포의 일부로 __추론 구성__ 개체를 만들고 사용하는 데 익숙하다고 가정합니다. 자세한 내용은 [배포 할 위치 및 방법의](how-to-deploy-and-where.md#prepare-to-deploy)"배포 준비" 섹션을 참조하십시오.

## <a name="create-a-custom-base-image"></a>사용자 지정 기본 이미지 만들기

이 섹션의 정보는 Azure 컨테이너 레지스트리를 사용하여 Docker 이미지를 저장하는 것으로 가정합니다. Azure 기계 학습에 대 한 사용자 지정 이미지를 만들 계획 할 때 다음 검사 목록을 사용 합니다.

* Azure 기계 학습 작업 영역 또는 독립 실행형 Azure 컨테이너 레지스트리에 대해 만든 Azure 컨테이너 레지스트리를 사용합니까?

    작업 영역에 대한 __컨테이너 레지스트리에__저장된 이미지를 사용하는 경우 레지스트리를 인증할 필요가 없습니다. 인증은 작업 영역에서 처리됩니다.

    > [!WARNING]
    > 작업 영역에 대한 Azure 컨테이너 레지스트리는 작업 영역을 사용하여 __모델을 처음 학습하거나 배포할 때 만들어집니다.__ 새 작업 영역을 만들었지만 학습되거나 모델을 만들지 않은 경우 작업 영역에 대한 Azure 컨테이너 레지스트리가 없습니다.

    작업 영역에 대한 Azure 컨테이너 레지스트리 이름을 검색하는 방법에 대한 자세한 내용은 이 문서의 [컨테이너 레지스트리 이름 받기](#getname) 섹션을 참조하십시오.

    __독립 실행형 컨테이너 레지스트리에__저장된 이미지를 사용하는 경우 최소한 읽기 액세스 권한이 있는 서비스 주체를 구성해야 합니다. 그런 다음 레지스트리의 이미지를 사용하는 모든 사용자에게 서비스 주체 ID(사용자 이름)와 암호를 제공합니다. 컨테이너 레지스트리를 공개적으로 액세스할 수 있도록 하는 경우는 예외입니다.

    개인 Azure 컨테이너 레지스트리를 만드는 방법에 대한 자세한 내용은 [개인 컨테이너 레지스트리 만들기](/azure/container-registry/container-registry-get-started-azure-cli)를 참조하십시오.

    Azure 컨테이너 레지스트리를 사용하여 서비스 주체를 사용하는 방법에 대한 자세한 내용은 [서비스 주체가 있는 Azure 컨테이너 레지스트리 인증을](/azure/container-registry/container-registry-auth-service-principal)참조하십시오.

* Azure 컨테이너 레지스트리 및 이미지 정보: 이미지 이름을 사용해야 하는 모든 사람에게 이미지 이름을 제공합니다. 예를 들어 `myimage`, 명명된 레지스트리에 `myregistry`저장된 이미지는 모델 `myregistry.azurecr.io/myimage` 배포에 이미지를 사용할 때참조됩니다.

* 이미지 요구 사항: Azure 기계 학습은 다음 소프트웨어를 제공하는 Docker 이미지만 지원합니다.

    * 우분투 16.04 이상.
    * 콘다 4.5.# 이상.
    * 파이썬 3.5.# 또는 3.6.#.

<a id="getname"></a>

### <a name="get-container-registry-information"></a>컨테이너 레지스트리 정보 얻기

이 섹션에서는 Azure 기계 학습 작업 영역에 대한 Azure 컨테이너 레지스트리의 이름을 얻는 방법을 알아봅니다.

> [!WARNING]
> 작업 영역에 대한 Azure 컨테이너 레지스트리는 작업 영역을 사용하여 __모델을 처음 학습하거나 배포할 때 만들어집니다.__ 새 작업 영역을 만들었지만 학습되거나 모델을 만들지 않은 경우 작업 영역에 대한 Azure 컨테이너 레지스트리가 없습니다.

Azure 기계 학습을 사용하여 모델을 이미 학습하거나 배포한 경우 작업 영역에 대한 컨테이너 레지스트리가 만들어졌습니다. 이 컨테이너 레지스트리의 이름을 찾으려면 다음 단계를 사용합니다.

1. 새 셸 또는 명령 프롬프트를 열고 다음 명령을 사용하여 Azure 구독을 인증합니다.

    ```azurecli-interactive
    az login
    ```

    프롬프트에 따라 구독을 인증합니다.

    [!INCLUDE [select-subscription](../../includes/machine-learning-cli-subscription.md)]

2. 다음 명령을 사용하여 작업 영역에 대한 컨테이너 레지스트리를 나열합니다. Azure `<myworkspace>` 기계 학습 작업 영역 이름으로 바꿉니다. 작업 `<resourcegroup>` 영역이 포함된 Azure 리소스 그룹으로 바꿉습니다.

    ```azurecli-interactive
    az ml workspace show -w <myworkspace> -g <resourcegroup> --query containerRegistry
    ```

    [!INCLUDE [install extension](../../includes/machine-learning-service-install-extension.md)]

    반환되는 정보는 다음 텍스트와 유사합니다.

    ```text
    /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.ContainerRegistry/registries/<registry_name>
    ```

    `<registry_name>` 값은 작업 영역에 대한 Azure 컨테이너 레지스트리의 이름입니다.

### <a name="build-a-custom-base-image"></a>사용자 지정 기본 이미지 빌드

이 섹션의 단계는 Azure 컨테이너 레지스트리에서 사용자 지정 Docker 이미지를 만드는 연습입니다.

1. 의 라는 `Dockerfile`새 텍스트 파일을 만들고 다음 텍스트를 내용으로 사용 합니다.

    ```text
    FROM ubuntu:16.04

    ARG CONDA_VERSION=4.5.12
    ARG PYTHON_VERSION=3.6

    ENV LANG=C.UTF-8 LC_ALL=C.UTF-8
    ENV PATH /opt/miniconda/bin:$PATH

    RUN apt-get update --fix-missing && \
        apt-get install -y wget bzip2 && \
        apt-get clean && \
        rm -rf /var/lib/apt/lists/*

    RUN wget --quiet https://repo.anaconda.com/miniconda/Miniconda3-${CONDA_VERSION}-Linux-x86_64.sh -O ~/miniconda.sh && \
        /bin/bash ~/miniconda.sh -b -p /opt/miniconda && \
        rm ~/miniconda.sh && \
        /opt/miniconda/bin/conda clean -tipsy

    RUN conda install -y conda=${CONDA_VERSION} python=${PYTHON_VERSION} && \
        conda clean -aqy && \
        rm -rf /opt/miniconda/pkgs && \
        find / -type d -name __pycache__ -prune -exec rm -rf {} \;
    ```

2. 셸 또는 명령 프롬프트에서 다음을 사용하여 Azure 컨테이너 레지스트리를 인증합니다. `<registry_name>` 이미지를 저장할 컨테이너 레지스트리의 이름으로 바꿉니다.

    ```azurecli-interactive
    az acr login --name <registry_name>
    ```

3. Dockerfile을 업로드하고 빌드하려면 다음 명령을 사용합니다. 이미지를 `<registry_name>` 저장할 컨테이너 레지스트리의 이름으로 바꿉니다.

    ```azurecli-interactive
    az acr build --image myimage:v1 --registry <registry_name> --file Dockerfile .
    ```

    > [!TIP]
    > 이 예제에서는 이미지에 `:v1` 태그가 적용됩니다. 태그가 제공되지 않으면 태그가 `:latest` 적용됩니다.

    빌드 프로세스 중에 정보가 명령줄로 다시 스트리밍됩니다. 빌드가 성공하면 다음 텍스트와 유사한 메시지가 나타납니다.

    ```text
    Run ID: cda was successful after 2m56s
    ```

Azure 컨테이너 레지스트리를 사용하여 이미지 빌드에 대한 자세한 내용은 [Azure 컨테이너 레지스트리 작업을 사용하여 컨테이너 이미지 빌드 및 실행을](https://docs.microsoft.com/azure/container-registry/container-registry-quickstart-task-cli) 참조하세요.

Azure 컨테이너 레지스트리에 기존 이미지를 업로드하는 방법에 대한 자세한 내용은 [첫 번째 이미지를 개인 Docker 컨테이너 레지스트리로 푸시를](/azure/container-registry/container-registry-get-started-docker-cli)참조하십시오.

## <a name="use-a-custom-base-image"></a>사용자 지정 기본 이미지 사용

사용자 지정 이미지를 사용하려면 다음 정보가 필요합니다.

* __이미지 이름__. 예를 들어 `mcr.microsoft.com/azureml/o16n-sample-user-base/ubuntu-miniconda` Microsoft에서 제공하는 기본 Docker 이미지에 대한 경로입니다.

    > [!IMPORTANT]
    > 만든 사용자 지정 이미지의 경우 이미지와 함께 사용된 태그를 포함해야 합니다. 예를 들어 이미지가 다음과 같은 특정 태그로 `:v1`만들어진 경우 와 같은. 이미지를 만들 때 특정 태그를 사용하지 않은 경우 `:latest` 태그가 적용되었습니다.

* 이미지가 __개인 리포지토리에__있는 경우 다음 정보가 필요합니다.

    * 레지스트리 __주소__. `myregistry.azureecr.io`)을 입력합니다.
    * 레지스트리에 대한 읽기 액세스 권한이 있는 서비스 주체 __사용자 이름__ 및 __암호입니다.__

    이 정보가 없는 경우 이미지가 포함된 Azure 컨테이너 레지스트리의 관리자에게 문의하십시오.

### <a name="publicly-available-base-images"></a>공개적으로 사용 가능한 기본 이미지

Microsoft는 공개적으로 액세스할 수 있는 리포지토리에 여러 도커 이미지를 제공하며 이 섹션의 단계와 함께 사용할 수 있습니다.

| 이미지 | 설명 |
| ----- | ----- |
| `mcr.microsoft.com/azureml/o16n-sample-user-base/ubuntu-miniconda` | Azure 기계 학습을 위한 기본 이미지 |
| `mcr.microsoft.com/azureml/onnxruntime:latest` | CPU 추론을 위한 ONNX 런타임 포함 |
| `mcr.microsoft.com/azureml/onnxruntime:latest-cuda` | GPU용 ONNX 런타임 및 CUDA 포함 |
| `mcr.microsoft.com/azureml/onnxruntime:latest-tensorrt` | GPU용 ONNX 런타임 및 텐서RT 포함 |
| `mcr.microsoft.com/azureml/onnxruntime:latest-openvino-vadm ` | Movidius<sup> </sup> <sup>TM</sup> MyriadX Vpus를 기반으로 하는 인텔 비전 가속기 설계를 위한 ONNX 런타임 및 OpenVINO 포함 |
| `mcr.microsoft.com/azureml/onnxruntime:latest-openvino-myriad` | 인텔<sup> </sup> 모비디우스<sup>TM</sup> USB 스틱ONNX 런타임 및 오픈비노 포함 |

ONNX 런타임 기본 이미지에 대한 자세한 내용은 GitHub 리포지토리의 [ONNX 런타임 도커파일 섹션을](https://github.com/microsoft/onnxruntime/blob/master/dockerfiles/README.md) 참조하십시오.

> [!TIP]
> 이러한 이미지는 공개적으로 사용할 수 있으므로 사용할 때 주소, 사용자 이름 또는 암호를 제공할 필요가 없습니다.

자세한 내용은 [Azure 기계 학습 컨테이너를](https://github.com/Azure/AzureML-Containers)참조하십시오.

> [!TIP]
>Azure 기계 학습 SDK 버전 __1.0.22 이상을__ 사용하여 __모델이 Azure 기계 학습 계산에 대해 학습된 경우__학습 중에 이미지가 만들어집니다. 이 이미지의 이름을 검색하려면 `run.properties["AzureML.DerivedImageName"]`을 사용합니다. 다음 예제에서는 이 이미지를 사용하는 방법을 보여 줍니다.
>
> ```python
> # Use an image built during training with SDK 1.0.22 or greater
> image_config.base_image = run.properties["AzureML.DerivedImageName"]
> ```

### <a name="use-an-image-with-the-azure-machine-learning-sdk"></a>Azure 기계 학습 SDK에서 이미지 사용

**작업 영역에 Azure 컨테이너 레지스트리에**저장된 이미지를 사용하거나 **공개적으로 액세스할 수 있는 컨테이너 레지스트리를**사용하려면 다음 [환경](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py) 특성을 설정합니다.

+ `docker.enabled=True`
+ `docker.base_image`: 레지스트리로 설정하고 이미지에 대한 경로를 설정합니다.

```python
from azureml.core.environment import Environment
# Create the environment
myenv = Environment(name="myenv")
# Enable Docker and reference an image
myenv.docker.enabled = True
myenv.docker.base_image = "mcr.microsoft.com/azureml/o16n-sample-user-base/ubuntu-miniconda"
```

작업 영역에 없는 __개인 컨테이너 레지스트리의__ 이미지를 사용하려면 리포지토리의 주소와 사용자 이름 및 암호를 지정해야 합니다. `docker.base_image_registry`

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

버전 >= 1.0.45를 핍 종속성으로 azureml 기본값을 추가해야 합니다. 이 패키지에는 모델을 웹 서비스로 호스팅하는 데 필요한 기능이 포함되어 있습니다. 또한 환경에 inferencing_stack_version 속성을 "최신"으로 설정해야 하며 웹 서비스에 필요한 특정 apt 패키지를 설치합니다. 

환경을 정의한 후 [InferenceConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.inferenceconfig?view=azure-ml-py) 개체와 함께 사용하여 모델 및 웹 서비스가 실행되는 추론 환경을 정의합니다.

```python
from azureml.core.model import InferenceConfig
# Use environment in InferenceConfig
inference_config = InferenceConfig(entry_script="score.py",
                                   environment=myenv)
```

이 시점에서 배포를 계속할 수 있습니다. 예를 들어 다음 코드 코드 조각은 추론 구성 및 사용자 지정 이미지를 사용하여 웹 서비스를 로컬로 배포합니다.

```python
from azureml.core.webservice import LocalWebservice, Webservice

deployment_config = LocalWebservice.deploy_configuration(port=8890)
service = Model.deploy(ws, "myservice", [model], inference_config, deployment_config)
service.wait_for_deployment(show_output = True)
print(service.state)
```

배포에 대한 자세한 내용은 [Azure 기계 학습을 통해 모델 배포를](how-to-deploy-and-where.md)참조하십시오.

Python 환경 사용자 지정에 대한 자세한 내용은 [교육 및 배포를 위한 환경 만들기 및 관리를](how-to-use-environments.md)참조하십시오. 

### <a name="use-an-image-with-the-machine-learning-cli"></a>기계 학습 CLI와 함께 이미지 사용

> [!IMPORTANT]
> 현재 기계 학습 CLI는 작업 영역 또는 공개적으로 액세스할 수 있는 리포지토리에 Azure 컨테이너 레지스트리의 이미지를 사용할 수 있습니다. 독립 실행형 개인 레지스트리의 이미지를 사용할 수 없습니다.

기계 학습 CLI를 사용하여 모델을 배포하기 전에 사용자 지정 이미지를 사용하는 [환경을](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py) 만듭니다. 그런 다음 환경을 참조하는 추론 구성 파일을 만듭니다. 추론 구성 파일에서 직접 환경을 정의할 수도 있습니다. 다음 JSON 문서에서는 공용 컨테이너 레지스트리에서 이미지를 참조하는 방법을 보여 줍니다. 이 예제에서 환경은 인라인으로 정의됩니다.

```json
{
    "entryScript": "score.py",
    "environment": {
        "docker": {
            "arguments": [],
            "baseDockerfile": null,
            "baseImage": "mcr.microsoft.com/azureml/o16n-sample-user-base/ubuntu-miniconda",
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

이 파일은 `az ml model deploy` 명령과 함께 사용됩니다. 매개 `--ic` 변수는 추론 구성 파일을 지정하는 데 사용됩니다.

```azurecli
az ml model deploy -n myservice -m mymodel:1 --ic inferenceconfig.json --dc deploymentconfig.json --ct akscomputetarget
```

ML CLI를 사용하여 모델을 배포하는 방법에 대한 자세한 내용은 Azure 기계 학습 문서의 [CLI 확장의](reference-azure-machine-learning-cli.md#model-registration-profiling-deployment) "모델 등록, 프로파일링 및 배포" 섹션을 참조하십시오.

## <a name="next-steps"></a>다음 단계

* [배포 할 위치 및 방법에](how-to-deploy-and-where.md)대해 자세히 알아봅니다.
* [Azure 파이프라인을 사용하여 기계 학습 모델을 학습하고 배포하는](/azure/devops/pipelines/targets/azure-machine-learning?view=azure-devops)방법에 대해 알아봅니다.
