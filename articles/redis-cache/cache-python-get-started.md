<properties
	pageTitle="Azure Redis Cache를 Python과 함께 사용하는 방법 | Microsoft Azure"
	description="Python을 사용하여 Azure Redis Cache를 시작합니다."
	services="redis-cache"
	documentationCenter=""
	authors="steved0x"
	manager="douge"
	editor="v-lincan"/>

<tags
	ms.service="cache"
	ms.devlang="python"
	ms.topic="hero-article"
	ms.tgt_pltfrm="cache-redis"
	ms.workload="tbd"
	ms.date="08/16/2016"
	ms.author="sdanie"/>

# Azure Redis Cache를 Python과 함께 사용하는 방법

> [AZURE.SELECTOR]
- [.NET](cache-dotnet-how-to-use-azure-redis-cache.md)
- [ASP.NET](cache-web-app-howto.md)
- [Node.JS](cache-nodejs-get-started.md)
- [Java](cache-java-get-started.md)
- [Python](cache-python-get-started.md)

이 항목에서는 Python을 사용하여 Azure Redis Cache를 시작하는 방법을 설명합니다.


## 필수 조건

[redis-py](https://github.com/andymccurdy/redis-py)를 설치합니다.


## Azure에 Redis 캐시 만들기

[AZURE.INCLUDE [redis-cache-create](../../includes/redis-cache-create.md)]

## 호스트 이름 및 액세스 키 검색

[AZURE.INCLUDE [redis-cache-create](../../includes/redis-cache-access-keys.md)]


## 비 SSL 끝점 사용

일부 Redis 클라이언트는 SSL을 지원하지 않으며 기본적으로 [새 Azure Redis Cache 인스턴스에 대해 비 SSL 포트는 사용되지 않습니다](cache-configure.md#access-ports). 이 문서 작성 당시 [redis-py](https://github.com/andymccurdy/redis-py) 클라이언트는 SSL을 지원하지 않습니다.

[AZURE.INCLUDE [redis-cache-create](../../includes/redis-cache-non-ssl-port.md)]


## 캐시에 항목 추가 및 검색


	>>> import redis
	>>> r = redis.StrictRedis(host='<name>.redis.cache.windows.net',
	      port=6380, db=0, password='<key>', ssl=True)
	>>> r.set('foo', 'bar')
	True
	>>> r.get('foo')
	b'bar'


`<name>`을 사용 중인 캐시 이름으로 바꾸고 `key`를 사용 중인 액세스 키로 바꿉니다.


<!--Image references-->
[1]: ./media/cache-python-get-started/redis-cache-new-cache-menu.png
[2]: ./media/cache-python-get-started/redis-cache-cache-create.png

<!---HONumber=AcomDC_0817_2016-->