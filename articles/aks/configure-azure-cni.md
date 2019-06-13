---
title: AKS(Azure Kubernetes Service)에서 Azure CNI 네트워킹 구성
description: AKS 클러스터를 기존 가상 네트워크와 서브넷에 배포하는 것을 포함하여 AKS(Azure Kubernetes Service)에서 Azure CNI 네트워크를 구성하는 방법을 알아봅니다.
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: article
ms.date: 06/03/2019
ms.author: iainfou
ms.openlocfilehash: 25ff618045c65371b1bddd8aeb32166b3e168a93
ms.sourcegitcommit: 600d5b140dae979f029c43c033757652cddc2029
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/04/2019
ms.locfileid: "66497211"
---
# <a name="configure-azure-cni-networking-in-azure-kubernetes-service-aks"></a>AKS(Azure Kubernetes Service)에서 Azure CNI 네트워킹 구성

기본적으로 AKS 클러스터는 [kubenet][kubenet]을 사용합니다. 그러면 가상 네트워크 및 서브넷이 만들어집니다. *Kubenet*을 사용하면 노드는 가상 네트워크 서브넷의 IP 주소를 얻습니다. 그런 다음, NAT(Network Address Translation)이 노드에서 구성되며 pod가 노드 IP 뒤에 “숨겨진” IP 주소를 받습니다. 이 방법을 사용하면 네트워크 공간에서 pod가 사용하도록 예약해야 하는 IP 주소의 수가 줄어듭니다.

[Azure CNI(컨테이너 네트워킹 인터페이스)][cni-networking]를 사용하면 모든 pod가 서브넷에서 IP 주소를 가져오고 직접 액세스할 수 있습니다. 이러한 IP 주소는 네트워크 공간에서 고유해야 하며 미리 계획해야 합니다. 각 노드에는 지원하는 최대 Pod 수에 대한 구성 매개 변수가 있습니다. 그러면 노드당 동일한 IP 주소 수가 해당 노드에 대해 미리 예약됩니다. 이 방식을 사용할 경우 더 많은 계획이 필요하며, 애플리케이션 요구가 증가하면서 IP 주소가 고갈되거나 더 큰 서브넷에서 클러스터를 다시 빌드해야 할 수 있습니다.

이 문서에서는 *Azure CNI* 네트워킹을 사용하여 AKS 클러스터용 가상 네트워크 서브넷을 만들고 사용하는 방법에 대해 설명합니다. 네트워킹 옵션 및 고려 사항에 대한 자세한 내용은 [Kubernetes 및 AKS에 대한 네트워크 개념][aks-network-concepts]을 참조하세요.

## <a name="prerequisites"></a>필수 조건

