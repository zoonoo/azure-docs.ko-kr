---
title: Azure Functions에서 Python 앱의 처리량 성능 향상
description: 뛰어난 성능과 부하가 뛰어난 Python을 사용 하 여 Azure Functions 앱을 개발 하는 방법을 알아보세요.
ms.topic: article
ms.date: 10/13/2020
ms.custom: devx-track-python
ms.openlocfilehash: e3bbdb8819062d45d071633e0208fb58a003da54
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98786109"
---
# <a name="improve-throughput-performance-of-python-apps-in-azure-functions"></a>Azure Functions에서 Python 앱의 처리량 성능 향상

Python을 사용 하 여 Azure Functions를 개발 하는 경우 함수에서 수행 하는 방식과 함수 앱의 크기를 조정 하는 방법에 대 한 성능 영향을 이해 해야 합니다. 고성능 앱을 디자인할 때 요구 사항이 더 중요 합니다. 함수 앱을 디자인, 작성 및 구성할 때 고려해 야 할 주요 요소는 수평적 크기 조정 및 처리량 성능 구성입니다.

## <a name="horizontal-scaling"></a>수평 크기 조정
기본적으로 Azure Functions는 애플리케이션의 부하를 자동으로 모니터링하면서 필요에 따라 Python에 사용할 추가 호스트 인스턴스를 만듭니다. Azure Functions는 여러 트리거 유형에 대해 기본 제공 임계값을 사용 하 여 QueueTrigger의 메시지 보존 기간 및 큐 크기와 같은 인스턴스를 추가할 시기를 결정 합니다. 이러한 임계값은 사용자가 구성할 수 없습니다. 자세한 내용은 [Azure Functions 이벤트 기반 크기 조정](event-driven-scaling.md)을 참조 하세요.

## <a name="improving-throughput-performance"></a>처리량 성능 향상

기본 구성은 대부분의 Azure Functions 응용 프로그램에 적합 합니다. 그러나 워크 로드 프로필을 기반으로 구성을 사용 하 여 응용 프로그램 처리량의 성능을 향상 시킬 수 있습니다. 첫 번째 단계는 실행 중인 작업의 유형을 이해 하는 것입니다.

| 워크로드 유형 | 함수 앱 특징       | 예제                                          |
| ------------- | ---------------------------------- | ------------------------------------------------- |
| **I/o 바인딩**     | • 앱은 많은 동시 호출을 처리 해야 합니다.<br>• 앱은 네트워크 호출 및 디스크 읽기/쓰기와 같은 많은 i/o 이벤트를 처리 합니다. | • 웹 Api                                          |
| **CPU 바인딩**     | • 앱은 이미지 크기 조정과 같은 장기 실행 계산을 수행 합니다.<br>• 앱은 데이터를 변환 합니다.                                                | • 데이터 처리<br>• 기계 학습 유추<br> |

 
실제 기능 워크 로드는 일반적으로 i/o와 CPU가 혼합 된 것 이므로 실제 프로덕션 부하에서 앱을 프로 파일링 해야 합니다.


### <a name="performance-specific-configurations"></a>성능 관련 구성

함수 앱의 작업 프로필을 이해 한 후에는 함수에 대 한 처리량 성능을 향상 시키기 위해 사용할 수 있는 구성이 다음과 같습니다.

