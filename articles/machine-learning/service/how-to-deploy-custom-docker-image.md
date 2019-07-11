---
title: 사용자 지정 Docker 이미지를 사용 하 여 모델을 배포 하는 방법
titleSuffix: Azure Machine Learning service
description: Azure Machine Learning 서비스 모델을 배포할 때 사용자 지정 Docker 이미지를 사용 하는 방법에 알아봅니다. 학습된 된 모델을 배포할 때 Docker 이미지를 호스트 이미지, 웹 서버 및 서비스를 실행 하는 데 필요한 기타 구성 요소에 만들어집니다. Azure Machine Learning 서비스는 기본 이미지를을 제공 하지만 사용자 고유의 이미지도 사용할 수 있습니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: jordane
author: jpe316
ms.reviewer: larryfr
ms.date: 06/05/2019
ms.openlocfilehash: 29fdb200075a5b5843944a7a890cc2f8ad61f1ee
ms.sourcegitcommit: 5bdd50e769a4d50ccb89e135cfd38b788ade594d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/03/2019
ms.locfileid: "67543848"
---
# <a name="deploy-a-model-using-a-custom-docker-image"></a>사용자 지정 Docker 이미지를 사용 하 여 모델 배포

Azure Machine Learning 서비스를 사용 하 여 학습 된 모델을 배포할 때 사용자 지정 Docker 이미지를 사용 하는 방법에 알아봅니다.

웹 서비스 또는 IoT Edge 장치에 학습된 된 모델을 배포할 때 Docker 이미지를 생성 됩니다. 이 이미지는 모델, conda 환경 및 모델을 사용 하는 데 필요한 자산을 포함 합니다. 또한 웹 서비스 및 Azure IoT Hub를 사용 하는 데 필요한 구성 요소를 배포 하는 경우 들어오는 요청을 처리 하도록 웹 서버를 포함 합니다.

Azure Machine Learning 서비스 만들기에 대 한 걱정 하지 않아도 되므로 기본 Docker 이미지를 제공 합니다. 으로 만든 사용자 지정 이미지를 사용할 수도 있습니다는 _기본 이미지_합니다. 기본 이미지를 배포에 대 한 이미지를 만들 때 시작 지점으로 사용 됩니다. 기본 운영 체제 및 구성 요소를 제공합니다. 그런 다음 배포 프로세스는 배포 하기 전에 이미지에 모델, conda 환경 및 기타 자산 등의 추가 구성 요소를 추가 합니다.

일반적으로 구성 요소 버전을 제어 하거나 배포 하는 동안 시간을 절약 하려는 경우 사용자 지정 이미지를 만듭니다. 예를 들어, 다음 특정 버전의 Python, Conda, 또는 다른 구성 요소를 표준화 하는 것이 좋습니다. 설치 프로세스의 시간이 오래 걸리는 있는 모델에 필요한 소프트웨어를 설치할 수도 있습니다. 소프트웨어를 설치 하 여 기본 이미지를 만들 때 각 배포에 대해 설치할 필요가 없습니다 의미 합니다.

> [!IMPORTANT]
> 모델을 배포할 때 웹 서버와 같은 핵심 구성 요소 또는 IoT Edge 구성 요소를 재정의할 수 없습니다. 이러한 구성 요소는 알려진된 작업 환경을 제공을 테스트 하 고 Microsoft에서 지원 합니다.

> [!WARNING]
> Microsoft는 사용자 지정 이미지를 야기 하는 문제를 해결할 수 있습니다. 문제가 있는 경우 기본 이미지 또는 이미지에 특정 문제가 발생 한 경우를 확인 하려면 Microsoft 제공 이미지 중 하나를 사용 하 라는 메시지가 표시 될 수 있습니다.

이 문서는 두 섹션으로 구분 됩니다.

