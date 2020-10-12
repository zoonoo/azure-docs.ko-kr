---
title: Azure App Service에 ml 모델 배포 (미리 보기)
titleSuffix: Azure Machine Learning
description: Azure Machine Learning를 사용 하 여 Azure App Service에서 웹 앱에 모델을 배포 하는 방법을 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: aashishb
author: aashishb
ms.reviewer: larryfr
ms.date: 06/23/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python
ms.openlocfilehash: fd7ba2eeddb3eddd98f97bda1a26c69c2cfa77c8
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91315306"
---
# <a name="deploy-a-machine-learning-model-to-azure-app-service-preview"></a>Azure App Service에 machine learning 모델 배포 (미리 보기)


Azure App Service에서 웹 앱으로 Azure Machine Learning에서 모델을 배포 하는 방법에 대해 알아봅니다.

> [!IMPORTANT]
> Azure Machine Learning와 Azure App Service를 모두 사용할 수 있지만 Machine Learning 서비스에서 App Service로 모델을 배포 하는 기능은 미리 보기 상태입니다.

Azure Machine Learning를 통해 학습 된 기계 학습 모델에서 Docker 이미지를 만들 수 있습니다. 이 이미지에는 데이터를 수신 하 고 모델에 제출한 다음 응답을 반환 하는 웹 서비스가 포함 되어 있습니다. Azure App Service는 이미지를 배포 하는 데 사용할 수 있으며 다음과 같은 기능을 제공 합니다.

* 보안 강화를 위한 고급 [인증](/azure/app-service/configure-authentication-provider-aad) 인증 방법에는 Azure Active Directory 및 multi-factor auth가 모두 포함 됩니다.
* 다시 배포할 필요 없이 [자동으로 크기를 조정](/azure/azure-monitor/platform/autoscale-get-started?toc=%2fazure%2fapp-service%2ftoc.json) 합니다.
* 클라이언트와 서비스 간의 보안 통신을 위한 [TLS 지원](/azure/app-service/configure-ssl-certificate-in-code)

Azure App Service에서 제공 하는 기능에 대 한 자세한 내용은 [App Service 개요](/azure/app-service/overview)를 참조 하세요.

> [!IMPORTANT]
> 배포 된 모델에 사용 되는 점수 매기기 데이터 또는 점수 매기기 결과를 기록 하는 기능이 필요한 경우에는 대신 Azure Kubernetes Service에 배포 해야 합니다. 자세한 내용은 [프로덕션 모델에서 데이터 수집](how-to-enable-data-collection.md)을 참조 하세요.

## <a name="prerequisites"></a>사전 요구 사항

* Azure Machine Learning 작업 영역 자세한 내용은 [작업 영역 만들기](how-to-manage-workspace.md) 문서를 참조 하세요.
* [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest&preserve-view=true)
* 작업 영역에 등록 된 학습 된 기계 학습 모델입니다. 모델이 없는 경우 [이미지 분류 자습서: 학습 모델 학습](tutorial-train-models-with-aml.md) 및 등록을 사용 합니다.

    > [!IMPORTANT]
    > 이 문서의 코드 조각은 다음 변수를 설정 했다고 가정 합니다.
    >
    > * `ws` -Azure Machine Learning 작업 영역입니다.
    > * `model` -배포 될 등록 된 모델입니다.
    > * `inference_config` -모델에 대 한 유추 구성입니다.
    >
    > 이러한 변수를 설정 하는 방법에 대 한 자세한 내용은 [Azure Machine Learning를 사용 하 여 모델 배포](how-to-deploy-and-where.md)를 참조 하세요.

## <a name="prepare-for-deployment"></a>배포 준비

을 배포 하기 전에 모델을 웹 서비스로 실행 하는 데 필요한 작업을 정의 해야 합니다. 다음 목록에서는 배포에 필요한 주요 항목에 대해 설명 합니다.

