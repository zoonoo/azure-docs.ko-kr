---
title: Microsoft 글로벌 네트워크 - Azure
description: Microsoft에서 전 세계의 가장 큰 백본 네트워크 중 하나를 빌드하고 운영 하는 방법과 우수한 클라우드 환경을 제공 하는 것이 중요 한 이유를 알아보세요.
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
ms.openlocfilehash: 9420d52cf7d88ff84fd23928f92cdc62e9627619
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87280905"
---
# <a name="microsoft-global-network"></a>Microsoft 글로벌 네트워크

Microsoft는 전 세계에서 가장 큰 백본 네트워크 중 하나를 소유하고 운영 중입니다. 160,000마일 넘게 뻗어 있는 이 촘촘한 글로벌 아키텍처는 데이터 센터와 고객을 연결합니다. 
 
매일 전 세계의 고객이 Microsoft Azure, Bing, Dynamics 365, Office 365, XBox 등에 연결하여 수조 건의 요청을 전달합니다. 종류가 무엇이든, 고객은 당사의 서비스에서 즉각적인 안정성과 응답성을 기대합니다. 
 
[Microsoft 글로벌 네트워크](https://azure.microsoft.com/global-infrastructure/global-network/)(WAN)는 우수한 클라우드 환경을 제공하는 데 있어서 핵심적인 역할을 합니다. 54개 Azure 지역의 Microsoft [데이터 센터](https://azure.microsoft.com/global-infrastructure/)와 전 세계에 전략적으로 배치된 그물망 같은 에지 노드를 연결하는 당사의 글로벌 네트워크는 모든 수요를 충족하는 가용성, 용량 및 유연성을 모두 제공합니다.

![Microsoft 글로벌 네트워크](./media/microsoft-global-network/microsoft-global-wan.png)
 
## <a name="get-the-premium-cloud-network"></a>프리미엄 클라우드 네트워크 이용
 
Microsoft 클라우드를 사용하면 [최상의 환경](https://www.sdxcentral.com/articles/news/azure-tops-aws-gcp-in-cloud-performance-says-thousandeyes/2018/11/)을 쉽게 사용할 수 있습니다. 전략적으로 배치된 에지 노드를 통해 고객 트래픽이 당사의 글로벌 네트워크에 진입하는 순간, 데이터가 최적화된 경로를 따라 광속에 가까운 속도로 이동합니다. 따라서 최상의 성능을 내기 위한 최적 대기 시간이 보장됩니다. 165개 이상의 위치에서 수천 개의 연결을 통해 3500개가 넘는 고유한 인터넷 파트너(피어)와 상호 연결된 이러한 에지 노드는 상호 연결 전략의 기반을 제공합니다. 
 
런던에서 도쿄로 연결하든 아니면 워싱턴 DC에서 로스앤젤레스로 연결하든, 네트워크 성능은 대기 시간, 지터, 패킷 손실, 처리량 등의 지표를 사용하여 계량화되며 그에 따른 영향을 받습니다.  Microsoft에서는 전송 링크와 달리 직접 상호 연결을 선호하고 사용합니다. 이렇게 하면 응답 트래픽이 대칭으로 유지되며 홉, 피어링 파티 및 경로를 가능한 짧고 간단하게 유지할 수 있습니다. 

예를 들어 런던의 사용자가 도쿄의 서비스에 액세스하려고 시도할 때 인터넷 트래픽이 런던에 있는 당사의 에지 중 하나에 진입하여 Microsoft WAN을 통해 프랑스, 유럽과 인도 사이의 아라비아 전역 경로를 거친 다음, 서비스가 호스팅되는 일본으로 이동합니다. 응답 트래픽이 대칭입니다. 이것을 [콜드 포테이토 라우팅](https://en.wikipedia.org/wiki/Hot-potato_and_cold-potato_routing)이라고도 하는데, Microsoft에서 트래픽을 전달할 때까지 트래픽이 Microsoft 네트워크에서 최대한 오래 머문다는 의미입니다.  
  
Microsoft 서비스를 사용할 때 모든 트래픽이 그렇다는 의미일까요? 예, Microsoft Azure 내에서 또는 Virtual Machines, Microsoft 365, XBox, SQL Db, 저장소 및 가상 네트워크와 같은 Microsoft 서비스 간에 전송 되는 모든 트래픽은 최상의 성능 및 무결성을 보장 하기 위해 공용 네트워크 내에서 라우팅됩니다.  
 
클라우드 및 온라인 서비스의 빠른 성장세를 유지하면서도 일관적이고 우수한 서비스 수준을 유지하려면 대도시, 지상파 및 해저 경로의 파이버 용량과 다양성에 막대한 투자를 해야 합니다. 최근 당사의 글로벌 네트워크에는 해저를 통해 스페인 빌바오와 미국 버지니아주 버지니아 비치를 연결하는 업계 최초의 OLS(Open Line System)인 [MAREA](https://www.submarinecablemap.com/#/submarine-cable/marea), 미국 뉴욕과 아일랜드 더블린을 연결하는 [AEC](https://www.submarinecablemap.com/#/submarine-cable/aeconnect-1), 일본 도쿄와 미국 오리건주 포틀랜드를 연결하는 [NCP(New Cross Pacific)](https://www.submarinecablemap.com/#/submarine-cable/new-cross-pacific-ncp-cable-system)가 추가되었습니다. 
 

## <a name="our-network-is-your-network"></a>당사의 네트워크가 귀사의 네트워크

당사는 언제나 최적의 성능을 제공하기 위해 네트워크에 대한 대규모 투자와 함께 20년의 경험을 쏟아부었습니다. 기업에서는 당사의 네트워크 자산을 최대한 활용하여 고급 오버레이 아키텍처를 빌드할 수 있습니다. 
 
Microsoft Azure는 가장 다양한 서비스 및 기능 포트폴리오를 제공하므로, 고객이 어디서나 쉽고 빠르게 네트워킹을 빌드하고, 확장하고, 네트워킹 요구 사항을 충족할 수 있습니다. 당사는 지역간 네트워크 피어링, 하이브리드, 클라우드 내 지점 및 사이트 간/사이트 간 아키텍처와 글로벌 IP 전송 시나리오를 해결하는 연결 서비스 제품군을 제공합니다.  자체 데이터 센터 또는 네트워크를 Azure에 연결하려는 기업 또는 데이터를 대규모로 수집하거나 전송해야 하는 고객을 위한 [ExpressRoute](../expressroute/expressroute-introduction.md) 및 [ExpressRoute Direct](../expressroute/expressroute-erdirect-about.md)는 전 세계의 피어링 위치에서 Microsoft의 글로벌 네트워크에 직접 최대 100Gbps 대역폭 옵션을 제공합니다.  
 
[ExpressRoute Global Reach](../expressroute/expressroute-global-reach.md)는 서비스 공급자의 WAN 구현을 보완하고 전 세계의 온-프레미스 사이트를 연결하도록 설계되었습니다. 전 세계 곳곳에서 사업장을 운영하는 경우 선호하는 로컬 서비스 공급자와 함께 ExpressRoute Global Reach를 사용하면 Microsoft 글로벌 네트워크를 통해 모든 글로벌 사이트를 연결할 수 있습니다. 클라우드(WAN)에서 여러 분기 사이트를 확장하는 작업은 Azure Virtual WAN을 통해 수행할 수 있으며, Azure Virtual WAN은 간편하고 자동화된 연결 및 구성 관리 기능이 내장된 SDWAN 및 VPN 디바이스(즉, 고객 프레미스 장비 또는 CPE)를 사용하여 분기를 Microsoft 글로벌 네트워크에 원활하게 연결하는 기능을 제공합니다. 
 
고객은 [글로벌 VNet 피어링](../virtual-network/virtual-network-peering-overview.md)을 사용하여 여러 지역에 위치한 여러 Azure 가상 네트워크를 원활하게 연결할 수 있습니다. 가상 네트워크는 피어링되면 하나로 표시됩니다. 트래픽이 개인 IP 주소를 통해 동일한 가상 네트워크에 있는 가상 네트워크 간에서만 라우팅되는 것과 마찬가지로 피어링된 가상 네트워크에 있는 가상 머신 간의 트래픽은 Microsoft 백본 인프라를 통해 라우팅됩니다. 
 

## <a name="well-managed-using-software-defined-innovation"></a>소프트웨어 정의 혁신을 사용하여 적절하게 관리

전 세계의 주요 클라우드 중 하나를 실행하는 Microsoft는 고성능 글로벌 인프라의 구축 및 관리에 대한 많은 인사이트와 경험을 얻었습니다.  
 
Microsoft는 다음과 같은 강력한 운영 원칙을 준수합니다. 
 
- 네트워크의 다양한 계층에서 최상의 전환 하드웨어를 사용합니다.  
- 최종 사용자에게 영향을 주지 않고 새 기능을 배포합니다.  
- 최대한 빠르게 업데이트를 안전하고 안정적으로 롤아웃합니다. 주 단위가 아닌 시간 단위로 줄입니다.  
- 클라우드 규모의 심층 분석 및 완전 자동 오류 완화를 활용합니다.  
- 통합된 소프트웨어 정의 네트워킹 기술을 사용하여 네트워크의 모든 하드웨어 요소를 제어합니다.  중복을 제거하고 오류를 줄입니다. 
 
호스트 네트워크 인터페이스부터 전환 플랫폼, 데이터 센터의 네트워크 기능(예: Load Balancer), 트래픽 엔지니어링 플랫폼과 광 네트워크를 사용하는 WAN까지 네트워크의 모든 계층에 이러한 원칙이 적용됩니다.  
 
Azure와 해당 네트워크가 폭발적으로 성장하면서 사람의 직관으로는 더 이상 글로벌 네트워크 운영을 관리할 수 없는 시점에 도달했습니다. 네트워크에서 장기, 중기 및 단기 변경 사항의 유효성을 검사하기 위해 당사는 프로덕션 네트워크를 종합적으로 미러링하고 에뮬레이트하는 플랫폼을 개발했습니다. 미러링 환경을 만들어서 수백만 개의 시뮬레이션을 실행하는 기능을 사용하면 소프트웨어 및 하드웨어의 변경 내용과 그 영향을 프로덕션 플랫폼 및 네트워크에 커밋하기 전에 미리 테스트할 수 있습니다. 

## <a name="next-steps"></a>다음 단계
- [Azure에서 제공하는 네트워킹 서비스에 대한 자세한 정보](https://azure.microsoft.com/product-categories/networking/)
