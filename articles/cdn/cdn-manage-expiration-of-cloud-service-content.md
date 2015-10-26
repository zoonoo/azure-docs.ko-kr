<properties 
 pageTitle="Azure CDN(콘텐츠 배달 네트워크)에서 클라우드 서비스 콘텐츠의 만료를 관리하는 방법" 
 description="" 
 services="cdn" 
 documentationCenter=".NET" 
 authors="zhangmanling" 
 manager="dwrede" 
 editor=""/>
<tags 
 ms.service="cdn" 
 ms.workload="media" 
 ms.tgt_pltfrm="na" 
 ms.devlang="dotnet" 
 ms.topic="article" 
 ms.date="09/01/2015" 
 ms.author="mazha"/>

#Azure CDN(콘텐츠 배달 네트워크)에서 클라우드 서비스 콘텐츠의 만료를 관리하는 방법

Azure CDN 캐싱이 가장 도움이 되는 개체는 TTL(Time-To-Live) 기간 중 자주 액세스되는 Blob입니다. 개체는 TTL 기간 동안 캐시에 유지되고 해당 기간이 경과된 후 클라우드 서비스에서 새로 고쳐집니다. 그런 다음 프로세스가 반복됩니다.

캐시 값을 제공하지 않을 경우 개체의 TTL은 7일입니다.

이미지 및 스타일시트와 같은 정적 콘텐츠의 경우 콘텐츠가 들어 있는 CDN 폴더에 web.config를 포함하고 콘텐츠에 대한 Cache-Control 헤더를 제어하는 **clientCache** 설정을 수정하여 업데이트 빈도를 제어할 수 있습니다. web.config 설정은 더 아래의 다른 하위 폴더에서 재정의되지 않은 경우 폴더 및 모든 하위 폴더의 항목에 영향을 줍니다. 예를 들어 루트에서 모든 정적 콘텐츠가 3일간 캐시되지만 캐시 설정이 6시간인 추가 변수 콘텐츠가 포함된 하위 폴더가 있도록 기본 TTL(Time-To-Live)을 설정할 수 있습니다.

다음 XML은 최대 보존 기간을 3일로 지정하는 **clientCache** 설정 예제를 보여 줍니다.

	<configuration> 
	  <system.webServer> 
	        <staticContent> 
	            <clientCache cacheControlMode="UseMaxAge" cacheControlMaxAge="3.00:00:00" /> 
	        </staticContent> 
	  </system.webServer> 
	</configuration>

**UseMaxAge**를 지정하면 **CacheControlMaxAge** 특성에 지정된 값에 따라 Cache-Control: max-age=<nnn> 헤더가 응답에 추가됩니다. **cacheControlMaxAge** 특성에 대한 시간 범위의 형식은 <days>.<hours>:<min>:<sec>입니다. **clientCache** 노드에 대한 자세한 내용은 [클라이언트 캐시 <clientCache>](http://www.iis.net/ConfigReference/system.webServer/staticContent/clientCache)(영문)를 참조하세요.

응용 프로그램에서 반환된 콘텐츠(예: .aspx 페이지)의 경우 **HttpResponse.Cache** 속성을 설정하여 프로그래밍 방식으로 CDN 캐싱 동작을 설정할 수 있습니다. **HttpResponse.Cache** 속성에 대한 자세한 내용은 [HttpResponse.Cache 속성](http://msdn.microsoft.com/library/system.web.httpresponse.cache.aspx) 및 [HttpCachePolicy 클래스](http://msdn.microsoft.com/library/system.web.httpcachepolicy.aspx)를 참조하세요.

프로그래밍 방식으로 응용 프로그램 콘텐츠를 캐시하려는 경우 HttpCacheability를 *Public*으로 설정하여 콘텐츠가 캐시 가능으로 표시되었는지 확인합니다. 또한 캐시 유효성 검사기가 설정되었는지 확인합니다. 캐시 유효성 검사기는 SetLastModified를 호출하여 설정된 마지막으로 수정한 날짜 타임스탬프 또는 SetETag를 호출하여 설정된 etag 값일 수 있습니다. 필요에 따라, SetExpires를 호출하여 캐시 만료 시간을 지정하거나 이 문서의 앞부분에서 설명한 기본 캐시 추론을 사용할 수도 있습니다.

예를 들어 1시간 동안의 콘텐츠를 캐시하려면 다음을 추가합니다.

            // Set the caching parameters.
            Response.Cache.SetExpires(DateTime.Now.AddHours(1));
            Response.Cache.SetCacheability(HttpCacheability.Public);
            Response.Cache.SetLastModified(DateTime.Now);

##참고 항목

[Azure CDN(콘텐츠 배달 네트워크)에서 Blob 콘텐츠의 만료를 관리하는 방법](./cdn-manage-expiration-of-blob-content.md)

<!---HONumber=Oct15_HO3-->