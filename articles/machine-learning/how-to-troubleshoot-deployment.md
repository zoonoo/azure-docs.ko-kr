---
title: 배포 문제 해결 가이드
titleSuffix: Azure Machine Learning
description: Azure Kubernetes Service 및 Azure Machine Learning를 사용 하 여 Azure Container Instances 일반적인 Docker 배포 오류를 해결 하 고 해결 하 고 해결 하는 방법을 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
author: chris-lauren
ms.author: clauren
ms.reviewer: jmartens
ms.date: 10/25/2019
ms.custom: seodec18
ms.openlocfilehash: 5d6f94d9b454ee0144ef6d495b164686014952e5
ms.sourcegitcommit: ce4a99b493f8cf2d2fd4e29d9ba92f5f942a754c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/28/2019
ms.locfileid: "75539100"
---
# <a name="troubleshooting-azure-machine-learning-azure-kubernetes-service-and-azure-container-instances-deployment"></a>Azure Kubernetes Service 및 Azure Container Instances 배포 Azure Machine Learning 문제 해결

Azure Machine Learning를 사용 하 여 Azure Container Instances (ACI) 및 Azure Kubernetes 서비스 (AKS)를 통해 일반적인 Docker 배포 오류를 해결 하거나 해결 하는 방법에 대해 알아봅니다.

Azure Machine Learning에서 모델을 배포 하는 경우 시스템에서 많은 작업을 수행 합니다. 배포 작업은 다음과 같습니다.

1. 모델을 작업 영역 모델 레지스트리에 등록합니다.

2. 다음을 포함하여 Docker 이미지를 빌드합니다.
    1. 레지스트리에서 등록 모델을 다운로드합니다. 
    2. 환경 yaml 파일에 지정된 종속성 기반의 Python 환경을 사용하여 dockerfile을 만듭니다.
    3. dockerfile에 제공하는 모델 파일 및 채점 스크립트를 추가합니다.
    4. dockerfile을 사용하여 새 Docker 이미지를 빌드합니다.
    5. 작업 영역과 연결된 Azure Container Registry에 Docker 이미지를 등록합니다.

    > [!IMPORTANT]
    > 코드에 따라 사용자 입력 없이 이미지 생성이 자동으로 수행 됩니다.

3. ACI(Azure Container Instance) 서비스 또는 AKS(Azure Kubernetes Service)에 Docker 이미지를 배포합니다.

4. ACI 또는 AKS에서 새 컨테이너(또는 여러 개의 새 컨테이너)를 시작합니다. 

이 프로세스에 대한 자세한 정보는 [모델 관리](concept-model-management-and-deployment.md) 소개를 참조하세요.

## <a name="prerequisites"></a>필수 조건

