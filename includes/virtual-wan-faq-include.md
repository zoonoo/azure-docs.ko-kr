---
title: 포함 파일
description: 포함 파일
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: include
ms.date: 07/10/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 8c3f727c6154a0364f151d22000d2684c361676a
ms.sourcegitcommit: 04fc1781fe897ed1c21765865b73f941287e222f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/13/2018
ms.locfileid: "39037213"
---
### <a name="what-is-the-difference-between-an-azure-virtual-network-gateway-vpn-gateway-and-an-azure-virtual-wan-vpngateway"></a>Azure 가상 네트워크 게이트웨이(VPN Gateway)와 Azure Virtual WAN vpngateway 간의 차이점은 무엇입니까?

Virtual WAN은 대규모 사이트 간 연결을 제공하고 처리량, 확장성 및 사용 편의성을 위해 빌드됩니다. CPE 분기 장치는 Azure Virtual WAN으로 자동 프로비전하고 연결합니다. 이러한 장치는 SD-WAN 및 VPN 파트너의 증가하는 에코시스템에서 사용할 수 있습니다.

### <a name="which-device-providers-virtual-wan-partners-are-supported-at-launch-time"></a>장치 공급자(Virtual WAN 파트너)는 시작 시 지원되나요? 

이 때 Citrix와 Riverbed는 완전히 자동화된 Virtual WAN 환경을 지원합니다. Nokia Nuage, Palo Alto 및 Checkpoint를 포함한 더 많은 파트너가 앞으로 온보딩될 예정입니다. 자세한 내용은 [Virtual WAN 파트너](https://aka.ms/virtualwan)를 참조하세요.

### <a name="am-i-required-to-use-a-preferred-partner-device"></a>원하는 파트너 장치를 사용해야 하나요?

아니요. IKEv2 IPsec 지원에 대한 미리 보기 요구 사항을 준수하는 모든 VPN 지원 장치를 사용할 수 있습니다.

### <a name="how-do-virtual-wan-partners-automate-connectivity-with-azure-virtual-wan"></a>Virtual WAN 파트너는 Azure Virtual WAN과 연결을 어떻게 자동화하나요?

일반적으로 소프트웨어 정의 연결 솔루션은 컨트롤러 또는 장치 프로비저닝 센터를 사용하여 해당 분기 장치를 관리합니다. 컨트롤러는 Azure API를 사용하여 Azure Virtual WAN에 대한 연결을 자동화할 수 있습니다. 작동 방법에 대한 자세한 내용은 [Virtual WAN 파트너 자동화](../articles/virtual-wan/virtual-wan-configure-automation-providers.md)를 참조하세요.

### <a name="does-virtual-wan-change-any-existing-connectivity-features"></a>Virtual WAN은 기존 연결 기능을 변경하지 않나요?   

기존 Azure 연결 기능에 아무런 변경 내용이 없습니다.

### <a name="are-there-new-resource-manager-resources-available-for-virtual-wan"></a>Virtual WAN에 사용 가능한 새 Resource Manager 리소스가 있나요?
  
예, Virtual WAN은 새 Resource Manager 리소스를 소개합니다. 자세한 내용은 [개요](https://go.microsoft.com/fwlink/p/?LinkId=2004389)를 참조하세요.

### <a name="are-there-any-special-requirements-to-join-the-preview"></a>미리 보기에 조인할 특별한 요구 사항이 있나요? 

Azure Virtual WAN을 구성하려면 그 전에 먼저 미리 보기에서 구독을 등록해야 합니다. 등록하려면 구독 ID로 <azurevirtualwan@microsoft.com>에 이메일을 전송합니다. 구독이 등록되면 이메일 회신을 받게 됩니다. 구독이 등록되었다는 확인을 받을 때까지 포털에서 Virtual WAN을 사용할 수 없습니다.

고려 사항:

* 미리 보기는 Azure 공용 지역으로만 제한됩니다.
* 가상 허브당 최대 100개의 연결이 지원됩니다. 각 연결은 활성-활성 구성에 있는 2개의 터널로 이루어져 있습니다. 터널은 Azure Virtual Hub vpngateway에서 종료합니다.
* 다음과 같은 경우에 이 미리 보기를 사용하는 것이 좋습니다.
  * 가상 허브당 1Gbps 미만의 집계 대역폭을 배포하려고 합니다.
  * 경로 기반 구성 및 IKEv2 IPsec 연결을 지원하는 VPN 장치가 있습니다.
  * 출시 파트너에서 SD-WAN 및 운영 분기 장치를 사용하여 탐색하려고 합니다(Riverbed 및 Citrix).<br>또는<br>온-프레미스 장치의 구성 관리를 포함하는 분기-분기 및 분기-Azure 연결을 설정하려고 합니다. (자체 구성)

### <a name="is-global-vnet-peering-supported-with-azure-virtual-wan"></a>글로벌 VNet 피어링은 Azure Virtual WAN을 사용하여 지원되나요? 

 아니요.

### <a name="can-spoke-vnets-connected-to-a-virtual-hub-communicate-with-each-other"></a>가상 허브에 연결된 VNet 스포크는 서로 통신할 수 있나요?

예. 가상 허브에 연결된 스포크 간에 VNet 피어링을 직접 수행할 수 있습니다. 자세한 내용은 [Virtual Network 피어링](../articles/virtual-network/virtual-network-peering-overview.md)을 참조하세요.

### <a name="can-i-deploy-and-use-my-favorite-network-virtual-appliance-in-an-nva-vnet-with-azure-virtual-wan"></a>Azure Virtual WAN과 함께 즐겨 찾는 네트워크 가상 어플라이언스(NVA VNet에서)를 배포 및 사용할 수 있나요?

예, Azure Virtual WAN에 즐겨 찾는 NVA(네트워크 가상 어플라이언스) VNet을 연결할 수 있지만 GA에 있는 허브에서 라우팅 기능이 필요합니다. NVA VNet에 연결된 모든 스포크는 가상 허브에 또한 연결되어야 합니다. 

### <a name="can-an-nva-vnet-have-a-virtual-network-gateway"></a>NVA VNet에서 가상 네트워크 게이트웨이를 가질 수 있나요?

아니요. 가상 허브에 연결되어 있는 경우 NVA VNet에서 가상 네트워크 게이트웨이를 가질 수 없습니다. 

### <a name="is-there-support-for-bgp"></a>BGP에 대한 지원이 있습니까?

예, BGP가 지원됩니다. NVA VNet의 경로가 적절하게 보급되었는지 확인하려면 스포크는 차례로 가상 허브에 연결된 NVA VNet에 연결된 경우 BGP를 비활성화해야 합니다. 또한 가상 허브에 스포크 VNet을 연결합니다.

### <a name="can-i-direct-traffic-using-udr-in-the-virtual-hub"></a>가상 허브에서 UDR을 사용하여 트래픽을 보낼 수 있나요?

UDR 및 라우팅 기능은 GA에서 사용할 수 있습니다.

### <a name="is-there-any-licensing-or-pricing-information-for-virtual-wan"></a>Virtual WAN에 대한 라이선스 또는 가격 책정 정보가 있나요?
 
미리 보기 동안 추가 비용은 없습니다. 현재 미리 보기 동안 [Azure VPN 및 송신 가격 책정](https://azure.microsoft.com/pricing/details/vpn-gateway/)이 적용됩니다.