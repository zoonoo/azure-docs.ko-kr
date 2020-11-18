---
title: Azure Kubernetes Service 만들기 및 연결
titleSuffix: Azure Machine Learning
description: Azure Machine Learning를 통해 새 Azure Kubernetes Service 클러스터를 만드는 방법 또는 작업 영역에 기존 AKS 클러스터를 연결 하는 방법에 대해 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to, devx-track-azurecli
ms.author: jordane
author: jpe316
ms.reviewer: larryfr
ms.date: 10/02/2020
ms.openlocfilehash: c8b3ab965c5a85bd6f25e5325fdca24026aec787
ms.sourcegitcommit: 642988f1ac17cfd7a72ad38ce38ed7a5c2926b6c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/18/2020
ms.locfileid: "94873831"
---
# <a name="create-and-attach-an-azure-kubernetes-service-cluster"></a>Azure Kubernetes Service 클러스터 만들기 및 연결

Azure Kubernetes Service에 학습 된 기계 학습 모델을 배포할 수 Azure Machine Learning. 그러나 먼저 Azure ML 작업 영역에서 AKS (Azure Kubernetes Service) 클러스터를 __만들거나__ 기존 AKS 클러스터를 __연결__ 해야 합니다. 이 문서에서는 클러스터를 만들고 연결 하는 방법에 대 한 정보를 제공 합니다.

## <a name="prerequisites"></a>사전 요구 사항

- Azure Machine Learning 작업 영역 자세한 내용은 [Azure Machine Learning 작업 영역 만들기](how-to-manage-workspace.md)를 참조 하세요.

- Machine Learning 서비스, [Azure Machine Learning PYTHON SDK](/python/api/overview/azure/ml/intro?preserve-view=true&view=azure-ml-py)또는 [Azure Machine Learning Visual Studio Code 확장](tutorial-setup-vscode-extension.md) [에 대 한 Azure CLI 확장](reference-azure-machine-learning-cli.md)입니다.

- Azure Virtual Network을 사용 하 여 Azure ML 작업 영역 및 AKS 클러스터 간의 통신을 보호 하려는 경우 [학습 & 유추 문서에서 네트워크 격리](./how-to-network-security-overview.md) 를 읽어 보세요.

## <a name="limitations"></a>제한 사항

- BLB (기본 Load Balancer) 대신 클러스터에 **표준 Load Balancer (SLB)** 를 배포 해야 하는 경우 AKS PORTAL/CLI/SDK에서 클러스터를 만든 다음 AML 작업 영역에 **연결** 합니다.

- 공용 IP 주소 만들기를 제한 하는 Azure Policy 있는 경우 AKS 클러스터를 만들 수 없습니다. AKS에는 [송신 트래픽에](../aks/limit-egress-traffic.md)대 한 공용 IP가 필요 합니다. 또한 송신 트래픽 문서에서는 일부 정규화 된 도메인 이름을 제외 하 고 공용 IP를 통해 클러스터에서 송신 트래픽을 잠그는 지침을 제공 합니다. 공용 IP를 사용 하도록 설정 하는 방법에는 두 가지가 있습니다.
    - 클러스터는 기본적으로 BLB 또는 SLB와 함께 생성 된 공용 IP를 사용할 수 있습니다.
    - 공용 ip 없이 클러스터를 만들 수 있으며, 공용 IP는 사용자 정의 경로를 사용 하 여 방화벽으로 구성 됩니다. 자세한 내용은 [사용자 정의 경로를 사용 하 여 클러스터 송신 사용자 지정](../aks/egress-outboundtype.md)을 참조 하세요.
    
    AML 컨트롤 평면은이 공용 IP와 통신 하지 않습니다. 배포에 대 한 AKS 제어 평면과 통신 합니다. 

