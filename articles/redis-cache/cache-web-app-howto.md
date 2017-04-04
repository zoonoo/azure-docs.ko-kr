---
title: "Redis Cache를 사용하여 웹앱을 만드는 방법 | Microsoft 문서"
description: "Redis Cache를 사용하여 웹앱을 만드는 방법 알아보기"
services: redis-cache
documentationcenter: 
author: steved0x
manager: douge
editor: 
ms.assetid: 454e23d7-a99b-4e6e-8dd7-156451d2da7c
ms.service: cache
ms.workload: tbd
ms.tgt_pltfrm: cache-redis
ms.devlang: na
ms.topic: hero-article
ms.date: 03/27/2017
ms.author: sdanie
translationtype: Human Translation
ms.sourcegitcommit: 6e0ad6b5bec11c5197dd7bded64168a1b8cc2fdd
ms.openlocfilehash: 02e30f7fcbe0782528460b542a75f1d11c7286a1
ms.lasthandoff: 03/28/2017


---
# <a name="how-to-create-a-web-app-with-redis-cache"></a>Redis Cache를 사용하여 웹앱을 만드는 방법
> [!div class="op_single_selector"]
> * [.NET](cache-dotnet-how-to-use-azure-redis-cache.md)
> * [ASP.NET](cache-web-app-howto.md)
> * [Node.JS](cache-nodejs-get-started.md)
> * [Java](cache-java-get-started.md)
> * [Python](cache-python-get-started.md)
> 
> 

이 자습서에서는 Visual Studio 2017을 사용하여 Azure App Service에서 ASP.NET 웹 응용 프로그램을 만들고 웹앱에 배포하는 방법을 보여 줍니다. 샘플 응용 프로그램은 데이터베이스에서 팀 통계 목록을 표시하고 Azure Redis Cache를 사용하여 캐시에서 데이터를 저장 및 검색하는 여러 가지 방법을 보여 줍니다. 이 자습서를 완료하면 Azure Redis Cache에 최적화되고 Azure에서 호스팅되며 데이터베이스에 읽고 쓰는 실행 웹앱을 갖게 됩니다.

다음 내용을 배웁니다.

* Visual Studio에서 ASP.NET MVC 5 웹 응용 프로그램을 만드는 방법.
* Entity Framework를 사용하여 데이터베이스에서 데이터에 액세스하는 방법.
* Azure Redis Cache를 사용하여 데이터를 저장 및 검색하여 데이터 처리 능력을 개선하고 데이터베이스 부하를 줄이는 방법.
* Redis 정렬된 집합을 사용하여 상위 5개 팀을 검색하도록 설정하는 방법.
* Resource Manager 템플릿을 사용하여 응용 프로그램에 대한 Azure 리소스를 프로비전하는 방법.
* Visual Studio를 사용하여 응용 프로그램을 Azure에 게시하는 방법.

## <a name="prerequisites"></a>필수 조건
자습서를 완료하려면 다음 필수 구성 요소가 있어야 합니다.

