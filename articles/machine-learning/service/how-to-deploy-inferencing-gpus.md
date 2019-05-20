---
title: GPU 사용 하 여 추론을 위해 모델 배포
titleSuffix: Azure Machine Learning service
description: 추론에 대 한 GPU를 사용 하는 웹 서비스로 심화 학습 모델을 배포 하는 방법에 알아봅니다. Tensorflow 모델은이 문서에서는 Azure Kubernetes Service 클러스터에 배포 됩니다. 클러스터는 호스트 웹 서비스 및 점수 매기기 추론 요청에 GPU 가능 VM을 사용합니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: vaidyas
author: csteegz
ms.reviewer: larryfr
ms.date: 05/02/2019
ms.openlocfilehash: 7796e8dc07889c9816e4227f3b38904d91a24da3
ms.sourcegitcommit: 1fbc75b822d7fe8d766329f443506b830e101a5e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/14/2019
ms.locfileid: "65595650"
---
# <a name="deploy-a-deep-learning-model-for-inferencing-with-gpu"></a>GPU 사용 하 여 추론에 대 한 딥 러닝 모델 배포

Machine learning 웹 서비스로 배포 하는 모델에 대 한 GPU 추론을 사용 하는 방법에 알아봅니다. 이 문서에서는 예로 Tensorflow 딥 러닝 모델을 배포 하려면 Azure Machine Learning 서비스를 사용 하는 방법을 알아봅니다. 모델은 GPU 가능 VM을 사용 하 여 서비스를 호스트 하는 Azure Kubernetes Service (AKS) 클러스터에 배포 됩니다. 요청 서비스에 전송 될 때 모델 추론을 수행 하는 GPU를 사용 합니다.

Gpu에는 항상 병렬 계산에 Cpu에 성능 이점을 제공합니다. 학습 및 추론 딥 러닝 모델 (특히 큰 일괄 처리 요청)은 Gpu에 대 한 좋은 사용 사례입니다.  

이 예제는 저장 TensorFlow 모델에서 Azure Machine Learning을 배포 하는 방법을 보여 줍니다.
* GPU 가능 AKS 클러스터 만들기
* Tensorflow GPU를 사용 하 여 모델 배포

## <a name="prerequisites"></a>필수 조건

