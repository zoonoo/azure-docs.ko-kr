---
title: Triton를 사용 하는 고성능 모델 (미리 보기)
titleSuffix: Azure Machine Learning
description: Azure Machine Learning Triton 유추 서버를 사용 하 여 모델을 배포 하는 방법을 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: gopalv
author: gvashishtha
ms.date: 09/23/2020
ms.topic: conceptual
ms.reviewer: larryfr
ms.custom: deploy
ms.openlocfilehash: 3a3600c4065d331ca1cfc129cd55dd56add21424
ms.sourcegitcommit: 9b8425300745ffe8d9b7fbe3c04199550d30e003
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/23/2020
ms.locfileid: "92428343"
---
# <a name="high-performance-serving-with-triton-inference-server-preview"></a>Triton 유추 서버를 사용 하는 고성능 서비스 (미리 보기) 

[NVIDIA Triton 유추 서버](https://developer.nvidia.com/nvidia-triton-inference-server) 를 사용 하 여 모델 유추에 사용 되는 웹 서비스의 성능을 향상 시키는 방법에 대해 알아봅니다.

유추를 위해 모델을 배포 하는 방법 중 하나는 웹 서비스입니다. 예를 들어 Azure Kubernetes Service에 대 한 배포 또는 Azure Container Instances입니다. 기본적으로 Azure Machine Learning는 웹 서비스 배포에 대 한 단일 스레드 *범용* 웹 프레임 워크를 사용 합니다.

Triton는 *유추에 최적화*된 프레임 워크입니다. Gpu 및 더 비용 효율적인 유추의 활용도를 향상 시킬 수 있습니다. 서버 쪽에서는 들어오는 요청을 일괄 처리 하 고 유추를 위해 이러한 일괄 처리를 전송 합니다. 일괄 처리는 GPU 리소스를 더 효율적으로 활용 하며, Triton의 핵심 부분입니다.

> [!IMPORTANT]
> Azure Machine Learning에서 배포 하는 데 Triton를 사용 하는 것은 현재 __미리 보기__상태입니다. 미리 보기 기능은 고객 지원에 포함 되지 않을 수 있습니다. 자세한 내용은 [Microsoft Azure 미리 보기에 대 한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) 을 참조 하세요.

> [!TIP]
> 이 문서의 코드 조각은 설명 목적으로 작성 되었으며 전체 솔루션을 표시 하지 않을 수 있습니다. 작업 예제 코드는 [Azure Machine Learning에서 Triton의 종단 간 샘플](https://github.com/Azure/azureml-examples/tree/main/tutorials)을 참조 하세요.

## <a name="prerequisites"></a>필수 구성 요소

* **Azure 구독**. 구독이 없는 경우[Azure Machine Learning 평가판 또는 유료 버전](https://aka.ms/AMLFree)을 사용해 보세요.
* Azure Machine Learning를 사용 하 여 [모델을 배포 하는 방법과 위치](how-to-deploy-and-where.md) 에 대해 잘 알고 있어야 합니다.
* [Python 용 AZURE MACHINE LEARNING SDK](https://docs.microsoft.com/python/api/overview/azure/ml/?view=azure-ml-py) **또는** [Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest) 및 [Machine Learning 확장](reference-azure-machine-learning-cli.md)
* 로컬 테스트를 위한 Docker의 작동 설치 Docker를 설치 하 고 유효성을 검사 하는 방법에 대 한 자세한 내용은 docker 설명서의 [방향 및 설정](https://docs.docker.com/get-started/) 을 참조 하세요.

## <a name="architectural-overview"></a>아키텍처 개요

모델에 대해 Triton를 사용 하기 전에 Azure Machine Learning에서 작동 하는 방식과 기본 배포와 비교 하는 방법을 이해 하는 것이 중요 합니다.

**Triton 없는 기본 배포**

* 여러 [Gunicorn](https://gunicorn.org/) worker는 들어오는 요청을 동시에 처리 하기 시작 합니다.
* 이러한 작업자는 전처리, 모델 호출 및 후 처리를 처리 합니다. 
* 유추 요청은 __점수 매기기 URI__를 사용 합니다. 예: `https://myserevice.azureml.net/score`.

:::image type="content" source="./media/how-to-deploy-with-triton/normal-deploy.png" alt-text="Triton이 아닌 일반 배포 아키텍처 다이어그램":::

**Triton를 사용한 유추 구성 배포**

* 여러 [Gunicorn](https://gunicorn.org/) worker는 들어오는 요청을 동시에 처리 하기 시작 합니다.
* 요청은 **Triton 서버로**전달 됩니다. 
* Triton은 요청을 일괄 처리로 처리 하 여 GPU 사용률을 최대화 합니다.
* 클라이언트는 __점수 매기기 URI__ 를 사용 하 여 요청을 수행 합니다. 예: `https://myserevice.azureml.net/score`.

:::image type="content" source="./media/how-to-deploy-with-triton/inferenceconfig-deploy.png" alt-text="Triton이 아닌 일반 배포 아키텍처 다이어그램":::

모델 배포에 Triton를 사용 하는 워크플로는 다음과 같습니다.

1. Triton가 모델을 제공할 수 있는지 확인 합니다.
1. Triton 배포 된 모델에 요청을 보낼 수 있는지 확인 합니다.
1. Triton 특정 코드를 AML 배포에 통합 합니다.

## <a name="optional-define-a-model-config-file"></a>필드 모델 구성 파일 정의

모델 구성 파일은 Triton에서 예상 되는 입력 수와 해당 입력의 크기를 알려 줍니다. 구성 파일을 만드는 방법에 대 한 자세한 내용은 NVIDIA 설명서의 [모델 구성](https://docs.nvidia.com/deeplearning/triton-inference-server/user-guide/docs/model_configuration.html) 을 참조 하세요.

> [!TIP]
> `--strict-model-config=false`Triton 유추 서버를 시작할 때 옵션을 사용 합니다. 즉, `config.pbtxt` onnx 또는 TensorFlow 모델에 대 한 파일을 제공할 필요가 없습니다.
> 
> 이 옵션에 대 한 자세한 내용은 NVIDIA 설명서의 [생성 된 모델 구성](https://docs.nvidia.com/deeplearning/triton-inference-server/user-guide/docs/model_configuration.html#generated-model-configuration) 을 참조 하세요.

## <a name="directory-structure"></a>디렉터리 구조

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
> 이 디렉터리 구조는 Triton 모델 리포지토리입니다. 모델에서 Triton와 함께 작동 하는 데 필요 합니다. 자세한 내용은 NVIDIA 설명서에서 [Triton Model 리포지토리](https://docs.nvidia.com/deeplearning/triton-inference-server/user-guide/docs/model_repository.html) 를 참조 하세요.

## <a name="test-with-triton-and-docker"></a>Triton 및 Docker를 사용 하 여 테스트

Triton를 사용 하 여 실행 되는지 확인 하기 위해 모델을 테스트 하려면 Docker를 사용 하면 됩니다. 다음 명령은 Triton 컨테이너를 로컬 컴퓨터로 끌어오고 Triton 서버를 시작 합니다.

1. Triton 서버에 대 한 이미지를 로컬 컴퓨터로 꺼내려면 다음 명령을 사용 합니다.

    ```bash
    docker pull nvcr.io/nvidia/tritonserver:20.09-py3
    ```

1. Triton 서버를 시작 하려면 다음 명령을 사용 합니다. 을 `<path-to-models/triton>` 모델을 포함 하는 Triton model 리포지토리의 경로로 바꿉니다.

    ```bash
    docker run --rm -ti -v<path-to-models/triton>:/models nvcr.io/nvidia/tritonserver:20.09-py3 tritonserver --model-repository=/models --strict-model-config=false
    ```

    > [!IMPORTANT]
    > Windows를 사용 하는 경우 명령을 처음 실행할 때이 프로세스에 대 한 네트워크 연결을 허용 하 라는 메시지가 표시 될 수 있습니다. 그렇다면 액세스를 사용 하려면 선택 합니다.

    시작 되 면 다음 텍스트와 유사한 정보가 명령줄에 기록 됩니다.

    ```bash
    I0923 19:21:30.582866 1 http_server.cc:2705] Started HTTPService at 0.0.0.0:8000
    I0923 19:21:30.626081 1 http_server.cc:2724] Started Metrics Service at 0.0.0.0:8002
    ```

    첫 번째 줄은 웹 서비스의 주소를 나타냅니다. 이 경우는 `0.0.0.0:8000` 와 같습니다 `localhost:8000` .

1. 말아 넘기기와 같은 유틸리티를 사용 하 여 상태 끝점에 액세스 합니다.

    ```bash
    curl -L -v -i localhost:8000/v2/health/ready
    ```

    이 명령은 다음과 유사한 정보를 반환 합니다. 참고 `200 OK` 로,이 상태는 웹 서버가 실행 중임을 의미 합니다.

    ```bash
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

기본 상태 검사 외에도 유추를 위해 Triton에 데이터를 전송 하는 클라이언트를 만들 수 있습니다. 클라이언트를 만드는 방법에 대 한 자세한 내용은 NVIDIA 설명서의 [클라이언트 예제](https://docs.nvidia.com/deeplearning/triton-inference-server/user-guide/docs/client_example.html) 를 참조 하십시오. [Triton GitHub에는 Python 샘플](https://github.com/triton-inference-server/server/tree/master/src/clients/python/examples)도 있습니다.

Docker를 사용 하 여 Triton를 실행 하는 방법에 대 한 자세한 내용은 gpu를 사용 하는 [시스템에서 Triton 실행](https://docs.nvidia.com/deeplearning/triton-inference-server/user-guide/docs/run.html#running-triton-on-a-system-with-a-gpu) 및 [gpu 없이 시스템에서 Triton 실행](https://docs.nvidia.com/deeplearning/triton-inference-server/user-guide/docs/run.html#running-triton-on-a-system-without-a-gpu)을 참조 하세요.

## <a name="register-your-model"></a>모델 등록

이제 모델이 Triton에서 작동 하는 것을 확인 했으므로 Azure Machine Learning에 등록 합니다. 모델 등록은 모델 파일을 Azure Machine Learning 작업 영역에 저장 하 고 Python SDK 및 Azure CLI를 사용 하 여 배포할 때 사용 됩니다.

다음 예에서는 모델을 등록 하는 방법을 보여 줍니다.

# <a name="python"></a>[Python](#tab/python)

```python
from azureml.core.model import Model

model = Model.register(
    model_path=os.path.join("..", "triton"),
    model_name="bidaf_onnx",
    tags={'area': "Natural language processing", 'type': "Question answering"},
    description="Question answering model from ONNX model zoo",
    workspace=ws
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az ml model register --model-path='triton' \
--name='bidaf_onnx' \
--workspace-name='<my_workspace>'
```
---

<a id="processing"></a>

## <a name="add-pre-and-post-processing"></a>전처리 및 후 처리 추가

웹 서비스가 작동 하는지 확인 한 후에는 _항목 스크립트_를 정의 하 여 전처리 및 후 처리 코드를 추가할 수 있습니다. 이 파일의 이름은 `score.py` 입니다. 항목 스크립트에 대 한 자세한 내용은 [항목 스크립트 정의](how-to-deploy-and-where.md#define-an-entry-script)를 참조 하세요.

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

## <a name="redeploy-with-an-inference-configuration"></a>유추 구성을 사용 하 여 재배포

유추 구성을 사용 하면 Python SDK 또는 Azure CLI를 사용 하 여 Azure Machine Learning 배포 프로세스 뿐만 아니라 항목 스크립트를 사용할 수 있습니다.

> [!IMPORTANT]
> `AzureML-Triton` [큐 레이트 환경을](./resource-curated-environments.md)지정 해야 합니다.
>
> Python 코드 예제는 `AzureML-Triton` 라는 다른 환경으로 복제 합니다 `My-Triton` . Azure CLI 코드는이 환경도 사용 합니다. 환경 복제에 대 한 자세한 내용은 [environment. Clone ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py&preserve-view=true#clone-new-name-) 참조를 참조 하세요.

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

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

> [!TIP]
> 유추 구성을 만드는 방법에 대 한 자세한 내용은 [유추 구성 스키마](./reference-azure-machine-learning-cli.md#inference-configuration-schema)를 참조 하세요.

```azurecli
az ml model deploy -n triton-densenet-onnx \
-m densenet_onnx:1 \
--ic inference-config.json \
-e My-Triton --dc deploymentconfig.json \
--overwrite --compute-target=aks-gpu
```

---

배포가 완료 되 면 점수 매기기 URI가 표시 됩니다. 이 로컬 배포의 경우이 됩니다 `http://localhost:6789/score` . 클라우드에 배포 하는 경우 [az ml service show](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/service?view=azure-cli-latest#ext_azure_cli_ml_az_ml_service_show) CLI 명령을 사용 하 여 점수 매기기 URI를 가져올 수 있습니다.

추론 요청을 점수 매기기 URI로 보내는 클라이언트를 만드는 방법에 대 한 자세한 내용은 [웹 서비스로 배포 된 모델 사용](how-to-consume-web-service.md)을 참조 하세요.

## <a name="clean-up-resources"></a>리소스 정리

Azure Machine Learning 작업 영역을 계속 사용 하려는 경우 배포 된 서비스를 제거 하려면 다음 옵션 중 하나를 사용 합니다.

# <a name="python"></a>[Python](#tab/python)

```python
local_service.delete()
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az ml service delete -n triton-densenet-onnx
```

---

## <a name="next-steps"></a>다음 단계

* [Azure Machine Learning에서 Triton의 종단 간 샘플을 참조 하세요.](https://aka.ms/aml-triton-sample)
* [Triton client 예제](https://github.com/triton-inference-server/server/tree/master/src/clients/python/examples) 확인
* [Triton 유추 서버 설명서](https://docs.nvidia.com/deeplearning/triton-inference-server/user-guide/docs/index.html) 읽기
* [실패 한 배포 문제 해결](how-to-troubleshoot-deployment.md)
* [Azure Kubernetes Service로 배포](how-to-deploy-azure-kubernetes-service.md)
* [웹 서비스 업데이트](how-to-deploy-update-web-service.md)
* [프로덕션 환경에서 모델용 데이터 수집](how-to-enable-data-collection.md)
