---
title: 가상 네트워크 또는 VPN 연결 구성 및 유효성 검사
description: 다양 한 Azure VPN 및 가상 네트워크 배포를 구성 하 고 유효성을 검사 하는 단계별 지침
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
ms.sourcegitcommit: cd70273f0845cd39b435bd5978ca0df4ac4d7b2c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/18/2019
ms.locfileid: "71099068"
---
# <a name="configure-and-validate-virtual-network-or-vpn-connections"></a>가상 네트워크 또는 VPN 연결 구성 및 유효성 검사

이 연습에서는 다양 한 Azure VPN 및 가상 네트워크 배포를 구성 하 고 유효성을 검사 하는 단계별 지침을 제공 합니다. 이러한 시나리오에는 전송 라우팅, 네트워크 간 연결, BGP (Border Gateway Protocol), 멀티 사이트 연결 및 지점 및 사이트 간 연결이 포함 됩니다.

Azure VPN gateway를 사용 하면 Azure에서 거의 모든 종류의 연결 된 가상 네트워크 토폴로지를 유연 하 게 배열할 수 있습니다. 예를 들어 가상 네트워크를 연결할 수 있습니다.

- 지역 간.
- 가상 네트워크 형식 (Azure Resource Manager 기본) 사이.
- Azure 내에서 또는 온-프레미스 하이브리드 환경 내에서
- 서로 다른 구독에 있습니다. 

## <a name="network-to-network-vpn-connection"></a>네트워크 간 VPN 연결

VPN을 통해 가상 네트워크를 다른 가상 네트워크 (네트워크-네트워크)에 연결 하는 것은 가상 네트워크를 온-프레미스 사이트 위치에 연결 하는 것과 비슷합니다. 두 연결 유형 모두 VPN gateway를 사용 하 여 IPsec 및 IKE를 통해 보안 터널을 제공 합니다. 가상 네트워크는 같은 또는 다른 구독의 같은 지역에 있을 수도 있고 다른 지역에 있을 수도 있습니다.

![IPsec을 사용 하는 네트워크 간 연결](./media/virtual-network-configure-vnet-connections/4034386_en_2.png)
 
가상 네트워크가 동일한 지역에 있는 경우 가상 네트워크 피어 링을 사용 하 여 연결 하는 것을 고려해 볼 수 있습니다. 가상 네트워크 피어 링은 VPN gateway를 사용 하지 않습니다. 처리량이 증가 하 고 대기 시간이 감소 합니다. 가상 네트워크 피어 링 연결을 구성 하려면 **VNet 피어 링 구성 및 유효성 검사**를 선택 합니다.

Azure 리소스 관리자 배포 모델을 통해 가상 네트워크를 만든 경우 **리소스 관리자 vnet 연결에 대 한 리소스 관리자 Vnet 구성 및 유효성 검사** 를 선택 하 여 VPN 연결을 구성 합니다.

Azure 클래식 배포 모델을 통해 만든 가상 네트워크 중 하나를 리소스 관리자를 통해 만든 경우, **리소스 관리자 VNet 연결에 대 한 클래식 Vnet 구성 및 유효성 검사** 를 선택 하 여 VPN 연결을 구성 합니다.

### <a name="configure-virtual-network-peering-for-two-virtual-networks-in-the-same-region"></a>동일한 지역에 있는 두 가상 네트워크에 대 한 가상 네트워크 피어 링 구성

Azure 가상 네트워크 피어 링 구현 및 구성을 시작 하기 전에 다음 필수 구성 요소를 충족 하는지 확인 합니다.

* 피어링된 가상 네트워크는 동일한 Azure 지역에 있어야 합니다.
* 피어 링 가상 네트워크에는 겹치지 않는 IP 주소 공간이 있어야 합니다.
* 가상 네트워크 피어링은 두 개의 가상 네트워크 간에 가능합니다. 피어 링 간에는 파생 된 전이적 관계가 없습니다. 예를 들어 VNetA가 Vneta로 피어 링이 고 Vneta가 VNetC를 사용 하 여 피어 링 경우 VNetA는 VNetC와 피어 링 *되지 않습니다* .

