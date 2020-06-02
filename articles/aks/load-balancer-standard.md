---
title: 표준 SKU 부하 분산 장치 사용
titleSuffix: Azure Kubernetes Service
description: 표준 SKU가 포함된 부하 분산 장치를 사용하여 AKS(Azure Kubernetes Service)를 통해 서비스를 노출하는 방법을 알아봅니다.
services: container-service
author: zr-msft
ms.topic: article
ms.date: 09/27/2019
ms.author: zarhoads
ms.openlocfilehash: 14e80f6348772af77c5a53b1d5e9111c4ae8ba9b
ms.sourcegitcommit: 90d2d95f2ae972046b1cb13d9956d6668756a02e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/14/2020
ms.locfileid: "83402061"
---
# <a name="use-a-standard-sku-load-balancer-in-azure-kubernetes-service-aks"></a>AKS(Azure Kubernetes Service)에서 표준 SKU 부하 분산 장치 사용

AKS(Azure Kubernetes Service)에서 `LoadBalancer` 형식의 Kubernetes 서비스를 통해 애플리케이션에 대한 액세스를 제공하려면 Azure Load Balancer를 사용할 수 있습니다. AKS에서 실행 중인 부하 분산 장치는 내부 또는 외부 부하 분산 장치로 사용할 수 있습니다. 내부 부하 분산 장치는 Kubernetes 서비스가 AKS 클러스터와 동일한 가상 네트워크에서 실행되는 애플리케이션에만 액세스할 수 있게 합니다. 외부 부하 분산 장치는 수신용으로 공용 IP를 하나 이상 받고 해당 공용 IP를 사용하여 외부에서 Kubernetes 서비스에 액세스할 수 있도록 합니다.

Azure Load Balancer는 ‘기본’ 및 ‘표준’이라는 두 SKU에서 사용할 수 있습니다.  기본적으로 ‘표준’ SKU는 AKS 클러스터를 만들 때 사용됩니다. ‘표준’ SKU 부하 분산 장치를 사용하여 더 큰 백엔드 풀 크기 및 가용성 영역 같은 추가 기능을 제공합니다. 사용할 부하 분산 장치를 선택하기 전에 ‘표준’ 및 ‘기본’ 부하 분산 장치 간의 차이점을 이해하는 것이 중요합니다.  AKS 클러스터를 만든 후에는 해당 클러스터에 대한 부하 분산 장치 SKU를 변경할 수 없습니다. ‘기본’ 및 ‘표준’ SKU에 대한 자세한 정보는 [Azure 부하 분산 장치 SKU 비교][azure-lb-comparison]를 참조하세요. 

이 문서에서는 Kubernetes 및 Azure Load Balancer 개념을 기본적으로 이해하고 있다고 가정합니다. 자세한 내용은 [AKS(Azure Kubernetes Service)의 Kubernetes 핵심 개념][kubernetes-concepts] 및 [Azure Load Balancer란?][azure-lb]를 참조하세요.

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

CLI를 로컬로 설치하여 사용하도록 선택한 경우 Azure CLI 버전 2.0.81 이상을 실행 중이어야 합니다. `az --version`을 실행하여 버전을 찾습니다. 설치 또는 업그레이드해야 하는 경우 [Azure CLI 설치][install-azure-cli]를 참조하세요.

## <a name="before-you-begin"></a>시작하기 전에

이 문서에서는 ‘표준’ SKU Azure Load Balancer를 사용하는 AKS 클러스터가 있다고 가정합니다. AKS 클러스터가 필요한 경우 AKS 빠른 시작 [Azure CLI 사용][aks-quickstart-cli] 또는 [Azure Portal 사용][aks-quickstart-portal]을 참조하세요.

