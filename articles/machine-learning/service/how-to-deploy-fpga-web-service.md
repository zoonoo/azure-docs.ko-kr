---
title: FPGA에 모델 배포
titleSuffix: Azure Machine Learning service
description: 매우 낮은 대기 시간 유추를 위해 Azure Machine Learning 서비스를 사용 하 여 FPGA에서 실행 되는 모델을 사용 하 여 웹 서비스를 배포 하는 방법을 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: larryfr
ms.author: tedway
author: tedway
ms.date: 07/25/2019
ms.custom: seodec18
ms.openlocfilehash: 5e780c1a1d18954d0b3e9413e26f478e4ea3a8b1
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/08/2019
ms.locfileid: "68856099"
---
# <a name="deploy-a-model-as-a-web-service-on-an-fpga-with-azure-machine-learning-service"></a>Azure Machine Learning 서비스를 사용하여 FPGA에 웹 서비스로 모델 배포

Azure Machine Learning 하드웨어 가속 모델를 사용 하 여 모델을 [FPGAs (프로그래밍할 수 있는 문)](concept-accelerate-with-fpgas.md) 에 웹 서비스로 배포할 수 있습니다. FPGAs를 사용 하면 단일 일괄 처리 크기를 사용 하는 경우에도 매우 짧은 대기 시간 유추가 가능 합니다. 유추 또는 모델 점수 매기기는 배포 된 모델이 예측에 사용 되는 단계 이며 가장 일반적으로 프로덕션 데이터에 사용 됩니다.

현재 사용 가능한 모델은 다음과 같습니다.
  - ResNet 50
  - ResNet 152
  - DenseNet-121
  - VGG-16
  - SSD-VGG

FPGAs는 다음 Azure 지역에서 사용할 수 있습니다.
  - East US
  - 동남아시아
  - 유럽 서부
  - 미국 서부 2

> [!IMPORTANT]
> 대기 시간 및 처리량을 최적화 하기 위해 FPGA 모델에 데이터를 보내는 클라이언트는 위의 영역 중 하나 (모델을 배포한 항목)에 있어야 합니다.

## <a name="prerequisites"></a>필수 구성 요소

