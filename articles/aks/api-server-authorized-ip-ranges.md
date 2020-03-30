---
title: Azure Kubernetes 서비스(AKS)의 API 서버 인증 IP 범위
description: AZURE Kubernetes 서비스(AKS)의 API 서버에 액세스하기 위해 IP 주소 범위를 사용하여 클러스터를 보호하는 방법을 알아봅니다.
services: container-service
ms.topic: article
ms.date: 11/05/2019
ms.openlocfilehash: 593f9e0b335e6f4d62c76ce92f833ff4e9143372
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79126627"
---
# <a name="secure-access-to-the-api-server-using-authorized-ip-address-ranges-in-azure-kubernetes-service-aks"></a>Azure Kubernetes 서비스(AKS)에서 승인된 IP 주소 범위를 사용하여 API 서버에 대한 보안 액세스

Kubernetes에서 API 서버는 리소스를 만들거나 노드 수를 확장하는 등의 작업을 수행하기 위한 요청을 받습니다. API 서버는 클러스터와 상호 작용하고 관리하는 중앙 방법입니다. 클러스터 보안을 개선하고 공격을 최소화하려면 제한된 IP 주소 범위에서만 API 서버에 액세스할 수 있어야 합니다.

이 문서에서는 API 서버 인증 IP 주소 범위를 사용하여 제어 평면에 액세스할 수 있는 IP 주소와 CID를 제한하는 방법을 보여 주십니다.

> [!IMPORTANT]
> 새 클러스터에서 API 서버 권한 IP 주소 범위는 *표준* SKU 로드 밸런서에서만 지원됩니다. *기본* SKU 로드 밸런서 및 구성된 API 서버 공인 IP 주소 범위가 있는 기존 클러스터는 현재와 같이 작동하지만 *표준* SKU 로드 밸런서로 마이그레이션할 수 없습니다. Kubernetes 버전 또는 제어 평면을 업그레이드하는 경우에도 기존 클러스터가 계속 작동합니다.

## <a name="before-you-begin"></a>시작하기 전에

API 서버 권한 IP 범위는 생성한 새 AKS 클러스터에 대해서만 작동합니다. 이 문서에서는 Azure CLI를 사용하여 AKS 클러스터를 만드는 방법을 보여 주며, 이 문서에서는

Azure CLI 버전 2.0.76 이상 설치 및 구성이 필요합니다.  `az --version`을 실행하여 버전을 찾습니다. 설치 또는 업그레이드해야 하는 경우  [Azure CLI 설치][install-azure-cli]를 참조하세요.

## <a name="overview-of-api-server-authorized-ip-ranges"></a>API 서버 인증 IP 범위 개요

Kubernetes API 서버는 기본 Kubernetes API가 노출되는 방법입니다. 이 구성 요소는 `kubectl` 또는 Kubernetes 대시보드와 같은 관리 도구에 대한 상호 작용을 제공합니다. AKS는 전용 API 서버를 통해 단일 테넌트 클러스터 마스터를 제공합니다. 기본적으로 API 서버에는 공용 IP 주소가 할당되며 RBAC(역할 기반 액세스 제어)를 사용하여 액세스를 제어해야 합니다.

공개적으로 액세스할 수 있는 AKS 제어 평면/API 서버에 대한 액세스를 보호하려면 권한 있는 IP 범위를 활성화하고 사용할 수 있습니다. 이러한 권한 있는 IP 범위는 정의된 IP 주소 범위만 API 서버와 통신할 수 있도록 허용합니다. 이러한 승인된 IP 범위의 일부가 아닌 IP 주소에서 API 서버에 대한 요청이 차단됩니다. RBAC를 계속 사용하여 사용자와 사용자가 요청하는 작업에 권한을 부여합니다.

API 서버 및 기타 클러스터 구성 요소에 대한 자세한 내용은 [AKS에 대한 Kubernetes 핵심 개념을][concepts-clusters-workloads]참조하십시오.

