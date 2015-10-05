<properties 
	pageTitle="Windows 데스크톱 앱 및 서비스용 Application Insights" 
	description="Application Insights를 사용하여 Windows 데스크톱 앱의 사용량 및 성능을 분석합니다." 
	services="application-insights" 
    documentationCenter="windows"
	authors="alancameronwills" 
	manager="douge"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="09/07/2015" 
	ms.author="awills"/>

# Windows 데스크톱 앱, 서비스 및 작업자 역할의 Application Insights

*Application Insights는 미리 보기 상태입니다.*

[AZURE.INCLUDE [app-insights-selector-get-started](../../includes/app-insights-selector-get-started.md)]

Application Insights를 사용하면 사용량 및 성능을 위해 배포된 응용 프로그램을 모니터링할 수 있습니다.

데스크톱 앱, 백그라운드 서비스 및 작업자 역할을 포함하는 모든 Windows 응용 프로그램은 Application Insights 코어 SDK를 사용하여 원격 분석을 Application Insights에 보낼 수 있습니다. 또한 클래스 라이브러리 프로젝트에 Application Insights SDK를 추가할 수 있습니다.

코어 SDK는 웹 또는 장치 SDK와 달리 API만을 제공합니다. 데이터를 자동으로 수집하는 모듈이 포함되지 않으므로 코드를 작성하여 사용자 고유의 원격 분석을 보내야 합니다. 또한 성능 카운터 수집기와 같은 일부 다른 패키지는 데스크톱 앱에서 작동합니다.


## <a name="add"></a> Application Insights 리소스 만들기


1.  [Azure 포털][portal]에서 새 Application Insights 리소스를 만듭니다. 응용 프로그램 종류에 대해 Windows 스토어 앱을 선택합니다. 

    ![새로 만들기, Application Insights 클릭](./media/app-insights-windows-desktop/01-new.png)

    (응용 프로그램 종류 선택은 개요 블레이드의 내용 및 [메트릭 탐색기][metrics]에서 사용할 수 있는 속성을 설정합니다.)

2.  계측 키를 복사합니다.

    ![속성 클릭, 키 선택 및 ctrl+C 누르기](./media/app-insights-windows-desktop/02-props.png)

## <a name="sdk"></a>응용 프로그램에 SDK를 설치합니다.


1. Visual Studio에서 데스크톱 앱 프로젝트의 NuGet 패키지를 편집합니다.

    ![마우스 오른쪽 단추로 프로젝트 클릭 및 Nuget 패키지 관리 선택](./media/app-insights-windows-desktop/03-nuget.png)

2. Application Insights 코어 API 패키지를 설치합니다. Microsoft.ApplicationInsights.

    !["Application Insights" 검색](./media/app-insights-windows-desktop/04-core-nuget.png)

    *다른 패키지를 사용할 수 있나요?*

    예, 해당 모듈을 사용하려는 경우 성능 카운터 및 종속성 수집기 패키지와 같은 다른 패키지를 설치할 수 있습니다. Microsoft.ApplicationInsights.Web은 이러한 여러 패키지를 포함합니다. [로그 또는 추적 수집기 패키지](app-insights-asp-net-trace-logs.md)를 사용하려는 경우 웹 서버 패키지를 시작합니다.

    (Windows 스토어 앱용 Microsoft.ApplicationInsights.Windows를 사용하지 마십시오.)

3. InstrumentationKey를 설정합니다.

    * 코어 API 패키지 Microsoft.ApplicationInsights를 설치한 경우 main()와 같은 코드에서 키를 설정해야 합니다. 

     `TelemetryConfiguration.Active.InstrumentationKey = "`*키*`";`

    * 다른 패키지 중 하나를 설치한 경우 코드를 사용하여 키를 설정하거나 ApplicationInsights.config에서 설정할 수 있습니다.
 
     `<InstrumentationKey>`*키*`</InstrumentationKey>`



## <a name="telemetry"></a>원격 분석 호출 삽입

`TelemetryClient` 인스턴스를 만들고 [이를 사용하여 원격 분석을 전송][api]합니다.


예를들어, Windows Forms 응용 프로그램에서는 다음을 작성할 수 있습니다.

