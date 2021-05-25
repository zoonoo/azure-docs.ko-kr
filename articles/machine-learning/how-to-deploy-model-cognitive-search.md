---
title: Cognitive Search에 사용할 모델 배포
titleSuffix: Azure Machine Learning
description: Azure Machine Learning을 사용하여 Cognitive Search에서 사용할 모델을 배포하는 방법을 알아봅니다. 이 모델은 검색 환경을 보강하기 위한 사용자 지정 기술로 사용됩니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: cgronlun
author: cjgronlund
ms.reviewer: larryfr
ms.date: 03/11/2021
ms.custom: deploy
ms.openlocfilehash: 22c8880cbcde1f1a55fa66beee0323e0348e1164
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103149616"
---
# <a name="deploy-a-model-for-use-with-cognitive-search"></a>Cognitive Search에 사용할 모델 배포


이 문서에서는 Azure Machine Learning을 사용하여 [Azure Cognitive Search](../search/search-what-is-azure-search.md)와 함께 사용할 모델을 배포하는 방법을 설명합니다.

Cognitive Search는 다른 유형의 콘텐츠를 통해 콘텐츠 처리를 수행하여 사람이나 애플리케이션에서 쿼리할 수 있도록 합니다. Azure Machine Learning에서 배포된 모델을 사용하여 이 프로세스를 향상시킬 수 있습니다.

Azure Machine Learning은 학습된 모델을 웹 서비스로 배포할 수 있습니다. 그런 다음, 웹 서비스는 처리 파이프라인의 일부가 되는 Cognitive Search _기술_ 에 포함됩니다.