기존 서브넷 또는 리소스 그룹을 사용하는 경우 AKS 클러스터 서비스 주체에도 네트워크 리소스를 관리할 수 있는 권한이 있어야 합니다. 일반적으로 위임된 리소스의 서비스 주체에게 ‘네트워크 기여자’ 역할을 할당합니다. 서비스 주체를 대신하여 사용 권한에 대한 시스템이 할당한 관리 ID를 사용할 수도 있습니다. 자세한 내용은 [관리 ID 사용](use-managed-identity.md)을 참조하세요. 사용 권한에 대한 자세한 내용은 [다른 Azure 리소스에 대한 AKS 액세스 권한 위임][aks-sp]을 참조하세요.

### <a name="moving-from-a-basic-sku-load-balancer-to-standard-sku"></a>기본 SKU 부하 분산 장치에서 표준 SKU로 이동

기본 SKU 부하 분산 장치를 사용하는 기존 클러스터가 있는 경우, 표준 SKU 부하 분산 장치를 사용하는 클러스터를 사용하도록 마이그레이션할 때 유의해야 할 중요한 동작상의 차이점이 있습니다.

예를 들어 클러스터의 `load-balancer-sku` 유형을 클러스터 생성 시점에만 정의할 수 있는 경우 클러스터를 마이그레이션하기 위한 파란색/녹색 배포를 수행하는 것이 일반적인 방법입니다. 그러나 ‘기본 SKU’ 부하 분산 장치는 ‘표준 SKU’ 부하 분산 장치(‘표준 SKU’ IP 주소가 필요함)와 호환되지 않는 ‘기본 SKU’ IP 주소를 사용합니다.    클러스터를 마이그레이션하여 부하 분산 장치 SKU를 업그레이드하는 경우 호환되는 IP 주소 SKU가 포함된 새 IP 주소가 필요합니다.

클러스터를 마이그레이션하는 방법에 대한 자세한 내용은 [마이그레이션 고려 사항에 대한 설명서](aks-migration.md)를 참조하여 마이그레이션할 때 고려해야 하는 중요한 토픽의 목록을 확인하세요. AKS에서 표준 SKU 부하 분산 장치를 사용하는 경우 아래 제한 사항에 유의해야 하는 중요한 동작상의 차이점이 있습니다.

### <a name="limitations"></a>제한 사항

‘표준’ SKU를 포함한 부하 분산 장치를 지원하는 AKS 클러스터를 만들고 관리하는 경우 다음과 같은 제한 사항이 적용됩니다.

* AKS 클러스터에서 송신 트래픽을 허용하려면 하나 이상의 공용 IP 또는 IP 접두사가 필요합니다. 공용 IP 또는 IP 접두사는 컨트롤 플레인과 에이전트 노드 간의 연결을 유지하는 데뿐만 아니라 AKS의 이전 버전과의 호환성을 유지하는 데도 필요합니다. ‘표준’ SKU 부하 분산 장치를 사용하여 공용 IP 또는 IP 접두사를 지정하는 다음과 같은 옵션이 있습니다.
    * 사용자 고유의 공용 IP를 제공합니다.
    * 사용자 고유의 공용 IP 접두사를 제공합니다.
    * 최대 100까지 숫자를 지정하여 AKS 클러스터가 해당하는 숫자의 ‘표준’ SKU 공용 IP를 AKS 클러스터에서 만들어진 동일한 리소스 그룹(일반적으로 처음에 *MC_* 로 이름이 지정됨)에 만들 수 있도록 합니다. AKS는 ‘표준’ SKU 부하 분산 장치에 공용 IP를 할당합니다. 공용 IP, 공용 IP 접두사 또는 IP 수가 지정되지 않은 경우 기본적으로 하나의 공용 IP가 AKS 클러스터와 동일한 리소스 그룹에 자동으로 만들어집니다. 또한 공용 주소를 허용하고 IP 생성을 금지하는 Azure Policy를 만들지 않도록 해야 합니다.
