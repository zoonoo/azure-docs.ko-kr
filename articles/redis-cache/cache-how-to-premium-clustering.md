<properties 
	pageTitle="프리미엄 Azure Redis Cache에 Redis 클러스터링을 구성하는 방법" 
	description="프리미엄 계층 Azure Redis Cache 인스턴스에 대해 Redis 클러스터링을 만들고 관리하는 방법에 대해 알아봅니다." 
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

# 프리미엄 Azure Redis Cache에 Redis 클러스터링을 구성하는 방법
Azure Redis Cache에는 현재 미리 보기 상태인 새 프리미엄 계층을 포함하여 캐시 크기 및 기능을 유연하게 선택할 수 있는 다양한 캐시 제품이 있습니다.

Azure Redis Cache 프리미엄 계층에는 클러스터링, 지속성 및 가상 네트워크 지원이 포함됩니다. 이 문서에서는 프리미엄 Azure Redis Cache에서 클러스터링을 구성하는 방법을 설명합니다.

프리미엄 캐시 기능에 대한 자세한 내용은 [프리미엄 Azure Redis Cache에 지속성을 구성하는 방법](cache-how-to-premium-persistence.md)과 [프리미엄 Azure Redis Cache에 가상 네트워크 지원을 구성하는 방법](cache-how-to-premium-vnet.md)을 참조하세요.

>[AZURE.NOTE]Azure Redis Cache 프리미엄 계층은 현재 미리 보기 상태입니다.

