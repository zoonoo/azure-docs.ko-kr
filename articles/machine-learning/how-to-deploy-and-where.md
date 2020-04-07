---
title: 모델을 배포하는 방법 및 위치
titleSuffix: Azure Machine Learning
description: Azure 컨테이너 인스턴스, Azure Kubernetes 서비스, Azure IoT Edge 및 필드 프로그래밍 가능한 게이트 어레이를 포함하여 Azure 기계 학습 모델을 배포하는 방법과 위치에 대해 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: jordane
author: jpe316
ms.reviewer: larryfr
ms.date: 02/27/2020
ms.custom: seoapril2019
ms.openlocfilehash: 3fe13dcb35e6985d160f52b7ee3f9da4accd7806
ms.sourcegitcommit: 441db70765ff9042db87c60f4aa3c51df2afae2d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/06/2020
ms.locfileid: "80756675"
---
# <a name="deploy-models-with-azure-machine-learning"></a>Azure Machine Learning을 사용하여 모델 배포
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

기계 학습 모델을 Azure 클라우드 또는 Azure IoT Edge 장치에 웹 서비스로 배포하는 방법을 알아봅니다.

워크플로는 모델을 [배포하는 위치에](#target) 관계없이 비슷합니다.

1. 모델을 등록합니다.
1. 배포를 준비합니다. (자산, 사용량, 컴퓨팅 대상을 지정합니다.)
1. 컴퓨팅 대상에 모델을 배포합니다.
1. 웹 서비스라고도 하는 배포된 모델을 테스트합니다.

배포 워크플로와 관련된 개념에 대한 자세한 내용은 [Azure Machine Learning을 사용하는 모델 관리, 배포 및 모니터를](concept-model-management-and-deployment.md)참조하십시오.

## <a name="prerequisites"></a>사전 요구 사항

- Azure Machine Learning 작업 영역 자세한 내용은 [Azure 기계 학습 작업 영역 만들기를](how-to-manage-workspace.md)참조하십시오.

- 모델. 학습된 모델이 없는 경우 [이 자습서에서](https://aka.ms/azml-deploy-cloud)제공하는 모델 및 종속성 파일을 사용할 수 있습니다.

- [기계 학습 서비스에 대한 Azure CLI 확장,](reference-azure-machine-learning-cli.md) [파이썬용 Azure 기계 학습 SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)또는 [Azure 기계 학습 시각적 스튜디오 코드 확장.](tutorial-setup-vscode-extension.md)

## <a name="connect-to-your-workspace"></a>작업 영역에 연결

다음 코드는 로컬 개발 환경에 캐시된 정보를 사용하여 Azure 기계 학습 작업 영역에 연결하는 방법을 보여 주며 있습니다.

+ **SDK 사용**

   ```python
   from azureml.core import Workspace
   ws = Workspace.from_config(path=".file-path/ws_config.json")
   ```

  SDK를 사용하여 작업 영역에 연결하는 방법에 대한 자세한 내용은 [파이썬용 Azure 기계 학습 SDK 설명서를](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py#workspace) 참조하십시오.

+ **CLI 사용**

   CLI를 사용하는 경우 `-w` 또는 `--workspace-name` 매개 변수를 사용하여 명령의 작업 영역을 지정합니다.

+ **Visual Studio Code 사용**

   Visual Studio 코드를 사용하는 경우 그래픽 인터페이스를 사용하여 작업 영역을 선택합니다. 자세한 내용은 Visual Studio 코드 확장 설명서의 [모델 배포 및 관리를](tutorial-train-deploy-image-classification-model-vscode.md#deploy-the-model) 참조하세요.

## <a name="register-your-model"></a><a id="registermodel"></a>모델 등록

등록된 모델은 모델을 구성하는 하나 이상의 파일에 대한 논리 컨테이너입니다. 예를 들어 여러 파일에 저장된 모델이 있는 경우 작업 공간에서 단일 모델로 등록할 수 있습니다. 파일을 등록한 후 등록된 모델을 다운로드하거나 배포하고 등록한 모든 파일을 받을 수 있습니다.

> [!TIP]
> 모델을 등록할 때 클라우드 위치(교육 실행) 또는 로컬 디렉터리의 경로를 제공합니다. 이 경로는 등록 프로세스의 일부로 업로드할 파일을 찾는 것입니다. 항목 스크립트에 사용된 경로와 일치하지 않아도 됩니다. 자세한 내용은 [입력 스크립트에서 모델 파일 찾기를](#load-model-files-in-your-entry-script)참조하십시오.

기계 학습 모델은 Azure 기계 학습 작업 영역에 등록됩니다. 모델은 Azure 기계 학습또는 다른 곳에서 올 수 있습니다. 모델을 등록할 때 모델에 대한 메타데이터를 선택적으로 제공할 수 있습니다. 그런 `tags` `properties` 다음 모델 등록에 적용하는 사전및 사전을 사용하여 모델을 필터링할 수 있습니다.

다음 예제에서는 모델을 등록하는 방법을 보여 줍니다.

### <a name="register-a-model-from-an-experiment-run"></a>실험 실행에서 모델 등록

이 섹션의 코드 조각은 학습 실행에서 모델을 등록하는 방법을 보여 줍니다.

> [!IMPORTANT]
> 이러한 스니펫을 사용하려면 이전에 학습 실행을 수행해야 하며 `Run` 개체(SDK 예제) 또는 실행 ID 값(CLI 예제)에 액세스할 수 있어야 합니다. 학습 모델에 대한 자세한 내용은 [모델 학습에 대한 계산 대상 설정을](how-to-set-up-training-targets.md)참조하십시오.

+ **SDK 사용**

  SDK를 사용하여 모델을 학습하는 경우 모델을 학습한 방법에 따라 [실행](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py) 개체 또는 [AutoMLRun](/python/api/azureml-train-automl-client/azureml.train.automl.run.automlrun) 개체를 받을 수 있습니다. 각 개체는 실험 실행에 의해 생성된 모델을 등록하는 데 사용할 수 있습니다.

  + 개체에서 모델을 `azureml.core.Run` 등록합니다.
 
    ```python
    model = run.register_model(model_name='sklearn_mnist',
                               tags={'area': 'mnist'},
                               model_path='outputs/sklearn_mnist_model.pkl')
    print(model.name, model.id, model.version, sep='\t')
    ```

    매개변수는 `model_path` 모델의 구름 위치를 나타냅니다. 이 예제에서는 단일 파일의 경로가 사용됩니다. 모델 등록에 여러 파일을 포함하려면 파일을 포함하는 폴더의 경로로 설정합니다. `model_path` 자세한 내용은 [Run.register_model](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py#register-model-model-name--model-path-none--tags-none--properties-none--model-framework-none--model-framework-version-none--description-none--datasets-none--sample-input-dataset-none--sample-output-dataset-none--resource-configuration-none----kwargs-) 설명서를 참조하십시오.

  + 개체에서 모델을 `azureml.train.automl.run.AutoMLRun` 등록합니다.

    ```python
        description = 'My AutoML Model'
        model = run.register_model(description = description,
                                   tags={'area': 'mnist'})

        print(run.model_id)
    ```

    이 예제에서는 `metric` 및 `iteration` 매개 변수가 지정되지 않으므로 최상의 기본 메트릭이 있는 반복이 등록됩니다. 실행에서 반환되는 `model_id` 값은 모델 이름 대신 사용됩니다.

    자세한 내용은 [AutoMLRun.register_model](/python/api/azureml-train-automl-client/azureml.train.automl.run.automlrun#register-model-model-name-none--description-none--tags-none--iteration-none--metric-none-) 설명서를 참조하십시오.

+ **CLI 사용**

  ```azurecli-interactive
  az ml model register -n sklearn_mnist  --asset-path outputs/sklearn_mnist_model.pkl  --experiment-name myexperiment --run-id myrunid --tag area=mnist
  ```

  [!INCLUDE [install extension](../../includes/machine-learning-service-install-extension.md)]

  매개변수는 `--asset-path` 모델의 구름 위치를 나타냅니다. 이 예제에서는 단일 파일의 경로가 사용됩니다. 모델 등록에 여러 파일을 포함하려면 파일을 포함하는 폴더의 경로로 설정합니다. `--asset-path`

+ **Visual Studio Code 사용**

  [Visual Studio 코드](tutorial-train-deploy-image-classification-model-vscode.md#deploy-the-model) 확장자를 사용하여 모델 파일 또는 폴더를 사용하여 모델을 등록합니다.

### <a name="register-a-model-from-a-local-file"></a>로컬 파일에서 모델 등록

모델의 로컬 경로를 제공하여 모델을 등록할 수 있습니다. 폴더 또는 단일 파일의 경로를 제공할 수 있습니다. 이 메서드를 사용하여 Azure 기계 학습으로 학습된 모델을 등록한 다음 다운로드할 수 있습니다. 이 메서드를 사용하여 Azure 기계 학습 외부에서 학습된 모델을 등록할 수도 있습니다.

[!INCLUDE [trusted models](../../includes/machine-learning-service-trusted-model.md)]

+ **SDK 및 ONNX 사용**

    ```python
    import os
    import urllib.request
    from azureml.core.model import Model
    # Download model
    onnx_model_url = "https://www.cntk.ai/OnnxModels/mnist/opset_7/mnist.tar.gz"
    urllib.request.urlretrieve(onnx_model_url, filename="mnist.tar.gz")
    os.system('tar xvzf mnist.tar.gz')
    # Register model
    model = Model.register(workspace = ws,
                            model_path ="mnist/model.onnx",
                            model_name = "onnx_mnist",
                            tags = {"onnx": "demo"},
                            description = "MNIST image classification CNN from ONNX Model Zoo",)
    ```

  모델 등록에 여러 파일을 포함하려면 파일을 포함하는 폴더의 경로로 설정합니다. `model_path`

+ **CLI 사용**

  ```azurecli-interactive
  az ml model register -n onnx_mnist -p mnist/model.onnx
  ```

  모델 등록에 여러 파일을 포함하려면 파일을 포함하는 폴더의 경로로 설정합니다. `-p`

**예상 시간 :** 약 10초.

자세한 내용은 [모델 클래스에](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py)대한 설명서를 참조하십시오.

Azure 기계 학습 외부에서 학습된 모델 작업에 대한 자세한 내용은 [기존 모델을 배포하는 방법을](how-to-deploy-existing-model.md)참조하세요.

<a name="target"></a>

## <a name="single-versus-multi-model-endpoints"></a>단일 모델 끝점과 다중 모델 끝점
Azure ML은 단일 끝점 뒤에 단일 또는 여러 모델 배포를 지원합니다.

다중 모델 끝점은 공유 컨테이너를 사용하여 여러 모델을 호스트합니다. 이를 통해 오버헤드 비용을 절감하고 활용도를 개선하며 모듈을 앙상블로 연결할 수 있습니다. 배포 스크립트에서 지정한 모델은 서빙 컨테이너의 디스크에 탑재되어 제공되며, 점수 매기기 시 요청되는 특정 모델에 따라 필요에 따라 메모리에 로드하고 점수를 매겨야 합니다.

컨테이너화된 단일 끝점 뒤에 여러 모델을 사용하는 방법을 보여 제공하는 E2E 예제의 경우 [다음 예제를](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/deployment/deploy-multi-model) 참조하세요.

## <a name="prepare-to-deploy"></a>배포 준비

모델을 서비스로 배포하려면 다음 구성 요소가 필요합니다.

* **추론 환경을 정의합니다.** 이 환경은 추론을 위해 모델을 실행하는 데 필요한 종속성을 캡슐화합니다.
* **점수 매기기 코드를 정의합니다.** 이 스크립트는 요청을 수락하고 모델을 사용하여 요청을 점수로 만들고 결과를 반환합니다.
* **추론 구성을 정의합니다.** 추론 구성은 모델을 서비스로 실행하는 데 필요한 환경 구성, 엔트리 스크립트 및 기타 구성 요소를 지정합니다.

필요한 구성 요소가 있으면 모델을 배포하여 CPU 및 메모리 요구 사항을 이해한 결과로 생성되는 서비스를 프로파일링할 수 있습니다.

### <a name="1-define-inference-environment"></a>1. 추론 환경 정의

추론 구성은 모델을 포함하는 웹 서비스를 설정하는 방법을 설명합니다. 나중에 모델을 배포할 때 사용됩니다.

추론 구성은 Azure 기계 학습 환경을 사용하여 배포에 필요한 소프트웨어 종속성을 정의합니다. 환경을 사용하면 교육 및 배포에 필요한 소프트웨어 종속성을 생성, 관리 및 다시 사용할 수 있습니다. 사용자 지정 종속성 파일에서 환경을 만들거나 선별된 Azure 기계 학습 환경 중 하나를 사용할 수 있습니다. 다음 YAML은 추론을 위한 Conda 종속성 파일의 예입니다. 웹 서비스로 모델을 호스트하는 데 필요한 기능이 포함되어 있으므로 >기본적으로 1.0.45를 핍 종속성으로 사용하여 azureml 기본값을 표시해야 합니다. 자동 스키마 생성을 사용하려면 엔트리 스크립트도 `inference-schema` 패키지를 가져와야 합니다.

```YAML
name: project_environment
dependencies:
  - python=3.6.2
  - scikit-learn=0.20.0
  - pip:
      # You must list azureml-defaults as a pip dependency
    - azureml-defaults>=1.0.45
    - inference-schema[numpy-support]
```

> [!IMPORTANT]
> Conda와 pip(PyPi)를 통해 종속성을 사용할 수 있는 경우 Conda 패키지에는 일반적으로 설치를 보다 안정적으로 만드는 사전 빌드된 바이너리와 함께 제공하므로 Conda 버전을 사용하는 것이 좋습니다.
>
> 자세한 내용은 [콘다 및 핍 이해](https://www.anaconda.com/understanding-conda-and-pip/)를 참조하십시오.
>
> Conda를 통해 종속성을 사용할 수 있는지 `conda search <package-name>` 확인하려면 명령을 사용하거나 에서 [https://anaconda.org/anaconda/repo](https://anaconda.org/anaconda/repo) [https://anaconda.org/conda-forge/repo](https://anaconda.org/conda-forge/repo)패키지 인덱스를 사용합니다.

종속성 파일을 사용하여 환경 개체를 만들고 나중에 사용할 수 있도록 작업 영역에 저장할 수 있습니다.

```python
from azureml.core.environment import Environment
myenv = Environment.from_conda_specification(name = 'myenv',
                                             file_path = 'path-to-conda-specification-file'
myenv.register(workspace=ws)
```

### <a name="2-define-scoring-code"></a><a id="script"></a>2. 점수 매기기 코드 정의

항목 스크립트는 배포된 웹 서비스에 전송된 데이터를 받아서 모델에 전달합니다. 그런 후 모델이 반환한 응답을 수락한 후 클라이언트에 반환합니다. *스크립트는 모델에 따라 다릅니다.* 모델이 예상하고 반환하는 데이터를 이해해야 합니다.

이 스크립트는 모델을 로드하고 실행하는 2가지 함수를 포함합니다.

* `init()`: 일반적으로 이 함수는 모델을 전역 개체에 로드합니다. 이 함수는 웹 서비스에 대한 Docker 컨테이너가 시작될 때 한 번만 실행됩니다.

* `run(input_data)`: 이 함수는 모델을 사용하여 입력 데이터를 기반으로 값을 예측합니다. 실행에 대한 입력 및 출력은 일반적으로 직렬화 및 역직렬화에 JSON을 사용합니다. 원시 이진 데이터를 사용할 수도 있습니다. 모델에 보내기 전에 또는 클라이언트에 반환하기 전에 데이터를 변환할 수 있습니다.

#### <a name="load-model-files-in-your-entry-script"></a>입력 스크립트에서 모델 파일 로드

입력 스크립트에서 모델을 찾는 방법에는 두 가지가 있습니다.
* `AZUREML_MODEL_DIR`: 모델 위치에 대한 경로를 포함하는 환경 변수입니다.
* `Model.get_model_path`: 등록된 모델 이름을 사용하여 파일로 경로를 반환하는 API입니다.

##### <a name="azureml_model_dir"></a>AZUREML_MODEL_DIR

AZUREML_MODEL_DIR 서비스 배포 중에 생성된 환경 변수입니다. 이 환경 변수를 사용하여 배포된 모델의 위치를 찾을 수 있습니다.

다음 표는 배포된 모델 수에 따라 AZUREML_MODEL_DIR 값에 대해 설명합니다.

| 배포 | 환경 변수 값 |
| ----- | ----- |
| 단일 모델 | 모델을 포함하는 폴더에 대한 경로입니다. |
| 여러 모델 | 모든 모델을 포함하는 폴더에 대한 경로입니다. 모델은 이 폴더()`$MODEL_NAME/$VERSION` |

모델 등록 및 배포 중에 모델이 AZUREML_MODEL_DIR 경로에 배치되고 원래 파일 이름이 유지됩니다.

입력 스크립트에서 모델 파일로 가는 경로를 얻으려면 환경 변수를 찾고 있는 파일 경로와 결합합니다.

**단일 모델 예제**
```python
# Example when the model is a file
model_path = os.path.join(os.getenv('AZUREML_MODEL_DIR'), 'sklearn_regression_model.pkl')

# Example when the model is a folder containing a file
file_path = os.path.join(os.getenv('AZUREML_MODEL_DIR'), 'my_model_folder', 'sklearn_regression_model.pkl')
```

**여러 모델 예제**
```python
# Example when the model is a file, and the deployment contains multiple models
model_path = os.path.join(os.getenv('AZUREML_MODEL_DIR'), 'sklearn_model', '1', 'sklearn_regression_model.pkl')
```

##### <a name="get_model_path"></a>get_model_path

모델을 등록할 때 레지스트리에서 모델을 관리하는 데 사용되는 모델 이름을 제공합니다. [Model.get_model_path()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#get-model-path-model-name--version-none---workspace-none-) 메서드와 함께 이 이름을 사용하여 모델 파일의 경로 또는 로컬 파일 시스템의 파일을 검색합니다. 폴더 또는 파일 모음을 등록하는 경우 이 API는 해당 파일이 포함된 디렉터리 경로를 반환합니다.

모델을 등록할 때 이름을 지정합니다. 이름은 로컬 또는 서비스 배포 중에 모델이 배치된 위치에 해당합니다.

#### <a name="optional-define-model-web-service-schema"></a>(선택 사항) 모델 웹 서비스 스키마 정의

웹 서비스에 대한 스키마를 자동으로 생성하려면 정의된 형식 개체 중 하나에 대한 생성자의 입력 및/또는 출력 샘플을 제공합니다. 유형 및 샘플은 스키마를 자동으로 만드는 데 사용됩니다. 그런 다음 Azure 기계 학습은 배포 하는 동안 웹 서비스에 대 한 [OpenAPI](https://swagger.io/docs/specification/about/) (Swagger) 사양을 만듭니다.

이러한 형식은 현재 지원됩니다.

* `pandas`
* `numpy`
* `pyspark`
* 표준 파이썬 오브젝트

스키마 생성을 사용하려면 종속성 `inference-schema` 파일에 오픈 소스 패키지를 포함합니다. 이 패키지에 대한 자세한 [https://github.com/Azure/InferenceSchema](https://github.com/Azure/InferenceSchema)내용은 을 참조하십시오. 웹 서비스에 대한 요청 및 `input_sample` 응답 `output_sample` 형식을 나타내는 및 변수에서 입력 및 출력 샘플 형식을 정의합니다. `run()` 함수의 입력 및 출력 함수 데코레이터에서 이러한 샘플을 사용합니다. 다음 scikit-learn 예제에서는 스키마 생성을 사용합니다.

##### <a name="example-entry-script"></a>예제 항목 스크립트

다음 예제에서는 JSON 데이터를 수락하고 반환하는 방법을 보여 줍니다.

```python
#Example: scikit-learn and Swagger
import json
import numpy as np
import os
from sklearn.externals import joblib
from sklearn.linear_model import Ridge

from inference_schema.schema_decorators import input_schema, output_schema
from inference_schema.parameter_types.numpy_parameter_type import NumpyParameterType


def init():
    global model
    # AZUREML_MODEL_DIR is an environment variable created during deployment. Join this path with the filename of the model file.
    # It holds the path to the directory that contains the deployed model (./azureml-models/$MODEL_NAME/$VERSION).
    # If there are multiple models, this value is the path to the directory containing all deployed models (./azureml-models).
    # Alternatively: model_path = Model.get_model_path('sklearn_mnist')
    model_path = os.path.join(os.getenv('AZUREML_MODEL_DIR'), 'sklearn_mnist_model.pkl')
    # Deserialize the model file back into a sklearn model
    model = joblib.load(model_path)


input_sample = np.array([[10, 9, 8, 7, 6, 5, 4, 3, 2, 1]])
output_sample = np.array([3726.995])


@input_schema('data', NumpyParameterType(input_sample))
@output_schema(NumpyParameterType(output_sample))
def run(data):
    try:
        result = model.predict(data)
        # You can return any data type, as long as it is JSON serializable.
        return result.tolist()
    except Exception as e:
        error = str(e)
        return error
```

다음 예제에서는 DataFrame을 사용하여 입력 `<key: value>` 데이터를 사전으로 정의하는 방법을 보여 줍니다. 이 메서드는 Power BI에서 배포된 웹 서비스를 사용 하기 위해 지원 됩니다. (Power[BI에서 웹 서비스를 사용하는 방법에 대해 자세히 알아보기.)](https://docs.microsoft.com/power-bi/service-machine-learning-integration)

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
    # Replace filename if needed.
    model_path = os.path.join(os.getenv('AZUREML_MODEL_DIR'), 'model_file.pkl')
    # Deserialize the model file back into a sklearn model.
    model = joblib.load(model_path)


input_sample = pd.DataFrame(data=[{
    # This is a decimal type sample. Use the data type that reflects this column in your data.
    "input_name_1": 5.1,
    # This is a string type sample. Use the data type that reflects this column in your data.
    "input_name_2": "value2",
    # This is an integer type sample. Use the data type that reflects this column in your data.
    "input_name_3": 3
}])

# This is an integer type sample. Use the data type that reflects the expected result.
output_sample = np.array([0])


@input_schema('data', PandasParameterType(input_sample))
@output_schema(NumpyParameterType(output_sample))
def run(data):
    try:
        result = model.predict(data)
        # You can return any data type, as long as it is JSON serializable.
        return result.tolist()
    except Exception as e:
        error = str(e)
        return error
```

자세한 예제는 다음 스크립트를 참조하십시오.

* [PyTorch](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/ml-frameworks/pytorch)
* [Tensorflow](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/ml-frameworks/tensorflow)
* [Keras](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-keras)
* [AutoML](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning/classification-bank-marketing-all-features)
* [ONNX](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/deployment/onnx/)
* [이진 데이터](#binary)
* [CORS](#cors)

### <a name="3-define-inference-configuration"></a><a id="script"></a>3. 추론 구성 정의
    
다음 예제에서는 작업 영역에서 환경을 로드한 다음 추론 구성과 함께 사용하는 것을 보여 줍니다.

```python
from azureml.core.environment import Environment
from azureml.core.model import InferenceConfig


myenv = Environment.get(workspace=ws, name='myenv', version='1')
inference_config = InferenceConfig(entry_script='path-to-score.py',
                                   environment=myenv)
```

환경에 대한 자세한 내용은 [교육 및 배포를 위한 환경 만들기 및 관리를](how-to-use-environments.md)참조하십시오.

추론 구성에 대한 자세한 내용은 [InferenceConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.inferenceconfig?view=azure-ml-py) 클래스 설명서를 참조하십시오.

추론 구성이 있는 사용자 지정 Docker 이미지 사용에 대한 자세한 내용은 [사용자 지정 Docker 이미지를 사용하여 모델을 배포하는 방법을](how-to-deploy-custom-docker-image.md)참조하십시오.

#### <a name="cli-example-of-inferenceconfig"></a>추론 구성의 CLI 예

[!INCLUDE [inference config](../../includes/machine-learning-service-inference-config.md)]

다음 명령은 CLI를 사용하여 모델을 배포하는 방법을 보여 줍니다.

```azurecli-interactive
az ml model deploy -n myservice -m mymodel:1 --ic inferenceconfig.json
```

이 예제에서 구성은 다음 설정을 지정합니다.

* 모델에 파이썬이 필요합니다.
* 배포된 서비스로 전송된 웹 요청을 처리하는 데 사용되는 [항목 스크립트입니다.](#script)
* 추론에 필요한 파이썬 패키지를 설명하는 Conda 파일입니다.

추론 구성이 있는 사용자 지정 Docker 이미지 사용에 대한 자세한 내용은 [사용자 지정 Docker 이미지를 사용하여 모델을 배포하는 방법을](how-to-deploy-custom-docker-image.md)참조하십시오.

### <a name="4-optional-profile-your-model-to-determine-resource-utilization"></a><a id="profilemodel"></a>4. (선택 사항) 리소스 사용률을 결정하기 위해 모델 프로파일

모델을 등록하고 배포에 필요한 다른 구성 요소를 준비한 후에는 배포된 서비스에 필요한 CPU와 메모리를 결정할 수 있습니다. 프로파일링은 모델을 실행하는 서비스를 테스트하고 CPU 사용량, 메모리 사용량 및 응답 대기 시간과 같은 정보를 반환합니다. 또한 리소스 사용량을 기반으로 CPU 및 메모리에 대한 권장 사항을 제공합니다.

모델 프로필을 프로파일로 연결하려면 다음이 필요합니다.
* 등록된 모델입니다.
* 항목 스크립트 및 유추 환경 정의에 기반한 추론 구성입니다.
* 각 행에 샘플 요청 데이터를 나타내는 문자열이 포함된 단일 열 테이블 형식 데이터 집합입니다.

> [!IMPORTANT]
> 이 시점에서는 문자열 직렬화된 json, 텍스트, 문자열 직렬화된 이미지 등과 같은 요청 데이터가 문자열로 예상되는 서비스의 프로파일링만 지원합니다. 데이터 집합(문자열)의 각 행의 내용은 HTTP 요청본문에 넣고 점수를 매기기 위해 모델을 캡슐화하는 서비스로 전송됩니다.

다음은 들어오는 요청 데이터에 직렬화된 json을 포함할 것으로 예상되는 서비스를 프로파일로 입력 데이터 집합을 생성하는 방법의 예입니다. 이 경우 동일한 요청 데이터 콘텐츠의 100개 인스턴스를 기반으로 데이터 집합을 만들었습니다. 실제 시나리오에서는 특히 모델 리소스 사용/동작이 입력에 종속된 경우 다양한 입력을 포함하는 더 큰 데이터 집합을 사용하는 것이 좋습니다.

```python
import json
from azureml.core import Datastore
from azureml.core.dataset import Dataset
from azureml.data import dataset_type_definitions

input_json = {'data': [[1, 2, 3, 4, 5, 6, 7, 8, 9, 10],
                       [10, 9, 8, 7, 6, 5, 4, 3, 2, 1]]}
# create a string that can be utf-8 encoded and
# put in the body of the request
serialized_input_json = json.dumps(input_json)
dataset_content = []
for i in range(100):
    dataset_content.append(serialized_input_json)
dataset_content = '\n'.join(dataset_content)
file_name = 'sample_request_data.txt'
f = open(file_name, 'w')
f.write(dataset_content)
f.close()

# upload the txt file created above to the Datastore and create a dataset from it
data_store = Datastore.get_default(ws)
data_store.upload_files(['./' + file_name], target_path='sample_request_data')
datastore_path = [(data_store, 'sample_request_data' +'/' + file_name)]
sample_request_data = Dataset.Tabular.from_delimited_files(
    datastore_path, separator='\n',
    infer_column_types=True,
    header=dataset_type_definitions.PromoteHeadersBehavior.NO_HEADERS)
sample_request_data = sample_request_data.register(workspace=ws,
                                                   name='sample_request_data',
                                                   create_new_version=True)
```

샘플 요청 데이터가 포함된 데이터 집합이 준비되면 추론 구성을 만듭니다. 추론 구성은 score.py 및 환경 정의를 기반으로 합니다. 다음 예제에서는 추론 구성을 만들고 프로파일링을 실행하는 방법을 보여 줍니다.

```python
from azureml.core.model import InferenceConfig, Model
from azureml.core.dataset import Dataset


model = Model(ws, id=model_id)
inference_config = InferenceConfig(entry_script='path-to-score.py',
                                   environment=myenv)
input_dataset = Dataset.get_by_name(workspace=ws, name='sample_request_data')
profile = Model.profile(ws,
            'unique_name',
            [model],
            inference_config,
            input_dataset=input_dataset)

profile.wait_for_completion(True)

# see the result
details = profile.get_details()
```

다음 명령은 CLI를 사용하여 모델을 프로파일링하는 방법을 보여 줍니다.

```azurecli-interactive
az ml model profile -g <resource-group-name> -w <workspace-name> --inference-config-file <path-to-inf-config.json> -m <model-id> --idi <input-dataset-id> -n <unique-name>
```

> [!TIP]
> 프로파일링을 통해 반환되는 정보를 유지하려면 모델에 태그 또는 속성을 사용합니다. 태그 또는 속성을 사용하면 모델 레지스트리에 모델이 있는 데이터가 저장됩니다. 다음 예제에서는 `requestedCpu` 및 `requestedMemoryInGb` 정보를 포함하는 새 태그를 추가하는 것을 보여 줍니다.
>
> ```python
> model.add_tags({'requestedCpu': details['requestedCpu'],
>                 'requestedMemoryInGb': details['requestedMemoryInGb']})
> ```
>
> ```azurecli-interactive
> az ml model profile -g <resource-group-name> -w <workspace-name> --i <model-id> --add-tag requestedCpu=1 --add-tag requestedMemoryInGb=0.5
> ```

## <a name="deploy-to-target"></a>대상에 배포

배포는 추론 구성 배포 구성을 사용하여 모델을 배포합니다. 배포 프로세스는 계산 대상에 관계없이 유사합니다. AKS 클러스터에 대한 참조를 제공해야 하기 때문에 AKS에 배포하는 것은 약간 다릅니다.

### <a name="choose-a-compute-target"></a>계산 대상 선택

다음 계산 대상을 사용하거나 리소스를 계산하여 웹 서비스 배포를 호스팅할 수 있습니다.

[!INCLUDE [aml-compute-target-deploy](../../includes/aml-compute-target-deploy.md)]

### <a name="define-your-deployment-configuration"></a>배포 구성 정의

모델을 배포하기 전에 배포 구성을 정의해야 합니다. *배포 구성은 웹 서비스를 호스트하는 계산 대상에 따라 다릅니다.* 예를 들어 모델을 로컬로 배포할 때 서비스가 요청을 수락하는 포트를 지정해야 합니다. 배포 구성은 항목 스크립트의 일부가 아닙니다. 모델 및 입력 스크립트를 호스트하는 계산 대상의 특성을 정의하는 데 사용됩니다.

예를 들어 작업 영역과 연결된 AZURE Kubernetes 서비스(AKS) 인스턴스가 아직 없는 경우 계산 리소스를 만들어야 할 수도 있습니다.

다음 표에서는 각 계산 대상에 대한 배포 구성을 만드는 예제를 제공합니다.

| 컴퓨팅 대상 | 배포 구성 예제 |
| ----- | ----- |
| 로컬 | `deployment_config = LocalWebservice.deploy_configuration(port=8890)` |
| Azure Container Instances | `deployment_config = AciWebservice.deploy_configuration(cpu_cores = 1, memory_gb = 1)` |
| Azure Kubernetes Service | `deployment_config = AksWebservice.deploy_configuration(cpu_cores = 1, memory_gb = 1)` |

로컬, Azure 컨테이너 인스턴스 및 AKS 웹 서비스에 대한 `azureml.core.webservice`클래스를 다음에서 가져올 수 있습니다.

```python
from azureml.core.webservice import AciWebservice, AksWebservice, LocalWebservice
```

### <a name="securing-deployments-with-tls"></a>TLS를 통해 배포 보안

웹 서비스 배포를 보호하는 방법에 대한 자세한 내용은 [TLS 및 배포 를 활성화](how-to-secure-web-service.md#enable)를 참조하십시오.

### <a name="local-deployment"></a><a id="local"></a>로컬 배포

모델을 로컬로 배포하려면 로컬 컴퓨터에 Docker를 설치해야 합니다.

#### <a name="using-the-sdk"></a>SDK 사용

```python
from azureml.core.webservice import LocalWebservice, Webservice

deployment_config = LocalWebservice.deploy_configuration(port=8890)
service = Model.deploy(ws, "myservice", [model], inference_config, deployment_config)
service.wait_for_deployment(show_output = True)
print(service.state)
```

자세한 내용은 [LocalWebservice,](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.local.localwebservice?view=azure-ml-py) [Model.deploy()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#deploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-)및 웹 [서비스에](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.webservice?view=azure-ml-py)대한 설명서를 참조하십시오.

#### <a name="using-the-cli"></a>CLI 사용

CLI를 사용하여 모델을 배포하려면 다음 명령을 사용합니다. 등록된 `mymodel:1` 모델의 이름 및 버전으로 바꿉니다.

```azurecli-interactive
az ml model deploy -m mymodel:1 --ic inferenceconfig.json --dc deploymentconfig.json
```

[!INCLUDE [aml-local-deploy-config](../../includes/machine-learning-service-local-deploy-config.md)]

자세한 내용은 az [ml 모델 배포](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/model?view=azure-cli-latest#ext-azure-cli-ml-az-ml-model-deploy) 설명서를 참조하십시오.

### <a name="understanding-service-state"></a>서비스 상태 이해

모델 배포 중에 완전히 배포하는 동안 서비스 상태가 변경될 수 있습니다.

다음 표는 다양한 서비스 상태에 대해 설명합니다.

| 웹 서비스 상태 | 설명 | 최종 상태?
| ----- | ----- | ----- |
| 전환 | 서비스는 배포 중입니다. | 예 |
| 비정상 | 서비스가 배포되었지만 현재 연결할 수 없습니다.  | 예 |
| 예약 할 수 없습니다. | 리소스 부족으로 현재 서비스를 배포할 수 없습니다. | 예 |
| 실패 | 오류 또는 충돌로 인해 서비스가 배포되지 않았습니다. | 예 |
| Healthy | 서비스가 정상이며 끝점을 사용할 수 있습니다. | 예 |

### <a name="compute-instance-web-service-devtest"></a><a id="notebookvm"></a>인스턴스 웹 서비스 계산(개발/테스트)

Azure 기계 학습 계산 인스턴스에 모델 배포 를 [참조합니다.](how-to-deploy-local-container-notebook-vm.md)

### <a name="azure-container-instances-devtest"></a><a id="aci"></a>Azure 컨테이너 인스턴스(개발/테스트)

[Azure 컨테이너 인스턴스에 대한 배포를](how-to-deploy-azure-container-instance.md)참조하십시오.

### <a name="azure-kubernetes-service-devtest-and-production"></a><a id="aks"></a>Azure Kubernetes 서비스(개발/테스트 및 프로덕션)

[Azure Kubernetes 서비스에 대한 배포를](how-to-deploy-azure-kubernetes-service.md)참조하십시오.

### <a name="ab-testing-controlled-rollout"></a>A/B 테스트(제어된 롤아웃)
자세한 내용은 [ML 모델의 제어된 롤아웃을](how-to-deploy-azure-kubernetes-service.md#deploy-models-to-aks-using-controlled-rollout-preview) 참조하십시오.

## <a name="consume-web-services"></a>웹 서비스 사용

배포된 모든 웹 서비스는 REST 엔드포인트를 제공하므로 모든 프로그래밍 언어로 클라이언트 응용 프로그램을 만들 수 있습니다.
서비스에 대해 키 기반 인증을 사용하도록 설정한 경우 요청 헤더에 토큰으로 서비스 키를 제공해야 합니다.
서비스에 대해 토큰 기반 인증을 사용하도록 설정한 경우 요청 헤더에 Azure 기계 학습 JSON 웹 토큰(JWT)을 보유자 토큰으로 제공해야 합니다. 

주요 차이점은 **키가 정적이며 수동으로 다시 생성할 수**있으며 만료 시 **토큰을 새로 고쳐야 한다는**것입니다. 키 기반 인증은 Azure 컨테이너 인스턴스 및 Azure Kubernetes 서비스 배포 웹 서비스에 대해 지원되며 토큰 기반 인증은 Azure Kubernetes 서비스 **배포에만** 사용할 수 있습니다. 자세한 정보 및 특정 코드 샘플은 인증 [방법을](how-to-setup-authentication.md#web-service-authentication) 참조하십시오.

> [!TIP]
> 서비스를 배포한 후 스키마 JSON 문서를 검색할 수 있습니다. 배포된 [swagger_uri property](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.local.localwebservice?view=azure-ml-py#swagger-uri) 웹 `service.swagger_uri`서비스(예:)에서 swagger_uri 속성을 사용하여 URI를 로컬 웹 서비스의 Swagger 파일로 가져옵니다.

### <a name="request-response-consumption"></a>요청 응답 소비

다음은 파이썬에서 서비스를 호출하는 방법의 예입니다.
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

자세한 내용은 [웹 서비스를 사용할 클라이언트 응용 프로그램 만들기를](how-to-consume-web-service.md)참조하십시오.

### <a name="web-service-schema-openapi-specification"></a>웹 서비스 스키마(OpenAPI 사양)

배포와 함께 자동 스키마 생성을 사용하는 경우 [swagger_uri 속성을](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.local.localwebservice?view=azure-ml-py#swagger-uri)사용하여 서비스에 대한 OpenAPI 사양의 주소를 얻을 수 있습니다. (예: `print(service.swagger_uri)`.) GET 요청을 사용하거나 브라우저에서 URI를 열어 사양을 검색합니다.

다음 JSON 문서는 배포에 대해 생성된 스키마(OpenAPI 사양)의 예입니다.

```json
{
    "swagger": "2.0",
    "info": {
        "title": "myservice",
        "description": "API specification for Azure Machine Learning myservice",
        "version": "1.0"
    },
    "schemes": [
        "https"
    ],
    "consumes": [
        "application/json"
    ],
    "produces": [
        "application/json"
    ],
    "securityDefinitions": {
        "Bearer": {
            "type": "apiKey",
            "name": "Authorization",
            "in": "header",
            "description": "For example: Bearer abc123"
        }
    },
    "paths": {
        "/": {
            "get": {
                "operationId": "ServiceHealthCheck",
                "description": "Simple health check endpoint to ensure the service is up at any given point.",
                "responses": {
                    "200": {
                        "description": "If service is up and running, this response will be returned with the content 'Healthy'",
                        "schema": {
                            "type": "string"
                        },
                        "examples": {
                            "application/json": "Healthy"
                        }
                    },
                    "default": {
                        "description": "The service failed to execute due to an error.",
                        "schema": {
                            "$ref": "#/definitions/ErrorResponse"
                        }
                    }
                }
            }
        },
        "/score": {
            "post": {
                "operationId": "RunMLService",
                "description": "Run web service's model and get the prediction output",
                "security": [
                    {
                        "Bearer": []
                    }
                ],
                "parameters": [
                    {
                        "name": "serviceInputPayload",
                        "in": "body",
                        "description": "The input payload for executing the real-time machine learning service.",
                        "schema": {
                            "$ref": "#/definitions/ServiceInput"
                        }
                    }
                ],
                "responses": {
                    "200": {
                        "description": "The service processed the input correctly and provided a result prediction, if applicable.",
                        "schema": {
                            "$ref": "#/definitions/ServiceOutput"
                        }
                    },
                    "default": {
                        "description": "The service failed to execute due to an error.",
                        "schema": {
                            "$ref": "#/definitions/ErrorResponse"
                        }
                    }
                }
            }
        }
    },
    "definitions": {
        "ServiceInput": {
            "type": "object",
            "properties": {
                "data": {
                    "type": "array",
                    "items": {
                        "type": "array",
                        "items": {
                            "type": "integer",
                            "format": "int64"
                        }
                    }
                }
            },
            "example": {
                "data": [
                    [ 10, 9, 8, 7, 6, 5, 4, 3, 2, 1 ]
                ]
            }
        },
        "ServiceOutput": {
            "type": "array",
            "items": {
                "type": "number",
                "format": "double"
            },
            "example": [
                3726.995
            ]
        },
        "ErrorResponse": {
            "type": "object",
            "properties": {
                "status_code": {
                    "type": "integer",
                    "format": "int32"
                },
                "message": {
                    "type": "string"
                }
            }
        }
    }
}
```

자세한 내용은 [OpenAPI 사양을](https://swagger.io/specification/)참조하십시오.

사양에서 클라이언트 라이브러리를 만들 수 있는 유틸리티의 경우 [swagger-codegen](https://github.com/swagger-api/swagger-codegen)을 참조하십시오.

### <a name="batch-inference"></a><a id="azuremlcompute"></a>일괄 처리 추론
Azure 기계 학습 계산 대상은 Azure 기계 학습에 의해 만들어지고 관리됩니다. Azure 기계 학습 파이프라인의 일괄 처리 예측에 사용할 수 있습니다.

Azure 기계 학습 계산을 사용하여 일괄 처리 추론을 [연습하려면 일괄 처리 예측을 실행하는 방법을](tutorial-pipeline-batch-scoring-classification.md)참조하세요.

### <a name="iot-edge-inference"></a><a id="iotedge"></a>IoT 에지 추론
에지에 배포에 대한 지원은 미리 보기상태입니다. 자세한 내용은 [Azure 기계 학습을 IoT Edge 모듈로 배포를](https://docs.microsoft.com/azure/iot-edge/tutorial-deploy-machine-learning)참조하십시오.


## <a name="update-web-services"></a><a id="update"></a>웹 서비스 업데이트

[!INCLUDE [aml-update-web-service](../../includes/machine-learning-update-web-service.md)]

## <a name="continuously-deploy-models"></a>모델을 지속적으로 배포

[Azure DevOps에](https://azure.microsoft.com/services/devops/)대한 기계 학습 확장을 사용하여 모델을 지속적으로 배포할 수 있습니다. Azure DevOps에 대한 기계 학습 확장을 사용하여 새 기계 학습 모델이 Azure 기계 학습 작업 영역에 등록될 때 배포 파이프라인을 트리거할 수 있습니다.

1. [Azure 파이프라인에](https://docs.microsoft.com/azure/devops/pipelines/get-started/pipelines-sign-up?view=azure-devops)등록하면 모든 플랫폼 또는 클라우드에 응용 프로그램을 지속적으로 통합하고 전달할 수 있습니다. Azure 파이프라인은 [기계 학습 파이프라인과](concept-ml-pipelines.md#compare)다 다 다.

1. [Azure DevOps 프로젝트를 만듭니다.](https://docs.microsoft.com/azure/devops/organizations/projects/create-project?view=azure-devops)

1. Azure [파이프라인에 대한 기계 학습 확장을 설치합니다.](https://marketplace.visualstudio.com/items?itemName=ms-air-aiagility.vss-services-azureml&targetId=6756afbe-7032-4a36-9cb6-2771710cadc2&utm_source=vstsproduct&utm_medium=ExtHubManageList)

1. 서비스 연결을 사용하여 아티팩트에 액세스할 수 있도록 Azure Machine Learning 작업 영역에 대한 서비스 주체 연결을 설정합니다. 프로젝트 설정으로 이동하여 **서비스 연결을**선택한 다음 **Azure 리소스 관리자를**선택합니다.

    [![Azure 리소스 관리자 선택](media/how-to-deploy-and-where/view-service-connection.png)](media/how-to-deploy-and-where/view-service-connection-expanded.png)

1. 범위 **수준** 목록에서 **AzureMLWorkspace를**선택한 다음 나머지 값을 입력합니다.

    ![AzureML워크스페이스 선택](./media/how-to-deploy-and-where/resource-manager-connection.png)

1. Azure 파이프라인을 사용하여 기계 학습 모델을 지속적으로 배포하려면 파이프라인 아래에 **있는 릴리스를**선택합니다. 새 아티팩트를 추가한 다음 **AzureML 모델** 아티팩트 및 이전에 만든 서비스 연결을 선택합니다. 배포를 트리거할 모델 및 버전을 선택합니다.

    [![AzureML 모델 선택](media/how-to-deploy-and-where/enable-modeltrigger-artifact.png)](media/how-to-deploy-and-where/enable-modeltrigger-artifact-expanded.png)

1. 모델 아티팩트에서 모델 트리거를 활성화합니다. 트리거를 켜면 해당 모델의 지정된 버전(즉, 최신 버전)이 작업 영역에 등록될 때마다 Azure DevOps 릴리스 파이프라인이 트리거됩니다.

    [![모델 트리거 사용](media/how-to-deploy-and-where/set-modeltrigger.png)](media/how-to-deploy-and-where/set-modeltrigger-expanded.png)

자세한 샘플 프로젝트 및 예제는 GitHub에서 다음 샘플 리포지토리를 참조하십시오.

* [마이크로소프트/MLOps](https://github.com/Microsoft/MLOps)
* [마이크로소프트/MLOps파이썬](https://github.com/microsoft/MLOpsPython)

## <a name="download-a-model"></a>모델 다운로드
모델을 다운로드하여 자체 실행 환경에서 사용하려면 다음 SDK / CLI 명령을 사용하여 모델을 다운로드할 수 있습니다.

Sdk:
```python
model_path = Model(ws,'mymodel').download()
```

CLI:
```azurecli-interactive
az ml model download --model-id mymodel:1 --target-dir model_folder
```

## <a name="preview-no-code-model-deployment"></a>(미리 보기) 코드 없음 모델 배포

코드 없음 모델 배포는 현재 미리 보기 중이며 다음 기계 학습 프레임워크를 지원합니다.

### <a name="tensorflow-savedmodel-format"></a>텐서플로우 저장된 모델 형식
코드 없는 모델 배포와 함께 작동하려면 텐서플로우 모델을 **SaveModel 형식으로** 등록해야 합니다.

저장된 모델을 만드는 방법에 대한 자세한 내용은 [이 링크를](https://www.tensorflow.org/guide/saved_model) 참조하십시오.

```python
from azureml.core import Model

model = Model.register(workspace=ws,
                       model_name='flowers',                        # Name of the registered model in your workspace.
                       model_path='./flowers_model',                # Local Tensorflow SavedModel folder to upload and register as a model.
                       model_framework=Model.Framework.TENSORFLOW,  # Framework used to create the model.
                       model_framework_version='1.14.0',            # Version of Tensorflow used to create the model.
                       description='Flowers model')

service_name = 'tensorflow-flower-service'
service = Model.deploy(ws, service_name, [model])
```

### <a name="onnx-models"></a>ONNX 모델

ONNX 모델 등록 및 배포는 모든 ONNX 추론 그래프에 대해 지원됩니다. 프로세스 및 후처리 단계는 현재 지원되지 않습니다.

다음은 MNIST ONNX 모델을 등록하고 배포하는 방법의 예입니다.

```python
from azureml.core import Model

model = Model.register(workspace=ws,
                       model_name='mnist-sample',                  # Name of the registered model in your workspace.
                       model_path='mnist-model.onnx',              # Local ONNX model to upload and register as a model.
                       model_framework=Model.Framework.ONNX ,      # Framework used to create the model.
                       model_framework_version='1.3',              # Version of ONNX used to create the model.
                       description='Onnx MNIST model')

service_name = 'onnx-mnist-service'
service = Model.deploy(ws, service_name, [model])
```

Pytorch를 사용하는 경우 [PyTorch에서 ONNX로 모델을 내보내는](https://github.com/onnx/tutorials/blob/master/tutorials/PytorchOnnxExport.ipynb) 데 변환 및 제한 사항에 대한 세부 정보가 있습니다. 

### <a name="scikit-learn-models"></a>시킷 학습 모델

모든 기본 제공 scikit-learn 모델 유형에 대해 코드 모델 배포가 지원되지 않습니다.

다음은 추가 코드 없이 sklearn 모델을 등록하고 배포하는 방법의 예입니다.

```python
from azureml.core import Model
from azureml.core.resource_configuration import ResourceConfiguration

model = Model.register(workspace=ws,
                       model_name='my-sklearn-model',                # Name of the registered model in your workspace.
                       model_path='./sklearn_regression_model.pkl',  # Local file to upload and register as a model.
                       model_framework=Model.Framework.SCIKITLEARN,  # Framework used to create the model.
                       model_framework_version='0.19.1',             # Version of scikit-learn used to create the model.
                       resource_configuration=ResourceConfiguration(cpu=1, memory_in_gb=0.5),
                       description='Ridge regression model to predict diabetes progression.',
                       tags={'area': 'diabetes', 'type': 'regression'})
                       
service_name = 'my-sklearn-service'
service = Model.deploy(ws, service_name, [model])
```

참고: predict_proba 지원하는 모델은 기본적으로 해당 메서드를 사용합니다. 이를 재정의하여 예측을 사용하려면 POST 본문을 다음과 같이 수정할 수 있습니다.
```python
import json


input_payload = json.dumps({
    'data': [
        [ 0.03807591,  0.05068012,  0.06169621, 0.02187235, -0.0442235,
         -0.03482076, -0.04340085, -0.00259226, 0.01990842, -0.01764613]
    ],
    'method': 'predict'  # If you have a classification model, the default behavior is to run 'predict_proba'.
})

output = service.run(input_payload)

print(output)
```

참고: 이러한 종속성은 미리 빌드된 sklearn 추론 컨테이너에 포함됩니다.

```yaml
    - azureml-defaults
    - inference-schema[numpy-support]
    - scikit-learn
    - numpy
```

## <a name="package-models"></a>패키지 모델

경우에 따라 모델을 배포하지 않고 Docker 이미지를 만들 수도 있습니다(예: Azure [App Service에 배포하려는](how-to-deploy-app-service.md)경우). 또는 이미지를 다운로드하여 로컬 Docker 설치에서 실행할 수 있습니다. 이미지를 빌드하고, 검사하고, 수정하고, 수동으로 이미지를 빌드하는 데 사용되는 파일을 다운로드할 수도 있습니다.

모델 패키징을 사용하면 이러한 작업을 수행할 수 있습니다. 웹 서비스로 모델을 호스트하는 데 필요한 모든 자산을 패키징하고 완전히 빌드된 Docker 이미지 또는 모델을 빌드하는 데 필요한 파일을 다운로드할 수 있습니다. 모델 패키징을 사용하는 방법에는 두 가지가 있습니다.

**패키지 모델 다운로드:** 웹 서비스로 호스팅하는 데 필요한 모델 및 기타 파일이 포함된 Docker 이미지를 다운로드합니다.

**도커파일 생성:** Dockerfile 이미지를 빌드하는 데 필요한 Dockerfile, 모델, 입력 스크립트 및 기타 자산을 다운로드합니다. 그런 다음 이미지를 로컬로 빌드하기 전에 파일을 검사하거나 변경할 수 있습니다.

두 패키지 모두 로컬 Docker 이미지를 얻는 데 사용할 수 있습니다.

> [!TIP]
> 패키지 만들기는 모델을 배포하는 것과 유사합니다. 등록된 모델과 추론 구성을 사용합니다.

> [!IMPORTANT]
> 완전히 빌드된 이미지를 다운로드하거나 로컬로 이미지를 빌드하려면 개발 환경에 [Docker를](https://www.docker.com) 설치해야 합니다.

### <a name="download-a-packaged-model"></a>패키지 모델 다운로드

다음 예제에서는 작업 영역에 대 한 Azure 컨테이너 레지스트리에 등록 된 이미지를 빌드 합니다.

```python
package = Model.package(ws, [model], inference_config)
package.wait_for_creation(show_output=True)
```

패키지를 만든 후 이미지를 로컬 `package.pull()` Docker 환경으로 끌어올 수 있습니다. 이 명령의 출력에는 이미지 이름이 표시됩니다. 다음은 그 예입니다. 

`Status: Downloaded newer image for myworkspacef78fd10.azurecr.io/package:20190822181338`. 

모델을 다운로드한 후 명령을 `docker images` 사용하여 로컬 이미지를 나열합니다.

```text
REPOSITORY                               TAG                 IMAGE ID            CREATED             SIZE
myworkspacef78fd10.azurecr.io/package    20190822181338      7ff48015d5bd        4 minutes ago       1.43 GB
```

이 이미지를 기반으로 로컬 컨테이너를 시작하려면 다음 명령을 사용하여 셸 또는 명령줄에서 명명된 컨테이너를 시작합니다. 값을 `<imageid>` `docker images` 명령에서 반환하는 이미지 ID로 바꿉습니다.

```bash
docker run -p 6789:5001 --name mycontainer <imageid>
```

이 명령은 의 이미지의 `myimage`최신 버전을 시작합니다. 로컬 포트(6789)를 웹 서비스가 수신 대기 중인 컨테이너의 포트에 매핑합니다(5001). 또한 컨테이너에 이름을 `mycontainer` 할당하므로 컨테이너를 더 쉽게 중지할 수 있습니다. 컨테이너가 시작된 후 `http://localhost:6789/score`에 요청을 제출할 수 있습니다.

### <a name="generate-a-dockerfile-and-dependencies"></a>Dockerfile 및 종속성 생성

다음 예제에서는 이미지를 로컬로 빌드하는 데 필요한 Dockerfile, 모델 및 기타 자산을 다운로드하는 방법을 보여 주습니다. 매개 `generate_dockerfile=True` 변수는 완전히 빌드된 이미지가 아니라 파일을 원한다는 것을 나타냅니다.

```python
package = Model.package(ws, [model], inference_config, generate_dockerfile=True)
package.wait_for_creation(show_output=True)
# Download the package.
package.save("./imagefiles")
# Get the Azure container registry that the model/Dockerfile uses.
acr=package.get_container_registry()
print("Address:", acr.address)
print("Username:", acr.username)
print("Password:", acr.password)
```

이 코드는 디렉토리에 이미지를 빌드하는 데 필요한 파일을 다운로드합니다. `imagefiles` 저장된 파일에 포함된 Dockerfile은 Azure 컨테이너 레지스트리에 저장된 기본 이미지를 참조합니다. 로컬 Docker 설치에서 이미지를 빌드할 때 레지스트리를 인증하려면 주소, 사용자 이름 및 암호를 사용해야 합니다. 다음 단계를 사용하여 로컬 Docker 설치를 사용하여 이미지를 빌드합니다.

1. 셸 또는 명령줄 세션에서 다음 명령을 사용하여 Azure 컨테이너 레지스트리를 사용하여 Docker를 인증합니다. 을 `<address>` `<username>`을 `<password>` 에서 검색한 값으로 `package.get_container_registry()`대체합니다.

    ```bash
    docker login <address> -u <username> -p <password>
    ```

2. 이미지를 빌드하려면 다음 명령을 사용합니다. 파일을 `<imagefiles>` `package.save()` 저장한 디렉터리 경로로 바꿉습니다.

    ```bash
    docker build --tag myimage <imagefiles>
    ```

    이 명령은 이미지 이름을 `myimage`로 설정합니다.

이미지가 빌드되었는지 확인하려면 `docker images` 명령을 사용합니다. 목록에 `myimage` 이미지가 표시됩니다.

```text
REPOSITORY      TAG                 IMAGE ID            CREATED             SIZE
<none>          <none>              2d5ee0bf3b3b        49 seconds ago      1.43 GB
myimage         latest              739f22498d64        3 minutes ago       1.43 GB
```

이 이미지를 기반으로 새 컨테이너를 시작하려면 다음 명령을 사용합니다.

```bash
docker run -p 6789:5001 --name mycontainer myimage:latest
```

이 명령은 의 이미지의 `myimage`최신 버전을 시작합니다. 로컬 포트(6789)를 웹 서비스가 수신 대기 중인 컨테이너의 포트에 매핑합니다(5001). 또한 컨테이너에 이름을 `mycontainer` 할당하므로 컨테이너를 더 쉽게 중지할 수 있습니다. 컨테이너가 시작된 후 `http://localhost:6789/score`에 요청을 제출할 수 있습니다.

### <a name="example-client-to-test-the-local-container"></a>로컬 컨테이너를 테스트하는 클라이언트 예제

다음 코드는 컨테이너와 함께 사용할 수 있는 Python 클라이언트의 예입니다.

```python
import requests
import json

# URL for the web service.
scoring_uri = 'http://localhost:6789/score'

# Two sets of data to score, so we get two results back.
data = {"data":
        [
            [ 1,2,3,4,5,6,7,8,9,10 ],
            [ 10,9,8,7,6,5,4,3,2,1 ]
        ]
        }
# Convert to JSON string.
input_data = json.dumps(data)

# Set the content type.
headers = {'Content-Type': 'application/json'}

# Make the request and display the response.
resp = requests.post(scoring_uri, input_data, headers=headers)
print(resp.text)
```

예를 들어 다른 프로그래밍 언어의 클라이언트는 [웹 서비스로 배포된 모델 사용(을 참조)을](how-to-consume-web-service.md)참조하십시오.

### <a name="stop-the-docker-container"></a>Docker 컨테이너 중지

컨테이너를 중지하려면 다른 셸 또는 명령줄에서 다음 명령을 사용합니다.

```bash
docker kill mycontainer
```

## <a name="clean-up-resources"></a>리소스 정리

배포된 웹 서비스를 삭제하려면 `service.delete()`를 사용합니다.
등록된 모델을 삭제하려면 `model.delete()`를 사용합니다.

자세한 내용은 [WebService.delete()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice(class)?view=azure-ml-py#delete--) 및 [Model.delete()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#delete--)에 대한 설명서를 참조하십시오.

<a id="advanced-entry-script"></a>
## <a name="advanced-entry-script-authoring"></a>고급 엔트리 스크립트 작성

<a id="binary"></a>

### <a name="binary-data"></a>이진 데이터

모델이 이미지와 같은 이진 데이터를 허용하는 경우 원시 `score.py` HTTP 요청을 수락하려면 배포에 사용된 파일을 수정해야 합니다. 원시 데이터를 허용하려면 `AMLRequest` 입력 스크립트의 클래스를 `@rawhttp` 사용하고 데코레이터를 함수에 `run()` 추가합니다.

다음은 이진 데이터를 `score.py` 허용하는 예제입니다.

```python
from azureml.contrib.services.aml_request import AMLRequest, rawhttp
from azureml.contrib.services.aml_response import AMLResponse


def init():
    print("This is init()")


@rawhttp
def run(request):
    print("This is run()")
    print("Request: [{0}]".format(request))
    if request.method == 'GET':
        # For this example, just return the URL for GETs.
        respBody = str.encode(request.full_path)
        return AMLResponse(respBody, 200)
    elif request.method == 'POST':
        reqBody = request.get_data(False)
        # For a real-world solution, you would load the data from reqBody
        # and send it to the model. Then return the response.

        # For demonstration purposes, this example just returns the posted data as the response.
        return AMLResponse(reqBody, 200)
    else:
        return AMLResponse("bad request", 500)
```

> [!IMPORTANT]
> `AMLRequest` 클래스가 `azureml.contrib` 네임스페이스에 있습니다. 이 네임스페이스의 엔터티는 서비스를 개선하기 위해 작업할 때 자주 변경됩니다. 이 네임스페이스의 모든 것은 Microsoft에서 완전히 지원하지 않는 미리 보기로 간주되어야 합니다.
>
> 로컬 개발 환경에서 이를 테스트해야 하는 경우 다음 명령을 사용하여 구성 요소를 설치할 수 있습니다.
>
> ```shell
> pip install azureml-contrib-services
> ```

클래스는 `AMLRequest` score.py 원시 게시 된 데이터에 액세스 할 수 있습니다, 클라이언트 측 구성 요소가 없습니다. 클라이언트에서 정상적으로 데이터를 게시합니다. 예를 들어 다음 Python 코드는 이미지 파일을 읽고 데이터를 게시합니다.

```python
import requests
# Load image data
data = open('example.jpg', 'rb').read()
# Post raw data to scoring URI
res = request.post(url='<scoring-uri>', data=data, headers={'Content-Type': 'application/octet-stream'})
```

<a id="cors"></a>

### <a name="cross-origin-resource-sharing-cors"></a>원본 간 리소스 공유(CORS)

원본 간 리소스 공유는 웹 페이지의 리소스를 다른 도메인에서 요청할 수 있도록 하는 방법입니다. CORS는 클라이언트 요청과 함께 전송되고 서비스 응답과 함께 반환되는 HTTP 헤더를 통해 작동합니다. CORS 및 유효한 헤더에 대한 자세한 내용은 위키백과에서 [원본 간 리소스 공유를](https://en.wikipedia.org/wiki/Cross-origin_resource_sharing) 참조하십시오.

CORS를 지원하도록 모델 배포를 `AMLResponse` 구성하려면 항목 스크립트에서 클래스를 사용합니다. 이 클래스를 사용하면 응답 개체의 헤더를 설정할 수 있습니다.

다음 예제는 `Access-Control-Allow-Origin` 항목 스크립트의 응답에 대한 헤더를 설정합니다.

```python
from azureml.contrib.services.aml_response import AMLResponse

def init():
    print("This is init()")

def run(request):
    print("This is run()")
    print("Request: [{0}]".format(request))
    if request.method == 'GET':
        # For this example, just return the URL for GETs.
        respBody = str.encode(request.full_path)
        return AMLResponse(respBody, 200)
    elif request.method == 'POST':
        reqBody = request.get_data(False)
        # For a real-world solution, you would load the data from reqBody
        # and send it to the model. Then return the response.

        # For demonstration purposes, this example
        # adds a header and returns the request body.
        resp = AMLResponse(reqBody, 200)
        resp.headers['Access-Control-Allow-Origin'] = "http://www.example.com"
        return resp
    else:
        return AMLResponse("bad request", 500)
```

> [!IMPORTANT]
> `AMLResponse` 클래스가 `azureml.contrib` 네임스페이스에 있습니다. 이 네임스페이스의 엔터티는 서비스를 개선하기 위해 작업할 때 자주 변경됩니다. 이 네임스페이스의 모든 것은 Microsoft에서 완전히 지원하지 않는 미리 보기로 간주되어야 합니다.
>
> 로컬 개발 환경에서 이를 테스트해야 하는 경우 다음 명령을 사용하여 구성 요소를 설치할 수 있습니다.
>
> ```shell
> pip install azureml-contrib-services
> ```


> [!WARNING]
> Azure 기계 학습은 POST 및 GET 요청만 채점 서비스를 실행하는 컨테이너로 라우팅합니다. 이로 인해 OPTIONS 요청을 사용하여 CORS 요청을 사전 비행하기 위해 브라우저에 오류가 발생할 수 있습니다.
> 

## <a name="next-steps"></a>다음 단계

* [사용자 지정 Docker 이미지를 사용하여 모델을 배포하는 방법](how-to-deploy-custom-docker-image.md)
* [배포 문제 해결](how-to-troubleshoot-deployment.md)
* [TLS를 사용하여 Azure 기계 학습을 통해 웹 서비스 보호](how-to-secure-web-service.md)
* [웹 서비스로 배포된 Azure Machine Learning 모델 사용](how-to-consume-web-service.md)
* [Application Insights를 사용하여 Azure Machine Learning 모델 모니터링](how-to-enable-app-insights.md)
* [프로덕션 환경에서 모델용 데이터 수집](how-to-enable-data-collection.md)
* [모델 배포에 대한 이벤트 경고 및 트리거 만들기](how-to-use-event-grid.md)

