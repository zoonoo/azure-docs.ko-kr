<properties 
	pageTitle="프리미엄 Azure Redis Cache에 가상 네트워크 지원을 구성하는 방법" 
	description="프리미엄 계층 Azure Redis Cache 인스턴스에 대한 가상 네트워크 지원을 만들고 관리하는 방법에 대해 알아봅니다." 
	services="redis-cache" 
	documentationCenter="" 
	authors="steved0x" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="cache" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="cache-redis" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="09/30/2015" 
	ms.author="sdanie"/>

# 프리미엄 Azure Redis Cache에 가상 네트워크 지원을 구성하는 방법
Azure Redis Cache에는 현재 미리 보기 상태인 새 프리미엄 계층을 포함하여 캐시 크기 및 기능을 유연하게 선택할 수 있는 다양한 캐시 제품이 있습니다.

Azure Redis Cache 프리미엄 계층에는 클러스터링, 지속성 및 가상 네트워크(VNET) 지원이 포함됩니다. VNET은 클라우드의 사용자 네트워크를 나타내는 표현입니다. Azure Redis Cache 인스턴스를 VNET으로 구성한 경우 클라이언트에서만 공개적으로 주소를 지정할 수 없으며, VNET 내의 클라이언트에서만 액세스할 수 있습니다. 이 문서에서는 프리미엄 Azure Redis Cache에 가상 네트워크 지원을 구성하는 방법을 설명합니다.

프리미엄 캐시 기능에 대한 자세한 내용은 [프리미엄 Azure Redis Cache에 지속성을 구성하는 방법](cache-how-to-premium-persistence.md)과 [프리미엄 Azure Redis Cache에 클러스터링을 구성하는 방법](cache-how-to-premium-clustering.md)을 참조하세요.

>[AZURE.NOTE]Azure Redis Cache 프리미엄 계층은 현재 미리 보기 상태입니다.

