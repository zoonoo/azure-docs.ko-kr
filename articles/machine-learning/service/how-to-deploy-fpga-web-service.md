---
title: Azure Machine Learning을 사용하여 FPGA에 웹 서비스로 모델 배포
description: Azure Machine Learning을 사용하여 FPGA에서 실행되는 모델로 웹 서비스를 배포하는 방법을 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: tedway
author: tedway
ms.date: 09/24/2018
ms.openlocfilehash: ee67585a523ab96b1442d9eee3e9dfd55a758d32
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2018
ms.locfileid: "46971487"
---
# <a name="deploy-a-model-as-a-web-service-on-an-fpga-with-azure-machine-learning"></a>Azure Machine Learning을 사용하여 FPGA에 웹 서비스로 모델 배포

[FPGA(Field-Programmable Gate Array)](concept-accelerate-with-fpgas.md)에서 웹 서비스로 모델을 배포할 수 있습니다.  FPGA를 사용하면 단일 배치 크기로도 대기 시간이 엄청나게 짧은 추론이 가능합니다.   

## <a name="prerequisites"></a>필수 조건

- Azure 구독. 구독이 없으면 시작하기 전에 [계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만드세요.

- Azure Machine Learning 작업 영역 및 Python용 Azure Machine Learning SDK가 설치되어 있어야 합니다. [개발 환경 구성 방법](how-to-configure-environment.md) 문서를 사용하여 이러한 필수 조건을 가져오는 방법을 알아봅니다.
 
  - 작업 영역은 *미국 동부 2* 지역에 있어야 합니다.

  - 다음과 같이 contrib를 추가로 설치합니다.

    ```shell
    pip install --upgrade azureml-sdk[contrib]
    ```  

## <a name="create-and-deploy-your-model"></a>모델 만들기 및 배포
입력 이미지를 전처리할 파이프라인을 만들고, FPGA에서 ResNet 50을 사용하여 기능화하고, ImageNet 데이터 집합을 기반으로 학습된 분류자를 통해 기능을 실행합니다.

지침에 따라 다음을 수행합니다.

* 모델 파이프라인 정의
* 모델 배포
* 배포된 모델 사용
* 배포된 서비스 삭제

> [!IMPORTANT]
> 대기 시간과 처리량을 최적화하려면 클라이언트가 엔드포인트와 동일한 Azure 지역에 있어야 합니다.  현재 API는 미국 동부 Azure 지역에 만들어졌습니다.

### <a name="get-the-notebook"></a>Notebook 가져오기

편의를 위해 이 자습서를 Jupyter Notebook으로 사용할 수 있습니다. 다음 방법 중 하나를 사용하여 `project-brainwave/project-brainwave-quickstart.ipynb` Notebook을 실행합니다.

[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-in-azure-notebook.md)]

### <a name="preprocess-image"></a>이미지 전처리
파이프라인의 첫 번째 단계는 이미지를 전처리하는 것입니다.

```python
import os
import tensorflow as tf

# Input images as a two-dimensional tensor containing an arbitrary number of images represented a strings
import azureml.contrib.brainwave.models.utils as utils
in_images = tf.placeholder(tf.string)
image_tensors = utils.preprocess_array(in_images)
print(image_tensors.shape)
```
### <a name="add-featurizer"></a>기능화기 추가
모델을 초기화하고 기능화기로 사용할 ResNet50의 양자화 버전에 대한 TensorFlow 검사점을 다운로드합니다.

```python
from azureml.contrib.brainwave.models import QuantizedResnet50, Resnet50
model_path = os.path.expanduser('~/models')
model = QuantizedResnet50(model_path, is_frozen = True)
feature_tensor = model.import_graph_def(image_tensors)
print(model.version)
print(feature_tensor.name)
print(feature_tensor.shape)
```

### <a name="add-classifier"></a>분류자 추가
이 분류자는 ImageNet 데이터 집합을 기반으로 학습되었습니다.

```python
classifier_input, classifier_output = Resnet50.get_default_classifier(feature_tensor, model_path)
```

