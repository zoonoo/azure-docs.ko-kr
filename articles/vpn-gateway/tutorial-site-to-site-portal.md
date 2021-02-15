---
title: '자습서 - 온-프레미스 네트워크를 가상 네트워크에 연결: Azure portal'
description: 포털을 사용하여 공용 인터넷을 통해 온-프레미스 네트워크에서 Azure 가상 네트워크에 사이트 간 VPN Gateway IPsec 연결을 만듭니다.
titleSuffix: Azure VPN Gateway
author: cherylmc
ms.author: cherylmc
ms.service: vpn-gateway
ms.topic: tutorial
ms.date: 12/04/2020
ms.openlocfilehash: ccb43c3e7efb9289450ad9a71c003f54e5362b66
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/28/2021
ms.locfileid: "99822204"
---
# <a name="tutorial-create-a-site-to-site-connection-in-the-azure-portal"></a>자습서: Azure Portal에서 사이트 간 연결 만들기

Azure VPN 게이트웨이는 고객 프레미스와 Azure 사이에 프레미스 간 연결을 제공합니다. 이 자습서에서는 Azure Portal을 사용하여 온-프레미스 네트워크에서 VNet으로 사이트 간 VPN Gateway 연결을 만드는 방법을 보여줍니다. [Azure PowerShell](vpn-gateway-create-site-to-site-rm-powershell.md) 또는 [Azure CLI](vpn-gateway-howto-site-to-site-resource-manager-cli.md)를 사용하여 이 구성을 만들 수도 있습니다.

:::image type="content" source="./media/tutorial-site-to-site-portal/diagram.png" alt-text="사이트 간 VPN Gateway 크로스-프레미스 연결 다이어그램":::

이 자습서에서는 다음 작업 방법을 알아봅니다.

> [!div class="checklist"]
> * 가상 네트워크 만들기
> * VPN 게이트웨이 만들기
> * 로컬 네트워크 게이트웨이 만들기
> * VPN 연결 만들기
> * 연결 확인
> * 가상 머신에 연결

## <a name="prerequisites"></a>필수 구성 요소

