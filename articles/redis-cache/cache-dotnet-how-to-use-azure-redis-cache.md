<properties 
	pageTitle="Azure Redis Cache 사용 방법" 
	description="Azure Redis Cache를 사용하여 Azure 응용 프로그램의 성능을 향상시키는 방법을 알아봅니다." 
	services="redis-cache,app-service" 
	documentationCenter="" 
	authors="steved0x" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="cache" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="cache-redis" 
	ms.devlang="dotnet" 
	ms.topic="hero-article" 
	ms.date="09/30/2015" 
	ms.author="sdanie"/>

# Azure Redis Cache 사용 방법

이 가이드에서는 **Azure Redis Cache**를 사용하는 방법을 설명합니다. 샘플은 C# 코드로 작성되며 [StackExchange.Redis][] 클라이언트를 사용합니다. 적용되는 시나리오에는 **캐시 만들기 및 구성**, **캐시 클라이언트 구성**, **캐시에서 개체 추가 및 제거**, **캐시에 ASP.NET 세션 상태 저장** 등이 포함됩니다. Azure Redis Cache 사용에 대한 자세한 내용은 [다음 단계][] 섹션을 참조하세요.

<a name="what-is"></a>
## Azure Redis Cache란?

Microsoft Azure Redis Cache는 많이 사용되는 오픈 소스 Redis Cache를 기반으로 합니다. Microsoft에서 관리하는 안전한 전용 Redis Cache에 액세스할 수 있게 합니다. Azure Redis 캐시를 사용하여 만들어진 캐시는 Microsoft Azure 내의 모든 응용 프로그램에서 액세스할 수 있습니다.

Microsoft Azure Redis 캐시는 다음 계층에서 사용할 수 있습니다.

-	**기본** – 단일 노드. 최대 53GB까지 여러 개의 크기
-	**표준** – 2노드 주/복제본. 최대 53GB까지 여러 개의 크기 99.9% SLA
-	**프리미엄** – 현재 미리 보기 상태입니다. 최대 10개 분할 데이터베이스와 2노드 주/복제본 6GB에서 530GB에 이르는 다양한 크기(자세한 내용 문의). 모든 표준 계층 기능과 추가적인 [Redis 클러스터](cache-how-to-premium-clustering.md), [Redis 지속성](cache-how-to-premium-persistence.md) 및 [Azure 가상 네트워크](cache-how-to-premium-vnet.md) 지원이 포함됩니다. 미리 보기 기간 동안 SLA는 없습니다.

각 계층은 기능과 가격이 다릅니다. 기능에 대해서는 이 가이드의 뒷부분에서 다룹니다. 가격에 대한 자세한 내용은 [캐시 가격 정보][]를 참조하세요.

이 가이드에서는 Azure Redis 캐시를 시작하기 위한 개요를 제공합니다. 이 시작하기 가이드의 범위를 벗어나는 기능에 대해 자세히 알아보려면 [Azure Redis 캐시 개요][]를 참조하세요.

<a name="getting-started-cache-service"></a>
## Azure Redis Cache 시작

Azure Redis 캐시를 시작하기는 쉽습니다. 먼저 캐시를 프로비전하고 구성합니다. 그런 다음 캐시에 액세스할 수 있도록 캐시 클라이언트를 구성합니다. 캐시 클라이언트를 구성하면 작업을 시작할 수 있습니다.

-	[캐시 만들기][]
-	[캐시 클라이언트 구성][]

<a name="create-cache"></a>
## 캐시 만들기

캐시를 만들려면 먼저 [Azure 미리 보기 포털][]에 로그인하고 **새로 만들기**, **데이터 + 저장소**, **Redis Cache**를 클릭합니다.

![새 캐시][NewCacheMenu]

>[AZURE.NOTE]Azure 계정이 없는 경우 몇 분 만에 무료 평가판 계정을 만들 수 있습니다. 자세한 내용은 [Azure 무료 체험][]을 참조하세요.

**새 Redis Cache** 블레이드에서 원하는 캐시 구성을 지정합니다.

![캐시 만들기][CacheCreate]

**DNS 이름** - 캐시 끝점에 사용할 하위 도메인 이름을 입력합니다. 끝점은 6~20자 사이의 문자열이어야 하며, 소문자와 숫자만 포함할 수 있고, 문자로 시작해야 합니다.

