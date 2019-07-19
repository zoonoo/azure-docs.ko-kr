---
title: GPU를 사용 하 여 유추를 위한 모델 배포
titleSuffix: Azure Machine Learning service
description: 이 문서에서는 Azure Machine Learning 서비스를 사용 하 여 GPU 사용 Tensorflow 심층 학습 모델을 웹 서비스로 배포 하는 방법을 설명 합니다. 서비스 및 점수 유추 요청
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: vaidyas
author: csteegz
ms.reviewer: larryfr
ms.date: 06/01/2019
ms.openlocfilehash: eeb1bc35e0438a7e99ea5ed8284f0c8611108da0
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/18/2019
ms.locfileid: "68326980"
---
# <a name="deploy-a-deep-learning-model-for-inference-with-gpu"></a>GPU를 사용 하 여 유추를 위한 심층 학습 모델 배포

이 문서에서는 Azure Machine Learning 서비스를 사용 하 여 GPU 사용 Tensorflow 심층 학습 모델을 웹 서비스로 배포 하는 방법을 설명 합니다.

AKS (Azure Kubernetes Service) 클러스터에 모델을 배포 하 여 GPU 사용 추론를 수행 합니다. 추론 또는 모델 점수 매기기는 배포 된 모델이 예측에 사용 되는 단계입니다. Cpu 대신 Gpu를 사용 하면 병렬화 계산에 대 한 성능 이점이 있습니다.

이 샘플에서는 TensorFlow 모델을 사용 하지만 점수 매기기 파일과 환경 파일을 약간만 변경 하 여 Gpu를 지 원하는 모든 기계 학습 프레임 워크에 다음 단계를 적용할 수 있습니다. 

이 문서에서는 다음 단계를 수행합니다.

* GPU 사용 AKS 클러스터 만들기
* Tensorflow GPU 모델 배포
* 배포 된 모델에 대 한 샘플 쿼리 실행

## <a name="prerequisites"></a>필수 구성 요소

