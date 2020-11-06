---
title: OpenCensus Python을 사용 하 여 Azure 애플리케이션 정보에서 들어오는 요청 추적 | Microsoft Docs
description: OpenCensus Python을 통해 Python 앱에 대 한 요청 호출을 모니터링 합니다.
ms.topic: conceptual
author: lzchen
ms.author: lechen
ms.date: 10/15/2019
ms.custom: devx-track-python
ms.openlocfilehash: 6cf0998eb4d769f2d1a7891892a5a462cd907e32
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/06/2020
ms.locfileid: "93422504"
---
# <a name="track-incoming-requests-with-opencensus-python"></a>OpenCensus Python을 사용 하 여 들어오는 요청 추적

들어오는 요청 데이터는 OpenCensus Python 및 다양 한 통합을 사용 하 여 수집 됩니다. 인기 있는 웹 프레임 워크를 기반으로 하는 웹 응용 프로그램에 전송 된 들어오는 요청 데이터를 추적 `django` `flask` `pyramid` 합니다. 그런 다음 데이터는 원격 분석으로 Azure Monitor Application Insights 전송 됩니다 `requests` .

먼저 최신 [OpenCensus PYTHON SDK](./opencensus-python.md)를 사용 하 여 python 응용 프로그램을 계측 합니다.

## <a name="tracking-django-applications"></a>Django 응용 프로그램 추적