* 부하 분산 장치에 대해 ‘표준’ SKU를 사용하는 경우 Kubernetes 버전 1.13 이상을 사용해야 합니다. 
* 부하 분산 장치 SKU를 정의하는 것은 AKS 클러스터를 만들 때만 수행할 수 있습니다. AKS 클러스터를 만든 후에는 부하 분산 장치 SKU를 변경할 수 없습니다.
* 단일 클러스터에서는 한 가지 유형의 부하 분산 장치 SKU(기본 또는 표준)만 사용할 수 있습니다.
* ‘표준’ SKU 부하 분산 장치는 ‘표준’ SKU IP 주소만 지원합니다. 

## <a name="use-the-standard-sku-load-balancer"></a>‘표준’ SKU 부하 분산 장치 사용

AKS 클러스터를 만들 때 기본적으로 ‘표준’ SKU 부하 분산 장치가 해당 클러스터에서 서비스를 실행할 때 사용됩니다. 예를 들어 [Azure CLI 사용한 빠른 시작][aks-quickstart-cli]은 ‘표준’ SKU 부하 분산 장치를 사용하는 애플리케이션 예제를 배포합니다.

> [!IMPORTANT]
> UDR(사용자 정의 경로)을 사용자 지정하면 공용 IP 주소를 피할 수 있습니다. AKS 클러스터의 아웃바운드 유형을 UDR로 지정하면 AKS 생성 Azure 부하 분산 장치에 대한 IP 프로비저닝 및 백엔드 풀 설정을 건너뛸 수 있습니다. [클러스터의 `outboundType`을 ‘userDefinedRouting’으로 설정](egress-outboundtype.md)을 참조하세요.

## <a name="configure-the-load-balancer-to-be-internal"></a>부하 분산 장치를 내부로 구성

부하 분산 장치를 내부로 구성하고 공용 IP를 노출하지 않을 수도 있습니다. 부하 분산 장치를 내부로 구성하려면 `service.beta.kubernetes.io/azure-load-balancer-internal: "true"`를 *LoadBalancer* 서비스에 주석으로 추가합니다. 샘플 yaml 매니페스트 및 내부 부하 분산 장치에 대한 자세한 정보는 [여기][internal-lb-yaml]를 참조할 수 있습니다.

## <a name="scale-the-number-of-managed-public-ips"></a>관리되는 공용 IP 개수 스케일링

기본적으로 생성된 관리 아웃바운드 공용 IP를 사용하는 ‘표준’ SKU 부하 분산 장치를 사용하는 경우 *load-balancer-managed-ip-count* 매개 변수를 사용하여 관리 아웃바운드 공용 IP 개수를 스케일링할 수 있습니다.

