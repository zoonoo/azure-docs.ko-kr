---
title: 개인 Azure Kubernetes Service 클러스터 만들기
description: AKS (개인 Azure Kubernetes Service) 클러스터를 만드는 방법을 알아봅니다.
services: container-service
ms.topic: article
ms.date: 2/21/2020
ms.openlocfilehash: b8b4f8062d9f60648e22ab4eb0be78eb47159834
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/13/2020
ms.locfileid: "79205177"
---
# <a name="create-a-private-azure-kubernetes-service-cluster"></a>개인 Azure Kubernetes Service 클러스터 만들기

개인 클러스터에서 제어 평면이 나 API 서버에는 [개인 인터넷 문서에 대 한 RFC1918 할당](https://tools.ietf.org/html/rfc1918) 에 정의 된 내부 IP 주소가 있습니다. 개인 클러스터를 사용 하 여 API 서버와 노드 풀 간의 네트워크 트래픽이 개인 네트워크에만 남아 있는지 확인할 수 있습니다.

제어 평면 또는 API 서버는 Azure Kubernetes 서비스 (AKS)에서 관리 되는 Azure 구독에 있습니다. 고객의 클러스터 또는 노드 풀은 고객의 구독에 있습니다. 서버와 클러스터 또는 노드 풀은 API 서버 가상 네트워크의 [Azure 개인 링크 서비스][private-link-service] 및 고객의 AKS 클러스터의 서브넷에 노출 되는 개인 끝점을 통해 서로 통신할 수 있습니다.

## <a name="prerequisites"></a>사전 요구 사항

* Azure CLI 버전 2.2.0 이상

## <a name="create-a-private-aks-cluster"></a>개인 AKS 클러스터 만들기

### <a name="create-a-resource-group"></a>리소스 그룹 만들기

리소스 그룹을 만들거나 AKS 클러스터에 대 한 기존 리소스 그룹을 사용 합니다.

```azurecli-interactive
az group create -l westus -n MyResourceGroup
```

### <a name="default-basic-networking"></a>기본 네트워킹 

```azurecli-interactive
az aks create -n <private-cluster-name> -g <private-cluster-resource-group> --load-balancer-sku standard --enable-private-cluster  
```
여기서 *--enable-private-cluster* 는 개인 클러스터에 대 한 필수 플래그입니다. 

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
여기서 *--enable-private-cluster* 는 개인 클러스터에 대 한 필수 플래그입니다. 

> [!NOTE]
> Docker 브리지 주소 CIDR (172.17.0.1/16)이 서브넷 CIDR과 충돌 하는 경우 Docker 브리지 주소를 적절 하 게 변경 합니다.

## <a name="options-for-connecting-to-the-private-cluster"></a>개인 클러스터에 연결 하기 위한 옵션

API 서버 끝점에 공용 IP 주소가 없습니다. API 서버를 관리 하려면 AKS 클러스터의 Azure Virtual Network (VNet)에 대 한 액세스 권한이 있는 VM을 사용 해야 합니다. 개인 클러스터에 대 한 네트워크 연결을 설정 하기 위한 몇 가지 옵션이 있습니다.

* AKS 클러스터와 동일한 Azure Virtual Network (VNet)에서 VM을 만듭니다.
* 별도 네트워크에서 VM을 사용 하 고 [가상 네트워크 피어 링][virtual-network-peering]을 설정 합니다.  이 옵션에 대 한 자세한 내용은 아래 섹션을 참조 하세요.
* [Express 경로 또는 VPN][express-route-or-VPN] 연결을 사용 합니다.

AKS 클러스터와 동일한 VNET에 VM을 만드는 것이 가장 쉬운 옵션입니다.  Express 경로 및 Vpn은 비용을 추가 하 고 추가 네트워킹 복잡성이 필요 합니다.  가상 네트워크 피어 링을 사용 하려면 겹치는 범위가 없도록 네트워크 CIDR 범위를 계획 해야 합니다.

## <a name="virtual-network-peering"></a>가상 네트워크 피어링

앞서 언급 했 듯이 VNet 피어 링은 개인 클러스터에 액세스 하는 한 가지 방법입니다. VNet 피어 링을 사용 하려면 가상 네트워크와 개인 DNS 영역 간의 링크를 설정 해야 합니다.
    
1. Azure Portal에서 MC_ * 리소스 그룹으로 이동 합니다.  
2. 개인 DNS 영역을 선택 합니다.   
3. 왼쪽 창에서 **Virtual network** 링크를 선택 합니다.  
4. VM의 가상 네트워크를 개인 DNS 영역에 추가 하는 새 링크를 만듭니다. DNS 영역 링크를 사용할 수 있게 되는 데 몇 분 정도 걸립니다.  
5. Azure Portal에서 MC_ * 리소스 그룹으로 돌아갑니다.  
6. 오른쪽 창에서 가상 네트워크를 선택 합니다. 가상 네트워크 이름은 *aks-\** 형식으로 되어 있습니다.  
7. 왼쪽 창에서 **피어 링**을 선택 합니다.  
8. **추가**를 선택 하 고 VM의 가상 네트워크를 추가한 다음 피어 링을 만듭니다.  
9. VM이 있는 가상 네트워크로 이동 하 **여 피어 링을 선택 하**고 AKS 가상 네트워크를 선택한 다음 피어 링을 만듭니다. AKS 가상 네트워크의 주소 범위와 VM의 가상 네트워크가 충돌 하는 경우 피어 링이 실패 합니다. 자세한 내용은 [가상 네트워크 피어 링][virtual-network-peering]을 참조 하세요.

## <a name="dependencies"></a>종속성  

* 개인 링크 서비스는 표준 Azure Load Balancer 에서만 지원 됩니다. 기본 Azure Load Balancer 지원 되지 않습니다.  
* 사용자 지정 DNS 서버를 사용 하려면 DNS를 사용 하 여이 IP 168.63.129.16 전달 되는 AD 서버를 배포 합니다.

## <a name="limitations"></a>제한 사항 
* IP 권한이 부여 된 범위는 개인 api 서버 끝점에 적용할 수 없으며 공용 API 서버에만 적용 됩니다.
* 현재 특정 지역에 대해 지원 되는 가용성 영역이 문서의 시작 부분을 참조 하세요. 
* [Azure 개인 링크 서비스 제한은][private-link-service] 현재 동일한 가상 네트워크에서 지원 되지 않는 개인 클러스터, Azure 개인 끝점 및 가상 네트워크 서비스 끝점에 적용 됩니다.
* 개인 Azure 가상 네트워크에서 개인 Azure Container Instances (ACI)를 spin 개인 클러스터의 가상 노드가 지원 되지 않음
* 개인 클러스터와 함께 기본 제공 되는 Azure DevOps 통합을 지원 하지 않습니다.
* Azure Container Registry를 개인 AKS 사용 하도록 설정 해야 하는 고객의 경우 Container Registry 가상 네트워크는 에이전트 클러스터 가상 네트워크와 피어 링 되어야 합니다.
* 현재 Azure Dev Spaces 지원 안 함
* 기존 AKS 클러스터를 개인 클러스터로 변환 하는 기능이 지원 되지 않음
* 고객 서브넷에서 개인 끝점을 삭제 하거나 수정 하면 클러스터의 작동이 중지 됩니다. 
* 컨테이너 라이브 데이터에 대 한 Azure Monitor 현재 지원 되지 않습니다.


<!-- LINKS - internal -->
[az-provider-register]: /cli/azure/provider?view=azure-cli-latest#az-provider-register
[az-feature-list]: /cli/azure/feature?view=azure-cli-latest#az-feature-list
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[private-link-service]: /private-link/private-link-service-overview
[virtual-network-peering]: ../virtual-network/virtual-network-peering-overview.md
[azure-bastion]: ../bastion/bastion-create-host-portal.md
[express-route-or-vpn]: ../expressroute/expressroute-about-virtual-network-gateways.md

