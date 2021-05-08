---
title: 'Azure ExpressRoute: Azure Portal을 사용하여 IPv6 지원 추가'
description: Azure Portal를 사용하여 Azure 배포에 연결하기 위해 IPv6 지원을 추가하는 방법을 알아봅니다.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 03/09/2021
ms.author: duau
ms.openlocfilehash: 7f5afc05a8d03d33366a2f76318bcf5e039d4d30
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105561664"
---
# <a name="add-ipv6-support-for-private-peering-using-the-azure-portal-preview"></a>Azure Portal를 사용하여 개인 피어링에 대한 IPv6 지원 추가(미리 보기)

이 문서에서는 Azure Portal를 사용하여 Azure에서 ExpressRoute를 통해 Azure의 리소스에 연결하기 위해 IPv6 지원을 추가하는 방법을 설명합니다. 

> [!Note]
> 이 기능은 현재 [가용성 영역이 있는 Azure 지역](../availability-zones/az-region.md#azure-regions-with-availability-zones)에서 미리 보기로 사용할 수 있습니다. 따라서 ExpressRoute 회로를 피어링 위치를 사용하여 만들 수 있지만 연결하는 IPv6 기반 배포는 가용성 영역이 있는 지역에 있어야 합니다.

## <a name="register-for-public-preview"></a>퍼블릭 미리 보기 등록
IPv6 지원을 추가하기 전에 먼저 구독을 등록해야 합니다. 등록하려면 Azure PowerShell을 통해 다음 명령을 실행합니다.

1.  Azure에 로그인하고 구독을 선택합니다. ExpressRoute 회로를 포함하는 구독 및 Azure 배포가 포함된 구독에 대해 이러한 명령을 실행합니다(다른 경우).

    ```azurepowershell-interactive
    Connect-AzAccount 

    Select-AzSubscription -Subscription "<SubscriptionID or SubscriptionName>"
    ```

1. 다음 명령을 사용하여 퍼블릭 미리 보기에 대한 구독을 등록합니다.
    ```azurepowershell-interactive
    Register-AzProviderFeature -FeatureName AllowIpv6PrivatePeering -ProviderNamespace Microsoft.Network
    ```

그러면 2-3일의 영업일 이내에 ExpressRoute 팀에서 요청을 승인하게 됩니다.

## <a name="sign-in-to-the-azure-portal"></a>Azure Portal에 로그인

브라우저에서 [Azure Portal](https://portal.azure.com)로 이동하고 Azure 계정으로 로그인합니다.

## <a name="add-ipv6-private-peering-to-your-expressroute-circuit"></a>ExpressRoute 회로에 IPv6 개인 피어링 추가

1. [ExpressRoute 회로를 만들거나](expressroute-howto-circuit-portal-resource-manager.md) 변경하려는 기존 회로로 이동합니다.

    :::image type="content" source="./media/expressroute-howto-add-ipv6-portal/navigate-to-circuit.png" alt-text="ExpressRoute 회로 목록의 스크린샷.":::

1. **Azure 프라이빗** 피어링 구성을 선택합니다.

    :::image type="content" source="./media/expressroute-howto-add-ipv6-portal/navigate-to-peering.png" alt-text="ExpressRoute 개요 페이지의 스크린샷.":::

1. **서브넷** 에 대해 "모두"를 선택하여 기존 IPv4 개인 피어링 구성에 IPv6 개인 피어링을 추가하거나, "IPv6"를 선택하여 새 회로에서 IPv6 개인 피어링만 사용하도록 설정합니다. 기본 링크 및 보조 링크에 대해 소유하고 있는 /126 IPv6 서브넷 한 쌍을 제공합니다. 이러한 각 서브넷에서는 Microsoft에서 사용 가능한 첫 번째 IP 주소를 해당 라우터의 두 번째 사용 가능한 IP로 사용하므로 이를 라우터로 할당합니다. 모든 매개 변수를 지정한 후에 피어링 구성을 **저장** 합니다.

    :::image type="content" source="./media/expressroute-howto-add-ipv6-portal/add-ipv6-peering.png" alt-text="개인 피어링 페이지에서 Ipv6를 추가하는 스크린샷.":::

1. 구성이 성공적으로 수락되고 나면 다음 예와 유사한 내용을 볼 수 있습니다.

    :::image type="content" source="./media/expressroute-howto-add-ipv6-portal/view-ipv6-peering.png" alt-text="개인 피어링용으로 구성된 Ipv6 스크린샷.":::

## <a name="update-your-connection-to-an-existing-virtual-network"></a>기존 가상 네트워크에 대한 연결 업데이트

IPv6 개인 피어링을 사용하려는 가용성 영역이 있는 지역에 Azure 리소스의 기존 환경이 있는 경우 다음 단계를 수행합니다.

1. ExpressRoute 회로가 연결된 가상 네트워크로 이동합니다.

    :::image type="content" source="./media/expressroute-howto-add-ipv6-portal/navigate-to-vnet.png" alt-text="가상 네트워크 목록의 스크린샷.":::

1. **주소 공간** 탭으로 이동하여 가상 네트워크에 IPv6 주소 공간을 추가합니다. 주소 공간을 **저장** 합니다.

    :::image type="content" source="./media/expressroute-howto-add-ipv6-portal/add-ipv6-space.png" alt-text="가상 네트워크에 Ipv6 주소 공간을 추가하는 스크린샷.":::

1. **서브넷** 탭으로 이동하여 **게이트웨이 서브넷** 을 선택합니다. **IPv6 주소 공간 추가** 를 선택하고 서브넷에 대한 IPv6 주소 공간을 제공합니다. 게이트웨이 IPv6 서브넷은/64 이상이어야 합니다. 모든 매개 변수를 지정한 후에 구성을 **저장** 합니다.

    :::image type="content" source="./media/expressroute-howto-add-ipv6-portal/add-ipv6-gateway-space.png" alt-text="서브넷에 Ipv6 주소 공간을 추가하는 스크린샷.":::

1. 기존 영역 중복 게이트웨이가 있는 경우, ExpressRoute 게이트웨이로 이동하고 리소스를 새로 고쳐 IPv6 연결을 사용하도록 설정합니다. 그렇지 않으면 영역 중복 SKU(ErGw1AZ, ErGw2AZ, ErGw3AZ)를 사용하여 [가상 네트워크 게이트웨이를 만듭니다](expressroute-howto-add-gateway-portal-resource-manager.md). FastPath를 사용하려는 경우, ErGw3AZ를 사용합니다.

    :::image type="content" source="./media/expressroute-howto-add-ipv6-portal/refresh-gateway.png" alt-text="게이트웨이를 새로 고치는 스크린샷.":::

## <a name="create-a-connection-to-a-new-virtual-network"></a>새 가상 네트워크에 대한 연결 만들기

IPv6 개인 피어링을 사용하여 가용성 영역이 있는 지역에서 새 Azure 리소스 세트에 연결하려는 경우, 다음 단계를 수행합니다.

1. IPv4 및 IPv6 주소 공간을 모두 사용하여 이중 스택 가상 네트워크를 만듭니다. 자세한 내용은 [가상 네트워크 만들기](../virtual-network/quick-create-portal.md#create-a-virtual-network)를 참조하세요.

1. [이중 스택 게이트웨이 서브넷 만들기](expressroute-howto-add-gateway-portal-resource-manager.md#create-the-gateway-subnet).

1. 영역 중복 SKU(ErGw1AZ, ErGw2AZ, ErGw3AZ)를 사용하여 [가상 네트워크 게이트웨이를 만듭니다](expressroute-howto-add-gateway-portal-resource-manager.md#create-the-virtual-network-gateway). FastPath를 사용하려는 경우, ErGw3AZ를 사용 합니다(이 옵션은 ExpressRoute Direct를 사용하는 회로에만 사용할 수 있음).

1. [가상 네트워크를 ExpressRoute 회로에 연결](expressroute-howto-linkvnet-portal-resource-manager.md).

## <a name="limitations"></a>제한 사항
IPv6는 가용성 영역이 있는 지역에서 배포에 연결하는 데 사용할 수 있지만 다음과 같은 사용 사례를 지원하지 않습니다.

* 비AZ ExpressRoute 게이트웨이 SKU를 통해 Azure의 배포에 연결
* 비AZ 지역의 배포에 대한 연결
* ExpressRoute 회로 간의 Global Reach 연결
* 가상 WAN으로 ExpressRoute 사용
* 비ExpressRoute Direct 회로가 포함된 FastPath
* 다음 피어링 위치에 회로가 있는 FastPath: 두바이
* VPN Gateway와 함께 사용

## <a name="next-steps"></a>다음 단계

ExpressRoute 문제를 해결하려면 다음 문서를 참조하세요.

* [ExpressRoute 연결 확인](expressroute-troubleshooting-expressroute-overview.md)
* [네트워크 성능 문제 해결](expressroute-troubleshooting-network-performance.md)