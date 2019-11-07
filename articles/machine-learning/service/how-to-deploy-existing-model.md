---
title: 기존 모델 사용 및 배포
titleSuffix: Azure Machine Learning
description: 서비스 외부에서 학습 된 모델에 Azure Machine Learning를 사용 하는 방법에 대해 알아봅니다. Azure Machine Learning 외부에서 만든 모델을 등록 한 다음 웹 서비스 또는 Azure IoT Edge 모듈로 배포할 수 있습니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: jordane
author: jpe316
ms.reviewer: larryfr
ms.date: 11/06/2019
ms.openlocfilehash: 4e82d52428195797fb5cdc7a54e900fff7d37655
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73671441"
---
# <a name="use-an-existing-model-with-azure-machine-learning"></a>Azure Machine Learning에서 기존 모델 사용
[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-basic-enterprise-sku.md)]

Azure Machine Learning에서 기존 machine learning 모델을 사용 하는 방법에 대해 알아봅니다.

Azure Machine Learning 외부에서 학습 된 machine learning 모델을 사용 하는 경우에도 서비스를 사용 하 여 모델을 웹 서비스로 배포 하거나 IoT Edge 장치로 배포할 수 있습니다. 

> [!TIP]
> 이 문서에서는 기존 모델을 등록 하 고 배포 하는 방법에 대 한 기본 정보를 제공 합니다. 배포 된 Azure Machine Learning 모델에 대 한 모니터링을 제공 합니다. 또한 배포에 전송 된 입력 데이터를 저장 하 여 데이터 드리프트 분석에 사용 하거나 모델의 새 버전을 학습할 수 있습니다.
>
> 여기에 사용 된 개념과 용어에 대 한 자세한 내용은 [machine learning 모델 관리, 배포 및 모니터링](concept-model-management-and-deployment.md)을 참조 하세요.
>
> 배포 프로세스에 대 한 일반 정보는 [Azure Machine Learning를 사용 하 여 모델 배포](how-to-deploy-and-where.md)를 참조 하세요.

## <a name="prerequisites"></a>필수 조건

* Azure Machine Learning 작업 영역 자세한 내용은 [작업 영역 만들기](how-to-manage-workspace.md)를 참조 하세요.

    > [!TIP]
    > 이 문서의 Python 예제에서는 `ws` 변수가 Azure Machine Learning 작업 영역으로 설정 되어 있다고 가정 합니다.
    >
    > CLI 예제에서는 `myworkspace` 및 `myresourcegroup`의 자리 표시자를 사용 합니다. 이를 작업 영역 이름 및이를 포함 하는 리소스 그룹으로 바꿉니다.

* [AZURE MACHINE LEARNING SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py)입니다.  

* [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) 및 [Machine Learning CLI 확장](reference-azure-machine-learning-cli.md)입니다.

