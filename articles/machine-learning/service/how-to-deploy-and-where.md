---
title: 모델을 배포 하는 방법 및 위치
titleSuffix: Azure Machine Learning service
description: 다음을 포함하는 Azure Machine Learning 서비스 모델을 배포하는 방법 및 위치를 알아봅니다. Azure Container Instances, Azure Kubernetes Service, Azure IoT Edge 및 Field-Programmable Gate Array
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: jordane
author: jpe316
ms.reviewer: larryfr
ms.date: 07/08/2019
ms.custom: seoapril2019
ms.openlocfilehash: 01ca3e5afc1c837a2789ba23e0b79447cd295fdd
ms.sourcegitcommit: d060947aae93728169b035fd54beef044dbe9480
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/02/2019
ms.locfileid: "68742336"
---
# <a name="deploy-models-with-the-azure-machine-learning-service"></a>Azure Machine Learning Services를 사용하여 모델 배포

Azure 클라우드에서 machine learning 모델을 웹 서비스로 배포 하거나 장치를 IoT Edge 하는 방법에 대해 알아봅니다. 

모델을 [배포 하는 위치](#target) 와 관계 없이 워크플로는 다음과 유사 합니다.

1. 모델을 등록합니다.
1. 배포 준비 (자산, 사용량, 계산 대상 지정)
1. 계산 대상에 모델을 배포 합니다.
1. 웹 서비스 라고도 하는 배포 된 모델을 테스트 합니다.

배포 워크플로에 관련된 개념에 대한 자세한 내용은 [Azure Machine Learning Service를 사용하여 모델 관리, 배포 및 모니터링](concept-model-management-and-deployment.md)을 참조하세요.

## <a name="prerequisites"></a>전제 조건

- 모델. 학습 된 모델이 없는 경우 [이 자습서](https://aka.ms/azml-deploy-cloud)에서 제공 하는 모델 & 종속성 파일을 사용할 수 있습니다.

- Machine Learning 서비스, [Azure Machine Learning PYTHON SDK](https://aka.ms/aml-sdk)또는 [Azure Machine Learning Visual Studio Code 확장](how-to-vscode-tools.md) [에 대 한 Azure CLI 확장](reference-azure-machine-learning-cli.md)입니다.

## <a id="registermodel"></a>모델 등록

모델을 구성 하는 하나 이상의 파일에 대해 등록 된 모델 논리적 컨테이너입니다. 예를 들어 여러 파일에 저장 된 모델의 경우 작업 영역에서 단일 모델로 등록할 수 있습니다. 등록 후 등록 된 모델을 다운로드 하거나 배포 하 고 등록 된 모든 파일을 받을 수 있습니다.

기계 학습 모델은 Azure Machine Learning 작업 영역에 등록 됩니다. 모델은 Azure Machine Learning에서 제공 되거나 다른 위치에서 가져올 수 있습니다. 다음 예에서는 파일에서 모델을 등록 하는 방법을 보여 줍니다.

### <a name="register-a-model-from-an-experiment-run"></a>실험 실행에서 모델 등록

+ **Scikit-SDK를 사용 하는 예제 배우기**
  ```python
  model = run.register_model(model_name='sklearn_mnist', model_path='outputs/sklearn_mnist_model.pkl')
  print(model.name, model.id, model.version, sep='\t')
  ```

  > [!TIP]
  > 모델 등록에 여러 파일을 포함 하려면를 파일이 `model_path` 포함 된 디렉터리로 설정 합니다.

+ **CLI 사용**

  ```azurecli-interactive
  az ml model register -n sklearn_mnist  --asset-path outputs/sklearn_mnist_model.pkl  --experiment-name myexperiment --run-id myrunid
  ```

  > [!TIP]
  > 모델 등록에 여러 파일을 포함 하려면를 파일이 `--asset-path` 포함 된 디렉터리로 설정 합니다.

+ **VS Code 사용**

  [VS Code](how-to-vscode-tools.md#deploy-and-manage-models) 확장 프로그램을 사용 하 여 모델 파일이 나 폴더를 사용 하 여 모델을 등록 합니다.

### <a name="register-an-externally-created-model"></a>외부에서 만든 모델 등록

[!INCLUDE [trusted models](../../../includes/machine-learning-service-trusted-model.md)]

모델에 **로컬 경로** 를 제공 하 여 외부에서 만든 모델을 등록할 수 있습니다. 폴더 또는 단일 파일을 제공할 수 있습니다.

+ **Python SDK를 사용 하는 ONNX 예제:**
  ```python
  onnx_model_url = "https://www.cntk.ai/OnnxModels/mnist/opset_7/mnist.tar.gz"
  urllib.request.urlretrieve(onnx_model_url, filename="mnist.tar.gz")
  !tar xvzf mnist.tar.gz
  
  model = Model.register(workspace = ws,
                         model_path ="mnist/model.onnx",
                         model_name = "onnx_mnist",
                         tags = {"onnx": "demo"},
                         description = "MNIST image classification CNN from ONNX Model Zoo",)
  ```

  > [!TIP]
  > 모델 등록에 여러 파일을 포함 하려면를 파일이 `model_path` 포함 된 디렉터리로 설정 합니다.

+ **CLI 사용**
  ```azurecli-interactive
  az ml model register -n onnx_mnist -p mnist/model.onnx
  ```

  > [!TIP]
  > 모델 등록에 여러 파일을 포함 하려면를 파일이 `-p` 포함 된 디렉터리로 설정 합니다.

**예상 시간**: 약 10초

자세한 내용은 [Model 클래스](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py)에 대한 참조 설명서를 참조하세요.

Azure Machine Learning 서비스 외부에서 학습 한 모델을 사용 하는 방법에 대 한 자세한 내용은 [기존 모델을 배포 하는 방법](how-to-deploy-existing-model.md)을 참조 하세요.

<a name="target"></a>

## <a name="choose-a-compute-target"></a>계산 대상 선택

다음 계산 대상 또는 계산 리소스를 사용 하 여 웹 서비스 배포를 호스트할 수 있습니다. 

[!INCLUDE [aml-compute-target-deploy](../../../includes/aml-compute-target-deploy.md)]

## <a name="prepare-to-deploy"></a>배포 준비

웹 서비스로 배포 하려면 유추 구성 (`InferenceConfig`) 및 배포 구성을 만들어야 합니다. 유추 또는 모델 점수 매기기는 배포 된 모델이 예측에 사용 되는 단계 이며 가장 일반적으로 프로덕션 데이터에 사용 됩니다. 유추 구성에서는 모델을 제공 하는 데 필요한 스크립트와 종속성을 지정 합니다. 배포 구성에서 계산 대상에 모델을 제공 하는 방법에 대 한 세부 정보를 지정 합니다.

> [!IMPORTANT]
> Azure Machine Learning SDK는 웹 서비스 또는 IoT Edge 배포에서 데이터 저장소 또는 데이터 집합에 액세스 하는 방법을 제공 하지 않습니다. 배포 외부에 저장 된 데이터에 액세스 하기 위해 배포 된 모델이 필요한 경우 (예: Azure Storage 계정에서) 관련 SDK를 사용 하 여 사용자 지정 코드 솔루션을 개발 해야 합니다. 예를 들어 [Python 용 AZURE STORAGE SDK](https://github.com/Azure/azure-storage-python)가 있습니다.
>
> 시나리오에 사용할 수 있는 또 다른 대안은 점수를 매길 때 데이터 저장소에 대 한 액세스를 제공 하는 [일괄 처리 예측](how-to-run-batch-predictions.md)입니다.

### <a id="script"></a> 1. 항목 스크립트 & 종속성 정의

항목 스크립트는 배포 된 웹 서비스에 전송 된 데이터를 받아서 모델에 전달 합니다. 그런 후 모델이 반환한 응답을 수락한 후 클라이언트에 반환합니다. **스크립트는 모델에 따라 다릅니다**. 모델에 필요한 데이터를 이해 하 고 반환 해야 합니다.

스크립트에는 모델을 로드 하 고 실행 하는 두 가지 함수가 포함 되어 있습니다.

* `init()`: 일반적으로 이 함수는 모델을 전역 개체에 로드합니다. 이 함수는 웹 서비스의 Docker 컨테이너가 시작 될 때 한 번만 실행 됩니다.

* `run(input_data)`: 이 함수는 모델을 사용하여 입력 데이터를 기반으로 값을 예측합니다. 실행에 대한 입력 및 출력은 일반적으로 serialization 및 deserialization용으로 JSON을 사용합니다. 원시 이진 데이터를 사용할 수도 있습니다. 모델에 보내기 전에 또는 클라이언트에 반환하기 전에 데이터를 변환할 수 있습니다.

#### <a name="what-is-get_model_path"></a>Get_model_path 란?

모델을 등록할 때 레지스트리에서 모델을 관리 하는 데 사용 되는 모델 이름을 제공 합니다. 이 이름을 Model과 함께 사용 [합니다. get _model_path ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#get-model-path-model-name--version-none---workspace-none-) 를 사용 하 여 로컬 파일 시스템에서 모델 파일의 경로를 검색 합니다. 폴더 또는 파일 컬렉션을 등록 하는 경우이 API는 해당 파일이 포함 된 디렉터리에 대 한 경로를 반환 합니다.

모델을 등록할 때 모델을 로컬 또는 서비스 배포 중에 배치 하는 위치에 해당 하는 이름을 지정 합니다.

아래 예제에서는 라는 `sklearn_mnist_model.pkl` 단일 파일에 대 한 경로를 반환 합니다 .이 파일은 이름 `sklearn_mnist`으로 등록 됩니다.

```python
model_path = Model.get_model_path('sklearn_mnist')
```

#### <a name="optional-automatic-swagger-schema-generation"></a>필드 자동 Swagger 스키마 생성

웹 서비스에 대 한 스키마를 자동으로 생성 하려면 정의 된 형식 개체 중 하나에 대 한 생성자에서 입력 및/또는 출력의 샘플을 제공 하 고, 형식 및 샘플은 스키마를 자동으로 만드는 데 사용 됩니다. 그런 다음 Azure Machine Learning 서비스는 배포 중에 웹 서비스에 대 한 [Openapi](https://swagger.io/docs/specification/about/) (Swagger) 사양을 만듭니다.

현재 지원 되는 형식은 다음과 같습니다.

* `pandas`
* `numpy`
* `pyspark`
* 표준 Python 개체

스키마 생성을 사용 하려면 conda 환경 `inference-schema` 파일에 패키지를 포함 합니다. 다음 예에서는 항목 `[numpy-support]` 스크립트가 numpy 매개 변수 형식을 사용 하기 때문에를 사용 합니다. 

#### <a name="example-dependencies-file"></a>예제 종속성 파일
다음 YAML은 유추를 위한 Conda 종속성 파일의 예입니다.

```YAML
name: project_environment
dependencies:
  - python=3.6.2
  - pip:
    - azureml-defaults
    - scikit-learn==0.20.0
    - inference-schema[numpy-support]
```

자동 스키마 생성을 사용 하려면 항목 스크립트가 패키지를 `inference-schema` 가져와야 합니다. 

`input_sample` 및`output_sample` 변수에서 웹 서비스의 요청 및 응답 형식을 나타내는 입력 및 출력 샘플 형식을 정의 합니다. `run()` 함수에 대 한 입력 및 출력 함수 데코레이터에서 이러한 샘플을 사용 합니다. 아래 scikit 예제에서는 스키마 생성을 사용 합니다.

> [!TIP]
> 서비스를 배포한 후 스키마 JSON 문서를 검색할 수 있습니다. 와`service.swagger_uri`같은 배포 된 웹 서비스의 [swagger_uri 속성](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.local.localwebservice?view=azure-ml-py#swagger-uri) 을 사용 하 여 로컬 웹 서비스의 swagger 파일에 대 한 uri를 가져옵니다.

#### <a name="example-entry-script"></a>예제 항목 스크립트

다음 예제에서는 JSON 데이터를 수락 하 고 반환 하는 방법을 보여 줍니다.

```python
#example: scikit-learn and Swagger
import json
import numpy as np
from sklearn.externals import joblib
from sklearn.linear_model import Ridge
from azureml.core.model import Model

from inference_schema.schema_decorators import input_schema, output_schema
from inference_schema.parameter_types.numpy_parameter_type import NumpyParameterType


def init():
    global model
    # note here "sklearn_regression_model.pkl" is the name of the model registered under
    # this is a different behavior than before when the code is run locally, even though the code is the same.
    model_path = Model.get_model_path('sklearn_regression_model.pkl')
    # deserialize the model file back into a sklearn model
    model = joblib.load(model_path)


input_sample = np.array([[10, 9, 8, 7, 6, 5, 4, 3, 2, 1]])
output_sample = np.array([3726.995])


@input_schema('data', NumpyParameterType(input_sample))
@output_schema(NumpyParameterType(output_sample))
def run(data):
    try:
        result = model.predict(data)
        # you can return any datatype as long as it is JSON-serializable
        return result.tolist()
    except Exception as e:
        error = str(e)
        return error
```

#### <a name="example-script-with-dictionary-input-support-consumption-from-power-bi"></a>사전 입력을 사용 하는 예제 스크립트 (Power BI 지원)

다음 예제에서는 데이터 프레임를 사용 하 여 입력 데이터를 < key: value > 사전으로 정의 하는 방법을 보여 줍니다. 이 메서드는 Power BI에서 배포 된 웹 서비스를 사용 하는 데 지원 됩니다 ([Power BI에서 웹 서비스를 사용 하는 방법에 대 한 자세한 정보](https://docs.microsoft.com/power-bi/service-machine-learning-integration)).

```python
import json
import pickle
import numpy as np
import pandas as pd
import azureml.train.automl
from sklearn.externals import joblib
from azureml.core.model import Model

from inference_schema.schema_decorators import input_schema, output_schema
from inference_schema.parameter_types.numpy_parameter_type import NumpyParameterType
from inference_schema.parameter_types.pandas_parameter_type import PandasParameterType


def init():
    global model
    # replace model_name with your actual model name, if needed
    model_path = Model.get_model_path('model_name')
    # deserialize the model file back into a sklearn model
    model = joblib.load(model_path)


input_sample = pd.DataFrame(data=[{
    # This is a decimal type sample. Use the data type that reflects this column in your data
    "input_name_1": 5.1,
    # This is a string type sample. Use the data type that reflects this column in your data
    "input_name_2": "value2",
    # This is a integer type sample. Use the data type that reflects this column in your data
    "input_name_3": 3
}])

# This is a integer type sample. Use the data type that reflects the expected result
output_sample = np.array([0])


@input_schema('data', PandasParameterType(input_sample))
@output_schema(NumpyParameterType(output_sample))
def run(data):
    try:
        result = model.predict(data)
        # you can return any datatype as long as it is JSON-serializable
        return result.tolist()
    except Exception as e:
        error = str(e)
        return error
```
예제 스크립트에 대 한 자세한 내용은 다음 예를 참조 하십시오.

* Pytorch: [https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-pytorch](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-pytorch)
* TensorFlow[https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-tensorflow](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-tensorflow)
* Keras[https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-keras](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-keras)
* ONNX[https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/deployment/onnx/](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/deployment/onnx/)
* 이진 데이터에 대 한 점수 매기기: [웹 서비스를 사용 하는 방법](how-to-consume-web-service.md)

### <a name="2-define-your-inferenceconfig"></a>2. InferenceConfig 정의

유추 구성에서는 예측을 만들도록 모델을 구성 하는 방법을 설명 합니다. 다음 예제에서는 유추 구성을 만드는 방법을 보여 줍니다. 이 구성은 런타임, 항목 스크립트 및 conda 환경 파일 (선택 사항)을 지정 합니다.

```python
inference_config = InferenceConfig(runtime="python",
                                   entry_script="x/y/score.py",
                                   conda_file="env/myenv.yml")
```

자세한 내용은 [InferenceConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.inferenceconfig?view=azure-ml-py) 클래스 참조를 참조 하세요.

유추 구성에서 사용자 지정 Docker 이미지를 사용 하는 방법에 대 한 자세한 내용은 [사용자 지정 docker 이미지를 사용 하 여 모델을 배포 하는 방법](how-to-deploy-custom-docker-image.md)을 참조 하세요.

### <a name="cli-example-of-inferenceconfig"></a>InferenceConfig의 CLI 예제

[!INCLUDE [inference config](../../../includes/machine-learning-service-inference-config.md)]

다음 명령은 CLI를 사용 하 여 모델을 배포 하는 방법을 보여 줍니다.

```azurecli-interactive
az ml model deploy -n myservice -m mymodel:1 --ic inferenceconfig.json
```

이 예제에서 구성에는 다음 항목이 포함 됩니다.

* 이 모델에는 Python이 필요 합니다.
* 배포 된 서비스로 전송 되는 웹 요청을 처리 하는 데 사용 되는 [항목 스크립트](#script)
* 유추에 필요한 Python 패키지를 설명 하는 conda 파일입니다.

유추 구성에서 사용자 지정 Docker 이미지를 사용 하는 방법에 대 한 자세한 내용은 [사용자 지정 docker 이미지를 사용 하 여 모델을 배포 하는 방법](how-to-deploy-custom-docker-image.md)을 참조 하세요.

### <a name="3-define-your-deployment-configuration"></a>3. 배포 구성 정의

를 배포 하기 전에 배포 구성을 정의 해야 합니다. __배포 구성은 웹 서비스를 호스팅하는 계산 대상에만 적용 됩니다__. 예를 들어 로컬로 배포할 때 서비스에서 요청을 수락 하는 포트를 지정 해야 합니다.

계산 리소스를 만들어야 할 수도 있습니다. 예를 들어, 작업 영역과 연결 된 Azure Kubernetes 서비스가 아직 없는 경우입니다.

다음 표에서는 각 계산 대상에 대 한 배포 구성을 만드는 예를 제공 합니다.

| 컴퓨팅 대상 | 배포 구성 예 |
| ----- | ----- |
| 로컬 | `deployment_config = LocalWebservice.deploy_configuration(port=8890)` |
| Azure Container Instances | `deployment_config = AciWebservice.deploy_configuration(cpu_cores = 1, memory_gb = 1)` |
| Azure Kubernetes Service | `deployment_config = AksWebservice.deploy_configuration(cpu_cores = 1, memory_gb = 1)` |

> [!TIP]
> 모델을 서비스로 배포 하기 전에이를 프로 파일링 하 여 최적의 CPU 및 메모리 요구 사항을 확인 하는 것이 좋습니다. SDK 또는 CLI를 사용 하 여 모델을 프로 파일링 할 수 있습니다. 자세한 내용은 [profile ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#profile-workspace--profile-name--models--inference-config--input-data-) 및 [az ml model profile](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/model?view=azure-cli-latest#ext-azure-cli-ml-az-ml-model-profile) reference를 참조 하세요.
>
> 모델 프로 파일링 결과를 `Run` 개체로 내보냅니다. 자세한 내용은 [Modelprofile](https://docs.microsoft.com/python/api/azureml-core/azureml.core.profile.modelprofile?view=azure-ml-py) 클래스 참조를 참조 하세요.

## <a name="deploy-to-target"></a>대상에 배포

배포에서는 유추 구성 배포 구성을 사용 하 여 모델을 배포 합니다. 배포 프로세스는 계산 대상에 관계 없이 유사 합니다. AKS 클러스터에 대 한 참조를 제공 해야 하므로 AKS에 배포 하는 것은 약간 다릅니다.

### <a id="local"></a>로컬 배포

로컬로 배포 하려면 로컬 컴퓨터에 Docker가 **설치** 되어 있어야 합니다.

#### <a name="using-the-sdk"></a>SDK 사용

```python
deployment_config = LocalWebservice.deploy_configuration(port=8890)
service = Model.deploy(ws, "myservice", [model], inference_config, deployment_config)
service.wait_for_deployment(show_output = True)
print(service.state)
```

자세한 내용은 [Localwebservice](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.local.localwebservice?view=azure-ml-py), [Model. deploy ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#deploy-workspace--name--models--inference-config--deployment-config-none--deployment-target-none-)및 [Webservice](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.webservice?view=azure-ml-py)에 대 한 참조 설명서를 참조 하세요.

#### <a name="using-the-cli"></a>CLI 사용

CLI를 사용 하 여 배포 하려면 다음 명령을 사용 합니다. 를 `mymodel:1` 등록 된 모델의 이름 및 버전으로 바꿉니다.

```azurecli-interactive
az ml model deploy -m mymodel:1 -ic inferenceconfig.json -dc deploymentconfig.json
```

[!INCLUDE [aml-local-deploy-config](../../../includes/machine-learning-service-local-deploy-config.md)]

자세한 내용은 [az ml model deploy](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/model?view=azure-cli-latest#ext-azure-cli-ml-az-ml-model-deploy) reference를 참조 하세요.

### <a id="aci"></a>Azure Container Instances (DEVTEST)

[Azure Container Instances에 배포를](how-to-deploy-azure-container-instance.md)참조 하세요.

### <a id="aks"></a>Azure Kubernetes 서비스 (DEVTEST & 프로덕션)

[Azure Kubernetes Service에 배포를](how-to-deploy-azure-kubernetes-service.md)참조 하세요.

## <a name="consume-web-services"></a>웹 서비스 사용

배포 된 모든 웹 서비스는 REST API을 제공 하므로 다양 한 프로그래밍 언어로 클라이언트 응용 프로그램을 만들 수 있습니다. 서비스에 대 한 키 인증을 사용 하는 경우 요청 헤더에 토큰으로 서비스 키를 제공 해야 합니다.
서비스에 대 한 토큰 인증을 사용 하도록 설정한 경우 요청 헤더에서 전달자 토큰으로 Azure Machine Learning JWT 토큰을 제공 해야 합니다.

> [!TIP]
> 서비스를 배포한 후 스키마 JSON 문서를 검색할 수 있습니다. 와`service.swagger_uri`같은 배포 된 웹 서비스의 [swagger_uri 속성](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.local.localwebservice?view=azure-ml-py#swagger-uri) 을 사용 하 여 로컬 웹 서비스의 swagger 파일에 대 한 uri를 가져옵니다.

### <a name="request-response-consumption"></a>요청-응답 소비

Python에서 서비스를 호출 하는 방법의 예는 다음과 같습니다.
```python
import requests
import json

headers = {'Content-Type': 'application/json'}

if service.auth_enabled:
    headers['Authorization'] = 'Bearer '+service.get_keys()[0]
elif service.token_auth_enabled:
    headers['Authorization'] = 'Bearer '+service.get_token()[0]

print(headers)

test_sample = json.dumps({'data': [
    [1, 2, 3, 4, 5, 6, 7, 8, 9, 10],
    [10, 9, 8, 7, 6, 5, 4, 3, 2, 1]
]})

response = requests.post(
    service.scoring_uri, data=test_sample, headers=headers)
print(response.status_code)
print(response.elapsed)
print(response.json())
```

자세한 내용은 [웹 서비스를 사용할 클라이언트 애플리케이션 만들기](how-to-consume-web-service.md)를 참조하세요.


### <a id="azuremlcompute"></a>일괄 처리 유추
Azure Machine Learning 계산 대상은 Azure Machine Learning 서비스에서 만들어지고 관리 됩니다. Azure Machine Learning 파이프라인에서 일괄 처리 예측에 사용할 수 있습니다.

Azure Machine Learning 계산을 사용한 일괄 처리 유추 연습은 [일괄 처리 예측을 실행 하는 방법](how-to-run-batch-predictions.md) 문서를 참조 하세요.

### <a id="iotedge"></a>IoT Edge 유추
에 지에 배포 하는 기능은 미리 보기 상태입니다. 자세한 내용은 [IoT Edge 모듈로 Azure Machine Learning 배포](https://docs.microsoft.com/azure/iot-edge/tutorial-deploy-machine-learning) 문서를 참조 하세요.


## <a id="update"></a>웹 서비스 업데이트

[!INCLUDE [aml-update-web-service](../../../includes/machine-learning-update-web-service.md)]

## <a name="continuous-model-deployment"></a>연속 모델 배포 

[Azure DevOps](https://azure.microsoft.com/services/devops/)에 대 한 Machine Learning 확장을 사용 하 여 모델을 지속적으로 배포할 수 있습니다. Azure DevOps 용 Machine Learning 확장을 사용 하 여 새 Machine Learning 모델이 Azure Machine Learning 서비스 작업 영역에 등록 되 면 배포 파이프라인을 트리거할 수 있습니다. 

1. [Azure Pipelines](https://docs.microsoft.com/azure/devops/pipelines/get-started/pipelines-sign-up?view=azure-devops)에 등록 하면 응용 프로그램을 모든 플랫폼/클라우드로 지속적으로 통합 하 고 제공할 수 있습니다. Azure Pipelines [ML 파이프라인과 다릅니다](concept-ml-pipelines.md#compare). 

1. [Azure DevOps 프로젝트를 만듭니다.](https://docs.microsoft.com/azure/devops/organizations/projects/create-project?view=azure-devops)

1. [Azure Pipelines에 대 한 Machine Learning 확장](https://marketplace.visualstudio.com/items?itemName=ms-air-aiagility.vss-services-azureml&targetId=6756afbe-7032-4a36-9cb6-2771710cadc2&utm_source=vstsproduct&utm_medium=ExtHubManageList) 을 설치 합니다. 

1. __서비스 연결__ 을 사용 하 여 Azure Machine Learning 서비스 작업 영역에 대 한 서비스 사용자 연결을 설정 하 고 모든 아티팩트에 액세스할 수 있습니다. 프로젝트 설정으로 이동 하 고 서비스 연결을 클릭 한 다음 Azure Resource Manager를 선택 합니다.

    ![view-service-connection](media/how-to-deploy-and-where/view-service-connection.png) 

1. AzureMLWorkspace를 __범위 수준__ 으로 정의 하 고 후속 매개 변수를 채웁니다.

    ![view-azure-resource-manager](media/how-to-deploy-and-where/resource-manager-connection.png)

1. 그런 다음 Azure Pipelines를 사용 하 여 기계 학습 모델을 지속적으로 배포 하려면 파이프라인에서 __릴리스__를 선택 합니다. 새 아티팩트를 추가 하 고, 이전 단계에서 만든 AzureML 모델 아티팩트 및 서비스 연결을 선택 합니다. 배포를 트리거할 모델 및 버전을 선택 합니다. 

    ![select-AzureMLmodel-artifact](media/how-to-deploy-and-where/enable-modeltrigger-artifact.png)

1. 모델 아티팩트에 대해 모델 트리거를 사용 하도록 설정 합니다. 지정 된 버전이 될 때마다 트리거를 설정 하 여 해당 모델의 최신 버전)이 작업 영역에 등록 되 면 Azure DevOps 릴리스 파이프라인이 트리거됩니다. 

    ![enable-model-trigger](media/how-to-deploy-and-where/set-modeltrigger.png)

샘플 프로젝트 및 예제를 보려면 [MLOps 리포지토리](https://github.com/Microsoft/MLOps) 를 확인 하세요.

## <a name="clean-up-resources"></a>리소스 정리
배포된 웹 서비스를 삭제하려면 `service.delete()`를 사용합니다.
등록된 모델을 삭제하려면 `model.delete()`를 사용합니다.

자세한 내용은 [WebService ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice(class)?view=azure-ml-py#delete--)및 [모델인 ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#delete--)에 대 한 참조 설명서를 참조 하세요.

## <a name="next-steps"></a>다음 단계
* [사용자 지정 Docker 이미지를 사용 하 여 모델을 배포 하는 방법](how-to-deploy-custom-docker-image.md)
* [배포 문제 해결](how-to-troubleshoot-deployment.md)
* [SSL을 사용하여 Azure Machine Learning 웹 서비스 보호](how-to-secure-web-service.md)
* [웹 서비스로 배포된 ML 모델 사용](how-to-consume-web-service.md)
* [Application Insights를 사용하여 Azure Machine Learning 모델 모니터링](how-to-enable-app-insights.md)
* [프로덕션 환경에서 모델용 데이터 수집](how-to-enable-data-collection.md)

