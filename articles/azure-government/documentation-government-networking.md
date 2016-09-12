<properties
	pageTitle="Azure Government 설명서 | Microsoft Azure"
	description="e 정부에 대한 개인 연결을 위한 기능 및 지침 비교를 제공합니다."
	services="Azure-Government"
	cloud="gov" 
	documentationCenter=""
	authors="ryansoc"
	manager="zakramer"
	editor=""/>

<tags
	ms.service="multiple"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="azure-government"
	ms.date="08/25/2016"
	ms.author="ryansoc"/>

#  Azure Government 네트워킹

##  ExpressRoute(개인 연결)

Azure Government에 대한 개인 네트워크 연결을 제공하는 두 가지 기본 서비스: VPN(일반 조직에 대한 사이트 간) 및 ExpressRoute가 있습니다.

Azure ExpressRoute는 Azure Government 데이터 센터와 온-프레미스 인프라 또는 공동 장소 환경 사이에 개인 연결을 만드는 데 사용됩니다. ExpressRoute 연결은 공용 인터넷을 사용하지 않으며 일반적인 인터넷 연결보다 안정적이고 속도가 빠르며 대기 시간이 짧습니다. 경우에 따라 온-프레미스 시스템과 Azure 간 데이터 전송에 ExpressRoute 연결을 사용하여 상당한 비용 혜택을 얻습니다.

ExpressRoute를 사용하여 ExpressRoute 위치(예: Exchange 공급자 기능)에서 Azure에 대한 연결을 설정하거나 기존 WAN 네트워크(네트워크 서비스 공급자에서 제공하는 MPLS(Multi-Protocol Label Switching) VPN)에서 Azure로 직접 연결합니다.

![대체 텍스트](./media/azure-government-capability-private-connectivity-options.PNG) ![대체 텍스트](./media/government-capability-expressroute.PNG)

Azure Government 고객 응용 프로그램 및 솔루션을 지원하기 위한 네트워크 서비스의 경우 Azure Government에 연결하도록 ExpressRoute(개인 연결)를 구현하는 것이 좋습니다. VPN 연결을 사용하는 경우 다음 사항을 고려해야 합니다.

• 고객은 개인 연결 또는 기타 보안 연결 메커니즘이 필요한지 여부를 확인하고 고려할 추가적인 제한을 식별하기 위해 권한 부여 공식/에이전시에 문의해야 합니다.

• 고객은 사이트 간 VPN이 개인 연결 영역을 통해 라우팅되는 것을 규정할지 여부를 결정해야 합니다.

• 고객은 허가된 개인 연결 액세스 공급자로 MPLS 회로 또는 VPN을 가져와야 합니다.

개인 연결 아키텍처를 활용하는 모든 고객은 Azure Government용 GN/I(게이트웨이 네트워크/인터넷) 에지 라우터 경계 지점에 대한 고객 연결을 위해 적절한 구현이 설정되고 유지 관리되는지 유효성을 검사해야 합니다. 마찬가지로 조직은 Azure Government에 대한 온-프레미스 환경 및 GN/C(게이트웨이 네트워크/고객) 에지 라우터 경계 지점 간 네트워크 연결을 설정해야 합니다.

ExpressRoute는 일반적으로 Azure Government에서 사용할 수 있습니다. 자세한 내용(파트너 및 피어링 위치 포함)은 <a href="https://azure.microsoft.com/ko-KR/documentation/services/expressroute/">ExpressRoute 공용 설명서</a>를 참조하세요.

추가 정보 및 업데이트는 <a href="https://blogs.msdn.microsoft.com/azuregov/">Microsoft Azure Government 블로그</a>를 구독하세요.

<!---HONumber=AcomDC_0831_2016-->