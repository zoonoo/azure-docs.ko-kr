---
title: Azure Functions에서 Python 앱의 처리량 성능 향상
description: Python을 사용해, 부하가 걸린 상태에서도 높은 성능을 발휘하고 잘 스케일링하는 Azure Functions 앱을 개발하는 방법을 알아봅니다.
ms.topic: article
ms.date: 10/13/2020
ms.custom: devx-track-python
ms.openlocfilehash: e3bbdb8819062d45d071633e0208fb58a003da54
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98786109"
---
# <a name="improve-throughput-performance-of-python-apps-in-azure-functions"></a>Azure Functions에서 Python 앱의 처리량 성능 향상

Python을 사용하여 Azure Functions용으로 개발한다면, 함수가 성능을 발휘하는 방법과 그 성능이 함수 앱의 스케일링하는 방법에 어떤 영향을 끼치는지를 이해해야 합니다. 고성능 앱을 디자인할 때는 이 요구 사항이 더 중요합니다. 함수 앱을 디자인, 작성 및 구성할 때 고려해야 할 주요 요소는 수평적 크기 조정 및 처리량 성능 구성입니다.

## <a name="horizontal-scaling"></a>수평 크기 조정
기본적으로 Azure Functions는 애플리케이션의 부하를 자동으로 모니터링하면서 필요에 따라 Python에 사용할 추가 호스트 인스턴스를 만듭니다. Azure Functions는 QueueTrigger에 대한 메시지 보존 기간, 큐 크기 등의 기본 제공 임계값을 여러 트리거 유형에 사용하여 인스턴스를 추가할 시기를 결정합니다. 이러한 임계값은 사용자가 구성할 수 없습니다. 자세한 내용은 [Azure Functions 이벤트 기반 크기 조정](event-driven-scaling.md)을 참조하세요.

## <a name="improving-throughput-performance"></a>처리량 성능 향상

기본 구성은 대부분의 Azure Functions 응용 프로그램에 적합합니다. 그러나 워크로드 프로필을 기반으로 구성을 사용하여 응용 프로그램 처리량 성능을 향상시킬 수 있습니다. 첫 번째 단계는 실행 중인 작업의 유형을 이해하는 것입니다.

| 워크로드 유형 | 함수 앱 특징       | 예                                          |
| ------------- | ---------------------------------- | ------------------------------------------------- |
| **I/O 바인딩**     | • 앱에서 여러 개의 동시 호출을 처리해야 합니다.<br>• 앱은 네트워크 호출 및 디스크 읽기/쓰기와 같은 많은 I/O 이벤트를 처리합니다. | • 웹 API                                          |
| **CPU 바인딩**     | • 앱은 이미지 크기 조정과 같은 장기 실행 계산을 수행합니다.<br>• 앱은 데이터를 변환합니다.                                                | • 데이터 처리<br>• 기계 학습 유추<br> |

 
실제 함수 워크로드는 일반적으로 I/O와 CPU 바인딩이 혼합되어 있기 때문에 실제 프로덕션 부하 환경에서 앱을 프로파일링해야 합니다.


### <a name="performance-specific-configurations"></a>성능별 구성

함수 앱의 워크로드 프로필을 이해한 후 함수의 처리량 성능을 향상시키는 데 사용할 수 있는 구성은 다음과 같습니다.