기존 클러스터를 업데이트하려면 다음 명령을 실행합니다. 이 매개 변수는 클러스터를 만들 때 여러 개의 관리 아웃바운드 공용 IP를 갖도록 설정될 수도 있습니다.

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --load-balancer-managed-outbound-ip-count 2
```

위의 예제에서는 관리 아웃바운드 공용 IP 개수를 *myResourceGroup*의 *myAKSCluster* 클러스터에 대해 *2*로 설정합니다. 

또한 `--load-balancer-managed-outbound-ip-count` 매개 변수를 추가하고 원하는 값으로 설정하여 클러스터를 만들 때 *load-balancer-managed-ip-count* 매개 변수를 사용하여 관리 아웃바운드 공용 IP의 초기 개수를 설정할 수도 있습니다. 관리 아웃바운드 공용 IP의 기본 개수는 1입니다.

## <a name="provide-your-own-public-ips-or-prefixes-for-egress"></a>송신을 위한 사용자 고유 공용 IP 또는 접두사 제공

‘표준’ SKU 부하 분산 장치를 사용하는 경우 AKS 클러스터는 AKS 클러스터에 대해 생성된 동일한 리소스 그룹에서 공용 IP를 자동으로 만들고 공용 IP를 ‘표준’ SKU 부하 분산 장치에 할당합니다.  또는 클러스터를 만들 때 사용자 고유의 공용 IP를 할당하거나 기존 클러스터의 부하 분산 장치 속성을 업데이트할 수 있습니다.

여러 IP 주소 또는 접두사를 가져오면 단일 부하 분산 장치 개체 뒤에 IP 주소를 정의할 때 여러 지원 서비스를 정의할 수 있습니다. 특정 노드의 송신 엔드포인트는 연결된 서비스에 따라 달라집니다.

### <a name="pre-requisites-to-bring-your-own-ip-addresses-or-ip-prefixes"></a>사용자 고유의 IP 주소 또는 IP 접두사를 가져오기 위한 필수 구성 요소
1. ‘표준’ SKU 부하 분산 장치를 사용해 송신하기 위해서는 ‘표준’ SKU 공용 IP를 사용해야 합니다.  [az network public-ip show][az-network-public-ip-show] 명령을 사용하여 공용 IP의 SKU를 확인할 수 있습니다.

   ```azurecli-interactive
   az network public-ip show --resource-group myResourceGroup --name myPublicIP --query sku.name -o tsv
   ```
 1. 공용 IP 및 IP 접두사는 AKS 클러스터와 동일한 지역 및 구독의 일부에 있어야 합니다.
 1. 공용 IP 및 IP 접두사는 AKS에서 관리 IP로 만든 IP일 수 없습니다. 사용자 지정 IP로 지정된 모든 IP가 AKS 서비스에 의해서가 아니라 수동으로 만들어졌는지 확인합니다.
 1. 공용 IP 및 IP 접두사는 다른 리소스나 서비스에서 사용할 수 없습니다.

 ### <a name="define-your-own-public-ip-or-prefixes-on-an-existing-cluster"></a>기존 클러스터에서 고유한 공용 IP 또는 접두사 정의

[az network public-ip show][az-network-public-ip-show] 명령을 사용하여 공용 IP의 ID를 나열합니다.

```azurecli-interactive
az network public-ip show --resource-group myResourceGroup --name myPublicIP --query id -o tsv
```

위의 명령은 *myResourceGroup* 리소스 그룹의 *myPublicIP* 공용 IP에 대한 ID를 표시합니다.

*az aks update* 명령을 *load-balancer-outbound-ips* 매개 변수와 함께 사용하여 공용 IP로 클러스터를 업데이트합니다.

다음 예제에서는 이전 명령의 ID와 함께 *load-balancer-outbound-ips* 매개 변수를 사용합니다.

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --load-balancer-outbound-ips <publicIpId1>,<publicIpId2>
```

‘표준’ SKU 부하 분산 장치를 사용하여 송신하는 데 공용 IP 접두사를 사용할 수도 있습니다. 다음 예제에서는 [az network public-ip prefix show][az-network-public-ip-prefix-show] 명령을 사용하여 공용 IP 접두사의 ID를 나열합니다.

```azurecli-interactive
az network public-ip prefix show --resource-group myResourceGroup --name myPublicIPPrefix --query id -o tsv
```

위의 명령은 *myResourceGroup* 리소스 그룹의 *myPublicIPPrefix* 공용 IP 접두사에 대한 ID를 표시합니다.

다음 예제에서는 이전 명령의 ID를 사용하여 *load-balancer-outbound-ip-prefixes* 매개 변수를 사용합니다.

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --load-balancer-outbound-ip-prefixes <publicIpPrefixId1>,<publicIpPrefixId2>
```

### <a name="define-your-own-public-ip-or-prefixes-at-cluster-create-time"></a>클러스터를 만들 때 고유한 공용 IP 또는 접두사 정의

허용 목록 송신 엔드포인트와 같은 시나리오를 지원하기 위해 클러스터를 만들 때 송신을 위한 고유 IP 주소 또는 IP 접두사를 가져오고 싶을 수 있습니다. 클러스터 생성 단계에 위에 표시된 것과 동일한 매개 변수를 추가하여 클러스터의 수명 주기 시작 부분에 고유한 공용 IP 및 IP 접두사를 정의합니다.

*az aks create* 명령을 *load-balancer-outbound-ips* 매개 변수를 사용하여 시작 시 공용 IP로 새 클러스터를 만듭니다.

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --vm-set-type VirtualMachineScaleSets \
    --node-count 1 \
    --load-balancer-sku standard \
    --generate-ssh-keys \
    --load-balancer-outbound-ips <publicIpId1>,<publicIpId2>
```

