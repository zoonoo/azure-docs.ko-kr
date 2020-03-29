---
title: Azure 함수 앱에 ml 모델 배포(미리 보기)
titleSuffix: Azure Machine Learning
description: Azure 기계 학습을 사용하여 Azure Functions 앱에 모델을 배포하는 방법을 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: vaidyas
author: vaidyas
ms.reviewer: larryfr
ms.date: 03/06/2020
ms.openlocfilehash: d03a3d482d147d3bc69354ee09dfe0b187610a09
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78927434"
---
# <a name="deploy-a-machine-learning-model-to-azure-functions-preview"></a>기계 학습 모델을 Azure 함수에 배포(미리 보기)
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Azure 함수에서 기능 앱으로 Azure 기계 학습에서 모델을 배포하는 방법을 알아봅니다.

> [!IMPORTANT]
> Azure 기계 학습 및 Azure 함수를 모두 일반적으로 사용할 수 있지만 함수에 대한 기계 학습 서비스에서 모델을 패키징하는 기능은 미리 보기입니다.

Azure 기계 학습을 사용하면 학습된 기계 학습 모델에서 Docker 이미지를 만들 수 있습니다. 이제 Azure 기계 학습에는 이러한 기계 학습 모델을 Azure Function에 배포할 수 있는 함수 [앱으로 빌드하는](https://docs.microsoft.com/azure/azure-functions/functions-deployment-technologies#docker-container)미리 보기 기능이 있습니다.

## <a name="prerequisites"></a>사전 요구 사항

* Azure Machine Learning 작업 영역 자세한 내용은 작업 [영역 만들기](how-to-manage-workspace.md) 문서를 참조하십시오.
* [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).
* 작업 영역에 등록된 학습된 기계 학습 모델입니다. 모델이 없는 경우 이미지 분류 [자습서: 기차 모델을](tutorial-train-models-with-aml.md) 사용하여 모델을 학습하고 등록합니다.

    > [!IMPORTANT]
    > 이 문서의 코드 조각은 다음과 같은 변수를 설정했다고 가정합니다.
    >
    > * `ws`- Azure 기계 학습 작업 영역입니다.
    > * `model`- 배포할 등록된 모델입니다.
    > * `inference_config`- 모델의 추론 구성입니다.
    >
    > 이러한 변수 설정에 대한 자세한 내용은 [Azure 기계 학습을 통해 모델 배포를](how-to-deploy-and-where.md)참조하십시오.

## <a name="prepare-for-deployment"></a>배포 준비

배포하기 전에 모델을 웹 서비스로 실행하는 데 필요한 것을 정의해야 합니다. 다음 목록에서는 배포에 필요한 기본 항목에 대해 설명합니다.

* __항목 스크립트__. 이 스크립트는 요청을 수락하고 모델을 사용하여 요청을 점수로 만들고 결과를 반환합니다.

    > [!IMPORTANT]
    > 입력 스크립트는 모델에 따라 다릅니다. 들어오는 요청 데이터의 형식, 모델에서 예상하는 데이터의 형식 및 클라이언트에 반환되는 데이터의 형식을 이해해야 합니다.
    >
    > 요청 데이터가 모델에서 사용할 수 없는 형식인 경우 스크립트는 요청 데이터를 허용 가능한 형식으로 변환할 수 있습니다. 또한 클라이언트로 돌아가기 전에 응답을 변환할 수도 있습니다.
    >
    > 기본적으로 함수에 대해 패키징할 때 입력은 텍스트로 처리됩니다. 입력의 원시 바이트(예: Blob 트리거)를 사용하려면 [AMLRequest를](https://docs.microsoft.com/azure/machine-learning/how-to-deploy-and-where#binary-data)사용하여 원시 데이터를 수락해야 합니다.


* 도움말 스크립트 또는 항목 스크립트 또는 모델을 실행하는 데 필요한 Python/Conda 패키지와 같은 **종속성**

이러한 엔터티는 __추론 구성으로__캡슐화됩니다. 추론 구성은 항목 스크립트 및 기타 종속성을 참조합니다.

> [!IMPORTANT]
> Azure Functions에 사용할 추론 구성을 만들 때 [환경](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment%28class%29?view=azure-ml-py) 개체를 사용해야 합니다. 사용자 지정 환경을 정의하는 경우 버전 >= 1.0.45를 핍 종속성으로 azureml 기본값을 추가해야 합니다. 이 패키지에는 모델을 웹 서비스로 호스팅하는 데 필요한 기능이 포함되어 있습니다. 다음 예제에서는 환경 개체를 만들고 추론 구성과 함께 사용 하 여 보여 줍니다.
>
> ```python
> from azureml.core.environment import Environment
> from azureml.core.conda_dependencies import CondaDependencies
>
> # Create an environment and add conda dependencies to it
> myenv = Environment(name="myenv")
> # Enable Docker based environment
> myenv.docker.enabled = True
> # Build conda dependencies
> myenv.python.conda_dependencies = CondaDependencies.create(conda_packages=['scikit-learn'],
>                                                            pip_packages=['azureml-defaults'])
> inference_config = InferenceConfig(entry_script="score.py", environment=myenv)
> ```

환경에 대한 자세한 내용은 [교육 및 배포를 위한 환경 만들기 및 관리를](how-to-use-environments.md)참조하십시오.

추론 구성에 대한 자세한 내용은 [Azure 기계 학습을 통해 모델 배포를](how-to-deploy-and-where.md)참조하십시오.

> [!IMPORTANT]
> 함수에 배포할 때 __배포 구성을__만들 필요가 없습니다.

## <a name="install-the-sdk-preview-package-for-functions-support"></a>기능 지원을 위한 SDK 미리 보기 패키지 설치

Azure 함수에 대한 패키지를 빌드하려면 SDK 미리 보기 패키지를 설치해야 합니다.

```bash
pip install azureml-contrib-functions
```

## <a name="create-the-image"></a>이미지 만들기

Azure Functions에 배포되는 Docker 이미지를 만들려면 [azureml.contrib.functions.package](https://docs.microsoft.com/python/api/azureml-contrib-functions/azureml.contrib.functions?view=azure-ml-py) 또는 사용하려는 트리거에 대한 특정 패키지 함수를 사용합니다. 다음 코드 조각은 모델 및 추론 구성에서 Blob 트리거를 사용하여 새 패키지를 만드는 방법을 보여 줍니다.

> [!NOTE]
> 코드 코드 조각은 등록된 `model` 모델을 포함하고 추론 `inference_config` 환경에 대한 구성을 포함하는 것으로 가정합니다. 자세한 내용은 [Azure 기계 학습을 통해 모델 배포를](how-to-deploy-and-where.md)참조하세요.

```python
from azureml.contrib.functions import package
from azureml.contrib.functions import BLOB_TRIGGER
blob = package(ws, [model], inference_config, functions_enabled=True, trigger=BLOB_TRIGGER, input_path="input/{blobname}.json", output_path="output/{blobname}_out.json")
blob.wait_for_creation(show_output=True)
# Display the package location/ACR path
print(blob.location)
```

Docker `show_output=True`빌드 프로세스의 출력이 표시될 때 프로세스가 완료되면 작업 영역에 대한 Azure 컨테이너 레지스트리에 이미지가 만들어집니다. 이미지가 빌드되면 Azure 컨테이너 레지스트리의 위치가 표시됩니다. 반환된 위치는 형식입니다. `<acrinstance>.azurecr.io/package@sha256:<hash>`

> [!NOTE]
> 함수에 대한 패키징은 현재 HTTP 트리거, Blob 트리거 및 서비스 버스 트리거를 지원합니다. 트리거에 대한 자세한 내용은 [Azure Functions 바인딩을](https://docs.microsoft.com/azure/azure-functions/functions-bindings-storage-blob-trigger#blob-name-patterns)참조하십시오.

> [!IMPORTANT]
> 이미지를 배포할 때 사용되는 위치 정보를 저장합니다.

## <a name="deploy-image-as-a-web-app"></a>이미지를 웹 앱으로 배포

1. 다음 명령을 사용하여 이미지가 포함된 Azure 컨테이너 레지스트리에 대한 로그인 자격 증명을 가져옵니다. : `<myacr>` 이전에 `package.location`반환된 값으로 바꿉꿉꿉 

    ```azurecli-interactive
    az acr credential show --name <myacr>
    ```

    이 명령의 출력은 다음 JSON 문서와 유사합니다.

    ```json
    {
    "passwords": [
        {
        "name": "password",
        "value": "Iv0lRZQ9762LUJrFiffo3P4sWgk4q+nW"
        },
        {
        "name": "password2",
        "value": "=pKCxHatX96jeoYBWZLsPR6opszr==mg"
        }
    ],
    "username": "myml08024f78fd10"
    }
    ```

    __사용자 이름과__ 암호 중 하나에 대한 값을 __저장합니다.__

1. 서비스를 배포할 리소스 그룹 또는 앱 서비스 계획이 아직 없는 경우 다음 명령을 통해 둘 다 만드는 방법을 보여 줍니다.

    ```azurecli-interactive
    az group create --name myresourcegroup --location "West Europe"
    az appservice plan create --name myplanname --resource-group myresourcegroup --sku B1 --is-linux
    ```

    이 예제에서는 _Linux 기본_ 가격`--sku B1`책정 계층()이 사용됩니다.

    > [!IMPORTANT]
    > Azure 기계 학습에서 만든 이미지는 Linux를 `--is-linux` 사용하므로 매개 변수를 사용해야 합니다.

1. 웹 작업 저장소에 사용할 저장소 계정을 만들고 연결 문자열을 가져옵니다. 사용할 `<webjobStorage>` 이름으로 바꿉니다.

    ```azurecli-interactive
    az storage account create --name <webjobStorage> --location westeurope --resource-group myresourcegroup --sku Standard_LRS
    ```
    ```azurecli-interactive
    az storage account show-connection-string --resource-group myresourcegroup --name <webJobStorage> --query connectionString --output tsv
    ```

1. 함수 앱을 만들려면 다음 명령을 사용합니다. 사용할 `<app-name>` 이름으로 바꿉니다. 이전 `<acrinstance>` `<imagename>` 반환된 값을 `package.location` 대체하고 로 바꿉습니다. 이전 `<webjobStorage>` 단계의 저장소 계정 이름으로 바꿉니다.

    ```azurecli-interactive
    az functionapp create --resource-group myresourcegroup --plan myplanname --name <app-name> --deployment-container-image-name <acrinstance>.azurecr.io/package:<imagename> --storage-account <webjobStorage>
    ```

    > [!IMPORTANT]
    > 이 시점에서 함수 앱이 만들어졌습니다. 그러나 Blob 트리거또는 자격 증명에 대한 연결 문자열을 이미지를 포함하는 Azure 컨테이너 레지스트리에 제공하지 않았으므로 함수 앱이 활성화되지 않습니다. 다음 단계에서는 컨테이너 레지스트리에 대한 연결 문자열 및 인증 정보를 제공합니다. 

1. Blob 트리거 저장소에 사용할 저장소 계정을 만들고 연결 문자열을 가져옵니다. 사용할 `<triggerStorage>` 이름으로 바꿉니다.

    ```azurecli-interactive
    az storage account create --name <triggerStorage> --location westeurope --resource-group myresourcegroup --sku Standard_LRS
    ```
    ```azurecli-interactiv
    az storage account show-connection-string --resource-group myresourcegroup --name <triggerStorage> --query connectionString --output tsv
    ```
    이 연결 문자열을 기록하여 함수 앱에 제공합니다. 나중에 요청하면 사용할 것입니다`<triggerConnectionString>`

1. 저장소 계정의 입력 및 출력에 대한 컨테이너를 만듭니다. 이전에 `<triggerConnectionString>` 반환된 연결 문자열로 바꾸기:

    ```azurecli-interactive
    az storage container create -n input --connection-string <triggerConnectionString>
    ```
    ```azurecli-interactive
    az storage container create -n output --connection-string <triggerConnectionString>
    ```

1. 트리거 연결 문자열을 함수 앱과 연결하려면 다음 명령을 사용합니다. 함수 `<app-name>` 앱의 이름으로 바꿉니다. 이전에 `<triggerConnectionString>` 반환된 연결 문자열로 바꾸기:

    ```azurecli-interactive
    az functionapp config appsettings set --name <app-name> --resource-group myresourcegroup --settings "TriggerConnectionString=<triggerConnectionString>"
    ```
1. 다음 명령을 사용하여 생성된 컨테이너와 연결된 태그를 검색해야 합니다. 컨테이너 `<username>` 레지스트리에서 이전에 반환된 사용자 이름으로 바꿉니다.

    ```azurecli-interactive
    az acr repository show-tags --repository package --name <username> --output tsv
    ```
    반환 된 값을 저장 하면 다음 `imagetag` 단계에서로 사용 됩니다.

1. 함수 앱에 컨테이너 레지스트리에 액세스하는 데 필요한 자격 증명을 제공하려면 다음 명령을 사용합니다. 함수 `<app-name>` 앱의 이름으로 바꿉니다. 이전 `<acrinstance>` `<imagetag>` 단계에서 AZ CLI 호출의 값을 바꾸고 로 바꿉습니다. 앞에서 `<username>` `<password>` 검색한 ACR 로그인 정보를 대체하고 로 이월합니다.

    ```azurecli-interactive
    az functionapp config container set --name <app-name> --resource-group myresourcegroup --docker-custom-image-name <acrinstance>.azurecr.io/package:<imagetag> --docker-registry-server-url https://<acrinstance>.azurecr.io --docker-registry-server-user <username> --docker-registry-server-password <password>
    ```

    이 명령은 다음 JSON 문서와 유사한 정보를 반환합니다.

    ```json
    [
    {
        "name": "WEBSITES_ENABLE_APP_SERVICE_STORAGE",
        "slotSetting": false,
        "value": "false"
    },
    {
        "name": "DOCKER_REGISTRY_SERVER_URL",
        "slotSetting": false,
        "value": "https://myml08024f78fd10.azurecr.io"
    },
    {
        "name": "DOCKER_REGISTRY_SERVER_USERNAME",
        "slotSetting": false,
        "value": "myml08024f78fd10"
    },
    {
        "name": "DOCKER_REGISTRY_SERVER_PASSWORD",
        "slotSetting": false,
        "value": null
    },
    {
        "name": "DOCKER_CUSTOM_IMAGE_NAME",
        "value": "DOCKER|myml08024f78fd10.azurecr.io/package:20190827195524"
    }
    ]
    ```

이 시점에서 함수 앱은 이미지 로드를 시작합니다.

> [!IMPORTANT]
> 이미지가 로드되기까지 몇 분 정도 걸릴 수 있습니다. Azure 포털을 사용하여 진행 률을 모니터링할 수 있습니다.

## <a name="test-the-deployment"></a>배포 테스트

이미지가 로드되고 앱을 사용할 수 있게 되면 다음 단계를 사용하여 앱을 트리거합니다.

1. score.py 파일이 기대하는 데이터가 포함된 텍스트 파일을 만듭니다. 다음 예제는 10개의 숫자 배열을 예상하는 score.py 함께 작동합니다.

    ```json
    {"data": [[1, 2, 3, 4, 5, 6, 7, 8, 9, 10], [10, 9, 8, 7, 6, 5, 4, 3, 2, 1]]}
    ```

    > [!IMPORTANT]
    > 데이터의 형식은 score.py 모델이 기대하는 것에 따라 달라집니다.

2. 다음 명령을 사용하여 이 파일을 이전에 만든 트리거 저장소 Blob의 입력 컨테이너에 업로드합니다. 데이터를 `<file>` 포함하는 파일의 이름으로 바꿉니다. 이전에 `<triggerConnectionString>` 반환된 연결 문자열로 바꿉습니다. 이 예제에서는 `input` 이전에 만든 입력 컨테이너의 이름입니다. 다른 이름을 사용한 경우 이 값을 바꿉니다.

    ```azurecli-interactive
    az storage blob upload --container-name input --file <file> --name <file> --connection-string <triggerConnectionString>
    ```

    이 명령의 출력은 다음 JSON과 유사합니다.

    ```json
    {
    "etag": "\"0x8D7C21528E08844\"",
    "lastModified": "2020-03-06T21:27:23+00:00"
    }
    ```

3. 함수에서 생성된 출력을 보려면 다음 명령을 사용하여 생성된 출력 파일을 나열합니다. 이전에 `<triggerConnectionString>` 반환된 연결 문자열로 바꿉습니다. 이 예제에서는 `output` 이전에 만든 출력 컨테이너의 이름입니다. 다른 이름을 사용한 경우 이 값을 바꿉니다.

    ```azurecli-interactive
    az storage blob list --container-name output --connection-string <triggerConnectionString> --query '[].name' --output tsv
    ```

    이 명령의 출력은 `sample_input_out.json`와 비슷합니다.

4. 파일을 다운로드하고 내용을 검사하려면 다음 명령을 사용합니다. 이전 `<file>` 명령에서 반환된 파일 이름으로 바꿉니다. 이전에 `<triggerConnectionString>` 반환된 연결 문자열로 바꾸기: 

    ```azurecli-interactive
    az storage blob download --container-name output --file <file> --name <file> --connection-string <triggerConnectionString>
    ```

    명령이 완료되면 파일을 엽니다. 여기에는 모델에서 반환하는 데이터가 포함됩니다.

Blob 트리거 사용에 대한 자세한 내용은 [Azure Blob 저장소에 의해 트리거된 함수 만들기](/azure/azure-functions/functions-create-storage-blob-triggered-function) 문서를 참조하세요.

## <a name="next-steps"></a>다음 단계

* [함수](/azure/azure-functions/functions-create-function-linux-custom-image) 설명서에서 함수 앱을 구성하는 방법을 알아봅니다.
* Blob 저장소 트리거 [Azure Blob 저장소 바인딩에](https://docs.microsoft.com/azure/azure-functions/functions-bindings-storage-blob)대해 자세히 알아보기.
* [모델을 Azure 앱 서비스에 배포합니다.](how-to-deploy-app-service.md)
* [웹 서비스로 배포된 ML 모델 사용](how-to-consume-web-service.md)
* [API 참조](https://docs.microsoft.com/python/api/azureml-contrib-functions/azureml.contrib.functions?view=azure-ml-py)
