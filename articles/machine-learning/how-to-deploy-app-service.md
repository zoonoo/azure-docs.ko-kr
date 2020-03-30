---
title: Azure 앱 서비스에 ml 모델 배포(미리 보기)
titleSuffix: Azure Machine Learning
description: Azure 기계 학습을 사용하여 Azure 앱 서비스의 웹 앱에 모델을 배포하는 방법을 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: aashishb
author: aashishb
ms.reviewer: larryfr
ms.date: 08/27/2019
ms.openlocfilehash: 3e6cfde20d9f4d56af836e06b0c9a84010dea47b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80282820"
---
# <a name="deploy-a-machine-learning-model-to-azure-app-service-preview"></a>Azure 앱 서비스에 기계 학습 모델 배포(미리 보기)
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Azure 앱 서비스에서 웹 앱으로 Azure 기계 학습에서 모델을 배포하는 방법을 알아봅니다.

> [!IMPORTANT]
> Azure 기계 학습 및 Azure 앱 서비스를 모두 일반적으로 사용할 수 있지만 기계 학습 서비스에서 앱 서비스에 모델을 배포하는 기능은 미리 보기입니다.

Azure 기계 학습을 사용하면 학습된 기계 학습 모델에서 Docker 이미지를 만들 수 있습니다. 이 이미지에는 데이터를 수신하고 모델에 제출한 다음 응답을 반환하는 웹 서비스가 포함되어 있습니다. Azure App Service를 사용하여 이미지를 배포할 수 있으며 다음 기능을 제공합니다.

* 향상된 보안을 위한 고급 [인증.](/azure/app-service/configure-authentication-provider-aad) 인증 방법에는 Azure Active Directory 및 다중 요소 인증이 모두 포함됩니다.
* 재배포할 필요 없이 [자동 크기 조정.](/azure/azure-monitor/platform/autoscale-get-started?toc=%2fazure%2fapp-service%2ftoc.json)
* [TLS는](/azure/app-service/configure-ssl-certificate-in-code) 클라이언트와 서비스 간의 보안 통신을 지원합니다.

Azure 앱 서비스에서 제공하는 기능에 대한 자세한 내용은 [앱 서비스 개요를](/azure/app-service/overview)참조하십시오.

