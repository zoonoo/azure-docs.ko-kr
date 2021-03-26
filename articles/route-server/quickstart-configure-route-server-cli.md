---
title: '빠른 시작: Azure CLI를 사용하여 Route Server 만들기 및 구성'
description: 이 빠른 시작에서는 Azure CLI를 사용하여 Route Server를 만들고 구성하는 방법을 알아봅니다.
services: route-server
author: duongau
ms.service: route-server
ms.topic: quickstart
ms.date: 03/02/2021
ms.author: duau
ms.openlocfilehash: e9c583db7493afc04b2c66553801f62d364b0a80
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/20/2021
ms.locfileid: "103419611"
---
# <a name="quickstart-create-and-configure-route-server-using-azure-cli"></a>빠른 시작: Azure CLI를 사용하여 Route Server 만들기 및 구성 

이 문서는 Azure CLI를 사용하여 가상 네트워크의 NVA(네트워크 가상 어플라이언스)와 피어링되도록 Azure Route Server를 구성하는 데 유용합니다. Azure Route Server는 NVA에서 경로를 학습하고 가상 네트워크의 가상 머신에 대해 프로그래밍합니다. 또한 NVA로 가상 네트워크 경로를 보급합니다. 자세한 내용은 [Azure Route Server](overview.md)를 읽어보세요.

> [!IMPORTANT]
> Azure Route Server(미리 보기)는 현재 퍼블릭 미리 보기로 제공됩니다.
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다.
> 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

##  <a name="prerequisites"></a>사전 요구 사항 

