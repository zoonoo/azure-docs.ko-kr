---
title: Azure Functions에 대한 Python 개발자 참조
description: Python으로 함수를 개발하는 방법 이해
services: functions
documentationcenter: na
author: ggailey777
manager: cfowler
keywords: Azure Functions, 함수, 이벤트 처리, 동적 계산, 서버리스 아키텍처, Python
ms.service: azure-functions
ms.devlang: python
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 04/16/2018
ms.author: glenga
ms.openlocfilehash: ec42693fe42f35d728a4a5018776867f07403f81
ms.sourcegitcommit: 920ad23613a9504212aac2bfbd24a7c3de15d549
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/15/2019
ms.locfileid: "68226869"
---
# <a name="azure-functions-python-developer-guide"></a>Azure Functions Python 개발자 가이드

이 문서에서는 Python을 사용하여 Azure Functions를 개발하는 방법을 소개합니다. 아래 내용은 [Azure Functions 개발자 가이드](functions-reference.md)를 이미 읽었다고 가정합니다.

[!INCLUDE [functions-python-preview-note](../../includes/functions-python-preview-note.md)]

## <a name="programming-model"></a>프로그래밍 모델

Azure 함수는 입력을 처리하고 출력을 생성하는 Python 스크립트의 상태 비저장 메서드여야 합니다. 기본적으로 런타임은 메서드가 `main()` `__init__.py` 파일에서 호출 되는 전역 메서드로 구현 될 것으로 예상 합니다.

`scriptFile` *Json* 파일에서 및 `entryPoint` 속성을 지정 하 여 기본 구성을 변경할 수 있습니다. 예를 들어 아래에 있는 _main.py_ 파일의 `customentry()` 메서드를 Azure function의 진입점으로 사용 하도록 런타임에 지시 합니다 _._

```json
{
  "scriptFile": "main.py",
  "entryPoint": "customentry",
  ...
}
```

트리거와 바인딩의 데이터는 `name` *함수 json* 파일에 정의 된 속성을 사용 하 여 메서드 특성을 통해 함수에 바인딩됩니다. 예를 들어, 아래 _함수인 json_ 은 라는 `req`HTTP 요청에 의해 트리거되는 간단한 함수를 설명 합니다.

```json
{
  "bindings": [
    {
      "name": "req",
      "direction": "in",
      "type": "httpTrigger",
      "authLevel": "anonymous"
    },
    {
      "name": "$return",
      "direction": "out",
      "type": "http"
    }
  ]
}
```

`__init__.py` 파일에는 다음 함수 코드가 포함되어 있습니다.

```python
def main(req):
    user = req.params.get('user')
    return f'Hello, {user}!'
```

필요에 따라 코드 편집기에서 제공 하는 intellisense 및 자동 완성 기능을 활용 하기 위해 Python 형식 주석을 사용 하 여 함수에서 특성 형식 및 반환 형식을 선언할 수도 있습니다. 

```python
import azure.functions


def main(req: azure.functions.HttpRequest) -> str:
    user = req.params.get('user')
    return f'Hello, {user}!'
```

[azure.functions.*](/python/api/azure-functions/azure.functions?view=azure-python) 패키지에 포함된 Python 주석을 사용하여 입력 및 출력을 메서드에 바인딩합니다.

## <a name="folder-structure"></a>폴더 구조

Python Functions 프로젝트의 폴더 구조는 다음과 같습니다.

```
 FunctionApp
 | - MyFirstFunction
 | | - __init__.py
 | | - function.json
 | - MySecondFunction
 | | - __init__.py
 | | - function.json
 | - SharedCode
 | | - myFirstHelperFunction.py
 | | - mySecondHelperFunction.py
 | - host.json
 | - requirements.txt
```

함수 앱을 구성하는 데 사용할 수 있는 공유 [host.json](functions-host-json.md) 파일이 있습니다. 각 함수에는 자체 코드 파일과 바인딩 구성 파일(function.json)이 있습니다. 

공유 코드는 별도의 폴더에 보관해야 합니다. SharedCode 폴더의 모듈을 참조하기 위해 다음 구문을 사용할 수 있습니다.

```
from __app__.SharedCode import myFirstHelperFunction
```

Azure의 함수 앱에 함수 프로젝트를 배포할 때 *Functionapp* 폴더의 전체 콘텐츠는 패키지에 포함 되어야 하지만 폴더 자체에는 포함 되지 않아야 합니다.

