---
title: Application Insights 포털 사용
description: Application Insights 포털의 메트릭, 검색, 대시보드 및 설정
services: application-insights
documentationcenter: ''
author: alancameronwills
manager: douge

ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: multiple
ms.topic: article
ms.date: 07/30/2016
ms.author: awills

---
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

![사용자 지정 대시보드입니다.](./media/app-insights-dashboards/31.png)

1. 대시보드 도구 모음을 사용하여 대시보드를 편집하고 만들고 공유합니다.
2. 현재 대시보드도 돌아가거나 최근의 다른 뷰로 전환합니다.
3. 대시보드를 전환합니다.
4. 대시보드를 만들고 편집 및 공유합니다.
5. 타일을 포인터로 가리킨 다음 그 위쪽 막대를 사용하여 타일을 이동, 사용자 지정, 또는 제거합니다.

## 대시보드 추가
특히 흥미로운 블레이드나 차트를 보는 경우 그 사본을 대시보드에 고정할 수 있습니다. 이 경우 다음에 대시보드로 돌아가면 해당 블레이드나 차트가 표시됩니다.

![차트를 고정하려면 차트 위에 마우스를 놓고 헤더에서 "..."를 클릭합니다.](./media/app-insights-dashboards/33.png)

1. 대시보드에 차트를 고정합니다. 차트의 복사본이 대시보드에 나타납니다.
2. 전체 블레이드를 대시보드에 고정합니다 - 대시보드에서 클릭할 수 있는 타일로 나타납니다.
3. 현재 대시보드 돌아가려면 왼쪽 위의 모퉁이를 클릭합니다. 그런 다음 현재 보기로 돌아가려면 드롭다운 메뉴를 사용할 수 있습니다.

차트는 타일로 그룹화되고 타일은 둘 이상의 차트를 포함할 수 있습니다. 타일 전체를 대시보드에 고정합니다.

### 분석 차트
[분석 차트](app-insights-analytics-using.md#pin-to-dashboard)를 [공유](#share-dashboards-with-your-team) 대시보드에 고정할 수도 있습니다. 그러면 임의 쿼리의 차트를 표준 메트릭과 함께 추가할 수 있습니다.

## 대시보드에서 타일을 조정합니다.
타일이 대시보드에 있으면, 조정할 수 있습니다.

![차트를 편집하려면 차트를 마우스로 가리킵니다.](./media/app-insights-dashboards/36.png)

1. 타일에 차트 추가
2. 메트릭, group-by 차원 및 차트의 스타일(테이블, 그래프)를 설정합니다.
3. 타일의 차트에 대한 timespan과 필터 속성을 설정합니다. 차트의 시간 범위를 설정하거나 대시보드의 시간 범위에서 상속할 수 있습니다.
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

[역할 및 액세스 제어](app-insights-resources-roles-access-control.md)에 대해 알아보세요.

## 설정
개요 블레이드의 설정 단추는 구성뿐만 아니라 여러 가지 유형의 차트를 확인할 수 있습니다.

* **감사 로그** - 앱 리소스에서 수행된 경고와 작업을 검토합니다.
* [새 지원 요청](app-insights-get-dev-support.md) - Azure 도움말 옵션입니다. Application Insights에서는 창 오른쪽 위에 있는 웃는 단추를 사용하여 도움말을 가져올 수 있습니다.
* [응용 프로그램 맵](app-insights-dependencies.md#application-map) - 종속성 정보에서 파생된, 응용 프로그램의 구성 요소를 표시하는 활성 맵입니다.
* [라이브 메트릭 스트림](app-insights-metrics-explorer.md#live-metrics-stream) -새로운 릴리스를 모니터링하기 위해, 약 1초마다 제공되는 주요 메트릭입니다.
* **오류** - 오류가 발생한 요청, 종속성 호출, 예외입니다. [편집 가능한 메트릭 블레이드](app-insights-metrics-explorer.md)입니다.
* **성능** - 응답 시간, 종속성 응답 시간입니다. [편집 가능한 메트릭 블레이드](app-insights-metrics-explorer.md)입니다.
* [서버](app-insights-web-monitor-performance.md) - 성능 카운터입니다. [상태 모니터를 설치하면](app-insights-monitor-performance-live-website-now.md) 사용할 수 있습니다.
* **브라우저** - 페이지 뷰 및 AJAX 성능입니다. [웹 페이지를 계측할 때](app-insights-javascript.md) 사용할 수 있습니다.
* **사용** - 페이지 조회수, 사용자 및 세션 수입니다. [웹 페이지를 계측할 때](app-insights-javascript.md) 사용할 수 있습니다.
* **시작하기** - 인라인 자습서입니다.
* **속성** - 계측 키, 구독 및 리소스 ID입니다.
* [가용성](app-insights-monitor-web-app-availability.md) -웹 테스트 구성 및 결과입니다.
* [경고](app-insights-alerts.md) -메트릭 경고 구성입니다.
* [연속 내보내기](app-insights-export-telemetry.md) - Azure Storage에 대한 원격 분석 내보내기를 구성합니다.
* [성능 테스트](app-insights-monitor-web-app-availability.md#performance-tests) -웹 사이트에 대한 종합 부하를 설정 합니다.
* [할당량 및 가격 책정](app-insights-pricing.md)과 [수집 샘플링](app-insights-sampling.md)입니다.
* **API 액세스** - 현재 [릴리스 주석](app-insights-annotations.md)을 작성하는 작업과 데이터 액세스 API에 사용됩니다.
* [**작업 항목**](app-insights-diagnostic-search.md#create-work-item) - 작업 추적 시스템과 연결하여 원격 분석을 검사하는 동안 버그를 만들 수 있습니다.
* [**사용자**](app-insights-resources-roles-access-control.md) - 앱 리소스에 액세스할 권한이 있는 사용자를 관리합니다.
* [**역할**](app-insights-resources-roles-access-control.md) - 사용자가 수행할 수 있는 작업을 관리합니다.
* [**태그**](../resource-group-using-tags.md) -Azure 리소스를 구성합니다.
* [**잠금**](../resource-group-lock-resources.md) -Azure 리소스를 잠급니다.
* [**템플릿 내보내기**](app-insights-powershell.md) - Azure 리소스의 정의를 내보내서 새로운 리소스의 템플릿으로 사용합니다.

## 다음 작업
|  |  |
| --- | --- |
| [메트릭 탐색기](app-insights-metrics-explorer.md)<br/>필터 및 세그먼트 메트릭 |![검색 예제](./media/app-insights-dashboards/64.png) |
| [진단 검색](app-insights-diagnostic-search.md)<br/>이벤트 찾기 및 검사, 관련 이벤트, 버그 만들기 |![검색 예제](./media/app-insights-dashboards/61.png) |
| [분석](app-insights-analytics.md)<br/>강력한 쿼리 언어 |![검색 예제](./media/app-insights-dashboards/63.png) |

<!---HONumber=AcomDC_0817_2016-->