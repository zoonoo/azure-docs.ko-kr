---
title: Redis 용 Azure Cache를 사용 하 여 Azure Functions에 machine learning 모델 배포
description: 이 문서에서는 Redis 용 Azure Cache 인스턴스를 사용 하 여 Azure Functions에서 함수 앱으로 Azure Machine Learning의 모델을 배포 합니다. Redis 용 Azure Cache는 매우 성능이 뛰어나고 확장 가능 합니다. 즉, Azure Machine Learning 모델과 함께 사용할 경우 응용 프로그램에서 대기 시간이 짧고 처리량이 높습니다.
author: curib
ms.author: cauribeg
ms.service: cache
ms.topic: conceptual
ms.date: 09/30/2020
ms.openlocfilehash: d9731455edf0afbe4c0768ae40a51316ac71ad94
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/26/2020
ms.locfileid: "92537578"
---
# <a name="deploy-a-machine-learning-model-to-azure-functions-with-azure-cache-for-redis"></a>Redis 용 Azure Cache를 사용 하 여 Azure Functions에 machine learning 모델 배포 

이 문서에서는 Redis 용 Azure Cache 인스턴스를 사용 하 여 Azure Functions에서 함수 앱으로 Azure Machine Learning의 모델을 배포 합니다.  

Redis 용 Azure Cache는 매우 성능이 뛰어나고 확장 가능 합니다. 즉, Azure Machine Learning 모델과 함께 사용할 경우 응용 프로그램에서 대기 시간이 짧고 처리량이 높습니다. 캐시가 특히 유용한 몇 가지 시나리오는 데이터를 추론 하 고 실제 모델을 유추 하는 경우입니다. 두 시나리오에서 메타 데이터 나 결과는 메모리에 저장 되므로 성능이 향상 됩니다. 

> [!NOTE]
> Azure Machine Learning와 Azure Functions를 모두 사용할 수 있지만 함수에 대 한 Machine Learning 서비스에서 모델을 패키징하는 기능은 미리 보기 상태입니다.  
>