* Azure Machine Learning 서비스 작업 영역
* Python
* Tensorflow SavedModel 등록 합니다. 모델을 등록 하는 방법을 알아보려면 참조 [모델 배포](https://docs.microsoft.com/azure/machine-learning/service/how-to-deploy-and-where#registermodel)

이 문서는 Jupyter notebook을 기준 [AKS에 배포 Tensorflow 모델](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/deployment/production-deploy-to-aks-gpu/production-deploy-to-aks-gpu.ipynb), 저장 TensorFlow를 사용 하는 모델 및 AKS 클러스터를 배포 합니다. 그러나 약간 변경 하 고 점수 매기기 파일 환경 파일을 사용 하 여 것를 Gpu를 지 원하는 모든 기계 학습 프레임 워크에 적용 합니다.  

## <a name="provision-aks-cluster-with-gpus"></a>Gpu 사용 하 여 AKS 클러스터를 프로 비전
Azure는 추론을 위해 사용할 수 있는 모든 다른 많은 GPU 옵션을 합니다. 참조 [N 시리즈 목록을](https://azure.microsoft.com/pricing/details/virtual-machines/linux/#n-series) 전체 분석 기능 및 비용에 대 한 합니다. 

AKS를 사용 하 여 Azure Machine Learning 서비스에 대 한 자세한 내용은 참조는 [문서를 배포 하는 방법.](https://docs.microsoft.com/azure/machine-learning/service/how-to-deploy-and-where#create-a-new-cluster)

```python
# Provision AKS cluster with GPU machine
prov_config = AksCompute.provisioning_configuration(vm_size="Standard_NC6")

# Create the cluster
aks_target = ComputeTarget.create(
    workspace=ws, name=aks_name, provisioning_configuration=prov_config
)

aks_target.wait_for_deployment()
```

> [!IMPORTANT]
> Azure에서 AKS 클러스터를 프로 비전 된으로 청구 합니다. 완료 되 면 AKS 클러스터를 삭제 해야 합니다. 사용 합니다.


## <a name="write-entry-script"></a>항목 스크립트 작성

다음으로 작업 디렉터리에 저장 `score.py`합니다. 이 파일은 서비스에 전송 되었을 때 이미지 점수 매기기를 사용 합니다. 이 파일 모델 저장 TensorFlow를 로드 하 고 각 게시물에 요청 전달 TensorFlow 세션 입력된 이미지 및 결과 점수를 반환 합니다.
다른 추론 프레임 워크는 다른 점수 매기기 파일이 필요 합니다.

```python
import tensorflow as tf
import numpy as np
import ujson
from azureml.core.model import Model
from azureml.contrib.services.aml_request import AMLRequest, rawhttp
from azureml.contrib.services.aml_response import AMLResponse

def init():
    global session
    global input_name
    global output_name
    
    session = tf.Session()

    model_path = Model.get_model_path('resnet50')
    model = tf.saved_model.loader.load(session, ['serve'], model_path)
    if len(model.signature_def['serving_default'].inputs) > 1:
        raise ValueError("This score.py only supports one input")
    input_name = [tensor.name for tensor in model.signature_def['serving_default'].inputs.values()][0]
    output_name = [tensor.name for tensor in model.signature_def['serving_default'].outputs.values()]
    

@rawhttp
def run(request):
    if request.method == 'POST':
        reqBody = request.get_data(False)
        resp = score(reqBody)
        return AMLResponse(resp, 200)
    if request.method == 'GET':
        respBody = str.encode("GET is not supported")
        return AMLResponse(respBody, 405)
    return AMLResponse("bad request", 500)

def score(data):
    result = session.run(output_name, {input_name: [data]})
    return ujson.dumps(result[1])

if __name__ == "__main__":
    init()
    with open("lynx.jpg", 'rb') as f: #load file for testing locally
        content = f.read()
        print(score(content))

```

## <a name="define-conda-environment"></a>Conda 환경 정의
Conda 환경 파일을 만듭니다 `myenv.yml` 서비스에 대 한 종속성을 지정 합니다. 사용 하는 지정 해야 `tensorflow-gpu` 가속화 된 성능을 얻을 수 있습니다.
```yaml
name: aml-accel-perf
channels:
  - defaults
dependencies:
  - tensorflow-gpu = 1.12
  - numpy
  - ujson
  - pip:
    - azureml-core
    - azureml-contrib-services
```

## <a name="define-gpu-inferenceconfig"></a>GPU InferenceConfig 정의

만들기는 [ `InferenceConfig` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.inferenceconfig?view=azure-ml-py) GPU를 사용 하는 있는지를 지정 합니다. 이렇게 하면 이미지를 사용 하 여 CUDA 설치 되어 있는지 합니다.

```python
from azureml.core.model import Model
from azureml.core.model import InferenceConfig

aks_service_name ='gpu-rn'
gpu_aks_config = AksWebservice.deploy_configuration(autoscale_enabled = False, 
                                                    num_replicas = 3, 
                                                    cpu_cores=2, 
                                                    memory_gb=4)
model = Model(ws,"resnet50")

inference_config = InferenceConfig(runtime= "python", 
                                   entry_script="score.py",
                                   conda_file="myenv.yml", 
                                   gpu_enabled=True)
```

자세한 내용은 [InferenceConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.inferenceconfig?view=azure-ml-py) 하 고 [AksServiceDeploymentConfiguration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aks.aksservicedeploymentconfiguration?view=azure-ml-py)합니다.
## <a name="deploy-the-model"></a>모델 배포

AKS 클러스터에 모델을 배포 하 고 서비스를 만드는 기다립니다.

```python
aks_service = Model.deploy(ws,
                           models=[model],
                           inference_config=inference_config, 
                           deployment_config=aks_config,
                           deployment_target=aks_target,
                           name=aks_service_name)

aks_service.wait_for_deployment(show_output = True)
print(aks_service.state)
```

> [!NOTE]
> Azure Machine Learning 서비스를 사용 하 여 모델을 배포 하지는 `InferenceConfig` GPU GPU 없이 클러스터에 필요한 합니다.

자세한 내용은 [모델](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py)합니다.

## <a name="issue-sample-query-to-deployed-model"></a>문제 샘플 쿼리를 모델 배포

배포 된 모델에 샘플 쿼리를 실행 합니다. 이 모델에 post 요청으로 보내는 모든 jpeg 이미지 점수 매기기 됩니다. 

```python
scoring_url = aks_service.scoring_uri
api_key = aks_service.get_key()(0)
IMAGEURL = "https://upload.wikimedia.org/wikipedia/commons/thumb/6/68/Lynx_lynx_poing.jpg/220px-Lynx_lynx_poing.jpg"

headers = {'Authorization':('Bearer '+ api_key)}
img_data = read_image_from(IMAGEURL).read()
r = requests.post(scoring_url, data = img_data, headers=headers)
```

> [!IMPORTANT]
> 대기 시간과 처리량을 최적화하려면 클라이언트가 엔드포인트와 동일한 Azure 지역에 있어야 합니다.  현재 API는 미국 동부 Azure 지역에 만들어졌습니다.

## <a name="cleaning-up-the-resources"></a>리소스 정리

데모를 사용 하 여 수행한 후에 리소스를 삭제 합니다.

> [!IMPORTANT]
> Azure는 AKS 클러스터가 배포 된 기간에 따라 청구 됩니다. 이 사용 하 여 수행한 후 정리 해야 합니다.

```python
aks_service.delete()
aks_target.delete()
```

## <a name="next-steps"></a>다음 단계

* [FPGA에 모델 배포](https://docs.microsoft.com/azure/machine-learning/service/how-to-deploy-fpga-web-service)
* [ONNX 사용 하 여 모델 배포](https://docs.microsoft.com/azure/machine-learning/service/how-to-build-deploy-onnx#deploy)
* [Tensorflow DNN 모델 학습](https://docs.microsoft.com/azure/machine-learning/service/how-to-train-tensorflow)