* [Async](#async)
* [여러 언어 작업자](#use-multiple-language-worker-processes)
* [언어 작업자 프로세스 내의 최대 작업자](#set-up-max-workers-within-a-language-worker-process)
* [이벤트 루프](#managing-event-loop)
* [수직 크기 조정](#vertical-scaling)



#### <a name="async"></a>Async

[Python은 단일 스레드 런타임](https://wiki.python.org/moin/GlobalInterpreterLock)이므로 python의 호스트 인스턴스는 기본적으로 한 번에 하나의 함수 호출만 처리할 수 있습니다. 많은 i/o 이벤트를 처리 하는 응용 프로그램 또는 i/o 바인딩된 응용 프로그램의 경우 함수를 비동기적으로 실행 하 여 성능을 크게 향상 시킬 수 있습니다.

함수를 비동기적으로 실행하려면 [asyncio](https://docs.python.org/3/library/asyncio.html)를 사용하여 함수를 직접 실행하는 `async def` 문을 사용하세요.

```python
async def main():
    await some_nonblocking_socket_io_op()
```
[Aiohttp](https://pypi.org/project/aiohttp/) http 클라이언트를 사용 하는 http 트리거를 사용 하는 함수의 예는 다음과 같습니다.

```python
import aiohttp

import azure.functions as func

async def main(req: func.HttpRequest) -> func.HttpResponse:
    async with aiohttp.ClientSession() as client:
        async with client.get("PUT_YOUR_URL_HERE") as response:
            return func.HttpResponse(await response.text())

    return func.HttpResponse(body='NotFound', status_code=404)
```


키워드가 없는 함수는 `async` ThreadPoolExecutor 스레드 풀에서 자동으로 실행 됩니다.

```python
# Runs in an ThreadPoolExecutor threadpool. Number of threads is defined by PYTHON_THREADPOOL_THREAD_COUNT. 
# The example is intended to show how default synchronous function are handled.

def main():
    some_blocking_socket_io()
```

함수를 비동기적으로 실행 하는 이점을 최대한 활용 하기 위해 코드에 사용 되는 i/o 작업/라이브러리에도 async가 구현 되어 있어야 합니다. 비동기로 정의 된 함수에서 동기 i/o 작업을 사용 하면 전체 성능이 **저하 될 수 있습니다** . 사용 중인 라이브러리에 비동기 버전이 구현 되어 있지 않은 경우에도 앱에서 [이벤트 루프를 관리](#managing-event-loop) 하 여 코드를 비동기적으로 실행 하는 것이 유용할 수 있습니다. 

다음은 비동기 패턴을 구현 하는 클라이언트 라이브러리의 몇 가지 예입니다.
- [aiohttp](https://pypi.org/project/aiohttp/) -asyncio 용 http 클라이언트/서버 
- [스트림 API](https://docs.python.org/3/library/asyncio-stream.html) -네트워크 연결을 사용 하기 위한 상위 수준 비동기/대기 중인 기본 형식
- [Janus 큐](https://pypi.org/project/janus/) -Python의 스레드로부터 안전한 asyncio 인식 큐
- [pyzmq](https://pypi.org/project/pyzmq/) -ZeroMQ에 대 한 Python 바인딩
 
##### <a name="understanding-async-in-python-worker"></a>Python 작업자의 비동기 이해

`async`함수 시그니처 앞에를 정의할 때 Python은 함수를 코 루틴로 표시 합니다. 코 루틴를 호출할 때 이벤트 루프에서 작업으로 예약할 수 있습니다. `await`비동기 함수에서를 호출 하는 경우 이벤트 루프에 연속을 등록 하 고 이벤트 루프가 대기 시간 동안 다음 작업을 처리할 수 있도록 합니다.

Python 작업자는 작업자가 이벤트 루프를 고객의 기능과 공유 하 `async` 고 여러 요청을 동시에 처리할 수 있습니다. 고객에 게 asyncio 호환 라이브러리를 사용 하는 것이 좋습니다 (예: [aiohttp](https://pypi.org/project/aiohttp/), [pyzmq](https://pypi.org/project/pyzmq/)). 이러한 권장 사항을 적용 하면 동기 방식으로 구현 된 라이브러리에 비해 함수 처리량이 크게 증가 합니다.

> [!NOTE]
>  함수를 구현 내에서로 선언 `async` 하지 않으면 `await` 이벤트 루프가 차단 되므로 python worker가 동시 요청을 처리할 수 없으므로 함수의 성능이 심각 하 게 영향을 받습니다.

#### <a name="use-multiple-language-worker-processes"></a>여러 언어 작업자 프로세스 사용

기본적으로 모든 Functions 호스트 인스턴스에는 언어 작업자 프로세스가 하나만 있습니다. [FUNCTIONS_WORKER_PROCESS_COUNT](functions-app-settings.md#functions_worker_process_count) 애플리케이션 설정을 사용하여 호스트당 작업자 프로세스 수를 10개까지 늘릴 수 있습니다. 그러면 Azure Functions는 이러한 작업자 사이에 동시 함수 호출을 균등하게 분산하려고 시도합니다.

CPU 바인딩된 응용 프로그램의 경우 언어 작업자 수를 함수 앱 당 사용 가능한 코어 수와 동일 하거나 그 보다 높게 설정 해야 합니다. 자세히 알아보려면 [사용 가능한 인스턴스 sku](functions-premium-plan.md#available-instance-skus)를 참조 하세요. 

I/o 바운드 앱은 사용 가능한 코어 수를 초과 하 여 작업자 프로세스 수를 늘릴 수도 있습니다. 작업자 수를 너무 높게 설정 하면 필요한 컨텍스트 전환 횟수가 증가 하 여 전반적인 성능에 영향을 줄 수 있습니다. 

요구 사항을 충족하기 위해 애플리케이션을 스케일 아웃할 때 Functions가 만드는 각 호스트에 FUNCTIONS_WORKER_PROCESS_COUNT가 적용됩니다.

#### <a name="set-up-max-workers-within-a-language-worker-process"></a>언어 작업자 프로세스 내에서 최대 작업자를 설정 합니다.

비동기 [섹션](#understanding-async-in-python-worker)에서 설명한 것 처럼 Python 언어 작업자는 함수와 [코 루틴](https://docs.python.org/3/library/asyncio-task.html#coroutines) 을 다르게 처리 합니다. 코 루틴는 언어 worker가 실행 되는 것과 동일한 이벤트 루프 내에서 실행 됩니다. 반면에 함수 호출은 [ThreadPoolExecutor](https://docs.python.org/3/library/concurrent.futures.html#threadpoolexecutor)내에서 실행 되며,이는 언어 작업자에 의해 유지 관리 됩니다.

[PYTHON_THREADPOOL_THREAD_COUNT](functions-app-settings.md#python_threadpool_thread_count) 응용 프로그램 설정을 사용 하 여 동기화 기능 실행에 허용 되는 최대 작업자의 값을 설정할 수 있습니다. 이 값은 `max_worker` Python에서 최대 스레드 풀을 사용 `max_worker` 하 여 비동기적으로 호출을 실행할 수 있도록 하는 ThreadPoolExecutor 개체의 인수를 설정 합니다. 는 `PYTHON_THREADPOOL_THREAD_COUNT` 함수 호스트가 만드는 각 작업자에 적용 되며, Python은 새 스레드를 만들거나 기존 유휴 스레드를 다시 사용할 시기를 결정 합니다. 이전 Python 버전 (즉,, `3.8` `3.7` 및)의 경우 `3.6` `max_worker` 값이 1로 설정 됩니다. Python 버전의 `3.9` 경우 `max_worker` 은로 설정 됩니다 `None` .

CPU 바인딩된 응용 프로그램의 경우, 1부터 시작 하 여 워크 로드를 시험해 볼 때 보다 높은 값으로 설정 된 상태를 유지 해야 합니다. 이러한 제안 사항은 컨텍스트 전환에 소요 되는 시간을 줄이고 CPU 바인딩된 작업을 완료 하도록 허용 하는 것입니다.

I/o 바인딩된 응용 프로그램의 경우 각 호출에서 작업 하는 스레드 수를 늘려서 상당한 이득을 확인할 수 있습니다. Python 기본값 (코어 수 + 4)으로 시작 하 고 표시 되는 처리량 값에 따라 조정 하는 것이 좋습니다.

혼합 워크 로드 앱의 경우 `FUNCTIONS_WORKER_PROCESS_COUNT` `PYTHON_THREADPOOL_THREAD_COUNT` 처리량을 최대화 하기 위해 및 구성의 균형을 조정 해야 합니다. 함수 앱이 가장 많은 시간을 소비 하는 것을 이해 하려면 프로필을 프로 파일링 하 고 표시 되는 동작에 따라 값을 설정 하는 것이 좋습니다. 또한 FUNCTIONS_WORKER_PROCESS_COUNT 응용 프로그램 설정에 대해 알아보려면이 [섹션](#use-multiple-language-worker-processes) 을 참조 하세요.

> [!NOTE]
>  이러한 권장 사항은 HTTP 및 HTTP가 아닌 함수 모두에 적용 되기는 하지만 HTTP로 트리거되는 함수에 대 한 다른 트리거 관련 구성을 조정 해야 함수 앱에서 예상 되는 성능을 얻을 수 있습니다. 이에 대 한 자세한 내용은이 [문서](functions-best-practices.md)를 참조 하세요.


#### <a name="managing-event-loop"></a>이벤트 루프 관리

Asyncio 호환 가능한 타사 라이브러리를 사용 해야 합니다. 사용자의 요구를 충족 하는 타사 라이브러리가 없으면 Azure Functions에서 이벤트 루프를 관리할 수도 있습니다. 이벤트 루프 관리를 통해 계산 리소스 관리의 유연성을 높일 수 있으며 동기 i/o 라이브러리를 코 루틴로 래핑할 수도 있습니다.

기본 제공 **asyncio** 라이브러리를 사용 하 여 [코 루틴 및 작업](https://docs.python.org/3/library/asyncio-task.html) 및 [이벤트 루프](https://docs.python.org/3.8/library/asyncio-eventloop.html) 를 설명 하는 많은 유용한 Python 공식 문서를 제공 합니다.

다음 [요청](https://github.com/psf/requests) 라이브러리를 예로 들어 보겠습니다 .이 코드 조각은 **asyncio** 라이브러리를 사용 하 여 메서드를 코 루틴로 래핑하고 `requests.get()` 여러 웹 요청을 동시 SAMPLE_URL에 실행 합니다.


```python
import asyncio
import json
import logging

import azure.functions as func
from time import time
from requests import get, Response


async def invoke_get_request(eventloop: asyncio.AbstractEventLoop) -> Response:
    # Wrap requests.get function into a coroutine
    single_result = await eventloop.run_in_executor(
        None,  # using the default executor
        get,  # each task call invoke_get_request
        'SAMPLE_URL'  # the url to be passed into the requests.get function
    )
    return single_result

async def main(req: func.HttpRequest) -> func.HttpResponse:
    logging.info('Python HTTP trigger function processed a request.')

    eventloop = asyncio.get_event_loop()

    # Create 10 tasks for requests.get synchronous call
    tasks = [
        asyncio.create_task(
            invoke_get_request(eventloop)
        ) for _ in range(10)
    ]

    done_tasks, _ = await asyncio.wait(tasks)
    status_codes = [d.result().status_code for d in done_tasks]

    return func.HttpResponse(body=json.dumps(status_codes),
                             mimetype='application/json')
```
#### <a name="vertical-scaling"></a>수직 크기 조정
특히 CPU 바인딩된 작업에서 더 많은 처리 단위를 사용 하려면 더 높은 사양을 사용 하 여 프리미엄 요금제로 업그레이드 하면 됩니다. 처리 단위가 높으면 사용 가능한 코어 수에 따라 작업자 프로세스 수를 조정 하 고 더 높은 병렬 처리 수준을 달성할 수 있습니다. 

## <a name="next-steps"></a>다음 단계

Python 개발을 Azure Functions 하는 방법에 대 한 자세한 내용은 다음 리소스를 참조 하세요.

* [Azure Functions Python 개발자 가이드](functions-reference-python.md)
* [Azure Functions에 대한 모범 사례](functions-best-practices.md)
* [Azure Functions 개발자 참조](functions-reference.md)