- [API 서버에 액세스할 수 있는 권한이 부여 된 IP 범위가](../aks/api-server-authorized-ip-ranges.md)있는 AKS 클러스터를 **연결** 하는 경우 AKS 클러스터에 대해 AML 제어 평면 IP 범위를 사용 하도록 설정 합니다. AML 컨트롤 평면은 쌍을 이루는 지역에 배포 되 고 AKS 클러스터에 유추 pod를 배포 합니다. API 서버에 대 한 액세스 권한이 없으면 유추 pod를 배포할 수 없습니다. AKS 클러스터에서 IP 범위를 사용 하도록 설정 하는 경우 [쌍을 이루는 지역](../best-practices-availability-paired-regions.md) 에 대 한 [ip 범위](https://www.microsoft.com/download/confirmation.aspx?id=56519) 를 사용 합니다.

    권한 있는 IP 범위는 표준 Load Balancer 에서만 작동 합니다.

- Azure 개인 링크를 사용 하 여 개인 AKS 클러스터를 사용 하려면 먼저 클러스터를 만든 다음 작업 영역에 **연결** 해야 합니다. 자세한 내용은 [개인 Azure Kubernetes Service 클러스터 만들기](../aks/private-clusters.md)를 참조 하세요.

- AKS 클러스터에 대 한 계산 이름은 Azure ML 작업 영역 내에서 고유 해야 합니다.
    - 이름은 필수 이며 길이가 3 ~ 007e; 24 자 사이 여야 합니다.
    - 유효한 문자는 대 문자와 소문자, 숫자 및 문자입니다.
    - 이름은 문자로 시작 해야 합니다.
    - 이름은 Azure 지역 내의 모든 기존 계산에서 고유 해야 합니다. 선택한 이름이 고유 하지 않으면 경고가 표시 됩니다.
   
 - **GPU** 노드나 **FPGA** 노드 (또는 특정 sku)에 모델을 배포 하려면 특정 sku를 사용 하 여 클러스터를 만들어야 합니다. 기존 클러스터에 보조 노드 풀을 만들고 보조 노드 풀에 모델을 배포 하는 것은 지원 되지 않습니다.
 
- 클러스터를 만들거나 연결할 때 __개발-테스트__ 또는 __프로덕션__ 을 위해 클러스터를 만들지 여부를 선택할 수 있습니다. 프로덕션 대신 __개발__, __유효성 검사__ 및 __테스트__ 를 위해 AKS 클러스터를 만들려는 경우 __클러스터 용도__ 를 개발 __-테스트__ 로 설정 합니다. 클러스터 용도를 지정 하지 않으면 __프로덕션__ 클러스터가 만들어집니다. 

    > [!IMPORTANT]
    > __개발-테스트__ 클러스터는 프로덕션 수준 트래픽에 적합 하지 않으며 유추 시간이 늘어날 수 있습니다. 또한 개발/테스트 클러스터는 내결함성을 보장 하지 않습니다.

- 클러스터를 만들거나 연결할 때 클러스터가 __프로덕션__ 에 사용 되는 경우에는 __가상 cpu__ 를 12 개 이상 포함 해야 합니다. 클러스터의 __노드 수__ 를 선택한 VM 크기에서 제공 하는 __코어 수__ 와 곱하여 가상 cpu 수를 계산할 수 있습니다. 예를 들어 가상 코어가 4 개 있는 "Standard_D3_v2"의 VM 크기를 사용 하는 경우 노드 수로 3 이상을 선택 해야 합니다.

    __개발-테스트__ 클러스터의 경우 2 개 이상의 가상 cpu를 다시 명령 합니다.

- Azure Machine Learning SDK는 AKS 클러스터의 크기 조정을 지원 하지 않습니다. 클러스터의 노드 크기를 조정 하려면 Azure Machine Learning studio에서 AKS 클러스터에 대 한 UI를 사용 합니다. 클러스터의 VM 크기가 아니라 노드 수만 변경할 수 있습니다. AKS 클러스터의 노드 크기를 조정 하는 방법에 대 한 자세한 내용은 다음 문서를 참조 하세요.

    - [AKS 클러스터에서 수동으로 노드 수 크기 조정](../aks/scale-cluster.md)
    - [AKS에서 cluster autoscaler 설정](../aks/cluster-autoscaler.md)

## <a name="azure-kubernetes-service-version"></a>Azure Kubernetes 서비스 버전

Azure Kubernetes 서비스를 사용 하면 다양 한 Kubernetes 버전을 사용 하 여 클러스터를 만들 수 있습니다. 사용 가능한 버전에 대 한 자세한 내용은 [Azure Kubernetes Service에서 지원 되는 Kubernetes 버전](../aks/supported-kubernetes-versions.md)을 참조 하세요.

다음 방법 중 하나를 사용 하 여 Azure Kubernetes 서비스 클러스터를 **만들** 때 생성 되는 클러스터의 *버전에서 선택할 수 없습니다* .

* Azure Machine Learning studio 또는 Azure Portal의 Azure Machine Learning 섹션입니다.
* Azure CLI에 대 한 Machine Learning 확장입니다.
* SDK를 Azure Machine Learning 합니다.

AKS 클러스터를 만드는 이러한 메서드는 클러스터의 __기본__ 버전을 사용 합니다. 새 Kubernetes 버전을 사용할 수 있게 되 면 *기본 버전은 시간이 지남에 따라 변경* 됩니다.

기존 AKS 클러스터를 **연결할** 때 현재 지원 되는 모든 AKS 버전을 지원 합니다.

> [!NOTE]
> 더 이상 지원 되지 않는 이전 클러스터가 있는 경우에는에 지 사례가 있을 수 있습니다. 이 경우 연결 작업은 오류를 반환 하 고 현재 지원 되는 버전을 나열 합니다.
>
> **미리 보기** 버전을 연결할 수 있습니다. 미리 보기 기능은 서비스 수준 계약 없이 제공 되며 프로덕션 워크 로드에는 권장 되지 않습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다. 미리 보기 버전 사용에 대 한 지원이 제한 될 수 있습니다. 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

### <a name="available-and-default-versions"></a>사용 가능한 버전 및 기본 버전

사용 가능한 및 기본 AKS 버전을 찾으려면 [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest&preserve-view=true) 명령 [az AKS](/cli/azure/aks?view=azure-cli-latest&preserve-view=true#az_aks_get_versions)를 사용 합니다. 예를 들어 다음 명령은 미국 서 부 지역에서 사용할 수 있는 버전을 반환 합니다.

```azurecli-interactive
az aks get-versions -l westus -o table
```

이 명령의 출력은 다음 텍스트와 비슷합니다.

```text
KubernetesVersion    Upgrades
-------------------  ----------------------------------------
1.18.6(preview)      None available
1.18.4(preview)      1.18.6(preview)
1.17.9               1.18.4(preview), 1.18.6(preview)
1.17.7               1.17.9, 1.18.4(preview), 1.18.6(preview)
1.16.13              1.17.7, 1.17.9
1.16.10              1.16.13, 1.17.7, 1.17.9
1.15.12              1.16.10, 1.16.13
1.15.11              1.15.12, 1.16.10, 1.16.13
```

Azure Machine Learning를 통해 클러스터를 **만들** 때 사용 되는 기본 버전을 찾으려면 `--query` 매개 변수를 사용 하 여 기본 버전을 선택할 수 있습니다.

```azurecli-interactive
az aks get-versions -l westus --query "orchestrators[?default == `true`].orchestratorVersion" -o table
```

이 명령의 출력은 다음 텍스트와 비슷합니다.

```text
Result
--------
1.16.13
```

**사용 가능한 버전을 프로그래밍 방식으로 확인** 하려면 [컨테이너 서비스 클라이언트 목록 orchestrator](/rest/api/container-service/container%20service%20client/listorchestrators) REST API를 사용 합니다. 사용 가능한 버전을 찾으려면 항목을 확인 `orchestratorType` 합니다. 여기서는 `Kubernetes` 입니다. 연결 된 `orchestrationVersion` 항목에는 작업 영역에 연결할 수 **attached** 있는 사용 가능한 버전이 포함 되어 있습니다.

Azure Machine Learning를 통해 클러스터를 **만들** 때 사용 되는 기본 버전을 찾으려면 `orchestratorType` 가이 `Kubernetes` 고 `default` 가 인 항목을 찾습니다 `true` . 연결 된 `orchestratorVersion` 값이 기본 버전입니다. 다음 JSON 코드 조각은 예제 항목을 보여 줍니다.

```json
...
 {
        "orchestratorType": "Kubernetes",
        "orchestratorVersion": "1.16.13",
        "default": true,
        "upgrades": [
          {
            "orchestratorType": "",
            "orchestratorVersion": "1.17.7",
            "isPreview": false
          }
        ]
      },
...
```

## <a name="create-a-new-aks-cluster"></a>새 AKS 클러스터 만들기

**예상 시간**: 약 10 분

작업 영역에 대 한 일회성 프로세스는 AKS 클러스터를 만들거나 연결 하는 것입니다. 이 클러스터를 여러 배포에 재사용할 수 있습니다. 클러스터 또는 클러스터를 포함 하는 리소스 그룹을 삭제 하는 경우 다음에를 배포 해야 할 때 새 클러스터를 만들어야 합니다. 여러 AKS 클러스터를 작업 영역에 연결할 수 있습니다.

다음 예제에서는 SDK 및 CLI를 사용 하 여 새 AKS 클러스터를 만드는 방법을 보여 줍니다.

# <a name="python"></a>[Python](#tab/python)

```python
from azureml.core.compute import AksCompute, ComputeTarget

# Use the default configuration (you can also provide parameters to customize this).
# For example, to create a dev/test cluster, use:
# prov_config = AksCompute.provisioning_configuration(cluster_purpose = AksCompute.ClusterPurpose.DEV_TEST)
prov_config = AksCompute.provisioning_configuration()

# Example configuration to use an existing virtual network
# prov_config.vnet_name = "mynetwork"
# prov_config.vnet_resourcegroup_name = "mygroup"
# prov_config.subnet_name = "default"
# prov_config.service_cidr = "10.0.0.0/16"
# prov_config.dns_service_ip = "10.0.0.10"
# prov_config.docker_bridge_cidr = "172.17.0.1/16"

aks_name = 'myaks'
# Create the cluster
aks_target = ComputeTarget.create(workspace = ws,
                                    name = aks_name,
                                    provisioning_configuration = prov_config)

# Wait for the create process to complete
aks_target.wait_for_completion(show_output = True)
```

이 예제에 사용 된 클래스, 메서드 및 매개 변수에 대 한 자세한 내용은 다음 참조 문서를 참조 하세요.

* [AksCompute](/python/api/azureml-core/azureml.core.compute.aks.akscompute.clusterpurpose?preserve-view=true&view=azure-ml-py)
* [AksCompute.provisioning_configuration](/python/api/azureml-core/azureml.core.compute.akscompute?view=azure-ml-py&preserve-view=true#attach-configuration-resource-group-none--cluster-name-none--resource-id-none--cluster-purpose-none-)
* [ComputeTarget](/python/api/azureml-core/azureml.core.compute.computetarget?preserve-view=true&view=azure-ml-py#create-workspace--name--provisioning-configuration-)
* [ComputeTarget.wait_for_completion](/python/api/azureml-core/azureml.core.compute.computetarget?preserve-view=true&view=azure-ml-py#wait-for-completion-show-output-false-)

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az ml computetarget create aks -n myaks
```

자세한 내용은 [az ml computetarget create aks](/cli/azure/ext/azure-cli-ml/ml/computetarget/create?preserve-view=true&view=azure-cli-latest#ext-azure-cli-ml-az-ml-computetarget-create-aks) reference를 참조 하세요.

# <a name="portal"></a>[포털](#tab/azure-portal)

포털에서 AKS 클러스터를 만드는 방법에 대 한 자세한 내용은 [Azure Machine Learning studio에서 계산 대상 만들기](how-to-create-attach-compute-studio.md#inference-clusters)를 참조 하세요.

---

## <a name="attach-an-existing-aks-cluster"></a>기존 AKS 클러스터 연결

**예상 시간:** 약 5 분.

Azure 구독에 AKS 클러스터가 이미 있고 버전이 1.17 이하인 경우이를 사용 하 여 이미지를 배포할 수 있습니다.

> [!TIP]
> 기존 AKS 클러스터는 Azure Machine Learning 작업 영역 이외의 Azure 지역에 있을 수 있습니다.


> [!WARNING]
> 작업 영역에서 동일한 AKS 클러스터에 대 한 동시 첨부 파일을 여러 개 만들지 마십시오. 예를 들어 두 개의 다른 이름을 사용 하 여 하나의 AKS 클러스터를 작업 영역에 연결 합니다. 새 첨부 파일은 이전의 기존 첨부 파일을 중단 합니다.
>
> AKS 클러스터를 다시 연결 하려는 경우 (예: TLS 또는 다른 클러스터 구성 설정을 변경 하려면 [) AksCompute ()](/python/api/azureml-core/azureml.core.compute.akscompute?preserve-view=true&view=azure-ml-py#detach--)를 사용 하 여 기존 첨부 파일을 제거 해야 합니다.

Azure CLI 또는 포털을 사용 하 여 AKS 클러스터를 만드는 방법에 대 한 자세한 내용은 다음 문서를 참조 하세요.

* [AKS 클러스터 만들기(CLI)](/cli/azure/aks?bc=%252fazure%252fbread%252ftoc.json&preserve-view=true&toc=%252fazure%252faks%252fTOC.json&view=azure-cli-latest#az-aks-create)
* [AKS 클러스터 만들기 (포털)](../aks/kubernetes-walkthrough-portal.md?preserve-view=true&view=azure-cli-latest)
* [AKS 클러스터 만들기 (Azure 빠른 시작 템플릿의 ARM 템플릿)](https://github.com/Azure/azure-quickstart-templates/tree/master/101-aks-azml-targetcompute)

다음 예제에서는 기존 AKS 클러스터를 작업 영역에 연결 하는 방법을 보여 줍니다.

# <a name="python"></a>[Python](#tab/python)

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

# Wait for the attach process to complete
aks_target.wait_for_completion(show_output = True)
```

이 예제에 사용 된 클래스, 메서드 및 매개 변수에 대 한 자세한 내용은 다음 참조 문서를 참조 하세요.

* [AksCompute.attach_configuration ()](/python/api/azureml-core/azureml.core.compute.akscompute?view=azure-ml-py&preserve-view=true#attach-configuration-resource-group-none--cluster-name-none--resource-id-none--cluster-purpose-none-)
* [AksCompute](/python/api/azureml-core/azureml.core.compute.aks.akscompute.clusterpurpose?preserve-view=true&view=azure-ml-py)
* [AksCompute](/python/api/azureml-core/azureml.core.compute.computetarget?preserve-view=true&view=azure-ml-py#attach-workspace--name--attach-configuration-)

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

CLI를 사용 하 여 기존 클러스터를 연결 하려면 기존 클러스터의 리소스 ID를 가져와야 합니다. 이 값을 가져오려면 다음 명령을 사용 합니다. `myexistingcluster`을 AKS 클러스터의 이름으로 바꿉니다. `myresourcegroup`을 클러스터를 포함 하는 리소스 그룹으로 바꿉니다.

```azurecli
az aks show -n myexistingcluster -g myresourcegroup --query id
```

이 명령은 다음 텍스트와 유사한 값을 반환합니다.

```text
/subscriptions/{GUID}/resourcegroups/{myresourcegroup}/providers/Microsoft.ContainerService/managedClusters/{myexistingcluster}
```

기존 클러스터를 작업 영역에 연결 하려면 다음 명령을 사용 합니다. `aksresourceid`이전 명령에서 반환 된 값으로 대체 합니다. 을 `myresourcegroup` 작업 영역을 포함 하는 리소스 그룹으로 바꿉니다. `myworkspace`을 작업 영역 이름으로 바꿉니다.

```azurecli
az ml computetarget attach aks -n myaks -i aksresourceid -g myresourcegroup -w myworkspace
```

자세한 내용은 [az ml computetarget attach aks](/cli/azure/ext/azure-cli-ml/ml/computetarget/attach?preserve-view=true&view=azure-cli-latest#ext-azure-cli-ml-az-ml-computetarget-attach-aks) reference를 참조 하세요.

# <a name="portal"></a>[포털](#tab/azure-portal)

포털에서 AKS 클러스터를 연결 하는 방법에 대 한 자세한 내용은 [Azure Machine Learning studio에서 계산 대상 만들기](how-to-create-attach-compute-studio.md#inference-clusters)를 참조 하세요.

---

## <a name="detach-an-aks-cluster"></a>AKS 클러스터 분리

작업 영역에서 클러스터를 분리 하려면 다음 방법 중 하나를 사용 합니다.

> [!WARNING]
> 기계 학습을 위해 Azure Machine Learning studio, SDK 또는 Azure CLI 확장을 사용 하 여 AKS 클러스터를 분리 **해도 AKS 클러스터는 삭제 되지 않습니다**. 클러스터를 삭제 하려면 [AKS와 함께 Azure CLI 사용](../aks/kubernetes-walkthrough.md#delete-the-cluster)을 참조 하세요.

# <a name="python"></a>[Python](#tab/python)

```python
aks_target.detach()
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

기존 클러스터를 작업 영역으로 분리 하려면 다음 명령을 사용 합니다. `myaks`AKS 클러스터가 작업 영역에 연결 된 이름으로 대체 합니다. 을 `myresourcegroup` 작업 영역을 포함 하는 리소스 그룹으로 바꿉니다. `myworkspace`을 작업 영역 이름으로 바꿉니다.

```azurecli
az ml computetarget detach -n myaks -g myresourcegroup -w myworkspace
```

# <a name="portal"></a>[포털](#tab/azure-portal)

Azure Machine Learning studio에서 __계산__, __유추 클러스터__ 및 제거 하려는 클러스터를 선택 합니다. __분리__ 링크를 사용 하 여 클러스터를 분리 합니다.

## <a name="next-steps"></a>다음 단계

* [Kubernetes 권한 부여에 Azure RBAC 사용](../aks/manage-azure-rbac.md)
* [모델을 배포 하는 방법 및 위치](how-to-deploy-and-where.md)
* [Azure Kubernetes Service 클러스터에 모델 배포](how-to-deploy-azure-kubernetes-service.md)