**가격 계층**을 사용하여 원하는 캐시 크기 및 기능을 선택합니다.

**리소스 그룹**에서 캐시에 대한 리소스 그룹을 선택하거나 만듭니다.

>[AZURE.NOTE]자세한 내용은 [리소스 그룹을 사용하여 Azure 리소스 관리][](영문)를 참조하세요.

**구독**에서는 캐시에 사용할 Azure 구독을 선택합니다. 계정에 구독이 하나뿐인 경우 해당 구독이 자동으로 선택되며 **구독** 드롭다운은 표시되지 않습니다.

**위치**를 사용하여 캐시가 호스트되는 지리적 위치를 지정합니다. 성능을 최적화하려면 캐시 클라이언트 응용 프로그램과 동일한 지역에 캐시를 만드는 것이 좋습니다.

새 캐시 옵션을 구성했으면 **만들기**를 클릭합니다. 캐시를 만드는 데 몇 분 정도 걸릴 수 있습니다. 상태를 확인하려면 시작 보드에서 진행률을 모니터링하면 됩니다. 캐시가 생성되면 새로운 캐시는 **Running** 상태가 되며, 기본 설정을 이용해 바로 사용할 수 있습니다.

![캐시 만듬][CacheCreated]

캐시가 만들어지면 **찾아보기** 블레이드에서 캐시에 액세스할 수 있습니다.

![찾아보기 블레이드][BrowseCaches]

**Redis Caches**를 클릭하여 캐시를 확인합니다.

![캐시][Caches]

<a name="NuGet"></a>
## 캐시 클라이언트 구성

Azure Redis Cache를 사용하여 만든 캐시는 모든 Azure 응용 프로그램에서 액세스할 수 있습니다. Visual Studio에서 개발한 .NET 응용 프로그램은 **StackExchange.Redis** 캐시 클라이언트를 사용할 수 있습니다. 이는 캐시 클라이언트 응용 프로그램의 구성을 단순화하는 NuGet 패키지를 사용하여 구성할 수 있습니다.

>[AZURE.NOTE]자세한 내용은 [StackExchange.Redis][](영문) github 페이지 및 [StackExchange.Redis 캐시 클라이언트 설명서][](영문)를 참조하세요.

StackExchange.Redis NuGet 패키지를 사용하여 Visual Studio에서 클라이언트 응용 프로그램을 구성하려면 **솔루션 탐색기**에서 프로젝트를 마우스 오른쪽 단추로 클릭하고 **NuGet 패키지 관리**를 선택합니다.

![NuGet 패키지 관리][NuGetMenu]

**온라인 검색** 텍스트 상자에 **StackExchange.Redis** 또는 **StackExchange.Redis.StrongName**을 입력하고 결과에서 원하는 버전을 선택한 후 **설치**를 클릭합니다.

>[AZURE.NOTE]강력한 이름의 **StackExchange.Redis** 클라이언트 라이브러리 버전을 사용하려면 **StackExchange.Redis.StrongName**을 선택하고 그렇지 않으면 **StackExchange.Redis**를 선택합니다.

![StackExchange.Redis NuGet 패키지][StackExchangeNuget]

NuGet 패키지는 클라이언트 응용 프로그램이 StackExchange.Redis 캐시 클라이언트를 사용하여 Azure Redis Cache에 액세스하는 데 필요한 어셈블리 참조를 다운로드하고 추가합니다.

클라이언트 프로젝트의 캐싱을 구성했으면 캐시 작업에 대해 다음 섹션에서 설명하는 기술을 사용할 수 있습니다.

<a name="working-with-caches"></a>
## 캐시 작업

이 섹션의 각 단계에서는 일반적인 캐시 작업 수행 방법에 대해 설명합니다.

-	[캐시에 연결][]
-   [캐시에서 개체 추가 및 검색][]
-   [캐시에 ASP.NET 세션 상태 저장][]

<a name="connect-to-cache"></a>
## 캐시에 연결

프로그래밍 방식으로 캐시 작업을 하려면 캐시에 대한 참조가 필요합니다. StackExchange.Redis 클라이언트를 사용하여 Azure Redis Cache에 액세스하는 위치가 되는 임의의 파일 맨 위에 다음을 추가합니다.

    using StackExchange.Redis;

