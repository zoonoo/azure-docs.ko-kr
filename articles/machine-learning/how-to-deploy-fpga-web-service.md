---
title: FPGA란 - 배포 방법
titleSuffix: Azure Machine Learning
description: 초저지연 추론을 위해 Azure 기계 학습을 사용하여 FPGA에서 실행되는 모델로 웹 서비스를 배포하는 방법을 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: larryfr
ms.author: jordane
author: jpe316
ms.date: 03/05/2020
ms.custom: seodec18
ms.openlocfilehash: 870f7b0ab0f1d7b247435cdbb74e21801b3b052a
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/13/2020
ms.locfileid: "81257184"
---
# <a name="what-are-field-programmable-gate-arrays-fpga-and-how-to-deploy"></a>현장 프로그래밍 가능한 게이트 어레이(FPGA)란 무엇이며 배포 방법
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

이 문서에서는 FPGA(필드 프로그래밍 가능 게이트 배열)에 대한 소개를 제공하고 Azure 기계 학습을 사용하여 모델을 Azure FPGA에 배포하는 방법을 보여 줍니다.

FPGA는 프로그래밍 가능한 논리 블록 배열과 재구성 가능한 상호 연결 계층 구조를 포함하고 있습니다. 제조 후 상호 연결을 통해 이러한 블록을 다양한 방법으로 구성할 수 있습니다. 다른 칩과 비교해서, FPGA는 프로그래밍 기능 및 성능 조합을 제공합니다.

## <a name="fpgas-vs-cpu-gpu-and-asic"></a>FPGA 대 CPU, GPU 및 ASIC

다음 다이어그램 및 표에서는 FPGA와 다른 프로세서를 비교해서 보여 줍니다.

![Azure 기계 학습 FPGA 비교 다이어그램](./media/how-to-deploy-fpga-web-service/azure-machine-learning-fpga-comparison.png)

|프로세서||Description|
|---|:-------:|------|
|애플리케이션 관련 집적 회로|ASIC|Google의 TPU(TensorFlow Processor Units) 같은 사용자 지정 회로는 가장 높은 효율성을 제공합니다. 이러한 회로는 변하는 요구 사항에 따라 재구성할 수 없습니다.|
|Field-programmable Gate Arrays|FPGA|Azure에서 사용할 수 있는 것과 같은 FPGA는 ASIC에 가까운 성능을 제공합니다. 또한 유연하고, 시간 경과에 따라 새 논리를 구현하기 위해 다시 구성할 수 있습니다.|
|그래픽 처리 장치|GPU|GPU는 AI 계산에 널리 사용되며, CPU보다 이미지 렌더링 속도가 빠른 병렬 처리 기능을 제공합니다.|
|중앙 처리 장치|CPU|범용 프로세서이며, 성능이 그래픽 및 비디오 처리에 적합하지 않습니다.|

Azure의 FPGA는 데이터 과학자와 개발자가 실시간 AI 계산을 가속화하는 데 사용하는 인텔의 FPGA 장치를 기반으로 합니다. 이 FPGA 지원 아키텍처는 강력한 성능, 유연성 및 확장성을 제공하며 Azure에서 사용할 수 있습니다.

FPGA를 통해 실시간 추론(또는 모델 채점) 요청에 대해 낮은 대기 시간을 달성할 수 있습니다. 비동기 요청(일괄 처리)이 필요하지 않습니다. 일괄 처리를 사용하면 더 많은 데이터를 처리해야 하므로 대기 시간이 길어질 수 있습니다. 신경 처리 장치의 구현에는 일괄 처리가 필요하지 않습니다. 따라서 CPU 및 GPU 프로세서에 비해 대기 시간이 여러 배 더 낮을 수 있습니다.

### <a name="reconfigurable-power"></a>재구성 가능한 전원
여러 형식의 기계 학습 모델에 대해 FPGA를 다시 구성할 수 있습니다. 이러한 유연성 덕분에 사용되는 최적의 숫자 정밀도 및 메모리 모델에 따라 애플리케이션을 가속화할 수 있습니다. FPGA는 다시 구성할 수 있으므로 빠르게 변화하는 AI 알고리즘의 요구 사항에 맞게 최신 상태를 유지할 수 있습니다.

