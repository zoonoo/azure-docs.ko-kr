---
title: 'Azure 익스프레스루트: 재해 복구를 위한 설계'
description: 이 페이지에서는 Azure ExpressRoute를 사용하는 동안 재해 복구에 대한 아키텍처 권장 사항을 제공합니다.
services: expressroute
author: rambk
ms.service: expressroute
ms.topic: article
ms.date: 05/25/2019
ms.author: rambala
ms.openlocfilehash: 726a014983c0da959d72b7976fef2ebb2c6e9b9e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74076697"
---
# <a name="designing-for-disaster-recovery-with-expressroute-private-peering"></a>ExpressRoute 개인 피어링을 통해 재해 복구 설계

ExpressRoute는 Microsoft 리소스에 캐리어 급 개인 네트워크 연결을 제공하기 위해 고가용성을 위해 설계되었습니다. 즉, Microsoft 네트워크 내의 ExpressRoute 경로에는 단일 실패 지점이 없습니다. ExpressRoute 회로의 가용성을 최대화하기 위한 설계 고려 사항은 [ExpressRoute를 사용하여 고가용성을 위한 설계를][HA]참조하십시오.

그러나 머피의 인기 있는 속담을*취하면 문제가 발생할 수 있다면*이 기사에서는 단일 ExpressRoute 회로를 사용하여 해결할 수 있는 오류를 넘어서는 솔루션에 초점을 맞출 수 있습니다. 즉, 이 문서에서는 지리적 중복 ExpressRoute 회로를 사용하여 재해 복구를 위한 강력한 백 엔드 네트워크 연결을 구축하기 위한 네트워크 아키텍처 고려 사항을 살펴보겠습니다.

## <a name="need-for-redundant-connectivity-solution"></a>중복 연결 솔루션 필요

Microsoft, 네트워크 서비스 공급자, 고객 또는 기타 클라우드 서비스 공급자와 같은 전체 지역 서비스(또는 다른 클라우드 서비스 공급자)가 저하될 수 있는 가능성과 경우가 있습니다. 이러한 지역 전반의 서비스 영향의 근본 원인은 자연 재해를 포함합니다. 따라서 비즈니스 연속성 및 미션 크리티컬 애플리케이션의 경우 재해 복구를 계획하는 것이 중요합니다.   

Azure 리전또는 온-프레미스 또는 다른 곳에서 미션 크리티컬 응용 프로그램을 실행하든 관계없이 다른 Azure 지역을 장애 조치 사이트로 사용할 수 있습니다. 다음 문서에서는 응용 프로그램 및 프런트 엔드 액세스 관점에서 재해 복구를 해결합니다.

- [엔터프라이즈급 재해 복구][Enterprise DR]
- [Azure Site Recovery를 사용한 SMB 재해 복구][SMB DR]

미션 크리티컬 작업을 위해 온-프레미스 네트워크와 Microsoft 간의 ExpressRoute 연결을 이용하는 경우 재해 복구 계획에는 지역 중복 네트워크 연결도 포함되어야 합니다. 

## <a name="challenges-of-using-multiple-expressroute-circuits"></a>여러 ExpressRoute 회로를 사용하는 과제

두 개 이상의 연결을 사용하여 동일한 네트워크 집합을 상호 연결하면 네트워크 간에 병렬 경로가 발생합니다. 병렬 경로가 제대로 설계되지 않은 경우 비대칭 라우팅으로 이어질 수 있습니다. 경로에 상태 관리 엔터티(예: NAT, 방화벽)가 있는 경우 비대칭 라우팅이 트래픽 흐름을 차단할 수 있습니다.  일반적으로 ExpressRoute 개인 피어링 경로를 통해 NAT 또는 방화벽과 같은 상태 개인 엔터티를 가로지르지 않습니다. 따라서 ExpressRoute 개인 피어링을 통해 비대칭 라우팅이 반드시 트래픽 흐름을 차단하지는 않습니다.
 
그러나 상태 관리 엔터티가 있는지 여부에 관계없이 지역 중복 병렬 경로에서 트래픽 균형을 조정하는 경우 네트워크 성능이 일치하지 않습니다. 이 문서에서는 이러한 문제를 해결하는 방법에 대해 살펴보겠습니다.

## <a name="small-to-medium-on-premises-network-considerations"></a>중소 규모의 온-프레미스 네트워크 고려 사항

