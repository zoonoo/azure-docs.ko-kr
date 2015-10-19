<properties 
	pageTitle="Azure Redis Cache 프리미엄 계층 소개" 
	description="프리미엄 계층 Azure Redis Cache 인스턴스에 대해 Redis 지속성, Redis 클러스터링 및 VNET 지원을 만들고 관리하는 방법에 대해 알아봅니다." 
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
	ms.date="10/02/2015" 
	ms.author="sdanie"/>

# Azure Redis Cache 프리미엄 계층 소개
Azure Redis Cache는 분산되고 관리된 캐시로, 데이터에 매우 빠르게 액세스하여 확장성과 응답성이 뛰어난 응용 프로그램을 빌드하는 데 사용할 수 있습니다.

새로운 프리미엄 계층은 모든 표준 계층 기능과 추가 기능(예: 성능 향상, 더 큰 작업, 재해 복구 및 강화된 보안)이 포함된 엔터프라이즈급 계층입니다. 프리미엄 캐시 계층의 추가 기능에 대해 자세히 알아보려면 계속 읽습니다.

## 표준 또는 기본 계층에 비해 향상된 성능
**표준 또는 기본 계층에 대해 향상된 성능.** 프리미엄 계층의 캐시는 더 빠른 프로세서가 포함되고 기본 또는 표준 계층에 비해 더 나은 성능을 제공하는 하드웨어에 배포됩니다. 프리미엄 계층 캐시는 처리량은 더 높고 대기 시간은 더 짧습니다.

**동일한 크기의 캐시에 대한 처리량이 표준 계층에 비해 프리미엄에서 더 높습니다.** 예를 들어 53GB 캐시의 경우 P4(프리미엄)의 처리량은 초당 250K의 요청인 반면 C6(표준)은 150K입니다.

프리미엄 캐시에서의 크기, 처리량 및 대역폭에 대한 자세한 내용은 [Azure Redis Cache FAQ](cache-faq.md#what-redis-cache-offering-and-size-should-i-use)를 참조하세요.

## Redis 데이터 지속성
프리미엄 계층을 사용하면 Azure 저장소 계정에서 캐시 데이터를 유지할 수 있습니다. 기본/표준 캐시에서 모든 데이터는 메모리에만 저장됩니다. 기본 인프라의 경우 문제는 잠재적인 데이터 손실이 있을 수 있다는 점입니다. 데이터 손실에 대한 복원력을 늘리기 위해 프리미엄 계층에서 Redis 데이터 지속성 기능을 사용하는 것이 좋습니다. Azure Redis Cache는 [Redis 지속성](http://redis.io/topics/persistence)에서 RDB 및 AOF(출시 예정) 옵션을 제공합니다.

지속성 구성에 대한 지침은 [프리미엄 Azure Redis Cache에 지속성을 구성하는 방법](cache-how-to-premium-persistence.md)을 참조하세요.

##Redis 클러스터
53GB보다 큰 캐시를 만들거나 여러 Redis 노드에서 데이터를 분할하려는 경우 프리미엄 계층에서 사용 가능한 Redis 클러스터링을 사용할 수 있습니다. 각 노드는 고가용성을 위해 Azure에 의해 관리되는 주/복제본 캐시 쌍으로 구성됩니다.

**Redis 클러스터링은 최대 배율 및 처리량을 제공합니다.** 클러스터에서 분할된 데이터베이스(노드) 수를 늘림에 따라 처리량이 선형으로 늘어납니다. 10개의 분할된 데이터베이스에 대해 P4 클러스터를 만드는 경우 가능한 처리량은 250K * 10 = 초당 250만 요청 수입니다. 프리미엄 캐시에서의 크기, 처리량 및 대역폭에 대한 자세한 내용은 [Azure Redis Cache FAQ](cache-faq.md#what-redis-cache-offering-and-size-should-i-use)를 참조하세요.

클러스터링을 시작하려면 [프리미엄 Azure Redis Cache에 클러스터링을 구성하는 방법](cache-how-to-premium-clustering.md)을 참조하세요.

##강화된 보안 및 격리
기본 또는 표준 계층에서 만든 캐시는 공용 인터넷에서 액세스할 수 있습니다. 캐시에 대한 액세스는 선택키에 따라 제한됩니다. 프리미엄 계층을 사용하면 지정된 네트워크 내의 클라이언트만 캐시에 액세스할 수 있는지 추가로 확인할 수 있습니다. [Azure VNet(가상 네트워크)](https://azure.microsoft.com/services/virtual-network/)에서 Redis Cache를 배포할 수 있습니다. 서브넷, 액세스 제어 정책과 같은 VNet의 모든 기능 및 다른 기능을 사용하여 추가로 Redis에 대한 액세스를 제한할 수 있습니다.

자세한 내용은 [프리미엄 Azure Redis Cache에 가상 네트워크 지원을 구성하는 방법](cache-how-to-premium-vnet.md)을 참조하세요.

## 다음 단계

캐시를 만들고 새로운 프리미엄 계층 기능을 탐색합니다.

-	[프리미엄 Azure Redis Cache에 지속성을 구성하는 방법](cache-how-to-premium-persistence.md)
-	[프리미엄 Azure Redis Cache에 가상 네트워크 지원을 구성하는 방법](cache-how-to-premium-vnet.md)
-	[프리미엄 Azure Redis Cache에 클러스터링을 구성하는 방법](cache-how-to-premium-clustering.md)
  

<!---HONumber=Oct15_HO2-->