## <a name="whats-supported-on-azure"></a>Azure에서 지원되는 내용
Microsoft Azure는 FPGA 부문에서 세계 최대의 클라우드 투자 규모를 자랑합니다. 이 FPGA 지원 하드웨어 아키텍처를 사용하면 학습된 신경망이 낮은 대기 시간으로 신속하게 실행됩니다. Azure는 FPGA 전체에서 미리 학습된 심층 신경망(DNN)을 병렬화하여 서비스를 확장할 수 있습니다. DNN은 전송 학습을 위한 심층 기능 기능화기로 미리 학습하거나 업데이트된 가중치를 사용하여 미세 조정할 수 있습니다.

Azure의 FPGA는 다음을 지원합니다.

+ 이미지 분류 및 인식 시나리오
+ 텐서플로우 배포(텐서플로우 1.x 필요)
+ Intel FPGA 하드웨어

이러한 DNN 모델은 현재 사용할 수 있습니다.
  - ResNet 50
  - ResNet 152
  - DenseNet-121
  - VGG-16
  - SSD-VGG

FPGA는 다음 Azure 리전에서 사용할 수 있습니다.
  - 미국 동부
  - 동남아시아
  - 서유럽
  - 미국 서부 2

> [!IMPORTANT]
> 대기 시간 및 처리량을 최적화하려면 클라이언트가 FPGA 모델로 데이터를 전송하는 경우 위의 지역 중 하나(모델을 배포한 지역)에 있어야 합니다.

**Azure VM의 PBS 제품군에는** 인텔 아리아 10 FPGA가 포함되어 있습니다. Azure 할당량 할당을 확인할 때 "표준 PBS 패밀리 vCPU"로 표시됩니다. PB6 VM에는 6개의 vCPU와 1개의 FPGA가 있으며, FPGA에 모델을 배포하는 과정에서 Azure ML에서 자동으로 프로비전됩니다. Azure ML에서만 사용되며 임의의 비트스트림을 실행할 수 없습니다. 예를 들어 암호화, 인코딩 등을 수행하는 비트스트림으로 FPGA를 플래시할 수 없습니다.

### <a name="scenarios-and-applications"></a>시나리오 및 애플리케이션

Azure FPGA는 Azure 기계 학습과 통합됩니다. Microsoft에서는 DNN 평가, Bing 검색 순위, SDN(소프트웨어 정의 네트워킹) 가속화에 FPGA를 사용하여 대기 시간을 줄이는 한편, CPU 리소스를 회수하여 다른 작업에 사용합니다.

