---
title: '이미지 분류 자습서: 모델 배포'
titleSuffix: Azure Machine Learning service
description: 이 자습서에서는 Azure Machine Learning 서비스를 사용하여 Python Jupyter 노트북에서 scikit-learn으로 이미지 분류 모델을 배포하는 방법을 보여줍니다. 이 자습서는 2부로 구성된 시리즈 중 제2부입니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: hning86
ms.author: haining
ms.reviewer: sgilley
ms.date: 09/24/2018
ms.custom: seodec18
ms.openlocfilehash: 887be89060a6d02eea74cd127cfbc93e48c0b3ff
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/30/2019
ms.locfileid: "55240865"
---
# <a name="tutorial-deploy-an-image-classification-model-in-azure-container-instances"></a>자습서: Azure Container Instance에서 이미지 분류 모델 배포

이 자습서는 **2부로 구성된 자습서 시리즈 중 제2부**입니다. [이전 자습서에서는](tutorial-train-models-with-aml.md), 기계 학습 모델을 학습한 후 클라우드에서 작업 영역에 모델을 등록합니다.  

이제 [Azure Container Instances](https://docs.microsoft.com/azure/container-instances/)에서 모델을 웹 서비스로 배포할 준비가 되었습니다. 웹 서비스는 이미지로, 이 경우 Docker 이미지입니다. 점수 매기기 논리와 모델 자체를 캡슐화합니다. 

이 자습서 부분에서는 Azure Machine Learning Service를 사용하여 다음 작업을 수행합니다.

> [!div class="checklist"]
> * 테스트 환경 설정
> * 작업 영역에서 모델 검색
> * 로컬에서 모델 테스트
> * Container Instances에 모델 배포
> * 배포된 모델 테스트

Container Instances는 프로덕션 배포에는 적합하지 않지만, 워크플로를 테스트하고 이해하는 데는 매우 적합합니다. 확장성 있는 프로덕션 배포에는 Azure Kubernetes Service를 사용하는 것이 좋습니다. 자세한 내용은 [배포 방법 및 위치](how-to-deploy-and-where.md)를 참조하세요.

## <a name="get-the-notebook"></a>Notebook 가져오기

사용자의 편의를 위해 이 자습서는 [Jupyter 노트북](https://github.com/Azure/MachineLearningNotebooks/blob/master/tutorials/img-classification-part2-deploy.ipynb)으로 제공됩니다. [Azure Notebooks](https://notebooks.azure.com/) 또는 사용자 고유의 Jupyter Notebook 서버에서 *tutorials/img-classification-part2-deploy.ipynb* Notebook을 실행합니다.

[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-in-azure-notebook.md)]

>[!NOTE]
> 이 문서의 코드는 Azure Machine Learning SDK 버전 1.0.2에서 테스트되었습니다.

## <a name="prerequisites"></a>필수 조건

다음 노트북에서 모델 학습을 수행합니다. [자습서(1부): Azure Machine Learning Service를 사용하여 이미지 분류 모델 학습](tutorial-train-models-with-aml.md) 노트북에서 모델 학습을 수행합니다.  


## <a name="set-up-the-environment"></a>환경 설정

테스트 환경 설정부터 시작합니다.

### <a name="import-packages"></a>패키지 가져오기

이 자습서에 필요한 Python 패키지를 가져옵니다.

```python
%matplotlib inline
import numpy as np
import matplotlib
import matplotlib.pyplot as plt
 
import azureml
from azureml.core import Workspace, Run

# display the core SDK version number
print("Azure ML SDK Version: ", azureml.core.VERSION)
```

### <a name="retrieve-the-model"></a>모델 검색

이전 자습서에서 작업 영역에 모델을 등록했습니다. 이제 이 작업 영역을 로드하고 로컬 디렉터리에 모델을 다운로드합니다.


```python
from azureml.core import Workspace
from azureml.core.model import Model

ws = Workspace.from_config()
model=Model(ws, 'sklearn_mnist')
model.download(target_dir = '.')
import os 
# verify the downloaded model file
os.stat('./sklearn_mnist_model.pkl')
```

## <a name="test-the-model-locally"></a>로컬에서 모델 테스트

배포 전에 모델이 로컬에서 작동하는지 확인합니다.
* 테스트 데이터 로드
* 테스트 데이터 예측
* 혼동 행렬 검사

### <a name="load-test-data"></a>테스트 데이터 로드

학습 자습서 중에 만든 **./data/** 디렉터리에서 테스트 데이터를 로드합니다.

```python
from utils import load_data

# note we also shrink the intensity values (X) from 0-255 to 0-1. This helps the neural network converge faster

X_test = load_data('./data/test-images.gz', False) / 255.0
y_test = load_data('./data/test-labels.gz', True).reshape(-1)

```

### <a name="predict-test-data"></a>테스트 데이터 예측

예측을 가져오기 위해 모델에 테스트 데이터 세트를 제공합니다.

```python
import pickle
from sklearn.externals import joblib

clf = joblib.load('./sklearn_mnist_model.pkl')
y_hat = clf.predict(X_test)
```

###  <a name="examine-the-confusion-matrix"></a>혼동 행렬을 검사합니다

테스트 집합에서 얼마나 많은 샘플 올바르게 분류되었는지를 확인하기 위해 혼동 행렬을 생성합니다. 잘못된 예측에 대해 잘못 분류된 값을 확인합니다. 

```python
from sklearn.metrics import confusion_matrix

conf_mx = confusion_matrix(y_test, y_hat)
print(conf_mx)
print('Overall accuracy:', np.average(y_hat == y_test))
```

출력은 혼동 행렬을 보여 줍니다.

    [[ 960    0    1    2    1    5    6    3    1    1]
     [   0 1112    3    1    0    1    5    1   12    0]
     [   9    8  920   20   10    4   10   11   37    3]
     [   4    0   17  921    2   21    4   12   20    9]
     [   1    2    5    3  915    0   10    2    6   38]
     [  10    2    0   41   10  770   17    7   28    7]
     [   9    3    7    2    6   20  907    1    3    0]
     [   2    7   22    5    8    1    1  950    5   27]
     [  10   15    5   21   15   27    7   11  851   12]
     [   7    8    2   13   32   13    0   24   12  898]]
    Overall accuracy: 0.9204
   

`matplotlib` 사용하여 혼동 행렬을 그래프로 표시합니다. 이 그래프에서 x-축은 실제 값을 나타내고 y-축은 예측된 값을 나타냅니다. 각 눈금의 색은 오류 비율을 나타냅니다. 색이 밝을수록 오류 비율이 높습니다. 예를 들어 여러 개의 5가 3으로 잘못 분류되어 있습니다. 따라서 (5,3)에서 밝은 눈금이 표시됩니다.

```python
# normalize the diagonal cells so that they don't overpower the rest of the cells when visualized
row_sums = conf_mx.sum(axis=1, keepdims=True)
norm_conf_mx = conf_mx / row_sums
np.fill_diagonal(norm_conf_mx, 0)

fig = plt.figure(figsize=(8,5))
ax = fig.add_subplot(111)
cax = ax.matshow(norm_conf_mx, cmap=plt.cm.bone)
ticks = np.arange(0, 10, 1)
ax.set_xticks(ticks)
ax.set_yticks(ticks)
ax.set_xticklabels(ticks)
ax.set_yticklabels(ticks)
fig.colorbar(cax)
plt.ylabel('true labels', fontsize=14)
plt.xlabel('predicted values', fontsize=14)
plt.savefig('conf.png')
plt.show()
```

![혼동 행렬을 보여 주는 차트](./media/tutorial-deploy-models-with-aml/confusion.png)

## <a name="deploy-as-a-web-service"></a>웹 서비스로 배포

모델을 테스트하고 결과에 만족하면 모델을 Container Instances에서 호스팅되는 웹 서비스로 배포합니다. 

Container Instances에 대한 올바른 환경을 빌드하려면 다음과 같은 구성 요소를 제공합니다.
* 모델을 사용하는 방법을 보여주는 채점 스크립트
* 설치해야 할 패키지가 무엇인지를 보여주는 환경 파일
* 컨테이너 인스턴스를 빌드하는 구성 파일
* 이전에 학습한 모델

<a name="make-script"></a>

### <a name="create-scoring-script"></a>채점 스크립트 만들기

**score.py**라는 채점 스크립트를 만듭니다. 웹 서비스 호출은 이 스크립트를 사용하여 모델을 사용하는 방법을 보여줍니다.

다음 두 개의 필수 함수를 채점 스크립트에 포함합니다.
* `init()` 함수는 일반적으로 모델을 전역 개체에 로드합니다. 이 함수는 Docker 컨테이너를 시작할 때 한 번만 실행됩니다. 

* `run(input_data)` 함수는 모델을 사용하여 입력 데이터를 기반으로 값을 예측합니다. 실행에 대한 입력 및 출력은 일반적으로 serialization 및 deserialization용으로 JSON을 사용하지만 다른 형식도 지원됩니다.

```python
%%writefile score.py
import json
import numpy as np
import os
import pickle
from sklearn.externals import joblib
from sklearn.linear_model import LogisticRegression

from azureml.core.model import Model

def init():
    global model
    # retrieve the path to the model file using the model name
    model_path = Model.get_model_path('sklearn_mnist')
    model = joblib.load(model_path)

def run(raw_data):
    data = np.array(json.loads(raw_data)['data'])
    # make prediction
    y_hat = model.predict(data)
    return json.dumps(y_hat.tolist())
```

<a name="make-myenv"></a>

### <a name="create-environment-file"></a>환경 파일 만들기

다음으로, 스크립트의 패키지 종속성을 모두 지정하는 **myenv.yml**이라는 환경 파일을 만듭니다. 이 파일은 해당 종속성 모두가 Docker 이미지에 설치되도록 하는 데 사용됩니다. 이 모델에는 `scikit-learn` 및 `azureml-sdk`가 필요합니다.

```python
from azureml.core.conda_dependencies import CondaDependencies 

myenv = CondaDependencies()
myenv.add_conda_package("scikit-learn")

with open("myenv.yml","w") as f:
    f.write(myenv.serialize_to_string())
```
`myenv.yml` 파일의 콘텐츠를 검토합니다.

```python
with open("myenv.yml","r") as f:
    print(f.read())
```

### <a name="create-a-configuration-file"></a>구성 파일 만들기

배포 구성 파일을 만듭니다. Container Instances 컨테이너에 필요한 CPU 수와 RAM 크기(기가바이트 단위)를 지정합니다. 모델에 따라 다르긴 하지만 대개 다수의 모델에서 기본적으로 1기가바이트의 RAM 및 1개 코어면 충분합니다. 나중에 더 필요한 경우 이미지를 다시 만들고 서비스를 다시 배포합니다.

```python
from azureml.core.webservice import AciWebservice

aciconfig = AciWebservice.deploy_configuration(cpu_cores=1, 
                                               memory_gb=1, 
                                               tags={"data": "MNIST",  "method" : "sklearn"}, 
                                               description='Predict MNIST with sklearn')
```

### <a name="deploy-in-container-instances"></a>Container Instances에 배포
배포가 완료되는 예상 시간은 **약 7 ~ 8분**입니다.

이미지를 구성하고 배포합니다. 다음 코드는 다음과 같은 단계를 거칩니다.

1. 다음 파일을 사용하여 이미지를 빌드합니다.
   * 점수 매기기 파일(`score.py`)
   * 환경 파일(`myenv.yml`)
   * 모델 파일
1. 작업 영역에서 해당 이미지를 등록합니다. 
1. 이미지를 Container Instances 컨테이너로 보냅니다.
1. 이미지를 사용하여 Container Instances에서 컨테이너를 시작합니다.
1. 웹 서비스 HTTP 엔드포인트를 가져옵니다.


```python
%%time
from azureml.core.webservice import Webservice
from azureml.core.image import ContainerImage

# configure the image
image_config = ContainerImage.image_configuration(execution_script="score.py", 
                                                  runtime="python", 
                                                  conda_file="myenv.yml")

service = Webservice.deploy_from_model(workspace=ws,
                                       name='sklearn-mnist-svc',
                                       deployment_config=aciconfig,
                                       models=[model],
                                       image_config=image_config)

service.wait_for_deployment(show_output=True)
```

REST 클라이언트 호출을 수락하는 채점 웹 서비스의 HTTP 엔드포인트를 가져옵니다. 이 엔드포인트는 웹 서비스를 테스트하거나 애플리케이션에 통합하려는 모든 사용자와 공유할 수 있습니다. 

```python
print(service.scoring_uri)
```


## <a name="test-the-deployed-service"></a>배포된 서비스 테스트

이전에 로컬 버전의 모델을 사용하여 모든 테스트 데이터에 대한 점수를 매겼습니다. 이제 테스트 데이터 중 무작위로 샘플링한 30개 이미지를 사용하여 배포된 모델을 테스트할 수 있습니다.  

다음 코드는 다음과 같은 단계를 거칩니다.
1. 데이터를 JSON 배열로 Container Instances에서 호스팅되는 웹 서비스에 보냅니다. 

1. SDK의 `run` API를 사용하여 서비스를 호출합니다. 또한 **curl**과 같은 HTTP 도구를 사용하여 원시 호출을 수행할 수도 있습니다.

1. 반환된 예측을 인쇄하고 입력 이미지와 함께 그립니다. 빨강 글꼴 및 역방향 이미지(검정 바탕에 흰색)는 오분류된 샘플을 강조 표시하는 데 사용됩니다. 

모델 정확도가 높으므로 오분류된 샘플을 보려면 다음 코드를 몇 번 실행해야 할 수 있습니다.

```python
import json

# find 30 random samples from test set
n = 30
sample_indices = np.random.permutation(X_test.shape[0])[0:n]

test_samples = json.dumps({"data": X_test[sample_indices].tolist()})
test_samples = bytes(test_samples, encoding = 'utf8')

# predict using the deployed model
result = json.loads(service.run(input_data=test_samples))

# compare actual value vs. the predicted values:
i = 0
plt.figure(figsize = (20, 1))

for s in sample_indices:
    plt.subplot(1, n, i + 1)
    plt.axhline('')
    plt.axvline('')
    
    # use different color for misclassified sample
    font_color = 'red' if y_test[s] != result[i] else 'black'
    clr_map = plt.cm.gray if y_test[s] != result[i] else plt.cm.Greys
    
    plt.text(x=10, y =-10, s=result[i], fontsize=18, color=font_color)
    plt.imshow(X_test[s].reshape(28, 28), cmap=clr_map)
    
    i = i + 1
plt.show()
```

테스트 이미지 중 임의의 한 샘플에 대한 결과는 다음과 같습니다.

![결과를 보여주는 그래픽](./media/tutorial-deploy-models-with-aml/results.png)

원시 HTTP 요청을 보내 웹 서비스를 테스트할 수도 있습니다.

```python
import requests
import json

# send a random row from the test set to score
random_index = np.random.randint(0, len(X_test)-1)
input_data = "{\"data\": [" + str(list(X_test[random_index])) + "]}"

headers = {'Content-Type':'application/json'}

# for AKS deployment you'd need to the service key in the header as well
# api_key = service.get_key()
# headers = {'Content-Type':'application/json',  'Authorization':('Bearer '+ api_key)} 

resp = requests.post(service.scoring_uri, input_data, headers=headers)

print("POST to url", service.scoring_uri)
#print("input data:", input_data)
print("label:", y_test[random_index])
print("prediction:", resp.text)
```

## <a name="clean-up-resources"></a>리소스 정리

다른 자습서 및 탐색을 위해 리소스 그룹과 작업 영역을 유지하려면 이 API 호출을 사용하여 Container Instances 배포만 삭제하면 됩니다.

```python
service.delete()
```

[!INCLUDE [aml-delete-resource-group](../../../includes/aml-delete-resource-group.md)]


## <a name="next-steps"></a>다음 단계

+ [Azure Machine Learning Service에 대한 배포 옵션](how-to-deploy-and-where.md)을 모두 알아봅니다. 옵션에는 Azure Container Instances, Azure Kubernetes Service, FPGA 및 Azure IoT Edge가 포함됩니다.

+ Azure Machine Learning Service에서 모델에 가장 적합한 알고리즘을 자동으로 선택하고, 조정하는 방법을 확인합니다. 또한 가장 적합한 모델을 빌드합니다. [자동 알고리즘 선택](tutorial-auto-train-models.md) 자습서를 사용해보세요. 
