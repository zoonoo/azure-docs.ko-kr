<properties 
	pageTitle="Azure 앱 서비스에서 Azure Redis 캐시를 사용하는 세션 상태" 
	description="Azure 캐시 서비스를 사용하여 ASP.NET 세션 상태 캐싱을 지원하는 방법에 대해 설명합니다." 
	services="app-service" 
	documentationCenter=".net" 
	authors="Rick-Anderson" 
	manager="wpickett" 
	editor="jimbe"/>

<tags 
	ms.service="app-service" 
	ms.workload="na" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="09/16/2015" 
	ms.author="riande"/>


# Azure 앱 서비스에서 Azure Redis 캐시를 사용하는 세션 상태


이 항목에서는 세션 상태에 Azure Redis 캐시 서비스를 사용하는 방법을 설명합니다.

ASP.NET 웹 앱에서 세션 상태를 사용하는 경우 외부 세션 상태 공급자(Redis 캐시 서비스 또는 SQL Server 세션 상태 공급자)를 구성해야 합니다. 세션 상태를 사용하는 경우 외부 공급자를 사용하지 않으면 웹 앱의 인스턴스가 하나로 제한됩니다. Redis 캐시 서비스는 가장 빠르고 간편하게 사용하도록 설정할 수 있습니다.

[AZURE.INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)]

