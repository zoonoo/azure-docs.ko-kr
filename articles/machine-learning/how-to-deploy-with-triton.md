---
title: Triton을 사용한 고성능 모델 제공(미리 보기)
titleSuffix: Azure Machine Learning
description: Azure Machine Learning에서 NVIDIA Triton Inference Server를 사용하여 모델을 배포하는 방법을 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: gopalv
author: gvashishtha
ms.date: 02/16/2020
ms.topic: conceptual
ms.reviewer: larryfr
ms.custom: deploy
ms.openlocfilehash: 0bb17ded6822c477fe2107c66711af5e2dc384d3
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105107842"
---
# <a name="high-performance-serving-with-triton-inference-server-preview"></a>Triton Inference Server를 사용한 고성능 서비스 제공(미리 보기) 

[NVIDIA Triton Inference Server](https://aka.ms/nvidia-triton-docs)를 사용하여 모델 추론에 사용되는 웹 서비스의 성능을 향상하는 방법에 대해 알아봅니다.

추론 모델을 배포하는 방법 중 하나는 웹 서비스입니다. 예를 들어 Azure Kubernetes Service 또는 Azure Container Instances에 대한 배포입니다. 기본적으로 Azure Machine Learning은 웹 서비스 배포에 대한 단일 스레드 범용 웹 프레임워크를 사용합니다.

Triton은 추론에 최적화된 프레임워크입니다. 더 뛰어난 GPU 사용률과 추론 비용 효과를 제공합니다. 서버 쪽에서 들어오는 요청을 일괄 처리하고 추론을 위해 이러한 일괄 처리를 제출합니다. 일괄 처리는 GPU 리소스를 더 효율적으로 활용하며 Triton의 핵심 부분입니다.

> [!IMPORTANT]
> Azure Machine Learning에서 Triton을 사용한 배포는 현재 __미리 보기__ 상태입니다. 미리 보기 기능에는 고객 지원이 적용되지 않을 수 있습니다. 자세한 내용은 [Microsoft Azure 미리 보기에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

> [!TIP]
> 이 문서의 코드 조각은 설명 목적으로 작성되었으며 전체 솔루션을 표시하지 않을 수 있습니다. 작동하는 예제 코드는 [Azure Machine Learning의 엔드투엔드 Triton 샘플](https://aka.ms/triton-aml-sample)을 참조하세요.

> [!NOTE]
> [NVIDIA Triton Inference Server](https://aka.ms/nvidia-triton-docs)는 Azure Machine Learning에 통합된 오픈 소스 타사 소프트웨어입니다.

## <a name="prerequisites"></a>필수 구성 요소

* **Azure 구독**. 구독이 없는 경우[Azure Machine Learning 평가판 또는 유료 버전](https://aka.ms/AMLFree)을 사용해 보세요.
* Azure Machine Learning을 사용하여 [모델을 배포하는 방법 및 위치](how-to-deploy-and-where.md)에 대한 지식
* [Python용 Azure Machine Learning SDK](/python/api/overview/azure/ml/) **또는** [Azure CLI](/cli/azure/) 및 [기계 학습 확장](reference-azure-machine-learning-cli.md)
* 로컬 테스트를 위한 유효한 Docker 설치. Docker를 설치하고 유효성을 검사하는 방법에 대한 자세한 내용은 Docker 설명서의 [소개 및 설정](https://docs.docker.com/get-started/)을 참조하세요.

## <a name="architectural-overview"></a>아키텍처 개요

자체 모델에 Triton을 사용하기 전에 Azure Machine Learning에서 작동하는 방식과 기본 배포와의 차이점을 이해하는 것이 중요합니다.

**Triton을 사용하지 않는 기본 배포**

* 여러 [Gunicorn](https://gunicorn.org/) 작업자가 들어오는 요청을 동시에 처리하기 시작합니다.
* 이러한 작업자는 전처리, 모델 호출 및 후처리를 처리합니다. 
* 클라이언트가 __Azure ML 채점 URI__ 를 사용합니다. 예들 들어 `https://myservice.azureml.net/score`입니다.

:::image type="content" source="./media/how-to-deploy-with-triton/normal-deploy.png" alt-text="비 Triton 일반 배포 아키텍처 다이어그램":::

**Triton을 사용하여 직접 배포**

* 요청은 Triton 서버로 직접 이동합니다.
* Triton이 일괄 처리로 요청을 처리하여 GPU 사용률을 최대화합니다.
* 클라이언트가 __Triton URI__ 를 사용하여 요청을 수행합니다. 예들 들어 `https://myservice.azureml.net/v2/models/${MODEL_NAME}/versions/${MODEL_VERSION}/infer`입니다.

:::image type="content" source="./media/how-to-deploy-with-triton/triton-deploy.png" alt-text="Triton만 사용하고 Python 미들웨어를 사용하지 않는 추론 구성 배포":::

**Triton을 사용한 추론 구성 배포**

* 여러 [Gunicorn](https://gunicorn.org/) 작업자가 들어오는 요청을 동시에 처리하기 시작합니다.
* 요청이 **Triton 서버** 로 전달됩니다. 
* Triton이 일괄 처리로 요청을 처리하여 GPU 사용률을 최대화합니다.
* 클라이언트가 __Azure ML 채점 URI__ 를 사용하여 요청을 수행합니다. 예들 들어 `https://myservice.azureml.net/score`입니다.

:::image type="content" source="./media/how-to-deploy-with-triton/inference-config-deploy.png" alt-text="Triton 및 Python 미들웨어를 사용하여 배포":::

모델 배포에 Triton을 사용하는 워크플로는 다음과 같습니다.

1. Triton을 사용하여 모델을 직접 제공합니다.
1. Triton 배포 모델에 요청을 보낼 수 있는지 확인합니다.
1. (선택 사항) 서버 쪽 전처리 및 후처리를 위한 Python 미들웨어 계층 만들기

## <a name="deploying-triton-without-python-pre--and-post-processing"></a>Python 전처리 및 후처리 없이 Triton 배포

먼저 다음 단계에 따라 Triton Inference Server가 모델을 제공할 수 있는지 확인합니다.

### <a name="optional-define-a-model-config-file"></a>(선택 사항) 모델 구성 파일 정의

모델 구성 파일은 Triton에게 예상되는 입력 수와 해당 입력의 차원을 알려 줍니다. 구성 파일을 만드는 방법에 대한 자세한 내용은 NVIDIA 설명서의 [모델 구성](https://aka.ms/nvidia-triton-docs)을 참조하세요.

> [!TIP]
> Triton Inference Server를 시작할 때 `--strict-model-config=false` 옵션을 사용합니다. 그러므로 ONNX 또는 TensorFlow 모델에 `config.pbtxt` 파일을 제공할 필요가 없습니다.
> 
> 이 옵션에 대한 자세한 내용은 NVIDIA 설명서의 [생성된 모델 구성](https://aka.ms/nvidia-triton-docs)을 참조하세요.

### <a name="use-the-correct-directory-structure"></a>올바른 디렉터리 구조 사용

Azure Machine Learning을 사용하여 모델을 등록할 때 개별 파일 또는 디렉터리 구조를 등록할 수 있습니다. Triton을 사용하려면 `triton`이라는 디렉터리를 포함하는 디렉터리 구조에 모델이 등록되어야 합니다. 이 디렉터리의 일반적인 구조는 다음과 같습니다.

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
> 이 디렉터리 구조는 Triton 모델 리포지토리이며 모델이 Triton에서 작동하는 데 필요합니다. 자세한 내용은 NVIDIA 설명서의 [Triton 모델 리포지토리](https://aka.ms/nvidia-triton-docs)를 참조하세요.

### <a name="register-your-triton-model"></a>Triton 모델 등록

# <a name="azure-cli"></a>[Azure CLI](#tab/azcli)

```azurecli-interactive
az ml model register -n my_triton_model -p models --model-framework=Multi
```

`az ml model register`에 대한 자세한 내용은 [참조 설명서](/cli/azure/ext/azure-cli-ml/ml/model)에서 확인하세요.

모델을 Azure Machine Learning에 등록할 때 `--model-path  -p` 매개 변수의 값은 Triton의 부모 폴더 이름이어야 합니다.  
위의 예제에서 `--model-path`는 'models'입니다.

`--name  -n` 매개 변수의 값(이 예제에서는 'my_triton_model')은 Azure Machine Learning 작업 영역에 알려져 있는 모델 이름입니다. 

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
자세한 내용은 [모델 클래스](/python/api/azureml-core/azureml.core.model.model)에 대한 설명서를 참조하세요.

---

### <a name="deploy-your-model"></a>모델 배포

# <a name="azure-cli"></a>[Azure CLI](#tab/azcli)

Azure Machine Learning을 통해 생성된 'aks-gpu'라는 GPU 지원 Azure Kubernetes Service 클러스터가 있는 경우 다음 명령을 사용하여 모델을 배포할 수 있습니다.

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

모델 배포에 대한 자세한 내용은 [이 설명서](how-to-deploy-and-where.md)를 참조하세요.

### <a name="call-into-your-deployed-model"></a>배포된 모델 호출

먼저 채점 URI 및 전달자 토큰을 가져옵니다.

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

다음을 수행하여 서비스가 실행 중인지 확인합니다. 

```{bash}
!curl -v $scoring_uri/v2/health/ready -H 'Authorization: Bearer '"$service_key"''
```

이 명령은 다음과 같은 정보를 반환합니다. `200 OK`가 표시되는데 이 상태는 웹 서버가 실행 중임을 의미합니다.

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

상태 확인을 수행한 후에는 추론을 위해 Triton에 데이터를 전송하는 클라이언트를 만들 수 있습니다. 클라이언트를 만드는 방법에 대한 자세한 내용은 NVIDIA 설명서의 [클라이언트 예제](https://aka.ms/nvidia-client-examples)를 참조하세요. [Triton GitHub에도 Python 샘플](https://aka.ms/nvidia-triton-docs)이 있습니다.

이 시점에서 Python 전처리 및 후처리를 배포된 웹 서비스에 추가하지 않으려는 경우 모두 끝난 것입니다. 이 전처리 및 후처리 논리를 추가하려면 계속 읽으세요.

## <a name="optional-re-deploy-with-a-python-entry-script-for-pre--and-post-processing"></a>(선택 사항) 전처리 및 후 처리를 위해 Python 항목 스크립트를 사용하여 다시 배포

Triton이 모델을 처리할 수 있는지 확인한 후에는 '항목 스크립트'를 정의하여 전처리 및 후처리 코드를 추가할 수 있습니다. 이 파일의 이름은 `score.py`입니다. 항목 스크립트에 대한 자세한 내용은 [항목 스크립트 정의](how-to-deploy-and-where.md#define-an-entry-script)를 참조하세요.

두 개의 주요 단계는 `init()` 메서드에서 Triton HTTP 클라이언트를 초기화하고 `run()` 함수에서 해당 클라이언트를 호출하는 것입니다.

### <a name="initialize-the-triton-client"></a>Triton 클라이언트 초기화

`score.py` 파일에 다음 예제와 같은 코드를 포함합니다. Azure Machine Learning의 Triton은 localhost, 포트 8000에서 주소가 지정될 것으로 예상합니다. 이 경우 localhost는 로컬 컴퓨터의 포트가 아닌 이 배포에 대한 Docker 이미지 내에 있습니다.

> [!TIP]
> `tritonhttpclient` pip 패키지는 큐레이팅된 `AzureML-Triton` 환경에 포함되어 있으므로 이를 pip 종속성으로 지정할 필요가 없습니다.

```python
import tritonhttpclient

def init():
    global triton_client
    triton_client = tritonhttpclient.InferenceServerClient(url="localhost:8000")
```

### <a name="modify-your-scoring-script-to-call-into-triton"></a>채점 스크립트를 수정하여 Triton을 호출

다음 예제에서는 모델에 대한 메타데이터를 동적으로 요청하는 방법을 보여 줍니다.

> [!TIP]
> Triton 클라이언트의 `.get_model_metadata` 메서드를 사용하여 Triton으로 로드된 모델의 메타데이터를 동적으로 요청할 수 있습니다. 사용 예는 [샘플 노트북](https://aka.ms/triton-aml-sample)을 참조하세요.

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

### <a name="redeploy-with-an-inference-configuration"></a>추론 구성을 사용하여 재배포

추론 구성을 사용하면 항목 스크립트를 사용할 수 있을 뿐 아니라 Python SDK 또는 Azure CLI를 통해 Azure Machine Learning 배포 프로세스를 사용할 수도 있습니다.

> [!IMPORTANT]
> `AzureML-Triton` [큐레이팅된 환경](./resource-curated-environments.md)을 지정해야 합니다.
>
> Python 코드 예제는 `AzureML-Triton`을 `My-Triton`이라는 다른 환경으로 복제합니다. Azure CLI 코드는 이 환경도 사용합니다. 환경 복제에 대한 자세한 내용은 [Environment.Clone()](/python/api/azureml-core/azureml.core.environment.environment#clone-new-name-) 참조를 참조하세요.

# <a name="azure-cli"></a>[Azure CLI](#tab/azcli)

> [!TIP]
> 추론 구성을 만들기에 관한 자세한 내용은 [추론 구성 스키마](./reference-azure-machine-learning-cli.md#inference-configuration-schema)를 참조하세요.

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

배포가 완료되면 채점 URI가 표시됩니다. 이 로컬 배포의 경우 `http://localhost:6789/score`입니다. 클라우드에 배포하는 경우 [az ml service show](/cli/azure/ext/azure-cli-ml/ml/service#ext_azure_cli_ml_az_ml_service_show) CLI 명령을 사용하여 채점 URI를 가져올 수 있습니다.

추론 요청을 채점 URI로 보내는 클라이언트를 만드는 방법에 대한 자세한 내용은 [웹 서비스로 배포된 모델 사용](how-to-consume-web-service.md)을 참조하세요.

### <a name="setting-the-number-of-workers"></a>작업자 수 설정

배포에서 작업자 수를 설정하려면 환경 변수 `WORKER_COUNT`를 설정합니다. `env`라는 [환경](/python/api/azureml-core/azureml.core.environment.environment) 개체가 있는 경우 다음을 수행할 수 있습니다.

```{py}
env.environment_variables["WORKER_COUNT"] = "1"
```

이 설정은 Azure ML에 지정하는 수의 작업자를 실행하도록 지시합니다.


## <a name="clean-up-resources"></a>리소스 정리

Azure Machine Learning 작업 영역을 계속 사용할 계획이지만 배포된 서비스를 제거하려는 경우 다음 옵션 중 하나를 사용합니다.


# <a name="azure-cli"></a>[Azure CLI](#tab/azcli)

```azurecli
az ml service delete -n triton-densenet-onnx
```
# <a name="python"></a>[Python](#tab/python)

```python
local_service.delete()
```


---
## <a name="troubleshoot"></a>문제 해결

* [실패한 배포의 문제를 해결](how-to-troubleshoot-deployment.md)하고, 모델을 배포할 때 발생할 수 있는 일반적인 오류를 해결하는 방법을 알아봅니다.

* 배포 로그에 **TritonServer를 시작하지 못함** 이 표시되는 경우 [Nvidia의 오픈 소스 설명서](https://github.com/triton-inference-server/server)를 참조하세요.

## <a name="next-steps"></a>다음 단계

* [Azure Machine Learning의 엔드투엔드 Triton 샘플 확인](https://aka.ms/aml-triton-sample)
* [Triton 클라이언트 예제](https://aka.ms/nvidia-client-examples) 확인
* [Triton Inference Server 설명서](https://aka.ms/nvidia-triton-docs) 읽기
* [Azure Kubernetes Service로 배포](how-to-deploy-azure-kubernetes-service.md)
* [웹 서비스 업데이트](how-to-deploy-update-web-service.md)
* [프로덕션 환경에서 모델용 데이터 수집](how-to-enable-data-collection.md)