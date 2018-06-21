---
title: ASP.NET Core용 Azure Application Insights | Microsoft Docs
description: 응용 프로그램의 가용성, 성능 및 사용 현황을 모니터링합니다.
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.assetid: 3b722e47-38bd-4667-9ba4-65b7006c074c
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: conceptual
ms.date: 06/03/2018
ms.author: mbullwin
ms.openlocfilehash: 261bc78bfe427173ba81eef731e33eddd2ec379b
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/11/2018
ms.locfileid: "35294278"
---
# <a name="application-insights-for-aspnet-core"></a>ASP.NET Core용 Application Insights

Azure Application Insights는 코드 수준까지 웹 응용 프로그램의 심층 모니터링을 제공합니다. 웹 응용 프로그램의 가용성, 성능 및 사용량을 쉽게 모니터링할 수 있습니다. 또한 사용자가 보고할 때까지 기다리지 않고 응용 프로그램의 오류를 빠르게 식별하고 진단할 수 있습니다.

이 문서에서는 Visual Studio에서 샘플 ASP.NET Core [Razor Pages](https://docs.microsoft.com/aspnet/core/mvc/razor-pages/?tabs=visual-studio) 응용 프로그램을 만드는 과정과 Azure Application Insights로 모니터링을 시작하는 방법을 설명합니다.

## <a name="prerequisites"></a>필수 조건

- NET Core 2.0.0 SDK 이상
- ASP.NET 및 웹 개발 워크로드가 있는 [Visual Studio 2017](https://www.visualstudio.com/downloads/) 버전 15.7.3 이상입니다. 

## <a name="create-an-aspnet-core-project-in-visual-studio"></a>Visual Studio에서 ASP.NET Core 프로젝트 만들기

1. 마우스 오른쪽 단추를 클릭하고 관리자 권한으로 **Visual Studio 2017**을 시작합니다.
2. **파일** > **새로 만들기** > **프로젝트**를 선택합니다(Ctrl+Shift+N).

   ![Visual Studio 파일, 새 프로젝트 메뉴 스크린샷](./media/app-insights-asp-net-core/001-new-project.png)

3. **Visual C#** 을 확장하고 **.NET Core** > **ASP.NET Core 웹 응용 프로그램**을 선택합니다. **이름** > **솔루션 이름**을 입력하고 **새 Git 리포지토리 만들기**를 선택합니다.

   ![Visual Studio 파일, 새 프로젝트 마법사 스크린샷](./media/app-insights-asp-net-core/002-asp-net-core-web-application.png)

4. **.NET Core** > **ASP.NET Core 2.0** **웹 응용 프로그램** > **확인**을 선택합니다.

    ![Visual Studio 파일, 새 프로젝트 선택 메뉴 스크린샷](./media/app-insights-asp-net-core/003-web-application.png)

## <a name="application-insights-search"></a>Application Insights 검색

기본적으로 ASP.NET Core 2+ 기반 프로젝트가 있는 Visual studio 버전 2015 업데이트 2 이상에서는 명시적으로 Application Insights를 사용자의 프로젝트에 추가하기 전이라도 [Application Insights 검색](https://docs.microsoft.com/en-us/azure/application-insights/app-insights-visual-studio)을 활용할 수 있습니다.

이 기능을 테스트하려면.

1. IIS Express를 클릭하여 앱 실행 ![Visual Studio IIS Express 아이콘 스크린샷](./media/app-insights-asp-net-core/004-iis-express.png)

2. **보기** > **다른 Windows** > **Application Insights 검색**을 선택합니다.

   ![Visual Studio 진단 도구 스크린샷](./media/app-insights-asp-net-core/005-view-other-windows-search.png)

3. 디버그 세션 원격 분석은 현재 로컬 분석에 대해서만 사용할 수 있습니다. Application Insights를 완전히 사용하려면 상단 오른쪽에서 **원격 분석 준비**를 선택하거나 다음 섹션의 단계를 따릅니다.

   ![Visual Studio Application Insights 검색 스크린샷](./media/app-insights-asp-net-core/006-search.png)

> [!NOTE]
> Application Insights를 ASP.NET Core 프로젝트에 추가하기 전에 [Application Insights 검색](app-insights-visual-studio.md) 및 [CodeLens](app-insights-visual-studio-codelens.md) 같은 기능을 Visual Studio에서 켜는 방법에 대해 자세히 알려면 [이 문서의 끝](#Application-Insights-search-continued)에 있는 설명을 확인하십시오.

## <a name="add-application-insights-telemetry"></a>Application Insights 원격 분석 추가

1. **프로젝트** > **Application Insights 원격 분석 추가...** 를 선택합니다. (또는 **연결된 서비스**를 마우스 오른쪽 단추로 클릭하고 연결된 서비스 추가를 선택합니다.)

    ![Visual Studio 파일, 새 프로젝트 선택 메뉴 스크린샷](./media/app-insights-asp-net-core/007-project-add-telemetry.png)

2. **시작**을 선택합니다. (Visual Studio의 버전에 따라 텍스트는 약간 달라질 수 있습니다. 대신 일부 이전 버전에는 **무료로 시작** 단추가 있습니다.)

    ![Visual Studio 파일, 새 프로젝트 선택 메뉴 스크린샷](./media/app-insights-asp-net-core/008-get-started.png)

3. 적합한 **구독** > **리소스** > **등록**을 선택합니다.

## <a name="changes-made-to-your-project"></a>프로젝트 변경 내용

Application Insights는 오버헤드가 낮습니다. Application Insights 원격 분석을 추가하여 프로젝트에 대한 변경 내용을 검토하려면

**보기** > **팀 탐색기**(Ctrl +\, Ctrl+M) > **프로젝트** > **변경**을 선택합니다.

- 총 4개의 변경 내용:

  ![Application Insights를 추가하여 변경된 파일 스크린샷](./media/app-insights-asp-net-core/009-changes.png)

- 다음과 같은 1개의 새 파일이 만들어집니다.

   _ConnectedService.json_

```json
{
  "ProviderId": "Microsoft.ApplicationInsights.ConnectedService.ConnectedServiceProvider",
  "Version": "8.12.10405.1",
  "GettingStartedDocument": {
    "Uri": "https://go.microsoft.com/fwlink/?LinkID=798432"
  }
}
```

- 다음과 같은 3개 파일이 수정됩니다.(변경 내용을 강조 표시하기 위해 추가된 추가 설명)

  _appsettings.json_

```json
{
  "Logging": {
    "IncludeScopes": false,
    "LogLevel": {
      "Default": "Warning"
    }
  },
// Changes to file post adding Application Insights Telemetry:
  "ApplicationInsights": {
    "InstrumentationKey": "10101010-1010-1010-1010-101010101010"
  }
}
//
```

  _ContosoDotNetCore.csproj_

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
  <PropertyGroup>
    <TargetFramework>netcoreapp2.0</TargetFramework>
 <!--Changes to file post adding Application Insights Telemetry:-->
    <ApplicationInsightsResourceId>/subscriptions/2546c5a9-fa20-4de1-9f4a-62818b14b8aa/resourcegroups/Default-ApplicationInsights-EastUS/providers/microsoft.insights/components/DotNetCore</ApplicationInsightsResourceId>
    <ApplicationInsightsAnnotationResourceId>/subscriptions/2546c5a9-fa20-4de1-9f4a-62818b14b8aa/resourcegroups/Default-ApplicationInsights-EastUS/providers/microsoft.insights/components/DotNetCore</ApplicationInsightsAnnotationResourceId>
<!---->
  </PropertyGroup>
  <ItemGroup>
 <!--Changes to file post adding Application Insights Telemetry:-->
    <PackageReference Include="Microsoft.ApplicationInsights.AspNetCore" Version="2.1.1" />
<!---->
    <PackageReference Include="Microsoft.AspNetCore.All" Version="2.0.8" />
  </ItemGroup>
  <ItemGroup>
    <DotNetCliToolReference Include="Microsoft.VisualStudio.Web.CodeGeneration.Tools" Version="2.0.4" />
  </ItemGroup>
<!--Changes to file post adding Application Insights Telemetry:-->
  <ItemGroup>
    <WCFMetadata Include="Connected Services" />
  </ItemGroup>
<!---->
</Project>
```

   _Program.cs_

```csharp
using System;
using System.Collections.Generic;
using System.IO;
using System.Linq;
using System.Threading.Tasks;
using Microsoft.AspNetCore;
using Microsoft.AspNetCore.Hosting;
using Microsoft.Extensions.Configuration;
using Microsoft.Extensions.Logging;

namespace DotNetCore
{
    public class Program
    {
        public static void Main(string[] args)
        {
            BuildWebHost(args).Run();
        }

        public static IWebHost BuildWebHost(string[] args) =>
            WebHost.CreateDefaultBuilder(args)
// Change to file post adding Application Insights Telemetry:
                .UseApplicationInsights()
//
                .UseStartup<Startup>()
                .Build();
    }
}
```

## <a name="synthetic-transactions-with-powershell"></a>PowerShell 사용한 가상 트랜잭션

가상 트랜잭션을 사용하여 앱에 대한 요청을 자동화하려면.

1. IIS Express를 클릭하여 앱 실행 ![Visual Studio IIS Express 아이콘 스크린샷](./media/app-insights-asp-net-core/004-iis-express.png)

2. 브라우저 주소 표시줄에서 URL을 복사합니다. 형식 http://localhost:{random 포트 번호}

   ![브라우저의 URL 주소 표시줄 스크린샷](./media/app-insights-asp-net-core/0013-copy-url.png)

3. 다음 PowerShell 루프를 실행하여 테스트 앱에 대해 100개의 가상 트랜잭션을 만듭니다. **localhost:** 다음의 포트 번호가 이전 단계에서 복사한 URL과 일치하도록 수정합니다.

   ```PowerShell
   for ($i = 0 ; $i -lt 100; $i++)
   {
    Invoke-WebRequest -uri http://localhost:50984/
   }
   ```

## <a name="open-application-insights-portal"></a>Application Insights 포털 열기

이전 섹션에서 PowerShell을 실행한 후 Application Insights를 시작하여 트랜잭션을 보고 데이터가 수집되고 있는지 확인합니다. 

Visual Studio 메뉴에서 **프로젝트** > **Application Insights** > **Application Insights 포털 열기**를 선택합니다.

   ![Application Insights 개요 스크린샷](./media/app-insights-asp-net-core/010-portal.png)

> [!NOTE]
> 위의 예제 스크린샷에서 **라이브 스트림**, **페이지 보기 로드 시간** 및 **실패한 요청**은 현재 수집되지 않습니다. 다음 섹션에서는 이러한 항목의 추가 작업을 안내합니다. **라이브 스트림** 및 **페이지 보기 로드 시간**을 이미 수집하고 있는 경우 **실패한 요청**에 대한 단계만 따릅니다.

## <a name="collect-failed-requests-live-stream--page-view-load-time"></a>실패한 요청, 라이브 스트림 및 페이지 보기 로드 시간 수집

### <a name="failed-requests"></a>실패한 요청

기술적으로 **실패한 요청**은 수집되지만 아직 발생하지 않았습니다. 이 프로세스를 가속화하기 위해 기존 프로젝트에 실제 예외를 시뮬레이트하는 사용자 지정 예외를 추가할 수 있습니다. **디버깅 중지**(Shift+F5)를 계속하기 전에 Visual Studio에서 앱이 여전히 실행되고 있는 경우 다음을 수행합니다.

1. **솔루션 탐색기**에서 **페이지** > **About.cshtml**을 확장하고 **About.cshtml.cs**를 엽니다.

   ![Visual Studio 솔루션 탐색기 스크린샷](./media/app-insights-asp-net-core/011-about.png)

2. ``Message=`` 아래에 예외를 추가하고 파일에 변경 내용을 저장합니다.

    ```csharp
    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Threading.Tasks;
    using Microsoft.AspNetCore.Mvc.RazorPages;

    namespace DotNetCore.Pages
    {
        public class AboutModel : PageModel
        {
            public string Message { get; set; }

            public void OnGet()
            {
                Message = "Your application description page.";
                throw new Exception("Test Exception");
            }
        }
    }
    ```

### <a name="live-stream"></a>라이브 스트림

**Microsoft.ApplicationInsights.AspNetCore 2.2.0** NuGet 패키지에 대한 ASP.NET Core 업데이트를 사용하여 Application Insights의 라이브 스트림 기능에 액세스하려면 다음을 수행합니다.

Visual Studio에서 **프로젝트** > **NuGet 패키지 관리** > **Microsoft.ApplicationInsights.AspNetCore** > 버전 **2.2.0** > **업데이트**를 선택합니다.

  ![NuGet 패키지 관리자 스크린샷](./media/app-insights-asp-net-core/012-nuget-update.png)

여러 확인 메시지가 표시됩니다. 변경 내용에 동의하는 경우 읽고 수락하세요.

### <a name="page-view-load-time"></a>페이지 보기 로드 시간

1. Visual Studio에서 **솔루션 탐색기** > **페이지**로 이동하여 두 파일 _Layout.cshtml_ 및 _ViewImports.cshtml_을 수정해야 합니다.

2. __ViewImports.cshtml_에서 다음을 추가합니다.

   ```csharp
   @using Microsoft.ApplicationInsights.AspNetCore
   @inject JavaScriptSnippet snippet
   ```

3. **_Layout.cshtml**에서 ``</head>`` 태그 앞에 또한 다른 스크립트 전에 아래의 줄을 추가합니다.

    ```csharp
    @Html.Raw(snippet.FullScript)
    ```

### <a name="test-failed-requests-page-view-load-time-live-stream"></a>실패한 요청, 페이지 보기 로드 시간, 라이브 스트림 테스트

모든 것이 작동하는지 테스트하고 확인하려면.

1. IIS Express를 클릭하여 앱 실행 ![Visual Studio IIS Express 아이콘 스크린샷](./media/app-insights-asp-net-core/0012-iis-express.png)

2. **정보** 페이지로 이동하여 테스트 예외를 트리거합니다. (디버그 모드에서 실행되는 경우 Application Insights에 예외가 표시되게 하려면 Visual Studio에서 **계속**을 클릭해야 합니다.)

3. 이전에 나온 시뮬레이션된 PowerShell 트랜잭션 스크립트를 다시 실행합니다(스크립트에서 포트 번호를 조정해야 할 수 있음.)

4. Application Insights 개요가 아직 열려 있지 않으면 Visual Studio 메뉴에서 **프로젝트** > **Application Insights** > **Application Insights 포털 열기**를 선택합니다. 

   > [!TIP]
   > 새 트래픽이 아직 표시되지 않으면 **시간 범위**를 선택하고 **새로 고침**을 클릭합니다.

   ![개요 창 스크린샷](./media/app-insights-asp-net-core/0019-overview-updated.png)

5. 라이브 스트림 선택

   ![라이브 메트릭 스트림 스크린샷](./media/app-insights-asp-net-core/0020-live-metrics-stream.png)

   (PowerShell 스크립트가 여전히 실행되는 경우, 라이브 메트릭이 표시되며, 중지된 경우에는 라이브 메트릭이 열린 상태에서 스크립트를 다시 실행해야 합니다.)

## <a name="app-insights-sdk-comparison"></a>App Insights SDK 비교

Application Insights 제품 그룹은 [전체 .NET Framework SDK](https://github.com/Microsoft/ApplicationInsights-dotnet)와 .Net Core SDK 간의 기능 패리티를 수행하기 위해 최선을 다하고 있습니다. Application Insights용 [ASP.NET Core SDK](https://github.com/Microsoft/ApplicationInsights-aspnetcore) 2.2.0 릴리스는 이러한 기능 간격을 많이 좁혔습니다.

[.NET과 .NET Core](https://docs.microsoft.com/dotnet/standard/choosing-core-framework-server) 간의 차이점과 균형 요인에 대해 알아보세요.

   | SDK 비교 | ASP.NET        | ASP.NET Core 2.1.0    | ASP.NET Core 2.2.0 |
  |:-- | :-------------: |:------------------------:|:----------------------:|
   | **라이브 메트릭**      | **+** |**-** | **+** |
   | **서버 원격 분석 채널** | **+** |**-** | **+**|
   |**적응 샘플링**| **+** | **-** | **+**|
   | **SQL 종속성 호출**     | **+** |**-** | **+**|
   | **성능 카운터*** | **+** | **-**| **-**|

이 컨텍스트의 _성능 카운터_는 프로세서, 메모리 및 디스크 사용률과 같은 [서버 쪽 성능 카운터](https://docs.microsoft.com/azure/application-insights/app-insights-performance-counters)를 나타냅니다.

## <a name="open-source-sdk"></a>오픈 소스 SDK
[코드를 읽고 참여하기](https://github.com/Microsoft/ApplicationInsights-aspnetcore#recent-updates)

## <a name="application-insights-search-continued"></a>Application Insights 검색 계속

Application Insights NuGet 패키지가 아직 명시적으로 설치되지 않은 경우에도 Application Insights 검색이 ASP.NET Core 2 프로젝트용 Visual Studio에서 작동하는 방법을 더 잘 이해하려면. 디버그 출력을 검사하는 데 도움이 될 수 있습니다.

_인사이트_ 단어의 출력을 검색하는 경우 다음과 유사한 결과가 강조 표시됩니다.

```DebugOuput
'dotnet.exe' (CoreCLR: clrhost): Loaded 'C:\Program Files\dotnet\store\x64\netcoreapp2.0\microsoft.aspnetcore.applicationinsights.hostingstartup\2.0.3\lib\netcoreapp2.0\Microsoft.AspNetCore.ApplicationInsights.HostingStartup.dll'.
'dotnet.exe' (CoreCLR: clrhost): Loaded 'C:\Program Files\dotnet\store\x64\netcoreapp2.0\microsoft.applicationinsights.aspnetcore\2.1.1\lib\netstandard1.6\Microsoft.ApplicationInsights.AspNetCore.dll'.

Application Insights Telemetry (unconfigured): {"name":"Microsoft.ApplicationInsights.Dev.Message","time":"2018-06-03T17:32:38.2796801Z","tags":{"ai.location.ip":"127.0.0.1","ai.operation.name":"DEBUG /","ai.internal.sdkVersion":"aspnet5c:2.1.1","ai.application.ver":"1.0.0.0","ai.cloud.roleInstance":"CONTOSO-SERVER","ai.operation.id":"de85878e-4618b05bad11b5a6","ai.internal.nodeName":"CONTOSO-SERVER","ai.operation.parentId":"|de85878e-4618b05bad11b5a6."},"data":{"baseType":"MessageData","baseData":{"ver":2,"message":"Request starting HTTP/1.1 DEBUG http://localhost:53022/  0","severityLevel":"Information","properties":{"AspNetCoreEnvironment":"Development","Protocol":"HTTP/1.1","CategoryName":"Microsoft.AspNetCore.Hosting.Internal.WebHost","Host":"localhost:53022","Path":"/","Scheme":"http","ContentLength":"0","DeveloperMode":"true","Method":"DEBUG"}}}}
```

CoreCLR은 두 어셈블리를 로드합니다. 

- _Microsoft.AspNetCore.ApplicationInsights.HostingStartup.dll_
- _Microsoft.ApplicationInsights.AspNetCore.dll_.

Application Insights 원격 분석의 각 인스턴스에서 _구성 취소_는 이 응용 프로그램이 ikey와 연결되지 않아 앱이 실행되는 동안 생성되는 데이터가 Azure로 전송되지 않고 로컬 검색 및 분석에만 사용할 수 있다는 것을 나타냅니다.

이것이 가능한 방법의 일부는 NuGet 패키지_Microsoft.AspNetCore.All_가 종속성[_Microsoft.ASPNetCoreApplicationInsights.HostingStartup_](https://docs.microsoft.com/dotnet/api/microsoft.aspnetcore.applicationinsights.hostingstartup.applicationinsightshostingstartup?view=aspnetcore-2.1)으로 사용하는 것입니다.

![Microsoft.AspNETCore.all에 대한 NuGet 종속성 그래프의 스크린샷](./media/app-insights-asp-net-core/013-dependency.png)

Visual Studio 외부의 VSCode 또는 일부 다른 편집기에서 ASP.NET Core 프로젝트를 편집하는 경우 프로젝트에 Application Insights를 명시적으로 추가하지 않으면 이러한 어셈블리는 디버그 동안 자동으로 로드되지 않습니다.

그러나 Visual Studio에서 외부 어셈블리에서 로컬 Application Insights 기능의 점등은 디버그 동안 Application Insights를 동적으로 추가하는 [IHostingStartup 인터페이스](https://docs.microsoft.com/dotnet/api/microsoft.aspnetcore.hosting.ihostingstartup?view=aspnetcore-2.1) 사용을 통해 수행됩니다.

[IHostingStartup을 사용한 ASP.NET Core의 외부 어셈블리](https://docs.microsoft.com/aspnet/core/fundamentals/configuration/platform-specific-configuration?view=aspnetcore-2.1)에서 앱 향상 방법에 대해 자세히 알려면. 

### <a name="how-to-disable-application-insights-in-visual-studio-net-core-projects"></a>Visual Studio.NET Core 프로젝트에서 Application Insights를 사용하지 않도록 설정하는 방법

Application Insights 검색 기능의 자동 점등이 일부에게는 유용할 수 있지만 예상치 못한 경우에 생성된 디버그 원격 분석의 확인은 혼동을 줄 수 있습니다.

원격 분석 생성을 사용하지 않는 것 만으로 충분한 경우 _Startup.cs_ 파일의 구성 방법에 이 코드 블록을 추가할 수 있습니다.

```csharp
  var configuration = app.ApplicationServices.GetService<Microsoft.ApplicationInsights.Extensibility.TelemetryConfiguration>();
            configuration.DisableTelemetry = true;
            if (env.IsDevelopment())
```

CoreCLR은 _Microsoft.AspNetCore.ApplicationInsights.HostingStartup.dll_ 및 _Microsoft.ApplicationInsights.AspNetCore.dll_을 로드하지만 아무 것도 수행하지 않습니다.

Visual Studio.NET Core 프로젝트에서 Application Insights를 완전히 사용하지 않도록 설정하려는 경우 선호되는 메서드는 **도구** > **옵션** >  **프로젝트 및 솔루션** > **웹 프로젝트**를 선택하고 ASP.NET Core 웹 프로젝트용 로컬 Application Insights를 사용하지 않도록 설정하기 위한 상자를 확인하는 것입니다. 이 기능은 Visual Studio 15.6에서 추가되었습니다.

![Visual Studio 옵션 창 웹 프로젝트 화면의 스크린샷](./media/app-insights-asp-net-core/014-disable.png)

이전 버전의 Visual Studio를 실행하고, 추가할 수 있는 IHostingStartup을 통해 로드된 모든 어셈블리를 완전히 제거하려는 경우.

`.UseSetting(WebHostDefaults.PreventHostingStartupKey, "true")`

_Program.cs_.

```csharp
using System;
using System.Collections.Generic;
using System.IO;
using System.Linq;
using System.Threading.Tasks;
using Microsoft.AspNetCore;
using Microsoft.AspNetCore.Hosting;
using Microsoft.Extensions.Configuration;
using Microsoft.Extensions.Logging;

namespace DotNetCore
{
    public class Program
    {
        public static void Main(string[] args)
        {
            BuildWebHost(args).Run();
        }

        public static IWebHost BuildWebHost(string[] args) =>
            WebHost.CreateDefaultBuilder(args)
                .UseSetting(WebHostDefaults.PreventHostingStartupKey, "true")
                .UseStartup<Startup>()
                .Build();
    }
}
```

또는 ``"ASPNETCORE_preventHostingStartup": "True"``을 _launchSettings.json_ 환경 변수에 추가할 수 있습니다.

이러한 방법 중 하나를 사용하는 문제는 IHostingStartup 점등 기능을 활용했던 Visual Studio에서 모든 것을 비활성화할 Application Insights를 비활성화하지 않는다는 것입니다.

## <a name="video"></a>비디오

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/100/player] 

## <a name="next-steps"></a>다음 단계
* [사용자 흐름을 탐색](app-insights-usage-flows.md)하여 사용자가 앱을 탐색하는 방식을 이해합니다.
* 예외가 throw되는 시점의 소스 코드 및 변수 상태를 보려면 [스냅숏 컬렉션을 구성](https://docs.microsoft.com/azure/application-insights/app-insights-snapshot-debugger#configure-snapshot-collection-for-aspnet-core-20-applications)합니다.
* [API를 사용](app-insights-api-custom-events-metrics.md) 합니다.
* [가용성 테스트](app-insights-monitor-web-app-availability.md) 는 사용자의 앱을 전 세계에서 지속적으로 확인합니다.
