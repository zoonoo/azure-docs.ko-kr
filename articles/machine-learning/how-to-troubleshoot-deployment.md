---
title: 웹 서비스 배포 문제 해결
titleSuffix: Azure Machine Learning
description: Azure Kubernetes Service 및 Azure Container Instances에서 일반적인 Docker 배포 오류를 해결 하 고 해결 하 고 문제를 해결 하는 방법에 대해 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
author: gvashishtha
ms.author: gopalv
ms.reviewer: jmartens
ms.date: 11/02/2020
ms.topic: troubleshooting
ms.custom: contperfq4, devx-track-python, deploy
ms.openlocfilehash: dfbfea22738e6aeb0df31ad941b2ff10e53795a4
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/04/2020
ms.locfileid: "93311293"
---
# <a name="troubleshoot-model-deployment"></a>모델 배포 문제 해결

Azure Machine Learning를 사용 하 여 Azure Container Instances (ACI) 및 Azure Kubernetes 서비스 (AKS)와의 일반적인 Docker 배포 오류를 해결 하 고 해결 하거나 해결 하는 방법을 알아봅니다.

## <a name="prerequisites"></a>필수 구성 요소

* **Azure 구독**. [Azure Machine Learning 평가판 또는 유료 버전](https://aka.ms/AMLFree)을 사용해 보세요.
* [Azure Machine Learning SDK](/python/api/overview/azure/ml/install?preserve-view=true&view=azure-ml-py)
* [Azure CLI](/cli/azure/install-azure-cli?preserve-view=true&view=azure-cli-latest)
* [Azure Machine Learning용 CLI 확장](reference-azure-machine-learning-cli.md)
* 로컬로 디버그하려면 로컬 시스템에서 작동하는 Docker가 설치되어 있어야 합니다.

    Docker 설치를 확인하려면 터미널 또는 명령 프롬프트에서 `docker run hello-world` 명령을 사용합니다. Docker 설치 또는 Docker 오류 문제 해결에 대한 자세한 내용은 [Docker 설명서](https://docs.docker.com/)를 참조하세요.

## <a name="steps-for-docker-deployment-of-machine-learning-models"></a>기계 학습 모델의 Docker 배포 단계

Azure Machine Learning에서 로컬이 아닌 계산에 모델을 배포 하는 경우 다음 작업이 수행 됩니다.

1. InferenceConfig의 환경 개체에서 지정한 Dockerfile은 원본 디렉터리의 내용과 함께 클라우드로 전송 됩니다.
1. 컨테이너 레지스트리에서 이전에 빌드된 이미지를 사용할 수 없는 경우 새 Docker 이미지가 클라우드에 빌드되고 작업 영역의 기본 컨테이너 레지스트리에 저장 됩니다.
1. 컨테이너 레지스트리의 Docker 이미지가 계산 대상으로 다운로드 됩니다.
1. 작업 영역의 기본 Blob 저장소가 계산 대상에 탑재 되어 등록 된 모델에 대 한 액세스를 제공 합니다.
1. 웹 서버는 사용자의 입력 스크립트 함수를 실행 하 여 초기화 됩니다. `init()`
1. 배포 된 모델에서 요청을 받으면 `run()` 함수는 해당 요청을 처리 합니다.

로컬 배포를 사용할 때의 주요 차이점은 컨테이너 이미지가 로컬 컴퓨터에 구축 된다는 것입니다. 따라서 로컬 배포를 위해 Docker를 설치 해야 합니다.

이러한 개략적인 단계를 이해 하면 오류가 발생 하는 위치를 이해 하는 데 도움이 됩니다.

## <a name="get-deployment-logs"></a>배포 로그 가져오기

디버깅 오류에 대 한 첫 번째 단계는 배포 로그를 가져오는 것입니다. 먼저 [여기에 설명 된 지침](how-to-deploy-and-where.md#connect-to-your-workspace) 에 따라 작업 영역에 연결 합니다.

# <a name="azure-cli"></a>[Azure CLI](#tab/azcli)

배포 된 웹 서비스에서 로그를 가져오려면 다음을 수행 합니다.

```bash
az ml service get-logs --verbose --workspace-name <my workspace name> --name <service name>
```

# <a name="python"></a>[Python](#tab/python)


라는 형식의 개체가 있다고 가정 하면 `azureml.core.Workspace` 다음을 `ws` 수행할 수 있습니다.

```python
print(ws.webservices)

# Choose the webservice you are interested in

from azureml.core import Webservice

service = Webservice(ws, '<insert name of webservice>')
print(service.get_logs())
```

---

## <a name="debug-locally"></a>로컬에서 디버그

ACI 또는 AKS에 모델을 배포할 때 문제가 발생 하는 경우 로컬 웹 서비스로 배포 합니다. 로컬 웹 서비스를 사용하면 문제를 더 쉽게 해결할 수 있습니다.

[MachineLearningNotebooks](https://github.com/Azure/MachineLearningNotebooks) 리포지토리에서 샘플 [로컬 배포 노트북](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/deployment/deploy-to-local/register-model-deploy-local.ipynb) 을 찾아 실행 가능한 예제를 탐색할 수 있습니다.

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

사용자 고유의 conda 사양 YAML을 정의 하는 경우 pip-기본값 버전 >= 1.0.45를 pip 종속성으로 나열 합니다. 이 패키지는 모델을 웹 서비스로 호스트 하는 데 필요 합니다.

이 시점에서 서비스를 정상적으로 사용할 수 있습니다. 다음 코드는 서비스에 데이터를 보내는 방법을 보여 줍니다.

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
> `reload` 메서드는 로컬 배포에만 사용할 수 있습니다. 다른 계산 대상으로 배포를 업데이트 하는 방법에 대 한 자세한 내용은 [webservice를 업데이트 하는 방법](how-to-deploy-update-web-service.md)을 참조 하세요.

```python
service.reload()
print(service.run(input_data=test_sample))
```

> [!NOTE]
> 스크립트는 서비스에서 사용하는 `InferenceConfig` 개체로 지정된 위치에서 다시 로드됩니다.

모델, Conda 종속성 또는 배포 구성을 변경하려면 [update()](/python/api/azureml-core/azureml.core.webservice%28class%29?preserve-view=true&view=azure-ml-py#&preserve-view=trueupdate--args-)를 사용합니다. 다음 예제에서는 서비스에서 사용하는 모델을 업데이트합니다.

```python
service.update([different_model], inference_config, deployment_config)
```

### <a name="delete-the-service"></a>서비스 삭제

서비스를 삭제하려면 [delete()](/python/api/azureml-core/azureml.core.webservice%28class%29?preserve-view=true&view=azure-ml-py#&preserve-view=truedelete--)를 사용합니다.

### <a name="inspect-the-docker-log"></a><a id="dockerlog"></a> Docker 로그 검사

서비스 개체에서 자세한 Docker 엔진 로그 메시지를 인쇄할 수 있습니다. ACI, AKS 및 로컬 배포에 대한 로그를 볼 수 있습니다. 다음 예제에서는 로그를 출력하는 방법을 보여 줍니다.

```python
# if you already have the service object handy
print(service.get_logs())

# if you only know the name of the service (note there might be multiple services with the same name but different version number)
print(ws.webservices['mysvc'].get_logs())
```
`Booting worker with pid: <pid>`로그에서 여러 번 발생 하는 줄이 표시 되 면 작업자를 시작 하는 데 충분 한 메모리가 없는 것입니다.
에서의 값을 늘려서 오류를 해결할 수 있습니다. `memory_gb``deployment_config`
 
## <a name="container-cannot-be-scheduled"></a>컨테이너를 예약할 수 없음

서비스를 Azure Kubernetes Service 컴퓨팅 대상에 배포하는 경우 Azure Machine Learning에서 서비스를 요청된 리소스의 양으로 예약하려고 합니다. 5 분 후에 적절 한 양의 리소스를 사용 하 여 클러스터에서 사용할 수 있는 노드가 없으면 배포에 실패 합니다. 실패 메시지는 `Couldn't Schedule because the kubernetes cluster didn't have available resources after trying for 00:05:00` 입니다. 노드를 더 추가 하거나, 노드의 SKU를 변경 하거나, 서비스의 리소스 요구 사항을 변경 하 여이 오류를 해결할 수 있습니다. 

오류 메시지는 일반적으로 필요한 리소스를 나타냅니다. 즉, `0/3 nodes are available: 3 Insufficient nvidia.com/gpu` 서비스에 gpu가 필요 하 고 사용 가능한 gpu가 없는 클러스터에 3 개의 노드가 있음을 나타내는 오류 메시지가 표시 됩니다. 이는 GPU SKU를 사용하는 경우 더 많은 노드를 추가하거나, 사용하지 않는 경우 GPU 지원 SKU로 전환하거나, GPU가 필요하지 않도록 환경을 변경하여 해결할 수 있습니다.  

## <a name="service-launch-fails"></a>서비스 시작 실패

이미지가 성공적으로 빌드되면 시스템에서 배포 구성을 사용하여 컨테이너를 시작하려고 합니다. 컨테이너 시작 프로세스의 일부로, 시스템에서 채점 스크립트의 `init()` 함수를 호출합니다. `init()` 함수에 catch되지 않은 예외가 있는 경우 오류 메시지에 **CrashLoopBackOff** 오류가 표시될 수 있습니다.

[Docker 로그 검사](#dockerlog) 섹션의 정보를 사용하여 로그를 확인하세요.

## <a name="function-fails-get_model_path"></a>함수 실패: get_model_path()

채점 스크립트의 `init()` 함수에서 컨테이너의 모델 파일 또는 모델 파일의 폴더를 찾기 위해 [Model.get_model_path()](/python/api/azureml-core/azureml.core.model.model?preserve-view=true&view=azure-ml-py#&preserve-view=trueget-model-path-model-name--version-none---workspace-none-) 함수가 호출되는 경우가 많습니다. 모델 파일 또는 폴더를 찾을 수 없으면 함수가 실패합니다. 이 오류를 디버그하는 가장 쉬운 방법은 컨테이너 셸에서 아래의 Python 코드를 실행하는 것입니다.

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

**참고** : `run(input_data)` 호출에서 오류 메시지를 반환하는 방법은 디버깅 용도로만 사용해야 합니다. 보안상의 이유로 프로덕션 환경에서는 오류 메시지를 이런 식으로 반환해서는 안 됩니다.

## <a name="http-status-code-502"></a>502 HTTP 상태 코드

502 상태 코드는 서비스에서 예외를 throw했거나 score.py 파일의 `run()` 메서드에서 작동을 중단했음을 나타냅니다. 이 문서의 정보를 사용하여 파일을 디버그하세요.

## <a name="http-status-code-503"></a>503 HTTP 상태 코드

Azure Kubernetes Service 배포는 자동 크기 조정을 지원하므로 추가 로드를 지원하기 위해 복제본을 추가할 수 있습니다. Autoscaler는 부하의 **점진적** 변화를 처리 하도록 설계 되었습니다. 초당 요청 수가 크게 급증하면 클라이언트에서 503 HTTP 상태 코드를 받을 수 있습니다. Autoscaler가 빠르게 반응 하더라도 추가 컨테이너를 만드는 데 상당한 시간이 소요 됩니다.

수직 확장/축소 결정은 현재 컨테이너 복제본의 사용률을 기준으로 합니다. 현재 복제본의 총 수로 나눈 사용 중인 복제본 수 (요청 처리)가 현재 사용량입니다. 이 수가 초과 `autoscale_target_utilization` 되 면 더 많은 복제본이 생성 됩니다. 이보다 낮으면 복제본이 줄어듭니다. 복제본 추가에 대 한 결정은 신속 하 고 빠르게 진행 됩니다 (약 1 초). 복제본 제거에 대 한 결정은 매우 보수적인 결정입니다 (약 1 분). 기본적으로 자동 크기 조정 대상 사용률은 **70%** 로 설정 됩니다. 즉, 서비스가 **최대 30%** 의 초당 급증 하는 요청 수 (RPS)를 처리할 수 있습니다.

503 상태 코드를 방지하는 데 도움이 되는 두 가지 방법이 있습니다.

> [!TIP]
> 이러한 두 가지 접근 방식은 개별적으로 또는 함께 사용할 수 있습니다.

* 자동 크기 조정에서 새 복제본을 만드는 사용률 수준을 변경합니다. `autoscale_target_utilization`을 더 낮은 값으로 설정하여 사용률 목표를 조정할 수 있습니다.

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

`autoscale_target_utilization`, `autoscale_max_replicas` 및 `autoscale_min_replicas` 설정에 대한 자세한 내용은 [AksWebservice](/python/api/azureml-core/azureml.core.webservice.akswebservice?preserve-view=true&view=azure-ml-py) 모듈 참조를 참조하세요.

## <a name="http-status-code-504"></a>504 HTTP 상태 코드

504 상태 코드는 요청 시간이 초과되었음을 나타냅니다. 기본 시간 제한은 1분입니다.

불필요한 호출을 제거하도록 score.py를 수정하여 시간 제한을 늘리거나 서비스 속도를 높일 수 있습니다. 이러한 작업으로도 문제가 해결되지 않으면 이 문서의 정보를 사용하여 score.py 파일을 디버그합니다. 이 코드는 응답성이 아닌 상태나 무한 루프에 있을 수 있습니다.

## <a name="advanced-debugging"></a>고급 디버깅

모델 배포에 포함된 Python 코드를 대화형으로 디버그해야 할 수도 있습니다. 예를 들어 항목 스크립트가 실패하고 추가 로깅으로 이유를 확인할 수 없는 경우입니다. Visual Studio Code 및 debugpy를 사용 하 여 Docker 컨테이너 내에서 실행 되는 코드에 연결할 수 있습니다.

자세한 내용은 [VS Code의 대화형 디버깅 가이드](how-to-debug-visual-studio-code.md#debug-and-troubleshoot-deployments)를 참조 하세요.

## <a name="model-deployment-user-forum"></a>[모델 배포 사용자 포럼](/answers/topics/azure-machine-learning-inference.html)

## <a name="next-steps"></a>다음 단계

배포에 대해 자세히 알아보세요.

* [배포 방법 및 위치](how-to-deploy-and-where.md)
* [자습서: 모델 학습 및 배포](tutorial-train-models-with-aml.md)