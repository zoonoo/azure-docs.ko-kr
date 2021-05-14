---
title: 'Azure ExpressRoute: 재해 복구용으로 디자인'
description: 이 페이지에서는 Azure ExpressRoute를 사용하는 동안 재해 복구를 위한 아키텍처 권장 사항을 제공합니다.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: article
ms.date: 03/22/2021
ms.author: duau
ms.openlocfilehash: d0aa9e8bfd565eeb7599d52adc0ac5b854e750bb
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105937229"
---
# <a name="designing-for-disaster-recovery-with-expressroute-private-peering"></a>ExpressRoute 개인 피어링을 사용하여 재해 복구용으로 디자인

ExpressRoute는 Microsoft 리소스에 대한 이동 통신 사업자급 프라이빗 네트워크 연결을 제공하는 고가용성을 위해 설계되었습니다. 즉, Microsoft 네트워크의 ExpressRoute 경로에는 단일 실패 지점이 없습니다. ExpressRoute 회로의 가용성을 최대화하기 위한 디자인 고려 사항은 [ExpressRoute를 사용한 고가용성을 위한 디자인][HA]을 참조하세요.

그러나 Murphy의 인기 있는 격언(*잘못될 일은 잘못된다*)을 고려하여, 이 문서에서는 단일 ExpressRoute 회로를 사용하여 해결할 수 있는 실패를 넘어서는 솔루션에 초점을 맞춰 보겠습니다. 지역 중복 ExpressRoute 회로를 사용하여 재해 복구를 위한 강력한 백 엔드 네트워크 연결을 구축하기 위한 네트워크 아키텍처 고려 사항을 살펴봅니다.

>[!NOTE]
>이 문서에 설명된 개념은 Virtual WAN 또는 Virtual WAN 외부에서 ExpressRoute 회로를 만들 때 동일하게 적용됩니다.
>

## <a name="need-for-redundant-connectivity-solution"></a>중복 연결 솔루션 필요

전체 지역 서비스(Microsoft, 네트워크 서비스 공급자, 고객 또는 기타 클라우드 서비스 공급자의 서비스)가 저하되는 경우와 가능성이 있습니다. 지역 전체에 서비스가 영향을 미치는 근본 원인으로는 자연재해가 있습니다. 따라서 비즈니스 연속성 및 중요 업무용 애플리케이션의 경우 재해 복구를 계획하는 것이 중요합니다.   

Azure 지역, 온-프레미스 또는 다른 곳에서 중요 업무용 애플리케이션을 실행하는지 여부와 관계없이 다른 Azure 지역을 장애 조치(failover) 사이트로 사용할 수 있습니다. 다음 문서에서는 애플리케이션과 프런트 엔드 액세스 관점에서 재해 복구를 다룹니다.

- [엔터프라이즈급 재해 복구][Enterprise DR]
- [Azure Site Recovery를 사용한 SMB 재해 복구][SMB DR]

중요 업무용 작업을 위해 온-프레미스 네트워크와 Microsoft 간 ExpressRoute 연결을 사용하는 경우 재해 복구 플랜에는 지역 중복 네트워크 연결도 포함되어야 합니다. 

## <a name="challenges-of-using-multiple-expressroute-circuits"></a>여러 ExpressRoute 회로를 사용하는 문제

둘 이상의 연결을 사용하여 같은 네트워크 세트를 상호 연결하면 네트워크 간에 병렬 경로가 도입됩니다. 병렬 경로가 제대로 설계되지 않으면 비대칭 라우팅이 발생할 수 있습니다. 경로에 상태 저장 엔터티(예: NAT, 방화벽)가 있으면 비대칭 라우팅이 트래픽 흐름을 차단할 수 있습니다.  일반적으로 NAT 또는 방화벽과 같은 상태 저장 엔터티는 ExpressRoute 개인 피어링 경로를 통해 제공되지 않습니다. 따라서 ExpressRoute 개인 피어링을 통한 비대칭 라우팅이 반드시 트래픽 흐름을 차단하는 것은 아닙니다.
 
