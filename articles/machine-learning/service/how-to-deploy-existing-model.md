---
title: 기존 모델을 사용 하는 방법
titleSuffix: Azure Machine Learning service
description: 외부 서비스 학습 된 모델을 사용 하 여 Azure Machine Learning 서비스를 사용 하는 방법을 알아봅니다. Azure Machine Learning 서비스 외부에서 생성 하는 모델을 등록 하 고 웹 서비스 또는 Azure IoT Edge 모듈로 배포할 수 있습니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: jordane
author: jpe316
ms.reviewer: larryfr
ms.date: 06/19/2019
ms.openlocfilehash: 332129c9847c317369d5631c3af584da9430e9dd
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/28/2019
ms.locfileid: "67453685"
---
# <a name="how-to-use-an-existing-model-with-azure-machine-learning-service"></a>Azure Machine Learning 서비스를 사용 하 여 기존 모델을 사용 하는 방법

Azure Machine Learning 서비스를 사용 하 여 모델을 학습 하는 기존 컴퓨터를 사용 하는 방법에 알아봅니다.

기계 학습 모델을 Azure Machine Learning 서비스 외부 학습 하는 경우 IoT Edge 장치 또는 웹 서비스로 모델을 배포 하려면 서비스를 계속 사용할 수 있습니다. 

> [!TIP]
> 이 문서는 등록 하 고 기존 모델을 배포에 기본 정보를 제공 합니다. 배포 되 면 Azure Machine Learning 서비스 모델에 대 한 모니터링을 제공 합니다. 또한 데이터 드리프트 분석 또는 교육 새 버전의 모델을 사용할 수 있는 배포, 전송 된 입력된 데이터를 저장할 수 있습니다.
>
> 여기에 사용 되는 용어 및 개념에 대 한 자세한 내용은 참조 하세요. [관리를 배포 하 고 기계 학습 모델 모니터링](concept-model-management-and-deployment.md)합니다.
>
> 배포 프로세스에 대 한 일반적인 내용은 참조 하세요. [Azure Machine Learning 서비스를 사용 하 여 모델 배포](how-to-deploy-and-where.md)합니다.

## <a name="prerequisites"></a>필수 조건

* Azure Machine Learning 서비스 작업 영역. 자세한 내용은 [작업 영역 만들기](setup-create-workspace.md)합니다.

    > [!TIP]
    > 이 문서의 Python 예제에서는 `ws` 변수가 Azure Machine Learning 서비스 작업 영역으로 설정 됩니다.
    >
    > CLI 예제 사용의 자리 표시자 `myworkspace` 고 `myresourcegroup`입니다. 이러한 작업 영역 및 포함 하는 리소스 그룹의 이름으로 대체 합니다.

