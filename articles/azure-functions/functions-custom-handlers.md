---
title: 사용자 지정 처리기 Azure Functions (미리 보기)
description: 모든 언어 또는 런타임 버전과 Azure Functions를 사용 하는 방법에 대해 알아봅니다.
author: craigshoemaker
ms.author: cshoe
ms.date: 3/18/2020
ms.topic: article
ms.openlocfilehash: 5abc216e182d7becd9d6f42e0f566ee96d09c2a5
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "79479255"
---
# <a name="azure-functions-custom-handlers-preview"></a>사용자 지정 처리기 Azure Functions (미리 보기)

모든 함수 앱은 언어별 처리기를 통해 실행 됩니다. Azure Functions는 기본적으로 다양 한 [언어 처리기](./supported-languages.md) 를 지원 하지만 앱 실행 환경에 대 한 추가 제어를 원하는 경우도 있습니다. 사용자 지정 처리기는이 추가 제어를 제공 합니다.

사용자 지정 처리기는 함수 호스트에서 이벤트를 수신 하는 간단한 웹 서버입니다. HTTP 기본 형식을 지 원하는 모든 언어는 사용자 지정 처리기를 구현할 수 있습니다.

사용자 지정 처리기는 다음을 수행 하려는 경우에 가장 적합 합니다.

- 공식적으로 지원 되는 언어 이외의 언어로 함수 앱 구현
- 기본적으로 지원 되지 않는 언어 버전 또는 런타임으로 함수 앱 구현
- 앱 실행 환경에 대 한 세부적인 제어 권한 보유

사용자 지정 처리기를 사용 하면 모든 [트리거 및 입력 및 출력 바인딩이](./functions-triggers-bindings.md) [확장 번들](./functions-bindings-register.md)을 통해 지원 됩니다.

## <a name="overview"></a>개요

다음 다이어그램에서는 함수 호스트와 사용자 지정 처리기로 구현 된 웹 서버 간의 관계를 보여 줍니다.

![Azure Functions 사용자 지정 처리기 개요](./media/functions-custom-handlers/azure-functions-custom-handlers-overview.png)

