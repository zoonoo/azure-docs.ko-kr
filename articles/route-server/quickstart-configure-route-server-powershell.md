---
title: '빠른 시작: Azure PowerShell을 사용하여 Route Server 만들기 및 구성'
description: 이 빠른 시작에서는 Azure PowerShell을 사용하여 Route Server를 만들고 구성하는 방법을 알아봅니다.
services: route-server
author: duongau
ms.author: duau
ms.date: 04/23/2021
ms.topic: quickstart
ms.service: route-server
ms.custom: devx-track-azurepowershell - mode-api
ms.openlocfilehash: 2000c30a96f241ff5500552277f16b8b789ba8c9
ms.sourcegitcommit: 20acb9ad4700559ca0d98c7c622770a0499dd7ba
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/29/2021
ms.locfileid: "110690796"
---
# <a name="quickstart-create-and-configure-route-server-using-azure-powershell"></a>빠른 시작: Azure PowerShell을 사용하여 Route Server 만들기 및 구성

이 문서는 PowerShell을 사용하여 가상 네트워크의 NVA(네트워크 가상 어플라이언스)와 피어링되도록 Azure Route Server를 구성하는 데 유용합니다. Azure Route Server는 NVA에서 경로를 학습하고 가상 네트워크의 가상 머신에 대해 프로그래밍합니다. 또한 Azure Route Server는 NVA에 가상 네트워크 경로를 보급합니다. 자세한 내용은 [Azure Route Server](overview.md)를 읽어보세요.

:::image type="content" source="media/quickstart-configure-route-server-portal/environment-diagram.png" alt-text="Azure PowerShell을 사용하는 Route Server 배포 환경의 다이어그램." border="false":::

> [!IMPORTANT]
> Azure Route Server(미리 보기)는 현재 퍼블릭 미리 보기로 제공됩니다.
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다.
> 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

## <a name="prerequisites"></a>사전 요구 사항

