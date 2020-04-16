---
title: 표준 SKU 로드 밸레인저 사용
titleSuffix: Azure Kubernetes Service
description: 표준 SKU와 함께 로드 밸러버를 사용하여 AKS(Azure Kubernetes Service)를 사용하여 서비스를 노출하는 방법을 알아봅니다.
services: container-service
author: zr-msft
ms.topic: article
ms.date: 09/27/2019
ms.author: zarhoads
ms.openlocfilehash: c1d2c0e48394fbde1b595ae4b405d84f437dc5e4
ms.sourcegitcommit: d6e4eebf663df8adf8efe07deabdc3586616d1e4
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81392823"
---
# <a name="use-a-standard-sku-load-balancer-in-azure-kubernetes-service-aks"></a>Azure Kubernetes 서비스(AKS)에서 표준 SKU 로드 밸러커 사용

키버넷(Kubernetes) 서비스(AKS)에서 형식의 `LoadBalancer` Kubernetes 서비스를 통해 응용 프로그램에 대한 액세스를 제공하려면 Azure 로드 밸런서를 사용할 수 있습니다. AKS에서 실행되는 로드 밸런서를 내부 또는 외부 로드 밸런서로 사용할 수 있습니다. 내부 로드 밸런서는 AKS 클러스터와 동일한 가상 네트워크에서 실행되는 응용 프로그램에서만 Kubernetes 서비스에 액세스할 수 있습니다. 외부 로드 밸런서는 수신에 대해 하나 이상의 공용 IP를 수신하고 공용 IP를 사용하여 Kubernetes 서비스에 외부적으로 액세스할 수 있도록 합니다.

Azure Load Balancer는 ‘기본’ 및 ‘표준’이라는 두 SKU에서 사용할 수 있습니다.**** 기본적으로 *표준* SKU는 AKS 클러스터를 만들 때 사용됩니다. *표준* SKU 로드 밸로이터를 사용하면 더 큰 백 엔드 풀 크기 및 가용성 영역과 같은 추가 기능과 기능을 제공합니다. 사용할 로드 밸러울러와 *표준* *Basic* 부하 밸로서간의 차이점을 이해하는 것이 중요합니다. AKS 클러스터를 만든 후에는 해당 클러스터의 로드 밸런서 SKU를 변경할 수 없습니다. *기본* 및 *표준* SKU에 대한 자세한 내용은 [Azure 로드 밸러서 SKU 비교를][azure-lb-comparison]참조하십시오.

이 문서에서는 Kubernetes 및 Azure 로드 밸러서 개념에 대한 기본적인 이해를 가정합니다. 자세한 내용은 [AKS(Azure Kubernetes Service)에 대한 Kubernetes 핵심 개념][kubernetes-concepts] 및 [Azure 로드 밸러저란 무엇입니까?][azure-lb]

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

CLI를 로컬로 설치하고 사용하도록 선택한 경우 이 문서에서는 Azure CLI 버전 2.0.81 이상을 실행해야 합니다. `az --version`을 실행하여 버전을 찾습니다. 설치 또는 업그레이드해야 하는 경우 [Azure CLI 설치][install-azure-cli]를 참조하세요.

## <a name="before-you-begin"></a>시작하기 전에

이 문서에서는 *표준* SKU Azure 로드 밸런서가 있는 AKS 클러스터가 있다고 가정합니다. AKS 클러스터가 필요한 경우 AKS 빠른 시작[Azure CLI 사용][aks-quickstart-cli] 또는 [Azure Portal 사용][aks-quickstart-portal]을 참조하세요.

AKS 클러스터 서비스 주체는 기존 서브넷 또는 리소스 그룹을 사용하는 경우 네트워크 리소스를 관리할 수 있는 권한도 필요합니다. 일반적으로 위임된 리소스의 서비스 주체에 *네트워크 기여자* 역할을 할당합니다. 서비스 주체 대신 관리되는 ID를 사용할 수 있는 시스템에 사용 권한을 사용할 수도 있습니다. 자세한 내용은 [관리되는 ID 사용을](use-managed-identity.md)참조하십시오. 사용 권한에 대한 자세한 내용은 [다른 Azure 리소스에 대한 AKS 액세스 대리자를][aks-sp]참조하십시오.

### <a name="moving-from-a-basic-sku-load-balancer-to-standard-sku"></a>기본 SKU 로드 밸레인저에서 표준 SKU로 전환

기본 SKU 로드 밸런서가 있는 기존 클러스터가 있는 경우 표준 SKU 로드 밸런서가 있는 클러스터를 사용하도록 마이그레이션할 때 주의해야 할 중요한 동작 차이가 있습니다.

