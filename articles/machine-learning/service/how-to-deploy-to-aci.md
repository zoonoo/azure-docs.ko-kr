---
title: ACI(Azure Container Instances)에 웹 서비스 배포 - Azure Machine Learning
description: Azure Machine Learning 서비스를 사용하여 ACI(Azure Container Instances)에서 학습된 모델을 웹 서비스로 배포하는 방법을 알아봅니다. 이 문서에서는 ACI에 모델을 배포하는 세 가지 방법을 보여 줍니다. 이러한 세 방법은 배포의 이름 지정 부분에 제공되는 코드 줄 수와 사용자의 제어 권한이 다릅니다.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.author: raymondl
author: raymondlaghaeian
ms.reviewer: sgilley
ms.date: 09/24/2018
ms.openlocfilehash: 3c07f39a6c6c4ce244ba49a26617b3e645c57acb
ms.sourcegitcommit: a4e4e0236197544569a0a7e34c1c20d071774dd6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/15/2018
ms.locfileid: "51710378"
---
# <a name="deploy-web-services-to-azure-container-instances"></a>Azure Container Instances에 웹 서비스 배포 

학습된 모델을 ACI([Azure Container Instances](https://azure.microsoft.com/services/container-instances/)), AKS([Azure Kubernetes Service](https://azure.microsoft.com/services/kubernetes-service/)), IoT Edge 장치 또는 [FPGA(필드 프로그래밍 가능 게이트 배열)](concept-accelerate-with-fpgas.md)에서 웹 서비스로 배포할 수 있습니다. 

일반적으로 ACI는 AKS보다 저렴하며 4~6개의 코드 줄로 설정할 수 있습니다. ACI는 배포를 테스트하기 위한 완벽한 옵션입니다. 나중에 대규모 프로덕션 용도로 모델 및 웹 서비스를 사용할 준비가 되면 [AKS에 배포](how-to-deploy-to-aks.md)할 수 있습니다.

이 문서에서는 ACI에 모델을 배포하는 세 가지 방법을 보여 줍니다. 이러한 세 방법은 배포의 이름 지정 부분에 제공되는 코드 줄 수와 사용자의 제어 권한이 다릅니다. 다음은 최소 코드 수 및 제어 권한을 포함하는 메서드부터 최대 코드 수 및 제어 권한을 포함하는 메서드 순서로 ACI 옵션을 설명한 것입니다.

* `Webservice.deploy()`를 사용하여 모델 파일에서 배포 
* `Webservice.deploy_from_model()`을 사용하여 등록된 모델에서 배포
* `Webservice.deploy_from_image()`를 사용하여 이미지에서 등록된 모델 배포

Azure 구독이 아직 없는 경우 시작하기 전에 [무료 계정](https://aka.ms/AMLfree) 을 만듭니다.


## <a name="prerequisites"></a>필수 조건

- Azure Machine Learning 서비스 작업 영역 및 Python용 Azure Machine Learning SDK가 설치되어 있어야 합니다. [Azure Machine Learning 빠른 시작을 통한 시작](quickstart-get-started.md)을 사용하여 이러한 필수 구성 요소를 충족하는 방법을 알아봅니다.

- Azure Machine Learning 서비스 작업 영역 개체

    ```python
    from azureml.core import Workspace
    ws = Workspace.from_config()
    ```

- 배포할 모델입니다. 이 문서의 예제에서는 “[모델 학습](tutorial-train-models-with-aml.md)” 자습서에 따라 만든 모델을 사용합니다. 이 모델을 사용하지 않는 경우 사용하는 모델 이름을 참조하도록 단계를 수정합니다.  또한 사용자 모델을 실행하기 위해 사용자 고유의 채점 스크립트를 작성해야 합니다.


## <a name="configure-an-image"></a>이미지 구성

모든 모델 파일을 저장하는 데 사용되는 Docker 이미지를 구성합니다.
1. [다음 지침을 사용](tutorial-deploy-models-with-aml.md#create-scoring-script)하여 채점 스크립트(score.py)를 만듭니다.

    > [!IMPORTANT]
    > 채점 스크립트는 클라이언트에서 제출한 데이터를 받아서 채점을 위해 모델로 전달합니다. 스크립트 및 모델에 필요한 데이터 구조를 문서화합니다. 이러한 설명서가 있으면 웹 서비스를 사용하도록 클라이언트를 빌드하는 경우 쉽게 수행할 수 있습니다.

1. [다음 지침을 사용](tutorial-deploy-models-with-aml.md#create-environment-file)하여 환경 파일(myenv.yml)을 만듭니다.

1. 이러한 두 파일을 사용하여 다음과 같이 SDK에서 Python으로 Docker 이미지를 구성합니다.

    ```python
    from azureml.core.image import ContainerImage

    image_config = ContainerImage.image_configuration(execution_script = "score.py",
                                                      runtime = "python",
                                                      conda_file = "myenv.yml",
                                                      description = "Image with mnist model",
                                                      tags = {"data": "mnist", "type": "classification"}
                                                     )
    ```

## <a name="configure-the-aci-container"></a>ACI 컨테이너 구성

ACI 컨테이너에 필요한 CPU 개수 및 RAM 기가바이트를 지정하여 ACI 컨테이너를 구성합니다. 기본 설정인 코어 1개와 1GB RAM이면 대부분의 모델에서 충분합니다. 나중에 더 필요할 것 같다면, 이미지를 다시 만들고 서비스를 다시 배포합니다.  

```python
from azureml.core.webservice import AciWebservice

aciconfig = AciWebservice.deploy_configuration(cpu_cores = 1, 
                                               memory_gb = 1, 
                                               tags = {"data": "mnist", "type": "classification"},
                                               description = 'Handwriting recognition')
```

## <a name="register-a-model"></a>모델 등록

> [모델 파일(`Webservice.deploy()`)에서 배포](#deploy-from-model-file)하는 경우 이 필수 구성 요소를 건너뜁니다.

[Webservice.deploy_from_model](#deploy-from-registered-model) 또는 [Webservice.deploy_from_image](#deploy-from-image)를 사용하도록 모델을 등록합니다. 또는 이미 등록된 모델이 있는 경우 지금 검색하세요.

### <a name="retrieve-a-registered-model"></a>등록된 모델 검색
Azure Machine Learning을 사용하여 모델을 학습하는 경우 모델이 작업 영역에 이미 등록되어 있을 수 있습니다.  예를 들어 [모델 학습](tutorial-train-models-with-aml.md) 자습서의 마지막 단계에서 모델을 등록했을 것입니다.  그런 다음, 배포할 등록된 모델을 검색합니다.

```python
from azureml.core.model import Model

model_name = "sklearn_mnist"
model=Model(ws, model_name)
```
  
### <a name="register-a-model-file"></a>모델 파일 등록

모델이 다른 위치에서 빌드된 경우에도 작업 영역에 등록할 수 있습니다.  모델을 등록하려면 모델 파일(이 예제의 `sklearn_mnist_model.pkl`)이 현재 작업 디렉터리에 있어야 합니다. 그런 다음, 해당 파일을 `Model.register()`를 사용하여 작업 영역에 `sklearn_mnist`라는 모델로 등록합니다.
    
```python
from azureml.core.model import Model

model_name = "sklearn_mnist"
model = Model.register(model_path = "sklearn_mnist_model.pkl",
                        model_name = model_name,
                        tags = {"data": "mnist", "type": "classification"},
                        description = "Mnist handwriting recognition",
                        workspace = ws)
```

<a name='deploy-from-model-file'/>
## <a name="option-1-deploy-from-model-file"></a>옵션 1: 모델 파일에서 배포

모델 파일에서 배포하는 옵션의 경우 최소의 코드만 작성하면 되지만, 구성 요소 명명에 대한 제어 권한도 최소로만 유지됩니다. 이 옵션은 모델 파일로 시작하고, 해당 파일을 작업 영역에 등록합니다.  그러나 모델 이름을 지정하거나 태그 또는 설명을 연결할 수는 없습니다.  

이 옵션은 SDK 메서드 Webservice.deploy()를 사용합니다.  

**예상 시간**: 배포하는 데 약 6-7분이 걸립니다.

1. 모델 파일이 로컬 작업 디렉터리에 있는지 확인하세요.

1. 필수 구성 요소 모델 파일 score.py를 열고 `init()` 섹션을 다음으로 변경합니다.

    ```python
    def init():
        global model
        # retreive the local path to the model using the model name
        model_path = Model.get_model_path('sklearn_mnist_model.pkl')
        model = joblib.load(model_path)
    ```

1. 모델 파일을 배포합니다.

    ```python
    from azureml.core.webservice import Webservice
    
    service_name = 'aci-mnist-1'
    service = Webservice.deploy(deployment_config = aciconfig,
                                    image_config = image_config,
                                    model_paths = ['sklearn_mnist_model.pkl'],
                                    name = service_name,
                                    workspace = ws)
    
    service.wait_for_deployment(show_output = True)
    print(service.state)
    ```

1. 이제 [웹 서비스를 테스트](#test-web-service)할 수 있습니다.

<a name='deploy-from-registered-model'/>
## <a name="option-2-deploy-from-registered-model"></a>옵션 2: 등록된 모델에서 배포

등록된 모델 파일을 배포하는 옵션은 좀 더 많은 코드 줄을 사용하며 출력의 명명을 좀 더 제어할 수 있도록 합니다. 이 옵션은 이미 등록된 모델을 배포하는 편리한 방법입니다.  그러나 Docker 이미지 이름은 지정할 수 없습니다.  

이 옵션은 SDK 메서드 Webservice.deploy_from_model()을 사용합니다.

**예상 시간**: 이 옵션을 사용하여 배포하는 데 약 8분이 걸립니다.

1. 코드를 실행하여 Docker 컨테이너 및 ACI 컨테이너를 구성하고 등록된 모델을 지정합니다.

    ```python
    from azureml.core.webservice import Webservice

    service_name = 'aci-mnist-2'
    service = Webservice.deploy_from_model(deployment_config = aciconfig,
                                           image_config = image_config,
                                           models = [model], # this is the registered model object
                                           name = service_name,
                                           workspace = ws)
    service.wait_for_deployment(show_output = True)
    print(service.state)
    ```

1. 이제 [웹 서비스를 테스트](#test-web-service)할 수 있습니다.

<a name='deploy-from-image'/>
## <a name="option-3-deploy-from-image"></a>옵션 3: 이미지에서 배포

`Webservice.deploy_from_image()`를 사용하여 등록된 모델(`model`)을 배포합니다. 이 메서드를 사용하여 Docker 이미지를 별도로 만든 후 해당 이미지에서 배포할 수 있습니다.

1. `ContainerImage.create()`를 사용하여 작업 영역에서 Docker 이미지를 빌드하고 등록합니다.

    이 메서드를 사용하면 이미지를 별도의 단계에서 만들게 되므로 좀 더 강력하게 제어할 수 있습니다.  등록된 모델(`model`)이 이미지에 포함됩니다.
    
    ```python
    from azureml.core.image import ContainerImage
    
    image = ContainerImage.create(name = "myimage1",
                                  models = [model], # this is the registered model object
                                  image_config = image_config,
                                  workspace = ws)
    
    image.wait_for_creation(show_output = True)
    ```
**예상 시간**: 약 3분이 소요됩니다.

1. `Webservice.deploy_from_image()`을 사용하여 Docker 이미지를 서비스로 배포합니다.

    이제 이미지를 ACI에 배포합니다.  
    
    ```python
    from azureml.core.webservice import Webservice
    
    service_name = 'aci-mnist-3'
    service = Webservice.deploy_from_image(deployment_config = aciconfig,
                                                image = image,
                                                name = service_name,
                                                workspace = ws)
    service.wait_for_deployment(show_output = True)
    print(service.state)
    ```   
 
**예상 시간**: 약 3분이 소요됩니다.

이 메서드는 배포의 구성 요소 생성 및 명명을 사용자가 가장 강력히 제어할 수 있습니다.

이제 웹 서비스를 테스트할 수 있습니다.

## <a name="a-nametest-web-servicetest-the-web-service"></a><a name='test-web-service'/>웹 서비스 테스트

웹 서비스는 어떤 메서드가 사용되든 동일합니다.  예측을 얻으려면 서비스의 `run` 메서드를 사용합니다.  

```python
# Load Data
import os
import urllib

os.makedirs('./data', exist_ok = True)

urllib.request.urlretrieve('http://yann.lecun.com/exdb/mnist/t10k-images-idx3-ubyte.gz', filename = './data/test-images.gz')

from utils import load_data
X_test = load_data('./data/test-images.gz', False) / 255.0

from sklearn import datasets
import numpy as np
import json

# find 5 random samples from test set
n = 5
sample_indices = np.random.permutation(X_test.shape[0])[0:n]

test_samples = json.dumps({"data": X_test[sample_indices].tolist()})
test_samples = bytes(test_samples, encoding = 'utf8')

# predict using the deployed model
prediction = service.run(input_data = test_samples)
print(prediction)
```


## <a name="clean-up-resources"></a>리소스 정리

이 웹 서비스를 사용하지 않으려는 경우 비용이 발생하지 않도록 삭제하세요.

```python
service.delete()
```

## <a name="next-steps"></a>다음 단계

* [웹 서비스로 배포된 ML 모델을 사용](how-to-consume-web-service.md)하는 방법에 대해 알아봅니다.
* 대규모 배포를 위해 [Azure Kubernetes Service에 배포](how-to-deploy-to-aks.md)하는 방법을 알아봅니다. 
