---
title: Azure Kubernetes 서비스에 모델을 배포하는 방법
titleSuffix: Azure Machine Learning
description: Azure Kubernetes 서비스를 사용하여 Azure 기계 학습 모델을 웹 서비스로 배포하는 방법을 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: jordane
author: jpe316
ms.reviewer: larryfr
ms.date: 01/16/2020
ms.openlocfilehash: 792964f28ddb3fcb10932b8de9499a9c7027960f
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/01/2020
ms.locfileid: "80475389"
---
# <a name="deploy-a-model-to-an-azure-kubernetes-service-cluster"></a>Azure Kubernetes 서비스 클러스터에 모델 배포
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Azure 기계 학습을 사용하여 AKS(Azure Kubernetes Service)에서 모델을 웹 서비스로 배포하는 방법을 알아봅니다. Azure Kubernetes 서비스는 대규모 프로덕션 배포에 적합합니다. 다음 기능 중 하나 이상이 필요한 경우 Azure Kubernetes 서비스를 사용합니다.

- __빠른 응답 시간__.
- 배포된 서비스의 __자동 크기 조정입니다.__
- GPU 및 현장 프로그래밍 가능한 게이트 어레이(FPGA)와 같은 __하드웨어 가속__ 옵션.

> [!IMPORTANT]
> 클러스터 크기 조정은 Azure 기계 학습 SDK를 통해 제공되지 않습니다. AKS 클러스터의 노드 크기 조정에 대한 자세한 내용은 [AKS 클러스터의 노드 수 확장을](../aks/scale-cluster.md)참조하십시오.

Azure Kubernetes 서비스에 배포할 때 작업 영역에 연결된 AKS __클러스터에 배포합니다.__ 작업 영역에 AKS 클러스터를 연결하는 방법에는 두 가지가 있습니다.