예를 들어 클러스터를 마이그레이션하기 위해 파란색/녹색 배포를 만드는 `load-balancer-sku` 것은 클러스터 생성 시만 클러스터 유형을 정의할 수 있으므로 일반적인 방법입니다. 그러나 *기본 SKU* 로드 밸러처는 표준 *SKU* IP 주소가 필요하므로 *표준 SKU* 로드 밸러저와 호환되지 않는 기본 *SKU* IP 주소를 사용합니다. 로드 밸런서 SKU를 업그레이드하기 위해 클러스터를 마이그레이션할 때 호환되는 IP 주소 SKU가 있는 새 IP 주소가 필요합니다.

클러스터를 마이그레이션하는 방법에 대한 자세한 내용은 [마이그레이션 고려 사항에 대한 설명서를](aks-migration.md) 방문하여 마이그레이션 할 때 고려해야 할 중요한 항목 목록을 확인하십시오. 아래 제한 사항은 AKS에서 표준 SKU 로드 밸러저를 사용할 때 주의해야 할 중요한 동작 차이이기도 합니다.

### <a name="limitations"></a>제한 사항

*표준* SKU를 사용하여 로드 밸런서를 지원하는 AKS 클러스터를 만들고 관리할 때 다음과 같은 제한 사항이 적용됩니다.

* AKS 클러스터에서 송신 트래픽을 허용하려면 하나 이상의 공용 IP 또는 IP 접두사가 필요합니다. 공용 IP 또는 IP 접두사는 제어 평면과 에이전트 노드 간의 연결을 유지하고 이전 버전의 AKS와의 호환성을 유지하려면 필요합니다. *표준* SKU 로드 밸런서를 사용 하 여 공용 IP 또는 IP 접두사를 지정 하는 다음 옵션이 있습니다.
    * 사용자 고유의 공용 IP를 제공합니다.
    * 사용자 고유의 공용 IP 접두사를 제공합니다.
    * AKS 클러스터가 *MC_* AKS 클러스터로 만든 동일한 리소스 그룹에 있는 많은 *표준* SKU 공용 IP를 만들 수 있도록 최대 100개까지 지정합니다. AKS는 공용 IP를 *표준* SKU 로드 밸런서에 할당합니다. 기본적으로 공용 IP, 공용 IP 접두사 또는 IP 수가 지정되지 않은 경우 AKS 클러스터와 동일한 리소스 그룹에 하나의 공용 IP가 자동으로 생성됩니다. 또한 공용 주소를 허용하고 IP 생성을 금지하는 Azure 정책을 만들지 않아야 합니다.
* 로드 밸러블러에 *표준* SKU를 사용하는 경우 Kubernetes 버전 *1.13 이상을*사용해야 합니다.
* 로드 밸런서 SKU 정의는 AKS 클러스터를 만들 때만 수행할 수 있습니다. AKS 클러스터를 만든 후에는 로드 밸런서 SKU를 변경할 수 없습니다.
* 단일 클러스터에서 한 가지 유형의 로드 밸런서 SKU(기본 또는 표준)만 사용할 수 있습니다.
* *스탠다드* SKU 로드 밸론서는 *표준* SKU IP 주소만 지원합니다.

## <a name="use-the-standard-sku-load-balancer"></a>*표준* SKU 로드 밸레인저 사용

AKS 클러스터를 만들 때 기본적으로 *표준* SKU 로드 밸런서가 해당 클러스터에서 서비스를 실행할 때 사용됩니다. 예를 들어 [Azure CLI를 사용하는 빠른 시작은][aks-quickstart-cli] *표준* SKU 로드 밸러워를 사용하는 샘플 응용 프로그램을 배포합니다.

> [!IMPORTANT]
> 공용 IP 주소는 UDR(사용자 정의 경로)을 사용자 지정하여 피할 수 있습니다. UDR로 AKS 클러스터의 아웃바운드 형식을 지정하면 AKS만든 Azure 로드 밸런서에 대한 IP 프로비저닝 및 백 엔드 풀 설정을 건너뛸 수 있습니다. [클러스터의 설정을 `outboundType` 'userDefined라우팅'으로 설정합니다.](egress-outboundtype.md)

## <a name="configure-the-load-balancer-to-be-internal"></a>로드 밸래서를 내부로 구성

