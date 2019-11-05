---
title: Azure Kubernetes 서비스 (AKS)의 API server 권한 있는 IP 범위
description: Azure Kubernetes 서비스 (AKS)에서 API 서버에 액세스 하기 위한 IP 주소 범위를 사용 하 여 클러스터를 보호 하는 방법을 알아봅니다.
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 05/06/2019
ms.author: mlearned
ms.openlocfilehash: 8418499cc3e094162ac7483aaa6c71e74db95ae1
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73472960"
---
# <a name="secure-access-to-the-api-server-using-authorized-ip-address-ranges-in-azure-kubernetes-service-aks"></a>AKS (Azure Kubernetes Service)에서 권한이 부여 된 IP 주소 범위를 사용 하 여 API 서버에 대 한 액세스 보호

Kubernetes에서 API 서버는 리소스를 만들거나 노드 수를 조정 하는 등 클러스터에서 작업을 수행 하기 위한 요청을 수신 합니다. API 서버는 클러스터와 상호 작용 하 고 클러스터를 관리 하는 중심 방법입니다. 클러스터 보안을 개선 하 고 공격을 최소화 하려면 제한 된 IP 주소 범위 에서만 API 서버에 액세스할 수 있어야 합니다.

이 문서에서는 API server 권한이 부여 된 IP 주소 범위를 사용 하 여 제어 평면에 액세스할 수 있는 IP 주소 및 CIDRs를 제한 하는 방법을 보여 줍니다.

> [!IMPORTANT]
> 새 클러스터에서 API 서버 권한이 부여 된 IP 주소 범위는 *표준* SKU 부하 분산 장치 에서만 지원 됩니다. *기본* SKU 부하 분산 장치 및 API 서버 권한이 부여 된 IP 주소 범위가 있는 기존 클러스터는 그대로 작동 합니다. 이러한 기존 clusers는 업그레이드 될 수도 있고 계속 작동 합니다.

## <a name="before-you-begin"></a>시작하기 전에

이 문서에서는 [kubenet][kubenet]를 사용 하는 클러스터로 작업 하는 경우를 가정 합니다.  [Azure 컨테이너 네트워킹 인터페이스 (CNI)][cni-networking] 기반 클러스터를 사용 하는 경우 액세스를 보호 하는 데 필요한 경로 테이블이 없습니다.  경로 테이블을 수동으로 만들어야 합니다.  자세한 내용은 [경로 테이블 관리](https://docs.microsoft.com/azure/virtual-network/manage-route-table) 를 참조 하세요.

API server 권한 있는 IP 범위는 새로 만든 AKS 클러스터에만 적용 됩니다. 이 문서에서는 Azure CLI를 사용 하 여 AKS 클러스터를 만드는 방법을 보여 줍니다.

Azure CLI 버전 2.0.76 이상이 설치 및 구성 되어 있어야 합니다.  `az --version`을 실행하여 버전을 찾습니다. 설치 또는 업그레이드 해야 하는 경우 [Azure CLI 설치][install-azure-cli]를 참조 하세요.

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
    --vm-set-type VirtualMachineScaleSets \
    --load-balancer-sku standard \
    --generate-ssh-keys

# Get credentials to access the cluster
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

## <a name="update-cluster-with-authorized-ip-ranges"></a>권한 있는 IP 범위로 클러스터 업데이트

기본적으로 클러스터는 아웃 바운드 게이트웨이를 구성 하는 데 사용할 수 있는 [표준 SKU 부하 분산 장치][standard-sku-lb]를 사용 합니다. [Az network 공용 ip list][az-network-public-ip-list] 를 사용 하 고 AKS 클러스터의 리소스 그룹을 지정 합니다 .이는 일반적으로 *MC_* 로 시작 합니다. 이렇게 하면 클러스터에 대 한 공용 IP가 표시 되어 허용할 수 있습니다. [Az aks update][az-aks-update] 명령을 사용 하 고 *--api-version-범위* 매개 변수를 지정 하 여 클러스터의 ip를 허용 합니다. 예:

```azurecli-interactive
RG=$(az aks show --resource-group myResourceGroup --name myAKSCluster --query nodeResourceGroup -o tsv)
SLB_PublicIP=$(az network public-ip list --resource-group $RG --query [].ipAddress -o tsv)
az aks update --api-server-authorized-ip-ranges $SLB_PublicIP --resource-group myResourceGroup --name myAKSCluster
```

API server 권한 있는 IP 범위를 사용 하도록 설정 하려면 [az aks update][az-aks-update] 명령을 사용 하 고 *--api-version-범위* 매개 변수를 지정 하 여 권한이 부여 된 ip 주소 범위 목록을 제공 합니다. 이러한 IP 주소 범위는 일반적으로 온-프레미스 네트워크 또는 공용 Ip에서 사용 되는 주소 범위입니다. CIDR 범위를 지정 하는 경우 범위의 첫 번째 IP 주소부터 시작 합니다. 예를 들어 *137.117.106.90/29* 는 유효한 범위 이지만 *137.117.106.88/29*와 같이 범위의 첫 번째 IP 주소를 지정 해야 합니다.

다음 예제에서는 *Myresourcegroup*이라는 리소스 그룹에서 *myAKSCluster* 이라는 클러스터의 API 서버에 권한 있는 IP 범위를 사용 하도록 설정 합니다. 권한을 부여할 IP 주소 범위는 *172.0.0.0/16* (Pod/Nodes 주소 범위) 및 *168.10.0.0/18* (ServiceCidr)입니다.

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --api-server-authorized-ip-ranges 172.0.0.0/16,168.10.0.0/18
```

> [!NOTE]
> 이러한 범위는 허용 목록에 추가 해야 합니다.
> - 방화벽 공용 IP 주소
> - 서비스 CIDR
> - 노드 및 pod를 포함 하는 서브넷의 주소 범위입니다.
> - 클러스터를 관리할 네트워크를 나타내는 모든 범위

## <a name="disable-authorized-ip-ranges"></a>권한 있는 IP 범위 사용 안 함

권한 있는 IP 범위를 사용 하지 않도록 설정 하려면 [az aks update][az-aks-update] 를 사용 하 고 API server 권한 있는 ip 범위를 사용 하지 않도록 빈 범위를 지정 합니다. 예:

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
[cni-networking]: https://github.com/Azure/azure-container-networking/blob/master/docs/cni.md
[kubenet]: https://kubernetes.io/docs/concepts/extend-kubernetes/compute-storage-net/network-plugins/#kubenet

<!-- LINKS - internal -->
[az-aks-update]: /cli/azure/ext/aks-preview/aks#ext-aks-preview-az-aks-update
[az-aks-create]: /cli/azure/aks#az-aks-create
[az-network-public-ip-list]: /cli/azure/network/public-ip#az-network-public-ip-list
[concepts-clusters-workloads]: concepts-clusters-workloads.md
[concepts-security]: concepts-security.md
[install-azure-cli]: /cli/azure/install-azure-cli
[operator-best-practices-cluster-security]: operator-best-practices-cluster-security.md
[standard-sku-lb]: load-balancer-standard.md
