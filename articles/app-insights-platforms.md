<properties 
	pageTitle="Application Insights: 플랫폼" 
	description="Application Insights와 같이 사용할 수 있습니까...?" 
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
	ms.date="04/20/2015" 
	ms.author="awills"/>
 
#Application Insights: 플랫폼

[AZURE.INCLUDE [app-insights-selector-get-started](../includes/app-insights-selector-get-started.md)]

####Application Insights와 같이 사용할 수 있나요...?


##언어

+ [C#, VB](app-insights-start-monitoring-app-health-usage.md)
+ [JavaScript 웹 페이지](app-insights-web-track-usage.md)
+ [Windows 스토어 JavaScript 앱](#cordova)
+ [Java](app-insights-java.md)
+ [Ruby](https://rubygems.org/gems/application_insights) 
+ [PHP](https://github.com/Microsoft/AppInsights-PHP)
+ [Python](https://pypi.python.org/pypi/applicationinsights/0.1.0)

##플랫폼

+ [ASP.NET](app-insights-start-monitoring-app-health-usage.md)
+ [Azure 웹 앱 및 VM](insights-perf-analytics.md)
+ [Android](https://github.com/Microsoft/AppInsights-Android)
+ [iOS](https://github.com/Microsoft/AppInsights-iOS)
+ [Cordova](#cordova)
+ [Angular](https://www.npmjs.com/package/angular-applicationinsights)
+ [Node.JS](https://www.npmjs.com/package/applicationinsights)
+ [Joomla](https://github.com/fidmor89/AppInsights-Joomla)
+ [SharePoint](app-insights-sharepoint.md)
+ [WordPress](https://wordpress.org/plugins/application-insights/)
+ [Windows 데스크톱](app-insights-windows-desktop.md)


##로깅 프레임워크

+	[Log4Net, NLog 또는 System.Diagnostics.Trace](app-insights-diagnostic-search.md)
+	[Java, Log4J 또는 Logback](app-insights-java-trace-logs.md)


##프로젝트

또한 [GitHub에서 Application Insights SDK 프로젝트](https://github.com/Microsoft/AppInsights-Home)를 방문해보세요.


###<a name="cordova"></a>Cordova 및 Windows 스토어 JavaScript 앱

Visual Studio에서 프로젝트를 마우스 오른쪽 단추로 클릭하고 **NuGet 패키지 관리**를 선택합니다.

**온라인** 및 Application Insights에서 검색합니다.

**JavaScript 응용 프로그램용 Application Insights API**를 설치합니다.

한 가지만 변경하여 표준 클라이언트 쪽 [웹 앱 스크립트](app-insights-web-track-usage.md)를 사용합니다.

Application Insights 포털에서 스크립트를 가져올 때 계측 키 다음에 다음과 같은 줄을 삽입합니다.

    ...{
        instrumentationKey:"00000000-662d-4479-0000-40c89770e67c",
        endpointUrl:"https://dc.services.visualstudio.com/v2/track"
    } ...

[Cordova](http://cordova.apache.org/)

[JavaScript를 사용하는 Windows 스토어 앱](https://msdn.microsoft.com/library/windows/apps/br211385.aspx)

<!--Link references-->



<!--HONumber=54-->