### <a name="create-service-definition"></a>서비스 정의 만들기
이제 서비스에서 실행되는 이미지 전처리, 기능화기 및 분류자가 정의되었으므로 서비스 정의를 만들 수 있습니다. 서비스 정의는 FPGA 서비스에 배포된 모델에서 생성된 파일의 집합입니다. 서비스 정의는 파이프라인으로 구성됩니다. 파이프라인은 순서대로 실행되는 일련의 단계입니다.  TensorFlow 단계, Keras 단계 및 BrainWave 단계가 지원됩니다.  단계는 서비스에서 순서대로 실행되며 각 단계의 출력은 후속 단계의 입력이 됩니다.

TensorFlow 단계를 만들려면 그래프(이 경우 기본 그래프가 사용됨)와 이 단계에 대한 입력 및 출력 텐서가 포함된 세션을 지정합니다.  이 정보는 그래프를 저장하는 데 사용되므로 서비스에서 실행될 수 있습니다.

```python
from azureml.contrib.brainwave.pipeline import ModelDefinition, TensorflowStage, BrainWaveStage

save_path = os.path.expanduser('~/models/save')
model_def_path = os.path.join(save_path, 'service_def.zip')

model_def = ModelDefinition()
with tf.Session() as sess:
    model_def.pipeline.append(TensorflowStage(sess, in_images, image_tensors))
    model_def.pipeline.append(BrainWaveStage(sess, model))
    model_def.pipeline.append(TensorflowStage(sess, classifier_input, classifier_output))
    model_def.save(model_def_path)
    print(model_def_path)
```

### <a name="deploy-model"></a>모델 배포
서비스 정의에서 서비스를 만듭니다.  작업 영역은 미국 동부 2 위치에 있어야 합니다.

```python
from azureml.core import Workspace

ws = Workspace.from_config()
print(ws.name, ws.resource_group, ws.location, ws.subscription_id, sep = '\n')

from azureml.core.model import Model
model_name = "resnet-50-rtai"
registered_model = Model.register(ws, model_def_path, model_name)

from azureml.core.webservice import Webservice
from azureml.exceptions import WebserviceException
from azureml.contrib.brainwave import BrainwaveWebservice, BrainwaveImage
service_name = "imagenet-infer"
service = None
try:
    service = Webservice(ws, service_name)
except WebserviceException:
    image_config = BrainwaveImage.image_configuration()
    deployment_config = BrainwaveWebservice.deploy_configuration()
    service = Webservice.deploy_from_model(ws, service_name, [registered_model], image_config, deployment_config)
    service.wait_for_deployment(true)
```

### <a name="test-the-service"></a>서비스 테스트
이미지를 API로 보내고 응답을 테스트하려면 출력 클래스 ID에서 ImageNet 클래스 이름으로 매핑을 추가합니다.

```python
import requests
classes_entries = requests.get("https://raw.githubusercontent.com/Lasagne/Recipes/master/examples/resnet50/imagenet_classes.txt").text.splitlines()
```

서비스를 호출하고 아래 “your-image.jpg” 파일 이름을 컴퓨터의 이미지로 바꿉니다. 

```python
with open('your-image.jpg') as f:
    results = service.run(f)
# map results [class_id] => [confidence]
results = enumerate(results)
# sort results by confidence
sorted_results = sorted(results, key=lambda x: x[1], reverse=True)
# print top 5 results
for top in sorted_results[:5]:
    print(classes_entries[top[0]], 'confidence:', top[1])
``` 

### <a name="clean-up-service"></a>서비스 정리
서비스를 삭제합니다.

```python
service.delete()
    
registered_model.delete()
```

## <a name="secure-fpga-web-services"></a>보안 FPGA 웹 서비스

FPGA에서 실행되는 Azure Machine Learning 모델은 SSL 지원과 키 기반 인증을 제공합니다. 따라서 서비스에 대한 액세스를 제한할 수 있고 클라이언트가 제출한 데이터를 보호할 수 있습니다.