그러나 상태 저장 엔터티가 있는지에 관계없이 지역 중복 병렬 경로에서 트래픽을 부하 분산하면 네트워크 성능이 일관되지 않을 수 있습니다. 이 지역 중복 병렬 경로는 [위치별 공급자](expressroute-locations-providers.md#partners) 페이지에 있는 같은 대도시 또는 다른 대도시를 통과할 수 있습니다. 

### <a name="same-metro"></a>같은 대도시

[많은 대도시](expressroute-locations-providers.md#global-commercial-azure)에 두 개의 ExpressRoute 위치가 있습니다. 예를 들어 *Amsterdam* 과 *Amsterdam2* 입니다. 중복도를 디자인할 때 같은 대도시에 있는 두 위치를 모두 사용하여 Azure에 대한 두 개의 병렬 경로를 빌드할 수 있습니다. 이 디자인의 장점은 애플리케이션 장애 조치가 발생할 때 온-프레미스 애플리케이션과 Microsoft 간의 엔드투엔드 대기 시간이 거의 동일하게 유지된다는 것입니다. 그러나 지진과 같은 자연재해가 발생하면 두 경로의 연결을 더는 사용할 수 없습니다.

### <a name="different-metros"></a>다른 대도시

중복도를 위해 다른 대도시를 사용하는 경우 같은 [지정학적 지역​​](expressroute-locations-providers.md#locations)에서 보조 위치를 선택해야 합니다. 지정학적 지역 외부의 위치를 ​​선택하려면 병렬 경로의 두 회로 모두에 프리미엄 SKU를 사용해야 합니다. 이 구성의 장점은 자연재해로 인해 두 링크가 중단될 가능성이 훨씬 낮다는 것입니다. 그러나 엔드투엔드 대기 시간은 늘어나는 단점이 있습니다.

이 문서에서는 지역 중복 경로를 구성할 때 발생할 수 있는 문제를 해결하는 방법을 설명해 보겠습니다.

## <a name="small-to-medium-on-premises-network-considerations"></a>중소규모의 온-프레미스 네트워크 고려 사항

다음 다이어그램에 설명된 예제 네트워크를 살펴보겠습니다. 이 예에서 지역 중복 ExpressRoute 연결은 Contoso의 온-프레미스 위치와 Azure 지역에 있는 Contoso의 VNet 간에 설정됩니다. 다이어그램에서 녹색 실선은 기본 경로(ExpressRoute 1을 통함)를 나타내고 점선은 대기 경로(ExpressRoute 2를 통함)를 나타냅니다.

[![1]][1]

재해 복구를 위한 ExpressRoute 연결을 디자인할 때 다음을 고려해야 합니다.

- 지역 중복 ExpressRoute 회로 사용
- 다른 ExpressRoute 회로에 다양한 서비스 공급자 네트워크 사용
- [고가용성][HA]을 위해 각 ExpressRoute 회로 디자인
- 고객 네트워크의 다른 위치에 있는 다른 ExpressRoute 회로 종료

기본적으로 모든 ExpressRoute 경로에 걸쳐 동일하게 경로를 보급하는 경우 Azure는 ECMP(등가 다중 경로) 라우팅을 사용하여 모든 ExpressRoute 경로에서 온-프레미스 바인딩된 트래픽을 부하 분산합니다.

그러나 지역 중복 ExpressRoute 회로에서는 다른 네트워크 경로(특히 네트워크 대기 시간)를 사용하는 다른 네트워크 성능을 고려해야 합니다. 정상 작동 중에 더 일관된 네트워크 성능을 얻으려면 최소 대기 시간을 제공하는 ExpressRoute 회로를 사용하는 것이 좋습니다.

다음 기술 중 하나를 사용하여 하나의 ExpressRoute 회로를 다른 회로보다 선호하도록 Azure에 영향을 줄 수 있습니다(효율성 순서대로 나열됨).

- 다른 ExpressRoute 회로와 비교하여 선호하는 ExpressRoute 회로를 통해 더 구체적인 경로 보급
- 가상 네트워크를 기본 ExpressRoute 회로에 링크하는 연결에서 더 높은 연결 가중치 구성
- 더 긴 AS 경로(AS 경로가 앞에 추가됨)를 사용하여 덜 선호되는 ExpressRoute 회로를 통해 경로 보급

### <a name="more-specific-route"></a>더 구체적인 경로

다음 다이어그램은 더 구체적인 경로 보급 알림을 사용하여 ExpressRoute 경로 선택에 미치는 영향을 보여줍니다. 설명된 예제에서 Contoso 온-프레미스 /24 IP 범위는 기본 경로(ExpressRoute 1)를 통해 2개의 /25 주소 범위로 보급되고 대기 경로(ExpressRoute 2)를 통해 /24로 보급됩니다.

[![2]][2]

/25가 /24보다 더 구체적이기 때문에 Azure는 정상 상태에서 ExpressRoute 1을 통해 10.1.11.0/24로 향하는 트래픽을 보냅니다. ExpressRoute 1의 두 연결이 모두 중단되면 VNet에는 ExpressRoute 2를 통해서만 10.1.11.0/24 경로 보급 알림이 표시됩니다. 따라서 이 실패 상태에서는 대기 회로가 사용됩니다.

### <a name="connection-weight"></a>연결 가중치

다음 스크린샷은 Azure Portal을 통해 ExpressRoute 연결의 가중치를 구성하는 방법을 보여 줍니다.

[![3]][3]

다음 다이어그램은 연결 가중치를 사용하여 ExpressRoute 경로 선택에 주는 영향을 보여 줍니다. 기본 연결 가중치는 0입니다. 아래 예제에서는 ExpressRoute 1의 연결 가중치가 100으로 구성됩니다. VNet이 둘 이상의 ExpressRoute 회로를 통해 보급된 경로 접두사를 수신하면 VNet은 가중치가 가장 높은 연결을 선호합니다.

[![4]][4]

ExpressRoute 1의 두 연결이 모두 중단되면 VNet에는 ExpressRoute 2를 통해서만 10.1.11.0/24 경로 보급 알림이 표시됩니다. 따라서 이 실패 상태에서는 대기 회로가 사용됩니다.

### <a name="as-path-prepend"></a>AS 경로 앞에 추가

다음 다이어그램은 AS 경로 앞에 추가를 사용하여 ExpressRoute 경로 선택에 주는 영향을 보여 줍니다. 다이어그램에서 ExpressRoute 1을 통한 경로 보급 알림은 eBGP의 기본 동작을 나타냅니다. ExpressRoute 2를 통한 경로 보급 알림에서 온-프레미스 네트워크의 ASN이 경로의 AS 경로 앞에 추가됩니다. 여러 ExpressRoute 회로를 통해 같은 경로가 수신되면 eBGP 경로 선택 프로세스에 따라 VNet은 AS 경로가 가장 짧은 경로를 선호합니다. 

[![5]][5]

ExpressRoute 1의 두 연결이 모두 중단되면 ExpressRoute 2를 통해서만 VNet에 10.1.11.0/24 경로 보급 알림이 표시됩니다. 결과적으로 긴 AS 경로는 적절하지 않습니다. 따라서 이 실패 상태에서는 대기 회로가 사용됩니다.

기술 중 하나를 사용하여 Azure에서 ExpressRoute 중 하나를 선호하도록 영향을 주는 경우, 비대칭 흐름을 방지하도록 온-프레미스 네트워크에서 Azure 바인딩된 트래픽에도 같은 ExpressRoute 경로를 선호하도록 해야 합니다. 일반적으로 로컬 기본 설정값은 하나의 ExpressRoute 회로를 다른 회로보다 선호하도록 온-프레미스 네트워크에 영향을 주는 데 사용합니다. 로컬 기본 설정은 iBGP(내부 BGP) 메트릭입니다. 로컬 기본 설정값이 가장 높은 BGP 경로를 사용하는 것이 좋습니다.

> [!IMPORTANT]
> 특정 ExpressRoute 회로를 대기 모드로 사용하는 경우 적극적으로 관리하고 주기적으로 장애 조치 작업을 테스트해야 합니다. 
> 

## <a name="large-distributed-enterprise-network"></a>대규모 분산 엔터프라이즈 네트워크

대규모 분산 엔터프라이즈 네트워크가 있으면 ExpressRoute 회로가 여러 개일 가능성이 큽니다. 이 섹션에서는 다른 대기 회로가 없어도 활성-활성 ExpressRoute 회로를 사용하여 재해 복구를 디자인하는 방법을 살펴보겠습니다. 

다음 다이어그램에 나와 있는 예제를 살펴보겠습니다. 이 예에서 Contoso에는 서로 다른 두 피어링 위치에서 ExpressRoute 회로를 통해 서로 다른 두 Azure 지역에 있는 두 개의 Contoso IaaS 배포에 연결된 두 개의 온-프레미스 위치가 있습니다. 

[![6]][6]

재해 복구를 설계하는 방법은 교차 지역에서 교차 위치로(region1/region2에서 location2/location1로) 트래픽이 라우팅되는 방식에 영향을 줍니다. 교차 지역-위치 트래픽을 다르게 라우팅하는 두 가지 재해 아키텍처를 고려해 보겠습니다.

### <a name="scenario-1"></a>시나리오 1

첫 번째 시나리오에서는 Azure 지역과 온-프레미스 네트워크 간의 모든 트래픽이 안정된 상태에서 로컬 ExpressRoute 회로를 통과하도록 재해 복구를 디자인해 보겠습니다. 로컬 ExpressRoute 회로가 실패하면 Azure와 온-프레미스 네트워크 간의 모든 트래픽 흐름에 원격 ExpressRoute 회로를 사용합니다.

다음 다이어그램에서는 시나리오 1을 설명합니다. 다이어그램에서 녹색 선은 VNet1과 온-프레미스 네트워크 간의 트래픽 흐름 경로를 나타냅니다. 파란색 선은 VNet2와 온-프레미스 네트워크 간의 트래픽 흐름 경로를 나타냅니다. 실선은 안정적인 상태의 올바른 경로를 나타내고 점선은 안정작인 상태의 트래픽 흐름을 전달하는 해당 ExpressRoute 회로가 실패하는 경우의 트래픽 경로를 나타냅니다. 

[![7]][7]

연결 가중치를 사용하여 VNet에서 온-프레미스 네트워크 바인딩된 트래픽에 로컬 피어링 위치 ExpressRoute에 대한 연결을 선호하도록 영향을 미치는 시나리오를 설계할 수 있습니다. 이 솔루션을 완료하려면 트래픽 흐름이 대칭 역방향이어야 합니다. ExpressRoute 회로를 선호하기 위해 온-프레미스 측에서 ExpressRoute 회로가 종료되는 BGP 라우터 사이의 iBGP 세션에서 로컬 기본 설정을 사용할 수 있습니다. 솔루션은 다음 다이어그램에 설명되어 있습니다. 

[![8]][8]

### <a name="scenario-2"></a>시나리오 2

다음 다이어그램에서는 시나리오 2를 설명합니다. 다이어그램에서 녹색 선은 VNet1과 온-프레미스 네트워크 간의 트래픽 흐름 경로를 나타냅니다. 파란색 선은 VNet2와 온-프레미스 네트워크 간의 트래픽 흐름 경로를 나타냅니다. 안정적인 상태(다이어그램의 실선)에서 Vnet과 온-프레미스 위치 간의 모든 트래픽은 대부분의 경우 Microsoft 백본을 통해 흐르고, ExpressRoute의 실패 상태(다이어그램의 점선)에 있는 온-프레미스 위치 간 상호 연결을 통해서만 흐릅니다.

[![9]][9]

솔루션은 다음 다이어그램에 설명되어 있습니다. 설명된 대로 더 구체적인 경로(옵션 1)나 AS 경로 앞에 추가(옵션 2)를 사용하여 VNet 경로 선택에 영향을 주는 시나리오를 설계할 수 있습니다. Azure 바인딩된 트래픽의 온-프레미스 네트워크 경로 선택에 영향을 미치려면 온-프레미스 위치 간 상호 연결을 덜 선호하도록 구성해야 합니다. 상호 연결 링크를 선호하도록 구성하는 방법은 온-프레미스 네트워크 내에서 사용되는 라우팅 프로토콜에 따라 다릅니다. iBGP에서 로컬 기본 설정을 사용하거나 IGP(OSPF 또는 IS-IS)에서 메트릭을 사용할 수 있습니다.

[![10]][10]


## <a name="next-steps"></a>다음 단계

이 문서에서는 ExpressRoute 회로 개인 피어링 연결의 재해 복구를 디자인하는 방법에 관해 설명합니다. 다음 문서에서는 애플리케이션과 프런트 엔드 액세스 관점에서 재해 복구를 다룹니다.

- [엔터프라이즈급 재해 복구][Enterprise DR]
- [Azure Site Recovery를 사용한 SMB 재해 복구][SMB DR]

<!--Image References-->
[1]: ./media/designing-for-disaster-recovery-with-expressroute-pvt/one-region.png "중소 규모의 온-프레미스 네트워크 고려 사항"
[2]: ./media/designing-for-disaster-recovery-with-expressroute-pvt/specificroute.png "더 구체적인 경로를 사용 하 여 경로 선택에 영향"
[3]: ./media/designing-for-disaster-recovery-with-expressroute-pvt/configure-weight.png "Azure Portal를 통해 연결 가중치 구성"
[4]: ./media/designing-for-disaster-recovery-with-expressroute-pvt/connectionweight.png "연결 가중치를 사용 하 여 경로 선택에 영향"
[5]: ./media/designing-for-disaster-recovery-with-expressroute-pvt/aspath.png에 "영향을 주는 경로 선택에 사용 경로 추가"
[6]: ./media/designing-for-disaster-recovery-with-expressroute-pvt/multi-region.png "대규모 분산 온-프레미스 네트워크 고려 사항"
[7]: ./media/designing-for-disaster-recovery-with-expressroute-pvt/multi-region-arch1.png "시나리오 1"
[8]: ./media/designing-for-disaster-recovery-with-expressroute-pvt/multi-region-sol1.png "활성-활성 ExpressRoute 회로 솔루션 1"
[9]: ./media/designing-for-disaster-recovery-with-expressroute-pvt/multi-region-arch2.png "시나리오 2"
[10]: ./media/designing-for-disaster-recovery-with-expressroute-pvt/multi-region-sol2.png "활성-활성 ExpressRoute 회로 솔루션 2"

<!--Link References-->
[HA]: ./designing-for-high-availability-with-expressroute.md
[Enterprise DR]: https://azure.microsoft.com/solutions/architecture/disaster-recovery-enterprise-scale-dr/
[SMB DR]: https://azure.microsoft.com/solutions/architecture/disaster-recovery-smb-azure-site-recovery/
[con wgt]: ./expressroute-optimize-routing.md#solution-assign-a-high-weight-to-local-connection
[AS Path Pre]: ./expressroute-optimize-routing.md#solution-use-as-path-prepending