## VNET을 사용하는 이유
[Azure 가상 네트워크(VNET)](https://azure.microsoft.com/ko-kr/services/virtual-network/) 배포는 Azure Redis Cache의 보안과 격리를 강화하며 Azure Redis Cache에 대한 액세스를 추가적으로 제한하기 위한 서브넷, 액세스 제어 정책 및 기타 기능을 제공합니다.

## 가상 네트워크 지원
가상 네트워크(VNET) 지원은 캐시를 만드는 중에 **새 Redis 캐시** 블레이드에 구성됩니다. 캐시를 만들려면 [Azure Preview 포털](https://portal.azure.com)에 로그인하고 **새로 만들기** -> **데이터 + 저장소** > **Redis 캐시**를 클릭합니다.

![Redis Cache 만들기][redis-cache-new-cache-menu]

VNET 지원을 구성하려면 먼저 **가격 계층 선택** 블레이드에서 **프리미엄** 캐시 중 하나를 선택합니다.

![가격 계층 선택][redis-cache-premium-pricing-tier]

Azure Redis Cache VNET 통합은 **가상 네트워크** 블레이드에 구성됩니다. 여기에서 기존 클래식 VNET을 선택할 수 있습니다. 새 VNET을 사용하려면 [Azure Preview 포털을 사용하여 가상 네트워크(클래식) 만들기](../virtual-network/virtual-networks-create-vnet-classic-pportal.md)에 있는 단계를 따른 다음 **Redis 캐시 가상 네트워크** 블레이드로 돌아가 선택합니다.

>[AZURE.NOTE]프리미엄 캐시의 미리 보기 기간 동안 Azure Redis Cache가 클래식 VNET에서 작동합니다. 클래식 VNET을 만드는 방법에 대한 정보는 [Azure Preview 포털을 사용하여 가상 네트워크(클래식) 만들기](../virtual-network/virtual-networks-create-vnet-classic-pportal.md)를 참조하세요.

![가상 네트워크][redis-cache-vnet]

**가상 네트워크** 블레이드에서 **가상 네트워크**를 클릭하여 VNET을 선택 및 구성합니다.

![가상 네트워크][redis-cache-vnet-select]

원하는 VNET을 클릭하여 선택합니다.

![가상 네트워크][redis-cache-vnet-subnet]

서브넷을 클릭하여 원하는 서브넷을 선택합니다.

![가상 네트워크][redis-cache-vnet-ip]

원하는 **고정 IP 주소**를 클릭하고 **확인**을 클릭하여 VNET 구성을 저장합니다. 선택한 고정 IP가 이미 사용 중인 경우 오류 메시지가 표시됩니다.

캐시를 만든 후에는 동일한 VNET 안의 클라이언트에서만 액세스할 수 있습니다.

## Azure Redis Cache VNET FAQ

다음 목록에는 Azure Redis Cache 크기 조정에 대해 일반적으로 묻는 질문과 답변이 들어 있습니다.

## Azure Redis 캐시 및 VNET의 몇 가지 일반적인 구성 오류 문제는 무엇인가요?

다음은 Azure Redis Cache의 정상 작동에 방해가 될 수 있는 몇 가지 일반적인 구성 오류의 목록입니다.

-	클라이언트가 Redis 연결에 사용하는 포트, 즉 6379 또는 6380을 차단합니다.
-	가상 네트워크에서 나가는 HTTPS 트래픽 차단하거나 가로챕니다. Azure Redis Cache에서는 나가는 HTTPS 트래픽을 Azure 서비스, 특히 저장소에 사용합니다.
-	서브넷 내에서 Redis 역할 인스턴스 VM이 서로 통신하는 것을 차단합니다. Redis 역할 인스턴스는 사용되는 포트에서 TCP를 사용하여 서로 통신하도록 허용해야 합니다. 이 내용은 변경될 수 있으나 최소한 Redis CSDEF 파일에서 모든 포트가 사용된다고 가정할 수 있습니다.
-	Azure 부하 분산 장치가 TCP/HTTP 포트 16001에서 Redis VM에 연결하는 것을 차단합니다. Azure Redis Cache는 기본 Azure 부하 분산 장치 프로브를 사용하여 어떤 역할 인스턴스가 실행 중인지 판단합니다. 기본 부하 분산 장치 프로브는 포트 16001에서 Azure 게스트 에이전트를 Ping하여 작동합니다. 이 Ping에 응답한 역할 인스턴스만 ILB에서 전달한 트래픽을 수신하는 순서에 배치됩니다. 포트가 차단되어 Ping이 실패함에 따라 순서에 인스턴스가 없는 경우 ILB는 들어오는 TCP 연결을 수락하지 않게 됩니다.
-	SSL 공용 키 유효성 검사에 사용되는 클라이언트 응용 프로그램의 웹 트래픽을 차단합니다. Redis 클라이언트(가상 네트워크 내)가 포트 6380을 사용하여 Redis에 연결하고 SSL 서버 인증을 수행하기 위해 CA 인증서와 인증서 해지 목록을 다운로드하려면 공용 인터넷에 대한 HTTP 트래픽을 수행할 수 있어야 합니다.
-	Azure 부하 분산 장치가 포트 1300x (13000, 13001 등) 또는 1500x (15000, 15001 등)에서 TCP를 통해 클러스터 내의 Redis VM에 연결하는 것을 차단합니다. 이 포트를 열려면 부하 분산 장치 프로브를 통해 csdef 파일에서 VNET을 구성해야 합니다. Azure 부하 분산 장치는 NSG에서 허용되어야 합니다. 기본 NSG는 AZURE\_LOADBALANCER 태그를 사용하여 이를 수행합니다. Azure 부하 분산 장치에는 단일 고정 IP 주소 168.63.126.16이 있습니다. 자세한 내용은 [NSG(네트워크 보안 그룹)란?](..\virtual-network\virtual-networks-nsg.md)을 참조하세요.

## 표준 또는 기본 캐시에 VNET을 사용할 수 있나요?

VNET은 프리미엄 캐시에만 사용할 수 있습니다.

## 다음 단계

다른 프리미엄 캐시 기능의 사용 방법은 [프리미엄 Azure Redis Cache에 지속성을 구성하는 방법](cache-how-to-premium-persistence.md)과 [프리미엄 Azure Redis Cache에 클러스터링을 구성하는 방법](cache-how-to-premium-clustering.md)을 참조하세요.





  
<!-- IMAGES -->

[redis-cache-new-cache-menu]: ./media/cache-how-to-premium-vnet/redis-cache-new-cache-menu.png

[redis-cache-premium-pricing-tier]: ./media/cache-how-to-premium-vnet/redis-cache-premium-pricing-tier.png

[redis-cache-vnet]: ./media/cache-how-to-premium-vnet/redis-cache-vnet.png

[redis-cache-vnet-select]: ./media/cache-how-to-premium-vnet/redis-cache-vnet-select.png

[redis-cache-vnet-ip]: ./media/cache-how-to-premium-vnet/redis-cache-vnet-ip.png

[redis-cache-vnet-subnet]: ./media/cache-how-to-premium-vnet/redis-cache-vnet-subnet.png

<!---HONumber=Oct15_HO1-->