> [!IMPORTANT]
> 배포된 모델과 함께 사용된 점수 매기기 데이터 또는 점수 매기기 결과를 기록하는 기능이 필요한 경우 대신 Azure Kubernetes 서비스에 배포해야 합니다. 자세한 내용은 [프로덕션 모델의 데이터 수집을](how-to-enable-data-collection.md)참조하십시오.

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

    > [!IMPORTANT]
    > Azure 기계 학습 SDK는 웹 서비스가 데이터스토어 또는 데이터 집합에 액세스할 수 있는 방법을 제공하지 않습니다. Azure Storage 계정과 같이 배포 외부에 저장된 데이터에 액세스하기 위해 배포된 모델이 필요한 경우 관련 SDK를 사용하여 사용자 지정 코드 솔루션을 개발해야 합니다. 예를 들어 [파이썬에 대한 Azure 저장소 SDK](https://github.com/Azure/azure-storage-python).
    >
    > 시나리오에 대해 작동할 수 있는 또 다른 대안은 점수 매기기 시 데이터스토어에 대한 액세스를 제공하는 [일괄 처리 예측입니다.](how-to-use-parallel-run-step.md)

    입력 스크립트에 대한 자세한 내용은 [Azure 기계 학습을 통해 모델 배포를](how-to-deploy-and-where.md)참조하십시오.

* 도움말 스크립트 또는 항목 스크립트 또는 모델을 실행하는 데 필요한 Python/Conda 패키지와 같은 **종속성**

이러한 엔터티는 __추론 구성으로__캡슐화됩니다. 추론 구성은 항목 스크립트 및 기타 종속성을 참조합니다.

> [!IMPORTANT]
> Azure 앱 서비스와 함께 사용할 추론 구성을 만들 때 [환경](https://docs.microsoft.com//python/api/azureml-core/azureml.core.environment%28class%29?view=azure-ml-py) 개체를 사용해야 합니다. 사용자 지정 환경을 정의하는 경우 버전 >= 1.0.45를 핍 종속성으로 azureml 기본값을 추가해야 합니다. 이 패키지에는 모델을 웹 서비스로 호스팅하는 데 필요한 기능이 포함되어 있습니다. 다음 예제에서는 환경 개체를 만들고 추론 구성과 함께 사용 하 여 보여 줍니다.
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

환경에 대한 자세한 내용은 [교육 및 배포를 위한 환경 만들기 및 관리를](how-to-use-environments.md)참조하십시오.

추론 구성에 대한 자세한 내용은 [Azure 기계 학습을 통해 모델 배포를](how-to-deploy-and-where.md)참조하십시오.

> [!IMPORTANT]
> Azure 앱 서비스에 배포할 때 __배포 구성을__만들 필요가 없습니다.

## <a name="create-the-image"></a>이미지 만들기

Azure 앱 서비스에 배포되는 Docker 이미지를 만들려면 [Model.package](https://docs.microsoft.com//python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#package-workspace--models--inference-config-none--generate-dockerfile-false-)를 사용합니다. 다음 코드 조각은 모델 및 추론 구성에서 새 이미지를 빌드하는 방법을 보여 줍니다.

> [!NOTE]
> 코드 코드 조각은 등록된 `model` 모델을 포함하고 추론 `inference_config` 환경에 대한 구성을 포함하는 것으로 가정합니다. 자세한 내용은 [Azure 기계 학습을 통해 모델 배포를](how-to-deploy-and-where.md)참조하세요.

```python
from azureml.core import Model

package = Model.package(ws, [model], inference_config)
package.wait_for_creation(show_output=True)
# Display the package location/ACR path
print(package.location)
```

Docker `show_output=True`빌드 프로세스의 출력이 표시될 때 프로세스가 완료되면 작업 영역에 대한 Azure 컨테이너 레지스트리에 이미지가 만들어집니다. 이미지가 빌드되면 Azure 컨테이너 레지스트리의 위치가 표시됩니다. 반환된 위치는 형식입니다. `<acrinstance>.azurecr.io/package:<imagename>` `myml08024f78fd10.azurecr.io/package:20190827151241`)을 입력합니다.

> [!IMPORTANT]
> 이미지를 배포할 때 사용되는 위치 정보를 저장합니다.

## <a name="deploy-image-as-a-web-app"></a>이미지를 웹 앱으로 배포

1. 다음 명령을 사용하여 이미지가 포함된 Azure 컨테이너 레지스트리에 대한 로그인 자격 증명을 가져옵니다. 에서 `<acrinstance>` 이전에 반환 된 `package.location`e 값으로 바꾸기 :

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

    이 예제에서는 __기본__ 가격`--sku B1`책정 계층()이 사용됩니다.

    > [!IMPORTANT]
    > Azure 기계 학습에서 만든 이미지는 Linux를 `--is-linux` 사용하므로 매개 변수를 사용해야 합니다.

1. 웹 앱을 만들려면 다음 명령을 사용합니다. 사용할 `<app-name>` 이름으로 바꿉니다. 이전 `<acrinstance>` `<imagename>` 반환된 값으로 `package.location` 바꾸고 다음을 수행합니다.

    ```azurecli-interactive
    az webapp create --resource-group myresourcegroup --plan myplanname --name <app-name> --deployment-container-image-name <acrinstance>.azurecr.io/package:<imagename>
    ```

    이 명령은 다음 JSON 문서와 유사한 정보를 반환합니다.

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
    > 이 시점에서 웹 앱이 만들어졌습니다. 그러나 이미지가 포함된 Azure 컨테이너 레지스트리에 자격 증명을 제공하지 않았으므로 웹 앱이 활성화되지 않습니다. 다음 단계에서는 컨테이너 레지스트리에 대한 인증 정보를 제공합니다.

1. 웹 앱에 컨테이너 레지스트리에 액세스하는 데 필요한 자격 증명을 제공하려면 다음 명령을 사용합니다. 사용할 `<app-name>` 이름으로 바꿉니다. 이전 `<acrinstance>` `<imagename>` 반환된 값을 `package.location` 대체하고 로 바꿉습니다. 앞에서 `<username>` `<password>` 검색한 ACR 로그인 정보를 대체하고 로 이월합니다.

    ```azurecli-interactive
    az webapp config container set --name <app-name> --resource-group myresourcegroup --docker-custom-image-name <acrinstance>.azurecr.io/package:<imagename> --docker-registry-server-url https://<acrinstance>.azurecr.io --docker-registry-server-user <username> --docker-registry-server-password <password>
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

이 시점에서 웹 앱이 이미지 로드를 시작합니다.

> [!IMPORTANT]
> 이미지가 로드되기까지 몇 분 정도 걸릴 수 있습니다. 진행률을 모니터링하려면 다음 명령을 사용합니다.
>
> ```azurecli-interactive
> az webapp log tail --name <app-name> --resource-group myresourcegroup
> ```
>
> 이미지가 로드되고 사이트가 활성 상태인 경우 로그에 `Container <container name> for site <app-name> initialized successfully and is ready to serve requests`가시라는 메시지가 표시됩니다.

이미지가 배포되면 다음 명령을 사용하여 호스트 이름을 찾을 수 있습니다.

```azurecli-interactive
az webapp show --name <app-name> --resource-group myresourcegroup
```

이 명령은 다음 호스트 이름과 유사한 `<app-name>.azurewebsites.net`정보를 반환합니다. 이 값을 서비스의 __기본 URL의__ 일부로 사용합니다.

## <a name="use-the-web-app"></a>웹 앱 사용

모델에 요청을 전달하는 웹 서비스는 에 `{baseurl}/score`있습니다. `https://<app-name>.azurewebsites.net/score`)을 입력합니다. 다음 Python 코드는 URL에 데이터를 제출하고 응답을 표시하는 방법을 보여 줍니다.

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

* Linux 설명서의 앱 [서비스에서](/azure/app-service/containers/) 웹 앱을 구성하는 방법을 알아봅니다.
* [Azure에서 자동 크기 조정을 시작하는](/azure/azure-monitor/platform/autoscale-get-started?toc=%2fazure%2fapp-service%2ftoc.json)경우 확장에 대해 자세히 알아봅니다.
* [Azure 앱 서비스에서 TLS/SSL 인증서를 사용합니다.](/azure/app-service/configure-ssl-certificate-in-code)
* [Azure Active 디렉터리 로그인을 사용하도록 앱 서비스 앱을 구성합니다.](/azure/app-service/configure-authentication-provider-aad)
* [웹 서비스로 배포된 ML 모델 사용](how-to-consume-web-service.md)