* [Azure 계정](#azure-account)
* [.NET용 Azure SDK 포함 Visual Studio 2017](#visual-studio-2017-with-the-azure-sdk-for-net)

### <a name="azure-account"></a>Azure 계정
이 자습서를 완료하려면 Azure 계정이 있어야 합니다. 다음을 수행할 수 있습니다.

* [Azure 계정을 무료로 개설할 수 있습니다](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=redis_cache_hero). 유료 Azure 서비스를 사용해볼 수 있는 크레딧을 받게 됩니다. 크레딧을 모두 사용한 후에도 계정을 유지하고 무료 Azure 서비스 및 기능을 사용할 수 있습니다.
* [Visual Studio 구독자 혜택 활성화](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=redis_cache_hero) MSDN 구독은 유료 Azure 서비스에 사용할 수 있는 크레딧을 매달 제공합니다.

### <a name="visual-studio-2017-with-the-azure-sdk-for-net"></a>.NET용 Azure SDK 포함 Visual Studio 2017
자습서는 [Azure SDK for.NET](https://www.visualstudio.com/news/releasenotes/vs2017-relnotes#azuretools)이 포함된 Visual Studio 2017을 기준으로 작성되었습니다. Azure SDK 2.9.5에는 Visual Studio 설치 관리자가 포함되어 있습니다.

Visual Studio 2015가 설치된 경우 [Azure SDK for.NET](../dotnet-sdk.md) 2.8.2 이상을 포함한 자습서를 따를 수 있습니다. [여기서 Visual Studio 2015용 최신 Azure SDK를 다운로드](http://go.microsoft.com/fwlink/?linkid=518003)합니다. SDK가 없는 경우 Visual Studio는 SDK와 함께 자동으로 설치됩니다. 일부 화면이 이 자습서에 표시된 그림과 다르게 보일 수 있습니다.

Visual Studio 2013이 있는 경우 [최신 Visual Studio 2013용 Azure SDK를 다운로드](http://go.microsoft.com/fwlink/?LinkID=324322)할 수 있습니다. 일부 화면이 이 자습서에 표시된 그림과 다르게 보일 수 있습니다.

## <a name="create-the-visual-studio-project"></a>Visual Studio 프로젝트 만들기
1. Visual Studio를 열고 **파일**, **새로 만들기**, **프로젝트**를 클릭합니다.
2. **템플릿** 목록에서 **Visual C#** 노드를 확장하고 **클라우드**를 선택한 다음 **ASP.NET 웹 응용 프로그램**을 클릭합니다. **.NET Framework 4.5.2** 이상이 선택되었는지 확인합니다.  **이름** 텍스트 상자에 **ContosoTeamStats**를 입력하고 **확인**을 클릭합니다.
   
    ![프로젝트 만들기][cache-create-project]
3. 프로젝트 유형으로 **MVC** 를 선택합니다. 

    **인증** 설정에 **인증 없음**을 지정했는지 확인합니다. Visual Studio의 버전에 따라 다른 기본값을 설정할 수 있습니다. 변경하려면 **인증 변경**을 클릭하고 **인증 없음**을 선택합니다.

    Visual Studio 2015에서 수행하는 경우 **클라우드에서 호스트** 확인란을 선택 취소합니다. 자습서의 이후 단계에서는 [Azure 리소스를 프로비전](#provision-the-azure-resources)하고 [응용 프로그램을 Azure에 게시](#publish-the-application-to-azure)합니다. **클라우드에서 호스트** 를 선택된 채로 두고 Visual Studio에서 앱 서비스 웹앱을 프로비전하는 예제는 [ASP.NET 및 Visual Studio를 사용하여 Azure 앱 서비스에서 웹앱 시작하기](../app-service-web/web-sites-dotnet-get-started.md)를 참조하세요.
   
    ![프로젝트 템플릿 선택][cache-select-template]
4. **확인** 을 클릭하여 프로젝트를 만듭니다.

## <a name="create-the-aspnet-mvc-application"></a>ASP.NET MVC 응용 프로그램 만들기
자습서의 이 섹션에서는 데이터베이스에서 팀 통계를 읽고 표시하는 기본 응용 프로그램을 만듭니다.

* [Entity Framework NuGet 패키지 추가](#add-the-entity-framework-nuget-package)
* [모델 추가](#add-the-model)
* [컨트롤러 추가](#add-the-controller)
* [보기 구성](#configure-the-views)

### <a name="add-the-entity-framework-nuget-package"></a>Entity Framework NuGet 패키지 추가

1. **도구** 메뉴에서 **NuGet 패키지 관리자**, **패키지 관리자 콘솔**을 차례로 클릭합니다.
2. `Package Manager Console` 창에서 다음 명령을 실행합니다.
    
    ```
    Install-Package EntityFramework
    ```

이 패키지에 대한 자세한 내용은 [EntityFramework](https://www.nuget.org/packages/EntityFramework/) NuGet 페이지를 참조하세요.

### <a name="add-the-model"></a>모델 추가
1. **솔루션 탐색기**에서 **모델**을 마우스 오른쪽 단추로 클릭하고 **추가**, **클래스**를 선택합니다. 
   
    ![모델 추가][cache-model-add-class]
2. 클래스 이름으로 `Team` 을 입력하고 **추가**를 클릭합니다.
   
    ![모델 클래스 추가][cache-model-add-class-dialog]
3. `Team.cs` 파일의 위쪽에 있는 `using` 문을 다음 `using` 문으로 바꿉니다.

    ```c#
    using System;
    using System.Collections.Generic;
    using System.Data.Entity;
    using System.Data.Entity.SqlServer;
    ```


1. `Team` 클래스의 정의를 업데이트된 `Team` 클래스 정의뿐만 아니라 몇몇 다른 Entity Framework 도우미 클래스도 포함하고 있는 다음 코드 조각으로 바꿉니다. 이 자습서에 사용되는 Entity Framework에 대한 Code First 접근방식에 대한 자세한 내용은 [새 데이터베이스에 대한 Code First](https://msdn.microsoft.com/data/jj193542)를 참조하세요.

    ```c#
    public class Team
    {
        public int ID { get; set; }
        public string Name { get; set; }
        public int Wins { get; set; }
        public int Losses { get; set; }
        public int Ties { get; set; }
    
        static public void PlayGames(IEnumerable<Team> teams)
        {
            // Simple random generation of statistics.
            Random r = new Random();
    
            foreach (var t in teams)
            {
                t.Wins = r.Next(33);
                t.Losses = r.Next(33);
                t.Ties = r.Next(0, 5);
            }
        }
    }
    
    public class TeamContext : DbContext
    {
        public TeamContext()
            : base("TeamContext")
        {
        }
    
        public DbSet<Team> Teams { get; set; }
    }
    
    public class TeamInitializer : CreateDatabaseIfNotExists<TeamContext>
    {
        protected override void Seed(TeamContext context)
        {
            var teams = new List<Team>
            {
                new Team{Name="Adventure Works Cycles"},
                new Team{Name="Alpine Ski House"},
                new Team{Name="Blue Yonder Airlines"},
                new Team{Name="Coho Vineyard"},
                new Team{Name="Contoso, Ltd."},
                new Team{Name="Fabrikam, Inc."},
                new Team{Name="Lucerne Publishing"},
                new Team{Name="Northwind Traders"},
                new Team{Name="Consolidated Messenger"},
                new Team{Name="Fourth Coffee"},
                new Team{Name="Graphic Design Institute"},
                new Team{Name="Nod Publishers"}
            };
    
            Team.PlayGames(teams);
    
            teams.ForEach(t => context.Teams.Add(t));
            context.SaveChanges();
        }
    }
    
    public class TeamConfiguration : DbConfiguration
    {
        public TeamConfiguration()
        {
            SetExecutionStrategy("System.Data.SqlClient", () => new SqlAzureExecutionStrategy());
        }
    }
    ```


1. **솔루션 탐색기**에서 **web.config**를 두 번 클릭하여 엽니다.
   
    ![Web.config][cache-web-config]
2. 다음 `connectionStrings` 섹션을 추가합니다. 연결 문자열의 이름은 `TeamContext`인 Entity Framework 데이터베이스 컨텍스트 클래스의 이름과 일치해야 합니다.

    ```xml
    <connectionStrings>
        <add name="TeamContext" connectionString="Data Source=(LocalDB)\v11.0;AttachDbFilename=|DataDirectory|\Teams.mdf;Integrated Security=True"     providerName="System.Data.SqlClient" />
    </connectionStrings>
    ```

    새 `connectionStrings` 섹션을 추가하여 다음 예제와 같이 `configSections` 뒤에 오도록 할 수 있습니다.

    ```xml
    <configuration>
      <configSections>
        <!-- For more information on Entity Framework configuration, visit http://go.microsoft.com/fwlink/?LinkID=237468 -->
        <section name="entityFramework" type="System.Data.Entity.Internal.ConfigFile.EntityFrameworkSection, EntityFramework, Version=6.0.0.0, Culture=neutral, PublicKeyToken=b77a5c561934e089" requirePermission="false" />
      </configSections>
      <connectionStrings>
        <add name="TeamContext" connectionString="Data Source=(LocalDB)\v11.0;AttachDbFilename=|DataDirectory|\Teams.mdf;Integrated Security=True"     providerName="System.Data.SqlClient" />
      </connectionStrings>
      ...
      ```

### <a name="add-the-controller"></a>컨트롤러 추가
1. **F6** 을 눌러 프로젝트를 빌드합니다. 
2. **솔루션 탐색기**에서 **컨트롤러** 폴더를 마우스 오른쪽 단추로 클릭하고 **추가**, **컨트롤러**를 클릭합니다.
   
    ![컨트롤러 추가][cache-add-controller]
3. **Entity Framework를 사용하여 보기가 포함된 MVC 5 컨트롤러**를 선택하고 **추가**를 클릭합니다. **추가**를 클릭한 후 오류가 발생하면 먼저 프로젝트를 빌드했는지 확인합니다.
   
    ![컨트롤러 클래스 추가][cache-add-controller-class]
4. **모델 클래스** 드롭다운 목록에서 **팀(ContosoTeamStats.Models)**을 선택합니다. **데이터 컨텍스트 클래스** 드롭다운 목록에서 **TeamContext(ContosoTeamStats.Models)**를 선택합니다. **컨트롤러** 이름 텍스트 상자에 `TeamsController`를 입력합니다(자동으로 채워지지 않은 경우). **추가** 를 클릭하여 컨트롤러 클래스를 만들고 기본 보기를 추가합니다.
   
    ![컨트롤러 구성][cache-configure-controller]
5. **솔루션 탐색기**에서 **Global.asax**를 확장하고 **Global.asax.cs**를 두 번 클릭하여 엽니다.
   
    ![Global.asax.cs][cache-global-asax]
6. 파일의 위쪽에 있는 다음 두 `using` 문을 다른 `using` 문 아래에 추가합니다.

    ```c#
    using System.Data.Entity;
    using ContosoTeamStats.Models;
    ```


1. 다음 코드 줄을 `Application_Start` 메서드의 끝에 추가합니다.

    ```c#
    Database.SetInitializer<TeamContext>(new TeamInitializer());
    ```


1. **솔루션 탐색기**에서 `App_Start`를 확장하고 `RouteConfig.cs`를 두 번 클릭합니다.
   
    ![RouteConfig.cs][cache-RouteConfig-cs]
2. 다음 예제와 같이 `RegisterRoutes` 메서드의 다음 코드에 있는 `controller = "Home"`을 `controller = "Teams"`로 바꿉니다.

    ```c#
    routes.MapRoute(
        name: "Default",
        url: "{controller}/{action}/{id}",
        defaults: new { controller = "Teams", action = "Index", id = UrlParameter.Optional }
    );
    ```


### <a name="configure-the-views"></a>보기 구성
1. **솔루션 탐색기**에서 **보기** 폴더, **공유** 폴더를 차례로 확장하고 **_Layout.cshtml**를 두 번 클릭합니다. 
   
    ![_Layout.cshtml][cache-layout-cshtml]
2. 다음 예제와 같이 `title` 요소의 내용을 변경하고 `My ASP.NET Application`을 `Contoso Team Stats`로 바꿉니다.

    ```html
    <title>@ViewBag.Title - Contoso Team Stats</title>
    ```


1. `body` 섹션에서 첫 번째 `Html.ActionLink` 문을 업데이트하고 `Application name`을 `Contoso Team Stats`로 바꾸고 `Home`을 `Teams`로 바꿉니다.
   
   * 이전: `@Html.ActionLink("Application name", "Index", "Home", new { area = "" }, new { @class = "navbar-brand" })`
   * 이후: `@Html.ActionLink("Contoso Team Stats", "Index", "Teams", new { area = "" }, new { @class = "navbar-brand" })`
     
     ![코드 변경 내용][cache-layout-cshtml-code]
2. **Ctrl+F5** 키를 눌러 응용 프로그램을 빌드 및 실행합니다. 이 버전의 응용 프로그램이 데이터베이스에서 직접 결과를 읽습니다. 참고로 **새로 만들기**, **편집**, **세부 정보** 및 **삭제** 작업은 응용 프로그램에 **Entity Framework를 사용하는 보기 포함 MVC 5 컨트롤러** 스캐폴드에 의해 자동으로 추가되었습니다. 자습서의 다음 섹션에서는 데이터 액세스를 최적화하고 응용 프로그램에 추가 기능을 제공하기 위해 Redis Cache를 추가합니다.

![시작 응용 프로그램][cache-starter-application]

## <a name="configure-the-application-to-use-redis-cache"></a>Redis Cache를 사용하도록 응용 프로그램 구성
자습서의 이 섹션에서는 [StackExchange.Redis](https://github.com/StackExchange/StackExchange.Redis) 캐시 클라이언트를 사용하여 Azure Redis Cache 인스턴스에서 Contoso 팀 통계를 저장 및 검색하도록 샘플 응용 프로그램을 구성합니다.

* [StackExchange.Redis를 사용하도록 응용 프로그램 구성](#configure-the-application-to-use-stackexchangeredis)
* [캐시 또는 데이터베이스에서 결과를 반환하도록 TeamsController 클래스 업데이트](#update-the-teamscontroller-class-to-return-results-from-the-cache-or-the-database)
* [캐시로 작업하도록 Create, Edit 및 Delete 메서드 업데이트](#update-the-create-edit-and-delete-methods-to-work-with-the-cache)
* [캐시로 작업하도록 팀 인덱스 보기 업데이트](#update-the-teams-index-view-to-work-with-the-cache)

### <a name="configure-the-application-to-use-stackexchangeredis"></a>StackExchange.Redis를 사용하도록 응용 프로그램 구성
1. StackExchange.Redis NuGet 패키지를 사용하여 Visual Studio에서 클라이언트 응용 프로그램을 구성하려면 **도구** 메뉴에서 **NuGet 패키지 관리자**, **패키지 관리자 콘솔**을 클릭합니다.
2. `Package Manager Console` 창에서 다음 명령을 실행합니다.
    
    ```
    Install-Package StackExchange.Redis
    ```
   
    NuGet 패키지는 클라이언트 응용 프로그램이 StackExchange.Redis 캐시 클라이언트를 사용하여 Azure Redis 캐시에 액세스하는 는 데 필요한 어셈블리 참조를 다운로드하고 추가합니다. 강력한 이름의 `StackExchange.Redis` 클라이언트 라이브러리 버전을 사용하려는 경우 `StackExchange.Redis.StrongName` 패키지를 설치합니다.
3. **솔루션 탐색기**에서 **컨트롤러** 폴더를 확장하고 **TeamsController.cs**를 두 번 클릭하여 엽니다.
   
    ![팀 컨트롤러][cache-teamscontroller]
4. **TeamsController.cs**에 다음 두 `using` 문을 추가합니다.

    ```c#   
    using System.Configuration;
    using StackExchange.Redis;
    ```

5. `TeamsController` 클래스에 다음 두 속성을 추가합니다.

    ```c#   
    // Redis Connection string info
    private static Lazy<ConnectionMultiplexer> lazyConnection = new Lazy<ConnectionMultiplexer>(() =>
    {
        string cacheConnection = ConfigurationManager.AppSettings["CacheConnection"].ToString();
        return ConnectionMultiplexer.Connect(cacheConnection);
    });
    
    public static ConnectionMultiplexer Connection
    {
        get
        {
            return lazyConnection.Value;
        }
    }
    ```

6. 컴퓨터에 `WebAppPlusCacheAppSecrets.config` 라는 파일을 만들고 이 파일을 다른 위치에서 체크인하기로 결정한 경우 샘플 응용 프로그램의 소스 코드에서 체크인하지 않을 위치에 배치합니다. 이 예제에서 `AppSettingsSecrets.config` 파일은 `C:\AppSecrets\WebAppPlusCacheAppSecrets.config`에 위치합니다.
   
    `WebAppPlusCacheAppSecrets.config` 파일을 편집하여 다음 내용을 추가합니다. 응용 프로그램을 로컬로 실행하는 경우 이 정보는 Azure Redis Cache 인스턴스를 연결하는 데 사용됩니다. 이 자습서의 뒷부분에서는 Azure Redis Cache 인스턴스를 프로비전하고 캐시 이름 및 암호를 업데이트합니다. 샘플 응용 프로그램을 로컬로 실행할 계획이 아닌 경우, Azure에 배포할 때 응용 프로그램이 이 파일이 아닌 웹앱에 대한 앱 설정에서 캐시 연결 정보를 검색하므로 이 파일 만들기 및 해당 파일을 참조하는 이후 단계를 건너뛸 수 있습니다. `WebAppPlusCacheAppSecrets.config` 는 응용 프로그램과 함께Azure에 배포되지 않으므로 응용 프로그램을 로컬로 실행하려는 경우가 아니면 필요하지 않습니다.

    ```xml
    <appSettings>
      <add key="CacheConnection" value="MyCache.redis.cache.windows.net,abortConnect=false,ssl=true,password=..."/>
    </appSettings>
    ```


1. **솔루션 탐색기**에서 **web.config**를 두 번 클릭하여 엽니다.
   
    ![Web.config][cache-web-config]
2. 다음 `file` 속성을 `appSettings` 요소에 추가합니다. 다른 파일 이름 또는 위치를 사용한 경우, 예제에 나타난 값을 해당 값으로 대체합니다.
   
   * 이전: `<appSettings>`
   * 이후: ` <appSettings file="C:\AppSecrets\WebAppPlusCacheAppSecrets.config">`
     
   ASP.NET 런타임은 외부 파일의 내용을 `<appSettings>` 요소의 태그와 병합합니다. 지정된 파일을 찾을 수 없는 경우 런타임에서 파일 특성을 무시합니다. 암호(캐시에 대한 연결 문자열)는 응용 프로그램에 대 한 소스 코드의 일부분으로 포함되지 않습니다. Azure에 웹앱을 배포하는 경우 `WebAppPlusCacheAppSecrests.config` 파일은 배포되지 않습니다(즉, 원하는 상태임). Azure에서 이러한 암호를 지정하는 여러 가지 방법이 있으며 이 자습서에서는 이후 단계에서 [Azure 리소스를 프로비전](#provision-the-azure-resources) 할 때 암호가 자동으로 구성됩니다. Azure에서 암호로 작업에 대한 자세한 내용은 [ASP.NET 및 Azure 앱 서비스에 암호 및 기타 중요한 데이터 배포를 위한 모범 사례](http://www.asp.net/identity/overview/features-api/best-practices-for-deploying-passwords-and-other-sensitive-data-to-aspnet-and-azure)를 참조하세요.

### <a name="update-the-teamscontroller-class-to-return-results-from-the-cache-or-the-database"></a>캐시 또는 데이터베이스에서 결과를 반환하도록 TeamsController 클래스 업데이트
이 샘플에서는 데이터베이스 또는 캐시에서 팀 통계를 검색할 수 있습니다. 팀 통계는 직렬화된 `List<Team>`으로 캐시에 저장되며 Redis 데이터 유형을 사용하여 정렬된 집합으로도 저장됩니다. 정렬된 집합에서 항목을 검색하는 경우 일부 또는 모두 검색하거나 특정 항목을 쿼리할 수 있습니다. 이 샘플에서는 정렬된 집합에서 승리 횟수 순으로 상위 5개 팀을 쿼리합니다.

> [!NOTE]
> Azure Redis Cache를 사용하려는 경우 여러 형식의 팀 통계를 캐시에 저장할 필요가 없습니다. 이 자습서에서는 여러 형식을 사용하여 데이터를 캐시하는 데 사용할 수 있는 몇 가지 방법 및 여러 가지 데이터 형식을 보여 줍니다.
> 
> 

1. 다음 `using` 문을 다른 `using` 문과 함께 위쪽의 `TeamsController.cs` 파일에 추가합니다.

    ```c#   
    using System.Diagnostics;
    using Newtonsoft.Json;
    ```

2. 현재 `public ActionResult Index()` 메서드 구현을 다음 구현으로 바꿉니다.

    ```c#
    // GET: Teams
    public ActionResult Index(string actionType, string resultType)
    {
        List<Team> teams = null;

        switch(actionType)
        {
            case "playGames": // Play a new season of games.
                PlayGames();
                break;

            case "clearCache": // Clear the results from the cache.
                ClearCachedTeams();
                break;

            case "rebuildDB": // Rebuild the database with sample data.
                RebuildDB();
                break;
        }

        // Measure the time it takes to retrieve the results.
        Stopwatch sw = Stopwatch.StartNew();

        switch(resultType)
        {
            case "teamsSortedSet": // Retrieve teams from sorted set.
                teams = GetFromSortedSet();
                break;

            case "teamsSortedSetTop5": // Retrieve the top 5 teams from the sorted set.
                teams = GetFromSortedSetTop5();
                break;

            case "teamsList": // Retrieve teams from the cached List<Team>.
                teams = GetFromList();
                break;

            case "fromDB": // Retrieve results from the database.
            default:
                teams = GetFromDB();
                break;
        }

        sw.Stop();
        double ms = sw.ElapsedTicks / (Stopwatch.Frequency / (1000.0));

        // Add the elapsed time of the operation to the ViewBag.msg.
        ViewBag.msg += " MS: " + ms.ToString();

        return View(teams);
    }
    ```


1. 이전 코드 조각에 추가된 switch 문에서 `playGames`, `clearCache` 및 `rebuildDB` 작업을 구현하기 위해 다음 세 가지 메서드를 `TeamsController` 클래스에 추가합니다.
   
    `PlayGames` 메서드는 게임 시즌을 시뮬레이션하여 팀 통계를 업데이트하고 결과를 데이터베이스에 저장하고 이제 만료된 데이터를 캐시에서 지웁니다.

    ```c#
    void PlayGames()
    {
        ViewBag.msg += "Updating team statistics. ";
        // Play a "season" of games.
        var teams = from t in db.Teams
                    select t;

        Team.PlayGames(teams);

        db.SaveChanges();

        // Clear any cached results
        ClearCachedTeams();
    }
    ```

    `RebuildDB` 메서드는 데이터베이스를 기본 팀 집합으로 다시 초기화하고 그에 대한 통계를 생성하고 이제 만료된 데이터를 캐시에서 지웁니다.

    ```c#
    void RebuildDB()
    {
        ViewBag.msg += "Rebuilding DB. ";
        // Delete and re-initialize the database with sample data.
        db.Database.Delete();
        db.Database.Initialize(true);

        // Clear any cached results
        ClearCachedTeams();
    }
    ```

    `ClearCachedTeams` 메서드는 캐시된 모든 팀 통계를 캐시에서 제거합니다.

    ```c#
    void ClearCachedTeams()
    {
        IDatabase cache = Connection.GetDatabase();
        cache.KeyDelete("teamsList");
        cache.KeyDelete("teamsSortedSet");
        ViewBag.msg += "Team data removed from cache. ";
    } 
    ```


1. 캐시 및 데이터베이스에서 팀 통계를 검색하는 여러 가지 방법을 구현하기 위해 다음 네 가지 메서드를 `TeamsController` 클래스에 추가합니다. 이러한 각 메서드는 `List<Team>` 를 반환하며 이는 보기에 의해 표시됩니다.
   
    `GetFromDB` 메서드는 팀 통계를 데이터베이스에서 읽습니다.
   
    ```c#
    List<Team> GetFromDB()
    {
        ViewBag.msg += "Results read from DB. ";
        var results = from t in db.Teams
            orderby t.Wins descending
            select t; 

        return results.ToList<Team>();
    }
    ```

    `GetFromList` 메서드는 팀 통계를 직렬화된 `List<Team>`으로 캐시에서 읽습니다. 캐시 누락이 있는 경우 팀 통계를 데이터베이스에서 읽은 후 다음에 캐시에 저장합니다. 이 샘플에서는 JSON.NET 직렬화를 사용하여 .NET 개체를 캐시에 대해 직렬화합니다. 자세한 내용은 [Azure Redis Cache에서 .NET 개체로 작업하는 방법](cache-dotnet-how-to-use-azure-redis-cache.md#work-with-net-objects-in-the-cache)을 참조하세요.

    ```c#
    List<Team> GetFromList()
    {
        List<Team> teams = null;

        IDatabase cache = Connection.GetDatabase();
        string serializedTeams = cache.StringGet("teamsList");
        if (!String.IsNullOrEmpty(serializedTeams))
        {
            teams = JsonConvert.DeserializeObject<List<Team>>(serializedTeams);

            ViewBag.msg += "List read from cache. ";
        }
        else
        {
            ViewBag.msg += "Teams list cache miss. ";
            // Get from database and store in cache
            teams = GetFromDB();

            ViewBag.msg += "Storing results to cache. ";
            cache.StringSet("teamsList", JsonConvert.SerializeObject(teams));
        }
        return teams;
    }
    ```

    `GetFromSortedSet` 메서드는 캐시된 정렬된 집합에서 팀 통계를 읽습니다. 캐시 누락이 있는 경우 팀 통계를 데이터베이스에서 읽은 후 정렬된 집합으로 캐시에 저장합니다.

    ```c#
    List<Team> GetFromSortedSet()
    {
        List<Team> teams = null;
        IDatabase cache = Connection.GetDatabase();
        // If the key teamsSortedSet is not present, this method returns a 0 length collection.
        var teamsSortedSet = cache.SortedSetRangeByRankWithScores("teamsSortedSet", order: Order.Descending);
        if (teamsSortedSet.Count() > 0)
        {
            ViewBag.msg += "Reading sorted set from cache. ";
            teams = new List<Team>();
            foreach (var t in teamsSortedSet)
            {
                Team tt = JsonConvert.DeserializeObject<Team>(t.Element);
                teams.Add(tt);
            }
        }
        else
        {
            ViewBag.msg += "Teams sorted set cache miss. ";

            // Read from DB
            teams = GetFromDB();

            ViewBag.msg += "Storing results to cache. ";
            foreach (var t in teams)
            {
                Console.WriteLine("Adding to sorted set: {0} - {1}", t.Name, t.Wins);
                cache.SortedSetAdd("teamsSortedSet", JsonConvert.SerializeObject(t), t.Wins);
            }
        }
        return teams;
    }
    ```

    `GetFromSortedSetTop5` 메서드는 캐시된 정렬된 집합에서 상위 5개 팀을 읽습니다. 먼저 캐시에서 `teamsSortedSet` 키의 유무를 확인합니다. 이 키가 없는 경우 `GetFromSortedSet` 메서드를 호출하여 팀 통계를 읽고 캐시에 저장합니다. 그런 다음 캐시된 정렬된 집합에 대해 반환된 상위 5개 팀을 쿼리합니다.

    ```c#
    List<Team> GetFromSortedSetTop5()
    {
        List<Team> teams = null;
        IDatabase cache = Connection.GetDatabase();

        // If the key teamsSortedSet is not present, this method returns a 0 length collection.
        var teamsSortedSet = cache.SortedSetRangeByRankWithScores("teamsSortedSet", stop: 4, order: Order.Descending);
        if(teamsSortedSet.Count() == 0)
        {
            // Load the entire sorted set into the cache.
            GetFromSortedSet();

            // Retrieve the top 5 teams.
            teamsSortedSet = cache.SortedSetRangeByRankWithScores("teamsSortedSet", stop: 4, order: Order.Descending);
        }

        ViewBag.msg += "Retrieving top 5 teams from cache. ";
        // Get the top 5 teams from the sorted set
        teams = new List<Team>();
        foreach (var team in teamsSortedSet)
        {
            teams.Add(JsonConvert.DeserializeObject<Team>(team.Element));
        }
        return teams;
    }
    ```

### <a name="update-the-create-edit-and-delete-methods-to-work-with-the-cache"></a>캐시로 작업하도록 Create, Edit 및 Delete 메서드 업데이트
이 샘플의 일부로 생성된 스캐폴딩 코드는 팀을 추가, 편집 및 삭제하는 메서드를 포함하고 있습니다. 팀이 추가, 편집 또는 제거될 때마다 캐시의 데이터가 만료됩니다. 이 섹션에서는 캐시와 데이터베이스의 동기화가 해제되지 않도록 캐시된 팀을 지우도록 이러한 세 가지 메서드를 수정합니다.

1. `TeamsController` 클래스의 `Create(Team team)` 메서드를 찾아갑니다. 다음 예제와 같이 `ClearCachedTeams` 메서드 호출을 추가합니다.

    ```c#
    // POST: Teams/Create
    // To protect from overposting attacks, please enable the specific properties you want to bind to, for 
    // more details see http://go.microsoft.com/fwlink/?LinkId=317598.
    [HttpPost]
    [ValidateAntiForgeryToken]
    public ActionResult Create([Bind(Include = "ID,Name,Wins,Losses,Ties")] Team team)
    {
        if (ModelState.IsValid)
        {
            db.Teams.Add(team);
            db.SaveChanges();
            // When a team is added, the cache is out of date.
            // Clear the cached teams.
            ClearCachedTeams();
            return RedirectToAction("Index");
        }

        return View(team);
    }
    ```


1. `TeamsController` 클래스의 `Edit(Team team)` 메서드를 찾아갑니다. 다음 예제와 같이 `ClearCachedTeams` 메서드 호출을 추가합니다.

    ```c#
    // POST: Teams/Edit/5
    // To protect from overposting attacks, please enable the specific properties you want to bind to, for 
    // more details see http://go.microsoft.com/fwlink/?LinkId=317598.
    [HttpPost]
    [ValidateAntiForgeryToken]
    public ActionResult Edit([Bind(Include = "ID,Name,Wins,Losses,Ties")] Team team)
    {
        if (ModelState.IsValid)
        {
            db.Entry(team).State = EntityState.Modified;
            db.SaveChanges();
            // When a team is edited, the cache is out of date.
            // Clear the cached teams.
            ClearCachedTeams();
            return RedirectToAction("Index");
        }
        return View(team);
    }
    ```


1. `TeamsController` 클래스의 `DeleteConfirmed(int id)` 메서드를 찾아갑니다. 다음 예제와 같이 `ClearCachedTeams` 메서드 호출을 추가합니다.

    ```c#
    // POST: Teams/Delete/5
    [HttpPost, ActionName("Delete")]
    [ValidateAntiForgeryToken]
    public ActionResult DeleteConfirmed(int id)
    {
        Team team = db.Teams.Find(id);
        db.Teams.Remove(team);
        db.SaveChanges();
        // When a team is deleted, the cache is out of date.
        // Clear the cached teams.
        ClearCachedTeams();
        return RedirectToAction("Index");
    }
    ```


### <a name="update-the-teams-index-view-to-work-with-the-cache"></a>캐시로 작업하도록 팀 인덱스 보기 업데이트
1. **솔루션 탐색기**에서 **보기** 폴더, **팀** 폴더를 차례로 확장한 다음 **Index.cshtml**을 두 번 클릭합니다.
   
    ![Index.cshtml][cache-views-teams-index-cshtml]
2. 파일의 위쪽 부근에서 다음 단락 요소를 찾습니다.
   
    ![작업 테이블][cache-teams-index-table]
   
    새 팀을 만들 수 있는 링크입니다. 단락 요소를 다음 테이블로 바꿉니다. 이 테이블에는 새 게임 시즌 실행, 캐시 지우기, 여러 형식의 캐시에서 팀 검색, 데이터베이스에서 팀 검색 및 새 샘플 데이터를 사용하여 데이터베이스 다시 빌드를 수행할 수 있는 작업 링크가 있습니다.

    ```html
    <table class="table">
        <tr>
            <td>
                @Html.ActionLink("Create New", "Create")
            </td>
            <td>
                @Html.ActionLink("Play Season", "Index", new { actionType = "playGames" })
            </td>
            <td>
                @Html.ActionLink("Clear Cache", "Index", new { actionType = "clearCache" })
            </td>
            <td>
                @Html.ActionLink("List from Cache", "Index", new { resultType = "teamsList" })
            </td>
            <td>
                @Html.ActionLink("Sorted Set from Cache", "Index", new { resultType = "teamsSortedSet" })
            </td>
            <td>
                @Html.ActionLink("Top 5 Teams from Cache", "Index", new { resultType = "teamsSortedSetTop5" })
            </td>
            <td>
                @Html.ActionLink("Load from DB", "Index", new { resultType = "fromDB" })
            </td>
            <td>
                @Html.ActionLink("Rebuild DB", "Index", new { actionType = "rebuildDB" })
            </td>
        </tr>    
    </table>
    ```


1. **Index.cshtml** 파일의 아래쪽으로 스크롤하고 다음 `tr` 요소를 파일의 마지막 테이블에서 마지막 행이 되도록 추가합니다.
   
    ```html
    <tr><td colspan="5">@ViewBag.Msg</td></tr>
    ```
   
    이 행은 현재 작업에 대한 상태 보고서를 포함하는 `ViewBag.Msg` 값을 표시합니다. `ViewBag.Msg`은 이전 단계에서 작업 링크를 클릭할 때 설정됩니다.   
   
    ![상태 메시지][cache-status-message]
2. **F6** 을 눌러 프로젝트를 빌드합니다.

## <a name="provision-the-azure-resources"></a>Azure 리소스를 프로비전
응용 프로그램을 Azure에서 호스트하려면 먼저 응용 프로그램에 필요한 Azure 서비스를 프로비전해야 합니다. 이 자습서의 샘플 응용 프로그램에서는 다음 Azure 서비스를 사용합니다.

* Azure Redis 캐시(영문)
* 앱 서비스 웹앱
* SQL 데이터베이스

선택한 새 또는 기존 리소스 그룹에 이러한 서비스를 배포하려면 다음 **Azure에 배포** 단추를 클릭합니다.

[![Azure에 배포][deploybutton]](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-web-app-redis-cache-sql-database%2Fazuredeploy.json)

이 **Azure에 배포** 단추는 [Web App 및 Redis Cache 및 SQL Database 만들기](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-redis-cache-sql-database) [Azure 빠른 시작](https://github.com/Azure/azure-quickstart-templates) 템플릿을 사용하여 이러한 서비스를 프로비전하고 SQL Database에 대한 연결 문자열을 설정하고 Azure Redis Cache 연결 문자열에 대한 응용 프로그램 설정을 지정합니다.

> [!NOTE]
> Azure 계정이 없는 경우 몇 분 만에 [무료 계정을 만들](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=redis_cache_hero) 수 있습니다.
> 
> 

**Azure에 배포** 단추를 클릭하면 Azure 포털로 이동하며 템플릿에서 설명한 리소스 만들기 과정을 시작합니다.

![Deploy to Azure][cache-deploy-to-azure-step-1]

1. **기본 사항** 섹션에서 사용할 Azure 구독을 선택하고 기존 리소스 그룹을 선택하거나 새 리소스 그룹을 만들고 리소스 그룹 위치를 지정합니다.
2. **설정** 섹션에서 **관리자 로그인**(**admin** 사용 안 함), **관리자 로그인 암호** 및 **데이터베이스 이름**을 지정합니다. 다른 매개 변수는 무료 앱 서비스 호스팅 계획을 위해 구성되며 더 낮은 비용 옵션은 SQL Database 및 무료 계층으로 제공되지 않는 Azure Redis Cache를 위해 구성됩니다.

    ![Deploy to Azure][cache-deploy-to-azure-step-2]

3. 원하는 설정을 구성한 후 페이지 끝으로 스크롤하여 사용 약관을 읽고 **위에 명시된 사용 약관에 동의함** 확인란을 선택합니다.
4. 리소스 프로비전을 시작하려면 **구매**를 클릭합니다.

배포의 진행률을 보려면 알림 아이콘을 클릭하고 **배포가 시작됨**을 클릭합니다.

![배포가 시작됨][cache-deployment-started]

**Microsoft.Template** 블레이드에서 배포의 상태를 볼 수 있습니다.

![Azure에 배포][cache-deploy-to-azure-step-3]

프로비전이 완료되면 Visual Studio에서 Azure에 응용 프로그램을 게시할 수 있습니다.

> [!NOTE]
> 프로비전 프로세스 중에 발생할 수 있는 오류는 **Microsoft.Template** 블레이드에 표시됩니다. 일반적인 오류는 너무 많은 SQL 서버 또는 구독에 따라 계획을 호스팅하는 너무 많은 무료 앱 서비스입니다. **Microsoft.Template** 블레이드의 **재배포** 또는 이 자습서의 **Azure에 배포** 단추를 클릭하여 오류를 해결하고 프로세스를 다시 시작합니다.
> 
> 

## <a name="publish-the-application-to-azure"></a>응용 프로그램을 Azure에 게시
자습서의 이 단계에서는 Azure에 응용 프로그램을 게시하고 클라우드에서 실행합니다.

1. Visual Studio에서 **ContosoTeamStats** 프로젝트를 마우스 오른쪽 단추로 클릭하고 **게시**를 선택합니다.
   
    ![게시][cache-publish-app]
2. **Microsoft Azure App Service**를 클릭하고 **기존 항목 선택**을 선택한 다음 **게시**를 클릭합니다.
   
    ![게시][cache-publish-to-app-service]
3. Azure 리소스를 만들 때 사용한 구독을 선택하고 리소스가 포함된 리소스 그룹을 확장하고 원하는 Web App을 선택합니다. **Azure에 배포** 단추를 사용한 경우 Web App 이름은 **webSite**로 시작하고 그 다음에 일부 추가 문자를 덧붙입니다.
   
    ![웹앱 선택][cache-select-web-app]
4. **확인**을 클릭하여 게시 프로세스를 시작합니다. 잠시 후 게시 프로세스가 완료되며 실행 중인 샘플 응용 프로그램과 함께 브라우저가 시작됩니다. 유효성 검사 또는 게시를 수행할 때 DNS 오류가 발생하고 응용 프로그램에 대한 Azure 리소스의 프로비저 프로세스가 최근에 완료된 경우 잠시 기다렸다가 다시 시도하세요.
   
    ![캐시 추가됨][cache-added-to-application]

다음 테이블은 샘플 응용 프로그램에서 각 작업 링크를 설명합니다.

| 작업 | 설명 |
| --- | --- |
| 새로 만들기 |새 팀을 만듭니다. |
| 시즌 재생 |게임 시즌을 재생하고 팀 통계를 업데이트하고 만료된 팀 데이터를 캐시에서 지웁니다. |
| 캐시 지우기 |캐시에서 팀 통계를 지웁니다. |
| 캐시에서 목록 |캐시에서 팀 통계를 검색합니다. 캐시 누락이 있는 경우 데이터베이스에서 통계를 로드하고 다음에 캐시에 저장합니다. |
| 캐시에서 정렬된 집합 |정렬된 집합을 사용하여 캐시에서 팀 통계를 검색합니다. 캐시 누락이 있는 경우 데이터베이스에서 통계를 로드하고 정렬된 집합을 사용하여 캐시에 저장합니다. |
| 캐시에서 상위 5개 팀 |정렬된 집합을 사용하여 캐시에서 상위 5개 팀을 검색합니다. 캐시 누락이 있는 경우 데이터베이스에서 통계를 로드하고 정렬된 집합을 사용하여 캐시에 저장합니다. |
| DB에서 로드 |데이터베이스에서 팀 통계를 검색합니다. |
| DB 다시 빌드 |데이터베이스를 다시 빌드하고 샘플 팀 데이터와 함께 다시 로드합니다. |
| 편집 / 세부 정보 / 삭제 |팀을 편집하고 팀에 대한 세부 정보를 보고 팀을 삭제합니다. |

일부 작업을 클릭하고 다른 출처의 데이터 검색을 실험합니다. 데이터베이스 및 캐시에서 데이터를 검색하는 여러 방법을 완료하는 데 걸리는 시간 차이는 없습니다.

## <a name="delete-the-resources-when-you-are-finished-with-the-application"></a>응용 프로그램을 마쳤으면 리소스를 삭제합니다.
샘플 자습서 응용 프로그램을 마쳤을 때 비용 및 리소스를 절감하기 위해 사용한 Azure 리소스를 삭제할 수 있습니다. **Azure 리소스** 섹션의 [Azure에 배포](#provision-the-azure-resources) 단추를 사용했고 모든 리소스를 같은 리소스 그룹에 포함시킨 경우 리소스 그룹을 삭제하여 한 번의 작업으로 이들을 모두 삭제할 수 있습니다.

1. [Azure 포털](https://portal.azure.com) 에 로그인하고 **리소스 그룹**을 클릭합니다.
2. **항목 필터링...** 텍스트 상자에 리소스 그룹의 이름을 입력합니다.
3. 리소스 그룹 오른쪽의 **...** 을 클릭합니다.
4. **삭제**를 클릭합니다.
   
    ![삭제][cache-delete-resource-group]
5. 리소스 그룹의 이름을 입력하고 **삭제**를 클릭합니다.
   
    ![삭제 확인][cache-delete-confirm]

잠시 후 리소스 그룹 및 해당 그룹에 포함된 모든 리소스가 삭제됩니다.

> [!IMPORTANT]
> 참고로 리소스 그룹 삭제는 취소할 수 없으며 해당 리소스 그룹 및 해당 그룹 안에 있는 모든 리소스는 영구적으로 삭제됩니다. 잘못된 리소스 그룹 또는 리소스를 자동으로 삭제하지 않도록 해야 합니다. 기존 리소스 그룹 내에 있는 이 샘플을 호스트하기 위한 리소스를 만든 경우 해당 블레이드에서 각 리소스를 개별적으로 삭제할 수 있습니다.
> 
> 

## <a name="run-the-sample-application-on-your-local-machine"></a>로컬 컴퓨터에서 샘플 응용 프로그램을 실행합니다.
응용 프로그램을 로컬로 실행하려면 데이터를 캐시하는 Azure Redis Cache 인스턴스가 필요합니다. 

* 이전 섹션에서 설명한 대로 응용 프로그램을 Azure에 게시한 경우 해당 단계에서 프로비전된 Azure Redis Cache 인스턴스를 사용할 수 있습니다.
* 다른 기존 Azure Redis Cache 인스턴스가 있는 경우 해당 인스턴스를 사용하여 이 샘플을 로컬로 실행할 수 있습니다.
* Azure Redis Cache 인스턴스를 만들어야 할 경우 [캐시 만들기](cache-dotnet-how-to-use-azure-redis-cache.md#create-a-cache)의 단계를 따를 수 있습니다.

사용할 캐시를 선택하거나 만든 후 Azure Portal에서 캐시를 찾아가고 캐시에 대해 [호스트 이름](cache-configure.md#properties) 및 [선택키](cache-configure.md#access-keys)를 검색합니다. 지침은 [Redis Cache 설정 구성](cache-configure.md#configure-redis-cache-settings)을 참조하세요.

1. 선택한 편집기를 사용하여 [Redis Cache를 사용하도록 응용 프로그램 구성](#configure-the-application-to-use-redis-cache) 단계 중에 만든 `WebAppPlusCacheAppSecrets.config` 파일을 엽니다.
2. `value` 특성을 편집하고 `MyCache.redis.cache.windows.net`을 캐시의 [호스트 이름](cache-configure.md#properties)으로 바꾸고 캐시의 [기본 또는 보조 키](cache-configure.md#access-keys)를 암호로 지정합니다.

    ```xml
    <appSettings>
      <add key="CacheConnection" value="MyCache.redis.cache.windows.net,abortConnect=false,ssl=true,password=..."/>
    </appSettings>
    ```


1. **Ctrl+F5** 를 눌러 응용 프로그램을 실행합니다.

> [!NOTE]
> 데이터베이스를 비롯한 응용 프로그램이 로컬로 실행되고 Redis Cache가 Azure에서 호스트되기 때문에 캐시는 데이터베이스의 성능을 저하시키는 것처럼 나타날 수 있습니다. 최상의 성능을 위해 클라이언트 응용 프로그램 및 Azure Redis Cache 인스턴스가 동일한 위치에 있어야 합니다. 
> 
> 

## <a name="next-steps"></a>다음 단계
* [ASP.NET](http://asp.net/) 사이트에서 [ASP.NET MVC 5로 시작](http://www.asp.net/mvc/overview/getting-started/introduction/getting-started)에 대해 더 자세히 알아봅니다.
* 앱 서비스에서 ASP.NET 웹앱을 만드는 방법의 더 많은 예제는 [HealthClinic.biz](https://github.com/Microsoft/HealthClinic.biz) 2015 Connect [데모](https://blogs.msdn.microsoft.com/visualstudio/2015/12/08/connectdemos-2015-healthclinic-biz/)에서 [Azure App Service에서 ASP.NET 웹앱 만들기 및 배포](https://github.com/Microsoft/HealthClinic.biz/wiki/Create-and-deploy-an-ASP.NET-web-app-in-Azure-App-Service)를 참조하세요.
  * HealthClinic.biz 데모에서 더 빠른 시작은 [Azure 개발자 도구 빠른 시작](https://github.com/Microsoft/HealthClinic.biz/wiki/Azure-Developer-Tools-Quickstarts)을 참조하세요.
* 이 자습서에 사용되는 Entity Framework에 대한 [새 데이터베이스에 Code First](https://msdn.microsoft.com/data/jj193542) 접근방식에 대해 더 자세히 알아봅니다.
* [Azure 앱 서비스의 웹앱](../app-service-web/app-service-web-overview.md)에 더 자세히 알아봅니다.
* Azure 포털에서 캐시를 [모니터링](cache-how-to-monitor.md) 하는 방법을 알아봅니다.
* Azure Redis Cache 프리미엄 계층 탐색
  
  * [프리미엄 Azure Redis Cache에 지속성을 구성하는 방법](cache-how-to-premium-persistence.md)
  * [프리미엄 Azure Redis Cache에 클러스터링을 구성하는 방법](cache-how-to-premium-clustering.md)
  * [프리미엄 Azure Redis Cache에 가상 네트워크 지원을 구성하는 방법](cache-how-to-premium-vnet.md)
  * 프리미엄 캐시에서의 크기, 처리량 및 대역폭에 대한 자세한 내용은 [Azure Redis Cache FAQ](cache-faq.md#what-redis-cache-offering-and-size-should-i-use) 를 참조하세요.

<!-- IMAGES -->
[cache-starter-application]: ./media/cache-web-app-howto/cache-starter-application.png
[cache-added-to-application]: ./media/cache-web-app-howto/cache-added-to-application.png
[cache-create-project]: ./media/cache-web-app-howto/cache-create-project.png
[cache-select-template]: ./media/cache-web-app-howto/cache-select-template.png
[cache-model-add-class]: ./media/cache-web-app-howto/cache-model-add-class.png
[cache-model-add-class-dialog]: ./media/cache-web-app-howto/cache-model-add-class-dialog.png
[cache-add-controller]: ./media/cache-web-app-howto/cache-add-controller.png
[cache-add-controller-class]: ./media/cache-web-app-howto/cache-add-controller-class.png
[cache-configure-controller]: ./media/cache-web-app-howto/cache-configure-controller.png
[cache-global-asax]: ./media/cache-web-app-howto/cache-global-asax.png
[cache-RouteConfig-cs]: ./media/cache-web-app-howto/cache-RouteConfig-cs.png
[cache-layout-cshtml]: ./media/cache-web-app-howto/cache-layout-cshtml.png
[cache-layout-cshtml-code]: ./media/cache-web-app-howto/cache-layout-cshtml-code.png
[redis-cache-manage-nuget-menu]: ./media/cache-web-app-howto/redis-cache-manage-nuget-menu.png
[redis-cache-stack-exchange-nuget]: ./media/cache-web-app-howto/redis-cache-stack-exchange-nuget.png
[cache-teamscontroller]: ./media/cache-web-app-howto/cache-teamscontroller.png
[cache-web-config]: ./media/cache-web-app-howto/cache-web-config.png
[cache-views-teams-index-cshtml]: ./media/cache-web-app-howto/cache-views-teams-index-cshtml.png
[cache-teams-index-table]: ./media/cache-web-app-howto/cache-teams-index-table.png
[cache-status-message]: ./media/cache-web-app-howto/cache-status-message.png
[deploybutton]: ./media/cache-web-app-howto/deploybutton.png
[cache-deploy-to-azure-step-1]: ./media/cache-web-app-howto/cache-deploy-to-azure-step-1.png
[cache-deploy-to-azure-step-2]: ./media/cache-web-app-howto/cache-deploy-to-azure-step-2.png
[cache-deploy-to-azure-step-3]: ./media/cache-web-app-howto/cache-deploy-to-azure-step-3.png
[cache-deployment-started]: ./media/cache-web-app-howto/cache-deployment-started.png
[cache-publish-app]: ./media/cache-web-app-howto/cache-publish-app.png
[cache-publish-to-app-service]: ./media/cache-web-app-howto/cache-publish-to-app-service.png
[cache-select-web-app]: ./media/cache-web-app-howto/cache-select-web-app.png
[cache-publish]: ./media/cache-web-app-howto/cache-publish.png
[cache-delete-resource-group]: ./media/cache-web-app-howto/cache-delete-resource-group.png
[cache-delete-confirm]: ./media/cache-web-app-howto/cache-delete-confirm.png