요구 사항을 충족 하는 경우 [자습서를 수행할 수 있습니다. Azure Portal](https://docs.microsoft.com/azure/virtual-network/virtual-network-create-peering) 를 사용 하 여 가상 네트워크 피어 링으로 가상 네트워크를 연결 하 여 피어 링을 만들고 구성 합니다.

피어 링 구성을 확인 하려면 다음 방법을 사용 합니다.

1. 필요한 [역할 및 사용 권한이](virtual-network-manage-peering.md#permissions)있는 계정을 사용 하 여 [Azure Portal](https://portal.azure.com/) 에 로그인 합니다.
2. **리소스 검색** 텍스트가 있는 포털 위쪽의 상자에서 **가상 네트워크**를 입력합니다. 검색 결과에 **가상 네트워크**가 표시되면 이를 선택합니다.
3. 표시 되는 **가상** 네트워크 블레이드에서 피어 링을 만들려는 가상 네트워크를 선택 합니다.
4. 가상 네트워크에 대해 표시 되는 창의 **설정** 섹션에서 **피어 링** 을 선택 합니다.
5. 피어 링을 선택 하 고 구성 결과를 확인 합니다.

![가상 네트워크 피어 링 구성을 확인 하기 위한 선택 항목](./media/virtual-network-configure-vnet-connections/4034496_en_1.png)
 
Azure PowerShell의 경우 [remove-azurermvirtualnetworkpeering](https://docs.microsoft.com/powershell/module/azurerm.network/get-azurermvirtualnetworkpeering?view=azurermps-4.1.0) 명령을 실행 하 여 가상 네트워크 피어 링을 가져옵니다. 예를 들면 다음과 같습니다.

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

한 리소스 관리자 가상 네트워크에서 다른 리소스 관리자 가상 네트워크로 직접 연결을 구성할 수 있습니다. 또는 IPsec을 사용 하 여 연결을 구성할 수 있습니다.

### <a name="configure-a-vpn-connection-between-resource-manager-virtual-networks"></a>리소스 관리자 가상 네트워크 간의 VPN 연결 구성

IPsec을 사용 하지 않는 리소스 관리자 가상 네트워크 간의 연결을 구성 하려면 [Azure Portal를 사용 하 여 네트워크 간 VPN gateway 연결 구성](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal)을 참조 하세요.

두 리소스 관리자 가상 네트워크 간의 IPsec 연결을 구성 하려면 각 가상 네트워크에 대 한 [Azure Portal에서 사이트 간 연결 만들기](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal) 의 1 ~ 5 단계를 수행 합니다.

> [!Note]
> 이러한 단계는 동일한 구독의 가상 네트워크에 대해서만 작동 합니다. 가상 네트워크가 서로 다른 구독에 있는 경우 PowerShell을 사용 하 여 연결 해야 합니다. [PowerShell](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-vnet-vnet-rm-ps) 문서를 참조하세요.

### <a name="validate-the-vpn-connection-between-resource-manager-virtual-networks"></a>리소스 관리자 가상 네트워크 간 VPN 연결의 유효성을 검사 합니다.

![Azure Resource Manager 가상 네트워크에 대 한 클래식 가상 네트워크 연결](./media/virtual-network-configure-vnet-connections/4034493_en_2.png)

VPN 연결이 올바르게 구성 되었는지 확인 하려면 다음 지침을 따르세요.

> [!Note] 
> 이러한 단계에서 가상 네트워크 구성 요소 다음의 숫자는 위의 다이어그램에 있는 숫자에 해당 합니다.

1. 연결 된 가상 네트워크에 겹치는 주소 공간이 없는지 확인 합니다.
2. Azure Resource Manager 가상 네트워크의 주소 범위 (1)가 **연결 개체** 인스턴스 (4)에 정확 하 게 정의 되어 있는지 확인 하십시오.
3. Azure Resource Manager 가상 네트워크의 주소 범위 (6)가 **연결 개체** 인스턴스 (3)에 정확 하 게 정의 되어 있는지 확인 합니다.
4. 미리 공유한 키가 연결 개체와 일치 하는지 확인 합니다.
5. Azure Resource Manager 가상 네트워크 게이트웨이 VIP (2)가 **연결 개체** 인스턴스 (4)에 정확 하 게 정의 되어 있는지 확인 하십시오.
6. Azure Resource Manager 가상 네트워크 게이트웨이 VIP (5)가 **연결 개체** 인스턴스 (3)에 정확 하 게 정의 되어 있는지 확인 합니다.

### <a name="connect-a-classic-virtual-network-to-a-resource-manager-virtual-network"></a>리소스 관리자 가상 네트워크에 클래식 가상 네트워크 연결

서로 다른 구독 및 다른 지역에 있는 가상 네트워크 간에 연결을 만들 수 있습니다. 게이트웨이 유형을 경로 기반으로 구성한 경우 온-프레미스 네트워크에 이미 연결 되어 있는 가상 네트워크를 연결할 수도 있습니다.

클래식 가상 네트워크와 리소스 관리자 가상 네트워크 간의 연결을 구성 하려면 [Azure Portal를 사용 하 여 다양 한 배포 모델에서 가상 네트워크 연결](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-connect-different-deployment-models-portal)을 참조 하세요.

![Azure Resource Manager 가상 네트워크에 대 한 클래식 가상 네트워크 연결](./media/virtual-network-configure-vnet-connections/4034389_en_2.png)

Azure Resource Manager 가상 네트워크에 클래식 가상 네트워크를 연결할 때 구성을 확인 하려면 다음 지침을 따르세요.

> [!Note] 
> 이러한 단계에서 가상 네트워크 구성 요소 다음의 숫자는 위의 다이어그램에 있는 숫자에 해당 합니다. 

1. 연결 된 가상 네트워크에 겹치는 주소 공간이 없는지 확인 합니다.
2. Azure Resource Manager 가상 네트워크 (6)의 주소 범위가 클래식 로컬 네트워크 정의 (3)에 정확 하 게 정의 되어 있는지 확인 합니다.
3. 클래식 가상 네트워크의 주소 범위 (1)가 Azure Resource Manager **연결 개체** 인스턴스 (4)에 정확 하 게 정의 되어 있는지 확인 하십시오.
4. 클래식 가상 네트워크 게이트웨이 VIP (2)가 Azure Resource Manager **연결 개체** 인스턴스 (4)에 정확 하 게 정의 되어 있는지 확인 합니다.
5. Azure Resource Manager 가상 네트워크 게이트웨이 (5)가 클래식 **로컬 네트워크 정의** 인스턴스 (3)에 정확 하 게 정의 되어 있는지 확인 합니다.
6. 연결 된 가상 네트워크에서 미리 공유한 키가 일치 하는지 확인 합니다.
   - 클래식 가상 네트워크: **로컬 네트워크 정의** 3
   - 가상 네트워크 Azure Resource Manager: **Connection 개체** 3-4

## <a name="create-a-point-to-site-vpn-connection"></a>지점 및 사이트 간 VPN 연결 만들기

지점 및 사이트 간 (다음 다이어그램의*P2S* ) 구성을 사용 하면 개별 클라이언트 컴퓨터에서 가상 네트워크로의 보안 연결을 만들 수 있습니다. 지점 및 사이트 간 연결은 집 또는 회의와 같은 원격 위치에서 가상 네트워크에 연결 하려는 경우에 유용 합니다. 가상 네트워크에 연결 해야 하는 소수의 클라이언트만 있는 경우에도 유용 합니다. 

지점 및 사이트 간 VPN 연결은 클라이언트 컴퓨터에서 기본 Windows VPN 클라이언트를 통해 시작 됩니다. 클라이언트 연결은 인증서를 사용하여 인증합니다.

![지점 및 사이트 간 연결](./media/virtual-network-configure-vnet-connections/4034387_en_3.png)

지점 및 사이트 간 연결에는 VPN 장치가 필요 하지 않습니다. SSTP (Secure Socket Tunneling Protocol)를 통해 VPN 연결을 만듭니다. 다양 한 배포 도구 및 배포 모델을 사용 하 여 지점 및 사이트 간 연결을 가상 네트워크에 연결할 수 있습니다.

* [Azure Portal를 사용 하 여 가상 네트워크에 지점 및 사이트 간 연결 구성](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal)
* [Azure Portal (클래식)를 사용 하 여 가상 네트워크에 지점 및 사이트 간 연결 구성](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-point-to-site-classic-azure-portal)
* [PowerShell을 사용 하 여 가상 네트워크에 지점 및 사이트 간 연결 구성](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps)

### <a name="validate-your-point-to-site-connection"></a>지점 및 사이트 간 연결의 유효성을 검사 합니다.

문제 해결 [문서: Azure 지점 및 사이트 간 연결 문제](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems) 는 지점 및 사이트 간 연결에 대 한 일반적인 문제를 안내 합니다.

## <a name="create-a-multisite-vpn-connection"></a>멀티 사이트 VPN 연결 만들기

사이트 간 연결, 지점 및 사이트 간 연결 또는 네트워크 간 연결이 이미 있는 가상 네트워크에 사이트 간 (다음 다이어그램의*S2S* ) 연결을 추가할 수 있습니다. 이러한 종류의 연결을 *멀티 사이트* 구성 이라고 하는 경우가 많습니다. 

![멀티 사이트 연결](./media/virtual-network-configure-vnet-connections/4034497_en_2.png)

Azure는 현재 다음과 같은 두 가지 배포 모델을 사용 합니다. 리소스 관리자 및 클래식. 두 모델은 서로 완전히 호환 되지 않습니다. 다른 모델을 사용 하 여 멀티 사이트 연결을 구성 하려면 다음 문서를 참조 하세요.

* [기존 VPN gateway 연결을 사용 하 여 가상 네트워크에 사이트 간 연결 추가](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-multi-site-to-site-resource-manager-portal)
* [기존 VPN gateway 연결을 사용 하 여 가상 네트워크에 사이트 간 연결 추가 (클래식)](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-multi-site)

> [!Note]
> 이러한 아티클의 단계는 Azure Express 경로 및 사이트 간 공존 연결 구성에 적용 되지 않습니다. 자세한 내용은 [express 경로 및 사이트 간 공존 연결](https://docs.microsoft.com/azure/expressroute/expressroute-howto-coexist-resource-manager)을 참조 하세요.

## <a name="configure-transit-routing"></a>전송 라우팅 구성

전송 라우팅은 데이지 체인 토폴로지에서 여러 네트워크를 연결 하는 특정 라우팅 시나리오입니다. 이 라우팅은 체인의 한쪽 끝에 있는 가상 네트워크의 리소스가 둘 중 하나의 가상 네트워크를 통해 서로 통신할 수 있도록 합니다. 전송 라우팅이 없으면 허브를 통해 피어 링 되는 네트워크 또는 장치는 서로 연결할 수 없습니다.

### <a name="configure-transit-routing-in-a-point-to-site-connection"></a>지점 및 사이트 간 연결에서 전송 라우팅 구성

VNetA와 Vneta 간에 사이트 간 VPN 연결을 구성 하려는 시나리오를 상상해 보세요. 또한 클라이언트가 VNetA의 게이트웨이에 연결 하도록 지점 및 사이트 간 VPN을 구성 하려고 합니다. 그런 다음 지점 및 사이트 간 클라이언트에 대해 전송 라우팅을 사용 하도록 설정 하 여 Vnetb를 통과 하는 VNetB에 연결 하려고 합니다. 

이 시나리오는 BGP가 VNetA와 Vneta 사이에서 사이트 간 VPN을 사용 하도록 설정 된 경우에만 지원 됩니다. 자세한 내용은 [지점 및 사이트 간 VPN 라우팅 정보](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-point-to-site-routing)를 참조 하세요.

### <a name="configure-transit-routing-in-an-expressroute-connection"></a>Express 경로 연결에서 전송 라우팅 구성

Azure ExpressRoute를 사용하면 연결 공급자에서 쉽게 처리된 전용 프라이빗 연결을 통해 온-프레미스 네트워크를 Microsoft 클라우드로 확장할 수 있습니다. ExpressRoute를 사용하면 Microsoft Azure, Office 365 및 Dynamics 365와 같은 Microsoft 클라우드 서비스에 대한 연결을 설정할 수 있습니다. 자세한 내용은 [express 경로 개요](https://docs.microsoft.com/azure/expressroute/expressroute-introduction)를 참조 하세요.

![Azure virtual network에 대 한 Express 경로 개인 피어 링 연결](./media/virtual-network-configure-vnet-connections/4034395_en_1.png)

> [!Note]
> VNetA 및 Vneta가 동일한 지정 학적 지역에 있는 경우 전송 라우팅을 구성 하는 대신 [두 가상 네트워크를 express 경로 회로에 연결](https://docs.microsoft.com/azure/expressroute/expressroute-howto-linkvnet-arm) 하는 것이 좋습니다. 가상 네트워크가 서로 다른 지정 학적 지역에 있는 경우 [express 경로 Premium](https://docs.microsoft.com/azure/expressroute/expressroute-faqs#expressroute-premium)이 있는 경우 회로에 직접 연결할 수도 있습니다. 

Express 경로 및 사이트 간 공존을 보유 하 고 있는 경우 전송 라우팅이 지원 되지 않습니다. 자세한 내용은 [PowerShell을 사용 하 여 express 경로 및 사이트 간 구성](https://docs.microsoft.com/azure/expressroute/expressroute-howto-coexist-resource-manager)을 참조 하세요.

Express 경로를 사용 하도록 설정 하 여 로컬 네트워크를 Azure virtual network에 연결 하는 경우 전송 라우팅을 사용할 가상 네트워크 간에 피어 링을 사용 하도록 설정할 수 있습니다. 로컬 네트워크에서 원격 가상 네트워크에 연결할 수 있도록 하려면 [가상 네트워크 피어 링](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview#gateways-and-on-premises-connectivity)을 구성 해야 합니다. 

> [!Note]
> 가상 네트워크 피어 링은 동일한 지역의 가상 네트워크에만 사용할 수 있습니다.

가상 네트워크 피어 링에 대 한 전송 라우팅을 구성 했는지 확인 하려면 다음 지침을 따르세요.

1. 필요한 [역할 및 사용 권한이](virtual-network-manage-peering.md#permissions)있는 계정을 사용 하 여 [Azure Portal](https://portal.azure.com/) 에 로그인 합니다.
2. 이전 다이어그램에 표시 된 것 처럼 [VNetA와 vneta 간에 피어 링을 만듭니다](https://docs.microsoft.com/azure/virtual-network/virtual-network-create-peering) . 
3. 가상 네트워크에 대해 표시 되는 창의 **설정** 섹션에서 **피어 링** 을 선택 합니다.
4. 보려는 피어 링을 선택 합니다. 그런 다음 **구성** 을 선택 하 여 express 경로 회로에 연결 된 vneta 네트워크에서 **게이트웨이 전송 허용** 및 express 경로에 연결 되지 않은 원격 Vneta 네트워크에서 **원격 게이트웨이 사용을 사용** 하도록 설정 했는지 확인 합니다. 단락.

### <a name="configure-transit-routing-in-a-virtual-network-peering-connection"></a>가상 네트워크 피어 링 연결에서 전송 라우팅 구성

가상 네트워크가 피어링되면 피어링된 가상 네트워크에 있는 게이트웨이를 온-프레미스 네트워크의 전송 지점으로 구성할 수 있습니다. 가상 네트워크 피어 링에서 전송 경로를 구성 하려면 [네트워크 간 연결](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-vnet-vnet-rm-ps?toc=/azure/virtual-network/toc.json)을 참조 하세요.

> [!Note]
> 게이트웨이 전송은 다른 배포 모델을 통해 만든 가상 네트워크 간의 피어 링 관계에서 지원 되지 않습니다. 피어 링 관계의 두 가상 네트워크는 게이트웨이 전송 작업을 위해 리소스 관리자를 통해 생성 되어야 합니다.

가상 네트워크 피어 링에 대 한 전송 경로를 구성 했는지 확인 하려면 다음 지침을 따르세요.

1. 필요한 [역할 및 사용 권한이](virtual-network-manage-peering.md#permissions)있는 계정을 사용 하 여 [Azure Portal](https://portal.azure.com/) 에 로그인 합니다.
2. **리소스 검색** 텍스트가 있는 포털 위쪽의 상자에서 **가상 네트워크**를 입력합니다. 검색 결과에 **가상 네트워크**가 표시되면 이를 선택합니다.
3. 표시 되는 **가상** 네트워크 블레이드에서 피어 링 설정을 확인할 가상 네트워크를 선택 합니다.
4. 선택한 가상 네트워크에 대해 표시 되는 창에서 **설정** 섹션의 **피어 링** 을 선택 합니다.
5. 보려는 피어 링을 선택 합니다. **구성**에서 **게이트웨이 전송 허용** 및 **원격 게이트웨이 사용** 을 사용 하도록 설정 했는지 확인 합니다.

![가상 네트워크 피어 링에 대 한 전송 경로를 구성 했는지 확인 하기 위한 선택 항목](./media/virtual-network-configure-vnet-connections/4035414_en_1.png)

### <a name="configure-transit-routing-in-a-network-to-network-connection"></a>네트워크 간 연결에서 전송 라우팅 구성

가상 네트워크 간의 전송 라우팅을 구성 하려면 리소스 관리자 배포 모델 및 PowerShell을 사용 하 여 모든 중간 네트워크에서 네트워크 연결에 대해 BGP를 사용 하도록 설정 해야 합니다. 자세한 지침은 [PowerShell을 사용 하 여 AZURE VPN gateway에서 BGP를 구성 하는 방법](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-bgp-resource-manager-ps)을 참조 하세요.

Azure VPN gateway를 통한 전송 트래픽은 클래식 배포 모델을 통해 가능 하지만 네트워크 구성 파일에서 정적으로 정의 된 주소 공간에 의존 합니다. BGP는 아직 클래식 배포 모델을 통해 Azure virtual network 및 VPN gateway에서 지원 되지 않습니다. BGP를 사용 하지 않으면 전송 주소 공간을 수동으로 정의 하는 것이 오류가 발생 하기 쉬우며 권장 하지 않습니다.

> [!Note]
> 클래식 포털에서 네트워크 구성 파일을 사용 하거나 Azure 클래식 포털을 사용 하 여 클래식 네트워크-네트워크 연결을 구성 합니다. Azure Resource Manager 배포 모델 또는 Azure Portal를 통해 클래식 가상 네트워크를 만들거나 수정할 수 없습니다. 클래식 가상 네트워크에 대 한 전송 라우팅에 대 한 자세한 내용은 [Microsoft 개발자 블로그](https://blogs.msdn.microsoft.com/igorpag/2015/10/01/hubspoke-daisy-chain-and-full-mesh-vnet-topologies-in-azure-arm-using-vpn-v1/)를 참조 하세요.

### <a name="configure-transit-routing-in-a-site-to-site-connection"></a>사이트 간 연결에서 전송 라우팅 구성

사이트 간 연결을 사용 하 여 온-프레미스 네트워크와 가상 네트워크 간에 전송 라우팅을 구성 하려면 리소스 관리자 배포 모델 및 PowerShell을 사용 하 여 모든 중간 사이트 간 연결에서 BGP를 사용 하도록 설정 해야 합니다. 지침은 [PowerShell을 사용 하 여 AZURE VPN gateway에서 BGP를 구성 하는 방법을](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-bgp-resource-manager-ps) 참조 하세요.

Azure VPN gateway를 통한 전송 트래픽은 클래식 배포 모델을 통해 가능 하지만 네트워크 구성 파일에서 정적으로 정의 된 주소 공간에 의존 합니다. BGP는 아직 클래식 배포 모델을 통해 Azure virtual network 및 VPN gateway에서 지원 되지 않습니다. BGP를 사용 하지 않으면 전송 주소 공간을 수동으로 정의 하는 것이 오류가 발생 하기 쉬우며 권장 하지 않습니다.

> [!Note]
> 클래식 포털에서 네트워크 구성 파일을 사용 하거나 Azure 클래식 포털을 사용 하 여 클래식 사이트 간 연결을 구성 합니다. Azure Resource Manager 배포 모델 또는 Azure Portal를 통해 클래식 가상 네트워크를 만들거나 수정할 수 없습니다. 클래식 가상 네트워크에 대 한 전송 라우팅에 대 한 자세한 내용은 [Microsoft 개발자 블로그](https://blogs.msdn.microsoft.com/igorpag/2015/10/01/hubspoke-daisy-chain-and-full-mesh-vnet-topologies-in-azure-arm-using-vpn-v1/)를 참조 하세요.

## <a name="configure-bgp-for-a-vpn-gateway"></a>VPN Gateway에 BGP 구성

BGP는 두 개 이상의 네트워크 간에 라우팅 및 연결 가능성 정보를 교환 하기 위해 인터넷에서 사용 되는 표준 라우팅 프로토콜입니다. Azure virtual network의 컨텍스트에서 BGP를 사용 하는 경우 Azure VPN gateway 및 온-프레미스 VPN 장치 (BGP 피어 또는 인접)를 사용할 수 있습니다. 이러한 접두사는 관련 된 게이트웨이 또는 라우터를 통과할 수 있도록 해당 접두사에 대 한 가용성 및 연결 가능성에 대 한 두 게이트웨이를 모두 제공 하는 "경로"를 교환 합니다. 

Bgp 게이트웨이가 하나의 BGP 피어에서 파악 한 경로를 다른 모든 BGP 피어로 전파 하 여 BGP가 여러 네트워크 간에 전송 라우팅을 사용할 수도 있습니다. 자세한 내용은 [Azure VPN Gateway를 사용 하는 BGP 개요](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-bgp-overview)를 참조 하세요.

### <a name="configure-bgp-for-a-vpn-connection"></a>VPN 연결에 대 한 BGP 구성

BGP를 사용 하는 VPN 연결을 구성 하려면 [PowerShell을 사용 하 여 AZURE vpn gateway에서 bgp를 구성 하는 방법](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-bgp-resource-manager-ps)을 참조 하세요.

가상 네트워크 게이트웨이에서 BGP를 사용 하도록 설정 합니다. 기본 게이트웨이는 BGP를 지원 하지 않습니다. 게이트웨이의 SKU를 확인 하려면 Azure Portal에서 **VPN Gateway** 블레이드의 **개요** 섹션으로 이동 합니다. SKU가 **Basic**인 경우 sku ( [게이트웨이 크기 조정](https://docs.microsoft.com/powershell/module/azurerm.network/resize-azurermvirtualnetworkgateway?view=azurermps-4.1.0&viewFallbackFrom=azurermps-4.0.0)참조)를 **VpnGw1**로 변경 해야 합니다. 

SKU를 확인 하면 20 분에서 30 분의 가동 중지 시간이 발생 합니다. 게이트웨이가 올바른 SKU를 사용 하는 즉시 [Get-azurermvirtualnetworkgateway](https://docs.microsoft.com/powershell/module/azurerm.network/set-azurermvirtualnetworkgateway?view=azurermps-3.8.0) PowerShell 기능을 사용 하 여 as 번호를 추가할 수 있습니다. AS 번호를 구성한 후 게이트웨이에 대 한 BGP 피어 IP가 자동으로 제공 됩니다.

AS 번호와 BGP `LocalNetworkGateway` 피어 주소를 사용 하 여를 수동으로 제공 해야 합니다. [AzureRmLocalNetworkGateway](https://docs.microsoft.com/powershell/module/azurerm.network/new-azurermlocalnetworkgateway?view=azurermps-4.1.0) 또는 AzureRmLocalNetworkGateway PowerShell `ASN` [기능](https://docs.microsoft.com/powershell/module/azurerm.network/set-azurermlocalnetworkgateway?view=azurermps-4.1.0) 중 하나를 사용 하 여 및 `-BgpPeeringAddress` 값을 설정할 수 있습니다. 일부 AS 번호는 Azure 용으로 예약 되어 있으므로 [azure VPN Gateway를 사용 하 여 BGP 정보](../vpn-gateway/vpn-gateway-bgp-overview.md#faq)에 설명 된 대로 사용할 수 없습니다.

연결 개체가 BGP를 사용 하도록 설정 되어 있어야 합니다. [Get-azurermvirtualnetworkgatewayconnection](https://docs.microsoft.com/powershell/module/azurerm.network/new-azurermvirtualnetworkgatewayconnection?view=azurermps-4.1.0) 또는 [get-azurermvirtualnetworkgatewayconnection](https://docs.microsoft.com/powershell/module/azurerm.network/set-azurermvirtualnetworkgatewayconnection?view=azurermps-4.1.0)을 `-EnableBGP` 통해 값 `$True` 을로 설정할 수 있습니다.

### <a name="validate-the-bgp-configuration"></a>BGP 구성 유효성 검사

BGP가 올바르게 구성 되어 있는지 확인 하려면 `get-AzureRmVirtualNetworkGateway` 및 `get-AzureRmLocalNetworkGateway` commandlets을 실행할 수 있습니다. 그런 다음 `BgpSettingsText` 파트에서 BGP 관련 출력을 확인할 수 있습니다. 예를 들어:

```
{

"Asn": AsnNumber,

"BgpPeeringAddress": "IP address",

"PeerWeight": 0

}
```

## <a name="create-a-highly-available-activeactive-vpn-connection"></a>항상 사용 가능한 활성/활성 VPN 연결 만들기

능동/능동 및 활성/대기 게이트웨이의 주요 차이점은 다음과 같습니다.

* 두 개의 공용 IP 주소를 사용 하 여 두 개의 게이트웨이 IP 구성을 만들어야 합니다.
* **Enableactiveactivefeature** 플래그를 설정 해야 합니다.
* 게이트웨이 SKU는 **VpnGw1**, **VpnGw2**또는 **VpnGw3**여야 합니다.

크로스-프레미스 및 네트워크 간 연결에 대 한 고가용성을 달성 하려면 여러 VPN 게이트웨이를 배포 하 고 네트워크와 Azure 간에 여러 병렬 연결을 설정 해야 합니다. 연결 옵션 및 토폴로지의 개요는 [항상 사용 가능한 크로스-프레미스 및 네트워크 간 연결](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-highlyavailable)을 참조 하세요.

능동/능동 크로스-프레미스 및 네트워크 간 연결을 만들려면 [AZURE vpn gateway를 사용 하 여 활성/활성 S2S VPN 연결 구성](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-activeactive-rm-powershell) 의 지침에 따라 활성/활성 모드로 azure vpn gateway를 구성 합니다.

> [!Note]  
> * BGP 사용 능동/능동 모드의 로컬 네트워크 게이트웨이에 주소를 추가 하는 경우 *bgp 피어의/32 주소만 추가*합니다. 더 많은 주소를 추가 하는 경우 해당 주소는 고정 경로로 간주 되며 BGP 경로 보다 우선적으로 적용 됩니다.
> * Azure에 연결 하는 온-프레미스 네트워크의 경우 다른 BGP를 숫자로 사용 해야 합니다. 동일한 경우 온-프레미스 VPN 장치에서 이미 다른 BGP 인접과 피어 링 하는 데 ASN을 사용 하는 경우 가상 네트워크를 숫자로 변경 해야 합니다.

## <a name="change-an-azure-vpn-gateway-type-after-deployment"></a>배포 후 Azure VPN gateway 유형 변경

Azure virtual network 게이트웨이 유형을 정책 기반에서 경로 기반으로 또는 다른 방법으로 직접 변경할 수는 없습니다. 게이트웨이를 먼저 삭제 해야 합니다. 그 후에는 IP 주소와 미리 공유한 키가 유지 되지 않습니다. 그런 다음 원하는 유형의 새 게이트웨이를 만들 수 있습니다. 

게이트웨이를 삭제 하 고 만들려면 다음 단계를 수행 합니다.

1. 원본 게이트웨이와 연결 된 연결을 모두 삭제 합니다.
2. Azure Portal, PowerShell 또는 클래식 PowerShell을 사용 하 여 게이트웨이를 삭제 합니다. 
   * [Azure Portal를 사용 하 여 가상 네트워크 게이트웨이를 삭제 합니다.](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-delete-vnet-gateway-portal)
   * [PowerShell을 사용 하 여 가상 네트워크 게이트웨이 삭제](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-delete-vnet-gateway-powershell)
   * [PowerShell (클래식)을 사용 하 여 가상 네트워크 게이트웨이 삭제](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-delete-vnet-gateway-classic-powershell)
3. [Vpn Gateway 만들기](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md#VNetGateway) 의 단계에 따라 원하는 유형의 새 게이트웨이를 만들고 vpn 설치를 완료 합니다.

> [!Note]
> 이 프로세스는 약 60 분 정도 걸립니다.

## <a name="next-steps"></a>다음 단계

* [Azure VM 간의 연결 문제 해결](https://docs.microsoft.com/azure/virtual-network/virtual-network-troubleshoot-connectivity-problem-between-vms)

