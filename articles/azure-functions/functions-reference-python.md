---
title: Azure Functions에 대한 Python 개발자 참조
description: Python으로 함수를 개발하는 방법 이해
services: functions
documentationcenter: na
author: ggailey777
manager: cfowler
keywords: Azure Functions, 함수, 이벤트 처리, 동적 컴퓨팅, 서버리스 아키텍처, Python
ms.service: azure-functions
ms.devlang: python
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 04/16/2018
ms.author: glenga
ms.openlocfilehash: 4fd73f528ac823a8e794a880f87dd5f8872e1251
ms.sourcegitcommit: 824e3d971490b0272e06f2b8b3fe98bbf7bfcb7f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/10/2019
ms.locfileid: "72243269"
---
# <a name="azure-functions-python-developer-guide"></a>Azure Functions Python 개발자 가이드

이 문서에서는 Python을 사용하여 Azure Functions를 개발하는 방법을 소개합니다. 아래 내용은 [Azure Functions 개발자 가이드](functions-reference.md)를 이미 읽었다고 가정합니다. 

Python의 독립 실행형 함수 샘플 프로젝트에 대해서는 [Python 함수 샘플](/samples/browse/?products=azure-functions&languages=python)을 참조 하세요. 

## <a name="programming-model"></a>프로그래밍 모델