로드 밸런서를 내부로 구성하고 공용 IP를 노출하지 않도록 구성할 수도 있습니다. 로드 밸런서를 내부로 `service.beta.kubernetes.io/azure-load-balancer-internal: "true"` 구성하려면 *LoadBalancer* 서비스에 추가합니다. 예제 yaml 매니페스트뿐만 아니라 내부 부하 분산에 대한 자세한 내용은 여기에서 볼 수 [있습니다.][internal-lb-yaml]

## <a name="scale-the-number-of-managed-public-ips"></a>관리되는 공용 IP 수 확장

기본적으로 생성되는 관리되는 아웃바운드 공용 IP를 사용하여 *표준* SKU 로드 밸런서를 사용하는 경우 *로드 밸런서 관리 ip-count* 매개 변수를 사용하여 관리되는 아웃바운드 공용 IP 수를 확장할 수 있습니다.

기존 클러스터를 업데이트하려면 다음 명령을 실행합니다. 이 매개 변수는 클러스터 만들기 시간에 여러 개의 관리되는 아웃바운드 공용 IP를 갖도록 설정할 수도 있습니다.

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --load-balancer-managed-outbound-ip-count 2
```

위의 예제에서는 *myResourceGroup의* *myAKSCluster* 클러스터에 대해 관리되는 아웃바운드 공용 IP 수를 *2로* 설정합니다. 

*또한 로드 밸런서 관리 ip-count* 매개 변수를 사용하여 `--load-balancer-managed-outbound-ip-count` 매개 변수를 적용하고 원하는 값으로 설정하여 클러스터를 만들 때 관리되는 아웃바운드 공용 IP의 초기 수를 설정할 수 있습니다. 관리되는 아웃바운드 공용 IP의 기본 수는 1입니다.

## <a name="provide-your-own-public-ips-or-prefixes-for-egress"></a>송신에 대한 자체 공용 IP 또는 접두사 제공

*표준* SKU 로드 밸런서를 사용하는 경우 AKS 클러스터는 AKS 클러스터에 대해 생성된 동일한 리소스 그룹에 공용 IP를 자동으로 만들고 공용 IP를 *표준* SKU 로드 밸런서에 할당합니다. 또는 클러스터 생성 시 자체 공용 IP를 할당하거나 기존 클러스터의 로드 밸런서 속성을 업데이트할 수 있습니다.

여러 IP 주소 또는 접두사를 가져오면 단일 로드 밸러서 개체 뒤에 IP 주소를 정의할 때 여러 백업 서비스를 정의할 수 있습니다. 특정 노드의 송신 끝점은 연결된 서비스에 따라 달라집니다.

> [!IMPORTANT]
> *표준* SKU 로드 밸런서와 함께 송신에 *표준* SKU 공용 IP를 사용해야 합니다. [az 네트워크 공용 IP 쇼][az-network-public-ip-show] 명령을 사용하여 공용 IP의 SKU를 확인할 수 있습니다.
>
> ```azurecli-interactive
> az network public-ip show --resource-group myResourceGroup --name myPublicIP --query sku.name -o tsv
> ```

az [네트워크 공용 IP 표시][az-network-public-ip-show] 명령을 사용하여 공용 IP의 IP를 나열합니다.

```azurecli-interactive
az network public-ip show --resource-group myResourceGroup --name myPublicIP --query id -o tsv
```

위의 명령은 *myResourceGroup* 리소스 그룹의 *myPublicIP* 공용 IP에 대한 ID를 표시합니다.

*로드 밸런서 아웃바운드 ips* 매개 변수를 사용하여 *az aks 업데이트* 명령을 사용하여 공용 IP로 클러스터를 업데이트합니다.

다음 예제에서는 이전 명령의 아이디와 함께 *로드 밸러서 아웃바운드 ips* 매개 변수를 사용합니다.

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --load-balancer-outbound-ips <publicIpId1>,<publicIpId2>
```

*표준* SKU 로드 밸런서를 사용하여 송신에 공용 IP 접두사를 사용할 수도 있습니다. 다음 예제에서는 [az 네트워크 공용 IP 접두사 표시][az-network-public-ip-prefix-show] 명령을 사용하여 공용 IP 접두사의 IP 를 나열합니다.

```azurecli-interactive
az network public-ip prefix show --resource-group myResourceGroup --name myPublicIPPrefix --query id -o tsv
```

위의 명령은 *myResourceGroup* 리소스 그룹의 *myPublicIPPrefix* 공용 IP 접두사에 대한 ID를 표시합니다.

