---
title: 포함 파일
description: 포함 파일
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: include
ms.date: 06/26/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 98b82047c1744fbd63ecd3665e804e9d8f6c5221
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85565968"
---
### <a name="does-the-user-need-to-have-hub-and-spoke-with-sd-wanvpn-devices-to-use-azure-virtual-wan"></a>Azure Virtual WAN을 사용하려면 사용자에게 SD-WAN/VPN 디바이스를 사용하는 허브 및 스포크가 있어야 하나요?

가상 WAN은 사이트/사이트 간 VPN 연결, 사용자/P2S 연결, Express 경로 연결, Virtual Network 연결, VPN Express 경로 상호 연결과, VNet 간 전이적 연결, 중앙 라우팅, Azure 방화벽 및 방화벽 관리자 보안, 모니터링, Express 경로 암호화 및 기타 다양 한 기능 등의 단일 창에 기본 제공 되는 다양 한 기능을 제공 합니다. 이러한 모든 사용 사례가 가상 WAN 사용을 시작 하는 것은 아닙니다. 사용 사례를 하나만 사용 하 여 시작할 수 있습니다. 가상 WAN 아키텍처는 규모와 성능이 기본 제공 되는 허브 및 스포크 아키텍처입니다. 여기서 분기 (VPN/SD-WAN 장치), 사용자 (Azure VPN 클라이언트, openVPN 또는 IKEv2 클라이언트), Express 경로 회로, 가상 네트워크는 가상 허브에 스포크 역할을 합니다. 모든 허브가 표준 Virtual WAN에서 풀 메시로 연결되므로 사용자는 Microsoft 백본을 임의(모든 스포크) 연결에 쉽게 사용할 수 있습니다. SD-WAN/VPN 디바이스를 사용하는 허브 및 스포크의 경우 사용자는 Azure Virtual WAN 포털에서 수동으로 설정하거나 Virtual WAN 파트너 CPE(SD-WAN/VPN)를 사용하여 Azure에 대한 연결을 설정할 수 있습니다. 가상 WAN 파트너는 장치 정보를 Azure로 내보내고 azure 구성을 다운로드 하 고 Azure 가상 WAN 허브에 대 한 연결을 설정 하는 기능을 제공 하는 연결에 대 한 자동화를 제공 합니다. 지점 및 사이트 간/사용자 VPN 연결의 경우 [AZURE vpn 클라이언트](https://go.microsoft.com/fwlink/?linkid=2117554), Openvpn 또는 IKEv2 클라이언트를 지원 합니다. 

### <a name="can-you-disable-fully-meshed-hubs-in-a-virtual-wan"></a>가상 WAN에서 완전히 작동 하는 허브를 사용 하지 않도록 설정할 수 있나요?

가상 WAN은 기본 및 표준의 두 가지 특성으로 제공 됩니다. 기본 가상 WAN에서 허브는 함께 사용할 수 없습니다. 표준 가상 WAN에서는 가상 WAN이 처음 설정 될 때 허브가 자동으로 연결 됩니다. 사용자는 특정 작업을 수행할 필요가 없습니다. 또한 사용자는이 기능을 사용 하지 않도록 설정 하거나 사용 하도록 설정 하 여 함께 사용할 수 있습니다. 가상 WAN은 모든 스포크 (VNet, VPN 또는 Express 경로) 간에 트래픽을 유도 하는 여러 라우팅 옵션을 제공 합니다. 이를 통해 완벽 하 게 연결 되는 허브의 용이성과 요구에 따라 트래픽 라우팅의 유연성이 제공 됩니다. 

### <a name="how-are-availability-zones-and-resiliency-handled-in-virtual-wan"></a>가상 WAN에서 가용성 영역 및 복원 력이 어떻게 처리 되나요?

가상 WAN은 허브 내에서 사용할 수 있는 허브 및 서비스의 컬렉션입니다. 사용자는 각자의 요구에 따라 가상 wan을 여러 개 사용할 수 있습니다. 가상 WAN 허브에는 VPN, Express 경로 등과 같은 여러 서비스가 있습니다. 이러한 각 서비스는 지역이 가용성 영역를 지 원하는 경우 가용성 영역 지역에 배포 됩니다. 허브의 초기 배포 후에도 지역이 가용성 영역이 되 면 사용자는 게이트웨이를 다시 만들 수 있으며,이 경우 가용성 영역 배포가 트리거됩니다. 모든 게이트웨이는 허브에 활성-활성으로 프로 비전 되며 허브 내에 기본 제공 되는 복원 력이 있음을 의미 합니다. 사용자는 여러 지역에서 복원 력을 원할 경우 여러 허브에 연결할 수 있습니다. 가상 WAN의 개념은 전역적 이지만 실제 가상 WAN 리소스는 리소스 관리자 기반 이며 배포 된 지역적으로입니다. 가상 WAN 지역 자체에 문제가 있는 경우 해당 가상 WAN의 모든 허브는 계속 해 서 그대로 작동 하지만, 사용자는 가상 WAN 지역을 사용할 수 있을 때까지 새 허브를 만들 수 없게 됩니다.

### <a name="what-client-does-the-azure-virtual-wan-user-vpn-point-to-site-support"></a>Azure Virtual WAN 사용자 VPN(지점 및 사이트 간)에서 지원하는 클라이언트는 무엇인가요?

Virtual WAN은 [Azure VPN 클라이언트](https://go.microsoft.com/fwlink/?linkid=2117554), OpenVPN 클라이언트 또는 모든 IKEv2 클라이언트를 지원합니다. Azure VPN 클라이언트는 Azure AD 인증을 지원합니다. 최소 Windows 10 클라이언트 OS 버전 17763.0 이상이 필요합니다.  OpenVPN 클라이언트는 인증서 기반 인증을 지원할 수 있습니다. 게이트웨이에서 인증서 기반 인증을 선택 하면 장치에 다운로드할 *. ovpn* 파일이 표시 됩니다. IKEv2는 인증서와 RADIUS 인증을 모두 지원 합니다. 

### <a name="for-user-vpn-point-to-site--why-is-the-p2s-client-pool-split-into-two-routes"></a>사용자 VPN(지점 및 사이트 간)의 경우 P2S 클라이언트 풀이 두 경로로 분할되는 이유는 무엇인가요?

각 게이트웨이에는 두 개의 인스턴스가 있습니다. 각 게이트웨이 인스턴스가 연결된 클라이언트에 대해 독립적으로 클라이언트 IP를 할당할 수 있도록 분할이 수행되고, 가상 네트워크로부터의 트래픽이 게이트웨이 간 인스턴스 홉을 방지하기 위해 올바른 게이트웨이 인스턴스로 다시 라우팅됩니다.

### <a name="how-do-i-add-dns-servers-for-p2s-clients"></a>P2S 클라이언트용 DNS 서버를 추가하려면 어떻게 하나요?

P2S 클라이언트용 DNS 서버를 추가하는 두 가지 옵션이 있습니다. 첫 번째 방법은 클라이언트 대신 게이트웨이에 사용자 지정 DNS 서버를 추가 하기 때문에 선호 되는 방법입니다.

1. 다음 PowerShell 스크립트를 사용 하 여 사용자 지정 DNS 서버를 추가 합니다. 사용자 환경에 대 한 값을 바꿉니다.

   ```powershell
   // Define variables
   $rgName = "testRG1"
   $virtualHubName = "virtualHub1"
   $P2SvpnGatewayName = "testP2SVpnGateway1"
   $vpnClientAddressSpaces = 
   $vpnServerConfiguration1Name = "vpnServerConfig1"
   $vpnClientAddressSpaces = New-Object string[] 2
   $vpnClientAddressSpaces[0] = "192.168.2.0/24"
   $vpnClientAddressSpaces[1] = "192.168.3.0/24"
   $customDnsServers = New-Object string[] 2
   $customDnsServers[0] = "7.7.7.7"
   $customDnsServers[1] = "8.8.8.8"
   $virtualHub = $virtualHub = Get-AzVirtualHub -ResourceGroupName $rgName -Name $virtualHubName
   $vpnServerConfig1 = Get-AzVpnServerConfiguration -ResourceGroupName $rgName -Name $vpnServerConfiguration1Name

   // Specify custom dns servers for P2SVpnGateway VirtualHub while creating gateway
   createdP2SVpnGateway = New-AzP2sVpnGateway -ResourceGroupName $rgname -Name $P2SvpnGatewayName -VirtualHub $virtualHub -VpnGatewayScaleUnit 1 -VpnClientAddressPool $vpnClientAddressSpaces -VpnServerConfiguration $vpnServerConfig1 -CustomDnsServer $customDnsServers

   // Specify custom dns servers for P2SVpnGateway VirtualHub while updating existing gateway
   $P2SVpnGateway = Get-AzP2sVpnGateway -ResourceGroupName $rgName -Name $P2SvpnGatewayName
   $updatedP2SVpnGateway = Update-AzP2sVpnGateway -ResourceGroupName $rgName -Name $P2SvpnGatewayName  -CustomDnsServer $customDnsServers 

   // Re-generate Vpn profile either from PS/Portal for Vpn clients to have the specified dns servers
   ```
2. 또는 Azure VPN 클라이언트를 Windows 10에 사용하는 경우 다운로드한 프로필 XML 파일을 수정하고 **\<dnsservers>\<dnsserver> \</dnsserver>\</dnsservers>** 태그를 추가한 후에 가져올 수 있습니다.

   ```powershell
      <azvpnprofile>
      <clientconfig>

          <dnsservers>
              <dnsserver>x.x.x.x</dnsserver>
              <dnsserver>y.y.y.y</dnsserver>
          </dnsservers>
    
      </clientconfig>
      </azvpnprofile>
   ```

### <a name="for-user-vpn-point-to-site--how-many-clients-are-supported"></a>사용자 VPN(지점 및 사이트 간)의 경우 지원되는 클라이언트 수는 어떻게 되나요?

각 사용자 VPN P2S 게이트웨이에는 두 개의 인스턴스가 있으며, 각 인스턴스는 배율 단위가 변경될 때 특정 사용자를 지원합니다. 배율 단위 1-3는 500 연결을 지원 하 고, scale unit 4-6은 1000 연결을 지원 하며, Scale unit 7-12은 5000 연결을 지원 하 고, Scale unit 13-20은 최대 1만 연결을 지원 

예를 들어 사용자가 1 배율 단위를 선택한다고 가정해 보겠습니다. 각 배율 단위는 활성-활성 게이트웨이를 의미 하며 각 인스턴스 (이 경우 2)는 최대 500 개의 연결을 지원 합니다. 게이트웨이 당 500 연결 * 2를 가져올 수 있으므로이 배율 단위에 대 한 500 대신 1000을 계획 하는 것은 아닙니다. 권장 되는 연결 수를 초과할 하는 경우 추가 500에 대 한 연결이 중단 될 수 있는 경우 인스턴스를 처리 해야 할 수 있습니다. 또한 크기 조정 단위를 확장 또는 축소 하거나 VPN 게이트웨이에서 지점 및 사이트 간 구성을 변경 하려는 경우에는 가동 중지 시간을 계획 해야 합니다.

### <a name="what-is-the-difference-between-an-azure-virtual-network-gateway-vpn-gateway-and-an-azure-virtual-wan-vpn-gateway"></a>Azure 가상 네트워크 게이트웨이(VPN Gateway)와 Azure Virtual WAN VPN Gateway의 차이는 무엇인가요?

Virtual WAN은 대규모 사이트 간 연결을 제공하며 처리량, 확장성 및 사용 편의성을 높여 줍니다. 사이트를 Virtual WAN VPN Gateway에 연결할 경우 게이트웨이 유형 'VPN'을 사용하는 일반 가상 네트워크 게이트웨이와는 다릅니다. 마찬가지로, ExpressRoute 회로를 Virtual WAN 허브에 연결할 경우 게이트웨이 유형 'ExpressRoute'를 사용하는 일반 가상 네트워크 게이트웨이와는 달리 ExpressRoute 게이트웨이에 다른 리소스를 사용합니다. 

Virtual WAN은 VPN과 ExpressRoute 모두에 최대 20Gbps의 집계 처리량을 지원합니다. Virtual WAN에는 또한 CPE 분기 디바이스 파트너의 에코시스템 연결을 위한 자동화 기능이 있습니다. CPE 분기 장치는 Azure 가상 WAN을 자동으로 프로 비전 하 고 연결 하는 기본 제공 자동화 기능을 제공 합니다. 이러한 디바이스는 SD-WAN 및 VPN 파트너의 증가하는 에코시스템에서 사용할 수 있습니다. [선호 파트너 목록](../articles/virtual-wan/virtual-wan-locations-partners.md)을 참조하세요.

### <a name="how-is-virtual-wan-different-from-an-azure-virtual-network-gateway"></a>Virtual WAN은 Azure 가상 네트워크 게이트웨이와 어떻게 다른가요?

가상 네트워크 게이트웨이 VPN은 터널 30개로 제한됩니다. 연결에는 대규모 VPN을 위한 Virtual WAN을 사용해야 합니다. 허브당 20Gbps의 집계를 사용하여 지역(가상 허브)당 최대 1000개의 분기 연결을 연결할 수 있습니다. 연결은 온-프레미스 VPN 디바이스에서 가상 허브로의 활성-활성 터널입니다. 지역당 하나의 허브만 있을 수 있습니다. 즉 허브에서 1,000개가 넘는 분기를 연결할 수 있습니다.

### <a name="what-is-a-virtual-wan-gateway-scale-unit"></a>가상 WAN 게이트웨이 확장 단위란?

배율 단위는 가상 허브에서 게이트웨이의 집계 처리량을 선택 하기 위해 정의 된 단위입니다. VPN의 1 가지 배율 단위 = 500 Mbps ExpressRoute의 1개 확장 단위 = 2Gbps 예제: VPN의 10 개 배율 단위는 500 Mbps * 10 = 5Gbps를 의미 합니다.

### <a name="which-device-providers-virtual-wan-partners-are-supported"></a>어떤 디바이스 공급자(Virtual WAN 파트너)가 지원되나요?

현재 많은 파트너가 완전히 자동화된 Virtual WAN 환경을 지원합니다. 자세한 내용은 [Virtual WAN 파트너](../articles/virtual-wan/virtual-wan-locations-partners.md)를 참조하세요.

### <a name="what-are-the-virtual-wan-partner-automation-steps"></a>Virtual WAN 파트너 자동화 단계란?

자세한 내용은 [Virtual WAN 파트너 자동화](../articles/virtual-wan/virtual-wan-configure-automation-providers.md)를 참조하세요.

### <a name="am-i-required-to-use-a-preferred-partner-device"></a>원하는 파트너 디바이스를 사용해야 하나요?

아니요. IKEv2/IKEv1 IPsec 지원에 대한 Azure 요구 사항을 준수하는 모든 VPN 지원 디바이스를 사용할 수 있습니다. 또한 가상 WAN에는 Azure 가상 WAN에 대 한 연결을 자동화 하는 CPE 파트너 솔루션이 있으므로 대규모로 IPsec VPN 연결을 보다 쉽게 설정할 수 있습니다.

### <a name="how-do-virtual-wan-partners-automate-connectivity-with-azure-virtual-wan"></a>Virtual WAN 파트너는 Azure Virtual WAN과 연결을 어떻게 자동화하나요?

일반적으로 소프트웨어 정의 연결 솔루션은 컨트롤러 또는 디바이스 프로비저닝 센터를 사용하여 해당 분기 디바이스를 관리합니다. 컨트롤러는 Azure API를 사용하여 Azure Virtual WAN에 대한 연결을 자동화할 수 있습니다. 자동화에는 분기 정보 업로드, Azure 구성 다운로드, Azure 가상 허브로의 IPSec 터널 설정, 분기 디바이스에서 Azure Virtual WAN으로의 자동 연결 설정이 포함됩니다. 수백 개의 분기가 있을 경우 온보딩 환경에서 대규모 IPsec 연결을 설정, 구성, 관리할 필요가 없기 때문에 Virtual WAN CPE 파트너를 사용하여 연결하는 것이 쉽습니다. 자세한 내용은 [Virtual WAN 파트너 자동화](../articles/virtual-wan/virtual-wan-configure-automation-providers.md)를 참조하세요.

### <a name="what-if-a-device-i-am-using-is-not-in-the-virtual-wan-partner-list-can-i-still-use-it-to-connect-to-azure-virtual-wan-vpn"></a>사용 중인 디바이스가 Virtual WAN 파트너 목록에 없는 경우 어떻게 되나요? 계속 Azure Virtual WAN VPN에 연결할 수 있나요?

네, 디바이스가 IPsec IKEv1 또는 IKEv2를 지원하는 경우 연결할 수 있습니다. Virtual WAN 파트너는 디바이스에서 Azure VPN 엔드포인트로의 연결을 자동화합니다. 이는 ‘분기 정보 업로드’, ‘IPsec 및 구성’ 및 ‘연결성’과 같은 단계를 자동화하는 것을 의미합니다. 디바이스가 Virtual WAN 파트너 에코시스템에서 온 것이 아니기 때문에 IPsec 연결을 설정하려면 Azure 구성을 가져오고 디바이스를 업데이트하는 작업을 수동으로 많이 수행해야 합니다.

### <a name="how-do-new-partners-that-are-not-listed-in-your-launch-partner-list-get-onboarded"></a>출시 파트너 목록에 없는 새 파트너는 어떻게 온보딩하나요?

모든 Virtual WAN API는 개방형 API입니다. [가상 WAN 파트너 자동화](../articles/virtual-wan/virtual-wan-configure-automation-providers.md) 설명서를 참조 하 여 기술 실현을 평가할 수 있습니다. IKEv1 또는 IKEv2 IPsec 연결용으로 프로비전 가능한 디바이스가 있는 파트너가 적합합니다. 회사에서 위에 제공 된 automation 지침에 따라 해당 CPE 장치에 대 한 자동화 작업을 완료 하면 azurevirtualwan@microsoft.com [파트너를 통한 연결]( ../articles/virtual-wan/virtual-wan-locations-partners.md#partners)에 나열 될 수 있습니다. 특정 회사 솔루션을 가상 WAN 파트너로 나열 하려는 고객의 경우에는로 전자 메일을 보내 회사에서 가상 WAN에 연결 해 주시기 바랍니다 azurevirtualwan@microsoft.com .

### <a name="how-is-virtual-wan-supporting-sd-wan-devices"></a>Virtual WAN이 SD-WAN 디바이스를 지원하는 방법

Virtual WAN 파트너는 Azure VPN 엔드포인트로의 IPsec 연결을 자동화합니다. Virtual WAN 파트너가 SD-WAN 공급 기업인 경우 SD-WAN 컨트롤러가 자동화 및 Azure VPN 엔드포인트로의 IPsec 연결을 관리함을 의미합니다. 독점적인 SD-WAN 기능을 위해 Azure VPN 대신 SD-WAN 디바이스가 자체 엔드포인트를 필요로 하는 경우, Azure VNet에 SD-WAN 엔드포인트를 배포하고 Azure Virtual WAN과 공존할 수 있습니다.

### <a name="how-many-vpn-devices-can-connect-to-a-single-hub"></a>한 허브에 연결할 수 있는 VPN 디바이스의 수는 몇 개인가요?

가상 허브당 최대 1,000개의 연결이 지원됩니다. 각 연결은 네 개의 링크로 구성되며, 각 링크 연결은 액티브-액티브 구성인 두 개 터널을 지원합니다. Azure 가상 허브 VPN 게이트웨이에서 터널이 종료 됩니다. 링크는 분기/v m 장치에서의 실제 ISP 링크를 나타냅니다.

### <a name="what-is-a-branch-connection-to-azure-virtual-wan"></a>Azure Virtual WAN에 대한 분기 연결이란?

분기 또는 VPN 장치에서 Azure 가상 WAN으로 연결 하는 것은 아무 것도 아니지만 가상 허브에서 거의 VPN 사이트와 Azure VPN Gateway를 연결 하는 VPN 연결입니다.

### <a name="can-the-on-premises-vpn-device-connect-to-multiple-hubs"></a>온-프레미스 VPN 디바이스가 여러 허브에 연결할 수 있나요?

예. 트래픽 흐름이 시작되면 온-프레미스 디바이스에서 가장 가까운 Microsoft 네트워크 에지로, 그리고 가상 허브로 이동합니다.

### <a name="are-there-new-resource-manager-resources-available-for-virtual-wan"></a>Virtual WAN에 사용 가능한 새 Resource Manager 리소스가 있나요?
  
예, 가상 WAN에는 새로운 리소스 관리자 리소스가 있습니다. 자세한 내용은 [개요](../articles/virtual-wan/virtual-wan-about.md)를 참조하세요.

### <a name="can-i-deploy-and-use-my-favorite-network-virtual-appliance-in-an-nva-vnet-with-azure-virtual-wan"></a>Azure Virtual WAN과 함께 즐겨 찾는 네트워크 가상 어플라이언스(NVA VNet에서)를 배포 및 사용할 수 있나요?

예. 원하는 NVA(네트워크 가상 어플라이언스) VNet을 Azure Virtual WAN에 연결할 수 있습니다.

### <a name="can-i-create-a-network-virtual-appliance-inside-the-virtual-hub"></a>가상 허브 내에 네트워크 가상 어플라이언스를 만들 수 있나요?

NVA(네트워크 가상 어플라이언스)는 가상 허브 내에 배포할 수 없습니다. 그러나 가상 허브에 연결 된 스포크 VNet에서 만들 수 있으며, 필요에 따라 트래픽을 보낼 적절 한 라우팅을 사용할 수 있습니다.

### <a name="can-a-spoke-vnet-have-a-virtual-network-gateway"></a>스포크 VNet이 가상 네트워크 게이트웨이를 가질 수 있나요?

아니요. 가상 허브에 연결되어 있는 경우 스포크 VNet은 가상 네트워크 게이트웨이를 가질 수 없습니다.

### <a name="is-there-support-for-bgp-in-vpn-connectivity"></a>VPN 연결에서 BGP를 지원 하나요?

예, BGP가 지원됩니다. VPN 사이트를 만들 때 BGP 매개 변수를 제공할 수 있습니다. 이는 해당 사이트에 대해 Azure에서 생성된 모든 연결이 BGP에 대해 활성화됨을 의미합니다.

### <a name="is-there-any-licensing-or-pricing-information-for-virtual-wan"></a>Virtual WAN에 대한 라이선스 또는 가격 책정 정보가 있나요?

예. [가격 책정](https://azure.microsoft.com/pricing/details/virtual-wan/) 페이지를 참조하세요.

### <a name="is-it-possible-to-construct-azure-virtual-wan-with-a-resource-manager-template"></a>Resource Manager 템플릿을 사용하여 Azure Virtual WAN을 만들 수 있나요?

[빠른 시작 템플릿](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Network)을 사용하면 허브와 vpnsite가 각각 하나씩 포함된 단일 Virtual WAN의 단순한 구성을 만들 수 있습니다. Virtual WAN은 주로 REST 또는 포털 기반 서비스입니다.

### <a name="can-spoke-vnets-connected-to-a-virtual-hub-communicate-with-each-other-v2v-transit"></a>가상 허브에 연결된 VNet 스포크는 서로 통신(V2V 전송)할 수 있나요?

예. 표준 가상 WAN은 Vnet 연결 된 가상 WAN 허브를 통해 VNet 간 전이적 연결을 지원 합니다. Virtual WAN 용어에서는 이러한 경로를 단일 지역 내의 Virtual WAN 허브에 연결된 VNet의 경우 "로컬 Virtual WAN VNet 전송"이라고 하며, 둘 이상의 지역에 걸친 여러 Virtual WAN 허브를 통해 연결된 VNet의 경우 "글로벌 Virtual WAN VNet 전송"이라고 합니다. 일부 시나리오에서는 로컬 또는 전역 가상 WAN VNet 전송 외에도 [Virtual Network 피어 링](../articles/virtual-network/virtual-network-peering-overview.md) 을 사용 하 여 스포크 vnet을 직접 피어 링 수 있습니다. 이 경우 VNet 피어 링이 가상 WAN 허브를 통해 전이적 연결 보다 우선적으로 적용 됩니다.

### <a name="is-branch-to-branch-connectivity-allowed-in-virtual-wan"></a>Virtual WAN에서 분기-분기 연결이 가능한가요?

예, Virtual WAN에서 분기-분기 연결을 사용할 수 있습니다. 분기는 개념적으로 VPN 사이트, Express 경로 회로 또는 지점 및 사이트 간/사용자 VPN 사용자에 게 적용 됩니다. 분기에 분기를 사용 하도록 설정 하는 기능은 기본적으로 사용 되며 WAN 구성 설정에서 찾을 수 있습니다. 이렇게 하면 vpn 분기/사용자가 다른 VPN 분기에 연결할 수 있을 뿐만 아니라 VPN과 Express 경로 사용자 간에 전송 연결이 설정 됩니다.

### <a name="does-branch-to-branch-traffic-traverse-through-the-azure-virtual-wan"></a>분기에서 분기로의 트래픽은 Azure Virtual WAN을 통과하나요?

예.

### <a name="does-virtual-wan-require-expressroute-from-each-site"></a>Virtual WAN은 각 사이트의 ExpressRoute가 필요한가요?

아니요. 가상 WAN은 각 사이트에서 Express 경로를 요구 하지 않습니다. 사이트는 ExpressRoute 회로를 사용하여 공급자 네트워크에 연결할 수 있습니다. Express 경로를 사용 하 여 동일한 허브에 IPsec VPN 뿐만 아니라 가상 허브에 연결 된 사이트의 경우 가상 허브는 VPN과 Express 경로 사용자 간에 전송 연결을 제공 합니다.

### <a name="is-there-a-network-throughput-or-connection-limit-when-using-azure-virtual-wan"></a>Azure 가상 WAN을 사용할 때 네트워크 처리량 또는 연결 제한이 있나요?

네트워크 처리량은 가상 WAN 허브의 서비스 당입니다. 가상 wan을 원하는 만큼 사용할 수 있지만 각 가상 WAN은 지역별 1 개의 허브를 허용 합니다. 각 허브에서 VPN 집계 처리량은 최대 20Gbps, Express 경로 집계 처리량은 최대 20gbps, 사용자 VPN/지점 및 사이트 간 VPN 집계 처리량은 최대 20gbps입니다. 가상 허브의 라우터는 VNet 간 트래픽 흐름에 대해 최대 50 g b를 지원 하며, 가상 WAN 허브의 모든 Vnet에서 총 2000 VM 워크 로드를 가정 합니다.

VPN 사이트를 허브에 연결 하는 경우 연결을 사용 하 여 연결 합니다. 가상 WAN은 가상 허브 당 최대 1000의 연결 또는 2000 IPsec 터널을 지원 합니다. 원격 사용자가 가상 허브에 연결 하면 가상 허브의 P2S VPN gateway에 대해 선택한 대역폭 (scale unit)에 따라 최대 1만 명의 사용자를 지 원하는 P2S VPN gateway에 연결 됩니다.

### <a name="what-is-the-total-vpn-throughput-of-a-vpn-tunnel-and-a-connection"></a>VPN 터널 및 연결의 총 VPN 처리량이란?

허브의 총 VPN 처리량은 VPN 게이트웨이의 선택 된 배율 단위를 기준으로 최대 20gbps입니다. 처리량은 모든 기존 연결에서 공유됩니다. 연결의 각 터널은 최대 1Gbps를 지원할 수 있습니다.

### <a name="i-dont-see-the-20-gbps-setting-for-the-virtual-hub-in-portal-how-do-i-configure-that"></a>포털에서 가상 허브에 대 한 20Gbps 설정이 표시 되지 않습니다. 이를 구성하려면 어떻게 하나요?

포털에서 허브 내의 VPN Gateway로 이동하여 배율 단위를 클릭하고 적절한 설정으로 변경합니다.

### <a name="does-virtual-wan-allow-the-on-premises-device-to-utilize-multiple-isps-in-parallel-or-is-it-always-a-single-vpn-tunnel"></a>Virtual WAN은 온-프레미스 디바이스가 여러 ISP를 동시에 사용하는 것을 허용하나요, 아니면 항상 단일 VPN 터널인가요?

온-프레미스 장치 솔루션은 여러 터널의 트래픽을 Azure 가상 WAN 허브 (가상 허브의 VPN gateway)로 유도 하는 트래픽 정책을 적용할 수 있습니다.

### <a name="what-is-global-transit-architecture"></a>글로벌 전송 아키텍처란 무엇인가요?

글로벌 전송 아키텍처에 대한 자세한 내용은 [글로벌 전송 네트워크 아키텍처 및 Virtual WAN](../articles/virtual-wan/virtual-wan-global-transit-network-architecture.md)을 참조하세요.

### <a name="how-is-traffic-routed-on-the-azure-backbone"></a>Azure 백본에서 트래픽은 어떻게 라우팅되나요?

트래픽은 분기 디바이스 -> ISP -> Microsoft 네트워크 에지 -> Microsoft DC(허브 VNet) -> Microsoft 네트워크 에지 -> ISP -> 분기 디바이스 패턴을 따릅니다.

### <a name="in-this-model-what-do-you-need-at-each-site-just-an-internet-connection"></a>이 모델은 각 사이트에서 무엇이 필요합니까? 인터넷 연결만 있으면 되나요?

예. IPsec을 지원하는 인터넷 연결과 물리적 디바이스(가급적이면 Microsoft의 통합 [Virtual WAN 파트너](../articles/virtual-wan/virtual-wan-locations-partners.md))만 있으면 됩니다. 선택적으로 선호하는 디바이스에서 Azure로의 연결과 구성을 수동으로 관리할 수 있습니다.

### <a name="how-do-i-enable-default-route-00000-in-a-connection-vpn-expressroute-or-virtual-network"></a>어떻게 연결(VPN, ExpressRoute 또는 가상 네트워크)에서 기본 경로(0.0.0.0/0)를 사용하도록 설정할 수 있을까요?

연결의 플래그가 '사용'일 경우 가상 허브가 학습된 기본 경로를 가상 네트워크/사이트 간 VPN/ExpressRoute 연결에 전파할 수 있습니다. 사용자가 가상 네트워크 연결, VPN 연결 또는 ExpressRoute 연결을 편집할 때 이 플래그를 볼 수 있습니다. 사이트 또는 ExpressRoute 회로가 허브에 연결된 경우 기본적으로 이 플래그는 사용하지 않도록 설정됩니다. VNet을 가상 허브에 연결하기 위해 가상 네트워크 연결을 추가하면 기본적으로 사용하도록 설정됩니다. 기본 경로는 Virtual WAN 허브에서 시작되지 않습니다. 허브에 방화벽을 배포한 결과로 Virtual WAN 허브에서 학습했거나 다른 연결된 사이트에서 강제 터널링을 사용할 경우 기본 경로가 전파됩니다.

### <a name="how-does-the-virtual-hub-in-a-virtual-wan-select-the-best-path-for-a-route-from-multiple-hubs"></a>Virtual WAN의 가상 허브가 여러 허브의 경로에 가장 적합한 경로를 선택하는 방법

가상 허브가 여러 원격 허브에서 동일한 경로를 학습 하는 경우이를 결정 하는 순서는 다음과 같습니다.

1. 가장 긴 접두사 일치입니다.
2. Interhub를 통한 로컬 경로
3. BGP를 통한 고정 경로: 가상 허브 라우터를 통해 결정 되는 상황에 따라 결정 됩니다. 그러나 의사 결정 작성자가 BGP를 통해 경로를 보급 하거나 고정 주소 접두사를 제공 하는 VPN 게이트웨이 인 경우 BGP 경로를 통해 고정 경로를 선호 하는 것일 수 있습니다.
4. VPN을 통한 Express 경로: ER는 컨텍스트가 로컬 허브 인 경우 VPN을 통해 선호 됩니다. Express 경로 회로 간의 전송 연결은 Global Reach 통해서만 사용할 수 있습니다. 따라서 Express 경로 회로가 하나의 허브에 연결 되 고 다른 Express 경로 회로가 VPN 연결을 사용 하는 다른 허브에 연결 된 시나리오에서는 허브 간 시나리오에 VPN을 사용 하는 것이 좋습니다.
5. 경로 길이로.

### <a name="does-virtual-wan-hub-allow-connectivity-between-expressroute-circuits"></a>가상 WAN 허브는 Express 경로 회로 간 연결을 허용 합니다.

ER 간 전송은 항상 글로벌 연결을 통해 전송 됩니다. 가상 허브 게이트웨이는 DC 또는 Azure 지역에 배포 됩니다. 두 개의 Express 경로 회로가 글로벌 연결을 통해 연결 되는 경우에 지 라우터에서 가상 허브 DC로의 모든 방식으로 트래픽을 보낼 필요가 없습니다.

### <a name="is-there-a-concept-of-weight-in-azure-virtual-wan-circuits-or-vpn-connections"></a>Azure 가상 WAN 회로 또는 VPN 연결에 가중치 개념이 있나요?

여러 Express 경로 회로가 가상 허브에 연결 된 경우 연결의 라우팅 가중치는 가상 허브에서 Express 경로에 대 한 메커니즘을 제공 하 여 다른 회로에 대 한 회로를 선호 합니다. VPN 연결에서 가중치를 설정 하는 메커니즘은 없습니다. Azure는 항상 단일 허브 내에서 VPN 연결을 통해 Express 경로 연결을 선호 합니다.

### <a name="when-two-hubs-hub-1-and-2-are-connected-and-there-is-an-expressroute-circuit-connected-as-a-bow-tie-to-both-the-hubs-what-is-the-path-for-a-vnet-connected-to-hub-1-to-reach-a-vnet-connected-in-hub-2"></a>두 허브 (허브 1 및 2)가 연결 되 고 두 허브에 모두 활로 연결 된 Express 경로 회로가 있는 경우 허브 1에 연결 된 VNet에서 허브 2에 연결 된 VNet에 연결 하는 경로는 무엇입니까?

현재 동작은 VNet 간 연결에 대 한 허브와 허브를 통한 Express 경로 회로 경로를 선호 하는 것입니다. 그러나이 방법은 가상 WAN 설정에서 권장 되지 않습니다. 가상 WAN 팀은 Express 경로에 대 한 허브에서 허브에 대 한 기본 설정을 사용 하도록 수정 하는 작업을 수행 합니다. 여러 Express 경로 회로 (다른 공급자)가 하나의 허브에 연결 하 고 지역 간 트래픽 흐름에 대해 가상 WAN이 제공 하는 허브-허브 연결을 사용 하는 것이 좋습니다.

### <a name="is-there-support-for-ipv6-in-virtual-wan"></a>Virtual WAN에서 IPv6를 지원하나요?

IPv6은 가상 WAN 허브 및 게이트웨이에서 지원 되지 않습니다. I p v 6을 지 원하는 VNet이 있고 VNet을 가상 WAN에 연결 하려는 경우이 시나리오는 현재 지원 되지 않습니다.

### <a name="what-are-the-differences-between-the-virtual-wan-types-basic-and-standard"></a>Virtual WAN 유형(기본 및 표준) 사이의 차이점은 무엇인가요?

[기본 및 표준 가상 wan](../articles/virtual-wan/virtual-wan-about.md#basicstandard)을 참조 하세요. 가격 책정에 대해서는 [가격 책정](https://azure.microsoft.com/pricing/details/virtual-wan/) 페이지를 참조하세요.