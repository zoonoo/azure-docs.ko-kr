---
title: 가상 네트워크 게이트웨이를 Azure 가상 WAN에 연결
description: 이 문서에서는 Azure 가상 네트워크 게이트웨이를 Azure 가상 WAN VPN 게이트웨이에 연결하는 데 도움이 됩니다.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 03/19/2020
ms.author: cherylmc
ms.openlocfilehash: 688183bc07aa14d5e5df182d7de0897cec93f0b9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80066243"
---
# <a name="connect-a-vpn-gateway-virtual-network-gateway-to-virtual-wan"></a>VPN 게이트웨이(가상 네트워크 게이트웨이)를 가상 WAN에 연결

이 문서에서는 Azure VPN 게이트웨이(가상 네트워크 게이트웨이)에서 Azure 가상 WAN(VPN 게이트웨이)에 대한 연결을 설정하는 데 도움이 됩니다. VPN 게이트웨이(가상 네트워크 게이트웨이)에서 가상 WAN(VPN 게이트웨이)으로 연결하는 것은 지점 VPN 사이트에서 가상 WAN에 대한 연결을 설정하는 것과 유사합니다.

두 기능 간의 혼동을 최소화하기 위해 참조하는 기능의 이름으로 게이트웨이앞에 서두를 것입니다. 예를 들어 VPN 게이트웨이 가상 네트워크 게이트웨이 및 가상 WAN VPN 게이트웨이입니다.

## <a name="before-you-begin"></a>시작하기 전에

시작하기 전에 다음 리소스를 만듭니다.

Azure 가상 WAN

