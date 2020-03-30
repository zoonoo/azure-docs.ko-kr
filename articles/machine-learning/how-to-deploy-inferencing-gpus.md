---
title: GPU로 추론을 위한 모델 배포
titleSuffix: Azure Machine Learning
description: 이 문서에서는 Azure 기계 학습을 사용하여 GPU 지원 Tensorflow 딥 러닝 모델을 웹 서비스로 배포하고 추론 요청을 점수화하는 방법을 설명합니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: vaidyas
author: csteegz
ms.reviewer: larryfr
ms.date: 03/05/2020
ms.openlocfilehash: b0fd537d1930e7c9d5f7a33f56ec5d00b1556562
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78398345"
---
# <a name="deploy-a-deep-learning-model-for-inference-with-gpu"></a>GPU로 추론할 수 있는 딥 러닝 모델 배포
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

이 문서에서는 Azure 기계 학습을 사용하여 GPU 지원 모델을 웹 서비스로 배포하는 방법을 설명합니다. 이 문서의 정보는 AKS(Azure Kubernetes Service)에 모델을 배포하는 것을 기반으로 합니다. AKS 클러스터는 추론을 위해 모델에서 사용하는 GPU 리소스를 제공합니다.

추론 또는 모델 점수 매기기는 배포된 모델을 사용하여 예측을 하는 단계입니다. CPU 대신 GPU를 사용하면 병렬화성이 뛰어난 계산에서 성능이 향상됩니다.

> [!IMPORTANT]
> 웹 서비스 배포의 경우 GPU 추론은 Azure Kubernetes 서비스에서만 지원됩니다. __기계 학습 파이프라인을__사용하는 추론의 경우 GPU는 Azure 기계 학습 계산에서만 지원됩니다. ML 파이프라인 사용에 대한 자세한 내용은 [일괄 처리 예측 실행을](how-to-use-parallel-run-step.md)참조하십시오. 

> [!TIP]
> 이 문서의 코드 조각은 TensorFlow 모델을 사용하지만 GPU를 지원하는 모든 기계 학습 프레임워크에 정보를 적용할 수 있습니다.

> [!NOTE]
> 이 문서의 정보는 [Azure Kubernetes 서비스](how-to-deploy-azure-kubernetes-service.md) 에 배포하는 방법 문서의 정보를 기반으로 합니다. 이 문서에서는 일반적으로 AKS에 대한 배포를 다룹니다.

## <a name="prerequisites"></a>사전 요구 사항

* Azure Machine Learning 작업 영역 자세한 내용은 [Azure 기계 학습 작업 영역 만들기를](how-to-manage-workspace.md)참조하십시오.

* Azure 기계 학습 SDK가 설치된 파이썬 개발 환경입니다. 자세한 내용은 [Azure 기계 학습 SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py)를 참조하십시오.  