* 활성 구독이 있는 Azure 계정. 계정이 없는 경우 [체험 계정을 만듭니다](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
* 호환되는 VPN 디바이스 및 이 디바이스를 구성할 수 있는 사람이 있는지 확인합니다. 호환되는 VPN 디바이스 및 디바이스 구성에 대한 자세한 내용은 [VPN 디바이스 정보](vpn-gateway-about-vpn-devices.md)를 참조하세요.
* VPN 디바이스에 대한 외부 연결 공용 IPv4 주소가 있는지 확인합니다.
* 온-프레미스 네트워크에 있는 IP 주소 범위에 익숙하지 않은 경우 세부 정보를 제공할 수 있는 다른 사람의 도움을 받아야 합니다. 이 구성을 만들 때 Azure가 온-프레미스 위치에 라우팅할 IP 주소 범위 접두사를 지정해야 합니다. 온-프레미스 네트워크의 어떤 서브넷도 사용자가 연결하려는 가상 네트워크 서브넷과 중첩될 수 없습니다.

## <a name="create-a-virtual-network"></a><a name="CreatVNet"></a>가상 네트워크 만들기

다음 값을 사용하여 VNet(가상 네트워크)을 만듭니다.

* **리소스 그룹:** TestRG1
* **이름:** VNet1
* **지역:** (미국) 미국 동부
* **IPv4 주소 공간:** 10.1.0.0/16
* **서브넷 이름:** FrontEnd
* **서브넷 주소 공간:** 10.1.0.0/24

[!INCLUDE [About cross-premises addresses](../../includes/vpn-gateway-cross-premises.md)]

[!INCLUDE [Create a virtual network](../../includes/vpn-gateway-basic-vnet-rm-portal-include.md)]

## <a name="create-a-vpn-gateway"></a><a name="VNetGateway"></a>VPN 게이트웨이 만들기

이 단계에서는 VNet용 가상 네트워크 게이트웨이를 만듭니다. 종종 선택한 게이트웨이 SKU에 따라 게이트웨이를 만드는 데 45분 이상 걸릴 수 있습니다.

### <a name="about-the-gateway-subnet"></a>게이트웨이 서브넷 정보

[!INCLUDE [About gateway subnets](../../includes/vpn-gateway-about-gwsubnet-portal-include.md)]

### <a name="create-the-gateway"></a>게이트웨이 만들기

다음 값을 사용하여 VPN 게이트웨이를 만듭니다.

* **이름:** VNet1GW
* **지역:** 미국 동부
* **게이트웨이 유형:** VPN
* **VPN 유형:** 경로 기반
* **SKU:** VpnGw1
* **생성:** Generation1
* **가상 네트워크:** VNet1
* **게이트웨이 서브넷 주소 범위:** 10.1.255.0/27
* **공용 IP 주소:** 새로 만들기
* **공용 IP 주소 이름:** VNet1GWpip
* **active-active 모드를 사용하도록 설정:** 사용 안 함
* **BGP 구성:** 사용 안 함

[!INCLUDE [Create a vpn gateway](../../includes/vpn-gateway-add-gw-rm-portal-include.md)]

[!INCLUDE [NSG warning](../../includes/vpn-gateway-no-nsg-include.md)]

### <a name="view-the-public-ip-address"></a><a name="view"></a>공용 IP 주소 보기

게이트웨이의 **개요** 페이지에서 게이트웨이 공용 IP 주소를 볼 수 있습니다.

:::image type="content" source="./media/tutorial-create-gateway-portal/address.png" alt-text="개요 페이지":::

공용 IP 주소 개체에 대한 추가 정보를 보려면 **공용 IP 주소** 옆에 있는 이름/IP 주소 링크를 클릭합니다.

## <a name="create-a-local-network-gateway"></a><a name="LocalNetworkGateway"></a>로컬 네트워크 게이트웨이 만들기

로컬 네트워크 게이트웨이는 라우팅 목적으로 온-프레미스 위치(사이트)를 나타내는 특정 개체입니다. Azure가 참조할 수 있는 사이트 이름을 지정한 다음, 연결을 만들 온-프레미스 VPN 디바이스의 IP 주소를 지정합니다. 또한 VPN Gateway를 통해 VPN 디바이스로 라우팅될 IP 주소 접두사를 지정합니다. 사용자가 지정하는 주소 접두사는 온-프레미스 네트워크에 있는 접두사입니다. 온-프레미스 네트워크가 변경되거나 VPN 디바이스에서 공용 IP 주소를 변경해야 하는 경우 나중에 값을 쉽게 업데이트할 수 있습니다.

다음 값을 사용하여 로컬 네트워크 게이트웨이를 만듭니다.

* **이름:** Site1
* **리소스 그룹:** TestRG1
* **위치:** 미국 동부

[!INCLUDE [Add a local network gateway](../../includes/vpn-gateway-add-local-network-gateway-portal-include.md)]

## <a name="configure-your-vpn-device"></a><a name="VPNDevice"></a>VPN 디바이스 구성

온-프레미스 네트워크에 대한 사이트 간 연결에는 VPN 디바이스가 필요합니다. 이 단계에서는 VPN 디바이스를 구성합니다. VPN 디바이스를 구성할 때 다음 값이 필요합니다.

* 공유 키 - 사이트 간 VPN 연결을 만들 때 지정하는 것과 동일한 공유 키입니다. 이 예제에서는 기본적인 공유 키를 사용합니다. 실제로 사용할 키는 좀 더 복잡하게 생성하는 것이 좋습니다.
* 가상 네트워크 게이트웨이의 공용 IP 주소 Azure Portal, PowerShell 또는 CLI를 사용하여 공용 IP 주소를 볼 수 있습니다. Azure Portal을 사용하여 VPN 게이트웨이의 공용 IP 주소를 찾으려면 **가상 네트워크 게이트웨이** 로 이동한 다음, 게이트웨이의 이름을 선택합니다.

[!INCLUDE [Configure a VPN device](../../includes/vpn-gateway-configure-vpn-device-include.md)]

## <a name="create-a-vpn-connection"></a><a name="CreateConnection"></a>VPN 연결 만들기

가상 네트워크 게이트웨이와 온-프레미스 VPN 디바이스 사이의 사이트 간 VPN 연결을 만듭니다.

다음 값을 사용하여 연결을 만듭니다.

* **로컬 네트워크 게이트웨이 이름:** Site1
* **연결 이름:** VNet1toSite1
* **공유 키:** 이 예제에서는 abc123을 사용합니다. 그러나 VPN 하드웨어와 호환이 되는 것이면 무엇이든 사용할 수 있습니다. 중요한 점은 값이 연결의 양쪽 모두에 일치합니다.

[!INCLUDE [Add a site-to-site connection](../../includes/vpn-gateway-add-site-to-site-connection-portal-include.md)]

## <a name="verify-the-vpn-connection"></a><a name="VerifyConnection"></a>VPN 연결 확인

[!INCLUDE [Verify the connection](../../includes/vpn-gateway-verify-connection-portal-include.md)]

## <a name="connect-to-a-virtual-machine"></a><a name="connectVM"></a>가상 머신에 연결

[!INCLUDE [Connect to a VM](../../includes/vpn-gateway-connect-vm.md)]

## <a name="optional-steps"></a>선택적 단계

### <a name="add-additional-connections-to-the-gateway"></a><a name="addconnect"></a>게이트웨이에 추가 연결 추가

어떤 주소 공간도 연결 간에 겹치지 않는다면 추가 연결을 추가할 수 있습니다.

1. 추가 연결을 추가하려면 VPN 게이트웨이로 이동한 다음, **연결** 을 선택하여 연결 페이지를 엽니다.
1. 사용자 연결을 추가하려면 **+추가** 를 선택합니다. VNet 간(다른 VNet 게이트웨이에 연결하는 경우) 또는 사이트 간을 reflect하려면 연결 유형을 조정합니다.
1. 사이트 간을 이용하여 연결하고 연결하려는 사이트에 대한 로컬 네트워크 게이트웨이를 아직 만들지 않았다면, 새 게이트웨이를 만들 수 있습니다.
1. 사용하려는 공유 키를 지정한 다음, **확인** 을 선택하여 연결을 만듭니다.

### <a name="resize-a-gateway-sku"></a><a name="resize"></a>게이트웨이 SKU 크기 조정

크기 조정 및 게이트웨이 SKU 변경과 관련된 특정 규칙이 있습니다. 이 섹션에서는 SKU의 크기를 조정합니다. 자세한 내용은 [게이트웨이 설정 - SKU 크기 조정 및 변경](vpn-gateway-about-vpn-gateway-settings.md#resizechange)을 참조하세요.

[!INCLUDE [resize a gateway](../../includes/vpn-gateway-resize-gw-portal-include.md)]

### <a name="reset-a-gateway"></a><a name="reset"></a>게이트웨이 다시 설정

Azure VPN Gateway 재설정은 하나 이상의 사이트 간 VPN 터널에서 크로스-프레미스 VPN 연결이 손실되는 경우에 유용합니다. 이 상황에서 온-프레미스 VPN 디바이스는 모두 올바르게 작동하지만 Azure VPN 게이트웨이와 IPsec 터널을 설정할 수 없습니다.

[!INCLUDE [reset a gateway](../../includes/vpn-gateway-reset-gw-portal-include.md)]

### <a name="additional-configuration-considerations"></a><a name="additional"></a>추가 구성 고려 사항

S2S 구성은 다양한 방법으로 사용자 지정할 수 있습니다. 자세한 내용은 다음 문서를 참조하세요.

* BGP에 대한 내용은 [BGP 개요](vpn-gateway-bgp-overview.md) 및 [BGP를 구성하는 방법](vpn-gateway-bgp-resource-manager-ps.md)을 참조하세요.
* 강제 터널링에 대한 내용은 [강제 터널링 정보](vpn-gateway-forced-tunneling-rm.md)를 참조하세요.
* 항상 사용 가능한 활성/활성 연결에 대한 정보는 [항상 사용 가능한 크로스-프레미스 및 VNet 간 연결](vpn-gateway-highlyavailable.md)을 참조하세요.
* 가상 네트워크에서 리소스에 네트워크 트래픽을 제한하는 방법에 관한 정보는 [네트워크 보안 그룹](../virtual-network/network-security-groups-overview.md)을 참조하세요.
* Azure에서 트래픽을 Azure, 온-프레미스 및 인터넷 리소스간에 라우팅하는 방법에 대한 정보는 [가상 네트워크 트래픽 라우팅](../virtual-network/virtual-networks-udr-overview.md)을 참조하세요.

## <a name="clean-up-resources"></a>리소스 정리

이 애플리케이션을 계속 사용하지 않거나 다음 자습서로 이동하지 않으려면 다음 단계에 따라 해당 리소스를 삭제합니다.

1. 포털 위쪽의 **검색** 상자에서 리소스 그룹 이름을 입력하고, 검색 결과에서 이를 선택합니다.

1. **리소스 그룹 삭제** 를 선택합니다.

1. **리소스 그룹 이름 입력** 에 대해 리소스 그룹을 입력하고, **삭제** 를 선택합니다.

## <a name="next-steps"></a>다음 단계

S2S 연결을 구성한 후에는 동일한 게이트웨이에 P2S 연결을 추가할 수 있습니다.

> [!div class="nextstepaction"]
> [지점 및 사이트 간 VPN 연결](vpn-gateway-howto-point-to-site-resource-manager-portal.md)
