---
title: 검색 트래픽 분석을 위한 원격 분석
titleSuffix: Azure Cognitive Search
description: Azure Cognitive Search에 대한 검색 트래픽 분석을 활성화하고, 응용 프로그램 통찰력을 사용하여 원격 분석 및 사용자가 시작한 이벤트를 수집한 다음 Power BI 보고서에서 결과를 분석합니다.
author: HeidiSteen
manager: nitinme
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 03/18/2020
ms.openlocfilehash: db8c1781061f038cc90310fcd00c220fa6f5d1a0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80258212"
---
# <a name="collect-telemetry-data-for-search-traffic-analytics"></a>검색 트래픽 분석을 위한 원격 분석 데이터 수집

검색 트래픽 분석은 사용자가 시작한 클릭 이벤트 및 키보드 입력과 같은 Azure Cognitive Search 응용 프로그램과의 사용자 상호 작용에 대한 원격 분석을 수집하기 위한 패턴입니다. 이 정보를 사용하여 인기 있는 검색어, 클릭스루 속도 및 결과를 0으로 산출하는 쿼리 입력을 포함하여 검색 솔루션의 효과를 확인할 수 있습니다.

이 패턴은 사용자 데이터를 수집하기 위해 [응용 프로그램 인사이트(Azure](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview) [Monitor의](https://docs.microsoft.com/azure/azure-monitor/)기능)에 종속됩니다. 이 문서에서 설명한 대로 클라이언트 코드에 계측을 추가해야 합니다. 마지막으로 데이터를 분석하기 위한 보고 메커니즘이 필요합니다. Power BI를 권장하지만 응용 프로그램 대시보드 또는 응용 프로그램 인사이트에 연결하는 모든 도구를 사용할 수 있습니다.

> [!NOTE]
> 이 문서에서 설명하는 패턴은 클라이언트에 추가하는 코드로 생성된 고급 시나리오 및 클릭스트림 데이터에 대한 것입니다. 반면 서비스 로그는 쉽게 설정하고 다양한 메트릭을 제공하며 코드가 필요 없이 포털에서 수행할 수 있습니다. 모든 시나리오에 진단 로깅을 사용하도록 설정하는 것이 좋습니다. 자세한 내용은 [로그 데이터 수집 및 분석](search-monitor-logs.md)을 참조하십시오.

## <a name="identify-relevant-search-data"></a>관련 검색 데이터 식별

검색 트래픽 분석에 유용한 메트릭을 사용하려면 검색 응용 프로그램의 사용자로부터 일부 신호를 기록해야 합니다. 이러한 신호는 사용자가 관심을 가지고 있고 관련성이 있다고 생각하는 콘텐츠를 의미합니다. 검색 트래픽 분석의 경우 다음과 같은 것이 포함됩니다.

+ 사용자 생성 검색 이벤트: 사용자가 시작한 검색 쿼리만 흥미롭습니다. 패싯, 추가 콘텐츠 또는 내부 정보를 채우는 데 사용되는 검색 요청은 중요하지 않으며 결과를 왜곡하고 편견을 갖게 만듭니다.

+ 사용자가 생성한 클릭 이벤트: 검색 결과 페이지에서 클릭 이벤트는 일반적으로 문서가 특정 검색 쿼리에 대한 관련 결과임을 의미합니다.

검색 및 클릭 이벤트를 상관 관계 ID와 연결하면 응용 프로그램의 검색 기능이 얼마나 잘 수행되는지 자세히 이해할 수 있습니다.

## <a name="add-search-traffic-analytics"></a>트래픽 분석 검색 추가

Azure Cognitive Search 서비스의 [포털](https://portal.azure.com) 페이지에는 검색 트래픽 분석 페이지에 이 원격 분석 패턴을 따르기 위한 치트 시트가 포함되어 있습니다. 이 페이지에서 Application Insights 리소스를 선택하거나 만들고, 계측 키를 얻고, 솔루션에 맞게 조정할 수 있는 조각 조각을 복사하고, 패턴에 반영된 스키마를 통해 빌드된 Power BI 보고서를 다운로드할 수 있습니다.

![포털에서 트래픽 분석 페이지 검색](media/search-traffic-analytics/azuresearch-trafficanalytics.png "포털에서 트래픽 분석 페이지 검색")

## <a name="1---set-up-application-insights"></a>1 - 애플리케이션 인사이트 설정

기존 Application Insights 리소스를 선택하거나 아직 없는 경우 [리소스를 만듭니다.](https://docs.microsoft.com/azure/azure-monitor/app/create-new-resource) 검색 트래픽 분석 페이지를 사용하는 경우 애플리케이션이 애플리케이션 인사이트에 연결하는 데 필요한 계측 키를 복사할 수 있습니다.

Application Insights 리소스가 있으면 [지원되는 언어 및 플랫폼에 대한 지침을](https://docs.microsoft.com/azure/azure-monitor/app/platforms) 따라 앱을 등록할 수 있습니다. 등록은 단순히 연결 집합코드에 Application Insights의 계측 키를 추가하는 것입니다. 기존 리소스를 선택할 때 포털 또는 검색 트래픽 분석 페이지에서 키를 찾을 수 있습니다.

일부 Visual Studio 프로젝트 유형에 대해 작동하는 바로 가기는 다음 단계에 반영됩니다. 리소스를 만들고 몇 번의 클릭만으로 앱을 등록합니다.

1. Visual Studio 및 ASP.NET 개발의 경우 솔루션을 열고 **프로젝트** > **응용 프로그램 인사이트 원격 분석을**선택합니다.

1. **시작**을 클릭합니다.

1. Microsoft 계정, Azure 구독 및 응용 프로그램 인사이트 리소스(새 리소스가 기본값)를 제공하여 앱을 등록합니다. **등록**을 클릭합니다.

이 시점에서 응용 프로그램은 응용 프로그램 모니터링을 위해 설정되므로 모든 페이지 로드가 기본 메트릭으로 추적됩니다. 이전 단계에 대한 자세한 내용은 [응용 프로그램 인사이트 서버 측 원격 분석 활성화를](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core#enable-application-insights-server-side-telemetry-visual-studio)참조하십시오.

## <a name="2---add-instrumentation"></a>2 - 계측 추가

이 단계는 위의 단계에서 만든 Application Insights 리소스를 사용하여 사용자 고유의 검색 응용 프로그램을 계측하는 위치입니다. 원격 분석 클라이언트를 만드는 것부터 시작하여 이 프로세스에는 네 단계가 있습니다.

### <a name="step-1-create-a-telemetry-client"></a>1단계: 원격 분석 클라이언트 만들기

응용 프로그램 인사이트로 이벤트를 보내는 개체를 만듭니다. 브라우저에서 실행되는 서버 측 응용 프로그램 코드 또는 클라이언트 측 코드에 계측을 추가할 수 있으며 여기에 C# 및 JavaScript 변형으로 표시되어 있습니다(다른 언어의 경우 [지원되는 플랫폼 및 프레임워크의](https://docs.microsoft.com/azure/application-insights/app-insights-platforms)전체 목록을 참조하세요. 원하는 깊이의 정보를 제공하는 접근 방식을 선택합니다.

서버 측 원격 분석은 클라우드에서 웹 서비스로 실행되는 응용 프로그램 또는 회사 네트워크의 온-프레미스 앱과 같은 응용 프로그램 계층에서 메트릭을 캡처합니다. 서버 측 원격 분석캡처는 검색 및 클릭 이벤트, 결과에서 문서의 위치 및 쿼리 정보를 캡처하지만 데이터 수집은 해당 계층에서 사용할 수 있는 모든 정보로 범위가 지정됩니다.

클라이언트에서 쿼리 입력을 조작하거나 탐색을 추가하거나 컨텍스트(예: 홈 페이지에서 시작된 쿼리와 제품 페이지)를 포함하는 추가 코드가 있을 수 있습니다. 솔루션에 대해 설명하는 경우 원격 분석이 추가 세부 정보를 반영하도록 클라이언트 측 계측을 선택할 수 있습니다. 이 추가 세부 정보를 수집하는 방법은 이 패턴의 범위를 벗어나지만 [웹 페이지에 대한 응용 프로그램 인사이트를](https://docs.microsoft.com/azure/azure-monitor/app/javascript#explore-browserclient-side-data) 검토하여 자세한 방향을 확인할 수 있습니다. 

**C# 사용**

C#의 경우 프로젝트가 ASP.NET 경우 appsettings.json과 같은 응용 프로그램 구성에서 **계측 키가** 있습니다. 키 위치를 잘 모르는 경우 등록 지침을 다시 참조하십시오.

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

### <a name="step-2-request-a-search-id-for-correlation"></a>2단계: 상관관계에 대한 검색 ID 요청

검색 요청을 클릭수와 상호 연결하려면 이러한 두 가지 이벤트를 포함하는 상관 관계 ID가 있어야 합니다. Azure Cognitive Search는 HTTP 헤더로 요청할 때 검색 ID를 제공합니다.

검색 ID를 사용하면 응용 프로그램 인사이트에 로그인하는 사용자 지정 메트릭과 함께 요청 자체에 대해 Azure Cognitive Search에서 내보내는 메트릭의 상관 관계를 사용할 수 있습니다.  

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

**자바 스크립트 사용 (나머지 API 호출)**

```javascript
request.setRequestHeader("x-ms-azs-return-searchid", "true");
request.setRequestHeader("Access-Control-Expose-Headers", "x-ms-azs-searchid");
var searchId = request.getResponseHeader('x-ms-azs-searchid');
```

### <a name="step-3-log-search-events"></a>3단계: 로그 검색 이벤트

사용자가 검색 요청을 발행할 때마다 Application Insights 사용자 지정 이벤트에서 다음 스키마가 있는 검색 이벤트로 기록해야 합니다. 사용자가 생성한 검색 쿼리만 기록해야 합니다.

+ **검색 서비스 이름**: (문자열) 검색 서비스 이름
+ **SearchId**: 검색 쿼리의 고유 식별자(guid) (검색 응답에 표시)
+ **인덱스 이름**: 쿼리할 검색 서비스 인덱스(문자열)
+ **쿼리 용어**: (문자열) 사용자가 입력 한 검색어
+ **ResultCount**: (int) 반환된 문서 수(검색 응답에 있음)
+ **점수 매기기 프로필**: 사용된 점수 매기기 프로필의 이름(있는 경우)

> [!NOTE]
> 검색 쿼리에 $count=true를 추가하여 사용자 생성 쿼리 수를 요청합니다. 자세한 내용은 [검색 문서(REST)를](/rest/api/searchservice/search-documents#counttrue--false)참조하십시오.
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

### <a name="step-4-log-click-events"></a>4단계: 클릭 로그 이벤트

사용자가 문서를 클릭할 때마다 검색 분석을 위해 신호를 기록해야 합니다. Application Insights 사용자 지정 이벤트를 사용하여 다음 스키마로 이러한 이벤트를 기록합니다.

+ **서비스 이름**: (문자열) 검색 서비스 이름
+ **SearchId**: 관련 검색 쿼리의 고유 식별자(guid)
+ **DocId**: (문자열) 문서 식별자
+ **위치**: 검색 결과 페이지에서 문서의 순위(int)

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

앱을 계측하고 응용 프로그램이 애플리케이션 인사이트에 올바르게 연결되어 있는지 확인한 후 미리 정의된 보고서 템플릿을 다운로드하여 Power BI 데스크톱에서 데이터를 분석합니다. 보고서에는 검색 트래픽 분석을 위해 캡처된 추가 데이터를 분석하는 데 유용한 미리 정의된 차트와 표가 포함되어 있습니다.

1. Azure 인지 검색 대시보드 왼쪽 탐색 창에서 **설정에서** **트래픽 분석 검색을**클릭합니다.

1. **트래픽 분석 검색** 페이지의 3단계에서 **Power BI Desktop 가져오기**를 클릭하여 Power BI를 설치합니다.

   ![Power BI 보고서 받기](./media/search-traffic-analytics/get-use-power-bi.png "Power BI 보고서 받기")

1. 동일한 페이지에서 Power **BI 보고서 다운로드를**클릭합니다.

1. 보고서는 Power BI 데스크톱에서 열리며 응용 프로그램 인사이트에 연결하고 자격 증명을 제공하라는 메시지가 표시됩니다. 응용 프로그램 인사이트 리소스에 대한 Azure 포털 페이지에서 연결 정보를 찾을 수 있습니다. 자격 증명의 경우 포털 로그인에 사용하는 것과 동일한 사용자 이름과 암호를 제공합니다.

   ![Application Insights에 연결](./media/search-traffic-analytics/connect-to-app-insights.png "Application Insights에 연결")

1. **로드를 클릭합니다.**

이 보고서에는 검색 성능과 관련성을 향상시키기 위해 정보에 기반한 합리적 의사 결정을 내리는 데 도움이 되는 차트와 테이블이 포함되어 있습니다.

메트릭에 포함되는 항목은 다음과 같습니다.

+ 검색 량 및 가장 인기 있는 용어 문서 쌍: 클릭한 동일한 문서를 클릭하고 클릭순으로 정렬하는 용어입니다.
+ 클릭 없이 검색: 클릭을 등록하지 않는 상위 쿼리를 일컫는 용어

다음 스크린샷은 모든 스키마 요소를 사용한 경우 기본 제공 보고서가 어떻게 보일지 보여 주었습니다.

![Azure 인지 검색을 위한 전원 BI 대시보드](./media/search-traffic-analytics/azuresearch-powerbi-dashboard.png "Azure 인지 검색을 위한 전원 BI 대시보드")

## <a name="next-steps"></a>다음 단계

검색 애플리케이션을 계측하여 검색 서비스에 대한 강력하고 통찰력 있는 데이터를 가져옵니다.

[Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview)에 대한 자세한 내용을 확인하고, [가격 페이지](https://azure.microsoft.com/pricing/details/application-insights/)를 방문하여 다양한 각 서비스 계층에 대해 자세히 알아볼 수 있습니다.

놀라운 보고서 만들기에 대해 자세히 알아보세요. 자세한 내용은 [Power BI 데스크톱을 시작하기](https://powerbi.microsoft.com/documentation/powerbi-desktop-getting-started/) 를 참조하십시오.