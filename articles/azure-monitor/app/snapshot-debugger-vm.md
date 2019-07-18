---
title: Azure Service Fabric, 클라우드 서비스 및 Virtual Machines에서.NET 앱에 대 한 스냅숏 디버거를 사용 하도록 설정 | Microsoft Docs
description: Azure Service Fabric, 클라우드 서비스 및 Virtual Machines에서.NET 앱에 대 한 스냅숏 디버거를 사용 하도록 설정
services: application-insights
documentationcenter: ''
author: brahmnes
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.reviewer: mbullwin
ms.date: 03/07/2019
ms.author: bfung
ms.openlocfilehash: 5ac1d1339cb8a26cc86157d4d2aa664517418095
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/07/2019
ms.locfileid: "67617806"
---
# <a name="enable-snapshot-debugger-for-net-apps-in-azure-service-fabric-cloud-service-and-virtual-machines"></a>Azure Service Fabric, 클라우드 서비스 및 Virtual Machines에서.NET 앱에 대 한 스냅숏 디버거를 사용 하도록 설정

에 ASP.NET 또는 ASP.NET core Azure App Service에서 응용 프로그램 실행을 하는 경우 것이 좋습니다 하 [스냅숏 디버거를 사용 하도록 설정 된 Application Insights 포털 페이지를 통해](snapshot-debugger-appservice.md?toc=/azure/azure-monitor/toc.json)합니다. 그러나 응용 프로그램을 사용자 지정 된 스냅숏 디버거 구성 또는.NET core의 미리 보기 버전에 필요한 경우 다음이 지침을 따라야 ***또한*** 에 대 한 지침을 [통해 사용 하도록 설정 Application Insights 포털 페이지](snapshot-debugger-appservice.md?toc=/azure/azure-monitor/toc.json)합니다.

응용 프로그램이 Azure Service Fabric, 클라우드 서비스, Virtual Machines에서 실행 하거나 온-프레미스 컴퓨터를 하는 경우 다음 지침을 사용 해야 합니다. 
    
## <a name="configure-snapshot-collection-for-aspnet-applications"></a>ASP.NET 애플리케이션에 대한 스냅샷 컬렉션 구성

1. 이 작업을 아직 수행하지 않은 경우 [웹앱에서 Application Insights를 사용하도록 설정](../../azure-monitor/app/asp-net.md)합니다.

2. [Microsoft.ApplicationInsights.SnapshotCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) NuGet 패키지를 앱에 포함합니다.

3. 필요한 경우 스냅숏 디버거 구성이 추가 사용자 지정 [ApplicationInsights.config](../../azure-monitor/app/configuration-with-applicationinsights-config.md)합니다. 기본 스냅숏 디버거 구성 비어 대개 하 고 모든 설정은 선택 사항입니다. 구성을 기본 구성으로 동일한 보여 주는 예제는 다음과 같습니다.

    ```xml
    <TelemetryProcessors>
        <Add Type="Microsoft.ApplicationInsights.SnapshotCollector.SnapshotCollectorTelemetryProcessor, Microsoft.ApplicationInsights.SnapshotCollector">
        <!-- The default is true, but you can disable Snapshot Debugging by setting it to false -->
        <IsEnabled>true</IsEnabled>
        <!-- Snapshot Debugging is usually disabled in developer mode, but you can enable it by setting this to true. -->
        <!-- DeveloperMode is a property on the active TelemetryChannel. -->
        <IsEnabledInDeveloperMode>false</IsEnabledInDeveloperMode>
        <!-- How many times we need to see an exception before we ask for snapshots. -->
        <ThresholdForSnapshotting>1</ThresholdForSnapshotting>
        <!-- The maximum number of examples we create for a single problem. -->
        <MaximumSnapshotsRequired>3</MaximumSnapshotsRequired>
        <!-- The maximum number of problems that we can be tracking at any time. -->
        <MaximumCollectionPlanSize>50</MaximumCollectionPlanSize>
        <!-- How often we reconnect to the stamp. The default value is 15 minutes.-->
        <ReconnectInterval>00:15:00</ReconnectInterval>
        <!-- How often to reset problem counters. -->
        <ProblemCounterResetInterval>1.00:00:00</ProblemCounterResetInterval>
        <!-- The maximum number of snapshots allowed in ten minutes.The default value is 1. -->
        <SnapshotsPerTenMinutesLimit>3</SnapshotsPerTenMinutesLimit>
        <!-- The maximum number of snapshots allowed per day. -->
        <SnapshotsPerDayLimit>30</SnapshotsPerDayLimit>
        <!-- Whether or not to collect snapshot in low IO priority thread. The default value is true. -->
        <SnapshotInLowPriorityThread>true</SnapshotInLowPriorityThread>
        <!-- Agree to send anonymous data to Microsoft to make this product better. -->
        <ProvideAnonymousTelemetry>true</ProvideAnonymousTelemetry>
        <!-- The limit on the number of failed requests to request snapshots before the telemetry processor is disabled. -->
        <FailedRequestLimit>3</FailedRequestLimit>
        </Add>
    </TelemetryProcessors>
    ```

