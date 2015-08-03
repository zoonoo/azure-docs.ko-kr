<properties 
   pageTitle="Azure Redis Cache를 사용하는 일반적인 캐시 패턴" 
   description="Azure Redis Cache를 사용하는 위치 및 이유를 알아봅니다." 
   services="redis-cache" 
   documentationCenter="" 
   authors="Rick-Anderson" 
   manager="wpickett" 
   editor=""/>

<tags
   ms.service="cache"
   ms.devlang="all"
   ms.topic="article"
   ms.tgt_pltfrm="cache-redis"
   ms.workload="tbd" 
   ms.date="05/21/2015"
   ms.author="riande"/>

# Azure Redis Cache를 사용하는 일반적인 캐시 패턴

이 페이지에서는 캐시 사용의 가장 일반적인 이점을 보여 줍니다.

## 캐시를 사용하여 데이터 액세스 최적화

캐시를 사용하면 데이터 저장소에서 가져오는 것보다 데이터 액세스 속도를 훨씬 향상시킬 수 있습니다. 캐시는 높은 처리량과 낮은 대기 시간을 제공합니다. 캐시에서 핫 데이터를 가져오면 앱 속도가 빨라질 뿐 아니라 데이터 액세스 부하를 줄이고 다른 쿼리에 대한 응답성을 증가시킬 수 있습니다. 캐시에 정보를 저장하면 리소스 절약에 도움이 되며 응용 프로그램의 수요가 증가할 때 확장성이 향상됩니다. 앱이 캐시에서 데이터를 효율적으로 가져올 수 있을 경우 폭증하는 부하에 대한 앱의 응답성이 훨씬 향상됩니다.

## 분산 세션 상태
세션 상태를 사용하지 않는 것이 모범 사례로 간주되지만 일부 응용 프로그램은 실제로 세션 데이터를 사용하여 성능을 향상시키고 복잡성을 줄이는 이점을 얻을 수 있으며, 명백하게 세션 상태가 필요한 앱도 있습니다. 세션 상태에 대한 기본 메모리 내 공급자는 규모 확장(웹 사이트의 여러 인스턴스 실행)을 허용하지 않습니다. ASP.NET SQL Server 세션 상태 공급자는 여러 웹 사이트가 세션 상태를 사용할 수 있도록 허용하지만 메모리 내 공급자에 비해 높은 대기 시간 비용이 발생합니다. Redis 세션 상태 캐시 공급자는 구성 및 설정이 매우 용이하고 대기 시간이 낮은 대안입니다. 앱이 제한적으로만 세션 상태를 사용하는 경우 대부분의 캐시를 데이터 캐싱에 사용하고 적은 양의 캐시를 세션 상태에 사용할 수 있습니다.

## 서비스 가동 중지 시간 후 정상 작동(캐시 대체)
 캐시에 데이터를 저장하면 응용 프로그램이 네트워크 대기 시간, 웹 서비스 문제 및 하드웨어 오류와 같은 시스템 오류가 발생해도 정상적으로 작동할 수 있습니다. 대체로 앱이 완전히 실패하는 것보다 웹 서비스나 데이터베이스가 복구될 때까지 캐시된 데이터를 제공하는 것이 더 낫습니다.

## 다음 단계
Azure Redis Cache 사용에 대해 자세히 알아보려면 다음을 참조하세요.
 
- [Redis Azure Cache 문서](http://azure.microsoft.com/documentation/services/cache/)(영문): 이 페이지에서는 Redis Azure Cache 사용에 유용한 많은 링크를 제공합니다.
- [15분 내에 Azure Redis Cache를 사용하여 MVC 동영상 앱 실행](http://azure.microsoft.com/blog/2014/06/05/mvc-movie-app-with-azure-redis-cache-in-15-minutes/)(영문): 이 블로그 게시물에서는 ASP.NET MVC 앱에서 빠르게 Azure Redis Cache를 사용하는 방법을 알려줍니다.
- [Azure 웹 사이트에서 ASP.NET 세션 상태를 사용하는 방법](../app-service-web/web-sites-dotnet-session-state-caching.md)(영문): 이 항목에서는 세션 상태에 Azure Redis Cache 서비스를 사용하는 방법을 설명합니다.

<!---HONumber=July15_HO4-->