* **Azure 구독**. 없는 경우 [무료 또는 유료 버전의 Azure Machine Learning](https://aka.ms/AMLFree)을 사용해 보세요.
* [AZURE MACHINE LEARNING SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py)입니다.
* [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)
* [Azure Machine Learning에 대 한 CLI 확장](reference-azure-machine-learning-cli.md)입니다.
* 로컬로 디버깅 하려면 로컬 시스템에서 작동 하는 Docker가 설치 되어 있어야 합니다.

    Docker 설치를 확인 하려면 터미널 또는 명령 프롬프트에서 `docker run hello-world` 명령을 사용 합니다. Docker를 설치 하거나 Docker 오류 문제를 해결 하는 방법에 대 한 자세한 내용은 [Docker 설명서](https://docs.docker.com/)를 참조 하세요.

## <a name="before-you-begin"></a>시작하기 전에

문제가 발생할 경우 가장 먼저 할 일은 배포 작업을 개별 단계로 분리하여(이전 설명 참조) 문제를 격리하는 것입니다.

[Webservice.deploy()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice%28class%29?view=azure-ml-py#deploy-workspace--name--model-paths--image-config--deployment-config-none--deployment-target-none--overwrite-false-) API, 및 [Deploy_from_model()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice%28class%29?view=azure-ml-py#deploy-from-model-workspace--name--models--image-config--deployment-config-none--deployment-target-none--overwrite-false-) api를 사용 하는 경우이 두 함수 는 위의 단계를 단일 작업으로 수행 하므로 배포를 작업으로 나누는 것이 유용 합니다. 일반적으로 이러한 Api는 편리 하지만 아래 API 호출로 바꿔서 문제를 해결 하는 데 도움이 됩니다.

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

Docker 이미지를 빌드할 수 없는 경우 [wait_for_creation ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.image.image(class)?view=azure-ml-py#wait-for-creation-show-output-false-) 또는 [서비스 wait_for_deployment ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice(class)?view=azure-ml-py#wait-for-deployment-show-output-false-) 호출은 몇 가지 오류 메시지와 함께 실패 하 고 일부 단서를 제공할 수 있습니다. 또한 이미지 빌드 로그에서 오류에 대한 자세한 내용을 찾을 수 있습니다. 아래는 이미지 빌드 로그 uri를 검색하는 방법을 보여주는 샘플 코드입니다.

```python
# if you already have the image object handy
print(image.image_build_log_uri)

# if you only know the name of the image (note there might be multiple images with the same name but different version number)
print(ws.images['myimg'].image_build_log_uri)

# list logs for all images in the workspace
for name, img in ws.images.items():
    print(img.name, img.version, img.image_build_log_uri)
```

이미지 로그 uri는 Azure Blob Storage에 저장된 로그 파일을 가리키는 SAS URL입니다. 간단하게 uri를 복사하여 브라우저 창에 붙여넣는 방법으로 로그 파일을 다운로드하여 볼 수 있습니다.

### <a name="azure-key-vault-access-policy-and-azure-resource-manager-templates"></a>Azure Key Vault 액세스 정책 및 Azure Resource Manager 템플릿

Azure Key Vault에 대 한 액세스 정책 문제로 인해 이미지 빌드가 실패할 수도 있습니다. Azure Resource Manager 템플릿을 사용 하 여 작업 영역 및 연결 된 리소스 (Azure Key Vault 포함)를 여러 번 만드는 경우 이런 상황이 발생할 수 있습니다. 예를 들어 연속 통합 및 배포 파이프라인의 일부와 동일한 매개 변수를 사용 하 여 템플릿을 여러 번 사용 합니다.

템플릿을 통한 대부분의 리소스 생성 작업은 idempotent 템플릿이 사용 될 때마다 액세스 정책을 지우는 Key Vault. 액세스 정책을 지우면 사용 중인 기존 작업 영역에 대 한 Key Vault 액세스가 중단 됩니다. 이 조건으로 인해 새 이미지를 만들려고 하면 오류가 발생 합니다. 다음은 수신할 수 있는 오류의 예입니다.

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

이 문제를 방지 하려면 다음 방법 중 하나를 사용 하는 것이 좋습니다.

* 동일한 매개 변수에 템플릿을 두 번 이상 배포 하지 마십시오. 또는 템플릿을 사용 하 여 기존 리소스를 다시 만들기 전에 삭제 합니다.
* Key Vault 액세스 정책을 검토 한 후 이러한 정책을 사용 하 여 템플릿의 `accessPolicies` 속성을 설정 합니다.
* Key Vault 리소스가 이미 있는지 확인 합니다. 이 경우 템플릿을 통해 다시 만들지 마세요. 예를 들어 이미 있는 경우 Key Vault 리소스 생성을 사용 하지 않도록 설정할 수 있는 매개 변수를 추가 합니다.

## <a name="debug-locally"></a>로컬에서 디버그

ACI 또는 AKS에 모델을 배포 하는 데 문제가 발생 하는 경우 로컬 웹 서비스로 배포 해 보세요. 로컬 웹 서비스를 사용 하면 문제를 보다 쉽게 해결할 수 있습니다. 모델을 포함 하는 Docker 이미지가 로컬 시스템에서 다운로드 되 고 시작 됩니다.

> [!WARNING]
> 로컬 웹 서비스 배포는 프로덕션 시나리오에 대해 지원 되지 않습니다.

로컬로 배포 하려면 `LocalWebservice.deploy_configuration()`를 사용 하 여 배포 구성을 만들 수 있도록 코드를 수정 합니다. 그런 다음 `Model.deploy()`를 사용 하 여 서비스를 배포 합니다. 다음 예에서는 모델 (`model` 변수에 포함)을 로컬 웹 서비스로 배포 합니다.

```python
from azureml.core.model import InferenceConfig, Model
from azureml.core.environment import Environment
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

사용자 고유의 conda 사양 YAML을 정의 하는 경우 pip 종속성으로 버전 > = 1.0.45이 있는 azureml 기본값을 나열 해야 합니다. 이 패키지에는 웹 서비스로 모델을 호스트 하는 데 필요한 기능이 포함 되어 있습니다.

이 시점에서 서비스를 정상적으로 사용할 수 있습니다. 예를 들어 다음 코드는 서비스에 데이터를 보내는 방법을 보여 줍니다.

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

### <a name="update-the-service"></a>서비스 업데이트

로컬 테스트 중에 `score.py` 파일을 업데이트 하 여 로깅을 추가 하거나 검색 한 문제를 해결 해 보아야 할 수 있습니다. `score.py` 파일에 대 한 변경 내용을 다시 로드 하려면 `reload()`를 사용 합니다. 예를 들어 다음 코드는 서비스에 대 한 스크립트를 다시 로드 한 다음 데이터를 보냅니다. 업데이트 된 `score.py` 파일을 사용 하 여 데이터 점수가 매겨집니다.

> [!IMPORTANT]
> `reload` 메서드는 로컬 배포에만 사용할 수 있습니다. 다른 계산 대상으로 배포를 업데이트 하는 방법에 대 한 자세한 내용은 [모델 배포](how-to-deploy-and-where.md#update)의 업데이트 섹션을 참조 하십시오.

```python
service.reload()
print(service.run(input_data=test_sample))
```

> [!NOTE]
> 스크립트가 서비스에서 사용 하는 `InferenceConfig` 개체로 지정 된 위치에서 다시 로드 됩니다.

모델, Conda 종속성 또는 배포 구성을 변경 하려면 [update ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice%28class%29?view=azure-ml-py#update--args-)를 사용 합니다. 다음 예에서는 서비스에서 사용 하는 모델을 업데이트 합니다.

```python
service.update([different_model], inference_config, deployment_config)
```

### <a name="delete-the-service"></a>서비스 삭제

서비스를 삭제 하려면 [delete ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice%28class%29?view=azure-ml-py#delete--)를 사용 합니다.

### <a id="dockerlog"></a>Docker 로그 검사

서비스 개체에서 자세한 Docker 엔진 로그 메시지를 인쇄할 수 있습니다. ACI, AKS 및 로컬 배포에 대 한 로그를 볼 수 있습니다. 다음 예에서는 로그를 인쇄 하는 방법을 보여 줍니다.

```python
# if you already have the service object handy
print(service.get_logs())

# if you only know the name of the service (note there might be multiple services with the same name but different version number)
print(ws.webservices['mysvc'].get_logs())
```

## <a name="service-launch-fails"></a>서비스 시작 실패

이미지가 성공적으로 작성 되 면 시스템은 배포 구성을 사용 하 여 컨테이너를 시작 하려고 시도 합니다. 컨테이너 시작 프로세스의 일부로, 시스템에서 채점 스크립트의 `init()` 함수를 호출합니다. `init()` 함수에 catch되지 않은 예외가 있는 경우 오류 메시지에 **CrashLoopBackOff** 오류가 표시될 수 있습니다.

[Docker 로그 검사](#dockerlog) 섹션의 정보를 사용 하 여 로그를 확인 합니다.

## <a name="function-fails-get_model_path"></a>함수 실패: get_model_path()

종종 점수 매기기 스크립트의 `init()` 함수에서 모델 파일이 나 컨테이너에 있는 모델 파일의 폴더를 찾기 위해 [get_model_path ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#get-model-path-model-name--version-none---workspace-none-) 함수가 호출 됩니다. 모델 파일이 나 폴더를 찾을 수 없는 경우 함수가 실패 합니다. 이 오류를 디버그하는 가장 쉬운 방법은 컨테이너 셸에서 아래의 Python 코드를 실행하는 것입니다.

```python
from azureml.core.model import Model
import logging
logging.basicConfig(level=logging.DEBUG)
print(Model.get_model_path(model_name='my-best-model'))
```

이 예제에서는 점수 매기기 스크립트가 모델 파일이 나 폴더를 찾을 것으로 예상 하는 컨테이너의 로컬 경로 (`/var/azureml-app`기준)를 출력 합니다. 그러면 예상 위치에 파일 또는 폴더가 실제로 있는지 확인할 수 있습니다.

로깅 수준을 디버그로 설정 하면 추가 정보가 기록 될 수 있으며이는 오류를 식별 하는 데 유용할 수 있습니다.

## <a name="function-fails-runinput_data"></a>함수 실패: run(input_data)

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

**참고**: `run(input_data)` 호출에서 오류 메시지를 반환하는 방법은 디버깅 용도로만 사용해야 합니다. 보안상의 이유로 프로덕션 환경에서는 이러한 방식으로 오류 메시지를 반환 해서는 안 됩니다.

## <a name="http-status-code-503"></a>HTTP 상태 코드 503

Azure Kubernetes 서비스 배포는 복제본을 추가 하 여 추가 부하를 지원할 수 있도록 자동 크기 조정을 지원 합니다. 그러나 autoscaler는 부하의 **점진적** 변화를 처리 하도록 설계 되었습니다. 초당 요청이 크게 급증 하면 클라이언트에서 HTTP 상태 코드 503을 수신할 수 있습니다.

503 상태 코드를 방지 하는 데 도움이 되는 두 가지 항목이 있습니다.

* 자동 크기 조정이 새 복제본을 만드는 사용률 수준을 변경 합니다.
    
    기본적으로 자동 크기 조정 대상 사용률은 70%로 설정 됩니다. 즉, 서비스가 최대 30%의 초당 급증 하는 요청 수 (RPS)를 처리할 수 있습니다. `autoscale_target_utilization`를 더 낮은 값으로 설정 하 여 사용률 목표를 조정할 수 있습니다.

    > [!IMPORTANT]
    > 이 변경으로 인해 복제본이 *더*이상 생성 되지 않습니다. 대신 낮은 사용률 임계값으로 만들어집니다. 서비스가 70% 이용 될 때까지 기다리는 대신 30%의 사용률을 발생 시킬 때 값을 30%로 변경 하면 복제본이 생성 됩니다.
    
    웹 서비스가 이미 현재 최대 복제본을 사용 중이 고 503 상태 코드가 표시 되는 경우 `autoscale_max_replicas` 값을 늘려 최대 복제본 수를 늘립니다.

* 최소 복제본 수를 변경 합니다. 최소 복제본 수를 늘리면 들어오는 급증을 처리할 수 있는 더 큰 풀이 제공 됩니다.

    최소 복제본 수를 늘리려면 `autoscale_min_replicas`을 더 큰 값으로 설정 합니다. 다음 코드를 사용 하 여 값을 프로젝트와 관련 된 값으로 대체 하 여 필요한 복제본을 계산할 수 있습니다.

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
    > 새 최소 복제본이 처리할 수 있는 것 보다 더 큰 요청 급증이 발생 하는 경우 503s을 다시 받을 수 있습니다. 예를 들어 서비스에 대 한 트래픽이 늘어나면 최소 복제본을 늘려야 할 수 있습니다.

에 대 한 `autoscale_target_utilization`, `autoscale_max_replicas`및 `autoscale_min_replicas`를 설정 하는 방법에 대 한 자세한 내용은 [AksWebservice](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.akswebservice?view=azure-ml-py) 모듈 참조를 참조 하세요.


## <a name="advanced-debugging"></a>고급 디버깅

모델 배포에 포함 된 Python 코드를 대화형으로 디버깅 해야 하는 경우도 있습니다. 예를 들어 항목 스크립트가 실패 하 고 그 이유를 추가 로깅으로 확인할 수 없는 경우입니다. Visual Studio Code 및 Visual Studio용 Python 도구 (PTVSD)를 사용 하 여 Docker 컨테이너 내에서 실행 되는 코드에 연결할 수 있습니다.

> [!IMPORTANT]
> `Model.deploy()` 및 `LocalWebservice.deploy_configuration`를 사용 하 여 모델을 로컬로 배포 하는 경우이 디버깅 방법이 작동 하지 않습니다. 대신 [get-containerimage](https://docs.microsoft.com/python/api/azureml-core/azureml.core.image.containerimage?view=azure-ml-py) 클래스를 사용 하 여 이미지를 만들어야 합니다. 

로컬 웹 서비스 배포에는 로컬 시스템에서 작동 하는 Docker 설치가 필요 합니다. Docker를 사용 하는 방법에 대 한 자세한 내용은 [Docker 설명서](https://docs.docker.com/)를 참조 하세요.

### <a name="configure-development-environment"></a>개발 환경 구성

1. 로컬 VS Code 개발 환경에 Visual Studio용 Python 도구 (PTVSD)를 설치 하려면 다음 명령을 사용 합니다.

    ```
    python -m pip install --upgrade ptvsd
    ```

    VS Code에서 PTVSD를 사용 하는 방법에 대 한 자세한 내용은 [원격 디버깅](https://code.visualstudio.com/docs/python/debugging#_remote-debugging)을 참조 하세요.

1. Docker 이미지와 통신 하도록 VS Code를 구성 하려면 새 디버그 구성을 만듭니다.

    1. VS Code에서 __디버그__ 메뉴를 선택 하 고 __구성 열기__를 선택 합니다. __시작__ 파일 이름이 열립니다.

    1. __시작. json__ 파일에서 `"configurations": [`포함 된 줄을 찾은 후 다음 텍스트를 삽입 합니다.

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
        > 구성 섹션에 다른 항목이 이미 있는 경우 삽입 한 코드 뒤에 쉼표 (,)를 추가 합니다.

        이 섹션은 포트 5678을 사용 하 여 Docker 컨테이너에 연결 합니다.

    1. __시작 json__ 파일을 저장 합니다.

### <a name="create-an-image-that-includes-ptvsd"></a>PTVSD를 포함 하는 이미지 만들기

1. PTVSD를 포함 하도록 배포에 대 한 conda 환경을 수정 합니다. 다음 예제에서는 `pip_packages` 매개 변수를 사용 하 여 추가 하는 방법을 보여 줍니다.

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

1. PTVSD를 시작 하 고 서비스가 시작 될 때 연결을 대기 하려면 `score.py` 파일의 맨 위에 다음을 추가 합니다.

    ```python
    import ptvsd
    # Allows other computers to attach to ptvsd on this IP address and port.
    ptvsd.enable_attach(address=('0.0.0.0', 5678), redirect_output = True)
    # Wait 30 seconds for a debugger to attach. If none attaches, the script continues as normal.
    ptvsd.wait_for_attach(timeout = 30)
    print("Debugger attached...")
    ```

1. 디버깅 하는 동안 이미지를 다시 만들지 않고도 이미지의 파일을 변경할 수 있습니다. Docker 이미지에 텍스트 편집기 (vim)를 설치 하려면 `Dockerfile.steps` 라는 새 텍스트 파일을 만들고 파일의 내용으로 다음을 사용 합니다.

    ```text
    RUN apt-get update && apt-get -y install vim
    ```

    텍스트 편집기를 사용 하면 새 이미지를 만들지 않고도 docker 이미지 내의 파일을 수정 하 여 변경 내용을 테스트할 수 있습니다.

1. `Dockerfile.steps` 파일을 사용 하는 이미지를 만들려면 이미지를 만들 때 `docker_file` 매개 변수를 사용 합니다. 다음 예제에서는이 작업을 수행 하는 방법을 보여 줍니다.

    > [!NOTE]
    > 이 예에서는 `ws`이 Azure Machine Learning 작업 영역을 가리키고 `model`가 배포 되는 모델 이라고 가정 합니다. `myenv.yml` 파일에는 1 단계에서 만든 conda 종속성이 포함 되어 있습니다.

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

이미지를 만든 후에는 레지스트리의 이미지 위치가 표시 됩니다. 위치는 다음 텍스트와 유사 합니다.

```text
myregistry.azurecr.io/myimage:1
```

이 텍스트 예제에서는 레지스트리 이름이 `myregistry` 되며 이미지 이름은 `myimage`입니다. 이미지 버전이 `1`되었습니다.

### <a name="download-the-image"></a>이미지 다운로드

1. 명령 프롬프트, 터미널 또는 기타 셸을 열고 다음 [Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest) 명령을 사용 하 여 Azure Machine Learning 작업 영역을 포함 하는 Azure 구독에 인증 합니다.

    ```azurecli
    az login
    ```

1. 이미지를 포함 하는 Azure Container Registry (ACR)에 인증 하려면 다음 명령을 사용 합니다. `myregistry`를 이미지를 등록할 때 반환 된 항목으로 바꿉니다.

    ```azurecli
    az acr login --name myregistry
    ```

1. 로컬 Docker에 이미지를 다운로드 하려면 다음 명령을 사용 합니다. `myimagepath`를 이미지를 등록할 때 반환 된 위치로 바꿉니다.

    ```bash
    docker pull myimagepath
    ```

    이미지 경로는 `myregistry.azurecr.io/myimage:1`유사 해야 합니다. 여기서 `myregistry`은 사용자의 `myimage` 이미지이 고 `1`는 이미지 버전입니다.

    > [!TIP]
    > 이전 단계의 인증은 영원히 지속 되지 않습니다. 인증 명령과 끌어오기 명령 사이에 충분 한 시간 동안 대기 하면 인증 오류가 표시 됩니다. 이런 경우 다시 인증 합니다.

    다운로드를 완료 하는 데 걸리는 시간은 인터넷 연결 속도에 따라 다릅니다. 프로세스 중에 다운로드 상태가 표시 됩니다. 다운로드가 완료 되 면 `docker images` 명령을 사용 하 여 해당 다운로드가 다운로드 되었는지 확인할 수 있습니다.

1. 이미지에 대 한 작업을 더 쉽게 수행 하려면 다음 명령을 사용 하 여 태그를 추가 합니다. `myimagepath`를 2 단계의 위치 값으로 바꿉니다.

    ```bash
    docker tag myimagepath debug:1
    ```

    나머지 단계에서는 로컬 이미지를 전체 이미지 경로 값 대신 `debug:1`으로 참조할 수 있습니다.

### <a name="debug-the-service"></a>서비스 디버깅

> [!TIP]
> `score.py` 파일에서 PTVSD 연결에 대 한 시간 제한을 설정 하는 경우 제한 시간이 만료 되기 전에 VS Code를 디버그 세션에 연결 해야 합니다. VS Code를 시작 하 고, `score.py`의 로컬 복사본을 열고, 중단점을 설정 하 고,이 섹션의 단계를 사용 하기 전에 준비 되도록 합니다.
>
> 중단점 디버깅 및 설정에 대 한 자세한 내용은 [디버깅](https://code.visualstudio.com/Docs/editor/debugging)을 참조 하세요.

1. 이미지를 사용 하 여 Docker 컨테이너를 시작 하려면 다음 명령을 사용 합니다.

    ```bash
    docker run --rm --name debug -p 8000:5001 -p 5678:5678 debug:1
    ```

1. 컨테이너 내부에 VS Code을 연결 하려면 VS Code를 열고 F5 키를 사용 하거나 __디버그__를 선택 합니다. 메시지가 표시 되 면 __Azure Machine Learning: Docker 디버그__ 구성을 선택 합니다. 디버그 드롭다운 메뉴의 __Azure Machine Learning: Docker 디버그__ 항목에서 디버그 아이콘을 선택 하 고 녹색 화살표를 사용 하 여 디버거를 연결할 수도 있습니다.

    ![디버그 아이콘, 디버깅 시작 단추 및 구성 선택기](./media/how-to-troubleshoot-deployment/start-debugging.png)

이 시점에서 VS Code Docker 컨테이너 내에서 PTVSD에 연결 하 고 이전에 설정한 중단점에서 중지 합니다. 이제 실행 되는 코드를 단계별로 실행 하 고 변수를 볼 수 있습니다.

VS Code를 사용 하 여 Python을 디버그 하는 방법에 대 한 자세한 내용은 [python 코드 디버그](https://docs.microsoft.com/visualstudio/python/debugging-python-in-visual-studio?view=vs-2019)를 참조 하세요.

<a id="editfiles"></a>
### <a name="modify-the-container-files"></a>컨테이너 파일 수정

이미지의 파일을 변경 하려면 실행 중인 컨테이너에 연결 하 고 bash 셸을 실행할 수 있습니다. 여기에서 vim를 사용 하 여 파일을 편집할 수 있습니다.

1. 실행 중인 컨테이너에 연결 하 고 컨테이너에서 bash 셸을 시작 하려면 다음 명령을 사용 합니다.

    ```bash
    docker exec -it debug /bin/bash
    ```

1. 서비스에서 사용 하는 파일을 찾으려면 기본 디렉터리가 `/var/azureml-app`와 다른 경우 컨테이너의 bash 셸에서 다음 명령을 사용 합니다.

    ```bash
    cd /var/azureml-app
    ```

    여기에서 vim를 사용 하 여 `score.py` 파일을 편집할 수 있습니다. Vim 사용에 대 한 자세한 내용은 [vim 편집기 사용](https://www.tldp.org/LDP/intro-linux/html/sect_06_02.html)을 참조 하세요.

1. 컨테이너에 대 한 변경 내용은 일반적으로 지속 되지 않습니다. 변경한 내용을 저장 하려면 다음 명령을 사용 합니다. 위 단계에서 시작 된 셸을 종료 하려면 (즉, 다른 셸에서)

    ```bash
    docker commit debug debug:2
    ```

    이 명령은 편집 내용을 포함 하는 `debug:2` 라는 새 이미지를 만듭니다.

    > [!TIP]
    > 변경 내용을 적용 하려면 현재 컨테이너를 중지 하 고 새 버전을 사용 하 여 시작 해야 합니다.

1. 컨테이너에 있는 파일의 변경 내용을 VS Code에서 사용 하는 로컬 파일과 동기화 된 상태로 유지 해야 합니다. 그렇지 않으면 디버거 환경이 예상 대로 작동 하지 않습니다.

### <a name="stop-the-container"></a>컨테이너 중지

컨테이너를 중지 하려면 다음 명령을 사용 합니다.

```bash
docker stop debug
```

## <a name="next-steps"></a>다음 단계

배포에 대해 자세히 알아보세요.

* [배포 방법 및 위치](how-to-deploy-and-where.md)
* [자습서: 모델 학습 & 배포](tutorial-train-models-with-aml.md)
