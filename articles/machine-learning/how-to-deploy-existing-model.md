---
title: 기존 모델 사용 및 배포
titleSuffix: Azure Machine Learning
description: Azure Machine Learning을 사용하여 Azure 외부에서 학습된 ML 모델을 Azure 클라우드로 가져오는 방법에 대해 알아봅니다. 그런 다음 웹 서비스 또는 IoT 모듈로 해당 모델을 배포합니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: jordane
author: jpe316
ms.reviewer: larryfr
ms.date: 07/17/2020
ms.topic: how-to
ms.custom: devx-track-python, deploy
ms.openlocfilehash: 651e158009c87449bef339d131edbb188763b45f
ms.sourcegitcommit: 5ce88326f2b02fda54dad05df94cf0b440da284b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/22/2021
ms.locfileid: "107885455"
---
# <a name="deploy-your-existing-model-with-azure-machine-learning"></a>Azure Machine Learning을 사용하여 기존 모델 배포


이 문서에서는 Azure Machine Learning 외부에서 학습한 기계 학습 모델을 등록하고 배포하는 방법에 대해 알아봅니다. 웹 서비스 형태로 또는 IoT Edge 디바이스에 배포할 수 있습니다.  배포되면 모델을 모니터링하고 Azure Machine Learning에서 데이터 드리프트를 검색할 수 있습니다. 

이 문서의 개념 및 용어에 대한 자세한 내용은 [기계 학습 모델 관리, 배포, 모니터링](concept-model-management-and-deployment.md)을 참조하세요.

## <a name="prerequisites"></a>사전 요구 사항

