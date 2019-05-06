---
title: FPGA에 모델 배포
titleSuffix: Azure Machine Learning service
description: 대기 시간이 매우 짧은 추론을 위해 Azure Machine Learning 서비스를 사용하여 FPGA에서 실행되는 모델로 웹 서비스를 배포하는 방법을 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: larryfr
ms.author: tedway
author: tedway
ms.date: 05/02/2019
ms.custom: seodec18
ms.openlocfilehash: cfe21d2119b92665c5950d792dec6500257c6316
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/02/2019
ms.locfileid: "65024176"
---
# <a name="deploy-a-model-as-a-web-service-on-an-fpga-with-azure-machine-learning-service"></a>Azure Machine Learning 서비스를 사용하여 FPGA에 웹 서비스로 모델 배포

모델을 웹 서비스로 배포할 수 있습니다 [field-programmable gate array (Fpga)](concept-accelerate-with-fpgas.md) Azure Machine Learning 하드웨어 가속 모델을 사용 하 여 합니다. FPGA를 사용하면 단일 배치 크기로도 대기 시간이 엄청나게 짧은 추론이 가능합니다.

현재 사용 가능한 모델은 다음과 같습니다.
  - ResNet 50
  - ResNet 152
  - DenseNet-121
  - VGG-16
  - SSD-VGG

Fpga는 이러한 Azure 지역에서 사용할 수 있습니다.
  - 미국 동부
  - 미국 서부 2
  - 서유럽
  - 동남아시아

> [!IMPORTANT]
> 대기 시간 및 처리량을 최적화 하려면 FPGA 모델에 데이터를 보내는 클라이언트 (모델을 배포한 하나) 위에 지역 중 하나에 있어야 합니다.

## <a name="prerequisites"></a>필수 조건

