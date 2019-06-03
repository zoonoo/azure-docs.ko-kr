---
title: GPU 사용 하 여 유추 모델 배포
titleSuffix: Azure Machine Learning service
description: 심층 학습 모델을 유추에 GPU를 사용 하는 웹 서비스로 배포 하는 방법에 알아봅니다. Tensorflow 모델은이 문서에서는 Azure Kubernetes Service 클러스터에 배포 됩니다. 클러스터는 호스트 웹 서비스 및 점수 매기기 유추 요청에 GPU 가능 VM을 사용합니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: vaidyas
author: csteegz
ms.reviewer: larryfr
ms.date: 05/02/2019
ms.openlocfilehash: ec71165553a1d65ff133d605bf94255100f74e6e
ms.sourcegitcommit: 3d4121badd265e99d1177a7c78edfa55ed7a9626
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/30/2019
ms.locfileid: "66388931"
---
# <a name="deploy-a-deep-learning-model-for-inference-with-gpu"></a>GPU 사용 하 여 유추에 대 한 딥 러닝 모델 배포

Machine learning 웹 서비스로 배포 하는 모델에 대 한 GPU 유추를 사용 하는 방법에 알아봅니다. 모델 점수 매기기 또는 유추 하는 단계 프로덕션 데이터에 가장 일반적으로 예측에 대 한 배포 된 모델이 사용 되는 위치입니다.

이 문서에서는 예로 Tensorflow 딥 러닝 모델을 GPU 사용 가상 컴퓨터 (VM)에서 Azure Kubernetes Service (AKS) 클러스터를 배포 하려면 Azure Machine Learning 서비스를 사용 하는 방법을 설명 합니다. 요청 서비스에 전송 될 때 모델 유추 워크 로드를 실행 하는 GPU를 사용 합니다.

Gpu에는 항상 병렬 계산에 Cpu에 성능 이점을 제공합니다. GPU 사용 Vm에 대 한 뛰어난 사용 사례로 딥 러닝 모델 학습 및 특히 큰 일괄 처리 요청에 대 한 유추 합니다.

이 예제에서는 Azure Machine learning 모델을 저장 하는 TensorFlow를 배포 하는 방법에 설명 합니다. 다음 단계를 수행하세요.

* GPU 가능 AKS 클러스터 만들기
* Tensorflow GPU 모델 배포

## <a name="prerequisites"></a>필수 조건

* 작업 영역을 Azure Machine Learning 서비스
* Python 배포판
* 등록 된 Tensorflow 모델을 저장 합니다. 모델을 등록 하는 방법에 알아보려면 참조 [배포 모델](../service/how-to-deploy-and-where.md#registermodel)합니다.

이 문서에서는 Jupyter notebook을 더해서 [AKS에 배포 Tensorflow 모델](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/deployment/production-deploy-to-aks-gpu/production-deploy-to-aks-gpu.ipynb)합니다. Jupyter notebook에는 모델 저장 TensorFlow를 사용 하 여 및 AKS 클러스터에 배포 합니다. 또한 machine learning 점수 매기기 파일 및 환경 파일을 작은 변경 하 여 Gpu를 지 원하는 프레임 워크에 notebook을 적용할 수 있습니다.  

## <a name="provision-an-aks-cluster-with-gpus"></a>Gpu 사용 하 여 AKS 클러스터를 프로 비전

Azure는 다양 한 GPU 옵션입니다. 추론을 위해 이들 중 하나를 사용할 수 있습니다. 참조 [N 시리즈 Vm 목록을](https://azure.microsoft.com/pricing/details/virtual-machines/linux/#n-series) 전체 분석 기능 및 비용에 대 한 합니다.

AKS를 사용 하 여 Azure Machine Learning 서비스에 대 한 자세한 내용은 참조 하세요. [배포 하는 방법 및 위치](../service/how-to-deploy-and-where.md#deploy-aks)합니다.

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
> Azure에서 AKS 클러스터를 프로 비전 된으로 청구 합니다. 이 사용 하 여 완료 되 면 AKS 클러스터를 삭제 해야 합니다.

## <a name="write-the-entry-script"></a>항목 스크립트 작성

다음 코드와 작업 디렉터리에 저장 `score.py`합니다. 이 파일 서비스에 전송 하는 이미지를 점수입니다. 저장 TensorFlow 모델을 로드, TensorFlow 세션 입력된 이미지를 각 POST 요청을 전달 하 고 결과 점수를 반환 합니다. 다른 추론 프레임 워크에는 다른 점수 매기기 파일이 필요합니다.

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

## <a name="define-the-conda-environment"></a>Conda 환경 정의

Conda 환경 파일을 만듭니다 `myenv.yml` 서비스에 대 한 종속성을 지정 합니다. 사용 하는 경우 지정 해야 `tensorflow-gpu` 가속화 된 성능을 얻을 수 있습니다.

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

## <a name="define-the-gpu-inferenceconfig-class"></a>GPU InferenceConfig 클래스를 정의 합니다.

만들기는 `InferenceConfig` Gpu를 사용 하도록 설정 하 고 Docker 이미지를 사용 하 여 CUDA 설치 되어 있는지 확인 하는 개체입니다.

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

## <a name="issue-a-sample-query-to-your-deployed-model"></a>배포 된 모델에 샘플 쿼리를 실행 합니다.

배포 된 모델에 테스트 쿼리를 보냅니다. Jpeg 이미지를 모델로 보내면 이미지 점수 매깁니다.

```python
scoring_url = aks_service.scoring_uri
api_key = aks_service.get_key()(0)
IMAGEURL = "https://upload.wikimedia.org/wikipedia/commons/thumb/6/68/Lynx_lynx_poing.jpg/220px-Lynx_lynx_poing.jpg"

headers = {'Authorization':('Bearer '+ api_key)}
img_data = read_image_from(IMAGEURL).read()
r = requests.post(scoring_url, data = img_data, headers=headers)
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