* __항목 스크립트__입니다. 이 스크립트는 요청을 수락 하 고, 모델을 사용 하 여 요청 점수를 생성 하 고, 결과를 반환 합니다.

    > [!IMPORTANT]
    > 항목 스크립트는 모델에 따라 다릅니다. 들어오는 요청 데이터의 형식, 모델에 필요한 데이터의 형식 및 클라이언트에 반환 되는 데이터 형식을 이해 해야 합니다.
    >
    > 요청 데이터가 모델에서 사용할 수 없는 형식인 경우 스크립트는이를 허용 되는 형식으로 변환할 수 있습니다. 클라이언트에 반환 하기 전에 응답을 변환할 수도 있습니다.

    > [!IMPORTANT]
    > Azure Machine Learning SDK는 웹 서비스에서 데이터 저장소 또는 데이터 집합에 액세스 하는 방법을 제공 하지 않습니다. 배포 외부에 저장 된 데이터에 액세스 하기 위해 배포 된 모델이 필요한 경우 (예: Azure Storage 계정에서) 관련 SDK를 사용 하 여 사용자 지정 코드 솔루션을 개발 해야 합니다. 예를 들어 [Python 용 AZURE STORAGE SDK](https://github.com/Azure/azure-storage-python)가 있습니다.
    >
    > 시나리오에 사용할 수 있는 또 다른 대안은 점수를 매길 때 데이터 저장소에 대 한 액세스를 제공 하는 [일괄 처리 예측](how-to-use-parallel-run-step.md)입니다.

    항목 스크립트에 대한 자세한 내용은 [Azure Machine Learning을 사용하여 모델 배포](how-to-deploy-and-where.md)를 참조하세요.

* 항목 스크립트나 모델을 실행 하는 데 필요한 도우미 스크립트 또는 Python/Conda 패키지와 같은 **종속성**

이러한 엔터티는 __유추 구성__에 캡슐화 됩니다. 추론 구성은 항목 스크립트 및 기타 종속성을 참조합니다.

> [!IMPORTANT]
> Azure App Service에 사용할 유추 구성을 만드는 경우 [환경](https://docs.microsoft.com//python/api/azureml-core/azureml.core.environment%28class%29?view=azure-ml-py&preserve-view=true) 개체를 사용 해야 합니다. 사용자 지정 환경을 정의 하는 경우 pip 종속성으로 version >= 1.0.45를 사용 하 여 azureml 기본값을 추가 해야 합니다. 이 패키지에는 모델을 웹 서비스로 호스팅하는 데 필요한 기능이 포함되어 있습니다. 다음 예제에서는 환경 개체를 만들고 유추 구성에서 사용 하는 방법을 보여 줍니다.
>
> ```python
> from azureml.core.environment import Environment
> from azureml.core.conda_dependencies import CondaDependencies
> from azureml.core.model import InferenceConfig
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

환경에 대 한 자세한 내용은 [교육 및 배포를 위한 환경 만들기 및 관리](how-to-use-environments.md)를 참조 하세요.

유추 구성에 대 한 자세한 내용은 [Azure Machine Learning를 사용 하 여 모델 배포](how-to-deploy-and-where.md)를 참조 하세요.

> [!IMPORTANT]
> Azure App Service에 배포 하는 경우 __배포 구성을__만들 필요가 없습니다.

## <a name="create-the-image"></a>이미지 만들기

Azure App Service에 배포 되는 Docker 이미지를 만들려면 [모델인 패키지](https://docs.microsoft.com//python/api/azureml-core/azureml.core.model.model?view=azure-ml-py&preserve-view=true#&preserve-view=truepackage-workspace--models--inference-config-none--generate-dockerfile-false-)를 사용 합니다. 다음 코드 조각에서는 모델 및 유추 구성에서 새 이미지를 작성 하는 방법을 보여 줍니다.

> [!NOTE]
> 이 코드 조각에서는에 `model` 등록 된 모델이 포함 되어 있고 `inference_config` 유추 환경에 대 한 구성이 포함 되어 있다고 가정 합니다. 자세한 내용은 [Azure Machine Learning를 사용 하 여 모델 배포](how-to-deploy-and-where.md)를 참조 하세요.

```python
from azureml.core import Model

package = Model.package(ws, [model], inference_config)
package.wait_for_creation(show_output=True)
# Display the package location/ACR path
print(package.location)
```

`show_output=True`인 경우 Docker 빌드 프로세스의 출력이 표시 됩니다. 프로세스가 완료 되 면 작업 영역에 대 한 Azure Container Registry에 이미지가 생성 됩니다. 이미지가 빌드되면 Azure Container Registry의 위치가 표시 됩니다. 반환 된 위치는 형식입니다 `<acrinstance>.azurecr.io/package@sha256:<imagename>` . 예들 들어 `myml08024f78fd10.azurecr.io/package@sha256:20190827151241`입니다.

> [!IMPORTANT]
> 이미지를 배포할 때 사용 되는 위치 정보를 저장 합니다.

## <a name="deploy-image-as-a-web-app"></a>이미지를 웹 앱으로 배포

1. 다음 명령을 사용 하 여 이미지를 포함 하는 Azure Container Registry에 대 한 로그인 자격 증명을 가져옵니다. `<acrinstance>`을 이전에에서 반환 된 값으로 바꿉니다 `package.location` .

    ```azurecli-interactive
    az acr credential show --name <myacr>
    ```

    이 명령의 출력은 다음 JSON 문서와 유사 합니다.

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

    __사용자 이름__ 및 __암호__중 하나에 대 한 값을 저장 합니다.

1. 서비스를 배포 하기 위한 리소스 그룹 또는 app service 계획이 아직 없는 경우 다음 명령에서 두 가지를 모두 만드는 방법을 보여 줍니다.

    ```azurecli-interactive
    az group create --name myresourcegroup --location "West Europe"
    az appservice plan create --name myplanname --resource-group myresourcegroup --sku B1 --is-linux
    ```

    이 예제에서는 __기본__ 가격 책정 계층 ( `--sku B1` )이 사용 됩니다.

    > [!IMPORTANT]
    > Azure Machine Learning에서 만든 이미지는 Linux를 사용 하므로 매개 변수를 사용 해야 합니다 `--is-linux` .

1. 웹 앱을 만들려면 다음 명령을 사용 합니다. `<app-name>`사용할 이름으로 대체 합니다. `<acrinstance>`및를 `<imagename>` 앞에서 반환 된의 값으로 바꿉니다 `package.location` .

    ```azurecli-interactive
    az webapp create --resource-group myresourcegroup --plan myplanname --name <app-name> --deployment-container-image-name <acrinstance>.azurecr.io/package@sha256:<imagename>
    ```

    이 명령은 다음 JSON 문서와 유사한 정보를 반환 합니다.

    ```json
    {
    "adminSiteName": null,
    "appServicePlanName": "myplanname",
    "geoRegion": "West Europe",
    "hostingEnvironmentProfile": null,
    "id": "/subscriptions/0000-0000/resourceGroups/myResourceGroup/providers/Microsoft.Web/serverfarms/myplanname",
    "kind": "linux",
    "location": "West Europe",
    "maximumNumberOfWorkers": 1,
    "name": "myplanname",
    < JSON data removed for brevity. >
    "targetWorkerSizeId": 0,
    "type": "Microsoft.Web/serverfarms",
    "workerTierName": null
    }
    ```

    > [!IMPORTANT]
    > 이제 웹 앱이 생성 되었습니다. 그러나 이미지를 포함 하는 Azure Container Registry에 대 한 자격 증명을 제공 하지 않았으므로 웹 앱은 활성화 되지 않습니다. 다음 단계에서는 컨테이너 레지스트리에 대 한 인증 정보를 제공 합니다.

1. 컨테이너 레지스트리에 액세스 하는 데 필요한 자격 증명을 웹 앱에 제공 하려면 다음 명령을 사용 합니다. `<app-name>`사용할 이름으로 대체 합니다. `<acrinstance>`및를 `<imagename>` 앞에서 반환 된의 값으로 바꿉니다 `package.location` . `<username>`및를 `<password>` 앞에서 검색 한 ACR 로그인 정보로 바꿉니다.

    ```azurecli-interactive
    az webapp config container set --name <app-name> --resource-group myresourcegroup --docker-custom-image-name <acrinstance>.azurecr.io/package@sha256:<imagename> --docker-registry-server-url https://<acrinstance>.azurecr.io --docker-registry-server-user <username> --docker-registry-server-password <password>
    ```

    이 명령은 다음 JSON 문서와 유사한 정보를 반환 합니다.

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
        "value": "DOCKER|myml08024f78fd10.azurecr.io/package@sha256:20190827195524"
    }
    ]
    ```

이 시점에서 웹 앱은 이미지를 로드 하기 시작 합니다.

> [!IMPORTANT]
> 이미지가 로드 되기까지 몇 분 정도 걸릴 수 있습니다. 진행률을 모니터링 하려면 다음 명령을 사용 합니다.
>
> ```azurecli-interactive
> az webapp log tail --name <app-name> --resource-group myresourcegroup
> ```
>
> 이미지가 로드 되 고 사이트가 활성화 되 면 로그에는 라는 메시지가 표시 됩니다 `Container <container name> for site <app-name> initialized successfully and is ready to serve requests` .

이미지를 배포한 후에는 다음 명령을 사용 하 여 호스트 이름을 찾을 수 있습니다.

```azurecli-interactive
az webapp show --name <app-name> --resource-group myresourcegroup
```

이 명령은 다음 호스트 이름과 유사한 정보를 반환 `<app-name>.azurewebsites.net` 합니다. 이 값은 서비스에 대 한 __기본 url__ 의 일부로 사용 합니다.

## <a name="use-the-web-app"></a>웹 앱 사용

모델에 요청을 전달 하는 웹 서비스는에 `{baseurl}/score` 있습니다. 예들 들어 `https://<app-name>.azurewebsites.net/score`입니다. 다음 Python 코드는 URL에 데이터를 전송 하 고 응답을 표시 하는 방법을 보여 줍니다.

```python
import requests
import json

scoring_uri = "https://mywebapp.azurewebsites.net/score"

headers = {'Content-Type':'application/json'}

test_sample = json.dumps({'data': [
    [1,2,3,4,5,6,7,8,9,10],
    [10,9,8,7,6,5,4,3,2,1]
]})

response = requests.post(scoring_uri, data=test_sample, headers=headers)
print(response.status_code)
print(response.elapsed)
print(response.json())
```

## <a name="next-steps"></a>다음 단계

* [Linux의 App Service](/azure/app-service/containers/) 설명서에서 웹 앱을 구성 하는 방법에 대해 알아봅니다.
* [Azure에서 자동 크기 조정 시작](/azure/azure-monitor/platform/autoscale-get-started?toc=%2fazure%2fapp-service%2ftoc.json)에서 크기 조정에 대해 자세히 알아보세요.
* [Azure App Service에서 TLS/SSL 인증서를 사용](/azure/app-service/configure-ssl-certificate-in-code)합니다.
* [Azure Active Directory 로그인을 사용 하도록 App Service 앱을 구성](/azure/app-service/configure-authentication-provider-aad)합니다.
* [웹 서비스로 배포된 ML 모델 사용](how-to-consume-web-service.md)
