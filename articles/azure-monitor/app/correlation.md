---
title: Azure Application Insights 원격 분석 상관 관계 | Microsoft 문서
description: Application Insights 원격 분석 상관 관계
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: lgayhardt
ms.author: lagayhar
ms.date: 06/07/2019
ms.reviewer: sergkanz
ms.openlocfilehash: aa683e90a328e9525fa7d0a78981aa107818188a
ms.sourcegitcommit: 1bd2207c69a0c45076848a094292735faa012d22
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/21/2019
ms.locfileid: "72678182"
---
# <a name="telemetry-correlation-in-application-insights"></a>Application Insights의 원격 분석 상관 관계

마이크로 서비스의 세계에서 모든 논리 작업은 서비스의 다양한 구성 요소에서 수행되어야 합니다. 이러한 각 구성 요소는 [Azure Application Insights](../../azure-monitor/app/app-insights-overview.md)에서 개별적으로 모니터링할 수 있습니다. 웹앱 구성 요소는 인증 공급자 구성 요소와 통신하여 사용자 자격 증명의 유효성을 검사하고, API 구성 요소를 사용하여 시각화할 데이터를 가져옵니다. API 구성 요소는 다른 서비스의 데이터를 쿼리하고, 캐시 공급자 구성 요소를 사용하여 이 호출에 대해 청구 구성 요소에 알릴 수 있습니다. Application Insights는 오류 또는 성능 저하를 일으키는 구성 요소를 검색하는 데 사용하는 분산된 원격 분석 상관 관계를 지원합니다.

이 문서에서는 여러 구성 요소에서 보낸 원격 분석의 상관 관계를 지정하기 위해 Application Insights에서 사용되는 데이터 모델에 대해 설명합니다. 컨텍스트 전파 기술 및 프로토콜을 다룹니다. 또한 다른 언어와 플랫폼의 상관 관계 개념에 대한 구현도 다룹니다.

## <a name="data-model-for-telemetry-correlation"></a>원격 분석 상관 관계에 대한 데이터 모델

Application Insights는 분산 원격 분석 상관 관계에 대한 [데이터 모델](../../azure-monitor/app/data-model.md)을 정의합니다. 원격 분석을 논리 작업과 연결하기 위해 모든 원격 분석 항목에 `operation_Id`라는 컨텍스트 필드가 있습니다. 이 식별자는 분산 추적의 모든 원격 분석 항목에서 공유됩니다. 따라서 단일 계층에서 원격 분석이 손실된 경우에도 다른 구성 요소에서 보고한 원격 분석을 연결할 수 있습니다.

분산 논리 작업은 일반적으로 구성 요소 중 하나에서 처리되는 요청인 더 작은 작업의 집합으로 구성됩니다. 이러한 작업은 [요청 원격 분석](../../azure-monitor/app/data-model-request-telemetry.md)에서 정의됩니다. 모든 요청 원격 분석에는 글로벌로 고유하게 식별되는 자체의 `id`가 있습니다. 그리고 이 요청과 연결된 모든 원격 분석 항목(예: 추적 및 예외)에서 `operation_parentId`를 요청 `id`의 값으로 설정해야 합니다.

모든 나가는 작업(예: 다른 구성 요소에 대한 HTTP 호출)은 [종속성 원격 분석](../../azure-monitor/app/data-model-dependency-telemetry.md)으로 표시됩니다. 종속성 원격 분석은 전체적으로 고유한 자체 `id`도 정의합니다. 이 종속성 호출을 통해 시작된 요청 원격 분석은 이 `id`를 `operation_parentId`로 사용합니다.

`operation_Id`, `operation_parentId` 및 `request.id`를 `dependency.id`와 함께 사용하여 분산 논리 작업의 보기를 빌드할 수 있습니다. 또한 이러한 필드는 원격 분석 호출의 인과 관계 순서를 정의합니다.

