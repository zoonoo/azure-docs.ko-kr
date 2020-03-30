---
title: 기존 모델 사용 및 배포
titleSuffix: Azure Machine Learning
description: 서비스 외부에서 학습된 모델과 함께 Azure 기계 학습을 사용하는 방법에 대해 알아봅니다. Azure 기계 학습 외부에서 만든 모델을 등록한 다음 웹 서비스 또는 Azure IoT Edge 모듈로 배포할 수 있습니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: jordane
author: jpe316
ms.reviewer: larryfr
ms.date: 03/17/2020
ms.openlocfilehash: 924bd2fdba2359e6f1108c39802ad3ce95ebdf07
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79472378"
---
# <a name="use-an-existing-model-with-azure-machine-learning"></a>Azure 기계 학습을 사용하여 기존 모델 사용
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Azure 기계 학습을 사용하여 기존 기계 학습 모델을 사용하는 방법에 대해 알아봅니다.

Azure Machine Learning 외부에서 학습된 기계 학습 모델이 있는 경우에도 서비스를 사용하여 모델을 웹 서비스또는 IoT Edge 장치로 배포할 수 있습니다. 

> [!TIP]
> 이 문서에서는 기존 모델을 등록하고 배포하는 방법에 대한 기본 정보를 제공합니다. 배포되면 Azure 기계 학습은 모델에 대한 모니터링을 제공합니다. 또한 배포로 전송된 입력 데이터를 저장할 수 있으며, 이 입력은 데이터 드리프트 분석 또는 모델의 새 버전 교육에 사용할 수 있습니다.
>
> 여기에서 사용되는 개념 및 용어에 대한 자세한 내용은 [기계 학습 모델 관리, 배포 및 모니터링을](concept-model-management-and-deployment.md)참조하십시오.
>
> 배포 프로세스에 대한 일반적인 정보는 [Azure 기계 학습을 통해 모델 배포를](how-to-deploy-and-where.md)참조하십시오.

## <a name="prerequisites"></a>사전 요구 사항

* Azure Machine Learning 작업 영역 자세한 내용은 [작업 영역 만들기를](how-to-manage-workspace.md)참조하십시오.

    > [!TIP]
    > 이 문서의 Python 예제에서는 `ws` 변수가 Azure 기계 학습 작업 영역으로 설정되어 있다고 가정합니다.
    >
    > CLI 예제는 `myworkspace` 의 `myresourcegroup`자리 표시자를 사용합니다. 작업 영역의 이름과 작업 영역이 포함된 리소스 그룹으로 바꿉니다.

* [Azure 기계 학습 SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py).  

* [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) 및 [기계 학습 CLI 확장.](reference-azure-machine-learning-cli.md)

