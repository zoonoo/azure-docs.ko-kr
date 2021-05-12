---
title: Azure VPN Gateway에 대해 OpenVPN을 구성하는 방법
description: 지점 및 사이트 간 환경에 대해 Azure VPN Gateway에서 OpenVPN 프로토콜을 사용하도록 설정하는 방법에 대해 알아봅니다.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 02/05/2021
ms.author: cherylmc
ms.openlocfilehash: 137e4e1372ef1af3319c0b9af7ba965fffcb9e34
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104584043"
---
# <a name="configure-openvpn-for-point-to-site-vpn-gateways"></a>지점 및 사이트 간 VPN 게이트웨이를 위한 OpenVPN 구성

이 문서는 Azure VPN 게이트웨이에서 **OpenVPN® 프로토콜** 을 설정하는 데 도움이 됩니다. 포털 또는 PowerShell 지침을 사용할 수 있습니다.

## <a name="prerequisites"></a>필수 구성 요소

* 이 문서에서는 작동하는 지점-사이트 간 환경이 이미 있다고 가정합니다. 없는 경우 다음 방법 중 하나를 사용하여 환경을 만듭니다.

  * [포털 - 지점 및 사이트 간 만들기](vpn-gateway-howto-point-to-site-resource-manager-portal.md)

  * [PowerShell - 지점 및 사이트 간 만들기](vpn-gateway-howto-point-to-site-rm-ps.md)

* VPN 게이트웨이에서 기본 SKU를 사용하지 않는지 확인합니다. 기본 SKU는 OpenVPN에서 지원되지 않습니다.

## <a name="portal"></a>포털

1. 포털에서 **가상 네트워크 게이트웨이 -> 지점 및 사이트 간 구성** 으로 이동합니다.
1. **터널 유형** 의 드롭다운에서 **OpenVPN(SSL)** 을 선택합니다.

   :::image type="content" source="./media/vpn-gateway-howto-openvpn/portal.png" alt-text="드롭다운에서 OpenVPN SSL을 선택":::
1. 변경 내용을 저장하고 **다음 단계** 를 계속합니다.

## <a name="powershell"></a>PowerShell

1. 다음 예제를 사용하여 게이트웨이에서 OpenVPN을 사용하도록 설정합니다.

   ```azurepowershell-interactive
   $gw = Get-AzVirtualNetworkGateway -ResourceGroupName $rgname -name $name
   Set-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -VpnClientProtocol OpenVPN
   ```
1. **다음 단계** 를 계속합니다.

## <a name="next-steps"></a>다음 단계

OpenVPN 클라이언트를 구성하려면 [OpenVPN 클라이언트 구성](vpn-gateway-howto-openvpn-clients.md)을 참조하세요.

**"OpenVPN"은 OpenVPN Inc.의 상표입니다.**