## <a name="create-an-aks-cluster-with-api-server-authorized-ip-ranges-enabled"></a>API 서버 권한 IP 범위를 사용하도록 설정한 AKS 클러스터 만들기

API 서버 권한 IP 범위는 새 AKS 클러스터에 대해서만 작동합니다. [az aks를][az-aks-create] 사용하여 클러스터를 만들고 *--api-server-권한 IP-ranges* 매개 변수를 지정하여 승인된 IP 주소 범위 목록을 제공합니다. 이러한 IP 주소 범위는 일반적으로 온-프레미스 네트워크 또는 공용 IP에서 사용하는 주소 범위입니다. CIDR 범위를 지정할 때 범위의 첫 번째 IP 주소로 시작합니다. 예를 들어 *137.117.106.90/29는* 유효한 범위이지만 *137.117.106.88/29와*같이 범위의 첫 번째 IP 주소를 지정해야 합니다.

> [!IMPORTANT]
> 기본적으로 클러스터는 아웃바운드 게이트웨이를 구성하는 데 사용할 수 있는 [표준 SKU 로드 밸런서를][standard-sku-lb] 사용합니다. 클러스터 를 만드는 동안 API 서버 권한 있는 IP 범위를 사용하도록 설정하면 클러스터에 대한 공용 IP도 지정한 범위 외에 기본적으로 허용됩니다. *--api-server-권한 IP 범위에*대해 *""* 또는 값을 지정하지 않으면 API 서버 권한 있는 IP 범위가 비활성화됩니다.

다음 예제에서는 API 서버 권한 있는 IP 범위를 사용하도록 설정한 *myResourceGroup이라는* 리소스 그룹에 *myAKSCluster라는* 단일 노드 클러스터를 만듭니다. 허용되는 IP 주소 범위는 *73.140.245.0/24입니다.*

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --node-count 1 \
    --vm-set-type VirtualMachineScaleSets \
    --load-balancer-sku standard \
    --api-server-authorized-ip-ranges 73.140.245.0/24 \
    --generate-ssh-keys
```

> [!NOTE]
> 허용 목록에 이러한 범위를 추가해야 합니다.
> - 방화벽 공용 IP 주소
> - 클러스터를 관리할 네트워크를 나타내는 모든 범위
> - AKS 클러스터에서 Azure 개발자 공간을 사용하는 경우 지역에 따라 추가 범위를 허용해야 [합니다.][dev-spaces-ranges]

> 지정할 수 있는 IP 범위 수의 상한은 3500입니다. 

### <a name="specify-the-outbound-ips-for-the-standard-sku-load-balancer"></a>표준 SKU 로드 밸러블러의 아웃바운드 IP 지정

AKS 클러스터를 만들 때 클러스터의 아웃바운드 IP 주소 또는 접두사를 지정하면 해당 주소 또는 접두사도 허용됩니다. 예를 들어:

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --node-count 1 \
    --vm-set-type VirtualMachineScaleSets \
    --load-balancer-sku standard \
    --api-server-authorized-ip-ranges 73.140.245.0/24 \
    --load-balancer-outbound-ips <publicIpId1>,<publicIpId2> \
    --generate-ssh-keys
```

위의 예에서 매개 변수 --로드 밸러버 아웃 바운드-ip-접두사---api-서버-권한-ip-범위 매개 변수의 IP와 함께 허용 *됩니다.-* *로드-밸러버-아웃 바운드-ip-접두사* 매개 변수에 제공 된 모든 IP입니다.

또는 *--load-밸러터 아웃바운드-ip-접두사* 매개변수를 지정하여 아웃바운드 로드 밸러저 IP 접두사를 허용할 수 있습니다.

### <a name="allow-only-the-outbound-public-ip-of-the-standard-sku-load-balancer"></a>표준 SKU 로드 밸런서의 아웃바운드 공용 IP만 허용

