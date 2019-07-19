---
title: Microsoft 글로벌 네트워크-Azure
description: Microsoft에서 빠르고 안정적인 글로벌 네트워크를 구축 하는 방법을 설명 합니다.
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
ms.openlocfilehash: c064a5afee8db0c747273ab1c38c901a6f10ef98
ms.sourcegitcommit: de47a27defce58b10ef998e8991a2294175d2098
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/15/2019
ms.locfileid: "67876141"
---
# <a name="microsoft-global-network"></a>Microsoft 글로벌 네트워크

Microsoft는 전 세계에서 가장 큰 백본 네트워크 중 하나를 소유 하 고 운영 합니다. 10만 마일 이상으로 확장 되는 이러한 글로벌 및 고급 아키텍처는 데이터 센터와 고객을 연결 합니다. 
 
매일 전 세계의 고객은 Microsoft Azure, Bing, Dynamics 365, Office 365, XBox 등에 대 한 엄청난 요청을 연결 하 고 전달 합니다. 형식에 관계 없이 고객은 서비스에서 인스턴트 안정성과 응답성을 필요로 합니다. 
 
Microsoft WAN ( [global network](https://azure.microsoft.com/global-infrastructure/global-network/) )은 훌륭한 클라우드 환경을 제공 하는 핵심적인 부분입니다. Microsoft [데이터 센터](https://azure.microsoft.com/global-infrastructure/) 를 54 Azure 지역에 연결 하 고 광범위 한에 지 노드를 광범위 하 게 연결 하 여 전 세계에 배치 된 글로벌 네트워크는 가용성, 용량 및 수요를 충족 하는 유연성을 모두 제공 합니다.

![Microsoft 글로벌 네트워크](./media/microsoft-global-network/microsoft-global-wan.png)
 
## <a name="get-the-premium-cloud-network"></a>프리미엄 클라우드 네트워크 가져오기
 
Microsoft 클라우드를 사용 하는 경우 [가장 적합 한 환경을](https://www.sdxcentral.com/articles/news/azure-tops-aws-gcp-in-cloud-performance-says-thousandeyes/2018/11/) 옵트인 하는 것이 쉽습니다. 전략적으로 배치 된 경계 노드를 통해 고객 트래픽이 글로벌 네트워크에 진입 하는 순간부터 데이터는 빛이 거의 가까운 속도로 최적화 된 경로를 통해 이동 합니다. 이렇게 하면 최상의 성능을 위해 최적 대기 시간을 보장 합니다. 3500 개 이상의 위치 145에서 수천 개의 연결을 통해 개가 넘는 고유한 인터넷 파트너 (피어)에 연결 된 이러한 가장자리 노드는 상호 연결 전략의 기반을 제공 합니다. 
 
런던에서 도쿄로 또는 워싱턴 DC에서 로스앤젤레스로 연결 하는 경우 네트워크 성능은 정량화 된 되며 대기 시간, 지터, 패킷 손실 및 처리량과 같은 항목의 영향을 받습니다.  Microsoft에서는 전송 링크와 달리 직접 상호 연결을 선호 하 고 사용 합니다. 이렇게 하면 응답 트래픽이 대칭으로 유지 되 고 홉, 피어 링 파티 및 경로를 가능한 짧고 간단 하 게 유지 하는 데 도움이 됩니다. 

예를 들어 런던의 사용자가 도쿄의 서비스에 액세스 하려고 하는 경우 인터넷 트래픽은 런던에 있는 가장자리 중 하나를 입력 하 고, 프랑스를 통해 Microsoft WAN, 유럽 및 인도의 전송 경로, 서비스를 호스팅하는 일본으로 차례로 이동 합니다. 응답 트래픽이 대칭입니다. 이를 사용 하는 것이 [감자 라우팅](https://en.wikipedia.org/wiki/Hot-potato_and_cold-potato_routing) 이라고 하는 경우가 있습니다 .이는 트래픽이 Microsoft 네트워크에 유지 되기 전에 가능한 한 오랫동안 유지 됨을 의미 합니다.  
  
Microsoft 서비스를 사용 하는 경우 모든 트래픽을 의미 하는 것은 무엇 인가요? 예, Microsoft Azure 내에서 또는 Virtual Machines, Office 365, XBox, SQL Db, 저장소 및 가상 네트워크와 같은 Microsoft 서비스 간에 전송 되는 데이터 센터 간의 트래픽은 글로벌 네트워크 내에서 라우팅됩니다. 성능 및 무결성.  
 
클라우드 및 온라인 서비스의 극단적인 증가를 주도하 하는 동시에 일관성 있고 높은 서비스 수준을 유지 하는 동시에 많은 규모의 파이버 용량과 metro, 지상파 및 해저 경로에 대 한 다양성을 유지 하는 것이 중요 합니다. 글로벌 네트워크에 대 한 최근 추가 사항은 Bilbao, 스페인 및 버지니아 해변, 버지니아, USA 간의 해저와 뉴욕, 미국, 더블린, 아일랜드 사이에 있는 [AEC](https://www.submarinecablemap.com/#/submarine-cable/aeconnect-1) 사이에 있는 [MAREA](https://www.submarinecablemap.com/#/submarine-cable/marea) 케이블, 업계의 최초의 OLS (Open Line System) 도쿄, 일본 및 포틀랜드, Oregon, 미국 간의 [새로운 태평양 (NCP)](https://www.submarinecablemap.com/#/submarine-cable/new-cross-pacific-ncp-cable-system) . 
 

## <a name="our-network-is-your-network"></a>네트워크는 네트워크입니다.

Microsoft는 네트워크에 대 한 대규모 투자와 함께 수십 년간의 경험을 제공 하 여 항상 최적의 성능을 보장 합니다. 기업은 네트워크 자산을 최대한 활용 하 고 위에 고급 오버레이 아키텍처를 구축할 수 있습니다. 
 
Microsoft Azure는 서비스 및 기능에 대 한 가장 다양 한 포트폴리오를 제공 하 여 고객이 어디에서 든 지 네트워킹 요구 사항을 빠르고 쉽게 빌드하고 확장 하 고 충족할 수 있습니다. Microsoft의 연결 서비스 제품군은 지역, 하이브리드 및 클라우드 간 지점 및 사이트 간 아키텍처와 글로벌 IP 전송 시나리오 간에 가상 네트워크 피어 링을 분산 합니다.  자체 데이터 센터 또는 네트워크를 Azure에 연결 하려는 기업 또는 대규모 데이터 수집 또는 전송 요구, [express](../expressroute/expressroute-introduction.md)경로 및 [express](../expressroute/expressroute-erdirect-about.md) 경로를 사용 하는 고객은 최대 100 g b의 대역폭을 직접 제공 합니다. 전 세계의 위치를 피어 링 하는 Microsoft의 글로벌 네트워크  
 
[Express 경로 Global Reach](../expressroute/expressroute-global-reach.md) 는 서비스 공급자의 WAN 구현을 보완 하 고 전 세계 온-프레미스 사이트에 연결 하도록 설계 되었습니다. 글로벌 작업을 실행 하는 경우 기본 및 로컬 서비스 공급자와 함께 Express 경로 Global Reach를 사용 하 여 Microsoft 글로벌 네트워크를 사용 하는 모든 글로벌 사이트를 연결할 수 있습니다. 클라우드 (WAN)에서 많은 수의 분기 사이트를 확장 하는 것은 Azure 가상 WAN을 통해 수행할 수 있습니다 .이를 통해 SDWAN & VPN 장치를 사용 하 여 Microsoft 글로벌 네트워크에 분기를 원활 하 게 연결할 수 있습니다. 기본 제공 되는 간편한 사용 및 자동화 된 연결 및 구성 관리가 포함 된 고객 프레미스 장비 또는 CPE). 
 
사용자는 [글로벌 VNet 피어 링](../virtual-network/virtual-network-peering-overview.md) 을 사용 하 여 여러 지역에서 두 개 이상의 Azure virtual network를 원활 하 게 연결할 수 있습니다. 피어 링 하면 가상 네트워크가 하나로 표시 됩니다. 피어 링 가상 네트워크의 가상 머신 간 트래픽은 Microsoft 백본 인프라를 통해 라우팅됩니다. 즉, 트래픽이 동일한 가상 네트워크에서 개인 IP 주소를 통해 가상 머신 간에 라우팅되는 것과 매우 비슷합니다. 
 

## <a name="well-managed-using-software-defined-innovation"></a>소프트웨어 정의 혁신을 사용한 잘 관리

Microsoft는 전 세계의 주요 클라우드 중 하나를 실행 하 여 고성능 글로벌 인프라를 [구축 하 고 관리](https://myignite.techcommunity.microsoft.com/sessions/66668) 하는 많은 통찰력 및 경험을 얻었습니다.  
 
Microsoft는 다음과 같은 강력한 운영 원리 집합을 준수 합니다. 
 
- 네트워크의 다양 한 계층에서 최고의 전환 하드웨어를 사용 합니다.  
- 최종 사용자에 게 영향을 주지 않는 새 기능을 배포 합니다.  
- 최대한 빠르게 업데이트를 안전 하 고 안정적으로 롤아웃 하세요. 주가 아닌 시간  
- 클라우드 규모의 심층 분석 및 완전히 자동화 된 오류 완화를 활용 합니다.  
- 통합 및 소프트웨어 정의 네트워킹 기술을 사용 하 여 네트워크의 모든 하드웨어 요소를 제어 합니다.  중복을 제거 하 고 오류를 줄입니다. 
 
이러한 원칙은 네트워크의 모든 계층에 적용 됩니다. 즉, 호스트 네트워크 인터페이스, 전환 플랫폼, 데이터 센터의 네트워크 기능 (예: 부하 분산 장치)에는 트래픽 엔지니어링 플랫폼과 광 네트워크를 사용 하는 WAN까지 모두 적용 됩니다.  
 
Azure 및 해당 네트워크에 대 한 지 수 증가는 사용자가 더 이상 글로벌 네트워크 작업을 관리 하는 데 의존해 서는 안 되는 지점에 도달 했습니다. 네트워크에서 긴, 보통 및 단기 변경의 유효성을 검사 해야 하는 필요성을 충족 하기 위해 프로덕션 네트워크 synthetically을 미러링 하 고 에뮬레이트하는 플랫폼을 개발 했습니다. 미러된 환경을 만들고 수백만 개의 시뮬레이션을 실행 하는 기능을 통해 소프트웨어 및 하드웨어 변경 내용과 해당 영향을 프로덕션 플랫폼 및 네트워크에 커밋하기 전에 테스트할 수 있습니다. 

## <a name="next-steps"></a>다음 단계
- [Azure에서 제공 하는 네트워킹 서비스에 대 한 자세한 정보](https://azure.microsoft.com/product-categories/networking/)
