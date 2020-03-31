---
title: Azure Application Insights 원격 분석 상관 관계 | Microsoft 문서
description: Application Insights 원격 분석 상관 관계
ms.topic: conceptual
author: lgayhardt
ms.author: lagayhar
ms.date: 06/07/2019
ms.reviewer: sergkanz
ms.openlocfilehash: 06897fffda490cdfcbb2a9cf6f55c7945e8afda0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79276128"
---
# <a name="telemetry-correlation-in-application-insights"></a>Application Insights의 원격 분석 상관 관계

마이크로 서비스의 세계에서 모든 논리 작업은 서비스의 다양한 구성 요소에서 수행되어야 합니다. [응용 프로그램 인사이트를](../../azure-monitor/app/app-insights-overview.md)사용하여 이러한 각 구성 요소를 별도로 모니터링할 수 있습니다. Application Insights는 오류 또는 성능 저하를 일으키는 구성 요소를 검색하는 데 사용하는 분산된 원격 분석 상관 관계를 지원합니다.

이 문서에서는 여러 구성 요소에서 보낸 원격 분석의 상관 관계를 지정하기 위해 Application Insights에서 사용되는 데이터 모델에 대해 설명합니다. 컨텍스트 전파 기술 및 프로토콜을 다룹니다. 또한 다른 언어와 플랫폼에서 상관 관계 전술의 구현을 다룹니다.

## <a name="data-model-for-telemetry-correlation"></a>원격 분석 상관 관계에 대한 데이터 모델

Application Insights는 분산 원격 분석 상관 관계에 대한 [데이터 모델](../../azure-monitor/app/data-model.md)을 정의합니다. 원격 분석을 논리 작업과 연결하기 위해 모든 원격 분석 `operation_Id`항목에는 컨텍스트 필드라는 컨텍스트 필드가 있습니다. 이 식별자는 분산 추적의 모든 원격 분석 항목에서 공유됩니다. 따라서 단일 계층에서 원격 분석을 잃어도 다른 구성 요소에서 보고한 원격 분석을 연결할 수 있습니다.

분산 논리 작업은 일반적으로 구성 요소 중 하나에서 처리되는 요청을 처리하는 작은 작업 집합으로 구성됩니다. 이러한 작업은 [요청 원격 분석](../../azure-monitor/app/data-model-request-telemetry.md)에서 정의됩니다. 모든 요청 원격 분석 항목에는 고유하고 전역적으로 식별되는 고유한 `id` 요청이 있습니다. 또한 요청과 연결된 모든 원격 분석 항목(예: 추적 및 예외)은 요청 `operation_parentId` `id`값을 로 설정해야 합니다.

모든 나가는 작업(예: 다른 구성 요소에 대한 HTTP 호출)은 [종속성 원격 분석](../../azure-monitor/app/data-model-dependency-telemetry.md)으로 표시됩니다. 종속성 원격 분석도 전역적으로 `id` 고유한 자체 를 정의합니다. 이 종속성 호출을 통해 시작된 요청 원격 분석은 이 `id`를 `operation_parentId`로 사용합니다.

`operation_Id`, `operation_parentId` 및 `request.id`를 `dependency.id`와 함께 사용하여 분산 논리 작업의 보기를 빌드할 수 있습니다. 또한 이러한 필드는 원격 분석 호출의 인과 관계 순서를 정의합니다.

마이크로 서비스 환경에서 구성 요소의 추적은 다른 스토리지 항목으로 이동할 수 있습니다. 모든 구성 요소에는 자체의 계측 키가 Application Insights에 있을 수 있습니다. 논리적 작업에 대한 원격 분석을 얻기 위해 Application Insights는 모든 저장소 항목에서 데이터를 쿼리합니다. 저장소 항목 수가 많으면 다음에 볼 위치에 대한 힌트가 필요합니다. Application Insights 데이터 모델에서는 이 문제를 해결하기 위해 두 가지 필드, 즉 `request.source` 및 `dependency.target`을 정의합니다. 첫 번째 필드는 종속성 요청을 시작한 구성 요소를 식별합니다. 두 번째 필드는 종속성 호출의 응답을 반환하는 구성 요소를 식별합니다.

