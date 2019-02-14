---
title: 모델을 웹 서비스로 배포
titleSuffix: Azure Machine Learning service
description: 다음을 포함하는 Azure Machine Learning 서비스 모델을 배포하는 방법 및 위치를 알아봅니다. Azure Container Instances, Azure Kubernetes Service, Azure IoT Edge 및 Field-Programmable Gate Array
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: aashishb
author: aashishb
ms.reviewer: larryfr
ms.date: 12/07/2018
ms.custom: seodec18
ms.openlocfilehash: caafd5ac43ca94f8b01298b4e18e48065b7001b9
ms.sourcegitcommit: 415742227ba5c3b089f7909aa16e0d8d5418f7fd
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/06/2019
ms.locfileid: "55766625"
---
# <a name="deploy-models-with-the-azure-machine-learning-service"></a>Azure Machine Learning Services를 사용하여 모델 배포

SDK를 사용하여 Azure Machine Learning Services는 학습된 모델을 배포할 수 있는 몇 가지 방법을 제공합니다. 이 문서에서는 모델을 Azure 클라우드에서 또는 IoT Edge 디바이스에 웹 서비스로 배포하는 방법을 알아봅니다.

> [!IMPORTANT]
> 모델을 웹 서비스로서 배포할 때 현재, CORS(크로스-원본 자원 공유)는 지원되지 않습니다.

모델은 다음과 같은 계산 대상에 배포할 수 있습니다.

