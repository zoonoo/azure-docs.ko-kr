---
title: OpenCensus Python을 사용한 Azure Application Insights 종속성 추적 | Microsoft Docs
description: OpenCensus Python을 통해 Python 앱에 대한 종속성 호출을 모니터링합니다.
ms.topic: conceptual
author: lzchen
ms.author: lechen
ms.date: 10/15/2019
ms.custom: devx-track-python
ms.openlocfilehash: a8f673295236d60ec6681bbfaee1201a4659674b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100585701"
---
# <a name="track-dependencies-with-opencensus-python"></a>OpenCensus Python을 사용하여 종속성 추적

종속성은 애플리케이션에서 호출하는 외부 구성 요소로, 종속성 데이터는 OpenCensus Python 및 다양한 통합을 사용하여 수집됩니다. 그런 다음 데이터는 `dependencies` 원격 분석 데이터로 Azure Monitor의 Application Insights에 전송됩니다.

먼저 최신 [OpenCensus Python SDK](./opencensus-python.md)를 사용하여 Python 애플리케이션을 계측합니다.

## <a name="in-process-dependencies"></a>In Process 종속성

Azure Monitor용 OpenCensus Python SDK를 사용하면 “in-process” 종속성 원격 분석 데이터(애플리케이션 내에서 발생하는 정보 및 논리)를 보낼 수 있습니다. In Process 종속성은 `type` 필드를 분석의 `INPROC`로서 갖게 됩니다.

```python
from opencensus.ext.azure.trace_exporter import AzureExporter
from opencensus.trace.samplers import ProbabilitySampler
from opencensus.trace.tracer import Tracer

tracer = Tracer(exporter=AzureExporter(connection_string="InstrumentationKey=<your-ikey-here>"), sampler=ProbabilitySampler(1.0))

with tracer.span(name='foo'): # <-- A dependency telemetry item will be sent for this span "foo"
    print('Hello, World!')
```

## <a name="dependencies-with-requests-integration"></a>“요청” 통합을 사용한 종속성

OpenCensus `requests` 통합을 사용하여 나가는 요청을 추적합니다.

[PyPI](https://pypi.org/project/opencensus-ext-requests/)에서 `opencensus-ext-requests`을 다운로드하여 설치하고 추적 통합에 추가합니다. Python [요청](https://pypi.org/project/requests/) 라이브러리를 사용하여 전송된 요청은 추적됩니다.

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

## <a name="dependencies-with-httplib-integration"></a>“httplib” 통합을 사용한 종속성

OpenCensus `httplib` 통합을 사용하여 나가는 요청을 추적합니다.

[PyPI](https://pypi.org/project/opencensus-ext-httplib/)에서 `opencensus-ext-httplib`을 다운로드하여 설치하고 추적 통합에 추가합니다. Python3의 [http.client](https://docs.python.org/3.7/library/http.client.html) 또는 Python2의 [httplib](https://docs.python.org/2/library/httplib.html)을 사용하여 전송된 요청은 추적됩니다.

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

## <a name="dependencies-with-django-integration"></a>“django” 통합을 사용한 종속성

OpenCensus `django` 통합을 사용하여 나가는 Django 요청을 추적합니다.

> [!NOTE]
> 나가는 Django 요청 중에서는 오직 데이터베이스에 대한 호출만이 추적됩니다. Django 애플리케이션에 대한 요청의 경우 [들어오는 요청](./opencensus-python-request.md#tracking-django-applications)을 참조하세요.

[PyPI](https://pypi.org/project/opencensus-ext-django/)에서 `opencensus-ext-django`를 다운로드하여 설치하고 Django `settings.py` 파일의 `MIDDLEWARE` 섹션에 다음 줄을 추가합니다.

```python
MIDDLEWARE = [
    ...
    'opencensus.ext.django.middleware.OpencensusMiddleware',
]
```

추가 구성을 제공하고, 전체 참조를 위해 [사용자 지정](https://github.com/census-instrumentation/opencensus-python#customization)을 읽을 수 있습니다.

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

## <a name="dependencies-with-mysql-integration"></a>“mysql” 통합을 사용한 종속성

OpenCensus `mysql` 통합을 사용하여 MYSQL 종속성을 추적합니다. 해당 통합은 [mysql-connector](https://pypi.org/project/mysql-connector-python/) 라이브러리를 지원합니다.

[PyPI](https://pypi.org/project/opencensus-ext-mysql/)에서 `opencensus-ext-mysql`을 다운로드하여 설치하고 코드에 다음 줄을 추가합니다.

```python
from opencensus.trace import config_integration

config_integration.trace_integrations(['mysql'])
```

## <a name="dependencies-with-pymysql-integration"></a>“pymysql” 통합을 사용한 종속성

OpenCensus `pymysql` 통합을 사용하여 PyMySQL 종속성을 추적합니다.

[PyPI](https://pypi.org/project/opencensus-ext-pymysql/)에서 `opencensus-ext-pymysql`을 다운로드하여 설치하고 코드에 다음 줄을 추가합니다.

```python
from opencensus.trace import config_integration

config_integration.trace_integrations(['pymysql'])
```

## <a name="dependencies-with-postgresql-integration"></a>“postgresql” 통합을 사용한 종속성

OpenCensus `postgresql` 통합을 사용하여 PostgreSQL 종속성을 추적합니다. 해당 통합은 [psycopg2](https://pypi.org/project/psycopg2/) 라이브러리를 지원합니다.

[PyPI](https://pypi.org/project/opencensus-ext-postgresql/)에서 `opencensus-ext-postgresql`을 다운로드하여 설치하고 코드에 다음 줄을 추가합니다.

```python
from opencensus.trace import config_integration

config_integration.trace_integrations(['postgresql'])
```

## <a name="dependencies-with-pymongo-integration"></a>“pymongo” 통합을 사용한 종속성

OpenCensus `pymongo` 통합을 사용하여 MongoDB 종속성을 추적합니다. 해당 통합은 [pymongo](https://pypi.org/project/pymongo/) 라이브러리를 지원합니다.

[PyPI](https://pypi.org/project/opencensus-ext-pymongo/)에서 `opencensus-ext-pymongo`을 다운로드하여 설치하고 코드에 다음 줄을 추가합니다.

```python
from opencensus.trace import config_integration

config_integration.trace_integrations(['pymongo'])
```

### <a name="dependencies-with-sqlalchemy-integration"></a>“sqlalchemy” 통합을 사용한 종속성

OpenCensus `sqlalchemy` 통합을 통해 SQLAlchemy를 사용하여 종속성을 추적합니다. 해당 통합은 기본 데이터베이스에 관계없이 [sqlalchemy](https://pypi.org/project/SQLAlchemy/) 패키지의 사용을 추적합니다.

```python
from opencensus.trace import config_integration

config_integration.trace_integrations(['sqlalchemy'])
```

## <a name="next-steps"></a>다음 단계

* [애플리케이션 맵](./app-map.md)
* [가용성](./monitor-web-app-availability.md)
* [검색](./diagnostic-search.md)
* [로그(분석) 쿼리](../logs/log-query-overview.md)
* [트랜잭션 진단](./transaction-diagnostics.md)

