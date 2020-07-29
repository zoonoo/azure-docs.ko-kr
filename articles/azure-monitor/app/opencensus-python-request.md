---
title: OpenCensus Python을 사용 하 여 Azure 애플리케이션 정보에서 들어오는 요청 추적 | Microsoft Docs
description: OpenCensus Python을 통해 Python 앱에 대 한 요청 호출을 모니터링 합니다.
ms.topic: conceptual
author: lzchen
ms.author: lechen
ms.date: 10/15/2019
ms.custom: tracking-python
ms.openlocfilehash: 6eae11e2157904b47582f78a601c8d2900e61247
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87324593"
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

## <a name="next-steps"></a>다음 단계

* [애플리케이션 맵](./app-map.md)
* [가용성](./monitor-web-app-availability.md)
* [검색](./diagnostic-search.md)
* [로그 (분석) 쿼리](../log-query/log-query-overview.md)
* [트랜잭션 진단](./transaction-diagnostics.md)

