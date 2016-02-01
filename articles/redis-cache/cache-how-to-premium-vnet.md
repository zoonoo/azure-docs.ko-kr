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
	ms.date="01/19/2016" 
	ms.author="sdanie"/>

# 프리미엄 Azure Redis Cache에 가상 네트워크 지원을 구성하는 방법
Azure Redis Cache에는 새 프리미엄 계층을 포함하여 캐시 크기 및 기능을 유연하게 선택할 수 있는 다양한 캐시 제품이 있습니다.

Azure Redis Cache 프리미엄 계층에는 클러스터링, 지속성 및 가상 네트워크(VNET) 지원이 포함됩니다. VNET은 클라우드의 사용자 네트워크를 나타내는 표현입니다. Azure Redis Cache 인스턴스를 VNET으로 구성한 경우 클라이언트에서만 공개적으로 주소를 지정할 수 없으며, VNET 내의 클라이언트에서만 액세스할 수 있습니다. 이 문서에서는 프리미엄 Azure Redis Cache에 가상 네트워크 지원을 구성하는 방법을 설명합니다.

프리미엄 캐시 기능에 대한 자세한 내용은 [프리미엄 Azure Redis Cache에 지속성을 구성하는 방법](cache-how-to-premium-persistence.md)과 [프리미엄 Azure Redis Cache에 클러스터링을 구성하는 방법](cache-how-to-premium-clustering.md)을 참조하세요.

