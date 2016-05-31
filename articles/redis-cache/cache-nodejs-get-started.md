<properties
	pageTitle="Azure Redis Cache를 Node.js와 함께 사용하는 방법 | Microsoft Azure"
	description="Node.js 및 node_redis를 사용하여 Azure Redis Cache를 시작합니다."
	services="redis-cache"
	documentationCenter=""
	authors="steved0x"
	manager="erikre"
	editor="v-lincan"/>

<tags
	ms.service="cache"
	ms.devlang="nodejs"
	ms.topic="hero-article"
	ms.tgt_pltfrm="cache-redis"
	ms.workload="tbd"
	ms.date="05/24/2016"
	ms.author="sdanie"/>

# Azure Redis Cache를 Node.js와 함께 사용하는 방법

> [AZURE.SELECTOR]
- [.NET](cache-dotnet-how-to-use-azure-redis-cache.md)
- [ASP.NET](cache-web-app-howto.md)
- [Node.JS](cache-nodejs-get-started.md)
- [Java](cache-java-get-started.md)
- [Python](cache-python-get-started.md)

Azure Redis Cache는 Microsoft에서 관리하는 안전한 전용 Redis Cache에 액세스할 수 있게 합니다. Microsoft Azure 내의 모든 응용 프로그램에서 캐시에 액세스할 수 있습니다.

이 항목에서는 Node.js를 사용하여 Azure Redis Cache를 시작하는 방법을 설명합니다. Azure Redis Cache를 Node.js와 함께 사용하는 방법에 대한 다른 예는 [Azure 웹 사이트에서 Socket.IO를 사용하여 Node.js 채팅 응용 프로그램 빌드](../app-service-web/web-sites-nodejs-chat-app-socketio.md)를 참조하세요.


## 필수 조건

[node\_redis](https://github.com/mranney/node_redis)를 설치합니다.

    npm install redis

이 자습서에서는 [node\_redis](https://github.com/mranney/node_redis)를 사용하지만 [http://redis.io/clients](http://redis.io/clients)에 나열된 모든 Node.js 클라이언트를 사용할 수 있습니다.

## Azure에 Redis 캐시 만들기

[AZURE.INCLUDE [redis-cache-create](../../includes/redis-cache-create.md)]

## 호스트 이름 및 액세스 키 검색

[AZURE.INCLUDE [redis-cache-create](../../includes/redis-cache-access-keys.md)]


## 비 SSL 끝점 사용

일부 Redis 클라이언트는 SSL을 지원하지 않으며 기본적으로 [새 Azure Redis Cache 인스턴스에 대해 비 SSL 포트는 사용되지 않습니다](cache-configure.md#access-ports). 이 문서 작성 당시 [node\_redis](https://github.com/mranney/node_redis) 클라이언트는 SSL을 지원하지 않습니다.

[AZURE.INCLUDE [redis-cache-create](../../includes/redis-cache-non-ssl-port.md)]


## 캐시에 항목 추가 및 검색

	  var redis = require("redis");
	
	  // Add your cache name and access key.
	var client = redis.createClient(6380,'<name>.redis.cache.windows.net', {auth_pass: '<key>', tls: {servername: '<name>.redis.cache.windows.net'}});
	
	client.set("key1", "value", function(err, reply) {
		    console.log(reply);
		});
	
	client.get("key1",  function(err, reply) {
		    console.log(reply);
		});

출력:

	OK
	value


## 다음 단계

- 캐시의 상태를 [모니터링](cache-how-to-monitor.md)할 수 있도록 [캐시 진단을 사용하도록 설정](cache-how-to-monitor.md#enable-cache-diagnostics)합니다.
- 공식 [Redis 설명서](http://redis.io/documentation)를 읽어보세요.

<!---HONumber=AcomDC_0525_2016-->