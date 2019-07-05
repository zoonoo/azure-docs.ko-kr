---
title: Azure ExpressRoute를 사용 하 여 고가용성을 위한 설계 | Microsoft Docs
description: 이 페이지는 Azure ExpressRoute를 사용 하는 동안 고가용성에 대 한 아키텍처 권장을 제공 합니다.
documentationcenter: na
services: networking
author: rambk
manager: tracsman
ms.service: expressroute
ms.topic: article
ms.workload: infrastructure-services
ms.date: 06/28/2019
ms.author: rambala
ms.openlocfilehash: 4984b30daf6170873cad9472bfed2d879af57efe
ms.sourcegitcommit: c63e5031aed4992d5adf45639addcef07c166224
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/28/2019
ms.locfileid: "67466641"
---
# <a name="designing-for-high-availability-with-expressroute"></a>ExpressRoute 사용 하 여 고가용성을 위한 디자인

ExpressRoute는 고가용성 운송 업체 Microsoft 리소스에 대 한 엔터프라이즈급 개인 네트워크 연결을 제공 하기 위해 설계 되었습니다. 즉, Microsoft 네트워크 내에서 ExpressRoute 경로에 있는 오류의 단일 지점이 없는 경우 가용성을 최대화 하려면 고객 및 ExpressRoute 회로의 서비스 공급자 세그먼트 해야도 직접 설계 고가용성에 대 한 합니다. 이 문서를 먼저 살펴보겠습니다는 ExpressRoute를 사용 하 여 강력한 네트워크 연결을 구축 하기 위한 네트워크 아키텍처 고려 사항을 살펴보고 다음 ExpressRoute 회로의 고가용성을 향상 시킬 수 있도록 미세 조정 기능에 살펴보겠습니다.


## <a name="architecture-considerations"></a>아키텍처 고려 사항

다음 그림에서는 ExpressRoute 회로 사용 하 여 ExpressRoute 회로의 가용성을 극대화 하기 위한 연결에 대 한 권장 되는 방법은 보여 줍니다.

 [![1]][1]

고가용성을 위해 종단 간 네트워크 전체에서 ExpressRoute 회로의 중복을 유지 하기 위해 반드시 합니다. 즉, 온-프레미스 네트워크 내에서 중복을 유지 해야 하 고 서비스 공급자 네트워크 내에서 중복을 손상 하지 않아야 합니다. 단일 네트워크 오류 지점을 방지 의미 최소한 중복성을 유지 관리 합니다. 중복 된 전원 및 냉각 장치를 추가 하는 네트워크에 대 한 고가용성을 향상 합니다.

### <a name="first-mile-physical-layer-design-considerations"></a>첫 번째 마일 물리적 계층 디자인 고려 사항

 모두는 ExpressRoute 회로에 동일한 고객 프레미스 장비 (CPE)의 기본 및 보조 연결을 종료 하는 경우 온-프레미스 네트워크 내에서 고가용성을 손상 하는 있습니다. 또한 모두 기본 및 보조 동일한 포트를 통해의 연결 (또는 다른 서브 인터페이스에서 두 개의 연결을 종료 하 여 파트너 네트워크 내에서 두 개의 연결을 병합 하 여) CPE 구성한 경우 있습니다 강제 적용 하는 파트너 고가용성도 해당 네트워크 세그먼트에서 손상 됩니다. 이 손상 다음 그림에 나와 있습니다.

[![2]][2]

반면에 주 데이터베이스와 다른 지리적 위치에는 ExpressRoute 회로의 보조 연결을 종료 하는 경우 다음 있습니다 수 되돌릴 연결의 네트워크 성능을 합니다. 두 경로 간의 네트워크 대기 시간이 상당한 잠재적 차이 최적이 아닌 네트워크 초래 경우 트래픽을 적극적으로 주 데이터베이스와 다른 지리적 위치에서 종료 되는 보조 연결에서 부하 분산 성능을 제공 합니다. 

