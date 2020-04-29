---
title: Azure 가상 WAN 및 보안 허브를 사용 하 여 중국과 상호 연결
description: Virtual WAN의 자동화된 확장성 있는 지점 간 연결, 사용 가능한 지역 및 파트너에 대해 알아봅니다.
services: virtual-wan
author: skishen525
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 03/25/2020
ms.author: sukishen
ms.openlocfilehash: d086484ece6faf95dccffb1e29da8a0e906b1da4
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "80985629"
---
# <a name="interconnect-with-china-using-azure-virtual-wan-and-secure-hub"></a>Azure 가상 WAN 및 보안 허브를 사용 하 여 중국과 상호 연결

일반적인 자동차, 제조, 물류 산업 또는 embassies와 같은 기타 institutes of을 살펴볼 때 중국과의 상호 연결이 향상 되는 방법에 대 한 질문은 종종 있습니다. 이러한 향상 된 기능은 Office 365, Azure 글로벌 서비스 또는 고객 백본으로 중국 내부의 상호 연결 분기와 같은 Cloud Services를 사용 하는 경우와 거의 관련이 있습니다.

대부분의 경우 고객은 많은 대기 시간, 낮은 대역폭, 불안정 한 연결 및 중국 외부 (예: 유럽 또는 미국)에 연결 하는 높은 비용으로 노력 하 고 있습니다.

이러한 겪던에 대 한 이유는 인터넷의 중국어 부분을 보호 하 고 중국으로 트래픽을 필터링 하는 "중국의 뛰어난 방화벽"입니다. 홍콩, 마카오 같은 특수 한 관리 영역을 제외 하 고 중국 본토에서 중국의 외부에서 실행 되는 거의 모든 트래픽은 뛰어난 방화벽을 전달 합니다. 홍콩 및 마카오를 통해 실행 되는 트래픽은 전체 force에서 뛰어난 방화벽에 도달 하지 않으며, 뛰어난 방화벽의 하위 집합에 의해 처리 됩니다.

![공급자 상호 연결](./media/interconnect-china/provider.png)

고객은 가상 WAN을 사용 하 여 Microsoft 클라우드 서비스에 대 한 보다 안정적이 고 안정적인 연결을 설정 하 고 중국어 사이버 보안 법률을 손상 시 키 지 않고 기업 네트워크에 연결할 수 있습니다.

## <a name="requirements-and-workflow"></a><a name="requirements"></a>요구 사항 및 워크플로

중국어 사이버 보안 법률을 준수 하려면 특정 조건 집합을 충족 해야 합니다.

먼저 중국의 ICP (인터넷 콘텐츠 공급자) 라이선스를 소유한 네트워크 및 ISP와 함께 작업 해야 합니다. 대부분의 경우 다음 공급자 중 하나를 사용 합니다.

* 중국 통신 글로벌 l t d.
* 중국 Mobile l t.
* 중국 Unicom l t.
* PCCW Global l t;
* 홍콩 통신 l t d.

이제 공급자와 요구 사항에 따라 다음 네트워크 연결 서비스 중 하나를 구입 하 여 중국 내에서 분기를 상호 연결 해야 합니다.

* MPLS/IPVPN 네트워크 
* SDWAN (소프트웨어 정의 WAN)
* 전용 인터넷 액세스

다음으로는 해당 공급자에 동의 하 여 Microsoft 글로벌 네트워크 및 해당 네트워크 입력 Microsoft 글로벌 네트워크에 대 한 브레이크를 제공 해야 합니다. 이 경우 홍콩는 물리적 연결 및 중국의 위치 때문에 매우 중요 합니다.

대부분의 고객에 게는 싱가포르를 사용 하는 것으로 생각 하는 반면, 지도를 확인할 때 중국에 가깝게 보이므로이는 그렇지 않습니다. 네트워크 파이버 maps를 따를 때 거의 모든 네트워크 연결은 베이징, 상하이 및 홍콩를 통해 진행 됩니다. 이렇게 하면 홍콩에 게 더 나은 위치를 선택할 수 있습니다.

