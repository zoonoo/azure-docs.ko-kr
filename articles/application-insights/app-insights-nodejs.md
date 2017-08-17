---
title: "Azure Application Insights를 사용하여 Node.js 서비스 모니터링 | Microsoft Docs"
description: "Application Insights를 사용하여 Node.js 서비스의 성능을 모니터링하고 문제를 진단합니다."
services: application-insights
documentationcenter: nodejs
author: joshgav
manager: carmonm
ms.assetid: 2ec7f809-5e1a-41cf-9fcd-d0ed4bebd08c
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: get-started-article
ms.date: 05/01/2017
ms.author: bwren
ms.translationtype: Human Translation
ms.sourcegitcommit: 64bd7f356673b385581c8060b17cba721d0cf8e3
ms.openlocfilehash: 76a8025cd2a67533beb321c88e924517c1977dfc
ms.contentlocale: ko-kr
ms.lasthandoff: 05/02/2017

---

# <a name="monitor-your-nodejs-services-and-apps-with-application-insights"></a>Application Insights를 사용하여 Node.js 서비스 및 앱 모니터링

백 엔드 서비스 및 구성 요소를 배포한 후 [Azure Application Insights](app-insights-overview.md)로 모니터링하여 [성능 및 기타 문제를 신속하게 발견하고 진단](app-insights-detect-triage-diagnose.md)할 수 있습니다. 데이터 센터, Azure VM, Web Apps, 기타 공용 클라우드 등 어느 위치에 호스트된 Node.js 서비스에도 사용할 수 있습니다.

모니터링 데이터를 수신, 저장 및 탐색하려면 다음 지침에 따라 코드에 에이전트를 포함하고 Azure에서 해당 Application Insights 리소스를 설정합니다. 에이전트는 추가 분석 및 탐색을 위해 해당 리소스로 데이터를 보냅니다.

Node.js 에이전트는 들어오고 나가는 HTTP 요청, 여러 시스템 메트릭 및 예외를 자동으로 모니터링할 수 있습니다. v0.20부터는 `mongodb`, `mysql`, `redis` 등의 일반적인 타사 패키지도 모니터링할 수 있습니다. 들어오는 HTTP 요청과 관련된 모든 이벤트는 좀 더 빠른 문제 해결을 위해 상호 관계가 지정됩니다.

뒷부분에서 설명할 에이전트 API를 사용하여 앱과 시스템을 수동으로 계측하면 앱과 시스템의 더 많은 요소를 모니터링할 수 있습니다.

![예제 성능 모니터링 차트](./media/app-insights-nodejs/10-perf.png)

## <a name="getting-started"></a>시작하기

앱 또는 서비스 모니터링을 설정하는 단계를 살펴보겠습니다.

### <a name="resource"></a> App Insights 리소스 설정

**시작하기 전에** Azure 구독이 있는지 확인하거나 [무료 계정을 새로 만듭니다][azure-free-offer]. 조직에 이미 Azure 구독이 있으면 관리자가 [다음 지침][add-aad-user]에 따라 사용자를 구독에 추가할 수 있습니다.

[azure-free-offer]: https://azure.microsoft.com/en-us/free/
[add-aad-user]: https://docs.microsoft.com/en-us/azure/active-directory/active-directory-users-create-azure-portal

[Azure Portal][portal]에 로그인하고 다음 그림처럼 "새로 만들기" > "개발자 도구" > "Application Insights"를 클릭하여 Application Insights 리소스를 만듭니다. 리소스에는 원격 분석 데이터를 수신하기 위한 끝점, 이 데이터 저장소, 저장된 보고서 및 대시보드, 규칙 및 경고 구성이 포함됩니다.

![App Insights 리소스 만들기](./media/app-insights-nodejs/03-new_appinsights_resource.png)

리소스 만들기 페이지의 응용 프로그램 유형 드롭다운에서 "Node.js 응용 프로그램"을 선택합니다. 앱 유형에 따라 생성되는 기본 대시보드 및 보고서 집합이 결정됩니다. 모든 App Insights 리소스는 모든 언어 및 플랫폼에서 데이터를 수집할 수 있으므로 걱정할 필요는 없습니다.

![새로운 App Insights 리소스 형식](./media/app-insights-nodejs/04-create_appinsights_resource.png)

### <a name="agent"></a> Node.js 에이전트 설정

이제 에이전트가 데이터를 수집할 수 있도록 앱에 에이전트를 포함해야 합니다.
먼저 아래와 같이 리소스의 계측 키(이하 `ikey`)를 복사합니다. App Insights 시스템은 이 키를 사용하여 데이터를 Azure 리소스로 매핑하므로 환경 변수 또는 코드에서 에이전트가 사용할 키를 지정해야 합니다.  

![계측 키 복사](./media/app-insights-nodejs/05-appinsights_ikey_portal.png)

다음으로 package.json을 통해 Node.js 에이전트 라이브러리를 앱의 종속성에 추가합니다. 앱의 루트 폴더에서 다음을 실행합니다.

```bash
npm install applicationinsights --save
```

이제 코드에서 라이브러리를 명시적으로 로드해야 합니다. 에이전트는 여러 다른 라이브러리에 계측 값을 삽입합니다. 따라서 최대한 신속하게, 심지어 다른 `require` 문보다도 먼저 로드해야 합니다. 시작하려면 첫 번째 .js 파일의 맨 위에 다음을 추가합니다.

```javascript
const appInsights = require("applicationinsights");
appInsights.setup("<instrumentation_key>");
appInsights.start();
```

`setup` 메서드는 기본적으로 모든 추적 항목에 사용할 계측 키(따라서 Azure 리소스까지)를 구성합니다. 구성 후 `start` 호출이 완료되고 원격 분석 데이터의 수집 및 송신이 시작됩니다.

