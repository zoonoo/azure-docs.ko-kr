---
title: 'Azure 익스프레스루트: 고가용성을 위한 설계'
description: 이 페이지에서는 Azure ExpressRoute를 사용하는 동안 고가용성에 대한 아키텍처 권장 사항을 제공합니다.
services: expressroute
author: rambk
ms.service: expressroute
ms.topic: article
ms.date: 06/28/2019
ms.author: rambala
ms.openlocfilehash: 4c3c6ae5fbdd91e6e44438be7fef2a3a91564a34
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74076672"
---
# <a name="designing-for-high-availability-with-expressroute"></a>익스프레스루트를 통해 고가용성 설계

ExpressRoute는 Microsoft 리소스에 캐리어 급 개인 네트워크 연결을 제공하기 위해 고가용성을 위해 설계되었습니다. 즉, Microsoft 네트워크 내의 ExpressRoute 경로에는 단일 실패 지점이 없습니다. 가용성을 최대화하려면 ExpressRoute 회로의 고객 및 서비스 공급자 세그먼트도 고가용성을 위해 설계해야 합니다. 이 문서에서는 먼저 ExpressRoute를 사용하여 강력한 네트워크 연결을 구축하기 위한 네트워크 아키텍처 고려 사항을 살펴본 다음 ExpressRoute 회로의 고가용성을 개선하는 데 도움이 되는 미세 조정 기능을 살펴보겠습니다.


## <a name="architecture-considerations"></a>아키텍처 고려 사항

다음 그림은 ExpressRoute 회로의 가용성을 최대화하기 위해 ExpressRoute 회로를 사용하여 연결하는 권장 방법을 보여 줍니다.

 [![1]][1개]

고가용성을 위해서는 종단 간 네트워크 전체에서 ExpressRoute 회로의 중복성을 유지하는 것이 중요합니다. 즉, 온-프레미스 네트워크 내에서 중복성을 유지해야 하며 서비스 공급자 네트워크 내의 중복성을 손상시키지 않아야 합니다. 최소한의 중복성을 유지하는 것은 단일 네트워크 오류 지점을 방지하는 것을 의미합니다. 네트워크 장치에 대한 중복 전력 및 냉각을 통해 고가용성이 더욱 향상됩니다.

### <a name="first-mile-physical-layer-design-considerations"></a>첫 번째 마일 물리 층 설계 고려 사항

 동일한 CPE(고객 구내 장비)에서 ExpressRoute 회로의 기본 연결과 보조 연결을 모두 종료하는 경우 온-프레미스 네트워크 내에서 고가용성이 저하됩니다. 또한 동일한 CPE 포트를 통해 기본 연결과 보조 연결을 모두 구성하는 경우(서로 다른 하위 인터페이스에서 두 연결을 종료하거나 파트너 네트워크 내에서 두 연결을 병합하여) 파트너를 강제로 연결합니다. 네트워크 세그먼트의 고가용성을 손상시볼 수 있습니다. 이 손상은 다음 그림에 나와 있습니다.

[![2]][2]

반면에 다른 지리적 위치에서 ExpressRoute 회로의 기본 연결과 보조 연결을 종료하면 연결의 네트워크 성능이 저하될 수 있습니다. 트래픽이 서로 다른 지리적 위치에서 종료되는 기본 연결과 다른 지리적 위치에서 종료되는 보조 연결 간에 트래픽이 적극적으로 부하되는 경우 두 경로 간의 네트워크 대기 시간에서 상당한 차이가 발생할 수 있습니다. 성능. 

지리적 중복 설계 고려 사항은 [ExpressRoute를 사용 하 여 재해 복구에 대 한 설계를][DR]참조 하십시오.

### <a name="active-active-connections"></a>활성-활성 연결

