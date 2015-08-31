<properties
	pageTitle="Application Insights 포털 사용"
	description="Application Insights를 사용한 사용량 분석 개요"
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
	ms.date="08/17/2015"
	ms.author="awills"/>

# Application Insights 포털 사용

[프로젝트에서 Application Insights를 설정](app-insights-get-started.md)하면 앱의 성능 및 사용에 대한 원격 분석 데이터가 [Azure 포털](https://portal.azure.com)에서 프로젝트의 Application Insights 리소스에 나타납니다.

## Azure에서 원격 분석 찾기

[Azure 포털](https://portal.azure.com)에 로그인하고 앱에 대해 만든 Application Insights 리소스를 찾습니다.

![찾아보기를 클릭하고 Application Insights를 선택한 후 앱을 선택합니다.](./media/app-insights-portal/00-start.png)

개요 페이지는 기본적인 원격 분석을 제공하고 더 많은 링크를 더합니다. 내용은 앱의 유형에 따라 달라지며 사용자가 지정할 수 있습니다.

## 앱 개요 블레이드

앱에 대한 개요 블레이드(페이지)는 성능 및 사용을 모니터링하기 위한 가장 중요한 차트를 보여줍니다. 내용은 앱의 유형에 따라 달라지며 어떤 경우에도 사용자가 지정할 수 있습니다.


### 개요 블레이드 사용자 지정 

개요에 표시하려는 내용을 선택합니다. 사용자 지정에서 섹션 제목을 삽입하고 타일 및 차트를 끌며 항목을 제거하고 갤러리에서 새 타일 및 차트를 추가합니다.

![사용자가 지정하려면 "..."을 클릭합니다. 타일 및 차트를 끕니다. 갤러리에서 타일을 추가합니다.](./media/app-insights-portal/020-customize.png)


## 사용자 고유의 메트릭 차트 및 그리드 만들기

### 차트 및 표 편집

블레이드에 새 차트를 추가하려면:

![메트릭 탐색기에서 추가 차트 선택](./media/app-insights-metrics-explorer/04-add.png)

기존 또는 새 차트를 선택하여 보이는 내용 편집:

![하나 이상의 메트릭 선택](./media/app-insights-metrics-explorer/08-select.png)

함께 표시할 수 있는 조합에 관한 제한이 있지만 차트에 하나 이상의 메트릭을 표시할 수 있습니다. 한 메트릭을 선택하면 일부 다른 메트릭을 사용할 수 없습니다.

[사용자 지정 메트릭](app-insights-api-custom-events-metrics.md#track-metric)을 앱으로 코딩하는 경우(TrackMetric 및 TrackEvent 호출) 여기에 나열됩니다.

### 데이터 분할

차트 또는 표를 선택하고 그룹으로 전환하여 그룹별로 속성을 선택합니다.

![그룹화를 선택한 다음 그룹별에서 속성 선택](./media/app-insights-metrics-explorer/15-segment.png)

사용자 지정 메트릭을 앱으로 코딩하고 [속성 값](app-insights-api-custom-events-metrics.md#properties)을 포함하는 경우 목록에서 속성을 선택할 수 있게 됩니다.

데이터를 분할하기에 차트가 너무 작나요? 높이 조정:

![슬라이더 조정](./media/app-insights-metrics-explorer/18-height.png)

### 데이터 필터링

속성 값의 선택한 집합에 대한 메트릭 보기:

![필터를 클릭하고 속성을 확장하여 값 선택](./media/app-insights-metrics-explorer/19-filter.png)

특정 속성에 대한 값을 선택하지 않은 경우 모두 선택한 것과 동일합니다. 즉, 해당 속성에는 필터가 없습니다.

각 속성 값과 함께 이벤트 수를 확인합니다. 한 속성 값을 선택하면 다른 속성 값과 함께 수가 조정됩니다.

### 매트릭 블레이드 저장

차트를 만든 경우 즐겨찾기로 저장합니다. 조직 계정을 사용하는 경우 다른 팀 구성원과 이를 공유할지 선택할 수 있습니다.

![즐겨찾기 선택](./media/app-insights-metrics-explorer/21-favorite-save.png)

블레이드를 다시 보려면 **개요 블레이드로 이동**하여 즐겨찾기를 엽니다.

![개요 블레이드에서 즐겨찾기 선택](./media/app-insights-metrics-explorer/22-favorite-get.png)

저장했을 때 상대 시간을 선택한 경우 해당 블레이드가 최신 메트릭으로 업데이트됩니다. 절대 시간 범위를 선택한 경우 매번 동일한 데이터가 표시됩니다.

### 블레이드 다시 설정

블레이드를 편집하지만 저장된 원본 세트로 되돌아가려는 경우 재설정을 클릭하면 됩니다.

![메트릭 탐색기 위쪽에 있는 단추](./media/app-insights-metrics-explorer/17-reset.png)

## 검색 페이지 만들기

진단 검색을 엽니다.

![진단 검색 열기](./media/app-insights-diagnostic-search/01-open-Diagnostic.png)

필터 블레이드를 열고 확인하려는 이벤트 유형을 선택합니다. (나중에 열었던 블레이드로 필터를 복원하려는 경우 재설정을 클릭합니다.)

![필터 선택 및 원격 분석 유형 선택](./media/app-insights-diagnostic-search/02-filter-req.png)

### 속성 값에서 필터링

해당 속성 값에서 이벤트를 필터링할 수 있습니다. 사용 가능한 속성은 선택한 이벤트 유형에 따라 다릅니다.

예를들어 특정 응답 코드에 대한 요청을 선택합니다.

![속성 확장 및 값 선택](./media/app-insights-diagnostic-search/03-response500.png)

특정 속성 값을 선택하지 않는 것은 모든 값을 선택하는 것과 동일한 효과가 있습니다. 해당 속성에서 필터링을 전한합니다.


### 검색 범위 좁히기

필터 값의 오른쪽에 있는 수는 현재 필터링된 집합에서 발생한 수를 보여줍니다.

이 예제에서는 `Reports/Employees` 요청이 500개의 오류 중 대부분을 초래함이 명확합니다.

![속성 확장 및 값 선택](./media/app-insights-diagnostic-search/04-failingReq.png)

또한 이 시간 동안 발생하고 있는 기타 이벤트가 무엇인지도 보려는 경우 **정의되지 않은 속성의 이벤트 포함**을 선택할 수 있습니다.

### 검색 저장

원하는 모든 필터를 설치할 때 즐겨찾기로 검색을 저장할 수 있습니다. 조직 계정으로 작업하는 경우 다른 팀 구성원과 공유할 것인지를 선택할 수 있습니다.

![즐겨찾기 클릭, 이름 설정 및 저장 클릭](./media/app-insights-diagnostic-search/08-favorite-save.png)


검색을 다시 보려면 **개요 블레이드로 이동**하여 즐겨찾기를 엽니다.

![즐겨찾기 타일](./media/app-insights-diagnostic-search/09-favorite-get.png)

상대 시간 범위로 저장한 경우 다시 열린 블레이드는 최신 데이터입니다. 절대 시간 범위로 저장한 경우 매번 같은 데이터가 보입니다.

<!---HONumber=August15_HO8-->