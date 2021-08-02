---
title: Microsoft 365 서비스에 대해 ExpressRoute 사용 | Microsoft Docs
description: 이 문서에서는 Microsoft 365 SaaS 서비스에 대해 ExpressRoute 회로를 사용하는 방법에 대해 객관적으로 설명합니다.
documentationcenter: na
services: expressroute
author: rambk
manager: tracsman
ms.service: expressroute
ms.topic: article
ms.workload: infrastructure-services
ms.date: 4/29/2021
ms.author: rambala
ms.openlocfilehash: 6e6b543741a27b7a99ef447e7e34fbec803403db
ms.sourcegitcommit: c072eefdba1fc1f582005cdd549218863d1e149e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/10/2021
ms.locfileid: "111970503"
---
# <a name="using-expressroute-for-routing-microsoft-365-traffic"></a>Microsoft 365 트래픽 라우팅을 위해 ExpressRoute 사용

ExpressRoute 회로는 Microsoft 백본 네트워크에 프라이빗 연결을 제공합니다. 
* Azure 지역에서 IaaS 배포의 프라이빗 엔드포인트에 연결하는 *개인 피어링* 을 제공합니다. 
* *Microsoft 피어링* 을 통해 Microsoft 네트워크에서 IaaS, PaaS 및 SaaS 서비스의 퍼블릭 엔드포인트에 연결할 수 있습니다. 

ExpressRoute에 대한 자세한 내용은 [ExpressRoute 소개][ExR-Intro] 문서를 참조하세요.


Microsoft 365 SaaS 트래픽 라우팅에 대해 ExpressRoute를 사용할 수 있는지 여부를 혼동하는 경우가 종종 있습니다. 

* 단일 측 인수: ExpressRoute는 Microsoft 피어링을 제공하며, 이를 통해 Microsoft 네트워크의 퍼블릭 엔드포인트 대부분에 도달할 수 있습니다. 실제로 *경로 필터* 를 사용하여 온-프레미스 네트워크에 Microsoft 피어링을 통해 보급해야 하는 Microsoft 365 서비스 접두사를 선택할 수 있습니다. 이러한 경로 보급 알림은 ExpressRoute 회로를 통해 Microsoft 365 서비스 트래픽 라우팅을 사용하도록 설정합니다. 
* 카운터 인수: Microsoft 365는 분산 서비스입니다. 이는 전 세계의 고객이 인터넷을 사용하여 서비스에 연결할 수 있도록 설계되었습니다. 따라서 Microsoft 365에 대해 ExpressRoute를 사용하지 않는 것이 좋습니다.

이 문서의 목표는 다음과 같습니다. 
* 인수에 대한 기술적인 근거 제공 및 
* Microsoft 365 트래픽을 라우팅하는 데 ExpressRoute를 사용하는 시기 및 사용하지 않을 시기를 객관적으로 논의

## <a name="network-requirements-of-microsoft-365-traffic"></a>Microsoft 365 트래픽의 네트워크 요구 사항
Microsoft 365 서비스에는 음성 및 비디오 통화, 온라인 모임, 실시간 협업 등 실시간 트래픽이 포함됩니다. 이러한 실시간 트래픽에는 대기 시간 및 지터 측면에서 엄격한 네트워크 성능 요구 사항이 있습니다. 네트워크 대기 시간에 대한 특정 한도 내에서 지터는 클라이언트 디바이스에서 버퍼를 사용하여 효과적으로 처리할 수 있습니다. 네트워크 대기 시간은 이동해야 하는 실제 거리 트래픽, 링크 대역폭 및 네트워크 처리 대기 시간의 기능입니다. 

## <a name="network-optimization-features-of-microsoft-365"></a>Microsoft 365의 네트워크 최적화 기능 

Microsoft는 아키텍처 및 기능 측면에서 모든 클라우드 애플리케이션의 네트워크 성능을 최적화하는 데 노력합니다. 시작하기 위해 Microsoft는 최상의 네트워크 성능을 제공하는 핵심 목표를 달성하기 위해 최적화된 최대 글로벌 네트워크 중 하나를 소유합니다. Microsoft 네트워크는 소프트웨어에 정의되어 있으며 "콜드 감자" 네트워크입니다. "콜드 감자" 네트워크는 클라이언트-디바이스/고객-네트워크에 최대한 가깝게 트래픽을 확보 및 송신합니다. 뿐만 아니라 Microsoft 네트워크는 매우 중복되며 항상 사용 가능합니다. 아키텍처 최적화에 대한 자세한 내용은 [Microsoft에서 빠르고 신뢰할 수 있는 글로벌 네트워크를 구축하는 방법][MGN]을 참조하세요.

엄격한 네트워크 대기 시간 요구 사항을 해결하기 위해 Microsoft 365는 경로 길이를 다음으로 단축합니다.
* 최종 사용자 연결을 가장 가까운 Microsoft 365 진입점으로 동적으로 라우팅 및 
* 진입점에서 Microsoft 글로벌 네트워크 내에서 가장 가까운(그리고 승인된) Microsoft 365 데이터 센터에 효율적으로 라우팅

Microsoft 365 진입점은 AFD(Azure Front Door)에서 서비스됩니다. AFD는 Microsoft 글로벌 에지 네트워크에 있는 광범위하게 분산된 서비스이며, 빠르고, 안전하고, 확장성이 뛰어난 SaaS 애플리케이션을 만드는 데 도움이 됩니다. AFD에서 웹 애플리케이션 성능을 가속화하는 방법을 자세히 알아보려면 [Azure Front Door란?][AFD]을 참조하세요. 가장 가까운 Microsoft 365 데이터 센터를 선택하는 동안 Microsoft는 지역 정치적 지역 내에서 데이터 주권 규정을 고려하고 있습니다.

