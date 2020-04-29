---
title: OpenCensus Python을 사용 하 여 Azure 애플리케이션 정보에서 종속성 추적 | Microsoft Docs
description: OpenCensus Python을 통해 Python 앱에 대 한 종속성 호출을 모니터링 합니다.
ms.topic: conceptual
author: lzchen
ms.author: lechen
ms.date: 10/15/2019
ms.openlocfilehash: e400669fd96518adead74a81fc332767c5f9b23b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "77669933"
---
# <a name="track-dependencies-with-opencensus-python"></a>OpenCensus Python으로 종속성 추적

종속성은 응용 프로그램에서 호출 하는 외부 구성 요소입니다. 종속성 데이터는 OpenCensus Python 및 다양 한 통합을 사용 하 여 수집 됩니다. 그런 다음 데이터는 원격 분석으로 `dependencies` Azure Monitor Application Insights 전송 됩니다.

먼저 최신 [OpenCensus PYTHON SDK](../../azure-monitor/app/opencensus-python.md)를 사용 하 여 python 응용 프로그램을 계측 합니다.

## <a name="in-process-dependencies"></a>In-process 종속성

OpenCensus Python SDK for Azure Monitor를 사용 하면 "in-process" 종속성 원격 분석 (응용 프로그램 내에서 발생 하는 정보 및 논리)을 보낼 수 있습니다. In-process 종속성은 analytics에서와 같이 `type` `INPROC` 필드를 갖게 됩니다.

```python
from opencensus.ext.azure.trace_exporter import AzureExporter
from opencensus.trace.samplers import ProbabilitySampler
from opencensus.trace.tracer import Tracer

tracer = Tracer(exporter=AzureExporter(connection_string="InstrumentationKey=<your-ikey-here>"), sampler=ProbabilitySampler(1.0))

with tracer.span(name='foo'): # <-- A dependency telemetry item will be sent for this span "foo"
    print('Hello, World!')
```

## <a name="dependencies-with-requests-integration"></a>"요청" 통합이 포함 된 종속성

OpenCensus `requests` 통합을 사용 하 여 나가는 요청을 추적 합니다.

`opencensus-ext-requests` [Pypi](https://pypi.org/project/opencensus-ext-requests/) 에서 다운로드 하 여 설치 하 고 통합 추적에 추가 합니다. Python [요청](https://pypi.org/project/requests/) 라이브러리를 사용 하 여 전송 된 요청은 추적 됩니다.

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

## <a name="dependencies-with-httplib-integration"></a>"Httplib" 통합을 사용한 종속성

OpenCensus `httplib` 통합을 사용 하 여 나가는 요청을 추적 합니다.

`opencensus-ext-httplib` [Pypi](https://pypi.org/project/opencensus-ext-httplib/) 에서 다운로드 하 여 설치 하 고 통합 추적에 추가 합니다. [Http](https://docs.python.org/3.7/library/http.client.html) 를 사용 하 여 전송 된 요청은 Python3 또는 [httplib](https://docs.python.org/2/library/httplib.html) for Python2에 대해 추적 됩니다.

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

## <a name="dependencies-with-django-integration"></a>"Django" 통합을 사용한 종속성

OpenCensus `django` 통합을 사용 하 여 나가는 Django 요청을 추적 합니다.

[Pypi](https://pypi.org/project/opencensus-ext-django/) 에서 다운로드 하 여 설치 `MIDDLEWARE` `settings.py` `opencensus-ext-django` 하 고 Django 파일의 섹션에 다음 줄을 추가 합니다.

```python
MIDDLEWARE = [
    ...
    'opencensus.ext.django.middleware.OpencensusMiddleware',
]
```

추가 구성을 제공 하 고, 전체 참조를 위해 [사용자 지정](https://github.com/census-instrumentation/opencensus-python#customization) 을 읽을 수 있습니다.

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

## <a name="dependencies-with-mysql-integration"></a>"Mysql" 통합을 사용한 종속성

OpenCensus `mysql` 통합을 사용 하 여 MYSQL 종속성을 추적 합니다. 이 통합은 [mysql 커넥터](https://pypi.org/project/mysql-connector-python/) 라이브러리를 지원 합니다.

`opencensus-ext-mysql` [Pypi](https://pypi.org/project/opencensus-ext-mysql/) 에서 다운로드 하 여 설치 하 고 코드에 다음 줄을 추가 합니다.

```python
from opencensus.trace import config_integration

config_integration.trace_integrations(['mysql'])
```

## <a name="dependencies-with-pymysql-integration"></a>"Pymysql" 통합을 사용한 종속성

OpenCensus `pymysql` 통합을 사용 하 여 PyMySQL 종속성을 추적 합니다.

`opencensus-ext-pymysql` [Pypi](https://pypi.org/project/opencensus-ext-pymysql/) 에서 다운로드 하 여 설치 하 고 코드에 다음 줄을 추가 합니다.

```python
from opencensus.trace import config_integration

config_integration.trace_integrations(['pymysql'])
```

## <a name="dependencies-with-postgresql-integration"></a>"Postgresql" 통합을 사용한 종속성

OpenCensus `postgresql` 통합을 사용 하 여 PostgreSQL 종속성을 추적 합니다. 이 통합은 [psycopg2](https://pypi.org/project/psycopg2/) 라이브러리를 지원 합니다.

`opencensus-ext-postgresql` [Pypi](https://pypi.org/project/opencensus-ext-postgresql/) 에서 다운로드 하 여 설치 하 고 코드에 다음 줄을 추가 합니다.

```python
from opencensus.trace import config_integration

config_integration.trace_integrations(['postgresql'])
```

## <a name="dependencies-with-pymongo-integration"></a>"Pymongo" 통합을 사용한 종속성

OpenCensus `pymongo` 통합을 사용 하 여 MongoDB 종속성을 추적 합니다. 이 통합은 [pymongo](https://pypi.org/project/pymongo/) 라이브러리를 지원 합니다.

`opencensus-ext-pymongo` [Pypi](https://pypi.org/project/opencensus-ext-pymongo/) 에서 다운로드 하 여 설치 하 고 코드에 다음 줄을 추가 합니다.

```python
from opencensus.trace import config_integration

config_integration.trace_integrations(['pymongo'])
```

### <a name="dependencies-with-sqlalchemy-integration"></a>"Sqlalchemy" 통합을 사용한 종속성

OpenCensus `sqlalchemy` 통합을 사용 하 여 SQLAlchemy를 사용 하 여 종속성을 추적 합니다. 이 통합은 기본 데이터베이스에 관계 없이 [sqlalchemy](https://pypi.org/project/SQLAlchemy/) 패키지의 사용을 추적 합니다.

```python
from opencensus.trace import config_integration

config_integration.trace_integrations(['sqlalchemy'])
```

## <a name="next-steps"></a>다음 단계

* [애플리케이션 맵](../../azure-monitor/app/app-map.md)
* [가용성](../../azure-monitor/app/monitor-web-app-availability.md)
* [검색](../../azure-monitor/app/diagnostic-search.md)
* [로그 (분석) 쿼리](../../azure-monitor/log-query/log-query-overview.md)
* [트랜잭션 진단](../../azure-monitor/app/transaction-diagnostics.md)
