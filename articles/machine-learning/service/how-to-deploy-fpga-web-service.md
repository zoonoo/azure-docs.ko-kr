---
title: FPGA에 모델 배포
titleSuffix: Azure Machine Learning service
description: 매우 짧은 대기 시간 유추를 위한 Azure Machine Learning 서비스를 사용 하 여 FPGA에서 실행 되는 모델을 사용 하 여 웹 서비스를 배포 하는 방법에 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: larryfr
ms.author: tedway
author: tedway
ms.date: 05/02/2019
ms.custom: seodec18
ms.openlocfilehash: 511333342371e18f75b48b60486705571353ee13
ms.sourcegitcommit: 4cdd4b65ddbd3261967cdcd6bc4adf46b4b49b01
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/06/2019
ms.locfileid: "66735061"
---
# <a name="deploy-a-model-as-a-web-service-on-an-fpga-with-azure-machine-learning-service"></a>Azure Machine Learning 서비스를 사용하여 FPGA에 웹 서비스로 모델 배포

모델을 웹 서비스로 배포할 수 있습니다 [field-programmable gate array (Fpga)](concept-accelerate-with-fpgas.md) Azure Machine Learning 하드웨어 가속 모델을 사용 하 여 합니다. Fpga를 사용 하 여 단일 일괄 처리 크기로 대기 시간이 엄청나게 짧은 유추를 제공 합니다. 모델 점수 매기기 또는 유추 하는 단계 프로덕션 데이터에 가장 일반적으로 예측에 대 한 배포 된 모델이 사용 되는 위치입니다.

현재 사용 가능한 모델은 다음과 같습니다.
  - ResNet 50
  - ResNet 152
  - DenseNet-121
  - VGG-16
  - SSD-VGG

Fpga는 이러한 Azure 지역에서 사용할 수 있습니다.
  - 미국 동부
  - 동남아시아
  - 서유럽
  - 미국 서부 2

> [!IMPORTANT]
> 대기 시간 및 처리량을 최적화 하려면 FPGA 모델에 데이터를 보내는 클라이언트 (모델을 배포한 하나) 위에 지역 중 하나에 있어야 합니다.

## <a name="prerequisites"></a>필수 조건

