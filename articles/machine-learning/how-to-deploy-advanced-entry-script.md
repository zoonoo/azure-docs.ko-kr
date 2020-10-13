---
title: 고급 시나리오에 대 한 작성 항목 스크립트
titleSuffix: Azure Machine Learning entry script authoring
description: 배포 하는 동안 전처리 및 후 처리에 대 한 Azure Machine Learning 입력 스크립트를 작성 하는 방법을 알아봅니다.
author: gvashishtha
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 09/17/2020
ms.author: gopalv
ms.reviewer: larryfr
ms.custom: deploy
ms.openlocfilehash: 3bd4953812ec88f28ac16956a85c95afc5bb8a38
ms.sourcegitcommit: 090ea6e8811663941827d1104b4593e29774fa19
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/13/2020
ms.locfileid: "91999216"
---
# <a name="advanced-entry-script-authoring"></a>고급 항목 스크립트 작성

이 문서에서는 특수 한 사용 사례에 대 한 입력 스크립트를 작성 하는 방법을 보여 줍니다.

## <a name="prerequisites"></a>필수 구성 요소

이 문서에서는 Azure Machine Learning를 사용 하 여 배포 하려는 학습 된 machine learning 모델이 이미 있다고 가정 합니다. 모델 배포에 대해 자세히 알아보려면 [이 자습서](how-to-deploy-and-where.md)를 참조 하세요.

## <a name="automatically-generate-a-swagger-schema"></a>Swagger 스키마 자동 생성