지역 중복 디자인 고려 사항을 참조 하세요 [ExpressRoute 사용 하 여 재해 복구를 위해 설계할][DR]합니다.

### <a name="active-active-connections"></a>활성-활성 연결

Microsoft 네트워크는 활성-활성 모드에서 ExpressRoute 회로의 기본 및 보조 연결을 작동 하도록 구성 됩니다. 그러나에 경로 보급을 통해 활성-수동 모드에서 작동 하는 ExpressRoute 회로의 중복 연결을 강제할 수 있습니다. 보다 구체적인 경로 및 BGP 보급 하 고 다른 보다 선호 하는 하나의 경로 확인 하는 일반적인 기술에는 path 앞에 추가 합니다.

고가용성을 향상 하는 것이 좋습니다 활성-활성 모드에서 ExpressRoute 회로의 연결을 작동 하도록 합니다. 활성-활성 모드에서 작동 하는 연결 하면, Microsoft 네트워크 트래픽을 분산 단위 흐름으로 연결을 통해 로드 됩니다.

두 연결 실패 활성 경로에서 오류 발생의 위험 활성-수동 모드 면에서 ExpressRoute 회로의 기본 및 보조 연결을 실행 합니다. 전환에 실패에 대 한 일반적인 원인은 유효 하지 않은 경로 보급 하는 수동 연결 및 수동 연결을 활성 관리 부족 합니다.

또는 실패 및 가져오기에 대 한 흐름에서 결과 ExpressRoute 회로의 기본 및 보조 연결을 활성-활성 모드에서 실행 해 다음 ExpressRoute 연결 오류가 발생 합니다. 따라서 활성-활성 모드 크게 개선에 도움이 되도록 평균 시간에 복구 (MTTR).

### <a name="nat-for-microsoft-peering"></a>Microsoft 피어 링에 대 한 NAT 

Microsoft 피어 링은 공용 끝점 간의 통신을 위해 설계 되었습니다. 따라서 일반적으로 온-프레미스 개인 끝점은 네트워크 주소 변환 (nat 된)는 고객 또는 파트너 네트워크에서 공용 IP를 사용 하 여 Microsoft 피어 링을 통해 통신 하려면 먼저입니다. 기본 및 보조 연결을 사용 하 여 활성-활성 모드에서 가정 하 고, 위치 및 방법을 NAT 있습니다 다음 ExpressRoute 연결 중 하나에서 오류가 복구 하는 속도에 영향을 줍니다. 두 가지 NAT 옵션은 다음 그림에 나와 있습니다.

[![3]][3]

옵션 1에서에서 NAT ExpressRoute의 기본 및 보조 연결 간에 트래픽을 분할 하는 이후에 적용 됩니다. NAT의 상태 저장 요구 사항에 맞게, 독립적인 NAT 풀 반환 트래픽도 흐름이 있는 egressed 동일한 edge 장치에 도착 하 주 및 보조 장치 간에 사용 됩니다.

옵션 2에서에서 일반적인 NAT 풀 ExpressRoute의 기본 및 보조 연결 간에 트래픽을 분하기 전에 사용 되었습니다. 트래픽을 분할 하기 전에 일반적인 NAT 풀 아닙니다 구분 해야 하므로 높은 가용성을 손상 시 키 지 오류의 단일 지점을 소개 합니다.

옵션 1, ExpressRoute 연결 오류가 다음을 사용 하 여 해당 NAT 풀에 연결할 수 있는 손상 되었습니다. 따라서 끊어진된 흐름 되어야 할 모든 다시 설정 하거나 TCP 또는 해당 창 제한 시간을 다음 응용 프로그램 계층. NAT 풀 경우 프런트 엔드는 온-프레미스 서버 중 하나가 데 연결 해결 될 때까지 Azure에서 온-프레미스 서버 해당 연결이 실패할 경우 연락할 수 없는 합니다.

옵션 2 사용 하 여 NAT는 기본 또는 보조 연결이 실패 한 후에 연결할 수 있습니다. 따라서 자체 네트워크 계층 오류 다음 모드 해제 패킷 및 도움말 빠른 복구를 라우팅할 수 있습니다. 

