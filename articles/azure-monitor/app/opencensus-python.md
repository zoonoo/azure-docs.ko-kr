---
title: Azure Monitor (미리 보기)를 사용 하 여 Python 응용 프로그램 모니터링 | Microsoft Docs
description: Azure Monitor를 사용 하 여 OpenCensus Python을 연결 하는 지침을 제공 합니다.
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: reyang
ms.author: reyang
ms.date: 10/11/2019
ms.reviewer: mbullwin
ms.openlocfilehash: af16643ed877ca427a22428afec028264de7a5d8
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/08/2019
ms.locfileid: "74928996"
---
# <a name="set-up-azure-monitor-for-your-python-application-preview"></a>Python 응용 프로그램에 대 한 Azure Monitor 설정 (미리 보기)

Azure Monitor는 [OpenCensus](https://opencensus.io)와의 통합을 통해 분산 추적, 메트릭 수집 및 Python 응용 프로그램의 로깅을 지원 합니다. 이 문서에서는 Python 용 OpenCensus를 설정 하 고 모니터링 데이터를 Azure Monitor으로 전송 하는 과정을 안내 합니다.

## <a name="prerequisites"></a>전제 조건

- Azure 구독. Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/)을 만듭니다.
- Python 설치. 이 문서에서는 [Python 3.7.0](https://www.python.org/downloads/)를 사용 하지만, 이전 버전은 사소한 변경 내용으로 작업할 가능성이 높습니다.

## <a name="sign-in-to-the-azure-portal"></a>Azure Portal에 로그인

[Azure portal](https://portal.azure.com/)에 로그인합니다.

## <a name="create-an-application-insights-resource-in-azure-monitor"></a>Azure Monitor에서 Application Insights 리소스 만들기

먼저 Azure Monitor에서 Application Insights 리소스를 만들어야 합니다. 그러면 계측 키 (ikey)가 생성 됩니다. 그런 다음 Azure Monitor에 원격 분석 데이터를 보내도록 OpenCensus SDK를 구성 하는 데 ikey를 사용 합니다.

1. **리소스 만들기** > **개발자 도구** > **Application Insights**를 선택합니다.

   ![Application Insights 리소스 추가](./media/opencensus-python/0001-create-resource.png)

1. 구성 상자가 표시 됩니다. 다음 표를 사용 하 여 입력 필드를 채웁니다.

   | 설정        | Value           | 설명  |
   | ------------- |:-------------|:-----|
   | **Name**      | 전역적으로 고유한 값 | 모니터링 중인 앱을 식별 하는 이름입니다. |
   | **리소스 그룹**     | myResourceGroup      | Application Insights 데이터를 호스트할 새 리소스 그룹의 이름입니다. |
   | **위치** | 미국 동부 | 가까운 위치 또는 앱이 호스트 되는 위치 근처 |

1. **만들기**를 선택합니다.

## <a name="instrument-with-opencensus-python-sdk-for-azure-monitor"></a>Azure Monitor 용 OpenCensus Python SDK를 사용 하 여 계측

OpenCensus Azure Monitor 내보내기를 설치 합니다.

```console
python -m pip install opencensus-ext-azure
```

패키지 및 통합의 전체 목록은 [OpenCensus 패키지](https://docs.microsoft.com/azure/azure-monitor/app/nuget#common-packages-for-python-using-opencensus)를 참조 하세요.

> [!NOTE]
> `python -m pip install opencensus-ext-azure` 명령은 Python 설치를 위한 `PATH` 환경 변수가 설정 되어 있다고 가정 합니다. 이 변수를 구성 하지 않은 경우 Python 실행 파일이 있는 위치에 대 한 전체 디렉터리 경로를 제공 해야 합니다. 결과는 `C:\Users\Administrator\AppData\Local\Programs\Python\Python37-32\python.exe -m pip install opencensus-ext-azure`와 같은 명령입니다.

SDK는 세 가지 Azure Monitor 내보내기를 사용 하 여 Azure Monitor에 대 한 다양 한 유형의 원격 분석 (추적, 메트릭 및 로그)을 보냅니다. 이러한 원격 분석 형식에 대 한 자세한 내용은 [데이터 플랫폼 개요](https://docs.microsoft.com/azure/azure-monitor/platform/data-platform)를 참조 하세요. 다음 지침을 사용 하 여 세 가지 내보내기를 통해 이러한 원격 분석 유형을 보냅니다.

## <a name="telemetry-type-mappings"></a>원격 분석 형식 매핑

다음은 Azure Monitor에서 볼 수 있는 원격 분석 유형에 OpenCensus가 제공 하는 내보내기입니다.

![OpenCensus에서 Azure Monitor로 원격 분석 유형을 매핑하는 스크린샷](./media/opencensus-python/0012-telemetry-types.png)

### <a name="trace"></a>추적

> [!NOTE]
> OpenCensus의 `Trace`은 [분산 추적](https://docs.microsoft.com/azure/azure-monitor/app/distributed-tracing)을 나타냅니다. `AzureExporter`는 Azure Monitor에 `requests` 및 `dependency` 원격 분석을 보냅니다.

1. 먼저 일부 추적 데이터를 로컬로 생성 해 보겠습니다. Python 유휴 또는 원하는 편집기에서 다음 코드를 입력 합니다.

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

2. 코드를 실행하면 값을 입력하라는 메시지가 반복적으로 표시됩니다. 각 항목을 사용 하면 값이 셸에 출력 되 고 OpenCensus Python 모듈은 `SpanData`의 해당 부분을 생성 합니다. OpenCensus 프로젝트는 추적을 [범위 트리로](https://opencensus.io/core-concepts/tracing/)정의 합니다.
    
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

3. 값을 입력 하는 것은 데모용으로 유용 하지만 궁극적으로는 `SpanData`를 Azure Monitor로 내보내야 합니다. 다음 코드 샘플을 기반으로 이전 단계에서 코드를 수정 합니다.

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

4. 이제 Python 스크립트를 실행할 때 값을 입력 하 라는 메시지가 표시 되지만 값만 셸에 인쇄 됩니다. 만든 `SpanData` Azure Monitor으로 전송 됩니다. `dependencies`에서 내보낸 범위 데이터를 찾을 수 있습니다.

5. OpenCensus 샘플링에 대 한 자세한 내용은 [OpenCensus의 샘플링](https://docs.microsoft.com/azure/azure-monitor/app/sampling#configuring-fixed-rate-sampling-in-opencensus-python)을 참조 하세요.

6. 추적 데이터의 원격 분석 상관 관계에 대 한 자세한 내용은 OpenCensus [원격 분석 상관 관계](https://docs.microsoft.com/azure/azure-monitor/app/correlation#telemetry-correlation-in-opencensus-python)를 참조 하세요.

### <a name="metrics"></a>메트릭

1. 먼저 일부 로컬 메트릭 데이터를 생성 해 보겠습니다. 사용자가 Enter 키를 누르는 횟수를 추적 하는 간단한 메트릭을 만듭니다.

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
2. 코드를 실행 하면 Enter 키를 눌러야 하는 메시지가 반복 해 서 표시 됩니다. Enter 키를 누르는 횟수를 추적 하는 메트릭이 생성 됩니다. 각 항목을 사용 하면 값이 증가 하 고 메트릭 정보가 콘솔에 표시 됩니다. 이 정보에는 메트릭이 업데이트 된 현재 값 및 현재 타임 스탬프가 포함 됩니다.

    ```
    Press enter.
    Point(value=ValueLong(5), timestamp=2019-10-09 20:58:04.930426)
    Press enter.
    Point(value=ValueLong(6), timestamp=2019-10-09 20:58:06.570167)
    Press enter.
    Point(value=ValueLong(7), timestamp=2019-10-09 20:58:07.138614)
    ```

3. 값 입력은 데모용으로 유용 하지만, 궁극적으로 메트릭 데이터를 Azure Monitor으로 내보내야 합니다. 다음 코드 샘플을 기반으로 이전 단계에서 코드를 수정 합니다.

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

4. 내보내기는 고정 된 간격으로 메트릭 데이터를 Azure Monitor 보냅니다. 기본값은 15 초 간격입니다. 단일 메트릭을 추적 하므로이 메트릭 데이터에 포함 된 값과 타임 스탬프를 포함 하는이 메트릭 데이터는 모든 간격으로 전송 됩니다. `customMetrics`에서 데이터를 찾을 수 있습니다.

### <a name="logs"></a>로그

1. 먼저 일부 로컬 로그 데이터를 생성 해 보겠습니다.

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

2.  이 코드는 입력 될 값을 지속적으로 요청 합니다. 입력 한 모든 값에 대 한 로그 항목이 내보내집니다.

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

3. 값을 입력 하는 것은 데모용으로 유용 하지만 궁극적으로는 로그 데이터를 Azure Monitor으로 내보내야 합니다. 다음 코드 샘플을 기반으로 이전 단계에서 코드를 수정 합니다.

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

4. 내보내기가 로그 데이터를 Azure Monitor으로 보냅니다. `traces`에서 데이터를 찾을 수 있습니다. 

> [!NOTE]
> 이 컨텍스트의 `traces` `Tracing`와 동일 하지 않습니다. `traces`은 `AzureLogHandler`을 활용할 때 Azure Monitor에 표시 되는 원격 분석의 유형을 나타냅니다. `Tracing` OpenCensus의 개념을 참조 하 고 [분산 추적](https://docs.microsoft.com/azure/azure-monitor/app/distributed-tracing)에 연결 합니다.

5. 로그 메시지의 형식을 지정 하기 위해 기본 제공 Python [로깅 API](https://docs.python.org/3/library/logging.html#formatter-objects)에서 `formatters`를 사용할 수 있습니다.

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

6. 로그에 사용자 지정 차원을 추가할 수도 있습니다. 이러한 값은 Azure Monitor `customDimensions`에서 키-값 쌍으로 나타납니다.
> [!NOTE]
> 이 기능이 작동 하려면 사전을 로그에 인수로 전달 해야 합니다. 다른 모든 데이터 구조는 무시 됩니다. 문자열 형식을 유지 하려면 사전에 저장 하 고 인수로 전달 합니다.

    ```python
    import logging
    
    from opencensus.ext.azure.log_exporter import AzureLogHandler
    
    logger = logging.getLogger(__name__)
    # TODO: replace the all-zero GUID with your instrumentation key.
    logger.addHandler(AzureLogHandler(
        connection_string='InstrumentationKey=00000000-0000-0000-0000-000000000000')
    )
    logger.warning('action', {'key-1': 'value-1', 'key-2': 'value2'})
    ```

7. 추적 컨텍스트 데이터로 로그를 보강 하는 방법에 대 한 자세한 내용은 OpenCensus Python [logs integration](https://docs.microsoft.com/azure/azure-monitor/app/correlation#logs-correlation)을 참조 하세요.

## <a name="view-your-data-with-queries"></a>쿼리를 사용 하 여 데이터 보기

**로그 (분석)** 탭을 통해 응용 프로그램에서 전송 된 원격 분석 데이터를 볼 수 있습니다.

![빨간색 상자에 "로그 (분석)"가 선택 된 개요 창의 스크린샷](./media/opencensus-python/0010-logs-query.png)

**활성 상태의**목록에서 다음을 수행 합니다.

- Azure Monitor 추적 내보내기를 사용 하 여 전송 되는 원격 분석의 경우 들어오는 요청은 `requests`아래에 나타납니다. 발신 또는 in-process 요청은 `dependencies`아래에 나타납니다.
- Azure Monitor 메트릭 내보내기를 사용 하 여 보내는 원격 분석의 경우 전송 된 메트릭이 `customMetrics`아래에 나타납니다.
- Azure Monitor 로그 내보내기를 사용 하 여 전송 되는 원격 분석의 경우 로그는 `traces`아래에 나타납니다. 예외는 `exceptions`아래에 나타납니다.

쿼리 및 로그를 사용 하는 방법에 대 한 자세한 내용은 [Azure Monitor의 로그](https://docs.microsoft.com/azure/azure-monitor/platform/data-platform-logs)를 참조 하세요.

## <a name="learn-more-about-opencensus-for-python"></a>Python 용 OpenCensus에 대 한 자세한 정보

* [GitHub의 OpenCensus Python](https://github.com/census-instrumentation/opencensus-python)
* [사용자 지정](https://github.com/census-instrumentation/opencensus-python/blob/master/README.rst#customization)
* [Flask 통합](https://github.com/census-instrumentation/opencensus-python/tree/master/contrib/opencensus-ext-flask)
* [Django 통합](https://github.com/census-instrumentation/opencensus-python/tree/master/contrib/opencensus-ext-django)
* [MySQL 통합](https://github.com/census-instrumentation/opencensus-python/tree/master/contrib/opencensus-ext-mysql)
* [PostgreSQL](https://github.com/census-instrumentation/opencensus-python/tree/master/contrib/opencensus-ext-postgresql)

## <a name="next-steps"></a>다음 단계

* [애플리케이션 맵](./../../azure-monitor/app/app-map.md)
* [엔드투엔드 성능 모니터링](./../../azure-monitor/learn/tutorial-performance.md)

### <a name="alerts"></a>경고

* [가용성 테스트](../../azure-monitor/app/monitor-web-app-availability.md): 웹 테스트를 만들어 사이트가 웹에 표시되는지 확인합니다.
* [스마트 진단](../../azure-monitor/app/proactive-diagnostics.md): 이 테스트는 자동으로 실행되므로 아무것도 설정할 필요가 없습니다. 앱이 실패한 요청으로 비정상적인 속도를 보일 경우 알려줍니다.
* [메트릭 경고](../../azure-monitor/app/alerts.md): 메트릭이 임계값을 초과 하는 경우 경고를 표시 하도록 경고를 설정 합니다. 앱에 코딩하는 사용자 지정 메트릭에 이러한 경고를 설정할 수 있습니다.