## <a name="prerequisites"></a>사전 요구 사항
* Azure 구독 - [체험 구독 만들기](https://azure.microsoft.com/free/).
* Azure Machine Learning 작업 영역 자세한 내용은 [작업 영역 만들기](../machine-learning/how-to-manage-workspace.md) 문서를 참조 하세요.
* [Azure CLI](/cli/azure/install-azure-cli?preserve-view=true&view=azure-cli-latest)
* 작업 영역에 등록 된 학습 된 기계 학습 모델입니다. 모델이 없는 경우 [이미지 분류 자습서: 학습 모델 학습](../machine-learning/tutorial-train-models-with-aml.md) 및 등록을 사용 합니다.

> [!IMPORTANT]
> 이 문서의 코드 조각은 다음 변수를 설정 했다고 가정 합니다.
>
> * `ws` -Azure Machine Learning 작업 영역입니다.
> * `model` -배포 될 등록 된 모델입니다.
> * `inference_config` -모델에 대 한 유추 구성입니다.
>
> 이러한 변수를 설정 하는 방법에 대 한 자세한 내용은 [Azure Machine Learning를 사용 하 여 모델 배포](../machine-learning/how-to-deploy-and-where.md)를 참조 하세요.

## <a name="create-an-azure-cache-for-redis-instance"></a>Azure Cache for Redis 인스턴스 만들기 
기본, 표준 또는 프리미엄 캐시 인스턴스와 Azure Functions 하기 위해 machine learning 모델을 배포할 수 있습니다. 캐시 인스턴스를 만들려면 다음 단계를 수행 합니다.  

1. Azure Portal 홈페이지로 이동 하거나 사이드바 메뉴를 열고 **리소스 만들기** 를 선택 합니다. 
   
1. **새로 만들기** 페이지에서 **데이터베이스** 를 선택한 다음, **Azure Cache for Redis** 를 선택합니다.

    :::image type="content" source="media/cache-private-link/2-select-cache.png" alt-text="Azure Cache for Redis를 선택합니다.":::
   
1. **새 Redis Cache** 페이지에서 새 캐시의 설정을 구성합니다.
   
   | 설정      | 제안 값  | Description |
   | ------------ |  ------- | -------------------------------------------------- |
   | **DNS 이름** | 전역적으로 고유한 이름을 입력합니다. | 캐시 이름은 1~63자의 문자열이어야 하며 숫자, 문자 및 하이픈만 포함할 수 있습니다. 이름은 숫자 또는 문자로 시작하고 끝나야 하며 연속 하이픈을 포함할 수 없습니다. 캐시 인스턴스의 *호스트 이름* 은 *\<DNS name>.redis.cache.windows.net* 입니다. | 
   | **구독** | 드롭다운하여 구독을 선택합니다. | 이 구독 아래에 새 Azure Cache for Redis 인스턴스가 만들어집니다. | 
   | **리소스 그룹** | 드롭다운하여 리소스 그룹을 선택하거나, **새로 만들기** 를 선택하고 새 리소스 그룹 이름을 입력합니다. | 캐시 및 기타 리소스를 만들 새 리소스 그룹의 이름입니다. 모든 앱 리소스를 하나의 리소스 그룹에 배치하면 앱 리소스를 쉽게 관리하거나 삭제할 수 있습니다. | 
   | **위치** | 드롭다운하여 위치를 선택합니다. | 캐시를 사용할 다른 서비스와 가까이 있는 [Azure 지역](https://azure.microsoft.com/regions/)을 선택합니다. |
   | **가격 책정 계층** | 드롭다운하여 [가격 책정 계층](https://azure.microsoft.com/pricing/details/cache/)을 선택합니다. |  가격 책정 계층은 캐시에 사용 가능한 크기, 성능 및 기능을 결정합니다. 자세한 내용은 [Azure Cache for Redis 개요](cache-overview.md)를 참조하세요. |

1. **네트워킹** 탭을 선택하거나 페이지 하단에 있는 **네트워킹** 단추를 클릭합니다.

1. **네트워킹** 탭에서 연결 방법을 선택합니다.

1. 페이지 맨 아래에서 **다음: 고급** 탭을 선택하거나 페이지 하단에서 **다음: 고급** 단추를 클릭합니다.

1. 기본 또는 표준 캐시 인스턴스의 **고급** 탭에서 TLS 포트가 아닌 다른 포트를 사용하도록 설정하려면 사용 토글을 선택합니다.

1. 프리미엄 캐시 인스턴스의 **고급** 탭에서 TLS가 아닌 다른 포트, 클러스터링 및 데이터 지속성에 대한 설정을 구성합니다.

1. 페이지 맨 아래에서 **다음: 태그** 탭을 선택하거나 페이지 하단에서 **다음: 태그** 단추를 클릭합니다.

1. 필요에 따라 리소스를 분류하려는 경우 **태그** 탭에서 이름 및 값을 입력합니다. 

1. **검토 + 만들기** 를 선택합니다. 검토 + 만들기 탭으로 이동됩니다. 여기서 구성이 유효한지 검사됩니다.

1. 녹색 유효성 검사 통과 메시지가 표시되면 **만들기** 를 선택합니다.

캐시를 만드는 데 잠시 시간이 걸립니다. Azure Cache for Redis **개요** 페이지에서 진행률을 모니터링할 수 있습니다. **상태** 가 **실행 중** 으로 표시되면 캐시를 사용할 준비가 된 것입니다. 

## <a name="prepare-for-deployment"></a>배포 준비

을 배포 하기 전에 모델을 웹 서비스로 실행 하는 데 필요한 작업을 정의 해야 합니다. 다음 목록에서는 배포에 필요한 핵심 항목에 대해 설명 합니다.

* __항목 스크립트__ 입니다. 이 스크립트는 요청을 수락 하 고, 모델을 사용 하 여 요청 점수를 생성 하 고, 결과를 반환 합니다.

    > [!IMPORTANT]
    > 항목 스크립트는 모델에 따라 다릅니다. 들어오는 요청 데이터의 형식, 모델에 필요한 데이터의 형식 및 클라이언트에 반환 되는 데이터 형식을 이해 해야 합니다.
    >
    > 요청 데이터가 모델에서 사용할 수 없는 형식인 경우 스크립트는이를 허용 되는 형식으로 변환할 수 있습니다. 응답을 클라이언트에 반환 하기 전에 변형할 수도 있습니다.
    >
    > 기본적으로 함수를 패키징하는 경우 입력은 텍스트로 처리 됩니다. Blob 트리거의 경우와 같이 입력의 원시 바이트를 사용 하는 데 관심이 있는 경우에는 [Amlrequest를 사용 하 여 원시 데이터를 수락](../machine-learning/how-to-deploy-advanced-entry-script.md#binary-data)해야 합니다.

Run 함수에서 Redis 끝점에 연결 하는지 확인 합니다.

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

항목 스크립트에 대 한 자세한 내용은 [점수 매기기 코드 정의](../machine-learning/how-to-deploy-and-where.md?tabs=python#define-an-entry-script) 를 참조 하세요.

* 항목 스크립트나 모델을 실행 하는 데 필요한 도우미 스크립트 또는 Python/Conda 패키지와 같은 **종속성**

이러한 엔터티는 __유추 구성__ 에 캡슐화 됩니다. 추론 구성은 항목 스크립트 및 기타 종속성을 참조합니다.

> [!IMPORTANT]
> Azure Functions에 사용할 유추 구성을 만드는 경우 [환경](/python/api/azureml-core/azureml.core.environment%28class%29?preserve-view=true&view=azure-ml-py) 개체를 사용 해야 합니다. 사용자 지정 환경을 정의 하는 경우 pip 종속성으로 version >= 1.0.45를 사용 하 여 azureml 기본값을 추가 해야 합니다. 이 패키지에는 모델을 웹 서비스로 호스팅하는 데 필요한 기능이 포함되어 있습니다. 다음 예제에서는 환경 개체를 만들고 유추 구성에서 사용 하는 방법을 보여 줍니다.
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

환경에 대 한 자세한 내용은 [교육 및 배포를 위한 환경 만들기 및 관리](../machine-learning/how-to-use-environments.md)를 참조 하세요.

유추 구성에 대 한 자세한 내용은 [Azure Machine Learning를 사용 하 여 모델 배포](../machine-learning/how-to-deploy-and-where.md?tabs=python#define-an-inference-configuration)를 참조 하세요.

> [!IMPORTANT]
> 함수에 배포 하는 경우 __배포 구성을__ 만들 필요가 없습니다.

## <a name="install-the-sdk-preview-package-for-functions-support"></a>함수 지원을 위한 SDK 미리 보기 패키지 설치

Azure Functions에 대 한 패키지를 빌드하려면 SDK 미리 보기 패키지를 설치 해야 합니다.

```bash
pip install azureml-contrib-functions
```

## <a name="create-the-image"></a>이미지 만들기

Azure Functions에 배포 되는 Docker 이미지를 만들려면 사용 하려는 트리거에 대해 [azureml](/python/api/azureml-contrib-functions/azureml.contrib.functions?preserve-view=true&view=azure-ml-py) 또는 특정 패키지 함수를 사용 합니다. 다음 코드 조각에서는 모델 및 유추 구성에서 HTTP 트리거를 사용 하 여 새 패키지를 만드는 방법을 보여 줍니다.

> [!NOTE]
> 이 코드 조각에서는에 `model` 등록 된 모델이 포함 되어 있고 `inference_config` 유추 환경에 대 한 구성이 포함 되어 있다고 가정 합니다. 자세한 내용은 [Azure Machine Learning를 사용 하 여 모델 배포](../machine-learning/how-to-deploy-and-where.md)를 참조 하세요.

```python
from azureml.contrib.functions import package
from azureml.contrib.functions import HTTP_TRIGGER
model_package = package(ws, [model], inference_config, functions_enabled=True, trigger=HTTP_TRIGGER)
model_package.wait_for_creation(show_output=True)
# Display the package location/ACR path
print(model_package.location)
```

`show_output=True`인 경우 Docker 빌드 프로세스의 출력이 표시 됩니다. 프로세스가 완료 되 면 작업 영역에 대 한 Azure Container Registry에 이미지가 생성 됩니다. 이미지가 빌드되면 Azure Container Registry의 위치가 표시 됩니다. 반환 된 위치는 형식입니다 `<acrinstance>.azurecr.io/package@sha256:<imagename>` .

> [!NOTE]
> 함수에 대 한 패키징은 현재 HTTP 트리거, Blob 트리거 및 Service bus 트리거를 지원 합니다. 트리거에 대 한 자세한 내용은 [Azure Functions 바인딩](../azure-functions/functions-bindings-storage-blob-trigger.md#blob-name-patterns)을 참조 하세요.

> [!IMPORTANT]
> 이미지를 배포할 때 사용 되는 위치 정보를 저장 합니다.

## <a name="deploy-image-as-a-web-app"></a>이미지를 웹 앱으로 배포

1. 다음 명령을 사용 하 여 이미지를 포함 하는 Azure Container Registry에 대 한 로그인 자격 증명을 가져옵니다. `<myacr>`을 이전에에서 반환 된 값으로 바꿉니다 `package.location` . 

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

    __사용자 이름__ 및 __암호__ 중 하나에 대 한 값을 저장 합니다.

1. 서비스를 배포 하기 위한 리소스 그룹 또는 app service 계획이 아직 없는 경우 다음 명령에서 두 가지를 모두 만드는 방법을 보여 줍니다.

    ```azurecli-interactive
    az group create --name myresourcegroup --location "West Europe"
    az appservice plan create --name myplanname --resource-group myresourcegroup --sku B1 --is-linux
    ```

    이 예제에서는 _Linux 기본_ 가격 책정 계층 ( `--sku B1` )이 사용 됩니다.

    > [!IMPORTANT]
    > Azure Machine Learning에서 만든 이미지는 Linux를 사용 하므로 매개 변수를 사용 해야 합니다 `--is-linux` .

1. 웹 작업 저장소에 사용할 저장소 계정을 만들고 연결 문자열을 가져옵니다. `<webjobStorage>`사용할 이름으로 대체 합니다.

    ```azurecli-interactive
    az storage account create --name <webjobStorage> --location westeurope --resource-group myresourcegroup --sku Standard_LRS
    ```
    ```azurecli-interactive
    az storage account show-connection-string --resource-group myresourcegroup --name <webJobStorage> --query connectionString --output tsv
    ```

1. 함수 앱을 만들려면 다음 명령을 사용 합니다. `<app-name>`사용할 이름으로 대체 합니다. `<acrinstance>`및를 `<imagename>` 앞에서 반환 된의 값으로 바꿉니다 `package.location` . `<webjobStorage>`을 이전 단계의 저장소 계정 이름으로 바꿉니다.

    ```azurecli-interactive
    az functionapp create --resource-group myresourcegroup --plan myplanname --name <app-name> --deployment-container-image-name <acrinstance>.azurecr.io/package:<imagename> --storage-account <webjobStorage>
    ```

    > [!IMPORTANT]
    > 이제 함수 앱이 생성 되었습니다. 그러나 이미지를 포함 하는 Azure Container Registry에 대 한 HTTP 트리거 또는 자격 증명에 대 한 연결 문자열을 제공 하지 않았으므로 함수 앱은 활성화 되지 않습니다. 다음 단계에서는 컨테이너 레지스트리에 대 한 연결 문자열과 인증 정보를 제공 합니다. 

1. 컨테이너 레지스트리에 액세스 하는 데 필요한 자격 증명을 함수 앱에 제공 하려면 다음 명령을 사용 합니다. `<app-name>`함수 앱의 이름으로 대체 합니다. `<acrinstance>`및를 `<imagetag>` 이전 단계에서 AZ CLI 호출의 값으로 바꿉니다. `<username>`및를 `<password>` 앞에서 검색 한 ACR 로그인 정보로 바꿉니다.

    ```azurecli-interactive
    az functionapp config container set --name <app-name> --resource-group myresourcegroup --docker-custom-image-name <acrinstance>.azurecr.io/package:<imagetag> --docker-registry-server-url https://<acrinstance>.azurecr.io --docker-registry-server-user <username> --docker-registry-server-password <password>
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
        "value": "DOCKER|myml08024f78fd10.azurecr.io/package:20190827195524"
    }
    ]
    ```

이 시점에서 함수 앱은 이미지를 로드 하기 시작 합니다.

> [!IMPORTANT]
> 이미지가 로드 되기까지 몇 분 정도 걸릴 수 있습니다. Azure Portal를 사용 하 여 진행률을 모니터링할 수 있습니다.

## <a name="test-azure-function-http-trigger"></a>Azure Function HTTP 트리거 테스트 

이제 Azure Function HTTP 트리거를 실행 하 고 테스트 합니다.

1. Azure Portal에서 Azure 함수 앱으로 이동 합니다.
1. 개발자에서 **코드 + 테스트** 를 선택 합니다. 
1. 오른쪽에서 **입력** 탭을 선택 합니다. 
1. **실행** 단추를 클릭 하 여 AZURE Function HTTP 트리거를 테스트 합니다. 

이제 Redis 용 Azure Cache 인스턴스를 사용 하 여 Azure Machine Learning에서 함수 앱으로 모델을 성공적으로 배포 했습니다. 아래 섹션의 링크로 이동 하 여 Azure Cache for Redis에 대해 자세히 알아보세요.

## <a name="clean-up-resources"></a>리소스 정리

다음 자습서를 계속 진행하는 경우 이 빠른 시작에서 만든 리소스를 그대로 두었다가 다시 사용할 수 있습니다.

그렇지 않으면 빠른 시작을 완료 하는 경우 요금을 방지 하기 위해이 빠른 시작에서 만든 Azure 리소스를 삭제할 수 있습니다. 

> [!IMPORTANT]
> 리소스 그룹을 삭제하면 다시 되돌릴 수 없습니다. 리소스 그룹을 삭제하는 경우 그 안의 모든 리소스가 영구적으로 삭제됩니다. 잘못된 리소스 그룹 또는 리소스를 자동으로 삭제하지 않도록 해야 합니다. 유지하려는 리소스가 포함된 기존 리소스 그룹 내에 이 샘플을 호스트하기 위한 리소스를 만든 경우 리소스 그룹을 삭제하는 대신, 해당 블레이드에서 각 리소스를 개별적으로 삭제할 수 있습니다.

### <a name="to-delete-a-resource-group"></a>리소스 그룹을 삭제하려면

1. [Azure Portal](https://portal.azure.com)에 로그인한 다음, **리소스 그룹** 을 선택합니다.

2. **이름을 기준으로 필터링...** 상자에 리소스 그룹의 이름을 입력합니다. 결과 목록의 리소스 그룹에서 **...** 를 선택한 다음, **리소스 그룹 삭제** 를 선택합니다.

리소스 그룹 삭제를 확인하는 메시지가 표시됩니다. 리소스 그룹의 이름을 입력하여 확인한 다음, **삭제** 를 선택합니다.

잠시 후, 리소스 그룹 및 모든 해당 리소스가 삭제됩니다.

## <a name="next-steps"></a>다음 단계 

* [Redis 용 Azure 캐시](./cache-overview.md) 에 대 한 자세한 정보
* [함수](../azure-functions/functions-create-function-linux-custom-image.md) 설명서에서 함수 앱을 구성 하는 방법에 대해 알아봅니다.
* [API 참조](/python/api/azureml-contrib-functions/azureml.contrib.functions?preserve-view=true&view=azure-ml-py) 
* [Redis 용 Azure Cache를 사용 하는 Python 앱](./cache-python-get-started.md) 만들기