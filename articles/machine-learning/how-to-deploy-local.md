---
title: 로컬로 실행 및 배포 하는 방법
titleSuffix: Azure Machine Learning
description: 로컬 컴퓨터에서 학습 된 모델을 실행 하는 방법을 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: laobri
author: lobrien
ms.date: 11/20/2020
ms.topic: conceptual
ms.custom: how-to, deploy
ms.openlocfilehash: fcea7c162e5c978a7c8ff1b42e09ffe0afb98f3c
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/24/2020
ms.locfileid: "95550095"
---
# <a name="deploy-on-your-local-machines-models-trained-with-azure-machine-learning"></a>Azure Machine Learning를 사용 하 여 학습 한 로컬 컴퓨터 모델에 배포

이 문서에서는 Azure Machine Learning에서 만든 모델을 배포 하거나 배포 하기 위해 로컬 컴퓨터를 대상으로 사용 하는 방법을 설명 합니다. Azure Machine Learning의 유연성 덕분에 대부분의 Python 기계 학습 프레임 워크를 사용할 수 있습니다. 기계 학습 솔루션에는 일반적으로 복제 하기 어려울 수 있는 복잡 한 종속성이 있습니다. 이 문서에서는 전체 제어 및 사용 편의성을 절충 하는 방법을 보여 줍니다.

로컬 배포에 대 한 몇 가지 시나리오는 다음과 같습니다.

* 프로젝트 초기에 신속 하 게 데이터, 스크립트 및 모델 반복
* 이후 단계에서 디버깅 및 문제 해결
* 사용자 관리 하드웨어에서 최종 배포

## <a name="prerequisites"></a>사전 요구 사항

- Azure Machine Learning 작업 영역 자세한 내용은 [Azure Machine Learning 작업 영역 만들기](how-to-manage-workspace.md) 를 참조 하세요.
- 모델 및 환경. 학습 된 모델이 없는 경우 [이 자습서](tutorial-train-models-with-aml.md) 에 제공 된 모델 및 종속성 파일을 사용할 수 있습니다.
- [Python 용 AZURE MACHINE LEARNING SDK (소프트웨어 개발 키트)](/python/api/overview/azure/ml/intro?preserve-view=true&view=azure-ml-py)
- Azure Machine Learning의 패키지 종속성을 미러링 하려는 경우 Anaconda 또는 miniconda와 같은 conda 관리자
- Docker, Azure Machine Learning 환경의 컨테이너 화 된 버전을 사용 하려는 경우

## <a name="prepare-your-local-machine"></a>로컬 머신 준비

Azure Machine Learning 모델을 로컬로 실행 하는 가장 안정적인 방법은 Docker 이미지를 사용 하는 것입니다. Docker 이미지는 하드웨어 문제를 제외 하 고 Azure 실행 환경을 중복 하는 격리 된 컨테이너 화 된 환경을 제공 합니다. 개발 시나리오를 위해 Docker를 설치 하 고 구성 하는 방법에 대 한 자세한 내용은 [Windows의 docker 원격 개발 개요](/windows/dev-environment/docker/overview)를 참조 하세요.