다음 예제에서는 이전 명령의 아이디와 함께 *로드 밸러저 아웃-ip-접두사* 매개 변수를 사용합니다.

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --load-balancer-outbound-ip-prefixes <publicIpPrefixId1>,<publicIpPrefixId2>
```

> [!IMPORTANT]
> 공용 IP 및 IP 접두사는 AKS 클러스터와 동일한 리전 및 동일한 구독의 일부여야 합니다. 

### <a name="define-your-own-public-ip-or-prefixes-at-cluster-create-time"></a>클러스터 생성 시간에 고유한 공용 IP 또는 접두사 정의

클러스터 생성 시 송신에 대한 자체 IP 주소 또는 IP 접두사를 가져와 허용 목록 송신 끝점과 같은 시나리오를 지원할 수 있습니다. 클러스터 생성 단계에 위에 표시된 동일한 매개 변수를 부가하여 클러스터 수명 주기가 시작될 때 고유한 공용 IP 및 IP 접두사를 정의합니다.

az *aks create* 명령을 *로드 밸런서 아웃바운드 ips* 매개 변수를 사용하여 처음에 공용 IP를 사용하여 새 클러스터를 만듭니다.

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

az *aks create* 명령을 *로드 밸런서 아웃바운드-ip-접두사* 매개 변수를 사용하여 처음에 공용 IP 접두사가 있는 새 클러스터를 만듭니다.

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

## <a name="configure-outbound-ports-and-idle-timeout"></a>아웃바운드 포트 및 유휴 시간 시간 구성

> [!WARNING]
> 다음 섹션에서는 대규모 네트워킹의 고급 시나리오 또는 기본 구성으로 SNAT 소모 문제를 해결하기 위한 것입니다. 정상 클러스터를 유지 하려면 기본값에서 *할당된OutboundPorts* 또는 *IdleTimeoutInMinutes를* 변경 하기 전에 VM 및 IP 주소에 대 한 사용 가능한 할당량의 정확한 인벤토리가 있어야 합니다.
> 
> *할당된아웃바운드 포트* 및 *IdleTimeoutInMinutes에* 대한 값을 변경하면 로드 밸러버에 대한 아웃바운드 규칙의 동작이 크게 변경될 수 있습니다. 이러한 값을 업데이트하기 전에 [로드 밸류에이션 아웃바운드 규칙,][azure-lb-outbound-rules-overview] [로드 밸류에이터 아웃바운드 규칙][azure-lb-outbound-rules]및 [아웃바운드 연결을][azure-lb-outbound-connections] 검토하여 변경 내용의 영향을 완전히 이해합니다.

아웃바운드 할당 된 포트 및 유휴 시간 [정SNAT에][azure-lb-outbound-connections]사용 됩니다. 기본적으로 *표준* SKU 로드 밸런서에서는 [백 엔드 풀 크기에 따라 아웃바운드 포트 수와][azure-lb-outbound-preallocatedports] 각 포트에 대해 30분의 유휴 시간 초과에 대해 자동 할당을 사용합니다. 이러한 값을 보려면 [az 네트워크 lb 아웃바운드 규칙 목록을][az-network-lb-outbound-rule-list] 사용하여 로드 밸러터에 대한 아웃바운드 규칙을 표시합니다.

```azurecli-interactive
NODE_RG=$(az aks show --resource-group myResourceGroup --name myAKSCluster --query nodeResourceGroup -o tsv)
az network lb outbound-rule list --resource-group $NODE_RG --lb-name kubernetes -o table
```

이전 명령에는 로드 밸러버에 대한 아웃바운드 규칙이 나열됩니다.

```console
AllocatedOutboundPorts    EnableTcpReset    IdleTimeoutInMinutes    Name             Protocol    ProvisioningState    ResourceGroup
------------------------  ----------------  ----------------------  ---------------  ----------  -------------------  -------------
0                         True              30                      aksOutboundRule  All         Succeeded            MC_myResourceGroup_myAKSCluster_eastus  
```

예제 출력에는 *할당된아웃바운드 포트* 및 *IdleTimeoutIn분에*대한 기본값이 표시됩니다. *할당된OutboundPorts에* 대한 값이 0이면 백 엔드 풀 크기를 기반으로 아웃바운드 포트 수에 대한 자동 할당을 사용하여 아웃바운드 포트 수를 설정합니다. 예를 들어 클러스터에 50개 이하의 노드가 있는 경우 각 노드에 대해 1024개의 포트가 할당됩니다.

위의 기본 구성에 따라 SNAT 소모에 직면할 것으로 예상되는 경우 *할당된아웃바운드 포트* 또는 *IdleTimeoutInMinutes* 설정을 변경하는 것이 좋습니다. 각 추가 IP 주소는 할당을 위해 64,000개의 추가 포트를 사용할 수 있지만 Azure 표준 로드 밸런서가 더 많은 IP 주소를 추가할 때 노드당 포트를 자동으로 증가시키지 않습니다. *로드 밸러서 아웃바운드 포트* 및 로드 *밸류에이터-유휴 시간 설정* 매개 변수를 설정하여 이러한 값을 변경할 수 있습니다. 다음은 그 예입니다.

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --load-balancer-outbound-ports 0 \
    --load-balancer-idle-timeout 30
```

