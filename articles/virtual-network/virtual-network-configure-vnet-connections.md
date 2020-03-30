---
title: 가상 네트워크 또는 VPN 연결 구성 및 검증
description: 다양한 Azure VPN 및 가상 네트워크 배포를 구성하고 검증하기 위한 단계별 지침
services: virtual-network
documentationcenter: na
author: v-miegge
manager: dcscontentpm
editor: ''
ms.assetid: 0433a4f4-b5a0-476d-b398-1506c57eafa2
ms.service: virtual-network
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/28/2019
ms.author: kaushika
ms.openlocfilehash: dddf402455292e19bf0fcda3c50d9ce10d5888d2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "71099068"
---
# <a name="configure-and-validate-virtual-network-or-vpn-connections"></a>가상 네트워크 또는 VPN 연결 구성 및 검증

이 연습에서는 다양한 Azure VPN 및 가상 네트워크 배포를 구성하고 유효성을 검사하는 단계별 지침을 제공합니다. 시나리오에는 전송 라우팅, 네트워크 간 연결, BGP(국경 게이트웨이 프로토콜), 다중 사이트 연결 및 사이트 간 연결이 포함됩니다.

Azure VPN 게이트웨이를 사용하면 Azure에서 거의 모든 종류의 연결된 가상 네트워크 토폴로지정렬을 유연하게 배열할 수 있습니다. 예를 들어 가상 네트워크를 연결할 수 있습니다.

- 여러 지역.
- 가상 네트워크 유형(Azure 리소스 관리자 대 클래식) 사이입니다.
- Azure 내에서 또는 온-프레미스 하이브리드 환경 내에서.
- 다른 구독에서. 

## <a name="network-to-network-vpn-connection"></a>네트워크 간 VPN 연결

VPN을 통해 가상 네트워크를 다른 가상 네트워크(네트워크 간 네트워크)에 연결하는 것은 가상 네트워크를 온-프레미스 사이트 위치에 연결하는 것과 유사합니다. 두 연결 유형 모두 VPN 게이트웨이를 사용하여 IPsec 및 IKE를 통해 안전한 터널을 제공합니다. 가상 네트워크는 같은 또는 다른 구독의 같은 지역에 있을 수도 있고 다른 지역에 있을 수도 있습니다.

![IPsec과의 네트워크 간 연결](./media/virtual-network-configure-vnet-connections/4034386_en_2.png)
 
가상 네트워크가 동일한 지역에 있는 경우 가상 네트워크 피어링을 사용하여 가상 네트워크를 연결하는 것이 좋습니다. 가상 네트워크 피어링은 VPN 게이트웨이를 사용하지 않습니다. 처리량을 늘리고 대기 시간을 줄입니다. 가상 네트워크 피어링 연결을 구성하려면 **VNet 피어링 구성 및 유효성 검사를**선택합니다.

Azure 리소스 관리자 배포 모델을 통해 가상 네트워크를 만든 경우 **리소스 관리자 VNet을 리소스 관리자 VNet 연결에 구성하고 유효성을 검사하여** VPN 연결을 구성합니다.

Azure 클래식 배포 모델을 통해 가상 네트워크 중 하나가 만들어졌고 다른 하나는 리소스 관리자를 통해 생성된 경우 구성을 선택하고 **리소스 관리자 VNet 연결에 대한 클래식 VNet의 유효성을 검사하여** VPN 연결을 구성합니다.

### <a name="configure-virtual-network-peering-for-two-virtual-networks-in-the-same-region"></a>동일한 리전에서 두 개의 가상 네트워크에 대해 가상 네트워크 피어링 구성

Azure 가상 네트워크 피어링을 구현하고 구성하기 전에 다음 필수 구성 조건을 충족하는지 확인합니다.

* 피어링된 가상 네트워크는 동일한 Azure 지역에 있어야 합니다.
* 피어난 가상 네트워크에는 겹치지 않는 IP 주소 공간이 있어야 합니다.
* 가상 네트워크 피어링은 두 개의 가상 네트워크 간에 가능합니다. 피어링 간에 파생된 전이 관계는 없습니다. 예를 들어 VNetA가 VNetB로 피어싱되고 VNetB가 VNetC로 피어싱된 경우 VNetA는 VNetC를 사용하지 *않습니다.*