- 이벤트는 함수 호스트로 전송 된 요청을 트리거합니다. 이 이벤트는 원시 HTTP 페이로드 (바인딩이 없는 HTTP 트리거 함수의 경우) 또는 함수에 대 한 입력 바인딩 데이터를 보유 하는 페이로드를 수행 합니다.
- 그러면 함수 호스트에서 요청 [페이로드](#request-payload)를 실행 하 여 웹 서버에 요청을 프록시 합니다.
- 웹 서버는 개별 함수를 실행 하 고 함수 호스트에 [응답 페이로드](#response-payload) 를 반환 합니다.
- 함수 호스트는 응답을 출력 바인딩 페이로드로 대상에 프록시 합니다.

사용자 지정 처리기로 구현 된 Azure Functions 앱은 몇 가지 규칙에 따라 *호스트 json* 및 *함수 json* 파일을 구성 해야 합니다.

## <a name="application-structure"></a>애플리케이션 구조

사용자 지정 처리기를 구현 하려면 응용 프로그램에 다음 요소가 필요 합니다.

- 응용 프로그램의 루트에 있는 *호스트 json* 파일
- 각 함수에 대 한 *함수 json* 파일 (함수 이름과 일치 하는 폴더 내)
- 웹 서버를 실행 하는 명령, 스크립트 또는 실행 파일

다음 다이어그램에서는 이러한 파일이 "order" 라는 함수의 파일 시스템에서 어떻게 표시 되는지 보여 줍니다.

```bash
| /order
|   function.json
|
| host.json
```

### <a name="configuration"></a>Configuration

응용 프로그램은 *호스트 json* 파일을 통해 구성 됩니다. 이 파일은 HTTP 이벤트를 처리할 수 있는 웹 서버를 가리켜 요청을 보내는 기능을 호스트에 알립니다.

사용자 지정 처리기는 섹션을 `httpWorker` 통해 웹 서버를 실행 하는 방법에 대 한 세부 정보를 사용 하 여 *호스트 json* 파일을 구성 하 여 정의 됩니다.

```json
{
    "version": "2.0",
    "httpWorker": {
        "description": {
            "defaultExecutablePath": "server.exe"
        }
    }
}
```

섹션 `httpWorker` 은에 `defaultExecutablePath`정의 된 대상을 가리킵니다. 실행 대상은 웹 서버가 구현 되는 명령, 실행 파일 또는 파일 일 수 있습니다.

스크립팅된 응용 프로그램의 `defaultExecutablePath` 경우은 스크립트 언어의 런타임을 가리키고 스크립트 `defaultWorkerPath` 파일 위치를 가리킵니다. 다음 예제에서는 node.js의 JavaScript 앱이 사용자 지정 처리기로 구성 되는 방법을 보여 줍니다.

```json
{
    "version": "2.0",
    "httpWorker": {
        "description": {
            "defaultExecutablePath": "node",
            "defaultWorkerPath": "server.js"
        }
    }
}
```

`arguments` 배열을 사용 하 여 인수를 전달할 수도 있습니다.

```json
{
    "version": "2.0",
    "httpWorker": {
        "description": {
            "defaultExecutablePath": "node",
            "defaultWorkerPath": "server.js",
            "arguments": [ "--argument1", "--argument2" ]
        }
    }
}
```

인수는 많은 디버깅을 지 원하는 데 필요 합니다. 자세한 내용은 [디버깅](#debugging) 섹션을 참조 하세요.

> [!NOTE]
> *호스트 json* 파일은 실행 중인 웹 서버와 동일한 디렉터리 구조 수준에 있어야 합니다. 일부 언어 및 도구 체인은 기본적으로 응용 프로그램 루트에이 파일을 저장 하지 않을 수 있습니다.

#### <a name="bindings-support"></a>바인딩 지원

입력 및 출력 바인딩과 함께 표준 트리거는 *호스트. json* 파일에서 [확장 번들](./functions-bindings-register.md) 을 참조 하 여 사용할 수 있습니다.

### <a name="function-metadata"></a>함수 메타 데이터

사용자 지정 처리기와 함께 사용 하는 경우에는 *함수 json* 내용이 다른 모든 컨텍스트에서 함수를 정의 하는 방법과 다르지 않습니다. 유일 하 게 함수는 함수 이름과 일치 하도록 라는 폴더에 있어야 *합니다.*

### <a name="request-payload"></a>요청 페이로드

순수 HTTP 함수에 대 한 요청 페이로드는 원시 HTTP 요청 페이로드입니다. 순수 HTTP 함수는 HTTP 응답을 반환 하는 입력 또는 출력 바인딩이 없는 함수로 정의 됩니다.

입력, 출력 바인딩 또는를 포함 하는 다른 유형의 함수는 HTTP 이외의 이벤트 소스를 통해 트리거됩니다. 사용자 지정 요청 페이로드가 있습니다.

다음 코드는 샘플 요청 페이로드를 나타냅니다. 페이로드에는 `Data` 와 `Metadata`라는 두 개의 멤버가 있는 JSON 구조가 포함 되어 있습니다.

멤버 `Data` 에는 *함수 json* 파일의 바인딩 배열에 정의 된 입력 및 트리거 이름과 일치 하는 키가 포함 되어 있습니다.

멤버 `Metadata` 는 [이벤트 소스에서 생성 된 메타 데이터](./functions-bindings-expressions-patterns.md#trigger-metadata)를 포함 합니다.

다음 함수에 정의 된 바인딩을 지정 *합니다. json* 파일:

```json
{
  "bindings": [
    {
      "name": "myQueueItem",
      "type": "queueTrigger",
      "direction": "in",
      "queueName": "messages-incoming",
      "connection": "AzureWebJobsStorage"
    },
    {
      "name": "$return",
      "type": "queue",
      "direction": "out",
      "queueName": "messages-outgoing",
      "connection": "AzureWebJobsStorage"
    }
  ]
}
```

이 예와 비슷한 요청 페이로드가 반환 됩니다.

```json
{
    "Data": {
        "myQueueItem": "{ message: \"Message sent\" }"
    },
    "Metadata": {
        "DequeueCount": 1,
        "ExpirationTime": "2019-10-16T17:58:31+00:00",
        "Id": "800ae4b3-bdd2-4c08-badd-f08e5a34b865",
        "InsertionTime": "2019-10-09T17:58:31+00:00",
        "NextVisibleTime": "2019-10-09T18:08:32+00:00",
        "PopReceipt": "AgAAAAMAAAAAAAAAAgtnj8x+1QE=",
        "sys": {
            "MethodName": "QueueTrigger",
            "UtcNow": "2019-10-09T17:58:32.2205399Z",
            "RandGuid": "24ad4c06-24ad-4e5b-8294-3da9714877e9"
        }
    }
}
```

### <a name="response-payload"></a>응답 페이로드

규칙에 따라 함수 응답은 키/값 쌍으로 형식이 지정 됩니다. 지원 되는 키는 다음과 같습니다.

| <nobr>페이로드 키</nobr>   | 데이터 형식 | 설명                                                      |
| ------------- | --------- | ------------------------------------------------------------ |
| `Outputs`     | JSON      | `bindings` 배열에서 *함수. json* 파일에 정의 된 응답 값을 보유 합니다.<br /><br />예를 들어 함수가 "blob" 이라는 blob 저장소 출력 바인딩을 사용 하 여 구성 된 경우는 blob `Outputs` 의 값으로 설정 `blob`된 라는 키를 포함 합니다. |
| `Logs`        | array     | 메시지는 함수 호출 로그에 표시 됩니다.<br /><br />Azure에서 실행 하는 경우 메시지가 Application Insights 표시 됩니다. |
| `ReturnValue` | string    | 출력이 `$return` *함수 json* 파일에서로 구성 된 경우 응답을 제공 하는 데 사용 됩니다. |

[샘플 페이로드는 예제](#bindings-implementation)를 참조 하세요.

## <a name="examples"></a>예

사용자 지정 처리기는 HTTP 이벤트를 지 원하는 모든 언어로 구현할 수 있습니다. [Javascript 및 node.js를 완벽 하 게 지 원하는](./functions-reference-node.md)Azure Functions 있지만 다음 예제에서는 명령 목적으로 Node.js에서 JavaScript를 사용 하 여 사용자 지정 처리기를 구현 하는 방법을 보여 줍니다.

> [!TIP]
> 다른 언어로 사용자 지정 처리기를 구현 하는 방법에 대 한 지침을 제공 하는 동안, 지원 되지 않는 node.js 버전에서 함수 앱을 실행 하려는 경우 여기에 표시 된 node.js 기반 예제도 유용할 수 있습니다.

## <a name="http-only-function"></a>HTTP 전용 함수

다음 예제에서는 추가 바인딩이나 출력 없이 HTTP로 트리거되는 함수를 구성 하는 방법을 보여 줍니다. 이 예제에서 구현 된 시나리오에서는 또는 `http` `GET` `POST` 를 허용 하는 라는 함수를 제공 합니다.

다음 코드 조각은 함수에 대 한 요청이 구성 되는 방법을 나타냅니다.

```http
POST http://127.0.0.1:7071/api/hello HTTP/1.1
content-type: application/json

{
  "message": "Hello World!"
}
```

<a id="hello-implementation" name="hello-implementation"></a>

### <a name="implementation"></a>구현

*Http*라는 폴더에서 *함수 JSON* 파일은 http로 트리거되는 함수를 구성 합니다.

```json
{
  "bindings": [
    {
      "type": "httpTrigger",
      "direction": "in",
      "name": "req",
      "methods": ["get", "post"]
    },
    {
      "type": "http",
      "direction": "out",
      "name": "res"
    }
  ]
}
```

함수는 및 `GET` `POST` 요청을 모두 허용 하도록 구성 되 고, 결과 값은 라는 `res`인수를 통해 제공 됩니다.

응용 프로그램의 루트에서 *호스트 json* 파일은 node.js를 실행 하 고 `server.js` 파일을 가리키도록 구성 됩니다.

```json
{
    "version": "2.0",
    "httpWorker": {
        "description": {
            "defaultExecutablePath": "node",
            "defaultWorkerPath": "server.js"
        }
    }
}
```

파일 *서버 .js* 파일은 웹 서버 및 HTTP 함수를 구현 합니다.

```javascript
const express = require("express");
const app = express();

app.use(express.json());

const PORT = process.env.FUNCTIONS_HTTPWORKER_PORT;

const server = app.listen(PORT, "localhost", () => {
  console.log(`Your port is ${PORT}`);
  const { address: host, port } = server.address();
  console.log(`Example app listening at http://${host}:${port}`);
});

app.get("/hello", (req, res) => {
  res.json("Hello World!");
});

app.post("/hello", (req, res) => {
  res.json({ value: req.body });
});
```

이 예제에서 Express는 HTTP 이벤트를 처리 하는 웹 서버를 만드는 데 사용 되며를 통해 요청을 수신 하도록 설정 `FUNCTIONS_HTTPWORKER_PORT`됩니다.

함수는의 `/hello`경로에 정의 되어 있습니다. `GET`요청은 간단한 JSON 개체 `POST` 를 반환 하 여 처리 되며 요청은를 통해 `req.body`요청 본문에 액세스할 수 있습니다.

여기에서 order 함수의 경로는 함수 호스트가 `/hello` 사용자 지정 `/api/hello` 처리기에 요청을 프록시 하 고 있기 때문입니다.

>[!NOTE]
>는 `FUNCTIONS_HTTPWORKER_PORT` 함수를 호출 하는 데 사용 되는 공용 포트가 아닙니다. 이 포트는 함수 호스트에서 사용자 지정 처리기를 호출 하는 데 사용 됩니다.

## <a name="function-with-bindings"></a>바인딩을 사용 하는 함수

이 예제에서 구현 된 시나리오에는 제품 주문을 `order` 나타내는 페이로드가 포함 `POST` 된을 허용 하는 이라는 함수가 있습니다. 함수가 함수에 게시 될 때 Queue Storage 메시지가 만들어지고 HTTP 응답이 반환 됩니다.

```http
POST http://127.0.0.1:7071/api/order HTTP/1.1
content-type: application/json

{
  "id": 1005,
  "quantity": 2,
  "color": "black"
}
```

<a id="bindings-implementation" name="bindings-implementation"></a>

### <a name="implementation"></a>구현

*순서가*지정 된 폴더에서 *함수. JSON* 파일은 HTTP로 트리거되는 함수를 구성 합니다.

```json
{
  "bindings": [
    {
      "type": "httpTrigger",
      "authLevel": "function",
      "direction": "in",
      "name": "req",
      "methods": ["post"]
    },
    {
      "type": "http",
      "direction": "out",
      "name": "res"
    },
    {
      "type": "queue",
      "name": "message",
      "direction": "out",
      "queueName": "orders",
      "connection": "AzureWebJobsStorage"
    }
  ]
}

```

이 함수는 [http 응답](./functions-bindings-http-webhook-output.md) 을 반환 하 고 [큐 저장소](./functions-bindings-storage-queue-output.md) 메시지를 출력 하는 [http 트리거 함수로](./functions-bindings-http-webhook-trigger.md) 정의 됩니다.

응용 프로그램의 루트에서 *호스트 json* 파일은 node.js를 실행 하 고 `server.js` 파일을 가리키도록 구성 됩니다.

```json
{
    "version": "2.0",
    "httpWorker": {
        "description": {
            "defaultExecutablePath": "node",
            "defaultWorkerPath": "server.js"
        }
    }
}
```

파일 *서버 .js* 파일은 웹 서버 및 HTTP 함수를 구현 합니다.

```javascript
const express = require("express");
const app = express();

app.use(express.json());

const PORT = process.env.FUNCTIONS_HTTPWORKER_PORT;

const server = app.listen(PORT, "localhost", () => {
  console.log(`Your port is ${PORT}`);
  const { address: host, port } = server.address();
  console.log(`Example app listening at http://${host}:${port}`);
});

app.post("/order", (req, res) => {
  const message = req.body.Data.req.Body;
  const response = {
    Outputs: {
      message: message,
      res: {
        statusCode: 200,
        body: "Order complete"
      }
    },
    Logs: ["order processed"]
  };
  res.json(response);
});
```

이 예제에서 Express는 HTTP 이벤트를 처리 하는 웹 서버를 만드는 데 사용 되며를 통해 요청을 수신 하도록 설정 `FUNCTIONS_HTTPWORKER_PORT`됩니다.

함수는의 `/order` 경로에 정의 되어 있습니다.  여기에서 order 함수의 경로는 함수 호스트가 `/order` 사용자 지정 `/api/order` 처리기에 요청을 프록시 하 고 있기 때문입니다.

`POST` 요청이이 함수로 전송 되 면 데이터는 몇 가지 요소를 통해 노출 됩니다.

- 요청 본문은를 통해 사용할 수 있습니다.`req.body`
- 함수에 게시 된 데이터는를 통해 사용할 수 있습니다.`req.body.Data.req.Body`

함수의 응답 형식은 키/값 쌍으로 지정 되며,이 값은 `Outputs` 멤버가 *json 파일에* 정의 된 출력과 일치 하는 json 값을 포함 합니다.

이 함수 `message` 는 요청에서 들어오는 메시지와 `res` 예상 된 HTTP 응답으로의 메시지를 설정 하 여 Queue Storage 하 고 http 응답을 반환 하는 메시지를 출력 합니다.

## <a name="debugging"></a>디버깅

함수 사용자 지정 처리기 앱을 디버깅 하려면 디버깅을 사용 하기 위해 언어 및 런타임에 적절 한 인수를 추가 해야 합니다.

예를 들어, node.js 응용 프로그램을 디버깅 하기 위해이 `--inspect` 플래그는 *호스트 json* 파일에 인수로 전달 됩니다.

```json
{
    "version": "2.0",
    "httpWorker": {
        "description": {
            "defaultExecutablePath": "node",
            "defaultWorkerPath": "server.js",
            "arguments": [ "--inspect" ]
        }
    }
}
```

> [!NOTE]
> 디버깅 구성은 *호스트 json* 파일의 일부 이므로 프로덕션 환경에 배포 하기 전에 일부 인수를 제거 해야 할 수도 있습니다.

이 구성을 사용 하 여 다음 명령을 사용 하 여 함수의 호스트 프로세스를 시작할 수 있습니다.

```bash
func host start
```

프로세스가 시작 되 면 디버거를 연결 하 고 중단점을 적중 할 수 있습니다.

### <a name="visual-studio-code"></a>Visual Studio Code

다음 예제는 응용 프로그램을 Visual Studio Code 디버거에 연결 하기 위해 *시작 json* 파일을 설정 하는 방법을 보여 주는 샘플 구성입니다.

이 예제는 node.js 용 이므로 다른 언어나 런타임에 대 한이 예제를 변경 해야 할 수 있습니다.

```json
{
  "version": "0.2.0",
  "configurations": [
    {
      "name": "Attach to Node Functions",
      "type": "node",
      "request": "attach",
      "port": 9229,
      "preLaunchTask": "func: host start"
    }
  ]
}
```

## <a name="deploying"></a>배포 중

사용자 지정 처리기는 거의 모든 Azure Functions 호스팅 옵션에 배포할 수 있습니다 ( [제한](#restrictions)참조). 처리기에 사용자 지정 종속성 (예: 언어 런타임)이 필요한 경우 [사용자 지정 컨테이너](./functions-create-function-linux-custom-image.md)를 사용 해야 할 수 있습니다.

## <a name="restrictions"></a>제한

- 사용자 지정 처리기는 Linux 소비 계획에서 지원 되지 않습니다.
- 웹 서버는 60 초 이내에 시작 해야 합니다.

## <a name="samples"></a>샘플

다양 한 언어로 함수를 구현 하는 방법에 대 한 예제는 [사용자 지정 처리기 샘플 GitHub 리포지토리](https://github.com/Azure-Samples/functions-custom-handlers) 를 참조 하세요.
