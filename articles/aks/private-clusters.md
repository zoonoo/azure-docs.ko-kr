---
title: 프라이빗 Azure Kubernetes 서비스 클러스터 만들기
description: 개인 Azure Kubernetes 서비스(AKS) 클러스터를 만드는 방법에 대해 알아봅니다.
services: container-service
ms.topic: article
ms.date: 2/21/2020
ms.openlocfilehash: cdefcfe460a97f647afa05947e92fae0c4d07001
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79499295"
---
# <a name="create-a-private-azure-kubernetes-service-cluster"></a>프라이빗 Azure Kubernetes 서비스 클러스터 만들기

개인 클러스터에서 제어 평면 또는 API 서버에는 [RFC1918 - 개인 인터넷](https://tools.ietf.org/html/rfc1918) 문서의 주소 할당에 정의된 내부 IP 주소가 있습니다. 개인 클러스터를 사용하면 API 서버와 노드 풀 간의 네트워크 트래픽이 개인 네트워크에만 유지되도록 할 수 있습니다.

제어 평면 또는 API 서버는 AKS(Azure Kubernetes) 관리 Azure 구독에 있습니다. 고객의 클러스터 또는 노드 풀이 고객의 구독에 있습니다. 서버와 클러스터 또는 노드 풀은 API 서버 가상 네트워크의 [Azure 개인 링크 서비스와][private-link-service] 고객의 AKS 클러스터의 서브넷에 노출되는 개인 끝점을 통해 서로 통신할 수 있습니다.

## <a name="prerequisites"></a>사전 요구 사항

* Azure CLI 버전 2.2.0 이상

## <a name="create-a-private-aks-cluster"></a>개인 AKS 클러스터 만들기

### <a name="create-a-resource-group"></a>리소스 그룹 만들기

리소스 그룹을 만들거나 AKS 클러스터에 대한 기존 리소스 그룹을 사용합니다.

```azurecli-interactive
az group create -l westus -n MyResourceGroup
```

### <a name="default-basic-networking"></a>기본 기본 네트워킹 

```azurecli-interactive
az aks create -n <private-cluster-name> -g <private-cluster-resource-group> --load-balancer-sku standard --enable-private-cluster  
```
*--enable-private-cluster는* 개인 클러스터에 대한 필수 플래그입니다. 

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
*--enable-private-cluster는* 개인 클러스터에 대한 필수 플래그입니다. 

> [!NOTE]
> Docker 브리지 주소 CIDR(172.17.0.1/16)이 서브넷 CIDR과 충돌하는 경우 Docker 브리지 주소를 적절하게 변경합니다.

## <a name="options-for-connecting-to-the-private-cluster"></a>개인 클러스터에 연결하기 위한 옵션

API 서버 끝점에 공용 IP 주소가 없습니다. API 서버를 관리하려면 AKS 클러스터의 Azure 가상 네트워크(VNet)에 액세스할 수 있는 VM을 사용해야 합니다. 개인 클러스터에 대한 네트워크 연결을 설정하는 데는 몇 가지 옵션이 있습니다.

* AKS 클러스터와 동일한 Azure 가상 네트워크(VNet)에서 VM을 만듭니다.
* 별도의 네트워크에서 VM을 사용하고 [가상 네트워크 피어링을][virtual-network-peering]설정합니다.  이 옵션에 대한 자세한 내용은 아래 섹션을 참조하십시오.
* 익스프레스 [경로 또는 VPN][express-route-or-VPN] 연결을 사용합니다.

AKS 클러스터와 동일한 VNET에서 VM을 만드는 것이 가장 쉬운 옵션입니다.  Express Route 및 VPN은 비용을 추가하고 네트워킹의 복잡성을 더해야 합니다.  가상 네트워크 피어링은 겹치는 범위가 없는지 확인하기 위해 네트워크 CIDR 범위를 계획해야 합니다.

## <a name="virtual-network-peering"></a>가상 네트워크 피어링

앞서 언급했듯이 VNet 피어링은 개인 클러스터에 액세스하는 한 가지 방법입니다. VNet 피어링을 사용하려면 가상 네트워크와 개인 DNS 영역 간의 링크를 설정해야 합니다.
    
1. Azure 포털의 MC_* 리소스 그룹으로 이동합니다.  
2. 개인 DNS 영역을 선택합니다.   
3. 왼쪽 창에서 **가상 네트워크** 링크를 선택합니다.  
4. 새 링크를 만들어 VM의 가상 네트워크를 개인 DNS 영역에 추가합니다. DNS 영역 링크를 사용할 수 있게 되려면 몇 분 정도 걸립니다.  
5. Azure 포털의 MC_* 리소스 그룹으로 돌아갑니다.  
6. 오른쪽 창에서 가상 네트워크를 선택합니다. 가상 네트워크 이름은 *aks-vnet-\** 형식으로 표시됩니다.  
7. 왼쪽 창에서 **피어링을 선택합니다.**  
8. **추가를**선택하고 VM의 가상 네트워크를 추가한 다음 피어링을 만듭니다.  
9. VM이 있는 가상 네트워크로 이동하여 **피어링을**선택하고 AKS 가상 네트워크를 선택한 다음 피어링을 만듭니다. 주소가 AKS 가상 네트워크와 VM의 가상 네트워크 간섭범위인 경우 피어링이 실패합니다. 자세한 내용은 [가상 네트워크 피어링][virtual-network-peering]을 참조하십시오.

## <a name="dependencies"></a>종속성  

* 개인 링크 서비스는 표준 Azure 로드 밸러버에서만 지원됩니다. 기본 Azure 로드 밸러서가 지원되지 않습니다.  
* 사용자 지정 DNS 서버를 사용하려면 Azure DNS IP 168.63.129.16을 사용자 지정 DNS 서버의 업스트림 DNS 서버로 추가합니다.

## <a name="limitations"></a>제한 사항 
* IP 인증 범위는 개인 API 서버 엔드포인트에 적용할 수 없으며 공용 API 서버에만 적용됩니다.
* 가용성 영역은 현재 특정 지역에 대해 지원됩니다. 
* [Azure 개인 링크 서비스 제한은][private-link-service] 현재 동일한 가상 네트워크에서 지원되지 않는 개인 클러스터, Azure 개인 끝점 및 가상 네트워크 서비스 끝점에 적용됩니다.
* 개인 Azure 가상 네트워크에서 개인 Azure 컨테이너 인스턴스(ACI)를 회전하는 개인 클러스터의 가상 노드에 대한 지원이 없습니다.
* 개인 클러스터를 사용할 수 있는 Azure DevOps 통합에 대한 지원이 없습니다.
* Azure 컨테이너 레지스트리가 개인 AKS로 작동하도록 설정해야 하는 고객의 경우 컨테이너 레지스트리 가상 네트워크는 에이전트 클러스터 가상 네트워크로 피어를 참조해야 합니다.
* Azure 개발자 공간에 대한 현재 지원 없음
* 기존 AKS 클러스터를 개인 클러스터로 변환하는 데 대한 지원이 없습니다.
* 고객 서브넷에서 개인 끝점을 삭제하거나 수정하면 클러스터의 작동이 중지됩니다. 
* 컨테이너에 대한 Azure 모니터 라이브 데이터는 현재 지원되지 않습니다.


<!-- LINKS - internal -->
[az-provider-register]: /cli/azure/provider?view=azure-cli-latest#az-provider-register
[az-feature-list]: /cli/azure/feature?view=azure-cli-latest#az-feature-list
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[private-link-service]: /azure/private-link/private-link-service-overview
[virtual-network-peering]: ../virtual-network/virtual-network-peering-overview.md
[azure-bastion]: ../bastion/bastion-create-host-portal.md
[express-route-or-vpn]: ../expressroute/expressroute-about-virtual-network-gateways.md