* Azure Machine Learning SDK입니다. 자세한 내용은 Python SDK 섹션을 참조 하세요 [작업 영역 만들기](setup-create-workspace.md#sdk)합니다.

* 합니다 [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) 하 고 [Machine Learning CLI 확장](reference-azure-machine-learning-cli.md)합니다.

* 학습된 모델. 모델 개발 환경에서 하나 이상의 파일에 유지 해야 합니다.

    > [!NOTE]
    > Azure Machine Learning 서비스 외부 학습 모델을 등록을 보여 주기 위해이 문서의 예제에서는 코드 조각 Paolo Ripamonti Twitter 감정 분석 프로젝트에서 만든 모델을 사용 합니다. [ https://www.kaggle.com/paoloripamonti/twitter-sentiment-analysis ](https://www.kaggle.com/paoloripamonti/twitter-sentiment-analysis)합니다.

## <a name="register-the-models"></a>모델 등록

모델 등록 저장, 버전 및 모델 작업 영역에 대 한 메타 데이터를 추적할 수 있습니다. 다음 Python 및 CLI 예제에서는 `models` 디렉터리에는 `model.h5`, `model.w2v`, `encoder.pkl`, 및 `tokenizer.pkl` 파일입니다. 이 예제에서는에 포함 된 파일을 업로드 합니다 `models` 이라는 새 모델 등록으로 디렉터리 `sentiment`:

```python
from azureml.core.model import Model
# Tip: When model_path is set to a directory, you can use the child_paths parameter to include
#      only some of the files from the directory
model = Model.register(model_path = "./models",
                       model_name = "sentiment",
                       description = "Sentiment analysis model trained outside Azure Machine Learning service",
                       workspace = ws)
```

자세한 내용은 참조는 [Model.register()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model(class)?view=azure-ml-py#register-workspace--model-path--model-name--tags-none--properties-none--description-none--datasets-none--model-framework-none--model-framework-version-none--child-paths-none-) 참조 합니다.

```azurecli
az ml model register -p ./models -n sentiment -w myworkspace -g myresourcegroup
```

자세한 내용은 참조는 [az ml 모델 등록](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/model?view=azure-cli-latest#ext-azure-cli-ml-az-ml-model-register) 참조 합니다.


모델 등록에 대 한 자세한 내용은 일반적으로 참조 [관리를 배포 하 고 기계 학습 모델 모니터링](concept-model-management-and-deployment.md)합니다.


## <a name="define-inference-configuration"></a>유추 구성 정의

배포 된 모델을 실행 하는 데 사용 환경을 정의 하는 유추 구성 합니다. 유추 구성을 배포할 때 모델을 실행 하는 데 사용 되는 다음 파일을 참조 합니다.

* 런타임입니다. 런타임에 대 한 유일한 유효 값에는 현재 Python 경우
* 시작 스크립트가 있습니다. 이 파일 (라는 `score.py`) 배포 된 서비스를 시작할 때 모델을 로드 합니다. 데이터 받기, 모델에 전달 및 응답을 반환 하는 일을 담당 이기도 합니다.
* Conda 환경 파일입니다. 이 파일에는 모델 및 입력 스크립트를 실행 하는 데 필요한 Python 패키지를 정의 합니다. 

다음 예제에서는 Python SDK를 사용 하 여 기본 유추 구성을 보여 줍니다.

```python
from azureml.core.model import InferenceConfig

inference_config = InferenceConfig(runtime= "python", 
                                   entry_script="score.py",
                                   conda_file="myenv.yml")
```

자세한 내용은 참조는 [InferenceConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.inferenceconfig?view=azure-ml-py) 참조 합니다.

CLI는 YAML 파일에서 유추 구성을 로드합니다.

```yaml
{
   "entryScript": "score.py",
   "runtime": "python",
   "condaFile": "myenv.yml"
}
```

유추 구성에 대 한 자세한 내용은 참조 하세요. [Azure Machine Learning 서비스를 사용 하 여 모델 배포](how-to-deploy-and-where.md)합니다.

### <a name="entry-script"></a>항목 스크립트

입력 스크립트에 두 개의 필수 함수 `init()` 고 `run(data)`입니다. 이러한 함수는 시작 시 서비스를 초기화 하 고 클라이언트에 의해 전달 된 요청 데이터를 사용 하 여 모델을 실행에 사용 됩니다. 스크립트의 나머지 부분 로드 및 실행 모델을 처리 합니다.

> [!IMPORTANT]
> 모든 모델에 대해 작동 하는 제네릭 엔트리 스크립트가 없습니다. 항상 사용 되는 모델에는 것이 다릅니다. 모델을 로드 하는 방법을 이해 해야 할 모델에서 예상 하는 데이터 형식 및 모델을 사용 하 여 데이터 점수 매기기 방법입니다.

다음 Python 코드는 예제 항목 스크립트 (`score.py`):

```python
import pickle
import json
import time
from keras.models import load_model
from keras.preprocessing.sequence import pad_sequences
from gensim.models.word2vec import Word2Vec
from azureml.core.model import Model

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

    # Get the path where the model(s) registered as the name 'sentiment' can be found.
    model_path = Model.get_model_path('sentiment')
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

입력 스크립트에 대 한 자세한 내용은 참조 하세요. [Azure Machine Learning 서비스를 사용 하 여 모델 배포](how-to-deploy-and-where.md)합니다.

### <a name="conda-environment"></a>Conda 환경

다음 YAML에 모델 및 입력 스크립트를 실행 하는 데 필요한 conda 환경을 설명 합니다.

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

자세한 내용은 [Azure Machine Learning 서비스를 사용 하 여 모델 배포](how-to-deploy-and-where.md)합니다.

## <a name="define-deployment"></a>배포를 정의 합니다.

합니다 [Webservice](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice?view=azure-ml-py) 패키지 배포에 사용 되는 클래스를 포함 합니다. 사용 하는 클래스는 모델을 배포 하는 위치를 결정 합니다. Azure Kubernetes Service에서 웹 서비스로 배포 하려면 사용 예를 들어 [AksWebService.deploy_configuration()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.akswebservice?view=azure-ml-py#deploy-configuration-autoscale-enabled-none--autoscale-min-replicas-none--autoscale-max-replicas-none--autoscale-refresh-seconds-none--autoscale-target-utilization-none--collect-model-data-none--auth-enabled-none--cpu-cores-none--memory-gb-none--enable-app-insights-none--scoring-timeout-ms-none--replica-max-concurrent-requests-none--max-request-wait-time-none--num-replicas-none--primary-key-none--secondary-key-none--tags-none--properties-none--description-none--gpu-cores-none--period-seconds-none--initial-delay-seconds-none--timeout-seconds-none--success-threshold-none--failure-threshold-none--namespace-none-) 배포 구성을 만듭니다.

다음 Python 코드에는 로컬 배포에 대 한 배포 구성을 정의합니다. 이 구성은 로컬 컴퓨터에 웹 서비스로 모델을 배포합니다.

> [!IMPORTANT]
> 로컬 배포를 설치 중인 필요 [Docker](https://www.docker.com/) 로컬 컴퓨터에서:

```python
from azureml.core.webservice import LocalWebservice

deployment_config = LocalWebservice.deploy_configuration()
```

자세한 내용은 참조는 [LocalWebservice.deploy_configuration()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.localwebservice?view=azure-ml-py#deploy-configuration-port-none-) 참조 합니다.

CLI는 YAML 파일에서 배포 구성을 로드합니다.

```YAML
{
    "computeType": "LOCAL"
}
```

Azure cloud에서 Azure Kubernetes Service와 같은 다양 한 계산 대상으로 배포 하는 것은 배포 구성을 변경 하는 것 만큼 쉽습니다. 자세한 내용은 [하는 방법 및 모델을 배포할 수 있는 위치](how-to-deploy-and-where.md)합니다.

## <a name="deploy-the-model"></a>모델 배포

다음 예제에서는 명명 된 등록 된 모델에 대 한 정보를 로드 `sentiment`, 라는 서비스로 배포 하 `sentiment`합니다. 배포 하는 동안 유추 구성 및 배포 구성 만들기 및 구성 서비스 환경에 사용 됩니다.

```python
from azureml.core.model import Model

model = Model(ws, name='sentiment')
service = Model.deploy(ws, 'myservice', [model], inference_config, deployment_config)

service.wait_for_deployment(True)
print(service.state)
print("scoring URI: " + service.scoring_uri)
```

자세한 내용은 참조는 [Model.deploy()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#deploy-workspace--name--models--inference-config--deployment-config-none--deployment-target-none-) 참조 합니다.

CLI에서 모델을 배포 하려면 다음 명령을 사용 합니다. 이 명령은 배포 된 모델의 버전 1 (`sentiment:1`)에 저장 된 유추 및 배포 구성을 사용 하 여 `inferenceConfig.json` 및 `deploymentConfig.json` 파일:

```azurecli
az ml model deploy -n myservice -m sentiment:1 --ic inferenceConfig.json --dc deploymentConfig.json
```

자세한 내용은 참조는 [az ml 모델 배포](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/model?view=azure-cli-latest#ext-azure-cli-ml-az-ml-model-deploy) 참조 합니다.

배포에 대 한 자세한 내용은 참조 하세요. [하는 방법 및 모델을 배포할 수 있는 위치](how-to-deploy-and-where.md)합니다.

## <a name="request-response-consumption"></a>요청-응답 사용

배포 후 점수 매기기 URI 표시 됩니다. 이 URI는 서비스에 요청을 제출 하는 클라이언트가 사용할 수 있습니다. 다음 예제는 서비스에 데이터를 전송 하 고 응답을 표시 하는 기본 Python 클라이언트:

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

배포 된 서비스를 사용 하는 방법에 대 한 자세한 내용은 참조 하세요. [클라이언트를 만드는](how-to-consume-web-service.md)합니다.

## <a name="next-steps"></a>다음 단계

* [Application Insights를 사용하여 Azure Machine Learning 모델 모니터링](how-to-enable-app-insights.md)
* [프로덕션 환경에서 모델용 데이터 수집](how-to-enable-data-collection.md)
* [방법 및 모델을 배포할 수 있는 위치](how-to-deploy-and-where.md)
* [배포 된 모델에 대 한 클라이언트를 만드는 방법](how-to-consume-web-service.md)