Docker에서 실행 중인 프로세스에 디버거를 연결할 수 있지만 ( [실행 중인 컨테이너에 연결](https://code.visualstudio.com/docs/remote/attach-container)참조) docker를 포함 하지 않고 Python 코드를 디버그 하 고 반복할 수 있습니다. 이 시나리오에서 로컬 컴퓨터는 Azure Machine Learning 실험을 실행할 때 사용 되는 것과 동일한 라이브러리를 사용 하는 것이 중요 합니다. Azure는 Python 종속성을 관리 하기 위해 [conda](https://docs.conda.io/)을 사용 합니다. 다른 패키지 관리자를 사용 하 여 환경을 다시 만들 수도 있지만, 로컬 컴퓨터에 conda를 설치 하 고 구성 하는 것이 가장 쉬운 방법입니다. 

## <a name="prepare-your-entry-script"></a>항목 스크립트 준비

Docker를 사용 하 여 모델 및 종속성을 관리 하는 경우에도 Python 점수 매기기 스크립트는 로컬 이어야 합니다. 스크립트에는 다음 두 가지 방법이 있어야 합니다.

- `init()`인수를 사용 하지 않고 아무것도 반환 하지 않는 메서드입니다. 
- `run()`Json 형식 문자열을 사용 하 고 json 직렬화 가능 개체를 반환 하는 메서드입니다.

메서드에 대 한 인수는 `run()` 다음과 같은 형식입니다. 

```json
{
    "data": <model-specific-data-structure>
}
```

메서드에서 반환 하는 개체는를 `run()` 구현 해야 합니다 `toJSON() -> string` .

다음 예에서는 등록 된 scikit 모델을 로드 하 고 numpy 데이터를 사용 하 여 점수를 매기는 방법을 보여 줍니다. 이 예제는 [이 자습서](tutorial-train-models-with-aml.md)의 모델 및 종속성을 기반으로 합니다.

```python
import json
import numpy as np
import os
import pickle
import joblib

def init():
    global model
    # AZUREML_MODEL_DIR is an environment variable created during deployment.
    # It is the path to the model folder (./azureml-models/$MODEL_NAME/$VERSION)
    # For multiple models, it points to the folder containing all deployed models (./azureml-models)
    model_path = os.path.join(os.getenv('AZUREML_MODEL_DIR'), 'sklearn_mnist_model.pkl')
    model = joblib.load(model_path)

def run(raw_data):
    data = np.array(json.loads(raw_data)['data'])
    # make prediction
    y_hat = model.predict(data)
    # you can return any data type as long as it is JSON-serializable
    return y_hat.tolist()
```

자동 Swagger 스키마 생성 및 이진 데이터의 점수를 매기는 방법 (예를 들어, 이미지) 데이터를 포함 하는 고급 예제를 보려면 [고급 입력 스크립트 작성](how-to-deploy-advanced-entry-script.md)을 참조 하세요. 

## <a name="deploy-as-a-local-web-service-using-docker"></a>Docker를 사용 하 여 로컬 웹 서비스로 배포

Azure Machine Learning에서 사용 하는 환경을 복제 하는 가장 쉬운 방법은 Docker를 사용 하 여 웹 서비스를 배포 하는 것입니다. Docker를 로컬 컴퓨터에서 실행 하는 경우 다음을 수행 합니다.

1. 모델이 등록 된 Azure Machine Learning 작업 영역에 연결
1. `Model`모델을 나타내는 개체 만들기
1. 종속성을 `Environment` 포함 하는 개체를 만들고 코드가 실행 될 소프트웨어 환경을 정의 합니다.
1. `InferenceConfig`항목 스크립트와을 연결 하는 개체를 만듭니다.`Environment`
1. `DeploymentConfiguration`하위 클래스의 개체를 만듭니다.`LocalWebserviceDeploymentConfiguration`
1. `Model.deploy()`를 사용 하 여 `Webservice` 개체를 만듭니다. 이 메서드는 Docker 이미지를 다운로드 하 고 `Model` , 및에 연결 합니다. `InferenceConfig``DeploymentConfiguration`
1. 을 사용 하 여를 활성화 합니다. `Webservice``Webservice.wait_for_deployment()`

다음 코드에서는 이러한 단계를 보여 줍니다.

```python
from azureml.core.webservice import Webservice
from azure.core.model import InferenceConfig
from azureml.core.environment import Environment
from azureml.core import Workspace
from azureml.core.model import Model

ws = Workspace.from_config()
model = Model(ws, 'sklearn_mnist')


myenv = Environment.get(workspace=ws, name="tutorial-env", version="1")
inference_config = InferenceConfig(entry_script="score.py", environment=myenv)

deployment_config = LocalWebservice.deploy_configuration(port=6789)

local_service = Model.deploy(workspace=ws, 
                       name='sklearn-mnist-local', 
                       models=[model], 
                       inference_config=inference_config, 
                       deployment_config = deployment_config)

local_service.wait_for_deployment(show_output=True)
print(f"Scoring URI is : {local_service.scoring_uri}")
```

을 호출 하는 데 `Model.deploy()` 몇 분 정도 걸릴 수 있습니다. 를 처음 배포한 후에는 처음부터 다시 시작 하는 대신 메서드를 사용 하는 것이 더 효율적입니다 `update()` . [배포 된 웹 서비스 업데이트를](how-to-deploy-update-web-service.md)참조 하세요.

### <a name="test-your-local-deployment"></a>로컬 배포 테스트

이전 배포 스크립트를 실행 하면 점수 매기기를 위해 데이터를 게시할 수 있는 URI (예를 들어)가 출력 됩니다 `http://localhost:6789/score` . 다음 샘플에서는 로컬로 배포 된 모델을 사용 하 여 샘플 데이터의 점수를 표시 하는 스크립트를 보여 줍니다 `"sklearn-mnist-local"` . 모델이 제대로 학습 된 경우 `normalized_pixel_values` "2"로 해석 되어야 하는 것으로 유추 됩니다. 

```python
import requests

normalized_pixel_values = "[\
0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, \
0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, \
0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, \
0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.5, 0.5, 0.7, 1.0, 1.0, 0.6, 0.4, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, \
0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.7, 1.0, 1.0, 1.0, 1.0, 1.0, 1.0, 0.9, 0.1, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, \
0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.7, 1.0, 1.0, 1.0, 0.8, 0.6, 0.7, 1.0, 1.0, 0.5, 0.0, 0.0, 0.0, 0.0, 0.0, \
0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.2, 1.0, 1.0, 0.8, 0.1, 0.0, 0.0, 0.0, 0.8, 1.0, 0.5, 0.0, 0.0, 0.0, 0.0, \
0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.3, 1.0, 0.8, 0.1, 0.0, 0.0, 0.0, 0.5, 1.0, 1.0, 0.3, 0.0, 0.0, 0.0, \
0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.1, 0.1, 0.0, 0.0, 0.0, 0.0, 0.8, 1.0, 1.0, 0.3, 0.0, 0.0, \
0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.5, 1.0, 1.0, 0.8, 0.0, 0.0, \
0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.3, 1.0, 1.0, 0.9, 0.2, 0.0, \
0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.5, 1.0, 1.0, 0.6, 0.0, \
0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.7, 1.0, 1.0, 0.6, 0.0, \
0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.1, 0.9, 1.0, 0.9, 0.1, \
0.1, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.8, 1.0, 1.0, 0.6, \
0.6, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.3, 1.0, 1.0, 0.7, \
0.7, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.1, 0.8, 1.0, 1.0, \
1.0, 0.6, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.5, 1.0, 1.0, \
1.0, 0.7, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 1.0, 1.0, \
1.0, 1.0, 0.1, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 1.0, \
1.0, 1.0, 1.0, 0.2, 0.1, 0.1, 0.1, 0.1, 0.0, 0.0, 0.0, 0.1, 0.1, 0.1, 0.6, 0.6, 0.6, 0.6, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, \
0.0, 1.0, 1.0, 1.0, 1.0, 1.0, 1.0, 1.0, 1.0, 0.7, 0.6, 0.7, 1.0, 1.0, 1.0, 1.0, 1.0, 1.0, 1.0, 0.5, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, \
0.0, 0.0, 0.7, 1.0, 1.0, 1.0, 1.0, 1.0, 1.0, 1.0, 1.0, 1.0, 1.0, 1.0, 1.0, 1.0, 1.0, 0.7, 0.5, 0.5, 0.2, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, \
0.0, 0.0, 0.0, 0.0, 0.5, 0.5, 0.5, 0.5, 0.7, 1.0, 1.0, 1.0, 0.6, 0.5, 0.5, 0.2, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, \
0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, \
0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, \
0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, \
0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0]"

input_data = "{\"data\": [" + normalized_pixel_values + "]}"

headers = {'Content-Type': 'application/json'}

scoring_uri = "http://localhost:6789/score"
resp = requests.post(scoring_uri, input_data, headers=headers)

print("Should be predicted as '2'")
print("prediction:", resp.text)
```

## <a name="download-and-run-your-model-directly"></a>모델을 직접 다운로드 하 여 실행 합니다.

Docker를 사용 하 여 웹 서비스로 모델을 배포 하는 것이 가장 일반적인 옵션 이지만 로컬 Python 스크립트를 사용 하 여 코드를 직접 실행할 수도 있습니다. 다음 두 가지 중요 한 요소가 필요 합니다. 

- 모델 자체
- 모델에 의존 하는 종속성 

모델 다운로드는 다음과 같은 작업을 수행할 수 있습니다.  

- 포털에서 **모델** 탭을 선택 하 고 원하는 모델을 선택한 다음 **세부 정보** 페이지에서 **다운로드** 를 선택 합니다.
- 명령줄에서를 사용 하 여 `az ml model download` ( [모델 다운로드 참조](/cli/azure/ext/azure-cli-ml/ml/model?view=azure-cli-latest#ext_azure_cli_ml_az_ml_model_download&preserve-view=false)참조)
- Python SDK를 사용 하 여 `Model.download()` 메서드 ( [모델 API 참조](/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#download-target-dir------exist-ok-false--exists-ok-none-&preserve-view=false)참조)

Azure 모델은 Python pickle 파일 (**. model.pkl** extension)로 패키지 된 하나 이상의 직렬화 된 python 개체입니다. Pickle 파일의 내용은 ML 라이브러리나 모델 학습에 사용 되는 기술에 따라 달라 집니다. 예를 들어 자습서의 모델을 사용 하면 다음을 사용 하 여 모델을 로드할 수 있습니다.

```python
import pickle

with open('sklearn_mnist_model.pkl', 'rb') as f : 
    logistic_model = pickle.load(f, encoding='latin1')
```

특히 기계 학습을 사용 하 여 종속성이 항상 적절 한 것은 아닙니다. 이러한 경우에는 특정 버전 요구 사항의 dizzying 웹이 필요할 수 있습니다. 클래스의 메서드를 사용 하 여 전체 conda 환경 또는 Docker 이미지로 로컬 컴퓨터에서 Azure Machine Learning 환경을 다시 만들 수 있습니다 `Environment` `build_local()` . 

```python
ws = Workspace.from_config()
myenv = Environment.get(workspace=ws, name="tutorial-env", version="1")
myenv.build_local(workspace=ws, useDocker=False) #Creates conda env
```

의 인수를로 설정 하면이 `build_local()` `useDocker` `True` 함수는 Conda 환경이 아닌 Docker 이미지를 만듭니다. 더 많은 제어를 원하는 경우 `Environment` 의 메서드를 사용 하 여 `save_to_directory()` 확장에 대 한 기준으로 미세 조정 하 고 사용할 수 있는 정의 파일 **에** **conda_dependencies** 를 기록 하 고azureml_environment.js를 작성할 수 있습니다. 

클래스에는 `Environment` 계산 하드웨어, Azure 작업 영역 및 Docker 이미지에서 환경을 동기화 하기 위한 여러 가지 방법이 있습니다. 자세한 내용은 [ `Environment` API 참조](/python/api/azureml-core/azureml.core.environment(class))를 참조 하세요.

모델을 다운로드 하 고 해당 종속성을 해결 한 후에는 점수 매기기를 수행 하 고, 모델을 미세 조정 하 고, 전송 학습을 사용 하는 방법에 대 한 Azure 정의 제한이 없습니다. 

## <a name="upload-a-retrained-model-to-azure-machine-learning"></a>Azure Machine Learning에 다시 학습 모델 업로드

로컬로 학습 된 모델 또는 다시 학습 모델이 있는 경우 Azure에 등록할 수 있습니다. 등록 한 후에는 azure compute를 사용 하 여 계속 튜닝 하거나 azure [Kubernetes Service](how-to-deploy-azure-kubernetes-service.md) 또는 [Triton 유추 서버 (미리 보기)](how-to-deploy-with-triton.md)와 같은 azure 기능을 사용 하 여 배포할 수 있습니다.

Azure Machine Learning의 Python SDK와 함께 사용 하려면 모델을 pickle 형식 ( **model.pkl** 파일)으로 직렬화 된 Python 개체로 저장 하 고 `predict(data)` JSON 직렬화 가능 개체를 반환 하는 메서드를 구현 해야 합니다. 예를 들어 다음을 사용 하 여 로컬로 학습 된 scikit-배우기 당뇨병 모델을 저장할 수 있습니다. 

```python
import joblib

from sklearn.datasets import load_diabetes
from sklearn.linear_model import Ridge

dataset_x, dataset_y = load_diabetes(return_X_y=True)

sk_model = Ridge().fit(dataset_x, dataset_y)

joblib.dump(sk_model, "sklearn_regression_model.pkl")
```

Azure에서 모델을 사용할 수 있도록 하려면 `register()` 클래스의 메서드를 사용 합니다 `Model` .

```python
from azureml.core.model import Model

model = Model.register(model_path="sklearn_regression_model.pkl",
                       model_name="sklearn_regression_model",
                       tags={'area': "diabetes", 'type': "regression"},
                       description="Ridge regression model to predict diabetes",
                       workspace=ws)
```

그런 다음 Azure Machine Learning의 **모델** 탭에서 새로 등록 된 모델을 찾을 수 있습니다.

:::image type="content" source="media/how-to-deploy-local/registered-model.png" alt-text="업로드 된 모델을 보여 주는 Azure Machine Learning 모델 탭의 스크린샷":::

모델 및 환경을 업로드 하 고 업데이트 하는 방법에 대 한 자세한 내용은 [모델 등록 및 고급 사용으로 로컬로 배포](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/deployment/deploy-to-local/register-model-deploy-local-advanced.ipynb)를 참조 하세요.

## <a name="next-steps"></a>다음 단계

- 환경 관리에 대 한 자세한 내용은 [에서 소프트웨어 환경 만들기 & 사용](how-to-use-environments.md) 을 참조 하세요 Azure Machine Learning
- 데이터 저장소에서 데이터에 액세스 하는 방법에 대해 알아보려면 [Azure에서 저장소 서비스에 연결](how-to-access-data.md) 을 참조 하세요.