다음 시나리오는 FPGA를 사용합니다.
+ [자동 광학 검사 시스템](https://blogs.microsoft.com/ai/build-2018-project-brainwave/)

+ [토지 커버 매핑](https://blogs.technet.microsoft.com/machinelearning/2018/05/29/how-to-use-fpgas-for-deep-learning-inference-to-perform-land-cover-mapping-on-terabytes-of-aerial-images/)

## <a name="example-deploy-models-on-fpgas"></a>예: FPGA에 모델 배포

Azure 기계 학습 하드웨어 가속 모델을 사용 하 고 FPGA에 웹 서비스로 모델을 배포할 수 있습니다. FPGA를 사용하면 단일 배치 크기에서도 매우 낮은 대기 시간 추론을 제공합니다. 추론 또는 모델 점수 매기기는 배포된 모델이 예측에 가장 일반적으로 사용되는 단계입니다.

### <a name="prerequisites"></a>사전 요구 사항

- Azure 구독  구독이 없으면 시작하기 전에 체험 계정을 만듭니다. 지금 [Azure Machine Learning 평가판 또는 유료 버전](https://aka.ms/AMLFree)을 사용해 보세요.

- FPGA 할당량. Azure CLI를 사용하여 할당량이 있는지 확인합니다.

    ```azurecli-interactive
    az vm list-usage --location "eastus" -o table --query "[?localName=='Standard PBS Family vCPUs']"
    ```

    > [!TIP]
    > 다른 가능한 위치는 ``southeastasia`` ``westeurope``에서 ``westus2``입니다.

    명령은 다음과 유사한 텍스트를 반환합니다.

    ```text
    CurrentValue    Limit    LocalName
    --------------  -------  -------------------------
    0               6        Standard PBS Family vCPUs
    ```

    __CurrentValue__에 6개 이상의 vCPU가 있는지 확인합니다.

    할당량이 없는 경우 에서 [https://aka.ms/accelerateAI](https://aka.ms/accelerateAI)요청을 제출합니다.

- Azure Machine Learning 작업 영역 및 Python용 Azure Machine Learning SDK가 설치되어 있어야 합니다. 자세한 내용은 [작업 영역 만들기를](how-to-manage-workspace.md)참조하십시오.
 
- 하드웨어 가속 모델용 Python SDK:

    ```bash
    pip install --upgrade azureml-accel-models[cpu]
    ```

## <a name="1-create-and-containerize-models"></a>1. 모델 생성 및 컨테이너화

이 문서에서는 입력 이미지를 미리 처리하고 FPGA에서 ResNet 50을 사용하여 위화기를 만든 다음 ImageNet 데이터 집합에서 학습된 분류자를 통해 기능을 실행하는 텐서플로우 그래프를 만드는 방법을 설명합니다.

지침에 따라 다음을 수행합니다.

* 텐서플로우 모델 정의
* 모델 변환
* 모델 배포
* 배포된 모델 사용
* 배포된 서비스 삭제

[Python용 Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)를 사용하여 서비스 정의를 만듭니다. 서비스 정의는 TensorFlow를 기반으로 그래프(입력, 기능화기 및 분류자) 파이프라인을 설명하는 파일입니다. 배포 명령은 자동으로 정의 및 그래프를 ZIP 파일로 압축하고, ZIP 파일을 Azure Blob Storage에 업로드합니다. DNN은 FPGA에서 실행되도록 이미 배포되었습니다.

### <a name="load-azure-machine-learning-workspace"></a>Azure 기계 학습 작업 영역 로드

Azure 기계 학습 작업 영역을 로드합니다.

```python
import os
import tensorflow as tf

from azureml.core import Workspace

ws = Workspace.from_config()
print(ws.name, ws.resource_group, ws.location, ws.subscription_id, sep='\n')
```

### <a name="preprocess-image"></a>이미지 전처리

웹 서비스에 대한 입력은 JPEG 이미지입니다.  첫 번째 단계는 JPEG 이미지를 디코딩하고 전처리하는 것입니다.  JPEG 이미지는 문자열로 처리되며 결과는 ResNet 50 모델에 대한 입력이 될 텐서입니다.

```python
# Input images as a two-dimensional tensor containing an arbitrary number of images represented a strings
import azureml.accel.models.utils as utils
tf.reset_default_graph()

in_images = tf.placeholder(tf.string)
image_tensors = utils.preprocess_array(in_images)
print(image_tensors.shape)
```

### <a name="load-featurizer"></a>하중 위화기

모델을 초기화하고 기능화기로 사용할 ResNet50의 양자화 버전에 대한 TensorFlow 검사점을 다운로드합니다.  아래 코드 스니펫에서 "QuantizedResnet50"을 다른 심층 신경망을 가져오는 것으로 바꿀 수 있습니다.

- 양자화된 레스넷152
- 양자화Vgg16
- 덴세넷121

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

이 분류자는 ImageNet 데이터 집합을 기반으로 학습되었습니다.  사용자 지정된 가중치를 전송 학습 및 교육예제로 [샘플 전자 필기장에서](https://aka.ms/aml-notebooks)확인할 수 있습니다.

```python
classifier_output = model_graph.get_default_classifier(feature_tensor)
print(classifier_output)
```

### <a name="save-the-model"></a>모델 저장

이제 전처리기, ResNet 50 위화기 및 분류기가 로드되었으므로 그래프 및 관련 변수를 모델로 저장합니다.

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

### <a name="save-input-and-output-tensors"></a>입력 및 출력 텐서 저장
전처리 및 분류기 단계에서 생성된 입력 및 출력 텐서가 모델 변환 및 추론에 필요합니다.

```python
input_tensors = in_images.name
output_tensors = classifier_output.name

print(input_tensors)
print(output_tensors)
```

> [!IMPORTANT]
> 모델 변환 및 추론 요청에 입력 및 출력 텐서가 필요하므로 입력 및 출력 텐서를 저장합니다.

사용 가능한 모델과 해당 기본 분류자 출력 텐서가 아래에 있으며, 기본 분류기를 사용한 경우 추론에 사용할 수 있습니다.

+ Resnet50, 양자화된Resnet50
  ```python
  output_tensors = "classifier_1/resnet_v1_50/predictions/Softmax:0"
  ```
+ Resnet152, 양자화된Resnet152
  ```python
  output_tensors = "classifier/resnet_v1_152/predictions/Softmax:0"
  ```
+ Densenet121, 양자화된덴스넷121
  ```python
  output_tensors = "classifier/densenet121/predictions/Softmax:0"
  ```
+ Vgg16, 양자화Vgg16
  ```python
  output_tensors = "classifier/vgg_16/fc8/squeezed:0"
  ```
+ SsdVgg, 양자화SsdVgg
  ```python
  output_tensors = ['ssd_300_vgg/block4_box/Reshape_1:0', 'ssd_300_vgg/block7_box/Reshape_1:0', 'ssd_300_vgg/block8_box/Reshape_1:0', 'ssd_300_vgg/block9_box/Reshape_1:0', 'ssd_300_vgg/block10_box/Reshape_1:0', 'ssd_300_vgg/block11_box/Reshape_1:0', 'ssd_300_vgg/block4_box/Reshape:0', 'ssd_300_vgg/block7_box/Reshape:0', 'ssd_300_vgg/block8_box/Reshape:0', 'ssd_300_vgg/block9_box/Reshape:0', 'ssd_300_vgg/block10_box/Reshape:0', 'ssd_300_vgg/block11_box/Reshape:0']
  ```

### <a name="register-model"></a>모델 등록

Azure Blob 저장소의 ZIP 파일과 함께 SDK를 사용하여 모델을 [등록합니다.](concept-model-management-and-deployment.md) 모델에 대한 태그 및 기타 메타데이터를 추가하면 학습된 모델을 추적하는 데 도움이 됩니다.

```python
from azureml.core.model import Model

registered_model = Model.register(workspace=ws,
                                  model_path=model_save_path,
                                  model_name=model_name)

print("Successfully registered: ", registered_model.name,
      registered_model.description, registered_model.version, sep='\t')
```

이미 모델을 등록하고 로드하려는 경우 모델을 검색할 수 있습니다.

```python
from azureml.core.model import Model
model_name = "resnet50"
# By default, the latest version is retrieved. You can specify the version, i.e. version=1
registered_model = Model(ws, name="resnet50")
print(registered_model.name, registered_model.description,
      registered_model.version, sep='\t')
```

### <a name="convert-model"></a>변환 모델

텐서플로우 그래프를 개방형 신경망 교환[형식(ONNX)으로](https://onnx.ai/)변환합니다.  입력 및 출력 텐서의 이름을 제공해야 하며 웹 서비스를 사용할 때 이러한 이름은 클라이언트에서 사용됩니다.

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

변환된 모델과 모든 종속성이 Docker 이미지에 추가됩니다.  그런 다음 이 Docker 이미지를 배포하고 인스턴스화할 수 있습니다.  지원되는 배포 대상에는 클라우드의 AKS 또는 [Azure 데이터 상자 가장자리와](https://docs.microsoft.com/azure/databox-online/data-box-edge-overview)같은 에지 장치가 포함됩니다.  등록된 Docker 이미지에 대한 태그와 설명을 추가할 수도 있습니다.

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

태그를 통해 이미지를 나열하고 디버깅에 대한 자세한 로그를 가져옵니다.

```python
for i in Image.list(workspace=ws):
    print('{}(v.{} [{}]) stored at {} with build log {}'.format(
        i.name, i.version, i.creation_state, i.image_location, i.image_build_log_uri))
```

## <a name="2-deploy-to-cloud-or-edge"></a>2. 클라우드 또는 에지에 배포

### <a name="deploy-to-the-cloud"></a>클라우드에 배포

모델을 확장성이 뛰어난 프로덕션 웹 서비스로 배포하려면 AKS(Azure Kubernetes Service)를 사용합니다. Azure 기계 학습 SDK, CLI 또는 [Azure 기계 학습 스튜디오를](https://ml.azure.com)사용하여 새 컴퓨터를 만들 수 있습니다.

```python
from azureml.core.compute import AksCompute, ComputeTarget

# Specify the Standard_PB6s Azure VM and location. Values for location may be "eastus", "southeastasia", "westeurope", or "westus2". If no value is specified, the default is "eastus".
prov_config = AksCompute.provisioning_configuration(vm_size = "Standard_PB6s",
                                                    agent_count = 1,
                                                    location = "eastus")

aks_name = 'my-aks-cluster'
# Create the cluster
aks_target = ComputeTarget.create(workspace=ws,
                                  name=aks_name,
                                  provisioning_configuration=prov_config)
```

AKS 배포에는 약 15분이 걸릴 수 있습니다.  배포가 성공했는지 확인합니다.

```python
aks_target.wait_for_completion(show_output=True)
print(aks_target.provisioning_state)
print(aks_target.provisioning_errors)
```

AKS 클러스터에 컨테이너를 배포합니다.
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
Docker 이미지는 gRPC 및 텐서플로우 제공 "예측" API를 지원합니다.  샘플 클라이언트를 사용하여 Docker 이미지를 호출하여 모델에서 예측을 가져옵니다.  샘플 클라이언트 코드를 사용할 수 있습니다.
- [Python](https://github.com/Azure/aml-real-time-ai/blob/master/pythonlib/amlrealtimeai/client.py)
- [C#](https://github.com/Azure/aml-real-time-ai/blob/master/sample-clients/csharp)

TensorFlow 서빙을 사용하려는 경우 [샘플 클라이언트를 다운로드할](https://www.tensorflow.org/serving/setup)수 있습니다.

```python
# Using the grpc client in Azure ML Accelerated Models SDK package
from azureml.accel import PredictionClient

address = aks_service.scoring_uri
ssl_enabled = address.startswith("https")
address = address[address.find('/')+2:].strip('/')
port = 443 if ssl_enabled else 80

# Initialize Azure ML Accelerated Models client
client = PredictionClient(address=address,
                          port=port,
                          use_ssl=ssl_enabled,
                          service_name=aks_service.name)
```

이 분류기는 [ImageNet](http://www.image-net.org/) 데이터 집합에서 학습되었기 때문에 클래스를 사람이 읽을 수 있는 레이블에 매핑합니다.

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
웹 서비스, 이미지 및 모델을 삭제합니다(종속성이 있으므로 이 순서로 수행해야 합니다).

```python
aks_service.delete()
aks_target.delete()
image.delete()
registered_model.delete()
converted_model.delete()
```

### <a name="deploy-to-a-local-edge-server"></a>로컬 에지 서버에 배포

모든 [Azure 데이터 상자 가장자리 장치에는](https://docs.microsoft.com/azure/databox-online/data-box-edge-overview
) 모델을 실행하기 위한 FPGA가 포함되어 있습니다.  한 번에 FPGA에서 하나의 모델만 실행할 수 있습니다.  다른 모델을 실행하려면 새 컨테이너를 배포하기만 하면 됩니다. 지침 및 샘플 코드는 [이 Azure 샘플에서](https://github.com/Azure-Samples/aml-hardware-accelerated-models)찾을 수 있습니다.

## <a name="secure-fpga-web-services"></a>보안 FPGA 웹 서비스

FPGA 웹 서비스를 보호하려면 [보안 웹 서비스](how-to-secure-web-service.md) 문서를 참조하세요.

## <a name="next-steps"></a>다음 단계

다음 노트북, 비디오 및 블로그를 확인하십시오.

+ 여러 [샘플 노트북](https://aka.ms/aml-accel-models-notebooks)

+ [하이퍼스케일 하드웨어: Azure + FPGA 위에 있는 규모의 ML: 빌드 2018 (비디오)](https://channel9.msdn.com/events/Build/2018/BRK3202)

+ [Microsoft FPGA 기반 구성 가능 클라우드 살펴보기(비디오)](https://channel9.msdn.com/Events/Build/2017/B8063)

+ [실시간 AI용 Project Brainwave: 프로젝트 홈페이지](https://www.microsoft.com/research/project/project-brainwave/)