다음 다이어그램에 설명된 예제 네트워크를 살펴보겠습니다. 이 예제에서는 Contoso의 온-프레미스 위치와 Azure 지역의 Contoso의 VNet 간에 지리적 중복 ExpressRoute 연결이 설정됩니다. 다이어그램에서 녹색 선은 기본 경로(ExpressRoute 1를 통해)를 나타내고 점선은 대기 경로를 나타냅니다(ExpressRoute 2를 통해).

[![1]][1]

재해 복구를 위해 ExpressRoute 연결을 디자인할 때는 다음을 고려해야 합니다.

- 지리적 중복 익스프레스루트 회로 사용
- 다른 ExpressRoute 회로에 대해 다양한 서비스 공급자 네트워크를 사용하여
- [고가용성을][HA] 위해 각 ExpressRoute 회로 설계
- 고객 네트워크의 다른 위치에서 다른 ExpressRoute 회로종료

기본적으로 모든 ExpressRoute 경로에서 동일한 경로를 광고하는 경우 Azure는 동일 비용 다중 경로(ECMP) 라우팅을 사용하여 모든 ExpressRoute 경로에서 온-프레미스 바운드 트래픽을 로드-균형 조정합니다.

그러나 지리적 중복 ExpressRoute 회로를 사용하면 네트워크 경로가 다른 다른 네트워크 성능(특히 네트워크 대기 시간)을 고려해야 합니다. 정상적인 작동 중에 보다 일관된 네트워크 성능을 얻으려면 최소한의 대기 시간을 제공하는 ExpressRoute 회로를 선호할 수 있습니다.

다음 기술 중 하나를 사용하여 다른 ExpressRoute 회로보다 하나의 ExpressRoute 회로를 선호하도록 Azure에 영향을 주면(효과 순서대로 나열됨).

- 다른 익스프레스루트 회로에 비해 선호하는 ExpressRoute 회로를 통해 보다 구체적인 경로를 광고합니다.
- 가상 네트워크를 기본 ExpressRoute 회로에 연결하는 연결에서 더 높은 연결 가중치 구성
- 더 긴 AS 경로(AS 경로 프리프엔드)로 덜 선호되는 ExpressRoute 회로를 통해 경로를 광고합니다.

### <a name="more-specific-route"></a>보다 구체적인 경로

다음 다이어그램은 보다 구체적인 경로 보급 알림을 사용하여 ExpressRoute 경로 선택에 영향을 주는 것을 보여 줍니다. 그림의 예에서 Contoso 온-프레미스/24 IP 범위는 기본 경로(ExpressRoute 1)를 통해 두 개의 /25 주소 범위와 대기 경로(ExpressRoute 2)를 통해 /24로 광고됩니다.

[![2]][2]

/25는 /24에 비해 더 구체적이기 때문에 Azure는 정상 상태의 ExpressRoute 1을 통해 10.1.11.0/24로 향하는 트래픽을 보냅니다. ExpressRoute 1의 연결이 모두 다운되면 VNet은 ExpressRoute 2를 통해서만 10.1.11.0/24 경로 광고를 볼 수 있습니다. 따라서 이 장애 상태에서 대기 회로가 사용됩니다.

### <a name="connection-weight"></a>연결 중량

다음 스크린샷은 Azure 포털을 통해 ExpressRoute 연결의 가중치를 구성하는 것을 보여 줍니다.

[![3]][3]

다음 다이어그램은 연결 가중치를 사용하여 ExpressRoute 경로 선택에 영향을 주는 것을 보여 줍니다. 기본 연결 가중치는 0입니다. 아래 예제에서 ExpressRoute 1에 대한 연결의 가중치는 100으로 구성됩니다. VNet이 두 개 이상의 ExpressRoute 회로를 통해 광고되는 경로 접두사를 받으면 VNet은 가장 높은 가중치의 연결을 선호합니다.

[![4]][4]

ExpressRoute 1의 연결이 모두 다운되면 VNet은 ExpressRoute 2를 통해서만 10.1.11.0/24 경로 광고를 볼 수 있습니다. 따라서 이 장애 상태에서 대기 회로가 사용됩니다.

### <a name="as-path-prepend"></a>AS 경로 프레벤드