>[AZURE.NOTE]StackExchange.Redis 클라이언트를 사용하려면 .NET Framework 4 이상이 필요합니다.

Azure Redis Cache 연결은 `ConnectionMultiplexer` 클래스로 관리됩니다. 이 클래스는 클라이언트 응용 프로그램 전체에서 공유하고 다시 사용하도록 설계되었으며 작업별로 만들 필요가 없습니다.

Azure Redis Cache에 연결하고 연결된 `ConnectionMultiplexer` 인스턴스를 반환하려면 다음 예제와 같이 정적 `Connect` 메서드를 호출하여 캐시 끝점 및 키에 전달합니다. 미리 보기 포털에서 생성된 Azure 키를 암호 매개 변수로 사용합니다.

	ConnectionMultiplexer connection = ConnectionMultiplexer.Connect("contoso5.redis.cache.windows.net,abortConnect=false,ssl=true,password=...");

>[AZURE.IMPORTANT]경고: 소스 코드에 자격 증명을 저장해서는 안 됩니다. 이 샘플을 단순하게 유지하기 위해 소스 코드로 표시합니다. [응용 프로그램 설정 및 연결 문자열 작동 방식][](영문)에서 자격 증명 저장 방법에 대해 자세히 알아보세요.

SSL을 사용하지 않으려면 `ssl=false`를 설정하거나 `ssl` 매개 변수를 생략합니다.

>[AZURE.NOTE]비 SSL 포트는 기본적으로 새 캐시에 대해 사용하지 않도록 설정됩니다. 비 SSL 포트를 사용하도록 설정하는 방법에 대한 자세한 내용은 [Azure Redis Cache에서 캐시 구성][] 항목의 액세스 포트 섹션을 참조하세요.

고급 연결 구성 옵션에 대한 자세한 내용은 [StackExchange.Redis 구성 모델][](영문)을 참조하세요.

캐시 끝점 및 키는 캐시 인스턴스에 대한 **Redis Cache** 블레이드에서 가져올 수 있습니다.

![캐시 속성][CacheProperties]

![키 관리][ManageKeys]

연결이 설정되고 나면 `ConnectionMultiplexer.GetDatabase` 메서드를 호출하여 Redis Cache 데이터베이스에 대한 참조를 반환합니다.

	// connection refers to a previously configured ConnectionMultiplexer
	IDatabase cache = connection.GetDatabase();

>[AZURE.NOTE]`GetDatabase` 메서드에서 반환된 개체는 경량의 통과 개체이며 저장할 필요가 없습니다.

	ConnectionMultiplexer connection = ConnectionMultiplexer.Connect("contoso5.redis.cache.windows.net,abortConnect=false,ssl=true,password=...");

	IDatabase cache = connection.GetDatabase();

	// Perform cache operations using the cache object...
	// Simple put of integral data types into the cache
	cache.StringSet("key1", "value");
	cache.StringSet("key2", 25);

	// Simple get of data types from the cache
	string key1 = cache.StringGet("key1");
	int key2 = (int)cache.StringGet("key2");

Azure Redis Cache 인스턴스에 연결하고 캐시 데이터베이스에 대한 참조를 반환하는 방법을 알아보았으며, 이제 캐시 작업에 대해 살펴보겠습니다.

<a name="add-object"></a>
## 캐시에서 개체 추가 및 검색

`StringSet` 및 `StringGet` 메서드를 사용하여 캐시에 항목을 저장하고 캐시에서 항목을 검색할 수 있습니다.

	// If key1 exists, it is overwritten.
	cache.StringSet("key1", "value1");

	string value = cache.StringGet("key1");

>[AZURE.NOTE]Redis는 대부분의 데이터를 Redis 문자열로 저장하지만, 이 문자열은 캐시에 .NET 개체를 저장할 때 사용할 수 있는 직렬화된 이진 데이터를 포함하여 다양한 데이터 유형을 포함할 수 있습니다.

