---
title: Azure Kubernetes 서비스 (AKS)의 API server 권한 있는 IP 범위
description: Azure Kubernetes 서비스 (AKS)에서 API 서버에 액세스 하기 위한 IP 주소 범위를 사용 하 여 클러스터를 보호 하는 방법을 알아봅니다.
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 05/06/2019
ms.author: mlearned
ms.openlocfilehash: 59e64b7c84e589da57ea28d6655c9305f4fdc101
ms.sourcegitcommit: ca359c0c2dd7a0229f73ba11a690e3384d198f40
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/17/2019
ms.locfileid: "71058339"
---
# <a name="preview---secure-access-to-the-api-server-using-authorized-ip-address-ranges-in-azure-kubernetes-service-aks"></a>미리 보기-Azure Kubernetes 서비스 (AKS)에서 권한이 부여 된 IP 주소 범위를 사용 하 여 API 서버에 안전 하 게 액세스할 수 있습니다.

Kubernetes에서 API 서버는 리소스를 만들거나 노드 수를 조정 하는 등 클러스터에서 작업을 수행 하기 위한 요청을 수신 합니다. API 서버는 클러스터와 상호 작용 하 고 클러스터를 관리 하는 중심 방법입니다. 클러스터 보안을 개선 하 고 공격을 최소화 하려면 제한 된 IP 주소 범위 에서만 API 서버에 액세스할 수 있어야 합니다.

이 문서에서는 API server 권한이 부여 된 IP 주소 범위를 사용 하 여 제어 평면에 대 한 요청을 제한 하는 방법을 보여 줍니다. 이 기능은 현재 미리 보기로 제공됩니다.

> [!IMPORTANT]
> AKS 미리 보기 기능은 셀프 서비스 옵트인입니다. 미리 보기는 "있는 그대로" 및 "사용 가능한 상태로" 제공 되며 서비스 수준 계약 및 제한 된 보증에서 제외 됩니다. AKS 미리 보기는 최상의 노력에 대 한 고객 지원에서 부분적으로 다룹니다. 이러한 기능은 프로덕션 용도로는 사용할 수 없습니다. 추가 정보 다음 지원 문서를 참조 하세요.
>
> * [AKS 지원 정책][aks-support-policies]
> * [Azure 지원 FAQ][aks-faq]

## <a name="before-you-begin"></a>시작하기 전 주의 사항