> [!IMPORTANT]
> 연결 또는 확장 문제를 방지하려면 *할당된OutboundPorts를* 사용자 지정하기 전에 [필요한 할당량을 계산해야][calculate-required-quota] 합니다. *할당된아웃바운드포트에* 대해 지정한 값도 8의 배수여야 합니다.

클러스터를 만들 때 *로드 밸런서 아웃바운드 포트와* *로드 밸런서-유휴 시간 지정* 매개 변수를 사용할 수도 있지만 로드 *밸런서 관리 아웃바운드 ip 수,* 로드 *밸런서-아웃바운드 ip또는* *로드 밸런서 아웃바운드 ip-접두사도* 지정해야 합니다.  다음은 그 예입니다.

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

*로드 밸런서 아웃바운드 포트와* 로드 *밸런서-유휴 시간 설정* 매개 변수를 기본값에서 변경하면 전체 클러스터에 영향을 미치는 로드 밸런서 프로파일의 동작에 영향을 줍니다.

### <a name="required-quota-for-customizing-allocatedoutboundports"></a>할당된 아웃바운드 포트를 사용자 지정하기 위한 필수 할당량
노드 VM 수와 원하는 할당된 아웃바운드 포트수에 따라 충분한 아웃바운드 IP 용량이 있어야 합니다. 아웃바운드 IP 용량이 충분한지 확인하려면 다음 수식을 사용하십시오. 
 
*아웃바운드IPPs* \* 64,000 \> *노드VM* \* *원하는할당아웃바운드포트*.
 
예를 들어 *3개의 노드VM과*50,000개의 *원하는 할당된 아웃바운드 포트가*있는 경우 최소 3개의 *아웃바운드 IP가*있어야 합니다. 필요한 것 이상으로 추가 아웃바운드 IP 용량을 통합하는 것이 좋습니다. 또한 아웃바운드 IP 용량을 계산할 때 클러스터 자동 크기 조정기와 노드 풀 업그레이드 가능성을 고려해야 합니다. 클러스터 자동 크기 조정기의 경우 현재 노드 수와 최대 노드 수를 검토하고 더 높은 값을 사용합니다. 업그레이드의 경우 업그레이드를 허용하는 모든 노드 풀에 대해 추가 노드 VM을 고려합니다.
 
*IdleTimeoutInMinutes을* 기본값인 30분과 다른 값으로 설정하는 경우 워크로드에 아웃바운드 연결이 필요한 기간을 고려합니다. 또한 AKS 외부에서 사용되는 *표준* SKU 로드 밸로드워서의 기본 시간 초과 값이 4분임을 고려합니다. 특정 AKS 워크로드를 보다 정확하게 반영하는 *IdleTimeoutInMinutes* 값은 더 이상 사용되지 않는 연결을 묶어 서 발생하는 SNAT 소모를 줄이는 데 도움이 될 수 있습니다.

## <a name="restrict-access-to-specific-ip-ranges"></a>특정 IP 범위에 대한 액세스 제한

로드 밸러버의 가상 네트워크와 연결된 NSG(네트워크 보안 그룹)에는 기본적으로 모든 인바운드 외부 트래픽을 허용하는 규칙이 있습니다. 인바운드 트래픽에 대한 특정 IP 범위만 허용하도록 이 규칙을 업데이트할 수 있습니다. 다음 매니페스트는 *loadBalancerSourceRanges를* 사용하여 인바운드 외부 트래픽에 대한 새 IP 범위를 지정합니다.

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

위의 예제에서는 *MY_EXTERNAL_IP_RANGE* 범위에서인바운드 외부 트래픽만 허용하도록 규칙을 업데이트합니다. 이 메서드를 사용하여 로드 밸러터 서비스에 대한 액세스를 제한하는 방법에 대한 자세한 내용은 [Kubernetes 설명서에서][kubernetes-cloud-provider-firewall]확인할 수 있습니다.

## <a name="next-steps"></a>다음 단계

[Kubernetes 서비스 설명서][kubernetes-services]에서 Kubernetes 서비스를 알아봅니다.

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
[azure-lb-comparison]: ../load-balancer/concepts-limitations.md#skus
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