`StringGet` 호출 시 개체가 있으면 반환되고 없으면 null이 반환됩니다. 이 경우에는 원하는 데이터 소스에서 값을 검색하여 이후에 사용할 수 있게 캐시에 저장할 수 있습니다. 이를 캐시 배제 패턴이라고 합니다.

    string value = cache.StringGet("key1");
    if (value == null)
    {
        // The item keyed by "key1" is not in the cache. Obtain
        // it from the desired data source and add it to the cache.
        value = GetValueFromDataSource();

        cache.StringSet("key1", value);
    }

>[AZURE.NOTE]Azure Redis 캐시는 .NET 개체 및 기본 데이터 형식을 캐시할 수 있습니다. 하지만 .NET 개체를 캐시하려면 먼저 직렬화해야 합니다. 이 과정은 응용 프로그램 개발자가 수행하며 이때 개발자는 유연하게 직렬 변환기를 선택할 수 있습니다. 자세한 내용은 [캐시의 .NET 개체 작업][](영문)을 참조하세요.

<a name="specify-expiration"></a>
## 캐시에서 항목의 만료 지정

캐시에서 항목의 만료를 지정하려면 `StringSet`의 `TimeSpan` 매개 변수를 사용합니다.

	cache.StringSet("key1", "value1", TimeSpan.FromMinutes(90));


<a name="store-session"></a>
## 캐시에 ASP.NET 세션 상태 저장

Azure Redis Cache는 메모리 내 또는 SQL Server 데이터베이스가 아니라 캐시에 세션 상태를 저장하는 데 사용할 수 있는 세션 상태 제공자를 제공합니다. 캐싱 세션 상태 제공자를 사용하려면 먼저 캐시를 구성하고 Redis Cache 세션 상태 NuGet 패키지를 사용하여 캐시용 ASP.NET 응용 프로그램을 구성합니다.

Redis 캐시 세션 상태 NuGet 패키지를 사용하여 Visual Studio에서 클라이언트 응용 프로그램을 구성하려면 **솔루션 탐색기**에서 프로젝트를 마우스 오른쪽 단추로 클릭하고 **NuGet 패키지 관리**를 선택합니다.

![NuGet 패키지 관리][NuGetMenu]

**온라인 검색** 텍스트 상자에 **RedisSessionStateProvider**를 입력하여 결과에서 선택한 후 **설치**를 클릭합니다.

![Redis 캐시 세션 상태 NuGet 패키지][SessionStateNuGet]

NuGet 패키지는 필요한 어셈블리 참조를 다운로드 및 추가하고 ASP.NET 응용 프로그램이 Redis 캐시 세션 상태 제공자를 사용하는 데 필요한 구성이 포함된 web.config 파일에 다음 섹션을 추가합니다.

    <sessionState mode="Custom" customProvider="MySessionStateStore">
      <providers>
        <!--
          <add name="MySessionStateStore" 
            host = "127.0.0.1" [String]
            port = "" [number]
            accessKey = "" [String]
            ssl = "false" [true|false]
            throwOnError = "true" [true|false]
            retryTimeoutInMilliseconds = "0" [number]
            databaseId = "0" [number]
            applicationName = "" [String]
            connectionTimeoutInMilliseconds = "5000" [number]
            operationTimeoutInMilliseconds = "5000" [number]
          />
        -->
        <add name="MySessionStateStore" type="Microsoft.Web.Redis.RedisSessionStateProvider" host="127.0.0.1" accessKey="" ssl="false" />
      </providers>
    </sessionState>

주석으로 처리된 섹션은 특성 예제 및 이에 대한 샘플 설정을 제공합니다.

미리 보기 포털의 캐시 블레이드에서 가져온 값을 사용하여 특성을 구성하고 원하는 대로 다른 값을 구성합니다.

	<sessionState mode="Custom" customProvider="MySessionStateStore">
      <providers>
        <!--
          <add name="MySessionStateStore" 
            host = "127.0.0.1" [String]
            port = "" [number]
            accessKey = "" [String]
            ssl = "false" [true|false]
            throwOnError = "true" [true|false]
            retryTimeoutInMilliseconds = "0" [number]
            databaseId = "0" [number]
            applicationName = "" [String]
            connectionTimeoutInMilliseconds = "5000" [number]
            operationTimeoutInMilliseconds = "5000" [number]
          />
        -->
        <add name="MySessionStateStore" type="Microsoft.Web.Redis.RedisSessionStateProvider" host="contoso5.redis.cache.windows.net" 
		accessKey="..." ssl="true" />
      </providers>
    </sessionState>

