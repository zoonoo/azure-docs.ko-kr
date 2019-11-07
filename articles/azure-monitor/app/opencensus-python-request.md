---
title: OpenCensus Python을 사용 하 여 Azure 애플리케이션 정보에서 들어오는 요청 추적 | Microsoft Docs
description: OpenCensus Python을 통해 Python 앱에 대 한 요청 호출을 모니터링 합니다.
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: lzchen
ms.author: lechen
ms.date: 10/15/2019
ms.openlocfilehash: f6b06e7bb2bb8637ca28b2fa4185754f8686798e
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73587928"
---
# <a name="track-incoming-requests-with-opencensus-python"></a>OpenCensus Python을 사용 하 여 들어오는 요청 추적

들어오는 요청 데이터는 OpenCensus Python 및 다양 한 통합을 사용 하 여 수집 됩니다. 인기 있는 웹 프레임 워크를 기반으로 작성 된 들어오는 요청 데이터를 추적 `django`, `flask` 및 `pyramid`합니다. 그런 다음 데이터는 Application Insights Azure Monitor `requests` 원격 분석으로 전송 됩니다.

먼저 최신 [OpenCensus PYTHON SDK](../../azure-monitor/app/opencensus-python.md)를 사용 하 여 python 응용 프로그램을 계측 합니다.

## <a name="tracking-django-applications"></a>Django 응용 프로그램 추적

1. [Pypi](https://pypi.org/project/opencensus-ext-django/) 에서 `opencensus-ext-django`를 다운로드 및 설치 하 고 `django` 미들웨어를 사용 하 여 응용 프로그램을 계측 합니다. `django` 응용 프로그램으로 전송 된 들어오는 요청이 추적 됩니다.

2. `MIDDLEWARE`에서 `settings.py` 파일에 `opencensus.ext.django.middleware.OpencensusMiddleware`를 포함 합니다.

    ```python
    MIDDLEWARE = (
        ...
        'opencensus.ext.django.middleware.OpencensusMiddleware',
        ...
    )
    ```

3. `settings.py` `OPENCENSUS`에서 AzureExporter 올바르게 구성 되어 있는지 확인 합니다.

    ```python
    OPENCENSUS = {
        'TRACE': {
            'SAMPLER': 'opencensus.trace.samplers.ProbabilitySampler(rate=0.5)',
            'EXPORTER': '''opencensus.ext.azure.trace_exporter.AzureExporter(
                service_name='foobar',
            )''',
        }
    }
    ```

4. 추적 하지 않으려는 요청에 대해 `BLACKLIST_PATHS`에서 `settings.py`에 url을 추가할 수도 있습니다.

    ```python
    OPENCENSUS = {
        'TRACE': {
            'SAMPLER': 'opencensus.trace.samplers.ProbabilitySampler(rate=0.5)',
            'EXPORTER': '''opencensus.ext.azure.trace_exporter.AzureExporter(
                service_name='foobar',
            )''',
            'BLACKLIST_PATHS': 'https://example.com',  <--- This site will not be traced if a request is sent from it.
        }
    }
    ```

## <a name="tracking-flask-applications"></a>Flask 응용 프로그램 추적

1. [Pypi](https://pypi.org/project/opencensus-ext-flask/) 에서 `opencensus-ext-flask`를 다운로드 및 설치 하 고 `flask` 미들웨어를 사용 하 여 응용 프로그램을 계측 합니다. `flask` 응용 프로그램으로 전송 된 들어오는 요청이 추적 됩니다.

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

2. 코드에서 직접 `flask` 미들웨어를 구성할 수 있습니다. 추적 하지 않으려는 url의 요청의 경우 `BLACKLIST_PATHS`에 추가 합니다.

    ```python
    app.config['OPENCENSUS'] = {
        'TRACE': {
            'SAMPLER': 'opencensus.trace.samplers.ProbabilitySampler(rate=1.0)',
            'EXPORTER': '''opencensus.ext.azure.trace_exporter.AzureExporter(
                service_name='foobar',
            )''',
            'BLACKLIST_PATHS': 'https://example.com',  <--- This site will not be traced if a request is sent to it.
        }
    }
    ```

## <a name="tracking-pyramid-applications"></a>피라미드 응용 프로그램 추적

1. [Pypi](https://pypi.org/project/opencensus-ext-pyramid/) 에서 `opencensus-ext-django`를 다운로드 하 여 설치 하 고 `pyramid` 트윈으로 응용 프로그램을 계측 합니다. `pyramid` 응용 프로그램으로 전송 된 들어오는 요청이 추적 됩니다.

    ```python
    def main(global_config, **settings):
        config = Configurator(settings=settings)
    
        config.add_tween('opencensus.ext.pyramid'
                         '.pyramid_middleware.OpenCensusTweenFactory')
    ```

2. 코드에서 직접 `pyramid` 트윈을 구성할 수 있습니다. 추적 하지 않으려는 url의 요청의 경우 `BLACKLIST_PATHS`에 추가 합니다.

    ```python
    settings = {
        'OPENCENSUS': {
            'TRACE': {
                'SAMPLER': 'opencensus.trace.samplers.ProbabilitySampler(rate=1.0)',
                'EXPORTER': '''opencensus.ext.azure.trace_exporter.AzureExporter(
                    service_name='foobar',
                )''',
                'BLACKLIST_PATHS': 'https://example.com',  <--- This site will not be traced if a request is sent to it.
            }
        }
    }
    config = Configurator(settings=settings)
    ```

## <a name="next-steps"></a>다음 단계

* [애플리케이션 맵](../../azure-monitor/app/app-map.md)
* [가용성](../../azure-monitor/app/monitor-web-app-availability.md)
* [이를 통해 검색](../../azure-monitor/app/diagnostic-search.md)
* [로그 (분석) 쿼리](../../azure-monitor/log-query/log-query-overview.md)
* [트랜잭션 진단](../../azure-monitor/app/transaction-diagnostics.md)