* 활성 구독이 있는 Azure 계정. [체험 계정을 만듭니다](https://azure.microsoft.com/free/?WT.mc_id=A261C142F). 
* 최신 Azure CLI가 있는지 확인하거나 포털에서 Azure Cloud Shell을 사용할 수 있습니다. 
* [Azure Route Server에 대한 서비스 제한](route-server-faq.md#limitations)을 검토합니다. 

##  <a name="create-a-route-server"></a>Route Server 만들기 

###  <a name="sign-in-to-your-azure-account-and-select-your-subscription"></a>Azure 계정에 로그인하고 구독을 선택합니다. 

구성을 시작하려면, Azure 계정에 로그인합니다. Cloud Shell의 "사용해 보세요"를 사용하는 경우 자동으로 로그인됩니다. 연결에 도움이 되도록 다음 예제를 사용합니다.

```azurecli-interactive
az login
```

계정에 대한 구독을 확인합니다.

```azurecli-interactive
az account list
```

ExpressRoute 회로를 만들려는 구독을 선택합니다.

```azurecli-interactive
az account set --subscription "<subscription ID>"
```

### <a name="create-a-resource-group-and-virtual-network"></a>리소스 그룹 및 가상 네트워크 만들기 

Azure Route Server를 만들려면 배포를 호스트하기 위한 가상 네트워크가 필요합니다. 다음 명령을 사용하여 리소스 그룹 및 가상 네트워크를 만듭니다. 가상 네트워크가 이미 있는 경우 다음 섹션으로 건너뛸 수 있습니다.

```azurecli-interactive
az group create -n "RouteServerRG" -l "westus" 
az network vnet create -g "RouteServerRG" -n "myVirtualNetwork" --address-prefix "10.0.0.0/16" 
``` 

### <a name="add-a-subnet"></a>서브넷 추가 

1. Azure Route Server를 배포할 *RouteServerSubnet* 이라는 서브넷을 추가합니다. 이 서브넷은 Azure Route Server에 대한 전용 서브넷입니다. RouteServerSubnet은 /27 또는 더 짧은 접두사(예: /26, /25)여야 합니다. 그렇지 않으면 Azure Route Server를 추가할 때 오류 메시지가 표시됩니다.

    ```azurecli-interactive 
    az network vnet subnet create -g "RouteServerRG" --vnet-name "myVirtualNetwork" --name "RouteServerSubnet" --address-prefix "10.0.0.0/24"  
    ``` 

1. RouteServerSubnet ID를 확인합니다. 가상 네트워크의 모든 서브넷에 대한 리소스 ID를 보려면 다음 명령을 사용합니다. 

    ```azurecli-interactive 
    $subnet_id = $(az network vnet subnet show -n "RouteServerSubnet" --vnet-name "myVirtualNetwork" -g "RouteServerRG" --query id -o tsv) 
    ``` 

RouteServerSubnet ID는 다음과 같습니다. 

`/subscriptions/<subscriptionID>/resourceGroups/RouteServerRG/providers/Microsoft.Network/virtualNetworks/myVirtualNetwork/subnets/RouteServerSubnet`

## <a name="create-the-route-server"></a>Route Server 만들기 

다음 명령을 사용하여 Route Server를 만듭니다. 

```azurecli-interactive
az network routeserver create -n "myRouteServer" -g "RouteServerRG" --hosted-subnet $subnet_id  
``` 

위치는 가상 네트워크의 위치와 일치해야 합니다. HostedSubnet은 이전 섹션에서 얻은 RouteServerSubnet ID입니다. 

## <a name="create-peering-with-an-nva"></a>NVA와의 피어링 만들기 

다음 명령을 사용하여 Route Server에서 NVA로의 피어링을 설정합니다. 

```azurecli-interactive 

az network routeserver peering create --routeserver-name "myRouteServer" -g "RouteServerRG" --peer-ip "nva_ip" --peer-asn "nva_asn" -n "NVA1_name" 

``` 

"nva_ip"는 NVA에 할당된 가상 네트워크 IP입니다. "nva_asn"은 NVA에 구성된 ASN(자율 시스템 번호)입니다. ASN은 65515-65520 범위 이외의 16비트 숫자가 될 수 있습니다. 이 ASN 범위는 Microsoft에서 예약되어 있습니다. 

중복성을 위해 다른 NVA 또는 동일한 NVA의 다른 인스턴스와의 피어링을 설정하려면 다음 명령을 사용합니다.

```azurecli-interactive 

az network routeserver peering create --routeserver-name "myRouteServer" -g "RouteServerRG" --peer-ip "nva_ip" --peer-asn "nva_asn" -n "NVA2_name" 
``` 

## <a name="complete-the-configuration-on-the-nva"></a>NVA에 대한 구성 완료 

NVA에 대한 구성을 완료하고 BGP 세션을 사용하도록 설정하려면 Azure Route Server의 IP 및 ASN이 필요합니다. 다음 명령을 사용하여 이 정보를 가져올 수 있습니다. 

```azurecli-interactive 
az network routeserver show -g "RouteServerRG" -n "myRouteServer" 
``` 

출력에는 다음 정보가 포함됩니다. 

```azurecli-interactive 
RouteServerAsn  : 65515 

RouteServerIps  : {10.5.10.4, 10.5.10.5}  "virtualRouterAsn": 65515, 

  "virtualRouterIps": [ 

    "10.0.0.4", 

    "10.0.0.5" 

  ], 

``` 

## <a name="configure-route-exchange"></a>경로 교환 구성 

ExpressRoute 게이트웨이와 Azure VPN Gateway가 동일한 VNet에 있으며 경로를 교환하려는 경우 Azure Route Server에서 경로 교환을 사용하도록 설정할 수 있습니다.

> [!IMPORTANT]
> 최적의 배포를 위해서는 Azure Route Server를 만들기 전에 Azure VPN Gateway를 만들어야 합니다. 그러지 않으면 Azure VPN Gateway 배포가 실패합니다.
> 

1. Azure Route Server와 게이트웨이 간의 경로 교환을 사용하도록 설정하려면 다음 명령을 사용합니다.

```azurecli-interactive 
az network routeserver update -g "RouteServerRG" -n "myRouteServer" --allow-b2b-traffic true 

``` 

2. Azure Route Server와 게이트웨이 간의 경로 교환을 사용하지 않도록 설정하려면 다음 명령을 사용합니다.

```azurecli-interactive
az network routeserver update -g "RouteServerRG" -n "myRouteServer" --allow-b2b-traffic false 
``` 

## <a name="troubleshooting"></a>문제 해결 

다음 명령을 사용하여 Azure Route Server에서 보급 및 수신한 경로를 볼 수 있습니다.

```azurecli-interactive 
az network routeserver peering list-advertised-routes -g RouteServerRG --vrouter-name myRouteServer -n NVA1_name 
az network routeserver peering list-learned-routes -g RouteServerRG --vrouter-name myRouteServer -n NVA1_name 
``` 

## <a name="clean-up-resources"></a>리소스 정리

Azure Route Server가 더 이상 필요하지 않은 경우 다음 명령을 사용하여 BGP 피어링을 제거한 후 Route Server를 제거합니다. 

1. 다음 명령을 사용하여 Azure Route Server와 NVA 간의 BGP 피어링을 제거합니다.

```azurecli-interactive
az network routeserver peering delete --routeserver-name "myRouteServer" -g "RouteServerRG" -n "NVA2_name" 
``` 

2. 다음 명령을 사용하여 Azure Route Server를 제거합니다. 

```azurecli-interactive 
az network routeserver delete -n "myRouteServer" -g "RouteServerRG" 
``` 

## <a name="next-steps"></a>다음 단계

Azure Route Server를 만든 후에 Azure Route Server가 ExpressRoute 및 VPN Gateway와 상호 작용하는 방법에 대해 계속 알아보세요. 

> [!div class="nextstepaction"]
> [Azure ExpressRoute 및 Azure VPN 지원](expressroute-vpn-support.md)
 
