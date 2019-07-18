---
title: 배포 문제 해결 가이드
titleSuffix: Azure Machine Learning service
description: 해결, 해결 및 Azure Kubernetes Service 및 Azure Machine Learning 서비스를 사용 하 여 Azure Container Instances를 사용 하 여 일반적인 Docker 배포 오류 해결 방법에 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
author: chris-lauren
ms.author: clauren
ms.reviewer: jmartens
ms.date: 07/09/2018
ms.custom: seodec18
ms.openlocfilehash: e0f4b024d717c08df3514df057abf89d55be1dc9
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67707030"
---
# <a name="troubleshooting-azure-machine-learning-service-azure-kubernetes-service-and-azure-container-instances-deployment"></a>Azure Machine Learning 서비스 Azure Kubernetes Service 및 Azure Container Instances 배포 문제 해결

해결 하거나 Azure ACI (Container Instances)와 Azure Kubernetes Service (AKS) Azure Machine Learning 서비스를 사용 하 여 일반적인 Docker 배포 오류를 해결 하는 방법에 알아봅니다.

Azure Machine Learning 서비스에서 모델을 배포할 때 시스템에서 많은 작업을 수행합니다. 배포 작업은 다음과 같습니다.

1. 모델을 작업 영역 모델 레지스트리에 등록합니다.

2. 다음을 포함하여 Docker 이미지를 빌드합니다.
    1. 레지스트리에서 등록 모델을 다운로드합니다. 
    2. 환경 yaml 파일에 지정된 종속성 기반의 Python 환경을 사용하여 dockerfile을 만듭니다.
    3. dockerfile에 제공하는 모델 파일 및 채점 스크립트를 추가합니다.
    4. dockerfile을 사용하여 새 Docker 이미지를 빌드합니다.
    5. 작업 영역과 연결된 Azure Container Registry에 Docker 이미지를 등록합니다.

    > [!IMPORTANT]
    > 코드에 따라 이미지를 만드는 사용자 입력 없이 자동으로 수행 합니다.

3. ACI(Azure Container Instance) 서비스 또는 AKS(Azure Kubernetes Service)에 Docker 이미지를 배포합니다.

4. ACI 또는 AKS에서 새 컨테이너(또는 여러 개의 새 컨테이너)를 시작합니다. 

이 프로세스에 대한 자세한 정보는 [모델 관리](concept-model-management-and-deployment.md) 소개를 참조하세요.

## <a name="before-you-begin"></a>시작하기 전 주의 사항

문제가 발생할 경우 가장 먼저 할 일은 배포 작업을 개별 단계로 분리하여(이전 설명 참조) 문제를 격리하는 것입니다.

