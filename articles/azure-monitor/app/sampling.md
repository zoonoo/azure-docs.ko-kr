---
title: Azure Application Insights의 원격 분석 샘플링 | Microsoft Docs
description: 제어에서 원격 분석의 양을 유지하는 방법입니다.
ms.topic: conceptual
ms.date: 01/17/2020
ms.reviewer: vitalyg
ms.custom: fasttrack-edit
ms.openlocfilehash: 5e888e0606b7a9bcd9a7a94c28455d705c5f1bec
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/13/2020
ms.locfileid: "81255484"
---
# <a name="sampling-in-application-insights"></a>Application Insights의 샘플링

샘플링은 [Azure Application Insights](../../azure-monitor/app/app-insights-overview.md)의 기능입니다. 응용 프로그램 데이터의 통계적으로 올바른 분석을 유지하면서 원격 분석 트래픽, 데이터 비용 및 저장소 비용을 줄이는 것이 좋습니다. 샘플링을 사용하면 원격 분석을 제한하는 응용 프로그램 정보를 방지할 수 있습니다. 샘플링 필터는 진단 조사를 수행할 때 항목 간에 탐색할 수 있도록 관련된 항목을 선택합니다.

메트릭 수가 포털에 표시되면 샘플링을 고려하도록 다시 정규화됩니다. 이렇게 하면 통계에 미치는 영향을 최소화할 수 있습니다.

## <a name="brief-summary"></a>간략한 요약

