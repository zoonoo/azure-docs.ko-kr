---
title: Azure 가상 WAN 및 보안 허브를 사용하여 중국과 상호 연결
description: Virtual WAN의 자동화된 확장성 있는 지점 간 연결, 사용 가능한 지역 및 파트너에 대해 알아봅니다.
services: virtual-wan
author: skishen525
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 03/25/2020
ms.author: sukishen
ms.openlocfilehash: d086484ece6faf95dccffb1e29da8a0e906b1da4
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/09/2020
ms.locfileid: "80985629"
---
# <a name="interconnect-with-china-using-azure-virtual-wan-and-secure-hub"></a>Azure 가상 WAN 및 보안 허브를 사용하여 중국과 상호 연결

일반적인 자동차, 제조, 물류 산업 또는 대사관과 같은 다른 기관을 볼 때 중국과의 상호 연결을 개선하는 방법에 대한 질문이 종종 있습니다. 이러한 개선 사항은 주로 Office 365, Azure Global Services 또는 중국 내부의 지점을 고객 백본과 상호 연결하는 것과 같은 클라우드 서비스를 사용하는 것과 관련이 있습니다.

대부분의 경우 고객은 높은 대기 시간, 낮은 대역폭, 불안정한 연결 및 중국 외부(예: 유럽 또는 미국)에 연결하는 높은 비용으로 어려움을 겪고 있습니다.

이러한 투쟁의 이유는 인터넷의 중국 부분을 보호하고 중국으로의 트래픽을 필터링하는 "중국의 만리 방화벽"입니다. 홍콩, 마카오 와 같은 특별 관리 구역을 제외한 중국 본토에서 중국 외부로 운행되는 거의 모든 트래픽이 만리 방화벽을 통과합니다. 홍콩과 마카오를 통해 실행되는 트래픽은 전체 힘에 만리 방화벽을 명중하지 않습니다, 그것은 만리 방화벽의 하위 집합에 의해 처리됩니다.

![공급자 상호 연결](./media/interconnect-china/provider.png)

고객은 Virtual WAN을 사용하여 중국 사이버 보안법을 위반하지 않고도 Microsoft 클라우드 서비스에 대한 보다 성능이 우수하고 안정적인 연결을 설정하고 엔터프라이즈 네트워크에 연결할 수 있습니다.

## <a name="requirements-and-workflow"></a><a name="requirements"></a>요구 사항 및 워크플로

중국 사이버 보안법을 준수하려면 특정 조건을 충족해야 합니다.

첫째, 중국의 ICP(인터넷 콘텐츠 제공업체) 라이선스를 소유하고 있는 네트워크 및 ISP와 협력해야 합니다. 대부분의 경우 다음 공급자 중 하나가 됩니다.

* 차이나 텔레콤 글로벌 주식회사
* 차이나 모바일 (주)
* 중국 유니콤 주식회사
* PCCW 글로벌 주식회사
* 홍콩 텔레콤 주식회사

공급자와 필요에 따라 이제 다음 네트워크 연결 서비스 중 하나를 구입하여 중국 내 지점을 상호 연결해야 합니다.

* MPLS/IPVPN 네트워크 
* 소프트웨어 정의 WAN(SDWAN)
* 전용 인터넷 액세스

다음으로, 베이징이나 상하이가 아닌 홍콩의 Microsoft 글로벌 네트워크와 에지 네트워크에 브레이크 아웃을 제공하기 위해 해당 공급자와 동의해야합니다. 이 경우 홍콩은 중국과의 물리적 인 연결 및 위치 때문에 매우 중요합니다.

대부분의 고객은 싱가포르를 상호 연결에 사용하는 것이 가장 좋은 경우라고 생각하지만 지도를 볼 때 중국에 더 가깝게 보이기 때문에 사실이 아닙니다. 네트워크 파이버 맵을 따라가면 거의 모든 네트워크 연결이 베이징, 상하이 및 홍콩을 통과합니다. 따라서 홍콩은 중국과 상호 연결하기에 더 나은 위치 선택입니다.

