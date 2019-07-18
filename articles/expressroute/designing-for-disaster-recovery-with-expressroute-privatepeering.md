---
title: Azure ExpressRoute를 사용 하 여 재해 복구를 위한 설계 | Microsoft Docs
description: 이 페이지는 Azure ExpressRoute를 사용 하는 동안 재해 복구에 대 한 아키텍처 권장을 제공 합니다.
documentationcenter: na
services: networking
author: rambk
manager: tracsman
ms.service: expressroute
ms.topic: article
ms.workload: infrastructure-services
ms.date: 05/25/2019
ms.author: rambala
ms.openlocfilehash: cf2b4e385de901254fde3c3d3e807feda98d5b41
ms.sourcegitcommit: c63e5031aed4992d5adf45639addcef07c166224
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/28/2019
ms.locfileid: "67466066"
---
# <a name="designing-for-disaster-recovery-with-expressroute-private-peering"></a>ExpressRoute 개인 피어 링이 있는 재해 복구를 위한 설계

ExpressRoute는 고가용성 운송 업체 Microsoft 리소스에 대 한 엔터프라이즈급 개인 네트워크 연결을 제공 하기 위해 설계 되었습니다. 즉, Microsoft 네트워크 내에서 ExpressRoute 경로에 있는 오류의 단일 지점이 없는 경우 ExpressRoute 회로의 가용성을 최대화 하기 위해 디자인 고려 사항에 대해서 [ExpressRoute 사용 하 여 고가용성을 위해 디자인][HA]합니다.

그러나 Murphy의 인기 있는 중요 한 원동력이-수행*아무 것도 문제가 발생할 수 있는, 경우*-을 고려해 야 할이 문서의 보겠습니다 집중을 단일 ExpressRoute 회로 사용 하 여 해결할 수 있는 오류를 넘어서는 솔루션이 있습니다. 즉,이 문서의 살펴보겠습니다 지역 중복 ExpressRoute 회로 사용 하 여 재해 복구를 위한 강력한 백 엔드 네트워크 연결을 구축 하기 위한 네트워크 아키텍처 고려 사항에.

## <a name="need-for-redundant-connectivity-solution"></a>중복 연결 솔루션에 대 한 필요성

전체 지역 서비스 (서비스 공급자, 고객 또는 다른 클라우드 서비스 공급자 네트워크는 Microsoft에서 일)을 가져옵니다 성능이 저하 된 인스턴스와 가능성이 있습니다. 이러한 넓은 지역 서비스 영향에 대 한 근본 원인을 천재지변 포함 됩니다. 따라서 비즈니스 연속성 및 업무상 중요 한 응용 프로그램에 대 한 재해 복구를 계획 하는 것이 반드시 합니다.   

여부를 실행 하면 Azure 지역 또는 온-프레미스에서 업무상 중요 한 응용 프로그램 또는 다른 곳에 관계 없이 다른 Azure 지역 장애 조치 사이트로 사용할 수 있습니다. 다음 문서 주소 재해 복구 응용 프로그램 및 프런트 엔드 액세스 관점에서:

- [엔터프라이즈급 재해 복구][Enterprise DR]
- [Azure Site Recovery를 사용한 SMB 재해 복구][SMB DR]

중요 한 작업에 대 한 온-프레미스 네트워크와 Microsoft 간의 ExpressRoute 연결에서 사용 하는 경우 재해 복구 계획에 지역 중복 네트워크 연결도 포함 해야 합니다. 

## <a name="challenges-of-using-multiple-expressroute-circuits"></a>여러 ExpressRoute 회로 사용 하 여 과제

둘 이상의 연결을 사용 하 여 네트워크의 동일한 집합을 상호 연결 하는 경우 네트워크 간에 병렬 경로 도입 합니다. 평행 경로 올바르게 설계를 비대칭 라우팅 발생할 수 있습니다. 상태 저장 엔터티 (예: NAT, 방화벽)의 경로 있는 경우 비대칭 라우팅 트래픽 흐름을 차단할 수 있습니다.  일반적으로 ExpressRoute 개인 피어 링 경로 통해 NAT 또는 방화벽 같은 상태 저장 엔터티에서 제공 하지 있습니다. 따라서 비대칭 라우팅 ExpressRoute 개인 피어 링을 통해 차단 하지 않습니다 반드시 트래픽 흐름.
 
그러나 여부, 상태 저장 엔터티가 있는지 여부에 관계 없이 지역 중복 병렬 경로 간에 트래픽 분산을 로드 하는 경우 일치 하지 않는 네트워크 성능을 발생 합니다. 이 문서에서는 이러한 문제를 해결 하는 방법을 살펴보겠습니다.

