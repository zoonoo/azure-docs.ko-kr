---
title: 프라이빗 Azure Kubernetes Service 클러스터 만들기
description: 프라이빗 AKS(Azure Kubernetes Service) 클러스터를 만드는 방법 알아보기
services: container-service
ms.topic: article
ms.date: 6/18/2020
ms.openlocfilehash: c788f2009bdc771bcdde20d1c3dbe9eafdbcffcb
ms.sourcegitcommit: dabd9eb9925308d3c2404c3957e5c921408089da
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/11/2020
ms.locfileid: "86244228"
---
# <a name="create-a-private-azure-kubernetes-service-cluster"></a>프라이빗 Azure Kubernetes Service 클러스터 만들기

프라이빗 클러스터에서 컨트롤 플레인 또는 API 서버에는 [RFC1918 - 프라이빗 인터넷용 주소 할당](https://tools.ietf.org/html/rfc1918) 문서에 정의된 내부 IP 주소가 있습니다. 프라이빗 클러스터를 사용하면 API 서버와 노드 풀 사이의 네트워크 트래픽이 프라이빗 네트워크에만 유지되는지 확인할 수 있습니다.

컨트롤 플레인 또는 API 서버는 AKS(Azure Kubernetes Service)에서 관리되는 Azure 구독에 있습니다. 고객의 클러스터 또는 노드 풀은 고객의 구독에 있습니다. 서버와 클러스터 또는 노드 풀은 API 서버 가상 네트워크의 [Azure Private Link 서비스][private-link-service]를 통해 서로 통신할 수 있으며, 고객의 AKS 클러스터의 서브넷에 노출되는 프라이빗 엔드포인트입니다.

## <a name="prerequisites"></a>사전 요구 사항

* Azure CLI 버전 2.2.0 이상

## <a name="create-a-private-aks-cluster"></a>프라이빗 AKS 클러스터 만들기

### <a name="create-a-resource-group"></a>리소스 그룹 만들기

리소스 그룹을 만들거나 AKS 클러스터를 위한 기존 리소스 그룹을 사용합니다.

```azurecli-interactive
az group create -l westus -n MyResourceGroup
```

### <a name="default-basic-networking"></a>기본값으로 설정된 기본 네트워킹 

```azurecli-interactive
az aks create -n <private-cluster-name> -g <private-cluster-resource-group> --load-balancer-sku standard --enable-private-cluster  
```
여기서 *--enable-private-cluster*는 프라이빗 클러스터에 대한 필수 플래그에 속합니다. 

### <a name="advanced-networking"></a>고급 네트워킹  

```azurecli-interactive
az aks create \
    --resource-group <private-cluster-resource-group> \
    --name <private-cluster-name> \
    --load-balancer-sku standard \
    --enable-private-cluster \
    --network-plugin azure \
    --vnet-subnet-id <subnet-id> \
    --docker-bridge-address 172.17.0.1/16 \
    --dns-service-ip 10.2.0.10 \
    --service-cidr 10.2.0.0/24 
```
여기서 *--enable-private-cluster*는 프라이빗 클러스터에 대한 필수 플래그에 속합니다. 

> [!NOTE]
> Docker 브리지 주소 CIDR(172.17.0.1/16)이 서브넷 CIDR과 충돌하는 경우, Docker 브리지 주소를 적절하게 변경합니다.

## <a name="options-for-connecting-to-the-private-cluster"></a>프라이빗 클러스터에 연결하기 위한 옵션

API 서버 엔드포인트에 공용 IP 주소가 없습니다. API 서버를 관리하려면 AKS 클러스터의 Azure Virtual Network(VNet)에 대한 액세스 권한이 있는 VM을 사용해야 합니다. 프라이빗 클러스터에 대한 네트워크 연결을 설정할 경우, 몇 가지 옵션이 있습니다.

* AKS 클러스터와 동일한 Azure Virtual Network(VNet)에서 VM을 만듭니다.
* 별도의 네트워크에서 VM을 사용하고 [가상 네트워크 피어링][virtual-network-peering]을 설정합니다.  이 옵션에 관한 자세한 내용은 아래의 해당 섹션을 참조하세요.
* [Express Route 또는 VPN][express-route-or-VPN] 연결을 사용합니다.

AKS 클러스터와 동일한 VNET에 VM을 만드는 것이 가장 쉬운 옵션입니다.  Express Route 및 VPN은 비용을 증가시키며 추가적인 네트워킹 복잡성을 요구합니다.  가상 네트워크 피어링을 사용하려면 중첩되는 범위가 없도록 네트워크 CIDR 범위를 계획해야 합니다.

## <a name="virtual-network-peering"></a>가상 네트워크 피어링

앞서 언급했듯이 VNet 피어링은 개인 클러스터에 액세스하는 한 가지 방법에 속합니다. VNet 피어링을 사용하려면 가상 네트워크와 프라이빗 DNS 영역 간에 링크를 설정해야 합니다.
    
1. Azure Portal에서 노드 리소스 그룹으로 이동 합니다.  
2. 프라이빗 DNS 영역을 선택합니다.   
3. 왼쪽 창에서 **가상 네트워크** 링크를 선택합니다.  
4. VM의 가상 네트워크를 프라이빗 DNS 영역에 추가하는 새 링크를 만듭니다. DNS 영역 링크를 사용할 수 있을 때까지 몇 분 정도 걸립니다.  
5. Azure Portal에서 클러스터의 VNet을 포함 하는 리소스 그룹으로 이동 합니다.  
6. 오른쪽 창에서 가상 네트워크를 선택합니다. 가상 네트워크 이름은 *aks-vnet-\** 형식으로 되어 있습니다.  
7. 왼쪽 창에서 **피어링**을 선택합니다.  
8. **추가**를 선택하고 VM의 가상 네트워크를 추가한 다음, 피어링을 만듭니다.  
9. VM이 있는 가상 네트워크로 이동하여 **피어링**을 선택하고 AKS 가상 네트워크를 선택한 후 피어링을 만듭니다. AKS 가상 네트워크의 주소 범위와 VM의 가상 네트워크가 충돌하는 경우, 피어링이 실패합니다. 자세한 내용은 [가상 네트워크 피어링][virtual-network-peering]을 참조하세요.

## <a name="hub-and-spoke-with-custom-dns"></a>사용자 지정 DNS를 사용하는 허브 및 스포크

[허브 및 스포크 아키텍처](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke)는 일반적으로 Azure에서 네트워크를 배포하는 데 사용됩니다. 이러한 배포 중 상당수에서 스포크 VNet의 DNS 설정은 중앙 DNS 전달자를 참조하여 온-프레미스 및 Azure 기반 DNS 확인을 허용하도록 구성됩니다. 이러한 네트워킹 환경에 AKS 클러스터를 배포하는 경우, 고려해야 할 몇 가지 특별한 사항들이 있습니다.

![프라이빗 클러스터 허브 및 스포크](media/private-clusters/aks-private-hub-spoke.png)

1. 기본적으로 프라이빗 클러스터가 프로비전되면 프라이빗 엔드포인트 (1)과 프라이빗 DNS 영역(2)이 클러스터 관리되는 리소스 그룹에서 생성됩니다. 클러스터는 프라이빗 영역의 A 레코드를 사용하여 API 서버와 통신할 프라이빗 엔드포인트의 IP를 확인합니다.

2. 프라이빗 DNS 영역은 클러스터 노드가 (3)과 연결된 VNet에만 연결됩니다. 즉, 연결된 VNet의 호스트만 프라이빗 엔드포인트를 확인할 수 있습니다. VNet에 사용자 지정 DNS가 구성되어 있지 않은 시나리오(기본값)에서 이 엔드포인트는 연결로 인해 프라이빗 DNS 영역에 있는 레코드를 확인할 수 있는 DNS의 168.63.129.16에 위치한 호스트 지점으로서 문제 없이 작동합니다.

3. 클러스터를 포함하는 VNet에 사용자 지정 DNS 설정(4)이 있는 시나리오에서는 프라이빗 DNS 영역이 사용자 지정 DNS 확인자(5)를 포함하는 VNet에 연결되어 있지 않으면 클러스터 배포가 실패합니다. 이 링크는 클러스터를 프로 비전 하는 동안 또는 이벤트 기반 배포 메커니즘 (예: Azure Event Grid 및 Azure Functions)을 사용 하 여 영역 생성 검색 시 자동화를 통해 만들 때 수동으로 만들 수 있습니다.

## <a name="dependencies"></a>종속성  

* Private Link 서비스는 표준 Azure Load Balancer에서만 지원됩니다. 기본 Azure Load Balancer는 지원되지 않습니다.  
* 사용자 지정 DNS 서버를 사용하려면 Azure DNS IP 168.63.129.16을 사용자 지정 DNS 서버에 업스트림 DNS 서버로 추가합니다.

## <a name="limitations"></a>제한 사항 
* IP 권한이 부여된 범위는 개인 API 서버 엔드포인트에 적용할 수 없으며 공용 API 서버에만 적용됩니다.
* [가용성 영역][availability-zones] 은 현재 특정 지역에서 지원 됩니다. 
* [Azure Private Link 서비스 제한][private-link-service]은 프라이빗 클러스터에 적용됩니다.
* 프라이빗 클러스터를 사용하는 Azure DevOps Microsoft 호스팅 에이전트를 지원하지 않습니다. [자체 호스팅 에이전트][devops-agents]를 사용하는 것이 좋습니다. 
* Azure Container Registry를 프라이빗 AKS와 함께 사용해야 하는 고객의 경우, Container Registry 가상 네트워크는 에이전트 클러스터 가상 네트워크와 피어링되어야 합니다.
* 현재 Azure Dev Spaces를 지원하지 않습니다.
* 기존 AKS 클러스터를 프라이빗 클러스터로 변환하는 기능이 지원되지 않습니다.
* 고객 서브넷에서 프라이빗 엔드포인트를 삭제하거나 수정하면 클러스터의 작동이 중지됩니다. 
* 컨테이너용 Azure Monitor의 라이브 데이터는 현재 지원되지 않습니다.
* 작동 시간 SLA는 현재 지원되지 않습니다.


<!-- LINKS - internal -->
[az-provider-register]: /cli/azure/provider?view=azure-cli-latest#az-provider-register
[az-feature-list]: /cli/azure/feature?view=azure-cli-latest#az-feature-list
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[private-link-service]: ../private-link/private-link-service-overview.md#limitations
[virtual-network-peering]: ../virtual-network/virtual-network-peering-overview.md
[azure-bastion]: ../bastion/bastion-create-host-portal.md
[express-route-or-vpn]: ../expressroute/expressroute-about-virtual-network-gateways.md
[devops-agents]: /azure/devops/pipelines/agents/agents?view=azure-devops
[availability-zones]: availability-zones.md