공급자에 따라 다른 서비스 제공을 받을 수 있습니다. 아래 표는 이 문서가 작성된 시점의 정보를 기반으로 공급자와 제공하는 서비스의 예를 보여줍니다.

| 서비스 | 공급자 예제 |
| --- | --- |
| MPLS/IPVPN 네트워크 |PCCW, 차이나 텔레콤 글로벌 |
|SDWAN| PCCW, 차이나 텔레콤 글로벌|
| 전용 인터넷 액세스 | PCCW, 홍콩 텔레콤, 차이나 모빌|

공급자를 사용하면 Microsoft 전역 백본에 도달하는 데 사용할 다음 두 가지 솔루션 중 어느 솔루션에 동의할 수 있습니다.

* 홍콩에서 종료된 Microsoft Azure 익스프레스루트를 얻기. MPLS/IPVPN을 사용하는 경우도 마찬가지입니다. 현재 홍콩에 익스프레스루트를 제공하는 유일한 ICP 라이선스 제공업체는 차이나 텔레콤 글로벌뿐입니다. 그러나 메가포트 나 인터클라우드와 같은 클라우드 교환 공급자를 활용하는 경우 다른 공급자와 대화할 수도 있습니다. 자세한 내용은 [ExpressRoute 연결 공급자를](../expressroute/expressroute-locations-providers.md#partners)참조하십시오.

* 다음 인터넷 교환 지점 중 하나에서 직접 전용 인터넷 액세스를 사용하거나 개인 네트워크 상호 연결을 사용합니다.

다음 목록은 홍콩에서 가능한 인터넷 교환을 보여줍니다.

* AMS-IX 홍콩
* BBIX 홍콩
* 에퀴닉스 홍콩
* HKIX

이 연결을 사용하는 경우 Microsoft 서비스에 대한 다음 BGP 홉은 Microsoft 자율 시스템 번호(AS#) 8075여야 합니다. 단일 위치 또는 SDWAN 솔루션을 사용하는 경우 연결을 선택할 수 있습니다.

어느 쪽이든, 우리는 여전히 중국 본토에 두 번째 및 정기적 인 인터넷 브레이크 아웃을 하는 것이 좋습니다. 이는 엔터프라이즈 트래픽 간의 트래픽을 Microsoft 365 및 Azure와 같은 클라우드 서비스로 분할하고 법에 따라 인터넷 트래픽을 규제하는 것입니다.

중국 내의 규정 준수 네트워크 아키텍처는 다음과 같은 예와 같습니다.

![여러 분기](./media/interconnect-china/multi-branch.png)

이 예제에서는 홍콩의 Microsoft 글로벌 네트워크와 상호 연결하면 이제 Azure [Virtual WAN 글로벌 전송 아키텍처](virtual-wan-global-transit-network-architecture.md) 및 Azure secure Virtual WAN 허브와 같은 추가 서비스를 활용하여 서비스를 사용하고 중국 외부의 지점 및 데이터 센터에 상호 연결할 수 있습니다.

## <a name="hub-to-hub-communication"></a><a name="hub-to-hub"></a>허브 간 통신

이 섹션에서는 가상 WAN 허브 간 통신을 사용하여 상호 연결합니다. 이 시나리오에서는 홍콩의 가상 WAN 허브, 선호하는 다른 지역, Azure 리소스가 이미 있는 지역 또는 연결하려는 지역에 연결하는 새 가상 WAN 허브 리소스를 만듭니다.

샘플 아키텍처는 다음과 같은 예제와 같을 수 있습니다.

![샘플 WAN](./media/interconnect-china/sample.png)

이 예에서 중국 지사는 VPN 또는 MPLS 연결을 사용하여 Azure Cloud China와 서로 연결합니다. 글로벌 서비스에 연결해야 하는 지점은 홍콩에 직접 연결된 MPLS 또는 인터넷 기반 서비스를 사용합니다. 홍콩과 다른 지역에서 ExpressRoute를 사용하려면 두 ExpressRoute 회로를 상호 연결하도록 [ExpressRoute 전역 도달](../expressroute/expressroute-global-reach.md) 범위를 구성해야 합니다.

익스프레스루트 글로벌 리치는 일부 지역에서는 사용할 수 없습니다. 예를 들어 브라질 또는 인도와 상호 연결해야 하는 경우 [클라우드 Exchange 공급자를](../expressroute/expressroute-locations.md#connectivity-through-exchange-providers) 활용하여 라우팅 서비스를 제공해야 합니다.

아래 그림은 이 시나리오의 두 예제를 모두 보여 주며 있습니다.

![Global Reach](./media/interconnect-china/global.png)

## <a name="secure-internet-breakout-for-office-365"></a><a name="secure"></a>Office 365를 위한 안전한 인터넷 브레이크아웃

또 다른 고려 사항은 네트워크 보안뿐만 아니라 중국과 가상 WAN 이 설정 된 백본 구성 요소 및 고객 백본 사이의 진입점에 대한 로깅입니다. 대부분의 경우 Microsoft Edge 네트워크에 직접 도달하기 위해 홍콩의 인터넷에 침입해야 하며 Microsoft 365 서비스에 사용되는 Azure 정문 서버가 필요합니다.

가상 WAN을 두 시나리오에서 [모두 Azure 가상 WAN 보안 허브를](../firewall-manager/secured-virtual-hub.md)활용합니다. Azure 방화벽 관리자를 사용하여 일반 가상 WAN 허브를 보안 허브로 변경한 다음 해당 허브 내에서 Azure 방화벽을 배포하고 관리할 수 있습니다.

다음 그림은 이 시나리오의 예를 보여 주며 다음과 같은 예입니다.

![웹 및 Microsoft 서비스 트래픽에 대한 인터넷 브레이크아웃](./media/interconnect-china/internet.png)

## <a name="architecture-and-traffic-flows"></a><a name="traffic"></a>아키텍처 및 트래픽 흐름

홍콩과의 연결에 대한 선택에 따라 전체 아키텍처가 약간 변경될 수 있습니다. 이 섹션에서는 VPN 또는 SDWAN 및/또는 ExpressRoute와 서로 다른 조합으로 사용 가능한 세 가지 아키텍처를 보여 주며 있습니다.

이러한 모든 옵션을 사용하면 홍콩에서 직접 M365 연결을 위해 Azure Virtual WAN 보안 허브를 사용할 수 있습니다. 또한 이러한 아키텍처는 Office [365 Multi-Geo에](https://docs.microsoft.com/office365/enterprise/office-365-multi-geo) 대한 규정 준수 요구 사항을 지원하고 해당 트래픽을 다음 Office 365 정문 위치 근처에 유지합니다. 결과적으로, 그것은 또한 마이크로소프트의 사용에 대 한 개선 365 중국에서.

Azure Virtual WAN을 인터넷 연결과 함께 사용하는 경우 모든 연결은 [Microsoft Azure 피어링 서비스(MAPS)와](https://docs.microsoft.com/azure/peering-service/about)같은 추가 서비스의 이점을 누릴 수 있습니다. MAPS는 제 3 자 인터넷 서비스 제공 업체에서 Microsoft 글로벌 네트워크에 오는 트래픽을 최적화하기 위해 만들어졌습니다.

### <a name="option-1-sdwan-or-vpn"></a><a name="option-1"></a>옵션 1: SDWAN 또는 VPN

이 섹션에서는 홍콩 및 기타 지사에 SDWAN 또는 VPN을 사용하는 디자인에 대해 설명합니다. 이 옵션은 가상 WAN 백본의 두 사이트에서 순수 인터넷 연결을 사용할 때 의 사용 및 트래픽 흐름을 보여줍니다. 이 경우 전용 인터넷 액세스 또는 ICP 공급자 SDWAN 솔루션을 사용하여 홍콩으로 연결됩니다. 다른 분기도 순수 인터넷 또는 SDWAN 솔루션을 사용하고 있습니다.

![중국에서 홍콩으로 교통](./media/interconnect-china/china-traffic.png)

이 아키텍처에서 모든 사이트는 VPN 및 Azure 가상 WAN을 사용하여 Microsoft 글로벌 네트워크에 연결됩니다. 사이트와 홍콩 간의 트래픽은 Microsoft 네트워크에서 전송되며 마지막 마일에서만 일반 인터넷 연결을 사용합니다.

### <a name="option-2-expressroute-and-sdwan-or-vpn"></a><a name="option-2"></a>옵션 2: 익스프레스루트 및 SDWAN 또는 VPN

이 섹션에서는 홍콩의 ExpressRoute 및 VPN/SDWAN 지점이 있는 다른 지점을 사용하는 디자인에 대해 설명합니다. 이 옵션은 홍콩 및 SDWAN 또는 VPN을 통해 연결된 다른 지점에서 종료된 ExpressRoute의 사용을 보여줍니다. 홍콩의 익스프레스루트는 현재 [익스프레스 루트 파트너](../expressroute/expressroute-locations-providers.md#global-commercial-azure)목록에서 찾을 수 있는 짧은 제공업체 목록으로 제한되어 있습니다.

![중국에서 홍콩행 교통 익스프레스루트](./media/interconnect-china/expressroute.png)

예를 들어 한국이나 일본과 같은 중국에서 익스프레스루트를 종료할 수도 있습니다. 그러나 규정 준수, 규정 및 대기 시간을 감안할 때 홍콩은 현재 최선의 선택입니다.

### <a name="option-3-expressroute-only"></a><a name="option-3"></a>옵션 3: 익스프레스루트만

이 섹션에서는 ExpressRoute가 홍콩 및 기타 지점에서 사용되는 디자인에 대해 설명합니다. 이 옵션은 양쪽 끝에 ExpressRoute를 사용하여 상호 연결을 표시합니다. 여기서 다른 트래픽 흐름과 다른 트래픽 흐름이 있습니다. Microsoft 365 트래픽은 Azure 가상 WAN 보안 허브로 이동하여 거기에서 Microsoft Edge 네트워크 및 인터넷으로 이동합니다.

상호 연결된 지점이나 중국의 위치로 이동하는 트래픽은 해당 아키텍처 내에서 다른 접근 방식을 따를 것입니다. 현재 가상 WAN은 익스프레스루트에서 익스프레스루트로의 전송을 지원하지 않습니다. 트래픽은 가상 WAN 허브를 통과하지 않고 ExpressRoute 글로벌 리치 또는 제 3 자 상호 연결을 활용합니다. 그것은 직접 한 마이크로 소프트 엔터프라이즈 에지에서 흐를 것입니다 (MSEE) 다른.

![ExpressRoute Global Reach](./media/interconnect-china/expressroute-virtual.png)

현재 ExpressRoute 전역 도달은 모든 국가에서 사용할 수 없지만 Azure Virtual WAN을 사용하여 솔루션을 구성할 수 있습니다.

예를 들어 Microsoft 피어링을 사용하여 ExpressRoute를 구성하고 해당 피어링을 통해 Azure Virtual WAN에 대한 VPN 터널을 연결할 수 있습니다. 이제 글로벌 리치 없이 VPN과 ExpressRoute 간의 전송과 메가포트 클라우드와 같은 제3자 제공업체 및 서비스 간의 전송을 활성화했습니다.

## <a name="next-steps"></a>다음 단계

자세한 내용은 다음 아티클을 참조하세요.

* [Azure 가상 WAN을 갖춘 글로벌 전송 네트워크 아키텍처](virtual-wan-global-transit-network-architecture.md)

* [가상 WAN 허브 만들기](virtual-wan-site-to-site-portal.md)

* [가상 WAN 보안 허브 구성](../firewall-manager/secure-cloud-network.md)

* [Azure 피어링 서비스 미리 보기 개요](https://docs.microsoft.com/azure/peering-service/about)
