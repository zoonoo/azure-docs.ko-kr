---
title: Docker 배포 문제 해결
titleSuffix: Azure Machine Learning
description: Azure Kubernetes Service 및 Azure Machine Learning를 사용 하 여 Azure Container Instances 일반적인 Docker 배포 오류를 해결 하 고 해결 하 고 해결 하는 방법을 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: troubleshooting
author: clauren42
ms.author: clauren
ms.reviewer: jmartens
ms.date: 03/05/2020
ms.custom: contperfq4, tracking-python
ms.openlocfilehash: 13ce9204ad09d2ecb4b149cf50696aa73d927314
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85214369"
---
# <a name="troubleshoot-docker-deployment-of-models-with-azure-kubernetes-service-and-azure-container-instances"></a>Azure Kubernetes Service 및 Azure Container Instances를 사용 하 여 모델의 Docker 배포 문제 해결 

Azure Machine Learning를 사용 하 여 Azure Container Instances (ACI) 및 Azure Kubernetes 서비스 (AKS)와의 일반적인 Docker 배포 오류를 해결 하 고 해결 하거나 해결 하는 방법을 알아봅니다.

## <a name="prerequisites"></a>사전 요구 사항

* **Azure 구독**. 구독이 없는 경우[Azure Machine Learning 평가판 또는 유료 버전](https://aka.ms/AMLFree)을 사용해 보세요.
* [Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py)
* [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)
* [Azure Machine Learning용 CLI 확장](reference-azure-machine-learning-cli.md)
* 로컬로 디버그하려면 로컬 시스템에서 작동하는 Docker가 설치되어 있어야 합니다.

    Docker 설치를 확인하려면 터미널 또는 명령 프롬프트에서 `docker run hello-world` 명령을 사용합니다. Docker 설치 또는 Docker 오류 문제 해결에 대한 자세한 내용은 [Docker 설명서](https://docs.docker.com/)를 참조하세요.

## <a name="steps-for-docker-deployment-of-machine-learning-models"></a>기계 학습 모델의 Docker 배포 단계

Azure Machine Learning에서 모델을 배포하는 경우 시스템에서 많은 작업을 수행합니다.

모델 배포에 권장 되는 방법은 [환경](how-to-use-environments.md) 개체를 입력 매개 변수로 사용 하는 [모델인 ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model%28class%29?view=azure-ml-py#deploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-) API를 사용 하는 것입니다. 이 경우 서비스는 배포 단계 중에 기본 docker 이미지를 만들고 필요한 모델을 모두 하나의 호출로 탑재 합니다. 기본 배포 작업은 다음과 같습니다.

1. 모델을 작업 영역 모델 레지스트리에 등록합니다.

2. 유추 구성을 정의합니다.
    1. 환경 yaml 파일에 지정한 종속성을 기반으로 하는 [Environment](how-to-use-environments.md) 개체를 만들거나 확보된 환경 중 하나를 사용합니다.
    2. 환경 및 채점 스크립트를 기반으로 하는 유추 구성(InferenceConfig 개체)을 만듭니다.

3. 모델을 ACI(Azure Container Instance) 서비스 또는 AKS(Azure Kubernetes Service)에 배포합니다.

이 프로세스에 대한 자세한 정보는 [모델 관리](concept-model-management-and-deployment.md) 소개를 참조하세요.

## <a name="before-you-begin"></a>시작하기 전에

문제가 발생할 경우 가장 먼저 할 일은 배포 작업을 개별 단계로 분리하여(이전 설명 참조) 문제를 격리하는 것입니다.

[Environment](how-to-use-environments.md) 개체를 입력 매개 변수로 사용하여 [Model.deploy()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model%28class%29?view=azure-ml-py#deploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-) API를 통해 배포하는 새 배포 방법 또는 추천 배포 방법을 사용한다고 가정하면 코드를 세 가지 주요 단계로 나눌 수 있습니다.

1. 모델을 등록합니다. 다음은 몇 가지 샘플 코드입니다.

    ```python
    from azureml.core.model import Model


    # register a model out of a run record
    model = best_run.register_model(model_name='my_best_model', model_path='outputs/my_model.pkl')

    # or, you can register a file or a folder of files as a model
    model = Model.register(model_path='my_model.pkl', model_name='my_best_model', workspace=ws)
    ```

2. 배포에 대한 유추 구성을 정의합니다.

    ```python
    from azureml.core.model import InferenceConfig
    from azureml.core.environment import Environment


    # create inference configuration based on the requirements defined in the YAML
    myenv = Environment.from_conda_specification(name="myenv", file_path="myenv.yml")
    inference_config = InferenceConfig(entry_script="score.py", environment=myenv)
    ```

3. 이전 단계에서 만든 유추 구성을 사용하여 모델을 배포합니다.

    ```python
    from azureml.core.webservice import AciWebservice


    # deploy the model
    aci_config = AciWebservice.deploy_configuration(cpu_cores=1, memory_gb=1)
    aci_service = Model.deploy(workspace=ws,
                           name='my-service',
                           models=[model],
                           inference_config=inference_config,
                           deployment_config=aci_config)
    aci_service.wait_for_deployment(show_output=True)
    ```

배포 프로세스를 개별 작업으로 분리한 후에는 가장 일반적인 오류 중 일부를 볼 수 있습니다.

## <a name="debug-locally"></a>로컬에서 디버그

ACI 또는 AKS에 모델을 배포하는 데 문제가 발생하면 로컬 웹 서비스로 배포해 보세요. 로컬 웹 서비스를 사용하면 문제를 더 쉽게 해결할 수 있습니다. 모델이 포함된 Docker 이미지는 로컬 시스템에서 다운로드되어 시작됩니다.

> [!WARNING]
> 프로덕션 시나리오에는 로컬 웹 서비스 배포가 지원되지 않습니다.

로컬로 배포하려면 `LocalWebservice.deploy_configuration()`을 사용하여 배포 구성을 만들도록 코드를 수정합니다. 그런 다음, `Model.deploy()`를 사용하여 서비스를 배포합니다. 다음 예제에서는 모델 변수에 포함된 모델을 로컬 웹 서비스로 배포합니다.

```python
from azureml.core.environment import Environment
from azureml.core.model import InferenceConfig, Model
from azureml.core.webservice import LocalWebservice


# Create inference configuration based on the environment definition and the entry script
myenv = Environment.from_conda_specification(name="env", file_path="myenv.yml")
inference_config = InferenceConfig(entry_script="score.py", environment=myenv)
# Create a local deployment, using port 8890 for the web service endpoint
deployment_config = LocalWebservice.deploy_configuration(port=8890)
# Deploy the service
service = Model.deploy(
    ws, "mymodel", [model], inference_config, deployment_config)
# Wait for the deployment to complete
service.wait_for_deployment(True)
# Display the port that the web service is available on
print(service.port)
```

사용자 고유의 conda 사양 YAML을 정의 하는 경우 pip 종속성으로 버전 >= 1.0.45이 있는 azureml 기본값을 나열 해야 합니다. 이 패키지에는 모델을 웹 서비스로 호스팅하는 데 필요한 기능이 포함되어 있습니다.

이 시점에서 서비스를 정상적으로 사용할 수 있습니다. 예를 들어 다음 코드에서는 데이터를 서비스에 보내는 방법을 보여 줍니다.

```python
import json

test_sample = json.dumps({'data': [
    [1, 2, 3, 4, 5, 6, 7, 8, 9, 10],
    [10, 9, 8, 7, 6, 5, 4, 3, 2, 1]
]})

test_sample = bytes(test_sample, encoding='utf8')

prediction = service.run(input_data=test_sample)
print(prediction)
```

Python 환경을 사용자 지정하는 방법에 대한 자세한 내용은 [학습 및 배포 환경 만들기 및 관리](how-to-use-environments.md)를 참조하세요. 

### <a name="update-the-service"></a>서비스 업데이트

로컬 테스트 중에 로깅을 추가하도록 `score.py` 파일을 업데이트하거나 검색한 문제를 해결해야 할 수도 있습니다. `score.py` 파일의 변경 내용을 다시 로드하려면 `reload()`를 사용합니다. 예를 들어 다음 코드에서는 서비스에 대한 스크립트를 다시 로드한 다음, 데이터를 이 스크립트에 보냅니다. 데이터는 업데이트된 `score.py` 파일을 사용하여 채점됩니다.

> [!IMPORTANT]
> `reload` 메서드는 로컬 배포에만 사용할 수 있습니다. 배포를 다른 컴퓨팅 대상으로 업데이트하는 방법에 대한 자세한 내용은 [모델 배포](how-to-deploy-and-where.md#update)의 업데이트 섹션을 참조하세요.

```python
service.reload()
print(service.run(input_data=test_sample))
```

> [!NOTE]
> 스크립트는 서비스에서 사용하는 `InferenceConfig` 개체로 지정된 위치에서 다시 로드됩니다.

모델, Conda 종속성 또는 배포 구성을 변경하려면 [update()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice%28class%29?view=azure-ml-py#update--args-)를 사용합니다. 다음 예제에서는 서비스에서 사용하는 모델을 업데이트합니다.

```python
service.update([different_model], inference_config, deployment_config)
```

### <a name="delete-the-service"></a>서비스 삭제

서비스를 삭제하려면 [delete()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice%28class%29?view=azure-ml-py#delete--)를 사용합니다.

### <a name="inspect-the-docker-log"></a><a id="dockerlog"></a> Docker 로그 검사

서비스 개체에서 자세한 Docker 엔진 로그 메시지를 인쇄할 수 있습니다. ACI, AKS 및 로컬 배포에 대한 로그를 볼 수 있습니다. 다음 예제에서는 로그를 출력하는 방법을 보여 줍니다.

```python
# if you already have the service object handy
print(service.get_logs())

# if you only know the name of the service (note there might be multiple services with the same name but different version number)
print(ws.webservices['mysvc'].get_logs())
```
## <a name="container-cannot-be-scheduled"></a>컨테이너를 예약할 수 없음

서비스를 Azure Kubernetes Service 컴퓨팅 대상에 배포하는 경우 Azure Machine Learning에서 서비스를 요청된 리소스의 양으로 예약하려고 합니다. 5 분 후에 클러스터에서 사용할 수 있는 적절 한 양의 리소스를 사용할 수 있는 노드가 없는 경우 배포는 메시지와 함께 실패 합니다 `Couldn't Schedule because the kubernetes cluster didn't have available resources after trying for 00:05:00` . 이 오류는 더 많은 노드를 추가하거나 노드의 SKU를 변경하거나 서비스의 리소스 요구 사항을 변경하여 해결할 수 있습니다. 

오류 메시지는 일반적으로 필요한 리소스를 나타냅니다. 즉, `0/3 nodes are available: 3 Insufficient nvidia.com/gpu` 서비스에 gpu가 필요 하 고 사용 가능한 gpu가 없는 클러스터에 3 개의 노드가 있음을 나타내는 오류 메시지가 표시 됩니다. 이는 GPU SKU를 사용하는 경우 더 많은 노드를 추가하거나, 사용하지 않는 경우 GPU 지원 SKU로 전환하거나, GPU가 필요하지 않도록 환경을 변경하여 해결할 수 있습니다.  

## <a name="service-launch-fails"></a>서비스 시작 실패

이미지가 성공적으로 빌드되면 시스템에서 배포 구성을 사용하여 컨테이너를 시작하려고 합니다. 컨테이너 시작 프로세스의 일부로, 시스템에서 채점 스크립트의 `init()` 함수를 호출합니다. `init()` 함수에 catch되지 않은 예외가 있는 경우 오류 메시지에 **CrashLoopBackOff** 오류가 표시될 수 있습니다.

[Docker 로그 검사](#dockerlog) 섹션의 정보를 사용하여 로그를 확인하세요.

## <a name="function-fails-get_model_path"></a>함수 실패: get_model_path()

채점 스크립트의 `init()` 함수에서 컨테이너의 모델 파일 또는 모델 파일의 폴더를 찾기 위해 [Model.get_model_path()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#get-model-path-model-name--version-none---workspace-none-) 함수가 호출되는 경우가 많습니다. 모델 파일 또는 폴더를 찾을 수 없으면 함수가 실패합니다. 이 오류를 디버그하는 가장 쉬운 방법은 컨테이너 셸에서 아래의 Python 코드를 실행하는 것입니다.

```python
from azureml.core.model import Model
import logging
logging.basicConfig(level=logging.DEBUG)
print(Model.get_model_path(model_name='my-best-model'))
```

이 예제에서는 채점 스크립트에서 모델 파일 또는 폴더를 찾을 것으로 예상되는 컨테이너의 로컬 경로(`/var/azureml-app`에 대한 상대 경로)를 출력합니다. 그러면 예상 위치에 파일 또는 폴더가 실제로 있는지 확인할 수 있습니다.

로깅 수준을 DEBUG로 설정하면 추가 정보가 기록될 수 있으며, 이는 오류를 식별하는 데 유용할 수 있습니다.

## <a name="function-fails-runinput_data"></a>함수 실패: run(input_data)

서비스가 성공적으로 배포되었지만 채점 엔드포인트에 데이터를 게시할 때 크래시가 발생하는 경우 오류를 catch하는 명령문을 `run(input_data)` 함수에 추가하면 구체적인 오류 메시지가 반환됩니다. 다음은 그 예입니다.

```python
def run(input_data):
    try:
        data = json.loads(input_data)['data']
        data = np.array(data)
        result = model.predict(data)
        return json.dumps({"result": result.tolist()})
    except Exception as e:
        result = str(e)
        # return error message back to the client
        return json.dumps({"error": result})
```

**참고**: `run(input_data)` 호출에서 오류 메시지를 반환하는 방법은 디버깅 용도로만 사용해야 합니다. 보안상의 이유로 프로덕션 환경에서는 오류 메시지를 이런 식으로 반환해서는 안 됩니다.

## <a name="http-status-code-502"></a>502 HTTP 상태 코드

502 상태 코드는 서비스에서 예외를 throw했거나 score.py 파일의 `run()` 메서드에서 작동을 중단했음을 나타냅니다. 이 문서의 정보를 사용하여 파일을 디버그하세요.

## <a name="http-status-code-503"></a>503 HTTP 상태 코드

Azure Kubernetes Service 배포는 자동 크기 조정을 지원하므로 추가 로드를 지원하기 위해 복제본을 추가할 수 있습니다. 그러나 자동 크기 조정기는 **점진적** 로드 변경을 처리하도록 설계되었습니다. 초당 요청 수가 크게 급증하면 클라이언트에서 503 HTTP 상태 코드를 받을 수 있습니다.

503 상태 코드를 방지하는 데 도움이 되는 두 가지 방법이 있습니다.

* 자동 크기 조정에서 새 복제본을 만드는 사용률 수준을 변경합니다.
    
    기본적으로 자동 크기 조정 목표 사용률은 70%로 설정됩니다. 즉 서비스에서 RPS(초당 요청 수)의 급증을 최대 30%까지 처리할 수 있습니다. `autoscale_target_utilization`을 더 낮은 값으로 설정하여 사용률 목표를 조정할 수 있습니다.

    > [!IMPORTANT]
    > 이 변경으로 인해 복제본이 *더 빨리* 만들어지지 않습니다. 대신 낮은 사용률 임계값에서 만들어집니다. 서비스 사용률이 70%가 될 때까지 기다리는 대신 값을 30%로 변경하면 30%의 사용률이 발생할 때 복제본이 만들어집니다.
    
    웹 서비스에서 이미 현재 최대 복제본을 사용하고 있고 여전히 503 상태 코드가 표시되면 `autoscale_max_replicas` 값을 늘려 최대 복제본 수를 늘립니다.

* 최소 복제본 수를 변경합니다. 최소 복제본 수를 늘리면 들어오는 급증을 처리할 수 있는 더 큰 풀이 제공됩니다.

    최소 복제본 수를 늘리려면 `autoscale_min_replicas`를 더 높은 값으로 설정합니다. 다음 코드를 사용하여 값을 프로젝트와 관련된 값으로 바꿔 필요한 복제본을 계산할 수 있습니다.

    ```python
    from math import ceil
    # target requests per second
    targetRps = 20
    # time to process the request (in seconds)
    reqTime = 10
    # Maximum requests per container
    maxReqPerContainer = 1
    # target_utilization. 70% in this example
    targetUtilization = .7

    concurrentRequests = targetRps * reqTime / targetUtilization

    # Number of container replicas
    replicas = ceil(concurrentRequests / maxReqPerContainer)
    ```

    > [!NOTE]
    > 새 최소 복제본에서 처리할 수 있는 것보다 더 큰 요청 급증을 받으면 503을 다시 받을 수 있습니다. 예를 들어 서비스 트래픽이 증가하면 최소 복제본을 늘려야 할 수 있습니다.

`autoscale_target_utilization`, `autoscale_max_replicas` 및 `autoscale_min_replicas` 설정에 대한 자세한 내용은 [AksWebservice](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.akswebservice?view=azure-ml-py) 모듈 참조를 참조하세요.

## <a name="http-status-code-504"></a>504 HTTP 상태 코드

504 상태 코드는 요청 시간이 초과되었음을 나타냅니다. 기본 시간 제한은 1분입니다.

불필요한 호출을 제거하도록 score.py를 수정하여 시간 제한을 늘리거나 서비스 속도를 높일 수 있습니다. 이러한 작업으로도 문제가 해결되지 않으면 이 문서의 정보를 사용하여 score.py 파일을 디버그합니다. 이 코드는 응답성이 아닌 상태나 무한 루프에 있을 수 있습니다.

## <a name="advanced-debugging"></a>고급 디버깅

경우에 따라 모델 배포에 포함된 Python 코드를 대화형으로 디버그해야 할 수도 있습니다. 예를 들어 항목 스크립트가 실패하고 추가 로깅으로 이유를 확인할 수 없는 경우입니다. Visual Studio Code 및 PTVSD(Visual Studio용 Python 도구)를 사용하면 Docker 컨테이너 내에서 실행되는 코드에 연결할 수 있습니다.

> [!IMPORTANT]
> `Model.deploy()` 및 `LocalWebservice.deploy_configuration`을 사용하여 모델을 로컬로 배포하는 경우 이 디버깅 방법이 작동하지 않습니다. 대신 [Model.package()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#package-workspace--models--inference-config-none--generate-dockerfile-false-) 메서드를 사용하여 이미지를 만들어야 합니다.

로컬 웹 서비스를 배포하려면 로컬 시스템에서 작동하는 Docker를 설치해야 합니다. Docker를 사용하는 방법에 대한 자세한 내용은 [Docker 설명서](https://docs.docker.com/)를 참조하세요.

### <a name="configure-development-environment"></a>개발 환경 구성

1. PTVSD(Visual Studio용 Python 도구)를 로컬 VS Code 개발 환경에 설치하려면 다음 명령을 사용합니다.

    ```
    python -m pip install --upgrade ptvsd
    ```

    VS Code에서 PTVSD를 사용하는 방법에 대한 자세한 내용은 [원격 디버깅](https://code.visualstudio.com/docs/python/debugging#_remote-debugging)을 참조하세요.

1. Docker 이미지와 통신하도록 VS Code를 구성하려면 새 디버그 구성을 만듭니다.

    1. VS Code에서 __디버그__ 메뉴를 선택한 다음, __구성 열기__를 선택합니다. __launch.json__이라는 파일이 열립니다.

    1. __launch.json__ 파일에서 `"configurations": [`가 포함된 줄을 찾고, 다음 텍스트를 이 줄 뒤에 삽입합니다.

        ```json
        {
            "name": "Azure Machine Learning: Docker Debug",
            "type": "python",
            "request": "attach",
            "port": 5678,
            "host": "localhost",
            "pathMappings": [
                {
                    "localRoot": "${workspaceFolder}",
                    "remoteRoot": "/var/azureml-app"
                }
            ]
        }
        ```

        > [!IMPORTANT]
        > 이미 다른 항목이 구성 섹션에 있으면 삽입한 코드 뒤에 쉼표(,)를 추가합니다.

        이 섹션에서는 5678 포트를 사용하여 Docker 컨테이너에 연결됩니다.

    1. __launch.json__ 파일을 저장합니다.

### <a name="create-an-image-that-includes-ptvsd"></a>PTVSD가 포함된 이미지 만들기

1. PTVSD를 포함하도록 배포에 대한 conda 환경을 수정합니다. 다음 예제에서는 `pip_packages` 매개 변수를 사용하여 추가하는 방법을 보여 줍니다.

    ```python
    from azureml.core.conda_dependencies import CondaDependencies 


    # Usually a good idea to choose specific version numbers
    # so training is made on same packages as scoring
    myenv = CondaDependencies.create(conda_packages=['numpy==1.15.4',            
                                'scikit-learn==0.19.1', 'pandas==0.23.4'],
                                 pip_packages = ['azureml-defaults==1.0.45', 'ptvsd'])

    with open("myenv.yml","w") as f:
        f.write(myenv.serialize_to_string())
    ```

1. PTVSD를 시작하고 서비스가 시작될 때 연결을 기다리려면 다음을 `score.py` 파일의 위쪽에 추가합니다.

    ```python
    import ptvsd
    # Allows other computers to attach to ptvsd on this IP address and port.
    ptvsd.enable_attach(address=('0.0.0.0', 5678), redirect_output = True)
    # Wait 30 seconds for a debugger to attach. If none attaches, the script continues as normal.
    ptvsd.wait_for_attach(timeout = 30)
    print("Debugger attached...")
    ```

1. 환경 정의를 기반으로 하는 이미지를 만들고, 이 이미지를 로컬 레지스트리로 끌어옵니다. 디버그하는 동안 이미지의 파일을 다시 만들지 않고도 변경할 수 있습니다. 텍스트 편집기(vim)를 Docker 이미지에 설치하려면 `Environment.docker.base_image` 및 `Environment.docker.base_dockerfile` 속성을 사용합니다.

    > [!NOTE]
    > 다음 예제에서는 `ws`에서 Azure Machine Learning 작업 영역을 가리키고 `model`이 배포할 모델이라고 가정합니다. `myenv.yml` 파일에는 1단계에서 만든 conda 종속성이 포함되어 있습니다.

    ```python
    from azureml.core.conda_dependencies import CondaDependencies
    from azureml.core.model import InferenceConfig
    from azureml.core.environment import Environment


    myenv = Environment.from_conda_specification(name="env", file_path="myenv.yml")
    myenv.docker.base_image = None
    myenv.docker.base_dockerfile = "FROM mcr.microsoft.com/azureml/base:intelmpi2018.3-ubuntu16.04\nRUN apt-get update && apt-get install vim -y"
    inference_config = InferenceConfig(entry_script="score.py", environment=myenv)
    package = Model.package(ws, [model], inference_config)
    package.wait_for_creation(show_output=True)  # Or show_output=False to hide the Docker build logs.
    package.pull()
    ```

    이미지가 만들어지고 다운로드되면 이미지 경로(리포지토리, 이름 및 태그 포함, 이 경우 digest이기도 함)가 다음과 비슷한 메시지에 표시됩니다.

    ```text
    Status: Downloaded newer image for myregistry.azurecr.io/package@sha256:<image-digest>
    ```

1. 이미지 작업을 더 쉽게 수행하려면 다음 명령을 사용하여 태그를 추가합니다. `myimagepath`를 이전 단계의 위치 값으로 바꿉니다.

    ```bash
    docker tag myimagepath debug:1
    ```

    나머지 단계에서는 전체 이미지 경로 값 대신 로컬 이미지를 `debug:1`로 참조할 수 있습니다.

### <a name="debug-the-service"></a>서비스 디버깅

> [!TIP]
> `score.py` 파일에서 PTVSD 연결에 대한 시간 제한을 설정하는 경우 시간 제한이 만료되기 전에 VS Code를 디버그 세션에 연결해야 합니다. VS Code를 시작하고, `score.py`의 로컬 복사본을 열고, 중단점을 설정하고, 이 섹션의 단계를 사용하기 전에 이동할 수 있도록 준비합니다.
>
> 디버깅 및 중단점 설정에 대한 자세한 내용은 [디버깅](https://code.visualstudio.com/Docs/editor/debugging)을 참조하세요.

1. 이미지를 사용하여 Docker 컨테이너를 시작하려면 다음 명령을 사용합니다.

    ```bash
    docker run --rm --name debug -p 8000:5001 -p 5678:5678 debug:1
    ```

1. VS Code를 컨테이너 내부의 PTVSD에 연결하려면 VS Code를 열고, F5 키를 사용하거나 __디버그__를 선택합니다. 메시지가 표시되면 __Azure Machine Learning: Docker 디버그__ 구성을 선택합니다. 또한 사이드바에서 디버그 아이콘을 선택하고, [디버그] 드롭다운 메뉴에서 __Azure Machine Learning: Docker 디버그__ 항목을 선택한 다음, 녹색 화살표를 사용하여 디버거를 연결할 수도 있습니다.

    ![디버그 아이콘, 디버깅 시작 단추 및 구성 선택기](./media/how-to-troubleshoot-deployment/start-debugging.png)

이 시점에서 VS Code는 Docker 컨테이너 내의 PTVSD에 연결하고 이전에 설정한 중단점에서 중지합니다. 이제 실행되는 코드를 단계별로 실행하고 변수를 보는 등의 작업을 수행할 수 있습니다.

VS Code를 사용하여 Python을 디버그하는 방법에 대한 자세한 내용은 [Python 코드 디버그](https://docs.microsoft.com/visualstudio/python/debugging-python-in-visual-studio?view=vs-2019)를 참조하세요.

<a id="editfiles"></a>
### <a name="modify-the-container-files"></a>컨테이너 파일 수정

이미지의 파일을 변경하려면 실행 중인 컨테이너에 연결하고 bash 셸을 실행할 수 있습니다. 여기서 vim을 사용하여 파일을 편집할 수 있습니다.

1. 실행 중인 컨테이너에 연결하고 컨테이너에서 bash 셸을 시작하려면 다음 명령을 사용합니다.

    ```bash
    docker exec -it debug /bin/bash
    ```

1. 서비스에서 사용하는 파일을 찾으려면 기본 디렉터리가 `/var/azureml-app`과 다른 경우 컨테이너의 bash 셸에서 다음 명령을 사용합니다.

    ```bash
    cd /var/azureml-app
    ```

    여기서 vim을 사용하여 `score.py` 파일을 편집할 수 있습니다. vim을 사용하는 방법에 대한 자세한 내용은 [Vim 편집기 사용](https://www.tldp.org/LDP/intro-linux/html/sect_06_02.html)을 참조하세요.

1. 컨테이너에 대한 변경 내용은 일반적으로 유지되지 않습니다. 변경 내용을 저장하려면 위의 단계에서 시작한 셸을 종료하기 전에(즉, 다른 셸에서) 다음 명령을 사용합니다.

    ```bash
    docker commit debug debug:2
    ```

    이 명령은 편집한 내용이 포함된 `debug:2`라는 새 이미지를 만듭니다.

    > [!TIP]
    > 변경 내용을 적용하려면 먼저 현재 컨테이너를 중지하고 새 버전 사용을 시작해야 합니다.

1. 컨테이너의 파일에 대한 변경 내용은 VS Code에서 사용하는 로컬 파일과 동기화된 상태로 유지해야 합니다. 그렇지 않으면 디버거 환경이 예상대로 작동하지 않습니다.

### <a name="stop-the-container"></a>컨테이너 중지

컨테이너를 중지하려면 다음 명령을 사용합니다.

```bash
docker stop debug
```

## <a name="next-steps"></a>다음 단계

배포에 대해 자세히 알아보세요.

* [배포 방법 및 위치](how-to-deploy-and-where.md)
* [자습서: 모델 학습 및 배포](tutorial-train-models-with-aml.md)