ikey를 `setup()` 또는 `getClient()`에 수동으로 전달하는 대신 APPINSIGHTS\_INSTRUMENTATIONKEY 환경 변수를 통해 제공할 수도 있습니다. 이렇게 하면 ikey가 커밋된 소스 코드의 영향을 받지 않으며 다른 환경에 다른 ikey를 지정할 수 있습니다.

추가 구성 옵션은 아래에 설명되어 있습니다.

계측 키를 비어 있지 않은 문자열로 설정하면 원격 분석을 전송하지 않고도 에이전트를 사용해 볼 수 있습니다.

### <a name="monitor"></a> 앱 모니터링

에이전트는 Node.js 런타임 및 일부 일반적인 타사 모듈에 대한 원격 분석 데이터를 자동으로 수집합니다. 이제 응용 프로그램을 사용하여 이 데이터를 생성합니다.

그런 후 다음 그림과 같이 [Azure Portal][portal]에서 이전에 만든 Application Insights 리소스를 찾고, 개요 타임 라인에서 첫 번째 데이터 요소를 찾습니다. 자세한 내용을 보려면 차트를 클릭합니다.

![첫 번째 데이터 요소](./media/app-insights-nodejs/12-first-perf.png)

다음 그림과 같이 응용 프로그램 맵 단추를 클릭하여 앱에 대해 검색된 토폴로지를 봅니다. 자세한 내용을 보려면 맵에서 구성 요소를 클릭합니다.

![간단한 앱 맵](./media/app-insights-nodejs/06-appinsights_appmap.png)

"조사" 섹션에 제공되는 다른 보기를 사용하여 앱에 대해 자세히 알아보고 문제를 해결하세요.

![조사 섹션](./media/app-insights-nodejs/07-appinsights_investigate_blades.png)

#### <a name="no-data"></a>데이터가 없나요?

에이전트는 제출할 데이터를 일괄 처리하기 때문에 항목이 포털에 표시될 때까지 지연이 있을 수 있습니다. 리소스에 데이터가 보이지 않으면 다음 해결 방법 중 일부를 시도해 보세요.

* 응용 프로그램을 좀 더 사용하고 원격 분석을 생성하는 작업을 좀 더 수행합니다.
* 포털 리소스 보기에서 **새로 고침**을 클릭합니다. 차트가 주기적으로 자동 새로 고침되지만 새로 고침을 클릭하면 즉시 새로 고쳐집니다.
* [필요한 발신 포트](app-insights-ip-addresses.md)가 열려 있는지 확인합니다.
* [검색](app-insights-diagnostic-search.md) 타일을 열고 개별 이벤트를 검색합니다.
* [FAQ][]를 확인합니다.


## <a name="agent-configuration"></a>에이전트 구성

다음은 에이전트의 구성 메서드와 해당 기본값입니다.

서비스에서 이벤트의 상관 관계를 완전하게 지정하려면 `.setAutoDependencyCorrelation(true)`을 설정합니다. 이렇게 하면 에이전트가 Node.js의 비동기 콜백에서 컨텍스트를 추적할 수 있습니다.

```javascript
const appInsights = require("applicationinsights");
appInsights.setup("<instrumentation_key>")
    .setAutoDependencyCorrelation(false)
    .setAutoCollectRequests(true)
    .setAutoCollectPerformance(true)
    .setAutoCollectExceptions(true)
    .setAutoCollectDependencies(true)
    .start();
```

## <a name="agent-api"></a>에이전트 API

<!-- TODO: Fully document agent API. -->

.NET 에이전트 API는 [여기](app-insights-api-custom-events-metrics.md)에 자세히 설명되어 있습니다.

Application Insights Node.js 클라이언트를 사용하여 모든 요청, 이벤트, 메트릭 또는 예외를 추적할 수 있습니다. 다음 예제에서는 사용 가능한 API의 일부를 보여 줍니다.

```javascript
let appInsights = require("applicationinsights");
appInsights.setup().start(); // assuming ikey in env var
let client = appInsights.getClient();

client.trackEvent("my custom event", {customProperty: "custom property value"});
client.trackException(new Error("handled exceptions can be logged with this method"));
client.trackMetric("custom metric", 3);
client.trackTrace("trace message");

let http = require("http");
http.createServer( (req, res) => {
  client.trackRequest(req, res); // Place at the beginning of your request handler
});
```

### <a name="track-your-dependencies"></a>종속성 추적

```javascript
let appInsights = require("applicationinsights");
let client = appInsights.getClient();

var success = false;
let startTime = Date.now();
// execute dependency call here....
let duration = Date.now() - startTime;
success = true;

client.trackDependency("dependency name", "command name", duration, success);
```

### <a name="add-a-custom-property-to-all-events"></a>모든 이벤트에 사용자 지정 속성 추가

```javascript
appInsights.client.commonProperties = {
    environment: process.env.SOME_ENV_VARIABLE
};
```

### <a name="track-http-get-requests"></a>HTTP GET 요청 추적

```javascript
var server = http.createServer((req, res) => {
    if ( req.method === "GET" ) {
            appInsights.client.trackRequest(req, res);
    }
    // other work here....
    res.end();
});
```

### <a name="track-server-startup-time"></a>서버 시작 시간 추적

```javascript
let start = Date.now();
server.on("listening", () => {
    let duration = Date.now() - start;
    appInsights.client.trackMetric("server startup time", duration);
});
```

## <a name="more-resources"></a>추가 리소스

* [포털에서 원격 분석 모니터링](app-insights-dashboards.md)
* [원격 분석에 분석 쿼리 작성](app-insights-analytics-tour.md)

<!--references-->

[portal]: https://portal.azure.com/
[FAQ]: app-insights-troubleshoot-faq.md