표준 **InProc** 세션 상태 공급자를 주석으로 처리해야 합니다.

    <!-- <sessionState mode="InProc" customProvider="DefaultSessionProvider">
      <providers>
        <add name="DefaultSessionProvider" type="System.Web.Providers.DefaultSessionStateProvider, System.Web.Providers, Version=1.0.0.0, Culture=neutral, PublicKeyToken=31bf3856ad364e35" connectionStringName="DefaultConnection" />
      </providers>
    </sessionState> -->

이러한 설정을 구성하고 Azure Redis 세션 상태 제공자를 사용하는 방법에 대한 자세한 내용은 [Azure Redis 세션 상태 제공자][]를 참조하세요.

<a name="next-steps"></a>
## 다음 단계

이제 Azure Redis Cache의 기본 사항을 배웠으므로 다음 링크를 따라 좀 더 복잡한 캐싱 작업을 수행하는 방법을 알아보세요.

-	캐시의 상태를 [cache-how-to-monitor.md)할 수 있도록 [캐시 진단을 사용하도록 설정](cache-how-to-monitor.md#enable-cache-diagnostics)합니다. 미리 보기 포털에서 메트릭을 볼 수 있으며 선택한 도구를 사용하여 메트릭을 [다운로드 및 검토](https://github.com/rustd/RedisSamples/tree/master/CustomMonitoring)할 수도 있습니다.
-	[StackExchange.Redis 캐시 클라이언트 설명서][](영문)를 확인하세요.
	-	Azure Redis Cache는 다양한 Redis 클라이언트와 개발 언어에서 액세스할 수 있습니다. 자세한 내용은 [http://redis.io/clients][](영문) 및 [Azure Redis Cache용으로 다른 언어에서 개발][]을 참조하세요.
	-	Redsmin과 같은 서비스와 함께 Azure Redis Cache를 사용할 수도 있습니다. 자세한 내용은 [Azure Redis 연결 문자열을 검색하고 Redsmin과 함께 사용하는 방법][](영문)을 참조하세요.
-	[redis][](영문) 설명서를 참조하고 [redis 데이터 형식][](영문) 및 [Redis 데이터 형식에 대한 15분 소개][](영문)에 대해 읽어 보세요.
-   [Azure Redis Cache][]에 대한 MSDN 참조를 확인하세요. 


<!-- INTRA-TOPIC LINKS -->
[다음 단계]: #next-steps
[Introduction to Azure Redis Cache (Video)]: #video
[What is Azure Redis Cache?]: #what-is
[Create an Azure Cache]: #create-cache
[Which type of caching is right for me?]: #choosing-cache
[Prepare Your Visual Studio Project to Use Azure Caching]: #prepare-vs
[Configure Your Application to Use Caching]: #configure-app
[Get Started with Azure Redis Cache]: #getting-started-cache-service
[캐시 만들기]: #create-cache
[Configure the cache]: #enable-caching
[캐시 클라이언트 구성]: #NuGet
[Working with Caches]: #working-with-caches
[캐시에 연결]: #connect-to-cache
[캐시에서 개체 추가 및 검색]: #add-object
[Specify the expiration of an object in the cache]: #specify-expiration
[캐시에 ASP.NET 세션 상태 저장]: #store-session

  
<!-- IMAGES -->
[NewCacheMenu]: ./media/cache-dotnet-how-to-use-azure-redis-cache/redis-cache-new-cache-menu.png

[CacheCreate]: ./media/cache-dotnet-how-to-use-azure-redis-cache/redis-cache-cache-create.png

[StackExchangeNuget]: ./media/cache-dotnet-how-to-use-azure-redis-cache/redis-cache-stackexchange-redis.png

[NuGetMenu]: ./media/cache-dotnet-how-to-use-azure-redis-cache/redis-cache-manage-nuget-menu.png

[CacheProperties]: ./media/cache-dotnet-how-to-use-azure-redis-cache/redis-cache-properties.png

[ManageKeys]: ./media/cache-dotnet-how-to-use-azure-redis-cache/redis-cache-manage-keys.png

[SessionStateNuGet]: ./media/cache-dotnet-how-to-use-azure-redis-cache/redis-cache-session-state-provider.png

[BrowseCaches]: ./media/cache-dotnet-how-to-use-azure-redis-cache/redis-cache-browse-caches.png

[Caches]: ./media/cache-dotnet-how-to-use-azure-redis-cache/redis-cache-caches.png

[CacheCreated]: ./media/cache-dotnet-how-to-use-azure-redis-cache/redis-cache-cache-created.png




   
<!-- LINKS -->
[http://redis.io/clients]: http://redis.io/clients
[Azure Redis Cache용으로 다른 언어에서 개발]: http://msdn.microsoft.com/library/azure/dn690470.aspx
[Azure Redis 연결 문자열을 검색하고 Redsmin과 함께 사용하는 방법]: https://redsmin.uservoice.com/knowledgebase/articles/485711-how-to-connect-redsmin-to-azure-redis-cache
[Azure Redis 세션 상태 제공자]: http://go.microsoft.com/fwlink/?LinkId=398249
[How to: Configure a Cache Client Programmatically]: http://msdn.microsoft.com/library/windowsazure/gg618003.aspx
[Session State Provider for Azure Cache]: http://go.microsoft.com/fwlink/?LinkId=320835
[Azure AppFabric Cache: Caching Session State]: http://www.microsoft.com/showcase/details.aspx?uuid=87c833e9-97a9-42b2-8bb1-7601f9b5ca20
[Output Cache Provider for Azure Cache]: http://go.microsoft.com/fwlink/?LinkId=320837
[Azure Shared Caching]: http://msdn.microsoft.com/library/windowsazure/gg278356.aspx
[Team Blog]: http://blogs.msdn.com/b/windowsazure/
[Azure Caching]: http://www.microsoft.com/showcase/Search.aspx?phrase=azure+caching
[How to Configure Virtual Machine Sizes]: http://go.microsoft.com/fwlink/?LinkId=164387
[Azure Caching Capacity Planning Considerations]: http://go.microsoft.com/fwlink/?LinkId=320167
[Azure Caching]: http://go.microsoft.com/fwlink/?LinkId=252658
[How to: Set the Cacheability of an ASP.NET Page Declaratively]: http://msdn.microsoft.com/library/zd1ysf1y.aspx
[How to: Set a Page's Cacheability Programmatically]: http://msdn.microsoft.com/library/z852zf6b.aspx
[Azure Redis Cache에서 캐시 구성]: http://msdn.microsoft.com/library/azure/dn793612.aspx

[StackExchange.Redis 구성 모델]: http://github.com/StackExchange/StackExchange.Redis/blob/master/Docs/Configuration.md

[캐시의 .NET 개체 작업]: http://msdn.microsoft.com/library/dn690521.aspx#Objects


[NuGet Package Manager Installation]: http://go.microsoft.com/fwlink/?LinkId=240311
[캐시 가격 정보]: http://www.windowsazure.com/pricing/details/cache/
[Azure 미리 보기 포털]: https://portal.azure.com/

[Azure Redis 캐시 개요]: http://go.microsoft.com/fwlink/?LinkId=320830
[Azure Redis Cache]: http://go.microsoft.com/fwlink/?LinkId=398247

[Migrate to Azure Redis Cache]: http://go.microsoft.com/fwlink/?LinkId=317347
[Azure Redis Cache Samples]: http://go.microsoft.com/fwlink/?LinkId=320840
[리소스 그룹을 사용하여 Azure 리소스 관리]: http://azure.microsoft.com/documentation/articles/resource-group-overview/

[StackExchange.Redis]: http://github.com/StackExchange/StackExchange.Redis
[StackExchange.Redis 캐시 클라이언트 설명서]: http://github.com/StackExchange/StackExchange.Redis#documentation

[Redis]: http://redis.io/documentation
[redis 데이터 형식]: http://redis.io/topics/data-types
[Redis 데이터 형식에 대한 15분 소개]: http://redis.io/topics/data-types-intro

[응용 프로그램 설정 및 연결 문자열 작동 방식]: http://azure.microsoft.com/blog/2013/07/17/windows-azure-web-sites-how-application-strings-and-connection-strings-work/

[Azure 무료 체험]: http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=redis_cache_hero

<!---HONumber=Oct15_HO1-->