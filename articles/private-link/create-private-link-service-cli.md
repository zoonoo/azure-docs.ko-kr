---
title: Azure CLI를 사용하여 Azure 개인 링크 서비스 만들기
description: Azure CLI를 사용하여 Azure 개인 링크 서비스를 만드는 방법에 대해 알아봅니다.
services: private-link
author: malopMSFT
ms.service: private-link
ms.topic: article
ms.date: 09/16/2019
ms.author: allensu
ms.openlocfilehash: 6e6148d305af26f7933567ae58023d2ba73263eb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75350236"
---
# <a name="create-a-private-link-service-using-azure-cli"></a>Azure CLI를 사용하여 개인 링크 서비스 만들기
이 문서에서는 Azure CLI를 사용하여 Azure에서 개인 링크 서비스를 만드는 방법을 보여 주며 있습니다.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

대신 로컬로 Azure CLI를 설치하고 사용하기로 결정한 경우 이 빠른 시작을 위해 최신 Azure CLI 버전을 사용해야 합니다. 설치된 버전을 확인하려면 `az --version`을 실행합니다. 설치 또는 업그레이드 정보는 [Azure CLI 설치](/cli/azure/install-azure-cli)를 참조하세요.
## <a name="create-a-private-link-service"></a>Private Link 서비스 만들기
### <a name="create-a-resource-group"></a>리소스 그룹 만들기

가상 네트워크를 만들려면 먼저 가상 네트워크를 호스트할 리소스 그룹을 만들어야 합니다. [az group create](/cli/azure/group)를 사용하여 리소스 그룹을 만듭니다. 다음 예제에서는 *myResourceGroup*이라는 리소스 그룹을 *westcentralus* 위치에 만듭니다.

