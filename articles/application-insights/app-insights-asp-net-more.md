<properties 
	pageTitle="Application Insights를 최대한 활용하기 | Microsoft Azure" 
	description="Application Insights를 시작한 경우 다음 기능을 사용할 수 있습니다." 
	services="application-insights" 
    documentationCenter=".net"
	authors="alancameronwills" 
	manager="douge"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="08/30/2016" 
	ms.author="awills"/>

# Application Insights의 추가 원격 분석

[ASP.NET 코드에 Application Insights를 추가](app-insights-asp-net.md)한 후 추가 원격 분석을 위해 수행할 수 있는 몇 가지 작업이 있습니다.

## 앱이 IIS 서버에서 실행되는 경우

앱이 사용자가 제어하는 IIS 서버에서 호스트되는 경우 서버에 Application Insights 상태 모니터를 설치합니다. 이미 설치되어 있으면 아무 작업도 수행할 필요가 없습니다.

1. 각 IIS 웹 서버에서 관리자 자격 증명으로 로그인합니다.
2. [상태 모니터 설치 관리자](http://go.microsoft.com/fwlink/?LinkId=506648)를 다운로드하고 실행합니다.
3. 설치 마법사에서 Microsoft Azure에 로그인합니다.

아무 작업도 수행하지 않아도 되지만 앱에 대해 모니터링이 사용하도록 설정되어 있는지 확인할 수 있습니다.

![Azure에서 확장](./media/app-insights-asp-net-more/025.png)

Visual Studio에서 앱을 계측하지 않은 경우에도 상태 모니터를 사용하여 [런타임에 모니터링을 사용하도록 설정](app-insights-monitor-performance-live-website-now.md)할 수도 있습니다.

### 이점

상태 모니터가 서버 컴퓨터에 설치된 경우 몇 가지 추가 원격 분석을 얻을 수 있습니다.

* .NET 4.5 앱에 대한 종속성 원격 분석(앱에 대한 SQL 호출 및 REST 호출). 이후 버전의 .NET에는 종속성 원격 분석에 상태 모니터가 필요하지 않습니다.
* 예외 스택 추적에 세부 정보가 표시됩니다.
* 성능 카운터. Application Insights에서 서버 블레이드에 다음과 같은 카운터가 나타납니다.

![Azure에서 확장](./media/app-insights-asp-net-more/070.png)

표시되는 카운터 수를 조정하려면 [차트를 편집](app-insights-metrics-explorer.md)합니다. 원하는 성능 카운터가 사용 가능한 집합에 포함되지 않은 경우 [성능 카운터 모듈에서 수집한 집합에 추가](app-insights-web-monitor-performance.md#system-performance-counters)할 수 있습니다.

## Azure 웹앱인 경우

앱이 Azure 웹앱으로 실행되는 경우 앱 또는 VM에 대한 Azure 제어판으로 이동하여 Application Insights 확장을 추가합니다. **도구**에서 **성능 모니터링**을 열고 **Application Insights**를 구성합니다. 대화 상자가 나타나면 이미 만든 Application Insights 리소스를 선택합니다.

![Azure에서 확장](./media/app-insights-asp-net-more/05-extend.png)

### 이점

* 예외 스택 추적에 세부 정보가 표시됩니다.
* .NET 4.5 앱에 대한 종속성 원격 분석(앱에 대한 SQL 호출 및 REST 호출). 이후 버전의 .NET에는 종속성 원격 분석에 확장이 필요하지 않습니다.

![Azure에서 확장](./media/app-insights-asp-net-more/080.png)

Visual Studio에서 앱을 계측하지 않은 경우에도 이 방법을 사용하여 [런타임에 성능 모니터링을 사용하도록 설정](app-insights-monitor-performance-live-website-now.md)할 수도 있습니다.

## 클라이언트 쪽 모니터링

응용 프로그램의 서버(백엔드)에서 원격 분석 데이터를 보내는 SDK를 설치 했습니다. 이제 클라이언트쪽 모니터링을 추가할 수 있습니다. 이 사용자, 세션, 페이지 보기 및 모든 예외 또는 브라우저에서 발생하는 충돌에 데이터를 제공합니다. 사용자는 응용 프로그램과 사용자의 고객이 함께 작동하는지 추적하는 코드를 오른쪽 아래의 클릭과 키 입력 세부 수준에서 작성할 수 있습니다.

웹 페이지 각각에 Application Insights JavaScript 코드 조각을 추가하여 클라이언트 브라우저에서 원격 분석을 가져올 수 있습니다.

1. Azure에서 앱에 대한 Application Insights 리소스를 엽니다.
2. 빠른 시작에서 클라이언트 쪽 모니터를 열고 코드 조각을 복사합니다.
3. 각 웹 페이지의 헤더에 표시되도록 붙여 넣습니다. 일반적으로 마스터 레이아웃 페이지에 붙여 넣는 방식으로 작업할 수 있습니다.

![Azure에서 확장](./media/app-insights-asp-net-more/100.png)

코드는 응용 프로그램 리소스를 식별하는 계측 키를 포함한다는 것을 참고하세요.

### 이점

* JavaScript를 작성하여 [웹 페이지에서 사용자 지정 원격 분석](app-insights-api-custom-events-metrics.md)(예: 단추 클릭 추적)을 보낼 수 있습니다.
* [분석](app-insights-analytics.md)에서 `pageViews`의 데이터 및 `dependencies`의 AJAX 데이터
* 브라우저 블레이드의 [클라이언트 성능 및 사용 현황 데이터](app-insights-javascript.md)

![Azure에서 확장](./media/app-insights-asp-net-more/090.png)


[웹 페이지 추적에 대해 더 알아보기](app-insights-web-track-usage.md)



## 응용 프로그램 버전 추적

MS 빌드 프로세스에서 `buildinfo.config`가 생성되도록 합니다. .csproj 파일에서 다음을 추가합니다.

```XML

    <PropertyGroup>
      <GenerateBuildInfoConfigFile>true</GenerateBuildInfoConfigFile>    <IncludeServerNameInBuildInfo>true</IncludeServerNameInBuildInfo>
    </PropertyGroup> 
```

빌드 정보가 있는 경우 Application Insights 웹 모듈에서 원격 분석의 모든 항목에 **응용 프로그램 버전**을 속성으로 자동으로 추가합니다. 이렇게 하면 [진단 검색](app-insights-diagnostic-search.md)을 수행하거나 [메트릭을 탐색](app-insights-metrics-explorer.md)할 때 버전을 기준으로 필터링할 수 있습니다.

그러나 빌드 버전 번호가 Visual Studio의 개발자 빌드가 아닌 MS 빌드에서 생성되도록 합니다.


## 가용성 웹 테스트

전 세계에서 정기적으로 발생하는 웹앱 HTTP 요청을 전송합니다. 응답 속도가 느리거나 불안정한 경우 경고가 표시됩니다.

앱에 대한 Application Insights 리소스에서 가용성 타일을 클릭하여 웹 테스트를 추가, 편집 및 확인합니다.

여러 위치에서 실행되는 여러 테스트를 추가할 수 있습니다.

![Azure에서 확장](./media/app-insights-asp-net-more/110.png)

[자세히 알아보기](app-insights-monitor-web-app-availability.md)

## 사용자 지정 원격 분석 및 로깅

코드에 추가한 Application Insights 패키지는 응용 프로그램에서 호출할 수 있는 API를 제공합니다.

* 비즈니스 이벤트 수를 계산하거나 성능을 모니터링하는 등에 사용되는 [사용자 고유의 이벤트 및 메트릭 생성](app-insights-api-custom-events-metrics.md)
* Log4Net, NLog 또는 System.Diagnostics.Trace에서 [로그 추적 캡처](app-insights-asp-net-trace-logs.md)
* 원격 분석 프로세서를 작성하여 앱에서 전송된 표준 원격 분석 [필터링, 수정 또는 보강](app-insights-api-filtering-sampling.md)


## 강력한 분석 및 프레젠테이션

다양한 방법으로 데이터를 탐색할 수 있습니다. 최근에 Application Insights를 시작한 경우 다음 문서를 확인해 보세요.

||
|---|---
|[**인스턴스 데이터에 대한 진단 검색**](app-insights-visual-studio.md)<br/>요청, 예외, 종속성 호출, 로그 추적 및 페이지 보기와 같은 이벤트를 검색하고 필터링할 수 있습니다. Visual Studio의 스택 추적에서 코드로 이동합니다.|![Visual studio](./media/app-insights-asp-net/61.png)
|[**집계된 데이터에 대한 메트릭 탐색기**](app-insights-metrics-explorer.md)<br/>요청, 오류 및 예외의 비율과 응답 시간, 페이지 로드 시간과 같은 집계된 데이터를 탐색, 필터링 및 분할할 수 있습니다.|![Visual studio](./media/app-insights-asp-net-more/060.png)
|[**대시보드**](app-insights-dashboards.md#dashboards)<br/>여러 리소스의 데이터를 매시업한 후 다른 사용자와 공유할 수 있습니다. 다중 구성 요소 응용 프로그램에서 사용하거나 단체방에 연속으로 표시하는 데 유용합니다. |![대시보드 샘플](./media/app-insights-asp-net/62.png)
|[**라이브 매트릭 스트림**](app-insights-metrics-explorer.md#live-metrics-stream)<br/>새 빌드를 배포할 때 이러한 실시간에 가까운 성능 표시기를 확인하여 모든 항목이 예상대로 작동하는지 알 수 있습니다.|![분석 샘플](./media/app-insights-asp-net-more/050.png)
|[**분석**](app-insights-analytics.md)<br/>이 강력한 쿼리 언어를 사용하여 앱의 성능 및 사용 현황에 대한 까다로운 질문에 답변할 수 있습니다.|![분석 샘플](./media/app-insights-asp-net-more/010.png)
|[**자동 및 수동 경고**](app-insights-alerts.md)<br/>자동 경고는 앱의 일반적인 원격 분석 패턴에 맞게 조정되고, 일반적인 패턴을 벗어나는 항목이 있으면 트리거합니다. 특정 수준의 사용자 지정 또는 표준 메트릭에 대해 경고를 설정할 수도 있습니다.|![경고 샘플](./media/app-insights-asp-net-more/020.png)

## 데이터 관리

|||
|---|---|
|[**연속 내보내기**](app-insights-export-telemetry.md)<br/>원하는 방식으로 분석할 수 있도록 모든 원격 분석을 저장소에 복사합니다.|
|**데이터 액세스 API**<br/>곧 제공될 예정입니다.|
|[**샘플링**](app-insights-sampling.md)<br/>데이터 속도를 줄이고 가격 책정 계층 제한을 벗어나지 않도록 하는 데 도움이 됩니다.|![샘플링 타일](./media/app-insights-asp-net-more/030.png)

<!---HONumber=AcomDC_0907_2016-->