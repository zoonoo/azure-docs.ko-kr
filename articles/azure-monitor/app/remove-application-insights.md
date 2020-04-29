---
title: Visual Studio에서 Application Insights 제거-Azure Monitor
description: Visual Studio에서 ASP.NET 및 ASP.NET Core에 대 한 Application Insights SDK를 제거 하는 방법입니다.
ms.topic: conceptual
ms.date: 04/06/2020
ms.openlocfilehash: 1c9ff8d3d305645ac7d113421e2c6c5f8451bd2b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "80805106"
---
# <a name="how-to-remove-application-insights-in-visual-studio"></a>Visual Studio에서 Application Insights를 제거 하는 방법

이 문서에서는 Visual Studio에서 ASP.NET 및 ASP.NET Core Application Insights SDK를 제거 하는 방법을 보여 줍니다.

Application Insights를 제거 하려면 응용 프로그램의 API에서 NuGet 패키지 및 참조를 제거 해야 합니다. 패키지 관리 콘솔을 사용 하거나 Visual Studio에서 NuGet 솔루션을 관리 하 여 NuGet 패키지를 제거할 수 있습니다. 다음 섹션에서는 NuGet 패키지를 제거 하는 두 가지 방법과 프로젝트에 자동으로 추가 된 항목을 보여 줍니다. API에 대 한 호출을 제거 하는 사용자 고유의 코드에서 추가 된 파일 및 영역을 확인 해야 합니다.

## <a name="uninstall-using-the-package-management-console"></a>패키지 관리 콘솔을 사용 하 여 제거

# <a name="net"></a>[.NET](#tab/net)

1. 패키지 관리 콘솔을 열려면 상단 메뉴에서 도구 > NuGet 패키지 관리자 > 패키지 관리자 콘솔을 선택 합니다.
     
    ![상단 메뉴에서 도구 > NuGet 패키지 관리자 > 패키지 관리자 콘솔을 클릭 합니다.](./media/remove-application-insights/package-manager.png)

    > [!NOTE]
    > 추적 컬렉션을 사용 하도록 설정한 경우 TraceListener를 먼저 제거 해야 합니다. Enter `Uninstall-package Microsoft.ApplicationInsights.TraceListener` 키를 누른 후 아래 단계를 수행 하 여 Microsoft Applicationinsights. 웹을 제거 합니다.

1. 다음 명령을 입력합니다. `Uninstall-Package Microsoft.ApplicationInsights.Web -RemoveDependencies`

    명령을 입력 하면 Application Insights 패키지와 모든 해당 종속성이 프로젝트에서 제거 됩니다.
    
    ![콘솔에 명령 입력](./media/remove-application-insights/package-management-console.png)

# <a name="net-core"></a>[.NET Core](#tab/netcore)

1. 패키지 관리 콘솔을 열려면 상단 메뉴에서 도구 > NuGet 패키지 관리자 > 패키지 관리자 콘솔을 선택 합니다.

    ![상단 메뉴에서 도구 > NuGet 패키지 관리자 > 패키지 관리자 콘솔을 클릭 합니다.](./media/remove-application-insights/package-manager.png)

1. 다음 명령을 입력합니다. ` Uninstall-Package Microsoft.ApplicationInsights.AspNetCore -RemoveDependencies`

    명령을 입력 하면 Application Insights 패키지와 모든 해당 종속성이 프로젝트에서 제거 됩니다.

---

## <a name="uninstall-using-the-visual-studio-nugetui"></a>Visual Studio NuGet UI를 사용 하 여 제거

# <a name="net"></a>[.NET](#tab/net)

1. 오른쪽의 *솔루션 탐색기* 에서 **솔루션** 을 마우스 오른쪽 단추로 클릭 하 고 **솔루션용 NuGet 패키지 관리**를 선택 합니다.

    그러면 프로젝트의 일부인 모든 NuGet 패키지를 편집할 수 있는 화면이 표시 됩니다.
    
     ![솔루션 탐색기에서 솔루션을 마우스 오른쪽 단추로 클릭 한 다음 솔루션에 대 한 NuGet 패키지 관리를 선택 합니다.](./media/remove-application-insights/manage-nuget-framework.png)

    > [!NOTE]
    > 추적 컬렉션을 사용 하도록 설정한 경우 종속성을 제거 하지 않고 TraceListener를 먼저 제거 해야 합니다. 그런 다음 아래 단계에 따라 종속성 제거를 선택 하 여 Microsoft-ApplicationInsights를 제거 합니다.
    
