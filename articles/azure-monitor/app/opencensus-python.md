---
title: Azure Application Insights를 사용 하 여 Python 응용 프로그램 모니터링 | Microsoft Docs
description: Application Insights를 사용 하 여 OpenCensus Python를 연결 하는 지침을 제공
services: application-insights
keywords: ''
author: reyang
ms.author: reyang
ms.date: 07/02/2019
ms.service: application-insights
ms.topic: conceptual
ms.reviewer: mbullwin
manager: carmonm
ms.openlocfilehash: 2c043ad793dcf5e59eaf460d1ec4aa7a3b48810d
ms.sourcegitcommit: 5bdd50e769a4d50ccb89e135cfd38b788ade594d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/03/2019
ms.locfileid: "67541444"
---
# <a name="collect-distributed-traces-from-python-preview"></a>Python(미리 보기)에서 분산 추적 수집

Application Insights 통합을 통해 Python 응용 프로그램의 추적을 분산 하는 지원 되는 이제 [OpenCensus](https://opencensus.io)합니다. 이 문서에서는 OpenCensus Python에 대 한 설정 하 고 Application Insights로 모니터링 데이터를 가져오는 과정을 단계별로 안내 합니다.

## <a name="prerequisites"></a>필수 조건

- Azure 구독이 필요합니다.
- Python을 설치해야 하며, 이전 버전은 사소한 조정이 작동할 가능성이 있지만 이 문서에서는 [Python 3.7.0](https://www.python.org/downloads/)을 사용합니다.

Azure 구독이 아직 없는 경우 시작하기 전에 [체험](https://azure.microsoft.com/free/) 계정을 만듭니다.

## <a name="sign-in-to-the-azure-portal"></a>Azure Portal에 로그인

[Azure Portal](https://portal.azure.com/)에 로그인합니다.

## <a name="create-application-insights-resource"></a>Application Insights 리소스 만들기

먼저 계측 key(ikey)를 생성 하는 Application Insights 리소스를 만들기 위한 해야 합니다. Ikey는 OpenCensus SDK Application Insights로 원격 분석 데이터를 보내도록 구성에 사용 됩니다.

1. **리소스 만들기** > **개발자 도구** > **Application Insights**를 선택합니다.

   ![Application Insights 리소스 추가](./media/opencensus-python/0001-create-resource.png)

   구성 상자가 표시되면 다음 표를 사용하여 입력 필드를 채웁니다.

    | 설정        | 값           | 설명  |
   | ------------- |:-------------|:-----|
   | **Name**      | 전역적으로 고유한 값 | 모니터링하는 응용 프로그램을 식별하는 이름입니다. |
   | **리소스 그룹**     | myResourceGroup      | Application Insights 데이터를 호스팅할 새 리소스 그룹의 이름입니다. |
   | **위치**: | 미국 동부 | 가까운 위치 또는 응용 프로그램이 호스팅되는 위치 근처를 선택합니다. |

2. **만들기**를 클릭합니다.

## <a name="install-opencensus-azure-monitor-exporters"></a>OpenCensus Azure 모니터 내보내기 도구를 설치 합니다.

1. OpenCensus Azure 모니터 내보내기 도구를 설치 합니다.

    ```console
    python -m pip install opencensus-ext-azure
    ```

    > [!NOTE]
    > `python -m pip install opencensus-ext-azure`는 Python 설치에 대해 PATH 환경 변수를 설정했다고 가정합니다. 아직 구성하지 않은 경우 `C:\Users\Administrator\AppData\Local\Programs\Python\Python37-32\python.exe -m pip install opencensus-ext-azure`와 같은 명령이 되는 Python 실행 파일이 위치하는 곳에 전체 디렉터리 경로를 제공해야 합니다.

2. 먼저 일부 추적 데이터를 로컬로 생성해 보겠습니다. Python IDLE에서 또는 원하는 편집기에서 다음 코드를 입력합니다.

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

3. 코드를 실행하면 값을 입력하라는 메시지가 반복적으로 표시됩니다. 각 항목을 사용하여 값은 셸에 출력되고, **SpanData**의 해당 부분이 OpenCensus Python 모듈에 의해 생성됩니다. OpenCensus 프로젝트는 [_범위 트리로 추적_](https://opencensus.io/core-concepts/tracing/)을 정의합니다.
    
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

4. 데모용으로 도움이 되는 동안에 Application Insights로 SpanData 내보낼 하고자 궁극적으로 합니다. 다음 코드 샘플을 기반으로 이전 단계의 코드를 수정 합니다.

    ```python
    from opencensus.ext.azure.trace_exporter import AzureExporter
    from opencensus.trace.samplers import ProbabilitySampler
    from opencensus.trace.tracer import Tracer
    
    # TODO: replace the all-zero GUID with your instrumentation key.
    tracer = Tracer(
        exporter=AzureExporter(
            instrumentation_key='00000000-0000-0000-0000-000000000000',
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
5. 실행할 때 Python 스크립트를 계속 묻는, 값을 입력 하지만 이제 셸에서 값만 인쇄 됩니다.

## <a name="start-monitoring-in-the-azure-portal"></a>Azure Portal에서 모니터링 시작

1. 이제 Azure Portal에서 Application Insights **개요** 페이지를 다시 열어 현재 실행 중인 애플리케이션에 대한 세부 정보를 볼 수 있습니다. **라이브 메트릭 스트림**을 선택합니다.

   ![빨간색 상자에서 라이브 메트릭 스트림이 선택된 개요 창의 스크린샷](./media/opencensus-python/0005-overview-live-metrics-stream.png)

2. **개요** 페이지로 다시 이동하고 종속성 관계의 시각적 개체 레이아웃에 대한 **애플리케이션 맵**을 선택하고 애플리케이션 구성 요소 간의 타이밍을 호출합니다.

    ![기본 애플리케이션 맵의 스크린샷](./media/opencensus-python/0007-application-map.png)

    하나의 메서드 호출만 추적했으므로 애플리케이션 맵은 흥미롭지 않습니다. 하지만 애플리케이션은 훨씬 분산된 애플리케이션을 시각화하도록 확장될 수 있습니다.

   ![애플리케이션 맵](media/opencensus-python/application-map.png)

3. **성능 조사**를 선택하여 자세한 성능 분석을 수행하고 성능 저하의 근본 원인을 확인합니다.

    ![성능 창의 스크린샷](./media/opencensus-python/0008-performance.png)

4. **샘플**을 선택한 다음, 오른쪽 창에 표시되는 샘플 중 하나를 클릭하면 종단 간 트랜잭션 세부 정보 환경이 시작됩니다. 샘플 앱은 단일 이벤트만을 표시하지만 더 복잡한 애플리케이션을 통해 개별 이벤트의 호출 스택의 수준까지 엔드투엔드 트랜잭션을 탐색할 수 있습니다.

     ![엔드투엔드 트랜잭션 인터페이스의 스크린샷](./media/opencensus-python/0009-end-to-end-transaction.png)

## <a name="opencensus-for-python"></a>Python 용 OpenCensus

* [사용자 지정](https://github.com/census-instrumentation/opencensus-python/blob/master/README.rst#customization)
* [Flask 통합](https://github.com/census-instrumentation/opencensus-python/tree/master/contrib/opencensus-ext-flask)
* [Django 통합](https://github.com/census-instrumentation/opencensus-python/tree/master/contrib/opencensus-ext-django)
* [MySQL 통합](https://github.com/census-instrumentation/opencensus-python/tree/master/contrib/opencensus-ext-mysql)
* [PostgreSQL](https://github.com/census-instrumentation/opencensus-python/tree/master/contrib/opencensus-ext-postgresql)

## <a name="next-steps"></a>다음 단계

* [GitHub의 Python OpenCensus](https://github.com/census-instrumentation/opencensus-python)
* [애플리케이션 맵](./../../azure-monitor/app/app-map.md)
* [종단 간 성능 모니터링](./../../azure-monitor/learn/tutorial-performance.md)