* Azure Machine Learning 서비스 작업 영역입니다.
* Python 배포판.
* 등록 된 Tensorflow 저장 된 모델입니다.
    * 모델을 등록 하는 방법에 대 한 자세한 내용은 [모델 배포](../service/how-to-deploy-and-where.md#registermodel)를 참조 하세요.

이 방법 시리즈 중 하나를 완료 하 고 [TensorFlow 모델을 학습 하는 방법](how-to-train-tensorflow.md)으로 필요한 필수 구성 요소를 충족할 수 있습니다.

## <a name="provision-an-aks-cluster-with-gpus"></a>Gpu를 사용 하 여 AKS 클러스터 프로 비전

Azure에는 다양 한 GPU 옵션이 있습니다. 추론에 대해 이러한 항목 중 하나를 사용할 수 있습니다. 기능 및 비용에 대 한 전체 분석은 [N 시리즈 vm의 목록을](https://azure.microsoft.com/pricing/details/virtual-machines/linux/#n-series) 참조 하세요.

Azure Machine Learning 서비스에서 AKS를 사용 하는 방법에 대 한 자세한 내용은 [배포 방법 및 위치](../service/how-to-deploy-and-where.md#deploy-aks)를 참조 하세요.

```Python
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
> Azure는 AKS 클러스터를 프로 비전 하는 동안 요금을 청구 합니다. 작업을 완료 하면 AKS 클러스터를 삭제 해야 합니다.

## <a name="write-the-entry-script"></a>항목 스크립트 작성

다음 코드를 작업 디렉터리에로 `score.py`저장 합니다. 이 파일은 서비스에 전송 되는 이미지 점수를 가집니다. TensorFlow 저장 된 모델을 로드 하 고 각 POST 요청에서 입력 이미지를 TensorFlow 세션에 전달한 다음 결과 점수를 반환 합니다. 다른 추론 프레임 워크에는 다른 점수 매기기 파일이 필요 합니다.

```python
import json
import numpy as np
import os
import tensorflow as tf

from azureml.core.model import Model

def init():
    global X, output, sess
    tf.reset_default_graph()
    model_root = Model.get_model_path('tf-dnn-mnist')
    saver = tf.train.import_meta_graph(os.path.join(model_root, 'mnist-tf.model.meta'))
    X = tf.get_default_graph().get_tensor_by_name("network/X:0")
    output = tf.get_default_graph().get_tensor_by_name("network/output/MatMul:0")
    
    sess = tf.Session()
    saver.restore(sess, os.path.join(model_root, 'mnist-tf.model'))

def run(raw_data):
    data = np.array(json.loads(raw_data)['data'])
    # make prediction
    out = output.eval(session=sess, feed_dict={X: data})
    y_hat = np.argmax(out, axis=1)
    return y_hat.tolist()

```
## <a name="define-the-conda-environment"></a>Conda 환경 정의

서비스에 대 한 종속성을 `myenv.yml` 지정 하는 이라는 conda environment 파일을 만듭니다. 를 사용 `tensorflow-gpu` 하 여 가속화 된 성능을 얻도록 지정 하는 것이 중요 합니다.

```yaml
name: project_environment
dependencies:
  # The python interpreter version.
  # Currently Azure ML only supports 3.5.2 and later.
- python=3.6.2

- pip:
  - azureml-defaults==1.0.43.*
- numpy
- tensorflow-gpu=1.12
channels:
- conda-forge
```

## <a name="define-the-gpu-inferenceconfig-class"></a>GPU InferenceConfig 클래스 정의

Gpu를 사용 하도록 설정 하는 개체를만들고Docker이미지를사용하여이를설치합니다.`InferenceConfig`

```python
from azureml.core.model import Model
from azureml.core.model import InferenceConfig

aks_service_name ='aks-dnn-mnist'
gpu_aks_config = AksWebservice.deploy_configuration(autoscale_enabled = False, 
                                                    num_replicas = 3, 
                                                    cpu_cores=2, 
                                                    memory_gb=4)
model = Model(ws,"tf-dnn-mnist")

inference_config = InferenceConfig(runtime= "python", 
                                   entry_script="score.py",
                                   conda_file="myenv.yml", 
                                   enable_gpu=True)
```

참조 항목:

- [InferenceConfig 클래스](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.inferenceconfig?view=azure-ml-py)
- [AksServiceDeploymentConfiguration 클래스](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aks.aksservicedeploymentconfiguration?view=azure-ml-py)

## <a name="deploy-the-model"></a>모델 배포

AKS 클러스터에 모델을 배포 하 고 서비스를 만들 때까지 기다립니다.

```python
aks_service = Model.deploy(ws,
                           models=[model],
                           inference_config=inference_config, 
                           deployment_config=gpu_aks_config,
                           deployment_target=aks_target,
                           name=aks_service_name)

aks_service.wait_for_deployment(show_output = True)
print(aks_service.state)
```

> [!NOTE]
> Azure Machine Learning 서비스는 gpu가 없는 클러스터에 `InferenceConfig` 대해 gpu를 사용 하도록 요구 하는 개체를 사용 하 여 모델을 배포 하지 않습니다.

자세한 내용은 [모델 클래스](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py)를 참조 하세요.

## <a name="issue-a-sample-query-to-your-model"></a>모델에 샘플 쿼리 실행

배포 된 모델에 테스트 쿼리를 보냅니다. Jpeg 이미지를 모델로 보내면 이미지 점수가 표시 됩니다. 다음 코드 샘플에서는 외부 유틸리티 함수를 사용 하 여 이미지를 로드 합니다. [GitHub의 pir TensorFlow 샘플](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-tensorflow/utils.py)에서 관련 코드를 찾을 수 있습니다. 

```python
# Used to test your webservice
from utils import load_data 

# Load test data from model training
X_test = load_data('./data/mnist/test-images.gz', False) / 255.0
y_test = load_data('./data/mnist/test-labels.gz', True).reshape(-1)

# send a random row from the test set to score
random_index = np.random.randint(0, len(X_test)-1)
input_data = "{\"data\": [" + str(list(X_test[random_index])) + "]}"

api_key = aks_service.get_keys()[0]
headers = {'Content-Type':'application/json', 'Authorization':('Bearer '+ api_key)}
resp = requests.post(aks_service.scoring_uri, input_data, headers=headers)

print("POST to url", aks_service.scoring_uri)
#print("input data:", input_data)
print("label:", y_test[random_index])
print("prediction:", resp.text)
```

> [!IMPORTANT]
> 대기 시간을 최소화 하 고 처리량을 최적화 하려면 클라이언트가 끝점과 동일한 Azure 지역에 있어야 합니다. 이 예제에서 Api는 미국 동부 Azure 지역에 생성 됩니다.

## <a name="clean-up-the-resources"></a>리소스 정리

이 예를 위해 특별히 AKS 클러스터를 만든 경우 완료 한 후 리소스를 삭제 합니다.

> [!IMPORTANT]
> Azure는 AKS 클러스터의 배포 기간에 따라 청구 합니다. 작업을 완료 한 후에는 정리 해야 합니다.

```python
aks_service.delete()
aks_target.delete()
```

## <a name="next-steps"></a>다음 단계

* [FPGA에 모델 배포](../service/how-to-deploy-fpga-web-service.md)
* [ONNX를 사용 하 여 모델 배포](../service/concept-onnx.md#deploy-onnx-models-in-azure)
* [Tensorflow DNN 모델 학습](../service/how-to-train-tensorflow.md)