## VNET을 사용하는 이유
[Azure 가상 네트워크(VNET)](https://azure.microsoft.com/services/virtual-network/) 배포는 Azure Redis Cache의 보안과 격리를 강화하며 Azure Redis Cache에 대한 액세스를 추가적으로 제한하기 위한 서브넷, 액세스 제어 정책 및 기타 기능을 제공합니다.

## 가상 네트워크 지원
가상 네트워크(VNET) 지원은 캐시를 만드는 중에 **새 Redis 캐시** 블레이드에 구성됩니다. 캐시를 만들려면 [Azure 포털](https://portal.azure.com)에 로그인하고 **새로 만들기**->**데이터 + 저장소**>**Redis Cache**를 클릭합니다.

![Redis Cache 만들기][redis-cache-new-cache-menu]

VNET 지원을 구성하려면 먼저 **가격 책정 계층 선택** 블레이드에서 **프리미엄** 캐시 중 하나를 선택합니다.

![가격 계층 선택][redis-cache-premium-pricing-tier]

Azure Redis Cache VNET 통합은 **가상 네트워크** 블레이드에 구성됩니다. 여기에서 기존 클래식 VNET을 선택할 수 있습니다. 새 VNET을 사용하려면 [Azure 포털을 사용하여 가상 네트워크(클래식) 만들기](../virtual-network/virtual-networks-create-vnet-classic-pportal.md)에 있는 단계를 따른 다음 **Redis Cache 가상 네트워크** 블레이드로 돌아가 선택합니다.

>[AZURE.NOTE]Azure Redis Cache는 클래식 VNET과 함께 작동합니다. 클래식 VNET을 만드는 방법에 대한 정보는 [Azure 포털을 사용하여 가상 네트워크(클래식) 만들기](../virtual-network/virtual-networks-create-vnet-classic-pportal.md)를 참조하세요. ARM VNET에 클래식 VNET을 연결하는 내용은 [새 VNet에 클래식 VNet 연결](../virtual-network/virtual-networks-arm-asm-s2s.md)을 참조하세요.

![가상 네트워크][redis-cache-vnet]

**가상 네트워크** 블레이드에서 **가상 네트워크**를 클릭하여 VNET을 선택 및 구성합니다.

![가상 네트워크][redis-cache-vnet-select]

원하는 VNET을 클릭하여 선택합니다.

![가상 네트워크][redis-cache-vnet-subnet]

서브넷을 클릭하여 원하는 서브넷을 선택합니다.

![가상 네트워크][redis-cache-vnet-ip]

**고정 IP 주소** 필드는 선택 사항입니다. 여기서 지정하지 않으면 선택한 서브넷에서 선택합니다. 특정 고정 IP가 필요한 경우 원하는 **고정 IP 주소**를 입력하고 **확인**을 클릭하여 VNET 구성을 저장합니다. 선택한 고정 IP가 이미 사용 중인 경우 오류 메시지가 표시됩니다.

캐시를 만든 후에는 동일한 VNET 안의 클라이언트에서만 액세스할 수 있습니다.

>[AZURE.IMPORTANT]VNET을 사용하는 경우 Azure Redis Cache 인스턴스에 액세스하려면 VNET에 있는 캐시의 고정 IP 주소를 첫 번째 매개 변수로 전달하고 캐시의 끝점을 사용하여 `sslhost` 매개 변수를 전달합니다. 다음 예제에서 고정 IP 주소는 `10.10.1.5`이고 캐시 끝점은 `contoso5.redis.cache.windows.net`입니다.

	private static Lazy<ConnectionMultiplexer> lazyConnection = new Lazy<ConnectionMultiplexer>(() =>
	{
	    return ConnectionMultiplexer.Connect("10.10.1.5,sslhost=contoso5.redis.cache.windows.net,abortConnect=false,ssl=true,password=password");
	});
	
	public static ConnectionMultiplexer Connection
	{
	    get
	    {
	        return lazyConnection.Value;
	    }
	}

## Azure Redis Cache VNET FAQ

다음 목록에는 Azure Redis Cache 크기 조정에 대해 일반적으로 묻는 질문과 답변이 들어 있습니다.

## Azure Redis 캐시 및 VNET의 몇 가지 일반적인 구성 오류 문제는 무엇인가요?

Azure Redis Cache가 VNET에 호스트된 경우 다음 표의 포트가 사용됩니다. 이러한 포트가 차단되면 캐시가 제대로 작동하지 않을 수 있습니다. 이러한 포트가 하나 이상 차단되는 것은 VNET에서 Azure Redis Cache를 사용하는 경우 가장 일반적인 잘못된 구성 문제입니다.

| 포트 | 방향 | 전송 프로토콜 | 목적 | 원격 IP |
|-------------|------------------|--------------------|-----------------------------------------------------------------------------------|-------------------------------------|
| 80, 443 | 아웃바운드 | TCP | Azure 저장소/PKI(인터넷)에 대한 Redis 종속성 | * |
| 53 | 아웃바운드 | TCP/UDP | DNS(인터넷/VNet)에 대한 Redis 종속성 | * |
| 6379, 6380 | 인바운드 | TCP | Redis, Azure 부하 분산에 대한 클라이언트 통신 | VIRTUAL\_NETWORK, AZURE\_LOADBALANCER |
| 8443 | 인바운드/아웃바운드 | TCP | Redis에 대한 구현 세부 정보 | VIRTUAL\_NETWORK |
| 8500 | 인바운드 | TCP/UDP | Azure 부하 분산 | AZURE\_LOADBALANCER |
| 10221-10231 | 인바운드/아웃바운드 | TCP | Redis에 대한 구현 세부 정보(VIRTUAL\_NETWORK에 원격 끝점을 제한할 수 있음) | VIRTUAL\_NETWORK, AZURE\_LOADBALANCER |
| 13000-13999 | 인바운드 | TCP | Redis 클러스터, Azure 부하 분산에 대한 클라이언트 통신 | VIRTUAL\_NETWORK, AZURE\_LOADBALANCER |
| 15000-15999 | 인바운드 | TCP | Redis 클러스터, Azure 부하 분산에 대한 클라이언트 통신 | VIRTUAL\_NETWORK, AZURE\_LOADBALANCER |
| 16001 | 인바운드 | TCP/UDP | Azure 부하 분산 | AZURE\_LOADBALANCER |
| 20226 | 인바운드+아웃바운드 | TCP | Redis 클러스터에 대한 구현 세부 정보 | VIRTUAL\_NETWORK |




## 표준 또는 기본 캐시에 VNET을 사용할 수 있나요?

VNET은 프리미엄 캐시에만 사용할 수 있습니다.

## 다음 단계
더 많은 프리미엄 캐시 기능을 사용하는 방법에 대해 알아봅니다.

-	[프리미엄 Azure Redis Cache에 지속성을 구성하는 방법](cache-how-to-premium-persistence.md)
-	[프리미엄 Azure Redis Cache에 클러스터링을 구성하는 방법](cache-how-to-premium-clustering.md)





  
<!-- IMAGES -->

[redis-cache-new-cache-menu]: ./media/cache-how-to-premium-vnet/redis-cache-new-cache-menu.png

[redis-cache-premium-pricing-tier]: ./media/cache-how-to-premium-vnet/redis-cache-premium-pricing-tier.png

[redis-cache-vnet]: ./media/cache-how-to-premium-vnet/redis-cache-vnet.png

[redis-cache-vnet-select]: ./media/cache-how-to-premium-vnet/redis-cache-vnet-select.png

[redis-cache-vnet-ip]: ./media/cache-how-to-premium-vnet/redis-cache-vnet-ip.png

[redis-cache-vnet-subnet]: ./media/cache-how-to-premium-vnet/redis-cache-vnet-subnet.png

<!---HONumber=AcomDC_0121_2016-->