*az aks create* 명령을 *load-balancer-outbound-ip-prefixes* 매개 변수와 함께 사용하여 시작 시 공용 IP 접두사로 새 클러스터를 만듭니다.

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --vm-set-type VirtualMachineScaleSets \
    --node-count 1 \
    --load-balancer-sku standard \
    --generate-ssh-keys \
    --load-balancer-outbound-ip-prefixes <publicIpPrefixId1>,<publicIpPrefixId2>
```

## <a name="configure-outbound-ports-and-idle-timeout"></a>아웃바운드 포트 및 유휴 시간 제한 구성

> [!WARNING]
> 다음 섹션은 더 큰 규모의 네트워킹 고급 시나리오 또는 기본 구성의 SNAT 고갈 문제를 해결하기 위한 내용입니다. 정상 클러스터를 유지하려면 기본값에서 *AllocatedOutboundPorts* 또는 를 변경하기 전에 VM 및 IP 주소에 대한 사용 가능한 할당량의 정확한 재고가 있어야 합니다.
> 
> *AllocatedOutboundPorts* 및 *IdleTimeoutInMinutes*에 대한 값을 변경하면 부하 분산 장치에 대한 아웃바운드 규칙의 동작이 크게 달라질 수 있습니다. 변경 내용의 영향을 완전히 이해하기 위해 이러한 값을 업데이트하기 전에 [Load Balancer 아웃바운드 규칙][azure-lb-outbound-rules-overview], [부하 분산 장치 아웃바운드 규칙][azure-lb-outbound-rules] 및 [Azure의 아웃바운드 연결][azure-lb-outbound-connections]을 검토합니다.

할당된 아웃바운드 포트 및 유휴 시간 제한은 [SNAT][azure-lb-outbound-connections]에 사용됩니다. 기본적으로 ‘표준’ SKU 부하 분산 장치는 [백엔드 풀 크기를 기반으로 하는 아웃바운드 포트 수에 대한 자동 할당][azure-lb-outbound-preallocatedports] 및 각 포트의 30분 유휴 시간 제한을 사용합니다. 이러한 값을 보려면 [az network lb outbound-rule list][az-network-lb-outbound-rule-list]를 사용하여 부하 분산 장치에 대한 아웃바운드 규칙을 표시합니다.

```azurecli-interactive
NODE_RG=$(az aks show --resource-group myResourceGroup --name myAKSCluster --query nodeResourceGroup -o tsv)
az network lb outbound-rule list --resource-group $NODE_RG --lb-name kubernetes -o table
```

이전 명령에는 부하 분산 장치에 대한 아웃바운드 규칙이 나열됩니다. 예를 들면 다음과 같습니다.

```console
AllocatedOutboundPorts    EnableTcpReset    IdleTimeoutInMinutes    Name             Protocol    ProvisioningState    ResourceGroup
------------------------  ----------------  ----------------------  ---------------  ----------  -------------------  -------------
0                         True              30                      aksOutboundRule  All         Succeeded            MC_myResourceGroup_myAKSCluster_eastus  
```

예제 출력에서는 *AllocatedOutboundPorts* 및 *IdleTimeoutInMinutes*에 대한 기본값을 보여 줍니다. *AllocatedOutboundPorts*에 대한 값 0은 백엔드 풀 크기에 따른 아웃바운드 포트 수에 자동 할당을 사용하는 아웃바운드 포트 수를 설정합니다. 예를 들어 클러스터에 50개 이하의 노드가 있는 경우 각 노드에 대한 1024개의 포트가 할당됩니다.

위의 기본 구성에 따라 SNAT 고갈이 발생할 것으로 예상되는 경우 *allocatedOutboundPorts* 또는 *IdleTimeoutInMinutes*의 설정을 변경하는 것이 좋습니다. 각각의 추가 IP 주소는 할당을 위해 64,000개의 추가 포트를 사용할 수 있지만, Azure 표준 Load Balancer는 추가 IP 주소가 추가될 때 노드당 포트를 자동으로 증가시키지 않습니다. *load-balancer-outbound-ports* 및 *load-balancer-idle-timeout* 매개 변수를 설정하여 이러한 값을 변경할 수 있습니다. 다음은 그 예입니다.

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --load-balancer-outbound-ports 0 \
    --load-balancer-idle-timeout 30
```