## <a name="example"></a>예제

예제를 살펴보겠습니다. 주식 가격이라는 응용 프로그램은 주식이라는 외부 API를 사용하여 주식의 현재 시장 가격을 보여줍니다. 주식 가격 응용 프로그램에는 클라이언트 웹 브라우저를 사용하여 `GET /Home/Stock`여는 Stock 페이지라는 페이지가 있습니다. 응용 프로그램은 HTTP 호출을 `GET /api/stock/value`사용하여 Stock API를 쿼리합니다.

쿼리를 실행하여 결과 원격 분석을 분석할 수 있습니다.

```kusto
(requests | union dependencies | union pageViews)
| where operation_Id == "STYz"
| project timestamp, itemType, name, id, operation_ParentId, operation_Id
```

결과에서 모든 원격 분석 항목은 루트 `operation_Id`를 공유합니다. 페이지에서 Ajax 호출이 이루어지면 종속성 원격`qJSXU`분석에 새 고유 ID()가 할당되고 pageView의 ID가 `operation_ParentId`로 사용됩니다. 그러면 서버 요청에서 Ajax ID를 `operation_ParentId`로 사용합니다.

| itemType   | name                      | ID           | operation_ParentId | operation_Id |
|------------|---------------------------|--------------|--------------------|--------------|
| pageView   | Stock page                |              | STYz               | STYz         |
| dependency | GET /Home/Stock           | qJSXU        | STYz               | STYz         |
| request    | GET Home/Stock            | KqKwlrSt9PA= | qJSXU              | STYz         |
| dependency | GET /api/stock/value      | bBrf2L7mm2g= | KqKwlrSt9PA=       | STYz         |

외부 서비스에 `GET /api/stock/value` 대한 호출이 이루어지면 필드를 적절하게 설정할 수 있도록 해당 `dependency.target` 서버의 ID를 알아야 합니다. 외부 서비스에서 모니터링을 지원하지 않는 경우 `target`은 서비스의 호스트 이름으로 설정됩니다(예: `stock-prices-api.com`). 그러나 서비스가 미리 정의된 HTTP 헤더를 반환하여 `target` 자신을 식별하는 경우 응용 프로그램 인사이트가 해당 서비스에서 원격 분석을 쿼리하여 분산 추적을 빌드할 수 있는 서비스 ID가 포함되어 있습니다.

## <a name="correlation-headers"></a>상관 관계 헤더