- Azure 구독이 없는 경우 시작하기 전에 체험 계정을 만듭니다. [Azure Machine Learning Service의 평가판 또는 유료 버전](https://aka.ms/AMLFree)을 지금 사용해 보세요.

- Azure Machine Learning 서비스 작업 영역 및 Python용 Azure Machine Learning SDK가 설치되어 있어야 합니다. [개발 환경 구성 방법](how-to-configure-environment.md) 문서를 사용하여 이러한 필수 구성 요소를 충족하는 방법을 알아보세요.
 
  - 하드웨어 가속 모델에 대 한 Python SDK를 설치 합니다.

    ```shell
    pip install --upgrade azureml-accel-models
    ```

## <a name="sample-notebooks"></a>샘플 Notebook

사용자 편의 위해 [샘플 notebook](https://aka.ms/aml-notebooks) 예를 들어 아래와 다른 예제 외에도 사용할 수 있습니다.  가속화 된 모델에 대 한 방법으로-사용-azureml 및 배포 폴더에서 찾습니다.

## <a name="create-and-containerize-your-model"></a>만들고 모델을 컨테이너 화

이 문서는 입력된 이미지 전처리, ResNet 50을 FPGA에 사용 하 여을 featurizer 있도록 TensorFlow 그래프를 만드는 방법에 설명 하 고 ImageNet 데이터 집합에서 학습 된 분류자를 통해 기능을 실행 합니다.

지침에 따라 다음을 수행합니다.

* TensorFlow 모델 정의
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
model_def_path = os.path.join(save_path, model_name)
print("Saving model in {}".format(model_def_path))

with tf.Session() as sess:
    model_graph.restore_weights(sess)
    tf.saved_model.simple_save(sess, model_def_path,
                                   inputs={'images': in_images},
                                   outputs={'output_alias': classifier_output})
```

### <a name="register-model"></a>모델 등록

[등록](./concept-model-management-and-deployment.md) 만든 모델입니다.  태그 및 기타 메타 데이터 모델에 대 한 추가 학습 된 모델을 추적 하는 데 도움이 됩니다.

```python
from azureml.core.model import Model

registered_model = Model.register(workspace = ws
                                  model_path = model_def_path,
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

TensorFlow 그래프를 열고 신경망 네트워크 교환 형식으로 변환 해야 합니다. ([ONNX](https://onnx.ai/)).  입력 및 출력 tensors의 이름을 제공 해야 하 고 웹 서비스를 사용 하는 경우 이러한 이름이 클라이언트에서 사용 됩니다.

```python
input_tensor = in_images.name
output_tensors = classifier_output.name

print(input_tensor)
print(output_tensors)


from azureml.accel.accel_onnx_converter import AccelOnnxConverter

convert_request = AccelOnnxConverter.convert_tf_model(ws, registered_model, input_tensor, output_tensors)
convert_request.wait_for_completion(show_output=True)

# If the above call succeeded, get the converted model
converted_model = convert_request.result
print(converted_model.name, converted_model.url, converted_model.version, converted_model.id,converted_model.created_time)
```

### <a name="create-docker-image"></a>Docker 이미지 만들기

변환 된 모델 및 모든 종속성을 Docker 이미지에 추가 됩니다.  이 Docker 이미지 수 다음 배포 하 고 클라우드 또는 지원 되는 edge 장치에서 같은 인스턴스화 [Azure 데이터 가장자리가 상자의](https://docs.microsoft.com/azure/databox-online/data-box-edge-overview)합니다.  등록 된 Docker 이미지에 대 한 태그 및 설명을 추가할 수 있습니다.

```python
from azureml.core.image import Image
from azureml.accel.accel_container_image import AccelContainerImage

image_config = AccelContainerImage.image_configuration()
image_name = "{}-image".format(model_name)

image = Image.create(name = image_name,
                     models = [converted_model],
                     image_config = image_config, 
                     workspace = ws)


image.wait_for_creation(show_output = True)
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
# Use the default configuration (can also provide parameters to customize)
prov_config = AksCompute.provisioning_configuration()

aks_name = 'my-aks-9' 
# Create the cluster
aks_target = ComputeTarget.create(workspace = ws, 
                                  name = aks_name, 
                                  provisioning_configuration = prov_config)

%%time
aks_target.wait_for_completion(show_output = True)
print(aks_target.provisioning_state)
print(aks_target.provisioning_errors)

#Set the web service configuration (using default here)
aks_config = AksWebservice.deploy_configuration()

%%time
aks_service_name ='aks-service-1'

aks_service = Webservice.deploy_from_image(workspace = ws, 
                                           name = aks_service_name,
                                           image = image,
                                           deployment_config = aks_config,
                                           deployment_target = aks_target)
aks_service.wait_for_deployment(show_output = True)
print(aks_service.state)
print(aks_service.scoring_uri)
```

#### <a name="test-the-cloud-service"></a>클라우드 서비스를 테스트 합니다.
Docker 이미지는 gRPC 및 TensorFlow 제공 "예측" API를 지원 합니다.  모델에서 예측을 구하려면 Docker 이미지를 호출 하는 샘플 클라이언트를 사용 합니다.  샘플 클라이언트 코드는 사용할 수 있습니다.
- [Python](https://github.com/Azure/aml-real-time-ai/blob/master/pythonlib/amlrealtimeai/client.py)
- [C#](https://github.com/Azure/aml-real-time-ai/blob/master/sample-clients/csharp)

TensorFlow 서비스를 사용 하려는 경우 [샘플 클라이언트 다운로드](https://www.tensorflow.org/serving/setup)합니다.

```python
import requests
classes_entries = requests.get("https://raw.githubusercontent.com/Lasagne/Recipes/master/examples/resnet50/imagenet_classes.txt").text.splitlines()

# Score image using input and output tensor names
results = client.score_file(path="./snowleopardgaze.jpg", 
                             input_name=input_tensor, 
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
image.delete()
registered_model.delete()
converted_model.delete()
```

## <a name="deploy-to-a-local-edge-server"></a>로컬에 지 서버에 배포

모든 [Azure 데이터 가장자리가 상자의 장치](https://docs.microsoft.com/azure/databox-online/data-box-edge-overview
) 모델을 실행 하기 위한 FPGA를 포함 합니다.  하나의 모델을 FPGA에 한 번에 실행할 수 있습니다.  다른 모델을 실행 하려면 새 컨테이너를 배포 하기만 하면 됩니다. 지침 및 샘플 코드에서 확인할 수 있습니다 [이 Azure 샘플](https://github.com/Azure-Samples/aml-hardware-accelerated-models)합니다.

## <a name="secure-fpga-web-services"></a>보안 FPGA 웹 서비스

FPGA 웹 서비스 보안에 대한 자세한 내용은 [웹 서비스 보호](how-to-secure-web-service.md) 문서를 참조하세요.