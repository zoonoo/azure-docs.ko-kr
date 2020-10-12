---
title: Azure Monitor의 Get-Metric Application Insights
description: Azure Monitor를 사용 하 여 .NET 및 .NET Core 응용 프로그램에 대해 로컬로 미리 집계 된 메트릭을 캡처하기 위해 GetMetric () 호출을 효과적으로 사용 하는 방법을 알아봅니다 Application Insights
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 04/28/2020
ms.openlocfilehash: 7aacb951d449583c875c71f260957a9d3bc8c663
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "86517147"
---
# <a name="custom-metric-collection-in-net-and-net-core"></a>.NET 및 .NET Core의 사용자 지정 메트릭 컬렉션

Azure Monitor Application Insights .NET 및 .NET Core Sdk에는 사용자 지정 메트릭, 및를 수집 하는 두 가지 방법이 있습니다 `TrackMetric()` `GetMetric()` . 이러한 두 방법의 주요 차이점은 로컬 집계입니다. `TrackMetric()` 에 사전 집계가 없으므로 사전 집계가 `GetMetric()` 없습니다. 권장 되는 방법은 집계를 사용 하는 것입니다 `TrackMetric()` . 따라서 더 이상 사용자 지정 메트릭을 수집 하는 기본 방법이 아닙니다. 이 문서에서는 GetMetric () 메서드를 사용 하는 방법 및 작동 방식에 대 한 몇 가지 이유를 설명 합니다.

## <a name="trackmetric-versus-getmetric"></a>고가 및 GetMetric

`TrackMetric()` 메트릭을 나타내는 원시 원격 분석을 보냅니다. 각 값에 대해 단일 원격 분석 항목을 보내는 것은 비효율적입니다. `TrackMetric()` 는 모두 `TrackMetric(item)` 원격 분석 이니셜라이저 및 프로세서의 전체 SDK 파이프라인을 거치 므로 성능 측면 에서도 비효율적입니다. 와 달리 `TrackMetric()` 는 `GetMetric()` 로컬 사전 집계를 처리 한 다음 1 분의 고정 된 간격으로 집계 요약 메트릭을 제출 합니다. 따라서 두 번째 또는 그 밀리초 수준에서 일부 사용자 지정 메트릭을 면밀 하 게 모니터링 해야 하는 경우에는 1 분에 한 번만 모니터링 하는 저장소 및 네트워크 트래픽 비용을 발생 시킬 수 있습니다. 또한 집계 된 메트릭에 대해 전송 되어야 하는 원격 분석 항목의 총 수가 크게 감소 하기 때문에 제한 발생 위험이 크게 줄어듭니다.

Application Insights에서 및를 통해 수집 된 사용자 지정 메트릭은 `TrackMetric()` `GetMetric()` [샘플링](./sampling.md)대상이 아닙니다. 중요 한 메트릭을 샘플링 하면 이러한 메트릭을 기반으로 작성 된 경고가 불안정 해질 수 있는 시나리오가 발생할 수 있습니다. 사용자 지정 메트릭을 샘플링 하지 않으면 일반적으로 경고 임계값이 위반 되는 경우 경고가 발생 한다는 것을 확신할 수 있습니다.  그러나 사용자 지정 메트릭이 샘플링 되지 않으므로 몇 가지 잠재적인 문제가 있습니다.

초당 메트릭에 대 한 추세를 추적 해야 하는 경우 또는 보다 세분화 된 간격이 있으면 다음과 같은 결과가 발생할 수 있습니다.

- 데이터 저장소 비용이 증가 합니다. Azure Monitor 하는 데 전송 하는 데이터의 양과 관련 된 비용이 있습니다. 데이터를 더 많이 보내면 전체 모니터링 비용이 더 많이 듭니다.
- 네트워크 트래픽/성능 오버 헤드가 증가 합니다. 일부 시나리오에서는이로 인해 통화와 응용 프로그램 성능 비용이 모두 포함 될 수 있습니다.
- 수집 제한의 위험. 앱이 짧은 시간 간격으로 매우 높은 수준의 원격 분석을 보내는 경우 Azure Monitor 서비스는 ("제한") 데이터 요소를 삭제 합니다.

