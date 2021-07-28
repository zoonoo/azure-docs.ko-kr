---
title: 지점 및 사이트 간 VPN 세션 관리
titleSuffix: Azure VPN Gateway
description: 지점 및 사이트 간 VPN 세션을 보고 연결을 끊는 방법에 대해 알아봅니다.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 04/26/2021
ms.author: cherylmc
ms.openlocfilehash: 79432bfd65feeae79017a883be990d88134cbb10
ms.sourcegitcommit: a5dd9799fa93c175b4644c9fe1509e9f97506cc6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2021
ms.locfileid: "108202536"
---
# <a name="point-to-site-vpn-session-management"></a>지점 및 사이트 간 VPN 세션 관리

Azure 가상 네트워크 게이트웨이는 현재 지점 및 사이트 간 VPN 세션을 확인하고 연결을 끊는 편리한 방법을 제공합니다. 이 문서는 현재 세션을 확인하고 연결을 끊는 데 도움이 됩니다. 세션 상태는 5분마다 업데이트됩니다. 즉시 업데이트되지 않습니다. 


## <a name="portal"></a>포털

>[!NOTE]
> 연결 원본 정보는 IKEv2 및 OpenVPN 연결에 대해서만 제공됩니다.
> 

포털에서 세션을 확인하고 연결을 끊으려면 다음을 수행합니다.

1. VPN Gateway로 이동합니다.
1. **모니터링** 섹션에서 **지점 및 사이트 간 세션** 을 선택합니다.

   :::image type="content" source="./media/p2s-session-management/portal.png" alt-text="포털 예제":::
1. windowpane에서 현재 세션을 모두 볼 수 있습니다.
1. 연결을 끊을 세션의 **“...”** 를 선택한 다음 **연결 끊기** 를 선택합니다.

현재 VpnGw4 및 VpnGw5 SKU용 포털에서는 이 기능을 사용할 수 없습니다. 이러한 게이트웨이 중 하나를 사용하는 경우 다음 섹션에서 설명하는 PowerShell 방법을 사용합니다.

## <a name="powershell"></a>PowerShell

PowerShell을 사용하여 세션을 확인하고 연결을 끊으려면 다음을 수행합니다.

1. 다음 PowerShell 명령을 실행하여 활성 세션을 나열합니다.

   ```azurepowershell-interactive
   Get-AzVirtualNetworkGatewayVpnClientConnectionHealth -VirtualNetworkGatewayName <name of the gateway>  -ResourceGroupName <name of the resource group>
   ```
1. 연결을 끊을 세션의 **VpnConnectionId** 를 복사합니다.

   :::image type="content" source="./media/p2s-session-management/powershell.png" alt-text="PowerShell 예제":::
1. 세션의 연결을 끊으려면 다음 명령을 실행합니다.

   ```azurepowershell-interactive
   Disconnect-AzVirtualNetworkGatewayVpnConnection -VirtualNetworkGatewayName <name of the gateway> -ResourceGroupName <name of the resource group> -VpnConnectionId <VpnConnectionId of the session>
   ```

## <a name="next-steps"></a>다음 단계

지점 및 사이트 간 연결에 대한 자세한 내용은 [지점 및 사이트 간 VPN 정보](point-to-site-about.md)를 참조하세요.