> [!IMPORTANT]
> 인증은 SSL 인증서와 키를 제공한 서비스에서만 사용할 수 있습니다. 
>
> SSL을 사용하지 않으면 인터넷상의 모든 사용자가 서비스를 호출할 수 있습니다.
>
> SSL을 사용하면 서비스에 액세스할 때 인증 키가 필요합니다.

SSL은 클라이언트와 서비스 간에 전송되는 데이터를 암호화합니다. 클라이언트가 서버의 ID를 확인하는 데도 사용됩니다.

SSL 사용 서비스를 배포하거나 이미 배포된 서비스를 SSL을 사용하도록 업데이트할 수 있습니다. 단계는 동일합니다.

1. 도메인 이름을 확보합니다.

2. SSL 인증서를 확보합니다.

3. SSL 사용 서비스를 배포하거나 업데이트합니다.

4. 서비스를 가리키도록 DNS를 업데이트합니다.

### <a name="acquire-a-domain-name"></a>도메인 이름 확보

도메인 이름을 소유하지 않은 경우 __도메인 이름 등록 기관__에서 구입할 수 있습니다. 프로세스는 등록 기관마다 다르며 비용도 그렇습니다. 등록 기관은 도메인 이름을 관리하는 도구도 제공합니다. 이러한 도구는 정규화된 도메인 이름(예: www.contoso.com)을 서비스가 호스팅되는 IP 주소에 매핑하는 데 사용됩니다.

### <a name="acquire-an-ssl-certificate"></a>SSL 인증서 확보

SSL 인증서를 확보하는 방법은 여러 가지입니다. 가장 일반적인 방법은 CA(__인증 기관__)에서 구입하는 것입니다. 인증서를 어디에서 확보하든 다음과 같은 파일이 필요합니다.

* __인증서__. 인증서는 전체 인증서 체인을 포함해야 하며 PEM으로 인코딩되어야 합니다.
* __키__. 키는 PEM으로 인코딩되어야 합니다.

