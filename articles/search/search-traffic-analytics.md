---
title: 검색 트래픽 분석에 대 한 원격 분석
titleSuffix: Azure Cognitive Search
description: Azure Cognitive Search에 대 한 검색 트래픽 분석을 사용 하도록 설정 하 고 Application Insights를 사용 하 여 원격 분석 및 사용자 시작 이벤트를 수집한 다음 Power BI 보고서에서 결과를 분석 합니다.
author: HeidiSteen
manager: nitinme
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 03/18/2020
ms.openlocfilehash: 7c843b45b5a398aaaa1aab66f80961560477cf18
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "82128110"
---
# <a name="collect-telemetry-data-for-search-traffic-analytics"></a>검색 트래픽 분석에 대 한 원격 분석 데이터 수집

검색 트래픽 분석은 사용자가 시작한 클릭 이벤트 및 키보드 입력과 같이 Azure Cognitive Search 응용 프로그램과의 사용자 상호 작용에 대 한 원격 분석을 수집 하기 위한 패턴입니다. 이 정보를 사용 하 여 인기 있는 검색 용어, 클릭 광고 요금 및 결과가 0 인 쿼리 입력을 포함 하 여 검색 솔루션의 효율성을 확인할 수 있습니다.

이 패턴은 [Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview) ( [Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/)의 기능)에 의존 하 여 사용자 데이터를 수집 합니다. 이 문서에 설명 된 대로 클라이언트 코드에 계측을 추가 해야 합니다. 마지막으로 데이터를 분석 하는 보고 메커니즘이 필요 합니다. Power BI 하는 것이 좋지만 Application Insights에 연결 되는 응용 프로그램 대시보드 또는 도구를 사용할 수 있습니다.

> [!NOTE]
> 이 문서에서 설명 하는 패턴은 고급 시나리오를 위한 것 이며, 클라이언트에 추가 하는 코드에서 생성 된 스트림 데이터를 클릭 합니다. 이와 대조적으로 서비스 로그는 설정 하기 쉬우며, 다양 한 메트릭을 제공 하며, 포털에서 코드 필요 없이 수행할 수 있습니다. 모든 시나리오에 대해 로깅을 사용 하는 것이 좋습니다. 자세한 내용은 [로그 데이터 수집 및 분석](search-monitor-logs.md)을 참조 하세요.

## <a name="identify-relevant-search-data"></a>관련 검색 데이터 식별

검색 트래픽 분석에 대 한 유용한 메트릭을 포함 하려면 검색 응용 프로그램의 사용자에 게 일부 신호를 기록 해야 합니다. 이러한 신호는 사용자에 게 관심이 있는 콘텐츠와 관련 된 것으로 간주 됩니다. 검색 트래픽 분석에는 다음이 포함 됩니다.

+ 사용자 생성 검색 이벤트: 사용자가 시작한 검색 쿼리만 흥미롭습니다. 패싯, 추가 콘텐츠 또는 내부 정보를 채우는 데 사용되는 검색 요청은 중요하지 않으며 결과를 왜곡하고 편견을 갖게 만듭니다.

+ 사용자가 생성 한 클릭 이벤트: 검색 결과 페이지에서 클릭 이벤트는 일반적으로 문서가 특정 검색 쿼리에 대 한 관련 결과 임을 의미 합니다.

상관 관계 ID를 사용 하 여 검색 및 클릭 이벤트를 연결 하면 응용 프로그램의 검색 기능이 얼마나 잘 수행 되 고 있다는 것을 깊이 있게 파악할 수 있습니다.

## <a name="add-search-traffic-analytics"></a>트래픽 분석 검색 추가

