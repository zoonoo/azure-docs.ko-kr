---
title: 사용자 지정 Docker 이미지를 사용 하 여 모델 배포
titleSuffix: Azure Machine Learning
description: Azure Machine Learning 모델을 배포할 때 사용자 지정 Docker 기본 이미지를 사용 하는 방법에 대해 알아봅니다. Azure Machine Learning 기본 이미지를 제공 하는 동안 고유한 기본 이미지를 사용할 수도 있습니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: jordane
author: jpe316
ms.reviewer: larryfr
ms.date: 03/16/2020
ms.openlocfilehash: a237beb72e35a236e353c58db520a8d611fdfdcd
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81618004"
---
# <a name="deploy-a-model-using-a-custom-docker-base-image"></a>사용자 지정 Docker 기본 이미지를 사용 하 여 모델 배포
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Azure Machine Learning를 사용 하 여 학습 된 모델을 배포할 때 사용자 지정 Docker 기본 이미지를 사용 하는 방법을 알아봅니다.

웹 서비스 또는 IoT Edge 장치에 학습 된 모델을 배포할 때 들어오는 요청을 처리할 웹 서버를 포함 하는 패키지가 생성 됩니다.

Azure Machine Learning는 기본 Docker 기본 이미지를 제공 하므로 만들 때 걱정할 필요가 없습니다. Azure Machine Learning __환경을__ 사용 하 여 특정 기본 이미지를 선택 하거나 사용자가 제공 하는 사용자 지정 이미지를 사용할 수도 있습니다.

기본 이미지는 배포에 대해 이미지를 만들 때 시작 지점으로 사용 됩니다. 기본 운영 체제 및 구성 요소를 제공 합니다. 그런 다음 배포 프로세스에서 모델, conda 환경 및 기타 자산과 같은 추가 구성 요소를 배포 하기 전에 이미지에 추가 합니다.

일반적으로 Docker를 사용 하 여 종속성을 관리 하거나, 구성 요소 버전에 대 한 완전 한 제어를 유지 관리 하거나, 배포 하는 동안 시간을 절약 하려는 경우 사용자 지정 기본 이미지를 만듭니다. 예를 들어 특정 버전의 Python, Conda 또는 기타 구성 요소를 표준화 하려고 할 수 있습니다. 모델에 필요한 소프트웨어를 설치 하 여 설치 프로세스에 오랜 시간이 걸릴 수도 있습니다. 기본 이미지를 만들 때 소프트웨어를 설치 하면 각 배포에 대해 설치 하지 않아도 됩니다.

> [!IMPORTANT]
> 모델을 배포할 때는 웹 서버 또는 IoT Edge 구성 요소와 같은 핵심 구성 요소를 재정의할 수 없습니다. 이러한 구성 요소는 Microsoft에서 테스트 하 고 지 원하는 알려진 작업 환경을 제공 합니다.

> [!WARNING]
> Microsoft는 사용자 지정 이미지에 의해 발생 한 문제를 해결 하는 데 도움을 받을 수 없습니다. 문제가 발생 하는 경우 기본 이미지 또는 Microsoft에서 제공 하는 이미지 중 하나를 사용 하 여 문제가 이미지와 관련 된 것인지를 확인 하는 메시지가 표시 될 수 있습니다.

이 문서는 두 개의 섹션으로 구분 되어 있습니다.

* 사용자 지정 기본 이미지 만들기: 사용자 지정 이미지를 만들고 Azure CLI 및 Machine Learning CLI를 사용 하 여 Azure Container Registry에 대 한 인증을 구성 하는 데 관리자 및 DevOps에 정보를 제공 합니다.
* 사용자 지정 기본 이미지를 사용 하 여 모델 배포: Python SDK 또는 ML CLI에서 학습 된 모델을 배포할 때 사용자 지정 이미지를 사용 하 여 데이터 과학자 및 DevOps/ML 엔지니어에 게 정보를 제공 합니다.

## <a name="prerequisites"></a>전제 조건

