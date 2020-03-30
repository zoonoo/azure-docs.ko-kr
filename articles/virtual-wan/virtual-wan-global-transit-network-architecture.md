---
title: '아키텍처: 글로벌 대중 교통 네트워크 아키텍처'
titleSuffix: Azure Virtual WAN
description: 가상 WAN을 위한 글로벌 대중교통 네트워크 아키텍처에 대해 알아보기
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: article
ms.date: 02/06/2020
ms.author: cherylmc
ms.openlocfilehash: 17d0e678008c76da32f20562aa795e83e49c80e4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77064974"
---
# <a name="global-transit-network-architecture-and-virtual-wan"></a>글로벌 대중 교통 네트워크 아키텍처 및 가상 WAN

최신 기업은 클라우드 및 온-프레미스 전반에 걸쳐 하이퍼 분산 애플리케이션, 데이터 및 사용자 간에 유비쿼터스 연결을 요구합니다. 클라우드 중심의 현대식 글로벌 엔터프라이즈 IT 공간을 통합, 연결 및 제어하기 위해 기업이 글로벌 대중 교통 네트워크 아키텍처를 채택하고 있습니다.

글로벌 대중 교통 네트워크 아키텍처는 클라우드 호스팅 네트워크 '허브'가 다양한 유형의 '스포크'에 분산될 수 있는 엔드포인트 간의 전이 연결을 가능하게 하는 고전적인 허브 및 스포크 연결 모델을 기반으로 합니다.

이 모델에서 스포크는 다음과 같은 것일 수 있습니다.
* 가상 네트워크(VNet)
* 물리적 분기 사이트
* 원격 사용자
* 인터넷

![허브 및 스포크](./media/virtual-wan-global-transit-network-architecture/figure1.png)

**그림 1: 글로벌 대중교통 허브 및 스포크 네트워크**

그림 1은 지리적으로 분산된 사용자, 물리적 사이트 및 VNet이 클라우드에서 호스팅되는 네트워킹 허브를 통해 상호 연결된 글로벌 전송 네트워크의 논리적 보기를 보여 주며, 이 네트워크는 다음과 같습니다. 이 아키텍처를 사용하면 네트워킹 끝점 간에 논리적인 원홉 전송 연결을 사용할 수 있습니다.

## <a name="global-transit-network-with-virtual-wan"></a><a name="globalnetworktransit"></a>가상 WAN을 통한 글로벌 운송 네트워크

Azure 가상 WAN은 Microsoft에서 관리하는 클라우드 네트워킹 서비스입니다. 이 서비스가 구성하는 모든 네트워킹 구성 요소는 Microsoft에서 호스팅하고 관리합니다. 가상 WAN에 대한 자세한 내용은 [가상 WAN 개요](virtual-wan-about.md) 문서를 참조하십시오.

Azure Virtual WAN은 VNet, 분기 사이트, SaaS 및 PaaS 응용 프로그램 및 사용자에서 전 세계에 분산된 클라우드 워크로드 집합 간에 유비쿼터스, 모든 연결을 사용하도록 설정하여 글로벌 전송 네트워크 아키텍처를 허용합니다.

![Azure 가상 WAN](./media/virtual-wan-global-transit-network-architecture/figure2.png)

**그림 2: 글로벌 교통 네트워크 및 가상 WAN**

Azure 가상 WAN 아키텍처에서 가상 WAN 허브는 Azure 리전에서 프로비전되며, 이 허브는 분기, VNet 및 원격 사용자를 연결하도록 선택할 수 있습니다. 물리적 분기 사이트는 프리미엄 익스프레스루트 또는 사이트-사이트 VPN을 통해 허브에 연결되고, VNet은 VNet 연결을 통해 허브에 연결되며, 원격 사용자는 사용자 VPN(지점 간 VPN)을 사용하여 허브에 직접 연결할 수 있습니다. 또한 Virtual WAN은 한 리전의 VNet을 다른 리전의 가상 WAN 허브에 연결할 수 있는 지역 간 VNet 연결을 지원합니다.