마이크로 서비스 환경에서 구성 요소의 추적은 다른 스토리지 항목으로 이동할 수 있습니다. 모든 구성 요소에는 자체의 계측 키가 Application Insights에 있을 수 있습니다. 논리 작업에 대 한 원격 분석을 가져오기 위해 Application Insights UX는 모든 저장소 항목의 데이터를 쿼리 합니다. 스토리지 항목의 수가 많은 경우 다음 항목을 찾을 위치에 대한 힌트가 필요합니다. Application Insights 데이터 모델에서는 이 문제를 해결하기 위해 두 가지 필드, 즉 `request.source` 및 `dependency.target`을 정의합니다. 첫 번째 필드는 종속성 요청을 시작한 구성 요소를 식별하고, 두 번째 필드는 종속성 호출의 응답을 반환한 구성 요소를 식별합니다.

## <a name="example"></a>예제

`Stock`이라는 외부 API를 사용하여 주식의 현재 시가를 보여 주는 '주가(Stock Prices)'라는 애플리케이션의 예제를 살펴보겠습니다. 주가 애플리케이션에는 `GET /Home/Stock`을 사용하여 클라이언트 웹 브라우저에서 열리는 `Stock page` 페이지가 있습니다. 애플리케이션에서 `GET /api/stock/value` HTTP 호출을 사용하여 `Stock` API를 쿼리합니다.

쿼리를 실행하여 결과 원격 분석을 분석할 수 있습니다.

```kusto
(requests | union dependencies | union pageViews)
| where operation_Id == "STYz"
| project timestamp, itemType, name, id, operation_ParentId, operation_Id
```

결과에서 모든 원격 분석 항목은 루트 `operation_Id`를 공유합니다. 페이지에서 Ajax 호출이 수행되면 새 고유 ID(`qJSXU`)가 종속성 원격 분석에 할당되고 pageView의 ID가 `operation_ParentId`로 사용됩니다. 그러면 서버 요청에서 Ajax ID를 `operation_ParentId`로 사용합니다.

| itemType   | 이름                      | ID           | operation_ParentId | operation_Id |
|------------|---------------------------|--------------|--------------------|--------------|
| pageView   | Stock page                |              | STYz               | STYz         |
| dependency | GET /Home/Stock           | qJSXU        | STYz               | STYz         |
| request    | GET Home/Stock            | KqKwlrSt9PA= | qJSXU              | STYz         |
| dependency | GET /api/stock/value      | bBrf2L7mm2g= | KqKwlrSt9PA=       | STYz         |

외부 서비스에 대한 `GET /api/stock/value` 호출이 수행되면 `dependency.target` 필드를 적절하게 설정할 수 있도록 해당 서버의 ID를 알고 있어야 합니다. 외부 서비스에서 모니터링을 지원하지 않는 경우 `target`은 서비스의 호스트 이름으로 설정됩니다(예: `stock-prices-api.com`). 그러나 서비스에서 미리 정의된 HTTP 헤더를 반환하여 해당 서비스 자체를 식별하는 경우 `target`에는 Application Insights에서 해당 서비스의 원격 분석을 쿼리하여 분산 추적을 빌드할 수 있는 서비스 ID가 포함됩니다.

## <a name="correlation-headers"></a>상관 관계 헤더