## <a name="small-to-medium-on-premises-network-considerations"></a>작은 중간 온-프레미스 네트워크 고려 사항

다음 다이어그램에 표시 된 예제에서는 네트워크를 생각해 보겠습니다. 예제에서는 지역 중복 ExpressRoute 연결을 Contoso의 온-프레미스 위치 및 Azure 지역에서 Contoso의 VNet 간에 설정 됩니다. 다이어그램에서 녹색 실선 (ExpressRoute 1)를 통해 기본 경로 나타냅니다 및 점선으로 표시 된 것 (ExpressRoute 2)를 통해 대기 경로 나타냅니다.

[![1]][1]

재해 복구에 대 한 ExpressRoute 연결을 디자인할 때 고려해 야 합니다.

- 지역 중복 ExpressRoute 회로 사용 하 여
- 서로 다른 ExpressRoute 회로 대 한 다양 한 서비스 공급자 네트워크를 사용 하 여
- 각 ExpressRoute 회로 대 한 디자인 [고가용성][HA]
- 고객 네트워크에 다른 위치에서 다른 ExpressRoute 회로 종료합니다.

기본적으로 모든 ExpressRoute 경로 통해 동일한 경로 보급 하는 경우 Azure는 온-프레미스 바인딩된 트래픽 부하 분산 같은 비용 다중 경로 (ECMP) 라우팅을 사용 하 여 모든 ExpressRoute 경로에서.

그러나 지역 중복 ExpressRoute 회로 사용 하 여 해야 다른 네트워크 경로 (특히 네트워크 대기 시간)를 사용 하 여 다른 네트워크 성능을 고려해 야 합니다. 정상적인 작업 중 더 일관 된 네트워크 성능을 얻으려면 최소 대기 시간을 제공 하는 ExpressRoute 회로 선호 하는 것이 좋습니다.

다른 (효율성 순서 대로 나열 됨) 다음 방법 중 하나를 사용 하 여 보다 이상의 ExpressRoute 회로 사용 하려면 Azure 영향을 줄 수 있습니다.

- 다른 ExpressRoute 회로에 비해 기본 ExpressRoute 회로 통해 보다 구체적인 경로 보급
- 기본 ExpressRoute 회로에 가상 네트워크를 연결 하는 연결에서 더 높은 연결 가중치를 구성 합니다.
- (경로 앞)에 따라 더 AS 경로 사용 하 여 작은 기본 ExpressRoute 회로 통해 경로 보급

### <a name="more-specific-route"></a>보다 구체적인 경로

다음 다이어그램은 보다 구체적인 경로 보급을 사용 하 여 미칠 ExpressRoute 경로 선택 합니다. 그림 예에서 Contoso 온-프레미스/24 IP 범위는 기본 경로 (ExpressRoute 1)를 통해 두 개의/25 주소 범위와 / 24 대기 경로 (ExpressRoute 2)를 통해 보급 됩니다.

[![2]][2]

/ 25 보다 구체적인 이기 때문 에/24에 비해 Azure가 보냅니다 10.1.11.0/24 표준 상태의 ExpressRoute 1을 통해 대상이 지정 된 트래픽을 합니다. ExpressRoute 1의 두 연결의 작동이 다음 VNet은 참조 ExpressRoute 2; 통해서만 10.1.11.0/24 경로 보급 알림 및 따라서 대기 회로이 오류 상태에 사용 됩니다.

### <a name="connection-weight"></a>연결 가중치

다음 스크린샷은 Azure portal 통해 ExpressRoute 연결의 가중치를 구성 하는 방법을 보여 줍니다.

[![3]][3]

다음 다이어그램에서는 연결 가중치를 사용 하 여 미칠 ExpressRoute 경로 선택 합니다. 기본 연결 가중치는 0입니다. 아래 예제에서 ExpressRoute 1에 대 한 연결의 가중치는 100으로 구성 됩니다. VNet에 둘 이상의 ExpressRoute 회로 통해 보급 된 경로 접두사를 받으면 VNet 가중치가 가장 높은 연결을 선호 합니다.

[![4]][4]

ExpressRoute 1의 두 연결의 작동이 다음 VNet은 참조 ExpressRoute 2; 통해서만 10.1.11.0/24 경로 보급 알림 및 따라서 대기 회로이 오류 상태에 사용 됩니다.

### <a name="as-path-prepend"></a>경로 앞에 추가