이 문서에서는 [kubenet][kubenet]를 사용 하는 클러스터로 작업 하는 assumess 합니다.  [Azure 컨테이너 네트워킹 인터페이스 (CNI)][cni-networking] 기반 클러스터를 사용 하는 경우 액세스를 보호 하는 데 필요한 경로 테이블이 없습니다.  경로 테이블을 수동으로 만들어야 합니다.  자세한 내용은 [경로 테이블 관리](https://docs.microsoft.com/azure/virtual-network/manage-route-table) 를 참조 하세요.

API server 권한 있는 IP 범위는 새로 만든 AKS 클러스터에만 적용 됩니다. 이 문서에서는 Azure CLI를 사용 하 여 AKS 클러스터를 만드는 방법을 보여 줍니다.

Azure CLI 버전 2.0.61 이상이 설치 및 구성 되어 있어야 합니다.  `az --version`을 실행하여 버전을 찾습니다. 설치 또는 업그레이드 해야 하는 경우 [Azure CLI 설치][install-azure-cli]를 참조 하세요.

### <a name="install-aks-preview-cli-extension"></a>aks-preview CLI 확장 설치

API server 권한 있는 IP 범위를 구성 하려면 *aks-preview* CLI extension version 0.4.1 이상이 필요 합니다. [Az extension add][az-extension-add] 명령을 사용 하 여 *aks-preview* Azure CLI 확장을 설치한 다음 [az extension update][az-extension-update] 명령을 사용 하 여 사용 가능한 업데이트를 확인 합니다.

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

### <a name="register-feature-flag-for-your-subscription"></a>구독에 대 한 기능 플래그 등록

API server 권한 있는 IP 범위를 사용 하려면 먼저 구독에서 기능 플래그를 사용 하도록 설정 합니다. *APIServerSecurityPreview* 기능 플래그를 등록 하려면 다음 예제와 같이 [az feature register][az-feature-register] 명령을 사용 합니다.

> [!CAUTION]
> 구독에 기능을 등록 하면 현재 해당 기능을 등록 취소할 수 없습니다. 일부 미리 보기 기능을 사용 하도록 설정한 후에는 구독에서 만든 모든 AKS 클러스터에 대 한 기본값을 사용할 수 있습니다. 프로덕션 구독에서 미리 보기 기능을 사용 하도록 설정 하지 마세요. 별도의 구독을 사용 하 여 미리 보기 기능을 테스트 하 고 피드백을 수집 합니다.

```azurecli-interactive
az feature register --name APIServerSecurityPreview --namespace Microsoft.ContainerService
```

상태가 *Registered*로 표시되는 데 몇 분 정도 걸립니다. [Az feature list][az-feature-list] 명령을 사용 하 여 등록 상태를 확인할 수 있습니다.

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/APIServerSecurityPreview')].{Name:name,State:properties.state}"
```

준비가 되 면 [az provider register][az-provider-register] 명령을 사용 하 여 *ContainerService* 리소스 공급자 등록을 새로 고칩니다.

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

## <a name="limitations"></a>제한 사항

API server 권한 있는 IP 범위를 구성 하는 경우 다음 제한 사항이 적용 됩니다.

* 현재 API 서버와의 통신도 차단 되므로 Azure Dev Spaces를 사용할 수 없습니다.

## <a name="overview-of-api-server-authorized-ip-ranges"></a>API server 권한 있는 IP 범위 개요

Kubernetes API 서버는 기본 Kubernetes Api가 노출 되는 방법입니다. 이 구성 요소는 `kubectl` 또는 Kubernetes 대시보드와 같은 관리 도구에 대한 상호 작용을 제공합니다. AKS는 전용 API 서버를 사용 하 여 단일 테 넌 트 클러스터 마스터를 제공 합니다. 기본적으로 API 서버에는 공용 IP 주소가 할당 되며, RBAC (역할 기반 액세스 제어)를 사용 하 여 액세스를 제어 해야 합니다.

공개적으로 액세스할 수 있는 AKS 제어 평면/a p i 서버에 대 한 액세스를 보호 하기 위해 권한 있는 IP 범위를 사용 하도록 설정 하 고 사용할 수 있습니다. 이러한 권한 있는 IP 범위는 정의 된 IP 주소 범위만 API 서버와 통신 하도록 허용 합니다. 이러한 권한 있는 IP 범위에 속하지 않은 IP 주소에서 API 서버에 대 한 요청은 차단 됩니다. RBAC를 사용 하 여 사용자에 게 권한을 부여 하 고 사용자에 게 요청 하는 작업을 계속 해야 합니다.

API 서버 및 기타 클러스터 구성 요소에 대 한 자세한 내용은 [Kubernetes core 개념 AKS][concepts-clusters-workloads]을 참조 하세요.

## <a name="create-an-aks-cluster"></a>AKS 클러스터 만들기

API server 권한 있는 IP 범위는 새 AKS 클러스터에만 적용 됩니다. 클러스터 만들기 작업의 일부로 권한 있는 IP 범위를 사용 하도록 설정할 수 없습니다. 클러스터 만들기 프로세스의 일부로 권한 있는 IP 범위를 사용 하도록 설정 하는 경우 해당 지점에서 송신 IP 주소가 정의 되지 않았기 때문에 클러스터 노드에서 배포 중에 API 서버에 액세스할 수 없습니다.

먼저 [az aks create][az-aks-create] 명령을 사용 하 여 클러스터를 만듭니다. 다음 예제에서는 *Myresourcegroup*이라는 리소스 그룹에 *myAKSCluster* 라는 단일 노드 클러스터를 만듭니다.

```azurecli-interactive
# Create an Azure resource group
az group create --name myResourceGroup --location eastus

# Create an AKS cluster
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --node-count 1 \
    --generate-ssh-keys

# Get credentials to access the cluster
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

## <a name="create-outbound-gateway-for-firewall-rules"></a>방화벽 규칙에 대 한 아웃 바운드 게이트웨이 만들기

다음 섹션에서 권한 있는 IP 범위를 사용 하도록 설정할 때 클러스터의 노드가 API 서버와 안정적으로 통신할 수 있도록 하려면 아웃 바운드 게이트웨이로 사용할 Azure 방화벽을 만듭니다. 그런 다음 Azure 방화벽의 IP 주소는 다음 섹션의 권한 있는 API 서버 IP 주소 목록에 추가 됩니다.

> [!WARNING]
> Azure 방화벽을 사용 하면 월간 청구 주기 동안 상당한 비용이 발생할 수 있습니다. Azure 방화벽을 사용 하는 요구 사항은 초기 미리 보기 기간에만 필요 합니다. 자세한 내용 및 비용 계획은 [Azure 방화벽 가격 책정][azure-firewall-costs]을 참조 하세요.
>
> 또는 클러스터에서 [표준 SKU 부하 분산 장치][standard-sku-lb]를 사용 하는 경우 Azure 방화벽을 아웃 바운드 게이트웨이로 구성할 필요가 없습니다. [Az network 공용 ip list][az-network-public-ip-list] 를 사용 하 고 AKS 클러스터의 리소스 그룹을 지정 합니다 .이는 일반적으로 *MC_* 로 시작 합니다. 그러면 허용 목록 수 있는 클러스터의 공용 IP가 표시 됩니다. 예를 들어:
>
> ```azurecli-interactive
> RG=$(az aks show --resource-group myResourceGroup --name myAKSClusterSLB --query nodeResourceGroup -o tsv)
> SLB_PublicIP=$(az network public-ip list --resource-group $RG --query [].ipAddress -o tsv)
> az aks update --api-server-authorized-ip-ranges $SLB_PublicIP --resource-group myResourceGroup --name myAKSClusterSLB
> ```

먼저 AKS 클러스터 및 가상 네트워크에 대 한 *MC_* 리소스 그룹 이름을 가져옵니다. 그런 다음 [az network vnet subnet create][az-network-vnet-subnet-create] 명령을 사용 하 여 서브넷을 만듭니다. 다음 예제에서는 CIDR 범위 *10.200.0.0/16*을 사용 하 여 *AzureFirewallSubnet* 이라는 서브넷을 만듭니다.

```azurecli-interactive
# Get the name of the MC_ cluster resource group
MC_RESOURCE_GROUP=$(az aks show \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --query nodeResourceGroup -o tsv)

# Get the name of the virtual network used by the cluster
VNET_NAME=$(az network vnet list \
    --resource-group $MC_RESOURCE_GROUP \
    --query [0].name -o tsv)

# Create a subnet in the virtual network for Azure Firewall
az network vnet subnet create \
    --resource-group $MC_RESOURCE_GROUP \
    --vnet-name $VNET_NAME \
    --name AzureFirewallSubnet \
    --address-prefixes 10.200.0.0/16
```

Azure 방화벽을 만들려면 [az extension add][az-extension-add] 명령을 사용 하 여 *azure 방화벽* CLI 확장을 설치 합니다. 그런 다음 [az network firewall create][az-network-firewall-create] 명령을 사용 하 여 방화벽을 만듭니다. 다음 예제에서는 *Myazurefirewall*이라는 Azure 방화벽을 만듭니다.

```azurecli-interactive
# Install the CLI extension for Azure Firewall
az extension add --name azure-firewall

# Create an Azure firewall
az network firewall create \
    --resource-group $MC_RESOURCE_GROUP\
    --name myAzureFirewall
```

Azure 방화벽에는 송신 트래픽이 전달 되는 공용 IP 주소가 할당 됩니다. [Az network 공용-ip create][az-network-public-ip-create] 명령을 사용 하 여 공용 주소를 만든 후 공용 ip를 적용 하는 [az network firewall ip-https create][az-network-firewall-ip-config-create] 를 사용 하 여 방화벽에서 ip 구성을 만듭니다.

```azurecli-interactive
# Create a public IP address for the firewall
FIREWALL_PUBLIC_IP=$(az network public-ip create \
    --resource-group $MC_RESOURCE_GROUP \
    --name myAzureFirewallPublicIP \
    --sku Standard \
    --query publicIp.ipAddress -o tsv)

# Associated the firewall with virtual network
az network firewall ip-config create \
    --resource-group $MC_RESOURCE_GROUP \
    --name myAzureFirewallIPConfig \
    --vnet-name $VNET_NAME \
    --firewall-name myAzureFirewall \
    --public-ip-address myAzureFirewallPublicIP
```

이제 [az network firewall network-rule create][az-network-firewall-network-rule-create] 명령을 사용 하 여 모든 *TCP* 트래픽을 *허용* 하는 Azure 방화벽 네트워크 규칙을 만듭니다. 다음 예제에서는 원본 또는 대상 주소가 있는 트래픽에 대해 *Allowtcpoutbound* 이라는 네트워크 규칙을 만듭니다.

```azurecli-interactive
az network firewall network-rule create \
    --resource-group $MC_RESOURCE_GROUP \
    --firewall-name myAzureFirewall \
    --name AllowTCPOutbound \
    --collection-name myAzureFirewallCollection \
    --priority 200 \
    --action Allow \
    --protocols TCP \
    --source-addresses '*' \
    --destination-addresses '*' \
    --destination-ports '*'
```

Azure 방화벽과 네트워크 경로를 연결 하려면 기존 경로 테이블 정보, Azure 방화벽의 내부 IP 주소 및 API 서버의 IP 주소를 가져옵니다. 이러한 IP 주소는 다음 섹션에서 클러스터 통신용 트래픽을 라우팅하는 방법을 제어 하기 위해 지정 됩니다.

```azurecli-interactive
# Get the AKS cluster route table
ROUTE_TABLE=$(az network route-table list \
    --resource-group $MC_RESOURCE_GROUP \
    --query "[?contains(name,'agentpool')].name" -o tsv)

# Get internal IP address of the firewall
FIREWALL_INTERNAL_IP=$(az network firewall show \
    --resource-group $MC_RESOURCE_GROUP \
    --name myAzureFirewall \
    --query ipConfigurations[0].privateIpAddress -o tsv)

# Get the IP address of API server endpoint
K8S_ENDPOINT_IP=$(kubectl get endpoints -o=jsonpath='{.items[?(@.metadata.name == "kubernetes")].subsets[].addresses[].ip}')
```

마지막으로, 트래픽이 API 서버 통신용으로 Azure 방화벽 어플라이언스를 사용할 수 있도록 하는 [az network ROUTE AKS route create][az-network-route-table-route-create] 명령을 사용 하 여 기존 네트워크 경로 테이블에 경로를 만듭니다.

```azurecli-interactive
az network route-table route create \
    --resource-group $MC_RESOURCE_GROUP \
    --route-table-name $ROUTE_TABLE \
    --name AzureFirewallAPIServer \
    --address-prefix $K8S_ENDPOINT_IP/32 \
    --next-hop-ip-address $FIREWALL_INTERNAL_IP \
    --next-hop-type VirtualAppliance

echo "Public IP address for the Azure Firewall instance that should be added to the list of API server authorized addresses is:" $FIREWALL_PUBLIC_IP
```

Azure 방화벽 어플라이언스의 공용 IP 주소를 기록해 둡니다. 이 주소는 다음 섹션의 API server 권한 부여 된 IP 범위 목록에 추가 됩니다.

## <a name="enable-authorized-ip-ranges"></a>권한 있는 IP 범위 사용

API server 권한 있는 IP 범위를 사용 하도록 설정 하려면 권한 있는 IP 주소 범위 목록을 제공 합니다. CIDR 범위를 지정 하는 경우 범위의 첫 번째 IP 주소부터 시작 합니다. 예를 들어 *137.117.106.90/29* 는 유효한 범위 이지만 *137.117.106.88/29*와 같이 범위의 첫 번째 IP 주소를 지정 해야 합니다.

[Az aks update][az-aks-update] 명령을 사용 하 여 허용 되는 *--api-version 범위* 를 지정 합니다. 이러한 IP 주소 범위는 일반적으로 온-프레미스 네트워크에서 사용 하는 주소 범위입니다. 이전 단계에서 얻은 Azure 방화벽의 공용 IP 주소를 추가 합니다 (예: *20.42.25.196/32*).

다음 예제에서는 *Myresourcegroup*이라는 리소스 그룹에서 *myAKSCluster* 이라는 클러스터의 API 서버에 권한 있는 IP 범위를 사용 하도록 설정 합니다. 권한을 부여 하는 IP 주소 범위는 *20.42.25.196/32* (Azure 방화벽 공용 IP 주소), *172.0.0.0/16* 및 *168.10.0.0/18*입니다.

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --api-server-authorized-ip-ranges 20.42.25.196/32,172.0.0.0/16,168.10.0.0/18
```

## <a name="update-or-disable-authorized-ip-ranges"></a>권한 있는 IP 범위 업데이트 또는 사용 안 함

권한 있는 IP 범위를 업데이트 하거나 사용 하지 않도록 설정 하려면 [az aks update][az-aks-update] 명령을 다시 사용 합니다. 다음 예와 같이 허용할 업데이트 된 CIDR 범위를 지정 하거나, 빈 범위를 지정 하 여 API server 권한 있는 IP 범위를 사용 하지 않도록 설정 합니다.

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --api-server-authorized-ip-ranges ""
```

## <a name="next-steps"></a>다음 단계

이 문서에서는 API 서버에 권한 있는 IP 범위를 사용 하도록 설정 했습니다. 이 방법은 secure AKS 클러스터를 실행 하는 방법의 한 부분입니다.

자세한 내용은 [AKS의 응용 프로그램 및 클러스터에 대 한 보안 개념][concepts-security] 및 [AKS에서 클러스터 보안 및 업그레이드에 대 한 모범 사례][operator-best-practices-cluster-security]를 참조 하세요.

<!-- LINKS - external -->
[azure-firewall-costs]: https://azure.microsoft.com/pricing/details/azure-firewall/
[kubenet]: https://kubernetes.io/docs/concepts/extend-kubernetes/compute-storage-net/network-plugins/#kubenet
[cni-networking]: https://github.com/Azure/azure-container-networking/blob/master/docs/cni.md

<!-- LINKS - internal -->
[aks-quickstart-cli]: kubernetes-walkthrough.md
[install-azure-cli]: /cli/azure/install-azure-cli
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-provider-register]: /cli/azure/provider#az-provider-register
[az-aks-update]: /cli/azure/ext/aks-preview/aks#ext-aks-preview-az-aks-update
[concepts-clusters-workloads]: concepts-clusters-workloads.md
[concepts-security]: concepts-security.md
[operator-best-practices-cluster-security]: operator-best-practices-cluster-security.md
[create-aks-sp]: kubernetes-service-principal.md#manually-create-a-service-principal
[az-aks-create]: /cli/azure/aks#az-aks-create
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-network-vnet-subnet-create]: /cli/azure/network/vnet/subnet#az-network-vnet-subnet-create
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-network-firewall-create]: /cli/azure/ext/azure-firewall/network/firewall#ext-azure-firewall-az-network-firewall-create
[az-network-public-ip-create]: /cli/azure/network/public-ip#az-network-public-ip-create
[az-network-public-ip-list]: /cli/azure/network/public-ip#az-network-public-ip-list
[az-network-firewall-ip-config-create]: /cli/azure/ext/azure-firewall/network/firewall/ip-config#ext-azure-firewall-az-network-firewall-ip-config-create
[az-network-firewall-network-rule-create]: /cli/azure/ext/azure-firewall/network/firewall/network-rule#ext-azure-firewall-az-network-firewall-network-rule-create
[az-network-route-table-route-create]: /cli/azure/network/route-table/route#az-network-route-table-route-create
[aks-support-policies]: support-policies.md
[aks-faq]: faq.md
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[standard-sku-lb]: load-balancer-standard.md
