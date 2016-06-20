<properties
	pageTitle="Application Insights 포털 사용"
	description="Application Insights 포털의 메트릭, 검색, 대시보드 및 설정"
	services="application-insights"
    documentationCenter=""
	authors="alancameronwills"
	manager="douge"/>

<tags
	ms.service="application-insights"
	ms.workload="tbd"
	ms.tgt_pltfrm="ibiza"
	ms.devlang="multiple"
	ms.topic="article" 
	ms.date="06/03/2016"
	ms.author="awills"/>

# Application Insights 포털에서 탐색 및 대시보드

[프로젝트에서 Application Insights를 설정](app-insights-overview.md)하면 앱의 성능 및 사용에 대한 원격 분석 데이터가 [Azure 포털](https://portal.azure.com)에서 프로젝트의 Application Insights 리소스에 나타납니다.

## 원격 분석 찾기

[Azure 포털](https://portal.azure.com)에 로그인하고 앱에 대해 만든 Application Insights 리소스를 찾습니다.

![찾아보기를 클릭하고 Application Insights를 선택한 후 앱을 선택합니다.](./media/app-insights-dashboards/00-start.png)

개요 페이지는 기본적인 원격 분석을 제공하고 더 많은 링크를 더합니다. 내용은 앱의 유형에 따라 달라지며 사용자가 지정할 수 있습니다.


## 앱 개요 블레이드

앱의 개요 블레이드(페이지)는 앱의 주요 진단 메트릭 요약을 보여 주며 포털의 다른 기능에 대한 게이트웨이입니다.

다음을 클릭합니다.

* **임의 차트 또는 타일**: 차트에 표시되는 데이터에 대한 자세한 정보를 확인할 수 있습니다.
* **설정**: 구성 페이지 뿐만 아니라 다른 메트릭의 미리 정의된 블레이드로 이동할 수 있습니다.
* [**메트릭 탐색기**](app-insights-metrics-explorer.md): 사용자 고유의 메트릭 페이지를 만들 수 있습니다.
* [**검색**](app-insights-diagnostic-search.md): 요청, 예외 또는 로그 추적과 같은 이벤트의 특정 인스턴스를 조사할 수 있습니다.
* [**분석**](app-insights-analytics.md): 원격 분석을 통해 강력한 쿼리를 합니다.
* [**라이브 스트림**](app-insights-metrics-explorer.md#live-stream): 거의 즉각적인 고정된 메트릭 집합을 제공하며, 새 빌드를 배포 또는 디버깅할 때 유용합니다.


![원격 분석을 보기 위한 주요 경로](./media/app-insights-dashboards/010-oview.png)


### 개요 블레이드 사용자 지정 

개요에 표시하려는 내용을 선택합니다. 사용자 지정에서 섹션 제목을 삽입하고 타일 및 차트를 끌며 항목을 제거하고 갤러리에서 새 타일 및 차트를 추가합니다.

![편집을 클릭합니다. 타일 및 차트를 끕니다. 갤러리에서 타일을 추가합니다. 완료를 클릭합니다.](./media/app-insights-dashboards/020-customize.png)

## 대시보드

[Microsoft Azure 포털](https://portal.azure.com)에 로그인한 후 가장 먼저 표시되는 것이 대시보드입니다. [Visual Studio Application Insights](app-insights-overview.md)의 원격 분석을 포함하여, 모든 Azure 리소스에서 가장 중요한 차트를 이곳에 한데 모을 수 있습니다.
 

![사용자 지정 대시보드입니다.](./media/app-insights-dashboards/30.png)

1. 언제든지 위쪽 모서리를 클릭하면 대시보드로 돌아갑니다.
2. 데이터에 대한 자세한 정보를 보려면 대시보드에서 차트 또는 타일을 클릭합니다.
3. 모든 리소스에 대한 완전한 보기는 탐색 모음을 사용합니다.
4. 대시보드 도구 모음을 사용하여 대시보드를 편집하고 만들고 공유합니다.

## 대시보드 추가

특히 흥미로운 블레이드나 차트를 보는 경우 그 사본을 대시보드에 고정할 수 있습니다. 이 경우 다음에 대시보드로 돌아가면 해당 블레이드나 차트가 표시됩니다.

![차트를 고정하려면 차트 위에 마우스를 놓고 헤더에서 "..."를 클릭합니다.](./media/app-insights-dashboards/33.png)

차트는 타일로 그룹화되고 타일은 둘 이상의 차트를 포함할 수 있습니다. 타일 전체를 대시보드에 고정합니다.

## 대시보드에서 타일을 조정합니다.

타일이 대시보드에 있으면, 조정할 수 있습니다.

![차트를 편집하려면 차트를 마우스로 가리킵니다.](./media/app-insights-dashboards/36.png)

1. 타일에 차트 추가 
2. 메트릭, group-by 차원 및 차트의 스타일(테이블, 그래프)를 설정합니다.
3. 타일의 차트에 대한 timespan과 필터 속성을 설정합니다.
4. 타일 제목을 설정합니다.

메트릭 탐색기 블레이드에서 고정된 타일에는 개요 블레이드에서 고정된 타일보다 편집 옵션이 많이 있습니다.

고정해 놓은 원래 타일은 편집의 영향을 받지 않습니다.


## 대시보드 사이 전환

둘 이상의 대시보드를 저장하고 해당 대시보드 간에 전환할 수 있습니다. 차트나 블레이드를 고정하면 현재 대시보드에 추가됩니다.

![대시보드 간에 전환하려면 대시보드를 클릭하고 저장된 대시보드를 선택합니다. 새 대시보드를 만들고 저장하려면 새로 만들기를 클릭합니다. 다시 정렬하려면 편집을 클릭합니다.](./media/app-insights-dashboards/32.png)

예를 들어 단체방에서 전체 화면을 표시하는 대시보드와 일반 개발용 대시보드를 유지할 수 있습니다.


대시보드에서 블레이드는 타일로 표시됩니다. 이를 클릭하면 블레이드로 이동합니다. 차트는 원래 위치에 있는 차트를 복제합니다.

![타일이 나타내는 블레이드를 열려면 타일을 클릭합니다.](./media/app-insights-dashboards/35.png)


## 팀과 대시보드 공유

대시보드를 만들면 다른 사용자와 공유할 수 있습니다.


![대시보드 헤더에서 공유를 클릭합니다.](./media/app-insights-dashboards/41.png)

[역할 및 액세스 제어](app-insights-resources-roles-access-control.md)에 대해 알아봅니다.

<!---HONumber=AcomDC_0608_2016-->