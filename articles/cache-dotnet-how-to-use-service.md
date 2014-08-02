<properties linkid="" urlDisplayName="" pageTitle="" metaKeywords="" description="" metaCanonical="" services="" documentationCenter="" title="How to Use Azure Cache Service (Preview)" authors="" solutions="" manager="" editor="" />

Azure 캐시 서비스(사전 검토) 사용 방법
======================================

이 가이드에서는 **Azure 캐시 서비스(사전 검토)**를 사용하는 방법에 대해 설명합니다. 샘플은 C\# 코드로 작성되었고 .NET API를 사용합니다. **캐시 클러스터 만들기와 구성**, **캐시 클라이언트 구성**, **캐시에서 개체 추가 및 삭제, 캐시에 ASP.NET 세션 상태 저장**, **캐시를 사용하여 ASP.NET 페이지 출력 캐싱 활성화** 등의 시나리오를 다룹니다. Azure 캐시 사용에 대한 자세한 내용은 [다음 단계](#next-steps) 섹션을 참조하십시오.

목차
----

-   [Azure 캐시란?](#what-is)
-   [캐시 서비스(사전 검토) 시작하기](#getting-started-cache-service)
    -   [캐시 만들기](#create-cache)
    -   [캐시 구성](#enable-caching)
    -   [캐시 클라이언트 구성](#NuGet)
-   [캐시 작업](#working-with-caches)
    -   [방법: DataCache 개체 만들기](#create-cache-object)
    -   [방법: 캐시에서 개체 추가 및 검색](#add-object)
    -   [방법: 캐시에서 개체의 만료 지정](#specify-expiration)
    -   [방법: 캐시에 ASP.NET 세션 상태 저장](#store-session)
    -   [방법: 캐시에 ASP.NET 페이지 출력 캐싱 저장](#store-page)
-   [다음 단계](#next-steps)

## Azure 캐시란?

Azure 캐시 서비스(사전 검토)는 확장 가능하며 분산된 메모리 내 솔루션으로, 데이터에 매우 빠르게 액세스하여 확장성과 응답성이 뛰어난 응용 프로그램을 빌드하는 데 사용할 수 있습니다.

Azure 캐시 서비스(사전 검토)의 특성은 다음과 같습니다.

-   ASP.NET이 미리 내장되어 세션 상태 및 페이지 출력 캐싱을 제공하므로 응용 프로그램 코드를 수정하지 않고도 웹 응용 프로그램을 가속화할 수 있습니다.
-   직렬화 가능한 모든 관리 개체(예: CLR 개체, 행, XML, 이진 데이터)를 캐시합니다.
-   Azure와 Windows Server AppFabric에서 모두 일관된 개발 모델을 제공합니다.

캐시 서비스(사전 검토)를 사용하면 Microsoft에서 관리하는 안전한 전용 캐시에 액세스할 수 있습니다. 캐시 서비스(사전 검토)를 사용하여 만든 캐시에는 Azure 웹 사이트, 웹 역할과 작업자 역할, 가상 컴퓨터에서 실행되는 Azure 내 응용 프로그램에서 액세스할 수 있습니다.

캐시 서비스(사전 검토)는 세 계층으로 제공됩니다.

-   기본 - 캐시 크기 128MB \~ 1GB
-   표준 - 캐시 크기 1GB \~ 10GB
-   프리미엄 - 캐시 크기 5GB \~ 150GB

각 계층은 기능과 가격이 다릅니다. 기능에 대해서는 이 가이드의 뒷부분에서 다룹니다. 가격에 대한 자세한 내용은 [캐시 가격 정보](http://www.windowsazure.com/en-us/pricing/details/cache/)를 참조하십시오.

이 가이드에서는 캐시 서비스(사전 검토)를 시작하기 위한 개요를 제공합니다. 이 시작하기 가이드의 범위를 벗어나는 기능에 대해 자세히 알아보려면 [Azure 캐시 서비스(사전 검토) 개요](http://go.microsoft.com/fwlink/?LinkId=320830)를 참조하십시오.

## 캐시 서비스(사전 검토) 시작하기

캐시 서비스(사전 검토) 시작하기는 간단합니다. 먼저 캐시를 프로비전하고 구성합니다. 그런 다음 캐시에 액세스할 수 있도록 캐시 클라이언트를 구성합니다. 캐시 클라이언트를 구성하면 작업을 시작할 수 있습니다.

-   [캐시 만들기](#create-cache)
-   [캐시 구성](#enable-caching)
-   [캐시 클라이언트 구성](#NuGet)

## 캐시 만들기

캐시를 만들려면 먼저 [관리 포털](https://manage.windowsazure.com/)에 로그인합니다.

> 캐시 서비스(사전 검토)를 처음 사용하는 경우 캐시 서비스 사전 검토 프로그램에 대한 액세스를 요청해야 합니다. 사전 검토 프로그램에 등록하려면 **새로 만들기**, **데이터 서비스**, **Cache 미리 보기**, **Preview Program**을 클릭합니다. 화면의 지시에 따라 캐시 서비스 사전 검토 프로그램에 대한 액세스를 요청하고, 액세스가 허용되면 다음 단계로 진행합니다.

**새로 만들기**, **데이터 서비스**, **Cache 미리 보기**, **빠른 생성**을 클릭합니다.

![NewCacheMenu](./media/cache-dotnet-how-to-use-service/CacheServiceNewCacheMenu.png)

![QuickCreate](./media/cache-dotnet-how-to-use-service/CacheServiceQuickCreate.png)

**끝점** - 캐시 끝점에 사용할 하위 도메인 이름을 입력합니다. 끝점은 6\~20자 사이의 문자열이어야 하며, 소문자와 숫자만 포함할 수 있고, 문자로 시작해야 합니다.

**지역** - 캐시의 지역을 선택합니다. 성능을 최적화하려면 캐시 클라이언트 응용 프로그램과 동일한 지역에 캐시를 만드십시오.

**구독** - 캐시에 사용할 Azure 구독을 선택합니다.

> 계정에 구독이 하나뿐인 경우 해당 구독이 자동으로 선택되며 구독 드롭다운은 표시되지 않습니다.

**Cache Offering** 및 **Cache Memory** - 이 둘은 함께 작동하여 캐시 크기를 결정합니다. 캐시 서비스(사전 검토)는 다음과 같은 세 계층으로 제공됩니다.

-   기본 - 캐시 크기는 128MB \~ 1GB이고 128MB 단위로 증가하며, 기본 명명된 캐시는 하나입니다.
-   표준 - 캐시 크기는 1GB \~ 10GB이고 1GB 단위로 증가하며, 알림이 지원되고 명명된 캐시는 최대 10개입니다.

-   프리미엄 - 캐시 크기는 5GB \~ 150GB이고 5GB 단위로 증가하며, 알림과 고가용성이 지원되고 명명된 캐시는 최대 10개입니다.

응용 프로그램 요구에 맞는 **Cache Offering** 및 **Cache Memory**를 선택합니다. 알림과 고가용성 등 일부 캐시 기능은 특정 캐시 옵션에서만 사용할 수 있습니다. 응용 프로그램에 가장 적합한 캐시 옵션과 크기를 선택하는 방법에 대한 자세한 내용은 [캐시 기능](http://go.microsoft.com/fwlink/?LinkId=317277) 및 [용량 계획](http://go.microsoft.com/fwlink/?LinkId=320167)을 참조하십시오.

새 캐시 옵션을 구성했으면 **Create a New Cache**를 클릭합니다. 캐시를 만드는 데 몇 분 정도 걸릴 수 있습니다. 상태를 확인하려면 포털 화면의 아래쪽에서 알림을 모니터링할 수 있습니다. 캐시가 생성되면 새로운 캐시는 'Running' 상태가 되며, 기본 설정을 이용해 바로 사용할 수 있습니다. 캐시의 구성을 사용자 지정하려면 다음의 [캐시 구성](#enable-caching) 섹션을 참조하십시오.

## 캐시 구성

관리 포털에서 캐시에 대한 **구성** 탭은 캐시의 옵션을 구성할 수 있는 곳입니다. 각 캐시에는 **기본** 명명된 캐시가 있습니다. 표준과 프리미엄에서는 최대 9개의 명명된 캐시가 추가로 지원되어 총 10개의 캐시가 제공됩니다. 명명된 각 캐시에는 자체 옵션 세트가 있으며, 이를 통해 매우 유연하게 캐시를 구성할 수 있습니다.

![NamedCaches](./media/cache-dotnet-how-to-use-service/CacheServiceNamedCaches.jpg)

명명된 캐시를 만들려면 **이름** 상자에 새 캐시의 이름을 입력하고, 원하는 옵션을 지정하고, **저장**을 클릭하고, **예**를 클릭하여 확인합니다. 변경 사항을 취소하려면 **취소**를 클릭합니다.

만료 정책 및 시간(분)
---------------------

**만료 정책**은 **시간(분)** 설정과 결합되어 캐시된 항목의 만료 시기를 결정합니다. 만료 정책에는 **절대**, **슬라이딩**, **없음**의 세 가지 유형이 있습니다.

**절대**를 선택한 경우에는 캐시에 항목이 추가되면 **시간(분)**으로 지정된 만료 간격이 시작됩니다. **시간(분)**으로 지정한 간격이 경과하면 항목이 만료됩니다.

**슬라이딩**을 선택한 경우에는 캐시에서 항목이 액세스될 때마다 **시간(분)**으로 지정된 만료 간격이 다시 설정됩니다. 항목에 대한 마지막 액세스 이후 **시간(분)**으로 지정한 간격이 경과할 때까지는 항목이 만료되지 않습니다.

**없음**을 선택한 경우에는 **시간(분)**을 **0**으로 설정해야 하며, 항목이 만료되지 않습니다.

기본 만료 정책은 **절대**이며 **시간(분)**의 기본 설정은 10분입니다. 명명된 캐시의 각 항목에는 고정된 만료 정책이 사용되지만, 시간 제한 매개 변수가 있는 **Add** 및 **Put** 오버로드를 사용하여 각 항목에 대해 **시간(분)**을 사용자 지정할 수 있습니다.

제거 및 만료 정책에 대한 자세한 내용은 [만료 및 제거](http://go.microsoft.com/fwlink/?LinkId=317278)를 참조하십시오.

알림
----

캐시 알림은 캐시 클러스터에서 다양한 캐시 작업이 발생할 때 응용 프로그램에서 비동기 알림을 받을 수 있도록 하는 기능입니다. 캐시 알림은 로컬로 캐시된 개체의 자동 무효화도 제공합니다. 자세한 내용은 [알림](http://go.microsoft.com/fwlink/?LinkId=317276)을 참조하십시오.

> 알림은 표준과 프리미엄에서만 사용할 수 있고, 기본에서는 사용할 수 없습니다. 자세한 내용은 [캐시 기능](http://go.microsoft.com/fwlink/?LinkId=317277)을 참조하십시오.

고가용성
--------

고가용성이 사용되는 경우에는 캐시에 추가되는 각 항목에 대해 백업 복사본이 만들어집니다. 항목의 기본 복사본에 예기치 않은 오류가 발생하면 백업 복사본을 사용할 수 있습니다.

정의에서 알 수 있듯이, 고가용성을 사용하면 캐시된 각 항목에 필요한 메모리 양의 두 배가 필요합니다. 용량 계획을 세우는 동안 이러한 메모리 문제를 고려하십시오. 자세한 내용은 [고가용성](http://go.microsoft.com/fwlink/?LinkId=317329)을 참조하십시오.

> 고가용성은 프리미엄에서만 사용할 수 있고, 기본 또는 표준에서는 사용할 수 없습니다. 자세한 내용은 [캐시 기능](http://go.microsoft.com/fwlink/?LinkId=317277)을 참조하십시오.

제거
----

캐시 내에서 사용 가능한 메모리의 용량을 유지하기 위해 LRU(오래 전에 사용한 항목) 제거가 지원됩니다. 메모리 소비가 임계값을 초과하면, 메모리 압력이 해소될 때까지 만료 여부와 상관없이 메모리에서 개체가 제거됩니다. 제거는 기본적으로 사용됩니다. 제거가 사용되지 않으면 용량이 한계에 도달해도 캐시에서 항목이 삭제되지 않습니다. 대신 Put 및 Add 작업이 실패하게 됩니다.

제거 및 만료 정책에 대한 자세한 내용은 [만료 및 제거](http://go.microsoft.com/fwlink/?LinkId=317278)를 참조하십시오.

캐시 구성을 마쳤으면 캐시에 액세스하도록 캐시 클라이언트를 구성할 수 있습니다.

## 캐시 클라이언트 구성

캐시 서비스(사전 검토)를 사용하여 만든 캐시에는 Azure 웹 사이트, 웹 역할과 작업자 역할, 가상 컴퓨터에서 실행되는 Azure 응용 프로그램에서 액세스할 수 있습니다. 캐시 클라이언트 응용 프로그램의 구성을 간소화하는 NuGet 패키지가 제공됩니다.

Cache NuGet 패키지를 사용하여 클라이언트 응용 프로그램을 구성하려면 **솔루션 탐색기**에서 프로젝트를 마우스 오른쪽 단추로 클릭하고 **NuGet 패키지 관리**를 선택합니다.

![NuGetPackageMenu](./media/cache-dotnet-how-to-use-service/CacheServiceManageNuGetPackagesMenu.png)

**Azure 캐싱**을 선택하고 **설치**와 '동의함'을 차례로 클릭합니다.

> **Azure 캐싱**이 목록에 나타나지 않으면 **온라인 검색** 텍스트 상자에 **WindowsAzure.Caching**을 입력한 후 결과에서 선택합니다.

![NuGetPackage](./media/cache-dotnet-how-to-use-service/CacheServiceManageNuGetPackage.png)

NuGet 패키지는 여러 가지 작업을 수행합니다. 응용 프로그램의 구성 파일에 필요한 구성을 추가하고, 필요한 어셈블리 참조를 추가합니다. 클라우드 서비스 프로젝트의 경우, 클라우드 서비스의 ServiceConfiguration.cscfg 파일에 캐시 클라이언트 진단 수준 설정도 추가합니다.

> ASP.NET 웹 프로젝트의 경우, Cache NuGet 패키지는 또한 web.config에 주석으로 처리된 두 개의 섹션을 추가합니다. 첫 번째 섹션은 세션 상태가 캐시에 저장되도록 하고, 두 번째 섹션은 ASP.NET 페이지 출력 캐싱을 활성화합니다. 자세한 내용은 [방법: 캐시에 ASP.NET 세션 상태 저장](#store-session) 및 [방법: 캐시에 ASP.NET 페이지 출력 캐싱 저장](#store-page)을 참조하십시오.

NuGet 패키지는 응용 프로그램의 web.config 또는 app.config에 다음과 같은 구성 요소를 추가합니다. **dataCacheClients** 섹션과 **cacheDiagnostics** 섹션이 **configSections** 요소 아래에 추가됩니다. **configSections** 요소가 없는 경우 **configuration** 요소의 하위 요소로서 하나가 생성됩니다.

    <configSections>
      <!-- Existing sections omitted for clarity. -->
      <section name="dataCacheClients" 
        type="Microsoft.ApplicationServer.Caching.DataCacheClientsSection,
              Microsoft.ApplicationServer.Caching.Core" 
        allowLocation="true" 
        allowDefinition="Everywhere" />
      
    <section name="cacheDiagnostics" 
        type="Microsoft.ApplicationServer.Caching.AzureCommon.DiagnosticsConfigurationSection,
              Microsoft.ApplicationServer.Caching.AzureCommon" 
        allowLocation="true" 
        allowDefinition="Everywhere" />

이 두 섹션에는 **dataCacheClients** 요소에 대한 참조가 포함되며, 이 요소 역시 **configuration** 요소에 추가됩니다.

    <dataCacheClients>
      <dataCacheClient name="default">
        <!--To use the in-role flavor of Azure Caching, 
            set identifier to be the cache cluster role name -->
        <!--To use the Azure Caching Service,
            set identifier to be the endpoint of the cache cluster -->
        <autoDiscover isEnabled="true" identifier="[Cache role name or Service Endpoint]" />
        <!--<localCache isEnabled="true" sync="TimeoutBased" objectCount="100000" ttlValue="300" />-->
        <!--Use this section to specify security settings for connecting to your cache. 
            This section is not required if your cache is hosted on a role that is a part
            of your cloud service. -->
        <!--<securityProperties mode="Message" sslEnabled="false">
          <messageSecurity authorizationInfo="[Authentication Key]" />
        </securityProperties>-->
      </dataCacheClient>
    </dataCacheClients>

구성이 추가되었으면 새로 추가된 구성에서 다음 두 항목을 교체합니다.

1.  **[Cache role name or Service Endpoint]**를 관리 포털의 대시보드에 표시되는 끝점으로 교체합니다.

    ![끝점](./media/cache-dotnet-how-to-use-service/CacheServiceEndpoint.png)

2.  securityProperties 섹션의 주석 처리를 제거하고 **[Authentication Key]**를 인증 키로 교체합니다. 관리 포털의 캐시 대시보드에서 **키 관리**를 클릭하면 인증 키를 찾을 수 있습니다.

    ![AccessKeys](./media/cache-dotnet-how-to-use-service/CacheServiceManageAccessKeys.png)

> 이러한 설정을 제대로 구성하지 않으면 클라이언트가 캐시에 액세스할 수 없습니다.

클라우드 서비스 프로젝트의 경우, NuGet 패키지는 또한 ServiceConfiguration.cscfg에 있는 캐시 클라이언트 역할의 **ConfigurationSettings**에 **ClientDiagnosticLevel** 설정을 추가합니다. 다음 예제는 **ClientDiagnosticLevel**이 1(**ClientDiagnosticLevel** 기본값)인 ServiceConfiguration.cscfg 파일의 **WebRole1** 섹션입니다.

    <Role name="WebRole1">
      <Instances count="1" />
      <ConfigurationSettings>
        <!-- Existing settings omitted for clarity. -->
        <Setting name="Microsoft.WindowsAzure.Plugins.Caching.ClientDiagnosticLevel" 
                 value="1" />
      </ConfigurationSettings>
    </Role>

> 클라이언트 진단 수준은 캐시 클라이언트를 위해 수집된 캐싱 진단 정보의 수준을 구성합니다. 자세한 내용은 [문제 해결 및 진단](http://go.microsoft.com/fwlink/?LinkId=320839)을 참조하십시오.

NuGet 패키지는 또한 다음 어셈블리에 참조를 추가합니다.

-   Microsoft.ApplicationServer.Caching.Client.dll
-   Microsoft.ApplicationServer.Caching.Core.dll
-   Microsoft.WindowsFabric.Common.dll
-   Microsoft.WindowsFabric.Data.Common.dll
-   Microsoft.ApplicationServer.Caching.AzureCommon.dll
-   Microsoft.ApplicationServer.Caching.AzureClientHelper.dll

웹 프로젝트인 경우에는 다음 어셈블리 참조도 추가됩니다.

-   Microsoft.Web.DistributedCache.dll.

> 이러한 어셈블리는 C:\\Program Files\\Microsoft SDKs\\Windows Azure\\.NET SDK\\[sdk version]\\ref\\Caching\\ 폴더에 있습니다.

클라이언트 프로젝트의 캐싱을 구성했으면 캐시 작업에 대해 다음 섹션에서 설명하는 기술을 사용할 수 있습니다.

## 캐시 작업

이 섹션의 각 단계에서는 일반적인 캐시 작업 수행 방법에 대해 설명합니다.

-   [방법: DataCache 개체 만들기](#create-cache-object)
-   [방법: 캐시에서 개체 추가 및 검색](#add-object)
-   [방법: 캐시에서 개체의 만료 지정](#specify-expiration)
-   [방법: 캐시에 ASP.NET 세션 상태 저장](#store-session)
-   [방법: 캐시에 ASP.NET 페이지 출력 캐싱 저장](#store-page)

## 방법: DataCache 개체 만들기

프로그래밍 방식으로 캐시 작업을 하려면 캐시에 대한 참조가 필요합니다. Azure 캐시를 사용할 파일의 상단에 다음을 추가합니다.

    using Microsoft.ApplicationServer.Caching;

> 필요한 참조를 추가하는 Cache NuGet 패키지를 설치한 후에도 Visual Studio에서 using 문의 유형을 인식하지 못하면 프로젝트의 대상 프로필이 .NET Framework 4 이상인지, **Client Profile**을 지정하지 않는 프로필 중 하나를 선택했는지를 확인하십시오. 캐시 클라이언트 구성에 대한 자세한 내용은 [캐시 클라이언트 구성](#NuGet)을 참조하십시오.

**DataCache** 개체를 만드는 두 가지 방법이 있습니다. 첫 번째 방법은 단순히 **DataCache**를 만들고 원하는 캐시 이름으로 전달하는 것입니다.

    DataCache cache = new DataCache("default");

**DataCache**가 인스턴스화되면, 다음 섹션에서 설명한 것처럼 이를 사용해 캐시를 조작할 수 있습니다.

두 번째 방법은 기본 생성자를 사용하여 응용 프로그램에 새로운 **DataCacheFactory** 개체를 만드는 것입니다. 이렇게 하면 캐시 클라이언트가 구성 파일의 설정을 사용합니다. **DataCache** 개체를 반환하는 새로운 **DataCacheFactory** 인스턴스의 **GetDefaultCache** 메서드를 호출하거나, **GetCache** 메서드를 호출하고 캐시의 이름으로 전달합니다. 두 가지 메서드 모두 프로그래밍 방식으로 캐시에 액세스하기 위해 사용할 수 있는 **DataCache** 개체를 반환합니다.

    // Cache client configured by settings in application configuration file.
    DataCacheFactory cacheFactory = new DataCacheFactory();
    DataCache cache = cacheFactory.GetDefaultCache();
    // Or DataCache cache = cacheFactory.GetCache("MyCache");
    // cache can now be used to add and retrieve items. 

## 방법: 캐시에서 개체 추가 및 검색

캐시에 항목을 추가하려면 **Add** 또는 **Put** 메서드를 사용할 수 있습니다. **Add** 메서드는 키 매개 변수 값의 키와 함께 지정한 개체를 캐시에 추가합니다.

    // Add the string "value" to the cache, keyed by "item"
    cache.Add("item", "value");

동일한 키의 개체가 이미 캐시에 있으면 다음 메시지와 함께 **DataCacheException**이 발생합니다.

> ErrorCode:SubStatus: 캐시에 이미 있는 키를 사용하여 개체를 만들려고 하고 있습니다. 캐시는 개체에 대해 고유한 키 값만 허용합니다.

특정 키로 개체를 검색하려면 **Get** 메서드를 사용할 수 있습니다. 개체가 있으면 반환되고 없으면 Null이 반환됩니다.

    // Add the string "value" to the cache, keyed by "key"
    object result = cache.Get("Item");
    if (result == null)
    {
        // "Item" not in cache. Obtain it from specified data source
        // and add it.
        string value = GetItemValue(...);
        cache.Add("item", value);
    }
    else
    {
        // "Item" is in cache, cast result to correct type.
    }

**Put** 메서드는 지정한 키의 개체를 캐시에 추가하거나(없는 경우) 개체를 교체합니다(있는 경우).

    // Add the string "value" to the cache, keyed by "item". If it exists,
    // replace it.
    cache.Put("item", "value");

## 방법: 캐시에서 개체의 만료 지정

기본적으로 캐시에 배치된 항목은 10분 후 만료됩니다. 이 값은 관리 포털의 캐시에 대한 '구성' 탭에 있는 **시간(분)** 설정에서 구성할 수 있습니다.

![NamedCaches](./media/cache-dotnet-how-to-use-service/CacheServiceNamedCaches.jpg)

**만료 정책**에는 **없음**, **절대**, **슬라이딩**의 세 가지 유형이 있습니다. 만료 유형은 **시간(분)**을 사용하여 만료를 결정하는 방법을 구성합니다. 기본 **만료 유형**은 **절대**입니다. 이는 항목이 캐시에 배치되면 항목의 만료에 대한 카운트다운 타이머가 시작된다는 뜻입니다. 항목에 대해 지정한 시간이 경과하면 항목이 만료됩니다. **슬라이딩**을 지정하면, 캐시에서 항목이 액세스될 때마다 만료 카운트다운이 다시 설정되며 마지막 액세스 이후 지정된 시간이 경과하기 전에는 항목이 만료되지 않습니다. **없음**을 지정하면 **시간(분)**을 **0**으로 설정해야 합니다. 이 경우 항목은 만료되지 않으며 캐시에 있는 한 유효한 상태가 유지됩니다.

캐시 속성에서 구성한 것보다 더 길거나 짧은 시간 제한 간격이 필요한 경우, 캐시에서 항목이 추가 또는 업데이트될 때 **TimeSpan** 매개 변수가 있는 **Add** 및 **Put** 오버로드를 사용하여 특정 기간을 지정할 수 있습니다. 다음 예제에서는 **item** 키와 함께 문자열 **value**가 캐시에 추가되었고 시간 제한은 30분입니다.

    // Add the string "value" to the cache, keyed by "item"
    cache.Add("item", "value", TimeSpan.FromMinutes(30));

캐시에 있는 항목의 남은 시간 제한 간격을 보려면 **GetCacheItem** 메서드를 사용하여, 남은 시간 제한 간격을 비롯하여 캐시의 항목에 대한 정보를 포함하는 **DataCacheItem** 개체를 검색할 수 있습니다.

    // Get a DataCacheItem object that contains information about
    // "item" in the cache. If there is no object keyed by "item" null
    // is returned. 
    DataCacheItem item = cache.GetCacheItem("item");
    TimeSpan timeRemaining = item.Timeout;

## 방법: 캐시에 ASP.NET 세션 상태 저장

Azure 캐시의 세션 상태 공급자는 ASP.NET 응용 프로그램의 Out of Process 저장소 메커니즘입니다. 이 공급자를 사용하면 메모리 내 또는 SQL Server 데이터베이스가 아니라 Azure 캐시에 세션 상태를 저장할 수 있습니다. 캐싱 세션 상태 공급자를 사용하려면 먼저 캐시를 구성하고 [캐시 서비스(사전 검토) 시작하기](#getting-started-cache-service)에서 설명한 대로 Cache NuGet 패키지를 사용하여 캐시용 ASP.NET 응용 프로그램을 구성하십시오. Cache NuGet 패키지를 설치하면 주석으로 처리된 섹션이 web.config에 추가됩니다. 이 섹션에는 ASP.NET 응용 프로그램에서 Azure 캐시에 세션 상태 공급자를 사용하는 데 필요한 구성이 포함되어 있습니다.

    <!--Uncomment this section to use Azure Caching for session state caching
    <system.web>
      <sessionState mode="Custom" customProvider="AFCacheSessionStateProvider">
        <providers>
          <add name="AFCacheSessionStateProvider" 
            type="Microsoft.Web.DistributedCache.DistributedCacheSessionStateStoreProvider,
                  Microsoft.Web.DistributedCache" 
            cacheName="default" 
            dataCacheClientName="default"/>
        </providers>
      </sessionState>
    </system.web>-->

> Cache NuGet 패키지를 설치한 후에도 주석으로 처리된 이 섹션이 web.config에 포함되지 않으면 [NuGet 패키지 관리자 설치](http://go.microsoft.com/fwlink/?LinkId=240311)에서 최신 NuGet 패키지 관리자를 설치했는지 확인하고, 패키지를 제거한 후 다시 설치합니다.

Azure 캐시에 세션 상태 공급자를 사용하려면 지정된 섹션의 주석 처리를 제거합니다. 제공된 코드 조각에 기본 캐시가 지정되어 있습니다. 다른 캐시를 사용하려면 **cacheName** 특성에서 원하는 캐시를 지정하십시오.

캐시 서비스 세션 상태 공급자에 대해 자세히 알아보려면 [Azure 캐시용 세션 상태 공급자](http://go.microsoft.com/fwlink/?LinkId=320835)를 참조하십시오.

## 방법: 캐시에 ASP.NET 페이지 출력 캐싱 저장

Azure 캐시의 출력 캐시 공급자는 출력 캐시 데이터의 Out of Process 저장소 메커니즘입니다. 이 데이터는 완전한 HTTP 응답(페이지 출력 캐싱)에 특별히 사용됩니다. 이 공급자는 ASP.NET 4에 도입된 새로운 출력 캐시 공급자 확장 포인트에 연결됩니다. 출력 캐시 공급자를 사용하려면 먼저 캐시 클러스터를 구성하고, [캐시 서비스(사전 검토) 시작하기](#getting-started-cache-service)에서 설명한 대로 Cache NuGet 패키지를 사용하여 캐싱용 ASP.NET 응용 프로그램을 구성하십시오. Caching NuGet 패키지를 설치하면 주석으로 처리된 다음 섹션이 web.config에 추가됩니다. 이 섹션에는 ASP.NET 응용 프로그램에서 Azure 캐싱에 출력 캐시 공급자를 사용하는 데 필요한 구성이 포함되어 있습니다.

    <!--Uncomment this section to use Azure Caching for output caching
    <caching>
      <outputCache defaultProvider="AFCacheOutputCacheProvider">
        <providers>
          <add name="AFCacheOutputCacheProvider" 
            type="Microsoft.Web.DistributedCache.DistributedCacheOutputCacheProvider,
                  Microsoft.Web.DistributedCache" 
            cacheName="default" 
            dataCacheClientName="default" />
        </providers>
      </outputCache>
    </caching>-->

> Cache NuGet 패키지를 설치한 후에도 주석으로 처리된 이 섹션이 web.config에 포함되지 않으면 [NuGet 패키지 관리자 설치](http://go.microsoft.com/fwlink/?LinkId=240311)에서 최신 NuGet 패키지 관리자를 설치했는지 확인하고, 패키지를 제거한 후 다시 설치합니다.

Azure 캐시에 출력 캐시 공급자를 사용하려면 지정된 섹션의 주석 처리를 제거합니다. 제공된 코드 조각에 기본 캐시가 지정되어 있습니다. 다른 캐시를 사용하려면 **cacheName** 특성에서 원하는 캐시를 지정하십시오.

출력을 캐시하고자 하는 각 페이지에 **OutputCache** 지시문을 추가합니다.

    <%@ OutputCache Duration="60" VaryByParam="*" %>

이 예제에서는 캐시된 페이지 데이터가 캐시에 60초 동안 머물게 되며, 각 매개 변수 조합에 따라 페이지의 다른 버전이 캐시됩니다. 사용 가능한 옵션에 대한 자세한 내용은 [OutputCache 지시문](http://go.microsoft.com/fwlink/?LinkId=251979)을 참조하십시오.

Azure 캐시에 출력 캐시 공급자를 사용하는 방법에 대한 자세한 내용은 [Azure 캐시용 출력 캐시 공급자](http://go.microsoft.com/fwlink/?LinkId=320837)를 참조하십시오.

## 다음 단계

이제 캐시 서비스(사전 검토)의 기본 사항을 배웠으므로 다음 링크를 따라 좀 더 복잡한 캐싱 작업을 수행하는 방법을 알아보십시오.

-   다음 MSDN 참조를 확인하십시오. [캐시 서비스(사전 검토)](http://go.microsoft.com/fwlink/?LinkId=320830)
-   캐시 서비스(사전 검토)로 마이그레이션하는 방법: [캐시 서비스(사전 검토)로 마이그레이션](http://go.microsoft.com/fwlink/?LinkId=317347)
-   샘플 확인: [캐시 서비스(사전 검토) 샘플](http://go.microsoft.com/fwlink/?LinkId=320840)