* GPU를 사용하는 등록된 모델입니다.

    * 모델을 등록하는 방법에 대해 알아보려면 [모델 배포](how-to-deploy-and-where.md#registermodel)를 참조하십시오.

    * 이 문서를 작성하는 데 사용되는 텐서플로우 모델을 작성하고 등록하려면 [텐서플로우 모델을 학습하는 방법을](how-to-train-tensorflow.md)참조하십시오.

* [모델을 배포하는 방법과 위치에 대한](how-to-deploy-and-where.md)일반적인 이해.

## <a name="connect-to-your-workspace"></a>작업 영역에 연결

기존 작업 영역에 연결하려면 다음 코드를 사용합니다.

> [!IMPORTANT]
> 이 코드 조각은 작업 영역 구성이 현재 디렉터리 또는 부모에 저장될 것으로 예상합니다. 작업 영역 만들기에 대한 자세한 내용은 [Azure 기계 학습 작업 영역 만들기 및 관리를](how-to-manage-workspace.md)참조하십시오.   구성을 파일로 저장하는 방법에 대한 자세한 내용은 [작업 영역 구성 파일 만들기를](how-to-configure-environment.md#workspace)참조하십시오.

```python
from azureml.core import Workspace

# Connect to the workspace
ws = Workspace.from_config()
```

## <a name="create-a-kubernetes-cluster-with-gpus"></a>GPU를 사용하여 Kubernetes 클러스터 만들기

Azure Kubernetes 서비스는 다양한 GPU 옵션을 제공합니다. 모델 추론에 사용할 수 있습니다. 기능 및 비용에 대한 전체 분석은 [N 시리즈 VM 목록을](https://azure.microsoft.com/pricing/details/virtual-machines/linux/#n-series) 참조하십시오.

다음 코드는 작업 영역에 대한 새 AKS 클러스터를 만드는 방법을 보여 줍니다.

```python
from azureml.core.compute import ComputeTarget, AksCompute
from azureml.exceptions import ComputeTargetException

# Choose a name for your cluster
aks_name = "aks-gpu"

# Check to see if the cluster already exists
try:
    aks_target = ComputeTarget(workspace=ws, name=aks_name)
    print('Found existing compute target')
except ComputeTargetException:
    print('Creating a new compute target...')
    # Provision AKS cluster with GPU machine
    prov_config = AksCompute.provisioning_configuration(vm_size="Standard_NC6")

    # Create the cluster
    aks_target = ComputeTarget.create(
        workspace=ws, name=aks_name, provisioning_configuration=prov_config
    )

    aks_target.wait_for_completion(show_output=True)
```

> [!IMPORTANT]
> Azure는 AKS 클러스터가 있는 한 요금을 청구합니다. 완료되면 AKS 클러스터를 삭제해야 합니다.

Azure 기계 학습을 사용하여 AKS를 사용하는 방법에 대한 자세한 내용은 [Azure Kubernetes 서비스에 배포하는 방법을 참조하세요.](how-to-deploy-azure-kubernetes-service.md)

## <a name="write-the-entry-script"></a>항목 스크립트 작성

입력 스크립트는 웹 서비스에 제출된 데이터를 수신하여 모델에 전달하고 점수 매기기 결과를 반환합니다. 다음 스크립트는 시작 시 Tensorflow 모델을 로드한 다음 모델을 사용하여 데이터를 점수 매기는 것입니다.

> [!TIP]
> 항목 스크립트는 모델에 따라 다릅니다. 예를 들어 스크립트는 모델, 데이터 형식 등과 함께 사용할 프레임워크를 알고 있어야 합니다.

```python
import json
import numpy as np
import os
import tensorflow as tf

from azureml.core.model import Model


def init():
    global X, output, sess
    tf.reset_default_graph()
    model_root = os.getenv('AZUREML_MODEL_DIR')
    # the name of the folder in which to look for tensorflow model files
    tf_model_folder = 'model'
    saver = tf.train.import_meta_graph(
        os.path.join(model_root, tf_model_folder, 'mnist-tf.model.meta'))
    X = tf.get_default_graph().get_tensor_by_name("network/X:0")
    output = tf.get_default_graph().get_tensor_by_name("network/output/MatMul:0")

    sess = tf.Session()
    saver.restore(sess, os.path.join(model_root, tf_model_folder, 'mnist-tf.model'))


def run(raw_data):
    data = np.array(json.loads(raw_data)['data'])
    # make prediction
    out = output.eval(session=sess, feed_dict={X: data})
    y_hat = np.argmax(out, axis=1)
    return y_hat.tolist()
```

이 파일의 `score.py`이름이 지정됩니다. 입력 스크립트에 대한 자세한 내용은 [배포 방법 및 위치를](how-to-deploy-and-where.md)참조하십시오.

## <a name="define-the-conda-environment"></a>콘다 환경 정의

conda 환경 파일은 서비스에 대한 종속성을 지정합니다. 여기에는 모델 및 항목 스크립트모두에 필요한 종속성이 포함됩니다. 웹 서비스로 모델을 호스트하는 데 필요한 기능이 포함되어 있으므로 verion >= 1.0.45를 핍 종속성으로 azureml 기본값을 표시해야 합니다. 다음 YAML은 텐서플로우 모델에 대한 환경을 정의합니다. 이 배포에 `tensorflow-gpu`사용된 GPU를 사용할 수 있도록 지정합니다.

```yaml
name: project_environment
dependencies:
  # The python interpreter version.
  # Currently Azure ML only supports 3.5.2 and later.
- python=3.6.2

- pip:
  # You must list azureml-defaults as a pip dependency
  - azureml-defaults>=1.0.45
- numpy
- tensorflow-gpu=1.12
channels:
- conda-forge
```

이 예제에서는 파일이 `myenv.yml`로 저장됩니다.

## <a name="define-the-deployment-configuration"></a>배포 구성 정의

배포 구성은 웹 서비스를 실행하는 데 사용되는 Azure Kubernetes 서비스 환경을 정의합니다.

```python
from azureml.core.webservice import AksWebservice

gpu_aks_config = AksWebservice.deploy_configuration(autoscale_enabled=False,
                                                    num_replicas=3,
                                                    cpu_cores=2,
                                                    memory_gb=4)
```

자세한 내용은 [AksService.deploy_configuration](/python/api/azureml-core/azureml.core.webservice.akswebservice?view=azure-ml-py#deploy-configuration-autoscale-enabled-none--autoscale-min-replicas-none--autoscale-max-replicas-none--autoscale-refresh-seconds-none--autoscale-target-utilization-none--collect-model-data-none--auth-enabled-none--cpu-cores-none--memory-gb-none--enable-app-insights-none--scoring-timeout-ms-none--replica-max-concurrent-requests-none--max-request-wait-time-none--num-replicas-none--primary-key-none--secondary-key-none--tags-none--properties-none--description-none--gpu-cores-none--period-seconds-none--initial-delay-seconds-none--timeout-seconds-none--success-threshold-none--failure-threshold-none--namespace-none--token-auth-enabled-none--compute-target-name-none-)에 대한 참조 문서를 참조하십시오.

## <a name="define-the-inference-configuration"></a>추론 구성 정의

추론 구성은 GPU 를 지원하는 도커 이미지를 사용하는 엔트리 스크립트와 환경 개체를 가리킵니다. 환경 정의에 사용되는 YAML 파일은 웹 서비스로 모델을 호스트하는 데 필요한 기능을 포함하기 때문에 버전 >= 1.0.45를 핍 종속성으로 나열해야 합니다.

```python
from azureml.core.model import InferenceConfig
from azureml.core.environment import Environment, DEFAULT_GPU_IMAGE

myenv = Environment.from_conda_specification(name="myenv", file_path="myenv.yml")
myenv.docker.base_image = DEFAULT_GPU_IMAGE
inference_config = InferenceConfig(entry_script="score.py", environment=myenv)
```

환경에 대한 자세한 내용은 [교육 및 배포를 위한 환경 만들기 및 관리를](how-to-use-environments.md)참조하십시오.
자세한 내용은 [InferenceConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.inferenceconfig?view=azure-ml-py)에 대한 참조 설명서를 참조하십시오.

## <a name="deploy-the-model"></a>모델 배포

모델을 AKS 클러스터에 배포하고 서비스가 생성될 때까지 기다립니다.

```python
from azureml.core.model import Model

# Name of the web service that is deployed
aks_service_name = 'aks-dnn-mnist'
# Get the registerd model
model = Model(ws, "tf-dnn-mnist")
# Deploy the model
aks_service = Model.deploy(ws,
                           models=[model],
                           inference_config=inference_config,
                           deployment_config=gpu_aks_config,
                           deployment_target=aks_target,
                           name=aks_service_name)

aks_service.wait_for_deployment(show_output=True)
print(aks_service.state)
```

> [!NOTE]
> 개체에 `InferenceConfig` `enable_gpu=True`있는 경우 `deployment_target` 매개 변수는 GPU를 제공하는 클러스터를 참조해야 합니다. 그렇지 않으면 배포에 실패합니다.

자세한 내용은 [모델](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py)에 대한 참조 설명서를 참조하십시오.

## <a name="issue-a-sample-query-to-your-service"></a>서비스에 샘플 쿼리 를 발행합니다.

배포된 모델로 테스트 쿼리를 보냅니다. jpeg 이미지를 모델에 보내면 이미지의 점수가 표시됩니다. 다음 코드 샘플은 테스트 데이터를 다운로드한 다음 서비스에 보낼 임의 테스트 이미지를 선택합니다.

```python
# Used to test your webservice
import os
import urllib
import gzip
import numpy as np
import struct
import requests

# load compressed MNIST gz files and return numpy arrays
def load_data(filename, label=False):
    with gzip.open(filename) as gz:
        struct.unpack('I', gz.read(4))
        n_items = struct.unpack('>I', gz.read(4))
        if not label:
            n_rows = struct.unpack('>I', gz.read(4))[0]
            n_cols = struct.unpack('>I', gz.read(4))[0]
            res = np.frombuffer(gz.read(n_items[0] * n_rows * n_cols), dtype=np.uint8)
            res = res.reshape(n_items[0], n_rows * n_cols)
        else:
            res = np.frombuffer(gz.read(n_items[0]), dtype=np.uint8)
            res = res.reshape(n_items[0], 1)
    return res

# one-hot encode a 1-D array
def one_hot_encode(array, num_of_classes):
    return np.eye(num_of_classes)[array.reshape(-1)]

# Download test data
os.makedirs('./data/mnist', exist_ok=True)
urllib.request.urlretrieve('http://yann.lecun.com/exdb/mnist/t10k-images-idx3-ubyte.gz', filename='./data/mnist/test-images.gz')
urllib.request.urlretrieve('http://yann.lecun.com/exdb/mnist/t10k-labels-idx1-ubyte.gz', filename='./data/mnist/test-labels.gz')

# Load test data from model training
X_test = load_data('./data/mnist/test-images.gz', False) / 255.0
y_test = load_data('./data/mnist/test-labels.gz', True).reshape(-1)

# send a random row from the test set to score
random_index = np.random.randint(0, len(X_test)-1)
input_data = "{\"data\": [" + str(list(X_test[random_index])) + "]}"

api_key = aks_service.get_keys()[0]
headers = {'Content-Type': 'application/json',
           'Authorization': ('Bearer ' + api_key)}
resp = requests.post(aks_service.scoring_uri, input_data, headers=headers)

print("POST to url", aks_service.scoring_uri)
print("label:", y_test[random_index])
print("prediction:", resp.text)
```

클라이언트 응용 프로그램 만들기에 대한 자세한 내용은 [배포된 웹 서비스를 사용하도록 클라이언트 만들기를](how-to-consume-web-service.md)참조하십시오.

## <a name="clean-up-the-resources"></a>리소스 정리

이 예제를 위해 AKS 클러스터를 특별히 만든 경우 완료된 후 리소스를 삭제합니다.

> [!IMPORTANT]
> Azure는 AKS 클러스터가 배포되는 기간에 따라 청구됩니다. 완료 된 후 청소 해야 합니다.

```python
aks_service.delete()
aks_target.delete()
```

## <a name="next-steps"></a>다음 단계

* [FPGA에 모델 배포](how-to-deploy-fpga-web-service.md)
* [ONNX를 통해 모델 배포](concept-onnx.md#deploy-onnx-models-in-azure)
* [텐서플로우 DNN 모델 학습](how-to-train-tensorflow.md)
