---
title: Express 경로 개인 피어 링을 통한 사이트 간 VPN 연결
description: 이 문서는 트래픽을 암호화 하기 위해 Express 경로 개인 피어 링을 통해 사이트 간 VPN을 사용 하도록 설정 하는 데 도움이 됩니다.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 10/06/2020
ms.author: cherylmc
ms.openlocfilehash: 016741606bad5536985a38b0e0664b39006e1df5
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91776564"
---
# <a name="configure-a-site-to-site-vpn-connection-over-expressroute-private-peering-preview"></a>Express 경로 개인 피어 링을 통해 사이트 간 VPN 연결 구성 (미리 보기)

RFC 1918 IP 주소를 사용 하 여 Express 경로 개인 피어 링을 통해 사이트 간 VPN을 가상 네트워크 게이트웨이에 구성할 수 있습니다. 이 구성은 다음과 같은 이점을 제공 합니다.

* 개인 피어 링을 통한 트래픽이 암호화 됩니다.

* 가상 네트워크 게이트웨이에 연결 하는 지점 및 사이트 간 사용자는 Express 경로 (사이트 간 터널을 통해)를 사용 하 여 온-프레미스 리소스에 액세스할 수 있습니다.

>[!NOTE]
>이 기능은 영역 중복 게이트웨이에서만 지원 됩니다. 예: VpnGw1AZ, VpnGw2AZ 등
>

이 구성을 완료 하려면 다음 필수 구성 요소를 충족 하는지 확인 합니다.

* VPN gateway가 생성 되거나 생성 될 VNet에 연결 되는 작동 하는 Express 경로 회로가 있습니다.

* Express 경로 회로를 통해 VNet의 RFC1918 (개인) IP를 통해 리소스에 연결할 수 있습니다.

## <a name="routing"></a><a name="routing"></a>라우팅

**그림 1** 에서는 express 경로 개인 피어 링을 통한 VPN 연결의 예를 보여 줍니다. 이 예제에서는 Express 경로 개인 피어 링을 통해 Azure 허브 VPN gateway에 연결 된 온-프레미스 네트워크 내 네트워크를 볼 수 있습니다. 이 구성에서 중요 한 점은 Express 경로와 VPN 경로를 통해 온-프레미스 네트워크와 Azure 간의 라우팅입니다.

그림 1

:::image type="content" source="media/site-to-site-vpn-private-peering/routing.png" alt-text="그림 1":::

연결 설정은 간단 합니다.

1. Express 경로 회로와 개인 피어 링을 사용 하 여 Express 경로 연결을 설정 합니다.

1. 이 문서의 단계를 사용 하 여 VPN 연결을 설정 합니다.

### <a name="traffic-from-on-premises-networks-to-azure"></a>온-프레미스 네트워크에서 Azure로의 트래픽

온-프레미스 네트워크에서 Azure로의 트래픽에 대해 Azure 접두사는 Express 경로 개인 피어 링 BGP와 VPN BGP를 통해 보급 됩니다. 그러면 온-프레미스 네트워크에서 Azure에 대 한 두 개의 네트워크 경로 (경로)가 생성 됩니다.

• IPsec으로 보호 된 경로를 통해 하나의 네트워크 경로

• IPsec을 보호 하지 않고 Express 경로를 통해 직접 하나의 네트워크 경로

통신에 암호화를 적용 하려면 **그림 1**에서 vpn에 연결 된 네트워크에 대해 온-프레미스 vpn gateway를 통한 Azure 경로가 직접 express 경로 보다 우선적으로 사용 되도록 해야 합니다.

### <a name="traffic-from-azure-to-on-premises-networks"></a>Azure에서 온-프레미스 네트워크로의 트래픽

Azure에서 온-프레미스 네트워크로의 트래픽에는 동일한 요구 사항이 적용 됩니다. Ipsec 경로가 직접 Express 경로 경로 (IPsec 없음) 보다 선호 되도록 하려면 다음 두 가지 옵션을 사용할 수 있습니다.

• **Vpn 연결 네트워크에 대 한 VPN BGP 세션에서 더 많은 특정 접두사를 보급**합니다. Express 경로 개인 피어 링을 통해 VPN에 연결 된 네트워크를 포함 하는 더 큰 범위를 보급 하 고 VPN BGP 세션에서 더 구체적인 범위를 확인할 수 있습니다. 예를 들어 Express 경로에 대해 10.0.0.0/16을, VPN을 통해 10.0.1.0/24를 보급 합니다.

• **VPN 및 express 경로에 대 한 비연속 접두사를 보급**합니다. VPN에 연결 된 네트워크 범위가 다른 Express 경로 연결 네트워크와 분리 된 경우 VPN 및 Express 경로 BGP 세션에서 각각 접두사를 보급할 수 있습니다. 예를 들어 Express 경로를 통해 10.0.0.0/24를, VPN을 통해 10.0.1.0/24를 보급 합니다.

