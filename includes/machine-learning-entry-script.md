---
author: gvashishtha
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: include
ms.date: 07/31/2020
ms.author: gopalv
ms.openlocfilehash: 2b4f768b25917e712380ca4a7f8ac58cb6140777
ms.sourcegitcommit: 8def3249f2c216d7b9d96b154eb096640221b6b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/03/2020
ms.locfileid: "87542801"
---
항목 스크립트는 배포된 웹 서비스에 전송된 데이터를 받아서 모델에 전달합니다. 그런 후 모델이 반환한 응답을 수락한 후 클라이언트에 반환합니다. *이 스크립트는 모델에 따라 다릅니다*. 모델에 필요한 데이터를 이해 하 고 반환 해야 합니다.

이 스크립트는 모델을 로드하고 실행하는 2가지 함수를 포함합니다.

* `init()`: 일반적으로이 함수는 전역 개체에 모델을 로드 합니다. 이 함수는 웹 서비스의 Docker 컨테이너가 시작 될 때 한 번만 실행 됩니다.

* `run(input_data)`:이 함수는 모델을 사용 하 여 입력 데이터를 기반으로 값을 예측 합니다. 실행에 대한 입력 및 출력은 일반적으로 직렬화 및 역직렬화에 JSON을 사용합니다. 원시 이진 데이터를 사용할 수도 있습니다. 모델에 보내기 전에 또는 클라이언트에 반환하기 전에 데이터를 변환할 수 있습니다.

REST API는 요청 본문이 다음과 같은 구조의 JSON 문서가 될 것으로 예상합니다.

```json
{
    "data":
        [
            <model-specific-data-structure>
        ]
}
```

다음 예에서는 등록 된 scikit 모델을 로드 하 고 numpy 데이터를 사용 하 여 점수를 매기는 방법을 보여 줍니다.

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

더 많은 예제를 보려면 다음 스크립트를 참조 하십시오.

* [PyTorch](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/ml-frameworks/pytorch)
* [Tensorflow](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/ml-frameworks/tensorflow)
* [Keras](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-keras)
* [AutoML](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning/classification-bank-marketing-all-features)
* [ONNX](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/deployment/onnx/)
* [이진 데이터](../articles/machine-learning/how-to-deploy-advanced-entry-script.md#binary-data)