Microsoft 네트워크는 Active-active 모드에서 ExpressRoute 회로의 기본 및 보조 연결을 작동하도록 구성됩니다. 그러나 경로 알림을 통해 ExpressRoute 회로의 중복 연결이 능동 수동 모드에서 작동하도록 강제할 수 있습니다. 보다 구체적인 경로 및 BGP AS 경로 사전 을 광고하는 것은 하나의 경로를 다른 경로보다 선호하는 것으로 만드는 데 사용되는 일반적인 기술입니다.

고가용성을 향상하려면 Active-active 모드에서 ExpressRoute 회로의 연결을 모두 작동하는 것이 좋습니다. 연결이 활성 활성 모드에서 작동하도록 허용하면 Microsoft 네트워크는 흐름별로 연결 전체의 트래픽 균형을 로드합니다.

액티브 패시브 모드에서 ExpressRoute 회로의 기본 및 보조 연결을 실행하면 활성 경로에서 오류가 발생한 후 두 연결이 모두 실패할 위험이 있습니다. 전환 실패의 일반적인 원인은 수동 연결의 능동적 관리 부족, 그리고 패시브 연결 광고 부실 경로입니다.

또는 활성 모드에서 ExpressRoute 회로의 기본 및 보조 연결을 실행하면 ExpressRoute 연결 오류 에 따라 흐름이 실패하고 경로가 다시 라우팅되는 흐름의 약 절반만 발생합니다. 따라서 활성 모드는 MTTR(평균 복구 시간)을 크게 개선하는 데 도움이 됩니다.

### <a name="nat-for-microsoft-peering"></a>마이크로 소프트 피어링에 대한 NAT 

Microsoft 피어링은 공용 끝점 간의 통신을 위해 설계되었습니다. 따라서 일반적으로 온-프레미스 프라이빗 엔드포인트는 Microsoft 피어링을 통해 통신하기 전에 고객 또는 파트너 네트워크에서 공용 IP를 가진 NATed(네트워크 주소 번역)입니다. 활성 모드에서 기본 연결과 보조 연결을 모두 사용했다고 가정하면 NAT가 ExpressRoute 연결 중 하나에서 오류가 발생한 후 복구하는 빈도에 영향을 미칩니다. 두 가지 다른 NAT 옵션은 다음 그림에 나와 있습니다.

[![3]][3]

옵션 1에서 NAT는 ExpressRoute의 기본 연결과 보조 연결 사이의 트래픽을 분할한 후 적용됩니다. NAT의 상태 관리 요구 사항을 충족하기 위해 기본 장치와 보조 장치 간에 독립적인 NAT 풀이 사용되므로 반환 트래픽이 흐름이 송신된 동일한 에지 장치에 도착합니다.

옵션 2에서는 ExpressRoute의 기본 연결과 보조 연결 사이의 트래픽을 분할하기 전에 공통 NAT 풀이 사용됩니다. 트래픽을 분할하기 전에 공통 NAT 풀이 단일 실패 지점을 도입하여 고가용성을 저하시키는 것을 의미하지 않는다는 점을 구별하는 것이 중요합니다.

옵션 1을 사용하면 ExpressRoute 연결 실패 후 해당 NAT 풀에 도달하는 기능이 손상됩니다. 따라서 모든 깨진된 흐름은 해당 창 시간 설정 다음에 TCP 또는 응용 프로그램 계층에 의해 다시 설정되어야 합니다. NAT 풀 중 하나를 사용하여 온-프레미스 서버를 프런트엔드에 사용하고 해당 연결이 실패하는 경우 연결이 수정될 때까지 Azure에서 온-프레미스 서버에 도달할 수 없습니다.

옵션 2의 경우 기본 또는 보조 연결 실패 후에도 NAT에 연결할 수 있습니다. 따라서 네트워크 계층 자체는 패킷의 경로를 다시 라우팅하고 오류 발생 후 더 빠른 복구를 도울 수 있습니다. 

