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
ms.date: 04/30/2017
ms.author: cfreeman
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 94b073df00b690f1347e246eeb7e12fdd1bd7cc1
ms.contentlocale: ko-kr
ms.lasthandoff: 05/10/2017


---
# <a name="debug-snapshots-on-exceptions-in-net-apps"></a>.NET 앱의 예외에 대한 디버그 스냅숏

*이 기능은 미리 보기 상태입니다.*

예외가 발생할 때 라이브 웹 응용 프로그램에서 자동으로 디버그 스냅숏을 수집합니다. 스냅숏은 예외가 throw되었을 때의 소스 코드 및 변수의 상태를 보여 줍니다. [Application Insights](app-insights-overview.md)의 스냅숏 디버거는 웹앱에서 예외 원격 분석을 모니터링합니다. 프로덕션에서 문제를 진단하는 데 필요한 정보를 유지하도록 많이 throw되는 예외에 대한 스냅숏을 수집합니다. [스냅숏 수집기 NuGet 패키지](http://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector)를 응용 프로그램에 포함하고 필요에 따라, [ApplicationInsights.config](app-insights-configuration-with-applicationinsights-config.md)에서 컬렉션 매개 변수를 구성합니다. 스냅숏은 Application Insights 포털의 [예외](app-insights-asp-net-exceptions.md)에 표시됩니다.

포털에서 디버그 스냅숏을 확인하여 호출 스택을 보고 각 호출 스택 프레임에서 변수를 검사할 수 있습니다. 소스 코드가 있는 좀 더 강력한 디버깅 환경을 구현하려면 [Visual Studio용 스냅숏 디버거 확장을 다운로드](https://aka.ms/snapshotdebugger)하여 Visual Studio 2017 Enterprise에서 스냅숏을 엽니다.

스냅숏 컬렉션은 Azure Compute 또는 Azure App Service의 IIS에 호스트된 .NET Framework 4.6 이상에서 실행되는 ASP.NET 웹앱에 사용할 수 있습니다.

## <a name="configure-snapshot-collection"></a>스냅숏 컬렉션 구성

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

스냅숏은 Application Insights SDK에 표시되는 예외에 대해서만 수집됩니다. 경우에 따라 포털에 스냅숏이 표시되는 상태로 예외를 볼 수 있게 [예외 컬렉션을 구성](app-insights-asp-net-exceptions.md#exceptions)해야 할 수 있습니다.

## <a name="debugging-snapshots-in-the-application-insights-portal"></a>Application Insights 포털의 디버그 스냅숏

지정된 예외 또는 문제 ID에 대해 스냅숏을 사용할 수 있으면 Application Insights 포털의 [예외](app-insights-asp-net-exceptions.md)에 *디버그 스냅숏 열기* 링크가 표시됩니다.

![예외에서 디버그 스냅숏 열기 단추](./media/app-insights-snapshot-debugger/snapshot-on-exception.png)

디버그 스냅숏 보기에는 호출 스택 및 변수 창이 표시됩니다. 호출 스택 창에서 호출 스택의 프레임을 선택하면 변수 창에 해당 함수 호출에 대한 지역 변수 및 매개 변수가 표시됩니다.

![포털에서 디버그 스냅숏 보기](./media/app-insights-snapshot-debugger/open-snapshot-portal.png)

스냅숏에는 중요한 정보가 포함될 수 있으며 기본적으로는 볼 수 없습니다. 스냅숏을 보려는 포털에서 구독 또는 리소스에 대한 `Application Insights Snapshot Debugger` 역할이 할당되어야 합니다. 현재 이 역할은 구독 소유자만 사용자 단위로 할당할 수 있습니다. Azure Active Directory 그룹에 대한 역할 할당은 현재 지원되지 않습니다.

## <a name="debugging-snapshots-with-visual-studio-2017-enterprise"></a>Visual Studio 2017 Enterprise를 사용한 스냅숏 디버그
*스냅숏 다운로드* 단추를 클릭하여 Visual Studio 2017 Enterprise에서 열 수 있는 `.diagsession` 파일을 다운로드할 수 있습니다. 현재 `.diagsession` 파일을 열려면 먼저 [Visual Studio용 스냅숏 디버거 확장을 다운로드한 후 설치](https://aka.ms/snapshotdebugger)해야 합니다.

스냅숏 파일을 열면 *관리 코드 디버그*를 클릭하여 스냅숏 디버그를 시작할 수 있는 Visual Studio의 미니덤프 디버깅 페이지로 이동됩니다. 예외가 throw되고 프로세스의 현재 상태를 디버그할 수 있는 코드 줄로 이동됩니다.

![Visual Studio에서 디버그 스냅숏 보기](./media/app-insights-snapshot-debugger/open-snapshot-visualstudio.png)

다운로드된 스냅숏에는 웹 응용 프로그램 서버에서 발견된 모든 기호 파일이 포함되어 있습니다. 이러한 기호 파일은 소스 코드에 스냅숏 데이터를 연결하는 데 필요합니다. Azure App Service 앱의 경우 웹앱을 게시할 때 기호를 배포하도록 설정할 수 있습니다.

## <a name="how-snapshots-work"></a>스냅숏 작동 방식

응용 프로그램이 시작되면 응용 프로그램에서 스냅숏 요청을 모니터링하는 별도 스냅숏 업로더 프로세스가 생성됩니다. 스냅숏이 요청되면 약 10-20밀리초 후에 실행 중인 프로세스의 섀도 복사본이 만들어집니다. 그런 후 주 프로세스가 계속 실행되고 사용자에게 트래픽이 공급되면서 이 섀도 프로세스가 분석되고, 스냅숏이 생성됩니다. 스냅숏은 스냅숏을 보는 데 필요한 관련 기호(.pdb) 파일과 함께 Application Insights로 업로드됩니다.

## <a name="current-limitations"></a>현재 제한 사항

### <a name="publishing-symbols"></a>기호 게시
스냅숏 디버거를 사용하려면 Visual Studio에서 변수를 디코딩하고 디버깅 환경을 제공하기 위해 프로덕션 서버에 기호 파일이 있어야 합니다. 15.2 릴리스 Visual Studio 2017은 Azure App Service에 게시할 때 기본적으로 릴리스 빌드에 대한 기호를 게시합니다. 이전 버전에서는 기호가 릴리스 모드에서 게시될 수 있게 게시 프로필 `.pubxml` 파일에 다음 줄을 추가해야 합니다.

```xml
    <ExcludeGeneratedDebugSymbol>False</ExcludeGeneratedDebugSymbol>
```

Azure Compute 및 기타 형식의 경우 기호 파일이 주 응용 프로그램 .dll의 동일한 폴더(일반적으로 `wwwroot/bin`)에 있거나 현재 경로에서 사용할 수 있는지 확인합니다.

### <a name="optimized-builds"></a>최적화된 빌드
일부 경우에 빌드 프로세스 동안 적용된 최적화 때문에 릴리스 빌드에서 지역 변수를 볼 수 없습니다. 이러한 제한은 향후 NuGet 패키지 릴리스에서 수정될 예정입니다.

## <a name="next-steps"></a>다음 단계

* [웹앱의 예외 진단](app-insights-asp-net-exceptions.md)에서는 Application Insights에서 추가 예외를 표시하는 방법을 설명합니다. 
* [스마트 검색](app-insights-proactive-diagnostics.md)은 성능 예외를 자동으로 검색합니다.