> [!IMPORTANT]
> 연결 또는 스케일링 문제를 방지하기 위해 *allocatedOutboundPorts*를 사용자 지정하기 전에 [필요한 할당량을 계산][calculate-required-quota]해야 합니다. *allocatedOutboundPorts*에 대해 지정하는 값도 8의 배수여야 합니다.

클러스터를 만들 때 *load-balancer-outbound-ports* 및 *load-balancer-idle-timeout* 매개 변수를 사용할 수도 있지만 *load-balancer-managed-outbound-ip-count*, *load-balancer-outbound-ips* 또는 *load-balancer-outbound-ip-prefixes* 중 하나를 지정해야 합니다.  다음은 그 예입니다.

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --vm-set-type VirtualMachineScaleSets \
    --node-count 1 \
    --load-balancer-sku standard \
    --generate-ssh-keys \
    --load-balancer-managed-outbound-ip-count 2 \
    --load-balancer-outbound-ports 0 \
    --load-balancer-idle-timeout 30
```

*load-balancer-outbound-ports* 및 *load-balancer-idle-timeout* 매개 변수를 기본값에서 변경하는 경우 전체 클러스터에 영향을 주는 부하 분산 장치 프로필의 동작에 영향을 줍니다.

### <a name="required-quota-for-customizing-allocatedoutboundports"></a>AllocatedOutboundPorts 사용자 지정에 필요한 할당량
노드 VM 수 및 원하는 할당된 아웃바운드 포트를 기반으로 아웃바운드 IP 용량이 충분해야 합니다. 아웃바운드 IP 용량이 충분한지 유효성을 검사하려면 다음 수식을 사용합니다. 
 
*outboundIPs* \* 64,000 \> *nodeVMs* \* *desiredAllocatedOutboundPorts*.
 
예를 들어 3개의 *nodeVMs* 및 50,000개의 *desiredAllocatedOutboundPorts*가 있는 경우 최소 3개 이상의 *outboundIPs*가 있어야 합니다. 필요한 것보다 더 많은 추가 아웃바운드 IP 용량을 통합하는 것이 좋습니다. 또한 아웃바운드 IP 용량을 계산할 때 클러스터 자동 크기 조정기 및 노드 풀 업그레이드의 가능성을 고려해야 합니다. 클러스터 자동 크기 조정기의 경우 현재 노드 수와 최대 노드 수를 검토하고 더 높은 값을 사용합니다. 업그레이드하는 경우 업그레이드를 허용하는 모든 노드 풀에 대해 추가 노드 VM을 고려합니다.
 
*IdleTimeoutInMinutes*를 기본값 30분이 아닌 다른 값으로 설정할 때는 워크로드에 아웃바운드 연결이 필요한 기간을 고려합니다. 또한 AKS 외부에서 사용되는 ‘표준’ SKU 부하 분산 장치에 대한 기본 시간 제한 값은 4분입니다. 특정 AKS 워크로드를 더 정확하게 반영하는 *IdleTimeoutInMinutes* 값을 지정하면 더 이상 사용되지 않는 연결로 인한 SNAT 고갈을 줄이는 데 도움이 될 수 있습니다.

## <a name="restrict-access-to-specific-ip-ranges"></a>특정 IP 범위로 액세스 제한

부하 분산 장치에 대한 가상 네트워크와 연결된 NSG(네트워크 보안 그룹)에는 기본적으로 모든 인바운드 외부 트래픽을 허용하는 규칙이 있습니다. 인바운드 트래픽에 대해 특정 IP 범위만 허용하도록 이 규칙을 업데이트할 수 있습니다. 다음 매니페스트는 *loadBalancerSourceRanges*를 사용하여 인바운드 외부 트래픽에 대한 새 IP 범위를 지정합니다.

```yaml
apiVersion: v1
kind: Service
metadata:
  name: azure-vote-front