공급자에 따라 서비스 제공이 다를 수 있습니다. 아래 표에서는이 문서가 작성 된 시점의 정보에 따라 공급자 및 공급자가 제공 하는 서비스의 예를 보여 줍니다.

| 서비스 | 공급자 예제 |
| --- | --- |
| MPLS/IPVPN 네트워크 |PCCW, 중국 통신 글로벌 |
|SDWAN| PCCW, 중국 통신 글로벌|
| 전용 인터넷 액세스 | PCCW, 홍콩 통신, 중국 Mobil|

공급자를 사용 하 여 Microsoft 전역 백본에 연결 하는 데 사용할 다음 두 가지 솔루션 중에 동의할 수 있습니다.

* 홍콩으로 종료 된 Microsoft Azure ExpressRoute를 가져옵니다. MPLS/IPVPN을 사용 하는 경우가 여기에 해당 합니다. 현재는 Express 경로를 홍콩 전용으로 사용 하는 ICP 라이선스 공급자만 중국 통신 전역입니다. 그러나 Megaport 또는 InterCloud와 같은 클라우드 교환 공급자를 활용 하는 경우 다른 공급자와도 통신할 수 있습니다. 자세한 내용은 [express 경로 연결 공급자](../expressroute/expressroute-locations-providers.md#partners)를 참조 하세요.

* 다음 인터넷 교환 지점 중 하나에서 직접 또는 개인 네트워크 상호 연결을 사용 하 여 전용 인터넷 액세스를 사용 합니다.

다음 목록에서는 홍콩에서 가능한 인터넷 교환을 보여 줍니다.

* AMS-IX 홍콩
* BBIX 홍콩 특별 행정구
* 서 진 홍콩
* HKIX

이 연결을 사용 하는 경우 Microsoft 서비스에 대 한 다음 BGP 홉은 Microsoft의 Microsoft 자치 시스템 번호 (#) 8075 이어야 합니다. 단일 위치 또는 SDWAN 솔루션을 사용 하는 경우 연결을 선택할 수 있습니다.

어떤 경우 든 지 중국어 본토에 대 한 두 번째 및 일반적인 인터넷 브레이크를 제공 하는 것이 좋습니다. 이는 엔터프라이즈 트래픽 간의 트래픽을 Microsoft 365 및 Azure와 같은 클라우드 서비스 및 법률에의 한 인터넷 트래픽을 분할 하는 것입니다.

중국 내의 규격 네트워크 아키텍처는 다음 예와 같습니다.

![여러 분기](./media/interconnect-china/multi-branch.png)

이 예제에서는 Microsoft Global 네트워크 입력 홍콩 특별 행정구를 사용 하는 경우, 이제 [Azure 가상 Wan 글로벌 전송 아키텍처](virtual-wan-global-transit-network-architecture.md) 와 Azure SECURE Virtual wan hub와 같은 추가 서비스를 활용 하 여 중국 외부의 분기 및 데이터 센터에 서비스 및 상호 연결을 사용할 수 있습니다.

## <a name="hub-to-hub-communication"></a><a name="hub-to-hub"></a>허브-허브 통신

이 섹션에서는 상호 연결에 대 한 가상 WAN 허브-허브 통신을 사용 합니다. 이 시나리오에서는 새 가상 WAN 허브 리소스를 만들어 홍콩, 다른 지역의 가상 WAN 허브, Azure 리소스가 이미 있는 지역 또는 연결 하려는 곳에 연결 합니다.

샘플 아키텍처는 다음 예제와 같습니다.

![샘플 WAN](./media/interconnect-china/sample.png)

이 예제에서는 중국 분기가 VPN 또는 MPLS 연결을 사용 하 여 Azure Cloud 중국에 연결 합니다. 글로벌 서비스에 연결 해야 하는 분기는 홍콩에 직접 연결 된 MPLS 또는 인터넷 기반 서비스를 사용 합니다. 홍콩와 기타 지역에서 Express 경로를 사용 하려는 경우 express 경로 회로를 상호 연결 하도록 [express 경로 Global Reach](../expressroute/expressroute-global-reach.md) 를 구성 해야 합니다.

Express 경로 Global Reach 일부 지역에서 사용할 수 없습니다. 예를 들어 브라질 또는 인도와 상호 연결 해야 하는 경우 [클라우드 Exchange 공급자](../expressroute/expressroute-locations.md#connectivity-through-exchange-providers) 를 활용 하 여 라우팅 서비스를 제공 해야 합니다.

아래 그림에서는이 시나리오에 대 한 두 가지 예제를 보여 줍니다.

![Global Reach](./media/interconnect-china/global.png)

## <a name="secure-internet-breakout-for-office-365"></a><a name="secure"></a>Office 365에 대 한 안전한 인터넷 분리

또 다른 고려 사항은 중국 및 가상 WAN 설정 백본 구성 요소와 고객 백본 간의 진입점에 대 한 로깅 뿐만 아니라 네트워크 보안입니다. 대부분의 경우에는 Microsoft Edge 네트워크와 Microsoft 365 서비스에 사용 되는 Azure 프런트 도어 서버에 직접 연결 하기 위해 홍콩에서 인터넷으로의 아웃을 해야 합니다.

가상 WAN을 사용 하는 두 시나리오 모두 [Azure 가상 wan 보안 허브](../firewall-manager/secured-virtual-hub.md)를 활용 합니다. Azure 방화벽 관리자를 사용 하 여 일반 가상 WAN 허브를 안전한 허브로 변경한 다음 해당 허브 내에서 Azure 방화벽을 배포 하 고 관리할 수 있습니다.

다음 그림은이 시나리오의 예를 보여 줍니다.

![웹 및 Microsoft 서비스 트래픽에 대 한 인터넷 분리](./media/interconnect-china/internet.png)

## <a name="architecture-and-traffic-flows"></a><a name="traffic"></a>아키텍처 및 트래픽 흐름

홍콩에 대 한 연결과 관련 된 선택에 따라 전체 아키텍처가 약간 변경 될 수 있습니다. 이 섹션에서는 VPN 또는 SDWAN 및/또는 Express 경로와 다양 하 게 조합 하 여 사용할 수 있는 세 가지 아키텍처를 보여 줍니다.

이러한 모든 옵션은 홍콩의 직접 M365 연결에 대 한 Azure 가상 WAN 보안 허브를 활용 합니다. 이러한 아키텍처는 또한 [office 365 다중 지역](https://docs.microsoft.com/office365/enterprise/office-365-multi-geo) 에 대 한 규정 준수 요구 사항을 지원 하 고 다음 Office 365 전면 도어 위치 근처의 트래픽을 유지 합니다. 따라서 중국의 Microsoft 365을 사용 하는 경우에도 향상 된 기능입니다.

인터넷 연결과 함께 Azure 가상 WAN을 사용 하는 경우 모든 연결은 [Microsoft Azure 피어 링 서비스 (MAPS)](https://docs.microsoft.com/azure/peering-service/about)와 같은 추가 서비스를 활용 하 여 이점을 누릴 수 있습니다. 맵은 타사 인터넷 서비스 공급자의 Microsoft 글로벌 네트워크로 들어오는 트래픽을 최적화 하도록 작성 되었습니다.

### <a name="option-1-sdwan-or-vpn"></a><a name="option-1"></a>옵션 1: SDWAN 또는 VPN

이 섹션에서는 SDWAN 또는 VPN을 사용 하 여 홍콩 및 다른 분기로의 디자인을 설명 합니다. 이 옵션은 가상 WAN 백본의 두 사이트에서 순수한 인터넷 연결을 사용할 때 사용 및 트래픽 흐름을 보여 줍니다. 이 경우에는 전용 인터넷 액세스 또는 ICP 공급자 SDWAN 솔루션을 사용 하 여 연결을 홍콩로 전환 합니다. 다른 분기 에서도 순수한 인터넷 또는 SDWAN 솔루션을 사용 합니다.

![중국에서 홍콩 트래픽](./media/interconnect-china/china-traffic.png)

이 아키텍처에서 모든 사이트는 VPN 및 Azure 가상 WAN을 사용 하 여 Microsoft 글로벌 네트워크에 연결 됩니다. 사이트와 홍콩의 트래픽은 Microsoft 네트워크 trough 전송 되며, 지난 마일에는 일반 인터넷 연결만 사용 됩니다.

### <a name="option-2-expressroute-and-sdwan-or-vpn"></a><a name="option-2"></a>옵션 2: Express 경로 및 SDWAN 또는 VPN

이 섹션에서는 홍콩에서 Express 경로를 사용 하 고 VPN/SDWAN 분기가 있는 다른 분기를 사용 하는 디자인에 대해 설명 합니다. 이 옵션은 홍콩 또는 SDWAN 또는 VPN을 통해 연결 된 다른 분기에서 종료 된 및 Express 경로를 사용 하는 방법을 보여 줍니다. 홍콩 형식의 express 경로는 현재 [Express 경로 파트너](../expressroute/expressroute-locations-providers.md#global-commercial-azure)목록에서 찾을 수 있는 간단한 공급자 목록으로 제한 되어 있습니다.

![중국에서 홍콩 트래픽 Express 경로](./media/interconnect-china/expressroute.png)

예를 들어 남부 대한민국 또는 일본에서 Express 경로를 종료 하는 옵션도 있습니다. 그러나 규정 준수, 규정 및 대기 시간을 고려 하 여 현재 홍콩를 선택 하는 것이 좋습니다.

### <a name="option-3-expressroute-only"></a><a name="option-3"></a>옵션 3: Express 경로 전용

이 섹션에서는 홍콩 및 기타 분기에 대해 Express 경로를 사용 하는 디자인에 대해 설명 합니다. 이 옵션은 양쪽 끝에서 Express 경로를 사용 하는 상호 연결을 보여 줍니다. 여기에 다른 트래픽 흐름이 있습니다. Microsoft 365 트래픽은 Azure 가상 WAN 보호 허브로 이동 하 여 Microsoft Edge 네트워크와 인터넷으로 이동 됩니다.

상호 연결 된 분기 또는 이러한 분기에서 중국의 위치로 이동 하는 트래픽은 해당 아키텍처 내에서 다른 방식으로 수행 됩니다. 현재 가상 WAN은 Express 경로 전송에 대 한 Express 경로를 지원 하지 않습니다. 트래픽은 가상 WAN 허브를 통과 하지 않고 Express 경로 Global Reach 또는 타사 상호 연결을 활용 합니다. 한 Microsoft Enterprise Edge (MSEE)에서 다른로 직접 이동 합니다.

![ExpressRoute Global Reach](./media/interconnect-china/expressroute-virtual.png)

현재 Express 경로 Global Reach는 모든 국가에서 사용할 수 없지만 Azure 가상 WAN을 사용 하 여 솔루션을 구성할 수 있습니다.

예를 들어 Microsoft 피어 링을 사용 하 여 Express 경로를 구성 하 고 해당 피어 링을 통해 VPN 터널을 Azure 가상 WAN에 연결할 수 있습니다. 이제 Global Reach와 타사 공급자 및 서비스 (예: Megaport Cloud)를 사용 하지 않고 VPN과 Express 경로 간의 전송을 다시 사용 하도록 설정 했습니다.

## <a name="next-steps"></a>다음 단계

자세한 내용은 다음 아티클을 참조하세요.

* [Azure 가상 WAN을 사용한 글로벌 전송 네트워크 아키텍처](virtual-wan-global-transit-network-architecture.md)

* [가상 WAN 허브 만들기](virtual-wan-site-to-site-portal.md)

* [가상 WAN 보안 허브 구성](../firewall-manager/secure-cloud-network.md)

* [Azure 피어 링 서비스 미리 보기 개요](https://docs.microsoft.com/azure/peering-service/about)
