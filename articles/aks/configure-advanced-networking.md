---
title: AKS(Azure Kubernetes Service)에서 고급 네트워킹 구성
description: AKS 클러스터를 기존 가상 네트워크와 서브넷에 배포하는 것을 포함하여 AKS(Azure Kubernetes Service)에서 고급 네트워크를 구성하는 방법을 알아봅니다.
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: article
ms.date: 10/11/2018
ms.author: iainfou
ms.openlocfilehash: 87c3ab9624116e9c1c61041531fdf5d3b26117e1
ms.sourcegitcommit: 3a7c1688d1f64ff7f1e68ec4bb799ba8a29a04a8
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/17/2018
ms.locfileid: "49380763"
---
# <a name="configure-advanced-networking-in-azure-kubernetes-service-aks"></a>AKS(Azure Kubernetes Service)에서 고급 네트워킹 구성

기본적으로 AKS 클러스터는 *기본* 네트워킹을 사용합니다. 이 네트워킹에서는 클러스터에 사용할 가상 네트워크와 서브넷을 만들고 구성합니다. IP 범위와 같은 이러한 네트워킹 옵션을 추가로 제어하려면 *고급* 네트워킹을 대신 사용할 수 있습니다. 고급 네트워킹을 사용하면 기존 가상 네트워크와 서브넷에 AKS 클러스터를 만들 수 있습니다. 이 기존 가상 네트워크는 종종 Azure ExpressRoute 또는 사이트 간 VPN을 사용하여 온-프레미스 네트워크에 연결합니다.

이 문서에서는 고급 네트워킹을 사용하여 AKS 클러스터에서 가상 네트워크를 만들고 사용하는 방법에 대해 설명합니다. 네트워킹에 대한 일반적인 내용은 [Kubernetes 및 AKS에 대한 네트워크 개념][aks-network-concepts]을 참조하세요.

## <a name="prerequisites"></a>필수 조건