Azure Cognitive Search 서비스에 대 한 [포털](https://portal.azure.com) 페이지에서 트래픽 분석 검색 페이지에는이 원격 분석 패턴에 따라 다음과 같은 참고 자료 시트가 포함 되어 있습니다. 이 페이지에서 Application Insights 리소스를 선택 하거나 만들고, 계측 키를 가져오고, 솔루션에 맞게 조정할 수 있는 코드 조각을 복사 하 고, 패턴에 반영 된 스키마를 통해 작성 된 Power BI 보고서를 다운로드할 수 있습니다.

![포털에서 트래픽 분석 페이지 검색](media/search-traffic-analytics/azuresearch-trafficanalytics.png "포털에서 트래픽 분석 페이지 검색")

## <a name="1---set-up-application-insights"></a>1-Application Insights 설정

기존 Application Insights 리소스를 선택 하거나 아직 없는 경우 [새로 만듭니다](https://docs.microsoft.com/azure/azure-monitor/app/create-new-resource) . 검색 트래픽 분석 페이지를 사용 하는 경우 응용 프로그램에서 Application Insights에 연결 하는 데 필요한 계측 키를 복사할 수 있습니다.

Application Insights 리소스가 있으면 [지원 되는 언어 및 플랫폼에 대 한 지침](https://docs.microsoft.com/azure/azure-monitor/app/platforms) 에 따라 앱을 등록할 수 있습니다. 등록은 Application Insights의 계측 키를 코드에 추가 하기만 하 여 연결을 설정 합니다. 포털에서 또는 기존 리소스를 선택할 때 검색 트래픽 분석 페이지에서 키를 찾을 수 있습니다.

일부 Visual Studio 프로젝트 형식에 대해 작동 하는 바로 가기는 다음 단계에서 반영 됩니다. 리소스를 만들고 앱을 몇 번의 클릭 만으로 등록 합니다.

1. Visual Studio 및 ASP.NET 개발의 경우 솔루션을 열고 **프로젝트** > **Application Insights 원격 분석 추가**를 선택 합니다.

1. **시작**을 클릭합니다.

1. Microsoft 계정, Azure 구독 및 Application Insights 리소스를 제공 하 여 앱을 등록 합니다 (새 리소스는 기본값). **등록**을 클릭합니다.

이 시점에서 응용 프로그램은 응용 프로그램 모니터링에 대해 설정 됩니다. 즉, 모든 페이지 로드가 기본 메트릭을 사용 하 여 추적 됩니다. 이전 단계에 대 한 자세한 내용은 [Enable Application Insights 서버 쪽 원격 분석](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core#enable-application-insights-server-side-telemetry-visual-studio)을 참조 하세요.

## <a name="2---add-instrumentation"></a>2 - 계측 추가

이 단계에서는 위의 단계에서 만든 Application Insights 리소스를 사용 하 여 사용자 고유의 검색 응용 프로그램을 계측 합니다. 이 프로세스에는 원격 분석 클라이언트 만들기부터 네 단계가 있습니다.

### <a name="step-1-create-a-telemetry-client"></a>1 단계: 원격 분석 클라이언트 만들기

Application Insights에 이벤트를 보내는 개체를 만듭니다. 브라우저에서 실행 되는 서버 쪽 응용 프로그램 코드 또는 클라이언트 쪽 코드에 계측을 추가 하 여 c # 및 JavaScript 변형으로 표시할 수 있습니다 (다른 언어의 경우 [지원 되는 플랫폼 및 프레임 워크](https://docs.microsoft.com/azure/application-insights/app-insights-platforms)의 전체 목록 참조). 원하는 수준의 정보를 제공 하는 방법을 선택 합니다.

서버 쪽 원격 분석은 응용 프로그램 계층에서 메트릭을 캡처합니다. 예를 들어 클라우드의 웹 서비스로 실행 되는 응용 프로그램 또는 회사 네트워크의 온-프레미스 앱에서 메트릭을 캡처합니다. 서버 쪽 원격 분석은 검색 및 클릭 이벤트, 결과의 문서 위치 및 쿼리 정보를 캡처하며, 데이터 컬렉션은 해당 계층에서 사용할 수 있는 모든 정보로 범위가 한정 됩니다.

클라이언트에 쿼리 입력을 조작 하거나, 탐색을 추가 하거나, 컨텍스트를 포함 하는 추가 코드 (예: 홈 페이지에서 시작 된 쿼리 및 제품 페이지에서 시작 된 쿼리)가 있을 수 있습니다. 솔루션을 설명 하는 경우 원격 분석에서 추가 세부 정보를 반영 하도록 클라이언트 쪽 계측을 선택할 수 있습니다. 이 추가 정보를 수집 하는 방법은이 패턴의 범위를 벗어나게 되지만 [웹 페이지에 대 한 Application Insights를](https://docs.microsoft.com/azure/azure-monitor/app/javascript#explore-browserclient-side-data) 검토할 수 있습니다. 

**C# 사용**

C #의 경우 **InstrumentationKey** 는 프로젝트가 ASP.NET 인 경우 appsettings와 같은 응용 프로그램 구성에서 찾을 수 있습니다. 키 위치를 잘 모르겠으면 등록 지침을 다시 참조 하세요.

```csharp
private static TelemetryClient _telemetryClient;

// Add a constructor that accepts a telemetry client:
public HomeController(TelemetryClient telemetry)
    {
        _telemetryClient = telemetry;
    }
```

**JavaScript 사용**

```javascript
<script type="text/javascript">var appInsights=window.appInsights||function(config){function r(config){t[config]=function(){var i=arguments;t.queue.push(function(){t[config].apply(t,i)})}}var t={config:config},u=document,e=window,o="script",s=u.createElement(o),i,f;s.src=config.url||"//az416426.vo.msecnd.net/scripts/a/ai.0.js";u.getElementsByTagName(o)[0].parentNode.appendChild(s);try{t.cookie=u.cookie}catch(h){}for(t.queue=[],i=["Event","Exception","Metric","PageView","Trace","Dependency"];i.length;)r("track"+i.pop());return r("setAuthenticatedUserContext"),r("clearAuthenticatedUserContext"),config.disableExceptionTracking||(i="onerror",r("_"+i),f=e[i],e[i]=function(config,r,u,e,o){var s=f&&f(config,r,u,e,o);return s!==!0&&t["_"+i](config,r,u,e,o),s}),t}
({
instrumentationKey: "<YOUR INSTRUMENTATION KEY>"
});
window.appInsights=appInsights;
</script>
```

### <a name="step-2-request-a-search-id-for-correlation"></a>2 단계: 상관 관계에 대 한 검색 ID 요청

검색 요청을 클릭과 상호 연결 하려면 이러한 두 개의 개별 이벤트를 연결 하는 상관 관계 ID가 필요 합니다. Azure Cognitive Search는 HTTP 헤더를 사용 하 여 요청할 때 검색 ID를 제공 합니다.

검색 ID를 사용 하면 로그인 하는 사용자 지정 메트릭과 Application Insights 하 여 요청 자체에 대해 Azure Cognitive Search에서 내보낸 메트릭에 상관 관계가 있습니다.  

**C# 사용**

```csharp
// This sample uses the .NET SDK https://www.nuget.org/packages/Microsoft.Azure.Search

var client = new SearchIndexClient(<SearchServiceName>, <IndexName>, new SearchCredentials(<QueryKey>)

// Use HTTP headers so that you can get the search ID from the response
var headers = new Dictionary<string, List<string>>() { { "x-ms-azs-return-searchid", new List<string>() { "true" } } };
var response = await client.Documents.SearchWithHttpMessagesAsync(searchText: searchText, searchParameters: parameters, customHeaders: headers);
string searchId = string.Empty;
if (response.Response.Headers.TryGetValues("x-ms-azs-searchid", out IEnumerable<string> headerValues)){
    searchId = headerValues.FirstOrDefault();
}
```

**JavaScript 사용 (REST Api 호출)**

```javascript
request.setRequestHeader("x-ms-azs-return-searchid", "true");
request.setRequestHeader("Access-Control-Expose-Headers", "x-ms-azs-searchid");
var searchId = request.getResponseHeader('x-ms-azs-searchid');
```

### <a name="step-3-log-search-events"></a>3 단계: 로그 검색 이벤트

사용자가 검색 요청을 실행할 때마다 Application Insights 사용자 지정 이벤트에서 다음 스키마를 사용 하 여 검색 이벤트로 로그인 해야 합니다. 사용자가 생성 한 검색 쿼리만 기록 해야 합니다.

+ **Searchservicename**: (문자열) 검색 서비스 이름
+ **Searchid**: (guid) 검색 쿼리의 고유 식별자 (검색 응답에 제공)
+ **IndexName**: (문자열) 쿼리할 검색 서비스 인덱스입니다.
+ **Queryterms**: (문자열) 사용자가 입력 한 검색어
+ **Resultcount**: (int) 반환 된 문서 수 (검색 응답에 제공 됨)
+ **ScoringProfile**: (문자열) 사용 하는 점수 매기기 프로필의 이름입니다 (있는 경우).

> [!NOTE]
> 검색 쿼리에 $count = true를 추가 하 여 사용자가 생성 한 쿼리 수를 요청 합니다. 자세한 내용은 [문서 검색 (REST)](/rest/api/searchservice/search-documents#counttrue--false)을 참조 하세요.
>

**C# 사용**

```csharp
var properties = new Dictionary <string, string> {
    {"SearchServiceName", <service name>},
    {"SearchId", <search Id>},
    {"IndexName", <index name>},
    {"QueryTerms", <search terms>},
    {"ResultCount", <results count>},
    {"ScoringProfile", <scoring profile used>}
    };
_telemetryClient.TrackEvent("Search", properties);
```

**JavaScript 사용**

```javascript
appInsights.trackEvent("Search", {
SearchServiceName: <service name>,
SearchId: <search id>,
IndexName: <index name>,
QueryTerms: <search terms>,
ResultCount: <results count>,
ScoringProfile: <scoring profile used>
});
```

### <a name="step-4-log-click-events"></a>4 단계: 클릭 이벤트 기록

사용자가 문서를 클릭할 때마다 검색 분석을 위해 신호를 기록해야 합니다. Application Insights 사용자 지정 이벤트를 사용하여 다음 스키마로 이러한 이벤트를 기록합니다.

+ **ServiceName**: (문자열) 검색 서비스 이름
+ **Searchid**: (guid) 관련 검색 쿼리의 고유 식별자
+ **DocId**: (문자열) 문서 식별자
+ **Position**: (int) 검색 결과 페이지에서 문서의 순위

> [!NOTE]
> 위치는 애플리케이션의 카디널 순서를 참조합니다. 이 숫자가 항상 같다면 이 숫자를 자유롭게 설정하여 비교할 수 있습니다.
>

**C# 사용**

```csharp
var properties = new Dictionary <string, string> {
    {"SearchServiceName", <service name>},
    {"SearchId", <search id>},
    {"ClickedDocId", <clicked document id>},
    {"Rank", <clicked document position>}
    };
_telemetryClient.TrackEvent("Click", properties);
```

**JavaScript 사용**

```javascript
appInsights.trackEvent("Click", {
    SearchServiceName: <service name>,
    SearchId: <search id>,
    ClickedDocId: <clicked document id>,
    Rank: <clicked document position>
});
```

## <a name="3---analyze-in-power-bi"></a>3 - Power BI에서 분석

앱을 계측 하 고 응용 프로그램이 Application Insights에 올바르게 연결 되었는지 확인 한 후 미리 정의 된 보고서 템플릿을 다운로드 하 여 Power BI desktop에서 데이터를 분석 합니다. 보고서에는 검색 트래픽 분석을 위해 캡처된 추가 데이터를 분석 하는 데 유용한 미리 정의 된 차트와 테이블이 포함 되어 있습니다.

1. Azure Cognitive Search 대시보드 왼쪽 탐색 창의 **설정**에서 **트래픽 분석 검색**을 클릭 합니다.

1. **트래픽 분석 검색** 페이지의 3단계에서 **Power BI Desktop 가져오기**를 클릭하여 Power BI를 설치합니다.

   ![Power BI 보고서 가져오기](./media/search-traffic-analytics/get-use-power-bi.png "Power BI 보고서 가져오기")

1. 동일한 페이지에서 **Power BI 보고서 다운로드**를 클릭 합니다.

1. Power BI Desktop에서 보고서가 열리고 Application Insights에 연결 하 고 자격 증명을 제공 하 라는 메시지가 표시 됩니다. Application Insights 리소스에 대 한 Azure Portal 페이지에서 연결 정보를 찾을 수 있습니다. 자격 증명에는 포털 로그인에 사용 하는 것과 동일한 사용자 이름 및 암호를 제공 합니다.

   ![Application Insights에 연결](./media/search-traffic-analytics/connect-to-app-insights.png "Application Insights에 연결")

1. **로드**를 클릭 합니다.

이 보고서에는 검색 성능과 관련성을 향상시키기 위해 정보에 기반한 합리적 의사 결정을 내리는 데 도움이 되는 차트와 테이블이 포함되어 있습니다.

메트릭에 포함되는 항목은 다음과 같습니다.

+ 검색 볼륨 및 가장 인기 있는 용어-문서 쌍: 같은 문서를 클릭 하 여 클릭을 기준으로 정렬 합니다.
+ 클릭 없이 검색: 클릭을 등록하지 않는 상위 쿼리를 일컫는 용어

다음 스크린샷에서는 모든 스키마 요소를 사용한 경우 기본 제공 보고서가 어떻게 표시 되는지 보여 줍니다.

![Azure Cognitive Search에 대 한 Power BI 대시보드](./media/search-traffic-analytics/azuresearch-powerbi-dashboard.png "Azure Cognitive Search에 대 한 Power BI 대시보드")

## <a name="next-steps"></a>다음 단계

검색 애플리케이션을 계측하여 검색 서비스에 대한 강력하고 통찰력 있는 데이터를 가져옵니다.

[Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview)에 대한 자세한 내용을 확인하고, [가격 페이지](https://azure.microsoft.com/pricing/details/application-insights/)를 방문하여 다양한 각 서비스 계층에 대해 자세히 알아볼 수 있습니다.

놀라운 보고서 만들기에 대해 자세히 알아보세요. 자세한 내용은 [Power BI Desktop 시작을](https://powerbi.microsoft.com/documentation/powerbi-desktop-getting-started/) 참조 하세요.