```azurecli-interactive
az group create --name myResourceGroup --location westcentralus
```
### <a name="create-a-virtual-network"></a>가상 네트워크 만들기
[az 네트워크 vnet을 사용하여](/cli/azure/network/vnet#az-network-vnet-create)가상 네트워크를 만듭니다. 이 예제는 *mySubnet이라는*하나의 서브넷을 가진 *myVirtualNetwork라는* 기본 가상 네트워크를 만듭니다.

```azurecli-interactive
az network vnet create --resource-group myResourceGroup --name myVirtualNetwork --address-prefix 10.0.0.0/16  
```
### <a name="create-a-subnet"></a>서브넷 만들기
[az 네트워크 vnet 서브넷을 만드는](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-create)가상 네트워크에 대한 서브넷을 만듭니다. 이 예제에서는 *myVirtualNetwork* 가상 네트워크에서 *mySubnet이라는* 서브넷을 만듭니다.

```azurecli-interactive
az network vnet subnet create --resource-group myResourceGroup --vnet-name myVirtualNetwork --name mySubnet --address-prefixes 10.0.0.0/24    
```
### <a name="create-a-internal-load-balancer"></a>내부 부하 밸러블러 작성 
[az 네트워크 lb를 사용하여](/cli/azure/network/lb#az-network-lb-create)내부 부하 분산기 만들기. 이 예제에서는 *myResourceGroup이라는*리소스 그룹에서 *myILB라는* 내부 로드 밸러블러를 만듭니다. 

```azurecli-interactive
az network lb create --resource-group myResourceGroup --name myILB --sku standard --vnet-name MyVirtualNetwork --subnet mySubnet --frontend-ip-name myFrontEnd --backend-pool-name myBackEndPool
```

### <a name="create-a-load-balancer-health-probe"></a>부하 분산 장치 상태 프로브 만들기

상태 프로브는 모든 가상 머신 인스턴스를 검사하여 네트워크 트래픽을 받을 수 있는지 확인합니다. 프로브 검사에 실패한 가상 머신 인스턴스는 다시 온라인 상태가 되어 프로브 검사가 정상으로 나올 때까지 부하 분산 장치에서 제거됩니다. [az network lb probe create](https://docs.microsoft.com/cli/azure/network/lb/probe?view=azure-cli-latest)를 사용하여 가상 머신의 상태를 모니터링하는 상태 프로브를 만듭니다. 

```azurecli-interactive
  az network lb probe create \
    --resource-group myResourceGroup \
    --lb-name myILB \
    --name myHealthProbe \
    --protocol tcp \
    --port 80   
```

### <a name="create-a-load-balancer-rule"></a>부하 분산 장치 규칙 만들기

부하 분산 장치 규칙은 들어오는 트래픽에 대한 프런트 엔드 IP 구성 및 트래픽을 받을 백 엔드 IP 풀과 필요한 원본 및 대상 포트를 함께 정의합니다. [az network lb rule create](https://docs.microsoft.com/cli/azure/network/lb/rule?view=azure-cli-latest)를 사용하여 *myFrontEnd* 프런트 엔드 풀의 80 포트에서 수신 대기하고, 마찬가지로 80 포트를 통해 부하 분산된 네트워크 트래픽을 *myBackEndPool* 백 엔드 주소 풀에 보내는 *myHTTPRule* 부하 분산 장치 규칙을 만듭니다. 

```azurecli-interactive
  az network lb rule create \
    --resource-group myResourceGroup \
    --lb-name myILB \
    --name myHTTPRule \
    --protocol tcp \
    --frontend-port 80 \
    --backend-port 80 \
    --frontend-ip-name myFrontEnd \
    --backend-pool-name myBackEndPool \
    --probe-name myHealthProbe  
```
### <a name="create-backend-servers"></a>백 엔드 서버 만들기

이 예제에서는 가상 컴퓨터 생성에 대해 다루지 않습니다. [Azure CLI를 사용하여 VM을 로드하기 위해 내부 로드 밸런서 만들기의](../load-balancer/load-balancer-get-started-ilb-arm-cli.md#create-servers-for-the-backend-address-pool) 단계를 수행하여 로드 밸런서의 백 엔드 서버로 사용할 두 개의 가상 컴퓨터를 만들 수 있습니다. 


### <a name="disable-private-link-service-network-policies-on-subnet"></a>서브넷에서 개인 링크 서비스 네트워크 정책 사용 안 함 
개인 링크 서비스는 가상 네트워크 내에서 선택한 모든 서브넷의 IP를 필요로 합니다. 현재 이러한 IP에 대한 네트워크 정책은 지원되지 않습니다.  따라서 서브넷의 네트워크 정책을 비활성화해야 합니다. az 네트워크 vnet 서브넷 업데이트로 개인 링크 서비스 네트워크 정책을 비활성화하려면 [서브넷을 업데이트합니다.](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-update)

```azurecli-interactive
az network vnet subnet update --resource-group myResourceGroup --vnet-name myVirtualNetwork --name mySubnet --disable-private-link-service-network-policies true 
```
 
## <a name="create-a-private-link-service"></a>Private Link 서비스 만들기  
 
[az 네트워크 개인 링크 서비스 만들기를](/cli/azure/network/private-link-service#az-network-private-link-service-create)사용하여 표준 로드 밸러저 프런트 엔드 IP 구성을 사용하여 개인 링크 서비스를 만듭니다. 이 예제에서는 *myResourceGroup이라는*리소스 그룹에서 *myLoadBalancer라는* 표준 로드 밸러블러를 사용하여 *myPLS라는* 개인 링크 서비스를 만듭니다. 
 
```azurecli-interactive
az network private-link-service create \
--resource-group myResourceGroup \
--name myPLS \
--vnet-name myVirtualNetwork \
--subnet mySubnet \
--lb-name myILB \
--lb-frontend-ip-configs myFrontEnd \
--location westcentralus 
```
생성된 후에는 개인 링크 서비스 ID를 기록해 둡을 기록합니다. 나중에 이 서비스에 대한 연결을 요청하려면 이 서비스가 필요합니다.  
 
이 단계에서 개인 링크 서비스가 성공적으로 만들어지고 트래픽을 받을 준비가 되었습니다. 위의 예제는 Azure CLI를 사용하여 개인 링크 서비스를 만드는 것을 보여 주기 위한 것입니다.  트래픽을 수신하기 위해 로드 밸러서 백 엔드 풀 또는 백 엔드 풀의 응용 프로그램을 구성하지 않았습니다. 종단 간 트래픽 흐름을 보려면 표준 로드 밸러서 뒤에 응용 프로그램을 구성하는 것이 좋습니다.  
 
다음으로 Azure CLI를 사용하여 다른 가상 네트워크의 개인 끝점에 이 서비스를 매핑하는 방법을 설명합니다. 다시 이 예제는 Azure CLI를 사용하여 위에서 만든 개인 끝점을 만들고 위에서 만든 개인 링크 서비스에 연결하는 것으로 제한됩니다. 또한 가상 네트워크에서 가상 컴퓨터를 만들어 개인 끝점으로 트래픽을 보내/받을 수 있습니다.        
 
## <a name="private-endpoints"></a>프라이빗 엔드포인트

### <a name="create-the-virtual-network"></a>가상 네트워크 만들기 
 [az 네트워크 vnet을 사용하여](/cli/azure/network/vnet#az-network-vnet-create)가상 네트워크를 만듭니다. 이 예제에서는 *myResourcegroup이라는*리소스 그룹에서 *myPEVNet이라는* 가상 네트워크를 만듭니다. 
```azurecli-interactive
az network vnet create \
--resource-group myResourceGroup \
--name myPEVnet \
--address-prefix 10.0.0.0/16  
```
### <a name="create-the-subnet"></a>서브넷 만들기 
 [az 네트워크 vnet 서브넷을 만드는](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-create)가상 네트워크에서 서브넷을 만듭니다. 이 예제에서는 *myResourcegroup이라는*리소스 그룹에서 *myPEVnet이라는* 가상 네트워크에서 *mySubnet이라는* 서브넷을 만듭니다. 

```azurecli-interactive 
az network vnet subnet create \
--resource-group myResourceGroup \
--vnet-name myPEVnet \
--name myPESubnet \
--address-prefixes 10.0.0.0/24 
```   
## <a name="disable-private-endpoint-network-policies-on-subnet"></a>서브넷에서 개인 엔드포인트 네트워크 정책 사용 안 함 
프라이빗 엔드포인트는 가상 네트워크 내에서 선택한 모든 서브넷에서 만들 수 있습니다. 현재 개인 끝점에 대한 네트워크 정책은 지원되지 않습니다.  따라서 서브넷의 네트워크 정책을 비활성화해야 합니다. az 네트워크 vnet 서브넷 업데이트를 사용하여 개인 엔드포인트 네트워크 정책을 비활성화하도록 [서브넷을 업데이트합니다.](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-update) 

```azurecli-interactive
az network vnet subnet update \
--resource-group myResourceGroup \
--vnet-name myPEVnet \
--name myPESubnet \
--disable-private-endpoint-network-policies true 
```
## <a name="create-private-endpoint-and-connect-to-private-link-service"></a>개인 끝점을 만들고 개인 링크 서비스에 연결 
가상 네트워크에서 위에서 만든 개인 링크 서비스를 사용 하기 위한 개인 끝점을 만듭니다.
  
```azurecli-interactive
az network private-endpoint create \
--resource-group myResourceGroup \
--name myPE \
--vnet-name myPEVnet \
--subnet myPESubnet \
--private-connection-resource-id {PLS_resourceURI} \
--connection-name myPEConnectingPLS \
--location westcentralus 
```
개인 링크 서비스에서 개인 연결 리소스 `az network private-link-service show` *ID를* 얻을 수 있습니다. ID는 다음과 같습니다.   
/구독/하위 ID/리소스그룹/리소스 그룹/공급자/Microsoft.Network/privateLinkServices/개인**링크 서비스 이름** *resourcegroupname* 
 
## <a name="show-private-link-service-connections"></a>개인 링크 서비스 연결 표시 
 
[az 네트워크 개인 링크 서비스 쇼를](/cli/azure/network/private-link-service#az-network-private-link-service-show)사용하여 개인 링크 서비스의 연결 요청을 참조하십시오.    
```azurecli-interactive 
az network private-link-service show --resource-group myResourceGroup --name myPLS 
```
## <a name="next-steps"></a>다음 단계
- [Azure 개인 링크 서비스에](private-link-service-overview.md) 대해 자세히 알아보기
 
