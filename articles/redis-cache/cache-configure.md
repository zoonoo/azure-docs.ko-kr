<properties 
   pageTitle="Azure Redis 캐시 구성 방법"
	description="Azure Redis 캐시에 대한 기본 Redis 구성을 이해하고 Azure Redis 캐시 인스턴스를 구성하는 방법을 알아봅니다."
	services="redis-cache"
	documentationCenter="na"
	authors="steved0x"
	manager="dwrede"
	editor="tysonn"/>
<tags 
   ms.service="cache"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="cache-redis"
	ms.workload="tbd"
	ms.date="09/03/2015"
	ms.author="sdanie"/>

# Azure Redis 캐시 구성 방법

이 항목에서는 Azure Redis 캐시 인스턴스에 대한 구성을 검토하고 업데이트하는 방법과 Azure Redis 캐시 인스턴스에 대한 기본 Redis 서버 구성을 살펴봅니다.

## Redis 캐시 설정 구성

캐시는 [Azure 미리 보기 포털](https://portal.azure.com)의 **찾아보기** 블레이드를 사용하여 액세스할 수 있습니다.

![Azure Redis 캐시 찾아보기 블레이드](./media/cache-configure/IC796920.png)

**Redis Caches**를 클릭하여 캐시를 확인합니다.

![Azure Redis 캐시 찾아보기 캐시 목록](./media/cache-configure/IC796921.png)

캐시에 대한 속성을 보려면 원하는 캐시를 선택합니다.

![Redis 캐시 모든 설정](./media/cache-configure/IC808312.png)

**설정** 또는 **모든 설정**을 클릭하여 캐시를 보고 구성할 수 있습니다.

![Redis 캐시 설정](./media/cache-configure/IC808313.png)

## 속성

**속성**을 클릭하여 캐시 끝점 및 포트를 포함하여 캐시에 대한 정보를 볼 수 있습니다.

![Redis 캐시 속성](./media/cache-configure/IC808314.png)

## 액세스 키

**액세스 키**를 클릭하여 캐시에 대한 액세스 키를 보거나 생성할 수 있습니다. 이러한 키는 클라이언트가 캐시에 연결할 때 **속성** 블레이드에서 호스트 이름 및 포트와 함께 사용됩니다.

![Redis 캐시 액세스 키](./media/cache-configure/IC808315.png)

## 액세스 포트

비 SSL 액세스는 기본적으로 새 캐시에 대해 사용하지 않도록 설정됩니다. 비 SSL 포트를 사용하도록 설정하려면 **액세스 포트** 블레이드를 클릭하고 **아니요**를 클릭합니다.

![Redis 캐시 액세스 포트](./media/cache-configure/IC808316.png)

## 가격 책정 계층

**가격 책정 계층**을 클릭하여 캐시에 대한 가격 책정 계층을 보거나 변경합니다. 크기 조정에 대한 자세한 내용은 [Azure Redis Cache 크기를 조정하는 방법](cache-how-to-scale.md)을 참조하세요.

![Redis Cache 가격 책정 계층](./media/cache-configure/pricing-tier.png)

## 진단

**진단**을 클릭하여 캐시 진단 정보 저장에 사용되는 저장소 계정을 구성할 수 있습니다.

![Redis 캐시 진단](./media/cache-configure/IC808317.png)

자세한 내용은 [Azure Redis Cache를 모니터링하는 방법](cache-how-to-monitor.md)을 참조하세요.

## Maxmemory-policy 및 maxmemory-reserved

**Maxmemory 정책**을 클릭하여 캐시에 대한 메모리 정책을 구성할 수 있습니다. **maxmemory-policy** 설정은 캐시에 대한 제거 정책을, **maxmemory-reserved** 설정은 비 캐시 프로세스를 위해 예약되는 메모리를 구성합니다.

![Redis 캐시 Maxmemory 정책](./media/cache-configure/IC808318.png)

**Maxmemory 정책**을 사용하여 다음 제거 정책 중에서 선택할 수 있습니다.

-	volatile-lru - 기본값입니다.
-	allkeys-lru
-	volatile-random
-	allkeys-random
-	volatile-ttl
-	noeviction

Maxmemory 정책에 대한 자세한 내용은 [제거 정책](http://redis.io/topics/lru-cache#eviction-policies)을 참조하세요.

**maxmemory-reserved** 설정은 장애 조치(failover) 중 복제와 같은 비캐시 작업을 위해 예약되는 메모리의 양을 MB 단위로 구성합니다. 조각화 비율이 높을 때 사용할 수도 있습니다. 이 값을 설정하면 부하가 달라져도 Redis 서버 환경이 더 일관되도록 할 수 있습니다. 이 값은 쓰기 작업이 많은 워크로드에 더 높게 설정되어야 합니다. 이러한 작업을 위해 메모리가 예약된 경우 이는 캐시된 데이터의 저장에는 사용할 수 없습니다.

>[AZURE.IMPORTANT]**maxmemory-reserved** 설정은 표준 캐시에만 사용할 수 있습니다.

## Keyspace 알림(고급 설정)

**고급 설정**을 클릭하여 Redis keyspace 알림을 구성할 수 있습니다. Keyspace 알림을 사용하면 특정 이벤트가 발생할 때 클라이언트에서 알림을 받을 수 있습니다.

![Redis 캐시 고급 설정](./media/cache-configure/IC808319.png)

>[AZURE.IMPORTANT]Keyspace 알림과 **notify-keyspace-events** 설정은 표준 캐시에만 사용할 수 있습니다.

자세한 내용은 [Redis Keyspace 알림](http://redis.io/topics/notifications)을 참조하세요. 샘플 코드는 [KeySpaceNotifications.cs](https://github.com/rustd/RedisSamples/blob/master/HelloWorld/KeySpaceNotifications.cs) 파일의 [Hello world](https://github.com/rustd/RedisSamples/tree/master/HelloWorld) 샘플을 참조하세요.

## 사용자 및 태그

![Redis 캐시 사용자 및 태그](./media/cache-configure/IC808320.png)

조직이 액세스 관리에 필요한 요구 사항을 간단하면서도 정밀하게 충족할 수 있도록 미리 보기 포털의 **사용자** 섹션에서 RBAC(역할 기반 액세스 제어)를 지원합니다. 자세한 내용은 [Azure 미리 보기 포털의 역할 기반 액세스 제어](http://go.microsoft.com/fwlink/?LinkId=512803)를 참조하세요.

**태그** 섹션은 리소스 구성에 도움이 됩니다. 자세한 내용은 [태그를 사용하여 Azure 리소스 구성](../resource-group-using-tags.md)을 참조하세요.

## 기본 Redis 서버 구성

새 Azure Redis 캐시 인스턴스는 다음과 같은 기본 Redis 구성 값으로 구성됩니다.

>[AZURE.NOTE]이 섹션의 설정은 `StackExchange.Redis.IServer.ConfigSet` 메서드를 사용하여 변경할 수 없습니다. 이 메서드를 이 섹션의 명령 중 하나와 함께 호출하면 다음과 유사한 예외가 발생됩니다.
>
>`StackExchange.Redis.RedisServerException: ERR unknown command 'CONFIG'`
>  
>**max-memory-policy**와 같이 구성 가능한 모든 값은 미리 보기 포털을 통해 구성할 수 있습니다.

|설정|기본값|설명|
|---|---|---|
|데이터베이스|16|기본 데이터베이스는 DB 0입니다. connection.GetDataBase(dbid)를 사용하여 연결 단위로 다른 데이터베이스를 사용할 수 있습니다. 여기서 dbid는 0에서 15 사이의 숫자입니다.|
|maxclients|가격 책정 계층에 따라 달라집니다.<sup>1</sup>|이 값은 동시에 연결이 허용되는 클라이언트의 최대 수입니다. 제한에 도달하면 Redis는 'max number of clients reached' 오류를 보내고 모든 새 연결을 닫습니다.|
|maxmemory-policy|volatile-lru|Maxmemory 정책은 최대 메모리(캐시를 만들 때 선택한 캐시의 크기)에 도달했을 때 Redis가 어떤 것을 제거할지 선택하는 방법에 대한 설정입니다. Azure Redis 캐시를 사용할 때의 기본 설정은 volatile-lru로, LRU 알고리즘을 사용하여 만료 설정이 있는 키를 제거합니다. 이 설정은 미리 보기 포털에서 구성할 수 있습니다. 자세한 내용은 [Maxmemory-policy 및 maxmemory-reserved](#maxmemory-policy-and-maxmemory-reserved)를 참조하세요.|
|maxmemory-samples|3|LRU 및 최소 TTL 알고리즘은 정밀한 알고리즘은 아니지만 대략적인(메모리 절약을 위해) 알고리즘이므로, 확인할 샘플도 선택할 수 있습니다. 기본 Redis를 예로 들면 세 개의 키를 확인하고 가장 오래 전에 사용된 키를 선택합니다.|
|lua-time-limit|5, 000|밀리초 단위의 Lua 스크립트 최대 실행 시간입니다. 최대 실행 시간에 도달하면 Redis는 허용된 시간 이후에도 실행 중인 스크립트를 기록하고 쿼리에 오류로 응답합니다.|
|lua-event-limit|500|스크립트 이벤트 큐의 최대 크기입니다.|
|client-output-buffer-limit normalclient-output-buffer-limit pubsub|0 0 032mb 8mb 60|클라이언트 출력 버퍼 제한은 어떤 이유로 서버에서 데이터를 읽는 속도가 충분히 빠르지 않은 클라이언트의 연결을 강제로 끊는 데 사용할 수 있습니다. 속도가 느린 일반적인 이유는 게시/구독 클라이언트가 게시자의 생성 속도만큼 빠르게 메시지를 소화하지 못하기 때문입니다. 자세한 내용은 [http://redis.io/topics/clients](http://redis.io/topics/clients)를 참조하세요.|

<sup>1</sup>`maxclients`은 Azure Redis Cache 가격 책정 계층마다 다릅니다.

-	C0(250MB) 캐시 - 최대 256개 연결
-	C1(1GB) 캐시 - 최대 1,000개 연결
-	C2(2.5GB) 캐시 - 최대 2,000개 연결
-	C3(6GB) 캐시 - 최대 5,000개 연결
-	C4(13GB) 캐시 - 최대 10,000개 연결
-	C5(26GB) 캐시 - 최대 15,000개 연결
-	C6(53GB) 캐시 - 최대 20,000개 연결

## Azure Redis Cache에서 지원되지 않는 Redis 명령

>[AZURE.IMPORTANT]Azure Redis 캐시 인스턴스의 구성과 관리는 미리 보기 포털을 사용하여 수행되므로 다음 명령이 비활성화됩니다. 이러한 명령을 호출하려고 하면 `"(error) ERR unknown command"`와 유사한 오류 메시지가 표시됩니다.
>
>-	BGREWRITEAOF
>-	BGSAVE
>-	CONFIG
>-	DEBUG
>-	MIGRATE
>-	저장
>-	SHUTDOWN
>-	SLAVEOF

Redis 명령에 대한 자세한 내용은 [http://redis.io/commands](http://redis.io/commands)를 참조하세요.

## Redis 콘솔

표준 캐시에서 사용할 수 있는 **Redis 콘솔**을 사용하면 Azure Redis Cache 인스턴스에 대해 안전하게 명령을 실행할 수 있습니다. Redis 콘솔에 액세스하려면 **Redis 캐시** 블레이드에서 **콘솔**을 클릭합니다.

![Redis 콘솔](./media/cache-configure/redis-console-menu.png)

>[AZURE.IMPORTANT]Redis 콘솔은 표준 캐시에서만 사용할 수 있습니다.

캐시 인스턴스에 대해 명령을 실행하려면 원하는 명령을 콘솔에 입력하면 됩니다.

![Redis 콘솔](./media/cache-configure/redis-console.png)

Azure Redis Cache에 대해 사용할 수 없도록 설정된 Redis 명령 목록은 이전 [Azure Redis Cache에서 지원되지 않는 Redis 명령](#redis-commands-not-supported-in-azure-redis-cache) 섹션을 참조하세요. Redis 명령에 대한 자세한 내용은 [http://redis.io/commands](http://redis.io/commands)를 참조하세요.

## 다음 단계
-	Redis 명령을 사용하는 방법은 [어떻게 Redis 명령을 실행할 수 있나요?](cache-faq.md#how-can-i-run-redis-commands)를 참조하세요.

<!---HONumber=September15_HO1-->