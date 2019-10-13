---
title: Azure Monitor를 사용 하 여 Python 응용 프로그램 모니터링 | Microsoft Docs
description: Azure Monitor를 사용 하 여 OpenCensus Python을 연결 하는 지침을 제공 합니다.
services: application-insights
keywords: ''
author: reyang
ms.author: reyang
ms.date: 10/11/2019
ms.service: application-insights
ms.topic: conceptual
ms.reviewer: mbullwin
manager: carmonm
ms.openlocfilehash: 1316cf6808f6ccfc4165ad162c51421638b130be
ms.sourcegitcommit: 8b44498b922f7d7d34e4de7189b3ad5a9ba1488b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/13/2019
ms.locfileid: "72293981"
---
# <a name="set-up-azure-monitor-for-your-python-application"></a>Python 응용 프로그램에 대 한 Azure Monitor 설정

Azure Monitor는 [OpenCensus](https://opencensus.io)와의 통합을 통해 Python 응용 프로그램의 분산 추적, 메트릭 수집 및 로깅을 지원 합니다. 이 문서에서는 Python 용 OpenCensus를 설정 하 고 Azure Monitor 하는 모니터링 데이터를 가져오는 과정을 단계별로 안내 합니다.

## <a name="prerequisites"></a>사전 요구 사항

- Azure 구독이 필요합니다.
- Python을 설치 해야 합니다 .이 문서에서는 [python 3.7.0](https://www.python.org/downloads/)를 사용 하지만, 이전 버전에서는 약간의 조정을 사용 하는 경우가 많습니다.

Azure 구독이 아직 없는 경우 시작하기 전에 [체험](https://azure.microsoft.com/free/) 계정을 만듭니다.

## <a name="sign-in-to-the-azure-portal"></a>Azure Portal에 로그인

[Azure Portal](https://portal.azure.com/)에 로그인합니다.

## <a name="create-application-insights-resource-in-azure-monitor"></a>Azure Monitor에서 Application Insights 리소스 만들기

먼저 Azure Monitor에서 Application Insights 리소스를 만들어야 합니다. 그러면 계측 키 (ikey)가 생성 됩니다. 그런 다음 Azure Monitor에 원격 분석 데이터를 보내도록 OpenCensus SDK를 구성 하는 데 ikey를 사용 합니다.

1. **리소스 만들기** > **개발자 도구** > **Application Insights**를 선택합니다.

   ![Application Insights 리소스 추가](./media/opencensus-python/0001-create-resource.png)

   구성 상자가 표시되면 다음 표를 사용하여 입력 필드를 채웁니다.

    | 설정        | 값           | 설명  |
   | ------------- |:-------------|:-----|
   | **이름**      | 전역적으로 고유한 값 | 모니터링하는 응용 프로그램을 식별하는 이름입니다. |
   | **리소스 그룹**     | myResourceGroup      | Application Insights 데이터를 호스팅할 새 리소스 그룹의 이름입니다. |
   | **Location**: | East US | 가까운 위치 또는 응용 프로그램이 호스팅되는 위치 근처를 선택합니다. |

2. **만들기**를 클릭합니다.

## <a name="instrumenting-with-opencensus-python-sdk-for-azure-monitor"></a>Azure Monitor 용 OpenCensus Python SDK를 사용 하 여 계측

1. OpenCensus Azure Monitor 내보내기를 설치 합니다.

    ```console
    python -m pip install opencensus-ext-azure
    ```

    > [!NOTE]
    > `python -m pip install opencensus-ext-azure`는 Python 설치에 대해 PATH 환경 변수를 설정했다고 가정합니다. 아직 구성하지 않은 경우 `C:\Users\Administrator\AppData\Local\Programs\Python\Python37-32\python.exe -m pip install opencensus-ext-azure`와 같은 명령이 되는 Python 실행 파일이 위치하는 곳에 전체 디렉터리 경로를 제공해야 합니다.

2. SDK는 세 가지 Azure Monitor 내보내기를 활용 하 여 Azure Monitor에 대 한 다양 한 유형의 원격 분석 (추적, 메트릭 및 로그)을 보냅니다. 이러한 다양 한 유형에 대 한 자세한 내용은 [데이터 플랫폼 개요](https://docs.microsoft.com/azure/azure-monitor/platform/data-platform) 를 참조 하세요. 다음 지침에 따라 세 가지 내보내기를 통해 이러한 여러 형식을 전송 하는 방법을 확인 합니다.

### <a name="trace"></a>추적

1. 먼저 일부 추적 데이터를 로컬로 생성해 보겠습니다. Python 유휴 또는 원하는 편집기에서 다음 코드를 입력 합니다.

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

2. 코드를 실행하면 값을 입력하라는 메시지가 반복적으로 표시됩니다. 각 항목을 사용하여 값은 셸에 출력되고, **SpanData**의 해당 부분이 OpenCensus Python 모듈에 의해 생성됩니다. OpenCensus 프로젝트는 [_범위 트리로 추적_](https://opencensus.io/core-concepts/tracing/)을 정의합니다.
    
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

3. 데모용으로는 유용 하지만 `SpanData`을 Azure Monitor로 내보내야 합니다. 다음 코드 샘플을 기반으로 이전 단계에서 코드를 수정 합니다.

    ```python
    from opencensus.ext.azure.trace_exporter import AzureExporter
    from opencensus.trace.samplers import ProbabilitySampler
    from opencensus.trace.tracer import Tracer
    
    # TODO: replace the all-zero GUID with your instrumentation key.
    tracer = Tracer(
        exporter=AzureExporter(
            connection_string='InstrumentationKey=00000000-0000-0000-0000-000000000000'),
        ),
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

4. 이제 Python 스크립트를 실행할 때 값을 입력 하 라는 메시지가 표시 되지만 이제는 값만 셸에 인쇄 됩니다. 만든 `SpanData`이 Azure Monitor 전송 됩니다. @No__t-0에서 내보낸 범위 데이터를 찾을 수 있습니다.

### <a name="metrics"></a>metrics

1. 먼저 일부 로컬 메트릭 데이터를 생성 해 보겠습니다. 사용자가 enter 키를 누르는 횟수를 추적 하는 간단한 메트릭을 만듭니다.

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
2. 코드를 실행 하면 enter 키를 눌러야 하는 메시지가 반복 해 서 표시 됩니다. 입력 한 횟수를 추적 하는 메트릭이 생성 됩니다. 각 항목을 사용 하면 값이 증가 하 고 메트릭을 업데이트할 때 현재 값과 현재 타임 스탬프를 사용 하 여 메트릭 정보가 콘솔에 표시 됩니다.

    ```
    Press enter.
    Point(value=ValueLong(5), timestamp=2019-10-09 20:58:04.930426)
    Press enter.
    Point(value=ValueLong(6), timestamp=2019-10-09 20:58:06.570167)
    Press enter.
    Point(value=ValueLong(7), timestamp=2019-10-09 20:58:07.138614)
    ```

3. 데모용으로 유용한 반면, 궁극적으로는 메트릭 데이터를 Azure Monitor으로 내보내도록 합니다. 다음 코드 샘플을 기반으로 이전 단계에서 코드를 수정 합니다.

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
    )
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

4. 내보내기는 고정 된 간격으로 Azure Monitor에 메트릭 데이터를 전송 하며 기본값은 15 초 마다입니다. 단일 메트릭을 추적 하므로이 메트릭 데이터에 포함 된 값과 타임 스탬프를 포함 하는이 메트릭 데이터는 모든 간격으로 전송 됩니다. @No__t-0에서 데이터를 찾을 수 있습니다.

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

2.  이 코드는 입력 될 값을 묻는 메시지를 계속 표시 합니다. 입력 된 값을 포함 하는 모든 값에 대해 로그 항목이 내보내집니다.

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

3. 데모용으로 유용한 반면, 궁극적으로는 메트릭 데이터를 Azure Monitor으로 내보내도록 합니다. 다음 코드 샘플을 기반으로 이전 단계에서 코드를 수정 합니다.

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

4. 내보내기가 로그 데이터를 Azure Monitor으로 보냅니다. @No__t-0에서 데이터를 찾을 수 있습니다.

## <a name="start-monitoring-in-the-azure-portal"></a>Azure Portal에서 모니터링 시작

1. 이제 Azure Portal에서 Application Insights **개요** 페이지를 다시 열어 현재 실행 중인 애플리케이션에 대한 세부 정보를 볼 수 있습니다. **라이브 메트릭 스트림**을 선택합니다.

   ![빨간색 상자에서 라이브 메트릭 스트림이 선택된 개요 창의 스크린샷](./media/opencensus-python/0005-overview-live-metrics-stream.png)

2. **개요** 페이지로 다시 이동하고 종속성 관계의 시각적 개체 레이아웃에 대한 **애플리케이션 맵**을 선택하고 애플리케이션 구성 요소 간의 타이밍을 호출합니다.

    ![기본 애플리케이션 맵의 스크린샷](./media/opencensus-python/0007-application-map.png)

    하나의 메서드 호출만 추적했으므로 애플리케이션 맵은 흥미롭지 않습니다. 하지만 애플리케이션은 훨씬 분산된 애플리케이션을 시각화하도록 확장될 수 있습니다.

   ![애플리케이션 맵](media/opencensus-python/application-map.png)

3. **성능 조사**를 선택하여 자세한 성능 분석을 수행하고 성능 저하의 근본 원인을 확인합니다.

    ![성능 창의 스크린샷](./media/opencensus-python/0008-performance.png)

4. **샘플**을 선택한 다음, 오른쪽 창에 표시되는 샘플 중 하나를 클릭하면 엔드투엔드 트랜잭션 세부 정보 환경이 시작됩니다. 샘플 앱은 단일 이벤트만을 표시하지만 더 복잡한 애플리케이션을 통해 개별 이벤트의 호출 스택의 수준까지 엔드투엔드 트랜잭션을 탐색할 수 있습니다.

     ![엔드투엔드 트랜잭션 인터페이스의 스크린샷](./media/opencensus-python/0009-end-to-end-transaction.png)

## <a name="view-your-data-with-queries"></a>쿼리를 사용 하 여 데이터 보기

1. 로그 (분석) 탭을 통해 응용 프로그램에서 전송 된 원격 분석 데이터를 볼 수 있습니다.

    ![빨간색 상자에서 선택한 로그 (분석)를 사용 하는 개요 창의 스크린샷](./media/opencensus-python/0010-logs-query.png)

2. Azure Monitor 추적 내보내기를 사용 하 여 전송 되는 원격 분석의 경우 들어오는 요청은 @no__t 아래에 표시 되 고, 나가는 요청은 `dependencies` 아래에 표시 됩니다.

3. Azure Monitor 메트릭 내보내기를 사용 하 여 전송 되는 원격 분석의 경우 전송 되는 메트릭은 `customMetrics` 아래에 표시 됩니다.

4. Azure Monitor 로그 내보내기를 사용 하 여 전송 되는 원격 분석의 경우 로그는 @no__t 아래에 표시 되 고 `exceptions` 아래에 예외가 표시 됩니다.

5. 쿼리 및 로그를 사용 하는 방법에 대 한 자세한 내용은 [Azure Monitor의 로그](https://docs.microsoft.com/azure/azure-monitor/platform/data-platform-logs) 를 참조 하세요.

## <a name="opencensus-for-python"></a>Python 용 OpenCensus

* [GitHub의 OpenCensus Python](https://github.com/census-instrumentation/opencensus-python)
* [사용자 지정](https://github.com/census-instrumentation/opencensus-python/blob/master/README.rst#customization)
* [Flask 통합](https://github.com/census-instrumentation/opencensus-python/tree/master/contrib/opencensus-ext-flask)
* [Django 통합](https://github.com/census-instrumentation/opencensus-python/tree/master/contrib/opencensus-ext-django)
* [MySQL 통합](https://github.com/census-instrumentation/opencensus-python/tree/master/contrib/opencensus-ext-mysql)
* [PostgreSQL](https://github.com/census-instrumentation/opencensus-python/tree/master/contrib/opencensus-ext-postgresql)

## <a name="next-steps"></a>다음 단계

* [API 요약](./../../azure-monitor/app/api-custom-events-metrics.md)
* [애플리케이션 맵](./../../azure-monitor/app/app-map.md)
* [엔드투엔드 성능 모니터링](./../../azure-monitor/learn/tutorial-performance.md)

### <a name="alerts"></a>,

* [가용성 테스트](../../azure-monitor/app/monitor-web-app-availability.md): 테스트를 만들어 사이트가 웹에 표시되는지 확인합니다.
* [스마트 진단](../../azure-monitor/app/proactive-diagnostics.md): 이 테스트는 자동으로 실행되므로 아무것도 설정할 필요가 없습니다. 앱이 실패한 요청으로 비정상적인 속도를 보일 경우 알려줍니다.
* [메트릭 경고](../../azure-monitor/app/alerts.md): 메트릭이 임계값을 초과할 경우 경고를 표시 하도록 경고를 설정 합니다. 앱에 코딩하는 사용자 지정 메트릭에 이러한 경고를 설정할 수 있습니다.