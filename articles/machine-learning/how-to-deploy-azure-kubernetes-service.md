---
title: Kubernetes Service에 ML 모델 배포
titleSuffix: Azure Machine Learning
description: Azure Kubernetes Service를 사용 하 여 Azure Machine Learning 모델을 웹 서비스로 배포 하는 방법을 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to, contperfq1
ms.author: jordane
author: jpe316
ms.reviewer: larryfr
ms.date: 09/01/2020
ms.openlocfilehash: c7c3825d97a5566a43346febc7bbf86a4b242b54
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/22/2020
ms.locfileid: "90905692"
---
# <a name="deploy-a-model-to-an-azure-kubernetes-service-cluster"></a>Azure Kubernetes Service 클러스터에 모델 배포


Azure Machine Learning를 사용 하 여 AKS (Azure Kubernetes Service)에서 웹 서비스로 모델을 배포 하는 방법에 대해 알아봅니다. Azure Kubernetes 서비스는 대규모 프로덕션 배포에 적합 합니다. 다음 기능이 하나 이상 필요한 경우 Azure Kubernetes service를 사용 합니다.

- __빠른 응답 시간__
- 배포 __된 서비스의 자동__ 크기 조정
- __Logging__
- __모델 데이터 수집__
- __인증__
- __TLS 종료__
- GPU 및 필드 프로그래밍 가능 게이트 배열 (FPGA)과 같은 __하드웨어 가속__ 옵션

Azure Kubernetes Service에 배포 하는 경우 __작업 영역에 연결__된 AKS 클러스터에 배포 합니다. AKS 클러스터를 작업 영역에 연결 하는 방법에 대 한 자세한 내용은 [Azure Kubernetes Service 클러스터 만들기 및 연결](how-to-create-attach-kubernetes.md)을 참조 하세요.

