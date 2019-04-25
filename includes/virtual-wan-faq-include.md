---
title: 포함 파일
description: 포함 파일
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: include
ms.date: 03/18/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: a89a5d753eaa241b11eb4c7eed9500c9715d405d
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/17/2019
ms.locfileid: "59737196"
---
### <a name="what-is-the-difference-between-an-azure-virtual-network-gateway-vpn-gateway-and-an-azure-virtual-wan-vpngateway"></a>Azure 가상 네트워크 게이트웨이(VPN Gateway)와 Azure Virtual WAN vpngateway 간의 차이점은 무엇입니까?

Virtual WAN은 대규모 사이트 간 연결을 제공하며 처리량, 확장성 및 사용 편의성을 높여 줍니다. ExpressRoute 연결과 지점 및 사이트 간 연결 기능은 현재 미리 보기 상태입니다. CPE 분기 디바이스는 Azure Virtual WAN으로 자동 프로비저닝하고 연결합니다. 이러한 디바이스는 SD-WAN 및 VPN 파트너의 증가하는 에코시스템에서 사용할 수 있습니다. [선호 파트너 목록](https://go.microsoft.com/fwlink/p/?linkid=2019615)을 참조하세요.

### <a name="which-device-providers-virtual-wan-partners-are-supported-at-launch-time"></a>디바이스 공급자(Virtual WAN 파트너)는 시작 시 지원되나요?

현재 많은 파트너가 완전히 자동화된 Virtual WAN 환경을 지원합니다. 자세한 내용은 [Virtual WAN 파트너](https://go.microsoft.com/fwlink/p/?linkid=2019615)를 참조하세요. 

### <a name="what-are-the-virtual-wan-partner-automation-steps"></a>Virtual WAN 파트너 자동화 단계란?

자세한 내용은 [Virtual WAN 파트너 자동화](../articles/virtual-wan/virtual-wan-configure-automation-providers.md)를 참조하세요.

### <a name="am-i-required-to-use-a-preferred-partner-device"></a>원하는 파트너 디바이스를 사용해야 하나요?

 아니요. IKEv2/IKEv1 IPsec 지원에 대한 Azure 요구 사항을 준수하는 모든 VPN 지원 디바이스를 사용할 수 있습니다.

### <a name="how-do-virtual-wan-partners-automate-connectivity-with-azure-virtual-wan"></a>Virtual WAN 파트너는 Azure Virtual WAN과 연결을 어떻게 자동화하나요?

일반적으로 소프트웨어 정의 연결 솔루션은 컨트롤러 또는 디바이스 프로비저닝 센터를 사용하여 해당 분기 디바이스를 관리합니다. 컨트롤러는 Azure API를 사용하여 Azure Virtual WAN에 대한 연결을 자동화할 수 있습니다. 자세한 내용은 Virtual WAN 파트너 자동화를 참조하세요.

### <a name="does-virtual-wan-change-any-existing-connectivity-features"></a>Virtual WAN은 기존 연결 기능을 변경하지 않나요?   

기존 Azure 연결 기능에 아무런 변경 내용이 없습니다.

### <a name="are-there-new-resource-manager-resources-available-for-virtual-wan"></a>Virtual WAN에 사용 가능한 새 Resource Manager 리소스가 있나요?
  
예, Virtual WAN은 새 Resource Manager 리소스를 소개합니다. 자세한 내용은 [개요](https://go.microsoft.com/fwlink/p/?LinkId=2004389)를 참조하세요.

### <a name="how-many-vpn-devices-can-connect-to-a-single-hub"></a>단일 허브에 연결할 수 있는 VPN 디바이스의 수는 몇 개인가요?

가상 허브당 최대 1,000개의 연결이 지원됩니다. 각 연결은 활성-활성 구성에 있는 두 개 터널로 구성됩니다. 터널은 Azure Virtual Hub vpngateway에서 종료합니다.

### <a name="can-the-on-premises-vpn-device-connect-to-multiple-hubs"></a>온-프레미스 VPN 디바이스가 여러 허브에 연결할 수 있나요?

예. 처음에는 트래픽이 온-프레미스 디바이스에서 가장 가까운 Microsoft Edge를 통해 가상 허브로 이동합니다.

### <a name="is-global-vnet-peering-supported-with-azure-virtual-wan"></a>글로벌 VNet 피어링은 Azure Virtual WAN을 사용하여 지원되나요? 

  아니요.

### <a name="can-spoke-vnets-connected-to-a-virtual-hub-communicate-with-each-other"></a>가상 허브에 연결된 VNet 스포크는 서로 통신할 수 있나요?

예. 스포크 VNet은 Virtual Network 피어링을 통해 직접 통신할 수 있습니다. 그러나 허브를 통해 전이적으로 통신하는 VNet은 지원되지 않습니다. 자세한 내용은 [Virtual Network 피어링](../articles/virtual-network/virtual-network-peering-overview.md)을 참조하세요.

### <a name="can-i-deploy-and-use-my-favorite-network-virtual-appliance-in-an-nva-vnet-with-azure-virtual-wan"></a>Azure Virtual WAN과 함께 즐겨 찾는 네트워크 가상 어플라이언스(NVA VNet에서)를 배포 및 사용할 수 있나요?

예. 원하는 NVA(네트워크 가상 어플라이언스) VNet을 Azure Virtual WAN에 연결할 수 있습니다. 이렇게 하려면 먼저 허브 가상 네트워크 연결을 사용하여 네트워크 가상 어플라이언스 VNet을 허브에 연결합니다. 그런 다음 가상 어플라이언스를 가리키는 다음 홉을 사용하여 가상 허브 경로를 만듭니다. 가상 허브 경로 테이블에 여러 경로를 적용할 수 있습니다. NVA VNet에 연결된 모든 스포크는 가상 허브에도 추가로 연결해야 합니다. 그래야 스포크 VNet 경로가 온-프레미스 시스템으로 전파됩니다.

### <a name="can-an-nva-vnet-have-a-virtual-network-gateway"></a>NVA VNet에서 가상 네트워크 게이트웨이를 가질 수 있나요?

 아니요. 가상 허브에 연결되어 있는 경우 NVA VNet에서 가상 네트워크 게이트웨이를 가질 수 없습니다. 

### <a name="is-there-support-for-bgp"></a>BGP에 대한 지원이 있습니까?

예, BGP가 지원됩니다. VPN 사이트를 만들 때 BGP 매개 변수를 제공할 수 있습니다. 이는 해당 사이트에 대해 Azure에서 생성된 모든 연결이 BGP에 대해 활성화됨을 의미합니다. 또한 NVA와 함께 VNet이 있고 이 NVA VNet이 Virtual WAN 허브에 연결된 경우 NVA VNet의 경로가 적절하게 보급되도록 하기 위해 NVA VNet에 연결된 스포크는 BGP를 사용하지 않도록 설정해야 합니다. 또한 스포크 VNet 경로가 온-프레미스 시스템으로 전파되도록 이러한 스포크 VNet을 가상 허브 VNet에 연결합니다.

### <a name="can-i-direct-traffic-using-udr-in-the-virtual-hub"></a>가상 허브에서 UDR을 사용하여 트래픽을 보낼 수 있나요?

예. 가상 허브 경로 테이블을 사용하여 트래픽을 VNet으로 보낼 수 있습니다.

### <a name="is-there-any-licensing-or-pricing-information-for-virtual-wan"></a>Virtual WAN에 대한 라이선스 또는 가격 책정 정보가 있나요?
 
예. [가격 책정](https://azure.microsoft.com/pricing/details/virtual-wan/) 페이지를 참조하세요.

### <a name="how-do-i-calculate-price-of-a-hub"></a>허브의 가격을 계산하는 방법은?
 
허브의 서비스에 대해 지불합니다. 예를 들어, Azure Virtual WAN에 연결해야 하는 10개 분기 또는 온-프레미스 디바이스는 허브의 VPN 엔드포인트에 연결하는 것을 의미합니다. 1 배율 단위의 VPN이 500Mbps면 이는 시간당 $0.361로 청구됩니다. 각 연결은 시간당 $0.08로 청구됩니다. 10개 연결의 경우 시간당 총 서비스 요금은 $0.361 + 시간당 $.8입니다. Azure에서 발생하는 트래픽에 대한 데이터 요금이 적용됩니다. 

### <a name="how-do-new-partners-that-are-not-listed-in-your-launch-partner-list-get-onboarded"></a>출시 파트너 목록에 없는 새 파트너는 어떻게 온보딩하나요?

azurevirtualwan@microsoft.com에 전자 메일을 보냅니다. IKEv1 또는 IKEv2 IPsec 연결용으로 프로비전 가능한 디바이스가 있는 파트너가 적합합니다.

### <a name="what-if-a-device-i-am-using-is-not-in-the-virtual-wan-partner-list-can-i-still-use-it-to-connect-to-azure-virtual-wan-vpn"></a>사용 중인 디바이스가 Virtual WAN 파트너 목록에 없는 경우에는 어떻게 하나요? 계속 Azure Virtual WAN VPN에 연결할 수 있나요?

네, 디바이스가 IPsec IKEv1 또는 IKEv2를 지원하는 경우 연결할 수 있습니다. Virtual WAN 파트너는 디바이스에서 Azure VPN 엔드포인트로의 연결을 자동화합니다. 이는 ‘분기 정보 업로드’, ‘IPsec 및 구성’ 및 ‘연결성’과 같은 단계를 자동화하는 것을 의미합니다. 디바이스가 Virtual WAN 파트너 에코시스템에서 온 것이 아니기 때문에 IPsec 연결을 설정하려면 Azure 구성을 가져오고 디바이스를 업데이트하는 작업을 수동으로 많이 수행해야 합니다. 

### <a name="is-it-possible-to-construct-azure-virtual-wan-with-a-resource-manager-template"></a>Resource Manager 템플릿을 사용하여 Azure Virtual WAN을 만들 수 있나요?

[Azure 빠른 시작 템플릿](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Network)을 사용하면 허브와 vpnsite가 각각 하나씩 포함된 단일 Virtual WAN의 단순한 구성을 만들 수 있습니다. Virtual WAN은 기본적으로 REST 또는 Portal 기반 서비스입니다.

### <a name="is-branch-to-branch-connectivity-allowed-in-virtual-wan"></a>Virtual WAN에서 분기-분기 연결이 가능한가요?

예. Virtual WAN에서는 VPN 및 VPN에서 ExpressRoute로의 분기 간 연결을 사용할 수 있습니다. 사이트 간 VPN은 일반 공급 상태인 반면 ExpressRoute/지점 및 사이트 간 VPN은 현재 미리 보기 상태입니다.

### <a name="does-branch-to-branch-traffic-traverse-through-the-azure-virtual-wan"></a>분기-분기 트래픽은 Azure Virtual WAN을 통과하나요?

예.

### <a name="how-is-virtual-wan-different-from-the-existing-azure-virtual-network-gateway"></a>Virtual WAN은 기존 Azure Virtual Network 게이트웨이와 어떻게 다른가요?

Virtual Network 게이트웨이 VPN은 터널 30개로 제한됩니다. 연결에는 대규모 VPN을 위한 Virtual WAN을 사용해야 합니다. 중서부 지역을 제외한 모든 지역의 허브에서 2Gbps의 최대 1,000개 분기 연결을 연결할 수 있습니다. 중서부 지역의 경우 20Gbps를 사용할 수 있습니다. 앞으로 추가 지역에는 20Gbps로 출시됩니다. 연결은 온-프레미스 VPN 디바이스에서 가상 허브로의 활성-활성 터널입니다. 지역당 하나의 허브만 있을 수 있습니다. 즉 허브에서 1,000개가 넘는 분기를 연결할 수 있습니다.

### <a name="how-is-virtual-wan-supporting-sd-wan-devices"></a>Virtual WAN이 SD-WAN 디바이스를 지원하는 방법

Virtual WAN 파트너는 Azure VPN 엔드포인트로의 IPsec 연결을 자동화합니다. Virtual WAN 파트너가 SD-WAN 공급 기업인 경우 SD-WAN 컨트롤러가 자동화 및 Azure VPN 엔드포인트로의 IPsec 연결을 관리함을 의미합니다. 독점적인 SD-WAN 기능을 위해 Azure VPN 대신 SD-WAN 디바이스가 자체 엔드포인트를 필요로 하는 경우, Azure VNet에 SD-WAN 엔드포인트를 배포하고 Azure Virtual WAN과 공존할 수 있습니다.

### <a name="does-this-virtual-wan-require-expressroute-from-each-site"></a>이 Virtual WAN은 각 사이트의 ExpressRoute가 필요한가요?

아니요, Virtual WAN은 각 사이트의 ExpressRoute가 필요하지 않습니다. 디바이스에서 Azure Virtual WAN 허브로의 인터넷 링크를 통해 표준 IPsec 사이트 간 연결을 사용합니다. 사이트는 ExpressRoute 회로를 사용하여 공급자 네트워크에 연결할 수 있습니다. 가상 허브(미리 보기)에서 ExpressRoute를 사용하여 연결된 사이트의 경우 VPN과 ExpressRoute 간의 분기 간 트래픽 흐름을 사용할 수 있습니다. 

### <a name="is-there-a-network-throughput-limit-when-using-azure-virtual-wan"></a>Azure Virtual WAN을 사용하는 경우 네트워크 처리량 제한이 있나요?

허브에서 분기 수는 허브/지역당 1,000개 연결, 속도는 총 2Gbps로 제한됩니다. 단, 미국 중서부 지역은 총 20Gbps입니다. 앞으로 다른 지역에는 20Gbps로 출시됩니다.

### <a name="does-virtual-wan-allow-the-on-premises-device-to-utilize-multiple-isps-in-parallel-or-is-it-always-a-single-vpn-tunnel"></a>Virtual WAN은 온-프레미스 디바이스가 여러 ISP를 병렬로 사용하는 것을 허용하나요 아니면 항상 단일 VPN 터널인가요?

Virtual WAN VPN으로 들어오는 연결은 항상 분기에서 사용 가능한 링크를 사용하는 활성-활성 터널입니다(동일한 허브/지역 내 복원력을 위해). 이 링크는 온-프레미스 분기에서 ISP 링크일 수 있습니다. Virtual WAN은 복수의 ISP를 병렬로 설정하는 특별한 논리를 제공하지 않습니다. 분기에서 ISP 간 장애 조치(failover)를 관리하는 것은 완전히 분기 중심의 네트워크 운영입니다. 즐겨찾는 SD-WAN 솔루션을 사용하여 분기에서 경로 선택을 수행할 수 있습니다.

### <a name="how-is-traffic-routed-on-the-azure-backbone"></a>Azure 백본에서 트래픽은 어떻게 라우팅되나요?

트래픽은 분기 디바이스 -> ISP -> Microsoft Edge -> Microsoft DC(Hub VNet) -> Microsoft Edge -> ISP -> 분기 디바이스 패턴을 따릅니다.

### <a name="in-this-model-what-do-you-need-at-each-site-just-an-internet-connection"></a>이 모델은 각 사이트에서 무엇이 필요합니까? 인터넷 연결만 있으면 되나요?

예. IPsec을 지원하는 인터넷 연결과 물리적 디바이스(이왕이면 Microsoft의 통합 [파트너](https://go.microsoft.com/fwlink/p/?linkid=2019615))만 있으면 됩니다. 필요한 경우 선호 디바이스에서 Azure로의 연결과 구성을 수동으로 관리할 수 있습니다.
