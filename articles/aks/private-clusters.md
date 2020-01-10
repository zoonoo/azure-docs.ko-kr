---
title: 개인 Azure Kubernetes 서비스 클러스터
description: AKS (개인 Azure Kubernetes Service) 클러스터를 만드는 방법을 알아봅니다.
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 12/10/2019
ms.author: mlearned
ms.openlocfilehash: 6152becb8debd0700ddab6190284514c6d6cf69d
ms.sourcegitcommit: 8b37091efe8c575467e56ece4d3f805ea2707a64
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/09/2020
ms.locfileid: "75830057"
---
# <a name="public-preview---private-azure-kubernetes-service-cluster"></a>공개 미리 보기-개인 Azure Kubernetes 서비스 클러스터

개인 클러스터에서 제어 평면/API 서버는 [RFC1918](https://tools.ietf.org/html/rfc1918)에 정의 된 내부 IP 주소를 갖습니다.  개인 클러스터를 사용 하 여 API 서버와 노드 풀 간의 네트워크 트래픽이 개인 네트워크에만 남아 있는지 확인할 수 있습니다.

AKS로 관리 되는 Azure 구독에 있는 제어 평면/a p i 서버와 고객 구독에 있는 고객 클러스터/노드 풀 간의 통신은 API 서버 VNET의 [개인 링크 서비스][private-link-service] 를 통해 서로 통신할 수 있으며 고객 AKS 클러스터의 서브넷에 노출 되는 개인 끝점으로 통신할 수 있습니다.

> [!IMPORTANT]
> AKS 미리 보기 기능은 셀프 서비스 옵트인입니다. 미리 보기는 "있는 그대로" 및 "사용 가능한 상태로" 제공 되며 서비스 수준 계약 및 제한 된 보증에서 제외 됩니다. AKS 미리 보기는 최상의 노력에 대 한 고객 지원에서 부분적으로 다룹니다. 이러한 기능은 프로덕션 용도로는 사용할 수 없습니다. 추가 정보 다음 지원 문서를 참조 하세요.
>
> * [AKS 지원 정책](support-policies.md)
> * [Azure 지원 FAQ](faq.md)

## <a name="before-you-begin"></a>시작하기 전에

* Azure CLI 버전 2.0.77 이상 및 aks-preview 0.4.18 확장이 필요 합니다.

## <a name="current-supported-regions"></a>현재 지원 되는 지역
* 미국 서부
* 미국 서부 2
* 미국 동부 2
* 캐나다 중부
* 북유럽
* 서유럽
* 오스트레일리아 동부

## <a name="install-latest-aks-cli-preview-extension"></a>최신 AKS CLI 미리 보기 확장 설치

개인 클러스터를 사용 하려면 *aks-preview* CLI extension version 0.4.18 이상이 필요 합니다. [Az extension add][az-extension-add] 명령을 사용 하 여 *aks-preview* Azure CLI 확장을 설치한 다음 [az extension update][az-extension-update] 명령을 사용 하 여 사용 가능한 업데이트를 확인 합니다.

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```
> [!CAUTION]
> 구독에 기능을 등록 하면 현재 해당 기능을 등록 취소할 수 없습니다. 일부 미리 보기 기능을 사용 하도록 설정한 후에는 구독에서 만든 모든 AKS 클러스터에 대 한 기본값을 사용할 수 있습니다. 프로덕션 구독에서 미리 보기 기능을 사용 하도록 설정 하지 마세요. 별도의 구독을 사용 하 여 미리 보기 기능을 테스트 하 고 피드백을 수집 합니다.

```azurecli-interactive
az feature register --name AKSPrivateLinkPreview --namespace Microsoft.ContainerService
```

상태를 *등록 된*것으로 표시 하는 데 몇 분 정도 걸릴 수 있습니다. [Az feature list][az-feature-list] 명령을 사용 하 여 등록 상태를 확인할 수 있습니다.

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/AKSPrivateLinkPreview')].{Name:name,State:properties.state}"
```

상태가 등록 되 면 [az provider register][az-provider-register] 명령을 사용 하 여 *ContainerService* 리소스 공급자의 등록을 새로 고칩니다.

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
az provider register --namespace Microsoft.Network
```
## <a name="create-a-private-aks-cluster"></a>개인 AKS 클러스터 만들기

#### <a name="default-basic-networking"></a>기본 네트워킹 

```azurecli-interactive
az aks create -n <private-cluster-name> -g <private-cluster-resource-group> --load-balancer-sku standard --enable-private-cluster  
```
여기서--enable-private-cluster는 개인 클러스터에 대 한 필수 플래그입니다. 

#### <a name="advanced-networking"></a>고급 네트워킹  

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
여기서--enable-private-cluster는 개인 클러스터에 대 한 필수 플래그입니다. 

## <a name="steps-to-connect-to-the-private-cluster"></a>개인 클러스터에 연결 하는 단계
API 서버 끝점에 공용 IP 주소가 없습니다. 따라서 사용자는 가상 네트워크에 Azure 가상 머신을 만들고 API 서버에 연결 해야 합니다. 의 단계

* 클러스터에 연결 하기 위한 자격 증명 가져오기

   ```azurecli-interactive
   az aks get-credentials --name MyManagedCluster --resource-group MyResourceGroup
   ```
* AKS 클러스터와 동일한 VNET에서 VM 만들기 또는 다른 VNET에서 VM 만들기 및 AKS 클러스터 VNET을 사용 하 여이 VNET 피어 링
* 다른 VNET에서 VM을 만드는 경우이 VNET과 사설 DNS 영역 간의 링크를 설정 해야 합니다.
    * 포털에서 MC_ * 리소스 그룹으로 이동 합니다. 
    * 사설 DNS 영역을 클릭 합니다. 
    * 왼쪽 창에서 Virtual network 링크를 선택 합니다.
    * VM의 VNET을 사설 DNS 영역에 추가 하는 새 링크를 만듭니다 *(DNS 영역 링크를 사용 가능 하 게 하는 데 몇 분이 소요 될 수 있음)* .
    * 포털에서 MC_ * 리소스 그룹으로 돌아가기
    * 오른쪽 창에서 가상 네트워크를 선택 합니다. 가상 네트워크 이름은 aks-* 형식으로 됩니다.
    * 왼쪽 창에서 피어 링을 선택 합니다.
    * 추가를 클릭 하 고 VM의 가상 네트워크를 추가 하 고 피어 링을 만듭니다.
    * VM이 있는 Vnet으로 이동한 다음 피어 링을 클릭 하 고 AKS 가상 네트워크를 선택 하 고 피어 링을 만듭니다. AKS 가상 네트워크의 주소 범위와 VM의 가상 네트워크가 충돌 하는 경우 피어 링이 실패 합니다. 가상 네트워크 피어 링에 대 한 자세한 내용은이 [문서][virtual-network-peering] 를 참조 하세요.
* VM에 대 한 SSH
* Kubectl 도구를 설치 하 고 Kubectl 명령을 실행 합니다.

## <a name="dependencies"></a>종속성  
* Standard LB Only-기본 부하 분산 장치에 대 한 지원 없음  

## <a name="limitations"></a>제한 사항 
* 동일한 [Azure 개인 링크 서비스 제한이][private-link-service] 개인 클러스터에 적용 되 고, Azure 개인 끝점 및 Virtual Network 서비스 끝점이 현재 동일한 VNET에서 지원 되지 않습니다.
* 개인 Azure VNET에서 개인 ACI 인스턴스를 스핀 하기 위해 개인 클러스터의 가상 노드가 지원 되지 않음
* 개인 클러스터와 함께 기본 제공 되는 Azure DevOps 통합을 지원 하지 않습니다.
* 고객이 개인 AKS 작업을 위해 ACR을 사용 하도록 설정 해야 하는 경우 ACR의 VNET은 에이전트 클러스터 VNET과 피어 링 해야 합니다.
* 현재 Azure Dev Spaces 지원 안 함
* 기존 AKS 클러스터를 개인 클러스터로 변환 하는 기능이 지원 되지 않음  
* 고객 서브넷에서 개인 끝점을 삭제 하거나 수정 하면 클러스터의 작동이 중지 됩니다. 
* 컨테이너 라이브 데이터에 대 한 Azure Monitor 현재 지원 되지 않습니다.
* 사용자 고유의 DNS 가져오기는 현재 지원 되지 않습니다.


<!-- LINKS - internal -->
[az-provider-register]: /cli/azure/provider?view=azure-cli-latest#az-provider-register
[az-feature-list]: /cli/azure/feature?view=azure-cli-latest#az-feature-list
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[private-link-service]: https://docs.microsoft.com/azure/private-link/private-link-service-overview
[virtual-network-peering]: ../virtual-network/virtual-network-peering-overview.md

