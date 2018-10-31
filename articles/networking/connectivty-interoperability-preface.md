---
title: 'ExpressRoute, 사이트 간 VPN 및 VNet 피어링의 상호 운용성 - 테스트 설정: Azure 백 엔드 연결 기능 상호 운용성 | Microsoft Docs'
description: 이 페이지는 ExpressRoute, 사이트 간 VPN 및 VNet 피어링 기능의 상호 운용성을 분석하는 데 사용되는 테스트 설정을 제공합니다.
documentationcenter: na
services: networking
author: rambk
manager: tracsman
ms.service: expressroute,vpn-gateway,virtual-network
ms.topic: article
ms.workload: infrastructure-services
ms.date: 10/18/2018
ms.author: rambala
ms.openlocfilehash: e859a0a3ac35a9d9f2dab579b7609192e599f90f
ms.sourcegitcommit: 9e179a577533ab3b2c0c7a4899ae13a7a0d5252b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/23/2018
ms.locfileid: "49947111"
---
# <a name="interoperability-of-expressroute-site-to-site-vpn-and-vnet-peering---test-setup"></a>ExpressRoute, 사이트 간 VPN 및 VNet 피어링의 상호 운용성 - 테스트 설정
이 문서에서는 서로 다른 기능이 제어 평면과 데이터 평면 수준에서 모두 서로 상호 운용하는 방법을 분석하는 데 사용할 수 있는 테스트 설정을 식별합니다. 테스트 설정에 설명하기 전에 이처럼 서로 다른 Azure 네트워킹 기능이 무엇을 의미하는지 간략히 살펴보겠습니다.

ExpressRoute: ExpressRoute 개인 피어링을 사용하면 온-프레미스 네트워크의 개인 IP 공간을 Azure VNet 배포에 직접 연결할 수 있습니다.  ExpressRoute를 사용하여 더 높은 대역폭 및 개인 연결을 달성할 수 있습니다. SLA를 사용하여 ExpressRoute 연결을 제공하는 여러 ExpressRoute 에코 파트너가 있습니다. ExpressRoute 및 해당 구성 방법에 대해 자세히 알아보려면 [ExpressRoute 소개][ExpressRoute]를 참조하세요.

사이트 간 VPN: 인터넷 또는 ExpressRoute를 통해 온-프레미스 네트워크를 Azure에 안전하게 연결하려면 S2S(사이트 간) VPN 옵션을 사용할 수 있습니다. S2S VPN을 Azure에 연결하도록 구성하는 방법을 알아보려면 [VPN Gateway 구성][VPN]을 참조하세요.

VNet 피어링: VNet 피어링은 VNete(가상 네트워크) 간의 연결을 설정하는 데 사용할 수 있습니다. VNet 피어링에 대한 자세히 알아보려면 [VNet 피어링 자습서][VNet]를 참조하세요.

##<a name="test-setup"></a>테스트 설정

아래 다이어그램에 테스트 설정이 나와 있습니다.

[![1]][1]

테스트 설정의 중심 부분은 Azure 지역 1의 허브 VNet입니다. 허브 VNet은 다음과 같이 다양한 네트워크에 연결됩니다.

1.  Vnet 피어링을 통해 VNet에. 스포크 VNet은 허브 VNet의 두 게이트웨이에 대해 모두 원격 액세스를 포함합니다.
2.  사이트 간 VPN을 통해 분기 VNet에. 연결은 eBGP를 사용하여 경로를 교환합니다.
3.  ExpressRoute 개인 피어링을 통해 기본 경로로 그리고 사이트 간 VPN 연결을 통해 백업 경로로 위치-1 온-프레미스 네트워크에. 이 문서의 나머지 부분에서는 이 ExpressRoute 회로를 ExpressRoute1이라고 부릅니다. 기본적으로 ExpressRoute 회로는 고가용성을 위해 중복 연결을 제공합니다. ExpressRoute1에서 보조 MSEE를 지향하는 보조 CE 라우터의 하위 인터페이스는 비활성화되어 있습니다. 이는 위 다이어그램에서 이중선 화살표 위에 빨간색 줄을 사용하여 표시됩니다.
4.  다른 ExpressRoute 개인 피어링을 통해 위치-2 온-프레미스 네트워크에. 이 문서의 나머지 부분에서는 이 두 번째 ExpressRoute 회로를 ExpressRoute2라고 부릅니다.
5.  또한 ExpressRoute1은 허브 VNet과 위치-1 온-프레미스를 모두 Azure 지역 2의 원격 VNet에 연결합니다.

## <a name="further-reading"></a>추가 참고 자료

### <a name="using-expressroute-and-site-to-site-vpn-connectivity-in-tandem"></a>탠덤에서 ExpressRoute 및 사이트 간 VPN 연결 사용

#### <a name="site-to-site-vpn-over-expressroute"></a>ExpressRoute를 통한 사이트 간 VPN 

사이트 간 VPN은 ExpressRoute Microsoft 피어링을 통해 온-프레미스 네트워크와 Azure VNet 간에 비밀성, 재생 방지, 신뢰성 및 무결성을 가지고 개인적으로 데이터를 교환하도록 구성할 수 있습니다. ExpressRoute Microsoft 피어링을 통해 터널 모드에서 사이트 간 IPSec VPN을 구성하는 방법에 관한 자세한 내용은 [ExpressRoute Microsoft 피어링을 통한 사이트 간 VPN][S2S-Over-ExR]을 참조하세요. 

