---
title: "Azure Governmenmt 네트워킹 | Microsoft 문서"
description: "e 정부에 대한 개인 연결을 위한 기능 및 지침 비교를 제공합니다."
services: Azure-Government
cloud: gov
documentationcenter: 
author: ryansoc
manager: zakramer
editor: 
ms.assetid: 3da70579-ecda-421a-8ebf-d52906334e9b
ms.service: multiple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: azure-government
ms.date: 09/28/2016
ms.author: ryansoc
translationtype: Human Translation
ms.sourcegitcommit: 14aa9126d3426c560c1f9497ed0d7a92448137b4
ms.openlocfilehash: 39e67d449c0fa37b76354dc522216f9473df7b97


---
# <a name="azure-government-networking"></a>Azure Government 네트워킹
## <a name="expressroute-private-connectivity"></a>ExpressRoute(개인 연결)
ExpressRoute는 일반적으로 Azure Government에서 사용할 수 있습니다. 자세한 내용(파트너 및 피어링 위치 포함)은 [ExpressRoute 공개 문서](../expressroute/index.md)를 참조하세요.

### <a name="variations"></a>변형
ExpressRoute는 Azure Government에서 일반적으로 사용(GA)할 수 있습니다. 

* 정부 고객은 전용 Azure Government(Gov) ExpressRoute(ER) 연결을 통해 물리적으로 격리된 용량에 연결합니다.
* Azure Gov는 800Km 이상 떨어져 있는 여러 지역 쌍을 활용하여 가용성 및 내구성을 향상시킵니다. 
* 기본적으로 모든 Azure Gov ER 연결은 버스트 지원을 통해 액티브-액티브 중복 방식으로 구성되며 최대 10GB 회로 용량(최소 50MB)을 제공합니다.
* Azure Gov ER 위치는 고객과 Azure Gov 지역 중복 지역에 최적화된 경로(최단 홉, 짧은 대기 시간, 높은 성능 등)를 제공합니다.
* Azure Gov ER 개인 연결은 인터넷을 활용하거나 트래버스하지 않으며, 인터넷에 종속되지 않습니다.
* Azure Gov 물리적 및 논리적 인프라는 물리적으로 전용되고 분리되며 미국인만 액세스할 수 있습니다.
* Microsoft는 Azure Gov Regions과 Azure Gov ER Meet-Me 위치간에 모든 광섬유 인프라를 소유하고 운영합니다.
* Azure Gov ER은 Microsoft Azure, O365 및 CRM 클라우드 서비스에 대한 연결성을 제공합니다.

### <a name="considerations"></a>고려 사항
Azure Government에 대한 개인 네트워크 연결을 제공하는 두 가지 기본 서비스: VPN(일반 조직에 대한 사이트 간) 및 ExpressRoute가 있습니다.

Azure ExpressRoute는 Azure Government 데이터 센터와 온-프레미스 인프라 또는 공동 장소 환경 사이에 개인 연결을 만드는 데 사용됩니다. ExpressRoute 연결은 공용 인터넷을 사용하지 않으며 일반적인 인터넷 연결보다 안정적이고 속도가 빠르며 대기 시간이 짧습니다. 경우에 따라 온-프레미스 시스템과 Azure 간 데이터 전송에 ExpressRoute 연결을 사용하여 상당한 비용 혜택을 얻습니다.   

ExpressRoute를 사용하여 ExpressRoute 위치(예: Exchange 공급자 기능)에서 Azure에 대한 연결을 설정하거나 기존 WAN 네트워크(네트워크 서비스 공급자에서 제공하는 MPLS(Multi-Protocol Label Switching) VPN)에서 Azure로 직접 연결합니다.

![대체 텍스트](./media/azure-government-capability-private-connectivity-options.PNG)  ![대체 텍스트](./media/government-capability-expressroute.PNG)  

Azure Government 고객 응용 프로그램 및 솔루션을 지원하기 위한 네트워크 서비스의 경우 Azure Government에 연결하도록 ExpressRoute(개인 연결)를 구현하는 것이 좋습니다. VPN 연결을 사용하는 경우 다음 사항을 고려해야 합니다.

* 고객은 개인 연결 또는 기타 보안 연결 메커니즘이 필요한지 여부를 확인하고 고려할 추가적인 제한 사항을 식별하기 위해 권한 부여 공식/대행 기관에 문의해야 합니다.
* 고객은 사이트 간 VPN에서 개인 연결 영역을 통해 라우팅하도록 지시할지 여부를 결정해야 합니다.
* 고객은 허가된 개인 연결 액세스 공급자를 통해 MPLS 회로 또는 VPN을 획득해야 합니다.

개인 연결 아키텍처를 활용하는 모든 고객은 Azure Government용 GN/I(게이트웨이 네트워크/인터넷) 에지 라우터 경계 지점에 대한 고객 연결을 위해 적절한 구현이 설정되고 유지 관리되는지 유효성을 검사해야 합니다. 마찬가지로 조직은 Azure Government에 대한 온-프레미스 환경 및 GN/C(게이트웨이 네트워크/고객) 에지 라우터 경계 지점 간 네트워크 연결을 설정해야 합니다.

## <a name="next-steps"></a>다음 단계
추가 정보 및 업데이트는 <a href="https://blogs.msdn.microsoft.com/azuregov/">Microsoft Azure Government 블로그</a>를 구독하세요.




<!--HONumber=Nov16_HO3-->


