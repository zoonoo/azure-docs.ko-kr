---
title: ".NET 앱용 Azure Application Insights 스냅숏 디버거 | Microsoft Docs"
description: "프로덕션 .NET 앱에서 예외가 throw되면 디버그 스냅숏이 자동으로 수집됨"
services: application-insights
documentationcenter: 
author: qubitron
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 07/03/2017
ms.author: cfreeman
ms.translationtype: Human Translation
ms.sourcegitcommit: 6dbb88577733d5ec0dc17acf7243b2ba7b829b38
ms.openlocfilehash: a499f1d5f3a53a0cafb9056c9b5a4d164f80d8c5
ms.contentlocale: ko-kr
ms.lasthandoff: 07/04/2017


---
# <a name="debug-snapshots-on-exceptions-in-net-apps"></a>.NET 앱의 예외에 대한 디버그 스냅숏

예외가 발생할 때 라이브 웹 응용 프로그램에서 자동으로 디버그 스냅숏을 수집할 수 있습니다. 스냅숏은 예외가 throw되었을 때의 소스 코드 및 변수의 상태를 보여 줍니다. [Azure Application Insights](app-insights-overview.md)의 스냅숏 디버거(미리 보기)는 웹앱에서 예외 원격 분석을 모니터링합니다. 프로덕션에서 문제를 진단하는 데 필요한 정보를 유지하도록 많이 throw되는 예외에 대한 스냅숏을 수집합니다. [스냅숏 수집기 NuGet 패키지](http://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector)를 응용 프로그램에 포함하고 필요에 따라, [ApplicationInsights.config](app-insights-configuration-with-applicationinsights-config.md)에서 컬렉션 매개 변수를 구성합니다. 스냅숏은 Application Insights 포털의 [예외](app-insights-asp-net-exceptions.md)에 표시됩니다.

포털에서 디버그 스냅숏을 확인하여 호출 스택을 보고 각 호출 스택 프레임에서 변수를 검사할 수 있습니다. 소스 코드가 있는 좀 더 강력한 디버깅 환경을 구현하려면 [Visual Studio용 스냅숏 디버거 확장을 다운로드](https://aka.ms/snapshotdebugger)하여 Visual Studio 2017 Enterprise에서 스냅숏을 엽니다.

스냅숏 컬렉션을 다음에 사용할 수 있습니다.
* .NET Framework 및 .NET Framework 4.5 이상을 실행하는 ASP.NET 응용 프로그램
* .NET Core 2.0 및 Windows에서 실행되는 ASP.NET Core 2.0 응용 프로그램

### <a name="configure-snapshot-collection-for-aspnet-applications"></a>ASP.NET 응용 프로그램에 대한 스냅숏 컬렉션 구성

1. 이 작업을 아직 수행하지 않은 경우 [웹앱에서 Application Insights를 사용하도록 설정](app-insights-asp-net.md)합니다.

2. [Microsoft.ApplicationInsights.SnapshotCollector](http://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) NuGet 패키지를 앱에 포함합니다. 

3. 패키지가 [ApplicationInsights.config](app-insights-configuration-with-applicationinsights-config.md)에 추가한 기본 옵션을 검토합니다.

    ```xml
    <TelemetryProcessors>
        <Add Type="Microsoft.ApplicationInsights.SnapshotCollector.SnapshotCollectorTelemetryProcessor, Microsoft.ApplicationInsights.SnapshotCollector">
        <!-- The default is true, but you can disable Snapshot Debugging by setting it to false -->
        <IsEnabled>true</IsEnabled>
        <!-- Snapshot Debugging is usually disabled in developer mode, but you can enable it by setting this to true. -->
        <!-- DeveloperMode is a property on the active TelemetryChannel. -->
        <IsEnabledInDeveloperMode>false</IsEnabledInDeveloperMode>
        <!-- How many times we need to see an exception before we ask for snapshots. -->
        <ThresholdForSnapshotting>5</ThresholdForSnapshotting>
        <!-- The maximum number of examples we create for a single problem. -->
        <MaximumSnapshotsRequired>3</MaximumSnapshotsRequired>
        <!-- The maximum number of problems that we can be tracking at any time. -->
        <MaximumCollectionPlanSize>50</MaximumCollectionPlanSize>
        <!-- How often to reset problem counters. -->
        <ProblemCounterResetInterval>06:00:00</ProblemCounterResetInterval>
        <!-- The maximum number of snapshots allowed in one minute. -->
        <SnapshotsPerMinuteLimit>2</SnapshotsPerMinuteLimit>
        <!-- The maximum number of snapshots allowed per day. -->
        <SnapshotsPerDayLimit>50</SnapshotsPerDayLimit>
        </Add>
    </TelemetryProcessors>
    ```

4. 스냅숏은 Application Insights에 보고되는 예외에 대해서만 수집됩니다. 경우에 따라(예: 이전 버전의 .NET 플랫폼) 포털에서 스냅숏 예외를 보려면 [예외 컬렉션을 구성](app-insights-asp-net-exceptions.md#exceptions)해야 할 수 있습니다.


### <a name="configure-snapshot-collection-for-aspnet-core-20-applications"></a>ASP.NET Core 2.0 응용 프로그램에 대한 스냅숏 컬렉션 구성

1. 이 작업을 아직 수행하지 않은 경우 [ASP.NET Core 웹앱에서 Application Insights를 사용하도록 설정](app-insights-asp-net-core.md)합니다.

2. [Microsoft.ApplicationInsights.SnapshotCollector](http://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) NuGet 패키지를 앱에 포함합니다.

3. 응용 프로그램의 `Startup` 클래스에서 `ConfigureServices` 메서드를 수정하여 스냅숏 수집기의 원격 분석 프로세서를 추가합니다.
   ```C#
   using Microsoft.ApplicationInsights.SnapshotCollector;
   ...
   class Startup
   {
       // This method gets called by the runtime. Use this method to add services to the container.
       public void ConfigureServices(IServiceCollection services)
       {
           services.AddSingleton<Func<ITelemetryProcessor, ITelemetryProcessor>>(next => new SnapshotCollectorTelemetryProcessor(next));
           // TODO: Add any other services your application needs here.
       }
   }
   ```

### <a name="configure-snapshot-collection-for-other-net-applications"></a>다른 .NET 응용 프로그램에 대한 스냅숏 컬렉션 구성

1. 응용 프로그램이 아직 Application Insights로 계측되지 않는 경우 먼저 [Application Insights를 사용하도록 설정하고 계측 키를 설정](app-insights-windows-desktop.md)합니다.

2. [Microsoft.ApplicationInsights.SnapshotCollector](http://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) NuGet 패키지를 앱에 추가합니다.

3. 스냅숏은 Application Insights에 보고되는 예외에 대해서만 수집됩니다. 예외를 보고하도록 코드를 수정해야 할 수도 있습니다. 예외 처리 코드는 응용 프로그램의 구조에 따라 달라집니다. 예제는 다음과 같습니다.
   ```C#
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

## Grant permissions

Owners of the Azure subscription can inspect snapshots. Other users must be granted permission by an owner.

To grant permission, assign the `Application Insights Snapshot Debugger` role to users who will inspect snapshots. This role can be assigned to individual users or groups by subscription owners for the target Application Insights resource or its resource group or subscription.

1. On the Application Insights navigation menu, select **Access Control (IAM)**.
2. Click **Roles** > **Application Insights Snapshot Debugger**.
3. Click **Add**, and then select a user or group.

    >[!IMPORTANT] 
    Snapshots can potentially contain personal and other sensitive information in variable and parameter values.

## Debug snapshots in the Application Insights portal

If a snapshot is available for a given exception or a problem ID, an **Open Debug Snapshot** button appears on the [exception](app-insights-asp-net-exceptions.md) in the Application Insights portal.

![Open Debug Snapshot button on exception](./media/app-insights-snapshot-debugger/snapshot-on-exception.png)

In the Debug Snapshot view, you see a call stack and a variables pane. When you select frames of the call stack in the call stack pane, you can view local variables and parameters for that function call in the variables pane.

![View Debug Snapshot in the portal](./media/app-insights-snapshot-debugger/open-snapshot-portal.png)

Snapshots might contain sensitive information, and by default they are not viewable. To view snapshots, you must have the `Application Insights Snapshot Debugger` role assigned to you.

## Debug snapshots with Visual Studio 2017 Enterprise
1. Click the **Download Snapshot** button to download a `.diagsession` file, which can be opened by Visual Studio 2017 Enterprise. 

2. To open the `.diagsession` file, you must first [download and install the Snapshot Debugger extension for Visual Studio](https://aka.ms/snapshotdebugger).

3. After you open the snapshot file, the Minidump Debugging page in Visual Studio appears. Click **Debug Managed Code** to start debugging the snapshot. The snapshot opens to the line of code where the exception was thrown so that you can debug the current state of the process.

    ![View debug snapshot in Visual Studio](./media/app-insights-snapshot-debugger/open-snapshot-visualstudio.png)

The downloaded snapshot contains any symbol files that were found on your web application server. These symbol files are required to associate snapshot data with source code. For App Service apps, make sure to enable symbol deployment when you publish your web apps.

## How snapshots work

When your application starts, a separate snapshot uploader process is created that monitors your application for snapshot requests. When a snapshot is requested, a shadow copy of the running process is made in about 10 to 20 minutes. The shadow process is then analyzed, and a snapshot is created while the main process continues to run and serve traffic to users. The snapshot is then uploaded to Application Insights along with any relevant symbol (.pdb) files that are needed to view the snapshot.

## Current limitations

### Publish symbols
The Snapshot Debugger requires symbol files on the production server to decode variables and to provide a debugging experience in Visual Studio. The 15.2 release of Visual Studio 2017 publishes symbols for release builds by default when it publishes to App Service. In prior versions, you need to add the following line to your publish profile `.pubxml` file so that symbols are published in release mode:

```xml
    <ExcludeGeneratedDebugSymbol>False</ExcludeGeneratedDebugSymbol>
```

Azure Compute 및 기타 형식의 경우 기호 파일이 주 응용 프로그램 .dll의 동일한 폴더(일반적으로 `wwwroot/bin`)에 있거나 현재 경로에서 사용할 수 있는지 확인합니다.

### <a name="optimized-builds"></a>최적화된 빌드
일부 경우에 빌드 프로세스 동안 적용된 최적화 때문에 릴리스 빌드에서 지역 변수를 볼 수 없습니다.

## <a name="troubleshooting"></a>문제 해결

이러한 팁은 스냅숏 디버거를 사용하여 문제를 해결하는 데 유용합니다.

### <a name="verify-the-instrumentation-key"></a>계측 키 확인

게시된 응용 프로그램에서 올바른 계측 키를 사용하는 있는지 확인합니다. 일반적으로 Application Insights는 ApplicationInsights.config 파일에서 계측 키를 읽습니다. 포털에 표시된 Application Insights 리소스에 대한 계측 키와 값이 동일한지 확인합니다.

### <a name="check-the-uploader-logs"></a>업로더 로그 확인

스냅숏을 만들면 디스크에 미니덤프 파일(.dmp)이 생성됩니다. 별도의 업로더 프로세스에서 연결된 모든 PDB와 함께 이 미니덤프 파일을 가져와 Application Insights 스냅숏 디버거 저장소에 업로드합니다. 미니덤프는 성공적으로 업로드된 후 디스크에서 삭제됩니다. 미니덤프 업로더에 대한 로그 파일은 디스크에 유지됩니다. App Service 환경에서는 `D:\Home\LogFiles\Uploader_*.log`에서 이러한 로그를 찾을 수 있습니다. App Service에 대한 Kudu 관리 사이트를 사용하여 이러한 로그 파일을 찾을 수 있습니다.

1. Azure Portal에서 App Service 응용 프로그램을 엽니다.

2. **고급 도구** 블레이드를 선택하거나, **Kudu**를 검색합니다.
3. **이동**을 클릭합니다.
4. **디버그 콘솔** 드롭다운 목록 상자에서 **CMD**를 선택합니다.
5. **LogFiles**를 클릭합니다.

이름이 `Uploader_`로 시작하고 확장명이 `.log`인 파일이 하나 이상 있어야 합니다. 해당 아이콘을 클릭하여 모든 로그 파일을 다운로드하거나 브라우저에서 엽니다.
파일 이름에 컴퓨터 이름이 포함되어 있습니다. App Service 인스턴스가 둘 이상의 컴퓨터에서 호스팅되는 경우 각 컴퓨터에 대한 별도의 로그 파일이 있습니다. 업로더가 새 미니덤프 파일을 검색한 경우 로그 파일에 기록됩니다. 성공적인 업로드의 예는 다음과 같습니다.

```
MinidumpUploader.exe Information: 0 : Dump available 139e411a23934dc0b9ea08a626db16c5.dmp
    DateTime=2017-05-25T14:25:08.0349846Z
MinidumpUploader.exe Information: 0 : Uploading D:\local\Temp\Dumps\c12a605e73c44346a984e00000000000\139e411a23934dc0b9ea08a626db16c5.dmp, 329.12 MB
    DateTime=2017-05-25T14:25:16.0145444Z
MinidumpUploader.exe Information: 0 : Upload successful.
    DateTime=2017-05-25T14:25:42.9164120Z
MinidumpUploader.exe Information: 0 : Extracting PDB info from D:\local\Temp\Dumps\c12a605e73c44346a984e00000000000\139e411a23934dc0b9ea08a626db16c5.dmp.
    DateTime=2017-05-25T14:25:42.9164120Z
MinidumpUploader.exe Information: 0 : Matched 2 PDB(s) with local files.
    DateTime=2017-05-25T14:25:44.2310982Z
MinidumpUploader.exe Information: 0 : Stamp does not want any of our matched PDBs.
    DateTime=2017-05-25T14:25:44.5435948Z
MinidumpUploader.exe Information: 0 : Deleted D:\local\Temp\Dumps\c12a605e73c44346a984e00000000000\139e411a23934dc0b9ea08a626db16c5.dmp
    DateTime=2017-05-25T14:25:44.6095821Z
```

위 예에서 계측 키는 `c12a605e73c44346a984e00000000000`입니다. 이 값은 응용 프로그램의 계측 키와 일치해야 합니다.
미니덤프는 ID가 `139e411a23934dc0b9ea08a626db16c5`인 스냅숏에 연결됩니다. 나중에 이 ID를 사용하여 Application Insights Analytics에서 연결된 예외 원격 분석을 찾을 수 있습니다.

업로더는 약 15분에 한 번씩 새 PDB를 검색합니다. 예를 들면 다음과 같습니다.

```
MinidumpUploader.exe Information: 0 : PDB rescan requested.
    DateTime=2017-05-25T15:11:38.8003886Z
MinidumpUploader.exe Information: 0 : Scanning D:\home\site\wwwroot\ for local PDBs.
    DateTime=2017-05-25T15:11:38.8003886Z
MinidumpUploader.exe Information: 0 : Scanning D:\local\Temporary ASP.NET Files\root\a6554c94\e3ad6f22\assembly\dl3\81d5008b\00b93cc8_dec5d201 for local PDBs.
    DateTime=2017-05-25T15:11:38.8160276Z
MinidumpUploader.exe Information: 0 : Local PDB scan complete. Found 2 PDB(s).
    DateTime=2017-05-25T15:11:38.8316450Z
MinidumpUploader.exe Information: 0 : Deleted PDB scan marker D:\local\Temp\Dumps\c12a605e73c44346a984e00000000000\.pdbscan.
    DateTime=2017-05-25T15:11:38.8316450Z
```

App Service에서 호스팅되지 _않는_ 응용 프로그램의 경우 업로더 로그는 미니덤프와 동일한 폴더 `%TEMP%\Dumps\<ikey>`(여기서 `<ikey>`는 계측 키)에 저장됩니다.

### <a name="use-application-insights-search-to-find-exceptions-with-snapshots"></a>Application Insights 검색을 사용하여 스냅숏 예외 찾기

스냅숏이 생성될 때 throw되는 예외에는 스냅숏 ID로 태그가 지정됩니다. 예외 원격 분석이 Application Insights에 보고될 때 이 스냅숏 ID가 사용자 지정 속성으로 포함됩니다. Application Insights에서 검색 블레이드를 사용하여 `ai.snapshot.id` 사용자 지정 속성으로 모든 원격 분석을 찾을 수 있습니다.

1. Azure Portal에서 Application Insights 리소스로 이동합니다.
2. **검색**을 클릭합니다.
3. 검색 텍스트 상자에 `ai.snapshot.id`를 입력하고 Enter 키를 누릅니다.

![포털에서 스냅숏 ID로 원격 분석 검색](./media/app-insights-snapshot-debugger/search-snapshot-portal.png)

이 검색에서 반환되는 결과가 없으면 선택한 시간 범위에서 응용 프로그램에 대해 Application Insights에 보고된 스냅숏이 없는 것입니다.

업로더 로그에서 특정 스냅숏 ID를 검색하려면 검색 상자에 해당 ID를 입력합니다. 업로드된 것을 알고 있는 스냅숏에 대한 원격 분석을 찾을 수 없는 경우 다음 단계를 따릅니다.

1. 계측 키를 확인하여 올바른 Application Insights 리소스가 표시되어 있는지 다시 확인합니다.

2. 업로더 로그에서 타임스탬프를 사용하여 해당 시간 범위를 포함하도록 검색의 시간 범위 필터를 조정합니다.

해당 스냅숏 ID가 포함된 예외가 여전히 보이지 않는 경우 Application Insights에 예외 원격 분석이 보고되지 않은 것입니다. 스냅숏을 만든 후 예외 원격 분석을 보고하기 전에 응용 프로그램의 작동이 중단된 경우에 이 상황이 발생할 수 있습니다. 이 경우 `Diagnose and solve problems`에서 App Service 로그를 검사하여 예기치 않은 다시 시작 또는 처리되지 않은 예외가 있는지 확인합니다.

## <a name="next-steps"></a>다음 단계

* 예외를 기다리지 않고 스냅숏을 가져오기 위해 [코드에서 snappoint를 설정](https://azure.microsoft.com/blog/snapshot-debugger-for-azure/)합니다.
* [웹앱의 예외 진단](app-insights-asp-net-exceptions.md)에서는 Application Insights에서 추가 예외를 표시하는 방법을 설명합니다. 
* [스마트 검색](app-insights-proactive-diagnostics.md)은 성능 예외를 자동으로 검색합니다.

