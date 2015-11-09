<properties 
   pageTitle="캐시 ASP.NET 출력 캐시 공급자"
   description="Azure Redis Cache를 사용하여 ASP.NET 페이지 출력 캐시하는 방법을 알아봅니다."
   services="redis-cache"
   documentationCenter="na"
   authors="steved0x"
   manager="dwrede"
   editor="tysonn" />
<tags 
   ms.service="cache"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="cache-redis"
   ms.workload="tbd"
   ms.date="10/23/2015"
   ms.author="sdanie" />

# Azure Redis Cache에 대한 ASP.NET 출력 캐시 공급자

Redis 출력 캐시 공급자는 출력 캐시 데이터에 대한 out-of-process 저장소 메커니즘입니다. 이 데이터는 완전한 HTTP 응답(페이지 출력 캐싱)에 특별히 사용됩니다. 공급자가 ASP.NET 4에 도입된 새로운 출력 캐시 공급자 확장 포인트에 연결됩니다.

Redis 출력 캐시 공급자를 사용 하려면 먼저 캐시를 구성하고 Redis 출력 개시 공급자 NuGet 패키지를 사용하여 사용자의 ASP.NET 응용 프로그램을 구성합니다. 이 항목에서는 Redis 출력 캐시 공급자를 사용할 수 있도록 응용 프로그램을 구성하는 지침을 제공합니다. Azure Redis Cache 인스턴스 생성 및 구성에 대한 자세한 내용은 [캐시 생성하기](cache-dotnet-how-to-use-azure-redis-cache.md#create-a-cache)을 참조하세요.

## ASP.NET 페이지 출력을 캐시에 저장

Redis 출력 캐시 공급자 NuGet 패키지를 사용하여 Visual Studio에서 클라이언트 응용 프로그램을 구성하려면 **솔루션 탐색**을 오른쪽 클릭하고 **NuGet 패키지 관리**를 선택합니다.

![Azure Redis Cache 관리 NuGet 패키지](./media/cache-asp.net-output-cache-provider/IC729541.png)

**RedisOutputCacheProvider**를 **온라인** 검색 텍스트 상자에 입력하고 검색 결과를 선택하고 **설치**를 클릭합니다.

![Azure Redis Cache 출력 캐시 공급자](./media/cache-asp.net-output-cache-provider/IC751727.jpg)

Reids Output 캐시 공급자 NuGet 패키지는 StackExchange.Redis.StrongName 패키지에 종속성을 갖습니다. StackExchange.Redis.StrongName 패키지가 프로젝트에 나타나지 않는 경우 설치됩니다. 강력한 이름의 StackExchange.Redis.StrongName 패키지 외에도 StackExchange.Redis non-strong-name 버전이 있다는 것을 참고하세요. 프로젝트에 non-strong 이름의 StackExchange.Redis 버전을 사용하고 있다면 Redis 출력 캐시 공급자 NuGet Package 설치 전이나 후에 꼭 삭제해야 합니다. 그렇지 않은 경우 프로젝트 내에 이름 충돌이 일어납니다. 이 패키지에 대한 자세한 내용은 [.NET 캐시 클라이언트 구성](cache-dotnet-how-to-use-azure-redis-cache.md#configure-the-cache-clients)을 참조하세요.

NuGet 패키지를 다운로드하고 필요한 어셈블리 참조를 추가합니다. Redis 출력 캐시 공급자를 사용하도록 ASP.NET 응용 프로그램에 필요한 구성이 포함된 web.config 파일에 다음 섹션을 추가합니다.

    <caching>
      <outputCachedefaultProvider="MyRedisOutputCache">
        <providers>
          <!--
          <add name="MyRedisOutputCache" 
            host = "127.0.0.1" [String]
            port = "" [number]
            accessKey = "" [String]
            ssl = "false" [true|false]
            databaseId = "0" [number]
            applicationName = "" [String]
            connectionTimeoutInMilliseconds = "5000" [number]
            operationTimeoutInMilliseconds = "5000" [number]
          />
          -->
          <add name="MyRedisOutputCache"type="Microsoft.Web.Redis.RedisOutputCacheProvider"host="127.0.0.1"accessKey="" ssl="false"/>
        </providers>
      </outputCache>
    </caching>

주석 처리된 섹션은 특성의 예와 각 특성의 샘플 설정을 제공합니다.

Microsoft Azure 포털의 캐시 블레이드에서 값으로 특성을 구성하고, 필요에 따라 다른 값을 구성합니다. 캐시 속성에 액세스 하는 방법은 [Redis 캐시 설정 구성](cache-configure.md#configure-redis-cache-settings)을 참조하세요.

-	**호스트** – 캐시 끝점을 지정합니다.
-	**포트** – ssl 설정에 따라 비-SSL 포트 또는 SSL 포트를 사용합니다.
-	**액세스키** – 캐시에 적합한 기본 또는 보조 키를 사용합니다.
-	**ssl** – ssl로 캐시/클라이언트 통신을 보호하려는 경우 true가 되고, 그 외의 경우 false입니다. 올바른 포트를 지정해야 합니다.
	-	비 SSL 포트는 기본적으로 새 캐시에 대해 사용하지 않도록 설정됩니다. SSL 포트를 사용하여 설정에 대한 true를 지정합니다. 비-SSL 포트 사용 방법에 대한 자세한 내용은 [캐시 구성](cache-configure.md) 토픽의 [액세스 포트](cache-configure.md#access-ports) 섹션을 참조하세요
-	**databaseId** – 캐시 출력 데이터에 사용할 데이터베이스를 지정합니다. 지정하지 않으면 기본값 0이 사용됩니다.
-	**applicationName** – <AppName>\_<SessionId>\_데이터로 redis에 저장된 키입니다. 이 방법으로 여러 응용 프로그램이 동일한 키를 공유할 수 있습니다. 이 매개변수는 선택적이며 사용자가 제공하지 않으면 기본값이 사용됩니다.
-	**connectionTimeoutInMilliseconds** – 이 설정은 StackExchange.Redis 클라이언트의 connectTimeout 설정을 무시할 수 있게 해줍니다. 지정하지 않으면 기본 connectTimeout 설정인 5000이 사용됩니다. 더 자세한 내용은 [StackExchange.Redis 구성 모델](http://go.microsoft.com/fwlink/?LinkId=398705)을 참조하세요.
-	**operationTimeoutInMilliseconds** – 이 설정은 StackExchange.Redis 클라이언트의 syncTimeout 설정을 무시할 수 있게 해줍니다. 지정하지 않으면 기본 syncTimeout 설정인 1000이 사용됩니다. 더 자세한 내용은 [StackExchange.Redis 구성 모델](http://go.microsoft.com/fwlink/?LinkId=398705)을 참조하세요.

출력을 캐시하고자 하는 각 페이지에 OutputCache 지시문을 추가합니다.

    <%@ OutputCache Duration="60" VaryByParam="*" %>

이 예제에서는 캐시된 페이지 데이터가 캐시에 60초 동안 머물게 되며, 각 매개 변수 조합에 따라 페이지의 다른 버전이 캐시됩니다. 출력 캐시 지시문에 대한 자세한 내용은 [@OutputCache](http://go.microsoft.com/fwlink/?linkid=320837)를 참조하세요.

이 단계를 수행하면, 응용 프로그램은 Redis 출력 캐시 공급자를 사용하도록 구성됩니다.

## 다음 단계

[Azure Redis Cache에 대한 ASP.NET 세션 상태 제공자](cache-asp.net-session-state-provider.md)를 확인하세요.

<!---HONumber=Nov15_HO1-->