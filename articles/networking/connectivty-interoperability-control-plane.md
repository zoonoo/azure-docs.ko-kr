---
title: 'ExpressRoute, 사이트 간 VPN 및 VNet 피어링의 상호 운용성 - 제어 평면 분석: Azure 백 엔드 연결 기능 상호 운용성 | Microsoft Docs'
description: 이 페이지는 ExpressRoute, 사이트 간 VPN 및 VNet 피어링 기능의 상호 운용성을 분석하기 위해 만들어진 테스트 설정에 대한 제어 평면 분석을 제공합니다.
documentationcenter: na
services: networking
author: rambk
manager: tracsman
ms.service: expressroute,vpn-gateway,virtual-network
ms.topic: article
ms.workload: infrastructure-services
ms.date: 10/18/2018
ms.author: rambala
ms.openlocfilehash: ee887da18b5666e61bc25365791b2e7dffb925e0
ms.sourcegitcommit: 9e179a577533ab3b2c0c7a4899ae13a7a0d5252b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/23/2018
ms.locfileid: "49947121"
---
# <a name="interoperability-of-expressroute-site-to-site-vpn-and-vnet-peering---control-plane-analysis"></a>ExpressRoute, 사이트 간 VPN 및 VNet 피어링의 상호 운용성 - 제어 평면 분석

이 문서에서는 테스트 설정의 제어 평면 분석을 탐색해 보겠습니다. 테스트 설정을 검토하려면 [테스트 설정][Setup]을 참조하세요. 테스트 설정 구성 세부 정보를 검토하려면 [테스트 설정 구성][Configuration]을 참조하세요.

제어 평면 분석에서는 기본적으로 토폴로지 내의 네트워크 간에 교환되는 경로를 검사합니다. 제어 평면 분석은 다양한 네트워크에서 토폴로지를 보는 방법에 도움이 됩니다.

##<a name="hub-and-spoke-vnet-perspective"></a>허브 및 스포크 VNet 관점

다음 다이어그램은 허브 VNet 및 스포크 VNet(파란색으로 강조 표시) 관점에서 네트워크를 보여 줍니다. 또한 이 다이어그램은 다양한 네트워크의 ASN(Autonomous System Number) 및 서로 다른 네트워크 간에 교환되는 경로를 보여 줍니다. 

[![1]][1]

VNet의 ExpressRoute 게이트웨이의 ASN이 Microsoft MSEE(Microsoft Enterprise Edge Router)의 ASN과 다른 것을 알 수 있습니다. ExpressRoute 게이트웨이는 개인 ASN(65515)을 사용하며 MSEE는 공용 ASN(12076)을 전역적으로 사용합니다. MSEE는 피어이므로 ExpressRoute 피어링을 구성할 때 피어 ASN으로 12076을 사용합니다. Azure 쪽의 경우 MSEE는 ExpressRoute GW를 사용하여 eBGP 피어링을 설정합니다. MSEE가 각 ExpressRoute 피어링에 대해 설정하는 이중 eBGP 피어링은 제어 평면 수준에서 투명합니다. 따라서 ExpressRoute 경로 테이블을 볼 때 VNet의 접두사에 대해 VNet의 ExpressRoute GW ASN이 표시됩니다. 예제 ExpressRoute 경로 테이블의 스크린샷이 아래에 나와 있습니다. 

[![5]][5]

Azure 내에서 ASN은 피어링 관점에서만 중요합니다. 기본적으로 ExpressRoute GW와 VPN GW 둘 다의 ASN은 65515입니다.

##<a name="on-premises-location-1-and-remote-vnet-perspective-via-expressroute-1"></a>ExpressRoute-1을 통한 온-프레미스 위치-1 및 원격 VNet 관점

온-프레미스 위치-1 및 원격 VNet은 모두 ExpressRoute 1을 통해 허브 VNet에 연결되므로 이들은 아래 다이어그램과 같이 토폴로지의 같은 관점을 공유합니다.

[![2]][2]

##<a name="on-premises-location-1-and-branch-vnet-perspective-via-site-to-site-vpn"></a>사이트 간 VPN을 통한 온-프레미스 위치-1 및 분기 VNet 관점

온-프레미스 위치-1 및 분기 VNet은 모두 사이트 간 VPN 연결을 통해 허브 VNet의 VPN GW에 연결되므로 이들은 아래 다이어그램과 같이 토폴로지의 같은 관점을 공유합니다.

[![3]][3]

##<a name="on-premises-location-2-perspective"></a>온-프레미스 위치-2 관점

온-프레미스 위치-2는 ExpressRoute 2의 개인 피어링을 통해 허브 VNet에 연결됩니다. 

[![4]][4]

## <a name="further-reading"></a>추가 참고 자료

### <a name="using-expressroute-and-site-to-site-vpn-connectivity-in-tandem"></a>탠덤에서 ExpressRoute 및 사이트 간 VPN 연결 사용

####  <a name="site-to-site-vpn-over-expressroute"></a>ExpressRoute를 통한 사이트 간 VPN

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

테스트 설정의 제어 평면 분석 및 Azure 네트워크 모니터링 기능 뷰는 [데이터 평면 분석][Data-Analysis]을 참조하세요.

ExpressRoute 게이트웨이에 연결할 수 있는 ExpressRoute 회로 수, ExpressRoute 회로에 연결할 수 있는 ExpressRoute 게이트웨이 수 또는 ExpressRoute의 다른 규모 제한에 대해 알아보려면 [ExpressRoute FAQ][ExR-FAQ]를 참조하세요.


<!--Image References-->
[1]: ./media/backend-interoperability/HubView.png "토폴로지의 허브 및 스포크 VNet 관점"
[2]: ./media/backend-interoperability/Loc1ExRView.png "토폴로지의 ExpressRoute 1을 통한 위치-1 및 원격 VNet 관점"
[3]: ./media/backend-interoperability/Loc1VPNView.png "토폴로지의 S2S VPN을 통한 위치-1 및 분기 VNet 관점"
[4]: ./media/backend-interoperability/Loc2View.png "토폴로지의 위치-2 관점"
[5]: ./media/backend-interoperability/ExR1-RouteTable.png "ExpressRoute 1 RouteTable"

<!--Link References-->
[Setup]: https://docs.microsoft.com/azure/networking/connectivty-interoperability-preface
[Configuration]: https://docs.microsoft.com/azure/networking/connectivty-interoperability-config
[ExpressRoute]: https://docs.microsoft.com/azure/expressroute/expressroute-introduction
[VPN]: https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways
[VNet]: https://docs.microsoft.com/azure/virtual-network/tutorial-connect-virtual-networks-portal
[Configuration]: https://docs.microsoft.com/azure/networking/connectivty-interoperability-configuration
[Control-Analysis]:https://docs.microsoft.com/azure/networking/connectivty-interoperability-control-plane
[Data-Analysis]: https://docs.microsoft.com/azure/networking/connectivty-interoperability-data-plane
[ExR-FAQ]: https://docs.microsoft.com/azure/expressroute/expressroute-faqs
[S2S-Over-ExR]: https://docs.microsoft.com/azure/expressroute/site-to-site-vpn-over-microsoft-peering
[ExR-S2S-CoEx]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-coexist-resource-manager
[Hub-n-Spoke]: https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/hub-spoke
[Deploy-NVA]: https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/nva-ha
[VNet-Config]: https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-peering




