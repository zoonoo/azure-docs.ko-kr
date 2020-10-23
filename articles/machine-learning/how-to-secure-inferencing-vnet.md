---
title: 가상 네트워크를 사용 하 여 추론 환경 보호
titleSuffix: Azure Machine Learning
description: 격리 된 Azure Virtual Network를 사용 하 여 Azure Machine Learning 추론 환경을 보호 합니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.reviewer: larryfr
ms.author: peterlu
author: peterclu
ms.date: 10/12/2020
ms.custom: contperfq4, tracking-python, contperfq1
ms.openlocfilehash: 0eb4f8a7994e7c1d04013e9c9cf92e604ef6a1a7
ms.sourcegitcommit: 6906980890a8321dec78dd174e6a7eb5f5fcc029
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2020
ms.locfileid: "92424452"
---
# <a name="secure-an-azure-machine-learning-inferencing-environment-with-virtual-networks"></a>가상 네트워크를 사용 하 여 Azure Machine Learning 추론 환경 보호

이 문서에서는 Azure Machine Learning의 가상 네트워크를 사용 하 여 추론 환경을 보호 하는 방법에 대해 알아봅니다.

이 문서는 Azure Machine Learning 워크플로를 보호 하는 과정을 안내 하는 5 부 시리즈의 4 부입니다. 먼저 전체 아키텍처를 이해 하려면 [1 부: VNet 개요](how-to-network-security-overview.md) 를 읽어 보는 것이 좋습니다. 

이 시리즈의 다른 문서를 참조 하세요.

[1. VNet 개요](how-to-network-security-overview.md)  >  [작업 영역 3을 보호](how-to-secure-workspace-vnet.md)  >  [합니다. 학습 환경 4를 안전 하 게 보호](how-to-secure-training-vnet.md)합니다  >  **. 추론 환경 5를 보호**합니다  >  [. 스튜디오 기능 사용](how-to-enable-studio-virtual-network.md)

이 문서에서는 가상 네트워크에서 다음 추론 리소스의 보안을 유지 하는 방법에 대해 알아봅니다.
> [!div class="checklist"]
> - 기본 AKS (Azure Kubernetes Service) 클러스터
> - 개인 AKS 클러스터
> - 개인 링크를 사용 하는 AKS 클러스터
> - ACI(Azure Container Instances)


## <a name="prerequisites"></a>필수 구성 요소

+ 일반적인 가상 네트워크 시나리오 및 전반적인 가상 네트워크 아키텍처를 이해 하려면 [네트워크 보안 개요](how-to-network-security-overview.md) 문서를 참조 하세요.

+ 계산 리소스에 사용할 기존 가상 네트워크 및 서브넷