> [!NOTE]
> NAT 옵션 1(기본 및 보조 ExpressRoute 연결에 대한 독립 NAT 풀)을 사용하고 NAT 풀 중 하나에서 온-프레미스 서버로 IP 주소 포트를 매핑하는 경우 해당 경우 해당 경우 익스프레스루트 회로를 통해 서버에 연결할 수 없습니다. 연결이 실패합니다.
> 

## <a name="fine-tuning-features-for-private-peering"></a>프라이빗 피어링을 위한 미세 조정 기능

이 섹션에서는 ExpressRoute 회로의 고가용성을 개선하는 데 도움이 되는 선택적 기능(Azure 배포 및 MTTR에 대한 민감도에 따라 다름)을 검토해 보겠습니다. 구체적으로 ExpressRoute 가상 네트워크 게이트웨이의 영역 인식 배포및 BFD(양방향 포워딩 감지)를 살펴보겠습니다.

### <a name="availability-zone-aware-expressroute-virtual-network-gateways"></a>가용성 영역 인식 익스프레스루트 가상 네트워크 게이트웨이

Azure 지역의 가용성 영역은 장애 도메인과 업데이트 도메인의 조합입니다. 영역 중복 Azure IaaS 배포를 선택하는 경우 ExpressRoute 개인 피어링을 종료하는 영역 중복 가상 네트워크 게이트웨이를 구성할 수도 있습니다. 자세한 내용은 [Azure 가용성 영역의 영역 중복 가상 네트워크 게이트웨이 를][zone redundant vgw]참조하십시오. 영역 중복 가상 네트워크 게이트웨이를 구성하려면 [Azure 가용성 영역에서 영역 중복 가상 네트워크 게이트웨이 만들기를][conf zone redundant vgw]참조하십시오.

### <a name="improving-failure-detection-time"></a>고장 감지 시간 개선

ExpressRoute는 개인 피어링을 통해 BFD를 지원합니다. BFD는 Microsoft 엔터프라이즈 Edge(기본값)와 온-프레미스 측의 BGP 인접 영역 간의 계층 2 네트워크에서 오류 감지 시간을 약 3분(기본값)에서 1초 미만으로 줄입니다. 빠른 오류 검색 시간은 오류 복구를 서두르는 데 도움이 됩니다. 자세한 내용은 [ExpressRoute를 통해 BFD 구성을][BFD]참조하십시오.

## <a name="next-steps"></a>다음 단계

이 문서에서는 ExpressRoute 회로 연결의 고가용성을 위해 설계하는 방법에 대해 설명했습니다. ExpressRoute 회로 피어링 포인트는 지리적 위치에 고정되므로 전체 위치에 영향을 주는 치명적인 오류의 영향을 받을 수 있습니다. 

전체 지역에 영향을 미치는 치명적인 오류를 견딜 수 있는 Microsoft 백본에 대한 지리적 중복 네트워크 연결을 구축하기 위한 설계 고려 사항은 [ExpressRoute 개인 피어링을 사용하여 재해 복구를 위한 설계를][DR]참조하십시오.

<!--Image References-->
[1]: ./media/designing-for-high-availability-with-expressroute/exr-reco.png "익스프레스루트를 사용하여 연결하는 권장 방법"
[2]: ./media/designing-for-high-availability-with-expressroute/suboptimal-lastmile-connectivity.png "최적이 아닌 마지막 마일 연결"
[3]: ./media/designing-for-high-availability-with-expressroute/nat-options.png "개의 NAT 옵션"


<!--Link References-->
[zone redundant vgw]: https://docs.microsoft.com/azure/vpn-gateway/about-zone-redundant-vnet-gateways
[conf zone redundant vgw]: https://docs.microsoft.com/azure/vpn-gateway/create-zone-redundant-vnet-gateway
[Configure Global Reach]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-set-global-reach
[BFD]: https://docs.microsoft.com/azure/expressroute/expressroute-bfd
[DR]: https://docs.microsoft.com/azure/expressroute/designing-for-disaster-recovery-with-expressroute-privatepeering