스포크(분기, VNet, 사용자)가 가장 많은 리전에서 단일 가상 WAN 허브를 만든 다음 다른 지역에 있는 스포크를 허브에 연결하여 가상 WAN을 설정할 수 있습니다. 이 옵션은 엔터프라이즈 실장 공간이 대부분 원격 스포크가 있는 한 지역에 있는 경우에 적합합니다.  
  
## <a name="hub-to-hub-connectivity"></a><a name="hubtohub"></a>허브 간 연결

엔터프라이즈 클라우드 설치 공간은 여러 클라우드 지역에 걸쳐 있을 수 있으며 물리적 사이트 및 사용자에게 가장 가까운 리전에서 클라우드에 액세스하는 것이 최적입니다. 글로벌 대중 교통 네트워크 아키텍처의 핵심 원칙 중 하나는 모든 클라우드와 온-프레미스 네트워크 엔드포인트 간의 지역 간 연결을 활성화하는 것입니다. 즉, 한 리전의 클라우드에 연결된 분기의 트래픽은 [Azure Global Network에서](https://azure.microsoft.com/global-infrastructure/global-network/)사용하도록 설정된 허브 간 연결을 사용하여 다른 지역의 다른 분기 또는 VNet에 도달할 수 있습니다.

![교차 영역](./media/virtual-wan-global-transit-network-architecture/figure3.png)

**그림 3: 가상 WAN 지역 간 연결**

단일 가상 WAN에서 여러 허브를 사용하도록 설정하면 허브-허브 링크를 통해 허브가 자동으로 상호 연결되므로 여러 지역에 분산되어 있는 브랜치와 Vnet 간의 전역 연결을 활성화할 수 있습니다. 

또한 동일한 가상 WAN의 일부인 허브는 다른 지역 액세스 및 보안 정책과 연결할 수 있습니다. 자세한 내용은 이 문서의 보안 [및 정책 제어를](#security) 참조하세요.

## <a name="any-to-any-connectivity"></a><a name="anytoany"></a>모든 연결

글로벌 대중 교통 네트워크 아키텍처를 통해 가상 WAN 허브를 통해 모든 연결을 수행할 수 있습니다. 이 아키텍처는 빌드 및 유지 관리가 더 복잡한 스포크 간에 전체 메시 또는 부분 메시 연결이 필요하지 않습니다. 또한 허브 및 스포크 대 메시 네트워크의 라우팅 제어를 구성하고 유지 관리하는 것이 더 쉽습니다.

전역 아키텍처의 컨텍스트에서 모든 연결 기능을 사용하면 전 세계에 분산된 사용자, 분기, 데이터 센터, VNet 및 응용 프로그램이 있는 기업이 "전송" 허브를 통해 서로 연결할 수 있습니다. Azure Virtual WAN은 글로벌 전송 시스템 역할을 합니다.

![어떤](./media/virtual-wan-global-transit-network-architecture/figure4.png)

**그림 4: 가상 WAN 트래픽 경로**

Azure Virtual WAN은 다음과 같은 전역 전송 연결 경로를 지원합니다. 괄호 안에 있는 문자는 그림 4에 매핑됩니다.

* 브랜치-VNet (a)
* 브랜치 간(b)
  * 익스프레스루트 글로벌 리치 및 가상 WAN
* 원격 사용자-VNet(c)
* 원격 사용자-분기(d)
* VNet-대 VNet(e)
* 분기-허브-허브-지점(f)
* 지점-허브-VNet(g)
* VNet-hub-hub-to-VNet(h)

### <a name="branch-to-vnet-a-and-branch-to-vnet-cross-region-g"></a>브랜치-VNet(a) 및 브랜치-VNet 교차 영역(g)

분기-VNetAzureAzure 가상 WAN에서 지원하는 기본 경로입니다. 이 경로를 사용하면 Azure VNet에 배포된 Azure IAAS 엔터프라이즈 워크로드에 분기를 연결할 수 있습니다. 지점은 ExpressRoute 또는 사이트 간 VPN을 통해 가상 WAN에 연결할 수 있습니다. 트래픽은 VNet 연결을 통해 가상 WAN 허브에 연결된 VNet으로 전송됩니다. Virtual WAN은 자동으로 분기 사이트로 게이트웨이 [전송을](../virtual-network/virtual-network-peering-overview.md#gateways-and-on-premises-connectivity) 활성화하기 때문에 가상 WAN에 명시적 게이트웨이 전송이 필요하지 않습니다. [SD-WAN](virtual-wan-configure-automation-providers.md) CPE를 가상 WAN에 연결하는 방법에 대한 가상 WAN 파트너 문서를 참조하십시오.

### <a name="expressroute-global-reach-and-virtual-wan"></a>익스프레스루트 글로벌 리치 및 가상 WAN

ExpressRoute는 온-프레미스 네트워크를 Microsoft 클라우드에 연결하는 개인적이고 탄력적인 방법입니다. 가상 WAN은 익스프레스 루트 회로 연결을 지원합니다. 분기 사이트를 고속 경로로 가상 WAN에 연결하려면 1) 프리미엄 회로 2) 회로가 전역 도달 사용 가능한 위치에 있어야 합니다.

익스프레스루트 글로벌 리치는 익스프레스루트의 추가 기능입니다. 글로벌 리치를 사용하면 ExpressRoute 회로를 함께 연결하여 온-프레미스 네트워크 간에 개인 네트워크를 만들 수 있습니다. ExpressRoute를 사용하여 Azure 가상 WAN에 연결된 분기는 서로 통신하기 위해 ExpressRoute 전역 도달이 필요합니다.

이 모델에서는 ExpressRoute를 사용하여 가상 WAN 허브에 연결된 각 분기가 분기-VNet 경로를 사용하여 VNet에 연결할 수 있습니다. ExpressRoute 전역 도달을 사용하면 Azure WAN을 통해 보다 최적의 경로를 사용할 수 있으므로 지점 간 트래픽은 허브를 통과하지 않습니다.

### <a name="branch-to-branch-b-and-branch-to-branch-cross-region-f"></a>분기 간(b) 및 분기 간 교차 영역(f)

분기는 ExpressRoute 회로 및/또는 사이트 간 VPN 연결을 사용하여 Azure 가상 WAN 허브에 연결할 수 있습니다. 분기와 가장 가까운 지역에 있는 가상 WAN 허브에 분기를 연결할 수 있습니다.

이 옵션을 사용하면 기업에서 Azure 백본을 활용하여 분기를 연결할 수 있습니다. 그러나 이 기능을 사용할 수 있더라도 개인 WAN을 사용하여 Azure Virtual WAN과 분기를 연결하는 이점을 고려해야 합니다.  

### <a name="remote-user-to-vnet-c"></a>원격 사용자-VNet(c)

원격 사용자 클라이언트에서 가상 WAN으로의 지점 간 연결을 사용하여 Azure에 대한 직접 보안 원격 액세스를 활성화할 수 있습니다. 엔터프라이즈 원격 사용자는 더 이상 회사 VPN을 사용하여 클라우드에 머리를 고정할 필요가 없습니다.

### <a name="remote-user-to-branch-d"></a>원격 사용자-분기(d)

원격 사용자-분기 경로를 사용하면 클라우드를 통해 전송하여 Azure 액세스 온-프레미스 워크로드 및 응용 프로그램에 대한 지점 간 연결을 사용하는 원격 사용자를 사용할 수 있습니다. 이 경로는 원격 사용자가 Azure 및 온-프레미스에 배포된 워크로드에 액세스할 수 있는 유연성을 제공합니다. 기업은 Azure Virtual WAN에서 중앙 클라우드 기반 보안 원격 액세스 서비스를 활성화할 수 있습니다.

### <a name="vnet-to-vnet-transit-e-and-vnet-to-vnet-cross-region-h"></a>VNet-VNet 전송(e) 및 VNet-VNet 교차 영역(h)

VNet-VNet 전송을 사용하면 VNet이 서로 연결하여 여러 VNet에서 구현되는 다중 계층 응용 프로그램을 상호 연결할 수 있습니다. 선택적으로 VNet 피어링을 통해 VNet을 서로 연결할 수 있으며 VWAN 허브를 통한 전송이 필요하지 않은 일부 시나리오에 적합할 수 있습니다.

## <a name="security-and-policy-control"></a><a name="security"></a>보안 및 정책 제어

Azure Virtual WAN 허브는 하이브리드 네트워크의 모든 네트워킹 끝점을 상호 연결하며 잠재적으로 모든 대중 교통 네트워크 트래픽을 볼 수 있습니다. VWAN 허브 내에 Azure 방화벽을 배포하여 클라우드 기반 보안, 액세스 및 정책 제어를 활성화하여 가상 WAN 허브를 보안 가상 허브로 변환할 수 있습니다. Azure 방화벽 관리자에서 가상 WAN 허브에서 Azure 방화벽의 오케스트레이션을 수행할 수 있습니다.

[Azure 방화벽 관리자는](https://go.microsoft.com/fwlink/?linkid=2107683) 글로벌 전송 네트워크의 보안을 관리하고 확장하는 기능을 제공합니다. Azure 방화벽 관리자는 Azure 방화벽과 함께 타사를 통해 라우팅, 글로벌 정책 관리, 고급 인터넷 보안 서비스를 중앙에서 관리할 수 있는 기능을 제공합니다.

![Azure 방화벽을 사용하는 보안 가상 허브](./media/virtual-wan-global-transit-network-architecture/figure5.png)

**그림 5: Azure 방화벽을 사용하는 보안 가상 허브**

가상 WAN에 대한 Azure 방화벽은 다음과 같은 글로벌 보안 전송 연결 경로를 지원합니다. 괄호 안에 있는 문자는 그림 5로 매핑됩니다.

* VNet-VNet 보안 전송(e)
* VNet-인터넷 또는 타사 보안 서비스 (i)
* 지점 간 또는 타사 보안 서비스(j)

### <a name="vnet-to-vnet-secured-transit-e"></a>VNet-VNet 보안 전송(e)

VNet-VNet 보안 전송을 통해 VNet은 가상 WAN 허브의 Azure 방화벽을 통해 서로 연결할 수 있습니다.

### <a name="vnet-to-internet-or-third-party-security-service-i"></a>VNet-인터넷 또는 타사 보안 서비스 (i)

VNet-인터넷 또는 타사 보안 전송을 통해 VNet은 가상 WAN 허브의 Azure 방화벽을 통해 인터넷 또는 지원되는 타사 보안 서비스에 연결할 수 있습니다.

### <a name="branch-to-internet-or-third-party-security-service-j"></a>지점 간 또는 타사 보안 서비스(j)
지점-인터넷 또는 타사 Secure transit을 사용하면 브랜치가 가상 WAN 허브의 Azure 방화벽을 통해 인터넷 또는 지원되는 타사 보안 서비스에 연결할 수 있습니다.

## <a name="next-steps"></a>다음 단계

가상 WAN을 사용하여 연결을 만들고 VWAN 허브에 Azure 방화벽을 배포합니다.

* [가상 WAN을 사용하는 사이트 간 연결](virtual-wan-site-to-site-portal.md)
* [가상 WAN을 사용하는 익스프레스루트 연결](virtual-wan-expressroute-portal.md)
* [VWAN에 Azure FW를 배포하는 Azure 방화벽 관리자](https://go.microsoft.com/fwlink/?linkid=2107683)
