---
title: "Azure Governmenmt 네트워킹 | Microsoft 문서"
description: "e 정부에 대한 개인 연결을 위한 기능 및 지침 비교를 제공합니다."
services: azure-government
cloud: gov
documentationcenter: 
author: jawalte
manager: zakramer
ms.assetid: 3da70579-ecda-421a-8ebf-d52906334e9b
ms.service: azure-government
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: azure-government
ms.date: 01/24/2017
ms.author: jawalte
translationtype: Human Translation
ms.sourcegitcommit: 8288ca2e85ec0cd1a1b53862b2d60fcb88e7be4b
ms.openlocfilehash: c6b8c27299c1bfceb045ee7d82981654f3a446d8


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

## <a name="support-for-bgp-communities"></a>BGP 커뮤니티에 대한 지원
이 섹션에서는 BGP 커뮤니티를 AzureGov에서 ExpressRoute와 함께 사용하는 방법에 대한 개요를 제공합니다. Microsoft는 적절한 커뮤니티 값으로 태그된 경로를 가진 공용 및 Microsoft 피어링 경로에 경로를 광고합니다. 이렇게 하는 이유 및 커뮤니티 값에 대한 세부 정보는 다음과 같습니다. 그러나 Microsoft는 Microsoft에 보급하는 경로에 태그된 커뮤니티 값을 인식하지 못합니다.

AzureGov 지역 내의 한 피어링 위치에서 ExpressRoute를 통해 Microsoft에 연결하는 경우 정부 경계 내의 모든 지역에서 모든 Microsoft 클라우드 서비스에 액세스할 수 있습니다. 

예를 들어 ExpressRoute를 통해 워싱턴 D.C의 Microsoft에 연결하는 경우 AzureGov에 호스팅되는 모든 Microsoft 클라우드 서비스에 액세스할 수 있습니다.

위치 및 파트너에 대한 자세한 내용 및 AzureGov 피어링 위치의 자세한 목록은 [ExpressRoute 공개 문서](../expressroute/index.md)의 “개요" 탭을 참조하세요.

ExpressRoute 회로를 두 개 이상 구입할 수 있습니다. 여러 연결이 있으면 지리적 중복으로 인해 고가용성에 상당한 이점을 제공합니다. Express 경로 회로가 여러 개 있는 경우 공용 피어링 및 Microsoft 경로 피어링의 Microsoft에서 보급된 동일한 접두사 집합을 받게 됩니다. 즉, 네트워크에서 Microsoft까지 여러 경로가 있습니다. 잠재적으로 네트워크 내에서 최적이 아닌 라우팅이 결정될 수 있습니다. 결과적으로 다른 서비스에 최적이 아닌 연결 환경이 발생할 수도 있습니다. 

Microsoft는 공용 피어링 및 Microsoft 피어링을 통해 접두사가 호스팅된 지역을 나타내는 적절한 BGP 커뮤니티 값으로 보급된 접두사를 태그합니다. 적절한 라우팅을 결정하는 커뮤니티 값에 의존하여 고객에게 최적의 라우팅을 제공할 수 있습니다.  자세한 내용은 [ExpressRoute 공개 문서](../expressroute/index.md)의 "시작" 탭을 참조하고 "라우팅 최적화"를 클릭하세요.

| **국가별 클라우드 Azure 지역**| **BGP 커뮤니티 값** |
| --- | --- |
| **미국 정부** |  |
| 미국 정부 아이오와 | 12076분&51109;초 |
| 미국 정부 버지니아 | 12076분&51105;초 |

Microsoft에서 보급하는 모든 경로는 적절한 커뮤니티 값으로 태그되어야 합니다. 

또한 위의 내용 외에도 Microsoft는 자신이 속한 서비스에 기반한 접두사에 태그합니다. Microsoft 피어링에 적용됩니다. 다음 테이블에서는 BGP 커뮤니티 값에 서비스의 매핑을 제공합니다.

| **국가별 클라우드의 서비스** | **BGP 커뮤니티 값** |
| --- | --- |
| **미국 정부** |  |
| Exchange Online |12076분&5110;초 |
| SharePoint Online |12076분&5120;초 |
| 비즈니스 온라인용 Skype |12076분&5130;초 |
| CRM Online |12076분&5140;초 |
| 기타 Office 365 온라인 서비스 |12076분&5200;초 |

> [!NOTE]
> Microsoft는 Microsoft에 보급하는 경로에 설정한 BGP 커뮤니티 값을 인식하지 않습니다.

## <a name="support-for-load-balancer"></a>부하 분산 장치에 대한 지원
부하 분산 장치는 일반적으로 Azure Government에서 제공합니다. 자세한 내용은 [부하 분산 장치 공개 문서](../load-balancer/load-balancer-overview.md)를 참조하세요. 

## <a name="support-for-traffic-manger"></a>Traffic Manger에 대한 지원
Traffic Manager는 일반적으로 Azure Government에서 제공합니다. 자세한 내용은 [Traffic Manager 공개 문서](../traffic-manager/traffic-manager-overview.md)를 참조하세요. 

## <a name="support-for-vnet-peering"></a>VNet 피어링에 대한 지원 
VNet 피어링은 일반적으로 Azure Government에서 제공합니다. 자세한 내용은 [VNet 피어링 공개 문서](../virtual-network/virtual-network-peering-overview.md)를 참조하세요. 

## <a name="support-for-vpn-gateway"></a>VPN Gateway에 대한 지원 
VPN Gateway는 일반적으로 Azure Government에서 제공합니다. 자세한 내용은 [VPN Gateway 공개 문서](../vpn-gateway/vpn-gateway-about-vpngateways.md)를 참조하세요. 

## <a name="next-steps"></a>다음 단계
추가 정보 및 업데이트는 <a href="https://blogs.msdn.microsoft.com/azuregov/">Microsoft Azure Government 블로그</a>를 구독하세요.




<!--HONumber=Jan17_HO5-->