다음 다이어그램에서는 미칠 ExpressRoute 경로 선택을 경로 앞에 추가 하는 대로 사용 하 여 보여 줍니다. 다이어그램 1 ExpressRoute를 통해 경로 보급 eBGP의 기본 동작을 나타냅니다. ExpressRoute 2를 통해 경로 보급 알림에서 온-프레미스 네트워크의 ASN은 또한에 추가 경로 경로로. 동일한 경로 eBGP 경로 선택 프로세스당 여러 ExpressRoute 회로 통해 수신 되는 경우 VNet 경로와 가장 짧은 경로 선호 합니다. 

[![5]][5]

ExpressRoute 1의 두 연결의 작동이 VNet이 ExpressRoute 2를 통해서만 10.1.11.0/24 경로 광고를 표시 됩니다. 검사가 경로로 길수록는 생기고 있습니다. 따라서이 오류 상태에 대기 회로 사용할 것입니다.

기술 중 하나를 사용 하 여 다른 ExpressRoute 중 하나를 사용 하려면 Azure를 영향을 주는 경우에 해야 온-프레미스 네트워크도 Azure 바인딩 트래픽을 비대칭 흐름을 방지 하려면 동일한 ExpressRoute 경로 선호 합니다. 일반적으로 로컬 기본 설정 값에 다른 하나의 ExpressRoute 회로 사용 하려면 온-프레미스 네트워크를 영향을 주는 사용 됩니다. 로컬 환경에 내부 BGP (iBGP) 메트릭 되어 있습니다. 가장 높은 로컬 기본 설정 값을 사용 하 여 BGP 경로 것이 좋습니다.

> [!IMPORTANT]
> 특정 ExpressRoute 회로 사용 하 여 대기로 능동적으로 관리 하 고 주기적으로 장애 조치 작업을 테스트 해야 합니다. 
> 

## <a name="large-distributed-enterprise-network"></a>대규모 분산된 엔터프라이즈 네트워크

대규모 분산된 엔터프라이즈 네트워크에 있는 경우에 여러 ExpressRoute 회로 포함할 가능성이 있습니다. 이 섹션에서는 활성-활성 ExpressRoute 회로 사용 하 여 추가 독립에서 회로 하지 않고도 재해 복구를 설계 하는 방법을 살펴보겠습니다. 

다음 다이어그램에 설명 된 예제를 살펴보겠습니다. 예제에서는 Contoso에는 두 개의 서로 다른 두 피어 링 위치에서 ExpressRoute 회로 통해 서로 다른 두 Azure 지역에서 두 Contoso IaaS 배포에 연결 된 온-프레미스 위치입니다. 

[![6]][6]

지역 간 교차 위치 (지역 1/region2 location2/location1에) 트래픽이 라우팅되는 방법을에 영향을 줄에 재해 복구 구축 하는 방법 다르게 교차 지역 위치 트래픽을 라우팅하는 두 개의 서로 다른 재해 아키텍처를 살펴보겠습니다.

### <a name="scenario-1"></a>시나리오 1

첫 번째 시나리오에서는 모든 Azure 지역과 온-프레미스 네트워크 간의 트래픽은 안정적인 상태에서 로컬 ExpressRoute 회로 통해 재해 복구를 설계 해 보겠습니다. 로컬 ExpressRoute 회로가 실패 하면, 그런 다음 원격 ExpressRoute 회로 Azure 사이의 모든 트래픽 흐름에 사용 되 고 온-프레미스 네트워크.

다음 다이어그램은 시나리오 1과 같습니다. 다이어그램에서 녹색 선은 VNet1 및 온-프레미스 네트워크 간의 트래픽 흐름에 대 한 경로 나타냅니다. 파란색 선은 VNet2와 온-프레미스 네트워크 간의 트래픽 흐름에 대 한 경로 나타냅니다. 실선 안정적인 상태의 원하는 경로 나타내고, 파선 안정적인 상태 트래픽 흐름을 전달 하는 해당 ExpressRoute 회로의 오류 트래픽 경로 나타냅니다. 

[![7]][7]

에 바인딩된 트래픽을 온-프레미스 네트워크에 대 한 로컬 피어 링 위치 ExpressRoute 연결을 사용 하려면 Vnet을 영향을 주는 연결 가중치를 사용 하 여 시나리오를 설계 하 합니다. 솔루션 완료 하는 데 대칭 역방향 트래픽 흐름을 확인 해야 합니다. ExpressRoute 회로 사용 하려면 (에 ExpressRoute 회로 종료 온-프레미스 쪽에서) BGP 라우터 간에 로컬 기본 설정을 iBGP 세션에서 사용할 수 있습니다. 다음 다이어그램은 솔루션을 보여 줍니다. 