* [Async](#async)
* [다중 언어 작업자](#use-multiple-language-worker-processes)
* [언어 작업자 프로세스 내의 최대 작업자 수](#set-up-max-workers-within-a-language-worker-process)
* [이벤트 루프](#managing-event-loop)
* [수직 크기 조정](#vertical-scaling)



#### <a name="async"></a>Async

[Python은 단일 스레드 런타임이므로](https://wiki.python.org/moin/GlobalInterpreterLock), Python에 대한 호스트 인스턴스는 기본적으로 한 번에 하나의 함수 호출만 처리할 수 있습니다. 대량의 I/O 이벤트를 처리하고(또는 처리하거나) I/O 바인딩된 응용 프로그램은 함수를 비동기적으로 실행하여 성능을 향상할 수 있습니다.

함수를 비동기적으로 실행하려면 [asyncio](https://docs.python.org/3/library/asyncio.html)를 사용하여 함수를 직접 실행하는 `async def` 문을 사용하세요.

```python
async def main():
    await some_nonblocking_socket_io_op()
```
다음은 [aiohttp](https://pypi.org/project/aiohttp/) http 클라이언트를 사용하는 HTTP 트리거가 있는 함수의 예입니다.

```python
import aiohttp

import azure.functions as func

async def main(req: func.HttpRequest) -> func.HttpResponse:
    async with aiohttp.ClientSession() as client:
        async with client.get("PUT_YOUR_URL_HERE") as response:
            return func.HttpResponse(await response.text())

    return func.HttpResponse(body='NotFound', status_code=404)
```


`async` 키워드가 없는 함수는 ThreadPoolExecutor 스레드 풀에서 자동으로 실행됩니다.

```python
# Runs in an ThreadPoolExecutor threadpool. Number of threads is defined by PYTHON_THREADPOOL_THREAD_COUNT. 
# The example is intended to show how default synchronous function are handled.

def main():
    some_blocking_socket_io()
```

함수를 비동기적으로 실행하는 이점을 전부 얻으려면 코드에 사용되는 I/O 작업/라이브러리도 비동기적으로 구현해야 합니다. 비동기로 정의된 함수에서 동기 I/O 작업을 사용하면 전체 성능이 **저하할** 수 있습니다. 사용 중인 라이브러리에 비동기 버전이 구현되지 않은 경우에도 앱에서 [이벤트 루프를 관리하여](#managing-event-loop) 코드를 비동기적으로 실행하는 것이 도움이 될 수 있습니다. 

다음은 비동기 패턴을 구현한 클라이언트 라이브러리의 몇 가지 예입니다.
- [aiohttp](https://pypi.org/project/aiohttp/) - asyncio용 Http 클라이언트/서버 
- [스트림 API](https://docs.python.org/3/library/asyncio-stream.html) - 네트워크 연결 작업을 위한 상위 수준 async/await-ready 기본형
- [Janus 큐](https://pypi.org/project/janus/) - Python용 Thread-safe asyncio-aware 큐
- [pyzmq](https://pypi.org/project/pyzmq/) - ZeroMQ용 Python 바인딩
 
##### <a name="understanding-async-in-python-worker"></a>Python 작업자의 비동기 이해

함수 시그니처 앞에서 `async`를 정의하면 Python은 함수를 코루틴으로 표시합니다. 코루틴을 호출할 때 이벤트 루프에 작업으로 예약할 수 있습니다. 비동기 함수에서 `await`를 호출하면 이벤트 루프에 연속을 등록하고 이벤트 루프가 대기 시간 동안 다음 작업을 처리하도록 허용합니다.

Python 작업자에서 작업자가 이벤트 루프를 고객의 `async` 기능과 공유하고 여러 요청을 동시에 처리할 수 있습니다. 고객은 asyncio 호환 라이브러리를 사용하는 것이 좋습니다(예: [aiohttp](https://pypi.org/project/aiohttp/), [pyzmq](https://pypi.org/project/pyzmq/)). 이러한 권장 사항을 적용하면 동기 방식으로 구현된 라이브러리에 비해 함수 처리량이 크게 증가합니다.

> [!NOTE]
>  함수를 구현 내에서 `async` 로 구현 내에 `await` 가 없는 상태에서 선언하면 Python 작업자가 동시 요청을 처리할 수 없어서 이벤트 루프가 차단되므로 함수 성능이 크게 영향을 받을 수 있습니다.

#### <a name="use-multiple-language-worker-processes"></a>여러 언어 작업자 프로세스 사용

기본적으로 모든 Functions 호스트 인스턴스에는 언어 작업자 프로세스가 하나만 있습니다. [FUNCTIONS_WORKER_PROCESS_COUNT](functions-app-settings.md#functions_worker_process_count) 애플리케이션 설정을 사용하여 호스트당 작업자 프로세스 수를 10개까지 늘릴 수 있습니다. 그러면 Azure Functions는 이러한 작업자 사이에 동시 함수 호출을 균등하게 분산하려고 시도합니다.

CPU 바인딩된 앱의 경우 언어 작업자 수를 함수 앱 당 사용 가능한 코어 수와 동일하거나 그보다 높게 설정해야 합니다. 자세히 알아보려면 [사용 가능한 인스턴스 SKU](functions-premium-plan.md#available-instance-skus)를 참조하세요. 

I/O 바인딩 앱은 사용 가능한 코어 수를 초과하여 작업자 프로세스 수를 늘릴 수도 있습니다. 작업자 수를 너무 높게 설정하면 필요한 컨텍스트 전환 횟수가 증가하여 전반적인 성능에 영향을 줄 수 있습니다. 

요구 사항을 충족하기 위해 애플리케이션을 스케일 아웃할 때 Functions가 만드는 각 호스트에 FUNCTIONS_WORKER_PROCESS_COUNT가 적용됩니다.

#### <a name="set-up-max-workers-within-a-language-worker-process"></a>언어 작업자 프로세스 내에서 최대 작업자 설정

비동기 [섹션](#understanding-async-in-python-worker)에서 설명한 것처럼 Python 언어 작업자는 함수와 [코루틴](https://docs.python.org/3/library/asyncio-task.html#coroutines)을 다르게 처리합니다. 코루틴은 언어 작업자가 실행되는 것과 동일한 이벤트 루프 내에서 실행됩니다. 반면에 함수 호출은 [ThreadPoolExecutor](https://docs.python.org/3/library/concurrent.futures.html#threadpoolexecutor)에서 실행되며, 이는 언어 작업자에 의해 유지 관리됩니다.

[PYTHON_THREADPOOL_THREAD_COUNT](functions-app-settings.md#python_threadpool_thread_count) 응용 프로그램 설정을 사용하여 동기화 기능 실행에 허용되는 최대 작업자의 값을 설정할 수 있습니다. 이 값은 ThreadPoolExecutor 개체의 `max_worker` 인수를 설정하는데, Python이 `max_worker` 스레드의 최대 풀을 사용하여 호출을 비동기적으로 실행할 수 있게 합니다. `PYTHON_THREADPOOL_THREAD_COUNT`는 함수 호스트가 만드는 각 작업자에 적용 되며, Python은 새 스레드를 만들거나 기존 유휴 스레드를 다시 사용할 시기를 결정합니다. 이전 Python 버전(즉, `3.8`, `3.7`, `3.6`)의 경우 `max_worker` 값은 1로 설정됩니다. Python 버전 `3.9`의 경우 `max_worker`는 `None`으로 설정됩니다.

CPU 바인딩된 앱은 경우 워크로드를 실험할 때 1부터 시작하여 증가하는 낮은 숫자로 설정을 유지해야 합니다. 이 제안은 컨텍스트 전환에 소요되는 시간을 줄이고 CPU 바인딩된 작업이 완료되도록 합니다.

I/O 바인딩된 앱은 각 호출에서 작업하는 스레드 수를 늘려 상당한 향상을 볼 수 있습니다. 권장 사항은 Python 기본값(코어 수 + 4)으로 시작한 다음, 표시되는 처리량 값에 따라 조정하는 것입니다.

혼합 워크로드 앱의 경우 `FUNCTIONS_WORKER_PROCESS_COUNT` 및 `PYTHON_THREADPOOL_THREAD_COUNT` 구성의 균형을 조정하여 처리량을 최대화해야 합니다. 함수 앱이 가장 많은 시간을 소비하는 부분을 이해하려면 함수 앱을 프로파일링하고 현재 수행하는 작업에 따라 값을 설정하는 것이 좋습니다. 또한 이 [섹션을](#use-multiple-language-worker-processes) 참조하여 FUNCTIONS_WORKER_PROCESS_COUNT 응용 프로그램 설정에 대해 알아보세요.

> [!NOTE]
>  이러한 권장 사항은 HTTP 트리거 함수와 HTTP가 아닌 함수 모두에 적용되지만, HTTP가 아닌 트리거 함수에 대한 다른 트리거 특정 구성을 조정하여 함수 앱에서 예상되는 성능을 얻어야 할 수도 있습니다. 자세한 내용은 이 [문서](functions-best-practices.md)를 참조하세요.


#### <a name="managing-event-loop"></a>이벤트 루프 관리

asyncio 호환 타사 라이브러리를 사용해야 합니다. 요구 사항을 충족하는 타사 라이브러리가 없는 경우 Azure Functions의 이벤트 루프를 관리할 수도 있습니다. 이벤트 루프를 관리하면 컴퓨팅 리소스 관리에 더 많은 유연성을 제공하고 동기 I/O 라이브러리를 코루틴으로 래핑할 수도 있습니다.

많은 유용한 Python 공식 설명서가 [코루틴 및 작업](https://docs.python.org/3/library/asyncio-task.html) 과 [이벤트 루프](https://docs.python.org/3.8/library/asyncio-eventloop.html) 에 대해 기본 **asyncio** 라이브러리를 사용하여 설명하고 있습니다.

다음 [요청](https://github.com/psf/requests) 라이브러리를 예로 들어 보겠습니다. 이 코드 조각은 **asyncio** 라이브러리를 사용하여 `requests.get()` 메서드를 코루틴으로 래핑하고 동시에 SAMPLE_URL에 여러 웹 요청을 실행합니다.


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
특히 CPU 바인딩된 작업에서 더 많은 처리 단위를 사용하려면 더 높은 사양을 사용하여 프리미엄 플랜으로 업그레이드 하면 됩니다. 프로세싱 단위가 높으면 사용 가능한 코어 수에 따라 작업자 프로세스 수를 조정하고 더 높은 병렬 처리 수준을 달성할 수 있습니다. 

## <a name="next-steps"></a>다음 단계

Azure Functions Python 개발에 대한 자세한 내용은 다음 리소스를 참조하세요.

* [Azure Functions Python 개발자 가이드](functions-reference-python.md)
* [Azure Functions에 대한 모범 사례](functions-best-practices.md)
* [Azure Functions 개발자 참조](functions-reference.md)