1. "Microsoft ApplicationInsights. 웹" 패키지를 클릭 합니다.오른쪽에서 *Project* 옆의 확인란을 선택 하 여 모든 프로젝트를 선택 합니다.
    
1. 제거할 때 모든 종속성을 제거 하려면 프로젝트를 선택한 섹션 아래에 있는 **옵션** 드롭다운 단추를 선택 합니다.

     *제거 옵션*에서 *종속성 제거*옆에 있는 확인란을 선택 합니다.

1. **제거**를 선택합니다.
    
    ![종속성 제거를 선택한 다음 제거를 선택 합니다.](./media/remove-application-insights/uninstall-framework.png)

    응용 프로그램에서 제거할 모든 종속성을 보여 주는 대화 상자가 표시 됩니다. **확인** 을 선택 하 여 제거 합니다.
    
    ![종속성 제거를 선택한 다음 제거를 선택 합니다.](./media/remove-application-insights/preview-uninstall-framework.png)
    
1.  모든 항목을 제거한 후에도 *솔루션 탐색기*에 "applicationinsights .config" 및 "AiHandleErrorAttribute.cs"가 표시 될 수 있습니다.두 파일을 수동으로 삭제할 수 있습니다.

# <a name="net-core"></a>[.NET Core](#tab/netcore)

1. 오른쪽의 *솔루션 탐색기* 에서 **솔루션** 을 마우스 오른쪽 단추로 클릭 하 고 **솔루션용 NuGet 패키지 관리**를 선택 합니다.

    그러면 프로젝트의 일부인 모든 NuGet 패키지를 편집할 수 있는 화면이 표시 됩니다.

    ![솔루션 탐색기에서 솔루션을 마우스 오른쪽 단추로 클릭 한 다음 솔루션에 대 한 NuGet 패키지 관리를 선택 합니다.](./media/remove-application-insights/manage-nuget-core.png)

1. "AspNetCore" 패키지를 클릭 합니다. 오른쪽에서 *Project* 옆의 확인란을 선택 하 여 모든 프로젝트를 선택 하 고 **제거**를 선택 합니다.

    ![종속성 제거를 선택한 다음 제거를 선택 합니다.](./media/remove-application-insights/uninstall-core.png)

---

## <a name="what-is-created-when-you-add-application-insights"></a>Application Insights를 추가할 때 생성 되는 내용

프로젝트에 Application Insights를 추가 하면 파일을 만들고 일부 파일에 코드를 추가 합니다. NuGet 패키지를 제거 하는 경우에만 파일 및 코드가 삭제 됩니다. Application Insights를 완전히 제거 하려면 추가 된 코드를 확인 하 고 프로젝트에 추가한 모든 API 호출과 함께 수동으로 삭제 해야 합니다.

# <a name="net"></a>[.NET](#tab/net)

Visual Studio ASP.NET 프로젝트에 Application Insights 원격 분석를 추가 하면 다음 파일이 추가 됩니다.

- ApplicationInsights.config
- AiHandleErrorAttribute.cs

다음과 같은 코드 조각이 추가 됩니다.

- [프로젝트의 이름] .csproj

    ```C#
     <ApplicationInsightsResourceId>/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/Default-ApplicationInsights-EastUS/providers/microsoft.insights/components/WebApplication4</ApplicationInsightsResourceId>
    ```