요구 사항을 충족하는 경우 [자습서: Azure 포털을 사용하여 가상 네트워크 피어링을 사용하여 가상 네트워크 피어링을 사용하여](https://docs.microsoft.com/azure/virtual-network/virtual-network-create-peering) 가상 네트워크를 연결하고 피어링을 구성할 수 있습니다.

피어링 구성을 확인하려면 다음 방법을 사용합니다.

1. 필요한 역할 및 사용 권한이 있는 계정을 사용하여 [Azure 포털에](https://portal.azure.com/) [로그인합니다.](virtual-network-manage-peering.md#permissions)
2. **리소스 검색** 텍스트가 있는 포털 위쪽의 상자에서 **가상 네트워크**를 입력합니다. 검색 결과에 **가상 네트워크**가 표시되면 이를 선택합니다.
3. 표시되는 **가상 네트워크** 블레이드에서 피어링을 만들 가상 네트워크를 선택합니다.
4. 가상 네트워크에 대해 나타나는 창에서 **설정** 섹션에서 **피어링을 선택합니다.**
5. 피어링을 선택하고 구성 결과를 봅니다.

![가상 네트워크 피어링 구성 을 확인하기 위한 선택 사항](./media/virtual-network-configure-vnet-connections/4034496_en_1.png)
 
Azure PowerShell의 경우 [Get-AzureRmVirtualNetworkPeering](https://docs.microsoft.com/powershell/module/azurerm.network/get-azurermvirtualnetworkpeering?view=azurermps-4.1.0) 명령을 실행하여 가상 네트워크 피어링을 가져옵니다. 예를 들면 다음과 같습니다.

```
PS C:\Users\User1> Get-AzureRmVirtualNetworkPeering -VirtualNetworkName Vnet10-01 -ResourceGroupName dev-vnets
Name                             : LinkToVNET10-02
Id                               : /subscriptions/GUID/resourceGroups/dev-vnets/providers/Microsoft.Network/virtualNetworks/VNET10-01/virtualNetworkPeerings/LinkToVNET10-0
2
Etag                             : W/"GUID"
ResourceGroupName                : dev-vnets
VirtualNetworkName               : vnet10-01
ProvisioningState                : Succeeded
RemoteVirtualNetwork             : {
                                  "Id": "/subscriptions/GUID/resourceGroups/DEV-VNET
                                   s/providers/Microsoft.Network/virtualNetworks/VNET10-02"
                                   }
AllowVirtualNetworkAccess        : True
AllowForwardedTraffic            : False
AllowGatewayTransit              : False
UseRemoteGateways                : False
RemoteGateways                   : null
RemoteVirtualNetworkAddressSpace : null
```

### <a name="connect-a-resource-manager-virtual-network-to-another-resource-manager-virtual-network"></a>리소스 관리자 가상 네트워크를 다른 리소스 관리자 가상 네트워크에 연결

한 리소스 관리자 가상 네트워크에서 다른 리소스 관리자 가상 네트워크로 직접 연결을 구성할 수 있습니다. 또는 IPsec을 사용하여 연결을 구성할 수 있습니다.

### <a name="configure-a-vpn-connection-between-resource-manager-virtual-networks"></a>리소스 관리자 가상 네트워크 간의 VPN 연결 구성

IPsec 없이 리소스 관리자 가상 네트워크 간의 연결을 구성 하려면 [Azure 포털을 사용 하 여 네트워크-네트워크 VPN 게이트웨이 연결 구성](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal)을 참조 합니다.

두 리소스 관리자 가상 네트워크 간에 IPsec연결을 구성하려면 각 가상 네트워크에 대한 [Azure 포털에서 사이트 간 연결 만들기에서](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal) 1~5단계를 따릅니다.

> [!Note]
> 이러한 단계는 동일한 구독의 가상 네트워크에대해서만 작동합니다. 가상 네트워크가 다른 구독에 있는 경우 PowerShell을 사용하여 연결을 만들어야 합니다. [PowerShell](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-vnet-vnet-rm-ps) 문서를 참조하세요.

### <a name="validate-the-vpn-connection-between-resource-manager-virtual-networks"></a>리소스 관리자 가상 네트워크 간의 VPN 연결 유효성 검사

![Azure 리소스 관리자 가상 네트워크에 대한 클래식 가상 네트워크 연결](./media/virtual-network-configure-vnet-connections/4034493_en_2.png)

VPN 연결이 올바르게 구성되어 있는지 확인하려면 다음 지침을 따르십시오.

> [!Note] 
> 이 단계의 가상 네트워크 구성 요소 다음의 숫자는 이전 다이어그램의 숫자에 해당합니다.

1. 연결된 가상 네트워크에 겹치는 주소 공간이 없는지 확인합니다.
2. Azure 리소스 관리자 가상 네트워크(1)의 주소 범위가 연결 **개체** 인스턴스(4)에서 정확하게 정의되었는지 확인합니다.
3. Azure 리소스 관리자 가상 네트워크(6)의 주소 범위가 연결 **개체** 인스턴스(3)에서 정확하게 정의되었는지 확인합니다.
4. 미리 공유된 키가 연결 개체에서 일치하는지 확인합니다.
5. Azure 리소스 관리자 가상 네트워크 게이트웨이 VIP(2)가 **연결 개체** 인스턴스(4)에서 정확하게 정의되었는지 확인합니다.
6. Azure 리소스 관리자 가상 네트워크 게이트웨이 VIP(5)가 **연결 개체** 인스턴스(3)에서 정확하게 정의되었는지 확인합니다.

### <a name="connect-a-classic-virtual-network-to-a-resource-manager-virtual-network"></a>클래식 가상 네트워크를 리소스 관리자 가상 네트워크에 연결

서로 다른 구독과 다른 지역에 있는 가상 네트워크 간에 연결을 만들 수 있습니다. 게이트웨이 유형을 경로 기반으로 구성한 경우 온-프레미스 네트워크에 이미 연결되어 있는 가상 네트워크를 연결할 수도 있습니다.

클래식 가상 네트워크와 리소스 관리자 가상 네트워크 간의 연결을 구성하려면 [Azure 포털을 사용하여 다른 배포 모델의 가상 네트워크 연결을](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-connect-different-deployment-models-portal)참조하십시오.

![Azure 리소스 관리자 가상 네트워크에 대한 클래식 가상 네트워크 연결](./media/virtual-network-configure-vnet-connections/4034389_en_2.png)

클래식 가상 네트워크를 Azure 리소스 관리자 가상 네트워크에 연결할 때 구성을 확인하려면 다음 지침을 따르십시오.

> [!Note] 
> 이 단계의 가상 네트워크 구성 요소 다음의 숫자는 이전 다이어그램의 숫자에 해당합니다. 

1. 연결된 가상 네트워크에 겹치는 주소 공간이 없는지 확인합니다.
2. Azure Resource Manager 가상 네트워크(6)의 주소 범위가 클래식 로컬 네트워크 정의(3)에서 정확하게 정의되는지 확인합니다.
3. 클래식 가상 네트워크(1)의 주소 범위가 Azure 리소스 관리자 **연결 개체** 인스턴스(4)에서 정확하게 정의되어 있는지 확인합니다.
4. 클래식 가상 네트워크 게이트웨이 VIP(2)가 Azure 리소스 관리자 **연결 개체** 인스턴스(4)에서 정확하게 정의되어 있는지 확인합니다.
5. Azure 리소스 관리자 가상 네트워크 게이트웨이(5)가 클래식 **로컬 네트워크 정의** 인스턴스(3)에서 정확하게 정의되어 있는지 확인합니다.
6. 사전 공유 키가 연결된 두 가상 네트워크에서 일치하는지 확인합니다.
   - 클래식 가상 네트워크: **로컬 네트워크 정의** (3)
   - Azure 리소스 관리자 가상 네트워크: **연결 개체** (4)

## <a name="create-a-point-to-site-vpn-connection"></a>지점 간 VPN 연결 만들기

다음 다이어그램의 지점 간*P2S)* 구성을 사용하면 개별 클라이언트 컴퓨터에서 가상 네트워크에 대한 보안 연결을 만들 수 있습니다. 사이트 간 연결은 집이나 회의와 같은 원격 위치에서 가상 네트워크에 연결하려는 경우에 유용합니다. 가상 네트워크에 연결해야 하는 클라이언트가 몇 개밖에 없는 경우에도 유용합니다. 

지점 간 VPN 연결은 클라이언트 컴퓨터에서 기본 Windows VPN 클라이언트를 통해 시작됩니다. 클라이언트 연결은 인증서를 사용하여 인증합니다.

![지점 및 사이트 간 연결](./media/virtual-network-configure-vnet-connections/4034387_en_3.png)

지점 간 연결에는 VPN 장치가 필요하지 않습니다. 보안 소켓 터널링 프로토콜(SSTP)을 통해 VPN 연결을 만듭니다. 다양한 배포 도구 및 배포 모델을 사용하여 지점 간 연결을 가상 네트워크에 연결할 수 있습니다.

* [Azure 포털을 사용하여 가상 네트워크에 대한 지점 간 연결을 구성합니다.](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal)
* [Azure 포털(클래식)을 사용하여 가상 네트워크에 대한 지점 간 연결을 구성합니다.](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-point-to-site-classic-azure-portal)
* [PowerShell을 사용하여 가상 네트워크에 대한 지점 간 연결 구성](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps)

### <a name="validate-your-point-to-site-connection"></a>지점 간 연결 의 유효성 검사

문서 [문제 해결: Azure 지점 간 연결 문제는](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems) 지점 간 연결과 관련된 일반적인 문제를 해결합니다.

## <a name="create-a-multisite-vpn-connection"></a>다중 사이트 VPN 연결 만들기

다음 다이어그램의 사이트 간 연결(다음 다이어그램의*S2S)* 연결을 이미 사이트 간 연결, 사이트 간 연결 또는 네트워크 간 연결이 있는 가상 네트워크에 추가할 수 있습니다. 이러한 종류의 연결을 *다중 사이트* 구성이라고도 합니다. 

![다중 사이트 연결](./media/virtual-network-configure-vnet-connections/4034497_en_2.png)

Azure는 현재 두 가지 배포 모델, Resource Manager와 클래식 모델에서 작동합니다. 두 모델은 서로 완전히 호환되지 않습니다. 다른 모델로 다중 사이트 연결을 구성하려면 다음 문서를 참조하십시오.

* [기존 VPN 게이트웨이 연결이 있는 가상 네트워크에 사이트 간 연결 추가](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-multi-site-to-site-resource-manager-portal)
* [기존 VPN 게이트웨이 연결이 있는 가상 네트워크에 사이트 간 연결 추가(클래식)](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-multi-site)

> [!Note]
> 이러한 문서의 단계는 Azure ExpressRoute 및 사이트 간 공존 연결 구성에는 적용되지 않습니다. 자세한 내용은 [ExpressRoute 및 사이트 간 공존 연결을](https://docs.microsoft.com/azure/expressroute/expressroute-howto-coexist-resource-manager)참조하십시오.

## <a name="configure-transit-routing"></a>대중교통 라우팅 구성

전송 라우팅은 데이지 체인 토폴로지에서 여러 네트워크를 연결하는 특정 라우팅 시나리오입니다. 이 라우팅을 사용하면 체인의 양쪽 끝에 있는 가상 네트워크의 리소스가 그 사이에 있는 가상 네트워크를 통해 서로 통신할 수 있습니다. 대중 교통 라우팅이 없으면 허브를 통해 피어온 네트워크 나 장치가 서로 연결할 수 없습니다.

### <a name="configure-transit-routing-in-a-point-to-site-connection"></a>지점 간 연결에서 전송 라우팅 구성

VNetA와 VNetB 간에 사이트 간 VPN 연결을 구성하려는 시나리오를 상상해 보십시오. 또한 클라이언트가 VNetA의 게이트웨이에 연결할 수 있도록 지점 간 VPN을 구성하려고 합니다. 그런 다음 지점 간 클라이언트가 VNetA를 통과하는 VNetB에 연결하도록 전송 라우팅을 사용하도록 설정하려고 합니다. 

이 시나리오는 VNetA와 VNetB 간의 사이트 간 VPN에서 BGP를 사용할 때 지원됩니다. 자세한 내용은 [지점 간 VPN 라우팅 정보를](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-point-to-site-routing)참조하십시오.

### <a name="configure-transit-routing-in-an-expressroute-connection"></a>익스프레스루트 연결에서 대중교통 라우팅 구성

Azure ExpressRoute를 사용하면 연결 공급자에서 쉽게 처리된 전용 프라이빗 연결을 통해 온-프레미스 네트워크를 Microsoft 클라우드로 확장할 수 있습니다. ExpressRoute를 사용하면 Microsoft Azure, Office 365 및 Dynamics 365와 같은 Microsoft 클라우드 서비스에 대한 연결을 설정할 수 있습니다. 자세한 내용은 [ExpressRoute 개요](https://docs.microsoft.com/azure/expressroute/expressroute-introduction)를 참조하세요.

![ExpressRoute Azure 가상 네트워크에 대한 개인 피어링 연결](./media/virtual-network-configure-vnet-connections/4034395_en_1.png)

> [!Note]
> VNetA와 VNetB가 동일한 지정학적 지역에 있는 경우 두 가상 네트워크를 대중 라우팅을 구성하는 대신 [ExpressRoute 회로에 연결하는](https://docs.microsoft.com/azure/expressroute/expressroute-howto-linkvnet-arm) 것이 좋습니다. 가상 네트워크가 다른 지정학적 지역에 있는 경우 [ExpressRoute Premium이](https://docs.microsoft.com/azure/expressroute/expressroute-faqs#expressroute-premium)있는 경우 직접 회로에 연결할 수도 있습니다. 

ExpressRoute 및 사이트 간 공존이 있는 경우 대중 교통 라우팅이 지원되지 않습니다. 자세한 내용은 [PowerShell을 사용하여 ExpressRoute 및 사이트 간 구성을](https://docs.microsoft.com/azure/expressroute/expressroute-howto-coexist-resource-manager)참조하십시오.

ExpressRoute를 사용하여 로컬 네트워크를 Azure 가상 네트워크에 연결하도록 설정한 경우 전송 라우팅을 원하는 가상 네트워크 간에 피어링을 사용하도록 설정할 수 있습니다. 로컬 네트워크가 원격 가상 네트워크에 연결할 수 있도록 하려면 [가상 네트워크 피어링을](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview#gateways-and-on-premises-connectivity)구성해야 합니다. 

> [!Note]
> 가상 네트워크 피어링은 동일한 리전의 가상 네트워크에서만 사용할 수 있습니다.

가상 네트워크 피어링을 위해 전송 라우팅을 구성했는지 확인하려면 다음 지침을 따르십시오.

1. 필요한 역할 및 사용 권한이 있는 계정을 사용하여 [Azure 포털에](https://portal.azure.com/) [로그인합니다.](virtual-network-manage-peering.md#permissions)
2. 이전 다이어그램과 같이 [VNetA와 VNetB 간에 피어링을 만듭니다.](https://docs.microsoft.com/azure/virtual-network/virtual-network-create-peering) 
3. 가상 네트워크에 대해 나타나는 창에서 **설정** 섹션에서 **피어링을 선택합니다.**
4. 보려는 피어링을 선택합니다. 그런 다음 **구성을** 선택하여 ExpressRoute 회로에 연결된 VNetA 네트워크에서 **게이트웨이 전송 허용을** 사용하도록 설정하고 ExpressRoute 회로에 연결되지 않은 원격 VNetB 네트워크에서 원격 **게이트웨이를 사용하도록** 설정했습니다.

### <a name="configure-transit-routing-in-a-virtual-network-peering-connection"></a>가상 네트워크 피어링 연결에서 전송 라우팅 구성

가상 네트워크가 피어링되면 피어링된 가상 네트워크에 있는 게이트웨이를 온-프레미스 네트워크의 전송 지점으로 구성할 수 있습니다. 가상 네트워크 피어링에서 전송 경로를 구성하려면 [네트워크 간 연결을](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-vnet-vnet-rm-ps?toc=/azure/virtual-network/toc.json)참조하십시오.

> [!Note]
> 게이트웨이 전송은 서로 다른 배포 모델을 통해 생성된 가상 네트워크 간의 피어링 관계에서 지원되지 않습니다. 피어링 관계의 두 가상 네트워크는 게이트웨이 전송이 작동하려면 Resource Manager를 통해 만들어졌어야 합니다.

가상 네트워크 피어링을 위한 전송 경로를 구성했는지 확인하려면 다음 지침을 따르십시오.

1. 필요한 역할 및 사용 권한이 있는 계정을 사용하여 [Azure 포털에](https://portal.azure.com/) [로그인합니다.](virtual-network-manage-peering.md#permissions)
2. **리소스 검색** 텍스트가 있는 포털 위쪽의 상자에서 **가상 네트워크**를 입력합니다. 검색 결과에 **가상 네트워크**가 표시되면 이를 선택합니다.
3. 표시되는 **가상 네트워크** 블레이드에서 피어링 설정을 확인할 가상 네트워크를 선택합니다.
4. 선택한 가상 네트워크에 대해 표시되는 창에서 **설정** 섹션에서 **피어링을 선택합니다.**
5. 보려는 피어링을 선택합니다. 게이트웨이 **전송 허용** 및 **구성에서** **원격 게이트웨이 사용을** 사용하도록 설정했습니까?

![가상 네트워크 피어링을 위한 전송 경로를 구성한 지 확인하기 위한 선택 사항](./media/virtual-network-configure-vnet-connections/4035414_en_1.png)

### <a name="configure-transit-routing-in-a-network-to-network-connection"></a>네트워크 간 연결에서 전송 라우팅 구성

가상 네트워크 간의 전송 라우팅을 구성하려면 리소스 관리자 배포 모델과 PowerShell을 사용하여 모든 중간 네트워크 간 연결에서 BGP를 사용하도록 설정해야 합니다. 지침은 [PowerShell 을 사용하여 Azure VPN 게이트웨이에서 BGP를 구성하는 방법을 참조하세요.](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-bgp-resource-manager-ps)

Azure VPN 게이트웨이를 통한 전송 트래픽은 기존 배포 모델을 통해 가능하지만 네트워크 구성 파일의 정적 정의 주소 공간에 의존합니다. BGP는 클래식 배포 모델을 통해 Azure 가상 네트워크 및 VPN 게이트웨이에서 아직 지원되지 않습니다. BGP가 없으면 대중 교통 주소 공간을 수동으로 정의하는 것은 오류가 발생하기 쉽기 때문에 권장하지 않습니다.

> [!Note]
> Azure 클래식 포털을 사용하거나 클래식 포털에서 네트워크 구성 파일을 사용하여 클래식 네트워크 간 연결을 구성합니다. Azure 리소스 관리자 배포 모델 또는 Azure 포털을 통해 클래식 가상 네트워크를 만들거나 수정할 수 없습니다. 클래식 가상 네트워크에 대한 전송 라우팅에 대한 자세한 내용은 [Microsoft 개발자 블로그를](https://blogs.msdn.microsoft.com/igorpag/2015/10/01/hubspoke-daisy-chain-and-full-mesh-vnet-topologies-in-azure-arm-using-vpn-v1/)참조하십시오.

### <a name="configure-transit-routing-in-a-site-to-site-connection"></a>사이트 간 연결에서 전송 라우팅 구성

온-프레미스 네트워크와 사이트 간 연결이 있는 가상 네트워크 간의 전송 라우팅을 구성하려면 리소스 관리자 배포 모델 및 PowerShell을 사용하여 모든 중간 사이트 간 연결에서 BGP를 사용하도록 설정해야 합니다. 지침에 [대 한 PowerShell을 사용 하 여 Azure VPN 게이트웨이에 BGP를 구성 하는 방법을](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-bgp-resource-manager-ps) 참조 하십시오.

Azure VPN 게이트웨이를 통한 전송 트래픽은 기존 배포 모델을 통해 가능하지만 네트워크 구성 파일의 정적 정의 주소 공간에 의존합니다. BGP는 클래식 배포 모델을 통해 Azure 가상 네트워크 및 VPN 게이트웨이에서 아직 지원되지 않습니다. BGP가 없으면 대중 교통 주소 공간을 수동으로 정의하는 것은 오류가 발생하기 쉽기 때문에 권장하지 않습니다.

> [!Note]
> Azure 클래식 포털을 사용하거나 클래식 포털에서 네트워크 구성 파일을 사용하여 클래식 사이트 간 연결을 구성합니다. Azure 리소스 관리자 배포 모델 또는 Azure 포털을 통해 클래식 가상 네트워크를 만들거나 수정할 수 없습니다. 클래식 가상 네트워크에 대한 전송 라우팅에 대한 자세한 내용은 [Microsoft 개발자 블로그를](https://blogs.msdn.microsoft.com/igorpag/2015/10/01/hubspoke-daisy-chain-and-full-mesh-vnet-topologies-in-azure-arm-using-vpn-v1/)참조하십시오.

## <a name="configure-bgp-for-a-vpn-gateway"></a>VPN Gateway에 BGP 구성

BGP는 둘 이상의 네트워크 간에 라우팅 및 도달 가능성 정보를 교환하기 위해 인터넷에서 사용되는 표준 라우팅 프로토콜입니다. Azure 가상 네트워크의 컨텍스트에서 BGP를 사용하면 Azure VPN 게이트웨이 및 BGP 피어 또는 인접라고 하는 온-프레미스 VPN 장치를 사용할 수 있습니다. 두 게이트웨이는 해당 접두사가 관련된 게이트웨이 또는 라우터를 통과할 수 있도록 가용성과 도달 가능성을 모두 알려주는 "경로"를 교환합니다. 

BGP는 또한 BGP 게이트웨이가 하나의 BGP 피어에서 다른 모든 BGP 피어로 학습하는 경로를 전파하여 여러 네트워크 간에 전송 라우팅을 활성화할 수 있습니다. 자세한 내용은 [Azure VPN 게이트웨이를 통해 BGP 의 개요를](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-bgp-overview)참조하십시오.

### <a name="configure-bgp-for-a-vpn-connection"></a>VPN 연결에 대한 BGP 구성

BGP를 사용하는 VPN 연결을 구성하려면 PowerShell 을 [사용하여 Azure VPN 게이트웨이에서 BGP를 구성하는 방법을 참조하세요.](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-bgp-resource-manager-ps)

가상 네트워크 게이트웨이에 대한 자율 시스템(AS) 번호를 만들어 가상 네트워크 게이트웨이에서 BGP를 활성화합니다. 기본 게이트웨이는 BGP를 지원하지 않습니다. 게이트웨이의 SKU를 확인하려면 Azure 포털의 VPN **게이트웨이** 블레이드의 **개요** 섹션으로 이동하십시오. SKU가 **기본인**경우 SKU(게이트웨이 크기 [조정](https://docs.microsoft.com/powershell/module/azurerm.network/resize-azurermvirtualnetworkgateway?view=azurermps-4.1.0&viewFallbackFrom=azurermps-4.0.0)참조)를 **VpnGw1로**변경해야 합니다. 

SKU를 확인하면 20~30분의 가동 중지 시간이 발생합니다. 게이트웨이에 올바른 SKU가 있는 즉시 [Set-AzureRmVirtualNetworkGateway](https://docs.microsoft.com/powershell/module/azurerm.network/set-azurermvirtualnetworkgateway?view=azurermps-3.8.0) PowerShell 명령어를 사용하여 AS 번호를 추가할 수 있습니다. AS 번호를 구성하면 게이트웨이에 대한 BGP 피어 IP가 자동으로 제공됩니다.

AS 번호와 `LocalNetworkGateway` BGP 피어 주소를 수동으로 제공해야 합니다. [새 AzureRmLocalNetwork게이트웨이](https://docs.microsoft.com/powershell/module/azurerm.network/new-azurermlocalnetworkgateway?view=azurermps-4.1.0) 또는 [Set-AzureRmLocalNetworkNetwork](https://docs.microsoft.com/powershell/module/azurerm.network/set-azurermlocalnetworkgateway?view=azurermps-4.1.0) PowerShell 명령어를 사용하여 `ASN` 및 `-BgpPeeringAddress` 값을 설정할 수 있습니다. 일부 AS 번호는 Azure용으로 예약되어 있으며 Azure [VPN 게이트웨이를 사용하여 BGP 에](../vpn-gateway/vpn-gateway-bgp-overview.md#faq)설명된 대로 사용할 수 없습니다.

연결 개체에 BGP가 활성화되어 있어야 합니다. `-EnableBGP` 새 [AzureRmVirtualNetwork게이트웨이연결](https://docs.microsoft.com/powershell/module/azurerm.network/new-azurermvirtualnetworkgatewayconnection?view=azurermps-4.1.0) 또는 [설정-AzureRmVirtualNetwork게이트웨이연결을](https://docs.microsoft.com/powershell/module/azurerm.network/set-azurermvirtualnetworkgatewayconnection?view=azurermps-4.1.0) `$True` 통해 값을 설정할 수 있습니다.

### <a name="validate-the-bgp-configuration"></a>BGP 구성 의 유효성 검사

BGP가 올바르게 구성되었는지 확인하려면 및 `get-AzureRmVirtualNetworkGateway` `get-AzureRmLocalNetworkGateway` 명령어를 실행할 수 있습니다. 그런 다음 부품에 BGP 관련 `BgpSettingsText` 출력을 알 수 있습니다. 예를 들어:

```
{

"Asn": AsnNumber,

"BgpPeeringAddress": "IP address",

"PeerWeight": 0

}
```

## <a name="create-a-highly-available-activeactive-vpn-connection"></a>고가용성 활성/활성 VPN 연결 만들기

활성/활성/대기 게이트웨이간의 주요 차이점은 다음과 같습니다.

* 두 개의 공용 IP 주소가 있는 두 개의 게이트웨이 IP 구성을 만들어야 합니다.
* **활성화활성 기능** 플래그를 설정해야 합니다.
* 게이트웨이 SKU는 **VpnGw1,** **VpnGw2**또는 **VpnGw3이어야**합니다.

크로스 프레미스 및 네트워크 간 연결을 위한 고가용성을 달성하려면 여러 VPN 게이트웨이를 배포하고 네트워크와 Azure 간에 여러 병렬 연결을 설정해야 합니다. 연결 옵션 및 토폴로지에 대한 개요는 [가용성이 높은 크로스-프레미스 및 네트워크 간 연결을](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-highlyavailable)참조하십시오.

활성/활성 크로스-프레미스 및 네트워크 간 연결을 만들려면 [Azure VPN 게이트웨이를 사용하여 활성/활성 S2S VPN 연결 구성의](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-activeactive-rm-powershell) 지침을 따라 활성/활성 모드에서 Azure VPN 게이트웨이를 구성합니다.

> [!Note]  
> * BGP 지원 활성/활성 모드에 대한 로컬 네트워크 게이트웨이에 주소를 추가하면 *BGP 피어의 /32 주소만 추가합니다.* 주소를 더 추가하면 정적 경로로 간주되며 BGP 경로보다 우선합니다.
> * Azure에 연결하는 온-프레미스 네트워크에 대해 다른 BGP AS 번호를 사용해야 합니다. (동일하는 경우 온-프레미스 VPN 장치에서 이미 ASN을 사용하여 다른 BGP 이웃과 피어피어인 경우 가상 네트워크 AS 번호로 변경해야 합니다.)

## <a name="change-an-azure-vpn-gateway-type-after-deployment"></a>배포 후 Azure VPN 게이트웨이 유형 변경

Azure 가상 네트워크 게이트웨이 유형은 정책 기반에서 경로 기반 또는 다른 방법으로 직접 변경할 수 없습니다. 먼저 게이트웨이를 삭제해야 합니다. 그 후에는 IP 주소와 미리 공유된 키가 보존되지 않습니다. 그런 다음 원하는 형식의 새 게이트웨이를 만들 수 있습니다. 

게이트웨이를 삭제하고 만들려면 다음 단계를 따르십시오.

1. 원래 게이트웨이와 연결된 모든 연결을 삭제합니다.
2. Azure 포털, PowerShell 또는 클래식 PowerShell을 사용하여 게이트웨이를 삭제합니다. 
   * [Azure 포털을 사용하여 가상 네트워크 게이트웨이 삭제](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-delete-vnet-gateway-portal)
   * [PowerShell을 사용하여 가상 네트워크 게이트웨이 삭제](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-delete-vnet-gateway-powershell)
   * [PowerShell(클래식)을 사용하여 가상 네트워크 게이트웨이 삭제](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-delete-vnet-gateway-classic-powershell)
3. VPN 게이트웨이 [만들기의](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md#VNetGateway) 단계를 수행하여 원하는 유형의 새 게이트웨이를 만들고 VPN 설정을 완료합니다.

> [!Note]
> 이 프로세스는 약 60분이 소요됩니다.

## <a name="next-steps"></a>다음 단계

* [Azure VM 간의 연결 문제 해결](https://docs.microsoft.com/azure/virtual-network/virtual-network-troubleshoot-connectivity-problem-between-vms)

