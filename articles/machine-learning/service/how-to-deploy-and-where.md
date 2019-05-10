---
title: 방법 및 모델을 배포할 수 있는 위치
titleSuffix: Azure Machine Learning service
description: 다음을 포함하는 Azure Machine Learning 서비스 모델을 배포하는 방법 및 위치를 알아봅니다. Azure Container Instances, Azure Kubernetes Service, Azure IoT Edge 및 Field-Programmable Gate Array
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: jordane
author: jpe316
ms.reviewer: larryfr
ms.date: 05/02/2019
ms.custom: seoapril2019
ms.openlocfilehash: 45421a249642abf37c89aa33e2e8a1b4a9e5e497
ms.sourcegitcommit: 8fc5f676285020379304e3869f01de0653e39466
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/09/2019
ms.locfileid: "65506999"
---
# <a name="deploy-models-with-the-azure-machine-learning-service"></a>Azure Machine Learning Services를 사용하여 모델 배포

기계 학습 모델을 웹 서비스로 Azure 클라우드에서 또는 IoT Edge 장치에 배포 하는 방법에 알아봅니다. 이 문서에서는 다음 계산 대상에 배포 하는 방법에 설명 합니다.

| 계산 대상 | 배포 유형 | 설명 |
| ----- | ----- | ----- |
| [로컬 웹 서비스](#local) | 테스트/디버그 | 제한 된 테스트 및 문제 해결에 적합 합니다.
| [AKS(Azure Kubernetes Service)](#aks) | 실시간 유추 | 확장성이 뛰어난 프로덕션 배포에 적합합니다. 자동 크기 조정 및 빠른 응답 시간을 제공합니다. |
| [ACI(Azure Container Instances)](#aci) | 테스트하는 중 | 낮은 등급, CPU 기반 워크 로드에 적합 합니다. |
| [Azure Machine Learning 컴퓨팅](how-to-run-batch-predictions.md) | (미리 보기) 일괄 처리 유추 | 서버 리스 계산에서 점수 매기기 일괄 처리를 실행 합니다. 일반 및 낮은 우선 순위 Vm 지원합니다. |
| [Azure IoT Edge](#iotedge) | (미리 보기) IoT 모듈 | 배포 및 IoT 장치에서 기계 학습 모델을 제공 합니다. |

## <a name="deployment-workflow"></a>배포 워크플로

모델을 배포하는 프로세스는 모든 컴퓨팅 대상에서 유사합니다.

1. 모델을 등록 합니다.
1. 모델을 배포 합니다.
1. 테스트 모델을 배포 합니다.

배포 워크플로에 관련된 개념에 대한 자세한 내용은 [Azure Machine Learning Service를 사용하여 모델 관리, 배포 및 모니터링](concept-model-management-and-deployment.md)을 참조하세요.

## <a name="prerequisites-for-deployment"></a>배포 필수 구성 요소

- 모델. 학습된 된 모델이 없는, 모델을 사용할 수 있습니다 & 종속성 파일에서 제공 [이 자습서](https://aka.ms/azml-deploy-cloud)합니다.

- 합니다 [Machine Learning 서비스에 대 한 Azure CLI extension](reference-azure-machine-learning-cli.md), 또는 [Azure Machine Learning Python SDK](https://aka.ms/aml-sdk)합니다.

## <a id="registermodel"></a> 기계 학습 모델을 등록 합니다.

모델 레지스트리는 학습된 모델을 Azure 클라우드에서 저장 및 구성하는 방법입니다. 모델은 Azure Machine Learning 서비스 작업 영역에 등록됩니다. Azure Machine Learning을 사용 하 여 또는 다른 곳에서 학습 된 모델에서 가져온 모델을 학습 될 수 있습니다. 다음 예제에서는 파일에서 모델을 등록 하는 방법을 보여 줍니다.

### <a name="register-a-model-from-an-experiment-run"></a>실험을 실행 하는 모델을 등록

**CLI 사용 하 여 Scikit-learn 예제**
```azurecli-interactive
az ml model register -n sklearn_mnist  --asset-path outputs/sklearn_mnist_model.pkl  --experiment-name myexperiment
```
**SDK를 사용 하 여**
```python
model = run.register_model(model_name='sklearn_mnist', model_path='outputs/sklearn_mnist_model.pkl')
print(model.name, model.id, model.version, sep='\t')
```

### <a name="register-an-externally-created-model"></a>외부에서 만든된 모델을 등록 합니다.
외부에서 만든된 모델을 제공 하 여 등록할 수 있습니다는 **로컬 경로** 모델입니다. 폴더 또는 단일 파일을 제공할 수 있습니다.

**Python SDK를 사용 하 여 ONNX 예제:**
```python
onnx_model_url = "https://www.cntk.ai/OnnxModels/mnist/opset_7/mnist.tar.gz"
urllib.request.urlretrieve(onnx_model_url, filename="mnist.tar.gz")
!tar xvzf mnist.tar.gz

model = Model.register(workspace = ws,
                       model_path ="mnist/model.onnx",
                       model_name = "onnx_mnist",
                       tags = {"onnx": "demo"},
                       description = "MNIST image classification CNN from ONNX Model Zoo",)
```

**CLI를 사용 하 여**
```azurecli-interactive
az ml model register -n onnx_mnist -p mnist/model.onnx
```

**예상 시간**: 약 10초

자세한 내용은 [Model 클래스](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py)에 대한 참조 설명서를 참조하세요.

## <a name="how-to-deploy"></a>배포 하는 방법

웹 서비스로 배포 하는 유추 구성을 만들어야 합니다 (`InferenceConfig`) 및 배포 구성. 유추 구성에서 스크립트 및 모델을 제공 하는 데 필요한 종속성을 지정 합니다. 배포 구성에서 계산 대상에 모델을 제공 하는 방법의 세부 정보를 지정 합니다.


### <a id="script"></a> 1. 사용자 엔트리 스크립트가 종속성 정의

엔트리 스크립트가 배포 된 웹 서비스에 전송 된 데이터를 받고 모델에 전달 합니다. 그런 후 모델이 반환한 응답을 수락한 후 클라이언트에 반환합니다. **이 스크립트는 해당 모델**; 모델을 사용 하 고 반환 하는 데이터를 이해 해야 합니다.

스크립트를 로드 하 고 모델을 실행 하는 두 함수를 포함 되어 있습니다.

* `init()`: 일반적으로 이 함수는 모델을 전역 개체에 로드합니다. 이 함수는 웹 서비스에 대 한 Docker 컨테이너를 시작할 때 한 번만 실행 됩니다.

* `run(input_data)`: 이 함수는 모델을 사용하여 입력 데이터를 기반으로 값을 예측합니다. 실행에 대한 입력 및 출력은 일반적으로 serialization 및 deserialization용으로 JSON을 사용합니다. 원시 이진 데이터를 사용할 수도 있습니다. 모델에 보내기 전에 또는 클라이언트에 반환하기 전에 데이터를 변환할 수 있습니다.

#### <a name="optional-automatic-swagger-schema-generation"></a>(선택 사항) 자동으로 Swagger 스키마 생성

자동으로 웹 서비스에 대 한 스키마를 생성, 입력의 샘플을 제공 및/또는 출력에 정의 된 형식이 개체 형식 및 샘플 중 하나에 대 한 생성자를 자동으로 스키마를 만드는 사용 됩니다. Azure Machine Learning 서비스를 만듭니다는 [OpenAPI](https://swagger.io/docs/specification/about/) 배포 하는 동안 웹 서비스에 대 한 (Swagger) 사양입니다.

형식은 현재 지원 됩니다.

* `pandas`
* `numpy`
* `pyspark`
* 표준 Python 개체

스키마 생성을 사용 하려면 포함는 `inference-schema` conda 환경 파일에 패키지 합니다. 다음 예제에서는 `[numpy-support]` 엔트리 스크립트가 numpy 매개 변수 형식을 사용 하므로: 

#### <a name="example-dependencies-file"></a>예제 종속성 파일
다음은 유추를 위한 Conda 종속성 파일의 예입니다.
```python
name: project_environment
dependencies:
  - python=3.6.2
  - pip:
    - azureml-defaults
    - scikit-learn
    - inference-schema[numpy-support]
```

자동 스키마 생성, 입력 스크립트를 사용 하려는 경우 **해야 합니다** 가져오기는 `inference-schema` 패키지 있습니다. 

입력을 정의 하 고 출력의 샘플 형식 합니다 `input_sample` 및 `output_sample` 변수를 나타내는 웹 서비스에 대 한 요청 및 응답 형식입니다. 이러한 샘플을 사용 하 여 입력에서 및 데코레이터 함수에서 출력 된 `run()` 함수입니다. scikit-스키마 생성을 사용 하 여 아래 예제에 알아봅니다.

> [!TIP]
> 서비스를 배포한 후 사용 된 `swagger_uri` 스키마 JSON 문서를 검색할 속성입니다.

#### <a name="example-entry-script"></a>예제 항목 스크립트

다음 예에서는 허용 하 고 JSON 데이터를 반환 하는 방법을 보여 줍니다.

**Swagger 생성 Scikit-학습 예제:**
```python
import json
import numpy as np
from sklearn.externals import joblib
from sklearn.linear_model import Ridge
from azureml.core.model import Model

from inference_schema.schema_decorators import input_schema, output_schema
from inference_schema.parameter_types.numpy_parameter_type import NumpyParameterType

def init():
    global model
    # note here "sklearn_regression_model.pkl" is the name of the model registered under
    # this is a different behavior than before when the code is run locally, even though the code is the same.
    model_path = Model.get_model_path('sklearn_regression_model.pkl')
    # deserialize the model file back into a sklearn model
    model = joblib.load(model_path)

input_sample = np.array([[10,9,8,7,6,5,4,3,2,1]])
output_sample = np.array([3726.995])

@input_schema('data', NumpyParameterType(input_sample))
@output_schema(NumpyParameterType(output_sample))
def run(data):
    try:
        result = model.predict(data)
        # you can return any datatype as long as it is JSON-serializable
        return result.tolist()
    except Exception as e:
        error = str(e)
        return error
```

자세한 예제 스크립트의 경우 다음 예제를 참조 하세요.

* Pytorch: [https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-pytorch](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-pytorch)
* TensorFlow: [https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-tensorflow](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-tensorflow)
* Keras: [https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-keras](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-keras)
* ONNX: [https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/deployment/onnx/](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/deployment/onnx/)
* 이진 데이터에 대 한 점수 매기기: [웹 서비스를 사용 하는 방법](how-to-consume-web-service.md)

### <a name="2-define-your-inferenceconfig"></a>2. 프로그램 InferenceConfig 정의

유추 구성 예측 하는 모델을 구성 하는 방법에 설명 합니다. 다음 예제는 유추 구성을 만드는 방법을 보여 줍니다.

```python
inference_config = InferenceConfig(source_directory="C:/abc",
                                   runtime= "python",
                                   entry_script="x/y/score.py",
                                   conda_file="env/myenv.yml")
```

이 예제에서는 구성에는 다음 항목이 포함 됩니다.

* 유추 하는 데 필요한 자산을 포함 하는 디렉터리
* 이 모델에서는 Python는
* 합니다 [엔트리 스크립트가](#script), 배포 된 서비스에 전송 하는 웹 요청을 처리 하는 데 사용 되는
* 유추를 실행 하는 데 필요한 Python 패키지를 설명 하는 conda 파일

InferenceConfig 기능에 대 한 내용은 참조는 [고급 구성](#advanced-config) 섹션입니다.

### <a name="3-define-your-deployment-configuration"></a>3. 배포 구성 정의

를 배포 하기 전에 배포 구성을 정의 해야 합니다. 배포 구성 웹 서비스를 호스트 하는 계산 대상에 따라 다릅니다. 예를 들어, 로컬로 배포 하는 경우 서비스는 요청을 수락 하는 위치는 포트를 지정 해야 합니다.

계산 리소스를 만들 또한 해야 합니다. 예를 들어 있지 않으면 Azure Kubernetes Service 연결 작업 영역입니다.

다음 표에서 각 계산 대상에 대 한 배포 구성을 만드는 예제입니다.

| 계산 대상 | 배포 구성 예제 |
| ----- | ----- |
| 로컬 | `deployment_config = LocalWebservice.deploy_configuration(port=8890)` |
| Azure Container Instances | `deployment_config = AciWebservice.deploy_configuration(cpu_cores = 1, memory_gb = 1)` |
| Azure Kubernetes Service | `deployment_config = AksWebservice.deploy_configuration(cpu_cores = 1, memory_gb = 1)` |

다음 섹션에서는 배포 구성 만들기를 사용 하 여 웹 서비스를 배포 하는 방법을 보여 줍니다.

## <a name="where-to-deploy"></a>배포할 위치

### <a id="local"></a> 로컬로 배포

이 예제에서는 사용 섹션 [deploy_from_image](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice(class)?view=azure-ml-py#deploy-from-model-workspace--name--models--image-config--deployment-config-none--deployment-target-none-), 모델 및 이미지 배포를 수행 하기 전에 등록 해야 합니다. 다른 배포 방법에 대 한 자세한 내용은 참조 하세요. [배포할](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice(class)?view=azure-ml-py#deploy-workspace--name--model-paths--image-config--deployment-config-none--deployment-target-none-) 하 고 [deploy_from_model](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice(class)?view=azure-ml-py#deploy-from-model-workspace--name--models--image-config--deployment-config-none--deployment-target-none-)합니다.

**로컬에 배포 하려면 docker를 로컬 컴퓨터에 설치 해야 합니다.**

**SDK를 사용 하 여**

```python
deployment_config = LocalWebservice.deploy_configuration(port=8890)
service = Model.deploy(ws, "myservice", [model], inference_config, deployment_config)
service.wait_for_deployment(show_output = True)
print(service.state)
```

**CLI를 사용 하 여**

```azurecli-interactive
az ml model deploy -m sklearn_mnist:1 -ic inferenceconfig.json -dc deploymentconfig.json
```

### <a id="aci"></a> Azure Container Instances (DEVTEST)에 배포

다음 조건 중 하나 이상에 해당하는 경우 Azure Container Instances를 사용하여 모델을 웹 서비스로 배포합니다.
- 모델을 빠르게 배포하고 유효성을 검사해야 합니다.
- 개발 중인 모델을 테스트합니다. 

ACI에 대 한 할당량 및 지역 가용성을 확인, 참조를 [할당량 및 Azure Container Instances에 대 한 지역 가용성](https://docs.microsoft.com/azure/container-instances/container-instances-quotas) 문서.

**SDK를 사용 하 여**

```python
deployment_config = AciWebservice.deploy_configuration(cpu_cores = 1, memory_gb = 1)
service = Model.deploy(ws, "aciservice", [model], inference_config, deployment_config)
service.wait_for_deployment(show_output = True)
print(service.state)
```

**CLI를 사용 하 여**

```azurecli-interactive
az ml model deploy -m sklearn_mnist:1 -n aciservice -ic inferenceconfig.json -dc deploymentconfig.json
```

자세한 내용은 [AciWebservice](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aciwebservice?view=azure-ml-py) 및 [Webservice](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.webservice?view=azure-ml-py) 클래스에 대한 참조 설명서를 참조하세요.

### <a id="aks"></a> Azure Kubernetes Service (프로덕션)에 배포

기존 AKS 클러스터를 사용하거나 Azure Machine Learning SDK, CLI 또는 Azure Portal을 사용하여 새로운 클러스터를 만들 수 있습니다.


> [!IMPORTANT]
> AKS 클러스터 만들기는 작업 영역에 대한 일회성 프로세스입니다. 이 클러스터를 여러 배포에 재사용할 수 있습니다.
> 생성 하지 않았거나는 AKS를 연결 하는 경우 클러스터 go <a href="#create-attach-aks">여기</a>합니다.

#### AKS에 배포 <a id="deploy-aks"></a>

Azure ML CLI를 사용 하 여 AKS에 배포할 수 있습니다.
```azurecli-interactive
az ml model deploy -ct myaks -m mymodel:1 -n aksservice -ic inferenceconfig.json -dc deploymentconfig.json
```

또한 Python SDK를 사용할 수 있습니다.
```python
aks_target = AksCompute(ws,"myaks")
deployment_config = AksWebservice.deploy_configuration(cpu_cores = 1, memory_gb = 1)
service = Model.deploy(ws, "aksservice", [model], inference_config, deployment_config, aks_target)
service.wait_for_deployment(show_output = True)
print(service.state)
print(service.get_logs())
```

자동 크기 조정 등을 포함 하 여 AKS 배포를 구성 하는 방법에 대 한 자세한 내용은 참조는 [AksWebservice.deploy_configuration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.akswebservice) 참조 합니다.

**예상 시간:** 약 5 분입니다.

#### 만들거나 AKS 클러스터를 연결 합니다. <a id="create-attach-aks"></a>
만들거나 AKS 클러스터 연결을 **하나의 시간 프로세스** 작업 영역에 대 한 합니다. 클러스터에 작업 영역과 연결 된 후에 여러 배포에 대해 사용할 수 있습니다. 

클러스터 또는 포함 하는 리소스 그룹을 삭제 하면 다음에 배포 해야 새 클러스터를 만들어야 합니다.

##### <a name="create-a-new-aks-cluster"></a>새 AKS 클러스터 만들기
설정에 대 한 자세한 내용은 `autoscale_target_utilization`, `autoscale_max_replicas`, 및 `autoscale_min_replicas`를 참조 합니다 [AksWebservice.deploy_configuration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.akswebservice?view=azure-ml-py#deploy-configuration-autoscale-enabled-none--autoscale-min-replicas-none--autoscale-max-replicas-none--autoscale-refresh-seconds-none--autoscale-target-utilization-none--collect-model-data-none--auth-enabled-none--cpu-cores-none--memory-gb-none--enable-app-insights-none--scoring-timeout-ms-none--replica-max-concurrent-requests-none--max-request-wait-time-none--num-replicas-none--primary-key-none--secondary-key-none--tags-none--properties-none--description-none-) 참조 합니다.
다음 예제에서는 새 Azure Kubernetes Service 클러스터를 만드는 방법을 보여 줍니다.

```python
from azureml.core.compute import AksCompute, ComputeTarget

# Use the default configuration (you can also provide parameters to customize this)
prov_config = AksCompute.provisioning_configuration()

aks_name = 'myaks'
# Create the cluster
aks_target = ComputeTarget.create(workspace = ws,
                                    name = aks_name,
                                    provisioning_configuration = prov_config)

# Wait for the create process to complete
aks_target.wait_for_completion(show_output = True)
```

Azure Machine Learning SDK 외부에서 AKS 클러스터를 만드는 방법에 대 한 자세한 내용은 다음 문서를 참조 하세요.
* [AKS 클러스터 만들기](https://docs.microsoft.com/cli/azure/aks?toc=%2Fazure%2Faks%2FTOC.json&bc=%2Fazure%2Fbread%2Ftoc.json&view=azure-cli-latest#az-aks-create)
* [AKS 클러스터 (포털) 만들기](https://docs.microsoft.com/azure/aks/kubernetes-walkthrough-portal?view=azure-cli-latest)


> [!IMPORTANT]
> [`provisioning_configuration()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.akscompute?view=azure-ml-py)의 경우, agent_count 및 vm_size에 대한 사용자 지정 값을 선택하는 경우 agent_count와 vm_size를 곱한 값이 12개 가상 CPU보다 크거나 같아야 합니다. 예를 들어, 4개의 가상 CPU가 있는 "Standard_D3_v2"의 vm_size를 사용하는 경우는 3 이상의 agent_count를 선택해야 합니다.

**예상 시간**: 약 20분입니다.

##### <a name="attach-an-existing-aks-cluster"></a>기존 AKS 클러스터를 연결 합니다.

Azure 구독에서 AKS 클러스터를 이미 있는 경우 버전 1.12. # # 및 12 개 이상의 가상 Cpu에 이미지를 배포 하려면 사용할 수 있습니다. 다음 코드는 기존 AKS 1.12를 연결 하는 방법에 설명 합니다. # # 작업 영역에 클러스터:

```python
from azureml.core.compute import AksCompute, ComputeTarget
# Set the resource group that contains the AKS cluster and the cluster name
resource_group = 'myresourcegroup'
cluster_name = 'mycluster'

# Attach the cluster to your workgroup
attach_config = AksCompute.attach_configuration(resource_group = resource_group,
                                         cluster_name = cluster_name)
aks_target = ComputeTarget.attach(ws, 'mycompute', attach_config)
```

## <a name="consume-web-services"></a>웹 서비스 사용
모든 배포 된 웹 서비스는 프로그래밍 언어의 다양 한 클라이언트 응용 프로그램을 만들 수 있도록 REST API를 제공 합니다. 서비스에 대 한 인증을 설정한 경우에 요청 헤더에서 토큰으로 서비스 키를 제공 해야 합니다.

Python에서 서비스를 호출 하는 방법의 예는 다음과 같습니다.
```python
import requests
import json

headers = {'Content-Type':'application/json'}

if service.auth_enabled:
    headers['Authorization'] = 'Bearer '+service.get_keys()[0]

print(headers)
    
test_sample = json.dumps({'data': [
    [1,2,3,4,5,6,7,8,9,10], 
    [10,9,8,7,6,5,4,3,2,1]
]})

response = requests.post(service.scoring_uri, data=test_sample, headers=headers)
print(response.status_code)
print(response.elapsed)
print(response.json())
```

자세한 내용은 [웹 서비스를 사용할 클라이언트 애플리케이션 만들기](how-to-consume-web-service.md)를 참조하세요.

## <a id="update"></a> 웹 서비스 업데이트

새 모델을 만들 때 새 모델을 사용 하려는 각 서비스를 수동으로 업데이트 해야 합니다. 웹 서비스를 업데이트하려면 `update` 메서드를 사용합니다. 다음 코드에는 새 모델을 사용 하도록 웹 서비스를 업데이트 하는 방법을 보여 줍니다.

```python
from azureml.core.webservice import Webservice
from azureml.core.model import Model

# register new model
new_model = Model.register(model_path = "outputs/sklearn_mnist_model.pkl",
                       model_name = "sklearn_mnist",
                       tags = {"key": "0.1"},
                       description = "test",
                       workspace = ws)

service_name = 'myservice'
# Retrieve existing service
service = Webservice(name = service_name, workspace = ws)

# Update to new model(s)
service.update(models = [new_model])
print(service.state)
print(service.get_logs())
```

## <a name="clean-up"></a>정리
배포된 웹 서비스를 삭제하려면 `service.delete()`를 사용합니다.
등록된 모델을 삭제하려면 `model.delete()`를 사용합니다.

자세한 내용은 참조 설명서를 참조 하세요 [WebService.delete()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice(class)?view=azure-ml-py#delete--), 및 [Model.delete()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#delete--)합니다.

## 고급 구성 설정 <a id="advanced-config"></a>

### <a id="customimage"></a> 사용자 지정 기본 이미지를 사용 하 여

내부적으로 InferenceConfig 모델 및 서비스에 필요한 기타 자산을 포함 하는 Docker 이미지를 만듭니다. 지정 하지 않으면 기본 기본 이미지가 사용 됩니다.

유추 구성을 사용 하는 이미지를 만들 때 이미지에는 다음 요구 사항을 충족 해야 합니다.

* Ubuntu 16.04 이상입니다.
* Conda 4.5. # 이상.
* Python 3.5. # 또는 3.6. #.

사용자 지정 이미지를 사용 하려면 설정의 `base_image` 이미지의 주소로 유추 구성의 속성입니다. 다음 예제에서는 두 공용 및 개인 Azure 컨테이너 레지스트리에서 이미지를 사용 하는 방법을 보여 줍니다.

```python
# use an image available in public Container Registry without authentication
inference_config.base_image = "mcr.microsoft.com/azureml/o16n-sample-user-base/ubuntu-miniconda"

# or, use an image available in a private Container Registry
inference_config.base_image = "myregistry.azurecr.io/mycustomimage:1.0"
inference_config.base_image_registry.address = "myregistry.azurecr.io"
inference_config.base_image_registry.username = "username"
inference_config.base_image_registry.password = "password"
```

다음 이미지 Uri Microsoft에서 제공 하는 이미지에 대 한 중 이며 사용자 이름 또는 암호 값을 제공 하지 않고 사용할 수 있습니다.

* `mcr.microsoft.com/azureml/o16n-sample-user-base/ubuntu-miniconda`
* `mcr.microsoft.com/azureml/onnxruntime:v0.4.0`
* `mcr.microsoft.com/azureml/onnxruntime:v0.4.0-cuda10.0-cudnn7`
* `mcr.microsoft.com/azureml/onnxruntime:v0.4.0-tensorrt19.03`

이러한 이미지를 사용 하려면 설정의 `base_image` 위의 목록에서 uri입니다. `base_image_registry.address`을 `mcr.microsoft.com`로 설정합니다.

> [!IMPORTANT]
> Microsoft 이미지 CUDA 또는 TensorRT 사용 하는 Microsoft Azure 서비스에만 사용 되어야 합니다.

사용자 고유의 이미지를 Azure Container Registry에 업로드에 대 한 자세한 내용은 참조 하세요. [개인 Docker 컨테이너 레지스트리로 이미지 밀어넣기](https://docs.microsoft.com/azure/container-registry/container-registry-get-started-docker-cli)합니다.

모델은 Azure Machine Learning Compute에서 학습을 사용 하 여 __1.0.22 버전 이상__ Azure Machine Learning SDK의 이미지를 학습 하는 동안 만들어집니다. 다음 예제에서는이 이미지를 사용 하는 방법을 보여 줍니다.

```python
# Use an image built during training with SDK 1.0.22 or greater
image_config.base_image = run.properties["AzureML.DerivedImageName"]
```

## <a name="other-inference-options"></a>다른 유추 옵션

### <a id="azuremlcompute"></a> 일괄 처리 유추
Azure Machine Learning Compute 목표 생성 및 Azure Machine Learning 서비스에 의해 관리 됩니다. Azure Machine Learning 파이프라인에서 일괄 처리 예측을 위해 사용할 수 있습니다.

연습은 사용 하 여 Azure Machine Learning Compute batch 유추 읽기를 [일괄 처리 예측을 실행 하는 방법을](how-to-run-batch-predictions.md) 문서.

## <a id="iotedge"></a> IoT Edge의 유추
에 지에 배포 하는 것에 대 한 지원은 미리 보기로 제공 됩니다. 자세한 내용은 참조는 [IoT Edge 모듈로 Azure Machine Learning 배포](https://docs.microsoft.com/azure/iot-edge/tutorial-deploy-machine-learning) 문서.

## <a name="next-steps"></a>다음 단계
* [배포 문제 해결](how-to-troubleshoot-deployment.md)
* [SSL을 사용하여 Azure Machine Learning 웹 서비스 보호](how-to-secure-web-service.md)
* [웹 서비스로 배포된 ML 모델 사용](how-to-consume-web-service.md)
* [Application Insights를 사용하여 Azure Machine Learning 모델 모니터링](how-to-enable-app-insights.md)
* [프로덕션 환경에서 모델용 데이터 수집](how-to-enable-data-collection.md)