## <a name="triggers-and-inputs"></a>트리거 및 입력

입력은 Azure Functions에서 트리거 입력과 추가 입력의 두 가지 범주로 나뉩니다. `function.json` 파일에는 차이가 있지만 사용은 Python 코드에서 동일 합니다.  트리거 및 입력 소스에 대 한 연결 문자열 또는 암호는 로컬로 실행할 `local.settings.json` 때 파일의 값에 매핑되고, Azure에서 실행할 때 응용 프로그램 설정에 매핑됩니다. 

예를 들어 다음 코드는 두 가지 간의 차이점을 보여 줍니다.

```json
// function.json
{
  "scriptFile": "__init__.py",
  "bindings": [
    {
      "name": "req",
      "direction": "in",
      "type": "httpTrigger",
      "authLevel": "anonymous",
      "route": "items/{id}"
    },
    {
      "name": "obj",
      "direction": "in",
      "type": "blob",
      "path": "samples/{id}",
      "connection": "AzureWebJobsStorage"
    }
  ]
}
```

```json
// local.settings.json
{
  "IsEncrypted": false,
  "Values": {
    "FUNCTIONS_WORKER_RUNTIME": "python",
    "AzureWebJobsStorage": "<azure-storage-connection-string>"
  }
}
```

```python
# __init__.py
import azure.functions as func
import logging


def main(req: func.HttpRequest,
         obj: func.InputStream):

    logging.info(f'Python HTTP triggered function processed: {obj.read()}')
```

함수가 호출되면 HTTP 요청이 `req`로 함수에 전달됩니다. 항목은 경로 URL의 _ID_ 를 기반으로 Azure Blob Storage에서 검색 되 고 함수 본문에서로 `obj` 사용할 수 있게 됩니다.  여기서 지정 된 저장소 계정은 함수 앱에서 사용 하는 `AzureWebJobsStorage` 것과 동일한 저장소 계정으로 검색 된 연결 문자열입니다.


## <a name="outputs"></a>출력

출력은 반환 값 및 출력 매개 변수 둘 다로 표현될 수 있습니다. 출력이 하나만 있는 경우 반환 값을 사용하는 것이 좋습니다. 다중 출력의 경우 출력 매개 변수를 사용해야 합니다.

함수의 반환 값을 출력 바인딩의 값으로 사용하려면 바인딩의 `name` 속성을 `function.json`의 `$return`으로 설정해야 합니다.

여러 출력을 생성 하려면 `set()` [`azure.functions.Out`](/python/api/azure-functions/azure.functions.out?view=azure-python) 인터페이스에서 제공 하는 메서드를 사용 하 여 값을 바인딩에 할당 합니다. 예를 들어 다음 함수는 메시지를 큐로 푸시하고 HTTP 응답도 반환할 수 있습니다.

```json
{
  "scriptFile": "__init__.py",
  "bindings": [
    {
      "name": "req",
      "direction": "in",
      "type": "httpTrigger",
      "authLevel": "anonymous"
    },
    {
      "name": "msg",
      "direction": "out",
      "type": "queue",
      "queueName": "outqueue",
      "connection": "AzureWebJobsStorage"
    },
    {
      "name": "$return",
      "direction": "out",
      "type": "http"
    }
  ]
}
```

```python
import azure.functions as func


def main(req: func.HttpRequest,
         msg: func.Out[func.QueueMessage]) -> str:

    message = req.params.get('body')
    msg.set(message)
    return message
```

## <a name="logging"></a>로깅