웹 서비스에 대 한 스키마를 자동으로 생성 하려면 정의 된 형식 개체 중 하나에 대 한 생성자에서 입력 및/또는 출력의 샘플을 제공 합니다. 유형 및 샘플은 스키마를 자동으로 만드는 데 사용 됩니다. 그런 다음 Azure Machine Learning는 배포 중에 웹 서비스에 대 한 [Openapi](https://swagger.io/docs/specification/about/) (Swagger) 사양을 만듭니다.

현재 지원 되는 형식은 다음과 같습니다.

* `pandas`
* `numpy`
* `pyspark`
* 표준 Python 개체

스키마 생성을 사용 하려면 `inference-schema` 종속성 파일에 오픈 소스 패키지 버전 1.1.0을 포함 합니다. 이 패키지에 대 한 자세한 내용은을 참조 하십시오 [https://github.com/Azure/InferenceSchema](https://github.com/Azure/InferenceSchema) . 준수 하는 swagger 자동화 웹 서비스 사용을 생성 하기 위해 스크립트 실행 () 함수에는 다음과 같은 API 셰이프가 있어야 합니다.
* PandasDataframeParameterTypes를 포함 하는 "StandardPythonParameterType" 형식의 첫 번째 매개 변수, 명명 된 입력입니다.
* 형식이 "StandardPythonParameterType"이 고 중첩 되지 않은 GlobalParameter 라는 선택적인 두 번째 매개 변수입니다.
* PandasDataFrameParameterTypes를 포함 하는 중첩 된 "StandardPythonParameterType" 형식의 사전을 반환 합니다.
`input_sample`및 `output_sample` 변수에서 웹 서비스의 요청 및 응답 형식을 나타내는 입력 및 출력 샘플 형식을 정의 합니다. 함수에 대 한 입력 및 출력 함수 데코레이터에서 이러한 샘플을 사용 `run()` 합니다. 다음 scikit 예제에서는 스키마 생성을 사용 합니다.



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
    model_path = os.path.join(os.getenv('AZUREML_MODEL_DIR'), 'sklearn_mnist_model.pkl')

    # If your model were stored in the same directory as your score.py, you could also use the following:
    # model_path = os.path.abspath(os.path.join(os.path.dirname(__file_), 'sklearn_mnist_model.pkl')

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

## <a name="power-bi-compatible-endpoint"></a>Power BI 호환 끝점 

다음 예제에서는 위의 명령에 따라 API 셰이프를 정의 하는 방법을 보여 줍니다. 이 메서드는 Power BI에서 배포 된 웹 서비스를 사용 하는 데 지원 됩니다. [Power BI에서 웹 서비스를 사용 하는 방법에 대해 자세히 알아보세요](https://docs.microsoft.com/power-bi/service-machine-learning-integration).

```python
import json
import pickle
import numpy as np
import pandas as pd
import azureml.train.automl
from sklearn.externals import joblib
from sklearn.linear_model import Ridge

from inference_schema.schema_decorators import input_schema, output_schema
from inference_schema.parameter_types.standard_py_parameter_type import StandardPythonParameterType
from inference_schema.parameter_types.numpy_parameter_type import NumpyParameterType
from inference_schema.parameter_types.pandas_parameter_type import PandasParameterType


def init():
    global model
    # Replace filename if needed.
    model_path = os.path.join(os.getenv('AZUREML_MODEL_DIR'), 'sklearn_regression_model.pkl')
    # Deserialize the model file back into a sklearn model.
    model = joblib.load(model_path)

# providing 3 sample inputs for schema generation
numpy_sample_input = NumpyParameterType(np.array([[1,2,3,4,5,6,7,8,9,10],[10,9,8,7,6,5,4,3,2,1]],dtype='float64'))
pandas_sample_input = PandasParameterType(pd.DataFrame({'name': ['Sarah', 'John'], 'age': [25, 26]}))
standard_sample_input = StandardPythonParameterType(0.0)

# This is a nested input sample, any item wrapped by `ParameterType` will be described by schema
sample_input = StandardPythonParameterType({'input1': numpy_sample_input, 
                                            'input2': pandas_sample_input, 
                                            'input3': standard_sample_input})

sample_global_parameters = StandardPythonParameterType(1.0) #this is optional
sample_output = StandardPythonParameterType([1.0, 1.0])

@input_schema('inputs', sample_input)
@input_schema('global_parameters', sample_global_parameters) #this is optional
@output_schema(sample_output)
def run(inputs, global_parameters):
    try:
        data = inputs['input1']
        # data will be convert to target format
        assert isinstance(data, np.ndarray)
        result = model.predict(data)
        return result.tolist()
    except Exception as e:
        error = str(e)
        return error
```


## <a name="binary-ie-image-data"></a><a id="binary-data"></a> 이진 데이터 (예: 이미지)

모델에서 이미지와 같은 이진 데이터를 허용 하는 경우 `score.py` 원시 HTTP 요청을 수락 하도록 배포에 사용 되는 파일을 수정 해야 합니다. 원시 데이터를 수락 하려면 `AMLRequest` 항목 스크립트에서 클래스를 사용 하 고 `@rawhttp` 함수에 데코레이터를 추가 합니다 `run()` .

`score.py`이진 데이터를 허용 하는의 예는 다음과 같습니다.

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
> `AMLRequest` 클래스가 `azureml.contrib` 네임스페이스에 있습니다. 이 네임 스페이스의 엔터티는 서비스를 개선 하기 위해 작업할 때 자주 변경 됩니다. 이 네임 스페이스의 모든 항목을 Microsoft에서 완벽 하 게 지원 하지 않는 미리 보기로 간주 해야 합니다.
>
> 로컬 개발 환경에서 테스트를 수행 해야 하는 경우 다음 명령을 사용 하 여 구성 요소를 설치할 수 있습니다.
>
> ```shell
> pip install azureml-contrib-services
> ```

`AMLRequest`클래스를 사용 하면 score.py에서 게시 된 원시 데이터에만 액세스할 수 있으며 클라이언트 쪽 구성 요소는 없습니다. 클라이언트에서 데이터를 정상적으로 게시 합니다. 예를 들어 다음 Python 코드는 이미지 파일을 읽고 데이터를 게시 합니다.

```python
import requests
# Load image data
data = open('example.jpg', 'rb').read()
# Post raw data to scoring URI
res = requests.post(url='<scoring-uri>', data=data, headers={'Content-Type': 'application/octet-stream'})
```

<a id="cors"></a>

## <a name="cross-origin-resource-sharing-cors"></a>CORS(원본 간 리소스 공유)

크로스-원본 자원 공유는 웹 페이지의 리소스가 다른 도메인에서 요청 될 수 있도록 하는 방법입니다. CORS는 클라이언트 요청과 함께 전송 되 고 서비스 응답과 함께 반환 된 HTTP 헤더를 통해 작동 합니다. CORS 및 유효한 헤더에 대 한 자세한 내용은 위키백과의 [원본 간 리소스 공유](https://en.wikipedia.org/wiki/Cross-origin_resource_sharing) 를 참조 하세요.

CORS를 지원 하도록 모델 배포를 구성 하려면 `AMLResponse` 항목 스크립트에서 클래스를 사용 합니다. 이 클래스를 사용 하 여 응답 개체의 헤더를 설정할 수 있습니다.

다음 예에서는 `Access-Control-Allow-Origin` 입력 스크립트의 응답에 대 한 헤더를 설정 합니다.

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

        # For demonstration purposes, this example
        # adds a header and returns the request body.
        resp = AMLResponse(reqBody, 200)
        resp.headers['Access-Control-Allow-Origin'] = "http://www.example.com"
        return resp
    else:
        return AMLResponse("bad request", 500)
```

> [!IMPORTANT]
> `AMLResponse` 클래스가 `azureml.contrib` 네임스페이스에 있습니다. 이 네임 스페이스의 엔터티는 서비스를 개선 하기 위해 작업할 때 자주 변경 됩니다. 이 네임 스페이스의 모든 항목을 Microsoft에서 완벽 하 게 지원 하지 않는 미리 보기로 간주 해야 합니다.
>
> 로컬 개발 환경에서 테스트를 수행 해야 하는 경우 다음 명령을 사용 하 여 구성 요소를 설치할 수 있습니다.
>
> ```shell
> pip install azureml-contrib-services
> ```


> [!WARNING]
> Azure Machine Learning은 점수 매기기 서비스를 실행 하는 컨테이너에 대 한 POST 및 GET 요청만 라우팅합니다. 이로 인해 미리 비행 된 CORS 요청에 대 한 옵션 요청을 사용 하는 브라우저로 인 한 오류가 발생할 수 있습니다.
> 


## <a name="load-registered-models"></a>등록 된 모델 로드

다음 두 가지 방법으로 입력 스크립트에서 모델을 찾을 수 있습니다.
* `AZUREML_MODEL_DIR`: 모델 위치의 경로를 포함 하는 환경 변수입니다.
* `Model.get_model_path`: 등록 된 모델 이름을 사용 하 여 모델 파일에 대 한 경로를 반환 하는 API입니다.

#### <a name="azureml_model_dir"></a>AZUREML_MODEL_DIR

AZUREML_MODEL_DIR은 서비스 배포 중에 생성 되는 환경 변수입니다. 이 환경 변수를 사용 하 여 배포 된 모델의 위치를 찾을 수 있습니다.

다음 표에서는 배포 된 모델의 수에 따라 AZUREML_MODEL_DIR 값에 대해 설명 합니다.

| 배포 | 환경 변수 값 |
| ----- | ----- |
| 단일 모델 | 모델을 포함 하는 폴더의 경로입니다. |
| 여러 모델 | 모든 모델을 포함 하는 폴더의 경로입니다. 모델은이 폴더의 이름 및 버전으로 위치 합니다 ( `$MODEL_NAME/$VERSION` ). |

모델 등록 및 배포 중에 모델은 AZUREML_MODEL_DIR 경로에 배치 되 고 원래 파일 이름은 유지 됩니다.

항목 스크립트의 모델 파일에 대 한 경로를 가져오려면 환경 변수를 찾고 있는 파일 경로와 결합 합니다.

**단일 모델 예제**
```python
# Example when the model is a file
model_path = os.path.join(os.getenv('AZUREML_MODEL_DIR'), 'sklearn_regression_model.pkl')

# Example when the model is a folder containing a file
file_path = os.path.join(os.getenv('AZUREML_MODEL_DIR'), 'my_model_folder', 'sklearn_regression_model.pkl')
```

**여러 모델 예제**

이 시나리오에서는 두 개의 모델이 작업 영역에 등록 됩니다.

* `my_first_model`: 하나의 파일 ( `my_first_model.pkl` )을 포함 하 고 하나의 버전 ()만 있습니다 `1` .
* `my_second_model`: 하나의 파일 ( `my_second_model.pkl` )을 포함 하 고, 및의 두 가지 버전이 있습니다 `1` `2` .

서비스가 배포 되 면 배포 작업에 두 모델이 모두 제공 됩니다.

```python
first_model = Model(ws, name="my_first_model", version=1)
second_model = Model(ws, name="my_second_model", version=2)
service = Model.deploy(ws, "myservice", [first_model, second_model], inference_config, deployment_config)
```

서비스를 호스팅하는 Docker 이미지에서 `AZUREML_MODEL_DIR` 환경 변수는 모델이 위치한 디렉터리를 포함 합니다.
이 디렉터리에서 각 모델은의 디렉터리 경로에 있습니다 `MODEL_NAME/VERSION` . 여기서 `MODEL_NAME` 은 등록 된 모델의 이름이 고 `VERSION` 은 모델의 버전입니다. 등록 된 모델을 구성 하는 파일은 다음 디렉터리에 저장 됩니다.

이 예제에서 경로는 `$AZUREML_MODEL_DIR/my_first_model/1/my_first_model.pkl` 및 `$AZUREML_MODEL_DIR/my_second_model/2/my_second_model.pkl` 입니다.


```python
# Example when the model is a file, and the deployment contains multiple models
first_model_name = 'my_first_model'
first_model_version = '1'
first_model_path = os.path.join(os.getenv('AZUREML_MODEL_DIR'), first_model_name, first_model_version, 'my_first_model.pkl')
second_model_name = 'my_second_model'
second_model_version = '2'
second_model_path = os.path.join(os.getenv('AZUREML_MODEL_DIR'), second_model_name, second_model_version, 'my_second_model.pkl')
```

### <a name="get_model_path"></a>get_model_path

모델을 등록할 때 레지스트리에서 모델을 관리 하는 데 사용 되는 모델 이름을 제공 합니다. 이 이름을 [Model.get_model_path ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py&preserve-view=true#&preserve-view=trueget-model-path-model-name--version-none---workspace-none-) 메서드와 함께 사용 하 여 로컬 파일 시스템에서 모델 파일의 경로를 검색 합니다. 폴더 또는 파일 컬렉션을 등록 하는 경우이 API는 해당 파일이 포함 된 디렉터리의 경로를 반환 합니다.

모델을 등록할 때 이름을 지정 합니다. 이름은 모델을 로컬 또는 서비스 배포 중에 배치 하는 위치에 해당 합니다.

## <a name="framework-specific-examples"></a>프레임 워크 관련 예제

특정 machine learning 사용 사례에 대 한 추가 항목 스크립트 예제는 아래에서 찾을 수 있습니다.

* [PyTorch](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/ml-frameworks/pytorch)
* [Tensorflow](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/ml-frameworks/tensorflow)
* [Keras](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/ml-frameworks/keras/train-hyperparameter-tune-deploy-with-keras/train-hyperparameter-tune-deploy-with-keras.yml)
* [AutoML](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning/classification-bank-marketing-all-features)
* [ONNX](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/deployment/onnx/)

## <a name="next-steps"></a>다음 단계

* [실패 한 배포 문제 해결](how-to-troubleshoot-deployment.md)
* [Azure Kubernetes Service로 배포](how-to-deploy-azure-kubernetes-service.md)
* [웹 서비스를 사용 하는 클라이언트 응용 프로그램 만들기](how-to-consume-web-service.md)
* [웹 서비스 업데이트](how-to-deploy-update-web-service.md)
* [사용자 지정 Docker 이미지를 사용 하 여 모델을 배포 하는 방법](how-to-deploy-custom-docker-image.md)
* [TLS를 사용하여 Azure Machine Learning을 통해 웹 서비스 보호](how-to-secure-web-service.md)
* [Application Insights를 사용하여 Azure Machine Learning 모델 모니터링](how-to-enable-app-insights.md)
* [프로덕션 환경에서 모델용 데이터 수집](how-to-enable-data-collection.md)
* [모델 배포에 대 한 이벤트 경고 및 트리거 만들기](how-to-use-event-grid.md)
