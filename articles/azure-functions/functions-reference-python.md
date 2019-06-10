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
ms.openlocfilehash: 039b0951484a6bf57703d9a91d604c9c5e5c9a66
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2019
ms.locfileid: "64571164"
---
# <a name="azure-functions-python-developer-guide"></a>Azure Functions Python 개발자 가이드

이 문서에서는 Python을 사용하여 Azure Functions를 개발하는 방법을 소개합니다. 아래 내용은 [Azure Functions 개발자 가이드](functions-reference.md)를 이미 읽었다고 가정합니다.

[!INCLUDE [functions-python-preview-note](../../includes/functions-python-preview-note.md)]

## <a name="programming-model"></a>프로그래밍 모델

Azure 함수는 입력을 처리하고 출력을 생성하는 Python 스크립트의 상태 비저장 메서드여야 합니다. 기본적으로 런타임에 메서드를 호출 하는 전역 방법으로 구현 해야 `main()` 에 `__init__.py` 파일입니다.

`function.json` 파일에 `scriptFile` 및 `entryPoint` 속성을 지정하여 기본 구성을 변경할 수 있습니다. 예를 들어 아래의 _function.json_은 _main.py_ 파일의 _customentry()_ 메서드를 Azure 함수의 진입점으로 사용하도록 런타임에 지시합니다.

```json
{
  "scriptFile": "main.py",
  "entryPoint": "customentry",
  ...
}
```

트리거 및 바인딩의 데이터는 `function.json` 구성 파일에 정의된 `name` 속성을 사용하여 메서드 특성을 통해 함수에 바인딩됩니다. 예를 들어 합니다 _function.json_ 라는 HTTP 요청에 의해 트리거되는 간단한 함수에 설명 합니다 `req`:

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

선택적으로, Python 형식 주석을 사용하여 함수에서 매개 변수 형식 및 반환 형식을 선언할 수도 있습니다. 예를 들어 다음과 같이 주석을 사용하여 동일한 함수를 작성할 수 있습니다.

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
 | - extensions.csproj
 | - bin
```

함수 앱을 구성하는 데 사용할 수 있는 공유 [host.json](functions-host-json.md) 파일이 있습니다. 각 함수에는 자체 코드 파일과 바인딩 구성 파일(function.json)이 있습니다. 

공유 코드는 별도의 폴더에 보관해야 합니다. SharedCode 폴더의 모듈을 참조하기 위해 다음 구문을 사용할 수 있습니다.

```
from ..SharedCode import myFirstHelperFunction
```

Functions 런타임에서 사용되는 바인딩 확장은 `extensions.csproj` 파일에 정의되어 있고, 실제 라이브러리 파일은 `bin` 폴더에 있습니다. 로컬에서 개발할 때는 Azure Functions Core Tools를 사용하여 [바인딩 확장을 등록](./functions-bindings-register.md#local-development-with-azure-functions-core-tools-and-extension-bundles)해야 합니다. 

Azure의 함수 앱에 Functions 프로젝트를 배포할 때 FunctionApp 폴더의 전체 내용을 패키지에 포함해야 하지만 폴더 자체는 포함하지 않습니다.

## <a name="triggers-and-inputs"></a>트리거 및 입력

입력은 Azure Functions에서 트리거 입력과 추가 입력의 두 가지 범주로 나뉩니다. `function.json`에서는 서로 다르지만, Python 코드에서는 사용법이 동일합니다.  트리거 및 입력 원본에 대 한 연결 문자열의 값에 매핑하는 `local.settings.json` 파일을 로컬로 및 Azure에서 실행 하는 경우 응용 프로그램 설정 합니다. 예를 들어 다음 코드 조각을 살펴보겠습니다.

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

함수가 호출되면 HTTP 요청이 `req`로 함수에 전달됩니다. 기준으로 Azure Blob 저장소에서 항목을 검색할를 _ID_ 경로 url로 사용할 수 있게 `obj` 함수 본문의 합니다.  여기서 저장소 계정 연결 문자열에 위치한 지정 `AzureWebJobsStorage` 는 함수 앱에서 사용 하는 동일한 저장소 계정입니다.


## <a name="outputs"></a>outputs

출력은 반환 값 및 출력 매개 변수 둘 다로 표현될 수 있습니다. 출력이 하나만 있는 경우 반환 값을 사용하는 것이 좋습니다. 다중 출력의 경우 출력 매개 변수를 사용해야 합니다.

함수의 반환 값을 출력 바인딩의 값으로 사용하려면 바인딩의 `name` 속성을 `function.json`의 `$return`으로 설정해야 합니다.

다중 출력을 생성하려면 `azure.functions.Out` 인터페이스에서 제공하는 `set()` 메서드를 사용하여 바인딩에 값을 할당합니다. 예를 들어 다음 함수는 메시지를 큐로 푸시하고 HTTP 응답도 반환할 수 있습니다.

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

| 방법                 | 설명                                |
| ---------------------- | ------------------------------------------ |
| logging.**critical(_message_)**   | 루트 로거에 위험 수준의 메시지를 기록합니다.  |
| logging.**error(_message_)**   | 루트 로거에 오류 수준의 메시지를 기록합니다.    |
| logging.**warning(_message_)**    | 루트 로거에 경고 수준의 메시지를 기록합니다.  |
| logging.**info(_message_)**    | 루트 로거에 정보 수준의 메시지를 기록합니다.  |
| logging.**debug(_message_)** | 루트 로거에 디버그 수준의 메시지를 기록합니다.  |

## <a name="importing-shared-code-into-a-function-module"></a>공유 코드를 함수 모듈로 가져오기

함수 모듈과 함께 게시된 Python 모듈은 상대적 가져오기 구문을 사용해서 가져와야 합니다.

```python
from . import helpers  # Use more dots to navigate up the folder structure.
def main(req: func.HttpRequest):
    helpers.process_http_request(req)
