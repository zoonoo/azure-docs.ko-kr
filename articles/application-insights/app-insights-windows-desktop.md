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
	ms.date="08/19/2015" 
	ms.author="awills"/>

# Windows 데스크톱 앱, 서비스 및 작업자 역할의 Application Insights

*Application Insights는 미리 보기 상태입니다.*

[AZURE.INCLUDE [app-insights-selector-get-started](../../includes/app-insights-selector-get-started.md)]

Application Insights를 사용하면 사용량 및 성능을 위해 배포된 응용 프로그램을 모니터링할 수 있습니다.

데스크톱 앱, 백그라운드 서비스 및 작업자 역할을 포함하는 모든 Windows 응용 프로그램은 Application Insights 코어 SDK를 사용하여 원격 분석을 Application Insights에 보낼 수 있습니다. 코어 SDK는 웹 또는 장치 SDK와 달리 API만을 제공합니다. 데이터를 자동으로 수집하는 모듈이 포함되지 않으므로 코드를 작성하여 사용자 고유의 원격 분석을 보내야 합니다.


## <a name="add"></a> Application Insights 리소스 만들기


1.  [Azure 포털][portal]에서 새 Application Insights 리소스를 만듭니다. 응용 프로그램 종류에 대해 Windows 스토어 앱을 선택합니다. 

    ![새로 만들기, Application Insights 클릭](./media/app-insights-windows-desktop/01-new.png)

    (응용 프로그램 종류 선택은 개요 블레이드의 내용 및 [메트릭 탐색기][metrics]에서 사용할 수 있는 속성을 설정합니다.)

2.  계측 키를 복사합니다.

    ![속성 클릭, 키 선택 및 ctrl+C 누르기](./media/app-insights-windows-desktop/02-props.png)

## <a name="sdk"></a>응용 프로그램에 SDK를 설치합니다.


1. Visual Studio에서 데스크톱 앱 프로젝트의 NuGet 패키지를 편집합니다.

    ![마우스 오른쪽 단추로 프로젝트 클릭 및 Nuget 패키지 관리 선택](./media/app-insights-windows-desktop/03-nuget.png)

2. Application Insights 코어 API 패키지를 설치합니다.

    !["Application Insights" 검색](./media/app-insights-windows-desktop/04-core-nuget.png)

    해당 기능을 사용하려는 경우 성능 카운터 및 로그 캡처 패키지와 같은 다른 패키지를 설치할 수 있습니다.

3. 코드에 InstrumentationKey를 설정합니다(예: main()).

    `TelemetryConfiguration.Active.InstrumentationKey = "your key";`

*ApplicationInsights.config가 없는 이유는?*

* .config 파일은 원격 분석 수집기 구성에 사용되는 코어 API 패키지에 의해 설치되지 않습니다. 따라서 계측 키를 설정하고 원격 분석을 전송하는 사용자 고유의 코드를 작성합니다.
* 다른 패키지 중 하나를 설치한 경우 .config 파일이 있습니다. 코드에서 설정하는 대신 계측 키를 삽입할 수 있습니다.

*다른 NuGet 패키지를 사용할 수 있습니까?*

* 예, 성능 카운터와 같은 다양한 컬렉션 모듈에 수집기를 설치하는 웹 서버 패키지 (Microsoft.ApplicationInsights.Web)를 사용할 수 있습니다. 그러면 계측 키를 넣는 .config 파일이 설치됩니다. HTTP 요청 수집기와 같이 [원하지 않는 모듈을 사용하지 않도록 설정하는 ApplicationInsights.config](app-insights-configuration-with-applicationinsights-config.md)를 사용합니다. 
* [로그 또는 추적 수집기 패키지](app-insights-asp-net-trace-logs.md)를 사용하려는 경우 웹 서버 패키지를 시작합니다. 

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
            }
            base.OnClosing(e);
        }

```

[Application Insights API][api] 중 하나를 사용하여 원격 분석을 보냅니다. Windows 데스크톱 응용 프로그램에서는 원격 분석이 자동으로 전송되지 않습니다. 일반적으로 다음을 사용할 수 있습니다.

* 양식, 페이지 또는 탭 전환 시 TrackPageView(pageName)
* 다른 사용자 동작에 대해서는 TrackEvent(eventName)
* 특정 이벤트에 연결되지 않은 메트릭의 정기적인 보고서를 보내기 위한 배경 작업에서는 TrackMetric(name, value)
* [진단 로깅][diagnostic]에 대해서는 TrackTrace(logEvent)
* Catch 절에서는 TrackException(exception)


앱을 닫기 전에 모든 원격 분석이 전송되었는지 확인하려면 `TelemetryClient.Flush()`을 사용합니다. 일반적으로 원격 분석은 일괄 처리되고 정기적으로 전송됩니다. (플러시는 코어 API를 사용하는 경우에만 사용하는 것이 좋습니다. 웹 및 장치 SDK는 이 동작을 자동으로 구현합니다.)


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

첫 번째 이벤트가 진단 검색에 표시됩니다.

더 많은 데이터를 기대하는 경우 몇 초 후에 새로고침을 클릭합니다.

TrackMetric 또는 TrackEvent의 측정 매개 변수를 사용한 경우 [메트릭을 탐색기][metrics]를 열고 메트릭스가 보이는 필터 블레이드를 엽니다.



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
 

<!---HONumber=August15_HO8-->