1. `opencensus-ext-django` [Pypi](https://pypi.org/project/opencensus-ext-django/) 에서 다운로드 하 여 설치 하 고 미들웨어를 사용 하 여 응용 프로그램을 계측 `django` 합니다. 응용 프로그램에 전송 된 들어오는 요청이 `django` 추적 됩니다.

2. `opencensus.ext.django.middleware.OpencensusMiddleware`파일에를 포함 `settings.py` `MIDDLEWARE` 합니다.

    ```python
    MIDDLEWARE = (
        ...
        'opencensus.ext.django.middleware.OpencensusMiddleware',
        ...
    )
    ```

3. 에서 AzureExporter 올바르게 구성 되어 있는지 확인 `settings.py` `OPENCENSUS` 합니다. 추적 하지 않으려는 url의 요청을에 추가 `BLACKLIST_PATHS` 합니다.

    ```python
    OPENCENSUS = {
        'TRACE': {
            'SAMPLER': 'opencensus.trace.samplers.ProbabilitySampler(rate=1)',
            'EXPORTER': '''opencensus.ext.azure.trace_exporter.AzureExporter(
                connection_string="InstrumentationKey=<your-ikey-here>"
            )''',
            'BLACKLIST_PATHS': ['https://example.com'],  <--- These sites will not be traced if a request is sent to it.
        }
    }
    ```

## <a name="tracking-flask-applications"></a>Flask 응용 프로그램 추적

1. `opencensus-ext-flask` [Pypi](https://pypi.org/project/opencensus-ext-flask/) 에서 다운로드 하 여 설치 하 고 미들웨어를 사용 하 여 응용 프로그램을 계측 `flask` 합니다. 응용 프로그램에 전송 된 들어오는 요청이 `flask` 추적 됩니다.

    ```python
    
    from flask import Flask
    from opencensus.ext.azure.trace_exporter import AzureExporter
    from opencensus.ext.flask.flask_middleware import FlaskMiddleware
    from opencensus.trace.samplers import ProbabilitySampler
    
    app = Flask(__name__)
    middleware = FlaskMiddleware(
        app,
        exporter=AzureExporter(connection_string="InstrumentationKey=<your-ikey-here>"),
        sampler=ProbabilitySampler(rate=1.0),
    )
    
    @app.route('/')
    def hello():
        return 'Hello World!'
    
    if __name__ == '__main__':
        app.run(host='localhost', port=8080, threaded=True)
    
    ```

2. 를 통해 응용 프로그램을 구성할 수도 있습니다 `flask` `app.config` . 추적 하지 않으려는 url의 요청을에 추가 `BLACKLIST_PATHS` 합니다.

    ```python
    app.config['OPENCENSUS'] = {
        'TRACE': {
            'SAMPLER': 'opencensus.trace.samplers.ProbabilitySampler(rate=1.0)',
            'EXPORTER': '''opencensus.ext.azure.trace_exporter.AzureExporter(
                connection_string="InstrumentationKey=<your-ikey-here>",
            )''',
            'BLACKLIST_PATHS': ['https://example.com'],  <--- These sites will not be traced if a request is sent to it.
        }
    }
    ```

## <a name="tracking-pyramid-applications"></a>피라미드 응용 프로그램 추적

1. `opencensus-ext-django` [Pypi](https://pypi.org/project/opencensus-ext-pyramid/) 에서 다운로드 하 여 설치 하 고 트윈으로 응용 프로그램을 계측 `pyramid` 합니다. 응용 프로그램에 전송 된 들어오는 요청이 `pyramid` 추적 됩니다.

    ```python
    def main(global_config, **settings):
        config = Configurator(settings=settings)
    
        config.add_tween('opencensus.ext.pyramid'
                         '.pyramid_middleware.OpenCensusTweenFactory')
    ```

2. `pyramid`코드에서 직접 트윈을 구성할 수 있습니다. 추적 하지 않으려는 url의 요청을에 추가 `BLACKLIST_PATHS` 합니다.

    ```python
    settings = {
        'OPENCENSUS': {
            'TRACE': {
                'SAMPLER': 'opencensus.trace.samplers.ProbabilitySampler(rate=1.0)',
                'EXPORTER': '''opencensus.ext.azure.trace_exporter.AzureExporter(
                    connection_string="InstrumentationKey=<your-ikey-here>",
                )''',
                'BLACKLIST_PATHS': ['https://example.com'],  <--- These sites will not be traced if a request is sent to it.
            }
        }
    }
    config = Configurator(settings=settings)
    ```

## <a name="tracking-fastapi-applications"></a>FastAPI 응용 프로그램 추적

OpenCensus에는 FastAPI에 대 한 확장명이 없습니다. 고유한 FastAPI 미들웨어를 작성 하려면 다음 단계를 완료 합니다.

1. 다음 종속성이 필요 합니다. 
    - [fastapi](https://pypi.org/project/fastapi/)
    - [uvicorn](https://pypi.org/project/uvicorn/)

2. [Fastapi 미들웨어](https://fastapi.tiangolo.com/tutorial/middleware/)를 추가 합니다. 범위 종류 서버를 설정 했는지 확인 `span.span_kind = SpanKind.SERVER` 합니다.

3. 애플리케이션을 실행합니다. FastAPI 응용 프로그램에 대 한 호출은 자동으로 추적 되 고 원격 분석은 Azure Monitor에 직접 기록 되어야 합니다.

    ```python 
    # Opencensus imports
    from opencensus.ext.azure.trace_exporter import AzureExporter
    from opencensus.trace.samplers import ProbabilitySampler
    from opencensus.trace.tracer import Tracer
    from opencensus.trace.span import SpanKind
    from opencensus.trace.attributes_helper import COMMON_ATTRIBUTES
    # FastAPI imports
    from fastapi import FastAPI, Request
    # uvicorn
    import uvicorn

    app = FastAPI()

    HTTP_URL = COMMON_ATTRIBUTES['HTTP_URL']
    HTTP_STATUS_CODE = COMMON_ATTRIBUTES['HTTP_STATUS_CODE']

    # fastapi middleware for opencensus
    @app.middleware("http")
    async def middlewareOpencensus(request: Request, call_next):
        tracer = Tracer(exporter=AzureExporter(connection_string=f'InstrumentationKey={APPINSIGHTS_INSTRUMENTATIONKEY}'),sampler=ProbabilitySampler(1.0))
        with tracer.span("main") as span:
            span.span_kind = SpanKind.SERVER

            response = await call_next(request)

            tracer.add_attribute_to_current_span(
                attribute_key=HTTP_STATUS_CODE,
                attribute_value=response.status_code)
            tracer.add_attribute_to_current_span(
                attribute_key=HTTP_URL,
                attribute_value=str(request.url))

        return response

    @app.get("/")
    async def root():
        return "Hello World!"

    if __name__ == '__main__':
        uvicorn.run("example:app", host="127.0.0.1", port=5000, log_level="info")
    ```

## <a name="next-steps"></a>다음 단계

* [애플리케이션 맵](./app-map.md)
* [가용성](./monitor-web-app-availability.md)
* [검색](./diagnostic-search.md)
* [로그 (분석) 쿼리](../log-query/log-query-overview.md)
* [트랜잭션 진단](./transaction-diagnostics.md)

