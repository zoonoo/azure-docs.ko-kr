---
author: gvashishtha
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: include
ms.date: 07/31/2020
ms.author: gopalv
ms.openlocfilehash: 95acb028867caf6f497dd99ad3082efcaab09c7b
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "93325282"
---
항목 스크립트는 배포된 웹 서비스에 전송된 데이터를 받아서 모델에 전달합니다. 그런 후 모델이 반환한 응답을 수락한 후 클라이언트에 반환합니다. *스크립트는 모델에 따라 다릅니다*. 모델이 기대하고 반환하는 데이터를 해석해야 합니다.

입력 스크립트에서 수행해야 하는 두 가지 작업은 다음과 같습니다.

1. 모델 로드(`init()`라는 기능 사용)
1. 입력 데이터에서 모델 실행(`run()`이라는 함수 사용)

이 단계를 자세히 살펴보겠습니다.

### <a name="writing-init"></a>init() 작성 

#### <a name="loading-a-registered-model"></a>등록된 모델 로드

등록된 모델은 `AZUREML_MODEL_DIR`이라는 환경 변수가 가리키는 경로에 저장됩니다. 정확한 디렉터리 구조에 대한 자세한 내용은 [항목 스크립트에서 모델 파일 찾기](../articles/machine-learning/how-to-deploy-advanced-entry-script.md#load-registered-models)를 참조하세요.

#### <a name="loading-a-local-model"></a>로컬 모델 로드

모델 등록을 거부하고 모델을 소스 디렉터리의 일부로 전달한 경우 채점 스크립트에 상대적인 모델에 대한 경로를 전달하여 로컬에서와 같이 모델을 읽을 수 있습니다. 예를 들어 다음과 같은 구조의 디렉터리가 있습니다.

```bash

- source_dir
    - score.py
    - models
        - model1.onnx

```

다음 Python 코드를 사용하여 모델을 로드할 수 있습니다.

```python
import os

model = open(os.path.join('.', 'models', 'model1.onnx'))
```

### <a name="writing-run"></a>run() 작성

`run()`은 모델이 채점 요청을 받을 때마다 실행되며 요청 본문이 다음 구조의 JSON 문서가 될 것으로 예상합니다.

```json
{
    "data": <model-specific-data-structure>
}

```

`run()`에 대한 입력은 "data" 키 뒤에 오는 모든 것을 포함하는 Python 문자열입니다.

다음 예에서는 등록된 scikit-learn 모델을 로드하고 numpy 데이터로 채점하는 방법을 보여줍니다.

```python
import json
import numpy as np
import os
from sklearn.externals import joblib


def init():
    global model
    model_path = os.path.join(os.getenv('AZUREML_MODEL_DIR'), 'sklearn_mnist_model.pkl')
    model = joblib.load(model_path)

def run(data):
    try:
        data = np.array(json.loads(data))
        result = model.predict(data)
        # You can return any data type, as long as it is JSON serializable.
        return result.tolist()
    except Exception as e:
        error = str(e)
        return error
```

자동 Swagger 스키마 생성 및 이진(예: 이미지) 데이터를 포함한 [고급 예는 고급 항목 스크립트 작성에 대한 문서](../articles/machine-learning/how-to-deploy-advanced-entry-script.md)를 참조하세요.