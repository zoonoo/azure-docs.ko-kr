<properties 
	pageTitle="Windows 서비스용 Application Insights" 
	description="Application Insights를 사용하여 Windows 백그라운드 서비스의 사용량 및 성능을 분석합니다." 
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
	ms.date="04/27/2016" 
	ms.author="awills"/>

# Windows 백그라운드 서비스의 Application Insights

*Application Insights는 미리 보기 상태입니다.*

[Visual Studio Application Insights](app-insights-get-started.md)를 사용하면 배포된 응용 프로그램의 사용량 및 성능을 모니터링할 수 있습니다.

백그라운드 서비스 및 작업자 역할을 포함하는 모든 Windows 응용 프로그램은 Application Insights SDK를 사용하여 Application Insights에 원격 분석을 보낼 수 있습니다. 또한 클래스 라이브러리 프로젝트에 SDK를 추가할 수 있습니다.

성능 카운터나 종속성 호출을 모니터링하는 등의 목적으로 사용할 표준 데이터 수집기를 선택하거나, 코어 API를 사용하고 사용자 고유의 원격 분석을 작성할 수 있습니다.

먼저 다른 유형의 Windows 응용 프로그램을 사용하여 작업하는지 여부를 확인합니다.

* 웹앱: [ASP.NET 4](app-insights-asp-net.md), [ASP.NET 5](app-insights-asp-net-five.md)로 이동합니다.
* [Azure 클라우드 서비스](app-insights-cloudservices.md)
* 데스크톱 앱: [HockeyApp](https://hockeyapp.net)을 사용하는 것이 좋습니다. HockeyApp를 사용하면 사용량 및 충돌 보고서를 모니터링할 수 있을 뿐만 아니라 배포, 라이브 테스트 및 사용자 의견을 관리할 수 있습니다. 또한 [데스크톱 앱에서 Application Insights에 원격 분석을 전송](app-insights-windows-desktop.md)할 수 있습니다. 


## <a name="add"></a> Application Insights 리소스 만들기


1.  [Azure 포털][portal]에서 새 Application Insights 리소스를 만듭니다. 응용 프로그램 유형으로 ASP.NET 앱을 선택합니다. 

    ![새로 만들기, Application Insights 클릭](./media/app-insights-windows-services/01-new.png)


2.  계측 키를 복사합니다. 방금 만든 새 리소스의 필수 드롭다운에서 키를 찾습니다. 응용 프로그램 맵을 닫거나 리소스에 대한 개요 블레이드로 왼쪽으로 스크롤합니다.

    ![Essentials 클릭, 키 선택 및 Ctrl+C 누르기](./media/app-insights-windows-services/10.png)

## <a name="sdk"></a>응용 프로그램에 SDK를 설치합니다.


1. Visual Studio에서 Windows 응용 프로그램 프로젝트의 NuGet 패키지를 편집합니다.

    ![마우스 오른쪽 단추로 프로젝트 클릭 및 Nuget 패키지 관리 선택](./media/app-insights-windows-services/03-nuget.png)

2. Application Insights Windows Server 패키지를 설치합니다. Microsoft.ApplicationInsights.WindowsServer

    !["Application Insights" 검색](./media/app-insights-windows-services/04-ai-nuget.png)

    *다른 패키지를 사용할 수 있나요?*

    예. API만 사용하여 사용자 고유의 원격 분석을 보내려는 경우 코어 API(Microsoft.ApplicationInsights)를 선택합니다. Windows Server 패키지에는 코어 API와 성능 카운터 수집 및 종속성 모니터링과 같은 다양한 다른 패키지가 자동으로 포함됩니다.


3. InstrumentationKey를 설정합니다.

    * 코어 API 패키지 Microsoft.ApplicationInsights를 설치한 경우 main()와 같은 코드에서 키를 설정해야 합니다. 

    `TelemetryConfiguration.Active.InstrumentationKey = "` *키* `";`

    다른 패키지 중 하나를 설치한 경우 코드를 사용하여 키를 설정하거나 ApplicationInsights.config에서 설정할 수 있습니다.
 
    `<InstrumentationKey>`*키*`</InstrumentationKey>`

    ApplicationInsights.config를 사용하는 경우 솔루션 탐색기에서 해당 속성이 **빌드 작업 = 콘텐츠, 출력 디렉터리로 복사 = 복사**로 설정되도록 합니다.

## <a name="telemetry"></a>원격 분석 호출 삽입

[Application Insights API][api] 중 하나를 사용하여 원격 분석을 보냅니다. 코어 API를 사용하는 경우 원격 분석이 자동으로 전송되지 않습니다. 일반적으로 다음을 사용할 수 있습니다.

* 양식, 페이지 또는 탭 전환에서 `TrackPageView(pageName)`
* 다른 사용자 작업에 대한 `TrackEvent(eventName)`
* 특정 이벤트에 연결되지 않은 메트릭의 정기적인 보고서를 보내기 위한 배경 작업에서 `TrackMetric(name, value)`입니다.
* [진단 로깅][diagnostic]을 위한 `TrackTrace(logEvent)`
* catch 절에서 `TrackException(exception)`
* 앱을 닫기 전에 모든 원격 분석이 전송되었는지 확인하려면 `Flush()`입니다. 코어 API(Microsoft.ApplicationInsights)를 사용하는 경우 이를 사용합니다. 웹 SDK는 이 동작을 자동으로 구현합니다. (인터넷을 항상 사용할 수 없는 컨텍스트에서 앱을 실행하는 경우 [지속성 채널](#persistence-channel)을 참조하세요.)


#### 원격 분석 이니셜라이저

사용자 및 세션 수를 보려면 각 `TelemetryClient`인스턴스에 대한 값을 설정할 수 있습니다. 또는 원격 분석 이니셜라이저를 사용하여 모든 클라이언트에 대해 이 덧셈을 수행할 수 있습니다.

```C#

    class UserSessionInitializer : ITelemetryInitializer
    {
        public void Initialize(ITelemetry telemetry)
        {
            telemetry.Context.User.Id = Environment.UserName;
            telemetry.Context.Session.Id = Guid.NewGuid().ToString();
        }
        
    }

    static class Program
    {
        ...
        static void Main()
        {
            TelemetryConfiguration.Active.TelemetryInitializers.Add(
                new UserSessionInitializer());
            ...

```



## <a name="run"></a>프로젝트 실행

[F5를 사용하여 응용 프로그램을 실행](http://msdn.microsoft.com/library/windows/apps/bg161304.aspx)하고 이를 사용하여 일부 원격 분석을 생성합니다.

Visual Studio에 전송한 이벤트 수가 표시됩니다.

![](./media/app-insights-windows-services/appinsights-09eventcount.png)

이벤트는 진단 및 출력 창에도 표시됩니다.

## <a name="monitor"></a>데이터 모니터링 보기

Azure 포털에서 사용자 응용 프로그램 블레이드로 돌아갑니다.

첫 이벤트는 [라이브 메트릭 스트림](app-insights-metrics-explorer.md#live-metrics-stream)에 나타납니다.


## 지속성 채널 

인터넷 연결을 항상 사용할 수 없거나 느린 곳에서 앱을 실행하는 경우 기본 메모리 내 채널 대신 지속성 채널을 사용하는 것이 좋습니다.

기본 메모리 내 채널에서 앱을 닫을 때까지 전송되지 않은 모든 원격 분석은 손실됩니다. `Flush()`를 사용하여 버퍼에 남아 있는 데이터를 보내려고 시도할 수 있지만 인터넷이 연결되어 있지 않거나 전송이 완료되기 전에 앱이 종료되면 데이터가 손실됩니다.

반면 지속성 채널은 파일을 포털에 보내기 전에 파일에서 원격 분석을 버퍼링합니다. `Flush()`은 데이터가 파일에 저장되도록 보장합니다. 앱을 닫을 때까지 모든 데이터가 전송되지 않은 경우 파일에 유지됩니다. 앱을 다시 시작하면 인터넷이 연결된 경우 데이터가 전송됩니다. 연결을 사용할 수 있을 때까지 필요한 만큼 데이터 파일이 누적됩니다.

### 지속성 채널을 사용하려면

1. NuGet 패키지 [Microsoft.ApplicationInsights.PersistenceChannel](https://www.nuget.org/packages/Microsoft.ApplicationInsights.PersistenceChannel/1.2.3)을 가져옵니다.
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

    Flush()는 지속성 채널에서 동기 상태이지만 타 채널에서는 비동기 상태입니다.

 
지속성 채널은 응용 프로그램에서 생성된 이벤트 수가 상대적으로 작고 연결이 자주 신뢰할 수 없는 장치 시나리오에 최적화됩니다. 이 채널은 신뢰할 수 있는 디스크의 저장소에 먼저 이벤트를 쓰고 전송하려 합니다.

#### 예

처리되지 않은 예외를 모니터링하려는 경우를 가정해 보겠습니다. `UnhandledException` 이벤트를 구독합니다. 콜백에서 플러시에 호출을 포함하여 원격 분석이 유지되도록 합니다.
 
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


지속성 채널의 코드는 [github](https://github.com/Microsoft/ApplicationInsights-dotnet/tree/v1.2.3/src/TelemetryChannels/PersistenceChannel)에 있습니다.


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
 

<!---HONumber=AcomDC_0615_2016-->