클러스터 를 만드는 동안 API 서버 권한 있는 IP 범위를 사용하도록 설정하면 클러스터에 대한 표준 SKU 로드 밸런서에 대한 아웃바운드 공용 IP도 지정한 범위 외에 기본적으로 허용됩니다. 표준 SKU 로드 밸런서의 아웃바운드 공용 IP만 허용하려면 *--api-server-권한 IP 범위* 매개 변수를 지정할 때 *0.0.0.0/32를* 사용합니다.

다음 예제에서는 표준 SKU 로드 밸런서의 아웃바운드 공용 IP만 허용되며 클러스터 내의 노드에서만 API 서버에 액세스할 수 있습니다.

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --node-count 1 \
    --vm-set-type VirtualMachineScaleSets \
    --load-balancer-sku standard \
    --api-server-authorized-ip-ranges 0.0.0.0/32 \
    --generate-ssh-keys
```

## <a name="update-a-clusters-api-server-authorized-ip-ranges"></a>클러스터의 API 서버 인증 IP 범위 업데이트

기존 클러스터에서 API 서버 권한 있는 IP 범위를 업데이트하려면 [az aks 업데이트][az-aks-update] 명령을 사용하고 *--api-server-권한 있는 IP 범위,* *--로드 밸런서-아웃바운드-ip-접두사,* *로드 밸런서-아웃바운드-ips*또는 *-로드 밸런서 아웃바운드-ip-접두사* 매개 변수를 사용합니다.

다음 예제에서는 *myResourceGroup이라는*리소스 그룹의 *myAKSCluster라는* 클러스터에서 API 서버 인증 IP 범위를 업데이트합니다. 승인할 IP 주소 범위는 *73.140.245.0/24입니다.*

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --api-server-authorized-ip-ranges  73.140.245.0/24
```

표준 SKU 로드 밸런서의 공용 IP만 허용하도록 *--api-server-권한 IP-ranges* 매개 변수를 지정할 때 *0.0.0.0/32를* 사용할 수도 있습니다.

## <a name="disable-authorized-ip-ranges"></a>승인된 IP 범위 사용 안 함

승인된 IP 범위를 비활성화하려면 [az aks 업데이트를][az-aks-update] 사용하고 빈 범위를 지정하여 API 서버 권한 있는 IP 범위를 비활성화합니다. 예를 들어:

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --api-server-authorized-ip-ranges ""
```

## <a name="next-steps"></a>다음 단계

이 문서에서는 API 서버 권한 있는 IP 범위를 사용하도록 설정했습니다. 이 방법은 보안 AKS 클러스터를 실행하는 방법의 한 부분입니다.

자세한 내용은 [AKS의 응용 프로그램 및 클러스터에 대한 보안 개념과 AKS의][concepts-security] [클러스터 보안 및 업그레이드에 대한 모범 사례를][operator-best-practices-cluster-security]참조하십시오.

<!-- LINKS - external -->
[cni-networking]: https://github.com/Azure/azure-container-networking/blob/master/docs/cni.md
[dev-spaces-ranges]: https://github.com/Azure/dev-spaces/tree/master/public-ips
[kubenet]: https://kubernetes.io/docs/concepts/extend-kubernetes/compute-storage-net/network-plugins/#kubenet

<!-- LINKS - internal -->
[az-aks-update]: /cli/azure/ext/aks-preview/aks#ext-aks-preview-az-aks-update
[az-aks-create]: /cli/azure/aks#az-aks-create
[az-network-public-ip-list]: /cli/azure/network/public-ip#az-network-public-ip-list
[concepts-clusters-workloads]: concepts-clusters-workloads.md
[concepts-security]: concepts-security.md
[install-azure-cli]: /cli/azure/install-azure-cli
[operator-best-practices-cluster-security]: operator-best-practices-cluster-security.md
[route-tables]: ../virtual-network/manage-route-table.md
[standard-sku-lb]: load-balancer-standard.md