다음 다이어그램은 AS 경로 준비 를 사용하여 ExpressRoute 경로 선택에 영향을 주는 것을 보여 줍니다. 다이어그램에서 ExpressRoute 1을 둘러싼 경로 보급 알림은 eBGP의 기본 동작을 나타냅니다. ExpressRoute 2를 통해 경로 광고에서 온-프레미스 네트워크의 ASN은 경로의 AS 경로에 추가로 준비됩니다. eBGP 경로 선택 프로세스에 따라 여러 ExpressRoute 회로를 통해 동일한 경로가 수신되면 VNet은 가장 짧은 AS 경로가 있는 경로를 선호합니다. 

[![5]][5]

ExpressRoute 1의 연결이 모두 중단되면 VNet은 ExpressRoute 2를 통해서만 10.1.11.0/24 경로 광고를 볼 수 있습니다. 따라서 AS 경로가 길수록 관련이 없습니다. 따라서 이 장애 상태에서 대기 회로가 사용됩니다.

모든 기술을 사용하여 Azure에서 다른 기술보다 ExpressRoute 중 하나를 선호하도록 영향을 받는 경우 온-프레미스 네트워크에서비대칭 흐름을 피하기 위해 Azure 바인딩 트래픽에 대해 동일한 ExpressRoute 경로를 선호해야 합니다. 일반적으로 로컬 기본 설정 값은 온-프레미스 네트워크에 영향을 주어 다른 회로보다 하나의 ExpressRoute 회로를 선호하는 데 사용됩니다. 로컬 기본 설정은 내부 BGP(iBGP) 메트릭입니다. 로컬 기본 설정 값이 가장 높은 BGP 경로가 선호됩니다.

> [!IMPORTANT]
> 특정 ExpressRoute 회로를 대기로 사용하는 경우 이를 적극적으로 관리하고 장애 조치 작업을 주기적으로 테스트해야 합니다. 
> 

## <a name="large-distributed-enterprise-network"></a>대규모 분산 엔터프라이즈 네트워크

대규모 분산 엔터프라이즈 네트워크가 있는 경우 여러 ExpressRoute 회로가 있을 수 있습니다. 이 섹션에서는 추가 대기 회로없이 활성 ExpressRoute 회로를 사용하여 재해 복구를 설계하는 방법을 살펴보겠습니다. 

다음 다이어그램에 설명된 예제를 살펴보겠습니다. 이 예에서 Contoso는 두 개의 서로 다른 피어링 위치에서 ExpressRoute 회로를 통해 두 개의 서로 다른 Azure 지역에 두 개의 Contoso IaaS 배포에 연결된 두 개의 온-프레미스 위치가 있습니다. 

[![6]][6]

재해 복구를 설계하는 방법은 지역간 교차 위치(지역1/지역2에서 location2/location1) 트래픽이 라우팅되는 방식에 영향을 미칩니다. 지역 위치 트래픽을 다르게 라우팅하는 두 가지 재해 아키텍처를 살펴보겠습니다.

### <a name="scenario-1"></a>시나리오 1

첫 번째 시나리오에서는 Azure 지역과 온-프레미스 네트워크 간의 모든 트래픽이 정상 상태의 로컬 ExpressRoute 회로를 통해 흐르도록 재해 복구를 설계해 보겠습니다. 로컬 ExpressRoute 회로가 실패하면 원격 ExpressRoute 회로가 Azure와 온-프레미스 네트워크 간의 모든 트래픽 흐름에 사용됩니다.

시나리오 1은 다음 다이어그램에 나와 있습니다. 다이어그램에서 녹색 선은 VNet1 과 온-프레미스 네트워크 간의 트래픽 흐름 경로를 나타냅니다. 파란색 선은 VNet2와 온-프레미스 네트워크 간의 트래픽 흐름 경로를 나타냅니다. 실선은 정상 상태에서 원하는 경로를 나타내고 파선은 정상 상태 트래픽 흐름을 전달하는 해당 ExpressRoute 회로의 실패시 트래픽 경로를 나타냅니다. 

[![7]][7]

연결 가중치를 사용하여 시나리오를 설계하여 VNet에 영향을 주어 온-프레미스 네트워크 바인딩 트래픽에 대한 로컬 피어링 위치 ExpressRoute에 대한 연결을 선호할 수 있습니다. 솔루션을 완료하려면 대칭 역방향 트래픽 흐름을 보장해야 합니다. 익스프레스루트 회로를 선호하기 위해 BGP 라우터(온-프레미스 측에서 종료되는) 사이의 iBGP 세션에서 로컬 기본 설정을 사용할 수 있습니다. 솔루션은 다음 다이어그램에 나와 있습니다. 