> [!IMPORTANT]
> 이 문서의 정보는 모델 배포에만 적용됩니다. Cognitive Search에서 모델을 사용할 수 있도록 지원되는 배포 구성에 대한 정보를 제공합니다.
>
> 배포된 모델을 사용하도록 Cognitive Search를 구성하는 방법에 대한 자세한 내용은 [Azure Machine Learning을 사용하여 사용자 지정 기술 빌드 및 배포](../search/cognitive-search-tutorial-aml-custom-skill.md) 자습서를 참조하세요.
>
> 자습서의 기반이 되는 샘플은 [https://github.com/Azure-Samples/azure-search-python-samples/tree/master/AzureML-Custom-Skill](https://github.com/Azure-Samples/azure-search-python-samples/tree/master/AzureML-Custom-Skill)을 참조하세요.

Azure Cognitive Search와 함께 사용할 모델을 배포하는 경우 배포는 다음 요구 사항을 충족해야 합니다.

* Azure Kubernetes Service를 사용하여 유추를 위한 모델을 호스팅합니다.
* Azure Kubernetes Service에 대해 TLS(전송 계층 보안)를 사용하도록 설정합니다. TLS는 Cognitive Search와 배포된 모델 간의 HTTPS 통신을 보호하는 데 사용됩니다.
* 항목 스크립트는 `inference_schema` 패키지를 사용하여 서비스에 대한 OpenAPI(Swagger) 스키마를 생성해야 합니다.
* 또한 엔트리 스크립트는 JSON 데이터를 입력으로 수락하고 JSON을 출력으로 생성해야 합니다.


## <a name="prerequisites"></a>사전 요구 사항

* Azure Machine Learning 작업 영역 자세한 내용은 [Azure Machine Learning 작업 영역 만들기](how-to-manage-workspace.md)를 참조하세요.

* Azure Machine Learning SDK가 설치된 Python 개발 환경입니다. 자세한 내용은 [Azure Machine Learning SDK](/python/api/overview/azure/ml/install)를 참조하세요.  

* 등록된 모델입니다. 모델이 없는 경우 [https://github.com/Azure-Samples/azure-search-python-samples/tree/master/AzureML-Custom-Skill](https://github.com/Azure-Samples/azure-search-python-samples/tree/master/AzureML-Custom-Skill)에서 예제 Notebook을 사용합니다.

* [모델 배포 방법 및 위치](how-to-deploy-and-where.md)에 대한 일반적인 이해입니다.

## <a name="connect-to-your-workspace"></a>작업 영역에 연결

Azure Machine Learning 작업 영역은 Azure Machine Learning을 사용하는 경우 만드는 모든 아티팩트를 사용할 수 있는 중앙 집중식 위치를 제공합니다. 작업 영역은 스크립트의 로그, 메트릭, 출력 및 스냅샷을 비롯한 모든 학습 실행의 기록을 유지합니다.

기존 작업 영역에 연결하려면 다음 코드를 사용합니다.

> [!IMPORTANT]
> 이 코드 조각은 작업 영역 구성이 현재 디렉터리 또는 부모 디렉터리에 저장될 것으로 예상합니다. 자세한 내용은 [Azure Machine Learning 작업 영역 만들기 및 관리](how-to-manage-workspace.md)를 참조하세요. 파일에 구성 저장에 대한 자세한 내용은 [작업 영역 구성 파일 만들기](how-to-configure-environment.md#workspace)를 참조 하세요.

```python
from azureml.core import Workspace

try:
    # Load the workspace configuration from local cached inffo
    ws = Workspace.from_config()
    print(ws.name, ws.location, ws.resource_group, ws.location, sep='\t')
    print('Library configuration succeeded')
except:
    print('Workspace not found')
```

## <a name="create-a-kubernetes-cluster"></a>Kubernetes 클러스터 생성

**예상 시간**: 약 20분이 소요됩니다.

Kubernetes 클러스터는 컨테이너화된 애플리케이션을 실행하는 데 사용되는 가상 머신 인스턴스(노드) 집합입니다.

Azure Machine Learning에서 Azure Kubernetes Service로 모델을 배포하는 경우 모델 및 해당 모델을 웹 서비스로 호스트하는 데 필요한 모든 자산이 Docker 컨테이너에 패키지됩니다. 그런 다음, 이 컨테이너는 클러스터에 배포됩니다.

다음 코드에서는 작업 영역에 대한 새 AKS(Azure Kubernetes Service) 클러스터를 만드는 방법을 보여 줍니다.

> [!TIP]
> 기존 Azure Kubernetes Service를 Azure Machine Learning 작업 영역에 연결할 수도 있습니다. 자세한 내용은 [Azure Kubernetes Service에 모델을 배포하는 방법](how-to-deploy-azure-kubernetes-service.md)을 참조하세요.

> [!IMPORTANT]
> 코드는 `enable_ssl()` 메서드를 사용하여 클러스터에 대한 TLS(전송 계층 보안)를 사용하도록 설정합니다. Cognitive Services에서 배포된 모델을 사용하려는 경우에 필요합니다.

```python
from azureml.core.compute import AksCompute, ComputeTarget
# Create or attach to an AKS inferencing cluster

# Create the provisioning configuration with defaults
prov_config = AksCompute.provisioning_configuration()

# Enable TLS (sometimes called SSL) communications
# Leaf domain label generates a name using the formula
#  "<leaf-domain-label>######.<azure-region>.cloudapp.azure.com"
#  where "######" is a random series of characters
prov_config.enable_ssl(leaf_domain_label = "contoso")

cluster_name = 'amlskills'
# Try to use an existing compute target by that name.
# If one doesn't exist, create one.
try:
    
    aks_target = ComputeTarget(ws, cluster_name)
    print("Attaching to existing cluster")
except Exception as e:
    print("Creating new cluster")
    aks_target = ComputeTarget.create(workspace = ws, 
                                  name = cluster_name, 
                                  provisioning_configuration = prov_config)
    # Wait for the create process to complete
    aks_target.wait_for_completion(show_output = True)
```

> [!IMPORTANT]
> Azure는 AKS 클러스터가 있는 한 요금을 청구합니다. 작업을 완료하면 AKS 클러스터를 삭제해야 합니다.

Azure Machine Learning과 함께 AKS를 사용하는 방법은 [Azure Kubernetes Service에 배포하는 방법](how-to-deploy-azure-kubernetes-service.md)을 참조하세요.

## <a name="write-the-entry-script"></a>항목 스크립트 쓰기

항목 스크립트는 웹 서비스에 제출된 데이터를 수신하고, 모델에 전달하고, 채점 결과를 반환합니다. 다음 스크립트는 시작 시 모델을 로드한 다음, 모델을 사용하여 데이터를 채점합니다. 이 파일은 경우에 따라 `score.py`라고 합니다.

> [!TIP]
> 항목 스크립트는 모델에 따라 다릅니다. 예를 들어 스크립트는 모델, 데이터 형식 등에 사용할 프레임워크를 알고 있어야 합니다.

> [!IMPORTANT]
> Azure Cognitive Services에서 배포된 모델을 사용하려면 `inference_schema` 패키지를 사용하여 배포에 대한 스키마 생성을 사용하도록 설정해야 합니다. 이 패키지는 모델을 사용하여 유추를 수행하는 웹 서비스의 입력 및 출력 데이터 형식을 정의할 수 있는 데코레이터를 제공합니다.

```python
from azureml.core.model import Model
from nlp_architect.models.absa.inference.inference import SentimentInference
from spacy.cli.download import download as spacy_download
import traceback
import json
# Inference schema for schema discovery
from inference_schema.schema_decorators import input_schema, output_schema
from inference_schema.parameter_types.numpy_parameter_type import NumpyParameterType
from inference_schema.parameter_types.standard_py_parameter_type import StandardPythonParameterType

def init():
    """
    Set up the ABSA model for Inference  
    """
    global SentInference
    spacy_download('en')
    aspect_lex = Model.get_model_path('hotel_aspect_lex')
    opinion_lex = Model.get_model_path('hotel_opinion_lex') 
    SentInference = SentimentInference(aspect_lex, opinion_lex)

# Use inference schema decorators and sample input/output to
# build the OpenAPI (Swagger) schema for the deployment
standard_sample_input = {'text': 'a sample input record containing some text' }
standard_sample_output = {"sentiment": {"sentence": "This place makes false booking prices, when you get there, they say they do not have the reservation for that day.", 
                                        "terms": [{"text": "hotels", "type": "AS", "polarity": "POS", "score": 1.0, "start": 300, "len": 6}, 
                                                  {"text": "nice", "type": "OP", "polarity": "POS", "score": 1.0, "start": 295, "len": 4}]}}
@input_schema('raw_data', StandardPythonParameterType(standard_sample_input))
@output_schema(StandardPythonParameterType(standard_sample_output))    
def run(raw_data):
    try:
        # Get the value of the 'text' field from the JSON input and perform inference
        input_txt = raw_data["text"]
        doc = SentInference.run(doc=input_txt)
        if doc is None:
            return None
        sentences = doc._sentences
        result = {"sentence": doc._doc_text}
        terms = []
        for sentence in sentences:
            for event in sentence._events:
                for x in event:
                    term = {"text": x._text, "type":x._type.value, "polarity": x._polarity.value, "score": x._score,"start": x._start,"len": x._len }
                    terms.append(term)
        result["terms"] = terms
        print("Success!")
        # Return the results to the client as a JSON document
        return {"sentiment": result}
    except Exception as e:
        result = str(e)
        # return error message back to the client
        print("Failure!")
        print(traceback.format_exc())
        return json.dumps({"error": result, "tb": traceback.format_exc()})
```

항목 스크립트에 대한 자세한 내용은 [배포 방법 및 위치](how-to-deploy-and-where.md)를 참조하세요.

## <a name="define-the-software-environment"></a>소프트웨어 환경 정의

환경 클래스는 서비스에 대한 Python 종속성을 정의하는 데 사용됩니다. 여기에는 모델과 항목 스크립트 모두에 필요한 종속성이 포함됩니다. 이 예제에서는 일반 pypi 인덱스뿐만 아니라 GitHub 리포지토리에서 패키지를 설치합니다. 

```python
from azureml.core.conda_dependencies import CondaDependencies 
from azureml.core import Environment

conda = None
pip = ["azureml-defaults", "azureml-monitoring", 
       "git+https://github.com/NervanaSystems/nlp-architect.git@absa", 'nlp-architect', 'inference-schema',
       "spacy==2.0.18"]

conda_deps = CondaDependencies.create(conda_packages=None, pip_packages=pip)

myenv = Environment(name='myenv')
myenv.python.conda_dependencies = conda_deps
```

환경에 대한 자세한 내용은 [학습 및 배포를 위한 환경 만들기 및 관리](how-to-use-environments.md)를 참조하세요.

## <a name="define-the-deployment-configuration"></a>배포 구성 정의

배포 구성은 웹 서비스를 실행하는 데 사용되는 Azure Kubernetes Service 호스팅 환경을 정의합니다.

> [!TIP]
> 배포의 메모리, CPU 또는 GPU 요구 사항에 대해 잘 모를 경우 프로파일링을 사용하여 이러한 정보를 확인할 수 있습니다. 자세한 내용은 [모델 배포 방법 및 위치](how-to-deploy-and-where.md)를 참조하세요.

```python
from azureml.core.model import Model
from azureml.core.webservice import Webservice
from azureml.core.image import ContainerImage
from azureml.core.webservice import AksWebservice, Webservice

# If deploying to a cluster configured for dev/test, ensure that it was created with enough
# cores and memory to handle this deployment configuration. Note that memory is also used by
# things such as dependencies and AML components.

aks_config = AksWebservice.deploy_configuration(autoscale_enabled=True, 
                                                       autoscale_min_replicas=1, 
                                                       autoscale_max_replicas=3, 
                                                       autoscale_refresh_seconds=10, 
                                                       autoscale_target_utilization=70,
                                                       auth_enabled=True, 
                                                       cpu_cores=1, memory_gb=2, 
                                                       scoring_timeout_ms=5000, 
                                                       replica_max_concurrent_requests=2, 
                                                       max_request_wait_time=5000)
```

자세한 내용은 [AksService.deploy_configuration](/python/api/azureml-core/azureml.core.webservice.akswebservice#deploy-configuration-autoscale-enabled-none--autoscale-min-replicas-none--autoscale-max-replicas-none--autoscale-refresh-seconds-none--autoscale-target-utilization-none--collect-model-data-none--auth-enabled-none--cpu-cores-none--memory-gb-none--enable-app-insights-none--scoring-timeout-ms-none--replica-max-concurrent-requests-none--max-request-wait-time-none--num-replicas-none--primary-key-none--secondary-key-none--tags-none--properties-none--description-none--gpu-cores-none--period-seconds-none--initial-delay-seconds-none--timeout-seconds-none--success-threshold-none--failure-threshold-none--namespace-none--token-auth-enabled-none--compute-target-name-none-)에 대한 참조 설명서를 확인하세요.

## <a name="define-the-inference-configuration"></a>유추 구성 정의

유추 구성은 항목 스크립트와 환경 개체를 가리킵니다.

```python
from azureml.core.model import InferenceConfig
inf_config = InferenceConfig(entry_script='score.py', environment=myenv)
```

자세한 내용은 [InferenceConfig](/python/api/azureml-core/azureml.core.model.inferenceconfig)에 대한 참조 설명서를 확인하세요.

## <a name="deploy-the-model"></a>모델 배포

AKS 클러스터에 모델을 배포하고 서비스를 만들 때까지 기다립니다. 이 예제에서는 등록된 두 개의 모델이 레지스트리에서 로드되고 AKS에 배포됩니다. 배포 후 배포의 `score.py` 파일은 이러한 모델을 로드하고 이를 사용하여 유추를 수행합니다.

```python
from azureml.core.webservice import AksWebservice, Webservice

c_aspect_lex = Model(ws, 'hotel_aspect_lex')
c_opinion_lex = Model(ws, 'hotel_opinion_lex') 
service_name = "hotel-absa-v2"

aks_service = Model.deploy(workspace=ws,
                           name=service_name,
                           models=[c_aspect_lex, c_opinion_lex],
                           inference_config=inf_config,
                           deployment_config=aks_config,
                           deployment_target=aks_target,
                           overwrite=True)

aks_service.wait_for_deployment(show_output = True)
print(aks_service.state)
```

자세한 내용은 [모델](/python/api/azureml-core/azureml.core.model.model)에 대한 참조 설명서를 확인하세요.

## <a name="issue-a-sample-query-to-your-service"></a>서비스에 대한 샘플 쿼리 실행

다음 예제에서는 이전 코드 섹션에서 `aks_service` 변수에 저장된 배포 정보를 사용합니다. 이 변수를 사용하여 서비스와 통신하는 데 필요한 점수 매기기 URL 및 인증 토큰을 검색합니다.

```python
import requests
import json

primary, secondary = aks_service.get_keys()

# Test data
input_data = '{"raw_data": {"text": "This is a nice place for a relaxing evening out with friends. The owners seem pretty nice, too. I have been there a few times including last night. Recommend."}}'

# Since authentication was enabled for the deployment, set the authorization header.
headers = {'Content-Type':'application/json',  'Authorization':('Bearer '+ primary)} 

# Send the request and display the results
resp = requests.post(aks_service.scoring_uri, input_data, headers=headers)
print(resp.text)
```

서비스에서 반환되는 결과는 다음 JSON과 유사합니다.

```json
{"sentiment": {"sentence": "This is a nice place for a relaxing evening out with friends. The owners seem pretty nice, too. I have been there a few times including last night. Recommend.", "terms": [{"text": "place", "type": "AS", "polarity": "POS", "score": 1.0, "start": 15, "len": 5}, {"text": "nice", "type": "OP", "polarity": "POS", "score": 1.0, "start": 10, "len": 4}]}}
```

## <a name="connect-to-cognitive-search"></a>Cognitive Search에 연결

Cognitive Search에서 이 모델을 사용하는 방법에 대한 자세한 내용은 [Azure Machine Learning을 사용하여 사용자 지정 기술 빌드 및 배포](../search/cognitive-search-tutorial-aml-custom-skill.md) 자습서를 참조하세요.

## <a name="clean-up-the-resources"></a>리소스 정리

이 예를 위해 특별히 AKS 클러스터를 만든 경우 Cognitive Search를 사용하여 테스트를 완료 후 리소스를 삭제합니다.

> [!IMPORTANT]
> Azure는 AKS 클러스터의 배포 기간에 따라 요금을 청구합니다. 작업을 완료한 후에는 정리해야 합니다.

```python
aks_service.delete()
aks_target.delete()
```

## <a name="next-steps"></a>다음 단계

* [Azure Machine Learning을 사용하여 사용자 지정 기술 빌드 및 배포](../search/cognitive-search-tutorial-aml-custom-skill.md)