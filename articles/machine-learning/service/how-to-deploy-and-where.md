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
ms.openlocfilehash: cae6039b904f3dcd19ed191dc1b5fdd2f05f0323
ms.sourcegitcommit: a6873b710ca07eb956d45596d4ec2c1d5dc57353
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/16/2019
ms.locfileid: "68260338"
---
# <a name="deploy-models-with-the-azure-machine-learning-service"></a>Azure Machine Learning Services를 사용하여 모델 배포

Azure 클라우드에서 machine learning 모델을 웹 서비스로 배포 하거나 장치를 IoT Edge 하는 방법에 대해 알아봅니다. 

모델을 [배포 하는 위치](#target) 와 관계 없이 워크플로는 다음과 유사 합니다.

1. 모델을 등록합니다.
1. 배포 준비 (자산, 사용량, 계산 대상 지정)
1. 계산 대상에 모델을 배포 합니다.
1. 웹 서비스 라고도 하는 배포 된 모델을 테스트 합니다.

배포 워크플로에 관련된 개념에 대한 자세한 내용은 [Azure Machine Learning Service를 사용하여 모델 관리, 배포 및 모니터링](concept-model-management-and-deployment.md)을 참조하세요.

## <a name="prerequisites"></a>필수 구성 요소

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
  az ml model register -n sklearn_mnist  --asset-path outputs/sklearn_mnist_model.pkl  --experiment-name myexperiment
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


### <a id="script"></a> 1. 항목 스크립트 & 종속성 정의

항목 스크립트는 배포 된 웹 서비스에 전송 된 데이터를 받아서 모델에 전달 합니다. 그런 후 모델이 반환한 응답을 수락한 후 클라이언트에 반환합니다. **스크립트는 모델에 따라 다릅니다**. 모델에 필요한 데이터를 이해 하 고 반환 해야 합니다.

스크립트에는 모델을 로드 하 고 실행 하는 두 가지 함수가 포함 되어 있습니다.

* `init()`: 일반적으로 이 함수는 모델을 전역 개체에 로드합니다. 이 함수는 웹 서비스의 Docker 컨테이너가 시작 될 때 한 번만 실행 됩니다.

* `run(input_data)`: 이 함수는 모델을 사용하여 입력 데이터를 기반으로 값을 예측합니다. 실행에 대한 입력 및 출력은 일반적으로 serialization 및 deserialization용으로 JSON을 사용합니다. 원시 이진 데이터를 사용할 수도 있습니다. 모델에 보내기 전에 또는 클라이언트에 반환하기 전에 데이터를 변환할 수 있습니다.

#### <a name="what-is-getmodelpath"></a>Get_model_path 란?

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

자동 스키마 생성을 사용 하려면 **항목 스크립트가 패키지** 를 `inference-schema` 가져와야 합니다. 

`input_sample` 및`output_sample` 변수에서 웹 서비스의 요청 및 응답 형식을 나타내는 입력 및 출력 샘플 형식을 정의 합니다. `run()` 함수에 대 한 입력 및 출력 함수 데코레이터에서 이러한 샘플을 사용 합니다. 아래 scikit 예제에서는 스키마 생성을 사용 합니다.

> [!TIP]
> 서비스를 배포한 후에는 `swagger_uri` 속성을 사용 하 여 스키마 JSON 문서를 검색 합니다.

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

input_sample = np.array([[10,9,8,7,6,5,4,3,2,1]])
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
    model_path = Model.get_model_path('model_name')   # replace model_name with your actual model name, if needed
    # deserialize the model file back into a sklearn model
    model = joblib.load(model_path)

input_sample = pd.DataFrame(data=[{
              "input_name_1": 5.1,         # This is a decimal type sample. Use the data type that reflects this column in your data
              "input_name_2": "value2",    # This is a string type sample. Use the data type that reflects this column in your data
              "input_name_3": 3            # This is a integer type sample. Use the data type that reflects this column in your data
            }])

output_sample = np.array([0])              # This is a integer type sample. Use the data type that reflects the expected result

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
inference_config = InferenceConfig(runtime= "python",
                                   entry_script="x/y/score.py",
                                   conda_file="env/myenv.yml")
```

자세한 내용은 [InferenceConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.inferenceconfig?view=azure-ml-py) 클래스 참조를 참조 하세요.

유추 구성에서 사용자 지정 Docker 이미지를 사용 하는 방법에 대 한 자세한 내용은 [사용자 지정 docker 이미지를 사용 하 여 모델을 배포 하는 방법](how-to-deploy-custom-docker-image.md)을 참조 하세요.

### <a name="cli-example-of-inferenceconfig"></a>InferenceConfig의 CLI 예제

다음 JSON 문서는 machine learning CLI에서 사용 하기 위한 유추 구성의 예제입니다.

```JSON
{
   "entryScript": "x/y/score.py",
   "runtime": "python",
   "condaFile": "env/myenv.yml",
   "sourceDirectory":"C:/abc",
}
```

이 파일에서 유효한 엔터티는 다음과 같습니다.

* __entryScript__: 이미지에 대해 실행할 코드를 포함 하는 로컬 파일의 경로입니다.
* __런타임__: 이미지에 사용할 런타임입니다. 현재 지원 되는 런타임은 ' py ' 및 ' python '입니다.
* __condaFile__ (선택 사항): 이미지에 사용할 conda 환경 정의가 포함 된 로컬 파일의 경로입니다.
* __extraDockerFileSteps__ (선택 사항): 이미지를 설정할 때 실행할 추가 Docker 단계가 포함 된 로컬 파일의 경로입니다.
* __Sourcedirectory__ (선택 사항): 이미지를 만들 모든 파일이 포함 된 폴더의 경로입니다.
* __enableGpu__ (선택 사항): 이미지에서 GPU 지원을 사용할지 여부입니다. GPU 이미지는 Azure Container Instances, Azure Machine Learning 계산, Azure Virtual Machines 및 Azure Kubernetes 서비스와 같은 Microsoft Azure 서비스에서 사용 해야 합니다. 기본값은 False입니다.
* __Baseimage__ (선택 사항): 기본 이미지로 사용 되는 사용자 지정 이미지입니다. 기본 이미지를 지정 하지 않으면 지정 된 런타임 매개 변수를 기반으로 기본 이미지가 사용 됩니다.
* __baseImageRegistry__ (선택 사항): 기본 이미지를 포함 하는 이미지 레지스트리
* __Cudaversion__ (선택 사항): GPU 지원이 필요한 이미지에 대해 설치 하는 VERDA의 버전입니다. GPU 이미지는 Azure Container Instances, Azure Machine Learning 계산, Azure Virtual Machines 및 Azure Kubernetes 서비스와 같은 Microsoft Azure 서비스에서 사용 해야 합니다. 지원 되는 버전은 9.0, 9.1 및 10.0입니다. ' Enable_gpu '이 설정 된 경우 기본값은 ' 9.1 '입니다.

이러한 엔터티는 [InferenceConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.inferenceconfig?view=azure-ml-py) 클래스에 대 한 매개 변수에 매핑됩니다.

다음 명령에서는 CLI를 사용 하 여 모델을 배포 하는 방법을 보여 줍니다.

```azurecli-interactive
az ml model deploy -n myservice -m mymodel:1 --ic inferenceconfig.json
```

이 예제에서 구성에는 다음 항목이 포함 됩니다.

* 유추에 필요한 자산을 포함 하는 디렉터리입니다.
* 이 모델에는 Python이 필요 합니다.
* 배포 된 서비스로 전송 되는 웹 요청을 처리 하는 데 사용 되는 [항목 스크립트](#script)
* 유추에 필요한 Python 패키지를 설명 하는 conda 파일입니다.

유추 구성에서 사용자 지정 Docker 이미지를 사용 하는 방법에 대 한 자세한 내용은 [사용자 지정 docker 이미지를 사용 하 여 모델을 배포 하는 방법](how-to-deploy-custom-docker-image.md)을 참조 하세요.

### <a name="3-define-your-deployment-configuration"></a>3. 배포 구성 정의

를 배포 하기 전에 배포 구성을 정의 해야 합니다. 배포 구성은 웹 서비스를 호스팅하는 계산 대상에만 적용 됩니다. 예를 들어 로컬로 배포할 때 서비스에서 요청을 수락 하는 포트를 지정 해야 합니다.

계산 리소스를 만들어야 할 수도 있습니다. 예를 들어, 작업 영역과 연결 된 Azure Kubernetes 서비스가 아직 없는 경우입니다.

다음 표에서는 각 계산 대상에 대 한 배포 구성을 만드는 예를 제공 합니다.

| 계산 대상 | 배포 구성 예 |
| ----- | ----- |
| 로컬 | `deployment_config = LocalWebservice.deploy_configuration(port=8890)` |
| Azure Container Instance | `deployment_config = AciWebservice.deploy_configuration(cpu_cores = 1, memory_gb = 1)` |
| Azure Kubernetes Service | `deployment_config = AksWebservice.deploy_configuration(cpu_cores = 1, memory_gb = 1)` |

다음 섹션에서는 배포 구성을 만든 다음이를 사용 하 여 웹 서비스를 배포 하는 방법을 보여 줍니다.

### <a name="optional-profile-your-model"></a>선택 사항: 모델 프로 파일링

모델을 서비스로 배포 하기 전에 프로 파일링 하 여 SDK 또는 CLI를 사용 하는 최적의 CPU 및 메모리 요구 사항을 확인할 수 있습니다.  모델 프로 파일링 결과를 `Run` 개체로 내보냅니다. 모델 프로필 스키마의 전체 세부 정보는 [API 설명서에서 찾을 수 있습니다](https://docs.microsoft.com/python/api/azureml-core/azureml.core.profile.modelprofile?view=azure-ml-py) .

[SDK를 사용 하 여 모델을 프로 파일링 하는 방법](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#profile-workspace--profile-name--models--inference-config--input-data-)에 대해 자세히 알아보세요.

CLI를 사용 하 여 모델을 프로 파일링 하려면 [az ml model profile](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/model?view=azure-cli-latest#ext-azure-cli-ml-az-ml-model-profile)을 사용 합니다.

## <a name="deploy-to-target"></a>대상에 배포

### <a id="local"></a>로컬 배포

로컬로 배포 하려면 로컬 컴퓨터에 Docker가 **설치** 되어 있어야 합니다.

+ **SDK 사용**

  ```python
  deployment_config = LocalWebservice.deploy_configuration(port=8890)
  service = Model.deploy(ws, "myservice", [model], inference_config, deployment_config)
  service.wait_for_deployment(show_output = True)
  print(service.state)
  ```

+ **CLI 사용**

    CLI를 사용 하 여 배포 하려면 다음 명령을 사용 합니다. 를 `mymodel:1` 등록 된 모델의 이름 및 버전으로 바꿉니다.

  ```azurecli-interactive
  az ml model deploy -m mymodel:1 -ic inferenceconfig.json -dc deploymentconfig.json
  ```

    `deploymentconfig.json` 문서의 항목은 [localwebservice. deploy_configuration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.local.localwebservicedeploymentconfiguration?view=azure-ml-py)에 대 한 매개 변수에 매핑됩니다. 다음 표에서는 JSON 문서의 엔터티 및 메서드에 대 한 매개 변수의 매핑에 대해 설명 합니다.

    | JSON 엔터티 | 메서드 매개 변수 | Description |
    | ----- | ----- | ----- |
    | `computeType` | NA | 계산 대상. Local의 경우 값은 이어야 `local`합니다. |
    | `port` | `port` | 서비스의 HTTP 끝점을 노출할 로컬 포트입니다. |

    다음 JSON은 CLI에서 사용할 수 있는 배포 구성의 예입니다.

    ```json
    {
        "computeType": "local",
        "port": 32267
    }
    ```

### <a id="aci"></a>Azure Container Instances (DEVTEST)

다음 조건 중 하나 이상에 해당하는 경우 Azure Container Instances를 사용하여 모델을 웹 서비스로 배포합니다.
- 모델을 빠르게 배포하고 유효성을 검사해야 합니다.
- 개발 중인 모델을 테스트합니다. 

ACI의 할당량 및 지역 가용성을 보려면 [Azure Container Instances의 할당량 및 지역 가용성](https://docs.microsoft.com/azure/container-instances/container-instances-quotas) 문서를 참조 하세요.

+ **SDK 사용**

  ```python
  deployment_config = AciWebservice.deploy_configuration(cpu_cores = 1, memory_gb = 1)
  service = Model.deploy(ws, "aciservice", [model], inference_config, deployment_config)
  service.wait_for_deployment(show_output = True)
  print(service.state)
  ```

+ **CLI 사용**

    CLI를 사용 하 여 배포 하려면 다음 명령을 사용 합니다. 를 `mymodel:1` 등록 된 모델의 이름 및 버전으로 바꿉니다. 이 `myservice` 서비스를 제공할 이름으로 대체 합니다.

    ```azurecli-interactive
    az ml model deploy -m mymodel:1 -n myservice -ic inferenceconfig.json -dc deploymentconfig.json
    ```

    `deploymentconfig.json` 문서의 항목은 [aciwebservice. deploy_configuration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aci.aciservicedeploymentconfiguration?view=azure-ml-py)에 대 한 매개 변수에 매핑됩니다. 다음 표에서는 JSON 문서의 엔터티 및 메서드에 대 한 매개 변수의 매핑에 대해 설명 합니다.

    | JSON 엔터티 | 메서드 매개 변수 | Description |
    | ----- | ----- | ----- |
    | `computeType` | NA | 계산 대상. ACI의 경우 값은 이어야 `ACI`합니다. |
    | `containerResourceRequirements` | NA | 컨테이너에 할당 된 CPU 및 메모리에 대 한 구성 요소를 포함 합니다. |
    | &emsp;&emsp;`cpu` | `cpu_cores` | 이 웹 서비스에 할당할 CPU 코어 수입니다. 기본값과`0.1` |
    | &emsp;&emsp;`memoryInGB` | `memory_gb` | 이 웹 서비스에 할당할 메모리 양 (GB)입니다. 기본`0.5` |
    | `location` | `location` | 이 웹 서비스를 배포할 Azure 지역입니다. 지정 하지 않으면 작업 영역 위치가 사용 됩니다. 사용 가능한 지역에 대 한 자세한 내용은 다음을 참조 하세요. [ACI 지역](https://azure.microsoft.com/global-infrastructure/services/?regions=all&products=container-instances) |
    | `authEnabled` | `auth_enabled` | 이 웹 서비스에 대 한 인증을 사용할지 여부입니다. 기본값은 False입니다. |
    | `sslEnabled` | `ssl_enabled` | 이 웹 서비스에 대해 SSL을 사용 하도록 설정할지 여부입니다. 기본값은 False입니다. |
    | `appInsightsEnabled` | `enable_app_insights` | 이 웹 서비스에 대해 AppInsights를 사용 하도록 설정할지 여부입니다. 기본값은 False입니다. |
    | `sslCertificate` | `ssl_cert_pem_file` | SSL을 사용 하는 경우 필요한 인증서 파일 |
    | `sslKey` | `ssl_key_pem_file` | SSL을 사용 하는 경우 필요한 키 파일 |
    | `cname` | `ssl_cname` | SSL을 사용 하는 경우에 대 한 cname |
    | `dnsNameLabel` | `dns_name_label` | 점수 매기기 끝점의 dns 이름 레이블입니다. 지정 하지 않으면 점수 매기기 끝점에 대해 고유한 dns 이름 레이블이 생성 됩니다. |

    다음 JSON은 CLI에서 사용할 수 있는 배포 구성의 예입니다.

    ```json
    {
        "computeType": "aci",
        "containerResourceRequirements":
        {
            "cpu": 0.5,
            "memoryInGB": 1.0
        },
        "authEnabled": true,
        "sslEnabled": false,
        "appInsightsEnabled": false
    }
    ```

+ **VS Code 사용**

  [VS Code를 사용 하 여 모델을 배포](how-to-vscode-tools.md#deploy-and-manage-models) 하려면 aci 컨테이너가 즉석에서 만들어지기 때문에 미리 테스트할 aci 컨테이너를 만들 필요가 없습니다.

자세한 내용은 [AciWebservice](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aciwebservice?view=azure-ml-py) 및 [Webservice](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.webservice?view=azure-ml-py) 클래스에 대한 참조 설명서를 참조하세요.

### <a id="aks"></a>Azure Kubernetes 서비스 (DEVTEST & 프로덕션)

기존 AKS 클러스터를 사용하거나 Azure Machine Learning SDK, CLI 또는 Azure Portal을 사용하여 새로운 클러스터를 만들 수 있습니다.

<a id="deploy-aks"></a>

AKS 클러스터가 이미 연결 되어 있는 경우에는 배포할 수 있습니다. AKS 클러스터를 만들거나 연결 하지 않은 경우 프로세스에 따라 <a href="#create-attach-aks">새 AKS 클러스터를 만듭니다</a>.

+ **SDK 사용**

  ```python
  aks_target = AksCompute(ws,"myaks")
  # If deploying to a cluster configured for dev/test, ensure that it was created with enough
  # cores and memory to handle this deployment configuration. Note that memory is also used by
  # things such as dependencies and AML components.
  deployment_config = AksWebservice.deploy_configuration(cpu_cores = 1, memory_gb = 1)
  service = Model.deploy(ws, "aksservice", [model], inference_config, deployment_config, aks_target)
  service.wait_for_deployment(show_output = True)
  print(service.state)
  print(service.get_logs())
  ```

+ **CLI 사용**

    CLI를 사용 하 여 배포 하려면 다음 명령을 사용 합니다. AKS `myaks` 계산 대상의 이름으로 대체 합니다. 를 `mymodel:1` 등록 된 모델의 이름 및 버전으로 바꿉니다. 이 `myservice` 서비스를 제공할 이름으로 대체 합니다.

  ```azurecli-interactive
  az ml model deploy -ct myaks -m mymodel:1 -n myservice -ic inferenceconfig.json -dc deploymentconfig.json
  ```

    `deploymentconfig.json` 문서의 항목은 [AksWebservice. deploy_configuration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aks.aksservicedeploymentconfiguration?view=azure-ml-py)에 대 한 매개 변수에 매핑됩니다. 다음 표에서는 JSON 문서의 엔터티 및 메서드에 대 한 매개 변수의 매핑에 대해 설명 합니다.

    | JSON 엔터티 | 메서드 매개 변수 | Description |
    | ----- | ----- | ----- |
    | `computeType` | NA | 계산 대상. AKS의 경우이 값은 여야 `aks`합니다. |
    | `autoScaler` | NA | 자동 크기 조정에 대 한 구성 요소를 포함 합니다. Autoscaler 표를 참조 하세요. |
    | &emsp;&emsp;`autoscaleEnabled` | `autoscale_enabled` | 웹 서비스에 자동 크기 조정을 사용할지 여부입니다. `numReplicas`이면 이고 = , 그렇지`False`않으면입니다. `0` `True` |
    | &emsp;&emsp;`minReplicas` | `autoscale_min_replicas` | 이 웹 서비스의 크기를 자동으로 조정 하는 데 사용할 최소 컨테이너 수입니다. 기본값은 `1`입니다. |
    | &emsp;&emsp;`maxReplicas` | `autoscale_max_replicas` | 이 웹 서비스의 자동 크기를 자동으로 조정 하는 데 사용할 최대 컨테이너 수입니다. 기본값은 `10`입니다. |
    | &emsp;&emsp;`refreshPeriodInSeconds` | `autoscale_refresh_seconds` | Autoscaler이이 웹 서비스의 크기를 조정 하는 빈도입니다. 기본값은 `1`입니다. |
    | &emsp;&emsp;`targetUtilization` | `autoscale_target_utilization` | Autoscaler이이 웹 서비스에 대 한 유지 관리를 시도 하는 대상 사용률 (100의 백분율)입니다. 기본값은 `70`입니다. |
    | `dataCollection` | NA | 데이터 컬렉션에 대 한 구성 요소를 포함 합니다. |
    | &emsp;&emsp;`storageEnabled` | `collect_model_data` | 웹 서비스에 대해 모델 데이터 수집을 사용 하도록 설정할지 여부입니다. 기본값은 `False`입니다. |
    | `authEnabled` | `auth_enabled` | 웹 서비스에 대 한 인증을 사용할지 여부입니다. 기본값은 `True`입니다. |
    | `containerResourceRequirements` | NA | 컨테이너에 할당 된 CPU 및 메모리에 대 한 구성 요소를 포함 합니다. |
    | &emsp;&emsp;`cpu` | `cpu_cores` | 이 웹 서비스에 할당할 CPU 코어 수입니다. 기본값과`0.1` |
    | &emsp;&emsp;`memoryInGB` | `memory_gb` | 이 웹 서비스에 할당할 메모리 양 (GB)입니다. 기본`0.5` |
    | `appInsightsEnabled` | `enable_app_insights` | 웹 서비스에 대 한 Application Insights 로깅을 사용할지 여부입니다. 기본값은 `False`입니다. |
    | `scoringTimeoutMs` | `scoring_timeout_ms` | 웹 서비스에 대 한 점수 매기기 호출에 적용 되는 시간 제한입니다. 기본값은 `60000`입니다. |
    | `maxConcurrentRequestsPerContainer` | `replica_max_concurrent_requests` | 이 웹 서비스에 대 한 노드당 최대 동시 요청 수입니다. 기본값은 `1`입니다. |
    | `maxQueueWaitMs` | `max_request_wait_time` | 503 오류가 반환 되기까지 요청을 큐에 유지 하는 최대 시간 (밀리초)입니다. 기본값은 `500`입니다. |
    | `numReplicas` | `num_replicas` | 이 웹 서비스에 할당할 컨테이너 수입니다. 기본값은 없습니다. 이 매개 변수를 설정 하지 않으면 기본적으로 autoscaler가 사용 됩니다. |
    | `keys` | NA | 키에 대 한 구성 요소를 포함 합니다. |
    | &emsp;&emsp;`primaryKey` | `primary_key` | 이 웹 서비스에 사용할 기본 인증 키 |
    | &emsp;&emsp;`secondaryKey` | `secondary_key` | 이 웹 서비스에 사용할 보조 인증 키 |
    | `gpuCores` | `gpu_cores` | 이 웹 서비스에 할당할 GPU 코어의 수입니다. 기본값은 1입니다. |
    | `livenessProbeRequirements` | NA | 선거의 프로브 요구 사항에 대 한 구성 요소를 포함 합니다. |
    | &emsp;&emsp;`periodSeconds` | `period_seconds` | 선거의 프로브를 수행 하는 빈도 (초)입니다. 기본값은 10 초입니다. 최소값은 1입니다. |
    | &emsp;&emsp;`initialDelaySeconds` | `initial_delay_seconds` | 컨테이너를 시작한 후 선거의 프로브를 시작 하는 시간 (초)입니다. 기본값은 310입니다. |
    | &emsp;&emsp;`timeoutSeconds` | `timeout_seconds` | 선거의 프로브 시간이 초과 되는 시간 (초)입니다. 기본값은 2 초입니다. 최소값은 1입니다. |
    | &emsp;&emsp;`successThreshold` | `success_threshold` | 선거의 프로브가 실패 한 후 성공으로 간주 되는 최소 연속 성공입니다. 기본값은 1입니다. 최소값은 1입니다. |
    | &emsp;&emsp;`failureThreshold` | `failure_threshold` | Pod가 시작 되 고 선거의 프로브가 실패 하면 Kubernetes는 포기 하기 전에 카운터가 failurethreshold 시간을 시도 합니다. 기본값은 3입니다. 최소값은 1입니다. |
    | `namespace` | `namespace` | 웹 서비스가 배포 되는 Kubernetes 네임 스페이스입니다. 최대 63 소문자 영숫자 (' 으로만 구성-a'-'z ', ' 0 '-' 9 ') 및 하이픈 ('-') 문자. 첫 번째 및 마지막 문자는 하이픈을 사용할 수 없습니다. |

    다음 JSON은 CLI에서 사용할 수 있는 배포 구성의 예입니다.

    ```json
    {
        "computeType": "aks",
        "autoScaler":
        {
            "autoscaleEnabled": true,
            "minReplicas": 1,
            "maxReplicas": 3,
            "refreshPeriodInSeconds": 1,
            "targetUtilization": 70
        },
        "dataCollection":
        {
            "storageEnabled": true
        },
        "authEnabled": true,
        "containerResourceRequirements":
        {
            "cpu": 0.5,
            "memoryInGB": 1.0
        }
    }
    ```

+ **VS Code 사용**

  [VS Code 확장을 통해 AKS에 배포할](how-to-vscode-tools.md#deploy-and-manage-models)수도 있지만 AKS 클러스터를 미리 구성 해야 합니다.

[Deploy_configuration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.akswebservice) 참조에서 AKS 배포 및 자동 크기 조정에 대해 자세히 알아보세요.

#### 새 AKS 클러스터 만들기<a id="create-attach-aks"></a>
**예상 시간**: 약 20분입니다.

작업 영역에 대 한 일회성 프로세스는 AKS 클러스터를 만들거나 연결 하는 것입니다. 이 클러스터를 여러 배포에 재사용할 수 있습니다. 클러스터 또는 클러스터를 포함 하는 리소스 그룹을 삭제 하는 경우 다음에를 배포 해야 할 때 새 클러스터를 만들어야 합니다. 여러 AKS 클러스터를 작업 영역에 연결할 수 있습니다.

개발, 유효성 검사 및 테스트를 위해 AKS 클러스터를 만들려는 경우을 사용할 `cluster_purpose = AksCompute.ClusterPurpose.DEV_TEST` [`provisioning_configuration()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.akscompute?view=azure-ml-py)때를 설정 합니다. 이 설정을 사용 하 여 만든 클러스터에는 노드가 하나만 있습니다.