##<a id="createcache"></a>캐시 만들기
캐시를 만들려면 [다음 지침](../cache-dotnet-how-to-use-azure-redis-cache.md#create-cache)을 따르세요.

##<a id="configureproject"></a>웹앱에 RedisSessionStateProvider NuGet 패키지 추가
NuGet `RedisSessionStateProvider` 패키지를 설치합니다. 다음 명령을 사용하여 패키지 관리자 콘솔에서 설치합니다(**도구** > **NuGet 패키지 관리자** > **패키지 관리자 콘솔**):

  `PM> Install-Package Microsoft.Web.RedisSessionStateProvider`
  
**도구** > **NuGet 패키지 관리자** > **솔루션의 NuGet 패키지 관리**에서 설치하려면 `RedisSessionStateProvider`를 검색합니다.

자세한 내용은 [NuGet RedisSessionStateProvider 페이지](http://www.nuget.org/packages/Microsoft.Web.RedisSessionStateProvider/) 및 [캐시 클라이언트 구성](../cache-dotnet-how-to-use-azure-redis-cache.md#NuGet)을 참조하세요.

##<a id="configurewebconfig"></a>Web.Config 파일 수정
NuGet 패키지는 캐시에 대한 어셈블리 참조를 만들 뿐 아니라 *web.config* 파일에 스텁 항목을 추가합니다.

1. *web.config*를 열고 **sessionState** 요소를 찾습니다.

1. `host`, `accessKey`, `port`(SSL 포트는 6380이어야 함) 값을 입력하고 `SSL`를 `true`로 설정합니다. 이러한 값은 캐시 인스턴스에 대한 [Azure 포털](http://go.microsoft.com/fwlink/?LinkId=529715) 블레이드에서 가져올 수 있습니다. 자세한 내용은 [캐시에 연결](../cache-dotnet-how-to-use-azure-redis-cache.md#connect-to-cache)을 참조하세요. 비 SSL 포트는 기본적으로 새 캐시에 대해 사용하지 않도록 설정됩니다. 비 SSL 포트를 사용하도록 설정하기에 대한 자세한 내용은 [Azure Redis 캐시에서 캐시 구성](https://msdn.microsoft.com/library/azure/dn793612.aspx) 항목의 [액세스 포트](https://msdn.microsoft.com/library/azure/dn793612.aspx#AccessPorts) 섹션을 참조하세요. 다음 태그는 *web.config* 파일에 대한 변경 내용, 특히 *port*, *host*, accessKey* 및 *ssl*에 대한 변경 내용을 보여 줍니다.

		  <system.web>;
		    <customErrors mode="Off" />;
		    <authentication mode="None" />;
		    <compilation debug="true" targetFramework="4.5" />;
		    <httpRuntime targetFramework="4.5" />;
		    <sessionState mode="Custom" customProvider="RedisSessionProvider">;
		      <providers>;  
		          <!--<add name="RedisSessionProvider" 
		            host = "127.0.0.1" [String]
		            port = "" [number]
		            accessKey = "" [String]
		            ssl = "false" [true|false]
		            throwOnError = "true" [true|false]
		            retryTimeoutInMilliseconds = "0" [number]
		            databaseId = "0" [number]
		            applicationName = "" [String]
		          />;-->;
		         <add name="RedisSessionProvider" 
		              type="Microsoft.Web.Redis.RedisSessionStateProvider" 
		              port="6380"
		              host="movie2.redis.cache.windows.net" 
		              accessKey="m7PNV60CrvKpLqMUxosC3dSe6kx9nQ6jP5del8TmADk=" 
		              ssl="true" />;
		      <!--<add name="MySessionStateStore" type="Microsoft.Web.Redis.RedisSessionStateProvider" host="127.0.0.1" accessKey="" ssl="false" />;-->;
		      </providers>;
		    </sessionState>;
		  </system.web>;


##<a id="usesessionobject"></a> 코드에서 Session 개체 사용
마지막 단계는 ASP.NET 코드에서 Session 개체 사용을 시작하는 것입니다. **Session.Add** 메서드를 사용하여 세션 상태에 개체를 추가합니다. 이 메서드는 키-값 쌍을 사용하여 세션 상태 캐시에 항목을 저장합니다.

    string strValue = "yourvalue";
	Session.Add("yourkey", strValue);

다음 코드를 사용하면 세션 상태에서 이 값이 검색됩니다.

    object objValue = Session["yourkey"];
    if (objValue != null)
       strValue = (string)objValue;	

Redis 캐시를 사용하여 웹 앱에서 개체를 캐시할 수도 있습니다. 자세한 내용은 [Azure Redis 캐시를 사용한 MVC 동영상 앱(15분)](http://azure.microsoft.com/blog/2014/06/05/mvc-movie-app-with-azure-redis-cache-in-15-minutes/)(영문)을 참조하세요. ASP.NET 세션 상태 사용 방법에 대한 자세한 내용은 [ASP.NET 세션 상태 개요][]를 참조하세요.

>[AZURE.NOTE]Azure 계정을 등록하기 전에 Azure 앱 서비스를 시작하려면 [앱 서비스 평가](http://go.microsoft.com/fwlink/?LinkId=523751)로 이동합니다. 앱 서비스에서 단기 스타터 웹 앱을 즉시 만들 수 있습니다. 신용 카드는 필요하지 않으며 약정도 필요하지 않습니다.

## 변경된 내용
* 웹 사이트에서 앱 서비스로의 변경에 대한 지침은 [Azure 앱 서비스와 이 서비스가 기존 Azure 서비스에 미치는 영향](http://go.microsoft.com/fwlink/?LinkId=529714)을 참조하세요.
* 이전 포털에서 새 포털로의 변경에 대한 지침은 [미리 보기 포털 탐색에 대한 참조](http://go.microsoft.com/fwlink/?LinkId=529715)를 참조하세요.

  *작성자: [Rick Anderson](https://twitter.com/RickAndMSFT)*
  
  [installed the latest]: http://www.windowsazure.com/downloads/?sdk=net
  [ASP.NET 세션 상태 개요]: http://msdn.microsoft.com/library/ms178581.aspx

  [NewIcon]: ./media/web-sites-dotnet-session-state-caching/CacheScreenshot_NewButton.png
  [NewCacheDialog]: ./media/web-sites-dotnet-session-state-caching/CachingScreenshot_CreateOptions.png
  [CacheIcon]: ./media/web-sites-dotnet-session-state-caching/CachingScreenshot_CacheIcon.png
  [NuGetDialog]: ./media/web-sites-dotnet-session-state-caching/CachingScreenshot_NuGet.png
  [OutputConfig]: ./media/web-sites-dotnet-session-state-caching/CachingScreenshot_OC_WebConfig.png
  [CacheConfig]: ./media/web-sites-dotnet-session-state-caching/CachingScreenshot_CacheConfig.png
  [EndpointURL]: ./media/web-sites-dotnet-session-state-caching/CachingScreenshot_EndpointURL.png
  [ManageKeys]: ./media/web-sites-dotnet-session-state-caching/CachingScreenshot_ManageAccessKeys.png
 

<!---HONumber=Oct15_HO1-->