> [!NOTE]
> 서버는 ExpressRoute를 통해 연결할 수 수 NAT 옵션 1 (기본 및 보조 ExpressRoute 연결에 대 한 독립적인 NAT 풀)을 사용 하는 온-프레미스 서버에 NAT 풀 중 하나에서 IP 주소의 포트를 매핑하는 경우 회로 해당 연결이 실패합니다.
> 

## <a name="fine-tuning-features-for-private-peering"></a>개인 피어 링에 대 한 기능을 미세 조정

이 섹션에서는 알려주세요 (Azure 배포 및 MTTR를 여러분이 중요도)에 따라 선택적 검토 기능을 ExpressRoute 회로의 고가용성을 개선 합니다. 특히 ExpressRoute 가상 네트워크 게이트웨이 및 양방향 전달 검색 (BFD)의 표준 시간대 인식 배포를 검토해 보겠습니다.

### <a name="availability-zone-aware-expressroute-virtual-network-gateways"></a>ExpressRoute 가상 네트워크 게이트웨이 인식 하는 가용성 영역

Azure 지역의 가용성 영역은 장애 도메인과 업데이트 도메인의 조합입니다. 영역 중복 Azure IaaS 배포를 선택 하는 경우 ExpressRoute 개인 피어 링을 종료 하는 영역 중복 가상 네트워크 게이트웨이 구성할 수도 있습니다. 추가 알아보려면 [Azure 가용성 영역에서 영역 중복 가상 네트워크 게이트웨이에 대 한][zone redundant vgw]. To configure zone-redundant virtual network gateway, see [Create a zone-redundant virtual network gateway in Azure Availability Zones][conf zone redundant vgw]합니다.

### <a name="improving-failure-detection-time"></a>실패 감지 시간 개선

ExpressRoute 개인 피어 링을 통해 BFD를 지원합니다. BFD 검색 시간을 줄여 줍니다 오류 Microsoft Enterprise Edge (Msee) 및 온-프레미스 쪽에서 해당 BGP 이웃 노드 간에 계층 2 네트워크를 통해 약 3 분 (기본값)에서 초 내에 있습니다. 빠른 실패 감지 시간 hastening 오류 복구 하는 데 도움이 됩니다. 추가 알아보려면 [ExpressRoute 통해 구성 BFD][BFD]합니다.

## <a name="next-steps"></a>다음 단계

이 문서는 ExpressRoute 회로 연결의 높은 가용성을 설계 하는 방법에 설명 했습니다. ExpressRoute 회로 피어 링 지점 지리적 위치에 고정 되었으며 전체 위치에 영향을 주는 심각한 오류로 인해 영향을 받을 수 있습니다. 

지역 중복 네트워크 연결 전체 지역에 영향을 하는 치명적인 오류를 견딜 수 있는 Microsoft 백본에 빌드 디자인 고려 사항을 참조 하세요. [ExpressRoute 개인 피어 링를사용하여재해복구를위한설계][DR].

<!--Image References-->
[1]: ./media/designing-for-high-availability-with-expressroute/exr-reco.png "ExpressRoute를 사용 하 여 연결 하는 데 권장"
[2]: ./media/designing-for-high-availability-with-expressroute/suboptimal-lastmile-connectivity.png "Suboptimal 마지막 마일 연결"
[3]: ./media/designing-for-high-availability-with-expressroute/nat-options.png "NAT 옵션"


<!--Link References-->
[zone redundant vgw]: https://docs.microsoft.com/azure/vpn-gateway/about-zone-redundant-vnet-gateways
[conf zone redundant vgw]: https://docs.microsoft.com/azure/vpn-gateway/create-zone-redundant-vnet-gateway
[Configure Global Reach]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-set-global-reach
[BFD]: https://docs.microsoft.com/azure/expressroute/expressroute-bfd
[DR]: https://docs.microsoft.com/azure/expressroute/designing-for-disaster-recovery-with-expressroute-privatepeering