spec:
  type: LoadBalancer
  ports:
  - port: 80
  selector:
    app: azure-vote-front
  loadBalancerSourceRanges:
  - MY_EXTERNAL_IP_RANGE
```

위의 예에서는 *MY_EXTERNAL_IP_RANGE* 범위에서 인바운드 외부 트래픽만 허용하도록 규칙을 업데이트합니다. 이 방법을 사용하여 부하 분산 장치 서비스에 대한 액세스를 제한하는 방법에 대한 자세한 내용은 [Kubernetes 설명서][kubernetes-cloud-provider-firewall]에서 확인할 수 있습니다.

## <a name="next-steps"></a>다음 단계

[Kubernetes 서비스 설명서][kubernetes-services]에서 Kubernetes 서비스에 대해 자세히 알아봅니다.

<!-- LINKS - External -->
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[kubernetes-cloud-provider-firewall]: https://kubernetes.io/docs/tasks/access-application-cluster/configure-cloud-provider-firewall/#restrict-access-for-loadbalancer-service
[kubectl-delete]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#delete
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubernetes-services]: https://kubernetes.io/docs/concepts/services-networking/service/
[aks-engine]: https://github.com/Azure/aks-engine

<!-- LINKS - Internal -->
[advanced-networking]: configure-azure-cni.md
[aks-support-policies]: support-policies.md
[aks-faq]: faq.md
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[aks-sp]: kubernetes-service-principal.md#delegate-access-to-other-azure-resources
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-aks-create]: /cli/azure/aks?view=azure-cli-latest#az-aks-create
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[az-aks-install-cli]: /cli/azure/aks?view=azure-cli-latest#az-aks-install-cli
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-group-create]: /cli/azure/group#az-group-create
[az-provider-register]: /cli/azure/provider#az-provider-register
[az-network-lb-outbound-rule-list]: /cli/azure/network/lb/outbound-rule?view=azure-cli-latest#az-network-lb-outbound-rule-list
[az-network-public-ip-show]: /cli/azure/network/public-ip?view=azure-cli-latest#az-network-public-ip-show
[az-network-public-ip-prefix-show]: /cli/azure/network/public-ip/prefix?view=azure-cli-latest#az-network-public-ip-prefix-show
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
[azure-lb]: ../load-balancer/load-balancer-overview.md
[azure-lb-comparison]: ../load-balancer/skus.md
[azure-lb-outbound-rules]: ../load-balancer/load-balancer-outbound-rules-overview.md#snatports
[azure-lb-outbound-connections]: ../load-balancer/load-balancer-outbound-connections.md#snat
[azure-lb-outbound-preallocatedports]: ../load-balancer/load-balancer-outbound-connections.md#preallocatedports
[azure-lb-outbound-rules-overview]: ../load-balancer/load-balancer-outbound-rules-overview.md
[install-azure-cli]: /cli/azure/install-azure-cli
[internal-lb-yaml]: internal-lb.md#create-an-internal-load-balancer
[kubernetes-concepts]: concepts-clusters-workloads.md
[use-kubenet]: configure-kubenet.md
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[calculate-required-quota]: #required-quota-for-customizing-allocatedoutboundports
