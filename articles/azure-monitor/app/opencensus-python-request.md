---
title: OpenCensus 파이썬을 사용하여 Azure 응용 프로그램 인사이트에서 들어오는 요청 추적 | 마이크로 소프트 문서
description: OpenCensus 파이썬을 통해 파이썬 앱에 대한 요청 호출을 모니터링하십시오.
ms.topic: conceptual
author: lzchen
ms.author: lechen
ms.date: 10/15/2019
ms.openlocfilehash: 0396bd8d150c6145a39f36e7be9e6e2dcacef2c4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77669950"
---
# <a name="track-incoming-requests-with-opencensus-python"></a>오픈인구파이썬으로 들어오는 요청을 추적

들어오는 요청 데이터는 OpenCensus 파이썬과 다양한 통합을 사용하여 수집됩니다. 인기 있는 웹 프레임워크 위에 구축된 웹 응용 프로그램으로 전송되는 `flask` `pyramid`수신 요청 데이터를 추적합니다. `django` 그런 다음 데이터는 Azure 모니터에서 원격 `requests` 분석으로 응용 프로그램 인사이트로 전송됩니다.

첫째, 최신 [OpenCensus 파이썬 SDK와](../../azure-monitor/app/opencensus-python.md)파이썬 응용 프로그램을 계측 .

## <a name="tracking-django-applications"></a>장고 응용 프로그램 추적

1. [PyPI에서](https://pypi.org/project/opencensus-ext-django/) 다운로드 하여 설치하고 `django` `opencensus-ext-django` 미들웨어로 애플리케이션을 계측하십시오. `django` 응용 프로그램으로 전송되는 들어오는 요청이 추적됩니다.

2. 아래 `opencensus.ext.django.middleware.OpencensusMiddleware` 파일에 `settings.py` `MIDDLEWARE`포함합니다.

    ```python
    MIDDLEWARE = (
        ...
        'opencensus.ext.django.middleware.OpencensusMiddleware',
        ...
    )
    ```

3. 아래 에서 Azure Exporter이 제대로 `settings.py` `OPENCENSUS`구성되었는지 확인합니다.

    ```python
    OPENCENSUS = {
        'TRACE': {
            'SAMPLER': 'opencensus.trace.samplers.ProbabilitySampler(rate=1)',
            'EXPORTER': '''opencensus.ext.azure.trace_exporter.AzureExporter(
                connection_string="InstrumentationKey=<your-ikey-here>"
            )''',
        }
    }
    ```

4. 추적하지 않으려는 요청에 `settings.py` `BLACKLIST_PATHS` 대해 아래 URL을 추가할 수도 있습니다.

    ```python
    OPENCENSUS = {
        'TRACE': {
            'SAMPLER': 'opencensus.trace.samplers.ProbabilitySampler(rate=0.5)',
            'EXPORTER': '''opencensus.ext.azure.trace_exporter.AzureExporter(
                connection_string="InstrumentationKey=<your-ikey-here>",
            )''',
            'BLACKLIST_PATHS': ['https://example.com'],  <--- These sites will not be traced if a request is sent from it.
        }
    }
    ```

## <a name="tracking-flask-applications"></a>플라스크 애플리케이션 추적

1. [PyPI에서](https://pypi.org/project/opencensus-ext-flask/) 다운로드 하여 설치하고 `flask` `opencensus-ext-flask` 미들웨어로 애플리케이션을 계측하십시오. `flask` 응용 프로그램으로 전송되는 들어오는 요청이 추적됩니다.

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

2. 코드에서 직접 `flask` 미들웨어를 구성할 수 있습니다. 추적하지 않으려는 URL의 요청의 경우 에 `BLACKLIST_PATHS`추가합니다.

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

1. [PyPI에서](https://pypi.org/project/opencensus-ext-pyramid/) 다운로드 하여 설치하고 `pyramid` `opencensus-ext-django` 핀으로 애플리케이션을 계측하십시오. `pyramid` 응용 프로그램으로 전송되는 들어오는 요청이 추적됩니다.

    ```python
    def main(global_config, **settings):
        config = Configurator(settings=settings)
    
        config.add_tween('opencensus.ext.pyramid'
                         '.pyramid_middleware.OpenCensusTweenFactory')
    ```

2. 코드에서 직접 `pyramid` 트위넨을 구성할 수 있습니다. 추적하지 않으려는 URL의 요청의 경우 에 `BLACKLIST_PATHS`추가합니다.

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

* [애플리케이션 맵](../../azure-monitor/app/app-map.md)
* [가용성](../../azure-monitor/app/monitor-web-app-availability.md)
* [검색](../../azure-monitor/app/diagnostic-search.md)
* [로그(분석) 쿼리](../../azure-monitor/log-query/log-query-overview.md)
* [트랜잭션 진단](../../azure-monitor/app/transaction-diagnostics.md)
