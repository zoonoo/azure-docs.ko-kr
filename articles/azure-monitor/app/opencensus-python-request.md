---
title: OpenCensus Python을 사용하여 Azure Application Insights로 들어오는 요청 추적 | Microsoft Docs
description: OpenCensus Python을 통해 Python 앱에 대한 요청 호출을 모니터링합니다.
ms.topic: conceptual
author: lzchen
ms.author: lechen
ms.date: 10/15/2019
ms.custom: devx-track-python
ms.openlocfilehash: 093a31991300867e2598cabacbef7f56dfe890e9
ms.sourcegitcommit: 80d311abffb2d9a457333bcca898dfae830ea1b4
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/26/2021
ms.locfileid: "110472720"
---
# <a name="track-incoming-requests-with-opencensus-python"></a>OpenCensus Python을 사용하여 들어오는 요청 추적

들어오는 요청 데이터는 OpenCensus Python 및 다양한 통합을 사용하여 수집됩니다. 인기 있는 웹 프레임 워크 `django`, `flask`, `pyramid`를 기반으로 하는 웹 애플리케이션에 전송된 들어오는 요청 데이터를 추적합니다. 그런 다음 데이터는 `requests` 원격 분석 데이터로 Azure Monitor의 Application Insights에 전송됩니다.

먼저 최신 [OpenCensus Python SDK](./opencensus-python.md)를 사용하여 Python 애플리케이션을 계측합니다.

## <a name="tracking-django-applications"></a>Django 애플리케이션 추적

1. [PyPI](https://pypi.org/project/opencensus-ext-django/)에서 `opencensus-ext-django`를 다운로드하여 설치하고 `django` 미들웨어를 사용하여 애플리케이션을 계측합니다. `django` 애플리케이션에 전송된 들어오는 요청이 추적됩니다.

2. `MIDDLEWARE`의 `settings.py` 파일에 `opencensus.ext.django.middleware.OpencensusMiddleware`를 포함합니다.

    ```python
    MIDDLEWARE = (
        ...
        'opencensus.ext.django.middleware.OpencensusMiddleware',
        ...
    )
    ```

3. `OPENCENSUS`의 `settings.py`에 AzureExporter가 올바르게 구성되어 있는지 확인합니다. 추적하지 않으려는 URL의 요청은 `EXCLUDELIST_PATHS`에 추가합니다.

    ```python
    OPENCENSUS = {
        'TRACE': {
            'SAMPLER': 'opencensus.trace.samplers.ProbabilitySampler(rate=1)',
            'EXPORTER': '''opencensus.ext.azure.trace_exporter.AzureExporter(
                connection_string="InstrumentationKey=<your-ikey-here>"
            )''',
            'EXCLUDELIST_PATHS': ['https://example.com'],  <--- These sites will not be traced if a request is sent to it.
        }
    }
    ```

## <a name="tracking-flask-applications"></a>Flask 애플리케이션 추적

1. [PyPI](https://pypi.org/project/opencensus-ext-flask/)에서 `opencensus-ext-flask`를 다운로드하여 설치하고 `flask` 미들웨어를 사용하여 애플리케이션을 계측합니다. `flask` 애플리케이션에 전송된 들어오는 요청이 추적됩니다.

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

2. `app.config`를 통해 `flask` 애플리케이션을 구성할 수도 있습니다. 추적하지 않으려는 URL의 요청은 `EXCLUDELIST_PATHS`에 추가합니다.

    ```python
    app.config['OPENCENSUS'] = {
        'TRACE': {
            'SAMPLER': 'opencensus.trace.samplers.ProbabilitySampler(rate=1.0)',
            'EXPORTER': '''opencensus.ext.azure.trace_exporter.AzureExporter(
                connection_string="InstrumentationKey=<your-ikey-here>",
            )''',
            'EXCLUDELIST_PATHS': ['https://example.com'],  <--- These sites will not be traced if a request is sent to it.
        }
    }
    ```
    
    > [!NOTE]
    > Docker 환경의 uWSGI에서 Flask를 실행하려면, 먼저 uWSGI 구성 파일(uwsgi.ini)에 `lazy-apps = true`을 추가해야 합니다. 자세한 내용은 [이슈 설명](https://github.com/census-instrumentation/opencensus-python/issues/660)을 참조하십시오. 
    
## <a name="tracking-pyramid-applications"></a>Pyramid 애플리케이션 추적

1. [PyPI](https://pypi.org/project/opencensus-ext-pyramid/)에서 `opencensus-ext-django`를 다운로드하여 설치하고 `pyramid` 트윈으로 애플리케이션을 계측합니다. `pyramid` 애플리케이션에 전송된 들어오는 요청이 추적됩니다.

    ```python
    def main(global_config, **settings):
        config = Configurator(settings=settings)
    
        config.add_tween('opencensus.ext.pyramid'
                         '.pyramid_middleware.OpenCensusTweenFactory')
    ```

2. 코드에서 직접 `pyramid` 트윈을 구성할 수 있습니다. 추적하지 않으려는 URL의 요청은 `EXCLUDELIST_PATHS`에 추가합니다.

    ```python
    settings = {
        'OPENCENSUS': {
            'TRACE': {
                'SAMPLER': 'opencensus.trace.samplers.ProbabilitySampler(rate=1.0)',
                'EXPORTER': '''opencensus.ext.azure.trace_exporter.AzureExporter(
                    connection_string="InstrumentationKey=<your-ikey-here>",
                )''',
                'EXCLUDELIST_PATHS': ['https://example.com'],  <--- These sites will not be traced if a request is sent to it.
            }
        }
    }
    config = Configurator(settings=settings)
    ```

## <a name="tracking-fastapi-applications"></a>FastAPI 애플리케이션 추적

OpenCensus에는 FastAPI에 대한 확장명이 없습니다. 고유한 FastAPI 미들웨어를 작성하려면 다음 단계를 완료합니다.

1. 다음 종속성이 필요합니다. 
    - [fastapi](https://pypi.org/project/fastapi/)
    - [uvicorn](https://pypi.org/project/uvicorn/)

2. [FastAPI 미들웨어](https://fastapi.tiangolo.com/tutorial/middleware/)를 추가합니다. 범위 종류 서버를 다음과 같이 설정했는지 확인합니다. `span.span_kind = SpanKind.SERVER`

3. 애플리케이션을 실행합니다. FastAPI 애플리케이션에 대한 호출은 자동으로 추적되고 원격 분석 데이터는 Azure Monitor에 직접 로그되어야 합니다.

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
* [로그(분석) 쿼리](../logs/log-query-overview.md)
* [트랜잭션 진단](./transaction-diagnostics.md)

