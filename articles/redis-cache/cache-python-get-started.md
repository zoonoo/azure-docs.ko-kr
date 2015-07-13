<properties
   pageTitle="Azure Redis Cache를 Python과 함께 사용하는 방법"
   description="Python을 사용하여 Azure Redis Cache를 시작합니다."
   services="redis-cache"
   documentationCenter=""
   authors="MikeWasson"
   manager="wpickett"
   editor=""/>

<tags
   ms.service="cache"
   ms.devlang="python"
   ms.topic="hero-article"
   ms.tgt_pltfrm="cache-redis"
   ms.workload="required"
   ms.date="04/30/2015"
   ms.author="mwasson"/>

# Azure Redis Cache를 Python과 함께 사용하는 방법

이 항목에서는 Python을 사용하여 Azure Redis Cache를 시작하는 방법을 설명합니다.


## 필수 조건

[redis-py](https://github.com/andymccurdy/redis-py)를 설치합니다.


## Azure에 Redis 캐시 만들기

[Azure 관리 포털 미리 보기](http://go.microsoft.com/fwlink/?LinkId=398536)에서 **새로 만들기**, **데이터 + 저장소**를 클릭하고 **Redis Cache**를 선택합니다.

  ![][1]

DNS 호스트 이름을 입력합니다. 양식은 `<name>.redis.cache.windows.net`입니다. **만들기**를 클릭합니다.

  ![][2]

캐시가 만들어졌으면 포털에서 캐시를 클릭하여 캐시 설정을 확인합니다. 다음 정보가 필요합니다.

- **호스트 이름.** 캐시를 만들 때 입력한 이름입니다.
- **포트.** 포트를 보려면 **포트** 아래 링크를 클릭합니다. SSL 포트를 사용합니다.
- **액세스 키.** **키** 아래 링크를 클릭하고 기본 키를 복사합니다.

## 캐시에 항목 추가 및 검색

    >>> import redis
    >>> r = redis.StrictRedis(host='<name>.redis.cache.windows.net',
          port=6380, db=0, password='<key>', ssl=True)
    >>> r.set('foo', 'bar')
    True
    >>> r.get('foo')
    b'bar'

*&lt;name&gt;*을 캐시 이름으로, *&lt;key&gt;*를 액세스 키로 바꿉니다.


<!--Image references-->
[1]: ./media/cache-python-get-started/cache01.png
[2]: ./media/cache-python-get-started/cache02.png

<!---HONumber=July15_HO1-->