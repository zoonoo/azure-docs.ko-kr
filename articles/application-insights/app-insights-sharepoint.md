<properties 
	pageTitle="Application Insights를 사용하여 SharePoint 사이트 모니터링" 
	description="새 계측 키를 사용하여 새 응용 프로그램 모니터링 시작" 
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
	ms.date="07/13/2015" 
	ms.author="awills"/>

# Application Insights를 사용하여 SharePoint 사이트 모니터링


[AZURE.INCLUDE [app-insights-selector-get-started](../../includes/app-insights-selector-get-started.md)]

Visual Studio Application Insights는 응용 프로그램의 가용성, 성능 및 사용량을 모니터링합니다. 여기에서는 SharePoint 사이트에 맞게 설정하는 방법을 알아봅니다.


## Application Insights 리소스 만들기


[Azure 포털](http://portal.azure.com)에서 새 Application Insights 리소스를 만듭니다. 응용 프로그램 유형으로 ASP.NET을 선택합니다.

![속성 클릭, 키 선택 및 ctrl+C 누르기](./media/app-insights-sharepoint/01-new.png)


열리는 블레이드에서 앱의 성능 및 사용 데이터를 볼 수 있습니다. 다음에 Azure에 로그인할 때 다시 이 블레이드로 돌아가려면 시작 화면에서 해당 타일을 찾아야 합니다. 또는 찾아보기를 클릭하여 찾아야 합니다.
    


## 웹 페이지에 스크립트 추가

빠른 시작에서 웹 페이지용 스크립트를 가져옵니다.

![](./media/app-insights-sharepoint/02-monitor-web-page.png)

추적하려는 모든 페이지의 &lt;/head&gt; 태그 바로 앞에 스크립트를 삽입합니다. 웹 사이트에 마스터 페이지가 있는 경우 이 페이지에 스크립트를 넣을 수 있습니다. 예를 들어 ASP.NET MVC 프로젝트에서는 View\\Shared\_Layout.cshtml에 추가합니다.

스크립트에는 Application Insights 리소스에 원격 분석을 전달하는 계측 키가 포함됩니다.

### 사이트 페이지에 코드를 추가합니다.

#### 마스터 페이지에서

사이트의 마스터 페이지를 편집할 수 있는 경우 사이트의 모든 페이지에 대한 모니터링을 제공합니다.

마스터 페이지를 체크 아웃하고 SharePoint Designer 또는 다른 편집기를 사용하여 편집합니다.

![](./media/app-insights-sharepoint/03-master.png)


</head> 태그 바로 앞에 코드를 추가합니다.


![](./media/app-insights-sharepoint/04-code.png)

#### 또는 개별 페이지에서

제한된 페이지 집합을 모니터링하려면 각 페이지에 개별적으로 스크립트를 추가합니다.

웹 파트를 삽입하고 코드 조각을 포함합니다.


![](./media/app-insights-sharepoint/05-page.png)


## 앱에 대한 데이터 보기

응용 프로그램을 다시 배포 합니다.

[Azure 포털](http://portal.azure.com)에서 사용자 응용 프로그램 블레이드로 돌아갑니다.

첫 번째 이벤트가 진단 검색에 표시됩니다.

![](./media/app-insights-sharepoint/09-search.png)

더 많은 데이터를 기대하는 경우 몇 초 후에 새로고침을 클릭합니다.

**사용 현황 분석**은 사용자, 세션 및 페이지 보기의 차트를 연결합니다.

![](./media/app-insights-sharepoint/06-usage.png)

예를 들어, 페이지 보기를 클릭하여 자세한 내용을 봅니다.

![](./media/app-insights-sharepoint/07-pages.png)

사용자를 클릭하면서 새 사용자와 해당 위치에 대한 자세한 정보를 확인합니다.


![](./media/app-insights-sharepoint/08-users.png)



## 다음 단계

* 사이트 가용성을 모니터링하는 [웹 테스트](app-insights-monitor-web-app-availability.md)

* 다른 유형의 응용 프로그램에 대한 [Application Insights](app-insights-overview.md)



<!--Link references-->

<!---HONumber=Oct15_HO3-->