## Redis 클러스터란?
Azure Redis Cache는 [Redis에서 구현된 형태의](http://redis.io/topics/cluster-tutorial) Redis 클러스터를 제공합니다. Redis 클러스터를 사용하면 다음과 같은 이점을 얻을 수 있습니다.

-	여러 노드 간에 자동으로 데이터 세트를 분할하는 기능. 
-	노드의 하위 집합에서 오류가 발생하거나 나머지 클러스터와 통신할 수 없더라도 작업을 계속하는 기능. 
-	처리량 증대: 분할된 데이터베이스(노드) 수를 늘림에 따라 처리량이 선형으로 늘어납니다. 
-	메모리 크기 증대: 분할된 데이터베이스(노드) 수를 늘림에 따라 선형으로 늘어납니다.  

프리미엄 캐시에서의 크기, 처리량 및 대역폭에 대한 자세한 내용은 [Azure Redis Cache FAQ](cache-faq.md#what-redis-cache-offering-and-size-should-i-use)를 참조하세요.

Azure에서 Redis 클러스터는 각각의 분할된 데이터베이스가 복제본이 있는 주/복제본 쌍을 갖는 주/복제본 모델 형태로 제공됩니다. 여기서는 Azure Redis Cache 서비스가 복제본을 관리합니다.

## 클러스터링
클러스터링은 캐시를 만드는 중에 **새 Redis 캐시** 블레이드에 구성됩니다. 캐시를 만들려면 [Azure Preview 포털](https://portal.azure.com)에 로그인하고 **새로 만들기** -> **데이터 + 저장소** > **Redis 캐시**를 클릭합니다.

![Redis Cache 만들기][redis-cache-new-cache-menu]

클러스터링을 구성하려면 먼저 **가격 계층 선택** 블레이드에서 **프리미엄** 캐시 중 하나를 선택합니다.

![가격 계층 선택][redis-cache-premium-pricing-tier]

클러스터링은 **Redis 클러스터** 블레이드에 구성됩니다.

![클러스터링][redis-cache-clustering]

클러스터에서 최대 10개의 분할된 데이터베이스를 사용할 수 있습니다. **사용**을 클릭하고 슬러이더를 밀거나 **분할된 데이터베이스 수**에 1에서 10 사이의 숫자를 입력하고 **확인**을 클릭합니다.

각각의 분할된 데이터베이스는 Azure에서 관리하는 주/복제본 캐시 쌍이며, 캐시의 총 크기는 분할된 데이텁베이스 수에 가격 책정 계층에서 선택한 캐시 크기를 곱하여 산출합니다.

![클러스터링][redis-cache-clustering-selected]

캐시가 생성되면 캐시에 연결하여 비 클러스터 캐시처럼 사용할 수 있으며 Redis가 데이터를 캐시 분할 데이터베이스 전체에 배포합니다.

## 클러스터링 FAQ

다음 목록에는 Azure Redis Cache 클러스터링에 대해 일반적으로 묻는 질문과 답변이 들어 있습니다.

## 만들 수 있는 최대 캐시 크기는?

가장 큰 프리미엄 캐시 크기는 53GB입니다. 최대 10개의 분할된 데이터베이스를 만들 수 있으므로 530GB의 최대 크기를 제공합니다. 더 큰 크기가 필요한 경우 [추가 요청](mailto:wapteams@microsoft.com?subject=Redis%20Cache%20quota%20increase)이 가능합니다. 자세한 내용은 [Azure Redis Cache 가격 책정](https://azure.microsoft.com/pricing/details/cache/)을 참조하세요.

## 모든 Redis 클라이언트가 클러스터링을 지원하나요?

현재 일부 클라이언트가 Redis 클러스터링을 지원합니다. 이를 지원하는 클라이언트는 StackExchange.Redis입니다. 다른 클라이언트에 대한 자세한 내용은 [Redis 클러스터 자습서](http://redis.io/topics/cluster-tutorial)의 [클러스터 작업](http://redis.io/topics/cluster-tutorial#playing-with-the-cluster) 섹션을 참조하세요.

>[AZURE.NOTE]StackExchange.Redis를 클라이언트로 사용하는 경우 클러스터링이 제대로 작동할 수 있게 [StackExchange.Redis](https://www.nuget.org/packages/StackExchange.Redis/) 1.0.481 이상의 최신 버전을 사용합니다.

## 클러스터링을 사용할 때 캐시에 어떻게 연결하나요?

클러스터링을 사용하지 않는 캐시에 연결할 때와 동일한 [끝점, 포트 및 키](cache-configure.md#properties)를 사용하여 캐시에 연결할 수 있습니다. Redis가 백엔드에서 클러스터링을 관리하므로 클라이언트에서의 관리가 필요하지 않습니다.

## 내 케시의 분할된 데이터베이스에 직접 연결할 수 있나요?

공식적으로는 지원되지 않습니다. 즉 분할된 각각의 데이터베이스는 통틀어 캐시 인스턴스라고 하는 주/복제본 캐시로 구성되어 있습니다. 다음 패턴에서 redis-cli.exe를 사용하여 이러한 캐시 인스턴스에 연결할 수 있습니다.

비 SSL은 다음 명령을 사용합니다.

	Redis-cli.exe –h <<cachename>> -p 13000 (to connect to instance 0)
	Redis-cli.exe –h <<cachename>> -p 13001 (to connect to instance 1)
	Redis-cli.exe –h <<cachename>> -p 13002 (to connect to instance 2)
	...
	Redis-cli.exe –h <<cachename>> -p 1300N (to connect to instance N)

SSL에서는 `1300N`을 `1500N`으로 대체합니다.

## 이전에 만든된 캐시에 대해 클러스터링을 구성할 수 있나요?

미리 보기 기간 중에는 캐시를 만들 때만 클러스터링을 활성화하고 구성할 수 있습니다.

## 기본 또는 표준 캐시에 클러스터링을 구성할 수 있나요?

클러스터링은 프리미엄 캐시에만 사용할 수 있습니다.

## 다음 단계

다른 프리미엄 캐시 기능의 사용 방법은 [프리미엄 Azure Redis Cache에 지속성을 구성하는 방법](cache-how-to-premium-persistence.md)과 [프리미엄 Azure Redis Cache에 가상 네트워크 지원을 구성하는 방법](cache-how-to-premium-vnet.md)을 참조하세요.
  
<!-- IMAGES -->

[redis-cache-new-cache-menu]: ./media/cache-how-to-premium-clustering/redis-cache-new-cache-menu.png

[redis-cache-premium-pricing-tier]: ./media/cache-how-to-premium-clustering/redis-cache-premium-pricing-tier.png

[NewCacheMenu]: ./media/cache-how-to-premium-clustering/redis-cache-new-cache-menu.png

[CacheCreate]: ./media/cache-how-to-premium-clustering/redis-cache-cache-create.png

[redis-cache-premium-pricing-group]: ./media/cache-how-to-premium-clustering/redis-cache-premium-pricing-group.png

[redis-cache-premium-features]: ./media/cache-how-to-premium-clustering/redis-cache-premium-features.png

[redis-cache-clustering]: ./media/cache-how-to-premium-clustering/redis-cache-clustering.png

[redis-cache-clustering-selected]: ./media/cache-how-to-premium-clustering/redis-cache-clustering-selected.png

<!---HONumber=Oct15_HO1-->