* Azure 기계 학습 SDK, 기계 학습 CLI 또는 [Azure 기계 학습 스튜디오를](https://ml.azure.com)사용하여 AKS 클러스터를 만듭니다. 이 프로세스는 클러스터를 작업 영역에 자동으로 연결합니다.
* 기존 AKS 클러스터를 Azure 기계 학습 작업 영역에 연결합니다. Azure 기계 학습 SDK, 기계 학습 CLI 또는 Azure 기계 학습 스튜디오를 사용하여 클러스터를 연결할 수 있습니다.

> [!IMPORTANT]
> 생성 또는 첨부 프로세스는 일회성 작업입니다. AKS 클러스터가 작업 영역에 연결되면 배포에 사용할 수 있습니다. 더 이상 필요하지 않은 경우 AKS 클러스터를 분리하거나 삭제할 수 있습니다. 분리되거나 삭제되면 더 이상 클러스터에 배포할 수 없습니다.

## <a name="prerequisites"></a>사전 요구 사항

- Azure Machine Learning 작업 영역 자세한 내용은 [Azure 기계 학습 작업 영역 만들기를](how-to-manage-workspace.md)참조하십시오.

- 작업 영역에 등록된 기계 학습 모델입니다. 등록된 모델이 없는 경우 [모델을 배포하는 방법과 위치를](how-to-deploy-and-where.md)참조하십시오.

- [기계 학습 서비스에 대한 Azure CLI 확장,](reference-azure-machine-learning-cli.md)Azure 기계 학습 [파이썬 SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)또는 [Azure 기계 학습 시각적 스튜디오 코드 확장](tutorial-setup-vscode-extension.md).

- 이 문서의 __Python__ 코드 조각은 다음 변수가 설정되어 있다고 가정합니다.

    * `ws`- 작업 공간으로 설정합니다.
    * `model`- 등록 된 모델로 설정합니다.
    * `inference_config`- 모델의 추론 구성으로 설정합니다.

    이러한 변수 설정에 대한 자세한 내용은 [모델을 배포하는 방법 및 위치를](how-to-deploy-and-where.md)참조하십시오.

- 이 문서의 __CLI__ 코드 조각은 문서를 만들었다고 `inferenceconfig.json` 가정합니다. 이 문서를 만드는 방법에 대한 자세한 내용은 [모델을 배포하는 방법 및 위치를](how-to-deploy-and-where.md)참조하십시오.

## <a name="create-a-new-aks-cluster"></a>새 AKS 클러스터 만들기

**예상 시간**: 약 20분.

AKS 클러스터를 만들거나 연결하는 것은 작업 영역에 대한 일회성 프로세스입니다. 이 클러스터를 여러 배포에 재사용할 수 있습니다. 클러스터 또는 클러스터가 포함된 리소스 그룹을 삭제하는 경우 다음에 배포해야 할 때 새 클러스터를 만들어야 합니다. 작업 영역에 여러 AKS 클러스터를 연결할 수 있습니다.

> [!TIP]
> Azure 가상 네트워크를 사용하여 AKS 클러스터를 보호하려면 먼저 가상 네트워크를 만들어야 합니다. 자세한 내용은 [Azure 가상 네트워크 의 보안 실험 및 추론을](how-to-enable-virtual-network.md#aksvnet)참조하십시오.

프로덕션 대신 __개발,__ __유효성 검사__및 __테스트를__ 위해 AKS 클러스터를 만들려면 __테스트를 개발할__ __클러스터 목적을__ 지정할 수 있습니다.

> [!WARNING]
> 설정된 `cluster_purpose = AksCompute.ClusterPurpose.DEV_TEST`경우 생성된 클러스터는 프로덕션 수준 트래픽에 적합하지 않으며 추론 시간이 증가할 수 있습니다. 개발/테스트 클러스터도 내결함성을 보장하지 않습니다. 개발/테스트 클러스터에는 최소 2개의 가상 CPU를 권장합니다.

다음 예제에서는 SDK 및 CLI를 사용하여 새 AKS 클러스터를 만드는 방법을 보여 줍니다.

**SDK 사용**

```python
from azureml.core.compute import AksCompute, ComputeTarget

# Use the default configuration (you can also provide parameters to customize this).
# For example, to create a dev/test cluster, use:
# prov_config = AksCompute.provisioning_configuration(cluster_purpose = AksCompute.ClusterPurpose.DEV_TEST)
prov_config = AksCompute.provisioning_configuration()

aks_name = 'myaks'
# Create the cluster
aks_target = ComputeTarget.create(workspace = ws,
                                    name = aks_name,
                                    provisioning_configuration = prov_config)

# Wait for the create process to complete
aks_target.wait_for_completion(show_output = True)
```

> [!IMPORTANT]
> 에 [`provisioning_configuration()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.akscompute?view=azure-ml-py) `agent_count` 대 `vm_size`한 사용자 지정 값을 `cluster_purpose` 선택 `DEV_TEST`하 고 하지 `agent_count` 않는 경우 `vm_size` 다음 12 가상 CPU보다 크거나 같을 수 있는지 확인 해야 합니다. 예를 들어 가상 CPU가 4개 인 "Standard_D3_v2"을 사용하는 `vm_size` 경우 3개 이상 을 `agent_count` 선택해야 합니다.
>
> Azure 기계 학습 SDK는 AKS 클러스터를 확장하는 지원을 제공하지 않습니다. 클러스터의 노드를 확장하려면 Azure 기계 학습 스튜디오에서 AKS 클러스터에 대한 UI를 사용합니다. 클러스터의 VM 크기가 아니라 노드 수만 변경할 수 있습니다.

이 예제에서 사용되는 클래스, 메서드 및 매개 변수에 대한 자세한 내용은 다음 참조 문서를 참조하십시오.

* [AksCompute.클러스터목적](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.aks.akscompute.clusterpurpose?view=azure-ml-py)
* [악스컴테.provisioning_configuration](/python/api/azureml-core/azureml.core.compute.akscompute?view=azure-ml-py#attach-configuration-resource-group-none--cluster-name-none--resource-id-none--cluster-purpose-none-)
* [계산 대상.create](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.computetarget?view=azure-ml-py#create-workspace--name--provisioning-configuration-)
* [계산대상.wait_for_completion](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.computetarget?view=azure-ml-py#wait-for-completion-show-output-false-)

**CLI 사용**

```azurecli
az ml computetarget create aks -n myaks
```

자세한 내용은 az [ml 계산 대상 만들기 aks 참조를](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/computetarget/create?view=azure-cli-latest#ext-azure-cli-ml-az-ml-computetarget-create-aks) 참조하십시오.

## <a name="attach-an-existing-aks-cluster"></a>기존 AKS 클러스터 연결

**예상 시간:** 약 5 분.

Azure 구독에 AKS 클러스터가 이미 있고 버전 1.17 이하인 경우 이미지를 배포하는 데 사용할 수 있습니다.

> [!TIP]
> 기존 AKS 클러스터는 Azure 기계 학습 작업 영역이 아닌 Azure 지역에 있을 수 있습니다.
>
> Azure 가상 네트워크를 사용하여 AKS 클러스터를 보호하려면 먼저 가상 네트워크를 만들어야 합니다. 자세한 내용은 [Azure 가상 네트워크 의 보안 실험 및 추론을](how-to-enable-virtual-network.md#aksvnet)참조하십시오.

AKS 클러스터를 작업 공간에 연결할 때 `cluster_purpose` 매개 변수를 설정하여 클러스터를 사용하는 방법을 정의할 수 있습니다.

매개 변수를 `cluster_purpose` 설정하지 않거나 `cluster_purpose = AksCompute.ClusterPurpose.FAST_PROD`설정하지 않으면 클러스터에 최소 12개의 가상 CPU가 있어야 합니다.

설정하면 `cluster_purpose = AksCompute.ClusterPurpose.DEV_TEST`클러스터에 12개의 가상 CPU가 필요하지 않습니다. 개발/테스트를 위해 최소 2개의 가상 CPU를 권장합니다. 그러나 개발/테스트를 위해 구성된 클러스터는 프로덕션 수준 트래픽에 적합하지 않으며 추론 시간이 늘어까요. 개발/테스트 클러스터도 내결함성을 보장하지 않습니다.

> [!WARNING]
> 작업 영역에서 동일한 AKS 클러스터에 대한 여러 개의 동시 첨부 파일을 만들지 마십시오. 예를 들어 두 개의 서로 다른 이름을 사용하여 하나의 AKS 클러스터를 작업 영역에 연결합니다. 각 새 첨부 파일은 이전 기존 첨부 파일을 중단합니다.
>
> TLS 또는 다른 클러스터 구성 설정을 변경하기 위해 AKS 클러스터를 다시 연결하려면 먼저 [AksCompute.detach()를](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.akscompute?view=azure-ml-py#detach--)사용하여 기존 첨부 파일을 제거해야 합니다.

Azure CLI 또는 포털을 사용하여 AKS 클러스터를 만드는 방법에 대한 자세한 내용은 다음 문서를 참조하십시오.

* [AKS 클러스터 만들기(CLI)](https://docs.microsoft.com/cli/azure/aks?toc=%2Fazure%2Faks%2FTOC.json&bc=%2Fazure%2Fbread%2Ftoc.json&view=azure-cli-latest#az-aks-create)
* [AKS 클러스터 만들기(포털)](https://docs.microsoft.com/azure/aks/kubernetes-walkthrough-portal?view=azure-cli-latest)

다음 예제에서는 기존 AKS 클러스터를 작업 영역에 연결하는 방법을 보여 줍니다.

**SDK 사용**

```python
from azureml.core.compute import AksCompute, ComputeTarget
# Set the resource group that contains the AKS cluster and the cluster name
resource_group = 'myresourcegroup'
cluster_name = 'myexistingcluster'

# Attach the cluster to your workgroup. If the cluster has less than 12 virtual CPUs, use the following instead:
# attach_config = AksCompute.attach_configuration(resource_group = resource_group,
#                                         cluster_name = cluster_name,
#                                         cluster_purpose = AksCompute.ClusterPurpose.DEV_TEST)
attach_config = AksCompute.attach_configuration(resource_group = resource_group,
                                         cluster_name = cluster_name)
aks_target = ComputeTarget.attach(ws, 'myaks', attach_config)
```

이 예제에서 사용되는 클래스, 메서드 및 매개 변수에 대한 자세한 내용은 다음 참조 문서를 참조하십시오.

* [악스컴퓨팅.attach_configuration()](/python/api/azureml-core/azureml.core.compute.akscompute?view=azure-ml-py#attach-configuration-resource-group-none--cluster-name-none--resource-id-none--cluster-purpose-none-)
* [AksCompute.클러스터목적](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.aks.akscompute.clusterpurpose?view=azure-ml-py)
* [AksCompute.attach](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.computetarget?view=azure-ml-py#attach-workspace--name--attach-configuration-)

**CLI 사용**

CLI를 사용하여 기존 클러스터를 연결하려면 기존 클러스터의 리소스 ID를 얻어야 합니다. 이 값을 얻으려면 다음 명령을 사용합니다. AKS 클러스터의 이름으로 바꿉니다. `myexistingcluster` 클러스터를 포함하는 리소스 그룹으로 바꿉습니다. `myresourcegroup`

```azurecli
az aks show -n myexistingcluster -g myresourcegroup --query id
```

이 명령은 다음 텍스트와 유사한 값을 반환합니다.

```text
/subscriptions/{GUID}/resourcegroups/{myresourcegroup}/providers/Microsoft.ContainerService/managedClusters/{myexistingcluster}
```

기존 클러스터를 작업 영역에 연결하려면 다음 명령을 사용합니다. 이전 `aksresourceid` 명령에서 반환된 값으로 바꿉습니다. 작업 `myresourcegroup` 영역이 포함된 리소스 그룹으로 바꿉습니다. 작업 `myworkspace` 영역 이름으로 바꿉니다.

```azurecli
az ml computetarget attach aks -n myaks -i aksresourceid -g myresourcegroup -w myworkspace
```

자세한 내용은 az [ml 계산 대상 aks 참조를 참조하십시오.](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/computetarget/attach?view=azure-cli-latest#ext-azure-cli-ml-az-ml-computetarget-attach-aks)

## <a name="deploy-to-aks"></a>AKS에 배포

Azure Kubernetes 서비스에 모델을 배포하려면 필요한 계산 리소스를 설명하는 __배포 구성을__ 만듭니다. 예를 들어 코어 및 메모리 수입니다. 또한 모델 및 웹 서비스를 호스트하는 데 필요한 환경을 설명하는 __추론 구성이__필요합니다. 추론 구성을 만드는 방법에 대한 자세한 내용은 [모델을 배포하는 방법 및 위치를](how-to-deploy-and-where.md)참조하십시오.

### <a name="using-the-sdk"></a>SDK 사용

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

이 예제에서 사용되는 클래스, 메서드 및 매개 변수에 대한 자세한 내용은 다음 참조 문서를 참조하십시오.

* [악스컴테](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.aks.akscompute?view=azure-ml-py)
* [AksWebservice.deploy_configuration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aks.aksservicedeploymentconfiguration?view=azure-ml-py)
* [모델.배포](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#deploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-)
* [웹 서비스.wait_for_deployment](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice%28class%29?view=azure-ml-py#wait-for-deployment-show-output-false-)

### <a name="using-the-cli"></a>CLI 사용

CLI를 사용하여 배포하려면 다음 명령을 사용합니다. AKS 계산 대상의 이름으로 바꿉니다. `myaks` 등록된 `mymodel:1` 모델의 이름 및 버전으로 바꿉니다. 이 `myservice` 서비스를 제공하기 위해 이름으로 바꿉니다.

```azurecli-interactive
az ml model deploy -ct myaks -m mymodel:1 -n myservice -ic inferenceconfig.json -dc deploymentconfig.json
```

[!INCLUDE [deploymentconfig](../../includes/machine-learning-service-aks-deploy-config.md)]

자세한 내용은 az [ml 모델 배포](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/model?view=azure-cli-latest#ext-azure-cli-ml-az-ml-model-deploy) 참조를 참조하십시오.

### <a name="using-vs-code"></a>VS Code 사용

VS 코드 사용에 대한 자세한 내용은 [VS 코드 확장을 통해 AKS에 배포를](tutorial-train-deploy-image-classification-model-vscode.md#deploy-the-model)참조하십시오.

> [!IMPORTANT]
> VS 코드를 통해 배포하려면 AKS 클러스터를 미리 만들거나 작업 영역에 연결해야 합니다.

## <a name="deploy-models-to-aks-using-controlled-rollout-preview"></a>제어된 롤아웃(미리 보기)을 사용하여 AKS에 모델 배포
끝점을 사용하여 제어된 방식으로 모델 버전을 분석하고 승격합니다. 단일 엔드포인트 뒤에 최대 6개의 버전을 배포하고 배포된 각 버전에 대한 트래픽 점수 비율을 구성합니다. 앱 인사이트를 활성화하여 엔드포인트 및 배포된 버전의 운영 메트릭을 볼 수 있습니다.

### <a name="create-an-endpoint"></a>엔드포인트 만들기
모델을 배포할 준비가 되면 점수 매기기 엔드포인트를 만들고 첫 번째 버전을 배포합니다. 아래 단계에서는 SDK를 사용하여 끝점을 배포하고 만드는 방법을 보여 주어 있습니다. 첫 번째 배포는 모든 버전에서 지정되지 않은 트래픽 백분위수로 기본 버전으로 이동한다는 것을 의미하는 기본 버전으로 정의됩니다.  

```python
import azureml.core,
from azureml.core.webservice import AksEndpoint
from azureml.core.compute import AksCompute
from azureml.core.compute import ComputeTarget
# select a created compute
compute = ComputeTarget(ws, 'myaks')
namespace_name= endpointnamespace
# define the endpoint and version name
endpoint_name = "mynewendpoint",
version_name= "versiona",
# create the deployment config and define the scoring traffic percentile for the first deployment
endpoint_deployment_config = AksEndpoint.deploy_configuration(cpu_cores = 0.1, memory_gb = 0.2,
                                                              enable_app_insights = True,
                                                              tags = {'sckitlearn':'demo'},
                                                              description = "testing versions",
                                                              version_name = version_name,
                                                              traffic_percentile = 20)
 # deploy the model and endpoint
 endpoint = Model.deploy(ws, endpoint_name, [model], inference_config, endpoint_deployment_config, compute)
 ```

### <a name="update-and-add-versions-to-an-endpoint"></a>끝점에 버전 업데이트 및 추가

끝점에 다른 버전을 추가하고 버전으로 가는 점수 매기기 트래픽 백분위수로 구성합니다. 버전에는 컨트롤과 치료 버전이라는 두 가지 유형이 있습니다. 단일 제어 버전과 비교하는 데 도움이 되는 여러 치료 버전이 있을 수 있습니다.

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
```

기존 버전을 업데이트하거나 끝점에서 삭제합니다. 버전의 기본 유형, 제어 유형 및 트래픽 백분위수(백분율)를 변경할 수 있습니다.

 ```python
from azureml.core.webservice import AksEndpoint

# update the version's scoring traffic percentage and if it is a default or control type
endpoint.update_version(version_name=endpoint.versions["versionb"].name,
                        description="my second version update",
                        traffic_percentile=40,
                        is_default=True,
                        is_control_version_type=True)

# delete a version in an endpoint
endpoint.delete_version(version_name="versionb")

```


## <a name="web-service-authentication"></a>웹 서비스 인증

Azure Kubernetes 서비스에 배포할 때 __키 기반__ 인증은 기본적으로 활성화됩니다. __토큰 기반__ 인증을 활성화할 수도 있습니다. 토큰 기반 인증을 사용하려면 클라이언트가 Azure Active Directory 계정을 사용하여 배포된 서비스에 대한 요청을 하는 데 사용되는 인증 토큰을 요청해야 합니다.

인증을 __사용하지 않으려면__ 배포 구성을 `auth_enabled=False` 만들 때 매개 변수를 설정합니다. 다음 예제는 SDK를 사용 하 여 인증을 사용 하지 않도록 설정 합니다.

```python
deployment_config = AksWebservice.deploy_configuration(cpu_cores=1, memory_gb=1, auth_enabled=False)
```

클라이언트 응용 프로그램에서 인증하는 방법에 대한 자세한 내용은 [웹 서비스로 배포된 Azure 기계 학습 모델 사용](how-to-consume-web-service.md)을 참조하십시오.

### <a name="authentication-with-keys"></a>키로 인증

키 인증을 사용하도록 설정한 경우 `get_keys` 메서드를 사용하여 기본 및 보조 인증 키를 검색할 수 있습니다.

```python
primary, secondary = service.get_keys()
print(primary)
```

> [!IMPORTANT]
> 키를 다시 생성해야 하는 경우[`service.regen_key`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice(class)?view=azure-ml-py)

### <a name="authentication-with-tokens"></a>토큰으로 인증

토큰 인증을 사용하려면 `token_auth_enabled=True` 배포를 만들거나 업데이트할 때 매개 변수를 설정합니다. 다음 예제는 SDK를 사용하여 토큰 인증을 활성화합니다.

```python
deployment_config = AksWebservice.deploy_configuration(cpu_cores=1, memory_gb=1, token_auth_enabled=True)
```

토큰 인증이 활성화된 경우 `get_token` 메서드를 사용하여 JWT 토큰및 해당 토큰의 만료 시간을 검색할 수 있습니다.

```python
token, refresh_by = service.get_token()
print(token)
```

> [!IMPORTANT]
> 토큰의 시간 이후에 새 토큰을 `refresh_by` 요청해야 합니다.
>
> Azure Kubernetes 서비스 클러스터와 동일한 지역에 Azure 기계 학습 작업 영역을 만드는 것이 좋습니다. 토큰으로 인증하기 위해 웹 서비스는 Azure Machine Learning 작업 영역이 만들어지는 지역을 호출합니다. 작업 영역을 사용할 수 없는 경우 클러스터가 작업 영역과 다른 지역에 있더라도 웹 서비스에 대한 토큰을 가져올 수 없습니다. 이렇게 하면 작업 영역의 영역을 다시 사용할 수 있게 될 때까지 토큰 기반 인증을 사용할 수 없게 됩니다. 또한 클러스터의 영역과 작업 영역 간의 거리가 클수록 토큰을 가져오는 데 더 오래 걸릴 수 있습니다.

## <a name="update-the-web-service"></a>웹 서비스 업데이트

[!INCLUDE [aml-update-web-service](../../includes/machine-learning-update-web-service.md)]

## <a name="next-steps"></a>다음 단계

* [가상 네트워크에서 실험 및 추론 보안](how-to-enable-virtual-network.md)
* [사용자 지정 Docker 이미지를 사용하여 모델을 배포하는 방법](how-to-deploy-custom-docker-image.md)
* [배포 문제 해결](how-to-troubleshoot-deployment.md)
* [TLS를 사용하여 Azure 기계 학습을 통해 웹 서비스 보호](how-to-secure-web-service.md)
* [웹 서비스로 배포된 ML 모델 사용](how-to-consume-web-service.md)
* [Application Insights를 사용하여 Azure Machine Learning 모델 모니터링](how-to-enable-app-insights.md)
* [프로덕션 환경에서 모델용 데이터 수집](how-to-enable-data-collection.md)
