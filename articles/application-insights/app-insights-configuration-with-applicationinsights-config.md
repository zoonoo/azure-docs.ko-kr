<properties 
	pageTitle="ApplicationInsights.config 또는 .xml로 Application Insights SDK 구성" 
	description="데이터 수집 모듈을 사용하거나 사용하지 않도록 설정하고 성능 카운터 및 기타 매개 변수를 추가합니다." 
	services="application-insights"
    documentationCenter="" 
	authors="alancameronwills" 
	manager="ronmart"/>
 
<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/04/2015" 
	ms.author="awills"/>

# ApplicationInsights.config 또는 .xml로 Application Insights SDK 구성

Application Insights SDK는 여러 모듈로 구성됩니다. 핵심 모듈은 Application Insights 포털에 원격 분석을 보내는 기본 API를 제공합니다. 추가 모듈은 응용 프로그램 및 해당 컨텍스트에서 데이터를 수집합니다. 구성 파일을 조정하여 모듈을 활성화하거나 비활성화하고 이 중 일부 모듈의 매개 변수를 설정할 수 있습니다.

구성 파일의 이름은 응용 프로그램 유형에 따라 `ApplicationInsights.config` 또는 `ApplicationInsights.xml`입니다. 구성 파일은 [SDK 설치][start]를 할 때 프로젝트에 자동으로 추가됩니다. 또한 [IIS 서버의 상태 모니터][redfield]에 의해 또는 [Azure 웹사이트 또는 VM에 대한 Appplication Insights 확장을 선택][azure]하는 경우 웹앱에 추가됩니다.

[웹 페이지에서 SDK][client]를 제어할 동급의 파일은 없습니다.


## 원격 분석 모듈

각 모듈에 대해 구성 파일에 노드가 있습니다. 모듈을 사용하지 않으려면 노드를 삭제하거나 주석으로 처리합니다.

#### Implementation.Tracing.DiagnosticsTelemetryModule

SDK에서의 오류를 보고합니다. 예를 들어 SDK가 성능 카운터에 액세스할 수 없는 경우 또는 사용자 지정 TelemetryInitializer에서 예외가 발생하는 경우입니다.

데이터가 [진단 검색][diagnostic]에 표시됩니다.

#### RuntimeTelemetry.RemoteDependencyModule

응용 프로그램에서 사용하는 외부 구성 요소의 응답 성능에 대한 데이터를 수집합니다. 이 모듈이 IIS 서버에서 작동하도록 하려면 [상태 모니터를 설치][redfield]해야 합니다. Azure 웹앱 또는 VM에서 사용하려면 [Application Insights 확장을 선택][azure]합니다.

#### Web.WebApplicationLifecycleModule

원격 분석 데이터의 모든 메모리 내 버퍼를 플러시하려고 하므로 프로세스 종료 시 손실되지 않습니다.

#### Web.RequestTracking.TelemetryModules.WebRequestTrackingTelemetryModule

웹앱에 도착하는 요청 수를 계산하고 응답 시간을 측정합니다.

#### Web.RequestTracking.TelemetryModules.WebExceptionTrackingTelemetryModule

웹앱에서 처리되지 않은 예외를 계산합니다. [오류 및 예외][exceptions]를 참조하세요.



#### Web.TelemetryModules.DeveloperModeWithDebuggerAttachedTelemetryModule



## 성능 수집기 모듈

#### PerfCollector.PerformanceCollectorModule

기본적으로 이 모듈은 다양한 Windows 성능 카운터를 수집합니다. 메트릭 탐색기에서 필터 블레이드를 열면 이러한 카운터를 볼 수 있습니다.

추가 성능 카운터(표준 Windows 카운터 및 추가한 다른 모든 카운터)를 모니터링할 수 있습니다.
      
추가 성능 카운터를 수집하려면 다음 구문을 사용합니다.
      
      <Counters>
        <Add PerformanceCounter="\MyCategory\MyCounter" />
        <Add PerformanceCounter="\Process(??APP_WIN32_PROC??)\Handle Count" ReportAs="Process handle count" />
        ...
      </Counters>
      
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

    <ApplicationInsights> ... <Channel> <MaxTelemetryBufferCapacity>100</MaxTelemetryBufferCapacity> </Channel> ... </ApplicationInsights>

#### FlushIntervalInSeconds 

메모리 내 저장소에 저장된 데이터가 플러시될(Application Insights로 보내는) 빈도를 결정합니다.

-	최소: 1
-	최대: 300
-	기본값: 5

    <ApplicationInsights> ... <Channel> <FlushIntervalInSeconds>100</FlushIntervalInSeconds> </Channel> ... </ApplicationInsights>

#### MaxTransmissionStorageCapacityInMB

로컬 디스크에서 영구 저장소에 할당되는 최대 크기(MB)를 결정합니다. 이 저장소는 Application Insights 끝점으로 전송하지 못한 원격 분석 항목을 유지하는 데 사용됩니다. 저장소 크기를 충족하면 새 원격 분석 항목이 삭제됩니다.

