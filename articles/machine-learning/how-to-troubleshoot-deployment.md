---
title: 배포 문제 해결 가이드
titleSuffix: Azure Machine Learning
description: Azure 기계 학습을 사용하여 Azure Kubernetes 서비스 및 Azure 컨테이너 인스턴스를 사용하여 일반적인 Docker 배포 오류를 해결, 해결 및 해결하는 방법을 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
author: clauren42
ms.author: clauren
ms.reviewer: jmartens
ms.date: 03/05/2020
ms.custom: seodec18
ms.openlocfilehash: fab46f7d7ae74ad643ce3f122b27b0dc767f5a78
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78399673"
---
# <a name="troubleshooting-azure-machine-learning-azure-kubernetes-service-and-azure-container-instances-deployment"></a>Azure 기계 학습 Azure Kubernetes 서비스 및 Azure 컨테이너 인스턴스 배포 문제 해결

Azure 기계 학습을 사용하여 Azure 컨테이너 인스턴스(ACI) 및 AKS(Azure Kubernetes Service)를 사용하여 일반적인 Docker 배포 오류를 해결하거나 해결하는 방법에 대해 알아봅니다.

Azure 기계 학습에서 모델을 배포할 때 시스템은 여러 작업을 수행합니다.

모델 배포에 권장되고 최신 접근 방식은 [환경](https://docs.microsoft.com/azure/machine-learning/service/how-to-use-environments) 개체를 입력 매개 변수로 사용하는 [Model.deploy()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model%28class%29?view=azure-ml-py#deploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-) API를 사용하는 것입니다. 이 경우 서비스는 배포 단계에서 기본 docker 이미지를 만들고 필요한 모델을 모두 한 번에 마운트합니다. 기본 배포 작업은 다음과 같습니다.

1. 모델을 작업 영역 모델 레지스트리에 등록합니다.

2. 추론 구성 정의:
    1. [환경](https://docs.microsoft.com/azure/machine-learning/service/how-to-use-environments) yaml 파일에 지정한 종속성을 기반으로 환경 개체를 만들거나 조달된 환경 중 하나를 사용합니다.
    2. 환경 및 점수 매기기 스크립트를 기반으로 추론 구성(추론 구성 개체)을 만듭니다.

3. ACI(Azure 컨테이너 인스턴스) 서비스 또는 AKS(Azure Kubernetes Service)에 모델을 배포합니다.

이 프로세스에 대한 자세한 정보는 [모델 관리](concept-model-management-and-deployment.md) 소개를 참조하세요.

## <a name="prerequisites"></a>사전 요구 사항

* **Azure 구독**. 없는 경우 [Azure 기계 학습의 무료 또는 유료 버전을](https://aka.ms/AMLFree)사용해 보십시오.
* [Azure 기계 학습 SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py).
* [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).
* [Azure 기계 학습에 대한 CLI 확장.](reference-azure-machine-learning-cli.md)
* 로컬로 디버깅하려면 로컬 시스템에 작업 중인 Docker 설치가 있어야 합니다.

    Docker 설치를 확인하려면 터미널 `docker run hello-world` 또는 명령 프롬프트의 명령을 사용합니다. Docker 설치 또는 Docker 오류 해결에 대한 자세한 내용은 [Docker 설명서를](https://docs.docker.com/)참조하십시오.

## <a name="before-you-begin"></a>시작하기 전에

문제가 발생할 경우 가장 먼저 할 일은 배포 작업을 개별 단계로 분리하여(이전 설명 참조) 문제를 격리하는 것입니다.

[환경](https://docs.microsoft.com/azure/machine-learning/service/how-to-use-environments) 개체가 있는 [Model.deploy()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model%28class%29?view=azure-ml-py#deploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-) API를 통해 새/권장 배포 방법을 입력 매개 변수로 사용한다고 가정하면 코드를 세 가지 주요 단계로 나눌 수 있습니다.

1. 모델을 등록합니다. 다음은 몇 가지 샘플 코드입니다.

    ```python
    from azureml.core.model import Model


    # register a model out of a run record
    model = best_run.register_model(model_name='my_best_model', model_path='outputs/my_model.pkl')

    # or, you can register a file or a folder of files as a model
    model = Model.register(model_path='my_model.pkl', model_name='my_best_model', workspace=ws)
    ```

2. 배포에 대한 추론 구성 정의:

    ```python
    from azureml.core.model import InferenceConfig
    from azureml.core.environment import Environment


    # create inference configuration based on the requirements defined in the YAML
    myenv = Environment.from_conda_specification(name="myenv", file_path="myenv.yml")
    inference_config = InferenceConfig(entry_script="score.py", environment=myenv)
    ```

3. 이전 단계에서 만든 추론 구성을 사용하여 모델을 배포합니다.

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

ACI 또는 AKS에 모델을 배포하는 데 문제가 있는 경우 로컬 웹 서비스로 배포해 보십시오. 로컬 웹 서비스를 사용하면 문제를 보다 쉽게 해결할 수 있습니다. 모델이 포함된 Docker 이미지가 다운로드되어 로컬 시스템에서 시작됩니다.

> [!WARNING]
> 로컬 웹 서비스 배포는 프로덕션 시나리오에 대해 지원되지 않습니다.

로컬로 배포하려면 배포 구성을 만드는 데 사용할 `LocalWebservice.deploy_configuration()` 코드를 수정합니다. 그런 `Model.deploy()` 다음 서비스를 배포하는 데 사용합니다. 다음 예제에서는 모델(모델 변수에 포함)을 로컬 웹 서비스로 배포합니다.

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

고유한 conda 사양 YAML을 정의하는 경우 버전 >= 1.0.45를 핍 종속성으로 azureml 기본값을 나열해야 합니다. 이 패키지에는 모델을 웹 서비스로 호스팅하는 데 필요한 기능이 포함되어 있습니다.

이 시점에서 정상적으로 서비스를 사용할 수 있습니다. 예를 들어 다음 코드는 서비스에 데이터를 보내는 방법을 보여 줍니다.

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

Python 환경 사용자 지정에 대한 자세한 내용은 [교육 및 배포를 위한 환경 만들기 및 관리를](how-to-use-environments.md)참조하십시오. 

### <a name="update-the-service"></a>서비스 업데이트

로컬 테스트 중에 로깅을 `score.py` 추가하거나 발견한 문제를 해결하기 위해 파일을 업데이트해야 할 수 있습니다. `score.py` 파일에 대한 변경 내용을 다시 `reload()`로드하려면 을 사용합니다. 예를 들어 다음 코드는 서비스에 대한 스크립트를 다시 로드한 다음 데이터를 전송합니다. 업데이트된 `score.py` 파일을 사용하여 데이터의 점수가 매겨지다.

> [!IMPORTANT]
> 이 `reload` 메서드는 로컬 배포에만 사용할 수 있습니다. 다른 계산 대상으로 배포를 업데이트하는 방법에 대한 자세한 내용은 [모델 배포의](how-to-deploy-and-where.md#update)업데이트 섹션을 참조하십시오.

```python
service.reload()
print(service.run(input_data=test_sample))
```

> [!NOTE]
> 스크립트는 서비스에서 사용하는 `InferenceConfig` 개체가 지정한 위치에서 다시 로드됩니다.

모델, Conda 종속성 또는 배포 구성을 변경하려면 [update()를](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice%28class%29?view=azure-ml-py#update--args-)사용합니다. 다음 예제는 서비스에서 사용하는 모델을 업데이트합니다.

```python
service.update([different_model], inference_config, deployment_config)
```

### <a name="delete-the-service"></a>서비스 삭제

서비스를 삭제하려면 [delete()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice%28class%29?view=azure-ml-py#delete--)를 사용합니다.

### <a name="inspect-the-docker-log"></a><a id="dockerlog"></a>Docker 로그 검사

서비스 개체에서 자세한 Docker 엔진 로그 메시지를 인쇄할 수 있습니다. ACI, AKS 및 로컬 배포에 대한 로그를 볼 수 있습니다. 다음 예제에서는 로그를 인쇄하는 방법을 보여 줍니다.

```python
# if you already have the service object handy
print(service.get_logs())

# if you only know the name of the service (note there might be multiple services with the same name but different version number)
print(ws.webservices['mysvc'].get_logs())
```

## <a name="service-launch-fails"></a>서비스 시작 실패

이미지가 성공적으로 빌드되면 시스템은 배포 구성을 사용하여 컨테이너를 시작하려고 시도합니다. 컨테이너 시작 프로세스의 일부로, 시스템에서 채점 스크립트의 `init()` 함수를 호출합니다. `init()` 함수에 catch되지 않은 예외가 있는 경우 오류 메시지에 **CrashLoopBackOff** 오류가 표시될 수 있습니다.

[Docker 로그 검사](#dockerlog) 섹션의 정보를 사용하여 로그를 확인합니다.

## <a name="function-fails-get_model_path"></a>함수 실패: get_model_path()

종종 점수 `init()` 매기기 스크립트의 함수에서 [Model.get_model_path()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#get-model-path-model-name--version-none---workspace-none-) 함수는 컨테이너에 모델 파일 또는 모델 파일 폴더를 찾기 위해 호출됩니다. 모델 파일 이나 폴더를 찾을 수 없는 경우 함수가 실패 합니다. 이 오류를 디버그하는 가장 쉬운 방법은 컨테이너 셸에서 아래의 Python 코드를 실행하는 것입니다.

```python
from azureml.core.model import Model
import logging
logging.basicConfig(level=logging.DEBUG)
print(Model.get_model_path(model_name='my-best-model'))
```

이 예제에서는 점수 매기기 스크립트가 모델 파일 또는 폴더를 `/var/azureml-app`찾을 것으로 예상되는 컨테이너의 로컬 경로(상대)를 인쇄합니다. 그러면 예상 위치에 파일 또는 폴더가 실제로 있는지 확인할 수 있습니다.

로깅 수준을 DEBUG로 설정하면 추가 정보가 기록될 수 있으며, 이로 인해 오류를 식별하는 데 유용할 수 있습니다.

## <a name="function-fails-runinput_data"></a>함수 실패: run(input_data)

서비스가 성공적으로 배포되었지만 채점 엔드포인트에 데이터를 게시할 때 크래시가 발생하는 경우 오류를 catch하는 명령문을 `run(input_data)` 함수에 추가하면 구체적인 오류 메시지가 반환됩니다. 예를 들어:

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

**참고**: `run(input_data)` 호출에서 오류 메시지를 반환하는 방법은 디버깅 용도로만 사용해야 합니다. 보안상의 이유로 프로덕션 환경에서 는 오류 메시지를 이러한 방식으로 반환해서는 안 됩니다.

## <a name="http-status-code-502"></a>HTTP 상태 코드 502

502 상태 코드는 서비스가 score.py 파일의 `run()` 메서드에서 예외를 throw했거나 충돌했음을 나타냅니다. 이 문서의 정보를 사용하여 파일을 디버깅합니다.

## <a name="http-status-code-503"></a>HTTP 상태 코드 503

Azure Kubernetes 서비스 배포는 자동 크기 조정을 지원하므로 추가 로드를 지원하기 위해 복제본을 추가할 수 있습니다. 그러나 자동 크기 조정기는 **부하의 점진적인** 변화를 처리하도록 설계되었습니다. 초당 요청이 큰 스파이크를 받으면 클라이언트는 HTTP 상태 코드 503을 받을 수 있습니다.

503 상태 코드를 방지하는 데 도움이 되는 두 가지 방법이 있습니다.

* 자동 크기 조정이 새 복제본을 만드는 사용률 수준을 변경합니다.
    
    기본적으로 대상 사용률을 70%로 설정하여 서비스가 최대 30%의 RPS(초당 요청 스파이크)를 처리할 수 있습니다. 사용률 목표를 더 낮은 `autoscale_target_utilization` 값으로 설정하여 조정할 수 있습니다.

    > [!IMPORTANT]
    > 이렇게 변경해도 복제본을 *더 빠르게*만들 수 없습니다. 대신 낮은 사용률 임계값으로 만들어집니다. 서비스가 70%가 사용될 때까지 기다리는 대신 값을 30%로 변경하면 30% 사용률이 발생할 때 복제본이 만들어집니다.
    
    웹 서비스에서 현재 최대 복제본을 이미 사용하고 있고 여전히 503개의 `autoscale_max_replicas` 상태 코드가 표시되는 경우 값을 늘려 최대 복제본 수를 늘립니다.

* 최소 복제본 수를 변경합니다. 최소 복제본을 늘리면 들어오는 스파이크를 처리하기 위한 더 큰 풀이 제공됩니다.

    복제본의 최소 수를 늘리려면 `autoscale_min_replicas` 더 높은 값으로 설정합니다. 다음 코드를 사용하여 필요한 복제본을 계산하여 값을 프로젝트와 관련된 값으로 대체할 수 있습니다.

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
    > 새 최소 복제본이 처리할 수 있는 것보다 큰 요청 스파이크를 받으면 503을 다시 받을 수 있습니다. 예를 들어 서비스에 대한 트래픽이 증가함에 따라 최소 복제본을 늘려야 할 수 있습니다.

및 `autoscale_target_utilization` `autoscale_max_replicas` `autoscale_min_replicas` 설정에 대한 자세한 내용은 [AksWebservice](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.akswebservice?view=azure-ml-py) 모듈 참조를 참조하십시오.

## <a name="http-status-code-504"></a>HTTP 상태 코드 504

504 상태 코드는 요청시간이 만료되었음을 나타냅니다. 기본 시간 설정은 1분입니다.

시간 시간을 늘리거나 score.py 수정하여 불필요한 호출을 제거하여 서비스 속도를 높일 수 있습니다. 이러한 작업으로 인해 문제가 해결되지 않으면 이 문서의 정보를 사용하여 score.py 파일을 디버깅합니다. 코드는 중단된 상태이거나 무한 루프일 수 있습니다.

## <a name="advanced-debugging"></a>고급 디버깅

경우에 따라 모델 배포에 포함된 Python 코드를 대화형으로 디버깅해야 할 수 있습니다. 예를 들어 항목 스크립트가 실패하고 추가 로깅으로 이유를 확인할 수 없는 경우입니다. 비주얼 스튜디오 코드와 PTVSD(비주얼 스튜디오용 파이썬 도구)를 사용하여 Docker 컨테이너 내에서 실행되는 코드에 연결할 수 있습니다.

> [!IMPORTANT]
> 이 디버깅 방법은 모델을 `Model.deploy()` `LocalWebservice.deploy_configuration` 로컬로 사용하고 배포할 때 작동하지 않습니다. 대신 [Model.package()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#package-workspace--models--inference-config-none--generate-dockerfile-false-) 메서드를 사용하여 이미지를 만들어야 합니다.

로컬 웹 서비스 배포에는 로컬 시스템에 작업 Docker 설치가 필요합니다. Docker 사용에 대한 자세한 내용은 [Docker 설명서를](https://docs.docker.com/)참조하십시오.

### <a name="configure-development-environment"></a>개발 환경 구성

1. 로컬 VS 코드 개발 환경에 PTVSD용 파이썬 도구(PTVSD)를 설치하려면 다음 명령을 사용합니다.

    ```
    python -m pip install --upgrade ptvsd
    ```

    VS 코드와 함께 PTVSD 사용에 대한 자세한 내용은 [원격 디버깅](https://code.visualstudio.com/docs/python/debugging#_remote-debugging)을 참조하십시오.

1. DOCKER 이미지와 통신하도록 VS 코드를 구성하려면 새 디버그 구성을 만듭니다.

    1. VS 코드에서 __디버그__ 메뉴를 선택한 다음 __구성 열기를 선택합니다.__ __launch.json이라는__ 파일이 열립니다.

    1. __launch.json__ 파일에서 을 포함하는 `"configurations": [`줄을 찾아 다음 텍스트를 삽입합니다.

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
        > 구성 섹션에 이미 다른 항목이 있는 경우 삽입한 코드 다음의 쉼표(,)를 추가합니다.

        이 섹션은 포트 5678을 사용하여 Docker 컨테이너에 연결합니다.

    1. __launch.json__ 파일을 저장합니다.

### <a name="create-an-image-that-includes-ptvsd"></a>PTVSD가 포함된 이미지 만들기

1. PTVSD를 포함되도록 배포에 대한 conda 환경을 수정합니다. 다음 예제에서는 매개 변수를 `pip_packages` 사용 하 여 추가 보여 줍니다.

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

1. PTVSD를 시작하고 서비스가 시작될 때 연결을 기다리려면 파일 맨 위에 `score.py` 다음을 추가합니다.

    ```python
    import ptvsd
    # Allows other computers to attach to ptvsd on this IP address and port.
    ptvsd.enable_attach(address=('0.0.0.0', 5678), redirect_output = True)
    # Wait 30 seconds for a debugger to attach. If none attaches, the script continues as normal.
    ptvsd.wait_for_attach(timeout = 30)
    print("Debugger attached...")
    ```

1. 환경 정의에 따라 이미지를 만들고 이미지를 로컬 레지스트리로 가져옵니다. 디버깅 하는 동안 다시 만들 필요 하지 않고 이미지의 파일을 변경 하는 것이 좋습니다. Docker 이미지에 텍스트 편집기(vim)를 `Environment.docker.base_image` 설치하려면 `Environment.docker.base_dockerfile` 및 속성을 사용합니다.

    > [!NOTE]
    > 이 예제에서는 `ws` Azure 기계 학습 작업 영역을 `model` 가리키고 배포 중인 모델이라고 가정합니다. 파일에는 `myenv.yml` 1단계에서 만든 conda 종속성이 포함되어 있습니다.

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

    이미지를 만들고 다운로드하면 이미지 경로(이 경우 다이제스트이기도 한 리포지토리, 이름 및 태그 포함)가 다음과 유사한 메시지에 표시됩니다.

    ```text
    Status: Downloaded newer image for myregistry.azurecr.io/package@sha256:<image-digest>
    ```

1. 이미지 작업을 더 쉽게 수행하려면 다음 명령을 사용하여 태그를 추가합니다. 이전 `myimagepath` 단계의 위치 값으로 바꿉습니다.

    ```bash
    docker tag myimagepath debug:1
    ```

    나머지 단계에서는 전체 이미지 경로 값 `debug:1` 대신 로컬 이미지를 참조할 수 있습니다.

### <a name="debug-the-service"></a>서비스 디버그

> [!TIP]
> `score.py` 파일의 PTVSD 연결에 대한 시간 시간을 설정한 경우 시간 지정이 만료되기 전에 VS 코드를 디버그 세션에 연결해야 합니다. VS 코드를 시작하고, 의 `score.py`로컬 복사본을 열고, 중단점을 설정하고, 이 섹션의 단계를 사용하기 전에 바로 사용할 수 있습니다.
>
> 중단점 디버깅 및 설정에 대한 자세한 내용은 [디버깅](https://code.visualstudio.com/Docs/editor/debugging)을 참조하십시오.

1. 이미지를 사용하여 Docker 컨테이너를 시작하려면 다음 명령을 사용합니다.

    ```bash
    docker run --rm --name debug -p 8000:5001 -p 5678:5678 debug:1
    ```

1. 컨테이너 내부의 PTVSD에 VS 코드를 연결하려면 VS 코드를 열고 F5 키를 사용하거나 __디버그를__선택합니다. 메시지가 표시되면 __Azure 기계 학습: Docker 디버그__ 구성을 선택합니다. 또한 사이드 바에서 디버그 아이콘을 선택할 수 있습니다., __Azure 기계 학습: 도커 디버그 디버그__ 디버그 메뉴에서, 다음 녹색 화살표를 사용 하 여 디버거를 연결 합니다.

    ![디버그 아이콘, 디버깅 버튼 시작 및 구성 선택기](./media/how-to-troubleshoot-deployment/start-debugging.png)

이 시점에서 VS 코드는 Docker 컨테이너 내부의 PTVSD에 연결하고 이전에 설정한 중단점에서 중지합니다. 이제 코드를 실행하면서 단계별로 실행하고 변수를 볼 수 있습니다.

VS 코드를 사용하여 파이썬을 디버깅하는 방법에 대한 자세한 내용은 [파이썬 코드 디버깅을](https://docs.microsoft.com/visualstudio/python/debugging-python-in-visual-studio?view=vs-2019)참조하십시오.

<a id="editfiles"></a>
### <a name="modify-the-container-files"></a>컨테이너 파일 수정

이미지의 파일을 변경하려면 실행 중인 컨테이너에 연결하고 bash 셸을 실행할 수 있습니다. 거기에서 vim을 사용하여 파일을 편집 할 수 있습니다.

1. 실행 중인 컨테이너에 연결 하 고 컨테이너에서 bash 셸을 시작 하려면 다음 명령을 사용 합니다.

    ```bash
    docker exec -it debug /bin/bash
    ```

1. 서비스에서 사용하는 파일을 찾으려면 기본 디렉터리와 다른 `/var/azureml-app`경우 컨테이너의 bash 셸에서 다음 명령을 사용합니다.

    ```bash
    cd /var/azureml-app
    ```

    여기에서 vim을 사용하여 `score.py` 파일을 편집할 수 있습니다. vim 사용에 대한 자세한 내용은 [Vim 편집기 사용을](https://www.tldp.org/LDP/intro-linux/html/sect_06_02.html)참조하십시오.

1. 컨테이너에 대한 변경 내용은 일반적으로 유지되지 않습니다. 변경 내용을 저장하려면 위의 단계(즉, 다른 셸에서)에서 시작된 셸을 종료하기 전에 다음 명령을 사용합니다.

    ```bash
    docker commit debug debug:2
    ```

    이 명령은 편집 내용이 포함된 새 `debug:2` 이미지를 만듭니다.

    > [!TIP]
    > 변경 사항이 적용되기 전에 현재 컨테이너를 중지하고 새 버전 사용을 시작해야 합니다.

1. 컨테이너의 파일에 대한 변경 내용을 VS Code에서 사용하는 로컬 파일과 동기화된 상태로 유지해야 합니다. 그렇지 않으면 디버거 환경이 예상대로 작동하지 않습니다.

### <a name="stop-the-container"></a>컨테이너 중지

컨테이너를 중지하려면 다음 명령을 사용합니다.

```bash
docker stop debug
```

## <a name="next-steps"></a>다음 단계

배포에 대해 자세히 알아보세요.

* [배포 방법 및 위치](how-to-deploy-and-where.md)
* [자습서: 학습 & 모델 배포](tutorial-train-models-with-aml.md)