* AKS 클러스터에 대한 가상 네트워크는 아웃바운드 인터넷 연결을 허용해야 합니다.
* 동일한 서브넷에 둘 이상의 AKS 클러스터를 만들지 마세요.
* AKS 클러스터는 Kubernetes 서비스 주소 범위에 `169.254.0.0/16`, `172.30.0.0/16` 또는 `172.31.0.0/16`을 사용하지 못할 수도 있습니다.
* AKS 클러스터에서 사용되는 서비스 주체에는 가상 네트워크 내의 서브넷에 대해 [네트워크 참가자](../role-based-access-control/built-in-roles.md#network-contributor) 이상의 권한이 있어야 합니다. 기본 제공 네트워크 참가자 역할을 사용하는 대신 [사용자 지정 역할](../role-based-access-control/custom-roles.md)을 정의하려는 경우 다음 권한이 필요합니다.
  * `Microsoft.Network/virtualNetworks/subnets/join/action`
  * `Microsoft.Network/virtualNetworks/subnets/read`

## <a name="plan-ip-addressing-for-your-cluster"></a>클러스터에 대한 IP 주소 지정 계획

Azure CNI 네트워킹으로 구성한 클러스터에는 추가 계획이 필요합니다. 가상 네트워크 및 해당 서브넷의 크기는 실행하려는 Pod의 수와 클러스터에 대한 노드 수에 부합되어야 합니다.

Pod 및 클러스터 노드의 IP 주소는 가상 네트워크 내의 지정된 서브넷에서 할당됩니다. 각 노드는 기본 IP 주소로 구성됩니다. 기본적으로 30개의 추가 IP 주소가 노드에서 예약된 Pod에 할당된 Azure CNI에 의해 미리 구성됩니다. 클러스터를 스케일 아웃할 때 각 노드는 서브넷의 IP 주소로 비슷하게 구성됩니다. [노드당 최대 Pod](#maximum-pods-per-node)를 확인할 수도 있습니다.

> [!IMPORTANT]
> 필요한 IP 주소 수에는 업그레이드 및 크기 조정 작업에 대한 고려가 반영되어야 합니다. 고정 노드 수만 지원하는 IP 주소 범위를 설정할 경우 클러스터의 업그레이드나 크기 조정이 불가능합니다.
>
> - AKS 클러스터를 **업그레이드**할 경우 새 노드가 클러스터에 배포됩니다. 서비스 및 워크로드가 새 노드에서 실행되기 시작하고 기존 노드가 클러스터에서 제거됩니다. 이 업그레이드 배포 프로세스를 위해서는 최소 하나의 추가 IP 주소 블록을 사용할 수 있어야 합니다. 그러면 노드 수가 `n + 1`입니다.
>   - 이 고려 사항은 (현재 AKS에서 미리 보기)는에서 Windows Server 노드 풀을 사용 하는 경우에 특히 중요 합니다. AKS에서 Windows 서버 노드에서 Windows 업데이트를 자동으로 적용 되지 않습니다, 그리고 노드 풀에서 업그레이드 대신 수행 합니다. 이 업그레이드는 최신 창 Server 2019 노드 기본 이미지 및 보안 패치를 사용 하 여 새 노드를 배포합니다. Windows Server 노드 풀을 업그레이드 하는 방법은 참조 하세요 [AKS에 노드 풀을 업그레이드][nodepool-upgrade]합니다.
>
> - AKS 클러스터를 **확장**할 경우 새 노드가 클러스터에 배포됩니다. 서비스 및 워크로드가 새 노드에서 실행되기 시작합니다. IP 주소 범위에서는 클러스터가 지원할 수 있는 노드 및 Pod 수를 조정하려는 방법을 고려해야 합니다. 업그레이드 작업에 대한 추가 노드 하나도 포함되어야 합니다. 그러면 노드 수가 `n + number-of-additional-scaled-nodes-you-anticipate + 1`입니다.

노드가 최대 Pod 수를 실행하고 Pod를 정기적으로 제거하고 배포하려는 경우 노드당 추가 IP 주소 역시 반영해야 합니다. 이러한 추가 IP 주소에서는 새 서비스가 배포되어 주소를 획득하기 위해 서비스가 삭제되고 IP 주소가 해제되는 데 몇 초 정도 걸린다는 점을 고려합니다.

AKS 클러스터에 대한 IP 주소 계획은 노드 및 Pod에 대한 하나 이상의 서브넷에서 가상 네트워크 및 Kubernetes 서비스 주소 범위로 구성됩니다.

| 주소 범위 / Azure 리소스 | 한도 및 크기 조정 |
| --------- | ------------- |
| 가상 네트워크 | Azure Virtual Network는 /8 이하일 수 있지만 구성된 IP 주소 수는 65,536개로 제한됩니다. |
| 서브넷 | 클러스터에서 프로비전될 수 있는 노드, 포드와 모든 Kubernetes 및 Azure 리소스를 수용할 만큼 커야 합니다. 예를 들어, 내부 Azure Load Balancer를 배포하는 경우, 해당 프런트 엔드 IP는 공용 IP가 아닌 클러스터 서브넷에서 할당됩니다. 서브넷 크기도 업그레이드 작업이나 향후의 크기 조정 요구를 반영해야 합니다.<p />업그레이드 작업을 위한 추가 노드를 포함한 *최소* 서브넷 크기를 계산하려면`(number of nodes + 1) + ((number of nodes + 1) * maximum pods per node that you configure)`<p/>50 노드 클러스터의 예: `(51) + (51  * 30 (default)) = 1,581`(/21 이상)<p/>추가 10개 노드를 확장하는 프로비전도 포함하는 50개 노드 클러스터의 예: `(61) + (61 * 30 (default)) = 1,891` (/21 이상)<p>클러스터를 만들 때 노드당 최대 Pod를 지정하지 않으면 노드당 최대 Pod 수는 *30*개로 설정됩니다. 필요한 최소 IP 주소 수는 이 값을 기준으로 합니다. 다른 최댓값을 기준으로 최소 IP 주소 요구 사항을 계산하는 경우 [노드당 최대 Pod 수를 구성하는 방법](#configure-maximum---new-clusters)을 참조하여 클러스터를 배포할 때 이 값을 설정하세요 |
| Kubernetes 서비스 주소 범위 | 이 범위는 이 가상 네트워크 또는 이 가상 네트워크에 연결된 모든 네트워크 요소에서 사용하지 말아야 합니다. 서비스 주소 CIDR은 /12보다 작아야 합니다. |
| Kubernetes DNS 서비스 IP 주소 | 클러스터 서비스 검색에서 사용되는 Kubernetes 서비스 주소 범위 내의 IP 주소입니다(kube-dns). .1과 같은 주소 범위의 첫 번째 IP 주소를 사용하지 마세요. 서브넷 범위의 첫 번째 주소는 *kubernetes.default.svc.cluster.local* 주소에 사용됩니다. |
| Docker 브리지 주소 | 노드에서 Docker 브리지 IP 주소로 사용되는 IP 주소(CIDR 표기법)입니다. 172.17.0.1/16의 기본값 |

## <a name="maximum-pods-per-node"></a>노드당 최대 포드

AKS 클러스터에 노드당 pod의 최대 수는 250. 노드당 *기본* 최대 pod 수는 *Kubenet* 및 *Azure CNI* 네트워킹과 클러스터 배포 방법에 따라 다릅니다.

| 배포 방법 | Kubenet 기본 | Azure CNI 기본 | 배포 시 구성 가능 |
| -- | :--: | :--: | -- |
| Azure CLI | 110 | 30 | 예 (최대 250) |
| Resource Manager 템플릿 | 110 | 30 | 예 (최대 250) |
| 포털 | 110 | 30 | 아닙니다. |

### <a name="configure-maximum---new-clusters"></a>최댓값 구성 - 새 클러스터

*클러스터 배포 시간에만* 노드당 최대 Pod 수를 구성할 수 있습니다. Azure CLI 또는 Resource Manager 템플릿을 사용 하 여 배포 하는 경우에 250으로 노드 값 당 최대 pod를 설정할 수 있습니다.

| 네트워킹 | 최소 | 최대 |
| -- | :--: | :--: |
| Azure CNI | 30 | 250 |
| Kubenet | 30 | 110 |

> [!NOTE]
> 위의 표에 최소값 AKS 서비스에서 엄격 하 게 적용 됩니다. 이렇게 하면 되므로 방지할 수 클러스터부터 표시 최소값 보다 낮은 maxPods 값을 하지 설정할 수 있습니다.

* **Azure CLI**: [az aks create][az-aks-create] 명령을 사용하여 클러스터를 배포할 때 `--max-pods` 인수를 지정합니다. 최대값은 250입니다.
* **Resource Manager 템플릿**: Resource Manager 템플릿을 사용하여 클러스터를 배포할 때 [ManagedClusterAgentPoolProfile] 개체에 `maxPods` 속성을 지정합니다. 최대값은 250입니다.
* **Azure 포털**: Azure Portal을 사용하여 클러스터를 배포하는 경우 노드당 최대 Pod 수를 변경할 수 없습니다. Azure Portal을 사용하여 배포하는 경우 Azure CNI 네트워킹 클러스터는 노드당 30개 Pod로 제한됩니다.

### <a name="configure-maximum---existing-clusters"></a>최댓값 구성 - 기존 클러스터

기존 AKS 클러스터의 노드당 최대 Pod 수는 변경할 수 없습니다. 클러스터를 처음 배포할 때만 이 값을 조정할 수 있습니다.

## <a name="deployment-parameters"></a>배포 매개 변수

AKS 클러스터를 만들 때 Azure CNI 네트워킹에서 다음 매개 변수를 구성할 수 있습니다.

**가상 네트워크**: Kubernetes 클러스터를 배포하려는 가상 네트워크입니다. 클러스터에 대해 새 가상 네트워크를 만들려는 경우 *새로 만들기*를 선택하고 *가상 네트워크 만들기* 섹션의 단계를 따릅니다. Azure Virtual Network의 제한 및 할당량에 대한 내용은 [Azure 구독 및 서비스 제한, 할당량 및 제약 조건](../azure-subscription-service-limits.md#azure-resource-manager-virtual-networking-limits)을 참조하세요.

**서브넷**: 클러스터를 배포하려는 가상 네트워크 내의 서브넷입니다. 클러스터에 대해 가상 네트워크에 새 서브넷을 만들려는 경우 *새로 만들기*를 선택하고 *서브넷 만들기* 섹션의 단계를 따릅니다. 하이브리드 연결의 경우 주소 범위가 환경의 다른 가상 네트워크와 겹쳐서는 안 됩니다.

**Kubernetes 서비스 주소 범위**: 이는 Kubernetes가 클러스터의 내부 [서비스][services]에 할당하는 가상 IP의 세트입니다. 다음 요구 사항을 충족하는 모든 프라이빗 주소 범위를 사용할 수 있습니다.

* 클러스터의 가상 네트워크 IP 주소 범위에 속하지 않아야 합니다.
* 클러스터 가상 네트워크가 피어링된 다른 가상 네트워크와 겹치지 않아야 합니다.
* 온-프레미스 IP와 겹치지 않아야 합니다.
* `169.254.0.0/16`, `172.30.0.0/16` 또는 `172.31.0.0/16` 범위에 속하지 않아야 합니다.

기술적으로 클러스터와 동일한 가상 네트워크 내의 서비스 주소 범위를 지정할 수 있지만 이는 권장되지 않습니다. 겹치는 IP 범위를 사용한 경우 예측할 수 없는 동작이 발생할 수 있습니다. 자세한 내용은 이 문서의 [FAQ](#frequently-asked-questions) 섹션을 참조하세요. Kubernetes 서비스에 자세한 내용은 Kubernetes 설명서의 [서비스][services]를 참조하세요.

**Kubernetes DNS 서비스 IP 주소**:  클러스터의 DNS 서비스에 대한 IP 주소입니다. 이 주소는 *Kubernetes 서비스 주소 범위*에 속해야 합니다. .1과 같은 주소 범위의 첫 번째 IP 주소를 사용하지 마세요. 서브넷 범위의 첫 번째 주소는 *kubernetes.default.svc.cluster.local* 주소에 사용됩니다.

**Docker 브리지 주소**: Docker 브리지에 할당할 IP 주소 및 넷마스크입니다. AKS 노드는 Docker 브리지를 통해 기본 관리 플랫폼과 통신할 수 있습니다. 이 IP 주소는 클러스터의 가상 네트워크 IP 주소 범위 내에 속하지 않아야 하고 네트워크에서 사용 중인 다른 주소 범위와 겹쳐서는 안 됩니다.

## <a name="configure-networking---cli"></a>네트워킹 구성 - CLI

Azure CLI를 사용하여 AKS 클러스터를 만드는 경우 Azure CNI 네트워킹을 구성할 수도 있습니다. 다음 명령을 사용하여 Azure CNI 네트워킹 기능을 사용하는 새로운 AKS 클러스터를 만듭니다.

먼저 기존 서브넷의 서브넷 리소스 ID를 조인할 AKS 클러스터로 가져옵니다.

```azurecli-interactive
$ az network vnet subnet list \
    --resource-group myVnet \
    --vnet-name myVnet \
    --query [].id --output tsv

/subscriptions/<guid>/resourceGroups/myVnet/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/default
```

`--network-plugin azure` 인수와 함께 [az aks create][az-aks-create] 명령을 사용하여 고급 네트워킹을 포함한 클러스터를 만듭니다. `--vnet-subnet-id` 값을 이전 단계에서 수집한 서브넷 ID로 업데이트합니다.

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --network-plugin azure \
    --vnet-subnet-id <subnet-id> \
    --docker-bridge-address 172.17.0.1/16 \
    --dns-service-ip 10.2.0.10 \
    --service-cidr 10.2.0.0/24 \
    --generate-ssh-keys
```

## <a name="configure-networking---portal"></a>네트워킹 구성 - 포털

Azure Portal의 다음 스크린샷은 AKS 클러스터를 만드는 동안 이러한 설정을 구성하는 방법의 예를 보여줍니다.

![Azure Portal의 고급 네트워킹 구성][portal-01-networking-advanced]

## <a name="frequently-asked-questions"></a>질문과 대답

다음과 같은 질문과 대답은 **Azure CNI** 네트워킹 구성에 적용됩니다.

* *내 클러스터 서브넷에 VM을 배포할 수 있나요?*

  아니요. Kubernetes 클러스터에서 사용되는 서브넷에 VM을 배포하는 것은 지원되지 않습니다. VM은 동일한 가상 네트워크의 다른 서브넷에 배포할 수 있습니다.

* *Pod별 네트워크 정책을 구성할 수 있나요?*

  예, Kubernetes 네트워크 정책에는 AKS에서 사용할 수 있습니다. 시작 하려면 참조 [AKS에 네트워크 정책을 사용 하 여 pod 간에 트래픽을 보호][network-policy]합니다.

* *구성 가능한 노드로 배포할 수 있는 Pod의 최대 수는 얼마나 되나요?*

  예. Azure CLI 또는 Resource Manager 템플릿을 사용하여 클러스터를 배포할 경우입니다. [노드당 최대 포드](#maximum-pods-per-node)를 참조하세요.

  기존 클러스터의 노드당 최대 Pod 수는 변경할 수 없습니다.

* *AKS 클러스터를 만드는 동안 만든 서브넷에 대해 추가 속성을 구성하려면 어떻게 해야 하나요? 예를 들어, 서비스 엔드포인트를 구성한다고 가정해보세요.*

  AKS 클러스터를 만드는 동안 만든 가상 네트워크와 서브넷에 대한 속성의 전체 목록은 Azure Portal의 표준 가상 네트워크 구성 페이지에서 구성할 수 있습니다.

* **Kubernetes 서비스 주소 범위**에 대해 *내 클러스터 가상 네트워크 내의 다른 서브넷을 사용할 수 있나요*?

  권장되지 않지만 이 구성은 가능합니다. 서비스 주소 범위는 Kubernetes가 클러스터의 내부 서비스에 할당하는 VIP(가상 IP)의 세트입니다. Azure 네트워킹은 Kubernetes 클러스터의 서비스 IP 범위를 볼 수 없습니다. 클러스터의 서비스 주소 범위에 대한 가시성 부족으로 인해 서비스 주소 범위와 겹치는 클러스터 가상 네트워크에서 나중에 새 서브넷을 만들 수 있습니다. 이러한 중복이 발생하는 경우 Kubernetes는 예기치 않은 동작이나 오류를 일으키는, 서브넷의 다른 리소스에서 이미 사용 중인 IP를 서비스에 할당할 수 있습니다. 클러스터의 가상 네트워크 외부에서 주소 범위를 사용하는 것을 확인하여 겹치는 위험을 방지할 수 있습니다.

## <a name="next-steps"></a>다음 단계

AKS의 네트워킹에 대한 자세한 내용은 다음 문서를 참조하세요.

- [AKS(Azure Kubernetes Service) 부하 분산 장치에 고정 IP 주소 사용](static-ip.md)
- [AKS(Azure 컨테이너 서비스)를 통해 내부 부하 분산 장치 사용](internal-lb.md)

- [외부 네트워크 연결을 사용하여 기본적인 수신 컨트롤러 만들기][aks-ingress-basic]
- [HTTP 애플리케이션 라우팅 추가 기능 사용][aks-http-app-routing]
- [내부 개인 네트워크 및 IP 주소를 사용하는 수신 컨트롤러 만들기][aks-ingress-internal]
- [동적 공용 IP를 사용하여 수신 컨트롤러를 만들고 TLS 인증서를 자동으로 생성하도록 Let’s Encrypt 구성][aks-ingress-tls]
- [동적 공용 IP를 사용하여 수신 컨트롤러를 만들고 TLS 인증서를 자동으로 생성하도록 Let’s Encrypt 구성][aks-ingress-static-tls]

### <a name="aks-engine"></a>AKS 엔진

[AKS 엔진(Azure Kubernetes Service 엔진)][aks-engine]은 Azure에 Kubernetes 클러스터를 배포하는 데 사용할 수 있는 Azure Resource Manager 템플릿을 생성하는 오픈 소스 프로젝트입니다.

AKS 엔진을 사용하여 만든 Kubernetes 클러스터는 [kubenet][kubenet] 및 [Azure CNI][cni-networking] 플러그 인을 둘 다 지원합니다. 따라서 AKS 엔진은 두 네트워킹 시나리오를 모두 지원합니다.

<!-- IMAGES -->
[advanced-networking-diagram-01]: ./media/networking-overview/advanced-networking-diagram-01.png
[portal-01-networking-advanced]: ./media/networking-overview/portal-01-networking-advanced.png

<!-- LINKS - External -->
[aks-engine]: https://github.com/Azure/aks-engine
[services]: https://kubernetes.io/docs/concepts/services-networking/service/
[portal]: https://portal.azure.com
[cni-networking]: https://github.com/Azure/azure-container-networking/blob/master/docs/cni.md
[kubenet]: https://kubernetes.io/docs/concepts/cluster-administration/network-plugins/#kubenet

<!-- LINKS - Internal -->
[az-aks-create]: /cli/azure/aks?view=azure-cli-latest#az-aks-create
[aks-ssh]: ssh.md
[ManagedClusterAgentPoolProfile]: /azure/templates/microsoft.containerservice/managedclusters#managedclusteragentpoolprofile-object
[aks-network-concepts]: concepts-network.md
[aks-ingress-basic]: ingress-basic.md
[aks-ingress-tls]: ingress-tls.md
[aks-ingress-static-tls]: ingress-static-ip.md
[aks-http-app-routing]: http-application-routing.md
[aks-ingress-internal]: ingress-internal-ip.md
[network-policy]: use-network-policies.md
[nodepool-upgrade]: use-multiple-node-pools.md#upgrade-a-node-pool
