<properties 
   pageTitle="Azure Redis Cache를 Java와 함께 사용하는 방법" 
   description="Java를 사용하여 Azure Redis Cache를 시작합니다." 
   services="redis-cache" 
   documentationCenter="" 
   authors="MikeWasson" 
   manager="wpickett" 
   editor=""/>

<tags
   ms.service="cache"
   ms.devlang="java"
   ms.topic="article"
   ms.tgt_pltfrm="cache-redis"
   ms.workload="required" 
   ms.date="02/20/2015"
   ms.author="mwasson"/>

# Azure Redis Cache를 Java와 함께 사용하는 방법 

Azure Redis Cache는 Microsoft에서 관리하는 안전한 전용 Redis Cache에 액세스할 수 있게 합니다. Microsoft Azure 내의 모든 응용 프로그램에서 캐시에 액세스할 수 있습니다.

이 항목에서는 Java를 사용하여 Azure Redis Cache를 시작하는 방법을 설명합니다.


## 필수 조건

[Jedis](https://github.com/xetorthio/jedis) - Redis용 Java 클라이언트

이 자습서에서는 Jedis를 사용하지만 [http://redis.io/clients](http://redis.io/clients)에 나열된 모든 Java 클라이언트를 사용할 수 있습니다.


## Azure에 Redis 캐시 만들기

[Azure 관리 포털 미리 보기](http://go.microsoft.com/fwlink/?LinkId=398536)에서 **새로 만들기**를 클릭하고 **Redis Cache**를 선택합니다. 

  ![][1]

DNS 호스트 이름을 입력합니다. 이 이름은 `<name>.redis.cache.windows.net` 형식이 됩니다. **만들기**를 클릭합니다.

  ![][2]


캐시가 만들어졌으면 포털에서 캐시를 클릭하여 캐시 설정을 확인합니다. **키** 아래의 링크를 클릭하고 기본 키를 복사합니다. 요청을 인증하려면 이 키가 필요합니다.

  ![][4]


## 비 SSL 끝점 사용


**포트** 아래의 링크를 클릭하고 "SSL을 통한 액세스만 허용"에 대해 **아니요**를 클릭합니다. 이렇게 하면 캐시에 대해 비 SSL 포트를 사용할 수 있습니다. 현재 Jedis 클라이언트는 SSL을 지원하지 않습니다.

  ![][3]


## 캐시에 항목 추가 및 검색

	package com.mycompany.app;
	import redis.clients.jedis.Jedis;
	import redis.clients.jedis.JedisShardInfo;
	 
	/* Make sure your turn on non SSL port in Azure Redis using the Configuration section in the Azure portal */
	public class App
	{
	  public static void main( String[] args )
	  {
        /* In this line, replace <name> with your cache name: */
	    JedisShardInfo shardInfo = new JedisShardInfo("<name>.redis.cache.windows.net", 6379);
	    shardInfo.setPassword("<key>"); /* Use your access key. */
	    Jedis jedis = new Jedis(shardInfo);
     	jedis.set("foo", "bar");
     	String value = jedis.get("foo");
	  }
	} 
    

## 다음 단계

- 캐시의 상태를 [모니터링](https://msdn.microsoft.com/library/azure/dn763945.aspx)할 수 있도록 [캐시 진단을 사용하도록 설정](https://msdn.microsoft.com/library/azure/dn763945.aspx#EnableDiagnostics)합니다. 
- 공식 [Redis 설명서](http://redis.io/documentation)를 읽어보세요.


<!--Image references-->
[1]: ./media/cache-java-get-started/cache01.png
[2]: ./media/cache-java-get-started/cache02.png
[3]: ./media/cache-java-get-started/cache03.png
[4]: ./media/cache-java-get-started/cache04.png


<!--HONumber=49-->