-	최소: 1
-	최대: 100
-	기본값: 10

    <ApplicationInsights> ... <Channel> <MaxTransmissionStorageCapacityInMB>50</MaxTransmissionStorageCapacityInMB> </Channel> ... </ApplicationInsights>


## 컨텍스트 이니셜라이저

이러한 구성 요소는 플랫폼에서 데이터를 수집합니다. 데이터는 [TelemetryContext 개체][api]에 수집됩니다.

#### BuildInfoConfigComponentVersionContextInitializer

#### DeviceContextInitializer

#### MachineNameContextInitializer

#### ComponentContextInitializer

#### Web.AzureRoleEnvironmentContextInitializer

#### Web.DomainNameRoleInstanceContextInitializer

#### Web.BuildInfoConfigComponentVersionContextInitializer

#### Web.DeviceContextInitializer



## 원격 분석 이니셜라이저

이러한 구성 요소는 Application Insights로 전송된 각 원격 분석 이벤트에 데이터를 추가합니다.


#### Web.TelemetryInitializers.WebSyntheticTelemetryInitializer

이 구성 요소는 검색 엔진 및 웹 테스트 등의 로봇에서 가져온 것으로 보이는 HTTP 요청을 식별합니다. 이 구성 요소는 TelemetryClient.Context.Operation.SyntheticSource를 설정합니다.

#### Web.TelemetryInitializers.WebOperationNameTelemetryInitializer

원격 분석의 모든 항목에 작업 이름을 추가합니다. 웹앱에서 "작업"은 HTTP 요청을 의미합니다. TelemetryClient.Context.Operation.Name을 설정합니다.

#### Web.TelemetryInitializers.WebOperationIdTelemetryInitializer

[진단 검색][diagnostic]에서 "같은 요청에서 이벤트 찾기" 기능을 사용할 수 있습니다. TelemetryClient.Context.Operation.Id를 설정합니다.

Application Insights로 전송된 각 데이터 항목에 작업 ID를 추가합니다. 웹앱에서 "작업"은 HTTP 요청입니다. 따라서 예를 들어 요청과 해당 요청 처리의 일부인 사용자 지정 이벤트 및 추적은 모두 작업 ID가 동일합니다.

#### Web.TelemetryInitializers.WebUserTelemetryInitializer

모든 원격 분석 항목에 익명 사용자 ID를 추가합니다. 이렇게 하면 진단 검색에서 한 사용자의 활동과 관련된 이벤트만 필터링할 수 있습니다. 예를 들어 예외가 보고되는 경우 해당 사용자가 수행한 활동을 추적할 수 있습니다.

telemetryClient.Context.User를 설정합니다.

#### Web.TelemetryInitializers.WebSessionTelemetryInitializer

각 이벤트에 세션 ID를 추가합니다. telemetryClient.Context.Session을 설정합니다.

## 사용자 지정 이니셜라이저


표준 이니셜라이저가 응용 프로그램에 적합하지 않으면 이니셜라이저를 직접 만들 수 있습니다.

컨텍스트 이니셜라이저를 사용하여 모든 원격 분석 클라이언트를 초기화하는 데 사용될 값을 설정할 수 있습니다. 예를 들어 앱 버전을 둘 이상 게시한 경우 사용자 지정 속성으로 필터링하여 데이터를 구분할 수 있는지 확인할 수 있습니다.

    plublic class MyContextInitializer: IContextInitializer
    {
        public void Initialize(TelemetryContext context)
        {
          context.Properties["AppVersion"] = "v2.1";
        }
    }

원격 분석 이니셜라이저를 사용하여 각 이벤트에 처리를 추가합니다. 예를 들어 웹 SDK는 응답 코드가 400보다 크거나 같은 모든 요청을 실패로 지정합니다. 이 동작을 재정의할 수 있습니다.

    public class MyTelemetryInitializer : ITelemetryInitializer
    {
        public void Initialize(ITelemetry telemetry)
        {
            var requestTelemetry = telemetry as RequestTelemetry;
            if (requestTelemetry == null) return;
            int code;
            bool parsed = Int32.TryParse(requestTelemetry.ResponseCode, out code);
            if (!parsed) return;
            if (code >= 400 && code < 500)
            {
                requestTelemetry.Success = true;
                requestTelemetry.Context.Properties["Overridden400s"] = "true";
            }            
        }
    }
 
이니셜라이저를 설치하려면 ApplicationInsights.config에 다음 줄을 추가합니다.

    <TelemetryInitializers> <!-- or ContextInitializers -->
    <Add Type="MyNamespace.MyTelemetryInitializer, MyAssemblyName" />


또는 응용 프로그램 실행 시 처음에 이니셜라이저를 설치하는 코드를 작성할 수 있습니다. 예:


    // In the app initializer such as Global.asax.cs:

    protected void Application_Start()
    {
      TelemetryConfiguration.Active.TelemetryInitializers.Add(
                new MyTelemetryInitializer());
            ...




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
[start]: app-insights-get-started.md

 

<!---HONumber=62-->