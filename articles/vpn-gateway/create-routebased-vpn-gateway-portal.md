---
title: '경로 기반 VPN gateway 만들기: 포털'
titleSuffix: Azure VPN Gateway
description: Azure Portal을 사용하여 경로 기반 VPN 게이트웨이 만들기
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: article
ms.date: 09/24/2019
ms.author: cherylmc
ms.openlocfilehash: a44bdf654842d964a02f6aebe60e3632aabcd64d
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/17/2019
ms.locfileid: "74151678"
---
# <a name="create-a-route-based-vpn-gateway-using-the-azure-portal"></a>Azure Portal을 사용하여 경로 기반 VPN 게이트웨이 만들기

이 문서는 Azure Portal을 사용하여 경로 기반 Azure VPN Gateway를 빠르게 만드는 데 도움이 됩니다.  VPN 게이트웨이는 온-프레미스 네트워크에 대한 VPN 연결을 만들 때 사용됩니다. VPN 게이트웨이를 사용하여 VNet을 연결할 수도 있습니다. 

이 문서의 단계에서는 VNet, 서브넷, 게이트웨이 서브넷 및 경로 기반 VPN 게이트웨이(가상 네트워크 게이트웨이)를 만듭니다. 게이트웨이 생성이 완료되면 연결을 만들 수 있습니다. 이러한 단계에는 Azure 구독이 필요합니다. Azure 구독이 아직 없는 경우 시작하기 전에 [무료 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) 을 만듭니다.

## <a name="vnet"></a>가상 네트워크 만들기

[!INCLUDE [create-gateway](../../includes/vpn-gateway-create-virtual-network-portal-include.md)]

## <a name="gwvalues"></a>게이트웨이 구성 및 만들기

이 단계에서는 VNet용 가상 네트워크 게이트웨이를 만듭니다. 종종 선택한 게이트웨이 SKU에 따라 게이트웨이를 만드는 데 45분 이상 걸릴 수 있습니다.

[!INCLUDE [About gateway subnets](../../includes/vpn-gateway-about-gwsubnet-portal-include.md)]

[!INCLUDE [create-gateway](../../includes/vpn-gateway-add-gw-p2s-rm-portal-include.md)]

>[!NOTE]
>기본 게이트웨이 SKU는 IKEv2 또는 RADIUS 인증을 지원 하지 않습니다. Mac 클라이언트가 가상 네트워크에 연결 하도록 계획 하는 경우 기본 SKU를 사용 하지 마세요.

[!INCLUDE [NSG warning](../../includes/vpn-gateway-no-nsg-include.md)]

## <a name="viewgw"></a>VPN 게이트웨이 보기

1. 게이트웨이가 생성된 후 포털에서 VNet1로 이동합니다. VPN 게이트웨이가 개요 페이지에 연결된 디바이스로 나타납니다.

   ![연결 된 장치](./media/create-routebased-vpn-gateway-portal/view-connected-devices.png "연결된 디바이스")

2. 디바이스 목록에서 **VNet1GW**를 클릭하여 자세한 정보를 확인합니다.

   ![VPN gateway 보기](./media/create-routebased-vpn-gateway-portal/view-gateway.png "VPN gateway 보기")

## <a name="next-steps"></a>다음 단계

게이트웨이 생성이 완료되면 가상 네트워크와 VNet 간에 연결을 만들 수 있습니다. 또는 가상 네트워크와 온-프레미스 위치 간에 연결을 만듭니다.

> [!div class="nextstepaction"]
> [사이트 간 연결 만들기](vpn-gateway-howto-site-to-site-resource-manager-portal.md)<br><br>
> [지점 및 사이트 간 연결 만들기](vpn-gateway-howto-point-to-site-resource-manager-portal.md)<br><br>
> [다른 VNet에 대한 연결 만들기](vpn-gateway-howto-vnet-vnet-resource-manager-portal.md)
