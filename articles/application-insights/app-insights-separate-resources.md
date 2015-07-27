<properties 
	pageTitle="새 Application Insights 리소스 만들기" 
	description="새 응용 프로그램에 대해 설정하고 새 계측 키를 가져옵니다. Application Insights는 라이브 응용 프로그램의 성능 및 사용을 모니터링합니다." 
	services="application-insights" 
    documentationCenter=""
	authors="alancameronwills" 
	manager="douge"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/14/2015" 
	ms.author="awills"/>

# 새 Application Insights 리소스 만들기


디버깅, 테스트, 응용 프로그램의 프로덕션 버전에서 원격 분석 혼합을 피하려면, 별도의 [Application Insights][start] 리소스를 생성하여 각 버전의 데이터를 수신합니다.

응용 프로그램에서 받은 데이터는 Microsoft Azure *리소스*의 Application Insights에 의해 저장되고 처리됩니다. 각 리소스는 해당 *계측 키*로 식별됩니다. 응용 프로그램에서 올바른 리소스를 수집하는 데이터를 보낼 수 있도록 키 Application Insights SDK를 제공합니다. 코드 또는 ApplicationInsights.config에서 키를 제공 받을 수 있습니다. SDK에 있는 키를 변경하여 데이터를 다른 리소스로 디렉트할 수 있습니다.


## Application Insights 리소스 만들기
  

[portal.azure.com](https://portal.azure.com)에서 Application Insights 리소스를 추가합니다.

![새로 만들기, Application Insights 클릭](./media/app-insights-create-new-resource/01-new.png)


* **응용 프로그램 유형**은 개요 블레이드에 표시되는 내용 및 [메트릭 탐색기][metrics]에서 사용할 수 있는 속성에 영향을 줍니다. 앱 유형이 표시되지 않으면 웹 페이지에 대해 웹 유형 중 하나를 선택하고 다른 장치에 대해서는 전화 유형 중 하나를 선택합니다.
* **리소스 그룹**은 [액세스 제어](app-insights-resources-roles-access-control.md)와 같은 속성을 관리하기 위한 편의 기능입니다. 개발, 테스트 및 프로덕션 환경에 대 한 별도 리소스 그룹을 사용할 수 있습니다.
* **구독**은 Azure의 지불 계정입니다.
* **위치**는 데이터를 보관하는 곳입니다. 현재는 변경할 수 없습니다.
* **시작 보드에 추가**는 Azure 홈 페이지에 리소스에 대한 빠른 액세스 타일을 넣습니다. 

리소스 생성 시 몇 초 정도 걸립니다. 완료되면 알림이 표시 됩니다.

(리소스를 자동으로 만드는 [PowerShell 스크립트](app-insights-powershell-script-create-resource.md)를 작성할 수 있습니다).


## 계측 키 복사

계측 키는 사용자가 만든 리소스를 식별합니다.

![Essentials과 계측 키를 차례로 클릭하고, CTRL + C 누릅니다.](./media/app-insights-create-new-resource/02-props.png)

사용자의 앱이 데이터를 보낼 모든 리소스의 계측 키가 필요합니다.


## <a name="dynamic-ikey"></a> 동적 계측 키

일반적으로 SDK는 ApplicationInsights.config로부터 iKey를 가져옵니다. 대신, 코드에서 이것을 설정하여 보다 쉽게 변경할 수 있습니다.

ASP.NET 서비스의 global.aspx.cs 같은 초기화 메서드에서 키를 설정합니다.

*C#*

    protected void Application_Start()
    {
      Microsoft.ApplicationInsights.Extensibility.
        TelemetryConfiguration.Active.InstrumentationKey = 
          // - for example -
          WebConfigurationManager.AppSettings["ikey"];
      ...

이 예제에서는 서로 다른 리소스에 대한 ikeys는 다른 버전의 웹 구성 파일에 배치됩니다. 웹 구성 파일 교체는 대상 리소스를 교체합니다.

#### 웹 페이지

iKey는 [빠른 시작 블레이드에서 가져온 스크립트](app-insights-javascript.md) 내의 응용 프로그램 웹페이지에서도 사용 가능합니다. 스크립트에 문자 그대로 코딩하는 대신, 서버 상태로부터 생성합니다. 예를 들어, ASP.NET 응용 프로그램에서:

*Razor에서 JavaScript*

    <script type="text/javascript">
    // Standard Application Insights web page script:
    var appInsights = window.appInsights || function(config){ ...
    // Modify this part:
    }({instrumentationKey:  
      // Generate from server property:
      @Microsoft.ApplicationInsights.Extensibility.
         TelemetryConfiguration.Active.InstrumentationKey"
    }) // ...





<!--Link references-->

[api]: app-insights-api-custom-events-metrics.md
[diagnostic]: app-insights-diagnostic-search.md
[metrics]: app-insights-metrics-explorer.md
[start]: app-insights-get-started.md

 

<!---HONumber=July15_HO3-->