* [Azure Machine Learning 작업 영역](how-to-manage-workspace.md)
  + Python 예제에서는 `ws` 변수가 Azure Machine Learning 작업 영역으로 설정된 것으로 가정합니다. 작업 영역에 연결하는 방법에 대한 자세한 내용은 [Python용 Azure Machine Learning SDK 설명서](/python/api/overview/azure/ml/#workspace)를 참조하세요.
  
  + CLI 예제에서는 `myworkspace` 및 `myresourcegroup` 자리 표시자를 사용합니다. 이 자리 표시자를 작업 영역의 이름 및 작업 영역을 포함하는 리소스 그룹의 이름으로 바꾸어야 합니다.

* [Azure Machine Learning Python SDK](/python/api/overview/azure/ml/install).  

* [Azure CLI](/cli/azure/install-azure-cli) 및 [Machine Learning CLI 확장](reference-azure-machine-learning-cli.md).

* 학습된 모델. 모델은 개발 환경에서 하나 이상의 파일로 유지되어야 합니다. <br><br>학습된 모델을 등록하는 방법을 보여 주기 위해 이 문서의 예제 코드에서는 [Paolo Ripamonti 님의 Twitter 감정 분석 프로젝트](https://www.kaggle.com/paoloripamonti/twitter-sentiment-analysis)의 모델을 사용합니다.

## <a name="register-the-models"></a>모델 등록

모델을 등록하면 작업 영역에서 모델에 대한 메타데이터를 저장 및 추적하고, 메타데이터의 버전을 관리할 수 있습니다. 다음 Python 및 CLI 예제에서 `models` 디렉터리는 `model.h5`, `model.w2v`, `encoder.pkl`, `tokenizer.pkl` 파일을 포함합니다. 이 예제에서는 `models` 디렉터리에 포함된 파일을 `sentiment`라는 새 모델로 등록하여 업로드합니다.

```python
from azureml.core.model import Model
# Tip: When model_path is set to a directory, you can use the child_paths parameter to include
#      only some of the files from the directory
model = Model.register(model_path = "./models",
                       model_name = "sentiment",
                       description = "Sentiment analysis model trained outside Azure Machine Learning",
                       workspace = ws)
```

자세한 내용은 [Model.register()](/python/api/azureml-core/azureml.core.model%28class%29#register-workspace--model-path--model-name--tags-none--properties-none--description-none--datasets-none--model-framework-none--model-framework-version-none--child-paths-none--sample-input-dataset-none--sample-output-dataset-none--resource-configuration-none-) 참조를 확인하세요.

```azurecli
az ml model register -p ./models -n sentiment -w myworkspace -g myresourcegroup
```

> [!TIP]
> 또한 등록된 모델에 `tags` 및 `properties` 사전 개체를 추가할 수 있습니다. 해당 값은 나중에 특정 모델을 식별하는 데 사용할 수 있습니다. 예를 들어 사용된 프레임워크, 학습 매개 변수 등입니다.

자세한 내용은 [az ml model register](/cli/azure/ml/model#az_ml_model_register) 참조를 확인하세요.


일반적인 모델 등록에 대한 자세한 내용은 [기계 학습 모델 관리, 배포, 모니터링](concept-model-management-and-deployment.md)을 참조하세요.

## <a name="define-inference-configuration"></a>유추 구성 정의

유추 구성은 배포된 모델을 실행하는 데 사용되는 환경을 정의합니다. 유추 구성은 모델을 배포할 때 모델을 실행하는 데 사용되는 다음 엔터티를 참조합니다.

* `score.py`라는 항목 스크립트는 배포된 서비스가 시작될 때 모델을 로드합니다. 이 스크립트는 데이터를 수신하고 모델에 전달한 다음 응답을 반환하는 작업도 담당합니다.
* Azure Machine Learning [환경](how-to-use-environments.md). 환경은 모델 및 항목 스크립트를 실행하는 데 필요한 소프트웨어 종속성을 정의합니다.

다음 예제에서는 SDK를 사용하여 환경을 만든 다음 이를 유추 구성과 함께 사용하는 방법을 보여 줍니다.

```python
from azureml.core.model import InferenceConfig
from azureml.core.environment import Environment
from azureml.core.conda_dependencies import CondaDependencies

# Create the environment
myenv = Environment(name="myenv")
conda_dep = CondaDependencies()

# Define the packages needed by the model and scripts
conda_dep.add_conda_package("tensorflow")
conda_dep.add_conda_package("numpy")
conda_dep.add_conda_package("scikit-learn")
# You must list azureml-defaults as a pip dependency
conda_dep.add_pip_package("azureml-defaults")
conda_dep.add_pip_package("keras")
conda_dep.add_pip_package("gensim")

# Adds dependencies to PythonSection of myenv
myenv.python.conda_dependencies=conda_dep

inference_config = InferenceConfig(entry_script="score.py",
                                   environment=myenv)
```

자세한 내용은 다음 아티클을 참조하세요.

+ [환경 사용 방식](how-to-use-environments.md).
+ [InferenceConfig](/python/api/azureml-core/azureml.core.model.inferenceconfig) 참조.


CLI는 YAML 파일에서 유추 구성을 로드합니다.

```yaml
{
   "entryScript": "score.py",
   "runtime": "python",
   "condaFile": "myenv.yml"
}
```

CLI를 사용하면, 유추 구성에서 참조하는 `myenv.yml` 파일에 conda 환경이 정의됩니다. 다음 YAML은 이 파일의 내용입니다.

```yaml
name: inference_environment
dependencies:
- python=3.6.2
- tensorflow
- numpy
- scikit-learn
- pip:
    - azureml-defaults
    - keras
    - gensim
```

유추 구성에 대한 자세한 내용은 [Azure Machine Learning을 사용하여 모델 배포](how-to-deploy-and-where.md)를 참조하세요.

### <a name="entry-script-scorepy"></a>항목 스크립트(score.py)

항목 스크립트에는 `init()` 및 `run(data)`라는 두 개의 필수 함수만 있습니다. 해당 함수는 시작 시 서비스를 초기화하고 클라이언트에서 전달된 요청 데이터를 사용하여 모델을 실행하는 데 사용됩니다. 스크립트의 나머지 부분은 모델 로드와 실행을 처리합니다.

> [!IMPORTANT]
> 모든 모델에 대해 작동하는 일반 항목 스크립트는 없습니다. 이는 항상 사용되는 모델과 관련이 있습니다. 항목 스크립트는 모델을 로드하는 방법, 모델에 필요한 데이터 형식을 로드하는 방법, 모델을 사용하여 데이터를 채점하는 방법을 이해해야 합니다.

다음 Python 코드는 예제 항목 스크립트(`score.py`)입니다.

```python
import os
import pickle
import json
import time
from keras.models import load_model
from keras.preprocessing.sequence import pad_sequences
from gensim.models.word2vec import Word2Vec

# SENTIMENT
POSITIVE = "POSITIVE"
NEGATIVE = "NEGATIVE"
NEUTRAL = "NEUTRAL"
SENTIMENT_THRESHOLDS = (0.4, 0.7)
SEQUENCE_LENGTH = 300

# Called when the deployed service starts
def init():
    global model
    global tokenizer
    global encoder
    global w2v_model

    # Get the path where the deployed model can be found.
    model_path = os.path.join(os.getenv('AZUREML_MODEL_DIR'), './models')
    # load models
    model = load_model(model_path + '/model.h5')
    w2v_model = Word2Vec.load(model_path + '/model.w2v')

    with open(model_path + '/tokenizer.pkl','rb') as handle:
        tokenizer = pickle.load(handle)

    with open(model_path + '/encoder.pkl','rb') as handle:
        encoder = pickle.load(handle)

# Handle requests to the service
def run(data):
    try:
        # Pick out the text property of the JSON request.
        # This expects a request in the form of {"text": "some text to score for sentiment"}
        data = json.loads(data)
        prediction = predict(data['text'])
        #Return prediction
        return prediction
    except Exception as e:
        error = str(e)
        return error

# Determine sentiment from score
def decode_sentiment(score, include_neutral=True):
    if include_neutral:
        label = NEUTRAL
        if score <= SENTIMENT_THRESHOLDS[0]:
            label = NEGATIVE
        elif score >= SENTIMENT_THRESHOLDS[1]:
            label = POSITIVE
        return label
    else:
        return NEGATIVE if score < 0.5 else POSITIVE

# Predict sentiment using the model
def predict(text, include_neutral=True):
    start_at = time.time()
    # Tokenize text
    x_test = pad_sequences(tokenizer.texts_to_sequences([text]), maxlen=SEQUENCE_LENGTH)
    # Predict
    score = model.predict([x_test])[0]
    # Decode sentiment
    label = decode_sentiment(score, include_neutral=include_neutral)

    return {"label": label, "score": float(score),
       "elapsed_time": time.time()-start_at}  
```

항목 스크립트에 대한 자세한 내용은 [Azure Machine Learning을 사용하여 모델 배포](how-to-deploy-and-where.md)를 참조하세요.

## <a name="define-deployment"></a>배포 정의

[웹 서비스](/python/api/azureml-core/azureml.core.webservice) 패키지에는 배포에 사용되는 클래스가 포함되어 있습니다. 사용하는 클래스에 따라 모델을 배포하는 위치가 결정됩니다. 예를 들어 Azure Kubernetes Service 기반 웹 서비스 형태로 배포하려면 [AksWebService.deploy_configuration()](/python/api/azureml-core/azureml.core.webservice.akswebservice#deploy-configuration-autoscale-enabled-none--autoscale-min-replicas-none--autoscale-max-replicas-none--autoscale-refresh-seconds-none--autoscale-target-utilization-none--collect-model-data-none--auth-enabled-none--cpu-cores-none--memory-gb-none--enable-app-insights-none--scoring-timeout-ms-none--replica-max-concurrent-requests-none--max-request-wait-time-none--num-replicas-none--primary-key-none--secondary-key-none--tags-none--properties-none--description-none--gpu-cores-none--period-seconds-none--initial-delay-seconds-none--timeout-seconds-none--success-threshold-none--failure-threshold-none--namespace-none--token-auth-enabled-none--compute-target-name-none-)을 사용하여 배포 구성을 만듭니다.

다음 Python 코드는 로컬 배포에 대한 배포 구성을 정의합니다. 이 구성은 모델을 로컬 컴퓨터에 웹 서비스 형태로 배포합니다.

> [!IMPORTANT]
> 로컬 배포를 수행하려면 로컬 컴퓨터에 [Docker](https://www.docker.com/)를 정상적으로 설치해야 합니다.

```python
from azureml.core.webservice import LocalWebservice

deployment_config = LocalWebservice.deploy_configuration()
```

자세한 내용은 [LocalWebservice.deploy_configuration()](/python/api/azureml-core/azureml.core.webservice.localwebservice#deploy-configuration-port-none-) 참조를 확인하세요.

CLI는 YAML 파일에서 배포 구성을 로드합니다.

```YAML
{
    "computeType": "LOCAL"
}
```

Azure 클라우드에서 Azure Kubernetes Service와 같은 다른 컴퓨팅 대상에 배포하는 작업은 배포 구성을 변경하는 작업과 마찬가지로 쉽습니다. 자세한 내용은 [모델 배포 방법 및 배포 위치](how-to-deploy-and-where.md)를 참조하세요.

## <a name="deploy-the-model"></a>모델 배포

다음 예제에서는 `sentiment`라는 등록된 모델에 대한 정보를 로드한 다음 `sentiment`라는 서비스 형태로 배포합니다. 배포하는 동안 유추 구성 및 배포 구성은 서비스 환경을 만들고 구성하는 데 사용됩니다.

```python
from azureml.core.model import Model

model = Model(ws, name='sentiment')
service = Model.deploy(ws, 'myservice', [model], inference_config, deployment_config)

service.wait_for_deployment(True)
print(service.state)
print("scoring URI: " + service.scoring_uri)
```

자세한 내용은 [Model.deploy()](/python/api/azureml-core/azureml.core.model.model#deploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-) 참조를 확인하세요.

CLI에서 모델을 배포하려면 다음 명령을 사용합니다. 이 명령은 `inferenceConfig.json` 및 `deploymentConfig.json` 파일에 저장된 유추 및 배포 구성을 사용하여 등록된 모델(`sentiment:1`)의 버전 1을 배포합니다.

```azurecli
az ml model deploy -n myservice -m sentiment:1 --ic inferenceConfig.json --dc deploymentConfig.json
```

자세한 내용은 [az ml model deploy](/cli/azure/ml/model#az_ml_model_deploy) 참조를 확인하세요.

배포에 대한 자세한 내용은 [모델 배포 방법 및 배포 위치](how-to-deploy-and-where.md)를 참조하세요.

## <a name="request-response-consumption"></a>요청-응답 사용

배포 후에는 채점 URI가 표시됩니다. 클라이언트는 이 URI를 사용하여 서비스에 요청을 제출할 수 있습니다. 다음 예제는 서비스에 데이터를 제출하고 응답을 표시하는 간단한 Python 클라이언트입니다.

```python
import requests
import json

scoring_uri = 'scoring uri for your service'
headers = {'Content-Type':'application/json'}

test_data = json.dumps({'text': 'Today is a great day!'})

response = requests.post(scoring_uri, data=test_data, headers=headers)
print(response.status_code)
print(response.elapsed)
print(response.json())
```

배포된 서비스를 사용하는 방법에 대한 자세한 내용은 [클라이언트 만들기](how-to-consume-web-service.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계

* [Application Insights를 사용하여 Azure Machine Learning 모델 모니터링](how-to-enable-app-insights.md)
* [프로덕션 환경에서 모델용 데이터 수집](how-to-enable-data-collection.md)
* [배포된 모델에 대한 클라이언트를 만드는 방법](how-to-consume-web-service.md)