다음을 정의 하는 [W3C 추적 컨텍스트로](https://w3c.github.io/trace-context/) 전환 하 고 있습니다.

- `traceparent`: 전역 고유 작업 ID와 호출의 고유 식별자를 전달 합니다.
- `tracestate`: 추적 시스템 관련 컨텍스트를 전달 합니다.

최신 버전의 Application Insights Sdk는 추적 컨텍스트 프로토콜을 지원 하지만이를 옵트인 (opt in) 해야 할 수 있습니다 .이는 ApplicationInsights Sdk에서 지 원하는 이전 상관 관계 프로토콜과 이전 버전과의 호환성을 유지 합니다.

[상관 관계 HTTP 프로토콜 즉,](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/HttpCorrelationProtocol.md) 은 사용 중단 경로에 있습니다. 이 프로토콜은 두 가지 헤더를 정의 합니다.

- `Request-Id`: 호출의 guid (globally unique ID)를 전달 합니다.
- `Correlation-Context`: 분산 추적 속성의 이름-값 쌍 컬렉션을 전달 합니다.

또한 Application Insights은 상관 관계 HTTP 프로토콜에 대 한 [확장](https://github.com/lmolkova/correlation/blob/master/http_protocol_proposal_v2.md) 을 정의 합니다. `Request-Context` 이름-값 쌍을 사용하여 즉각적인 호출자 또는 호출 수신자에서 사용하는 속성의 컬렉션을 전파합니다. Application Insights SDK는 이 헤더를 사용하여 `dependency.target` 및 `request.source` 필드를 설정합니다.

### <a name="enable-w3c-distributed-tracing-support-for-classic-aspnet-apps"></a>클래식 ASP.NET 앱에 W3C 분산 추적 지원을 사용하도록 설정
 
  > [!NOTE]
  > @No__t_0 및 `Microsoft.ApplicationInsights.DependencyCollector`부터 구성이 필요 하지 않습니다. 

W3C 추적-컨텍스트 지원은 이전 버전과 호환 되는 방식으로 수행 되며 상관 관계는 이전 버전의 SDK를 사용 하 여 계측 된 응용 프로그램에서 작동 합니다 (W3C 지원 없음). 

기존 `Request-Id` 프로토콜을 계속 사용 하려는 경우 다음 구성으로 추적 컨텍스트를 *사용 하지 않도록 설정할* 수 있습니다.

```csharp
  Activity.DefaultIdFormat = ActivityIdFormat.Hierarchical;
  Activity.ForceDefaultIdFormat = true;
```

이전 버전의 SDK를 실행 하는 경우 추적 컨텍스트를 사용 하도록 설정 하거나 다음 구성을 적용 하는 것이 좋습니다.
이 기능은 버전 2.8.0-beta1부터 `Microsoft.ApplicationInsights.Web` 및 `Microsoft.ApplicationInsights.DependencyCollector` 패키지에서 사용할 수 있으며,
기본적으로 사용하지 않도록 설정되어 있습니다. 사용하도록 설정하려면 `ApplicationInsights.config`를 다음과 같이 변경합니다.

- `RequestTrackingTelemetryModule` 아래에서 `EnableW3CHeadersExtraction` 요소를 추가하고 값을 `true`로 설정합니다.
- `DependencyTrackingTelemetryModule` 아래에서 `EnableW3CHeadersInjection` 요소를 추가하고 값을 `true`로 설정합니다.
- 다음과 유사 하 게 `TelemetryInitializers`에 `W3COperationCorrelationTelemetryInitializer`를 추가 합니다. 

```xml
<TelemetryInitializers>
  <Add Type="Microsoft.ApplicationInsights.Extensibility.W3C.W3COperationCorrelationTelemetryInitializer, Microsoft.ApplicationInsights"/>
   ...
</TelemetryInitializers> 
```

### <a name="enable-w3c-distributed-tracing-support-for-aspnet-core-apps"></a>ASP.NET Core 앱에 W3C 분산 추적 지원을 사용하도록 설정

 > [!NOTE]
  > @No__t_0 버전 2.8.0로 시작 하는 구성이 필요 하지 않습니다.
 
W3C 추적-컨텍스트 지원은 이전 버전과 호환 되는 방식으로 수행 되며 상관 관계는 이전 버전의 SDK를 사용 하 여 계측 된 응용 프로그램에서 작동 합니다 (W3C 지원 없음). 

기존 `Request-Id` 프로토콜을 계속 사용 하려는 경우 다음 구성으로 추적 컨텍스트를 *사용 하지 않도록 설정할* 수 있습니다.

```csharp
  Activity.DefaultIdFormat = ActivityIdFormat.Hierarchical;
  Activity.ForceDefaultIdFormat = true;
```

이전 버전의 SDK를 실행 하는 경우 추적 컨텍스트를 사용 하도록 설정 하거나 다음 구성을 적용 하는 것이 좋습니다.

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

  - Java EE 앱의 경우 ApplicationInsights.xml 내의 `<TelemetryModules>` 태그에 다음을 추가합니다.

    ```xml
    <Add type="com.microsoft.applicationinsights.web.extensibility.modules.WebRequestTrackingTelemetryModule>
       <Param name = "W3CEnabled" value ="true"/>
       <Param name ="enableW3CBackCompat" value = "true" />
    </Add>
    ```
  - Spring Boot 앱의 경우 다음 속성을 추가합니다.

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
> 들어오는 구성과 나가는 구성이 정확히 동일해야 합니다.

### <a name="enable-w3c-distributed-tracing-support-for-web-apps"></a>웹 앱에 대해 W3C distributed tracing 지원 사용

이 기능은 `Microsoft.ApplicationInsights.JavaScript`에 있습니다. 기본적으로 사용하지 않도록 설정되어 있습니다. 사용 하도록 설정 하려면 `distributedTracingMode` config를 사용 합니다. AI_AND_W3C은 레거시 Application Insights 계측 된 서비스와의 이전 버전과의 호환성을 위해 제공 됩니다.

- **NPM 설치 (조각 설치를 사용 하는 경우 무시)**

  ```javascript
  import { ApplicationInsights, DistributedTracingModes } from '@microsoft/applicationinsights-web';

  const appInsights = new ApplicationInsights({ config: {
    instrumentationKey: 'YOUR_INSTRUMENTATION_KEY_GOES_HERE',
    distributedTracingMode: DistributedTracingModes.W3C
    /* ...Other Configuration Options... */
  } });
  appInsights.loadAppInsights();
  ```
  
- **조각 설치 (NPM 설치를 사용 하는 경우 무시)**

  ```
  <script type="text/javascript">
  var sdkInstance="appInsightsSDK";window[sdkInstance]="appInsights";var aiName=window[sdkInstance],aisdk=window[aiName]||function(e){function n(e){i[e]=function(){var n=arguments;i.queue.push(function(){i[e].apply(i,n)})}}var i={config:e};i.initialize=!0;var a=document,t=window;setTimeout(function(){var n=a.createElement("script");n.src=e.url||"https://az416426.vo.msecnd.net/scripts/b/ai.2.min.js",a.getElementsByTagName("script")[0].parentNode.appendChild(n)});try{i.cookie=a.cookie}catch(e){}i.queue=[],i.version=2;for(var r=["Event","PageView","Exception","Trace","DependencyData","Metric","PageViewPerformance"];r.length;)n("track"+r.pop());n("startTrackPage"),n("stopTrackPage");var o="Track"+r[0];if(n("start"+o),n("stop"+o),!(!0===e.disableExceptionTracking||e.extensionConfig&&e.extensionConfig.ApplicationInsightsAnalytics&&!0===e.extensionConfig.ApplicationInsightsAnalytics.disableExceptionTracking)){n("_"+(r="onerror"));var s=t[r];t[r]=function(e,n,a,t,o){var c=s&&s(e,n,a,t,o);return!0!==c&&i["_"+r]({message:e,url:n,lineNumber:a,columnNumber:t,error:o}),c},e.autoExceptionInstrumented=!0}return i}
  (
    {
      instrumentationKey:"INSTRUMENTATION_KEY",
      distributedTracingMode: 2 // DistributedTracingModes.W3C
      /* ...Other Configuration Options... */
    }
  );
  window[aiName]=aisdk,aisdk.queue&&0===aisdk.queue.length&&aisdk.trackPageView({});
  </script>
  ```

## <a name="opentracing-and-application-insights"></a>OpenTracing 및 Application Insights

[OpenTracing 데이터 모델 사양](https://opentracing.io/)과 Application Insights 데이터 모델은 다음과 같은 방식으로 매핑됩니다.

| Application Insights                  | OpenTracing                                       |
|------------------------------------   |-------------------------------------------------  |
| `Request`, `PageView`                 | `Span`(`span.kind = server` 사용)                  |
| `Dependency`                          | `Span`(`span.kind = client` 사용)                  |
| `Request` 및 `Dependency`의 `Id`    | `SpanId`                                          |
| `Operation_Id`                        | `TraceId`                                         |
| `Operation_ParentId`                  | `ChildOf` 유형의 `Reference`(상위 범위)   |

자세한 내용은 [Application Insights 원격 분석 데이터 모델](../../azure-monitor/app/data-model.md)을 참조하세요. 

OpenTracing 개념에 대한 정의는 OpenTracing [사양](https://github.com/opentracing/specification/blob/master/specification.md) 및 [semantic_conventions](https://github.com/opentracing/specification/blob/master/semantic_conventions.md)를 참조하세요.

## <a name="telemetry-correlation-in-net"></a>.NET의 원격 분석 상관 관계

시간이 지남에 따라 .NET에서는 원격 분석과 진단 로그를 상호 연결하는 몇 가지 방법을 정의했습니다.

- `System.Diagnostics.CorrelationManager`는 [LogicalOperationStack 및 ActivityId](https://msdn.microsoft.com/library/system.diagnostics.correlationmanager.aspx)의 추적을 허용합니다. 
- `System.Diagnostics.Tracing.EventSource` 및 ETW(Windows용 이벤트 추적)는 [SetCurrentThreadActivityId](https://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource.setcurrentthreadactivityid.aspx) 메서드를 정의합니다.
- `ILogger`는 [Log Scopes](https://docs.microsoft.com/aspnet/core/fundamentals/logging#log-scopes)(로그 범위)를 사용합니다. 
- WCF(Windows Communication Foundation) 및 HTTP는 "현재" 컨텍스트 전파를 연결합니다.

그러나 이러한 방법에서는 자동 분산 추적 지원을 사용하도록 설정하지 않았습니다. `DiagnosticSource`는 머신 간 자동 상관 관계를 지원하는 한 가지 방법입니다. .NET 라이브러리는 'DiagnosticSource'를 지원하고, HTTP와 같은 전송을 통해 상관 관계 컨텍스트의 머신 간 자동 전파를 허용합니다.

`DiagnosticSource`의 [활동 가이드](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/ActivityUserGuide.md)는 활동 추적의 기본 사항에 대해 설명하고 있습니다.

ASP.NET Core 2.0은 HTTP 헤더 추출 및 새 활동 시작을 지원합니다.

`System.Net.Http.HttpClient`는 버전 4.1.0부터 상관 관계 HTTP 헤더의 자동 삽입 및 HTTP 호출을 활동으로 추적하는 기능을 지원합니다.

클래식 ASP.NET에 대한 새로운 [Microsoft.AspNet.TelemetryCorrelation](https://www.nuget.org/packages/Microsoft.AspNet.TelemetryCorrelation/) HTTP 모듈이 있습니다. 이 모듈은 `DiagnosticSource`를 사용하여 원격 분석 상관 관계를 구현하며, 들어오는 요청 헤더에 기반한 활동을 시작합니다. 또한 IIS(인터넷 정보 서비스) 처리의 모든 단계가 다른 관리형 스레드에서 실행되는 경우에도 다양한 요청 처리 단계에서 원격 분석을 상호 연결합니다.

Application Insights SDK는 버전 2.4.0-beta1부터 `DiagnosticSource` 및 `Activity`를 사용하여 원격 분석을 수집하고 현재 활동과 연결합니다.

<a name="java-correlation"></a>
## <a name="telemetry-correlation-in-the-java-sdk"></a>Java SDK의 원격 분석 상관 관계

[Java용 Application Insights SDK](../../azure-monitor/app/java-get-started.md)는 2.0.0 버전부터 원격 분석의 자동 상관 관계를 지원합니다. 요청 범위 내에서 실행된 모든 원격 분석(예: 추적, 예외 및 사용자 지정 이벤트)에 대해 `operation_id`를 자동으로 채웁니다. 또한 [Java SDK 에이전트](../../azure-monitor/app/java-agent.md)가 구성된 경우 HTTP를 통한 서비스 간 호출에 대해 앞에서 설명한 상관 관계 헤더를 전파합니다.

> [!NOTE]
> 상관 관계 기능에는 Apache HTTPClient를 통해 수행되는 호출만 지원됩니다. Spring RestTemplate 또는 Feign을 사용하는 경우 둘 다 Apache HTTPClient에서 내부적으로 사용할 수 있습니다.

현재 메시징 기술(예: Kafka, RabbitMQ, Azure Service Bus)을 통한 자동 컨텍스트 전파는 지원되지 않습니다. 그러나 `trackDependency` 및 `trackRequest` API를 사용하여 이러한 시나리오를 수동으로 코딩할 수 있습니다. 이러한 API에서 종속성 원격 분석은 생산자를 통해 큐에 넣는 메시지를 나타내고, 요청은 소비자를 통해 처리하는 메시지를 나타냅니다. 이 경우 `operation_id` 및 `operation_parentId` 둘 다 메시지의 속성에 전파되어야 합니다.

### <a name="telemetry-correlation-in-asynchronous-java-application"></a>비동기 Java 응용 프로그램의 원격 분석 상관 관계

비동기 스프링 부팅 응용 프로그램에서 원격 분석의 상관 관계를 확인 하려면 [이](https://github.com/Microsoft/ApplicationInsights-Java/wiki/Distributed-Tracing-in-Asynchronous-Java-Applications) 심층적인 문서를 참조 하세요. 스프링의 [ThreadPoolTaskExecutor](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/scheduling/concurrent/ThreadPoolTaskExecutor.html) 및 [ThreadPoolTaskScheduler](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/scheduling/concurrent/ThreadPoolTaskScheduler.html)를 계측 하는 방법에 대 한 지침을 제공 합니다. 


<a name="java-role-name"></a>
## <a name="role-name"></a>역할 이름

경우에 따라, [애플리케이션 맵](../../azure-monitor/app/app-map.md)에 구성 요소 이름에 표시되는 방식을 사용자 지정하려고 할 수 있습니다. 이렇게 하려면 다음 중 하나를 수행하여 `cloud_RoleName`을 수동으로 설정할 수 있습니다.

- Application Insights Spring Boot 스타터에서 Spring Boot를 사용하는 경우 application.properties 파일에서 애플리케이션에 대한 사용자 지정 이름을 설정하도록 변경하기만 하면 됩니다.

  `spring.application.name=<name-of-app>`

  Spring Boot 스타터는 `cloudRoleName`을 `spring.application.name` 속성에 대해 입력한 값에 자동으로 할당합니다.

- `WebRequestTrackingFilter`를 사용하는 경우 `WebAppNameContextInitializer`에서 애플리케이션 이름을 자동으로 설정합니다. 구성 파일(ApplicationInsights.xml)에 다음을 추가합니다.

  ```XML
  <ContextInitializers>
    <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebAppNameContextInitializer" />
  </ContextInitializers>
  ```

- 클라우드 컨텍스트 클래스를 사용하는 경우:

  ```Java
  telemetryClient.getContext().getCloud().setRole("My Component Name");
  ```

## <a name="next-steps"></a>다음 단계

- [사용자 지정 원격 분석](../../azure-monitor/app/api-custom-events-metrics.md)을 작성합니다.
- ASP.NET Core 및 ASP.NET의 고급 상관 관계 시나리오는 [사용자 지정 작업 추적](custom-operations-tracking.md) 문서를 참조 하세요.
- 다른 SDK의 [cloud_RoleName 설정](../../azure-monitor/app/app-map.md#set-cloud-role-name)에 대해 자세히 알아봅니다.
- Application Insights에서 마이크로 서비스의 모든 구성 요소를 온보딩합니다. [지원되는 플랫폼](../../azure-monitor/app/platforms.md)을 확인합니다.
- Application Insights 유형에 대한 [데이터 모델](../../azure-monitor/app/data-model.md)을 참조합니다.
- [원격 분석을 확장 및 필터링](../../azure-monitor/app/api-filtering-sampling.md)하는 방법을 알아봅니다.
- [Application Insights 구성 참조](configuration-with-applicationinsights-config.md)를 검토합니다.