4. 스냅샷은 Application Insights에 보고되는 예외에 대해서만 수집됩니다. 경우에 따라(예: 이전 버전의 .NET 플랫폼) 포털에서 스냅샷 예외를 보려면 [예외 컬렉션을 구성](../../azure-monitor/app/asp-net-exceptions.md#exceptions)해야 할 수 있습니다.


## <a name="configure-snapshot-collection-for-applications-using-aspnet-core-20-or-above"></a>ASP.NET Core 2.0을 사용 하 여 응용 프로그램에 대 한 이상 스냅숏 컬렉션 구성

1. 이 작업을 아직 수행하지 않은 경우 [ASP.NET Core 웹앱에서 Application Insights를 사용하도록 설정](../../azure-monitor/app/asp-net-core.md)합니다.

    > [!NOTE]
    > 애플리케이션이 Microsoft.ApplicationInsights.AspNetCore 패키지의 버전 2.1.1 이상을 참조하는지 확인하세요.

2. [Microsoft.ApplicationInsights.SnapshotCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) NuGet 패키지를 앱에 포함합니다.

3. 애플리케이션의 `Startup` 클래스를 수정하여 스냅샷 수집기의 원격 분석 프로세서를 추가하고 구성합니다.
    1. 하는 경우 [Microsoft.ApplicationInsights.SnapshotCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) NuGet 패키지 버전 1.3.5 또는 위에 사용 추가한 다음 using 문을 `Startup.cs`합니다.

       ```csharp
            using Microsoft.ApplicationInsights.SnapshotCollector;
       ```

       ConfigureServices 메소드 에서처럼의 끝에 다음을 추가 합니다 `Startup` 클래스의 `Startup.cs`합니다.

       ```csharp
            services.AddSnapshotCollector((configuration) =>
            {
                IConfigurationSection section = Configuration.GetSection(nameof(SnapshotCollectorConfiguration));
                if (section.Value != null)
                {
                    section.Bind(configuration);
                }
            });

       ```
    2. 하는 경우 [Microsoft.ApplicationInsights.SnapshotCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) NuGet 패키지 버전 1.3.4 또는 아래 사용 추가한 다음 using 문을 `Startup.cs`합니다.

       ```csharp
       using Microsoft.ApplicationInsights.SnapshotCollector;
       using Microsoft.Extensions.Options;
       using Microsoft.ApplicationInsights.AspNetCore;
       using Microsoft.ApplicationInsights.Extensibility;
       ```
    
       다음 `SnapshotCollectorTelemetryProcessorFactory` 클래스를 `Startup` 클래스에 추가합니다.
    
       ```csharp
       class Startup
       {
           private class SnapshotCollectorTelemetryProcessorFactory : ITelemetryProcessorFactory
           {
               private readonly IServiceProvider _serviceProvider;
    
               public SnapshotCollectorTelemetryProcessorFactory(IServiceProvider serviceProvider) =>
                   _serviceProvider = serviceProvider;
    
               public ITelemetryProcessor Create(ITelemetryProcessor next)
               {
                   var snapshotConfigurationOptions = _serviceProvider.GetService<IOptions<SnapshotCollectorConfiguration>>();
                   return new SnapshotCollectorTelemetryProcessor(next, configuration: snapshotConfigurationOptions.Value);
               }
           }
           ...
        ```
        `SnapshotCollectorConfiguration` 및 `SnapshotCollectorTelemetryProcessorFactory` 서비스를 시작 파이프라인에 추가합니다.
    
        ```csharp
           // This method gets called by the runtime. Use this method to add services to the container.
           public void ConfigureServices(IServiceCollection services)
           {
               // Configure SnapshotCollector from application settings
               services.Configure<SnapshotCollectorConfiguration>(Configuration.GetSection(nameof(SnapshotCollectorConfiguration)));
    
               // Add SnapshotCollector telemetry processor.
               services.AddSingleton<ITelemetryProcessorFactory>(sp => new SnapshotCollectorTelemetryProcessorFactory(sp));
    
               // TODO: Add other services your application needs here.
           }
       }
       ```

4. 필요한 경우 스냅숏 디버거 구성을 appsettings.json에 SnapshotCollectorConfiguration 섹션을 추가 하 여 사용자 지정 합니다. 모든 스냅숏 디버거 구성에서 설정은 선택 사항입니다. 구성을 기본 구성으로 동일한 보여 주는 예제는 다음과 같습니다.

   ```json
   {
     "SnapshotCollectorConfiguration": {
       "IsEnabledInDeveloperMode": false,
       "ThresholdForSnapshotting": 1,
       "MaximumSnapshotsRequired": 3,
       "MaximumCollectionPlanSize": 50,
       "ReconnectInterval": "00:15:00",
       "ProblemCounterResetInterval":"1.00:00:00",
       "SnapshotsPerTenMinutesLimit": 1,
       "SnapshotsPerDayLimit": 30,
       "SnapshotInLowPriorityThread": true,
       "ProvideAnonymousTelemetry": true,
       "FailedRequestLimit": 3
     }
   }
   ```

## <a name="configure-snapshot-collection-for-other-net-applications"></a>다른 .NET 애플리케이션에 대한 스냅샷 컬렉션 구성

1. 애플리케이션이 아직 Application Insights로 계측되지 않는 경우 먼저 [Application Insights를 사용하도록 설정하고 계측 키를 설정](../../azure-monitor/app/windows-desktop.md)합니다.

2. [Microsoft.ApplicationInsights.SnapshotCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) NuGet 패키지를 앱에 추가합니다.

3. 스냅샷은 Application Insights에 보고되는 예외에 대해서만 수집됩니다. 예외를 보고하도록 코드를 수정해야 할 수도 있습니다. 예외 처리 코드는 애플리케이션의 구조에 따라 달라집니다. 예제는 다음과 같습니다.
    ```csharp
   TelemetryClient _telemetryClient = new TelemetryClient();

   void ExampleRequest()
   {
        try
        {
            // TODO: Handle the request.
        }
        catch (Exception ex)
        {
            // Report the exception to Application Insights.
            _telemetryClient.TrackException(ex);

            // TODO: Rethrow the exception if desired.
        }
   }
    ```

## <a name="next-steps"></a>다음 단계

- 예외를 트리거할 수 있는 응용 프로그램에 대 한 트래픽을 생성 합니다. 그런 다음 Application Insights 인스턴스를 전송할 수 있도록 스냅숏 10 ~ 15 분을 기다립니다.
- 참조 [스냅숏을](snapshot-debugger.md?toc=/azure/azure-monitor/toc.json#view-snapshots-in-the-portal) Azure portal에서 합니다.
- 스냅숏 디버거 문제 해결 도움말을 참조 하세요 [Snapshot Debugger 문제 해결](snapshot-debugger-troubleshoot.md?toc=/azure/azure-monitor/toc.json)합니다.
