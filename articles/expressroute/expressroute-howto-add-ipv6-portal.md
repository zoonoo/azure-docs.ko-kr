---
title: 'Azure Express 경로: Azure Portal을 사용 하 여 IPv6 지원 추가'
description: Azure Portal를 사용 하 여 Azure 배포에 연결 하기 위해 IPv6 지원을 추가 하는 방법을 알아봅니다.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 03/09/2021
ms.author: duau
ms.openlocfilehash: 9926102a2e6b25060c2a8840b56d690ce2868ade
ms.sourcegitcommit: d135e9a267fe26fbb5be98d2b5fd4327d355fe97
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/10/2021
ms.locfileid: "102618797"
---
# <a name="add-ipv6-support-for-private-peering-using-the-azure-portal-preview"></a>Azure Portal를 사용 하 여 개인 피어 링에 대 한 IPv6 지원 추가 (미리 보기)

이 문서에서는 Azure Portal를 사용 하 여 Azure에서 Express 경로를 통해 Azure의 리소스에 연결 하기 위해 IPv6 지원을 추가 하는 방법을 설명 합니다. 

> [!Note]
> 이 기능은 현재 [가용성 영역를 사용 하 여 Azure 지역](https://docs.microsoft.com/azure/availability-zones/az-region#azure-regions-with-availability-zones)에서 미리 보기에 사용할 수 있습니다. 따라서 Express 경로 회로를 피어 링 위치를 사용 하 여 만들 수 있지만 연결 하는 IPv6 기반 배포는 가용성 영역 있는 지역에 있어야 합니다.

## <a name="register-for-public-preview"></a>공개 미리 보기 등록
IPv6 지원을 추가 하기 전에 먼저 구독을 등록 해야 합니다. 등록 하려면 Azure PowerShell를 통해 다음 명령을 실행 합니다.

1.  Azure에 로그인 하 고 구독을 선택 합니다. Express 경로 회로를 포함 하는 구독 및 Azure 배포가 포함 된 구독에 대해 이러한 명령을 실행 합니다 (다른 경우).

    ```azurepowershell-interactive
    Connect-AzAccount 

    Select-AzSubscription -Subscription "<SubscriptionID or SubscriptionName>"
    ```

1. 다음 명령을 사용 하 여 공개 미리 보기에 대 한 구독을 등록 합니다.
    ```azurepowershell-interactive
    Register-AzProviderFeature -FeatureName AllowIpv6PrivatePeering -ProviderNamespace Microsoft.Network
    ```

그러면 2-3 영업일 이내에 Express 경로 팀에서 요청을 승인 하 게 됩니다.

## <a name="sign-in-to-the-azure-portal"></a>Azure Portal에 로그인

브라우저에서 [Azure Portal](https://portal.azure.com)으로 이동한 다음 Azure 계정으로 로그인 합니다.

## <a name="add-ipv6-private-peering-to-your-expressroute-circuit"></a>Express 경로 회로에 IPv6 개인 피어 링 추가

1. [Express 경로 회로를 만들거나](expressroute-howto-circuit-portal-resource-manager.md) 변경 하려는 기존 회로로 이동 합니다.

    :::image type="content" source="./media/expressroute-howto-add-ipv6-portal/navigate-to-circuit.png" alt-text="Express 경로 회로 목록의 스크린샷.":::

1. **Azure 개인** 피어 링 구성을 선택 합니다.

    :::image type="content" source="./media/expressroute-howto-add-ipv6-portal/navigate-to-peering.png" alt-text="Express 경로 개요 페이지의 스크린샷":::

1. **서브넷** 에 대해 "모두"를 선택 하 여 기존 IPv4 개인 피어 링 구성에 Ipv6 개인 피어 링을 추가 하거나 "ipv6"을 선택 하 여 새 회로에서 Ipv6 개인 피어 링만 사용 하도록 설정 합니다. 기본 링크 및 보조 링크에 대해 소유 하 고 있는/126 IPv6 서브넷을 제공 합니다. 이러한 각 서브넷에서 사용 가능한 첫 번째 IP 주소를 라우터에 할당 합니다. Microsoft는 라우터에 사용 가능한 두 번째 IP를 사용 합니다. 모든 매개 변수를 지정 하면 피어 링 구성을 **저장** 합니다.

    :::image type="content" source="./media/expressroute-howto-add-ipv6-portal/add-ipv6-peering.png" alt-text="개인 피어 링 페이지에서 Ipv6을 추가 하는 스크린샷":::

1. 구성이 성공적으로 수락 되 면 다음 예제와 유사한 내용이 표시 되어야 합니다.

    :::image type="content" source="./media/expressroute-howto-add-ipv6-portal/view-ipv6-peering.png" alt-text="개인 피어 링 용으로 구성 된 Ipv6 스크린샷.":::

## <a name="update-your-connection-to-an-existing-virtual-network"></a>기존 가상 네트워크에 대 한 연결 업데이트

에서 IPv6 개인 피어 링을 사용 하려는 가용성 영역 있는 지역에 Azure 리소스의 기존 환경이 있는 경우 아래 단계를 수행 합니다.

1. Express 경로 회로가 연결 된 가상 네트워크로 이동 합니다.

    :::image type="content" source="./media/expressroute-howto-add-ipv6-portal/navigate-to-vnet.png" alt-text="가상 네트워크 목록의 스크린샷.":::

1. **주소 공간** 탭으로 이동 하 여 가상 네트워크에 IPv6 주소 공간을 추가 합니다. 주소 공간을 **저장** 합니다.

    :::image type="content" source="./media/expressroute-howto-add-ipv6-portal/add-ipv6-space.png" alt-text="가상 네트워크에 Ipv6 주소 공간 추가의 스크린샷":::

1. **서브넷** 탭으로 이동 하 여 **게이트웨이 서브넷** 을 선택 합니다. **Ipv6 주소 공간 추가** 를 선택 하 고 서브넷에 대 한 ipv6 주소 공간을 제공 합니다. 게이트웨이 IPv6 서브넷은/64 이상 이어야 합니다. 모든 매개 변수를 지정한 후에 구성을 **저장** 합니다.

    :::image type="content" source="./media/expressroute-howto-add-ipv6-portal/add-ipv6-gateway-space.png" alt-text="서브넷에 Ipv6 주소 공간 추가의 스크린샷":::

1. 기존 영역 중복 게이트웨이가 있는 경우 Express 경로 게이트웨이로 이동 하 고 리소스를 새로 고쳐 IPv6 연결을 사용 하도록 설정 합니다. 그렇지 않으면 영역 중복 SKU (ErGw1AZ, ErGw2AZ, ErGw3AZ)를 사용 하 여 [가상 네트워크 게이트웨이를 만듭니다](expressroute-howto-add-gateway-portal-resource-manager.md) . FastPath를 사용 하려는 경우 ErGw3AZ를 사용 합니다.

    :::image type="content" source="./media/expressroute-howto-add-ipv6-portal/refresh-gateway.png" alt-text="게이트웨이를 새로 고치는 스크린샷":::

## <a name="create-a-connection-to-a-new-virtual-network"></a>새 가상 네트워크에 대 한 연결 만들기

IPv6 개인 피어 링을 사용 하 여 가용성 영역 있는 지역에서 새 Azure 리소스 집합에 연결 하려는 경우 아래 단계를 수행 합니다.

1. IPv4 및 IPv6 주소 공간을 모두 사용 하 여 이중 스택 가상 네트워크를 만듭니다. 자세한 내용은 [가상 네트워크 만들기](../virtual-network/quick-create-portal.md#create-a-virtual-network)를 참조 하세요.

1. [이중 스택 게이트웨이 서브넷을 만듭니다](expressroute-howto-add-gateway-portal-resource-manager.md#create-the-gateway-subnet).

1. 영역 중복 SKU (ErGw1AZ, ErGw2AZ, ErGw3AZ)를 사용 하 여 [가상 네트워크 게이트웨이를 만듭니다](expressroute-howto-add-gateway-portal-resource-manager.md#create-the-virtual-network-gateway) . FastPath를 사용 하려는 경우 ErGw3AZ를 사용 합니다 .이 옵션은 Express 경로 직접를 사용 하는 회로에만 사용할 수 있습니다.

1. [Express 경로 회로에 가상 네트워크를 연결](expressroute-howto-linkvnet-portal-resource-manager.md)합니다.

## <a name="limitations"></a>제한 사항
I p v 6은 가용성 영역 있는 지역에서 배포에 연결 하는 데 사용할 수 있지만 다음과 같은 사용 사례를 지원 하지 않습니다.

* AZ Express 경로 게이트웨이 SKU를 통해 Azure의 배포에 연결
* 비 AZ regions의 배포에 대 한 연결
* Express 경로 회로 간의 Global Reach 연결
* 가상 WAN과 함께 Express 경로 사용
* 비-Express 경로 직접 회로가 포함 된 FastPath
* VPN Gateway과 함께 사용

## <a name="next-steps"></a>다음 단계

Express 경로 문제를 해결 하려면 다음 문서를 참조 하세요.

* [ExpressRoute 연결 확인](expressroute-troubleshooting-expressroute-overview.md)
* [네트워크 성능 문제 해결](expressroute-troubleshooting-network-performance.md)
