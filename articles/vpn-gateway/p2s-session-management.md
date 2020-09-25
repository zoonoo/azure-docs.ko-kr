---
title: 'Azure VPN Gateway: 지점 및 사이트 간 VPN 세션 관리'
description: 이 문서에서는 지점 및 사이트 간 VPN 세션을 확인 하 고 연결을 해제 하는 방법을 설명 합니다.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 09/23/2020
ms.author: cherylmc
ms.openlocfilehash: 2f2184507e17e3ecae40bb33be4202c183d32b77
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/25/2020
ms.locfileid: "91274236"
---
# <a name="point-to-site-vpn-session-management"></a>지점 및 사이트 간 VPN 세션 관리

Azure 가상 네트워크 게이트웨이는 현재 지점 및 사이트 간 VPN 세션을 쉽게 확인 하 고 연결을 끊는 방법을 제공 합니다. 이 문서를 통해 현재 세션을 확인 하 고 연결을 끊을 수 있습니다.

>[!NOTE]
>세션 상태는 5 분 마다 업데이트 됩니다. 즉시 업데이트 되지 않습니다.
>

## <a name="portal"></a>포털

포털에서 세션을 확인 하 고 연결을 끊으려면:

1. VPN 게이트웨이로 이동 합니다.
1. **모니터링** 섹션에서 **지점 및 사이트 간 세션**을 선택 합니다.

   :::image type="content" source="./media/p2s-session-management/portal.png" alt-text="포털 예제":::
1. 창의 이름의 모든 현재 세션을 볼 수 있습니다.
1. 연결을 끊을 세션에 대해 **"..."** 를 선택한 다음 **연결 끊기**를 선택 합니다.

## <a name="powershell"></a>PowerShell

PowerShell을 사용 하 여 세션을 확인 하 고 연결을 끊으려면

1. 다음 PowerShell 명령을 실행 하 여 활성 세션을 나열 합니다.

   ```azurepowershell-interactive
   Get-AzVirtualNetworkGatewayVpnClientConnectionHealth -VirtualNetworkGatewayName <name of the gateway>  -ResourceGroupName <name of the resource group>
   ```
1. 연결을 끊을 세션의 **VpnConnectionId** 를 복사 합니다.

   :::image type="content" source="./media/p2s-session-management/powershell.png" alt-text="PowerShell 예제":::
1. 세션의 연결을 끊으려면 다음 명령을 실행 합니다.

   ```azurepowershell-interactive
   Disconnect-AzVirtualNetworkGatewayVpnConnection -VirtualNetworkGatewayName <name of the gateway> -ResourceGroupName <name of the resource group> -VpnConnectionId <VpnConnectionId of the session>
   ```

## <a name="next-steps"></a>다음 단계

지점 및 사이트 간 연결에 대 한 자세한 내용은 지점 및 [사이트 간 VPN 정보](point-to-site-about.md)를 참조 하세요.