* [가상 WAN 을 만듭니다.](virtual-wan-site-to-site-portal.md#openvwan)
* [허브 를 만듭니다.](virtual-wan-site-to-site-portal.md#hub) 가상 허브에는 가상 WAN VPN 게이트웨이가 포함되어 있습니다.

Azure Virtual Network

* 가상 네트워크 게이트웨이 없이 가상 네트워크를 만듭니다. 온-프레미스 네트워크의 어떤 서브넷도 연결하려는 가상 네트워크 서브넷과 중첩되지 않는지 확인합니다. Azure Portal에서 가상 네트워크를 만들려면 [빠른 시작](../virtual-network/quick-create-portal.md)을 참조하세요.

## <a name="1-create-an-azure-virtual-network-gateway"></a><a name="vnetgw"></a>1. Azure 가상 네트워크 게이트웨이 만들기

가상 네트워크에 대한 활성 모드에서 가상 네트워크에 대한 VPN 게이트웨이 가상 네트워크 게이트웨이를 만듭니다. 게이트웨이를 만들 때 게이트웨이의 두 인스턴스에 대해 기존 공용 IP 주소를 사용하거나 새 공용 IP를 만들 수 있습니다. 가상 WAN 사이트를 설정할 때 이러한 공용 IP를 사용합니다. 활성 모드에 대한 자세한 내용은 [활성-활성 연결 구성을](../vpn-gateway/vpn-gateway-activeactive-rm-powershell.md#aagateway)참조하십시오.

### <a name="active-active-mode-setting"></a><a name="active-active"></a>액티브 모드 설정

![활성 활성](./media/connect-virtual-network-gateway-vwan/active.png "액티브-액티브")

### <a name="bgp-setting"></a><a name="BGP"></a>BGP 설정

BGP ASN은 65515일 수 없습니다. 66515는 Azure 가상 WAN에서 사용됩니다.

![BGP](./media/connect-virtual-network-gateway-vwan/bgp.png "Bgp")

### <a name="public-ip-addresses"></a><a name="pip"></a>공용 IP 주소

게이트웨이가 만들어지면 **속성** 페이지로 이동합니다. 속성 및 구성 설정은 다음 예제와 유사합니다. 게이트웨이에 사용되는 두 개의 공용 IP 주소를 확인합니다.

![속성](./media/connect-virtual-network-gateway-vwan/publicip.png "properties")

## <a name="2-create-virtual-wan-vpn-sites"></a><a name="vwansite"></a>2. 가상 WAN VPN 사이트 만들기

가상 WAN VPN 사이트를 만들려면 가상 WAN으로 이동하여 **연결에서** **VPN 사이트를**선택합니다. 이 섹션에서는 이전 섹션에서 만든 가상 네트워크 게이트웨이에 해당하는 두 개의 가상 WAN VPN 사이트를 만듭니다.

1. **+사이트 만들기를**선택합니다.
2. VPN **사이트 만들기** 페이지에서 다음 값을 입력합니다.

   * **지역** - (Azure VPN 게이트웨이 가상 네트워크 게이트웨이와 동일한 지역)
   * **장치 공급업체** - 장치 공급업체(이름 포함)
   * **개인 주소 공간** - (값을 입력하거나 BGP가 활성화된 경우 비워 둡니다)
   * **테두리 게이트웨이 프로토콜** - (Azure VPN 게이트웨이 가상 네트워크 게이트웨이가 BGP를 사용하도록 설정한 경우 **사용하도록** 설정)
   * **허브에** 연결(드롭다운에서 필수 구성 에서 만든 허브 선택)
3. **링크**에서 다음 값을 입력합니다.

   * **공급자 이름** - 링크 이름과 공급자 이름(모든 이름)을 입력합니다.
   * **속도** - 속도 (임의의 숫자)
   * **IP 주소** - IP 주소(VPN 게이트웨이) 가상 네트워크 게이트웨이 속성 아래에 표시된 첫 번째 공용 IP 주소와 동일) 입력
   * **BGP 주소** 및 **ASN** - BGP 주소 및 ASN. [1단계에서](#vnetgw)구성한 VPN 게이트웨이 가상 네트워크 게이트웨이의 BGP 피어 IP 주소 및 ASN과 동일해야 합니다.
4. 사이트를 만들려면 **확인을** 검토하고 선택합니다.
5. 이전 단계를 반복하여 VPN 게이트웨이 가상 네트워크 게이트웨이의 두 번째 인스턴스와 일치하는 두 번째 사이트를 만듭니다. VPN 게이트웨이 구성에서 두 번째 공용 IP 주소와 두 번째 BGP 피어 IP 주소를 사용하는 경우를 제외하고 동일한 설정을 유지합니다.
6. 이제 두 개의 사이트가 성공적으로 프로비전되었으며 다음 섹션으로 이동하여 구성 파일을 다운로드할 수 있습니다.

## <a name="3-download-the-vpn-configuration-files"></a><a name="downloadconfig"></a>3. VPN 구성 파일 다운로드

이 섹션에서는 이전 섹션에서 만든 각 사이트에 대한 VPN 구성 파일을 다운로드합니다.

1. 가상 WAN **VPN 사이트** 페이지 상단에서 **사이트를**선택한 다음 사이트 간 VPN **구성 다운로드를 선택합니다.** Azure는 설정이 있는 구성 파일을 만듭니다.

   ![구성 파일 다운로드](./media/connect-virtual-network-gateway-vwan/download.png "다운로드로 사용 가능한 제품 설명서에서 데이터 공급자 설치 섹션을 참조하세요")
2. 구성 파일을 다운로드하여 엽니다.
3. 두 번째 사이트에 대해 다음 단계를 반복합니다. 두 구성 파일이 모두 열리면 다음 섹션으로 진행할 수 있습니다.

## <a name="4-create-the-local-network-gateways"></a><a name="createlocalgateways"></a>4. 로컬 네트워크 게이트웨이 만들기

이 섹션에서는 두 개의 Azure VPN 게이트웨이 로컬 네트워크 게이트웨이를 만듭니다. 이전 단계의 구성 파일에는 게이트웨이 구성 설정이 포함되어 있습니다. 이러한 설정을 사용하여 Azure VPN 게이트웨이 로컬 네트워크 게이트웨이를 만들고 구성합니다.

1. 이러한 설정을 사용하여 로컬 네트워크 게이트웨이를 만듭니다. VPN 게이트웨이 로컬 네트워크 게이트웨이를 만드는 방법에 대한 자세한 내용은 VPN 게이트웨이 문서 [로컬 네트워크 게이트웨이 만들기](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md#LocalNetworkGateway)를 참조하십시오.

   * **IP 주소** - 구성 파일에서 *게이트웨이 구성에* 대해 표시된 Instance0 IP 주소를 사용합니다.
   * **BGP** - 연결이 BGP를 초과한 경우 **BGP 설정 구성을** 선택하고 ASN '65515'를 입력합니다. BGP 피어 IP 주소를 입력합니다. 구성 파일에서 *게이트웨이 구성에* '인스턴스0 BgpPeering주소'를 사용합니다.
   * **구독, 리소스 그룹 및 위치는** 가상 WAN 허브와 동일합니다.
2. 로컬 네트워크 게이트웨이를 검토하고 만듭니다. 로컬 네트워크 게이트웨이는 이 예제와 유사해야 합니다.

   ![구성 파일 다운로드](./media/connect-virtual-network-gateway-vwan/lng1.png "인스턴스 0")
3. 이 단계를 반복하여 다른 로컬 네트워크 게이트웨이를 만들지만 이번에는 구성 파일에서 'Instance0' 값 대신 'Instance1' 값을 사용합니다.

   ![구성 파일 다운로드](./media/connect-virtual-network-gateway-vwan/lng2.png "instance1")

## <a name="5-create-connections"></a><a name="createlocalgateways"></a>5. 연결 만들기

이 섹션에서는 VPN 게이트웨이 로컬 네트워크 게이트웨이와 가상 네트워크 게이트웨이 간의 연결을 만듭니다. VPN 게이트웨이 연결을 만드는 방법에 대한 단계는 [연결 구성을](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md#CreateConnection)참조하십시오.

1. 포털에서 가상 네트워크 게이트웨이로 이동하여 **연결을**클릭합니다. 연결 페이지의 맨 위에 있는 **+추가**를 클릭하여 **연결 추가** 페이지를 엽니다.
2. 연결 **추가** 페이지에서 연결에 대해 다음 값을 구성합니다.

   * **이름:** 연결의 이름을 지정합니다.
   * **연결 유형:** **IPSec(사이트 간)** 선택
   * **가상 네트워크 게이트웨이:** 이 게이트웨이에서 연결하기 때문에 값이 고정됩니다.
   * **로컬 네트워크 게이트웨이:** 이 연결은 가상 네트워크 게이트웨이를 로컬 네트워크 게이트웨이에 연결합니다. 이전에 만든 로컬 네트워크 게이트웨이 중 하나를 선택합니다.
   * **공유 키:** 공유 키를 입력합니다.
   * **IKE 프로토콜:** IKE 프로토콜을 선택합니다.
   * **BGP:** 연결이 **BGP를** 초과한 경우 BGP 활성화를 선택합니다.
3. **확인** 을 클릭하여 연결을 만듭니다.
4. 가상 네트워크 게이트웨이의 **연결** 페이지에서 연결을 볼 수 있습니다.

   ![연결](./media/connect-virtual-network-gateway-vwan/connect.png "연결")
5. 위의 단계를 반복하여 두 번째 연결을 만듭니다. 두 번째 연결의 경우 만든 다른 로컬 네트워크 게이트웨이를 선택합니다.

## <a name="6-test-connections"></a><a name="test"></a>6. 테스트 연결

VPN 게이트웨이 가상 네트워크 게이트웨이 측면에 하나씩, 가상 WAN용 가상 네트워크에 하나씩 두 개의 가상 컴퓨터를 만든 다음 두 개의 가상 컴퓨터를 ping하여 연결을 테스트할 수 있습니다.

1. Azure VPN 게이트웨이(Test1-VNG)에 대한 가상 네트워크(Test1-VNet)에서 가상 컴퓨터를 만듭니다. 게이트웨이서브넷에서 가상 컴퓨터를 만들지 마십시오.
2. 가상 WAN에 연결하는 다른 가상 네트워크를 만듭니다. 이 가상 네트워크의 서브넷에서 가상 컴퓨터를 만듭니다. 이 가상 네트워크는 가상 네트워크 게이트웨이를 포함할 수 없습니다. [사이트 간 연결](virtual-wan-site-to-site-portal.md#vnet) 아티클의 PowerShell 단계를 사용하여 가상 네트워크를 빠르게 만들 수 있습니다. cmdlet을 실행하기 전에 값을 변경해야 합니다.
3. VNet을 가상 WAN 허브에 연결합니다. 가상 WAN의 페이지에서 가상 **네트워크 연결을**선택한 다음 **+Add 연결을**선택합니다. **연결 추가** 페이지에서 다음 필드를 채웁니다.

    * **연결 이름** - 연결의 이름을 지정합니다.
    * **허브** - 이 연결과 연결할 허브를 선택합니다.
    * **구독** - 구독을 확인합니다.
    * **가상 네트워크** - 이 허브에 연결할 가상 네트워크를 선택합니다. 가상 네트워크에는 기존의 가상 네트워크 게이트웨이를 사용할 수 없습니다.
4. **확인**을 클릭하여 가상 네트워크 연결을 만듭니다.
5. 이제 VM 간에 연결이 설정됩니다. 통신을 차단하는 방화벽이나 다른 정책이 없는 한 다른 VM에서 한 VM을 ping할 수 있어야 합니다.

## <a name="next-steps"></a>다음 단계

사용자 지정 IPsec 정책을 구성하는 단계는 [가상 WAN에 대한 사용자 지정 IPsec 정책 구성을](virtual-wan-custom-ipsec-portal.md)참조하십시오.
Virtual WAN에 대한 자세한 내용은 [Azure Virtual WAN 정보](virtual-wan-about.md) 및 [Azure Virtual WAN FAQ](virtual-wan-faq.md)를 참조하세요.