```

또는 공유 코드를 독립 실행형 패키지에 넣어 공용 또는 프라이빗 PyPI 인스턴스에 게시한 다음, 일반 종속성으로 지정합니다.

## <a name="async"></a>Async

함수 앱당 하나의 Python 프로세스만 존재할 수 있으므로 `async def` 문을 사용하여 Azure 함수를 비동기 코루틴으로 구현하는 것이 좋습니다.

```python
# Will be run with asyncio directly
async def main():
    await some_nonblocking_socket_io_op()
```

main() 함수가 동기인 경우(`async` 한정자 없음) `asyncio` 스레드 풀에서 자동으로 실행됩니다.

```python
# Would be run in an asyncio thread-pool
def main():
    some_blocking_socket_io()
```

## <a name="context"></a>Context

실행 중에 함수의 호출 컨텍스트를 가져오려면 해당 서명에 `context` 인수를 포함합니다. 

예를 들면 다음과 같습니다.

```python
import azure.functions

def main(req: azure.functions.HttpRequest,
            context: azure.functions.Context) -> str:
    return f'{context.invocation_id}'
```

**Context** 클래스에는 다음과 같은 메서드가 있습니다.

`function_directory`  
함수가 실행되는 디렉터리입니다.

`function_name`  
함수의 이름입니다.

`invocation_id`  
현재 함수 호출의 ID입니다.

## <a name="python-version-and-package-management"></a>Python 버전 및 패키지 관리

현재 Azure Functions는 Python3.6.x(공식 CPython 배포)만 지원합니다.

Azure Functions Core Tools 또는 Visual Studio Code를 사용하여 로컬에서 개발하는 경우 필요한 패키지의 이름과 버전을 `requirements.txt` 파일에 추가하고 `pip`를 사용하여 설치합니다.

예를 들어 다음 요구 사항 파일 및 pip 명령을 사용하여 PyPI에서 `requests` 패키지를 설치할 수 있습니다.

```bash
pip install requests
```

```txt
requests==2.19.1
```

```bash
pip install -r requirements.txt
```

게시할 준비가 되면, 모든 종속성이 프로젝트 디렉터리의 루트에 있는 `requirements.txt` 파일에 나열되는지 확인합니다. Azure Functions을 성공적으로 실행하려면 요구 사항 파일에 다음 패키지가 있어야 합니다.

```txt
azure-functions
azure-functions-worker
grpcio==1.14.1
grpcio-tools==1.14.1
protobuf==3.6.1
six==1.11.0
```

## <a name="publishing-to-azure"></a>Azure에 게시

컴파일러가 필요하고 PyPI에서 manylinux 호환 휠의 설치를 지원하지 않는 패키지를 사용하는 경우 Azure에 게시하면 다음 오류가 발생하고 실패합니다. 

```
There was an error restoring dependencies.ERROR: cannot install <package name - version> dependency: binary dependencies without wheels are not supported.  
The terminal process terminated with exit code: 1
```

필요한 이진을 자동으로 빌드하고 구성하려면 로컬 머신에 [Docker를 설치](https://docs.docker.com/install/)하고 다음 명령을 실행하여 [Azure Functions Core Tools](functions-run-local.md#v2)(func)를 통해 게시합니다. `<app name>`을 Azure의 함수 앱 이름으로 바꾸어야 합니다. 

```bash
func azure functionapp publish <app name> --build-native-deps
```

이면에서 Core Tools는 docker를 사용하여 [mcr.microsoft.com/azure-functions/python](https://hub.docker.com/r/microsoft/azure-functions/) 이미지를 로컬 머신의 컨테이너로 실행합니다. 이 환경을 사용하여, Azure에 최종 배포하기 위해 패키징하기 전에 원본 배포에서 필요한 모듈을 빌드하고 설치합니다.

> [!NOTE]
> Core Tools(func)는 PyInstaller 프로그램을 사용하여 사용자의 코드와 종속성을 Azure에서 실행할 단일 독립 실행형 실행 파일로 동결합니다. 이 기능은 현재 미리 보기로 제공되고 있으며, 모든 유형의 Python 패키지로 확장되지 않을 수 있습니다. 모듈을 가져올 수 없는 경우 `--no-bundler` 옵션을 사용하여 다시 게시해 보세요. 
> ```
> func azure functionapp publish <app_name> --build-native-deps --no-bundler
> ```
> 문제가 계속되면 [문제를 열고](https://github.com/Azure/azure-functions-core-tools/issues/new) 문제에 대한 설명을 포함하여 알려주시기 바랍니다. 


종속성을 빌드하고 CI(연속 통합) 및 CD(지속적인 업데이트) 시스템을 사용하여 게시하려면 [Azure 파이프라인](https://docs.microsoft.com/azure/devops/pipelines/get-started-yaml?view=vsts) 또는 [Travis CI 사용자 지정 스크립트](https://docs.travis-ci.com/user/deployment/script/)를 사용할 수 있습니다. 

다음은 빌드 및 게시 프로세스에 대한 예제 `azure-pipelines.yml` 스크립트입니다.
```yml
pool:
  vmImage: 'Ubuntu 16.04'