* Azure Machine Learning 작업 그룹입니다. 자세한 내용은 [작업 영역 만들기](how-to-manage-workspace.md) 문서를 참조 하세요.
* [AZURE MACHINE LEARNING SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py)입니다. 
* [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)입니다.
* [Azure Machine Learning에 대 한 CLI 확장](reference-azure-machine-learning-cli.md)입니다.
* 인터넷에서 액세스할 수 있는 [Azure Container Registry](/azure/container-registry) 또는 기타 Docker 레지스트리
* 이 문서의 단계에서는 모델 배포의 일부로 __유추 구성__ 개체를 만들고 사용 하는 방법을 잘 알고 있다고 가정 합니다. 자세한 내용은 [배포할 위치 및 방법](how-to-deploy-and-where.md#prepare-to-deploy)의 "배포 준비" 섹션을 참조 하세요.

## <a name="create-a-custom-base-image"></a>사용자 지정 기본 이미지 만들기

이 섹션에서는 Azure Container Registry를 사용 하 여 Docker 이미지를 저장 한다고 가정 합니다. Azure Machine Learning에 대 한 사용자 지정 이미지를 만들 계획인 경우 다음 검사 목록을 사용 합니다.

* Azure Machine Learning 작업 영역에 대해 만든 Azure Container Registry 또는 독립 실행형 Azure Container Registry을 사용 하나요?

    __작업 영역에 대 한 컨테이너 레지스트리에__저장 된 이미지를 사용 하는 경우 레지스트리에 인증할 필요가 없습니다. 작업 영역에서 인증을 처리 합니다.

    > [!WARNING]
    > 작업 영역에 대 한 Azure Container Registry는 처음으로 작업 영역을 사용 하 여 __모델을 학습 하거나 배포할 때 생성__ 됩니다. 새 작업 영역을 만들었지만 모델을 학습 하거나 만들지 않은 경우 작업 영역에 대 한 Azure Container Registry 없습니다.

    작업 영역에 대 한 Azure Container Registry의 이름을 검색 하는 방법에 대 한 자세한 내용은이 문서의 [컨테이너 레지스트리 이름 가져오기](#getname) 섹션을 참조 하세요.

    __독립 실행형 컨테이너 레지스트리에__저장 된 이미지를 사용 하는 경우 최소한 읽기 액세스 권한이 있는 서비스 주체를 구성 해야 합니다. 그런 다음 레지스트리의 이미지를 사용 하는 모든 사용자에 게 서비스 주체 ID (사용자 이름) 및 암호를 제공 합니다. 컨테이너 레지스트리를 공개적으로 액세스할 수 있도록 설정 하는 경우는 예외입니다.

    개인 Azure Container Registry을 만드는 방법에 대 한 자세한 내용은 [개인 컨테이너 레지스트리 만들기](/azure/container-registry/container-registry-get-started-azure-cli)를 참조 하세요.

    Azure Container Registry에서 서비스 주체를 사용 하는 방법에 대 한 자세한 내용은 [서비스 사용자로 인증 Azure Container Registry](/azure/container-registry/container-registry-auth-service-principal)을 참조 하세요.

* Azure Container Registry 및 이미지 정보: 이미지 이름을 사용 해야 하는 모든 사용자에 게 제공 합니다. 예를 들어 라는 `myimage` `myregistry`레지스트리에 저장 된 이라는 이미지는 모델 배포에 이미지를 사용 `myregistry.azurecr.io/myimage` 하는 경우로 참조 됩니다.

* 이미지 요구 사항: Azure Machine Learning는 다음과 같은 소프트웨어를 제공 하는 Docker 이미지만 지원 합니다.

    * Ubuntu 16.04 이상.
    * Conda 4.5. # 이상
    * Python 3.5. # 또는 3.6. #.

<a id="getname"></a>

### <a name="get-container-registry-information"></a>컨테이너 레지스트리 정보 가져오기

이 섹션에서는 Azure Machine Learning 작업 영역에 대 한 Azure Container Registry의 이름을 가져오는 방법에 대해 알아봅니다.

> [!WARNING]
> 작업 영역에 대 한 Azure Container Registry는 처음으로 작업 영역을 사용 하 여 __모델을 학습 하거나 배포할 때 생성__ 됩니다. 새 작업 영역을 만들었지만 모델을 학습 하거나 만들지 않은 경우 작업 영역에 대 한 Azure Container Registry 없습니다.

Azure Machine Learning를 사용 하 여 모델을 이미 학습 하거나 배포한 경우 작업 영역에 대해 컨테이너 레지스트리를 만들었습니다. 이 컨테이너 레지스트리의 이름을 찾으려면 다음 단계를 사용 합니다.

1. 새 셸 또는 명령 프롬프트를 열고 다음 명령을 사용 하 여 Azure 구독에 인증 합니다.

    ```azurecli-interactive
    az login
    ```

    프롬프트에 따라 구독에 인증 합니다.

    [!INCLUDE [select-subscription](../../includes/machine-learning-cli-subscription.md)] 

2. 다음 명령을 사용 하 여 작업 영역에 대 한 컨테이너 레지스트리를 나열 합니다. 을 `<myworkspace>` Azure Machine Learning 작업 영역 이름으로 바꿉니다. 을 `<resourcegroup>` 작업 영역을 포함 하는 Azure 리소스 그룹으로 바꿉니다.

    ```azurecli-interactive
    az ml workspace show -w <myworkspace> -g <resourcegroup> --query containerRegistry
    ```

    [!INCLUDE [install extension](../../includes/machine-learning-service-install-extension.md)]

    반환되는 정보는 다음 텍스트와 유사합니다.

    ```text
    /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.ContainerRegistry/registries/<registry_name>
    ```

    `<registry_name>` 값은 작업 영역에 대 한 Azure Container Registry의 이름입니다.

### <a name="build-a-custom-base-image"></a>사용자 지정 기본 이미지 빌드

이 섹션의 단계에서는 Azure Container Registry에서 사용자 지정 Docker 이미지를 만드는 과정을 안내 합니다.

1. 이라는 `Dockerfile`새 텍스트 파일을 만들고 내용으로 다음 텍스트를 사용 합니다.

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

2. 셸 또는 명령 프롬프트에서 다음을 사용 하 여 Azure Container Registry에 인증 합니다. 을 `<registry_name>` 이미지를 저장 하려는 컨테이너 레지스트리의 이름으로 바꿉니다.

    ```azurecli-interactive
    az acr login --name <registry_name>
    ```

3. Dockerfile을 업로드 하 고 빌드하려면 다음 명령을 사용 합니다. 을 `<registry_name>` 이미지를 저장 하려는 컨테이너 레지스트리의 이름으로 바꿉니다.

    ```azurecli-interactive
    az acr build --image myimage:v1 --registry <registry_name> --file Dockerfile .
    ```

    > [!TIP]
    > 이 예제에서는의 `:v1` 태그가 이미지에 적용 됩니다. 태그를 제공 하지 않으면의 `:latest` 태그가 적용 됩니다.

    빌드 프로세스 중에 정보는 명령줄로 돌아옵니다. 빌드가 성공적으로 수행 되 면 다음 텍스트와 유사한 메시지가 표시 됩니다.

    ```text
    Run ID: cda was successful after 2m56s
    ```

Azure Container Registry를 사용 하 여 이미지를 빌드하는 방법에 대 한 자세한 내용은 [Azure Container Registry 작업을 사용 하 여 컨테이너 이미지 빌드 및 실행](https://docs.microsoft.com/azure/container-registry/container-registry-quickstart-task-cli) 을 참조 하세요.

Azure Container Registry에 기존 이미지를 업로드 하는 방법에 대 한 자세한 내용은 [첫 번째 이미지를 개인 Docker 컨테이너 레지스트리로 밀어넣기](/azure/container-registry/container-registry-get-started-docker-cli)를 참조 하세요.

## <a name="use-a-custom-base-image"></a>사용자 지정 기본 이미지 사용

사용자 지정 이미지를 사용 하려면 다음 정보가 필요 합니다.

* __이미지 이름__입니다. 예를 들어 `mcr.microsoft.com/azureml/o16n-sample-user-base/ubuntu-miniconda` 은 Microsoft에서 제공 하는 기본 Docker 이미지에 대 한 경로입니다.

    > [!IMPORTANT]
    > 만든 사용자 지정 이미지의 경우 이미지에 사용 된 태그를 포함 해야 합니다. 예를 들어와 `:v1`같은 특정 태그를 사용 하 여 이미지를 만든 경우입니다. 이미지를 만들 때 특정 태그를 사용 하지 않은 경우의 `:latest` 태그가 적용 됩니다.

* 이미지가 __개인 리포지토리에__있는 경우 다음 정보가 필요 합니다.

    * 레지스트리 __주소__입니다. `myregistry.azureecr.io`)을 입력합니다.
    * 레지스트리에 대 한 읽기 권한이 있는 서비스 사용자 __이름__ 및 __암호__ 입니다.

    이 정보가 없는 경우 관리자에 게 이미지를 포함 하는 Azure Container Registry에 대해 문의 하십시오.

### <a name="publicly-available-base-images"></a>공개적으로 사용 가능한 기본 이미지

Microsoft는 공개적으로 액세스할 수 있는 리포지토리에 여러 docker 이미지를 제공 하며,이는이 섹션의 단계에서 사용할 수 있습니다.

| 이미지 | Description |
| ----- | ----- |
| `mcr.microsoft.com/azureml/o16n-sample-user-base/ubuntu-miniconda` | Azure Machine Learning에 대 한 기본 이미지 |
| `mcr.microsoft.com/azureml/onnxruntime:latest` | CPU 추론에 대 한 ONNX 런타임을 포함 합니다. |
| `mcr.microsoft.com/azureml/onnxruntime:latest-cuda` | GPU에 대 한 ONNX 런타임 및 UDA를 포함 합니다. |
| `mcr.microsoft.com/azureml/onnxruntime:latest-tensorrt` | ONNX Runtime 및 GPU 용 TensorRT을 포함 합니다. |
| `mcr.microsoft.com/azureml/onnxruntime:latest-openvino-vadm ` | Movidius TM myriadx VPUs을<sup> </sup> 기반으로 하는 Intel 비전<sup>TM</sup> 가속기 디자인에 대 한 Onnx 런타임 및 OpenVINO를 포함 합니다. |
| `mcr.microsoft.com/azureml/onnxruntime:latest-openvino-myriad` | Intel<sup> </sup> Movidius<sup>TM</sup> USB 스틱 용 Onnx Runtime 및 OpenVINO 포함 |

ONNX 런타임 기본 이미지에 대 한 자세한 내용은 GitHub 리포지토리의 [Onnx 런타임 dockerfile 섹션](https://github.com/microsoft/onnxruntime/blob/master/dockerfiles/README.md) 을 참조 하십시오.

> [!TIP]
> 이러한 이미지는 공개적으로 사용할 수 있으므로 사용 하는 경우 주소, 사용자 이름 또는 암호를 제공할 필요가 없습니다.

자세한 내용은 [Azure Machine Learning 컨테이너](https://github.com/Azure/AzureML-Containers)를 참조 하세요.

> [!TIP]
>__모델을 Azure Machine Learning 계산에 대해 학습 하는 경우__Azure Machine Learning SDK의 __버전 1.0.22 이상을__ 사용 하면 학습 중에 이미지가 생성 됩니다. 이 이미지의 이름을 검색 하려면를 사용 `run.properties["AzureML.DerivedImageName"]`합니다. 다음 예제에서는이 이미지를 사용 하는 방법을 보여 줍니다.
>
> ```python
> # Use an image built during training with SDK 1.0.22 or greater
> image_config.base_image = run.properties["AzureML.DerivedImageName"]
> ```

### <a name="use-an-image-with-the-azure-machine-learning-sdk"></a>Azure Machine Learning SDK를 사용 하 여 이미지 사용

**작업 영역에 대 한 Azure Container Registry**에 저장 된 이미지 또는 **공개적으로 액세스할 수 있는 컨테이너 레지스트리**를 사용 하려면 다음과 같은 [환경](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py) 특성을 설정 합니다.

+ `docker.enabled=True`
+ `docker.base_image`: 레지스트리 및 이미지 경로로 설정 합니다.

```python
from azureml.core.environment import Environment
# Create the environment
myenv = Environment(name="myenv")
# Enable Docker and reference an image
myenv.docker.enabled = True
myenv.docker.base_image = "mcr.microsoft.com/azureml/o16n-sample-user-base/ubuntu-miniconda"
```

작업 영역에 없는 __개인 컨테이너 레지스트리에서__ 이미지를 사용 하려면를 사용 `docker.base_image_registry` 하 여 리포지토리의 주소와 사용자 이름 및 암호를 지정 해야 합니다.

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

Pip 종속성으로 version >= 1.0.45를 사용 하 여 azureml 기본값을 추가 해야 합니다. 이 패키지에는 모델을 웹 서비스로 호스팅하는 데 필요한 기능이 포함되어 있습니다. 또한 환경의 inferencing_stack_version 속성을 "최신"으로 설정 해야 합니다. 이렇게 하면 웹 서비스에 필요한 특정 apt 패키지가 설치 됩니다. 

환경을 정의한 후 [InferenceConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.inferenceconfig?view=azure-ml-py) 개체와 함께 사용 하 여 모델 및 웹 서비스가 실행 될 유추 환경을 정의 합니다.

```python
from azureml.core.model import InferenceConfig
# Use environment in InferenceConfig
inference_config = InferenceConfig(entry_script="score.py",
                                   environment=myenv)
```

이 시점에서 배포를 계속할 수 있습니다. 예를 들어 다음 코드 조각은 유추 구성 및 사용자 지정 이미지를 사용 하 여 웹 서비스를 로컬로 배포 합니다.

```python
from azureml.core.webservice import LocalWebservice, Webservice

deployment_config = LocalWebservice.deploy_configuration(port=8890)
service = Model.deploy(ws, "myservice", [model], inference_config, deployment_config)
service.wait_for_deployment(show_output = True)
print(service.state)
```

배포에 대 한 자세한 내용은 [Azure Machine Learning를 사용 하 여 모델 배포](how-to-deploy-and-where.md)를 참조 하세요.

Python 환경을 사용자 지정 하는 방법에 대 한 자세한 내용은 [학습 및 배포를 위한 환경 만들기 및 관리](how-to-use-environments.md)를 참조 하세요. 

### <a name="use-an-image-with-the-machine-learning-cli"></a>Machine Learning CLI를 사용 하 여 이미지 사용

> [!IMPORTANT]
> 현재 Machine Learning CLI는 작업 영역 또는 공개적으로 액세스할 수 있는 리포지토리에 대해 Azure Container Registry 이미지를 사용할 수 있습니다. 독립 실행형 개인 레지스트리에서는 이미지를 사용할 수 없습니다.

Machine Learning CLI를 사용 하 여 모델을 배포 하기 전에 사용자 지정 이미지를 사용 하는 [환경을](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py) 만듭니다. 그런 다음 환경을 참조 하는 유추 구성 파일을 만듭니다. 유추 구성 파일에서 직접 환경을 정의할 수도 있습니다. 다음 JSON 문서는 공용 컨테이너 레지스트리에서 이미지를 참조 하는 방법을 보여 줍니다. 이 예제에서 환경은 인라인으로 정의 됩니다.

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

이 파일은 `az ml model deploy` 명령과 함께 사용 됩니다. `--ic` 매개 변수는 유추 구성 파일을 지정 하는 데 사용 됩니다.

```azurecli
az ml model deploy -n myservice -m mymodel:1 --ic inferenceconfig.json --dc deploymentconfig.json --ct akscomputetarget
```

ML CLI를 사용 하 여 모델을 배포 하는 방법에 대 한 자세한 내용은 [Azure Machine Learning에 대 한 CLI 확장](reference-azure-machine-learning-cli.md#model-registration-profiling-deployment) 의 "모델 등록, 프로 파일링 및 배포" 섹션을 참조 하세요.

## <a name="next-steps"></a>다음 단계

* [배포 위치 및 방법](how-to-deploy-and-where.md)에 대해 자세히 알아보세요.
* [Azure Pipelines를 사용 하 여 기계 학습 모델을 학습 하 고 배포](/azure/devops/pipelines/targets/azure-machine-learning?view=azure-devops)하는 방법을 알아봅니다.