응용 프로그램 인사이트는 다음을 정의하는 [W3C 추적 컨텍스트로](https://w3c.github.io/trace-context/)전환됩니다.

- `traceparent`: 전역고유의 작업 ID와 호출의 고유 식별자를 전달합니다.
- `tracestate`: 시스템별 추적 컨텍스트를 전달합니다.

응용 프로그램 인사이트 SDK의 최신 버전은 추적 컨텍스트 프로토콜을 지원하지만 이를 옵트인해야 할 수도 있습니다. 응용 프로그램 인사이트 SDK에서 지원하는 이전 상관 관계 프로토콜과의 이전 호환성이 유지됩니다.

[요청 ID라고도 하는 상관 관계 HTTP 프로토콜이](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/HttpCorrelationProtocol.md)더 이상 사용되지 않습니다. 이 프로토콜은 두 개의 헤더를 정의합니다.

- `Request-Id`: 통화의 전역 고유 ID를 전달합니다.
- `Correlation-Context`: 분산 추적 속성의 이름 값 쌍 컬렉션을 전달합니다.

또한 응용 프로그램 인사이트는 상관 관계 HTTP 프로토콜에 대한 [확장을](https://github.com/lmolkova/correlation/blob/master/http_protocol_proposal_v2.md) 정의합니다. `Request-Context` 이름-값 쌍을 사용하여 즉각적인 호출자 또는 호출 수신자에서 사용하는 속성의 컬렉션을 전파합니다. 응용 프로그램 인사이트 SDK는 `dependency.target` 이 `request.source` 헤더를 사용하여 및 필드를 설정합니다.

### <a name="enable-w3c-distributed-tracing-support-for-classic-aspnet-apps"></a>클래식 ASP.NET 앱에 W3C 분산 추적 지원을 사용하도록 설정
 
  > [!NOTE]
  >  부터 `Microsoft.ApplicationInsights.Web` 구성이 `Microsoft.ApplicationInsights.DependencyCollector`필요하지 않습니다.

W3C 추적 컨텍스트 지원은 이전 버전과 호환되는 방식으로 구현됩니다. 상관 관계는 이전 버전의 SDK와 함께 계측된 응용 프로그램과 함께 작동할 것으로 예상됩니다(W3C 지원 제외).

레거시 `Request-Id` 프로토콜을 계속 사용하려면 다음 구성을 사용하여 추적 컨텍스트를 사용하지 않도록 설정할 수 있습니다.

```csharp
  Activity.DefaultIdFormat = ActivityIdFormat.Hierarchical;
  Activity.ForceDefaultIdFormat = true;
```

이전 버전의 SDK를 실행하는 경우 추적 컨텍스트를 사용하도록 설정하려면 업데이트하거나 다음 구성을 적용하는 것이 좋습니다.
이 기능은 버전 `Microsoft.ApplicationInsights.Web` 2.8.0-beta1부터 패키지및 `Microsoft.ApplicationInsights.DependencyCollector` 패키지에서 사용할 수 있습니다.
기본적으로 사용하지 않도록 설정되어 있습니다. 이를 사용하려면 다음을 `ApplicationInsights.config`변경합니다.

- 에서 `RequestTrackingTelemetryModule` `EnableW3CHeadersExtraction` 요소는 추가하고 해당 값을 `true`에 설정합니다.
- 에서 `DependencyTrackingTelemetryModule` `EnableW3CHeadersInjection` 요소는 추가하고 해당 값을 `true`에 설정합니다.
- 아래에 `W3COperationCorrelationTelemetryInitializer` `TelemetryInitializers`추가합니다. 이 예제와 유사하게 보입니다.

```xml
<TelemetryInitializers>
  <Add Type="Microsoft.ApplicationInsights.Extensibility.W3C.W3COperationCorrelationTelemetryInitializer, Microsoft.ApplicationInsights"/>
   ...
</TelemetryInitializers>
```

### <a name="enable-w3c-distributed-tracing-support-for-aspnet-core-apps"></a>ASP.NET Core 앱에 W3C 분산 추적 지원을 사용하도록 설정

 > [!NOTE]
  > 버전 `Microsoft.ApplicationInsights.AspNetCore` 2.8.0부터는 구성이 필요하지 않습니다.
 
W3C 추적 컨텍스트 지원은 이전 버전과 호환되는 방식으로 구현됩니다. 상관 관계는 이전 버전의 SDK와 함께 계측된 응용 프로그램과 함께 작동할 것으로 예상됩니다(W3C 지원 제외).

레거시 `Request-Id` 프로토콜을 계속 사용하려면 다음 구성을 사용하여 추적 컨텍스트를 사용하지 않도록 설정할 수 있습니다.

```csharp
  Activity.DefaultIdFormat = ActivityIdFormat.Hierarchical;
  Activity.ForceDefaultIdFormat = true;
```

이전 버전의 SDK를 실행하는 경우 추적 컨텍스트를 사용하도록 설정하려면 업데이트하거나 다음 구성을 적용하는 것이 좋습니다.

이 기능은 `Microsoft.ApplicationInsights.AspNetCore` 버전 2.5.0-beta1 및 `Microsoft.ApplicationInsights.DependencyCollector` 버전 2.8.0-beta1에 있으며,
기본적으로 사용하지 않도록 설정되어 있습니다. 사용하도록 설정하려면 `ApplicationInsightsServiceOptions.RequestCollectionOptions.EnableW3CDistributedTracing`을 `true`로 설정합니다.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddApplicationInsightsTelemetry(o => 
        o.RequestCollectionOptions.EnableW3CDistributedTracing = true );
    // ....
}
```

### <a name="enable-w3c-distributed-tracing-support-for-java-apps"></a>Java 앱에 W3C 분산 추적 지원을 사용하도록 설정

- **들어오는 구성**

  - Java EE 앱의 경우 ApplicationInsights.xml의 태그에 다음을 `<TelemetryModules>` 추가합니다.

    ```xml
    <Add type="com.microsoft.applicationinsights.web.extensibility.modules.WebRequestTrackingTelemetryModule>
       <Param name = "W3CEnabled" value ="true"/>
       <Param name ="enableW3CBackCompat" value = "true" />
    </Add>
    ```
    
  - 스프링 부팅 앱의 경우 다음 속성을 추가합니다.

    - `azure.application-insights.web.enable-W3C=true`
    - `azure.application-insights.web.enable-W3C-backcompat-mode=true`

- **나가는 구성**

  AI-Agent.xml에 다음을 추가합니다.

  ```xml
  <Instrumentation>
    <BuiltIn enabled="true">
      <HTTP enabled="true" W3C="true" enableW3CBackCompat="true"/>
    </BuiltIn>
  </Instrumentation>
  ```

  > [!NOTE]
  > 이전 버전과의 호환성 모드는 기본적으로 사용하도록 설정되어 있으며, `enableW3CBackCompat` 매개 변수는 선택 사항입니다. 이전 버전과의 호환성을 해제하려는 경우에만 사용하세요.
  >
  > 모든 서비스가 W3C 프로토콜을 지원하는 최신 버전의 SDK로 업데이트되었으면 이 기능을 해제하는 것이 가장 좋습니다. 가능한 한 빨리 최신 SDK로 이동하는 것이 좋습니다.

> [!IMPORTANT]
> 들어오는 구성과 나가는 구성이 정확히 동일한지 확인합니다.

### <a name="enable-w3c-distributed-tracing-support-for-web-apps"></a>웹 앱에 대한 W3C 분산 추적 지원 사용

이 기능은 `Microsoft.ApplicationInsights.JavaScript`에 있습니다. 기본적으로 사용하지 않도록 설정되어 있습니다. 이를 사용하려면 `distributedTracingMode` 구성을 사용합니다. AI_AND_W3C 애플리케이션 인사이트(Application Insights)에서 계측한 레거시 서비스와의 이전 버전과의 호환성을 위해 제공됩니다.

- **npm 설정(스니펫 설정을 사용하는 경우 무시)**

  ```javascript
  import { ApplicationInsights, DistributedTracingModes } from '@microsoft/applicationinsights-web';

  const appInsights = new ApplicationInsights({ config: {
    instrumentationKey: 'YOUR_INSTRUMENTATION_KEY_GOES_HERE',
    distributedTracingMode: DistributedTracingModes.W3C
    /* ...other configuration options... */
  } });
  appInsights.loadAppInsights();
  ```
  
- **스니펫 설정(npm 설정을 사용하는 경우 무시)**

  ```
  <script type="text/javascript">
  var sdkInstance="appInsightsSDK";window[sdkInstance]="appInsights";var aiName=window[sdkInstance],aisdk=window[aiName]||function(e){function n(e){i[e]=function(){var n=arguments;i.queue.push(function(){i[e].apply(i,n)})}}var i={config:e};i.initialize=!0;var a=document,t=window;setTimeout(function(){var n=a.createElement("script");n.src=e.url||"https://az416426.vo.msecnd.net/scripts/b/ai.2.min.js",a.getElementsByTagName("script")[0].parentNode.appendChild(n)});try{i.cookie=a.cookie}catch(e){}i.queue=[],i.version=2;for(var r=["Event","PageView","Exception","Trace","DependencyData","Metric","PageViewPerformance"];r.length;)n("track"+r.pop());n("startTrackPage"),n("stopTrackPage");var o="Track"+r[0];if(n("start"+o),n("stop"+o),!(!0===e.disableExceptionTracking||e.extensionConfig&&e.extensionConfig.ApplicationInsightsAnalytics&&!0===e.extensionConfig.ApplicationInsightsAnalytics.disableExceptionTracking)){n("_"+(r="onerror"));var s=t[r];t[r]=function(e,n,a,t,o){var c=s&&s(e,n,a,t,o);return!0!==c&&i["_"+r]({message:e,url:n,lineNumber:a,columnNumber:t,error:o}),c},e.autoExceptionInstrumented=!0}return i}
  (
    {
      instrumentationKey:"INSTRUMENTATION_KEY",
      distributedTracingMode: 2 // DistributedTracingModes.W3C
      /* ...other configuration options... */
    }
  );
  window[aiName]=aisdk,aisdk.queue&&0===aisdk.queue.length&&aisdk.trackPageView({});
  </script>
  ```

## <a name="opentracing-and-application-insights"></a>OpenTracing 및 Application Insights

[OpenTracing 데이터 모델 사양](https://opentracing.io/)과 Application Insights 데이터 모델은 다음과 같은 방식으로 매핑됩니다.

| 애플리케이션 정보                  | OpenTracing                                       |
|------------------------------------   |-------------------------------------------------  |
| `Request`, `PageView`                 | `Span`(`span.kind = server` 사용)                  |
| `Dependency`                          | `Span`(`span.kind = client` 사용)                  |
| `Request` 및 `Dependency`의 `Id`    | `SpanId`                                          |
| `Operation_Id`                        | `TraceId`                                         |
| `Operation_ParentId`                  | `ChildOf` 유형의 `Reference`(상위 범위)   |

자세한 내용은 [응용 프로그램 인사이트 원격 분석 데이터 모델을](../../azure-monitor/app/data-model.md)참조하십시오.

OpenTracing 개념에 대한 정의는 OpenTracing [사양](https://github.com/opentracing/specification/blob/master/specification.md) 및 [의미 체계 규칙을](https://github.com/opentracing/specification/blob/master/semantic_conventions.md)참조하십시오.

## <a name="telemetry-correlation-in-opencensus-python"></a>OpenCensus 파이썬의 원격 분석 상관 관계

OpenCensus 파이썬은 앞서 설명한 `OpenTracing` 데이터 모델 사양을 따릅니다. 또한 구성없이 [W3C 추적 컨텍스트를](https://w3c.github.io/trace-context/) 지원합니다.

### <a name="incoming-request-correlation"></a>들어오는 요청 상관 관계

OpenCensus 파이썬은 들어오는 요청에서 요청 자체에서 생성된 범위까지 W3C 추적 컨텍스트 헤더를 상호 연관시다. OpenCensus는 플라스크, 장고 및 피라미드와 같은 인기있는 웹 응용 프로그램 프레임 워크에 대한 통합으로 자동으로이 작업을 수행합니다. W3C 추적 컨텍스트 헤더를 [올바른 형식으로](https://www.w3.org/TR/trace-context/#trace-context-http-headers-format) 채우고 요청과 함께 보내면 됩니다. 다음은 이를 보여 주는 샘플 Flask 응용 프로그램입니다.

```python
from flask import Flask
from opencensus.ext.azure.trace_exporter import AzureExporter
from opencensus.ext.flask.flask_middleware import FlaskMiddleware
from opencensus.trace.samplers import ProbabilitySampler

app = Flask(__name__)
middleware = FlaskMiddleware(
    app,
    exporter=AzureExporter(),
    sampler=ProbabilitySampler(rate=1.0),
)

@app.route('/')
def hello():
    return 'Hello World!'

if __name__ == '__main__':
    app.run(host='localhost', port=8080, threaded=True)
```

이 코드는 포트를 `8080`수신 대기하는 로컬 컴퓨터에서 샘플 Flask 응용 프로그램을 실행합니다. 추적 컨텍스트의 상관 관계를 지정하려면 요청을 끝점으로 보냅니다. 이 예제에서는 `curl` 다음 명령을 사용할 수 있습니다.
```
curl --header "traceparent: 00-4bf92f3577b34da6a3ce929d0e0e4736-00f067aa0ba902b7-01" localhost:8080
```
추적 컨텍스트 [헤더 형식을](https://www.w3.org/TR/trace-context/#trace-context-http-headers-format)보면 다음 정보를 도출할 수 있습니다.

`version`: `00`

`trace-id`: `4bf92f3577b34da6a3ce929d0e0e4736`

`parent-id/span-id`: `00f067aa0ba902b7`

`trace-flags`: `01`

Azure Monitor로 전송된 요청 항목을 보면 추적 헤더 정보가 채워진 필드를 볼 수 있습니다. 이 데이터는 Azure 모니터 응용 프로그램 인사이트 리소스에서 로그(분석)에서 찾을 수 있습니다.

![로그에서 원격 분석 요청(분석)](./media/opencensus-python/0011-correlation.png)

`id` 필드는 형식에 `<trace-id>.<span-id>`있으며 요청에서 `trace-id` 전달된 추적 헤더에서 가져온 필드는 이 `span-id` 범위에 대해 생성된 8바이트 배열입니다.

필드는 `operation_ParentId` 형식에 `<trace-id>.<parent-id>`있으며 요청에서 `trace-id` 전달된 `parent-id` 추적 헤더에서 필드와 를 모두 가져옵니다.

### <a name="log-correlation"></a>로그 상관관계

OpenCensus Python을 사용하면 레코드에 추적 ID, 범위 ID 및 샘플링 플래그를 추가하여 로그를 상호 연관시킬 수 있습니다. OpenCensus [로깅 통합을](https://pypi.org/project/opencensus-ext-logging/)설치하여 이러한 특성을 추가합니다. `LogRecord` 다음 특성은 Python 개체에 `traceId` `spanId` `traceSampled`추가됩니다. 이는 통합 후에 생성된 로거에 대해서만 적용됩니다.

다음은 이를 보여 주는 샘플 응용 프로그램입니다.

```python
import logging

from opencensus.trace import config_integration
from opencensus.trace.samplers import AlwaysOnSampler
from opencensus.trace.tracer import Tracer

config_integration.trace_integrations(['logging'])
logging.basicConfig(format='%(asctime)s traceId=%(traceId)s spanId=%(spanId)s %(message)s')
tracer = Tracer(sampler=AlwaysOnSampler())

logger = logging.getLogger(__name__)
logger.warning('Before the span')
with tracer.span(name='hello'):
    logger.warning('In the span')
logger.warning('After the span')
```
이 코드가 실행되면 콘솔에서 다음이 인쇄됩니다.
```
2019-10-17 11:25:59,382 traceId=c54cb1d4bbbec5864bf0917c64aeacdc spanId=0000000000000000 Before the span
2019-10-17 11:25:59,384 traceId=c54cb1d4bbbec5864bf0917c64aeacdc spanId=70da28f5a4831014 In the span
2019-10-17 11:25:59,385 traceId=c54cb1d4bbbec5864bf0917c64aeacdc spanId=0000000000000000 After the span
```
범위 내에 있는 `spanId` 로그 메시지에 대한 선물이 있습니다. 이 이름은 `spanId` 범위에 속하는 것과 `hello`동일합니다.

을 사용하여 `AzureLogHandler`로그 데이터를 내보낼 수 있습니다. 자세한 내용은 [이 문서를](https://docs.microsoft.com/azure/azure-monitor/app/opencensus-python#logs)참조하십시오.

## <a name="telemetry-correlation-in-net"></a>.NET의 원격 분석 상관 관계

시간이 지남에 따라 .NET은 원격 분석 및 진단 로그를 상호 연관시키는 여러 가지 방법을 정의했습니다.

- `System.Diagnostics.CorrelationManager`논리 작업 [스택 및 ActivityId의 추적을](https://msdn.microsoft.com/library/system.diagnostics.correlationmanager.aspx)허용 합니다.
- `System.Diagnostics.Tracing.EventSource` 및 ETW(Windows용 이벤트 추적)는 [SetCurrentThreadActivityId](https://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource.setcurrentthreadactivityid.aspx) 메서드를 정의합니다.
- `ILogger`[로그 범위를](https://docs.microsoft.com/aspnet/core/fundamentals/logging#log-scopes)사용합니다.
- WCF(Windows Communication Foundation) 및 HTTP는 "현재" 컨텍스트 전파를 연결합니다.

그러나 이러한 메서드는 자동 분산 추적 지원을 사용할 수 없습니다. `DiagnosticSource`자동 교차 시스템 상관 관계를 지원합니다. .NET 라이브러리는 HTTP와 같은 전송을 통해 상관 관계 컨텍스트의 자동 교차 시스템 전파를 지원하고 `DiagnosticSource` 허용합니다.

[활동 사용자 가이드에서](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/ActivityUserGuide.md) `DiagnosticSource` 추적 활동의 기본 사항에 대해 설명합니다.

ASP.NET Core 2.0은 HTTP 헤더 추출 및 새 작업을 지원합니다.

`System.Net.Http.HttpClient`에서는 버전 4.1.0부터 상관 관계 HTTP 헤더의 자동 삽입및 HTTP 호출을 활동으로 추적할 수 있습니다.

새로운 HTTP 모듈이 있다, [마이크로소프트.AspNet.TelemetryCorrelation,](https://www.nuget.org/packages/Microsoft.AspNet.TelemetryCorrelation/)클래식 ASP.NET 대 한. 이 모듈은 `DiagnosticSource`를 사용하여 원격 분석 상관 관계를 구현하며, 들어오는 요청 헤더에 기반한 활동을 시작합니다. 또한 IIS(인터넷 정보 서비스) 처리의 모든 단계가 다른 관리 스레드에서 실행되는 경우에도 요청 처리의 여러 단계에서 원격 분석을 상호 연결합니다.

Application Insights SDK는 버전 2.4.0-beta1부터 `DiagnosticSource` 및 `Activity`를 사용하여 원격 분석을 수집하고 현재 활동과 연결합니다.

<a name="java-correlation"></a>
## <a name="telemetry-correlation-in-the-java-sdk"></a>Java SDK의 원격 분석 상관 관계

Java 버전 2.0.0 [이상용 응용 프로그램 인사이트 SDK는](../../azure-monitor/app/java-get-started.md) 원격 분석의 자동 상관 관계를 지원합니다. 요청 범위 내에서 `operation_id` 발급된 모든 원격 분석(예: 추적, 예외 및 사용자 지정 이벤트)에 대해 자동으로 채워집니다. 또한 [Java SDK 에이전트가](../../azure-monitor/app/java-agent.md) 구성된 경우 HTTP를 통해 서비스 간 호출에 대한 상관 헤더(앞에 설명)를 전파합니다.

> [!NOTE]
> 아파치 HttpClient를 통해 이루어진 호출만 상관 관계 기능에 대해 지원됩니다. 스프링 레스트템플릿과 페이그 모두 후드 아래 아파치 HttpClient와 함께 사용할 수 있습니다.

현재 메시징 기술(예: Kafka, RabbitMQ 및 Azure Service Bus)에 대한 자동 컨텍스트 전파는 지원되지 않습니다. `trackDependency` 이러한 시나리오는 및 `trackRequest` 메서드를 사용하여 수동으로 코딩할 수 있습니다. 이러한 메서드에서 종속성 원격 분석 생산자에 의해 큐에 대기 되는 메시지를 나타냅니다. 요청은 소비자가 처리중인 메시지를 나타냅니다. 이 경우 `operation_id` 및 `operation_parentId` 둘 다 메시지의 속성에 전파되어야 합니다.

### <a name="telemetry-correlation-in-asynchronous-java-applications"></a>비동기 Java 응용 프로그램의 원격 분석 상관 관계

비동기 스프링 부팅 응용 프로그램에서 원격 분석을 상호 연관시키는 방법을 알아보려면 [비동기 Java 응용 프로그램에서 분산 추적을](https://github.com/Microsoft/ApplicationInsights-Java/wiki/Distributed-Tracing-in-Asynchronous-Java-Applications)참조하십시오. 이 문서에서는 스프링의 [스레드풀태스크익스커](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/scheduling/concurrent/ThreadPoolTaskExecutor.html) 및 [스레드풀태스크스케줄러를](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/scheduling/concurrent/ThreadPoolTaskScheduler.html)계측하기 위한 지침을 제공합니다.


<a name="java-role-name"></a>
## <a name="role-name"></a>역할 이름

구성 요소 이름이 [응용 프로그램 맵에](../../azure-monitor/app/app-map.md)표시되는 방식을 사용자 지정할 수 있습니다. 이렇게 하려면 다음 작업 중 `cloud_RoleName` 하나를 수행 하 여 수동으로 설정할 수 있습니다.

- 응용 프로그램 인사이트 Java SDK 2.5.0 `cloud_RoleName` 이상에서는 `<RoleName>` ApplicationInsights.xml 파일에 추가하여 다음을 지정할 수 있습니다.

  ```XML
  <?xml version="1.0" encoding="utf-8"?>
  <ApplicationInsights xmlns="http://schemas.microsoft.com/ApplicationInsights/2013/Settings" schemaVersion="2014-05-30">
     <InstrumentationKey>** Your instrumentation key **</InstrumentationKey>
     <RoleName>** Your role name **</RoleName>
     ...
  </ApplicationInsights>
  ```

- 응용 프로그램 인사이트 스프링 부팅 스타터와 함께 스프링 부팅을 사용하는 경우 application.properties 파일에서 응용 프로그램에 대한 사용자 지정 이름을 설정하기만 하면 됩니다.

  `spring.application.name=<name-of-app>`

  스프링 부팅 스타터는 속성에 대해 입력한 `spring.application.name` 값을 자동으로 할당합니다. `cloudRoleName`

## <a name="next-steps"></a>다음 단계

- [사용자 지정 원격 분석을](../../azure-monitor/app/api-custom-events-metrics.md)작성합니다.
- ASP.NET 코어 및 ASP.NET 고급 상관 관계 시나리오에 대 한 [사용자 지정 작업 추적](custom-operations-tracking.md)을 참조 하십시오.
- 다른 SDK의 [cloud_RoleName 설정](../../azure-monitor/app/app-map.md#set-cloud-role-name)에 대해 자세히 알아봅니다.
- Application Insights에서 마이크로 서비스의 모든 구성 요소를 온보딩합니다. [지원되는 플랫폼](../../azure-monitor/app/platforms.md)을 확인합니다.
- Application Insights 유형에 대한 [데이터 모델](../../azure-monitor/app/data-model.md)을 참조합니다.
- [원격 분석을 확장 및 필터링](../../azure-monitor/app/api-filtering-sampling.md)하는 방법을 알아봅니다.
- [Application Insights 구성 참조](configuration-with-applicationinsights-config.md)를 검토합니다.