배포 작업으로 분할 하는 것은 사용 하는 경우에 유용 합니다 [Webservice.deploy()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice%28class%29?view=azure-ml-py#deploy-workspace--name--model-paths--image-config--deployment-config-none--deployment-target-none-) API, 또는 [Webservice.deploy_from_model()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice%28class%29?view=azure-ml-py#deploy-from-model-workspace--name--models--image-config--deployment-config-none--deployment-target-none-) API로 이러한 함수는 모두 앞에서 언급 한 단계를 수행는 단일 동작입니다. 일반적으로 이러한 Api는 편리 하지만 단계를 사용 하 여 대체 하 여 문제를 해결할 때 중단 하는 것은 API 호출 아래.

1. 모델을 등록합니다. 다음은 샘플 코드입니다.

    ```python
    # register a model out of a run record
    model = best_run.register_model(model_name='my_best_model', model_path='outputs/my_model.pkl')

    # or, you can register a file or a folder of files as a model
    model = Model.register(model_path='my_model.pkl', model_name='my_best_model', workspace=ws)
    ```

2. 이미지를 빌드합니다. 다음은 샘플 코드입니다.

    ```python
    # configure the image
    image_config = ContainerImage.image_configuration(runtime="python",
                                                      entry_script="score.py",
                                                      conda_file="myenv.yml")

    # create the image
    image = Image.create(name='myimg', models=[model], image_config=image_config, workspace=ws)

    # wait for image creation to finish
    image.wait_for_creation(show_output=True)
    ```

3. 이미지를 서비스로 배포합니다. 다음은 샘플 코드입니다.

    ```python
    # configure an ACI-based deployment
    aci_config = AciWebservice.deploy_configuration(cpu_cores=1, memory_gb=1)

    aci_service = Webservice.deploy_from_image(deployment_config=aci_config, 
                                               image=image, 
                                               name='mysvc', 
                                               workspace=ws)
    aci_service.wait_for_deployment(show_output=True)    
    ```

배포 프로세스를 개별 작업으로 분리한 후에는 가장 일반적인 오류 중 일부를 볼 수 있습니다.

## <a name="image-building-fails"></a>이미지 빌드 실패

Docker 이미지를 빌드할 수 없습니다, 하는 경우는 [image.wait_for_creation()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.image.image(class)?view=azure-ml-py#wait-for-creation-show-output-false-) 하거나 [service.wait_for_deployment()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice(class)?view=azure-ml-py#wait-for-deployment-show-output-false-) 호출이 실패 하 고 몇 가지 단서를 제공할 수 있는 일부 오류 메시지입니다. 또한 이미지 빌드 로그에서 오류에 대한 자세한 내용을 찾을 수 있습니다. 아래는 이미지 빌드 로그 uri를 검색하는 방법을 보여주는 샘플 코드입니다.

```python
# if you already have the image object handy
print(image.image_build_log_uri)

# if you only know the name of the image (note there might be multiple images with the same name but different version number)
print(ws.images['myimg'].image_build_log_uri)

# list logs for all images in the workspace
for name, img in ws.images.items():
    print (img.name, img.version, img.image_build_log_uri)
```

이미지 로그 uri는 Azure Blob Storage에 저장된 로그 파일을 가리키는 SAS URL입니다. 간단하게 uri를 복사하여 브라우저 창에 붙여넣는 방법으로 로그 파일을 다운로드하여 볼 수 있습니다.

### <a name="azure-key-vault-access-policy-and-azure-resource-manager-templates"></a>Azure Key Vault 액세스 정책 및 Azure Resource Manager 템플릿

이미지 빌드도 Azure Key vault 액세스 정책 관련 문제로 인해 실패할 수 있습니다. Azure Resource Manager 템플릿을 사용 하 여 연결 된 리소스 (Azure Key Vault를 포함)을 여러 번 확인 하 고 작업 영역을 만들려면이 상황이 발생할 수 있습니다. 예를 들어, 템플릿을 사용 하 여를 여러 번 연속 통합 및 배포 파이프라인의 일부로 동일한 매개 변수를 사용 하 여 합니다.

템플릿을 통해 대부분의 리소스 생성 작업은 idempotent 상태, 되지만 키 자격 증명 모음 액세스 정책 템플릿이 사용 될 때마다를 지웁니다. 사용 하는 모든 기존 작업 영역에 대 한 Key Vault에 대 한 액세스 정책을 나누기 액세스의 선택을 취소 합니다. 이 조건은 새 이미지를 만들려고 할 때 오류가 발생 합니다. 다음은 받을 수 있는 오류의 예입니다.

__포털__:
```text
Create image "myimage": An internal server error occurred. Please try again. If the problem persists, contact support.
```

__SDK__:
```python
image = ContainerImage.create(name = "myimage", models = [model], image_config = image_config, workspace = ws)
Creating image
Traceback (most recent call last):
  File "C:\Python37\lib\site-packages\azureml\core\image\image.py", line 341, in create
    resp.raise_for_status()
  File "C:\Python37\lib\site-packages\requests\models.py", line 940, in raise_for_status
    raise HTTPError(http_error_msg, response=self)
requests.exceptions.HTTPError: 500 Server Error: Internal Server Error for url: https://eastus.modelmanagement.azureml.net/api/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.MachineLearningServices/workspaces/<workspace-name>/images?api-version=2018-11-19

Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
  File "C:\Python37\lib\site-packages\azureml\core\image\image.py", line 346, in create
    'Content: {}'.format(resp.status_code, resp.headers, resp.content))
azureml.exceptions._azureml_exception.WebserviceException: Received bad response from Model Management Service:
Response Code: 500
Headers: {'Date': 'Tue, 26 Feb 2019 17:47:53 GMT', 'Content-Type': 'application/json', 'Transfer-Encoding': 'chunked', 'Connection': 'keep-alive', 'api-supported-versions': '2018-03-01-preview, 2018-11-19', 'x-ms-client-request-id': '3cdcf791f1214b9cbac93076ebfb5167', 'x-ms-client-session-id': '', 'Strict-Transport-Security': 'max-age=15724800; includeSubDomains; preload'}
Content: b'{"code":"InternalServerError","statusCode":500,"message":"An internal server error occurred. Please try again. If the problem persists, contact support"}'
```

__CLI__:
```text
ERROR: {'Azure-cli-ml Version': None, 'Error': WebserviceException('Received bad response from Model Management Service:\nResponse Code: 500\nHeaders: {\'Date\': \'Tue, 26 Feb 2019 17:34:05
GMT\', \'Content-Type\': \'application/json\', \'Transfer-Encoding\': \'chunked\', \'Connection\': \'keep-alive\', \'api-supported-versions\': \'2018-03-01-preview, 2018-11-19\', \'x-ms-client-request-id\':
\'bc89430916164412abe3d82acb1d1109\', \'x-ms-client-session-id\': \'\', \'Strict-Transport-Security\': \'max-age=15724800; includeSubDomains; preload\'}\nContent:
b\'{"code":"InternalServerError","statusCode":500,"message":"An internal server error occurred. Please try again. If the problem persists, contact support"}\'',)}
```

이 문제를 방지 하려면 좋습니다 다음 방법 중 하나:

* 동일한 매개 변수에 대 한 템플릿을 두 번 이상는 배포 하지 않습니다. 또는 기존 리소스를 다시 만드는 데 템플릿을 사용 하기 전에 삭제 합니다.
* 키 자격 증명 모음 액세스 정책을 검토 하 고 그런 다음 이러한 정책을 사용 하 여 설정 된 `accessPolicies` 템플릿의 속성입니다.
* Key Vault 리소스 이미 있는지 확인 합니다. 이 템플릿을 통해 다시 만들지 않습니다. 예를 들어, 이미 있는 경우 키 자격 증명 모음 리소스의 생성을 사용 하지 않도록 할 수 있도록 매개 변수를 추가 합니다.

## <a name="debug-locally"></a>로컬로 디버그

그래도 ACI AKS에 모델을 배포 하는 문제가 발생 하면 로컬 웹 서비스로 배포 합니다. 로컬 웹 서비스를 사용 하 여 쉽게 문제를 해결 합니다. 모델이 포함 된 Docker 이미지 다운로드 되어 로컬 시스템에서 시작 됩니다.

> [!IMPORTANT]
> 로컬 웹 서비스 배포에는 작동 하는 로컬 시스템에 설치 된 Docker 필요합니다. Docker는 로컬 웹 서비스를 배포 하기 전에 실행 되어야 합니다. 설치 하 고 Docker를 사용 하 여에 대 한 내용은 참조 하세요 [ https://www.docker.com/ ](https://www.docker.com/)합니다.

> [!WARNING]
> 로컬 웹 서비스 배포는 프로덕션 시나리오에 대 한 지원 되지 않습니다.

로컬로 배포를 사용 하도록 코드를 수정할 `LocalWebservice.deploy_configuration()` 배포 구성을 만들 수 있습니다. 사용 하 여 `Model.deploy()` 서비스를 배포 합니다. 다음 예제에서는 배포 모델 (에 포함 된를 `model` 변수) 로컬 웹 서비스:

```python
from azureml.core.model import InferenceConfig,Model
from azureml.core.webservice import LocalWebservice

# Create inference configuration. This creates a docker image that contains the model.
inference_config = InferenceConfig(runtime= "python", 
                                   entry_script="score.py",
                                   conda_file="myenv.yml")

# Create a local deployment, using port 8890 for the web service endpoint
deployment_config = LocalWebservice.deploy_configuration(port=8890)
# Deploy the service
service = Model.deploy(ws, "mymodel", [model], inference_config, deployment_config)
# Wait for the deployment to complete
service.wait_for_deployment(True)
# Display the port that the web service is available on
print(service.port)
```

이 시점에서 정상적으로 서비스를 사용 하 여 작업할 수 있습니다. 예를 들어, 다음 코드를 서비스에 데이터를 보내는 방법을 보여 줍니다.

```python
import json

test_sample = json.dumps({'data': [
    [1,2,3,4,5,6,7,8,9,10], 
    [10,9,8,7,6,5,4,3,2,1]
]})

test_sample = bytes(test_sample,encoding = 'utf8')

prediction = service.run(input_data=test_sample)
print(prediction)
```

### <a name="update-the-service"></a>서비스 업데이트

로컬 테스트 하는 동안 업데이트 해야 합니다 `score.py` 파일 로깅 추가 또는 발견 된 문제를 해결 하려고 합니다. 변경 내용을 다시 로드 합니다 `score.py` 파일을 사용 하 여 `reload()`입니다. 예를 들어, 다음 코드는 서비스에 대 한 스크립트를 다시 로드 하 고에 데이터를 보냅니다. 데이터는 업데이트를 사용 하 여 점수가 매겨진 `score.py` 파일:

```python
service.reload()
print(service.run(input_data=test_sample))
```

> [!NOTE]
> 지정 된 위치에서 스크립트를 다시 로드를 `InferenceConfig` 서비스에서 사용 되는 개체입니다.

모델, Conda 종속성 또는 배포 구성을 변경 하려면 [update ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice%28class%29?view=azure-ml-py#update--args-)합니다. 다음 예제에서는 서비스에서 사용 되는 모델을 업데이트 합니다.

```python
service.update([different_model], inference_config, deployment_config)
```

### <a name="delete-the-service"></a>서비스 삭제

서비스를 삭제 하려면 [delete ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice%28class%29?view=azure-ml-py#delete--)합니다.

### <a id="dockerlog"></a> Docker 로그를 검사 합니다.

서비스 개체에서 자세한 Docker 엔진 로그 메시지를 인쇄할 수 있습니다. ACI, AKS, 및 로컬 배포에 대 한 로그를 볼 수 있습니다. 다음 예제에서는 로그를 인쇄 하는 방법에 설명 합니다.

```python
# if you already have the service object handy
print(service.get_logs())

# if you only know the name of the service (note there might be multiple services with the same name but different version number)
print(ws.webservices['mysvc'].get_logs())
```

## <a name="service-launch-fails"></a>서비스 시작 실패

이미지는 성공적으로 빌드되면 시스템 배포 구성을 사용 하 여 컨테이너 시작을 시도 합니다. 컨테이너 시작 프로세스의 일부로, 시스템에서 채점 스크립트의 `init()` 함수를 호출합니다. `init()` 함수에 catch되지 않은 예외가 있는 경우 오류 메시지에 **CrashLoopBackOff** 오류가 표시될 수 있습니다.

정보를 사용 합니다 [Docker 로그를 검사](#dockerlog) 로그를 확인 하는 섹션입니다.

## <a name="function-fails-getmodelpath"></a>함수 실패: get_model_path()

종종 합니다 `init()` 점수 매기기 스크립트에서 함수 [Model.get_model_path()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#get-model-path-model-name--version-none---workspace-none-) 함수가 호출 되어 컨테이너에는 모델 또는 모델 파일의 폴더를 찾습니다. 모델 파일 또는 폴더를 찾을 수 없는 경우 함수 실패 합니다. 이 오류를 디버그하는 가장 쉬운 방법은 컨테이너 셸에서 아래의 Python 코드를 실행하는 것입니다.

```python
import logging
logging.basicConfig(level=logging.DEBUG)
from azureml.core.model import Model
print(Model.get_model_path(model_name='my-best-model'))
```

이 예제에서는 로컬 경로 출력 (상대적인 `/var/azureml-app`) 점수 매기기 스크립트는 모델 파일 또는 폴더 찾기 하는데 여기서 컨테이너에 있습니다. 그러면 예상 위치에 파일 또는 폴더가 실제로 있는지 확인할 수 있습니다.

디버그 하는 로깅 수준을 설정 하면 오류를 식별 하는 데 유용할 수 있는 추가 정보를 로그에 기록 발생할 수 있습니다.

## <a name="function-fails-runinputdata"></a>함수 실패: run(input_data)

서비스가 성공적으로 배포되었지만 채점 엔드포인트에 데이터를 게시할 때 크래시가 발생하는 경우 오류를 catch하는 명령문을 `run(input_data)` 함수에 추가하면 구체적인 오류 메시지가 반환됩니다. 예:

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

**참고**: `run(input_data)` 호출에서 오류 메시지를 반환하는 방법은 디버깅 용도로만 사용해야 합니다. 보안상의 이유로 하지 반환 해야 오류 메시지 이러한 방식으로 프로덕션 환경에서.

## <a name="http-status-code-503"></a>HTTP 상태 코드 503

Azure Kubernetes Service 배포에 복제본 추가 부하를 지원 하기 위해 추가할 수 있는 자동 크기 조정을 지원 합니다. 그러나 autoscaler 처리 하도록 설계 되었습니다 **점진적** 부하 변경을 합니다. 초당 요청에서 큰 스파이크를 수신 하는 경우 클라이언트의 HTTP 상태 코드 503 나타날 수 있습니다.

503 상태 코드를 방지 하는 데 도움이 되는 두 가지 있습니다.

* 변경의 사용률 수준은 자동 크기 조정은 새 복제본을 만듭니다.
    
    기본적으로 자동 크기 조정 목표 사용률 설정 70%, 즉, 서비스 요청 RPS (초당) 최대 30%의 급증을 처리할 수 있도록 합니다. 설정 하 여 사용률 목표를 조정할 수 있습니다는 `autoscale_target_utilization` 더 낮은 값입니다.

    > [!IMPORTANT]
    > 이 변경에 만들려는 복제본 이어지지 *빠르게*합니다. 대신, 낮은 사용률 임계값 생성 됩니다. 서비스를 활용 하는 70%가 될 때까지 대기 하는 대신 값을 30%로 변경 하면 30% 사용률 발생할 때 만들어지는 복제본입니다.
    
    웹 서비스에서 이미 현재 최대 복제본을 사용 하 고 상태 코드 503 계속 표시 되는 경우 향상 된 `autoscale_max_replicas` 복제본의 최대 수를 늘리려면 값입니다.

* 복제본의 최소 수를 변경 합니다. 최소 복제본 증가 들어오는 급증을 처리 하려면 더 큰 풀을 제공 합니다.

    복제본의 최소 수를 늘리려면 설정 `autoscale_min_replicas` 을 더 높은 값입니다. 다음 코드를 사용 하 여 프로젝트에 특정 값을 사용 하 여 값을 바꿀 필요한 복제본 계산할 수 있습니다.

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
    > 요청 급증을 처리할 수 있는 새 최소 복제본 보다 더 큰 경우에 따라 503s 다시 나타날 수 있습니다. 예를 들어,에 서비스 증가 트래픽으로 최소 복제본을 높이기 위해 해야 할 수 있습니다.

설정에 대 한 자세한 내용은 `autoscale_target_utilization`, `autoscale_max_replicas`, 및 `autoscale_min_replicas` 를 참조 합니다 [AksWebservice](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.akswebservice?view=azure-ml-py) 모듈 참조.


## <a name="advanced-debugging"></a>고급 디버깅

경우에 따라 모델 배포에 포함 된 Python 코드를 대화형으로 디버깅 하는 것이 해야 합니다. 예를 들어, 엔트리 스크립트가 실패 하는 경우 추가 로그인 하 여 이유를 확인할 수 없습니다. 를 사용 하 여 Visual Studio Code 및 Python Tools Visual Studio (PTVSD)에 대 한 Docker 컨테이너에서 실행 되는 코드에 연결할 수 있습니다.

> [!IMPORTANT]
> 이러한 방식의 디버깅에 사용 하는 경우 작동 하지 않습니다 `Model.deploy()` 및 `LocalWebservice.deploy_configuration` 로컬로 모델을 배포 합니다. 사용 하 여 이미지를 만들어야 하는 대신 합니다 [ContainerImage](https://docs.microsoft.com/python/api/azureml-core/azureml.core.image.containerimage?view=azure-ml-py) 클래스입니다. 
>
> 로컬 웹 서비스 배포에는 작동 하는 로컬 시스템에 설치 된 Docker 필요합니다. Docker는 로컬 웹 서비스를 배포 하기 전에 실행 되어야 합니다. 설치 하 고 Docker를 사용 하 여에 대 한 내용은 참조 하세요 [ https://www.docker.com/ ](https://www.docker.com/)합니다.

### <a name="configure-development-environment"></a>개발 환경 구성

1. Python 도구를 설치 하려면 Visual Studio (PTVSD)에 대 한 로컬 VS Code 개발 환경에서 다음 명령을 사용 합니다.

    ```
    python -m pip install --upgrade ptvsd
    ```

    PTVSD를 사용 하 여 VS Code를 사용 하는 방법은 참조 하세요 [원격 디버깅](https://code.visualstudio.com/docs/python/debugging#_remote-debugging)합니다.

1. VS Code Docker 이미지를 사용 하 여 통신을 구성 하려면 새 디버그 구성을 만듭니다.

    1. VS Code에서 선택 합니다 __디버그__ 메뉴를 선택 합니다 __구성을 열고__합니다. 라는 파일로 __launch.json__ 열립니다.

    1. 에 __launch.json__ 파일을 포함 하는 줄을 찾습니다 `"configurations": [`, 그 후 다음 텍스트를 삽입 하 고:

        ```json
        {
            "name": "Azure Machine Learning service: Docker Debug",
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
        > 항목이 있는 경우 이미 다른 구성 섹션에서을 삽입 하는 코드 뒤 쉼표 (,)를 추가 합니다.

        이 섹션에서는 포트 5678를 사용 하 여 Docker 컨테이너에 연결 합니다.

    1. 저장 된 __launch.json__ 파일입니다.

### <a name="create-an-image-that-includes-ptvsd"></a>PTVSD를 포함 하는 이미지 만들기

1. PTVSD 포함 하도록 배포를 위한 conda 환경을 수정 합니다. 다음 예제에서는 사용 하 여 추가 된 `pip_packages` 매개 변수:

    ```python
    from azureml.core.conda_dependencies import CondaDependencies 
    
    # Usually a good idea to choose specific version numbers
    # so training is made on same packages as scoring
    myenv = CondaDependencies.create(conda_packages=['numpy==1.15.4',            
                                'scikit-learn==0.19.1', 'pandas==0.23.4'],
                                 pip_packages = ['azureml-defaults==1.0.17', 'ptvsd'])
    
    with open("myenv.yml","w") as f:
        f.write(myenv.serialize_to_string())
    ```

1. PTVSD를 시작 하 고 서비스를 시작할 때 연결을 대기 합니다 하려면의 맨 위에 다음을 추가 하면 `score.py` 파일:

    ```python
    import ptvsd
    # Allows other computers to attach to ptvsd on this IP address and port.
    ptvsd.enable_attach(address=('0.0.0.0', 5678), redirect_output = True)
    # Wait 30 seconds for a debugger to attach. If none attaches, the script continues as normal.
    ptvsd.wait_for_attach(timeout = 30)
    print("Debugger attached...")
    ```

1. 디버그 하는 동안 다시 하지 않고도 이미지의 파일에 변경 하는 것이 좋습니다. 텍스트 편집기 (vim) Docker 이미지를 설치 하려면 라는 새 텍스트 파일을 만듭니다 `Dockerfile.steps` 파일의 내용으로 다음을 사용 합니다.

    ```text
    RUN apt-get update && apt-get -y install vim
    ```

    텍스트 편집기를 사용 하면 새 이미지를 만들지 않고 변경 내용을 테스트 하려면 docker 이미지 내부에 있는 파일을 수정할 수 있습니다.

1. 사용 하는 이미지를 만드는 합니다 `Dockerfile.steps` 파일을 사용 하 여는 `docker_file` 이미지를 만들 때 매개 변수입니다. 다음 예제에서는이 작업을 수행 하는 방법에 설명 합니다.

    > [!NOTE]
    > 이 예에서는 가정 `ws` Azure Machine Learning 작업 영역에 있고 해당 지점 `model` 배포 모델입니다. `myenv.yml` 파일 1 단계에서 만든 conda 종속성을 포함 합니다.

    ```python
    from azureml.core.image import Image, ContainerImage
    image_config = ContainerImage.image_configuration(runtime= "python",
                                 execution_script="score.py",
                                 conda_file="myenv.yml",
                                 docker_file="Dockerfile.steps")

    image = Image.create(name = "myimage",
                     models = [model],
                     image_config = image_config, 
                     workspace = ws)
    # Print the location of the image in the repository
    print(image.image_location)
    ```

이미지를 만든 후에 레지스트리에 이미지 위치가 표시 됩니다. 위치는 다음 텍스트와 비슷합니다.

```text
myregistry.azurecr.io/myimage:1
```

이 텍스트 예제의 레지스트리 이름은 `myregistry` 이미지는 이름이 `myimage`합니다. 이미지 버전은 `1`합니다.

### <a name="download-the-image"></a>이미지를 다운로드 합니다.

1. 명령 프롬프트, 터미널 또는 다른 셸을 열고 다음을 사용 하 여 [Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest) Azure Machine Learning 작업 영역을 포함 하는 Azure 구독에 인증 하는 명령:

    ```azurecli
    az login
    ```

1. 에 컨테이너 레지스트리 ACR (Azure) 이미지를 포함 하는 인증을 위해 다음 명령을 사용 합니다. 대체 `myregistry` 이미지를 등록 한 경우에 반환 됩니다.

    ```azurecli
    az acr login --name myregistry
    ```

1. 로컬 docker 이미지를 다운로드 하려면 다음 명령을 사용 합니다. 대체 `myimagepath` 반환 위치를 사용 하 여 이미지를 등록 합니다.

    ```bash
    docker pull myimagepath
    ```

    이미지 경로 유사 해야 합니다. `myregistry.azurecr.io/myimage:1`합니다. 여기서 `myregistry` 레지스트리에 `myimage` 이미지에는 및 `1` 이미지 버전입니다.

    > [!TIP]
    > 이전 단계에서 인증 영원히 지속 되지 않습니다. 인증 명령 및 끌어오기 명령 간에 충분 한 시간 동안 대기 하는 인증 실패를 받게 됩니다. 이 경우 다시 인증 합니다.

    다운로드를 완료 하는 데 걸리는 시간에 인터넷 연결 속도에 따라 달라 집니다. 프로세스 중에 다운로드 상태가 표시 됩니다. 다운로드가 완료 되 면 사용할 수는 `docker images` 에 다운로드 되었는지 확인 하려면 명령입니다.

1. 이미지를 사용 하 여 작업을 쉽게 태그를 추가 하려면 다음 명령을 사용 합니다. 대체 `myimagepath` 2 단계의 위치 값입니다.

    ```bash
    docker tag myimagepath debug:1
    ```

    나머지 단계에 대 한 로컬 이미지를 참조할 수 있습니다 `debug:1` 전체 이미지 경로 값을 대신 합니다.

### <a name="debug-the-service"></a>서비스 디버그

> [!TIP]
> PTVSD 연결 제한 시간을 설정할 경우의 `score.py` 파일인 디버그 세션 제한 시간이 만료 되기 전에 VS Code를 연결 해야 합니다. VS Code를 시작, 로컬 복사본을 열고 `score.py`중단점을 설정 하 고이 섹션의 단계를 사용 하기 전에 진행할 준비가 되 게 합니다.
>
> 디버깅 중단점을 설정에 대 한 자세한 내용은 참조 하세요. [디버깅](https://code.visualstudio.com/Docs/editor/debugging)합니다.

1. 이미지를 사용 하 여 Docker 컨테이너를 시작 하려면 다음 명령을 사용 합니다.

    ```bash
    docker run --rm --name debug -p 8000:5001 -p 5678:5678 debug:1
    ```

1. VS Code에 연결 하려면 PTVSD 컨테이너 내에서 VS Code 열고 F5 키 또는 선택 사용 __디버그__합니다. 메시지가 표시 되 면 선택 된 __Azure Machine Learning 서비스: Docker 디버그__ 구성 합니다. 세로 막대에서 디버그 아이콘을 선택할 수도 있습니다는 __Azure Machine Learning 서비스: Docker 디버그__ 디버그 드롭다운 메뉴를 사용 하 여 디버거를 연결 하려면 녹색 화살표는 항목입니다.

    ![디버그 아이콘, 시작 디버깅 단추 및 구성 선택기](media/how-to-troubleshoot-deployment/start-debugging.png)

이 시점에서 VS Code Docker 컨테이너에서 PTVSD에 연결을 이전에 설정한 중단점에서 중지 됩니다. 실행 될 때 이제 코드를 단계별로 실행할 수 변수 등을 확인 합니다.

VS Code를 사용 하 여 Python 디버깅에 대 한 자세한 내용은 참조 하세요. [Python 코드 디버그](https://docs.microsoft.com/visualstudio/python/debugging-python-in-visual-studio?view=vs-2019)합니다.

<a id="editfiles"></a>
### <a name="modify-the-container-files"></a>컨테이너 파일을 수정

이미지에서 파일을 변경 하려면 실행 중인 컨테이너에 연결할 수 있으며 bash 셸 실행. 여기에서 파일을 편집 하려면 vim을 사용할 수 있습니다.

1. 실행 중인 컨테이너에 연결 하 고 컨테이너에서 bash 셸을 시작 하려면 다음 명령을 사용 합니다.

    ```bash
    docker exec -it debug /bin/bash
    ```

1. 서비스에서 사용 되는 파일을 찾으려면 컨테이너에서 bash 셸에서 다음 명령을 사용 합니다.

    ```bash
    cd /var/azureml-app
    ```

    여기에서 편집할 vim을 사용할 수 있습니다는 `score.py` 파일입니다. Vim을 사용 하 여 자세한 내용은 [Vim 편집기를 사용 하 여](https://www.tldp.org/LDP/intro-linux/html/sect_06_02.html)입니다.

1. 컨테이너에 대 한 변경 내용은 정상적으로 유지 되지 않습니다. 셸을 종료 하기 전에 다음 명령을 사용 하 여 변경한 내용을 저장 하려면 위의 단계에서 시작 (즉, 다른 셸에서):

    ```bash
    docker commit debug debug:2
    ```

    이 명령은 명명 된 새 이미지를 만듭니다 `debug:2` 편집 내용을 포함 하는 합니다.

    > [!TIP]
    > 현재 컨테이너를 중지 하 고 변경 내용을 적용 하려면 새 버전을 사용 하 여 시작 해야 합니다.

1. 하는 컨테이너에서 파일 동기화 VS Code를 사용 하는 로컬 파일을 사용 하 여 변경 내용을 유지 해야 합니다. 그렇지 않으면 디버거 환경이 예상 대로 작동 하지 않습니다.

### <a name="stop-the-container"></a>컨테이너 중지

컨테이너를 중지 하려면 다음 명령을 사용 합니다.

```bash
docker stop debug
```

## <a name="next-steps"></a>다음 단계

배포에 대해 자세히 알아보세요.

* [배포 방법 및 위치](how-to-deploy-and-where.md)
* [자습서: 모델 학습 및 배포](tutorial-train-models-with-aml.md)