+ 가상 네트워크 또는 서브넷에 리소스를 배포 하려면 사용자 계정에 azure 역할 기반 액세스 제어 (Azure RBAC)에서 다음 작업에 대 한 사용 권한이 있어야 합니다.

    - 가상 네트워크 리소스에 대 한 "Microsoft. Network/virtualNetworks/join/action".
    - 서브넷 리소스에 대 한 "Microsoft. Network/virtualNetworks/subnet/join/action".

    네트워킹에 대 한 Azure RBAC에 대 한 자세한 내용은 [네트워킹 기본 제공 역할](/azure/role-based-access-control/built-in-roles#networking) 을 참조 하세요.

<a id="aksvnet"></a>

## <a name="azure-kubernetes-service"></a>Azure Kubernetes Service

가상 네트워크에서 AKS 클러스터를 사용 하려면 다음 네트워크 요구 사항을 충족 해야 합니다.

> [!div class="checklist"]
> * [Azure Kubernetes 서비스에서 고급 네트워킹 구성 (AKS)](../aks/configure-azure-cni.md#prerequisites)의 필수 구성 요소를 따릅니다.
> * AKS 인스턴스와 가상 네트워크는 동일한 지역에 있어야 합니다. 가상 네트워크의 작업 영역에서 사용 하는 Azure Storage 계정의 보안을 유지 하는 경우 AKS 인스턴스와 동일한 가상 네트워크에 있어야 합니다.


가상 네트워크의 AKS를 작업 영역에 추가 하려면 다음 단계를 사용 합니다.

1. [Azure Machine Learning 스튜디오](https://ml.azure.com/)에 로그인한 다음, 구독과 작업 영역을 선택합니다.

1. 왼쪽에서 __Compute__를 선택합니다.

1. 가운데에서 __유추 클러스터__를 선택한 다음, __+__ 를 선택합니다.

1. __새 유추 클러스터__ 대화 상자에서 __네트워크 구성__ 아래 __고급__을 선택합니다.

1. 컴퓨팅 리소스가 가상 네트워크를 사용하도록 구성하려면 다음 작업을 수행합니다.

    1. __리소스 그룹__ 드롭다운 목록에서 가상 네트워크가 포함된 리소스 그룹을 선택합니다.
    1. __가상 네트워크__ 드롭다운 목록에서 서브넷이 포함된 가상 네트워크를 선택합니다.
    1. __서브넷__ 드롭다운 목록에서 서브넷을 선택합니다.
    1. __Kubernetes 서비스 주소 범위__ 상자에 Kubernetes 서비스 주소 범위를 입력합니다. 이 주소 범위는 CIDR(Classless Inter-Domain Routing) 표기법 IP 범위를 사용하여 클러스터에 사용할 수 있는 IP 주소를 정의합니다. 서브넷 IP 범위(예: 10.0.0.0/16)와 겹치지 않아야 합니다.
    1. __Kubernetes DNS 서비스 IP 주소__ 상자에 Kubernetes DNS 서비스 IP 주소를 입력합니다. 이 IP 주소는 Kubernetes DNS 서비스에 할당됩니다. 이 주소는 Kubernetes 서비스 주소 범위(예: 10.0.0.10)에 속해야 합니다.
    1. __Docker 브리지 주소__ 상자에 Docker 브리지 주소를 입력합니다. 이 IP 주소는 Docker 브리지에 할당됩니다. 서브넷 IP 범위 또는 Kubernetes 서비스 주소 범위(예: 172.17.0.1/16)에 속하지 않아야 합니다.

   ![Azure Machine Learning: Machine Learning 컴퓨팅 가상 네트워크 설정](./media/how-to-enable-virtual-network/aks-virtual-network-screen.png)

1. AKS에 웹 서비스로 모델을 배포 하는 경우 추론 요청을 처리 하는 점수 매기기 끝점이 만들어집니다. 가상 네트워크 외부에서 호출 하려는 경우 가상 네트워크를 제어 하는 NSG 그룹에 점수 매기기 끝점의 IP 주소에 대해 사용 하도록 설정 된 인바운드 보안 규칙이 있는지 확인 합니다.

    점수 매기기 끝점의 IP 주소를 찾으려면 배포 된 서비스에 대 한 점수 매기기 URI를 확인 합니다. 점수 매기기 URI를 보는 방법에 대 한 자세한 내용은 [웹 서비스로 배포 된 모델 사용](how-to-consume-web-service.md#connection-information)을 참조 하세요.

   > [!IMPORTANT]
   > NSG에 대한 기본 아웃바운드 규칙을 유지합니다. 자세한 내용은 [보안 그룹](https://docs.microsoft.com/azure/virtual-network/security-overview#default-security-rules)의 기본 보안 규칙을 참조하세요.

   [![인바운드 보안 규칙](./media/how-to-enable-virtual-network/aks-vnet-inbound-nsg-scoring.png)](./media/how-to-enable-virtual-network/aks-vnet-inbound-nsg-scoring.png#lightbox)

    > [!IMPORTANT]
    > 점수 매기기 끝점의 이미지에 표시 되는 IP 주소는 배포에 대해 다릅니다. 동일한 IP가 하나의 AKS 클러스터에 대 한 모든 배포에서 공유 되는 동안 각 AKS 클러스터는 서로 다른 IP 주소를 갖게 됩니다.

Azure Machine Learning SDK를 사용하여 가상 네트워크에 Azure Kubernetes Service를 추가할 수도 있습니다. 가상 네트워크에 AKS 클러스터가 이미 있는 경우 [AKS에 배포하는 방법](how-to-deploy-and-where.md)의 설명에 따라 작업 영역에 연결할 수 있습니다. 다음 코드는 `mynetwork`라는 가상 네트워크의 `default` 서브넷에 새 AKS 인스턴스를 만듭니다.

```python
from azureml.core.compute import ComputeTarget, AksCompute

# Create the compute configuration and set virtual network information
config = AksCompute.provisioning_configuration(location="eastus2")
config.vnet_resourcegroup_name = "mygroup"
config.vnet_name = "mynetwork"
config.subnet_name = "default"
config.service_cidr = "10.0.0.0/16"
config.dns_service_ip = "10.0.0.10"
config.docker_bridge_cidr = "172.17.0.1/16"

# Create the compute target
aks_target = ComputeTarget.create(workspace=ws,
                                  name="myaks",
                                  provisioning_configuration=config)
```

만들기 프로세스가 완료되면 가상 네트워크 뒤에서 AKS 클러스터에 유추 또는 모델 채점을 수행할 수 있습니다. 자세한 내용은 [AKS에 배포하는 방법](how-to-deploy-and-where.md)을 참조하세요.

## <a name="secure-vnet-traffic"></a>VNet 트래픽 보안

AKS 클러스터와 가상 네트워크 간에 트래픽을 격리 하는 방법에는 두 가지가 있습니다.

* __PRIVATE AKS cluster__:이 방법은 Azure 개인 링크를 사용 하 여 배포/관리 작업을 위한 클러스터와의 통신을 보호 합니다.
* __내부 AKS 부하 분산 장치__:이 방법은 가상 네트워크 내에서 개인 IP를 사용 하도록 AKS에 배포 하기 위한 끝점을 구성 합니다.

> [!WARNING]
> **개인 AKS 또는 내부 부하 분산 장치 중 하나만 사용**합니다.

### <a name="private-aks-cluster"></a>개인 AKS 클러스터

기본적으로 AKS 클러스터에는 공용 IP 주소를 사용 하는 제어 평면 또는 API 서버가 있습니다. 개인 AKS 클러스터를 만들어 개인 제어 평면을 사용 하도록 AKS를 구성할 수 있습니다. 자세한 내용은 [개인 Azure Kubernetes Service 클러스터 만들기](../aks/private-clusters.md)를 참조 하세요.

개인 AKS 클러스터를 만든 후에는 [클러스터를 가상 네트워크에 연결](how-to-create-attach-kubernetes.md) 하 여 Azure Machine Learning에 사용 합니다.

> [!IMPORTANT]
> Azure Machine Learning에서 개인 링크 사용 AKS 클러스터를 사용 하려면 먼저 지원 인시던트를 열어이 기능을 사용 하도록 설정 해야 합니다. 자세한 내용은 [할당량 관리 및 늘리기](how-to-manage-quotas.md#private-endpoint-and-private-dns-quota-increases)를 참조 하세요.

## <a name="internal-aks-load-balancer"></a>내부 AKS 부하 분산 장치

기본적으로 AKS 배포는 [공용 부하 분산 장치](../aks/load-balancer-standard.md)를 사용 합니다. 이 섹션에서는 내부 부하 분산 장치를 사용 하도록 AKS를 구성 하는 방법에 대해 알아봅니다. 내부 (또는 개인) 부하 분산 장치는 개인 Ip만 프런트 엔드로 허용 되는 경우에 사용 됩니다. 내부 부하 분산 장치는 가상 네트워크 내에서 트래픽 부하를 분산 하는 데 사용 됩니다.

_내부 부하 분산_장치를 사용 하도록 AKS를 구성 하 여 전용 부하 분산 장치를 사용 하도록 설정 합니다. 

#### <a name="network-contributor-role"></a>네트워크 참가자 역할

> [!IMPORTANT]
> 이전에 만든 가상 네트워크를 제공 하 여 AKS 클러스터를 만들거나 연결 하는 경우 AKS 클러스터에 대 한 SP (서비스 사용자) 또는 관리 _id를 가상_ 네트워크를 포함 하는 리소스 그룹에 부여 해야 합니다. 내부 부하 분산 장치를 개인 IP로 변경 하기 전에이 작업을 수행 해야 합니다.
>
> 네트워크 참가자로 id를 추가 하려면 다음 단계를 사용 합니다.

1. AKS에 대 한 서비스 주체 또는 관리 id ID를 찾으려면 다음 Azure CLI 명령을 사용 합니다. `<aks-cluster-name>`을 클러스터의 이름으로 바꿉니다. `<resource-group-name>`을 _AKS 클러스터를 포함_하는 리소스 그룹의 이름으로 바꿉니다.

    ```azurecli-interactive
    az aks show -n <aks-cluster-name> --resource-group <resource-group-name> --query servicePrincipalProfile.clientId
    ``` 

    이 명령이 값을 반환 하는 경우 `msi` 다음 명령을 사용 하 여 관리 되는 id의 보안 주체 ID를 식별 합니다.

    ```azurecli-interactive
    az aks show -n <aks-cluster-name> --resource-group <resource-group-name> --query identity.principalId
    ```

1. 가상 네트워크를 포함 하는 리소스 그룹의 ID를 찾으려면 다음 명령을 사용 합니다. `<resource-group-name>`을 _가상 네트워크를 포함_하는 리소스 그룹의 이름으로 바꿉니다.

    ```azurecli-interactive
    az group show -n <resource-group-name> --query id
    ```

1. 서비스 주체 또는 관리 되는 id를 네트워크 참가자로 추가 하려면 다음 명령을 사용 합니다. 를 `<SP-or-managed-identity>` 서비스 주체 또는 관리 id에 대해 반환 된 id로 바꿉니다. 을 `<resource-group-id>` 가상 네트워크를 포함 하는 리소스 그룹에 대해 반환 된 ID로 바꿉니다.

    ```azurecli-interactive
    az role assignment create --assignee <SP-or-managed-identity> --role 'Network Contributor' --scope <resource-group-id>
    ```
AKS에서 내부 부하 분산 장치를 사용하는 방법에 대한 자세한 내용은 [Azure Kubernetes Service에서 내부 부하 분산 장치 사용](/azure/aks/internal-lb)을 참조하세요.

#### <a name="enable-private-load-balancer"></a>전용 부하 분산 장치 사용

> [!IMPORTANT]
> Azure Machine Learning studio에서 Azure Kubernetes 서비스 클러스터를 만들 때 개인 IP를 사용 하도록 설정할 수 없습니다. 기계 학습을 위해 Python SDK 또는 Azure CLI 확장을 사용 하는 경우 내부 부하 분산 장치를 사용 하 여 하나를 만들 수 있습니다.

다음 예제에서는 SDK 및 CLI를 사용 하 여 __개인 IP/내부 부하 분산 장치를 사용 하 여 새 AKS 클러스터를 만드는__ 방법을 보여 줍니다.

# <a name="python"></a>[Python](#tab/python)

```python
import azureml.core
from azureml.core.compute import AksCompute, ComputeTarget

# Verify that cluster does not exist already
try:
    aks_target = AksCompute(workspace=ws, name=aks_cluster_name)
    print("Found existing aks cluster")

except:
    print("Creating new aks cluster")

    # Subnet to use for AKS
    subnet_name = "default"
    # Create AKS configuration
    prov_config=AksCompute.provisioning_configuration(load_balancer_type="InternalLoadBalancer")
    # Set info for existing virtual network to create the cluster in
    prov_config.vnet_resourcegroup_name = "myvnetresourcegroup"
    prov_config.vnet_name = "myvnetname"
    prov_config.service_cidr = "10.0.0.0/16"
    prov_config.dns_service_ip = "10.0.0.10"
    prov_config.subnet_name = subnet_name
    prov_config.docker_bridge_cidr = "172.17.0.1/16"

    # Create compute target
    aks_target = ComputeTarget.create(workspace = ws, name = "myaks", provisioning_configuration = prov_config)
    # Wait for the operation to complete
    aks_target.wait_for_completion(show_output = True)
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az ml computetarget create aks -n myaks --load-balancer-type InternalLoadBalancer
```

자세한 내용은 [az ml computetarget create aks](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/computetarget/create?view=azure-cli-latest&preserve-view=true#ext-azure-cli-ml-az-ml-computetarget-create-aks) reference를 참조 하세요.

---

__기존 클러스터__ 를 작업 영역에 연결 하는 경우 연결 작업 후에 부하 분산 장치를 구성할 때까지 기다려야 합니다. 클러스터를 연결 하는 방법에 대 한 자세한 내용은 [기존 AKS 클러스터 연결](how-to-create-attach-kubernetes.md)을 참조 하세요.

기존 클러스터를 연결한 후에는 내부 부하 분산 장치/개인 IP를 사용 하도록 클러스터를 업데이트할 수 있습니다.

```python
import azureml.core
from azureml.core.compute.aks import AksUpdateConfiguration
from azureml.core.compute import AksCompute

# ws = workspace object. Creation not shown in this snippet
aks_target = AksCompute(ws,"myaks")

# Change to the name of the subnet that contains AKS
subnet_name = "default"
# Update AKS configuration to use an internal load balancer
update_config = AksUpdateConfiguration(None, "InternalLoadBalancer", subnet_name)
aks_target.update(update_config)
# Wait for the operation to complete
aks_target.wait_for_completion(show_output = True)
```

## <a name="enable-azure-container-instances-aci"></a>Azure Container Instances 사용 (ACI)

Azure Container Instances는 모델을 배포할 때 동적으로 생성됩니다. Azure Machine Learning이 가상 네트워크 내에 ACI를 만들 수 있도록 설정하려면 배포에 사용되는 서브넷에 __서브넷 위임__을 활성화해야 합니다.

> [!WARNING]
> 가상 네트워크에서 Azure Container Instances를 사용 하는 경우 가상 네트워크가 Azure Machine Learning 작업 영역과 동일한 리소스 그룹에 있어야 합니다.
>
> 가상 네트워크 내에서 Azure Container Instances를 사용 하는 경우 작업 영역에 대 한 Azure Container Registry (ACR)는 가상 네트워크에도 있을 수 없습니다.

가상 네트워크의 ACI를 작업 영역으로 사용하려면 다음 단계를 사용하세요.

1. 가상 네트워크에서 서브넷 위임을 사용하도록 설정하려면 [서브넷 위임 추가 또는 제거](../virtual-network/manage-subnet-delegation.md) 문서의 정보를 참조하세요. 가상 네트워크를 만들 때 위임을 사용하도록 설정하거나 기존 네트워크에 추가할 수 있습니다.

    > [!IMPORTANT]
    > 위임을 사용하도록 설정하는 경우 `Microsoft.ContainerInstance/containerGroups`을 __서비스에 서브넷 위임__ 값으로 사용합니다.

2. [AciWebservice.deploy_configuration()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aci.aciwebservice?view=azure-ml-py&preserve-view=true#deploy-configuration-cpu-cores-none--memory-gb-none--tags-none--properties-none--description-none--location-none--auth-enabled-none--ssl-enabled-none--enable-app-insights-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--ssl-cname-none--dns-name-label-none--primary-key-none--secondary-key-none--collect-model-data-none--cmk-vault-base-url-none--cmk-key-name-none--cmk-key-version-none--vnet-name-none--subnet-name-none-&preserve-view=true)을 사용하여 모델을 배포하고 `vnet_name` 및 `subnet_name` 매개 변수를 사용합니다. 이 매개 변수를 위임을 사용하도록 설정한 가상 네트워크 이름 및 서브넷으로 설정합니다.


## <a name="next-steps"></a>다음 단계

이 문서는 네 부분으로 구성 된 가상 네트워크 시리즈의 3 부입니다. 가상 네트워크를 보호 하는 방법을 알아보려면 나머지 문서를 참조 하세요.

* [1 부: 가상 네트워크 개요](how-to-network-security-overview.md)
* [2 부: 작업 영역 리소스 보안](how-to-secure-workspace-vnet.md)
* [3 부: 학습 환경 보안](how-to-secure-training-vnet.md)
* [5 부: studio 기능 사용](how-to-enable-studio-virtual-network.md)