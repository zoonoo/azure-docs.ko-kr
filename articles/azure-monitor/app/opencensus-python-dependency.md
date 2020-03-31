---
title: OpenCensus 파이썬을 사용하여 Azure 응용 프로그램 인사이트내 종속성 추적 | 마이크로 소프트 문서
description: OpenCensus 파이썬을 통해 파이썬 앱에 대한 종속성 호출을 모니터링합니다.
ms.topic: conceptual
author: lzchen
ms.author: lechen
ms.date: 10/15/2019
ms.openlocfilehash: e400669fd96518adead74a81fc332767c5f9b23b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77669933"
---
# <a name="track-dependencies-with-opencensus-python"></a>오픈인구파이썬으로 종속성을 추적

종속성은 응용 프로그램에서 호출하는 외부 구성 요소입니다. 종속성 데이터는 OpenCensus 파이썬과 다양한 통합을 사용하여 수집됩니다. 그런 다음 데이터는 Azure 모니터에서 원격 `dependencies` 분석으로 응용 프로그램 인사이트로 전송됩니다.

첫째, 최신 [OpenCensus 파이썬 SDK와](../../azure-monitor/app/opencensus-python.md)파이썬 응용 프로그램을 계측 .

## <a name="in-process-dependencies"></a>프로세스 내 종속성

Azure 모니터에 대한 OpenCensus 파이썬 SDK를 사용하면 "프로세스 중" 종속성 원격 분석(응용 프로그램 내에서 발생하는 정보 및 논리)을 보낼 수 있습니다. 프로세스 내 종속성에는 분석과 `type` 같은 `INPROC` 필드가 있습니다.

```python
from opencensus.ext.azure.trace_exporter import AzureExporter
from opencensus.trace.samplers import ProbabilitySampler
from opencensus.trace.tracer import Tracer

tracer = Tracer(exporter=AzureExporter(connection_string="InstrumentationKey=<your-ikey-here>"), sampler=ProbabilitySampler(1.0))

with tracer.span(name='foo'): # <-- A dependency telemetry item will be sent for this span "foo"
    print('Hello, World!')
```

## <a name="dependencies-with-requests-integration"></a>"요청" 통합을 가진 종속성

OpenCensus 통합을 통해 나가는 `requests` 요청을 추적할 수 있습니다.