* 사용자 지정 이미지를 만듭니다. 사용자 지정 이미지 만들기 및 Azure CLI 및 Machine Learning CLI를 사용 하 여 Azure Container Registry로 인증 구성 관리자 및 DevOps 정보를 제공 합니다.
* 사용자 지정 이미지를 사용 합니다. Python SDK 또는 ML CLI에서 학습 된 모델을 배포할 때 사용자 지정 이미지를 사용 하 여 데이터 과학자 및 DevOps/MLOps 정보를 제공 합니다.

## <a name="prerequisites"></a>필수 조건

* Azure Machine Learning 서비스는 작업 그룹. 자세한 내용은 참조는 [작업 영역 만들기](setup-create-workspace.md) 문서.
* Azure Machine Learning SDK입니다. 자세한 내용은 Python SDK 섹션을 참조 합니다 [작업 영역 만들기](setup-create-workspace.md#sdk) 문서.
* [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)
* 합니다 [Azure Machine Learning에 대 한 CLI 확장](reference-azure-machine-learning-cli.md)합니다.
* [Azure Container Registry](/azure/container-registry) 또는 인터넷에서 액세스할 수 있는 다른 Docker 레지스트리입니다.
* 이 문서의 단계를 만들고 사용 하 여 잘 알고 있다고 가정 합니다는 __유추 구성__ 모델 배포의 일부로 개체입니다. 자세한 내용은의 "배포 준비" 섹션을 참조 하세요. [배포 하는 위치와 방법을](how-to-deploy-and-where.md#prepare-to-deploy)합니다.

## <a name="create-a-custom-image"></a>사용자 지정 이미지 만들기

이 섹션의 정보는 Docker 이미지를 저장할 Azure Container Registry를 사용 한다고 가정 합니다. Azure Machine Learning 서비스에 대 한 사용자 지정 이미지를 만들려면 계획할 때는 다음 검사 목록의 사용 합니다.

* Azure Machine Learning 서비스 작업 영역 또는 독립 실행형 Azure Container Registry에 대해 만든 Azure Container Registry를 사용할 것인가?

    경우에 저장 된 이미지를 사용 하는 __작업 영역에 대 한 컨테이너 레지스트리__, 레지스트리를 인증할 필요가 없습니다. 인증은 작업 영역에서 처리 됩니다.

    > [!WARNING]
    > 작업 영역에 대 한 Azure 컨테이너 Rzegistry 됩니다 __처음으로 학습 또는 모델 배포를 만든__ 작업 영역을 사용 합니다. 새 작업 영역을 생성 하지만 하지 학습 하거나 모델을 만들고, 작업 영역에 없는 Azure Container Registry는 존재 합니다.

    작업 영역에 대 한 Azure Container Registry의 이름을 검색에 대 한 내용은 참조는 [Get 컨테이너 레지스트리 이름의](#getname) 이 문서의 섹션입니다.

    경우에 저장 된 이미지를 사용 하는 __독립 실행형 컨테이너 레지스트리__, 서비스 주체에 게 최소한 읽기 액세스를 구성 해야 합니다. 그런 다음 레지스트리에서 이미지를 사용 하는 모든 사용자에 게 서비스 주체 ID (사용자 이름) 및 암호 제공 합니다. 경우 있습니다 컨테이너 레지스트리 공개적으로 액세스할 수 있도록 하는 예외가입니다.

    개인 Azure 컨테이너 레지스트리 만들기에 대 한 내용은 참조 하세요 [개인 컨테이너 레지스트리 만들기](/azure/container-registry/container-registry-get-started-azure-cli)합니다.

    서비스 주체를 사용 하 여 Azure Container Registry를 사용 하 여 정보를 참조 하세요 [서비스 주체를 사용 하 여 Azure Container Registry 인증](/azure/container-registry/container-registry-auth-service-principal)합니다.

* Azure Container Registry 및 이미지 정보: 이미지 이름을 사용 해야 하는 모든 사용자에 게 제공 합니다. 예를 들어, 명명 된 이미지로 `myimage`라는 레지스트리에 저장 된 `myregistry`, 참조 `myregistry.azurecr.io/myimage` 모델 배포에 대 한 이미지를 사용 하는 경우

* 이미지 요구 사항: Azure Machine Learning 서비스는만 다음 소프트웨어를 제공 하는 Docker 이미지를 지원 합니다.

    * Ubuntu 16.04 이상입니다.
    * Conda 4.5. # 이상.
    * Python 3.5. # 또는 3.6. #.

<a id="getname"></a>

### <a name="get-container-registry-information"></a>컨테이너 레지스트리 정보 가져오기

이 섹션에서는 Azure Machine Learning 서비스 작업 영역에 대 한 Azure Container Registry의 이름을 가져오는 방법에 알아봅니다.

> [!WARNING]
> 작업 영역에 대 한 Azure Container Registry는 __처음으로 학습 또는 모델 배포를 만든__ 작업 영역을 사용 합니다. 새 작업 영역을 생성 하지만 하지 학습 하거나 모델을 만들고, 작업 영역에 없는 Azure Container Registry는 존재 합니다.

학습 이미 했거나 Azure Machine Learning 서비스를 사용 하 여 모델을 배포 하는 경우 작업 영역에서 container registry는 만들었습니다. 이 컨테이너 레지스트리의 이름을 찾으려면 다음 단계를 사용 합니다.

1. 새로운 셸 또는 명령 프롬프트를 열고 다음 명령을 사용 하 여 Azure 구독에 인증 합니다.

    ```azurecli-interactive
    az login
    ```

    지시에 따라 구독에 인증 합니다.

2. 다음 명령을 사용 하 여 작업 영역에 대 한 컨테이너 레지스트리를 나열 합니다. 대체 `<myworkspace>` Azure Machine Learning 서비스 작업 영역 이름입니다. 대체 `<resourcegroup>` 작업 영역을 포함 하는 Azure 리소스 그룹을 사용 하 여:

    ```azurecli-interactive
    az ml workspace show -w <myworkspace> -g <resourcegroup> --query containerRegistry
    ```

    반환되는 정보는 다음 텍스트와 유사합니다.

    ```text
    /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.ContainerRegistry/registries/<registry_name>
    ```

    `<registry_name>` 값은 작업 영역에 대 한 Azure Container Registry의 이름입니다.

### <a name="build-a-custom-image"></a>사용자 지정 이미지 빌드

Azure Container Registry에서 사용자 지정 Docker 이미지를 만드는이 섹션에서는 연습에서 설명 합니다.

1. 라는 새 텍스트 파일을 만듭니다 `Dockerfile`를 내용으로 다음 텍스트를 사용 합니다.

    ```text
    FROM ubuntu:16.04

    ENV LANG=C.UTF-8 LC_ALL=C.UTF-8
    ENV PATH /opt/miniconda/bin:$PATH

    RUN apt-get update --fix-missing && \
        apt-get install -y wget bzip2 && \
        apt-get clean && \
        rm -rf /var/lib/apt/lists/*

    RUN wget --quiet https://repo.anaconda.com/miniconda/Miniconda3-4.5.12-Linux-x86_64.sh -O ~/miniconda.sh && \
        /bin/bash ~/miniconda.sh -b -p /opt/miniconda && \
        rm ~/miniconda.sh && \
        /opt/miniconda/bin/conda clean -tipsy

    RUN conda install -y python=3.6 && \
        conda clean -aqy && \
        rm -rf /opt/miniconda/pkgs && \
        find / -type d -name __pycache__ -prune -exec rm -rf {} \;
    ```

2. 셸 또는 명령 프롬프트에서 다음 Azure Container Registry에 인증을 사용 합니다. 대체는 `<registry_name>` 에서 이미지를 저장할 컨테이너 레지스트리 이름:

    ```azurecli-interactive
    az acr login --name <registry_name>
    ```

3. Dockerfile을 업로드 하 고 빌드, 다음 명령을 사용 합니다. 대체 `<registry_name>` 에서 이미지를 저장할 컨테이너 레지스트리 이름:

    ```azurecli-interactive
    az acr build --image myimage:v1 --registry <registry_name> --file Dockerfile .
    ```

    빌드 프로세스 중 정보 명령줄에 백업 하려면 스트리밍됩니다. 빌드가 성공 하면 다음 텍스트와 유사한 메시지가 나타납니다.

    ```text
    Run ID: cda was successful after 2m56s
    ```

Azure Container Registry를 사용 하 여 이미지를 빌드에 대 한 자세한 내용은 참조 하세요. [빌드 및 Azure 컨테이너 레지스트리 작업을 사용 하 여 컨테이너 이미지 실행](https://docs.microsoft.com/azure/container-registry/container-registry-quickstart-task-cli)

기존 이미지를 Azure Container Registry에 업로드에 대 한 자세한 내용은 참조 하세요. [개인 Docker 컨테이너 레지스트리로 이미지 밀어넣기](/azure/container-registry/container-registry-get-started-docker-cli)합니다.

## <a name="use-a-custom-image"></a>사용자 지정 이미지 사용

사용자 지정 이미지를 사용 하려면 다음 정보가 필요 합니다.

* 합니다 __이미지 이름을__입니다. 예를 들어 `mcr.microsoft.com/azureml/o16n-sample-user-base/ubuntu-miniconda` Microsoft에서 제공 하는 기본 Docker 이미지의 경로입니다.
* 이미지의 경우는 __개인 리포지토리__, 다음 정보가 필요 합니다.

    * 레지스트리에 __주소__합니다. 예: `myregistry.azureecr.io`.
    * 서비스 주체 __사용자 이름__ 하 고 __암호__ 레지스트리에 대 한 읽기 액세스 권한이 있는 합니다.

    이 정보가 없으면 이미지를 포함 하는 Azure Container Registry에 대 한 관리자에 게 문의 합니다.

### <a name="publicly-available-images"></a>공개적으로 사용 가능한 이미지

Microsoft는이 섹션의 단계를 사용 하 여 사용할 수 있는 공개적으로 액세스할 수 있는 리포지토리에서 몇 가지 docker 이미지를 제공 합니다.

| 이미지 | 설명 |
| ----- | ----- |
| `mcr.microsoft.com/azureml/o16n-sample-user-base/ubuntu-miniconda` | Azure Machine Learning 서비스에 대 한 기본 이미지 |
| `mcr.microsoft.com/azureml/onnxruntime:v0.4.0` | ONNX 런타임이 포함 되어 있습니다. |
| `mcr.microsoft.com/azureml/onnxruntime:v0.4.0-cuda10.0-cudnn7` | ONNX 런타임과 CUDA 구성 요소를 포함합니다. |
| `mcr.microsoft.com/azureml/onnxruntime:v0.4.0-tensorrt19.03` | ONNX 런타임과 TensorRT 포함 되어 있습니다. |

> [!TIP]
> 이러한 이미지를 공개적으로 사용할 수 있으므로 사용 하는 경우는 주소, 사용자 이름 또는 암호를 제공할 필요가 없습니다.

> [!IMPORTANT]
> Microsoft 이미지 CUDA 또는 TensorRT 사용 하는 Microsoft Azure 서비스에만 사용 되어야 합니다.

> [!TIP]
>__Azure Machine Learning Compute에 모델을 학습 하는 경우__를 사용 하 여 __1.0.22 버전 이상__ Azure Machine Learning SDK의 이미지를 학습 하는 동안 만들어집니다. 이 이미지의 이름을 검색 하려면 사용 `run.properties["AzureML.DerivedImageName"]`합니다. 다음 예제에서는이 이미지를 사용 하는 방법을 보여 줍니다.
>
> ```python
> # Use an image built during training with SDK 1.0.22 or greater
> image_config.base_image = run.properties["AzureML.DerivedImageName"]
> ```

### <a name="use-an-image-with-the-azure-machine-learning-sdk"></a>Azure Machine Learning SDK를 사용 하 여 이미지를 사용 합니다.

설정 사용자 지정 이미지를 사용 하는 `base_image` 의 속성을 [유추 구성 개체](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.inferenceconfig?view=azure-ml-py) 이미지의 주소로:

```python
# use an image from a registry named 'myregistry'
inference_config.base_image = "myregistry.azurecr.io/myimage:v1"
```

이 형식은 공개적으로 액세스할 수 있는 작업 영역 및 컨테이너 레지스트리에 대 한 Azure Container Registry에 저장 된 이미지를 모두 사용할 수 있습니다. 예를 들어, 다음 코드는 Microsoft에서 제공 하는 기본 이미지를 사용 합니다.

```python
# use an image available in public Container Registry without authentication
inference_config.base_image = "mcr.microsoft.com/azureml/o16n-sample-user-base/ubuntu-miniconda"
```

이미지를 사용 하는 __개인 컨테이너 레지스트리에__ 작업 영역에 없는 리포지토리 및 사용자 이름 및 암호의 주소를 지정 해야 합니다.

```python
# Use an image available in a private Container Registry
inference_config.base_image = "myregistry.azurecr.io/mycustomimage:1.0"
inference_config.base_image_registry.address = "myregistry.azurecr.io"
inference_config.base_image_registry.username = "username"
inference_config.base_image_registry.password = "password"
```

### <a name="use-an-image-with-the-machine-learning-cli"></a>Machine Learning CLI를 사용 하 여 이미지를 사용 합니다.

> [!IMPORTANT]
> 현재 Machine Learning CLI 작업 영역 또는 공개적으로 액세스할 수 있는 저장소에 대해 Azure Container Registry에서 이미지를 사용할 수 있습니다. 독립 실행형 개인 레지스트리에서 이미지를 사용할 수 없습니다.

Machine Learning CLI를 사용 하 여 모델을 배포할 때 사용자 지정 이미지를 참조 하는 유추 구성 파일을 제공 합니다. 다음 JSON 문서에는 공용 컨테이너 레지스트리에서 이미지를 참조 하는 방법을 보여 줍니다.

```json
{
   "entryScript": "score.py",
   "runtime": "python",
   "condaFile": "infenv.yml",
   "extraDockerfileSteps": null,
   "sourceDirectory": null,
   "enableGpu": false,
   "baseImage": "mcr.microsoft.com/azureml/o16n-sample-user-base/ubuntu-miniconda",
   "baseImageRegistry": "mcr.microsoft.com"
}
```

이 파일을 사용 하 여 사용 된 `az ml model deploy` 명령입니다. `--ic` 매개 변수는 유추 구성 파일을 지정 하는 데 사용 됩니다.

```azurecli
az ml model deploy -n myservice -m mymodel:1 --ic inferenceconfig.json --dc deploymentconfig.json --ct akscomputetarget
```

ML CLI를 사용 하 여 모델 배포에 대 한 자세한 내용은 "모델 등록, 프로 파일링 및 배포" 섹션을 참조 합니다 [Azure Machine Learning 서비스에 대 한 CLI 확장](reference-azure-machine-learning-cli.md#model-registration-profiling-deployment) 문서.

## <a name="next-steps"></a>다음 단계

* 에 대해 자세히 알아보세요 [배포 하는 위치와 방법을](how-to-deploy-and-where.md)합니다.
* 에 대해 알아봅니다 하는 방법 [학습 하 고 Azure 파이프라인을 사용 하 여 기계 학습 모델을 배포](/azure/devops/pipelines/targets/azure-machine-learning?view=azure-devops)합니다.
