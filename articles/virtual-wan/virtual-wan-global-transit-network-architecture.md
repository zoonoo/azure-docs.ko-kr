---
title: '아키텍처: 글로벌 전송 네트워크 아키텍처'
titleSuffix: Azure Virtual WAN
description: VNet, 분기 사이트, SaaS와 PaaS 애플리케이션 및 사용자에 있는 전 세계적으로 분산된 클라우드 워크로드 집합 간의 Any-to-Any 연결을 사용하도록 설정하여 Azure Virtual WAN이 글로벌 전송 네트워크 아키텍처를 실현하는 방법을 알아봅니다.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 05/07/2020
ms.author: cherylmc
ms.openlocfilehash: 0f1cac384193183b4c3495476d8f022ea7397fa4
ms.sourcegitcommit: 62e800ec1306c45e2d8310c40da5873f7945c657
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2021
ms.locfileid: "108163988"
---
# <a name="global-transit-network-architecture-and-virtual-wan"></a>글로벌 전송 네트워크 아키텍처 및 Virtual WAN

최신 기업은 클라우드 및 온-프레미스에서 하이퍼-분산 애플리케이션, 데이터 및 사용자 간에 다양한 연결이 필요합니다. 이러한 기업은 글로벌 전송 네트워크 아키텍처를 채택하여 클라우드 중심의 최신 글로벌 엔터프라이즈 IT 공간을 통합, 연결 및 제어하고 있습니다.

글로벌 전송 네트워크 아키텍처는 기본 허브 및 스포크 연결 모델을 기반으로 합니다. 이 모델에서 클라우드 호스팅 네트워크 '허브'가 여러 유형의 '스포크'에 분산될 수 있는 엔드포인트 간의 전이적 연결을 지원합니다.

이 모델에서 스포크는 다음과 같을 수 있습니다.
* VNet(가상 네트워크)
* 실제 분기 사이트
* 원격 사용자
* 인터넷

![허브 및 스포크](./media/virtual-wan-global-transit-network-architecture/figure1.png)

**그림 1: 글로벌 전송 허브 및 스포크 네트워크**

그림 1에서는 지리적으로 분산된 사용자, 물리적 사이트 및 VNet이 클라우드에서 호스트되는 네트워킹 허브를 통해 상호 연결된 글로벌 전송 네트워크의 논리적 보기를 보여 줍니다. 이 아키텍처에서는 네트워킹 엔드포인트 간에 논리적인 1홉 전송 연결을 사용하도록 설정합니다.

## <a name="global-transit-network-with-virtual-wan"></a><a name="globalnetworktransit"></a>Virtual WAN의 글로벌 전송 네트워크

Azure Virtual WAN은 Microsoft에서 관리하는 클라우드 네트워킹 서비스입니다. 이 서비스가 구성된 모든 네트워킹 구성 요소는 Microsoft에서 호스트되고 관리됩니다. Virtual WAN에 대한 자세한 내용은 [Virtual WAN 개요](virtual-wan-about.md) 문서를 참조하세요.

Azure Virtual WAN은 VNet, 분기 사이트, SaaS와 PaaS 애플리케이션 및 사용자에 있는 전 세계적으로 분산된 클라우드 워크로드 집합 간에 Any-to-Any 연결을 가능하게 하여 글로벌 전송 네트워크 아키텍처를 실현합니다.

![Azure 가상 WAN](./media/virtual-wan-global-transit-network-architecture/figure2.png)

**그림 2: 글로벌 전송 네트워크 아키텍처 및 Virtual WAN**

Azure Virtual WAN 아키텍처에서 Virtual WAN 허브는 Azure 지역에 프로비저닝되어 분기, VNet 및 원격 사용자를 연결하도록 선택할 수 있습니다. 물리적 분기 사이트는 프리미엄 또는 표준 ExpressRoute 또는 사이트 간 VPN을 통해 허브에 연결되고, VNet은 VNet 연결을 통해 허브에 연결되고, 원격 사용자는 사용자 VPN(지점 및 사이트 간 VPN)을 사용하여 허브에 직접 연결할 수 있습니다. 또한 가상 WAN은 한 지역의 VNet을 다른 지역의 가상 WAN 허브에 연결할 수 있는 지역 간 VNet 연결을 지원합니다.

스포크(분기, VNet, 사용자) 수가 가장 많은 지역에서 단일 가상 WAN 허브를 만든 다음 다른 지역의 스포크를 허브에 연결하여 가상 WAN을 설정할 수 있습니다. 이 설정은 적은 수의 원격 스포크가 있는 한 지역에 엔터프라이즈 공간 대부분이 있는 경우 좋은 옵션입니다.  
  