> [!IMPORTANT]
> 웹 서비스로 배포 하기 전에 로컬로 디버그 하는 것이 좋습니다. 자세한 내용은 [로컬로 디버그](https://docs.microsoft.com/azure/machine-learning/how-to-troubleshoot-deployment#debug-locally) 를 참조 하세요.
>
> 또한 Azure Machine Learning - [로컬 Notebook에 배포](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/deployment/deploy-to-local)를 참조할 수 있습니다.

## <a name="prerequisites"></a>사전 요구 사항

- Azure Machine Learning 작업 영역 자세한 내용은 [Azure Machine Learning 작업 영역 만들기](how-to-manage-workspace.md)를 참조 하세요.

- 작업 영역에 등록 된 machine learning 모델입니다. 등록 된 모델이 없는 경우 모델을 배포 하 [는 방법 및 위치](how-to-deploy-and-where.md)를 참조 하세요.

- Machine Learning 서비스, [Azure Machine Learning PYTHON SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py&preserve-view=true)또는 [Azure Machine Learning Visual Studio Code 확장](tutorial-setup-vscode-extension.md) [에 대 한 Azure CLI 확장](reference-azure-machine-learning-cli.md)입니다.

- 이 문서의 __Python__ 코드 조각에서는 다음 변수가 설정 되었다고 가정 합니다.

    * `ws` -작업 영역으로 설정 합니다.
    * `model` -등록 된 모델로 설정 합니다.
    * `inference_config` -모델에 대 한 유추 구성으로 설정 합니다.

    이러한 변수를 설정 하는 방법에 대 한 자세한 내용은 [모델을 배포 하는 방법 및 위치](how-to-deploy-and-where.md)를 참조 하세요.

- 이 문서의 __CLI__ 코드 조각은 문서를 만든 것으로 가정 합니다 `inferenceconfig.json` . 이 문서를 만드는 방법에 대 한 자세한 내용은 [모델을 배포 하는 방법 및 위치](how-to-deploy-and-where.md)를 참조 하세요.

- 작업 영역에 연결 된 Azure Kubernetes 서비스 클러스터입니다. 자세한 내용은 [Azure Kubernetes Service 클러스터 만들기 및 연결](how-to-create-attach-kubernetes.md)을 참조 하세요.

    - GPU 노드나 FPGA 노드 (또는 특정 SKU)에 모델을 배포 하려면 특정 SKU를 사용 하 여 클러스터를 만들어야 합니다. 기존 클러스터에 보조 노드 풀을 만들고 보조 노드 풀에 모델을 배포 하는 것은 지원 되지 않습니다.

## <a name="understand-the-deployment-processes"></a>배포 프로세스 이해

단어 "deployment"는 Kubernetes 및 Azure Machine Learning 모두에 사용 됩니다. 이러한 두 컨텍스트에서는 "배포"의 의미가 다릅니다. Kubernetes에서는 `Deployment` 선언적 YAML 파일을 사용 하 여 지정 된 구체적인 엔터티입니다. Kubernetes에는 `Deployment` 및와 같은 다른 Kubernetes 엔터티와의 정의 된 수명 주기와 구체적 관계가 있습니다 `Pods` `ReplicaSets` . 문서 및 비디오의 Kubernetes에 대 한 자세한 내용은 [Kubernetes?를](https://aka.ms/k8slearning)살펴보세요.

Azure Machine Learning에서 "배포"는 보다 일반적으로 사용 가능 하 고 프로젝트 리소스를 정리 하는 데 사용 됩니다. 배포의 일부를 고려 하 Azure Machine Learning 단계는 다음과 같습니다.

1. Amlignore 또는. .gitignore에 지정 된 파일을 무시 하 고 프로젝트 폴더의 파일을 압축 합니다.
1. 계산 클러스터 확장 (Kubernetes과 관련)
1. Dockerfile을 계산 노드에 빌드 또는 다운로드 (Kubernetes와 관련 됨)
    1. 시스템은 다음의 해시를 계산 합니다. 
        - 기본 이미지 
        - 사용자 지정 docker 단계 ( [사용자 지정 docker 기본 이미지를 사용 하 여 모델 배포](https://docs.microsoft.com/azure/machine-learning/how-to-deploy-custom-docker-image)참조)
        - Conda definition YAML ( [Azure Machine Learning에서 소프트웨어 환경 만들기 & 사용](https://docs.microsoft.com/azure/machine-learning/how-to-use-environments)을 참조 하세요.)
    1. 시스템은 작업 영역 Azure Container Registry (ACR) 조회에서이 해시를 키로 사용 합니다.
    1. 찾을 수 없는 경우 전역 ACR에서 일치 하는 항목을 찾습니다.
    1. 이 파일을 찾을 수 없는 경우 시스템은 캐시 되 고 작업 영역 ACR로 푸시되는 새 이미지를 작성 합니다.
1. 계산 노드의 임시 저장소에 압축 된 프로젝트 파일 다운로드
1. 프로젝트 파일 압축 풀기
1. 실행 하는 계산 노드 `python <entry script> <arguments>`
1. `./outputs`작업 영역과 연결 된 저장소 계정에 기록 된 로그, 모델 파일 및 기타 파일 저장
1. 임시 저장소 제거를 포함 하 여 계산 축소 (Kubernetes와 관련 됨)

### <a name="azure-ml-router"></a>Azure ML 라우터

들어오는 유추 요청을 배포 된 서비스로 라우팅하는 프런트 엔드 구성 요소 (azureml-fe)는 필요에 따라 자동으로 크기를 조정 합니다. Azureml-fe의 크기 조정은 AKS 클러스터 용도 및 크기 (노드 수)를 기반으로 합니다. 클러스터 용도 및 노드는 [AKS 클러스터를 만들거나 연결할](how-to-create-attach-kubernetes.md)때 구성 됩니다. 클러스터 마다 하나의 azureml-fe 서비스가 있으며,이는 여러 pod에서 실행 될 수 있습니다.

> [!IMPORTANT]
> __개발-테스트__로 구성 된 클러스터를 사용 하는 경우 자체 scaler 사용 **하지 않도록 설정**됩니다.

Azureml-fe는 더 많은 코어를 사용 하도록 수직으로 확장 하 고 더 많은 pod를 사용 하기 위해 수평으로 확장 합니다. 수직 확장을 결정할 때 들어오는 유추 요청을 라우팅하는 데 걸리는 시간이 사용 됩니다. 이 시간이 임계값을 초과 하면 확장이 발생 합니다. 들어오는 요청을 라우팅하는 데 걸리는 시간이 임계값을 계속 초과 하면 확장이 발생 합니다.

규모를 축소 하는 경우 CPU 사용량이 사용 됩니다. CPU 사용 임계값에 도달 하면 프런트 엔드는 먼저 축소 됩니다. CPU 사용량이 확장 임계값으로 떨어지면 확장 작업이 수행 됩니다. 규모를 확장 및 축소 하는 것은 사용 가능한 클러스터 리소스가 충분 한 경우에만 발생 합니다.

## <a name="deploy-to-aks"></a>AKS에 배포

Azure Kubernetes Service에 모델을 배포 하려면 필요한 계산 리소스를 설명 하는 __배포 구성을__ 만듭니다. 예를 들면 코어 수와 메모리입니다. 모델 및 웹 서비스를 호스트 하는 데 필요한 환경을 설명 하는 __유추 구성__도 필요 합니다. 유추 구성을 만드는 방법에 대 한 자세한 내용은 [모델을 배포 하는 방법 및 위치](how-to-deploy-and-where.md)를 참조 하세요.

> [!NOTE]
> 배포할 모델 수는 배포 당 1000 모델 (컨테이너 당)으로 제한 됩니다.

<a id="using-the-cli"></a>

# <a name="python"></a>[Python](#tab/python)

```python
from azureml.core.webservice import AksWebservice, Webservice
from azureml.core.model import Model

aks_target = AksCompute(ws,"myaks")
# If deploying to a cluster configured for dev/test, ensure that it was created with enough
# cores and memory to handle this deployment configuration. Note that memory is also used by
# things such as dependencies and AML components.
deployment_config = AksWebservice.deploy_configuration(cpu_cores = 1, memory_gb = 1)
service = Model.deploy(ws, "myservice", [model], inference_config, deployment_config, aks_target)
service.wait_for_deployment(show_output = True)
print(service.state)
print(service.get_logs())
```

이 예제에 사용 된 클래스, 메서드 및 매개 변수에 대 한 자세한 내용은 다음 참조 문서를 참조 하세요.

* [AksCompute](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.aks.akscompute?view=azure-ml-py&preserve-view=true)
* [AksWebservice deploy_configuration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aks.aksservicedeploymentconfiguration?view=azure-ml-py&preserve-view=true)
* [모델. 배포](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#&preserve-view=truedeploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-)
* [웹 서비스. wait_for_deployment](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice%28class%29?view=azure-ml-py#&preserve-view=truewait-for-deployment-show-output-false-)

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

CLI를 사용 하 여 배포 하려면 다음 명령을 사용 합니다. `myaks`AKS 계산 대상의 이름으로 대체 합니다. `mymodel:1`를 등록 된 모델의 이름 및 버전으로 바꿉니다. `myservice`이 서비스를 제공할 이름으로 대체 합니다.

```azurecli-interactive
az ml model deploy -ct myaks -m mymodel:1 -n myservice -ic inferenceconfig.json -dc deploymentconfig.json
```

[!INCLUDE [deploymentconfig](../../includes/machine-learning-service-aks-deploy-config.md)]

자세한 내용은 [az ml model deploy](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/model?view=azure-cli-latest#ext-azure-cli-ml-az-ml-model-deploy) reference를 참조 하세요.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

VS Code 사용에 대 한 자세한 내용은 [VS Code 확장을 통해 AKS에 배포를](tutorial-train-deploy-image-classification-model-vscode.md#deploy-the-model)참조 하세요.

> [!IMPORTANT]
> VS Code를 통해 배포 하려면 AKS 클러스터를 미리 만들거나 작업 영역에 미리 연결 해야 합니다.

---

### <a name="autoscaling"></a>자동 확장

Azure ML 모델 배포에 대 한 자동 크기 조정을 처리 하는 구성 요소는 azureml-fe 이며 스마트 요청 라우터입니다. 모든 유추 요청은이를 통과 하므로 배포 된 모델의 크기를 자동으로 조정 하는 데 필요한 데이터가 있습니다.

> [!IMPORTANT]
> * **모델 배포에 대해 HPA (Kubernetes 수평 Pod Autoscaler)를 사용 하지 마십시오**. 이렇게 하면 두 개의 자동 크기 조정 구성 요소가 서로 경쟁 하 게 됩니다. Azureml-fe는 Azure ML에 의해 배포 된 모델의 크기를 자동으로 조정 하도록 설계 되었습니다. 여기서 HPA는 CPU 사용량 또는 사용자 지정 메트릭 구성과 같은 일반 메트릭에 대해 추측 하거나 대략적인 모델 사용률을 가져야 합니다.
> 
> * **Azureml-fe는 AKS 클러스터의 노드 수를 조정 하지**않습니다 .이로 인해 예기치 않은 비용 증가가 발생할 수 있습니다. 대신 실제 클러스터 경계 내에서 **모델에 대 한 복제본 수를 조정** 합니다. 클러스터 내에서 노드 수를 조정 해야 하는 경우 수동으로 클러스터의 크기를 조정 하거나 [AKS cluster autoscaler을 구성할](/azure/aks/cluster-autoscaler)수 있습니다.

자동 크기 조정은 `autoscale_target_utilization` `autoscale_min_replicas` `autoscale_max_replicas` AKS 웹 서비스에 대해, 및를 설정 하 여 제어할 수 있습니다. 다음 예제에서는 자동 크기 조정을 사용 하도록 설정 하는 방법을 보여 줍니다.

```python
aks_config = AksWebservice.deploy_configuration(autoscale_enabled=True, 
                                                autoscale_target_utilization=30,
                                                autoscale_min_replicas=1,
                                                autoscale_max_replicas=4)
```

수직 확장/축소 결정은 현재 컨테이너 복제본의 사용률을 기준으로 합니다. 현재 복제본의 총 수로 나눈 사용 중인 복제본 수 (요청 처리)가 현재 사용량입니다. 이 수가 초과 `autoscale_target_utilization` 되 면 더 많은 복제본이 생성 됩니다. 이보다 낮으면 복제본이 줄어듭니다. 기본적으로 대상 사용률은 70%입니다.

복제본 추가에 대 한 결정은 신속 하 고 빠르게 진행 됩니다 (약 1 초). 복제본 제거에 대 한 결정은 매우 보수적인 결정입니다 (약 1 분).

다음 코드를 사용 하 여 필요한 복제본을 계산할 수 있습니다.

```python
from math import ceil
# target requests per second
targetRps = 20
# time to process the request (in seconds)
reqTime = 10
# Maximum requests per container
maxReqPerContainer = 1
# target_utilization. 70% in this example
targetUtilization = .7

concurrentRequests = targetRps * reqTime / targetUtilization

# Number of container replicas
replicas = ceil(concurrentRequests / maxReqPerContainer)
```

, 및를 설정 하는 방법에 대 한 자세한 내용은 `autoscale_target_utilization` `autoscale_max_replicas` `autoscale_min_replicas` [AksWebservice](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.akswebservice?view=azure-ml-py) 모듈 참조를 참조 하세요.

## <a name="deploy-models-to-aks-using-controlled-rollout-preview"></a>제어 된 롤아웃 (미리 보기)을 사용 하 여 AKS에 모델 배포

끝점을 사용 하 여 제어 된 방식으로 모델 버전을 분석 하 고 승격 합니다. 단일 끝점 뒤에 최대 6 개의 버전을 배포할 수 있습니다. 끝점은 다음과 같은 기능을 제공 합니다.

* __각 끝점으로 전송 되는 점수 매기기 트래픽의 백분율__을 구성 합니다. 예를 들어 트래픽의 20%를 끝점 ' 테스트 '로, 80%를 ' 프로덕션 '으로 라우팅합니다.

    > [!NOTE]
    > 트래픽의 100%를 고려 하지 않는 경우 남은 백분율은 __기본__ 끝점 버전으로 라우팅됩니다. 예를 들어 ' test ' 끝점 버전을 구성 하 여 트래픽의 10%를 가져오고, 30%의 경우 ' r u p '를 구성 하는 경우 나머지 60%는 기본 끝점 버전으로 전송 됩니다.
    >
    > 만든 첫 번째 끝점 버전이 자동으로 기본값으로 구성 됩니다. `is_default=True`끝점 버전을 만들거나 업데이트할 때를 설정 하 여이를 변경할 수 있습니다.
     
* 끝점 버전을 __제어__ 또는 __처리__중 하나로 태그를 합니다. 예를 들어 현재 프로덕션 끝점 버전은 컨트롤이 될 수 있지만 잠재적 새 모델은 처리 버전으로 배포 됩니다. 처리 버전의 성능을 평가한 후에는 현재 컨트롤이 우수함 새 프로덕션/컨트롤로 승격 될 수 있습니다.

    > [!NOTE]
    > __컨트롤은 하나만 사용할__ 수 있습니다. 여러 처리가를 사용할 수 있습니다.

App insights를 사용 하도록 설정 하 여 끝점 및 배포 된 버전의 작업 메트릭을 볼 수 있습니다.

### <a name="create-an-endpoint"></a>엔드포인트 만들기
모델을 배포할 준비가 되 면 점수 매기기 끝점을 만들고 첫 번째 버전을 배포 합니다. 다음 예제에서는 SDK를 사용 하 여 끝점을 배포 하 고 만드는 방법을 보여 줍니다. 첫 번째 배포는 기본 버전으로 정의 됩니다. 즉, 모든 버전에서 지정 되지 않은 트래픽 백분위 수가 기본 버전으로 이동 합니다.  

> [!TIP]
> 다음 예제에서는 구성에서 초기 끝점 버전을 설정 하 여 트래픽의 20%를 처리 합니다. 이 끝점은 첫 번째 끝점 이므로 기본 버전 이기도 합니다. 또한 다른 80%의 트래픽에 대 한 다른 버전은 없으므로 기본값으로 라우팅됩니다. 트래픽의 백분율을 사용 하는 다른 버전을 배포할 때까지이는 트래픽 100%를 효과적으로 수신 합니다.

```python
import azureml.core,
from azureml.core.webservice import AksEndpoint
from azureml.core.compute import AksCompute
from azureml.core.compute import ComputeTarget
# select a created compute
compute = ComputeTarget(ws, 'myaks')
namespace_name= endpointnamespace
# define the endpoint and version name
endpoint_name = "mynewendpoint"
version_name= "versiona"
# create the deployment config and define the scoring traffic percentile for the first deployment
endpoint_deployment_config = AksEndpoint.deploy_configuration(cpu_cores = 0.1, memory_gb = 0.2,
                                                              enable_app_insights = True,
                                                              tags = {'sckitlearn':'demo'},
                                                              description = "testing versions",
                                                              version_name = version_name,
                                                              traffic_percentile = 20)
 # deploy the model and endpoint
 endpoint = Model.deploy(ws, endpoint_name, [model], inference_config, endpoint_deployment_config, compute)
 # Wait for he process to complete
 endpoint.wait_for_deployment(True)
 ```

### <a name="update-and-add-versions-to-an-endpoint"></a>끝점에 버전 업데이트 및 추가

다른 버전을 끝점에 추가 하 고 버전으로 이동 하는 점수 매기기 트래픽 백분위 수를 구성 합니다. 두 가지 유형의 버전, 즉 컨트롤과 처리 버전이 있습니다. 단일 컨트롤 버전을 비교 하는 데 도움이 되는 여러 처리 버전이 있을 수 있습니다.

> [!TIP]
> 다음 코드 조각에 의해 생성 된 두 번째 버전은 트래픽의 10%를 허용 합니다. 첫 번째 버전은 20%로 구성 되므로 특정 버전에 대 한 트래픽의 30%만 구성 됩니다. 나머지 70%는 기본 버전 이기도 하기 때문에 첫 번째 끝점 버전으로 전송 됩니다.

 ```python
from azureml.core.webservice import AksEndpoint

# add another model deployment to the same endpoint as above
version_name_add = "versionb"
endpoint.create_version(version_name = version_name_add,
                        inference_config=inference_config,
                        models=[model],
                        tags = {'modelVersion':'b'},
                        description = "my second version",
                        traffic_percentile = 10)
endpoint.wait_for_deployment(True)
```

기존 버전을 업데이트 하거나 끝점에서 삭제 합니다. 버전의 기본 형식, 컨트롤 형식 및 트래픽 백분위 수를 변경할 수 있습니다. 다음 예제에서 두 번째 버전은 트래픽을 40%로 늘리고 이제는 기본값입니다.

> [!TIP]
> 다음 코드 조각 후에는 이제 두 번째 버전이 기본값입니다. 이제 40%에 대해 구성 되지만 원래 버전은 20%로 구성 되어 있습니다. 즉, 버전 구성에서 40%의 트래픽이 고려 되지 않습니다. 남아 있는 트래픽은 이제 기본값 이기 때문에 두 번째 버전으로 라우팅됩니다. 실제로 트래픽의 80%를 수신 합니다.

 ```python
from azureml.core.webservice import AksEndpoint

# update the version's scoring traffic percentage and if it is a default or control type
endpoint.update_version(version_name=endpoint.versions["versionb"].name,
                        description="my second version update",
                        traffic_percentile=40,
                        is_default=True,
                        is_control_version_type=True)
# Wait for the process to complete before deleting
endpoint.wait_for_deployment(true)
# delete a version in an endpoint
endpoint.delete_version(version_name="versionb")

```

## <a name="web-service-authentication"></a>웹 서비스 인증

Azure Kubernetes Service에 배포 하는 경우 __키 기반__ 인증은 기본적으로 사용 하도록 설정 됩니다. __토큰 기반__ 인증을 사용 하도록 설정할 수도 있습니다. 토큰 기반 인증을 사용 하려면 클라이언트가 Azure Active Directory 계정을 사용 하 여 배포 된 서비스에 대 한 요청을 수행 하는 데 사용 되는 인증 토큰을 요청 해야 합니다.

인증을 __사용 하지 않도록__ 설정 하려면 `auth_enabled=False` 배포 구성을 만들 때 매개 변수를 설정 합니다. 다음 예제에서는 SDK를 사용 하 여 인증을 사용 하지 않도록 설정 합니다.

```python
deployment_config = AksWebservice.deploy_configuration(cpu_cores=1, memory_gb=1, auth_enabled=False)
```

클라이언트 응용 프로그램에서 인증 하는 방법에 대 한 자세한 내용은 [웹 서비스로 배포 된 Azure Machine Learning 모델 사용](how-to-consume-web-service.md)을 참조 하세요.

### <a name="authentication-with-keys"></a>키를 사용 하 여 인증

키 인증을 사용 하는 경우 메서드를 사용 하 여 `get_keys` 기본 및 보조 인증 키를 검색할 수 있습니다.

```python
primary, secondary = service.get_keys()
print(primary)
```

> [!IMPORTANT]
> 키를 다시 생성 해야 하는 경우 다음을 사용 합니다. [`service.regen_key`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice(class)?view=azure-ml-py&preserve-view=true)

### <a name="authentication-with-tokens"></a>토큰을 사용한 인증

토큰 인증을 사용 하도록 설정 하려면 `token_auth_enabled=True` 배포를 만들거나 업데이트할 때 매개 변수를 설정 합니다. 다음 예제에서는 SDK를 사용 하 여 토큰 인증을 사용 하도록 설정 합니다.

```python
deployment_config = AksWebservice.deploy_configuration(cpu_cores=1, memory_gb=1, token_auth_enabled=True)
```

토큰 인증을 사용 하는 경우 메서드를 사용 하 여 `get_token` JWT 토큰 및 해당 토큰의 만료 시간을 검색할 수 있습니다.

```python
token, refresh_by = service.get_token()
print(token)
```

> [!IMPORTANT]
> 토큰의 시간 이후에 새 토큰을 요청 해야 합니다 `refresh_by` .
>
> Azure Kubernetes Service 클러스터와 동일한 지역에 Azure Machine Learning 작업 영역을 만드는 것이 좋습니다. 토큰으로 인증하기 위해 웹 서비스는 Azure Machine Learning 작업 영역이 생성되는 지역을 호출합니다. 작업 영역을 사용할 수 없는 경우에는 클러스터가 작업 영역과 다른 지역에 있는 경우에도 웹 서비스에 대 한 토큰을 가져올 수 없습니다. 이로 인해 작업 영역을 다시 사용할 수 있을 때까지 토큰 기반 인증을 사용할 수 없습니다. 또한 클러스터의 지역과 작업 영역 영역 간의 거리가 클수록 토큰을 인출 하는 데 시간이 오래 걸립니다.
>
> 토큰을 검색 하려면 Azure Machine Learning SDK 또는 [az ml service get-token](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/service?view=azure-cli-latest#ext-azure-cli-ml-az-ml-service-get-access-token) 명령을 사용 해야 합니다.

## <a name="next-steps"></a>다음 단계

* [Azure Virtual Network를 사용 하 여 추론 환경 보호](how-to-secure-inferencing-vnet.md)
* [사용자 지정 Docker 이미지를 사용 하 여 모델을 배포 하는 방법](how-to-deploy-custom-docker-image.md)
* [배포 문제 해결](how-to-troubleshoot-deployment.md)
* [웹 서비스 업데이트](how-to-deploy-update-web-service.md)
* [TLS를 사용하여 Azure Machine Learning을 통해 웹 서비스 보호](how-to-secure-web-service.md)
* [웹 서비스로 배포된 ML 모델 사용](how-to-consume-web-service.md)
* [Application Insights를 사용하여 Azure Machine Learning 모델 모니터링](how-to-enable-app-insights.md)
* [프로덕션 환경에서 모델용 데이터 수집](how-to-enable-data-collection.md)