함수 앱의 루트 [`logging`](https://docs.python.org/3/library/logging.html#module-logging) 처리기를 통해 Azure Functions 런타임 로거에 액세스할 수 있습니다. 이 로거는 Application Insights에 연결되어 있으며, 함수를 실행하는 동안 발생하는 경고 및 오류에 플래그를 지정할 수 있도록 합니다.

다음 예제는 HTTP 트리거를 통해 함수가 호출될 때 정보 메시지를 기록합니다.

```python
import logging


def main(req):
    logging.info('Python HTTP trigger function processed a request.')
```

다양한 추적 수준에서 콘솔에 쓸 수 있는 추가 로깅 메서드가 제공됩니다.

| 메서드                 | Description                                |
| ---------------------- | ------------------------------------------ |
| logging.**critical(_message_)**   | 루트 로거에 위험 수준의 메시지를 기록합니다.  |
| logging.**error(_message_)**   | 루트 로거에 오류 수준의 메시지를 기록합니다.    |
| logging.**warning(_message_)**    | 루트 로거에 경고 수준의 메시지를 기록합니다.  |
| logging.**info(_message_)**    | 루트 로거에 정보 수준의 메시지를 기록합니다.  |
| logging.**debug(_message_)** | 루트 로거에 디버그 수준의 메시지를 기록합니다.  |

## <a name="async"></a>Async

`async def` 문을 사용 하 여 Azure 함수를 비동기 코 루틴 작성 하는 것이 좋습니다.

```python
# Will be run with asyncio directly


async def main():
    await some_nonblocking_socket_io_op()
```

Main () 함수가 동기 (한정자 없음 `async` ) 이면 `asyncio` 스레드 풀에서 함수를 자동으로 실행 합니다.

```python
# Would be run in an asyncio thread-pool


def main():
    some_blocking_socket_io()
```

## <a name="context"></a>컨텍스트

실행 중에 함수의 호출 컨텍스트를 가져오려면 해당 시그니처에 [`context`](/python/api/azure-functions/azure.functions.context?view=azure-python) 인수를 포함 합니다. 

예를 들어:

```python
import azure.functions


def main(req: azure.functions.HttpRequest,
         context: azure.functions.Context) -> str:
    return f'{context.invocation_id}'
```

[**Context**](/python/api/azure-functions/azure.functions.context?view=azure-python) 클래스에는 다음과 같은 메서드가 있습니다.

`function_directory`  
함수가 실행되는 디렉터리입니다.

`function_name`  
함수의 이름입니다.

`invocation_id`  
현재 함수 호출의 ID입니다.

## <a name="global-variables"></a>전역 변수

나중에 실행 하기 위해 앱의 상태를 유지 하는 것은 보장 되지 않습니다. 그러나 Azure Functions 런타임은 동일한 앱을 여러 번 실행 하는 경우에도 동일한 프로세스를 다시 사용할 수도 있습니다. 비용이 많이 드는 계산 결과를 캐시 하려면 전역 변수로 선언 합니다. 

```python
CACHED_DATA = None


def main(req):
    global CACHED_DATA
    if CACHED_DATA is None:
        CACHED_DATA = load_json()

    # ... use CACHED_DATA in code
```

## <a name="python-version-and-package-management"></a>Python 버전 및 패키지 관리

현재 Azure Functions는 Python3.6.x(공식 CPython 배포)만 지원합니다.

Azure Functions Core Tools 또는 Visual Studio Code를 사용하여 로컬에서 개발하는 경우 필요한 패키지의 이름과 버전을 `requirements.txt` 파일에 추가하고 `pip`를 사용하여 설치합니다.

예를 들어 다음 요구 사항 파일 및 pip 명령을 사용하여 PyPI에서 `requests` 패키지를 설치할 수 있습니다.

```txt
requests==2.19.1
```

```bash
pip install -r requirements.txt
```

## <a name="publishing-to-azure"></a>Azure에 게시

게시할 준비가 되 면 모든 종속성이 프로젝트 디렉터리의 루트에 있는 *요구 사항 .txt* 파일에 나열 되어 있는지 확인 합니다. 컴파일러가 필요하고 PyPI에서 manylinux 호환 휠의 설치를 지원하지 않는 패키지를 사용하는 경우 Azure에 게시하면 다음 오류가 발생하고 실패합니다. 

```
There was an error restoring dependencies.ERROR: cannot install <package name - version> dependency: binary dependencies without wheels are not supported.  
The terminal process terminated with exit code: 1
```

필요한 이진을 자동으로 빌드하고 구성하려면 로컬 머신에 [Docker를 설치](https://docs.docker.com/install/)하고 다음 명령을 실행하여 [Azure Functions Core Tools](functions-run-local.md#v2)(func)를 통해 게시합니다. `<app name>`을 Azure의 함수 앱 이름으로 바꾸어야 합니다. 

```bash
func azure functionapp publish <app name> --build-native-deps
```

이면에서 Core Tools는 docker를 사용하여 [mcr.microsoft.com/azure-functions/python](https://hub.docker.com/r/microsoft/azure-functions/) 이미지를 로컬 머신의 컨테이너로 실행합니다. 이 환경을 사용하여, Azure에 최종 배포하기 위해 패키징하기 전에 원본 배포에서 필요한 모듈을 빌드하고 설치합니다.

종속성을 빌드하고 CD (지속적인 업데이트) 시스템을 사용 하 여 게시 하려면 [Azure DevOps 파이프라인을 사용](functions-how-to-azure-devops.md)합니다. 

## <a name="unit-testing"></a>유닛 테스트

Python으로 작성 된 함수는 표준 테스트 프레임 워크를 사용 하 여 다른 Python 코드와 같이 테스트할 수 있습니다. 대부분의 바인딩에서 `azure.functions` 패키지에서 적절 한 클래스의 인스턴스를 만들어 모의 입력 개체를 만들 수 있습니다. 패키지를 즉시 사용할 수 없으므로 위의 [Python 버전 및 패키지 관리](#python-version-and-package-management) 섹션에 설명 `requirements.txt` 된 대로 파일을 통해 설치 해야 합니다. [`azure.functions`](https://pypi.org/project/azure-functions/)

예를 들어 다음은 HTTP 트리거 함수의 모의 테스트입니다.

```json
{
  "scriptFile": "httpfunc.py",
  "entryPoint": "my_function",
  "bindings": [
    {
      "authLevel": "function",
      "type": "httpTrigger",
      "direction": "in",
      "name": "req",
      "methods": [
        "get",
        "post"
      ]
    },
    {
      "type": "http",
      "direction": "out",
      "name": "$return"
    }
  ]
}
```

```python
# myapp/httpfunc.py
import azure.functions as func
import logging

def my_function(req: func.HttpRequest) -> func.HttpResponse:
    logging.info('Python HTTP trigger function processed a request.')

    name = req.params.get('name')
    if not name:
        try:
            req_body = req.get_json()
        except ValueError:
            pass
        else:
            name = req_body.get('name')

    if name:
        return func.HttpResponse(f"Hello {name}")
    else:
        return func.HttpResponse(
             "Please pass a name on the query string or in the request body",
             status_code=400
        )
```

```python
# myapp/test_httpfunc.py
import unittest

import azure.functions as func
from httpfunc import my_function


class TestFunction(unittest.TestCase):
    def test_my_function(self):
        # Construct a mock HTTP request.
        req = func.HttpRequest(
            method='GET',
            body=None,
            url='/api/HttpTrigger',
            params={'name': 'Test'})

        # Call the function.
        resp = my_function(req)

        # Check the output.
        self.assertEqual(
            resp.get_body(),
            b'Hello Test',
        )
```

큐 트리거 함수를 사용 하는 또 다른 예는 다음과 같습니다.

```python
# myapp/__init__.py
import azure.functions as func


def my_function(msg: func.QueueMessage) -> str:
    return f'msg body: {msg.get_body().decode()}'
```

```python
# myapp/test_func.py
import unittest

import azure.functions as func
from . import my_function


class TestFunction(unittest.TestCase):
    def test_my_function(self):
        # Construct a mock Queue message.
        req = func.QueueMessage(
            body=b'test')

        # Call the function.
        resp = my_function(req)

        # Check the output.
        self.assertEqual(
            resp,
            'msg body: test',
        )
```

## <a name="known-issues-and-faq"></a>알려진 문제 및 FAQ

모든 알려진 문제 및 기능 요청은 [GitHub 문제](https://github.com/Azure/azure-functions-python-worker/issues) 목록을 사용하여 추적됩니다. 문제가 발생하여 GitHub에서 해당 문제를 찾을 수 없는 경우 새 문제를 열고 해당 문제에 대한 자세한 설명을 제공해 주세요.

## <a name="next-steps"></a>다음 단계

자세한 내용은 다음 리소스를 참조하십시오.

* [Azure Functions 패키지 API 설명서](/python/api/azure-functions/azure.functions?view=azure-python)
* [Azure Functions에 대한 모범 사례](functions-best-practices.md)
* [Azure Functions 트리거 및 바인딩](functions-triggers-bindings.md)
* [Blob Storage 바인딩](functions-bindings-storage-blob.md)
* [HTTP 및 Webhook 바인딩](functions-bindings-http-webhook.md)
* [Queue Storage 바인딩](functions-bindings-storage-queue.md)
* [타이머 트리거](functions-bindings-timer.md)