> [!TIP]
> 인증 기관이 인증서와 키를 PEM으로 인코딩된 파일로 제공할 수 없는 경우 [OpenSSL](https://www.openssl.org/)과 같은 유틸리티를 사용하여 형식을 변경할 수 있습니다.

> [!IMPORTANT]
> 자체 서명된 인증서는 개발에만 사용해야 합니다. 프로덕션에 사용해서는 안됩니다.
>
> 자체 서명된 인증서를 사용하는 경우 [자체 서명된 인증서로 서비스 소비](#self-signed) 섹션에서 구체적인 지침을 참조하세요.

> [!WARNING]
> 인증서를 요청할 때는 서비스에 사용할 주소의 FQDN(정규화된 도메인 이름)을 제공해야 합니다. 예: www.contoso.com. 인증서에 스탬프가 지정된 주소와 클라이언트에서 사용되는 주소는 서비스의 ID를 확인할 때 비교됩니다.
>
> 주소가 일치하지 않으면 클라이언트로 오류가 전송됩니다. 

### <a name="deploy-or-update-the-service-with-ssl-enabled"></a>SSL 사용 서비스를 배포하거나 업데이트

SSL 사용 서비스를 배포하려면 `ssl_enabled` 매개 변수를 `True`로 설정합니다. `ssl_certificate` 매개 변수를 __인증서__ 파일의 값으로 설정하고 `ssl_key`를 __키__ 파일의 값으로 설정합니다. 다음 예제는 SSL 사용 서비스를 배포하는 방법을 보여줍니다.

```python
from amlrealtimeai import DeploymentClient

subscription_id = "<Your Azure Subscription ID>"
resource_group = "<Your Azure Resource Group Name>"
model_management_account = "<Your AzureML Model Management Account Name>"
location = "eastus2"

model_name = "resnet50-model"
service_name = "quickstart-service"

deployment_client = DeploymentClient(subscription_id, resource_group, model_management_account, location)

with open('cert.pem','r') as cert_file:
    with open('key.pem','r') as key_file:
        cert = cert_file.read()
        key = key_file.read()
        service = deployment_client.create_service(service_name, model_id, ssl_enabled=True, ssl_certificate=cert, ssl_key=key)
```

`create_service` 작업의 응답에는 서비스의 IP 주소가 포함됩니다. IP 주소는 DNS 이름을 서비스의 IP 주소에 매핑할 때 사용됩니다.

응답에는 서비스를 소비하는 데 사용되는 __기본 키__와 __보조 키__도 포함됩니다.

### <a name="update-your-dns-to-point-to-the-service"></a>서비스를 가리키도록 DNS 업데이트

도메인 이름 등록 기관이 제공하는 도구를 사용하여 도메인 이름의 DNS 레코드를 업데이트합니다. 레코드는 서비스의 IP 주소를 가리켜야 합니다.

> [!NOTE]
> 등록 기관 및 도메인 이름에 대해 구성된 TTL(Time To Live)에 따라 클라이언트가 도메인 이름을 확인하는 데 몇 분에서 몇 시간이 걸릴 수 있습니다.

### <a name="consume-authenticated-services"></a>인증된 서비스 사용

다음 예제는 Python과 C#을 사용하여 인증된 서비스를 사용하는 방법을 보여줍니다.

> [!NOTE]
> `authkey`를 서비스를 만들 때 반환된 기본 키 또는 보조 키로 바꿉니다.

```python
from amlrealtimeai import PredictionClient
client = PredictionClient(service.ipAddress, service.port, use_ssl=True, access_token="authKey")
image_file = R'C:\path_to_file\image.jpg'
results = client.score_image(image_file)
```

```csharp
var client = new ScoringClient(host, 50051, useSSL, "authKey");
float[,] result;
using (var content = File.OpenRead(image))
    {
        IScoringRequest request = new ImageRequest(content);
        result = client.Score<float[,]>(request);
    }
```

다른 gRPC 클라이언트는 인증 헤더를 설정하여 요청을 인증할 수 있습니다. 일반적인 방법은 `SslCredentials`와 `CallCredentials`를 결합하는 `ChannelCredentials` 개체를 생성하는 것입니다. 이것은 요청의 인증 헤더에 추가됩니다. 특정 헤더에 대한 지원 구현에 대한 자세한 내용은 [https://grpc.io/docs/guides/auth.html](https://grpc.io/docs/guides/auth.html)을 참조하세요.

다음 예제에서는 C# 및 Go에서 헤더를 설정하는 방법을 보여줍니다.

```csharp
creds = ChannelCredentials.Create(baseCreds, CallCredentials.FromInterceptor(
                      async (context, metadata) =>
                      {
                          metadata.Add(new Metadata.Entry("authorization", "authKey"));
                          await Task.CompletedTask;
                      }));

```

```go
conn, err := grpc.Dial(serverAddr, 
    grpc.WithTransportCredentials(credentials.NewClientTLSFromCert(nil, "")),
    grpc.WithPerRPCCredentials(&authCreds{
    Key: "authKey"}))

type authCreds struct {
    Key string
}

func (c *authCreds) GetRequestMetadata(context.Context, uri ...string) (map[string]string, error) {
    return map[string]string{
        "authorization": c.Key,
    }, nil
}

func (c *authCreds) RequireTransportSecurity() bool {
    return true
}
```

### <a id="self-signed"></a>자체 서명된 인증서로 서비스 사용

클라이언트가 자체 서명된 인증서로 보안이 설정된 서버를 인증할 수 있게 설정하는 방법은 두 가지입니다.

* 클라이언트 시스템에서, 클라이언트 시스템의 `GRPC_DEFAULT_SSL_ROOTS_FILE_PATH` 환경 변수가 인증서 파일을 가리키도록 설정합니다.

* `SslCredentials` 개체를 생성할 때 인증서 파일의 콘텐츠를 생성자에게 전달합니다.

두 가지 방법 중 하나를 사용하면 gRPC가 인증서를 루트 인증서로 사용하게 됩니다.

> [!IMPORTANT]
> gRPC는 신뢰할 수 없는 인증서를 허용하지 않습니다. 신뢰할 수 없는 인증서를 사용하면 `Unavailable` 상태 코드가 발생하면서 실패합니다. 실패의 세부 정보에는 `Connection Failed`가 포함됩니다.