- Azure 구독.  계정이 없는 경우 시작 하기 전에 무료 계정을 만듭니다. [Azure Machine Learning Service의 평가판 또는 유료 버전](https://aka.ms/AMLFree)을 지금 사용해 보세요.

- FPGA 할당량입니다. Azure CLI를 사용 하 여 할당량이 있는지 여부를 확인 합니다.

    ```shell
    az vm list-usage --location "eastus" -o table --query "[?localName=='Standard PBS Family vCPUs']"
    ```

    > [!TIP]
    > 가능한 다른 위치 ``southeastasia``는, ``westeurope``및 ``westus2``입니다.

    명령은 다음과 유사한 텍스트를 반환 합니다.

    ```text
    CurrentValue    Limit    LocalName
    --------------  -------  -------------------------
    0               6        Standard PBS Family vCPUs
    ```

    __Currentvalue__아래에 6 개 이상의 vcpus가 있는지 확인 합니다.

    할당량이 없는 경우에서 [https://aka.ms/accelerateAI](https://aka.ms/accelerateAI)요청을 제출 합니다.

- Azure Machine Learning 서비스 작업 영역 및 Python용 Azure Machine Learning SDK가 설치되어 있어야 합니다. 자세한 내용은 [작업 영역 만들기](how-to-manage-workspace.md)를 참조 하세요.
 
- 하드웨어 가속 모델용 Python SDK:

    ```shell
    pip install --upgrade azureml-accel-models
    ```

## <a name="sample-notebooks"></a>샘플 Notebook

편의를 위해 [샘플 노트북](https://aka.ms/aml-accel-models-notebooks) 은 아래 예제와 다른 예제에서 사용할 수 있습니다.

## <a name="create-and-containerize-your-model"></a>모델 만들기 및 컨테이너 화

이 문서에서는 TensorFlow 그래프를 만들어 입력 이미지를 전처리 하 고, FPGA에서 ResNet 50을 사용 하 여 featurizer 만든 후 ImageNet 데이터 집합에 대해 학습 된 분류자를 통해 기능을 실행 하는 방법을 설명 합니다.

지침에 따라 다음을 수행합니다.

* TensorFlow 모델 정의
* 모델 변환
* 모델 배포
* 배포된 모델 사용
* 배포된 서비스 삭제

### <a name="load-azure-ml-workspace"></a>Azure ML 작업 영역 로드

Azure ML 작업 영역을 로드 합니다.

```python
import os
import tensorflow as tf

from azureml.core import Workspace

ws = Workspace.from_config()
print(ws.name, ws.resource_group, ws.location, ws.subscription_id, sep='\n')
```

### <a name="preprocess-image"></a>이미지 전처리

웹 서비스에 대 한 입력은 JPEG 이미지입니다.  첫 번째 단계는 JPEG 이미지를 디코딩하고 전처리 하는 것입니다.  JPEG 이미지는 문자열로 처리 되 고 결과는 tensors 이며,이는 ResNet 50 모델에 대 한 입력입니다.

```python
# Input images as a two-dimensional tensor containing an arbitrary number of images represented a strings
import azureml.accel.models.utils as utils
tf.reset_default_graph()

in_images = tf.placeholder(tf.string)
image_tensors = utils.preprocess_array(in_images)
print(image_tensors.shape)
```

### <a name="load-featurizer"></a>Load featurizer

모델을 초기화하고 기능화기로 사용할 ResNet50의 양자화 버전에 대한 TensorFlow 검사점을 다운로드합니다.  다른 심층 신경망을 가져오면 아래 코드 조각에서 "QuantizedResnet50"를으로 바꿀 수 있습니다.

- QuantizedResnet152
- QuantizedVgg16
- Densenet121

```python
from azureml.accel.models import QuantizedResnet50
save_path = os.path.expanduser('~/models')
model_graph = QuantizedResnet50(save_path, is_frozen=True)
feature_tensor = model_graph.import_graph_def(image_tensors)
print(model_graph.version)
print(feature_tensor.name)
print(feature_tensor.shape)
```

### <a name="add-classifier"></a>분류자 추가

이 분류자는 ImageNet 데이터 집합을 기반으로 학습되었습니다.  사용자 지정 가중치에 대 한 전송 학습 및 학습 예제는 [샘플 노트북](https://aka.ms/aml-notebooks)집합에서 사용할 수 있습니다.

```python
classifier_output = model_graph.get_default_classifier(feature_tensor)
print(classifier_output)
```

### <a name="save-the-model"></a>모델 저장

이제 전처리기, ResNet 50 featurizer 및 분류자가 로드 되었으므로 그래프 및 연관 된 변수를 모델로 저장 합니다.

```python
model_name = "resnet50"
model_save_path = os.path.join(save_path, model_name)
print("Saving model in {}".format(model_save_path))

with tf.Session() as sess:
    model_graph.restore_weights(sess)
    tf.saved_model.simple_save(sess, model_save_path,
                               inputs={'images': in_images},
                               outputs={'output_alias': classifier_output})
```

### <a name="save-input-and-output-tensors"></a>입력 및 출력 tensors 저장
전처리 및 분류자 단계 중에 만들어진 입력 및 출력 tensors 모델 변환 및 유추에 필요 합니다.

```python
input_tensors = in_images.name
output_tensors = classifier_output.name

print(input_tensors)
print(output_tensors)
```

> [!IMPORTANT]
> 모델 변환 및 유추 요청에 필요 하므로 입력 및 출력 tensors을 저장 합니다.

사용 가능한 모델 및 해당 기본 분류자 출력 tensors은 아래와 같습니다. 기본 분류자를 사용 하는 경우 유추에 사용 합니다.

+ Resnet50, QuantizedResnet50
  ```python
  output_tensors = "classifier_1/resnet_v1_50/predictions/Softmax:0"
  ```
+ Resnet152, QuantizedResnet152
  ```python
  output_tensors = "classifier/resnet_v1_152/predictions/Softmax:0"
  ```
+ Densenet121, QuantizedDensenet121
  ```python
  output_tensors = "classifier/densenet121/predictions/Softmax:0"
  ```
+ Vgg16, QuantizedVgg16
  ```python
  output_tensors = "classifier/vgg_16/fc8/squeezed:0"
  ```
+ SsdVgg, QuantizedSsdVgg
  ```python
  output_tensors = ['ssd_300_vgg/block4_box/Reshape_1:0', 'ssd_300_vgg/block7_box/Reshape_1:0', 'ssd_300_vgg/block8_box/Reshape_1:0', 'ssd_300_vgg/block9_box/Reshape_1:0', 'ssd_300_vgg/block10_box/Reshape_1:0', 'ssd_300_vgg/block11_box/Reshape_1:0', 'ssd_300_vgg/block4_box/Reshape:0', 'ssd_300_vgg/block7_box/Reshape:0', 'ssd_300_vgg/block8_box/Reshape:0', 'ssd_300_vgg/block9_box/Reshape:0', 'ssd_300_vgg/block10_box/Reshape:0', 'ssd_300_vgg/block11_box/Reshape:0']
  ```

### <a name="register-model"></a>모델 등록

만든 모델을 [등록](./concept-model-management-and-deployment.md) 합니다.  모델에 대 한 태그 및 기타 메타 데이터를 추가 하면 학습 된 모델을 추적 하는 데 도움이 됩니다.

```python
from azureml.core.model import Model

registered_model = Model.register(workspace=ws,
                                  model_path=model_save_path,
                                  model_name=model_name)

print("Successfully registered: ", registered_model.name,
      registered_model.description, registered_model.version, sep='\t')
```

이미 모델을 등록 하 고 로드 하려면 모델을 검색할 수 있습니다.

```python
from azureml.core.model import Model
model_name = "resnet50"
# By default, the latest version is retrieved. You can specify the version, i.e. version=1
registered_model = Model(ws, name="resnet50")
print(registered_model.name, registered_model.description,
      registered_model.version, sep='\t')
```

### <a name="convert-model"></a>모델 변환

TensorFlow graph를[Onnx](https://onnx.ai/)(Open 신경망 Exchange format)로 변환 합니다.  입력 및 출력 tensors의 이름을 제공 해야 하며,이 이름은 웹 서비스를 사용할 때 클라이언트에서 사용 됩니다.

```python
from azureml.accel import AccelOnnxConverter

convert_request = AccelOnnxConverter.convert_tf_model(
    ws, registered_model, input_tensors, output_tensors)

# If it fails, you can run wait_for_completion again with show_output=True.
convert_request.wait_for_completion(show_output=False)

# If the above call succeeded, get the converted model
converted_model = convert_request.result
print("\nSuccessfully converted: ", converted_model.name, converted_model.url, converted_model.version,
      converted_model.id, converted_model.created_time, '\n')
```

### <a name="create-docker-image"></a>Docker 이미지 만들기

변환 된 모델 및 모든 종속성이 Docker 이미지에 추가 됩니다.  그런 다음이 Docker 이미지를 배포 하 고 인스턴스화할 수 있습니다.  지원 되는 배포 대상에는 클라우드 또는 [Azure Data Box Edge](https://docs.microsoft.com/azure/databox-online/data-box-edge-overview)와 같은에 지 장치에 있는 AKS 포함 됩니다.  등록 된 Docker 이미지에 대 한 태그 및 설명을 추가할 수도 있습니다.

```python
from azureml.core.image import Image
from azureml.accel import AccelContainerImage

image_config = AccelContainerImage.image_configuration()
# Image name must be lowercase
image_name = "{}-image".format(model_name)

image = Image.create(name=image_name,
                     models=[converted_model],
                     image_config=image_config,
                     workspace=ws)
image.wait_for_creation(show_output=False)
```

태그로 이미지를 나열 하 고 디버깅에 대 한 자세한 로그를 가져옵니다.

```python
for i in Image.list(workspace=ws):
    print('{}(v.{} [{}]) stored at {} with build log {}'.format(
        i.name, i.version, i.creation_state, i.image_location, i.image_build_log_uri))
```

## <a name="model-deployment"></a>모델 배포

### <a name="deploy-to-the-cloud"></a>클라우드에 배포

모델을 확장성이 뛰어난 프로덕션 웹 서비스로 배포하려면 AKS(Azure Kubernetes Service)를 사용합니다. Azure Machine Learning SDK, CLI 또는 Azure Portal를 사용 하 여 새 항목을 만들 수 있습니다.

```python
from azureml.core.compute import AksCompute, ComputeTarget

# Specify the Standard_PB6s Azure VM
prov_config = AksCompute.provisioning_configuration(vm_size="Standard_PB6s",
                                                    agent_count=1)

aks_name = 'my-aks-cluster'
# Create the cluster
aks_target = ComputeTarget.create(workspace=ws,
                                  name=aks_name,
                                  provisioning_configuration=prov_config)
```

AKS 배포는 약 15 분 정도 걸릴 수 있습니다.  배포가 성공 했는지 확인 합니다.

```python
aks_target.wait_for_completion(show_output=True)
print(aks_target.provisioning_state)
print(aks_target.provisioning_errors)
```

AKS 클러스터에 컨테이너를 배포 합니다.
```python
from azureml.core.webservice import Webservice, AksWebservice

# For this deployment, set the web service configuration without enabling auto-scaling or authentication for testing
aks_config = AksWebservice.deploy_configuration(autoscale_enabled=False,
                                                num_replicas=1,
                                                auth_enabled=False)

aks_service_name = 'my-aks-service'

aks_service = Webservice.deploy_from_image(workspace=ws,
                                           name=aks_service_name,
                                           image=image,
                                           deployment_config=aks_config,
                                           deployment_target=aks_target)
aks_service.wait_for_deployment(show_output=True)
```

#### <a name="test-the-cloud-service"></a>클라우드 서비스 테스트
Docker 이미지는 gRPC와 "predict" API를 제공 하는 TensorFlow을 지원 합니다.  샘플 클라이언트를 사용 하 여 Docker 이미지를 호출 하 여 모델에서 예측을 가져옵니다.  샘플 클라이언트 코드를 사용할 수 있습니다.
- [Python](https://github.com/Azure/aml-real-time-ai/blob/master/pythonlib/amlrealtimeai/client.py)
- [C#](https://github.com/Azure/aml-real-time-ai/blob/master/sample-clients/csharp)

TensorFlow 서비스를 사용 하려는 경우 [샘플 클라이언트를 다운로드할](https://www.tensorflow.org/serving/setup)수 있습니다.

```python
# Using the grpc client in Azure ML Accelerated Models SDK package
from azureml.accel import PredictionClient

address = aks_service.scoring_uri
ssl_enabled = address.startswith("https")
address = address[address.find('/')+2:].strip('/')
port = 443 if ssl_enabled else 80

# Initialize AzureML Accelerated Models client
client = PredictionClient(address=address,
                          port=port,
                          use_ssl=ssl_enabled,
                          service_name=aks_service.name)
```

이 분류자는 [Imagenet](http://www.image-net.org/) 데이터 집합에 대해 학습 되었으므로 클래스를 사람이 읽을 수 있는 레이블에 매핑합니다.

```python
import requests
classes_entries = requests.get(
    "https://raw.githubusercontent.com/Lasagne/Recipes/master/examples/resnet50/imagenet_classes.txt").text.splitlines()

# Score image with input and output tensor names
results = client.score_file(path="./snowleopardgaze.jpg",
                            input_name=input_tensors,
                            outputs=output_tensors)

# map results [class_id] => [confidence]
results = enumerate(results)
# sort results by confidence
sorted_results = sorted(results, key=lambda x: x[1], reverse=True)
# print top 5 results
for top in sorted_results[:5]:
    print(classes_entries[top[0]], 'confidence:', top[1])
```

### <a name="clean-up-the-service"></a>서비스 정리
웹 서비스, 이미지 및 모델을 삭제 합니다 (종속성이 있으므로이 순서 대로 수행 해야 함).

```python
aks_service.delete()
aks_target.delete()
image.delete()
registered_model.delete()
converted_model.delete()
```

## <a name="deploy-to-a-local-edge-server"></a>로컬에 지 서버에 배포

모든 [Azure Data Box Edge 장치](https://docs.microsoft.com/azure/databox-online/data-box-edge-overview
) 에는 모델을 실행 하기 위한 FPGA이 포함 되어 있습니다.  한 번에 하나의 모델만 FPGA에서 실행할 수 있습니다.  다른 모델을 실행 하려면 새 컨테이너를 배포 하기만 하면 됩니다. 지침 및 샘플 코드는 [이 Azure 샘플](https://github.com/Azure-Samples/aml-hardware-accelerated-models)에서 찾을 수 있습니다.

## <a name="secure-fpga-web-services"></a>보안 FPGA 웹 서비스

FPGA 웹 서비스 보안에 대한 자세한 내용은 [웹 서비스 보호](how-to-secure-web-service.md) 문서를 참조하세요.

## <a name="pbs-family-vms"></a>PBS 제품군 Vm

Azure Vm의 PBS 패밀리에는 Intel Arria 10 FPGAs 포함 됩니다.  Azure 할당량 할당을 확인 하는 경우 "표준 PBS 제품군 vCPUs"로 표시 됩니다.  PB6 VM에는 6 개의 vCPUs와 하나의 FPGA가 있으며, FPGA에 모델을 배포 하는 과정에서 Azure ML에 의해 자동으로 프로 비전 됩니다.  Azure ML 에서만 사용 되며 임의의 bitstreams을 실행할 수 없습니다.  예를 들어 bitstreams을 사용 하 여 FPGA을 플래시 하 여 암호화, 인코딩 등을 수행할 수 없습니다. 