예를 들어, 제한을 샘플링 하는 경우 경고를 트리거하는 조건이 로컬로 발생 한 다음 너무 많은 데이터를 전송 하 여 수집 끝점에서 삭제 될 수 있기 때문에 제한으로 인해 경고가 누락 될 수 있습니다. `TrackMetric()`사용자 고유의 로컬 집계 논리를 구현 하지 않는 한를 사용 하지 않는 것이 좋습니다. 모든 인스턴스를 추적 하려고 하는 경우 지정 된 기간 동안 이벤트가 발생 하는 것 [`TrackEvent()`](./api-custom-events-metrics.md#trackevent) 이 더 적합할 수 있습니다. 사용자 지정 메트릭과 달리 사용자 지정 이벤트는 샘플링의 영향을 받습니다. 물론 `TrackMetric()` 로컬 사전 집계를 작성 하지 않고도 계속 사용할 수 있지만,이 경우에는 문제에 대해 알고 있어야 합니다.

요약 하자면 `GetMetric()` 사전 집계를 수행 하기 때문에 권장 되는 방법입니다. 모든 Track () 호출의 값을 누적 하 고 1 분 마다 요약/집계를 보냅니다. 이렇게 하면 더 적은 데이터 요소를 전송 하 고 모든 관련 정보를 수집 하 여 비용 및 성능 오버 헤드를 크게 줄일 수 있습니다.

> [!NOTE]
> .NET 및 .NET Core Sdk만 GetMetric () 메서드를 포함 합니다. Java를 사용 하는 경우 [마이크로 측정기 메트릭](./micrometer-java.md) 또는를 사용할 수 있습니다 `TrackMetric()` . Python의 경우 [OpenCensus](./opencensus-python.md#metrics) 를 사용 하 여 사용자 지정 메트릭을 보낼 수 있습니다. JavaScript 및 Node.js의 경우 계속 사용할 수 `TrackMetric()` 있지만 이전 섹션에서 설명한 주의 사항을 염두에 두어야 합니다.

## <a name="getting-started-with-getmetric"></a>GetMetric 시작

이 예제에서는 기본 .NET Core 3.1 작업자 서비스 응용 프로그램을 사용 합니다. 이러한 예제에 사용 된 테스트 환경을 정확 하 게 복제 하려면 [모니터링 작업자 서비스 문서](./worker-service.md#net-core-30-worker-service-application) 1-6의 단계에 따라 기본 작업자 서비스 프로젝트 템플릿에 Application Insights를 추가 합니다. 이러한 개념은 웹 앱 및 콘솔 앱을 포함 하 여 SDK를 사용할 수 있는 모든 일반 응용 프로그램에 적용 됩니다.

### <a name="sending-metrics"></a>메트릭 보내기

파일의 내용을 `worker.cs` 다음으로 바꿉니다.

```csharp
using System;
using System.Threading;
using System.Threading.Tasks;
using Microsoft.Extensions.Hosting;
using Microsoft.Extensions.Logging;
using Microsoft.ApplicationInsights;

namespace WorkerService3
{
    public class Worker : BackgroundService
    {
        private readonly ILogger<Worker> _logger;
        private TelemetryClient _telemetryClient;

        public Worker(ILogger<Worker> logger, TelemetryClient tc)
        {
            _logger = logger;
            _telemetryClient = tc;
        }

        protected override async Task ExecuteAsync(CancellationToken stoppingToken)
        {   // The following line demonstrates usages of GetMetric API.
            // Here "computersSold", a custom metric name, is being tracked with a value of 42 every second.
            while (!stoppingToken.IsCancellationRequested)
            {
                _telemetryClient.GetMetric("computersSold").TrackValue(42);

                _logger.LogInformation("Worker running at: {time}", DateTimeOffset.Now);
                await Task.Delay(1000, stoppingToken);
            }
        }
    }
}
```

위의 코드를 실행 하 고 Visual Studio 출력 창이 나 Telerik의 Fiddler와 같은 도구를 통해 전송 되는 원격 분석을 시청 하는 경우, 원격 분석을 전송 하지 않고 while 루프를 반복적으로 실행 하 고, 60 초 표시를 통해 단일 원격 분석 항목을 전송 합니다 .이는 테스트의 경우 다음과 같이 표시 됩니다. :

```json
Application Insights Telemetry: {"name":"Microsoft.ApplicationInsights.Dev.00000000-0000-0000-0000-000000000000.Metric", "time":"2019-12-28T00:54:19.0000000Z",
"ikey":"00000000-0000-0000-0000-000000000000",
"tags":{"ai.application.ver":"1.0.0.0",
"ai.cloud.roleInstance":"Test-Computer-Name",
"ai.internal.sdkVersion":"m-agg2c:2.12.0-21496",
"ai.internal.nodeName":"Test-Computer-Name"},
"data":{"baseType":"MetricData",
"baseData":{"ver":2,"metrics":[{"name":"computersSold",
"kind":"Aggregation",
"value":1722,
"count":41,
"min":42,
"max":42,
"stdDev":0}],
"properties":{"_MS.AggregationIntervalMs":"42000",
"DeveloperMode":"true"}}}}
```

이 단일 원격 분석 항목은 41 고유 메트릭 측정의 집계를 나타냅니다. 같은 값을 계속 해 서 전송 했으므로 동일한 *최대 (최대)* 값이 있는 0의 *표준 편차 (stDev)* 와 *최소 (min)* 값이 있습니다. *Value* 속성은 집계 된 모든 개별 값의 합계를 나타냅니다.

로그 (분석) 환경에서 Application Insights 리소스를 검토 하는 경우이 개별 원격 분석 항목은 다음과 같이 표시 됩니다.

![Log Analytics 쿼리 뷰](./media/get-metric/log-analytics.png)

> [!NOTE]
> 원시 원격 분석 항목에는 명시적 sum 속성/필드가 수집 한 번만 포함 되지 않았습니다. 이 경우와 속성은 모두 동일한 작업을 `value` `valueSum` 나타냅니다.

포털의 [_메트릭_](../platform/metrics-charts.md) 섹션에서 사용자 지정 메트릭 원격 분석에 액세스할 수도 있습니다. [로그 기반 및 사용자 지정 메트릭으로](pre-aggregated-metrics-log-metrics.md)모두 사용 됩니다. (아래 스크린샷은 로그 기반의 예입니다.) ![메트릭 탐색기 보기](./media/get-metric/metrics-explorer.png)

### <a name="caching-metric-reference-for-high-throughput-usage"></a>높은 처리량 사용을 위한 캐싱 메트릭 참조

경우에 따라 메트릭 값은 매우 자주 관찰 됩니다. 예를 들어 500 요청을 처리 하는 처리량이 높은 서비스는 각 요청에 대 한 20 개의 원격 분석 메트릭을 내보낼 수 있습니다. 이는 초당 1만 값을 추적 하는 것을 의미 합니다. 이러한 처리량이 높은 시나리오에서는 사용자가 일부 조회를 방지 하 여 SDK를 지원 해야 할 수 있습니다.

예를 들어이 경우 위의 예제에서는 메트릭 "ComputersSold"에 대 한 핸들 조회를 수행한 후 관찰 된 값 42을 추적 했습니다. 대신 여러 추적 호출에 대해 핸들이 캐시 될 수 있습니다.

```csharp
//...

        protected override async Task ExecuteAsync(CancellationToken stoppingToken)
        {
            // This is where the cache is stored to handle faster lookup
            Metric computersSold = _telemetryClient.GetMetric("ComputersSold");
            while (!stoppingToken.IsCancellationRequested)
            {

                computersSold.TrackValue(42);

                computersSold.TrackValue(142);

                _logger.LogInformation("Worker running at: {time}", DateTimeOffset.Now);
                await Task.Delay(50, stoppingToken);
            }
        }

```

메트릭 핸들을 캐시 하는 것 외에도 위의 예제에서는 `Task.Delay` 루프가 772 호출에 더 자주 실행 되도록를 50 밀리초로 줄였습니다 `TrackValue()` .

## <a name="multi-dimensional-metrics"></a>다차원 메트릭

이전 섹션의 예제는 0 차원 메트릭을 보여 줍니다. 메트릭은 다차원 일 수도 있습니다. 현재 최대 10 개의 차원을 지원 합니다.

 1 차원 메트릭을 만드는 방법의 예는 다음과 같습니다.

```csharp
//...

        protected override async Task ExecuteAsync(CancellationToken stoppingToken)
        {
            // This is an example of a metric with a single dimension.
            // FormFactor is the name of the dimension.
            Metric computersSold= _telemetryClient.GetMetric("ComputersSold", "FormFactor");

            while (!stoppingToken.IsCancellationRequested)
            {
                // The number of arguments (dimension values)
                // must match the number of dimensions specified while GetMetric.
                // Laptop, Tablet, etc are values for the dimension "FormFactor"
                computersSold.TrackValue(42, "Laptop");
                computersSold.TrackValue(20, "Tablet");
                computersSold.TrackValue(126, "Desktop");


                _logger.LogInformation("Worker running at: {time}", DateTimeOffset.Now);
                await Task.Delay(50, stoppingToken);
            }
        }

```

이 코드를 최소 60 초 동안 실행 하면 세 가지 폼 팩터 중 하나의 집계를 나타내는 세 개의 고유한 원격 분석 항목이 Azure로 전송 됩니다. 이전과 마찬가지로 로그 (분석) 보기에서 다음을 확인할 수 있습니다.

![다차원 메트릭의 Log analytics 보기](./media/get-metric/log-analytics-multi-dimensional.png)

또한 메트릭 탐색기 환경에서 다음을 수행 합니다.

![사용자 지정 메트릭](./media/get-metric/custom-metrics.png)

그러나 새 사용자 지정 차원에 따라 메트릭을 분할 하거나 메트릭 보기를 사용 하 여 사용자 지정 차원을 볼 수 없습니다.

![분할 지원](./media/get-metric/splitting-support.png)

기본적으로 메트릭 탐색기 환경 내의 다차원 메트릭은 Application Insights 리소스에서 설정 되지 않습니다.

### <a name="enable-multi-dimensional-metrics"></a>다차원 메트릭 사용

Application Insights 리소스에 대해 다차원 메트릭을 사용 하도록 설정 하려면 **사용량 및 예상 비용**사용자 지정 메트릭을 선택 하 여  >  **Custom Metrics**  >  **사용자 지정 메트릭 차원에 대 한 경고 사용**  >  **을**선택 합니다. 이에 대 한 자세한 내용은 [여기](pre-aggregated-metrics-log-metrics.md#custom-metrics-dimensions-and-pre-aggregation)에서 찾을 수 있습니다.

이러한 변경을 수행 하 고 새 다차원 원격 분석을 보내면 **분할을 적용할**수 있습니다.

> [!NOTE]
> 포털에서 기능이 설정 된 후 새로 전송 된 메트릭만 차원이 저장 됩니다.

![분할 적용](./media/get-metric/apply-splitting.png)

그리고 각 _Formfactor_ 차원에 대 한 메트릭 집계를 확인 합니다.

![폼 팩터](./media/get-metric/formfactor.png)

### <a name="how-to-use-metricidentifier-when-there-are-more-than-three-dimensions"></a>3 개 이상의 차원이 있을 때 MetricIdentifier를 사용 하는 방법

현재 10 개의 차원이 지원 되지만 3 차원 이상에서는를 사용 해야 합니다 `MetricIdentifier` .

```csharp
// Add "using Microsoft.ApplicationInsights.Metrics;" to use MetricIdentifier
// MetricIdentifier id = new MetricIdentifier("[metricNamespace]","[metricId],"[dim1]","[dim2]","[dim3]","[dim4]","[dim5]");
MetricIdentifier id = new MetricIdentifier("CustomMetricNamespace","ComputerSold", "FormFactor", "GraphicsCard", "MemorySpeed", "BatteryCapacity", "StorageCapacity");
Metric computersSold  = _telemetryClient.GetMetric(id);
computersSold.TrackValue(110,"Laptop", "Nvidia", "DDR4", "39Wh", "1TB");
```

## <a name="custom-metric-configuration"></a>사용자 지정 메트릭 구성

메트릭 구성을 변경 하려면 메트릭이 초기화 된 위치에서이 작업을 수행 해야 합니다.

### <a name="special-dimension-names"></a>특수 차원 이름

메트릭에 액세스 하는 데 사용 되는의 원격 분석 컨텍스트를 사용 하지 않습니다 `TelemetryClient` . 클래스의 상수로 사용할 수 있는 특수 차원 이름이 `MetricDimensionNames` 이 제한에 대 한 최상의 해결 방법입니다.

아래 "특수 작업 요청 크기"에 의해 전송 된 메트릭 집계-메트릭은 **not** `Context.Operation.Name` "특수 작업"으로 설정 되지 않습니다. `TrackMetric()`또는 다른 모든 조합 xxx ()는 `OperationName` "특수 작업"으로 올바르게 설정 됩니다.

``` csharp
        //...
        TelemetryClient specialClient;
        private static int GetCurrentRequestSize()
        {
            // Do stuff
            return 1100;
        }
        int requestSize = GetCurrentRequestSize()

        protected override async Task ExecuteAsync(CancellationToken stoppingToken)
        {
            while (!stoppingToken.IsCancellationRequested)
            {
                //...
                specialClient.Context.Operation.Name = "Special Operation";
                specialClient.GetMetric("Special Operation Request Size").TrackValue(requestSize);
                //...
            }
                   
        }
```

이 경우 값을 지정 하기 위해 클래스에 나열 된 특수 차원 이름을 사용 `MetricDimensionNames` `TelemetryContext` 합니다.

예를 들어 다음 문으로 생성 된 메트릭 집계가 Application Insights 클라우드 끝점으로 전송 되는 경우 해당 `Context.Operation.Name` 데이터 필드는 "특수 작업"으로 설정 됩니다.

```csharp
_telemetryClient.GetMetric("Request Size", MetricDimensionNames.TelemetryContext.Operation.Name).TrackValue(requestSize, "Special Operation");
```

이 특수 차원의 값은에 복사 되 `TelemetryContext` 고 ' normal ' 차원으로 사용 되지 않습니다. 표준 메트릭 탐색을 위한 작업 차원도 유지 하려는 경우 해당 목적을 위해 별도의 차원을 만들어야 합니다.

```csharp
_telemetryClient.GetMetric("Request Size", "Operation Name", MetricDimensionNames.TelemetryContext.Operation.Name).TrackValue(requestSize, "Special Operation", "Special Operation");
```

### <a name="dimension-and-time-series-capping"></a>차원 및 시계열 50,

 원격 분석 하위 시스템이 실수로 리소스를 사용 하는 것을 방지 하기 위해 메트릭 당 최대 데이터 계열 수를 제어할 수 있습니다. 기본 제한 값은 메트릭 당 총 1000 개의 데이터 계열이 며 각 차원에는 100 개 이하로 제한 됩니다.

 차원 및 시계열 50,의 컨텍스트에서는를 사용 하 여 `Metric.TrackValue(..)` 한도가 관찰 되도록 합니다. 제한에 이미 도달한 경우 `Metric.TrackValue(..)` 는 "False"를 반환 하 고 값은 추적 되지 않습니다. 그렇지 않으면 "True"를 반환 합니다. 이는 메트릭에 대 한 데이터가 사용자 입력에서 발생 하는 경우에 유용 합니다.

`MetricConfiguration`생성자는 `IMetricSeriesConfiguration` 각 메트릭의 각 계열에 대 한 집계 동작을 지정 하는을 구현 하는 클래스의 개체와 각 메트릭에 있는 여러 계열을 관리 하는 방법에 대 한 몇 가지 옵션을 사용 합니다.

``` csharp
var metConfig = new MetricConfiguration(seriesCountLimit: 100, valuesPerDimensionLimit:2,
                new MetricSeriesConfigurationForMeasurement(restrictToUInt32Values: false));

Metric computersSold = _telemetryClient.GetMetric("ComputersSold", "Dimension1", "Dimension2", metConfig);

// Start tracking.
computersSold.TrackValue(100, "Dim1Value1", "Dim2Value1");
computersSold.TrackValue(100, "Dim1Value1", "Dim2Value2");

// The following call gives 3rd unique value for dimension2, which is above the limit of 2.
computersSold.TrackValue(100, "Dim1Value1", "Dim2Value3");
// The above call does not track the metric, and returns false.
```

* `seriesCountLimit` 메트릭에 포함할 수 있는 데이터 시계열의 최대 개수입니다. 이 한도에 도달 하면를 호출 `TrackValue()` 합니다.
* `valuesPerDimensionLimit` 비슷한 방식으로 차원 마다 고유한 값의 수를 제한 합니다.
* `restrictToUInt32Values` 음수가 아닌 정수 값만 추적 해야 하는지 여부를 결정 합니다.

다음은 cap 제한을 초과 하는지 확인 하는 메시지를 보내는 방법의 예입니다.

```csharp
if (! computersSold.TrackValue(100, "Dim1Value1", "Dim2Value3"))
{
// Add "using Microsoft.ApplicationInsights.DataContract;" to use SeverityLevel.Error
_telemetryClient.TrackTrace("Metric value not tracked as value of one of the dimension exceeded the cap. Revisit the dimensions to ensure they are within the limits",
SeverityLevel.Error);
}
```

## <a name="next-steps"></a>다음 단계

* Worker 서비스 응용 프로그램 모니터링에 [대해 자세히 알아보세요 ](./worker-service.md).
* [로그 기반 및 미리 집계 된 메트릭에](./pre-aggregated-metrics-log-metrics.md)대 한 자세한 내용은을 참조 하세요.
* [메트릭 탐색기](../platform/metrics-getting-started.md)
* [ASP.NET Core 응용 프로그램](asp-net-core.md) 에 대해 Application Insights를 사용 하도록 설정 하는 방법
* [ASP.NET 응용 프로그램](asp-net.md) 에 대해 Application Insights를 사용 하도록 설정 하는 방법