* 활성 구독이 있는 Azure 계정. [체험 계정을 만듭니다](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* 최신 PowerShell 모듈이 있는지 확인하거나 포털에서 Azure Cloud Shell을 사용할 수 있습니다.
* [Azure Route Server에 대한 서비스 제한](route-server-faq.md#limitations)을 검토합니다.

## <a name="create-a-route-server"></a>Route Server 만들기

### <a name="sign-in-to-your-azure-account-and-select-your-subscription"></a>Azure 계정에 로그인하고 구독을 선택합니다.

[!INCLUDE [sign in](../../includes/expressroute-cloud-shell-connect.md)]

### <a name="create-a-resource-group-and-virtual-network"></a>리소스 그룹 및 가상 네트워크 만들기

Azure Route Server를 만들려면 배포를 호스트하기 위한 가상 네트워크가 필요합니다. 다음 명령을 사용하여 리소스 그룹 및 가상 네트워크를 만듭니다. 가상 네트워크가 이미 있는 경우 다음 섹션으로 건너뛸 수 있습니다.

```azurepowershell-interactive
New-AzResourceGroup –Name "RouteServerRG” -Location “West US"
New-AzVirtualNetwork –ResourceGroupName "RouteServerRG" -Location "West US" -Name myVirtualNetwork –AddressPrefix 10.0.0.0/16
```

### <a name="add-a-subnet"></a>서브넷 추가

1. Azure Route Server를 배포할 *RouteServerSubnet* 이라는 서브넷을 추가합니다. 이 서브넷은 Azure Route Server에 대한 전용 서브넷입니다. RouteServerSubnet은 /27 또는 더 짧은 접두사(예: /26, /25)여야 합니다. 그렇지 않으면 Azure Route Server를 추가할 때 오류 메시지가 표시됩니다.

    ```azurepowershell-interactive
    $vnet = Get-AzVirtualNetwork –Name "myVirtualNetwork" - ResourceGroupName "RouteServerRG"
    Add-AzVirtualNetworkSubnetConfig –Name "RouteServerSubnet" -AddressPrefix 10.0.0.0/24 -VirtualNetwork $vnet
    $vnet | Set-AzVirtualNetwork
    ```

1. RouteServerSubnet ID를 확인합니다. 가상 네트워크의 모든 서브넷에 대한 리소스 ID를 보려면 다음 명령을 사용합니다.

    ```azurepowershell-interactive
    $vnet = Get-AzVirtualNetwork –Name "vnet_name" -ResourceGroupName "RouteServerRG"
    $vnet.Subnets
    ```

RouteServerSubnet ID는 다음과 같습니다.

`/subscriptions/<subscriptionID>/resourceGroups/RouteServerRG/providers/Microsoft.Network/virtualNetworks/myVirtualNetwork/subnets/RouteServerSubnet`

## <a name="create-the-route-server"></a>Route Server 만들기

다음 명령을 사용하여 Route Server를 만듭니다.

```azurepowershell-interactive 
New-AzRouteServer -RouteServerName myRouteServer -ResourceGroupName RouteServerRG -Location "West US" -HostedSubnet "RouteServerSubnet_ID"
```

위치는 가상 네트워크의 위치와 일치해야 합니다. HostedSubnet은 이전 섹션에서 얻은 RouteServerSubnet ID입니다.

## <a name="create-peering-with-an-nva"></a>NVA와의 피어링 만들기

다음 명령을 사용하여 Route Server에서 NVA로의 BGP 피어링을 설정합니다.

```azurepowershell-interactive 
Add-AzRouteServerPeer -PeerName "myNVA" -PeerIp "nva_ip" -PeerAsn "nva_asn" -RouteServerName myRouteServer -ResourceGroupName RouteServerRG
```

"nva_ip"는 NVA에 할당된 가상 네트워크 IP입니다. "nva_asn"은 NVA에 구성된 ASN(자치 시스템 번호)입니다. ASN은 65515-65520 범위 이외의 16비트 숫자가 될 수 있습니다. 이 ASN 범위는 Microsoft에서 예약되어 있습니다.

중복성을 위해 다른 NVA 또는 동일한 NVA의 다른 인스턴스와의 피어링을 설정하려면 다음 명령을 사용합니다.

```azurepowershell-interactive 
Add-AzRouteServerPeer -PeerName "NVA2_name" -PeerIp "nva2_ip" -PeerAsn "nva2_asn" -RouteServerName myRouteServer -ResourceGroupName RouteServerRG 
```

## <a name="complete-the-configuration-on-the-nva"></a>NVA에 대한 구성 완료

NVA에 대한 구성을 완료하고 BGP 세션을 사용하도록 설정하려면 Azure Route Server의 IP 및 ASN이 필요합니다. 다음 명령을 사용하여 이 정보를 가져올 수 있습니다.

```azurepowershell-interactive 
Get-AzRouteServer -RouterServerName myRouteServer -ResourceGroupName RouteServerRG
```

출력에는 다음 정보가 포함됩니다.

``` 
RouteServerAsn : 65515
RouteServerIps : {10.5.10.4, 10.5.10.5}
```

## <a name="configure-route-exchange"></a><a name = "route-exchange"></a>경로 교환 구성

ExpressRoute 게이트웨이와 Azure VPN Gateway가 동일한 VNet에 있으며 경로를 교환하려는 경우 Azure Route Server에서 경로 교환을 사용하도록 설정할 수 있습니다.

1. Azure Route Server와 게이트웨이 간의 경로 교환을 사용하도록 설정하려면 다음 명령을 사용합니다.

```azurepowershell-interactive 
Update-AzRouteServer -RouteServerName myRouteServer -ResourceGroupName RouteServerRG -AllowBranchToBranchTraffic 
```

2. Azure Route Server와 게이트웨이 간의 경로 교환을 사용하지 않도록 설정하려면 다음 명령을 사용합니다.

```azurepowershell-interactive 
Update-AzRouteServer -RouteServerName myRouteServer -ResourceGroupName RouteServerRG
```

## <a name="troubleshooting"></a>문제 해결

다음 명령을 사용하여 Azure Route Server에서 보급 및 수신한 경로를 볼 수 있습니다.

```azurepowershell-interactive
Get-AzRouteServerPeerAdvertisedRoute
Get-AzRouteServerPeerLearnedRoute
```
## <a name="clean-up-resources"></a>리소스 정리

Azure Route Server가 더 이상 필요하지 않은 경우 다음 명령을 사용하여 BGP 피어링을 제거한 후 Route Server를 제거합니다. 

1. 다음 명령을 사용하여 Azure Route Server와 NVA 간의 BGP 피어링을 제거합니다.

```azurepowershell-interactive 
Remove-AzRouteServerPeer -PeerName "nva_name" -RouteServerName myRouteServer -ResourceGroupName RouteServerRG 
```

2. 다음 명령을 사용하여 Azure Route Server를 제거합니다.

```azurepowershell-interactive 
Remove-AzRouteServer -RouteServerName myRouteServer -ResourceGroupName RouteServerRG
```

## <a name="next-steps"></a>다음 단계

Azure Route Server를 만든 후에 Azure Route Server가 ExpressRoute 및 VPN Gateway와 상호 작용하는 방법에 대해 계속 알아보세요. 

> [!div class="nextstepaction"]
> [Azure ExpressRoute 및 Azure VPN 지원](expressroute-vpn-support.md)