[![8]][8]

### <a name="scenario-2"></a>시나리오 2

시나리오 2는 다음 다이어그램에 나와 있습니다. 다이어그램에서 녹색 선은 VNet1 과 온-프레미스 네트워크 간의 트래픽 흐름 경로를 나타냅니다. 파란색 선은 VNet2와 온-프레미스 네트워크 간의 트래픽 흐름 경로를 나타냅니다. 정상 상태(다이어그램의 실선)에서 VNet과 온-프레미스 위치 간의 모든 트래픽은 대부분 Microsoft 백본을 통해 흐르고, 온-프레미스 위치 간의 상호 연결을 통해 실패 상태(점선에서만 흐릅니다.). 익스프레스루트의 다이어그램)을 참조하십시오.

[![9]][9]

솔루션은 다음 다이어그램에 나와 있습니다. 그림과 같이 보다 구체적인 경로(옵션 1) 또는 AS 경로 준비(옵션 2)를 사용하여 VNet 경로 선택에 영향을 주어 시나리오를 설계할 수 있습니다. Azure 바인딩 트래픽에 대한 온-프레미스 네트워크 경로 선택에 영향을 주려면 온-프레미스 위치 간의 상호 연결을 덜 선호하도록 구성해야 합니다. 상호 연결 링크를 구성하는 하우는 온-프레미스 네트워크 내에서 사용되는 라우팅 프로토콜에 따라 달라집니다. IGp(OSPF 또는 IS-IS)에서 iBGP 또는 메트릭을 사용하여 로컬 기본 설정을 사용할 수 있습니다.

[![10]][10]


## <a name="next-steps"></a>다음 단계

이 문서에서는 ExpressRoute 회로 개인 피어링 연결의 재해 복구를 설계하는 방법에 대해 설명했습니다. 다음 문서에서는 응용 프로그램 및 프런트 엔드 액세스 관점에서 재해 복구를 해결합니다.

- [엔터프라이즈급 재해 복구][Enterprise DR]
- [Azure Site Recovery를 사용한 SMB 재해 복구][SMB DR]

<!--Image References-->
[1]: ./media/designing-for-disaster-recovery-with-expressroute-pvt/one-region.png "중소 규모의 온-프레미스 네트워크 고려 사항"
[보다]: ./media/designing-for-disaster-recovery-with-expressroute-pvt/specificroute.png "구체적인 경로를 이용한 2가지 경로 선택"
 "Azure 포털을 통한 연결 가중치 구성" [3개]: ./media/designing-for-disaster-recovery-with-expressroute-pvt/configure-weight.png
[4]: ./media/designing-for-disaster-recovery-with-expressroute-pvt/connectionweight.png "연결 가중치를 사용한 4가지 경로 선택"
 "AS 경로 프레펜드를 사용한 5가지 경로 선택" [5]: ./media/designing-for-disaster-recovery-with-expressroute-pvt/aspath.png
[6개의]: ./media/designing-for-disaster-recovery-with-expressroute-pvt/multi-region.png "대규모 분산 온-프레미스 네트워크 고려 사항"
[7]: ./media/designing-for-disaster-recovery-with-expressroute-pvt/multi-region-arch1.png "시나리오 1"
[8]: ./media/designing-for-disaster-recovery-with-expressroute-pvt/multi-region-sol1.png "액티브 익스프레스루트 회로 솔루션 1"
[9]: ./media/designing-for-disaster-recovery-with-expressroute-pvt/multi-region-arch2.png "시나리오 2"
[10]: ./media/designing-for-disaster-recovery-with-expressroute-pvt/multi-region-sol2.png액티브 "익스프레스루트 회로 솔루션 2"

<!--Link References-->
[HA]: https://docs.microsoft.com/azure/expressroute/designing-for-high-availability-with-expressroute
[Enterprise DR]: https://azure.microsoft.com/solutions/architecture/disaster-recovery-enterprise-scale-dr/
[SMB DR]: https://azure.microsoft.com/solutions/architecture/disaster-recovery-smb-azure-site-recovery/
[con wgt]: https://docs.microsoft.com/azure/expressroute/expressroute-optimize-routing#solution-assign-a-high-weight-to-local-connection
[AS Path Pre]: https://docs.microsoft.com/azure/expressroute/expressroute-optimize-routing#solution-use-as-path-prepending