* 학습된 모델. 모델은 개발 환경에서 하나 이상의 파일에 유지 되어야 합니다.

    > [!NOTE]
    > Azure Machine Learning 외부에서 학습 한 모델 등록을 보여 주기 위해이 문서의 예제 코드 조각은 Paolo Ripamonti의 Twitter 감정 분석 프로젝트에서 만든 모델을 사용 합니다. [https://www.kaggle.com/paoloripamonti/twitter-sentiment-analysis](https://www.kaggle.com/paoloripamonti/twitter-sentiment-analysis).

## <a name="register-the-models"></a>모델 등록

모델을 등록 하면 작업 영역에서 모델에 대 한 메타 데이터를 저장 하 고, 버전을 추적 하 고, 추적할 수 있습니다. 다음 Python 및 CLI 예제에서 `models` 디렉터리에는 `model.h5`, `model.w2v`, `encoder.pkl`및 `tokenizer.pkl` 파일이 포함 되어 있습니다. 이 예에서는 `models` 디렉터리에 포함 된 파일을 `sentiment`라는 새 모델 등록으로 업로드 합니다.

```python
from azureml.core.model import Model
# Tip: When model_path is set to a directory, you can use the child_paths parameter to include
#      only some of the files from the directory
model = Model.register(model_path = "./models",
                       model_name = "sentiment",
                       description = "Sentiment analysis model trained outside Azure Machine Learning",
                       workspace = ws)
```

자세한 내용은 [Model. register ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model(class)?view=azure-ml-py#register-workspace--model-path--model-name--tags-none--properties-none--description-none--datasets-none--model-framework-none--model-framework-version-none--child-paths-none-) 참조를 참조 하세요.

```azurecli
az ml model register -p ./models -n sentiment -w myworkspace -g myresourcegroup
```

자세한 내용은 [az ml model register](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/model?view=azure-cli-latest#ext-azure-cli-ml-az-ml-model-register) reference를 참조 하세요.


일반적인 모델 등록에 대 한 자세한 내용은 [machine learning 모델 관리, 배포 및 모니터링](concept-model-management-and-deployment.md)을 참조 하세요.


## <a name="define-inference-configuration"></a>유추 구성 정의

유추 구성은 배포 된 모델을 실행 하는 데 사용 되는 환경을 정의 합니다. 유추 구성은 모델을 배포할 때 모델을 실행 하는 데 사용 되는 다음 엔터티를 참조 합니다.

* 항목 스크립트입니다. 이 파일 (명명 된 `score.py`)은 배포 된 서비스가 시작 될 때 모델을 로드 합니다. 또한 데이터를 수신 하 고 모델에 전달한 다음 응답을 반환 하는 일을 담당 합니다.
* Azure Machine Learning [환경](how-to-use-environments.md)입니다. 환경은 모델 및 항목 스크립트를 실행 하는 데 필요한 소프트웨어 종속성을 정의 합니다.

다음 예제에서는 SDK를 사용 하 여 환경을 만든 다음이를 유추 구성과 함께 사용 하는 방법을 보여 줍니다.

```python
from azureml.core.model import InferenceConfig
from azureml.core import Environment
from azureml.core.environment import CondaDependencies

# Create the environment
myenv = Environment(name="myenv")
conda_dep = CondaDependencies()

# Define the packages needed by the model and scripts
conda_dep.add_conda_package("tensorflow")
conda_dep.add_conda_package("numpy")
conda_dep.add_conda_package("scikit-learn")
conda_dep.add_pip_package("keras")

# Adds dependencies to PythonSection of myenv
myenv.python.conda_dependencies=conda_dep

inference_config = InferenceConfig(entry_script="score.py",
                                   environment=myenv)
```

자세한 내용은 다음 문서를 참조하세요.

+ [환경을 사용 하는 방법](how-to-use-environments.md)
+ [InferenceConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.inferenceconfig?view=azure-ml-py) 참조입니다.


CLI는 YAML 파일에서 유추 구성을 로드 합니다.

```yaml
{
   "entryScript": "score.py",
   "runtime": "python",
   "condaFile": "myenv.yml"
}
```

CLI를 사용 하 여 conda 환경은 유추 구성에서 참조 하는 `myenv.yml` 파일에 정의 됩니다. 다음 YAML은이 파일의 내용입니다.

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
```

유추 구성에 대 한 자세한 내용은 [Azure Machine Learning를 사용 하 여 모델 배포](how-to-deploy-and-where.md)를 참조 하세요.

### <a name="entry-script"></a>항목 스크립트

항목 스크립트에는 두 개의 필수 함수인 `init()`와 `run(data)`만 있습니다. 이러한 함수는 시작 시 서비스를 초기화 하 고 클라이언트에서 전달 된 요청 데이터를 사용 하 여 모델을 실행 하는 데 사용 됩니다. 스크립트의 나머지 부분은 모델 로드 및 실행을 처리 합니다.

> [!IMPORTANT]
> 모든 모델에 대해 작동 하는 일반 항목 스크립트가 없습니다. 사용 되는 모델과 항상 관련이 있습니다. 모델을 로드 하는 방법, 모델에 필요한 데이터 형식 및 모델을 사용 하 여 데이터의 점수를 매기는 방법을 이해 해야 합니다.

다음 Python 코드는 예제 입력 스크립트 (`score.py`)입니다.

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

항목 스크립트에 대 한 자세한 내용은 [Azure Machine Learning를 사용 하 여 모델 배포](how-to-deploy-and-where.md)를 참조 하세요.

## <a name="define-deployment"></a>배포 정의

[웹 서비스](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice?view=azure-ml-py) 패키지에는 배포에 사용 되는 클래스가 포함 되어 있습니다. 모델을 배포 하는 위치를 결정 하는 데 사용 하는 클래스입니다. 예를 들어 Azure Kubernetes Service에서 웹 서비스로 배포 하려면 [deploy_configuration ()](/python/api/azureml-core/azureml.core.webservice.akswebservice?view=azure-ml-py#deploy-configuration-autoscale-enabled-none--autoscale-min-replicas-none--autoscale-max-replicas-none--autoscale-refresh-seconds-none--autoscale-target-utilization-none--collect-model-data-none--auth-enabled-none--cpu-cores-none--memory-gb-none--enable-app-insights-none--scoring-timeout-ms-none--replica-max-concurrent-requests-none--max-request-wait-time-none--num-replicas-none--primary-key-none--secondary-key-none--tags-none--properties-none--description-none--gpu-cores-none--period-seconds-none--initial-delay-seconds-none--timeout-seconds-none--success-threshold-none--failure-threshold-none--namespace-none--token-auth-enabled-none-) 를 사용 하 여 배포 구성을 만듭니다.

다음 Python 코드는 로컬 배포에 대 한 배포 구성을 정의 합니다. 이 구성은 모델을 로컬 컴퓨터에 웹 서비스로 배포 합니다.

> [!IMPORTANT]
> 로컬 배포를 수행 하려면 로컬 컴퓨터에서 [Docker](https://www.docker.com/) 를 정상적으로 설치 해야 합니다.

```python
from azureml.core.webservice import LocalWebservice

deployment_config = LocalWebservice.deploy_configuration()
```

자세한 내용은 [deploy_configuration ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.localwebservice?view=azure-ml-py#deploy-configuration-port-none-) 참조를 참조 하세요.

CLI는 YAML 파일에서 배포 구성을 로드 합니다.

```YAML
{
    "computeType": "LOCAL"
}
```

Azure 클라우드에서 Azure Kubernetes Service와 같은 다른 계산 대상에 배포 하는 것은 배포 구성을 변경 하는 것 만큼 쉽습니다. 자세한 내용은 [모델을 배포 하는 방법 및 위치](how-to-deploy-and-where.md)를 참조 하세요.

## <a name="deploy-the-model"></a>모델 배포

다음 예에서는 `sentiment`등록 된 모델에 대 한 정보를 로드 한 다음 `sentiment`이라는 서비스로 배포 합니다. 배포 하는 동안 유추 구성 및 배포 구성은 서비스 환경을 만들고 구성 하는 데 사용 됩니다.

```python
from azureml.core.model import Model

model = Model(ws, name='sentiment')
service = Model.deploy(ws, 'myservice', [model], inference_config, deployment_config)

service.wait_for_deployment(True)
print(service.state)
print("scoring URI: " + service.scoring_uri)
```

자세한 내용은 [모델인 ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#deploy-workspace--name--models--inference-config--deployment-config-none--deployment-target-none-) 참조를 참조 하세요.

CLI에서 모델을 배포 하려면 다음 명령을 사용 합니다. 이 명령은 `inferenceConfig.json` 및 `deploymentConfig.json` 파일에 저장 된 유추 및 배포 구성을 사용 하 여 등록 된 모델 (`sentiment:1`)의 버전 1을 배포 합니다.

```azurecli
az ml model deploy -n myservice -m sentiment:1 --ic inferenceConfig.json --dc deploymentConfig.json
```

자세한 내용은 [az ml model deploy](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/model?view=azure-cli-latest#ext-azure-cli-ml-az-ml-model-deploy) reference를 참조 하세요.

배포에 대 한 자세한 내용은 [모델을 배포 하는 방법 및 위치](how-to-deploy-and-where.md)를 참조 하세요.

## <a name="request-response-consumption"></a>요청-응답 소비

배포 후 점수 매기기 URI가 표시 됩니다. 클라이언트는이 URI를 사용 하 여 서비스에 요청을 제출할 수 있습니다. 다음 예제는 서비스에 데이터를 전송 하 고 응답을 표시 하는 기본 Python 클라이언트입니다.

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

배포 된 서비스를 사용 하는 방법에 대 한 자세한 내용은 [클라이언트 만들기](how-to-consume-web-service.md)를 참조 하세요.

## <a name="next-steps"></a>다음 단계

* [Application Insights를 사용하여 Azure Machine Learning 모델 모니터링](how-to-enable-app-insights.md)
* [프로덕션 환경에서 모델용 데이터 수집](how-to-enable-data-collection.md)
* [모델을 배포 하는 방법 및 위치](how-to-deploy-and-where.md)
* [배포 된 모델에 대 한 클라이언트를 만드는 방법](how-to-consume-web-service.md)
