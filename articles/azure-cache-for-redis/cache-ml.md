---
title: Azure Cache for Redis를 사용하여 Azure Functions에 기계 학습 모델 배포
description: 이 문서에서는 Azure Cache for Redis 인스턴스를 사용하여 Azure Machine Learning의 모델을 함수 앱으로 Azure Functions에 배포합니다. Azure Cache for Redis는 성능이 뛰어나고 확장 가능합니다. 즉, Azure Machine Learning 모델과 함께 사용할 경우 애플리케이션의 대기 시간이 짧아지고 처리량이 높아집니다.
author: curib
ms.author: cauribeg
ms.service: cache
ms.topic: conceptual
ms.date: 09/30/2020
ms.openlocfilehash: 0541b626168fb680daa2fc5c0c14df5bc8a4ea7c
ms.sourcegitcommit: f9e368733d7fca2877d9013ae73a8a63911cb88f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/10/2021
ms.locfileid: "111904178"
---
# <a name="deploy-a-machine-learning-model-to-azure-functions-with-azure-cache-for-redis"></a>Azure Cache for Redis를 사용하여 Azure Functions에 기계 학습 모델 배포

이 문서에서는 Azure Cache for Redis 인스턴스를 사용하여 Azure Machine Learning의 모델을 함수 앱으로 Azure Functions에 배포합니다.  

Azure Cache for Redis는 성능이 뛰어나고 확장 가능합니다. Azure Machine Learning 모델과 페어링하면 애플리케이션에서 짧은 대기 시간과 높은 처리량을 얻을 수 있습니다. 캐시가 유용한 몇 가지 시나리오는 데이터를 유추하는 경우와 실제 모델 유추 결과에 사용하는 경우입니다. 두 시나리오에서 메타데이터 또는 결과가 메모리 내에 저장되므로 성능이 향상됩니다.

> [!NOTE]
> Azure Machine Learning과 Azure Functions는 모두 일반 공급되지만 Functions용 Machine Learning Service의 모델을 패키징하는 기능은 미리 보기 상태입니다.  
>

## <a name="prerequisites"></a>전제 조건