* AKS 클러스터에 대한 가상 네트워크는 아웃바운드 인터넷 연결을 허용해야 합니다.
* 동일한 서브넷에 둘 이상의 AKS 클러스터를 만들지 마십시오.
* AKS 클러스터는 Kubernetes 서비스 주소 범위에 `169.254.0.0/16`, `172.30.0.0/16` 또는 `172.31.0.0/16`을 사용하지 못할 수도 있습니다.
* AKS 클러스터에서 사용되는 서비스 주체에는 가상 네트워크 내의 서브넷에 대해 [네트워크 참가자](../role-based-access-control/built-in-roles.md#network-contributor) 이상의 권한이 있어야 합니다. 기본 제공 네트워크 참가자 역할을 사용하는 대신 [사용자 지정 역할](../role-based-access-control/custom-roles.md)을 정의하려는 경우 다음 권한이 필요합니다.
  * `Microsoft.Network/virtualNetworks/subnets/join/action`
  * `Microsoft.Network/virtualNetworks/subnets/read`

## <a name="plan-ip-addressing-for-your-cluster"></a>클러스터에 대한 IP 주소 지정 계획

고급 네트워킹으로 구성한 클러스터에는 추가 계획이 필요합니다. 가상 네트워크 및 해당 서브넷의 크기는 실행하려는 Pod의 수와 클러스터에 대한 노드 수에 부합되어야 합니다.

Pod 및 클러스터 노드의 IP 주소는 가상 네트워크 내의 지정된 서브넷에서 할당됩니다. 각 노드는 노드의 IP인 기본 IP와 노드에 예약된 Pod에 할당되어 있는 Azure CNI에서 미리 구성된 30개의 추가 IP 주소로 구성됩니다. 클러스터를 스케일 아웃할 때 각 노드는 서브넷의 IP 주소로 비슷하게 구성됩니다.

AKS 클러스터에 대한 IP 주소 계획은 노드 및 Pod에 대한 하나 이상의 서브넷에서 가상 네트워크 및 Kubernetes 서비스 주소 범위로 구성됩니다.

| 주소 범위 / Azure 리소스 | 한도 및 크기 조정 |
| --------- | ------------- |
| 가상 네트워크 | Azure Virtual Network는 /8 이하일 수 있지만 구성된 IP 주소 수는 65,536개로 제한됩니다. |
| 서브넷 | 클러스터에서 프로비전될 수 있는 노드, 포드와 모든 Kubernetes 및 Azure 리소스를 수용할 만큼 커야 합니다. 예를 들어, 내부 Azure Load Balancer를 배포하는 경우, 해당 프런트 엔드 IP는 공용 IP가 아닌 클러스터 서브넷에서 할당됩니다. <p/>‘최소’ 서브넷 크기를 계산하려면: `(number of nodes) + (number of nodes * pods per node)` <p/>50 노드 클러스터의 예: `(50) + (50 * 30) = 1,550`(/21 이상) |
| Kubernetes 서비스 주소 범위 | 이 범위는 이 가상 네트워크 또는 이 가상 네트워크에 연결된 모든 네트워크 요소에서 사용하지 말아야 합니다. 서비스 주소 CIDR은 /12보다 작아야 합니다. |
| Kubernetes DNS 서비스 IP 주소 | 클러스터 서비스 검색에서 사용되는 Kubernetes 서비스 주소 범위 내의 IP 주소입니다(kube-dns). |
| Docker 브리지 주소 | 노드에서 Docker 브리지 IP 주소로 사용되는 IP 주소(CIDR 표기법)입니다. 172.17.0.1/16의 기본값 |

## <a name="maximum-pods-per-node"></a>노드당 최대 포드

AKS 클러스터의 노드당 기본 최대 포드 수는 기본 및 고급 네트워킹과 클러스터 배포 방법에 따라 다릅니다.

| 배포 방법 | Basic | 고급 | 배포 시 구성 가능 |
| -- | :--: | :--: | -- |
| Azure CLI | 110 | 30 | 예(최대: 110개) |
| Resource Manager 템플릿 | 110 | 30 | 예(최대: 110개) |
| 포털 | 110 | 30 | 아니요 |

### <a name="configure-maximum---new-clusters"></a>최댓값 구성 - 새 클러스터

*클러스터 배포 시간에만* 노드당 최대 Pod 수를 구성할 수 있습니다. Azure CLI 또는 Resource Manager 템플릿을 사용하여 배포하는 경우 노드당 최대 Pod 수를 최대 110으로 설정할 수 있습니다.

* **Azure CLI**: [az aks create][az-aks-create] 명령을 사용하여 클러스터를 배포할 때 `--max-pods` 인수를 지정합니다. 최댓값은 110입니다.
* **Resource Manager 템플릿**: Resource Manager 템플릿을 사용하여 클러스터를 배포할 때 [ManagedClusterAgentPoolProfile] 개체에 `maxPods` 속성을 지정합니다. 최댓값은 110입니다.
* **Azure Portal**: Azure Portal을 사용하여 클러스터를 배포하는 경우에는 노드당 최대 Pod 수를 변경할 수 없습니다. Azure Portal을 사용하여 배포하는 경우 고급 네트워킹 클러스터는 노드당 30개 Pod로 제한됩니다.

### <a name="configure-maximum---existing-clusters"></a>최댓값 구성 - 기존 클러스터

기존 AKS 클러스터의 노드당 최대 Pod 수는 변경할 수 없습니다. 클러스터를 처음 배포할 때만 이 값을 조정할 수 있습니다.

## <a name="deployment-parameters"></a>배포 매개 변수

AKS 클러스터를 만들 때 고급 네트워킹에서 다음 매개 변수를 구성할 수 있습니다.

**가상 네트워크**: Kubernetes 클러스터를 배포하려는 가상 네트워크입니다. 클러스터에 대해 새 가상 네트워크를 만들려는 경우 *새로 만들기*를 선택하고 *가상 네트워크 만들기* 섹션의 단계를 따릅니다. Azure Virtual Network의 제한 및 할당량에 대한 내용은 [Azure 구독 및 서비스 제한, 할당량 및 제약 조건](../azure-subscription-service-limits.md#azure-resource-manager-virtual-networking-limits)을 참조하세요.

**서브넷**: 클러스터를 배포하려는 가상 네트워크 내의 서브넷입니다. 클러스터에 대해 가상 네트워크에 새 서브넷을 만들려는 경우 *새로 만들기*를 선택하고 *서브넷 만들기* 섹션의 단계를 따릅니다.

**Kubernetes 서비스 주소 범위**: Kubernetes가 클러스터에서 [서비스][services]에 할당하는 가상 IP의 집합입니다. 다음 요구 사항을 충족하는 모든 개인 주소 범위를 사용할 수 있습니다.

* 클러스터의 가상 네트워크 IP 주소 범위에 속하지 않아야 합니다.
* 클러스터 가상 네트워크가 피어링된 다른 가상 네트워크와 겹치지 않아야 합니다.
* 온-프레미스 IP와 겹치지 않아야 합니다.
* `169.254.0.0/16`, `172.30.0.0/16` 또는 `172.31.0.0/16` 범위에 속하지 않아야 합니다.

기술적으로 클러스터와 동일한 가상 네트워크 내의 서비스 주소 범위를 지정할 수 있지만 이는 권장되지 않습니다. 겹치는 IP 범위를 사용한 경우 예측할 수 없는 동작이 발생할 수 있습니다. 자세한 내용은 이 문서의 [FAQ](#frequently-asked-questions) 섹션을 참조하세요. Kubernetes 서비스에 자세한 내용은 Kubernetes 설명서의 [서비스][services]를 참조하세요.

**Kubernetes DNS 서비스 IP 주소**: 클러스터의 DNS 서비스의 IP 주소입니다. 이 주소는 *Kubernetes 서비스 주소 범위*에 속해야 합니다.

**Docker 브리지 주소**: Docker 브리지에 할당할 IP 주소 및 네트워크 마스크입니다. 이 IP 주소는 클러스터의 가상 네트워크 IP 주소 범위에 속하지 않아야 합니다.

## <a name="configure-networking---cli"></a>네트워킹 구성 - CLI

Azure CLI를 사용하여 AKS 클러스터를 만들면 고급 네트워킹을 구성할 수도 있습니다. 다음 명령을 사용하여 고급 네트워킹 기능을 사용한 새로운 AKS 클러스터를 만듭니다.

먼저 기존 서브넷의 서브넷 리소스 ID를 조인할 AKS 클러스터로 가져옵니다.

```console
$ az network vnet subnet list --resource-group myVnet --vnet-name myVnet --query [].id --output tsv

/subscriptions/d5b9d4b7-6fc1-46c5-bafe-38effaed19b2/resourceGroups/myVnet/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/default
```

`--network-plugin azure` 인수와 함께 [az aks create][az-aks-create] 명령을 사용하여 고급 네트워킹을 포함한 클러스터를 만듭니다. `--vnet-subnet-id` 값을 이전 단계에서 수집한 서브넷 ID로 업데이트합니다.

```azurecli
az aks create --resource-group myAKSCluster --name myAKSCluster --network-plugin azure --vnet-subnet-id <subnet-id> --docker-bridge-address 172.17.0.1/16 --dns-service-ip 10.2.0.10 --service-cidr 10.2.0.0/24
```

## <a name="configure-networking---portal"></a>네트워킹 구성 - 포털

Azure Portal의 다음 스크린샷은 AKS 클러스터를 만드는 동안 이러한 설정을 구성하는 방법의 예를 보여줍니다.

![Azure Portal의 고급 네트워킹 구성][portal-01-networking-advanced]

## <a name="frequently-asked-questions"></a>질문과 대답

다음과 같은 질문과 대답이 **고급** 네트워킹 구성에 적용됩니다.

* *내 클러스터 서브넷에 VM을 배포할 수 있나요?*

  아니요. Kubernetes 클러스터에서 사용되는 서브넷에 VM을 배포하는 것은 지원되지 않습니다. VM은 동일한 가상 네트워크의 다른 서브넷에 배포할 수 있습니다.

* *Pod별 네트워크 정책을 구성할 수 있나요?*

  아니요. Pod별 네트워크 정책은 현재 지원되지 않습니다.

* *구성 가능한 노드로 배포할 수 있는 Pod의 최대 수는 얼마나 되나요?*

  예. Azure CLI 또는 Resource Manager 템플릿을 사용하여 클러스터를 배포할 경우입니다. [노드당 최대 포드](#maximum-pods-per-node)를 참조하세요.

  기존 클러스터의 노드당 최대 Pod 수는 변경할 수 없습니다.

* *AKS 클러스터를 만드는 동안 만든 서브넷에 대해 추가 속성을 구성하려면 어떻게 해야 하나요? 예를 들어, 서비스 엔드포인트를 구성한다고 가정해보세요.*

  AKS 클러스터를 만드는 동안 만든 가상 네트워크와 서브넷에 대한 속성의 전체 목록은 Azure Portal의 표준 가상 네트워크 구성 페이지에서 구성할 수 있습니다.

* **Kubernetes 서비스 주소 범위**에 대해 *내 클러스터 가상 네트워크 내의 다른 서브넷을 사용할 수 있나요*?

  권장되지 않지만 이 구성은 가능합니다. 서비스 주소 범위는 Kubernetes가 클러스터에서 서비스에 할당하는 VIP(가상 IP)의 집합입니다. Azure 네트워킹은 Kubernetes 클러스터의 서비스 IP 범위를 볼 수 없습니다. 클러스터의 서비스 주소 범위에 대한 가시성 부족으로 인해 서비스 주소 범위와 겹치는 클러스터 가상 네트워크에서 나중에 새 서브넷을 만들 수 있습니다. 이러한 중복이 발생하는 경우 Kubernetes는 예기치 않은 동작이나 오류를 일으키는, 서브넷의 다른 리소스에서 이미 사용 중인 IP를 서비스에 할당할 수 있습니다. 클러스터의 가상 네트워크 외부에서 주소 범위를 사용하는 것을 확인하여 겹치는 위험을 방지할 수 있습니다.

## <a name="next-steps"></a>다음 단계

### <a name="networking-in-aks"></a>AKS의 네트워킹

AKS의 네트워킹에 대한 자세한 내용은 다음 문서를 참조하세요.

- [AKS(Azure Kubernetes Service) 부하 분산 장치에 고정 IP 주소 사용](static-ip.md)
- [AKS(Azure 컨테이너 서비스)를 통해 내부 부하 분산 장치 사용](internal-lb.md)

- [외부 네트워크 연결을 사용하여 기본적인 수신 컨트롤러 만들기][aks-ingress-basic]
- [HTTP 응용 프로그램 라우팅 추가 기능 사용][aks-http-app-routing]
- [내부 개인 네트워크 및 IP 주소를 사용하는 수신 컨트롤러 만들기][aks-ingress-internal]
- [동적 공용 IP를 사용하여 수신 컨트롤러를 만들고 TLS 인증서를 자동으로 생성하도록 Let’s Encrypt 구성][aks-ingress-tls]
- [고정 공용 IP를 사용하여 수신 컨트롤러를 만들고, TLS 인증서를 자동으로 생성하도록 Let’s Encrypt 구성][aks-ingress-static-tls]

### <a name="acs-engine"></a>ACS 엔진

[ACS 엔진(Azure Container Service 엔진)][acs-engine]은 Azure에서 Docker 지원 클러스터를 배포하는 데 사용할 수 있는 Azure Resource Manager 템플릿을 생성하는 오픈 소스 프로젝트입니다. Kubernetes, DC/OS, Swarm 모드 및 Swarm 오케스트레이터는 ACS 엔진을 사용하여 배포할 수 있습니다.

ACS 엔진을 사용하여 만든 Kubernetes 클러스터는 [kubenet][kubenet] 및 [Azure CNI][cni-networking] 플러그 인을 둘 다 지원합니다. 따라서 ACS 엔진은 기본 및 고급 네트워킹 시나리오를 둘 다 지원합니다.

<!-- IMAGES -->
[advanced-networking-diagram-01]: ./media/networking-overview/advanced-networking-diagram-01.png
[portal-01-networking-advanced]: ./media/networking-overview/portal-01-networking-advanced.png

<!-- LINKS - External -->
[acs-engine]: https://github.com/Azure/acs-engine
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