- Azure 구독.  하나 없으면 먼저 무료 계정을 만듭니다. [Azure Machine Learning Service의 평가판 또는 유료 버전](https://aka.ms/AMLFree)을 지금 사용해 보세요.

- FPGA 할당량입니다.  Azure CLI를 사용 하 여 할당량이 있는지 확인 합니다.
    ```shell
    az vm list-usage --location "eastus" -o table
    ```

    다른 위치는 ``southeastasia``, ``westeurope``, 및 ``westus2``합니다.

    "Name" 열 아래의 "표준 PBS 제품군 Vcpu"에 대 한 확인 하 고 "CurrentValue."에서 6 개 이상의 Vcpu가 있는지 확인

    할당량이 없는 경우 다음 요청 양식을 제출 [여기](https://aka.ms/accelerateAI)합니다.

- Azure Machine Learning 서비스 작업 영역 및 Python용 Azure Machine Learning SDK가 설치되어 있어야 합니다. [개발 환경 구성 방법](how-to-configure-environment.md) 문서를 사용하여 이러한 필수 구성 요소를 충족하는 방법을 알아보세요.
 
- 하드웨어 가속 모델에 대 한 Python SDK:

    ```shell
    pip install --upgrade azureml-accel-models
    ```

## <a name="sample-notebooks"></a>샘플 Notebook

사용자 편의 위해 [샘플 notebook](https://aka.ms/aml-accel-models-notebooks) 아래 예제에서는 다른 예제를 사용할 수 있습니다.

## <a name="create-and-containerize-your-model"></a>만들고 모델을 컨테이너 화

이 문서는 입력된 이미지 전처리, ResNet 50을 FPGA에 사용 하 여을 featurizer 있도록 TensorFlow 그래프를 만드는 방법에 설명 하 고 ImageNet 데이터 집합에서 학습 된 분류자를 통해 기능을 실행 합니다.

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
print(ws.name, ws.resource_group, ws.location, ws.subscription_id, sep = '\n')
```

### <a name="preprocess-image"></a>이미지 전처리

웹 서비스에 대 한 입력은 JPEG 이미지.  첫 번째 단계는 JPEG 이미지 디코딩 및이 전처리 하는 것입니다.  문자열 및 결과 tensors ResNet 50 모델에 입력 되는 JPEG 이미지 처리 됩니다.

```python
# Input images as a two-dimensional tensor containing an arbitrary number of images represented a strings
import azureml.accel.models.utils as utils
tf.reset_default_graph()

in_images = tf.placeholder(tf.string)
image_tensors = utils.preprocess_array(in_images)
print(image_tensors.shape)
```

### <a name="load-featurizer"></a>Featurizer 로드

모델을 초기화하고 기능화기로 사용할 ResNet50의 양자화 버전에 대한 TensorFlow 검사점을 다운로드합니다.  다른 심층 신경망을 가져와서 사용 하 여 아래 코드 조각에서 "QuantizedResnet50"를 바꿀 수 있습니다.

- QuantizedResnet152
- QuantizedVgg16
- Densenet121

```python
from azureml.accel.models import QuantizedResnet50
save_path = os.path.expanduser('~/models')
model_graph = QuantizedResnet50(save_path, is_frozen = True)
feature_tensor = model_graph.import_graph_def(image_tensors)
print(model_graph.version)
print(feature_tensor.name)
print(feature_tensor.shape)
```

### <a name="add-classifier"></a>분류자를 추가 합니다.

이 분류자는 ImageNet 데이터 집합을 기반으로 학습되었습니다.  학습 하 고 사용자 지정된에 가중치를 학습 전송에 대 한 예제 집합에서 사용할 수 있는 [샘플 notebook](https://aka.ms/aml-notebooks)합니다.

```python
classifier_output = model_graph.get_default_classifier(feature_tensor)
print(classifier_output)
```

### <a name="save-the-model"></a>모델 저장

전처리기, ResNet 50 featurizer 및 분류자 로드 된 경우 했으므로 모델로 그래프와 연결 된 변수를 저장 합니다.

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
전처리 및 분류자 단계 중 생성 된 입력 및 출력 tensors 모델 변환 및 유추에 필요 합니다.

```python
input_tensors = in_images.name
output_tensors = classifier_output.name

print(input_tensors)
print(output_tensors)
```

> [!IMPORTANT]
> 입력을 저장 하 고 모델 변환 및 유추 요청에 대 한 필요 하기 때문에 tensors를 출력 합니다.

사용 가능한 모델 및 해당 기본 분류자는 양만큼 유추에 대 한 기본 분류자를 사용 하는 경우는 아래 tensors는 출력입니다.

+ Resnet50, QuantizedResnet50 ``
output_tensors = "classifier_1/resnet_v1_50/predictions/Softmax:0"
``
+ Resnet152, QuantizedResnet152 ``
output_tensors = "classifier/resnet_v1_152/predictions/Softmax:0"
``
+ Densenet121, QuantizedDensenet121 ``
output_tensors = "classifier/densenet121/predictions/Softmax:0"
``
+ Vgg16, QuantizedVgg16 ``
output_tensors = "classifier/vgg_16/fc8/squeezed:0"
``
+ SsdVgg, QuantizedSsdVgg ``
output_tensors = ['ssd_300_vgg/block4_box/Reshape_1:0', 'ssd_300_vgg/block7_box/Reshape_1:0', 'ssd_300_vgg/block8_box/Reshape_1:0', 'ssd_300_vgg/block9_box/Reshape_1:0', 'ssd_300_vgg/block10_box/Reshape_1:0', 'ssd_300_vgg/block11_box/Reshape_1:0', 'ssd_300_vgg/block4_box/Reshape:0', 'ssd_300_vgg/block7_box/Reshape:0', 'ssd_300_vgg/block8_box/Reshape:0', 'ssd_300_vgg/block9_box/Reshape:0', 'ssd_300_vgg/block10_box/Reshape:0', 'ssd_300_vgg/block11_box/Reshape:0']
``

### <a name="register-model"></a>모델 등록

[등록](./concept-model-management-and-deployment.md) 만든 모델입니다.  태그 및 기타 메타 데이터 모델에 대 한 추가 학습 된 모델을 추적 하는 데 도움이 됩니다.

```python
from azureml.core.model import Model

registered_model = Model.register(workspace = ws,
                                  model_path = model_save_path,
                                  model_name = model_name)

print("Successfully registered: ", registered_model.name, registered_model.description, registered_model.version, sep = '\t')
```

이미 등록 된 모델을 로드 하려고 하는 경우에 검색할 수 있습니다.

```python
from azureml.core.model import Model
model_name = "resnet50"
# By default, the latest version is retrieved. You can specify the version, i.e. version=1
registered_model = Model(ws, name="resnet50")
print(registered_model.name, registered_model.description, registered_model.version, sep = '\t')
```

### <a name="convert-model"></a>모델 변환

TensorFlow 그래프를 열고 신경망 네트워크 교환 형식으로 변환 ([ONNX](https://onnx.ai/)).  입력 및 출력 tensors의 이름을 제공 해야 하 고 웹 서비스를 사용 하는 경우 이러한 이름이 클라이언트에서 사용 됩니다.

```python
from azureml.accel import AccelOnnxConverter

convert_request = AccelOnnxConverter.convert_tf_model(ws, registered_model, input_tensors, output_tensors)

# If it fails, you can run wait_for_completion again with show_output=True.
convert_request.wait_for_completion(show_output = False)

# If the above call succeeded, get the converted model
converted_model = convert_request.result
print("\nSuccessfully converted: ", converted_model.name, converted_model.url, converted_model.version, 
      converted_model.id, converted_model.created_time, '\n')
```

### <a name="create-docker-image"></a>Docker 이미지 만들기

변환 된 모델 및 모든 종속성을 Docker 이미지에 추가 됩니다.  그런 다음이 Docker 이미지 배포 고 인스턴스화할 수 있습니다.  지원 되는 배포 대상 클라우드 또는 지 장치에서와 같은 AKS을 포함할 [Azure 데이터 가장자리가 상자의](https://docs.microsoft.com/azure/databox-online/data-box-edge-overview)합니다.  등록 된 Docker 이미지에 대 한 태그 및 설명을 추가할 수 있습니다.

```python
from azureml.core.image import Image
from azureml.accel import AccelContainerImage

image_config = AccelContainerImage.image_configuration()
# Image name must be lowercase
image_name = "{}-image".format(model_name)

image = Image.create(name = image_name,
                     models = [converted_model],
                     image_config = image_config, 
                     workspace = ws)
image.wait_for_creation(show_output = False)
```

태그로 이미지를 나열 하 고 모든 디버깅에 대 한 자세한 로그를 가져옵니다.

```python
for i in Image.list(workspace = ws):
    print('{}(v.{} [{}]) stored at {} with build log {}'.format(i.name, i.version, i.creation_state, i.image_location, i.image_build_log_uri))
```

## <a name="model-deployment"></a>모델 배포

### <a name="deploy-to-the-cloud"></a>클라우드로 배포

모델을 확장성이 뛰어난 프로덕션 웹 서비스로 배포하려면 AKS(Azure Kubernetes Service)를 사용합니다. Azure Machine Learning SDK, CLI 또는 Azure portal을 사용 하 여 새로 만들 수 있습니다.

```python
from azureml.core.compute import AksCompute, ComputeTarget

# Specify the Standard_PB6s Azure VM
prov_config = AksCompute.provisioning_configuration(vm_size = "Standard_PB6s",
                                                    agent_count = 1)

aks_name = 'my-aks-cluster'
# Create the cluster
aks_target = ComputeTarget.create(workspace = ws, 
                                  name = aks_name, 
                                  provisioning_configuration = prov_config)
```

AKS 배포는 15 분 정도 걸릴 수 있습니다.  배포 성공 여부를 확인 합니다.

```python
aks_target.wait_for_completion(show_output = True)
print(aks_target.provisioning_state)
print(aks_target.provisioning_errors)
```

AKS 클러스터에 컨테이너를 배포 합니다.
```python
from azureml.core.webservice import Webservice, AksWebservice

# For this deployment, set the web service configuration without enabling auto-scaling or authentication for testing
aks_config = AksWebservice.deploy_configuration(autoscale_enabled=False,
                                                num_replicas=1,
                                                auth_enabled = False)

aks_service_name ='my-aks-service'

aks_service = Webservice.deploy_from_image(workspace = ws,
                                           name = aks_service_name,
                                           image = image,
                                           deployment_config = aks_config,
                                           deployment_target = aks_target)
aks_service.wait_for_deployment(show_output = True)
```

#### <a name="test-the-cloud-service"></a>클라우드 서비스를 테스트 합니다.
Docker 이미지는 gRPC 및 TensorFlow 제공 "예측" API를 지원 합니다.  모델에서 예측을 구하려면 Docker 이미지를 호출 하는 샘플 클라이언트를 사용 합니다.  샘플 클라이언트 코드는 사용할 수 있습니다.
- [Python](https://github.com/Azure/aml-real-time-ai/blob/master/pythonlib/amlrealtimeai/client.py)
- [C#](https://github.com/Azure/aml-real-time-ai/blob/master/sample-clients/csharp)

TensorFlow 서비스를 사용 하려는 경우 [샘플 클라이언트 다운로드](https://www.tensorflow.org/serving/setup)합니다.

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

이 분류자에 교육 받았습니다 있으므로 합니다 [ImageNet](http://www.image-net.org/) 데이터 집합, 사람이 읽을 수 있는 레이블로 클래스 매핑.

```python
import requests
classes_entries = requests.get("https://raw.githubusercontent.com/Lasagne/Recipes/master/examples/resnet50/imagenet_classes.txt").text.splitlines()

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
웹 서비스, 이미지 및 모델 (해야이 순서로 종속성 때문)를 삭제 합니다.

```python
aks_service.delete()
aks_target.delete()
image.delete()
registered_model.delete()
converted_model.delete()
```

## <a name="deploy-to-a-local-edge-server"></a>로컬에 지 서버에 배포

모든 [Azure 데이터 가장자리가 상자의 장치](https://docs.microsoft.com/azure/databox-online/data-box-edge-overview
) 모델을 실행 하기 위한 FPGA를 포함 합니다.  하나의 모델을 FPGA에 한 번에 실행할 수 있습니다.  다른 모델을 실행 하려면 새 컨테이너를 배포 하기만 하면 됩니다. 지침 및 샘플 코드에서 확인할 수 있습니다 [이 Azure 샘플](https://github.com/Azure-Samples/aml-hardware-accelerated-models)합니다.

## <a name="secure-fpga-web-services"></a>보안 FPGA 웹 서비스

FPGA 웹 서비스 보안에 대한 자세한 내용은 [웹 서비스 보호](how-to-secure-web-service.md) 문서를 참조하세요.

## <a name="pbs-family-vms"></a>PBS 제품군 Vm

Azure Vm의 PBS 제품군 Intel Arria 10 Fpga를 포함합니다.  "표준 PBS 제품군 Vcpu"로 표시 됩니다 할당 된 Azure 할당량을 선택 하면 됩니다.  PB6 VM Vcpu 6 개 있고 하나의 FPGA 및 FPGA에 모델을 배포 하는 일환으로 Azure 기계 학습에서 자동으로 프로 비전 됩니다.  Azure ML에만 사용 하 고 임의의 bitstreams 실행할 수 없습니다.  예를 들어, 암호화, 인코딩 등을 위해 bitstreams 사용 하 여 FPGA 플래시 수 없습니다. 