## <a name="what-is-geo-pinning-connections"></a>지역 고정 연결이란?

클라이언트-서버에서 지리적 위치에 있는 특정 네트워크 디바이스를 통해 트래픽을 강제로 이동하는 경우 네트워크 연결을 지역 고정이라고 합니다. 클라이언트-서버가 정적으로 위치한다는 기본 디자인 원칙이 있는 기존 네트워크 아키텍처는 일반적으로 연결을 지역 고정합니다.
예를 들어 기업 인터넷 연결이 회사 네트워크를 통과하고, 중앙 위치에서 수신하는 경우(일반적으로 프록시 서버 또는 방화벽 집합을 통해) 인터넷 연결을 지역 고정하는 것입니다.  

마찬가지로 SaaS 애플리케이션 아키텍처에서는 트래픽이 지역의 중간 데이터 센터(예: 클라우드 보안) 또는 특정 위치에 있는 하나 이상의 중간 네트워크 디바이스(예: ExpressRoute)를 통해 강제로 라우팅되는 경우 SaaS 연결을 지역 고정하는 것입니다.

## <a name="when-not-to-use-expressroute-for-microsoft-365"></a>Microsoft 365에 대해 ExpressRoute를 사용하지 않는 경우는 언제인가요?

동적으로 경로 길이를 단축하고 클라이언트의 위치에 따라 가장 가까운 서버 데이터 센터를 선택하는 기능이 있기 때문에 Microsoft 365는 인터넷을 위해 설계된 것이라고 합니다. 뿐만 아니라 특정 Microsoft 365 트래픽은 인터넷을 통해서만 라우팅됩니다.
SaaS 클라이언트를 지역 전체에 걸쳐 또는 전역적으로 광범위하게 배포하고, 특정 위치에 대한 연결을 지역 고정하는 경우 클라이언트는 지역 고정 위치에서 더 멀리 떨어져 더 높은 네트워크 대기 시간을 경험하게 됩니다. 네트워크 대기 시간이 길수록 네트워크 성능이 저하되고 애플리케이션 성능이 저하됩니다.

따라서 광범위하게 분산된 SaaS 클라이언트나 이동성이 뛰어난 클라이언트가 있는 시나리오에서는 트래픽이 특정 피어링 위치에서 ExpressRoute 회로를 통과하도록 하는 등 어떤 방법으로도 연결을 지역 고정하지 않는 것이 좋습니다.


## <a name="when-to-use-expressroute-for-microsoft-365"></a>Microsoft 365에 대해 ExpressRoute를 사용하는 경우는 언제인가요?

다음은 Microsoft 365 트래픽 라우팅에 대해 ExpressRoute를 사용할 수 있는 몇 가지 이유입니다.
* SaaS 클라이언트는 지리적 위치에 집중하며, Microsoft 글로벌 네트워크에 연결하는 가장 최적의 방법은 ExpressRoute 회로를 통한 것입니다.
* SaaS 클라이언트는 여러 글로벌 위치에서 집중하며 각 위치에는 Microsoft 글로벌 네트워크에 대한 최적의 연결을 제공하는 고유한 ExpressRoute 회로가 있습니다.
* 법률은 프라이빗 연결을 통해 클라우드 바인딩 트래픽을 라우팅하는 데 필요합니다.
* 모든 SaaS 트래픽을 지역 고정 중앙 위치(개인 또는 공용 데이터 센터)로 라우팅하고, 중앙 위치를 Microsoft 글로벌 네트워크에 연결하는 최적의 방법은 ExpressRoute를 통해 수행해야 합니다.
* 일부 정적 SaaS 클라이언트의 경우 ExpressRoute만 최적의 연결을 제공하는 반면 다른 클라이언트의 경우에는 인터넷을 사용합니다.

ExpressRoute를 사용하는 동안 ExpressRoute의 Microsoft 피어링과 연결된 경로 필터를 적용하여 ExpressRoute 회로를 통해 Microsoft 365 서비스 및/또는 Azure PaaS 서비스의 하위 집합만 라우팅할 수 있습니다. 자세한 내용은 [자습서: Microsoft 피어링에 대한 경로 필터 구성][ExRRF]을 참조하세요.

## <a name="next-steps"></a>다음 단계

* 최상의 결과를 위해 Express Route를 사용하는 경우를 비롯한 다양한 시나리오에서 Microsoft 팀이 흐름을 호출하는 방법과 네트워크 연결을 최적화하는 방법을 이해하려면 [Microsoft 팀 호출 흐름][Teams]을 참조하세요.
* 개별 사무실 위치에 대한 Microsoft 365 연결 문제를 이해하기 위해 테스트하려면 [Microsoft 365 네트워크 연결 테스트][Microsoft 365-Test]를 참조하세요.
* Microsoft 365 성능의 새로운 문제를 검색하는 데 도움이 되는 기준 및 성능 기록을 설정하려면 [기준 및 성능 기록을 사용한 Office 365 성능 조정][Microsoft 365perf]을 참조하세요.

<!--Link References-->
[ExR-Intro]: ./expressroute-introduction.md
[CreatePeering]: ./expressroute-howto-routing-portal-resource-manager.md
[MGN]: https://azure.microsoft.com/blog/how-microsoft-builds-its-fast-and-reliable-global-network/
[AFD]: ../frontdoor/front-door-overview.md
[ExRRF]: ./how-to-routefilter-portal.md
[Teams]: /microsoftteams/microsoft-teams-online-call-flows
[Microsoft 365-Test]: https://connectivity.office.com/
[Microsoft 365perf]: /microsoft-365/enterprise/performance-tuning-using-baselines-and-history