---
title: GPU 사용 하 여 유추 모델 배포
titleSuffix: Azure Machine Learning service
description: 이 문서에서는 Azure Machine Learning 서비스를 사용 하 여 딥 러닝 모델을 웹 service.service 및 점수 매기기 유추 요청에는 GPU 가능 Tensorflow를 배포 하는 방법을 설명 합니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: vaidyas
author: csteegz
ms.reviewer: larryfr
ms.date: 06/01/2019
ms.openlocfilehash: 8086d059913cc61bff0bca31681368bea6d76777
ms.sourcegitcommit: 5bdd50e769a4d50ccb89e135cfd38b788ade594d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/03/2019
ms.locfileid: "67543797"
---
# <a name="deploy-a-deep-learning-model-for-inference-with-gpu"></a>GPU 사용 하 여 유추에 대 한 딥 러닝 모델 배포

이 문서에서는 Azure Machine Learning 서비스는 GPU 가능 Tensorflow 딥 러닝 모델을 웹 서비스로 배포를 사용 하는 방법에 설명 합니다.

GPU 가능 추론을 위해 Azure Kubernetes Service (AKS) 클러스터에 모델을 배포 합니다. 모델 점수 매기기 인지 단계는 예측에 대 한 배포 된 모델이 사용 되는 위치입니다. Cpu 성능 장점을 항상 병렬 계산에서 제공 하는 대신 Gpu를 사용 합니다.

이 샘플에서는 TensorFlow 모델을 사용 하지만 machine learning 점수 매기기 파일 및 환경 파일을 작은 변경 하 여 Gpu를 지 원하는 프레임 워크에 다음 단계를 적용할 수 있습니다. 

이 문서에서는 다음 단계를 수행합니다.

* GPU 가능 AKS 클러스터 만들기
* Tensorflow GPU 모델 배포
* 배포 된 모델에 샘플 쿼리를 실행 합니다.

## <a name="prerequisites"></a>필수 조건

* Azure Machine Learning 서비스 작업 영역입니다.
* Python 배포판입니다.
* 등록 된 Tensorflow 모델을 저장 합니다.
    * 모델을 등록 하는 방법에 알아보려면 참조 [배포 모델](../service/how-to-deploy-and-where.md#registermodel)합니다.

이 사용 방법 시리즈의 1 부를 완료할 수 있습니다 [TensorFlow 모델을 학습 하는 방법을](how-to-train-tensorflow.md)를 필요한 전제 조건을 충족 합니다.

## <a name="provision-an-aks-cluster-with-gpus"></a>Gpu 사용 하 여 AKS 클러스터를 프로 비전

Azure는 다양 한 GPU 옵션입니다. 추론을 위해 이들 중 하나를 사용할 수 있습니다. 참조 [N 시리즈 Vm 목록을](https://azure.microsoft.com/pricing/details/virtual-machines/linux/#n-series) 전체 분석 기능 및 비용에 대 한 합니다.

AKS를 사용 하 여 Azure Machine Learning 서비스에 대 한 자세한 내용은 참조 하세요. [배포 하는 방법 및 위치](../service/how-to-deploy-and-where.md#deploy-aks)합니다.

```Python
# Choose a name for your cluster
aks_name = "aks-gpu"

# Check to see if the cluster already exists
try:
    compute_target = ComputeTarget(workspace=ws, name=aks_name)
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
> Azure에서 AKS 클러스터를 프로 비전 된으로 청구 합니다. 이 사용 하 여 완료 되 면 AKS 클러스터를 삭제 해야 합니다.

## <a name="write-the-entry-script"></a>항목 스크립트 작성

다음 코드와 작업 디렉터리에 저장 `score.py`합니다. 이 파일 서비스에 전송 하는 이미지를 점수입니다. 저장 TensorFlow 모델을 로드, TensorFlow 세션 입력된 이미지를 각 POST 요청을 전달 하 고 결과 점수를 반환 합니다. 다른 추론 프레임 워크에는 다른 점수 매기기 파일이 필요합니다.

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

Conda 환경 파일을 만듭니다 `myenv.yml` 서비스에 대 한 종속성을 지정 합니다. 사용 하는 경우 지정 해야 `tensorflow-gpu` 가속화 된 성능을 얻을 수 있습니다.

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

## <a name="define-the-gpu-inferenceconfig-class"></a>GPU InferenceConfig 클래스를 정의 합니다.

만들기는 `InferenceConfig` Gpu를 사용 하도록 설정 하 고 Docker 이미지를 사용 하 여 CUDA 설치 되어 있는지 확인 하는 개체입니다.

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

자세한 내용은 다음을 참조하세요.

- [InferenceConfig 클래스](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.inferenceconfig?view=azure-ml-py)
- [AksServiceDeploymentConfiguration 클래스](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aks.aksservicedeploymentconfiguration?view=azure-ml-py)

## <a name="deploy-the-model"></a>모델 배포

AKS 클러스터에 모델을 배포 하 고 서비스를 만드는 기다립니다.

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
> Azure Machine Learning 서비스는 사용 하 여 모델을 배포 하지 않는 한 `InferenceConfig` GPU는 GPU가 없는 클러스터에 사용할 필요로 하는 개체입니다.

자세한 내용은 [모델 클래스](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py)합니다.

## <a name="issue-a-sample-query-to-your-model"></a>모델에 샘플 쿼리를 실행 합니다.

배포 된 모델에 테스트 쿼리를 보냅니다. Jpeg 이미지를 모델로 보내면 이미지 점수 매깁니다. 다음 코드 샘플 이미지를 로드 하는 외부 유틸리티 함수를 사용 합니다. Pir에서 관련 코드를 찾을 수 있습니다 [GitHub에서 TensorFlow 샘플](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-tensorflow/utils.py)합니다. 

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
> 대기 시간을 최소화 하 고 처리량을 최적화 하려면 클라이언트 끝점과 동일한 Azure 지역에 있는지를 확인 합니다. 이 예제에서는 Api는 미국 동부 Azure 지역에 생성 됩니다.

## <a name="clean-up-the-resources"></a>리소스 정리

이 예제를 사용 하 여 완료 한 후 리소스를 삭제 합니다.

> [!IMPORTANT]
> Azure 청구서를 AKS 클러스터를 배포 하는 기간에 기반 합니다. 이 사용 하 여 수행한 후 정리 해야 합니다.

```python
aks_service.delete()
aks_target.delete()
```

## <a name="next-steps"></a>다음 단계

* [FPGA에 모델 배포](../service/how-to-deploy-fpga-web-service.md)
* [ONNX 사용 하 여 모델 배포](../service/concept-onnx.md#deploy-onnx-models-in-azure)
* [Tensorflow DNN 모델 학습](../service/how-to-train-tensorflow.md)