| 계산 대상 | 배포 유형 | 설명 |
| ----- | ----- | ----- |
| [ACI(Azure Container Instances)](#aci) | 웹 서비스 | 빠르게 배포합니다. 개발 또는 테스트에 적합합니다. |
| [AKS(Azure Kubernetes Service)](#aks) | 웹 서비스 | 확장성이 뛰어난 프로덕션 배포에 적합합니다. 자동 크기 조정 및 빠른 응답 시간을 제공합니다. |
| [Azure IoT Edge](#iotedge) | IoT 모듈 | IoT 디바이스에서 모델을 배포합니다. 디바이스에서 추론이 발생합니다. |
| [FPGA(Field-programmable Gate Array)](#fpga) | 웹 서비스 | 실시간 추론 시 대기 시간이 매우 짧습니다. |

모델을 배포하는 프로세스는 모든 컴퓨팅 대상에서 유사합니다.

1. 모델을 학습하고 등록합니다.
1. 모델을 사용하는 이미지를 구성하고 등록합니다.
1. 컴퓨팅 대상에 이미지를 배포합니다.
1. 배포 테스트

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE2Kwk3]


배포 워크플로에 관련된 개념에 대한 자세한 내용은 [Azure Machine Learning Service를 사용하여 모델 관리, 배포 및 모니터링](concept-model-management-and-deployment.md)을 참조하세요.

## <a name="prerequisites"></a>필수 조건

- Azure 구독. Azure 구독이 없는 경우 시작하기 전에 체험 계정을 만듭니다. [Azure Machine Learning Service의 평가판 또는 유료 버전](http://aka.ms/AMLFree)을 지금 사용해 보세요.

- Azure Machine Learning 서비스 작업 영역 및 Python용 Azure Machine Learning SDK가 설치되어 있어야 합니다. [Azure Machine Learning 빠른 시작을 통한 시작](quickstart-get-started.md)을 사용하여 이러한 필수 구성 요소를 충족하는 방법을 알아봅니다.

- 학습된 모델. 학습된 모델이 없는 경우 [모델 학습](tutorial-train-models-with-aml.md) 자습서의 단계를 사용하여 학습을 하고 Azure Machine Learning 서비스에 등록합니다.

    > [!NOTE]
    > Azure Machine Learning 서비스는 Python 3에 로드할 수 있는 모든 일반 모델에 작동할 수 있지만 이 문서의 예제에서는 pickle 형식으로 저장된 모델을 사용합니다.
    > 
    > ONNX 모델을 사용하는 방법에 대한 자세한 내용은 [ONNX 및 Azure Machine Learning](how-to-build-deploy-onnx.md) 문서를 참조하세요.

## <a id="registermodel"></a> 학습된 모델 등록

모델 레지스트리는 학습된 모델을 Azure 클라우드에서 저장 및 구성하는 방법입니다. 모델은 Azure Machine Learning 서비스 작업 영역에 등록됩니다. Azure Machine Learning 또는 다른 서비스를 사용하여 모델을 학습할 수 있습니다. 파일에서 모델을 등록하려면 다음 코드를 사용합니다.

```python
from azureml.core.model import Model

model = Model.register(model_path = "model.pkl",
                       model_name = "Mymodel",
                       tags = {"key": "0.1"},
                       description = "test",
                       workspace = ws)
```

**예상 시간**: 약 10초

자세한 내용은 [Model 클래스](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py)에 대한 참조 설명서를 참조하세요.

## <a id="configureimage"></a> 이미지 만들기 및 등록

배포된 모델은 이미지로 패키지됩니다. 이미지는 모델을 실행하는 데 필요한 종속성을 포함합니다.

**Azure Container Instance** **Azure Kubernetes Service** 및 **Azure IoT Edge** 배포의 경우 [azureml.core.image.ContainerImage](https://docs.microsoft.com/python/api/azureml-core/azureml.core.image.containerimage?view=azure-ml-py) 클래스를 사용하여 이미지 구성을 만듭니다. 이 이미지 구성은 새 Docker 이미지를 만드는 데 사용됩니다. 

다음 코드는 새 이미지 구성을 만드는 방법을 보여 줍니다.

```python
from azureml.core.image import ContainerImage

# Image configuration
image_config = ContainerImage.image_configuration(execution_script = "score.py",
                                                 runtime = "python",
                                                 conda_file = "myenv.yml",
                                                 description = "Image with ridge regression model",
                                                 tags = {"data": "diabetes", "type": "regression"}
                                                 )
```

**예상 시간**: 약 10초

이 예제의 중요 매개 변수를 다음 표에서 설명합니다.

| 매개 변수 | 설명 |
| ----- | ----- |
| `execution_script` | 서비스에 제출되는 요청을 수신하는 데 사용되는 Python 스크립트를 지정합니다. 이 예제에서 스크립트는 `score.py` 파일에 포함됩니다. 자세한 내용은 [실행 스크립트](#script) 섹션을 참조하세요. |
| `runtime` | 이미지는 Python을 사용함을 나타냅니다. 다른 옵션은 `spark-py`로, Apache Spark와 함께 Python을 사용합니다. |
| `conda_file` | conda 환경 파일을 제공하는 데 사용됩니다. 이 파일은 배포된 모델에 대한 conda 환경을 정의합니다. 이 파일을 만드는 방법에 대한 자세한 내용은 [환경 파일(myenv.yml) 만들기](tutorial-deploy-models-with-aml.md#create-environment-file)를 참조하세요. |

자세한 내용은 [ContainerImage 클래스](https://docs.microsoft.com/python/api/azureml-core/azureml.core.image.containerimage?view=azure-ml-py)에 대한 참조 설명서를 참조하세요.

### <a id="script"></a> 실행 스크립트

실행 스크립트는 배포된 이미지에 제출된 데이터를 수신한 후 모델에 전달합니다. 그런 후 모델이 반환한 응답을 수락한 후 클라이언트에 반환합니다. 이 스크립트는 모델마다 다르므로, 모델이 예상하고 반환하는 데이터를 이해해야 합니다. 스크립트는 일반적으로 모델을 로드하고 실행하는 2가지 함수를 포함합니다.

* `init()`: 일반적으로 이 함수는 모델을 전역 개체에 로드합니다. 이 함수는 Docker 컨테이너를 시작할 때 한 번만 실행됩니다. 

* `run(input_data)`: 이 함수는 모델을 사용하여 입력 데이터를 기반으로 값을 예측합니다. 실행에 대한 입력 및 출력은 일반적으로 serialization 및 deserialization용으로 JSON을 사용합니다. 원시 이진 데이터를 사용할 수도 있습니다. 모델에 보내기 전에 또는 클라이언트에 반환하기 전에 데이터를 변환할 수 있습니다. 

#### <a name="working-with-json-data"></a>JSON 데이터 작업

다음은 JSON 데이터를 수락하고 반환하는 예제 스크립트입니다. `run` 함수는 데이터를 모델이 예상하는 JSON으로 변환하고, 응답을 JSON으로 변환한 후 반환합니다.

```python
# import things required by this script
import json
import numpy as np
import os
import pickle
from sklearn.externals import joblib
from sklearn.linear_model import LogisticRegression

from azureml.core.model import Model

# load the model
def init():
    global model
    # retrieve the path to the model file using the model name
    model_path = Model.get_model_path('sklearn_mnist')
    model = joblib.load(model_path)

# Passes data to the model and returns the prediction
def run(raw_data):
    data = np.array(json.loads(raw_data)['data'])
    # make prediction
    y_hat = model.predict(data)
    return json.dumps(y_hat.tolist())
```

#### <a name="working-with-binary-data"></a>이진 데이터 작업

모델이 __이진 데이터__를 수락하는 경우 `AMLRequest`, `AMLResponse` 및 `rawhttp`를 사용합니다. 다음은 이진 데이터를 허용하고 POST 요청에 대해 반전된 바이트를 반환하는 예제 스크립트입니다. GET 요청의 경우 응답 본문의 전체 URL을 반환합니다.

```python
from azureml.contrib.services.aml_request  import AMLRequest, rawhttp
from azureml.contrib.services.aml_response import AMLResponse

def init():
    print("This is init()")

# Accept and return binary data
@rawhttp
def run(request):
    print("This is run()")
    print("Request: [{0}]".format(request))
    # handle GET requests
    if request.method == 'GET':
        respBody = str.encode(request.full_path)
        return AMLResponse(respBody, 200)
    # handle POST requests
    elif request.method == 'POST':
        reqBody = request.get_data(False)
        respBody = bytearray(reqBody)
        respBody.reverse()
        respBody = bytes(respBody)
        return AMLResponse(respBody, 200)
    else:
        return AMLResponse("bad request", 500)
```

> [!IMPORTANT]
> 서비스를 개선을 위해 노력하므로 `azureml.contrib` 네임스페이스는 자주 변경됩니다. 따라서 이 네임 스페이스의 모든 것을 미리 보기로 간주하므로 Microsoft에서 완벽히 지원하지 않아도 됩니다.
>
> 로컬 개발 환경에서 이를 테스트해야 하는 경우 다음 명령을 사용하여 `contrib` 네임스페이스에 구성 요소를 설치할 수 있습니다. 
> ```shell
> pip install azureml-contrib-services
> ```

### <a id="createimage"></a> 이미지 등록

일단 이미지 구성을 만들면 이미지를 등록하는 데 사용할 수 있습니다. 이 이미지는 작업 영역에 대한 컨테이너 레지스트리에 저장됩니다. 이미지는 한 번만 만든 후 여러 서버에 배포할 수 있습니다.

```python
# Register the image from the image configuration
image = ContainerImage.create(name = "myimage", 
                              models = [model], #this is the model object
                              image_config = image_config,
                              workspace = ws
                              )
```

**예상 시간**: 약 3분입니다.

동일한 이름으로 여러 이미지를 등록할 때 이미지에 자동으로 버전이 지정됩니다. 예를 들어 `myimage`로 등록된 첫 번째 이미지에는 ID `myimage:1`이 할당됩니다. 다음 번에 이미지를 `myimage`로 등록하면 새 이미지의 ID는 `myimage:2`가 됩니다.

자세한 내용은 [ContainerImage 클래스](https://docs.microsoft.com/python/api/azureml-core/azureml.core.image.containerimage?view=azure-ml-py)에 대한 참조 설명서를 참조하세요.

## <a id="deploy"></a> 이미지 배포

배포를 가져오는 프로세스는 배포하는 컴퓨팅 대상에 따라 약간 다릅니다. 다음 섹션의 정보를 참조하여 배포 방법을 알아보세요.

* [Azure Container Instances](#aci)
* [Azure Kubernetes Services](#aks)
* [Project Brainwave(Field-programmable Gate Array)](#fpga)
* [Azure IoT Edge 디바이스](#iotedge)

> [!NOTE]
> **웹 서비스로 배포**할 경우 사용할 수 있는 세 가지 배포 메서드는 다음과 같습니다.
>
> | 방법 | 메모 |
> | ----- | ----- |
> | [deploy_from_image](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice(class)?view=azure-ml-py#deploy-from-image-workspace--name--image--deployment-config-none--deployment-target-none-) | 이 메서드를 사용하기 전에 모델을 등록하고 이미지를 만들어야 합니다. |
> | [deploy](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice(class)?view=azure-ml-py#deploy-workspace--name--model-paths--image-config--deployment-config-none--deployment-target-none-) | 이 메서드를 사용하면 모델을 등록하거나 이미지를 만들 필요가 없습니다. 그러나 모델 또는 이미지 이름, 연결된 태그 및 설명을 을 제어할 수 없습니다. |
> | [deploy_from_model](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice(class)?view=azure-ml-py#deploy-from-model-workspace--name--models--image-config--deployment-config-none--deployment-target-none-) | 이 메서드를 사용하면 이미지를 만들 필요가 없습니다. 하지만 만드는 이미지의 이름을 제어할 수 없습니다. |
>
> 이 문서의 예제에서는 `deploy_from_image`를 사용합니다.

### <a id="aci"></a> Azure Container Instances에 배포

다음 조건 중 하나 이상에 해당하는 경우 Azure Container Instances를 사용하여 모델을 웹 서비스로 배포합니다.

- 모델을 빠르게 배포하고 유효성을 검사해야 합니다. ACI 배포는 5분 이내에 완료됩니다.
- 개발 중인 모델을 테스트합니다. ACI에 대한 할당량 및 지역 가용성을 확인하려면 [Azure Container Instances에 대한 할당량 및 지역 가용성](https://docs.microsoft.com/azure/container-instances/container-instances-quotas) 문서를 참조하세요.

Azure Container Instances에 배포하려면 다음 단계를 사용합니다.

1. 배포 구성을 정의합니다. 다음 예제에서는 하나의 CPU 코어 및 1GB의 메모리를 사용하는 구성을 정의합니다.

    [!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-deploy-to-aci/how-to-deploy-to-aci.py?name=configAci)]

2. 이 문서의 [이미지 만들기](#createimage) 섹션에서 만든 이미지를 배포하려면 다음 코드를 사용합니다.

    [!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-deploy-to-aci/how-to-deploy-to-aci.py?name=option3Deploy)]

    **예상 시간**: 약 3분입니다.

자세한 내용은 [AciWebservice](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aciwebservice?view=azure-ml-py) 및 [Webservice](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.webservice?view=azure-ml-py) 클래스에 대한 참조 설명서를 참조하세요.

### <a id="aks"></a> Azure Kubernetes Service에 배포

모델을 확장성이 뛰어난 프로덕션 웹 서비스로 배포하려면 AKS(Azure Kubernetes Service)를 사용합니다. 기존 AKS 클러스터를 사용하거나 Azure Machine Learning SDK, CLI 또는 Azure Portal을 사용하여 새로운 클러스터를 만들 수 있습니다.

AKS 클러스터 만들기는 작업 영역에 대한 일회성 프로세스입니다. 이 클러스터를 여러 배포에 재사용할 수 있습니다. 클러스터를 삭제하면, 다음에 배포할 때 새 클러스터를 만들어야 합니다.

Azure Kubernetes Service는 다음과 같은 기능을 제공합니다.

* 자동 확장
* 로깅
* 모델 데이터 수집
* 웹 서비스에 대한 빠른 응답 시간

#### <a name="create-a-new-cluster"></a>새 클러스터 만들기

새 Azure Kubernetes Service 클러스터를 만들려면 다음 코드를 사용합니다.

> [!IMPORTANT]
> AKS 클러스터 만들기는 작업 영역에 대한 하나의 프로세스입니다. 만든 후에는 여러 배포에 대해 이 클러스터를 재사용할 수 있습니다. 클러스터 또는 클러스터가 포함된 리소스 그룹을 삭제하면, 다음에 배포할 때 새 클러스터를 만들어야 합니다.
> [`provisioning_configuration()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.akscompute?view=azure-ml-py)의 경우, agent_count 및 vm_size에 대한 사용자 지정 값을 선택하는 경우 agent_count와 vm_size를 곱한 값이 12개 가상 CPU보다 크거나 같아야 합니다. 예를 들어, 4개의 가상 CPU가 있는 "Standard_D3_v2"의 vm_size를 사용하는 경우는 3 이상의 agent_count를 선택해야 합니다.

```python
from azureml.core.compute import AksCompute, ComputeTarget

# Use the default configuration (you can also provide parameters to customize this)
prov_config = AksCompute.provisioning_configuration()

aks_name = 'aml-aks-1' 
# Create the cluster
aks_target = ComputeTarget.create(workspace = ws, 
                                    name = aks_name, 
                                    provisioning_configuration = prov_config)

# Wait for the create process to complete
aks_target.wait_for_completion(show_output = True)
print(aks_target.provisioning_state)
print(aks_target.provisioning_errors)
```

**예상 시간**: 약 20분입니다.

#### <a name="use-an-existing-cluster"></a>기존 클러스터 사용

AKS 클러스터가 Azure 구독에 이미 있으며 해당 버전이 1.11.*인 경우 이미지를 배포하는 데 사용할 수 있습니다. 다음 코드는 작업 영역에 기존 클러스터를 연결하는 방법을 설명합니다.

```python
from azureml.core.compute import AksCompute, ComputeTarget
# Set the resource group that contains the AKS cluster and the cluster name
resource_group = 'myresourcegroup'
cluster_name = 'mycluster'

# Attach the cluster to your workgroup
attach_config = AksCompute.attach_configuration(resource_group = resource_group,
                                         cluster_name = cluster_name)
aks_target = ComputeTarget.attach(ws, 'mycompute', attach_config)

# Wait for the operation to complete
aks_target.wait_for_completion(True)
```

**예상 시간**: 약 3분입니다.

#### <a name="deploy-the-image"></a>이미지 배포

이 문서의 [이미지 만들기](#createimage) 섹션에서 만든 이미지를 Azure Kubernetes Server 클러스터에 배포하려면 다음 코드를 사용합니다.

```python
from azureml.core.webservice import Webservice, AksWebservice

# Set configuration and service name
aks_config = AksWebservice.deploy_configuration()
aks_service_name ='aks-service-1'
# Deploy from image
service = Webservice.deploy_from_image(workspace = ws, 
                                            name = aks_service_name,
                                            image = image,
                                            deployment_config = aks_config,
                                            deployment_target = aks_target)
# Wait for the deployment to complete
service.wait_for_deployment(show_output = True)
print(service.state)
```

**예상 시간**: 약 3분입니다.

자세한 내용은 [AksWebservice](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.akswebservice?view=azure-ml-py) 및 [Webservice](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.webservice.webservice?view=azure-ml-py) 클래스에 대한 참조 설명서를 참조하세요.

### <a id="fpga"></a> FPGA(Field-programmable Gate Array)에서 배포

Project Brainwave를 사용하여 실시간 추론 요청에 대해 매우 짧은 대기 시간을 얻을 수 있습니다. Project Brainwave는 Azure 클라우드의 FPGA(Field-programmable gate arrays)에 배포된 DNN(심층 신경망)을 가속화합니다. 일반적으로 사용되는 DNN은 전달 학습을 위한 보조 기능으로 사용하거나 자신의 데이터로 학습된 가중치로 사용자 지정할 수 있습니다.

Project Brainwave를 사용하여 모델을 배포하는 방법에 대한 연습 과정은 [FPGA에 배포](how-to-deploy-fpga-web-service.md) 문서를 참조하세요.

### <a id="iotedge"></a> Azure IoT Edge에 배포

Azure IoT Edge 디바이스는 Azure IoT Edge 런타임을 실행하는 Linux 또는 Windows 기반 디바이스입니다. 이러한 디바이스에 Machine Learning 모델을 IoT Edge 모듈로 배포할 수 있습니다. IoT Edge 디바이스에 모델을 배포하면 처리를 위해 클라우드로 데이터를 전송하는 대신 디바이스에서 직접 모델을 사용할 수 있습니다. 응답 시간은 더 빨라지고 데이터 전송은 더 적어집니다.

Azure IoT Edge 모듈은 컨테이너 레지스트리에서 디바이스에 배포됩니다. 모델에서 이미지를 만드는 경우 작업 영역에 대한 컨테이너 레지스트리에 저장됩니다.

#### <a name="set-up-your-environment"></a>환경 설정

* 개발 환경. 자세한 내용은 [개발 환경을 구성하는 방법](how-to-configure-environment.md) 문서를 참조하세요.

* Azure 구독의 [Azure IoT Hub](../../iot-hub/iot-hub-create-through-portal.md). 

* 학습된 모델. 모델 학습에 관한 예제는 [Azure Machine Learning을 사용하여 이미지 분류 모델 학습](tutorial-train-models-with-aml.md) 문서를 참조하세요. 미리 학습된 모델은 [Azure IoT Edge GitHub 리포지토리에 대한 AI 도구 키트](https://github.com/Azure/ai-toolkit-iot-edge/tree/master/IoT%20Edge%20anomaly%20detection%20tutorial)에서 사용할 수 있습니다.

#### <a name="prepare-the-iot-device"></a>IoT 디바이스 준비
[이 스크립트](https://raw.githubusercontent.com/Azure/ai-toolkit-iot-edge/master/amliotedge/createNregister)를 사용하여 IoT Hub를 만들고 디바이스를 등록하거나 기존 디바이스를 다시 사용해야 합니다.

``` bash
ssh <yourusername>@<yourdeviceip>
sudo wget https://raw.githubusercontent.com/Azure/ai-toolkit-iot-edge/master/amliotedge/createNregister
sudo chmod +x createNregister
sudo ./createNregister <The Azure subscriptionID you want to use> <Resourcegroup to use or create for the IoT hub> <Azure location to use e.g. eastus2> <the Hub ID you want to use or create> <the device ID you want to create>
```

결과 연결 문자열을 "cs":"{copy this string}" 다음에 저장합니다.

[이 스크립트](https://raw.githubusercontent.com/Azure/ai-toolkit-iot-edge/master/amliotedge/installIoTEdge)를 UbuntuX64 IoT Edge 노드 또는 DSVM에 다운로드하여 디바이스를 초기화합니다.

```bash
ssh <yourusername>@<yourdeviceip>
sudo wget https://raw.githubusercontent.com/Azure/ai-toolkit-iot-edge/master/amliotedge/installIoTEdge
sudo chmod +x installIoTEdge
sudo ./installIoTEdge
```

IoT Edge 노드가 IoT Hub의 연결 문자열을 수신할 준비가 되었습니다. ```device_connection_string:``` 줄을 찾고 위의 연결 문자열을 붙여넣은 후 큰따옴표로 묶습니다.

[빠른 시작: Linux x64 디바이스에 첫 번째 IoT Edge 모듈 배포](../../iot-edge/quickstart-linux.md) 문서에 따라 디바이스를 등록하고 IoT 런타임을 단계별로 설치하는 방법을 알아볼 수 있습니다.


#### <a name="get-the-container-registry-credentials"></a>컨테이너 레지스트리 자격 증명 가져오기
디바이스에 IoT Edge 모듈을 배포하려면 Azure IoT에 Azure Machine Learning 서비스가 Docker 이미지를 저장하는 컨테이너 레지스트리에 대한 자격 증명이 필요합니다.

다음 두 가지 방법으로 필요한 컨테이너 레지스트리 자격 증명을 쉽게 검색할 수 있습니다.

+ **Azure Portal에서**:

  1. [Azure Portal](https://portal.azure.com/signin/index)에 로그인합니다.

  1. Azure Machine Learning 서비스 작업 영역으로 이동하고 __개요__를 선택합니다. 컨테이너 레지스트리 설정으로 이동하려면 __레지스트리__ 링크를 선택합니다.

     ![컨테이너 레지스트리 항목의 이미지](./media/how-to-deploy-and-where/findregisteredcontainer.png)

  1. 컨테이너 레지스트리에서 **액세스 키**를 선택한 다음, 관리 사용자를 활성화합니다.
 
     ![액세스 키 화면의 이미지](./media/how-to-deploy-and-where/findaccesskey.png)

  1. **로그인 서버**, **사용자 이름** 및 **암호**의 값을 저장합니다. 

+ **Python 스크립트 사용**:

  1. 위에서 컨테이너를 만들기 위해 실행한 코드 이후에 다음 Python 스크립트를 사용합니다.

     ```python
     # Getting your container details
     container_reg = ws.get_details()["containerRegistry"]
     reg_name=container_reg.split("/")[-1]
     container_url = "\"" + image.image_location + "\","
     subscription_id = ws.subscription_id
     from azure.mgmt.containerregistry import ContainerRegistryManagementClient
     from azure.mgmt import containerregistry
     client = ContainerRegistryManagementClient(ws._auth,subscription_id)
     result= client.registries.list_credentials(resource_group_name, reg_name, custom_headers=None, raw=False)
     username = result.username
     password = result.passwords[0].value
     print('ContainerURL{}'.format(image.image_location))
     print('Servername: {}'.format(reg_name))
     print('Username: {}'.format(username))
     print('Password: {}'.format(password))
     ```
  1. ContainerURL, servername, username 및 password 값을 저장합니다. 

     이러한 자격 증명은 개인 컨테이너 레지스트리에 있는 이미지에 대한 IoT Edge 디바이스 액세스를 제공하는 데 필요합니다.

#### <a name="deploy-the-model-to-the-device"></a>디바이스에 모델 배포

[이 스크립트](https://raw.githubusercontent.com/Azure/ai-toolkit-iot-edge/master/amliotedge/deploymodel)를 실행하고 위 단계의 컨테이너 레지스트리 이름, 사용자 이름, 암호, 이미지 위치 URL, 원하는 배포 이름, IoT Hub 이름 및 만든 디바이스 ID를 제공하여 모델을 쉽게 배포할 수 있습니다. 다음 단계에 따라 VM에서 이 작업을 수행할 수 있습니다. 

```bash 
wget https://raw.githubusercontent.com/Azure/ai-toolkit-iot-edge/master/amliotedge/deploymodel
sudo chmod +x deploymodel
sudo ./deploymodel <ContainerRegistryName> <username> <password> <imageLocationURL> <DeploymentID> <IoTHubname> <DeviceID>
```

또는 [Azure Portal에서 Azure IoT Edge 모듈 배포](../../iot-edge/how-to-deploy-modules-portal.md) 문서의 단계에 따라 디바이스에 이미지를 배포할 수 있습니다. 디바이스의 __레지스트리 설정__을 구성하는 경우 작업 영역 컨테이너 레지스트리에 대해 __login server__, __username__ 및 __password__를 사용합니다.

> [!NOTE]
> Azure IoT에 익숙하지 않은 경우 다음 문서에서 서비스 시작 정보를 참조하세요:
>
> * [빠른 시작: Linux 디바이스에 첫 번째 IoT Edge 모듈 배포](../../iot-edge/quickstart-linux.md)
> * [빠른 시작: Windows 디바이스에 첫 번째 IoT Edge 모듈 배포](../../iot-edge/quickstart.md)


## <a name="testing-web-service-deployments"></a>웹 서비스 배포 테스트

웹 서비스 배포를 테스트하려면 Webservice 개체의 `run` 메서드를 사용할 수 있습니다. 다음 예제에서는 JSON 문서가 웹 서비스로 설정되고 결과가 표시됩니다. 전송되는 데이터는 모델에 필요한 데이터와 일치해야 합니다. 이 예제에서 데이터 형식은 diabetes 모델에 필요한 입력과 일치합니다.

```python
import json

test_sample = json.dumps({'data': [
    [1,2,3,4,5,6,7,8,9,10], 
    [10,9,8,7,6,5,4,3,2,1]
]})
test_sample = bytes(test_sample,encoding = 'utf8')

prediction = service.run(input_data = test_sample)
print(prediction)
```

웹 서비스는 REST API이므로 다양한 프로그래밍 언어로 클라이언트 애플리케이션을 만들 수 있습니다. 자세한 내용은 [웹 서비스를 사용할 클라이언트 애플리케이션 만들기](how-to-consume-web-service.md)를 참조하세요.

## <a id="update"></a> 웹 서비스 업데이트

새 이미지를 만들 때 새 이미지를 사용하게 하려는 각 서비스를 수동으로 업데이트해야 합니다. 웹 서비스를 업데이트하려면 `update` 메서드를 사용합니다. 다음 코드에서는 새 이미지를 사용하도록 웹 서비스를 업데이트하는 방법을 보여줍니다.

```python
from azureml.core.webservice import Webservice
from azureml.core.image import Image

service_name = 'aci-mnist-3'
# Retrieve existing service
service = Webservice(name = service_name, workspace = ws)

# point to a different image
new_image = Image(workspace = ws, id="myimage2:1")

# Update the image used by the service
service.update(image = new_image)
print(service.state)
```

자세한 내용은 [Webservice](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice(class)?view=azure-ml-py) 클래스의 참조 설명서를 참조하세요.

## <a name="clean-up"></a>정리

배포된 웹 서비스를 삭제하려면 `service.delete()`를 사용합니다.

이미지를 삭제하려면 `image.delete()`를 사용합니다.

등록된 모델을 삭제하려면 `model.delete()`를 사용합니다.

자세한 내용은 [WebService.delete()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice(class)?view=azure-ml-py#delete--), [Image.delete()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.image.image(class)?view=azure-ml-py#delete--) 및 [Model.delete()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#delete--)의 참조 설명서를 참조하세요.

## <a name="troubleshooting"></a>문제 해결

* __배포하는 동안 오류가 발생하면__ `service.get_logs()`를 사용하여 서비스 로그를 확인합니다. 로깅된 정보에 오류 원인이 나타날 수 있습니다.

* 로그에 __로깅 수준을 디버그로 설정__하라는 오류가 포함될 수 있습니다 로깅 수준을 설정하려면 채점 스크립트에 다음 줄을 추가하고, 이미지를 만들고, 다음 이미지를 사용하여 서비스를 만듭니다.

    ```python
    import logging
    logging.basicConfig(level=logging.DEBUG)
    ```

    이렇게 변경하면 추가 로깅이 가능하며, 오류 발생 원인에 대한 더 많은 정보가 반환될 수 있습니다.

## <a name="next-steps"></a>다음 단계

* [SSL을 사용하여 Azure Machine Learning 웹 서비스 보호](how-to-secure-web-service.md)
* [웹 서비스로 배포된 ML 모델 사용](how-to-consume-web-service.md)
* [일괄 처리 예측 실행 방법](how-to-run-batch-predictions.md)
* [Application Insights를 사용하여 Azure Machine Learning 모델 모니터링](how-to-enable-app-insights.md)
* [프로덕션 환경에서 모델용 데이터 수집](how-to-enable-data-collection.md)
* [Azure Machine Learning Service SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)
* [Azure Virtual Networks에서 Azure Machine Learning Service 사용](how-to-enable-virtual-network.md)
* [추천 시스템 빌드 모범 사례](https://github.com/Microsoft/Recommenders)
* [Azure에서 실시간 추천 API 빌드](https://docs.microsoft.com/azure/architecture/reference-architectures/ai/real-time-recommendation)
