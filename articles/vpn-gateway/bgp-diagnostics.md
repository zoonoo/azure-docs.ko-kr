---
title: BGP 상태 및 메트릭 보기
titleSuffix: Azure VPN Gateway
description: 문제 해결을 위해 중요한 BGP 관련 정보를 확인합니다.
services: vpn-gateway
author: anzaman
ms.service: vpn-gateway
ms.topic: sample
ms.date: 03/10/2021
ms.author: alzam
ms.openlocfilehash: f97bbccc980705699af822ba2730233239cdfd5f
ms.sourcegitcommit: 6776f0a27e2000fb1acb34a8dddc67af01ac14ac
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/11/2021
ms.locfileid: "103148849"
---
# <a name="view-bgp-metrics-and-status"></a>BGP 메트릭 및 상태 보기

Azure Portal 또는 Azure PowerShell을 사용하여 BGP 메트릭 및 상태를 볼 수 있습니다.

## <a name="azure-portal"></a>Azure portal

Azure Portal에서 BGP 피어, 학습 경로 및 보급 경로를 볼 수 있습니다. 이 데이터가 포함된 .csv 파일을 다운로드할 수도 있습니다.

1. [Azure Portal](https://portal.azure.com)에서 가상 네트워크 게이트웨이로 이동합니다.
1. **모니터링** 아래에서 **BGP 피어** 를 선택하여 BGP 피어 페이지를 엽니다.

   :::image type="content" source="./media/bgp-diagnostics/bgp-portal.jpg" alt-text="Azure Portal의 메트릭 스크린샷.":::

### <a name="learned-routes"></a>학습 경로

1. 포털에서 최대 50개의 학습 경로를 볼 수 있습니다.

   :::image type="content" source="./media/bgp-diagnostics/learned-routes.jpg" alt-text="학습 경로의 스크린샷.":::

1. 학습 경로 파일을 다운로드할 수도 있습니다. 학습 경로 수가 50개를 초과하는 경우 모든 경로를 볼 수 있는 유일한 방법은 .csv 파일을 다운로드하여 보는 것입니다. 다운로드하려면 **학습 경로 다운로드** 를 선택합니다.

   :::image type="content" source="./media/bgp-diagnostics/download-routes.jpg" alt-text="학습 경로 다운로드 스크린샷.":::
1. 그런 다음, 파일을 봅니다.

   :::image type="content" source="./media/bgp-diagnostics/learned-routes-file.jpg" alt-text="다운로드한 학습 경로의 스크린샷.":::

### <a name="advertised-routes"></a>보급 경로

1. 보급 경로를 보려면 보려는 네트워크의 끝에 있는 **...** 를 선택한 다음, **보급 경로 보기** 를 클릭합니다.

   :::image type="content" source="./media/bgp-diagnostics/select-route.png" alt-text="보급 경로를 보는 방법을 보여주는 스크린샷." lightbox="./media/bgp-diagnostics/route-expand.png":::
1. **피어에 보급된 경로** 페이지에서 최대 50개의 보급 경로를 볼 수 있습니다.

   :::image type="content" source="./media/bgp-diagnostics/advertised-routes.jpg" alt-text="보급 경로의 스크린샷.":::
1. 보급 경로 파일을 다운로드할 수도 있습니다. 보급 경로 수가 50개를 초과하는 경우 모든 경로를 볼 수 있는 유일한 방법은 .csv 파일을 다운로드하여 보는 것입니다. 다운로드하려면 **보급 경로 다운로드** 를 선택합니다.

   :::image type="content" source="./media/bgp-diagnostics/download-advertised.jpg" alt-text="다운로드된 보급 경로 선택 스크린샷.":::
1. 그런 다음, 파일을 봅니다.

   :::image type="content" source="./media/bgp-diagnostics/advertised-routes-file.jpg" alt-text="다운로드된 보급 경로의 스크린샷.":::

### <a name="bgp-peers"></a>BGP 피어

1. 포털에서 최대 50개의 BGP 피어를 볼 수 있습니다.

   :::image type="content" source="./media/bgp-diagnostics/peers.jpg" alt-text="BGP 피어의 스크린샷.":::
1. BGP 피어 파일을 다운로드할 수도 있습니다. BGP 피어가 50개를 초과하는 경우 모든 피어를 볼 수 있는 유일한 방법은 .csv 파일을 다운로드하여 보는 것입니다. 다운로드하려면 포털 페이지에서 **BGP 피어 다운로드** 를 선택합니다.

   :::image type="content" source="./media/bgp-diagnostics/download-peers.jpg" alt-text="BGP 피어 다운로드 스크린샷.":::
1. 그런 다음, 파일을 봅니다.

   :::image type="content" source="./media/bgp-diagnostics/peers-file.jpg" alt-text="다운로드한 BGP 피어의 스크린샷.":::

## <a name="powershell"></a>PowerShell

**Get-AzVirtualNetworkGatewayBGPPeerStatus** 를 사용하여 모든 BGP 피어 및 상태를 봅니다.

[!INCLUDE [VPN Gateway PowerShell instructions](../../includes/vpn-gateway-cloud-shell-powershell-about.md)]

```azurepowershell-interactive
Get-AzVirtualNetworkGatewayBgpPeerStatus -ResourceGroupName resourceGroup -VirtualNetworkGatewayName gatewayName

Asn               : 65515
ConnectedDuration : 9.01:04:53.5768637
LocalAddress      : 10.1.0.254
MessagesReceived  : 14893
MessagesSent      : 14900
Neighbor          : 10.0.0.254
RoutesReceived    : 1
State             : Connected
```

게이트웨이가 BGP를 통해 학습한 모든 경로를 보려면 **Get-AzVirtualNetworkGatewayLearnedRoute** 를 사용합니다.

```azurepowershell-interactive
Get-AzVirtualNetworkGatewayLearnedRoute -ResourceGroupName resourceGroup -VirtualNetworkGatewayname gatewayName

AsPath       :
LocalAddress : 10.1.0.254
Network      : 10.1.0.0/16
NextHop      :
Origin       : Network
SourcePeer   : 10.1.0.254
Weight       : 32768

AsPath       :
LocalAddress : 10.1.0.254
Network      : 10.0.0.254/32
NextHop      :
Origin       : Network
SourcePeer   : 10.1.0.254
Weight       : 32768

AsPath       : 65515
LocalAddress : 10.1.0.254
Network      : 10.0.0.0/16
NextHop      : 10.0.0.254
Origin       : EBgp
SourcePeer   : 10.0.0.254
Weight       : 32768
```

게이트웨이가 BGP를 통해 피어에 광고하는 모든 경로를 보려면 **Get-AzVirtualNetworkGatewayAdvertisedRoute** 를 사용합니다.

```azurepowershell-interactive
Get-AzVirtualNetworkGatewayAdvertisedRoute -VirtualNetworkGatewayName gatewayName -ResourceGroupName resourceGroupName -Peer 10.0.0.254
```

## <a name="next-steps"></a>다음 단계

BGP에 대한 자세한 내용은 [VPN Gateway에 대한 BGP 구성](bgp-howto.md)을 참조하세요.