Microsoft 피어링을 통해 S2S VPN을 구성할 때 주요 제한은 처리량입니다. IPSec 터널을 통한 처리량은 VPN GW 용량에 의해 제한됩니다. VPN GW 처리량은 ExpressRoute 처리량에 비해 작습니다. 그러한 시나리오에서 매우 안전한 트래픽의 경우 IPSec 터널 및 모든 다른 트래픽의 경우 개인 피어링을 사용하면 ExpressRoute 대역폭 사용량을 최적화하는 데 도움이 될 수 있습니다.

#### <a name="site-to-site-vpn-as-a-secure-failover-path-for-expressroute"></a>ExpressRoute에 대한 안전한 장애 조치(failover) 경로인 사이트 간 VPN
ExpressRoute는 고가용성을 보장하기 위해 중복 회로 쌍으로 제공됩니다. 다른 Azure 지역에서 지역 중복 ExpressRoute 연결을 구성할 수 있습니다. 또한 이 테스트 설정에서 수행한 것처럼 지정된 Azure 지역 내에서 ExpressRoute 연결에 대한 장애 조치(failover) 경로를 원하는 경우 사이트 간 VPN을 사용하여 그렇게 할 수 있습니다. ExpressRoute 및 S2S VPN을 통해 동일한 접두사를 보급하는 경우 Azure는 S2S VPN보다 ExpressRoute를 선호합니다. ExpressRoute와 S2S VPN 간의 비대칭 라우팅을 방지하기 위해 온-프레미스 네트워크 구성도 S2S VPN 연결보다 ExpressRoute를 선호하여 교환해야 합니다.

ExpressRoute 및 사이트 간 VPN이 공존하는 연결에 대한 자세한 내용은 [ExpressRoute 및 사이트 간 공존][ExR-S2S-CoEx]을 참조하세요.

### <a name="extending-backend-connectivity-to-spoke-vnets-and-branch-locations"></a>백 엔드 연결을 스포크 VNet 및 분기 위치로 확장

#### <a name="spoke-vnet-connectivity-using-vnet-peering"></a>VNet 피어링을 사용하여 스포크 VNet 연결

허브와 스포크 VNet 아키텍처는 널리 사용됩니다. 허브는 스포크 VNet 간 및 온-프레미스 네트워크에 대해 중앙 연결 지점 역할을 하는 Azure의 VNet(가상 네트워크)입니다. 스포크는 허브와 피어링하는 VNet이며 워크로드를 격리하는 데 사용할 수 있습니다. 트래픽은 ExpressRoute 또는 VPN 연결을 통해 온-프레미스 데이터 센터와 허브 사이를 흐릅니다. 아키텍처에 대한 자세한 내용은 [허브 및 스포크 아키텍처][Hub-n-Spoke]를 참조하세요.

하위 지역 내에서 VNet 피어링을 통해 스포크 VNet은 허브 VNet 게이트웨이(VPN과 ExpressRoute 게이트웨이 모두)를 사용하여 원격 네트워크와 통신할 수 있습니다.

#### <a name="branch-vnet-connectivity-using-site-to-site-vpn"></a>사이트 간 VPN을 사용하여 분기 VNet 연결

분기 VNet(다른 하위 지역의)과 온-프레미스 네트워크가 허브 VNet을 통해 서로 통신하도록 하려는 경우 원시 Azure 솔루션은 VPN을 사용하는 사이트 간 VPN 연결입니다. 대안 옵션은 허브의 라우팅을 위해 NVA를 사용하는 것입니다.

VPN Gateway 구성에 대해서는 [VPN Gateway 구성][VPN]을 참조하세요. 고가용성 NVA 배포에 대해서는 [고가용성 NVA 배포][Deploy-NVA]를 참조하세요.

## <a name="next-steps"></a>다음 단계

테스트 토폴로지의 구성 세부 정보는 [구성 세부 정보][Configuration]를 참조하세요.

테스트 설정에 대한 제어 평면 분석 및 토폴로지의 서로 다른 VNet/VLAN 뷰를 이해하려면 [제어 평면 분석][Control-Analysis]을 참조하세요.

테스트 설정의 제어 평면 분석 및 Azure 네트워크 모니터링 기능 뷰는 [데이터 평면 분석][Data-Analysis]을 참조하세요.

ExpressRoute 게이트웨이에 연결할 수 있는 ExpressRoute 회로 수, ExpressRoute 회로에 연결할 수 있는 ExpressRoute 게이트웨이 수 또는 ExpressRoute의 다른 규모 제한에 대해 알아보려면 [ExpressRoute FAQ][ExR-FAQ]를 참조하세요.



<!--Image References-->
[1]: ./media/backend-interoperability/TestSetup.png "테스트 토폴로지"

<!--Link References-->
[ExpressRoute]: https://docs.microsoft.com/azure/expressroute/expressroute-introduction
[VPN]: https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways
[VNet]: https://docs.microsoft.com/azure/virtual-network/tutorial-connect-virtual-networks-portal
[Configuration]: https://docs.microsoft.com/azure/connectivty-interoperability-configuration
[Control-Analysis]:https://docs.microsoft.com/azure/connectivty-interoperability-control-plane
[Data-Analysis]: https://docs.microsoft.com/azure/connectivty-interoperability-data-plane
[ExR-FAQ]: https://docs.microsoft.com/azure/expressroute/expressroute-faqs
[S2S-Over-ExR]: https://docs.microsoft.com/azure/expressroute/site-to-site-vpn-over-microsoft-peering
[ExR-S2S-CoEx]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-coexist-resource-manager
[Hub-n-Spoke]: https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/hub-spoke
[Deploy-NVA]: https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/nva-ha




