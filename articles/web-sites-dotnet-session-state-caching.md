<properties urlDisplayName="index" pageTitle="Azure 웹 사이트에서 ASP.NET 세션 상태 사용" metaKeywords="azure cache service session state" description="Learn how to use the Azure Cache Service to support ASP.NET session state caching." metaCanonical="" services="cache" documentationCenter=".NET" title="How to Use ASP.NET Session State with Azure Websites" authors="riande"  solutions="" manager="wpickett" editor="mollybos"  />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="10/13/2014" ms.author="riande" />


# Azure 웹 사이트에서 ASP.NET 세션 상태를 사용하는 방법

*작성자: [Rick Anderson](https://twitter.com/RickAndMSFT). 업데이트: 2014년 7월 1일.*

이 항목에서는 세션 상태에 Azure Redis 캐시 서비스(미리 보기)를 사용하는 방법을 설명합니다.

ASP.NET 웹 앱에서 세션 상태를 사용하는 경우 외부 세션 상태 공급자(Redis 캐시 서비스 또는 SQL Server 세션 상태 공급자)를 구성해야 합니다. 세션 상태를 사용하는 경우 외부 공급자를 사용하지 않으면 웹 앱의 인스턴스가 하나로 제한됩니다. Redis 캐시 서비스는 가장 빠르고 간편하게 사용하도록 설정할 수 있습니다.

세션 상태 캐싱에 캐시 서비스(사전 검토)를 사용하는 기본 단계는 다음과 같습니다.

* [캐시를 만듭니다.](#createcache)
* [웹 앱에 RedisSessionStateProvider NuGet 패키지를 추가합니다.](#configureproject)
* [web.config 파일을 수정합니다.](#configurewebconfig)
* [Session 개체를 사용하여 캐시된 항목을 저장 및 검색합니다.](#usesessionobject)

<h2><a id="createcache"></a>캐시 만들기</h2>
캐시를 만들려면 [다음 지침](http://azure.microsoft.com/ko-kr/documentation/articles/cache-dotnet-how-to-use-azure-redis-cache/#create-cache)을 따르세요.

<h2><a id="configureproject"></a>웹 앱에 RedisSessionStateProvider NuGet 패키지를 추가합니다.</h2>
NuGet 'RedisSessionStateProvider' 패키지를 설치합니다.  다음 명령을 사용하여 패키지 관리자 콘솔에서 설치합니다(**도구** > **NuGet 패키지 관리자** > **패키지 관리자 콘솔**).

  `PM> Install-Package RedisSessionStateProvider -IncludePrerelease`
  
**도구** > **NuGet 패키지 관리자** > **솔루션의 NuGet 패키지 관리**에서 설치하려면 `RedisSessionStateProvider`를 검색하고 **시험판 포함**을 지정해야 합니다.

자세한 내용은 [NuGet RedisSessionStateProvider 페이지](http://www.nuget.org/packages/Microsoft.Web.RedisSessionStateProvider/ )(영문) 및 [캐시 클라이언트 구성](http://azure.microsoft.com/ko-kr/documentation/articles/cache-dotnet-how-to-use-azure-redis-cache/#NuGet)을 참조하세요.

<h2><a id="configurewebconfig"></a>Web.Config 파일 수정</h2>
NuGet 패키지는 캐시에 대한 어셈블리 참조를 만들 뿐 아니라 *web.config* 파일에 스텁 항목을 추가합니다. 

1. *web.config*를 열고 **sessionState** 요소를 찾습니다.

1. `host`, `accessKey`, `port`(SSL 포트는 6380이어야 함)의 값을 입력하고 `SSL`을 `true`로 설정합니다. 이러한 값은 캐시 인스턴스에 대한 Azure 관리 미리 보기 포털 블레이드에서 가져올 수 있습니다. 자세한 내용은 [캐시에 연결](http://azure.microsoft.com/ko-kr/documentation/articles/cache-dotnet-how-to-use-azure-redis-cache/#connect-to-cache)을 참조하세요.
다음 태그는 *web.config* 파일에 변경 내용을 표시합니다.


  <pre class="prettyprint">  
    <system.web>
    <customErrors mode="Off" />
    <authentication mode="None" />
    <compilation debug="true" targetFramework="4.5" />
    <httpRuntime targetFramework="4.5" />
  <sessionState mode="Custom" customProvider="RedisSessionProvider">
      <providers>  
          <!--<add name="RedisSessionProvider" 
            host = "127.0.0.1" [String]
            port = "" [number]
            accessKey = "" [String]
            ssl = "false" [true|false]
            throwOnError = "true" [true|false]
            retryTimeoutInMilliseconds = "0" [number]
            databaseId = "0" [number]
            applicationName = "" [String]
          />-->
         <add name="RedisSessionProvider" 
              type="Microsoft.Web.Redis.RedisSessionStateProvider" 
              <mark>port="6380"
              host="movie2.redis.cache.windows.net" 
              accessKey="m7PNV60CrvKpLqMUxosC3dSe6kx9nQ6jP5del8TmADk=" 
              ssl="true"</mark> />
      <!--<add name="MySessionStateStore" type="Microsoft.Web.Redis.RedisSessionStateProvider" host="127.0.0.1" accessKey="" ssl="false" />-->
      </providers>
    </sessionState>
  </system.web></pre>


<h2><a id="usesessionobject"></a>코드에 Session 개체 사용</h2>
마지막 단계는 ASP.NET 코드에서 Session 개체 사용을 시작하는 것입니다. **Session.Add** 메서드를 사용하여 세션 상태에 개체를 추가합니다. 이 메서드는 키-값 쌍을 사용하여 세션 상태 캐시에 항목을 저장합니다.

    string strValue = "yourvalue";
	Session.Add("yourkey", strValue);

다음 코드를 사용하면 세션 상태에서 이 값이 검색됩니다.

    object objValue = Session["yourkey"];
    if (objValue != null)
       strValue = (string)obj;	

Redis 캐시를 사용하여 웹 앱에서 개체를 캐시할 수도 있습니다. 자세한 내용은 [Azure Redis 캐시를 사용한 MVC 동영상 앱(15분)](http://azure.microsoft.com/blog/2014/06/05/mvc-movie-app-with-azure-redis-cache-in-15-minutes/)(영문)을 참조하세요.
ASP.NET 세션 상태 사용 방법에 대한 자세한 내용은 [ASP.NET 세션 상태 개요][]를 참조하세요.

  
  
  [최신 설치]: http://www.windowsazure.com/ko-kr/downloads/?sdk=net  
  [ASP.NET 세션 상태 개요]: http://msdn.microsoft.com/ko-kr/library/ms178581.aspx

  [NewIcon]: ./media/web-sites-dotnet-session-state-caching/CacheScreenshot_NewButton.png
  [NewCacheDialog]: ./media/web-sites-dotnet-session-state-caching/CachingScreenshot_CreateOptions.png
  [CacheIcon]: ./media/web-sites-dotnet-session-state-caching/CachingScreenshot_CacheIcon.png
  [NuGetDialog]: ./media/web-sites-dotnet-session-state-caching/CachingScreenshot_NuGet.png
  [OutputConfig]: ./media/web-sites-dotnet-session-state-caching/CachingScreenshot_OC_WebConfig.png
  [CacheConfig]: ./media/web-sites-dotnet-session-state-caching/CachingScreenshot_CacheConfig.png
  [EndpointURL]: ./media/web-sites-dotnet-session-state-caching/CachingScreenshot_EndpointURL.png
  [ManageKeys]: ./media/web-sites-dotnet-session-state-caching/CachingScreenshot_ManageAccessKeys.png