* Azure 구독 - [체험 구독 만들기](https://azure.microsoft.com/free/).
* Azure Machine Learning 작업 영역 자세한 내용은 [작업 영역 만들기](../machine-learning/how-to-manage-workspace.md) 문서를 참조하세요.
* [Azure CLI](/cli/azure/install-azure-cli)
* 작업 영역에 등록된, 학습된 기계 학습 모델. 모델이 없는 경우 [이미지 분류 자습서: 모델 학습](../machine-learning/tutorial-train-models-with-aml.md)을 사용하여 모델을 학습시키고 등록합니다.

> [!IMPORTANT]
> 이 문서의 코드 조각에서는 다음 변수가 설정되었다고 가정합니다.
>
> * `ws` - Azure Machine Learning 작업 영역입니다.
> * `model` - 배포될 등록된 모델입니다.
> * `inference_config` - 모델의 유추 구성입니다.
>
> 이러한 변수를 설정하는 방법에 대한 자세한 내용은 [Azure Machine Learning을 사용하여 모델 배포](../machine-learning/how-to-deploy-and-where.md)를 참조하세요.

## <a name="create-an-azure-cache-for-redis-instance"></a>Azure Cache for Redis 인스턴스 만들기

기본, 표준 또는 프리미엄 캐시 인스턴스를 사용하여 기계 학습 모델을 Azure Functions에 배포할 수 있습니다. 캐시 인스턴스를 만들려면 다음 단계를 수행합니다.  

1. Azure Portal 홈페이지로 이동하거나 사이드바 메뉴를 연 후 **리소스 만들기** 를 선택합니다.

1. **새로 만들기** 페이지에서 **데이터베이스** 를 선택한 다음, **Azure Cache for Redis** 를 선택합니다.

    :::image type="content" source="media/cache-private-link/2-select-cache.png" alt-text="Azure Cache for Redis를 선택합니다.":::

1. **새 Redis Cache** 페이지에서 새 캐시의 설정을 구성합니다.

   | 설정      | 제안 값  | Description |
   | ------------ |  ------- | -------------------------------------------------- |
   | **DNS 이름** | 전역적으로 고유한 이름을 입력합니다. | 이 캐시 이름은 1~63자 사이의 문자열이어야 합니다. 문자열에는 숫자, 문자 또는 하이픈만 사용할 수 있습니다. 이름은 숫자 또는 문자로 시작하고 끝나야 하며 연속 하이픈을 포함할 수 없습니다. 캐시 인스턴스의 *호스트 이름* 은 *\<DNS name>.redis.cache.windows.net* 입니다. |
   | **구독** | 드롭다운하여 구독을 선택합니다. | 이 구독 아래에 새 Azure Cache for Redis 인스턴스가 만들어집니다. |
   | **리소스 그룹** | 드롭다운하여 리소스 그룹을 선택하거나, **새로 만들기** 를 선택하고 새 리소스 그룹 이름을 입력합니다. | 캐시 및 기타 리소스를 만들 새 리소스 그룹의 이름입니다. 모든 앱 리소스를 하나의 리소스 그룹에 배치하면 앱 리소스를 쉽게 관리하거나 삭제할 수 있습니다. |
   | **위치** | 드롭다운하여 위치를 선택합니다. | 캐시를 사용할 다른 서비스와 가까이 있는 [Azure 지역](https://azure.microsoft.com/regions/)을 선택합니다. |
   | **가격 책정 계층** | 드롭다운하여 [가격 책정 계층](https://azure.microsoft.com/pricing/details/cache/)을 선택합니다. |  가격 책정 계층은 캐시에 사용 가능한 크기, 성능 및 기능을 결정합니다. 자세한 내용은 [Azure Cache for Redis 개요](cache-overview.md)를 참조하세요. |

1. **네트워킹** 탭을 선택하거나 페이지 맨 아래에서 **네트워킹** 단추를 선택합니다.

1. **네트워킹** 탭에서 연결 방법을 선택합니다.

1. **다음: 고급** 탭을 선택하거나 페이지 맨 아래에서 **다음: 고급** 단추를 선택합니다.

1. 기본 또는 표준 캐시 인스턴스의 **고급** 탭에서 TLS 포트가 아닌 다른 포트를 사용하도록 설정하려면 사용 토글을 선택합니다.

1. 프리미엄 캐시 인스턴스의 **고급** 탭에서 TLS가 아닌 다른 포트, 클러스터링 및 데이터 지속성에 대한 설정을 구성합니다.

1. **다음: 태그** 탭을 선택하거나 페이지 맨 아래에서 **다음: 태그** 단추를 선택합니다.

1. 필요에 따라 리소스를 분류하려는 경우 **태그** 탭에서 이름 및 값을 입력합니다.

1. **검토 + 만들기** 를 선택합니다. 검토 + 만들기 탭으로 이동됩니다. 여기서 구성이 유효한지 검사됩니다.

1. 녹색 유효성 검사 통과 메시지가 표시되면 **만들기** 를 선택합니다.

캐시를 만드는 데 잠시 시간이 걸립니다. Azure Cache for Redis **개요** 페이지에서 진행률을 모니터링할 수 있습니다. **상태** 가 **실행 중** 으로 표시되면 캐시를 사용할 준비가 된 것입니다.

## <a name="prepare-for-deployment"></a>배포 준비

배포하기 전에 모델을 웹 서비스로 실행하는 데 필요한 항목을 정의해야 합니다. 다음 목록에서는 배포에 필요한 핵심 항목에 대해 설명합니다.

* __항목 스크립트__. 이 스크립트는 요청을 수락하고, 모델을 사용하여 요청을 채점하고, 결과를 반환합니다.

    > [!IMPORTANT]
    > 항목 스크립트는 모델에 따라 다릅니다. 들어오는 요청 데이터의 형식, 모델에 필요한 데이터의 형식 및 클라이언트에 반환되는 데이터의 형식을 이해해야 합니다.
    >
    > 요청 데이터가 모델에서 사용할 수 없는 형식인 경우 스크립트가 이를 허용되는 형식으로 변환할 수 있습니다. 응답을 클라이언트에 반환하기 전에 변환할 수도 있습니다.
    >
    > 기본적으로 함수용으로 패키징하는 경우 입력은 텍스트로 처리됩니다. 입력의 원시 바이트(예: Blob 트리거)를 사용하는 데 관심이 있는 경우에는 [AMLRequest를 사용하여 원시 데이터를 수락](../machine-learning/how-to-deploy-advanced-entry-script.md#binary-data)해야 합니다.

실행 함수의 경우 Redis 엔드포인트에 연결하는지 확인합니다.

```python
import json
import numpy as np
import os
import redis
from sklearn.externals import joblib

def init():
    global model
    global azrediscache
    azrediscache = redis.StrictRedis(host='<host_url>', port=6380, password="<access_key>", ssl=True)
    model_path = os.path.join(os.getenv('AZUREML_MODEL_DIR'), 'sklearn_mnist_model.pkl')
    model = joblib.load(model_path)

@input_schema('data', NumpyParameterType(input_sample))
@output_schema(NumpyParameterType(output_sample))
def run(data):
    try:
        input = azrediscache.get(data)
        result = model.predict(input)
        data = np.array(json.loads(data))
        result = model.predict(data)
        # You can return any data type, as long as it is JSON serializable.
        return result.tolist()
    except Exception as e:
        error = str(e)
        return error
```

항목 스크립트에 대한 자세한 내용은 [채점 코드 정의](../machine-learning/how-to-deploy-and-where.md?tabs=python#define-an-entry-script)를 참조하세요.

* 항목 스크립트나 모델을 실행하는 데 필요한 도우미 스크립트 또는 Python/Conda 패키지 같은 **종속성**

이러한 엔터티는 __유추 구성__ 에 캡슐화됩니다. 추론 구성은 항목 스크립트 및 기타 종속성을 참조합니다.

> [!IMPORTANT]
> Azure Functions에 사용할 유추 구성을 만들 때는 [환경](/python/api/azureml-core/azureml.core.environment%28class%29) 개체를 사용해야 합니다. 사용자 지정 환경을 정의하는 경우 pip 종속성으로 버전이 1.0.45 이상인 azureml-defaults를 추가해야 합니다. 이 패키지에는 모델을 웹 서비스로 호스팅하는 데 필요한 기능이 포함되어 있습니다. 다음 예제에서는 환경 개체를 만들고 유추 구성에서 사용하는 방법을 보여 줍니다.
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
>                                                            pip_packages=['azureml-defaults', 'redis'])
> inference_config = InferenceConfig(entry_script="score.py", environment=myenv)
> ```

환경에 대한 자세한 내용은 [학습 및 배포 환경 만들기 및 관리](../machine-learning/how-to-use-environments.md)를 참조하세요.

유추 구성에 대한 자세한 내용은 [Azure Machine Learning을 사용하여 모델 배포](../machine-learning/how-to-deploy-and-where.md?tabs=python#define-an-inference-configuration)를 참조하세요.

> [!IMPORTANT]
> Functions에 배포할 때는 __배포 구성__ 을 만들 필요가 없습니다.

## <a name="install-the-sdk-preview-package-for-functions-support"></a>Functions 지원을 위한 SDK 미리 보기 패키지 설치

Azure Functions용 패키지를 빌드하려면 SDK 미리 보기 패키지를 설치해야 합니다.

```bash
pip install azureml-contrib-functions
```

## <a name="create-the-image"></a>이미지 만들기

Azure Functions에 배포되는 Docker 이미지를 만들려면 사용하려는 트리거에 [azureml.contrib.functions.package](/python/api/azureml-contrib-functions/azureml.contrib.functions) 또는 특정 패키지 함수를 사용합니다. 다음 코드 조각에서는 모델 및 유추 구성에서 HTTP 트리거를 사용하여 새 패키지를 만드는 방법을 보여 줍니다.

> [!NOTE]
> 이 코드 조각에서는 `model`에 등록된 모델이 포함되어 있고, `inference_config`에 유추 환경에 대한 구성이 포함되어 있다고 가정합니다. 자세한 내용은 [Azure Machine Learning을 사용하여 모델 배포](../machine-learning/how-to-deploy-and-where.md)를 참조하세요.

```python
from azureml.contrib.functions import package
from azureml.contrib.functions import HTTP_TRIGGER
model_package = package(ws, [model], inference_config, functions_enabled=True, trigger=HTTP_TRIGGER)
model_package.wait_for_creation(show_output=True)
# Display the package location/ACR path
print(model_package.location)
```

`show_output=True`인 경우 Docker 빌드 프로세스의 출력이 표시됩니다. 프로세스가 완료되면 Azure Container Registry에 작업 영역에 대한 이미지가 생성됩니다. 이미지가 빌드되면 Azure Container Registry상의 위치가 표시됩니다. 반환되는 위치는 `<acrinstance>.azurecr.io/package@sha256:<imagename>` 형식입니다.

> [!NOTE]
> Functions에 대한 패키징은 현재 HTTP 트리거, Blob 트리거 및 Service Bus 트리거를 지원합니다. 트리거에 대한 자세한 내용은 [Azure Functions 바인딩](../azure-functions/functions-bindings-storage-blob-trigger.md#blob-name-patterns)을 참조하세요.

> [!IMPORTANT]
> 이미지를 배포할 때 사용되는 위치 정보를 저장합니다.

## <a name="deploy-image-as-a-web-app"></a>이미지를 웹앱으로 배포

1. 다음 명령을 사용하여 이미지를 포함하는 Azure Container Registry에 대한 로그인 자격 증명을 가져옵니다. `<myacr>`을 `package.location`에서 이전에 반환된 값으로 바꿉니다.

    ```azurecli-interactive
    az acr credential show --name <myacr>
    ```

    이 명령의 출력은 다음 JSON 문서와 비슷합니다.

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

    __사용자 이름__ 및 __암호__ 중 하나의 값을 저장합니다.

1. 서비스를 배포하기 위한 리소스 그룹 또는 App Service 요금제가 아직 없는 경우, 다음 명령에서 두 가지 모두를 만드는 방법을 보여줍니다.

    ```azurecli-interactive
    az group create --name myresourcegroup --location "West Europe"
    az appservice plan create --name myplanname --resource-group myresourcegroup --sku B1 --is-linux
    ```

    이 예제에서는 _Linux 기본_ 가격 책정 계층(`--sku B1`)이 사용됩니다.

    > [!IMPORTANT]
    > Azure Machine Learning에서 만든 이미지는 Linux를 사용하므로 `--is-linux` 매개 변수를 사용해야 합니다.

1. 웹 작업 스토리지에 사용할 스토리지 계정을 만들고 연결 문자열을 가져옵니다. `<webjobStorage>`를 원하는 이름으로 바꿉니다.

    ```azurecli-interactive
    az storage account create --name <webjobStorage> --location westeurope --resource-group myresourcegroup --sku Standard_LRS
    ```

    ```azurecli-interactive
    az storage account show-connection-string --resource-group myresourcegroup --name <webJobStorage> --query connectionString --output tsv
    ```

1. 함수 앱을 만들려면 다음 명령을 사용합니다. `<app-name>`을 원하는 이름으로 바꿉니다. `<acrinstance>` 및 `<imagename>`을 이전에 반환된 `package.location`의 값으로 바꿉니다. `<webjobStorage>`를 이전 단계의 스토리지 계정 이름으로 바꿉니다.

    ```azurecli-interactive
    az functionapp create --resource-group myresourcegroup --plan myplanname --name <app-name> --deployment-container-image-name <acrinstance>.azurecr.io/package:<imagename> --storage-account <webjobStorage>
    ```

    > [!IMPORTANT]
    > 이때 함수 앱이 생성됩니다. 그러나 이미지를 포함하는 Azure Container Registry에 HTTP 트리거 또는 자격 증명에 대한 연결 문자열을 제공하지 않았으므로 함수 앱이 활성화되지 않습니다. 다음 단계에서는 컨테이너 레지스트리에 대한 연결 문자열과 인증 정보를 제공합니다.

1. 컨테이너 레지스트리에 액세스하는 데 필요한 자격 증명을 함수 앱에 제공하려면 다음 명령을 사용합니다. `<app-name>`을 함수 앱의 이름으로 대체합니다. `<acrinstance>` 및 `<imagetag>`를 이전 단계에서 AZ CLI 호출로 얻은 값으로 바꿉니다. `<username>` 및 `<password>`를 이전에 검색한 ACR 로그인 정보로 바꿉니다.

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

이때 함수 앱은 이미지를 로드하기 시작합니다.

> [!IMPORTANT]
> 이미지가 로드되기까지 몇 분 정도 걸릴 수 있습니다. Azure Portal을 사용하여 진행률을 모니터링할 수 있습니다.

## <a name="test-azure-functions-http-trigger"></a>Azure Functions HTTP 트리거 테스트

이제 Azure Functions HTTP 트리거를 실행하고 테스트합니다.

1. Azure Portal에서 함수 앱으로 이동합니다.
1. 개발자에서 **코드 + 테스트** 를 선택합니다.
1. 오른쪽에서 **입력** 탭을 선택합니다.
1. **실행** 단추를 선택하여 Azure Functions HTTP 트리거를 테스트합니다.

이제 Azure Cache for Redis 인스턴스를 사용하여 Azure Machine Learning의 모델을 함수 앱으로 성공적으로 배포했습니다. 아래 섹션의 링크로 이동하여 Azure Cache for Redis에 대해 자세히 알아보세요.

## <a name="clean-up-resources"></a>리소스 정리

다음 자습서를 계속 진행하는 경우 이 빠른 시작에서 만든 리소스를 그대로 두었다가 다시 사용할 수 있습니다.

또는 빠른 시작을 완료한 경우 이 빠른 시작에서 만든 Azure 리소스를 삭제하여 요금이 청구되는 것을 방지할 수 있습니다.

> [!IMPORTANT]
> 리소스 그룹을 삭제하면 다시 되돌릴 수 없습니다. 리소스 그룹을 삭제하는 경우 그 안의 모든 리소스가 영구적으로 삭제됩니다. 잘못된 리소스 그룹 또는 리소스를 자동으로 삭제하지 않도록 해야 합니다. 유지하려는 리소스가 포함된 기존 리소스 그룹 내에 이 샘플을 호스트하기 위한 리소스를 만든 경우 리소스 그룹을 삭제하는 대신, 해당 블레이드에서 각 리소스를 개별적으로 삭제할 수 있습니다.

### <a name="to-delete-a-resource-group"></a>리소스 그룹을 삭제하려면

1. [Azure Portal](https://portal.azure.com)에 로그인한 다음, **리소스 그룹** 을 선택합니다.

2. **이름을 기준으로 필터링...** 상자에 리소스 그룹의 이름을 입력합니다. 결과 목록의 리소스 그룹에서 **...** 를 선택한 다음, **리소스 그룹 삭제** 를 선택합니다.

리소스 그룹 삭제를 확인하는 메시지가 표시됩니다. 리소스 그룹의 이름을 입력하여 확인한 다음, **삭제** 를 선택합니다.

잠시 후, 리소스 그룹 및 모든 해당 리소스가 삭제됩니다.

## <a name="next-steps"></a>다음 단계

* [Azure Cache for Redis](./cache-overview.md)에 대해 자세히 알아보기
* [Functions](../azure-functions/functions-create-function-linux-custom-image.md) 설명서에서 함수 앱을 구성하는 방법에 대해 알아보기
* [API 참조](/python/api/azureml-contrib-functions/azureml.contrib.functions)
* [Azure Cache for Redis를 사용하는 Python 앱](./cache-python-get-started.md) 만들기
