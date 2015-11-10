<properties 
	pageTitle="ApplicationInsights.config 또는 .xml로 Application Insights SDK 구성" 
	description="데이터 수집 모듈을 사용하거나 사용하지 않도록 설정하고 성능 카운터 및 기타 매개 변수를 추가합니다." 
	services="application-insights"
    documentationCenter="" 
	authors="OlegAnaniev-MSFT"
    editor="alancameronwills" 
	manager="meravd"/>
 
<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/29/2015" 
	ms.author="awills"/>

# ApplicationInsights.config 또는 .xml로 Application Insights SDK 구성

Application Insights.NET SDK는 NuGet 패키지의 숫자로 구성됩니다. [코어 패키지](http://www.nuget.org/packages/Microsoft.ApplicationInsights) Application Insights에 원격 분석을 보내는 경우에 API를 제공합니다. [추가 패키지](http://www.nuget.org/packages?q=Microsoft.ApplicationInsights)는 해당 컨텍스트 및 응용 프로그램에서 원격 분석을 자동으로 추적하기 위해 원격 분석 _모듈_ 및 _이니셜라이저_를 제공합니다. 구성 파일을 조정하여 모듈을 활성화하거나 비활성화하고 이 중 일부 모듈의 매개 변수를 설정할 수 있습니다.

구성 파일의 이름은 응용 프로그램 유형에 따라 `ApplicationInsights.config` 또는 `ApplicationInsights.xml`입니다. [일부 버전의 SDK 설치][start]를 설치할 때 프로젝트에 자동으로 추가됩니다. 또한 [IIS 서버의 상태 모니터][redfield]에 의해 또는 [Azure 웹사이트 또는 VM에 대한 Appplication Insights 확장을 선택][azure]하는 경우 웹앱에 추가됩니다.

[웹 페이지에서 SDK][client]를 제어할 동급의 파일은 없습니다.

각 모듈에 대해 구성 파일에 노드가 있습니다. 모듈을 사용하지 않으려면 노드를 삭제하거나 주석으로 처리합니다.

## [Microsoft.ApplicationInsights](http://www.nuget.org/packages/Microsoft.ApplicationInsights)NuGet 패키지

`Microsoft.ApplicationInsights`NuGet 패키지는 다음 원격 분석 모듈을 `ApplicationInsights.config`에 제공합니다.

```
<ApplicationInsights xmlns="http://schemas.microsoft.com/ApplicationInsights/2013/Settings">
  <TelemetryModules>
    <Add Type="Microsoft.ApplicationInsights.Extensibility.Implementation.Tracing.DiagnosticsTelemetryModule, Microsoft.ApplicationInsights" />
  </TelemetryModules>
</ApplicationInsights>
```
`DiagnosticsTelemetryModule`은 Application Insights instrumenation 코드 자체에 오류를 보고 합니다. 예를들어 코드가 성능 카운터에 액세스할 수 없는 경우 또는 `ITelemetryInitializer`를 throw 하는 경우는 예외입니다. 원격 분석 추적은 [진단 검색][diagnostic] 안의 이 모듈의 출현의 의해 추적됩니다.

## [Microsoft.ApplicationInsights.DependencyCollector](http://www.nuget.org/packages/Microsoft.ApplicationInsights.DependencyCollector) NuGet 패키지

`Microsoft.ApplicationInsights.DependencyCollector`NuGet 패키지는 다음 원격 분석 모듈을 `ApplicationInsights.config`에 제공합니다.

```
<ApplicationInsights xmlns="http://schemas.microsoft.com/ApplicationInsights/2013/Settings">
  <TelemetryModules>
    <Add Type="Microsoft.ApplicationInsights.Extensibility.DependencyCollector.DependencyTrackingTelemetryModule, Microsoft.ApplicationInsights.Extensibility.DependencyCollector" />
  </TelemetryModules>
</ApplicationInsights>
```
`DependencyTrackingTelemetryModule`HTTP 요청 및 SQL 쿼리 같은 응용 프로그램에 대해 수행한 외부 종속성에 대한 호출에 대한 원격 분석을 추적 합니다. 이 모듈이 IIS 서버에서 작동하도록 하려면 [상태 모니터를 설치][redfield]해야 합니다. Azure 웹앱 또는 VM에서 사용하려면 [Application Insights 확장을 선택][azure]합니다.

[TrackDependency API](app-insights-api-custom-events-metrics.md#track-dependency)를 사용하여 종속성 추적 코드를 작성할 수 있습니다.

## [Microsoft.ApplicationInsights.Web](http://www.nuget.org/packages/Microsoft.ApplicationInsights.Web)NuGet 패키지

`Microsoft.ApplicationInsights.Web`Nuget 패키지는 다음 원격 분석 이니셜라이저와 모듈을 `ApplicationInsights.config`에 제공합니다.

```
<ApplicationInsights xmlns="http://schemas.microsoft.com/ApplicationInsights/2013/Settings">
  <TelemetryInitializers>
    <Add Type="Microsoft.ApplicationInsights.Extensibility.Web.SyntheticTelemetryInitializer, Microsoft.ApplicationInsights.Extensibility.Web" />
    <Add Type="Microsoft.ApplicationInsights.Extensibility.Web.ClientIpHeaderTelemetryInitializer, Microsoft.ApplicationInsights.Extensibility.Web" />
    <Add Type="Microsoft.ApplicationInsights.Extensibility.Web.UserAgentTelemetryInitializer, Microsoft.ApplicationInsights.Extensibility.Web" />
    <Add Type="Microsoft.ApplicationInsights.Extensibility.Web.OperationNameTelemetryInitializer, Microsoft.ApplicationInsights.Extensibility.Web" />
    <Add Type="Microsoft.ApplicationInsights.Extensibility.Web.OperationIdTelemetryInitializer, Microsoft.ApplicationInsights.Extensibility.Web" />
    <Add Type="Microsoft.ApplicationInsights.Extensibility.Web.UserTelemetryInitializer, Microsoft.ApplicationInsights.Extensibility.Web" />
    <Add Type="Microsoft.ApplicationInsights.Extensibility.Web.SessionTelemetryInitializer, Microsoft.ApplicationInsights.Extensibility.Web" />
    <Add Type="Microsoft.ApplicationInsights.Extensibility.Web.AzureRoleEnvironmentTelemetryInitializer, Microsoft.ApplicationInsights.Extensibility.Web" />
    <Add Type="Microsoft.ApplicationInsights.Extensibility.Web.DomainNameRoleInstanceTelemetryInitializer, Microsoft.ApplicationInsights.Extensibility.Web" />
    <Add Type="Microsoft.ApplicationInsights.Extensibility.Web.BuildInfoConfigComponentVersionTelemetryInitializer, Microsoft.ApplicationInsights.Extensibility.Web" />
    <Add Type="Microsoft.ApplicationInsights.Extensibility.Web.DeviceTelemetryInitializer, Microsoft.ApplicationInsights.Extensibility.Web"/>
  </TelemetryInitializers>
  <TelemetryModules>
    <Add Type="Microsoft.ApplicationInsights.Extensibility.Web.RequestTrackingTelemetryModule, Microsoft.ApplicationInsights.Extensibility.Web" />
    <Add Type="Microsoft.ApplicationInsights.Extensibility.Web.ExceptionTrackingTelemetryModule, Microsoft.ApplicationInsights.Extensibility.Web" />
    <Add Type="Microsoft.ApplicationInsights.Extensibility.Web.DeveloperModeWithDebuggerAttachedTelemetryModule, Microsoft.ApplicationInsights.Extensibility.Web" />
  </TelemetryModules>
</ApplicationInsights>
```

**이니셜라이저**

* `SyntheticTelemetryInitializer`은 `User`, `Session` 및 가용성 테스트와 같은 가상 소스에서 요청을 처리하는 경우 모든 원격 분석 항목의 `Operation` 컨텍스트 속성을 업데이트합니다. Application Insights 포털은 기본적으로 가상 원격 분석을 표시 하지 않습니다.
* `ClientIpHeaderTelemetryInitializer`은 `X-Forwarded-For` HTTP 헤더 기반의 모든 원격 분석 항목의 `Location` 컨텍스트의 `Ip` 속성을 업데이트 합니다.
* `UserAgentTelemetryInitializer`은 `User-Agent` HTTP 헤더 기반의 모든 원격 분석 항목의 `User` 컨텍스트의 `UserAgent` 속성을 업데이트 합니다.
* `OperationNameTelemetryInitializer`은 `RequestTelemetry`의 `Name` 속성과 HTTP 메서드를 기반으로 한 모든 원격 분석 아이템의 `Operation` 컨텍스트의 `Name` 속성을 업데이트뿐만 아니라 ASP.NET MVC 컨트롤러와 요청을 처리하는 데 작업을 불러옵니다.
* `OperationNameTelemetryInitializer`은(는) 자동으로 생성된 `RequestTelemetry.Id`(으)로 요청을 처리하는 동안 추적되는 모든 원격 분석 항목의 `Operation.Id` 컨텍스트 속성을 업데이트합니다.
* `UserTelemetryInitializer`은`Id` 및 사용자의 브라우저에서 실행되는 Application insights JavaScript 계측 코드에 의해 제공된 `ai_user` 쿠키의 추출된 값을 사용하여 모든 원격 분석 항목에 대한 `User` 4 컨텍스트의`AcquisitionDate`속성을 업데이트합니다.
* `SessionTelemetryInitializer`은 사용자의 브라우저에서 실행되는 Application insights JavaScript 계측 코드에 의해 제공된 `ai_session`쿠키의 추출된 값을 사용하여 모든 원격 분석 항목에 대한 `Session` 컨텍스트의 `Id`속성을 업데이트합니다. 
* `AzureRoleEnvironmentTelemetryInitializer`은 `RoleName`및 Azure 런타임 환경에서 추출된 정보를 사용하여 모든 원격 분석 항목에 대해 `Device`컨텍스트에 대한 `RoleInstance`속성을 업데이트합니다.
* `DomainNameRoleInstanceTelemetryInitializer`은 웹 응용 프로그램이 실행되는 컴퓨터의 도메인 이름을 사용하여 모든 원격 분석 항목에 대해 `Device`컨텍스트의 `RoleInstance`속성을 업데이트합니다.
* `BuildInfoConfigComponentVersionTelemetryInitializer`은 TFS 빌드에 의해 생성된 `BuildInfo.config`파일로부터 추출된 값을 사용하여 모든 원격 분석 항목에 대한 `Component`컨텍스트의 `Version`속성을 업데이트합니다.
* `DeviceTelemetryInitializer`은 모든 원격 분석 항목에 대한 `Device` 컨텍스트의 다음 속성을 업데이트합니다.
 - `Type`은 "PC"로 설정
 - `Id`은 웹 응용 프로그램이 실행되고 있는 컴퓨터의 도메인 이름으로 설정되었습니다.
 - `OemName`은 WMI를 사용하여 `Win32_ComputerSystem.Manufacturer`필드에서 추출된 값으로 설정 되었습니다.
 - `Model`은 WMI를 사용하여 `Win32_ComputerSystem.Model`필드에서 추출된 값으로 설정 되었습니다.
 - `NetworkType`은 `NetworkInterface`로부터 추출된 값으로 설정되었습니다.
 - `Language`은 `CurrentCulture`의 이름으로 설정되었습니다.

**모듈**

* `RequestTrackingTelemetryModule`웹앱에서 받은 요청을 추적하고 응답 시간을 측정합니다.
* `ExceptionTrackingTelemetryModule` 웹앱에서 처리되지 않은 예외를 추적합니다. [오류 및 예외][exceptions]를 참조하세요.
* 한번에 원격 분석 항목을 하나씩 `DeveloperModeWithDebuggerAttachedTelemetryModule`이 Application Insights `TelemetryChannel`를 강제로 데이터를 응용 프로그램 프로세스에 디버거가 연결될 때 즉시 보냅니다. 응용 프로그램이 원격 분석을 추적할 때와 중요한 cpu 및 네트워크 대역폭 오버헤드의 Application insights 포털에 나타날 때의 간격의 시간차를 감소시킵니다.

## [Microsoft.ApplicationInsights.PerfCounterCollector](http://www.nuget.org/packages/Microsoft.ApplicationInsights.PerfCounterCollector)NuGet 패키지

`Microsoft.ApplicationInsights.PerfCounterCollector`다음 원격 분석 모듈을 추가하는 NuGet 패키지는 기본값을 `ApplicationInsights.config`로 합니다.

```
<ApplicationInsights xmlns="http://schemas.microsoft.com/ApplicationInsights/2013/Settings">
  <TelemetryModules>
    <Add Type="Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.PerformanceCollectorModule, Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector" />
  </TelemetryModules>
</ApplicationInsights>
```

### PerformanceCollectorModule

`PerformanceCollectorModule`Windows 성능 카운터의 수를 추적합니다. 이러한 카운터는 메트릭 탐색기에서 차트를 클릭하여 해당 세부 정보 블레이드가 열릴 때 볼 수 있습니다.

추가 성능 카운터(표준 Windows 카운터 및 추가한 다른 모든 카운터)를 모니터링할 수 있습니다.
      
추가 성능 카운터를 수집하려면 다음 구문을 사용합니다.

```
<Add Type="Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.PerformanceCollectorModule, Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector">
  <Counters>
    <Add PerformanceCounter="\MyCategory\MyCounter" />
    <Add PerformanceCounter="\Process(??APP_WIN32_PROC??)\Handle Count" ReportAs="Process handle count" />
    <!-- ... -->
  </Counters>
</Add>
```      
      
`PerformanceCounter`는 `\CategoryName(InstanceName)\CounterName` 또는 `\CategoryName\CounterName`이어야 합니다.
      
`ReportAs` 특성을 제공하는 경우 이 특성은 Application Insights에 표시되는 이름으로 사용됩니다.

Application Insights에 보고하려면 카운터 이름에 문자, 소괄호, 슬래시, 하이픈, 밑줄, 공백 및 점만 사용해야 합니다.

모니터링하려는 카운터에 '#' 또는 숫자와 같은 잘못된 문자가 포함된 경우 ReportAs를 사용해야 합니다.
      
다음 자리 표시자는 `InstanceName`으로 지원됩니다.

    ?APP_WIN32_PROC?? - instance name of the application process  for Win32 counters.
    ??APP_W3SVC_PROC?? - instance name of the application IIS worker process for IIS/ASP.NET counters.
    ??APP_CLR_PROC?? - instance name of the application CLR process for .NET counters.

## 채널 매개 변수(Java)

이러한 매개 변수는 Java SDK가 수집한 원격 분석 데이터를 저장하고 플러시하는 방법에 영향을 줍니다.

#### MaxTelemetryBufferCapacity

SDK의 메모리 내 저장소에 저장할 수 있는 원격 분석 항목의 수입니다. 이 수에 도달하면 원격 분석 버퍼가 플러시됩니다. 즉, 원격 분석 항목이 Application Insights 서버로 전송됩니다.

-	최소: 1
-	최대: 1000
-	기본값: 500

```

  <ApplicationInsights>
      ...
      <Channel>
       <MaxTelemetryBufferCapacity>100</MaxTelemetryBufferCapacity>
      </Channel>
      ...
  </ApplicationInsights>
```

#### FlushIntervalInSeconds 

메모리 내 저장소에 저장된 데이터가 플러시될(Application Insights로 보내는) 빈도를 결정합니다.

-	최소: 1
-	최대: 300
-	기본값: 5

```

    <ApplicationInsights>
      ...
      <Channel>
        <FlushIntervalInSeconds>100</FlushIntervalInSeconds>
      </Channel>
      ...
    </ApplicationInsights>
```

#### MaxTransmissionStorageCapacityInMB

로컬 디스크에서 영구 저장소에 할당되는 최대 크기(MB)를 결정합니다. 이 저장소는 Application Insights 끝점으로 전송하지 못한 원격 분석 항목을 유지하는 데 사용됩니다. 저장소 크기를 충족하면 새 원격 분석 항목이 삭제됩니다.

-	최소: 1
-	최대: 100
-	기본값: 10

```

   <ApplicationInsights>
      ...
      <Channel>
        <MaxTransmissionStorageCapacityInMB>50</MaxTransmissionStorageCapacityInMB>
      </Channel>
      ...
   </ApplicationInsights>
```

## 원격 분석 이니셜라이저

응용 프로그램에서 수집된 원격 분석을 필터링하고 수정하는 원격 분석 이니셜라이저를 작성할 수 있습니다. 이니셜라이저는 .config 파일에서 표준 모듈과 함께 초기화될 수 있습니다. [자세히 알아보기](app-insights-api-filtering-sampling.md)


## InstrumentationKey

데이터가 표시되는 Application Insights 리소스를 결정합니다. 일반적으로 각 응용 프로그램에 대해 별도 키를 사용하여 별도 리소스를 만듭니다.

키를 동적으로 설정하려는 경우, 예를 들어 응용 프로그램의 결과를 다른 리소스로 보내려는 경우 구성 파일에서 키를 생략하고 대신 코드에 설정할 수 있습니다.

표준 원격 분석 모듈을 비롯한 TelemetryClient의 모든 인스턴스에 대한 키를 설정하려면 TelemetryConfiguration.Active에 키를 설정합니다. ASP.NET 서비스의 global.aspx.cs와 같은 초기화 메서드에 키를 설정합니다.

```C#

    protected void Application_Start()
    {
      Microsoft.ApplicationInsights.Extensibility.
        TelemetryConfiguration.Active.InstrumentationKey = 
          // - for example -
          WebConfigurationManager.Settings["ikey"];
      //...
```

특정 이벤트 집합을 다른 리소스로 보내려는 경우 특정 TelemetryClient에 대한 키를 설정할 수 있습니다.

```C#

    var tc = new TelemetryClient();
    tc.Context.InstrumentationKey = "----- my key ----";
    tc.TrackEvent("myEvent");
    // ...

```

[Web API에 대해 자세히 알아보세요][api].

새 키를 얻으려면 [Application Insights 포털에서 새 리소스를 만듭니다][new].

<!--Link references-->

[api]: app-insights-api-custom-events-metrics.md
[azure]: ../insights-perf-analytics.md
[client]: app-insights-javascript.md
[diagnostic]: app-insights-diagnostic-search.md
[exceptions]: app-insights-web-failures-exceptions.md
[netlogs]: app-insights-asp-net-trace-logs.md
[new]: app-insights-create-new-resource.md
[redfield]: app-insights-monitor-performance-live-website-now.md
[start]: app-insights-overview.md

<!----HONumber=Nov15_HO1-->