`opencensus-ext-requests` [PyPI에서](https://pypi.org/project/opencensus-ext-requests/) 다운로드 하여 설치하고 추적 통합에 추가합니다. Python [요청](https://pypi.org/project/requests/) 라이브러리를 사용하여 전송된 요청이 추적됩니다.

```python
import requests
from opencensus.ext.azure.trace_exporter import AzureExporter
from opencensus.trace import config_integration
from opencensus.trace.samplers import ProbabilitySampler
from opencensus.trace.tracer import Tracer

config_integration.trace_integrations(['requests'])  # <-- this line enables the requests integration

tracer = Tracer(exporter=AzureExporter(connection_string="InstrumentationKey=<your-ikey-here>"), sampler=ProbabilitySampler(1.0))

with tracer.span(name='parent'):
    response = requests.get(url='https://www.wikipedia.org/wiki/Rabbit') # <-- this request will be tracked
```

## <a name="dependencies-with-httplib-integration"></a>"httplib" 통합을 가진 종속성

OpenCensus 통합을 통해 `httplib` 나가는 요청을 추적할 수 있습니다.

`opencensus-ext-httplib` [PyPI에서](https://pypi.org/project/opencensus-ext-httplib/) 다운로드 하여 설치하고 추적 통합에 추가합니다. 파이썬3에 대한 [http.client](https://docs.python.org/3.7/library/http.client.html) 또는 Python2에 대한 [httplib를](https://docs.python.org/2/library/httplib.html) 사용하여 전송된 요청이 추적됩니다.

```python
import http.client as httplib
from opencensus.ext.azure.trace_exporter import AzureExporter
from opencensus.trace import config_integration
from opencensus.trace.samplers import ProbabilitySampler
from opencensus.trace.tracer import Tracer

config_integration.trace_integrations(['httplib'])
conn = httplib.HTTPConnection("www.python.org")

tracer = Tracer(
    exporter=AzureExporter(),
    sampler=ProbabilitySampler(1.0)
)

conn.request("GET", "http://www.python.org", "", {})
response = conn.getresponse()
conn.close()
```

## <a name="dependencies-with-django-integration"></a>"장고" 통합에 대한 종속성

OpenCensus `django` 통합을 통해 나가는 장고 요청을 추적할 수 있습니다.

[PyPI에서](https://pypi.org/project/opencensus-ext-django/) 다운로드하여 설치하고 `MIDDLEWARE` `settings.py` `opencensus-ext-django` 장고 파일의 섹션에 다음 줄을 추가합니다.

```python
MIDDLEWARE = [
    ...
    'opencensus.ext.django.middleware.OpencensusMiddleware',
]
```

추가 구성을 제공하고 전체 참조를 위해 사용자 정의를 읽을 수 [있습니다.](https://github.com/census-instrumentation/opencensus-python#customization)

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

## <a name="dependencies-with-mysql-integration"></a>"mysql" 통합을 가진 종속성

OpenCensus `mysql` 통합을 통해 MYSQL 종속성을 추적할 수 있습니다. 이 통합은 [mysql 커넥터](https://pypi.org/project/mysql-connector-python/) 라이브러리를 지원합니다.

`opencensus-ext-mysql` [PyPI에서](https://pypi.org/project/opencensus-ext-mysql/) 다운로드 하여 설치하고 다음 줄을 코드에 추가합니다.

```python
from opencensus.trace import config_integration

config_integration.trace_integrations(['mysql'])
```

## <a name="dependencies-with-pymysql-integration"></a>"pymysql" 통합을 가진 종속성

OpenCensus `pymysql` 통합을 통해 PyMySQL 종속성을 추적할 수 있습니다.

`opencensus-ext-pymysql` [PyPI에서](https://pypi.org/project/opencensus-ext-pymysql/) 다운로드 하여 설치하고 다음 줄을 코드에 추가합니다.

```python
from opencensus.trace import config_integration

config_integration.trace_integrations(['pymysql'])
```

## <a name="dependencies-with-postgresql-integration"></a>"postgresql" 통합을 가진 종속성

OpenCensus `postgresql` 통합을 통해 PostgreSQL 종속성을 추적합니다. 이 통합은 [psycopg2](https://pypi.org/project/psycopg2/) 라이브러리를 지원합니다.

`opencensus-ext-postgresql` [PyPI에서](https://pypi.org/project/opencensus-ext-postgresql/) 다운로드 하여 설치하고 다음 줄을 코드에 추가합니다.

```python
from opencensus.trace import config_integration

config_integration.trace_integrations(['postgresql'])
```

## <a name="dependencies-with-pymongo-integration"></a>"피몬고" 통합을 가진 종속성

OpenCensus `pymongo` 통합을 통해 MongoDB 종속성을 추적합니다. 이 통합은 [피몬고](https://pypi.org/project/pymongo/) 라이브러리를 지원합니다.

`opencensus-ext-pymongo` [PyPI에서](https://pypi.org/project/opencensus-ext-pymongo/) 다운로드 하여 설치하고 다음 줄을 코드에 추가합니다.

```python
from opencensus.trace import config_integration

config_integration.trace_integrations(['pymongo'])
```

### <a name="dependencies-with-sqlalchemy-integration"></a>"sqlalchemy" 통합을 가진 종속성

OpenCensus 통합을 사용하여 SQLAlchemy를 `sqlalchemy` 사용하여 종속성을 추적합니다. 이 통합은 기본 데이터베이스에 관계없이 [sqlalchemy](https://pypi.org/project/SQLAlchemy/) 패키지의 사용을 추적합니다.

```python
from opencensus.trace import config_integration

config_integration.trace_integrations(['sqlalchemy'])
```

## <a name="next-steps"></a>다음 단계

* [애플리케이션 맵](../../azure-monitor/app/app-map.md)
* [가용성](../../azure-monitor/app/monitor-web-app-availability.md)
* [검색](../../azure-monitor/app/diagnostic-search.md)
* [로그(분석) 쿼리](../../azure-monitor/log-query/log-query-overview.md)
* [트랜잭션 진단](../../azure-monitor/app/transaction-diagnostics.md)
