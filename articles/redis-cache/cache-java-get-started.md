---
title: "Azure Redis Cache를 Java와 함께 사용하는 방법 | Microsoft 문서"
description: "Java를 사용하여 Azure Redis Cache를 시작합니다."
services: redis-cache
documentationcenter: 
author: steved0x
manager: douge
editor: 
ms.assetid: 29275a5e-2e39-4ef2-804f-7ecc5161eab9
ms.service: cache
ms.devlang: java
ms.topic: hero-article
ms.tgt_pltfrm: cache-redis
ms.workload: tbd
ms.date: 08/24/2016
ms.author: sdanie
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 907f75dc02bff7e25712a564410c1974e22f0d99


---
# <a name="how-to-use-azure-redis-cache-with-java"></a>Azure Redis Cache를 Java와 함께 사용하는 방법
> [!div class="op_single_selector"]
> * [.NET](cache-dotnet-how-to-use-azure-redis-cache.md)
> * [ASP.NET](cache-web-app-howto.md)
> * [Node.JS](cache-nodejs-get-started.md)
> * [Java](cache-java-get-started.md)
> * [Python](cache-python-get-started.md)
> 
> 

Azure Redis Cache는 Microsoft에서 관리하는 전용 Redis Cache에 액세스할 수 있게 합니다. Microsoft Azure 내의 모든 응용 프로그램에서 캐시에 액세스할 수 있습니다.

이 항목에서는 Java를 사용하여 Azure Redis Cache를 시작하는 방법을 설명합니다.

## <a name="prerequisites"></a>필수 조건
[Jedis](https://github.com/xetorthio/jedis) - Redis용 Java 클라이언트

이 자습서에서는 Jedis를 사용하지만 [http://redis.io/clients](http://redis.io/clients)에 나열된 모든 Java 클라이언트를 사용할 수 있습니다.

## <a name="create-a-redis-cache-on-azure"></a>Azure에 Redis 캐시 만들기
[!INCLUDE [redis-cache-create](../../includes/redis-cache-create.md)]

## <a name="retrieve-the-host-name-and-access-keys"></a>호스트 이름 및 액세스 키 검색
[!INCLUDE [redis-cache-create](../../includes/redis-cache-access-keys.md)]

## <a name="enable-the-nonssl-endpoint"></a>비 SSL 끝점 사용
일부 Redis 클라이언트는 SSL을 지원하지 않으며 기본적으로 [새 Azure Redis Cache 인스턴스에 대해 비 SSL 포트는 사용되지 않습니다](cache-configure.md#access-ports). 이 문서 작성 당시 [Jedis](https://github.com/xetorthio/jedis) 클라이언트는 SSL을 지원하지 않습니다. 

[!INCLUDE [redis-cache-create](../../includes/redis-cache-non-ssl-port.md)]

## <a name="add-something-to-the-cache-and-retrieve-it"></a>캐시에 항목 추가 및 검색
    package com.mycompany.app;
    import redis.clients.jedis.Jedis;
    import redis.clients.jedis.JedisShardInfo;

    /* Make sure you turn on non-SSL port in Azure Redis using the Configuration section in the Azure Portal */
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


## <a name="next-steps"></a>다음 단계
* [캐시 진단을 사용](https://msdn.microsoft.com/library/azure/dn763945.aspx#EnableDiagnostics)하도록 설정하면 캐시의 상태를 [모니터링](https://msdn.microsoft.com/library/azure/dn763945.aspx)할 수 있습니다.
* 공식 [Redis 설명서](http://redis.io/documentation)를 읽어보세요.




<!--HONumber=Nov16_HO2-->