* 샘플링에는 적응형 샘플링, 고정 비율 샘플링 및 흡기 샘플링의 세 가지 유형이 있습니다.
* 어댑티브 샘플링은 응용 프로그램 인사이트 ASP.NET 및 ASP.NET 핵심 소프트웨어 개발 키트(SDK)의 모든 최신 버전에서 기본적으로 활성화됩니다. Azure [함수에서도 사용됩니다.](https://docs.microsoft.com/azure/azure-functions/functions-overview)
* 고정 요금 샘플링은 ASP.NET, ASP.NET 코어, Java(에이전트 및 SDK 모두) 및 파이썬에 대한 응용 프로그램 인사이트 SDK의 최신 버전에서 사용할 수 있습니다.
* 인기 샘플링은 응용 프로그램 인사이트 서비스 끝점에서 작동합니다. 다른 샘플링이 적용되지 않는 경우에만 적용됩니다. SDK가 원격 분석을 샘플링하는 경우 인비저레이션 샘플링이 비활성화됩니다.
* 웹 응용 프로그램의 경우 사용자 지정 이벤트를 기록하고 이벤트 집합을 함께 유지하거나 삭제해야 하는 경우 `OperationId` 이벤트의 값이 같아야 합니다.
* 분석 쿼리를 작성하는 경우 [샘플링을 고려](../../azure-monitor/log-query/aggregations.md)해야 합니다. 특히, 레코드를 단순히 세는 대신 `summarize sum(itemCount)`를 사용해야 합니다.
* 성능 메트릭 및 사용자 지정 메트릭을 포함한 일부 원격 분석 유형은 샘플링사용 여부에 관계없이 항상 유지됩니다.

다음 표에는 각 SDK에 사용할 수 있는 샘플링 유형과 응용 프로그램 유형이 요약되어 있습니다.

| Application Insights SDK | 어댑티브 샘플링 지원 | 고정 요금 샘플링 지원 | 인비시레이션 샘플링 지원 |
|-|-|-|-|
| ASP.NET | [예(기본적으로 사용 중)](#configuring-adaptive-sampling-for-aspnet-applications) | [예](#configuring-fixed-rate-sampling-for-aspnet-applications) | 다른 샘플링이 적용되지 않는 경우에만 |
| ASP.NET Core | [예(기본적으로 사용 중)](#configuring-adaptive-sampling-for-aspnet-core-applications) | [예](#configuring-fixed-rate-sampling-for-aspnet-core-applications) | 다른 샘플링이 적용되지 않는 경우에만 |
| Azure 기능 | [예(기본적으로 사용 중)](#configuring-adaptive-sampling-for-azure-functions) | 예 | 다른 샘플링이 적용되지 않는 경우에만 |
| Java | 예 | [예](#configuring-fixed-rate-sampling-for-java-applications) | 다른 샘플링이 적용되지 않는 경우에만 |
| Python | 예 | [예](#configuring-fixed-rate-sampling-for-opencensus-python-applications) | 다른 샘플링이 적용되지 않는 경우에만 |
| 나머지 | 예 | 예 | [예](#ingestion-sampling) |

> [!NOTE]
> 이 페이지의 대부분에 대한 정보는 응용 프로그램 인사이트 SDK의 현재 버전에 적용됩니다. 이전 버전의 SDK에 대한 자세한 내용은 [아래 섹션을 참조하십시오.](#older-sdk-versions)

## <a name="types-of-sampling"></a>샘플링 유형

세 가지 샘플링 방법이 있습니다.

* **적응 샘플링은** ASP.NET/ASP.NET 핵심 앱과 Azure Functions에서 SDK에서 전송된 원격 분석의 볼륨을 자동으로 조정합니다. ASP.NET 또는 ASP.NET 코어 SDK를 사용할 때 기본 샘플링입니다. 적응형 샘플링은 현재 ASP.NET 서버 측 원격 분석 및 Azure Functions에만 사용할 수 있습니다.

* **고정 비율 샘플링은** ASP.NET 또는 ASP.NET 코어 또는 Java 서버와 사용자의 브라우저에서 전송되는 원격 분석의 양을 줄입니다. 비율은 사용자가 설정합니다. 클라이언트와 서버는 샘플링을 동기화하므로 검색에서 관련된 페이지 보기 및 요청 사이를 이동할 수 있습니다.

* **인비시 샘플링은** 애플리케이션 인사이트 서비스 엔드포인트에서 수행됩니다. 설정한 샘플링 주기에 따라 앱에서 보낸 원격 분석 중 일부를 삭제합니다. 앱에서 보낸 원격 분석 트래픽을 줄이지는 않지만 월별 할당량 내로 유지하는 데 도움이 됩니다. 섭취 샘플링의 주요 장점은 앱을 다시 배포하지 않고도 샘플링 속도를 설정할 수 있다는 것입니다. 인기 샘플링은 모든 서버와 클라이언트에 대해 균일하게 작동하지만 다른 유형의 샘플링이 작동중인 경우에는 적용되지 않습니다.

> [!IMPORTANT]
> 적응형 또는 고정 속도 샘플링 방법이 작동 중인 경우 섭취 샘플링이 비활성화됩니다.

## <a name="adaptive-sampling"></a>적응 샘플링

적응 샘플링은 웹 서버 앱에서 Application Insights 서비스 엔드포인트로 보내는 원격 분석의 양에 영향을 줍니다.

> [!TIP]
> 적응 샘플링은 ASP.NET SDK 또는 ASP.NET 코어 SDK를 사용할 때 기본적으로 활성화되며 Azure Functions에 대해서도 기본적으로 활성화됩니다.

볼륨은 지정된 최대 트래픽 속도 내에서 유지하도록 자동으로 조정되며 설정을 `MaxTelemetryItemsPerSecond`통해 제어됩니다. 응용 프로그램에서 디버깅 시 또는 사용량이 낮기 와 같이 낮은 양의 원격 분석을 생성하는 경우 볼륨이 아래에 `MaxTelemetryItemsPerSecond`있는 한 샘플링 프로세서에서 항목을 삭제하지 않습니다. 원격 분석 의 볼륨이 증가하면 대상 볼륨을 달성하기 위해 샘플링 속도가 조정됩니다. 조정은 정기적으로 다시 계산되고 발신 전송 속도의 이동 평균에 기반합니다.

목표량을 달성하기 위해 생성된 원격 분석 중 일부가 삭제됩니다. 그러나 다른 샘플링 유형과 마찬가지로 알고리즘에 관련 원격 분석 항목이 유지됩니다. 예를 들어 검색에서 원격 분석을 검사하는 경우 특정 예외와 관련된 요청을 찾을 수 있습니다.

요청 빈도 및 예외 처리 빈도와 같은 메트릭 수는 샘플링 주기에 맞게 보정되도록 조정되므로 메트릭 탐색기에서 거의 정확한 값으로 표시됩니다.

### <a name="configuring-adaptive-sampling-for-aspnet-applications"></a>ASP.NET 애플리케이션을 위한 어댑티브 샘플링 구성

> [!NOTE]
> 이 섹션은 핵심 응용 ASP.NET ASP.NET 응용 프로그램에 적용됩니다. [이 문서의 ASP.NET 핵심 응용 프로그램에 대한 적응 샘플링 구성에 대해 알아봅니다.](../../azure-monitor/app/sampling.md#configuring-adaptive-sampling-for-aspnet-core-applications)

에서 [`ApplicationInsights.config`](../../azure-monitor/app/configuration-with-applicationinsights-config.md)노드에서 여러 매개 변수를 `AdaptiveSamplingTelemetryProcessor` 조정할 수 있습니다. 표시된 수치는 기본값입니다.

* `<MaxTelemetryItemsPerSecond>5</MaxTelemetryItemsPerSecond>`
  
    대상 비율은 **각 서버 호스트에**대한 것을 목표로 하는 적응 알고리즘입니다. 여러 호스트에서 웹앱을 실행하는 경우 Application Insights 포털에서 트래픽을 대상 비율 범위 내에서 유지하기 위해 이 값을 줄입니다.

* `<EvaluationInterval>00:00:15</EvaluationInterval>` 
  
    현재 원격 분석 속도를 다시 평가하는 간격입니다. 평가는 이동 평균으로 수행됩니다. 원격 분석이 급격히 증가하는 경우 이 간격을 줄일 수 있습니다.

* `<SamplingPercentageDecreaseTimeout>00:02:00</SamplingPercentageDecreaseTimeout>`
  
    샘플링 백분율 값이 변경될 때 샘플링 백분율을 다시 낮게 하여 더 적은 데이터를 캡처할 수 있는 방법은 얼마나 빨리입니까?

* `<SamplingPercentageIncreaseTimeout>00:15:00</SamplingPercentageIncreaseTimeout>`
  
    샘플링 백분율 값이 변경될 때 샘플링 백분율을 다시 늘려 더 많은 데이터를 캡처할 수 있는 방법은 얼마나 빨리입니까?

* `<MinSamplingPercentage>0.1</MinSamplingPercentage>`
  
    샘플링 백분율이 다르므로 설정할 수 있는 최소 값은 얼마입니까?

* `<MaxSamplingPercentage>100.0</MaxSamplingPercentage>`
  
    샘플링 백분율이 다르므로 설정할 수 있는 최대 값은 무엇입니까?

* `<MovingAverageRatio>0.25</MovingAverageRatio>` 
  
    이동 평균 계산에서 가장 최근 값에 할당해야 하는 가중치를 지정합니다. 1보다 작거나 같은 값을 사용합니다. 값이 작을수록 알고리즘은 갑작스런 변화에 덜 반응합니다.

* `<InitialSamplingPercentage>100</InitialSamplingPercentage>`
  
    앱이 방금 시작되면 샘플링할 원격 분석의 양입니다. 디버깅하는 동안 이 값을 줄이지 마십시오.

* `<ExcludedTypes>Trace;Exception</ExcludedTypes>`
  
    샘플링대상이 되지 않으려는 세미콜론으로 구분된 형식 목록입니다. 인식된 형식은 `Event` `Exception`다음과 `PageView` `Dependency` `Request`같습니다. `Trace` 지정된 형식의 모든 원격 분석이 전송됩니다. 지정되지 않은 형식이 샘플링됩니다.

* `<IncludedTypes>Request;Dependency</IncludedTypes>`
  
    샘플링대상이 될 세미콜론으로 구분된 형식 목록입니다. 인식된 형식은 `Event` `Exception`다음과 `PageView` `Dependency` `Request`같습니다. `Trace` 지정된 형식이 샘플링됩니다. 다른 형식의 모든 원격 분석이 항상 전송됩니다.

적응 샘플링을 `AdaptiveSamplingTelemetryProcessor` **끄려면** 에서 `ApplicationInsights.config`노드를 제거합니다.

#### <a name="alternative-configure-adaptive-sampling-in-code"></a>대안: 코드에서 적응 샘플링 구성

파일에서 샘플링 매개 변수를 `.config` 설정하는 대신 프로그래밍 방식으로 이러한 값을 설정할 수 있습니다.

1. 파일에서 `AdaptiveSamplingTelemetryProcessor` 모든 노드를 제거합니다. `.config`
2. 다음 스니펫을 사용하여 적응 샘플링을 구성합니다.

    ```csharp
    using Microsoft.ApplicationInsights;
    using Microsoft.ApplicationInsights.Extensibility;
    using Microsoft.ApplicationInsights.WindowsServer.Channel.Implementation;
    using Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel;
    
    // ...

    var builder = TelemetryConfiguration.Active.DefaultTelemetrySink.TelemetryProcessorChainBuilder;
    // For older versions of the Application Insights SDK, use the following line instead:
    // var builder = TelemetryConfiguration.Active.TelemetryProcessorChainBuilder;

    // Enable AdaptiveSampling so as to keep overall telemetry volume to 5 items per second.
    builder.UseAdaptiveSampling(maxTelemetryItemsPerSecond:5);

    // If you have other telemetry processors:
    builder.Use((next) => new AnotherProcessor(next));

    builder.Build();
    ```

    ([원격 분석 프로세서에 대해 알아봅니다](../../azure-monitor/app/api-filtering-sampling.md#filtering).)

또한 각 원격 분석 유형에 대한 샘플링 속도를 개별적으로 조정하거나 특정 형식을 전혀 샘플링하지 못하도록 제외할 수도 있습니다.

```csharp
// The following configures adaptive sampling with 5 items per second, and also excludes Dependency telemetry from being subjected to sampling.
builder.UseAdaptiveSampling(maxTelemetryItemsPerSecond:5, excludedTypes: "Dependency");
```

### <a name="configuring-adaptive-sampling-for-aspnet-core-applications"></a>ASP.NET 코어 애플리케이션을 위한 어댑티브 샘플링 구성

ASP.NET Core `ApplicationInsights.config` 응용 프로그램에는 아무런 문제가 없으므로 모든 구성은 코드를 통해 수행됩니다.
적응 샘플링은 모든 ASP.NET Core 애플리케이션에 기본적으로 사용됩니다. 샘플링 동작을 사용하지 않도록 설정하거나 사용자 지정할 수 있습니다.

#### <a name="turning-off-adaptive-sampling"></a>어댑티브 샘플링 끄기

기본 샘플링 기능은 파일 내에서 사용하여 `ConfigureServices` `ApplicationInsightsServiceOptions` 메서드에서 Application Insights 서비스를 `Startup.cs` 추가하는 동안 비활성화할 수 있습니다.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // ...

    var aiOptions = new Microsoft.ApplicationInsights.AspNetCore.Extensions.ApplicationInsightsServiceOptions();
    aiOptions.EnableAdaptiveSampling = false;
    services.AddApplicationInsightsTelemetry(aiOptions);

    //...
}
```

위의 코드는 적응 샘플링을 사용하지 않도록 설정합니다. 더 많은 사용자 지정 옵션을 사용하여 샘플링을 추가하려면 다음 단계를 수행합니다.

#### <a name="configure-sampling-settings"></a>샘플링 설정 구성

아래와 같이 `TelemetryProcessorChainBuilder`의 확장 메서드를 사용하여 샘플링 동작을 사용자 지정합니다.

> [!IMPORTANT]
> 이 메서드를 사용하여 샘플링을 구성하는 경우 `aiOptions.EnableAdaptiveSampling` 을 호출할 `false` `AddApplicationInsightsTelemetry()`때 속성을 설정해야 합니다.

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env, TelemetryConfiguration configuration)
{
    var builder = configuration.DefaultTelemetrySink.TelemetryProcessorChainBuilder;
    // For older versions of the Application Insights SDK, use the following line instead:
    // var builder = configuration.TelemetryProcessorChainBuilder;

    // Using adaptive sampling
    builder.UseAdaptiveSampling(maxTelemetryItemsPerSecond:5);

    // Alternately, the following configures adaptive sampling with 5 items per second, and also excludes DependencyTelemetry from being subject to sampling.
    // builder.UseAdaptiveSampling(maxTelemetryItemsPerSecond:5, excludedTypes: "Dependency");

    // If you have other telemetry processors:
    builder.Use((next) => new AnotherProcessor(next));
    
    builder.Build();

    // ...
}
```

### <a name="configuring-adaptive-sampling-for-azure-functions"></a>Azure 함수에 대한 적응 샘플링 구성

[이 페이지의](https://docs.microsoft.com/azure/azure-functions/functions-monitoring#configure-sampling) 지침에 따라 Azure Functions에서 실행 중인 앱에 대한 적응 샘플링을 구성합니다.

## <a name="fixed-rate-sampling"></a>고정 금리 샘플링

고정 요금 샘플링은 웹 서버 및 웹 브라우저에서 전송되는 트래픽을 줄입니다. 적응 샘플링과 달리 사용자가 결정한 고정 비율로 원격 분석을 줄어듭니다. 고정 요금 샘플링은 ASP.NET, ASP.NET 코어, Java 및 파이썬 응용 프로그램에 사용할 수 있습니다.

다른 샘플링 기술과 마찬가지로 관련 항목도 유지합니다. 또한 클라이언트 및 서버 샘플링을 동기화하여 관련 항목이 유지되도록 합니다(예: Search에서 페이지 보기를 보면 관련 서버 요청을 찾을 수 있습니다). 

메트릭 탐색기에서 요청 및 예외 수와 같은 빈도는 거의 정확한 값이 되도록 계수가 곱해져 샘플링 주기에 맞게 보정됩니다.

### <a name="configuring-fixed-rate-sampling-for-aspnet-applications"></a>ASP.NET 애플리케이션을 위한 고정 요금 샘플링 구성

1. **적응 샘플링 사용** [`ApplicationInsights.config`](../../azure-monitor/app/configuration-with-applicationinsights-config.md)안 함 : `AdaptiveSamplingTelemetryProcessor` 에서 노드를 제거하거나 주석을 달수 있습니다.

    ```xml
    <TelemetryProcessors>
        <!-- Disabled adaptive sampling:
        <Add Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.AdaptiveSamplingTelemetryProcessor, Microsoft.AI.ServerTelemetryChannel">
            <MaxTelemetryItemsPerSecond>5</MaxTelemetryItemsPerSecond>
        </Add>
        -->
    ```

2. **고정 비율 샘플링 모듈을 사용하도록 설정합니다.** 이 스니펫을 [`ApplicationInsights.config`](../../azure-monitor/app/configuration-with-applicationinsights-config.md)다음에 추가합니다.
   
    ```XML
    <TelemetryProcessors>
        <Add Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.SamplingTelemetryProcessor, Microsoft.AI.ServerTelemetryChannel">
            <!-- Set a percentage close to 100/N where N is an integer. -->
            <!-- E.g. 50 (=100/2), 33.33 (=100/3), 25 (=100/4), 20, 1 (=100/100), 0.1 (=100/1000) -->
            <SamplingPercentage>10</SamplingPercentage>
        </Add>
    </TelemetryProcessors>
    ```

      또는 `ApplicationInsights.config` 파일에서 샘플링 매개 변수를 설정하는 대신 프로그래밍 방식으로 이러한 값을 설정할 수 있습니다.

    ```csharp
    using Microsoft.ApplicationInsights.Extensibility;
    using Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel;

    // ...

    var builder = TelemetryConfiguration.Active.DefaultTelemetrySink.TelemetryProcessorChainBuilder;
    // For older versions of the Application Insights SDK, use the following line instead:
    // var builder = TelemetryConfiguration.Active.TelemetryProcessorChainBuilder;

    builder.UseSampling(10.0); // percentage

    // If you have other telemetry processors:
    builder.Use((next) => new AnotherProcessor(next));

    builder.Build();
    ```

    ([원격 분석 프로세서에 대해 알아봅니다](../../azure-monitor/app/api-filtering-sampling.md#filtering).)

### <a name="configuring-fixed-rate-sampling-for-aspnet-core-applications"></a>ASP.NET 핵심 애플리케이션을 위한 고정 레이트 샘플링 구성

1. **적응 샘플링 사용 안 함**: `ConfigureServices` 다음을 `ApplicationInsightsServiceOptions`사용하여 메서드에서 변경할 수 있습니다.

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        // ...

        var aiOptions = new Microsoft.ApplicationInsights.AspNetCore.Extensions.ApplicationInsightsServiceOptions();
        aiOptions.EnableAdaptiveSampling = false;
        services.AddApplicationInsightsTelemetry(aiOptions);

        //...
    }
    ```

2. **고정 비율 샘플링 모듈을 사용하도록 설정합니다.** 아래 코드 조각에 `Configure` 표시된 대로 메서드에서 변경할 수 있습니다.

    ```csharp
    public void Configure(IApplicationBuilder app, IHostingEnvironment env)
    {
        var configuration = app.ApplicationServices.GetService<TelemetryConfiguration>();

        var builder = configuration.DefaultTelemetrySink.TelemetryProcessorChainBuilder;
        // For older versions of the Application Insights SDK, use the following line instead:
        // var builder = TelemetryConfiguration.Active.TelemetryProcessorChainBuilder;

        // Using fixed rate sampling   
        double fixedSamplingPercentage = 10;
        builder.UseSampling(fixedSamplingPercentage);

        builder.Build();

        // ...
    }
    ```

### <a name="configuring-fixed-rate-sampling-for-java-applications"></a>Java 애플리케이션을 위한 고정 레이트 샘플링 구성

기본적으로 Java 에이전트 및 SDK에서는 샘플링이 활성화되지 않습니다. 현재는 고정 비율 샘플링만 지원합니다. 적응형 샘플링은 Java에서 지원되지 않습니다.

#### <a name="configuring-java-agent"></a>자바 에이전트 구성

1. [애플리케이션 인사이트 에이전트 다운로드-3.0.0-PREVIEW.2.jar](https://github.com/microsoft/ApplicationInsights-Java/releases/download/3.0.0-PREVIEW.2/applicationinsights-agent-3.0.0-PREVIEW.2.jar)

1. 샘플링을 사용하려면 파일에 `ApplicationInsights.json` 다음을 추가합니다.

```json
{
  "instrumentationSettings": {
    "preview": {
      "sampling": {
        "fixedRate": {
          "percentage": 10 //this is just an example that shows you how to enable only only 10% of transaction 
        }
      }
    }
  }
}
```

#### <a name="configuring-java-sdk"></a>자바 SDK 구성

1. 다운로드 및 최신 [응용 프로그램 통찰력 자바 SDK와](../../azure-monitor/app/java-get-started.md)웹 응용 프로그램을 구성 .

2. 파일에 다음 스니펫을 추가하여 고정 속도 `ApplicationInsights.xml` 샘플링 **모듈을 활성화합니다.**

    ```XML
    <TelemetryProcessors>
        <BuiltInProcessors>
            <Processor type="FixedRateSamplingTelemetryProcessor">
                <!-- Set a percentage close to 100/N where N is an integer. -->
                <!-- E.g. 50 (=100/2), 33.33 (=100/3), 25 (=100/4), 20, 1 (=100/100), 0.1 (=100/1000) -->
                <Add name="SamplingPercentage" value="50" />
            </Processor>
        </BuiltInProcessors>
    </TelemetryProcessors>
    ```

3. `Processor` 태그 의 다음 태그를 사용 하 여 샘플링에서 특정 유형의 원격 `FixedRateSamplingTelemetryProcessor`분석을 포함 하거나 제외할 수 있습니다.
   
    ```XML
    <ExcludedTypes>
        <ExcludedType>Request</ExcludedType>
    </ExcludedTypes>

    <IncludedTypes>
        <IncludedType>Exception</IncludedType>
    </IncludedTypes>
    ```

샘플링에서 포함하거나 제외할 수 있는 원격 `Dependency`분석 `Event` `Exception`유형은 다음과 `Trace`같습니다. `PageView` `Request`

> [!NOTE]
> 샘플링 비율의 경우 100/N(여기서 N은 정수)에 가까운 백분율을 선택합니다.  현재 샘플링은 다른 값을 지원하지 않습니다.

### <a name="configuring-fixed-rate-sampling-for-opencensus-python-applications"></a>OpenCensus 파이썬 애플리케이션에 대한 고정 비율 샘플링 구성

최신 [OpenCensus Azure 모니터 내보내기를](../../azure-monitor/app/opencensus-python.md)사용하여 응용 프로그램을 계측합니다.

> [!NOTE]
> 메트릭 내보내기에는 고정 비율 샘플링을 사용할 수 없습니다. 즉, 사용자 지정 메트릭은 샘플링을 구성할 수 없는 유일한 원격 분석 유형입니다. 메트릭 내보내기는 추적하는 모든 원격 분석을 보냅니다.

#### <a name="fixed-rate-sampling-for-tracing"></a>추적을 위한 고정 비율 샘플링 ####
`Tracer` 구성의 일부로 `sampler`를 지정할 수 있습니다. 명시적 샘플러가 제공되지 `ProbabilitySampler` 않으면 기본적으로 사용됩니다. 기본적으로 `ProbabilitySampler` 1/10000의 비율을 사용하므로 요청 10000개 중 1개가 응용 프로그램 인사이트로 전송됩니다. 샘플링 속도를 지정하려면 아래를 참조하세요.

샘플링 속도를 지정하려면 샘플링 `Tracer` 속도가 0.0에서 1.0 사이인 샘플러를 지정해야 합니다. 샘플링 속도가 1.0이면 100%를 나타내므로 모든 요청이 원격 분석으로 응용 프로그램 인사이트로 전송됩니다.

```python
tracer = Tracer(
    exporter=AzureExporter(
        instrumentation_key='00000000-0000-0000-0000-000000000000',
    ),
    sampler=ProbabilitySampler(1.0),
)
```

#### <a name="fixed-rate-sampling-for-logs"></a>로그에 대한 고정 비율 샘플링 ####
선택적 인수를 `AzureLogHandler` 수정하여 고정 비율 샘플링을 `logging_sampling_rate` 구성할 수 있습니다. 인수가 제공되지 않으면 샘플링 속도가 1.0으로 사용됩니다. 샘플링 속도가 1.0이면 100%를 나타내므로 모든 요청이 원격 분석으로 응용 프로그램 인사이트로 전송됩니다.

```python
exporter = metrics_exporter.new_metrics_exporter(
    instrumentation_key='00000000-0000-0000-0000-000000000000',
    logging_sampling_rate=0.5,
)
```

### <a name="configuring-fixed-rate-sampling-for-web-pages-with-javascript"></a>자바 스크립트와 웹 페이지에 대한 고정 속도 샘플링 구성

응용 프로그램 인사이트를 사용하도록 JavaScript 기반 웹 페이지를 구성할 수 있습니다. 원격 분석은 사용자의 브라우저 내에서 실행되는 클라이언트 응용 프로그램에서 전송되며 모든 서버에서 페이지를 호스트할 수 있습니다.

응용 [프로그램 인사이트를 위해 JavaScript 기반 웹 페이지를 구성할](javascript.md)때 응용 프로그램 인사이트 포털에서 얻을 수 있는 JavaScript 스니펫을 수정합니다.

> [!TIP]
> 자바 스크립트가 포함 된 ASP.NET 앱에서 스니펫은 일반적으로 에 간다. `_Layout.cshtml`

계측 키 앞에 `samplingPercentage: 10,`과 같은 줄을 삽입합니다.

```xml
<script>
    var appInsights = // ... 
    ({ 
      // Value must be 100/N where N is an integer.
      // Valid examples: 50, 25, 20, 10, 5, 1, 0.1, ...
      samplingPercentage: 10, 

      instrumentationKey: ...
    }); 

    window.appInsights = appInsights; 
    appInsights.trackPageView(); 
</script>
```

샘플링 비율의 경우 100/N(여기서 N은 정수)에 가까운 백분율을 선택합니다. 현재 샘플링은 다른 값을 지원하지 않습니다.

#### <a name="coordinating-server-side-and-client-side-sampling"></a>서버 측 및 클라이언트 측 샘플링 조정

클라이언트 측 자바스크립트 SDK는 서버 측 SDK와 함께 고정 요금 샘플링에 참여합니다. 계측된 페이지는 서버 측 SDK가 샘플링에 포함하기로 결정한 동일한 사용자로부터만 클라이언트 측 원격 분석을 보냅니다. 이 논리는 클라이언트 및 서버 쪽 응용 프로그램 전체에서 사용자 세션의 무결성을 유지하도록 설계되었습니다. 따라서 Application Insights의 특정 원격 분석 항목에서 이 사용자 또는 세션에 대한 다른 모든 원격 분석 항목을 찾을 수 있으며 Search에서는 관련 페이지 보기와 요청 간에 탐색할 수 있습니다.

클라이언트 및 서버 측 원격 분석이 조정된 샘플을 표시하지 않는 경우:

* 서버와 클라이언트 모두에서 샘플링을 사용하도록 설정했는지 확인합니다.
* 클라이언트와 서버 모두에서 동일한 샘플링 비율을 설정하도록 합니다.
* SDK 버전이 2.0 이상이어야 합니다.

## <a name="ingestion-sampling"></a>수집 샘플링

통합 샘플링은 웹 서버, 브라우저 및 장치의 원격 분석이 Application Insights 서비스 끝점에 도달하는 지점에서 작동합니다. 앱에서 전송되는 원격 분석 트래픽을 줄이지는 않지만 Application Insights에서 처리 및 보존(및 청구)되는 양을 줄입니다.

앱이 월간 할당량을 자주 초과하지만 SDK 기반의 샘플링 유형 중 하나를 사용할 옵션이 없는 경우 이 샘플링 유형을 사용합니다. 

사용량 및 예상 비용 페이지에서 샘플링 주기를 설정합니다.

![응용 프로그램의 개요 블레이드에서 설정, 할당량, 샘플을 클릭한 다음 샘플링 속도를 선택하고 업데이트를 클릭합니다.](./media/sampling/data-sampling.png)

다른 샘플링 유형과 마찬가지로 알고리즘에 관련 원격 분석 항목이 유지됩니다. 예를 들어 검색에서 원격 분석을 검사하는 경우 특정 예외와 관련된 요청을 찾을 수 있습니다. 요청 빈도 및 예외 처리 빈도와 같은 메트릭 수는 올바르게 유지됩니다.

샘플링에서 무시된 데이터 요소는 [연속 내보내기](../../azure-monitor/app/export-telemetry.md)와 같은 Application Insights 기능에서 사용할 수 없습니다.

적응형 또는 고정 속도 샘플링이 작동하는 동안에는 섭취 샘플링이 작동하지 않습니다. 적응 샘플링은 ASP.NET SDK 또는 ASP.NET 코어 SDK가 사용 중이거나 Azure 앱 [서비스에서](azure-web-apps.md) 또는 상태 모니터를 사용하여 응용 프로그램 인사이트를 사용하도록 설정된 경우 기본적으로 활성화됩니다. Application Insights 서비스 끝점에서 원격 분석을 수신하면 원격 분석을 검사하고 샘플링 속도가 100% 미만(원격 분석이 샘플링중임을 나타냅니다)으로 보고되면 설정한 추출 샘플링 속도가 무시됩니다.

> [!WARNING]
> 포털 타일에 표시된 값은 인비온 샘플링에 대해 설정한 값을 나타냅니다. 모든 종류의 SDK 샘플링(적응형 또는 고정 속도 샘플링)이 작동 중인 경우 실제 샘플링 속도를 나타내지 않습니다.

## <a name="when-to-use-sampling"></a>샘플링 사용 시기

일반적으로 대부분의 중소 규모의 응용 프로그램의 경우 샘플링이 필요하지 않습니다. 가장 유용한 진단 정보 및 가장 정확한 통계는 모든 사용자 활동에서 데이터를 수집하여 구합니다. 

샘플링의 주요 장점은 다음과 같습니다.

* 응용 프로그램 인사이트 서비스는 앱이 짧은 시간 간격으로 매우 높은 원격 분석 속도를 전송할 때 데이터 포인트를 삭제("제한")합니다. 샘플링은 응용 프로그램에서 제한이 발생할 가능성을 줄입니다.
* 가격 책정 계층에 대한 데이터 요소의 [할당량](pricing.md) 을 유지합니다. 
* 원격 분석의 컬렉션에서 네트워크 트래픽을 줄입니다. 

### <a name="which-type-of-sampling-should-i-use"></a>어떤 유형의 샘플링을 사용해야 합니까?

**다음과 같은 경우 수집 샘플링을 사용합니다.**

* 월별 원격 분석 할당량을 사용하는 경우가 많습니다.
* 사용자의 웹 브라우저에서 너무 많은 원격 분석을 받고 있습니다.
* 샘플링을 지원하지 않는 SDK 버전(예: ASP.NET 버전 2 이전)을 사용하는 경우

**다음과 같은 경우 고정 비율 샘플링을 사용합니다.**

* [Search에서](../../azure-monitor/app/diagnostic-search.md)이벤트를 조사할 때 페이지 뷰 및 HTTP 요청과 같은 클라이언트와 서버의 관련 이벤트 사이를 탐색할 수 있도록 클라이언트와 서버 간에 동기화된 샘플링을 원합니다.
* 사용자가 앱에 대한 적절한 샘플링 비율을 확신하는 경우입니다. 샘플링 비율은 정확한 메트릭을 가져오기 위해 충분히 높아야 하지만 가격 책정 할당량 및 조정 제한을 초과하는 비율보다 낮아야 합니다.

**다음과 같은 경우 적응 샘플링을 사용합니다.**

다른 형태의 샘플링을 사용하는 조건이 적용되지 않으면 적응 샘플링을 사용하는 것이 좋습니다. 이 설정은 ASP.NET/ASP.NET 코어 SDK에서 기본적으로 활성화됩니다. 특정 최소 속도에 도달할 때까지 트래픽을 줄이지 않으므로 사용량이 적은 사이트는 전혀 샘플링되지 않을 수 있습니다.

## <a name="knowing-whether-sampling-is-in-operation"></a>샘플링이 작동하는지 여부를 알 수 있습니다.

적용된 위치에 관계 없이 실제 샘플링 주기를 검색하려면 다음과 같은 [분석 쿼리](../../azure-monitor/app/analytics.md) 를 사용합니다.

```kusto
union requests,dependencies,pageViews,browserTimings,exceptions,traces
| where timestamp > ago(1d)
| summarize RetainedPercentage = 100/avg(itemCount) by bin(timestamp, 1h), itemType
```

모든 형식에 `RetainedPercentage` 대해 100보다 적으면 해당 유형의 원격 분석이 샘플링됩니다.

> [!IMPORTANT]
> Application Insights는 샘플링 기술에서 세션, 메트릭(사용자 지정 메트릭 포함) 또는 성능 카운터 원격 분석 유형을 샘플링하지 않습니다. 이러한 형식은 항상 정밀도 감소가 이러한 원격 분석 유형에 매우 바람직하지 않을 수 있기 때문에 샘플링에서 제외됩니다.

## <a name="how-sampling-works"></a>샘플링 작동 방식

샘플링 알고리즘은 삭제할 원격 분석 항목과 유지할 원격 분석 항목을 결정합니다. 샘플링은 SDK또는 응용 프로그램 인사이트 서비스에서 수행되는지 여부에 관계없이 마찬가지입니다. 샘플링 의사 결정은 상호 관련된 데이터 요소를 그대로 유지하려는 목표의 여러 규칙에 기반하며 이는 감소된 데이터 집합을 사용하더라도 실행할 수 있고 신뢰할 수 있는 Application Insights에서 진단 환경을 유지 관리합니다. 예를 들어 앱에 샘플에 포함된 실패한 요청이 있는 경우 추가 원격 분석 항목(예: 이 요청에 대해 기록된 예외 및 추적)이 유지됩니다. 샘플링은 모두 함께 유지하거나 삭제합니다. 결과적으로 Application Insights에서 요청 세부 정보를 볼 때 항상 연결된 원격 분석 항목과 함께 요청을 확인할 수 있습니다.

샘플링 결정은 요청의 작업 ID를 기반으로 하며, 이는 특정 작업에 속하는 모든 원격 분석 항목이 보존되거나 삭제된다는 것을 의미합니다. 작업 ID 집합이 없는 원격 분석 항목(예: HTTP 컨텍스트가 없는 비동기 스레드에서 보고된 원격 분석 항목)의 샘플링은 각 유형의 원격 분석 항목의 백분율을 캡처합니다.

원격 분석을 다시 표시하는 경우 Application Insights 서비스는 누락된 데이터 요소를 보완하기 위해 컬렉션의 시간에 사용된 동일한 샘플링 백분율로 메트릭을 조정합니다. 따라서 Application Insights에서 원격 분석을 보면 사용자는 실제 수에 가까운 통계적으로 올바른 근사치를 확인할 수 있습니다.

근사치의 정확도는 주로 구성된 샘플링 비율에 따라 다릅니다. 또한 아주 많은 사용자에게서 많은 양의 일반적으로 비슷한 요청을 처리하는 애플리케이션에 대해 정확도가 증가합니다. 반면에 상당한 부하가 있을 때 작동하지 않는 애플리케이션의 경우 이러한 애플리케이션이 일반적으로 할당량 내에 있으면서 제한에서 데이터 손실이 발생하지 않고 해당 원격 분석을 모두 보낼 수 있기에 샘플링은 필요하지 않습니다. 

## <a name="frequently-asked-questions"></a>질문과 대답

*ASP.NET 및 ASP.NET 코어 SDK의 기본 샘플링 동작은 무엇입니까?*

* 위의 SDK의 최신 버전 중 하나를 사용하는 경우 적응 샘플링은 기본적으로 초당 5개의 원격 분석 항목으로 활성화됩니다.
  기본적으로 두 `AdaptiveSamplingTelemetryProcessor` 개의 노드가 추가되고 하나는 `Event` 샘플링에 형식을 포함하고 다른 `Event` 노드는 샘플링에서 형식을 제외합니다. 이 구성은 SDK가 원격 분석 항목을 형식의 `Event` 5개 원격 분석 항목과 결합된 다른 모든 유형의 5개의 `Events` 원격 분석 항목으로 제한하여 다른 원격 분석 유형과 별도로 샘플링되도록 합니다. 이벤트는 일반적으로 비즈니스 원격 분석에 사용되며 진단 원격 분석 볼륨의 영향을 받지 않아야 합니다.
  
  다음은 생성된 기본 `ApplicationInsights.config` 파일을 보여 주며, ASP.NET Core에서는 코드에서 동일한 기본 동작이 활성화됩니다. 이 [페이지의 이전 섹션의 예제를](#configuring-adaptive-sampling-for-aspnet-core-applications) 사용하여 이 기본 동작을 변경합니다.

    ```xml
    <TelemetryProcessors>
        <Add Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.AdaptiveSamplingTelemetryProcessor, Microsoft.AI.ServerTelemetryChannel">
            <MaxTelemetryItemsPerSecond>5</MaxTelemetryItemsPerSecond>
            <ExcludedTypes>Event</ExcludedTypes>
        </Add>
        <Add Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.AdaptiveSamplingTelemetryProcessor, Microsoft.AI.ServerTelemetryChannel">
            <MaxTelemetryItemsPerSecond>5</MaxTelemetryItemsPerSecond>
            <IncludedTypes>Event</IncludedTypes>
        </Add>
    </TelemetryProcessors>
    ```

*원격 분석을 두 번 이상 샘플링할 수 있습니까?*

* 아니요. 샘플링TelemetryProcessor는 항목이 이미 샘플링된 경우 샘플링 고려 사항에서 항목을 무시합니다. 흡기 샘플링도 마찬가지이며, SDK 자체에서 이미 샘플링된 항목에는 샘플링이 적용되지 않습니다.

*샘플링은 왜 간단히 "각 원격 분석 형식의 X 퍼센트를 수집"하지 않습니까?*

* 이 샘플링 방법은 메트릭 근사치에서 높은 수준의 정밀도를 제공하지만 진단에 중요한 사용자, 세션 및 요청당 진단 데이터를 상호 연관시키는 기능을 중단합니다. 따라서 샘플링은 "앱 사용자의 X%에 대한 모든 원격 분석 항목 수집" 또는 "앱 요청의 X%에 대한 모든 원격 분석을 수집"과 같은 정책에서 더 잘 작동합니다. 요청과 연결되지 않은 원격 분석 항목(예: 백그라운드 비동기 처리)의 경우 대체는 "각 원격 분석 유형에 대해 모든 항목의 X%를 수집"하는 것입니다. 

*샘플링 비율은 시간이 지나면서 달라질 수 있습니까?*

* 예, 적응 샘플링은 원격 분석의 현재 관찰된 양에 따라 샘플링 비율을 점차적으로 변경합니다.

*고정 비율 샘플링을 사용하는 경우 내 앱에 가장 적합한 샘플링 비율을 어떻게 알 수 있습니까?*

* 한 가지 방법은 적응 샘플링으로 시작하고 안정적인 비율(위 질문 참조)을 찾은 다음 해당 비율을 사용하여 고정 비율 샘플링으로 전환하는 것입니다. 
  
    그렇지 않으면 추측해야 합니다. Application Insights에서 현재 원격 분석 사용을 분석하고, 발생하는 모든 제한을 관찰하며, 수집된 원격 분석의 양을 추정합니다. 선택된 가격 책정 계층과 함께 이러한 세 가지 입력은 수집된 원격 분석의 볼륨을 얼마나 줄여야 할지 제안합니다. 그러나 사용자 수가 증가하거나 원격 분석의 양이 약간 다르게 변화되면 추정치가 무효화될 수도 있습니다.

*샘플링 백분율을 너무 낮게 구성하면 어떻게 되나요?*

* 샘플링 비율이 지나치게 낮으면 지나치게 공격적인 샘플링이 발생하고 Application Insights가 데이터 볼륨 감소를 위해 데이터의 시각화를 보정하려고 할 때 근사치의 정확도가 감소합니다. 또한 드물게 실패하거나 느린 요청 중 일부가 샘플링될 수 있으므로 진단 환경이 부정적인 영향을 받을 수 있습니다.

*샘플링 백분율을 너무 높게 구성하면 어떻게 되나요?*

* 샘플링 백분율을 너무 높게 구성하면(충분히 공격적이지 않음) 수집된 원격 분석의 볼륨이 부족합니다. 여전히 제한에 관련된 원격 분석 데이터 손실이 발생할 수 있고 Application Insights를 사용하는 비용은 초과 요금때문에 계획된 것 보다 클 수 있습니다.

*샘플링을 어떤 플랫폼에서 사용할 수 있습니까?*

* SDK가 샘플링을 수행하지 않는 경우 특정 볼륨을 초과하는 모든 원격 분석에 대해 자동으로 수집 샘플링이 발생할 수 있습니다. 예를 들어 이전 버전의 ASP.NET SDK 또는 Java SDK를 사용하는 경우 이 구성이 작동합니다.
* 현재 ASP.NET 또는 ASP.NET 코어 SDK(Azure 또는 자체 서버에서 호스팅됨)를 사용하는 경우 기본적으로 적응 샘플링을 받을 수 있지만 위에서 설명한 대로 고정 요금으로 전환할 수 있습니다. 고정 비율 샘플링을 사용하면 SDK 브라우저는 자동으로 관련 이벤트를 샘플링하도록 동기화합니다. 
* 현재 Java 에이전트를 사용하는 경우 고정 `ApplicationInsights.json` 비율 샘플링을 켜도록 `ApplicationInsights.xml`구성할 수 있습니다(Java SDK의 경우 구성). 샘플링은 기본적으로 꺼져 있습니다. 고정 속도 샘플링을 사용하면 브라우저 SDK와 서버가 관련 이벤트를 샘플링하기 위해 자동으로 동기화됩니다.

*항상 보고 싶은 확실히 드문 이벤트가 있습니다. 이전의 샘플링 모듈에서 그 이벤트를 어떻게 가져올 수 있습니까?*

* 이를 달성하는 가장 좋은 방법은 아래와 같이 유지하려는 원격 `SamplingPercentage` 분석 항목에 100으로 설정하는 사용자 지정 원격 분석 [초기라이저를](../../azure-monitor/app/api-filtering-sampling.md#addmodify-properties-itelemetryinitializer)작성하는 것입니다. 초기화자는 원격 분석 프로세서(샘플링 포함)전에 실행되도록 보장되기 때문에 모든 샘플링 기법이 모든 샘플링 고려 사항에서 이 항목을 무시할 수 있습니다. 사용자 지정 원격 분석 초기화는 ASP.NET SDK, ASP.NET 코어 SDK, JavaScript SDK 및 Java SDK에서 사용할 수 있습니다. 예를 들어 ASP.NET SDK를 사용하여 원격 분석 초기화자를 구성할 수 있습니다.

    ```csharp
    public class MyTelemetryInitializer : ITelemetryInitializer
    {
        public void Initialize(ITelemetry telemetry)
        {
            if(somecondition)
            {
                ((ISupportSampling)telemetry).SamplingPercentage = 100;
            }
        }
    }
    ```

## <a name="older-sdk-versions"></a>이전 SDK 버전

적응 샘플링은 ASP.NET v2.0.0-beta3 이상, Microsoft.ApplicationInsights.AspNetCore SDK v2.2.0-beta1 이상에 대한 응용 프로그램 인사이트 SDK에 사용할 수 있으며 기본적으로 활성화됩니다.

고정 요금 샘플링은 2.0.0 및 Java SDK 버전 2.0.1 이후의 ASP.NET 버전에서 SDK의 기능입니다.

ASP.NET SDK의 v2.5.0-beta2 및 ASP.NET 코어 SDK의 v2.2.0-beta3 이전에는 샘플링 결정이 "사용자"(즉, 가장 일반적인 웹 응용 프로그램)를 정의하는 응용 프로그램에 대한 사용자 ID의 해시를 기반으로 했습니다. 사용자를 정의하지 않은 응용 프로그램 유형(예: 웹 서비스)의 경우 샘플링 결정은 요청의 작업 ID를 기반으로 했습니다. ASP.NET 및 ASP.NET 코어 SDK의 최신 버전은 샘플링 결정에 작업 ID를 사용합니다.

## <a name="next-steps"></a>다음 단계

* [필터링](../../azure-monitor/app/api-filtering-sampling.md) 은 SDK에서 보내는 항목을 더 엄격하게 제어할 수 있습니다.
* [응용 프로그램 인사이트를 통해 원격 분석 최적화](https://msdn.microsoft.com/magazine/mt808502.aspx)개발자 네트워크 문서를 읽어보십시오.