[![8]][8]

### <a name="scenario-2"></a>시나리오 2

시나리오 2는 다음 다이어그램에 나와 있습니다. 다이어그램에서 녹색 선은 VNet1 및 온-프레미스 네트워크 간의 트래픽 흐름에 대 한 경로 나타냅니다. 파란색 선은 VNet2와 온-프레미스 네트워크 간의 트래픽 흐름에 대 한 경로 나타냅니다. 안정적인-상태 (다이어그램에서 실선)에서는 모든 Vnet 및 온-프레미스 위치 간의 트래픽은 Microsoft 백본을 통해 대부분의 경우 flow 간의 상호 연결을 통과 온-프레미스 위치 (점선의 오류 상태에만 다이어그램) ExpressRoute입니다.

[![9]][9]

다음 다이어그램은 솔루션을 보여 줍니다. 시나리오를 하거나 설계 수 있듯이, VNet 경로 선택에 영향을 줍니다 (옵션 2) 앞에 보다 구체적인 경로 (옵션 1) 또는 AS path를 사용 하 여 합니다. Azure 바인딩된 트래픽을 온-프레미스 네트워크 경로 선택에 영향을 줍니다, 떨어지는 것으로 선호 온-프레미스 위치 간 상호 연결을 구성 해야 합니다. 로드하우 선호으로 상호 연결 링크를 구성 하면 온-프레미스 네트워크 내에서 사용 하는 라우팅 프로토콜에 따라 달라 집니다. IBGP 또는 IGP (OSPF 또는 IS IS)를 사용 하 여 메트릭을 사용 하 여 로컬 기본 설정을 사용할 수 있습니다.

[![10]][10]


## <a name="next-steps"></a>다음 단계

이 문서에서는 ExpressRoute 회로 개인 피어 링 연결의 재해 복구를 위한 디자인 하는 방법에 설명 했습니다. 다음 문서 주소 재해 복구 응용 프로그램 및 프런트 엔드 액세스 관점에서:

- [엔터프라이즈급 재해 복구][Enterprise DR]
- [Azure Site Recovery를 사용한 SMB 재해 복구][SMB DR]

<!--Image References-->
[1]: ./media/designing-for-disaster-recovery-with-expressroute-pvt/one-region.png "작아서 중간 크기 온-프레미스 네트워크 고려 사항"
[2]: ./media/designing-for-disaster-recovery-with-expressroute-pvt/specificroute.png "보다 구체적인 경로 사용 하 여 경로 선택에 영향을 주는"
[3]: ./media/designing-for-disaster-recovery-with-expressroute-pvt/configure-weight.png "Azure portal 통해 연결 가중치를 구성 합니다."
[4]: ./media/designing-for-disaster-recovery-with-expressroute-pvt/connectionweight.png "연결 가중치를 사용 하 여 경로 선택에 영향을 주는"
[5]: ./media/designing-for-disaster-recovery-with-expressroute-pvt/aspath.png "경로로 사용 하 여 경로 선택에 영향을 주는 앞에 추가"
[6]: ./media/designing-for-disaster-recovery-with-expressroute-pvt/multi-region.png "대규모 분산된 온-프레미스 네트워크 고려 사항"
[7]: ./media/designing-for-disaster-recovery-with-expressroute-pvt/multi-region-arch1.png "scenario 1"
[8]: ./media/designing-for-disaster-recovery-with-expressroute-pvt/multi-region-sol1.png "활성-활성 ExpressRoute 회로 솔루션 1"
[9]: ./media/designing-for-disaster-recovery-with-expressroute-pvt/multi-region-arch2.png "시나리오 2"
[10]: ./media/designing-for-disaster-recovery-with-expressroute-pvt/multi-region-sol2.png "활성-활성 ExpressRoute 회로 솔루션 2"

<!--Link References-->
[HA]: https://docs.microsoft.com/azure/expressroute/designing-for-high-availability-with-expressroute
[Enterprise DR]: https://azure.microsoft.com/solutions/architecture/disaster-recovery-enterprise-scale-dr/
[SMB DR]: https://azure.microsoft.com/solutions/architecture/disaster-recovery-smb-azure-site-recovery/
[con wgt]: https://docs.microsoft.com/azure/expressroute/expressroute-optimize-routing#solution-assign-a-high-weight-to-local-connection
[AS Path Pre]: https://docs.microsoft.com/azure/expressroute/expressroute-optimize-routing#solution-use-as-path-prepending





