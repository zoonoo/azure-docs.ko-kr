---
author: gvashishtha
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: include
ms.date: 07/31/2020
ms.author: gopalv
ms.openlocfilehash: 95acb028867caf6f497dd99ad3082efcaab09c7b
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/04/2020
ms.locfileid: "93325282"
---
항목 스크립트는 배포된 웹 서비스에 전송된 데이터를 받아서 모델에 전달합니다. 그런 후 모델이 반환한 응답을 수락한 후 클라이언트에 반환합니다. *이 스크립트는 모델에 따라 다릅니다*. 모델에 필요한 데이터를 이해 하 고 반환 해야 합니다.

입력 스크립트에서 수행 해야 하는 두 가지 작업은 다음과 같습니다.

1. 호출 된 함수를 사용 하 여 모델 로드 `init()`
1. 호출 된 함수를 사용 하 여 입력 데이터에서 모델 실행 `run()`

이러한 단계를 자세히 살펴보겠습니다.

### <a name="writing-init"></a>Init () 쓰기 

#### <a name="loading-a-registered-model"></a>등록 된 모델 로드

등록 된 모델은 라는 환경 변수가 가리키는 경로에 저장 됩니다 `AZUREML_MODEL_DIR` . 정확한 디렉터리 구조에 대 한 자세한 내용은 [항목 스크립트에서 모델 파일 찾기](../articles/machine-learning/how-to-deploy-advanced-entry-script.md#load-registered-models) 를 참조 하세요.

#### <a name="loading-a-local-model"></a>로컬 모델 로드

모델을 등록 하 고 원본 디렉터리의 일부로 모델을 전달한 경우 점수 매기기 스크립트를 기준으로 모델에 대 한 경로를 전달 하 여 로컬에서와 마찬가지로이를 읽을 수 있습니다. 예를 들어 다음과 같이 구성 된 디렉터리가 있습니다.

```bash

- source_dir
    - score.py
    - models
        - model1.onnx

```

다음 Python 코드를 사용 하 여 모델을 로드할 수 있습니다.

```python
import os

model = open(os.path.join('.', 'models', 'model1.onnx'))
```

### <a name="writing-run"></a>쓰기 실행 ()

`run()` 는 모델이 점수 매기기 요청을 받을 때마다 실행 되며, 요청의 본문은 다음 구조의 JSON 문서가 될 것으로 예상 합니다.

```json
{
    "data": <model-specific-data-structure>
}

```

에 대 한 입력은 `run()` "data" 키 뒤에 나오는 항목을 포함 하는 Python 문자열입니다.

다음 예에서는 등록 된 scikit 모델을 로드 하 고 numpy 데이터를 사용 하 여 점수를 매기는 방법을 보여 줍니다.

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

자동 Swagger 스키마 생성 및 이진 (예: 이미지) 데이터를 포함 하 여 고급 예제를 보려면 [고급 입력 스크립트 작성 문서](../articles/machine-learning/how-to-deploy-advanced-entry-script.md) 를 참조 하세요.