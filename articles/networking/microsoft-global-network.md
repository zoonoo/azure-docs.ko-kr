---
title: 마이크로소프트 글로벌 네트워크 - Azure
description: Microsoft가 빠르고 신뢰할 수 있는 글로벌 네트워크를 구축하는 방법에 대해 설명합니다.
services: networking
documentationcenter: ''
author: KumudD
manager: ''
ms.service: virtual-network
ms.devlang: ''
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/13/2019
ms.author: kumud
ms.reviewer: ypitsch
ms.openlocfilehash: 10a061163447a60f1c25b386ef28028436284650
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75453067"
---
# <a name="microsoft-global-network"></a>Microsoft 글로벌 네트워크

Microsoft는 세계에서 가장 큰 백본 네트워크 중 하나를 소유하고 운영합니다. 100,000마일 이상에 이르는 이 글로벌하고 정교한 아키텍처는 데이터 센터와 고객을 연결합니다. 
 
전 세계 고객은 매일 Microsoft Azure, Bing, Dynamics 365, Office 365, XBox 및 기타 여러 사람에게 수조 건의 요청을 연결하고 전달합니다. 유형에 관계없이 고객은 당사의 서비스에서 즉각적인 신뢰성과 응답성을 기대합니다. 
 
Microsoft 글로벌 네트워크(WAN)는 훌륭한 클라우드 환경을 제공하는 데 있어 핵심적인 부분입니다. [Microsoft global network](https://azure.microsoft.com/global-infrastructure/global-network/) 전 세계에 전략적으로 배치된 54개의 Azure 지역과 대규모 에지 노드 메시에 Microsoft [데이터 센터를](https://azure.microsoft.com/global-infrastructure/) 연결하는 글로벌 네트워크는 가용성, 용량 및 모든 수요를 충족할 수 있는 유연성을 모두 제공합니다.

![Microsoft 글로벌 네트워크](./media/microsoft-global-network/microsoft-global-wan.png)
 
## <a name="get-the-premium-cloud-network"></a>프리미엄 클라우드 네트워크 받기
 
Microsoft 클라우드를 사용할 때 [최상의 환경을](https://www.sdxcentral.com/articles/news/azure-tops-aws-gcp-in-cloud-performance-says-thousandeyes/2018/11/) 선택하는 것은 쉽습니다. 전략적으로 배치된 에지 노드를 통해 고객 트래픽이 글로벌 네트워크에 유입되는 순간부터 데이터는 빛의 속도에 가까운 최적화된 경로를 통해 이동합니다. 이렇게 하면 최상의 성능을 위해 최적의 대기 시간이 보장됩니다. 3,500개 이상의 고유한 인터넷 파트너(피어)와 145개 이상의 위치에서 수천 개의 연결을 통해 상호 연결된 이러한 에지 노드는 상호 연결 전략의 토대를 제공합니다. 
 
런던에서 도쿄로 연결하든 워싱턴 DC에서 로스앤젤레스로 연결하든 네트워크 성능은 대기 시간, 지터, 패킷 손실 및 처리량과 같은 것들에 의해 정량화되고 영향을 받게 됩니다.  Microsoft에서는 전송 링크가 아닌 직접 상호 연결을 선호하고 사용하며, 이는 응답 트래픽을 대칭으로 유지하고 홉, 피어링 파티 및 경로를 가능한 한 짧고 간단하게 유지하는 데 도움이 됩니다. 

예를 들어 런던의 사용자가 도쿄에서 서비스에 액세스하려고 하면 인터넷 트래픽이 런던의 가장자리 중 하나에 들어가서 Microsoft WAN을 통해 프랑스, 유럽과 인도 간 아라비아 횡단 경로, 서비스가 호스팅되는 일본으로 이동합니다. 응답 트래픽은 대칭입니다. 이를 [콜드포테이토 라우팅이라고도](https://en.wikipedia.org/wiki/Hot-potato_and_cold-potato_routing) 하며, 이는 트래픽이 Microsoft 네트워크에서 가능한 한 오랫동안 유지된다는 것을 의미합니다.  
  
그렇다면 Microsoft 서비스를 사용할 때 모든 트래픽을 의미합니까? 예. 데이터 센터 간, Microsoft Azure 내에서 또는 가상 머신, Office 365, XBox, SQL DB, 저장소 및 가상 네트워크와 같은 Microsoft 서비스 간의 트래픽은 글로벌 네트워크 내에서 라우팅되며 공용 인터넷을 통해 절대로 라우팅되지 않아 최적의 보안을 보장합니다. 성능과 무결성을 위해  
 
메트로, 지상파 및 잠수함 경로 전반에 걸친 광섬유 용량과 다양성에 대한 대규모 투자는 클라우드 및 온라인 서비스의 극단적인 성장을 촉진하면서 일관되고 높은 서비스 수준을 유지하는 데 매우 중요합니다. 최근 글로벌 네트워크에 추가된 [MAREA](https://www.submarinecablemap.com/#/submarine-cable/marea) 잠수함 케이블은 미국 빌바오, 스페인, 버지니아 비치, 버지니아, 미국 빌바오, 미국, 뉴욕, 아일랜드, 뉴 크로스 퍼시픽(NCP) 사이, 미국 오리건 주 포틀랜드 간 [해저](https://www.submarinecablemap.com/#/submarine-cable/aeconnect-1) 간 업계 최초의 오픈 라인 [시스템(OLS)입니다.](https://www.submarinecablemap.com/#/submarine-cable/new-cross-pacific-ncp-cable-system) 
 

## <a name="our-network-is-your-network"></a>우리의 네트워크는 당신의 네트워크입니다

우리는 항상 최적의 성능을 보장하기 위해 네트워크에 막대한 투자와 함께 수십 년의 경험을 쌓았습니다. 기업은 네트워크 자산을 최대한 활용하고 고급 오버레이 아키텍처를 구축할 수 있습니다. 
 
Microsoft Azure는 다양한 서비스 및 기능 포트폴리오를 제공하여 고객이 어디서나 네트워킹 요구 사항을 빠르고 쉽게 구축, 확장 및 충족할 수 있도록 합니다. 로키지 의 연결 서비스 제품군은 글로벌 IP 전송 시나리오뿐만 아니라 지역, 하이브리드, 클라우드 내 지점 간 및 사이트 간 아키텍처 간의 가상 네트워크 피어링에 걸쳐 있습니다.  자체 데이터 센터 또는 네트워크를 Azure에 연결하려는 기업또는 대규모 데이터 수집 또는 전송 요구 사항이 있는 고객의 경우 [ExpressRoute](../expressroute/expressroute-introduction.md)및 [ExpressRoute Direct는](../expressroute/expressroute-erdirect-about.md) 전 세계 피어링 위치에서 Microsoft의 글로벌 네트워크에 최대 100Gbps의 대역폭 옵션을 제공합니다.  
 
[ExpressRoute 글로벌 리치는](../expressroute/expressroute-global-reach.md) 서비스 제공업체의 WAN 구현을 보완하고 전 세계 온-프레미스 사이트를 연결하도록 설계되었습니다. 전역 작업을 실행하는 경우 ExpressRoute 글로벌 도달 을 기본 및 로컬 서비스 공급자와 함께 사용하여 Microsoft 글로벌 네트워크를 사용하여 모든 글로벌 사이트를 연결할 수 있습니다. 클라우드(WAN)에서 새로운 네트워크를 확장하여 많은 수의 분기 사이트를 포괄할 수 있는 Azure Virtual WAN을 통해 SDWAN & VPN 장치(즉, 고객 구내 장비 또는 CPE)를 사용하여 Microsoft 글로벌 네트워크에 지점을 원활하게 연결할 수 있으며, 이는 기본 제공 용이성 및 자동화된 연결 및 구성 관리기능을 통해 수행됩니다. 
 
[글로벌 VNet 피어링을](../virtual-network/virtual-network-peering-overview.md) 사용하면 고객이 여러 지역에 걸쳐 두 개 이상의 Azure 가상 네트워크를 원활하게 연결할 수 있습니다. 피어가 발생하면 가상 네트워크가 하나로 나타납니다. 피어있는 가상 네트워크의 가상 시스템 간의 트래픽은 Microsoft 백본 인프라를 통해 라우팅되며, 이는 개인 IP 주소만 통해동일한 가상 네트워크의 가상 시스템 간에 트래픽이 라우팅되는 것과 같습니다. 
 

## <a name="well-managed-using-software-defined-innovation"></a>소프트웨어 정의 혁신을 사용하여 잘 관리

Microsoft는 세계 최고의 클라우드 중 하나를 운영하면서 고성능 글로벌 인프라를 구축하고 관리하는 데 많은 통찰력과 경험을 쌓았습니다.  
 
당사는 다음과 같은 강력한 운영 원칙을 준수합니다. 
 
- 네트워크의 다양한 계층에서 동급 최강의 스위칭 하드웨어를 사용하십시오.  
- 최종 사용자에게 영향을 미치지 전혀 없는 새로운 기능을 배포합니다.  
- 가능한 한 빨리 플릿 전체에 걸쳐 안전하고 안정적으로 업데이트를 배포할 수 있습니다. 몇 주가 아닌 몇 시간.  
- 클라우드 규모의 심층 원격 분석 및 완전 자동화된 오류 완화를 활용합니다.  
- 통합 되고 소프트웨어정의 네트워킹 기술을 사용하여 네트워크의 모든 하드웨어 요소를 제어합니다.  중복을 제거하고 오류를 줄입니다. 
 
이러한 원칙은 호스트 네트워크 인터페이스, 스위칭 플랫폼, 로드 밸러서와 같은 데이터 센터의 네트워크 기능, 트래픽 엔지니어링 플랫폼 및 광학 네트워크를 통해 WAN에 이르기까지 네트워크의 모든 계층에 적용됩니다.  
 
Azure와 그 네트워크의 기하급수적인 성장은 결국 인간의 직관이 더 이상 글로벌 네트워크 운영을 관리하는 데 의존할 수 없다는 것을 깨달았습니다. 네트워크의 장기, 중기 및 단기 적인 변화를 검증할 필요성을 충족하기 위해 생산 네트워크를 종합적으로 미러하고 에뮬레이트하는 플랫폼을 개발했습니다. 미러된 환경을 만들고 수백만 개의 시뮬레이션을 실행할 수 있는 기능을 통해 프로덕션 플랫폼 및 네트워크에 커밋하기 전에 소프트웨어 및 하드웨어 변경 사항과 그 영향을 테스트할 수 있습니다. 

## <a name="next-steps"></a>다음 단계
- [Azure에서 제공되는 네트워킹 서비스에 대해 자세히 알아보기](https://azure.microsoft.com/product-categories/networking/)