## <a name="hub-to-hub-connectivity"></a><a name="hubtohub"></a>허브 간 연결

엔터프라이즈 클라우드 공간은 여러 클라우드 지역에 걸쳐 있을 수 있으며, 물리적 사이트 및 사용자에게 가장 가까운 지역에서 클라우드에 액세스하는 것이 대기 시간 면에서 최적입니다. 글로벌 전송 네트워크 아키텍처의 핵심 원리 중 하나는 모든 클라우드 및 온-프레미스 네트워크 엔드포인트 간에 지역 간 연결을 사용하도록 설정하는 것입니다. 즉, 한 지역의 클라우드에 연결된 분기의 트래픽은 [Azure 글로벌 네트워크](https://azure.microsoft.com/global-infrastructure/global-network/)에서 사용하도록 설정된 허브 간 연결을 사용하여 다른 지역에 있는 다른 분기 또는 VNet에 도달할 수 있습니다.

![지역 간](./media/virtual-wan-global-transit-network-architecture/figure3.png)

**그림 3: 가상 WAN 지역 간 연결**

단일 가상 WAN에서 여러 허브를 사용하도록 설정하는 경우 허브는 허브 간 링크를 통해 자동으로 상호 연결되므로 여러 지역에 걸쳐 분산된 분기와 VNet 간에 글로벌 연결을 사용할 수 있습니다. 

또한 동일한 가상 WAN의 일부인 허브는 서로 다른 지역 액세스 및 보안 정책에 연결될 수 있습니다. 자세한 내용은 이 문서의 뒷부분에서 [보안 및 정책 제어](#security)를 참조하세요.

## <a name="any-to-any-connectivity"></a><a name="anytoany"></a>Any-to-Any 연결

글로벌 전송 네트워크 아키텍처에서는 가상 WAN 허브를 통해 Any-to-Any 연결을 사용하도록 설정합니다. 이 아키텍처에서는 빌드 및 유지 관리가 더 복잡한 스포크 간 전체 메시 또는 부분 메시 연결의 필요성을 제거하거나 감소시킵니다. 또한 허브 및 스포크 네트워크의 라우팅 제어를 구성하고 유지 관리하는 것이 더 쉽습니다.

글로벌 아키텍처의 컨텍스트에서 Any-to-Any 연결은 전 세계에 분산된 사용자, 분기, 데이터 센터, VNet 및 애플리케이션이 하나 이상의 "전송" 허브를 통해 서로 연결될 수 있도록 합니다. Azure Virtual WAN은 글로벌 전송 시스템 역할을 합니다.

![Any-to-Any](./media/virtual-wan-global-transit-network-architecture/figure4.png)

**그림 4: 가상 WAN 트래픽 경로**

Azure Virtual WAN은 다음과 같은 글로벌 전송 연결 경로를 지원합니다. 괄호 안의 문자는 그림 4에 매핑됩니다.

* 분기-VNet(a)
* 분기 간(b)
  * ExpressRoute Global Reach 및 Virtual WAN
* 원격 사용자-VNet(c)
* 원격 사용자-분기(d)
* VNET 간(e)
* 분기-허브, 허브-분기(f)
* 분기-허브, 허브-VNet(g)
* VNet-허브, 허브-VNet(h)

### <a name="branch-to-vnet-a-and-branch-to-vnet-cross-region-g"></a>분기-VNet(a) 및 분기-VNet 지역 간(g)

Azure Virtual WAN은 분기-VNet을 주 경로로 지원합니다. 이 경로를 사용하여 Azure VNet에 배포된 Azure IAAS 엔터프라이즈 워크로드에 분기를 연결할 수 있습니다. 분기는 ExpressRoute 또는 사이트 간 VPN을 통해 가상 WAN에 연결될 수 있습니다. 트래픽은 VNet 연결을 통해 가상 WAN 허브에 연결되는 VNet에 전송됩니다. Virtual WAN은 분기 사이트로의 게이트웨이 전송을 자동적으로 가능하게 하므로 Virtual WAN에 대한 명시적 [게이트웨이 전송](../virtual-network/virtual-network-peering-overview.md#gateways-and-on-premises-connectivity)은 필요하지 않습니다. Virtual WAN에 SD-WAN CPE를 연결하는 방법에 대해서는 [Virtual WAN 파트너](virtual-wan-configure-automation-providers.md) 문서를 참조하세요.

### <a name="expressroute-global-reach-and-virtual-wan"></a>ExpressRoute Global Reach 및 Virtual WAN

ExpressRoute는 온-프레미스 네트워크를 Microsoft 클라우드에 연결하는 개인적이고 복원력 있는 방법입니다. Virtual WAN은 Express 경로 회로 연결을 지원합니다. Express 경로를 사용하여 Virtual WAN에 분기 사이트를 연결하려면 1) 프리미엄 또는 표준 회로여야 하며 2) 회로가 Global Reach를 사용하도록 설정한 위치에 있어야 합니다.

ExpressRoute Global Reach는 ExpressRoute의 추가 기능입니다. Global Reach를 사용하면 온-프레미스 네트워크 간의 프라이빗 네트워크를 설정하기 위해 ExpressRoute 회로를 함께 연결할 수 있습니다. ExpressRoute를 사용하여 Azure Virtual WAN에 연결된 분기에서는 ExpressRoute Global Reach가 서로 통신해야 합니다.

이 모델에서 ExpressRoute를 사용하여 가상 WAN 허브에 연결된 각 분기에서 VNet 간 경로를 사용하여 VNet에 연결할 수 있습니다. ExpressRoute Global Reach는 Azure WAN을 통해 더 최적의 경로를 사용할 수 있으므로 분기 간 트래픽은 허브를 전송하지 않습니다.

### <a name="branch-to-branch-b-and-branch-to-branch-cross-region-f"></a>분기 간(b) 및 분기 간 지역 간(f)

분기는 ExpressRoute 회로 및/또는 사이트 간 VPN 연결을 사용하여 Azure Virtual WAN 허브에 연결할 수 있습니다. 분기와 가장 가까운 지역에 있는 가상 WAN 허브에 분기를 연결할 수 있습니다.

이 옵션을 통해 기업은 Azure 백본을 활용하여 분기를 연결할 수 있습니다. 그러나 이 기능을 사용할 수 있더라도 Azure Virtual WAN을 통해 분기를 연결하는 이점을 프라이빗 WAN을 사용하는 것과 비교 평가해야 합니다.  

> [!NOTE]
> Virtual WAN에서 분기 간 연결을 사용하지 않도록 설정 - 분기 간 연결을 사용하지 않도록 Virtual WAN을 구성할 수 있습니다. 이 구성은 VPN(S2S 및 P2S)과 Express 경로로 연결된 사이트 간의 경로 전파를 차단합니다. 이 구성은 분기-VNet 및 VNET 간 경로 전파 및 연결에 영향을 주지 않습니다. Azure Portal을 사용하여 이 설정을 구성하려면 Virtual WAN 구성 메뉴에서 설정: 분기 간 - 사용 안 함을 선택합니다. 

### <a name="remote-user-to-vnet-c"></a>원격 사용자-VNet(c)

원격 사용자 클라이언트에서 Virtual WAN으로 지점 및 사이트 간 연결을 사용하여 Azure에 대한 직접 보안 원격 액세스를 사용하도록 설정할 수 있습니다. 엔터프라이즈 원격 사용자는 더 이상 회사 VPN을 사용하여 클라우드에 헤어핀을 사용할 필요가 없습니다.

### <a name="remote-user-to-branch-d"></a>원격 사용자-분기(d)

원격 사용자-분기 경로를 사용하면 Azure에 대한 지점 및 사이트 간 연결을 사용하는 원격 사용자가 클라우드를 통해 전송하여 온-프레미스 워크로드 및 애플리케이션에 액세스할 수 있습니다. 이 경로를 통해 원격 사용자는 Azure 및 온-프레미스에 배포된 워크로드에 유연하게 액세스할 수 있습니다. 기업은 Azure Virtual WAN에서 중앙 클라우드 기반 보안 원격 액세스 서비스를 사용하도록 설정할 수 있습니다.

### <a name="vnet-to-vnet-transit-e-and-vnet-to-vnet-cross-region-h"></a>VNet 간 전송(e) 및 VNet 간 지역 간(h)

VNet 간 전송 기능을 사용하면 여러 VNet에서 구현된 다중 계층 애플리케이션을 상호 연결하기 위해 VNet에서 서로 연결할 수 있습니다. 필요에 따라 VNet 피어링을 통해 VNet을 서로 연결할 수 있으며, 이는 VWAN 허브를 통해 전송하지 않아도 되는 일부 시나리오에 적합합니다.


## <a name="force-tunneling-and-default-route-in-azure-virtual-wan"></a><a name="DefaultRoute"></a>Azure Virtual WAN의 강제 터널링 및 기본 경로

Virtual WAN에서 VPN, ExpressRoute 또는 Virtual Network 연결에 기본 경로 사용을 구성하여 강제 터널링을 사용하도록 설정할 수 있습니다.

연결에서 기본 플래그 사용이 '사용'으로 설정된 경우 가상 허브는 학습된 기본 경로를 가상 네트워크/사이트 간 VPN/ExpressRoute 연결에 전파합니다. 

사용자가 가상 네트워크 연결, VPN 연결 또는 ExpressRoute 연결을 편집할 때 이 플래그를 볼 수 있습니다. 사이트 또는 ExpressRoute 회로가 허브에 연결된 경우 기본적으로 이 플래그는 사용하지 않도록 설정됩니다. VNet을 가상 허브에 연결하기 위해 가상 네트워크 연결을 추가하면 기본적으로 사용하도록 설정됩니다. 기본 경로는 Virtual WAN 허브에서 시작되지 않습니다. 허브에 방화벽을 배포한 결과로 Virtual WAN 허브에서 학습했거나 다른 연결된 사이트에서 강제 터널링을 사용할 경우 기본 경로가 전파됩니다.

## <a name="security-and-policy-control"></a><a name="security"></a>보안 및 정책 컨트롤

Azure Virtual WAN 허브는 하이브리드 네트워크에서 모든 네트워킹 엔드포인트를 상호 연결하고 잠재적으로 모든 전송 네트워크 트래픽을 표시합니다. 클라우드 기반 보안, 액세스 및 정책 제어를 사용하기 위해 VWAN 허브 내에 Azure Firewall을 배포하여 가상 WAN 허브를 보안 가상 허브로 변환할 수 있습니다. Azure Firewall Manager에서 가상 WAN 허브의 Azure Firewall 오케스트레이션을 수행할 수 있습니다.

[Azure Firewall Manager](../firewall-manager/index.yml)는 글로벌 전송 네트워크에 대한 보안을 관리하고 스케일링하는 기능을 제공합니다. Azure Firewall Manager는 Azure Firewall과 함께 타사를 통해 라우팅, 글로벌 정책 관리, 고급 인터넷 보안 서비스를 중앙에서 관리하는 기능을 제공합니다.

![Azure Firewall을 사용한 보안 가상 허브](./media/virtual-wan-global-transit-network-architecture/figure5.png)

**그림 5: Azure Firewall을 사용한 보안 가상 허브**

가상 WAN에 대한 Azure Firewall은 다음과 같은 글로벌 보안 전송 연결 경로를 지원합니다. 괄호 안의 문자는 그림 5에 매핑됩니다.

* VNet 간 보안 전송(e)
* VNet-인터넷 또는 타사 보안 서비스(i)
* 분기인터넷 또는 타사 보안 서비스(j)

### <a name="vnet-to-vnet-secured-transit-e"></a>VNet 간 보안 전송(e)

VNet 간 보안 전송은 가상 WAN 허브의 Azure Firewall을 통해 VNet이 서로 연결할 수 있도록 합니다.

### <a name="vnet-to-internet-or-third-party-security-service-i"></a>VNet-인터넷 또는 타사 보안 서비스(i)

VNet 간 인터넷은 가상 WAN 허브의 Azure Firewall을 통해 VNet이 인터넷에 연결할 수 있도록 합니다. 지원되는 타사 보안 서비스를 통해 인터넷으로 전송되는 트래픽은 Azure Firewall을 통해 전달되지 않습니다. Azure Firewall Manager를 사용하여 지원되는 타사 보안 서비스를 통해 VNet-인터넷 경로를 구성할 수 있습니다.  

### <a name="branch-to-internet-or-third-party-security-service-j"></a>분기인터넷 또는 타사 보안 서비스(j)
분기-인터넷 연결은 가상 WAN 허브의 Azure Firewall을 통해 분기가 인터넷에 연결할 수 있도록 합니다. 지원되는 타사 보안 서비스를 통해 인터넷으로 전송되는 트래픽은 Azure Firewall을 통해 전달되지 않습니다. Azure Firewall Manager를 사용하여 지원되는 타사 보안 서비스를 통해 분기-인터넷 경로를 구성할 수 있습니다. 

### <a name="branch-to-branch-secured-transit-cross-region-f"></a>분기 간 보안 전송 지역 간(f)

분기는 ExpressRoute 회로 및/또는 사이트 간 VPN 연결을 사용하여 Azure Firewall을 사용한 보안 가상 허브에 연결할 수 있습니다. 분기와 가장 가까운 지역에 있는 가상 WAN 허브에 분기를 연결할 수 있습니다.

이 옵션을 통해 기업은 Azure 백본을 활용하여 분기를 연결할 수 있습니다. 그러나 이 기능을 사용할 수 있더라도 Azure Virtual WAN을 통해 분기를 연결하는 이점을 프라이빗 WAN을 사용하는 것과 비교 평가해야 합니다.  

> [!NOTE]
> 방화벽을 통한 트래픽의 허브 간 처리는 현재 지원되지 않습니다. 허브 간의 트래픽은 보안 가상 허브 내의 적절한 분기로 라우팅됩니다. 그러나 트래픽은 각 허브에서 Azure Firewall을 우회합니다.

### <a name="branch-to-vnet-secured-transit-g"></a>분기-VNet 보안 전송(g)

분기-VNet 보안 전송은 분기가 가상 WAN 허브와 동일한 지역에 있는 가상 네트워크 및 다른 지역의 다른 가상 WAN 허브에 연결된 다른 가상 네트워크와 통신할 수 있도록 합니다.

> [!NOTE]
> 방화벽이 있는 허브 간 연결은 현재 지원되지 않습니다. 허브 간의 트래픽은 각 허브에서 Azure Firewall을 우회하여 직접 이동합니다.  동일한 지역의 가상 네트워크로 향하는 연결을 통한 트래픽은 보안 허브의 Azure Firewall에 의해 처리됩니다.


### <a name="how-do-i-enable-default-route-00000-in-a-secured-virtual-hub"></a>보안 가상 허브에서 기본 경로(0.0.0.0/0)를 어떻게 사용 설정하나요?

Virtual WAN 허브(보안 가상 허브)에 배포된 Azure Firewall은 인터넷 또는 모든 분기에 대한 신뢰할 수 있는 보안 공급자(VPN 또는 Express 경로를 통해 연결), 스포크 VNet 및 사용자(P2S VPN을 통해 연결)에 대한 기본 라우터로 구성될 수 있습니다. 이 구성은 Azure Firewall Manager를 사용하여 수행해야 합니다.  허브로의 트래픽 라우팅을 참조하여 분기(사용자 포함) 및 VNet-인터넷의 모든 트래픽을 구성합니다. 

이는 두 단계로 구성되어 있습니다.

1. 보안 가상 허브 경로 설정 메뉴를 사용하여 인터넷 트래픽 라우팅을 구성합니다. 방화벽을 통해 인터넷으로 트래픽을 보낼 수 있는 VNet 및 분기를 구성합니다.

2. 허브 또는 신뢰할 수 있는 보안 공급자에서 Azure FW를 통해 인터넷(0.0.0.0/0)으로 트래픽을 라우팅할 수 있는 연결(VNet 및 분기)을 구성합니다. 이 단계를 수행하면 기본 경로가 선택한 분기 및 Connections를 통해 Virtual WAN 허브에 연결된 VNet에 전파됩니다. 

### <a name="force-tunneling-traffic-to-on-premises-firewall-in-a-secured-virtual-hub"></a>보안 가상 허브의 온-프레미스 방화벽에 강제 트래픽 터널링

분기(VPN 또는 ER 사이트) 중 하나에서 가상 허브에 의해 이미 BGP를 통해 학습된 기본 경로가 이미 있는 경우 이 기본 경로는 Azure Firewall Manager 설정에서 얻은 기본 경로에 의해 재정의됩니다. 이 경우 VNet에서 허브를 입력하는 모든 트래픽과 인터넷으로 향하는 분기가 Azure Firewall 또는 신뢰할 수 있는 보안 공급자로 라우팅됩니다.

> [!NOTE]
> 현재 VNet, 분기 또는 사용자에서 시작되는 인터넷 바운드 트래픽에 대해 온-프레미스 방화벽 또는 Azure Firewall(및 신뢰할 수 있는 보안 공급자)을 선택할 수 있는 옵션은 없습니다. Azure Firewall Manager 설정에서 배운 기본 경로는 항상 분기 중 하나에서 얻은 기본 경로보다 우선합니다.


## <a name="next-steps"></a>다음 단계

Virtual WAN을 사용하여 연결을 만들고 하나 이상의 VWAN 허브에 Azure Firewall을 배포합니다.

* [Virtual WAN을 사용하여 사이트 간 연결](virtual-wan-site-to-site-portal.md)
* [Azure Virtual WAN을 사용하여 ExpressRoute 연결](virtual-wan-expressroute-portal.md)
* [VWAN에 Azure FW를 배포하는 Azure Firewall Manager](../firewall-manager/index.yml)
