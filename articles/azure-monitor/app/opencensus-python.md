---
title: Azure 모니터를 사용 하 여 파이썬 응용 프로그램 모니터링 (미리 보기) | 마이크로 소프트 문서
description: Azure 모니터를 사용하여 OpenCensus 파이썬을 연결하는 지침을 제공합니다.
ms.topic: conceptual
author: reyang
ms.author: reyang
ms.date: 10/11/2019
ms.reviewer: mbullwin
ms.openlocfilehash: 6ef0675e3ae3f7a5da38138177f3033051723411
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79537111"
---
# <a name="set-up-azure-monitor-for-your-python-application"></a>파이썬 응용 프로그램에 대한 Azure 모니터 설정

Azure Monitor는 [OpenCensus와의](https://opencensus.io)통합을 통해 파이썬 응용 프로그램의 분산 추적, 메트릭 수집 및 로깅을 지원합니다. 이 문서에서는 파이썬에 대한 OpenCensus를 설정하고 모니터링 데이터를 Azure Monitor로 보내는 과정을 안내합니다.

## <a name="prerequisites"></a>사전 요구 사항

- Azure 구독 Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/)을 만듭니다.
- 파이썬 설치. 이 문서에서는 [Python 3.7.0을](https://www.python.org/downloads/)사용하지만 이전 버전은 사소한 변경 사항으로 작동할 수 있습니다.

## <a name="sign-in-to-the-azure-portal"></a>Azure Portal에 로그인

[Azure 포털에](https://portal.azure.com/)로그인합니다.

## <a name="create-an-application-insights-resource-in-azure-monitor"></a>Azure 모니터에서 응용 프로그램 인사이트 리소스 만들기

먼저 계측 키(ikey)를 생성하는 Azure Monitor에서 응용 프로그램 인사이트 리소스를 만들어야 합니다. 그런 다음 ikey를 사용하여 OpenCensus SDK를 구성하여 원격 분석 데이터를 Azure 모니터로 보냅니다.

1. **리소스** > 개발자 도구 응용**프로그램** > **인사이트**만들기를 선택합니다.

   ![응용 프로그램 인사이트 리소스 추가](./media/opencensus-python/0001-create-resource.png)

1. 구성 상자가 나타납니다. 다음 표를 사용하여 입력 필드를 채웁니다.

   | 설정        | 값           | 설명  |
   | ------------- |:-------------|:-----|
   | **이름**      | 전 세계적으로 고유한 가치 | 모니터링 중인 앱을 식별하는 이름 |
   | **리소스 그룹**     | myResourceGroup      | Application Insights 데이터를 호스트할 새 리소스 그룹의 이름 |
   | **위치** | 미국 동부 | 가까운 위치 또는 앱이 호스팅되는 위치 |

1. **만들기**를 선택합니다.

## <a name="instrument-with-opencensus-python-sdk-for-azure-monitor"></a>Azure 모니터용 오픈인구 조사 파이썬 SDK가 있는 계측기

OpenCensus Azure 모니터 내보내기 설치:

```console
python -m pip install opencensus-ext-azure
```

패키지 및 통합의 전체 목록은 [OpenCensus 패키지를](https://docs.microsoft.com/azure/azure-monitor/app/nuget#common-packages-for-python-using-opencensus)참조하십시오.

> [!NOTE]
> 이 `python -m pip install opencensus-ext-azure` 명령은 Python 설치를 `PATH` 위한 환경 변수 집합이 있다고 가정합니다. 이 변수를 구성하지 않은 경우 Python 실행 이있는 위치에 대한 전체 디렉터리 경로를 제공해야합니다. 결과는 다음과 같은 `C:\Users\Administrator\AppData\Local\Programs\Python\Python37-32\python.exe -m pip install opencensus-ext-azure`명령입니다.

SDK는 세 개의 Azure Monitor 내보내기를 사용하여 다양한 유형의 원격 분석을 Azure Monitor(추적, 메트릭 및 로그)로 보냅니다. 이러한 원격 분석 유형에 대한 자세한 내용은 [데이터 플랫폼 개요를](https://docs.microsoft.com/azure/azure-monitor/platform/data-platform)참조하십시오. 다음 지침을 사용하여 세 개의 내보내기를 통해 이러한 원격 분석 유형을 보냅니다.

## <a name="telemetry-type-mappings"></a>원격 분석 유형 매핑

다음은 OpenCensus가 Azure 모니터에 표시되는 원격 분석 유형에 매핑된 내보내기입니다.

![OpenCensus에서 Azure 모니터로 원격 분석 유형 매핑 스크린샷](./media/opencensus-python/0012-telemetry-types.png)

### <a name="trace"></a>추적

> [!NOTE]
> `Trace`OpenCensus에서 [분산 추적을](https://docs.microsoft.com/azure/azure-monitor/app/distributed-tracing)의미합니다. Azure `AzureExporter` `requests` 모니터로 전송 및 `dependency` 원격 분석.

1. 먼저 로컬로 추적 데이터를 생성해 보겠습니다. 파이썬 IDLE 또는 선택한 편집기에서 다음 코드를 입력합니다.

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

2. 코드를 실행하면 값을 입력하라는 메시지가 반복적으로 표시됩니다. 각 항목마다 값이 셸에 인쇄되고 OpenCensus 파이썬 모듈은 해당 `SpanData`의 해당 조각을 생성합니다. OpenCensus 프로젝트는 [범위 트리로 추적](https://opencensus.io/core-concepts/tracing/)을 정의합니다.
    
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

3. 값을 입력하는 것은 데모 용도로 유용하지만 궁극적으로 `SpanData` Azure Monitor에 값을 내보려고 합니다. 연결 문자열을 내보내기에 직접 전달하거나 환경 변수에 `APPLICATIONINSIGHTS_CONNECTION_STRING`지정할 수 있습니다. 다음 코드 샘플을 기반으로 이전 단계에서 코드를 수정합니다.

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

4. 이제 Python 스크립트를 실행할 때 값을 입력하라는 메시지가 표시되어야하지만 값만 셸에 인쇄됩니다. 생성된 `SpanData` 사용자는 Azure 모니터로 전송됩니다. 내보낸 범위 데이터는 아래에서 `dependencies`찾을 수 있습니다. 나가는 요청에 대한 자세한 내용은 OpenCensus 파이썬 [종속성을](https://docs.microsoft.com/azure/azure-monitor/app/opencensus-python-dependency)참조하십시오.
들어오는 요청에 대한 자세한 내용은 OpenCensus 파이썬 [요청을](https://docs.microsoft.com/azure/azure-monitor/app/opencensus-python-request)참조하십시오.

#### <a name="sampling"></a>샘플링

OpenCensus에서 샘플링에 대한 자세한 내용은 [OpenCensus에서 샘플링을](sampling.md#configuring-fixed-rate-sampling-for-opencensus-python-applications)참조하십시오.

#### <a name="trace-correlation"></a>추적 상관 관계

추적 데이터의 원격 분석 상관 관계에 대한 자세한 내용은 OpenCensus 파이썬 [원격 분석 상관 관계를](https://docs.microsoft.com/azure/azure-monitor/app/correlation#telemetry-correlation-in-opencensus-python)살펴보십시오.

#### <a name="modify-telemetry"></a>원격 분석 수정

추적된 원격 분석을 Azure 모니터로 보내기 전에 수정하는 방법에 대한 자세한 내용은 OpenCensus 파이썬 [원격 분석 프로세서를](https://docs.microsoft.com/azure/azure-monitor/app/api-filtering-sampling#opencensus-python-telemetry-processors)참조하십시오.

### <a name="metrics"></a>메트릭

1. 먼저 일부 로컬 메트릭 데이터를 생성해 보겠습니다. 사용자가 Enter를 누르는 횟수를 추적하는 간단한 메트릭을 만듭니다.

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
2. 코드를 실행하면 Enter를 계속 누르라고 메시지가 표시됩니다. Enter를 누른 횟수를 추적하기 위해 메트릭이 만들어집니다. 각 항목마다 값이 증분되고 메트릭 정보가 콘솔에 표시됩니다. 정보에는 메트릭이 업데이트될 때의 현재 값과 현재 타임스탬프가 포함됩니다.

    ```
    Press enter.
    Point(value=ValueLong(5), timestamp=2019-10-09 20:58:04.930426)
    Press enter.
    Point(value=ValueLong(6), timestamp=2019-10-09 20:58:06.570167)
    Press enter.
    Point(value=ValueLong(7), timestamp=2019-10-09 20:58:07.138614)
    ```

3. 값을 입력하는 것은 데모 용으로 유용하지만 궁극적으로 Azure Monitor에 메트릭 데이터를 내보려고 합니다. 연결 문자열을 내보내기에 직접 전달하거나 환경 변수에 `APPLICATIONINSIGHTS_CONNECTION_STRING`지정할 수 있습니다. 다음 코드 샘플을 기반으로 이전 단계에서 코드를 수정합니다.

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

4. 내보내기는 고정된 간격으로 메트릭 데이터를 Azure Monitor로 보냅니다. 기본값은 15초마다 입니다. 단일 메트릭을 추적하므로 이 메트릭 데이터에 포함된 값과 타임스탬프가 포함된 이 메트릭 데이터는 간격마다 전송됩니다. 에서 `customMetrics`데이터를 찾을 수 있습니다.

#### <a name="standard-metrics"></a>표준 메트릭

기본적으로 메트릭 내보내기는 표준 메트릭 집합을 Azure Monitor로 보냅니다. 메트릭 내보내기의 `enable_standard_metrics` 생성자에서 `False` 플래그를 설정하여 이 기능을 비활성화할 수 있습니다.

    ```python
    ...
    exporter = metrics_exporter.new_metrics_exporter(
      enable_standard_metrics=False,
      connection_string='InstrumentationKey=<your-instrumentation-key-here>')
    ...
    ```
다음은 현재 전송된 표준 메트릭 목록입니다.

- 사용 가능한 메모리(바이트)
- CPU 프로세서 시간(백분율)
- 수신 요청 요금(초당)
- 들어오는 요청 평균 실행 시간(밀리초)
- 나가는 요청 요금(초당)
- 프로세스 CPU 사용량(백분율)
- 프로세스 프라이빗 바이트(바이트)

에서 `performanceCounters`이러한 메트릭을 볼 수 있어야 합니다. 들어오는 요청 속도는 `customMetrics`아래에 있을 것입니다. 자세한 내용은 [성능 카운터를](https://docs.microsoft.com/azure/azure-monitor/app/performance-counters)참조하십시오.

#### <a name="modify-telemetry"></a>원격 분석 수정

추적된 원격 분석을 Azure 모니터로 보내기 전에 수정하는 방법에 대한 자세한 내용은 OpenCensus 파이썬 [원격 분석 프로세서를](https://docs.microsoft.com/azure/azure-monitor/app/api-filtering-sampling#opencensus-python-telemetry-processors)참조하십시오.

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

2.  코드는 지속적으로 값을 입력하도록 요청합니다. 입력된 모든 값에 대해 로그 항목이 내보내지다.

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

3. 값을 입력하는 것은 데모 용으로 유용하지만 궁극적으로 는 로그 데이터를 Azure Monitor에 내보려고 합니다. 연결 문자열을 내보내기에 직접 전달하거나 환경 변수에 `APPLICATIONINSIGHTS_CONNECTION_STRING`지정할 수 있습니다. 다음 코드 샘플을 기반으로 이전 단계에서 코드를 수정합니다.

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

4. 내보내기는 Azure 모니터로 로그 데이터를 보냅니다. 에서 `traces`데이터를 찾을 수 있습니다. 

> [!NOTE]
> `traces`이 컨텍스트에서 `Tracing`와 동일하지 않습니다. `traces`을 사용할 `AzureLogHandler`때 Azure 모니터에 표시되는 원격 분석 유형을 나타냅니다. `Tracing`OpenCensus의 개념을 참조하며 분산 [추적과](https://docs.microsoft.com/azure/azure-monitor/app/distributed-tracing)관련이 있습니다.

5. 로그 메시지를 포맷하려면 `formatters` 기본 제공 파이썬 [로깅 API에서](https://docs.python.org/3/library/logging.html#formatter-objects)사용할 수 있습니다.

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

6. custom_dimensions 필드를 사용하여 *추가* 키워드 인수에서 로그 메시지에 사용자 지정 속성을 추가할 수도 있습니다. Azure 모니터에서 `customDimensions` 키-값 쌍으로 나타납니다.
> [!NOTE]
> 이 기능을 작동하려면 사전을 custom_dimensions 필드에 전달해야 합니다. 다른 형식의 인수를 전달하면 로거는 인수를 무시합니다.

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

OpenCensus 파이썬은 원격 분석을 `exception` 자동으로 추적하고 전송하지 않습니다. 그들은 파이썬 `AzureLogHandler` 로깅 라이브러리를 통해 예외를 사용하여 전송됩니다. 일반 로깅과 마찬가지로 사용자 지정 속성을 추가할 수 있습니다.

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
예외를 명시적으로 기록해야 하므로 처리되지 않은 예외를 기록하는 방법은 사용자에게 달려 있습니다. OpenCensus는 예외 원격 분석을 명시적으로 기록하는 한 사용자가 이 작업을 수행하려는 방식에 제한을 두지 않습니다.

#### <a name="sampling"></a>샘플링

OpenCensus에서 샘플링에 대한 자세한 내용은 [OpenCensus에서 샘플링을](sampling.md#configuring-fixed-rate-sampling-for-opencensus-python-applications)참조하십시오.

#### <a name="log-correlation"></a>로그 상관관계

추적 컨텍스트 데이터로 로그를 보강하는 방법에 대한 자세한 내용은 OpenCensus 파이썬 [로그 통합을](https://docs.microsoft.com/azure/azure-monitor/app/correlation#log-correlation)참조하십시오.

#### <a name="modify-telemetry"></a>원격 분석 수정

추적된 원격 분석을 Azure 모니터로 보내기 전에 수정하는 방법에 대한 자세한 내용은 OpenCensus 파이썬 [원격 분석 프로세서를](https://docs.microsoft.com/azure/azure-monitor/app/api-filtering-sampling#opencensus-python-telemetry-processors)참조하십시오.

## <a name="view-your-data-with-queries"></a>쿼리로 데이터 보기

**로그(분석) 탭을** 통해 응용 프로그램에서 전송된 원격 분석 데이터를 볼 수 있습니다.

![빨간색 상자에서 "로그(분석)"가 선택된 개요 창의 스크린샷](./media/opencensus-python/0010-logs-query.png)

**활성**아래의 목록에서 :

- Azure Monitor 추적 내보내기와 함께 전송된 원격 `requests`분석의 경우 들어오는 요청이 아래에 나타납니다. 나가는 요청 또는 처리 중 `dependencies`요청이 아래에 표시됩니다.
- Azure Monitor 메트릭 내보내기와 함께 전송된 원격 분석의 경우 전송된 메트릭이 아래에 `customMetrics`표시됩니다.
- Azure Monitor 로그 내보내기로 전송된 원격 분석의 경우 로그가 아래에 `traces`나타납니다. 예외는 아래에 `exceptions`나타납니다.

쿼리 및 로그를 사용하는 방법에 대한 자세한 내용은 [Azure Monitor의 로그를](https://docs.microsoft.com/azure/azure-monitor/platform/data-platform-logs)참조하십시오.

## <a name="learn-more-about-opencensus-for-python"></a>파이썬을 위한 오픈인구조사에 대해 자세히 알아보기

* [GitHub에 오픈 인구 조사 파이썬](https://github.com/census-instrumentation/opencensus-python)
* [사용자 지정](https://github.com/census-instrumentation/opencensus-python/blob/master/README.rst#customization)
* [GitHub의 Azure 모니터 내보내기](https://github.com/census-instrumentation/opencensus-python/tree/master/contrib/opencensus-ext-azure)
* [오픈인구조사 통합](https://github.com/census-instrumentation/opencensus-python#extensions)
* [Azure 모니터 샘플 응용 프로그램](https://github.com/Azure-Samples/azure-monitor-opencensus-python)

## <a name="next-steps"></a>다음 단계

* [들어오는 요청 추적](./../../azure-monitor/app/opencensus-python-dependency.md)
* [나가는 요청 추적](./../../azure-monitor/app/opencensus-python-request.md)
* [신청 지도](./../../azure-monitor/app/app-map.md)
* [엔드투엔드 성능 모니터링](./../../azure-monitor/learn/tutorial-performance.md)

### <a name="alerts"></a>경고

* [가용성 테스트](../../azure-monitor/app/monitor-web-app-availability.md): 웹 테스트를 만들어 사이트가 웹에 표시되는지 확인합니다.
* [스마트 진단](../../azure-monitor/app/proactive-diagnostics.md): 이 테스트는 자동으로 실행되므로 아무것도 설정할 필요가 없습니다. 앱이 실패한 요청으로 비정상적인 속도를 보일 경우 알려줍니다.
* [메트릭 경고](../../azure-monitor/app/alerts.md): 메트릭이 임계값을 초과할 경우 경고하도록 경고를 설정합니다. 앱에 코딩하는 사용자 지정 메트릭에 이러한 경고를 설정할 수 있습니다.
