<properties
	pageTitle="Application Insights 포털 사용"
	description="Application Insights에 원격 분석을 보내도록 앱을 구성하고 나면 이 가이드에서 포털을 탐색하는 방법을 보여 줍니다."
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
	ms.date="11/23/2015"
	ms.author="awills"/>

# Application Insights 포털 사용

[프로젝트에서 Application Insights를 설정](app-insights-overview.md)하면 앱의 성능 및 사용에 대한 원격 분석 데이터가 [Azure 포털](https://portal.azure.com)에서 프로젝트의 Application Insights 리소스에 나타납니다.

## 원격 분석 찾기

[Azure 포털](https://portal.azure.com)에 로그인하고 앱에 대해 만든 Application Insights 리소스를 찾습니다.

![찾아보기를 클릭하고 Application Insights를 선택한 후 앱을 선택합니다.](./media/app-insights-portal/00-start.png)

개요 페이지는 기본적인 원격 분석을 제공하고 더 많은 링크를 더합니다. 내용은 앱의 유형에 따라 달라지며 사용자가 지정할 수 있습니다.



## 시간 범위

모든 블레이드의 차트 또는 표에서 다루는 시간 범위를 변경할 수 있습니다.

![Azure 포털에서 응용 프로그램의 개요 블레이드 열기](./media/app-insights-portal/03-range.png)


일부 데이터가 표시되어야 하지만 아직 표시되지 않은 경우 새로 고침을 클릭합니다. 차트는 특정 간격에 따라 자체적으로 새로 고쳐지지만 시간 범위가 더 클 경우 간격이 늘어납니다. 릴리스 모드에서 차트에 분석 파이프라인을 내놓기 위한 데이터에는 시간이 걸릴 수 있습니다.

차트의 일부로 확대/축소하려면 그 부분을 끌어서 돋보기 기호를 클릭합니다.

![차트의 일부를 끕니다.](./media/app-insights-portal/12-drag.png)



## 세분성 및 지점 값

해당 지점에서 메트릭 값을 표시하려면 차트 위로 마우스를 가져갑니다.

![차트 위로 마우스 이동](./media/app-insights-portal/02-focus.png)

특정 지점에서 메트릭 값은 이전 샘플링 간격에 걸쳐 집계됩니다.

샘플링 간격 또는 "세분성"는 블레이드 위쪽에 표시됩니다.

![블레이드의 헤더입니다.](./media/app-insights-portal/11-grain.png)

시간 범위 블레이드에서 세분성을 조정할 수 있습니다.

![블레이드의 헤더입니다.](./media/app-insights-portal/grain.png)

세분성은 선택한 시간 범위에 따라 사용 가능합니다. 명시적 세분성은 시간 범위에 대한 "자동" 세분성의 대안입니다.

## 앱 개요 블레이드

앱의 개요 블레이드(페이지)는 앱의 주요 진단 메트릭 요약을 보여 주며 포털의 다른 기능에 대한 게이트웨이입니다.

다음을 클릭합니다.

* **차트 또는 타일**: 자세한 내용을 볼 수 있습니다.
* **진단**: 다른 메트릭의 미리 정의된 페이지에 액세스할 수 있습니다.
* **메트릭 탐색기**: 사용자 고유의 메트릭 페이지를 만들 수 있습니다.
* **검색**: 요청, 예외 또는 로그 추적과 같은 이벤트의 특정 인스턴스를 조사할 수 있습니다.


![원격 분석을 보기 위한 주요 경로](./media/app-insights-portal/010-oview.png)


### 개요 블레이드 사용자 지정 

개요에 표시하려는 내용을 선택합니다. 사용자 지정에서 섹션 제목을 삽입하고 타일 및 차트를 끌며 항목을 제거하고 갤러리에서 새 타일 및 차트를 추가합니다.

![편집을 클릭합니다. 타일 및 차트를 끕니다. 갤러리에서 타일을 추가합니다. 완료를 클릭합니다.](./media/app-insights-portal/020-customize.png)

### Azure 대시보드 사용자 지정


Azure 포털 대시보드는 포털에 처음 로그인할 때 표시되는 홈페이지입니다. 여기에 여러 리소스의 타일(차트 그룹)을 모을 수 있습니다.

Application Insights 개요 블레이드의 타일을 포털 대시보드에 고정하려면 타일의 헤더를 선택한 다음 "..."을 선택합니다.

보다 포괄적인 대시보드를 얻으려면 [Power BI](https://azure.microsoft.com/blog/application-insights-content-pack-for-power-bi/)를 사용하여 원격 분석을 표시합니다.

## 메트릭 블레이드

자세한 내용을 보기 위해 개요 블레이드에서 클릭하면 메트릭 탐색기로 전환됩니다(보다 구체적인 제목이 표시될 수도 있음).

메트릭 탐색기 단추를 사용하여 새 블레이드를 만든 다음 편집하고 저장할 수도 있습니다.


![개요 블레이드에서 메트릭 클릭](./media/app-insights-portal/16-metrics.png)

### 차트 및 표 편집

블레이드에 새 차트를 추가하려면:

![메트릭 탐색기에서 추가 차트 선택](./media/app-insights-portal/04-add.png)

기존 또는 새 차트를 선택하여 보이는 내용 편집:

![하나 이상의 메트릭 선택](./media/app-insights-portal/08-select.png)

함께 표시할 수 있는 조합에 관한 제한이 있지만 차트에 하나 이상의 메트릭을 표시할 수 있습니다. 한 메트릭을 선택하면 일부 다른 메트릭을 사용할 수 없습니다.

[사용자 지정 메트릭](app-insights-api-custom-events-metrics.md#track-metric)을 앱에 코딩한 경우(TrackMetric 호출 및 TrackEvent 호출에 연결된 메트릭) 여기에 나열됩니다.

### 데이터 분할

차트 또는 표를 선택하고 그룹으로 전환하여 그룹별로 속성을 선택합니다.

![그룹화를 선택한 다음 그룹별에서 속성 선택](./media/app-insights-portal/15-segment.png)

[사용자 지정 메트릭](app-insights-api-custom-events-metrics.md#properties)을 앱에 코딩하고 속성 값을 포함하는 경우 목록에서 속성을 선택할 수 있게 됩니다.

데이터를 분할하기에 차트가 너무 작나요? 높이 조정:

![슬라이더 조정](./media/app-insights-portal/18-height.png)

### 데이터 필터링

속성 값의 선택한 집합에 대한 메트릭 보기:

![필터를 클릭하고 속성을 확장하여 값 선택](./media/app-insights-portal/19-filter.png)

특정 속성에 대한 값을 선택하지 않은 경우 모두 선택한 것과 동일합니다. 즉, 해당 속성에는 필터가 없습니다.

각 속성 값과 함께 이벤트 수를 확인합니다. 한 속성 값을 선택하면 다른 속성 값과 함께 수가 조정됩니다.

### 매트릭 블레이드 저장

차트를 만든 경우 즐겨찾기로 저장합니다. 조직 계정을 사용하는 경우 다른 팀 구성원과 이를 공유할지 선택할 수 있습니다.

![즐겨찾기 선택](./media/app-insights-portal/21-favorite-save.png)

블레이드를 다시 보려면 **개요 블레이드로 이동**하여 즐겨찾기를 엽니다.

![개요 블레이드에서 즐겨찾기 선택](./media/app-insights-portal/22-favorite-get.png)

저장했을 때 상대 시간을 선택한 경우 해당 블레이드가 최신 메트릭으로 업데이트됩니다. 절대 시간 범위를 선택한 경우 매번 동일한 데이터가 표시됩니다.

### 블레이드 다시 설정

블레이드를 편집하지만 저장된 원본 세트로 되돌아가려는 경우 재설정을 클릭하면 됩니다.

![메트릭 탐색기 위쪽에 있는 단추](./media/app-insights-portal/17-reset.png)

## 검색

검색은 페이지 보기, 요청, 예외, 로그 추적 및 사용자 지정 이벤트와 같은 개별 이벤트를 표시합니다. 집계된 메트릭 또는 TrackMetric() 호출 인스턴스는 표시하지 않습니다.

> [AZURE.NOTE]앱에서 다양한 원격 분석을 생성하는 경우(ASP.NET SDK 버전 2.0.0-beta3 이상 사용), 적응 샘플링 모듈 이벤트의 대표적인 일부만 전송하여 포털에 전송되는 볼륨이 자동으로 줄어듭니다. 그러나, 동일한 요청과 관련된 이벤트가 그룹으로 선택되거나 선택 취소되므로 관련 이벤트 간을 이동할 수 있습니다. [샘플링에 대해 알아봅니다](app-insights-sampling.md).

진단 검색을 엽니다.

![진단 검색 열기](./media/app-insights-diagnostic-search/01-open-Diagnostic.png)

필터 블레이드를 열고 확인하려는 이벤트 유형을 선택합니다. (나중에 열었던 블레이드로 필터를 복원하려는 경우 재설정을 클릭합니다.)

![필터 선택 및 원격 분석 유형 선택](./media/app-insights-portal/02-filter-req.png)

### 속성 값에서 필터링

해당 속성 값에서 이벤트를 필터링할 수 있습니다. 사용 가능한 속성은 선택한 이벤트 유형에 따라 다릅니다.

예를들어 특정 응답 코드에 대한 요청을 선택합니다.

![속성 확장 및 값 선택](./media/app-insights-portal/03-response500.png)

특정 속성 값을 선택하지 않는 것은 모든 값을 선택하는 것과 동일한 효과가 있습니다. 해당 속성에서 필터링을 전한합니다.

> [AZURE.NOTE]앱에서 다양한 원격 분석을 생성하는 경우 적응 샘플링 모듈은 이벤트의 대표적인 분수만 전송하여 포털에 전송되는 볼륨을 자동으로 줄입니다. 동일한 작업에 속하는 이벤트를 그룹으로 선택하거나 선택 취소되므로 관련된 이벤트를 탐색할 수 있습니다. [샘플링에 대해 알아봅니다.](app-insights-sampling.md)


### 검색 범위 좁히기

필터 값의 오른쪽에 있는 수는 현재 필터링된 집합에서 발생한 수를 보여줍니다.

이 예제에서는 `Reports/Employees` 요청이 500개의 오류 중 대부분을 초래함이 명확합니다.

![속성 확장 및 값 선택](./media/app-insights-portal/04-failingReq.png)

또한 이 시간 동안 발생하고 있는 기타 이벤트가 무엇인지도 보려는 경우 **정의되지 않은 속성의 이벤트 포함**을 선택할 수 있습니다.

### 검색 저장

원하는 모든 필터를 설치할 때 즐겨찾기로 검색을 저장할 수 있습니다. 조직 계정으로 작업하는 경우 다른 팀 구성원과 공유할 것인지를 선택할 수 있습니다.

![즐겨찾기 클릭, 이름 설정 및 저장 클릭](./media/app-insights-portal/08-favorite-save.png)


검색을 다시 보려면 **개요 블레이드로 이동**하여 즐겨찾기를 엽니다.

![즐겨찾기 타일](./media/app-insights-portal/22-favorite-get.png)

상대 시간 범위로 저장한 경우 다시 열린 블레이드는 최신 데이터입니다. 절대 시간 범위로 저장한 경우 매번 같은 데이터가 보입니다.

<!---HONumber=AcomDC_1125_2015-->