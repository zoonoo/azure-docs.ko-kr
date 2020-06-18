---
title: Azure Monitor(미리 보기)로 Python 애플리케이션 모니터링 | Microsoft Docs
description: OpenCensus Python과 Azure Monitor를 연결하는 지침을 제공합니다.
ms.topic: conceptual
author: reyang
ms.author: reyang
ms.date: 10/11/2019
ms.reviewer: mbullwin
ms.openlocfilehash: 6b8343d08962d8ce749e1160b0226b68571571f8
ms.sourcegitcommit: fc0431755effdc4da9a716f908298e34530b1238
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/24/2020
ms.locfileid: "83815726"
---
# <a name="set-up-azure-monitor-for-your-python-application"></a>Python 애플리케이션용 Azure Monitor 설정

Azure Monitor는 [OpenCensus](https://opencensus.io)와의 통합을 통해 분산 추적, 메트릭 컬렉션 및 Python 애플리케이션의 로깅을 지원합니다. 이 문서에서는 Python용 OpenCensus를 설정하고 Azure Monitor로 모니터링 데이터를 전송하는 프로세스를 단계별로 안내합니다.

## <a name="prerequisites"></a>사전 요구 사항

- Azure 구독 Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/)을 만듭니다.
- Python 설치. 이전 버전의 경우 약간 변경되어 작동할 가능성이 있으므로 이 문서에서는 [Python 3.7.0](https://www.python.org/downloads/)을 사용합니다.

## <a name="sign-in-to-the-azure-portal"></a>Azure Portal에 로그인

[Azure Portal](https://portal.azure.com/)에 로그인합니다.

## <a name="create-an-application-insights-resource-in-azure-monitor"></a>Azure Monitor에서 Application Insights 리소스 만들기

먼저 계측 키(ikey)를 생성할 Application Insights 리소스를 Azure Monitor에서 만들어야 합니다. ikey를 사용하여 OpenCensus SDK를 구성하고 원격 분석 데이터를 Azure Monitor로 전송합니다.

1. **리소스 만들기** > **개발자 도구** > **Application Insights**를 선택합니다.

   ![Application Insights 리소스 추가](./media/opencensus-python/0001-create-resource.png)

1. 구성 상자가 표시됩니다. 다음 표를 사용하여 입력 필드를 채웁니다.

   | 설정        | 값           | Description  |
   | ------------- |:-------------|:-----|
   | **이름**      | 전역적으로 고유한 값 | 모니터링 중인 앱을 식별하는 이름 |
   | **리소스 그룹**     | myResourceGroup      | Application Insights 데이터를 호스팅할 새 리소스 그룹의 이름 |
   | **위치** | 미국 동부 | 가까운 위치 또는 앱이 호스팅되는 위치 근처 |

1. **만들기**를 선택합니다.

## <a name="instrument-with-opencensus-python-sdk-for-azure-monitor"></a>Azure Monitor용 OpenCensus Python SDK를 사용한 계측

OpenCensus Azure Monitor 내보내기를 설치합니다.

```console
python -m pip install opencensus-ext-azure
```

패키지 및 통합의 전체 목록은 [OpenCensus 패키지](https://docs.microsoft.com/azure/azure-monitor/app/nuget#common-packages-for-python-using-opencensus)를 참조하세요.

> [!NOTE]
> `python -m pip install opencensus-ext-azure` 명령은 Python 설치에 대해 `PATH` 환경 변수를 설정했다고 가정합니다. 이 변수를 구성하지 않은 경우 Python 실행 파일이 위치하는 전체 디렉터리 경로를 제공해야 합니다. 결과로 나온 명령은 다음과 같습니다. `C:\Users\Administrator\AppData\Local\Programs\Python\Python37-32\python.exe -m pip install opencensus-ext-azure`.

SDK는 세 가지 Azure Monitor 내보내기를 사용하여 Azure Monitor에 대한 다양한 유형의 원격 분석(추적, 메트릭 및 로그)을 전송합니다. 이러한 원격 분석 유형에 대한 자세한 내용은 [데이터 플랫폼 개요](https://docs.microsoft.com/azure/azure-monitor/platform/data-platform)를 참조하세요. 다음 지침을 사용하여 세 가지 내보내기를 통해 이러한 원격 분석 유형을 전송합니다.

## <a name="telemetry-type-mappings"></a>원격 분석 유형 매핑

다음은 OpenCensus에서 제공하여 Azure Monitor에서 볼 수 있는 원격 분석 유형에 매핑되는 내보내기입니다.

![OpenCensus에서 Azure Monitor로의 원격 분석 유형 매핑 스크린샷](./media/opencensus-python/0012-telemetry-types.png)

### <a name="trace"></a>추적

> [!NOTE]
> OpenCensus의 `Trace`는 [분산 추적](https://docs.microsoft.com/azure/azure-monitor/app/distributed-tracing)을 의미합니다. `AzureExporter`는 `requests` 및 `dependency` 원격 분석을 Azure Monitor로 전송합니다.

1. 먼저 일부 추적 데이터를 로컬로 생성해 보겠습니다. Python IDLE에서 또는 원하는 편집기에서 다음 코드를 입력합니다.

    ```python
    from opencensus.trace.samplers import ProbabilitySampler
    from opencensus.trace.tracer import Tracer

    tracer = Tracer(sampler=ProbabilitySampler(1.0))

    def valuePrompt():
        with tracer.span(name="test") as span:
            line = input("Enter a value: ")
            print(line)

    def main():
        while True:
            valuePrompt()

    if __name__ == "__main__":
        main()
    ```

2. 코드를 실행하면 값을 입력하라는 메시지가 반복적으로 표시됩니다. 각 항목을 사용하여 값은 셸에 출력되고, OpenCensus Python Module에서 `SpanData`의 해당 부분을 생성합니다. OpenCensus 프로젝트는 [범위 트리로 추적](https://opencensus.io/core-concepts/tracing/)을 정의합니다.
    
    ```
    Enter a value: 4
    4
    [SpanData(name='test', context=SpanContext(trace_id=8aa41bc469f1a705aed1bdb20c342603, span_id=None, trace_options=TraceOptions(enabled=True), tracestate=None), span_id='15ac5123ac1f6847', parent_span_id=None, attributes=BoundedDict({}, maxlen=32), start_time='2019-06-27T18:21:22.805429Z', end_time='2019-06-27T18:21:44.933405Z', child_span_count=0, stack_trace=None, annotations=BoundedList([], maxlen=32), message_events=BoundedList([], maxlen=128), links=BoundedList([], maxlen=32), status=None, same_process_as_parent_span=None, span_kind=0)]
    Enter a value: 25
    25
    [SpanData(name='test', context=SpanContext(trace_id=8aa41bc469f1a705aed1bdb20c342603, span_id=None, trace_options=TraceOptions(enabled=True), tracestate=None), span_id='2e512f846ba342de', parent_span_id=None, attributes=BoundedDict({}, maxlen=32), start_time='2019-06-27T18:21:44.933405Z', end_time='2019-06-27T18:21:46.156787Z', child_span_count=0, stack_trace=None, annotations=BoundedList([], maxlen=32), message_events=BoundedList([], maxlen=128), links=BoundedList([], maxlen=32), status=None, same_process_as_parent_span=None, span_kind=0)]
    Enter a value: 100
    100
    [SpanData(name='test', context=SpanContext(trace_id=8aa41bc469f1a705aed1bdb20c342603, span_id=None, trace_options=TraceOptions(enabled=True), tracestate=None), span_id='f3f9f9ee6db4740a', parent_span_id=None, attributes=BoundedDict({}, maxlen=32), start_time='2019-06-27T18:21:46.157732Z', end_time='2019-06-27T18:21:47.269583Z', child_span_count=0, stack_trace=None, annotations=BoundedList([], maxlen=32), message_events=BoundedList([], maxlen=128), links=BoundedList([], maxlen=32), status=None, same_process_as_parent_span=None, span_kind=0)]
    ```

3. 시연을 위해 값을 입력하는 것이 유용하지만 결국은 `SpanData`를 Azure Monitor로 내보내야 합니다. 연결 문자열을 내보내기에 직접 전달하거나 `APPLICATIONINSIGHTS_CONNECTION_STRING`환경 변수에 이를 지정할 수 있습니다. 다음 코드 샘플을 기반으로 이전 단계에서 코드를 수정합니다.

    ```python
    from opencensus.ext.azure.trace_exporter import AzureExporter
    from opencensus.trace.samplers import ProbabilitySampler
    from opencensus.trace.tracer import Tracer
    
    # TODO: replace the all-zero GUID with your instrumentation key.
    tracer = Tracer(
        exporter=AzureExporter(
            connection_string='InstrumentationKey=00000000-0000-0000-0000-000000000000'),
        sampler=ProbabilitySampler(1.0),
    )

    def valuePrompt():
        with tracer.span(name="test") as span:
            line = input("Enter a value: ")
            print(line)

    def main():
        while True:
            valuePrompt()

    if __name__ == "__main__":
        main()
    ```

4. 이제 Python 스크립트를 실행할 때 값을 입력하라는 메시지가 여전히 표시되지만 이제는 값만 셸에 인쇄됩니다. 만들어진 `SpanData`는 Azure Monitor로 전송됩니다. `dependencies`에서 내보내진 범위 데이터를 찾을 수 있습니다. 나가는 요청에 대한 자세한 내용은 OpenCensus Python [종속성](https://docs.microsoft.com/azure/azure-monitor/app/opencensus-python-dependency)을 참조하세요.
들어오는 요청에 대한 자세한 내용은 OpenCensus Python [요청](https://docs.microsoft.com/azure/azure-monitor/app/opencensus-python-request)을 참조하세요.

#### <a name="sampling"></a>샘플링

OpenCensus의 샘플링에 대한 내용은 [OpenCensus의 샘플링](sampling.md#configuring-fixed-rate-sampling-for-opencensus-python-applications)을 참조하세요.

#### <a name="trace-correlation"></a>추적 상관 관계

추적 데이터의 원격 분석 상관 관계에 대한 자세한 내용은 OpenCensus Python [원격 분석 상관 관계](https://docs.microsoft.com/azure/azure-monitor/app/correlation#telemetry-correlation-in-opencensus-python)를 참조하세요.

#### <a name="modify-telemetry"></a>원격 분석 수정

Azure Monitor로 전송되기 전에 추적된 원격 분석을 수정하는 방법에 대한 자세한 내용은 OpenCensus Python [원격 분석 프로세서](https://docs.microsoft.com/azure/azure-monitor/app/api-filtering-sampling#opencensus-python-telemetry-processors)를 참조하세요.

### <a name="metrics"></a>메트릭

1. 먼저 일부 로컬 메트릭 데이터를 생성해 보겠습니다. 사용자가 Enter 키를 누르는 횟수를 추적하는 단순한 메트릭을 만들겠습니다.

    ```python
    from datetime import datetime
    from opencensus.stats import aggregation as aggregation_module
    from opencensus.stats import measure as measure_module
    from opencensus.stats import stats as stats_module
    from opencensus.stats import view as view_module
    from opencensus.tags import tag_map as tag_map_module

    stats = stats_module.stats
    view_manager = stats.view_manager
    stats_recorder = stats.stats_recorder
    
    prompt_measure = measure_module.MeasureInt("prompts",
                                               "number of prompts",
                                               "prompts")
    prompt_view = view_module.View("prompt view",
                                   "number of prompts",
                                   [],
                                   prompt_measure,
                                   aggregation_module.CountAggregation())
    view_manager.register_view(prompt_view)
    mmap = stats_recorder.new_measurement_map()
    tmap = tag_map_module.TagMap()

    def prompt():
        input("Press enter.")
        mmap.measure_int_put(prompt_measure, 1)
        mmap.record(tmap)
        metrics = list(mmap.measure_to_view_map.get_metrics(datetime.utcnow()))
        print(metrics[0].time_series[0].points[0])

    def main():
        while True:
            prompt()

    if __name__ == "__main__":
        main()
    ```
2. 코드를 실행하면 Enter 키를 누르라는 메시지가 반복적으로 표시됩니다. Enter 키를 누르는 횟수를 추적하는 메트릭이 생성됩니다. 각 항목을 사용하면 값이 증분되고 메트릭 정보는 콘솔에 표시됩니다. 이 정보에는 메트릭이 업데이트된 현재 값 및 현재 타임스탬프가 포함됩니다.

    ```
    Press enter.
    Point(value=ValueLong(5), timestamp=2019-10-09 20:58:04.930426)
    Press enter.
    Point(value=ValueLong(6), timestamp=2019-10-09 20:58:06.570167)
    Press enter.
    Point(value=ValueLong(7), timestamp=2019-10-09 20:58:07.138614)
    ```

3. 시연을 위해 값을 입력하는 것이 유용하지만 결국은 메트릭 데이터를 Azure Monitor로 내보내야 합니다. 연결 문자열을 내보내기에 직접 전달하거나 `APPLICATIONINSIGHTS_CONNECTION_STRING`환경 변수에 이를 지정할 수 있습니다. 다음 코드 샘플을 기반으로 이전 단계에서 코드를 수정합니다.

    ```python
    from datetime import datetime
    from opencensus.ext.azure import metrics_exporter
    from opencensus.stats import aggregation as aggregation_module
    from opencensus.stats import measure as measure_module
    from opencensus.stats import stats as stats_module
    from opencensus.stats import view as view_module
    from opencensus.tags import tag_map as tag_map_module

    stats = stats_module.stats
    view_manager = stats.view_manager
    stats_recorder = stats.stats_recorder
    
    prompt_measure = measure_module.MeasureInt("prompts",
                                               "number of prompts",
                                               "prompts")
    prompt_view = view_module.View("prompt view",
                                   "number of prompts",
                                   [],
                                   prompt_measure,
                                   aggregation_module.CountAggregation())
    view_manager.register_view(prompt_view)
    mmap = stats_recorder.new_measurement_map()
    tmap = tag_map_module.TagMap()

    # TODO: replace the all-zero GUID with your instrumentation key.
    exporter = metrics_exporter.new_metrics_exporter(
        connection_string='InstrumentationKey=00000000-0000-0000-0000-000000000000')

    view_manager.register_exporter(exporter)

    def prompt():
        input("Press enter.")
        mmap.measure_int_put(prompt_measure, 1)
        mmap.record(tmap)
        metrics = list(mmap.measure_to_view_map.get_metrics(datetime.utcnow()))
        print(metrics[0].time_series[0].points[0])

    def main():
        while True:
            prompt()

    if __name__ == "__main__":
        main()
    ```

4. 내보내기는 고정된 간격으로 메트릭 데이터를 Azure Monitor로 전송합니다. 기본값은 15초 간격입니다. 단일 메트릭을 추적하므로 어떤 값과 타임스탬프가 포함되는지와 무관하게 이 메트릭 데이터는 간격마다 전송됩니다. `customMetrics`에서 데이터를 찾을 수 있습니다.

#### <a name="standard-metrics"></a>표준 메트릭

기본적으로 메트릭 내보내기는 Azure Monitor에 표준 메트릭 집합을 전송합니다. 메트릭 내보내기의 생성자에서 `enable_standard_metrics` 플래그를 `False`로 설정하여 이를 사용하지 않도록 설정할 수 있습니다.

```python
...
exporter = metrics_exporter.new_metrics_exporter(
  enable_standard_metrics=False,
  connection_string='InstrumentationKey=<your-instrumentation-key-here>')
...
```
다음은 현재 전송되는 표준 메트릭의 목록입니다.

- 사용 가능한 메모리(바이트)
- CPU 프로세서 시간(백분율)
- 들어오는 요청 빈도(초당)
- 들어오는 요청 평균 실행 시간(밀리초)
- 보내는 요청 빈도(초당)
- 프로세스 CPU 사용량(백분율)
- 프로세스 프라이빗 바이트(바이트)

`performanceCounters`에서 이러한 메트릭을 볼 수 있어야 합니다. 들어오는 요청 빈도는 `customMetrics`에 있습니다. 자세한 내용은 [성능 카운터](https://docs.microsoft.com/azure/azure-monitor/app/performance-counters)를 참조하세요.

#### <a name="modify-telemetry"></a>원격 분석 수정

Azure Monitor로 전송되기 전에 추적된 원격 분석을 수정하는 방법에 대한 자세한 내용은 OpenCensus Python [원격 분석 프로세서](https://docs.microsoft.com/azure/azure-monitor/app/api-filtering-sampling#opencensus-python-telemetry-processors)를 참조하세요.

### <a name="logs"></a>로그

1. 먼저 일부 로컬 로그 데이터를 생성해 보겠습니다.

    ```python
    import logging

    logger = logging.getLogger(__name__)

    def valuePrompt():
        line = input("Enter a value: ")
        logger.warning(line)

    def main():
        while True:
            valuePrompt()

    if __name__ == "__main__":
        main()
    ```

2.  코드에서 지속적으로 값을 입력하도록 요청합니다. 입력한 모든 값에 대해 로그 항목이 내보내집니다.

    ```
    Enter a value: 24
    24
    Enter a value: 55
    55
    Enter a value: 123
    123
    Enter a value: 90
    90
    ```

3. 시연을 위해 값을 입력하는 것이 유용하지만 결국은 로그 데이터를 Azure Monitor로 내보내야 합니다. 연결 문자열을 내보내기에 직접 전달하거나 `APPLICATIONINSIGHTS_CONNECTION_STRING`환경 변수에 이를 지정할 수 있습니다. 다음 코드 샘플을 기반으로 이전 단계에서 코드를 수정합니다.

    ```python
    import logging
    from opencensus.ext.azure.log_exporter import AzureLogHandler
    
    logger = logging.getLogger(__name__)
    
    # TODO: replace the all-zero GUID with your instrumentation key.
    logger.addHandler(AzureLogHandler(
        connection_string='InstrumentationKey=00000000-0000-0000-0000-000000000000')
    )
    
    def valuePrompt():
        line = input("Enter a value: ")
        logger.warning(line)
    
    def main():
        while True:
            valuePrompt()
    
    if __name__ == "__main__":
        main()
    ```

4. 내보내기에서 로그 데이터를 Azure Monitor으로 전송합니다. `traces`에서 데이터를 찾을 수 있습니다. 

    > [!NOTE]
    > 이 컨텍스트에서 `traces`는 `Tracing`과 동일하지 않습니다. `traces`는 `AzureLogHandler`를 활용할 때 Azure Monitor에 표시되는 원격 분석 유형을 나타냅니다. `Tracing`는 OpenCensus의 개념을 의미하며 [분산 추적](https://docs.microsoft.com/azure/azure-monitor/app/distributed-tracing)과 관련이 있습니다.

5. 로그 메시지의 형식을 지정하려면 기본 제공 Python [로깅 API](https://docs.python.org/3/library/logging.html#formatter-objects)에서 `formatters`를 사용할 수 있습니다.

    ```python
    import logging
    from opencensus.ext.azure.log_exporter import AzureLogHandler
    
    logger = logging.getLogger(__name__)
    
    format_str = '%(asctime)s - %(levelname)-8s - %(message)s'
    date_format = '%Y-%m-%d %H:%M:%S'
    formatter = logging.Formatter(format_str, date_format)
    # TODO: replace the all-zero GUID with your instrumentation key.
    handler = AzureLogHandler(
        connection_string='InstrumentationKey=00000000-0000-0000-0000-000000000000')
    handler.setFormatter(formatter)
    logger.addHandler(handler)
    
    def valuePrompt():
        line = input("Enter a value: ")
        logger.warning(line)
    
    def main():
        while True:
            valuePrompt()
    
    if __name__ == "__main__":
        main()
    ```

6. custom_dimensions 필드를 사용하는 추가 키워드 인수의 로그 메시지에 사용자 지정 속성을 추가할 수도 있습니다. 이는 Azure Monitor에 `customDimensions` 키-값 쌍으로 표시됩니다.
    > [!NOTE]
    > 이 기능이 작동하려면 custom_dimensions 필드에 사전을 전달해야 합니다. 다른 유형의 인수를 전달하면 로거가 이를 무시합니다.

    ```python
    import logging
    
    from opencensus.ext.azure.log_exporter import AzureLogHandler
    
    logger = logging.getLogger(__name__)
    # TODO: replace the all-zero GUID with your instrumentation key.
    logger.addHandler(AzureLogHandler(
        connection_string='InstrumentationKey=00000000-0000-0000-0000-000000000000')
    )

    properties = {'custom_dimensions': {'key_1': 'value_1', 'key_2': 'value_2'}}

    # Use properties in logging statements
    logger.warning('action', extra=properties)
    ```

#### <a name="sending-exceptions"></a>예외 보내기

OpenCensus Python은 `exception` 원격 분석을 자동으로 추적하고 전송하지 않습니다. Python 로깅 라이브러리를 통해 예외를 사용하여 `AzureLogHandler`를 통해 전송됩니다. 일반 로깅과 마찬가지로 사용자 지정 속성을 추가할 수 있습니다.

```python
import logging

from opencensus.ext.azure.log_exporter import AzureLogHandler

logger = logging.getLogger(__name__)
# TODO: replace the all-zero GUID with your instrumentation key.
logger.addHandler(AzureLogHandler(
    connection_string='InstrumentationKey=00000000-0000-0000-0000-000000000000')
)

properties = {'custom_dimensions': {'key_1': 'value_1', 'key_2': 'value_2'}}

# Use properties in exception logs
try:
    result = 1 / 0  # generate a ZeroDivisionError
except Exception:
    logger.exception('Captured an exception.', extra=properties)
```
예외를 명시적으로 로깅해야 하므로 처리되지 않은 예외를 기록하는 방법은 사용자마다 다릅니다. OpenCensus는 예외 원격 분석을 명시적으로 기록하는 한 사용자가 이 작업을 수행하는 방법에 제한을 두지 않습니다.

#### <a name="sampling"></a>샘플링

OpenCensus의 샘플링에 대한 내용은 [OpenCensus의 샘플링](sampling.md#configuring-fixed-rate-sampling-for-opencensus-python-applications)을 참조하세요.

#### <a name="log-correlation"></a>로그 상관 관계

추적 컨텍스트 데이터를 통해 로그를 보강하는 방법에 대한 자세한 내용은 OpenCensus Python [로그 통합](https://docs.microsoft.com/azure/azure-monitor/app/correlation#log-correlation)을 참조하세요.

#### <a name="modify-telemetry"></a>원격 분석 수정

Azure Monitor로 전송되기 전에 추적된 원격 분석을 수정하는 방법에 대한 자세한 내용은 OpenCensus Python [원격 분석 프로세서](https://docs.microsoft.com/azure/azure-monitor/app/api-filtering-sampling#opencensus-python-telemetry-processors)를 참조하세요.

## <a name="view-your-data-with-queries"></a>쿼리를 사용하여 데이터 보기

**로그(분석)** 탭을 통해 애플리케이션에서 전송된 원격 분석 데이터를 볼 수 있습니다.

![붉은색 상자로 "로그(분석)"가 선택된 개요 창의 스크린샷](./media/opencensus-python/0010-logs-query.png)

**활성** 목록:

- Azure Monitor 추적 내보내기를 사용하여 전송되는 원격 분석의 경우 들어오는 요청은 `requests`에 표시됩니다. 나가는 또는 진행 중인 요청은 `dependencies`에 표시됩니다.
- Azure Monitor 메트릭 내보내기로 전송된 원격 분석의 경우 전송된 메트릭은 `customMetrics`에 표시됩니다.
- Azure Monitor 로그 내보내기를 사용하여 전송되는 원격 분석의 경우 로그는 `traces`에 표시됩니다. 예외는 `exceptions`에 표시됩니다.

쿼리 및 로그를 사용하는 방법에 대한 자세한 내용은 [Azure Monitor의 로그](https://docs.microsoft.com/azure/azure-monitor/platform/data-platform-logs)를 참조하세요.

## <a name="learn-more-about-opencensus-for-python"></a>Python용 OpenCensus에 대해 자세히 알아보기

* [GitHub의 OpenCensus Python](https://github.com/census-instrumentation/opencensus-python)
* [사용자 지정](https://github.com/census-instrumentation/opencensus-python/blob/master/README.rst#customization)
* [GitHub의 Azure Monitor 내보내기](https://github.com/census-instrumentation/opencensus-python/tree/master/contrib/opencensus-ext-azure)
* [OpenCensus 통합](https://github.com/census-instrumentation/opencensus-python#extensions)
* [Azure Monitor 샘플 애플리케이션](https://github.com/Azure-Samples/azure-monitor-opencensus-python)

## <a name="next-steps"></a>다음 단계

* [들어오는 요청 추적](./../../azure-monitor/app/opencensus-python-dependency.md)
* [나가는 요청 추적](./../../azure-monitor/app/opencensus-python-request.md)
* [애플리케이션 맵](./../../azure-monitor/app/app-map.md)
* [엔드투엔드 성능 모니터링](./../../azure-monitor/learn/tutorial-performance.md)

### <a name="alerts"></a>경고

* [가용성 테스트](../../azure-monitor/app/monitor-web-app-availability.md): 테스트를 만들어 사이트가 웹에 표시되는지 확인합니다.
* [스마트 진단](../../azure-monitor/app/proactive-diagnostics.md): 이 테스트는 자동으로 실행되므로 아무것도 설정할 필요가 없습니다. 앱이 실패한 요청으로 비정상적인 속도를 보일 경우 알려줍니다.
* [메트릭 경고](../../azure-monitor/platform/alerts-log.md): 메트릭이 임계값을 초과할 경우 경고 메시지를 표시하도록 설정합니다. 앱에 코딩하는 사용자 지정 메트릭에 이러한 경고를 설정할 수 있습니다.