```C#

    public partial class Form1 : Form
    {
        private TelemetryClient tc = new TelemetryClient();
        ...
        private void Form1_Load(object sender, EventArgs e)
        {
            // Alternative to setting ikey in config file:
            tc.InstrumentationKey = "key copied from portal";

            // Set session data:
            tc.Context.User.Id = Environment.GetUserName();
            tc.Context.Session.Id = Guid.NewGuid().ToString();
            tc.Context.Device.OperatingSystem = Environment.OSVersion.ToString();

            // Log a page view:
            tc.TrackPageView("Form1");
            ...
        }

        protected override void OnClosing(CancelEventArgs e)
        {
            stop = true;
            if (tc != null)
            {
                tc.Flush(); // only for desktop apps

                // Allow time for flushing:
                System.Threading.Thread.Sleep(1000);
            }
            base.OnClosing(e);
        }

```

[Application Insights API][api] 중 하나를 사용하여 원격 분석을 보냅니다. Windows 데스크톱 응용 프로그램에서는 원격 분석이 자동으로 전송되지 않습니다. 일반적으로 다음을 사용할 수 있습니다.

* 양식, 페이지 또는 탭 전환에서 `TrackPageView(pageName)`
* 다른 사용자 작업에 대한 `TrackEvent(eventName)`
* 특정 이벤트에 연결되지 않은 메트릭의 정기적인 보고서를 보내기 위한 배경 작업에서 `TrackMetric(name, value)`입니다.
* [진단 로깅][diagnostic]을 위한 `TrackTrace(logEvent)`
* catch 절에서 `TrackException(exception)`
* 앱을 닫기 전에 모든 원격 분석이 전송되었는지 확인하려면 `Flush()`입니다. 코어 API(Microsoft.ApplicationInsights)를 사용하는 경우 이를 사용합니다. 웹 및 장치 SDK는 이 동작을 자동으로 구현합니다. (인터넷을 항상 사용할 수 없는 컨텍스트에서 앱을 실행하는 경우 [지속성 채널](#persistence-channel)을 참조하세요.)


#### 컨텍스트 이니셜라이저

사용자 및 세션 수를 보려면 각 `TelemetryClient`인스턴스에 대한 값을 설정할 수 있습니다. 또는 컨텍스트 이니셜라이저를 사용하여 모든 클라이언트에 대해 이 덧셈을 수행할 수 있습니다.

```C#

    class UserSessionInitializer: IContextInitializer
    {
        public void Initialize(TelemetryContext context)
        {
            context.User.Id = Environment.UserName;
            context.Session.Id = Guid.NewGuid().ToString();
        }
    }

    static class Program
    {
        ...
        static void Main()
        {
            TelemetryConfiguration.Active.ContextInitializers.Add(
                new UserSessionInitializer());
            ...

```



## <a name="run"></a>프로젝트 실행

[F5를 사용하여 응용 프로그램을 실행](http://msdn.microsoft.com/library/windows/apps/bg161304.aspx)하고 이를 사용하여 일부 원격 분석을 생성합니다.

Visual Studio에 전송한 이벤트 수가 표시됩니다.

![](./media/app-insights-windows-desktop/appinsights-09eventcount.png)



## <a name="monitor"></a>데이터 모니터링 보기

Azure 포털에서 사용자 응용 프로그램 블레이드로 돌아갑니다.

첫 번째 이벤트가 [진단 검색](app-insights-diagnostic-search.md)에 표시됩니다.

더 많은 데이터를 기대하는 경우 몇 초 후에 새로고침을 클릭합니다.

TrackMetric 또는 TrackEvent의 측정 매개 변수를 사용한 경우 [메트릭을 탐색기][metrics]를 열고 필터 블레이드를 엽니다. 여기에 메트릭을 표시해야 하지만 때로 파이프라인을 통해 얻으려면 시간이 걸리므로 필터 블레이드 닫고 잠시 기다린 다음 새로 고쳐야 할 수 있습니다.



## 지속성 채널 

인터넷 연결을 항상 사용할 수 없거나 느린 곳에서 앱을 실행하는 경우 기본 메모리 내 채널 대신 지속성 채널을 사용하는 것이 좋습니다.

기본 메모리 내 채널에서 앱을 닫을 때까지 전송되지 않은 모든 원격 분석은 손실됩니다. `Flush()`를 사용하여 버퍼에 남아 있는 데이터를 보내려고 시도할 수 있지만 인터넷이 연결되어 있지 않거나 전송이 완료되기 전에 앱이 종료되면 데이터가 손실됩니다.

반면 지속성 채널은 파일을 포털에 보내기 전에 파일에서 원격 분석을 버퍼링합니다. `Flush()`은 데이터가 파일에 저장되도록 보장합니다. 앱을 닫을 때까지 모든 데이터가 전송되지 않은 경우 파일에 유지됩니다. 앱을 다시 시작하면 인터넷이 연결된 경우 데이터가 전송됩니다. 연결을 사용할 수 있을 때까지 필요한 만큼 데이터 파일이 누적됩니다.

### 지속성 채널을 사용하려면

1. NuGet 패키지 [Microsoft.ApplicationInsights.PersistenceChannel](https://www.nuget.org/packages/Microsoft.ApplicationInsights.PersistenceChannel)을 가져옵니다.
2. 초기화가 적합한 위치에서 앱에 이 코드를 포함합니다.
 
    ```C# 

      using Microsoft.ApplicationInsights.Channel;
      using Microsoft.ApplicationInsights.Extensibility;
      ...

      // Set up 
      TelemetryConfiguration.Active.InstrumentationKey = "YOUR INSTRUMENTATION KEY";
 
      TelemetryConfiguration.Active.TelemetryChannel = new PersistenceChannel();
    
    ``` 
3. 앱을 닫기 전에 `telemetryClient.Flush()`을 사용하여 데이터를 포털로 전송하거나 파일에 저장합니다.

 
지속성 채널은 응용 프로그램에서 생성된 이벤트 수가 상대적으로 작고 연결이 자주 신뢰할 수 없는 장치 시나리오에 최적화됩니다. 이 채널은 신뢰할 수 있는 디스크의 저장소에 먼저 이벤트를 쓰고 전송하려 합니다.

#### 예

처리되지 않은 예외를 모니터링하려는 경우를 가정해 보겠습니다. `UnhandledException` 이벤트에 구독합니다. 콜백에서 플러시에 호출을 포함하여 원격 분석이 유지되도록 합니다.
 
```C# 

AppDomain.CurrentDomain.UnhandledException += CurrentDomain_UnhandledException; 
 
... 
 
private void CurrentDomain_UnhandledException(object sender, UnhandledExceptionEventArgs e) 
{ 
    ExceptionTelemetry excTelemetry = new ExceptionTelemetry((Exception)e.ExceptionObject); 
    excTelemetry.SeverityLevel = SeverityLevel.Critical; 
    excTelemetry.HandledAt = ExceptionHandledAt.Unhandled; 
 
    telemetryClient.TrackException(excTelemetry); 
 
    telemetryClient.Flush(); 
} 

``` 

앱이 종료되면 압축된 이벤트를 포함하는 `%LocalAppData%\Microsoft\ApplicationInsights`에 파일이 표시됩니다.
 
다음 번에 이 응용 프로그램을 시작하면 채널이 해당 파일을 선택하고 가능한 경우 Application Insights에 원격 분석을 제공합니다.

#### 테스트 예제

```C#

using Microsoft.ApplicationInsights;
using Microsoft.ApplicationInsights.Channel;
using Microsoft.ApplicationInsights.Extensibility;

namespace ConsoleApplication1
{
    class Program
    {
        static void Main(string[] args)
        {
            // Send data from the last time the app ran
            System.Threading.Thread.Sleep(5 * 1000);

            // Set up persistence channel

            TelemetryConfiguration.Active.InstrumentationKey = "YOUR KEY";
            TelemetryConfiguration.Active.TelemetryChannel = new PersistenceChannel();

            // Send some data

            var telemetry = new TelemetryClient();

            for (var i = 0; i < 100; i++)
            {
                var e1 = new Microsoft.ApplicationInsights.DataContracts.EventTelemetry("persistenceTest");
                e1.Properties["i"] = "" + i;
                telemetry.TrackEvent(e1);
            }

            // Make sure it's persisted before we close
            telemetry.Flush();
        }
    }
}

```


지속성 채널의 코드는 [github](https://github.com/Microsoft/ApplicationInsights-dotnet/tree/master/src/TelemetryChannels/PersistenceChannel)에 있습니다.


## <a name="usage"></a>다음 단계

[앱 사용량 추적][knowUsers]

[진단 로그 캡처 및 검색][diagnostic]

[문제 해결][qna]




<!--Link references-->

[diagnostic]: app-insights-diagnostic-search.md
[metrics]: app-insights-metrics-explorer.md
[portal]: http://portal.azure.com/
[qna]: app-insights-troubleshoot-faq.md
[knowUsers]: app-insights-overview-usage.md
[api]: app-insights-api-custom-events-metrics.md
[CoreNuGet]: https://www.nuget.org/packages/Microsoft.ApplicationInsights
 

<!---HONumber=Sept15_HO4-->