Azure Functions는 Python 스크립트에서 입력을 처리 하 고 출력을 생성 하는 상태 비저장 메서드가 될 것으로 예상 합니다. 기본적으로 런타임은 `__init__.py` 파일에서 `main()` 이라는 전역 메서드로 구현 될 것으로 예상 합니다. 또한 [대체 진입점을 지정할](#alternate-entry-point)수 있습니다.

트리거와 바인딩의 데이터는 *함수 json* 파일에 정의 된 `name` 속성을 사용 하 여 메서드 특성을 통해 함수에 바인딩됩니다. 예를 들어, 아래 _함수인 json_ 은 `req` 이라는 HTTP 요청에 의해 트리거되는 간단한 함수를 설명 합니다.

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

Python 형식 주석을 사용 하 여 함수에서 특성 형식 및 반환 형식을 명시적으로 선언할 수도 있습니다. 이를 통해 많은 Python 코드 편집기에서 제공 하는 intellisense 및 자동 완성 기능을 사용할 수 있습니다.

```python
import azure.functions


def main(req: azure.functions.HttpRequest) -> str:
    user = req.params.get('user')
    return f'Hello, {user}!'
```

[azure.functions.*](/python/api/azure-functions/azure.functions?view=azure-python) 패키지에 포함된 Python 주석을 사용하여 입력 및 출력을 메서드에 바인딩합니다.

## <a name="alternate-entry-point"></a>대체 진입점

필요에 따라 *함수 json* 파일에 `scriptFile` 및 `entryPoint` 속성을 지정 하 여 함수의 기본 동작을 변경할 수 있습니다. 예를 들어 아래 _main.py_ 파일의 `customentry()` 메서드를 Azure function의 진입점으로 사용 하도록 런타임에 지시 합니다 _._

```json
{
  "scriptFile": "main.py",
  "entryPoint": "customentry",
  "bindings": [
      ...
  ]
}
```

## <a name="folder-structure"></a>폴더 구조

Python 함수 프로젝트에 대 한 폴더 구조는 다음 예제와 같습니다.

```
 FunctionApp
 | - MyFirstFunction
 | | - __init__.py
 | | - function.json
 | | - example.py
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

함수에 대 한 로컬 모듈을 참조 하려면 다음과 같이 상대 가져오기 구문을 사용할 수 있습니다.

```
from . import example
```

Azure의 함수 앱에 함수 프로젝트를 배포할 때 *Functionapp* 폴더의 전체 콘텐츠는 패키지에 포함 되어야 하지만 폴더 자체에는 포함 되지 않아야 합니다.

## <a name="triggers-and-inputs"></a>트리거 및 입력

입력은 Azure Functions에서 트리거 입력과 추가 입력의 두 가지 범주로 나뉩니다. @No__t-0 파일에서는 다르지만 사용은 Python 코드에서 동일 합니다.  트리거 및 입력 소스에 대 한 연결 문자열 또는 암호는 로컬로 실행할 때 `local.settings.json` 파일의 값에 매핑되고, Azure에서 실행할 때 응용 프로그램 설정에 매핑됩니다. 

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

함수가 호출되면 HTTP 요청이 `req`로 함수에 전달됩니다. 항목은 경로 URL의 _ID_ 를 기반으로 Azure Blob Storage에서 검색 되 고 함수 본문에서 `obj`로 사용할 수 있게 됩니다.  여기서 지정 된 저장소 계정은 함수 앱에서 사용 하는 것과 동일한 저장소 계정인에 있는 연결 문자열입니다.


## <a name="outputs"></a>출력

출력은 반환 값 및 출력 매개 변수 둘 다로 표현될 수 있습니다. 출력이 하나만 있는 경우 반환 값을 사용하는 것이 좋습니다. 다중 출력의 경우 출력 매개 변수를 사용해야 합니다.

함수의 반환 값을 출력 바인딩의 값으로 사용하려면 바인딩의 `name` 속성을 `function.json`의 `$return`으로 설정해야 합니다.

여러 출력을 생성 하려면 [`azure.functions.Out`](/python/api/azure-functions/azure.functions.out?view=azure-python) 인터페이스에서 제공 하는 `set()` 메서드를 사용 하 여 값을 바인딩에 할당 합니다. 예를 들어 다음 함수는 메시지를 큐로 푸시하고 HTTP 응답도 반환할 수 있습니다.

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

| 메서드                 | 설명                                |
| ---------------------- | ------------------------------------------ |
| **`critical(_message_)`**   | 루트 로거에 위험 수준의 메시지를 기록합니다.  |
| **`error(_message_)`**   | 루트 로거에 오류 수준의 메시지를 기록합니다.    |
| **`warning(_message_)`**    | 루트 로거에 경고 수준의 메시지를 기록합니다.  |
| **`info(_message_)`**    | 루트 로거에 정보 수준의 메시지를 기록합니다.  |
| **`debug(_message_)`** | 루트 로거에 디버그 수준의 메시지를 기록합니다.  |

로깅에 대해 자세히 알아보려면 [Azure Functions 모니터링](functions-monitoring.md)을 참조 하세요.

## <a name="http-trigger-and-bindings"></a>HTTP 트리거 및 바인딩

HTTP 트리거는 함수인 jon file에 정의 되어 있습니다. 바인딩의 `name`은 함수의 명명 된 매개 변수와 일치 해야 합니다. 앞의 예제에서 바인딩 이름 `req`이 사용 됩니다. 이 매개 변수는 [HttpRequest] 개체 이며 [httpresponse.cache] 개체가 반환 됩니다.

[HttpRequest] 개체에서 요청 헤더, 쿼리 매개 변수, 경로 매개 변수 및 메시지 본문을 가져올 수 있습니다. 

다음 예제는 [Python의 HTTP 트리거 템플릿에서](https://github.com/Azure/azure-functions-templates/tree/dev/Functions.Templates/Templates/HttpTrigger-Python)가져온 것입니다. 

```python
def main(req: func.HttpRequest) -> func.HttpResponse:
    headers = {"my-http-header": "some-value"}

    name = req.params.get('name')
    if not name:
        try:
            req_body = req.get_json()
        except ValueError:
            pass
        else:
            name = req_body.get('name')
            
    if name:
        return func.HttpResponse(f"Hello {name}!", headers=headers)
    else:
        return func.HttpResponse(
             "Please pass a name on the query string or in the request body",
             headers=headers, status_code=400
        )
```

이 함수에서 `name` 쿼리 매개 변수의 값은 [HttpRequest] 개체의 `params` 매개 변수에서 가져옵니다. JSON으로 인코딩된 메시지 본문은 `get_json` 메서드를 사용 하 여 읽습니다. 

마찬가지로 반환 된 [httpresponse.cache] 개체의 응답 메시지에 대해 `status_code` 및 `headers`을 설정할 수 있습니다.

## <a name="concurrency"></a>동시성

기본적으로 Python runtime 함수는 한 번에 하나의 함수 호출만 처리할 수 있습니다. 다음 조건 중 하나 이상으로 인해이 동시성 수준이 충분 하지 않을 수 있습니다.

+ 동시에 수행 되는 여러 호출을 처리 하려고 합니다.
+ 많은 수의 i/o 이벤트를 처리 하 고 있습니다.
+ 응용 프로그램이 i/o 바인딩되어 있습니다.

이러한 경우에는를 비동기적으로 실행 하 고 여러 언어 작업자 프로세스를 사용 하 여 성능을 향상 시킬 수 있습니다.  

### <a name="async"></a>Async

@No__t-0 문을 사용 하 여 함수가 비동기 코 루틴 실행 되도록 하는 것이 좋습니다.

```python
# Runs with asyncio directly

async def main():
    await some_nonblocking_socket_io_op()
```

@No__t-0 함수가 동기화 된 경우 (`async` 한정자가 없는 경우) 함수는 @no__t 2 스레드 풀에서 자동으로 실행 됩니다.

```python
# Runs in an asyncio thread-pool

def main():
    some_blocking_socket_io()
```

### <a name="use-multiple-language-worker-processes"></a>여러 언어 작업자 프로세스 사용

기본적으로 모든 함수 호스트 인스턴스에는 단일 언어 작업자 프로세스가 있습니다. 그러나 호스트 인스턴스당 여러 언어 작업자 프로세스를 지원할 수 있습니다. 그런 다음 함수 호출은 이러한 언어 작업자 프로세스 간에 균등 하 게 분산 될 수 있습니다. [FUNCTIONS_WORKER_PROCESS_COUNT](functions-app-settings.md#functions_worker_process_count) 응용 프로그램 설정을 사용 하 여이 값을 변경할 수 있습니다. 

## <a name="context"></a>컨텍스트

실행 중에 함수의 호출 컨텍스트를 가져오려면 시그니처에 [`context`](/python/api/azure-functions/azure.functions.context?view=azure-python) 인수를 포함 합니다. 

예를 들어 다음과 같은 가치를 제공해야 합니다.

```python
import azure.functions


def main(req: azure.functions.HttpRequest,
         context: azure.functions.Context) -> str:
    return f'{context.invocation_id}'
```

[**Context**](/python/api/azure-functions/azure.functions.context?view=azure-python) 클래스에는 다음과 같은 문자열 특성이 있습니다.

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

## <a name="environment-variables"></a>환경 변수

함수에서 서비스 연결 문자열과 같은 [응용 프로그램 설정은](functions-app-settings.md)실행 중에 환경 변수로 노출 됩니다. @No__t-0을 선언 하 고 `setting = os.environ["setting-name"]`을 사용 하 여 이러한 설정에 액세스할 수 있습니다.

다음 예제에서는 `myAppSetting` 이라는 키를 사용 하 여 [응용 프로그램 설정을](functions-how-to-use-azure-function-app-settings.md#settings)가져옵니다.

```python
import logging
import os
import azure.functions as func

def main(req: func.HttpRequest) -> func.HttpResponse:

    # Get the setting named 'myAppSetting'
    my_app_setting_value = os.environ["myAppSetting"]
    logging.info(f'My app setting value:{my_app_setting_value}')
```

로컬 개발의 경우 응용 프로그램 설정은 [로컬. 설정 json 파일에서 유지 관리](functions-run-local.md#local-settings-file)됩니다.  

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

게시할 준비가 되 면 모든 종속성이 프로젝트 디렉터리의 루트에 있는 *요구 사항 .txt* 파일에 나열 되어 있는지 확인 합니다. 이러한 종속성을 [원격으로 빌드할](functions-deployment-technologies.md#remote-build) 수 Azure Functions.

가상 환경 폴더를 비롯 하 여 게시에서 제외 되는 프로젝트 파일 및 폴더는 funcignore 파일에 나열 됩니다.  

Azure에 배포 하 고 원격 빌드를 수행 하려면 다음 명령을 사용 합니다.

```bash
func azure functionapp publish <app name> --build remote
```

원격 빌드를 사용 하지 않고, 컴파일러를 필요로 하는 패키지를 사용 하 고 PyPI에서 많은 Linux 호환 휠 설치를 지원 하지 않는 경우 로컬로 빌드하지 않고 Azure에 게시 하면 다음과 같은 오류가 발생 합니다.

```
There was an error restoring dependencies.ERROR: cannot install <package name - version> dependency: binary dependencies without wheels are not supported.  
The terminal process terminated with exit code: 1
```

로컬에서 빌드하고 필요한 이진 파일을 구성 하려면 로컬 컴퓨터에 [Docker를 설치](https://docs.docker.com/install/) 하 고 다음 명령을 실행 하 여 func ( [Azure Functions Core Tools](functions-run-local.md#v2) )를 사용 하 여 게시 합니다. `<app name>`을 Azure의 함수 앱 이름으로 바꾸어야 합니다. 

```bash
func azure functionapp publish <app name> --build-native-deps
```

이면에서 Core Tools는 docker를 사용하여 [mcr.microsoft.com/azure-functions/python](https://hub.docker.com/r/microsoft/azure-functions/) 이미지를 로컬 머신의 컨테이너로 실행합니다. 그런 다음이 환경을 사용 하 여 Azure에 대 한 최종 배포를 위해 패키지 하기 전에 원본 배포에서 필요한 모듈을 빌드하고 설치 합니다.

종속성을 빌드하고 CD (지속적인 업데이트) 시스템을 사용 하 여 게시 하려면 [Azure Pipelines을 사용](functions-how-to-azure-devops.md)합니다. 

## <a name="unit-testing"></a>유닛 테스트

Python으로 작성 된 함수는 표준 테스트 프레임 워크를 사용 하 여 다른 Python 코드와 같이 테스트할 수 있습니다. 대부분의 바인딩에서 `azure.functions` 패키지에서 적절 한 클래스의 인스턴스를 만들어 모의 입력 개체를 만들 수 있습니다. [@No__t-1](https://pypi.org/project/azure-functions/) 패키지는 즉시 사용할 수 없으므로 위의 [Python 버전 및 패키지 관리](#python-version-and-package-management) 섹션에 설명 된 대로 `requirements.txt` 파일을 통해 설치 해야 합니다.

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

### <a name="cross-origin-resource-sharing"></a>교차 원본 자원 공유

Azure Functions는 CORS (원본 간 리소스 공유)를 지원 합니다. CORS는 [포털](functions-how-to-use-azure-function-app-settings.md#cors) 및 [Azure CLI](/cli/azure/functionapp/cors)를 통해 구성 됩니다. CORS 허용 원본 목록은 함수 앱 수준에서 적용 됩니다. CORS를 사용 하면 응답에 `Access-Control-Allow-Origin` 헤더가 포함 됩니다. 자세한 내용은 [크로스-원본 자원 공유(CORS)](functions-how-to-use-azure-function-app-settings.md#cors)를 참조하십시오.

현재 Python 함수 앱에 대해 허용 되는 원본 목록이 [지원 되지 않습니다](https://github.com/Azure/azure-functions-python-worker/issues/444) . 이러한 제한 때문에 다음 예제와 같이 HTTP 함수에서 `Access-Control-Allow-Origin` 헤더를 명시적으로 설정 해야 합니다.

```python
def main(req: func.HttpRequest) -> func.HttpResponse:

    # Define the allow origin headers.
    headers = {"Access-Control-Allow-Origin": "https://contoso.com"}

    # Set the headers in the response.
    return func.HttpResponse(
            f"Allowed origin '{headers}'.",
            headers=headers, status_code=200
    )
``` 

OPTIONS HTTP 메서드를 지원 하도록 함수인 json도 업데이트 해야 합니다.

```json
    ...
      "methods": [
        "get",
        "post",
        "options"
      ]
    ...
```

이 메서드는 Chrome 브라우저에서 허용 된 원본 목록을 협상 하는 데 사용 됩니다. 

## <a name="next-steps"></a>다음 단계

자세한 내용은 다음 리소스를 참조하십시오.

* [Azure Functions 패키지 API 설명서](/python/api/azure-functions/azure.functions?view=azure-python)
* [Azure Functions에 대한 모범 사례](functions-best-practices.md)
* [Azure Functions 트리거 및 바인딩](functions-triggers-bindings.md)
* [Blob Storage 바인딩](functions-bindings-storage-blob.md)
* [HTTP 및 Webhook 바인딩](functions-bindings-http-webhook.md)
* [Queue Storage 바인딩](functions-bindings-storage-queue.md)
* [타이머 트리거](functions-bindings-timer.md)


[HttpRequest]: /python/api/azure-functions/azure.functions.httprequest?view=azure-python
[Httpresponse.cache]: /python/api/azure-functions/azure.functions.httpresponse?view=azure-python