* 학습된 모델. 모델은 개발 환경에서 하나 이상의 파일에 유지되어야 합니다.

    > [!NOTE]
    > Azure Machine Learning 외부에서 학습된 모델을 등록하는 방법을 설명하기 위해 이 문서의 예제 코드 조각은 Paolo Ripamonti의 트위터 감정 분석 프로젝트에서 만든 모델을 [https://www.kaggle.com/paoloripamonti/twitter-sentiment-analysis](https://www.kaggle.com/paoloripamonti/twitter-sentiment-analysis)사용합니다.

## <a name="register-the-models"></a>모델 등록

모델을 등록하면 작업 영역에서 모델에 대한 메타데이터를 저장, 버전 및 추적할 수 있습니다. 다음 Python 및 CLI 예제에서 `models` 디렉터리는 `model.h5`" `model.w2v` `encoder.pkl`및 `tokenizer.pkl` 파일을 포함합니다. 이 예제에서는 `models` 디렉터리에 포함된 파일을 다음과 같은 `sentiment`새 모델 등록으로 업로드합니다.

```python
from azureml.core.model import Model
# Tip: When model_path is set to a directory, you can use the child_paths parameter to include
#      only some of the files from the directory
model = Model.register(model_path = "./models",
                       model_name = "sentiment",
                       description = "Sentiment analysis model trained outside Azure Machine Learning",
                       workspace = ws)
```

자세한 내용은 [Model.register()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model(class)?view=azure-ml-py#register-workspace--model-path--model-name--tags-none--properties-none--description-none--datasets-none--model-framework-none--model-framework-version-none--child-paths-none--sample-input-dataset-none--sample-output-dataset-none--resource-configuration-none-) 참조를 참조하십시오.

```azurecli
az ml model register -p ./models -n sentiment -w myworkspace -g myresourcegroup
```

> [!TIP]
> 등록된 모델에 추가 `tags` `properties` 및 사전 개체를 설정할 수도 있습니다. 이러한 값은 나중에 특정 모델을 식별하는 데 사용할 수 있습니다. 예를 들어 사용되는 프레임워크, 학습 매개 변수 등입니다.

자세한 내용은 az [ml 모델 레지스터](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/model?view=azure-cli-latest#ext-azure-cli-ml-az-ml-model-register) 참조를 참조하십시오.


일반적으로 모델 등록에 대한 자세한 내용은 [기계 학습 모델 관리, 배포 및 모니터링을](concept-model-management-and-deployment.md)참조하십시오.

## <a name="define-inference-configuration"></a>추론 구성 정의

추론 구성은 배포된 모델을 실행하는 데 사용되는 환경을 정의합니다. 추론 구성은 모델을 배포할 때 모델을 실행하는 데 사용되는 다음 엔터티를 참조합니다.

* 항목 스크립트. 이 파일(명명)은 `score.py`배포된 서비스가 시작될 때 모델을 로드합니다. 또한 데이터를 수신하고 모델에 전달한 다음 응답을 반환해야 합니다.
* Azure 기계 학습 [환경.](how-to-use-environments.md) 환경은 모델 및 입력 스크립트를 실행하는 데 필요한 소프트웨어 종속성을 정의합니다.

다음 예제에서는 SDK를 사용하여 환경을 만든 다음 추론 구성과 함께 사용하는 방법을 보여 주십을 보여 주십습니다.

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

자세한 내용은 다음 문서를 참조하세요.

+ [사용 방법 환경](how-to-use-environments.md).
+ [추론구성](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.inferenceconfig?view=azure-ml-py) 참조.


CLI는 YAML 파일에서 추론 구성을 로드합니다.

```yaml
{
   "entryScript": "score.py",
   "runtime": "python",
   "condaFile": "myenv.yml"
}
```

CLI를 사용하면 추론 구성에서 참조하는 `myenv.yml` 파일에 conda 환경이 정의됩니다. 다음은 이 파일의 내용입니다.

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

추론 구성에 대한 자세한 내용은 [Azure 기계 학습을 통해 모델 배포를](how-to-deploy-and-where.md)참조하십시오.

### <a name="entry-script"></a>입력 스크립트

입력 스크립트에는 두 개의 필수 `init()` `run(data)`함수와 . 이러한 함수는 시작 시 서비스를 초기화하고 클라이언트에서 전달된 요청 데이터를 사용하여 모델을 실행하는 데 사용됩니다. 나머지 스크립트는 모델 로드 및 실행을 처리합니다.

> [!IMPORTANT]
> 모든 모델에 대해 작동하는 일반 항목 스크립트가 없습니다. 항상 사용되는 모델에만 해당됩니다. 모델을 로드하는 방법, 모델이 기대하는 데이터 형식 및 모델을 사용하여 데이터의 점수를 매기는 방법을 이해해야 합니다.

다음 파이썬 코드는 예제`score.py`항목 스크립트 () 입니다.

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

입력 스크립트에 대한 자세한 내용은 [Azure 기계 학습을 통해 모델 배포를](how-to-deploy-and-where.md)참조하십시오.

## <a name="define-deployment"></a>배포 정의

[웹 서비스](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice?view=azure-ml-py) 패키지에는 배포에 사용되는 클래스가 포함되어 있습니다. 사용하는 클래스에 따라 모델이 배포되는 위치가 결정됩니다. 예를 들어 Azure Kubernetes 서비스에서 웹 서비스로 배포하려면 [AksWebService.deploy_configuration()를](/python/api/azureml-core/azureml.core.webservice.akswebservice?view=azure-ml-py#deploy-configuration-autoscale-enabled-none--autoscale-min-replicas-none--autoscale-max-replicas-none--autoscale-refresh-seconds-none--autoscale-target-utilization-none--collect-model-data-none--auth-enabled-none--cpu-cores-none--memory-gb-none--enable-app-insights-none--scoring-timeout-ms-none--replica-max-concurrent-requests-none--max-request-wait-time-none--num-replicas-none--primary-key-none--secondary-key-none--tags-none--properties-none--description-none--gpu-cores-none--period-seconds-none--initial-delay-seconds-none--timeout-seconds-none--success-threshold-none--failure-threshold-none--namespace-none--token-auth-enabled-none--compute-target-name-none-) 사용하여 배포 구성을 만듭니다.

다음 Python 코드는 로컬 배포에 대한 배포 구성을 정의합니다. 이 구성은 모델을 로컬 컴퓨터에 웹 서비스로 배포합니다.

> [!IMPORTANT]
> 로컬 배포를 수행하려면 로컬 컴퓨터에 [Docker를](https://www.docker.com/) 설치해야 합니다.

```python
from azureml.core.webservice import LocalWebservice

deployment_config = LocalWebservice.deploy_configuration()
```

자세한 내용은 [LocalWebservice.deploy_configuration()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.localwebservice?view=azure-ml-py#deploy-configuration-port-none-) 참조를 참조하십시오.

CLI는 YAML 파일에서 배포 구성을 로드합니다.

```YAML
{
    "computeType": "LOCAL"
}
```

Azure 클라우드의 Azure Kubernetes 서비스와 같은 다른 계산 대상에 배포하는 것은 배포 구성을 변경하는 것만큼 쉽습니다. 자세한 내용은 [모델을 배포하는 방법 및 위치를](how-to-deploy-and-where.md)참조하십시오.

## <a name="deploy-the-model"></a>모델 배포

다음 예제에서는 에 이름이 지정된 `sentiment`등록된 모델에 대한 정보를 로드한 다음 을 서비스로 `sentiment`배포합니다. 배포 하는 동안 추론 구성 및 배포 구성 서비스 환경을 만들고 구성 하는 데 사용 됩니다.

```python
from azureml.core.model import Model

model = Model(ws, name='sentiment')
service = Model.deploy(ws, 'myservice', [model], inference_config, deployment_config)

service.wait_for_deployment(True)
print(service.state)
print("scoring URI: " + service.scoring_uri)
```

자세한 내용은 [Model.deploy()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#deploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-) 참조를 참조하십시오.

CLI에서 모델을 배포하려면 다음 명령을 사용합니다. 이 명령은 및`sentiment:1` `inferenceConfig.json` `deploymentConfig.json` 파일에 저장된 추론 및 배포 구성을 사용하여 등록된 모델()의 버전 1을 배포합니다.

```azurecli
az ml model deploy -n myservice -m sentiment:1 --ic inferenceConfig.json --dc deploymentConfig.json
```

자세한 내용은 az [ml 모델 배포](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/model?view=azure-cli-latest#ext-azure-cli-ml-az-ml-model-deploy) 참조를 참조하십시오.

배포에 대한 자세한 내용은 [모델을 배포하는 방법 및 위치를](how-to-deploy-and-where.md)참조하십시오.

## <a name="request-response-consumption"></a>요청 응답 소비

배포 후 점수 가점 URI가 표시됩니다. 이 URI는 클라이언트에서 서비스에 요청을 제출하는 데 사용할 수 있습니다. 다음 예제는 서비스에 데이터를 제출 하고 응답을 표시 하는 기본 파이썬 클라이언트입니다.

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

배포된 서비스를 사용하는 방법에 대한 자세한 내용은 [클라이언트 만들기](how-to-consume-web-service.md)를 참조하십시오.

## <a name="next-steps"></a>다음 단계

* [Application Insights를 사용하여 Azure Machine Learning 모델 모니터링](how-to-enable-app-insights.md)
* [프로덕션 환경에서 모델용 데이터 수집](how-to-enable-data-collection.md)
* [모델을 배포하는 방법 및 위치](how-to-deploy-and-where.md)
* [배포된 모델에 대한 클라이언트를 만드는 방법](how-to-consume-web-service.md)