steps:
- task: NodeTool@0
  inputs:
    versionSpec: '8.x'

- script: |
    set -e
    echo "deb [arch=amd64] https://packages.microsoft.com/repos/azure-cli/ wheezy main" | sudo tee /etc/apt/sources.list.d/azure-cli.list
    curl -L https://packages.microsoft.com/keys/microsoft.asc | sudo apt-key add -
    sudo apt-get install -y apt-transport-https
    echo "install Azure CLI..."
    sudo apt-get update && sudo apt-get install -y azure-cli
    npm i -g azure-functions-core-tools --unsafe-perm true
    echo "installing dotnet core"
    curl -sSL https://dot.net/v1/dotnet-install.sh | bash /dev/stdin --channel 2.0
- script: |
    set -e
    az login --service-principal --username "$(APP_ID)" --password "$(PASSWORD)" --tenant "$(TENANT_ID)" 
    func settings add FUNCTIONS_WORKER_RUNTIME python
    func extensions install
    func azure functionapp publish $(APP_NAME) --build-native-deps
```

다음은 빌드 및 게시 프로세스에 대한 예제 `.travis.yaml` 스크립트입니다.

```yml
sudo: required

language: node_js

node_js:
  - "8"

services:
  - docker

before_install:
  - echo "deb [arch=amd64] https://packages.microsoft.com/repos/azure-cli/ wheezy main" | sudo tee /etc/apt/sources.list.d/azure-cli.list
  - curl -L https://packages.microsoft.com/keys/microsoft.asc | sudo apt-key add -
  - sudo apt-get install -y apt-transport-https
  - sudo apt-get update && sudo apt-get install -y azure-cli
  - npm i -g azure-functions-core-tools --unsafe-perm true


script:
  - az login --service-principal --username "$APP_ID" --password "$PASSWORD" --tenant "$TENANT_ID"
  - az account get-access-token --query "accessToken" | func azure functionapp publish $APP_NAME --build-native-deps

```

## <a name="known-issues-and-faq"></a>알려진 문제 및 FAQ

모든 알려진 문제 및 기능 요청은 [GitHub 문제](https://github.com/Azure/azure-functions-python-worker/issues) 목록을 사용하여 추적됩니다. 문제가 발생하여 GitHub에서 해당 문제를 찾을 수 없는 경우 새 문제를 열고 해당 문제에 대한 자세한 설명을 제공해 주세요.

## <a name="next-steps"></a>다음 단계

자세한 내용은 다음 리소스를 참조하세요.

* [Azure Functions에 대한 모범 사례](functions-best-practices.md)
* [Azure Functions 트리거 및 바인딩](functions-triggers-bindings.md)
* [Blob Storage 바인딩](functions-bindings-storage-blob.md)
* [HTTP 및 Webhook 바인딩](functions-bindings-http-webhook.md)
* [Queue Storage 바인딩](functions-bindings-storage-queue.md)
* [타이머 트리거](functions-bindings-timer.md)