> [!IMPORTANT]
> 설정은 `cluster_purpose = AksCompute.ClusterPurpose.DEV_TEST` 프로덕션 트래픽을 처리 하는 데 적합 하지 않은 AKS 클러스터를 만듭니다. 유추 시간은 프로덕션을 위해 생성 된 클러스터 보다 길어질 수 있습니다. 개발/테스트 클러스터에 대 한 내결함성도 보장 되지 않습니다.
>
> 개발/테스트를 위해 생성 된 클러스터에서 두 개 이상의 가상 Cpu를 사용 하는 것이 좋습니다.

다음 예제에서는 새 Azure Kubernetes Service 클러스터를 만드는 방법을 보여 줍니다.

```python
from azureml.core.compute import AksCompute, ComputeTarget

# Use the default configuration (you can also provide parameters to customize this).
# For example, to create a dev/test cluster, use:
# prov_config = AksCompute.provisioning_configuration(cluster_purpose = AksComputee.ClusterPurpose.DEV_TEST)
prov_config = AksCompute.provisioning_configuration()

aks_name = 'myaks'
# Create the cluster
aks_target = ComputeTarget.create(workspace = ws,
                                    name = aks_name,
                                    provisioning_configuration = prov_config)

# Wait for the create process to complete
aks_target.wait_for_completion(show_output = True)
```