이러한 두 예제에서 Azure는 vpn 보호 없이 Express 경로를 사용 하는 대신 VPN 연결을 통해 10.0.1.0/24로 트래픽을 전송 합니다.

>[!Warning]
>Express 경로와 VPN 연결 모두에 동일한 접두사를 보급 하는 경우 Azure >VPN 보호 없이 직접 Express 경로 경로를 사용 합니다.
>

## <a name="portal-steps"></a><a name="portal"></a>포털 단계

1. 사이트 간 연결을 구성 합니다. 단계는 [사이트 간 구성](vpn-gateway-howto-site-to-site-resource-manager-portal.md) 문서를 참조 하세요. 게이트웨이에 대 한 영역 중복 게이트웨이 SKU를 선택 해야 합니다. 

   영역 중복 Sku는 SKU의 끝에 "AZ"가 있습니다. 예를 들면 **VpnGw1AZ**입니다. 영역 중복 게이트웨이는 가용성 영역 서비스를 사용할 수 있는 지역 에서만 사용할 수 있습니다. 가용성 영역을 지 원하는 지역에 대 한 자세한 내용은 [가용성 영역을 지 원하는 지역](../availability-zones/az-region.md)을 참조 하세요.

   :::image type="content" source="media/site-to-site-vpn-private-peering/gateway.png" alt-text="그림 1":::
1. 게이트웨이에서 개인 Ip를 사용 하도록 설정 합니다. **구성**을 선택한 다음 **게이트웨이 개인 ip** 를 **사용**으로 설정 합니다. **저장**을 선택하여 변경 내용을 저장합니다.
1. **개요** 페이지에서 **자세히** 보기를 선택 하 여 개인 IP 주소를 확인 합니다. 나중에 구성 단계에서 사용할 수 있도록이 정보를 적어 씁니다.

   :::image type="content" source="media/site-to-site-vpn-private-peering/gateway-overview.png" alt-text="그림 1" lightbox="media/site-to-site-vpn-private-peering/gateway-overview.png":::
1. 연결에서 **Azure 개인 IP 주소를 사용** 하도록 설정 하려면  **구성**을 선택 합니다. **Azure 개인 IP 주소 사용** **을 사용으로 설정 하**고 **저장**을 선택 합니다.

   :::image type="content" source="media/site-to-site-vpn-private-peering/connection.png" alt-text="그림 1":::
1. 방화벽에서 3 단계에서 기록한 개인 IP를 ping 합니다. 개인 IP는 Express 경로 개인 피어 링을 통해 연결할 수 있어야 합니다.
1. 이 개인 IP를 온-프레미스 방화벽에서 원격 IP로 사용 하 여 Express 경로 개인 피어 링을 통해 사이트 간 터널을 설정 합니다.

## <a name="powershell-steps"></a><a name="powershell"></a>PowerShell 단계

1. 사이트 간 연결을 구성 합니다. 단계에 대 한 자세한 내용은 [사이트 간 VPN 구성](vpn-gateway-howto-site-to-site-resource-manager-portal.md) 문서를 참조 하세요. 게이트웨이에 대 한 영역 중복 게이트웨이 SKU를 선택 해야 합니다. 영역 중복 Sku는 SKU의 끝에 "AZ"가 있습니다. 예를 들면 VpnGw1AZ입니다.
1. 다음 PowerShell 명령을 사용 하 여 게이트웨이에서 개인 IP를 사용 하도록 플래그를 설정 합니다.

   ```azurepowershell-interactive
   $Gateway = Get-AzVirtualNetworkGateway -Name <name of gateway> -ResourceGroup <name of resource group>

   Set-AzVirtualNetworkGateway -VirtualNetworkGateway $Gateway -EnablePrivateIpAddress $true
   ```

   공용 및 개인 IP 주소가 표시 되어야 합니다. 출력의 "TunnelIpAddresses" 섹션 아래에 있는 IP 주소를 기록 합니다. 이후 단계에서이 정보를 사용 합니다.
1. 다음 PowerShell 명령을 사용 하 여 개인 IP 주소를 사용 하도록 연결을 설정 합니다.

   ```azurepowershell-interactive
   $Connection = get-AzVirtualNetworkGatewayConnection -Name <name of the connection> -ResourceGroupName <name of resource group>

   Set-AzVirtualNetworkGatewayConnection --VirtualNetworkGatewayConnection $Connection -UseLocalAzureIpAddress $true
   ```
1. 방화벽에서 2 단계에서 적어 둔 개인 IP를 ping 합니다. Express 경로 개인 피어 링을 통해 연결할 수 있어야 합니다.
1. 이 개인 IP를 온-프레미스 방화벽에서 원격 IP로 사용 하 여 Express 경로 개인 피어 링을 통해 사이트 간 터널을 설정 합니다.

## <a name="next-steps"></a>다음 단계

VPN Gateway에 대 한 자세한 내용은 [VPN Gateway 항목](vpn-gateway-about-vpngateways.md) 을 참조 하세요.
