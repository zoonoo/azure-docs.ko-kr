---
title: Triton를 사용 하는 고성능 모델 (미리 보기)
titleSuffix: Azure Machine Learning
description: Azure Machine Learning에서 NVIDIA Triton 유추 서버를 사용 하 여 모델을 배포 하는 방법을 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: gopalv
author: gvashishtha
ms.date: 02/16/2020
ms.topic: conceptual
ms.reviewer: larryfr
ms.custom: deploy
ms.openlocfilehash: 2966b685e1904102467bf16994ea781556544047
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102519200"
---
# <a name="high-performance-serving-with-triton-inference-server-preview"></a>Triton 유추 서버를 사용 하는 고성능 서비스 (미리 보기) 

[NVIDIA Triton 유추 서버](https://aka.ms/nvidia-triton-docs) 를 사용 하 여 모델 유추에 사용 되는 웹 서비스의 성능을 향상 시키는 방법에 대해 알아봅니다.

유추를 위해 모델을 배포 하는 방법 중 하나는 웹 서비스입니다. 예를 들어 Azure Kubernetes Service에 대 한 배포 또는 Azure Container Instances입니다. 기본적으로 Azure Machine Learning는 웹 서비스 배포에 대 한 단일 스레드 *범용* 웹 프레임 워크를 사용 합니다.

Triton는 *유추에 최적화* 된 프레임 워크입니다. Gpu 및 더 비용 효율적인 유추의 활용도를 향상 시킬 수 있습니다. 서버 쪽에서는 들어오는 요청을 일괄 처리 하 고 유추를 위해 이러한 일괄 처리를 전송 합니다. 일괄 처리는 GPU 리소스를 더 효율적으로 활용 하며, Triton의 핵심 부분입니다.

> [!IMPORTANT]
> Azure Machine Learning에서 배포 하는 데 Triton를 사용 하는 것은 현재 __미리 보기__ 상태입니다. 미리 보기 기능은 고객 지원에 포함 되지 않을 수 있습니다. 자세한 내용은 [Microsoft Azure 미리 보기에 대 한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) 을 참조 하세요.

> [!TIP]
> 이 문서의 코드 조각은 설명 목적으로 작성 되었으며 전체 솔루션을 표시 하지 않을 수 있습니다. 작업 예제 코드는 [Azure Machine Learning에서 Triton의 종단 간 샘플](https://aka.ms/triton-aml-sample)을 참조 하세요.

## <a name="prerequisites"></a>필수 구성 요소

* **Azure 구독**. 구독이 없는 경우[Azure Machine Learning 평가판 또는 유료 버전](https://aka.ms/AMLFree)을 사용해 보세요.
* Azure Machine Learning를 사용 하 여 [모델을 배포 하는 방법과 위치](how-to-deploy-and-where.md) 에 대해 잘 알고 있어야 합니다.
* [Python 용 AZURE MACHINE LEARNING SDK](/python/api/overview/azure/ml/) **또는** [Azure CLI](/cli/azure/) 및 [Machine Learning 확장](reference-azure-machine-learning-cli.md)
* 로컬 테스트를 위한 Docker의 작동 설치 Docker를 설치 하 고 유효성을 검사 하는 방법에 대 한 자세한 내용은 docker 설명서의 [방향 및 설정](https://docs.docker.com/get-started/) 을 참조 하세요.

## <a name="architectural-overview"></a>아키텍처 개요

모델에 대해 Triton를 사용 하기 전에 Azure Machine Learning에서 작동 하는 방식과 기본 배포와 비교 하는 방법을 이해 하는 것이 중요 합니다.

**Triton 없는 기본 배포**

* 여러 [Gunicorn](https://gunicorn.org/) worker는 들어오는 요청을 동시에 처리 하기 시작 합니다.
* 이러한 작업자는 전처리, 모델 호출 및 후 처리를 처리 합니다. 
* 클라이언트는 __AZURE ML 점수 매기기 URI__ 를 사용 합니다. 예: `https://myservice.azureml.net/score`

:::image type="content" source="./media/how-to-deploy-with-triton/normal-deploy.png" alt-text="Triton이 아닌 일반 배포 아키텍처 다이어그램":::

**Triton를 사용 하 여 직접 배포**

* 요청은 Triton 서버로 직접 이동 합니다.
* Triton은 요청을 일괄 처리로 처리 하 여 GPU 사용률을 최대화 합니다.
* 클라이언트는 __TRITON URI__ 를 사용 하 여 요청을 수행 합니다. 예: `https://myservice.azureml.net/v2/models/${MODEL_NAME}/versions/${MODEL_VERSION}/infer`

:::image type="content" source="./media/how-to-deploy-with-triton/triton-deploy.png" alt-text="Triton only를 사용 하 여 Inferenceconfig 배포 및 Python 미들웨어 없음":::

**Triton를 사용한 유추 구성 배포**

* 여러 [Gunicorn](https://gunicorn.org/) worker는 들어오는 요청을 동시에 처리 하기 시작 합니다.
* 요청은 **Triton 서버로** 전달 됩니다. 
* Triton은 요청을 일괄 처리로 처리 하 여 GPU 사용률을 최대화 합니다.
* 클라이언트는 __AZURE ML 점수 매기기 URI__ 를 사용 하 여 요청을 수행 합니다. 예: `https://myservice.azureml.net/score`

:::image type="content" source="./media/how-to-deploy-with-triton/inference-config-deploy.png" alt-text="Triton 및 Python 미들웨어를 사용 하 여 배포":::

모델 배포에 Triton를 사용 하는 워크플로는 다음과 같습니다.

1. Triton를 사용 하 여 모델을 직접 제공 합니다.
1. Triton 배포 된 모델에 요청을 보낼 수 있는지 확인 합니다.
1. 필드 서버 쪽 전처리 및 후 처리를 위한 Python 미들웨어 계층 만들기

## <a name="deploying-triton-without-python-pre--and-post-processing"></a>Python 이전 및 후 처리를 사용 하지 않고 Triton 배포

먼저 다음 단계에 따라 Triton 유추 서버가 모델을 제공할 수 있는지 확인 합니다.

### <a name="optional-define-a-model-config-file"></a>필드 모델 구성 파일 정의

모델 구성 파일은 Triton에서 예상 되는 입력 수와 해당 입력의 크기를 알려 줍니다. 구성 파일을 만드는 방법에 대 한 자세한 내용은 NVIDIA 설명서의 [모델 구성](https://aka.ms/nvidia-triton-docs) 을 참조 하세요.

> [!TIP]
> `--strict-model-config=false`Triton 유추 서버를 시작할 때 옵션을 사용 합니다. 즉, `config.pbtxt` onnx 또는 TensorFlow 모델에 대 한 파일을 제공할 필요가 없습니다.
> 
> 이 옵션에 대 한 자세한 내용은 NVIDIA 설명서의 [생성 된 모델 구성](https://aka.ms/nvidia-triton-docs) 을 참조 하세요.

### <a name="use-the-correct-directory-structure"></a>올바른 디렉터리 구조 사용

Azure Machine Learning를 사용 하 여 모델을 등록할 때 개별 파일 또는 디렉터리 구조를 등록할 수 있습니다. Triton를 사용 하려면 라는 디렉터리를 포함 하는 디렉터리 구조에 대 한 모델 등록을 사용 해야 합니다 `triton` . 이 디렉터리의 일반적인 구조는 다음과 같습니다.

```bash
models
    - triton
        - model_1
            - model_version
                - model_file
                - config_file
        - model_2
            ...
```

> [!IMPORTANT]
> 이 디렉터리 구조는 Triton 모델 리포지토리입니다. 모델에서 Triton와 함께 작동 하는 데 필요 합니다. 자세한 내용은 NVIDIA 설명서에서 [Triton Model 리포지토리](https://aka.ms/nvidia-triton-docs) 를 참조 하세요.

### <a name="register-your-triton-model"></a>Triton 모델 등록

# <a name="azure-cli"></a>[Azure CLI](#tab/azcli)

```azurecli-interactive
az ml model register -n my_triton_model -p models --model-framework=Multi
```

에 대 한 자세한 내용은 `az ml model register` [참조 설명서](/cli/azure/ext/azure-cli-ml/ml/model)를 참조 하세요.

# <a name="python"></a>[Python](#tab/python)


```python

from azureml.core.model import Model

model_path = "models"

model = Model.register(
    model_path=model_path,
    model_name="bidaf-9-tutorial",
    tags={"area": "Natural language processing", "type": "Question-answering"},
    description="Question answering from ONNX model zoo",
    workspace=ws,
    model_framework=Model.Framework.MULTI,  # This line tells us you are registering a Triton model
)

```
자세한 내용은 [모델 클래스](/python/api/azureml-core/azureml.core.model.model)에 대 한 설명서를 참조 하세요.

---

### <a name="deploy-your-model"></a>모델 배포

# <a name="azure-cli"></a>[Azure CLI](#tab/azcli)

Azure Machine Learning를 통해 생성 된 "aks-gpu" 라는 GPU 사용 Azure Kubernetes 서비스 클러스터가 있는 경우 다음 명령을 사용 하 여 모델을 배포할 수 있습니다.

```azurecli
az ml model deploy -n triton-webservice -m triton_model:1 --dc deploymentconfig.json --compute-target aks-gpu
```

# <a name="python"></a>[Python](#tab/python)

```python
from azureml.core.webservice import AksWebservice
from azureml.core.model import InferenceConfig
from random import randint

service_name = "triton-webservice"

config = AksWebservice.deploy_configuration(
    compute_target_name="aks-gpu",
    gpu_cores=1,
    cpu_cores=1,
    memory_gb=4,
    auth_enabled=True,
)

service = Model.deploy(
    workspace=ws,
    name=service_name,
    models=[model],
    deployment_config=config,
    overwrite=True,
)
```
---

[모델 배포에 대 한 자세한 내용은이 설명서를](how-to-deploy-and-where.md)참조 하세요.

### <a name="call-into-your-deployed-model"></a>배포 된 모델 호출

먼저 점수 매기기 URI 및 전달자 토큰을 가져옵니다.

# <a name="azure-cli"></a>[Azure CLI](#tab/azcli)


```azurecli
az ml service show --name=triton-webservice
```
# <a name="python"></a>[Python](#tab/python)

```python
import requests

print(service.scoring_uri)
print(service.get_keys())

```

---

그런 다음 다음을 수행 하 여 서비스가 실행 중인지 확인 합니다. 

```{bash}
!curl -v $scoring_uri/v2/health/ready -H 'Authorization: Bearer '"$service_key"''
```

이 명령은 다음과 유사한 정보를 반환 합니다. 참고 `200 OK` 로,이 상태는 웹 서버가 실행 중임을 의미 합니다.

```{bash}
*   Trying 127.0.0.1:8000...
* Connected to localhost (127.0.0.1) port 8000 (#0)
> GET /v2/health/ready HTTP/1.1
> Host: localhost:8000
> User-Agent: curl/7.71.1
> Accept: */*
>
* Mark bundle as not supporting multiuse
< HTTP/1.1 200 OK
HTTP/1.1 200 OK
```

상태 검사를 수행한 후에는 유추를 위해 Triton에 데이터를 전송 하는 클라이언트를 만들 수 있습니다. 클라이언트를 만드는 방법에 대 한 자세한 내용은 NVIDIA 설명서의 [클라이언트 예제](https://aka.ms/nvidia-client-examples) 를 참조 하십시오. [Triton GitHub에는 Python 샘플](https://aka.ms/nvidia-triton-docs)도 있습니다.

이 시점에서 Python 사전 및 사후 처리를 배포 된 웹 서비스에 추가 하지 않으려는 경우 작업을 수행할 수 있습니다. 이 전처리 및 후 처리 논리를 추가 하려면를 참조 하세요.

## <a name="optional-re-deploy-with-a-python-entry-script-for-pre--and-post-processing"></a>필드 전처리 및 후 처리를 위해 Python 항목 스크립트를 사용 하 여 다시 배포

Triton가 모델을 처리할 수 있는지 확인 한 후에는 _항목 스크립트_ 를 정의 하 여 전처리 및 후 처리 코드를 추가할 수 있습니다. 이 파일의 이름은 `score.py` 입니다. 항목 스크립트에 대 한 자세한 내용은 [항목 스크립트 정의](how-to-deploy-and-where.md#define-an-entry-script)를 참조 하세요.

두 가지 주요 단계는 메서드에서 Triton HTTP 클라이언트를 초기화 하 `init()` 고 함수에서 해당 클라이언트를 호출 하는 것입니다 `run()` .

### <a name="initialize-the-triton-client"></a>Triton Client 초기화

파일에 다음 예제와 같은 코드를 포함 `score.py` 합니다. Azure Machine Learning의 Triton는 localhost, 포트 8000에서 해결 될 것으로 예상 됩니다. 이 경우 localhost는 로컬 컴퓨터의 포트가 아닌이 배포에 대 한 Docker 이미지 내에 있습니다.

> [!TIP]
> `tritonhttpclient`Pip 패키지는 큐 레이트 환경에 포함 되어 `AzureML-Triton` 있으므로 pip 종속성으로 지정할 필요가 없습니다.

```python
import tritonhttpclient

def init():
    global triton_client
    triton_client = tritonhttpclient.InferenceServerClient(url="localhost:8000")
```

### <a name="modify-your-scoring-script-to-call-into-triton"></a>점수 매기기 스크립트를 수정 하 여 Triton를 호출 합니다.

다음 예제에서는 모델에 대 한 메타 데이터를 동적으로 요청 하는 방법을 보여 줍니다.

> [!TIP]
> Triton 클라이언트의 메서드를 사용 하 여 Triton로 로드 된 모델의 메타 데이터를 동적으로 요청할 수 있습니다 `.get_model_metadata` . 사용 예제는 [샘플 노트북](https://aka.ms/triton-aml-sample) 을 참조 하세요.

```python
input = tritonhttpclient.InferInput(input_name, data.shape, datatype)
input.set_data_from_numpy(data, binary_data=binary_data)

output = tritonhttpclient.InferRequestedOutput(
         output_name, binary_data=binary_data, class_count=class_count)

# Run inference
res = triton_client.infer(model_name,
                          [input]
                          request_id='0',
                          outputs=[output])

```

<a id="redeploy"></a>

### <a name="redeploy-with-an-inference-configuration"></a>유추 구성을 사용 하 여 재배포

유추 구성을 사용 하면 Python SDK 또는 Azure CLI를 사용 하 여 Azure Machine Learning 배포 프로세스 뿐만 아니라 항목 스크립트를 사용할 수 있습니다.

> [!IMPORTANT]
> `AzureML-Triton` [큐 레이트 환경을](./resource-curated-environments.md)지정 해야 합니다.
>
> Python 코드 예제는 `AzureML-Triton` 라는 다른 환경으로 복제 합니다 `My-Triton` . Azure CLI 코드는이 환경도 사용 합니다. 환경 복제에 대 한 자세한 내용은 [environment. Clone ()](/python/api/azureml-core/azureml.core.environment.environment#clone-new-name-) 참조를 참조 하세요.

# <a name="azure-cli"></a>[Azure CLI](#tab/azcli)

> [!TIP]
> 유추 구성을 만드는 방법에 대 한 자세한 내용은 [유추 구성 스키마](./reference-azure-machine-learning-cli.md#inference-configuration-schema)를 참조 하세요.

```azurecli
az ml model deploy -n triton-densenet-onnx \
-m densenet_onnx:1 \
--ic inference-config.json \
-e My-Triton --dc deploymentconfig.json \
--overwrite --compute-target=aks-gpu
```

# <a name="python"></a>[Python](#tab/python)

```python
from azureml.core.webservice import LocalWebservice
from azureml.core import Environment
from azureml.core.model import InferenceConfig


local_service_name = "triton-bidaf-onnx"
env = Environment.get(ws, "AzureML-Triton").clone("My-Triton")

for pip_package in ["nltk"]:
    env.python.conda_dependencies.add_pip_package(pip_package)

inference_config = InferenceConfig(
    entry_script="score_bidaf.py",  # This entry script is where we dispatch a call to the Triton server
    source_directory=os.path.join("..", "scripts"),
    environment=env
)

local_config = LocalWebservice.deploy_configuration(
    port=6789
)

local_service = Model.deploy(
    workspace=ws,
    name=local_service_name,
    models=[model],
    inference_config=inference_config,
    deployment_config=local_config,
    overwrite=True)

local_service.wait_for_deployment(show_output = True)
print(local_service.state)
# Print the URI you can use to call the local deployment
print(local_service.scoring_uri)
```

---

배포가 완료 되 면 점수 매기기 URI가 표시 됩니다. 이 로컬 배포의 경우이 됩니다 `http://localhost:6789/score` . 클라우드에 배포 하는 경우 [az ml service show](/cli/azure/ext/azure-cli-ml/ml/service#ext_azure_cli_ml_az_ml_service_show) CLI 명령을 사용 하 여 점수 매기기 URI를 가져올 수 있습니다.

추론 요청을 점수 매기기 URI로 보내는 클라이언트를 만드는 방법에 대 한 자세한 내용은 [웹 서비스로 배포 된 모델 사용](how-to-consume-web-service.md)을 참조 하세요.

### <a name="setting-the-number-of-workers"></a>작업자 수 설정

배포의 작업자 수를 설정 하려면 환경 변수를 설정 `WORKER_COUNT` 합니다. 라는 [환경](/python/api/azureml-core/azureml.core.environment.environment) 개체가 있는 경우 다음을 `env` 수행할 수 있습니다.

```{py}
env.environment_variables["WORKER_COUNT"] = "1"
```

그러면 지정 하는 작업자 수를 실행 하는 Azure ML에 지시할 수 있습니다.


## <a name="clean-up-resources"></a>리소스 정리

Azure Machine Learning 작업 영역을 계속 사용 하려는 경우 배포 된 서비스를 제거 하려면 다음 옵션 중 하나를 사용 합니다.


# <a name="azure-cli"></a>[Azure CLI](#tab/azcli)

```azurecli
az ml service delete -n triton-densenet-onnx
```
# <a name="python"></a>[Python](#tab/python)

```python
local_service.delete()
```


---

## <a name="next-steps"></a>다음 단계

* [Azure Machine Learning에서 Triton의 종단 간 샘플을 참조 하세요.](https://aka.ms/aml-triton-sample)
* [Triton client 예제](https://aka.ms/nvidia-client-examples) 확인
* [Triton 유추 서버 설명서](https://aka.ms/nvidia-triton-docs) 읽기
* [실패한 배포 문제 해결](how-to-troubleshoot-deployment.md)
* [Azure Kubernetes Service로 배포](how-to-deploy-azure-kubernetes-service.md)
* [웹 서비스 업데이트](how-to-deploy-update-web-service.md)
* [프로덕션 환경에서 모델용 데이터 수집](how-to-enable-data-collection.md)