Azure Machine Learning SDK 외부에서 AKS 클러스터를 만드는 방법에 대 한 자세한 내용은 다음 문서를 참조 하세요.
* [AKS 클러스터 만들기](https://docs.microsoft.com/cli/azure/aks?toc=%2Fazure%2Faks%2FTOC.json&bc=%2Fazure%2Fbread%2Ftoc.json&view=azure-cli-latest#az-aks-create)
* [AKS 클러스터 만들기 (포털)](https://docs.microsoft.com/azure/aks/kubernetes-walkthrough-portal?view=azure-cli-latest)

`cluster_purpose` 매개 변수에 대 한 자세한 내용은 [AksCompute](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.aks.akscompute.clusterpurpose?view=azure-ml-py) 참조를 참조 하세요.

> [!IMPORTANT]
> [`provisioning_configuration()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.akscompute?view=azure-ml-py)의 경우, agent_count 및 vm_size에 대한 사용자 지정 값을 선택하는 경우 agent_count와 vm_size를 곱한 값이 12개 가상 CPU보다 크거나 같아야 합니다. 예를 들어, 4개의 가상 CPU가 있는 "Standard_D3_v2"의 vm_size를 사용하는 경우는 3 이상의 agent_count를 선택해야 합니다.
>
> Azure Machine Learning SDK는 AKS 클러스터의 크기 조정을 지원 하지 않습니다. 클러스터의 노드 크기를 조정 하려면 Azure Portal에서 AKS 클러스터에 대 한 UI를 사용 합니다. 클러스터의 VM 크기가 아니라 노드 수만 변경할 수 있습니다.

#### <a name="attach-an-existing-aks-cluster"></a>기존 AKS 클러스터 연결
**예상 시간:** 약 5 분.

Azure 구독에 AKS 클러스터가 이미 있고 버전 1.12. # # 인 경우 이미지를 배포 하는 데 사용할 수 있습니다.

> [!WARNING]
> AKS 클러스터를 작업 영역에 연결 하는 경우 매개 변수를 `cluster_purpose` 설정 하 여 클러스터를 사용 하는 방법을 정의할 수 있습니다.
>
> `cluster_purpose` 매개 변수를 설정 하지 않거나를 설정 `cluster_purpose = AksCompute.ClusterPurpose.FAST_PROD`하지 않은 경우 클러스터에는 사용 가능한 가상 cpu가 12 개 이상 있어야 합니다.
>
> 를 설정 `cluster_purpose = AksCompute.ClusterPurpose.DEV_TEST`하는 경우 클러스터에는 12 개의 가상 cpu가 필요 하지 않습니다. 그러나 개발/테스트용으로 구성 된 클러스터는 프로덕션 수준 트래픽에 적합 하지 않으며 유추 시간이 늘어날 수 있습니다.

다음 코드에서는 기존 AKS 1.12. # # 클러스터를 작업 영역에 연결 하는 방법을 보여 줍니다.

```python
from azureml.core.compute import AksCompute, ComputeTarget
# Set the resource group that contains the AKS cluster and the cluster name
resource_group = 'myresourcegroup'
cluster_name = 'mycluster'

# Attach the cluster to your workgroup. If the cluster has less than 12 virtual CPUs, use the following instead:
# attach_config = AksCompute.attach_configuration(resource_group = resource_group,
#                                         cluster_name = cluster_name,
#                                         cluster_purpose = AksCompute.ClusterPurpose.DEV_TEST)
attach_config = AksCompute.attach_configuration(resource_group = resource_group,
                                         cluster_name = cluster_name)
aks_target = ComputeTarget.attach(ws, 'mycompute', attach_config)
```

에 대 `attack_configuration()`한 자세한 내용은 [AksCompute. attach_configuration ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.akscompute?view=azure-ml-py#attach-configuration-resource-group-none--cluster-name-none--resource-id-none--cluster-purpose-none-) 참조를 참조 하세요.

`cluster_purpose` 매개 변수에 대 한 자세한 내용은 [AksCompute](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.aks.akscompute.clusterpurpose?view=azure-ml-py) 참조를 참조 하세요.

## <a name="consume-web-services"></a>웹 서비스 사용

배포 된 모든 웹 서비스는 REST API을 제공 하므로 다양 한 프로그래밍 언어로 클라이언트 응용 프로그램을 만들 수 있습니다. 서비스에 대 한 인증을 사용 하도록 설정한 경우 서비스 키를 요청 헤더에 토큰으로 제공 해야 합니다.

### <a name="request-response-consumption"></a>요청-응답 소비

Python에서 서비스를 호출 하는 방법의 예는 다음과 같습니다.
```python
import requests
import json

headers = {'Content-Type':'application/json'}

if service.auth_enabled:
    headers['Authorization'] = 'Bearer '+service.get_keys()[0]

print(headers)
    
test_sample = json.dumps({'data': [
    [1,2,3,4,5,6,7,8,9,10], 
    [10,9,8,7,6,5,4,3,2,1]
]})

response = requests.post(service.scoring_uri, data=test_sample, headers=headers)
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

새 모델을 만들 때 새 모델을 사용 하려는 각 서비스를 수동으로 업데이트 해야 합니다. 웹 서비스를 업데이트하려면 `update` 메서드를 사용합니다. 다음 코드에서는 새 모델을 사용 하도록 웹 서비스를 업데이트 하는 방법을 보여 줍니다.

```python
from azureml.core.webservice import Webservice
from azureml.core.model import Model

# register new model
new_model = Model.register(model_path = "outputs/sklearn_mnist_model.pkl",
                       model_name = "sklearn_mnist",
                       tags = {"key": "0.1"},
                       description = "test",
                       workspace = ws)

service_name = 'myservice'
# Retrieve existing service
service = Webservice(name = service_name, workspace = ws)

# Update to new model(s)
service.update(models = [new_model])
print(service.state)
print(service.get_logs())
```

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