- 패키지 .config

    ```xml
    <packages>
    ...
    
      <package id="Microsoft.ApplicationInsights" version="2.12.0" targetFramework="net472" />
      <package id="Microsoft.ApplicationInsights.Agent.Intercept" version="2.4.0" targetFramework="net472" />
      <package id="Microsoft.ApplicationInsights.DependencyCollector" version="2.12.0" targetFramework="net472" />
      <package id="Microsoft.ApplicationInsights.PerfCounterCollector" version="2.12.0" targetFramework="net472" />
      <package id="Microsoft.ApplicationInsights.Web" version="2.12.0" targetFramework="net472" />
      <package id="Microsoft.ApplicationInsights.WindowsServer" version="2.12.0" targetFramework="net472" />
      <package id="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel" version="2.12.0" targetFramework="net472" />
    
      <package id="Microsoft.AspNet.TelemetryCorrelation" version="1.0.7" targetFramework="net472" />
    
      <package id="System.Buffers" version="4.4.0" targetFramework="net472" />
      <package id="System.Diagnostics.DiagnosticSource" version="4.6.0" targetFramework="net472" />
      <package id="System.Memory" version="4.5.3" targetFramework="net472" />
      <package id="System.Numerics.Vectors" version="4.4.0" targetFramework="net472" />
      <package id="System.Runtime.CompilerServices.Unsafe" version="4.5.2" targetFramework="net472" />
    ...
    </packages>
    ```

- Layout. cshtml

    프로젝트에 레이아웃이 있는 경우에는 아래 코드가 추가 됩니다.
    
    ```html
    <head>
    ...
        <script type = 'text/javascript' >
            var appInsights=window.appInsights||function(config)
            {
                function r(config){ t[config] = function(){ var i = arguments; t.queue.push(function(){ t[config].apply(t, i)})} }
                var t = { config:config},u=document,e=window,o='script',s=u.createElement(o),i,f;for(s.src=config.url||'//az416426.vo.msecnd.net/scripts/a/ai.0.js',u.getElementsByTagName(o)[0].parentNode.appendChild(s),t.cookie=u.cookie,t.queue=[],i=['Event','Exception','Metric','PageView','Trace','Ajax'];i.length;)r('track'+i.pop());return r('setAuthenticatedUserContext'),r('clearAuthenticatedUserContext'),config.disableExceptionTracking||(i='onerror',r('_'+i),f=e[i],e[i]=function(config, r, u, e, o) { var s = f && f(config, r, u, e, o); return s !== !0 && t['_' + i](config, r, u, e, o),s}),t
            }({
                instrumentationKey:'00000000-0000-0000-0000-000000000000'
            });
            
            window.appInsights=appInsights;
            appInsights.trackPageView();
        </script>
    ...
    </head>
    ```

- ConnectedService.json

    ```json
    {
      "ProviderId": "Microsoft.ApplicationInsights.ConnectedService.ConnectedServiceProvider",
      "Version": "16.0.0.0",
      "GettingStartedDocument": {
        "Uri": "https://go.microsoft.com/fwlink/?LinkID=613413"
      }
    }
    ```

- FilterConfig.cs

    ```csharp
            public static void RegisterGlobalFilters(GlobalFilterCollection filters)
            {
                filters.Add(new ErrorHandler.AiHandleErrorAttribute());// This line was added
            }
    ```

# <a name="net-core"></a>[.NET Core](#tab/netcore)

Visual Studio ASP.NET Core 템플릿 프로젝트에 Application Insights 원격 분석를 추가 하면 다음 코드가 추가 됩니다.

- [프로젝트의 이름] .csproj

    ```csharp
      <PropertyGroup>
        <TargetFramework>netcoreapp3.1</TargetFramework>
        <ApplicationInsightsResourceId>/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/Default-ApplicationInsights-EastUS/providers/microsoft.insights/components/WebApplication4core</ApplicationInsightsResourceId>
      </PropertyGroup>
    
      <ItemGroup>
        <PackageReference Include="Microsoft.ApplicationInsights.AspNetCore" Version="2.12.0" />
      </ItemGroup>
    
      <ItemGroup>
        <WCFMetadata Include="Connected Services" />
      </ItemGroup>
    ```

- Appsettings. json:

    ```json
    "ApplicationInsights": {
        "InstrumentationKey": "00000000-0000-0000-0000-000000000000"
    ```

- ConnectedService.json
    
    ```json
    {
      "ProviderId": "Microsoft.ApplicationInsights.ConnectedService.ConnectedServiceProvider",
      "Version": "16.0.0.0",
      "GettingStartedDocument": {
        "Uri": "https://go.microsoft.com/fwlink/?LinkID=798432"
      }
    }
    ```
- Startup.cs

    ```csharp
       public void ConfigureServices(IServiceCollection services)
            {
                services.AddRazorPages();
                services.AddApplicationInsightsTelemetry(); // This is added
            }
    ```

---

## <a name="next-steps"></a>다음 단계

- [Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/overview)