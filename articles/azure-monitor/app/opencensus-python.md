---
title: Azure Monitor를 사용 하 여 Python 응용 프로그램 모니터링 | Microsoft Docs
description: OpenCensus Python과 Azure Monitor를 연결하는 지침을 제공합니다.
ms.topic: conceptual
ms.date: 09/24/2020
ms.reviewer: mbullwin
ms.custom: devx-track-python
ms.openlocfilehash: 1e6376cd8389a4f1f0defebce0a2c7b6d0f9deed
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91323268"
---
# <a name="set-up-azure-monitor-for-your-python-application"></a>Python 애플리케이션용 Azure Monitor 설정

Azure Monitor는 [OpenCensus](https://opencensus.io)와의 통합을 통해 분산 추적, 메트릭 컬렉션 및 Python 애플리케이션의 로깅을 지원합니다. 이 문서에서는 Python 용 OpenCensus를 설정 하 고 모니터링 데이터를 Azure Monitor으로 전송 하는 과정을 안내 합니다.

## <a name="prerequisites"></a>사전 요구 사항

- Azure 구독 Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/)을 만듭니다.
- Python 설치. 이 문서에서는 [Python 3.7.0](https://www.python.org/downloads/release/python-370/)를 사용 하지만, 다른 버전은 사소한 변경으로 작업할 가능성이 높습니다. SDK는 Python v 2.7 및 v 3.4-v 3.7만 지원 합니다.
- Application Insights [리소스](./create-new-resource.md)를 만듭니다. 리소스에 대 한 자체 계측 키 (ikey)가 할당 됩니다.

## <a name="instrument-with-opencensus-python-sdk-for-azure-monitor"></a>Azure Monitor용 OpenCensus Python SDK를 사용한 계측

OpenCensus Azure Monitor 내보내기를 설치합니다.

```console
python -m pip install opencensus-ext-azure
```

> [!NOTE]
> `python -m pip install opencensus-ext-azure` 명령은 Python 설치에 대해 `PATH` 환경 변수를 설정했다고 가정합니다. 이 변수를 구성하지 않은 경우 Python 실행 파일이 위치하는 전체 디렉터리 경로를 제공해야 합니다. 결과로 나온 명령은 다음과 같습니다. `C:\Users\Administrator\AppData\Local\Programs\Python\Python37-32\python.exe -m pip install opencensus-ext-azure`.

SDK는 세 가지 Azure Monitor 내보내기를 사용 하 여 Azure Monitor에 다른 유형의 원격 분석을 보냅니다. 추적, 메트릭 및 로그입니다. 이러한 원격 분석 유형에 대한 자세한 내용은 [데이터 플랫폼 개요](../platform/data-platform.md)를 참조하세요. 다음 지침을 사용하여 세 가지 내보내기를 통해 이러한 원격 분석 유형을 전송합니다.

## <a name="telemetry-type-mappings"></a>원격 분석 유형 매핑

다음은 Azure Monitor에서 볼 수 있는 원격 분석 유형에 OpenCensus가 제공 하는 내보내기입니다.

| 관찰성의 기둥 | Azure Monitor 원격 분석 유형    | 설명                                         |
|-------------------------|------------------------------------|-----------------------------------------------------|
| 로그                    | 추적, 예외, customEvents   | 로그 원격 분석, 예외 원격 분석, 이벤트 원격 분석 |
| 메트릭                 | customMetrics, performanceCounters | 사용자 지정 메트릭 성능 카운터                |
| 추적                 | 요청 종속성             | 들어오는 요청, 보내는 요청                |

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

1. 코드는 입력 될 값을 계속 요청 합니다. 입력한 모든 값에 대해 로그 항목이 내보내집니다.

    ```output
    Enter a value: 24
    24
    Enter a value: 55
    55
    Enter a value: 123
    123
    Enter a value: 90
    90
    ```

1. 시연을 위해 값을 입력하는 것이 유용하지만 결국은 로그 데이터를 Azure Monitor로 내보내야 합니다. 연결 문자열을 내보내기에 직접 전달 합니다. 또는 환경 변수에서 지정할 수 있습니다 `APPLICATIONINSIGHTS_CONNECTION_STRING` . 다음 코드 샘플을 기반으로 이전 단계에서 코드를 수정합니다.

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

1. 내보내기가 로그 데이터를 Azure Monitor 보냅니다. `traces`에서 데이터를 찾을 수 있습니다. 

    > [!NOTE]
    > 이 컨텍스트에서 `traces` 은와 동일 하지 않습니다 `tracing` . 여기서는를 `traces` 활용할 때 Azure Monitor에 표시 되는 원격 분석의 유형을 나타냅니다 `AzureLogHandler` . 그러나 `tracing` OpenCensus에서 개념을 참조 하 고 [분산 추적](./distributed-tracing.md)에 연결 합니다.

    > [!NOTE]
    > 루트 로거가 경고 수준으로 구성 됩니다. 즉, 심각도가 떨어지는 로그는 무시 되 고 다시 Azure Monitor 전송 되지 않습니다. 자세한 내용은 [설명서](https://docs.python.org/3/library/logging.html#logging.Logger.setLevel)를 참조하세요.

1. Custom_dimensions 필드를 사용 하 여 *추가* 키워드 인수에서 로그 메시지에 사용자 지정 속성을 추가할 수도 있습니다. 이러한 속성은 Azure Monitor에서 키-값 쌍으로 나타납니다 `customDimensions` .
    > [!NOTE]
    > 이 기능이 작동하려면 custom_dimensions 필드에 사전을 전달해야 합니다. 다른 형식의 인수를 전달 하는 경우로 거는이를 무시 합니다.

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

#### <a name="configure-logging-for-django-applications"></a>Django 응용 프로그램에 대 한 로깅 구성

Django 응용 프로그램에 대해 위와 같은 응용 프로그램 코드에서 명시적으로 로깅을 구성 하거나 Django의 로깅 구성에서 지정할 수 있습니다. 이 코드는 Django 설정 구성에 사용 하는 모든 파일로 이동할 수 있습니다. Django 설정을 구성 하는 방법에 대 한 자세한 내용은 [Django settings](https://docs.djangoproject.com/en/3.0/topics/settings/)를 참조 하세요. 로깅 구성에 대 한 자세한 내용은 [Django logging](https://docs.djangoproject.com/en/3.0/topics/logging/)를 참조 하세요.

```json
LOGGING = {
    "handlers": {
        "azure": {
            "level": "DEBUG",
        "class": "opencensus.ext.azure.log_exporter.AzureLogHandler",
            "instrumentation_key": "<your-ikey-here>",
         },
        "console": {
            "level": "DEBUG",
            "class": "logging.StreamHandler",
            "stream": sys.stdout,
         },
      },
    "loggers": {
        "logger_name": {"handlers": ["azure", "console"]},
    },
}
```

구성에 지정 된 것과 동일한 이름으로로 거를 사용 해야 합니다.

```python
import logging

logger = logging.getLogger("logger_name")
logger.warning("this will be tracked")
```

#### <a name="send-exceptions"></a>예외 보내기

OpenCensus Python은 원격 분석을 자동으로 추적 하 고 전송 하지 않습니다 `exception` . `AzureLogHandler`Python 로깅 라이브러리를 통해 예외를 사용 하 여를 통해 전송 됩니다. 일반 로깅과 마찬가지로 사용자 지정 속성을 추가할 수 있습니다.

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
예외를 명시적으로 기록해 야 하므로 처리 되지 않은 예외를 기록 하는 방법이 사용자에 게 있습니다. OpenCensus는 예외 원격 분석을 명시적으로 기록 하는 한 사용자가이 작업을 수행 하는 방법에 대 한 제한을 두지 않습니다.

#### <a name="send-events"></a>이벤트 보내기

대신를 사용 하는 경우를 `customEvent` 제외 하 고 원격 분석을 보내는 것과 동일한 방식으로 원격 분석을 보낼 수 있습니다 `trace` `AzureEventHandler` .

```python
import logging

from opencensus.ext.azure.log_exporter import AzureEventHandler

logger = logging.getLogger(__name__)
logger.addHandler(AzureEventHandler(connection_string='InstrumentationKey=<your-instrumentation_key-here>'))
logger.setLevel(logging.INFO)
logger.info('Hello, World!')
```

#### <a name="sampling"></a>샘플링

OpenCensus의 샘플링에 대한 내용은 [OpenCensus의 샘플링](sampling.md#configuring-fixed-rate-sampling-for-opencensus-python-applications)을 참조하세요.

#### <a name="log-correlation"></a>로그 상관 관계

추적 컨텍스트 데이터를 통해 로그를 보강하는 방법에 대한 자세한 내용은 OpenCensus Python [로그 통합](./correlation.md#log-correlation)을 참조하세요.

#### <a name="modify-telemetry"></a>원격 분석 수정

Azure Monitor로 보내기 전에 추적 된 원격 분석을 수정 하는 방법에 대 한 자세한 내용은 OpenCensus Python [원격 분석 프로세서](./api-filtering-sampling.md#opencensus-python-telemetry-processors)를 참조 하세요.


### <a name="metrics"></a>메트릭

1. 먼저 일부 로컬 메트릭 데이터를 생성해 보겠습니다. 사용자가 **Enter** 키를 선택 하는 횟수를 추적 하는 간단한 메트릭을 만듭니다.

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
1. 코드를 반복적으로 실행 하면 **Enter 키**를 선택 하 라는 메시지가 표시 됩니다. **Enter 키** 를 선택한 횟수를 추적 하는 메트릭이 생성 됩니다. 각 항목을 사용 하면 값이 증가 하 고 메트릭 정보가 콘솔에 표시 됩니다. 이 정보에는 메트릭이 업데이트된 현재 값 및 현재 타임스탬프가 포함됩니다.

    ```output
    Press enter.
    Point(value=ValueLong(5), timestamp=2019-10-09 20:58:04.930426)
    Press enter.
    Point(value=ValueLong(6), timestamp=2019-10-09 20:58:06.570167)
    Press enter.
    Point(value=ValueLong(7), timestamp=2019-10-09 20:58:07.138614)
    ```

1. 시연을 위해 값을 입력하는 것이 유용하지만 결국은 메트릭 데이터를 Azure Monitor로 내보내야 합니다. 연결 문자열을 내보내기에 직접 전달 합니다. 또는 환경 변수에서 지정할 수 있습니다 `APPLICATIONINSIGHTS_CONNECTION_STRING` . 다음 코드 샘플을 기반으로 이전 단계에서 코드를 수정합니다.

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

1. 내보내기가 고정 간격으로 Azure Monitor에 메트릭 데이터를 보냅니다. 기본값은 15초 간격입니다. 단일 메트릭을 추적 하므로 여기에 포함 된 값과 타임 스탬프를 포함 하는이 메트릭 데이터는 간격 마다 전송 됩니다. `customMetrics`에서 데이터를 찾을 수 있습니다.

#### <a name="performance-counters"></a>성능 카운터

기본적으로 메트릭 내보내기는 Azure Monitor에 대 한 성능 카운터 집합을 보냅니다. 메트릭 내보내기의 생성자에서 `enable_standard_metrics` 플래그를 `False`로 설정하여 이를 사용하지 않도록 설정할 수 있습니다.

```python
...
exporter = metrics_exporter.new_metrics_exporter(
  enable_standard_metrics=False,
  connection_string='InstrumentationKey=<your-instrumentation-key-here>')
...
```

이러한 성능 카운터는 현재 다음과 같이 전송 됩니다.

- 사용 가능한 메모리(바이트)
- CPU 프로세서 시간(백분율)
- 들어오는 요청 빈도(초당)
- 들어오는 요청 평균 실행 시간(밀리초)
- 프로세스 CPU 사용량(백분율)
- 프로세스 프라이빗 바이트(바이트)

`performanceCounters`에서 이러한 메트릭을 볼 수 있어야 합니다. 자세한 내용은 [성능 카운터](./performance-counters.md)를 참조하세요.

#### <a name="modify-telemetry"></a>원격 분석 수정

Azure Monitor로 보내기 전에 추적 된 원격 분석을 수정 하는 방법에 대 한 자세한 내용은 OpenCensus Python [원격 분석 프로세서](./api-filtering-sampling.md#opencensus-python-telemetry-processors)를 참조 하세요.

### <a name="tracing"></a>추적

> [!NOTE]
> OpenCensus에서는 `tracing` [분산 추적](./distributed-tracing.md)을 나타냅니다. `AzureExporter`는 `requests` 및 `dependency` 원격 분석을 Azure Monitor로 전송합니다.

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

1. 코드를 반복적으로 실행 하면 값을 입력 하 라는 메시지가 표시 됩니다. 각 항목을 사용 하면 값이 셸에 출력 됩니다. OpenCensus Python 모듈은 해당 하는 부분을 생성 합니다 `SpanData` . OpenCensus 프로젝트는 [범위 트리로 추적](https://opencensus.io/core-concepts/tracing/)을 정의합니다.
    
    ```output
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

1. 값 입력은 데모용으로 유용 하지만 궁극적으로는 Azure Monitor로 내보내야 합니다 `SpanData` . 연결 문자열을 내보내기에 직접 전달 합니다. 또는 환경 변수에서 지정할 수 있습니다 `APPLICATIONINSIGHTS_CONNECTION_STRING` . 다음 코드 샘플을 기반으로 이전 단계에서 코드를 수정합니다.

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

1. 이제 Python 스크립트를 실행할 때 값을 입력하라는 메시지가 여전히 표시되지만 이제는 값만 셸에 인쇄됩니다. 만들어진 `SpanData` 가 Azure Monitor으로 전송 됩니다. `dependencies`에서 내보내진 범위 데이터를 찾을 수 있습니다. 보내는 요청에 대 한 자세한 내용은 OpenCensus Python [종속성](./opencensus-python-dependency.md)을 참조 하세요.
들어오는 요청에 대 한 자세한 내용은 OpenCensus Python [requests](./opencensus-python-request.md)항목을 참조 하세요.

#### <a name="sampling"></a>샘플링

OpenCensus의 샘플링에 대한 내용은 [OpenCensus의 샘플링](sampling.md#configuring-fixed-rate-sampling-for-opencensus-python-applications)을 참조하세요.

#### <a name="trace-correlation"></a>추적 상관 관계

추적 데이터의 원격 분석 상관 관계에 대 한 자세한 내용은 OpenCensus Python [원격 분석 상관 관계](./correlation.md#telemetry-correlation-in-opencensus-python)를 참조 하세요.

#### <a name="modify-telemetry"></a>원격 분석 수정

Azure Monitor로 보내기 전에 추적 된 원격 분석을 수정 하는 방법에 대 한 자세한 내용은 OpenCensus Python [원격 분석 프로세서](./api-filtering-sampling.md#opencensus-python-telemetry-processors)를 참조 하세요.

## <a name="configure-azure-monitor-exporters"></a>Azure Monitor 내보내기 구성

표시 된 것 처럼 OpenCensus를 지 원하는 3 가지 Azure Monitor 내보내기 있습니다. 각 항목은 서로 다른 유형의 원격 분석을 Azure Monitor 보냅니다. 각 내보내기에서 보내는 원격 분석의 유형을 확인 하려면 다음 목록을 참조 하세요.

각 내보내기는 생성자를 통해 전달 되는 구성에 대해 동일한 인수를 수락 합니다. 여기에서 각 항목에 대 한 세부 정보를 볼 수 있습니다.

- `connection_string`: Azure Monitor 리소스에 연결 하는 데 사용 되는 연결 문자열입니다. 우선 순위를 사용 `instrumentation_key` 합니다.
- `enable_standard_metrics`:에 사용 `AzureMetricsExporter` 됩니다. 자동으로 Azure Monitor 하기 위해 [성능 카운터](../platform/app-insights-metrics.md#performance-counters) 메트릭을 보내도록 내보내기를 신호로 보냅니다. 기본값은 `True`입니다.
- `export_interval`: 내보내는 빈도 (초)를 지정 하는 데 사용 됩니다.
- `instrumentation_key`: Azure Monitor 리소스에 연결 하는 데 사용 되는 계측 키입니다.
- `logging_sampling_rate`:에 사용 `AzureLogHandler` 됩니다. 로그를 내보내기 위한 샘플링 주기 [0, 1.0]을 제공 합니다. 기본값은 1.0입니다.
- `max_batch_size`: 한 번에 내보낸 원격 분석의 최대 크기를 지정 합니다.
- `proxies`: Azure Monitor에 데이터를 보내는 데 사용할 프록시 시퀀스를 지정 합니다. 자세한 내용은 [프록시](https://requests.readthedocs.io/en/master/user/advanced/#proxies)를 참조 하세요.
- `storage_path`: 로컬 저장소 폴더가 있는 위치에 대 한 경로입니다 (보내지 않은 원격 분석). `opencensus-ext-azure`V 1.0.3 기본 경로는 OS 임시 디렉터리 + `opencensus-python`  +  `your-ikey` 입니다. V 1.0.3 이전에는 기본 경로가 $USER + `.opencensus`  +  `.azure`  +  `python-file-name` 입니다.

## <a name="view-your-data-with-queries"></a>쿼리를 사용하여 데이터 보기

**로그(분석)** 탭을 통해 애플리케이션에서 전송된 원격 분석 데이터를 볼 수 있습니다.

![붉은색 상자로 "로그(분석)"가 선택된 개요 창의 스크린샷](./media/opencensus-python/0010-logs-query.png)

**활성** 목록:

- Azure Monitor 추적 내보내기를 사용하여 전송되는 원격 분석의 경우 들어오는 요청은 `requests`에 표시됩니다. 나가는 또는 진행 중인 요청은 `dependencies`에 표시됩니다.
- Azure Monitor 메트릭 내보내기로 전송된 원격 분석의 경우 전송된 메트릭은 `customMetrics`에 표시됩니다.
- Azure Monitor 로그 내보내기를 사용하여 전송되는 원격 분석의 경우 로그는 `traces`에 표시됩니다. 예외는 `exceptions`에 표시됩니다.

쿼리 및 로그를 사용하는 방법에 대한 자세한 내용은 [Azure Monitor의 로그](../platform/data-platform-logs.md)를 참조하세요.

## <a name="learn-more-about-opencensus-for-python"></a>Python용 OpenCensus에 대해 자세히 알아보기

* [GitHub의 OpenCensus Python](https://github.com/census-instrumentation/opencensus-python)
* [사용자 지정](https://github.com/census-instrumentation/opencensus-python/blob/master/README.rst#customization)
* [GitHub의 Azure Monitor 내보내기](https://github.com/census-instrumentation/opencensus-python/tree/master/contrib/opencensus-ext-azure)
* [OpenCensus 통합](https://github.com/census-instrumentation/opencensus-python#extensions)
* [Azure Monitor 샘플 애플리케이션](https://github.com/Azure-Samples/azure-monitor-opencensus-python)

## <a name="next-steps"></a>다음 단계

* [들어오는 요청 추적](./opencensus-python-dependency.md)
* [진행 중인 요청 추적](./opencensus-python-request.md)
* [애플리케이션 맵](./app-map.md)
* [엔드투엔드 성능 모니터링](../learn/tutorial-performance.md)

### <a name="alerts"></a>경고

* [가용성 테스트](./monitor-web-app-availability.md): 테스트를 만들어 사이트가 웹에 표시되는지 확인합니다.
* [스마트 진단](./proactive-diagnostics.md): 이 테스트는 자동으로 실행되므로 아무것도 설정할 필요가 없습니다. 앱이 실패한 요청으로 비정상적인 속도를 보일 경우 알려줍니다.
* [메트릭 경고](../platform/alerts-log.md): 메트릭이 임계값을 초과할 경우 경고 메시지를 표시하도록 설정합니다. 앱에 코딩하는 사용자 지정 메트릭에 이러한 경고를 설정할 수 있습니다.

