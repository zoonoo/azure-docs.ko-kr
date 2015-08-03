<properties 
	pageTitle="Azure Redis Cache 크기를 조정하는 방법" 
	description="Azure Redis Cache 인스턴스 크기를 조정하는 방법에 대해 알아봅니다." 
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
	ms.date="06/18/2015" 
	ms.author="sdanie"/>

# Azure Redis Cache 크기를 조정하는 방법

>[AZURE.NOTE]Azure Redis Cache 크기 조정 기능은 현재 미리 보기 상태입니다.

Azure Redis Cache에는 캐시 크기 및 기능을 유연하게 선택할 수 있는 다양한 캐시 제품이 있습니다. 캐시를 만든 후 응용 프로그램 요구 사항이 변경되면 [Azure 포털](https://portal.azure.com)의 **가격 책정 계층 변경** 블레이드를 사용하여 캐시 크기를 조정할 수 있습니다.

>[AZURE.NOTE]Azure Redis Cache 크기를 조정하는 경우 크기는 변경할 수는 있으나 표준 캐시에서 기본 캐시로 또는 그 반대로 변경할 수는 없습니다.

## 크기를 조정하는 경우

Azure Redis Cache의 [모니터링](cache-how-to-monitor.md) 기능을 사용하여 캐시 응용 프로그램의 상태 및 성능을 모니터링하고 캐시 크기를 조정해야 하는지 결정하는데 도움을 줄 수 있습니다.

다음 메트릭을 모니터링하면 크기를 조정해야 하는지 결정하는데 도움이 될 수 있습니다.

-	Redis 서버 부하
-	메모리 사용량
-	네트워크 대역폭
-	CPU 사용량

캐시가 더 이상 응용 프로그램 요구 사항을 충족시키지 못한다고 판단되면 응용 프로그램에 적합하도록 더 크거나 더 작은 캐시 가격 책정 계층으로 변경할 수 있습니다. 사용할 캐시 가격 책정 계층 결정에 대한 자세한 내용은 [어떤 Redis Cache 제품 및 크기를 사용해야 하나요?](cache-faq.md#what-redis-cache-offering-and-size-should-i-use)를 참조하세요.

## 캐시 크기 조정
캐시 크기를 조정하려면 [Azure 포털](https://portal.azure.com)에서 [캐시를 찾고](https://msdn.microsoft.com/library/azure/dn793612.aspx#RedisCacheConfiguration) **Redis Cache** 블레이드에서 **표준 계층** 또는 **기본 계층** 부분을 클릭합니다.

![가격 책정 계층][redis-cache-pricing-tier-part]

**가격 책정 계층** 블레이드에서 원하는 가격 책정 계층을 선택하고 **선택**을 클릭합니다.

![가격 책정 계층][redis-cache-pricing-tier-blade]

>[AZURE.NOTE]캐시를 기본 계획에서 표준 계획 또는 그 반대로 변경할 수 없으며 캐시 크기를 250MB보다 더 큰 크기 중 하나에서 250MB로 축소할 수 없습니다. 캐시 크기를 250MB에서 더 큰 크기로 확장할 수 있으나 다시 250MB 가격 책정 계층으로 축소할 수 없습니다. 기본에서 표준으로 변경해야 하는 경우 또는 250MB 크기로 축소해야 하는 경우 새 캐시를 만들어야 합니다.

새로운 가격 책정 계층으로 캐시 크기를 조정하는 동안에는 **Scaling(크기 조정 중)** 상태가 **Redis Cache** 블레이드에 표시됩니다.

![확장][redis-cache-scaling]

크기 조정이 완료되면 상태가 **Scaling(크기 조정 중)**에서 **실행 중**으로 변경됩니다.

>[AZURE.IMPORTANT]크기 조정 작업을 수행하는 동안에는 기본 캐시가 오프라인 상태이며 캐시의 모든 데이터가 손실됩니다. 크기 조정 작업이 완료되면 기본 캐시는 데이터가 없는 온라인 상태로 돌아옵니다. 표준 캐시는 크기 조정 작업을 수행하는 동안 온라인 상태를 유지하며 표준 캐시를 더 큰 크기로 조정하는 경우 일반적으로 데이터가 손실되지 않습니다. 표준 캐시를 더 작은 크기로 조정하는 경우 새 크기가 캐시된 데이터의 크기보다 작으면 일부 데이터가 손실될 수 있습니다. 크기를 축소하는 경우 데이터가 손실되면 [allkeys-lru](http://redis.io/topics/lru-cache) 제거 정책을 사용하여 키를 제거합니다. 표준 캐시의 가용성에 대한 SLA는 99.9%이나 데이터 손실에 대한 SLA는 없습니다.

## 크기 조정 작업을 자동화하는 방법

Azure 포털에서 Azure Redis Cache 인스턴스 크기를 조정할 뿐만 아니라 [MAML(Microsoft Azure Management Libraries)](http://azure.microsoft.com/updates/management-libraries-for-net-release-announcement/)로 크기를 조정할 수도 있습니다. 캐시 크기를 조정하려면 `IRedisOperations.CreateOrUpdate` 메서드를 호출하고 `RedisProperties.SKU.Capacity`의 새 크기를 전달합니다.

    static void Main(string[] args)
    {
        // For instructions on getting the access token, see
        // https://msdn.microsoft.com/ko-kr/library/azure/dn790557.aspx#bk_portal
        string token = GetAuthorizationHeader();

        TokenCloudCredentials creds = new TokenCloudCredentials(subscriptionId,token);

        RedisManagementClient client = new RedisManagementClient(creds);
        var redisProperties = new RedisProperties();

        // To scale, set a new size for the redisSKUCapacity parameter.
        redisProperties.Sku = new Sku(redisSKUName,redisSKUFamily,redisSKUCapacity);
        redisProperties.RedisVersion = redisVersion;
        var redisParams = new RedisCreateOrUpdateParameters(redisProperties, redisCacheRegion);
        client.Redis.CreateOrUpdate(resourceGroupName,cacheName, redisParams);
    }

자세한 내용은 [MAML로 Redis Cache 관리](https://github.com/rustd/RedisSamples/tree/master/ManageCacheUsingMAML) 샘플을 참조하세요.

## 크기 조정 FAQ

다음 목록에는 Azure Redis Cache 크기 조정에 대해 일반적으로 묻는 질문과 답변이 들어 있습니다.

## 크기를 조정한 후 내 캐시 이름 또는 액세스 키를 변경해야 하나요?

아니요, 캐시 이름 및 키는 크기 조정 작업을 수행하는 동안 변경되지 않습니다.

## 크기 조정은 어떻게 수행되나요?

**기본** 캐시 크기를 조정하는 경우 캐시가 종료되고 새 크기를 사용하여 새 캐시를 프로비전합니다. 이 시간 동안에는 캐시를 사용할 수 없으며 캐시의 모든 데이터가 손실됩니다.

**표준** 캐시 크기를 조정하는 경우 복제본 중 하나가 종료되고 새 크기로 다시 프로비전되며 데이터가 전송됩니다. 그런 다음 나머지 복제본이 장애 조치(failover)를 수행한 후 다시 프로비전됩니다. 캐시 노드 중 하나에 오류가 발생하면 수행되는 프로세스와 비슷합니다.

## 크기를 조정하는 동안 캐시의 데이터가 손실되나요?

**기본** 캐시 크기를 조정하는 경우 모든 데이터가 손실되고 크기 조정 작업을 수행하는 동안 캐시를 사용할 수 없습니다.

**표준** 캐시를 더 큰 크기로 조정하는 경우 일반적으로 모든 데이터가 유지됩니다. **표준** 캐시를 더 작은 크기로 조정하는 경우 조정된 새 크기에 비해 얼마나 많은 데이터가 캐시에 있는지에 따라 데이터가 손실될 수 있습니다. 크기를 축소하는 경우 데이터가 손실되면 [allkeys-lru](http://redis.io/topics/lru-cache) 제거 정책을 사용하여 키를 제거합니다. 표준 캐시의 가용성에 대한 SLA는 99.9%이나 데이터 손실에 대한 SLA는 없습니다.

## 크기를 조정하는 동안 내 캐시를 사용할 수 있나요?

**표준** 캐시는 크기 조정 작업을 수행하는 동안 사용할 수 있습니다.

**기본** 캐시는 크기 조정 작업을 수행하는 동안 오프라인 상태가 됩니다.

## 지원되지 않는 작업

크기 조정 작업을 수행하는 동안에는 **기본** 캐시에서 **표준** 캐시 또는 그 반대로 변경할 수 없습니다.

**C0**(250MB) 캐시에서 더 큰 크기로 확장할 수는 있으나 더 큰 크기에서 **C0** 캐시로 축소할 수는 없습니다.

크기 조정 작업이 실패하면 서비스는 작업을 되돌리려고 하며 캐시는 원래 크기로 되돌아갑니다.

## 크기 조정은 시간이 얼마나 걸리나요?

크기 조정은 약 20분이 걸립니다. 캐시에 있는 데이터의 양에 따라 다를 수 있습니다.

## 크기 조정이 완료되었는지 어떻게 알 수 있나요?

포털에서 진행 중인 크기 조정 작업을 볼 수 있습니다. 크기 조정이 완료되면 캐시 상태가 **실행 중**으로 변경됩니다.

## 이 기능이 미리 보기 상태인 이유는 무엇인가요?

이 기능은 사용자 의견을 듣기 위해 출시되었습니다. 사용자 의견을 기반으로 곧 이 기능을 일반 공급으로 출시할 것입니다.





  
<!-- IMAGES -->
[redis-cache-pricing-tier-part]: ./media/cache-how-to-scale/redis-cache-pricing-tier-part.png

[redis-cache-pricing-tier-blade]: ./media/cache-how-to-scale/redis-cache-pricing-tier-blade.png

[redis-cache-scaling]: ./media/cache-how-to-scale/redis-cache-scaling.png

<!---HONumber=July15_HO4-->