---
title: 'Azure VPN 게이트웨이: P2S VPN 클라이언트에 대한 사용자 지정 경로 광고'
description: 지점 간 클라이언트에 사용자 지정 경로를 광고하는 단계
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: article
ms.date: 11/11/2019
ms.author: cherylmc
ms.openlocfilehash: 7a904857b8aa0ed2aa18fc2a1b81fe31541e6f9e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74151899"
---
# <a name="advertise-custom-routes-for-p2s-vpn-clients"></a>P2S VPN 클라이언트를 위한 사용자 지정 경로 광고

모든 지점 간 VPN 클라이언트에 사용자 지정 경로를 보급할 수 있습니다. 예를 들어 VNet에서 저장소 끝점을 사용하도록 설정하고 원격 사용자가 VPN 연결을 통해 이러한 저장소 계정에 액세스할 수 있도록 하려는 경우를 예로 들 수 있습니다. 저장소 끝점의 IP 주소를 모든 원격 사용자에게 보급하여 저장소 계정에 대한 트래픽이 공용 인터넷이 아닌 VPN 터널을 통과하도록 할 수 있습니다.

![Azure VPN Gateway 다중 사이트 연결 예제](./media/vpn-gateway-p2s-advertise-custom-routes/custom-routes.png)

## <a name="to-advertise-custom-routes"></a>사용자 지정 경로를 광고하려면

사용자 지정 경로를 보급하려면 `Set-AzVirtualNetworkGateway cmdlet`을 사용합니다. 다음 예제에서는 [Contoso 저장소 계정 테이블에](https://contoso.table.core.windows.net)대 한 IP를 보급 하는 방법을 보여 주실 수 있습니다.

1. ping *contoso.table.core.windows.net* IP 주소를 기록합니다. 예를 들어:

    ```cmd
    C:\>ping contoso.table.core.windows.net
    Pinging table.by4prdstr05a.store.core.windows.net [13.88.144.250] with 32 bytes of data:
    ```

2. 다음 PowerShell 명령을 실행합니다.

    ```azurepowershell-interactive
    $gw = Get-AzVirtualNetworkGateway -Name <name of gateway> -ResourceGroupName <name of resource group>
    Set-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -CustomRoute 13.88.144.250/32
    ```

3. 여러 사용자 지정 경로를 추가하려면 혼수 및 공백을 사용하여 주소를 구분합니다. 예를 들어:

    ```azurepowershell-interactive
    Set-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -CustomRoute x.x.x.x/xx , y.y.y.y/yy
    ```
## <a name="to-view-custom-routes"></a>사용자 지정 경로를 보려면

다음 예제를 사용하여 사용자 지정 경로를 봅니다.

  ```azurepowershell-interactive
  $gw = Get-AzVirtualNetworkGateway -Name <name of gateway> -ResourceGroupName <name of resource group>
  $gw.CustomRoutes | Format-List
  ```
## <a name="to-delete-custom-routes"></a>사용자 지정 경로를 삭제하려면

다음 예제를 사용하여 사용자 지정 경로를 삭제합니다.

  ```azurepowershell-interactive
  $gw = Get-AzVirtualNetworkGateway -Name <name of gateway> -ResourceGroupName <name of resource group>
  Set-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -CustomRoute @0
  ```
## <a name="next-steps"></a>다음 단계

추가 P2S 라우팅 정보는 [지점 간 라우팅 정보를](vpn-gateway-about-point-to-site-routing.md)참조하십시오.
